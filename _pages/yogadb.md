## YogaDB | LING 508 Project

YogaDB is the modernized version of my final class assignment for LING 508, Computational Techniques for Linguists. This updated version maintains much of the original Flask application, and has an improved UI and an updated database of 100 yoga poses. The project began as a simple API that returned a yoga pose matching the body part the user selected.

## Motivation

I started this project the same summer I joined a 30-day yoga challenge at a local studio in Tucson. What began as a way to recover and exercise regularly during Tucson's brutal summer quickly turned into a genuine interest in yoga practice. I wanted to understand it better and thought that building a structured pose database would be both fun and challenging. 

The first version met the course requirements. It was a simple API that returned a pose based on a target body part selected by the user. I became interested in exploring features that could make it more dynamic with features like recommended sequences, advanced filtering, and experimenting with different database types.
  


## Architecture Overview

YogaDB now follows a crisp three-layer architecture. The presentation layer is a single Jinja2 template that loads Bootstrap 5 from CDN and relies on lightweight JavaScript to call the API and render results. The service layer is a minimal Flask application that exposes two routes: the landing page and a `/pose` endpoint that returns JSON. The persistence layer lives in MySQL, running locally via Docker with seed data mounted at container start-up. I kept the codebase intentionally small, so every module has a clear responsibility: `model/` defines data classes and enums, `db/` encapsulates database access via `MySQLRepository`, and `model/yoga_service.py` is the seam between the API and data store. New helper scripts in `scripts/` handle ingestion (`load_poses.py`) and analytics (`pose_stats.py`), giving me reproducible pipelines without bloating the Flask runtime. Tests sit under `test/`, now discoverable through a simple `pytest` command thanks to a `conftest.py` that adjusts `sys.path` for local modules.

## Backend Enhancements and Metadata Strategy

The original assignment stored poses in a single table with a generic `name` field and unstructured instructions, which made it impossible to distinguish English from Sanskrit titles or capture targeting metadata. I refactored the schema to match the real-world data I wanted to serve: each pose now stores `english_name`, `sanskrit_name`, `pose_type`, `target_body_parts`, and `instructions`. The MySQL repository converts each row into a `YogaPose` dataclass, ensuring the API returns consistently structured JSON. To populate the database I curated a `data/pose_catalog.json` file with 100 unique poses, covering a balanced set of body parts (44 poses mention shoulders, 24 highlight hips, 28 strengthen hamstrings, etc.). A custom loader script reads the JSON, skips duplicates, and inserts new entries so I can refresh the catalog without hand-running SQL. I paired that loader with a stats script that prints body-part coverage; it doubles as a validation tool confirming that the catalog remains balanced over time. Finally, unit tests use stub repositories to avoid touching the live database during CI, while dedicated repository tests monkeypatch the connection layer to keep everything deterministic.

## Frontend Experience

The landing page went from a plain text form to a polished mini-app with Sonoran desert-inspired gradients, glassmorphism cards, and responsive layout built with Bootstrap. Instead of requesting users to type blindly, the interface now offers a curated dropdown of common body parts (hips, shoulders, spine, hamstrings, etc.) alongside an optional free-text field for bespoke requests. As soon as the page loads it fetches “hips” poses to demonstrate the results format, establishing trust and giving a quick win. Each pose is presented in a card that highlights English and Sanskrit names, pose categories, targeted regions, and instructions. I added a lightweight status bar that communicates loading state, errors (such as missing body part), and empty results with friendly language. The structure is future-proofed for richer assets—images or sequence recommendations can slot directly into each card without redesign. Accessibility was a priority: the color palette meets contrast guidelines, controls are keyboard navigable, and the content reads cleanly for assistive technologies.

## Outcomes and Impact

The upgrade delivers tangible improvements across the board. The dataset grew from five sample entries to a curated catalog of one hundred, each with clean metadata that supports filtering and future analytics. The API now responds with predictable JSON that front-end consumers (including the built-in UI) can trust, while the ingestion pipeline ensures the database and JSON catalog stay in sync. Automated tests cover the service layer, repository, and data transformations, giving confidence that refactors or additions will not regress core behavior. From a product standpoint, the refreshed interface turns a classroom assignment into an application I would share with recruiters or wellness partners. Version control practices—feature branches, pull requests, and a comprehensive README—document the engineering process and prepare the project for collaboration. In short, YogaDB is no longer just a homework submission; it is a demonstrable example of shipping a data-rich, user-friendly tool end to end.

## Mapping to HLT Learning Outcomes

This project reinforces the four pillars of the HLT program. *Readable, efficient code*: refactoring the repository and service layers into lean, well-tested modules, supported by dataclasses and consistent serialization. *Algorithms and core concepts*: designing the metadata model and query logic to match real usage (case-insensitive search, multiple target body parts, deduplication). *Tools and libraries*: integrating Flask, MySQL, Docker, Bootstrap, and pytest into a cohesive workflow that mirrors professional development environments. *Professional skills*: maintaining disciplined version control, documenting setup procedures, and crafting a polished UX narrative that communicates value to both technical and non-technical stakeholders. Collectively, these outcomes illustrate how I can take an academic prototype, debug it rigorously, and deliver production-quality improvements that align with stakeholder goals.

## Future Directions

YogaDB now has a solid foundation, and there are several directions I’d like to explore next. One feature I’m especially interested in is recommended sequences—short routines that combine poses in a logical, flowing order. Because each pose already includes metadata for target areas and difficulty, the system could generate progressions automatically or use an LLM to design custom sequences based on user input. This would move the app beyond static lookup and toward something more dynamic and interactive


