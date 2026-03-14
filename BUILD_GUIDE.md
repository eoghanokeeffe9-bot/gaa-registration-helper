# Google Sheets Build Guide
## Lomans Lions Registrations 2026

Work through each step in order. All formulas are Google Sheets syntax — paste exactly as written.

---

## STEP 1 — Create the Workbook

1. Go to sheets.google.com → click **Blank**
2. Rename the file: `Lomans Lions Registrations 2026`
3. Create the following tabs (right-click tab → Insert sheet, or click the + button):

**Tab names in order:**
```
RAW_DATA
MASTER
AUDIT_LOG
U6 Boys
U6 Girls
U8 Boys
U8 Girls
U10 Boys
U10 Girls
U12 Boys
U12 Girls
U14 Boys
U14 Girls
U16 Boys
U16 Girls
U18 Boys
U18 Girls
Non-Playing
Adult Player
Adult Student
Senior Active
Gym
Dads & Lads
G4MO
```

---

## STEP 2 — RAW_DATA Tab

1. Click the **RAW_DATA** tab
2. Click cell **A1**
3. Paste the following headers across row 1 (copy the line below and paste into A1 — Google Sheets will split across columns):

```
Registered date & time	Program name	Plan name	Membership status	Member first name	Member last name	DOB	Gender	Guardian first name	Guardian last name	Template	Contact email	Contact number	Member address	Member county	Member country	Consent to be contacted by club	Preferred method of communication	Are you a player?	Member code	Please choose your code	Please list any medical conditions / injuries / allergies / special needs the club need to be aware of	Relationship to member	I consent to allow photo/videos to be taken of me/my child for official club use and/or promotional purposes.	Would you be interested in volunteering for the club?	I consent for member information to be retained by the Club and submitted to the relevant Governing Body for administrative and statistical purposes for such period as the membership subsists	GAA and LGFA Data policy	Were you registered as a member of St Lomans Mullingar GAA Club last year?	Please include your lotto lines here (eg 1,2,3,4)	Where did you hear about Lomans Lions?	What size Lomans Lions Hoodie would you like for your child?	What school / pre-school does your child attend?
```

4. **View → Freeze → 1 row**
5. Leave everything else blank — this tab is paste-only

---

## STEP 3 — MASTER Tab

### Row 1 — Headers
Click cell **A1** on the MASTER tab and enter these headers one per column:

| Cell | Header |
|---|---|
| A1 | Member Name |
| B1 | DOB |
| C1 | Birth Year |
| D1 | Age Group |
| E1 | Gender |
| F1 | Group Tag |
| G1 | Guardian Name |
| H1 | Contact Email |
| I1 | Contact Number |
| J1 | Medical Conditions |
| K1 | Membership Status |
| L1 | Plan Name |

### Row 2 — Formulas
Enter each formula into the cell shown. The `IF(RAW_DATA!E2="","", ...)` wrapper keeps blank rows clean.

**A2 — Member Name:**
```
=IF(RAW_DATA!E2="","",PROPER(TRIM(RAW_DATA!E2&" "&RAW_DATA!F2)))
```

**B2 — DOB:**
```
=IF(RAW_DATA!G2="","",IFERROR(DATEVALUE(SUBSTITUTE(RAW_DATA!G2,"-","/")), "INVALID DATE"))
```
After entering, format column B as: Format → Number → Custom date/time → `DD/MM/YYYY`

**C2 — Birth Year:**
```
=IF(B2="","",IF(ISNUMBER(B2),YEAR(B2),"ERROR"))
```

**D2 — Age Group:**
```
=IF(C2="","",IFS(AND(C2>=2020,C2<=2021),"U6",AND(C2>=2018,C2<=2019),"U8",AND(C2>=2016,C2<=2017),"U10",AND(C2>=2014,C2<=2015),"U12",AND(C2>=2012,C2<=2013),"U14",AND(C2>=2010,C2<=2011),"U16",AND(C2>=2008,C2<=2009),"U18",TRUE,"UNASSIGNED"))
```

**E2 — Gender:**
```
=IF(RAW_DATA!H2="","",IF(LOWER(TRIM(RAW_DATA!H2))="male","Boys",IF(LOWER(TRIM(RAW_DATA!H2))="female","Girls","UNKNOWN")))
```

**F2 — Group Tag:**
```
=IF(D2="","",D2&" "&E2)
```

**G2 — Guardian Name:**
```
=IF(RAW_DATA!I2="","",PROPER(TRIM(RAW_DATA!I2&" "&RAW_DATA!J2)))
```

**H2 — Contact Email:**
```
=IF(RAW_DATA!L2="","",LOWER(TRIM(RAW_DATA!L2)))
```

**I2 — Contact Number:**
```
=IF(RAW_DATA!M2="","",SUBSTITUTE(SUBSTITUTE(SUBSTITUTE(SUBSTITUTE(TRIM(RAW_DATA!M2)," ",""),"-",""),"(",""),")",""))
```

