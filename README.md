<div align="center">

```
███████╗██████╗  █████╗      █████╗ ██████╗  ██████╗██╗  ██╗
██╔════╝██╔══██╗██╔══██╗    ██╔══██╗██╔══██╗██╔════╝██║  ██║
███████╗██████╔╝███████║    ███████║██████╔╝██║     ███████║
╚════██║██╔═══╝ ██╔══██║    ██╔══██║██╔══██╗██║     ██╔══██║
███████║██║     ██║  ██║    ██║  ██║██║  ██║╚██████╗██║  ██║
╚══════╝╚═╝     ╚═╝  ╚═╝    ╚═╝  ╚═╝╚═╝  ╚═╝ ╚═════╝╚═╝  ╚═╝
```

**Architect Skills — spaceagency architects (WA)**

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

</div>

> Agents, skills, and rules for architects in **Western Australia** — use with [Claude Desktop](https://claude.ai) or [Claude Code](https://docs.anthropic.com/en/docs/claude-code).

This is a fork of [AlpacaLabsLLC/skills-for-architects](https://github.com/AlpacaLabsLLC/skills-for-architects) amended for WA practice. It teaches Claude spaceagency architects' workflows — WA site analysis, planning envelope against the R-Codes / LPS / MRS, due diligence against Landgate / inHerit / DFES / DWER, NCC-based programming, NATSPEC specifications, materials research, sustainability (Green Star / NABERS / Section J), and presentations.

**7 agents**, **40 skills**, **7 rules**, and **3 hooks** across **9 plugins**. NYC and Uruguay skills are retained at the skill level for international work.

## Architecture

```
Architect Skills
├── /studio                              ← entry point
│
├── agents/
│   ├── site-planner                     4 skills · site research + synthesis
│   ├── wa-planning-expert               3 skills · WA DD + planning envelope + 3D
│   ├── workplace-strategist             2 skills · occupancy (NCC) + programming
│   ├── product-and-materials-researcher 5 skills · find, extract, tag
│   ├── ffe-designer                     7 skills · schedule, QA, export
│   ├── sustainability-specialist        4 skills · EPDs, GWP, Green Star / NABERS
│   └── brand-manager                    2 skills · decks + palettes
│
├── plugins/
│   ├── 00-due-diligence                 8 skills · WA (1) + NYC legacy (7)
│   ├── 01-site-planning                 4 skills · WA defaults, intl fallback
│   ├── 02-zoning-analysis               4 skills · WA (1) + NYC + UY + 3D viewer
│   ├── 03-programming                   2 skills · NCC / AU programming [partial WA]
│   ├── 04-specifications                1 skill · CSI MasterFormat [NATSPEC pending]
│   ├── 05-sustainability                4 skills · EPDs (international + AU context)
│   ├── 06-materials-research           12 skills · jurisdiction-neutral
│   ├── 07-presentations                 3 skills · jurisdiction-neutral
│   └── 08-dispatcher                    2 skills
│
├── rules/
│   ├── units-and-measurements           · metric (SI), GFA/NLA/NSA, AHD
│   ├── code-citations                   · NCC 2022, R-Codes, AS/NZS, WA legislation
│   ├── professional-disclaimer          · Architects Act 2004 (WA) framing
│   ├── natspec-formatting               · 4-digit worksections, 3-part structure
│   ├── terminology                      · Australian English, WA planning & heritage
│   ├── output-formatting                · Landgate / ABS / BoM / inHerit sources
│   └── transparency                     · NCC / R-Codes / LPS / SAT citation
│
└── hooks/
    ├── post-write-disclaimer-check      · NCC / R-Codes / BAL / heritage keywords
    ├── post-output-metadata             · YAML front matter stamping
    └── pre-commit-spec-lint             · NATSPEC worksection format
```

**Agents** orchestrate skills across plugins — they assess your input, choose a path, and exercise judgment. **Skills** are single-purpose tools invoked with a slash command. **Rules** govern every output. **Hooks** are event-driven automations. Skills are grouped into **plugins** (installable bundles organized by project lifecycle).

## Quick Start

### Install

**Claude Desktop:** Open **Customize** → **Browse plugins** → **+** → **Add marketplace from GitHub** → enter `SpaceagencyArchitects/skills-for-architects`

**Claude Code:**
```bash
claude plugin marketplace add SpaceagencyArchitects/skills-for-architects
claude plugin install 02-zoning-analysis@skills-for-architects
```

### Use

Type `/studio` followed by what you need. The router reads your request and hands off to the right agent or skill.

```
/studio 48 Swanbourne St, Fremantle WA 6160
/studio plot ratio for an R-AC3 site in Mixed Use, City of Fremantle
/studio I need a space program for 40 people, 3 days hybrid
/studio find an oak veneer task chair under $1200 AUD
/studio make a presentation from this feasibility report
```

Type `/skills` for the full menu. Or call any skill directly by name (e.g. `/planning-analysis-wa`).

## Agents

Agents are the orchestration layer. Describe your task — the agent decides which skills to call, in what order, and what judgment to apply.

| Agent | Domain | What it does |
|-------|--------|-------------|
| [site-planner](./agents/site-planner.md) | Site Planning | Climate (BoM), mobility (Transperth), demographics (ABS), heritage / context (inHerit, SLWA, Trove) — synthesised into a single site brief |
| [wa-planning-expert](./agents/wa-planning-expert.md) | **WA DD + Planning** (default) | Full WA due diligence + planning envelope — title, heritage, DA history, R-Codes / LPS envelope, 3D viewer, approval pathway |
| [workplace-strategist](./agents/workplace-strategist.md) | Programming | Headcount + work style → space program, occupancy (NCC 2022 Section D), room schedule |
| [product-and-materials-researcher](./agents/product-and-materials-researcher.md) | Materials Research | Finds products from a brief, extracts specs from URLs / PDFs, tags and classifies, finds alternatives |
| [ffe-designer](./agents/ffe-designer.md) | FF&E Design | Builds clean schedules from messy inputs, composes room packages, runs QA, exports to dealer formats |
| [sustainability-specialist](./agents/sustainability-specialist.md) | Sustainability | EPD research, GWP comparison, Green Star / NABERS / NCC Section J eligibility, spec thresholds |
| [brand-manager](./agents/brand-manager.md) | Presentations | Owns visual identity — builds decks, creates palettes, QAs deliverables for presentation readiness |

See the [agents directory](./agents) for full workflows and handoff logic.

## Plugins & Skills

Organized by project lifecycle — from due diligence through delivery.

| # | Plugin | Skills | Description |
|---|--------|--------|-------------|
| 0 | [Due Diligence](./plugins/00-due-diligence) | 8 | WA: Landgate + inHerit + LGA DA + SAT + DWER + DFES + AHIS + NatureMap. Legacy NYC skills retained |
| 1 | [Site Planning](./plugins/01-site-planning) | 4 | BoM, Transperth, ABS, inHerit / SLWA / Trove — with international fallback |
| 2 | [Zoning & Planning Analysis](./plugins/02-zoning-analysis) | 4 | WA planning envelope (MRS / LPS / R-Codes); NYC (PLUTO); Uruguay (TONE); 3D viewer (metric + imperial) |
| 3 | [Programming](./plugins/03-programming) | 2 | Workplace space programs + occupancy loads (NCC migration pending) |
| 4 | [Specifications](./plugins/04-specifications) | 1 | CSI outline specs (NATSPEC migration pending) |
| 5 | [Sustainability](./plugins/05-sustainability) | 4 | EPD parsing / research / comparison / spec; international + AU context |
| 6 | [Materials Research](./plugins/06-materials-research) | 12 | FF&E product research, spec extraction, cleanup, image processing |
| 7 | [Presentations](./plugins/07-presentations) | 3 | HTML slide decks, colour palettes, image resizing |
| 8 | [Dispatcher](./plugins/08-dispatcher) | 2 | Studio router (`/studio`) and help menu (`/skills`) |

<details>
<summary><strong>All 40 skills</strong></summary>

### Due Diligence

| Skill | Description |
|---|---|
| [`/wa-property-report`](./plugins/00-due-diligence/skills/wa-property-report) | **WA** — comprehensive DD: Landgate, inHerit, LGA DA, SAT, DWER, DFES, AHIS, NatureMap |
| [`/nyc-landmarks`](./plugins/00-due-diligence/skills/nyc-landmarks) | Legacy — LPC landmark and historic district check |
| [`/nyc-dob-permits`](./plugins/00-due-diligence/skills/nyc-dob-permits) | Legacy — DOB permit and filing history |
| [`/nyc-dob-violations`](./plugins/00-due-diligence/skills/nyc-dob-violations) | Legacy — DOB and ECB violations |
| [`/nyc-acris`](./plugins/00-due-diligence/skills/nyc-acris) | Legacy — ACRIS property transaction records |
| [`/nyc-hpd`](./plugins/00-due-diligence/skills/nyc-hpd) | Legacy — HPD violations, complaints, registration |
| [`/nyc-bsa`](./plugins/00-due-diligence/skills/nyc-bsa) | Legacy — BSA variances and special permits |
| [`/nyc-property-report`](./plugins/00-due-diligence/skills/nyc-property-report) | Legacy — combined NYC property report |

### Site Planning

| Skill | Description |
|---|---|
| [`/environmental-analysis`](./plugins/01-site-planning/skills/environmental-analysis) | Climate (BoM), bushfire (DFES), coastal (SPP 2.6), seismic, soil, contamination, ASS |
| [`/mobility-analysis`](./plugins/01-site-planning/skills/mobility-analysis) | Transperth, Main Roads WA, cycling network, Walk Score, airport access |
| [`/demographics-analysis`](./plugins/01-site-planning/skills/demographics-analysis) | ABS Census, .id Community, REIWA, WA Tomorrow projections |
| [`/history`](./plugins/01-site-planning/skills/history) | Traditional Owners, inHerit, SLWA / Trove, architectural character, planned development |

### Zoning & Planning Analysis

| Skill | Description |
|---|---|
| [`/planning-analysis-wa`](./plugins/02-zoning-analysis/skills/planning-analysis-wa) | **WA** — planning envelope: MRS / LPS zone, R-Code, plot ratio, height, setbacks, heritage, BAL |
| [`/zoning-analysis-nyc`](./plugins/02-zoning-analysis/skills/zoning-analysis-nyc) | Legacy — NYC buildable envelope from PLUTO |
| [`/zoning-analysis-uruguay`](./plugins/02-zoning-analysis/skills/zoning-analysis-uruguay) | Maldonado, Uruguay |
| [`/zoning-envelope`](./plugins/02-zoning-analysis/skills/zoning-envelope) | Interactive 3D envelope viewer — metric or imperial |

### Programming

| Skill | Description |
|---|---|
| [`/workplace-programmer`](./plugins/03-programming/skills/workplace-programmer) | Space programs from headcount and work style |
| [`/occupancy-calculator`](./plugins/03-programming/skills/occupancy-calculator) | IBC occupancy loads, egress, plumbing fixture counts (NCC migration pending) |

### Specifications

| Skill | Description |
|---|---|
| [`/spec-writer`](./plugins/04-specifications/skills/spec-writer) | CSI outline specs — MasterFormat, three-part sections (NATSPEC migration pending) |

### Sustainability

| Skill | Description |
|---|---|
| [`/epd-parser`](./plugins/05-sustainability/skills/epd-parser) | Extract data from EPD PDFs — GWP, life cycle stages, certifications |
| [`/epd-research`](./plugins/05-sustainability/skills/epd-research) | Search EC3, UL, Environdec for EPDs by material or category |
| [`/epd-compare`](./plugins/05-sustainability/skills/epd-compare) | Side-by-side environmental impact comparison |
| [`/epd-to-spec`](./plugins/05-sustainability/skills/epd-to-spec) | Specs with EPD requirements and GWP thresholds |

### Materials Research

| Skill | Description |
|---|---|
| [`/product-research`](./plugins/06-materials-research/skills/product-research) | Find products from a design brief |
| [`/product-spec-bulk-fetch`](./plugins/06-materials-research/skills/product-spec-bulk-fetch) | Extract specs from product URLs at scale |
| [`/product-data-cleanup`](./plugins/06-materials-research/skills/product-data-cleanup) | Normalise messy FF&E schedules |
| [`/product-spec-pdf-parser`](./plugins/06-materials-research/skills/product-spec-pdf-parser) | Extract specs from PDF catalogues |
| [`/product-image-processor`](./plugins/06-materials-research/skills/product-image-processor) | Batch download, resize, remove backgrounds |
| [`/product-data-import`](./plugins/06-materials-research/skills/product-data-import) | Import raw product data into the master schedule |
| [`/master-schedule`](./plugins/06-materials-research/skills/master-schedule) | Connect a product library sheet to the project |
| [`/product-enrich`](./plugins/06-materials-research/skills/product-enrich) | Auto-tag products with categories, colours, materials |
| [`/product-match`](./plugins/06-materials-research/skills/product-match) | Find similar products |
| [`/product-pair`](./plugins/06-materials-research/skills/product-pair) | Suggest complementary products |
| [`/csv-to-sif`](./plugins/06-materials-research/skills/csv-to-sif) | Convert CSV to SIF for dealer systems |
| [`/sif-to-csv`](./plugins/06-materials-research/skills/sif-to-csv) | Convert SIF to readable spreadsheets |

### Presentations

| Skill | Description |
|---|---|
| [`/slide-deck-generator`](./plugins/07-presentations/skills/slide-deck-generator) | HTML slide decks — editorial layout, 22 slide types |
| [`/color-palette-generator`](./plugins/07-presentations/skills/color-palette-generator) | Colour palettes from descriptions, images, or hex codes |
| [`/resize-images`](./plugins/07-presentations/skills/resize-images) | Batch-resize photos for web, social, slides, and print |

### Dispatcher

| Skill | Description |
|---|---|
| [`/studio`](./plugins/08-dispatcher/skills/studio) | Smart router — describe your task, get routed |
| [`/skills`](./plugins/08-dispatcher/skills/skills-menu) | Help menu — shows all skills and agents |

</details>

## Rules

Always-on conventions that govern every output — loaded automatically, not invoked. **Default jurisdiction: Western Australia.**

| Rule | What it governs |
|---|---|
| [units-and-measurements](./rules/units-and-measurements.md) | Metric (SI), area types (GFA / NLA / NSA / Plot Ratio), dimensions (mm / m), AHD levels, AUD |
| [code-citations](./rules/code-citations.md) | NCC 2022, R-Codes, AS / AS-NZS, LPS / LPP, WA legislation — edition years, clause notation |
| [professional-disclaimer](./rules/professional-disclaimer.md) | *Architects Act 2004* (WA) framing; what AI outputs can and cannot claim |
| [natspec-formatting](./rules/natspec-formatting.md) | NATSPEC 4-digit worksections, three-part structure, AS / AS-NZS references |
| [terminology](./rules/terminology.md) | Australian English, WA planning vocabulary, heritage terms, material names |
| [output-formatting](./rules/output-formatting.md) | Tables, source attribution (ABS / Landgate / BoM / inHerit / DFES), file naming |
| [transparency](./rules/transparency.md) | Show your work — link NCC / R-Codes / LPS / SAT sources, expose inputs |

## Hooks

Event-driven automations — opt-in via Claude Code settings.

| Hook | Event | What it does |
|---|---|---|
| [post-write-disclaimer-check](./hooks/post-write-disclaimer-check.sh) | After Write | Warns if regulatory output (NCC / R-Codes / LPS / BAL / heritage / WAPC / SAT) is missing the disclaimer |
| [post-output-metadata](./hooks/post-output-metadata.sh) | After Write | Stamps markdown reports with YAML front matter |
| [pre-commit-spec-lint](./hooks/pre-commit-spec-lint.sh) | Before git commit | Flags malformed NATSPEC worksection numbers |

See the [hooks directory](./hooks) for installation instructions.

## Status — WA migration

This fork is migrating the original NYC-focused Architecture Studio to WA practice. Current state:

| Layer | Status |
|---|---|
| Rules | ✅ Complete — 7 rules amended, NATSPEC added, CSI superseded |
| Hooks | ✅ Complete — WA keyword detection, NATSPEC lint |
| Plugin 00 — Due Diligence | ✅ WA skill added; NYC legacy retained |
| Plugin 01 — Site Planning | ✅ All 4 skills amended for WA defaults |
| Plugin 02 — Zoning / Planning | ✅ WA skill added; NYC + Uruguay retained |
| Plugin 03 — Programming | ⏳ Pending — IBC → NCC, ft² → m² migration |
| Plugin 04 — Specifications | ⏳ Pending — CSI → NATSPEC spec writer |
| Plugin 05 — Sustainability | ⏳ Pending — Green Star / NABERS / NCC Section J in skill files |
| Plugin 06 — Materials Research | ✅ Jurisdiction-neutral; no changes needed |
| Plugin 07 — Presentations | ✅ Jurisdiction-neutral; no changes needed |
| Plugin 08 — Dispatcher | ✅ Router + menu updated |
| Agents | ✅ wa-planning-expert added; other agents updated for WA context; legacy NYC agent removed (NYC skills retained) |

## Contributing

Want to add a skill — a new LGA's LPS file, a new WA data source, a new workflow?

1. Create your skill in the appropriate plugin folder, following the existing structure
2. Each skill needs a `SKILL.md` (workflow + output format + sources), a `README.md`, and any supporting reference files in subfolders
3. Adhere to `rules/` — default metric, Australian English, proper WA source citation, professional disclaimer
4. Commit and push

## License

MIT — see [LICENSE](LICENSE).

---

Fork of [AlpacaLabsLLC/skills-for-architects](https://github.com/AlpacaLabsLLC/skills-for-architects) by [ALPA](https://alpa.llc). Amended for Western Australian practice by spaceagency architects.
