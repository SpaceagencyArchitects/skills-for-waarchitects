# Due Diligence

A Claude Code plugin for property due diligence in Western Australia. One comprehensive skill covers every major WA data source — title, heritage, DA history, environmental constraints — in a single guided workflow.

**Jurisdiction: Western Australia.**

## The Problem

Due diligence on a WA site means checking 8+ different data sources — Landgate for title, inHerit for heritage, the LGA DA register for approval history, SAT for appeals, DWER for contamination, DFES for bushfire, AHIS for Aboriginal heritage, NatureMap for flora/fauna. Each has its own portal, its own search interface, its own interpretation rules. A thorough pre-purchase or pre-design check takes half a day and is easy to do incompletely.

## The Solution

**`/wa-property-report`** — one guided workflow that covers every major WA data source, captures findings in a consistent format, applies WA-specific interpretation (what a Memorial means, what an LHS Management Category A implies, what an AHIS Registered Site requires), and produces a single structured report with a risk summary, specialist consultant recommendations, and sources.

The WA due diligence problem is specific to WA data realities — most WA data sources don't have clean APIs (Landgate certified searches are paid; LGA DA registers vary; AHIS is legal-advice-dependent). The skill is honest about this: it's a guided workflow + structured output, not a database lookup.

```
Site address + LGA + Lot/DP
       │
       ├──→ Cadastre & Title (Landgate)
       │    ├─ free map search
       │    └─ paid certified CoT
       ├──→ Heritage (inHerit + LHS)
       │    ├─ State Register
       │    ├─ Local Heritage Survey
       │    └─ Heritage Areas
       ├──→ DA History (LGA register + SAT)
       │    ├─ active / recent DAs
       │    ├─ refusals (why?)
       │    └─ SAT appeals + precedent
       └──→ Environmental (8 sub-checks)
            ├─ Contamination (DWER)
            ├─ Bushfire (DFES)
            ├─ Coastal (SPP 2.6 / CHRMAP)
            ├─ Flood (LGA)
            ├─ Aboriginal heritage (AHIS)
            ├─ Flora/fauna/TECs (NatureMap)
            ├─ Acid sulfate soils (DWER)
            └─ Services (visible / DBYD)
       │
       └──→ /wa-property-report
            · traffic-light risk summary
            · specialist consultant list
            · structured markdown report
```

## Skills

| Skill | Description |
|---|---|
| [wa-property-report](skills/wa-property-report/) | Comprehensive due-diligence across cadastre, title, heritage, DA history, SAT, contamination, bushfire, coastal, flood, Aboriginal heritage, flora/fauna, ASS, services |

## Data Sources

| Source | Access | What it provides |
|---|---|---|
| [Landgate Map Viewer Plus](https://maps.landgate.wa.gov.au/maps-landgate/registered/) | Free | Lot, DP/SSP/SP, site area, zoning overlay |
| [WA Online Land Transactions](https://www0.landgate.wa.gov.au/) | Paid (~$29/search) | Certified Certificate of Title + encumbrances + Memorials |
| [inHerit](https://inherit.dplh.wa.gov.au/) | Free | State Register + Local Heritage Surveys |
| LGA DA register | Free (varies) | Development application history |
| [SAT](https://www.sat.justice.wa.gov.au/) | Free | Appeal decisions |
| [DWER Contaminated Sites Database](https://www.wa.gov.au/service/environment/environment-information-services/contaminated-sites-database) | Free | Contamination classifications |
| [DFES Bush Fire Prone Areas map](https://www.dfes.wa.gov.au/regulations/planning-and-development/bushfire-prone-areas) | Free | BPA designation |
| [AHIS](https://www.wa.gov.au/organisation/department-of-planning-lands-and-heritage/aboriginal-heritage-inquiry-system-ahis) | Free | Aboriginal heritage sites |
| [NatureMap (DBCA)](https://naturemap.dbca.wa.gov.au/) | Free | Flora, fauna, TECs |

## Install

**Claude Code (interactive):**

1. Start Claude Code by running `claude` in a terminal
2. Inside Claude Code, run `/plugin marketplace add SpaceagencyArchitects/skills-for-architects`
3. Run `/plugin` and go to the **Discover** tab to browse and install the **Due Diligence** plugin
4. Run `/reload-plugins` to activate

**Claude Code (terminal):**

```bash
claude plugin marketplace add SpaceagencyArchitects/skills-for-architects
claude plugin install 00-due-diligence@skills-for-architects
```

**Manual:**

```bash
git clone https://github.com/SpaceagencyArchitects/skills-for-architects.git
ln -s $(pwd)/skills-for-architects/plugins/00-due-diligence/skills/wa-property-report ~/.claude/skills/wa-property-report
```

## Related

- [`/planning-analysis-wa`](../02-zoning-analysis/skills/planning-analysis-wa/) — planning envelope analysis. Run DD first, then envelope, for a full pre-design workup.

## License

MIT — fork of [AlpacaLabsLLC/skills-for-architects](https://github.com/AlpacaLabsLLC/skills-for-architects), amended for Western Australian practice.
