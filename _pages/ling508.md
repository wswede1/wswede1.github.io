---
permalink: /projects/ling508/
title: "YogaDB | LING 508 Project"
layout: single
author_profile: true
toc: true
toc_label: "Contents"
toc_sticky: true
---

# YogaDB

**Course:** LING 508 – Computational Techniques for Linguists  
**Semester:** Summer 2024  

---

## Summary & Motivation

YogaDB is the modernized version of my final class assignment for LING 508, Computational Techniques for Linguists. This updated version maintains much of the original Flask application, and has an improved UI and an updated database of 100 yoga poses. The project began as a simple API that returned a yoga pose matching the body part the user selected.

I improved YogaDB so it was more aesthetically pleasing and I thought it might actually be useful to someone other than me. When the page loads it immediately shows sample results, which helps people understand what the app does right away. The interface lets you search with either a dropdown menu or by typing your own keywords, and the results display clearly with pose names, instructions, and the body parts each pose targets. 

![Screenshot of the YogaDB interface showing pose cards for hips](/assets/images/ling508_ui.png)


**Technologies Used:** Python 3.11, Flask, Flask-CORS, MySQL 8, mysql-connector-python, Bootstrap 5, Docker, Pytest  
**Repository:** [https://github.com/wswede1/yoga_project](https://github.com/wswede1/yoga_project)

---

## Project Overview

### Project Scope

I built a full-stack yoga pose finder that helps people find poses based on the body parts they want to target. The app separates the frontend interface from the backend logic and database, which made it easier to work on different parts independently. I created a dataset of 100 yoga poses with consistent metadata, and wrote scripts to load the data into the database and check that everything looks balanced. The web interface uses Bootstrap, and it shows results right away when you search. I also wrote tests for the core functionality and set up the database to run in Docker, which makes it simple to get the project running locally.

### Project Goals

The goals aligned with the course objectives and served as something I would actually use. I also wanted to demonstrate the technical skills we were learning in class, like working with databases, building web APIs, and structuring code in a maintainable way. The original assignment had just a few sample poses, so expanding this to a comprehensive searchable catalog felt like a significant first improvement. 

### Approach and Methodology

I worked on this iteratively, starting with the backend and then moving to the frontend. I set up the Flask routes first, created data structures for poses, and built the database layer before worrying about how it would look. Once the API was working, I built the web interface. I wanted the page to feel responsive, so it automatically runs a sample search when it loads and displays results in a clean card layout. While working on the UI, I also put together the dataset. I tested things as I went by making requests to the API and checking that everything worked, and wrote tests to catch problems early. This approach let me make changes and improvements without breaking what was already working.

---

## Technical Implementation

### System Architecture

YogaDB follows a three-layer architecture that keeps different concerns separated. The presentation layer is a single `index.html` Jinja2 template rendered by Flask. It loads Bootstrap 5, applies a custom gradient theme, and uses Jinja2 template syntax to render pose cards server-side. The service layer is handled by `YogaService`, which encapsulates business logic like input normalization and mapping database rows to JSON-friendly dictionaries. Flask routes import the service, handle HTTP concerns like validation, logging, and status codes, and delegate the actual data fetching to the service layer. The persistence layer uses `MySQLRepository`, which implements the Repository interface with parameterized SQL queries against a Dockerized MySQL 8 instance. Using dependency injection through the interface allows tests to swap the real repository for a stub, which means I can test service logic without needing MySQL running. This separation makes it easy to iterate on the UI without touching SQL, and to test different parts of the application independently.

<video controls width="720" poster="/assets/images/ling508_ui.png">
  <source src="/assets/clips/ling508_demo.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>

### Dataset

The dataset in `data/pose_catalog.json` was generated entirely using ChatGPT-5. I initially wanted to scrape data from yoga websites, but the most organized and useful sites I found didn't allow web scraping, so I decided to respect those policies and use an LLM-generated dataset instead. The catalog contains 100 unique poses, and each entry records English and Sanskrit names, comma-separated pose types, target body parts, and step-by-step instructions. The data starts as a JSON catalog for ingestion, then gets loaded into a MySQL table called `yoga_poses` where everything is stored as normalized strings. The `pose_stats.py` script summarizes coverage so I can confirm the dataset is balanced. Currently 44 poses assist shoulders, 36 support spine mobility, 28 target hamstrings, and the rest cover various other body parts like hips and neck.

### Technologies and Tools

- **Programming Languages:** Python 3.11 for backend, scripts, and server-side template rendering.
- **Libraries & Frameworks:**
  - `Flask` + `flask-cors` for HTTP routing and cross-origin support.
  - `mysql-connector-python` for database connectivity.
  - `Bootstrap` for responsive UI scaffolding.
  - `Pytest` with stub repositories for unit testing.
- **Infrastructure & Tooling:** Docker Compose sets up a MySQL database that comes pre-loaded with sample pose data. Logging is configured through Python's `logging.config.dictConfig`. 

### Code Examples

#### Example 1: Flask Route Handler with Server-Side Rendering

```python
@app.route('/', methods=['GET'])
def index():
    # Get body_part from query string (form submission)
    raw_body_part = request.args.get('body-part', '')
    custom_body_part = request.args.get('custom-body-part', '').strip()
    
    # Use custom input if provided, otherwise use dropdown selection
    body_part_input = custom_body_part if custom_body_part else raw_body_part
    body_part = body_part_input.strip().lower()
    
    poses = []
    error_message = None
    
    # If a search was performed, query the database
    if body_part:
        app.logger.info(f"Search request for body part: {body_part}")
        try:
            poses = yoga_service.get_poses_by_body_part(body_part)
            if poses:
                app.logger.info(f"Found {len(poses)} poses for body part: {body_part}")
            else:
                error_message = "No poses found for this body part. Try another focus area."
        except Exception as exc:
            app.logger.exception("Unhandled error retrieving poses")
            error_message = "An error occurred while searching for poses. Please try again."
    else:
        # On initial page load, show sample results for "hips"
        try:
            poses = yoga_service.get_poses_by_body_part("hips")
        except Exception as exc:
            app.logger.exception("Error loading initial sample poses")
    
    return render_template("index.html", 
                         poses=poses, 
                         selected_body_part=raw_body_part,
                         custom_body_part=custom_body_part,
                         error_message=error_message)
```

This route handles both initial page loads and form submissions. When the user submits the form, it reads the selected dropdown value or custom text input, queries the database through the service layer, and passes the results to the Jinja2 template for server-side rendering. The template displays the poses using Jinja2 loops. If something goes wrong, it catches the error and passes an error message to the template instead of crashing. On the first page load, it automatically shows sample results for "hips" strictly because that's often what I need the most.

#### Example 2: Converting Strings to Lists

```python
def _split_csv(value: str) -> List[str]:
    if not value:
        return []
    return [segment.strip() for segment in value.split(",") if segment.strip()]

def to_dict(self) -> Dict[str, object]:
    """Return a JSON-serialisable representation."""
    return {
        "english_name": self.english_name,
        "sanskrit_name": self.sanskrit_name,
        "pose_type": _split_csv(self.pose_type),
        "target_body_parts": _split_csv(self.target_body_parts),
        "instructions": self.instructions,
    }
```

The database stores pose types and body parts as comma-separated strings like "Standing, Balance, Strength" or "Hips, Shoulders, Spine". The `_split_csv` helper function converts these strings into Python lists by splitting on commas, removing extra spaces, and filtering out empty values. This makes it easy to iterate over them in the Jinja2 template when rendering pose cards.

#### Example 3: Database Query

```python
def get_poses_by_body_part(self, body_part: str) -> List[YogaPose]:
    query = """
        SELECT english_name, sanskrit_name, pose_type, target_body_parts, instructions
        FROM yoga_poses
        WHERE LOWER(target_body_parts) LIKE %s
    """
    like_pattern = f"%{body_part.lower()}%"
    connection = self._get_connection()
    cursor = connection.cursor(dictionary=True)

    try:
        cursor.execute(query, (like_pattern,))
        result = cursor.fetchall()
        return [YogaPose(**pose) for pose in result]
    finally:
        cursor.close()
        connection.close()
```

This method searches for poses that target a specific body part. The SQL query uses `LIKE` with a pattern to find matches, and it converts everything to lowercase so the search isn't case-sensitive. The `try/finally` block ensures the database connection and cursor are always closed, even if something goes wrong. This prevents connection leaks that would eventually cause the database to stop accepting new connections.

#### Example 4: Tests

```python
def test_get_poses_by_body_part():
    repository = StubRepository()
    service = YogaService(repository)
    pose = build_pose("Core Twist")
    repository.pose_by_target["hips"] = [pose]

    result = service.get_poses_by_body_part("hips")

    assert result == [pose.to_dict()]
```

The tests use a fake repository instead of a real database, so I can test the service logic without needing MySQL running. The test checks that the results come back as dictionaries, which helps catch problems if I change how the data is structured later. Having tests like this means I can make changes to the code without worrying about breaking things.

---

## Challenges and Solutions

### Challenge 1: Converting Comma-Separated Strings to Lists

The database stores pose types and target body parts as comma-separated strings like "Standing, Balance, Strength", but the Jinja2 template needs these as arrays so it can display them as separate badges. I needed a consistent way to convert these strings to lists while handling edge cases like empty strings, extra spaces, or missing values. I wrote a helper function called `_split_csv` that handles all the edge cases in one place by splitting on commas, stripping whitespace, and filtering out empty strings. This function gets used in the `YogaPose.to_dict()` method to convert the stored strings into clean arrays before passing them to the template. Now the template consistently receives arrays for pose types and body parts, regardless of how the data is formatted in the database.

### Challenge 2: Case-Insensitive Search with SQL LIKE

When users search for poses, they might type "Hips" or "hips" or "HIPS" and expect to find the same results. The database stores body parts with mixed capitalization, so I needed the SQL query to ignore case differences. I also wanted partial matches to work—if someone types "hip" they should find poses targeting "hips" or "hip flexors". I solved this by using MySQL's `LOWER()` function on both the database column and the search term, then wrapping the search term with wildcards using the `LIKE` operator. So a search for "hip" becomes `%hip%` and matches anything containing those letters regardless of case. This makes the search feel more forgiving and user-friendly, even if people don't type exactly what's stored in the database.

---

## Results 

Compared to the original assignment, this version is a significant upgrade. The original was a simple API that returned a few hardcoded poses with basic names. Now each pose includes English and Sanskrit names, pose types (such as "Standing, Foundation, Balance"), target body parts, and step-by-step instructions. Instead of five sample poses, there are 100. 

The scripts I wrote to load data into the database demonstrate good software practices by separating concerns. The data is stored in a JSON file that's easy to edit, the script handles validation and normalization, and it uses the repository pattern to keep database access separate from the data loading logic. This means I can update the pose data without writing SQL, and the script checks for duplicates and validates the data before inserting anything. 

---

## Future Work

I'm interested in exploring how LLMs could help generate the yoga sequences. Instead of just finding individual poses, users could describe what they want, whether that's a body part or a morning routine, and an LLM could analyze the pose relationships and create a flowing sequence that makes sense. The model could consider which poses complement each other, what order feels natural, and how to creatively transition between poses (one of the best parts of yoga, in my opinion). This would move the app from being a simple lookup tool to something that can actually design custom routines and be useful to instructors and practitioners.

I'm also curious about experimenting with graph databases instead of MySQL. Graph databases have become really popular with LLM applications because they're excellent at representing relationships between entities, and I think that could reveal interesting and counterintuitive ways that poses relate to each other, which body parts they target, and how they flow together. Instead of storing poses in tables, a graph database would represent each pose as a node, and the edges could capture relationships like which poses complement each other, which ones target the same body parts, or which ones make good transitions. This structure would make it easier to discover pose relationships and could work really well with the LLM sequence generation. Plus, I'm interested in learning about different database technologies beyond the relational databases I've used so far, and graphs seem like an interesting next step for this project.

---

*Project completed: Summer 2024, Updated October 2025*  