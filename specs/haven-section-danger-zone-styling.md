# Haven section-danger-zone styling — the quiet bottom-of-page destructive-action section

_Brand-spec for the `section-danger-zone` primitive — a quiet bottom-of-page section that hosts a rarely-used destructive action with explanatory context above it. Visual treatment + rationale only; structure and CSS implementation live in [`Lab/haven-ui/packages/design-system/pattern-library/components/section-danger-zone.html`](../../haven-ui/packages/design-system/pattern-library/components/section-danger-zone.html) and [`packages/design-system/src/styles/tokens/components.css`](../../haven-ui/packages/design-system/src/styles/tokens/components.css)._

---

## 1. Context

The `section-danger-zone` primitive codifies the "quiet danger zone at the bottom of a settings or record-detail page" pattern — the shape Apple, GitHub, and Stripe settings UIs all use for rarely-invoked destructive actions: top border separating it from preceding content, modest title, descriptive prose explaining what the action does and its consequences, then the action affordance (typically a `confirm-dialog-danger-outline` composed in the action slot).

Codified 2026-06-18 against the 3-use trigger per `haven-primitive-codification.md`. Three internal-app screens (patient-profile, programs-edit, kitchen-detail) and one patient-app surface (account → delete-account) all reach for this shape. Prior to codification, the three internal screens placed their destructive verb in the `page-header-default-with-actions` trailing slot — a structurally valid placement for *frequent* or *status-changing* primary actions, but too loud for actions whose frequency is "rare to never per session." Aaron's 2026-06-18 read of the rendered patient-profile surfaced the loudness; the codification consolidates the pattern at the quieter altitude.

**The inverse of `page-header-default-with-actions`.** That primitive's trailing-actions slot is the right home for the page's *primary* verb — frequent, status-changing, or pace-setting. `section-danger-zone` is the right home for the page's *terminal* verb — rare, consequential, and structurally distant from the page's regular workflow. The two primitives are sibling design-system primitives, not alternatives; a single screen could legitimately compose both (e.g., a future programs-edit with "Save changes" in the page-header AND "End program" in the danger zone).

**Brand intent.** The section must read as **"this action is here, but only when you really need it"** at a glance. The top border breaks the page's regular content rhythm so a reader's eye registers "different section." The modest title (sand-700, font-medium, sm) carries low typographic weight — it's not announcing itself like a regular section title. The descriptive prose (sand-600, body-small, max-w-2xl) explains WHAT the action does and its consequences without emotional load. The action affordance below uses the `-outline` danger variant (border + sand body, not solid red fill) so the button reads as available-but-quiet, not as a primary call to action.

---

## 2. Brand frame for this primitive

A reader scrolling to the bottom of a record-detail or settings page should see "an available destructive action with the context I need to choose it deliberately" — not "a danger warning" and not "a hidden affordance I have to hunt for." Three brand decisions follow:

- **Top border + generous mt-12 anchors the visual separation.** The section starts with a thin sand-200 top border (sand-700 in dark mode) and 48px of top margin from preceding content. This is the strongest visual signal that what follows is structurally distinct from the page's regular content — not "the next section" but "the page's terminal section." The 6px (pt-6) inside the border preserves breathing room above the title; without it the title reads as bumping the border.
- **Title + description sit at the quiet end of the type scale.** The title is `text-sm font-medium text-sand-700` — same register as field labels and small section labels, deliberately *below* the visual weight of a regular section title or page heading. The description is `text-sm text-sand-600 max-w-2xl` — body-small, sand-600 for the read-without-shouting register, max-width-2xl to prevent line lengths that flatten into block-like reads (matches the "long-form prose" treatment from the brief authoring conventions). Both spec-marked with explicit dark-mode tones.
- **The action affordance is the `outline` variant of danger, never solid fill.** Composed children in the action slot use `confirm-dialog-danger-outline` (border + sand body + dark red text on hover), never `confirm-dialog-danger` (solid red fill). The outline variant reads as "available but quiet"; the solid variant reads as "the page's primary call to action." A solid-red destructive button at the bottom of a page would re-introduce the loudness this primitive exists to prevent.

The "grew, not built" test on this primitive: scrolling to the bottom of a settings page, a reader should NOT think "oh, a warning." They should think "ah, that's where the leave-the-program action lives — let me read what it actually does." The visual treatment encodes the contract: "available, but with context, and not announcing itself."

