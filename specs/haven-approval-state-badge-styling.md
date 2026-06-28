# Haven approval-state badge styling — four-state lifecycle with the amber bridge-state register

_Brand spec for the `badge-approval-{state}` family — the canonical approval-lifecycle register on documents and artifacts whose authority depends on reviewer sign-off. Visual treatment + rationale only; structure and CSS implementation live in [`Lab/haven-ui/packages/design-system/pattern-library/components/badge.html`](../../haven-ui/packages/design-system/pattern-library/components/badge.html) (extended with approval-state variants) and [`packages/design-system/src/styles/tokens/components.css`](../../haven-ui/packages/design-system/src/styles/tokens/components.css)._

---

## 1. Context

The `badge-approval-{state}` family is the canonical way to render a document or artifact's place in its approval lifecycle. Four states span the lifecycle: `missing` (not yet authored) → `draft` (authored, not yet reviewed) → `reviewer-pending` (authored AND nominally approved, but reviewer metadata absent from source) → `approved` (reviewer metadata present and binding). The family ships with cross-district reach: the same vocabulary applies to SoP docs (Document district), staff-published content like care plans / intake forms / kitchen menus (UI district), workflow-diagram approval status (Diagram district), and future agent-emitted content that needs human-reviewer routing.

The primitive earned codification when the UConn pilot staff-task-universe surface (round-3) calibrated its approval-badge rendering against the source-frontmatter contract: pre-round-3, the build script returned green "Approved" for any document with `status: approved` even when the source frontmatter carried no reviewer metadata (no name, no version, no date). That green badge over-claimed authority the source could not back — to a clinician reader, the green badge signals "a reviewer signed off on this," but the source had no such signature. Round-3 added the **amber bridge-state** ("Reviewer pending") for the precise condition where the content has been drafted and nominally approved but the reviewer-attribution contract has not yet been satisfied. Source incidents: `~/.claude/staging/uconn-ux-review-2026-06-26/haven-steward-round-3-review.md` § P4 + round-3 implementation at `build-staff-pages.mjs:324-405, 608-622`.

**Why a four-state family vs. a binary approved/not-approved badge.**

Binary semantics force every artifact to be either "approved" or "broken" — a category mismatch with how Cena's approval workflows actually run. A drafted-but-not-reviewed doc is not broken; it is in flight. An approved-without-attribution doc is not broken either; it is honestly in a different state than approved-with-attribution. The four-state family names each genuine condition so the badge's signal stays calibrated: missing is its own state (nothing exists yet), draft is its own state (content exists, needs review), reviewer-pending is its own state (the bridge case), approved is the terminal state. The system stops over-claiming when reviewer metadata is empty AND stops under-claiming when drafted content is genuinely in-review.

**Brand intent.** The approval-state register must read as **"the system being honest about the document's authority status."** Green over-claims; red under-claims; the right family for the bridge case is amber, which the brand reads as "in process / pay attention / not yet final" — distinct from red's "stop / something is wrong." The amber bridge state is the load-bearing brand call this spec encodes; without it, the design system cannot truthfully render Cena's actual approval workflows.

---

## 2. Brand frame for this family

The defining brand call: **amber means "in process" in the approval-state register, NOT "warning."** This is the load-bearing decision the family rests on. The reason it lands as amber and not as some other family:

- **Green is wrong** for the bridge state. Green carries "complete / signed off / safe to rely on" — using green for "approved but no reviewer metadata yet" trains the reader to discount the green signal entirely. The cost is catastrophic: a clinician seeing a green "Approved" badge on a SoP needs to be able to trust that a reviewer signed off; if green sometimes means "well, actually nobody signed off," the badge loses its meaning across the corpus.
- **Red is wrong** for the bridge state. Red carries "stop / error / broken" — using red for "approved but no reviewer metadata yet" mis-states the consequence. The content has been drafted and nominally approved; the operator can read it; nothing is broken. Red would push readers to discard the document when the right behavior is "read it, but know the attribution contract isn't satisfied yet."
- **Sand is wrong** for the bridge state. Sand is the brand's structural-neutral register (see [`haven-cmd-palette-result-state-styling.md`](haven-cmd-palette-result-state-styling.md) § 2). Approval state is NOT structurally neutral — it carries a "pay attention to the missing attribution" signal. Sand would under-state the condition.
- **Amber is right** because it carries the brand's "in process / pay attention / not yet final" register without claiming "stop / error." This is the same register `alert-warning` and `escalation` and `review-marker` already use in the directive vocabulary ([`haven-directive-styling.md`](haven-directive-styling.md) § 3.2, § 3.6, § 3.9). The approval-state bridge case rhymes with those: a SoP whose approval attribution is pending is the same KIND of "draft / pending" signal as a `review-marker` in pre-approval SoP draft prose.

