# Engineering findings

## Phase 2 — Derived repository conventions and risks

### 1. API contract consistency

Hecho: `FinancialMovement` existe tanto en:

- `backend/app/routes.py`
- `frontend/src/lib/financial-types.ts`

Campos compartidos:

- `create_date`
- `amount`
- `operation_type`
- `category`
- `business_type`

En el backend, `create_date` es `date` y los demás campos están restringidos mediante tipos `Literal` y Pydantic. En el frontend, `create_date` se representa como `string` ISO y los valores permitidos se expresan mediante unions TypeScript.

Modificar solo uno de los contratos puede producir respuestas incompatibles, errores de cálculo o datos que el frontend no pueda consumir correctamente.

**Future rule candidate:** Los cambios al contrato deben revisar backend, frontend, consumidores y tests.

**Evidencia:** `backend/app/routes.py`, `frontend/src/lib/financial-types.ts`.

### 2. Primary API dependency

Hecho: `frontend/src/App.tsx` consume:

```text
GET /api/metrics
```

El componente ejecuta `fetch` sobre `${API_BASE_URL}/api/metrics` y utiliza los movimientos recibidos para calcular KPI y datos mensuales.

Cambios en la ruta, los campos de respuesta o el comportamiento del endpoint pueden afectar directamente al dashboard.

**Evidencia:** `frontend/src/App.tsx`, `frontend/src/lib/financial-utils.ts`, `backend/app/routes.py`.

### 3. Financial calculations

Hecho: existen cálculos financieros tanto en:

- `backend/app/routes.py`
- `frontend/src/lib/financial-utils.ts`

El backend calcula, entre otros valores, neto mediante `calculate_net_value`, resúmenes agrupados mediante `summarize_movements`, comparaciones y alertas. El frontend calcula KPI mediante `computeKPIs` y agrupaciones mensuales mediante `computeMonthlyData`, incluyendo ingresos, egresos, beneficio y porcentaje de beneficio.

La duplicación puede producir divergencias en fórmulas, redondeos, períodos o tratamiento de casos límite.

El repositorio no documenta cuál de las dos capas debería ser la única source of truth.

**Evidencia:** `backend/app/routes.py`, `frontend/src/lib/financial-utils.ts`, `frontend/src/lib/financial-utils.test.ts`.

### 4. Dates and displayed period

Hecho:

- el backend utiliza `date.today()` en `generate_mock_movements`;
- el frontend muestra `2024 - Full Year` mediante `DashboardHeader`.

La intención de esta combinación no está documentada. Existe un riesgo de que el período mostrado no coincida con las fechas reales de los datos generados.

**Evidencia:** `backend/app/routes.py`, `frontend/src/App.tsx`, `frontend/src/components/dashboard/dashboard-header.tsx`.

### 5. Deterministic mock data

Hecho: el backend utiliza:

```python
generate_mock_movements(seed=42)
```

Los tests verifican, entre otras cosas:

- 360 movimientos;
- orden cronológico;
- filtros de fecha inclusivos;
- filtros B2B/B2C;
- categorías y tipos de operación esperados.

Cambiar la generación de mocks, la semilla, la cantidad o la distribución puede alterar respuestas, contratos observados y tests existentes.

**Evidencia:** `backend/app/routes.py`, `backend/tests/test_routes.py`.

### 6. Runtime integration

Según `verification.md`, los servicios funcionan individualmente:

- el backend responde a `/health` y `/api/metrics`;
- el frontend sirve la aplicación;
- `/docs` responde correctamente.

Sin embargo, la ruta completa:

```text
frontend → Vite proxy → backend
```

no quedó validada debido al timeout observado en Phase 1 al solicitar:

```text
http://localhost:5173/api/metrics
```

**Future rule candidate:** Los cambios de integración deben validarse de extremo a extremo.

No se determina ni se especula sobre la causa del timeout.

**Evidencia:** `verification.md`, `frontend/vite.config.ts`, `docker-compose.yml`.

### 7. Frontend configuration coupling

Hecho: el alias `@` está definido tanto en:

- `frontend/vite.config.ts`
- `frontend/tsconfig.app.json`

Ambas configuraciones deben permanecer coherentes para que los imports y el build resuelvan correctamente.

Además, el proxy `/api` en Vite apunta a:

```text
http://backend:8000
```

Esto acopla la configuración del frontend al nombre del servicio backend usado por Docker Compose.

**Evidencia:** `frontend/vite.config.ts`, `frontend/tsconfig.app.json`, `docker-compose.yml`.

## Existing validation commands

### Frontend

Los comandos disponibles son:

```bash
npm test
npm run lint
npm run build
```

Cada comando está definido en los scripts de:

```text
frontend/package.json
```

- `npm test` ejecuta `vitest run`.
- `npm run lint` ejecuta `eslint .`.
- `npm run build` ejecuta `tsc -b && vite build`.

### Backend

El comando disponible es:

```bash
pytest
```

La disponibilidad de pytest está demostrada por:

```text
backend/requirements.txt
```

La existencia de tests está demostrada por:

```text
backend/tests/test_routes.py
```

## Findings not promoted to repository rules yet

Las siguientes observaciones están verificadas, pero no se promueven todavía a reglas obligatorias:

- **Configuración CORS:** `backend/app/main.py` utiliza `allow_origins=["*"]`, `allow_credentials=True`, `allow_methods=["*"]` y `allow_headers=["*"]`. Podría convertirse en una regla futura si una tarea introduce autenticación, despliegue externo o requisitos de seguridad.
- **`depends_on` sin healthcheck:** `docker-compose.yml` declara `depends_on: - backend`, pero no define `healthcheck`. Podría justificar una regla futura si una tarea modifica el arranque o la disponibilidad entre servicios.
- **Cobertura limitada de algunos errores:** `backend/tests/test_routes.py` cubre varios casos válidos, pero no documenta una cobertura exhaustiva de errores de validación o fechas inválidas. Podría convertirse en una regla si una tarea amplía parámetros o validaciones.
- **Estructura de carpetas frontend:** existen `frontend/src/components/dashboard/`, `frontend/src/components/ui/` y `frontend/src/lib/`. Podría convertirse en una regla de organización si una tarea añade componentes o nuevas utilidades.

Estas observaciones requieren contexto de una tarea concreta antes de convertirse en reglas de agentes.
