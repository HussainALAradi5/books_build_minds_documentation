# Third-party and external APIs

The Flask server owns these integrations. Client components call the project's API and never receive provider credentials. This table lists every external HTTP API used by the current code.

| Provider | Project use and server entry point | Configuration | Test path and limits |
| --- | --- | --- | --- |
| [Resend Email API](https://resend.com/docs) | Password reset email; `services/accounts/reset_delivery.py` posts to `https://api.resend.com/emails`. | `RESEND_API_KEY`, `RESEND_FROM_EMAIL` in server `.env`. Verify the sending domain for real recipients. | The [free plan](https://resend.com/pricing) currently lists 3,000 emails/month and 100/day. A test sender can be used within [Resend's test restrictions](https://resend.com/docs/dashboard/domains/introduction). No key is committed. |
| [countries.dev](https://countries.dev/docs/api/countries) | Administrator country sync; `services/country_service.py` fetches name, two-letter code, and flags into the local `country` table. Author and publisher records reference those local rows. | No API key; administrator calls `POST /api/countries/sync`. | [Keyless API](https://countries.dev/docs/authentication). After sync, the client loads `/api/countries` to populate the country selector. The book detail dialogs read country data from the book API; they do not call the provider. |
| [Tap Payments API](https://developers.tap.company/docs/get-started) | BHD sandbox charges and refunds; `services/payments/tap_gateway.py` calls `https://api.tap.company/v2/`. | `TAP_SECRET_KEY`, `TAP_WEBHOOK_URL` in server `.env`; see [payment setup](../features/payments/setup/README.md). | Merchant sandbox credentials and webhook access are required. The project rejects live keys. Tap availability and sandbox terms must be confirmed with the provider. |

## Test password recovery without a provider account

Set `PASSWORD_RESET_DELIVERY=console` in the server's local `.env`, keep `APP_ENV` different from `production`, and restart Flask. Create an account using an email address you control, then request a reset from `/forgot-password`. The six-digit code appears only in the **Flask terminal**, never in the HTTP response or browser. Console delivery is rejected in production.

To test real email delivery, configure a Resend account and verified sender. The reset endpoint accepts an email, username, or saved E.164 mobile number as the identifier, but always sends the code to the saved email. **SMS is not integrated.** A free SMS trial would not provide unrestricted custom OTP delivery to arbitrary phones; this flow does not claim that it does.

Request codes expire after 10 minutes, can be used once, and allow at most five guesses. Requests have per-IP rate limits and a per-account one-minute resend interval. The server stores an HMAC digest, not the code, and a successful reset increments the user's token version to revoke old sessions. See [account endpoints](../features/accounts/endpoints/README.md) and [security controls](../security/controls/README.md).
