# Tech stack

## Frontend

React, TypeScript, Vite, Tailwind CSS, Recharts, Vitest and ESLint. Dependencies, scripts and plugins are declared in `frontend/package.json` and `frontend/vite.config.ts`.

## Backend

Python, FastAPI, Uvicorn, Pydantic and pytest. Runtime dependencies are in `backend/requirements.txt`; app setup is in `backend/app/main.py`, API models in `backend/app/routes.py`, and tests in `backend/tests/`.

## Runtime and configuration

Docker Compose runs frontend on port `5173` and backend on `8000`; debugpy listens on `5678`. See `docker-compose.yml` and both Dockerfiles.

Vite proxies `/api` to `http://backend:8000`. `VITE_API_BASE_URL` is read by `frontend/src/App.tsx` and defaults to an empty string. The `@` alias is configured in `frontend/vite.config.ts` and `frontend/tsconfig.app.json`.

## Validation commands

From `frontend/`: `npm test`, `npm run lint`, `npm run build` (scripts in `frontend/package.json`). From `backend/`: `pytest` (dependency in `backend/requirements.txt`; tests in `backend/tests/`). The recorded Compose runtime command is `docker compose up --build`; integration changes also require checking the affected runtime path and endpoints.
