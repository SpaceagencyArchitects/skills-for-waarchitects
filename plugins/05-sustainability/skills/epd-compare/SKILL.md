---
name: epd-compare
description: Compare 2+ products side-by-side on environmental impact metrics. Normalises declared units, checks system boundary alignment, and flags Green Star Responsible Products tier, NABERS Embodied Emissions compliance, and LEED MRc2 eligibility.
allowed-tools:
  - Read
  - Write
  - Edit
  - Bash
  - Glob
  - Grep
  - AskUserQuestion
  - mcp__google-sheets__get_sheet_data
  - mcp__google-sheets__list_sheets
---

# /epd-compare — EPD Comparator

Compare 2 or more products side-by-side on environmental impact metrics. Validates comparability (declared units, system boundaries, PCR alignment), generates comparison tables with percentage deltas, and assesses against the relevant rating tool — **Green Star Responsible Products** and **NABERS Embodied Emissions** for AU projects, **LEED v4.1 MRc2** for international.

This skill **reads** from the EPD Google Sheet but does **not write** to it. Output is a markdown comparison report.

**Default jurisdiction: Western Australia.** Australian English, metric (m², m³, kg) throughout. Falls back to LEED-only assessment for international projects.

## Input

The user provides EPD data in one of these ways:

1. **Sheet row references** — "compare rows 5, 8, and 12" from the EPD Google Sheet
2. **Inline data** — pasted product names with GWP values
3. **File path** — a CSV or markdown file with EPD data
4. **From prior skills** — "compare the EPDs I just parsed / found" (uses data from the current conversation)
5. **Mixed** — "compare this PDF I just parsed against what's in the sheet"

If the user doesn't specify a source, ask: **"Where is the EPD data? Sheet rows, pasted values, or from earlier in this conversation?"**

## Workflow

### Step 1: Collect data

Gather EPD data from the specified source. For each product, you need at minimum:

- Product name and manufacturer
- GWP (A1–A3) value
- Declared unit

Additional fields improve the comparison: ODP, AP, EP, POCP, energy use, water use, system boundary, PCR, validity dates, programme operator, rating-tool eligibility, plant location.

### Step 2: Validate comparability

Before comparing, run these checks and report findings:

**Declared unit alignment:**
- Are all products using the same declared unit (e.g., all per m³, all per kg)?
- If units differ, attempt normalisation where possible:
  - kg ↔ tonne (multiply / divide by 1000)
  - m² at different thicknesses (if thickness is known, normalise to same thickness)
- If normalisation is impossible, warn: "Product A reports per m², Product B reports per kg. Direct comparison requires density or thickness data. Provide conversion factors, or I'll compare only within matching units."
- **Never silently compare products with different declared units.**

**System boundary alignment:**
- Flag if some are cradle-to-gate (A1–A3) and others cradle-to-grave (A1–A3 + C1–C4 + D).
- Note: "A1–A3 comparison is still valid across both types. Full life cycle comparison is only valid for cradle-to-grave EPDs."

**PCR alignment:**
- Flag if products use different PCRs. Products under the same PCR are most directly comparable.
- Note the PCR names if they differ.

**EN 15804 version:**
- Flag if some use +A1 and others +A2. Impact indicator units may differ (AP in kg SO₂e vs mol H+ eq).
- GWP in kg CO₂e is comparable across versions.

**Validity:**
- Flag any expired EPDs with their expiration date.

**EPD type:**
- Flag mix of product-specific and industry-average EPDs. Note that industry-average EPDs are less precise and earn lower rating-tool points.

**Programme operator:**
- Flag if comparing across operators (e.g., EPD Australasia vs Environdec vs UL). All ISO 14025 / EN 15804 conforming operators produce comparable EPDs, but methodology details may differ.

**Transport (A4) — WA-specific:**
- For WA projects, flag plant location and approximate distance to Perth where data is available. A4 is often outside the EPD scope but is a real cost — note where transport may shift the picture (e.g., a low-A1–A3 product imported from Europe vs a slightly-higher product made in Welshpool).

Report all findings before proceeding:

```
## Comparability Check

✓ Declared unit: All products use 1 m³
✓ System boundary: All cradle-to-gate (A1–A3)
⚠ PCR: Products 1–2 use EPD Australasia PCR for Concrete (2023), Product 3 uses NSF PCR — results are comparable but not identical methodology
⚠ Validity: Product 2 expired 2025-12-01
✓ EPD type: All product-specific
✓ Standard: All EN 15804+A2
ℹ Plant locations: P1 Welshpool WA (~10 km from Perth), P2 Kewdale WA (~15 km), P3 Geelong VIC (~3,400 km — A4 transport not in scope)
```

### Step 3: Generate comparison

