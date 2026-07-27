---
name: Generate a spreadsheet from a prompt
description: Use the Shortcut Platform API to turn a natural-language prompt into a generated Excel workbook, using the submit -> poll -> download flow.
api: openapi/fundamental-research-labs-openapi-original.json
operations:
  - GET /api/spreadsheets/verify
  - POST /api/spreadsheets
  - GET /api/spreadsheets/{runId}
  - GET /api/spreadsheets/{runId}/download
---

# Generate a spreadsheet from a prompt

Base URL: `https://api.shortcut.ai`. Authenticate every request with your API key
as a Bearer token: `Authorization: Bearer sc-xxx` (create one in Shortcut under
Settings -> API Keys).

## Steps

1. **Verify the API key** — `GET /api/spreadsheets/verify`. Confirms the key is
   valid and returns `webhookSecret` (needed only if you use webhook callbacks).
2. **Submit the job** — `POST /api/spreadsheets` with a JSON body:
   `{ "prompt": "Build a 3-statement model from these assumptions..." }`.
   Optional fields: `mode` (`action` writes the sheet, `ask` is read-only),
   `initFile` (a File ID), `contextFiles` (up to 5 File IDs), `timeoutSeconds`
   (default 1800, max 7200), `model`, `thinking`, `skills`, `webhookUrl`.
   The response returns `{ runId, status }` with status `queued` or `running`.
3. **Poll status** — `GET /api/spreadsheets/{runId}` until `status` is
   `completed`, `failed`, or `cancelled` (transient values: `queued`, `running`,
   `finalizing`). A completed job returns `downloadUrl`, `summary`, and `artifacts`.
4. **Download the result** — `GET /api/spreadsheets/{runId}/download` (or follow
   `downloadUrl`) to retrieve the binary `.xlsx` workbook.

## Rules

- **No idempotency key** — re-submitting the same prompt creates a new run;
  deduplicate on the client if needed.
- **Credits** — a `402 Payment Required` means insufficient credits (typically
  2-15 credits per message). Top up or upgrade the plan.
- **Rate limits** — 500 requests/min per API key; a `429` means back off and retry.
- **Errors** use the envelope `{ success: false, error, message }` (not RFC 9457).
- Prefer webhook completion (`webhookUrl`) over tight polling for long jobs; verify
  the `X-Shortcut-Signature: sha256=<hmac>` header with the webhook secret.
