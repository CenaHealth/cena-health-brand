# Haven cmd-palette result-state styling — completeness/availability register on search results

_Brand spec for the `cmd-palette-result` state-modifier family — the canonical completeness/availability register on search results inside a Cmd+K palette. Visual treatment + rationale only; structure and CSS implementation live in [`Lab/haven-ui/packages/design-system/pattern-library/components/complex-command-palette.html`](../../haven-ui/packages/design-system/pattern-library/components/complex-command-palette.html) and [`packages/design-system/src/styles/tokens/components.css`](../../haven-ui/packages/design-system/src/styles/tokens/components.css)._

---

## 1. Context

The `cmd-palette-result.is-{state}` family is the canonical way to signal a search-result's **completeness or availability state** inside a command palette. The result element renders and its link still resolves; the modifier subdues the primary text and adds an inline status pill so the operator knows the result corresponds to an incomplete (not-yet-shipped / planned / coming-soon / etc.) entity before they click.

The primitive earned codification when the UConn pilot staff-task-universe surface (round-3) extended its Cmd+K palette to index ALL 57 capability docs — including the ~20 not-yet-authored. The pre-round-3 palette filtered `status: missing` results out entirely, which silently violated the cmd-palette's contract that "everything in the corpus is reachable from search." Round-3 inverted that choice (per `pipeline-coverage-gate.md` Gate 4): index all 57 caps, but render the planned ones with an honest visibility-cue. Source incident: `~/.claude/staging/uconn-ux-review-2026-06-26/haven-steward-round-3-review.md` § P3 + round-3 implementation at `staff-universe.css:1038-1056`.

**Why a state-family on the result row vs. a separate "planned-result" primitive.**

A separate primitive would (a) double the palette's variant surface (every consumer would need to know about TWO result shapes), (b) lose the "this is still a result, just incomplete" affordance reading (a separate primitive reads as a different KIND of row, not as a same-kind-row in a different state), (c) re-derive the link-still-resolves contract per consumer. The state-modifier shape composes against the existing result and inherits its semantics — the operator's mental model stays "results, some are still in flight" rather than "results and planned-result-things."

**Brand intent.** The completeness register must read as **"the system being honest about what's ready and what's still coming"** — not as alarm, not as warning, not as denial. The result is reachable; the destination is just a stub today. The pill is **informational**, in the structural neutral register (sand family), not in the severity register (the alert / warning families). A search-result palette is a routine wayfinding surface; an in-progress state on one of its rows is a routine condition. Brand restraint per Principle 6: the pill is the minimum-viable visibility cue that does the work without making the result look broken.

---

## 2. Brand frame for this family

The defining brand call: **the state-pill sits in the structural neutral register (sand family), not in the severity register (amber / red).** A search-result that resolves to a not-yet-authored doc is NOT a warning state — it is a completeness state. Using amber on every "Planned" pill would (a) train the operator to treat the palette as alarm-prone (and tune out the cue when it appears on truly-warning surfaces like the approval-badge family — see [`haven-approval-state-badge-styling.md`](haven-approval-state-badge-styling.md)), (b) over-state the consequence (the operator can navigate; the destination just doesn't have content yet), (c) break the directive-spec discipline ("sand carries structure; amber carries severity" — established in [`haven-directive-styling.md`](haven-directive-styling.md) § Pattern asserted). The sand-family pill is the structural register: "this row is a different KIND of result," not "this row is alarming."

Three brand choices follow:

- **Pill family is sand, not amber.** Sand-100 ground / sand-600 text / sand-300 border is the brand's structural-neutral chip register. Amber would route this through the severity register where it does not belong (the operator's destination is still reachable; nothing is broken). Red would be a category violation. Teal is reserved for interactive commitments per Haven's primary-teal discipline. Sand is the right family because the pill's job is "say a true thing about this row's state without raising any alarm bell."

- **The primary text on a state-modified result subdues — it does not strike through, italicize, or grey out completely.** Sand-600 (the same value as a result breadcrumb) instead of sand-900 (the canonical title weight). Strike-through would read as "deleted" / "deprecated" / "not available"; italicization would read as editorial commentary; full grey-out would suggest the row is inert / not-clickable (it IS clickable). The subdued-but-readable treatment is the right register for "the row is still a destination, just an incomplete one."