Produce three or four outputs depending on rating-tool context:

#### a. Side-by-side impact table

```
## Environmental Impact Comparison

| Metric | ENVISIA (Boral) | Standard (BGC) | ReadyMix (Hanson) | Unit |
|---|---:|---:|---:|---|
| **GWP-total (A1–A3)** | **218** | 295 | 365 | kg CO₂e/m³ |
| GWP-fossil (A1–A3) | 215 | 290 | — | kg CO₂e/m³ |
| GWP-biogenic (A1–A3) | 3 | 5 | — | kg CO₂e/m³ |
| ODP (A1–A3) | 1.1 × 10⁻⁶ | 1.4 × 10⁻⁶ | 1.7 × 10⁻⁶ | kg CFC-11e/m³ |
| AP (A1–A3) | 0.42 | 0.50 | 0.59 | mol H+ eq/m³ |
| EP-fw (A1–A3) | 0.07 | 0.10 | 0.13 | kg P eq/m³ |
| PERE (A1–A3) | 195 | 110 | 80 | MJ/m³ |
| PENRE (A1–A3) | 1,380 | 1,820 | 2,260 | MJ/m³ |
| FW (A1–A3) | 0.30 | 0.39 | 0.52 | m³/m³ |
| Recycled / SCM Content | 38 % | 22 % | 14 % | % |
```

Bold the **best value** in each row (lowest for impacts, highest for recycled content / renewable energy). Use `—` for missing data. Never fill in missing values.

#### b. Percentage comparison (relative to lowest)

```
## GWP Comparison (relative to lowest)

| Product | GWP (A1–A3) | vs Lowest | vs Industry Baseline |
|---|---:|---:|---:|
| ENVISIA (Boral) | 218 kg CO₂e/m³ | — baseline — | −39 % |
| Standard (BGC) | 295 kg CO₂e/m³ | +35 % | −18 % |
| ReadyMix (Hanson) | 365 kg CO₂e/m³ | +67 % | +1 % |
| *CCAA industry baseline (32 MPa)* | *~360 kg CO₂e/m³* | — | — |
```

Include an industry baseline **only if the user provides one** (e.g., from CCAA / MECLA published benchmarks for AU concrete, Worldsteel for steel, an industry-average EPD, or a published baseline document). Do not use approximate or hardcoded baselines.

If the user hasn't provided a baseline, ask: **"Do you have an industry baseline for this material category? CCAA publishes baselines for Australian concrete (32 MPa, 40 MPa etc.); MECLA publishes member benchmarks; Worldsteel / WSA publishes steel baselines. If you have one, share it. Otherwise I'll omit the baseline column rather than guess."**

If no baseline is available, omit the "vs Industry Baseline" column entirely rather than guessing.

#### c. Green Star Responsible Products assessment (default for AU)

Include this section when AU rating tools apply (the default for WA projects):

```
## Green Star Buildings — Responsible Products Credit

Each product evaluated against Green Star tiers (Recognised / Best Practice / Leadership).

| Product | EPD Type | Programme Operator | Verification | Tier | Notes |
|---|---|---|---|---|---|
| ENVISIA (Boral) | Product-specific | EPD Australasia | thinkstep-anz | **Tier B (Best Practice)** | EN 15804+A2; performance below CCAA baseline |
| Standard (BGC) | Product-specific | EPD Australasia | thinkstep-anz | Tier C (Recognised) | EN 15804+A2; meets entry level |
| ReadyMix (Hanson) | Product-specific | NSF | UL Verifier | Tier C (Recognised) | EN 15804+A1; international PO recognised under Green Star |

### Tier definitions (current GBCA framework)
- **Tier C — Recognised**: ISO 14025 / EN 15804 conforming EPD or recognised eco-label exists
- **Tier B — Best Practice**: demonstrated performance below the relevant benchmark
- **Tier A — Leadership**: significant performance leadership

> Final tier assignment must be confirmed by the project's Green Star Accredited Professional (AP). Always verify against the current Green Star Buildings Submission Guidelines and Recognised Initiatives list at gbca.org.au.
```

#### d. NABERS Embodied Emissions assessment

```
## NABERS Embodied Emissions Module

The NABERS Embodied Emissions module measures whole-building embodied carbon. EPDs feed
into the calculation; the module compares against an industry benchmark to produce a star rating.

| Product | EPD Compliant? | A1–A3 GWP (kg CO₂e/m³) | Notes |
|---|---|---:|---|
| ENVISIA (Boral) | ✓ | 218 | EN 15804+A2, third-party verified |
| Standard (BGC) | ✓ | 295 | EN 15804+A2, third-party verified |
| ReadyMix (Hanson) | ✓ | 365 | EN 15804+A1 — note version difference |

Whole-building star rating depends on total project embodied carbon (kg CO₂e/m² GFA) — this comparison is one input. Coordinate with the NABERS-accredited assessor for the project rating.
```

