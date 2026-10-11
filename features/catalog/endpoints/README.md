# Authors, publishers, and countries: endpoints

All paths are relative to the Flask origin. The controller registers each route and applies access checks; the named service executes the operation. `401` means missing/invalid authentication on a protected route, and `403` means insufficient permission.

| Method | Endpoint | Access rule | Service function | What happens | Exceptions / error responses |
| --- | --- | --- | --- | --- | --- |
| GET | `/api/countries` | `Public` | `country_service.list_countries` | Page countries stored in the local database. | 400 invalid page or sort |
| POST | `/api/countries/sync` | `Permission.COUNTRY_SYNC` | `country_service.sync_countries` | Fetch countries from the provider and upsert local records. | 400/502 provider or payload failure; 401/403 missing access |
| GET | `/api/authors` | `Public` | `author_service.list_authors` | Page active authors with server search and sort. | 400 invalid page or sort |
| POST | `/api/authors` | `Permission.AUTHOR_WRITE` | `author_service.save_author` | Create or edit an author, description, country, and work links. | 400 invalid data; 404 missing author/country; 409 duplicate; 401/403 missing access |
| GET | `/api/authors/<int:author_id>` | `Public` | `author_service.get_author` | Return an author and associated works. | 404 author missing |
| PUT | `/api/authors/<int:author_id>` | `Permission.AUTHOR_WRITE` | `author_service.save_author` | Create or edit an author, description, country, and work links. | 400 invalid data; 404 missing author/country; 409 duplicate; 401/403 missing access |
| GET | `/api/publishers` | `Public` | `publisher_service.list_publishers` | Page active publishers with server search and sort. | 400 invalid page or sort |
| POST | `/api/publishers` | `Permission.PUBLISHER_WRITE` | `publisher_service.save_publisher` | Create or edit publisher details and country association. | 400 invalid data; 404 missing publisher/country; 409 duplicate; 401/403 missing access |
| GET | `/api/publishers/<int:publisher_id>` | `Public` | `publisher_service.get_publisher` | Return a publisher and associated works. | 404 publisher missing |
| PUT | `/api/publishers/<int:publisher_id>` | `Permission.PUBLISHER_WRITE` | `publisher_service.save_publisher` | Create or edit publisher details and country association. | 400 invalid data; 404 missing publisher/country; 409 duplicate; 401/403 missing access |

See [service signatures and rules](../services/README.md).
