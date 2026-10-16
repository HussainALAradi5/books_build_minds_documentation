# Books and purchases: endpoints

All paths are relative to the Flask origin. The controller registers each route and applies access checks; the named service executes the operation. `401` means missing/invalid authentication on a protected route, and `403` means insufficient permission.

| Method | Endpoint | Access rule | Service function | What happens | Exceptions / error responses |
| --- | --- | --- | --- | --- | --- |
| GET | `/api/` | `Public` | `book_service.list_books` | Return visible books with server pagination and sorting. | 400 invalid filter, page, or sort |
| POST | `/api/book` | `Admin role AND Permission.BOOK_WRITE_ONE` | `book_service.create_book` | Validate book input, resolve author and publisher, and insert a unique book. | 400 invalid fields; 409 duplicate title or ISBN; 401/403 missing access |
| GET | `/api/book/export` | Signed-in user | `book_service.export_books` | Export visible, nondeleted, nonarchived books as PDF, XLSX, or CSV. Catalog filters apply; optional comma-separated `ids` restricts output to 1–100 selected books. Up to 5,000 rows are returned. | 400 unsupported format/filter or invalid IDs; 401 missing session |
| GET | `/api/book/manage` | `Permission.BOOK_READ_ANY` | `book_service.list_managed_books` | Return nondeleted books including archived items for inventory management. | 400 invalid filter, page, or sort; 401/403 missing access |
| POST | `/api/book/import` | `Admin role AND Permission.BOOK_IMPORT` | `book_service.import_books` | Validate tabular rows, resolve authors and publishers, and commit imported books. | 400 malformed file or row; duplicate title or ISBN; 401/403 missing access |
| POST | `/api/book/bulk` | `Admin role AND Permission.BOOK_BULK_EDIT` | `book_service.bulk_books` | Archive, unarchive, or soft delete 1–100 selected books. | 400 invalid selection/action; 404 missing book; 409 active loan on delete |
| GET | `/api/book/<string:slug>` | `Public` | `book_service.get_book` | Return an active, visible book by slug. | 404 book missing, archived, or deleted |
| PUT | `/api/book/<string:slug>` | `Admin role AND Permission.BOOK_EDIT_ANY` | `book_service.update_book` | Edit supported fields while preserving unique names and active-loan capacity. | 400 invalid fields; 404 missing book; 409 duplicate or insufficient copies |
| DELETE | `/api/book/<string:slug>` | `Admin role AND Permission.BOOK_DELETE_ANY` | `book_service.delete_book` | Soft delete a book only when no copies are on active loan. | 404 missing book; 409 active loan; 401/403 missing access |
| POST | `/api/book/<slug>/purchase` | `Permission.PURCHASE_WRITE_ONE` | `book_service.purchase_book` | Legacy unpaid purchase disabled; use `/api/transactions/checkout`. | 409 use checkout |

See [service signatures and rules](../services/README.md).

See [book printing services, endpoints, contracts, and scanner setup](../printing/README.md).
