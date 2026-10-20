# Flutter project structure

The mobile repository is [books_build_minds_mobile](https://github.com/HussainALAradi5/books_build_minds_mobile). The `lib/` tree uses **one parent folder per concern**. Each parent has domain subfolders, so a book widget and a review widget remain easy to find without a nested `features/` wrapper.

```text
lib/
├─ apis/          admin, auth, book_detail, catalog, checkout, loans, profile, reviews
├─ app/           app scope, shell, theme, tab navigation
├─ controllers/   auth, book_detail, catalog state and commands
├─ dtos/          backend request bodies by domain
├─ enums/         auth, catalog, checkout, loans; labels and semantic colors
├─ filters/       server query models
├─ interfaces/    network transport and request middleware contracts
├─ middleware/    authenticated request header policy
├─ models/        typed app records and shared page result
├─ screens/       domain screens and sheets
├─ services/      typed operations and secure session storage
├─ shared/        API client, routes, failures, configuration
├─ utils/         money/date formatting, pagination query, request keys, book actions
└─ widgets/       admin, auth, book_detail, catalog, checkout, loans,
                 profile, reviews, shared
test/            contract and widget checks
```

Each layer has a distinct responsibility:

- `apis/{domain}` calls Flask endpoints through `ApiTransport` and serializes request DTOs.
- `services/{domain}` maps responses into app models and coordinates secure local state where needed.
- `dtos/{domain}` mirrors specific backend request shapes. `models/{domain}` represents typed data used by the app. Flutter properties use `lowerCamelCase`; serialization converts to Flask `snake_case` at the boundary.
- `interfaces/network` lets an API use another transport in a test. `middleware/network` rejects protected calls without a token and adds the bearer header. The server still authorizes every action.
- `controllers/{domain}` owns page/filter/loading/workflow state. `screens/{domain}` and `widgets/{domain}` render it. `widgets/shared` contains controls reused by multiple domains.
- `enums/{domain}` keeps each enum in its own file with labels and semantic colors. `utils/shared` contains pure formatting and pagination helpers.

Use one public class or enum per named file when it has independent meaning. Avoid catch-all `index.dart` exports. Keep screens and widgets below 250 lines by extracting coherent pieces. Do not add model fields absent from the API response.

The current mobile app implements core reader flows and a compact administrator view. The web client remains the full management surface for uploads, bulk import/export, and role/group editing. See the [mobile README](https://github.com/HussainALAradi5/books_build_minds_mobile/blob/main/README.md) for setup and current limits.
