# /epd-research

Search for Environmental Product Declarations by product category, NATSPEC worksection, or material type for [Claude Code](https://docs.anthropic.com/en/docs/claude-code). Searches Australian registries (EPD Australasia, Global GreenTag, GECA), international (Environdec, IBU, EC3, UL, NSF, SCS), and manufacturer sites. Returns candidates sorted by GWP with Green Star / NABERS / LEED eligibility flags.

**Default jurisdiction: Western Australia.** WA-available manufacturers (BlueScope, Boral, BGC, Cement Australia, CSR Bradford, Knauf, Bondor, Capral, Viridian, etc.) are searched alongside the registries. International projects fall back to Environdec / IBU / UL / NSF defaults.

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](../../../../LICENSE)

## Install

```bash
# Via plugin system
claude plugin marketplace add SpaceagencyArchitects/skills-for-architects
claude plugin install 05-sustainability@skills-for-architects

# Or symlink just this skill
git clone https://github.com/SpaceagencyArchitects/skills-for-architects.git
ln -s $(pwd)/skills-for-architects/plugins/05-sustainability/skills/epd-research ~/.claude/skills/epd-research
```

## Usage

```
/epd-research CLT
```

With more detail:

```
/epd-research ready-mix concrete, 32 MPa, plants within 100 km of Perth, GWP under 300, eligible for Green Star Responsible Products
```

## What it searches

### Australian / NZ (default for WA projects)

| Source | Coverage |
|--------|----------|
| EPD Australasia | Regional hub of the International EPD System — Australian / NZ EPDs |
| Global GreenTag | Australian product certification (LCARate, PhD); recognised under Green Star |
| GECA | Good Environmental Choice Australia eco-labelling |
| Declare (ILFI) | Materials transparency; recognised under Green Star |
| MECLA | Materials & Embodied Carbon Leaders' Alliance — AU benchmarks |
| NABERS | Operational ratings + Embodied Emissions module |
| GBCA | Green Star Recognised Initiatives list |

### International (always searched)

| Source | Coverage |
|--------|----------|
| Building Transparency / EC3 | Largest open EPD database — requires API access |
| Environdec | Largest international registry (parent of EPD Australasia) |
| IBU | German programme operator — strong on European products imported to AU |
| UL Environment | Major US programme operator |
| NSF International | US — strong in concrete, masonry |
| SCS Global Services | US programme operator |
| ASTM International | US programme operator (newer) |
| Manufacturer sites | Direct sustainability pages |

Claude runs 3–5 searches across registries and manufacturer sites, fetches EPD listings, and returns 6–12 candidates sorted by GWP (lowest first) with Green Star tier, NABERS Embodied compliance, and LEED MRc2 eligibility flags.

## WA-specific behaviour

- Prioritises WA-manufactured product (Welshpool, Kwinana, Naval Base, Wangara) for transport reasons — Perth's geographic isolation makes A4 emissions material
- Lists known WA-relevant suppliers by category (concrete, steel, insulation, cladding, etc.)
- Flags published industry baselines (CCAA for concrete, MECLA member benchmarks, Worldsteel for steel) — never invents baselines
- Notes Climate Active certified products where the manufacturer holds federal carbon-neutral certification

## What's Included

| File | Purpose |
|------|---------|
| `SKILL.md` | Research workflow, registry search strategy, AU + international source list, presentation rules |

## License

MIT — fork of [AlpacaLabsLLC/skills-for-architects](https://github.com/AlpacaLabsLLC/skills-for-architects), amended for Australian / WA practice with Green Star + NABERS additions.
