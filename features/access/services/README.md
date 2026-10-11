# Permissions, roles, and groups: services

Administrator management of persistent permission keys, roles, groups, and user assignments.

Service functions return Flask responses unless described as an internal helper. Endpoint-level authorization is enforced by `require_user` before a protected service runs. Business checks remain in the service.

| Service file | Signature | Business and code behavior | Errors and when they occur |
| --- | --- | --- | --- |
| `access/permission_service.py` | `list_permissions()` | Page active permission keys with search and sort. | 400 invalid page; 401/403 missing access |
| `access/permission_service.py` | `save_permission(key)` | Create or update a validated permission key and label. | 400 invalid key/label; 404 missing; 409 duplicate |
| `access/permission_service.py` | `delete_permission(key)` | Soft delete a custom permission; protect built-in keys. | 403 built-in; 404 missing |
| `access/role_service.py` | `list_roles()` | Page active access roles with search and sort. | 400 invalid page; 401/403 missing access |
| `access/role_service.py` | `save_role(key)` | Create or update role metadata and permission assignments; keep admin grants fixed. | 400 invalid key/name/permission; 403 admin grants fixed; 404 missing; 409 duplicate |
| `access/role_service.py` | `delete_role(key)` | Soft delete a custom role; protect built-in roles. | 403 built-in; 404 missing |
| `access/group_service.py` | `list_groups()` | Page active groups with search and sort. | 400 invalid page; 401/403 missing access |
| `access/group_service.py` | `save_group(group_id)` | Create or update a named group and its role assignments. | 400 invalid name/role; 404 missing; 409 duplicate |
| `access/group_service.py` | `delete_group(group_id)` | Soft delete a group and stop applying its grants. | 404 missing |
| `access/user_access_service.py` | `serialize_user(user: User)` | Return account role, direct roles, groups, and audit fields for management. | Internal DTO mapping |
| `access/user_access_service.py` | `list_users()` | Page nondeleted users by username search. | 400 invalid page; 401/403 missing access |
| `access/user_access_service.py` | `set_user_access(user_id)` | Validate account role, direct roles, and groups; prevent admin self-demotion. | 400 invalid or inactive references; 403 self-demotion; 404 user |
| `access/common.py` | `list_records(model, search_column, sort_column)` | Shared paginated active-record query used by permissions, roles, and groups. | 400 invalid page or sort |

See [endpoint mapping](../endpoints/README.md) and [shared response rules](../../../architecture/api-contract.md).
