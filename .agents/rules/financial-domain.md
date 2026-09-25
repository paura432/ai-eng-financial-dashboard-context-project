# Financial domain rules

## Scope

Apply this rule when changing financial formulas, aggregations, filters, generated data, dates, periods, or movement ordering.

## Rules

1. Review both financial implementations before changing a formula or aggregation:
   - backend logic in `backend/app/routes.py`;
   - frontend logic in `frontend/src/lib/financial-utils.ts`.

   The repository does not define a single source of truth. Do not declare one without explicit repository evidence.

2. Preserve the current inclusive date-filter semantics unless the task explicitly changes the contract:
   - `create_date >= start_date`;
   - `create_date <= end_date`.

   Evidence: `backend/app/routes.py` and `backend/tests/test_routes.py`.

3. Preserve chronological ordering for movement responses. Use the existing ordering behavior represented by `ensure_chronological_order` and covered by `backend/tests/test_routes.py`.

4. Treat `generate_mock_movements(seed=42)` as the current reproducible mock-data behavior. Changes to the seed, number, distribution, categories, or generated dates require reviewing the affected backend tests and consumers.

5. When changing generated dates or date grouping, review displayed period labels. The backend uses `date.today()` in `backend/app/routes.py`, while the dashboard currently displays `2024 - Full Year` in `frontend/src/App.tsx`.

6. When changing a financial metric, check formulas, rounding, zero-income behavior, and period grouping in both implementations and their tests. Relevant code is in `backend/app/routes.py`, `frontend/src/lib/financial-utils.ts`, `backend/tests/test_routes.py`, and `frontend/src/lib/financial-utils.test.ts`.

## Evidence

- `backend/app/routes.py`
- `frontend/src/lib/financial-utils.ts`
- `frontend/src/App.tsx`
- `backend/tests/test_routes.py`
- `frontend/src/lib/financial-utils.test.ts`
