# Deployment requirements and known limitations

Publishing the source on GitHub does not deploy or secure a live service. Before a public application deployment:

1. Set `APP_ENV=production`, configure a strong persistent `JWT_SECRET_KEY`, and keep database credentials and environment files outside Git. The current fallback generates a temporary secret; it is unsuitable for multiple production workers or stable sessions.
2. Use HTTPS, a production Next.js build, and an appropriate Flask/Socket.IO deployment behind a reverse proxy. Configure the exact allowed browser origins; do not enable development debug mode.
3. Do not seed documented demo accounts on production. The seed command refuses when `APP_ENV=production`, but changing that environment variable does not remove already-seeded accounts. Disable or remove demo access before exposing an existing database.
4. Add distributed rate limiting for login, registration, uploads, expensive exports, and Socket.IO joins. No application rate limiter is currently implemented.
5. Add session revocation/token versioning if password changes or logout must invalidate all issued JWTs immediately. Logout currently removes browser cookies; a copied token remains usable until expiry unless its account is disabled, archived, or deleted.
6. Configure and test production browser headers, including a CSP compatible with Chakra/Emotion and Next.js, frame restrictions, and HSTS after HTTPS is confirmed. The API's existing headers alone do not provide these protections to client pages.
7. Treat image uploads as public. Introduce authenticated media delivery and storage lifecycle cleanup before supporting private files. Add malware scanning and image re-encoding if the upload threat model requires them.
8. Use a least-privilege PostgreSQL account, protect pgAdmin from public access, back up the database and uploaded files, and test restore procedures.
9. Review dependency advisories and run secret scanning on the full Git history before publishing or deploying. A passing build or test suite does not establish that dependencies are vulnerability-free.

Next.js Server Actions and SameSite cookies contribute to browser request protection; do not relax allowed origins or add cookie-authenticated Flask endpoints without reviewing CSRF protection. Server Actions are callable endpoints and must continue relying on server-side authorization.

The current receipt uses the book's current price rather than an immutable transaction-price snapshot. This is an integrity limitation for a real paid library; design payment verification, idempotency, transactional inventory locking, and immutable purchase amounts before accepting real payments. The current demo does not integrate a payment gateway.
