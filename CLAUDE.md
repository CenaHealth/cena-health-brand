# Cena Health Brand — AI Coding Assistant Rules

Read this file completely before touching any file in this project.

---

## The five rules most commonly violated

**1. Background is `#FBFAF8` (`var(--color-surface-page)`), never `#ffffff` or `white`.**
The warm off-white is the entire warmth mechanism. Using pure white breaks it.

**2. Interactive fill is `color-primary` (teal-700, `#1e5149`), not `color-brand-primary` (teal-500, `#3A8478`).**
Teal-500 (4.54:1) fails the brand's WCAG bar with light text; teal-700 clears AAA (8.96:1). Use `var(--color-primary)` for buttons and interactive states. (Cena Color System v2 — conventional scale, so the dark interactive teal is teal-700, not v1's inverted teal-400.)

**3. All component values use semantic CSS custom properties, never raw hex or hardcoded pixels.**
Write `var(--color-text-primary)`, `var(--space-4)`, `var(--radius-md)` — not `#0D322D`, `16px`, `8px`.

**4. Elevation uses surface color shifts and warm borders first, shadows last.**
Cards sit on `surface-primary` on `surface-page`. Add `border` for more separation. Only use `shadow-*` for floating elements (modals, dropdowns, toasts).

**5. Density is set once on a parent container via `data-density="compact"`, never per-component.**
The spacing token scale re-maps under `[data-density="compact"]` and `[data-density="comfortable"]` — do not add per-component padding overrides to achieve density.

---

## Stack

- **Tailwind CSS v4** (CSS-first, no `tailwind.config.js`)
- **vite-plugin-html-inject** for `<load src="...">` partials
- **Vite** dev server at `http://localhost:5174` (`strictPort: true`)
- **No Preline, no jQuery, no component framework** — vanilla JS only
- **No FontAwesome** — icons are inline SVG only

---

## Tailwind v4 — Critical Rules

### All CSS must use `@layer`

Every component CSS file must wrap its rules in `@layer components`. Base/reset rules use `@layer base`. Failure to do this causes Tailwind to override component styles.

```css
/* Correct */
@layer components {
  .alert { ... }
}

/* Wrong — rule will be overridden by Tailwind utilities */
.alert { ... }
```

### Pure CSS rules need `display` or another property to survive tree-shaking

In Tailwind v4, any CSS rule with **only raw CSS properties** (no `@apply`) can be silently dropped during build. To prevent this, always include at least one meaningful CSS property (any real CSS declaration is sufficient — using `display` is a good convention).

```css
/* Wrong — may be silently dropped */
.pl-note {
  font-style: italic;
}

/* Correct — raw CSS rule survives */
.pl-note {
  display: block;
  font-style: italic;
}
```

This applies anywhere in `src/css/` — component files, base.css, and slides.

### Never use `@apply` with a custom semantic class

`@apply` can only resolve Tailwind's own utility classes. It cannot resolve custom classes defined in your own CSS files. Writing `@apply alert` inside another class will cause a build error.

```css
/* Wrong — build error */
.alert--custom {
  @apply alert;
}

/* Correct — repeat the properties inline */
.alert--custom {
  display: flex;
  align-items: flex-start;
  /* ... rest of alert base ... */
}
```

## References

**Token names:** See [.project-docs/token-namespace.md] — all spacing, radius, motion, typography, and color token names.

**CSS template:** See [.project-docs/component-css-template.md] — required structure for component CSS files.

**Pattern library HTML:** See [.project-docs/pattern-library-conventions.md] — component and page HTML structure, nav sections.

---

## Pattern library build workflow

Every component build task follows this sequence:

1. **Read** `.agents/skills/pl-component-builder/SKILL.md` — before writing any file
2. **Output a pre-build plan** — the skill specifies the format
3. **Write the files** — following the structural rules in the skill
4. **Run** `npm run build:css` — must pass before proceeding
5. **Run QA** using `.agents/skills/pl-component-qa/SKILL.md` — produces a structured pass/fail report
6. **Report to Aaron** with the QA report and a list of things to verify at localhost
7. **Commit** only after Aaron confirms, then push to remote

Do not skip steps 2, 4, or 5. The pre-build plan prevents class name errors. The QA report catches systematic errors before they reach Aaron.

---

## Adding a new component — the required update list

Every new component requires all of these, in this order:

