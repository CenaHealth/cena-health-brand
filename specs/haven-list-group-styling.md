# Haven list-group styling — the labeled-row container family

_Brand-spec for the `list-group` primitive family across both the existing variants (basic, icons, descriptions, trailing, actionable, states, flush, striped, bound) and three new row-shape variants (`list-group-row-action`, `list-group-row-toggle`, `list-group-row-control`) added in the 2026-06-15 codification slice for cena-apps patient-app account screen. Visual treatment + rationale only; structure and CSS implementation live in `Lab/haven-ui/packages/design-system/pattern-library/components/data-list-group.html` and `packages/design-system/src/styles/tokens/components.css`._

---

## 1. Context

The `list-group` family is haven's general-purpose **labeled-row container** — a bordered vertical stack of related items. It is the second-most-load-bearing container after `card` in the document district and is the default vocabulary for any "list of named items, optionally with secondary content, optionally interactive" surface: settings rows, profile fields, navigation lists, status roll-ups, patient rosters, schedule items, notification toggles.

The family has shipped + been in use across haven-ui's pattern-library since the initial design-system landing (no brand spec authored at that time). This file closes that debt and extends the family with three new row-shape variants surfaced by the cena-apps patient-app account screen's pre-emptive codification needs.

**Why now (debt close).** The 2026-06-15 Haven Steward dispatch flagged `list-group` as having shipped PL + components.css with no brand-spec entry — a `haven-primitive-codification.md` five-place check failure. Per Path B (codify-at-creation), this spec lands ahead of (or alongside) the next consumer slice that touches the family.

**Why now (new variants).** The cena-apps patient-app account screen wireframe (`Lab/cena-apps/apps/patient/wireframes/account/spec.md`) introduces a recurring composition shape: a labeled row whose **value column doubles as a trailing affordance** — an edit pen, an inline toggle, or an inline segmented-control. Three uses appeared in one wireframe (Profile, Notifications, Profile/Language); a fourth wireframe section (Outcomes) reuses the action shape. Per `generative-determinism.md`'s shape trigger and the Steward's promotion floor, this is contract-first work, not a one-off composition.

---

## 2. Brand frame for this family

The list-group is, structurally, the **inverse of the card**: where a card is a discrete bounded region holding mixed content (heading + body + actions), the list-group is a flat ribbon of parallel rows whose readability depends on the row-to-row rhythm staying quiet. The brand decisions therefore live almost entirely at three layers:

- **Border vocabulary.** The outer 1px `sand-200` border + per-row `sand-200` divider is the family's silhouette. It rhymes with the card border, the table border, and the blockquote left-rule (all use the brand's `border-default` neighborhood). The family reads as a sibling of these existing structural rules, not as new chrome — a clean expression of the "grew, not built" test.
- **Typography weight on the label.** The row label is `sand-900` semibold — the highest weight any sand-family text takes outside Lora headings. This is intentional: the label IS the row's purpose. Every other element in the row is either secondary or affordance.
- **Trailing-slot vocabulary.** Trailing content is the only element in the row that can carry hue (badges with severity, teal chevrons for navigation affordance). Hue at the trailing slot acts as **the row's status or affordance signal**, not as decoration — the leftmost-to-rightmost reading is `[icon: category] → [label: what] → [description: optional why] → [trailing: status or action]`. Hue at trailing earns its place when there's a status to declare or an action to invite; otherwise the slot stays neutral or empty.

Brand restraint per Principle 6: the family stays quiet by default. The `striped` modifier exists for dense data scans but is the only variant that asks the row-rhythm to break. Every interactive variant (`-action`, the new `-row-action` / `-row-toggle` / `-row-control`) earns affordance distinctness through hover/focus surface shifts, never through always-on color saturation.

The "grew, not built" test on this family: a settings screen using only basic list-groups + cards should feel like a single coherent surface. If the rows feel like a widget pasted onto the card, the row padding has compressed or the divider has thickened. The default `px-4 py-3 gap-3` with hairline dividers is what produces the woven-into-the-document reading.

---

## 3. Existing variants — visual treatment + rationale

### 3.1 `list-group` (container) + `list-group-item` (basic row)