---

## 3. Composition — visual treatment + rationale

The primitive carries one section element with three semantic children: title, description, and a composed action affordance. The CSS encodes the spacing rhythm + tone discipline; the children carry their own visual weight from their own primitives.

### 3.1 Container — `.section-danger-zone`

- **Semantic intent.** The page's terminal section — visually distinct from preceding content rhythm.
- **Visual treatment.** `mt-12 pt-6 border-t border-sand-200` (light mode); `border-sand-700` in dark mode. 48px of top margin separates it from the preceding card or section, 1px thin top border anchors the boundary, 24px of padding above the title preserves breathing room.
- **Rationale.** The mt + border + pt combination encodes "this is a structurally distinct terminal section" without reaching for stronger signals (full-width separator card, error-colored background, "Danger Zone" caption). The quiet visual treatment is the load-bearing brand decision.

### 3.2 Title — `.section-danger-zone-title` (h3 by default, semantically appropriate)

- **Semantic intent.** Concise name of the action being made available — typically the same verb as the action button below.
- **Visual treatment.** `text-sm font-medium text-sand-700 mb-2` (light mode); `text-sand-200` in dark mode. Body-small size, medium weight, sand-700 (the field-label register), 8px bottom margin to the description.
- **Rationale.** Below the visual weight of a regular section title (`.section-title` is Lora semibold heading-03) by design — the danger zone announces its availability without competing for top-of-page attention.

### 3.3 Description — `.section-danger-zone-description`

- **Semantic intent.** The "why this is destructive" prose — what happens if the user invokes the action, what stays, what's notified, whether it's reversible.
- **Visual treatment.** `text-sm text-sand-600 mb-4 max-w-2xl` (light mode); `text-sand-300` in dark mode. Body-small, sand-600 (the help-text register), 16px bottom margin to the action affordance, max-width-2xl to prevent runaway line lengths.
- **Rationale.** Long-form prose at the help-text register reads as "context, not warning." The max-width caps line length around 65ch for readable prose; without it the description spans full container width and reads as flat block content.

### 3.4 Action affordance — slot composing `confirm-dialog-danger-outline`

- **Semantic intent.** The destructive verb itself, as a confirm-dialog trigger (the primitive expects the consumer composes a confirm-dialog so the action requires a deliberate second click).
- **Visual treatment.** Inherited from `confirm-dialog-danger-outline` — `btn-danger-outline` (border + sand background + red text on hover), `fa-circle-exclamation` icon prefix, dialog markup for the confirmation flow.
- **Rationale.** Single-source — the affordance's visual treatment is owned by the composed primitive, not this section. The slot accepts any action primitive (a future variant could host an inline btn-danger-outline without a dialog, for actions that don't require confirmation).

---

## 4. Honest limits + open follow-ons

- **The primitive does not encode a "Danger Zone" caption.** Apple, GitHub, Stripe all use the explicit "Danger Zone" label above the title. We deliberately omit it for V0 — the top border + mt-12 + sand-700 title carry the visual signal without the explicit label. If we observe in user testing that the visual separation is insufficient (users miss the action, or invoke it without reading the description), the V1 follow-on is a `.section-danger-zone-caption` modifier rendering "Danger zone" or equivalent above the title.
- **Dialog markup placement.** The `confirm-dialog-danger-outline` composed in the slot includes both the trigger button AND the dialog markup. The dialog uses Preline's `hs-overlay` and lives at the page level via `position: fixed`. Ensure each page composing this primitive uses a unique dialog id (e.g., `#hs-confirm-dropout`, `#hs-confirm-end-program`) so the triggers and dialogs pair correctly when multiple destructive verbs exist on the same page.
- **Icon semantic — resolved 2026-06-18.** The prior `fa-trash-can` icon in `confirm-dialog-danger` and `confirm-dialog-danger-outline` was semantically wrong for 3 of 4 section-danger-zone consumers (dropout = exit, end-program = archive, deactivate-kitchen = pause; only delete-account is literal deletion). Replaced with `fa-triangle-exclamation` (warning triangle) — decoupled from action-shape, reads as "this is destructive, confirm before proceeding." Matches Apple / Stripe / GitHub convention for destructive-but-soft confirmation dialogs.
