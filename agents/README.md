# Agents

Agents are autonomous specialists that orchestrate multiple skills to complete a complex task. Unlike skills (single-purpose, invoked directly), agents assess the situation, choose a path, and exercise judgment.

**Default jurisdiction: Western Australia.** The WA-specific agent is the default for local practice. Legacy NYC and other-jurisdiction agents are retained for international work.

## Available Agents

| Agent | Domain | Skills it orchestrates |
|---|---|---|
| [site-planner](./site-planner.md) | Site Planning | environmental-analysis, mobility-analysis, demographics-analysis, history |
| [wa-planning-expert](./wa-planning-expert.md) | **WA Due Diligence + Planning Envelope** (default) | wa-property-report, planning-analysis-wa, zoning-envelope |
| [nyc-zoning-expert](./nyc-zoning-expert.md) | NYC Due Diligence + Zoning (legacy / international) | nyc-landmarks, nyc-dob-permits, nyc-dob-violations, nyc-acris, nyc-hpd, nyc-bsa, nyc-property-report, zoning-analysis-nyc, zoning-envelope |
| [workplace-strategist](./workplace-strategist.md) | Programming | occupancy-calculator, workplace-programmer |
| [product-and-materials-researcher](./product-and-materials-researcher.md) | Materials Research | product-research, product-spec-bulk-fetch, product-spec-pdf-parser, product-match, product-enrich |
| [ffe-designer](./ffe-designer.md) | FF&E Design | product-pair, product-data-cleanup, product-data-import, product-enrich, product-image-processor, csv-to-sif, sif-to-csv |
| [sustainability-specialist](./sustainability-specialist.md) | Sustainability (Green Star / NABERS / NCC Section J) | epd-research, epd-compare, epd-parser, epd-to-spec |
| [brand-manager](./brand-manager.md) | Presentations | slide-deck-generator, color-palette-generator, resize-images |

## How Agents Differ from Skills

| Layer | Behavior | Example |
|---|---|---|
| **Skill** | Does one thing when invoked | `/wa-property-report` walks the WA DD workflow |
| **Agent** | Assesses the input, chooses a path, orchestrates skills, exercises judgment | The WA Planning Expert decides whether to run full DD + envelope + 3D, or just an envelope check, based on the user's question |

## How They Work Together

```
Site address (WA)
      ↓
Site Planner
      → climate, transit, demographics, neighbourhood context
      ↓
WA Planning Expert
      → property DD, planning envelope, 3D viewer, approval pathway
      ↓
Workplace Strategist
      → occupancy compliance (NCC), zone allocation, room schedule
      ↓
Product & Materials Researcher
      → finds products, extracts specs, tags and classifies
      ↓
Sustainability Specialist
      → embodied carbon, GWP comparison, Green Star / NABERS / Section J
      ↓
FF&E Designer
      → composes room packages, builds schedule, runs QA, exports
      ↓
Brand Manager
      → builds the presentation, ensures visual consistency
```

For NYC or Uruguay projects, swap **WA Planning Expert** for **NYC Zoning Expert** or `/zoning-analysis-uruguay`. The rest of the chain is jurisdiction-neutral.

Each agent works standalone. Use one, several, or all depending on the task.

## Usage

Agents are reference documents — they define how Claude should behave when delegating complex work. To invoke an agent's behavior, describe your task and Claude will follow the agent's workflow.