- **Semantic intent.** The default container + row. A bordered vertical stack of named items with no secondary content. The simplest entry point to the family.
- **Visual treatment.** Container: 1px `sand-200` border, `rounded-lg` radius (8px), `overflow-hidden` so the radius clips the per-row dividers cleanly. Row: `flex items-center gap-3 px-4 py-3 text-sm text-sand-800` with a per-row `border-b border-sand-200`, `last:border-b-0` removing the divider on the final row. Dark mode mirrors: container `border-sand-700`, row `text-sand-200 border-sand-700`.
- **Rationale.** The 8px radius + hairline divider is haven's structural-container language (matches card border, table cell border). The 12px gap + 16px horizontal padding is the family's silhouette: enough breathing room for an optional leading icon, the label, and an optional trailing element to coexist without crowding. The 12px vertical padding gives a 44px+ touch target naturally (text-sm = 14px line-height ≈ 21px + 24px combined padding = 45px) without needing per-component overrides. `text-sand-800` body weight is intentionally lighter than `sand-900` semibold reserved for the title slot — the basic row carries less hierarchy than the description-row variant below.
- **Quality-test notes.** Quiet-mode: a basic list-group of 5–7 rows on a sand-50 page reads as a quietly-bordered ribbon, each row legible at 24px viewing distance. Grew-not-built: the `rounded-lg` matches the card radius exactly, so a card containing a flush list-group reads as one continuous element. Density consumer (`--density-default: compact`) tightens to `px-3 py-2 gap-2`, preserving the 44px touch floor through line-height alone — flagged for verification on tablets.

### 3.2 `list-group` with `list-group-item-icon`

- **Semantic intent.** Each row gains a leading icon (FA Pro solid). Used when the icon helps the reader scan or when the icon carries category meaning (a pill icon for medications, a stethoscope for clinical content).
- **Visual treatment.** Icon: `text-sand-600 text-base flex-shrink-0` (16px FA Pro solid, sand-600 neutral). Dark mode: `text-sand-400`.
- **Rationale.** Icons in the basic-row position are **wayfinding, not decoration** — they help the eye land on the right row in a long list. The sand-600 weight is one stop lighter than the row label, so the icon recedes when the reader is reading and surfaces when the reader is scanning. Pulling the icon higher (sand-700, sand-800) would compete with the label; pulling it lower (sand-500) would fade it on warm-ground surfaces. Cena reserves teal for state-changing commits per `DESIGN.md §Brand-taste` — `list-group-item-icon` never carries teal by default. When a row IS interactive (the actionable variant), the icon still stays sand-600; the hover/focus surface shift carries the affordance signal, not the icon hue.
- **Quality-test notes.** Quiet-mode: icons fade into the reading rhythm. Grew-not-built: the 12px gap between icon and label matches the gap pattern in card headers and button-icon labels — one spacing vocabulary across the system.

### 3.3 `list-group-item-content` with `list-group-item-title` + `list-group-item-description`

- **Semantic intent.** Two-line row: a primary label + a secondary description (MRN, last-visit date, contextual subhead). Used when one line per row doesn't carry enough information.
- **Visual treatment.** Title: `text-sm font-semibold text-sand-900 truncate`. Description: `text-xs text-sand-600 mt-0.5 truncate`. Both truncate at the content edge so wide rows don't break the family's row-rhythm.
- **Rationale.** Title's `sand-900 semibold` is the heaviest weight any list-group text takes — the title IS the row's identity, and this is the family's "Lora-commanding-Source-Sans-working" formula expressed at the row scale: the title commands, the description supports, the trailing slot punctuates. The 2px vertical gap (`mt-0.5`) is tighter than the family's default 12px gap because title and description are one semantic unit — pulling them apart would let the reader misread them as two siblings. The truncate-on-both is a deliberate constraint: the family's promise is a clean row rhythm, and a row that wraps its description into three lines breaks that promise. If the content cannot be truncated meaningfully, the consumer needs a different family (a card with `card-body` prose, or a 2-column descriptive table).
- **Quality-test notes.** Quiet-mode: a roster of 8 patient rows with title + description reads as a clean reference list, like a printed contact-roster. Grew-not-built: the description's `sand-600` neutral matches the icon weight — one supportive-content color across the row's leading and content slots.

