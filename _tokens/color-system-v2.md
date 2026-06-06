# Cena Health — Color System v2

_Generative root/max/min color architecture. 17 Tailwind hue families + 1 neutral, Cena-tuned. OKLCH-in-P3 canonical, sRGB hex as legacy fallback. Tailwind scale-direction (50=lightest, 950=darkest)._

**Authority:** This document is the canonical color-system spec for Cena Health going forward. The earlier `color.md` and `color-web.md` are preserved as historical reference but no longer canonical once v2 is approved.

**Status:** Phase A draft, 2026-05-11. Pending Aaron review + approval. Phase B (build-time generator) and Phase D (haven-ui integration) follow.

**Relationship to other docs:**
- `data-visualization.md` — applies the palette to diagrammatic content; v2's family set covers all five data-vis anchors (teal, sage, ochre, violet, indigo) and the warm-neutral baseline
- `illustration.md` — applies the palette to illustrative content
- `iconography.md` — applies the palette to icons
- `surface-treatment.md` — references surface tokens which alias into the palette
- `principles/design-principles.md` — Principle 1 (Hue Shift Is the Brand) + Principle 6 (Two Registers) shape which families are foreground vs supporting

---

## 0. Why v2 exists

Three structural defects in v1 forced a system-level redesign:

1. **Cross-family contrast asymmetry.** Hand-tuned 11-stop ladders + Tailwind-HSL passthrough fragments gave families uneven perceptual weight at the same nominal stop. Default warm-neutral pills read lighter than colored pills using the canonical fill-950/border-800/text-500 formula. The cross-family contrast issue was load-bearing — it surfaces on every multi-family surface (badges, alerts, substrates, diagram color anchors).
2. **Scale-direction inversion.** v1's `color.md` uses 50=darkest, 950=lightest. haven-ui uses Tailwind convention (50=lightest, 950=darkest). Same hex appears at different stop numbers in each system. Hypothesis: this inversion is the source of recurring dark/light theme bugs — `.dark` overrides referencing inverted stops silently flip semantics.
3. **Family-set arbitrariness.** Some families were brand-canonical hex; some Tailwind defaults; some missing (ochre). No principled answer to "what's the next family to add" or "what does that family's lightness curve look like."

v2 addresses all three by construction: every family has the same architecture, the same number of derivation parameters, the same scale-direction. The 17 Tailwind family names provide ecosystem familiarity and future-proof chart/illustration/infographic needs while every value is Cena-tuned.

---

## 1. Architecture

### 1.1 The generative model

Each family is defined by exactly three OKLCH-in-P3 values plus zero or more locked-stop constraints:

```
family-name: {
  root:   oklch(L_root  C_root  H_root)
  max:    oklch(L_max   C_max   H_max)     // lightest extreme
  min:    oklch(L_min   C_min   H_min)     // darkest extreme
  locked: [                                 // optional, for logo / brand-anchor stops
    { stop: 400, value: oklch(...) },
    { stop: 800, value: oklch(...) }
  ]
}
```

The 11-stop ramp (50, 100, 200, 300, 400, 500, 600, 700, 800, 900, 950) is computed by interpolation along the root↔max gradient (lighter half) and root↔min gradient (darker half).

### 1.2 Scale direction

**50 = lightest, 950 = darkest. Across all repos, all references, all consumers.**

This is Tailwind convention. v1's inverted scale (50 = darkest in `color.md`) flips in v2. Hex values do not change; only the numerical labels move. Consumers who previously referenced `color-teal-200` for the dark-anchor wordmark color reference `color-teal-800` in v2.

Migration table for the four currently-locked stops:

| Color | Hex | v1 stop | v2 stop |
|---|---|---|---|
| Logo primary teal | `#3A8478` | teal-500 | teal-500 (palindrome, unchanged) |
| Logo secondary teal | `#52A395` | teal-600 | teal-400 |
| Logo anchor-dark teal | `#0D322D` | teal-200 | teal-800 |
| Logo sage green | `#81B983` | sage-700 | sage-300 |

### 1.3 Position mapping

The 11 stops are NOT proportional to their stop numbers. They are 11 named positions on the root-to-max-and-min gradient:

| Stop | Position from root | Gradient |
|---|---|---|
| 50 | 5/5 toward max | lighter |
| 100 | 4/5 toward max | lighter |
| 200 | 3/5 toward max | lighter |
| 300 | 2/5 toward max | lighter |
| 400 | 1/5 toward max | lighter |
| **500** | **0 (= root)** | — |
| 600 | 1/5 toward min | darker |
| 700 | 2/5 toward min | darker |
| 800 | 3/5 toward min | darker |
| 900 | 4/5 toward min | darker |
| 950 | 5/5 toward min | darker |

The mapping mirrors Tailwind's stop-number convention so haven-ui's Tailwind utility classes (`bg-teal-500`, `text-sand-800`) work without translation.

### 1.4 Interpolation curve

OKLCH lightness (L) varies along the gradient via a uniform curve. **The same curve applies to every family. No per-family curve tuning.**

