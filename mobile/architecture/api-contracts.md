# Mobile API and data contracts

Flutter calls the **Flask origin directly**, not Next.js server functions. The configured base origin plus `/api` is the only REST base URL. Development environments need a reachable host address for a device or emulator; `localhost` on a physical phone points to the phone itself. Production must use HTTPS.

## Shared client contract

`ApiClient` provides typed `get`, `post`, `put`, `patch`, `delete`, multipart upload, and file download operations. It adds the bearer token only to protected requests, applies timeouts, converts JSON and error envelopes, and returns typed failures. A caller can cancel stale search requests. Never log passwords, reset codes, authorization headers, payment URLs, or personal response bodies.

| Shared type | Fields and behavior |
| --- | --- |
| `PaginationQuery` | `page`, `perPage` (positive, endpoint limit respected) |
| `FilterQuery extends PaginationQuery` | Optional `query`, `sortBy`, `sortDirection`; feature filters extend this contract |
| `SortDirection` | `asc`, `desc`, with one mapper to `sort_direction` |
| `Page<T>` | `items`, `pagination` mapped from the server page envelope |
| `ApiFailure` | Status, safe message, optional field errors; distinguish offline/timeout from HTTP errors |
| `Session` | Server `user_id` and JWT plus profile/effective permissions loaded from the server |

The JSON mapper converts only at the edge: `perPage ↔ per_page`, `sortDirection ↔ sort_direction`, `requestKey ↔ request_key`, `createdAt ↔ created_at`, and so on. Use explicit mappings or generated serializers with golden payload tests; Dart reflection cannot turn an interface into runtime field names. Preserve money in **integer minor units** for checkout and display BHD to three decimal places; never use a floating-point amount for a payment request.

Flask returns `{"error":"..."}` on failures. Treat `400` as validation, `401` as expired/missing login, `403` as denied role/ownership, `404` as missing or hidden data, `409` as a changed state requiring refresh, `429` as rate limiting, and `502/503` as upstream or configuration failure. Retry only safe reads or an explicitly idempotent operation. Keep the checkout `requestKey` stable for a retry.

## Service-to-endpoint map

Paths below are appended to `/api`. Their detailed input, permissions, business rules, and exceptions remain in the linked server endpoint references.

| Mobile service | Server endpoints | Notes |
| --- | --- | --- |
| `AuthService` | `POST /login`, `/register`, `/password-reset/request`, `/password-reset/confirm` | Login accepts email, username, or optional mobile identifier plus password; reset code goes to saved email. [Accounts](../../features/accounts/endpoints/README.md) |
| `ProfileService` | `GET /profile/{userId}`, `PUT /profile/{userId}/edit`, `GET /profile/{userId}/books`, `/activities` | Own resources only; use server pagination. [Accounts](../../features/accounts/endpoints/README.md) |
| `BookService` | `GET /`, `/book/{slug}`, `/book/export`, `/book/manage`; admin `POST /book`, `/book/import`, `/book/bulk`, `PATCH /book/{slug}/price`, `DELETE /book/{slug}` | Catalog filters/sort/page run on server. Existing books change price only. [Books](../../features/books/endpoints/README.md) |
| `CatalogReferenceService` | `GET /authors`, `/authors/{id}`, `/publishers`, `/publishers/{id}`, `/countries`; admin create/sync routes | Existing author and publisher profiles are read only. [Catalog](../../features/catalog/endpoints/README.md) |
| `LoanService` | `GET /book/{slug}/quote`, `/book/{slug}/loan`, `POST /book/{slug}/borrow`, `/book/{slug}/return`, `GET /profile/{userId}/loans` | Terms and quote must be current; the legacy unpaid `/rent` route rejects. [Loans](../../features/loans/endpoints/README.md) |
| `TransactionService` | `POST /transactions/checkout`, `GET /transactions`, `/transactions/{id}`, `POST /transactions/{id}/sync`, `/cancel` | Hosted **sandbox** checkout; trust verified server status, not redirect data. [Payments](../../features/payments/endpoints/README.md) |
| `ReviewService` | `GET /book/{slug}/review`, `/mine`, `POST /book/{slug}/review`, `PUT`/`DELETE /book/{slug}/review/{id}` | Purchase-gated write; author edit; author/admin soft delete. [Reviews](../../features/reviews/endpoints/README.md) |
| `MediaService` | `GET /media/books/{bookId}`, `POST /media/books/{bookId}`, `/media/profile-picture` | Multipart uploads; server validates media and stores metadata. [Media](../../features/media/endpoints/README.md) |
| `ReceiptService` | `GET /transactions/{id}/receipt`, `/book/{slug}/receipt` | Authenticated PDF stream, displayed or shared with platform controls. [Receipts](../../features/receipts/endpoints/README.md), [payments](../../features/payments/endpoints/README.md) |
| `AccessService` | Permission, role, group, and assignment routes | Administrator account role **and** matching permission. [Access](../../features/access/endpoints/README.md) |

Book PDF, QR, and barcode routes are listed in [printing](../../features/books/printing/README.md). The server creates these assets; the Flutter app renders or downloads them. Book/review Socket.IO events may refresh an open book view, but the REST response remains authoritative. Confirm event names and authentication policy before wiring the socket client.

## Authentication boundary

The web client's HttpOnly cookie belongs to Next.js and is not reused in Flutter. Mobile `POST /login` receives a JWT; store it in OS-backed secure storage and keep it out of logs and plain preferences. Load `/profile/{userId}` after sign-in or app resume to refresh role and effective permissions. Clear local session data on logout or `401`; password reset invalidates earlier JWTs server-side. The current API has no documented refresh-token endpoint, so do not design silent renewal around one. A public screen must still work when no session exists.
