# Release verification — 22 October 2026

## Scope

The Flask server and Next.js client were run against a disposable SQLite database seeded with the documented demo accounts and six catalog books. A separate disposable SQLite database checked the migration chain. The configured PostgreSQL database and any live payment provider were not part of this check.

## Results

| Check | Result |
| --- | --- |
| Client lint and TypeScript | Passed (`npm run lint`, `npm run typecheck`) |
| Client production build | Passed on Next.js 16.4.1 (`npm run build`) |
| Client dependency audit | Zero reported vulnerabilities (`npm audit --audit-level=low`) |
| Profile hydration | No browser warnings after full reload in production and development modes |
| Borrow and return | Demo client borrowed `deep-work`: available copies changed 1 → 0 and the loan appeared on the profile. Returning it restored availability to 1 and marked the loan returned. |
| Server unit/API suite | All 55 tests passed (`python -m unittest discover -s tests -q`) |
| Database migrations | Fresh SQLite database upgraded through `c6d4e27a0012`; `flask db check` reported no new operations |
| Mobile static analysis | No issues from `flutter analyze --no-pub` |
| Mobile widget and contract tests | All 10 tests passed (`flutter test --no-pub`) |

The client now uses Chakra UI's Emotion registry for streamed App Router rendering. Next.js was updated from 16.4.0 to 16.4.1 because published advisories list 16.4.0 as affected by image optimization remote code execution and Server Action denial of service. [Image optimization advisory](https://github.com/advisories/GHSA-2464-fhr4-w9xw) · [Server Action advisory](https://github.com/advisories/GHSA-p3m5-59mp-jv5r).

## Limits

Flutter analysis and tests used a temporary directory inside the mobile workspace because the default sandbox temp directory blocked the test compiler. The client checks used local Node.js 25.2.1 although `package.json` requests Node.js 24.x; repeat them on Node.js 24 before deployment. This local check does not establish PostgreSQL migration health, payment provider readiness, production hosting security, or Android device behavior. Continue to use the [deployment checklist](../security/deployment/README.md) before publishing a public service.
