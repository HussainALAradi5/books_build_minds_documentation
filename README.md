# Books Builds Minds documentation

This repository documents the separate [client](../client/README.md) and [server](../server/README.md) repositories in a sibling checkout. Each feature has subfolders for service signatures, business rules, endpoint mappings, and errors.

## Related repositories

| Repository | Local link | What it contains |
| --- | --- | --- |
| Documentation | This repository | Guides, service references, endpoint tables, and error cases |
| Client | [Open client repository](../client/README.md) | Next.js UI and workflow screenshots |
| Server | [Open server repository](../server/README.md) | Flask API, PostgreSQL models, migrations, and demo accounts |

These local links work when the three independent Git repositories are cloned as sibling folders. Add GitHub URLs after the remote repositories are created.

## How to use these docs

1. Follow [local setup](getting-started/README.md) to start PostgreSQL, Flask, and Next.js.
2. Use the [demo account guide](getting-started/demo-accounts.md) to sign in as an administrator, employee, or client and compare their access.
3. Follow the [illustrated client workflow](../client/docs/screenshots/workflows/README.md) to borrow, rent, return, purchase, download a receipt, and write a review.
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
- [Permissions, roles, and groups](features/access/README.md)

## Project guides

- [Architecture and API contract](architecture/README.md)
- [Access control](architecture/access-control.md)
- [Database migrations](database/migrations.md)
- [Local setup](getting-started/README.md)
- [Verification](testing/verification.md)
- [Screenshots](screenshots/README.md)
