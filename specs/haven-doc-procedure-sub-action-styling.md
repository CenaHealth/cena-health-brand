# Haven doc-procedure sub-action styling — the subordinate register under a compound step

_Brand spec for the `procedure-sub-action` register — the indented, one-tier-quieter list that splits a compound procedure step (an 800+-char action wall) into a lead action line plus its ordered constituent actions. Visual treatment + rationale only; structure and CSS implementation live in [`Lab/haven-ui/packages/design-system/pattern-library/components/doc-procedure-steps.html`](../../haven-ui/packages/design-system/pattern-library/components/doc-procedure-steps.html) and [`packages/design-system/src/styles/tokens/components.css`](../../haven-ui/packages/design-system/src/styles/tokens/components.css)._

---

## 1. Context

`doc-procedure-step` teaches one action of a role SOP: a numbered chip + an action line, optionally with where/result detail rows. Some steps are genuinely compound — a single step whose action is really several ordered sub-actions crammed into one paragraph. The UConn SoT cap corpus carries these: cap-29a's mega-step ran 897 characters in one `procedure-step-action`, rendering as an unscannable wall.

The `@slot: sub-actions` register (2026-07-06) is the fix: the lead action stays in the action line, and the constituent actions render as an indented list one register below it. A reader scans the numbered step, then the sub-actions beneath — a hierarchy, not a paragraph.

The primitive earned codification in the cena-sot procedure-detail UX pass (panel finding #5, Haven Steward R2.2). Source: `workflows/ui-development/engagements/cena-sot-procedure-detail/review-haven-steward.md` § R2.2.

**Why a sub-action register vs. reshaping the whole Steps block.** Splitting the compound step is a content-model change the primitive absorbs (an optional slot); reshaping Steps into nested numbered sub-steps would (a) double the numbered register (1 → 1.1, 1.2 …), competing with the parent chip, (b) force every action-only step to declare it isn't nested, (c) over-formalize what is a readability aid, not a new instructional structure. The sub-action list is subordinate ornament, not a second numbering system.

---

## 2. Brand frame

The defining brand call: **a sub-action reads as subordinate to its parent action — one register below it, never competing with the numbered step chip.** The step chip (sand-100 ground, serif numeral) is the primary hierarchy marker on the Steps block; sub-actions must not introduce a second marker that reads as equally load-bearing.

Three brand choices follow:

- **Marker is a quiet dash, not a number or letter.** A numbered/lettered sub-list (1.1, 1.2 or a, b, c) would read as a second ordinal register competing with the parent step chip — the reader's eye would have to reconcile two numbering systems. A dash (`–`, sand-400, normal weight) is the minimum marker that says "constituent item" without asserting its own ordinal authority. The sub-actions are ordered by position, not by an explicit numeral.

- **Type is one tier below the action line, but above the detail-row register.** `procedure-step-action` is `text-base font-semibold sand-900` (the step's primary line). `procedure-step-detail` (where/result metadata) is `text-sm sand-600`. A sub-action is an *action*, not metadata — so it sits between: `text-sm sand-700`, one tier quieter than the parent action but a touch more present than a detail row. The reader reads sub-actions as things-to-do, not as annotations about the parent.

- **Indent is a modest hanging indent, aligned under the action text.** The sub-action list sits inside `procedure-step-body` (already offset past the number chip), with a small left pad + a hanging dash marker so wrapped lines align under the sub-action text, not under the marker. The indent says "these belong to the action above" by spatial containment, without a heavy rule, box, or ground-shift. Brand restraint per Principle 6: the minimum spatial cue that groups the sub-actions under their parent.

The "grew, not built" test: a step with sub-actions should read as "one action, here are its parts" — a natural hierarchy the eye descends — not as "two levels of numbered procedure" or "a step with a boxed callout." The dash marker, the between-tiers type, and the quiet hanging indent should compose into one subordinate register that clarifies the compound step without fracturing the Steps block's single numbered rhythm.

---

## 3. Backward-compatibility (a brand-adjacent contract)

The `.procedure-sub-actions` list is **empty-collapsible**: an unfilled list renders at zero height with zero margin (the top margin is `:has(.procedure-sub-action)`-gated — the compose engine leaves a comment placeholder in an unfilled slot, which defeats `:empty`, so the margin keys off a real sub-action child instead). A step that supplies no sub-actions renders byte-for-byte as it did before. This is a brand-adjacent guarantee — the register adds nothing to the visual field of a simple action-only step, so the Steps block's established rhythm is preserved for the common case and the sub-action register appears only where a compound step needs it.

---

## 4. Implementation pointers (define-once)

- **Structure (PL fragment):** [`doc-procedure-steps.html`](../../haven-ui/packages/design-system/pattern-library/components/doc-procedure-steps.html) — `doc-procedure-step`'s `@slot: sub-actions` + the `doc-procedure-sub-action` leaf.
- **CSS register:** [`components.css`](../../haven-ui/packages/design-system/src/styles/tokens/components.css) — `.procedure-sub-actions`, `.procedure-sub-actions:empty`, `.procedure-sub-action`, `.procedure-sub-action::before`.
- **Discovery:** [`COMPONENT-INDEX.md`](../../haven-ui/packages/design-system/pattern-library/COMPONENT-INDEX.md) — `doc-procedure-sub-action` row.

Changing the marker, indent depth, or type register is a brand call that lands here first, then in `components.css`.
