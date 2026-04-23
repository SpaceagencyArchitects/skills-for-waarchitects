# /spec-writer

NATSPEC outline specification writer for [Claude Code](https://docs.anthropic.com/en/docs/claude-code). Feed it a materials list, product schedule, or project description — get structured outline specs organised by NATSPEC worksections with AS / AS-NZS references and NCC 2022 citations.

**Default jurisdiction: Western Australia.** Uses **NATSPEC Building** (commercial) or **NATSPEC Domestic** (residential) by default. Falls back to CSI MasterFormat 2020 for overseas / US-client projects.

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](../../../../LICENSE)

## Install

```bash
# Via plugin system
claude plugin marketplace add SpaceagencyArchitects/skills-for-architects
claude plugin install 04-specifications@skills-for-architects

# Or symlink just this skill
git clone https://github.com/SpaceagencyArchitects/skills-for-architects.git
ln -s $(pwd)/skills-for-architects/plugins/04-specifications/skills/spec-writer ~/.claude/skills/spec-writer
```

## Usage

```
/spec-writer
```

Then provide your materials — paste a list, point to a file, or describe the project.

```
/spec-writer

Porcelain floor tile, suspended ceiling tile, painted plasterboard linings,
aluminium-framed windows, resilient skirting, ceramic wall tile in wet areas,
timber-framed doors, proprietary wet-area waterproofing membrane
```

From a file:

```
/spec-writer ~/Documents/finishes-schedule.csv
```

By description:

```
/spec-writer

Three-storey Fremantle commercial fit-out. Double-brick party walls with bagged
finish, Colorbond roof over trusses, carpet tile throughout offices, porcelain
tile in lobbies and wet areas, suspended mineral-fibre ceiling, aluminium
windows to street frontage.
```

## What It Does

1. Maps each material to the correct **NATSPEC worksection number** (4-digit) and title
2. Generates three-part outline worksections — **1 General / 2 Products / 3 Execution** — with AS / AS-NZS and NCC references
3. Cites applicable performance standards — **AS 3959** (BAL), **AS 4586** (slip), **AS/ISO 717** (acoustic), **AS 1530.4** (fire), **AS/NZS 4859** (thermal)
4. Lists Australian-available manufacturers (Colorbond, Austral Bricks, Laminex, Dulux, etc.) where applicable
5. Flags generic worksections with `[REVIEW REQUIRED]` for senior specifier attention
6. Writes a single organised `.md` file to `./outline-specs-[project-slug].md`

## Worksections Covered

NATSPEC Building worksection groups:

- **01xx** General (preliminaries, quality, contract administration)
- **02xx** Sitework, demolition, earthworks
- **03xx** Structural (concrete, reinforcement, formwork)
- **04xx** Masonry
- **05xx** Metalwork
- **06xx** Carpentry, joinery, timber
- **07xx** Thermal, moisture, waterproofing
- **08xx** Doors, windows, glazing
- **09xx** Linings, finishes, painting
- **10xx–11xx** Specialties, equipment, fixtures
- **12xx** Furnishings
- **13xx** Special construction
- **14xx** Conveying systems (lifts)
- **20xx–27xx** Services (mechanical, hydraulic, fire, electrical, communications)

For Domestic projects (single dwellings, Class 1), worksections are prefixed with `D` (e.g. `D0411 Brick and block construction`).

For overseas work, the skill switches to CSI MasterFormat 2020 (6-digit section numbers, e.g. `09 30 00 Tiling`).

## Australian context

Things the skill handles for WA practice:

- **Australian English** — colour, centre, organise, metre, storey, aluminium
- **Metric units** — mm, m, m², kg, kPa, °C throughout
- **AS / AS/NZS standards** — referenced with year (e.g. "to AS 3600–2018")
- **NCC 2022** — Parts / Clauses cited where a standard is code-driven (e.g. "provide fire resisting construction to NCC 2022 Vol. 1 Spec C1.1, tested to AS 1530.4")
- **BAL ratings** — AS 3959 references for Bushfire Prone Areas
- **Heritage flags** — where the site is State-listed (inHerit) or within a local Heritage Area (LPS), worksections flag the need for Conservation Management Plan cross-reference
- **Sustainability submissions** — CodeMark, WERS, Green Star credits, EPDs called out where applicable

## What's Included

| File | Purpose |
|------|---------|
| `SKILL.md` | Workflow, NATSPEC worksection mapping, three-part format rules, writing style |
| `sample.md` | Worked NATSPEC example — commercial fit-out with 5 worksections |

## License

MIT — fork of [AlpacaLabsLLC/skills-for-architects](https://github.com/AlpacaLabsLLC/skills-for-architects), rewritten for NATSPEC / WA practice.
