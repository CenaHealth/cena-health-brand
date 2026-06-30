# Haven Surface Chrome Styling

> Cena Health · Haven design system · Brand spec
> Authored 2026-06-28 (HVD dispatch #1 → codification slice) · Sources: Steward codification proposal #04, HVD chrome-family dispatch

## What this spec owns

Visual rationale for the SoT-site / documentation-surface chrome family — **`surface-banner` + `surface-nav` + `surface-footer`** — plus the rail companion **`rail-foot-keyboard-cue`**. The names and slot contracts are set by the Steward codification proposal (dispatch #04, 2026-06-28); this file owns the brand-fidelity verdict on how each member looks. Implementation lives in [`packages/design-system/src/styles/tokens/components.css`](../../haven-ui/packages/design-system/src/styles/tokens/components.css); intent lives here. Per [.claude/rules/define-once.md](../../../.claude/rules/define-once.md), this spec is the canonical source for *why this looks the way it does* for the chrome family.

Boundary against `layout-app-navbar` / `nav-topbar` / `nav-sidebar` (the chrome-for-app family) is explicit in §1 + §2.5. Pairs with [haven-directive-styling.md](haven-directive-styling.md) at the family-discipline layer — chrome inherits the same Lora-commands · Source-Sans-quiet-frame pairing the directive vocabulary uses inside the document body.

Primitives covered:
- `.surface-banner` (+ `-identity`, `-brand`, `-caveat`, `-title-block`, `-eyebrow`, `-title`, `-lede`, `-utility`)
- `.surface-nav` (+ `-links`, `-link`, `-link[aria-current="page"]`, `-trailing`)
- `.surface-footer` (+ `-identity`, `-brand`, `-copyright`, `-utility`)
- `.rail-foot-keyboard-cue` (+ `-glyph`, `-label`)

## 1. Why this spec exists

The Cena SoT-site (and the broader [Cena SoT initiative](../../../Knowledge/Areas/Meta/Entities/cena-health/cena-sot-initiative.md) — landing, timeline, patient journey, future stage surfaces) is a **documentation district**: reference surfaces staff scan, not application screens they operate. That district needs its own chrome — a top-of-surface banner that crowns the document, a peer-nav row that connects sibling documents in the corpus, a bottom-of-surface footer that closes the document, and a sticky keyboard-cue inside the rail aside that surfaces the Cmd+K palette. Until now, that chrome lived as a fragmented `staff-universe.css` `.surface-header-*` family (project-local, ~36KB, bypassing haven's `compose.mjs` / `wrap.mjs` path — flagged 2026-06-28 by [`catalog-first.md`](../../../.claude/rules/catalog-first.md) primitives-outside-home signal). This spec authors the brand-fidelity verdict for the codified haven canon that retires that fork.

The structural argument for one spec covering banner + nav + footer + rail-foot-keyboard-cue (rather than four scattered entries): these primitives share **family-level decisions** that drift apart the moment they are derived independently. Warm-ground integration, the eyebrow register that pairs with Lora, brand-mark placement, the help-affordance treatment — all are decisions that read coherently only when authored once and applied consistently across the family. §2 owns those decisions; §3 owns per-member specifics that depend on them.

**Boundary, explicit.** The chrome-for-document-surfaces family this spec governs is structurally distinct from the chrome-for-app-screens family that lives in `layout-app-navbar` / `nav-topbar` / `nav-sidebar` (the app-shell context — persistent navigation that doesn't change page-to-page, user-action affordances, application destinations). The document surface carries a **read-only-surface caveat**, peer-document navigation rather than app destinations, and copyright in the footer — three contracts the app-shell chrome does not. §2.5 names the boundary one more time so a future manifest author or agent reaching for chrome on a documentation surface does not reflex-pick the app-shell family.

**This spec authors visual treatment only.** PL fragments, `components.css` rules, COMPONENT-INDEX rows, and manifest references are the codification slice's scope per the Steward proposal §1.4–§6.4. Tokens — fonts, colors, spacing — are referenced from canonical sources ([`palette.css`](../_tokens/generated/palette.css), [`DESIGN.md` § Typography](../../haven-ui/DESIGN.md), [`spacing.md`](../_tokens/spacing.md), [`typography.md`](../_tokens/typography.md)) per [`define-once.md`](../../../.claude/rules/define-once.md); they are named here, not restated.

## 2. Family-level discipline (shared by banner + nav + footer)

The four sub-decisions below are the spec's load-bearing core. Banner, nav, and footer each apply them; the rail-foot-keyboard-cue (§3.5) inherits the warm-ground + register decisions but lives inside the rail rather than as a top-level chrome member. Authoring these once means the per-member sections in §3 are short — they name only the per-member deltas from the family contract.

The family inherits the same authoring discipline as [haven-directive-styling.md § 2 (Brand frame for this surface)](haven-directive-styling.md): warm sand carries the structural register; Lora carries display authority; Source Sans 3 carries the quiet frame around it; teal punctuates only where teal earns its place (it does not earn its place inside this family except at one moment — see §3.3.2).

### 2.1 Warm-ground integration

The body ground for every documentation surface is `var(--color-surface-page)` (= `--color-sand-50`, `#FBFAF8`) per haven canon. The chrome family sits against that ground; the visual question is whether each chrome member carries its **own** ground (slightly warmer or cooler than the body) or sits **transparent on the body**, and whether a thin separator demarcates chrome from body.

**Verdict — chrome members sit transparent on the body ground.** Banner, nav, and footer carry `background: transparent;` so the warm sand grounds the entire surface continuously from top to bottom. No banner-color, no footer-color; the chrome is *infrastructure that grew*, not chrome that announces itself with its own slab of color (Principle 6 — restraint as default; the warm ground is the brand's essential mechanism per HVD Principle 3, and chrome owns no separate color register).

**Verdict — separators are quiet borders, not shadows.** Between `surface-nav` and the body content below it, a `1px solid var(--color-sand-100)` (`#F3F1EE`) bottom border carries the demarcation. The border is hue-adjacent to the warm ground (sand-100 sits one step into the sand ramp from sand-50), so it reads as a quiet horizontal axis rather than a hard line. Banner carries **no bottom border** when followed by `surface-nav` (the nav's top edge IS the demarcation, paired with the nav's own bottom border); banner carries a sand-100 bottom border when followed directly by body content with no nav between (a future doc-class surface without peer-nav). Footer carries `1px solid var(--color-sand-100)` top border to demarcate from body end. No shadows on any chrome member — shadows are the last-resort layer per [`Lab/cena-health-brand/CLAUDE.md`](../CLAUDE.md) Rule 4 ("elevation uses surface color shifts and warm borders first, shadows last"), and the document district has no elevation; chrome is the page, not a floating panel.

The HVD trade-off note: a heavier separator (sand-200 or sand-250) would read more confidently but would push the chrome into "block that announces itself" territory. Sand-100 sits in the restraint slot; iteration path on visual review is *down* (lighter / sand-50→no-border) only if the warm-ground integration reads as broken; *up* to sand-200 only if the demarcation reads as missing under partner review.

### 2.2 Eyebrow register

The eyebrow is the small line above a Lora H1 — in this family it serves three purposes that share one visual register: (a) the read-only-surface caveat in `surface-banner` ("Read-only — for staff reference"), (b) the stage / context label above the procedure title ("Stage 1 · Onboarding"), and (c) any future eyebrow-class label in the surface chrome.

**Verdict — the eyebrow register is Source Sans 3, `var(--text-2xs)` (10px), uppercase, letter-spacing `var(--tracking-widest)` (`0.08em`), `var(--color-sand-600)` (`#777069`), line-height `var(--leading-snug)` (1.20).** This is the haven canonical "Overline" register from [typography.md](../_tokens/typography.md) — restated for clarity on this surface, never re-decided.

**Verdict — eyebrow → title vertical rhythm = `var(--space-2)` (8px).** The eyebrow sits visually attached to the H1 it announces, not floating away from it. Eight pixels reads as "label + thing it labels" pairing; sixteen would read as two separate elements.

**Why sand-600 and not teal-700.** The eyebrow's job is to *frame* the title quietly; teal in the eyebrow slot would pull the eye to the eyebrow first (Principle 2 — weight and value move together; teal at small size + high tracking carries more weight than the surrounding warmth would justify). Sand-600 sits at the "structural-quiet" register — present, readable, but ceding the eye's primary anchor to the Lora H1 below. This is the same restraint applied to the 2026-05-28 directive-spec amendment that moved H3 from teal-700 to sand-600 + uppercase.

### 2.3 Brand-mark placement

The Cena Health brand mark is the canonical haven logo asset — the combined wordmark+icon SVG (`logo-cena-health-teal-icon-and-mark.svg`; the codification slice MUST confirm the exact path inside haven-ui's design-system package and reference it; no project-local logo copies). The mark renders identically on banner (full size) and footer (smaller, secondary register).

**Verdict — banner brand-mark sizing: 32px tall.** Reads as anchoring presence without dominating the title-block area; pairs visually with the eyebrow + Lora H1 register (32px tall ≈ 1.5× the eyebrow line-height, 0.82× the Lora H1 cap height — sits in the visual hierarchy between them). Matches the existing `.nav-logo-img` 32px height from [haven-nav-styling.md § 1](haven-nav-styling.md) — the brand-mark size convention holds across nav + surface-banner.

**Verdict — footer brand-mark sizing: 20px tall.** ~0.625× the banner mark. This ratio sits between the two originally-considered options (0.66× and 0.75×) and lands on a clean pixel value paired with footer's smaller copyright register (13px). The footer mark is *recognized but secondary* — it confirms the document's authorship without re-announcing the brand the banner already established.

**Verdict — vertical alignment, banner: brand mark center-aligns to the visual center of the title-block (eyebrow baseline + H1 cap-height combined).** Reads as "the mark and the title are paired"; both anchor to the same eye-level on a left-to-right scan. The mark sits to the LEFT of the title-block in the `surface-banner-identity` row.

**Verdict — vertical alignment, footer: brand mark center-aligns to the copyright baseline.** Single-line pairing; mark + copyright read as one horizontal unit.

**Verdict — horizontal padding from page edge: matches the body content's left padding** so the brand mark and the body's first column share a visual gutter (alignment IS the warmth — generous space exposing the warm ground per Decision heuristic #3). Token: `var(--space-6)` (24px) at default density, scaling to `var(--space-8)` (32px) at comfortable density via the parent surface's `data-density` cascade.

### 2.4 Help-affordance treatment

The help affordance appears in both `surface-banner.utility` (top-right) and `surface-footer.utility` (bottom-right). The visual contract: **the same control top and bottom** — same size, same icon, same hover/focus treatment — so a staff member who scrolls past the banner can still recognize the help affordance at the footer without re-orienting.

**Verdict — both help affordances compose `btn-icon` haven canon at default size.** Touch-target 44×44px minimum per [`Lab/cena-health-brand/CLAUDE.md`](../CLAUDE.md) accessibility floor; icon glyph is `fa-circle-question` (FA Pro v7 per [project_haven_ui_icon_canon](memory) — the codification slice confirms the exact icon-name against haven's icon canon).

**Verdict — register is outline-on-transparent (`.btn-ghost` or `.btn-outline`'s resting-quiet state).** Per the 2026-06-20 outline-altitude doctrine: the help affordance is *available, not executing* — outline-on-transparent reads as "help is here when you need it" without claiming the eye. The chrome ground is transparent (§2.1), so the help-affordance sits as a small circle-question silhouette in `var(--color-sand-700)` (`#5a544e`) with the warm-ground reading through. Hover: subtle `var(--color-sand-100)` ground-fill at the button's rect (resting → hover altitude shift; the conventional darken-on-hover that the outline-button retro validated).

**Verdict — placement: trailing-right of the utility row at both banner and footer.** Mirror-symmetric across the vertical axis — banner-utility-right pairs visually with footer-utility-right, reinforcing the "same control top + bottom" contract.

The codification slice MUST confirm `btn-icon`'s existing PL fragment exposes a `data-variant="ghost"` (or equivalent outline-resting-quiet) slot; if not, the chrome family's help-affordance ships as a Gate 4a follow-up dispatch per [`pipeline-coverage-gate.md`](../../../.claude/rules/pipeline-coverage-gate.md).

### 2.5 Family register — the four-piece discipline

The discipline this spec authors, stated as a single line authors can hold in memory while composing the family:

> **Lora commands · Source Sans frames quiet · brand mark anchors · `bg-sand-50` grounds.**

Four pieces, one register:

- **Lora commands** — the H1 inside `surface-banner.title-block` is the only Lora character in the chrome family. The display serif carries authority and editorial warmth at exactly one point in the visual hierarchy (the procedure / surface title); the rest of the chrome cedes the eye to it. The Steward proposal's "eyebrow + Lora title pattern is right" is the load-bearing observation; this spec confirms Lora at one place and one place only.
- **Source Sans frames quiet** — eyebrow, caveat line, nav-link text, copyright, help-affordance label (when present as a tooltip), keyboard-cue label — every quiet-frame text element in the chrome family is Source Sans 3 at the small / quiet / sand-600 register. Source Sans is the *infrastructure typeface* the way Lora is the *display typeface*; the chrome shows the discipline.
- **Brand mark anchors** — the Cena logo SVG appears twice in the surface (banner top-left, footer bottom-left), at different sizes (32px / 20px) but identical asset. The mark is the surface's *identity stamp*, not a decorative element; placing it consistently at both ends frames the document as Cena-authored.
- **`bg-sand-50` grounds** — the surface body's warm sand reads continuously from page top to page bottom because chrome members are transparent (§2.1). The single ground is the *connective tissue* that makes banner + nav + body + footer read as one document, not four separate slabs of UI. This is the structural difference from chrome-for-app, which uses chrome ground colors (navbar background, sidebar background) to delineate persistent regions.

**What this distinguishes from chrome-for-app (`layout-app-navbar` / `nav-topbar` / `nav-sidebar`).** Application chrome carries:

- A **slab navbar** with its own ground color (app-navbar surface, often a darker or contrast-shifted band) — the chrome announces itself as the app's persistent UI region.
- **User-action affordances** (account menu, notifications, settings) — the chrome is interactive operating surface.
- **Application destinations** in the primary nav (Patients, Schedule, Messages — N top-level routes the user moves between).
- **Sidebar/rail-as-app-navigation** that persists across screens — the rail is part of the app shell, not a per-document affordance.

The documentation chrome carries none of those: transparent ground (§2.1), help affordance as a single quiet outline (§2.4), peer-document links rather than app destinations, rail-as-per-surface-content-navigation. When a manifest author or future agent is reaching for chrome and the surface is a **document** (a thing staff *reference*), this family. When the surface is an **application screen** (a thing staff *operate*), the `layout-app-*` / `nav-topbar` / `nav-sidebar` family. The COMPONENT-INDEX `when-to-use` text per Steward proposal §2.5 names the boundary at the primitive layer; this section names it at the brand-discipline layer.

## 3. Per-member specifics

### 3.1 `surface-banner`

The banner crowns the document. Composition order from top to bottom inside the banner block: skip-link (first focusable child of `@slot: identity` per Aaron Q2 = Option B; see §3.1.5 below) → identity row (brand mark + caveat line) → title-block (eyebrow + Lora H1) → optional lede → utility (trailing right; visually paired with the identity row across the banner's full width).

**Layout pattern.** Two horizontal bands inside the banner: an *identity band* (brand mark on the left, utility/help affordance on the right) and a *title band* (eyebrow + H1 + optional lede, full-width stacked left). The identity band's left and right elements anchor the banner's outer corners; the title band sits beneath it, indented to share the same left padding as the body content below.

#### 3.1.1 Title block — eyebrow + Lora H1

- **Lora H1** — composes the haven canonical `h1` style from [DESIGN.md § Typography](../../haven-ui/DESIGN.md) + [typography.md](../_tokens/typography.md): `font-family: var(--font-display)` (Lora), `font-size: var(--text-2xl)` (39px), `font-weight: 700`, `line-height: 1.15`, `letter-spacing: -0.01em`.
- **Color verdict — `var(--color-text-primary-warm)` (= `--color-sand-900`, `#25211D`), NOT `--color-text-primary` (teal-ink `#0D322D`).** This applies the 2026-05-28 (evening) retro discipline: Lora display register on documentation surfaces reads cooler than intended when colored teal-ink; sand-900 is the warm-dark Lora authority that pairs with the warm sand ground. The body's running prose underneath continues to use `--color-text-primary` per haven canon — the override applies only to chrome H1 / H2 display register. The codification slice MUST confirm the exact token name against [`palette.css`](../_tokens/generated/palette.css) — `--color-text-primary-warm` is the spec-side proposal; if the canonical token differs, substitute it.
- **Eyebrow → H1 spacing** — `var(--space-2)` (8px), per §2.2.
- **H1 → body / lede spacing** — `var(--space-4)` (16px) when lede is empty (the most common case for cap-* surfaces); `var(--space-3)` (12px) between H1 and lede when lede is filled (tighter pairing so H1+lede read as one display block).

#### 3.1.2 Caveat line — read-only-surface caveat

The "Read-only — for staff reference" caveat sits **inside the identity row, to the right of the brand mark, before the utility region** (so the eye reads `[mark] Read-only — for staff reference … [help-icon]` left-to-right). The caveat is a content contract of the documentation district — every staff-facing reference surface carries it.

- **Register — Source Sans 3, `var(--text-xs)` (13px), italic, `var(--color-sand-600)` (`#777069`), line-height `var(--leading-relaxed)` (1.45).** Italic earns its place here because the caveat is a *meta-statement about the surface* (not surface content); italic reads as the page speaking *about itself* rather than *its content*. This is the same justification the 2026-05-28 (morning) retro applied to `glossary-term` (italic as the "this is a term being defined, not running prose" signal).
- **Spacing — `var(--space-3)` (12px) gap between brand mark and caveat; sits on the same baseline as the brand mark's vertical center.**

#### 3.1.3 Lede (when filled)

The lede slot is **empty by default** per the restraint discipline in §3.2 below. When filled (orientation surfaces — landing, journey, stage overview), the lede serves an *orienting* function: one sentence answering "what this page is for."

- **Register verdict — Source Sans 3, `var(--text-md)` (20px), `font-weight: 400`, line-height `var(--leading-body-sm)` (1.50), `var(--color-sand-700)` (`#5a544e`).** The HVD choice between Lora light italic and Source Sans 3 large quiet lands on **Source Sans 3 large quiet**, with explicit rationale: Lora italic in the lede slot would compete with the Lora H1 above it (two Lora registers stacked reads as "two display elements not paired with each other"); Source Sans 3 large quiet reads as the *body's first paragraph*, which is exactly what the lede is — a body-class orientation paragraph promoted to the chrome by virtue of needing to land above the nav. The chrome's "Lora commands at exactly one point" discipline (§2.5) is preserved.
- **Line-length cap — `max-width: var(--measure-base)` (= 65ch per haven reading-measure canon, or whichever the canonical measure token defines; the codification slice confirms exact token name).** Long ledes are an anti-pattern — if the lede needs more than ~80 characters to orient, the surface's job is unclear and a brief sentence cannot rescue it; the line-length cap forces the discipline at the composition layer.
- **Top margin from H1 — `var(--space-3)` (12px).** Bottom margin from lede to nav (or to body if no nav follows) — `var(--space-6)` (24px).

#### 3.1.4 Spacing — the banner's outer dimensions

- **Top padding from page edge — `var(--space-8)` (32px) default, `var(--space-10)` (40px) at comfortable density.** Generous space above the banner exposes the warm ground; the document does not crash against the viewport top.
- **Bottom margin before `surface-nav` (when present) — `var(--space-4)` (16px).** The banner closes; the nav opens; both share the page's left padding for vertical alignment.
- **Bottom margin before body (when no nav present) — `var(--space-6)` (24px).** Slightly more generous because the body's first H2 needs more breathing room from the banner's H1 than the nav's link row does.
- **Horizontal padding — matches body content padding (`var(--space-6)` default, scaling via `data-density`).** Established in §2.3.

#### 3.1.5 Skip-link composition (Aaron Q2 = Option B)

Per Aaron's 2026-06-28 call: the skip-link is composed as the **first child of `surface-banner`'s `@slot: identity`** — the surface-banner PL fragment includes a default skip-link in that position, making the affordance automatic when a consumer composes the banner (impossible to forget).

- **Default state — off-screen, visually invisible to non-keyboard users.** Composed via the canonical haven a11y off-screen pattern (existing `.skip-link` rule at `components.css` line 14783, which applies `sr-only`).
- **`:focus-visible` state — surfaces at the top-left of the banner.** Visual treatment is owned by [`haven-nav-styling.md` § Skip-link](haven-nav-styling.md) per Steward §2.2 routing (HVD dispatch #2 ships the brand-spec content as a new §5 in that file). This spec's job is to confirm the **placement** within the banner: when surfaced, the skip-link reads inside the identity row's left zone, visually above the brand mark (it surfaces at z-index above the banner's content). After the user tabs past it (or activates it), it returns off-screen.
- **Skip target** — `href="#main"` by default; the consumer's manifest may override. The `<main>` element on every doc surface carries `id="main"` (the codification slice MUST audit existing layouts).
- **Why this placement preserves a11y contract.** Even though the skip-link is composed *inside* `surface-banner` per Aaron Q2 = Option B, it remains the FIRST focusable element on the page because (a) the banner is the first element after `<body>` (no other focusable content sits between `<body>` and the banner), and (b) the skip-link is the first child of the banner's identity slot, which is the first slot in the banner's composition order. Tab-from-page-load lands on skip-link. The codification slice's render-pipeline verification per Steward §1.5 falsifies this with a keyboard-nav test.

### 3.2 surface-banner lede restraint discipline

The lede slot exists; whether to fill it is a per-surface authoring decision the manifest carries. This subsection authors the canonical reasoning so manifests inherit it rather than re-deciding per page.

**Fill the lede slot when the surface is an orientation surface.** Orientation surfaces answer "what is this page for, and where does it sit in the bigger picture" — they help the staff member figure out whether they're in the right place. Examples in the current and near-future Cena SoT-site:

- **Landing surface** — "Cena Health's source-of-truth for clinical operations" — the lede tells visitors what corpus they've arrived at.
- **Patient Journey surface** — "How a patient moves from referral through enrollment to active care" — the lede orients to the journey's scope.
- **Stage Overview surfaces** (Stage 1: Onboarding, Stage 2: Active Care, etc.) — "Onboarding spans referral receipt through enrollment confirmation" — the lede frames the cap-* procedures that follow.

**Do NOT fill the lede slot when the surface is a reference surface.** Reference surfaces are the per-procedure cap-* pages — `cap-03`, `cap-25`, `cap-47`. Staff arrive at these *already knowing the surface's purpose* (they came here to look up a specific procedure); the eyebrow ("Stage 1 · Onboarding") + Lora H1 (the procedure name) + the kv-row meta strip below (cap-id + roles + scope) carry full context. A lede in that position re-states what those three already establish.

**The boy-who-cried-lede rationale.** A lede that fills on every surface trains staff to skim past it. The next time the lede is actually load-bearing (the user genuinely needs orientation), they have learned to skip it. Reserving the lede slot for surfaces where it carries weight keeps it readable when it does appear — restraint as a *readability-preservation mechanism*, not an austerity preference.

**The mechanical contract for manifests:**

- The `procedure.screen.json` family (cap-* surfaces) does NOT include a `lede` binding in the surface-banner slot.
- The `landing.screen.json`, `journey.screen.json`, `stage-overview-*.screen.json` family DOES include a `lede` binding.
- Future surface manifests inherit the decision at authoring time: if the surface answers "what is this page for" already through eyebrow + H1 + meta, skip the lede; if the surface needs to *orient*, fill it.

**The honest limit.** This discipline is judgment, not a mechanical gate. The codification slice MUST NOT bake the lede-empty-default into the surface-banner PL fragment in a way that makes filling the slot impossible — the slot's contract per Steward §2.3 is *optional, consumer-overrideable*. The restraint discipline lives at the manifest-author layer + this brand spec; it's enforced through review (the codification slice's File List includes the manifest, and reviewer reads against this section), not through tooling.

### 3.3 `surface-nav`

The peer-page nav row sits below `surface-banner`. Composition: a horizontal cluster of N anchored links (sibling documents in the SoT corpus — e.g., SoT · Timeline · Patient Journey · Staff) on the left, with the trailing `toolbar-search` composition on the right. ARIA role `navigation` with `aria-label="Surface navigation"`.

#### 3.3.1 Link row visual treatment

- **Register — Source Sans 3, `var(--text-sm)` (14px), `font-weight: 500` (medium — slightly above default 400 to carry the link weight without reaching toward bold).** `var(--color-sand-700)` (`#5a544e`) in the inactive state; line-height `var(--leading-normal)` (1.25).
- **Inter-link spacing — `var(--space-6)` (24px) horizontal gap between links.** Generous gap exposes the warm ground between links; reads as "links sit on the page" rather than "links cluster together."
- **Hover state — color shifts to `var(--color-sand-900)` (`#25211D`) + underline appears (`text-decoration: underline; text-underline-offset: 4px; text-decoration-color: var(--color-sand-300)` or equivalent quiet-warm token; `text-decoration-thickness: 1.5px`).** No background-fill on hover (the warm ground continues; restraint discipline).
- **Focus-visible — standard haven focus ring per [`Lab/cena-health-brand/CLAUDE.md`](../CLAUDE.md) accessibility floor: `box-shadow: 0 0 0 2px var(--color-surface-page), 0 0 0 4px var(--color-border-focus)`.**

#### 3.3.2 Active state — `aria-current="page"`

Per Aaron's 2026-06-28 Q1 call: **triple-cue (color + weight + teal-400 underline) without a ground-shift.** This reverses the Steward proposal's §2.4 "warm-sand ground-shift" recommendation. The rationale: a ground-shift on the active link would be the only ground-color exception inside the §2.1 transparent-chrome discipline; the triple-cue carries the active signal without breaking the family contract.

The active link uses **`aria-current="page"` as the binding** + a visual treatment that does NOT rely on color alone (WCAG 1.4.1).

- **Color — `var(--color-sand-900)` (`#25211D`) — the warm-dark display tone.** Distinct from sand-700 inactive by ~10 OKLCH luminance steps; passes WCAG AA on sand-50 ground (~10:1 contrast — actually AAA).
- **Weight — `font-weight: 600` (semibold).** Distinct from medium inactive; provides the non-color cue per WCAG 1.4.1.
- **Underline — persistent `text-decoration: underline; text-underline-offset: 4px; text-decoration-thickness: 2px; text-decoration-color: var(--color-teal-400)` (`#5f9a8f`).** This is the **one place teal earns its presence in the chrome family** — a thin teal underline as the active marker reads as the *brand punctuating the active state*. Restraint preserved (it's 2px on one link, not a ground-shift); identity preserved (the brand's signature teal appears, anchoring "this is Cena" without taking over).
- **No warm-sand ground-shift on the active link.** Per Aaron Q1 (2026-06-28): the triple cue (color + weight + teal underline) carries the active state without the ground-shift. Iteration path: if at render-time review the triple-cue reads as under-anchored (the active link does not stand out clearly enough against the inactive ones), the ground-shift can be added on top of the triple-cue. The change is reversible and isolated to one selector; ship the more-restrained version first.

#### 3.3.3 Trailing `toolbar-search` placement

- **Verdict — vertical-center aligned to the link row's baseline-cap-height range.** The toolbar-search input sits with its visual center matching the link row's visual center; reads as "search is peer to the links, sitting alongside them."
- **Spacing from rightmost link — `var(--space-8)` (32px).** More gap than between links so search reads as a distinct cluster, not as another nav item.
- **The search composition is `toolbar-search` haven canon, not duplicated here.** This spec does not author toolbar-search styling; the existing `toolbar-search` PL fragment + brand spec (wherever it lives — Steward §2.2 routing flagged this for HVD verification at codification time) owns the search input's own register. The chrome's role: provide the slot, position it correctly.

#### 3.3.4 Bottom border — demarcation from body

- **Verdict — `1px solid var(--color-sand-100)` (`#F3F1EE`) bottom border.** Per §2.1 + Aaron's Q2 call (2026-06-28): trust render-time review. Sand-100 is the canonical body-from-chrome separator for this family.
- **Top border — none.** The banner's bottom edge (no border per §3.1.4 banner spec, when nav follows) + the nav's top edge sit on the body's warm ground; the nav opens visually below the banner without a hard demarcation between the two chrome members. The banner and nav read as one unit; the bottom border separates that combined unit from the body.

#### 3.3.5 Mobile collapse behavior

- **Verdict — DO collapse to hamburger menu below the `md` breakpoint (768px / `var(--breakpoint-md)`).** Above `md`, the link row sits inline with toolbar-search. Below `md`, the link row collapses into a hamburger affordance on the right (mirror of where toolbar-search sat in the desktop view); toolbar-search becomes a search-icon affordance to the left of the hamburger. Tapping hamburger expands a vertical link list below the banner; tapping the search icon expands the search input into a full-width row.
- **Rationale.** Per [`feedback_browser_verification_required`](memory): the cena-sot surfaces are read on desktop staff workstations primarily, but the brief named responsive-at-all-sizes as a contract (per [`pipeline-coverage-gate.md`](../../../.claude/rules/pipeline-coverage-gate.md) source incident §2). Collapsing to hamburger is the canonical responsive pattern that haven's existing `nav-topbar` (the app-chrome cousin) already implements; reusing the same pattern at the surface-nav layer preserves consistency across haven chrome.
- **The codification slice MUST author the collapse markup in the PL fragment** — including the `<button class="surface-nav-toggle">` for hamburger affordance + the `aria-expanded` binding for the collapsed-state expand/collapse + the focus-trap discipline when the menu is open (focus traps inside the expanded list; Esc closes; click-outside closes). These are render-pipeline contracts the codification slice owns; this spec authors the visual treatment.
- **Hamburger glyph** — `fa-bars` (haven icon canon, FA Pro v7); same `btn-icon` register as the help affordance per §2.4 (outline-on-transparent, sand-700, 44×44 touch target).

#### 3.3.6 Bound variants — `surface-nav-link-bound` + `surface-nav-link-active-bound`

- **Purpose** — per-page peer-link binding inside `@slot: links`. Each consumer surface class (procedure-detail / landing / timeline / patient-journey) names its own peer-link set; the bound variants let the manifest emit those labels + hrefs without restating the surface-nav-link markup. Authored 2026-06-30 per Steward §6.1 dispatch (HVD round-5).
- **Visual register — unchanged from §3.3.1 + §3.3.2.** Bound variants inherit the inactive and active treatments authored above; the binding contract is mechanical (`data-field="label"` for the link text, `{href}` for the anchor target). The `-active-bound` variant adds `aria-current="page"` per §3.3.2 — the only structural delta between the two bound variants is that one attribute.
- **Composition shape** — the bound variants emit an `<li><a class="surface-nav-link">…</a></li>` pair (each-iterating directly into `surface-nav-links cluster` per the default exemplar's wrapping list). Mirrors the established `surface-rail-top-link-bound` precedent.
- **Why bound + active-bound as two primitives, not one with conditional active.** The compose engine resolves a per-iteration active variant via `each.active-child-id` (mirrors the `nav-item-rail-row-bound` + `-active-bound` pair). Two named primitives keep the slot-fill mechanical; the manifest names the active-key field once, the engine swaps in the active markup for the matching iteration item.

### 3.4 `surface-footer`

The footer closes the document. Sibling to `surface-banner` by family discipline (§2) at a smaller register. Composition order from left to right: brand mark (20px tall per §2.3) → copyright text → trailing help affordance (mirror of banner's per §2.4). Single horizontal row at default density; wraps gracefully on narrow viewports (the copyright line may break under the brand-mark on mobile if needed).

#### 3.4.1 Brand-mark sizing

- **Verdict — 20px tall** (~0.625× the banner mark). See §2.3 for the full sizing rationale.

#### 3.4.2 Copyright register

- **Register — Source Sans 3, `var(--text-xs)` (13px), `font-weight: 400`, `var(--color-sand-600)` (`#777069`), line-height `var(--leading-relaxed)` (1.45).** Quiet enough to read as legal-text infrastructure (where copyright lines conventionally sit in the visual hierarchy); not so quiet that it becomes invisible.
- **Content contract** — the manifest provides the copyright string. The canonical entity copy is "© 2026 MLC Marketing LLC DBA Cena Health" per [`reference_cena_legal_entity`](memory). The year is bound (the codification slice MUST source year from build-time or manifest binding, not hard-code, so the line stays accurate across calendar years).
- **Line-length cap — `max-width: var(--measure-narrow)` (~50ch; codification slice confirms exact token).** Long copyright strings (multi-entity attribution; jurisdiction disclaimers) wrap to multiple lines gracefully rather than spanning the full footer width.

#### 3.4.3 Help-affordance mirror

- **Identical to banner's help affordance per §2.4.** Same `btn-icon` register, same outline-on-transparent quiet treatment, same fa-circle-question icon, same 44×44 touch target. The mirror discipline is the contract — staff who scroll past the banner can still recognize the help affordance at the footer without re-orientation.
- **Placement — trailing right.** Mirror-symmetric across the vertical axis to the banner's utility region.

#### 3.4.4 Spacing — the footer's outer dimensions

- **Top margin from `<main>` end — `var(--space-10)` (40px).** Generous space above the footer demarcates "the document is over" before the footer's small register begins. Without that breathing room the footer reads as a continuation of the body's last paragraph.
- **Bottom padding to page edge — `var(--space-8)` (32px).** Mirror of the banner's `var(--space-8)` top padding (§3.1.4) — the document's top and bottom outer margins are visually balanced.
- **Horizontal padding — matches banner per §2.3** (`var(--space-6)` default, scaling via `data-density`).
- **Internal vertical alignment — center.** Brand mark, copyright, and help-affordance share a single horizontal baseline at the footer row's vertical center; the row's height is determined by the tallest element (the 44×44 help-icon touch target).

#### 3.4.5 Top border — demarcation from body

- **Verdict — `1px solid var(--color-sand-100)` (`#F3F1EE`) top border.** Per §2.1 + Aaron's Q2 call (2026-06-28): mirrors the nav's bottom border (the body sits between two sand-100 horizontal axes — the nav's bottom and the footer's top — which read as a quiet enclosure around the reading region).
- **Why the symmetry matters.** Nav-bottom-border and footer-top-border using the same token + thickness mean the body content sits in a recognizable horizontal channel. The eye reads "the document is bounded above by nav and below by footer" — same shape, same weight, same warmth.

### 3.5 `rail-foot-keyboard-cue`

The sticky-foot fragment of the rail aside. The canonical content is `⌘ K to search` — a keyboard-cue reminder for the Cmd+K palette that's the SoT-site's corpus search affordance. Persistent across rail scroll; sits at the bottom of the rail's `<aside>` so a reader who has scrolled deep into the cap accordion list can still see the keyboard hint without scrolling back to the top.

#### 3.5.1 Glyph register

- **Font — Source Code Pro (`var(--font-mono)`).** The `⌘` glyph (U+2318, Place of Interest Sign, used canonically as the macOS Command symbol) reads as a *keyboard-language token* in monospace, distinct from prose. Same rationale haven applies to inline code and cap-id displays.
- **Size — `var(--text-sm)` (14px), `font-weight: 500`, line-height `var(--leading-snug)` (1.20).** The glyph + letter pair (`⌘ K`) sits at body-small size — large enough to read but small enough to not announce itself.
- **Color — `var(--color-sand-800)` (`#3D3832`).** A step warmer-dark than the label that follows (sand-600); the glyph carries the eye's attention because IT is the affordance the cue is pointing at; the surrounding label is supporting frame.
- **Glyph + letter spacing** — `0.25em` between `⌘` and `K` (visual breathing room so the modifier and key read as paired but distinct).

#### 3.5.2 Label register

- **Font — Source Sans 3 (`var(--font-body)`).** The label "to search" is prose-class supporting text around the keyboard token.
- **Size — `var(--text-xs)` (13px), `font-weight: 400`, line-height `var(--leading-relaxed)` (1.45).**
- **Color — `var(--color-sand-600)` (`#777069`).** Quiet frame around the keyboard-language token; same register as the eyebrow + caveat in §2.2.
- **Glyph → label gap — `var(--space-2)` (8px).** Visual pairing — `[⌘ K] to search` reads as one phrase.

#### 3.5.3 Sticky positioning inside the rail aside

- **Position — `position: sticky; bottom: 0;` inside the rail's `<aside>`.** Sticks to the rail-foot when the rail scrolls; visible at all scroll positions.
- **Bottom padding from rail-bottom — `var(--space-4)` (16px).** Generous enough that the cue does not crash against the viewport edge.
- **Top padding from cue-content — `var(--space-3)` (12px).** Provides clearance from the accordion content above when scrolled to the bottom.
- **Horizontal padding inside the rail aside — matches the accordion content's horizontal padding** so the cue aligns visually with the nav rows above it (single left-edge alignment for everything in the rail).

#### 3.5.4 Contrast against rail ground

The rail's `<aside>` MAY carry its own slightly-warmer ground than the body's `bg-sand-50` (per the existing `sidebar` haven canon; this spec does not modify the rail's ground). The keyboard-cue inherits whatever ground the rail provides; the cue does NOT carry its own background-fill.

- **If the rail ground is `var(--color-sand-50)` (transparent or matching body):** the cue's sand-800 glyph + sand-600 label both clear WCAG AA against sand-50 (~7.0:1 and ~4.7:1 respectively).
- **If the rail ground is `var(--color-sand-100)` (`#F3F1EE`, one step warmer):** sand-800 + sand-600 still clear AA (~6.7:1 and ~4.4:1 — the sand-600 against sand-100 sits at the AA threshold; the codification slice's render verification MUST confirm).
- **Top border — `1px solid var(--color-sand-200)` (or sand-100; HVD validates against rail's actual ground at codification time).** Separates the cue from the scrollable accordion list above; reads as "this is the rail's foot zone, not part of the scroll content."

#### 3.5.5 Optional second variant — different keyboard cue

The slot's `@slot: cue` is consumer-overrideable per Steward §2.3. Other keyboard affordances may use the same primitive — `? to open help`, `Esc to close`. The register stays the same; only the content changes. Future surfaces that surface a different cue inherit the visual treatment automatically.

### 3.6 `surface-rail` — `@slot: filter-row` (consumer-bound aside-level controls)

The `surface-rail` primitive (sibling to the rail-foot-keyboard-cue covered in §3.5; the rail itself is composed in the SoT-site shell, not authored as a chrome family member here) exposes an aside-level secondary-control slot between its top-links section and its stage accordion. The slot's role is "a single consumer-bound aside-level control region"; what fills it depends on the consumer surface class.

- **For procedure-detail surfaces (cap-\* manifests)** — compose `nav-filter-pills-bound` to drive a single-select role filter (CC · RDN · Admin · Kitchen · MPH Student). The role filter scopes the visible cap rows by author/owner role.
- **For landing / timeline / patient-journey surfaces** — compose `toolbar-search` by reference. The control is corpus search at the rail level (a sibling to the surface-nav's trailing toolbar-search).
- **For surfaces that don't need a secondary control** — leave the slot empty; the default PL content (an honest disabled `toolbar-search`) is suppressed by the consumer override per the half-state-promises discipline.

#### 3.6.1 Slot-name discipline — why `filter-row`, not `search`

The slot was renamed from `@slot: search` to `@slot: filter-row` on 2026-06-30 per Steward §6.3 dispatch (HVD round-5 §6.3 + §8.2 cross-affordance synthesis). The rationale: per HVD §8.2, slot names describe the slot's *role*, not the slot's first-instance content. The `search` name baked the original first-instance content (a toolbar-search) into the slot's identity; the role of the slot is broader (a single aside-level secondary control, which may be search OR a filter OR a future bound primitive yet to be authored).

`filter-row` is not strictly perfect either (it now names the *other* dominant first-instance content — the role filter). The candidate considered + rejected: `controls` (genuine role-level name, neutral between search + filter + future). The call: `filter-row` won the rename because (a) the SoT-site's dominant consumer class is procedure-detail surfaces (every cap-\* manifest), and (b) `filter-row` is more concrete than `controls` for a manifest author reaching for the right primitive at the right slot. Future altitude shift to `controls` is reversible if a third orthogonal first-instance content emerges (the slot rename itself was a zero-migration change — no existing consumer overrode `@slot: search` before the bound-variant machinery existed; the same will be true at the time of any future rename).

The CSS wrapper class `.surface-rail-search` was deliberately NOT renamed in this dispatch. The wrapper class owns the styled padding/layout for the slot's content, and renaming it requires components.css edits + any future consumer cleanup. The slot name is the primary discovery surface for manifest authors (it appears in the PL fragment's `@slot:` marker, which is what `compose.mjs` reads); the CSS class is the styling layer's internal detail. Splitting them is a one-time inconsistency that future styling-class follow-up can close (a `.surface-rail-controls` rename is queued as a Steward observation; not blocking).

#### 3.6.2 Default content — the honest-not-yet pattern

The PL exemplar's default content for `@slot: filter-row` is an `input type="search"` with `disabled` and placeholder "Search — coming in a later slice". This is the half-state-promises.md honest pattern: a consumer who composes `surface-rail` without overriding the slot inherits a visibly-not-yet affordance rather than a half-state promise (a search box that *appears* wired but does nothing).

When the consumer overrides the slot with a real control (the bound nav-filter-pills, or a wired toolbar-search), the default content is replaced. The override path is mechanical via `compose.mjs`'s slot-fill engine.

### 3.7 `nav-filter-pills` — bound variants for `@slot: filter-row`

The `nav-filter-pills` primitive (codified separately at `pattern-library/components/nav-filter-pills.html`) is the canonical "single-select category filter" affordance across haven. The Steward §6.3 dispatch (2026-06-30) added bound variants — `nav-filter-pills-bound` (the wrapper, exposing `@slot: pills`) + `filter-pill-bound` (the per-pill iterator) — for data-bound composition inside `surface-rail.@slot: filter-row` and any future patient-list / alert-list / meal-list filter consumer.

The bound variants do NOT change the visual treatment; `.filter-pill` + `.filter-pill.active` register stays canonical. The bound variant's active-state binding uses `[data-active="true"]` (mirroring `.filter-pill.active` via a union selector in `components.css`); the manifest's bind contract for the active field is `bind: { active: "selected" }` where the content's `selected` field carries `"true"` or `"false"`. This pairs the static-exemplar path (the `.active` class on a hand-authored pill) with the bound-iteration path (the `data-active` attribute on an each-emitted pill) under one visual register.

The `.nav-filter-pills` semantic wrapper class was added 2026-06-30 (it had previously been a utility-soup `flex flex-wrap gap-2` inline on the default exemplar's parent div). The bound wrapper variant uses this semantic class so consumers compose against a named primitive rather than utility soup, per the haven-ui authoring discipline.

## 4. Accessibility floor (shared)

The chrome family inherits the accessibility floor from [`Lab/cena-health-brand/CLAUDE.md`](../CLAUDE.md) and [DESIGN.md § motion / persona](../../haven-ui/DESIGN.md). This section names the chrome-specific applications.

### 4.1 Focus-visible rings

Every focusable affordance in the chrome family — skip-link, nav-link, toolbar-search input, help btn-icon, mobile hamburger toggle (when present), rail-foot-keyboard-cue (if it ever becomes focusable; default is text-only display) — uses the haven canonical focus ring:

```
box-shadow:
  0 0 0 2px var(--color-surface-page),
  0 0 0 4px var(--color-border-focus);
```

The inner ring matches the surface ground (sand-50) so the focus ring reads as floating above the page; the outer ring is the focus-color token (haven canon — typically a teal-400 derivative). On the skip-link's `:focus-visible` surface state, the same ring applies on top of whatever surfaced positioning haven-nav-styling.md authors.

### 4.2 Reduced-motion behavior

The chrome family carries minimal motion to begin with — there are no entrance animations, no hover scale-shifts, no parallax. The only motion-carrying affordances are:

- **Hamburger menu expand/collapse** (mobile, §3.3.5) — when present, the expanded link list transitions in via `transform: translateY` + `opacity`. Under `@media (prefers-reduced-motion: reduce)`, the transition is removed (`transition: none`); the list appears instantly.
- **Skip-link surface on `:focus-visible`** — the off-screen→on-screen transition is instant by default; reduced-motion users get no change.
- **Hover state on nav-links** — the underline appearance is instant by default; reduced-motion users get no change.

The chrome family does NOT add motion that the user did not consent to in a context they did not opt into (per DESIGN.md § persona-motion precedence).

### 4.3 WCAG AA contrast pairs (named)

The chrome family's color pairs, validated against WCAG AA non-text-contrast and text-contrast thresholds:

| Foreground | Background | Use | Contrast | Pass |
|---|---|---|---|---|
| `--color-sand-900` (`#25211D`) | `--color-sand-50` (`#FBFAF8`) | banner H1, active nav link | ~10:1 | AAA |
| `--color-sand-700` (`#5a544e`) | `--color-sand-50` | inactive nav link, help-icon glyph | ~6.0:1 | AA |
| `--color-sand-600` (`#777069`) | `--color-sand-50` | eyebrow, caveat line, copyright, label register | ~4.7:1 | AA (text) |
| `--color-sand-800` (`#3D3832`) | `--color-sand-50` | keyboard-cue glyph | ~7.7:1 | AAA |
| `--color-sand-600` | `--color-sand-100` (`#F3F1EE`) | keyboard-cue label on rail ground | ~4.4:1 | AA (threshold; validate at render) |
| `--color-sand-100` border | `--color-sand-50` | nav-bottom + footer-top separators | ~1.07:1 | borderline non-text; iterate to sand-200 if reads as missing |
| `--color-teal-400` (`#5f9a8f`) underline | `--color-sand-50` | active nav-link teal underline | ~2.8:1 | non-text-contrast pass paired with weight + color cues |

The borderline rows (sand-100 border, teal-400 underline at 2.8:1) ride render-time visual review as binding sign-off; iteration paths named (sand-200 border step-up if too quiet; teal-500 underline step-up if too quiet).

## 5. Decisions made

Aaron's 2026-06-28 calls that the codification slice integrates against this spec:

- **Q1 (Steward `surface-*` vs `doc-*` family prefix):** `surface-*` accepted. Rationale per Steward §Open Q1: the chrome operates at the *surface* layer (position), distinct from the body's `doc-*` family that names *content shape*.
- **Q2 (Steward skip-link placement):** Option B accepted. Skip-link composes as the first child of `surface-banner`'s `@slot: identity`; the PL fragment includes the default composition so the affordance is automatic.
- **HVD Q1 (active-link visual):** triple-cue (color + weight + teal-400 underline) without ground-shift accepted. Reverses Steward §2.4's "warm-sand active ground-shift" recommendation. Iteration path: add ground-shift on top of triple-cue only if render-time review shows the cue under-anchored.
- **HVD Q2 (separator border weight):** trust render-time review accepted. Sand-100 ships as authored; iteration to sand-200 only if render reads as missing.
- **HVD nav-ext Q1 (`sand-800` vs `sand-700` for rail-row label idle):** `sand-800` accepted (conservative AAA across both possible parent grounds). Lives in [haven-nav-styling.md § 6](haven-nav-styling.md).
- **HVD nav-ext Q2 (multi-line label clamp ceiling):** 2 lines + tooltip + ellipsis past 2 accepted. Lives in [haven-nav-styling.md § 6](haven-nav-styling.md).

## 6. Five-place check (haven-primitive-codification)

Per [`haven-primitive-codification.md`](../../../.claude/rules/haven-primitive-codification.md), four primitives codified by this spec:

### `surface-banner`

| Place | Status | Reference |
|---|---|---|
| 1. PL fragment | ✓ codification slice | [`surface-banner.html`](../../haven-ui/packages/design-system/pattern-library/components/surface-banner.html) |
| 2. components.css rules | pending | `.surface-banner` family — added by codification slice |
| 3. Brand spec entry | ✓ this file §3.1 | — |
| 4. Consumer-surface references | ✓ codification slice | COMPONENT-INDEX (regenerated) + `Lab/haven-ui/CLAUDE.md` family note + cena-sot `procedure.screen.json` audit + sibling SoT-site manifests |
| 5. Render-pipeline verification | pending | PL dev server :5173 + cena-sot rebase emit + HVD visual review |

### `surface-nav`

| Place | Status | Reference |
|---|---|---|
| 1. PL fragment | ✓ codification slice | [`surface-nav.html`](../../haven-ui/packages/design-system/pattern-library/components/surface-nav.html) |
| 2. components.css rules | pending | `.surface-nav` family |
| 3. Brand spec entry | ✓ this file §3.3 | — |
| 4. Consumer-surface references | ✓ codification slice | same as surface-banner |
| 5. Render-pipeline verification | pending | same as surface-banner |

### `surface-footer`

| Place | Status | Reference |
|---|---|---|
| 1. PL fragment | ✓ codification slice | [`surface-footer.html`](../../haven-ui/packages/design-system/pattern-library/components/surface-footer.html) |
| 2. components.css rules | pending | `.surface-footer` family |
| 3. Brand spec entry | ✓ this file §3.4 | — |
| 4. Consumer-surface references | ✓ codification slice | same as surface-banner |
| 5. Render-pipeline verification | pending | same as surface-banner |

### `rail-foot-keyboard-cue`

| Place | Status | Reference |
|---|---|---|
| 1. PL fragment | ✓ codification slice | [`rail-foot-keyboard-cue.html`](../../haven-ui/packages/design-system/pattern-library/components/rail-foot-keyboard-cue.html) |
| 2. components.css rules | pending | `.rail-foot-keyboard-cue` family |
| 3. Brand spec entry | ✓ this file §3.5 | — |
| 4. Consumer-surface references | ✓ codification slice | COMPONENT-INDEX + cena-sot `staff.screen.json` (the rail home) + sibling SoT-site manifests |
| 5. Render-pipeline verification | pending | PL dev server :5173 + cena-sot rebase emit |

## 7. Source provenance

- **2026-06-28 Steward codification proposal (dispatch #04)** — six-primitive family codification proposal (this spec's three chrome members + rail-foot-keyboard-cue + skip-link [routed to haven-nav-styling.md §5] + nav-item-rail-row [routed to haven-nav-styling.md §6]). Aaron approved Q1 (`surface-*` prefix) + Q2 (skip-link as first child of surface-banner identity slot) on 2026-06-28 19:30 PDT.
- **2026-06-28 HVD chrome-family dispatch #1** — the brand-fidelity verdict integrated into this spec; Aaron approved both HVD Q1 (triple-cue without ground-shift) + Q2 (trust render-time review on sand-100 separators) on 2026-06-28 20:01 PDT.
- **2026-06-28 [`catalog-first.md`](../../../.claude/rules/catalog-first.md) primitives-outside-home detection** — the `staff-universe.css` (36KB) + custom Engine B template surfaced as the upstream signal that triggered the codification slice + the rebase plan ([`Plans/cena-sot-document-district-alignment.md`](../../../.claude/plans/cena-sot-document-district-alignment.md)).
- **2026-05-28 (evening) retro** — established Lora-on-warm-document = sand-900, not teal-ink. The chrome H1 color verdict (§3.1.1) inherits this discipline.
- **2026-06-20 outline-button retro** — outline-on-transparent altitude for available-not-executing affordances. The help-affordance treatment (§2.4) inherits this discipline.

## 8. Relationship to other specs

- [`haven-directive-styling.md`](haven-directive-styling.md) — parent discipline at the body layer. The chrome family inherits the same Lora-commands · Source-Sans-works · teal-punctuates discipline that governs SoP directives; the directive spec's §5 third bullet carries the cross-reference back to this file (per HVD dispatch #3 cross-reference pass).
- [`haven-nav-styling.md`](haven-nav-styling.md) — sibling spec. Owns the `nav-*` primitives that compose inside this family's chrome (surface-nav link rows reference the existing nav-link discipline; skip-link visual treatment lives in haven-nav-styling.md §5; nav-item-rail-row composition lives in haven-nav-styling.md §6 — both per HVD dispatch #2).
- [`haven-list-group-styling.md`](haven-list-group-styling.md) — sibling spec. Owns `badge-sm` (composed by `nav-item-rail-row` for the meta chip) + `list-group-*` primitives that compose inside the body, not the chrome.
- [`haven-page-header-trailing-actions-styling.md`](haven-page-header-trailing-actions-styling.md) — sibling spec at the body layer. Page-header is the *body's* header (procedure title + meta strip + breadcrumb), distinct from this spec's chrome family which is the *surface's* header. Body-class vs. surface-class.
- [`haven-docx-typography.md`](haven-docx-typography.md) — sibling spec covering the docx surface; the chrome family is HTML-surface-only (the docx surface has no chrome; SoPs render as document body with no banner/nav/footer). Sibling, not descendant.
