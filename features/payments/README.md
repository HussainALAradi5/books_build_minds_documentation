# Payments and transaction workflow

Purchases and paid rentals use **Tap Payments' BHD sandbox**. Live keys are rejected. Free borrowing does not require payment credentials. No card data is collected by the library client or API.

## Flow

1. Client loads the server quote and displays availability, BHD price, duration and policy terms.
2. Reader accepts the terms and confirms. No loan or reservation is created by opening or cancelling the dialog.
3. Server checks permissions, policy version, current amount and inventory under a PostgreSQL row lock.
4. A pending transaction reserves a copy before the external call. A stable Tap `reference.idempotent` value prevents duplicate charge creation within the provider's 24-hour window.
5. Reader completes the hosted checkout. A browser redirect alone never grants a book.
6. Signed webhook or authenticated refresh retrieves the canonical charge. Only a matching test-mode captured charge completes the transaction.
7. Ownership/rental, receipt snapshot, activities and transaction status commit together. Public socket events contain inventory changes, not payment information.

## States

| State | Meaning | Stock |
| --- | --- | --- |
| pending | Provider state is unresolved | Reserved |
| completed | Verified purchase or rental fulfilled | Sold or on loan |
| expired | Provider reports a terminal unsuccessful charge | Released |
| refund_pending | Captured payment cannot safely be fulfilled | Released; refund reconciliation required |
| refunded | Matching full refund confirmed by provider | Released |
| failed | Reserved application status for a confirmed failure | Released |

Unknown states and network errors retain the reservation. Local time alone never releases it. Cancelling locally cannot cancel a Tap charge: cancel on the hosted page or wait for provider expiry, then refresh. A late captured payment after release requires a refund, not unreserved fulfillment.

Read [setup](setup/README.md), [services](services/README.md), [endpoints](endpoints/README.md), and [verification/limitations](verification/README.md).
