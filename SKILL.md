---
name: pbi-tester
description: "Static quality analysis of a Power BI Project (PBIP/PBIR/TMDL). Runs 50+ checks across 10 categories without requiring PBI Desktop, Power BI Service, or any external tools. Produces a pass/fail report with actionable findings. Use when user says: 'test my dashboard', 'check PBI quality', 'run PBI tester', 'validate PBIP', 'audit dashboard', 'static analysis', 'quality check', 'lint my report', or provides a .pbip or PBIP folder path with quality check intent."
---

# PBI Tester

## Overview

Static PBIR/TMDL quality analysis — no external tools, no PBI Desktop, no Power BI Service connection required. Reads the raw JSON/TMDL/PBIR files directly and runs 50+ checks across 10 categories.

Trigger phrases: `test my dashboard`, `check PBI quality`, `run PBI tester`, `validate PBIP`, `audit dashboard`, `static analysis`, `quality check`, `lint my report`, `PBI tester`

---

## Workflow

### 1. Locate the PBIP

Accept either:
- A `.pbip` file path
- A folder path containing a `.pbip` file

Resolve the `.Report/` and `.SemanticModel/` subfolders from the project root.

### 2. Run all 50+ checks

Execute checks across all 10 categories below. For each check, record:
- Category
- Check name
- Status: ✅ PASS / ❌ FAIL / ⚠️ WARN / ℹ️ INFO
- Finding (specific detail if FAIL or WARN)
- Recommendation (what to fix)

---

## Check Categories

### 1. Layout & Visual Design (10 checks)

| # | Check | PASS condition |
|---|---|---|
| L1 | Page count | 1–15 pages (WARN if > 15) |
| L2 | Hidden pages | No hidden pages without a drillthrough purpose |
| L3 | Visual count per page | ≤ 25 visuals per page (WARN if > 20) |
| L4 | Overlapping visuals | No visuals with identical position coordinates |
| L5 | Canvas size consistency | All pages use the same canvas width/height |
| L6 | Blank titles | No visible visuals with empty or whitespace-only title |
| L7 | Tooltips enabled | Table/matrix visuals have tooltips enabled |
| L8 | Drillthrough pages | Drillthrough pages have at least one drillthrough field configured |
| L9 | Mobile layout | If mobile layout exists, all key visuals are included |
| L10 | Bookmark count | WARN if > 20 bookmarks (navigation complexity) |

### 2. Text Quality (8 checks)

| # | Check | PASS condition |
|---|---|---|
| T1 | Title casing | Visual titles use Title Case or Sentence case (not ALL CAPS) |
| T2 | Title length | Visual titles ≤ 80 characters |
| T3 | Axis label clarity | Axis titles present on all chart visuals |
| T4 | Legend labels | Legends have non-default labels (not "Legend") |
| T5 | Slicer headers | Slicers have descriptive header text |
| T6 | Placeholder text | No "Sample Text" or "Lorem ipsum" in any title/subtitle |
| T7 | Subtitle present | Report-level subtitle/description field is populated |
| T8 | Alt text | WARN if alt text is empty on any visual (accessibility) |

### 3. Format Strings (6 checks)

| # | Check | PASS condition |
|---|---|---|
| F1 | Currency columns | Columns with dollar/currency signals have `$#,0.00` or `$#,0` format |
| F2 | Percentage columns | Columns with pct/rate/ratio signals have `0.0%` or `0%` format |
| F3 | Date columns | Date columns have an explicit format string (not auto) |
| F4 | Integer counts | Count/headcount columns use `#,0` or `#,##0` (no decimals) |
| F5 | Blank format strings | No measure has an empty format string |
| F6 | Locale consistency | All format strings use the same decimal/thousands separators |

### 4. Accessibility (5 checks)

| # | Check | PASS condition |
|---|---|---|
| A1 | Colour contrast | No white text on light background or black text on dark background (heuristic check) |
| A2 | Alt text on images | Image visuals have alt text |
| A3 | Tab order | Tab order is configured (not default sequential) |
| A4 | Focus trap | No visuals overlap the reading order flow |
| A5 | Font size | No text smaller than 9pt in any visible visual title |

### 5. Design Consistency (6 checks)

| # | Check | PASS condition |
|---|---|---|
| D1 | Theme applied | A non-default theme JSON is applied |
| D2 | Font consistency | ≤ 3 distinct font families used across all visuals |
| D3 | Colour palette | ≤ 8 distinct hex colours used in visual fills |
| D4 | Background fill | All pages use a consistent background colour |
| D5 | Border consistency | Border style (on/off, width, radius) is consistent across card-type visuals |
| D6 | Padding consistency | Padding values consistent within the same visual type |

### 6. TMDL Model Quality (7 checks)