- **The pill IS visually distinct from a `badge-neutral` chip — they are different family members for different consumer surfaces.** The cmd-palette state-pill uses tighter padding (0.1rem 0.45rem vs. badge's px-1.5 py-0.5), smaller font (0.6875rem vs. badge's text-xs), and a `vertical-align: 0.1em` offset that lifts it relative to the title baseline. This is intentional: an inline pill that floats next to a 16px title needs a different visual weight than a standalone badge that fills a meta row. Mistakenly composing a `badge-neutral` in this slot would over-state the pill against the title; mistakenly using the cmd-palette state-pill in a meta row would under-state it against sibling badges.

The "grew, not built" test on this family: a palette result with `is-planned` should read as "a result that the system is being honest about — still coming," not as "a broken row" or "a row I should avoid." The sand-family pill, the subdued title, and the still-functional link should compose into one coherent signal: the corpus is still in flight, here is the row, click if you want the partial.

---

## 3. State vocabulary (open lexicon)

The family ships with `is-planned` as the v1 state. The vocabulary is **open** — additional states earn their place by surfacing in real consumer surfaces. The brand discipline (below) constrains which family a new state's pill belongs in; the specific tokens for sand-tier states are reused verbatim, and severity-tier states (if they ever earn the warrant) inherit from the alert / warning families.

### 3.1 Sand-tier states (structural completeness register)

These states share the sand-100 / sand-600 / sand-300 pill treatment. Use when the state describes the **completeness or availability of the destination's content**, not a warning or alarm.

- **`is-planned`** — Pill label: **"PLANNED"**. The destination exists in the corpus index but the content is not yet authored. The operator can navigate; they will land on a stub or a placeholder. Canonical v1 use: UConn pilot capability docs whose source status is `missing`. (Implementation at `staff-universe.css:1038-1056`.)
- **`is-coming-soon`** — Pill label: **"COMING SOON"**. The destination is on the near-term roadmap with a known timeline. Distinct from `is-planned` (no timeline commitment) and `is-beta` (the destination exists but is preview-quality). Use when the team wants to communicate intent + cadence ("this is in the next release"). Not yet shipped at v1; reserved for codification when a second consumer surfaces.
- **`is-beta`** — Pill label: **"BETA"**. The destination exists and is functional, but the team is signaling preview-quality (incomplete polish, in-flight validation, feedback wanted). Distinct from `is-planned` (content is real) and `is-coming-soon` (cadence-bound). Not yet shipped at v1; reserved.

A new sand-tier state earns codification when a consumer surface needs it AND the existing vocabulary does not fit. Adding one is the registered three-place change (this brand spec row + PL fragment variant + COMPONENT-INDEX `when-to-use` entry) per [`.claude/rules/haven-primitive-codification.md`](../../../.claude/rules/haven-primitive-codification.md)'s 5-place gate.

### 3.2 Severity-tier states (rare — earn warrant per state)

Severity-tier states route through the alert / warning / error families' tokens, NOT through sand. They earn their place only when the state describes a **destination the operator should be cautious about reaching** — not just incomplete, but actively in a state that warrants warning. Examples a future surface might want:

- **`is-deprecated`** — Pill label: **"DEPRECATED"**. The destination still resolves but is being retired; the operator should reach for a replacement. Pill family would be sand (still structural — the destination is reachable) OR amber if the team wants to signal active migration urgency. The right family is a brand call at codification time; default to sand and escalate to amber only with explicit warrant.
- **`is-broken`** — Pill label: **"NEEDS ATTENTION"**. The destination is in a known-broken state and the team is signaling the operator should not rely on it until it's fixed. This IS severity register — amber-50 / amber-700 / amber-200, matching `badge-warning`. The operator's destination is genuinely problematic, not just incomplete. Brand discipline: if a `is-broken` state ever earns the warrant in a real surface, codify with amber tokens AND cross-reference [`haven-approval-state-badge-styling.md`](haven-approval-state-badge-styling.md)'s bridge-state discipline so the family stays coherent.

The 3-use floor applies: do not pre-author severity-tier states without a real consumer. The discipline this spec encodes is "when the warrant comes, codify in the right register family" — not "build the full lexicon now."