- **Default curve: linear-in-L.** `L(p) = L_root + (p/5) * (L_anchor - L_root)`, where `p` is position (1 to 5) and `L_anchor` is `L_max` or `L_min` depending on gradient direction. Each numerical stop sits at an equal L-delta from its neighbors. Linear gives uniform perceptual stepping across the 11 stops, which matches Tailwind v4's actual L-distribution and produces distinct values at every stop number.
- **Why not cubic ease.** An earlier draft (pre-2026-05-11) specified cubic ease with k=2.4. Preview feedback (Aaron 2026-05-11): cubic clustered values at the extremes (stops 50/100 visually similar, stops 400/500 perceptually far apart) and produced too few distinct mid-tones. Linear inverts that — perceptually distinct stops across the full range, no clustering at extremes.
- **Chroma (C) and Hue (H) interpolation:** linear in OKLCH coordinates from root → max and root → min. Chroma typically peaks at root (high saturation at the perceptual middle) and tapers to lower values at both extremes. Hue drift is typically small (a few degrees) and usually warmer at lighter values.

### 1.5 Logo identity vs family palette — separate concerns

Originally the family palette was going to "constrain to logo" so logo hex landed on clean stop numbers. **This is revised 2026-05-11 (Aaron).** Logo identity and family palette are now **decoupled**:

- **Logo identity tokens** (§5) hold exact hex values for the four logo-derived colors. These are referenced by logo SVG, wordmarks, and any surface that must render the logo identity at brand-canonical hex.
- **Family palette stops** (`--color-teal-*`, `--color-sage-*`, etc.) are computed by the default interpolation curve from root/max/min. They may drift slightly from the logo's exact hex at the equivalent stop number. That drift is acceptable at this stage of the brand's life; Aaron can adjust later if needed.

This decoupling lets the family palette stay clean (default curve, no per-family tuning, mechanical generation) while logo identity remains hex-exact and load-bearing.

Surfaces choose which token namespace to consume:
- Logo SVG paths, wordmark text fill, anchor surfaces requiring identity fidelity → `var(--logo-primary)`, `var(--logo-secondary)`, `var(--logo-anchor-dark)`, `var(--logo-sage)`
- Everything else (buttons, badges, alerts, text, borders, fills) → `var(--color-teal-500)` etc. from the family palette

The two namespaces will hex-drift slightly but visually relate (a button using `--color-teal-500` and a logo using `--logo-primary` will both read as "Cena teal," just not pixel-equivalent hex). Where pixel-exact match matters within a single context, surface authors reference the logo token explicitly.

### 1.6 Output format

Build-time generator produces, per family, per stop:

- **Canonical:** OKLCH-in-P3 declaration
  ```css
  --color-teal-500: oklch(56.3% 0.0762 181.3);
  ```
- **Legacy fallback:** sRGB hex for the same value
  ```css
  --color-teal-500-hex: #3A8478;  /* sRGB-clipped fallback */
  ```
  Surfaces that consume hex (static SVG export via librsvg, email rendering, very old browsers) reference the `-hex` variant. Default consumers reference the OKLCH variant.
- **Optional CSS color-mix expression:** for runtime tuning use cases
  ```css
  --color-teal-500-mix: color-mix(in oklch, var(--color-teal-root) 0%, var(--color-teal-max) 0%);
  ```
  Not used in standard surfaces; available for advanced cases.

The build-time generator is the single source of truth. No hand-edits to the emitted token files; all changes go through the family-definition source spec.

---

## 2. Family Set — 17 hues + 1 neutral

### 2.1 The 17 hue families (all Cena-tuned, Tailwind-named)

Names follow Tailwind convention. Hue centers, chroma profiles, and lightness curves are Cena-tuned, NOT Tailwind defaults.

| # | Family | Hue center | Status | Cena role |
|---|---|---|---|---|
| 1 | **red** | H:25 (warm red-orange) | Phase A active | Error / critical (feedback) |
| 2 | **orange** | H:35 (terra cotta) | Phase C | Reserved; ochre's hue space — could absorb ochre's data-vis role |
| 3 | **amber** | H:82 (warm ochre-gold) | Phase A active | Warning (feedback) |
| 4 | **yellow** | H:95 (warm yellow) | Phase C | Reserved; illumination/highlight |
| 5 | **lime** | H:120 (sage-adjacent warm) | Phase C | Reserved |
| 6 | **green** | H:160 (desaturated green) | Phase A active | Success (feedback) |
| 7 | **emerald** | H:155 (saturated green) | Phase C | Reserved; alt success or chart accent |
| 8 | **teal** | H:181 (brand primary) | Phase A active | Brand identity, infrastructure, institutional |
| 9 | **cyan** | H:200 (warm-shifted) | Phase A active | Info (feedback) — warm-shifted to keep cool-exclusion |
| 10 | **sky** | H:220 (warm-shifted) | Phase C | Reserved; slate-blue adjacent — warm-shifted per Phase-0 #1 |
| 11 | **blue** | H:235 (slate-blue) | Phase C | Info-blue-adjacent — Cena's only allowed cool hue zone (warm-shifted toward slate) |
| 12 | **indigo** | H:265 (warm blue-violet) | Phase C | Extended palette — partners/external systems (per data-vis spec) |
| 13 | **violet** | H:305 (warm purple) | Phase C | Extended palette — data/AI/inference (per data-vis spec) |
| 14 | **purple** | H:285 | Phase C | Reserved; alt violet |
| 15 | **fuchsia** | H:325 | Phase C | Reserved |
| 16 | **pink** | H:340 | Phase C | Reserved; warm-pink — alt rose |
| 17 | **rose** | H:350 (warm pink) | Phase C | Extended palette — community/relational (not used as data-vis anchor per spec) |

