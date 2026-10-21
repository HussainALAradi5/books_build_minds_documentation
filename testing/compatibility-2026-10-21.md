# Cross-client compatibility check — 21 October 2026

This check compares the current sibling `server`, `client`, and `books_build_minds_mobile` checkouts. It covers registered API routes, typed client calls, build gates, and local automated tests. It does not certify a hosted deployment or a real device.

## Results

| Area | Web client | Flutter client | Flask API |
| --- | --- | --- | --- |
| Catalog and book detail | Initial pages use `bookQueryService`; browser filters use `bookService.catalog` and `/catalog-data` | `BookApi` uses `GET /api/` and `GET /api/book/{slug}` | Both routes are registered; filtering and pagination have backend tests |
| Account and recovery | Next server actions call login, register, and reset routes; protected calls forward the HttpOnly token | `AuthApi` calls the same routes directly and stores the token in secure storage | Login identifiers and one-use reset codes have backend tests |
| Loans and reviews | Typed services call loan, quote, borrow, return, and review routes through the Next proxy | `LoanApi` and `ReviewApi` call the same Flask paths | Borrow/return, purchase-gated reviews, ownership, and permissions have backend tests |
| Transactions and administration | Typed transaction and access services call the registered routes | Mobile supports checkout, transaction listing/sync, inventory price updates, and read-only access lists | Routes are registered; payment, price, and permission tests pass |
| Response keys | Shared conversion maps backend snake_case JSON to camelCase interfaces | Models read backend snake_case JSON | API DTOs and route results use snake_case |

The server reorganized `models`, `services`, `utils`, and `controllers` into domain subfolders. Flask route paths, model table names, and migration files were not changed. Its full suite passed after the moves. Flutter registration now accepts the API's optional `mobile_number`, validates international format, and sends the normalized value.

## Checks run

- Flask: **55 unit/API tests passed** after the folder refactor and demo profile enrichment. Tests cover catalog filters, security, borrow/return, media, reviews, demo roles, payments, receipts, password reset, and preserving manually edited catalog profiles.
- Migrations: a disposable SQLite database upgraded from the baseline through `c6d4e27a0012`; `flask db check` then reported no new upgrade operations. The disposable database was removed after verification.
- Next.js: `npm run lint` and `npm run build` passed. The build includes TypeScript checking. The repository retains the one-worker build setting needed for this Windows checkout.
- Flutter: `flutter analyze` found no issues; `flutter test --concurrency=1` passed **10 tests** when `TEMP` and `TMP` pointed to the project's `.tmp` folder. Contract tests cover book JSON, inherited pagination filters, request DTOs including optional mobile registration, and auth middleware.
- Android APK: direct Gradle `:app:assembleDebug` builds with JDK 21 succeeded before and after replacing the template application ID and debug-signed release configuration. The final APK identifies as `com.booksbuildminds.mobile`, version `0.1.0` (code 1), with Android minimum SDK 24 and target SDK 36. Release signing uses an ignored local `android/key.properties` file and still needs a real keystore and store account.
- Android release compilation: `flutter build appbundle --release --no-pub --dart-define=API_BASE_URL=https://api.example.invalid` succeeded with JDK 21. `jarsigner -verify` reports **jar is unsigned**, as intended when no local signing key is configured. The placeholder API origin makes this a build-check artifact only; it must not be uploaded or installed as a working release.
- Local PostgreSQL: a custom-format backup was saved outside the repositories at `D:/IT/Projects/books_builds_minds/server-backups/before-mobile-migrations-20261021-113748.dump`. Revisions `f5c2a16b0011` and `c6d4e27a0012` were then applied individually. `flask db current` reports `c6d4e27a0012 (head)` and `flask db check` reports no new upgrade operations.
- PostgreSQL API smoke check: the catalog returned a paginated page with three books; author, publisher, country, book detail, and public review reads returned `200`. The documented demo client signed in; its own profile and a book loan quote returned `200`. This was a Flask test-client check against the configured PostgreSQL database, not a browser or phone walkthrough.
- Live HTTP smoke check: the running Flask server returned a catalog page and all three demo roles signed in and received a loan quote. Admin access returned `200` for `demo_admin` and `403` for employee and client. The Next.js production server returned `200` for home, a book detail, login, registration, password recovery, and the catalog proxy. Guest profile redirected to login; guest admin redirected home.
- Browser walkthrough: `demo_client` signed in through the Next.js page, opened the profile and loans tab, then opened **Deep Work**. The book description toggle, author and publisher dialogs, and first two borrow guide steps rendered. No borrow was submitted or terms accepted. A fresh PostgreSQL backup was saved at `D:/IT/Projects/books_builds_minds/server-backups/before-demo-enrichment-20261021-152740.dump` before rerunning the idempotent seed. The refreshed page then displayed the seeded book description and both creator profiles with country, description, and works.
- Android emulator: three AVD definitions exist, but `Small_Phone` exited before boot. Its diagnostic reports missing usable Windows hardware acceleration. `adb devices` showed no connected device, so installation and on-device interaction remain unverified.

## Release checks still required

1. Run the remaining browser and mobile journeys, including final borrow/return submission, review ownership, and password recovery delivery. The checks above verify routes and several screens but do not replace full interaction tests.
2. Install the fresh Android build on a device or an emulator with working acceleration. The Magic7 Pro screenshots are layout previews, not device certification.
3. For a store release, create and protect a release keystore, configure an HTTPS API, and verify the signed app bundle on a device. If sandbox checkout is included, also verify provider callbacks and receipts with a configured test account. Automated payment tests use mocked provider responses.

No production payment or email delivery was verified in this check.
