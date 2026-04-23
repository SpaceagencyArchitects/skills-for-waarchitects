# /epd-parser

Extract structured environmental impact data from EPD PDFs for [Claude Code](https://docs.anthropic.com/en/docs/claude-code). Parses Environmental Product Declaration PDFs — extracting GWP, life cycle stages, programme operator metadata, and rating-tool eligibility (Green Star Responsible Products, NABERS Embodied Emissions, LEED MRc2) into a standardised 42-column schema.

**Default jurisdiction: Western Australia.** Handles EPD Australasia, EU EN 15804+A2, and US (UL / NSF / SCS) formats. Australian English spelling and metric units (m², m³, kg) throughout.

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](../../../../LICENSE)

## Install

```bash
# Via plugin system
claude plugin marketplace add SpaceagencyArchitects/skills-for-architects
claude plugin install 05-sustainability@skills-for-architects

# Or symlink just this skill
git clone https://github.com/SpaceagencyArchitects/skills-for-architects.git
ln -s $(pwd)/skills-for-architects/plugins/05-sustainability/skills/epd-parser ~/.claude/skills/epd-parser
```

## Usage

```
/epd-parser ~/Downloads/boral-envisia-epd.pdf
```

A folder of EPDs:

```
/epd-parser ~/Documents/project-epds/
```

## What it extracts

- **Product identity** — manufacturer, product name, declared unit, NATSPEC worksection (CSI fallback for international)
- **EPD metadata** — registration number, programme operator, PCR, standard version, validity dates, system boundary
- **Impact indicators** — GWP-total, GWP-fossil, GWP-biogenic (A1–A3), plus A4–A5, B1–B7, C1–C4, D when available
- **Additional impacts** — ODP, AP, EP, POCP
- **Resource use** — renewable / non-renewable primary energy, fresh water, recycled content, waste
- **Rating-tool eligibility** — single field combining Green Star Responsible Products tier (Recognised / Best Practice / Leadership), NABERS Embodied Emissions compliance, and LEED v4.1 MRc2 eligibility
- **Plant / facility location** — captured for WA projects where A4 transport distance matters

Handles EN 15804+A1 and +A2 formats, multi-product EPDs, non-English documents, and varying table layouts across programme operators (EPD Australasia, Environdec, IBU, UL, NSF, SCS, ASTM).

Output saves to CSV (42-column EPD schema) or Google Sheets.

## Australian context

- Recognises **EPD Australasia** as the default Australian / NZ programme operator
- Captures **Climate Active** product certifications in the Notes field where applicable
- Notes plant-to-Perth distance for WA projects in the Notes field where data is available
- Sets `Rating Tool Eligibility` based on EPD type, verification, and programme operator — defaults to Green Star tier flags for AU projects

## What's Included

| File | Purpose |
|------|---------|
| `SKILL.md` | Extraction workflow, 42-column schema, rating-tool eligibility logic, edge case handling |

## License

MIT — fork of [AlpacaLabsLLC/skills-for-architects](https://github.com/AlpacaLabsLLC/skills-for-architects), amended for Australian / WA practice with Green Star + NABERS additions.