**Phase A active = currently in use by haven-ui components.** These get fitted root/max/min targets in §3. Phase C families get target values during the 17-family build-out.

**Cool-exclusion principle preserved:** sky/blue are warm-shifted toward slate-blue, NOT toward Tailwind's cool defaults. Cyan is warm-shifted toward teal territory. The three close-positioned families (cyan/sky/blue) need Phase C distinguishability testing — if they fail, the system retreats to defining 14 families instead of 17.

### 2.2 The single neutral

| Family | Hue range | Cena role |
|---|---|---|
| **sand** | H:60-85 (warm yellow-brown) | The only neutral. Surfaces, backgrounds, body text, borders, dividers. |

Sand maps to Tailwind's `neutral` for utility-class compatibility. The five defensive aliases (`stone`, `slate`, `gray`, `zinc`, `neutral`) mirror sand identically — preserved from haven-ui's current pattern as a Tailwind-cascade-override defense.

Cool gray, cool slate, cool blue-gray are explicitly excluded from the system. Cena identity is warm-ground; cool neutrals break the warmth mechanism.

### 2.3 Cena-canonical 18th family — sage

Cena's brand requires a hue between Tailwind's `lime` (H:120) and `green` (H:160) that Tailwind doesn't name: warm yellow-green at H:145, sage. The logo's inner-ring identity hue (`#81B983`) lives in this gap. Sage is treated as the 18th hue family — it follows the same root/max/min architecture as the 17 Tailwind families but keeps its `--color-sage-*` token name (not aliased to a Tailwind family).

**System total (after sky drop 2026-05-11):** 16 Tailwind hue families + sage (Cena-canonical) + sand (neutral) = **18 families**.

Sky (Tailwind name) was dropped 2026-05-11 per Phase-0 #1's distinguishability fallback. Sky H:215 was sandwiched between teal (H:181) and cyan (H:235); under Cena's warm-restraint chroma profile, sky/blue/cyan family-50 stops read near-identical in Aaron's visual review (preview iteration 2026-05-11). Sky's token namespace is reserved but not generated; surfaces that need slate-blue-like accents use either teal-pale stops or cyan-pale stops depending on direction.

**Implication for future ports:** any port that assumes "Tailwind 17 names" needs to add sage and accept sky's omission. The build-time generator emits sage alongside the 16 active Tailwind families with no special-casing.

---

## 3. Phase A Family Targets — root, max, min for the seven active families

These are **target inputs** for the Phase B build-time generator. The default cubic-ease curve (§1.4) applies to all families uniformly; logo identity tokens (§5) hold exact logo hex separately. Family palette stops may drift slightly from logo hex at the equivalent stop number — accepted per §1.5.

### 3.1 teal — brand primary

```
root:   oklch(56.3%  0.0762  181.3)    // #3A8478  ← stop 500 (perceptual middle, aligned to logo primary)
max:    oklch(96%    0.013   181)      // ~#E9F5F2 ← stop 50, near-white teal
min:    oklch(15%    0.025   183)      // ~#010F0C ← stop 950, near-black teal
```

**Notes:** root chosen to align with logo primary so brand-anchor surfaces at stop 500 read as "Cena teal" without drift. Stops 400 and 800 will drift slightly from logo secondary (#52A395) and logo anchor-dark (#0D322D); logo-exact values remain available via `--logo-secondary` and `--logo-anchor-dark` (§5).

### 3.2 sage — paired counterpart

```
root:   oklch(55%    0.085   145.5)    // perceptual middle, hue per cena Principle 1
max:    oklch(95%    0.018   148)      // ~#E7F2E8 ← stop 50
min:    oklch(15%    0.020   148)      // ~#060D07 ← stop 950
```

**Notes:** sage-root is a free choice (logo sage `#81B983` is a light value, not a midpoint). Hue drifts from H:148 at light end to H:145.5 at root (analogous-warmer-at-lighter, per cena Principle 1). Logo sage will drift slightly from computed stop 300; `--logo-sage` holds the exact `#81B983`.

### 3.3 sand — warm neutral (no locks)

```
root:   oklch(65%    0.016   75)       // #958E85  ← Tailwind-direction stop 500
max:    oklch(98.5%  0.003   85)       // #FBFAF8  ← stop 50, page background canonical
min:    oklch(15%    0.008   60)       // #0E0A08  ← stop 950
```

**Curve note:** sand has no locks; default k=2.4 with chroma tapering aggressively at extremes (warm neutral should approach achromatic at both ends). The lightness-compression Aaron flagged in data-vis iter step 18 — sand-50→100→200 small L deltas — is preserved by design; warm-neutral surfaces are designed to vary subtly at the light end.

### 3.4 red — error / critical (no locks; rebrand of v1 error-base)

```
root:   oklch(55%    0.170   25)       // #C13C3B  ← v1 error-base
max:    oklch(96%    0.020   30)       // ~#FCE5E3 ← stop 50
min:    oklch(20%    0.075   23)       // dark red
```

**Hue note:** H:25 keeps red warm-shifted (toward terracotta) per cena Principle 4 — no pure-red (H:0) which clashes with teal.

### 3.5 amber — warning / caution (no locks; rebrand of v1 warning-base)

```
root:   oklch(63%    0.108   82)       // ~#B58B20  ← haven-ui amber-500 hand-tuned
max:    oklch(96%    0.020   85)       // pale ochre tint ← stop 50 (retuned 2026-05-11)
min:    oklch(22%    0.060   78)       // dark amber
```

**Hue note:** H:82 (warm ochre-gold) — shifted away from pure yellow (H:60-65) toward the brand's warm-earth territory.

**Tuning note (2026-05-11):** original max at oklch(94% 0.030 85) read more saturated than peer family-50 stops in Aaron's visual review. Bumped L 94→96 and reduced chroma 0.030→0.020 to bring amber-50 in line with peers (most families use L_max=95-96 and C_max=0.015-0.025 at the lightest tint).

### 3.6 green — success / confirmation (no locks; rebrand of v1 success-base)

```
root:   oklch(55%    0.105   160)      // ~#3A8E64  ← haven-ui green-500 hand-tuned
max:    oklch(96%    0.025   155)      // #DCF0E5  ← stop 50
min:    oklch(20%    0.060   165)      // dark green
```

**Hue note:** H:160 desaturated green — distinct from sage's H:145 (the brand's hue-shift family). Success is utility/feedback; sage is identity.

