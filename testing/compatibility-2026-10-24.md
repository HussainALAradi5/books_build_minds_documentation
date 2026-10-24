# Compatibility and security review — 24 October 2026

## Scope

This check covered the current Flask server, Next.js client, Flutter mobile app, and documentation checkouts. The registered Flask routes were compared with the web services and Flutter API classes. The review used the shared-contract, web, server, mobile, documentation, and release-verification skill guidance. Tests and builds below were run locally; prior dated reports remain historical evidence.

## Results

| Area | Check | Result |
| --- | --- | --- |
| Server | `python -m unittest discover -s tests -q` | 56 tests passed after the pagination fix |
| PostgreSQL | `flask --app app db check` | No new upgrade operations detected |
| PostgreSQL API | Flask test client against the configured database | Catalog, authors, publishers, countries, demo-client login, own profile, loans, and transactions returned `200` |
| Web | `npm run typecheck`, `npm run lint`, `npm run build` | Passed; production build used Node 24.22.1 |
| Web dependencies | `npm audit --audit-level=low` | No reported vulnerabilities |
| Server dependencies | `pip-audit --vulnerability-service osv --path .venv/Lib/site-packages` | No known vulnerabilities in 38 installed packages after updating Pillow and pip |
| Flutter | `flutter analyze --no-pub` | No issues |
| Flutter | `flutter test --no-pub` | 10 tests passed |
| Android | `flutter build apk --debug --no-pub` | Passed with an explicit JDK 21 path; APK generated |
| Documentation | Local Markdown links in tracked project docs | No broken relative links found |

The production Next.js server and Flask API also passed HTTP smoke checks: public home, login, registration, password recovery, catalog proxy, and API catalog returned `200`; anonymous profile and admin requests redirected to their expected pages. The client services and Flutter APIs use registered Flask routes for their implemented catalog, authentication, profile, loan, review, transaction, and administrator actions. Flutter currently implements fewer administration and file operations than the web client; this is stated in the [mobile API contract](../mobile/architecture/api-contracts.md).

## Fixed during this review

A very large `page` query value previously overflowed PostgreSQL's offset and returned HTTP `500`. The shared pagination validator now rejects pages above 100,000 with HTTP `400` across paginated endpoints. A regression test covers the catalog and reviews, and the failing request was repeated against PostgreSQL after the fix.

The server requirement now uses Pillow 13, which addresses the known advisories reported for the installed Pillow 12.3.0. The local environment was updated and all 56 server tests passed again. The pip upgrade was local tooling maintenance; pip is not an application requirement.

The repository READMEs now link the Flutter project alongside the server, web client, and documentation. The [API contract](../architecture/api-contract.md) records the page limit.

## Security and release limits

- No environment files or private key files were found among the tracked server, web, or mobile files checked. Example environment files are tracked intentionally.
- The known production controls remain in the [deployment requirements](../security/deployment/README.md): HTTPS, persistent secrets, shared rate limits, browser headers, protected media where needed, backups, and removal of demo access.
- This review did not complete a new browser interaction walkthrough, physical-device installation, payment-provider callback, or email-delivery test.
- Automated tests use mocked payment-provider responses. Passing tests do not establish a working Tap sandbox account or production payment readiness.
- Source publication and local builds are distinct from a production deployment. Complete the deployment requirements and real-device/provider checks before offering this as a public library service.
