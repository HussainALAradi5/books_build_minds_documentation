# Receipts: services

Each purchase creates a receipt record; the purchaser can download its PDF.

Service functions return Flask responses unless described as an internal helper. Endpoint-level authorization is enforced by `require_user` before a protected service runs. Business checks remain in the service.

| Service file | Signature | Business and code behavior | Errors and when they occur |
| --- | --- | --- | --- |
| `receipts/receipt_service.py` | `create_purchase_receipt(user_id: int, book_id: int)` | Create an unsaved receipt model for the purchase transaction. | Called only from purchase_book |
| `receipts/receipt_service.py` | `download_receipt(slug)` | Check purchaser ownership and stream a server-generated PDF. | 403 no purchase; 404 missing book |

See [endpoint mapping](../endpoints/README.md) and [shared response rules](../../../architecture/api-contract.md).