Three brand choices follow from the bridge-state decision:

- **The four states map to four tokens-families, not four shades of one family.** Each state earns a distinct color family because each state carries a distinct semantic signal: missing (neutral / sand — "not yet authored" is structurally a different kind of absence than draft), draft (amber — "in flight, expected to advance"), reviewer-pending (amber — same family as draft because both are "in flight in the approval cycle"), approved (green — terminal). Using one family with shading would visually compress the lifecycle into "more or less of the same thing," which is the opposite of what the badge needs to communicate. The lifecycle has discrete stages; the badges reflect that.

- **The icon paired with each state carries equal weight to the color.** Per the brand's accessibility floor (dual-cue: never color alone), every state ships color + icon + label. The icon vocabulary is deliberate — pencil for "being authored / in-process" (draft + reviewer-pending), check for "approved," minus / dash for "not yet authored." The pencil icon is the load-bearing icon choice for the bridge state and earns its own subsection below (§ 5.2).

- **Draft and reviewer-pending share an icon AND a color family, but carry distinct labels.** Both states use amber + pencil; the differentiation is the label text ("Draft — not yet approved" vs. "Reviewer pending"). This is intentional: both states are "in flight in the approval cycle, expected to advance"; the labels carry the precise differentiation (draft means "no review has started"; reviewer-pending means "review has been claimed but the attribution contract isn't satisfied"). A reader at quick scan sees "amber + pencil" and reads "in flight"; a reader who pauses to read sees the specific state. The visual coupling encodes the cycle relationship; the label encodes the state difference.

The "grew, not built" test on this family: a SoP-list rendering with a mix of all four states — say, 2 approved (green check), 5 reviewer-pending (amber pencil), 3 draft (amber pencil), and 4 missing (sand dash) — should read as "an honest snapshot of where the corpus is in its approval lifecycle." A reader should not have to ask "wait, what's the difference between draft and reviewer-pending" because the labels make it clear; a reader should also not need to read every label because the amber-vs-green-vs-sand visual pattern tells the lifecycle-position story at a glance.

---

## 3. State vocabulary

The family ships four canonical states at v1. The vocabulary is extensible but bounded — new states earn their place by surfacing in real consumer workflows AND fitting the lifecycle's "monotone progression toward approval" frame.

| State | Class | Color family | Icon | Label | When |
|---|---|---|---|---|---|
| `missing` | `badge-approval-missing` | sand (neutral) | `fa-minus` | "Not yet authored" | The artifact's identity exists in the corpus index (e.g., a cap-id is registered) but no source content has been authored. |
| `draft` | `badge-approval-draft` | amber (warning) | `fa-pen` | "Draft — not yet approved" | Source content exists; review has not been claimed. The source frontmatter typically carries `status: draft`. |
| `reviewer-pending` | `badge-approval-reviewer-pending` | amber (warning) | `fa-pen` | "Reviewer pending" | Source content exists AND `status: approved` is declared, but reviewer attribution metadata (reviewer name, version, date) is absent from frontmatter. The bridge state. |
| `approved` | `badge-approval-approved` | green (success) | `fa-check` | "Approved by {reviewer} · v{version} · {date}" | Source content exists, `status: approved` is declared, AND reviewer attribution metadata is present. The terminal state. |

**Why no `under-revision` state at v1.** The lifecycle's monotone-progression frame breaks if an approved artifact can return to in-flight. A re-review or update WOULD warrant a state, but: (a) Cena's current workflows don't have re-review yet (the SoP corpus is on its first review pass), (b) the right shape for re-review may be a SECOND badge (a "v2.1 in review" pill alongside the "Approved v2.0" badge) rather than a state-change on the primary badge. Defer until a real consumer surfaces.

