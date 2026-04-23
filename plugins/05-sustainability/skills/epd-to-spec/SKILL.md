---
name: epd-to-spec
description: Generate NATSPEC-formatted (or CSI-fallback) specification clauses requiring EPDs and setting maximum GWP thresholds. References ISO 14025, ISO 21930, EN 15804+A2, AS / AS-NZS, NCC 2022, and aligns with Green Star Responsible Products and NABERS Embodied Emissions credits.
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

# /epd-to-spec — EPD Specification Writer

Takes EPD data, GWP limits, or a materials list and generates **NATSPEC-formatted** specification clauses (default) requiring Environmental Product Declarations and setting maximum Global Warming Potential thresholds. Output follows the same three-part structure used by `/spec-writer` (1 General / 2 Products / 3 Execution).

For overseas / US-client projects the skill switches to **CSI MasterFormat** with three-part section format (Part 1 General / Part 2 Products / Part 3 Execution).

**Default jurisdiction: Western Australia.** NATSPEC + AS / AS-NZS + NCC 2022 + Green Star + NABERS. Falls back to CSI + ASTM + IBC + LEED for international.

Follows `rules/natspec-formatting.md`, `rules/code-citations.md`, `rules/professional-disclaimer.md`. Pairs with `/spec-writer`.

## Input

The user provides one or more of:

1. **Material list with GWP limits** — "concrete max 300 kg CO₂e/m³, rebar max 1.0 kg CO₂e/kg"
2. **EPD data from the sheet** — "use the EPDs I saved to set thresholds"
3. **Comparison report** — "use the lowest GWP from my last comparison as the max"
4. **Rating-tool target** — "we're pursuing Green Star Buildings 5-Star, Responsible Products credit Tier B"
5. **Verbal description** — "write EPD requirements for a Fremantle commercial fit-out, concrete and steel structure, aluminium curtain wall, suspended ceiling"
6. **Project type** — helps determine which NATSPEC worksections need EPD language

If the user invokes the skill without input, ask:

1. **What materials need EPD requirements?** (paste a list, reference the sheet, or describe the project)
2. **Which rating tool is the project targeting?** (Green Star Buildings, NABERS, LEED, none / general low-carbon)
3. **Do you have specific GWP thresholds, or should I use industry baselines?** (CCAA for AU concrete, MECLA member benchmarks, Worldsteel for steel, etc.)

## NATSPEC Worksections Where EPDs Are Most Common

Map materials to the correct worksection. EPD requirements are most relevant for structural, envelope, and interior finish materials with high embodied carbon:

| Worksection | Title | Common EPD Products |
|---|---|---|
| `0341` | Concrete in situ | Ready-mix concrete (25, 32, 40, 50 MPa), low-carbon blends (ENVISIA, EcoBlend, EFC) |
| `0351` | Precast concrete | Architectural and structural precast |
| `0321` | Steel reinforcement | Rebar, mesh (InfraBuild, OneSteel) |
| `0511` | Structural steelwork | Hot-rolled sections, HSS, plate (BlueScope XLERPLATE, TrueCore) |
| `0561` | Metalwork | Miscellaneous metals, balustrades |
| `0611` | Timber framing | Dimensional timber, engineered timber (LVL, glulam) |
| `0651` | Stairs and balustrades | Engineered timber stairs, CLT |
| `0671` | Joinery | Plywood, MDF, particleboard (Laminex, Carter Holt Harvey) |
| `0711` | Thermal insulation | Glass wool, mineral wool, polyester, PIR / phenolic (CSR Bradford, Knauf, Kingspan) |
| `0731` | Membranes | Roofing and below-ground membranes |
| `0741` | Metal roofing | Colorbond, Zincalume (BlueScope) |
| `0851` | Windows — aluminium | Capral, Alspec, G.James |
| `0881` | Glazing | IGUs, low-E (Viridian, G.James, Pilkington) |
| `0911` | Plasterboard linings | Standard / fire-rated / wet-area (CSR Gyprock, Knauf, USG Boral) |
| `0931` | Tiling | Porcelain, ceramic |
| `0951` | Suspended ceilings | Mineral fibre tile (Armstrong, Rockfon, USG Boral) |
| `0961` | Resilient flooring | LVT, vinyl plank (Karndean, Polyflor, Gerflor) |
| `0971` | Carpet | Carpet tile, broadloom (Godfrey Hirst, Interface, Tarkett) |
| `0981` | Painting | Low-VOC interior / exterior (Dulux, Haymes, Wattyl) |
| `0231` | External pavements | Asphalt, concrete pavers |

