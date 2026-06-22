# Haven account-form styling — multi-field commit-together edit-form pair

_Brand-spec for the `account-form-field` + `commit-action-page-save` primitives — the canonical multi-field commit-together edit-form pair. Visual treatment + rationale only; structure and CSS implementation live in [`Lab/haven-ui/packages/design-system/pattern-library/components/account-form-field.html`](../../haven-ui/packages/design-system/pattern-library/components/account-form-field.html) + [`commit-action-page-save.html`](../../haven-ui/packages/design-system/pattern-library/components/commit-action-page-save.html) and [`packages/design-system/src/styles/tokens/components.css`](../../haven-ui/packages/design-system/src/styles/tokens/components.css)._

---

## 1. Context

The `account-form-field` + `commit-action-page-save` primitive pair is the canonical multi-field commit-together edit-form vocabulary for haven-ui. The pair earned codification when the cena-apps patient-app account.html hand-author (slice 4 of patient-app-2026-06-17-feedback plan, 2026-06-17) introduced the Profile section's four-field inline-edit shape with [data-account-field] + [data-account-save] + dirty-tracking JS inline in the HTML. Haven Steward dispatch 2026-06-22 codified the shape pre-emptively under `generative-determinism.md`'s shape trigger (multi-field commit-together edit-form is a recognizable shape with reasonable likelihood of recurring across future patient / staff preference surfaces — settings pages, notification preferences, communication channels).

**Why a sibling primitive pair vs. extending an existing form-row family.**

- `field-row-default` / `form-input-text` are the static building blocks for hand-authored forms; they carry no dirty-tracking and no commit-button coupling.
- `field-row-inline-edit` is the per-row commit-on-existing-record sibling; the read-state dominates the visual register and the operator commits one attribute at a time.
- `form-input-default-bound` (et al.) is the manifest-bound create-form sibling; it ships visible inputs by default but no client-side state.

The account-form pair sits in the gap between these: multiple visible inputs that the patient edits and then commits AS ONE UNIT via a single footer Save button — with per-field "you've edited this" indication along the way. Composing inline-edit on a multi-field edit form makes every row read as "this is read-only, but you can edit one at a time" — the wrong contract for a form whose entire purpose is multi-attribute data entry. Composing bare field-row-default with hand-rolled dirty-tracking JS works but re-derives the shape every time a new consumer needs it. Codification absorbs the shape so future profile / preferences / settings surfaces inherit it.

**Brand intent.** Both primitives must read as **"a clean, calm edit form — you change things, you save them, you see what you've changed."** The patient (a Cena Health care recipient, often older, sometimes navigating a stress-laden moment) should never feel they have to interpret extra chrome to know what's happening. Three visible cues do the work:

1. The dirty-marker dot — primary-500, small, inline after the label — reads as "active / pending change," not as alarm.
2. The commit button's enabled state — the existing btn-primary :disabled chrome — tells the patient there's something to save.
3. The commit button's success register — color flip + label swap — tells the patient the save landed.

No motion-driven flourishes, no superfluous animations, no extra signal lines. Brand restraint per the cena-health-brand tone: solid, confident, clinical-warm.

---

## 2. Brand frame for this primitive pair

The pair sits inside haven's existing form family (field-row + btn-primary). A reader scanning an account-page Profile card containing four `account-form-field` rows + one `commit-action-page-save` footer should see "a single coherent edit form" — not "a list of standalone editable rows," not "a form with extra chrome." Three brand decisions follow:

