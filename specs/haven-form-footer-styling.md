# Haven form-footer styling — the paired Cancel + Save form-footer composition

_Brand-spec for the `commit-action-with-cancel-bound` variant — the canonical right-aligned `btn-outline` Cancel + `btn-primary` Save form footer for cena-apps create/edit forms whose Cancel routes back to the previous page. Visual treatment + rationale only; structure and CSS implementation live in [`Lab/haven-ui/packages/design-system/pattern-library/components/commit-action.html`](../../haven-ui/packages/design-system/pattern-library/components/commit-action.html) and [`packages/design-system/src/styles/tokens/components.css`](../../haven-ui/packages/design-system/src/styles/tokens/components.css)._

---

## 1. Context

The `commit-action-with-cancel-bound` variant codifies the paired Cancel + Save form-footer pattern that cena-apps's five active create/edit forms (programs-create, programs-edit, kitchen-create, patient-create, add-meal) reach for as their commit-row. Prior to codification, those screens composed a `cluster` + `text-link-item` Cancel sibling alongside a standalone `commit-action-submit-button-variant` Save — a structurally valid composition but one whose wireframe-canonical shape (per `cap-59-programs-edit.md` § Block-level structure) is right-aligned `btn-outline` Cancel + `btn-primary` Save inside a card-footer-shaped container. Each of the five screen.jsons carried a `$notes.cancel-button-emit-fallback` flag naming the missing primitive; the variant closes the gap.

Codified 2026-06-18 against the 3-use trigger per `haven-primitive-codification.md`. The variant lands as a sibling bound id inside `commit-action.html` — the right-side action is still a commit-action, the left-side Cancel adds the paired affordance the commit-row needs. The bound shape exposes three strings: `cancel-label` (typically "Cancel"), `cancel-href` (the route the Cancel anchor navigates back to), and `commit-label` (per-consumer Save / Create / Submit).

**Mirrors the confirm-dialog footer.** The container `card-footer flex justify-end gap-3` is the same shape `overlay-confirm-dialog.html` already uses for its five dialog variants (lines 41–46, 73–75, 105–107, 135–137, 172–174). Same `btn-outline` Cancel + `btn-primary`-class commit on the same baseline with the same `gap-3` (12px) spacing. The form-footer variant brings the dialog-proven shape to standalone create/edit forms so the affordance pair reads identically across modal-and-non-modal commit contexts.

**Brand intent.** The variant must read as **"the form's commit affordance pair, anchored at the form's bottom edge"** at a glance. The card-footer container anchors the pair visually as the form's terminal commit row — separated from the form body by a top border + padding. The right-alignment keeps the primary verb (Save) where the eye lands after scanning the form fields, with Cancel adjacent-but-secondary at its left. The btn-outline + btn-primary pairing is the canonical Haven affordance-pair vocabulary for "commit or back out" decisions — the outline variant reads as available-but-secondary, the primary teal reads as the form's terminal commit.

---

## 2. Brand frame for this variant

A reader scrolling to the bottom of a create/edit form should see "the form's terminal commit pair, anchored at the form's right edge" — not "two equal-weight buttons" and not "a primary commit with a buried Cancel." Three brand decisions follow:

- **Right-alignment via `justify-end` is the canonical commit-pair convention.** The container's `flex justify-end gap-3` pulls both buttons to the right edge. This matches the confirm-dialog footer pattern (overlay-confirm-dialog.html), the page-header trailing-actions slot (haven-page-header-trailing-actions-styling.md §2), and the broader "primary verb to the right" convention from Material, Carbon, and Polaris. Left-aligned commit pairs read as toolbar shapes; centered commit pairs read as marketing CTAs; right-aligned commit pairs read as form terminals. The form-footer variant inherits the right-aligned convention so it reads as belonging to the same affordance vocabulary the rest of the system speaks.

- **`btn-outline` Cancel sits to the left of `btn-primary` Save on the same baseline.** The reading order on the right-aligned pair is Cancel-then-Save (left to right). This is opposite to the source-order intent of the buttons (Save is the primary), but matches the WCAG convention for affordance pairs: the destructive-or-back-out affordance reads first so a hurried user does not mis-commit. `btn-outline` provides the visual weight discipline — sand-300 border + sand-50 background, against the form's sand-50 surrounding context — so Cancel reads as available-but-secondary. The btn-primary Save carries the form's terminal commit weight (teal-700 fill, white text); the typography pair reads as "back out OR commit," with the commit's primary fill carrying the eye on a successful read.

- **`gap-3` (12px) is the canonical button-pair spacing.** Tighter (gap-2) reads as crammed; looser (gap-4) reads as disconnected. 12px is the spacing the confirm-dialog footer already uses, and the form-footer variant inherits it directly. The two affordances read as a paired unit with breathing room — not two independent buttons that happened to land on the same row.

The "grew, not built" test on this variant: scrolling to the bottom of a populated create or edit form, a reader's eye should land on the form's last field, sweep down to the card-footer's top border, and then sweep right across Cancel → Save in a single coherent commit-row read. If the affordance pair shifts up or visually swallows the form's last field's bottom margin, the composition reads as broken. If the Save button takes secondary visual weight against an over-styled Cancel, the form reads as ambiguous about what commits the work.

---

## 3. Composition — visual treatment + rationale

