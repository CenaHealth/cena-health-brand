# Haven derived-override field styling — the derived-with-override input family

_Brand-spec for the `field-row-derived-override` primitive that pairs an editable input with a state-bound derived-value helper, an in-input reset action, and an adjacent Save button. Visual treatment + rationale only; structure and CSS implementation live in [`Lab/haven-ui/packages/design-system/pattern-library/components/form-derived-override.html`](../../haven-ui/packages/design-system/pattern-library/components/form-derived-override.html) and [`packages/design-system/src/styles/tokens/components.css`](../../haven-ui/packages/design-system/src/styles/tokens/components.css)._

---

## 1. Context

The `field-row-derived-override` primitive is a variant of haven's `field-row` family — it adds three new slots (a derived-from helper, an in-input reset affordance, an adjacent Save action) to the proven label + input shell. The variant earned codification when the cena-apps internal-app Meals catalog (cap-34) shipped V0 with override as plain `(none)` text and the wireframe-named interactive shape deferred, then the follow-on Kitchen detail wireframe (cap-63) re-surfaced the same composition. Per `generative-determinism.md`'s shape trigger and `haven-primitive-codification.md`'s codify-at-creation rule, the primitive lands now rather than letting consumer surfaces re-derive it.

**Why a separate variant vs. a one-off composition.** The pattern is "field whose unset value is derived; operator can override; reset returns to derived; Save commits because server-side recomputation may follow." That shape recurs across cena-apps' staff surfaces (meals override, future program-level fee overrides, future per-patient meal-plan custom calorie targets) and is structurally distinct from the family's existing variants: `field-row-prefix` / `field-row-suffix` carry units, not derivation; `field-row-help` shows static helper text, not state-bound derived-from rationale; `field-row-error` shows commit-failure state, not pre-edit derivation context. Codifying as a named variant means future surfaces inherit the helper/reset/save composition + the state vocabulary without re-deriving.

**Brand intent.** The variant must read as **"this field has a default that came from somewhere else; you can override it"** at a glance, without the affordances feeling like new chrome. Every visual decision below serves that one read.

---

## 2. Brand frame for this variant

The variant sits inside haven's existing `field-row` family — it cannot earn new chrome that other field-rows don't have, because the staff-facing surfaces (kitchen detail, program edit, future admin screens) compose this row alongside `field-row-default` / `field-row-prefix` / `field-row-suffix` in the same card. A reader scrolling a kitchen-info form should see "a list of editable fields, one of which has a derived default + reset" — not "a list of editable fields, one of which looks like a different component."

Three brand decisions follow from that constraint:

- **The derived-from helper inherits `field-help`'s visual register.** Same sand-600 text-xs, same `mt-1` rhythm, same gap-1 to the leading icon. The only structural addition is the leading `fa-circle-info` icon — it signals "this is a system-derived value, not your input." Pulling the helper to a warmer color or heavier weight would let the helper compete with the label; staying in `field-help`'s register lets the row read as a field-row first and a derived-override second.

- **The in-input reset is a sand-neutral chrome affordance, not a teal punctuation.** Cena reserves teal for state-changing commits per `DESIGN.md §Brand-taste`. Reset clears the operator's override and returns to the derived default — that IS a state change, but it's a state *un-do*, not a state *commit*. Treating reset as teal would conflate "I'm setting this value" with "I'm clearing what I set" and make the row read as two commit affordances side-by-side. The reset stays sand-600 (matches `list-group-item-icon` weight, matches `field-help` text) and earns its presence through position (in-input, not external) and hover surface shift (sand-100 ground) rather than hue. The Save button's teal is the row's one teal moment, and it's reserved for the actual commit.

- **The adjacent Save is `btn-primary` teal, sized for the row.** Save IS a state-changing commit (the override may trigger server-side recomputation of derived sibling fields, per the cap-41 spec). The brand's primary teal (`color-primary` / teal-700) earns its place here exactly because this is the row's one commit moment. The button sits sibling to the input-group rather than nested inside, so the input-group keeps its standalone rhythm with other field-rows in the same form. In a table cell context (the Meals catalog Override column), the consumer can opt into a btn-sm variant to fit the row height; outside table contexts the default btn-primary footprint is correct.