- **The dirty-marker is a dot, not a flag / pill / badge / asterisk.** A small primary-500 circle inline after the label text. Chosen over (a) an exclamation icon (reads as alarm), (b) a "modified" pill (heavier weight than the label deserves), (c) a left-border accent on the row (over-emphasizes a single field over the form's whole), (d) an asterisk (visually confusable with the required-field marker). The dot is the minimum-viable-signal that says "this row has pending change" without competing for attention with the input itself.

- **The dot color is primary-500 (active register), not warning-500 (alarm).** Per the cena-health-brand tone, the patient editing a value is engaged in a routine maintenance action — they're not flagging an error, they're not crossing a boundary. A warning-500 dot would read as "something is wrong here." A primary-500 dot reads as "this is now in motion" — paired with the eventual success-600 flip on commit, the patient sees a clean teal → green journey from "I edited" → "It saved." That color story carries the emotional arc of the edit; warning-amber would interrupt it.

- **The commit button success-register is a color flip + label swap, not an animation.** Per the cena-health-brand tone, commit-success should feel solid and confident, not playful. A success-600 ground fill + a check glyph + a label change ("Save updates" → "✓ Saved") is one coherent "the commit landed" beat. A motion-driven success animation (button squeeze, bounce, particle effect) would feel like product showmanship in a clinical context. The color-flip approach is also `prefers-reduced-motion`-native: the change is purely color, no transform or opacity animation, so reduced-motion users see the same cue with no override needed.

The "grew, not built" test on this pair: a Profile card containing four `account-form-field` rows + one `commit-action-page-save` footer should read identically to a hand-authored form using `field-row-default` + `btn-primary btn-block` — same shell, same label register, same input chrome, same button fill — with TWO additions: (a) the dirty-marker dot when a row is edited, (b) the success-state flip on commit. Every other visual register matches the existing canon.

---

## 3. Composition treatment per region

The pair composes existing primitives. Visual decisions documented below are inherited from haven's existing canon — the codification's brand work is naming WHICH existing decisions earn promotion to the emit / state-managed surface, plus the two additions above.

### 3.1 account-form-field row

- **Semantic intent.** The patient reads the label to know what the field expects; types their edit; sees the dirty marker appear inline if the value differs from the snapshot; sees it disappear if they revert.
- **Visual treatment.** Composes `field-row`-equivalent gap-1 between label and input; `field-label` semantics on the label (sand-700 text-sm font-medium); global input chrome on the `<input>` (haven canon — border, padding, focus, disabled handled by element selectors in components.css).
- **Inter-field rhythm.** Adjacent `account-form-field` siblings carry `mt-4` (16px). Tighter than the generic `field-row + field-row { mt-6 }` because a Profile card stacks four siblings inside one card-body that already supplies the outer rhythm.
- **Dirty marker.** When `[data-dirty="true"]` is set on the row, the label carries a CSS `::after` pseudo-element: an 8×8px primary-500 circle, inline at the end of the label, with `gap-2` between label text and dot. The marker is purely visual chrome (CSS-only, not announced); AT users get the same signal from the submit button's enabled state changing.
- **Error register.** Composes `.field-row-error` (existing canon — red input border) + `.field-error` (red error message below). Stacks with `.account-form-field` on the same div. The dirty marker remains visible in the error state (the row is still dirty even though it's invalid); the error chrome reads as the dominant cue.
- **Disabled register.** Composes the native `disabled` attribute on the input; existing input `:disabled` chrome handles the visual (sand-100 background, sand-500 text). No dirty marker on disabled inputs (the patient can't edit them anyway).

### 3.2 commit-action-page-save button

- **Semantic intent.** The patient reads the button label to know what commits; sees the disabled register before any edits; sees the enabled register once they've changed something; sees the success register once they've saved.
- **Default chrome.** Composes `btn-primary` + `btn-block` — same primary-600 fill, same py-3 px-4 padding, same :hover transition to primary-700. The full-width `btn-block` is canonical for sticky-footer / mobile-form CTAs.
- **Disabled register.** The native `disabled` attribute + existing btn-primary `:disabled` chrome (faded primary, no pointer cursor). The patient sees a dimmed button before any edits — implicit cue that "nothing to save yet."
- **Enabled register.** No additional chrome — the patient sees the standard btn-primary register. Removal of the `disabled` attribute is the visible state-change cue. Hover / focus chrome inherits from btn-primary.
- **Success register.** When `.is-success` is set on the button:
  - **Ground fill** flips from primary-600 to success-600 (`#16a34a` family). Hover state stays success-600 (no hover state change during the brief success window).
  - **Label** swaps from "Save updates" / "Guardar cambios" to "✓ Saved" / "✓ Guardado" — the leading check glyph + the changed verb tense together signal "this is done."
  - **Duration** ~1800ms. Long enough for the patient to register the success beat; short enough that they don't wait on it before navigating. Same duration as the cart-panel's success-state lock animation for app-internal consistency.
  - **Restoration** after the window: success-600 → primary-600, label → default, disabled → re-evaluated per form-dirty state (typically disabled again, since the snapshot has been re-baselined to the just-saved values).
- **Reduced motion.** No `@media (prefers-reduced-motion: reduce)` override needed — the success register is purely color + label change, no transform / opacity animation.

### 3.3 i18n integration

Both primitives carry i18n-aware label attributes:
- Row labels: `data-i18n-en="Name" data-i18n-es="Nombre"` etc.
- Commit button: `data-i18n-en="Save updates" data-i18n-es="Guardar cambios" data-i18n-en-saved="✓ Saved" data-i18n-es-saved="✓ Guardado"`.

The `language-toggle.js` script swaps the row labels on language change. The `account-form.js` script swaps the commit-button label per state (default vs. saved). During the success window, the commit button carries `data-account-state="saved"` so `language-toggle.js` skips the swap on that element — without that marker, a language toggle mid-success-window would clobber the "✓ Saved" label with the default register.

---

## 4. What this brand spec does NOT cover

- **Inline-edit per-row commit semantics.** That's `field-row-inline-edit`'s register; see [`haven-inline-edit-field-styling.md`](haven-inline-edit-field-styling.md).
- **Manifest-bound create-form rows.** That's `form-input-default-bound`'s register; see [`haven-form-input-affordances-styling.md`](haven-form-input-affordances-styling.md).
- **Footer-bar dual-button commit patterns** (e.g. Cancel + Save). Out of scope for v1. When a consumer surface needs Cancel + Save side-by-side, a footer-bar primitive composing `commit-action-page-save` + `btn-outline` earns codification.
- **Optimistic-UI commit behavior.** account-form.js's default success-flip happens on submit synchronously (no backend round-trip). Consumers needing to defer the success register until a backend POST confirms should listen for `account-form:submit`, `preventDefault()`, then call `_accountForm.flashSuccess()` / `_accountForm.revertSuccess()` per the round-trip's resolution. v1 documents the contract; an optimistic-UI register variant earns codification when a consumer needs it.

---

## 5. Source incident

cena-apps patient-app account.html hand-author (slice 4 of patient-app-2026-06-17-feedback plan, 2026-06-17). Aaron's feedback consolidated: inline-edit Profile section with four fields + a Save updates footer button + dirty-tracking JS inline in the HTML. The dirty-tracking + success-flip behaviors were authored on-page, not as PL primitives — a tactical V0 choice that worked for the slice but left the shape uncodified. Haven Steward dispatch 2026-06-22 (this brand spec) codified the pair pre-emptively so future profile / preferences / settings screens inherit the contract without re-deriving it.

The hand-author shape this codification absorbs:
- 4 `<input data-account-field>` inputs inside a `<form data-account-form="profile">` container
- 1 `<button data-account-save disabled>` footer button
- Snapshot/diff JS that toggled the `disabled` attribute per form-dirty
- A success-flip that briefly swapped the button label to "✓ Saved" via the `data-i18n-en-saved` / `data-i18n-es-saved` attribute pair + a `btn-success-state` class (now codified as `.commit-action-page-save.is-success`)

The codified shape differs from the hand-author in three places:
- **`.btn-success-state` → `.commit-action-page-save.is-success`** — the new class is namespaced under the primitive so consumers + invariant tripwires can pin "the canonical save-button success register" vs. the general "any button success register."
- **Dirty marker dot** — the hand-author had NO per-field dirty marker (only the button's enabled state told the patient something was dirty). The codified primitive adds the dot via CSS `::after` on `.account-form-field[data-dirty="true"] label`. This is a brand-additive choice — the hand-author shipped without it, but the visible per-field cue serves the patient population better than the button-only cue.
- **i18n during success window** — the hand-author used a `data-accountState="saved"` (camelCase) marker; codified version uses `data-account-state="saved"` (kebab-case, standard HTML dataset convention). Functionally equivalent.

Documented in the completion report as the consumer-source divergence to reconcile in the next cena-apps re-emit slice.
