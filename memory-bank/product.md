# Product

Dashboard de métricas financieras. Presenta ingresos, egresos, beneficio y margen de beneficio, además de gráficos mensuales de ingresos/egresos y margen.

Los datos actuales son movimientos simulados generados en backend por `generate_mock_movements(seed=42)`; no hay persistencia de base de datos en el flujo verificado.

Partes principales: dashboard React en `frontend/src/App.tsx` y `frontend/src/components/dashboard/`; cálculos y tipos en `frontend/src/lib/`; API y generación de datos en `backend/app/`.

Flujo principal: `App.tsx` solicita `GET /api/metrics` y calcula KPI y series mensuales en el frontend. En Docker Compose, las solicitudes `/api` pasan por el proxy de Vite hacia `http://backend:8000`. El recorrido completo no quedó validado; ver `current-status.md`.

## Evidence

- `frontend/src/App.tsx`
- `frontend/src/components/dashboard/`
- `frontend/src/lib/financial-utils.ts`
- `backend/app/routes.py`
- `docker-compose.yml`
- `frontend/vite.config.ts`
- `verification.md`
