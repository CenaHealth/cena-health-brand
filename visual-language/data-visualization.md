# Cena Health — Data Visualization System

_Design specification for the Cena Health data visualization language — system diagrams, architecture diagrams, process flows, integration maps, and other schematic visuals that are neither charts (numeric data) nor illustrations (narrative scenes). Builds on iconography.md's drawing language and illustration.md §5.5's adjacency without duplicating either._

**Stop-number direction (Cena Color System v2, 2026-05-11):** all family-stop references in this document use Tailwind direction (50 = lightest tint, 950 = darkest tint). This is consistent with `_tokens/color-system-v2.md`. References authored under the earlier inverted convention (50 = darkest) were swept on 2026-05-11.

---

## 0. Why This System Exists Separately

The brand has three diagrammatic registers that share a visual world but serve different purposes. Confusing them produces work that fails because it is being judged against a different system's bar.

| System | Purpose | Authority | Examples |
|---|---|---|---|
| **Charts** | Quantitative data — comparisons, distributions, time series | Chart.js or equivalent; defaults defined by haven-ui chart config (not in this document) | Bar charts, line charts, stat-card numeric displays |
| **Illustration §5.5 — Diagram Illustrations** | Narrative depiction of process or relationship — "what happens" with people, food, devices, care | illustration.md §5.5 | Care-pathway scenes with spot-illustration nodes connected by flowing curves; the food-as-medicine delivery chain told through illustrated moments |
| **Data Visualization (this spec)** | Schematic depiction of system structure — architecture, integration topology, process flow, conceptual map | This document | Platform architecture with substrate + surfaces + arrows; sequence flows with steps and decision points; integration maps |

The category is decided by the content's _grain_, not its shape:
- If the artifact's job is to show a **story** with people or specific moments, use illustration §5.5.
- If the artifact's job is to show a **structure** of components and their relationships, use this spec.
- If the artifact's job is to show **numeric values**, use charts.

Hybrid forms exist — an integration map that names a partner clinic by photographic logo, a chart whose bars sit inside an illustrated container — but each region of the hybrid is governed by the system its content type belongs to. The systems do not blend mid-artifact.

This document closes the gap that opened when haven-ui shipped its `diagram-*` primitives without an authority to point to. Until this exists, every implementation choice is judged against either iconography.md (which governs icons, not diagrams) or illustration.md §5.5 (which prohibits the box-and-arrow form). Both judgments find the work wanting because the work is neither.

---

## 1. Style Definition

### Visual Character

Cena Health data visualizations are drawn with iconography's hand and arranged with infrastructure's logic. They share the icon system's variable stroke weight, round terminals, and organic curve quality. They share the spacing system's proportional restraint. They depart from illustration's compositional freedom — diagrams obey their structure. They depart from clip-art schematic conventions — they do not use uniform stroke weight, sharp corners, or mechanical symmetry.

The result reads as **architecture rendered by a thoughtful technical illustrator**, not as either software-generated flowchart output or hand-drawn brainstorm. The viewer should feel: "someone made this on purpose, and the structure is real."

### Specific visual qualities

- **Strokes carry weight, fills carry mass.** Connectors and shape contours use the iconography stroke system. Node fills use the surface token family — warm-on-warm tonal lift creates structure without hard edges. The 2.5:1 stroke ratio (this document, §3) is what makes the diagram feel hand-placed rather than uniformly drawn.
- **Curves over angles, but rectangles for nodes.** Connector paths flow with curvature where space allows; orthogonal routing is permitted when many connectors must share alignment. Nodes are rounded rectangles (radius-sm minimum), never sharp-cornered, never circular except for state-dot atoms (status indicators within a node).
- **Hierarchy through accumulated differentiation.** A primary connector is heavier in stroke AND warmer in color AND visually higher in the composition AND, where labeled, given more typographic emphasis. Per design-principles.md Principle 2 ("Weight and Value Move Together"), at least two properties change per hierarchy level.
- **Quiet by default; expression earned.** Most nodes use default surface fills and 1.5px connectors. Emphasis nodes and emphasis paths are reserved for the diagram's most important argument. A diagram where every node has equal emphasis has no emphasis at all.
- **Specific, not generic.** Just as illustration depicts a particular kitchen (illustration.md §5.2), data visualization depicts a particular system. A generic three-tier architecture rendered against this system reads as wrong because it is not particularly Cena Health's three-tier architecture. The diagram earns its place by showing real components labeled in real terms.

### What this system is not

- Not flat, uniform, or schematic-clip-art. Diagrams that could have been generated by a flowchart tool with default styles read as work that nobody made. This system requires authored decisions about hierarchy, spacing, and emphasis.
- Not narrative. Diagrams do not depict moments, characters, or stories. If the artifact wants to say "Maria's care pathway begins here," it is illustration §5.5, not this system.
- Not maximalist. Information density is governed (§6). A diagram with 40 nodes and 80 connectors is not "comprehensive" — it is unreadable. Diagrams that exceed the information-density threshold are decomposed into a sequence of smaller diagrams, each with one argument.
- Not decorative. Every shape, line, and label must carry meaning. Color zones, surface tints, and stroke variations are reserved for hierarchy and grouping; they are not used to "make the diagram more interesting." Per design-principles.md §5 anti-pattern: "Illustration as Wallpaper" applies here too.
- Not illustrative-hybrid. Spot illustrations do not appear inside diagrams. If the artifact wants illustrated elements, it is illustration §5.5 (which uses spot-illustration nodes by design). The two systems remain distinct.

---

## 2. Primitives

Diagrams compose from a small primitive vocabulary. Each primitive has a defined role and a defined relationship to surrounding primitives.

### 2.0 Text-in-container convention (cross-cutting)

Status: locked Aaron 2026-05-10.

Every text element that lives **inside** a primitive's bounded surface (node labels, substrate headings, pill labels, milestone names, lane labels) is authored as HTML inside an SVG `<foreignObject>`, NOT as an SVG `<text>` element. This is the canonical convention for all text-in-container primitives in this spec.

**Rationale.** Diagrams must reliably contain and wrap text. Content length is not knowable in advance — system names, partner-org descriptions, lab identifiers, future-tense use cases. SVG `<text>` has no auto-layout: it doesn't wrap, doesn't clip at parent rect boundaries, doesn't respect padding, and silently overflows past container edges. `<foreignObject>` with HTML inside gets CSS's full layout system — line-wrap, padding, flex, text-align, all of it — for the cost of one extra wrapper element per text block.

**Template** (substrate-style box with overline + heading + sublabel + pill):

```svg
<svg viewBox="0 0 760 160" role="img" aria-label="...">
  <rect x="40" y="20" width="680" height="120" rx="5" fill="..." />
  <foreignObject x="40" y="20" width="680" height="120">
    <div xmlns="http://www.w3.org/1999/xhtml"
         style="width:100%;height:100%;padding:18px 24px;
                display:flex;flex-direction:column;
                font-family:'Source Sans 3',sans-serif;color:var(--color-text-primary);
                box-sizing:border-box;">
      <div style="display:flex;justify-content:space-between;align-items:flex-start;">
        <div class="diagram-overline">SUBSTRATE</div>
        <div class="diagram-pill">CENA-OWNED</div>
      </div>
      <div class="diagram-heading">cena-corpus + ava-brain</div>
      <div class="diagram-sublabel">files in git, audit-trailed, governance-aware</div>
    </div>
  </foreignObject>
</svg>
```

Notes on the template:
- `<foreignObject>` dimensions match the parent rect exactly. Padding handled inside via CSS.
- `xmlns="http://www.w3.org/1999/xhtml"` on the inner `<div>` is required for the HTML namespace to take effect inside SVG.
- Use CSS classes (`.diagram-overline`, `.diagram-heading`, `.diagram-sublabel`, `.diagram-pill`) for typography and color. Inline `style=""` only for layout primitives (`display`, `flex-direction`, `padding`, `width`, `height`) and only when class-based layout isn't expressive enough.
- The inner div uses `box-sizing: border-box` so padding lands inside the SVG rect, not outside.

**Trade-off acknowledged.** `<foreignObject>` rendering is reliable in all modern browsers (Chrome, Firefox, Safari, Edge) but degrades in some SVG-to-PNG / SVG-to-PDF conversion tools (rsvg, librsvg, certain image-embedders). Diagrams produced under this convention are consumed by: haven-ui app rendering, vault HTML rendering, slide-deck rendering via browser screenshot. None of those failure modes apply.

