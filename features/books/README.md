# Books and purchases

Public catalog, administrative inventory, imports, exports, purchases, and soft deletion.

Each book belongs to one to five categories. Administrators create categories separately, then select existing authors, publishers, and categories through paginated search when adding a book. The catalog supports category filtering and server-ranked `most_rated` and `best_selling` sorts. Category showcase pages return the top five books per category. Demo data includes multiple categories on applicable books.

After a book is created, its book record is fixed except for its price. An administrator changes the price through `PATCH /api/book/<slug>/price`; the API rejects any additional fields. The creation request may include a description up to 4,000 characters; imported rows may include a `description` column. Author and publisher profiles are created separately and then read only. The book page places its description, price, publication facts, condition, availability, and actions in one reusable card. Small author and publisher buttons open read-only dialogs with their country, description, and works.

- [Services and business logic](services/README.md)
- [Endpoints and error cases](endpoints/README.md)