1. `components/ui/{name}.md` — design spec (if not already present)
2. `src/css/components/{name}.css` — CSS implementation using `@layer components`
3. `src/css/main.css` — add `@import "./components/{name}.css"` alphabetically in the Components section
4. `pattern-library/components/{name}.html` — markup with all variants
5. `pattern-library/pages/{name}.html` — page that loads the component via `<load src="../components/{name}.html" />`
6. `pattern-library/partials/pl-nav.html` — nav link in the correct section
7. `pattern-library/pages/index.html` — card in the index grid

Do not skip any of these. An incomplete update leaves the pattern library out of sync with the CSS.

---

## JavaScript rules

- **Vanilla JS only.** No Preline, no jQuery, no component framework.
- Component demo JS lives in the component HTML file as an IIFE: `(function() { ... })()`
- Do not add `<script src="...">` tags to component files — inline only
- `pl-scripts.html` handles the active nav state script — add page-level shared scripts here
- Never use `localStorage` or `sessionStorage`

---

## Accessibility requirements for every component

- WCAG AA minimum. AAA for primary/secondary text.
- Dual-cue for all success states: icon + text label, never color alone.
- Touch targets: 44×44px minimum for all interactive elements.
- Focus ring: `box-shadow: 0 0 0 2px var(--color-surface-page), 0 0 0 4px var(--color-border-focus)` — match the inner ring to the component's background on colored surfaces.
- `aria-label` on icon-only buttons.
- Appropriate `role` and `aria-live` on alert/status components.
- Every motion component needs `@media (prefers-reduced-motion: reduce)` override.

---

## Build and verification

- Dev server: `npm run dev` → `http://localhost:5174`
- Always verify at `http://localhost:5174` — never a different port (`strictPort: true`)
- CSS-only build: `npm run build:css` → `dist/cena-health.css`
- Full build: `npm run build`

---

## Verification checklist

After every build task:

- [ ] Dev server at `http://localhost:5174` — not any other port
- [ ] All CSS wrapped in `@layer components` (or `@layer base` for resets)
- [ ] No raw CSS rules without at least one CSS property (tree-shaking safety)
- [ ] All values use semantic tokens — no raw hex, no hardcoded px
- [ ] Density inherited from parent `data-density` — no per-component padding overrides
- [ ] `src/css/main.css` updated with new `@import` (alphabetically)
- [ ] `pattern-library/components/{name}.html` created
- [ ] `pattern-library/pages/{name}.html` created
- [ ] `pattern-library/partials/pl-nav.html` updated with new nav link
- [ ] `pattern-library/pages/index.html` updated with new card
- [ ] All animated components include `@media (prefers-reduced-motion: reduce)` block
- [ ] Dual-cue on all success states (icon + text label)
- [ ] `npm run build:css` completes without error

---

## Completion report format

After every task, output this:

```
## Completion Report — {TASK-ID}

- Files created: [list]
- Files modified: [list]
- New CSS classes added: [list]
- Token names used: [list]
- Judgment calls: [list, or "none"]
- Reduced motion added: yes / no
- Build passes: yes / no
- Items deferred or incomplete: [list, or "none"]
```

Then provide the git commands:
```bash
git add -A
git commit -m "{description of what was built}"
```

---

## Dummy copy — use the canonical registry

All placeholder text comes from `.project-docs/dummy-copy.md`. Do not invent names, conditions, stats, or copy. Do not use Lorem ipsum. The canonical patient is **Maria Rivera** (68, MRN PT-2024-0847, Type 2 Diabetes). Known naming collision: "James Chen" appears as both patient and provider — use him only as a provider in new components.

---

## Where to find the full system

- Brand brief: `_brief/brand-brief.md`
- Color tokens: `_tokens/generated/palette.css` (Cena Color System v2 canon — 18 families, conventional scale, `sand` neutral) + `src/css/tokens-semantic.css` (semantic layer). v1 retired to `src/css/_archive/tokens-color-v1.css` (do not consume). Migration rationale: `Knowledge/Projects/Cena Health/Brand/token-reconciliation-recommendation.md`
- Typography spec: `_tokens/typography.md` and `src/css/tokens-typography.css`
- Spacing spec: `_tokens/spacing.md` and `src/css/tokens-spacing.css`
- Motion spec: `_tokens/motion.md` and `src/css/tokens-motion.css`
- Component specs: `components/ui/` and `components/slides/`
- Design principles: `principles/design-principles.md`
- Coherence notes: `principles/coherence-notes.md`
- Accessibility audit: `audits/accessibility-audit.md`
- Outstanding work queue: `ROADMAP.md §Outstanding Work`
- Agent roles: `.agents/agent-roles.md`
- Full project context: `.agents/PROJECT-CONTEXT.md`
