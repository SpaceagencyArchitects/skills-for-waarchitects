# /occupancy-calculator

Occupancy load calculator for [Claude Code](https://docs.anthropic.com/en/docs/claude-code). Describe your building — get per-area occupant loads, egress requirements, and sanitary fixture guidance.

**Default jurisdiction: Western Australia.** Uses NCC 2022 Vol. 1 Part D3 + Appendix WA. IBC fallback for international projects.

- **Western Australia** — [NCC 2022 Vol. 1](https://ncc.abcb.gov.au/) Part D3D15 + Appendix WA
- **Other Australian state** — NCC 2022 Vol. 1 (check state-specific appendix)
- **New York City** — [NYC Building Code 2022](https://codelibrary.amlegal.com/codes/newyorkcity/latest/NYCbldg/) (IBC 2015 + NYC amendments)
- **Other US** — IBC 2021 bundled as reference; verify your state's version at [UpCodes](https://up.codes/viewer/general/ibc-2021/chapter/10)

Every report cites the code edition, clause, and a public link to the source.

> **Important — NCC data completeness.** The bundled NCC data file (`data/ncc-occupancy-load-factors.json`) is currently a **structured stub**. Several entries have verified load factors; most are marked TODO pending verification against the gazetted NCC 2022 Vol. 1 Schedule 7 D3D15. When the skill encounters a TODO entry it will ask the user to supply the value from the current NCC text — it will not guess. Contributions to complete this file are welcome.

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](../../../../LICENSE)

## Install

```bash
# Via plugin system
claude plugin marketplace add SpaceagencyArchitects/skills-for-architects
claude plugin install 03-programming@skills-for-architects

# Or symlink just this skill
git clone https://github.com/SpaceagencyArchitects/skills-for-architects.git
ln -s $(pwd)/skills-for-architects/plugins/03-programming/skills/occupancy-calculator ~/.claude/skills/occupancy-calculator
```

## Usage

```
/occupancy-calculator 1,500 m² office on two levels, Fremantle
```

Or describe a mixed-use building:

```
/occupancy-calculator ground floor café (100 m²) + three floors office (1,200 m²)
```

Or start with no context:

```
/occupancy-calculator
```

### Conversation Flow

The skill works through four phases:

1. **Discover** — learns about your building, identifies NCC Class(es), clarifies the area basis (NCC floor area vs GFA vs NLA)
2. **Calculate** — breaks the building into parts by Class, applies D3D15 load factors, calculates occupant loads per area and total
3. **Detail** — provides egress requirements (NCC D3D3 exits, D3D10/11 widths, D3D6/7 travel distances) and indicative sanitary fixtures (NCC Part F4/F6 + AS/NZS 3500.1)
4. **Refine** — handles adjustments with before/after comparison and updated egress implications

## Workplace Programmer Integration

If a `program.json` file exists in the working directory (from `/workplace-programmer`), the skill offers to calculate occupancy directly from the accommodation schedule — mapping meeting rooms to Class 5 (or Class 9b if large), open desks to Class 5 office, cafés to Class 6 dining, and storage to Class 7b.

## What's Included

| File | Purpose |
|------|---------|
| `SKILL.md` | Persona, domain expertise (NCC + IBC fallback), conversation flow, formatting rules |
| `data/ncc-occupancy-load-factors.json` | NCC 2022 Vol. 1 Schedule 7 D3D15 load factors — structured stub with TODOs to populate |
| `data/occupancy-load-factors.json` | Legacy — 39 IBC 2021 Table 1004.5 use types with load factors, gross/net designation, aliases, and NYC BC variant notes. Used for international / legacy projects. |
| `data/use-groups.json` | Legacy — 21 IBC use group classifications (A through U) with descriptions and examples. Used for IBC work. |

## Customisation

### Populate the NCC data

Open `data/ncc-occupancy-load-factors.json` and fill in the `load_factor_m2_per_person` value for each entry marked `"status": "TODO"`. The source of truth is **NCC 2022 Vol. 1 Schedule 7 D3D15** — always verify against the gazetted text at [ncc.abcb.gov.au](https://ncc.abcb.gov.au/).

When updating, set `"status": "verified"` and add the date of verification to the entry.

### Add a jurisdiction variant

The WA variation is bundled via reference. If working in a jurisdiction with explicit NCC variations (VIC, NSW, QLD, etc.) you can extend the data file with state-specific entries — include a `jurisdiction` field and adjust the load factor per the state appendix.

### Change the persona

Edit `SKILL.md` to adjust the personality or domain focus. The conversation flow adapts to whatever role you write.

## License

MIT
