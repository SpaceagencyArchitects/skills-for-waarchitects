# Specifications

A Claude Code plugin for construction documentation. Generates **NATSPEC** outline specifications from a materials list — organised by worksection with three-part structure, AS / AS-NZS standards, and NCC 2022 references.

**Default jurisdiction: Western Australia.** Uses **NATSPEC Building** (commercial) or **NATSPEC Domestic** (residential) by default. Falls back to CSI MasterFormat 2020 for overseas / US-client projects.

## The Problem

Specification writing is tedious and repetitive — mapping materials to NATSPEC worksections, writing three-part specs with the right language, referencing the correct AS / AS-NZS standards and NCC clauses. Architects in WA spend hours producing outline specs that follow the same structural patterns every time, and getting the references wrong (wrong AS edition, wrong NCC clause, wrong BAL approach) can cost real money downstream.

## The Solution

A skill that knows NATSPEC worksection conventions and produces properly formatted outline specs for WA practice. Give it a materials list — pasted, from a file, or described verbally — and it maps each material to the correct worksection, writes three-part worksections (**1 General / 2 Products / 3 Execution**), cites the right AS / AS-NZS standards and NCC 2022 clauses, and flags items that need professional review.

```
┌──────────────────────────────────┐
│         DESIGNER INPUT           │
│                                  │
│  Materials list:                 │
│  "porcelain tile,                │
│   plasterboard linings,          │
│   Colorbond roofing,             │
│   aluminium windows..."          │
└─────────────┬────────────────────┘
              │
              ▼
   ┌───────────────────┐
   │   Spec Writer     │
   │                   │
   │  Materials →      │
   │  NATSPEC          │
   │  worksections →   │
   │  3-part specs     │
   │                   │
   │  Worksections:    │
   │  0111–2711        │
   │  (01 General →    │
   │   27 Comms)       │
   │                   │
   │  1 General        │
   │  2 Products       │
   │  3 Execution      │
   │                   │
   │  AS / AS-NZS      │
   │  NCC 2022         │
   │                   │
   │  [REVIEW REQUIRED]│
   │  flags on generic │
   │  or life-safety   │
   │  worksections     │
   └─────────┬─────────┘
             │
             ▼
   ┌───────────────────┐
   │  Markdown file    │
   │  outline-specs-   │
   │  [project].md     │
   └───────────────────┘
```

## Data Flow

| Step | What happens |
|------|-------------|
| **Parse** | Reads materials list (pasted, file, or verbal) and classifies into NATSPEC worksections |
| **Generate** | Writes 3-part outline worksections — 1 General / 2 Products / 3 Execution — with AS / AS-NZS and NCC 2022 references |
| **Annotate** | Adds `[REVIEW REQUIRED]` flags on generic worksections, life-safety sections, assumed criteria |
| **Export** | Saves markdown file with professional disclaimer |

Covers the full NATSPEC Building worksection range (01xx General through 27xx Communications services), with NATSPEC Domestic prefixes for residential work. Uses proper spec language — "shall", "provide", "comply with", "to", imperative mood, Australian English throughout.

## Skills

| Skill | Description |
|-------|-------------|
| [spec-writer](skills/spec-writer/) | NATSPEC outline specification writer — maps materials to NATSPEC worksections with three-part structure and AS / AS-NZS / NCC 2022 references |

## Australian context

Things the plugin handles for WA practice:

- **NATSPEC worksection numbering** — 4-digit codes (e.g. `0411 Brick and block construction`) with space separator, per `rules/natspec-formatting.md`
- **Domestic vs Building** — `D`-prefixed worksections for Class 1 / single dwelling work
- **AS / AS-NZS with year** — e.g. "to AS 3600–2018, *Concrete structures*"
- **NCC 2022 citations** — Parts / Clauses / Specs where the AS reference is code-driven
- **BAL ratings** — AS 3959 references for Bushfire Prone Areas
- **WA-available manufacturers** — Colorbond, Austral Bricks, CSR Gyprock, Laminex, Dulux etc. named where appropriate
- **Metric throughout** — mm, m, m², kg, kPa, °C
- **Australian English** — colour, centre, organise, aluminium, storey

## Install

**Claude Desktop:**

1. Open the **+** menu → **Add marketplace from GitHub**
2. Enter `SpaceagencyArchitects/skills-for-architects`
3. Install the **Specifications** plugin

**Claude Code (terminal):**

```bash
claude plugin marketplace add SpaceagencyArchitects/skills-for-architects
claude plugin install 04-specifications@skills-for-architects
```

**Manual:**

```bash
git clone https://github.com/SpaceagencyArchitects/skills-for-architects.git
ln -s $(pwd)/skills-for-architects/plugins/04-specifications/skills/spec-writer ~/.claude/skills/spec-writer
```

## License

MIT — fork of [AlpacaLabsLLC/skills-for-architects](https://github.com/AlpacaLabsLLC/skills-for-architects), rewritten for NATSPEC / WA practice.
