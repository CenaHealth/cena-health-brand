---
name: cena-pl-component-builder
description: Build a new pattern library component page for the Cena Health design system. Use this skill for every pattern library component build task — before writing any file. Produces a structured pre-build plan that the agent must follow exactly.
---

# Cena Health — Pattern Library Component Builder Skill

## When to invoke this skill

Read this skill before building any pattern library component. It applies whenever the task involves:
- Creating `pattern-library/components/{name}.html`
- Promoting a "coming soon" page stub to a real component page
- Updating any of the 7 required files in the component update checklist

Do not start writing files until you have completed Steps 1–3 below.

---

## Step 1: Read before touching anything

In this exact order:

1. `CLAUDE.md` — the five rules, the Tailwind v4 constraints, the 7-file checklist
2. `pattern-library/components/alert.html` — this is the canonical structural model. Every convention in this file is the standard.
3. `src/css/components/{name}.css` — every class name that exists. Do not invent class names.
4. `components/ui/{name}.md` — every variant, size, state, density behavior, and surface rule.
5. The current state of `pattern-library/pages/{name}.html` — note what already exists before overwriting.
6. `pattern-library/partials/pl-nav.html` — find the correct section for the new nav link.

---

## Step 2: Produce a pre-build plan (output this before writing files)

Before writing any file, output this structured plan:

```
## Pre-Build Plan — {Component Name}

### Classes confirmed in {name}.css
[List every class the component HTML will use, verified against the CSS file]

### Sections to build
[List each pl-section with its data-pl-section value and what it demonstrates]

### JS required
- Icon injection: yes / no
- Interactive demo: yes / no — [describe what it demonstrates]
- State simulation: yes / no — [which states need frozen visual demos]

### 7-file update checklist
- [ ] src/css/components/{name}.css — exists / needs creation
- [ ] src/css/main.css — @import present / needs adding
- [ ] pattern-library/components/{name}.html — needs creation
- [ ] pattern-library/pages/{name}.html — stub / needs promotion
- [ ] pattern-library/partials/pl-nav.html — link present / needs adding
- [ ] pattern-library/pages/index.html — card present / needs adding
- [ ] components/ui/{name}.md — spec exists (read-only in this task)

### Token names to be used
[List every CSS custom property the component HTML or state-simulation styles will reference]

### PL-only styles needed
[List any .btn--state-hover style helper classes needed for frozen state demos — these go in a <style> block in the component file ONLY, never in src/css/]
```

---

## Step 3: Structural rules — follow these exactly

### Component HTML file structure

Every component HTML file must follow this exact structure in this exact order:

```html
<!--
  @component-meta
  name: {Name}
  category: {Category}
  file: {name}.html
  classes: {comma-separated list of all CSS classes}
  spec: components/ui/{name}.md
  css: src/css/components/{name}.css
  when-to-use: {one sentence}
  do-not-use-when: {one sentence}
  @end-meta
-->

{PL-only <style> block if needed — state simulation helpers only}

{<template> blocks for SVG icons if needed}

{pl-section blocks — one per variant group}

{<script>(function() { ... })()</script> — IIFE at bottom, only if JS is needed}
```

### pl-section structure (required for every section)

```html
<section class="pl-section" data-pl-section="{unique-id}">
  <h2 class="pl-section-title">{SECTION TITLE IN CAPS}</h2>
  [<p class="pl-section-description">Only if explanation is genuinely needed.</p>]

  <div class="pl-canvas" style="background: var(--color-surface-{name});">
    <p class="pl-canvas-label">{surface-name} ({hex})</p>
    {component markup}
  </div>
</section>
```

Multiple canvases within a section (for surface matrix demos) are stacked directly inside the section without a wrapper div — use `<div style="display: flex; flex-direction: column; gap: var(--space-4);">` if spacing is needed.

### pl-canvas surface backgrounds (use these exact styles)

| Surface | Style attribute |
|---------|----------------|
| page | `style="background: var(--color-surface-page);"` — label: `surface-page (#FBFAF8)` |
| primary | `style="background: var(--color-surface-primary);"` — label: `surface-primary (#F3F1EE)` |
| secondary | `style="background: var(--color-surface-secondary);"` — label: `surface-secondary (#E7E4DF)` |
| teal | `style="background: var(--color-surface-teal);"` — label: `surface-teal (#E9F5F2)` |
| sage | `style="background: var(--color-surface-sage);"` — label: `surface-sage (#E7F2E8)` |