### 3.7 cyan — info / informational (no locks; rebrand of v1 info-base; warm-shifted per Phase-0 #1)

```
root:   oklch(50%    0.090   235)      // ~#287AA3  ← haven-ui cyan-500 hand-tuned
max:    oklch(94%    0.035   215)      // #DFEEF7  ← stop 50
min:    oklch(20%    0.060   240)      // dark cyan
```

**Hue note:** H:235 slate-blue — Cena's only allowed cool family. Sufficiently distinct from teal (H:181) for info-vs-brand differentiation. Phase-0 #1 warm-shifts; Phase C distinguishability test against sky and blue (close hue neighbors) gates whether this position holds.

---

## 3b. Phase C Family Targets — root, max, min for the remaining 12 families

Initial targets for the build-time generator. Same architecture as Phase A; minor adjustments expected after the generator's first run + Aaron's visual review.

### 3b.1 orange — terracotta accent

```
root:   oklch(58%    0.115   35)        // terracotta mid
max:    oklch(96%    0.025   45)        // pale terracotta wash
min:    oklch(20%    0.065   30)        // dark terracotta
```

**Hue note:** H:35 places orange between red (H:25) and amber (H:82). Absorbs the ochre/earth role from cena-brand v1 §1.4.1. Categorical use: warm-earth, food, material contexts.

### 3b.2 yellow — illumination accent

```
root:   oklch(72%    0.115   95)        // saturated golden yellow
max:    oklch(96%    0.025   90)        // pale cream-yellow
min:    oklch(28%    0.060   100)       // dark olive-yellow
```

**Hue note:** H:95 (10° from amber H:82, 20° from lime H:115). Higher L_root (~72%) than other families because yellow reads bright at saturation; lower L_root would read muddy. Restrained chroma vs Tailwind yellow defaults.

**Tuning note (2026-05-11):** original placement at H:85 was only 3° from amber — Aaron's adjacent-square review confirmed they read as near-twins. Moved to H:95 to give yellow its own hue identity.

### 3b.3 lime — sage-adjacent accent

```
root:   oklch(65%    0.110   115)       // warm yellow-green
max:    oklch(95%    0.025   125)       // pale lime wash
min:    oklch(25%    0.060   110)       // dark lime
```

**Hue note:** H:115 between yellow (H:85) and sage (H:145). Sage-adjacent on the warm side; distinct enough from sage to read as a different family.

### 3b.4 emerald — alt saturated green

```
root:   oklch(56%    0.115   170)       // saturated green
max:    oklch(96%    0.025   165)       // pale emerald wash
min:    oklch(20%    0.065   175)       // dark emerald
```

**Hue note:** H:170 between green (H:160) and teal (H:181). More saturated than success-green. Available for chart series differentiation or secondary success states.

### 3b.5 sky — DROPPED 2026-05-11

Sky was provisionally placed at H:215 (between teal H:181 and cyan H:235). In Phase C visual review (2026-05-11), the sky/blue/cyan family-50 stops read near-identical because Cena's warm-restraint chroma profile (low C at light tints) doesn't support the 10-20° hue distinctions needed to differentiate three families packed into a 20° hue band. Per Phase-0 #1's fallback, sky was dropped.

Sky's token namespace (`--color-sky-*`) is **reserved but not generated.** Any future surface that needs a sky-like slate-blue uses either teal-pale stops (warmer direction) or cyan-pale stops (cooler direction). If a strong use case for sky surfaces in the future, revisit.

### 3b.6 blue — deep slate (post-sky-drop)

