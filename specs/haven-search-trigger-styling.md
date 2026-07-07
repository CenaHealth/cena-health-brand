# Haven search-trigger styling — a button that reads as a search box

_Brand spec for the `search-trigger` / `search-trigger-compact` family — the visible, discoverable affordance that opens the `cmd-palette-search` corpus palette. Visual treatment + rationale only; structure and CSS implementation live in [`Lab/haven-ui/packages/design-system/pattern-library/components/complex-command-palette.html`](../../haven-ui/packages/design-system/pattern-library/components/complex-command-palette.html) and [`packages/design-system/src/styles/tokens/components.css`](../../haven-ui/packages/design-system/src/styles/tokens/components.css)._

---

## 1. Context

The corpus search is a `⌘K` command palette (`cmd-palette-search-shell` + `cmd-palette-search.js`). A modal palette needs a **visible trigger** — the shortcut alone is discoverable by almost nobody. The first slice-2 emit leaned on a `⌘ K to search` cue tucked in the rail foot and quietly wired it to open the modal on click; Aaron flagged both problems on cold-look (2026-07-06): (a) the far-bottom-left cue is obvious to ~no one, and (b) a non-button that opens a modal has no interactive visual states — it *behaves* interactive without *looking* interactive, a fresh half-state promise.

`search-trigger` is the fix: a real `<button>` styled to read as a search input, so it is unmistakably "search" and carries proper hover / focus / active states. `search-trigger-compact` is its icon-only form for chrome too tight for the full box (the mobile banner).

Source incident: cena-sot procedure-detail slice-2 cold-look, Aaron 2026-07-06.

---

## 2. Brand frame

The defining brand call: **the trigger reads as a search box, not as a generic button.** A `btn-outline` labeled "Search" is a button that *mentions* search; a field-shaped affordance with a magnifying glass and a muted placeholder *is* search at a glance. The pattern is the well-trodden DocSearch shape (Algolia, Stripe, Linear) — users recognize it instantly.

Three brand choices follow:

- **It borrows the haven input register, not the button register.** Sand-50 ground, sand-200 border, `shadow-sm`, rounded — the same treatment as `.toolbar-search-input` / `.select-haven`. This is deliberate: the affordance should read as "a place you type a search," which is the input register, even though it is mechanically a button (it opens a palette rather than accepting inline text). Reusing the input tokens means it sits in the rail as a sibling of the filter pills without introducing a new visual language.

- **The label is a muted placeholder, not button text.** "Search procedures…" in sand-500 (the placeholder register), not sand-900 button-label weight. A muted, trailing-ellipsis label reads as "type here to search," reinforcing the input reading. The magnifying glass (sand-400) leads; the `⌘K` keycap trails — advertising the shortcut *on* the affordance, so the shortcut is taught by the thing you click, not hidden in a corner.

- **Focus is the sibling chrome-trigger ring, not a teal fill.** The focus-visible state is the two-layer ring (`--color-surface-page` inset + `--color-border-focus`/teal-400) that `.surface-rail-mobile-trigger` already uses — teal as a *focus indicator*, never as chrome fill (decision-digest haven-design #1: teal is commit-only). Hover is a quiet ground-shift (sand-100) + border-darken (sand-300), the same restraint the nav uses. No elevation change, no teal ground.

The `search-trigger-compact` form drops to icon-only and mirrors `surface-rail-mobile-trigger` exactly (44px touch target, transparent ground, hover ground-shift, same focus ring) — so on mobile the search icon and the hamburger read as one family of chrome controls.

The "grew, not built" test: the trigger should read as "the search box for this site," a recognized affordance the user reaches for without instruction — not as "a button that happens to say search," and not as the mystery-clickable hint it replaces.

---

## 3. Placement (IA note — owned by the consumer, recorded here for register continuity)

- **Desktop:** `search-trigger` (full) at rail-top, directly above the role-filter pills — search (find any cap) and filter (scope the rail) sit together as the discovery pair.
- **Mobile:** `search-trigger-compact` (icon) in the `surface-banner` utility slot, beside the hamburger — always visible, since the rail (and its full trigger) collapse behind the drawer at mobile widths. `search-trigger-compact` is hidden at ≥1024px so the two forms never both show.

Aaron's placement ruling: rail-top desktop + banner mobile (2026-07-06).

---

## 4. Implementation pointers (define-once)

- **Structure (PL fragment):** [`complex-command-palette.html`](../../haven-ui/packages/design-system/pattern-library/components/complex-command-palette.html) — `search-trigger` + `search-trigger-compact`.
- **CSS register:** [`components.css`](../../haven-ui/packages/design-system/src/styles/tokens/components.css) — `.search-trigger`, `-icon`, `-label`, `-kbd`, `.search-trigger-compact`.
- **Behavior:** `cmd-palette-search.js` wires every `[data-cmd-search-open]` to open the palette.
- **Discovery:** [`COMPONENT-INDEX.md`](../../haven-ui/packages/design-system/pattern-library/COMPONENT-INDEX.md).

Changing the ground, border, label register, or focus ring is a brand call that lands here first, then in `components.css`.
