# Reviews: endpoints

All paths are relative to the Flask origin. The controller registers each route and applies access checks; the named service executes the operation. `401` means missing/invalid authentication on a protected route, and `403` means insufficient permission.

| Method | Endpoint | Access rule | Service function | What happens | Exceptions / error responses |
| --- | --- | --- | --- | --- | --- |
| GET | `/api/book/<string:slug>/review` | `Public` | `review_service.list_reviews` | Page visible reviews with comment search, minimum rating, and sort. | 400 invalid rating, sort, or page; 404 missing book |
| GET | `/api/book/<string:slug>/review/mine` | `Permission.REVIEW_READ_OWN` | `review_service.my_review` | Return the current user's active review for a book. | 404 missing book; 401/403 missing access |
| POST | `/api/book/<string:slug>/review` | `Permission.REVIEW_WRITE_ONE` | `review_service.add_review` | Require purchase and one active review; validate rating 1–5 and a 10-character comment; broadcast change. | 400 invalid rating/comment; 403 no purchase; 404 book; 409 duplicate |
| PUT | `/api/book/<string:slug>/review/<int:review_id>` | `Permission.REVIEW_EDIT_OWN` plus author ownership | `review_service.edit_review` | Author updates their review and broadcasts the result. | 400 invalid rating/comment; 403 not author; 404 review |
| DELETE | `/api/book/<string:slug>/review/<int:review_id>` | `any_of(Permission.REVIEW_DELETE_OWN, Permission.REVIEW_DELETE_ANY)` | `review_service.delete_review` | Soft delete an authorized review and broadcast the result. | 403 not author/administrator; 404 review |

See [service signatures and rules](../services/README.md).