```
root:   oklch(48%    0.090   245)       // deep slate-blue
max:    oklch(95%    0.025   240)       // pale blue wash
min:    oklch(18%    0.060   250)       // dark blue-slate
```

**Hue note:** H:245 adjusted from initial H:225 on 2026-05-11. After sky was dropped, blue moved deeper into the cool zone to give ~20° spread from cyan (H:235) and ~20° from indigo (H:265). Blue now anchors the "deep slate" position, distinct from cyan's "info slate-blue" identity at H:235.

**Distinguishability check needed:** blue at H:245 sits close to indigo at H:265. Phase C review verifies the blue→indigo hue step is sufficient. If they merge, blue is also a drop candidate, leaving only cyan (info) + indigo (partner) in the cool zone.

### 3b.7 indigo — external systems / partners (extended)

```
root:   oklch(55%    0.095   265)       // warm blue-violet (Cena §1.4.4)
max:    oklch(95%    0.030   270)       // pale indigo wash
min:    oklch(22%    0.060   260)       // dark indigo
```

**Hue note:** H:265 from cena-brand v1 §1.4.4. Already warm-shifted. Data-vis spec role: partner clinics, vendor integrations, third-party systems.

### 3b.8 violet — data / AI / inference (extended)

```
root:   oklch(55%    0.100   300)       // warm purple (cena-brand §1.4.3)
max:    oklch(95%    0.030   315)       // pale violet wash
min:    oklch(22%    0.065   295)       // dark violet
```

**Hue note:** H:300 from cena-brand v1 §1.4.3. Data-vis spec role: AI inference, ML pipelines, analytical signals.

### 3b.9 purple — alt violet

```
root:   oklch(52%    0.105   285)       // saturated purple
max:    oklch(95%    0.030   295)       // pale purple wash
min:    oklch(20%    0.070   280)       // dark purple
```

**Hue note:** H:285 between violet (H:300) and fuchsia (H:325). Chart accent reserve; no current data-vis role.

### 3b.10 fuchsia — vivid magenta accent

```
root:   oklch(58%    0.130   325)       // saturated magenta
max:    oklch(95%    0.030   335)       // pale fuchsia wash
min:    oklch(22%    0.075   320)       // dark fuchsia
```

**Hue note:** Higher chroma (0.130) for vivid accent — useful for chart series that need to stand out. Use sparingly; conflicts with Cena restraint at large scale.

### 3b.11 pink — warm-shifted pink

```
root:   oklch(60%    0.100   340)       // warm pink
max:    oklch(95%    0.025   348)       // pale pink wash
min:    oklch(22%    0.060   335)       // dark pink
```

**Hue note:** H:340 between fuchsia (H:325) and rose (H:355). Distinct from rose by hue; lighter L_root since pink reads pastel-bright.

### 3b.12 rose — community / relational (extended)

```
root:   oklch(55%    0.100   355)       // warm pink-red (cena-brand §1.4.2)
max:    oklch(95%    0.025   0)         // pale rose wash (hue wraps to H:0)
min:    oklch(22%    0.065   350)       // dark rose
```

**Hue note:** H:355 from cena-brand v1 §1.4.2. The 30° separation from red (H:25) preserves perceptual distance between rose-as-relational and red-as-error. Component categorical use only; not a data-vis anchor (dropped 2026-05-11).

**Hue note:** H:235 slate-blue — Cena's only allowed cool family. Sufficiently distinct from teal (H:181) for info-vs-brand differentiation. The Phase-0 #1 decision warm-shifts the family as a whole; this hue value will be revisited in Phase C if sky/blue's distinguishability test forces a re-position.

---

## 4. Semantic naming layer

The 17 Tailwind family tokens at the base layer (`--color-teal-500`, `--color-amber-300`, etc.) are referenced by **semantic alias tokens** at the consumption layer:

```css
/* Brand — family palette aliases (systematic, may drift slightly from logo hex) */
--color-brand-primary:        var(--color-teal-500);
--color-brand-secondary:      var(--color-teal-400);
--color-brand-anchor:         var(--color-teal-800);
--color-brand-sage:           var(--color-sage-300);

/* Brand — logo identity aliases (hex-exact, for logo SVG and wordmark surfaces) */
--color-logo-primary:         var(--logo-primary);
--color-logo-secondary:       var(--logo-secondary);
--color-logo-anchor-dark:     var(--logo-anchor-dark);
--color-logo-sage:            var(--logo-sage);

/* Surface */
--color-surface-page:         var(--color-sand-50);
--color-surface-primary:      var(--color-sand-100);
--color-surface-secondary:    var(--color-sand-200);

/* Text */
--color-text-primary:         var(--color-teal-800);
--color-text-secondary:       var(--color-sand-700);
--color-text-inverse:         var(--color-sand-50);

/* Feedback */
--color-error-bg:             var(--color-red-50);
--color-error-border:         var(--color-red-400);
--color-error-base:           var(--color-red-500);
--color-error-text:           var(--color-red-700);
--color-warning-bg:           var(--color-amber-50);
--color-warning-border:       var(--color-amber-400);
--color-warning-base:         var(--color-amber-500);
--color-warning-text:         var(--color-amber-700);
--color-success-bg:           var(--color-green-50);
--color-success-border:       var(--color-green-400);
--color-success-base:         var(--color-green-500);
--color-success-text:         var(--color-green-800);
--color-info-bg:              var(--color-cyan-50);
--color-info-border:          var(--color-cyan-400);
--color-info-base:            var(--color-cyan-500);
--color-info-text:            var(--color-cyan-700);
```

