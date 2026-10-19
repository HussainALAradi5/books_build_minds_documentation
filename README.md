# Books Builds Minds documentation

This repository documents the separate [client](https://github.com/HussainALAradi5/books_build_minds_client_side/blob/main/README.md) and [server](https://github.com/HussainALAradi5/books_build_minds_server/blob/main/README.md) repositories in a sibling checkout. Each feature has subfolders for service signatures, business rules, endpoint mappings, and errors.

## Related repositories

| Repository | GitHub link | What it contains |
| --- | --- | --- |
| Documentation | This repository | Guides, service references, endpoint tables, and error cases |
| Client | [Open client repository](https://github.com/HussainALAradi5/books_build_minds_client_side/blob/main/README.md) | Next.js UI and workflow screenshots |
| Server | [Open server repository](https://github.com/HussainALAradi5/books_build_minds_server/blob/main/README.md) | Flask API, PostgreSQL models, migrations, and demo accounts |

The related repository links point to the published GitHub repositories.

## Local demo users

After applying server migrations, run `python -m flask --app app seed-demo --test-accounts` in the server repository. Then sign in through the client with one of these local test accounts:

| Role | Username | Password | Access to try |
| --- | --- | --- | --- |
| Administrator | `demo_admin` | `LibraryAdmin2026!` | Manage books, inventory, imports, bulk actions, roles, and permissions |
| Employee | `demo_employee` | `LibraryStaff2026!` | Reader workflows and own profile; admin pages are restricted by default |
| Client | `demo_client` | `LibraryReader2026!` | Borrow, rent, return, purchase, download receipts, and review purchased books |

The seeded `demo_visitor` account is inactive and has no login password. Anonymous visitors can browse and register. These credentials are for local demo data only; see the [full role and permission guide](getting-started/demo-accounts.md).

## How to use these docs

1. Follow [local setup](getting-started/README.md) to start PostgreSQL, Flask, and Next.js.
2. Use the [demo account guide](getting-started/demo-accounts.md) to sign in as an administrator, employee, or client and compare their access.
3. Follow the [illustrated client workflow](https://github.com/HussainALAradi5/books_build_minds_client_side/blob/main/docs/screenshots/workflows/README.md) to borrow, rent, return, purchase, download a receipt, and write a review.
4. Select a feature below. Read its `services/README.md` for function signatures, business logic, and errors, then its `endpoints/README.md` for route-to-service mapping.
5. Use [architecture](architecture/README.md), [access control](architecture/access-control.md), and [migrations](database/migrations.md) when changing the implementation.

## Features

- [Accounts and profiles](features/accounts/README.md)
- [Books and purchases](features/books/README.md)
- [Authors, publishers, and countries](features/catalog/README.md)
- [Borrow, rent, and return](features/loans/README.md)
- [Media and profile pictures](features/media/README.md)
- [Reviews](features/reviews/README.md)
- [Receipts](features/receipts/README.md)
- [Tap BHD sandbox payments and transactions](features/payments/README.md)
- [Permissions, roles, and groups](features/access/README.md)

## Project guides

- [Architecture and API contract](architecture/README.md)
- [Access control](architecture/access-control.md)
- [Reusable tabs, guides, and animation](architecture/ui-components.md)
- [Security controls and deployment requirements](security/README.md)
- [Database migrations](database/migrations.md)
- [Local setup](getting-started/README.md)
- [Verification](testing/verification.md)
- [Screenshots](screenshots/README.md)
- [Third-party and external APIs](integrations/external-apis.md)
- [Flutter mobile application plan](mobile/README.md)
