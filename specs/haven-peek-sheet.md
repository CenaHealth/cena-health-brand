# Haven peek-sheet styling — persistent in-flow order-summary container

_Brand-spec for the `peek-sheet` primitive — a mobile-only persistent, in-flow, accordion-collapsible bottom container introduced in the 2026-06-15 haven-ui slice for the cena-apps patient-app menu screen. Visual treatment + rationale only; structure and CSS implementation live in `Lab/haven-ui/packages/design-system/pattern-library/components/peek-sheet.html` and `Lab/haven-ui/packages/design-system/src/styles/tokens/components.css`._

---

## 1. Context

The patient-app menu screen merges the meal cart INTO the menu surface as an **always-present order summary**, replacing the prior "menu → cart → submit" linear flow. Aaron and Andrey aligned on this UX shift as the v1 cena-apps direction.

Desktop hosts the order summary in the right rail (cart-panel composed directly). Mobile cannot afford a permanent rail, so the order summary lives at the viewport bottom in two equally-valid states:

- `is-peeked` — collapsed bar showing the live cart count, a compact budget meter, and a "Review & send" cue with up-chevron. Persistent. Always visible. Does not block content above.
- `is-expanded` — body slot reveals the full cart-panel; tap-header (or drag-handle-down) collapses back. Body scrolls within an 85vh envelope, mirroring overlay-bottom-sheet's cap so the two patterns share a viewport rhythm.

The peek-sheet is a **sibling primitive** to overlay-bottom-sheet, not a variant (Steward verdict 2026-06-15). The structural delta is categorical: persistent vs. modal, two stable states vs. open/closed, no backdrop, no Preline coupling, no focus trap. The two primitives can coexist on the same screen (a confirm dialog in overlay-bottom-sheet can open over a peek-sheet in either of its states because peek-sheet sits at z-40 deliberately below bottom-sheet-panel's z-[80]).

---

## 2. Brand frame for this primitive

The peek-sheet is structurally a **persistent feedback ribbon** — the user sees what they have in their cart and what remains in their budget without leaving the menu surface. The brand decisions live at four layers:

- **Surface vocabulary.** Container surface is `bg-surface-card` (the same warm white used by cart-panel, card, list-group) with a `border-t border-sand-200` (NOT a full bordered card — the peek-sheet rides at the viewport bottom, so only the top edge needs the structural rule). The top corners use `rounded-t-2xl` (16px) matching bottom-sheet-panel's geometry exactly — two bottom-anchored mobile containers sharing one silhouette so the user reads them as the same family even when their behaviors differ.
- **Handle vocabulary.** The drag handle reuses bottom-sheet-handle's 36×4 sand-300 pill geometry. Identical pixel dimensions. The reuse is deliberate: a user who has learned to drag a bottom-sheet down by its handle should not have to relearn the gesture on peek-sheet. Cross-primitive gesture language is a brand discipline at the same layer as cross-primitive border-vocabulary in the list-group family.
- **Cue color register.** The "Review & send" cue uses `amber-700` (NOT primary-teal). Primary-teal is reserved for the actual irreversible commit — `cart-submit` inside the expanded body, "Send order to kitchen". The peek cue **names the next step** without claiming the commit moment; amber is the family's "warm-and-supportive" register (same hue as `cart-helper.is-gate`'s gate-copy). The patient is being invited toward the commit, not being told to commit. The chevron-up icon at `amber-600` (one stop lighter than the text) matches the cart-helper-icon discipline of one-stop-lighter affordance icons.
- **Count + meter rhythm.** The peek state's left content is the count (`sand-800` semibold, no truncation) flowed against the compact budget-meter (which expands to fill the row). The count IS the row's identity ("3 items"); the meter IS the row's state (where you are in your budget). The cue is the row's affordance (what to do next). Reading order left-to-right is `[identity] → [state] → [affordance]` — the same left-to-right discipline list-group uses for `[icon: category] → [label: what] → [trailing: status or action]`.

Brand restraint per Principle 6: the peek state stays quiet by default. No hue saturation on the container, no shadow except the standard `shadow-lg` that lifts the sheet off the page (matches bottom-sheet-panel). The only hue in the peek state is the amber cue, which earns its place because the cue is the row's single affordance and warm-restrained amber is the family's affordance register.

The "grew, not built" test on this primitive: a menu screen with a peek-sheet at the bottom and meal-option-cards above should feel like the cards continue under the sheet. If the peek reads as a widget pasted onto the page (banded border, shadow line, color shift), the surface or the border has compressed wrong. The `bg-surface-card` + `border-t border-sand-200` + `shadow-lg` combination is what produces the "the sheet is a fold in the same paper" reading.

---

## 3. State treatments — visual + rationale

### 3.1 `peek-sheet.is-peeked` — collapsed default

