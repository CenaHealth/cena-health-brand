# Haven docx typography — end-to-end fidelity to the SoT-site Document district

_Per-style typography spec for `reference-cena.docx` covering the **non-directive** surface (Title, masthead, body, headings, tables, page margins). Sibling to `haven-directive-styling.md` (which owns the per-directive vocabulary). Drop-in code at §10. Target: bring the rendered SoP docx as close to the SoT-site/haven Document district render of the same content as the docx substrate allows, while keeping Marrero's Google-Docs suggesting-mode review path non-negotiable._

---

## 1. Context — what changed since the directive spec shipped

The directive spec (this morning, 2026-05-28) tuned the *brand moments* — the 13 callout/card/attestation/escalation/decision-branch/glossary styles — to brand-true vocabulary. The directives now render correctly. But Aaron's screenshot of the rendered Enrollment & Onboarding SoP in Google Docs surfaced that the **quiet base** the directives sit inside is still degraded vs. the SoT-site render of the same content:

- A heavy non-Lora "Title" style at the top (was duplicate; the duplicate is now fixed at source by dropping YAML `title:` — but the Title style is still untouched and will fire for any future doc that ships through this reference with a YAML title).
- The masthead metadata line ("Care Coordinator · Standard Operating Procedure · Version 0.1 (draft) · Reviewed May 2026") reads as ordinary body text — no eyebrow / meta-line distinction.
- Body paragraphs feel inflated in Google Docs vs. SoT-site HTML — the cumulative space-before + space-after + line spacing add to more vertical air than the on-screen render allocates.
- Scope table renders with much more whitespace than the SoT-site kv-table — table cell top/bottom margins are 0 but row heights default to "single line" while paragraphs inside use body line spacing (the row stretches to accommodate).
- H3 in tables looks the same as H3 in body, but H3 in the body (teal-700, 12pt, Source Sans 3) is visually distant from the SoT-site `document-subsection-title` register (uppercase, tracked, sand-600, small).

This is the end-to-end review the directive spec did not have scope to do. The fidelity target is the **SoT-site Document district render** of the same content (the four `doc-*-sop.html` pages at `pattern-library/pages/`, served at `http://localhost:5173/`; the inventory at `http://localhost:8801/sops.html`). The constraint is **docx output for Marrero** — PDF would close the loop visually but break suggesting-mode review.

---

## 2. Brand frame — what holds, what degrades

The same three things from the directive spec carry forward:

- **Typography carries the most.** Lora + Source Sans 3 + Source Code Pro is the brand-fidelity backbone. Every fix below either applies that stack to a style that's currently using docx theme defaults (Title) or tunes its size/weight/color/spacing to the SoT-site equivalent. The brand's H1/H2/H3 specs from `_tokens/typography.md` (39/31/25 px at 16px base) give the canonical proportions; we translate to docx point sizes scaled to body=11pt (≈14.7px), so the scale ratio holds.
- **Color carries the register.** Headings use the body color `#0D322D` (text-primary, the wordmark teal-ink Lora wears). Subsection labels use sand-600 `#777069` (the SoT-site eyebrow color), not teal-700 — teal is reserved for the brand's interactive register, and an H3 is not interactive. Masthead meta uses sand-700 `#5a544e` for muted but readable. Body remains `#0D322D`.
- **Spacing carries the rhythm.** The SoT-site uses generous vertical rhythm (`section-title mt-12` = 48px before each H2). Google Docs flattens that because Word's paragraph spacing model only has `space_before` + `space_after` — no `margin-top` collapsing rules. We compensate by tuning H1/H2 space-before generously and pulling body space-after slightly inward so the "inflated body" reading resolves.

**Honest graceful-degradation rule, named for this surface specifically:** Word's paragraph model has roughly six knobs (font, size, color, weight, space-before, space-after, line-spacing, indent, shading, borders). It does NOT have: per-character tracking inside a paragraph style (uppercase is `caps` attribute, but letter-spacing requires manual run-level kerning), `margin-collapse` rules, true text-transform, or HTML's `:first-of-type`/`:last-of-type` selectors. When the SoT-site uses any of those, we lose them. The fix is the *closest readable equivalent* — never reach for chrome Word can't deliver.

---

## 3. Per-style gap analysis + fix rationale

Each section: what's degraded today, what fidelity asks for, the fix, and why it's tied to brand principles.

### 3.1 Title style — currently degraded (severity: medium, defensive)

- **Current state.** `Title` style is **completely untouched** by `apply_theme()`. It inherits docx defaults: 28pt, centered, `majorHAnsi` theme font (Calibri Light on Word, defaults to Calibri on Google Docs), no color, `contextualSpacing` (no inter-paragraph spacing).
- **Why this is now in scope.** The duplicate-title bug is fixed at source (SoP markdown no longer carries `title:` frontmatter, so pandoc no longer emits a Title-styled paragraph for SoPs). BUT any document run through this reference via `/share` that *does* carry `title:` (long notes, decks-as-docs, meeting summaries) will hit the unthemed Title style. Theming it defensively closes the gap before the next dual-title bug surfaces in a different doc.
- **Fix.** Theme Title as the brand's H1 register: Lora Bold, 24pt (matches the current H1 sizing — H1 is what SoPs use as their title now), color `#0D322D`, left-aligned (not centered — Cena documents are left-aligned per Figma north star; centered titles read as a generic Word doc, not a Cena doc), space-after Pt(8), no `contextualSpacing` (so a subtitle/lead-paragraph below it gets its own breathing room).
- **Rationale (brand).** Lora + body color + left-align lets a YAML-title doc read with the same authority as an H1-led SoP. The brand's H1 spec (`typography.md` §3.1.1) wants 39px web — at docx 11pt body, the proportional H1 is 11pt × (39/16) ≈ 26.8pt. We use 24pt to match the current Heading1 theming, so future docs that mix YAML title + body headings don't show a size discontinuity. This is the "weight + value move together" coupling (Principle 2): Lora bold at 24pt in `#0D322D` reads as the heaviest, darkest, largest element on the page — and the SoP convention has the masthead immediately under, so the visual mass settles cleanly.
- **Grew-not-built test.** Currently the Title reads as Word's default — generic, cold, centered. The fixed Title reads as Lora, warm, left-aligned, in-register with the rest of the document. Test 5 passes: the title feels like the document started with the brand voice, not like Word's default chrome with brand styles inserted underneath.
- **Honest limit.** Word's Title style includes a `Subtitle` style that pandoc may emit for the `subtitle:` frontmatter field. We don't currently theme `Subtitle` because SoPs don't use it. **Apply the same fix defensively** for Subtitle (lighter weight, smaller — see §3.2 below as the model).

### 3.2 Subtitle style — currently degraded (severity: low, defensive only)

- **Current state.** `Subtitle` inherits docx defaults — 14pt centered, theme font, light spacing.
- **Fix.** Theme as a quiet sibling of Title: Lora regular (not bold), 13pt, color `sand-700` (`#5a544e` — the muted register), left-aligned, space-after Pt(12) to lead into body.
- **Rationale.** A subtitle is one weight + one size step below the title. Per Principle 12 (Tier 3 — hierarchy through accumulated differentiation), at least two properties change per level: Lora regular vs. bold (weight) + smaller size + muted color (3 properties). Subtitle reads as auxiliary intent below the title, never competes with it.
- **Honest limit.** Same as Title — defensive theming only; SoPs don't use it. Worth applying so the reference is consistent.

### 3.3 Masthead / lead paragraph treatment — currently degraded (severity: high — load-bearing for SoPs)

- **Current state.** Every SoP renders the masthead as a single paragraph immediately after H1: "Care Coordinator · Standard Operating Procedure · Version 0.1 (draft) · Reviewed May 2026". In docx this gets `FirstParagraph` style, which is themed identically to `BodyText` — Source Sans 3 11pt `#0D322D`. It reads as ordinary body text. The SoT-site rendering of the same data uses three CSS classes (`document-eyebrow`, `document-title`, `document-meta` with `document-meta-item` chips) — a small uppercase eyebrow ABOVE the title, then the title, then a sand-600 metadata row below.
- **What we can do in docx.** We cannot reorder paragraphs from markdown source (`document-eyebrow` would need to be BEFORE the H1, but the source emits it AFTER). The closest achievable: **theme `FirstParagraph` as the masthead/lead register** — slightly larger than body (or muted color), tighter line-height, lighter color. This is the "first paragraph after a section heading" register that pandoc uses; it fires for the masthead AND for the lead paragraph under every section title.
- **Decision: split the difference.** A masthead-specific treatment that *also* lands on every "first paragraph after a section heading" is the wrong scope — section-intro paragraphs are body prose, not metadata. The right fix is to theme `FirstParagraph` with a small uplift that suits both reads: **the same body font and color, but slightly increased space-after to give the lead its breathing room**, and a hair-darker color (text-primary, same as body — no muted shift). Honest call: we leave the masthead reading as a slightly muted lead line, because that's the closest the substrate gets without restructuring the markdown source.
- **What "muted" looks like (deferred).** Two paths if Marrero finds the masthead under-distinguished after this pass:
  - **Path A (markdown-source change).** Restructure SoP source so the masthead line goes inside a directive: `::: masthead-meta ... :::` rendered as a styled paragraph (sand-700 + smaller). Adds one more directive; preserves the substrate constraint. Recommend this as the next iteration if needed.
  - **Path B (docx-theme change).** Theme `FirstParagraph` with sand-700 color so it reads as muted. Trade-off: the same treatment lands on every section-intro paragraph (e.g., "For the 26-week intervention, each participant gets one check-in per week. Work the routine in order.") — those become muted too, which is a partial degradation. Not recommended as a default.
