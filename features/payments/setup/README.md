# Tap sandbox setup

1. Obtain your own Tap merchant sandbox credentials with BHD enabled. See [Tap getting started](https://developers.tap.company/docs/get-started).
2. Set these values in the **server's local `.env`**. Never commit them or place the secret in `NEXT_PUBLIC_*` variables:

```dotenv
TAP_SECRET_KEY=sk_test_your_own_key
TAP_WEBHOOK_URL=https://your-public-test-api.example/api/payments/tap/webhook
PUBLIC_CLIENT_URL=http://localhost:3000
```

3. Use a controlled HTTPS tunnel or test deployment for the webhook. Forward it to Flask, not the Next.js application. Configure the client `BACKEND_URL` and Socket.IO origin to your API as described in the root setup guide.
4. Back up the database. For an existing database at `c9f1d82b0008`, run `python -m flask --app app db upgrade`, then `python -m flask --app app db check`. For a new database, follow the existing initialization guide; the migration history does not contain the original schema creation.
5. Start the API and client. Sign in as a demo client, open a book, choose Rent or Purchase, accept terms, and complete the hosted sandbox flow using [Tap's test cards](https://developers.tap.company/reference/testing-cards).
6. Confirm that Transactions becomes completed and the PDF receipt matches the paid BHD amount. Replay the webhook and confirm no second receipt, sale, or rental appears.
7. Schedule `python -m flask --app app reconcile-payments --limit 100` every minute using your process scheduler. Alert on failures. This catches missed webhooks and retries required refunds. The repository does not install a scheduler automatically.

If charge creation or refund creation remains ambiguous for 23 hours, automatic creation retries stop before Tap's 24-hour idempotency window closes. An operator must inspect the Tap dashboard and reconcile the matching transaction/reference; never restart payment with a new key merely to clear an error. Existing refund IDs are retrieved instead of creating another refund.

Provider references: [charge creation](https://developers.tap.company/reference/create-a-charge), [idempotency](https://developers.tap.company/docs/idempotency), [webhook verification](https://developers.tap.company/docs/webhook), [refunds](https://developers.tap.company/reference/create-a-refund). These documents support the API contract; merchant account eligibility and sandbox access still need provider confirmation.
