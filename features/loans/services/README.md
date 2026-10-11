# Borrow, rent, and return: services

Availability checked at loan creation; borrowing lasts 14 days and rental lasts 7 days.

Service functions return Flask responses unless described as an internal helper. Endpoint-level authorization is enforced by `require_user` before a protected service runs. Business checks remain in the service.

| Service file | Signature | Business and code behavior | Errors and when they occur |
| --- | --- | --- | --- |
| `loan_service.py` | `active_loan(book_id, user_id)` | Find one user's unreturned loan for a book. | None when no active loan |
| `loan_service.py` | `current_loan(slug)` | Return the signed-in user's current loan for a book. | 404 missing book; 401/403 missing access |
| `loan_service.py` | `borrow_book(slug)` | Delegate to start_loan with borrow kind. | 404 missing book; 409 duplicate loan or no copies |
| `loan_service.py` | `rent_book(slug)` | Delegate to start_loan with rent kind. | 404 missing book; 409 duplicate loan or no copies |
| `loan_service.py` | `start_loan(slug, kind)` | Lock the book row, check availability, set due date and rental fee, and record activity. | 404 missing book; 409 existing loan or no copies |
| `loan_service.py` | `return_book(slug)` | Timestamp the user's active loan and record a return activity. | 404 missing book or active loan; 401/403 missing access |
| `loan_service.py` | `list_loans(user_id)` | Page a user's loans by active, overdue, or returned status. | 400 invalid status/page; 403 other user's loans |

See [endpoint mapping](../endpoints/README.md) and [shared response rules](../../../architecture/api-contract.md).
