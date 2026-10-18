# Books and purchases

Public catalog, administrative inventory, imports, exports, purchases, and soft deletion.

After a book is created, its book record is fixed except for its price. An administrator changes the price through `PATCH /api/book/<slug>/price`; the API rejects any additional fields. The creation request may include a description up to 4,000 characters; imported rows may include a `description` column. Author and publisher profiles are created separately and then read only. The book page places its description, price, publication facts, condition, availability, and actions in one reusable card; author and publisher biographies share a separate tab.

- [Services and business logic](services/README.md)
- [Endpoints and error cases](endpoints/README.md)
