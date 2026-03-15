# GAA Registration System Specification
## St Lomans Mullingar GAA Club — Lomans Lions (2026)

---

### Overview
Google Sheets workbook (private, registrar-only) that:
1. Accepts a full Clubforce export into RAW_DATA
2. Cleans and enriches data in MASTER
3. Filters into 14 age group sheets automatically
4. Enables fast PDF generation per age group for coach distribution

---

### Source Data (Clubforce Export Columns)
Key fields used:
- Member first name, Member last name
- DOB (separate column)
- Gender (separate column)
- Guardian first name, Guardian last name
- Contact email, Contact number
- Membership status
- Medical conditions / injuries / allergies / special needs
- Member code (unique ID — not exposed to coaches)
- Plan name (for reference)
- Registered date & time

Fields deliberately excluded from coach output:
- Member address, county, country
- All consent/GDPR fields
- Lotto lines, hoodie size, school, volunteering, source

---

### Sheet Structure

#### RAW_DATA
- Direct paste of full Clubforce export (no edits)
- Headers match Clubforce column names exactly
- Cleared and replaced each import cycle

#### MASTER
Columns (formula-driven, referencing RAW_DATA):
1. Member Name (PROPER + TRIM of first + last)
2. DOB (forced DD/MM/YYYY)
3. Birth Year (extracted from DOB)
4. Age Group (U6–U18, see logic below)
5. Gender (standardised: "Boys" / "Girls")
6. Group Tag (Age Group + " " + Gender, e.g. "U10 Boys")
7. Guardian Name (PROPER + TRIM of guardian first + last)
8. Contact Email (TRIM + LOWER)
9. Contact Number (stripped of spaces/dashes/brackets)
10. Medical Conditions (raw text from export)
11. Membership Status (raw from export)
12. Plan Name (raw from export)

Filter: MASTER only includes rows where Membership Status = "Active"

#### Age Group Assignment Logic (2026)
| Age Group | Birth Years |
|---|---|
| U6  | 2021–2020 |
| U8  | 2019–2018 |
| U10 | 2017–2016 |
| U12 | 2015–2014 |
| U14 | 2013–2012 |
| U16 | 2011–2010 |
| U18 | 2009–2008 |

Formula approach: IFS() or nested IF() matching Birth Year ranges.
Edge case: Birth years outside all ranges → flagged as "UNASSIGNED" in Age Group column.

#### SUMMARY Sheet
A single sheet providing a count reconciliation across all groups after each import. Allows the registrar to confirm no active members are missing from a group sheet.

| Column A | Column B |
|---|---|
| Group | Count |
| U6 Boys | `=COUNTIF(MASTER!F:F,"U6 Boys")` |
| U6 Girls | `=COUNTIF(MASTER!F:F,"U6 Girls")` |
| U8 Boys | `=COUNTIF(MASTER!F:F,"U8 Boys")` |
| U8 Girls | `=COUNTIF(MASTER!F:F,"U8 Girls")` |
| U10 Boys | `=COUNTIF(MASTER!F:F,"U10 Boys")` |
| U10 Girls | `=COUNTIF(MASTER!F:F,"U10 Girls")` |
| U12 Boys | `=COUNTIF(MASTER!F:F,"U12 Boys")` |
| U12 Girls | `=COUNTIF(MASTER!F:F,"U12 Girls")` |
| U14 Boys | `=COUNTIF(MASTER!F:F,"U14 Boys")` |
| U14 Girls | `=COUNTIF(MASTER!F:F,"U14 Girls")` |
| U16 Boys | `=COUNTIF(MASTER!F:F,"U16 Boys")` |
| U16 Girls | `=COUNTIF(MASTER!F:F,"U16 Girls")` |
| U18 Boys | `=COUNTIF(MASTER!F:F,"U18 Boys")` |
| U18 Girls | `=COUNTIF(MASTER!F:F,"U18 Girls")` |
| **Total Youth** | `=SUM(B2:B15)` |
| Non-Playing | `=COUNTIF(MASTER!L:L,"Adult Non-Playing")` |
| Senior Men | `=COUNTIFS(MASTER!L:L,"Adult Player + Gym",MASTER!E:E,"Boys",MASTER!K:K,"active")+COUNTIFS(MASTER!L:L,"Adult Student + Gym",MASTER!E:E,"Boys",MASTER!K:K,"active")` |
| Senior Women | `=COUNTIFS(MASTER!L:L,"Adult Player + Gym",MASTER!E:E,"Girls",MASTER!K:K,"active")+COUNTIFS(MASTER!L:L,"Adult Student + Gym",MASTER!E:E,"Girls",MASTER!K:K,"active")` |
| Senior Active | `=COUNTIF(MASTER!L:L,"Senior Active Group Membership")` |
| Gym | `=COUNTIF(MASTER!L:L,"Gym Membership")` |
| Dads & Lads | `=COUNTIF(MASTER!L:L,"Dads & Lads Membership")` |
| G4MO | `=COUNTIF(MASTER!L:L,"Gaelic 4 Mothers & Others Membership")` |
| **Total Adults** | `=SUM(B17:B23)` |
| UNKNOWN Gender | `=COUNTIF(MASTER!E:E,"UNKNOWN")` |
| UNASSIGNED Age | `=COUNTIF(MASTER!D:D,"UNASSIGNED")` |
| **Total Active** | `=COUNTIF(MASTER!K:K,"active")` |