---

## 4. Visual treatment per region

The treatment below is the v1 sand-tier shape; severity-tier states (3.2) substitute the appropriate family's tokens (amber for warning, etc.) but keep the same chrome architecture (inline pill, vertical-align offset, padding, font, uppercase, letter-spacing).

### 4.1 The state-modifier on the result row

- **Semantic intent.** Signals that the result row is in a non-default state without changing its result-row semantics (still a navigable destination, still keyboard-selectable, still hoverable).
- **Class shape.** `cmd-palette-result is-{state}` (e.g., `cmd-palette-result is-planned`).
- **Effect on primary text (title).** `.cmd-palette-result-title` color shifts from sand-900 (canonical) to **sand-600** (`#777069`). This is the same value as the breadcrumb's color; the title visually descends from "primary content" to "secondary content" register. No font-weight change (stays 600), no italics, no strike-through.
- **Effect on breadcrumb and meta.** No change. The breadcrumb stays sand-600 (it was already there); a cap-id or other meta stays sand-700. The state-modifier touches only the title's color register — the row is still readable as a complete data shape.
- **Effect on hover / aria-selected state.** Inherits the canonical hover (sand-100 ground); the state-modifier does NOT change the ground-fill on hover. The hover signal still reads as "you are pointing at this row"; the state-pill reads as "this row is in a non-default state." The two signals compose without competition.
- **Touch target and link semantics.** Unchanged. The row is still a clickable destination; the link still resolves.

### 4.2 The state-pill (`.cmd-palette-result-status`)

- **Semantic intent.** Inline informational chip naming the state. Visually adjacent to the title (vertically slightly raised relative to title baseline so it sits as a "label on the title" rather than as "another piece of content next to the title").
- **Visual treatment (sand-tier).**
  - **Background:** `var(--color-sand-100)` (`#e4dfd7` per palette canon; the round-3 implementation uses the legacy hex `#f5efde` from the staff-universe vendor — the canonical token is what the PL fragment must use; the round-3 surface will inherit the canonical value on its next re-vendor).
  - **Text color:** `var(--color-sand-600)` (`#777069`).
  - **Border:** `1px solid var(--color-sand-300)` (`#bbb6ad`).
  - **Border radius:** `4px` (slightly tighter than the canonical 6px rounded-md — the pill is a smaller, more chip-like shape; the tighter radius reads as a label, not as a button).
  - **Padding:** `0.1rem 0.45rem` (vertical/horizontal). Tighter than `badge-neutral`'s `px-1.5 py-0.5` — the pill is meant to be small and quiet, not to compete with sibling chips.
  - **Margin-inline-start:** `0.4rem` (creates the gap between title text and pill).
- **Visual treatment (severity-tier, when warranted).** Substitute the family tokens (e.g., `var(--color-warning-50)` / `var(--color-warning-700)` / `var(--color-warning-200)`) but preserve chrome architecture (radius, padding, font, vertical-align). The pill should remain visually quieter than `badge-warning` (which is meant to fill a meta cell at larger scale).
- **Typography.**
  - **Font family:** `'Source Sans 3', sans-serif` (canonical body font).
  - **Font size:** `0.6875rem` (11px) — one step smaller than the body 12px / 0.75rem; the pill is at the bottom of the typography hierarchy by intent.
  - **Font weight:** `500` (medium) — heavier than body 400, lighter than title 600. The medium weight is what makes a small uppercase label legible without shouting.
  - **Text-transform:** `uppercase`.
  - **Letter-spacing:** `0.04em` — the canonical Cena uppercase letter-spacing (matches the eyebrow register in body prose; matches the H3 caps treatment per `haven-docx-typography.md` § H3).
- **Vertical alignment.** `vertical-align: 0.1em`. This is the load-bearing micro-call: the pill is OFFSET up from the title's baseline by 0.1em so it reads as a "label tagged onto" the title rather than as "another inline word." Without the offset, the small uppercase pill sits visually below the title baseline (because small caps' optical center is lower than mixed-case 600-weight at the same baseline), which makes the pill look like it dropped off the title. The `0.1em` lift reconciles the optical centers. Any future per-state treatment must preserve the offset.

### 4.3 Active / selected state interaction

