# Local setup

Clone the [server](../../server/README.md), [client](../../client/README.md), and this documentation repository into sibling folders named `server`, `client`, and `documentation`. Start PostgreSQL and create `books_builds_minds` in pgAdmin. Follow the server README to configure `.env`, install Python requirements, apply migrations, seed demo data, and start Flask. Then follow the client README to configure `.env.local`, install Node packages, and start Next.js.

The server supplies all demo books and accounts. `seed-demo` prints generated passwords locally; do not put them in Git documentation. The client points `BACKEND_URL` to the Flask origin. The default UI URL is `http://localhost:3000`; the API origin depends on your server run configuration.

For ordered database updates, see [migrations](../database/migrations.md). For operational checks, see [verification](../testing/verification.md).
