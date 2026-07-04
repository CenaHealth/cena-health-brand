# Haven quantity-stepper remove-at-min styling — the trash-at-floor affordance

_Brand-spec for the `quantity-stepper` primitive's opt-in remove-at-min affordance (`data-remove-at-min`). Visual treatment + rationale only; structure, CSS, and behavior live in [`Lab/haven-ui/packages/design-system/pattern-library/components/quantity-stepper.html`](../../haven-ui/packages/design-system/pattern-library/components/quantity-stepper.html), [`packages/design-system/src/styles/tokens/components.css`](../../haven-ui/packages/design-system/src/styles/tokens/components.css), and [`packages/design-system/src/scripts/components/quantity-stepper.js`](../../haven-ui/packages/design-system/src/scripts/components/quantity-stepper.js)._

---

## 1. Context

The `quantity-stepper` primitive is Haven's `−` / value / `+` control for adjusting a numeric quantity. Its default behavior disables the decrement button at the floor (`data-min`). The **remove-at-min** affordance is an opt-in variant (Instacart-style): when the floor is the "present" floor — 1 for a cart or order line item — the decrement control becomes a REMOVE action at the floor rather than a dead disabled button. Below the floor means the item is gone, so the control that would take you below it removes it.

The variant earned codification from Aaron's product observation (2026-07-04): a grocery-cart quantity stepper where, at qty 1, the `−` becomes a trash icon. One control carries a second verb — decrement above the floor, remove at the floor. It is a denser but more efficient affordance: no separate "remove" button competing for space in a tight line-item row.

**Why the primitive, not a consumer composition.** Wiring "swap the icon and change the click behavior at value === 1" into every cart / order surface re-derives the same state logic, splits the `quantity-remove` contract across consumers, and risks each consumer inventing its own remove-event name. Codifying it at the stepper absorbs the shape so every cart/order surface inherits one contract.

**Opt-in, not default.** Kitchen quantity grids (`meal-qty-cell`) and any future dose / count steppers want disable-at-floor, not remove. The affordance is gated behind `data-remove-at-min` so existing steppers are untouched.

---

## 2. Brand frame for this affordance

The remove-at-min variant introduces **one new visual event**: the decrement glyph swaps from minus (`fa-minus`) to trash (`fa-trash-can`) at the floor. Every other aspect of the control — pill chrome, button size, value cell, hover, focus — is inherited from the existing `quantity-stepper` canon verbatim.

The governing brand tension, named by Aaron at codification: **trash is visually dense — "not pretty" — but the affordance is efficient.** This spec resolves the tension by making the trash as quiet as the affordance is dense:

- **The trash carries the same quiet `text-sand-700` weight as the minus it replaces.** It is NOT rendered in destructive red (`danger`/`error`). A red trash would read as "warning — irreversible destruction ahead," which oversells a reversible, low-stakes action (re-add the item; it is a cart, not a database record). The quiet trash reads as "this control now removes" without alarm. Destructive-red is reserved for genuinely destructive, hard-to-reverse actions elsewhere in Haven; a cart line-item remove is not that.
- **The swap is instantaneous — no transition on the glyph change.** Per Haven's motion restraint, animating the minus→trash morph would add motion to a routine adjustment. The glyph is simply minus above the floor and trash at the floor.
- **No confirmation step.** Clicking the trash dispatches `quantity-remove` immediately; the consumer removes the line. The action is reversible (re-add from the menu), so a confirm dialog would be ceremony. If a specific consumer surface has a costly-to-reverse removal, that surface adds its own confirm — the primitive does not build one in.

The "grew, not built" test: a cart line-item row using remove-at-min should read identically to a hand-authored equivalent — same pill stepper, with a trash glyph in the minus slot at qty 1. The affordance's brand work is the quiet-trash restraint, not new chrome.

---

## 3. Treatment

### 3.1 Glyph swap

- **Above the floor.** `fa-minus` shows (class `quantity-stepper-icon-decrement`); trash is hidden. The control decrements normally.
- **At the floor.** `fa-trash-can` shows (class `quantity-stepper-icon-remove`); minus is hidden. Both glyphs live in the decrement button markup; CSS toggles which is visible, keyed on the `data-at-min="true"` state attribute the JS module sets. The button stays enabled (it is not disabled-at-floor like the default variant).
- **Weight + color.** Both glyphs inherit `.quantity-stepper-btn`'s `text-sand-700` (dark: `text-sand-200`). The trash is the same size and weight as the minus. No color change, no size change.

### 3.2 Label + a11y

- **aria-label swaps with the glyph.** Above the floor the button reads its initial label (default "Decrease quantity"; contextual labels like "Decrease quantity of Pollo guisado" are preserved). At the floor it reads the remove label (default "Remove"; override via `data-remove-label` for contextual labels like "Remove Pollo guisado"). A screen-reader user hears the control's verb change, matching the visible glyph change.
- **The trash is `aria-hidden` (decorative);** the button's accessible name carries the meaning. Same pattern as the minus/plus glyphs.

### 3.3 Behavior contract (brand-visible)

- At the floor, clicking dispatches a bubbling `quantity-remove` CustomEvent (`detail = { value }`) instead of clamping. The consumer owns the actual line-item removal.
- **Half-state discipline:** the trash the surface renders is backed by a real dispatched event — the affordance is not a promise the primitive can't keep. The consumer's job is to listen for `quantity-remove` and remove the line; the primitive's job (dispatch the honest event, swap the label, swap the glyph) is complete. See [`.claude/rules/half-state-promises.md`](../../../.claude/rules/half-state-promises.md).

---

## 4. What this spec does NOT cover

- **Consumer adoption in a specific surface.** Whether a given cart/order surface uses remove-at-min is that surface's product decision — and it depends on the surface's quantity model. The existing `cart-panel.html` PL page uses a `data-min="0"` portion-picker model (0 = "none," item stays listed), which is a different model from the 1-floor add/remove-line model remove-at-min is built for. Adopting remove-at-min into a portion-picker surface is a product change (floor → 1, 0 → removed), not a primitive swap. Left as a flagged follow-up, not silently applied.
- **Multi-step-to-remove.** Some carts decrement to 0 and keep the row visible with an "add again" affordance instead of removing. That is a different pattern (soft-remove); remove-at-min is hard-remove-at-floor. If a soft-remove pattern earns codification, it is its own variant.
- **Destructive-confirm removal.** If a consumer's removal is costly to reverse, the confirm is the consumer's; the primitive intentionally ships no confirm step (cart removal is reversible).

---

## 5. Source

2026-07-04: Aaron's grocery-cart screenshot — a quantity stepper where the `−` becomes a trash icon at qty 1. He named the tradeoff himself ("trash is visually dense so it's not pretty but it's a really efficient affordance") and asked to codify the pattern into Haven's relevant canonical components. Codified on the `quantity-stepper` primitive (the reusable control), opt-in via `data-remove-at-min`. The quiet-trash restraint in §2 is this spec's resolution of the density-vs-efficiency tension Aaron flagged.