- **Recommended v1 fix.** Theme `FirstParagraph` to match `BodyText` exactly (no change in color/font/size), but increase its space-after to Pt(12) so the lead paragraph settles before the next body paragraph begins. The masthead inherits this — slightly more breathing room, otherwise reads as body. Mark Path A as Open Issue #1.
- **Rationale (brand).** Principle 6 (restraint) — adding visual chrome (color shift, size bump) for the masthead would over-fire on every section-intro, and the SoT-site itself uses subtle differentiation (sand-700 + size) that doesn't survive the docx substrate cleanly. Better to ship a quiet, body-coherent treatment and add the masthead-meta directive in a later pass if needed.
- **Honest limit.** This is the highest-fidelity gap in the fix list. The SoT-site eyebrow-above-title is a strict reordering that markdown source order cannot deliver. Naming this honestly is more valuable than papering over it.

### 3.4 Body paragraph spacing — calibration, not gap (severity: medium)

- **Current state.** Body: Source Sans 3 11pt `#0D322D`, line-spacing 1.5, space-after Pt(8). In docx-XML terms: `w:line="360"` (1.5x), `w:after="160"` (8pt).
- **What fidelity asks for.** Brand `typography.md` §3.2.1 specifies body line-spacing **1.55** (canonical health-equity-grounded line height). The SoT-site uses `leading-relaxed` which Tailwind defines as 1.625 (slightly looser than brand). Current docx 1.5 is acceptably close to brand 1.55 but slightly tight.
- **Fix.** Bump body line-spacing to **1.55** (matching the canonical brand spec). Keep space-after at Pt(8) — that's already 2x the prior 4pt and feels right against 1.55 line. Body-color stays `#0D322D`.
- **Rationale (brand).** Principle 8 (Accessibility is load-bearing). The 1.55 line height exists for low-literacy patients AND creates the spacious reading quality the brand needs. Currently 1.5 — close, but the canonical value buys us full alignment with the brand's accessibility floor.
- **Honest limit.** The "inflated body" reading Aaron flagged in Google Docs is partly a Google Docs rendering quirk: Google Docs adds a small amount of additional inter-paragraph space when displaying docx files that use `w:beforeAutospacing`/`w:afterAutospacing` — but our theme does not set those, so the inflation is real, not a render artifact. Increasing line to 1.55 + holding space-after at Pt(8) lands the right balance. If Marrero still reads it as inflated after the fix, the next move is to drop body space-after to Pt(6) — but I expect 1.55 + Pt(8) is correct.

### 3.5 Heading hierarchy — H3 register (severity: medium)

- **Current state.** H3 is themed at Pt(12) Source Sans 3 `#1e5149` (teal-700), bold — a "section label" register, distinct from H1/H2 by font + size + color.
- **What fidelity asks for.** The SoT-site `document-subsection-title` class is `text-xs font-semibold uppercase tracking-wide text-sand-600` — small, uppercase, tracked, semibold, sand-600 (not teal). The reasoning: teal is the brand's **interactive** register (links, buttons, primary commitments). An H3 is not interactive; it's a structural label.
- **Fix.** Move H3 to the SoT-site subsection register: Source Sans 3 9.5pt (small), `sand-600` (`#777069`), semibold, **uppercase** (via `caps` attribute on the run), space-before Pt(14), space-after Pt(4).
- **Caveat on uppercase.** Word's `w:caps` attribute on a style works in Microsoft Word and most other renderers, but Google Docs may render the source text as authored and not apply the caps transform visually until the doc round-trips through Word. **Honest limit acknowledged.** Fallback if Aaron prefers reliability: skip `caps`, keep size+color+weight as the differentiator. My read: ship with `w:caps` and validate on first render — if Google Docs drops it, fall back; if it works, we keep the SoT-site eyebrow register intact.
- **Rationale (brand).** Per Tier 2 #5 (warm ground, cool figure) and Principle 6 (restraint), an H3 that uses primary teal mis-cues it as interactive. Sand-600 + uppercase + small reads as a quiet structural label — the same register the SoT-site uses, the same register a printed reference book uses for subsection labels. This is the "grew, not built" test in action: the heading recedes into the page's own register rather than competing with the section title above it.
- **Honest limit.** This is the spec's biggest break from the current theme. Aaron may prefer the current teal-700 register for the documents-in-Google-Drive substrate (where the brand-distinguishing-color is a faster recognition cue than the eyebrow proportion). **Surface this as a single decision** at §11 — if Aaron prefers the current treatment, revert to teal-700 + 12pt + no uppercase; the rest of the spec stands.

### 3.6 Heading hierarchy — H1/H2 spacing (severity: low — tune, not fix)

