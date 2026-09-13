---
name: strategy-brief
description: Synthesize trajectory, CVE, and GitHub findings into a prioritized, honest recommendation and escalation queue for Hamid — ruthlessly weighted against real revenue, not busywork
allowed-tools: Read, Write, AskUserQuestion
user-invocable: true
metadata:
  version: "1.0"
  created: 2026-09-12
  author: aegis-ceo
---

# Strategy Brief

## Purpose

Turn the last period's real findings — trajectory, CVE watch, GitHub activity — into one honest, prioritized recommendation for Hamid. This is the synthesis skill: it does not gather new raw data itself, it reasons over what the other three skills already found.

## Process

### Step 1: Gather Recent Findings

Read the local history files if present: `trajectory-history.json`, `cve-watch-history.json`. If running on Trinity, prefer the durable record: call `mcp__trinity__list_reports` for `report_type` values `aegis_ceo.trajectory_review`, `aegis_ceo.cve_finding`, `aegis_ceo.github_pulse` over the last 7 days (`hours: 168`), then `mcp__trinity__get_report` on the most relevant ones for detail.

If none of these sources have any data yet, say so plainly: "I don't have enough history yet to write a real strategy brief — run the other three skills first." Do not synthesize from nothing.

### Step 2: Assess Against the North Star

The only question that matters: is AEGIS closer to real revenue ($0 → $1–2K → $10K MRR) than it was last period? Everything else is secondary. For each finding, ask:

- Does this move MRR, signups, or retention — directly or by removing a real blocker?
- Is this a genuine risk to the business (security, uptime, reputation) that would cost more to ignore than to fix now?
- Or is this activity that *looks* like progress but isn't — new infrastructure, refactors, or features with no connection to a paying customer?

Be willing to say a busy week produced zero real progress if that's true.

### Step 3: Identify What Needs Hamid's Decision

Anything that would expand this agent's own authority, another agent's scope, hiring the next department-agent, or granting write access to prod/Stripe/the repo goes into an explicit **escalation queue** — recommended, never decided here.

### Step 4: Write the Brief

```
## Strategy Brief — [date range]

**Bottom line:** [one honest sentence — did we move closer to real revenue this period, yes/no/unclear, and why]

### What actually happened
- [Real, sourced facts only — cite which skill/report each came from]

### Recommended priorities (next period)
1. [Highest-leverage real-revenue action]
2. [...]

### Escalation queue — needs your decision
- [Anything expanding scope/authority/access — framed as a recommendation, not a fait accompli]

### What I don't know
- [Anything you couldn't verify this period — be specific about the gap]
```

### Step 5: Confirm Before Escalating (Interactive Only)

When running interactively and the escalation queue is non-empty, use `AskUserQuestion` to let Hamid triage it now rather than burying it in the brief text — e.g. "Which of these should I act on vs. leave for later?" Skip this step on a scheduled/headless run; just include the queue in the written brief and let Hamid respond when he reads it.

### Step 6: Publish a Report (Trinity)

If `mcp__trinity__report` is available, call `mcp__trinity__list_reports` first (`report_type: aegis_ceo.strategy_brief`, `hours: 168`) to avoid duplicating this week's brief, then publish:

- `report_type`: `aegis_ceo.strategy_brief`
- `display_hint`: `markdown`
- `title`: `Strategy brief — [date range]`
- `payload`: `{"markdown": "<the full brief text>"}`

Skip silently if the tool isn't available.

## Outputs

- A direct, prioritized strategy brief in chat
- An explicit escalation queue for anything expanding scope or access
- A `markdown`-hinted Trinity report (when deployed)
