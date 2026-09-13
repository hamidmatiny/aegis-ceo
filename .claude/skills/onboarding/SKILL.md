---
name: onboarding
description: Track your setup progress — shows what's done, what's next, and walks you through each step
allowed-tools: Read, Write, Edit, Bash, AskUserQuestion
user-invocable: true
metadata:
  version: "1.0"
  created: 2026-09-12
  author: aegis-ceo
---

# Onboarding

Track and continue your setup progress. This skill reads `onboarding.json`, shows your current status, and walks you through the next incomplete step.

## Process

### Step 1: Load State

Read `onboarding.json` from the agent root directory. If it doesn't exist, inform Hamid that onboarding is complete or the file was removed.

### Step 2: Show Progress

Display a checklist grouped by phase. Mark the current phase with an arrow. Use checkboxes:

```
## AEGIS CEO — Setup Progress

### Phase 1: Local Setup  ← current
- [ ] Configure environment variables (.env)
- [ ] Confirm corp-orchestrator API shape + real test call
- [ ] Run /daily-trajectory-review with real credentials
- [ ] Install recommended plugins

### Phase 2: Trinity Deployment
- [ ] Deploy to Trinity
- [ ] Run a skill remotely

### Phase 3: Schedules
- [ ] Enable recommended schedules
- [ ] Verify first scheduled execution

**Progress: 0/8 complete**
```

### Step 3: Guide Next Step

Identify the first incomplete step in the current phase. Based on which step it is, provide specific guidance:

**For `env_configured`:**
- Check if `.env` exists. If not, guide: `cp .env.example .env` then fill in values.
- List the required variables from `.env.example`: `GITHUB_TOKEN` (fine-grained PAT, read-only, scoped to the AEGIS repo), `CORP_ORCHESTRATOR_API_URL`, `CORP_ORCHESTRATOR_API_TOKEN` (read-only), and optionally `NVD_API_KEY`.
- After user confirms, mark done.

**For `corp_orchestrator_verified`:**
- This is the one genuinely custom step — `corp-orchestrator`'s real Trajectory Report API shape isn't fully known yet. Ask Hamid to confirm (or point at) the real endpoint path under `/admin/company` on `defenseaegis.org`, then run a real test call:
  ```bash
  curl -sf -H "Authorization: Bearer $CORP_ORCHESTRATOR_API_TOKEN" "$CORP_ORCHESTRATOR_API_URL/trajectory/latest"
  ```
- If the shape differs from what `/daily-trajectory-review` assumes, update that skill's Step 2 to match the real endpoint and response fields before marking this done.
- After a real successful call, mark done.

**For `first_skill_run`:**
- Tell Hamid to run `/daily-trajectory-review` directly.
- After it produces a real (not placeholder/error) result, mark done.

**For `plugins_installed`:**
- Run the install commands:
  ```
  /plugin install agent-dev@abilityai
  /plugin install trinity@abilityai
  /plugin install utilities@abilityai
  ```
- Run each install command via Bash. Note successes and failures.
- After all attempted, mark done.

**For `onboarded` (Trinity phase):**
- Guide Hamid to run `/trinity:onboard`.
- After completion, mark done and advance phase.

**For `first_remote_run`:**
- Tell Hamid to run `mcp__trinity__chat_with_agent` with the agent name `aegis-ceo` and a skill like `/github-pulse`.
- After completion, mark done and advance phase.

**For `schedules_configured`:**
- Remind Hamid the recommended schedules live in `template.yaml` (`schedules:`), all shipped `enabled: false` by design. Confirm which ones he wants live, flip them to `true`, and re-run `/trinity:onboard` (or `mcp__trinity__create_agent_schedule` directly) to reconcile.
- To turn one on/off later on the live agent, use `mcp__trinity__toggle_agent_schedule`.
- After completion, mark done.

**For `first_scheduled_run`:**
- Tell Hamid to check `mcp__trinity__get_schedule_executions` for execution confirmation.
- After verified, mark done.

### Step 4: Update State

After each step is completed, update `onboarding.json`:
- Set the step's `done` to `true`
- If all steps in current phase are done, advance `phase` to the next phase
- If all phases complete, congratulate the user

### Step 5: Phase Transitions

When all steps in a phase are complete:

**Local → Trinity:**
```
## Local Setup Complete!

AEGIS CEO is fully configured and producing real trajectory reviews locally.

Ready for the next level? Trinity gives you:
- Remote execution (check in from anywhere, not just this terminal)
- Scheduling (daily trajectory reviews, continuous CVE watching)
- A durable Reports history you can review without reading chat

Run /onboarding again when you're ready to set up Trinity.
```

**Trinity → Schedules:**
```
## Trinity Deployment Complete!

AEGIS CEO is live on Trinity. Now let's decide what runs on its own.

Run /onboarding to enable scheduled reviews.
```

**All Complete:**
```
## Onboarding Complete!

AEGIS CEO is fully set up:
- ✓ Local environment configured
- ✓ Deployed to Trinity
- ✓ Schedules running

You're all set. The onboarding.json file can be kept as a record or deleted.
```

## Outputs

- Updated `onboarding.json` with progress
- Step-by-step guidance for the current task
- Phase transition messages at milestones