**Why no `deprecated` state at v1.** Same reasoning: not yet needed. When it earns the warrant, the brand call is whether deprecated belongs in the approval-state family at all (it's not really an approval state; it's a lifecycle-end state) or whether it earns its own sibling family (`badge-lifecycle-{state}` with `deprecated` / `archived` / `superseded` as its members).

---

## 4. Visual treatment per state

The base shape is the canonical `badge` from haven (`badge` + `badge-with-icon` modifiers, per `components.css:825-850`). The per-state family substitutes the color tokens AND fixes the icon + label. The visual chrome (padding, border-radius, font, layout) is inherited from the badge canon; the family's contribution is the semantic state + the icon + label pairing.

### 4.1 `badge-approval-missing` — sand (neutral)

- **Semantic intent.** The artifact is registered but not authored. Distinguishes "we plan to have this" from "we have this in draft."
- **Class composition.** `badge badge-neutral badge-with-icon`.
- **Color tokens (inherited from `badge-neutral`).**
  - Background: `var(--color-sand-100)` (`#e4dfd7`).
  - Text: `var(--color-sand-600)` (`#777069`).
  - Border: `1px solid var(--color-sand-300)` (`#bbb6ad`).
- **Icon.** `fa-solid fa-minus` (a dash). The brand call: a dash is the most minimal "nothing here" signal; an `x` would read as "rejected / wrong" (it's not — the artifact just hasn't been authored yet); a question mark would read as "uncertain" (also wrong — we're certain it doesn't exist yet).
- **Label.** "Not yet authored" — full phrase. Avoid "Missing" (reads as broken / lost) or "Empty" (reads as accidentally-emptied). "Not yet authored" carries the right temporal frame: this is expected, just hasn't happened yet.
- **`role` attribute on the badge element.** `status` (per accessibility canon for state badges).
- **`title` attribute.** "Not yet authored" (matches the visible label).

### 4.2 `badge-approval-draft` — amber (warning)

- **Semantic intent.** Source content exists; review has not been claimed. The artifact is readable; reliance on its content is at the reader's risk because the brand promises authority only after approval.
- **Class composition.** `badge badge-warning badge-with-icon`.
- **Color tokens (inherited from `badge-warning`).**
  - Background: `var(--color-warning-50)` (`#faefda`, amber-50).
  - Text: `var(--color-warning-600)` (amber-600 in canon).
  - Border: `1px solid var(--color-warning-200)` (amber-200 in canon).
- **Icon.** `fa-solid fa-pen`. The brand call: pencil reads as "being authored / in-process"; this is the bridge-state icon (same as `reviewer-pending`) because both states are "in flight in the approval cycle." Full rationale at § 5.2.
- **Label.** "Draft — not yet approved" — the long-form label that names BOTH the state ("Draft") AND the missing condition ("not yet approved"). Compact form ("Draft") may be used in space-constrained contexts (table cells, dense lists) — see § 4.5.
- **`role` attribute.** `status`.
- **`title` attribute.** "Draft — not yet approved" (matches the visible label).

### 4.3 `badge-approval-reviewer-pending` — amber (warning) — THE BRIDGE STATE

- **Semantic intent.** Source content exists, `status: approved` is declared, but reviewer attribution metadata is absent. The artifact is approved in intent; the contract that makes approval *attributable* hasn't been satisfied yet. This is the state the badge family was specifically designed to express — without it, the system over-claims (green Approved when nobody is named) or under-claims (red error when the document is fine).
- **Class composition.** `badge badge-warning badge-with-icon`.
- **Color tokens.** Same as `badge-approval-draft` (inherited from `badge-warning`). Same family signal: "in flight in the approval cycle."
- **Icon.** `fa-solid fa-pen` (same as draft). Both states are "in process"; the icon couples them visually as a cycle pair.
- **Label.** "Reviewer pending" — compact, names the precise missing contract. (Round-3 evolved from the longer "V0 draft (reviewer pending)" to the shorter "Reviewer pending"; the full context lives in the `title` attribute.)
- **`role` attribute.** `status`.
- **`title` attribute.** "V0 draft — reviewer pending" (carries the full context for hover / AT readers).
- **The brand call this state codifies.** Amber + pencil = "in process" — NOT "warning of risk." See § 5.1 for the full register discipline.

