# Haven pack-card styling — master/member clarity by context, not glyph

_Brand-spec for the `pack-card` primitive (kitchen Orders V3 pack surface) and its `kitchen-pack-*` row family. Visual treatment + rationale only; structure, CSS, and behavior live in [`Lab/haven-ui/packages/design-system/pattern-library/components/data-pack-card.html`](../../haven-ui/packages/design-system/pattern-library/components/data-pack-card.html) and [`packages/design-system/src/styles/tokens/components.css`](../../haven-ui/packages/design-system/src/styles/tokens/components.css) (`.pack-card` family). Codifies the CF-5 clarity treatment (Aaron feedback 2026-07-17; canon-findings §CF-5)._

> **Why a dedicated spec, not a section in `haven-surface-chrome-styling.md`.** The CF-5 disposition defaulted the place-3 home to the chrome spec, but that spec's §2.5 boundary is emphatically the *documentation-surface* chrome family (banner · nav · footer · rail-foot-keyboard-cue) and is explicitly NOT app components. `pack-card` is a kitchen-app data container; folding it into the chrome family would erode the boundary that spec spends real effort defending. Per [`define-once.md`](../../../.claude/rules/define-once.md) and the directory's one-focused-spec-per-component norm (cf. [`haven-quantity-stepper-remove-affordance-styling.md`](haven-quantity-stepper-remove-affordance-styling.md), [`haven-search-trigger-styling.md`](haven-search-trigger-styling.md)), pack-card earns its own home. — HVD, 2026-07-19.

---

## 1. Context

The `pack-card` is the kitchen Orders V3 pack surface's day-group container: a card holding a section heading ("Pack list · For delivery Wed Sep 16"), a **tri-state group checkbox** that marks every row in the group packed, and a `rows` slot iterating `kitchen-pack-row` instances (one household per row, each row's checkbox marking that household packed).

The card composes **two checkbox registers with deliberately different label-association models**, encoded in the fragment meta:

- **Header group checkbox — NOT `<label>`-wrapped.** "Click the title → mark everyone packed" is a strong, destructive-feeling bulk action easy to fat-finger, so the title is intentionally not a tap target.
- **Per-row checkbox — `<label>`-wrapped** (`kitchen-pack-row-toggle`). The whole row is the tap target because per-row mistakes are cheap to revert.

Aaron's feedback (2026-07-17): *"it has two checkboxes and the layout doesn't feel clear."* Before this treatment the two checkboxes were the same size on the same left edge, so nothing signalled *master-control vs member-rows* — they read as two peer checkboxes stacked. (The separate structural-alignment defect — the fragment rendering on browser defaults because the family had zero backing CSS — was fixed in the same finding; this spec covers the clarity treatment layered on top of that fix.)

---

## 2. Brand frame — differentiate the containers, not the controls

The governing call: **the honest lever is context, not glyph.** Restyling one checkbox to look different from the other (a bigger box, a color, a different glyph) would invent a new visual token — a brand-spec decision in its own right — and fight the shared `.checkbox` canon that every Haven checkbox reads from. Two identical checkboxes read unambiguously as master + members the moment their *surroundings* say so.

So the treatment makes the **containers** differ, using two register-safe moves drawn entirely from existing Haven vocabulary — zero new hexes, zero inline styles, the shared checkbox glyph untouched:

1. **Grouping — a quiet warm header band** puts the master checkbox in a *toolbar/section-header context*.
2. **Nesting — a one-level row indent** puts the member checkboxes in a *nested-list context*.

This is the minimum treatment that resolves the register confusion while preserving the two-tap-target asymmetry the fragment meta deliberately encodes (the master stays a bare non-label control; the rows stay whole-row `<label>` tap targets — `w-full` inside the item keeps the row tap target intact).

The "grew, not built" test: a pack surface using this treatment should read as a card whose header is quietly set apart and whose rows are quietly nested — the way a well-made list *grew* a header, not the way a UI *bolts on* a toolbar. The brand work is the restraint (warmth + structure carry the tier; the controls stay canon), not new chrome.

---

## 3. Treatment

### 3.1 Grouping — the header band

The `.pack-card-header` becomes a **quiet warm band**, and the card body stays the canonical white card figure:

- **Band ground — `bg-sand-50` (light) / `bg-sand-950` (dark).** The band steps *toward the page ground* in both modes (light page ground is sand-50; dark shell is sand-950), so the header reads as the surface's ground surfacing up through the card's white body (`bg-white` / dark `bg-sand-900` — the canonical `.card` figure per components.css `.card`). This is the same quiet-ground logic the active-nav "Quiet Ground" pattern and `.card-header` chrome already use: warmth marks a distinct region without a saturated slab (digest ruling #5, "chrome grounds"; Principle 6, restraint as default).
- **Bottom border — `border-b border-sand-200`** (dark `sand-700`). The hue-adjacent warm border does the crisp demarcation the band tone is deliberately too quiet to do alone.
- **Clip — `.pack-card` carries `overflow-hidden`** so the band's square top corners clip to the card's `rounded-md` (11px card radius, digest ruling #7). Without it the band would poke past the rounded card.
- **Title — `font-serif font-semibold text-heading-04 text-sand-800`** (dark `sand-200`). Lora commands the band the way it commands every card surface (§ Lora-commands discipline); the serif title + the band + the border together reframe the top checkbox as a section-header/bulk control rather than a first row.

**Band-tone ratification + iteration path.** `sand-50` is the restrained end of a defensible range and is **ratified as shipped**: in the render, the master/member tier is carried primarily by the *indent* (§3.2) plus the border and serif title, with the band contributing a faint warmth rather than a loud slab — which is the correct division of labor (structure carries the tier; color reinforces). Because sand-50 against a white body is a near-whisper (~0.97:1), the named render-time iteration path is *up one step to `sand-100`* — the canonical "quiet distinct region" tone (active nav, hover, chrome) — **only if** the band reads as absent under Aaron's eye. This mirrors the chrome spec's sand-100→sand-200 separator iteration discipline (ship restrained, step up only if the demarcation reads as missing). Iteration *down* is not available (sand-50 is already the page-ground floor). The dark band (`sand-950`) is the symmetric inversion and rides the same review.

### 3.2 Nesting — the one-level row indent

- **`.pack-card-rows .kitchen-pack-row` overrides to `ps-8` (32px)**, one 16px level inboard of the header's group checkbox (`px-4` = 16px). The master checkbox sits at the outer edge; the member-row checkbox column sits one level in — the universal tree signal that the master *owns* the indented members.
- **Step ratified as `ps-8`.** 16px is exactly one step on the spacing scale and reads as a single, unambiguous level of nesting in both desktop and mobile renders — enough to signal hierarchy, not so deep it detaches the rows from the card gutter. The `w-full` label preserves the whole-row tap target, so the deliberate two-tap-target model (§1) is untouched by the indent.
- Right padding is unaffected — the base `px-4` right side survives; only the start (inline-left) padding is overridden.

### 3.3 Why not restyle a checkbox

Named explicitly because it is the tempting wrong lever: making the group checkbox visually distinct from the row checkboxes (larger, tinted, a different glyph) would (a) invent a new visual token that owes its own brand-spec decision and dark-mode pair, (b) fight the shared `.checkbox` canon every Haven surface reads, and (c) route the master/member signal through a *control* difference the user must decode, rather than a *context* difference they read pre-attentively. Context-differentiation is the more restrained lever and the more legible one — it needs no new vocabulary and no glyph decode.

---

## 4. What this spec does NOT cover

- **The structural-alignment fix + the placeholder codification.** The `pack-card` / `kitchen-pack-*` family's base layout (the `flex items-center` header, `flex items-start` row, card-shape + sand tokens) is the CF-5 structural fix, documented in the components.css block and canon-findings §CF-5 — not a visual-brand decision this spec re-decides.
- **The tri-state group-checkbox behavior.** Indeterminate state, click-toggles-all, partial-state rendering are behavior owned by the kitchen-side `pack-list-checkboxes` script + the fragment meta, not brand treatment.
- **Multi-day pack surfaces.** One delivery day per card is the working assumption; multi-day expands as sibling pack-cards. Whether a surface stacks siblings or tabs them is a consumer/product decision.
- **Consumer adoption specifics** (which kitchen screen composes pack-card, how the rows bind) — that is the kitchen app's composition layer, not this primitive's brand spec.

---

## 5. Source

2026-07-17: Aaron's feedback on `data-pack-card` — *"a gross misalignment between the checkbox and the label. also it has two checkboxes and the layout doesn't feel clear."* The alignment half was an uncodified-primitive structural defect (family had zero backing CSS); this spec covers the *clarity* half — the Haven Steward's grouping+nesting treatment (2026-07-17), ratified and codified here by the Haven Visual Designer (2026-07-19) with the sand-100 band iteration path named for render-time review. First application of the [`failure-shape-sibling-sweep`](../../../.claude/rules/failure-shape-sibling-sweep.md) rule; the sweep found `pack-card` the sole same-failure instance (`kitchen-meal-row`'s bulk-select master+rows is the closest cousin but is clean — its `data-table` thead/tbody + check-cell column already supplies the tier differentiation the card had to add). Full trail: canon-findings §CF-5.
