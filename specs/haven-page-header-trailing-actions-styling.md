# Haven page-header trailing-actions styling — the title + co-located actions composition

_Brand-spec for the `page-header-default-with-actions` primitive that pairs the canonical `.page-title` `<h1>` with one or more trailing action affordances (primary `commit-action`, destructive `confirm-dialog-danger` trigger, supporting `btn-outline` chrome) on the same horizontal baseline. Visual treatment + rationale only; structure and CSS implementation live in [`Lab/haven-ui/packages/design-system/pattern-library/components/typography-page-title.html`](../../haven-ui/packages/design-system/pattern-library/components/typography-page-title.html) and [`packages/design-system/src/styles/tokens/components.css`](../../haven-ui/packages/design-system/src/styles/tokens/components.css)._

---

## 1. Context

The `page-header-default-with-actions` primitive is the emit-bound variant of haven's `page-header` composition shape. It pairs the canonical `<h1 class="page-title">` (Heading/01 register — Lora 27.65px Medium on sand-900, per `DESIGN.md §Typography`) with a trailing actions slot on the same baseline. The standalone (no-actions) variant remains `page-title-default` — a bare `<h1>` for the majority of screens whose page-header is just a title. The with-actions variant earned codification when three internal-app screens (cap-59 Programs list, cap-59 Programs-edit, cap-63 Kitchen detail) called for a trailing primary action (Add program) or destructive trigger (End program / Deactivate kitchen) co-located with the page title — a Gate-4 deferral that Aaron's 2026-06-18 AUQ Q3 verdict tagged for closure before the Andrey handoff.

Per `generative-determinism.md`'s shape trigger and `haven-primitive-codification.md`'s codify-at-creation rule, the variant lands as one primitive with an `@slot: actions` accepting any bound action child, rather than a family of per-action variants. The trade is one bound id (covers primary, destructive, and mixed action sets) for one less surface to maintain.

**Why a separate variant vs. revising `page-title-default` in place.** `page-title-default` is a bare `<h1>` emit — no flex wrapper, no actions slot, no structural assumption. Roughly ten active consumer screens (login, account, patients, patient-create, patient-profile, order-edit, programs-create, kitchens, plus future patient-app surfaces) compose it as the canonical h1 emit and would break if we wrapped them in a flex `.page-header` for an empty actions slot. The sibling primitive preserves the bare-h1 emit for those consumers and gives the three trailing-action screens an opt-in upgrade path.