| # | Check | PASS condition |
|---|---|---|
| M1 | Table descriptions | All tables have a non-empty description |
| M2 | Column descriptions | WARN if > 20% of visible columns lack descriptions |
| M3 | Measure descriptions | WARN if any measure has no description |
| M4 | Hidden unused columns | No columns marked `isHidden` that are never referenced by a measure or visual |
| M5 | Calculated columns | WARN if calculated columns exist (prefer measures) |
| M6 | Format string on measures | All measures have an explicit format string |
| M7 | Naming conventions | Tables, columns, measures use Title Case or consistent snake_case (no mix) |

### 7. Relationships (5 checks)

| # | Check | PASS condition |
|---|---|---|
| R1 | Bidirectional relationships | No bidirectional cross-filter relationships (WARN — can cause ambiguity) |
| R2 | Inactive relationships | No inactive relationships (or document why they exist) |
| R3 | Many-to-many | WARN if any many-to-many relationships exist |
| R4 | Orphan tables | No tables with zero relationships (unless intentional parameter table) |
| R5 | Date table marked | A date table is marked as date table in the model |

### 8. Security (4 checks)

| # | Check | PASS condition |
|---|---|---|
| S1 | RLS roles defined | At least one Row Level Security role is defined (WARN if none — may be intentional) |
| S2 | RLS test coverage | If RLS roles exist, they have at least one DAX filter rule |
| S3 | Sensitive column names | WARN if any column name matches: `password`, `ssn`, `dob`, `salary`, `bank_account` |
| S4 | Direct query credentials | If DirectQuery source detected, WARN that credentials are not stored in PBIP |

### 9. Performance Signals (5 checks)

| # | Check | PASS condition |
|---|---|---|
| P1 | Measure complexity | WARN if any measure DAX contains > 10 nested function calls |
| P2 | CALCULATE nesting | WARN if CALCULATE is nested > 3 levels |
| P3 | ALL() usage | WARN if ALL() without a specific column is used on large tables |
| P4 | Large M queries | WARN if any M query exceeds 200 lines |
| P5 | Aggregations | INFO if no explicit aggregations are defined (opportunity) |

### 10. Cross-Layer Consistency (4 checks)

| # | Check | PASS condition |
|---|---|---|
| X1 | Unused measures | WARN if measures defined in the model are not used in any visual |
| X2 | Visual references valid | All column/measure references in visuals exist in the semantic model |
| X3 | Page filter references | All page-level filters reference valid fields |
| X4 | Report filter references | All report-level filters reference valid fields |

---

## Output Format

### Summary Header

```
PBI Tester Report
Dashboard: <dashboard name>
Path: <pbip path>
Date: <today>

Results: XX/54 PASS | XX WARN | XX FAIL | XX INFO
```

### Results Table

```
| # | Category | Check | Status | Finding |
|---|---|---|---|---|
| L1 | Layout | Page count | ✅ PASS | 8 pages |
| T1 | Text | Title casing | ❌ FAIL | "EXECUTIVE SUMMARY" on page 2 — use Title Case |
| M1 | TMDL | Table descriptions | ⚠️ WARN | 3/5 tables missing descriptions |
```

### Action Items

Group all FAIL and WARN items:

```
## Action Items

### ❌ FAIL (must fix)
1. [T1] Title casing — "EXECUTIVE SUMMARY" on page 2. Change to "Executive Summary".
2. [F5] Blank format strings — Measure "Total Cost" has no format string. Add `$#,0.00`.

### ⚠️ WARN (recommended)
1. [M1] Table descriptions — Fact, Dim_Date, Dim_Region missing descriptions.
2. [R1] Bidirectional relationship — Fact ↔ Dim_Product is bidirectional. Review necessity.
```

---

## Rules

- **No external tools required** — reads PBIP files directly (JSON, TMDL, PBIR format).
- **Never modify the PBIP** — read-only analysis only.
- **Show full path** for every finding so the user knows exactly where to look.
- **FAIL items are blockers** for production deployment; WARN items are recommendations.
- If the PBIP uses PBIR-Legacy format (single `report.json`), apply the same checks but note reduced granularity for layout checks.

## TMDL Direct-Edit Constraints (CRITICAL — do not violate)

When asked to *fix* findings after running this tester, the following TMDL edits will **break PBI Desktop** and must never be attempted:

| What NOT to do | Why | Correct fix |
|---|---|---|
| Add `description:` to table blocks | `description` is not a supported TMDL property for tables in PBI Desktop | Set via PBI Desktop UI: Model View → select table → Properties pane |
| Add `description:` to measure blocks | Same — unsupported in current TMDL parser | Set via PBI Desktop UI: Model View → select measure → Properties pane |
| Add `description:` to column blocks | Same | Set via PBI Desktop UI: Model View → select column → Properties pane |
| Add `isDateTable` to table block | Requires specific TMDL syntax confirmed by round-tripping through PBI Desktop | Use PBI Desktop: Table Tools → Mark as Date Table |

**For M1/M2/M3 check findings:** always recommend "manual fix required — use PBI Desktop UI" and never attempt to write `description:` lines into .tmdl files directly.