**Revisit trigger.** If a use case emerges where Cena diagrams must export reliably to PNG/PDF via non-browser tooling (a client-facing PDF pipeline, an automated raster export for emails), the hybrid fallback is documented in §10 implementation notes: use SVG `<text>` for fixed-size single-line labels (pill text, milestone names), `<foreignObject>` only where wrap is required.

**Legacy SVG-text usage.** The canonical `diagram-box.html` substrate variant in haven-ui currently uses SVG `<text>` (pre-decision). Migration to `<foreignObject>` is a separate task. Until migrated, the legacy path must follow the haven-ui convention: positioning attributes (`text-anchor`, `dominant-baseline`, `x`, `y`) live in markup, never in CSS class rules — the 2026-05-10 substrate bug (commit `7f2a086`) was caused by CSS overriding presentation attributes and is the canonical example of why.

**Iteration record.** See haven-ui pattern-library page `pages/diagram-text-iter.html` for the side-by-side comparison that led to this decision, plus the trade-offs table and stress test.

### 2.1 Frame

The outer container that holds the diagram. Defines viewport, aspect ratio, and the surface the diagram sits on.

| Property | Specification |
|---|---|
| Surface | `surface-page` (`#FBFAF8`) by default. `surface-primary` (`#F3F1EE`) when the diagram is contained within a card or panel. Never pure white, never cool gray — see design-principles.md §5 anti-patterns. |
| Aspect ratio | Three named slots: **landscape** (~16:9, ~760×460 at 1x), **wide** (~21:9, ~760×360), **timeline** (~21:6, ~760×200). Custom aspects permitted when the diagram's content dictates them. |
| Padding | `space-6` (24px) inside the frame. Content does not touch the frame edge. |
| Border | Generally none — the frame is implicit, defined by content boundaries. When containment must be made explicit (a diagram embedded in a tightly-packed page), use a 1px stroke in `color-border-default` with `radius-md`. |

### 2.2 Node (the labeled rectangle)

The default surface element. Carries a label, optionally a sublabel, optionally an inline pill or icon. Nodes are the "things" in the diagram — services, components, steps, roles, regions of responsibility.

| Property | Specification |
|---|---|
| Shape | Rounded rectangle. Corner radius `radius-sm` (4px) at standard size, scaling with node dimensions. Never circular. Never sharp-cornered. |
| Default fill | `surface-primary` (warm-100, `#F3F1EE`) — lifted one tonal step above page surface. This creates a subtle figure-on-warm-ground relationship without using hard contrast. |
| Default stroke | `color-border-default` (warm-300, `#D1CDC6`) at 1px — the accent stroke weight (§3). The stroke makes the node legible even when fill-vs-page contrast is at the edge of perceptibility. |
| Default label | Source Sans 3 SemiBold at 12px (web), `color-text-primary` (`#0D322D`). |
| Default sublabel | Source Sans 3 Regular at 10px, `color-text-secondary` (`#5B544C`). |
| Minimum size | 80×40px. Below this size, switch to a pill (§2.3) or to icon-with-caption. |
| Density | One label, optional one sublabel. Three lines of content is the cap; beyond that, the node is a different primitive (a card region) and belongs to a different system. |

### 2.3 Pill (the inline chip)

Small standalone chip carrying a tag, a category label, or a short identifier. Used inside a node when annotating it, used adjacent to a node when introducing it, used as a freestanding marker when grouping.

#### Geometry & typography

| Property | Specification |
|---|---|
| Shape | Pill — `radius-full` on the short axis. |
| Stroke weight | 1px (accent stroke per §3) |
| Default label | Source Sans 3 SemiBold at 10px, uppercase, letter-spacing 0.05em |
| Mono label variant | Source Code Pro Regular at 10px, no uppercase. For technical labels — component names, version strings, API endpoints. |
| Use | One pill = one short label. Pills do not stack vertically; for multi-tag composition, use a horizontal pill row with `space-2` separation. |

#### Color variants

Pills carry categorical color per §5. Each variant follows the canonical badge formula (per §5 contrast formulas): fill = family-50 (lightest tint), border = family-200 (light/pale), text = family-500 (saturated mid). The warm-neutral default substitutes family-500 for the border per the lightness-compression caveat in §5.

| Variant | Fill | Border | Label | Use |
|---|---|---|---|---|
| `default` (warm-neutral) | `color-surface-primary` (warm-100, `#F3F1EE`) | `warm-500` (`#958E85`) | `color-text-secondary` (`#5B544C`) | Unmarked / no-categorical-association tags. The diagram's quiet baseline. Border uses warm-500 (not warm-200) per lightness-compression caveat — warm family's 50→200 gap is too small for a visible border at 200. |
| `teal` | `teal-50` (`#E9F5F2`) | `teal-200` (`#A8D1C9`) | `teal-500` (`#3A8478`) | Default-authoritative tags — Cena platform, infrastructure (Cena overlay). |
| `sage` | `sage-50` (`#E7F2E8`) | `sage-200` (`#ACCFAD`) | `sage-500` (`#507F53`) | Secondary-anchor tags (Cena overlay: patient, nutrition, care-receiver). |
| `ochre` | `ochre-950` (`#F4EDE6`) | `ochre-800` (`#D9BEAC`) | `ochre-500` (`#9E603C`) | Material — concrete artifacts, physical/tangible elements (Cena overlay: food materials, ingredients). |
| `violet` | `violet-50` (`#F2ECF5`) | `violet-200` (`#CDBDDA`) | `violet-500` (`#7F62A0`) | Data — information layer, analytical pipelines (Cena overlay: AVA / AI inference, ML signals). |
| `indigo` | `indigo-50` (`#ECEEF7`) | `indigo-200` (`#BAC3DF`) | `indigo-500` (`#5670A9`) | External — systems outside the focal system (Cena overlay: partner clinics, vendor integrations). |
| `surface-dark` | `rgba(255, 255, 255, 0.18)` | `rgba(255, 255, 255, 0.4)` | `color-text-inverse` (`#FBFAF8`) | For use inside dark substrate (`color-brand-anchor`). |
| `mono` | Inherits the pill's color variant | Inherits | Inherits (in Source Code Pro) | Modifier — apply alongside any color variant when the label is technical (e.g., `mono` + `indigo` for a vendor's API endpoint name). |

This pattern aligns with the canonical `.badge-*` family in haven-ui (`packages/design-system/src/styles/tokens/components.css`); diagram pills consume the same visual treatment as standalone badges. *Revised 2026-05-11 from previous fill-800/stroke-500/text-primary formula, which read too heavy and was inconsistent with the established badge aesthetic.*

#### Restraint

Per §5 restraint rules: a single diagram uses pills from at most the diagram's chosen color anchors (max 4 anchors per diagram). A diagram with teal + sage + warm-neutral anchors uses default-, teal-, and sage-pill variants — not ochre/violet/indigo, even if a designer is tempted to "add a hint" with one of those.

### 2.4 Connector (the directed path)

The relationship between two nodes. Direction, weight, and curvature express the kind of relationship.

#### Stroke

