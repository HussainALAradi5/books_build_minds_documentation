# Receipt services

| Function | Behavior | Exceptions |
| --- | --- | --- |
| `receipt_service.download_receipt(slug)` | Finds the signed-in user's latest purchase receipt, including legacy records; renders immutable snapshot values | 404 missing book; 403 no purchase receipt |
| `receipt_service.download_transaction_receipt(transaction)` | Streams PDF for the authorized completed transaction | 409 receipt not ready; controller enforces owner/admin |
| `receipt_service.send_receipt(receipt)` | Returns PDF attachment with stable receipt filename | Rendering errors propagate; no payment state mutation |
| `receipt_pdf.render_receipt(receipt)` | Formats snapshot title, customer, amount, currency, date and transaction ID; marks sandbox receipts | Legacy unknown amount is explicit, never replaced with current price |

Receipts are created inside `fulfillment_service.complete`, in the same transaction as verified ownership or rental. One receipt per transaction allows repeated rentals without overwriting history. See [payment services](../../payments/services/README.md).