When a state-modified result is keyboard-selected (`aria-selected="true"`) or hovered:

- The row ground-fill becomes sand-100 (canonical hover/selected treatment, inherited from the base `cmd-palette-result`).
- **The pill's sand-100 ground now matches the row's sand-100 ground.** The pill's text + border remain in their original tokens; the pill is still legible (the sand-300 border + sand-600 text are enough to anchor it against the matching ground), but the contrast is reduced.
- This is intentional: when the operator is selecting the row, the pill recedes slightly; the primary affordance is the row itself. The pill's "this is a state" signal has done its work by drawing the operator's eye to the row in the first place; once they're on it, the state is context, not focus. A future surface that finds this reduction unacceptable can override with `aria-selected .cmd-palette-result-status { background: var(--color-sand-50); }` — but the default is the matching-ground treatment.

---

## 5. Rationale — why these specific choices

### 5.1 Why sand-100 / sand-600 / sand-300 (and not teal-100 / teal-700)

A teal-100 pill would read as a **branded affordance** — teal is Cena's primary-interactive register (per `cena-health-brand/CLAUDE.md` rule 2). An operator scanning the palette would see a teal pill and read it as "click here for this state" or "this is a featured / promoted state" — neither is the intended signal. The state-pill is informational, not affordance; sand keeps it in the structural-neutral register where the brand discipline says informational chips belong.

A second consideration: teal-100 against teal-700 text is the brand's primary-button family. Compositing a teal-100 pill onto a sand-100 hover row would visually nest two warm-cool-warm layers in close proximity (sand row → teal pill → sand title), which creates color-thrash. Sand-on-sand keeps the row visually coherent.

### 5.2 Why `vertical-align: 0.1em` specifically

The pill's `font-size: 0.6875rem` puts the small caps' optical center roughly 0.08em below the title's mixed-case optical center at `font-size: 0.875rem`. Without the lift, the pill reads as "dropped below the title baseline" (the same way subscripts read as below-baseline). The `0.1em` lift moves the pill's optical center just above the title's, which the eye reconciles as "label hovering at title height" — the desired register.

The value was tuned visually in round-3 implementation. Variations: `0.05em` reads as slightly-low; `0.15em` starts to look superscripted. `0.1em` is the brand-correct landing.

### 5.3 Why uppercase + 0.04em letter-spacing

The uppercase treatment is what makes "PLANNED" read as a *label register* rather than as a *word register*. A title-case "Planned" pill would compete with the title text for reading register; an uppercase pill is structurally a different kind of content (the same way the eyebrow register works in body prose — `haven-docx-typography.md` § H3, § eyebrows). The 0.04em letter-spacing is the canonical Cena uppercase value (matches H3 caps, matches the eyebrow register in body prose). Tighter spacing reads as cramped; wider reads as institutional.

### 5.4 Why size is one step smaller than body

The pill is intentionally at the bottom of the typography hierarchy. The reading order on a state-modified row is: title (primary), pill (secondary state label), breadcrumb (tertiary context), meta (tertiary detail). Sizing the pill at body-12px would lift it to the breadcrumb's register and confuse the hierarchy. The 11px / 0.6875rem keeps it visually subordinate; uppercase + medium-weight compensate so the legibility floor still clears.

### 5.5 Brand call on register: sand for completeness, amber for caution

The clearest discipline this spec encodes: **completeness ≠ warning**. A search-result that points to incomplete content is not the same kind of signal as a search-result that points to dangerous content. Brand uses sand for the first (structural-neutral, "still in flight") and amber for the second (severity, "be careful"). Conflating them — using amber for `is-planned` — would (a) over-state every "planned" row, (b) drain amber's signal-weight when it actually fires for `badge-warning` / `escalation` / approval-state "Reviewer pending", (c) train operators to ignore the alert register entirely. The brand's restraint discipline is preserved by routing each signal through the right register family.

This rule generalizes: **any future state-pill family** (in palettes, in lists, in cards, in tables) chooses its register family by **what kind of signal** the state carries, not by what color "looks right." Structural-neutral states → sand. Severity-aware states → the appropriate severity family (warning / error / success). The cross-spec discipline is documented at [`haven-approval-state-badge-styling.md`](haven-approval-state-badge-styling.md) § 5 for the approval-state badge family's parallel decision.

