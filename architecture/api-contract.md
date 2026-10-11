# API contract

All Flask routes are under `/api`. Successful mutations return a JSON `message` plus operation data. Errors return `{"error":"..."}` with the relevant HTTP status. Most list endpoints return:

```json
{"items":[],"pagination":{"page":1,"per_page":12,"total":0,"pages":0,"has_next":false,"has_previous":false}}
```

`page` and `per_page` must be positive integers; `per_page` is capped by the endpoint. `sort_direction` is `asc` or `desc`. Endpoint filters such as `q`, `condition`, `publisher`, `min_price`, `max_price`, `status`, `action`, and `min_rating` are evaluated in SQL by the server. Invalid values return `400`. Client interfaces use camelCase and map to the server's snake_case wire contract in shared helpers.

Protected routes require `Authorization: Bearer <token>` from the Next.js server layer. Missing or invalid authentication returns `401`; insufficient permission or ownership returns `403`. Missing active resources return `404`, and conflicts such as duplicates or exhausted copies return `409`. A `DELETE` request sets `is_deleted` for supported models instead of removing the row.

The endpoint tables in each [feature folder](../README.md) identify the actual registered route, access expression, service function, business action, and feature-specific errors.
