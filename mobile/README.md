# Flutter mobile application — implementation plan

This is the mobile implementation guide for the [Flutter client](https://github.com/HussainALAradi5/books_build_minds_mobile). The existing [Flask server](../architecture/README.md) remains the source of truth for catalog data, prices, stock, roles, permissions, loans, transactions, receipts, reviews, and media. The Next.js client is a design and behavior reference, not a mobile API gateway. Sections describing uploads, full administration, and release checks are future scope unless the mobile README says they are implemented.

## Goals and boundaries

- Build one responsive Flutter application for Android and iOS. Phone layouts come first; wider layouts adapt without copying desktop tables into narrow screens.
- Let anonymous visitors browse books and reviews, register, sign in, and recover access. Client and employee accounts get the same initial member actions. Administrator features appear only when the server reports the administrator account role and the required permission.
- Reuse typed models, contracts, services, controllers, validators, and widgets across features. A screen composes widgets; its controller owns state and user actions; a repository coordinates API calls; Flask owns business decisions.
- Use the current `/api` contract and server-side search, filters, sorting, pagination, and workflow validation. No mobile-only demo catalog or client-calculated stock, ownership, or payment result.
- Keep paid purchase and rental flows in **test/sandbox mode** unless a separate release decision approves live payments. Free borrowing requires no payment. The mobile app never collects or stores card details.

## Reader journey

1. Browse or search the catalog and switch between card and compact list views. Open a book for description, price, condition, availability, author and publisher information, and reviews.
2. Sign in using email, username, or an optional international mobile number. A new account receives the client role. Password reset sends a code to the saved email; mobile number is an identifier, not an SMS destination.
3. Review the server quote and terms before borrowing. Confirm through the server, then see the loan in the profile and return it through a confirmation dialog.
4. Where sandbox checkout is configured, confirm a purchase or rental quote, open the provider's hosted checkout, then refresh the **server transaction** until its verified state is known. A browser or deep-link return alone does not grant a book or create a receipt.
5. A purchaser can leave one active rating and comment review. The author can edit their review; the author or administrator can soft delete it. Administrators may reveal deleted reviews.
6. Profile tabs show account, photo, owned books, loans, transactions, and activity. Receipts and book print PDFs come from the server.

## Read next

| Document | Purpose |
| --- | --- |
| [Project structure](architecture/project-structure.md) | Parent layer folders, domain subfolders, DTOs, interfaces, middleware, models, and enums |
| [Models and enums](architecture/models-and-enums.md) | Typed entity inventory, lifecycle fields, values, labels, and semantic colors |
| [API and data contracts](architecture/api-contracts.md) | Base URL, authentication, DTO mapping, service boundaries, errors, and endpoint map |
| [Reusable widgets and state](architecture/widgets-and-state.md) | Generic cards, filters, lists, forms, dialogs, upload, progress, tabs, and controllers |
| [Features and workflows](features/README.md) | Screen map, role visibility, and step-by-step library actions |
| [Security and verification](security-and-verification.md) | Mobile token handling, protected actions, accessibility, responsive checks, and release gates |

Use the [existing endpoint documentation](../README.md#features) for route-specific business rules and errors. Confirm mobile journeys against a running Flask server before treating them as release ready.
