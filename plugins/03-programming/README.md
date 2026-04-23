# Programming

A Claude Code plugin for architectural space programming. Calculate occupancy loads and build workplace space programs through guided conversation — from headcount and work style to zone splits, accommodation schedules, and desk counts, backed by industry research.

**Default jurisdiction: Western Australia.** Metric throughout, NLA as the default area convention, NCC 2022 for occupancy calculations.

## The Problem

Space programming sits at the intersection of building code, workplace strategy, and client needs. Architects need to calculate occupancy loads from the NCC, then translate vague client requirements ("we need space for 200 people, hybrid work") into a detailed room-by-room program with defensible ratios. This requires expertise in code compliance, workplace typologies, and the math to make it all add up to the available NLA.

## The Solution

Two conversational skills — one for code-driven occupancy calculation (NCC-first, IBC fallback), one for strategy-driven workplace programming — that guide the designer through a structured process while handling the math, code lookups, and tradeoff analysis.

```
┌──────────────────────────────────────────────────────────────┐
│                    DESIGNER INPUT                            │
│                                                              │
│  "200 people, hybrid 3 days/wk, 2,800 m² NLA, tech company" │
└──────────────────────────┬───────────────────────────────────┘
                           │
              ┌────────────┴────────────┐
              ▼                         ▼
   ┌───────────────────┐     ┌───────────────────┐
   │    Occupancy      │     │    Workplace      │
   │    Calculator     │     │    Programmer     │
   │                   │     │                   │
   │  NCC 2022 Vol. 1  │     │  Industry data:   │
   │  Part D3D15       │     │  • Zone ratios    │
   │  (WA default)     │     │  • Desk standards │
   │                   │     │  • Room standards │
   │  IBC fallback     │     │  • Hybrid adj.    │
   │  for intl work    │     │                   │
   │                   │     │  4 phases:        │
   │  4 phases:        │     │  Discover →       │
   │  Discover →       │     │  Synthesise →     │
   │  Calculate →      │     │  Detail →         │
   │  Detail →         │     │  Refine           │
   │  Refine           │     │                   │
   │                   │     │  Outputs:         │
   │  Outputs:         │     │  • Zone splits    │
   │  • Occupant load  │     │  • Room schedule  │
   │  • Egress width   │     │  • Desk breakdown │
   │  • Exit count     │     │  • Support spaces │
   │  • Sanitary fxt.  │     │                   │
   └─────────┬─────────┘     └─────────┬─────────┘
             │                         │
             ▼                         ▼
   ┌───────────────────┐     ┌───────────────────┐
   │  occupancy.json   │     │   program.json    │
   │  + markdown       │     │  + markdown       │
   │    report         │     │    report         │
   │                   │     │  + CSV export     │
   └───────────────────┘     └───────────────────┘
```

## Data Flow

### Occupancy Calculator

| Phase | What happens |
|-------|-------------|
| **Discover** | Conversational — learns about the building, identifies NCC Class(es), clarifies area basis (NCC floor area vs GFA vs NLA) |
| **Calculate** | Breaks space into parts by NCC Class, applies D3D15 load factors, sums occupant loads |
| **Detail** | Calculates egress (NCC D3D3/D3D10/D3D11/D3D6/D3D7), indicative sanitary fixtures (NCC Part F4/F6 + AS/NZS 3500.1) |
| **Refine** | Handles adjustments, what-ifs, mixed-use scenarios |

Applies NCC 2022 Vol. 1 Part D3D15 load factors (Western Australia and other AU states). IBC 2021 fallback retained for international projects. Handles floor area definition, mixed classes, mezzanines, fixed seating.

Outputs `occupancy.json` for state persistence and a markdown report with the WA professional disclaimer.

> **Note — NCC data completeness:** The bundled NCC load-factor file (`data/ncc-occupancy-load-factors.json`) is currently a structured stub. Several entries are verified (Class 5 office = 10 m²/person); most are marked TODO pending verification against the gazetted NCC 2022 Vol. 1 Schedule 7 D3D15. When the skill encounters a TODO entry it will prompt the user to supply the value from the current NCC text — it will not guess.

### Workplace Programmer

| Phase | What happens |
|-------|-------------|
| **Discover** | Learns the organisation — headcount, work style, culture, hybrid policy |
| **Synthesise** | Forms custom recommendation with zone splits across 5 zones |
| **Detail** | Proposes desk breakdown, accommodation schedule, and support spaces |
| **Refine** | Handles adjustments with tradeoff explanations |

Programs space across five zones:

| Zone | Typical range | What's in it |
|---|---|---|
| Work | 13–46% | Assigned desks, private offices, benching |
| Meeting | 12–25% | Meeting rooms, huddles, phone booths |
| Common | 5–30% | Café, lounge, pantry, reception |
| Circulation | 27% (fixed) | Corridors, lobbies |
| BOH | 2–12% | IT, storage, printing, facilities |

Outputs `program.json` for state persistence, a markdown report, and optional CSV export.

## Skills

| Skill | Description |
|---|---|
| [occupancy-calculator](skills/occupancy-calculator/) | Occupancy load calculator — NCC 2022 Vol. 1 Part D3 (WA default) with IBC 2021 fallback. Per-area loads, egress, sanitary fixtures |
| [workplace-programmer](skills/workplace-programmer/) | Workplace strategy consultant — zone splits, accommodation schedules, desk counts. Metric / NLA by default |

## Agent

For full space programming (occupancy compliance → workplace strategy → accommodation schedule), see the [Workplace Strategist](../../agents/workplace-strategist.md) agent.

## Install

**Claude Desktop:**

1. Open the **+** menu → **Add marketplace from GitHub**
2. Enter `SpaceagencyArchitects/skills-for-architects`
3. Install the **Programming** plugin

**Claude Code (terminal):**

```bash
claude plugin marketplace add SpaceagencyArchitects/skills-for-architects
claude plugin install 03-programming@skills-for-architects
```

**Manual:**

```bash
git clone https://github.com/SpaceagencyArchitects/skills-for-architects.git
ln -s $(pwd)/skills-for-architects/plugins/03-programming/skills/occupancy-calculator ~/.claude/skills/occupancy-calculator
ln -s $(pwd)/skills-for-architects/plugins/03-programming/skills/workplace-programmer ~/.claude/skills/workplace-programmer
```

## License

MIT — fork of [AlpacaLabsLLC/skills-for-architects](https://github.com/AlpacaLabsLLC/skills-for-architects), amended for Western Australian practice.
