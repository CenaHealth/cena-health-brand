# Haven data-table styling — the table family

_Brand spec for the `data-table` family in haven-ui. Covers variants whose visual treatment carries brand-fidelity weight; structure and CSS implementation live in [`Lab/haven-ui/packages/design-system/pattern-library/components/data-table.html`](../../haven-ui/packages/design-system/pattern-library/components/data-table.html) and [`packages/design-system/src/styles/tokens/components.css`](../../haven-ui/packages/design-system/src/styles/tokens/components.css)._

---

## 1. Context

The `data-table` family is the primary tabular-data primitive across cena-apps' internal staff surfaces (patient roster, programs index, kitchen catalog, order-edit, etc.). Visual treatment for the base shape (sticky first column, sortable headers, row dividers, hover state, header band) lives in `components.css` and codified incrementally as cena-apps surfaces shipped. This spec captures the brand-fidelity decisions for variants whose treatment is **visually weighted** — meaning a brand-taste call drove the spec, not just structural composition. Mechanical structural variants without brand-fidelity weight (e.g., `data-table-section-divider`) are documented in the PL fragment + COMPONENT-INDEX without a spec entry here.

---

## 2. `data-table-total-row` — footer-row variant for sum-of-rows tables

### 2.1 Context

The total-row primitive landed 2026-06-18 against the order-edit surface (the staff-facing meal-quantity editor). Order-edit's data-table sums to a single visible "Order total" — without a footer-row treatment, the total renders as an orphan `<div>` below the table, visually disconnected from the rows it sums. Aaron flagged the orphan reading as "completely separate" rather than as the sum-of-rows the user is composing.

The variant codifies the footer-row shape: a `<tfoot>` row with a top-border separator + tabular-nums on the total cell, composed inside the `data-table` host. Use when the total is the literal sum-of-rows; for arbitrary section breaks use `data-table-section-divider` instead.

### 2.2 Brand frame

A reader scanning a table that sums to a single total should see "the total is the settled outcome of these rows" — not "another data row with bold weight." Three brand decisions follow:

- **Top-border separator weight is one tier heavier than the row dividers.** The row-divider weight is sand-100 (subtle, lets rows read as a continuous scan). The total-row's top-border weight is **sand-300** — visibly more present than the row divider, less assertive than a header band. Sand-300 reads as "a line drawn under the data" rather than "another grid rule." This is the structural call that earns the variant its brand-fidelity weight; sand-200 reads as a slightly-heavier-row-divider (ambiguous), and sand-400+ reads as a hard section break (over-stated for a sum). The 1px weight matches the row-divider's stroke width — only the color shifts.

- **Cell typography mirrors the header band, not the row content.** The label cell uses `cell-primary` weight (sand-900 medium) and the total cell uses `cell-numeric` with semibold + tabular-nums. This matches the visual weight of column headers — the total reads as "a labeled outcome" parallel to "a labeled column." Row content uses sand-600 medium (the cell-primary body weight) — visibly lighter than the total-row's sand-900 cells. The difference in text color (sand-900 vs sand-600) carries the "outcome vs. data" distinction more than any background fill would.

- **No background fill on the total-row.** The header band is sand-100 — a continuous strip across the top of the table. The total-row is intentionally NOT a sand-100 strip below — pairing band-top with band-bottom would visually box the table content, which works for stat-card structures but reads as over-structured for a summable list. The white-on-white-with-top-rule treatment lets the total read as "the table's terminal outcome" rather than "a second header at the bottom."

The "earned weight" test on this variant: a reader scanning the order-edit table should land on the last meal row, sweep down to the total-row's top-border separator, then read the total as "this is what those rows sum to." If the total-row reads as another data row (because the border was too light) OR as a second header band (because the fill was too heavy), the composition reads as broken.

### 2.3 Pairing — `card-footer-borderless` on the form footer below

When the data-table-total-row sits above a form footer (Cancel + Save), the form footer's default `card-footer` top-border would stack against the total-row's top-border, producing visual noise (two parallel rules with content between). The `card-footer-borderless` modifier solves this: same padding rhythm, no top-border. The total-row carries the rule; the form-footer composes the actions.

Use the modifier ONLY when stacking directly under a total-row (or any other rule-drawing primitive). When the form is a plain create/edit form with no preceding rule, use the canonical `card-footer` — the border IS the form's terminal separator.

---

## 3. Open follow-ups

- **Brand-fidelity panel review owed on the total-row variant.** The sand-300 border weight and the cell-primary/cell-numeric typography mirroring the header band are calls that should go through the design-system-steward + brand-fidelity panel; this entry captures the rationale from the ux-design-lead dispatch but the panel pass hasn't formally happened yet. Recorded so a future review surfaces the right artifacts.
- **Dark mode treatment** — sand-600 border in dark mode is mirrored from the canon's general dark-mode practice; explicit dark-mode design call hasn't happened. Defer until a dark-mode-active consumer surfaces.
- **Right-aligned total carve-out** — the order-edit example has the total cell as the rightmost numeric column. For tables whose total isn't visually anchored to a numeric column (e.g., a stat-summary row with a centered text total), the colspan + cell-numeric pair may need a sibling shape; not codified yet. Surface when a second consumer requests it.
