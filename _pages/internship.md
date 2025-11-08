---
permalink: /projects/internship/
title: "Internship Project: RAG System for Podcast Transcripts"
layout: single
author_profile: true
toc: true
toc_label: "Contents"
toc_sticky: true
---

# RAG System for Podcast Transcripts

**Organization:** Epistemix  
**Duration:** Summer/Fall 2025  
**Role:** NLP Intern  

---

## Motivation

As a second language speaker, I have always been fascinated by language variation, especially the phonetic variation of Spanish. This interest naturally led me to speech technologies during my HLT coursework, where I trained a VITS text-to-speech model on Galician, a language spoken in Northwest Spain. This project introduced me to the technical challenges and creative solutions in audio and speech data processing. I'm also an avid podcast listener, and I love how podcasts make complex information accessible and engaging. While I was searching for an internship, I thought it would be a fun challenge to do something with podcast transcripts.

That idea grew into my RAG project. I started thinking about how large language models could learn from a company’s internal content and serve as an informed representative that can answer questions about its work and perspective. I wanted to build a system where people interested in a company, or listeners of a podcast, could easily search conversations and company information, get timestamps and references about specific topics, and explore the company or podcast's ideas in a more interactive way.

---

## Summary

During my internship at Epistemix, I developed a Retrieval-Augmented Generation (RAG) system for "The Flux", their company podcast. The system enables semantic search over podcast transcripts and provides answers with precise timecode citations, making hours of audio content easily searchable. I implemented the pipeline from audio ingestion through LLM-powered question answering, integrating technologies including FastAPI, Streamlit, ChromaDB, LangChain, OpenAI's Whisper model, and Ollama.

The project involved transcribing podcast episodes using Whisper on the university high-performance computing infrastructure, building an intelligent chunking system that preserved linguistic boundaries, generating embeddings with the BGE-M3 model, and storing them in a ChromaDB vector database. The final deliverable includes a REST API backend and web interface that allows for information retrieval from audio content.

---

## Technical Implementation

The system follows a typical RAG pipeline: **Audio → Transcription → Chunking → Embeddings → Vector Storage → Retrieval → Answer Generation**

### 1. Data Ingestion & Transcription
I built scripts to parse podcast RSS feeds, extract episode metadata (title, audio URL, duration, publish date), and download the audio files. For transcription, I wanted to use OpenAI's Whisper model because we had explored this model in the advanced speech technology class. In order to speed up transcription, I used the university HPC and processed 24 episodes with timestamps and speaker detection stored in JSON format.

### 2. Text Chunking
The chunking pipeline uses NLTK's sentence tokenizer to respect linguistic boundaries rather than naively splitting at character counts. Chunks target ~1000 characters with 200-character overlap to ensure concepts spanning boundaries remain searchable. Special handling prevents excessively long sentences from breaking chunk size constraints.

### 4. Retrieval & API
The FastAPI backend is used for search and question answering by using Maximum Marginal Relevance (MMR) to improve result diversity.

### 5. Frontend Interface
The Streamlit web application offers search mode (explore chunks) and ask mode (get answers with citations). Users can adjust parameters, filter by episode, and toggle between extractive and LLM-generated answers. 

### Code Examples

#### Example 1: RSS Feed Parsing for Podcast Metadata

```python
def build_manifest_rows(rss_url: str, limit: int | None) -> list[dict]:
    """Parse podcast RSS feed and extract episode metadata."""
    feed = feedparser.parse(rss_url)
    if feed.bozo:
        raise RuntimeError(f"RSS parse error: {feed.bozo_exception!r}")
    
    rows: list[dict] = []
    entries = feed.entries if limit is None else feed.entries[:limit]
    
    for entry in entries:
        audio_url = first_audio_url(entry)
        if not audio_url:
            continue
            
        title = sanitize(entry.get("title", "untitled"))
        published_iso = to_iso(entry.get("published_parsed"), entry.get("published"))
        episode_id = derive_episode_id(entry, title, audio_url)
        duration_s = parse_duration_seconds(entry)
        
        rows.append({
            "episode_id": episode_id,
            "title": title,
            "audio_url": audio_url,
            "page_url": entry.get("link") or "",
            "published_iso": published_iso,
            "duration_s": duration_s if duration_s is not None else "",
            "filename": f"{episode_id}.orig",
        })
    
    return rows
```