After each import: Total Youth + Total Adults + UNASSIGNED should equal Total Active. Any UNKNOWN Gender or UNASSIGNED Age count above zero means records need review in the NEEDS REVIEW sheet.

#### NEEDS REVIEW Sheet
Auto-populated sheet that surfaces any active member who could not be placed in a group — either because their gender was unrecognised or their birth year falls outside U6–U18 ranges.

Formula for A2:
```
=IFERROR(FILTER(
  {MASTER!A:A,MASTER!B:B,MASTER!D:D,MASTER!E:E,MASTER!F:F,MASTER!G:G,MASTER!H:H,MASTER!I:I},
  ((MASTER!E:E="UNKNOWN")+(MASTER!D:D="UNASSIGNED"))*(MASTER!K:K="active")
),"No issues found")
```

Columns: Member Name, DOB, Age Group, Gender, Group Tag, Guardian Name, Contact Email, Contact Number.

This sheet should be empty after every clean import. If it has rows, cross-check against the Clubforce source data and correct before sending PDFs to coaches.

#### 14 Age Group Sheets (Youth)
One sheet per group: U6 Boys, U6 Girls, U8 Boys, U8 Girls, ..., U18 Boys, U18 Girls

Each sheet uses FILTER formula from MASTER:
```
=FILTER(MASTER_RANGE, MASTER[Group Tag] = "U10 Boys")
```

Columns displayed on each age group sheet (the coach view):
1. Member Name
2. DOB
3. Age Group
4. Guardian Name
5. Contact Email
6. Contact Number
7. Medical Conditions

#### 7 Adult Plan Sheets
One sheet per adult plan type, filtered from MASTER by Plan Name + active status.

| Sheet Name | Filter Logic |
|---|---|
| Non-Playing | Plan = "Adult Non-Playing" |
| Senior Men | (Plan = "Adult Player + Gym" OR Plan = "Adult Student + Gym") AND Gender = "Boys" |
| Senior Women | (Plan = "Adult Player + Gym" OR Plan = "Adult Student + Gym") AND Gender = "Girls" |
| Senior Active | Plan = "Senior Active Group Membership" |
| Gym | Plan = "Gym Membership" |
| Dads & Lads | Plan = "Dads & Lads Membership" |
| G4MO | Plan = "Gaelic 4 Mothers & Others Membership" |

Note: Senior Men and Senior Women combine both Adult Player + Gym and Adult Student + Gym plans, split by gender. A member on either plan will appear in the appropriate sheet based on the gender recorded in their Clubforce registration.

Columns displayed on each adult sheet:
1. Member Name
2. DOB
3. Plan Name
4. Membership Status

---

### Sheet Formulas (Google Sheets syntax)

