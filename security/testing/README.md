# Security verification

Run from the server repository after installing its dependencies:

```powershell
python -m unittest discover -s tests -v
```

The tests use an isolated in-memory SQLite database. They cover profile ownership, registration role control, book permissions, image validation, profile image ownership, purchase-gated reviews, review owner/admin moderation, review/book ID matching, receipt ownership, soft deletion, and permission expressions.

`test_delegated_permissions_cannot_bypass_admin_or_review_ownership` assigns privileged action permissions to an employee and verifies that book writes/import/bulk/media and another reader's review edit/delete still return 403. Only the review author can edit; the author and administrator can delete.

For each new endpoint, add negative checks for anonymous requests, wrong account roles, wrong owners, deleted/archived records, malformed input, and repeated submissions as appropriate. Do not rely solely on successful administrator requests.

UI checks cover protected page composition and absence of an Edit book tab for a reader. Reusable tabs retain state, but hidden panels must never receive secrets. Guide navigation must issue no business mutations. Verify keyboard navigation, Escape dismissal, focus restoration, reduced motion, and 390px layouts whenever changing the shared controls.

Production readiness also requires the outstanding controls in the [deployment guide](../deployment/README.md). These regression tests are not a substitute for dependency review, load testing, or independent security assessment.