The "grew, not built" test on this variant: a kitchen-info card containing 4 `field-row-default` rows and 1 `field-row-derived-override` row should read as a single coherent form. If the derived-override row visually competes with the default rows for attention, the helper has surfaced too loud or the Save button has been pulled inline. The Save's teal punctuation should read as "this row has a commit pending" not as "this row is different."

---

## 3. States — visual treatment + rationale

The row carries `data-state="empty|dirty|saved|error"`. The CSS family handles the visual transitions between states; the consumer's renderer is responsible for setting `data-state` based on input value + commit lifecycle.

### 3.1 Empty state — derived value shown as helper

- **Semantic intent.** No override is set. The row displays the derived value + rationale below the input as helper text. The operator reads "this field's value is currently $12.40, derived from kitchen price + delivery fee + markup; if you don't override, this is what it will be."
- **Visual treatment.** Input renders empty (placeholder shows `0.00`). Helper line below: `text-xs text-sand-600 mt-1 flex items-center gap-1` with a leading `fa-circle-info` icon. Helper copy structure: **`Derived: ${value} from ${source description}`** — the literal word "Derived:" is the affordance signal (renders consistently across consumer surfaces); the value and the source description come from the consumer's data binding. Reset button hidden. Save button hidden. Dark mode: helper text shifts to `sand-300` (matches `.field-help` dark treatment).
- **Rationale.** The empty state is the row's resting register, and it must read as "informational, not action-pending." Suppressing the reset and Save affordances when there's nothing to reset or save protects that read — affordances should appear only when they're actionable, not as always-on chrome. The `fa-circle-info` icon's sand-600 weight matches the helper text exactly so the icon recedes into the line rather than punctuating it; the icon is wayfinding ("this line is a system-derived note") not decoration. The leading "Derived:" word is the load-bearing brand choice: a future operator scanning a column of unfamiliar fields needs the word, not just an info icon, to anchor the read. Localization note: "Derived" should translate per locale; the brand spec asserts the *visual* register (sand-600 text-xs, leading info icon, gap-1 layout), not the literal English string.
- **Quality-test notes.** Quiet-mode: a meals catalog with 8 rows in empty state reads as a column of small italic-feeling info lines — present but recessed, with the inputs themselves carrying the primary read. Grew-not-built: the helper's `mt-1` rhythm matches `.field-help` exactly, so a `field-row-derived-override` next to a `field-row-help` in the same form reads as the same vocabulary.

### 3.2 Dirty state — operator entered override; Save active

- **Semantic intent.** The operator has typed a value into the input. The override is staged but not committed. Save becomes the row's primary action; reset stays available for "I changed my mind, return to derived."
- **Visual treatment.** Input shows the operator's value. Helper line **hidden** (CSS rule: `data-state="dirty" .field-derived-helper { display: none }`). Reset button surfaces at the right edge of the input, absolutely positioned (`absolute top-1/2 -translate-y-1/2 end-2`), 28×28px, sand-600 icon (`fa-rotate-left`), `rounded-md` background that shifts to `sand-100` on hover. Save button surfaces adjacent to the input-group (sibling, not nested), composes `btn-primary` (teal-700 fill, white text). Input gains `pe-9` (right padding) to keep digits from sliding under the reset button. Dark mode mirrors: reset's hover surface shifts to `sand-700`; Save inherits btn-primary dark treatment.
- **Rationale.** The dirty state is where the row earns its codification — three things change simultaneously (helper hides, reset appears, Save appears) and the row's visual hierarchy reorients around the pending commit. The helper hides because the derived rationale is no longer the operative read; the operator is now telling the system "no, use my number." The reset's absolute positioning inside the input is the key brand decision: the reset is *part of the input*, not a sibling affordance. The reader's mental model becomes "the input has an override and a reset; the override is the value, the reset is the undo, both live in the same chrome." Pulling the reset outside the input would create three siblings (input, reset, Save) and make the row read as a button group, not a single editable cell. The reset's sand-600 + hover-sand-100 treatment matches `list-group-item-action` hover semantics — one affordance vocabulary across haven. The Save being sibling to the input-group (not nested under it) is the second key decision: when this row composes inside a card with other field-rows, the input-group's left edge aligns with sibling rows' left edges, and the Save's right edge aligns with the form's right edge — preserving the form's vertical-rhythm reading.
- **Quality-test notes.** Quiet-mode: the only teal moment on the row is the Save button. The reset stays neutral, the input border stays sand-200 (no commit-pending border treatment), the label stays sand-700. Grew-not-built: the Save reads as "the row's commit, sized for the row's context," not as "a primary action elevated above the field." Touch target: the 28×28 reset is at the family's minimum for in-chrome affordances (matches `quantity-stepper-btn`'s minus/plus and `btn-icon`'s small sizing).

