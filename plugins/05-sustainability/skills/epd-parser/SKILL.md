---
name: epd-parser
description: Parse EPD (Environmental Product Declaration) PDF documents to extract structured environmental impact data — GWP, life cycle stages, certifications, and compliance metrics. Handles AU (EPD Australasia), EU (EN 15804+A2), and US (UL/NSF/SCS) EPD formats.
allowed-tools:
  - Read
  - Write
  - Edit
  - Bash
  - Glob
  - Grep
  - AskUserQuestion
  - mcp__google-sheets__get_sheet_data
  - mcp__google-sheets__update_cells
  - mcp__google-sheets__list_sheets
---

# /epd-parser — EPD PDF Parser

Extract structured environmental impact data from EPD (Environmental Product Declaration) PDF files. Uses PyMuPDF for text extraction and Claude's reasoning to parse varying EPD formats into a standardised 42-column schema.

EPDs follow ISO 14025 / ISO 21930 / EN 15804 and report life cycle environmental impacts of building products. This skill reads those PDFs and structures the data for comparison, specification, and rating-tool documentation (Green Star Responsible Products, NABERS Embodied Emissions, LEED MRc2).

**Default jurisdiction: Western Australia.** EPDs published under EPD Australasia follow EN 15804+A2 and parse cleanly into the standard schema. International EPDs (EU, US, etc.) parse equally well — the schema is jurisdiction-neutral.

Australian English spelling and metric units (m², m³, kg) used throughout output.

## Input

The user provides EPD PDFs in one of these ways:

1. **File paths** — one or more PDF file paths
2. **Folder path** — a directory containing PDFs (will process all `.pdf` files)
3. **Just invoked** — ask the user for file paths or a folder

Also ask (or use defaults):

- **Output destination** — Google Sheet, local CSV, or markdown (default: ask)

## Output Schema

EPD data uses a **42-column schema** — separate from the 33-column FF&E product schema. When writing to CSV, use the same column order.

### Product Identity (columns A–H)

| Col | Field | Description | Format |
|---|---|---|---|
| A | EPD Link | URL to original EPD document | `=HYPERLINK(url, "EPD")` or blank for local PDFs |
| B | Manufacturer | Company that makes the product | Title Case |
| C | Product Name | Declared product or product group | Title Case |
| D | Description | Brief product description | Sentence case |
| E | Declared Unit | Functional / declared unit (e.g., "1 m²", "1 kg", "1 m³") | As stated in EPD |
| F | Functional Unit | Functional unit with RSL if different from declared | As stated, blank if same |
| G | NATSPEC / CSI | NATSPEC worksection (4-digit) or CSI MasterFormat division | `0341`, `0511`, `0711`; CSI fallback `03 30 00`, `05 12 00` |
| H | Material Category | Normalised material type | See vocabulary below |

### EPD Metadata (columns I–P)

| Col | Field | Description | Format |
|---|---|---|---|
| I | EPD Registration No. | Unique EPD identifier | As published |
| J | Programme Operator | Certifying body | `EPD Australasia`, `Environdec`, `IBU`, `UL`, `NSF`, `SCS`, etc. |
| K | PCR Reference | Product Category Rule reference | Full citation |
| L | PCR Expiry | PCR expiration date | YYYY-MM-DD |
| M | Standard | Governing standard | `ISO 14025`, `ISO 21930`, `EN 15804+A2`, etc. |
| N | System Boundary | Scope of LCA | `Cradle-to-gate`, `Cradle-to-grave`, `Cradle-to-gate with options` |
| O | Valid From | EPD publication date | YYYY-MM-DD |
| P | Valid To | EPD expiration date | YYYY-MM-DD |

### Impact Indicators — Product Stage A1–A3 (columns Q–V)

| Col | Field | Description | Unit |
|---|---|---|---|
| Q | GWP-total (A1–A3) | Global Warming Potential, total | kg CO₂e |
| R | GWP-fossil (A1–A3) | GWP from fossil sources | kg CO₂e |
| S | GWP-biogenic (A1–A3) | GWP from biogenic sources | kg CO₂e |
| T | ODP (A1–A3) | Ozone Depletion Potential | kg CFC-11e |
| U | AP (A1–A3) | Acidification Potential | kg SO₂e (or mol H+ eq for +A2) |
| V | EP (A1–A3) | Eutrophication Potential | kg PO₄e (or mol N eq / kg P eq for +A2) |

