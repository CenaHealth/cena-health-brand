# Partner logos

Third-party organization marks — payers, health systems, public bodies, funders — extracted from the retired Haven Figma file (`opvzZC7Ds38MNwRFvFuKZe`, page `Partners`, node `303:1201`).

## Trademark gate — read before using these outside an internal deck

These are **other organizations' trademarks**. Using them in an internal deck or a working document is ordinary. Placing them on a public page, a marketing surface, or anything that implies partnership or endorsement is a **different claim** and needs checking first. Agreed with Aaron 2026-09-09.

## Current state — partial, by choice

Two of 33 extracted, because those are the two with live deck need (`Lab/deliverable-pl/decks/cena/northwell-transition`). The other 31 are catalogued and reachable; nobody has needed them yet.

| Logo | Files | State |
|---|---|---|
| Northwell Health | `northwell-health.svg` | Clean single SVG. Ready. |
| Mass General Brigham | `mass-general-brigham-{1..4}.svg` | **Four fragments, not assembled.** Use `mass-general-brigham-assembled.png` (174×102) until someone composes the SVG. |

`*-assembled.png` files are reference renders of the whole node, kept so the intended result is visible without opening Figma.

## How to extract the remaining 31 — method, proven 2026-09-09

**One `download_assets` call per logo node.** Do not call it on the page node: the page's subtree trips a 20-image cap and returns unrelated raster fills with `svgAssets: []`, which reads as "these logos are bitmaps" and is wrong.

```
mcp__claude_ai_Figma__download_assets(fileKey="opvzZC7Ds38MNwRFvFuKZe", nodeId="<symbol id>")
```

Two things the method has to handle:

- **A node can return several SVG fragments rather than one logo** (Mass General Brigham returned four). Check the count; if >1, either compose them or fall back to the node's `export` render.
- **URLs are short-lived.** Download immediately in the same pass.

Node ids for all 33 are in the metadata dump of `303:1201`. Most are normalized to 54px height; Mass General Brigham (`816:237`) and Northwell (`816:259`) are later additions at ~101–112px and are the exceptions.

## Roster

Payers — Aetna, Cigna, United Healthcare, Optum, Evernorth, Carelon, TRICARE, HUSKY Health Connecticut.
Systems — Northwell, Mass General Brigham, Cedars-Sinai, Hartford Healthcare, Trinity Health of New England, Trinity Health St Francis, UConn Health, Vanderbilt Health, AdventHealth, Providence Health, Ardent Health, LifePoint Health.
Public — CMS, HHS, NIH-OADR.
Platform — Epic.
Funders and community — Gawlicki Family Foundation, Forge City Works, Connecticut Community Care, Connecticut Health Council, NSIPA, DHJ Services, Johnson & Johnson, Nourish Autoimmunity Challenge.
