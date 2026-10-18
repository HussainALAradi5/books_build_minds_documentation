# Books and purchases

Public catalog, administrative inventory, imports, exports, purchases, and soft deletion.

After a book is created, its book record is fixed except for its price. An administrator changes the price through `PATCH /api/book/<slug>/price`; the API rejects any additional fields. Author and publisher profiles are created separately and then read only. The book page displays its publisher, publication date, ISBN, and condition in a reusable information card near the title; author and publisher biographies share a separate tab.

- [Services and business logic](services/README.md)
- [Endpoints and error cases](endpoints/README.md)
