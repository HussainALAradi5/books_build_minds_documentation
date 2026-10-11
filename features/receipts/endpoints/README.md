# Receipts: endpoints

All paths are relative to the Flask origin. The controller registers each route and applies access checks; the named service executes the operation. `401` means missing/invalid authentication on a protected route, and `403` means insufficient permission.

| Method | Endpoint | Access rule | Service function | What happens | Exceptions / error responses |
| --- | --- | --- | --- | --- | --- |
| GET | `/api/book/<string:slug>/receipt` | `Permission.RECEIPT_READ_OWN` | `receipt_service.download_receipt` | Check purchaser ownership and stream a server-generated PDF. | 403 no purchase; 404 missing book |

See [service signatures and rules](../services/README.md).
