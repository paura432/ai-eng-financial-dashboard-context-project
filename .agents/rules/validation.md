# Validation rules

## Scope

Apply the validation that matches the files and behavior changed.

## Commands

### Frontend changes

From `frontend/`, run:

```bash
npm test
npm run lint
npm run build
```

These commands are defined in `frontend/package.json`.

### Backend changes

From `backend/`, run:

```bash
pytest
```

The dependency is declared in `backend/requirements.txt` and tests are in `backend/tests/`.

## Integration, API, Docker, and proxy changes

1. Validate runtime in addition to tests. Use the repository's Compose workflow and check the affected endpoints.
2. Test the complete path `frontend → Vite proxy → backend`, not only direct backend requests. The proxy target is configured in `frontend/vite.config.ts`.
3. Do not treat containers being `Up` as proof that integration works. `verification.md` records a known case where services were individually available but the proxied `/api/metrics` request timed out.
4. When validating Codespaces, do not assume `localhost` is the public URL. Distinguish the configured port, the documented local URL, and the forwarded Codespaces URL. Use the Codespaces forwarded-port information for the public URL.
5. Use `verification.md` as the history of verified runtime behavior and known unresolved integration findings.

## Evidence status

When a statement cannot be verified from repository evidence, write:

```text
❓ unverified
```

When a statement is shown to be incorrect, record both the original claim and its correction:

```text
❌ claim → ✅ correction
```

## Evidence

- `frontend/package.json`
- `backend/requirements.txt`
- `backend/tests/`
- `frontend/vite.config.ts`
- `docker-compose.yml`
- `verification.md`
