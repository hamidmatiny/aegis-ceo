# Fleet career ladder (Track B)

Evidence-based leveling. **No self-declared promotions.** Manager judges from real task history; `aegis-ceo` is judged by Hamid.

## Levels

| Level | Title | Objective bar (all must be met) |
|-------|-------|----------------------------------|
| L1 | SE I | Completes assigned core playbooks with **≥80% success** over **≥20** scheduled/ad-hoc executions (Trinity status `success`, not cancelled). Failures are honest Slack close-outs, not silent. Scope: single-skill, well-specified tasks. |
| L2 | SE II | L1 bar **and** handles multi-step playbooks / cross-skill composition; **≥85% success** over **≥40** executions; at least **2** durable fixes or process improvements that stayed green for **≥14 days** (cited execution ids + dates). |
| L3 | Senior SE | L2 bar **and** owns a domain end-to-end (e.g. threat scan cadence, revenue verify loop); **≥90% success** over **≥60** executions; at least **1** incident or anomaly caught before Hamid had to notice, with confirmed CEO escalation delivery; mentors via documented playbook improvements (manager-accepted before/after). |
| L4 | Staff SE | L3 bar **and** cross-agent design impact (routing, shared standards, verification gates) with **≥2** accepted fleet-wide changes; failure rate on owned systems not worse after change (before/after execution stats); proposes hires only through formal request — never creates agents. |
| L5 | Manager | L4 bar **and** manages ≥1 specialist with documented promotion/coaching decisions grounded in evidence; fleet outcomes under their branch improve or stay honest under constraint; may **request** hiring for their team (tier-proposal → Hamid approval — never unilateral). |

### Explicit non-criteria

- Self-nominated titles
- Self-graded “I improved”
- Volume of Slack messages or reports without success rate
- Fancy architecture proposals with no shipped evidence

### Promotion process

1. Manager pulls Trinity execution history for the window (success/fail counts, skill names).
2. Manager writes a short evidence packet (links/ids, dates, what stayed fixed).
3. Manager applies the level in this roster + the agent's `CLAUDE.md` Career section.
4. Agent never updates its own level upward.

### Hiring

Any level, including Manager: hiring request → `aegis-infra` `/propose-agent-tier` → **Hamid final approval**. No agent creates another agent without that sign-off.

## Current roster levels

| Agent | Level | Since | Evidence summary | Judged by |
|-------|-------|-------|------------------|-----------|
| aegis-ceo | SE II (interim) | 2026-09-15 | Running trajectory/CVE/GitHub/strategy skills; corrected $0 MRR narrative delivered; not yet at Senior sample size under new ladder | Hamid |
| aegis-infra | SE II (interim) | 2026-09-15 | OmniRoute ownership, tier proposals, stampede diagnosis, `/token-budget`+allocation design shipped | aegis-ceo |
| aegis-threat-intel | SE I | 2026-09-15 | Core scan skills exist; recurring schedule newly enabled — promote only after ≥20 successful unattended scans | aegis-ceo |
| aegis-analyst | SE I | 2026-09-15 | Revenue check skills live; schedule newly enabled — need sample size | aegis-ceo |
| aegis-core-infra | SE I | 2026-09-15 | Diff review skills live; schedule newly enabled | aegis-ceo |
| aegis-data-quality | SE I | 2026-09-15 | Review skills live; schedule newly enabled | aegis-ceo |
| aegis-growth | SE I | 2026-09-15 | Analysis live; execution skills added 2026-09-15 — no promotion until execution evidence | aegis-ceo |
| the-brain | SE II (interim) | 2026-09-15 | VP synthesis role; mid-cost tier — confirm with execution sample before Senior | aegis-ceo |

**First promotion evaluations:** after **≥20** successful unattended core-schedule executions per specialist (or ≥14 days of clean cadence), managers re-score against the table. No promotions on day-of-ladder creation.

## Promotion history

| Date | Agent | From → To | Evidence | Decided by |
|------|-------|-----------|----------|------------|
| 2026-09-15 | (all) | — → initial placement | Ladder created; interim placements only | Hamid / aegis-ceo |