### Layout helpers within canvases

Do not add new classes to `src/css/`. Use inline styles or existing PL classes from `pl-head.html`:
- `pl-stack` — vertical stack with `gap: var(--space-3)`
- `pl-grid-3` — 3-column grid
- `pl-col-label` — column label above items
- `pl-row` — does not exist as a class; use `style="display: flex; gap: var(--space-3); flex-wrap: wrap; align-items: center;"`

### State simulation helpers (PL-only `<style>` block)

When frozen visual states are needed for documentation (hover, focus, active), add a `<style>` block at the top of the component file. These classes must:
- Never duplicate class names from `src/css/`
- Use `!important` to override interactive states (these are visual-only demos with `pointer-events: none`)
- Be prefixed with `--state-` to make their PL-only nature obvious

```html
<style>
  .demo--hover  { /* frozen hover visual */ }
  .demo--focus  { /* frozen focus visual */ }
  .demo--active { /* frozen active visual */ }
</style>
```

### JS rules

- IIFE only: `<script>(function() { ... })()</script>` — no external references
- Icons: inject from `<template>` elements, not hardcoded inline in each markup instance
- Dismiss/interactive demos: always use event delegation on a container, not per-element listeners
- Never use `localStorage`, `sessionStorage`, or any Web APIs that require permissions
- Keep JS minimal — the pattern library is documentation, not a product

### Dummy copy — required for all placeholder text

All placeholder text must come from `.project-docs/dummy-copy.md`. Do not invent
names, conditions, stats, or filler copy.

- The canonical primary patient is **Maria Rivera** (Type 2 Diabetes, Mediterranean Diet,
  MRN PT-2024-0847, enrolled January 2025). Use her for any single-patient context.
- For tables and data grids, use the full patient list from `dummy-copy.md`.
- For provider names, use the canonical provider list from `dummy-copy.md`.
- **No Lorem ipsum.** It is banned. Use the Cena Health medium paragraph block
  from `dummy-copy.md` for any body-text placeholder need.
- The "James Chen" collision (patient + CMO) is known. Use him only as a provider
  in new components.

### Forbidden in component HTML files

- Raw hex values (e.g. `color: #0D322D`) — use CSS custom properties
- Inline `style` attributes with raw hex — use CSS custom properties in inline styles too
- New semantic classes in `<style>` blocks — PL-only state helpers only
- `<script src="...">` tags — inline IIFE only
- Tailwind utility classes in component markup — semantic classes only

### Note on PL chrome styles in `pl-head.html`

The PL chrome styles (sidebar, canvas, nav) in `pl-head.html` are documented as "intentionally raw values" but this is wrong — they should also use semantic tokens. Raw palette steps like `var(--color-warm-100)` are unsafe because the warm scale is inverted (50 = darkest, 950 = lightest), making it easy to accidentally use dark values where light ones are intended.

When editing `pl-head.html`, always use semantic surface and border tokens:
- Sidebar background: `var(--color-surface-primary)`
- Page background: `var(--color-surface-secondary)`
- All borders and dividers: `var(--color-border-default)`
- Active nav background: `var(--color-teal-950)` (light teal tint)
- Hover background: `var(--color-surface-secondary)`

Never use `var(--color-warm-50)` through `var(--color-warm-400)` in PL chrome — these are dark values.

---

## Step 4: Required sections for every component

Not all components need all sections, but the following are standard and should be included unless there is a specific reason to omit:

| Section | data-pl-section | Include when |
|---------|----------------|--------------|
| Variants | `variants` | Always |
| Sizes | `sizes` | Component has size variants |
| States | `states` | Component has interactive states |
| Icon-only | `icon-only` | Component supports icon-only mode |
| Button group / composition | `composition` | Component is used in groups |
| Density tiers | `density` | Always |
| Surface variants | `surfaces` | Always — show all 5 surfaces or the ones with meaningful differences |
| Enter animation | `animation` | Component has enter/exit animation |

---

## Step 5: After writing all files

Run `npm run build:css` before declaring the task complete. If it fails, fix the error before proceeding.

Then hand off to the `cena-pl-component-qa` skill for the QA pass.

---

## What this skill does NOT cover

- Design decisions — follow the spec in `components/ui/{name}.md` exactly
- Token changes — those go through the standard agent review chain
- Component spec authorship — this skill assumes the spec already exists
- Visual judgment calls — those are Aaron's review at localhost
