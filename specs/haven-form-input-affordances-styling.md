# Haven form-input affordances styling — bound create-form field-row family

_Brand-spec for the `form-input-default-bound` (plus `-prefix` / `-suffix`) and `form-select-bound` primitives — the bound siblings to `field-row-default` / `field-row-prefix` / `field-row-suffix` / `field-row-textarea` / `form-select`. Visual treatment + rationale only; structure and CSS implementation live in [`Lab/haven-ui/packages/design-system/pattern-library/components/form-field-row-bound.html`](../../haven-ui/packages/design-system/pattern-library/components/form-field-row-bound.html) and [`packages/design-system/src/styles/tokens/components.css`](../../haven-ui/packages/design-system/src/styles/tokens/components.css)._

---

## 1. Context

The bound `form-input-default-bound` family is the create-or-edit-form-row sibling to the `field-row-inline-edit` variant. Where inline-edit pairs a read-only key/value display with a hover-revealed Edit affordance for per-row commits on existing records, the bound family ships visible inputs by default for create flows that commit via a footer-Save. The variant family earned codification when the cena-apps internal-app Programs-create form (cap-59) and Patient-create form (cap-02 D2.4) substituted `field-row-inline-edit-read` (with empty value + current-value) as a stopgap for the wireframe-spec'd regular form inputs. The inline-edit stopgap rendered create forms as read-only-looking rows with hover-revealed edit pens — visually correct binding (labels right) but semantically wrong shape (create forms should show regular form inputs visible by default).

**Why a separate variant family vs. extending `field-row-inline-edit`.** The two families share the `field-row` shell + the label register + the helper-text register + the error register, but they answer different brand questions. Inline-edit's read-mode display dominates the row's resting state; the input is hidden until the operator clicks Edit. The bound family's input dominates the row's resting state; there is no read-mode display. Composing inline-edit on an empty create form makes every row read as "this is read-only, but you can edit one at a time" — the wrong contract for a form whose entire purpose is data entry.

**Why a separate variant family vs. extending the existing `field-row-default` / `form-input-text` / `form-select`.** The existing primitives are NOT emit-bound (no `emit: bound` in their `@component-meta` notes); their `@component:` blocks carry hardcoded demo content ("Patient Name", "Email Address"). A manifest composing them inherits the demo content because the engine has no binding leaves to fill. The bound family preserves the existing primitives' visual contract verbatim — same field-row shell, same label register, same helper register, same field-input-group / field-addon shells, same select-haven class — and adds the `data-field` + `{token}` leaves the engine needs to bind from manifest content. The visual treatment IS the existing canon; the brand decision in this codification is "should the bound family adopt or refine each visual choice the canon already carries?" Section 2 below names each decision absorbed and §3 names what's left visually unchanged.

**Brand intent.** The variant must read as **"a traditional create form, no surprises"** at a glance. The operator scans a column of bound rows and sees labels, inputs, helpers, required asterisks where the wireframe specs them. Every visual decision below serves that one read. The bound family does NOT introduce new visual chrome — it adopts haven's existing form vocabulary and earns codification through the binding contract, not through visual novelty.

---

## 2. Brand frame for this variant family

The family sits inside haven's existing `field-row` family. A reader scanning a programs-create card containing six bound rows + a footer commit pair should see "a single coherent create form" — not "a list of read-only rows," not "a list of inline-edit affordances," not "a form-in-disguise." Three brand decisions follow from that constraint:

- **The input is visible by default; the label is canonical above; the helper is right below.** No reveal-on-hover, no Edit affordance, no state-machine. The row reads as "label, input, helper" in that vertical order — the form-rhythm haven already carries on `field-row-default`. This is the load-bearing decision: introducing any read-state-by-default chrome on the bound family would let it visually compete with inline-edit, and the operator scanning a create form should not have to distinguish "is this row editable now or do I have to click something first."

- **The required marker is a CSS-rendered asterisk on the label, not a separate icon or badge.** The existing `.required` class adds `*` via `::after { content: '*' }` (components.css line 1472). The bound family composes that class on the label element via the `{required-class}` token — the renderer supplies `"required"` for required fields and `""` for optional fields. The visible asterisk lands after the label text in the family's existing error-500 color (sand-700 on the label, error-500 on the asterisk). This is brand-intentional: required-status is a property of the LABEL (the operator reads "Email *" and knows the field is required), not a property of the input (the input itself doesn't carry visible required chrome). Rendering the asterisk via CSS rather than markup keeps the binding contract clean — the renderer toggles ONE class, not a separate `<span>` element.