Two layers: family tokens at the base (hex-named), semantic tokens at the alias (role-named). Components and surfaces reference semantic tokens. The family token layer changes propagate through the alias layer automatically.

This is the same two-layer model haven-ui already uses today; v2 keeps it intact and clarifies the family ↔ semantic relationship.

---

## 5. Logo identity tokens

Logo identity is a **separate token namespace** from the family palette (per §1.5). These tokens hold the exact hex values for the four logo-derived colors. They are referenced directly by logo SVG, wordmarks, and any surface where the logo identity must render at brand-canonical hex without drift.

```css
--logo-primary:          #3A8478;   /* oklch(56.3% 0.0762 181.3) — outer ring + "health" wordmark */
--logo-secondary:        #52A395;   /* oklch(66%   0.0827 181)   — middle ring */
--logo-anchor-dark:      #0D322D;   /* oklch(28.9% 0.0426 182.8) — "Cena" wordmark */
--logo-sage:             #81B983;   /* oklch(73.3% 0.0967 145.4) — inner ring */
```

**Why separate from family palette:**

- Family palette stops (`--color-teal-*`, `--color-sage-*`) are computed by the default interpolation curve. They are systematic, generative, mechanical. They may drift slightly from the logo hex at the equivalent stop number — that's a feature, not a bug, because the family palette serves a different purpose (a parametrized color ramp for components, charts, surfaces) than the logo identity (one specific brand mark with hex-exact requirements).
- Logo identity tokens are hex-frozen. They never go through the generator. They are not computed from anything; they are the brand's identity.

**Authoring rule:** when a surface must render the logo identity (logo SVG, wordmark text), it references the logo tokens. When a surface uses the family palette systematically (a button using teal, a badge using sage), it references the family palette. The drift between `--color-teal-500` and `--logo-primary` is acceptable in surfaces that mix the two — the family palette stop reads as "Cena teal" by hue, just not pixel-identical to the logo.

**Updates to logo identity** require Aaron's review and a new logo decision. Adding new logo-derived hex values to this namespace is a brand-identity change, not a palette change.

---

## 6. Validation rules

The build-time generator enforces these rules per family; build fails on violation:

### 6.1 Logo identity preservation
- Logo identity tokens (§5) are hex-frozen at the values stated. The generator does not compute them; they are not validated against family palette stops. Per §1.5, family palette stops may drift slightly from logo hex at the equivalent stop number.
- If a surface needs the logo identity, it references `--logo-*` directly. The family palette's drift from logo hex is not a build failure.

### 6.2 Lightness ordering
- L must be strictly monotonic across the 11 stops: L(50) > L(100) > ... > L(950). Reason: scale-direction convention requires light → dark.

### 6.3 Contrast ratios — internal element formula (Tailwind direction: 50=lightest, 950=darkest)
- Canonical light-pill formula (the common case):
  - Fill: family-50 (lightest tint)
  - Border: family-200 (light saturated — provides canvas separation against sand-50 page bg)
  - Heading text on fill: family-500 (saturated mid). Target: ≥4.5:1 (WCAG AA on body text).
  - Body text on fill: family-800 (dark saturated, not extreme). Target: ≥7:1 (WCAG AAA).
- Canonical dark-anchor variant (used sparingly — at most one per diagram per data-visualization.md):
  - Fill: family-800 (NOT family-950 — see §6.7 hue-identity caveat below)
  - Text inverse: warm-neutral-50 (sand-50). Target: ≥7:1.
- Generator emits a contrast report per family; ratios below target fail the build for actively-used families (Phase A active). Phase C families warn but don't fail.

### 6.4 Cross-canvas separation
- Family-50 (lightest tint) vs `color-surface-page` (sand-50): perceptual delta detectable from 24px away. For low-chroma family-50 values that read as the same near-white as the page bg, the family is unsuitable as a light substrate without an explicit family-200 border.
- This matches data-visualization.md §5 "external contrast" rule.

#### Degenerate case — same-family same-surface

When a pill's family equals the surface family AND the surface is the page background, fill-vs-bg distinction collapses by construction:

| Pill family | Surface | Pill fill (family-50) | Surface fill | Distinction |
|---|---|---|---|---|
| sand | sand-50 page bg | sand-50 | sand-50 | **outline only** (border carries the boundary) |
| sand | sand-100 card | sand-50 | sand-100 | natural L delta — pill reads lighter than card |
| sand | sand-200 substrate | sand-50 | sand-200 | natural L delta — pill clearly lighter |
| teal | sand-50 page bg | teal-50 | sand-50 | hue tint + L tint — distinct |
| teal | sand-100 card | teal-50 | sand-100 | hue tint, slight L tint — distinct |

