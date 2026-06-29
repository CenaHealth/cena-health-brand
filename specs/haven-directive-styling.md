# Haven directive styling — docx (Google Drive surface)

_Per-directive paragraph styling for `reference-cena.docx`. Drop-in `directive_styles` dict at the bottom. Brand-true treatment for the SoP review surface with Dr. Marrero, replacing the diagnostic placeholder shipped in the 2026-05-28 round-trip test._

---

## 1. Context

Cena Health's first SoP review cycle ships through Google Drive: markdown → pandoc (with `reference-cena.docx` + Lua filter) → `.docx` → Google Doc in suggesting mode → reviewer comments + edits → `pandoc --track-changes=all` back to markdown. The round-trip works (2026-05-28 test). One placeholder `alert-warning` style — diagnostic-loud amber on warm-amber bg — was sufficient to prove the pipeline; it is not brand-true and was never meant to be.

This spec replaces that placeholder with the full Haven directive vocabulary, tuned for what python-docx can actually express and what survives Google Docs' style-name flattening on import. The output is a styling pass on `apply_directive_styles()` at lines 246–316 of `.claude/config/drive-themes/generate-references.py`; this spec authors only the data block (lines 394–401), not the function.

§3.13 introduces a structurally new directive class: **resolved-asset directives** whose rendered output is a pre-built asset (HTML SVG / DOCX PNG) resolved from a slug attribute at emit time, rather than a styled paragraph block authored inline. Their visual treatment has two layers — the asset itself (owned by the haven-diagram entity) and the figure framing the asset sits inside (owned here). The brand spec section is consequently longer than §3.1–§3.12 because both layers need to be specified to land the directive coherently across HTML and DOCX surfaces.

---

## 2. Brand frame for this surface

The docx surface is the **Document district** in Haven's vocabulary, rendered through a substrate (Microsoft Word's OOXML schema, viewed in Google Docs) that compresses every brand decision into seven paragraph-level knobs: font, size, color, bold, space-before/after, single-left border, and shading. There is no native vocabulary for right/top/bottom borders, no rounded corners, no inline character spans authored at the directive layer, no card-like nested containers.

Three things hold steady across the compression:

- **Typography carries the most.** The reference docx already encodes Lora + Source Sans 3 + Source Code Pro, Cena Color System v2 anchors, the warm body color `#0D322D`, and generous line spacing. The directives inherit from that body voice and earn distinction through restrained delta — color, bg shading, weight, or a left border — not through replacing the type system.
- **Color carries the severity register.** Cena's functional palette is desaturated by design (Principle 6 — restraint as default): `red-500` is `#c13c3b`, not candy-red; `amber-500` is `#aa8232`, an earthen ochre. Saturated borders on the `-50` background steps give the severity ramp without breaking the warm-ground register.
- **Bg shading is the load-bearing graceful degradation.** Google Docs flattens style names on import but preserves per-paragraph background shading verbatim (validated 2026-05-28 round-trip). That makes shading the only way to assert "this is a callout block" once the `alert-warning` style name is gone. Use it deliberately — for genuine block-level callouts where the per-paragraph-rectangle reading is desired — and never for inline-shaped directives.

What the quiet-mode test means here: read a one-page SoP rendered through this theme with all directives removed. The page should still feel like Haven — Lora commanding the headings, Source Sans 3 working through the body at 1.5 line-height on warm-on-dark teal text. The directives are the brand moments inside that quiet base; if every paragraph carries a colored shade, the quiet base is gone and the directives have lost their function. Restraint discipline (Tier 3 Principle 11) is therefore the load-bearing constraint on how many directives a single SoP uses, not how each one is styled.

The "grew, not built" test on this surface: directives should feel like the document's own paragraphs given slightly more emphasis, not like inset cards parachuted in from another design system. The treatments below use the **paragraph itself** — its background, its left rule, its slight color shift — rather than reaching for chrome the format cannot deliver.

---

## 3. Per-directive spec

Color values throughout reference Cena Color System v2 (`Lab/cena-health-brand/_tokens/generated/palette.css`). All `-50` and `-500` references are to v2 family steps.

### 3.1 `alert-info`

- **Semantic intent.** Neutral context — a note, a clarification, a pointer to a sibling document. The "for your awareness" register; lowest severity. In SoPs: backstory on why the procedure exists, where a related policy lives, mode-specific caveats.
- **Visual treatment.** Cyan-50 background (`#d2f2fa`), cyan-500 left border (`#236b8f`, 18-unit thick), Source Sans 3 body, body color preserved, 8pt space before/after. The cyan family is the system's "info" register — a desaturated slate-blue that reads as informational rather than alerting.
- **Rationale.** Principle 6 (restraint) — info is the lowest-severity advisory, so the visual delta is smallest. The bg + left rule asserts "this is a block" without competing with body text. Color carries severity (cyan = info per the functional family naming); the type stays in the body's working voice.
- **Quality-test notes.** Quiet-mode: at one or two per page, this reads as a quiet aside. Grew-not-built: the left rule echoes the blockquote treatment already in the theme (teal-500 quote border), so this directive feels like a sibling of an existing convention, not a new chrome. Defer to Aaron only if the cyan family reads as "too blue" against the warm ground in Google Docs preview — fallback option is to drop the bg and lean only on the cyan border.

### 3.2 `alert-warning`

- **Semantic intent.** "Pay attention here" — a constraint, a common pitfall, a thing that bites you if missed. One step up from info: the reader should pause. In SoPs: phrasing tips on sensitive conversations, edge cases that trip new staff, deadlines that are easy to miss.
- **Visual treatment.** Amber-50 background (`#f8f1e3`), amber-500 left border (`#aa8232`), Source Sans 3 body, body color preserved, 8pt space before/after.
- **Rationale.** Cena's amber-500 is `#aa8232` — an earthen ochre, not a candy yellow. This is the brand's restraint at work: warning reads as warning without breaking the warm-ground register. The placeholder used `#a8420c` / `#fef3e2` (diagnostic-loud, picked for visibility in the round-trip test); the v2 amber family is the brand's actual warning vocabulary and reads as cohesive with the rest of the palette. Same bg/border architecture as info — severity carried by hue alone, not by adding chrome.
- **Quality-test notes.** Quiet-mode: amber-50 is the warmest of the four callout backgrounds; on the body's warm body register, it reads as a slightly more amber paragraph rather than a foreign rectangle. Grew-not-built: amber is hue-adjacent to the warm ground (both at H:75-85), so the directive feels like the page itself raising an eyebrow, not an injected widget. **Replaces the 2026-05-28 placeholder.**