### 3.4 `list-group-item-trailing`

- **Semantic intent.** A right-aligned slot for status, count, action, or affordance content. The row's "punctuation."
- **Visual treatment.** `ml-auto flex-shrink-0 flex items-center gap-2`. No color, no padding — the slot is a compose surface for whatever the consumer puts inside (a `badge`, an icon, a count, a chip).
- **Rationale.** The trailing slot has no opinion of its own — it is a position, not a treatment. Its visual weight comes from what the consumer composes inside it. The 8px gap between trailing children matches the badge-spacing pattern elsewhere in the system (chip clusters, badge groups). `ml-auto` is what produces the row's stable left-content / right-trailing rhythm without requiring a fixed-width column; rows with no trailing content collapse cleanly to a left-aligned label.
- **Quality-test notes.** Quiet-mode: a trailing badge or chevron acts as the row's status/affordance cue; without it, the row reads as informational-only. Grew-not-built: trailing badges adopt their family's chip vocabulary (`badge-warning`, `badge-success`, `badge-pill badge-error`) directly — one badge language across the whole system, not a list-group-specific variant.

### 3.5 `list-group-item-action` + `.active` + `.disabled`

- **Semantic intent.** A row that **acts as a link or button** — the row itself is the affordance, not a control inside it. Used for navigation lists, settings menus where each row opens a detail surface, or any list where the row's identity is "tap this to go there."
- **Visual treatment.** Inherits the basic-row layout. Adds: `w-full text-left cursor-pointer hover:bg-sand-100 focus:bg-sand-100 transition-colors`. `.active` state: `bg-primary-50 text-primary-700 border-b-primary-100` (teal-tinted ground + teal text). `.disabled`: `opacity-50 pointer-events-none`. Dark mode: hover/focus shifts to `sand-800`; active shifts to `primary-900/20` + `primary-400` text.
- **Rationale.** Hover and focus produce the same surface shift (sand-100) so keyboard and mouse users see the same affordance read — one of haven's standing accessibility patterns. **The `.active` state is the family's one teal moment.** Cena reserves teal for state-changing commits (per `DESIGN.md §Brand-taste`); selecting a navigation row IS a state change ("I am now on this surface"), so teal earns its place here. The teal background uses `primary-50` (the lightest interactive teal stop) so the active row reads as "highlighted-belonging-to-the-section" rather than "shouting." Text shifts to `primary-700` for AAA contrast (8.96:1 on sand-50) while staying within the brand's reserved-for-commits teal vocabulary. The active border-bottom uses `primary-100` instead of `sand-200` so the divider beneath the active row doesn't visually cut it off — the row holds together as a single highlighted shape.
- **Quality-test notes.** Quiet-mode: hover/focus is detectable but not loud — `sand-100` is one stop warmer than the page ground, signaling "this row will respond" without breaking the row rhythm. Grew-not-built: the active teal matches the brand's `color-primary` exactly (teal-700) so an active navigation row visually rhymes with primary teal buttons and links elsewhere on the page. Accessibility: focus ring inherits from the project's focus-visible standard; consumers MUST verify their app shell's focus-ring renders correctly on the warm `sand-100` hover ground (the WCAG 1.4.11 non-text-boundary check).

### 3.6 `list-group-flush`

- **Semantic intent.** A list-group embedded inside a card or other parent container. Drops the outer border + radius so the rows sit flush against the parent's edges.
- **Visual treatment.** `border-0 rounded-none` on the container; per-row dividers preserved.
- **Rationale.** When a list-group is the contents of a card, doubling the borders (card border + list-group border) reads as a frame-inside-a-frame — visual noise that breaks the "one container" reading. Flush removes the inner frame so the card's border carries the silhouette; the list-group provides only the row dividers. This is the family's most-used variant in the cena-apps account screen (every list-group inside an account section uses `list-group-flush` because every list-group is inside a card).
- **Quality-test notes.** Quiet-mode: a card with a flush list-group reads as one bordered region with internal row dividers — exactly the "grew-not-built" reading for a settings or account surface. Grew-not-built: the row dividers visually align with the card's bottom edge because `overflow-hidden` on the parent card clips the final row cleanly.