**Brand intent.** The variant must read as **"the page's title, with its primary co-located action"** at a glance. The h1 carries the wayfinding weight (you're on this page); the trailing action carries the primary verb available *from* this page (Add a program, End this program, Deactivate this kitchen). The two anchor opposite ends of the same baseline so a reader's eye lands on the title first, then sweeps to the action. The trailing slot is the wireframe-canonical placement for one-or-two top-level page verbs; secondary navigation and filtering live below the header in their own rows.

---

## 2. Brand frame for this variant

The variant sits inside haven's existing `.page-header` flex shell. A reader scanning an internal-app screen (Programs list, Programs edit, Kitchen detail) should see "the page's title and its primary action, on the same line, with the action visually anchored at the right" — not "a heading with floating buttons" and not "a navbar with a heading." Three brand decisions follow from that constraint:

- **The h1 keeps its Heading/01 register; the actions cluster sits adjacent at btn-md weight.** The h1 dominates the baseline by typographic weight (Lora 27.65px Medium); the trailing actions cluster at the default btn-md size (40px tall, body-02 font weight) reads as paired-but-subordinate. Pulling the actions to btn-sm would underweight the verb against the title; pulling them to btn-lg would shout. Default-weight is the load-bearing decision: actions earn their place on the title baseline but don't compete with the title for typographic weight.

- **The actions slot is right-aligned on `sm:` and stacked-below on mobile.** The `.page-header` shell already encodes the responsive shape: `flex flex-col sm:flex-row sm:items-center sm:justify-between gap-4`. On desktop (sm:+), title-left / actions-right is the canonical wireframe-spec'd layout. On mobile (default), the actions stack below the title so the title gets full inline-width and the action(s) wrap to the next row without truncating. This is the same shape `data-bulk-action-bar` and `card-header` reach for; the variant inherits the family vocabulary.

- **The actions slot accepts any bound action child — no per-action visual treatment lives here.** The slot doesn't constrain WHAT goes in (primary commit-action, destructive confirm-dialog-danger trigger, supporting btn-outline filter chrome, icon-only btn-icon). Each action child carries its own visual treatment from its own primitive (btn-primary teal for commits, btn-danger-outline for destructive triggers, btn-outline for secondary). The variant's job is the title+action composition shape; the child's job is the per-action visual weight. This matches the slot-as-extension-point convention from `data-bulk-action-bar` and `card-action-bar`.

The "grew, not built" test on this variant: a screen header containing an h1 plus one Add-program btn-primary should read as a single coherent page title with its primary verb — title-anchored-left, verb-anchored-right, both on the same baseline. If the action shifts up or down off the title's optical baseline, the composition reads as broken. If the action takes more visual weight than the title, the page reads as an action surface (toolbar) rather than a content surface (titled page). If the action gets visually swallowed by the title's typographic weight, the verb fails to be discoverable as the page's primary affordance.

---

## 3. Composition — visual treatment + rationale

The variant carries the existing `.page-header` flex shell + the `.page-title` h1 child + a `@slot: actions` wrapping the trailing buttons. The CSS family (already shipped) handles the responsive shape; the new primitive's job is to bind the title text and host the actions slot.

### 3.1 Title region — h1.page-title with data-field="title"

- **Semantic intent.** The page's name. The wayfinding anchor — a reader scanning the screen confirms they're on the right page before reaching for the action.
- **Visual treatment.** Inherits the canonical `.page-title` register from `DESIGN.md §Typography`: Lora 27.65px Medium on sand-900, `mb-0` (the page-header shell owns the bottom rhythm, not the h1).
- **Rationale.** Single source of truth — the title's visual weight is owned by the `.page-title` class; this variant just composes around it without re-deciding the type spec.

### 3.2 Actions region — .flex.gap-2 wrapping @slot: actions

- **Semantic intent.** The page's primary affordance(s). One action is the canonical case (Add program); a destructive trigger is the destructive case (End program); two actions side-by-side is the mixed case (e.g., a future patient-detail screen with Edit + Discharge).
- **Visual treatment.** Inline flex row with `gap-2` (8px between adjacent actions). No padding, no border, no background — the cluster inherits its visual weight from each action child's own primitive. The `.page-header` shell pulls the cluster to the right on sm:+ via `sm:justify-between` and stacks it below the title on mobile.
- **Rationale.** Single-source — the actions slot owns layout (flex direction, gap) but not visual treatment (color, weight, size). The slot accepts heterogeneous children; visual coherence comes from each child being a properly-built action primitive, not from the slot constraining what fits.

### 3.3 Responsive shape — title-left + actions-right on sm:; stacked on mobile

- **Semantic intent.** The page-header composition reads identically on desktop and on mobile, but the layout reflows so neither the title nor the actions truncate on a narrow viewport.
- **Visual treatment.** On `< sm` (under 640px), `.page-header` is `flex flex-col gap-4` — title above, actions below, full-width inline on each row. On `sm:+`, `.page-header` shifts to `flex-row items-center justify-between` — title flush-left, actions flush-right, vertically centered on the same baseline. The 16px gap (`gap-4`) becomes a horizontal gap between title and actions on desktop, a vertical gap between rows on mobile.
- **Rationale.** Inherited from the existing `.page-header` CSS family — no new responsive decision authored here. The variant uses the canonical shape because it IS the canonical shape; pulling to a custom breakpoint would fork the family for one variant.

---

## 4. Per-action child treatment — what goes in the @slot: actions

The variant doesn't pin a specific action child; the slot accepts any bound action. This section names the canonical fills for V0 — the patterns the three closing-Gate-4 consumers reach for — without locking the slot to only these.

### 4.1 Primary commit action — Add program / Add patient / Add kitchen

- **Primitive composed.** `commit-action-anchor-variant-navigates-and-commits` (existing bound primitive). Renders `<a href="..." class="btn-primary" data-field="label">Label</a>`.
- **Visual treatment.** Teal-700 fill (`var(--color-primary)`), white text, default btn-md size (40px tall). The button is full text — no leading icon in V0 (the wireframe-spec'd `<i class="fa-solid fa-plus"></i>` glyph is a Steward-side icon-variant iteration; lands when an icon-bound commit-action variant is authored).
- **Rationale.** Teal is reserved for state-changing commits per `DESIGN.md §Brand-taste`. Add-program IS a state-changing commit (the action creates a new program record). The anchor variant is the right shape because the action routes (navigates to /programs/new) AND commits (creates the new resource on submission of the new-program form). The label binds via `data-field="label"` so each consumer's content.json sets the verb.

### 4.2 Destructive trigger — End program / Deactivate kitchen / Discharge patient

- **Primitive composed.** `confirm-dialog-danger-outline` (new sibling bound primitive of `confirm-dialog-danger`, codified in this same Steward dispatch). Structurally identical to `confirm-dialog-danger` — same dialog overlay markup, same `hs-overlay` wiring, same five bound strings (trigger-label, title, body, cancel-label, commit-label) — but emits the trigger button as `btn-danger-outline` (outline ring + red text) rather than `btn-danger` (filled red). The dialog's commit button stays `btn-danger` (filled) because the COMMIT is the destructive moment; the trigger is the affordance-discovery moment that earns visual restraint.
- **Visual treatment.** Trigger button: `btn-danger-outline` (red-500 ring 1px, red-700 text, transparent background, hover ground red-50). Sits adjacent to the page title on the same baseline. The visual register reads as "this destructive verb is available" rather than "this destructive verb is staged for execution" — the filled-red weight is reserved for the dialog's commit button, where the user has actively chosen to perform the destructive action.
- **Rationale.** Two-tier visual weight on the destructive flow is the brand decision: outline trigger (available) → dialog opens → filled commit (executing). A filled-red button adjacent to a page title reads as a warning surface (caution) rather than a content surface with an available destructive verb (intent). The outline trigger respects the page-as-content read while preserving full destructive semantics in the dialog body and commit button. The bottom-card workaround (dedicated "End program" card with a section-lede and a `btn-danger` trigger inside) was the V0 fallback when `page-title-default` had no trailing actions slot; with the slot now codified AND the outline trigger variant authored alongside, the workaround collapses and the destructive verb moves to its wireframe-canonical placement at the correct visual weight.

### 4.3 Two-action mixed case — primary + supporting

- **Pattern.** A primary commit action plus a supporting filter / chrome action (`btn-outline` Filter, btn-icon search). The wireframe-spec'd shape on patient-list and future programs-list (post-V0) carries this composition.
- **V0 disposition.** Not exercised by the three closing-Gate-4 screens; lands on the surface that needs it. The slot already accepts the composition (`<button class="btn-outline">Filter</button>` + `<a class="btn-primary">Add Patient</a>` adjacent inside the slot, separated by the existing `gap-2`).

---

## 5. Cross-cutting brand decisions

These decisions apply to the variant regardless of which action(s) compose into the slot.

### 5.1 Title gets `mb-0`; .page-header shell owns vertical rhythm via `mb-6`

The `.page-title` and `<h1>` inherit `mb-0` inside the `.page-header` shell because the shell carries `mb-6` (24px below the header before the next content row). Two bottom margins stacked would double the rhythm; one is correct. This is the existing `.page-header` family contract — no new decision authored here.

### 5.2 Actions slot inherits the family's `gap-2` (8px between adjacent actions)

When two or more action children compose into the slot, they sit 8px apart — the canonical `data-bulk-action-bar` and `card-action-bar` family gap. Pulling to `gap-3` (12px) would loosen the cluster; pulling to `gap-1` (4px) would tighten it past the family rhythm. Default is the family default.

### 5.3 Actions slot does NOT support background, border, or padding overrides

The slot is layout-only — no `.page-header-actions-container` wrapper class, no slot-level background or border. The cluster's visual weight comes entirely from each action child. If a future consumer needs to set the cluster off (e.g., a sticky-on-scroll page header with a colored action region), that's a separate primitive (likely `page-header-sticky-with-actions` or similar), not a modifier on this variant.

### 5.4 The h1 binds `data-field="title"`; the actions slot binds via @slot: actions

The variant's content contract is dead simple: one bound text field (the title) and one bound slot (the actions). Consumers can't mis-bind the slot — anything they compose into the actions array renders in the slot, in order. The contract scales to one or many action children without growing per-action binding ceremony.

---

## 6. Quality-test addendum (per haven-pl-qa.md)

The variant passes the standard pattern-library QA checks (semantic class usage, no inline styles, FA Pro icon usage, no Material Icons, dark-mode parity inherited from .page-header + .page-title + each action child's primitive). The variant-specific checks that earn rigor at codification time:

- **Title baseline-alignment with actions on desktop.** The h1 (Lora 27.65px) and the action cluster (btn-md, 40px tall) sit on a shared baseline at `sm:items-center`. Verify visually: the h1's optical center and the action button's vertical center align. If the actions optical-shift up or down by more than ~2px, the composition reads as misaligned.
- **Mobile reflow correctness.** Under 640px, the title and actions stack with 16px gap between rows. The actions row stays full-width inline-flex (so two adjacent actions sit side-by-side, not stacked vertically). If the actions wrap to multiple rows on narrow mobile, that's a content-layer concern (too many actions for one header) not a primitive concern.
- **Destructive-trigger visual weight when adjacent to title.** The `btn-danger` filled trigger reads heavier than the title's Lora weight. V0 ships this way (per the existing `confirm-dialog-danger` primitive); the btn-danger-outline variant codification will lighten it. The current ratio is V0-accepted; revisit when the outline variant lands.
- **No accidental `.page-header` style override.** The variant uses the existing `.page-header` class as-is. If a consumer adds a sibling class (e.g., `.page-header-sticky`) to compose into the same shell, that's a separate decision that doesn't ship with this variant.

---

## 7. What's NOT in this variant — boundary statements

- **No leading icon on the primary commit action.** The wireframe specs `<i class="fa-solid fa-plus"></i>` before "Add program"; V0 emits without the icon because the `commit-action-anchor-variant-navigates-and-commits` primitive doesn't currently emit a leading icon. An icon-bound commit-action variant lands when authored; until then, the text-only label is V0-accepted (functionally correct; visually less affordant than the wireframe's icon-leading shape).
- **The outline-trigger variant of `confirm-dialog-danger` does NOT replace the filled variant.** Both ship: `confirm-dialog-danger` (filled trigger) for cases where the destructive verb earns full visual weight at the trigger moment (e.g., a stand-alone Delete-record button in a card body); `confirm-dialog-danger-outline` (outline trigger) for cases where the destructive verb sits adjacent to other content (e.g., the page-header trailing-action context this variant was built for). The two variants share the dialog markup verbatim; only the trigger button's class set differs.
- **No sticky-on-scroll behavior.** The variant emits a static header; sticky composition is a separate primitive when a real consumer needs it.
- **No nested action-group (button-group) composition.** The slot accepts adjacent action children with `gap-2`. If a consumer needs a tight button-group (no gap, shared border), that composes inside the slot as its own primitive (e.g., a future `btn-group`); the slot doesn't constrain it.
- **No active-state visual treatment for the page name.** The page-header is a static header — the title doesn't carry "you're here" affordances (those live in the navbar / sidebar where they belong).

---

## 8. Variant codification trail

- **2026-06-18 (Steward Dispatch 4.8c).** Codified `page-header-default-with-actions` as a sibling bound primitive to `page-title-default`, AND `confirm-dialog-danger-outline` as a sibling bound primitive to `confirm-dialog-danger`. The two primitives ship together because the with-actions header earns its keep only when the destructive-trigger composition has the right visual weight (outline at the trigger, filled at the commit). Closes Gate-4 deferrals on `programs.screen.json` (Add-program trailing action via `commit-action-anchor-variant-navigates-and-commits`), `programs-edit.screen.json` (End-program trailing destructive trigger via `confirm-dialog-danger-outline`), `kitchen-detail.screen.json` (Deactivate-kitchen trailing destructive trigger via `confirm-dialog-danger-outline`). Bottom-card destructive composition removed from the two destructive-action screens; the trigger now sits in the header, the dialog overlay markup composes inside the outline-variant primitive alongside (position: fixed + hidden, position-independent visually). Sibling primitive path chosen over in-place revision of `page-title-default` to preserve the bare-h1 emit for ~10 existing consumers.
