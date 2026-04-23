---
name: epd-research
description: Search for EPDs by product category, NATSPEC worksection / CSI division, or material type. Searches Australian (EPD Australasia, Global GreenTag, GECA), international (EC3, Environdec, IBU), and US (UL, NSF, SCS) registries plus manufacturer sites. WA defaults; international fallback.
allowed-tools:
  - Read
  - Write
  - Edit
  - Bash
  - Glob
  - Grep
  - WebFetch
  - WebSearch
  - AskUserQuestion
  - mcp__google-sheets__get_sheet_data
  - mcp__google-sheets__update_cells
  - mcp__google-sheets__list_sheets
---

# /epd-research — EPD Research

Receives a brief describing a material or product category, searches the web for matching EPDs (Environmental Product Declarations), and returns a curated shortlist sorted by environmental impact (lowest GWP first). Selected EPDs are saved to the EPD Google Sheet — the same one used by `/epd-parser` and the other EPD skills.

**Default jurisdiction: Western Australia.** Searches Australian registries (EPD Australasia, Global GreenTag, GECA), Australian-available manufacturers (BlueScope, Boral, Cement Australia, Bondor, CSR, Knauf, Laminex, etc.), and the international registries (Environdec, IBU, EC3, UL, NSF, SCS). For overseas projects, the same skill works with international defaults.

Follows `rules/units-and-measurements.md`, `rules/code-citations.md`, `rules/output-formatting.md`.

## How It Works

```
User describes what they need EPDs for
        |
Claude searches AU + international registries + manufacturer sites
        |
Presents candidates sorted by GWP (lowest first)
Cross-references to Green Star / NABERS / LEED eligibility
        |
User picks winners
        |
Saved to EPD Google Sheet (42-column schema)
```

## Step 1: Take the Brief

The user describes what they need EPDs for. A brief can be loose or specific:

**Loose:**
> "I need concrete EPDs"

**Specific:**
> "Looking for ready-mix concrete EPDs, 32 MPa, plants within 100 km of Perth, GWP under 300 kg CO2e/m³, eligible for Green Star Responsible Products"

### What to capture from the brief

Extract as many of these as the user provides. **Don't ask for fields they didn't mention** — work with what you have.

| Field | Examples |
|---|---|
| **Material / product** | Ready-mix concrete, structural steel, mineral wool insulation, carpet tile |
| **NATSPEC worksection / CSI division** | `0341 Concrete in situ`, `0511 Structural steelwork`, `0711 Thermal insulation` |
| **Performance specs** | 32 MPa, R 2.5, BAL-29 cladding, FRL 60/60/60 |
| **Geographic preference** | Plants near Perth, manufactured in WA / Australia, NZ-sourced ok, imported acceptable |
| **GWP target** | Under 300 kg CO₂e/m³, below industry average, lowest available |
| **Manufacturers** | "Include Boral and Cement Australia", "no imported steel", "BlueScope only" |
| **EPD type** | Product-specific only, industry-average ok |
| **Standard** | EN 15804+A2, ISO 21930, EPD Australasia |
| **Rating tool eligibility** | Green Star Responsible Products Tier 1, NABERS Embodied Emissions, LEED MRc2 |

**Don't interview the user.** If the brief is "concrete EPDs," that's enough to start searching. Clarify *after* showing initial results if needed.

## Step 2: Research

Search the web for EPDs matching the brief. Use multiple targeted queries to cover different sources.

### Australian / NZ registries (default for WA projects)

