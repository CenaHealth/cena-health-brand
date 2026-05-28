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
