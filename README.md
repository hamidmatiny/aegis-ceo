# AEGIS CEO

**Role:** Personal chief-executive assistant to Hamid for AEGIS — a real LLM/agent-security company extended into AEGIS-for-SMB, live at [defenseaegis.org](https://defenseaegis.org).

AEGIS CEO runs day-to-day oversight so Hamid can check in periodically instead of managing every detail himself: real trajectory data pulled from AEGIS's own production `corp-orchestrator` system, active CVE/security watching, GitHub activity checks, and honest, numbers-first strategic recommendations. It is deliberately read/advisory-only — it does not write to production, Stripe, or the AEGIS repo unless Hamid explicitly expands its scope.

## Capabilities

- **Daily trajectory review** — pulls real MRR/signups/uptime/security-findings data from `corp-orchestrator` (`/daily-trajectory-review`)
- **CVE & security watch** — scans CVE feeds and security news relevant to AEGIS's own stack and its customers' typical infrastructure (`/cve-watch`)
- **GitHub pulse** — checks real, read-only activity on the AEGIS repo (`/github-pulse`)
- **Strategy brief** — synthesizes the above into a prioritized, honest recommendation and an explicit escalation queue for anything requiring Hamid's decision (`/strategy-brief`)

## Getting Started

```
cd aegis-ceo && claude
/onboarding
```

See **[ARCHITECTURE.md](ARCHITECTURE.md)** for how the agent is built today and **[TARGET-ARCHITECTURE.md](TARGET-ARCHITECTURE.md)** for where it's headed.

## Skills

| Skill | Purpose |
|-------|---------|
| `/daily-trajectory-review` | Pull and summarize corp-orchestrator's real trajectory data |
| `/cve-watch` | Scan for CVEs/security news relevant to AEGIS's stack |
| `/github-pulse` | Check real GitHub activity on the AEGIS repo |
| `/strategy-brief` | Synthesize findings into a prioritized, honest recommendation |
| `/reconcile-docs` | Keep docs, skills, and architecture consistent |
| `/update-dashboard` | Refresh the live Trinity dashboard snapshot |
