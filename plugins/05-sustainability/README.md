# Sustainability

A Claude Code plugin for embodied carbon and Environmental Product Declarations. Parses EPD PDFs, searches Australian and international registries for published EPDs, compares products on GWP and other impact metrics, and generates **NATSPEC** specification clauses with maximum GWP thresholds aligned to **Green Star Buildings Responsible Products**, **NABERS Embodied Emissions**, and (for international work) **LEED v4.1 MRc2**.

**Default jurisdiction: Western Australia.** The skills work equally well for international projects — they retain LEED workflows and US / EU programme operators alongside the Australian additions.

## The Problem

Embodied carbon is increasingly central to AU practice — Green Star Buildings has a Responsible Products credit that requires EPDs and rewards low-carbon performance, NABERS now publishes an Embodied Emissions module, and the *Climate Change Act 2022* (Cth) creates pressure on the construction sector to reduce upfront carbon. But EPDs are dense PDFs in inconsistent formats, scattered across registries (EPD Australasia, Environdec, IBU, EC3, UL, NSF), and the data needs to flow into NATSPEC specifications and whole-building LCA.

## The Solution

Four skills form a pipeline: parse EPD PDFs into structured data, search registries for published EPDs (AU + international), compare products on environmental impact metrics, and generate NATSPEC specification clauses with GWP thresholds.

```
EPD PDFs ──→ /epd-parser ──→ structured data ←── /epd-research ←── registries
                                    │
                             /epd-compare
                                    │
                          comparison report
                          + Green Star tier
                          + NABERS compliance
                          + LEED MRc2 (intl)
                                    │
                              /epd-to-spec
                                    │
                       NATSPEC clauses
                       + Green Star appendix
                       + NABERS appendix
                       + Section J cross-ref
```

Each skill works standalone. Chaining is natural but not required.

## Skills

| Skill | Description |
|---|---|
| [epd-parser](skills/epd-parser/) | Extract structured data from EPD PDFs (EPD Australasia, EU EN 15804+A2, US UL/NSF/SCS) — GWP, life cycle stages, rating-tool eligibility |
| [epd-research](skills/epd-research/) | Search EPD Australasia, Global GreenTag, GECA, Environdec, IBU, EC3, UL, NSF, and manufacturer sites for EPDs |
| [epd-compare](skills/epd-compare/) | Side-by-side impact comparison with Green Star Responsible Products tier, NABERS Embodied compliance, LEED MRc2 eligibility |
| [epd-to-spec](skills/epd-to-spec/) | NATSPEC specification clauses requiring EPDs and setting GWP limits, aligned to Green Star / NABERS / LEED |

## Output

All skills share a **42-column EPD schema** covering product identity, EPD metadata, impact indicators (GWP-fossil, GWP-biogenic, ODP, AP, EP, POCP), resource use, and rating-tool eligibility (Green Star tier + NABERS Embodied + LEED MRc2). Data saves to CSV or Google Sheets. Comparison reports and specifications save as markdown.

## Australian context

- **Green Star Buildings (GBCA)** — Responsible Products credit recognises EPD Australasia, Environdec, IBU, UL, Global GreenTag, GECA, Declare, Cradle to Cradle, FSC, PEFC. Three tiers: Recognised / Best Practice / Leadership.
- **NABERS Embodied Emissions** — module measuring whole-building embodied carbon at design stage; EPDs feed the calculation.
- **NCC 2022 Section J** — energy efficiency provisions create an embodied / operational carbon trade-off worth flagging in envelope worksections (insulation, glazing, roofing, cladding).
- **MECLA** — Materials & Embodied Carbon Leaders' Alliance; publishes AU benchmarks for concrete and steel.
- **CCAA** — Cement Concrete & Aggregates Australia; publishes industry baselines for AU concrete.
- **Climate Active** — federal carbon-neutral certification scheme; some manufacturers (Adbri, Boral) hold Climate Active product certifications.
- **WA-specific transport (A4)** — Perth's geographic isolation makes A4 transport emissions material. Local manufacture (Welshpool, Kwinana, Naval Base) often beats lower-A1–A3 imports once transport is included.

## Install

**Claude Desktop:**

1. Open the **+** menu → **Add marketplace from GitHub**
2. Enter `SpaceagencyArchitects/skills-for-architects`
3. Install the **Sustainability** plugin

**Claude Code (terminal):**

```bash
claude plugin marketplace add SpaceagencyArchitects/skills-for-architects
claude plugin install 05-sustainability@skills-for-architects
```

**Manual:**

```bash
git clone https://github.com/SpaceagencyArchitects/skills-for-architects.git
ln -s $(pwd)/skills-for-architects/plugins/05-sustainability/skills/epd-parser ~/.claude/skills/epd-parser
ln -s $(pwd)/skills-for-architects/plugins/05-sustainability/skills/epd-research ~/.claude/skills/epd-research
ln -s $(pwd)/skills-for-architects/plugins/05-sustainability/skills/epd-compare ~/.claude/skills/epd-compare
ln -s $(pwd)/skills-for-architects/plugins/05-sustainability/skills/epd-to-spec ~/.claude/skills/epd-to-spec
```

## License

MIT — fork of [AlpacaLabsLLC/skills-for-architects](https://github.com/AlpacaLabsLLC/skills-for-architects), amended for Australian / WA practice with Green Star + NABERS additions.
