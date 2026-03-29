# GENRECORD

**GENealogical RECORDs** — An Open Standard for Sharing Genealogical Source Records

Version 1.0 — Working Draft — 29 March 2026

---

## What Is GENRECORD?

GENRECORD is an open, plain-text file format for packaging and sharing genealogical source records — cemetery transcriptions, marriage indexes, obituary abstracts, census extracts, courthouse records — between societies, archives, and software.

A single `.genrecord` file carries both the metadata (what the collection is, where it came from, who created it, what license covers it) and the actual record data.

## What Problem Does It Solve?

Genealogical societies create transcribed record sets, but there is no standard way to share them. Data gets trapped in proprietary platforms, static HTML pages, or random spreadsheets on a volunteer's laptop. GENRECORD gives that data a portable, self-describing container that any software can read and any human can open in a text editor.

## What GENRECORD Is Not

- **Not GEDCOM.** GEDCOM is for family trees (individuals, families, relationships). GENRECORD is for source records (transcriptions, indexes, abstracts). They are complementary, not competing.
- **Not a digitized document format.** GENRECORD carries structured index data — names, dates, locations, fields — not scanned images of original documents.
- **Not a database.** It is an exchange format. Flat tabular data with a metadata header. No queries, no relationships between records, no nesting.
- **Not proprietary.** CC0 Public Domain. No owner, no gatekeeper, no license fees.

## Quick Example

```
##GENRECORD 1.0
#version: 1.0
#type: CEM
#title: Bexar County Cemetery Transcriptions
#society: San Antonio Genealogical and Historical Society
#society_url: https://txsaghs.org
#created: 2026-03-29
#license: CC0
#coverage.place: Bexar County, Texas, USA
#coverage.dates: 1850-1975
#contact: education@txsaghs.org
---
record_id,surname,given,gender,birth_date,death_date,cemetery,cemetery_place,section,lot,condition,notes
1,Stricklin,Walter Davis,M,20 Apr 1892,14 Mar 1962,City Cemetery,San Antonio TX,A,14,Legible,
2,,,,,1901,City Cemetery,San Antonio TX,,,Illegible,Stone heavily weathered
3,Moreno,Luis Jimenez,M,1878,1931,San Fernando Cemetery,San Antonio TX,B,22,Partial,
```

## File Structure

A `.genrecord` file is plain UTF-8 text with two sections separated by `---`:

1. **Header block** — metadata as `#key: value` pairs
2. **Data block** — CSV with a column definition row followed by data rows

## Record Types

GENRECORD 1.0 defines 35 record types across 7 categories:

| Category | Types |
|----------|-------|
| Vital | BIRTH, DEATH, BURI, CEM, MARR, DIV |
| Legal | CENSUS, NAT, PROB, LAND, COURT, PRISON, TAX, VOTE, IMMIG, EMIG |
| Military | MIL |
| Religious | BAPT, CONF, CONGR, CLERGY, PROXY, EXCOM, BARMIT, INITIAT |
| Education | SCHOOL, DEGREE, CERT, TEACH |
| Newspaper | OBIT, MARANN, BIRTHANN |
| Compiled | BIO, BIBLE, FUNERAL |

## Specification

The full specification is available in [`specification.md`](specification.md).

## Sample Files

The [`samples/`](samples/) directory contains example `.genrecord` files for common record types.

## Software Support

- [**SocietyPress**](https://getsocietypress.org) — Free, open-source WordPress platform for genealogical societies. Import and export `.genrecord` files natively.

## License

This specification is released under the [CC0 1.0 Universal Public Domain Dedication](https://creativecommons.org/publicdomain/zero/1.0/). No rights reserved. Free for any use by anyone for any purpose.
