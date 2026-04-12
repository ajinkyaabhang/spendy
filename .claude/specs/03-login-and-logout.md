# Spec: Login and Logout

## Overview
Implement session-based login and logout so registered users can authenticate with Spendly. The `/login` route already handles GET (renders the static form); this step wires up the POST handler to validate credentials against the `users` table, store the user's `id` and `name` in the Flask session on success, and redirect to `/profile`. The `/logout` route clears the session and redirects to the landing page. Together these two routes establish the auth layer that all subsequent protected routes (profile, expenses) depend on.

## Depends on
- Step 01 — Database Setup (`users` table exists, `get_db()` works)
- Step 02 — Registration (users can be created and stored with hashed passwords)

## Routes
- `POST /login` — validate email/password, set session, redirect to `/profile` — public
- `POST /logout` — clear session, redirect to `/` — logged-in

The existing `GET /login` route stays unchanged (renders `login.html`). The route handler is updated to accept both methods. The existing `GET /logout` stub is replaced with a `POST`-only route.

## Database changes
No database changes. The `users` table already has all columns needed (`id`, `name`, `email`, `password_hash`).

## Templates
- **Modify:** `templates/login.html`
  - Add `method="POST"` and `action="{{ url_for('login') }}"` to the `<form>` tag if not already present.
  - Repopulate `email` input with `value="{{ request.form.email }}"` so the user does not lose their input on a failed login.
  - Display inline error block when `error` is passed from the route (same pattern as `register.html`).
- **Modify:** `templates/base.html`
  - Update the navbar logout link to use a `<form method="POST" action="{{ url_for('logout') }}">` with a submit button styled as a link, so logout is a POST request.
  - Show login/register links when user is not in session; show username + logout button when `session.get('user_name')` is set.

## Files to change
- `app.py`
  - Import `check_password_hash` from `werkzeug.security` (alongside existing `generate_password_hash`).
  - Import `session` from `flask` (alongside existing imports).
  - Update `/login` route to accept `GET` and `POST` methods; add POST logic.
  - Replace `/logout` GET stub with a `POST`-only route that clears the session.
- `templates/login.html` — form action/method, email repopulation, inline error display.
- `templates/base.html` — conditional nav links and logout form.

## Files to create
None.

## New dependencies
No new dependencies. `werkzeug.security` and `flask.session` are already available.

## Rules for implementation
- No SQLAlchemy or ORMs — use raw `sqlite3` via `get_db()`
- Parameterised queries only — never use string formatting in SQL
- Verify passwords with `werkzeug.security.check_password_hash` — never compare plain text
- Use CSS variables — never hardcode hex values
- All templates extend `base.html`
- Store only `user_id` (int) and `user_name` (str) in the session — never store the password hash
- On failed login (wrong email or wrong password) return a **single generic error** — "Invalid email or password." — do not reveal which field was wrong
- On success redirect to `url_for('profile')` (the stub is fine for now; Step 04 will implement it)
- `/logout` must be `POST` only — a `GET /logout` must not clear the session; redirect to landing if accessed via GET
- Do not use flash messages — pass `error=` kwarg directly to `render_template`

## Definition of done
- [ ] `GET /login` renders the login form correctly
- [ ] Submitting valid credentials sets `session['user_id']` and `session['user_name']` and redirects to `/profile`
- [ ] Submitting an unknown email returns the form with "Invalid email or password." error (no crash)
- [ ] Submitting a correct email but wrong password returns the same generic error
- [ ] Email input is repopulated after a failed login attempt
- [ ] `POST /logout` clears the session and redirects to `/`
- [ ] After logout, `session.get('user_id')` is `None`
- [ ] Navbar shows login/register links when logged out and username + logout button when logged in
- [ ] App starts without errors (`python app.py`)
