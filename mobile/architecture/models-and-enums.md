# Flutter models, contracts, and enums

Model this app from the **actual Flask JSON**, not from database tables or screenshots. The implemented tree keeps backend request shapes in `lib/dtos/{domain}/`, stable app records in `lib/models/{domain}/`, and enum values with labels/colors in `lib/enums/{domain}/`. API and service classes form the mapping boundary. One class or enum should have one named file when it has independent meaning; small private helpers may stay beside their owner.

## First model inventory

| Feature | Wire contracts to verify | App-facing models |
| --- | --- | --- |
| Auth/profile | Login/register/reset requests; login response; profile response; profile edit request | `Session`, `UserProfile`, `EffectiveAccess`, `ProfileUpdate` |
| Catalog | Book page/detail responses; book filter query; author/publisher/country responses | `BookSummary`, `BookDetail`, `BookFilter`, `Author`, `Publisher`, `Country` |
| Loans | Quote response; borrow request; loan response; loan filter | `LoanQuote`, `Loan`, `LoanFilter` |
| Checkout | Checkout request/response; transaction list/detail response | `CheckoutIntent`, `Transaction`, `TransactionFilter`, `ReceiptReference` |
| Reviews | Review response; create/edit request; review filter | `Review`, `ReviewDraft`, `ReviewFilter` |
| Media/printing | Media response; upload result; QR/barcode response | `MediaAsset`, `BookCodes` |
| Admin | Inventory filter; bulk/import response; access role/group/permission response | `InventorySelection`, `ImportResult`, `AccessRole`, `AccessGroup`, `AccessPermission` |

`BookFilter`, `LoanFilter`, `ReviewFilter`, and `TransactionFilter` extend the shared pagination/filter contract and add only their feature fields. Filtering, sorting, and pagination happen on the server. The controller can hold an immutable draft filter, but the API service serializes it once when applying.

Use a shared audit metadata contract for `createdBy`, `createdAt`, `updatedBy`, `updatedAt`, `isArchived`, and `isDeleted` only in responses that expose those fields. A Dart domain class can compose `AuditMetadata`; Flutter widgets should not inherit database persistence behavior.

## Enums and presentation maps

The values below are the current server wire values. Each enum gets its own file, with a separate label/color map for presentation. The Flutter palette maps to semantic theme colors in light and dark mode; do not copy Chakra CSS tokens into Dart. Badge text stays visible even when color is unavailable.

| Enum | Current values | Suggested semantic treatment |
| --- | --- | --- |
| `AccountRole` | `visitor`, `client`, `employee`, `admin` | Neutral, blue, teal, violet |
| `BookStatus` (condition) | `new`, `good`, `fair`, `damaged` | Green, blue, amber, red; same mapping in filter choices and badges |
| `MediaType` | `book_cover`, `book_gallery`, `profile_picture` | Type label; color is optional where type is not a status |
| `GenericStatus` (record lifecycle) | `active`, `archived`, `deleted` | Green, neutral, red |
| `LoanKind` | `borrow`, `rent` | Blue, violet |
| `LoanStatus` | `active`, `returned`, `overdue` | Blue, green, red |
| `TransactionKind` | `purchase`, `rental` | Violet, blue |
| `TransactionStatus` | `pending`, `completed`, `expired`, `failed`, `refund_pending`, `refunded` | Amber, green, neutral, red, amber, teal |
| `AdminDecision` | `pending`, `accepted`, `rejected` | Amber, green, red |
| `SortDirection` | `asc`, `desc` | Direction label, no status color needed |

`GenericStatus` describes archival/deletion state; it is **not** a substitute for borrow, return, purchase, or rental actions. Those are separate workflow types and server operations. Handle unknown enum values safely in read-only UI and log a redacted compatibility signal, rather than guessing a privileged action.

## Contract rules

- Prices shown to readers are BHD amounts; checkout uses integer `expectedAmountMinor` mapped to `expected_amount_minor`. Keep conversion and formatting in one tested money utility.
- Dates from the server are parsed once and displayed using the user's locale/time zone. Do not use formatted date strings as model identity or sorting keys.
- Use `id`/`slug` from the server as stable identity, not list position. Preserve audit flags so admin deleted-review views can label records correctly.
- Use `BookStatus` for condition and a separate available/total copy count for stock. A colored wear scale is a **category visualization**, not a measured damage percentage.
- Review has rating, comment, optional description, author information, and audit fields where supplied. Its edit/delete actions are determined by authenticated ownership and server permissions.
- Do not infer purchase eligibility, role grants, or transaction completion from a client model alone. Refresh the server resource and let the server enforce the action.

Before coding serializers, capture sanitized JSON fixtures from the running demo API and compare them with [API contract documentation](../../architecture/api-contract.md). Update this inventory whenever the backend contract changes.
