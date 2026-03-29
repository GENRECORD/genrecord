# GENRECORD

**GENealogical RECORDs** — An open standard for sharing genealogical source records between societies, archives, and software.

## What Is GENRECORD?

GENRECORD is a plain-text file format for packaging and sharing genealogical source records — cemetery transcriptions, marriage indexes, obituary abstracts, census extracts, courthouse records. A single `.genrecord` file carries both the metadata (what the collection is, where it came from, who created it, what license covers it) and the actual record data.

It is **not GEDCOM**. GEDCOM is for family trees. GENRECORD is for source records. They are complementary.

## Quick Example

```
##GENRECORD 1.0
#version: 1.0
#type: CEM
#title: Bexar County Cemetery Transcriptions
#society: San Antonio Genealogical and Historical Society
#created: 2026-03-29
#license: CC0
#coverage.place: Bexar County, Texas, USA
#coverage.dates: 1850-1975
---
record_id,surname,given,gender,birth_date,death_date,cemetery,cemetery_place,section,lot,condition,notes
1,Stricklin,Walter Davis,M,20 Apr 1892,14 Mar 1962,City Cemetery,San Antonio TX,A,14,Legible,
2,,,,,1901,City Cemetery,San Antonio TX,,,Illegible,Stone heavily weathered
```

## Repository Structure

```
genrecord/
├── README.md               — This file
├── SPEC.md                 — Full specification
├── CHANGELOG.md            — Version history
├── LICENSE                 — CC0 Public Domain
├── schemas/                — Field definitions by record type
│   ├── BIRTH.md
│   ├── DEATH.md
│   └── ...
├── examples/               — Sample .genrecord files
│   ├── sample_BIRTH.genrecord
│   ├── sample_CEM.genrecord
│   └── ...
└── tools/                  — Community-contributed parsers and validators
```

## Record Types

GENRECORD 1.0 defines 35 record types across seven categories:

| Category | Types |
|---|---|
| Vital | BIRTH, DEATH, BURI, CEM, MARR, DIV |
| Legal | CENSUS, NAT, PROB, LAND, COURT, PRISON, TAX, VOTE, IMMIG, EMIG |
| Military | MIL |
| Religious | BAPT, CONF, CONGR, CLERGY, PROXY, EXCOM, BARMIT, INITIAT |
| Education | SCHOOL, DEGREE, CERT, TEACH |
| Newspaper | OBIT, MARANN, BIRTHANN |
| Compiled | BIO, BIBLE, FUNERAL |

## License

CC0 1.0 Universal Public Domain Dedication. No rights reserved. Use for any purpose without permission.

## Contributing

GENRECORD grows through community input. To contribute:

- **Open an issue** to report errors, propose new fields, or suggest new record types
- **Submit a pull request** to contribute a parser, validator, or sample file
- **Faith tradition experts** — BARMIT and INITIAT are stubs awaiting your input
- **Archivists and librarians** — tell us where GENRECORD fits with EAD, Dublin Core, and OAI-PMH

## Project Home

[genrecord.org](https://genrecord.org)
