# Haven data-visualization palette — visual treatment

**Status:** v1 landed 2026-07-22 from the Haven Visual Designer's ratified rationale (the "why this looks the way it does" layer). Prose refinement is an HVD follow-up; the decisions and their rationale are settled.

**Define-once boundary.** Token *values* live once in `Lab/haven-ui/packages/design-system/src/styles/tokens/semantic.css` (`--viz-*`) — this spec **references** them and never copies a hex. The **cap policy + register guardrails** live in [haven-ui DESIGN.md §Color → Data-visualization palette](../../haven-ui/DESIGN.md); the **binding accessibility conditions** in [haven-ui `.project-docs/dataviz-palette-a11y-conditions.md`](../../haven-ui/.project-docs/dataviz-palette-a11y-conditions.md). This spec owns only the visual rationale.

## The warm-ground rationale — why the marks stay muted

The load-bearing "why" a future agent or reviewer needs when tempted to saturate a chart series: **Cena's charts draw from the same muted, low-chroma brand ramps as the rest of the system — deliberately.** A chart is a data surface, the quietest and most utilitarian register in the brand; it is the *last* place to spend a saturation budget. A punched-up 8-hue analytics palette is precisely the "any other dashboard" look the brand exists to not be (Tier-2 identity rule #6: candy-colored tints are an identity violation).

This is Principle 3 (Warm Ground, Cool Figure) and Principle 6 (Restraint as default) applied to data: the **warmth comes from the ground, not the marks.** The sand-50 light surface and the chromatic teal-900 dark surface carry the warmth; the marks stay quiet and identity is carried by **secondary encoding** (direct labels + legend, texture/dash/marker where color is stripped). The most diagnostic quality test — "grew, not built" — is why the muted marks are correct: answering a "correct but cold" chart with more saturation is the exact move the ledger names as producing correct-but-cold. The fix for a flat chart is a stronger hero and more warm ground, never a louder hue.

## Per-role visual treatment

- **Categorical ordering is the brand signature, not cosmetic.** Teal (`--viz-cat-1`, institutional) and sage (`--viz-cat-4`, organic) **bookend** the set on purpose, so the categorical range frames itself with the brand's 37° teal→sage hue-shift (Principle 1). Orange/terracotta (warm earth) and violet (the v2 "data/AI/inference" family) are the two accent poles between. The order also maximizes the minimum adjacent CVD separation — it is the accessibility-safety mechanism, so it never changes.
- **Data-teal ≠ commit-teal, structurally.** Chart teal is drawn from the **data step** (`--viz-cat-1` = teal-600 light / teal-400 dark), deliberately NOT the reserved commit step teal-700 (`--color-primary`). The data register and the commit register are literally different tokens — a quiet structural reinforcement of the teal-commit boundary (digest #1), not a rule anyone has to remember.
- **Terracotta as the diverging warm pole (over rose).** The diverging scale is teal ↔ terracotta-orange over a warm **sand** midpoint — Warm-Ground/Cool-Figure rendered *as a scale*, where the neutral genuinely reads as *absence* because it is the page's own ground. Terracotta is the higher-contrast, more unambiguously *warm* opposite of teal; rose was rejected as too cool-adjacent.
- **Chromatic teal-dark chart surface (over neutral gray).** The dark chart surface is teal-900 (`--viz-surface` dark), not a cool gray — Principle 3's correct dark inversion. A cool-gray surface would be foreign matter in Cena (cool-gray neutrals are a named anti-pattern). The plane behind sits at teal-950 so the chart floats a hair above its ground (surface-shift before shadows, brand rule #4).

## The C-5a re-step rationale

The one dark hue change: dark `--viz-cat-2` = orange-500 and `--viz-cat-3` = violet-500 (light is untouched). The Accessibility gate found the original dark violet↔orange pair near-isoluminant (adjacent normal-floor 13.6 < the 15 hard gate) and mandated a re-step. Among three valid in-band re-steps, the chosen one buys **CVD 9.4 headroom** (well above the 8 target) where it is cheap — the muted palette's real structural risk is CVD collapse, so spending a single in-ramp step on colorblind headroom is Principle-8 (accessibility is load-bearing structure). No restraint break: a deeper terracotta + violet on the chromatic teal-dark ground read as *richer earth*, not candy (dark grounds absorb saturation).

## Chrome / ink pinning (warm — no cool gray)

Chart chrome is warm throughout: gridlines from sand-200 (light) / teal-800 (dark), axis + muted ink from sand-600, primary ink from sand-900 (light) / sand-50 (dark). Cool-gray chart chrome is the anti-pattern; the page-plane/surface separation is a value shift, never a shadow.

## Forced-colors / print fidelity

Mirrors the print-form spec's `§print-fidelity` precedent (standing case law since 2026-07-03): under `forced-colors: active` and print/grayscale, the muted hues **vanish** — so **texture (fills) / dash (lines) / marker (points) becomes the identity channel and must be authored, not decorative.** Texture is slot-ordered (fixed slot→angle mapping, 45°/135° only), tone-on-tone, equal loudness across slots, and respects reduced-motion. This is the visual-treatment half of accessibility conditions C-6a and C-3-forced; the muted-palette bet is only honest because this channel carries identity where hue can't.

## Source
2026-07-22 palette codification (HVD verdict `~/.claude/staging/2026-07-22-haven-dataviz-palette/verdict-haven-visual-designer.md`). Rendered proof: the staged `swatch-preview.html`. Ambient plan: `~/.claude/plans/haven-dataviz-palette.md`.
