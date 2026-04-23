# /workplace-programmer

Workplace strategy consultant for [Claude Code](https://docs.anthropic.com/en/docs/claude-code). Builds office space programs through conversation — zone splits, accommodation schedules, desk counts, and exportable reports — backed by industry research from JLL, CBRE, Gensler, VergeSense, Hassell and others.

**Default jurisdiction: Western Australia.** Works in **metric (m²)** with **NLA** (Net Lettable Area per PCA Method of Measurement) as the default. Australian terminology throughout.

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](../../../../LICENSE)

## Install

```bash
# Via plugin system
claude plugin marketplace add SpaceagencyArchitects/skills-for-architects
claude plugin install 03-programming@skills-for-architects

# Or symlink just this skill
git clone https://github.com/SpaceagencyArchitects/skills-for-architects.git
ln -s $(pwd)/skills-for-architects/plugins/03-programming/skills/workplace-programmer ~/.claude/skills/workplace-programmer
```

## Usage

```
/workplace-programmer 2,800 m² NLA, 200 people, 3 days hybrid, tech company
```

Or start with no context and let the skill ask discovery questions:

```
/workplace-programmer
```

### Conversation Flow

The skill works through four phases:

1. **Discover** — asks about your organisation, headcount, work policy, and culture while sharing relevant research (e.g., JLL occupancy benchmarks, Hassell Workplace Futures Survey)
2. **Synthesise** — generates zone splits across five zones: Work, Meeting, Common, Circulation, and BOH
3. **Detail** — proposes desk types, meeting room schedules, and support spaces with m² calculations
4. **Refine** — handles adjustments with explicit tradeoff analysis ("adding 2 large meeting rooms means removing 10 desks")

## Demo: Law Firm — 1,860 m² NLA

Real output from a session where the brief was "1,860 m² NLA law firm, maximise lawyer headcount, 1 private office per 10 lawyers, 1 support staff per 10 lawyers, 5 days in office."

The skill pushed the work zone to 41% and landed at **111 lawyers + 11 support = 122 total headcount at 15 m² / seat**:

```
| Zone        |    % |   m²  |
|-------------|-----:|------:|
| Work        |  41% |   763 |
| Meeting     |  17% |   316 |
| Common      |   7% |   130 |
| Circulation |  27% |   502 |
| BOH         |   8% |   149 |
| **Total**   | 100% | 1,860 |
```

With the full accommodation schedule:

```
| Type                    | Count | m² each | Total m² |
|-------------------------|------:|--------:|---------:|
| Boardroom (14p)         |     1 |      45 |       45 |
| Large Meeting (10p)     |     2 |      28 |       56 |
| Medium Meeting (6p)     |     4 |      21 |       84 |
| Small Huddle (4p)       |     8 |       9 |       72 |
| Phone Booth (1p)        |    10 |     2.3 |       23 |
| Lounge / Informal (4p)  |     5 |     5.2 |       26 |
| **Subtotal**            |  **30** |       |    **306** |
```

Every recommendation includes research citations and tradeoff analysis. The full program exports to `program.json`, `.md`, and `.csv`.

## What's Included

| File | Purpose |
|------|---------|
| `SKILL.md` | Persona, domain expertise, conversation flow, formatting rules |
| `data/archetypes.json` | 10 benchmark office profiles (Dense Open at 6 m²/seat through Legal Services at 28 m²/seat) — converted from original US benchmarks |
| `data/space-types.json` | 22 room and desk types with default m² and capacity |
| `data/findings.json` | 43 research findings from JLL, CBRE, Gensler, VergeSense, Density, Leesman, Steelcase, Hassell (incl. 6 years of Hassell Workplace Futures Survey 2020–2025) — note: US-sourced findings still quote original SF |

## Australian context

A few things the skill handles for WA practice:

- **NLA by default** — the PCA Method of Measurement convention used in Australian commercial leases. If the client quotes GFA instead, the skill will flag the ~15% efficiency gap.
- **Australian spelling** — colour, centre, organise, metre, storey, kilometre.
- **Statutory rooms** — Parents / Lactation Room required under *Sex Discrimination Act 1984* (Cth) and WA *Equal Opportunity Act 1984*. Accessible WC per AS 1428.1.
- **End-of-Trip (EoT) facilities** — usually base-building in commercial WA; flagged for Green Star / active-travel requirements.
- **Hassell research** — the skill draws on Hassell's Workplace Futures Survey as the best AU-specific benchmark.

## Customisation

Everything the skill knows lives in editable JSON files. No code to change — just data.

### Add a space type

Add to `data/space-types.json`:

```json
{
  "id": "focus-pod-2p",
  "name": "Focus Pod (2p)",
  "category": "meeting",
  "default_m2": 11,
  "capacity": 2,
  "utilisation_nla_m2": 140,
  "source": "custom",
  "is_global": true,
  "project_id": null,
  "created_at": "2026-03-01T00:00:00Z"
}
```

The skill will immediately use it when building accommodation schedules. `utilisation_nla_m2` sets the ratio (1 per 140 m² here) — set to `null` if it doesn't apply (desks, support spaces).

### Add an archetype

Run a program, like it, want to reuse it as a starting benchmark? Save it as an archetype in `data/archetypes.json`:

```json
{
  "id": "healthcare-clinic",
  "name": "Healthcare Clinic",
  "description": "High BOH for medical storage, exam rooms in meeting zone, minimal common. Designed for outpatient clinics and medical offices.",
  "m2_per_seat": 18,
  "area_splits": {
    "work": 25,
    "meeting": 22,
    "common": 8,
    "circulation": 27,
    "boh": 18
  },
  "private_office_pct": 40,
  "desk_type_mix": {
    "bench-1500x900": 60,
    "private-office": 40
  },
  "room_ratios": {},
  "source": "custom",
  "created_at": "2026-03-01T00:00:00Z"
}
```

The five zone percentages must sum to 100. `desk_type_mix` percentages should also sum to 100.

### Add research findings

Drop new findings into `data/findings.json` and the skill will cite them during discovery and synthesis:

```json
{
  "id": "your-source-topic-year",
  "topics": ["hybrid", "density"],
  "source": "Source Name",
  "source_year": 2025,
  "study": "Study or Report Title",
  "finding": "The concrete finding with numbers.",
  "confidence": "high",
  "added": "2026-04-22"
}
```

Tag with relevant `topics` so the skill surfaces findings at the right moment: `hybrid`, `density`, `meeting-rooms`, `focus`, `acoustics`, `amenity-roi`, `abw`, `neighborhoods`, `open-vs-private`.

### Change the persona

Edit `SKILL.md` to change who the skill thinks it is. Swap "senior workplace strategy consultant" for "lab planning specialist" or "retail space planner" — the conversation flow and domain expertise adapt to whatever you write.

## License

MIT