**Member Name (MASTER col A):**
```
=PROPER(TRIM(RAW_DATA!E2&" "&RAW_DATA!F2))
```

**DOB (MASTER col B — format cells as DD/MM/YYYY):**
```
=IFERROR(DATEVALUE(SUBSTITUTE(RAW_DATA!G2,"-","/")), "INVALID DATE")
```
Note: Clubforce exports DOB as DD-MM-YYYY (dashes). SUBSTITUTE converts to DD/MM/YYYY before DATEVALUE parses it.

**Birth Year (MASTER col C):**
```
=IF(ISNUMBER(B2), YEAR(B2), "ERROR")
```

**Age Group (MASTER col D):**
```
=IFS(
  AND(C2>=2020,C2<=2021),"U6",
  AND(C2>=2018,C2<=2019),"U8",
  AND(C2>=2016,C2<=2017),"U10",
  AND(C2>=2014,C2<=2015),"U12",
  AND(C2>=2012,C2<=2013),"U14",
  AND(C2>=2010,C2<=2011),"U16",
  AND(C2>=2008,C2<=2009),"U18",
  TRUE,"UNASSIGNED"
)
```

**Gender (MASTER col E — standardise):**
```
=IF(LOWER(TRIM(RAW_DATA!H2))="male","Boys",IF(LOWER(TRIM(RAW_DATA!H2))="female","Girls","UNKNOWN"))
```
Note: Confirm exact values used by Clubforce (Male/Female, Boy/Girl, m/f, etc.)

**Group Tag (MASTER col F):**
```
=D2&" "&E2
```

**Guardian Name (MASTER col G):**
```
=PROPER(TRIM(RAW_DATA!I2&" "&RAW_DATA!J2))
```

**Contact Email (MASTER col H):**
```
=LOWER(TRIM(RAW_DATA!L2))
```

**Contact Number (MASTER col I — strip formatting):**
```
=SUBSTITUTE(SUBSTITUTE(SUBSTITUTE(SUBSTITUTE(TRIM(RAW_DATA!M2)," ",""),"-",""),"(",""),")","")
```

**Medical Conditions (MASTER col J):**
```
=RAW_DATA!V2
```
Column V = "Please list any medical conditions / injuries / allergies / special needs" — verify against actual export.

**Membership Status (MASTER col K):**
```
=LOWER(TRIM(RAW_DATA!D2))
```
Note: Clubforce exports status as lowercase "active". Normalise with LOWER+TRIM so filter comparisons are consistent.

**Plan Name (MASTER col L):**
```
=RAW_DATA!C2
```

**Age Group Sheet FILTER formula (row 2 of each sheet — adjust Group Tag per sheet):**
```
=FILTER(
  {MASTER!A:A, MASTER!B:B, MASTER!D:D, MASTER!G:G, MASTER!H:H, MASTER!I:I, MASTER!J:J},
  (MASTER!F:F = "U10 Boys") * (MASTER!K:K = "active")
)
```
Columns returned: Member Name, DOB, Age Group, Guardian Name, Contact Email, Contact Number, Medical Conditions.
Note: Membership status comparison uses lowercase "active" (confirmed from Clubforce export).

**Adult Plan Sheet FILTER formula — single plan (e.g. Non-Playing):**
```
=FILTER(
  {MASTER!A:A, MASTER!B:B, MASTER!L:L, MASTER!K:K},
  (MASTER!L:L = "Adult Non-Playing") * (MASTER!K:K = "active")
)
```

**Adult Plan Sheet FILTER formula — Senior Men (combined plans, filtered by gender):**
```
=FILTER(
  {MASTER!A:A, MASTER!B:B, MASTER!L:L, MASTER!K:K},
  ((MASTER!L:L = "Adult Player + Gym") + (MASTER!L:L = "Adult Student + Gym")) * (MASTER!E:E = "Boys") * (MASTER!K:K = "active")
)
```

**Adult Plan Sheet FILTER formula — Senior Women (combined plans, filtered by gender):**
```
=FILTER(
  {MASTER!A:A, MASTER!B:B, MASTER!L:L, MASTER!K:K},
  ((MASTER!L:L = "Adult Player + Gym") + (MASTER!L:L = "Adult Student + Gym")) * (MASTER!E:E = "Girls") * (MASTER!K:K = "active")
)
```
Columns returned: Member Name, DOB, Plan Name, Membership Status.

