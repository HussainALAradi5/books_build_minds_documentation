# Architecture

Books Builds Minds consists of three sibling Git repositories:

```text
books_builds_minds/
├─ client/         Next.js 16, TypeScript, Chakra UI
├─ server/         Flask, SQLAlchemy, Alembic, PostgreSQL
└─ documentation/  Markdown feature and operation guides
```

The client sends requests through typed services and one shared API client. Protected requests run through Next.js server functions, which forward the HttpOnly session token to Flask. The Flask controller checks authentication and permission expressions; service functions validate business rules and persist changes. Book/review events use Socket.IO to refresh subscribed book views. Sample records are seeded only by the server.

## Read next

- [API contract, filters, and errors](api-contract.md)
- [Access control and ownership](access-control.md)
- [Feature service and endpoint maps](../README.md)
- [Planned Flutter mobile architecture](../mobile/README.md)
