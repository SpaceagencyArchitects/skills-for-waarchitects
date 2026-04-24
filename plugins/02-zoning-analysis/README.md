# Zoning & Planning Analysis

A Claude Code plugin for planning envelope analysis. Give it a site and it calculates the buildable envelope — plot ratio / FAR, height, setbacks, open space, permitted uses, parking, and incentives — using public data sources and the applicable planning instruments.

**Default jurisdiction: Western Australia.** A Uruguay (Maldonado) skill is retained as a legacy reference.

## The Problem

Envelope analysis is one of the most time-consuming and error-prone tasks in early-stage design. In WA, the applicable controls are layered — MRS (or regional scheme) → LPS → LPPs → R-Codes → SPPs — and interact in ways that are easy to get wrong. A single missed LPP clause or misread heritage overlay can cost weeks of design time.

## The Solution

Per-jurisdiction skills that: identify the applicable layers, load the relevant bundled regulations, apply the rules to the site, and produce a structured markdown report + a JSON envelope block for 3D visualisation.

```
                      ┌──────────────────────┐
                      │  Site address + LGA  │
                      └──────────┬───────────┘
                                 │
          ┌──────────────────────┼──────────────────────┐
          │                      │                      │
          ▼                      ▼                      ▼
  ┌─────────────┐        ┌─────────────┐        ┌─────────────┐
  │  MRS zone   │        │  LPS zone   │        │  R-Code     │
  │  (MyPlan)   │        │  (LGA maps) │        │  (LPS map)  │
  └──────┬──────┘        └──────┬──────┘        └──────┬──────┘
         │                      │                      │
         └──────────────────────┼──────────────────────┘
                                │
                                ▼
                ┌──────────────────────────────┐
                │  Bundled rules               │
                │  - R-Codes Vol. 1 & 2        │
                │  - Use classes P/D/A/X       │
                │  - Bundled LPS file per LGA  │
                │  - MRS zones reference       │
                └──────────────┬───────────────┘
                               │
                               ▼
                ┌──────────────────────────────┐
                │  Planning analysis report    │
                │                              │
                │  • Plot ratio / GFA / yield  │
                │  • Height + storeys          │
                │  • Setbacks                  │
                │  • Open space + deep soil    │
                │  • Car parking               │
                │  • Permissibility            │
                │  • Responsible Authority     │
                │  • Heritage / BAL / risk     │
                │  • Scenarios                 │
                └──────────────┬───────────────┘
                               │
                               ▼
                ┌──────────────────────────────┐
                │  /zoning-envelope            │
                │                              │
                │  Interactive 3D HTML viewer  │
                │  (Three.js, unit-aware)      │
                └──────────────────────────────┘
```

## Data Sources

| Jurisdiction | Primary sources |
|---|---|
| Western Australia | [Landgate](https://maps.landgate.wa.gov.au/maps-landgate/registered/), [WAPC MyPlan](https://app-ll.planning.wa.gov.au/MyPlan/), LGA IntraMaps viewers, [inHerit](https://inherit.dplh.wa.gov.au/), [DFES BPA map](https://www.dfes.wa.gov.au/), bundled R-Codes + LPS files |
| Uruguay (Maldonado) | Bundled TONE regulation files |

## Skills

| Skill | Jurisdiction | Description |
|---|---|---|
| [planning-analysis-wa](skills/planning-analysis-wa/) | WA (default) | Envelope analysis for any site in Western Australia — MRS / LPS zone, R-Code, plot ratio, height, setbacks, heritage, BAL. First LGA bundled: City of Fremantle LPS4 |
| [zoning-analysis-uruguay](skills/zoning-analysis-uruguay/) | Maldonado, UY | Buildable envelope — FOS, FOT, height, setbacks |
| [zoning-envelope](skills/zoning-envelope/) | Any | Interactive 3D envelope viewer — generates a self-contained HTML file from any planning analysis report. Supports both metric and imperial units |

## Adding a new LGA (WA)

The WA skill bundles City of Fremantle LPS4 to start. To add a new LGA:

1. Create `skills/planning-analysis-wa/planning-rules/lps-[lga-slug].md`
2. Follow the structure of `lps-city-of-fremantle-lps4.md` — zones, heritage overlays, LPPs, Responsible Authority notes
3. Do **not** embed live plot ratio / setback values from memory — the file is a framework; specific values are derived from the gazetted Scheme at analysis time

## Install

**Claude Code (interactive):**

1. Start Claude Code by running `claude` in a terminal
2. Inside Claude Code, run `/plugin marketplace add SpaceagencyArchitects/skills-for-architects`
3. Run `/plugin` and go to the **Discover** tab to browse and install the **Zoning Analysis** plugin
4. Run `/reload-plugins` to activate

**Claude Code (terminal):**

```bash
claude plugin marketplace add SpaceagencyArchitects/skills-for-architects
claude plugin install 02-zoning-analysis@skills-for-architects
```

**Manual:**

```bash
git clone https://github.com/SpaceagencyArchitects/skills-for-architects.git
ln -s $(pwd)/skills-for-architects/plugins/02-zoning-analysis/skills/planning-analysis-wa ~/.claude/skills/planning-analysis-wa
```

## License

MIT — fork of [AlpacaLabsLLC/skills-for-architects](https://github.com/AlpacaLabsLLC/skills-for-architects), amended for Western Australian practice.