**J2 — Medical Conditions:**
```
=RAW_DATA!V2
```

**K2 — Membership Status:**
```
=IF(RAW_DATA!D2="","",LOWER(TRIM(RAW_DATA!D2)))
```

**L2 — Plan Name:**
```
=IF(RAW_DATA!C2="","",RAW_DATA!C2)
```

### Extend Formulas Down
1. Select **A2:L2**
2. Copy (Ctrl+C)
3. Select **A3:L500**
4. Paste (Ctrl+V)

This covers up to 499 members. Extend further if needed.

### Freeze Row 1
**View → Freeze → 1 row**

---

## STEP 4 — Youth Age Group Sheets

For each sheet below:
1. Click the tab
2. Add headers in row 1 (A1–G1): `Member Name | DOB | Age Group | Guardian Name | Contact Email | Contact Number | Medical Conditions`
3. In cell **A2**, paste the FILTER formula shown
4. **View → Freeze → 1 row**

| Tab | Formula for A2 |
|---|---|
| U6 Boys | `=IFERROR(FILTER({MASTER!A:A,MASTER!B:B,MASTER!D:D,MASTER!G:G,MASTER!H:H,MASTER!I:I,MASTER!J:J},(MASTER!F:F="U6 Boys")*(MASTER!K:K="active")),"No results")` |
| U6 Girls | `=IFERROR(FILTER({MASTER!A:A,MASTER!B:B,MASTER!D:D,MASTER!G:G,MASTER!H:H,MASTER!I:I,MASTER!J:J},(MASTER!F:F="U6 Girls")*(MASTER!K:K="active")),"No results")` |
| U8 Boys | `=IFERROR(FILTER({MASTER!A:A,MASTER!B:B,MASTER!D:D,MASTER!G:G,MASTER!H:H,MASTER!I:I,MASTER!J:J},(MASTER!F:F="U8 Boys")*(MASTER!K:K="active")),"No results")` |
| U8 Girls | `=IFERROR(FILTER({MASTER!A:A,MASTER!B:B,MASTER!D:D,MASTER!G:G,MASTER!H:H,MASTER!I:I,MASTER!J:J},(MASTER!F:F="U8 Girls")*(MASTER!K:K="active")),"No results")` |
| U10 Boys | `=IFERROR(FILTER({MASTER!A:A,MASTER!B:B,MASTER!D:D,MASTER!G:G,MASTER!H:H,MASTER!I:I,MASTER!J:J},(MASTER!F:F="U10 Boys")*(MASTER!K:K="active")),"No results")` |
| U10 Girls | `=IFERROR(FILTER({MASTER!A:A,MASTER!B:B,MASTER!D:D,MASTER!G:G,MASTER!H:H,MASTER!I:I,MASTER!J:J},(MASTER!F:F="U10 Girls")*(MASTER!K:K="active")),"No results")` |
| U12 Boys | `=IFERROR(FILTER({MASTER!A:A,MASTER!B:B,MASTER!D:D,MASTER!G:G,MASTER!H:H,MASTER!I:I,MASTER!J:J},(MASTER!F:F="U12 Boys")*(MASTER!K:K="active")),"No results")` |
| U12 Girls | `=IFERROR(FILTER({MASTER!A:A,MASTER!B:B,MASTER!D:D,MASTER!G:G,MASTER!H:H,MASTER!I:I,MASTER!J:J},(MASTER!F:F="U12 Girls")*(MASTER!K:K="active")),"No results")` |
| U14 Boys | `=IFERROR(FILTER({MASTER!A:A,MASTER!B:B,MASTER!D:D,MASTER!G:G,MASTER!H:H,MASTER!I:I,MASTER!J:J},(MASTER!F:F="U14 Boys")*(MASTER!K:K="active")),"No results")` |
| U14 Girls | `=IFERROR(FILTER({MASTER!A:A,MASTER!B:B,MASTER!D:D,MASTER!G:G,MASTER!H:H,MASTER!I:I,MASTER!J:J},(MASTER!F:F="U14 Girls")*(MASTER!K:K="active")),"No results")` |
| U16 Boys | `=IFERROR(FILTER({MASTER!A:A,MASTER!B:B,MASTER!D:D,MASTER!G:G,MASTER!H:H,MASTER!I:I,MASTER!J:J},(MASTER!F:F="U16 Boys")*(MASTER!K:K="active")),"No results")` |
| U16 Girls | `=IFERROR(FILTER({MASTER!A:A,MASTER!B:B,MASTER!D:D,MASTER!G:G,MASTER!H:H,MASTER!I:I,MASTER!J:J},(MASTER!F:F="U16 Girls")*(MASTER!K:K="active")),"No results")` |
| U18 Boys | `=IFERROR(FILTER({MASTER!A:A,MASTER!B:B,MASTER!D:D,MASTER!G:G,MASTER!H:H,MASTER!I:I,MASTER!J:J},(MASTER!F:F="U18 Boys")*(MASTER!K:K="active")),"No results")` |
| U18 Girls | `=IFERROR(FILTER({MASTER!A:A,MASTER!B:B,MASTER!D:D,MASTER!G:G,MASTER!H:H,MASTER!I:I,MASTER!J:J},(MASTER!F:F="U18 Girls")*(MASTER!K:K="active")),"No results")` |

