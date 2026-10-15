# Payment service contracts

Paths are relative to the server repository. Transport details stay in `services/payments/tap_gateway.py`; controllers apply authentication and serialize service results.

| Service/function | Signature | Business logic | Errors and when |
| --- | --- | --- | --- |
| checkout_service.start_checkout | `(data) -> Transaction` | Validates UUID request key, action, accepted policy and current amount; locks book, reserves copy and creates/reuses hosted checkout | 400 invalid input; 403 controller permission; 404 hidden/missing book; 409 changed price/policy, owned book, active loan, no stock or conflicting checkout; 503 missing sandbox configuration |
| checkout_service.ensure_checkout | `(item) -> Transaction` | Reuses charge ID; otherwise repeats the same provider idempotency key and applies canonical result | 409 inconsistent provider ID or ambiguous creation older than 23 hours; 502 provider unavailable/rejects request |
| fulfillment_service.apply_session | `(session, event_id=None, event_type='reconcile') -> Transaction` | Locks book then transaction; checks amount, currency, test mode, metadata and charge ID; fulfills atomically once; records verified event | 404 unknown reference; 409 verification mismatch; database conflicts roll back |
| fulfillment_service.retry_refund | `(item) -> Transaction` | Persists first attempt time; creates one full refund or retrieves existing refund; validates amount/currency/charge/test mode | 409 mismatch or ambiguous attempt older than 23 hours; 502 provider failure; pending provider refund remains pending |
| transaction_service.owned_transaction | `(transaction_id) -> Transaction` | Owner or administrator access; hides other users' records | 404 missing, hidden or unauthorized record |
| transaction_service.list_transactions | `() -> paginated result` | Current user's transactions, validated status filter and shared pagination/sort | 400 unsupported filter |
| transaction_service.reconcile | `(item) -> Transaction` | Retrieves pending charge; retries pending refund; terminal records are unchanged | Provider/verification errors above |
| transaction_service.cancel | `(item) -> Transaction` | Reconciles before deciding; only confirmed provider terminal status releases stock | 409 still active: cancel at Tap or wait for provider expiry |
| tap_gateway.verify_event | `(payload: bytes, signature: str) -> dict` | Constant-time HMAC comparison with BHD three-decimal amount; rejects malformed payloads | 400 controller response for invalid signature/payload; 503 missing key |
| loan_quote_service.loan_quote | `(slug) -> response` | Produces current availability, BHD amount, borrowing/rental durations and policy terms | 404 hidden/missing book |

Refund and checkout calls have a 20-second timeout. Provider response bodies and credentials are not sent to clients. Financial transaction/event records have no generic update/delete endpoint. Receipts copy immutable payment/title/customer values at fulfillment; legacy receipts retain unknown amounts instead of inventing them from today's price.