### 3.7 `list-group-striped`

- **Semantic intent.** Alternating row backgrounds for dense data scans. Used when the consumer is comparing values across rows (a price list, a schedule, a value-comparison table) and the row-rhythm alone doesn't carry enough rhythm.
- **Visual treatment.** Odd rows get `bg-sand-50` (the page-ground itself). Dark mode: `bg-sand-800/50`.
- **Rationale.** The stripe uses `sand-50` against the row's inherited surface (typically `sand-50` page or card body) — a near-imperceptible warm-on-warm shift on the page surface, more visible when the list-group is inside a `card` (sand-50 stripe on the card's white-ish body). This is restraint at the stripe layer: a Zebra-tabular pattern with high-contrast banding would shout at the reader; sand-50 banding whispers, supporting the scan without dominating it. The variant should be reached for sparingly — most settings/navigation surfaces don't need it, and using it on a low-density list is over-styling.
- **Quality-test notes.** Quiet-mode: the stripe should be detectable when the reader is scanning a column of values, invisible when the reader is reading a single row's content. Grew-not-built: the sand-50 matches the page-ground exactly, so a striped list-group on a card reads as "the page itself banding through the card window," not as an injected band. Consumer guidance: skip the stripe on lists of < 5 rows or lists with prominent leading icons (the icons + dividers already carry enough rhythm).

### 3.8 `list-group-bound` + `list-group-row` (emit-binding pair)

- **Semantic intent.** The emit-pipeline binding contract. `list-group-bound` is the each-target wrapper (the slot the renderer fills with N row instances); `list-group-row` is the row template with `data-field` attributes naming the bound primitives. Used by haven's compose pipeline to render data-driven lists.
- **Visual treatment.** Identical to a basic list-group with title + description + trailing — this is a binding contract, not a visual variant. The visual layer is whatever the bound row template composes.
- **Rationale.** The brand has no opinion on the binding contract beyond "what comes out at render time must follow the rest of this spec." The compose pipeline owns the structural rules; this spec owns the visual treatment of the rendered output. If a bound row composes title + description + trailing, the rendered list reads exactly like §3.3 + §3.4 above; the binding is invisible at the visual layer.
- **Quality-test notes.** Quiet-mode: a rendered bound list is indistinguishable from a hand-authored basic list of the same shape. Grew-not-built: this is the family's deepest "infrastructure" moment — the binding is the contract that lets the same brand-correct row appear across every consumer surface (settings, rosters, schedules) without re-authoring per surface.

---

## 4. New variants — labeled-row + trailing-affordance

The three new variants share one shape: a left-aligned label and a right-aligned trailing affordance, with the affordance occupying the value-column position (the place where a description would normally sit in §3.3). The reader's mental model: **"label : affordance."** The variants differ only in what kind of affordance lives in the trailing slot.

These variants are first-class bound-row shapes, registered as `list-group-row-action`, `list-group-row-toggle`, `list-group-row-control` in the COMPONENT-INDEX. They compose with `list-group-flush` (the typical embedding pattern inside a card).

### 4.1 `list-group-row-action`