| Property | Specification |
|---|---|
| Default stroke | `color-arrow-default` (warm-500, `#958E85`) at the secondary stroke weight (§3, 1.5px). Cap and join: round, exclusively. |
| Variants | **emphasis** (primary stroke weight + brand-primary color — reserved for the diagram's most important relationship; never more than 25% of connectors are emphasis); **muted** (accent stroke weight + warm-600 color — for secondary or implied relationships); **dashed** (stroke-dasharray 4 3 — for planned, conditional, or "if/then" relationships); **dotted** (stroke-dasharray 1 3 — for very-low-priority or aspirational connections). |
| Arrowheads | Required at the endpoint of every directed connector. Markers follow §4 rules. Bidirectional arrows mark both endpoints with the same marker shape, indicating mutual relationship. |

#### Geometry — three permitted forms

| Form | Use | Quality bar |
|---|---|---|
| **Orthogonal** | Grid-aligned topologies where many connectors share alignment lanes. The default for system-architecture and integration-map diagrams. | Right-angled segments with **rounded elbow corners** (large-radius arcs at junctions, not sharp 90° intersections). Elbow radius is approximately `radius-md` (8px) at standard scale. The rounded elbow is what differentiates Cena's orthogonal connectors from default flowchart-tool output. |
| **Straight** | Single line segment between adjacent nodes when no routing is needed. | Single segment, axis-aligned where possible, terminated by a marker at the destination. |
| **Curved** | Connectors with significant horizontal-or-vertical run between source and destination, where space allows expressive routing. | See "Curve quality" below. |

#### Curve quality — the load-bearing detail

This subsection applies **only to connecting lines between diagram elements** — the directed paths joining nodes. It does not govern other line types within or adjacent to diagrams (illustrative lines, decorative motifs, axis indicators), which are out of scope for this spec until connector geometry is brand-validated.

A curved connector is **not a default Bézier between two endpoints with handles at the midpoint.** The default-Bézier shape reads as accidental rather than authored — drawn by someone without intentional pen-tool control.

The Cena curve quality is closer to **FigJam's diagram-line treatment**: connectors that are predominantly orthogonal (segments running along clear axes) with **graceful curved transitions at the elbows and endpoints** rather than uniform curvature throughout. The connector reads as deliberate routing — the path took a specific shape because the geometry of the situation called for it, not because it was the path of least drawing effort.

Specifically, a Cena curved connector:

- **Begins and ends with a tangent that is perpendicular to the source/destination node's edge.** A connector exiting the right edge of a source node leaves rightward; it does not exit at a diagonal toward its destination. This produces clean attachment, not accidental geometry.
- **Uses curve only where curve serves the routing.** A connector traveling rightward then downward to a node below-and-right has two segments — a horizontal run and a vertical run — joined by a curved corner of approximately `radius-md` (8px) radius. The horizontal and vertical runs are straight; the corner is curved.
- **Avoids S-curves and serpentine paths** unless the connector legitimately must reverse direction. A connector that curves up then down then up to "look organic" is decoration, not routing.
- **Maintains consistent corner radius across all corners in a single diagram.** Mixing 8px and 16px elbow radii within the same diagram fragments the visual rhythm.

> **Connector geometry needs a designer review before this section is locked.** Aaron has flagged that earlier iterations' curves felt accidental ("default Bézier, not pen-tool authored") and offered to provide FigJam line examples for reference. The language above is the policy direction; the precise geometry (elbow-radius value, tangent-attachment math, S-curve edge cases) needs a designer to author exemplar connectors that the brand team validates before the spec is considered locked. The goal Aaron named: **demonstrate that the system can draw nice brand-aligned connectors as a first step**, before extending the spec to other line types or visual elaborations. Until exemplars exist, implementations should err on the orthogonal side — orthogonal-with-rounded-elbows is the safer default than free curves.

#### What to avoid

- Connectors that cross other connectors when an alternative routing exists.
- Connectors at angles other than 0°/45°/90° (the eye reads off-axis lines as accidental).
- Curves with handles that produce visible "sag" or "bulge" — the curve should feel taut, not loose.
- Different curve treatments within a single diagram (some orthogonal, some smooth-Bézier, some hand-curved). Pick one geometric language per diagram.

#### Locked primitive geometry — orthogonal-with-quarter-arc form

Canonical primitive data for the orthogonal connector form, locked via the haven-ui iteration ground (variant B, 2026-05-10). See implementation note in §10 for the live iteration page.

**Status**: GEOMETRY locked for the orthogonal-with-quarter-arc form. Color and stroke variants inherit from the §2.4 stroke spec above. The broader curve-treatment policy (free curves, S-curves, expressive routing) remains pending designer review per the curve-quality callout above.

**Geometric ratios** — scale-invariant, expressed in units of stroke-width `W`:

| Element | Ratio | At standard scale (W = 1.5px) |
|---|---|---|
| Connector body stroke-width | `W` | 1.5px |
| Elbow corner radius | per existing spec, `radius-md` | 8px |
| Chevron marker width | `4 × W` | 6px |
| Chevron marker height | `8.5 × W` | 12.75px |
| Chevron opening angle | ~94° (atan(4.25/4) ≈ 47° per flank) | — |
| Start dot radius | `1.5 × W` | 2.25px |
| Marker refX | `0` (chevron extends past path endpoint) | — |
| Marker refY | half chevron height (`4.25 × W`) | — |

**SVG marker template** — open stroked chevron with round caps. The chevron is open (no fill, no closing path); tip shaped by `stroke-linejoin="round"`, back-flank ends by `stroke-linecap="round"`. Reads as "the line bent twice," not a constructed arrowhead.

```svg
<marker id="connector-arrowhead"
        viewBox="0 0 [4W] [8.5W]"
        refX="0" refY="[4.25W]"
        markerWidth="[4W]" markerHeight="[8.5W]"
        markerUnits="userSpaceOnUse"
        orient="auto">
  <path d="M 0 0 L [4W] [4.25W] L 0 [8.5W]"
        fill="none"
        stroke="[color-arrow-default]"
        stroke-width="[W]"
        stroke-linecap="round"
        stroke-linejoin="round" />
</marker>
```

**SVG body template** — orthogonal segments with quarter-arc corners:

```svg
<path d="M [start] L [horizontal-run] A [r] [r] 0 0 [sweep] [corner-end] L [vertical-run] ... L [endpoint]"
      fill="none"
      stroke="[color-arrow-default]"
      stroke-width="[W]"
      stroke-linecap="round"
      stroke-linejoin="round"
      marker-end="url(#connector-arrowhead)" />
```

Where `r` is the elbow corner radius (`radius-md`) and `[sweep]` is `0` or `1` per the SVG arc-sweep convention.

**Start dot** — filled circle at the path origin:

```svg
<circle cx="[start.x]" cy="[start.y]" r="[1.5W]" fill="[color-arrow-default]" />
```

**Why `refX="0"` rather than the conventional refX=tip**: with `stroke-linecap="round"` on the body, a refX-at-tip placement causes the body's round cap to protrude past the chevron tip (the cap extends `0.5W` beyond the geometric endpoint). `refX="0"` places the chevron's back-flanks at the body endpoint and the tip `4W` further in path direction. The body line terminates at the chevron's mouth; the chevron is the visual destination beyond it.

**Round caps and joins everywhere** — both body and marker use `stroke-linecap="round"` and `stroke-linejoin="round"`. This is what makes the chevron read as a continuation of the line.

**Color caveat**: the haven-ui iteration ground rendered with `#D8CEC0` (sand-250) verbatim from the FigJam geometry reference. Production implementations should use the spec colors per the §2.4 stroke table (`color-arrow-default` = warm-500 = `#958E85` for default), NOT the iteration's reference color.

### 2.5 Substrate (the grouping region)

A filled background region that contains multiple nodes, expressing "these elements share a context." Substrates are how the diagram says "this is the platform layer," "this is the partner-clinic boundary," "this is the patient device."

| Property | Specification |
|---|---|
| Shape | Rounded rectangle, `radius-md` (8px). Substrates are larger than nodes; the slightly larger radius differentiates them as containers. |
| Color logic | Two named contexts: **institutional** uses `surface-teal` (teal-50, `#E9F5F2`) for light-tinted substrate, or `color-brand-anchor` (teal-800, `#0D322D`) for dark-tinted substrate (use sparingly, at most one dark substrate per diagram). **Patient/nutrition** uses `surface-sage` (sage-50, `#E7F2E8`) for light-tinted, `sage-300` (`#81B983`) for accent-tinted (rare). |
| Stroke | **Light substrates**: 1px family-200 border per §5 contrast-formulas external-separation rule — defines the rect edge so light tints don't dissolve into canvas. **Dark anchor substrate**: no stroke; dark fill self-separates from canvas. Revised 2026-05-11 from previous "none for filled substrates" rule; light tints in ochre/violet/indigo families failed canvas-separation without an explicit border. |
| Label | Optional region label — Source Sans 3 SemiBold at 11px, uppercase, letter-spacing 0.18em, `color-text-secondary`. Positioned at the substrate's top-left or top-center. Treats the label as a section header for the contained nodes. |
| Density rule | A substrate must contain at least 2 nodes (otherwise it adds no grouping). It must not exceed 7 nodes (beyond that, the grouping has fragmented and needs decomposition). |

### 2.6 Lane (the labeled axis)

A horizontal or vertical channel that gives the diagram an explicit axis — "this is the time progression," "this is the data flow direction," "this is the tier hierarchy." Lanes label structure that would otherwise be implicit.

| Property | Specification |
|---|---|
| Shape | Optional outline — dashed border at `color-border-default`, `radius-md`, sand-900 fill at low opacity. The outline is permitted but not required; an unboxed lane label is the lighter form. |
| Label | Source Sans 3 SemiBold at 10px, uppercase, letter-spacing 0.18em, `color-text-secondary`. Positioned at the lane's leading edge (left for horizontal, top for vertical). |
| Use | At most two lanes per diagram (one horizontal axis + one vertical axis). More lanes turn the diagram into a matrix that is better expressed as a table. |

### 2.7 Caption

A short explanatory note positioned beneath or beside the diagram. Conveys what the diagram is showing in human terms.

| Property | Specification |
|---|---|
| Typography | Lora Italic at 12px, weight 500 (one step above 400 roman per the broader Lora-italic-weight-bump rule), `color-text-secondary`. Italic earns warmth in the explanatory slot. |
| Position | Below-frame (centered) for diagram-summary captions, inline-anchored (left or right of a specific element) for element-specific notes. |
| Length | One sentence. If the explanation needs more than one sentence, it belongs in surrounding prose, not in the diagram. |

### 2.8 Milestone (the progression marker)

A circular indicator on a timeline or sequential progression. Marks a state along an axis. Used in process diagrams that show steps with status (queued, in-progress, done).

| Property | Specification |
|---|---|
| Shape | Circle. Diameter ~16px at default node scale. |
| State: queued | Fill `#FFFFFF` _replaced by surface-primary in implementation, see CLAUDE.md rule #1 — pure white forbidden_; stroke `warm-300` at primary weight. |
| State: in-progress | Fill `color-primary` (teal-600, `#1B685E`); stroke same color. |
| State: done | Fill `success-base` (`#438C60`); contains a centered checkmark stroke at primary weight, color `surface-page` (so the check is the warm off-white, not pure white). |
| Track | A horizontal line connecting milestones. Stroke `warm-300` at secondary weight, round caps. |

### 2.9 Icon (the named glyph within a diagram)

An icon used as a label or as the entire content of a small node. Follows iconography.md spec strictly — variable stroke weight, round caps/joins, organic curves, drawn (not FontAwesome — see design-principles.md §5 anti-patterns; brand uses inline custom SVG only).

| Property | Specification |
|---|---|
| Source | The Cena icon system per iconography.md. When the icon system has not yet rendered the needed glyph, the diagram either omits the icon or substitutes the icon with a text label until the glyph is authored. |
| Color | `color-text-primary` (`#0D322D`) within nodes; `color-text-inverse` (`#FBFAF8`) within dark substrates. |
| Size | `icon-xs` (16px) inline within node labels; `icon-sm` (20px) as a leading element within larger nodes. |

---

## 3. Stroke Specification

Diagrams use the same stroke logic as iconography but applied to architectural rather than iconic forms. The 2.5:1 ratio between primary and accent strokes carries the diagrammatic hierarchy.

### Weight

| Role | Weight (web px at standard diagram scale) | Use |
|---|---|---|
| Primary stroke | 2.5px | Emphasis connectors, milestone shapes and checks, the diagram's most important argument. Reserved — at most ~25% of stroked elements use primary weight. |
| Secondary stroke | 1.5px | Default connectors, milestone tracks, marker-stroke elements. The workhorse weight; most strokes in a diagram are secondary. |
| Accent stroke | 1px | Node contours, pill contours, lane boundaries. Quiet weight that defines without emphasizing. |

The 2.5:1 ratio (primary:accent) intentionally exceeds the iconography 2:1 ratio (iconography.md §3) because diagrams are read at greater viewing distances and viewport sizes than icons. The wider ratio preserves legible weight differentiation when a 600px diagram is shown at 300px in a slide thumbnail or compressed to mobile view.

### Cap Style

**Round caps exclusively.** Per iconography.md §3 and illustration.md §2. Connectors, marker strokes, and milestone tracks all terminate in round endpoints.

### Join Style

**Round joins exclusively.** Per iconography.md §3. Where two segments meet at an angle (orthogonal connector elbows), the join is rounded.

### Corner Radius

Node and pill corners use the spacing-radius token system per surface-treatment.md §1:
- **Pills** — `radius-full` on the short axis.
- **Standard nodes** — `radius-sm` (4px). The smallest visible radius. Prevents clinical sharpness without competing with the node's content.
- **Substrates** — `radius-md` (8px). Slightly larger than nodes, marking the substrate as a containing region.
- **Frame border (when used)** — `radius-md` (8px). Matches substrate radius.

---

## 4. Markers (Arrowheads)

Arrowheads are rendered as illustrated terminals at the tip of a connector path, not as constructed triangles. The visible difference between a flowchart-tool's arrowhead and Cena Health's arrowhead should be the difference between a constructed and a drawn quality. **Markers are scoped to connector terminations only** — they are not used as standalone graphic elements elsewhere in the system.

### Geometry — provisional, pending designer review

The current proposed arrowhead path is a curved-tail triangle:

```
M 0 1 L 10 5 L 0 9 Q -1 5 0 1 z
```

This triangle has:
- A peak at `(10, 5)` — the marker tip.
- Two flank corners at `(0, 1)` and `(0, 9)` — slightly inset from the marker base, preventing visual sharpness at the trailing edges.
- A curved tail (`Q -1 5 0 1`) — the back of the triangle is gently convex rather than flat.

Open (stroke-only) variant uses an unclosed path: `M 0 1 L 10 5 L 0 9` — same inset endpoints, no tail curve (no fill to enclose).

> **The exact marker geometry is provisional.** The same designer-review pending §2.4 connector geometry applies here — markers are the terminations of connectors and should be authored alongside them as a coordinated visual decision, not specified separately and stitched together. The path above is the working baseline; brand-team-approved exemplars should drive the final shape, and the marker may need to be re-authored to match the chosen connector curve language (e.g., if connectors land with rounded-elbow orthogonal routing, the marker shape might want to harmonize with that geometric vocabulary rather than introduce its own curve quality).

### Sizing

| Variant | markerWidth × markerHeight | refX | Use |
|---|---|---|---|
| Default | 10 × 10 | 9 | All standard arrows. The 10×10 size gives the curved-tail geometry enough render area to read at typical web scales. |
| Compact | 7 × 7 | 6 | Reserved for diagrams smaller than 320px viewport width, where 10×10 markers would overpower thin connectors. |

The marker size of 10×10 is intentionally larger than the typical flowchart-tool default (6–8). At smaller sizes, the curved-tail geometry collapses to indistinguishable from a constructed triangle.

### Color

| Variant | Path color |
|---|---|
| `arrow-default` | `warm-500` (`#958E85`) — matches default connector stroke. |
| `arrow-emphasis` | `color-brand-primary` (teal-500, `#3A8478`) — matches emphasis connector stroke. |
| `arrow-stroke` (open chevron) | Same as parent connector stroke color. |

Markers do not appear in functional colors (red/yellow/green/blue). State changes in a diagram (e.g., a milestone going from queued to done) are expressed at the milestone shape, not at the arrowhead.

---

## 5. Color Language

### The principle

Per design-principles.md Principle 1 ("The Hue Shift Is the Brand"), data visualization recapitulates the teal-to-sage progression at its scale. The brand's signature elements (substrate-anchor, emphasis path) sit in the teal-sage families. Beyond those, **categorical accent colors** drawn from the brand's extended-family palette (color.md §1.4) carry semantic meaning when a diagram needs to differentiate concepts that no amount of stroke-or-fill variation can express on its own.

The system supports five categorical anchors. Beyond them, **warm neutral is the baseline** — every diagram is warm-neutral by default. Categorical anchors are layered on only when the diagram needs to differentiate something a viewer wouldn't grasp from the neutral baseline alone. **Teal is the default-authoritative anchor** (use it when something is worth calling attention to without other context) — but teal is not required in every diagram. A diagram that gains nothing from including teal should not include teal.

The anchor semantics work in two layers: a **domain-neutral structural role** (what the anchor means at the layout level) and a **Cena-specific overlay** (how Cena's content typically maps to each role, as one application of the structural layer). Diagrams in non-Cena domains use the structural roles directly without invoking the overlays.

### Anchor set — structural roles

| Anchor | Token family | Structural role |
|---|---|---|
| **Teal** | `color-brand-primary` family | Default-authoritative anchor. Use when something is worth calling attention to without other context. Not required in every diagram. |
| **Sage** | `color-brand-sage` family | Secondary anchor — the paired counterpart, often "for whom" or "served party". *Provisional framing pending brand-expert review; revisit if outputs feel forced.* |
| **Indigo** | extended indigo family (color.md §1.4.4) | External — systems, parties, or actors outside the focal system. |
| **Violet** | extended violet family (color.md §1.4.3) | Data — information layer, analytical pipelines, derived signals. |
| **Ochre** | extended ochre family (color.md §1.4.1) | Material — concrete artifacts, physical/tangible elements. |
| Warm neutral | warm-* family | Baseline. Default node and connector color when categorical color would add noise rather than meaning. Every diagram starts here. |

### Anchor set — Cena-specific overlays

How Cena's content typically maps to each structural role. This is one application of the structural layer, not a restriction on the structural layer's reach. A non-Cena diagram (e.g., a billing-flow diagram, an org chart, a third-party integration map) uses the structural roles directly without these overlays.

| Anchor | Cena application |
|---|---|
| Teal | Cena platform, infrastructure, the institutional clinical layer. |
| Sage | Patient, nutrition, care-receiver. |
| Indigo | Partner clinics, vendor integrations, third-party systems. |
| Violet | AVA / AI inference, ML pipelines, analytical signals. |
| Ochre | Food materials, ingredients, meal artifacts. |

### Anchor scope and authority

The extended families (ochre, violet, indigo) are categorical accents, not full brand colors. They appear at the **800-stop fill** with **500-stop stroke** for tag/badge-scale elements (verified WCAG-passing per color.md §1.4 accessibility summaries). They do not appear as substrate-anchor or large-fill elements; that level of authority is reserved for teal and sage.

### Anchor that was excluded

Rose was previously included in this set as a "community / social / relational" anchor. Removed 2026-05-11 (Aaron): the structural role was unclear, and applying rose to social-context elements risked making Cena's social/community assets feel disjointed from the brand surfaces already established for that subject matter. If a future use case surfaces a clear structural need for a sixth anchor in this slot, revisit.

### Contrast formulas (cross-family canonical ratios)

Status: locked 2026-05-11 from the haven-ui color-stroke iteration. These formulas extend to any future hue family added to the system. Apply them when defining a new family's role in diagrams (chip, substrate, pill, badge, label).

There are TWO contrast contexts, and they answer different questions. Both must be considered when defining an element's visual treatment.

#### Internal contrast — within an element

Inside a pill, badge, or substrate, multiple visual layers stack: fill (the background), border (the boundary), text (the label or body content). Internal contrast governs whether each layer reads against the next.

| Layer pair | Target | Formula |
|---|---|---|
| Text on fill | ≥4.5:1 (WCAG AA body text) | text uses family-800 (heading/body) or family-500 (overline/sublabel/mid-weight) on family-50 fill |
| Border on fill | visible but not competing | border uses family-200; one major lightness step darker than family-50 fill |
| Text on dark anchor fill | ≥7:1 (WCAG AAA, for the high-authority surface) | text uses text-inverse (warm-50) on family-800 fill (e.g., dark teal anchor) |

The **canonical layer formula** for any hue family is:

```
fill   = family-50   (lightest tint)
border = family-200   (light/pale)
text   = family-500   (saturated mid)
heading/body text on light fill = family-800   (dark variant of the family)
overline/sublabel on light fill = family-500   (mid)
```

This formula holds for any hue family calibrated against the cena-brand OKLCH lightness curve (color.md §1).

#### External contrast — element vs canvas

An element on the page must visually separate from the page background (`surface-page`, `#FBFAF8`). Hue alone can do this when the family is saturated enough at the 950 stop (teal, sage carry visible hue at 95% lightness). For families whose 950 stop is close-to-neutral in hue (ochre, indigo, violet at the lightest tint), an explicit border is required for canvas separation — hue alone is insufficient.

| Element | External separation strategy |
|---|---|
| Pill / badge | family-200 border at 1px (canonical badge pattern). Visible against canvas regardless of fill hue. |
| Light substrate / large surface | family-200 border at 1px. Defines the rect edge so light tints don't dissolve into canvas. |
| Dark anchor substrate | None needed. Dark fill (family-800) carries visible weight against light canvas. |
| Warm-neutral pill / surface | Use family-500 (mid) for border, NOT family-200. Warm family's lightness curve is compressed (98→92% for 50→200 vs teal's 96→83%), so the family-200 border barely registers; the mid stop restores apparent contrast. |

#### Lightness-compression caveat

Not all families have the same lightness gap between their 950 and 800 stops. The warm-neutral family is compressed because it's tuned for surface use (cream, sand, off-white). Mechanically applying "fill 950 / border 800" produces a near-invisible border for warm-neutral while producing a clear border for teal or sage.

The fix is family-aware: when a family's 950-to-800 lightness gap is ≤7 percentage points, substitute family-500 (or family-300 if it exists at sufficient saturation) for the border. The principle is "border visible against fill" not "border at the canonical stop." Future hue families added to the system should be tested against this rule on first use.

#### When to apply which formula

| Element type | Apply internal? | Apply external? |
|---|---|---|
| Pill / badge | Yes (fill/border/text triad) | Yes (border serves both internal contrast and canvas separation) |
| Light substrate | Yes (text on fill via family-800) | Yes (1px border for canvas separation, may be invisible against fill but defines the edge against canvas) |
| Dark anchor substrate | Yes (text-inverse on family-800) | No (dark fill self-separates from light canvas) |
| Node (warm-neutral default) | Yes (text on warm-100 fill via text-primary) | Yes (use sand-700 / warm-300 border for visibility) |
| Connector | N/A (no fill) | N/A (line on canvas; stroke is the element) |

#### Cross-family target — when adding a new hue family

To add a new hue family to the diagram-system, do these checks:

1. Family must have stops at 50 (lightest tint), 200 (light/pale), 500 (saturated mid), 800 (dark variant). All must be defined.
2. Family-50 vs `surface-page` lightness delta should be ≥1.5%. If less, the family cannot be used as a light substrate without an explicit border being load-bearing.
3. Family-50 vs family-200 lightness delta should be ≥8%. If less, the canonical "fill 50 / border 200" formula will produce a near-invisible border; substitute family-500 (or another sufficiently-dark stop) per the lightness-compression caveat above.
4. Family-800 vs family-50 contrast ratio against text-rendering should be ≥7:1 (WCAG AAA) for body text. If less, the family is unsuitable for body-text use; restrict to fill/border/pill-text contexts only.
5. Family-500 vs family-50 contrast ratio should be ≥4.5:1 (WCAG AA) for the pill-text-on-pill-fill case. If less, choose a different stop for pill text.

If a family fails 2 or 3, it can still be a categorical accent (pills, regions), but cannot be a light substrate. If it fails 4, restrict its use. Document the failure mode in the family's color.md entry so future authors know its scope.

### Default palette

When a diagram has no categorical color logic to apply, it uses these defaults:

| Element | Token | Hex |
|---|---|---|
| Frame surface | `color-surface-page` | `#FBFAF8` |
| Default node fill | `color-surface-primary` | `#F3F1EE` |
| Default node stroke | `color-border-default` | `#D1CDC6` |
| Default label | `color-text-primary` | `#0D322D` |
| Default sublabel | `color-text-secondary` | `#5B544C` |
| Default connector | `warm-500` (`color-arrow-default`) | `#958E85` |
| Emphasis connector | `color-brand-primary` | `#3A8478` |
| Muted connector | `warm-600` | `#6B645C` |
| Default pill fill | `color-surface-secondary` (warm-200) | `#E7E4DF` |
| Default pill stroke | `warm-500` | `#958E85` |

### Substrate variants

A diagram has at most one **dark substrate** — the high-authority anchor region representing the most institutional or system-anchoring element. "Dark substrate" means a filled rectangle with light text on a dark surface; its purpose is to call attention to the single most important grounded element of the diagram. The "if everything screams, nothing screams" rule applies: more than one dark substrate per diagram dilutes the device.

| Substrate context | Fill | Foreground text | Foreground muted |
|---|---|---|---|
| Light institutional (default) | `surface-teal` (`#E9F5F2`) | `color-text-primary` (`#0D322D`) | `color-text-secondary` |
| Dark institutional (anchor — at most one per diagram) | `color-brand-anchor` (`#0D322D`) | `color-text-inverse` (`#FBFAF8`) | `teal-900` (`#D0E7E2`) |
| Light patient/nutrition | `surface-sage` (`#E7F2E8`) | `color-text-primary` | `color-text-secondary` |
| Light ochre/violet/indigo categorical regions | `{family}-950` (lightest stop) at 95% opacity over `surface-page` | `color-text-primary` | `color-text-secondary` |

The categorical-substrate variants (ochre/violet/indigo) are permitted at the lightest tinted level only. They do not have a dark variant — only teal carries the dark-substrate authority.

### Restraint within a single diagram

| Constraint | Limit |
|---|---|
| Distinct color anchors used | At most 4 in any single diagram. (Counting: teal, sage, ochre, violet, indigo, warm-neutral. Pick at most 4 of these 6 per diagram.) |
| Dark substrates | At most 1 (always teal-800 / `color-brand-anchor`). |
| Categorical-substrate regions | At most 3 light-tinted regions per diagram (combined across all anchors). |
| Pill colors per diagram | The diagram's chosen anchors. A diagram with teal + sage + ochre anchors can use pills in those three families plus the warm-neutral default; not in violet/indigo. |
| Emphasis-anchor contrast | Emphasis connectors and emphasis nodes use the diagram's primary anchor (teal by default; sage in patient registers). Mixing emphasis colors within a single diagram fragments hierarchy. |

### What the color language excludes

- **Pure white** (`#FFFFFF`) — anywhere. Per design-principles.md §5 anti-pattern. Even the default node's "white-feeling" fill is `surface-primary` (warm-100), not white. The check inside a milestone-done shape uses `surface-page`, not white.
- **Cool gray** (`#F5F5F5`, `#9E9E9E`, `#999`, etc.) — anywhere. The diagram's neutral surfaces all carry warm hue (H:60–85).
- **Functional colors** (`error-base`, `warning-base`, `info-base`, `success-base`) as decorative element colors — they remain reserved for interactive UI semantics. Functional colors appear in diagrams only when the diagram is itself an interactive UI artifact (a system status dashboard) and the functional color carries that status meaning.
- **Gradient fills** — anywhere. Per surface-treatment.md §5, gradients are restricted to large-surface decorative use (≥800px). A diagram is not a large-surface decorative gradient context.
- **Cool-blue indigo at the indigo-600 or darker stops** — anywhere. Per color.md §1.4.4, the indigo family is shifted warm to differentiate from cool blue; using cool-blue indigo would re-introduce the cool register the brand explicitly excludes.

### Two-register modulation (default guidance, not hard rule)

The brand has two registers: institutional (CFO/clinical-decision-maker audience) and patient/nutrition (patient/community audience). Per design-principles.md Principle 4, the same system serves both through compositional emphasis.

In data visualization, the **default** is to lean the diagram's color emphasis toward its primary audience: institutional → teal-anchor + brand-primary emphasis paths; patient/nutrition → sage-anchor + sage-300 emphasis paths. This is guidance, not a hard rule — diagrams that genuinely span both registers (e.g., a partner-integration map showing both clinical-platform and patient-facing components) may use both anchors with the categorical color logic distinguishing the regions.

The two-register modulation is a working hypothesis. As exemplars accumulate, the brand team will validate whether explicit register-color modulation reads as helpful (audience-aware emphasis) or as redundant (diagrams already declare their context through their content). Until that validation happens, follow the default guidance and flag any case where the modulation feels forced.

---

## 6. Layout & Density

### Grid

Diagrams compose on the spacing system's grid. Node positions snap to multiples of `space-2` (8px) horizontally and vertically. This creates the same proportional rhythm as text blocks and component layouts — a diagram inserted into a page does not break the page's spatial grid.

### Information density

A well-formed diagram explains one structural argument. The argument is the answer to "what is this showing me?"

| Density | Node count | Connector count | Use |
|---|---|---|---|
| Minimal | 3–5 | 2–6 | Default. The diagram makes one clear point. |
| Moderate | 6–10 | 6–15 | Acceptable for system-architecture diagrams that legitimately have multiple components. The composition needs explicit structure (substrates, lanes) to remain readable. |
| Maximal | 11–15 | 15–25 | Discouraged. Diagrams beyond this density should be decomposed into a sequence of smaller diagrams, each making one point, linked by surrounding prose. |
| Beyond maximal | 16+ | 25+ | Forbidden. The artifact is a different kind of object — an inventory, a network analysis, a database schema — and belongs to a different visualization category. |

### Spacing

- Between sibling nodes: `space-6` (24px) minimum at standard scale.
- Between substrate boundary and contained nodes: `space-4` (16px) inset on all sides.
- Between adjacent substrates: `space-8` (32px) — substrates need more breathing room than nodes, or their boundaries fail to read as separate.
- Between diagram and adjacent prose: `space-12` (48px) above and below — the diagram is a brand-moment-adjacent surface (per surface-treatment.md §3) and earns the surrounding breath.

### Mobile / small viewport behavior

When a diagram's natural viewBox exceeds the available render width:

1. **First preference** — wrap in a horizontal-scroll container. The diagram's natural composition is preserved; the user pans horizontally to read it. Acceptable on mobile up to ~520px viewport.
2. **Second preference** — re-author the diagram for vertical-stack composition. Nodes that flowed left-to-right at desktop scale stack top-to-bottom, with connectors becoming vertical paths. Requires authoring the mobile layout as a separate composition; not a free transformation.
3. **Forbidden** — uniform-scale shrink. Reducing a 760px diagram to 320px makes labels unreadable and stroke ratios collapse. The diagram either fits the available width naturally or gets re-authored.

---

## 7. Boundary — When to Reach for Other Systems

| Situation | Use this | Not this | Why |
|---|---|---|---|
| Showing platform architecture | This system | Illustration §5.5 | Architecture is structural; §5.5 is narrative. |
| Showing the food-as-medicine delivery chain as a story | Illustration §5.5 | This system | The point is the human moments; this system has no spot-illustration node primitive. |
| Showing the food-as-medicine delivery chain as a process | This system | Illustration §5.5 | The point is the structural sequence; nodes can be pills or short labels, not illustrated scenes. |
| Showing a numeric outcome | Chart (Chart.js) | This system | Numbers belong in charts; data viz is structural, not quantitative. |
| Showing how care coordinator interacts with VozCare and AVA | Could be either, depending on register | — | Institutional deck → this system, depicting the platform topology. Patient-facing onboarding → §5.5, depicting the human moments. |
| Showing a state machine | This system | — | State transitions are structure. |
| Showing a journey map with emotional moments at each step | Illustration §5.5 | This system | Emotional content needs spot illustration; structural state transitions in a journey map can use this system if the emotional layer is not part of the artifact. |
| Showing a partner-integration topology | This system | — | Integration topology is the canonical use case. |
| Showing a comparison of three approaches | Could be table, chart, or this system | — | If the comparison has structured relationships between approaches, this system. If it's primarily quantitative differences, chart. If it's primarily textual, table. |

When the situation is genuinely ambiguous, decide by the §0 content-grain rule: story → illustration §5.5; structure → this system; numbers → charts. A caption can clarify what a diagram is showing, but a caption does not change what category it belongs to.

---

## 8. Animation — Deferred

**Animation is out of scope for this draft.** Per Aaron's resolution of §11 Q8: the static visual language has not yet been validated against rendered exemplars; adding animation rules before the static layer is brand-team-approved would compound complexity ahead of foundation. The system motion principles (clinical brevity, organic deceleration, restraint as default) will govern when this section is filled in.

This section is reserved. Future additions land here once: (a) static data-vis exemplars exist and have been brand-team-validated, (b) the iconography and illustration systems' animation specs (iconography.md §7, illustration.md TBD) provide a base to extend, (c) a real motion need has surfaced from a working application of the system that justifies the addition.

---

## 9. What This System Explicitly Rejects

Each rejection is traced to the system principle it would violate.

### Rejected: Clip-art aesthetic

Uniform stroke weight throughout, sharp 90° node corners, mechanical symmetry, default flowchart-tool colors. _(Violates: Style Definition §1; design-principles.md Principle 5 "Organic Form, Systematic Application"; design-principles.md §5 "Sharp Corners")_

### Rejected: Maximalist information density

Diagrams with 30+ nodes attempting to show "the entire platform on one slide." Density limits per §6 are not advisory — they are the threshold at which the diagram stops being readable. _(Violates: design-principles.md Principle 6 "Restraint as Default")_

### Rejected: Spot-illustration nodes

If the diagram's nodes are illustrated scenes of people preparing food, devices in hands, or care coordinators at desks, the artifact is illustration §5.5, not this system. The two systems remain distinct per §0 and per illustration.md §1's "separate visual languages" rule. _(Violates: §0 boundary)_

### Rejected: Pure white backgrounds and surfaces

Any element rendered in `#FFFFFF`. Default node fill is `surface-primary` (`#F3F1EE`), not white. Frame fill is `surface-page` (`#FBFAF8`), not white. The check inside milestone-done is `surface-page`, not white. _(Violates: design-principles.md §5 "Pure White Surfaces")_

### Rejected: Cool-gray neutrals

Borders, fills, or strokes in cool gray (`#F5F5F5`, `#9E9E9E`, etc.). Every neutral in the system carries warm hue. _(Violates: design-principles.md §5 "Cool Gray Neutrals")_

### Rejected: Functional colors as decorative emphasis

Using `error-base` red for an "important" connector that does not represent an error condition. Functional colors carry interactive semantics across the brand; their decorative use here would dilute that semantics across the system. Emphasis is expressed through stroke weight + brand-primary teal, not through alarming colors. _(Violates: design-principles.md §3 Tier 1 constraint)_

### Rejected: Gradients on diagram elements

Any gradient fill on nodes, connectors, or backgrounds within a diagram. Gradients are restricted to large-surface decorative use per surface-treatment.md §5. _(Violates: surface-treatment.md §5)_

### Rejected: Sharp-cornered nodes or substrates

Right-angle corners on any visible diagram element. The brand contains no sharp corners — node, substrate, frame, pill, none of them. _(Violates: design-principles.md §5 "Sharp Corners")_

### Rejected: Decorative pattern fills on substrates

Substrate regions filled with hatching, dots, lines, cross-hatch, or other decorative pattern. Substrate uses single-color tonal fill exclusively. The illustrated-background-texture system (illustration.md §5.4) does not apply to data visualization — its near-imperceptible textures are for narrative-illustration grounds, not for diagrammatic substrate. _(Violates: surface-treatment.md §2 "Pattern, not texture"; design-principles.md Principle 6)_

### Rejected: Photographic elements within a diagram

Real images of partners, hospitals, food, or any other photographic asset embedded as a node or substrate background. Photography belongs to imagery.md's evidential register; this system is structural. The two registers do not blend mid-artifact per §0. _(Violates: design-principles.md Principle 7 "Evidence and Expression Stay in Their Lanes"; imagery.md §1)_

### Rejected: Hand-rendered "marker pen" or "sketchnote" aesthetic

Diagrams styled to look like they were drawn on a whiteboard or in a sketchnote app — wobbly strokes, scribbled handwriting, deliberate imperfection. The system's organic quality is _restrained_ organic: variable stroke weight and round terminals, not visible imprecision. _(Violates: design-principles.md Principle 5 "Organic Form, Systematic Application")_

---

## 10. Implementation Notes

### Token namespace

This document specifies the visual language. The token namespace that implementations consume is in [_tokens/color-web.md] and the surface-treatment.md radius/spacing tokens. New tokens specific to data visualization (e.g., diagram stroke weights, marker geometry) live in haven-ui or the equivalent implementation layer, derived from this spec.

### Implementation examples

The first implementation is `haven-ui`'s `diagram-*` primitive family at `Lab/haven-ui/packages/design-system/pattern-library/components/diagram-*.html` plus `diagram-graph` Layer 2 helper at `src/scripts/env/diagram-graph.js`. That implementation predates this document and was iterated against an inferred reading of iconography.md + illustration.md §5.5; it should be re-evaluated against this spec and reconciled where it diverges.

### Authoring workflow

1. **Decide the register first.** Institutional vs patient/nutrition. This decides the substrate color family.
2. **Decide the argument.** What single point is the diagram making? If the answer requires a paragraph, the diagram is too dense.
3. **Sketch the structure on paper or low-fidelity.** Identify substrates, nodes, lanes. Confirm the §6 density limits are met.
4. **Author the SVG against the primitives.** Consume this document's vocabulary; do not re-invent. If the needed primitive does not exist, propose its addition to this spec rather than authoring a one-off.
5. **Add the caption.** A diagram without a caption defaults to an aesthetic-only artifact; the caption disambiguates register and meaning.
6. **Validate against §9 rejection list.** A diagram that survives all rejections may still fail the §1 quality bar; rejections are necessary, not sufficient.

### Text-in-container fallback (only if export-to-raster reliability becomes load-bearing)

The §2.0 convention specifies `<foreignObject>` with HTML inside for all text-in-container primitives. This is the canonical path. The fallback documented here exists for one specific contingency: if a use case emerges where Cena diagrams must export reliably to PNG/PDF via non-browser tooling (client-facing PDF pipeline, automated raster export for emails), `<foreignObject>` rendering will degrade or fail in some SVG-to-image converters (rsvg, librsvg, certain image-embedders).

Fallback path: **hybrid by content type.**

- SVG `<text>` for fixed-size single-line labels — pill text (`CENA-OWNED`), milestone names (`Phase 0`), badge text, any label where content is short and known in advance.
- `<foreignObject>` for anything that might wrap — substrate body text, node descriptions, lane labels with prose, captions with multi-sentence content.

Hybrid path constraints if adopted:
- SVG `<text>` requires positioning attributes (`text-anchor`, `dominant-baseline`, `x`, `y`) in markup, never in CSS. The 2026-05-10 substrate bug (haven-ui commit `7f2a086`) is the canonical example of why CSS-set `text-anchor` overrides markup attributes and produces unreadable diagrams. Authors must follow the markup-positioning discipline rigorously.
- Authors must know in advance which slot needs which approach. This is more cognitive load than the canonical foreignObject-everywhere convention.

Decision trigger: only switch to hybrid if a concrete export-to-raster requirement materializes. Until then, stay on the canonical path.

---

## 11. Open Questions — Resolution Log

Aaron's read on the original 9 open questions, captured 2026-05-09. Each item shows the original question, Aaron's response (`@Vault`), and the resulting decision applied to the spec (`@Claude`).

### Q1 — Stroke ratio 2.5:1 vs iconography's 2:1 — RESOLVED

**Original:** I argued §3 that diagrams need wider ratio because they are read at greater distances. Is this true, or should diagrams stay on the iconography 2:1 ratio for system coherence?

%% @Vault: agree with your rationale. diagrams are more information-dense than icons given that they communicate multiple concepts as opposed to a pictogram representing a single concept. thicker strokes makes sense in this case in my opinion. %%

%% @Vault 2026-05-09: 2.5:1 ratio confirmed in §3. No spec change. %%

### Q2 — Color palette beyond teal/sage — RESOLVED, with scope expansion

**Original:** I cap dark substrate use at one per diagram for restraint. Brand may want zero or unrestricted.

%% @Vault: i'm interpreting "dark substrates" as rectangles containing text that have light text and dark surface. i'm reading your rationale as "we may want this option to highlight a single key element". i definitely agree with restraint here. i'm a fan of the adage "if everything screams, nothing screams" in reference to the fact that multiple high-contrast elements become a noisy pattern quickly instead of calling attention. this brings to mind the fact that color is often used as semantic information and given that we have a spectrum of colors as well as the ability to generate harmonious variants across arbitrary hues, i think we should have a wide enough set of options for colors and styles to allow designers options when designing diagrams based on the needs of what's being communicated. tldr: let's support more colors outside of teal and sand. %%

%% @Vault 2026-05-09: §5 Color Language was substantially expanded. Six categorical color anchors (teal, sage, ochre, rose, violet, indigo) + warm-neutral default, each with semantic association, drawn from color.md §1.4 extended families. Added categorical-substrate variants (light-tinted only — dark stays teal-only per "if everything screams" rule). Added restraint table: max 4 anchors per diagram, max 1 dark substrate, max 3 categorical-substrate regions. §2.3 Pill expanded with corresponding color variants. %%

### Q3 — Pill default fill — RESOLVED, with expansion

**Original:** Current implementation uses sand-800; draft specifies warm-200. Which is canonical?

%% @Vault: this system's purpose, like most design systems, is to provide brand-consistent options to support a wide spectrum of design possibilities. the constraining boundaries are: avoid having so many options that brand-consistency is lost or outputs start to look chaotic or random; avoid providing so few options that diagrams appear "one-note", like they're all the same, or that we lose the ability to communicate semantic concepts or group things by color. hopefully this makes sense. there's a balance we need to find here between not enough and too many options. right now my instinct is that we have not enough. %%

%% @Vault 2026-05-09: §2.3 Pill restructured into geometry+typography (shared) and color variants (9 variants: default + 6 categorical + surface-dark + mono modifier). Default fill is `color-surface-secondary` (warm-200, `#E7E4DF`) per spec. Restraint enforced through §5's "max 4 anchors per diagram" rule. %%

### Q4 — Marker size + curve quality — RESOLVED on size, OPEN on geometry

**Original:** Marker size 10×10 vs 7×7. Brand may want a different baseline.

%% @Vault: i'm not 100% clear on what "marker" and "curved-tail" refer to. with that said, per my rationale above on line-weight i'm aligned with 10 instead of 7. one thing i want to call out here regarding curves is that the curves i saw in the current iterations have felt less intentionally designed than expected. hopefully that makes sense. the curves felt "default" like bezier curves drawn by someone without experience with the pen tool. anyway, i think a good source of inspiration may be FigJam's diagram lines. they strike a nice balance between curved corners and straight lines as needed along with their line-weight. we don't need to copy it but i think designers should review their approach. let me know if i can be helpful in providing examples here or if you can find them easily. %%

%% @Vault clarification (sent during revision): the FigJam reference and pen-tool quality apply explicitly to **connecting lines between diagram elements** — i.e., connectors. Non-connective lines (illustrative or other purposes) should be considered separately. The first-step goal: demonstrate the system can draw nice brand-aligned connectors before extending to other line types. %%

%% @Vault 2026-05-09: Three changes. (1) Marker size 10×10 confirmed in §4. (2) §2.4 Connector restructured: stroke spec, three permitted geometric forms (orthogonal / straight / curved), and a new "Curve quality" subsection that's the load-bearing detail. The curve-quality subsection scopes itself to connectors only, names the FigJam orthogonal-with-rounded-elbow treatment as the reference, and flags that the precise geometry needs designer review with brand-team-validated exemplars. (3) §4 Markers updated with a "provisional, pending designer review" caveat — the marker geometry will be re-authored as part of the connector geometry exercise so the two harmonize rather than being specified independently. **Still open:** designer-authored connector exemplars + FigJam reference examples Aaron offered. Sequencing per Aaron's clarification: connector exemplars first, before extending to other line types. %%

### Q5 — Diagram caption typography (Lora Italic at 12px) — RESOLVED

**Original:** Per the broader Lora-italic-weight-bump rule, italic earns a +1 weight step. But Lora Italic at 12px is unusual.

%% @Vault: Let me see if i'm understanding correctly. Lora's letterforms are more detailed and so are less legible adjacent to Source Sans 3 at the same size. Also, we use Lora for headings which are typically not the same size as adjacent body copy. Given these assumptions, i agree that bumping Lora styles by +1 weight .... oh ... weight ... i understood size previously. well. bumping up weight i think still makes sense here. i don't understand the assertion that 12px is unusual for a given font. i know that 12px is not one of the canonical font-sizes in haven-ui but beyond that i'm not understanding why 12px is problematic. i think i'm missing something here. open to feedback / opinions. %%

%% @Vault 2026-05-09: My "unusual at 12px" framing was muddled and not load-bearing. Spec is correct as written: Lora Italic 500 at 12px, color-text-secondary. The italic-weight-bump rule applies. No further spec change beyond removing the muddled hedging from this question's text. If a future review surfaces a real legibility concern at 12px, that's a separate issue to investigate with rendered exemplars. %%

### Q6 — Two-register modulation rule — RESOLVED

**Original:** Is institutional-teal vs patient-sage a hard rule or a default?

%% @Vault: no, not a hard rule. it's an idea that makes sense and exists in brand guidelines. the key is that it has yet to be implemented widely and so there's no basis yet for opinion on whether it's effective or just noise. at this point, i think we should follow the guideline and see how it goes. open to contrary opinions from our experts. %%

%% @Vault 2026-05-09: §5 "Two-register modulation" softened from hard rule to default guidance. Added explicit working-hypothesis framing: as exemplars accumulate, the brand team will validate whether the modulation reads as helpful or as redundant. Until then, follow the default and flag forced-feeling cases. %%

### Q7 — Boundary-table default for ambiguous cases — RESOLVED (question retired)

**Original:** Is "this system + caption" the right default for ambiguous cases?

%% @Vault: i don't understand this question. %%

%% @Vault 2026-05-09: The question itself was muddy. Retired. §7 closing language now defers to the §0 content-grain rule (story → §5.5; structure → this system; numbers → charts) for ambiguous cases. Captions clarify what a diagram is showing but don't change category. %%

### Q8 — Animation — DEFERRED

**Original:** Is the stroke draw-on for connectors actually wanted, or does it violate Restraint as Default?

%% @Vault: i love animation personally, just being transparent about my personal bias. with that said i am also of the strong opinion that animations can greatly reduce friction, improve attention as well as less tangible effects from a ux perspective. so in general defensible, right-sized animations for a use case would be well-received by me. but ... i think this is early. i do want to explore animation but at this stage we haven't even demonstrated that we can draw brand consistent assets. adding the complexity of animation on top of that feels premature at this moment. let's save this idea though and come back to it later. %%

%% @Vault 2026-05-09: §8 (Animation) replaced with a deferred-section placeholder. The original animation rules (stroke draw-on, milestone state crossfade, hover elevation, reduced motion) are removed for now. Section reserved for future definition once: (a) static exemplars exist and are brand-validated, (b) iconography/illustration animation specs provide a base to extend, (c) a real motion need has surfaced from a working application. %%

### Q9 — AI-generated rejection rule — RETIRED

**Original:** Is the rejection of AI-generated diagrams enforceable, or aspirational?

%% @Vault: i don't understand the question. With that said "ai-generated" is not a success state we've reached so it seems difficult to decide whether review is needed and how to enforce it. we need review very much right now since we're iterating. once we can design the things we need and we've codified the generation process we will better understand the possible failure modes and can make a more defensible assessment on review necessity, shape, and enforcement strength / approach. %%

%% @Vault 2026-05-09: The rejection-list entry "AI-generated without authoring review" was removed from §9. Premature — the brand has not reached a generation success state, so a review-policy rule is shadow-boxing. Will revisit when generation is operational and failure modes are observable. %%

---

### Newly open after Aaron's feedback

The above resolutions surface follow-on items needing brand-team or designer input before the spec can be considered locked:

1. **Designer-authored connector exemplars + FigJam reference examples.** Aaron offered to provide FigJam line examples; designer needs to author Cena-aligned connector exemplars at multiple geometries (orthogonal, curved, mixed) for brand-team validation. This is the first-step goal Aaron named: prove the system can draw nice brand-aligned connectors before extending the spec elsewhere. §2.4 and §4 stay provisional until those exemplars exist. *Update 2026-05-10: connector primitive geometry (orthogonal-with-quarter-arc form) locked at variant B via haven-ui iteration; canonical primitive data captured into §2.4. Curve-quality and free-curve form still pending designer review.*
2. **Color-anchor semantic associations.** Previously: I assigned semantic meanings to six categorical anchors (teal=infrastructure, sage=nutrition, ochre=earth/food, rose=community, violet=data/AI, indigo=technical-vendor). *Resolved 2026-05-11 (Aaron): pivoted to two-layer framing — domain-neutral structural roles + Cena-specific overlays. Anchor set reduced to five (rose dropped — social/community framing unclear; risk that Cena's social assets would feel disjointed from established brand surfaces for that subject matter). Sage's structural role framing ("secondary anchor / paired counterpart") flagged as provisional pending brand-expert review. See §5 for current spec.*
3. **(Newly open 2026-05-11) Sage's structural role.** Marked provisional in §5 pending brand-expert review. If outputs feel forced when the diagram doesn't have a clear "served party" counterpart, revisit the framing.

---

## 12. Relationship to Other Visual-Language Documents

- **iconography.md** — the drawing language source. Stroke weight ratios, cap/join rules, organic-curve quality are inherited directly; the difference is application scale (icons at 16–48px; diagrams at 320–800px).
- **illustration.md §5.5 Diagram Illustrations** — the adjacent-but-distinct system. Illustrations §5.5 own narrative-diagrammatic content (spot-illustration nodes + flowing curves); this document owns structural-diagrammatic content (rectangle nodes + named connector primitives). The boundary is content type, not visual style.
- **surface-treatment.md** — radius, gradient, and pattern rules apply. Diagrams are not exempt from the warm-on-warm tonal ladder.
- **design-principles.md** — every principle applies. Particularly Principle 1 (hue shift), Principle 2 (weight & value move together), Principle 3 (warm ground/cool figure), Principle 5 (organic form/systematic application), Principle 6 (restraint as default).
- **imagery.md** — non-overlapping. Diagrams do not contain photography.
- **_tokens/color.md, _tokens/spacing.md, _tokens/typography.md** — the token sources this spec consumes. All hex and pixel values quoted here trace to these documents.
