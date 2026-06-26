# Haven nav styling — the Quiet Ground composition

> Cena Health · Haven design system · Brand spec
> Authored 2026-06-18 (Aaron) · Source: HVD scout + Claude Design test + Aaron contrast-anchor discipline

## What this spec owns

Visual rationale for the haven-ui left-nav primitives — the **why** behind the proportions, colors, and treatment choices in [`packages/design-system/src/styles/tokens/components.css`](../../haven-ui/packages/design-system/src/styles/tokens/components.css)'s nav cluster. Implementation lives there; intent lives here. Per [.claude/rules/define-once.md](../../../.claude/rules/define-once.md), this spec is the canonical source for *why this looks the way it does*.

Primitives covered:
- `.nav-header` (brand zone) + `.nav-logo` + `.nav-logo-img` (combined wordmark+icon SVG, sized 32px)
- `.nav-section` (nav-items container) + `.nav-item` (idle / hover / active)
- `.sidebar-subnav-list` + `.sidebar-subnav-item` (nested-nav children)
- `.nav-section-footer` (pinned-bottom user-menu zone)
- `.nav-user-dropdown` cluster (`.nav-user-trigger / -avatar / -identity / -name / -role / -chevron`)

## The load-bearing principle

**Warm Ground, Cool Figure** (Cena brand Principle 3) applied to navigation:

> The active state moves the *ground* before it ever reaches for the accent. Teal punctuates; it never fills.

Every choice below is measured against this. When the design wants to make the current row legible, it deepens the warm ground (sand-50 → sand-100) and darkens the figure (sand-700 ink → sand-900 + weight 500 → 600). It does NOT introduce teal fill, drop-shadow, or saturated-accent backgrounds. Those are the conventional dashboard moves that violate Haven's restraint-as-default.

## The contrast anchor

> **Text always hits the WCAG floor; everything else relates to the text.** (Aaron 2026-06-18)

Cena's typography bar per haven-ui CLAUDE.md is **AAA for primary and secondary text**, **AA for chrome (chevrons, dividers, decorative borders)**. The contrast-anchor discipline says: don't pick colors aesthetically and check WCAG after — pick text colors that pass AAA and inherit icon colors from text, then color chrome relative to that floor.

