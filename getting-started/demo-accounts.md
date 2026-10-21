# Local demo accounts and permissions

Run `python -m flask --app app seed-demo --test-accounts` from the [server repository](https://github.com/HussainALAradi5/books_build_minds_server/blob/main/README.md) after migrations. This development-only command updates the three active demo passwords to the values below. The seed command refuses to run when `APP_ENV=production`.

To refresh missing book descriptions and author/publisher profile details later without changing existing demo passwords, run `python -m flask --app app seed-demo` with no flags. Existing nonempty catalog profile fields are preserved.

| Account role | Username | Password | What to test |
| --- | --- | --- | --- |
| Administrator | `demo_admin` | `LibraryAdmin2026!` | Book and catalog editing, inventory import/export/bulk actions, access requests, permission/role/group management |
| Employee | `demo_employee` | `LibraryStaff2026!` | Personal profile and reader workflows; no admin pages by default |
| Client | `demo_client` | `LibraryReader2026!` | Browse, borrow/rent/return, purchase, receipt, and review a purchased book |

Anonymous visitors may browse books and reviews, then register. Registration creates a `client` account. The seeded `demo_visitor` record is inactive and cannot log in.

## Role behavior

| Action | Anonymous | Client | Employee | Administrator |
| --- | --- | --- | --- | --- |
| Browse books and reviews | Yes | Yes | Yes | Yes |
| Register and sign in | Register | Yes | Yes | Yes |
| Edit own profile and upload picture | No | Yes | Yes | Yes |
| Borrow, rent, return, and purchase | No | Yes | Yes | Yes |
| Download own receipt | No | Yes | Yes | Yes |
| Review a purchased book | No | Yes | Yes | Yes |
| Edit or delete own review | No | Yes | Yes | Yes |
| Manage books, inventory, authors, publishers | No | No by default | No by default | Yes |
| Manage permissions, roles, groups, and users | No | No | No | Yes |

Employee and client start with the same member permission set. An administrator may add access roles or groups, so effective permissions can change. The access management page and API require the administrator account role. Review creation requires purchase. Only the review author can edit it; the author or an administrator can soft delete it.

## Walkthrough

1. Open the [client login page](https://github.com/HussainALAradi5/books_build_minds_client_side/blob/main/README.md), sign in with one account, and inspect **My profile**.
2. Use the [illustrated workflow](https://github.com/HussainALAradi5/books_build_minds_client_side/blob/main/docs/screenshots/workflows/README.md) to compare the actions visible to client, employee, and administrator.
3. Sign out before switching accounts. For purchases and reviews, choose a book that the account has not bought or reviewed yet.

The screenshots show a completed purchase, loan, and review for **Sapiens** in the local preview. A newly seeded database has the accounts and catalog; those transactions are created by following the walkthrough.
