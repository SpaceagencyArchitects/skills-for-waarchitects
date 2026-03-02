# Workplace Programmer

AI workplace strategy consultant for [Claude Code](https://claude.ai/code). Builds office space programs through conversation — area splits, room schedules, seat counts, and reports — backed by real industry research.

## Install

```bash
# From the root of this repo
ln -s "$(pwd)/workplace-programmer" ~/.claude/skills/workplace-programmer
```

Or copy the directory:

```bash
cp -r workplace-programmer ~/.claude/skills/workplace-programmer
```

## Usage

In Claude Code:

```
/workplace-programmer 30,000 RSF tech company, 200 people, 3 days hybrid
```

Or start with no context:

```
/workplace-programmer
```

The skill works through four conversation phases:

1. **Discover** — Learns about your organization while sharing relevant research (JLL occupancy data, Gensler workspace surveys, VergeSense room analytics)
2. **Synthesize** — Generates custom area splits across five zones (work, meeting, common, circulation, BOH)
3. **Detail** — Proposes seat types, conference room schedules, and support spaces with SF calculations
4. **Refine** — Handles adjustments with explicit tradeoff analysis

## Sample output

Mid-conversation, the skill produces recommendations like:

```
Based on your 30,000 RSF hybrid tech office with 200 people on a 3-day
in-office policy, here's my recommendation:

| Zone        |  Pct |      SF |
|-------------|------:|--------:|
| Work        |  28% |   8,400 |
| Meeting     |  22% |   6,600 |
| Common      |  16% |   4,800 |
| Circulation |  27% |   8,100 |
| BOH         |   7% |   2,100 |
| **Total**   | 100% |  30,000 |

I'm pulling work down to 28% because JLL's 2024 data shows hybrid
offices with 3-day policies allocate 15-20% less to individual work
than pre-pandemic baselines. Your 200 people at 3 days means ~120
concurrent on peak days — we're designing for that mid-week peak,
not full capacity.
```

Program state saves to `program.json` in your working directory:

```json
{
  "inputs": {
    "name": "Tech HQ",
    "rsf": 30000,
    "headcount": 200,
    "hybrid_policy": "3 days in office"
  },
  "area_splits": {
    "work":        { "pct": 28, "sf": 8400 },
    "meeting":     { "pct": 22, "sf": 6600 },
    "common":      { "pct": 16, "sf": 4800 },
    "circulation": { "pct": 27, "sf": 8100 },
    "boh":         { "pct": 7,  "sf": 2100 }
  },
  "seats": [
    { "name": "60\"x36\" Adjustable Desk", "count": 100, "sf_each": 65, "sf_total": 6500 },
    { "name": "48\"x24\" Bench", "count": 40, "sf_each": 48, "sf_total": 1920 }
  ],
  "rooms": [
    { "name": "Large Conference (10p)", "count": 2, "sf_each": 300, "sf_total": 600 },
    { "name": "Medium Conference (6p)", "count": 3, "sf_each": 225, "sf_total": 675 },
    { "name": "Small Huddle (4p)", "count": 5, "sf_each": 100, "sf_total": 500 },
    { "name": "Phone Booth", "count": 8, "sf_each": 25, "sf_total": 200 }
  ],
  "total_sf": 30000,
  "sf_per_seat": 150
}
```

Ask for a report and the skill writes `report.md` and `report.csv` with the full breakdown.

## What's included

- **8 seed archetypes** — Dense Open Office (65 SF/seat) through Legal Services (300 SF/seat)
- **22 space types** — Desks, conference rooms, phone booths, support spaces with default SF and capacity
- **31 research findings** — From JLL, CBRE, Gensler, VergeSense, Density, Leesman, Steelcase, Hassell, and peer-reviewed studies

## Customization

| File | What it controls |
|------|-----------------|
| `SKILL.md` | Persona, domain expertise, conversation flow, formatting |
| `data/archetypes.json` | Benchmark office profiles with area splits and desk mixes |
| `data/space-types.json` | Room and desk catalog with default SF and capacity |
| `data/findings.json` | Research findings the AI cites during recommendations |

Edit any of these to adapt the skill for your practice.

## License

MIT
