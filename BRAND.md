# Cena Health — Brand Reference

_Last updated: 2026-03-16_
_Status: Phase 7 complete — living document_

This is a distilled reference that spans the full brand system. It is not the source of truth — the source files are linked throughout. Use this when you need a fast orientation, and follow the links when you need the authoritative spec.

---

## 1. What Cena Health Is

Clinical infrastructure for prescription-based nutrition care. Not a food delivery service. Not a wellness brand. Not a tech startup performing empathy. It is a healthcare system layer — plumbing for medically tailored meals — that happens to touch patients at one of the most intimate points in their lives.

Full positioning: `_brief/brand-brief.md §1`

---

## 2. The Unifying Idea

> "Cena Health makes infrastructure feel like it grew rather than was built."

Every decision traces back here. The logo's concentric rings, the teal-to-sage hue shift, the organic deceleration of motion, the natural-metaphor approach to depicting care workflows — all of it recapitulates this idea at every scale.

---

## 3. Design Principles (summary)

| Principle | In one sentence |
|-----------|-----------------|
| Clinical Warmth | Every visual must carry both medical credibility and human approachability — never one without the other |
| Specificity Over Generality | Show the actual thing: a specific dish, a specific moment, a specific community |
| Dignified Agency | Every person depicted is an active participant, never a passive recipient |
| Grounded Optimism | Acknowledge difficulty without catastrophizing — mid-journey moments, not before/after transformations |
| Organic Precision | Warm and deliberate, not loose or sketchy — organic forms executed with intentional control |
| Infrastructure Humility | Technology shown at human scale, in context, as a tool in people's hands |

Full spec: `principles/design-principles.md`

---

## 4. Color

### 4.1 Logo Colors (locked)

| Name | Hex | Role |
|------|-----|------|
| Deep forest green-black | `#0D322D` | Wordmark "Cena", primary text |
| Medium teal | `#3A8478` | Wordmark "health", brand identity (non-interactive) |
| Lighter teal | `#52A395` | Middle icon ring |
| Sage green | `#81B983` | Innermost icon ring |

### 4.2 Critical Color Rules

- **Background is `#FBFAF8` (`var(--color-surface-page)`) always.** Never `#ffffff` or `white`. This is non-overridable.
- **Interactive fill is `color-primary` (teal-400, `#1B685E`)**, not `color-brand-primary` (teal-500, `#3A8478`). Teal-500 fails WCAG AA with white text.
- `color-text-brand` = teal-400. Teal-500 is identity, not text.
- All OKLCH within sRGB gamut. Hex fallback via `@supports`.
- `color-brand-secondary` (teal-600) and `color-brand-sage` are non-text-safe.

Full tokens: `_tokens/color.md` and `src/css/tokens-color.css`
Web reconciliation: `_tokens/color-web.md`

### 4.3 Feedback Color Families

Four feedback families for UI state communication:

| Family | Base token | Use |
|--------|-----------|-----|
| Info | `--color-info-*` | Informational status |
| Warning | `--color-warning-*` | Caution, time-sensitive |
| Error | `--color-error-*` | Failures, blocking states |
| Success | `--color-success-*` | Confirmations, completion |

Each family has: `-bg`, `-border`, `-base`, `-text` tokens. Backgrounds are tinted; base and text values carry contrast requirements. Full values in `_tokens/color.md §4`.

### 4.4 Surface Hierarchy

Five named surfaces in ascending elevation:

| Surface | Token | Hex | Use |
|---------|-------|-----|-----|
| Page | `--color-surface-page` | `#FBFAF8` | App/page background |
| Primary | `--color-surface-primary` | `#F3F1EE` | Cards, panels, sidebars |
| Secondary | `--color-surface-secondary` | `#E7E4DF` | Nested panels, input backgrounds |
| Teal | `--color-surface-teal` | `#E9F5F2` | Brand accent surfaces |
| Sage | `--color-surface-sage` | `#E7F2E8` | Success/growth accent surfaces |

Elevation through surface color shifts and warm borders, not shadows. Shadows only for floating elements (modals, dropdowns, toasts).

---

## 5. Typography

### 5.1 Typefaces

| Role | Family | License |
|------|--------|---------|
| Display / headings | Lora | OFL |
| Body / UI | Source Sans 3 | OFL |
| Monospace / code / data | Source Code Pro | OFL |

Full spec: `_tokens/typography.md` and `_tokens/typography-web.md`

### 5.2 Scale

Major third (1.250) ratio, 16px base, 11 steps (all clean integers). CSS tokens: `--text-xs` through `--text-4xl`.

### 5.3 Critical Rules

- `--font-display` = Lora (headings, large callouts)
- `--font-body` = Source Sans 3 (all UI text, labels, body copy)
- `--font-mono` = Source Code Pro (code, numeric data, EHR IDs)
- Density is set via `data-density` on parent containers — never per-component font-size overrides
- Two-tone wordmark logic (dark "Cena" / teal "health") stays isolated to the logo — does not extend to body type

---

## 6. Spacing

4px base. 9/15 steps on the 8px grid; 6 sub-grid values intentional. Tokens: `--space-0.5` through `--space-24`.

Density tiers via `data-density="compact"` / `data-density="comfortable"` on parent containers. Never modify per-component padding for density.

