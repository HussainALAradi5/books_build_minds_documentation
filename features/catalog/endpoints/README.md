# Authors, publishers, and countries: endpoints

All paths are relative to the Flask origin. The controller registers each route and applies access checks; the named service executes the operation. `401` means missing/invalid authentication on a protected route, and `403` means insufficient permission.

| Method | Endpoint | Access rule | Service function | What happens | Exceptions / error responses |
| --- | --- | --- | --- | --- | --- |
| GET | `/api/countries` | `Public` | `country_service.list_countries` | Page countries stored in the local database. | 400 invalid page or sort |
| POST | `/api/countries/sync` | `Permission.COUNTRY_SYNC` | `country_service.sync_countries` | Fetch countries from the provider and upsert local records. | 400/502 provider or payload failure; 401/403 missing access |
| GET | `/api/authors` | `Public` | `author_service.list_authors` | Page active authors with server search and sort. | 400 invalid page or sort |
| POST | `/api/authors` | `Permission.AUTHOR_WRITE` | `author_service.save_author` | Create an author with description, optional address, and country. Works are linked when books are created. | 400 invalid data or country; 409 duplicate; 401/403 missing access |
| GET | `/api/authors/<int:author_id>` | `Public` | `author_service.get_author` | Return an author and associated works. | 404 author missing |
| GET | `/api/publishers` | `Public` | `publisher_service.list_publishers` | Page active publishers with server search and sort. | 400 invalid page or sort |
| POST | `/api/publishers` | `Permission.PUBLISHER_WRITE` | `publisher_service.save_publisher` | Create a publisher with description, optional address, and country. | 400 invalid data or country; 409 duplicate; 401/403 missing access |
| GET | `/api/publishers/<int:publisher_id>` | `Public` | `publisher_service.get_publisher` | Return a publisher and associated works. | 404 publisher missing |
| GET | `/api/categories` | `Public` | `category_service.list_categories` | Page active categories; `q` searches names. | 400 invalid page or sort |
| POST | `/api/categories` | `Admin role AND Permission.BOOK_WRITE_ONE` | `category_service.save_category` | Create a unique category. | 400 invalid name; 409 duplicate; 401/403 missing access |
| GET | `/api/categories/top` | `Public` | `category_service.top_categories` | Page categories and return up to five books per category; `sort=most_rated` or `best_selling`. | 400 invalid page or sort |

Existing author and publisher profiles are read only. `PUT` to either individual route returns `405 Method Not Allowed`.

See [service signatures and rules](../services/README.md).
