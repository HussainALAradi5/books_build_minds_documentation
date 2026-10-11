# Reviews: services

A purchaser may leave one active review with a rating and comment; the author or a moderator may change it.

Service functions return Flask responses unless described as an internal helper. Endpoint-level authorization is enforced by `require_user` before a protected service runs. Business checks remain in the service.

| Service file | Signature | Business and code behavior | Errors and when they occur |
| --- | --- | --- | --- |
| `reviews/review_service.py` | `list_reviews(slug)` | Page visible reviews with comment search, minimum rating, and sort. | 400 invalid rating, sort, or page; 404 missing book |
| `reviews/review_service.py` | `my_review(slug)` | Return the current user's active review for a book. | 404 missing book; 401/403 missing access |
| `reviews/review_service.py` | `add_review(slug)` | Require purchase and one active review; validate rating 1–5 and a 10-character comment; broadcast change. | 400 invalid rating/comment; 403 no purchase; 404 book; 409 duplicate |
| `reviews/review_service.py` | `owned_review(slug, review_id, own_permission: Permission, any_permission: Permission)` | Load a review and enforce author ownership or any-review moderation permission. | 403 not author/moderator; 404 missing review |
| `reviews/review_service.py` | `edit_review(slug, review_id)` | Update permitted review fields and broadcast the result. | 400 invalid rating/comment; 403 not author/moderator; 404 review |
| `reviews/review_service.py` | `delete_review(slug, review_id)` | Soft delete an authorized review and broadcast the result. | 403 not author/moderator; 404 review |

See [endpoint mapping](../endpoints/README.md) and [shared response rules](../../../architecture/api-contract.md).
