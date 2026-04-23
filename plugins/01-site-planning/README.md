# Site Planning

A Claude Code plugin for site research and early-stage feasibility. Give it a WA address and it builds a comprehensive site analysis — climate, transit, demographics, neighbourhood character — using public Australian data sources. What used to take days of manual research happens in minutes.

**Default jurisdiction: Western Australia.**

## The Problem

Early-stage site analysis requires pulling data from dozens of sources — BoM for climate, Transperth for transit, ABS for demographics, inHerit and SLWA for heritage, DFES for bushfire, REIWA for housing market, the LGA's IntraMaps for everything local. Architects and developers spend days assembling this into a coherent picture before design can even begin.

## The Solution

Four research skills that each investigate a different dimension of a site. Each skill searches authoritative public data sources, synthesises findings, and outputs a structured markdown report.

```
                                    ┌─────────────┐
                                    │   Address   │
                                    └──────┬──────┘
                                           │
         ┌─────────────┬───────────────────┼───────────────────┐
         │             │                   │                   │
         ▼             ▼                   ▼                   ▼
  ┌────────────┐ ┌────────────┐ ┌──────────────────┐ ┌──────────────────┐
  │Environmental│ │  Mobility  │ │  Demographics    │ │  History         │
  │            │ │            │ │                  │ │                  │
  │ Climate    │ │ Transperth │ │ Population (ABS) │ │ Traditional      │
  │ (BoM)      │ │ Walk Score │ │ Income, age      │ │ Owners, heritage │
  │ Bushfire   │ │ Bike       │ │ Housing market   │ │ Architectural    │
  │ (DFES)     │ │ network    │ │ (REIWA)          │ │ character        │
  │ Coastal    │ │ Roads      │ │ Employment       │ │ Landmarks        │
  │ (SPP 2.6)  │ │ (Main Rds) │ │                  │ │ Adjacent uses    │
  │ Soil, ASS  │ │ Airports   │ │                  │ │ Planned devt.    │
  └─────┬──────┘ └─────┬──────┘ └────────┬─────────┘ └────────┬─────────┘
        │              │                 │                     │
        ▼              ▼                 ▼                     ▼
  ┌────────────────────────────────────────────────────────────────────┐
  │                      Markdown Reports                              │
  │                                                                    │
  │  One file per analysis, each with:                                 │
  │  Key Metrics table + detailed sections + sources                   │
  └────────────────────────────────────────────────────────────────────┘
```

## Data Flow

### Input

Every research skill takes a single input: **an address or location**. It asks once, then researches autonomously without interrupting.

### Research sources (WA defaults)

Each skill searches authoritative governmental and non-profit sources:

| Skill | Primary sources |
|---|---|
| Environmental | BoM (climate), DFES (bushfire), DWER (contamination, ASS), Geoscience Australia (seismic, elevation), NatureMap (DBCA), DPIRD (soils), WAPC MyPlan |
| Mobility | Transperth, PTA WA, Main Roads WA, Department of Transport WA (cycling network), Walk Score |
| Demographics | ABS Census, .id Community, REIWA, WA Tomorrow projections, RBA |
| History | inHerit, LGA Local Heritage Survey, SLWA, Trove (NLA), AHIS, SWALSC, LGA DA registers |

### Output

Each skill produces a **structured markdown report** with a Key Metrics summary table followed by detailed sections, sources, and a Gaps & Caveats section. Reports save to local files.

## Skills

| Skill | Description |
|---|---|
| [environmental-analysis](skills/environmental-analysis/) | Climate, bushfire (BPA), coastal, seismic, soil, ASS, vegetation, contamination, topography |
| [mobility-analysis](skills/mobility-analysis/) | Transperth (train + bus), Walk Score, road network, airport access, cycling and pedestrian infrastructure |
| [demographics-analysis](skills/demographics-analysis/) | ABS Census suburb / SA2 profile, REIWA market data, employment, age, housing |
| [history](skills/history/) | Traditional Owner context, Aboriginal heritage, European development history, heritage listings, architectural character, planned development |

## Agent

For full site context (all four skills run in parallel with a synthesised brief), see the [Site Planner](../../agents/site-planner.md) agent.

## Related

- [`/wa-property-report`](../00-due-diligence/skills/wa-property-report/) — adds title, encumbrances, DA history, and detailed environmental DD
- [`/planning-analysis-wa`](../02-zoning-analysis/skills/planning-analysis-wa/) — the planning envelope for the same site

A typical pre-design workup is: site planning (this plugin) → due diligence (`/wa-property-report`) → planning envelope (`/planning-analysis-wa`).

## Install

**Claude Desktop:**

1. Open the **+** menu → **Add marketplace from GitHub**
2. Enter `SpaceagencyArchitects/skills-for-architects`
3. Install the **Site Planning** plugin

**Claude Code (terminal):**

```bash
claude plugin marketplace add SpaceagencyArchitects/skills-for-architects
claude plugin install 01-site-planning@skills-for-architects
```

**Manual:**

```bash
git clone https://github.com/SpaceagencyArchitects/skills-for-architects.git
ln -s $(pwd)/skills-for-architects/plugins/01-site-planning/skills/environmental-analysis ~/.claude/skills/environmental-analysis
```

## License

MIT — fork of [AlpacaLabsLLC/skills-for-architects](https://github.com/AlpacaLabsLLC/skills-for-architects), amended for Western Australian practice.
