# /epd-to-spec

Generate **NATSPEC**-formatted specification clauses requiring Environmental Product Declarations and setting maximum GWP thresholds for [Claude Code](https://docs.anthropic.com/en/docs/claude-code). References ISO 14025, ISO 21930, EN 15804+A2, AS / AS-NZS, and NCC 2022. Aligned with **Green Star Buildings Responsible Products** and **NABERS Embodied Emissions** for AU / WA projects, with **LEED v4.1 MRc2** fallback for international.

**Default jurisdiction: Western Australia.** NATSPEC + AS / AS-NZS + NCC 2022 + Green Star + NABERS. Falls back to CSI MasterFormat + ASTM + IBC + LEED for international projects.

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](../../../../LICENSE)

## Install

```bash
# Via plugin system
claude plugin marketplace add SpaceagencyArchitects/skills-for-architects
claude plugin install 05-sustainability@skills-for-architects

# Or symlink just this skill
git clone https://github.com/SpaceagencyArchitects/skills-for-architects.git
ln -s $(pwd)/skills-for-architects/plugins/05-sustainability/skills/epd-to-spec ~/.claude/skills/epd-to-spec
```

## Usage

With explicit thresholds:

```
/epd-to-spec concrete max 300 kg CO₂e/m³, rebar max 1.0 kg CO₂e/kg, glass wool insulation max 1.8 kg CO₂e/kg
```

From a project description:

```
/epd-to-spec write EPD requirements for a Fremantle commercial fit-out — concrete and steel structure, aluminium curtain wall, suspended ceiling, targeting Green Star Buildings 5-Star
```

From the EPD sheet:

```
/epd-to-spec use the EPDs I saved in rows 5-12 to set thresholds, target Green Star Tier B
```

## What it generates

For each material, a NATSPEC three-part worksection:

- **1 General** — EPD submissions (clause 1.4): ISO 14025 conforming EPDs, accredited programme operators (EPD Australasia, Environdec, IBU, UL, NSF, SCS), third-party verification, validity. Sustainability requirements (clause 1.8): maximum GWP thresholds, rating tool documentation, recycled / SCM content, regional materials preference (A4 transport).
- **2 Products** — Maximum GWP per declared unit, sustainable sourcing, manufacturer EPD qualification. WA-available basis-of-design products listed (Boral ENVISIA, Wagners EFC, BlueScope, InfraBuild, etc.).
- **3 Execution** — Standard execution (no EPD-specific changes).

Plus optional rating-tool appendices:

- **Green Star Buildings — Responsible Products** appendix with Recognised Initiatives list and Tier framework (C / B / A)
- **NABERS Embodied Emissions** appendix with documentation requirements
- **LEED v4.1 MRc2** appendix (Option 1 + Option 2) for international / LEED projects

Plus a **NCC 2022 Section J cross-reference note** for envelope materials (insulation, glazing, roofing, cladding) flagging the embodied / operational carbon trade-off.

## NATSPEC worksections covered

The skill maps to NATSPEC 4-digit worksections where EPDs are most common:

- `0341` Concrete in situ · `0351` Precast concrete · `0321` Steel reinforcement
- `0511` Structural steelwork · `0561` Metalwork
- `0611` Timber framing · `0651` Stairs and balustrades · `0671` Joinery
- `0711` Thermal insulation · `0731` Membranes · `0741` Metal roofing
- `0851` Windows — aluminium · `0881` Glazing
- `0911` Plasterboard linings · `0931` Tiling · `0951` Suspended ceilings
- `0961` Resilient flooring · `0971` Carpet · `0981` Painting
- `0231` External pavements

For Domestic (Class 1) projects, prefix with `D`. For overseas / CSI projects, see Appendix A in the SKILL.md.

## What this skill does NOT do

- Does not invent GWP baselines from memory — requires user-supplied EPD, comparison report, or published baseline (CCAA / MECLA / Worldsteel)
- Does not assert Green Star Tier B / Leadership without evidence — flags for Green Star AP confirmation
- Does not write the full body of a worksection — pairs with `/spec-writer` which provides the standard material specification; this skill provides the EPD layer

## What's Included

| File | Purpose |
|------|---------|
| `SKILL.md` | Spec generation workflow, NATSPEC worksection mapping, EPD clause language, Green Star / NABERS / LEED appendices, NCC Section J interaction, CSI fallback |

## License

MIT — fork of [AlpacaLabsLLC/skills-for-architects](https://github.com/AlpacaLabsLLC/skills-for-architects), amended for Australian / WA practice with Green Star + NABERS additions.
