# Haven print/fill-form styling — the print-medium hand-fill register

_Brand-fidelity treatment + print-fidelity rationale for the `doc-form-*` primitive family (Document district, print/fill-form artifact-type). Place-3 of the five-place codification gate. Two artifacts in one doc: a **visual entry** (the standard brand-spec "why each looks the way it does" layer) and a **`## Print-fidelity` section** (the print-medium rationale that has no slot in the UI brand-spec shape — the load-bearing half)._

> Sibling of [`haven-directive-styling.md`](haven-directive-styling.md) (the docx / Document-district screen-read register). This spec owns the **print/paper/fax** register of the same district. Primitive family + CSS + codification gate: `~/.claude/plans/scratch/2026-07-07-haven-print-form-primitive-codification.md` (Haven Steward). Source form: `Lab/haven-ui/handoff/cena-referral-form-practice/index.html`.

---

## 1. Context — what this surface is

- The `doc-form-*` family is the Document district's **print/hand-fill register**: a static, printable, faxable form a human completes with a pen. First instance is the UConn HIV-pilot Medical Meals Referral (manual / fax fallback).
- It is deliberately **not** the app-interactive `form-*` family (data-bound inputs, dirty-tracking, focus flows) and **not** the screen-read `document-*` register (52rem screen measure, docx export). It is paper. Ink goes on the page by hand; the page may travel through a fax machine.
- Three destinations, in descending fidelity: **screen** (a floated sheet the provider prints or saves as PDF), **print/PDF** (the physical page), and **fax** (a monochrome, low-resolution, bilevel-thresholded transmission). Every visual decision below is judged against the *worst* of these — the fax — because a form that only survives on screen is a form that fails in the field.
- This spec is the visual-brand + print-fidelity rationale for a family whose CSS, PL fragment, and COMPONENT-INDEX rows are owned by the Steward's codification packet. It **references** the primitive names and the canonical tokens; it does not restate the CSS.

---

## 2. Brand frame for the print medium

The print form is the Document district rendered through a substrate — physical paper, possibly a fax — that compresses brand decisions differently than either the screen or the docx surface. Four things hold steady across the compression:

- **Warm ground is the one deliberate exception on this surface.** The brand's essential mechanism is Warm Ground, Cool Figure — `surface-page` (`#FBFAF8`) never pure white. The print sheet **breaks this on purpose**: `doc-form-sheet` is `#fff`. A referral form is a legal/clinical instrument that must photocopy and fax cleanly, and a warm-tinted ground desaturates to a muddy mid-gray on a fax (adding noise to the very transmission the form exists to survive). The warm ground lives in the **screen chrome around** the sheet (`sand-100` body behind the floated page) — so the brand's warmth frames the form on screen, and the form itself stays paper-honest. This is the one place the print medium overrides Principle 3, and it is a fidelity decision, not a lapse.
- **Teal punctuates; it never fills.** `teal-700` carries the masthead rule and the section-title register (the document's section-label authority, matching the H2/H3 label treatment in the docx spec); `teal-500` accents the section number and the "Health" word of the wordmark. This is the **section-label register**, not an interactive-commit fill — so it is consistent with haven-design ruling #1 ("teal = commit-only; teal punctuates, never fills"). There is no interactive commit on a paper form; the teal here is pure editorial punctuation, the same use `card-title` makes of `teal-700` in the docx spec.
- **The red register is the form's only severity moment, and it is dual-cued by construction.** The `--practice` banner and the `doc-pending-placeholder` are the only non-neutral color on the page. Because the page must survive a monochrome fax where hue is destroyed, the red is *never allowed to carry signal alone* — it is always paired with a non-color redundant cue (border, bold weight, bracket syntax, literal words). §4.5 is where this discipline is proven against the luminance math.
- **Type carries the hierarchy; size does the work print can't delegate.** Lora commands (masthead title, section titles), Source Sans 3 works (labels, body, check rows, footer). But on paper the hierarchy is anchored in **physical point size**, not a viewport-relative scale — §4.3.

**The quiet-mode test on paper:** strip the practice banner and the pending markers. The page should still read as Haven — Lora section titles in teal, Source Sans labels in muted sand, a teal masthead rule, generous fill-lines with breathing room between them. If the quiet base reads as a generic government form, the teal section register or the type hierarchy is missing. The test's inversion also matters here: strip the *color* (imagine the fax) and the page must **still function** — every fill-line, checkbox, warning, and pending marker legible in pure grayscale. A print form that only works in color is a print form that fails its medium.

---

## 3. Per-primitive visual spec (the visually-weighted decisions)

Color values reference Cena Color System v2 (`_tokens/generated/palette.css`). **Canonical token names are `--color-red-*`, `--color-amber-*`, `--color-teal-*`, `--color-sand-*` — NOT `--color-error-*` / `--color-warning-*`** (the Steward's draft CSS used the latter; those tokens do not exist in the palette — see §5, flag 1).

### 3.1 `doc-form-sheet` — the page surface

- **Treatment.** Paper-white `#fff` (not `surface-page`), Letter measure (`8.5in` max-width), floated on screen (`sand-100` body ground behind it, `0.5in` auto margin, 3px hairline `sand-200` border, 3px radius, warm-tinted soft shadow); under `@media print` the float dissolves (margin/padding/border/shadow → 0; the physical `@page` margin takes over).
- **Rationale.** The paper-white ground is the deliberate Principle-3 override argued in §2 — fax cleanliness wins over warm-ground on this one surface, and the warmth is preserved in the screen chrome around the sheet. The screen float (border + soft shadow + margin) is the brand's standard "page floats on ground" treatment (haven-design ruling #5) applied so the on-screen form reads as a document sitting on the warm workspace; it is chrome, correctly discarded on print. The radius stays small (3px) because a printed instrument reads as square-cornered paper, not a rounded card.
- **Quality note.** Quiet-mode: on screen the sheet should read as a real page resting on a warm desk; on paper it should read as an unadorned clinical form. The shadow is the only elevation and it exists only on screen — no shadow survives to print (Principle 4, shadows-last, honored trivially because print has no elevation).

### 3.2 `doc-form-status-banner` / `--practice` — the red warning register

- **Treatment.** Full-width banner at the top of the sheet. `--practice` = the **red/critical register**: `--color-red-50` background, `--color-red-700` text + border, bold weight, centered, letter-spaced, uppercase warning copy ("PRACTICE COPY — NOT FOR LIVE USE · DO NOT FAX"). The un-suffixed default banner sits in the **amber/warning register** (`--color-amber-50` bg, `--color-amber-700` border) for lower-severity states (DRAFT, REVISED).
- **Rationale.** "PRACTICE — NOT FOR LIVE" is a genuine do-not-use-in-production warning — the highest severity a form carries — so the error/red register is correct (same severity logic as `alert-error` in the docx spec, one district over). Cena's red is warm-shifted and earthen (`red-500` `#c13c3b`, `red-700` `#832224`), not candy-red, so it reads as the page raising a serious flag rather than a fire alarm — Principle 6 restraint preserved even at top severity. The banner is the print sibling of `document-draft-banner`; keeping it a **separate primitive with register variants** (red `--practice` vs. amber default) rather than folding into the screen draft-banner is a define-once call flagged to Aaron (Steward D1).
- **The fax question this raises is the load-bearing one — see §4.5.** Short version: the red *background* vanishes on a fax and the red *text/border* survives only as generic dark ink, so the warning must never depend on hue. The border + bold + uppercase + centered + literal words are what carry it through the monochrome transmission. Those cues are load-bearing, not decorative.

### 3.3 `doc-form-masthead` — teal rule + wordmark lockup + title/eyebrow/meta

- **Treatment.** Wordmark lockup (compose `brand-wordmark-cena-health`, do not fork) + `doc-form-masthead-title` (Lora medium, `teal-700`, 15.5pt) + `-eyebrow` (Source Sans semibold uppercase, `sand-600`, letter-spaced) + `-meta` (small sand body, holds pending markers). A **2px `teal-700` rule** closes the masthead below.
- **Rationale.** This is the print realization of `document-masthead` plus the branded wordmark lockup. The wordmark renders "Cena" in the anchor near-black (`#0D322D`, the Lora wordmark register) and "Health" in `teal-500` — teal punctuating the brand mark exactly as it does on every Cena surface. The masthead title is Lora because it is the document's editorial authority (Lora commands); `teal-700` on the title is the section-authority register, not an interactive fill. The 2px teal rule is the strongest teal moment on the page and the right place for it — it is the document declaring its identity boundary, structural not interactive.
- **Quality note.** Grew-not-built: the wordmark + teal rule read as the letterhead of a real Cena instrument, not a template with a logo pasted on. On fax the teal "Health" desaturates to a mid-gray (it may read slightly lighter than "Cena") but the Lora-bold letterforms survive thresholding — the wordmark loses its teal tint but never its legibility (acceptable brand-tint loss, not a signal loss; §4.5).

### 3.4 `doc-form-section` / `-title` / `-num` — the teal section-label register

- **Treatment.** `doc-form-section-title` in Lora medium, `teal-700`, 11pt, with a 1px `sand-200` underline; `doc-form-section-num` in `teal-500` preceding the title; `doc-form-subhead` in bold uppercase `sand-600` for sub-groups; `doc-form-section-note` in small `sand-600` for instruction lines.
- **Rationale.** Section titles are the print form's primary wayfinding. Lora + `teal-700` is the document-district section-label register (the same register `card-title` uses `teal-700` for in the docx spec — "the established section-label color in the theme"). The `teal-500` number is a lighter teal punctuation, giving the numeral a quiet accent without competing with the title text. The `sand-200` underline is a decorative separator, deliberately the lightest structural element (it is allowed to fade on a fax; the teal-700 title text carries the section boundary regardless — §4.5).
- **Quality note.** Two teal weights in one line (700 title, 500 number) is the accumulation principle — the numeral reads as *part of* the section register but subordinate to the title. Restraint holds: teal appears only in the masthead rule, section titles, section numbers, and the wordmark accent — never as a fill, never on a fill-line or checkbox.

### 3.5 `doc-pending-placeholder` — value-not-yet-known inline red

- **Treatment.** Inline span, `--color-red-700`, semibold, wrapping bracketed content: `[ Fax destination — pending confirmation ]`, `[ IRB Protocol # — pending approval ]`.
- **Rationale.** A pending marker signals "this value is not yet known" at the moment the form is authored — it must be *impossible to miss* so a form never ships to a provider (or a fax) with a hole still open. Red is the correct alarm hue on screen. But the marker is the subtlest fax-survival case on the page: the red text desaturates to a dark gray *indistinguishable from body ink* after a fax's bilevel threshold. Its survival therefore rides entirely on the **bracket syntax + bold weight + the literal word "pending"** — the `[ … ]` structural cue and the word are what make the marker legible-as-a-hole in pure grayscale. The red is the screen cue; the brackets are the fax cue. This makes the bracket syntax a **content contract, not incidental formatting** — flagged to the Steward (§5, flag 2).
- **Cross-district note.** "Value not yet known" recurs across all Document/print output (and plausibly decks), so this primitive is a cross-district-vocabulary candidate (Steward D2) — the red + bracket + bold treatment specced here is the reference realization wherever it lands.

---

## 4. Print-fidelity (the load-bearing rationale with no UI-brand-spec slot)

This section is the rationale slot the UI brand-spec shape has no room for. A UI primitive's "why it looks this way" is fully captured by color/type/spacing on screen. A **print/fill** primitive's most consequential decisions are fill-ergonomics, physical type size, page-break integrity, and monochrome-fax survival — none of which a screen brand-spec reasons about. Per the 2026-07-03 haven-family-contract stress-test, this is exactly the medium where the rationale slot breaks if it is only realized as a visual entry; §4 is that slot realized for print.

### 4.1 Fill-line heights + hand-fill ergonomics

- **The values (rationale, not restatement):** `doc-form-field-fill` `min-height: 15px`; the inline `doc-form-check-inline` fill `min-height: 14px`; `~5px` vertical margin between stacked fields.
- **Why 15px.** A fill-line is where a human writes with a pen. At print scale, 15px ≈ 11pt ≈ ~4mm of vertical clearance above the baseline rule — the ergonomic floor for one line of adult handwriting without the ink colliding into the label above or the line below. Shorter and the written value crowds the label; taller and the page holds fewer fields per sheet (a real cost on a one-page faxable form). 15px is the minimum that reads as "there is room to write here."
- **Why a bottom-border, not a box.** The fill is a single `border-bottom` rule, not an outlined box. The pen rides the rule; a boxed field invites the writer to cram inside the box and fights multi-word values. The `~5px` inter-field margin gives stacked fill-lines enough separation that handwritten values on adjacent lines don't merge.
- **Baseline flex, never wrap.** The label + fill compose on a shared baseline via flex; the label is `white-space: nowrap` and fixed-width, the fill takes the remaining width. A fill-line that wrapped to a second row would break the "one value, one line" reading a paper form depends on.

### 4.2 Checkbox pen-target sizing

- **The values:** `doc-form-checkbox` is an `11px` square, `1.5px` border (`anchor` near-black), `2px` radius, `vertical-align: -1px`.
- **Why 11px — a pen target, not a click target.** A screen checkbox obeys the 44×44px WCAG *touch* target (a fingertip needs room to land). A **pen** target has different ergonomics: the hand places ink precisely, so the square can be small; what matters is that it reads unambiguously as "mark here" and sits inline with 9.5pt label text. 11px ≈ 8pt ≈ ~2.9mm — the smallest square that reads as a discrete checkbox at desk distance and receives a ballpoint X or ✓ legibly. Larger would float off the text baseline and read as a button; smaller would collapse toward a bullet.
- **Why the 1.5px border and the near-black.** The border weight is doing fax-survival work (§4.5) as much as brand work: a hairline square edge drops out on a fax, so 1.5px is the floor for the box to survive as a *box* rather than a smudge. The `anchor` near-black (`#0D322D`) desaturates to ~15% gray — essentially black — so the checkbox reads crisply in pure monochrome. The 2px radius softens the corners to match Haven's rounded language without rounding so far that the square stops reading as a checkbox.

### 4.3 Point-size type rationale (pt, not rem)

- **The scale (print / screen):** body 10pt / 10.5pt · labels 9pt · check rows 9.5pt · subheads 8.3pt · section titles 11pt · masthead title 15.5pt · footer fine-print 7.8pt.
- **Why pt, not the rem type-scale.** Print legibility is anchored to *physical* size. `rem` scales with root font-size — a viewport-relative screen concept; on paper a 10pt body is a fixed physical 10-point measure regardless of zoom or device. The haven screen type-scale (rem-based) does not transfer; print demands physical point sizes chosen against desk reading distance. This is the clearest instance of a rationale a UI brand-spec cannot hold: the screen scale and the print scale are different axes, not different values on one axis.
- **Why these sizes.** 9pt is the legibility floor for labels and instructions an adult reads at desk distance on a filled form; 10pt body sits just above it; the 7.8pt footer is legal fine-print at the "reference, not read" register (acceptable because it is contact/BAA boilerplate, never a fill target). Section titles (11pt Lora) and the masthead title (15.5pt Lora) establish hierarchy through physical size + serif authority. The **screen values run +0.5pt** (10.5 vs 10 body) because on-screen viewing distance exceeds paper-at-desk; print tightens back to the physical floor.

### 4.4 Page-break behavior (`break-inside`, `@page`, screen-float / print-fill)

- **`break-inside: avoid` on `doc-form-section`.** A numbered section is the **atomic print unit**. A section split across a page break — label on page 1, fill-line on page 2 — breaks the form's integrity: the provider loses label context mid-fill, and a faxed split section transmits as two disconnected fragments that can be mis-collated. `break-inside: avoid` keeps each section whole; the page break falls *between* sections, never through one.
- **`@page { size: Letter; margin: 0.5in }`.** Sets the physical page to US Letter (the fax/clinical standard) with a 0.5in margin that clears the fax machine's non-printable edge and the receiving machine's header band.
- **Screen-float / print-fill dual behavior.** On screen the sheet floats (decorative margin/border/shadow, §3.1); under `@media print` those reset to 0 and the `@page` margin owns the physical edge. Screen-only affordances (the "Print / Save as PDF" button, `doc-form-screen-only`) are `display: none` on print. This medium-adaptation logic has **no screen analog** — it exists only because one source renders to two physical realities.

### 4.5 Fax-safe grayscale — the monochrome-survival discipline + THE verdict

**The judgment call, stated plainly:** a referral form gets faxed. A fax is monochrome, low-resolution, and (in standard mode) **bilevel** — every pixel thresholds to pure black or white, destroying hue entirely. Does the practice-banner / pending-placeholder **error-red survive**? Tested against the actual token luminance (Rec-601 luma `Y = 0.299R + 0.587G + 0.114B`, the transform scanners/fax use to grayscale before thresholding):

| Element | Token | Hex | Luma (0–255) | Grayscale | Fax fate |
|---|---|---|---|---|---|
| Banner background | `red-50` | `#ffe9e5` | ~239 | ~94% (near-white) | **Vanishes** — thresholds to white |
| Banner text + border | `red-700` | `#832224` | ~63 | ~25% (dark) | Survives as dark ink — **but loses all "red = danger" meaning** |
| Pending placeholder | `red-700` | `#832224` | ~63 | ~25% (dark) | Survives as dark ink — **indistinguishable from body ink after threshold** |
| Body ink | `sand-900` | `#25211d` | ~34 | ~13% (near-black) | Baseline — near-black |
| Fill-line | `sand-300` | `#bbb6ad` | ~182 | ~72% (light) | **Marginal** — thin light-gray rule at real drop-out risk |
| Checkbox border | `anchor` | `#0D322D` | ~38 | ~15% (near-black) | Survives crisply |
| Masthead teal rule | `teal-700` | `#1e5149` | ~65 | ~25% (dark, 2px) | Survives |
| Section underline | `sand-200` | `#cfcac2` | ~203 | ~79% (light) | Fades — acceptable (decorative) |
| Wordmark "Health" | `teal-500` | `#3a8478` | ~109 | ~43% (mid) | Survives via bold Lora strokes; loses teal tint |

**Verdict: the error-red does NOT survive a monochrome fax as a color signal — and it must never be allowed to try.**

- The red *background* (`red-50`, ~94% gray) desaturates to effectively white and **vanishes**.
- The red *text and border* (`red-700`, ~25% gray) survive as dark ink but lose every trace of "red = warning" — the hue that carried the meaning is gone, and after bilevel thresholding the red text is indistinguishable from ordinary body ink (~13%).
- Therefore **any warning or pending signal that relies on hue alone FAILS on fax.** This is the WCAG "never rely on color alone" / dual-cue principle, imported into the fax medium where it is not a preference but a physical certainty.

**The fallback treatment (the discipline the primitives must bake in):**

1. **`--practice` banner — the redundant cues are load-bearing, not decorative.** The warning survives the fax *only* because it is carried simultaneously by: a **border** (`red-700`, ~25% gray — survives as a dark enclosing rule), **bold weight**, **uppercase + letter-spacing**, **centered layout**, and the **literal words** ("PRACTICE COPY — NOT FOR LIVE USE · DO NOT FAX"). Every one of those is a non-color cue that survives monochrome. Keep all of them. **Recommend the `--practice` border go to 2px** (from 1.5px): at fax resolution (≈200×100 dpi standard) a 1.5px light rule is at the edge of survival, and the border is the banner's primary bounding cue once the red-50 fill is gone. A 2px `red-700` rule reads unambiguously as an enclosing warning box in pure grayscale.
2. **`doc-pending-placeholder` — the bracket syntax is a content contract.** With the red gone and the dark text merged into body ink, the marker's "this is a hole" signal rides entirely on the `[ … ]` bracket wrapper + bold weight + the literal word "pending." So the brackets are **not incidental formatting** — they are the fax-fallback cue and must be guaranteed, not left to author discipline. Either the primitive's canonical usage must *require* the `[ … ]` wrapper (documented + linted), or the brackets should be CSS-injected (`::before { content: "[ " } ::after { content: " ]" }`) so the fallback cue can never be forgotten. Recommendation: document the bracket-as-contract now; consider CSS injection if a pending marker ever ships without brackets. Flagged to Steward (§5, flag 2).
3. **Fill-lines — the brand-restrained `sand-300` is a screen/PDF choice; a fax-destined form needs a darker line.** The `sand-300` fill-line (~72% gray) is the correct *restrained* warm rule on screen and PDF — quiet, brand-coherent. But at ~72% gray + 1px it is **marginal on a fax**: thin light-gray lines are the first casualty of bilevel thresholding + low dpi, and the fill-line is the single most critical element (it is where the ink goes). For any form that may actually be faxed, the fill-line should darken to **`sand-500` (~56%) at minimum, `sand-700` (~33%) for certainty** and/or thicken to 1.5px, so the rule the provider writes on survives the transmission. This is a genuine print-fidelity finding: the restrained brand line and the fax-survivable line are different values, and the medium decides which. Flagged to Steward (§5, flag 3) as a candidate print-context modifier.

**What is acceptable to lose on fax:** the `sand-200` section underline (decorative — the teal-700 section title carries the boundary), the wordmark's teal "Health" tint (the Lora-bold letterforms keep the word legible), the screen float. None of those carry fill-critical or warning signal.

**One honest note on the practice copy specifically:** this first form's banner literally says "DO NOT FAX" — it is a training document, not meant to travel. But the *live* version of this form WILL be faxed, pending markers may still be present on early live copies, and future `doc-form-status-banner` registers (DRAFT, REVISED) could appear on a form that is faxed. So the fax-survival discipline is baked into the **primitive family**, not waived because this one instance forbids faxing. The family must be fax-safe even where a given instance isn't faxed.

---

## 5. Flags back to the Haven Steward (primitive CSS the brand-fidelity read requires)

Ordered by how load-bearing. Flags 1–3 are changes the brand-fidelity + print-fidelity read says the primitive CSS should carry; the rest are confirmations.

1. **Token names are wrong in the draft CSS — `--color-error-*` / `--color-warning-*` do not exist.** The palette (`_tokens/generated/palette.css`) uses `--color-red-*` and `--color-amber-*`. Remap at landing: `doc-form-status-banner` default → `--color-amber-50` / `--color-amber-700`; `--practice` → `--color-red-50` bg, `--color-red-700` text+border; `doc-pending-placeholder` → `--color-red-700`. (The source form's raw `#9a2820` was an approximation of `red-700` `#832224`; use the token.) This is the Steward's own "verify at landing" caveat, now confirmed: the names differ, remap is required.
2. **`doc-pending-placeholder` — the `[ … ]` bracket wrapper is a fax-survival content contract, not incidental.** The red is destroyed on a monochrome fax; the marker's "this is a hole" signal then rides entirely on brackets + bold + the word "pending." Either document the canonical usage as *requiring* the `[ … ]` wrapper (and lint it), or CSS-inject the brackets (`::before`/`::after` content) so the fallback cue can't be dropped. Bold weight (already present) stays mandatory.
3. **`doc-form-field-fill` — add a fax-context darker fill-line variant.** `sand-300` (~72% gray) is the correct restrained line for screen/PDF but marginal on a fax (thin light-gray rules drop out first). Recommend a modifier (e.g. `doc-form-field--fax` or a print-context rule) darkening the fill-line to `sand-500`/`sand-700` and/or 1.5px for forms that will actually be faxed. Default stays `sand-300`; the darker line is opt-in for the fax path.
4. **`doc-form-status-banner--practice` — bump the border to 2px.** Once the `red-50` fill vanishes on a fax, the border is the banner's primary bounding cue; 1.5px is at the fax-resolution survival edge. 2px `red-700` reads unambiguously as an enclosing warning box in grayscale. (1.5px is fine for the amber default register, which is lower-stakes and less likely to be the fax-critical warning.)
5. **Confirmations (no change needed):** the paper-white `doc-form-sheet` ground (deliberate Principle-3 override, §2) is correct — do not "fix" it to `surface-page`. The `teal-700` masthead rule + section titles are the section-label register, consistent with ruling #1 (teal punctuates) — not an interactive-fill violation. The `anchor` checkbox border and its 1.5px weight are fax-correct — keep.

---

## 6. Token references + honest limits

**Canonical token source (define-once — referenced, never restated):**
- Color: `Lab/cena-health-brand/_tokens/generated/palette.css` — `--color-red-{50,500,700}`, `--color-amber-{50,500,700}`, `--color-teal-{500,700}`, `--color-sand-{200,300,500,700,900}`, anchor `#0D322D`.
- Type: canonical Haven stack — **Lora** (masthead title, section titles), **Source Sans 3** (labels, body, check rows, footer). Print uses physical `pt` (§4.3), not the rem type-scale.
- Primitive family, CSS, PL fragment, COMPONENT-INDEX rows: the Steward's codification packet `~/.claude/plans/scratch/2026-07-07-haven-print-form-primitive-codification.md`.
- Sibling register (docx / screen-read Document district): [`haven-directive-styling.md`](haven-directive-styling.md).

**Honest limits:**
- **The fax analysis is luminance-model + threshold reasoning, not a physical fax test.** The Rec-601 luma values and the bilevel-threshold predictions are sound (they are the actual transforms scanners/fax use), but the drop-out calls on marginal elements (`sand-300` fill-lines, the 1.5px vs 2px border) are predictions to confirm with a real fax round-trip on the *live* form before it ships to the field. Predict-then-verify, same discipline as the docx spec's Google-Docs round-trip.
- **Fax modes vary.** Standard mode is bilevel (the worst case this spec designs against); fine/superfine modes retain more grayscale and would preserve more than the table predicts. Designing against the worst case means the form over-survives the better modes — the safe direction.
- **The `sand-500` hex (`#958e85`) and the fax luminance floor are read from the current palette;** if the palette's sand ramp is retuned, re-run the luma check on the fill-line variant before treating "sand-500 survives fax" as settled.
- **This spec authors visual + print-fidelity rationale; it does not author the CSS.** The Steward owns the primitive CSS; flags in §5 are proposals for that CSS, landed by the Steward + Aaron per the codification packet's landing checklist. Place-3 of the five-place gate is green when this spec (visual entry + `## Print-fidelity`) is authored AND the Steward's CSS absorbs the §5 flags.