- **Semantic intent.** A labeled row whose trailing affordance is a single-purpose icon-action — most commonly an edit pen ("tap to edit this value"), an add-icon ("tap to log a new value"), or a navigation chevron ("tap to drill into detail"). The whole row is the click target; the icon is the affordance signal.
- **Visual treatment.** Inherits `list-group-item-action`. Label uses `text-sand-900` semibold at `text-sm`. Value content (the data column) uses `text-sand-700` at `text-sm`, right-aligned but left of the trailing icon. Trailing icon: FA Pro solid at `text-base` (16px), `text-sand-600` neutral. Hover/focus surface shift: `sand-100` (inherits from `list-group-item-action`). Compose: `<a class="list-group-item-action list-group-row-action">` with three children: `<span class="list-group-row-label">`, `<span class="list-group-row-value">`, `<i class="fa-solid fa-pen list-group-row-trailing-icon">`.
- **Rationale.** This is the cena-apps account screen's most-used row shape (Profile section uses it 4 times — name/email/phone/address — and Outcomes uses it 3 times — weight/BP/A1C). The label staying at `sand-900` semibold ensures the field name is the primary read; the value at `sand-700` is secondary (the user reads "what's my email" then verifies the value). The trailing icon stays at `sand-600` neutral by default — affordance presence is signaled by the row's interactive state (hover/focus surface shift), not by always-on icon color. Cena reserves teal for state-changing commits; opening an edit dialog is a state-change candidate but the action of *opening the dialog* is not yet a commit (the commit happens inside the dialog with the Save button). Keeping the icon neutral preserves the brand's "teal punctuates" discipline. Two pens on a screen of 8 list-group rows is a quiet, scannable surface; two teal pens would dominate the page.
- **Quality-test notes.** Quiet-mode: a Profile section with 4 row-action rows reads as a settings list whose values are visible at a glance, with a soft "you can tap any of these to edit" affordance. Grew-not-built: the row-action shape rhymes with the existing `list-group-item-action` (same hover, same focus, same touch target floor) — consumers reading the family from the docs see this as "the actionable variant with a structured value column" not as "a new component." Accessibility: the trailing icon needs an `aria-hidden` since the row-level button carries the accessible name ("Edit name", "Edit email"); the `aria-label` lives on the button, not the icon. Open question (flagged for Aaron): should the icon ever shift to `sand-800` on hover for stronger affordance cuing? Read says no (the surface shift is enough), but worth verifying with the patient-app QA pass.

### 4.2 `list-group-row-toggle`

- **Semantic intent.** A labeled row whose trailing affordance is an inline `haven.toggle` — used for binary preferences (Phone OK, SMS OK, Email OK in the Notifications section). The toggle is the affordance and the value display simultaneously; the row carries no separate value column.
- **Visual treatment.** Inherits `list-group-item` (NOT `-action` — the row is not itself clickable; only the toggle is). Label uses `text-sand-900` semibold at `text-sm`. Toggle: standard `haven.toggle` placed in the `list-group-item-trailing` slot, right-aligned, with `gap-3` between label and toggle. No row-level hover state (toggle owns its own hover/focus). Touch target: ensured by the toggle's own `44×44` minimum at the toggle element. Compose: `<div class="list-group-item list-group-row-toggle">` with `<span class="list-group-row-label">` and `<label class="toggle">` as children.
- **Rationale.** The label stays at `sand-900` semibold to match `-row-action`'s label register — the user reads "Phone call OK" as the primary identity of the row, with the toggle as the affordance. The row is intentionally **not** `list-group-item-action`; tapping anywhere in the row should not toggle the preference (the toggle has a clear hit area; expanding the hit area to the whole row would interfere with the user's expectation that toggles are tap-the-switch). Cena reserves teal for state-changing commits, and the toggle's ON state IS a state-changing commit — so the toggle uses teal when active (per haven's existing toggle spec). The label staying sand-neutral lets the toggle's teal-on-state read clearly against it. Helper text ("Cena will only contact you for care-related reasons.") lives outside the list-group, in the card body above it, not as a per-row description — the policy applies to all toggles, not to each individually.
- **Quality-test notes.** Quiet-mode: a Notifications section with 3 toggle rows reads as a clean preference list. The OFF state is sand-neutral; the ON state has a teal punctuation per toggle — three ON toggles read as "three teal punctuations" on a sand surface, not as "the whole row is teal." Grew-not-built: the toggle inheriting from `haven.toggle` means future toggle improvements (focus ring, label-association behavior) propagate automatically. Accessibility: the label is associated to the toggle via `<label for="...">`, not by visual proximity — the row layout doesn't change that contract. Open question: should the row's max-width be capped so toggles never drift more than ~400px from their label? On mobile this is not a concern; on tablet/desktop, the toggle drifting far right might break the label↔toggle association at a glance. Worth verifying in tablet/desktop layouts.

### 4.3 `list-group-row-control`

