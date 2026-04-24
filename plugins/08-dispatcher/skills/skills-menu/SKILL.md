---
name: skills-menu
description: Show all available skills, agents, and how to use them — organized by task.
allowed-tools:
  - Read
user-invocable: true
---

# /skills — What's Available

You display the full menu of available skills and agents, organised by what the user needs to accomplish. This is a read-only help command.

## On Start

Print the following menu. Do not read any files — the menu is static.

## Output

```
# Architect Skills — spaceagency architects (WA)

**40 skills, 7 agents** — type /studio [your task] to get routed, or call any skill directly.
Default jurisdiction: Western Australia. NYC and Uruguay skills retained at the skill level for international work.

## Agents — describe your task, they figure out the rest

| Agent | What it does |
|---|---|
| Site Planner | Full site brief — climate (BoM), transit (Transperth), demographics (ABS), history |
| WA Planning Expert | WA DD + planning envelope — title, heritage, R-Codes / LPS, 3D viewer |
| Workplace Strategist | Space programs — headcount to occupancy (NCC) to room schedules |
| Product & Materials Researcher | Find products from a brief, extract specs from URLs/PDFs, find alternatives |
| FF&E Designer | Build schedules from messy inputs, compose room packages, QA, export |
| Sustainability Specialist | EPD research, GWP comparison, Green Star / NABERS / NCC Section J |
| Brand Manager | Presentations, colour palettes, visual consistency, deliverable QA |

## Skills — call directly for a specific task

### Site & Due Diligence
/environmental-analysis [address] — climate (BoM), bushfire (DFES), coastal, seismic, soil, ASS
/mobility-analysis [address] — Transperth, roads, cycling network, Walk Score, airport
/demographics-analysis [address] — ABS Census, REIWA, employment, age, housing
/history [address] — Traditional Owners, heritage, architectural character, planned devt.
/wa-property-report [address] — WA DD: Landgate, inHerit, LGA DA, SAT, DWER, DFES, AHIS
/nyc-landmarks [address] — legacy NYC LPC landmark check
/nyc-dob-permits [address] — legacy NYC DOB permit history
/nyc-dob-violations [address] — legacy NYC DOB/ECB violations
/nyc-acris [address] — legacy NYC property transactions
/nyc-hpd [address] — legacy NYC HPD violations
/nyc-bsa [address] — legacy NYC BSA variances
/nyc-property-report [address] — legacy combined NYC report

### Zoning & Planning
/planning-analysis-wa [address] — WA planning envelope: MRS / LPS / R-Codes / height / setbacks
/zoning-analysis-nyc [address] — legacy NYC buildable envelope from PLUTO
/zoning-analysis-uruguay — Maldonado, Uruguay lot analysis
/zoning-envelope [report] — interactive 3D envelope viewer (metric or imperial)

### Programming
/occupancy-calculator — occupancy loads, egress, plumbing (NCC migration pending)
/workplace-programmer — space programs from headcount and work style

### Specifications
/spec-writer — outline specs (NATSPEC migration pending)

### Sustainability
/epd-research [material] — search EC3 / UL / Environdec for EPDs
/epd-parser [file] — extract data from an EPD PDF
/epd-compare — side-by-side environmental impact comparison
/epd-to-spec — specs with EPD requirements and GWP thresholds

### Product & Materials Research
/product-research — find products from a design brief
/product-spec-bulk-fetch — extract specs from product URLs
/product-spec-pdf-parser — extract specs from PDF catalogues
/product-data-cleanup — normalise a messy FF&E schedule
/product-enrich — auto-tag products with categories, colours, materials
/product-match — find similar products
/product-pair — suggest complementary products
/product-image-processor — download, resize, remove backgrounds
/product-data-import — import raw product data into master schedule
/master-schedule — connect a product library sheet to the project
/csv-to-sif — convert CSV to dealer format
/sif-to-csv — convert dealer format to CSV

### Presentations
/slide-deck-generator [topic] — HTML slide deck with editorial layout
/color-palette-generator — colour palettes from descriptions or images
/resize-images — batch-resize photos for web, social, slides, and print
```

That's it. Do not add commentary, suggestions, or follow-up questions. Just print the menu.
