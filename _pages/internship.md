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
**Duration:** Summer/Fall 2024  
**Role:** NLP/RAG Systems Intern  
**Repository:** [github.com/wswede1/epistemix-pod](https://github.com/wswede1/epistemix-pod)

---

## Executive Summary

During my internship at Epistemix, I developed a comprehensive Retrieval-Augmented Generation (RAG) system for searching and answering questions about "The Flux by Epistemix" podcast transcripts. The system enables users to perform semantic search over podcast content and receive answers with precise timecode citations, making hours of audio content easily searchable and accessible. This project involved implementing the complete pipeline from audio ingestion through LLM-powered question answering, requiring integration of multiple modern NLP technologies and frameworks.

The project successfully processed 24 podcast episodes, transcribing approximately 20 hours of audio content using OpenAI's Whisper model on high-performance computing infrastructure. I built an intelligent text chunking system that preserved sentence and speaker boundaries, generated embeddings for 970 text segments using the BGE-M3 model, and stored them in a ChromaDB vector database. The final system includes a FastAPI REST API backend and a Streamlit web interface, with optional integration of Ollama for LLM-powered answer generation with automatic citation management.

**Technologies Used:** Python, FastAPI, Streamlit, ChromaDB, LangChain, sentence-transformers, OpenAI Whisper, Ollama  
**Key Metrics:** 24 episodes processed, 970 searchable chunks, sub-100ms search latency, ~1000 lines of production code

---

## What I Did

My primary responsibility was designing and implementing the complete RAG pipeline from initial concept to working production system. I began by developing the data ingestion infrastructure, which included parsing RSS feeds to identify podcast episodes, downloading audio files with proper error handling and metadata extraction, and setting up batch processing scripts for large-scale transcription jobs. I leveraged the university's high-performance computing resources to transcribe 24 episodes using GPU-accelerated Whisper, significantly reducing processing time from days to hours.

The core technical challenge involved building an intelligent chunking system that balanced multiple competing requirements: chunks needed to be small enough for effective semantic search but large enough to maintain context, they had to respect sentence boundaries to avoid cutting thoughts mid-stream, and they needed to preserve speaker information and timestamps for accurate citation. I implemented this using NLTK's sentence tokenizer combined with custom logic for boundary detection and overlap management.

For the retrieval component, I integrated ChromaDB with LangChain to create a production-ready vector store. I generated embeddings using the BGE-M3 model, which provides 1024-dimensional vectors optimized for semantic search tasks. I implemented Maximum Marginal Relevance (MMR) reranking to improve result diversity and reduce redundancy in search results, ensuring users receive varied perspectives rather than slightly different phrasings of the same content.

Finally, I developed both backend API and frontend interfaces to make the system accessible. The FastAPI backend provides RESTful endpoints for search, answer generation, and system statistics, with background initialization to minimize startup latency. The Streamlit frontend offers an intuitive user interface with adjustable search parameters, episode filtering, and real-time citation tracking. I integrated Ollama for optional LLM-powered answer generation, implementing prompt engineering techniques to ensure responses stay grounded in retrieved context and include proper citations.

---

## Technical Implementation

The system architecture follows a classic RAG pipeline design: Audio → Transcription → Chunking → Embeddings → Vector Storage → Retrieval → Answer Generation. Each stage was carefully engineered to balance performance, accuracy, and maintainability.

The transcription stage uses OpenAI's Whisper base model running on university HPC resources with SLURM job scheduling. I wrote batch processing scripts that handle job submission, monitor execution, and aggregate results across multiple compute nodes. The output format includes word-level timestamps, speaker detection (when available), and segment-level metadata stored in JSON format.

The chunking pipeline implements a sliding window approach with intelligent boundary detection. Rather than naively splitting at character counts, the system uses NLTK to identify sentence boundaries and consolidates sentences into chunks targeting approximately 1000 characters with 200-character overlap between consecutive chunks. This overlap ensures that concepts spanning chunk boundaries remain searchable. I implemented special handling for very long sentences (like those containing lists or complex clauses) to prevent individual chunks from becoming too large.

For the embedding and storage layer, I integrated LangChain's ChromaDB vector store with the sentence-transformers library. The BGE-M3 model (BAAI/bge-m3) was selected for its strong performance on semantic search benchmarks and its 1024-dimensional output that balances expressiveness with computational efficiency. Each chunk is stored with comprehensive metadata including episode ID, title, start/end timestamps, chunk index, and original text. This metadata enables filtering by episode, time-based retrieval, and accurate citation generation.

The API layer uses FastAPI with async support for concurrent request handling. Key endpoints include `/search` for semantic retrieval, `/answer` for RAG-based question answering, `/episodes` for listing available content, and `/health` for system monitoring. I implemented background model loading to allow the API to start quickly while embedding models initialize asynchronously. The search endpoint supports multiple parameters including result count (k), MMR diversity settings, episode filtering, and similarity thresholds.

The frontend Streamlit application provides two main modes: search mode for exploring relevant chunks and ask mode for getting answers with citations. Users can adjust search parameters in real-time, filter by episode, and toggle between extractive answers (concatenated chunks) and LLM-generated answers. The citation system automatically formats timestamps as clickable MM:SS links that could be integrated with an audio player.

### Code Examples

#### Example 1: Intelligent Text Chunking with Sentence Boundaries

```python
class TranscriptChunker:
    """Chunks podcast transcripts into smaller segments with overlap."""
    
    def __init__(
        self,
        target_chunk_size: int = 1000,
        overlap_size: int = 200,
        min_chunk_size: int = 100,
        max_chunk_size: int = 1500,
    ):
        self.target_chunk_size = target_chunk_size
        self.overlap_size = overlap_size
        self.min_chunk_size = min_chunk_size
        self.max_chunk_size = max_chunk_size
        
        # Validate parameters to ensure logical configuration
        if overlap_size >= target_chunk_size:
            raise ValueError("Overlap size must be less than target chunk size")

    def _split_into_sentences(self, text: str) -> List[str]:
        """Split text into sentences using NLTK's sentence tokenizer."""
        sentences = nltk.sent_tokenize(text)
        return sentences
        
    def chunk_text(self, text: str) -> List[str]:
        """
        Chunk text into segments while respecting sentence boundaries.
        
        This method splits text into sentences, then groups sentences into
        chunks that approximate the target size while ensuring no sentence
        is cut mid-stream. Overlap between chunks maintains context.
        """
        sentences = self._split_into_sentences(text)
        chunks = []
        current_chunk = []
        current_size = 0
        
        for sentence in sentences:
            sentence_len = len(sentence)
            
            # If adding this sentence keeps us under target, add it
            if current_size + sentence_len <= self.target_chunk_size:
                current_chunk.append(sentence)
                current_size += sentence_len
            else:
                # Save current chunk and start new one
                if current_chunk:
                    chunks.append(" ".join(current_chunk))
                
                # Start new chunk with overlap from previous
                overlap_text = self._get_overlap_text(current_chunk)
                current_chunk = [overlap_text, sentence] if overlap_text else [sentence]
                current_size = len(" ".join(current_chunk))
        
        # Add final chunk
        if current_chunk:
            chunks.append(" ".join(current_chunk))
            
        return chunks
```

**Explanation:** This chunking implementation demonstrates a key challenge in RAG systems: balancing chunk size for effective retrieval while preserving linguistic coherence. Rather than naively splitting at character counts, the code uses NLTK's sentence tokenizer to identify natural boundaries, then groups complete sentences into chunks. The overlap mechanism (not fully shown) ensures concepts spanning chunk boundaries remain searchable by including the last few sentences from one chunk in the beginning of the next. This approach significantly improves retrieval quality by ensuring retrieved context is semantically complete and readable. The validation logic prevents configuration errors that would break the overlap mechanism.

#### Example 2: BGE-M3 Embedding Generation with LangChain

```python
from langchain_core.embeddings import Embeddings
from sentence_transformers import SentenceTransformer

class DirectHuggingFaceEmbeddings(Embeddings):
    """Direct SentenceTransformer embeddings wrapper for LangChain."""
    
    def __init__(self, model_name: str, device: str = "cpu", **kwargs):
        """Initialize with SentenceTransformer model."""
        self.model = SentenceTransformer(model_name, device=device)
        self.model_name = model_name
    
    def embed_documents(self, texts: List[str]) -> List[List[float]]:
        """Embed a batch of documents."""
        embeddings = self.model.encode(
            texts, 
            normalize_embeddings=True,
            show_progress_bar=False
        )
        return embeddings.tolist()
    
    def embed_query(self, text: str) -> List[float]:
        """Embed a single search query."""
        embedding = self.model.encode(
            text, 
            normalize_embeddings=True,
            show_progress_bar=False
        )
        return embedding.tolist()

# Usage in vector store initialization
embedder = DirectHuggingFaceEmbeddings(model_name="BAAI/bge-m3")
vector_store = LangChainPodcastVectorStore(
    embeddings=embedder,
    db_path=Path("data/chroma_langchain"),
    collection_name="podcast_chunks_lc"
)
```

**Explanation:** This code integrates the BGE-M3 embedding model with LangChain's ecosystem by implementing the `Embeddings` interface. The wrapper provides two key methods: `embed_documents` for batch processing during indexing and `embed_query` for individual query encoding. The `normalize_embeddings=True` parameter is crucial—it ensures all vectors have unit length, which makes cosine similarity equivalent to dot product and improves retrieval stability. This integration pattern allows seamless swapping of embedding models (just change `model_name`) while maintaining consistent interfaces throughout the application. The separation between document and query embedding methods enables potential future enhancements like asymmetric retrieval models where documents and queries use different encoding strategies.

#### Example 3: FastAPI Endpoint with Background Initialization

```python
app = FastAPI(title="Epistemix Podcast RAG API", version="0.1.0")

# Global state for embeddings and vector store
_embedder: Optional[LangChainEmbeddingGenerator] = None
_vector_store: Optional[LangChainPodcastVectorStore] = None
_init_status: str = "initializing"  # initializing | ready | error

def _initialize_backend() -> None:
    """Load embeddings and vector store in a background thread."""
    global _embedder, _vector_store, _init_status
    try:
        logger.info("Initializing embeddings model (background)...")
        _embedder = LangChainEmbeddingGenerator(model_name="BAAI/bge-m3")
        logger.info("Initializing vector store (background)...")
        _vector_store = LangChainPodcastVectorStore(
            embeddings=_embedder.embeddings,
            db_path=Path("data/chroma_langchain"),
            collection_name="podcast_chunks_lc"
        )
        _init_status = "ready"
        logger.info("Backend initialization complete (ready)")
    except Exception as e:
        _init_status = "error"
        logger.exception(f"Backend initialization failed: {e}")

@app.on_event("startup")
def startup_event():
    """Start background initialization to avoid blocking startup."""
    logger.info("API starting - backend will initialize in background")
    t = threading.Thread(target=_initialize_backend, daemon=True)
    t.start()
    logger.info("Startup complete (non-blocking)")

@app.get("/health")
def health():
    """Health check endpoint that reports initialization status."""
    return {
        "status": _init_status,
        "message": "API is operational" if _init_status == "ready" else "Initializing..."
    }

@app.get("/search")
def search(
    query: str,
    k: int = Query(default=5, ge=1, le=50),
    use_mmr: bool = False,
    lambda_mult: float = Query(default=0.5, ge=0, le=1),
    episode_id: Optional[str] = None
):
    """Semantic search endpoint with optional MMR reranking."""
    vector_store = get_vector_store()  # Checks if ready
    
    # Build metadata filter if episode specified
    where_filter = {"episode_id": episode_id} if episode_id else None
    
    if use_mmr:
        results = vector_store.search_mmr(
            query=query,
            k=k,
            lambda_mult=lambda_mult,
            where_filter=where_filter
        )
    else:
        results = vector_store.search(
            query=query,
            k=k,
            where_filter=where_filter
        )
    
    return {"query": query, "results": results, "count": len(results)}
```

**Explanation:** This FastAPI implementation demonstrates production-ready patterns for ML-powered APIs. The key insight is that loading the BGE-M3 model takes 30-60 seconds, which is unacceptable for synchronous startup. Instead, the code uses background initialization via a daemon thread that loads models asynchronously after the API server binds its socket. This means the API can immediately respond to `/health` checks (critical for container orchestration systems like Kubernetes) while reporting "initializing" status until models are loaded. The `get_vector_store()` helper (not shown) raises HTTP 503 if called before initialization completes, providing graceful degradation. The search endpoint demonstrates FastAPI's query parameter validation with `Query()` annotations that enforce types, ranges, and default values—this auto-generates OpenAPI documentation and provides input validation without manual checking. The MMR toggle allows clients to choose between pure similarity (fast) and diversity-aware ranking (slower but better result variety).

---

## Challenges and Solutions

### Challenge 1: Corrupted Virtual Environment

**The Problem:** Midway through development, the FastAPI server began hanging indefinitely on import statements without producing any error messages or output. The process would start but never reach the point of actually running the application. This made debugging extremely difficult as there were no logs, exceptions, or stack traces to analyze.

**My Solution:** After extensive investigation, I discovered that the virtual environment had become corrupted due to conflicting dependency updates, particularly around pydantic and starlette versions that FastAPI depends on. I completely removed the `.venv` directory and all `__pycache__` directories, then rebuilt the environment from scratch using `uv sync`. I also documented this issue and solution in the project's troubleshooting guide to help future developers avoid the same problem.

**Outcome:** The clean rebuild resolved the import hanging issue completely. This experience taught me the importance of dependency management and the value of reproducible environments. I now maintain more careful documentation of dependency versions and understand when environment corruption might be the root cause of mysterious failures.

### Challenge 2: Prompt Engineering for Accurate Answers

**The Problem:** Initial attempts at LLM-powered answer generation produced overly cautious responses that frequently claimed insufficient information even when relevant context was present. For example, when asked "What is agent-based modeling?" with chunks discussing the topic, the system would respond "not explicitly defined" rather than synthesizing the available information.

**My Solution:** I iteratively refined the system prompt and user message format, testing with various queries and evaluating response quality. The key insight was that the initial prompt was too restrictive, emphasizing caution over synthesis. I revised it to explicitly encourage extracting and synthesizing information from context, only claiming insufficient information when context is truly unrelated to the query. I also increased the temperature from 0.2 to 0.3 to allow more natural language generation and raised default retrieval parameters (k=5, fetch_k=25) to provide richer context.

**Outcome:** The improved prompts dramatically increased answer quality and comprehensiveness while maintaining factual grounding. The system now provides detailed, well-cited answers that appropriately synthesize information from multiple chunks. This experience deepened my understanding of prompt engineering as an iterative, empirical process requiring careful evaluation of outputs.

### Challenge 3: Background Model Loading

**The Problem:** Loading the BGE-M3 embedding model takes 30-60 seconds, which is problematic for API startup. If model loading happens synchronously, the API cannot respond to any requests until initialization completes, creating a poor user experience and failing health checks in containerized deployments.

**My Solution:** I implemented asynchronous model initialization using FastAPI's startup events. The API immediately begins accepting requests with a status indicator showing "initializing" until the model loads. The `/health` endpoint reports the initialization state, allowing monitoring systems to wait for readiness. Once loading completes, the status updates to "ready" and search endpoints become available.

**Outcome:** This approach dramatically improved perceived startup time and deployment reliability. Users can access the UI and see system information immediately, while monitoring systems can properly track readiness status. This taught me important lessons about graceful degradation and the user experience implications of ML model initialization in production systems.

---

## Learning and Development

### Knowledge Applied from Coursework

This project allowed me to apply concepts and techniques from several HLT courses. From LING 508 (HLT Tools and Frameworks), I applied understanding of API design principles, web framework architecture, and the importance of proper documentation and error handling in production systems. The course's emphasis on tool integration was directly relevant to combining FastAPI, Streamlit, ChromaDB, and LangChain into a cohesive application.

Concepts from computational linguistics courses informed the chunking strategy, particularly the importance of preserving linguistic boundaries rather than arbitrarily splitting text. Understanding of information retrieval principles helped me implement MMR reranking effectively and tune similarity thresholds for optimal precision-recall trade-offs.

### New Skills Acquired

This internship required learning several technologies and concepts not covered in depth during coursework. I learned to work with high-performance computing infrastructure, including writing SLURM batch scripts, managing GPU resources, and debugging distributed computing jobs. This was essential for efficiently transcribing 24 hours of audio content.

I gained hands-on experience with vector databases and embedding models, learning how to evaluate different embedding models for specific use cases, optimize vector similarity search performance, and design metadata schemas that enable effective filtering and retrieval. The BGE-M3 model selection involved comparing several options based on dimensionality, speed, and semantic search performance.

Building a production RAG system taught me about LLM integration patterns, particularly prompt engineering techniques for maintaining factual grounding, citation management strategies, and the trade-offs between extractive and generative approaches. I learned to implement streaming responses, handle rate limiting with external LLM providers, and design prompts that balance comprehensiveness with accuracy.

---

## Results and Impact

The project successfully delivered a fully functional RAG system that makes podcast content searchable and accessible. The system currently indexes 23 episodes totaling 970 searchable chunks, with search queries returning relevant results in under 100 milliseconds. The semantic search reliably finds topically relevant content even when query terms don't exactly match transcript text, demonstrating effective embedding-based retrieval.

**Quantitative Metrics:**
- 24 episodes transcribed (one pending processing)
- 970 text chunks indexed with embeddings
- Sub-100ms average search latency
- ~1000 lines of production Python code
- 5 API endpoints implemented
- Full test coverage for core chunking logic

**Qualitative Outcomes:**
- Complete working system with backend API and frontend UI
- Comprehensive documentation including architecture diagrams, API specifications, and user guides
- Clean, maintainable codebase with proper error handling and logging
- Flexible architecture supporting future extensions like additional LLM providers or advanced re-ranking algorithms

The system is currently deployed and functional, with the codebase publicly available on GitHub. Future enhancements could include processing additional episodes as they're released, implementing more sophisticated re-ranking algorithms, adding support for multi-turn conversations with context tracking, and integrating an audio player for seamless playback from timestamped citations.

---

## Demonstration of HLT Learning Outcomes

This project comprehensively demonstrates all four learning outcomes of the MS in Human Language Technology program:

### 1. Code Development and Documentation

I wrote approximately 1000 lines of production Python code across multiple modules including chunking logic (`index/chunker.py`), embedding generation (`index/langchain_embedder.py`), vector store management (`index/langchain_vector_store.py`), API endpoints (`api/main.py`), and UI components (`ui/app.py`). The code follows Python best practices including type hints, docstrings, and modular design with clear separation of concerns.

When faced with the corrupted virtual environment issue causing mysterious import hangs, I systematically debugged the problem through process of elimination, eventually identifying dependency conflicts and implementing a complete environment rebuild. This demonstrated practical debugging skills for production issues that don't produce clear error messages.

Documentation was a priority throughout the project. I created a comprehensive README with quickstart instructions, architecture diagrams, API documentation, and troubleshooting guides. Each module includes docstrings explaining purpose, parameters, and return values. The repository structure is organized logically with clear naming conventions that make code navigation intuitive.

### 2. Algorithm Selection and Application of HLT Concepts

The project required selecting appropriate algorithms for multiple NLP tasks. For embeddings, I evaluated several options (BGE-small, BGE-M3, sentence-transformers/all-MiniLM) based on dimensionality, semantic search performance, and computational cost, ultimately selecting BGE-M3 for its strong balance of accuracy and efficiency at 1024 dimensions.

I implemented Maximum Marginal Relevance (MMR) reranking to improve result diversity. MMR balances relevance (similarity to query) with novelty (dissimilarity to already-selected results), preventing the system from returning multiple near-duplicate chunks. The implementation allows tuning the relevance-diversity trade-off via a lambda parameter.

The RAG architecture itself represents application of core NLP concepts: semantic search via dense vector representations, context-aware text generation with retrieval augmentation, and citation management to ensure factual grounding. I designed the chunking strategy based on psycholinguistic principles about context windows and cognitive processing boundaries, using 1000-character chunks with 200-character overlap to balance searchability with coherence.

### 3. Tool and Library Integration

This project integrates multiple specialized tools into a cohesive system:

**LangChain** provides abstractions for vector stores and embeddings, allowing flexible switching between different providers while maintaining consistent interfaces. I used LangChain's ChromaDB integration for the vector store and sentence-transformers integration for embeddings.

**ChromaDB** serves as the vector database, providing persistent storage and efficient similarity search via approximate nearest neighbor algorithms. I configured collection settings, metadata filtering, and query parameters to optimize retrieval performance.

**FastAPI** powers the REST API backend, providing async request handling, automatic OpenAPI documentation, and type validation. I implemented proper error handling, CORS configuration, and health check endpoints following API best practices.

**Streamlit** enables rapid frontend development with Python, allowing creation of an interactive UI without extensive JavaScript work. I used Streamlit's session state management, form handling, and layout components to create an intuitive search interface.

**Ollama** provides local LLM inference, which I integrated for answer generation. The integration handles connection management, streaming responses, and graceful fallback when Ollama is unavailable.

### 4. Professional Skills

The internship environment required extensive communication and documentation skills. I maintained detailed development logs documenting design decisions, challenges encountered, and solutions implemented. These logs serve both as project memory and as learning artifacts demonstrating problem-solving processes.

Critical thinking was essential throughout the project. When evaluating chunking strategies, I had to balance competing requirements: smaller chunks improve search precision but lose context, while larger chunks maintain coherence but reduce granularity. I systematically evaluated options through experimentation and metrics analysis, ultimately selecting parameters that balanced these trade-offs effectively.

The project required understanding of production deployment considerations including startup time optimization (background model loading), error handling and graceful degradation, logging and monitoring, and API design for extensibility. These professional engineering practices ensure the system is maintainable and reliable beyond the development phase.

Working independently as an intern required self-direction in project planning, time management across multiple development phases, and initiative in identifying and solving problems without constant supervision. I successfully delivered a complete, documented, functional system that meets its stated objectives and provides value to the organization.

---

*Project completed: Fall 2024*  
*Total word count: ~1,950 words (excluding code snippets)*