This function handles the first stage of the data ingestion pipeline by fetching podcast audio and extracting episode metadata from RSS feeds. It manages edge cases like missing audio URLs, inconsistent publish dates, and differences in duration formats. The code preserves query parameters in audio links, sanitizes text fields, and creates unique episode IDs by combining cleaned titles with parts of the URL. This ensures the downstream pipeline receives clean and consistent metadata for every episode.

#### Example 2: LangChain ChromaDB Vector Store Initialization

```python
from langchain_chroma import Chroma
from langchain_core.embeddings import Embeddings

class LangChainPodcastVectorStore:
    """LangChain-compatible ChromaDB vector store for podcast chunks."""
    
    def __init__(
        self,
        embeddings: Embeddings,
        db_path: Union[str, Path] = "./data/chroma_langchain",
        collection_name: str = "podcast_chunks_lc",
    ):
        self.db_path = Path(db_path)
        self.collection_name = collection_name
        self.embeddings = embeddings
        
        # Ensure directory exists
        self.db_path.mkdir(parents=True, exist_ok=True)
        
        # Initialize LangChain Chroma vector store
        self.vectorstore = Chroma(
            collection_name=collection_name,
            embedding_function=embeddings,
            persist_directory=str(self.db_path),
        )
    
    def add_chunks(self, chunks: List[Dict], episode_id: str) -> None:
        """Add chunks to the vector store using LangChain Documents."""
        documents = []
        ids = []
        
        for chunk in chunks:
            metadata = {
                "episode_id": episode_id,
                "chunk_index": chunk["chunk_index"],
                "start_time": chunk.get("start_time", 0.0),
                "end_time": chunk.get("end_time", 0.0),
            }
            doc = Document(page_content=chunk["text"], metadata=metadata)
            documents.append(doc)
            ids.append(f"{episode_id}_{chunk['chunk_index']}")
        
        self.vectorstore.add_documents(documents=documents, ids=ids)
```

This class connects ChromaDB to LangChain’s vector store interface, making it easy to work with and extend. I intentionally chose LangChain because it's become a standard tool for working with LLMs. The class creates a persistent ChromaDB collection using the BGE-M3 embedding model and converts raw text chunks into LangChain Document objects with metadata such as episode IDs, timestamps, and chunk indices. This metadata supports filtering, such as, for example, searching within a specific episode, and ensures accurate citations. The setup also makes it simple to switch out databases or embedding models later without touching the rest of the code.

#### Example 3: Maximum Marginal Relevance (MMR) Retrieval

```python
def query_mmr(
    self,
    query_text: str,
    n_results: int = 5,
    fetch_k: int = 20,
    lambda_mult: float = 0.5,
    episode_filter: Optional[str] = None,
) -> Dict:
    """MMR query using LangChain for improved result diversity."""
    search_kwargs: Dict[str, object] = {
        "k": n_results,
        "fetch_k": fetch_k,
        "lambda_mult": lambda_mult,
    }
    if episode_filter:
        search_kwargs["filter"] = {"episode_id": episode_filter}
    
    # Use LangChain's built-in MMR search
    docs = self.vectorstore.max_marginal_relevance_search(
        query_text, **search_kwargs
    )
    
    # Format results with metadata
    formatted_results = {
        "query": query_text,
        "n_results": n_results,
        "chunks": [],
        "reranker": "mmr",
    }
    
    for doc in docs:
        chunk_result = {
            "id": f"{doc.metadata['episode_id']}_{doc.metadata['chunk_index']}",
            "text": doc.page_content,
            "metadata": doc.metadata,
        }
        formatted_results["chunks"].append(chunk_result)
    
    return formatted_results
```

