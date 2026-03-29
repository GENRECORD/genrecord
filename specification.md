# GENRECORD Specification

**GENealogical RECORDs**
An Open Standard for Sharing Genealogical Source Records

Version 1.0 — Working Draft — 29 March 2026

CC0 1.0 Universal Public Domain Dedication
No rights reserved. This standard is free for any use by anyone for any purpose.

---

## Table of Contents

1. [Preamble](#1-preamble)
2. [Architecture](#2-architecture)
3. [Header Block](#3-header-block)
4. [Date and Place Formats](#4-date-and-place-formats)
5. [Controlled Vocabularies](#5-controlled-vocabularies)
6. [Record ID Convention](#6-record-id-convention)
7. [File Naming Convention](#7-file-naming-convention)
8. [Custom Fields](#8-custom-fields)
9. [Record Type Taxonomy](#9-record-type-taxonomy)
10. [Field Schemas](#10-field-schemas)
11. [Sample Files](#11-sample-files)

---

## 1. Preamble

### 1.1 What is GENRECORD?

GENRECORD is an open, plain-text file format for packaging and sharing genealogical source records — cemetery transcriptions, marriage indexes, obituary abstracts, census extracts, courthouse records — between societies, archives, and software. A single file carries both the metadata (what the collection is, where it came from, who created it, what license covers it) and the actual record data.

### 1.2 What Problem Does It Solve?

Genealogical societies create transcribed record sets, but there is no standard way to share them. Data gets trapped in proprietary platforms, static HTML pages, or random spreadsheets on a volunteer's laptop. GENRECORD gives that data a portable, self-describing container that any software can read and any human can open in a text editor.

### 1.3 What GENRECORD Is Not

- **Not GEDCOM.** GEDCOM is for family trees (individuals, families, relationships). GENRECORD is for source records (transcriptions, indexes, abstracts). They are complementary, not competing.
- **Not a digitized document format.** GENRECORD carries structured index data — names, dates, locations, fields — not scanned images of original documents.
- **Not a database.** It is an exchange format. Flat tabular data with a metadata header. No queries, no relationships between records, no nesting.
- **Not a delivery mechanism.** GENRECORD defines the file format, not how files are transferred, hosted, or discovered. HTTP, FTP, email attachment, USB drive — all out of scope.
- **Not proprietary.** CC0 Public Domain. No owner, no gatekeeper, no license fees.

---

## 2. Architecture

A GENRECORD file is a plain UTF-8 text file with the extension `.genrecord`. It consists of two sections: a header block and a data block, separated by a line containing only three dashes (`---`).

**Structure:**

```
##GENRECORD 1.0
#key: value
#key: value
---
field1,field2,field3
data,data,data
```

The header block contains metadata about the collection as key-value pairs. The data block contains a column definition row followed by data rows in comma-separated values (CSV) format. One file contains exactly one record type.

---

## 3. Header Block

The first line of every GENRECORD file must be `##GENRECORD` followed by the version number. All subsequent header lines begin with `#`. Header keys are lowercase. The header block ends at the `---` separator line.

| Field | Status | Notes |
|-------|--------|-------|
| `##GENRECORD` | Required | Must be first line. Declares file as GENRECORD format. |
| `#version` | Required | GENRECORD version number. Current: `1.0` |
| `#type` | Required | Record type code (e.g. CEM, MARR, BIRTH). One type per file. |
| `#title` | Required | Descriptive title of the collection. |
| `#society` | Recommended | Name of contributing society or organization. |
| `#society_url` | Optional | Website of contributing society. |
| `#created` | Recommended | Date file was created. Format: `YYYY-MM-DD` |
| `#modified` | Optional | Date file was last modified. Format: `YYYY-MM-DD` |
| `#license` | Required | License governing use. Recommended: `CC0` |
| `#coverage.place` | Recommended | Geographic scope (e.g. Bexar County, Texas, USA) |
| `#coverage.dates` | Optional | Date range of records (e.g. 1850-1975) |
| `#language` | Optional | Language of records. Default: `en` |
| `#encoding` | Optional | Character encoding. Default: `UTF-8` |
| `#contact` | Optional | Email or URL for questions about this file. |
| `#record_count` | Optional | Number of data rows in file. |
| `#notes` | Optional | Any additional information about the collection. |

---

## 4. Date and Place Formats

### 4.1 Date Format

Dates should be recorded as they appear in the original source. GENRECORD does not enforce a single date format in order to preserve the original record as transcribed. However, the following conventions are strongly recommended for new data entry:

| Format | Example |
|--------|---------|
| Full dates | `DD Mon YYYY` (e.g. `20 Apr 1892`) |
| Year only | `YYYY` (e.g. `1892`) |
| Approximate dates | prefix with `abt` (e.g. `abt 1892`) |
| Before/after | prefix with `bef` or `aft` (e.g. `bef 1900`) |
| Date ranges | use `/` separator (e.g. `1890/1895`) |
| Unknown | leave field blank |

### 4.2 Place Format

Places should be recorded from most specific to least specific, separated by commas. The recommended order is: City or Township, County, State or Province, Country. Examples:

- `San Antonio, Bexar County, Texas, USA`
- `Wayne County, Tennessee, USA`
- `County Cork, Ireland`

Record places as they existed at the time of the event, not as they exist today. Use the notes field to record current place names if helpful.

---

## 5. Controlled Vocabularies

Certain fields use controlled vocabularies to ensure consistency and interoperability. Producers should use these values wherever possible.

### 5.1 Gender

Field: `gender`

| Value | Meaning |
|-------|---------|
| `M` | Male |
| `F` | Female |

### 5.2 Marital Status

Field: `marital_status`

| Value | Meaning |
|-------|---------|
| `S` | Single |
| `M` | Married |
| `W` | Widowed |
| `D` | Divorced |

### 5.3 Military Branch

Field: `branch` — recommended values (not exhaustive):

| Value | Meaning |
|-------|---------|
| `Army` | United States Army |
| `Navy` | United States Navy |
| `Marines` | United States Marine Corps |
| `Air Force` | United States Air Force |
| `Coast Guard` | United States Coast Guard |
| `National Guard` | State National Guard |
| `Confederate` | Confederate States Army or Navy |
| `Union` | Union Army or Navy (Civil War) |

### 5.4 Cemetery Condition

Field: `condition`

| Value | Meaning |
|-------|---------|
| `Legible` | Stone fully readable |
| `Partial` | Stone partially readable |
| `Illegible` | Stone unreadable |

### 5.5 Ordinance Type (PROXY)

Field: `ordinance_type`

| Value | Meaning |
|-------|---------|
| `Baptism` | Proxy baptism for the dead |
| `Endowment` | Proxy endowment |
| `Sealing` | Proxy sealing |

---

## 6. Record ID Convention

Every data row must have a `record_id`. The `record_id` is a simple sequential integer beginning at 1. It is unique within a file but not globally unique. Its purpose is structural: to give every row a stable identifier so that errors can be reported and corrections can reference specific rows.

Example: `1, 2, 3, 4...`

Producers must not reuse `record_id` values within a file. If a row is removed, its `record_id` should not be reassigned.

---

## 7. File Naming Convention

GENRECORD files use the extension `.genrecord`. The recommended file naming pattern is:

```
society_type_scope_daterange.genrecord
```

Examples:

- `saghs_cem_bexar_1850-1975.genrecord`
- `saghs_marr_bexar_1900-1950.genrecord`
- `wayne-county-tn_census_1880.genrecord`

File names should be lowercase, use hyphens within components, and underscores between components. Spaces are not permitted.

---

## 8. Custom Fields

Producers may add custom fields not defined in the standard schema. All custom fields must be prefixed with `x_` to distinguish them from standard fields.

Examples:

- `x_find_a_grave_id` — Find A Grave memorial ID
- `x_ancestry_pid` — Ancestry.com person ID
- `x_transcriber` — Name of individual transcriber

Custom fields may appear anywhere in the column definition row. Consumers that do not recognize a custom field should ignore it without error.

---

## 9. Record Type Taxonomy

Each GENRECORD file contains exactly one record type, declared in the `#type` header field. The following type codes are defined in GENRECORD 1.0:

| Code | Type | Category |
|------|------|----------|
| `BIRTH` | Birth record | Vital |
| `DEATH` | Death record | Vital |
| `BURI` | Burial record | Vital |
| `CEM` | Cemetery transcription | Vital |
| `MARR` | Marriage record | Vital |
| `DIV` | Divorce record | Vital |
| `CENSUS` | Census extract | Legal |
| `NAT` | Naturalization | Legal |
| `PROB` | Probate / will abstract | Legal |
| `LAND` | Land / deed abstract | Legal |
| `COURT` | Court record | Legal |
| `PRISON` | Prison / penitentiary | Legal |
| `TAX` | Tax record | Legal |
| `VOTE` | Voter registration / poll book | Legal |
| `IMMIG` | Immigration / arrival record | Legal |
| `EMIG` | Emigration / departure record | Legal |
| `MIL` | Military record | Military |
| `BAPT` | Baptism / Christening | Religious |
| `CONF` | Confirmation | Religious |
| `CONGR` | Congregation membership | Religious |
| `CLERGY` | Clergy / ordination | Religious |
| `PROXY` | LDS proxy ordinance | Religious |
| `EXCOM` | Excommunication | Religious |
| `BARMIT` | Bar/Bat Mitzvah | Religious |
| `INITIAT` | Religious initiation | Religious |
| `SCHOOL` | School enrollment | Education |
| `DEGREE` | College / university degree | Education |
| `CERT` | Vocational / professional certification | Education |
| `TEACH` | Teacher record | Education |
| `OBIT` | Obituary | Newspaper |
| `MARANN` | Marriage announcement | Newspaper |
| `BIRTHANN` | Birth announcement | Newspaper |
| `BIO` | Biography abstract | Compiled |
| `BIBLE` | Family bible record | Compiled |
| `FUNERAL` | Funeral home record | Compiled |

Types marked as stubs (`BARMIT`, `INITIAT`) have minimal field definitions and are awaiting community input from practitioners of those faith traditions.

---

## 10. Field Schemas

The following schemas define the fields for each record type. Fields marked **Required** must be present in every record. Fields marked **Recommended** should be included whenever the data is available. Fields marked **Optional** may be omitted freely.

The field `given` holds all given names as recorded in the source, including middle names. No separate middle name field is defined. This convention applies across all record types.

Producers may add custom `x_` fields to any record type.

### 10.1 BIRTH — Birth record

Category: Vital

| Field | Status | Notes |
|-------|--------|-------|
| `record_id` | Required | Universal unique identifier |
| `gender` | Required | M or F |
| `surname` | Recommended | Child's surname |
| `given` | Recommended | All given names as recorded |
| `namesake` | Optional | Who child was named after |
| `birth_date` | Recommended | Full date if known |
| `birth_place` | Recommended | City, county, state |
| `birth_year` | Optional | When full date unknown |
| `father_surname` | Recommended | |
| `father_given` | Recommended | |
| `father_birthplace` | Optional | |
| `mother_surname` | Recommended | Maiden name |
| `mother_given` | Recommended | |
| `mother_birthplace` | Optional | |
| `source` | Recommended | Register name, volume, page |
| `notes` | Optional | |

### 10.2 DEATH — Death record

Category: Vital

| Field | Status | Notes |
|-------|--------|-------|
| `record_id` | Required | Universal unique identifier |
| `surname` | Recommended | |
| `given` | Recommended | All given names as recorded |
| `gender` | Recommended | M or F |
| `death_date` | Recommended | |
| `death_place` | Recommended | City, county, state |
| `death_year` | Optional | When full date unknown |
| `birth_date` | Optional | |
| `birth_year` | Optional | |
| `age` | Optional | Age at death as recorded |
| `cause` | Optional | Cause of death as recorded |
| `father_surname` | Optional | |
| `father_given` | Optional | |
| `mother_surname` | Optional | Maiden name |
| `mother_given` | Optional | |
| `spouse_surname` | Optional | |
| `spouse_given` | Optional | |
| `burial_place` | Optional | Cemetery name, city |
| `informant` | Optional | Name of person who reported death |
| `source` | Recommended | Register name, volume, page |
| `notes` | Optional | |

### 10.3 BURI — Burial record

Category: Vital

| Field | Status | Notes |
|-------|--------|-------|
| `record_id` | Required | Universal unique identifier |
| `surname` | Recommended | |
| `given` | Recommended | All given names as recorded |
| `gender` | Recommended | M or F |
| `burial_date` | Recommended | |
| `burial_place` | Recommended | Cemetery name, city, county, state |
| `death_date` | Optional | |
| `birth_date` | Optional | |
| `birth_year` | Optional | |
| `age` | Optional | Age at death as recorded |
| `funeral_home` | Optional | |
| `officiating_clergy` | Optional | |
| `lot` | Optional | Lot or plot number |
| `section` | Optional | Cemetery section |
| `source` | Recommended | Register name, volume, page |
| `notes` | Optional | |

### 10.4 CEM — Cemetery transcription

Category: Vital

| Field | Status | Notes |
|-------|--------|-------|
| `record_id` | Required | Universal unique identifier |
| `surname` | Recommended | |
| `given` | Recommended | All given names as recorded |
| `gender` | Optional | M or F |
| `birth_date` | Recommended | |
| `birth_year` | Optional | When full date unknown |
| `death_date` | Recommended | |
| `death_year` | Optional | When full date unknown |
| `cemetery` | Recommended | Cemetery name |
| `cemetery_place` | Recommended | City, county, state |
| `section` | Optional | |
| `lot` | Optional | |
| `inscription` | Optional | Full transcription of stone text |
| `condition` | Optional | Legible, partial, illegible |
| `latitude` | Optional | GPS latitude |
| `longitude` | Optional | GPS longitude |
| `map_url` | Optional | Link to mapping service or Find A Grave |
| `photo_url` | Optional | URL to headstone photograph |
| `source` | Recommended | Transcriber name, date |
| `notes` | Optional | |

### 10.5 MARR — Marriage record

Category: Vital

| Field | Status | Notes |
|-------|--------|-------|
| `record_id` | Required | Universal unique identifier |
| `groom_surname` | Recommended | |
| `groom_given` | Recommended | All given names as recorded |
| `groom_birth_year` | Optional | |
| `groom_birthplace` | Optional | |
| `groom_father_surname` | Optional | |
| `groom_father_given` | Optional | |
| `groom_mother_surname` | Optional | Maiden name |
| `groom_mother_given` | Optional | |
| `bride_surname` | Recommended | Maiden name |
| `bride_given` | Recommended | All given names as recorded |
| `bride_birth_year` | Optional | |
| `bride_birthplace` | Optional | |
| `bride_father_surname` | Optional | |
| `bride_father_given` | Optional | |
| `bride_mother_surname` | Optional | Maiden name |
| `bride_mother_given` | Optional | |
| `marr_date` | Recommended | |
| `marr_place` | Recommended | City, county, state |
| `officiating_clergy` | Optional | |
| `witness1_surname` | Optional | |
| `witness1_given` | Optional | |
| `witness2_surname` | Optional | |
| `witness2_given` | Optional | |
| `source` | Recommended | Register name, volume, page |
| `notes` | Optional | |

### 10.6 DIV — Divorce record

Category: Vital

| Field | Status | Notes |
|-------|--------|-------|
| `record_id` | Required | Universal unique identifier |
| `petitioner_surname` | Recommended | |
| `petitioner_given` | Recommended | All given names as recorded |
| `respondent_surname` | Recommended | |
| `respondent_given` | Recommended | All given names as recorded |
| `div_date` | Recommended | Date decree granted |
| `div_place` | Recommended | City, county, state |
| `filed_date` | Optional | Date petition filed |
| `grounds` | Optional | Grounds as recorded |
| `marr_date` | Optional | Date of original marriage |
| `source` | Recommended | Court record, volume, page |
| `notes` | Optional | |

### 10.7 CENSUS — Census extract

Category: Legal

| Field | Status | Notes |
|-------|--------|-------|
| `record_id` | Required | Universal unique identifier |
| `census_year` | Required | e.g. 1880 |
| `surname` | Recommended | |
| `given` | Recommended | All given names as recorded |
| `gender` | Recommended | M or F |
| `age` | Recommended | Age as recorded |
| `birth_year` | Optional | Derived from age if not stated |
| `birthplace` | Recommended | State or country as recorded |
| `father_birthplace` | Optional | |
| `mother_birthplace` | Optional | |
| `relation` | Recommended | Relation to head of household |
| `head_surname` | Optional | Head of household surname |
| `head_given` | Optional | Head of household given name |
| `marital_status` | Optional | S, M, W, D as recorded |
| `occupation` | Optional | |
| `dwelling` | Optional | Dwelling number |
| `family_no` | Optional | Family number |
| `ward` | Optional | |
| `county` | Recommended | |
| `state` | Recommended | |
| `source` | Recommended | Roll, page, enumeration district |
| `notes` | Optional | |

### 10.8 NAT — Naturalization

Category: Legal

| Field | Status | Notes |
|-------|--------|-------|
| `record_id` | Required | Universal unique identifier |
| `surname` | Recommended | |
| `given` | Recommended | All given names as recorded |
| `gender` | Recommended | M or F |
| `birth_date` | Optional | |
| `birth_year` | Optional | |
| `birthplace` | Recommended | Country of origin |
| `arrival_date` | Optional | Date of arrival in US |
| `arrival_port` | Optional | |
| `decl_date` | Optional | Date of declaration of intent |
| `nat_date` | Recommended | Date of naturalization |
| `nat_place` | Recommended | Court, city, state |
| `occupation` | Optional | |
| `residence` | Optional | At time of naturalization |
| `witness1_surname` | Optional | |
| `witness1_given` | Optional | |
| `source` | Recommended | Court record, volume, page |
| `notes` | Optional | |

### 10.9 PROB — Probate / will abstract

Category: Legal

| Field | Status | Notes |
|-------|--------|-------|
| `record_id` | Required | Universal unique identifier |
| `surname` | Recommended | Deceased |
| `given` | Recommended | All given names as recorded |
| `gender` | Recommended | M or F |
| `death_date` | Optional | |
| `death_year` | Optional | |
| `will_date` | Optional | Date will was written |
| `prob_date` | Recommended | Date probated |
| `prob_place` | Recommended | County, state |
| `executor_surname` | Optional | |
| `executor_given` | Optional | |
| `heirs` | Optional | Names of heirs as recorded |
| `estate_value` | Optional | Appraised value if recorded |
| `source` | Recommended | Court record, volume, page |
| `notes` | Optional | |

### 10.10 LAND — Land / deed abstract

Category: Legal

| Field | Status | Notes |
|-------|--------|-------|
| `record_id` | Required | Universal unique identifier |
| `grantor_surname` | Recommended | Seller or granter |
| `grantor_given` | Recommended | All given names as recorded |
| `grantee_surname` | Recommended | Buyer or recipient |
| `grantee_given` | Recommended | All given names as recorded |
| `deed_date` | Recommended | Date of transaction |
| `record_date` | Optional | Date recorded in register |
| `county` | Recommended | |
| `state` | Recommended | |
| `description` | Optional | Legal description of property |
| `consideration` | Optional | Purchase price if recorded |
| `witness1_surname` | Optional | |
| `witness1_given` | Optional | |
| `source` | Recommended | Deed book, volume, page |
| `notes` | Optional | |

### 10.11 COURT — Court record

Category: Legal

| Field | Status | Notes |
|-------|--------|-------|
| `record_id` | Required | Universal unique identifier |
| `plaintiff_surname` | Recommended | |
| `plaintiff_given` | Recommended | All given names as recorded |
| `defendant_surname` | Recommended | |
| `defendant_given` | Recommended | All given names as recorded |
| `case_type` | Recommended | Civil, criminal, equity, etc. |
| `filed_date` | Recommended | |
| `decided_date` | Optional | |
| `court_name` | Recommended | |
| `county` | Recommended | |
| `state` | Recommended | |
| `verdict` | Optional | Outcome as recorded |
| `source` | Recommended | Court record, volume, page |
| `notes` | Optional | |

### 10.12 PRISON — Prison / penitentiary

Category: Legal

| Field | Status | Notes |
|-------|--------|-------|
| `record_id` | Required | Universal unique identifier |
| `surname` | Recommended | |
| `given` | Recommended | All given names as recorded |
| `gender` | Recommended | M or F |
| `birth_year` | Optional | |
| `birthplace` | Optional | |
| `commit_date` | Recommended | Date of commitment |
| `release_date` | Optional | |
| `institution` | Recommended | Prison or penitentiary name |
| `institution_place` | Recommended | City, state |
| `offense` | Optional | Offense as recorded |
| `sentence` | Optional | Sentence as recorded |
| `occupation` | Optional | |
| `physical_desc` | Optional | Physical description if recorded |
| `source` | Recommended | Register name, volume, page |
| `notes` | Optional | |

### 10.13 TAX — Tax record

Category: Legal

| Field | Status | Notes |
|-------|--------|-------|
| `record_id` | Required | Universal unique identifier |
| `surname` | Recommended | |
| `given` | Recommended | All given names as recorded |
| `tax_year` | Required | Year of assessment |
| `county` | Recommended | |
| `state` | Recommended | |
| `tax_type` | Optional | Property, poll, income, etc. |
| `assessed_value` | Optional | |
| `tax_amount` | Optional | |
| `property_desc` | Optional | Description of taxed property |
| `district` | Optional | Tax district if recorded |
| `source` | Recommended | Tax list, volume, page |
| `notes` | Optional | |

### 10.14 VOTE — Voter registration / poll book

Category: Legal

| Field | Status | Notes |
|-------|--------|-------|
| `record_id` | Required | Universal unique identifier |
| `surname` | Recommended | |
| `given` | Recommended | All given names as recorded |
| `gender` | Recommended | M or F |
| `birth_year` | Optional | |
| `birthplace` | Optional | |
| `reg_date` | Recommended | Date of registration |
| `reg_place` | Recommended | County, state |
| `precinct` | Optional | |
| `ward` | Optional | |
| `occupation` | Optional | |
| `residence` | Optional | Address at time of registration |
| `party` | Optional | Political party if recorded |
| `source` | Recommended | Poll book, register, volume, page |
| `notes` | Optional | |

### 10.15 IMMIG — Immigration / arrival record

Category: Legal

| Field | Status | Notes |
|-------|--------|-------|
| `record_id` | Required | Universal unique identifier |
| `surname` | Recommended | |
| `given` | Recommended | All given names as recorded |
| `gender` | Recommended | M or F |
| `birth_year` | Optional | |
| `birthplace` | Recommended | Country or region of origin |
| `arrival_date` | Recommended | |
| `arrival_port` | Recommended | Port of entry |
| `departure_port` | Optional | |
| `ship` | Optional | Vessel name |
| `destination` | Optional | Stated destination |
| `occupation` | Optional | |
| `marital_status` | Optional | S, M, W, D |
| `contact_name` | Optional | Name of contact in destination country |
| `contact_place` | Optional | |
| `passage_paid_by` | Optional | Self, relative, employer, etc. |
| `source` | Recommended | Manifest, roll, page |
| `notes` | Optional | |

### 10.16 EMIG — Emigration / departure record

Category: Legal

| Field | Status | Notes |
|-------|--------|-------|
| `record_id` | Required | Universal unique identifier |
| `surname` | Recommended | |
| `given` | Recommended | All given names as recorded |
| `gender` | Recommended | M or F |
| `birth_year` | Optional | |
| `birthplace` | Recommended | Country or region |
| `departure_date` | Recommended | |
| `departure_port` | Recommended | |
| `destination` | Optional | Stated destination country |
| `ship` | Optional | Vessel name |
| `occupation` | Optional | |
| `source` | Recommended | Emigration register, volume, page |
| `notes` | Optional | |

### 10.17 MIL — Military record

Category: Military

| Field | Status | Notes |
|-------|--------|-------|
| `record_id` | Required | Universal unique identifier |
| `surname` | Recommended | |
| `given` | Recommended | All given names as recorded |
| `gender` | Recommended | M or F |
| `birth_date` | Optional | |
| `birth_year` | Optional | |
| `birthplace` | Optional | |
| `enlist_date` | Recommended | |
| `enlist_place` | Optional | |
| `discharge_date` | Optional | |
| `discharge_place` | Optional | |
| `branch` | Recommended | Army, Navy, Marines, etc. |
| `rank` | Optional | Rank at discharge or as recorded |
| `unit` | Optional | Regiment, division, ship, etc. |
| `conflict` | Optional | War or conflict name |
| `service_no` | Optional | Service or serial number |
| `pension_no` | Optional | |
| `occupation` | Optional | Civilian occupation if recorded |
| `physical_desc` | Optional | Height, eye color, etc. |
| `source` | Recommended | Record type, roll, page |
| `notes` | Optional | |

### 10.18 BAPT — Baptism / Christening

Category: Religious

| Field | Status | Notes |
|-------|--------|-------|
| `record_id` | Required | Universal unique identifier |
| `surname` | Recommended | |
| `given` | Recommended | All given names as recorded |
| `gender` | Recommended | M or F |
| `birth_date` | Optional | |
| `birth_year` | Optional | |
| `bapt_date` | Recommended | |
| `bapt_place` | Recommended | Church name, city, state |
| `denomination` | Recommended | Catholic, Lutheran, Methodist, etc. |
| `father_surname` | Recommended | |
| `father_given` | Recommended | |
| `mother_surname` | Recommended | Maiden name |
| `mother_given` | Recommended | |
| `godfather_surname` | Optional | |
| `godfather_given` | Optional | |
| `godmother_surname` | Optional | |
| `godmother_given` | Optional | |
| `officiating_clergy` | Optional | |
| `source` | Recommended | Register name, volume, page |
| `notes` | Optional | |

### 10.19 CONF — Confirmation

Category: Religious

| Field | Status | Notes |
|-------|--------|-------|
| `record_id` | Required | Universal unique identifier |
| `surname` | Recommended | |
| `given` | Recommended | All given names as recorded |
| `gender` | Recommended | M or F |
| `birth_year` | Optional | |
| `conf_date` | Recommended | |
| `conf_place` | Recommended | Church name, city, state |
| `denomination` | Recommended | |
| `sponsor_surname` | Optional | |
| `sponsor_given` | Optional | |
| `officiating_clergy` | Optional | |
| `father_surname` | Optional | |
| `father_given` | Optional | |
| `mother_surname` | Optional | Maiden name |
| `mother_given` | Optional | |
| `source` | Recommended | Register name, volume, page |
| `notes` | Optional | |

### 10.20 CONGR — Congregation membership

Category: Religious

| Field | Status | Notes |
|-------|--------|-------|
| `record_id` | Required | Universal unique identifier |
| `surname` | Recommended | |
| `given` | Recommended | All given names as recorded |
| `gender` | Recommended | M or F |
| `birth_year` | Optional | |
| `join_date` | Recommended | Date joined congregation |
| `leave_date` | Optional | Date left or removed |
| `leave_reason` | Optional | Transfer, death, excommunication, etc. |
| `congregation` | Recommended | Congregation or church name |
| `congregation_place` | Recommended | City, state |
| `denomination` | Recommended | |
| `source` | Recommended | Register name, volume, page |
| `notes` | Optional | |

### 10.21 CLERGY — Clergy / ordination

Category: Religious

| Field | Status | Notes |
|-------|--------|-------|
| `record_id` | Required | Universal unique identifier |
| `surname` | Recommended | |
| `given` | Recommended | All given names as recorded |
| `gender` | Recommended | M or F |
| `birth_year` | Optional | |
| `ord_date` | Recommended | Date of ordination or licensing |
| `ord_place` | Recommended | City, state |
| `denomination` | Recommended | |
| `title` | Optional | Pastor, priest, rabbi, imam, etc. |
| `congregation` | Optional | |
| `congregation_place` | Optional | |
| `source` | Recommended | Register name, volume, page |
| `notes` | Optional | |

### 10.22 PROXY — LDS proxy ordinance

Category: Religious

| Field | Status | Notes |
|-------|--------|-------|
| `record_id` | Required | Universal unique identifier |
| `surname` | Recommended | Deceased person |
| `given` | Recommended | All given names as recorded |
| `gender` | Recommended | M or F |
| `birth_year` | Optional | |
| `death_year` | Optional | |
| `ordinance_type` | Recommended | Baptism, endowment, sealing |
| `ordinance_date` | Recommended | Date ordinance performed |
| `ordinance_place` | Recommended | Temple name, city, state |
| `proxy_surname` | Optional | Person who performed the ordinance |
| `proxy_given` | Optional | |
| `source` | Recommended | FamilySearch or temple record |
| `notes` | Optional | |

### 10.23 EXCOM — Excommunication

Category: Religious

| Field | Status | Notes |
|-------|--------|-------|
| `record_id` | Required | Universal unique identifier |
| `surname` | Recommended | |
| `given` | Recommended | All given names as recorded |
| `gender` | Recommended | M or F |
| `birth_year` | Optional | |
| `excom_date` | Recommended | Date of excommunication |
| `congregation` | Recommended | |
| `congregation_place` | Recommended | City, state |
| `denomination` | Recommended | |
| `grounds` | Optional | Grounds as recorded |
| `source` | Recommended | Register name, volume, page |
| `notes` | Optional | |

### 10.24 BARMIT — Bar/Bat Mitzvah

Category: Religious

| Field | Status | Notes |
|-------|--------|-------|
| `record_id` | Required | Universal unique identifier |
| `surname` | Recommended | |
| `given` | Recommended | All given names as recorded |
| `gender` | Recommended | M or F |
| `birth_year` | Optional | |
| `ceremony_date` | Recommended | |
| `ceremony_place` | Recommended | Synagogue name, city, state |
| `father_surname` | Optional | |
| `father_given` | Optional | |
| `mother_surname` | Optional | |
| `mother_given` | Optional | |
| `officiating_clergy` | Optional | Rabbi name |
| `source` | Recommended | Register name, volume, page |
| `notes` | Optional | Stub — awaiting community input |

### 10.25 INITIAT — Religious initiation

Category: Religious

| Field | Status | Notes |
|-------|--------|-------|
| `record_id` | Required | Universal unique identifier |
| `surname` | Recommended | |
| `given` | Recommended | All given names as recorded |
| `gender` | Recommended | M or F |
| `birth_year` | Optional | |
| `initiat_date` | Recommended | |
| `initiat_place` | Recommended | Institution name, city, state |
| `tradition` | Recommended | Faith tradition as recorded |
| `ceremony_name` | Optional | Name of ceremony in that tradition |
| `source` | Recommended | Register name, volume, page |
| `notes` | Optional | Stub — awaiting community input |

### 10.26 SCHOOL — School enrollment

Category: Education

| Field | Status | Notes |
|-------|--------|-------|
| `record_id` | Required | Universal unique identifier |
| `surname` | Recommended | |
| `given` | Recommended | All given names as recorded |
| `birth_year` | Recommended | For identification |
| `gender` | Recommended | M or F |
| `enroll_date` | Recommended | |
| `grade` | Recommended | Grade or year level |
| `school_name` | Recommended | |
| `school_place` | Recommended | City, county, state |
| `father_surname` | Optional | |
| `father_given` | Optional | |
| `mother_surname` | Optional | |
| `mother_given` | Optional | |
| `grad_date` | Optional | |
| `withdraw_date` | Optional | |
| `withdraw_reason` | Optional | Transferred, deceased, etc. |
| `source` | Recommended | Register name, volume, page |
| `notes` | Optional | |

### 10.27 DEGREE — College / university degree

Category: Education

| Field | Status | Notes |
|-------|--------|-------|
| `record_id` | Required | Universal unique identifier |
| `surname` | Recommended | |
| `given` | Recommended | All given names as recorded |
| `gender` | Recommended | M or F |
| `birth_year` | Optional | |
| `degree_type` | Recommended | BA, BS, MA, PhD, MD, JD, etc. |
| `degree_field` | Optional | Field of study |
| `confer_date` | Recommended | Date degree conferred |
| `institution` | Recommended | University or college name |
| `institution_place` | Recommended | City, state |
| `honors` | Optional | Cum laude, etc. |
| `source` | Recommended | Commencement record, register |
| `notes` | Optional | |

### 10.28 CERT — Vocational / professional certification

Category: Education

| Field | Status | Notes |
|-------|--------|-------|
| `record_id` | Required | Universal unique identifier |
| `surname` | Recommended | |
| `given` | Recommended | All given names as recorded |
| `gender` | Recommended | M or F |
| `birth_year` | Optional | |
| `cert_type` | Recommended | Type of certification or trade |
| `issue_date` | Recommended | |
| `expiry_date` | Optional | |
| `issuing_body` | Recommended | Institution or board name |
| `issuing_place` | Optional | City, state |
| `source` | Recommended | Register name, volume, page |
| `notes` | Optional | |

### 10.29 TEACH — Teacher record

Category: Education

| Field | Status | Notes |
|-------|--------|-------|
| `record_id` | Required | Universal unique identifier |
| `surname` | Recommended | |
| `given` | Recommended | All given names as recorded |
| `gender` | Recommended | M or F |
| `birth_year` | Optional | |
| `cert_date` | Recommended | Date certified or licensed |
| `cert_place` | Recommended | State issuing certification |
| `employ_date` | Optional | Date of employment |
| `school_name` | Optional | |
| `school_place` | Optional | City, county, state |
| `grade_level` | Optional | Elementary, secondary, etc. |
| `subject` | Optional | Subject taught if recorded |
| `source` | Recommended | Register name, volume, page |
| `notes` | Optional | |

### 10.30 OBIT — Obituary

Category: Newspaper

| Field | Status | Notes |
|-------|--------|-------|
| `record_id` | Required | Universal unique identifier |
| `surname` | Recommended | |
| `given` | Recommended | All given names as recorded |
| `gender` | Recommended | M or F |
| `birth_date` | Optional | |
| `birth_year` | Optional | |
| `birthplace` | Optional | |
| `death_date` | Recommended | |
| `death_place` | Optional | |
| `publication` | Recommended | Newspaper name |
| `pub_date` | Recommended | Date published |
| `pub_place` | Recommended | City, state |
| `page` | Optional | Page and column |
| `survivors` | Optional | Names of survivors as recorded |
| `burial_place` | Optional | |
| `source` | Recommended | Newspaper name, date, page |
| `notes` | Optional | |

### 10.31 MARANN — Marriage announcement

Category: Newspaper

| Field | Status | Notes |
|-------|--------|-------|
| `record_id` | Required | Universal unique identifier |
| `groom_surname` | Recommended | |
| `groom_given` | Recommended | All given names as recorded |
| `bride_surname` | Recommended | Maiden name |
| `bride_given` | Recommended | All given names as recorded |
| `marr_date` | Optional | Date of marriage if stated |
| `marr_place` | Optional | |
| `publication` | Recommended | Newspaper name |
| `pub_date` | Recommended | Date published |
| `pub_place` | Recommended | City, state |
| `page` | Optional | Page and column |
| `source` | Recommended | Newspaper name, date, page |
| `notes` | Optional | |

### 10.32 BIRTHANN — Birth announcement

Category: Newspaper

| Field | Status | Notes |
|-------|--------|-------|
| `record_id` | Required | Universal unique identifier |
| `child_surname` | Recommended | |
| `child_given` | Optional | If named in announcement |
| `gender` | Optional | M or F |
| `birth_date` | Recommended | |
| `father_surname` | Recommended | |
| `father_given` | Recommended | |
| `mother_surname` | Optional | Maiden name |
| `mother_given` | Optional | |
| `publication` | Recommended | Newspaper name |
| `pub_date` | Recommended | Date published |
| `pub_place` | Recommended | City, state |
| `page` | Optional | Page and column |
| `source` | Recommended | Newspaper name, date, page |
| `notes` | Optional | |

### 10.33 BIO — Biography abstract

Category: Compiled

| Field | Status | Notes |
|-------|--------|-------|
| `record_id` | Required | Universal unique identifier |
| `surname` | Recommended | |
| `given` | Recommended | All given names as recorded |
| `gender` | Recommended | M or F |
| `birth_year` | Optional | |
| `birthplace` | Optional | |
| `death_year` | Optional | |
| `occupation` | Optional | |
| `abstract` | Optional | Brief summary of biographical content |
| `publication` | Recommended | Book or periodical title |
| `pub_year` | Recommended | |
| `pub_place` | Optional | |
| `page` | Optional | |
| `author` | Optional | |
| `source` | Recommended | Full citation |
| `notes` | Optional | |

### 10.34 BIBLE — Family bible record

Category: Compiled

| Field | Status | Notes |
|-------|--------|-------|
| `record_id` | Required | Universal unique identifier |
| `surname` | Recommended | |
| `given` | Recommended | All given names as recorded |
| `gender` | Optional | M or F |
| `event_type` | Recommended | Birth, marriage, death, etc. |
| `event_date` | Recommended | |
| `event_place` | Optional | |
| `bible_owner` | Recommended | Name of bible owner or family |
| `transcriber` | Optional | Name of person who transcribed |
| `transcribe_date` | Optional | |
| `repository` | Optional | Where bible is held |
| `source` | Recommended | Bible description, repository |
| `notes` | Optional | |

### 10.35 FUNERAL — Funeral home record

Category: Compiled

| Field | Status | Notes |
|-------|--------|-------|
| `record_id` | Required | Universal unique identifier |
| `surname` | Recommended | |
| `given` | Recommended | All given names as recorded |
| `gender` | Recommended | M or F |
| `birth_date` | Optional | |
| `birth_year` | Optional | |
| `death_date` | Recommended | |
| `death_place` | Optional | |
| `burial_date` | Optional | |
| `burial_place` | Optional | |
| `age` | Optional | Age at death as recorded |
| `cause` | Optional | Cause of death if recorded |
| `funeral_home` | Recommended | |
| `funeral_place` | Recommended | City, state |
| `next_of_kin` | Optional | Name of next of kin |
| `source` | Recommended | Register name, volume, page |
| `notes` | Optional | |

---

## 11. Sample Files

### 11.1 Cemetery Transcription (CEM)

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

### 11.2 Birth Record (BIRTH)

```
##GENRECORD 1.0
#version: 1.0
#type: BIRTH
#title: Bexar County Birth Records 1900-1920
#society: San Antonio Genealogical and Historical Society
#society_url: https://txsaghs.org
#created: 2026-03-29
#license: CC0
#coverage.place: Bexar County, Texas, USA
#coverage.dates: 1900-1920
---
record_id,gender,surname,given,birth_date,birth_place,father_surname,father_given,mother_surname,mother_given,source,notes
1,M,Stricklin,Charles Walter,15 Jun 1910,San Antonio TX,Stricklin,Walter Davis,Jones,Mary Ellen,Bexar County Birth Register Vol 3 p 42,
2,F,Garcia,Maria Elena,3 Mar 1912,San Antonio TX,Garcia,Jose,Reyes,Carmen,,
```

---

## Appendix A: Versioning Policy

GENRECORD follows a simple versioning policy designed to protect producers and consumers from breaking changes.

- **Minor versions** (1.0, 1.1, 1.2) add optional fields or new record types. A consumer built for 1.0 can read a 1.2 file without modification.
- **Major versions** (1.0, 2.0) may introduce breaking changes and are expected to be rare.
- Fields are never removed in minor versions. Deprecated fields are marked in the spec and ignored by producers, but remain valid for consumers.
- The version number is declared in both the `##GENRECORD` line and the `#version` header field.

---

## Appendix B: Contributing

GENRECORD is a community standard. Corrections, additions, and new record type definitions are welcome. The canonical repository is hosted at [github.com/GENRECORD/genrecord](https://github.com/GENRECORD/genrecord).

To propose changes: open an issue or pull request on GitHub. To report errors in this document: contact the maintainer via the repository.

Faith traditions with institutional genealogical records who wish to contribute field schemas for BARMIT, INITIAT, or new record types are especially encouraged to participate.

---

## Appendix C: License

This specification is released under the **CC0 1.0 Universal Public Domain Dedication**. To the extent possible under law, the authors have waived all copyright and related rights to this work. You may copy, modify, distribute, and use this specification for any purpose, commercial or non-commercial, without asking permission.

Full license text: [https://creativecommons.org/publicdomain/zero/1.0/](https://creativecommons.org/publicdomain/zero/1.0/)