| Element | Color | Ratio on sand-50 (#FBFAF8) | Pass |
|---|---|---|---|
| `.nav-item` text idle | `sand-700` #5B544C | ~7.5:1 | AAA |
| `.nav-item` icon idle | `inherit` (sand-700) | ~7.5:1 | AAA |
| `.nav-item.active` text + icon | `sand-900` #25211D on `sand-100` #F3F1EE | ~14:1 | AAA |
| `.sidebar-subnav-item` text idle | `sand-700` (was sand-600 — bumped per Aaron) | ~7.5:1 | AAA |
| `.nav-user-name` | `sand-900` | ~14:1 | AAA |
| `.nav-user-role` (secondary) | `sand-700` (was sand-600 — bumped) | ~7.5:1 | AAA |
| `.nav-logo-img` (combined SVG, embedded teal-800 / teal-700) | — | embedded vector | AAA on intent |
| `.nav-user-avatar` text | `teal-800` on `teal-100` #D0E7E2 | ~9:1 | AAA |
| `.nav-item` accordion chevron | `sand-600` #787066 (was #A39B90 — bumped) | ~4.8:1 | AA |
| `.nav-user-chevron` | `sand-600` | ~4.8:1 | AA |
| Connector line (sand-300 #D1CDC6) | — | decorative | exempt |
| `.nav-section-footer` border-top (sand-200) | — | decorative | exempt |

**Bumped from the original Claude Design composition:** chevrons and the "Health" wordmark accent color failed AA in the source design. Raised to AAA-for-text-bearing and AA-for-chrome per Aaron's anchor discipline.

## The four design problems and how the spec resolves them

### 1. Logo lockup sizing

**Failure shape it resolves:** the prior canon used the combined wordmark+icon SVG at 36px height, which dominated the rail's vertical rhythm. Brand presence out-weighed wayfinding — a sidebar's job is navigation, not marketing.

**Resolution (v2, Aaron 2026-06-18 19:07):** keep the combined wordmark+icon SVG (`logo-cena-health-teal-icon-and-mark.svg`) as the canonical lockup — one SVG, not decomposed — and shrink it to **32px height** via `.nav-logo-img`. At a 5.45:1 SVG aspect ratio the rendered lockup is ~175px wide × 32px tall, which sits **≤ active nav-item row (44px)** so brand presence is proportional to wayfinding. Same rule covers `.nav-logo img` and `.nav-logo-img` to handle both bare `<img>` children and the explicit-class case.

**Principle honored:** P1 — *logo presence ≤ most-emphasized nav item, never larger*.

**Why v2 reverted the v1 decomposition:** the v1 attempt split the lockup into a 22px-square mark SVG (`.nav-logo-mark`) plus inline two-tone text wordmark (`.nav-logo-wordmark` / `-primary` / `-accent`) to match the proportions in the Claude Design source composition. Aaron rejected the decomposition same day: keep the existing combined SVG, just size it small like Claude Design did. Single SVG honors brand-asset integrity (no parallel text wordmark drifting from the SVG's typographic tuning) and removes the two-tone-color encoding from CSS, where it would only ever live in one place. The v1 classes were never shipped to consumers.

### 2. Nav-item hierarchy + active state

**Failure shape it resolves:** the prior canon used **font-bold + teal-700 text + no background** on active items. At body-size (14px) on two-word labels, weight + color alone don't register strongly enough — the rail reads as flat under fast scanning.

**Resolution:** active row earns its read through **exactly two spatial moves** layered:
- **Move 1 — Warm ground-shift.** Background shifts from transparent (on sand-50 page) to **sand-100** (#F3F1EE). The warm-ground mechanism *deepens* on the active row rather than being overridden — sand-100 is a warmer-deeper version of the page surface, not a cool-figure override.
- **Move 2 — Figure-darken.** Text shifts from `sand-700` to `sand-900` (ink) at weight **600** (Semibold, not Bold — Bold at 14px reads as heavy alongside the ground-shift). Icon inherits text color, so the figure-shift is unified across glyph and label.

No edge bar, no drop-shadow, no fill. Hover gets the ground-shift alone (sand-100) without the figure-darken — so hover reads as "this is hoverable" and active as "this is current" via the figure delta.

**Principle honored:** P7 — *active state uses exactly 2 spatial moves on top of weight + color, never 3, never 0*. P8 — *the chosen spatial move is a warm ground-shift, not an edge bar or saturated fill*.

**Why no left-edge bar:** an edge bar in teal-700 would add a cool figure (teal) to compete with the text-color signal. Two figures of attention beats one. The Stripe/Pylon "anchored rail" pattern is right for products where wayfinding-under-load is the priority, but Cena's editorial restraint can earn the same read through ground-shift alone for the V0 nav set; promote to edge-bar only if scan-speed proves insufficient in dogfooding.

### 3. Nested-menu disclosure

**Failure shape it resolves:** the prior canon used a 3px sand-200 left-border on `.sidebar-subnav-list` with active children showing `bg-primary-50 + text-primary-700 + border-color: primary-500`. Three moves on active children (bg + text + border), and the primary-50 mint-green active background introduced a register inconsistent with the warm-ground discipline.

**Resolution:** indent + 1px **sand-300** vertical connector line drawn as a `::before` pseudo-element under the parent's icon-column (left: 1.25rem). Active children mirror the parent active mechanism exactly — sand-100 ground-shift + sand-900 ink + weight 600 — at one-step tighter font-size (13px) and row height (36px).

**Principle honored:** P11 — *pick ONE visual model for parent-child relationship and apply it system-wide*. P12 — *use indent + a left-edge inset rule (the parent's vertical accent line), not indent-only or per-child markers*. P14 — *a nested child's active state uses the same ground-shift mechanism as a top-level nav-item*.

**Why connector over indent-only:** indent alone reads as "less hierarchy" — children look like they could belong to anything above them. A single 1px vertical rule from the parent's expanded state through the children visually answers "these belong to that parent" in one stroke. This survives a second depth level the way indent-only does not — important because Inbox, Calendar, and Reports will likely grow children too as the app matures.

### 4. User-menu treatment (codification gap closed)

**Failure shape it resolves:** PL fragment (`nav-user-dropdown.html`) and COMPONENT-INDEX row pre-existed; **components.css carried zero CSS rules for the `.nav-user-*` cluster**. Consumers rendering the markup inherited browser defaults + Preline button reset — exactly why the trigger read as "undesigned" in the current cena-apps internal app. HVD scout (2026-06-18) surfaced the gap; this slice closes it per the [haven-primitive-codification](../../../.claude/rules/haven-primitive-codification.md) five-place check.

**Resolution:** codification ships the following CSS rules:

- **`.nav-user-trigger`** — full-width edge-to-edge button row inside `.nav-section-footer`. Padding `0.75rem 0.875rem`, gap 11px, no border-radius (the section-footer's border-top supplies the spatial separation; rounding inside would create awkward inset). Hover bg `sand-100` fills the entire footer zone cleanly. Focus-visible: teal-700 ring at 4px outer + sand-50 inner ring for figure/ground separation.

- **`.nav-user-avatar`** — 32px circle, **`teal-100` bg + `teal-800` ink**, 9:1 contrast (AAA). The teal-tint pairs with the inline count-badge teal language (e.g., Inbox "12" in teal-700), giving the rail one quiet brand moment at identity scale. Supports `<i>` glyph (V0 default) or `<img>` profile photos via `object-fit: cover`.

- **`.nav-user-identity`** (optional wrapper) + **`.nav-user-name`** (sand-900 ink, AAA, 14px Semibold) + **`.nav-user-role`** (sand-700 ink, AAA, 12px Regular, optional secondary line). When present, identity stacks two lines for clinical-staff context (name + role); solo `.nav-user-name` without the wrapper still works for backwards-compat with V0 one-line markup.

- **`.nav-user-chevron`** — sand-600 (AA, 4.8:1) at 11px. Quieter than the identity name (which is sand-900 AAA), visible without competing.

**Principle honored:** P4 — *user menu identifies who-you-are first and announces a menu second*. P5 — *avatar size matches nav-item icon size (1.25rem visual footprint), not nav-item height — the trigger sits as a peer to nav items, not as a heavier element*. P6 — *trigger pops above via Preline `[--placement:top-start]` with the existing `.hs-dropdown-menu` panel treatment*.

## Anti-patterns to watch for

Recurring failure modes if future implementers reach for the wrong shape:

1. **Adding `box-shadow` or elevation to `.nav-item.active`.** Restraint-as-default reaches for ground-shift before elevation. Violates Principle 6.
2. **Using `var(--color-primary-700)` text on `var(--color-primary-50)` background for active.** That mint-green tinted active state was the prior nested-nav treatment — visually inconsistent with the warm-ground discipline. The active mechanism is sand-based, not primary-based.
3. **Reaching for a left-edge teal bar.** Adds a cool figure (teal) to compete with the text-color signal. If wayfinding-under-load proves insufficient in dogfooding, promote per a separate slice — don't pre-emptively layer.
4. **Bumping border-radius on row backgrounds above 6px.** Larger radii read as approachable/consumer; this surface needs professional clinical gravity.
5. **Generous padding as a comfort move.** 44px row height is the touch-target floor; padding earns its keep through legibility, not breath-as-aesthetic. Default to tighter than instinct.
6. **Pure white (`#FFFFFF`) anywhere.** Backgrounds are warm off-white (`sand-50` #FBFAF8 or warmer `sand-100`). Pure white is an explicit brand anti-pattern.

## Source provenance

- **2026-06-18 ::8000 HVD scout** at `Lab/cena-apps/src/docs/internal-nav-design-scout.md` — initial three-finalist comparison (Quiet Rail / Anchored Rail / Iconic Rail) that surfaced the `.nav-user-*` codification gap and recommended the conservative ground-shift active state.
- **2026-06-18 Claude Design test** at `https://claude.ai/design/p/e21500fe-...` — Aaron's editorial-restraint correction reframed HVD's principles ("padded-playschool restraint" was the failure shape), producing the Quiet Ground composition this spec codifies.
- **2026-06-18 contrast anchor** (Aaron) — "ensure text always hits minimum and then you can style other colors relative to the text" raised every text-bearing element above the AAA floor and chrome above AA.

## Five-place check (haven-primitive-codification)

| Place | Status | Reference |
|---|---|---|
| 1. PL fragment | ✓ updated (v2 single-SVG) | [`layout-app-shell-responsive.html`](../../haven-ui/packages/design-system/pattern-library/components/layout-app-shell-responsive.html), [`nav-user-dropdown.html`](../../haven-ui/packages/design-system/pattern-library/components/nav-user-dropdown.html) |
| 2. components.css rules | ✓ codified | [`components.css`](../../haven-ui/packages/design-system/src/styles/tokens/components.css) — `.nav-header / .nav-logo / .nav-logo-img / .nav-item / .nav-section-footer / .nav-user-*` cluster |
| 3. COMPONENT-INDEX rows | ✓ updated | [`COMPONENT-INDEX.md`](../../haven-ui/packages/design-system/pattern-library/COMPONENT-INDEX.md) — `layout-app-shell-responsive`, `nav-user-dropdown` rows updated with codification status |
| 4. Brand spec entry | ✓ this file | — |
| 5. Render-pipeline verification | pending | Haven PL dev server :5173 (auto-reload) + cena-apps internal app at :8000 after re-vendor |

## Procedure mode toggle (codified 2026-06-25)

The `procedure-mode-toggle` primitive extends the nav family — visually it inherits `nav-segmented-control`'s chip-group layout, but its codification is separate because the contract is richer (two-chip-only shape, always-visible inert state with helper line, URL persistence, body-render hook, WAI-ARIA tablist arrow-skip discipline, inert-click toast). Canonical consumer: UConn pilot staff-task-universe procedure-detail screen — slice 1 ships Training-active + Execution-inert; slice 2 lights Execution via `setInert('execution', false)` (a wiring change, not a layout change).

### What this primitive owns

- The **location-reservation discipline** is the load-bearing UX promise. Slice 1 reserves the Execution chip's position so slice 2 ships without a layout shift. The reader's eye learns the chip group's shape in slice 1; slice 2 is "the inert chip lit up," not "a new control appeared."
- The **two-chip-only contract** is intentional. Three-mode procedure surfaces (the Audit-mode case the UConn pilot ITD considered in §C12 and dropped in §C13) do NOT use this primitive — propose a new one if three modes return.

### The visual register — peer-to-title chip group, not tab-strip-in-chrome

**Failure shape it resolves:** the obvious "tabs across the top of the page" treatment would put Training/Execution in the chrome zone alongside the surface-nav links. That reads as page-level navigation ("which tab am I on") rather than mode-of-this-procedure ("which lens am I reading this through"). The chrome zone is for cross-procedure wayfinding (Stage 5 · Run a weekly check-in); the mode-toggle is per-procedure context.

**Resolution:** the chip group renders **below the page-header zone** (after the title, kv-row meta, breadcrumb, approval badge) and **above the body**, as a peer-to-title element — not chrome. Inherits `.segmented-control` visual base (sand-200 border, sand-50 fill, sand-100 active ground-shift, sand-900 ink + weight 500 on active) so the chip group reads as a known Haven shape, not a novel control.

**Active mechanism — Warm Ground, Cool Figure:** active chip uses sand-100 ground-shift + sand-900 ink (the same mechanism as `nav-item.active` per Principle 8). NO teal fill, NO edge bar, NO drop-shadow. The restraint discipline that holds for the nav rail holds here too — the toggle is part of the same visual family.

**Why not the existing `.nav-segmented-control` directly:** the codified primitive carries contract (inert state, helper line, toast, URL persistence, body-render hook, tablist arrow-skip) that `nav-segmented-control` does not. Composing `nav-segmented-control` for the mode-toggle slot would silently lose that contract. The codified primitive is the only consumer for the procedure mode-toggle affordance.

### The inert chip — visible-but-disabled, never aria-hidden

**Failure shape it resolves:** removing the Execution chip from the DOM in slice 1 (or rendering it `aria-hidden="true"` or `display:none`) breaks the location-reservation discipline. Slice 2 would then introduce a new control mid-flight, which is the exact layout-change-not-wiring-change shape this primitive is built to prevent.

**Resolution:** the inert chip:
- Carries `aria-disabled="true"` (NOT `disabled`, NOT `aria-hidden`)
- Renders at sand-600 text + sand-50 fill — `≥4.5:1` contrast per WCAG 2.1 1.4.3 (disabled state must remain readable for AT users; opacity-as-disabled is anti-pattern)
- Stays in the keyboard Tab order so AT users can Tab to it, hear the accessible name + helper-line context, and choose whether to activate
- The WAI-ARIA tablist arrow contract SKIPS the disabled chip on ←/→ cycling per the tab pattern (only enabled chips cycle), but Tab still reaches it

**Cursor signal:** `cursor: not-allowed` on the inert chip — visual feedback that the click won't do what the user might expect. Clicking still fires the toast + inert-click event; the cursor signals "this won't work the way you think" while the toast + helper line explain why.

### The helper line — kv-row-shaped register below the chip group

**Failure shape it resolves:** the "Shipping slice 2" disclosure has three obvious placements: (a) below the chip group as a helper line, (b) as a trailing micro-badge on the chip itself ("Soon ↗"), (c) only on hover/focus as a tooltip. Options (b) and (c) require the user to discover the disclosure — (b) crowds the chip's visual weight; (c) requires a hover interaction that AT and touch users don't have access to.

**Resolution:** always-visible helper line below the chip group, in kv-row register (sand-700 ink on sand-50 page, 12px Source Sans 3 Regular, single short string). Renders only when an inert chip is present; clears automatically when the inert chip is enabled via `setInert(mode, false)`. The line is the chip group's accessible disclosure — readable by sighted users, AT users, and screen readers without any discovery interaction.

**Why "Shipping slice 2 — you're reading the Training shape":** the prose names both the future state ("Shipping slice 2") AND the present reading register ("you're reading the Training shape"). The second clause is a Plain Language Positioning move — naming what the reader IS doing right now beats only naming what's missing.

### The inert-click toast — 2 seconds, inline, opacity-only

**Failure shape it resolves:** clicking the inert chip with no feedback reads as a broken control — the user can't tell if the click registered. A loud modal or full-screen toast over-corrects.

**Resolution:** small inline toast in a dedicated region below the chip group, mounted on click, removed after 2 seconds. The toast:
- Uses `role="status"` + `aria-live="polite"` so AT announces it
- Uses opacity-only fade (no slide) so motion-sensitive users + reduced-motion preference get an instant appear/disappear
- Repeats the helper line's prose ("Execution mode shipping slice 2 — you're reading the Training shape") so the disclosure is consistent across the always-visible helper AND the on-click feedback
- Does NOT update the URL (URL stays on `?mode=training` or omitted — bookmarking the inert click would mislead the next reader)

**Why 2 seconds:** longer reads as urgent; shorter doesn't give AT users time to hear the announcement. 2 seconds matches existing Haven toast conventions.

### Mobile compact (≤480px) — icon-only with mortarboard + checklist glyphs

**Failure shape it resolves:** at 320px (iPhone SE floor), two text chips ("Training" + "Execution") + helper line + toast region compete for horizontal + vertical space against the title + kv-row + breadcrumb + approval badge + print icon. Full-text chips overflow.

**Resolution:** ≤480px compacts the chip group to icon-only — Training = mortarboard glyph (`fa-solid fa-graduation-cap`); Execution = checklist glyph (`fa-solid fa-list-check`). Glyphs chosen for plain-language read: the mortarboard is the canonical "learning/training" symbol; the checklist is the canonical "stepwise execution" symbol. Tooltips via `[aria-label]` carry the full text for hover/focus + AT.

**Touch target floor maintained:** chip padding ensures ≥44×44px touch target even with the label visually hidden. The label stays in the DOM (visually clipped via sr-only positioning) so AT reads the accessible name.

**Why not collapse to a dropdown:** a single trigger + dropdown loses the location-reservation discipline — both chips need to remain visible so slice 2's wiring change is symmetric on mobile.

### Anti-patterns to watch for

1. **Using teal fill on the active chip.** Violates Principle 3 (Warm Ground, Cool Figure). Active state is ground-shift, not figure-overlay.
2. **Setting `disabled` instead of `aria-disabled` on the inert chip.** The `disabled` attribute removes the button from Tab order; AT users lose the chance to discover the inert mode + read the helper line. Always `aria-disabled="true"`.
3. **Using opacity to dim the inert chip.** Kills contrast and fails WCAG 1.4.3. The inert chip uses sand-600 text (≥4.5:1) at full opacity.
4. **Routing the toast to an overlay/full-screen Haven toast.** Over-corrects the feedback weight. The primitive's own inline toast region is the correct surface.
5. **Composing `nav-segmented-control` directly for the mode-toggle slot.** Silently loses the codified contract (URL persistence, helper line, toast, arrow-skip). Use `procedure-mode-toggle` or propose a new primitive if the consumer needs a different contract.
6. **Writing `?mode=execution` to the URL on inert click.** Bookmarking from that state would mislead the next reader. URL only updates on enabled-chip activation.

### Five-place check (haven-primitive-codification)

| Place | Status | Reference |
|---|---|---|
| 1. PL fragment | ✓ codified 2026-06-25 | [`procedure-mode-toggle.html`](../../haven-ui/packages/design-system/pattern-library/components/procedure-mode-toggle.html) |
| 2. components.css rules | ✓ codified | [`components.css`](../../haven-ui/packages/design-system/src/styles/tokens/components.css) — `.procedure-mode-toggle` family |
| 3. Brand spec entry | ✓ this section | — |
| 4. Consumer-surface references | ✓ wireframe references resolved | [UConn procedure-detail spec](../../../Knowledge/Projects/Cena%20Health/Partners/UCONN%20Health/surface/wireframes/procedure-detail/spec.md) — STEWARD-GAP-1 names the codified primitive |
| 5. Render-pipeline verification | see slice dispatch return | PL dev server at :5173 (see dispatch return for run-verification status or skip artifact) |

### Source provenance

- **2026-06-25 UConn pilot ITD §9.5** — Gap 1 named as the slice-1 Steward dispatch; codified primitive ships slice 1 (not a fallback composition).
- **2026-06-25 procedure-detail wireframe** — owns the contract this primitive honors (Layout body, Interaction notes, a11y checklist).
- **Extends `nav-segmented-control` visually** per Cena brand Principle 3 (Warm Ground, Cool Figure) — same active mechanism family as `nav-item.active` per `haven-nav-styling.md` §2.

## Relationship to other specs

- [`haven-directive-styling.md`](haven-directive-styling.md) — owns the directive vocabulary (callout, escalation, etc.); the nav primitives are a structural surface, not a directive surface.
- [`haven-list-group-styling.md`](haven-list-group-styling.md) — owns the data-list-group visual register; nav-items are NOT list-group rows (they're navigation, not data display), so the two surfaces stay distinct even though they share Source Sans 3 + warm-ground discipline.
- [`haven-page-header-trailing-actions-styling.md`](haven-page-header-trailing-actions-styling.md) — owns the topbar trailing-actions slot; nav rail and topbar compose together at `.app-shell-frame` but own distinct surfaces.
- [`haven-language-toggle-styling.md`](haven-language-toggle-styling.md) — sibling segmented-control extension (visually inherits `nav-segmented-control`; codified separately because it carries a JS contract `nav-segmented-control` does not). Same compose-extend-but-codify-separately discipline as `procedure-mode-toggle`.