For Domestic (Class 1) projects, prefix with `D` (e.g. `D0341 Concrete in situ`).

For overseas / CSI projects, the equivalent CSI division mapping is retained — see Appendix A at the end of this skill.

## Spec Generation Workflow

### Step 1: Parse and classify materials

Read the user's input and build an inventory:

- **Material / product** — as provided
- **NATSPEC worksection number and title** — mapped from the material type (per `rules/natspec-formatting.md`)
- **GWP threshold** — user-provided limit, EPD sheet value, or industry baseline
- **Declared unit** — must match the unit used in the GWP threshold

If the user provided EPD sheet data or a comparison report, extract the GWP values and declared units from there.

If no GWP thresholds are specified, **do not use approximate baselines.** Instead, ask the user:

> **"I need GWP thresholds to write the spec. You can provide them by:
> 1. **Sharing an EPD** — I'll extract the GWP value and declared unit
> 2. **Using `/epd-research`** — I'll find EPDs for your material categories
> 3. **Using `/epd-compare`** — compare products and pick a threshold from the results
> 4. **Stating a number** — e.g. 'concrete max 300 kg CO₂e/m³'
> 5. **Citing a published baseline** — CCAA for AU concrete, MECLA member benchmarks, Worldsteel / WSA for steel, etc.
>
> I won't use approximate baselines from memory. Please pick one of the above, or I can write the spec with `[THRESHOLD TBD]` placeholders that you can fill in later."**

If the user asks to proceed without data, use `[THRESHOLD TBD]` placeholders and flag every one clearly.

Report the mapping:

```
Identified X materials across Y worksections:
- 0341 Concrete in situ: GWP max 300 kg CO₂e/m³ (from comparison — Boral ENVISIA basis)
- 0511 Structural steelwork: GWP max 2.4 kg CO₂e/kg [VERIFY THRESHOLD — Worldsteel BOF baseline ~2.5]
- 0711 Thermal insulation (glass wool R 2.5): GWP max 1.8 kg CO₂e/kg [VERIFY THRESHOLD]
```

Ask: **"Does this mapping look correct? Any thresholds to adjust?"**

### Step 2: Generate worksection clauses

For each material, write a three-part outline. The EPD-specific language is concentrated in **1 General** (submissions, quality, sustainability) and **2 Products** (environmental performance requirements). **3 Execution** is generally unchanged from the standard worksection.

The structure below is for NATSPEC. For CSI fallback, swap to "Part 1 — General" / 1.01 / 1.02 numbering and use the equivalent CSI section number.

#### 1 General

**1.1 Cross-references**
Standard cross-references PLUS:
- `0181 General requirements — sustainability`: project sustainability targets and submission protocols
- The relevant trade-coordination worksections

**1.2 Standards**
Standard material standards PLUS:
- ISO 14025, *Environmental labels and declarations — Type III environmental declarations — Principles and procedures*.
- ISO 21930, *Sustainability in buildings and civil engineering works — Core rules for environmental product declarations of construction products and services*.
- EN 15804+A2, *Sustainability of construction works — Environmental product declarations — Core rules for the product category of construction products*.
- ISO 14044, *Environmental management — Life cycle assessment — Requirements and guidelines*.
- AS ISO 14025:2007, *Environmental labels and declarations — Type III environmental declarations* (AS adoption).

**1.4 Submissions**
Add the following EPD-specific submissions:

