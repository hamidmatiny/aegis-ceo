---
name: handle-anomaly
description: Triage an incoming escalation from aegis-analyst or aegis-threat-intel — acknowledge, restate facts, decide log-vs-Slack-Hamid; never fix or dismiss the underlying issue
allowed-tools: Read, Write, Bash, mcp__trinity__list_channel_groups, mcp__trinity__send_group_message, mcp__trinity__send_message, mcp__trinity__report, mcp__trinity__list_reports
user-invocable: true
metadata:
  version: "1.0"
  created: 2026-09-13
  author: aegis-ceo
---

# Handle Anomaly / Escalation Triage

## Purpose

Receive an escalation from `aegis-analyst` (revenue arithmetic anomaly) or `aegis-threat-intel` (security finding) via Trinity `chat_with_agent`, acknowledge it, restate what was reported without embellishment, and make **one** judgment call: log for the next trajectory review, or push to Hamid immediately on Slack `#aegis-ceo`.

**Hard rules:**
- Do **not** fix, dismiss, remediate, or resolve the underlying issue.
- Do **not** invent numbers, CVEs, or severity beyond what the sender stated.
- Do **not** take irreversible, costly, or public-facing action — Slack to Hamid is the max outbound for "urgent."
- Numbers-first: if the payload is missing concrete figures/IDs, say so and treat as incomplete (still acknowledge).

## When this skill runs

- Explicit `/handle-anomaly …` (analyst convention)
- Or any inbound agent message that is clearly an escalation from `aegis-analyst` or `aegis-threat-intel` (finding / anomaly / E2E escalation test)

## Process

### Step 1: Identify source and payload

Note `source` if present (`source=aegis-analyst`, headers, or message body). Extract the factual claim only:
- Analyst: conflicting figures, endpoints, timestamps
- TI: CVE/id, component, severity/CVSS, stack scope, patch status

### Step 2: Acknowledge + restate

Reply with:
1. **Acknowledged** — escalation received from `<source>`
2. **Restatement** — bullet the facts exactly as reported (no reinterpretation)
3. **Triage decision** — `LOG` or `URGENT_SLACK` with one-line reason

### Step 3: Triage judgment (only decision allowed)

Choose **URGENT_SLACK** (push Hamid now) if any of:
- Revenue/MRR arithmetic contradiction or implausible jump (money integrity)
- Own-stack security finding with high/critical severity (e.g. CVSS ≥ 7) or "no patch" on a running dependency
- Sender explicitly marked critical / E2E delivery test asking for acknowledgment of a critical path (treat simulated critical findings as URGENT_SLACK for the test, and say they are simulated if labeled as such)

Choose **LOG** if:
- Low severity / informational / customer-typical-only with no immediate exploit signal
- Incomplete payload (acknowledge gap; still LOG)
- Duplicate of something already handled in the last 24h (check `mcp__trinity__list_reports` for `aegis_ceo.escalation_triage` if available)

### Step 4: If URGENT_SLACK — notify Hamid

1. `mcp__trinity__list_channel_groups` with `channel_type: "slack"`
2. Prefer bound `#aegis-ceo` via `mcp__trinity__send_group_message` with a short message: source, restated facts, that you are **not** remediating, and that Hamid should decide next steps
3. If Slack fails, say so plainly in the chat reply — do not claim delivery

If **LOG**: do not Slack; state that it will be available for the next `/daily-trajectory-review` / strategy pass.

### Step 5: Publish triage record (Trinity)

If `mcp__trinity__report` is available:
- `report_type`: `aegis_ceo.escalation_triage`
- `display_hint`: `markdown`
- `title`: e.g. `Triage URGENT_SLACK — analyst anomaly` or `Triage LOG — TI finding`
- `payload`: `{ "markdown": "...", "source": "...", "decision": "LOG"|"URGENT_SLACK", "slack_delivered": true|false }`

Skip silently if unavailable.

## Known failure modes

### FM-1 — Unknown command / no handler

**What went wrong:** Analyst escalations arrived at CEO as `/handle-anomaly` but no skill existed → `Unknown command: /handle-anomaly`.

**Correct behavior:** This skill is the handler. Keep the `/handle-anomaly` name stable for analyst callers.

### FM-2 — Acting instead of triaging

**What went wrong:** Temptation to "fix" MRR or mitigate a CVE.

**Correct behavior:** Acknowledge, restate, LOG vs URGENT_SLACK only.

## Outputs

- Chat reply: acknowledged + restatement + triage decision
- Optional Slack to `#aegis-ceo` when URGENT_SLACK and Slack bound
- Optional Trinity report `aegis_ceo.escalation_triage`
