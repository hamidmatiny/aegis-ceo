---
name: daily-trajectory-review
description: Pull AEGIS's real trajectory data from corp-orchestrator and push a real summary to Hamid via Slack (and WhatsApp once approved) — never invented numbers
allowed-tools: Read, Write, Bash, WebFetch, mcp__trinity__list_channel_groups, mcp__trinity__send_group_message, mcp__trinity__send_message, mcp__trinity__list_reports, mcp__trinity__report
user-invocable: true
metadata:
  version: "1.1"
  created: 2026-09-12
  updated: 2026-09-13
  author: aegis-ceo
---

# Daily Trajectory Review

## Purpose

Give Hamid an honest, numbers-first snapshot of how AEGIS is actually doing today — pulled from `corp-orchestrator`'s real BEV Trajectory API, never estimated or inferred — and **push that same real summary to him on Slack** (WhatsApp only if that channel is already configured and approved).

## Process

### Step 1: Check Credentials

Verify `CORP_ORCHESTRATOR_API_URL` and `CORP_ORCHESTRATOR_API_TOKEN` are set:

```bash
[ -n "$CORP_ORCHESTRATOR_API_URL" ] && [ -n "$CORP_ORCHESTRATOR_API_TOKEN" ] && echo "configured" || echo "missing"
```

If missing, stop and say plainly: "I don't have a `corp-orchestrator` API token configured yet — I can't produce a real trajectory review without it. Run `/onboarding` to set it up." Do not fabricate numbers to fill the gap.

### Step 2: Pull Real Data

Call the live read-only endpoints (Bearer = `CORP_READONLY_TOKEN` / `CORP_ORCHESTRATOR_API_TOKEN`):

```bash
# Primary — CEO trajectory + signup chart
curl -sf -H "Authorization: Bearer $CORP_ORCHESTRATOR_API_TOKEN" \
  "$CORP_ORCHESTRATOR_API_URL/v1/bev/trajectory"

# Companion — live agent/task counts (same token, GET-only)
curl -sf -H "Authorization: Bearer $CORP_ORCHESTRATOR_API_TOKEN" \
  "$CORP_ORCHESTRATOR_API_URL/v1/bev/summary"
```

Canonical production base URL: `https://defenseaegis.org/api/corp`  
(so full trajectory URL is `https://defenseaegis.org/api/corp/v1/bev/trajectory`).

If either call fails (network, auth, unexpected shape), report the failure honestly — do not substitute a plausible-looking number.

### Step 3: Load Yesterday's Snapshot for Comparison

Read `trajectory-history.json` in the agent root if it exists (create it on first run) to compare today's numbers against the prior run — MRR/signups/agent/task deltas from whatever fields the live JSON actually contains. Never invent fields that aren't in the response.

### Step 4: Summarize

Build a short, direct summary from the **real JSON only**:

```
## Trajectory Review — [date]

- CEO registered: [yes/no]
- Agent status: [idle/running/…] · model [provider/model]
- Latest CEO report: [status] — [first ~400 chars of result, or "none yet"]
- Agents: [total] (idle/running/escalated/error from summary)
- Tasks completed today: [n]
- Open escalations: [n]
- Signups (14d chart): [chart.status] · [n] day-points

[One honest sentence on the real state — no softening empty reports or $0.]
```

If the CEO report body embeds MRR/uptime/security numbers, quote those; if not, say so explicitly rather than inventing them.

### Step 5: Persist for Next Comparison

Append today's pulled numbers (with timestamp) to `trajectory-history.json` so tomorrow's run has a real delta to compare against.

### Step 6: Deliver to Hamid (Slack first)

This is not optional once Trinity MCP is available — the point of the review is that it reaches him without opening a terminal.

1. Prefer **Slack channel** if bound:
   - Call `mcp__trinity__list_channel_groups` with `channel_type: "slack"`.
   - If at least one channel is returned, call `mcp__trinity__send_group_message` with that `chat_id`, `channel_type: "slack"`, and the **same real summary text** from Step 4 (not a placeholder, not "review complete").
2. Else try **Slack DM / proactive user message**:
   - Call `mcp__trinity__send_message` with `recipient_email: "hamidmatiny@gmail.com"`, `channel: "slack"`, and the same real summary.
3. If Slack fails (not connected, proactive consent off, rate-limited), say so plainly in chat — do not pretend the message was sent.
4. **WhatsApp**: only attempt if Hamid has already approved exposing Trinity for Twilio webhooks *and* a WhatsApp binding exists for this agent. Prefer `send_message` with `channel: "auto"` only when WhatsApp is known-ready; never invent a WhatsApp send path.

### Step 7: Publish a Report (Trinity UI)

If `mcp__trinity__report` is available, call `mcp__trinity__list_reports` first (`report_type: aegis_ceo.trajectory_review`, `hours: 24`) to avoid duplicates, then publish a `kpi`-hinted report whose tile values come from the **same live JSON**, never placeholders.

Skip silently if the tool isn't available.

## Outputs

- A direct trajectory summary in chat
- Updated `trajectory-history.json`
- A real Slack message to Hamid containing that same summary (when Slack is connected)
- A `kpi`-hinted Trinity report (when deployed)
