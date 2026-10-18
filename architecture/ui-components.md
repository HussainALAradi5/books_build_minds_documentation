# Tabs, workflow guides, and animation

The client uses Chakra UI for all three shared components. No additional UI or animation package is required.

| Component | Contract | State and behavior |
| --- | --- | --- |
| `Components/custom/tabs/GenericTabs` | `GenericTabsProps`, `TabItem` | `useGenericTabs` selects a valid enabled tab; Chakra manages keyboard navigation, focus, and ARIA relationships. |
| `Components/custom/workflow/GenericWorkflowGuide` | `GenericWorkflowGuideProps`, `WorkflowStep` | `useWorkflowGuide` controls open state, bounded Previous/Next navigation, and resets on reopening. |
| `Components/custom/animation/GenericAnimation` | `GenericAnimationProps`, `AnimationPreset` | Uses shared Chakra theme keyframes and disables animation when the user requests reduced motion. |

Each interface and enum has its own file in the corresponding `interfaces/ui`, `props/custom`, or `enums/ui` subfolder. Guide instructions live in `constants/workflow`, separate from rendered components. Animation names and keyframes live in `constants/ui/animations`.

## Composition

- Profile: `profile/tabs/ProfileTabs` combines Account, Loans, and Activity. Each tab has a separate component; account display logic is in `useProfileOverview`, and loan/activity state stays in the existing feature hooks.
- Administration: `admin/tabs/AdminTabs` combines Inventory, Add book, Catalog, and Access requests.
- Access management: `admin/access/tabs/AccessTabs` combines Permissions, Roles, Groups, and Users under the existing access provider.
- Book detail: `books/BookInformationTabs` places Book details and a combined read only Author & publisher tab beside the cover. `books/BookInfoCard` uses `GenericCard` to group price, the server-backed description, publication facts, condition, availability, and book actions. `BookActionBar` keeps purchase and PDF controls in one row when space permits; QR and barcode open in a dialog. On mobile, details precede the shorter cover. Reviews remain below the header. Administrators open a compact price-only edit dialog from Book details. The small library workflow guide is in the main navbar.

`GenericTabs` uses `lazyMount` and keeps visited panels mounted. A panel first mounts when visited; its form values and filters survive switching. Hidden panels use Chakra's default display hiding, so their existing effects remain active. Feature hooks own data refresh; switching tabs does not discard their state. Tab labels scroll horizontally on small screens.

Use stable, unique `TabItem.value` strings. Provide a descriptive tab-list label. Guide steps likewise need stable, unique IDs. Do not pass secrets or unauthorized server data into a hidden panel: tabs control presentation, not access.

## Guide behavior

The guide is an instructional dialog, not a transaction wizard. Next never buys, returns, edits, or submits anything. Users perform those operations through the existing validated forms and API services. Empty guides have a disabled trigger. Chakra traps dialog focus, supports Escape dismissal, and restores focus to the trigger. Step changes are announced through a polite live region.

## Verification

Browser checks cover lazy mounting, arrow-key navigation, retained loan filters and unsaved book fields, first/last guide boundaries, reopen reset, Escape/focus return, reduced motion, mobile overflow, and all administration tabs. See the [screenshots](https://github.com/HussainALAradi5/books_build_minds_client_side/blob/main/docs/screenshots/tabs/README.md) and [security boundaries](../security/README.md).