### Impact Indicators — Other Stages (columns W–AB)

| Col | Field | Description | Unit |
|---|---|---|---|
| W | GWP (A4–A5) | Construction stage GWP | kg CO₂e |
| X | GWP (B1–B7) | Use stage GWP | kg CO₂e |
| Y | GWP (C1–C4) | End-of-life GWP | kg CO₂e |
| Z | GWP (D) | Beyond system boundary GWP | kg CO₂e |
| AA | GWP-total (all stages) | Sum of all declared stages | kg CO₂e |
| AB | POCP (A1–A3) | Photochemical Ozone Creation Potential | kg C₂H₄e (or kg NMVOC eq for +A2) |

### Resource Use (columns AC–AH)

| Col | Field | Description | Unit |
|---|---|---|---|
| AC | PERE (A1–A3) | Primary Energy, Renewable, energy use | MJ |
| AD | PENRE (A1–A3) | Primary Energy, Non-Renewable, energy use | MJ |
| AE | Total Energy (A1–A3) | PERE + PENRE | MJ |
| AF | FW (A1–A3) | Fresh Water Use | m³ |
| AG | Recycled Content | Percentage of recycled content | % |
| AH | Waste (A1–A3) | Total waste generated | kg |

### Tracking (columns AI–AP)

| Col | Field | Description | Format |
|---|---|---|---|
| AI | Rating Tool Eligibility | Rating tool flags | `Green Star Tier B; NABERS Embodied; LEED MRc2 — Yes` |
| AJ | EC3 ID | Building Transparency EC3 identifier | As listed, blank if unknown |
| AK | Plant / Facility | Manufacturing plant or facility name | As stated |
| AL | Country | Manufacturing country | ISO 3166-1 alpha-2 (`AU`, `NZ`, `DE`, `US`) |
| AM | Parsed At | Timestamp of parsing | ISO 8601 |
| AN | Tags | User-assigned tags | Comma-separated |
| AO | Notes | Additional context | Free text — see below |
| AP | Source | Which skill created this row | `epd-parser` |

### Material Category Vocabulary

Use ONE normalised term: Concrete, Cement, Aggregate, Steel, Reinforcement, Aluminium, Glass, Wood / Timber, CLT, Glulam, LVL, Plywood, Insulation, Plasterboard, Tile / Ceramic, Stone, Carpet, Resilient Flooring, Roofing Membrane, Cladding, Sealant, Paint / Coating, Masonry, Composite Panel, Acoustic, Furniture, Other.

(Note Australian English: **Aluminium** not Aluminum.)

### EPD-specific data in Notes (col AO)

EPDs contain fields that don't have dedicated columns. Append these to Notes:

- **EPD Type**: `Type: Product-specific` or `Type: Industry-average` or `Type: Sector`
- **Verification**: `Verified by: [verifier name]`
- **EN version**: `EN 15804+A1` or `EN 15804+A2` (important for comparability)
- **Source File**: `Source: boral-envisia-epd.pdf`
- **LCA Software**: `LCA: GaBi` or `LCA: SimaPro` or `LCA: openLCA`
- **Biogenic carbon note**: If the EPD reports biogenic carbon storage separately
- **Climate Active certified**: `Climate Active: Yes — Cert No. CACPxxx` (Australian federal carbon-neutral scheme)
- **Transport context**: `Plant-to-Perth distance: ~XX km` if relevant for WA projects

Example Notes cell:
`Type: Product-specific | Verified by: thinkstep-anz | EN 15804+A2 | LCA: GaBi | Source: boral-envisia-epd.pdf | Plant-to-Perth: 10 km`

### Rating Tool Eligibility Logic (col AI)

The single field combines flags for the major rating tools. Format as semicolon-separated entries:

