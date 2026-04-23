---
name: spec-writer
description: NATSPEC outline specification writer for Australian / WA practice — takes a materials or products list and generates structured specs with NATSPEC worksections, AS/NZS references, NCC citations, and acceptable manufacturers. CSI fallback for overseas projects.
allowed-tools:
  - Read
  - Write
  - Edit
  - Bash
  - Glob
  - Grep
---

# /spec-writer — NATSPEC Outline Specification Writer

Takes a materials list, product schedule, or project description and produces outline specifications organised by **NATSPEC worksections** (default) or **CSI MasterFormat 2020 sections** (for overseas / US-client projects). Output is a structured `.md` file ready for review by a senior specifier.

**Default specification system: NATSPEC Building** — the Australian national specification system administered by Construction Information Systems Limited. NATSPEC Domestic is used for residential work. Follows `rules/natspec-formatting.md`, `rules/code-citations.md` (NCC 2022, AS/NZS standards), and `rules/professional-disclaimer.md`.

## Input

The user provides materials / products in one of these ways:

1. **Pasted text** — a materials list, product schedule, or finishes legend pasted into the conversation
2. **File path** — path to a CSV, Excel export, schedule PDF, or markdown file containing materials / products
3. **Verbal description** — project type and general materials ("three-storey Fremantle office fit-out with double-brick party walls, rendered finish, Colorbond cladding, and timber-look vinyl plank")

If the user invokes the skill without input, ask:

1. **What is the project type?** (commercial fit-out, new build commercial, multi-residential, single dwelling, heritage, health, education, industrial)
2. **Which NATSPEC product?** — Default to **NATSPEC Building** for commercial / Class 2–9 work; use **NATSPEC Domestic** for Class 1 / single dwelling; ask if unclear.
3. **What materials or products should be specified?** (paste a list, provide a file path, or describe them)

If the project is clearly overseas (US address, international client, contract specifies CSI), switch to CSI mode and confirm with the user before proceeding.

## NATSPEC Worksection Groupings

Map every material / product to the correct worksection using the NATSPEC grouping below. Worksection numbers are **4 digits** and the title follows after a single space — e.g. `0411 Brick and block construction`. For Domestic work, prefix with `D` — e.g. `D0411 Brick and block construction`.

| Group | Subject | Common worksections |
|-------|---------|---------------------|
| 01xx | General — preliminaries, quality, contract admin | `0111 General requirements`, `0171 General engineering requirements` |
| 02xx | Sitework, demolition, earthworks, site services | `0241 Demolition`, `0251 Earthworks`, `0283 Stormwater` |
| 03xx | Structural engineering systems | `0311 Concrete formwork`, `0321 Steel reinforcement`, `0341 Concrete in situ` |
| 04xx | Masonry | `0411 Brick and block construction`, `0441 Stonework` |
| 05xx | Metalwork and structural steel | `0511 Structural steelwork`, `0561 Metalwork` |
| 06xx | Carpentry, joinery and timber | `0611 Timber framing`, `0651 Stairs and balustrades`, `0671 Joinery` |
| 07xx | Thermal, moisture and waterproofing | `0711 Thermal insulation`, `0731 Membranes`, `0741 Metal roofing`, `0751 Waterproofing — wet areas` |
| 08xx | Doors, windows and glazing | `0811 Metal doors and frames`, `0821 Timber doors`, `0851 Windows — aluminium`, `0881 Glazing` |
| 09xx | Linings, finishes and painting | `0911 Plasterboard linings`, `0931 Tiling`, `0951 Suspended ceilings`, `0961 Resilient flooring`, `0971 Carpet`, `0981 Painting` |
| 10xx, 11xx | Specialties, equipment, fixtures | `1011 Signage`, `1021 Toilet partitions`, `1031 Toilet accessories` |
| 12xx | Furnishings | `1211 Window treatments`, `1231 Benchtops` |
| 13xx | Special construction | `1321 Swimming pools`, `1341 Modular building systems` |
| 14xx | Conveying systems | `1411 Lifts` |
| 20xx–27xx | Mechanical, hydraulic, fire, electrical, communications services | `2011 Mechanical services`, `2211 Hydraulic services`, `2311 Fire services`, `2611 Electrical services`, `2711 Communications` |

**If uncertain of an exact worksection number, cite the title only and flag the number as `[confirm NATSPEC ref]`.** Do not invent worksection numbers.

### CSI MasterFormat fallback (overseas projects)

