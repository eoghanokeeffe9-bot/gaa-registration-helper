# GAA Registration System — Google Sheets Overview

## Background

This document outlines the plan to build a structured, low-maintenance Google Sheets registration system for the club. The registrar exports raw member data from the registration platform and needs a clean, automated way to organise members by age group and gender, with consistent data formatting throughout.

---

## Age Groups (2026)

The club caters for players from U6 through to U18 in 2-year increments. Eligibility is based on the age a player turns during the 2026 calendar year.

| Age Group | Birth Years |
|-----------|-------------|
| U6        | 2021 – 2020 |
| U8        | 2019 – 2018 |
| U10       | 2017 – 2016 |
| U12       | 2015 – 2014 |
| U14       | 2013 – 2012 |
| U16       | 2011 – 2010 |
| U18       | 2009 – 2008 |

---

## System Architecture

### Google Sheets Structure

| Sheet | Purpose |
|-------|---------|
| `RAW_DATA` | Paste exported registration data here — no edits, untouched |
| `MASTER` | Cleaned and enriched data, driven by formulas referencing RAW_DATA |
| `U6 Boys` / `U6 Girls` | Auto-populated from MASTER via FILTER formula |
| `U8 Boys` / `U8 Girls` | Auto-populated from MASTER via FILTER formula |
| `U10 Boys` / `U10 Girls` | Auto-populated from MASTER via FILTER formula |
| `U12 Boys` / `U12 Girls` | Auto-populated from MASTER via FILTER formula |
| `U14 Boys` / `U14 Girls` | Auto-populated from MASTER via FILTER formula |
| `U16 Boys` / `U16 Girls` | Auto-populated from MASTER via FILTER formula |
| `U18 Boys` / `U18 Girls` | Auto-populated from MASTER via FILTER formula |

---

## Data Cleaning (MASTER Sheet)

The MASTER sheet will apply formulas to clean and standardise the following fields pulled from RAW_DATA:

| Field | Cleaning Required |
|-------|------------------|
| Member Name | Trim whitespace, fix capitalisation (PROPER) |
| Parent Name | Trim whitespace, fix capitalisation (PROPER) |
| Guardian Name | Trim whitespace, fix capitalisation (PROPER) |
| Phone Number | Strip spaces, dashes, brackets — standardise format |
| Email | Trim whitespace, lowercase |
| Date of Birth | Force consistent DD/MM/YYYY format, flag invalid entries |
| Membership Plan | Trim whitespace, standardise casing |
| Gender | Standardise to consistent value (e.g. Boys / Girls) |

### Derived/Calculated Columns (MASTER Sheet)

These columns will be automatically calculated from cleaned data:

| Column | Description |
|--------|-------------|
| Birth Year | Extracted from cleaned DOB — used for age group logic |
| Age Group | Auto-assigned (U6–U18) based on birth year |
| Group Tag | Combines Age Group + Gender (e.g. `U8 Boys`) — drives sheet filtering |

---

## Age Group Sheets

Each age group sheet (e.g. `U8 Boys`) will:
- Automatically pull all matching members from MASTER using a `FILTER` formula
- Require no manual updates — when RAW_DATA is updated and MASTER refreshes, all sheets update
- Display key fields only: Member Name, Parent Name, Guardian Name, Phone, Email, DOB, Membership Plan

---

## Key Fields Carried Across All Sheets

- Membership Plan
- Member Name
- Parent / Guardian Name
- Contact Number
- Email Address
- Date of Birth
- Age Group (auto-assigned)

---

## Next Steps

1. **Confirm export format** — share a sample row from the registration export (personal data removed) so formulas can be matched to actual column headers and data formats
2. **Confirm DOB format** — identify exactly how dates of birth are exported (e.g. `14/03/2020`, `2020-03-14`, `14-Mar-20`)
3. **Confirm gender field** — how is gender recorded in the export (e.g. Male/Female, M/F, or embedded in membership plan name)
4. **Build RAW_DATA sheet** — set up column headers to match the export
5. **Build MASTER sheet** — write all cleaning and derived column formulas
6. **Build age group sheets** — one per group (14 sheets total: U6–U18, Boys and Girls)
7. **Test with live data** — paste a real export, verify all formulas populate correctly
8. **Handover and documentation** — notes on how to use the system each registration cycle

---

*Document created: March 2026*
*Author: GAA Registrar*