| Source | URL | Notes |
|---|---|---|
| **EPD Australasia** | epd-australasia.com | The Australian / NZ regional hub of the International EPD System. Programme operator for AU and NZ EPDs. Public searchable register. |
| **Global GreenTag** | globalgreentag.com | Australian product certification scheme; LCARate and PhD certifications include LCA-based ratings. Recognised under Green Star. |
| **Good Environmental Choice Australia (GECA)** | geca.eco | Australian eco-labelling for low-impact products; recognised under Green Star Responsible Products. |
| **Declare (ILFI)** | declare.living-future.org | Materials transparency labels including AU products; relevant for Living Building Challenge and recognised under Green Star. |
| **MECLA (Materials & Embodied Carbon Leaders' Alliance)** | mecla.org.au | Industry alliance publishing AU benchmarks and member EPDs (concrete, steel). |
| **NABERS** | nabers.gov.au | Operational ratings (energy, water) plus the Embodied Emissions module — useful context for material selection. |
| **Green Star (GBCA)** | gbca.org.au | Green Building Council of Australia. The Responsible Products credit specifies which certifications and EPD types qualify. |

### International registries (always search these)

| Source | URL | Notes |
|---|---|---|
| **Building Transparency / EC3** | buildingtransparency.org | Largest EPD database globally. Requires authenticated API access (free professional account + API key). See notes below. |
| **Environdec (International EPD System)** | environdec.com | Largest international registry. EPD Australasia is a regional hub of this system, so AU EPDs may also appear here. |
| **IBU (Institut Bauen und Umwelt)** | ibu-epd.com | German programme operator. Strong in European products often imported to AU (glazing, hardware, tiles). |
| **UL EPD Programme** | ul.com | Major US programme operator. |
| **NSF International** | nsf.org | US programme operator, strong in concrete / masonry. |
| **SCS Global Services** | scsglobalservices.com | US programme operator. |
| **ASTM International** | astm.org | US programme operator (newer). |
| **Manufacturer sites** | varies | Major manufacturers publish EPDs on their sustainability pages. |

### Manufacturer search — WA-relevant suppliers

For WA projects, prioritise EPDs from manufacturers with Australian distribution. Common WA-available suppliers with EPDs include:

- **Concrete:** Boral (ENVISIA low-carbon range), Cement Australia (EcoBlend), Holcim Australia, Hanson, BGC Concrete (WA-based)
- **Cement / SCMs:** Cement Australia, Adelaide Brighton Cement, Independent Cement & Lime, Ecocem (slag)
- **Steel:** BlueScope (Colorbond, TrueCore, XLERPLATE), Liberty OneSteel, InfraBuild (rebar)
- **Reinforcement:** InfraBuild, OneSteel
- **Aluminium:** Capral, Alspec, G.James Glass
- **Glass:** Viridian, G.James, Pilkington (Australian-stocked)
- **Insulation:** CSR Bradford (Gold Wool, Anticon, Polyester), Knauf Insulation, Kingspan (PIR / phenolic)
- **Plasterboard:** CSR Gyprock, Knauf Plasterboard, USG Boral
- **Plywood / engineered timber:** Carter Holt Harvey, Australian Sustainable Hardwoods (ASH), XLam (CLT, AU/NZ), Wesbeam (LVL)
- **Roofing / cladding:** BlueScope (Colorbond), Stratco, Bondor (Insulated panels), Lysaght
- **Carpet / soft floor:** Godfrey Hirst, Interface, Tarkett Australia, Modulyss (imported)
- **Resilient flooring:** Karndean Australia, Polyflor, Gerflor Australasia, Forbo
- **Tiles:** Beaumont Tiles, National Tiles, Academy Tiles
- **Paint / coatings:** Dulux, Haymes Paint, Wattyl

This list is not exhaustive — search broadly and confirm AU availability.

### Search strategy

For a brief like "ready-mix concrete EPDs, 32 MPa, near Perth":

1. **AU registry search:** `site:epd-australasia.com ready-mix concrete 32 MPa`
2. **AU manufacturer searches:** `Boral ENVISIA EPD`, `Cement Australia EcoBlend EPD`, `BGC Concrete EPD`
3. **International registry search:** `site:environdec.com Australia ready-mix concrete EPD`
4. **MECLA / industry body search:** `MECLA concrete EPD Australia`, `CCAA concrete EPD` (Cement Concrete & Aggregates Australia)
5. **Green Star Responsible Products search:** confirm which manufacturers have current registrations under the Green Star Recognised Initiatives.

Run **3–5 searches** depending on brief complexity. Aim for breadth — different manufacturers, regions, GWP ranges, plus the AU layer.

### For each EPD found

Attempt to fetch the registry page or EPD listing with WebFetch. Extract:

- Product name and manufacturer
- GWP (A1–A3) per declared unit — the primary comparison metric
- Declared unit (m³ for concrete, kg for steel, m² for sheet products etc.)
- Programme operator (EPD Australasia, Environdec, UL, etc.) and registration number
- System boundary (cradle-to-gate / cradle-to-grave)
- Validity dates
- Link to EPD PDF
- Plant / facility and location (if listed) — important for WA projects (transport distance)
- Green Star / NABERS / LEED rating-tool eligibility if stated

If the page is JS-rendered and returns limited data, use whatever info is available from the search result snippet plus general knowledge. Note as "unverified — confirm against EPD PDF" if sourced from snippets.

**Target: 6–12 EPD candidates** that genuinely match the brief. Don't pad with weak matches.

## Step 3: Present Candidates

Show results as a numbered shortlist sorted by GWP (lowest first). Use **m²** / **m³** / **kg** units throughout. AUD where currency is relevant. Australian English spelling.

```
## EPD Research: Ready-Mix Concrete (32 MPa, Perth Metro)

### 1. ENVISIA 32 MPa — Boral
Plant: Welshpool, WA · GWP: 218 kg CO₂e/m³
Declared Unit: 1 m³ · System Boundary: Cradle-to-gate (A1–A3)
Programme Operator: EPD Australasia · Reg: EPD-IES-AU-XXXX · Valid: 2024-08 to 2029-08
Green Star: Yes (Responsible Products Tier B — Best Practice)
NABERS Embodied: Compliant
PDF: [link]
Why: WA-manufactured, lowest GWP in this set, well below the CCAA industry
average (~360 kg CO₂e/m³ for 32 MPa). ENVISIA uses high-SCM blend (slag +
fly ash). Welshpool plant ~10 km from Perth CBD.

### 2. Standard 32 MPa — BGC Concrete
Plant: Naval Base, WA · GWP: 295 kg CO₂e/m³
Declared Unit: 1 m³ · System Boundary: Cradle-to-gate (A1–A3)
Programme Operator: EPD Australasia · Reg: EPD-IES-AU-XXXX · Valid: 2023-04 to 2028-04
Green Star: Yes (Responsible Products Tier C — Recognised)
PDF: [link]
Why: WA-based supplier, conventional mix. Higher GWP than ENVISIA but still
below CCAA average. Naval Base plant well-positioned for Perth metro south.

### 3. ...

---

## Summary

| # | Product | Manufacturer | Plant | GWP (A1–A3) | Unit | Valid To | Green Star | NABERS |
|---|---|---|---|---:|---|---|---|---|
| 1 | ENVISIA 32 MPa | Boral | Welshpool, WA | 218 | kg CO₂e/m³ | 2029-08 | Tier B | ✓ |
| 2 | Standard 32 MPa | BGC | Naval Base, WA | 295 | kg CO₂e/m³ | 2028-04 | Tier C | — |
| 3 | … | … | … | … | … | … | … | … |

Industry baseline (CCAA, 32 MPa): ~360 kg CO₂e/m³

Which ones should I save to your EPD library?
```

### Presentation rules

- **Sort by GWP (lowest first)** — environmental performance is the primary ranking criterion
- **Include industry baseline** for the material category if known (CCAA for AU concrete, MECLA published baselines, Worldsteel / WSA for steel) — never invent a baseline; cite source
- **Include "Why"** for each — explain why this EPD is relevant to the brief, flag transport / availability trade-offs
- **Flag expired EPDs** — include them if relevant but clearly mark as expired
- **Note system boundary differences** — if some are cradle-to-gate and others cradle-to-grave, call it out
- **Distinguish EPD types** — product-specific vs industry-average matters for Green Star / LEED scoring
- **Cross-reference rating tools** — note Green Star Responsible Products tier (Recognised / Best Practice / Leadership), NABERS Embodied Emissions compliance, LEED MRc2 eligibility (international projects)

## Step 4: Save to Sheet

When the user picks EPDs ("save 1, 3, and 5"), write them to the EPD Google Sheet using the 42-column schema (see `/epd-parser` for the full schema definition).

### Connecting to the sheet

If not already connected, ask for the Google Sheet ID or URL. This is a **separate spreadsheet** from the FF&E product library — EPD data has a different schema.

### Row format

Write to the 42-column EPD schema. Set:
- `Parsed At` — current ISO timestamp
- `Source` — `epd-research`
- `Notes` — the "Why" reasoning + Green Star tier / NABERS compliance + any caveats
- `Tags` — from brief context (e.g., `32-mpa`, `perth-metro`, `wa`, `green-star-tier-b`, project name)
- `LEED Eligible` — based on EPD type and verification status (legacy field; retain for international)

### After saving

```
Saved 3 EPDs to your library (rows 12–14).
Tagged: 32-mpa, perth-metro, wa, green-star-tier-b

Want me to compare these? Or search for more options?
```

## Step 5 (Optional): Iterate

The user may want to refine:

- **"Any with lower GWP?"** — search for higher-SCM blends (slag, fly ash), newer EPDs, geopolymer concrete (Wagners EFC), Earth Friendly Concrete
- **"What about precast?"** — expand to related categories (`0351 Precast concrete`)
- **"Compare #1 and #2"** — hand off to `/epd-compare`
- **"Write spec language for #1's GWP as the max threshold"** — hand off to `/epd-to-spec`
- **"Find the PDF for #3 so I can parse the full data"** — search for downloadable EPD document, then offer `/epd-parser`

## Conversation Style

- **Don't over-ask before searching.** A material name is enough to start.
- **Show results, then refine.** It's faster to react to real options than to specify everything upfront.
- **Be opinionated.** Flag the lowest-GWP options, note which are below industry baseline, recommend what to specify.
- **Know the WA / AU industry.** Understand that GWP varies by region (local plants use local materials, transport distances matter), that SCM content drives concrete GWP, that Australian steel is largely BOF (BlueScope Port Kembla) but EAF capacity is growing, that insulation GWP depends on blowing agent, that timber EPDs benefit from biogenic carbon storage.

## Notes

### Australian rating tools — quick reference

- **Green Star Buildings v1 / v2 (GBCA)** — the dominant AU green building certification. The **Responsible Products** credit specifies which EPD types and certifications earn points. Recognised Initiatives currently include: EPD Australasia, Environdec, IBU, UL Environment, Global GreenTag (LCARate, PhD), GECA, Declare, Cradle to Cradle, FSC, PEFC, Forest Stewardship.
  - **Tier C (Recognised)** — entry level: an EPD or eco-label exists
  - **Tier B (Best Practice)** — performance below the relevant benchmark
  - **Tier A (Leadership)** — significant performance leadership

- **NABERS Embodied Emissions** — module developed by NABERS (administered by NSW DPE) to measure whole-building embodied carbon at design stage. Provides a star rating against an industry benchmark. EPDs feed into the calculation.

- **NCC 2022 Section J** (Energy efficiency) — not directly EPD-driven, but relevant for envelope materials. High-performance insulation, glazing, and cladding may improve operational energy performance at the cost of embodied carbon — flag the trade-off when relevant.

- **NCOS / Climate Active** — Climate Active is the federal carbon-neutral certification scheme. Some manufacturers (e.g. Adbri, Boral) have Climate Active certified products — note this in the EPD entry.

### EC3 (Building Transparency)

EC3 is the largest EPD database, but all data is behind authenticated API access — `site:buildingtransparency.org` web searches will not return results. If the user hasn't configured EC3 API credentials, tell them: *"EC3 has the largest EPD database but requires a free API key from buildingtransparency.org (professional account with a business email). I'll search EPD Australasia, Environdec, and manufacturer sites directly instead."* Then proceed with the other sources listed above.

### General rules

- **EPD validity matters.** A 2019 EPD based on EN 15804+A1 is less useful than a 2024 EPD based on +A2. Prefer newer EPDs when available.
- **Regional EPDs are more useful than national averages.** A plant-specific EPD from a nearby facility (Welshpool, Naval Base, Wangara, Kwinana) is more valuable for a WA project than a national average.
- **Transport (A4) matters more in WA.** Perth is geographically isolated; A4 transport emissions can be a meaningful chunk of total embodied carbon for imported products. Where the EPD reports A4, present it; where it doesn't, flag transport as an out-of-scope consideration.
- **This skill finds EPDs. `/epd-parser` extracts full data from the PDFs.** If the user wants deep data from a found EPD, download the PDF and run `/epd-parser`.
