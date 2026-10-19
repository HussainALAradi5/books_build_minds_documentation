# Reusable Flutter widgets and state

Use native Flutter layout, theme, accessibility, and animation primitives to build a consistent light/dark design. Color tokens must remain legible in both modes. Enum labels and semantic colors are centralized; a badge reads them from one mapping instead of duplicating switch statements in each screen.

| Shared widget | Typed inputs | Expected behavior |
| --- | --- | --- |
| `GenericCard<T>` | Item, content builder, optional action/status | Consistent surface, spacing, focus, and compact/wide layouts; catalog and profile cards provide their own content builders. |
| `GenericStatusBadge<E>` | Enum value, label/color mapping | Short intrinsic-width pill; never stretches to the card width. Use for condition, role, loan, transaction, and review state. |
| `GenericFilter<F>` | Typed filter model, controls, apply/reset callbacks | Search, searchable publisher picker, condition colors, price bounds, sort; apply/reset refreshes **only results**, not the route. Debounce text search and cancel stale reads. |
| `GenericList<T>` | Page, item builder, empty/error/loading state, optional selection | Cards on phones; optional compact list on wider screens. Server sorting/filtering/pagination; no full dataset download. |
| `GenericPagination` | Server page metadata, page-change callback | Previous/next or load-more according to screen; preserve active filters and scroll position. |
| `GenericForm<T>` | Field descriptors, validation, submit state | Reusable text/select/number/media inputs. Nonnegative numeric constraints; domain validation also occurs on server. Submit button disables while pending and shows a spinner. |
| `GenericDialog` / `ConfirmActionDialog` | Title, content, action, destructive flag | Focus-safe confirmation before delete, return, or bulk changes; destructive action uses a red treatment. Cancel has no side effect. |
| `GenericUpload` | Allowed media, count/size guidance, progress | Multiple book images for authorized admins or one profile picture; preview locally, upload as multipart, rely on server validation. |
| `GenericTabs` | Tab IDs, labels, panel builders | Profile and administration sections retain state; accessible labels and compact navigation on phones. |
| `GenericWorkflowSteps` | Stable steps, current step, server quote, callbacks | Terms → review → confirm for borrow and sandbox checkout. Opening/closing the guide never submits an action. |
| `GenericProgress` | Value, semantic state, optional motion | Availability and workflow progress; a condition wear scale is explicitly labeled as a category, not a measured percentage. Respect reduced-motion settings. |
| `MediaViewer` | Server image URL, alt text | Open a cover or profile image with pinch zoom, bounded pan, and dismiss controls. |

Feature widgets remain separate: `BookInfoCard`, `BookActionBar`, `AuthorInfoDialog`, `PublisherInfoDialog`, `ReviewCard`, `LoanCard`, `TransactionCard`, and `ProfileHeader`. Reuse shared surfaces and controls without forcing unrelated feature data into one giant generic widget. Book description stays in its information card; author and publisher information opens in compact read-only dialogs.

## State and refresh rules

- Each feature controller exposes immutable loading, data, empty, error, and submitting states. One action has one in-flight guard to avoid double submission.
- Search/filter/query state is distinct from the current result page. Applying a new filter resets to page 1; changing a page preserves the filter. A reset updates just the list region.
- A mutation invalidates only affected queries: borrowing refreshes book stock, current loan, loan list, and activity; a review edit refreshes that book's reviews and rating; price changes refresh the book and catalog item; a transaction sync refreshes transaction and ownership/loan/receipt only after server verification.
- Optimistic UI may indicate an action is pending, but it must not claim a loan, purchase, refund, review, or permission change before the server confirms it.
- Keep form draft state while switching tabs or temporarily losing connectivity. Do not preserve passwords or reset codes beyond their form lifecycle.
- Use native responsive constraints, safe areas, keyboard avoidance, scalable text, touch targets, and explicit empty/error states. A wide admin data table becomes cards or a horizontally scrollable list on phones; bulk selection remains clear and admin-only.

Widget tests should cover enum colors/labels, width constraints, dark mode, large text, keyboard focus, dialog cancellation, loading buttons, filter reset, and list/grid switching.