- **Semantic intent.** A labeled row whose trailing affordance is an inline rich control — most often a `haven.segmented-control` (per cena-apps account screen's Language row: English / Español), but extensible to any inline control that fits the trailing-slot footprint (chip-group, small select, inline radio-group of ≤3 options). Distinct from `-row-toggle` (binary, dedicated toggle primitive) and `-row-action` (single-purpose icon-action that opens a dialog).
- **Visual treatment.** Inherits `list-group-item`. Label uses `text-sand-900` semibold at `text-sm`. Control: dropped into the `list-group-item-trailing` slot via the row's `@slot: control`, right-aligned. The control inherits its own brand-correct treatment (e.g., `haven.segmented-control` has its own brand spec; this row doesn't override). Row may need `min-h-12` (48px) on tablet/desktop to accommodate the control's own height without breaking the row-rhythm. Default `max-w-[200px]` on the embedded control protects mobile layouts (consumer can opt out via composition). Compose: `<div class="list-group-item list-group-row-control">` with `<span class="list-group-row-label">` and the control slot as children.
- **Rationale.** This is the most flexible of the three new variants — it accepts any haven control that fits the trailing footprint. The row itself stays opinion-free at the visual layer; the brand correctness is delegated to the embedded control. Why a separate variant from "just put a segmented-control in the trailing slot of a basic row": cap-19 surfaces this row shape in one account screen today, but the patient-app brief expects it across Outcomes preferences, Care-team contact-method selection, and future per-patient toggles between treatment-plan modes — a recurring shape across multiple wireframe sections. Codifying it as a named variant means future surfaces inherit the row-height handling + label-register + control-positioning without rediscovering. The row staying non-interactive (no `-action`) is deliberate: the control owns its own interaction; tapping the row label area is intentionally inert.
- **Quality-test notes.** Quiet-mode: a Language row with a 2-segment segmented-control reads as one quietly-balanced row — label on the left, two-segment choice on the right. Grew-not-built: the row's structural shape matches `-row-toggle` (label + control), so the family reads coherently when both variants appear in the same card (e.g., Notifications + Language under a unified Preferences card). Accessibility: the embedded control owns its own label-association; the row's visual label is informational, not the control's accessible name.

---

## 5. Cross-cutting decisions

What this family asserts about the brand, taken as a system:

- **The label is the row's identity.** Every variant uses `sand-900 semibold` for the primary label. This is the family's "Lora-commanding-Source-Sans-working" formula at the row scale: the label commands, secondary content (description, value, control) supports.
- **Teal is reserved for state-changing commits, not for affordance signal.** The only teal moment in the family is `list-group-item-action.active` (a navigation row whose tap commits the user to a different surface) and toggle-ON (which IS a state-change). Icons, action triggers, and toggle-OFF states stay sand-neutral. This protects haven's reserved-teal discipline across the family.
- **Hover and focus produce the same surface shift.** All actionable variants (`list-group-item-action` and `list-group-row-action`) use `sand-100` for both states. Keyboard and mouse users see the same affordance signal — one of haven's standing accessibility patterns.
- **Hairline dividers are the family's silhouette.** No variant thickens or recolors the divider beyond the canonical `sand-200`. The active-state border-bottom variation in §3.5 is the only exception, and it uses the teal-tinted family color so the divider doesn't visually cut off the active row.
- **Trailing-slot hue is earned, not default.** A row's trailing slot stays neutral (sand-600 icons, no hue) unless a status is being declared (severity badges) or a state is being shown (toggle-ON teal). This protects the family from drifting into chip-soup readability across long lists.
- **Density consumer respected.** The `--density-default: compact` consumer tightens row padding to `px-3 py-2 gap-2`; new variants inherit this behavior automatically. Per-variant density overrides are not authorized — density is set once on the parent container.
- **No variant introduces new typography weights.** All variants use Source Sans 3 in the `text-sm` / `text-xs` register. The family does not earn a Lora moment — Lora is reserved for headings and the document district's authority register; rows are working-voice, not authority-voice.

---

## 6. Open issues / future work

1. **Per-row description-AND-trailing-affordance composition** — none of the three new variants supports a description line alongside a trailing affordance. The cena-apps wireframes don't surface a need yet, but Outcomes ("last reading + relative time" as a value + add-icon as trailing) gets close. If a clear two-line + trailing affordance variant surfaces, propose `list-group-row-action-detail` (or similar) as a separate codification. Don't speculatively codify now.

