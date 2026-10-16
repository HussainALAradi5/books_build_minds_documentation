# Books and purchases: services

Public catalog, administrative inventory, imports, exports, purchases, and soft deletion.

Service functions return Flask responses unless described as an internal helper. Endpoint-level authorization is enforced by `require_user` before a protected service runs. Business checks remain in the service.

| Service file | Signature | Business and code behavior | Errors and when they occur |
| --- | --- | --- | --- |
| `book_service.py` | `filtered_books(options, include_archived)` | Build public or managed book query with text, publisher, condition, price, and sort. | 400 invalid condition, price, or sort |
| `book_service.py` | `list_books()` | Return visible books with server pagination and sorting. | 400 invalid filter, page, or sort |
| `book_service.py` | `list_managed_books()` | Return nondeleted books including archived items for inventory management. | 400 invalid filter, page, or sort; 401/403 missing access |
| `book_service.py` | `export_books()` | Apply the public catalog filters, optionally intersect with 1–100 selected book IDs, and export up to 5,000 visible rows as PDF, XLSX, or CSV. Signed-in clients and administrators may export; import and bulk changes remain administrator-only. | 400 unsupported format/filter or invalid IDs; 401 missing session |
| `book_service.py` | `import_books()` | Validate tabular rows, resolve authors and publishers, and commit imported books. | 400 malformed file or row; duplicate title or ISBN; 401/403 missing access |
| `book_service.py` | `bulk_books()` | Archive, unarchive, or soft delete 1–100 selected books. | 400 invalid selection/action; 404 missing book; 409 active loan on delete |
| `book_service.py` | `get_book(slug)` | Return an active, visible book by slug. | 404 book missing, archived, or deleted |
| `book_service.py` | `create_book()` | Validate book input, resolve author and publisher, and insert a unique book. | 400 invalid fields; 409 duplicate title or ISBN; 401/403 missing access |
| `book_service.py` | `update_book(slug)` | Edit supported fields while preserving unique names and active-loan capacity. | 400 invalid fields; 404 missing book; 409 duplicate or insufficient copies |
| `book_service.py` | `delete_book(slug)` | Soft delete a book only when no copies are on active loan. | 404 missing book; 409 active loan; 401/403 missing access |
| `book_service.py` | `purchase_book(slug)` | Legacy unpaid purchase disabled; use verified transaction checkout. | 409 use checkout |

See [endpoint mapping](../endpoints/README.md) and [shared response rules](../../../architecture/api-contract.md).

See [book printing services, endpoints, contracts, and scanner setup](../printing/README.md).
