# /epd-compare

Compare 2+ products side-by-side on environmental impact metrics for [Claude Code](https://docs.anthropic.com/en/docs/claude-code). Validates comparability, normalises declared units, generates percentage deltas, and assesses against Green Star Responsible Products tiers, NABERS Embodied Emissions compliance, and (for international projects) LEED v4.1 MRc2.

**Default jurisdiction: Western Australia.** Australian English, metric (m², m³, kg) throughout. Falls back to LEED-only assessment for international projects.

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](../../../../LICENSE)

## Install

```bash
# Via plugin system
claude plugin marketplace add SpaceagencyArchitects/skills-for-architects
claude plugin install 05-sustainability@skills-for-architects

# Or symlink just this skill
git clone https://github.com/SpaceagencyArchitects/skills-for-architects.git
ln -s $(pwd)/skills-for-architects/plugins/05-sustainability/skills/epd-compare ~/.claude/skills/epd-compare
```

## Usage

After parsing or researching EPDs:

```
/epd-compare compare the concrete EPDs I just found
```

Or with sheet rows:

```
/epd-compare compare rows 5, 8, and 12 from my EPD sheet
```

Or with inline data:

```
/epd-compare concrete A: 218 kg CO₂e/m³, concrete B: 295 kg CO₂e/m³, concrete C: 365 kg CO₂e/m³
```

## What it checks

- **Declared unit alignment** — warns if units differ and normalisation isn't possible
- **System boundary** — flags cradle-to-gate vs cradle-to-grave mismatch
- **PCR alignment** — notes when products use different Product Category Rules
- **EN 15804 version** — flags +A1 vs +A2 differences (different units for AP / EP / POCP)
- **Validity** — marks expired EPDs
- **EPD type** — distinguishes product-specific from industry-average
- **Programme operator** — flags cross-operator comparisons
- **Plant location (WA)** — flags A4 transport implications for Perth-based projects

## What it produces

- **Side-by-side impact table** — GWP-total / fossil / biogenic, ODP, AP, EP, PERE, PENRE, FW, recycled content
- **Percentage comparison** vs lowest GWP and (where the user supplies one) vs published industry baseline
- **Green Star Buildings Responsible Products tier assessment** (Tier C / B / A) per product, default for AU projects
- **NABERS Embodied Emissions compliance assessment**
- **LEED v4.1 MRc2 assessment** (Option 1 + Option 2) for international or LEED-targeting projects
- **Recommendation summary** — direct, opinionated, accounting for A4 transport and AU manufacturer availability

## What this skill does NOT do

- Does not write to the EPD Google Sheet (read-only)
- Does not invent industry baselines — requires user-supplied baseline (CCAA, MECLA, Worldsteel, etc.) or omits the baseline column
- Does not assert Green Star Tier B / Leadership without evidence — defaults to Tier C and flags for Green Star AP confirmation

## What's Included

| File | Purpose |
|------|---------|
| `SKILL.md` | Comparison workflow, comparability checks, Green Star + NABERS + LEED assessment, recommendation logic |

## License

MIT — fork of [AlpacaLabsLLC/skills-for-architects](https://github.com/AlpacaLabsLLC/skills-for-architects), amended for Australian / WA practice with Green Star + NABERS additions.