---

## STEP 5 — Adult Plan Sheets

For each sheet below:
1. Click the tab
2. Add headers in row 1 (A1–D1): `Member Name | DOB | Plan Name | Membership Status`
3. In cell **A2**, paste the FILTER formula shown
4. **View → Freeze → 1 row**

| Tab | Formula for A2 |
|---|---|
| Non-Playing | `=IFERROR(FILTER({MASTER!A:A,MASTER!B:B,MASTER!L:L,MASTER!K:K},(MASTER!L:L="Adult Non-Playing")*(MASTER!K:K="active")),"No results")` |
| Adult Player | `=IFERROR(FILTER({MASTER!A:A,MASTER!B:B,MASTER!L:L,MASTER!K:K},(MASTER!L:L="Adult Player + Gym")*(MASTER!K:K="active")),"No results")` |
| Adult Student | `=IFERROR(FILTER({MASTER!A:A,MASTER!B:B,MASTER!L:L,MASTER!K:K},(MASTER!L:L="Adult Student + Gym")*(MASTER!K:K="active")),"No results")` |
| Senior Active | `=IFERROR(FILTER({MASTER!A:A,MASTER!B:B,MASTER!L:L,MASTER!K:K},(MASTER!L:L="Senior Active Group Membership")*(MASTER!K:K="active")),"No results")` |
| Gym | `=IFERROR(FILTER({MASTER!A:A,MASTER!B:B,MASTER!L:L,MASTER!K:K},(MASTER!L:L="Gym Membership")*(MASTER!K:K="active")),"No results")` |
| Dads & Lads | `=IFERROR(FILTER({MASTER!A:A,MASTER!B:B,MASTER!L:L,MASTER!K:K},(MASTER!L:L="Dads & Lads Membership")*(MASTER!K:K="active")),"No results")` |
| G4MO | `=IFERROR(FILTER({MASTER!A:A,MASTER!B:B,MASTER!L:L,MASTER!K:K},(MASTER!L:L="Gaelic 4 Mothers & Others Membership")*(MASTER!K:K="active")),"No results")` |

---

## STEP 6 — AUDIT_LOG Tab

Click the **AUDIT_LOG** tab and add these headers in row 1:

| A1 | B1 | C1 | D1 | E1 |
|---|---|---|---|---|
| Date Sent | Age Group | Coach Name | Coach Email | Notes |

Freeze row 1. Fill in manually each time a PDF is sent to a coach.

---

## STEP 7 — Verification

Once you have the sheet built, paste these 3 test rows into RAW_DATA (rows 2–4) and confirm the results in MASTER and the age group sheets:

**Test row 1 — Active U10 Boy (should appear in U10 Boys sheet):**
```
2026-01-10 09:00:00	Membership 2026	Youth Player	active	Ciarán	Murphy	15-06-2016	Male	Seán	Murphy	GAA template	sean.murphy@gmail.com	0851234567	123 Main St, Mullingar	Westmeath	Ireland	Yes	Email	Yes		Football		None
```

**Test row 2 — Pending U12 Girl (should NOT appear in any age group sheet):**
```
2026-01-11 10:00:00	Membership 2026	Youth Player	pending	Aoife	Kelly	22-03-2014	Female	Mary	Kelly	GAA template	mary.kelly@gmail.com	0879876543	45 Oak Ave, Mullingar	Westmeath	Ireland	Yes	Email	Yes		Football		Asthma
```

**Test row 3 — Active Adult Non-Playing (should appear in Non-Playing sheet, UNASSIGNED in MASTER):**
```
2026-02-01 11:00:00	Membership 2026	Adult Non-Playing	active	Deirdre	Brennan	04-11-1985	Female			GAA template	deirdre.b@gmail.com	0861112222	67 Park Rd, Mullingar	Westmeath	Ireland	Yes	Email	No		Football
```

**Expected results:**
- Row 1 → MASTER shows Age Group = U10, Group Tag = U10 Boys → appears in U10 Boys sheet ✓
- Row 2 → MASTER shows status = pending → does NOT appear in U10 Girls sheet ✓
- Row 3 → MASTER shows Age Group = UNASSIGNED → appears in Non-Playing adult sheet ✓

---

## STEP 8 — Lock Down Access

1. Click **Share** (top right)
2. Confirm no one else has access
3. Do not share the workbook — coaches receive PDFs only

---

## PDF Export (Youth Sheets Only)

1. Navigate to the relevant age group sheet (e.g. U10 Boys)
2. **File → Download → PDF document**
3. Settings: Current sheet · Fit to page width · Portrait
4. Email to lead coach
5. Log in AUDIT_LOG: date, age group, coach name, coach email
