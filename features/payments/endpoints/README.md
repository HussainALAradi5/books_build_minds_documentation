# Payment endpoints

All paths below start with `/api`. JSON contracts are snake_case at the Flask boundary; the shared client adapter maps to camelCase interfaces. Errors use the existing reusable API error response.

| Method/path | Access | Service/function | Result | Exceptions |
| --- | --- | --- | --- | --- |
| GET `/book/<slug>/quote` | Signed in | loan_quote_service.loan_quote | Current terms, stock and BHD quote | 401, 404 |
| POST `/transactions/checkout` | Signed in + purchase.write.one or loan.write.one | checkout_service.start_checkout | Reused/new transaction and hosted URL if pending | 400, 401, 403, 404, 409, 429, 502, 503 |
| GET `/transactions` | Signed in, own records | transaction_service.list_transactions | Shared paginated list; status/page/per_page/sort parameters | 400, 401 |
| GET `/transactions/<id>` | Owner/admin | transaction_service.owned_transaction | Transaction status and receipt reference | 401, 404 |
| POST `/transactions/<id>/sync` | Owner/admin | transaction_service.reconcile | Verified updated state | 401, 404, 409, 429, 502, 503 |
| POST `/transactions/<id>/cancel` | Owner/admin | transaction_service.cancel | Terminal state, or pending explanation | 401, 404, 409, 429, 502, 503 |
| GET `/transactions/<id>/receipt` | Owner/admin | receipt_service.download_transaction_receipt | PDF for completed transaction | 401, 404, 409 |
| POST `/payments/tap/webhook` | Valid Tap `hashstring` signature | tap_gateway.verify_event → retrieve_session → fulfillment_service.apply_session | Acknowledgement after verified application | 400 invalid signature/payload, 413 size, 404 unknown reference, 409 mismatch, 502/503 provider/config |

Checkout body: `book_slug`, `kind` (`purchase`/`rental`), UUID `request_key`, `accepted_terms: true`, current `policy_version`, and integer `expected_amount_minor`. The amount is re-derived server-side; the client cannot choose the price.

Free POST `/book/<slug>/borrow` requires accepted terms and policy version. Legacy direct `/purchase` and `/rent` reject with 409, preventing unpaid fulfillment. Webhook is a direct backend route; it is not exposed through the authenticated client service bridge.
