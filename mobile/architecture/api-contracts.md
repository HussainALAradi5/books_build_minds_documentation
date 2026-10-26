# Mobile API and data contracts

Flutter calls the **Flask origin directly**, not Next.js server functions. The configured base origin plus `/api` is the only REST base URL. Development environments need a reachable host address for a device or emulator; `localhost` on a physical phone points to the phone itself. Production must use HTTPS.

## Shared client contract

The implemented `ApiClient` provides JSON `get`, `post`, `put`, `patch`, and `delete`, multipart `upload`, and authenticated binary `download` through `ApiTransport`. `AuthHeaderMiddleware` adds the bearer token only to protected requests and rejects a missing token before opening a connection. The client applies timeouts and maps network/HTTP errors to `ApiFailure`. The catalog controller ignores stale search results; the transport does not yet cancel in-flight requests. File picker selection is checked locally; Flask is the final authority for media type, size, and permissions. Never log passwords, reset codes, authorization headers, payment URLs, or personal response bodies.

| Shared type | Fields and behavior |
| --- | --- |
| `PaginationFilter` | `page`, `perPage`, `sortDirection`; maps to validated server query keys |
| `CatalogSearchFilter`, `BookFilter`, `LoanFilter`, `ReviewFilter` | Extend `PaginationFilter` and add only their endpoint-specific options; catalog profile/category pickers request pages from the server |
| `SortDirection` | `asc`, `desc`, with one mapper to `sort_direction` |
| `PageResult<T>` | `items`, `pagination` mapped from the server page envelope |
| `ApiFailure` | Status and safe message; distinguishes network/timeout failures from HTTP errors |
| `LoginResult` and `UserProfile` | Server `user_id` and JWT plus profile/effective permissions loaded from the server |

The JSON mapper converts only at the edge: `perPage ↔ per_page`, `sortDirection ↔ sort_direction`, `requestKey ↔ request_key`, `createdAt ↔ created_at`, and so on. Use explicit mappings or generated serializers with golden payload tests; Dart reflection cannot turn an interface into runtime field names. Preserve money in **integer minor units** for checkout and display BHD to three decimal places; never use a floating-point amount for a payment request.

Flask returns `{"error":"..."}` on failures. Treat `400` as validation, `401` as expired/missing login, `403` as denied role/ownership, `404` as missing or hidden data, `409` as a changed state requiring refresh, `429` as rate limiting, and `502/503` as upstream or configuration failure. Retry only safe reads or an explicitly idempotent operation. Keep the checkout `requestKey` stable for a retry.

## Service-to-endpoint map

Paths below are appended to `/api`. Flutter now has typed services for media uploads, receipts, book codes, admin catalog/import/export/bulk operations, access writes, and transaction detail in addition to the reader workflows. The transaction cancel endpoint has a client method, but the server returns `409` while hosted checkout is active; the mobile UI does not advertise an instant cancellation. Detailed inputs, permissions, rules, and exceptions remain in the linked server endpoint references.

| Mobile service | Server endpoints | Notes |
| --- | --- | --- |
| `AuthService` | `POST /login`, `/register`, `/password-reset/request`, `/password-reset/confirm` | Login accepts email, username, or registered mobile number plus password. Registration can include a normalized international `mobile_number`; reset code goes to saved email. [Accounts](../../features/accounts/endpoints/README.md) |
| `ProfileService` | `GET /profile/{userId}`, `PUT /profile/{userId}/edit`, `GET /profile/{userId}/books`, `/activities` | Own resources only; use server pagination. [Accounts](../../features/accounts/endpoints/README.md) |
| `BookService` | `GET /`, `/book/{slug}`, `/book/export`, `/book/manage`; admin `POST /book`, `/book/import`, `/book/bulk`, `PATCH /book/{slug}/price`, `DELETE /book/{slug}` | Catalog filters/sort/page run on server, including category slug, most-rated, and best-selling choices. Creation selects existing author, publisher, and up to five categories. Existing books change price only. [Books](../../features/books/endpoints/README.md) |
| `CatalogReferenceService` | `GET /authors`, `/authors/{id}`, `/publishers`, `/publishers/{id}`, `/categories`, `/categories/top`, `/countries`; admin create/sync routes | Search and page profile and category choices; creator address is optional. Category showcase returns up to five books per category. Existing author and publisher profiles are read only. [Catalog](../../features/catalog/endpoints/README.md) |
| `LoanService` | `GET /book/{slug}/quote`, `/book/{slug}/loan`, `POST /book/{slug}/borrow`, `/book/{slug}/return`, `GET /profile/{userId}/loans` | Terms and quote must be current; the legacy unpaid `/rent` route rejects. [Loans](../../features/loans/endpoints/README.md) |
| `TransactionService` | `POST /transactions/checkout`, `GET /transactions`, `/transactions/{id}`, `POST /transactions/{id}/sync`, `/cancel` | Hosted **sandbox** checkout; trust verified server status, not redirect data. [Payments](../../features/payments/endpoints/README.md) |
| `ReviewService` | `GET /book/{slug}/review`, `/mine`, `POST /book/{slug}/review`, `PUT`/`DELETE /book/{slug}/review/{id}` | Purchase-gated write; author edit; author/admin soft delete. [Reviews](../../features/reviews/endpoints/README.md) |
| `MediaService` | `GET /media/books/{bookId}`, `POST /media/books/{bookId}`, `/media/profile-picture` | Multipart uploads; server validates media and stores metadata. [Media](../../features/media/endpoints/README.md) |
| `ReceiptService` | `GET /transactions/{id}/receipt`, `/book/{slug}/receipt` | Authenticated PDF stream, displayed or shared with platform controls. [Receipts](../../features/receipts/endpoints/README.md), [payments](../../features/payments/endpoints/README.md) |
| `AccessService` | Permission, role, group, and assignment routes | Administrator account role **and** matching permission. [Access](../../features/access/endpoints/README.md) |

Book PDF, QR, and barcode routes are listed in [printing](../../features/books/printing/README.md). The server creates these assets; Flutter displays QR/barcode SVGs and opens the book PDF URL. The book detail screen joins a public Socket.IO room and reloads server data after `book_changed`. REST responses remain authoritative.

## Authentication boundary

The web client's HttpOnly cookie belongs to Next.js and is not reused in Flutter. Mobile `POST /login` receives a JWT; store it in OS-backed secure storage and keep it out of logs and plain preferences. Load `/profile/{userId}` after sign-in or app resume to refresh role and effective permissions. Clear local session data on logout or `401`; password reset invalidates earlier JWTs server-side. The current API has no documented refresh-token endpoint, so do not design silent renewal around one. A public screen must still work when no session exists.