- **Green Star (GBCA)** — `Green Star Tier C` (Recognised), `Green Star Tier B` (Best Practice), `Green Star Tier A` (Leadership). Determined by EPD type, verification, programme operator, and (for Tier A/B) demonstrated performance against benchmark.
- **NABERS Embodied Emissions** — `NABERS Embodied: Compliant` (or omit). The NABERS Embodied Emissions module accepts EPDs from any ISO 14025 / EN 15804 compliant programme operator.
- **LEED MRc2 (international)** — `LEED MRc2: Yes` (product-specific, third-party verified, ISO 14025 conforming) / `LEED MRc2: Partial` (industry-average / sector EPD) / `LEED MRc2: No` (self-declared, unverified).

Example values:
- `Green Star Tier B; NABERS Embodied: Compliant; LEED MRc2: Yes`
- `Green Star Tier C; LEED MRc2: Partial`
- `LEED MRc2: Yes` (international project, no Green Star reference)

## Workflow

### Step 1: Get input

Parse the user's input to identify PDF file(s) and output preferences.

- If given a folder, list all `.pdf` files and report count
- If no PDFs found or path is invalid, ask the user
- Report: "Found N EPD PDF(s) to process."

### Step 2: Extract text from PDF

Use PyMuPDF (fitz) to extract text from each PDF. Run this Python script via Bash:

```python
import fitz
import sys
import json

pdf_path = sys.argv[1]
doc = fitz.open(pdf_path)
pages = []
for i, page in enumerate(doc):
    text = page.get_text()
    pages.append({"page": i + 1, "text": text})
doc.close()

print(json.dumps({"filename": pdf_path.split("/")[-1], "total_pages": len(pages), "pages": pages}))
```

For each PDF, extract all pages and save the JSON output.

### Step 3: Parse EPD data

Read the extracted text and identify all environmental impact data. This is the core intelligence step.

**For small EPDs (≤30 pages):** Process all pages at once.

**For large EPDs (>30 pages):** Process in chunks of 15 pages. Carry forward context between chunks.

**Parsing instructions:**

1. **Identify the EPD structure** — EPDs typically have these sections:
   - General information (pages 1–2): manufacturer, product, declared unit, programme operator, validity
   - Product description (pages 2–3): materials, manufacturing process, application
   - LCA information: system boundary, data sources, allocation rules
   - Impact indicator tables: the core data — usually 1–3 pages of tables
   - Resource use and waste tables
   - Additional information: scenarios, biogenic carbon, recycled content

2. **Extract product identity first** — manufacturer, product name, declared unit, functional unit. These are always on page 1–2.

3. **Extract EPD metadata** — registration number, programme operator, PCR, standard, dates, system boundary. Usually on page 1 or in a header / sidebar.

4. **Find and parse impact indicator tables** — the most critical step:
   - Look for tables with life cycle stage columns (A1, A2, A3 or A1–A3 combined, A4, A5, B1–B7, C1–C4, D)
   - Impact categories are rows: GWP, ODP, AP, EP, POCP, ADP (or ADPE / ADPF)
   - **EN 15804+A2 EPDs** split GWP into: GWP-total, GWP-fossil, GWP-biogenic, GWP-luluc. Capture all that exist.
   - **EN 15804+A1 EPDs** report a single GWP row. Put this value in GWP-total (A1–A3).
   - If A1, A2, A3 are reported separately (not combined), sum them for the A1–A3 total.
   - Units are standardised: GWP = kg CO₂e, ODP = kg CFC-11e, AP = kg SO₂e (or mol H+ eq for +A2), EP = kg PO₄e (or mol N eq / kg P eq for +A2), POCP = kg C₂H₄e (or kg NMVOC eq for +A2).

5. **Extract resource use** — PERE, PENRE, fresh water, waste. Usually in a separate table following the impact indicators.

6. **Extract additional data** — recycled content %, manufacturing plant, country of origin, LCA software, verifier name.

7. **Determine rating-tool eligibility** — based on EPD type, verification status, programme operator. See Rating Tool Eligibility Logic above. For AU projects, prioritise Green Star and NABERS flags. For international projects, use LEED MRc2.

8. **Leave fields blank rather than guessing** — if a field isn't in the EPD, leave it empty.

### Multi-product EPDs

Some EPDs declare impacts for multiple products, product groups, or concrete mix designs. Create **one row per product / variant**:

- If the EPD covers "Product A" and "Product B" with separate impact tables, create two rows
- If the EPD covers multiple concrete mixes (e.g., 25 MPa, 32 MPa, 40 MPa), create one row per mix
- The product name should distinguish variants: "ENVISIA — 32 MPa"

