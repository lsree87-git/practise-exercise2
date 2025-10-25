## Purpose

This file gives concise, repository-specific guidance for AI coding agents working on the
`practise-exercise2` sample FastAPI + static frontend project. Focus on quick, actionable
knowledge that makes an agent productive immediately.

## Big picture

- Backend: `src/app.py` — a small FastAPI app exposing an `app` object. It mounts `src/static`
  at `/static` and redirects `/` to `/static/index.html`.
- Frontend: `src/static/index.html`, `src/static/app.js`, `src/static/styles.css` — a tiny single-
  page UI that fetches `/activities` and posts signups to `/activities/{activity_name}/signup`.
- Data: in-memory `activities` dict in `src/app.py`. No persistence — state resets on restart.

## How to run (explicit, reproducible)

- Install dependencies: `pip install -r requirements.txt` (file lists `fastapi` and `uvicorn`).
- Run server (recommended):

  python -m uvicorn src.app:app --reload --host 0.0.0.0 --port 8000

  Notes: the package exposes `app` in `src/app.py`, so use the `src.app:app` import target.

## Important endpoints & examples

- GET /activities — returns full `activities` dict (see `get_activities` in `src/app.py`).
- POST /activities/{activity_name}/signup?email=... — sign up a student. Example fetch from
  frontend (see `src/static/app.js`):

  fetch(`/activities/${encodeURIComponent(activity)}/signup?email=${encodeURIComponent(email)}`, { method: 'POST' })

  Remember: activity names are used as keys (e.g. "Chess Club") — they must be URL-encoded
  when used in path segments.

## Project-specific patterns and conventions

- Activities are modelled as a dict keyed by the activity name (string). Editing or adding
  activities is typically done by updating the `activities` dict in `src/app.py`.
- The frontend uses direct client-side fetches to the backend (no CORS handling required when
  served from the same origin because the backend mounts `static` under `/static`).
- No database layer or schemas — treat changes to the data model as code edits (no migration
  system to update).

## Developer workflows

- Local dev server: use the uvicorn command above. The `--reload` flag helps during edits.
- API docs: once running, visit `/docs` (Swagger UI) or `/redoc`.
- Tests: `pytest` is configured via `pytest.ini` (pythonpath = .). There are no tests in the
  repository by default; add tests under `tests/` and run `pytest`.

## Integration points and gotchas for agents

- When changing endpoint signatures or response shapes, also update `src/static/app.js` which
  directly consumes the JSON returned by `/activities`.
- Because activity names contain spaces, use `encodeURIComponent` for path segments. The
  backend looks up `activity_name` directly in the dict and is case-sensitive.
- The app is deliberately minimal: adding persistence requires introducing a DB and changing
  the in-memory usage; be explicit about migration and new dependency additions in PRs.

## Files to inspect first (quick checklist)

- `src/app.py` — backend, `app` instance, mount point, endpoints, `activities` data
- `src/static/app.js` — client fetch patterns and signup request format
- `src/static/index.html` — structure of the UI and where `app.js` is loaded
- `requirements.txt` — runtime dependencies
- `pytest.ini` — test runner config

## Example small tasks an agent can do safely

- Update text or add an activity to the `activities` dict (low-risk, in-memory change). Example:
  add a new dict key under `activities` with description/schedule/max_participants.
- Add basic unit tests for `get_activities` and `signup_for_activity` (create `tests/test_api.py`).

## When to ask the human

- If you plan to add a persistent database or authentication, ask about the preferred DB and
  whether backward compatibility is required.
- If renaming activity identifiers or changing case-sensitivity rules, confirm how existing
  frontend behaviour should map to backend keys.

---

If any of the above is unclear or you'd like this file expanded with example PR snippets or
test templates, tell me what to add and I'll iterate.