### 3.3 Saved state — override committed; reset visible; Save hidden

- **Semantic intent.** The override committed successfully. The operator can still reset to the derived value, but there's nothing pending to save.
- **Visual treatment.** Input shows the saved override value (same as dirty's value). Helper line hidden. Reset button visible (same treatment as dirty). Save button **hidden** (CSS rule: `data-state="saved" .field-derived-save { display: none }`). Input keeps `pe-9` padding so the right edge feels consistent with the dirty state. Dark mode mirrors.
- **Rationale.** Hiding the Save once the override is committed is the same affordance-only-when-actionable discipline as the empty state. The reset *stays* because the operator might realize an hour later that they wanted the derived default after all — reset is the row's permanent escape hatch from a committed override. The decision NOT to switch the helper to "Saved: $11.00" or similar status text is deliberate: the input itself shows the saved value; adding a redundant status line would crowd the row and let the row read as "in a different state from sibling rows" when it should read as "a normal field-row whose value happens to override a derived default." If the consumer needs to surface "saved 2 minutes ago" or similar metadata, that lives in a separate row affordance (a small timestamp chip in a table cell context), not in the helper slot.
- **Quality-test notes.** Quiet-mode: a row in saved state reads as a normal field-row with a small in-input reset affordance — sand-neutral, no commit-pending punctuation. Grew-not-built: a column of 8 rows where 2 are saved, 2 are dirty, and 4 are empty should read as one continuous form with subtle per-row affordance differences, not as three different component shapes.

### 3.4 Error state — save failed; input preserves value; error message replaces helper

- **Semantic intent.** Save was attempted and failed (server rejected, validation failed, network failed). The operator's value is preserved so they can correct it; the error message replaces the derived helper; Save remains available for retry; reset remains available for "give up and go back."
- **Visual treatment.** Compose `field-row-derived-override` + `field-row-error` (the existing family modifier). Input border turns red (`field-row-error input { border-color: error-500 }`), `aria-invalid="true"`, `aria-describedby` points at the error message. Helper line hidden. Error message visible below in `.field-error` register (text-sm text-error-600, leading `fa-circle-exclamation`). Reset visible (same as dirty/saved). Save visible (same as dirty). Dark mode: error uses `error-400` per `.field-row-error` family treatment.
- **Rationale.** This state composes two existing families (`field-row-derived-override` + `field-row-error`) rather than introducing new chrome — protects the "grew, not built" test by letting the row inherit the family's existing error vocabulary. The error message replaces the helper because both occupy the row's `mt-1` slot below the input; having both stacked would crowd the row and the error is the operative read while there's a commit failure. The Save staying visible is critical: errors are recoverable, and the operator needs to retry without leaving the row. The error state is sibling to the dirty state visually (same affordances present) plus the red input border + error message — the reader's mental model: "this is a dirty row whose save failed."
- **Quality-test notes.** Quiet-mode: the red border is the only loud signal; the helper is gone, the error message reads in error-red but at the family's text-sm register, the reset stays neutral, the Save stays teal. Grew-not-built: the error treatment matches `field-row-error` exactly so a future agent reading this state recognizes it as "haven's standard error state, applied to the derived-override row."

---

## 4. Cross-cutting decisions

What this variant asserts about the brand, taken as a system:

- **Teal is reserved for state-changing commits.** Save is the only teal moment in the row, and it's reserved for the actual commit. Reset (a state un-do) and the helper info icon stay sand-neutral. This protects haven's reserved-teal discipline across the form family.
- **In-input affordances are sand-neutral; they earn presence through position, not hue.** The reset button reads as part of the input chrome (sand-600 idle, sand-100 hover ground), matching `list-group-item-action` hover semantics and `quantity-stepper-btn` weight conventions. Pulling the reset to teal or to a stronger sand weight would let it compete with the row's adjacent Save commit.
- **The derived-from helper inherits `field-help`'s register.** Same sand-600 text-xs, same `mt-1` rhythm, same gap-1 to the leading icon. The only structural addition is the leading `fa-circle-info` glyph and the literal "Derived:" word. This protects the form-family reading: a `field-row-help` and a `field-row-derived-override` empty state read as the same vocabulary.
- **Affordances appear only when actionable.** Reset is hidden in empty; Save is hidden in empty + saved; helper is hidden in dirty + saved + error. This protects against "always-on chrome" feeling — the row carries only the affordances the operator can act on in the current state.
- **State is bound via `data-state` on the row.** No state-specific CSS classes added to children; the row's `data-state` attribute drives child visibility. This protects the family from per-state class soup and lets the renderer flip state by changing one attribute.
- **Save is NOT inline-save.** The button is sibling to the input, and the consumer's controller decides whether the commit triggers server-side recomputation of derived sibling fields. This is the load-bearing decision the brand spec carries forward — future surfaces composing this row inherit the contract that "Save is a deliberate commit, not a typing-finished signal."
- **No new typography weights or sizes.** All slots use Source Sans 3 in the `text-sm` (label, input) / `text-xs` (helper, error) register. Save inherits btn-primary's type contract. The variant does not earn a Lora moment — Lora is reserved for headings and the document district's authority register; form rows are working-voice.
- **The row is not the inline-edit pattern.** Inline-edit (click-to-edit, tap-to-commit, no separate Save) is a different shape — typically used for field-rows whose value is independently editable without server-side derivation. The derived-override row's Save-with-server-recomputation contract is the distinguishing brand decision.

---

## 5. Open issues / future work

1. **Vanilla JS module ships in a follow-up slice.** Per the PL fragment's `@component-meta` notes, the state-binding + event-dispatch behavior (`derived-override.js`) ships when the cena-apps internal-app lights up client-side state. V0 renders the markup with `data-state` set per-row by the renderer; the four PL exemplars (empty / dirty / saved / error) are static composition examples. When the JS lands, the brand spec re-validates the live state transitions against the four static states above.

2. **Save button sizing in tight table cells.** The Meals catalog's Override column is a table cell. The default `btn-primary` footprint may be too tall for the row height. The brand spec leaves the cell-context sizing as a consumer composition decision: pass `btn-sm` modifier alongside `field-derived-save` when packing into a table; otherwise default `btn-primary`. If multiple consumers reach for `btn-sm` consistently, codify a `field-derived-save-compact` variant in a future slice.

3. **Helper text wrap on long source descriptions.** If a derived value comes from many sources ("kitchen price + delivery fee + markup + program adjustment + per-patient discount"), the helper line wraps. Default behavior is acceptable (no truncate), but a future surface with extremely long source-attribution may want a tooltip-on-hover variant. Not authored speculatively.

4. **Currency-vs-other-unit variants.** The PL exemplars all show currency (`$` prefix). The same row shape applies to percentage overrides (markup %), duration overrides (delivery window), and unit-bearing overrides (calorie targets) — all of which compose `field-addon` per the existing family. No variant work needed; document the polymorphism in the consumer's wireframe.

5. **The "edit existing override" interaction model.** Currently the dirty state assumes the operator started from empty (or saved) and is staging an edit. If the operator opens a row already in saved state and edits the input, the row transitions from saved → dirty (Save reappears). This is correct, but the brand spec asserts it explicitly so future implementations don't drift toward "saved is permanent until reset is clicked."

---

## 6. Validation checklist (for the codification slice's render-pipeline verification)

After the variant ships in `components.css` + `form-derived-override.html` and the cena-apps internal-app Kitchen detail Meals catalog renders:

- [ ] Meals catalog Override column: each row composes `field-row-derived-override` with `data-state` set per row's commit status
- [ ] Empty state: helper line reads `Derived: $X.XX from kitchen price + delivery fee + markup` with leading `fa-circle-info` icon at sand-600; input is empty; no reset; no Save
- [ ] Dirty state: input shows operator's value; helper hidden; in-input reset button at sand-600 with `fa-rotate-left` icon, 28×28px, right-edge of input; adjacent Save button uses `btn-primary` (teal-700)
- [ ] Saved state: input shows committed value; helper hidden; reset visible; Save hidden
- [ ] Error state: composes `field-row-error` + `field-row-derived-override`; input border red (`error-500`); helper hidden; error message visible at text-sm error-600 with leading `fa-circle-exclamation`; reset visible; Save visible
- [ ] Quiet-mode check: a column of 8 meals with 6 in empty state, 1 in saved, 1 in dirty reads as one continuous form-rhythm; the dirty row's teal Save is the column's only teal punctuation
- [ ] Grew-not-built check: composing one `field-row-derived-override` alongside three `field-row-default` rows in the kitchen-info card reads as a single coherent form; the derived-override row doesn't visually compete for attention
- [ ] No row composition violates §4's cross-cutting decisions (no teal on idle affordances, no Lora on labels, no per-state class proliferation, no always-on chrome)
- [ ] Reset's hover surface shift is `sand-100` ground (matches `list-group-item-action` semantics); focus ring is sand-700 (matches `quantity-stepper-btn`)
- [ ] Helper's leading "Derived:" word is the affordance signal; the value and source come from data binding
- [ ] Save dispatches `derived-override-save` CustomEvent; reset dispatches `derived-override-reset` CustomEvent (when JS module lands; pre-JS V0 the static markup is the verification target)

---

## Source

2026-06-17: Haven Steward dispatch — codification slice for the derived-with-override + reset + adjacent-save input pattern. First-class consumer is the cena-apps internal-app Kitchen detail Meals catalog Override column (cap-34 / cap-63). Companion artifacts: PL fragment in [`Lab/haven-ui/packages/design-system/pattern-library/components/form-derived-override.html`](../../haven-ui/packages/design-system/pattern-library/components/form-derived-override.html); CSS in [`Lab/haven-ui/packages/design-system/src/styles/tokens/components.css`](../../haven-ui/packages/design-system/src/styles/tokens/components.css); COMPONENT-INDEX rows in [`Lab/haven-ui/packages/design-system/pattern-library/COMPONENT-INDEX.md`](../../haven-ui/packages/design-system/pattern-library/COMPONENT-INDEX.md); consumer-surface reference in [`Lab/cena-apps/src/docs/Internal App/wireframes.md`](../../cena-apps/src/docs/Internal%20App/wireframes.md) "Components needed but possibly missing entirely from the PL" section. Codification rule: [`.claude/rules/haven-primitive-codification.md`](../../../.claude/rules/haven-primitive-codification.md). Source incident: cena-apps `clinical-meals-row` shipped V0 with Override column as plain text + the interactive shape deferred (Gate 4 deferred in the meals manifest); follow-on Kitchen detail wireframe (cap-63) re-surfaced the same shape, escalating to a Haven Steward dispatch before Phase 4 emit per `generative-determinism.md`'s shape trigger.
