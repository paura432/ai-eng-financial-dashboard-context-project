# Current status

## Working

- Frontend serves the application on port `5173`; backend runs on `8000` with debugpy on `5678`.
- Backend `/health`, `/api/metrics` and `/docs` returned HTTP 200 in the Phase 1 runtime check. Frontend also served the application.
- Mock movements are generated with `generate_mock_movements(seed=42)`; the backend test verifies 360 chronologically ordered records.
- Frontend utility tests and backend route tests exist.

## Known issue / unverified

During Phase 1, requests through `localhost:5173/api/metrics` and from the frontend container to `backend:8000/api/metrics` timed out; direct host access to backend `/api/metrics` succeeded. The cause remains `❓ unverified`. This is not a confirmed code bug. See `verification.md`.

## Agent readiness

`AGENTS.md`, `.agents/rules/`, `verification.md` and `engineering-findings.md` exist. This `memory-bank/` adds persistent project context. Phase 1 recorded that rules and memory bank did not yet exist; that was the state at that time, not the current state.

## Important constraints

- Keep backend and frontend API contracts aligned; see `.agents/rules/api-contracts.md`.
- Review both financial implementations before changing calculations; the repo does not declare one source of truth.
- Preserve inclusive date filters and chronological movement order unless the contract changes explicitly. Review seed-42 mock behavior when changing generated data.
- For integration changes, validate runtime and the full frontend -> Vite proxy -> backend path; containers being `Up` is insufficient.

## Evidence

- `verification.md`
- `engineering-findings.md`
- `.agents/rules/`
- `backend/app/routes.py`
- `backend/tests/test_routes.py`
- `frontend/src/App.tsx`
- `frontend/src/lib/financial-utils.test.ts`
- `docker-compose.yml`
- `frontend/Dockerfile`
- `backend/Dockerfile`