The variant carries one container with two semantic children: a Cancel anchor and a Save submit button. The container encodes the spacing and alignment rhythm; the children carry their own visual weight from `btn-outline` and `btn-primary`.

### 3.1 Container — `.card-footer flex justify-end gap-3` (reused, no new class)

- **Semantic intent.** The form's terminal commit-row — visually distinct from the form body by a top border and padding rhythm.
- **Visual treatment.** Reused directly from the existing `.card-footer` semantic class — `px-4 py-3 md:px-5 md:py-4 border-t border-sand-200 dark:border-sand-700`. Composed inline with `flex justify-end gap-3` utility classes for the right-aligned button-pair layout. No new semantic class lives at this layer — the existing `.card-footer` already carries the spacing and border rhythm; the `flex justify-end gap-3` is the layout decision specific to the paired-cancel commit row.
- **Rationale.** Reusing `.card-footer` ties the form footer to the same surface vocabulary the rest of the design system speaks — cards, modal panels, and form sections all reach for `.card-footer` for their terminal rows. A bespoke `.form-footer` class would fragment the vocabulary without adding semantic distinction (the form's commit row IS structurally a card-footer; the form IS the card). The inline `flex justify-end gap-3` is shared with confirm-dialog-footer via composition pattern — no new semantic class needed.

### 3.2 Cancel — `<a class="btn-outline" data-field="cancel-href">`

- **Semantic intent.** The back-out affordance — routes the user back to the previous page (typically the listing page that linked to the create/edit form).
- **Visual treatment.** Inherited from `btn-outline` — `border-sand-300 bg-sand-50` (light mode), sand-700 text, hover lightens background. Default btn-md size (40px tall, body-02 weight).
- **Rationale.** Anchor (not button) so the Cancel routes via browser navigation rather than a JS handler — keeps the primitive framework-agnostic and skips the consumer-wires-the-handler ceremony for a back-out action that has a stable target (the parent listing). `btn-outline` is the canonical Haven affordance for "available but secondary" — same vocabulary the page-header secondary actions, modal-footer Cancel buttons, and confirm-dialog Cancels speak. Single-source — no per-variant visual treatment.

### 3.3 Save — `<button type="submit" class="btn-primary" data-field="commit-label">`

- **Semantic intent.** The form's terminal commit — submits the form, triggers the server-side write, navigates to the post-write destination per the consumer's wiring.
- **Visual treatment.** Inherited from `btn-primary` — `bg-teal-700 text-white` (light mode), hover deepens, focus ring. Default btn-md size.
- **Rationale.** `type="submit"` triggers the form's onSubmit handler (the consumer wires that per its own write surface). The btn-primary teal fill is the canonical Haven affordance for "the page's terminal commit" — same vocabulary the commit-action primitives speak everywhere else in the system. Single-source — no per-variant visual treatment.

---

## 4. Honest limits + open follow-ons

- **The variant assumes Cancel is an anchor (navigation), not a button (JS handler).** This is the right default for the five active cena-apps consumers — all five Cancels route back to the previous listing page (programs-create → programs, programs-edit → programs/:id, kitchen-create → kitchens, patient-create → patients, add-meal → kitchen-detail). A future consumer whose Cancel needs a JS handler (e.g., "clear unsaved changes before navigating") will need a sibling variant rendering Cancel as a button. Defer until a real consumer needs it.
- **The variant does NOT encode unsaved-changes confirmation.** Composing this footer in a form that has unsaved changes will let the Cancel anchor navigate without confirmation. If a consumer needs unsaved-changes protection, that wiring lives at the consumer's onChange-tracking layer, not in this primitive's container. The primitive's contract is "right-aligned Cancel + Save"; the contract is silent on unsaved-changes state by design.
- **The variant does NOT enforce a third action ("Save and continue", "Save draft").** Some forms benefit from a tertiary commit option. The bound shape is intentionally fixed at the two-action pair to preserve the canonical wireframe shape; a future three-action variant (`commit-action-with-cancel-and-tertiary-bound`) would be a sibling primitive, not a modifier of this one.
- **No new semantic class lives at the variant layer.** The composition is `card-footer` + `flex justify-end gap-3` + `btn-outline` + `btn-primary` — all existing primitives composed inline. The variant IS the bound shape, not a new CSS surface. If a future consumer needs a different border treatment or padding rhythm, that's a fork of the variant, not a modifier — the bound shape preserves the confirm-dialog parity that's load-bearing for the affordance-vocabulary read.

---

## 5. Canonical consumers (2026-06-18 codification)

Five cena-apps screens consume the variant on first codification:

- `cena-apps/src/apps/internal/programs-create.screen.json` (Cancel → `/programs`)
- `cena-apps/src/apps/internal/programs-edit.screen.json` (Cancel → `/programs/:id`)
- `cena-apps/src/apps/kitchen/kitchen-create.screen.json` (Cancel → `/kitchens`)
- `cena-apps/src/apps/internal/patient-create.screen.json` (Cancel → `/patients`)
- `cena-apps/src/apps/kitchen/add-meal.screen.json` (Cancel → `/kitchen/:id`)

A sixth consumer (`cena-apps/src/apps/kitchen/edit-meal.screen.json`) will join when that screen's slice fires.

Each consumer's `$notes.cancel-button-emit-fallback` flag references this primitive after codification, closing the loop on the gap that motivated the slice.