- **Semantic intent.** The mobile resting state. The user is browsing the menu; the peek-sheet shows them where their order stands without interrupting browse flow.
- **Visual treatment.** Container: fixed at `bottom: 0`, `inset-x-0`, `z-40`, `max-height: 4rem` (64px — enough for the 44px touch floor + 10px of internal padding on each end). Background `bg-surface-card`, top border `border-t border-sand-200`, `shadow-lg` lifts it off the page, `rounded-t-2xl` on the top corners. Drag handle hidden (the .peek-sheet-peek button IS the expand trigger; the handle's role is the drag-down-to-collapse affordance when expanded). Row: `flex items-center justify-between gap-3 px-4 py-3` — same 16px-horizontal / 12px-vertical rhythm as list-group-item. Count: `text-sm font-medium text-sand-800`. Meter: fills the remaining flex width. Cue: right-aligned `amber-700` text with `amber-600` chevron-up.
- **Rationale.** 64px is the minimum height that admits a 44px tap target with breathing room; smaller compresses the meter unreadably, larger eats menu real-estate the user is trying to browse. The `flex-1` on the meter (which itself is a slim `--progress-thickness-md` bar at 8px) gives the meter ~60-70% of the row's horizontal width — enough to read the ratio at a glance, not so much that the count or cue get crowded. The cue's `whitespace-nowrap` is non-negotiable: a wrapped cue ("Review & / send") breaks the row's affordance reading. If the screen is narrow enough to wrap the cue, the meter compresses first; if the screen is too narrow even for that, the consumer should shorten the cue copy (e.g. "Send" — bindable via `data-field="cue-label"`).
- **Quality-test notes.** Quiet-mode: scrolling the menu with the peek visible should feel like the menu cards slide under a fold, not like a chrome bar sits on top. Grew-not-built: the top corners' 16px radius matches bottom-sheet-panel exactly — if a user opens a bottom-sheet over the peek, the two top corners should look like they came out of the same SVG. Touch-target floor: tapping anywhere in the peek-row expands; tapping the cue specifically does NOT have a different behavior (the whole row IS the button).

### 3.2 `peek-sheet.is-expanded` — body slot revealed

- **Semantic intent.** The user has chosen to review their order in full. The body slot reveals — v1 consumer is cart-panel, future consumers compose any haven primitive(s) inside the slot. The user can return to browsing by tapping the header chevron-down or dragging the handle down.
- **Visual treatment.** Container: same fixed bottom positioning, but `max-height: 85vh` (mirroring bottom-sheet-panel's `max-h-[85vh]`). Drag handle: visible at top, 12px-top / 4px-bottom centered, 36×4 sand-300 pill (identical to bottom-sheet-handle). Header row: `flex items-center justify-between gap-3 px-4 py-3` with `border-b border-sand-200` separating header from body. Title: `text-base font-semibold text-sand-800` (default "Your order"; bindable via `data-field="title"`). Chevron-down: `sand-500` neutral, smaller-weight than the amber cue chevron because the collapse affordance is secondary to the peek cue's primary "review & send" invitation. Body: `flex-1 overflow-y-auto px-4 py-4` — matches bottom-sheet-body padding, scrolls within the 85vh envelope.
- **Rationale.** 85vh is the family's "as much room as we can take without obscuring the page header context" cap — same number bottom-sheet-panel uses, so a user has a consistent vertical-space expectation between the two patterns. The 250ms ease-out max-height transition between peeked (4rem) and expanded (85vh) is **fast enough** to feel responsive and **slow enough** to read as accordion-expansion, not modal-overlay. A transition shorter than 200ms reads as snap and breaks the persistent-in-flow framing; longer than 400ms reads as sluggish. Header chevron is `sand-500` because the collapse moment is a secondary affordance — the primary affordance in the expanded state is the cart-submit button at the bottom of the body slot (`btn-primary` teal). Hierarchy: primary commit → cart-submit (teal); secondary affordance → header chevron (sand); tertiary gesture → drag handle (sand-300).
- **Quality-test notes.** Quiet-mode: an expanded peek-sheet showing the cart-panel should read as one continuous document — peek-sheet's `bg-surface-card` matches cart-panel's `bg-surface-card`, peek-sheet-header's `border-b border-sand-200` rhymes with cart-panel-header's `border-b border-sand-200`. The user shouldn't feel like there are two containers (peek-sheet wrapping cart-panel); they should feel like one expanding container showing the order. Grew-not-built: the handle's 36×4 sand-300 pill should look indistinguishable from bottom-sheet-handle's pill at the same magnification — same pixel dimensions, same border-radius (pill), same color.

### 3.3 State transition — the peek ↔ expanded motion

- **Visual treatment.** `transition: max-height 250ms ease-out` on the `.peek-sheet` container. No other property animates explicitly; the children appear/disappear via `display: none` on the inactive state's elements (handled by `.peek-sheet.is-peeked .peek-sheet-handle / .peek-sheet-header / .peek-sheet-body { display: none }` and `.peek-sheet.is-expanded .peek-sheet-peek { display: none }`).
- **Rationale.** Max-height transition is the cheapest way to get accordion-feel without paying for explicit height calculations on resize. The cost: a height-collapsed-from-fixed-value-to-fixed-value animation is rendered cleanly by every modern browser without layout thrash. `display: none` swap on the children is deliberate — the inactive state's DOM stays in place (good for screen-reader landmark stability), but display:none keeps it out of the accessibility tree during the inactive period. No `opacity` animation because the inactive children are gone, not faded — fading them would imply they remain visible during transition, which conflicts with the "the other state is gone" model.
- **Quality-test notes.** Reduced-motion: a `@media (prefers-reduced-motion: reduce)` override should set `transition: none` so the state change happens instantly. (Not authored in v1; tracked as immediate follow-up — see Section 6.) Mid-transition tap: a user tapping the cue while the sheet is still expanding should not re-trigger the expansion. The peek-sheet.js state-toggle is idempotent (calling expand() while expanded is a no-op), so this is handled.

---

## 4. Cross-primitive composition rules

- **Hosting cart-panel.** The peek-sheet body slot hosts cart-panel directly — cart-panel keeps its own header, status pill, body, helpers, and submit. peek-sheet does NOT wrap cart-panel's chrome; the slot is a transparent compose surface. When the cart-panel is in its `.is-locked` post-submit variant (showing "Sent to kitchen"), it composes inside peek-sheet just the same — the locked variant is a cart-panel concern, not a peek-sheet concern.
- **Hosting the compact budget-meter.** The peek-sheet peek slot hosts `.budget-meter.is-compact` directly. The compact variant is a budget-meter modifier (not a sibling primitive — Steward verdict 2026-06-15). The peek-sheet's `.peek-sheet-peek-summary .budget-meter.is-compact { flex: 1; min-width: 0 }` rule is what gives the meter the row's remaining width without crowding count or cue. The compact meter MUST carry its own `aria-label` because the visible label is hidden — the consumer's manifest binds this label at render time.
- **Coexistence with overlay-bottom-sheet.** A confirm dialog or action sheet (overlay-bottom-sheet) can open over a peek-sheet in either state because peek-sheet sits at `z-40` and bottom-sheet-panel at `z-[80]`. When the overlay-bottom-sheet opens, its backdrop covers the peek-sheet (the peek remains in the DOM but is visually obscured + cannot be focused while the overlay's focus trap is active). When the overlay-bottom-sheet closes, the peek-sheet remains in its prior state — the overlay does not collapse or expand the peek.
- **Coexistence with the rest of the page.** The peek-sheet is `position: fixed` (or `position: absolute` inside a `.relative` demo-context ancestor). It does NOT contribute to page document flow. Consumer apps should add bottom padding to the menu content equal to the peek's collapsed height (64px) so the last menu item is not visually obscured by the peek. This padding stays at 64px regardless of the peek's state because the user can always collapse the expanded peek back to the peeked state.

---

## 5. Quality test — does this feel like Haven

For each test, a yes/no answer; ship requires yes on all four.

| Test | Pass condition |
|---|---|
| The peek-sheet at viewport bottom reads as a fold in the menu page, not a chrome bar pasted on top. | The `bg-surface-card` + `border-t border-sand-200` + `shadow-lg` combination produces a continuous-surface reading; the menu cards above appear to slide under it during scroll. |
| The cue's amber-700 register reads as "warm invitation," not "warning." | Compared side-by-side against `cart-helper.is-gate` (also amber-700) the two should read as the same supportive-warm register, not as two different states. |
| The expanded peek-sheet hosting cart-panel reads as one document, not two stacked containers. | Both surfaces are `bg-surface-card`; the peek-sheet-header's bottom border matches the cart-panel-header's bottom border weight + color. No visual seam between peek-sheet and cart-panel. |
| The drag-handle gesture transfers cleanly from bottom-sheet to peek-sheet. | A user who has used the bottom-sheet drag-down-to-close gesture should be able to drag-down-to-collapse the peek-sheet without instruction. The handle's 36×4 sand-300 pill is identical. |

---

## 6. Tracked follow-ups

- **Reduced-motion override.** v1 ships with the 250ms ease-out max-height transition unconditionally. A `@media (prefers-reduced-motion: reduce)` rule setting `transition: none` should land in a follow-up slice. Tracked in haven-peek-sheet.md as a known gap.
- **Mobile-tablet boundary.** v1 hides `.peek-sheet` at `≥ md` (768px). If the patient-app's tablet viewport experience needs the peek pattern (rather than reverting to the desktop right-rail), the boundary will move and a `is-tablet` modifier may earn its keep. Not authored speculatively; earns it when a tablet-only consumer surfaces.
- **Dark mode visual verification.** All CSS includes `dark:` variants. Cross-checking the dark-mode peek state on real device (especially the amber-700 → amber-300 cue mapping for contrast on dark surface) is a Q3 follow-up.
- **Cena-apps consumer wiring.** Thread C (cena-apps) owns the menu-screen wireframe + screen.json manifest authoring that composes peek-sheet on the patient-app. Skip-decision artifact for consumer-surface deferral lives at `~/.claude/plans/scratch/skip-2026-06-15-peek-sheet-consumer-surface.md` per the haven-primitive-codification rule's operational-skip path.