### 4.4 `badge-approval-approved` — green (success)

- **Semantic intent.** Source content exists, `status: approved` is declared, AND reviewer attribution metadata is complete. The artifact carries Cena's approval authority.
- **Class composition.** `badge badge-success badge-with-icon`.
- **Color tokens (inherited from `badge-success`).**
  - Background: `var(--color-success-50)` (green-50 in canon).
  - Text: `var(--color-success-600)` (green-600 in canon).
  - Border: `1px solid var(--color-success-200)` (green-200 in canon).
- **Icon.** `fa-solid fa-check`. The brand call: a check is the canonical "complete / verified" icon and reads cleanly across cultures and contexts. A thumb-up would carry endorsement weight (over-stated); a badge / shield icon would carry institutional weight (under-grounds the human-reviewer signal).
- **Label.** `Approved by {reviewer} · v{version} · {date}` — the full attribution phrase. The interpolated values come from source frontmatter; if any are missing, the badge MUST NOT render as `approved` (the precondition contract is "all three present"). Compact form ("Approved") may be used in space-constrained contexts — see § 4.5.
- **`role` attribute.** `status`.
- **`title` attribute.** Same as the visible label, OR the compact label expanded to full attribution if the compact label was shown.

### 4.5 Compact / dense-context variants

When the badge sits in a space-constrained context (a data-table cell, a dense list row, a card meta-row with multiple badges), the long-form labels compress to the single-word state:

- `badge-approval-missing` compact label: "Not yet authored" → "Missing"? **No.** Even in compact contexts, "Not yet authored" stays because the alternative ("Missing") changes the reading. Compact rule: if the long form is two words or fewer, keep it.
- `badge-approval-draft` compact label: "Draft — not yet approved" → "Draft".
- `badge-approval-reviewer-pending` compact label: "Reviewer pending" → "Reviewer pending" (already compact).
- `badge-approval-approved` compact label: "Approved by {reviewer} · v{version} · {date}" → "Approved".

The `title` attribute always carries the full long-form phrase regardless of which label form renders. Compact form is for visual scan; full form is for the AT user and the hover.

When to use compact:
- The badge is one of several in a meta-row (per UConn round-3 implementation at `build-staff-pages.mjs:344-345` — strip-card meta uses compact form).
- The badge sits in a data-table cell whose row already names the artifact.
- A page-level header badge that names the document the page IS about — the document title carries the identity; the badge just states the lifecycle position.

When to use full form:
- A standalone approval line at the bottom of a document (the attestation surface).
- A list of items where the badge IS the primary identifier of the lifecycle position.
- Any time the badge is rendered without sibling context that names what's being approved.

The default rendering helper SHOULD accept a `compact: boolean` parameter; consumer surfaces opt-in by context.

---

## 5. Rationale — the load-bearing brand calls

### 5.1 The bridge-state register decision: when does amber mean "processing" vs. "warning"?

This is the highest-leverage call the family encodes. The brand uses amber across at least four surfaces today: `alert-warning` (advisory severity), `escalation` (safety routing), `review-marker` (draft scaffolding), and now this family's `draft` + `reviewer-pending` states. If amber meant different things across these surfaces, the brand's signal would erode. The discipline this spec codifies:

**Amber + the right icon = a precise register.** The icon disambiguates which kind of amber-signal is firing:

- **Amber + pencil (`fa-pen`)** → "In process, expected to advance." Used by: `badge-approval-draft`, `badge-approval-reviewer-pending`. The signal: the artifact is in the approval cycle; reading it is fine; reliance on it without attribution is at the reader's risk.
- **Amber + warning icon (`fa-triangle-exclamation` or `fa-circle-exclamation`)** → "Pay attention, may need action." Used by: `alert-warning` directive's content-typical icons. The signal: something requires the reader's attention.
- **Amber + escalation icon (e.g., `fa-arrow-up-right-from-square` or a routing icon)** → "Safety route." Used by: `escalation` directive's content. The signal: when something already broke, do this.
- **Amber + dashed border (no icon required)** → "Draft scaffolding." Used by: `review-marker` directive. The signal: pre-approval content that should not ship.

