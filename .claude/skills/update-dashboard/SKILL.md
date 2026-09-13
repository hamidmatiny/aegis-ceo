---
name: update-dashboard
description: Refresh dashboard.yaml with current metrics from the trajectory/CVE/GitHub history files
allowed-tools: Read, Write, Edit, Bash, Glob, Grep
user-invocable: true
metadata:
  version: "1.0"
  created: 2026-09-12
  author: aegis-ceo
---

# Update Dashboard

Refresh `dashboard.yaml` with current metrics gathered from this agent's local history files and recent skill outputs.

## Process

### Step 1: Gather Metrics

Read the agent's data sources to collect current values:
- `trajectory-history.json` — latest MRR, signups, uptime, open security findings, and the timestamp of the last review
- `cve-watch-history.json` — count of new CVEs found in the last 7 days
- Most recent `/github-pulse` output (from chat context or a Trinity report, if deployed) — open PR/issue counts
- Most recent `/strategy-brief` output — count of open escalation-queue items

If a source file doesn't exist yet, leave the corresponding widget at `—` rather than inventing a value.

### Step 2: Update Dashboard

Read `dashboard.yaml`, update widget values with fresh data:
- Update the `updated` timestamp to now (ISO 8601 UTC)
- Update metric values from gathered data
- Update the "Recent Findings" list with the last few CVE findings or escalation items
- Set the "Agent Status" color to `yellow` if `corp-orchestrator` or GitHub credentials are missing/failing, `green` otherwise

Write the updated `dashboard.yaml`.

### Step 3: Publish a KPI snapshot report (Trinity)

If the `mcp__trinity__report` tool is available (i.e. running on Trinity), also publish the same headline numbers as a report so they accumulate as history alongside the live snapshot:

- `report_type`: `aegis_ceo.kpi_snapshot`
- `display_hint`: `kpi`
- `payload`: `{ "tiles": [ {"label": "MRR", "value": "...", "unit": "USD"}, {"label": "Signups", "value": "..."}, {"label": "Open security findings", "value": "..."}, {"label": "Open escalations", "value": "..."} ] }`, built from the same values just written to the dashboard.

Skip this step silently if the tool isn't available — the dashboard refresh above still succeeds.

### Step 4: Confirm

Report what was updated:
```
Dashboard refreshed:
- MRR: [old] → [new]
- Open security findings: [old] → [new]
- Last updated: [timestamp]
```

Note: On Trinity remote, the dashboard path is `/home/developer/dashboard.yaml`.

## Outputs

- Updated `dashboard.yaml` with current metrics
