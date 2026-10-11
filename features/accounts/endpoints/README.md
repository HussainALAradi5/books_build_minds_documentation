# Accounts and profiles: endpoints

All paths are relative to the Flask origin. The controller registers each route and applies access checks; the named service executes the operation. `401` means missing/invalid authentication on a protected route, and `403` means insufficient permission.

| Method | Endpoint | Access rule | Service function | What happens | Exceptions / error responses |
| --- | --- | --- | --- | --- | --- |
| POST | `/api/register` | `Public` | `account_service.register` | Validate identity and password, create an active client account, and record its creator. | 400 invalid input; 409 duplicate username or email |
| POST | `/api/login` | `Public` | `account_service.login` | Verify an active account and password, then issue a JWT. | 401 invalid credentials or inactive account |
| GET | `/api/profile/<int:user_id>` | `Permission.PROFILE_READ_OWN` | `account_service.profile` | Return the authenticated user's profile and effective permissions. | 403 other user's profile |
| PUT | `/api/profile/<int:user_id>/edit` | `Permission.PROFILE_EDIT_OWN` | `account_service.edit_profile` | Update own identity or password; reject role and account status changes. | 400 invalid fields; 403 other user or role change; 409 duplicate identity |
| GET | `/api/profile/<int:user_id>/books` | `Permission.PURCHASE_READ_OWN` | `account_service.purchased_books` | Filter the user's purchases on the server and return a page. | 400 invalid page or sort; 403 other user |
| GET | `/api/profile/<int:user_id>/activities` | `Permission.ACTIVITY_READ_OWN` | `account_service.list_activities` | Page the user's library actions with optional action filter. | 400 invalid action or page; 403 other user |
| GET | `/api/admin-requests` | `Permission.ADMIN_REQUEST_READ_ANY` | `account_service.list_admin_requests` | Page administrator access requests by decision state. | 400 invalid status or page; 401/403 missing access |
| POST | `/api/admin-requests` | `Permission.ADMIN_REQUEST_WRITE_ONE` | `account_service.submit_admin_request` | Create one pending request for a nonadministrator with a reason. | 400 short reason; 403 administrator; 409 existing pending request |
| PATCH | `/api/admin-requests/<int:request_id>` | `Permission.ADMIN_REQUEST_EDIT_ANY` | `account_service.review_admin_request` | Record an accept or reject decision; acceptance promotes the requester. | 400 invalid decision; 404 missing request or user; 409 already reviewed |

See [service signatures and rules](../services/README.md).