**The single problematic combination — sand pill on sand-50 page bg — is by design.** Sand IS the surface family; sand-50 IS the page-bg color. A sand-50 fill on a sand-50 surface "is of the page" and the border explicitly frames it as a contained element. This is consistent with Cena's surface-tier principle ("Cards sit on surface-primary on surface-page. Add border for more separation.") — sand pills on the page bg use border for separation by design.

**When a sand element needs strong visual lift on the page bg**, promote it to card-tier: use sand-100 fill (one surface step in) instead of sand-50. This is a separate component variant, not a fix to the pill formula.

### 6.5 Family-800 to family-50 contrast
- Within a family, the canonical body-text-on-fill ratio (text family-800 over fill family-50) must be ≥7:1. Otherwise the family fails the canonical light-pill formula and is restricted to non-text-pill use only.

### 6.6 Cool-exclusion principle
- Hue centers in (H:185°, H:235°) range are flagged for review. Hue centers in (H:160°-180°) and (H:235°-265°) are allowed (Cena's slate-blue exception territory). Pure cool blue (H:200-230 at standard saturation) is excluded except where explicitly tagged in §2.1 (sky/blue's warm-shifted cool territory).

### 6.7 Hue identity at extremes — usage guidance

**Observation (Aaron 2026-05-11):** at the lightness extremes (stops 50–100 near max; stops 900–950 near min), chroma is necessarily low — the stops approach near-white and near-black respectively. Hue identity is weak at these stops: dark teal (stop 950) and dark sage (stop 950) read as very similar near-black with subtle tint; light teal (stop 50) and light sage (stop 50) read as very similar near-white. This is not a desaturation problem; it's the perceptual reality of any color system at the extremes.

**Implication for usage:**
- **When hue must communicate** (color-differentiated chips, chart series with categorical meaning, semantic indicators where "this is the teal one" matters) — use **mid-saturation stops** (roughly 200–700). These stops carry clear hue identity.
- **When hue identity is incidental** (text shadow, very subtle borders, large surfaces that fade behind content) — extreme stops (50–100, 900–950) are fine. The hue is felt rather than seen.

**This is a usage guideline, not a generator rule.** The system still emits all 11 stops for every family. The guideline lives at the consumption layer: surface authors choose stops appropriate to their communication intent.

**Component-author rule of thumb:**
| Use case | Stop range |
|---|---|
| Categorical chip / badge fill where hue identifies the category | family-200 fill + family-500 text |
| Categorical chip border alone | family-300 to family-500 |
| Large colored surface where the hue is the message | family-300 to family-700 |
| Pill background (light pill formula) | family-50 fill — accept that this reads as "very pale tint" not "clearly the teal one" |
| Body text on surface | family-700 or family-800 |
| Subtle accent or shadow | family-100 or family-900 (hue weak by design) |

### 6.8 Feedback-surface convention — per-family stops for full-area feedback surfaces

Sand is the canonical surface family (per §4 semantic tokens). The four **feedback families** — `red`, `amber`, `green`, `cyan` — are additionally authorized for *full-area feedback surface* use: a tinted alert region, a callout block, an info panel, a confirmation card. In these cases the hue itself IS the semantic (severity, warning, success, info) and a tinted ground is the right vocabulary.

This convention is narrow by design. **The other 14 families — including teal and sage — are not authorized as full-area tinted surfaces.** Brand identity (teal, sage) and accent families (orange / yellow / lime / emerald / blue / indigo / violet / purple / fuchsia / pink / rose) carry hue at the chip, badge, accent, icon, or chart-categorical layer per §6.3 and §6.7 — never as running-text container.

**Convention (light mode):**

| Role | Stop | Example (red) |
|---|---|---|
| `surface` (background fill) | family-50 | red-50 (`#ffe9e5`) |
| `border` (edge stroke, 1px) | family-300 | red-300 (`#e0857c`) |
| `text-on-surface` default | family-900 | red-900 (`#4a0a0e`) |
| `text-on-surface` muted **+ icon-on-surface** | family-700 | red-700 (`#832224`) |

Icon-on-surface shares the muted-text stop — there is no separate icon token. Icons earn visual weight from glyph mass and stroke (FA-solid), not from a stop step away from body text. This matches the canonical sand-surface pattern in `semantic.css`, where `--color-text-muted` (sand-700) carries icon duty implicitly.

