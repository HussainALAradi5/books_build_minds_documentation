# Authors, publishers, and countries: services

Author and publisher records, their countries and works, and a local country catalog.

Service functions return Flask responses unless described as an internal helper. Endpoint-level authorization is enforced by `require_user` before a protected service runs. Business checks remain in the service.

| Service file | Signature | Business and code behavior | Errors and when they occur |
| --- | --- | --- | --- |
| `author_service.py` | `list_authors()` | Page active authors with server search and sort. | 400 invalid page or sort |
| `author_service.py` | `get_author(author_id)` | Return an author and associated works. | 404 author missing |
| `author_service.py` | `save_author()` | Create an author with description and country. The profile cannot be changed after creation. | 400 invalid data; 404 missing country; 409 duplicate; 401/403 missing access |
| `publisher_service.py` | `list_publishers()` | Page active publishers with server search and sort. | 400 invalid page or sort |
| `publisher_service.py` | `get_publisher(publisher_id)` | Return a publisher and associated works. | 404 publisher missing |
| `publisher_service.py` | `save_publisher()` | Create a publisher with description and country. The profile cannot be changed after creation. | 400 invalid data; 404 missing country; 409 duplicate; 401/403 missing access |
| `country_service.py` | `list_countries()` | Page countries stored in the local database. | 400 invalid page or sort |
| `country_service.py` | `sync_countries()` | Fetch countries from the provider and upsert local records. | 400/502 provider or payload failure; 401/403 missing access |

The development-only `seed-demo` command calls `enrich_demo_catalog()` from `services/catalog/demo_catalog_service.py`. It creates the demo countries, authors, and publishers and fills missing descriptions or country codes. Existing nonempty profile fields are preserved. This helper is not an HTTP endpoint and does not run in production.

See [endpoint mapping](../endpoints/README.md) and [shared response rules](../../../architecture/api-contract.md).
