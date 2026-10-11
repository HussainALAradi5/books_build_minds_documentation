# Borrow, rent, and return: endpoints

All paths are relative to the Flask origin. The controller registers each route and applies access checks; the named service executes the operation. `401` means missing/invalid authentication on a protected route, and `403` means insufficient permission.

| Method | Endpoint | Access rule | Service function | What happens | Exceptions / error responses |
| --- | --- | --- | --- | --- | --- |
| GET | `/api/book/<string:slug>/loan` | `Permission.LOAN_READ_OWN` | `loan_service.current_loan` | Return the signed-in user's current loan for a book. | 404 missing book; 401/403 missing access |
| POST | `/api/book/<string:slug>/borrow` | `Permission.LOAN_WRITE_ONE` | `loan_service.borrow_book` | Delegate to start_loan with borrow kind. | 404 missing book; 409 duplicate loan or no copies |
| POST | `/api/book/<string:slug>/rent` | `Permission.LOAN_WRITE_ONE` | `loan_service.rent_book` | Delegate to start_loan with rent kind. | 404 missing book; 409 duplicate loan or no copies |
| POST | `/api/book/<string:slug>/return` | `Permission.LOAN_WRITE_ONE` | `loan_service.return_book` | Timestamp the user's active loan and record a return activity. | 404 missing book or active loan; 401/403 missing access |
| GET | `/api/profile/<int:user_id>/loans` | `Permission.LOAN_READ_OWN` | `loan_service.list_loans` | Page a user's loans by active, overdue, or returned status. | 400 invalid status/page; 403 other user's loans |

See [service signatures and rules](../services/README.md).