#### e. LEED v4.1 MRc2 assessment (international or LEED-targeting projects)

Include this section if the user mentions LEED, the project is international, or LEED eligibility data is in the EPD records:

```
## LEED v4.1 MRc2 — Building Product Disclosure and Optimization

### Option 1 — EPD Disclosure (1 point for 20+ products with EPDs)
| Product | Qualifying EPD? | Type | Notes |
|---|---|---|---|
| ENVISIA (Boral) | ✓ | Product-specific | Third-party verified, ISO 14025 conforming |
| Standard (BGC) | ✓ | Product-specific | Third-party verified |
| ReadyMix (Hanson) | ✓ | Product-specific | Third-party verified |

### Option 2 — Embodied Carbon Optimization (up to 2 points)
Products must demonstrate GWP below category baseline:
| Product | GWP | Baseline | Delta | Qualifies? |
|---|---:|---:|---:|---|
| ENVISIA | 218 | 360 | −39 % | ✓ Yes — significant reduction |
| Standard | 295 | 360 | −18 % | ✓ Yes |
| ReadyMix | 365 | 360 | +1 % | No — at / above baseline |
```

### Step 4: Recommendation summary

End with a brief recommendation:

```
## Recommendation

**ENVISIA by Boral** is the clear winner on environmental performance — 39 % below
the CCAA industry baseline GWP and lowest across all impact categories. The Welshpool
plant is closest to the project site (~10 km), which also keeps A4 transport emissions
low (out of EPD scope but a real consideration for whole-building LCA).

**BGC Concrete** is a strong second option — also WA-manufactured, 18 % below baseline.
Useful as a backup if Boral can't meet schedule or volume.

**Hanson** would require shipping from Geelong (Vic) — significant additional A4 transport
that isn't captured in the A1–A3 numbers. Avoid for this project.

Both Boral and BGC qualify for Green Star Tier B (Best Practice) and contribute positively
to NABERS Embodied Emissions. Both also qualify for LEED MRc2 Option 1 and Option 2 if
the project is dual-rated.
```

Be direct and opinionated. The user wants a recommendation, not just data.

### Step 5: Save output

Save the comparison report as markdown:

- **Default path**: `./epd-comparison-YYYY-MM-DD.md`
- If the user says it's final: `./deliverables/`
- Ask the user if they want a different path

After saving:

```
Comparison saved to [path].

Next steps:
- /epd-to-spec — generate NATSPEC spec language using ENVISIA's GWP (218) as the threshold
- /epd-research — find more options to compare (geopolymer / EFC concrete for further reduction?)
```

## Edge Cases

- **Single product**: Can't compare, but can still show the data card with industry baseline context. Suggest finding more EPDs to compare against.
- **Mixed material categories**: Warn that cross-material comparison (e.g., concrete vs steel) is generally not meaningful because declared units and functional roles differ. Offer to group by category.
- **Incomplete data**: Compare on whatever fields are available. Use `—` for missing values. Note which products have more complete data.
- **All expired EPDs**: Proceed with comparison but add a prominent warning that results are based on expired declarations and should be verified with current EPDs.
- **Very large comparisons (10+ products)**: Show summary table first, then offer to drill into top 3–5 candidates.
- **AU + international mix**: Comparison still works — flag the programme operator difference and any A4 transport implications.
- **Green Star tier uncertainty**: Tier B / Tier A determination depends on benchmark performance and may require Green Star AP review. Default to Tier C unless evidence of benchmark performance is strong; flag for AP confirmation.

## Notes

- **This skill reads, not writes.** It does not add rows to the EPD Google Sheet. It produces a comparison report as a markdown file.
- **No hardcoded baselines.** Never use approximate GWP baselines from training data. If the user needs a baseline comparison, ask them to provide an industry-average EPD, a CCAA / MECLA published benchmark, or use `/epd-research` to find one.
- **GWP (A1–A3) is the primary metric** for most comparisons and rating tools. Other indicators (ODP, AP, EP) provide a fuller picture but GWP drives most specification decisions.
- **Australian rating-tool context — Green Star is the dominant AU certification.** NABERS focuses on operational performance but its Embodied Emissions module is gaining adoption. LEED is used in AU for international clients or for cross-market portfolios.
- **Suggest next skills.** After comparison, the natural next steps are `/epd-to-spec` (to write NATSPEC spec language) or `/epd-research` (to find alternatives — particularly geopolymer / Earth Friendly Concrete / alkali-activated alternatives for further GWP reduction).
