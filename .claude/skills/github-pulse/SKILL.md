---
name: github-pulse
description: Check real GitHub activity on github.codmatiny/aegis — commits, PRs, issues, CI status — read-only, no write access
allowed-tools: Read, Write, Bash, WebFetch
user-invocable: true
metadata:
  version: "1.0"
  created: 2026-09-12
  author: aegis-ceo
---

# GitHub Pulse

## Purpose

Give Hamid a real, read-only snapshot of development activity on the AEGIS repo — what shipped, what's open, what CI says — without ever writing to the repo.

## Process

### Step 1: Check Credentials

```bash
[ -n "$GITHUB_TOKEN" ] && echo "configured" || echo "missing"
```

If missing, say so plainly and point at `/onboarding`. Do not guess at repo activity.

### Step 2: Pull Real Activity (Read-Only)

Use the GitHub REST API with `GITHUB_TOKEN` — read-only calls only, never a write endpoint:

```bash
# Recent commits (last 24h)
curl -sf -H "Authorization: Bearer $GITHUB_TOKEN" -H "Accept: application/vnd.github+json" \
  "https://api.github.com/repos/codmatiny/aegis/commits?since=$(date -u -v-1d +%Y-%m-%dT%H:%M:%SZ)"

# Open PRs
curl -sf -H "Authorization: Bearer $GITHUB_TOKEN" -H "Accept: application/vnd.github+json" \
  "https://api.github.com/repos/codmatiny/aegis/pulls?state=open"

# Open issues
curl -sf -H "Authorization: Bearer $GITHUB_TOKEN" -H "Accept: application/vnd.github+json" \
  "https://api.github.com/repos/codmatiny/aegis/issues?state=open&filter=all"

# Recent CI workflow runs
curl -sf -H "Authorization: Bearer $GITHUB_TOKEN" -H "Accept: application/vnd.github+json" \
  "https://api.github.com/repos/codmatiny/aegis/actions/runs?per_page=10"
```

(Verify the actual owner/repo slug during onboarding — `github.codmatiny/aegis` as given may need translating to the real `owner/repo` path GitHub's API expects.)

If any call 404s or 401s, report that specific failure honestly rather than silently omitting that section.

### Step 3: Summarize

```
## GitHub Pulse — [date]

- Commits (24h): [N] — [one-line highlight of the most notable change, if any]
- Open PRs: [N] ([X] awaiting review, [Y] with failing CI)
- Open issues: [N] ([X] opened in last 24h)
- CI: [last N runs — pass/fail breakdown]

[One direct sentence — e.g. "Quiet day, one PR stuck on failing tests for 3 days" — not a vague "steady progress."]
```

### Step 4: Publish a Report (Trinity)

If `mcp__trinity__report` is available, call `mcp__trinity__list_reports` first (`report_type: aegis_ceo.github_pulse`, `hours: 24`) to avoid duplicating today's check, then publish:

- `report_type`: `aegis_ceo.github_pulse`
- `display_hint`: `timeline`
- `title`: `GitHub pulse — [date]`
- `payload`: `{"events": [{"ts": "<iso8601>", "label": "commits|PRs|issues|CI", "detail": "..."}]}`

Skip silently if the tool isn't available.

## Outputs

- A direct GitHub activity summary in chat
- A `timeline`-hinted Trinity report (when deployed)
