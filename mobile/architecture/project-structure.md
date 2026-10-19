# Flutter project structure

Create a **separate Flutter repository** when implementation starts. The proposed `lib/` tree is feature first; shared infrastructure is kept in `core/`, and reusable UI in `shared/`.

```text
lib/
├─ app/
│  ├─ app.dart                     Application widget and route composition
│  ├─ router/                      Public and protected route definitions
│  └─ theme/                       Light/dark tokens, typography, spacing, enum colors
├─ core/
│  ├─ config/                      Environment-specific API and socket origins
│  ├─ constants/                   Limits, route names, workflow policy constants
│  ├─ enums/                       One enum per file, with separate label/color maps
│  ├─ error/                       Typed API, network, and validation failures
│  ├─ interfaces/                  Abstract contracts for transport, storage, clock
│  ├─ models/                      Shared value models: page, pagination, session
│  ├─ network/                     One API client, auth interceptor, mapper, file transfer
│  ├─ security/                    Secure token storage and session lifecycle
│  └─ utils/                       Pure money, date, query, and validation helpers
├─ features/
│  ├─ auth/                         login, register, recovery
│  ├─ catalog/                      books, filters, authors, publishers, countries
│  ├─ book_detail/                  facts, availability, printing, creator dialogs
│  ├─ loans/                        quote, borrow, return, loan history
│  ├─ checkout/                     sandbox purchase/rental, transaction status
│  ├─ reviews/                      list, create, inline edit, soft delete
│  ├─ profile/                      account, photo, owned books, activity
│  ├─ receipts/                     authorized PDF download/open
│  └─ admin/                        inventory, catalog, import/export, access
│     └─ access/                    permissions, roles, groups, users
└─ shared/
   ├─ interfaces/                  Widget configuration contracts
   ├─ widgets/                     GenericCard, GenericFilter, GenericList, etc.
   └─ utils/                       UI-only formatting and accessibility helpers
test/                             Pure mapper, controller, and widget tests
integration_test/                 Server-backed journeys with disposable data
```

Within each feature, use `data/contracts/` for wire requests/responses, `data/mappers/` for JSON conversion, `data/repositories/` for API implementations, `domain/interfaces/` for repository contracts, `domain/models/` and `domain/enums/` for app concepts, and `presentation/controllers/`, `screens/`, and `widgets/` for state and rendering.

## Naming and ownership

- Use Dart `snake_case.dart` filenames and `UpperCamelCase` class names. Dart properties stay `lowerCamelCase` (`requestId`, `createdAt`), while Flask's JSON stays `snake_case` at the transport boundary.
- One public enum or interface/abstract contract per named file. Keep labels and color mappings alongside the related enum in dedicated files. Avoid catch-all `index.dart` exports and large mixed-type files.
- Call a class `Dto` only when it mirrors a specific backend request or response. Domain/UI models are not DTOs. JSON conversion belongs in the mapper, not in widgets.
- Represent shared audit fields (`createdBy`, `createdAt`, `updatedBy`, `updatedAt`, `isArchived`, `isDeleted`) with a reusable base contract or composed metadata value **where the response actually exposes them**. Do not add fields to API models that the server does not return.
- Controllers own loading, validation state, debouncing, pagination, selection, and commands. Widgets render the supplied state and report user actions. Repositories call typed API services; widgets never construct URLs or parse raw JSON.
- Keep each screen/widget focused and small; split long layouts into named feature widgets. Prefer immutable models and a single source of state for each workflow.

## Dependency direction

`screen/widgets → controller → domain repository interface → data repository → ApiClient → Flask`.

The shared layer may be used by features; it must not import a specific feature. A feature may use another feature's public domain contract only when there is a real business dependency. For example, review eligibility comes from the server and purchase/profile state, not from a client-side guess.

The selected state-management and routing packages will be pinned during implementation after compatibility and accessibility checks. This plan defines responsibilities without locking an unverified package version.

See [models and enums](models-and-enums.md) for the first typed contract inventory.
