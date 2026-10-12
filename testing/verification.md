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

Review the [client screenshots](https://github.com/HussainALAradi5/books_build_minds_client_side/blob/main/docs/screenshots/README.md) for desktop/mobile light and dark layouts. Tests exercise permissions, role/group assignments, purchase-gated reviews, loans, and the shared API behavior. A local end-to-end preview requires both running processes and PostgreSQL.

## Latest validation — 2026-10-12

- Server: 21 unit/API tests passed using isolated SQLite databases.
- Client: production build passed from a clean local validation copy after Windows denied creation of output folders in the original checkout.
- Browser: lazy tabs, keyboard navigation, preserved filter/form state, guide navigation/reset/focus, reduced motion, admin/book tabs, and mobile width checks passed with no browser errors.
- Twelve refreshed desktop/mobile light/dark screenshots are in the [tabs gallery](https://github.com/HussainALAradi5/books_build_minds_client_side/blob/main/docs/screenshots/tabs/README.md).

These checks do not certify production deployment or PostgreSQL behavior. See the [security deployment requirements](../security/deployment/README.md).