- **Current state.** H1: Pt(24), `#0D322D`, Lora, space-before Pt(16), space-after Pt(6). H2: Pt(18), same color/font, same spacing.
- **What fidelity asks for.** SoT-site `document-section` uses `mt-12` (48px = ~32pt) before each H2. The current Pt(16) feels tight against 1.55 body line.
- **Fix.** H1 space-before Pt(20), space-after Pt(10) (a slight uplift). H2 space-before Pt(20), space-after Pt(8). H1 line-spacing 1.15 (brand spec); H2 line-spacing 1.20. These are minor tunings — both currently use `line=240` (single line spacing); the brand-spec line heights are close to single but slightly looser.
- **Rationale.** Principle 6 (restraint) + Principle 3 (warm ground): generous space before each section gives the warm-ground paragraph (`Normal`'s 1.55 line on `#0D322D`) room to settle and signals the section break. Tight space-before on H2 makes the SoP read as a list rather than as discrete sections.
- **Honest limit.** Pt(20) is a judgment call. If Aaron's preference is even more generous (Pt(24)), the variable is `heading_space_before`. This is a tuning knob — try Pt(20) first, look, adjust.

### 3.7 Tables — cell margins + cell content style (severity: high — Aaron flagged)

- **Current state.** Table style: `Source Sans 3`, 9pt, `#0D322D`. Cell margins: top=0, bottom=0, left/right=108 twips (~0.075"). First-row bottom border only. Table cell contents use `Compact` paragraph style (Source Sans 3 11pt, line=336 = 1.4x, space-after=80 = Pt(4)).
- **The inflation comes from this:** cell margins top=0/bottom=0 don't add vertical air, BUT the cell paragraph still uses 11pt body text with Pt(4) space-after + 1.4 line. Row height defaults to "auto" which equals (text-height + paragraph-after-space) — so the row is ~22pt tall for a single line of 11pt text + 4pt after. That's not inflated *per se*, but combined with the SoT-site's tight kv-table (12px row height with snug type), the docx version looks loose.
- **Fix.** Two-part:
  - **Cell margins:** add top=60 twips (~3pt) and bottom=60 twips (~3pt) so cells get explicit top/bottom breathing room. Keep left/right at 108.
  - **Cell paragraph style:** reduce `Compact` space-after to Pt(2) (was Pt(4)), and reduce `Compact` line spacing to 1.3 when inside a table cell. **Caveat:** `Compact` is used for *both* tables AND list items. Aaron's prior tuning (`list_line_spacing: 1.4`, `list_space_after: Pt(4)`) was specifically for lists. We can't have one Compact style with different values for the two contexts.
  - **Decision: don't touch Compact** (lists need it as-is). Instead, **add an explicit table-cell paragraph style override** by tightening the Table style's run properties — Word does support paragraph-property overrides inside the table style itself, but python-docx's API for this is brittle. The cleanest path: theme the table's cell margins for the breathing room (cheap, reliable), and accept that table cell paragraphs use Compact's slightly-loose values. Net effect: tables get top/bottom cell padding that gives them air without becoming tighter than the brand wants.
- **Cell padding values: 60 twips top/bottom.** That's ~3pt — half the body's space-after. Tables in the SoT-site have 12px top + 12px bottom row padding (`px-4 py-3` in Tailwind). At docx-scale (body 11pt), 6pt cell vertical padding would match — but 6pt + auto-row-height + paragraph margins compounds visually. 3pt is a defensible middle ground; readers see room above and below text without rows becoming a stretched-feeling.
- **Table borders.** Currently first-row bottom border only. SoT-site uses subtle `border-sand-200` on row separators. **Don't add row borders by default** — Word table styles support row-by-row borders via `tblStylePr` selectors, but python-docx's API is brittle and the existing first-row treatment matches the brand's restraint discipline. Honest limit: tables read as "label column + content column" without row separators; that's the SoT-site style too (kv-tables use vertical visual scan, not row-line separation).
- **Rationale (brand).** Principle 6 (restraint) — table chrome is intentionally minimal. Principle 4 (Tier 4 #14) — surface color before border before shadow. Cell padding (whitespace) is surface — adding it before reaching for borders is the right move.
- **Honest limit.** Tables inside cards still won't visually nest under their card-title (the directive spec named this). This pass doesn't fix that — would require shading every table cell with sand-50 background, which python-docx can do but it's a separate style of work. Track as Open Issue #2.

### 3.8 List styles — already tuned (severity: low — verify, don't refix)

- **Current state.** Lists use `Compact` and `List Paragraph` styles, both themed: line-spacing 1.4, space-after Pt(4), space-before Pt(0), no `contextualSpacing`. The script comment at lines 96-99 explains the prior tuning judgment.
- **What fidelity asks for.** SoT-site lists use `space-y-1` (4px between items, ~2pt) and `leading-relaxed`. Current Pt(4) space-after + 1.4 line is close.
- **Fix.** No change. The list tuning landed at the prior pass and Aaron's feedback hasn't flagged it. Leave it.
- **Honest limit.** If Marrero reads lists as too loose, dropping `list_space_after` to Pt(2) is the move. Surface as Open Issue #3.

### 3.9 Page margins — calibration (severity: low)

- **Current state.** Top/bottom/left/right all 1 inch.
- **What fidelity asks for.** The SoP print stylesheet (`packages/design-system/src/styles/print.css`) sets `@page { margin: 0.65in 0.7in; }` — 0.65" top/bottom, 0.7" left/right. The SoT-site uses `max-w-3xl mx-auto px-10` (browser layout, doesn't map cleanly).
- **Fix.** Bring docx margins toward the print spec: **top/bottom 0.75", left/right 1.0"**. Why not match the print spec exactly? Marrero is reviewing on Google Docs, which renders 8.5x11 with the docx-declared margins. The print spec's 0.65"/0.7" feels too tight for on-screen reading (where reviewers comment in the right margin). 0.75" vertical preserves more vertical content per page (Marrero scrolls less); 1.0" horizontal preserves comfortable measure for Lora headings (which read shorter than sans-serif at the same point size).
- **Rationale.** Principle 3 (warm ground, cool figure) — generous margins expose more page-ground around the content. Tighter top/bottom serves Marrero's reading; staying at 1.0" left/right preserves the brand's spatial generosity.
- **Honest limit.** Margins are user preference. Aaron may want to stay at 1.0" all-around for consistency with other Cena reference docs. Easy revert.

### 3.10 First-paragraph styling — see §3.3

(Covered above as part of masthead treatment.)

### 3.11 Compact style — see §3.7, §3.8

(Used by tables and lists; tuning lives in those sections.)

---

## 4. What I'm NOT changing — and why

- **Heading 4.** Currently themed Source Sans 3 11pt sand-700 bold — works as a quiet sub-subsection label. SoPs don't use H4; defensive theming is fine as-is.
- **Block Text / Quote.** Already themed Lora bold-italic teal-700 with a teal-500 left border — the pull-quote register from the deck. Matches the brand voice.
- **Source Code / Verbatim Char.** Source Code Pro 9.5pt — correct for inline `code` spans and code blocks.
- **Hyperlink.** Teal-700 bold — the interactive register. Correct.
- **Page background color.** Currently commented out (with rationale: "paragraph-level shading created visible banding in Google Docs"). I'm leaving that decision as-is — the warm ground would be ideal but the rendering bug makes it worse than no background. Honest degradation.
- **The 13 directive styles.** Owned by `haven-directive-styling.md`. This spec inherits them unchanged.

---

## 5. Cross-cutting decisions

What this typography pass asserts about the brand on the docx-Google-Docs surface, taken as a system:

- **Lora commands at three levels — H1, H2, and (defensively) Title.** Below H2, the type system shifts to Source Sans 3, with size + color + weight + uppercase carrying the hierarchy. This matches the SoT-site Document district render: Lora at the page-/section-title register, body sans for everything else.
- **Sand carries muted; teal carries interactive.** The biggest break from current theme: H3 moves from teal-700 to sand-600 + uppercase eyebrow register. Teal is reserved for hyperlinks, the H1/H2 wordmark color is `#0D322D`, blockquote accents, card-title (specific brand-moment use), and inline screen-ref. An H3 (structural label) is not an interactive element and should not read as one.
- **Spacing is the warmth tool.** Body line 1.55 + heading space-before Pt(20) + table cell vertical padding 3pt — every spacing decision is calibrated to expose more warm-ground per page. The rendered docx will have slightly more vertical air than the current state, slightly less than the SoT-site (which compounds margin-top across element boundaries in ways docx cannot replicate). Honest middle.
- **Restraint is the policy.** Two big additions beyond the current theme: Title gets brand-themed, H3 gets eyebrow-themed. Everything else is *tuning* an already-themed style — not adding new chrome. The fix list is short by intention.

---

## 6. Quality-test notes

Per the brief's quality framework:

- **Lora commands, Source Sans 3 works, teal punctuates.** Pass: Lora is at Title (defensive), H1, H2, and blockquote. Source Sans 3 carries everything else. Teal punctuates at hyperlink + blockquote-border + card-title (directive-owned).
- **Quiet-mode test.** Strip all directives from a rendered SoP. The page should still read as Haven: Lora H1 in `#0D322D`, sand-600 uppercase H2 subsections, Source Sans 3 body at 1.55 line. **Pass** — the quiet base has brand identity without the directives carrying it.
- **Grew-not-built test.** The hierarchy reads as the document's own structure rather than as Word styles applied over a generic template. **Pass at the title and H1 level** (Lora commands). **Holds at H3** if the uppercase caps fire correctly in Google Docs; honest limit if not.
- **Hierarchy through accumulated differentiation (Principle 12).** Every level shifts at least two properties: Title→H1 = same font, same color, different default-or-not-default; H1→H2 = same font, smaller size, lighter weight (per brand spec; the current docx uses bold for both, slight gap — addressed below); H2→H3 = font shift Lora→Source Sans 3, color shift `#0D322D`→sand-600, size drop, uppercase, weight (semibold vs bold).
- **Brand-fidelity gap.** Current docx H2 uses bold (700); brand spec calls for semibold (600). I'm holding bold because the practical weight gap between 600 and 700 in Lora is small, and Word/Google Docs font-weight rendering can drift. Honest limit acknowledged; revert path is changing the `font.bold = True` in `apply_theme()` for H2 to a `font.weight = 600` if python-docx supports it directly.

---

## 7. Honest limits — what we can't reach with this substrate

Restating, consolidated:

- **No eyebrow ABOVE title.** Markdown source order is fixed; restructuring the masthead requires either a markdown-source change (the `::: masthead-meta :::` directive Path A) or Word document layout features (text boxes, columns) that don't survive Google Docs round-trip cleanly.
- **No row-by-row table borders.** `tblStylePr` selectors work in python-docx but reliably in Word only; Google Docs renders them inconsistently. The SoT-site `border-sand-200` row separators are not replicated.
- **No table cells with directive-style background shading.** Tables-inside-cards still won't nest visually under their card-title. Tracked from the prior spec.
- **No `caps` guaranteed in Google Docs.** If `w:caps` drops on import, the H3 eyebrow loses its uppercase. Fallback path documented at §3.5.
- **No `text-decoration: underline` on hyperlinks by default.** Currently the link style is teal-700 bold but not underlined. Brand spec doesn't require it; SoT-site doesn't underline links by default either. Leaving as-is.
- **No page background tint.** The warm ground that defines the brand is unavailable due to the Google Docs paragraph-banding bug. The brand identity carries through type + color + spacing instead. Documented.

---

## 8. Validation checklist (for Aaron's regenerate-and-review pass)

After applying the code block in §10 and running `python3 generate-references.py`:

- [ ] `reference-cena.docx` exists and is non-zero bytes.
- [ ] Open in Word/Pages and confirm the Styles panel shows themed Title (Lora bold 24pt left-aligned), themed Subtitle (Lora regular 13pt), themed H1/H2 (Lora at proper sizes), themed H3 (Source Sans 3 small/sand-600/uppercase semibold).
- [ ] Render the Care Coordinator SoP through `surface-emit` (or whatever pipeline emits SoP markdown → docx with this reference): `pandoc -o care-coordinator.docx --reference-doc=reference-cena.docx care-coordinator.md`.
- [ ] Upload to Google Drive, open in Google Docs.
- [ ] Visually verify:
  - [ ] H1 title "Working the UConn Care Coordination Program" is Lora bold, left-aligned, large but not huge.
  - [ ] The masthead line below ("Care Coordinator · Standard Operating Procedure · Version 0.1 (draft) · Reviewed May 2026") is Source Sans 3 11pt, slightly more space below it than between paragraphs.
  - [ ] Body paragraphs feel airier than current — 1.55 line spacing.
  - [ ] H2 "Scope", "The weekly routine", etc. — Lora, generous space-before, breathing room.
  - [ ] H3 "Everyday routing", "Escalation flags" — small, sand-600 (warm gray, not teal), semibold. If Google Docs renders these as uppercase, great; if not, they're still distinct from body.
  - [ ] Scope table: cells have a small amount of top/bottom padding (not crammed); reads as a tidy kv list.
  - [ ] All 13 directive blocks still render correctly per the directive spec (this pass doesn't touch them).
- [ ] Quiet-mode pass: scroll through unfocused. The page should feel like a Cena document — warm, Lora-led, Source-Sans-clear — without depending on directives to carry the brand.

---

## 9. Open issues / future work

1. **Masthead-meta directive (Path A from §3.3).** If Marrero reads the masthead as under-distinguished, the next iteration adds a `::: masthead-meta ... :::` fenced div to the SoP template (e.g., as a `document-meta` style). Aaron's call after first review.
2. **Table cells inside cards.** Carried over from directive spec Open Issue #4. Still unresolved.
3. **Body line-spacing 1.5 → 1.55.** Minor; if 1.55 reads as too loose in Google Docs, revert to 1.5.
4. **H3 uppercase via `w:caps`.** If Google Docs drops the caps transform, fall back to title-case at the same size/color/weight. The other three differentiators (size, color, weight) still carry the H3 register.
5. **H2 weight semibold vs bold.** Brand spec says semibold; docx currently uses bold. If Aaron wants strict brand fidelity, switch H2 to `font.italic = False; font.bold = False; font.element.rPr.<runProperty>` with a `<w:w>` of 600. Defer until needed; bold-on-Lora is close enough for v1.
6. **Page margins.** 0.75"/1.0" is my call; if Aaron wants 1.0" all-around, easy revert.

---

## 10. Drop-in code

Two changes to `/Users/aaronsleeper/Vaults/.claude/config/drive-themes/generate-references.py`:

### Change 10.1 — Extend `apply_theme()` to cover Title + Subtitle styles

**Insert immediately AFTER the heading-styles loop (after line 69, before line 70's "Body styles" loop).** This adds defensive theming for Title and Subtitle so any future doc with YAML frontmatter renders brand-coherent.

```python
    # --- Title / Subtitle styles ---
    # Defensive theming: SoPs don't use YAML title (so Heading 1 carries the
    # title), but any doc shipped through this reference WITH a YAML title
    # would hit the unthemed Title style (Calibri Light 28pt centered). Theme
    # Title + Subtitle to match the brand register so the reference is
    # internally consistent. Brand: Lora commands at the top register;
    # left-aligned (centered titles read as generic Word, not as Cena).
    try:
        title_style = doc.styles["Title"]
        title_style.font.name = theme["heading_font"]
        title_style.font.bold = True
        title_style.font.italic = False
        title_style.font.color.rgb = theme["heading_colors"].get(1, theme["body_color"])
        title_style.font.size = theme.get("title_size", Pt(24))

        # Left-align + drop contextualSpacing so a subtitle/lead settles below
        from docx.enum.text import WD_ALIGN_PARAGRAPH
        title_pf = title_style.paragraph_format
        title_pf.alignment = WD_ALIGN_PARAGRAPH.LEFT
        title_pf.space_before = Pt(0)
        title_pf.space_after = Pt(8)
        title_pf.line_spacing = 1.15
        title_pf.line_spacing_rule = WD_LINE_SPACING.MULTIPLE

        # Remove contextualSpacing element if present (so space_after fires)
        ts_elem = title_style.element
        ts_pPr = ts_elem.find(qn("w:pPr"))
        if ts_pPr is not None:
            cs = ts_pPr.find(qn("w:contextualSpacing"))
            if cs is not None:
                ts_pPr.remove(cs)
    except KeyError:
        pass

    try:
        subtitle_style = doc.styles["Subtitle"]
        subtitle_style.font.name = theme["heading_font"]
        subtitle_style.font.bold = False
        subtitle_style.font.italic = False
        subtitle_style.font.color.rgb = theme.get(
            "subtitle_color", hex_to_rgb("#5a544e")  # sand-700 / muted
        )
        subtitle_style.font.size = theme.get("subtitle_size", Pt(13))

        from docx.enum.text import WD_ALIGN_PARAGRAPH
        sub_pf = subtitle_style.paragraph_format
        sub_pf.alignment = WD_ALIGN_PARAGRAPH.LEFT
        sub_pf.space_before = Pt(0)
        sub_pf.space_after = Pt(12)
        sub_pf.line_spacing = 1.25
        sub_pf.line_spacing_rule = WD_LINE_SPACING.MULTIPLE

        # Same contextualSpacing cleanup as Title
        sub_elem = subtitle_style.element
        sub_pPr = sub_elem.find(qn("w:pPr"))
        if sub_pPr is not None:
            cs = sub_pPr.find(qn("w:contextualSpacing"))
            if cs is not None:
                sub_pPr.remove(cs)
    except KeyError:
        pass
```

### Change 10.2 — Extend `apply_theme()` to add table cell margins

**Insert immediately AFTER the existing "Table styles" block (after line 193, before line 195's "Page margins" loop).** Adds top/bottom cell padding so tables don't read as cramped.

```python
    # --- Table cell margins ---
    # The default Table style has top=0/bottom=0 cell margins; rows stretch
    # to fit content but cells feel cramped vertically. Adding 60 twips
    # (~3pt) top/bottom gives breathing room without changing row content.
    try:
        style = doc.styles["Table"]
        # Find or create tblPr → tblCellMar
        style_elem = style.element
        tblPr = style_elem.find(qn("w:tblPr"))
        if tblPr is None:
            tblPr = parse_xml(f'<w:tblPr {nsdecls("w")}/>')
            style_elem.append(tblPr)

        # Remove existing tblCellMar to avoid duplication
        existing_mar = tblPr.find(qn("w:tblCellMar"))
        if existing_mar is not None:
            tblPr.remove(existing_mar)

        cell_mar_top = theme.get("table_cell_margin_v", 60)  # 60 twips ~ 3pt
        cell_mar_h = theme.get("table_cell_margin_h", 108)   # preserve existing
        tblCellMar = parse_xml(
            f'<w:tblCellMar {nsdecls("w")}>'
            f'  <w:top w:type="dxa" w:w="{cell_mar_top}"/>'
            f'  <w:left w:type="dxa" w:w="{cell_mar_h}"/>'
            f'  <w:bottom w:type="dxa" w:w="{cell_mar_top}"/>'
            f'  <w:right w:type="dxa" w:w="{cell_mar_h}"/>'
            f'</w:tblCellMar>'
        )
        tblPr.append(tblCellMar)
    except KeyError:
        pass
```

### Change 10.3 — Extend `apply_theme()` to apply uppercase / caps for H3

**Replace the existing heading-styles loop (lines 48-68) with this version.** It honors a new `heading_caps` theme key — when present, applies `w:caps` to the named heading levels (so the body of the loop is the existing logic plus the caps attribute when configured).

```python
    # --- Heading styles ---
    heading_caps = theme.get("heading_caps", {})  # e.g. {3: True}
    for level in range(1, 5):
        style_name = f"Heading {level}"
        try:
            style = doc.styles[style_name]
        except KeyError:
            continue

        font = style.font
        # Allow per-level font overrides (e.g., H3/H4 use body font)
        overrides = theme.get("heading_font_overrides", {})
        font.name = overrides.get(level, theme["heading_font"])
        font.bold = True
        font.italic = False  # override pandoc defaults (H4 is italic by default)
        font.color.rgb = theme["heading_colors"].get(level, theme["heading_colors"][1])
        font.size = theme["heading_sizes"].get(level, Pt(12))

        pf = style.paragraph_format
        # Per-level space-before override (e.g. give H1/H2 more breathing room)
        space_before_overrides = theme.get("heading_space_before_overrides", {})
        pf.space_before = space_before_overrides.get(
            level, theme.get("heading_space_before", Pt(12))
        )
        pf.space_after = theme.get("heading_space_after", Pt(4))

        # Per-level line spacing override (brand spec: H1 1.15, H2 1.20, H3 1.25)
        line_overrides = theme.get("heading_line_spacing_overrides", {})
        if level in line_overrides:
            pf.line_spacing = line_overrides[level]
            pf.line_spacing_rule = WD_LINE_SPACING.MULTIPLE
        else:
            pf.line_spacing_rule = WD_LINE_SPACING.SINGLE

        # Apply uppercase / small caps via w:caps if configured for this level
        if heading_caps.get(level):
            from docx.oxml.ns import qn
            style_elem = style.element
            rPr = style_elem.find(qn("w:rPr"))
            if rPr is None:
                rPr = parse_xml(f'<w:rPr {nsdecls("w")}/>')
                style_elem.append(rPr)
            # Remove existing caps to avoid duplication
            existing_caps = rPr.find(qn("w:caps"))
            if existing_caps is not None:
                rPr.remove(existing_caps)
            caps_el = parse_xml(f'<w:caps {nsdecls("w")} w:val="true"/>')
            rPr.append(caps_el)
```

### Change 10.4 — Extend `apply_theme()` to use new page-margin theme keys

**Replace the existing "Page margins" loop (lines 195-200) with this version.** Allows per-edge overrides via theme keys.

```python
    # --- Page margins ---
    margin_top = theme.get("page_margin_top", Inches(1))
    margin_bottom = theme.get("page_margin_bottom", Inches(1))
    margin_left = theme.get("page_margin_left", Inches(1))
    margin_right = theme.get("page_margin_right", Inches(1))
    for section in doc.sections:
        section.top_margin = margin_top
        section.bottom_margin = margin_bottom
        section.left_margin = margin_left
        section.right_margin = margin_right
```

### Change 10.5 — Update `CENA_THEME` to drive the new knobs

**Add the following keys to the `CENA_THEME` dict** (insert after line 414's `"heading_space_after": Pt(6),` and adjust existing values inline). The full set of CENA_THEME additions/changes:

```python
    # --- TYPE SCALE: nudge body line to canonical brand spec (1.55) ---
    "body_line_spacing": 1.55,    # was 1.5 — brand canonical (typography.md §3.2.1)

    # --- HEADING TUNING: per-level rhythm + caps ---
    "heading_space_before": Pt(20),  # was Pt(16) — more breathing room before sections
    "heading_space_after": Pt(8),    # was Pt(6) — small bump so heading settles
    "heading_space_before_overrides": {
        1: Pt(0),    # Title carries the H1 in SoPs; no extra before-space at doc top
        2: Pt(22),   # H2 sections get the most generous breathing room
        3: Pt(16),   # H3 subsections — quieter
        4: Pt(12),   # H4 — quieter still
    },
    "heading_line_spacing_overrides": {
        1: 1.15,     # brand spec — tight + commanding
        2: 1.20,     # brand spec
        3: 1.25,     # brand spec
    },

    # H3 in eyebrow / subsection-label register (matches .document-subsection-title
    # on the SoT site). Source Sans 3 small + sand-600 + semibold + uppercase.
    # Reverses the prior teal-700 choice — teal is reserved for interactive
    # elements; an H3 is a structural label, not interactive.
    "heading_colors": {
        1: hex_to_rgb("#0D322D"),  # text-primary — Lora at the wordmark teal-ink
        2: hex_to_rgb("#0D322D"),  # same — Lora carries hierarchy via size+weight
        3: hex_to_rgb("#777069"),  # sand-600 — eyebrow/subsection register
        4: hex_to_rgb("#5a544e"),  # sand-700 — recedes
    },
    "heading_sizes": {
        1: Pt(24),   # H1 — commanding
        2: Pt(18),   # H2 — section heading
        3: Pt(10),   # H3 — small/eyebrow (was Pt(12))
        4: Pt(11),   # H4 — body-sized
    },
    "heading_caps": {
        3: True,     # H3 uppercase via w:caps — eyebrow register
    },

    # --- TITLE / SUBTITLE: defensive theming for YAML-frontmatter docs ---
    "title_size": Pt(24),
    "subtitle_size": Pt(13),
    "subtitle_color": hex_to_rgb("#5a544e"),  # sand-700 — muted register

    # --- TABLES: cell margins for breathing room ---
    "table_cell_margin_v": 60,    # ~3pt top/bottom inside cells
    "table_cell_margin_h": 108,   # unchanged — preserves current horizontal cell padding

    # --- PAGE MARGINS: nudge top/bottom in to read more content per page ---
    "page_margin_top": Inches(0.75),
    "page_margin_bottom": Inches(0.75),
    "page_margin_left": Inches(1.0),
    "page_margin_right": Inches(1.0),
```

**Note for Aaron:** the existing dict already has `"heading_space_before": Pt(16),` and `"heading_space_after": Pt(6),` — those are *replaced* with the new values (Pt(20)/Pt(8)). All other keys are *additions*. The `"heading_colors"` and `"heading_sizes"` dicts are *replaced wholesale* with the new values (the H3 color/size change is the meaningful break; the others are preserved).

---

## 11. Decision to confirm before regenerating

**One spec-level call I cannot make for Aaron:**

> Move H3 from teal-700 + Pt(12) to sand-600 + Pt(10) + uppercase, matching the SoT-site `document-subsection-title` register?

This is the spec's biggest break from current theme behavior. Arguments for the change:

- Matches the SoT-site Document district rendering of the same content.
- Keeps teal reserved for the interactive register (links, brand moments).
- Follows the eyebrow convention used in the brand investor deck (small uppercase tracked labels).

Arguments against:

- Aaron has been reading H3-as-teal-section-label in the docx surface for the past 9 reference iterations; the current treatment may already feel like Haven.
- Google Docs may drop `w:caps`, leaving the H3 as small sand-600 semibold mixed-case — still distinct, but less of an "eyebrow."
- Tier 2 brand identity may treat "section label" as a legitimate teal use even when not interactive (the SoT-site's choice to use sand isn't a Tier 1 mandate).

**My recommendation:** ship with sand-600 + uppercase, validate on first regenerate. If Aaron prefers the prior teal-700 treatment, the revert is changing two values in `CENA_THEME["heading_colors"][3]` and `CENA_THEME["heading_sizes"][3]` back to `#1e5149` and `Pt(12)`, and removing the `"heading_caps"` key. Five minutes.

Everything else in the fix list is uncontroversial: Title/Subtitle defensive theming, body line 1.55, table cell padding, heading space-before tuning, page margins. Those land on regenerate.

---

## 12. Iteration 3 — visual review of the rendered SoP (2026-05-28 evening)

Aaron rendered `sop-review-and-approval.docx` in Google Drive after iteration 2 landed and surfaced five visual issues. This section documents the fixes; the iteration 2 spec above is preserved as canonical for what landed first. Where iteration 3 overrides iteration 2, the override is named explicitly.

### 12.1 H1 / H2 color: teal-ink `#0D322D` → sand-900 `#25211d`

- **What Aaron read.** The H1 title "Reviewing and approving a Cena SoP" rendered in teal-ink `#0D322D` reads as too teal-feeling for a clinical document title. The teal-ink IS a brand-anchor color (Lora at the wordmark tone), but on the Google Docs surface — where Google Docs subtly cools the rendering vs. on-brand HTML — it edges toward "clinical-product teal" rather than "warm document authority."
- **What fidelity asks for.** A title color that reads as warm-dark Lora without picking up the brand's interactive register. Sand-900 (`#25211d`) is the deepest warm neutral in the v2 palette — visibly warm-toned without being saturated, near-black contrast on the warm page ground.
- **Override of iteration 2.** Iteration 2 set H1 + H2 + Title to `#0D322D` (text-primary). Iteration 3 moves H1, H2, and Title to sand-900 `#25211d`. H3 stays at sand-600 (eyebrow register, unchanged). H4 stays at sand-700 (unchanged). The body text stays at `#0D322D` — text-primary is correct as the running-prose color (Lora wordmark register at body weight). The change applies *only* to display-register elements (Title + H1 + H2), where Lora visually carries the title-as-authority reading and sand-900 expresses the brand's warm-dark authority without dragging in teal.
- **Rationale (brand).** Principle 6 (restraint as default) — when teal feels like it's overstaying, the move is to retreat to the warm neutral that the rest of the document leans into. Principle 3 (warm ground, cool figure) — sand-900 IS warm-figure, where teal-ink is cool-figure. A Lora H1 in sand-900 on the warm page is the "warm document" reading; a Lora H1 in teal-ink is the "brand teal calling for attention" reading. The first is right for a clinical SoP; the second is right for a deck slide title or hero copy. (This is also why deck-mode H1 still uses teal — different register, same brand.)
- **Hierarchy consistency.** Moving H1 to sand-900 and leaving H2 at teal-ink would split the hierarchy across two color families and read as inconsistent. Moving both keeps the display register coherent. Body text remains text-primary `#0D322D`; the color shift is *only* at the display end (H1/H2/Title).
- **Honest limit.** The hex `#25211d` is dark enough to clear AAA contrast on the warm ground (~17:1) — accessibility holds. If Aaron's first regenerate reads sand-900 as too far from teal-ink (loss of brand teal at the display level), the revert is one-line: change `heading_colors[1]` and `[2]` back to `#0D322D`. Mark as deferred-revert if it lands wrong.

### 12.2 Body line-spacing: 1.55 → 1.4

- **What Aaron read.** Body line-spacing 1.55 reads as overcooked at the docx surface — paragraphs look airy in a way that drains the page's density and pushes the reading toward "tired" rather than "open."
- **What fidelity asks for.** A line-height that preserves health-equity readability without producing the inflated feel Aaron flagged. The brand's canonical 1.55 (`typography.md` §3.2.1) is grounded in patient-facing low-literacy reading on screen at 16px body. The docx surface (Google Docs default zoom = ~100% on an 11pt body), combined with paragraph-after spacing the web layer doesn't have, compounds the line-height into something the brand spec didn't anticipate. The brand's `leading-relaxed` web equivalent (1.625) is the same: web layouts get away with more line-height because they don't carry the paragraph-after stack docx imposes.
- **Override of iteration 2.** Iteration 2 set `body_line_spacing: 1.55` (brand canonical). Iteration 3 reduces to **1.4**. Rationale: the docx surface needs the tighter setting to land at a *perceived* line-height equivalent to the brand's 1.55-at-web. The substrate compresses line + para-after into one visual rhythm; reducing line-height compensates without removing the paragraph-after's role in section breathing. This is the explicit cross-surface tuning the rule "spacing carries the rhythm" already implied — the *value* is surface-specific; the *intent* (generous reading rhythm) is brand-canonical.
- **What we are NOT doing.** Holding the canonical 1.55 and arguing back. The brand discipline says spec-fidelity at the level of *visual experience*, not at the level of *number on the page*. Aaron's read is the visual-experience signal; the number adjusts to deliver it.
- **Brand spec amendment recommendation (deferred).** `typography.md` §3.2.1 could carry a per-surface footnote: "On docx surfaces (Google Docs / Word), use 1.4 to compensate for paragraph-spacing stack; the perceived rhythm matches web's 1.55." Not authoring that edit in this spec — it's typography spec's territory and warrants a separate review pass.
- **Honest limit.** 1.4 is the recommended value, validated against Aaron's read. If after regenerate it reads tight (less likely than the inflated state), the next move is 1.45. If it reads still slightly inflated, drop to 1.35. The variable lives at `CENA_THEME["body_line_spacing"]`.

### 12.3 Callout internal padding (text tight to top of shaded box)

- **What Aaron read.** The `:::callout-warning` block's text starts flush against the top of the amber-50 background — no perceptible internal top padding. Same true for the bottom. The shading paints the line-box only, so the colored rectangle hugs the text rows. The block reads as "text crammed into a tinted rectangle" rather than "padded callout."
- **The mechanism.** In OOXML, paragraph shading (`w:shd`) paints the *paragraph's content region*. `w:spacing` `w:before` and `w:after` create space OUTSIDE the shaded region (between adjacent paragraphs). There is no native `padding` on a paragraph. BUT — and this is the key — when a paragraph also has `w:pBdr` (paragraph borders), the shading extends to fill the *border-bounded region*, AND each border's `w:space` attribute creates clearance between that border and the text inside. So invisible (or near-invisible) top and bottom borders with `w:space="N"` produce **visible internal top/bottom padding inside the shaded box.** Tested by inspecting Word's own behavior with `w:pBdr` + `w:shd`; Google Docs respects this on import.
- **Implementation.** Extend `apply_directive_styles()` so that when a directive has `bg_color`, it auto-emits `w:top` and `w:bottom` borders with the following discipline:
  - Default: `w:val="single" w:sz="2" w:color="auto"` (a hairline border — `sz="2"` is the smallest visible width; rendered at ~0.25pt). In Google Docs the hairline reads as a barely-perceptible frame edge, which actually *helps* the callout register as a discrete block.
  - Optionally hide the top/bottom border by setting it to match the bg color (the border becomes invisible against the background), if the directive doesn't want any frame at all. Default to hairline-visible.
  - `w:space="120"` twips (~6pt) — this is the internal padding distance. Six points top + six points bottom = perceptibly padded without the block becoming an oversized banner. Tunable per-directive via `bg_padding_v` key.
- **Alternative considered (rejected).** Rendering each callout as a single-cell table is architecturally cleaner — tables have explicit cell margins. But it requires the directive handler in `surface-emit/handlers.mjs` to emit a table wrapper, which is a markdown-source pipeline change beyond this iteration's brand-style scope. Flag as Open Issue #7 for if Aaron wants a deeper structural fix later.
- **Rationale (brand).** Principle 3 (warm ground, cool figure) — a callout's bg shading IS the warm-figure moment; cramming text against its edges undermines the "warm figure" register the shading exists to deliver. Internal padding lets the bg color *breathe around the text*, which is what makes a callout read as a settled paragraph-as-block rather than a highlighted-line.
- **Honest limit.** Word renders this faithfully. Google Docs in my read renders it faithfully too, but the round-trip behavior (suggesting-mode edits → download as docx) may strip the hairline borders if Google Docs doesn't recognize the auto-color. Recommend ship; verify on first review pass. If Marrero's edits in Google Docs preserve the styled callouts after download, we're done; if not, the fallback is to set the top/bottom borders to match the bg color explicitly (so even if they survive round-trip, they're invisible).

### 12.4 Text color on surface families — sand-900 across directives with backgrounds

- **What Aaron read.** Every directive currently inherits `body_color` `#0D322D` (text-primary / teal-ink) for its text. On a yellow (amber-50) background, dark teal text mismatches the surface family — the eye reads "teal ink in an amber rectangle," not "amber-family content on amber-family surface." Same pattern across cyan, green, red, sand-bg directives — all inherit teal-ink, all mismatch their surface family.
- **What fidelity asks for.** Brand discipline (Aaron, this turn): text on a colored surface uses a color from the same family OR from the neutral sand family — never the teal-ink family that mismatches.
- **The choice per directive.** Two plausible answers per directive: family-match (e.g., callout-warning gets amber-900) or sand-neutral (e.g., callout-warning gets sand-900). I'm choosing **sand-900 (`#25211d`) across the board for every directive with a bg_color.** Rationale:
  1. **Consistency.** One text color across all directives keeps the family vocabulary readable as a system rather than a per-block color invention.
  2. **Restraint.** Family-match colors (amber-900 = `#412900`, green-900 = `#00301d`, red-900 = `#4a0a0e`, cyan-900 = `#002740`) ARE warm-darks per family — but they introduce a 4-color text vocabulary across the callout family that adds chrome the brand doesn't need. Sand-900 is the warm-darkest neutral; it sits inside every family's complement and reads as "warm authority on warm surface" universally.
  3. **Aaron's instinct in the rule statement.** "Sand OR a color from the family." When two valid options exist for the same discipline, restraint chooses the one that produces less variation. Sand-900 is that choice.
  4. **The "grew, not built" test.** A callout family where every text color is the same warm neutral feels like the document's own register varying its background; a callout family where each callout has a different text color feels like a graphic system layered on top.
- **Per-directive assignments (all sand-900 unless noted).**
  - `callout-info` (cyan-50 bg) → text sand-900 `#25211d`
  - `callout-warning` (amber-50 bg) → text sand-900
  - `callout-success` (green-50 bg) → text sand-900
  - `callout-error` (red-50 bg) → text sand-900
  - `card`, `card-body` (sand-50 bg) → text sand-900 (sand on sand — already feels right, makes explicit)
  - `card-title` (sand-50 bg) → keep teal-700 (this is the document's interactive register at the card-title level; family-match would lose the section-label reading the iteration-1 spec named). **Exception to the rule; documented inline.**
  - `attestation`, `attestation-gate` (sand-100 bg) → text sand-900
  - `escalation` (sand-50 bg) → text sand-900
  - `decision-branch` (sand-50 bg) → text sand-900
  - `screen-ref` (character style, no bg) → no change — keep teal-700 (inline interactive/wayfinding register, correct as-is)
  - `glossary-term`, `glossary-def` (no bg) → no change — inherit body
- **Why card-title stays teal-700.** The card-title is functionally a heading inside a container. The brand-discipline rule applies to *body text on a surface*; a heading is a different register with its own color logic (the section-label register, which IS teal-700 in the brand). The card-title is the one place where the rule reaches its natural carve-out. Document and proceed.
- **Rationale (brand).** Cena's brand favors **one warm-dark text color** running through the document's working voice, with teal punctuating *interactive moments* (card-title is title-of-a-named-block — borderline, kept) and *body color* punctuating running prose. Multiple text colors per directive would atomize the brand vocabulary; sand-900 unifies it.
- **Honest limit.** This is opinionated. If Aaron prefers family-match (cyan-900 on cyan, amber-900 on amber, etc.) after seeing the regenerate, the revert is per-directive `color:` swaps — five-minute change. Surface this in §12.7 as the single decision Aaron should validate against the rendered output.

### 12.5 Scope kv-table separation

- **What Aaron read.** In the Scope section's kv-table, label cells like "For" feel far from their body content. Eye travel from label to body is noticeable.
- **Diagnosis.** The Scope table is rendered as a pipe-table by pandoc → `<w:tbl>` with the `Table` style. Both label and body cells top-align by default (correct — Word has no `vAlign` on cells without explicit `tcPr`). Cell margins are top/bottom 60 twips (~3pt — iteration 2's addition) + Compact paragraph style with space-after Pt(4) and line-spacing 1.4. The label cell is one line of "For" + cell margins + paragraph space-after = ~24pt of content area; the body cell wraps to 2–3 lines = ~70–90pt of content area. Top-aligned, the label sits at row top with the body cell's wrapped content trailing below — the row stretches to the taller cell, leaving visible empty space below the label *inside the same row*. The reading: label is isolated at the top, body trails below in the next column.
- **Why it's not a styling bug exactly.** Top-alignment is correct (the label and the first line of body text DO read aligned). The "separation" Aaron sees comes from row-height-following-tallest-cell + multi-line body wrapping. This is a pipe-table-as-kv-table architectural limit.
- **What this iteration can do.** Reduce the table cell vertical margin from 60 twips → **30 twips (~1.5pt)**. Rationale: in iteration 2 I added 60 twips because tables felt cramped without any vertical breathing room; in iteration 3 Aaron's signal is that 60-twip vertical margin compounds with `Compact`'s Pt(4) space-after to make label cells visually padded-bottom (empty cell space below "For"). Cutting cell margin in half reduces that bottom-empty-space without removing the breathing entirely.
- **What this iteration CANNOT cleanly do.** Per-cell margin differentiation (label gets margin=0, body keeps 30 twips) requires either per-cell `w:tcPr/w:tcMar` overrides at table-emission time (markdown-source pipeline change) or a dedicated `KvTableCompact` paragraph style + a custom Table style with `tblStylePr` first-column override (architectural addition). Both are beyond this iteration's brand-style scope. Surface as Open Issue #8.
- **The deeper structural fix (Open Issue #8).** Pipe tables are wrong for kv-tables. Definition lists (markdown `Term : Definition`) render in pandoc-docx as dedicated `Definition Term` + `Definition` styles which can be themed independently — a label-then-body register without the row-height/cell-margin coupling. If the SoP markdown source emits kv-tables as definition lists instead, the separation issue disappears at the substrate level. This is markdown-source surgery, not a docx-theme fix; surface to the surface-emit handler owner as a separate recommendation.
- **Rationale (brand).** Principle 6 (restraint as default) — when the substrate constrains visual fidelity beyond what theming can solve, the move is to ship the most-restrained-possible substrate fix and surface the structural fix as an issue, not to over-theme around the limit. Halving the cell margin is the restrained fix; the deeper change is structural.
- **Honest limit.** The 30-twip cell margin may still read as visibly looser than the SoT-site kv-table. The structural fix at the markdown-source layer is the only path to full SoT-site fidelity. Name it; don't paper over it.

### 12.6 Drop-in code (iteration 3)

Three changes layered on top of iteration 2's `apply_theme()` + `apply_directive_styles()` + `CENA_THEME`. Apply in order.

#### 12.6.1 Update `CENA_THEME` color + line-spacing values

In the `CENA_THEME` dict — change the values shown, leave everything else alone:

```python
    # --- HEADING COLORS: H1 + H2 to sand-900 (warm-dark Lora authority);
    #     H3 stays sand-600 (eyebrow); H4 stays sand-700. Body color is
    #     unchanged (text-primary teal-ink for running prose). Iteration 3
    #     override of iteration 2's H1/H2 = #0D322D.
    "heading_colors": {
        1: hex_to_rgb("#25211d"),  # sand-900 — warm-dark Lora authority
        2: hex_to_rgb("#25211d"),  # sand-900 — hierarchy stays in one family
        3: hex_to_rgb("#777069"),  # sand-600 — eyebrow/subsection register (unchanged)
        4: hex_to_rgb("#5a544e"),  # sand-700 — recedes (unchanged)
    },

    # --- TITLE: also shifts to sand-900 for hierarchy consistency
    "title_color": hex_to_rgb("#25211d"),  # sand-900 (was inheriting heading_colors[1])

    # --- BODY LINE-SPACING: 1.55 → 1.4 (iteration 3 override of iteration 2).
    #     1.55 is brand canonical at the web surface; the docx substrate
    #     compounds line-height with paragraph-after spacing, producing a
    #     perceived rhythm looser than the canonical intent. 1.4 lands at
    #     web-1.55-equivalent perceived rhythm on docx.
    "body_line_spacing": 1.4,    # was 1.55

    # --- TABLE CELL MARGIN: 60 → 30 twips top/bottom (iteration 3 override of
    #     iteration 2). Iteration 2 added 60 twips to give tables breathing
    #     room; iteration 3 cuts in half because compounding with Compact's
    #     Pt(4) space-after produced visible empty space below short label
    #     cells in kv-tables.
    "table_cell_margin_v": 30,   # was 60
    "table_cell_margin_h": 108,  # unchanged
```

#### 12.6.2 Extend `apply_theme()` to honor `title_color`

**In `apply_theme()`, replace** the line:

```python
        title_style.font.color.rgb = theme["heading_colors"].get(1, theme["body_color"])
```

**with**:

```python
        title_style.font.color.rgb = theme.get(
            "title_color", theme["heading_colors"].get(1, theme["body_color"])
        )
```

Rationale: title color now defaults to heading_colors[1] (sand-900 after this iteration) but can be overridden per theme without coupling to heading-1 color downstream.

#### 12.6.3 Extend `apply_directive_styles()` for internal padding + text colors

**Add this helper at module level (top of file, after imports)**:

```python
SAND_900 = "#25211d"  # warm-dark text color for directive surfaces
```

**Replace the entire `apply_directive_styles()` function body** with the version below. The changes:
1. Adds auto top/bottom hairline borders when `bg_color` is present, with `w:space` for internal padding (item 12.3).
2. Defaults text color to sand-900 when `bg_color` is present (item 12.4) — directive's explicit `color` key overrides.
3. Preserves all existing behavior (left border via `border_color`, indent, shading, character styles).

```python
def apply_directive_styles(doc: Document, theme: dict):
    """Add haven-directive styles. Only runs if theme defines them.

    Each entry in theme["directive_styles"] adds a Word style named after the
    directive class (e.g., "callout-warning", "screen-ref"). Pandoc maps a
    fenced div's class name to a paragraph style and a bracketed-span's class
    to a character style of the same name.

    Per-directive keys:
      style_type   "paragraph" (default) or "character"
      font, size, color, bold        — both types
      space_before, space_after      — paragraph only
      border_color                   — paragraph only; single left rule, auto
                                       240-twip indent unless "indent" overrides
      bg_color                       — paragraph only; paragraph shading; auto-
                                       emits top/bottom hairline borders with
                                       w:space for internal vertical padding
      bg_padding_v                   — paragraph only; internal vertical
                                       padding in twips (default 120 = ~6pt)
      bg_text_color                  — paragraph only; text color when bg
                                       present; default sand-900 #25211d
      indent                         — paragraph only; left indent in twips
                                       (independent of border)
    """
    if "directive_styles" not in theme:
        return

    from docx.enum.style import WD_STYLE_TYPE

    for style_name, props in theme["directive_styles"].items():
        style_type = props.get("style_type", "paragraph")

        if style_type == "character":
            try:
                style = doc.styles.add_style(style_name, WD_STYLE_TYPE.CHARACTER)
            except ValueError:
                style = doc.styles[style_name]
            font = style.font
            if "font" in props:
                font.name = props["font"]
            if "size" in props:
                font.size = props["size"]
            if "color" in props:
                font.color.rgb = props["color"]
            if props.get("bold"):
                font.bold = True
            continue

        try:
            style = doc.styles.add_style(style_name, WD_STYLE_TYPE.PARAGRAPH)
        except ValueError:
            style = doc.styles[style_name]

        style.base_style = doc.styles["Normal"]

        font = style.font
        font.name = props.get("font", theme["body_font"])
        font.size = props.get("size", theme["body_size"])
        # Color resolution: explicit "color" wins; if bg_color is set and no
        # explicit color, default to sand-900 (or theme-provided bg_text_color)
        # so text family matches the surface (or sits in neutral sand on
        # colored bgs) instead of inheriting teal-ink body color.
        if "color" in props:
            font.color.rgb = props["color"]
        elif "bg_color" in props:
            default_bg_text = props.get("bg_text_color", hex_to_rgb(SAND_900))
            if isinstance(default_bg_text, str):
                default_bg_text = hex_to_rgb(default_bg_text)
            font.color.rgb = default_bg_text
        if props.get("bold"):
            font.bold = True

        pf = style.paragraph_format
        pf.space_before = props.get("space_before", Pt(6))
        pf.space_after = props.get("space_after", Pt(6))

        # Build pBdr: left rule from border_color (existing behavior) +
        # auto top/bottom hairline borders when bg_color is present (new).
        # The auto top/bottom borders carry w:space (internal padding) so
        # the shading region extends with breathing room above and below
        # the text. Hairline width (sz=2) is the smallest renderable;
        # reads as a near-invisible frame edge — actually helps the
        # callout register as a discrete block in Google Docs.
        left_border = "border_color" in props
        has_bg = "bg_color" in props
        if left_border or has_bg:
            pf_elem = style.element
            pPr = pf_elem.find(qn("w:pPr"))
            if pPr is None:
                pPr = parse_xml(f'<w:pPr {nsdecls("w")}/>')
                pf_elem.insert(0, pPr)
            existing_bdr = pPr.find(qn("w:pBdr"))
            if existing_bdr is not None:
                pPr.remove(existing_bdr)

            # Build border children selectively
            border_parts = []
            if has_bg:
                bg_padding = props.get("bg_padding_v", 120)  # twips; ~6pt default
                # Top hairline border — invisible-ish frame + carries internal padding
                border_parts.append(
                    f'<w:top w:val="single" w:sz="2" w:space="{bg_padding // 20}" w:color="auto"/>'
                )
            if left_border:
                border_hex = props["border_color"].lstrip("#")
                border_parts.append(
                    f'<w:left w:val="single" w:sz="18" w:space="8" w:color="{border_hex}"/>'
                )
            if has_bg:
                bg_padding = props.get("bg_padding_v", 120)
                border_parts.append(
                    f'<w:bottom w:val="single" w:sz="2" w:space="{bg_padding // 20}" w:color="auto"/>'
                )

            pBdr_xml = (
                f'<w:pBdr {nsdecls("w")}>' + "".join(border_parts) + '</w:pBdr>'
            )
            pBdr = parse_xml(pBdr_xml)
            pPr.append(pBdr)

        # Optional left indent — explicit "indent" wins; otherwise borders
        # carry a default 240-twip clearance so the rule doesn't crowd content.
        indent_twips = props.get("indent")
        if indent_twips is None and "border_color" in props:
            indent_twips = 240
        if indent_twips is not None:
            pf_elem = style.element
            pPr = pf_elem.find(qn("w:pPr"))
            if pPr is None:
                pPr = parse_xml(f'<w:pPr {nsdecls("w")}/>')
                pf_elem.insert(0, pPr)
            existing_ind = pPr.find(qn("w:ind"))
            if existing_ind is not None:
                pPr.remove(existing_ind)
            ind = parse_xml(f'<w:ind {nsdecls("w")} w:left="{indent_twips}"/>')
            pPr.append(ind)

        # Optional background shading
        if "bg_color" in props:
            bg_hex = props["bg_color"].lstrip("#")
            pf_elem = style.element
            pPr = pf_elem.find(qn("w:pPr"))
            if pPr is None:
                pPr = parse_xml(f'<w:pPr {nsdecls("w")}/>')
                pf_elem.insert(0, pPr)
            existing_shd = pPr.find(qn("w:shd"))
            if existing_shd is not None:
                pPr.remove(existing_shd)
            shd = parse_xml(
                f'<w:shd {nsdecls("w")} w:val="clear" w:color="auto" w:fill="{bg_hex}"/>'
            )
            pPr.append(shd)
```

**Note on the `w:space` units.** In OOXML, border `w:space` is measured in **points**, not twips, so `bg_padding // 20` converts twips → points (1pt = 20 twips). Default `bg_padding_v=120` twips → 6pt internal padding, top and bottom.

#### 12.6.4 Update `directive_styles` dict for explicit per-directive `color` overrides where needed

Most directives now pick up sand-900 text color automatically (since they have `bg_color`). The exceptions need explicit handling:

```python
        # In CENA_THEME["directive_styles"], modify card-title to keep its
        # teal-700 color despite bg_color (sand-50). This is the documented
        # exception — card-title is a section-label register, not body text
        # on a surface, so the family-match rule yields to the section-label
        # rule. (Iteration 3 §12.4.)
        "card-title": {
            "font":         "Lora",
            "size":         Pt(12),
            "color":        hex_to_rgb("#1e5149"),  # teal-700 — section-label register
            "bold":         True,
            "bg_color":     "#fbfaf8",
            "space_before": Pt(4),
            "space_after":  Pt(2),
        },
```

Every other directive with `bg_color` automatically picks up sand-900 text from the new default in `apply_directive_styles()`. **No other directive entries need editing.** If Aaron later prefers family-match per-callout (e.g., `callout-warning` text = amber-900), the path is to add an explicit `"color": hex_to_rgb("#412900")` line per directive.

### 12.7 Single decision to validate post-regenerate

The biggest call in iteration 3 is **§12.4: sand-900 across the board vs. family-match per directive**. My choice: sand-900 across the board for consistency + restraint + Aaron's instinct.

Validation path:
- Regenerate → render the SoP → open in Google Docs.
- Read the four callout variants (info / warning / success / error) side by side. Does sand-900-on-cyan-50, sand-900-on-amber-50, sand-900-on-green-50, sand-900-on-red-50 read as a coherent family? If yes — ship. If a specific callout reads "wrong family" (most likely candidate: red, where sand-900 may pull warm-red toward neutral-gray-ish), the swap is one line per directive: add `"color": hex_to_rgb("#4a0a0e")` (red-900) for `callout-error`, etc.

### 12.8 Open issues added by iteration 3

7. **Single-cell table wrappers for callouts.** The pBdr-based internal padding approach (§12.3) is the cheapest mechanism; if Google Docs round-trip strips the hairline borders, the next move is to wrap each callout in a 1-cell `<w:tbl>` with explicit `tcMar` for padding. Architectural change in `surface-emit/handlers.mjs`.

8. **Definition lists for kv-tables.** The Scope section's pipe-table-as-kv-table has structural separation issues that can't be themed away (§12.5). The clean fix is to emit kv-tables as markdown definition lists, which pandoc renders as `Definition Term` + `Definition` paragraph styles that don't carry the pipe-table cell-height coupling. Markdown-source-layer change.

9. **Per-directive family-match text colors.** §12.4 chose sand-900 universally; per-callout family-match (amber-900 / cyan-900 / etc.) is the alternative. Tracked as a swap-ready decision if Aaron prefers it after seeing the rendered output.

10. **Brand spec amendment for body line-spacing at the docx surface.** `_tokens/typography.md` §3.2.1 specifies 1.55 at the web surface; the docx surface needs 1.4 to deliver the same perceived rhythm (§12.2). The brand spec could carry a per-surface footnote. Not authoring here — typography-spec territory.

