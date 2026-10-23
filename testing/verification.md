# Verification

For the latest local checks, see [22 October 2026 release verification](compatibility-2026-10-22.md). The dated records below are historical.

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

## Latest validation — 2026-10-17

- Server: 44 unit/API tests passed. A clean in-memory SQLite database upgraded sequentially from the new baseline through revision `e47a2c9d0010`.
- Client: lint and the production build passed with the reusable Chakra progress bar in workflow dialogs and book availability. TypeScript passed as part of the build.
- The first build attempt used a temporary output folder and hit a Windows `EPERM` directory-creation error; the normal `npm run build` completed successfully.
- The latest Reset filters and progress interactions still need a local browser walkthrough. This validation does not establish PostgreSQL deployment or provider sandbox readiness.

## Local validation — 2026-10-18

- Server: 44 unit/API tests passed. A fresh in-memory SQLite database applied all migrations from the baseline to `e47a2c9d0010`.
- Local PostgreSQL: backed up before upgrade; migrated from `d0a2e93c0009` to `e47a2c9d0010`. `flask db current` reported the head and `flask db check` reported no new upgrade operations.
- Client: the normal `npm run build` passed, including TypeScript checking.
- Browser against the production client build and local Flask API: catalog Reset refreshed results without a document reload; availability and workflow progress rendered at desktop and mobile dark widths; the review owner edited inside the card while an administrator saw Delete without Edit. No browser page errors were observed.
- Six refreshed screenshots are in the [workflow gallery](https://github.com/HussainALAradi5/books_build_minds_client_side/blob/main/docs/screenshots/workflows/README.md#refreshed-interaction-checks--2026-10-18).

These are local checks. Production deployment still requires the controls listed in the [security deployment guide](../security/deployment/README.md). No live payment gateway is configured.