The four amber-signals are visually distinguishable because each pairs amber with a different secondary cue (icon, border style, content shape). An operator who learns the system reads:
- "amber + pencil-on-badge → in-process artifact, fine to read, attribution pending"
- "amber + warning-icon-on-block → pay attention to this paragraph"
- "amber + dashed-border-on-block → draft hole, don't ship with this present"

The amber color carries the common thread ("not yet final / pay attention") and the secondary cue carries the specific signal. Drift between any two of these — e.g., a future surface uses amber + pencil for "warning" instead of "in process" — would compromise the discipline. Codification at this spec is what prevents that drift on the approval-state surface; siblings codify the same discipline for their own surfaces.

**The contrast with red is what makes amber readable.** Red is reserved for: `alert-error` (critical risk), genuine error states (`badge-error` family in canon — not currently approval-related), and any state where the consequence is "stop, do not proceed." If the approval-state family used red for reviewer-pending, the reader would conflate "missing attribution" with "active danger" — wrong on both counts. The bridge case needs amber's softer urgency because the consequence is contextual (reliance is at risk) not absolute (the content is fine to read).

### 5.2 Why the pencil icon for draft + reviewer-pending (and not clock / hourglass)

The pencil icon was the resolution to round-3's exact problem: how do we visually distinguish "being processed" from "needs attention." The candidates considered:

- **Clock (`fa-clock`)** — reads as "scheduled / queued for review" but doesn't communicate that the artifact IS being authored / IS under review. The clock implies the work is waiting in someone's queue, not that it's actively in flight.
- **Hourglass (`fa-hourglass-half`)** — reads as "time elapsing / waiting" — same problem as clock, plus a slight "you'll have to wait for this" implication that doesn't match the read-now-but-reliance-pending semantic.
- **Spinner / loading (`fa-spinner`)** — animated, reads as "this is in active computation" (an AI generation, a network request). Wrong domain — approval review isn't synchronous computation.
- **Pencil (`fa-pen`)** — reads as "being authored / being edited / human in the process." This IS the signal. The pencil names the process is human-shaped (not machine-shaped) and that the artifact is in active authoring or review. The reader's mental model becomes "someone is working on this," which is the correct mental model for both draft (someone is drafting) and reviewer-pending (someone is reviewing the draft, attribution forthcoming).

The pencil's secondary virtue: it visually distinguishes from the check icon for `approved`. The reader scanning a list sees pencil → check as the lifecycle progression, which is the right mental shape ("being worked on → done").

The pencil's tertiary virtue: it pairs cleanly with `fa-pen` semantics in FA Pro's icon vocabulary. Future approval-state-adjacent primitives (an `update-pending` mode, a `revision-in-flight` indicator) can use pencil-family variants (`fa-pen-clip`, `fa-pen-to-square`) without breaking the family's icon discipline.

### 5.3 Why color + icon + label (the dual-cue floor, applied)

Per brand accessibility canon (`cena-health-brand/CLAUDE.md` § Accessibility): "Dual-cue for all success states: icon + text label, never color alone." The approval-state family applies this floor consistently:

- A reader with full color vision sees the color first, confirms with the icon, reads the label for precision.
- A reader with red-green colorblindness sees the icon (pencil = in flight, check = done, dash = not yet) and reads the label for full context.
- An AT user reads the badge's text content + the `title` attribute as the canonical signal.

The discipline is structural to the family: no state ships color-only. A future severity-tier extension that adds, say, a `badge-approval-rejected` state would carry red + an x-icon + the "Rejected" label — never red-only, never icon-only, never label-only.

### 5.4 Cross-district applicability — what stays constant, what varies

The approval-lifecycle vocabulary is the SAME across districts:
- **Document district** (SoPs, care plans, kitchen menus authored as docs): the four states + this family's tokens + icons + labels.
- **UI district** (staff-published surfaces, intake forms, care plans rendered as live UI): the four states + this family's tokens + icons + labels.
- **Diagram district** (workflow diagrams with approval lifecycle): the four states + this family's tokens + icons + labels.
- **Agent-emitted content** (future auto-emit pipelines with reviewer routing): the four states + this family's tokens + icons + labels.

