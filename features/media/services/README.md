# Media and profile pictures: services

Server validated image uploads and metadata records for books and profiles.

Service functions return Flask responses unless described as an internal helper. Endpoint-level authorization is enforced by `require_user` before a protected service runs. Business checks remain in the service.

| Service file | Signature | Business and code behavior | Errors and when they occur |
| --- | --- | --- | --- |
| `media_service.py` | `list_media(book_id)` | Page active images linked to a visible book. | 400 invalid page; 404 missing book |
| `media_service.py` | `validate_file(file)` | Read and verify actual image bytes, size, and supported JPEG/PNG/GIF/WebP format. | ValueError empty, over 5 MB, corrupt, or unsupported image |
| `media_service.py` | `upload_media(book_id)` | Validate 1–10 images, save files and media records, set first book cover, and roll back files on DB failure. | 400 invalid count/file; 404 missing book; 401/403 missing access |
| `media_service.py` | `upload_profile_picture()` | Validate one image, save a profile media record, and remove file on DB failure. | 400 invalid count/file; 401/403 missing access |

See [endpoint mapping](../endpoints/README.md) and [shared response rules](../../../architecture/api-contract.md).
