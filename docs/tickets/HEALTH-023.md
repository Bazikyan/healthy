# HEALTH-023 — CSV data export

| Field | Value |
|-------|-------|
| **Epic** | E5 — Dashboard, history, export |
| **Priority** | P2 |
| **Estimate** | S (~2–3h) |
| **Status** | backlog |
| **Spec** | [05-dashboard-history.md](../specs/05-dashboard-history.md) |
| **Depends on** | HEALTH-010, HEALTH-012 |
| **Blocks** | — |

## User story

As a user, I want to export my data, so that I own a copy of my logs outside the app.

## Summary

Download CSV from settings with weight and food log rows.

## Technical approach

### Controller: `ExportController@download`

`GET /settings/export` → `StreamedResponse` with `Content-Disposition: attachment`

### Exporter: `App\Services\DataExportService`

**weight.csv columns:**

`date,weight_kg,note`

**food_log.csv columns:**

`date,meal_type,name,brand,quantity,quantity_unit,calories,protein_g,carbs_g,fat_g,source`

Option: single ZIP with both files using `ZipArchive`.

### UI

Link on settings profile page: “Download my data”

### Implementation tasks

- [ ] ExportService streaming rows (chunked query for large datasets)
- [ ] Route + controller
- [ ] Feature test: assert CSV headers and row count

## Acceptance criteria

- [ ] Export contains only authenticated user's rows
- [ ] UTF-8 CSV with header row
- [ ] Filename `healthy-export-2026-05-15.csv` or `.zip`
- [ ] Guest cannot access

## Definition of done

- [ ] Test passes; manual open in Excel/Numbers OK
