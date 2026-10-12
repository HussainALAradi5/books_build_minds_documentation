# Local setup

Clone the [server](https://github.com/HussainALAradi5/books_build_minds_server/blob/main/README.md), [client](https://github.com/HussainALAradi5/books_build_minds_client_side/blob/main/README.md), and this documentation repository into sibling folders named `server`, `client`, and `documentation`. Start PostgreSQL and create `books_builds_minds` in pgAdmin. Follow the server README to configure `.env`, install Python requirements, apply migrations, run `seed-demo --test-accounts`, and start Flask. Then follow the client README to configure `.env.local`, install Node packages, and start Next.js.

The server supplies all demo books and accounts. `seed-demo --test-accounts` uses the local [test credentials and role rules](demo-accounts.md); plain `seed-demo` uses random passwords for new accounts. The client points both `BACKEND_URL` and `NEXT_PUBLIC_BACKEND_URL` to the Flask origin. The default UI URL is `http://localhost:3000`; the API origin depends on your server run configuration.

For ordered database updates, see [migrations](../database/migrations.md). For operational checks, see [verification](../testing/verification.md).