Full spec: `_tokens/spacing.md` and `_tokens/spacing-web.md`

---

## 7. Motion

### 7.1 Easing Tokens

| Token | Curve | Use |
|-------|-------|-----|
| `--ease-enter` | Decelerate (cubic-bezier(0.0, 0.0, 0.2, 1)) | Elements entering the scene |
| `--ease-exit` | Accelerate (cubic-bezier(0.4, 0.0, 1, 1)) | Elements leaving the scene |
| `--ease-transition` | Standard (cubic-bezier(0.4, 0.0, 0.2, 1)) | State changes, hover |

### 7.2 Duration Tokens

| Token | Value | Use |
|-------|-------|-----|
| `--duration-micro` | 80ms | Pressed states, scale transforms |
| `--duration-fast` | 120ms | Hover color changes, dismiss exits |
| `--duration-normal` | 200ms | Component enter animations, panel reveals |
| `--duration-slow` | 300ms | Page transitions, large reveals |

### 7.3 Reduced Motion

All animated components must include `@media (prefers-reduced-motion: reduce)` overrides. Reduced motion: opacity changes only (no translate, no scale, no height animation), instant or `duration-fast` timing. Full spec: `_tokens/motion.md`

---

## 8. Visual Language

### 8.1 Iconography

- System: outlined/stroke icons, 2px primary strokes
- Size tokens: `--icon-sm` (16px), `--icon-md` (20px), `--icon-lg` (24px), `--icon-xl` (32px)
- Color: always a semantic token, never hardcoded
- No filled icons in functional UI — filled variants reserved for active/selected states

Full spec: `visual-language/iconography.md`

### 8.2 Illustration

Three core types: spot (120–240px), narrative scenes (400–800px, 16:9), data illustrations (120–320px alongside metrics).

Key rules:
- Infrastructure through natural metaphor — not diagram, not literal pipes
- No text overlays, no standalone icons, no UI-style graphics within illustrations
- UI elements in illustrated scenes = color blocks only, no text
- 25% surface-area rule for warm swatches; three-swatch maximum
- Photography proves; illustration expresses — never composited

Full spec: `visual-language/illustration.md`

### 8.3 Photography (if used)

Warm color treatment: highlights → `#F3F1EE` (warm neutral 900), shadows → `#0D322D` (teal-200 equivalent), saturation −15–20% from capture default.

Full spec: `visual-language/imagery.md`

### 8.4 Surface Treatment

Elevation via surface color layering, not drop shadows. Shadows only for Level 4+ (floating: modals, dropdowns, toasts). Full token set and use rules: `visual-language/surface-treatment.md`

### 8.5 Document & Spreadsheet Emission

How the brand renders when exported to Google Docs/Sheets for partners (the `/share` pipeline). Display Lora + body Source Sans 3; text ink `#0D322D`, accent/table-header teal-700 `#1e5149`, borders sand-300. Contrast rule: white text only on dark fills, ink on light. Full spec (doc + spreadsheet treatment): `visual-language/document-emission.md`

---

## 9. Component System

### 9.1 What exists

**UI components:** button, input, card, alert (spec complete, CSS implementation pending)
**Slides components:** cover-slide, content-slide, stat-callout

Full CSS: `src/css/components/` and `src/css/slides/`
Full specs: `components/ui/` and `components/slides/`

### 9.2 Adding a new component

Every new component requires changes to four places — in this order:

1. `components/ui/{name}.md` or `components/slides/{name}.md` — design spec
2. `src/css/components/{name}.css` or `src/css/slides/{name}.css` — implementation
3. `src/css/main.css` — add `@import` alphabetically in Components section
4. `pattern-library/components/{name}.html` — markup with all variants
5. `pattern-library/pages/{name}.html` — page that loads the component
6. `pattern-library/partials/pl-nav.html` — nav link in the correct section
7. `pattern-library/pages/index.html` — card in the index grid

Pattern library page structure: `CLAUDE.md §Pattern library — what to update`

### 9.3 Component CSS rules

- All values use semantic CSS custom properties — never raw hex, never hardcoded pixels
- `@layer components` wrapper on all component CSS files
- Density inherited from parent `data-density` — never set per-component
- Reduced motion block in every component that animates

---

## 10. Accessibility Floor

- WCAG AA minimum. AAA for primary/secondary text.
- Dual-cue required for all success states (icon + text, not color alone)
- Touch targets: 44×44px minimum
- Focus rings: 2px `color-border-focus` ring at 4px offset. `color-border-focus` (#3A8478) verified for all surface types
- All interactive components: keyboard navigable, screen reader tested
- `color-brand-secondary` and `color-brand-sage` are non-text-safe — never use for text

Full audit: `audits/accessibility-audit.md`

---

## 11. Dev Server and Build

```bash
npm run dev        # watch mode → http://localhost:5174
npm run build:css  # dist/cena-health.css (~49KB minified)
```

Production artifact: `dist/cena-health.css`

---

## 12. Agent Infrastructure

Agent roles and skill files: `.agents/`
Project context for agents: `.agents/PROJECT-CONTEXT.md`
AI coding rules: `CLAUDE.md`
Outstanding work queue: `ROADMAP.md §Outstanding Work`
