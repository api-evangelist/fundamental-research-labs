---
name: Upload files and run an analysis with skills
description: Upload an initial Excel file plus context files, pick an agent skill, and run a Shortcut analysis job over them.
api: openapi/fundamental-research-labs-openapi-original.json
operations:
  - POST /api/spreadsheets/upload
  - GET /api/spreadsheets/skills
  - POST /api/spreadsheets
  - GET /api/spreadsheets/{runId}
---

# Upload files and run an analysis with skills

Base URL: `https://api.shortcut.ai`. Auth: `Authorization: Bearer sc-xxx`.

## Steps

1. **Upload each file** — `POST /api/spreadsheets/upload` (one call per file).
   Each returns `{ success: true, fileId }`. Max 100MB per file; a `413` means the
   file is too large. Keep the `fileId` values.
2. **List available skills** — `GET /api/spreadsheets/skills`. Each skill has a
   source-qualified `id` such as `default:commit`, `personal:my-skill`, or
   `team:{teamId}:team-skill`. Only these exact ids are accepted; older
   folder-name-only ids are rejected.
3. **Submit the job** — `POST /api/spreadsheets` with:
   `{ "prompt": "...", "initFile": "<fileId>", "contextFiles": ["<fileId>", ...],
   "skills": ["default:commit"] }`. Pass at most 5 context files; passing one
   skill is recommended (combining several may be unpredictable). Use
   `mode: "ask"` for read-only review without modifying the workbook.
4. **Poll status** — `GET /api/spreadsheets/{runId}` until terminal; read
   `summary` and `artifacts`, and download via
   `GET /api/spreadsheets/{runId}/download` or the artifacts endpoint.

## Rules

- Upload before submit — `initFile`/`contextFiles` take File IDs, not raw files.
- Skill ids are validated for shape and team authorization at submit time.
- Same error envelope, credit (`402`), and rate-limit (`429`) rules as the
  generate-spreadsheet skill.