---

## 6. Quality-test notes

**Quiet-mode test.** Render a palette with 12 results, 4 of which carry `is-planned`. The four state-modified rows should read as "results in a different register" — the operator's eye should land on them, register the state, and move on. If the four rows read as "broken" / "alarming" / "this palette has a problem," the sand register has shifted too warm or the pill is too prominent (likely culprits: ground-fill too saturated, or vertical-align reading as superscript). If the four rows read as "indistinguishable from default" / "I didn't notice the state at all," the pill is too quiet (likely culprit: pill text-color dropped below sand-600, or padding too compressed).

**Grew-not-built test.** The state-modifier should read as part of the palette's vocabulary, not as a feature bolted on. The pill's sand-family hue is hue-adjacent to the palette's hover sand-100 ground; the title's sand-600 subdued color is the same value as the breadcrumb the operator already sees on every row. Both choices are "the palette being a little quieter on this row" — the integration is structural, not chrome.

**Affordance-fidelity test.** A state-modified row's link must still resolve and the row must still be keyboard-selectable. The state-pill is informational only; it carries no aria-hidden semantics and no click handler. Per [`half-state-promises.md`](../../../.claude/rules/half-state-promises.md): the pill's signal is "this destination is incomplete," not "this destination is unreachable." A state-modifier that disables the link would be a different shape (`is-disabled`, not currently codified — would require its own brand call about whether disabled rows belong in the palette at all).

**Severity-tier brand-fidelity test.** When (if) a severity-tier state earns codification, the pill's tokens MUST come from the severity family (warning / error), and the pill MUST be cross-referenced against the approval-state badge family in [`haven-approval-state-badge-styling.md`](haven-approval-state-badge-styling.md) § 5 so the two families' use of amber stays coherent (amber-on-palette-pill carries the same "be careful" meaning as amber-on-approval-badge). Drift between the two would erode the brand's signal-weight on amber.

**Touch / focus floor.** The result row's existing touch target (44×44px effective via the row's padding + height) carries through; the pill adds no separate interaction target. Focus ring on `aria-selected` is inherited from the row's canonical focus treatment; the pill does not get its own focus ring.

---

## 7. What this brand spec does NOT cover