When working in CSI mode, use 6-digit section numbers with space separators (e.g. `09 30 00 Tiling`) grouped by MasterFormat 2020 divisions (03 Concrete through 26 Electrical, 33 Utilities). Follow the same three-part structure.

## Spec Generation Workflow

### Step 1: Parse and classify materials

Read the user's input and build an inventory:

- **Material / product name** — as provided
- **NATSPEC worksection number and title** — mapped from the material type
- **If the worksection number is not known with confidence**, cite title only and flag `[confirm NATSPEC ref]`

Sort by worksection number, then group. Combine related items under the same worksection where appropriate (e.g. two paint types both sit under `0981 Painting`).

Report the mapping to the user:

```
Identified X materials across Y worksections:
- 0731 Membranes: bituminous membrane to deck, proprietary liquid membrane at parapets
- 0931 Tiling: porcelain floor tile, ceramic wall tile
- 0981 Painting: low-VOC interior latex, exterior acrylic
```

Ask: **"Does this mapping look correct? Any items to add or reassign?"**

### Step 2: Generate outline specifications

For each worksection, write a three-part outline spec following NATSPEC convention.

**Labels and numbering:**
- Parts use bold label with no decimal: **1 General**, **2 Products**, **3 Execution**
- Clauses within a part use decimal numbering: `1.1`, `1.2`, `1.3` … `2.1` … `3.1` …
- Sub-clauses use a second decimal: `1.1.1`, `1.1.2`

#### 1 General

- **1.1 Cross-references** — Other worksections this worksection depends on or hands off to.
- **1.2 Standards** — Applicable **AS / AS/NZS** standards with year (e.g. "to AS 3958.1–2007"). Cite the **NCC clause** that drives the standard where relevant.
- **1.3 Interpretation** — Abbreviations, definitions specific to this worksection.
- **1.4 Submissions** — Product data, samples, shop drawings, test reports, warranties, certificates, compliance documentation (e.g. CodeMark, WERS, BAL assessment), and sustainability documentation (Green Star / NABERS / EPDs) as applicable.
- **1.5 Inspections** — Hold points, witness points, inspection notifications required before proceeding.
- **1.6 Quality** — Installer qualifications (e.g. Master Builders Association member, MPAQ-certified, licensed trades), mock-ups where relevant (exposed finishes, masonry, curtain walling, architectural joinery), and compliance certification requirements.
- **1.7 Warranties** — Manufacturer and installer warranty periods. Use industry-standard minimums if not specified by the user.

#### 2 Products

- **2.1 Manufacturers** — Minimum of three acceptable Australian / WA-available manufacturers where possible, with "or approved equivalent" language. Prefer Australian manufacturers where the standard is Australian-specific (e.g. Colorbond steel, Austral Bricks). Include international brands where they dominate the local market (e.g. Laminex, Dulux).
- **2.2 Materials** — Material composition, grade, class, or type. Reference applicable AS / AS/NZS standards.
- **2.3 Performance** — Fire performance (Group number per AS 5637.1, FRL per AS 1530.4), slip resistance (R-rating per AS 4586 for floor tiles / coatings), acoustic performance (STC / Rw / Ln,w per AS/ISO 717), thermal performance (R-value per AS/NZS 4859.1 for insulation, U-value / SHGC for glazing), BAL-rating per AS 3959 where relevant, and other measurable criteria.
- **2.4 Finishes** — Colour, texture, pattern, sheen, or surface treatment. Use "colour to be selected by the Architect from the manufacturer's standard range" unless the user specifies.
- **2.5 Components** — Ancillary items required for a complete installation (fixings, adhesives, grouts, sealants, trims).

#### 3 Execution

- **3.1 Preparation** — Substrate conditions to verify before installation. Moisture content, levelness tolerances, primer requirements.
- **3.2 Installation** — Method of installation per manufacturer's written instructions and the referenced AS / AS-NZS standard. Include key installation requirements specific to the product.
- **3.3 Tolerances** — Dimensional and finish tolerances — typically referenced to the relevant AS Handbook or manufacturer's published tolerances.
- **3.4 Completion** — Field testing, inspection, defects rectification, protection until Practical Completion.
- **3.5 Cleaning** — Post-installation cleaning and handover condition.

### Step 3: Add spec notes

Include these where relevant:

