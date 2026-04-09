# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
# Set up virtual environment
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Run the app (port 5001)
python app.py

# Run tests
pytest

# Run a single test file
pytest tests/test_file.py

# Run a single test
pytest tests/test_file.py::test_function_name
```

## Architecture

**Spendly** is a Flask expense tracker with server-side Jinja2 rendering and SQLite storage.

### Stack
- **Backend:** Flask 3.1.3, Python, SQLite
- **Frontend:** Jinja2 templates, custom CSS (`static/css/`), vanilla JS (`static/js/main.js`)
- **Testing:** pytest + pytest-flask

### Key Files
- `app.py` — Flask app, all route definitions
- `database/db.py` — SQLite helpers: `get_db()`, `init_db()`, `seed_db()` (stub, to be implemented)
- `templates/base.html` — Base layout with navbar/footer; all other templates extend this
- `static/css/style.css` — Primary stylesheet with CSS variables and responsive breakpoints

### Route Structure
Currently implemented: `/`, `/login`, `/register`, `/terms`, `/privacy`  
Planned (Steps 3–9): `/logout` (POST), `/profile`, `/expenses/add`, `/expenses/<id>/edit`, `/expenses/<id>/delete`

### Database
SQLite file at `expense_tracker.db` (git-ignored). The `database/db.py` module needs:
- `get_db()` — returns a connection with `row_factory` and foreign keys enabled
- `init_db()` — creates tables using `CREATE TABLE IF NOT EXISTS`
- `seed_db()` — inserts sample data for development

### Template Inheritance
All pages extend `base.html`. Use `{% block content %}` to inject page-specific content.