```
[d] Environmental Product Declaration (EPD):
    [i]   Submit a current product-specific Type III EPD conforming to ISO 14025
          and either EN 15804+A2 or ISO 21930.
    [ii]  EPD shall be published by an accredited programme operator including
          but not limited to: EPD Australasia, Environdec (International EPD
          System), IBU, UL Environment, NSF, SCS Global Services or ASTM.
    [iii] EPD shall be third-party verified by an independent verifier approved
          by the programme operator (e.g. thinkstep-anz, Edge Environment,
          LCAS, EPD Verification).
    [iv]  EPD shall be current and valid at the time of submission. Expired
          EPDs are not acceptable.
    [v]   EPD shall report environmental impacts for life cycle stages A1
          through A3 at minimum. Cradle-to-grave EPDs (A1–C4 + D) are
          preferred.
    [vi]  Where a product-specific EPD is not available, an industry-average
          or sector EPD may be submitted with prior approval, and shall be
          accompanied by documentation demonstrating that the product-specific
          EPD is not available.
    [vii] If the product is sourced from a different manufacturing plant
          than the one covered by the submitted EPD, provide documentation
          that the EPD is representative of the actual production facility,
          or submit a plant-specific EPD.

[e] Manufacturer's environmental declaration: where an ISO 14025 EPD is not
    available, submit the manufacturer's environmental data sheet covering
    composition, recycled content, and any third-party eco-label
    certifications (Global GreenTag, GECA, Declare, FSC, PEFC).
```

**1.5 Inspections**
Standard. No EPD-specific changes.

**1.6 Quality**
Standard quality clauses PLUS:

```
Environmental performance verification: where the supplied product is sourced
from a different plant or batch than the submitted EPD, the Contractor shall
demonstrate that the actual product meets the maximum GWP threshold specified
in clause 2.X.
```

**1.7 Warranties**
Standard. No EPD-specific changes.

**1.8 Sustainability requirements** (new clause — add if not already in the worksection)

```
[a] Environmental performance:
    [i]  Provide products with current Type III EPDs meeting the
         requirements of clause 1.4[d].
    [ii] Maximum Global Warming Potential (GWP), life cycle stages A1–A3:
         . [Material A]: [VALUE] kg CO₂e per [DECLARED UNIT]
         . [Material B]: [VALUE] kg CO₂e per [DECLARED UNIT]
    [iii] GWP shall be calculated in accordance with ISO 21930 / EN 15804+A2
          using characterisation factors from IPCC AR5 or later.

[b] Rating tool documentation (insert as applicable):
    [i]  GREEN STAR — provide EPD documentation in the format required for
         the project's Green Star Buildings Responsible Products credit
         submission. Coordinate with the Project's Green Star Accredited
         Professional.
    [ii] NABERS EMBODIED EMISSIONS — provide product GWP data in the format
         required for the project's NABERS Embodied Emissions assessment.
         Coordinate with the project's NABERS-accredited assessor.
    [iii] LEED v4.1 MRc2 — provide EPD documentation in the format required
          for LEED MRc2 credit submission.

[c] Recycled / SCM content:
    [i]  Where applicable, minimum [VALUE] % by mass.
    [ii] Substitute Cementitious Material (SCM) — for concrete only — minimum
         [VALUE] % blend of fly ash, slag (GGBFS), or silica fume to AS 3582.

[d] Regional materials (preference, not requirement):
    [i]  Preference shall be given to products manufactured within
         [DISTANCE] km of the project site. The intent is to reduce A4
         transport emissions which are typically not captured in A1–A3 EPD
         scopes.
    [ii] For a Perth metro project, suppliers within Perth metro
         (~50 km) are preferred; suppliers in other states (e.g. Boral
         Geelong, BlueScope Port Kembla) require A4 disclosure.
```

#### 2 Products

**2.1 Manufacturers**
Standard manufacturer list PLUS:

```
All listed manufacturers shall provide a current, valid Type III EPD meeting
the requirements of clause 1.4[d]. Manufacturers unable to provide a conforming
EPD shall not be approved as substitutes.
```

**2.2 Materials**
Standard material specifications. No EPD-specific changes.