- **Substitutions** — "Requests for substitution of specified products shall be submitted to the Superintendent in writing a minimum of 10 working days before tender close. Include product data, samples, and a clause-by-clause comparison demonstrating equivalent performance."
- **Mock-ups** — For exposed finishes (face brickwork, architectural joinery, tiling, curtain walling, off-form concrete), require a mock-up panel of specified size for Architect approval before proceeding with production work.
- **BAL-rated construction** — Where the site is in a Bushfire Prone Area, flag BAL-specific product requirements per AS 3959.
- **Heritage** — Where the site is Heritage-listed or within a Heritage Area, flag the relevant conservation approach (e.g. "Match existing — refer to the Conservation Management Plan").
- **[REVIEW REQUIRED]** flag — Append to a worksection where:
  - No specific product or manufacturer was provided by the user
  - The worksection is life-safety-related (fire-stopping, fire-rated assemblies, glazing, balustrades, wet-area waterproofing)
  - Performance criteria are assumed rather than confirmed

  > **[REVIEW REQUIRED]** This worksection contains generic outline specifications. A senior specifier or the relevant sub-consultant (structural engineer, fire engineer, building surveyor, accessibility consultant) shall review and supplement with project-specific requirements, confirmed performance data, and final NATSPEC worksection numbers prior to issue for tender.

### Step 4: Write output

Compile all worksections into a single `.md` file organised by worksection number ascending.

**Default output path**: `./outline-specs-[project-slug].md`

- Derive `[project-slug]` from the project name or type provided by the user (lowercase, hyphenated — e.g. `outline-specs-swanbourne-street-fit-out.md`)
- If no project name is given, use `outline-specs-draft.md`
- Ask the user if they want a different path

**File structure:**

```markdown
# Outline Specifications — [Project Name]

**Generated:** [DD Month YYYY]
**Project type:** [type]
**Jurisdiction:** Western Australia (default)
**Specification system:** NATSPEC Building [or NATSPEC Domestic / CSI MasterFormat 2020]
**Worksections:** [count]

> **Disclaimer:** This is an AI-generated outline specification for preliminary design and budget purposes only. It is not a contract document. A registered architect and suitably qualified specifier must review, verify, and supplement this outline prior to issue for tender or construction. All AS / AS/NZS references and NCC clauses must be verified against current editions.

---

## 0731 Membranes

### 1 General

**1.1 Cross-references**
…

**1.2 Standards**
…

### 2 Products

**2.1 Manufacturers**
…

### 3 Execution

**3.1 Preparation**
…

---

## 0931 Tiling
…
```

### Step 5: Summary

After writing the file, report:

```
Specifications written: X worksections
Output: [file path]
Worksections flagged for review: [count]
- [list flagged worksections]
```

## Writing Style

- Use specification language throughout: "shall", "provide", "verify", "submit", "comply with", "to" (e.g. "to AS 3600")
- Write in imperative mood, third person
- No contractions
- No first person ("we", "our")
- Capitalise **Architect**, **Superintendent**, **Principal**, **Contractor**, **Subcontractor**, **Installer** when referring to project roles
- Reference standards by full designation on first use (e.g. "AS/NZS 1170.0:2002, *Structural design actions — General principles*"), abbreviated thereafter
- Use "approved equivalent" rather than "or equal"
- **Measurements in metric (mm, m, m², m³, kg, kPa, °C)**
- **Australian English spelling throughout** (colour, centre, organise, metre, storey, kilometre, grey, aluminium)
- Use **NCC 2022** as the governing code reference; cite specific Parts / Clauses (e.g. "NCC 2022 Vol. 1 Part F4")

## Edge Cases

- **Single material input**: Generate one worksection. Still include the full three-part structure.
- **Ambiguous materials**: Ask the user to clarify. Example: "tile" could be ceramic wall tile (0931), porcelain floor tile (0931), or ceiling tile (0951 Suspended ceilings — as a lay-in mineral fibre panel).
- **Materials outside the listed worksections**: Note the limitation and provide the best-fit worksection. Example: "Lift cab finishes are typically specified within the 1411 Lifts worksection under a specialist subcontractor package; coordinate with the lift vendor's own documentation."
- **Duplicate materials**: Consolidate under one worksection. Do not create separate worksections for "interior paint — walls" and "interior paint — ceilings" — combine under `0981 Painting` with both applications noted.
- **Very long lists (20+ materials)**: Process all of them. Give a progress update after every 5 worksections written.
- **Overseas project**: Switch to CSI mode. Confirm the switch with the user. Use 6-digit MasterFormat numbers, imperial units if US-client (or confirm), ASTM / ANSI standards instead of AS / AS-NZS.
- **Mixed jurisdictions** (rare — e.g. an Australian practice producing specs for a Singaporean project that references BCA Singapore): ask the user which reference system to use; do not mix within a single document.
