# Agents

Agents are autonomous specialists that orchestrate multiple skills to complete a complex task. Unlike skills (single-purpose, invoked directly), agents assess the situation, choose a path, and exercise judgment.

**Default jurisdiction: Western Australia.** All agents are configured for WA practice.

## Available Agents

| Agent | Domain | Skills it orchestrates |
|---|---|---|
| [site-planner](./site-planner.md) | Site Planning | environmental-analysis, mobility-analysis, demographics-analysis, history |
| [wa-planning-expert](./wa-planning-expert.md) | **WA Due Diligence + Planning Envelope** (default) | wa-property-report, planning-analysis-wa, zoning-envelope |
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

For international projects, the legacy NYC skills (in plugins 00 and 02) and the `/zoning-analysis-uruguay` skill are available — call them directly. The rest of the chain is jurisdiction-neutral.

Each agent works standalone. Use one, several, or all depending on the task.

## Usage

Agents are reference documents — they define how Claude should behave when delegating complex work. To invoke an agent's behavior, describe your task and Claude will follow the agent's workflow.