**What CAN vary by district:**
- The badge's **placement** on the surface (header / footer / inline / sidebar) — the consumer surface chooses.
- The badge's **compact-vs-full label** form — the consumer surface chooses based on context density (§ 4.5).
- The badge's **interaction affordance** (whether clicking the badge opens an approval-history view) — surface-specific behavior, not part of this spec.

**What MUST NOT vary by district:**
- The state vocabulary (the four states + their names).
- The color family per state (sand / amber / amber / green).
- The icon per state (minus / pen / pen / check).
- The label text (compact or full forms; no synonymizing).

A consumer that renders the approval lifecycle with different colors or icons or labels has forked the family — the discipline is broken, and a reader who knows one Cena surface will be confused by the second. Codification at this spec is what enforces the cross-district consistency.

---

## 6. Quality-test notes

**Quiet-mode test.** Render a coverage page showing 30 SoPs at various lifecycle stages — say, 8 approved (green check), 7 reviewer-pending (amber pencil), 6 draft (amber pencil), 9 missing (sand dash). The page should read as "a coherent snapshot of where the corpus is" with the eye gravitating to the green count (the trust-able subset) and the amber blocks reading as "in flight, expected to land." If the amber states read as "this corpus is full of warnings" or "something is wrong," amber is over-weighting (likely cause: amber tokens drifted toward red, or the amber-50 ground is too saturated). If the missing states read as "irrelevant / hidden / not part of the corpus," sand is under-weighting (likely cause: missing badges rendering with too little contrast against page ground).

**Bridge-state honesty test.** Display three identical SoPs: one with full reviewer attribution, one with `status: approved` but no reviewer, one with `status: draft`. The first should render green. The SECOND should render amber + "Reviewer pending" — NOT green. If a developer or agent renders the second as green, the bridge state has been bypassed and the brand's signal is broken. The render-pipeline verification (place 5 of the codification 5-place check) should include a render test that proves the amber bridge fires when reviewer metadata is absent.

**Grew-not-built test.** A reader who has never seen the approval-state family before, looking at a single artifact's footer with "Approved by Dr. Marrero · v2.1 · 2026-05-15" in a green check badge, should immediately read it as "this is a clinical document that a named human approved at a known version and date." The badge should not require a legend or a tooltip to be readable — the icon + color + label combine into a self-evident signal.

**Cross-district coherence test.** A staff member who learns the family on one surface (a SoP doc, say) should correctly read the same badge on a different surface (a kitchen menu, a workflow diagram, an agent-emitted care-plan draft) without re-training. Amber + pencil + "Reviewer pending" should mean the same thing everywhere: "approved in intent, attribution pending." If a consumer surface departs from this — even with good local rationale — it forks the family and the discipline is broken. Surface-level reviews should catch the divergence.

**Affordance-fidelity test.** Per [`half-state-promises.md`](../../../.claude/rules/half-state-promises.md): the badge's signal must match what the source can actually back. The pre-round-3 UConn surface failed this test by rendering green when source frontmatter was empty; round-3 added the amber bridge state to deliver the contract. Future approval-state implementations MUST gate the rendering on the data contract: green renders ONLY when reviewer + version + date are all present in source; if any are missing, the badge falls back to amber `reviewer-pending`. A render-pipeline that does not enforce this gate has shipped a half-state promise and fails brand-fidelity review.

**Touch / focus floor.** The badge is informational (`role="status"`) — not interactive. No focus ring required. If a consumer surface makes the badge clickable (e.g., to open approval history), the clickable wrapper element (not the badge itself) carries the focus ring and the 44×44px touch target per haven accessibility canon.

---

## 7. What this brand spec does NOT cover