**2.3 Performance** — add an "Environmental performance" sub-clause

```
[a] Maximum GWP, A1–A3 (per declared unit):
    [Material]: maximum [VALUE] kg CO₂e per [DECLARED UNIT].
    GWP shall be as reported in the product-specific EPD submitted under
    clause 1.4[d].

[b] Where multiple grades / classes / mixes are supplied (e.g. concrete at
    25, 32, 40 MPa; steel at different grades), maximum GWP applies to each
    grade independently and shall be evidenced by separate EPDs or a
    multi-product EPD covering each grade.

[c] Acceptable AU low-carbon products meeting the threshold (indicative
    basis-of-design — equivalents accepted):
    . [Concrete]: Boral ENVISIA, Cement Australia EcoBlend, Wagners EFC,
      BGC low-carbon concrete
    . [Steel]: BlueScope XLERPLATE EAF (where available), InfraBuild
      EAF rebar
    . [etc.]
```

**2.4 Finishes**
Standard. No EPD-specific changes.

**2.5 Components**
Standard. No EPD-specific changes.

#### 3 Execution

Standard execution language for the material. No EPD-specific additions:

- 3.1 Preparation
- 3.2 Installation
- 3.3 Tolerances
- 3.4 Completion
- 3.5 Cleaning

### Step 3: Add rating-tool appendix (if applicable)

#### Green Star Buildings — Responsible Products credit

If the project is targeting Green Star, append a dedicated appendix at the end of the spec:

```
## Appendix — Green Star Buildings: Responsible Products

Each major construction product shall be assessed against the Green Star
Responsible Products framework.

### Recognised Initiatives
The following are currently recognised by the GBCA — verify against the
current Green Star Buildings Submission Guidelines at gbca.org.au:
- EPD Australasia
- Environdec (International EPD System)
- IBU
- UL Environment
- Global GreenTag (LCARate, PhD)
- Good Environmental Choice Australia (GECA)
- Declare (ILFI)
- Cradle to Cradle
- FSC, PEFC, Forest Stewardship (timber)

### Tier framework
- **Tier C — Recognised**: ISO 14025 / EN 15804 conforming EPD or recognised eco-label exists
- **Tier B — Best Practice**: demonstrated performance below the relevant benchmark
- **Tier A — Leadership**: significant performance leadership

### Documentation requirements
[a] Maintain a project Responsible Products register (spreadsheet or sheet) tracking:
    manufacturer, product, NATSPEC worksection, EPD registration number,
    programme operator, GWP (A1–A3), declared unit, Green Star tier achieved,
    location of manufacture.
[b] Submit register to the Project's Green Star Accredited Professional at
    nominated milestones.
[c] Retain copies of all submitted EPDs for the project record.
```

#### NABERS Embodied Emissions

If the project is targeting NABERS Embodied Emissions, append:

```
## Appendix — NABERS Embodied Emissions

The NABERS Embodied Emissions module measures whole-building embodied carbon
at design stage and produces a star rating against an industry benchmark.

### Documentation requirements
[a] Provide product GWP data in the format required by the NABERS-accredited
    assessor for inclusion in the project Embodied Emissions assessment.
[b] Where EPDs are missing for a product category, default values from the
    NABERS Embodied Emissions methodology shall apply (typically resulting
    in a higher embodied-carbon allocation — providing an EPD is favourable
    to the project rating).
[c] Coordinate with the NABERS-accredited assessor for cradle-to-practical-
    completion scope, allocation rules, and the project's target star rating.
```

#### LEED v4.1 MRc2 (international or LEED-targeting)

If the project is pursuing LEED:

```
## Appendix — LEED v4.1 MRc2: Building Product Disclosure and Optimization

### Option 1 — Environmental Product Disclosure (1 point)
Use at least 20 permanently installed products sourced from at least five
different manufacturers that have Type III EPDs conforming to ISO 14025.

Products with product-specific EPDs: 1 product = 1 product count.
Products with industry-wide (generic) EPDs: 1 product = 0.5 product count.

### Option 2 — Environmental Product Optimization (up to 2 points)
Products that demonstrate impact reduction below baseline:
- Products with EPDs showing impact reduction compared to industry average
  earn additional credit.
- Third-party verified product-specific EPDs preferred.
- Whole-building life cycle assessment (WBLCA) per ISO 14044 may be used as
  an alternative compliance path.

### Documentation requirements
1. Maintain a product EPD log tracking all qualifying products.
2. For each product, document: manufacturer, product name, EPD registration
   number, programme operator, GWP (A1–A3), and declared unit.
3. Submit EPD log to LEED reviewer as part of MRc2 credit documentation.
```

### Step 4: Add cross-reference to NCC 2022 Section J (envelope materials only)

For envelope materials (insulation `0711`, glazing `0881`, windows `0851`, roofing `0741`, cladding), add a brief note acknowledging the embodied / operational trade-off:

```
> NOTE — NCC 2022 Section J interaction: this worksection's product selection
> may be governed by NCC 2022 Vol. 1 Section J (Energy efficiency) thermal
> performance requirements. High-performance options (thicker insulation,
> double / triple glazing, high-R cladding) reduce operational energy
> emissions but typically increase embodied carbon. Evaluate the trade-off
> in conjunction with the project's whole-of-life carbon assessment;
> coordinate with the ESD consultant.
```

### Step 5: Add spec notes

Apply `[REVIEW REQUIRED]` flags when:

- GWP thresholds are based on industry baselines rather than project-specific targets
- The worksection covers life-safety materials (firestopping, fire-rated assemblies, wet-area waterproofing)
- Performance criteria are assumed
- Green Star tier is asserted as Best Practice / Leadership without confirmed benchmark performance — must be confirmed by Green Star AP

Apply `[VERIFY THRESHOLD]` flags when:

- GWP values are industry baselines that should be confirmed with current data
- Declared units need confirmation for the specific product being specified
- The threshold is sourced from a single EPD that may not represent supplier capability

### Step 6: Write output

Compile all worksections into a single `.md` file organised by worksection number.

**Default output path**: `./epd-specs-[project-slug].md`

- Derive `[project-slug]` from the project name or type (lowercase, hyphenated)
- If no project name: `epd-specs-draft.md`
- If no client context: `./deliverables/`
- Ask the user if they want a different path

**File structure:**

```markdown
# EPD Specifications — [Project Name]

**Generated:** [DD Month YYYY]
**Project type:** [type]
**Jurisdiction:** Western Australia (default)
**Specification system:** NATSPEC Building [or NATSPEC Domestic / CSI MasterFormat 2020]
**Rating tool target:** [Green Star Buildings X-Star / NABERS Embodied / LEED v4.1 / none]
**Worksections:** [count]

> **Disclaimer:** This is an AI-generated outline specification for preliminary
> design purposes only. It is not a contract document. A registered architect
> and suitably qualified specifier must review, verify, and supplement this
> outline. GWP thresholds must be confirmed against current EPDs and project
> sustainability targets prior to issue for tender.

---

## 0341 Concrete in situ

### 1 General
…

### 2 Products
…

### 3 Execution
…

---

## 0511 Structural steelwork
…
```

### Step 7: Summary

After writing the file, report:

```
EPD specifications written: X worksections
Output: [file path]
GWP thresholds set:
- 0341 Concrete in situ: 300 kg CO₂e/m³ (Boral ENVISIA basis)
- 0511 Structural steelwork: 2.4 kg CO₂e/kg [VERIFY THRESHOLD]
Rating tool: Green Star Buildings — Responsible Products
Worksections flagged for review: [count]
- [list flagged worksections]

Next steps:
- Review thresholds with the Green Star AP / ESD consultant
- Confirm WA-specific manufacturers can supply at the threshold
- Run /spec-writer for the full spec body — this skill provides EPD layer only
```

## Writing Style

