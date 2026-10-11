# Permissions, roles, and groups: endpoints

All paths are relative to the Flask origin. The controller registers each route and applies access checks; the named service executes the operation. `401` means missing/invalid authentication on a protected route, and `403` means insufficient permission.

| Method | Endpoint | Access rule | Service function | What happens | Exceptions / error responses |
| --- | --- | --- | --- | --- | --- |
| GET | `/api/access/permissions` | `admin role AND Permission.ACCESS_READ_ANY` | `permission_service.list_permissions` | Page active permission keys with search and sort. | 400 invalid page; 401/403 missing access |
| POST | `/api/access/permissions` | `admin role AND Permission.ACCESS_EDIT_ANY` | `permission_service.save_permission` | Create or update a validated permission key and label. | 400 invalid key/label; 404 missing; 409 duplicate |
| PUT | `/api/access/permissions/<string:key>` | `admin role AND Permission.ACCESS_EDIT_ANY` | `permission_service.save_permission` | Create or update a validated permission key and label. | 400 invalid key/label; 404 missing; 409 duplicate |
| DELETE | `/api/access/permissions/<string:key>` | `admin role AND Permission.ACCESS_EDIT_ANY` | `permission_service.delete_permission` | Soft delete a custom permission; protect built-in keys. | 403 built-in; 404 missing |
| GET | `/api/access/roles` | `admin role AND Permission.ACCESS_READ_ANY` | `role_service.list_roles` | Page active access roles with search and sort. | 400 invalid page; 401/403 missing access |
| POST | `/api/access/roles` | `admin role AND Permission.ACCESS_EDIT_ANY` | `role_service.save_role` | Create or update role metadata and permission assignments; keep admin grants fixed. | 400 invalid key/name/permission; 403 admin grants fixed; 404 missing; 409 duplicate |
| PUT | `/api/access/roles/<string:key>` | `admin role AND Permission.ACCESS_EDIT_ANY` | `role_service.save_role` | Create or update role metadata and permission assignments; keep admin grants fixed. | 400 invalid key/name/permission; 403 admin grants fixed; 404 missing; 409 duplicate |
| DELETE | `/api/access/roles/<string:key>` | `admin role AND Permission.ACCESS_EDIT_ANY` | `role_service.delete_role` | Soft delete a custom role; protect built-in roles. | 403 built-in; 404 missing |
| GET | `/api/access/groups` | `admin role AND Permission.ACCESS_READ_ANY` | `group_service.list_groups` | Page active groups with search and sort. | 400 invalid page; 401/403 missing access |
| POST | `/api/access/groups` | `admin role AND Permission.ACCESS_EDIT_ANY` | `group_service.save_group` | Create or update a named group and its role assignments. | 400 invalid name/role; 404 missing; 409 duplicate |
| PUT | `/api/access/groups/<int:group_id>` | `admin role AND Permission.ACCESS_EDIT_ANY` | `group_service.save_group` | Create or update a named group and its role assignments. | 400 invalid name/role; 404 missing; 409 duplicate |
| DELETE | `/api/access/groups/<int:group_id>` | `admin role AND Permission.ACCESS_EDIT_ANY` | `group_service.delete_group` | Soft delete a group and stop applying its grants. | 404 missing |
| GET | `/api/access/users` | `admin role AND Permission.ACCESS_READ_ANY` | `user_access_service.list_users` | Page nondeleted users by username search. | 400 invalid page; 401/403 missing access |
| PUT | `/api/access/users/<int:user_id>` | `admin role AND Permission.ACCESS_EDIT_ANY` | `user_access_service.set_user_access` | Validate account role, direct roles, and groups; prevent admin self-demotion. | 400 invalid or inactive references; 403 self-demotion; 404 user |

See [service signatures and rules](../services/README.md).
