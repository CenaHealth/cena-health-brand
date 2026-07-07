# Haven doc-return-link styling — the quiet return affordance at the foot of a document

_Brand spec for the `doc-return-link` register — the muted bottom-of-page "return to parent" anchor on long Document-district surfaces (cena-sot cap-detail pages, SoP bodies, and future doc-class reading surfaces). Visual treatment + rationale only; structure and CSS implementation live in [`Lab/haven-ui/packages/design-system/pattern-library/components/doc-return-link.html`](../../haven-ui/packages/design-system/pattern-library/components/doc-return-link.html) and [`packages/design-system/src/styles/tokens/components.css`](../../haven-ui/packages/design-system/src/styles/tokens/components.css)._

---

## 1. Context

A long cena-sot cap-detail page (one of 33 staff procedure pages) opens with a top breadcrumb — the location chain `nav-breadcrumb` composes — and closes with a return affordance: a single "Back to {parent stage}" anchor at the foot of the reading column, so a reader who has scrolled the whole procedure can step back up without scrolling to the top.

The failure this primitive corrects: that bottom-of-page return was composing the **same** `nav-breadcrumb` / `nav-breadcrumb-crumb` primitive as the top breadcrumb. Two structurally-identical breadcrumb affordances on one page — one at top, one at bottom — read as accidental duplication, not as two distinct wayfinding moments. The bottom one is not a location chain; it is a single, quiet "return" gesture, and it wanted its own register.

The primitive earned codification in the cena-sot cap-page family pass (Haven Steward dispatch, 2026-07-07). Direction was set by Aaron; this spec records the register calibration.

---

## 2. Brand frame

The defining brand call: **the return link is a quiet, intentional "return" gesture — muted, sand-family, never teal — visually distinct from the top breadcrumb's inline crumb-chain.** It sits at the calm end of the page and must not compete with the primary teal commitment register or read as a second breadcrumb.

Three brand choices follow:

- **Muted sand register, hover stays in the sand family.** The link is `text-sand-600` at rest, `hover:text-sand-900`, `font-medium`, `text-sm` — derived directly from the established `file-list-add` icon+label link canon. This is the deliberate differentiator from `nav-breadcrumb-crumb`, which hovers toward `primary-600` (teal). A breadcrumb crumb's teal hover implies "this is a link in a location chain you traverse"; the return link's sand-900 hover is a **figure-darken** — the same Quiet Ground language the active-nav state uses (warm ground, figure-darken, never a teal fill). The return link reads as "step back", not "navigate the chain". Teal is reserved for state-changing commitments (Decision digest #1); a wayfinding return is not a commitment, so it never carries teal.

- **It is navigation, so it is an anchor, never a button.** The return link is an `<a href>`. A `btn-outline` was rejected: a button implies an action performed in place, and the outline register would assert more visual weight than a foot-of-page return earns. An anchor with a muted text register is the honest shape for "go to the parent page".

- **The leading arrow icon owns the "←"; the label carries only words.** A single FA Pro v7 `fa-arrow-left-long` (solid) sits before the label, `aria-hidden="true"` (decorative — the visible label is the accessible name). Because the icon carries the directional "back" signal, the bound `label` text is words only — "Back to 1. Referral & intake", never "← Back to …". One directional cue, not two. `fa-arrow-left-long` (a long horizontal arrow) reads as "go back" more clearly than `fa-chevron-left` (which reads as pagination/prev); the return is a destination move, not a step through a sequence.

The "grew, not built" test: at the foot of a cap-detail page the return link should read as "quietly step back up to the parent stage" — a calm, single gesture the eye finds without it demanding attention — not as "a second breadcrumb" or "a button to click". The muted sand register, the anchor shape, and the single leading arrow should compose into one quiet return affordance that closes the reading surface without competing with its content or its commitment register.

---

## 3. Accessibility (a brand-adjacent contract)

- The decorative icon is `aria-hidden="true"`; the visible label is the accessible name. No `aria-label` is added — the "Back to {stage}" text is sufficient, matching the canon icon+label links (`file-list-add`, `accordion-toggle`), which add none.
- Focus is a visible `sand-700` ring (`focus-visible:ring-2 ring-sand-700 ring-offset-1`, canon focus register) — the muted rest state must not cost keyboard users a visible focus indicator.

---

## 4. Implementation pointers (define-once)

- **Structure (PL fragment):** [`doc-return-link.html`](../../haven-ui/packages/design-system/pattern-library/components/doc-return-link.html) — the bound `<a>` with the `{href}` attribute token, the decorative `fa-arrow-left-long` icon, and the `<span data-field="label">` leaf.
- **CSS register:** [`components.css`](../../haven-ui/packages/design-system/src/styles/tokens/components.css) — `.doc-return-link`, `.doc-return-link:focus-visible` (co-located directly after the `.breadcrumb` block so the register differentiation is readable side by side).
- **Discovery:** [`COMPONENT-INDEX.md`](../../haven-ui/packages/design-system/pattern-library/COMPONENT-INDEX.md) — `doc-return-link` row (generated).

Changing the hover target, icon, or register is a brand call that lands here first, then in `components.css`.