2. **Status-row variant** — a labeled row whose trailing slot is *always* a badge (Consent: Signed / Not yet on file in the account screen). The cena-apps account screen handles this inside a `card-body` with a manually-composed badge instead of a row. If multiple wireframes start composing the badge-row pattern, codify `list-group-row-status` as a fourth variant in a future slice.

3. **Accessibility of `-row-toggle` on tablet/desktop layouts** — flagged in §4.2. If the toggle drifts >400px from its label in wide layouts, the label↔toggle association breaks at a glance. Tablet/desktop QA should verify the row's `max-width` constraint (probably inherited from the parent card) is sufficient.

4. **Dark-mode inversion of the active state** — the `dark:bg-primary-900/20 dark:text-primary-400 dark:border-b-primary-800` pattern in `list-group-item-action.active` is already in components.css. This spec inherits it as-is; if dark-mode rendering in the cena-apps shell or future apps reveals weakness, propose a dark-mode amendment then.

5. **A possible `list-group-row-label-stack` modifier** — for very long labels that need to wrap (e.g., "Authorization for caregiver to receive my health information"). Today the family relies on label brevity; if a real surface needs a wrapping label, codify the wrap behavior as a modifier rather than letting individual consumers override `truncate` ad-hoc.

---

## 7. Validation checklist (for the codification slice's render-pipeline verification)

After the three new variants ship in `components.css` + `data-list-group.html` and the cena-apps patient-app account screen renders:

- [ ] Profile section: 4 rows (Name / Email / Phone / Address) use `list-group-row-action`; each has a value at `sand-700` and a trailing pen icon at `sand-600`; hover/focus shifts the row to `sand-100`
- [ ] Profile/Language row: uses `list-group-row-control` with a 2-segment `haven.segmented-control` (English / Español)
- [ ] Notifications section: 3 rows (Phone / SMS / Email) use `list-group-row-toggle`; each has a label at `sand-900 semibold` and a `haven.toggle` in the trailing slot; OFF state is sand-neutral, ON state shows teal
- [ ] Outcomes section: 3 rows (Weight / BP / A1C) use `list-group-row-action` with the value column showing most-recent reading + relative time, trailing add-icon
- [ ] All sections are inside `card` containers with `list-group list-group-flush` (no double border)
- [ ] Density consumer (if cena-apps sets `--density-default: compact`) tightens row padding without breaking 44px touch targets
- [ ] Quiet-mode check: scroll the account screen with eyes unfocused; the page should read as one warm-ground card sequence with quietly-bordered row ribbons, not as a stacked-widget settings menu
- [ ] Grew-not-built check: a hover over an edit-pen row produces a `sand-100` ground shift that feels like "the page acknowledging your cursor," not like "a button lit up"
- [ ] No row composition violates §5's cross-cutting decisions (no Lora on row labels, no teal on idle icons, no thickened dividers, no per-row density overrides)

---

## Source

2026-06-15: Haven Visual Designer dispatch via Haven Steward codification slice (Path B). Closes pre-existing list-group brand-spec debt + codifies 3 new bound-row variants (`list-group-row-action`, `list-group-row-toggle`, `list-group-row-control`) earned by cena-apps patient-app account screen wireframe. Companion artifacts: PL fragments in [`Lab/haven-ui/packages/design-system/pattern-library/components/data-list-group.html`](../../haven-ui/packages/design-system/pattern-library/components/data-list-group.html); CSS in [`Lab/haven-ui/packages/design-system/src/styles/tokens/components.css`](../../haven-ui/packages/design-system/src/styles/tokens/components.css); index rows in [`Lab/haven-ui/packages/design-system/pattern-library/COMPONENT-INDEX.md`](../../haven-ui/packages/design-system/pattern-library/COMPONENT-INDEX.md). Steward briefing: [`Knowledge/Projects/Cena Health/experts/haven-steward.md`](../../../Knowledge/Projects/Cena%20Health/experts/haven-steward.md).
