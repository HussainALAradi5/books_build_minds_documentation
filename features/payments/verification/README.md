# Verification and release limits

Automated verification covers stock reservation, duplicate checkout/fulfillment, signature rejection, canonical provider retrieval, immutable receipt amounts, owner access, rental renewal after return, accepted terms, hidden records, changed prices, logout revocation, disabled-account refunds, three-decimal BHD amounts, and test-only credentials. Provider responses are mocked in tests; no runtime payment bypass is installed.

PostgreSQL checks additionally exercise simultaneous requests for the last copy, simultaneous duplicate fulfillment, and migration upgrade/downgrade/re-upgrade with preservation of legacy receipts. SQLite unit tests alone do not prove row-lock behavior.

## Required before calling payments ready

- Run a real Tap sandbox round trip with your own BHD-enabled account and reachable signed webhook, including failed checkout, duplicate callback and refund.
- Configure and monitor the reconciliation scheduler; investigate ambiguous requests before retrying beyond provider idempotency limits.
- Audit historical purchase/inventory counts. Sold copies now consume inventory along with active loans and pending reservations.
- Disable documented demo accounts on any public deployment and meet the security deployment requirements.

This is a sandbox implementation, not a live-payment release. It does not implement taxes, delivery, subscription billing, staff-confirmed physical returns, individual physical-copy identifiers, or an administrator UI for voluntary refunds of completed purchases. Return confirmation records the reader's assertion, not proof of physical collection. Provider success, account onboarding and production operating controls cannot be established by mocked tests.
