# Accounts and profiles: services

Registration, login, personal profiles, purchases, activity history, and administrator requests.

Service functions return Flask responses unless described as an internal helper. Endpoint-level authorization is enforced by `require_user` before a protected service runs. Business checks remain in the service.

| Service file | Signature | Business and code behavior | Errors and when they occur |
| --- | --- | --- | --- |
| `account_service.py` | `register()` | Validate identity and password, create an active client account, and record its creator. | 400 invalid input; 409 duplicate username or email |
| `account_service.py` | `login()` | Verify an active account and password, then issue a JWT. | 401 invalid credentials or inactive account |
| `account_service.py` | `profile(user_id)` | Return the authenticated user's profile and effective permissions. | 403 other user's profile |
| `account_service.py` | `edit_profile(user_id)` | Update own identity or password; reject role and account status changes. | 400 invalid fields; 403 other user or role change; 409 duplicate identity |
| `account_service.py` | `purchased_books(user_id)` | Filter the user's purchases on the server and return a page. | 400 invalid page or sort; 403 other user |
| `account_service.py` | `list_activities(user_id)` | Page the user's library actions with optional action filter. | 400 invalid action or page; 403 other user |
| `account_service.py` | `list_admin_requests()` | Page administrator access requests by decision state. | 400 invalid status or page; 401/403 missing access |
| `account_service.py` | `submit_admin_request()` | Create one pending request for a nonadministrator with a reason. | 400 short reason; 403 administrator; 409 existing pending request |
| `account_service.py` | `review_admin_request(request_id)` | Record an accept or reject decision; acceptance promotes the requester. | 400 invalid decision; 404 missing request or user; 409 already reviewed |

See [endpoint mapping](../endpoints/README.md) and [shared response rules](../../../architecture/api-contract.md).