- Use specification language throughout: "shall", "provide", "comply with", "to" (e.g. "to AS 3600")
- Imperative mood, third person
- No contractions
- No first person
- Capitalise **Architect**, **Superintendent**, **Principal**, **Contractor**, **Subcontractor**, **Installer**
- Reference standards by full designation on first use, abbreviated thereafter
- Use "approved equivalent" rather than "or equal"
- **Metric units** (mm, m, m², m³, kg, MPa, °C) — except GWP units which follow industry convention
- **Australian English spelling** (colour, centre, organise, metre, storey, kilometre, aluminium)
- **GWP values always include the unit** (kg CO₂e) and declared unit (per m³, per kg, per m²)

## Edge Cases

- **Single material**: Generate one worksection. Still include the full three-part structure.
- **No GWP threshold provided**: Do not use approximate baselines. Ask the user (see Step 1). If the user asks to proceed without data, use `[THRESHOLD TBD]` placeholders and flag every one.
- **Materials outside common EPD worksections**: Some categories (1011 Signage, 1031 Toilet accessories, 1211 Window treatments) rarely have EPDs. Note: "EPDs are uncommon for this product category. Consider requiring manufacturer environmental data sheets and any relevant eco-label (Global GreenTag, GECA) as alternative submissions."
- **Mixed metric / imperial**: GWP declared units follow industry convention — concrete in kg CO₂e/m³, steel in kg CO₂e/kg. Don't convert these.
- **Multiple concrete mixes**: Create separate GWP thresholds per strength class (25, 32, 40, 50 MPa) if the user specifies different mixes.
- **Mixed rating tools**: A project may target both Green Star and NABERS, or be cross-market (Green Star + LEED). Include all relevant appendices.
- **Heritage projects**: Where the existing fabric is heritage-significant, EPD requirements apply only to new materials. Match-existing materials are out of scope and should be flagged.

## Notes

- **This skill generates EPD-related spec language, not full specs.** For the body of the spec (general material specifications, execution requirements), pair with `/spec-writer`. This skill adds the EPD / sustainability layer to specific worksections.
- **No hardcoded baselines.** Never use approximate GWP baselines from training data. Always require the user to provide an EPD or a specific threshold (with source citation).
- **Green Star tiers require Green Star AP confirmation.** Default to Tier C unless evidence is strong. Tier B / Tier A determination depends on benchmark performance and rating tool methodology that may have changed.
- **Buy local: A4 transport in WA matters.** Perth's geographic isolation means a low A1–A3 product manufactured in NSW or Vic may have a worse total carbon footprint than a slightly-higher-A1–A3 product made in Welshpool or Kwinana. Where the user is making a buy-local case, write the regional materials clause as a requirement rather than a preference.

## Appendix A — CSI MasterFormat fallback (international / US-client projects)

For overseas projects, the same skill switches to CSI MasterFormat 2020. CSI section equivalents for the worksections above:

| NATSPEC | CSI MasterFormat |
|---|---|
| `0341` | `03 30 00 Cast-in-Place Concrete` |
| `0351` | `03 40 00 Precast Concrete` |
| `0321` | `05 50 00 Metal Fabrications` (rebar typically here) |
| `0511` | `05 12 00 Structural Steel Framing` |
| `0671` | `06 40 00 Architectural Woodwork` |
| `0711` | `07 21 00 Thermal Insulation` |
| `0731` | `07 27 00 Air Barriers` / `07 50 00 Membrane Roofing` |
| `0741` | `07 41 00 Roof Panels` |
| `0851` | `08 50 00 Windows` |
| `0881` | `08 80 00 Glazing` |
| `0911` | `09 21 00 Plaster and Gypsum Board` |
| `0931` | `09 30 00 Tiling` |
| `0951` | `09 51 00 Acoustical Ceilings` |
| `0961` | `09 65 00 Resilient Flooring` |
| `0971` | `09 68 00 Carpeting` |
| `0981` | `09 91 00 Painting` |

CSI sections use 6-digit numbering and Part 1 / Part 2 / Part 3 with 1.01, 1.02 sub-clause numbering. ASTM standards replace AS / AS-NZS. IBC replaces NCC. LEED replaces Green Star / NABERS in the rating-tool appendix.