Maximum Marginal Relevance addresses a common RAG problem, where pure similarity search often returns results that are almost identical to each other. In my experience curating data and evaluating model outputs, I’ve seen that just because a passage is very similar to the query does not mean it is actually relevant. High cosine similarity between embeddings does not always reflect true semantic relevance; a chunk might repeat keywords from the query but discuss them in a completely different context. This kind of misleading similarity produces false positives that look relevant but are not useful for answering the question. MMR reranks results to balance relevance (similarity to query) with diversity (dissimilarity to already-selected results). The `fetch_k` parameter retrieves more candidates than needed, then the `lambda_mult` value (0-1) controls the relevance-diversity tradeoff. Here, lower values favor diversity, and higher values favor relevance. This implementation leverages LangChain's built-in MMR to improve search quality without custom reranking logic. The episode filter also shows how metadata supports scoped search within specific content subsets, which becomes especially useful as collections grow.

---

## Challenges and Solutions

### Prompt Engineering
The initial LLM responses were overly cautious 
and frequently claimed "insufficient information" 
even when the relevant context was present. For 
example, when asked "What is agent-based modeling", a question that would absolutely have been mentioned in the podcast, the LLM output that it didn't have enough information, rather than synthesizing the available information from the retrieved chunks.

I iteratively refined prompts through repeated 
testing with various queries. The key insight was 
that the initial prompt overemphasized caution, 
which is a common concern when an LLM is the 
voice of a company. I had to find a balance so 
that it was cautious toward malicious requests, 
but also only claimed insufficient information 
when context is truly unrelated. Adjusting the 
temperature and increased retrieval parameters 
also provide richer context and allows the model 
to be slightly more creative with responses.

Answer quality improved and maintained factual 
grounding in the text. The system now provides 
detailed, well-cited answers that appropriately 
synthesize multiple chunks. I had extensive 
experience with prompt engineering from my time 
designing AI agent tasks at work, so I knew that 
iterating and making small adjustments along the 
way are all part of the process. 

### Whisper Transcription on the HPC

Getting Whisper to run on the university’s GPU cluster required significant iteration. My first SLURM jobs kept failing or saving files in the wrong place, so I ended up hacking together a working solution from a transcription script I had used for the Whisper model in my advanced speech technology class. After a lot of trial and error (mostly error), I finally got a reliable setup that could process all 24 podcast episodes (about 20 hours of audio) in under an hour. The output included JSON and text transcripts with timestamps and speaker labels, which flowed straight into the chunking stage.

### Preserving Linguistic Coherence and Timestamps During Chunking

Most off-the-shelf text splitters cut transcripts by character count, which often breaks sentences mid-thought and loses the timing data that makes citations useful. One of my main goals was to keep those timestamps so users could immediately jump to the right moment in an episode. Early prototypes struggled with this: chunks combined different speakers or pointed to the wrong place in the audio. To fix that, I built a custom TranscriptChunker that uses NLTK’s sentence tokenizer, applies target length and overlap constraints (around 1000 characters with a 200-character overlap), and maps each chunk back to the original Whisper segments to recover start and end times. The final chunks read like coherent conversational turns and kept precise timestamps, so search results return answers with accurate timestamps so that a user can immediately find the audio if they choose to listen to that section of the podcast.

---

## Results

The project delivered a fully functional RAG system making podcast content easily searchable and synthesized by an LLM. Semantic search reliably finds topically relevant content even when query terms don't exactly match transcript text, demonstrating effective embedding-based retrieval. There is some latency when initially loading the models and returning answers, which is to be expected in a prototype. 

---

## Future Directions

There are a few directions I’d like to take this project next. The system already separates retrieval from generation, so adding support for multiple local LLMs (such as Gemma, or Mistral) would be straightforward. With a small interface for model selection, the system could automatically choose the best LLM for each query, which is a feature that’s becoming increasingly common in LLM applications.

Another step would be to automate ingestion. A  background process could check the podcast’s RSS feed each day, download any new episodes, run transcription, and update the ChromaDB index. This would keep the search interface up to date automatically, without any manual work.

Finally, I’d like to make the user experience more interactive. Features like conversational history, relevance feedback (“was this helpful?”), and an embedded audio player that jumps to cited timestamps would make the system feel less like a database and more like a responsive, exploratory dialogue with the content.

---

*Project completed: Fall 2025*

