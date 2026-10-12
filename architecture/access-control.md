# Access control

An account has one built-in role (`visitor`, `client`, `employee`, or `admin`), optional direct access roles, and optional groups containing roles. Permission keys have the shape `resource:action.scope`, such as `review:edit.own` and `book:edit.any`. The effective grants are the union of the account role, direct roles, and group roles; archived/deleted records stop granting access.

Flask's `require_user` validates the JWT and evaluates the required rule before calling a service. `all_of` requires every rule, `any_of` accepts at least one, `owns(user_id)` checks the user ID, and `purchased(book_id)` checks ownership. Review creation combines the write permission with purchase. Review updates/deletion allow the author with an own permission or an administrator with an any permission. Services also validate resource state and conflicts after authorization.

The server owns enforcement; hiding a client button is only presentation. The administration UI is `/admin/access`, and its management API requires both the administrator account role and the matching access permission. The [access feature](../features/access/README.md) lists its persisted permission, role, group, and assignment APIs. Built-in permission keys and roles cannot be soft deleted. The built-in admin role's grants are fixed, and self-demotion from admin is rejected.

Book changes and access management require the administrator account role in addition to their action permissions. Delegating a book-write permission to another account does not bypass that role check. Custom roles may grant supported read or member actions; review the resulting grants before assignment. An account role and an access role are separate fields.
