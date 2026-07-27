---
name: Export team usage metrics
description: Export a Shortcut team or organization's usage metrics as JSON or CSV over a date range for reporting and finance.
api: openapi/fundamental-research-labs-openapi-original.json
operations:
  - GET /api/v1/team/usage-export
---

# Export team usage metrics

Base URL: `https://api.shortcut.ai`. Auth: `Authorization: Bearer sc-xxx`. The API
key must belong to a user with usage-metrics access for the requested team/org.

## Steps

1. **Call the export** — `GET /api/v1/team/usage-export` with query parameters:
   - `team_id` — team or organization ID (required for API-key integrations).
   - `period` — `day` | `week` | `month` (default) | `60days` | `90days` | `all`.
   - `start_date` / `end_date` — `YYYY-MM-DD`; must be supplied together and take
     precedence over `period`.
   - `timezone` — IANA timezone (default `UTC`).
   - `format` — `json` (default) | `csv`.
   - `report` — CSV only: `summary` (per-member totals) or `daily` (usage by day).
   - `group_by` — JSON only: `platform` to add per-platform breakdowns.
2. **Consume the response** — JSON object or CSV per `format`.

## Rules

- In-app **Usage Metrics -> Programmatic export** shows a prefilled command with
  your `team_id`; use it as the starting point.
- `start_date`/`end_date` override `period` when both are present.
- Only API-key auth is accepted for this endpoint.
