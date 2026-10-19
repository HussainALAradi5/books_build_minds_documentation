# Flutter screens and library workflows

This screen map follows the existing server behavior. Visibility is a convenience; every protected operation is authorized again by Flask. A phone uses cards and compact actions, while a tablet may show more columns. The same controllers and services power both layouts.

| Route or area | Visitor | Signed-in member | Administrator |
| --- | --- | --- | --- |
| Home and catalog | Browse, search, filter, sort, paginate | Same, plus own context | Same, plus management entry points |
| Book detail | Description, creators, condition, stock, public reviews, print codes | Borrow, return, sandbox purchase/rental, own review where eligible | Same, plus price-only edit and authorized media/catalog actions |
| Auth | Sign in, register, password recovery | Sign out | Sign out |
| Profile tabs | Sign-in prompt | Account, photo, purchased books, loans, transactions, activity | Own profile plus admin navigation |
| Admin inventory/catalog | Hidden | Hidden | Import/export, bulk selection, create, price edit, soft delete, with confirmation |
| Admin access | Hidden | Hidden | Permissions, roles, groups, users, and access requests |

New accounts default to the client role. The seeded employee currently has member permissions; custom roles/groups may add supported grants, but book writes and access management still require the administrator **account role**. Admin screens should derive visible actions from both role and effective permissions returned by the server. See [access rules](../../architecture/access-control.md).

## Public catalog and book detail

1. Fetch `/api/` with `BookFilter` extending the shared pagination/filter contract. Search, publisher, condition, BHD range, sort, and page are sent to Flask. A searchable publisher picker loads `/api/publishers` and sends the selected value; no local text comparison of an incomplete book page.
2. Show grid or compact list, condition badge and labeled wear scale, availability bar, and a short error/empty state. On a phone, book facts and actions appear before a tall cover.
3. Open `/api/book/{slug}` for full description and facts. Author and publisher buttons load their read-only records into small dialogs. Reviews are a separate section below the book information; user images appear with reviews where supplied.
4. Offer server-generated book PDF and QR/barcode content. A scan can feed the catalog search field; parse/lookup rules stay in the server contract.

## Borrow and return

1. Fetch `/api/book/{slug}/quote` and show current availability, free borrow duration, policy version, and terms.
2. `GenericWorkflowSteps` advances through terms, quote review, and confirmation. Acceptance is explicit. A local validation error keeps the user on the relevant step.
3. Submit `POST /api/book/{slug}/borrow` once with accepted terms and policy version. A `409` refreshes quote/stock and asks the user to review changed terms. Success refreshes only affected book/profile regions.
4. Show the active loan in the profile. Return opens a red-accented confirmation dialog; only confirm sends `POST /api/book/{slug}/return`. Refetch loan, stock, and activity after server success.

## Sandbox purchase, rental, transaction, and receipt

1. Fetch a fresh quote. Display BHD amount, inventory, rental duration where relevant, and terms. The app does not determine the final amount or create a loan locally.
2. With explicit consent, send `POST /api/transactions/checkout` using a stable UUID request key, `book_slug`, `kind`, accepted terms, current policy version, and `expected_amount_minor` from the quote. Do not create a new key when retrying the same submission.
3. Open the server-provided hosted **sandbox** URL in the platform browser. On return, read `GET /api/transactions/{id}` or call its authenticated sync endpoint. Display pending, completed, expired, failed, refund pending, and refunded states from the server only.
4. Offer the transaction receipt only after a verified completed state. A cancelled browser view or a redirect is not proof of payment. A pending state stays pending until verified reconciliation. Paid rental starts only after fulfillment.

There is no separate `sell` endpoint or seller listing contract in the documented API. Do not show a seller workflow in Flutter until the server defines ownership, inventory transfer, pricing, authorization, and transaction rules for it. The current purchase path is a library sale **to** a reader.

## Reviews and profile

- A signed-in purchaser may create one active review with rating 1–5, a comment of at least 10 characters, and optional description. The server decides purchase eligibility. Inline editing uses the same card/form area; only the review author can edit. The author or administrator can confirm a soft delete. Deleted reviews remain hidden publicly; the admin-only toggle requests `include_deleted=true`.
- Profile tabs show account, photo, owned books, loans, transactions, and activity. Each tab has its own controller and server page/filter state. Changing tabs keeps draft and filter state. Refresh only the affected tab after a mutation.
- Profile image selection supports preview, pinch zoom, and upload progress; only the server-validated result becomes the profile picture.

## Administrator workflows

- Inventory: server-paged list/grid with filters; export PDF/XLSX/CSV, import a supported table, and bulk archive/unarchive/soft delete within server limits. Show row-level import errors and require confirmation for destructive bulk actions.
- Catalog: create books and separate author/publisher records with selected countries. After creation, an existing book's **price only** is editable. Existing author and publisher profiles remain read only.
- Access: separate tabs for permissions, roles, groups, and user assignments. The UI may explain `AND`/`OR` permission composition, but the server evaluates it. Confirm access changes and refetch effective grants.

For every screen, document loading, empty, validation, offline, denied, conflict, and success states before implementation. Use the linked [service and endpoint reference](../../README.md#features) to keep behavior aligned.
