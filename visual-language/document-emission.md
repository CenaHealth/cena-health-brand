# Cena Health — Document & Spreadsheet Emission Styling

_How the brand renders when content is exported to office formats (Google Docs / Google Sheets) for partners and clients. The applied subset of the type and color systems for a delivery medium that isn't the web app or the pattern library._

> [!note] @Aaron
> Added 2026-05-27 after the UConn pilot docs were shared to Drive and surfaced drift: the emission tooling was carrying stale v1 values (a teal that no longer existed in v2, Inter instead of Source Sans 3) and a spreadsheet contrast bug (white text on light fills). This doc is now the canonical spec; the implementation lives in the vault `/share` machinery and references back here.

This is an **output-channel spec**, a sibling of `data-visualization.md`, `iconography.md`, and `imagery.md`. It does not redefine tokens — it specifies how the canonical tokens apply when a markdown source is converted to `.docx`/`.xlsx` and opened in Google Docs/Sheets.

Sources (canonical — this doc references, never restates the full scales):
- Color: `_tokens/color-system-v2.md` + `_tokens/generated/palette.css`
- Type: `_tokens/typography.md`
- Spacing: `_tokens/spacing.md`

---

## 0. Why emission needs its own spec

- Exported office docs inherit a constraint the web app doesn't have: a **fixed page width** and a **different rendering engine** (Google Docs/Sheets), so web CSS doesn't carry over — the styling is re-applied at conversion time.
- Partners (UConn, clients) judge brand fidelity on these artifacts. They are client-facing.
- Without a canonical spec the styling drifts: the implementation accumulated stale values and a contrast bug precisely because there was no spec to check against.

---

## 1. Shared brand foundation (applies to both docs and sheets)

- **Type**
  - Display / headings: **Lora** (`--font-display`)
  - Body / labels: **Source Sans 3** (`--font-body`)
  - Mono / code: **Source Code Pro** (`--font-mono`)
- **Color (applied subset)**
  - Text: `#0D322D` — `--color-text-primary` (brand anchor ink)
  - Accent / interactive / table-header fill: teal-700 `#1e5149` — `--color-primary` (clears AAA with white text)
  - Identity mark (quote borders, brand flourishes): teal-500 `#3a8478` — `--color-brand-primary`
  - Borders: sand-300 `#bcb8b1` — `--color-border-default`
  - Surfaces / stripes: sand-50 `#fbfaf8` (page), sand-100 `#e6e4e0` (panel), warm blend for zebra
- **Contrast rule (non-negotiable)**
  - White text **only** on dark fills (teal-700, anchor). On any **light** fill (sand, light-teal band, peach callout) text is brand ink `#0D322D`.
  - Why it's called out: the inverse — white on a light fill — renders invisible. It was the first bug this spec caught (UConn data-dictionary README, 2026-05-27).

---

## 2. Document styling (`.docx` → Google Docs)

- **Page**: portrait Letter, 1in margins. Do **not** use landscape for prose — it stretches line length past a comfortable measure and doesn't widen narrow tables (fix the table, not the page).
- **Headings**: H1/H2 Lora `#0D322D` at 24/18pt; H3 Source Sans 3 teal-700 `#1e5149` at 12pt (section-label register); H4 Source Sans 3 sand-700 `#5a544e` at 11pt. Space-before 16pt, space-after 6pt.
- **Body**: Source Sans 3 11pt, `#0D322D`, **leading ~1.5** (canonical body leading is 1.55), paragraph space-after 8pt. Tight leading reads cramped — don't drop below 1.45.
- **Lists**: gentle spacing — space-before 0, space-after ~4pt, line ~1.4. Not the stacked-gap default (too airy) and not collapsed (too tight).
- **Tables**: fill the text column (`tblW` = 100% + autofit; pandoc otherwise sizes to content and floats narrow). Header row teal-700 fill + white bold, repeated across page breaks. Sand-100 zebra stripe, sand-300 borders. Cell padding ~4pt vertical / 6pt horizontal so content clears the borders. **7+ columns → emit as a spreadsheet instead** (a doc table can't give 7 columns usable width).
- **Links**: teal-700 `#1e5149`, semibold.
- **Quotes**: Lora bold italic teal-700, with a teal-500 left border.

---

## 3. Spreadsheet styling (`.xlsx` → Google Sheets)

Use a spreadsheet (not a doc table) when content is a grid the recipient filters / sorts / maps field-by-field, or when a table exceeds ~6 columns.

- **Header row**: teal-700 `#1e5149` fill, white bold Source Sans 3, **frozen** (`freeze_panes`), vertically centered.
- **Data cells**: brand ink `#0D322D`, Source Sans 3 ~10pt, wrapped, **top-aligned**. Sand zebra stripe optional for long tables.
- **Borders**: sand-300 `#bcb8b1` thin, all sides.
- **Affordances**: autofilter on the header row; freeze the header (+ any key left identifier columns); size column widths to each column's role (don't leave default).
- **Padding note**: Sheets has no cell padding like docx — the levers are adequate column width + wrap + top-align (Sheets auto-sizes row height to wrapped content).
- **Multi-tab structure**: a README/context tab + the data tab + supporting tabs (e.g. decisions). On the README tab, section headers use **light** bands (light-teal `#e9f5f2`, warm-sand, or peach callout) with **ink** text — never white-on-light (the contrast rule). Only the single dark title band gets white text.

---

## 4. Implementation (lives in the vault `/share` machinery, references this spec)

- **Documents**: `.claude/config/drive-themes/generate-references.py` (branded pandoc reference docs — the `cena` theme is this spec's §1–2) + `post-process.py` (table full-width, header styling, cell padding).
- **Spreadsheets**: `.claude/config/drive-themes/xlsx_cena.py` — a reusable styler exposing `style_table_sheet`, `text_color_for`, and `band`. New `.xlsx` emissions import it rather than re-deriving styling.
- **Operational how-to** (when to pick docx vs xlsx, column budget, verify-via-quicklook): `/share` skill, "Format fit & table readability."
- **When v2 tokens change**: update §1 here, then regenerate the reference docs and re-style any live emissions.

---

## 5. Provenance

- 2026-05-27 — Created. Reconciled the emission theme from stale v1 values to Color System v2; added the spreadsheet half and the contrast rule after the UConn pilot Drive emissions surfaced the drift and the white-on-light bug.