### Step 4: Present results

Show a summary table for each parsed EPD:

```
## EPD Parse Results

### boral-envisia-epd.pdf
| Field | Value |
|---|---|
| Product | ENVISIA — 32 MPa |
| Manufacturer | Boral |
| Plant | Welshpool, WA |
| Declared Unit | 1 m³ |
| GWP (A1–A3) | 218 kg CO₂e |
| System Boundary | Cradle-to-gate |
| Programme Operator | EPD Australasia |
| Standard | EN 15804+A2 |
| Valid | 2024-08-15 to 2029-08-15 |
| Rating Eligibility | Green Star Tier B; NABERS Embodied: Compliant |

Products extracted: 3 (25 MPa, 32 MPa, 40 MPa)
```

Ask: **"Does this look correct? Should I adjust anything before saving?"**

### Step 5: Write output

Ask the user (if not already specified): **"Where should I save this?"**

Options:
- **EPD Google Sheet** — append rows to a dedicated EPD spreadsheet (separate from the FF&E product library). Ask for spreadsheet ID if not already known.
- **Local CSV** — save to a specified path (default: `./epd-data-YYYY-MM-DD.csv`)
- **Just the table** — leave as markdown in the conversation

## CSV Format

When saving to CSV, use the 42-column header:

```csv
EPD Link,Manufacturer,Product Name,Description,Declared Unit,Functional Unit,NATSPEC / CSI,Material Category,EPD Registration No.,Programme Operator,PCR Reference,PCR Expiry,Standard,System Boundary,Valid From,Valid To,GWP-total (A1-A3),GWP-fossil (A1-A3),GWP-biogenic (A1-A3),ODP (A1-A3),AP (A1-A3),EP (A1-A3),GWP (A4-A5),GWP (B1-B7),GWP (C1-C4),GWP (D),GWP-total (all stages),POCP (A1-A3),PERE (A1-A3),PENRE (A1-A3),Total Energy (A1-A3),FW (A1-A3),Recycled Content,Waste (A1-A3),Rating Tool Eligibility,EC3 ID,Plant/Facility,Country,Parsed At,Tags,Notes,Source
```

**Backwards compatibility note:** Earlier versions of this schema used `LEED Eligible` (single-tool flag) for column AI. Existing sheets with that header continue to work — Claude will write Green Star / NABERS / LEED data into the same column whatever the header says. New sheets should use the broader `Rating Tool Eligibility` column name.

## Edge Cases

- **Scanned PDFs (image-only)**: PyMuPDF will return empty or garbage text. Detect this (very short text relative to page count) and tell the user: "This PDF appears to be scanned / image-based. Text extraction won't work — consider using an OCR tool first."
- **Non-English EPDs**: Common for European manufacturers (German, French, Spanish, Swedish). Impact indicator abbreviations (GWP, ODP, AP, EP, POCP) are the same internationally. Extract numeric data regardless; note the language in Notes.
- **EN 15804+A1 vs +A2**: Older EPDs use +A1 (single GWP row, different EP / AP units). Newer (and EPD Australasia preferred) use +A2 (split GWP, different units for AP / EP / POCP). Always note which version in Notes. Map to schema as closely as possible.
- **Multi-product EPDs**: One row per product / variant. See Multi-product EPDs section above.
- **Expired EPDs**: Flag in Notes: `EXPIRED — valid to YYYY-MM-DD`. Still parse the data.
- **Password-protected PDFs**: PyMuPDF will fail to open. Catch the error and tell the user.
- **Very large PDFs (50+ pages)**: Process in 15-page chunks. Give progress updates.
- **EPDs with impact tables as images**: Detect missing numeric data in what should be table sections. Flag: "Impact tables may be embedded as images — manual extraction needed."
- **AU EPDs with NZ content**: EPD Australasia covers both AU and NZ. Note country of origin (`AU` or `NZ`) accurately — for WA projects, NZ-sourced products carry significant transport (A4) emissions.

## Error Reporting

After processing, always report:

```
Parsed: X products from Y EPD PDF(s)
- filename.pdf: N products extracted
- filename2.pdf: M products extracted
Issues: [list any problems — expired, scanned, missing tables, etc.]
```
