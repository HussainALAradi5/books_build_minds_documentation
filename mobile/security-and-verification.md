# Flutter security and verification plan

The server remains the authority for role, ownership, stock, price, terms, payment state, and soft deletion. Mobile visibility checks improve usability but cannot grant access. Follow the existing [security controls](../security/README.md) and [access expressions](../architecture/access-control.md).

## Mobile security decisions

| Area | Required behavior |
| --- | --- |
| Transport | HTTPS for production API, media, receipts, and socket connections. Development origins are environment-specific and must not be shipped as production defaults. |
| Session | Store the JWT only in OS-backed secure storage; keep it out of plain preferences, URLs, analytics, logs, screenshots, and crash reports. Clear token and protected in-memory data on logout or unauthorized response. The current server does not document refresh tokens. |
| Login/recovery | Password is required. Reset uses a server-issued code delivered to the saved email; never show the code in API responses or send it to an arbitrary mobile number. Avoid account enumeration in client messages. |
| Authorization | Fetch role/effective permissions from the server. Require administrator account role plus the appropriate permission for admin actions. Own profile/loan/transaction routes use the signed-in user ID; do not trust a user-controlled ID alone. |
| Files | Ask only for needed photo/file permissions. Preview selected files locally, then send multipart to the server. Validate type/count/size client-side for feedback, and treat server validation as final. Avoid leaving sensitive PDFs in broadly readable caches. |
| Payments | Test/sandbox hosted checkout only unless a later release explicitly approves live mode. Do not handle card numbers. Only a verified server transaction may unlock purchase, rental, or receipt UI. Preserve request idempotency. |
| Realtime | Subscribe only to public book/review events needed for an open screen. Re-fetch authoritative state over REST. Do not expose personal loan, transaction, or permission data in public socket events. |
| Logging | Redact authentication, recovery, payment, personal profile, and upload data. Show safe error messages while retaining a non-sensitive request correlation ID if the server provides one. |

## Verification matrix before release

| Scope | Checks |
| --- | --- |
| Contracts | Golden JSON tests for snake_case mapping, enum values, page envelope, BHD minor units, error responses, and missing optional fields. Compare with running server payloads. |
| Controllers | Search debounce and stale request cancellation; pagination/filter reset; one in-flight submission; tab state retention; targeted refresh after borrow, return, review, price edit, and transaction sync. |
| Widgets | Small phone, large phone, tablet, portrait/landscape, large text, dark/light mode, keyboard/focus, screen reader labels, reduced motion, image zoom, spinner/disabled button, destructive confirmation. |
| Access | Visitor can browse but cannot invoke protected actions. Client/employee cannot open admin functions. Delegated grants cannot bypass administrator role checks. Review edit is author-only; delete is author or admin. Own-resource IDs cannot expose other users. |
| Workflows | Borrow terms/stale quote/no copies/double tap; return cancellation; purchase/rental pending, failed, and completed sandbox status; receipt unavailable before completion; offline recovery after a request whose result is unknown. |
| Server integration | Use a disposable migrated database with server-seeded demo accounts. Run real API checks for login, catalog, loan, review, media, admin 403s, and transaction state. Never rely on local fake records for acceptance. |

## Implementation order

1. Confirm Flutter/toolchain versions, supported Android/iOS levels, a reachable development Flask URL, and the exact live JSON payloads. Create the separate mobile repository and pin dependencies.
2. Build `ApiClient`, secure session storage, JSON mappers, common page/filter/error contracts, theme tokens, and public/protected routing.
3. Implement visitor catalog, book detail, creator dialogs, auth, and profile read views using server data.
4. Add shared widgets and member workflows: borrow/return, review, profile media, owned books, activity, and receipts.
5. Add transaction **sandbox** flow with reconciliation, then administrator screens and file import/export.
6. Complete the verification matrix, accessibility review, responsive screenshots, and documentation updates. Only then consider a mobile release decision.

This document is a plan. It does not mean the mobile application or a live payment integration has been built or tested.