- **The base `badge` shape and chrome.** The badge's padding, border-radius, font, and base layout are owned by the `badge` family in `components.css` + the existing `badge.html` PL fragment. This spec covers ONLY the approval-state family of variants.
- **The `badge-with-icon` modifier.** Owned by the badge canon; this spec composes against it.
- **The full attribution-line variant** (a multi-line block with reviewer name, role, signature image, date — the kind of block that might surface at the end of a high-trust document). That is a separate primitive (an `attestation-block` derivative, possibly), not a badge. See [`haven-directive-styling.md`](haven-directive-styling.md) § 3.5 for the `attestation-block` directive's home.
- **Re-review / under-revision states.** Not v1; defer until a real consumer surfaces. The brand call at that point is whether to extend this family (`badge-approval-under-revision`) or add a sibling badge that composes alongside (`badge-revision-pending` paired with the prior `badge-approval-approved`).
- **The data-contract / frontmatter shape that drives the rendering.** Owned by the consumer surface's content layer (e.g., the SoP frontmatter spec at `tools/surface-emit/content-sot/sops/AUTHORING.md`). This spec mandates that the render-pipeline gate on the contract; the contract itself is defined elsewhere.
- **The render-pipeline helper function** (e.g., a `renderApprovalBadge(state, meta)` utility the build scripts can import). Phase 4 of the codification slice (render-pipeline utility extraction) authors that helper; the helper's API surface is informed by this spec but not specified here.
- **Dark mode.** v1 light-mode only. When a dark-mode consumer surfaces, the family inherits from `badge-warning` / `badge-success` / `badge-neutral`'s dark-mode treatments (already present in canon at `components.css:830-870`).
- **Animation on state transition.** v1 ships no animation when a badge changes state (e.g., draft → reviewer-pending → approved). Per brand restraint: a routine state change does not earn motion. If a surface ever wants celebratory animation on the draft → approved transition (an attestation moment, e.g.), it earns its own brand call — the badge itself stays still.

---

## 8. Extension discipline

A new state earns codification through the three-place change defined in [`.claude/rules/haven-primitive-codification.md`](../../../.claude/rules/haven-primitive-codification.md):

1. **This brand spec** — add the row to § 3 with: family classification (which color family, and why), the load-bearing brand call for the state's existence, the icon + label choices and their rationale.
2. **PL fragment variant** — add the state to `badge.html` (or a sibling `badge-approval-{state}.html` if the family grows beyond what fits cleanly into the parent).
3. **COMPONENT-INDEX `when-to-use` row** — name the specific data-contract condition that triggers this state.

Before authoring: ask the **lifecycle-position question first**. "Does this state advance the approval lifecycle, end it, or live outside it?" The answer routes:

- **Advances** (e.g., a future `partially-approved` intermediate) → extend this family; choose color from the in-flight register (amber by default; sand if the state is structurally absent rather than in-flight).
- **Ends** (e.g., `deprecated`, `archived`, `superseded`) → likely a sibling family (`badge-lifecycle-{state}`), not this one. Authoring is a separate brand call.
- **Lives outside** (e.g., `under-revision` — terminal-state artifact returning to in-flight) → check whether the right shape is a state-change on this family OR a second badge composed alongside. Often the second is right because it preserves the lifecycle's monotone reading.

Naming: state classes use `badge-approval-{state}` form with kebab-case. Icons should come from FA Pro's vocabulary; new icons earn their place by being unambiguous AND not conflicting with existing family members' icons. Labels follow brand voice (declarative, not imperative; no exclamation marks; no Title Case unless the label IS a proper noun).

---

## 9. Cross-references

