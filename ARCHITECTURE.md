# AEGIS CEO Architecture (Current State)

**What this is:** the agent as it actually runs today. For where it's deliberately headed, see the companion **`TARGET-ARCHITECTURE.md`**. When a target ships, it moves *out* of that doc and *into* this one.

**Last updated:** 2026-09-12

## Overview

AEGIS CEO is a single-agent, read/advisory-only Claude Code agent. It has four purpose-built skills that each produce a real, sourced finding, plus three fleet-standard housekeeping skills (onboarding, dashboard, doc reconciliation). It holds no write credentials to AEGIS production, Stripe, or the AEGIS repo.

## Components

### Skills

- `/daily-trajectory-review` — pulls MRR/signups/uptime/security-findings from `corp-orchestrator`'s Trajectory Report API
- `/cve-watch` — scans NVD + security news for findings relevant to AEGIS's stack
- `/github-pulse` — checks read-only GitHub activity on `github.codmatiny/aegis`
- `/strategy-brief` — synthesizes the above into a prioritized recommendation + escalation queue
- `/onboarding` — tracks setup progress across local, Trinity, and schedule phases
- `/update-dashboard` — refreshes `dashboard.yaml` from local history files
- `/reconcile-docs` — checks CLAUDE.md/README/architecture docs/skills stay mutually consistent

### Subagents

None yet.

### Data & State

- `trajectory-history.json` — per-run trajectory snapshots, for day-over-day comparison
- `cve-watch-history.json` — per-run CVE findings, to avoid re-flagging unchanged items
- `onboarding.json` — persistent setup checklist

### Schedules

Declared in `template.yaml` (`schedules:`), all shipped `enabled: false` pending Hamid's go-ahead:

- Daily trajectory review — `0 8 * * *` UTC
- CVE watch — every 6 hours
- GitHub pulse — `30 7 * * *` UTC
- Weekly strategy brief — Monday `0 9 * * 1` UTC
- Dashboard refresh — every 6 hours
- Doc reconciliation — Monday `0 9 * * 1` UTC

## Trinity Integration

Not yet deployed. `template.yaml` declares resources (2 CPU / 4g memory), the `agent-dev`/`trinity`/`utilities` plugins, and the credential contract (`GITHUB_TOKEN`, `CORP_ORCHESTRATOR_API_URL`, `CORP_ORCHESTRATOR_API_TOKEN`, optional `NVD_API_KEY`). No MCP servers of its own — `.mcp.json.template` is empty; Trinity injects its own `trinity` MCP entry at container start.