- **The base `cmd-palette` shape and chrome.** The palette's panel, search input, footer hint, group titles, and base result row are owned by the existing PL fragment (`complex-command-palette.html`) and components.css canon. This spec covers ONLY the state-modifier family that composes onto a result row.
- **The flat-search vs. grouped-results palette variants.** The round-3 review identified the UConn implementation as using a parallel-but-different class set (`cmd-palette-result*` vs. PL's `cmd-palette-item*`). The class-name reconciliation question is a Haven Steward call (see `haven-steward-round-3-review.md` § P3 § "Reconciliation with existing PL primitive"). This brand spec is class-name-agnostic — the state-modifier discipline applies to whichever class set the codified palette family ships with.
- **The empty-state palette variant.** A palette that returns zero results uses `cmd-palette-empty` (already in canon). The state-modifier family is for results that DO render but are in a non-default state.
- **Multiple state-modifiers on one row.** A result that is BOTH `is-planned` AND `is-beta` is structurally unusual. v1 ships with one state per row; if a future surface needs combined-state semantics, the family needs a brand call on (a) which state wins for the pill display, (b) whether a combined-pill ("PLANNED · BETA") earns codification, or (c) whether the second state surfaces in a different chrome slot. Defer until a real consumer surfaces.
- **The `data-*` attribute conventions for JS hookup.** The state-modifier is class-based; consumers that need data-attribute hooks (`data-cmd-palette-result-state="planned"`) can add them in addition to the class. The class is the canonical visual contract; data-attrs are consumer-specific behavior.
- **Dark mode.** v1 light-mode only. When a dark-mode consumer surfaces, the sand-tier treatment needs a dark-mode audit (sand-700 ground, sand-300 text, sand-600 border are the likely-correct values matching the `dark:` variants on `badge-neutral`); severity-tier states inherit from the warning / error families' dark-mode treatments.

---

## 8. Extension discipline

A new state earns codification through the three-place change defined in [`.claude/rules/haven-primitive-codification.md`](../../../.claude/rules/haven-primitive-codification.md):

1. **This brand spec** — add the row to § 3 (with family classification: sand-tier or severity-tier), name the pill label, the rationale for the chosen family.
2. **PL fragment variant** — add the state to `complex-command-palette.html` with realistic content.
3. **COMPONENT-INDEX `when-to-use` row** — name the specific consumer signal that earns this state.

Before authoring: ask the **family classification question first**. "Does this state describe **completeness** (sand-tier) or **caution** (severity-tier)?" The answer routes the rest of the work. If neither (e.g., a "branded / featured" state), the brand call is escalation: the brand discipline reserves teal for interactive commitments and does not codify a "branded result-row" register without an Aaron-level call.

Naming: state classes use `is-{adjective}` form. The pill label is uppercase, single word or two-word noun-phrase. Avoid sentence-case, avoid verbs (the pill describes state, not action), avoid abbreviations (write "BETA" not "β"; write "COMING SOON" not "CS").

---

## 9. Cross-references

- [`haven-approval-state-badge-styling.md`](haven-approval-state-badge-styling.md) — sibling state-family primitive at a different surface (badge vs. inline-pill). The approval-state family's bridge-state register decision (amber-as-processing) is the parallel brand call to this spec's sand-vs-amber discipline; both specs settle into the same overarching brand rule (sand carries completeness, amber carries caution, the operator's mental model stays coherent across surfaces).
- [`haven-directive-styling.md`](haven-directive-styling.md) — establishes the directive-spec pattern that this spec follows (sand for structural register, alert / warning / error families for severity register, escalation as the boundary case). The cmd-palette state-pill is the inline-search-surface analog of the block-level alert / card discipline.
- [`haven-data-table-styling.md`](haven-data-table-styling.md) — sibling brand spec at the same family-of-primitives layer (structural variants whose treatment carries brand-fidelity weight). Same authorial pattern (Context → Brand frame → Visual treatment per region → Rationale → Quality-test notes).
- [`.claude/rules/half-state-promises.md`](../../../.claude/rules/half-state-promises.md) — the discipline this spec satisfies for the round-3 UConn palette: the pre-round-3 palette violated the half-state-promises rule by silently excluding `status: missing` results (the affordance — "search reaches everything" — was contradicted by the rendering). Round-3 + this spec deliver-the-contract: every result renders, with honest visibility cues on the incomplete ones.
- [`.claude/rules/haven-primitive-codification.md`](../../../.claude/rules/haven-primitive-codification.md) — the codification-completeness rule that this spec satisfies for place 3 (brand spec entry). Places 1 (PL fragment), 2 (COMPONENT-INDEX row), 4 (consumer-surface references), and 5 (render-pipeline verification) are the responsibility of the broader codification slice, not this spec.

---

## 10. Source incident

UConn pilot staff-task-universe surface, round-3 implementation (`Knowledge/Projects/Cena Health/Partners/UCONN Health/surface/`, 2026-06-26). Pre-round-3: the Cmd+K palette filtered `status: missing` capability docs out entirely. Information Topology Designer review caught the silent filtering as a half-state violation — the palette's affordance ("search reaches the whole corpus") wasn't backed by the rendering. Round-3 inverted: index all 57 caps, add the `is-planned` modifier to render incomplete ones with subdued title + sand-toned pill.

Round-3 was a local UConn-surface implementation; the patterns ship inlined in `staff-universe.css`. Haven Steward review (`haven-steward-round-3-review.md` § P3) verdicted **promote** because the shape recurs across multiple Cena surfaces (cena-apps internal palette, patient-app palette, future SoT-site SoP palette, future haven-itself pattern-library browser search). This brand spec is place 3 of the 5-place codification gate; PL-fragment authoring (place 1) is phase 2 of the codification slice and inherits from this spec.

The load-bearing brand call this spec settled: **completeness ≠ warning**. Sand-family pill for `is-planned`; amber reserved for true severity. The discipline carries forward to any future state-pill family the design system codifies — and pairs with [`haven-approval-state-badge-styling.md`](haven-approval-state-badge-styling.md)'s bridge-state register decision (where amber DOES earn its place because the signal IS caution-shaped, not completeness-shaped).
