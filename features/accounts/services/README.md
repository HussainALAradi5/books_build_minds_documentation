# Accounts and profiles: services

Registration, login, personal profiles, purchases, activity history, and administrator requests.

Service functions return Flask responses unless described as an internal helper. Endpoint-level authorization is enforced by `require_user` before a protected service runs. Business checks remain in the service.

| Service file | Signature | Business and code behavior | Errors and when they occur |
| --- | --- | --- | --- |
| `account_service.py` | `register()` | Validate username, email, optional international mobile number, and password; create an active client account. | 400 invalid input; 409 duplicate identity |
| `account_service.py` | `login()` | Resolve email, username, or mobile; verify the mandatory password and active status; issue a JWT. | 401 invalid credentials or inactive account |
| `accounts/password_reset_service.py` | `issue_code()` | Generate a six-digit code, store only an HMAC digest, expire it after 10 minutes, allow one issue per account per minute, and send it to the account email. Unknown users receive the same response. | 400 missing identifier; 503 delivery not configured; 429 IP rate limit |
| `accounts/password_reset_service.py` | `confirm_code()` | Match the latest unused code, allow at most five guesses, replace the password, consume the code, and increment `token_version` to revoke sessions. | 400 invalid, expired, used, or locked code; 429 IP rate limit |
| `accounts/reset_delivery.py` | `send_reset_code(email, code)` | Send through the fixed Resend HTTPS endpoint, or log the code only in explicitly enabled development console mode. Provider credentials stay on the server. | Provider failure is logged without exposing whether an account exists. |
| `account_service.py` | `profile(user_id)` | Return the authenticated user's profile and effective permissions. | 403 other user's profile |
| `account_service.py` | `edit_profile(user_id)` | Update own identity, optional mobile number, or password; reject role and account status changes. | 400 invalid fields; 403 other user or role change; 409 duplicate identity |
| `account_service.py` | `purchased_books(user_id)` | Filter the user's purchases on the server and return a page. | 400 invalid page or sort; 403 other user |
| `account_service.py` | `list_activities(user_id)` | Page the user's library actions with optional action filter. | 400 invalid action or page; 403 other user |
| `account_service.py` | `list_admin_requests()` | Page administrator access requests by decision state. | 400 invalid status or page; 401/403 missing access |
| `account_service.py` | `submit_admin_request()` | Create one pending request for a nonadministrator with a reason. | 400 short reason; 403 administrator; 409 existing pending request |
| `account_service.py` | `review_admin_request(request_id)` | Record an accept or reject decision; acceptance promotes the requester. | 400 invalid decision; 404 missing request or user; 409 already reviewed |

See [endpoint mapping](../endpoints/README.md) and [shared response rules](../../../architecture/api-contract.md).
