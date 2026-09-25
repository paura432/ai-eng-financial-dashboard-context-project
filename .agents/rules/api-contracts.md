# API contract rules

## Scope

Apply this rule when changing API models, response fields, query parameters, endpoints, or frontend API consumers.

## Rules

1. Treat `FinancialMovement` as a shared contract. It exists in both:
   - `backend/app/routes.py` as the Pydantic model and its `Literal` fields.
   - `frontend/src/lib/financial-types.ts` as the TypeScript interface and unions.

2. When changing a field name, field type, allowed value, serialization, or response shape, review and update together:
   - `backend/app/routes.py`;
   - `frontend/src/lib/financial-types.ts`;
   - all consumers, including `frontend/src/App.tsx` and `frontend/src/lib/financial-utils.ts`;
   - relevant frontend and backend tests.

3. Do not assume that changing only the backend is sufficient. The frontend currently consumes `GET /api/metrics` from `frontend/src/App.tsx` and expects the movement fields defined in `frontend/src/lib/financial-types.ts`.

4. When frontend starts consuming a new backend endpoint, define TypeScript types aligned with that endpoint's Pydantic `response_model` in `backend/app/routes.py` before using its response in components or utilities.

5. Preserve the API path and response contract unless the task explicitly changes the contract and updates its consumers and tests. The current primary frontend request is `GET /api/metrics`.

## Evidence

- `backend/app/routes.py`
- `frontend/src/lib/financial-types.ts`
- `frontend/src/App.tsx`
- `frontend/src/lib/financial-utils.ts`
- `backend/tests/test_routes.py`
- `frontend/src/lib/financial-utils.test.ts`
