# Spec: Registration

## Overview
Implement user registration so visitors can create a Spendly account. The `/register` route currently only handles GET and renders a static form. This step wires up the POST handler to validate submitted data, hash the password, insert the new user into the `users` table, and redirect to the login page on success. Errors (missing fields, duplicate email, short password) are re-rendered inline on the form. This is the first step that writes user data to the database and establishes the pattern used by all subsequent auth routes.

## Depends on
- Step 01 — Database Setup (`database/db.py` fully implemented, `users` table exists)

## Routes
- `POST /register` — process registration form submission — public

The existing `GET /register` route stays unchanged (renders `register.html`). The route handler is updated to accept both methods.

## Database changes
No database changes. The `users` table (id, name, email, password_hash, created_at) already exists from Step 01.

## Templates
- **Modify:** `templates/register.html`
  - Repopulate `name` and `email` inputs with `value="{{ request.form.name }}"` / `value="{{ request.form.email }}"` so the user does not lose their input on a validation error.
  - No structural changes needed — the form, error block, and POST action are already in place.

## Files to change
- `app.py` — add `SECRET_KEY`, update `/register` to accept `POST`, import `request`, `redirect`, `url_for`, `session` from flask; import `generate_password_hash` from werkzeug; add POST logic

## Files to create
None

## New dependencies
No new dependencies. `werkzeug.security` is already installed as part of Flask.

## Rules for implementation
- No SQLAlchemy or ORMs — use raw `sqlite3` via `get_db()`
- Parameterised queries only — never use string formatting in SQL
- Hash passwords with `werkzeug.security.generate_password_hash`
- Use CSS variables — never hardcode hex values
- All templates extend `base.html`
- Set `app.secret_key` using a hard-coded dev string (e.g. `"dev-secret-change-in-prod"`); a comment must note it must be replaced in production
- Validate server-side: name required, valid email format (basic non-empty check is fine), password minimum 8 characters
- On duplicate email, catch the `sqlite3.IntegrityError` and return the form with a user-friendly error message
- On success, redirect to `/login` (dashboard does not exist yet)
- Do **not** log the user in after registration — sessions are handled in a later step
- Re-render form with `error=` kwarg on failure; do not use flash messages

## Definition of done
- [ ] `GET /register` still renders the empty form correctly
- [ ] Submitting the form with all valid fields inserts a new row into `users` and redirects to `/login`
- [ ] The new user's `password_hash` column is a Werkzeug hash, not plain text
- [ ] Submitting with a blank name returns the form with an error message (no DB insert)
- [ ] Submitting with a password shorter than 8 characters returns the form with an error message
- [ ] Submitting an email that already exists returns the form with a "Email already registered" error (no crash)
- [ ] Name and email inputs are repopulated after a failed submission
- [ ] App starts without errors (`python app.py`)
