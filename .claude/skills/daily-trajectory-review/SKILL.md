---
name: daily-trajectory-review
description: Pull AEGIS's real trajectory data (MRR, signups, uptime, security findings) from corp-orchestrator and summarize what actually changed — never invented numbers
allowed-tools: Read, Write, Bash, WebFetch
user-invocable: true
metadata:
  version: "1.0"
  created: 2026-09-12
  author: aegis-ceo
---

# Daily Trajectory Review

## Purpose

Give Hamid an honest, numbers-first snapshot of how AEGIS is actually doing today — pulled from `corp-orchestrator`'s real Trajectory Report API, never estimated or inferred.

## Process

### Step 1: Check Credentials

Verify `CORP_ORCHESTRATOR_API_URL` and `CORP_ORCHESTRATOR_API_TOKEN` are set:

```bash
[ -n "$CORP_ORCHESTRATOR_API_URL" ] && [ -n "$CORP_ORCHESTRATOR_API_TOKEN" ] && echo "configured" || echo "missing"
```

If missing, stop and say plainly: "I don't have a `corp-orchestrator` API token configured yet — I can't produce a real trajectory review without it. Run `/onboarding` to set it up." Do not fabricate numbers to fill the gap.

### Step 2: Pull Real Data

Call the `corp-orchestrator` Trajectory Report API (read-only) — adjust the exact path once Hamid confirms the actual endpoint shape during onboarding:

```bash
curl -sf -H "Authorization: Bearer $CORP_ORCHESTRATOR_API_TOKEN" \
  "$CORP_ORCHESTRATOR_API_URL/trajectory/latest"
```

If the call fails (network error, auth error, 404, unexpected shape), report the failure honestly — do not substitute a plausible-looking number. Say exactly what failed and why you can't complete the review.

### Step 3: Load Yesterday's Snapshot for Comparison

Read `trajectory-history.json` in the agent root if it exists (create it on first run) to compare today's numbers against the prior run — MRR delta, signup delta, uptime trend, new vs. resolved security findings.

### Step 4: Summarize

Present a short, direct summary:

```
## Trajectory Review — [date]

- MRR: $[X] ([+/-Y] vs. yesterday)
- Signups: [X] ([+/-Y] vs. yesterday)
- Uptime: [X]% (last 24h)
- Security findings: [X] open ([+/-Y] vs. yesterday)

[One honest sentence on the real state — no softening a $0 or a regression.]
```

### Step 5: Persist for Next Comparison

Append today's pulled numbers (with timestamp) to `trajectory-history.json` so tomorrow's run has a real delta to compare against.

### Step 6: Publish a Report (Trinity)

If `mcp__trinity__report` is available, call `mcp__trinity__list_reports` first (`report_type: aegis_ceo.trajectory_review`, `hours: 24`) to confirm you're not duplicating today's review, then publish:

- `report_type`: `aegis_ceo.trajectory_review`
- `display_hint`: `kpi`
- `title`: `Trajectory review — [date]`
- `payload`: `{"tiles": [{"label": "MRR", "value": "$X", "unit": "USD"}, {"label": "Signups", "value": "X"}, {"label": "Uptime", "value": "X%"}, {"label": "Open security findings", "value": "X"}]}`

Skip silently if the tool isn't available or refuses for lacking an agent-scoped key.

## Outputs

- A direct trajectory summary in chat
- Updated `trajectory-history.json`
- A `kpi`-hinted Trinity report (when deployed)