**Dark-mode mirror** (per palette midpoint, matching the sand mirror in `semantic.css`'s `.dark` block):

| Role | Stop |
|---|---|
| `surface` | family-900 |
| `border` | family-700 |
| `text-on-surface` default | family-50 |
| `text-on-surface` muted + icon-on-surface | family-300 |

**Visual companion:** [`Lab/cena-health-brand/specs/color-family-surface-spec.svg`](../specs/color-family-surface-spec.svg) renders this convention for the four feedback families.

**Relationship to §6.3 (light-pill formula).** §6.3's formula is for *inline pills, chips, and badges* — small elements with hue-as-category-signal. It uses a lighter border (family-200), saturated mid heading (family-500), and dark body text (family-800). §6.8's convention is for *full-area feedback surfaces* — larger regions with hue-as-semantic-signal, holding running text and icons. It uses a darker border (family-300) for clearer canvas edge at larger scale, and dark/muted text stops (family-900 / family-700) that match the canonical sand surface treatment in `semantic.css`. Both conventions are valid; component authors pick the one matching their element's scale and intent.

**Anti-pattern — tinted-identity surface (teal, sage) as "brand-anchor ground."** Brand-anchor moments use teal text, a teal accent rule, or a teal icon on a sand surface (per §4 semantic tokens) — never teal-tinted ground. The infrastructure-feels-grown mechanism requires warm ground; tinted-teal ground breaks Warm Ground / Cool Figure (Principle 3). Teal is figure, never ground. The same prohibition holds for sage and the 12 accent families: hue acts on figure, not on ground.

**Authoring rule.** When a component needs a tinted background AND the hue is one of red / amber / green / cyan AND the hue carries a feedback semantic — reach for this convention. Anywhere else, reach for the canonical sand surface (per §4) with accent stops on figures (per §6.3, §6.7). When in doubt, sand-surface is the safe move.

<!-- @Aaron: §6.8 absorbed Haven Visual Designer review 2026-06-06 — narrowed scope (4 feedback families, not 18), collapsed icon stop to muted-text stop, added tinted-identity-surface anti-pattern paragraph. Border stop family-300 confirmed. Disposition trail in ~/.claude/plans/color-system-v2-tinted-surface-section.md § Review trail. -->


---

## 7. Validation gate — Aaron review

This spec moves to LOCKED status after Aaron review covers:

1. **Architecture model (§1)** — root/max/min + interpolation + locked-stop constraint approach reads as workable
2. **Family set (§2)** — 17-family list + 1 neutral matches intent; cool-shift territory for sky/blue/cyan is acceptable
3. **Phase A targets (§3)** — initial OKLCH triplets for the 7 active families are reasonable starting points (Phase B refines)
4. **Semantic layer (§4)** — feedback family names (red/amber/green/cyan) replacing v1 role-named families (error/warning/success/info) at the base layer is OK; semantic aliases preserve role-based references
5. **Locked-stop registry (§5)** — only four logo-derived locks at Phase A is correct
6. **Validation rules (§6)** — gates and thresholds match brand intent

Once locked, Phase B begins: build the generator, fit the families, emit the token files.

---

## 8. Migration plan (high level)

Phase A (this doc): spec authored, awaiting Aaron review.

Phase B: build-time generator implementation. Outputs the 17-family palette in OKLCH-in-P3 + hex fallback. Validates against §5 registry and §6 rules.

Phase C: extend to all 17 families. Author root/max/min for the 10 not-yet-active families. Run sky/blue/cyan distinguishability test.

Phase D: haven-ui integration. Replace `palette.css` with generated output. Re-map `semantic.css` `.dark` overrides post-scale-direction-flip (this is when the dark/light theme audit Aaron flagged happens). Rebaseline Playwright visual fixtures.

Phase E: downstream propagation. Audit pattern-library + React-port + apps for hand-coded hex; replace with tokens.

Phase F: data-vis spec lock. Return to `data-visualization.md` (Aaron's in-flight effort); verify the anchor set + restraint constraints work cleanly against the new family set; lock.

Each phase has its own ambient plan or is captured under `~/.claude/plans/cena-color-system-v2.md`.

---

## 9. What v2 does NOT change

- **Logo hex values** — preserved exactly (§5).
- **Cena Health visual identity** — warm-ground / cool-figure, restraint, hue shift as brand signature, no pure white, no cool gray. v2 enforces these structurally rather than by convention.
- **Existing semantic tokens** — `--color-brand-primary`, `--color-text-primary`, `--color-error-base`, etc. retain their names. Only the underlying family tokens shift.
- **Tailwind scale direction in haven-ui** — already canonical there; cena-brand flips to match.
- **Pattern library, components.css, React ports** — class names and structure preserved. Hex values shift slightly as the new palette generates.

---

## 10. Open considerations (not blocking Phase A approval)

1. **Sage's role provisional flag** (per data-visualization.md §5) carries forward — v2 doesn't resolve sage's structural role debate; it just provides the palette infrastructure.
2. **Rose family** is kept in v2 despite being dropped as a data-vis anchor (data-visualization.md, 2026-05-11). Rose is available for component categorical accents and chart use — the spec layer doesn't restrict it, the consumption-layer documentation does.
3. **Distinguishability test for cyan/sky/blue** — Phase C must verify the three warm-shifted close-neighbor families remain perceptually distinguishable. If they fail, the system retreats to 14 of 17 (drop sky and cyan, keep blue at H:235 as the only cool family).
4. **Build-time generator's exact runtime stack** — Node + custom script, or PostCSS plugin, or Style Dictionary, or something else — TBD in Phase B. The spec is implementation-agnostic; any tool that reads the family-definition source and emits OKLCH + hex tokens satisfies the contract.

---

## 11. Relationship to v1 (`color.md`, `color-web.md`)

v1 documents remain in `_tokens/` for historical reference. After Aaron approves v2:

- `color.md`: marked as "superseded by color-system-v2.md" in a frontmatter / preamble note. Content preserved for design-history reference.
- `color-web.md`: same. The OKLCH browser-support table inside it (§1) remains useful supporting reference; the rest is v1-tied.

v2 supersedes both as the canonical color-system spec. New consumers reference v2 only.
