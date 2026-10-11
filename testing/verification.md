# Verification

From `server`, run the unit/API tests and the migration drift check:

```powershell
.\.venv\Scripts\python.exe -m unittest discover -s tests -v
.\.venv\Scripts\flask.exe --app app db check
```

From `client`, run the TypeScript, lint, and production build gates:

```powershell
npm run typecheck
npm run lint
npm run build
```

Review the [client screenshots](../../client/docs/screenshots/README.md) for desktop/mobile light and dark layouts. Tests exercise permissions, role/group assignments, purchase-gated reviews, loans, and the shared API behavior. A local end-to-end preview requires both running processes and PostgreSQL.