### 3.3 `alert-success`

- **Semantic intent.** Confirmation, healthy state, a positive outcome to recognize. In SoPs: "if you see this state, you're done"; ratification at the end of a procedure step; the "on track" register in escalation flag rows.
- **Visual treatment.** Green-50 background (`#e5f7eb`), green-500 left border (`#2c845c`), Source Sans 3 body, body color preserved, 8pt space before/after.
- **Rationale.** v2's green family (H:155–165) is distinct from sage (H:145–148). Sage is brand-identity; green is functional-success. Keeping them separate preserves the brand's analogous teal→sage hue shift without overloading sage with a functional-color meaning. Green-500 `#2c845c` is the desaturated success per Cena's functional palette — restraint applied to a register that often defaults to candy mint elsewhere.
- **Quality-test notes.** Quiet-mode: success callouts should be rare in SoPs (most steps don't end with celebration) — if a draft uses success often, the draft is over-affirming. Restraint test should flag that pattern. Grew-not-built: the green is hue-close enough to sage to feel related to the brand's analogous logic, while saturation difference keeps the functional reading intact.

### 3.4 `alert-error`

- **Semantic intent.** Critical risk — "do not do this," "this is unsafe," "wrong by default." Highest severity in the callout family. Distinct from escalation: error is the unsafe state to avoid; escalation is what to do when something already broke.
- **Visual treatment.** Red-50 background (`#ffedea`), red-500 left border (`#c13c3b`), Source Sans 3 body, body color preserved, 8pt space before/after.
- **Rationale.** Cena's red-500 `#c13c3b` is the brand's most assertive functional color — already a warm-shifted earthen red, not a fire-engine red. The bg + border architecture matches the rest of the callout family; severity is carried entirely by hue. Bold weight on the body would push this into "shouting" territory and break Principle 6 — color does the work.
- **Quality-test notes.** Quiet-mode: at most one error callout per SoP — if a procedure has three "do not do this" blocks, the procedure itself is poorly bounded. Restraint is the policy check, not a typography choice. Grew-not-built: red passes the warm-ground test (H:25, warm-red-orange) — it does not feel cool or clinical against the rest of the document.

### 3.5 `card`

- **Semantic intent.** A composite content block — a labeled grouping of related material that wants to read as "one thing." In SoPs: Scope tables, Quick-reference checklists, Block glossaries (see the four SoP HTMLs — every card wraps either a kv-table or a checklist).
- **Visual treatment.** Sand-50 background (`#fbfaf8`) — *very subtle warm shift* above the body's warm ground, no border, body font preserved, 8pt space before/after, slightly increased space-after to give the card "settling" room. **No left rule** — cards are containers, not advisories; a left rule would mis-cue them as callouts.
- **Rationale.** This is the spec's most honest graceful-degradation moment. A card is conceptually a *bordered container with a labeled header and a body* — none of which python-docx expresses as a single style. The paragraph-style implementation creates a very gentle bg shift that reads as "this paragraph belongs to a grouping" while accepting that the visual chrome of the HTML card (full border, rounded corners, divider line under header) is unavailable. The sand-50 against the page body's warm ground produces a near-imperceptible warm-on-warm shift — restraint as the *implementation*, not just the styling.
- **Quality-test notes.** Quiet-mode: a card is *more* quiet than a callout, not less. If the card bg is visually loud on the page, the bg shift was too aggressive. Grew-not-built: the sand-50 is the same value as the warm ground itself in v1 (and identical to it numerically in v2's neutral); the directive is the page being slightly more itself, which is exactly the "infrastructure that grew" reading. Defer to Aaron if Google Docs renders sand-50 as effectively invisible in preview — fallback is to switch to sand-100 `#e6e4e0`, accepting a more visible warm-tone shift.

### 3.6 `card-title`

- **Semantic intent.** The label that names a card's contents. In SoP HTML: the `<h3>` inside a `card-header`. Always paired with a card body; never freestanding.
- **Visual treatment.** Lora bold (matches body's heading font), 12pt, teal-700 color `#1e5149` (matches H3 in the existing theme), 4pt space before, 2pt space after, sand-50 bg to match the card body it labels.
- **Rationale.** Card-title is fundamentally a small heading inside a container. The treatment matches the existing H3 register (Lora-overridden-to-Source-Sans in the theme already; this directive *uses Lora* to give the card-title a hair more authority than an H3 because it labels a specific block rather than a section). Teal-700 is the established section-label color in the theme — re-using it keeps the card-title register coherent with H3 elsewhere.
- **Quality-test notes.** Quiet-mode: card-title is a *quieter* heading than H2/H3 because it sits inside a container; the size + color delta is intentionally smaller than H2→H3 to avoid competing. Grew-not-built: the sand-50 bg matches the card body, so the title visually sits within the container even though no border ties them. Honest limit: in Google Docs the title and body can read as two separate paragraphs with no visual seam between them — that is acceptable degradation since the content itself ("Each week, for every participant" / list of checks) is self-clarifying.

### 3.7 `card-body`

- **Semantic intent.** The content area of a card. Holds the actual paragraphs/lists/tables.
- **Visual treatment.** Source Sans 3 body, body color, body size, sand-50 bg, 4pt space before, 8pt space after.
- **Rationale.** Card-body is essentially "Normal paragraph but inside a card" — same type, same color, same line height, only the bg shifts. This is the simplest entry in the spec by design: the body inherits everything from Normal and only declares the bg shift that ties it visually to the card-title above.
- **Quality-test notes.** Note that **tables inside a card body** will render with their own borders + shading because the Word table style is applied independently. The card-body bg only colors paragraphs, not table cells. This is fine and was true of the SoP HTML too (the kv-tables inside cards in the HTML have their own visual treatment). Honest limit: if a card body contains a paragraph followed by a bullet list, the bullet list won't inherit `card-body` styling unless the markdown source explicitly opens a fenced div around the whole block — which the `surface-emit` handlers should already do, but is worth validating with Marrero's first round.

### 3.8 `attestation-block`

- **Semantic intent.** The approval block at the bottom of every SoP. The moment the SoP becomes operational. Highest brand weight in the directive vocabulary — this *is* the cena commitment surfacing in the document. Distinct from callouts (advisory) and cards (container): attestation is the document declaring its own authority status.
- **Visual treatment.** Sand-100 background (`#e6e4e0`) — perceptibly warmer than the page ground, signaling "different register"; sand-300 left border (`#bcb8b1`, the brand's `border-default`), Lora at 11pt (slight uplift from body), body color, 12pt space before, 8pt space after. The sand-300 border matches the theme's `border_color` token, so this directive looks like a sibling of the existing blockquote/table border language.
- **Rationale.** This block is structurally *like* a card — bg + border — but it earns more visual weight because it's the highest-stakes block in the document. The sand-100 (one step warmer than the card's sand-50) and the Lora type (slight uplift from body's Source Sans 3) both push it toward "this is more than container, this is signature." Lora here is doing the same work it does in the document's H1 and quotes: signaling editorial weight. The sand-300 border is hue-coherent with the body's warm ground rather than reaching for teal — a teal-bordered attestation would push it toward "interactive button" register (the v2 `color-primary` is teal-700), which is the wrong reading. Sand is the document's own warm authority.
- **Quality-test notes.** Quiet-mode: one attestation per SoP, always at the end — the cadence is governed by SoP structure, not by the directive's restraint. Grew-not-built: the sand-100 + sand-300 combo is the warmest container in the spec; it reads as the document settling into its own warm ground for the sign-off, which is the right narrative. The brief's "highest brand weight" requirement is honored by Lora type + sand-100 bg (the only bg that is *visibly* warmer than the page) + the heavier 12pt space-before that isolates the block.

### 3.9 `attestation-gate`

- **Semantic intent.** An individual gate within a multi-gate attestation block. Multi-gate is the RD/admin SoP pattern (clinical-accuracy + operationally-true + signed-off all required); single-gate (Care Coordinator) collapses to a single `attestation` block. Each gate is a labeled line: "Clinically accurate — clinical lead."
- **Visual treatment.** Source Sans 3 body, body color, body size, sand-100 background (inherits the attestation block's warm shift), **no border**, 2pt space-before/after (tight stacking — these are list items inside the attestation block, not standalone blocks).
- **Rationale.** A gate is functionally a list item within the attestation container. In the HTML it's an `<li class="attestation-gate">` inside `<ul class="attestation-gates">`. In docx-paragraph-style land, we approximate that by giving each gate the same bg as the parent attestation (so they sit inside the same warm container visually) and tight vertical spacing so they don't read as separate blocks. No border, no color shift — the gate's identity is the *line* it occupies, not chrome. The gate's role is to be one of N parallel commitments, not to compete with siblings.
- **Quality-test notes.** Quiet-mode: gates are intentionally restrained because the attestation block already carries the visual weight; the gates inside should read as tight, parallel, scannable. Grew-not-built: the sand-100 inheritance ties them visually to the attestation block above without requiring nesting. Honest limit: if the markdown emits each gate as a top-level fenced div rather than nested under the attestation, the gates will *visually* read as siblings of the attestation rather than children. This is a markdown-source concern, not a docx-styling concern — worth flagging to whoever owns the directive handlers but not solvable here.

### 3.10 `escalation`

- **Semantic intent.** "If X breaks or surfaces, contact Y." Safety-information weight without being alarming. Distinct from `alert-error` (which is "do not do this" — preventative): escalation is "when something has gone off-script, here's the route." In SoP HTML this is the "If a safety or medical concern comes up" block; in Care Coordinator's escalation flags it's the red-flag/yellow-flag/green panels (though those are alerts, not escalations strictly).
- **Visual treatment.** Sand-50 background (`#fbfaf8` — same as card, the warm ground itself), amber-700 left border (`#735311` — a darker, more grounded amber than the warning border), Source Sans 3 body, body color, 8pt space before/after.
- **Rationale.** Escalation lives in a register between warning and error. Using amber-700 (darker than warning's amber-500) gives it slightly more weight than `alert-warning` without going full red — which would mis-read as "do not do this" rather than "here's the route when something happened." The bg is sand-50 (the warm ground), so the directive's emphasis comes entirely from the left rule. This is the "infrastructure quietly providing the route" reading — escalation is the document offering safety, not raising alarm. The contrast with `alert-error`'s red-500 border is deliberate: error and escalation are visually distinguishable on the page, and the reader learns to treat them differently.
- **Quality-test notes.** Quiet-mode: escalation should appear once per SoP at most — at the boundary where the role hands off to a more accountable party. If it appears more than once, the SoP probably has scope drift. Grew-not-built: the amber-700-on-sand-50 combo is the spec's most "structural" treatment — quiet bg, strong rule. It reads as a piece of infrastructure (a routing rule) rather than as an advisory.

### 3.11 `decision-branch`

- **Semantic intent.** "If A → do X; if B → do Y" forks within a procedure. Structural, not advisory. In the SoP HTML this is a multi-row container; in markdown source it's a fenced div wrapping a sequence of condition/outcome pairs.
- **Visual treatment.** Sand-50 background (`#fbfaf8`), sand-300 left border (`#bcb8b1` — the brand's `border-default`, **matches the table/blockquote border color in the theme**), Source Sans 3 body, body color, body size, 8pt space-before/after.
- **Rationale.** Decision-branch is structurally the most "container-shaped" directive after card and attestation — it's a wrapper around N rows of branching logic. The treatment uses the same sand-50 bg as escalation but with the *neutral* sand-300 border instead of the amber-700 rule. The reason: decision-branch is not advisory, not severity-flagged; it's pure structure. The neutral border signals "this is a logical block" without coloring it as a warning or callout. Hue-matched to the theme's existing `border_color` (`#bcb8b1`) so the directive reads as a sibling of the document's other structural rules (table borders, blockquote rules), not as new chrome. The reader's eye learns: amber rule = severity (warning/escalation), sand rule = structure (decision-branch).
- **Quality-test notes.** Quiet-mode: decision-branches are common in SoPs — a single SoP might have 3-4 of them. The restraint test isn't on per-directive count here; it's on whether *individual rows* within a branch are tight and scannable. Grew-not-built: the sand-on-sand treatment is the spec's most "document grew this naturally" moment. The branch reads as the document folding back on itself to handle two cases, not as a widget. Honest limit: the HTML's row-by-row treatment (condition column → arrow → outcome column) does *not* translate to paragraph styling. In docx, each row will appear as a single paragraph carrying both condition and outcome text. The markdown source can format this as `**condition** — outcome` per row to preserve the contrast within a paragraph; that's a markdown-authoring convention to capture in the SoP authoring guide, not a styling decision here.

### 3.12 `glossary-def`

- **Semantic intent.** A term's definition. Paired with `glossary-term` (the term being defined). In SoP HTML, glossaries live inside a card containing a `kv-table` — each row is `<th>term</th><td>definition</td>`. The directive vocabulary expresses this as `glossary-term` + `glossary-def` so markdown source can author definition lists.
- **Visual treatment.** Source Sans 3 body, body color, body size, 240-twip left indent (the same indent the blockquote uses to clear its border), no border, no bg, 2pt space-before, 6pt space-after.
- **Rationale.** A glossary definition is a slightly-indented paragraph that follows its term. The indent gives the definition the visual relationship to the term without requiring bg shading (which would be too loud for a list of definitions). The reader scans the unindented term and then reads the indented definition. The treatment is intentionally minimal — glossaries are dense by nature; adding bg or border to each definition would overwhelm the reading.
- **Quality-test notes.** Quiet-mode: glossaries are uniformly quiet — the consistency *is* the reading affordance. Grew-not-built: definitions sitting slightly inset under their terms reads like a printed reference book, which is the right register for a clinical SoP glossary. Honest limit: this directive only works if `glossary-term` is rendered as a paragraph style as well (see below), or if markdown source uses a definition-list construct that pandoc handles natively. If pandoc doesn't recognize the term/def pairing structurally, the visual relationship breaks.

### 3.13 `diagram`

- **Semantic intent.** A workflow swim-lane diagram embedded inline in a SoP (or any prose surface that authors visual workflow). Distinct from every other directive: the others style a *paragraph the author wrote*; this one resolves to a *pre-built asset* the spec→render pipeline produced from a workflow YAML+markdown source. Author writes `:::diagram{workflow="escalation-phq9-positive"}`; emitter resolves the slug, runs `spec_to_haven_svg.mjs`, embeds the result inline. The directive is the **named-asset embed**, not the asset itself.

- **Visual treatment.**
  - **Figure-frame container.** A `figure`-semantic block with `8pt space-before / 12pt space-after` (more after than before — the figure earns settling room because it's denser than body prose). No card-style background; the diagram already carries its own warm-ground (`var(--color-surface-page)`) inside the viewport. The figure has **no left rule** (left rules are the callout/escalation family signature; a diagram is structural, not advisory). The interactive HTML viewport keeps its existing 1px `border-muted` hairline; that hairline is the figure's only chrome.
  - **Caption typography (figure-caption register, NEW — earns its place).** Source Sans 3 at 12pt (one step up from body 11pt — figures want a quietly more-formal caption, not a smaller one), sand-700 `#5a544e`, regular weight, 6pt space-after. Positioned **above** the figure — the convention SoPs already follow. Below the caption, an optional **micro-eyebrow** in Source Code Pro 10pt uppercase, sand-600 `#777069`, letter-spacing 0.08em, reading `WORKFLOW DIAGRAM` (matches the H3 eyebrow register in body prose). The eyebrow cues the reader to a register shift before the visual asset lands — the same forcing-function the lane labels do inside the diagram.
  - **Description paragraph (`diagram-meta`, below the figure).** Source Sans 3 body register at 13pt (one step up from body 11pt — captions earn slight uplift over body to read as "about the figure" rather than "next thought"), text-muted color `#555` on the HTML surface, sand-700 on docx. Italic. 16pt top-border-spacing (a hairline `border-muted` separator above), `max-width: 900px` constraint on HTML so the description line-length doesn't blow past the body measure even when the diagram is wider.
  - **Figure measure.** On the HTML surface, the figure honors `max-width: 1600px` (the existing `diagram-frame-wrapper` constraint) but is constrained by the parent `.document-shell` measure when embedded in SoP body — which the existing wrapper already handles via `margin: 0 auto`. On the docx surface, the embedded PNG sizes to the page content width (Pandoc default behavior); no per-directive width override needed because the print-zoom pipeline already produces a one-page-width-fit PNG.

- **Rationale.**
  - **Why no left rule.** The left rule is the directive family's signature for advisory blocks (callouts) and structural blocks (escalation, decision-branch). A diagram is neither — it's *the document showing its workflow visually*, the same way an org chart or a swim-lane is "the workflow itself," not a comment on the workflow. Adding a left rule would mis-cue the diagram as an advisory aside. The hairline border on the interactive viewport (HTML) and the natural figure-on-page convention (DOCX) do the figure-framing work without borrowing chrome from the family signature.
  - **Why caption-above + description-below.** Existing rendered diagrams already use this convention (the printed-textbook figure pattern). Caption above announces "what figure this is" before the reader engages the visual; description below gives prose context once they've scanned the asset. This survives across surfaces (HTML, DOCX, future PDF) without per-surface argument.
  - **Why Source Sans 3 12pt (not Lora) for caption.** Lora carries authority — H1/H2 register. A figure caption competing with H2 would break the reading hierarchy (the figure outranks its enclosing section, which is wrong). Source Sans 3 at 12pt + the Source Code Pro eyebrow above gives the figure announcement weight without challenging section hierarchy. This is the **Lora-commands-Source-Sans-works-teal-punctuates** formula applied to the figure surface: SoP body is Source Sans 3 at 11pt; figures get +1pt + an eyebrow, not a different family.
  - **Why an eyebrow above the caption.** The existing rendered diagrams use a `draft-marker` slot ("DRAFT • v0.1 • pending Marrero review") in Source Code Pro amber-700 at the same screen position. Once the diagram ships final, that slot reads empty unless we give it a permanent register-shift function. **Repurposing the slot as a register-cuing eyebrow** ("WORKFLOW DIAGRAM" in sand-600 mono) preserves the slot's "this is a register shift" signal without depending on draft state. When a diagram IS in draft, the eyebrow reads "DRAFT WORKFLOW DIAGRAM — v0.1" — eyebrow keeps its function across lifecycle.
  - **Why no bg on the figure container.** Three of the existing directive families use bg: callouts (severity register), card (structural container), attestation (commitment register). A figure has none of those readings — it's a content-replacement, not a content-grouping. A bg on the figure would compete with the warm-ground inside the viewport, reading as a doubled warm rectangle (the figure tinted, the viewport tinted, with a hairline between). The cleanest read is: prose → caption → asset (in its own viewport, with its own internal warm-ground) → description prose. The figure inherits the page's warm ground; no second tint.
  - **Why HTML controls live in a hardened `.btn-secondary` variant** (Tier 1 PL promotion landed 2026-06-07). The shipped pan/zoom controls (bottom-right) had previously read as widgets imported from outside the brand: white bg, sand-borders, generic mono labels. The brand-correct treatment is the existing `.btn-secondary` register in haven-ui — `secondary-100` (sand-100) bg, `secondary-700` (sand-700) text, hover `secondary-200` (sand-200) bg-shift, `:focus-visible` `secondary-700` outline. **The buttons recede unless interactive.** The diagram-viewport surface adds a 1px `sand-500` border on idle so the WCAG 1.4.11 non-text boundary clears 3:1 (3.99:1 measured) against the sand-50 viewport — the canonical `.btn-secondary` is border-less and relies on bg-shift alone, which fails the criterion on warm-ground surfaces. This hardened variant is a candidate pattern for the canonical `.btn-secondary` itself; see the vault-wide `.btn-secondary` audit candidate at `~/.claude/plans/scratch/separate-audit-2026-06-07-btn-secondary-vault-wide-wcag.md`. The sand-on-sand focus ring (vs the icon-canon teal-ring) preserves Haven's reservation of teal for state-changing commits — same discipline `.chat-chip` carries in haven-ui (components.css:3118-3121: *"Haven reserves teal for commits per DESIGN.md §Brand-taste"*). Pan/zoom is reversible viewport manipulation, not a commit; the chrome stays in the sand register. The zoom indicator (bottom-left) and help text (top-left) drop white-with-alpha for `color-mix(in srgb, var(--color-sand-50) 92%, transparent)` — matching the warm-ground rather than punching a cool hole in it; the indicator earns font-weight 500 + tabular-nums so live-state distinguishes from help's reference register. Touch targets 44×44 matching `.btn-icon` canon. FIT button uses `fa-arrows-to-dot` (FA Pro v7 vendor blob) — inward arrows converging to center reads as "fit to viewport" correctly (vs. fa-expand, which reads as "expand outward / fullscreen"). The Tier 1 PL fragment lives at `pattern-library/components/doc-diagram-viewport-controls.html`; the canonical CSS lives in `components.css`, read at build time by the A2 extractor in `tools/surface-emit/spec_to_haven_svg.mjs` so the iframe srcdoc inherits future haven discipline automatically.

- **Quality-test notes.**
  - **Quiet-mode.** Read a one-page SoP with a diagram embedded mid-body. The page should still feel like Haven — body prose flows in Source Sans 3, then a figure announces itself with eyebrow+caption, the diagram lives in its own warm viewport, prose resumes after the description. The figure should read as **the document expanding into its own visual workspace for a moment**, not as a chrome-heavy insert. If the figure feels like a frame around a screenshot, the caption typography is too loud or the eyebrow has lost its slot-shift function.
  - **Grew-not-built.** The hairline-only frame (no left rule, no bg) + the eyebrow-above-caption-above-figure convention is what a printed textbook does. The figure feels like the document making room for a visual it was always going to need, not a parachute-in widget. Lora staying out of the caption is what keeps it from reading as a competing heading.
  - **Per-SoP cadence.** Most SoPs will carry one diagram (the workflow's primary view); some may carry zero (the workflow is verbal-procedural without a swim-lane). Multiple diagrams per SoP is suspicious — if a SoP needs three diagrams, it probably needs three SoPs. Restraint cap: **≤2 diagrams per SoP**, surfaced in the SoP authoring guide.
  - **Dual-surface parity.** The HTML interactive diagram and the DOCX static PNG must feel like the same figure in two viewports — same caption typography, same eyebrow, same description below. Only divergence: HTML has live pan/zoom controls; DOCX shows the deterministic fit-to-page snapshot. Read both side-by-side: the caption + eyebrow should be visually identical (same font, same color, same spacing); the asset content should be the same (the PNG IS the HTML at print zoom); the description below should be the same prose. If they diverge on caption or eyebrow, the spec broke.
  - **DOCX page-fit.** The pan-zoom plan already settled this — Chrome headless renders the diagram at `--print-zoom` (computed at render time as `structural-min-width / target-print-width`); the PNG is one-page-width-fit. The brand spec does not need to address multi-page split logic because the print-zoom mechanism makes that case impossible by construction. If a future diagram is so structurally wide that print-zoom shrinks text below 6pt (an honest legibility floor), the fix is upstream — split the workflow, not the figure. Worth flagging in the SoP authoring guide as the implicit constraint: "if your workflow spec produces a diagram that doesn't survive print-fit at ≥6pt label text, the workflow is too dense for one figure."
  - **Defer to Aaron** if the eyebrow-above-caption pattern reads as ceremony in the final SoP render — fallback is to drop the eyebrow and let the caption alone announce the register shift. The eyebrow earns its place at the slot-shift function; if SoP readers signal it's noise, it goes.

### 3.14 `review-marker`

- **Semantic intent.** Visible scaffolding for unresolved review prompts in pre-approval SoP drafts. Wraps a question or open item the team is asking a clinical reviewer (or other approver) to resolve before the SoP ships. Distinguishes "the team is asking the reviewer this" from "the procedure says this." Distinct from every other directive: not advisory severity (the `:::alert-*` family), not safety-routing (`:::escalation`), not a structural container (`:::card`, `:::attestation-block`) — it is *content that should not be present in an approved SoP*, made visible so its presence is obvious.
- **Visual treatment.** Amber-50 background (`#faefda`), amber-700 left rule (`#735311`), dashed style on the HTML surface (solid on docx where dashed paragraph borders don't survive Google Docs round-trip), Source Sans 3 body, body color, 8pt space before/after. The amber-700 rule is the same value used for `escalation` but the *dashed* style + the differently-shaped amber-50 background (warmer than escalation's sand-50) makes the register distinct: escalation is "safety route, infrastructure;" review-marker is "draft scaffolding, not approved." Author's content typically opens with a bolded `[Needs <reviewer>]` prefix that names whose answer is required.
- **Rationale.** The failure shape this prevents: a coordinator on a live call reading "escalate per protocol — `[NEEDS VANESSA / MARRERO …]`" and not knowing whether the bracketed content is procedure or a hole. Inline `<code>` formatting (the prior treatment) collapses the marker into the procedural prose. A block-level directive with its own visual chrome quarantines the marker — visible on both surfaces (reviewer sees it in Google Docs, coordinator-facing HTML render makes it impossible to miss), failing closed when leftover markers ship to operational use. The dashed left rule signals "draft" without overloading any callout register; the amber palette signals "pay attention to this hole" without alarming.
- **Quality-test notes.**
  - **Quiet-mode.** A review-marker should NEVER appear in an approved SoP. If one is rendered in the final shipped version, the chrome's job is to make that obvious enough to catch on a quick scroll. The dashed border + warm bg achieves this without breaking the document's quiet register on every other page (because no approved SoP will have a marker rendered).
  - **Grew-not-built.** The dashed amber border is the haven document district's "draft / pending" register. It rhymes with the `document-draft-banner` (the masthead-level draft signal); both signal pre-approval state, both use the warning palette without going full red, both pair with the attestation block's `.is-pending` state. The reader learns: warm-amber-dashed = "this isn't final yet."
  - **Per-SoP cadence.** No cap. A draft might have 0 review-markers (close to ship) or 10 (early-stage). The marker's visibility makes high counts self-evident; restraint is enforced by the approval gate (markers must be resolved before sign-off), not by directive count.
  - **Dual-surface parity.** HTML dashed + DOCX solid is an acceptable divergence: Google Docs renders solid paragraph borders cleanly; dashed borders fragment on round-trip. Both surfaces preserve the amber-700 + amber-50 register; the dashed-vs-solid is a cosmetic detail the brand can absorb.
  - **Strip-before-ship.** Every review-marker in source markdown should be removed (along with its question being answered into the relevant procedure) before the SoP transitions to operational state. The chrome makes leftover markers visible on quick visual scan; the approval gate (attestation sign-off) is what enforces the removal. Source incident: 2026-06-07 UX-panel review of Care Coordinator SoP found `[NEEDS / CONFIRM]` markers rendering as inline `<code>` inside binding clinical instructions — Content Design + Plain Language Positioning both flagged block-tier risk to Marrero's read.

---

## 4. Inline-directive handling

Three directives are semantically inline (within a sentence): `badge`, `screen-ref`, `glossary-term`. The current `apply_directive_styles()` function only produces `WD_STYLE_TYPE.PARAGRAPH` styles. The brief named three honest choices per directive:

- **Choice A:** Treat as paragraph style anyway (only works for standalone-block use).
- **Choice B:** Recommend extending `apply_directive_styles()` to support `WD_STYLE_TYPE.CHARACTER`.
- **Choice C:** Recommend rendering as plain markdown emphasis/inline code at the docx layer.

Per-directive decision:

### 4.1 `badge` → **Choice C (defer to markdown emphasis)**

In SoP HTML, `badge` is a small inline label ("v0.1", "draft", role name). Most badge uses in the SoP drafts surface in the **document masthead** (the meta row with version + role + date) — that block is already laid out as `document-meta-item` paragraphs, not as inline badges within prose. In the rare case a badge appears mid-sentence, render it as **bold inline text** in the markdown source (`**v0.1**`). The semantic intent (small inline label) survives; the visual chrome (rounded pill, bg color) is lost gracefully.

Why not Choice B: character styles are doable in python-docx, but `badge`'s use is rare enough in the SoP corpus that the engineering cost outweighs the brand cost. Punt to Choice C; revisit if Marrero's review surfaces a high-volume badge use we missed.

### 4.2 `screen-ref` → **Choice B recommended (character style extension)**

`screen-ref` is the highest-value inline directive in the SoP vocabulary. It appears 4+ times per SoP (every procedure step that references a screen location: "In Care-coordinator app → Roster"). Reading the HTML, the visual treatment is a chip-shaped inline element with a sand-50 bg, sand-200 border, and a small icon. **Choice C (rendering as inline code or bold) would lose the wayfinding affordance** — the screen-ref reading as "go here in the system" is precisely what distinguishes it from emphasis.

Recommend extending `apply_directive_styles()` to support character styles when `props["style_type"] == "character"`. The extension is small — `WD_STYLE_TYPE.CHARACTER` styles support font, size, color, bold, and (via run-level XML) shading and borders, though border-on-run is rarely well-supported in OOXML and would not survive Google Docs round-trip cleanly. For `screen-ref`, a workable character-style treatment is:

- font: Source Code Pro (the brand's mono — picks up the "screen reference / system path" semantic)
- size: 10pt (one step down from body)
- color: `#1e5149` (teal-700 — the interactive register)
- bold: false

This loses the rounded-pill chrome but gains the typographic register: "this is a place in the system" reads as monospaced/teal in the same way clinical identifiers read as monospaced elsewhere in the brand. The trade-off is honest: a chip becomes a typed-text inline.

**Do NOT implement the character-style extension as part of this spec.** Surface it as Open Issue #1 below for Aaron's decision. If Aaron approves, the extension is ~15 lines added to `apply_directive_styles()` and is a separate patch.

### 4.3 `glossary-term` → **Choice A (treat as paragraph style)**

In SoP HTML, glossaries are rendered as `kv-table` rows, so each term/definition pair has its own row. In markdown source, a definition-list construct typically renders term and definition as separate paragraphs already. Therefore `glossary-term` can be a paragraph style without lying about the use pattern.

Visual treatment: Source Sans 3 **bold**, body color, body size, no bg, no border, 6pt space-before (gap above each new term), 2pt space-after (tight against its definition below). The bold term + indented definition pair reproduces the printed-reference-book register that fits a clinical SoP glossary.

---

## 5. Cross-cutting decisions

What this vocabulary asserts about the brand on the docx surface, taken as a system:

- **Cena's functional palette is the severity register; the warm-neutral palette is the structural register.** Callouts use functional families (cyan/amber/green/red) for severity ramping. Containers (card, attestation, decision-branch) use the sand family for structure. Escalation sits at the boundary — sand-50 bg with an amber-700 rule — because it's structurally a routing rule that carries safety weight. This split lets the reader learn the visual grammar within one SoP: "amber rule = pay attention; sand rule = this is a block."
- **The left rule is the directive's signature.** Every block-directive that isn't a card carries a single left rule. This unifies the family visually (they all "rhyme" with the existing blockquote treatment) and survives Google Docs round-trip cleanly. No directive uses right/top/bottom borders because python-docx can express them but Google Docs renders them inconsistently across exports — a known fragility I'm avoiding by sticking to one-sided.
- **No directive overrides the body type stack except `card-title` and `attestation`.** The brand's Lora-commands-Source-Sans-works formula stays intact; directives earn delta through bg, border, or weight, not by introducing new typography. `card-title` uses Lora because it labels a container; `attestation` uses Lora because it carries the highest brand weight. Everywhere else, Source Sans 3 is the working voice. **The same Lora-commands · Source-Sans-works · teal-punctuates discipline extends to the haven *chrome* family** — `surface-banner` carries Lora in its H1 title with Source Sans 3 framing the eyebrow and the read-only-surface caveat; `surface-nav` carries Source Sans 3 at link-row register; `surface-footer` carries Source Sans 3 small in the copyright with the brand mark mirroring banner; `rail-foot-keyboard-cue` carries Source Code Pro for the `⌘ K` glyph with Source Sans 3 framing "to search." Full chrome-family spec: [haven-surface-chrome-styling.md](haven-surface-chrome-styling.md).
- **No directive uses primary teal (`#1e5149`) as a bg or border on a block-level callout.** Primary teal is reserved for interactive elements + section labels in the brand system (per `feedback_no_haven_ui_react_conflation`-adjacent discipline observed in the brief's retro log: agent reaches for primary teal by default; surface the discipline). Using it on a callout would mis-cue the callout as interactive. Teal-700 appears only in `card-title` (where it's the section-label register, matching H3) and inline in `screen-ref` (where it's the interactive/wayfinding register).
- **Restraint is enforced by per-document discipline, not by the styling.** Every directive in this spec *can* be visually loud if used 8 times on a single page; the spec cannot prevent that. The SoP authoring guide should carry the per-SoP-instance caps (1 error, 1 escalation, ≤3 decision-branches, ≤2 callouts of any one severity). If those caps are needed in code, they belong in the `surface-emit` directive handlers as a lint, not here.

---

## 6. Open issues / future work

1. **Character-style extension for `screen-ref`.** Recommended path: extend `apply_directive_styles()` to handle `WD_STYLE_TYPE.CHARACTER` when a directive declares `"style_type": "character"`. Treatment per section 4.2. Decision point for Aaron: is screen-ref worth the extension cost, or do we defer to a future iteration after Marrero's first review surfaces volume? My read: **yes, worth it** — screen-ref appears 4+ times in every SoP, and degrading it to bold/inline-code loses the wayfinding affordance that's load-bearing for SoP navigation. But this is Aaron's call.

2. **The `bold: true` knob on `attestation` is not currently exposed in the dict.** Reviewing the `apply_directive_styles()` code (line 271-272), `bold` is supported per-directive. I have not used it in any treatment — the visual weight comes from bg + border + Lora + space. If Marrero's first review reveals attestation reads as under-weighted vs. the rest of the document, the cheapest fix is to add `"bold": True` to its dict entry. Flagging as a tuning knob, not a current problem.

3. **Decision-branch row formatting is a markdown-source concern, not a styling concern.** Section 3.11 notes that each row's condition/outcome pair must be authored in markdown as a single paragraph (e.g., `**A participant requests an appointment** — You receive an email in your queue.`). The directive handler in `surface-emit/handlers.mjs` may or may not produce this shape automatically. Worth validating against the SoP markdown source before regeneration.

4. **Tables inside cards inherit the table style, not the card body shading.** Section 3.7 notes this. Google Docs renders this as: card-title with sand-50 bg → kv-table with white-bg cells and sand-200 borders → no visual connection between title and table. This is acceptable degradation for v1; if Marrero reads it as disjointed, the fix is to either (a) drop the card-body shading entirely so the title and table read as "label above table" with no false-container expectation, or (b) extend the theme to shade table cells to sand-50 too (table styling is a separate function — would need its own pass).

5. **The 240-twip indent on `glossary-def` matches the blockquote indent.** This means a glossary definition and a blockquote will visually align at the same left edge. That's accidental coherence (both happen to use the same indent value because both are inheritable from the same border-indent pattern) and acceptable — both are "slightly-inset content" registers. Worth confirming on render that the alignment reads as intentional rather than as a markup error.

6. **The placeholder `alert-warning` entry at lines 394–401 of `generate-references.py` was authored before this spec.** Aaron applies the drop-in block in section 7 below in its place. The visual contrast between the placeholder (`#a8420c` / `#fef3e2` — high-saturation diagnostic amber) and this spec's `alert-warning` (`#aa8232` border / `#f8f1e3` bg — restrained brand amber) is what the swap will be visually obvious on. If Marrero's first review opens against the placeholder amber rather than the spec's restrained amber, the swap has not landed and the wrong build was distributed.

---

## 7. Drop-in code block

Replace lines 394–401 of `/Users/aaronsleeper/Vaults/.claude/config/drive-themes/generate-references.py` with the following. After replacement, Aaron regenerates `reference-cena.docx` and validates in Drive against an SoP markdown source.

```python
    # Haven directive styles — Cena Color System v2 canon, restrained per
    # Principle 6 (restraint as default). Severity ramp uses functional palette
    # (cyan/amber/green/red); structural blocks use sand. Single left rule is
    # the family signature; no right/top/bottom borders (graceful degradation
    # for Google Docs round-trip). Spec: Lab/cena-health-brand/specs/
    # haven-directive-styling.md.
    "directive_styles": {
        # --- Callouts: severity ramp on the functional palette ---
        "alert-info": {
            # cyan-500 border on cyan-50 bg — informational register, lowest severity
            "border_color": "#236b8f",  # cyan-500
            "bg_color":     "#d2f2fa",  # cyan-50
            "space_before": Pt(8),
            "space_after":  Pt(8),
        },
        "alert-warning": {
            # amber-500 border on amber-50 bg — pay attention, brand-restrained
            # (replaces the diagnostic placeholder #a8420c / #fef3e2 from the
            # 2026-05-28 round-trip test)
            "border_color": "#aa8232",  # amber-500
            "bg_color":     "#f8f1e3",  # amber-50
            "space_before": Pt(8),
            "space_after":  Pt(8),
        },
        "alert-success": {
            # green-500 border on green-50 bg — desaturated success per Cena
            # functional palette (distinct from sage, which is brand-identity)
            "border_color": "#2c845c",  # green-500
            "bg_color":     "#e5f7eb",  # green-50
            "space_before": Pt(8),
            "space_after":  Pt(8),
        },
        "alert-error": {
            # red-500 border on red-50 bg — critical risk; warm-shifted red,
            # not candy-red. Distinct from escalation (which is "what to do
            # when something already broke")
            "border_color": "#c13c3b",  # red-500
            "bg_color":     "#ffedea",  # red-50
            "space_before": Pt(8),
            "space_after":  Pt(8),
        },

        # --- Card family: structural container, no severity register ---
        "card": {
            # sand-50 bg, no border — quietest container, "this paragraph
            # belongs to a grouping". Below the body's warm ground in saturation
            # so it reads as the document being slightly more itself.
            "bg_color":     "#fbfaf8",  # sand-50
            "space_before": Pt(8),
            "space_after":  Pt(8),
        },
        "card-title": {
            # Lora bold + teal-700 — matches H3 section-label register, slightly
            # uplifted from body. Sand-50 bg inherits the card body it labels.
            "font":         "Lora",
            "size":         Pt(12),
            "color":        hex_to_rgb("#1e5149"),  # teal-700
            "bold":         True,
            "bg_color":     "#fbfaf8",  # sand-50 — match card body
            "space_before": Pt(4),
            "space_after":  Pt(2),
        },
        "card-body": {
            # Body type, body color, sand-50 bg. Inherits everything from Normal
            # except the shading that ties it visually to its card-title.
            "bg_color":     "#fbfaf8",  # sand-50 — match card-title
            "space_before": Pt(4),
            "space_after":  Pt(8),
        },

        # --- Process-critical: SoP-load-bearing structural blocks ---
        "attestation-block": {
            # Highest brand weight. Lora type + sand-100 bg (warmer than card)
            # + sand-300 border (matches theme border_color). The block where
            # the SoP becomes operational.
            "font":         "Lora",
            "size":         Pt(11),
            "border_color": "#bcb8b1",  # sand-300 (theme border_color)
            "bg_color":     "#e6e4e0",  # sand-100
            "space_before": Pt(12),
            "space_after":  Pt(8),
        },
        "attestation-gate": {
            # Individual gate line within an attestation block (RD/admin
            # multi-gate pattern). Tight stacking, inherits sand-100 bg from
            # the parent attestation block so they sit in the same container.
            "bg_color":     "#e6e4e0",  # sand-100 — match attestation parent
            "space_before": Pt(2),
            "space_after":  Pt(2),
        },
        "escalation": {
            # Routing rule with safety weight — between warning and error.
            # amber-700 rule (darker than alert-warning's amber-500) on
            # sand-50 bg (warm ground). "Infrastructure providing the route."
            "border_color": "#735311",  # amber-700
            "bg_color":     "#fbfaf8",  # sand-50
            "space_before": Pt(8),
            "space_after":  Pt(8),
        },
        "decision-branch": {
            # Structural block — "if A then X; if B then Y". Neutral sand-300
            # border (matches the theme's table/blockquote rule color) signals
            # "logical block" without coloring it as a severity callout.
            "border_color": "#bcb8b1",  # sand-300 (theme border_color)
            "bg_color":     "#fbfaf8",  # sand-50
            "space_before": Pt(8),
            "space_after":  Pt(8),
        },

        # --- Glossary: definition-list register ---
        "glossary-term": {
            # The term being defined — bold, body color, body size, slight
            # gap above to separate term/def pairs from each other.
            "bold":         True,
            "space_before": Pt(6),
            "space_after":  Pt(2),
        },
        "glossary-def": {
            # The definition — indented to its term, no chrome. Reads as a
            # printed-reference-book entry. Indent is applied via the same
            # 240-twip mechanism the blockquote uses, but no border.
            # NOTE: paragraph-style indent without a border is not currently
            # supported by apply_directive_styles() — see Open Issue #5 in
            # spec. For v1, ship without the indent (the bold term carries
            # the visual hierarchy); add indent support if needed in a
            # later iteration.
            "space_before": Pt(2),
            "space_after":  Pt(6),
        },

        # --- Inline directives (deferred — see spec §4) ---
        # badge: rendered as **inline bold** in markdown source (Choice C)
        # screen-ref: needs character-style extension (Choice B, Open Issue #1)
        # glossary-term: handled above as paragraph style (Choice A)
    },
```

**Note for Aaron on the `glossary-def` indent:** I wrote the dict without the 240-twip left indent because `apply_directive_styles()` currently only adds indent *when a border_color is present* (line 295-300 — the indent is part of the border-clearing pattern). Adding an indent-only path is a 4-line change (lift the `ind` XML block out of the `if "border_color" in props:` block and gate it on a separate `"indent" in props` key instead). I'm flagging this as Open Issue #5 rather than implementing — the bold-term-without-indent treatment still reads correctly as a glossary, just less printed-book-formal. Aaron's call whether to extend the function.

---

## 8. Validation checklist (for Aaron's regenerate-and-review pass)

After replacing the dict and running `python3 generate-references.py`:

- [ ] `reference-cena.docx` exists and is non-zero bytes
- [ ] Open in Microsoft Word or Pages — confirm all 13 directive styles appear in the Styles panel
- [ ] Render a test markdown (any SoP source with all directive types) via pandoc → docx
- [ ] Upload to Google Drive, open in Google Docs
- [ ] Visually confirm each directive's treatment matches its section above:
  - [ ] alert-warning: amber-on-cream paragraph rectangle with amber left rule (NOT the diagnostic-bright placeholder amber)
  - [ ] alert-info, success, error: matching severity-ramp treatment per their families
  - [ ] card / card-title / card-body: subtle warm bg, no left rule, Lora title in teal-700
  - [ ] attestation: warmer sand-100 bg, sand-300 left rule, Lora type
  - [ ] escalation: amber-700 left rule on sand-50 bg (NOT same as alert-warning)
  - [ ] decision-branch: sand-300 left rule on sand-50 bg (neutral, structural)
  - [ ] glossary-term: bold term followed by definition; reads as definition list
- [ ] Quiet-mode check: scroll through the rendered SoP with eyes unfocused. The page should still feel like Haven (warm ground, Lora headings, Source Sans body). Directives should read as *moments*, not as a tiled pattern.
- [ ] Round-trip test: edit one directive paragraph in Google Docs (add a sentence), download as docx, run `pandoc --track-changes=all` back to markdown. Confirm the directive structure survives (fenced div with class still present).