---

### Workflow — Each Registration Cycle

1. Export full member list from Clubforce (CSV or Excel)
2. Open Google Sheet → go to RAW_DATA tab
3. Select all → Delete → Paste fresh export (headers + all rows)
4. MASTER and all age group sheets update automatically
5. Review MASTER for any "UNASSIGNED" age groups or flagged DOB errors
6. To share with a coach:
   - Navigate to the relevant age group sheet (e.g. U10 Boys)
   - File → Download → PDF document
   - In PDF settings: select "Current sheet", fit to page width, include sheet name as header
   - Email PDF to lead coach for that group
   - Log the send in AUDIT_LOG
   - Note: PDF is a point-in-time snapshot — re-export when data changes

---

### GDPR Notes
- The Google Sheet workbook remains private (registrar only — do not share the workbook)
- Coach PDFs contain minimum data needed: name, DOB, guardian contact, medical
- Medical conditions are included under duty-of-care justification
- PDFs sent to coaches should be noted in AUDIT_LOG (coach name, age group, date sent)
- Do not include: address, consent history, member codes, financial info

---

### AUDIT_LOG Tab
Simple table the registrar manually updates when sending PDFs:

| Date Sent | Age Group | Coach Name | Coach Email | Notes |
|---|---|---|---|---|

---

### Workbook Setup Checklist

- [ ] Create new Google Sheet: `Lomans Lions Registrations 2026`
- [ ] Create tabs: RAW_DATA, MASTER, SUMMARY, NEEDS REVIEW, AUDIT_LOG, U6 Boys, U6 Girls, U8 Boys, U8 Girls, U10 Boys, U10 Girls, U12 Boys, U12 Girls, U14 Boys, U14 Girls, U16 Boys, U16 Girls, U18 Boys, U18 Girls, Non-Playing, Senior Men, Senior Women, Senior Active, Gym, Dads & Lads, G4MO
- [ ] RAW_DATA: paste Clubforce headers in row 1, freeze row 1
- [ ] MASTER: enter formulas in row 2, extend down, freeze row 1
- [ ] Each youth age group sheet: add headers in row 1, enter FILTER formula in row 2, freeze row 1
- [ ] Each adult plan sheet: add headers in row 1, enter FILTER formula in row 2, freeze row 1
- [ ] AUDIT_LOG: add column headers
- [ ] Verify Share settings — workbook must NOT be shared with anyone

---

### Verification Steps

1. Paste 5–10 anonymised test rows into RAW_DATA → confirm MASTER derives all columns correctly
2. Check at least one player per age group appears in the correct age group sheet
3. Confirm a player with "pending" status does NOT appear in age group sheets
4. Confirm a player outside U6–U18 birth year range shows "UNASSIGNED" and appears in NEEDS REVIEW
5. Confirm a player with an unrecognised gender value shows "UNKNOWN" and appears in NEEDS REVIEW
6. Confirm SUMMARY totals reconcile: Total Youth + Total Adults + UNASSIGNED = Total Active
7. Confirm NEEDS REVIEW shows "No issues found" once all test data is clean
8. Confirm medical conditions field passes through correctly
9. Export a test age group sheet as PDF — verify layout is clean and only approved columns are visible
10. Confirm workbook is NOT shared with anyone (check Share settings)

---

### Open Items to Confirm on First Run

- Membership status confirmed as lowercase "active" ✓ (formulas updated accordingly)
- Gender values confirmed as "Male" / "Female" ✓ (LOWER() comparison handles it)
- DOB format confirmed as DD-MM-YYYY (dashes) ✓ (SUBSTITUTE formula handles conversion)
- Column index for Medical Conditions confirmed as col V ✓
- Adult members (e.g. born 1997) will appear as UNASSIGNED — expected, filter to youth only
- Any players pre-2008 or post-2021 will show UNASSIGNED — review these on each import