- [`haven-cmd-palette-result-state-styling.md`](haven-cmd-palette-result-state-styling.md) — sibling state-family primitive at a different surface (inline-pill vs. badge). Together they encode the brand's discipline on the sand-vs-amber register distinction: sand for **completeness** (the cmd-palette `is-planned` shape), amber for **caution / in-process** (this family's draft + reviewer-pending shapes). The two specs settle into one coherent brand rule.
- [`haven-directive-styling.md`](haven-directive-styling.md) — establishes the amber register's discipline for severity-shaped block-level content (`alert-warning`, `escalation`, `review-marker`). This spec extends the same amber-with-the-right-secondary-cue discipline to badge-shaped inline content. The cross-spec rule § 5.1 documents the four amber-signals' visual distinctions.
- [`haven-data-table-styling.md`](haven-data-table-styling.md) — when the approval-state badge surfaces inside a data-table cell (the UConn coverage table is the canonical case at `build-staff-pages.mjs:608-622`), the compact form (§ 4.5) is what composes inside the row. The data-table spec covers the row chrome; this spec covers the badge that lives in the row.
- [`haven-account-form-styling.md`](haven-account-form-styling.md) — sibling spec with the dirty-marker primary-500 dot. Different domain (form-field state vs. document approval state), same brand discipline (a small visual signal that carries one precise meaning without competing for attention).
- [`.claude/rules/half-state-promises.md`](../../../.claude/rules/half-state-promises.md) — the discipline this spec satisfies: the pre-round-3 surface shipped a half-state promise (green badge with no reviewer attribution behind it). This family's existence and the render-pipeline gating discipline (§ 6 Affordance-fidelity test) are the deliver-the-contract resolution.
- [`.claude/rules/haven-primitive-codification.md`](../../../.claude/rules/haven-primitive-codification.md) — the codification-completeness rule this spec satisfies for place 3 (brand spec entry). Places 1 (PL fragment), 2 (COMPONENT-INDEX rows for all four states), 4 (consumer-surface references — UConn surface, future SoT-site render pipeline, AUTHORING.md for Document-district consumers, cena-apps wireframes when staff-approval flows wireframe), and 5 (render-pipeline verification — render test for the bridge state) are the responsibility of the broader codification slice.
- [`.claude/rules/specificity-first-primitive-selection.md`](../../../.claude/rules/specificity-first-primitive-selection.md) — when a consumer surface needs to render approval state, it MUST reach for this family (the most-specific primitive for the use case), not for a generic `badge-warning` + custom text composition. Reaching for the generic when this family exists is the specificity-first failure shape.

---

## 10. Source incident

UConn pilot staff-task-universe surface, round-3 implementation (`Knowledge/Projects/Cena Health/Partners/UCONN Health/surface/build-staff-pages.mjs`, 2026-06-26). Pre-round-3: the build script's `renderApprovalBadge`, `renderProcedureCardRow`, and `renderCoverageStatusBadge` functions all rendered green "Approved" for any document with `status: approved` regardless of whether reviewer attribution metadata was present in source frontmatter. Information Topology Designer review caught the over-claim — the green badge promised reviewer authority the source could not back, which was a half-state violation against clinician readers who would reasonably take the green as a trust signal.

Round-3 calibration (`build-staff-pages.mjs:343-345, 388-394, 615-621`): when `cap.status === 'approved'` AND `!cap.approval?.reviewer`, render amber `badge-warning` + pencil + "Reviewer pending" instead of green `badge-success` + check + "Approved". The change rippled through three render functions (one per surface context: list-row meta, standalone footer, coverage-table cell), which is a Codify-At-Creation Warning per [`haven-primitive-codification.md`](../../../.claude/rules/haven-primitive-codification.md) — four places in one file with the same conditional cascade. Codifying the primitive collapses the cascade into a single helper, which is phase 4 of the codification slice.

The shorter "Reviewer pending" label (vs. the round-3.0 longer "V0 draft (reviewer pending)") was the round-3.1 register decision: in dense contexts (strip-card meta, coverage-table cell), the long phrase compresses cleanly to the bridge-state's distinguishing word ("Reviewer pending"); the full context lives in the `title` attribute. The compact / full form discipline at § 4.5 of this spec generalizes the round-3.1 read.

Haven Steward review (`haven-steward-round-3-review.md` § P4) verdicted **promote** because:
- The approval-lifecycle vocabulary is cross-district (Document / UI / Diagram / future agent-emitted content all have the same shape).
- The bridge-state register is the load-bearing brand decision; without codification, future consumers will re-derive (likely incorrectly — the default reach for "approved but missing attribution" is either green or red, both wrong) and the brand's signal will fragment.
- The render-side conditional cascade is the Codify-At-Creation trigger: four places with the same logic in one file, today; many more places tomorrow if not codified.

This brand spec is place 3 of the 5-place codification gate; PL-fragment authoring (place 1) is phase 2 of the codification slice and inherits from this spec, particularly the per-state token + icon + label specification (§ 4) and the bridge-state register decision (§ 5.1).

The load-bearing brand call this spec settled: **amber + pencil means "in process," NOT "warning of risk."** The discipline carries forward to any future state-pill or state-badge family the design system codifies — and pairs with [`haven-cmd-palette-result-state-styling.md`](haven-cmd-palette-result-state-styling.md)'s sand-vs-amber discipline so the design system's register vocabulary stays coherent across surfaces.
