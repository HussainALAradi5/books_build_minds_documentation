# Authors, publishers, categories, and countries

Author and publisher records, optional addresses, countries and works, plus reusable book categories.

An administrator refreshes countries from the external provider, then selects a country by name while creating an author or publisher. Address is optional. Profiles are searched and paged on the server. The book page shows each read only creator profile in a small dialog, including country, address, description, and works.

Administrators add categories independently. Each book has one to five categories, so Science and Comedy can both apply. The public catalog can filter by category and rank books by average reader rating or completed purchases. `GET /api/categories/top` returns up to five books for each category on a paginated category page.

- [Services and business logic](services/README.md)
- [Endpoints and error cases](endpoints/README.md)