- **The addon shape ($ prefix, % suffix) earns sibling-variant codification, not a conditional token.** Three sibling components — `form-input-default-bound`, `-prefix`, `-suffix` — let the manifest pick the right shape per field. An empty `<span class="field-addon">` is a visual bug (the `.field-input-group` border-radius surgery assumes the addon is present when the wrapper is present), so a conditional token (empty-addon-drops-element) is the wrong shape. The three siblings cost three component ids in exchange for protecting the family's visual contract.

The "grew, not built" test on this variant family: a programs-create card containing six bound rows + a footer commit pair should read identically to a hand-authored form using `field-row-default` / `field-row-prefix` / `field-row-suffix`. The only differences are (a) the labels / placeholders / helpers / required-flags come from manifest content rather than hardcoded HTML, and (b) the input attributes (id, name, type, value, autocomplete) are token-bound. Every visual register matches the existing canon.

---

## 3. Composition treatment per region

The bound family composes existing primitives. The visual decisions documented below are inherited from haven's existing canon — the codification's brand work is naming WHICH existing decisions earn promotion to the emit-bound surface, not introducing new visual chrome.

### 3.1 Label region

- **Semantic intent.** The operator reads the label to know what the input expects. Required fields carry a visible asterisk.
- **Visual treatment.** Composes `.field-label` (sand-700 text-sm font-medium per `components.css` line 1500). Required fields compose `.field-label.required` — the existing class adds `*` via `::after { content: '*' }` in `text-error-500 ml-1`. Dark mode: sand-200 (existing canon).
- **Rationale.** The label register is the family's canonical "what is this input asking for" surface. Haven's existing canon uses sand-700 text-sm font-medium across all field-row variants; the bound family adopts it verbatim. The required asterisk uses error-500 not because required-status is an error (it isn't), but because haven's error vocabulary owns the "you need to do something here" register, and required-status IS a "you need to fill this in" signal. The asterisk lands after the label text (CSS `::after`) so the read order is `<label text> <asterisk>` — the operator reads the label first and sees the asterisk as a postpositive flag.
- **Quality-test notes.** Quiet-mode: labels in sand-700 with the occasional error-500 asterisk; no shouting, no all-caps. Grew-not-built: the bound family's label register matches `field-row-default`, `field-row-inline-edit`, `field-row-derived-override` — one family vocabulary.

### 3.2 Input region (plain — `form-input-default-bound`)

- **Semantic intent.** The operator types their value into the input.
- **Visual treatment.** Global `<input>` styling applies automatically (haven's `components.css` element selectors). Border sand-200, background sand-50, text sand-800, focus ring primary-500 (existing canon). Type-attribute bound via `{type}` token (`text` | `email` | `tel` | `number` | `date`); the native browser styling applies per type. Dark mode: sand-700 border, sand-800 background (existing canon).
- **Rationale.** The bound family inherits global input styling — no new visual decisions. The type-attribute token is the load-bearing binding decision: ONE primitive covers text / email / tel / number / date by binding the type via token rather than authoring 5 sibling components. The native browser rendering for `type="date"` is the V0 fallback per Aaron's 2026-06-17 Q2 AUQ call; a richer date-picker primitive lands when a consumer surface needs it.
- **Quality-test notes.** Quiet-mode: input border sand-200 at rest, primary-500 on focus, no per-type visual variants. Grew-not-built: the bound input uses the same global element styling every other field-row variant uses.

### 3.3 Input region with prefix addon ($) — `form-input-default-bound-prefix`

- **Semantic intent.** A currency or unit prefix sits before the input value (e.g., delivery fee `$`).
- **Visual treatment.** Composes `.field-input-group` (flex shell with border-radius surgery) wrapping `<span class="field-addon">$</span>` + `<input>`. The addon renders with sand-200 background, sand-700 text, rounded-l (left-rounded only — the input takes the right-rounded edge). Existing canon from `field-row-prefix`. Dark mode: sand-700 background, sand-200 text.
- **Rationale.** The prefix addon is the family's canonical pre-input glyph register. Used for currency (`$`), URL schemes (`https://`), search icons. The bound family composes this verbatim; the addon text is data-bound via `data-field="addon"` so the manifest supplies the glyph string.
- **Quality-test notes.** Quiet-mode: the addon reads as quiet chrome adjacent to the input, not as a button. Grew-not-built: matches `field-row-prefix` from `layout-field-row.html` exactly.

### 3.4 Input region with suffix addon (%) — `form-input-default-bound-suffix`

- **Semantic intent.** A unit suffix sits after the input value (e.g., markup `%`, glucose `mg/dL`).
- **Visual treatment.** Composes `.field-input-group` wrapping `<input>` + `<span class="field-addon">%</span>`. The addon renders with sand-200 background, sand-700 text, rounded-r (right-rounded only — the input takes the left-rounded edge). Existing canon from `field-row-suffix`. Dark mode mirrors prefix.
- **Rationale.** Same family decision as prefix. Suffix is the canonical post-input glyph register. Used for percent, units of measure, currency codes (`USD`).
- **Quality-test notes.** Quiet-mode: the suffix reads as quiet chrome adjacent to the input. Grew-not-built: matches `field-row-suffix` from `layout-field-row.html` exactly.

### 3.5 Select region — `form-select-bound`

- **Semantic intent.** The operator picks one option from a finite list (e.g., primary language: English / Español).
- **Visual treatment.** Composes the `.select-haven` class on the `<select>` element — adds the custom chevron and overrides native browser appearance (existing canon from `form-select.html`). The placeholder option renders as `<option value="" disabled selected>Select a language</option>` (dimmed by the browser's `:disabled` styling). Bound options render via the `@slot: options` shape — manifest's `each.source: <array>` populates a list of `<option value="{value}" data-field="label">…</option>` children. Dark mode inherits select-haven canon.
- **Rationale.** The select-haven custom chevron is haven's brand decision to override native browser appearance — the native chevron varies wildly across operating systems and breaks the family's vertical rhythm. The placeholder option pattern (disabled + selected by default) gives operators a clear "no choice yet" register without committing to a default value. The bound options via each.slot is the engine's canonical iteration shape per the recent each.slot work (Steward dispatch 2026-06-16 bless); selecting `<select>` as the each host element is engine-aligned (the engine treats DOM as text and slot-marker substitution is element-agnostic).
- **Quality-test notes.** Quiet-mode: the select reads as a single quiet affordance with the custom chevron, no shouting. Grew-not-built: matches the existing `form-select` primitive's visual contract; the binding contract is the only delta.

### 3.6 Helper-text region

- **Semantic intent.** Helper text sits below the input to explain format, units, or context (e.g., "Optional — programs without an end date run indefinitely").
- **Visual treatment.** Composes `.field-help` (sand-600 text-xs mt-1 per `components.css` line 1505). Bound via `data-field="helper"`. Empty helpers drop visually via the `.field-help:empty { display: none }` rule sibling to the `.field-help` register itself in `components.css`. Dark mode: sand-300.
- **Rationale.** Helper text register matches haven's existing canon — sand-600 text-xs sits one step below the input vertically. The `:empty` drop rule lives in `components.css` alongside the `.field-help` definition (define-once: one canonical home for the helper register including its empty-drop behavior). The rule is the engine-aligned shape for "conditional element presence" — the engine doesn't support drop-on-empty natively; CSS handles it post-render. The alternative (omitting the `<p>` element when helper is empty) would require engine extension; the current shape protects the engine's contract.
- **Quality-test notes.** Quiet-mode: helpers read as small sand-600 text below the input, no shouting. Grew-not-built: matches `field-row-help` from `layout-field-row.html`. Empty-helper rows visually identical to rows without helpers.

### 3.7 Error region

- **Semantic intent.** A failed validation surfaces below the input as a red-text message with a leading exclamation icon.
- **Visual treatment.** Composes existing `field-row-error` modifier on the row + `.field-error` register on the message. Input border error-500, error-message text-sm text-error-600 with leading `fa-circle-exclamation`. Existing canon from `layout-field-row.html` `field-row-error` variant. Dark mode: error-400.
- **Rationale.** The bound family does not introduce new error chrome — composes existing family-error vocabulary. The error region is NOT bound in V0 (no client-side validation in the static preview); a future codification adds error binding when Angular wires the form model. The PL fragment documents the error composition as a static visual reference; the bound emit does not include error binding today.
- **Quality-test notes.** Quiet-mode: when a row errors, the red border is the only loud signal; the error message reads in error-red at the family's text-sm register. Grew-not-built: matches `field-row-error` exactly.

---

## 4. Cross-cutting decisions

What this variant family asserts about the brand, taken as a system:

- **The bound family inherits ALL of haven's existing form vocabulary verbatim.** Label register, input styling, addon shells, select-haven class, helper register, error register — every visual decision below the binding layer was already canon. The codification's brand work is naming WHICH existing decisions earn the emit-bound treatment, not introducing new chrome.
- **Required-marker is a CSS-rendered asterisk on the label, never a separate icon or badge.** Toggled via the `{required-class}` token on the label element. Lives on the label not the input — required-status is a property of the label's read.
- **Type-attribute is bound via token — ONE primitive covers text / email / tel / number / date.** Multi-type variants would multiply the surface 5x without earning any new visual decision per type.
- **Addon shape is three sibling variants, not one conditional.** Empty-addon-elements break the field-input-group border-radius surgery; siblings are the engine-aligned shape.
- **Helper-text empty-drops via CSS, not engine logic.** Inline `<style>` scopes the rule to the bound family; engine alignment over engine extension.
- **Select-options bind via each.slot + bound child.** Engine-aligned with the each.slot work (Steward dispatch 2026-06-16 bless). The select element accepts slot-marker substitution like any other element.
- **No icon-leading variants in V0.** The wireframe specs no leading icons on bound inputs (the patient-create form has no icon inputs; programs-create has no icon inputs). An icon-leading variant lands when a consumer surface needs it.
- **No client-side validation in V0.** Required-marker is visual-only; submit-disabled-until-valid + MRN uniqueness check + email format validation all live in Andrey's Angular port. The bound emit ships static markup with bound attributes; the runtime layer earns its own dispatch when consumer surfaces light up state.
- **The bound family is NOT the inline-edit family.** Inline-edit ships per-row commits with hover-revealed Edit affordances on existing records; the bound family ships visible inputs by default for create flows. Future surfaces composing these alongside each other should distinguish by intent.
- **State is bound via input value + data attributes, not via per-state CSS classes.** No `data-state` attribute on the row (unlike inline-edit's read|editing|saving|saved|error). The bound family's state IS the input's value; the form's submit handler is the commit layer.
- **No new typography weights or sizes.** All slots use Source Sans 3 in the `text-sm` (label, input) / `text-xs` (helper, error) register. The variant does not earn a Lora moment — Lora is reserved for headings.

---

## 5. Open issues / future work

1. **Client-side validation + error binding.** V0 ships static markup with the visible required asterisk. Required-validity enforcement (`required` HTML attribute, error binding on validation failure, submit-disabled-until-valid) lands when Angular wires the form model. A future codification slice adds error-state binding to the bound family.

2. **Richer date-picker primitive.** V0 uses the native browser date input for `type="date"`. Native picker UX varies wildly (Safari shows a date stepper; Chrome shows a calendar popover; Firefox shows a calendar grid). A richer date-picker primitive lands when a consumer surface needs date-range constraints (start ≥ today; end ≥ start) or a consistent visual across browsers.

3. **Multi-line textarea-bound variant.** Patient-create's Address field is single-line text in V0 per the wireframe Reco. A future surface needing multi-line input (Clinical notes, Operator-comments) earns a `form-textarea-bound` sibling. Address would be a candidate for upgrade if a future cohort needs full street-address granularity.

4. **Selected-option-by-default for `form-select-bound`.** V0 ships the placeholder option as `disabled selected` — no value pre-selected. A future surface needing a pre-selected default (e.g., "Default language: English") earns a `selected-flag` field on the option record + an engine binding to set the `selected` attribute conditionally.

5. **Icon-leading variants.** A future surface needing an icon-leading bound input (e.g., search input with a leading magnifying-glass glyph) earns an `-icon-prefix` sibling variant. The existing `field-row-icon-prefix` from `layout-field-row.html` is the visual template; a bound version codifies when surfaced.

6. **`autocomplete` token enforcement.** The bound shape leaves `autocomplete="{autocomplete}"` as a manifest-supplied token. Future consumer surfaces SHOULD bind autocomplete tokens for accessibility / password-manager interop. A future codification could enforce a minimum autocomplete vocabulary per-type (e.g., `type="email"` requires `autocomplete="email"`).

7. **Required-validity enforcement via bound attribute.** The HTML `required` attribute on the input is NOT bound in V0 — the visible asterisk is the V0 contract. A future iteration adds a `required="{required-attr}"` token so consumers can opt into native browser validity enforcement before Angular wires the form model.

8. **Multi-field row variant.** A future surface composing two bound inputs in one row (e.g., "First name | Last name" inline) earns a row-level multi-input variant. Distinct shape from the single-input bound family; codify when surfaced.

---

## 6. Validation checklist (for the codification slice's render-pipeline verification)

After the variant family ships in `form-field-row-bound.html` and the cena-apps internal-app Programs-create + Patient-create forms render:

- [ ] Programs-create card: six bound rows compose `form-input-default-bound` / `-prefix` / `-suffix` with labels + placeholders + helpers + required-markers bound from content.json
- [ ] Markup % row: composes `-suffix` with `<span class="field-addon">%</span>` after the input
- [ ] Delivery fee row: composes `-prefix` with `<span class="field-addon">$</span>` before the input
- [ ] Start date row: composes `form-input-default-bound` with `type="date"` → native browser date input renders
- [ ] End date row: composes `form-input-default-bound` with `type="date"` + helper "Optional — programs without an end date run indefinitely" + NO required asterisk (the only optional row in the programs-create form)
- [ ] Required asterisk visible after labels on: Program name, Markup %, Delivery fee, Enrollment cap, Start date (the wireframe-required fields)
- [ ] Patient-create card: seven bound rows compose 6 × `form-input-default-bound` + 1 × `form-select-bound`
- [ ] Full name + MRN rows: required asterisks visible
- [ ] Contact email row: `type="email"` + `autocomplete="email"`
- [ ] Contact phone row: `type="tel"` + `autocomplete="tel"`
- [ ] Date of birth row: `type="date"` → native browser date input renders
- [ ] Primary language row: composes `form-select-bound` with EN + ES options bound from content.json's `language-option-en` / `language-option-es` strings via the `crumbs`-shaped each.source array
- [ ] Quiet-mode check: a column of six (or seven) bound rows reads as a single coherent create form; no row visually competes for attention; the required-asterisks are the only error-color signals
- [ ] Grew-not-built check: composing one `form-input-default-bound` alongside one `form-input-default-bound-prefix` and one `form-input-default-bound-suffix` in the same card reads as one form — the addons sit adjacent to their inputs without breaking row rhythm
- [ ] Helper-text empty-drops: rows without helpers render with the empty `<p class="field-help">` hidden by the inline `:empty { display: none }` rule; no vertical-spacing leak from empty paragraphs
- [ ] Form-select-bound options bind cleanly: the `<select>` host's `@slot: options` is filled with `<option value="..." data-field="label">...</option>` children per each item in the content.json array; the dimmed placeholder option (`disabled selected`) renders first; the bound options follow
- [ ] No row composition violates §4's cross-cutting decisions (no separate required-badge, no per-type-variant proliferation, no client-side validation chrome, no Lora on labels, no `data-state` attribute on the row)

---

## 7. Source

2026-06-18: Haven Steward dispatch 4.8d — codification slice for the bound create-form field-row family (`form-input-default-bound` + `-prefix` + `-suffix` + `form-select-bound` + `form-select-option`). Fourth of five dispatches in Aaron's 2026-06-18 AUQ Q3 verdict (land all 5 Gate-4 deferrals before Andrey handoff). Canonical consumers are the cena-apps internal-app Programs-create form (cap-59 — six fields including markup % + delivery fee + start/end dates) and the Patient-create form (cap-02 D2.4 — seven fields including a Primary language select). Companion artifacts: PL fragment in [`Lab/haven-ui/packages/design-system/pattern-library/components/form-field-row-bound.html`](../../haven-ui/packages/design-system/pattern-library/components/form-field-row-bound.html); COMPONENT-INDEX rows in [`Lab/haven-ui/packages/design-system/pattern-library/COMPONENT-INDEX.md`](../../haven-ui/packages/design-system/pattern-library/COMPONENT-INDEX.md); consumer-surface references in [`Lab/cena-apps/src/apps/internal/screens/programs-create.screen.json`](../../cena-apps/src/apps/internal/screens/programs-create.screen.json) + [`patient-create.screen.json`](../../cena-apps/src/apps/internal/screens/patient-create.screen.json) (paired content.json files updated to bind the field-row tokens). Codification rule: [`.claude/rules/haven-primitive-codification.md`](../../../.claude/rules/haven-primitive-codification.md). Source incident: cena-apps wireframes called for traditional create-form rows; the Gate-4 deferral on unbound `field-row-default` + `form-input-default` + `form-select` was substituted with `field-row-inline-edit-read` as a stopgap — semantically wrong shape. The Haven Steward codified the bound family pre-emptively under `generative-determinism.md`'s shape trigger before Andrey reviews the dist surface.
