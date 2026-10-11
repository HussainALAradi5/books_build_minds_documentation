# Media and profile pictures: endpoints

All paths are relative to the Flask origin. The controller registers each route and applies access checks; the named service executes the operation. `401` means missing/invalid authentication on a protected route, and `403` means insufficient permission.

| Method | Endpoint | Access rule | Service function | What happens | Exceptions / error responses |
| --- | --- | --- | --- | --- | --- |
| GET | `/api/media/books/<int:book_id>` | `Public` | `media_service.list_media` | Page active images linked to a visible book. | 400 invalid page; 404 missing book |
| POST | `/api/media/books/<int:book_id>` | `Permission.BOOK_MEDIA_WRITE` | `media_service.upload_media` | Validate 1–10 images, save files and media records, set first book cover, and roll back files on DB failure. | 400 invalid count/file; 404 missing book; 401/403 missing access |
| POST | `/api/media/profile-picture` | `Permission.PROFILE_MEDIA_WRITE_OWN` | `media_service.upload_profile_picture` | Validate one image, save a profile media record, and remove file on DB failure. | 400 invalid count/file; 401/403 missing access |

See [service signatures and rules](../services/README.md).
