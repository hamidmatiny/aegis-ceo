# AEGIS CEO Target Architecture

**What this is:** where the agent is deliberately headed. The companion to **`ARCHITECTURE.md`** (what runs today). When something here ships, it moves *out* of this doc and *into* `ARCHITECTURE.md`.

**Last updated:** 2026-09-12

## Direction

Grow AEGIS CEO's authority incrementally, in step with proven trust — never by default, never all at once. This mirrors the same staffing philosophy Hamid used for `corp-orchestrator`'s own department-agent rollout: narrow scope per role, expand only after it's earned. AEGIS CEO should never be the reason something breaks in production, and it should never let impressive-looking infrastructure substitute for real revenue moving.

## Planned Capabilities

- **Write-scoped actions (deferred, requires Hamid's explicit go-ahead)** — e.g. filing a GitHub issue directly instead of only recommending one, or triggering a specific pre-approved `corp-orchestrator` action. Depends on: a proven track record of accurate read/advisory output, and an explicit, scoped grant from Hamid (never a blanket write credential).
- **corp-orchestrator API** — `/daily-trajectory-review` pulls `GET /v1/bev/trajectory` (+ `/v1/bev/summary`) from `https://defenseaegis.org/api/corp` with `CORP_READONLY_TOKEN`, then delivers the same real summary via Trinity Slack MCP (`send_group_message` / `send_message`).
- **Department-agent oversight** — as Hamid hires the next department-agent (one at a time, per the staffing philosophy), AEGIS CEO's `/strategy-brief` should eventually read that agent's own reports too, not just `corp-orchestrator`'s aggregate. Depends on: the next hire actually existing and publishing Trinity reports.
- **Robot-teacher-factory tracking** — once AEGIS reaches a revenue milestone worth reinvesting, add a lightweight skill to track progress toward the long-term goal explicitly, so it doesn't get lost under day-to-day AEGIS operations. Not started — revenue isn't there yet.
