---
name: project-manage
description: Apply the fleet PM system (OKRs + WIP-limited Kanban + RICE) to prioritize work toward Track A revenue (real MRR), assign/direct specialists, and propose concrete next steps to Hamid. Use for prioritization, sprint planning, or "what should we do next".
allowed-tools: Read, Write, Bash, mcp__trinity__list_agents, mcp__trinity__list_reports, mcp__trinity__list_recent_executions, mcp__trinity__chat_with_agent, mcp__trinity__list_channel_groups, mcp__trinity__send_group_message, mcp__trinity__report
user-invocable: true
disable-model-invocation: false
metadata:
  version: "1.0"
  created: 2026-09-15
  author: aegis-ceo
  changelog:
    - "1.0: Initial PM mandate — OKR north star, WIP=3 Kanban, RICE scoring, revenue-first"
---

# Project Manage

## Purpose

Stop being a passive status compiler. Use a real PM system to push the fleet toward the north star: **commercializing AEGIS — real Track A MRR** (baseline corrected to **$0** as of 2026-09-15 verification; never invent growth).

## Synthesized PM approach (what we actually use)

Not a name-drop. Three pieces that fit a small agent fleet:

1. **OKRs (quarterly goals)** — one Objective, ≤3 Key Results, all numeric and falsifiable.
2. **WIP-limited Kanban (flow)** — max **3** active Initiatives fleet-wide. Nothing new starts until a card moves to Done or is explicitly killed. Prevents stampede of half-finished work (same failure mode as the Gemini 429 stampede).
3. **RICE prioritization** — score candidates: `Reach × Impact × Confidence / Effort`. Reach = who/what is affected (signups, paying path); Impact 0.25–3; Confidence 0–100%; Effort in agent-days. Highest RICE that still fits WIP wins.

### Explicitly not using

- Heavyweight waterfall Gantt
- Story-point theater without delivery evidence
- Vanity OKRs (“improve presence”) without a number

## Standing north-star OKR (update when MRR moves)

**Objective:** Turn AEGIS-for-SMB into a real revenue business.

| KR | Metric | Baseline (2026-09-15) | Target (90 days) |
|----|--------|----------------------|------------------|
| KR1 | Paying subscribers (Track A BEV) | 0 | ≥3 |
| KR2 | MRR (CAD, same source as trajectory) | $0 | ≥ $87 (3×$29) |
| KR3 | Qualified signup→trial/pay funnel movement | 1 signup / 0 paying | Documented weekly signup delta + ≥1 new payer |

If BEV says $0, the OKR board says $0.

## Process

### Step 1: Refresh ground truth

Pull latest trajectory/summary figures (or latest `aegis_analyst` / CEO trajectory report). Record MRR and paying count exactly.

### Step 2: List WIP board

Read `docs/pm-board.md`. Ensure ≤3 Initiatives in **Doing**. If over WIP, force kill or finish before adding.

### Step 3: Score inbox with RICE

Candidates from Hamid, specialist escalations, growth proposals, infra risks. Write RICE table; pick next only if WIP allows.

### Step 4: Direct agents (playbooks only)

Assign via one-line playbook calls / schedule expectations — never prose delegation without a `/skill`. Prefer revenue-touching work (growth SEO/directories, analyst verification, infra cost that unblocks free-pool reliability).

### Step 5: Propose to Hamid

End with **≤3 concrete next steps** for Hamid (approve hire, approve spend, decide product bet) — not a status essay.

### Step 6: Slack + report

Post board + RICE winners to `#aegis-ceo`. Optional `report_type: aegis_ceo.project_manage`, `display_hint: markdown`.

## Outputs

- Updated `docs/pm-board.md`
- Assignments / proposals
- Slack close-out
---

## Final step — Slack completed-task close-out (mandatory)

Post to `#aegis-ceo`.
