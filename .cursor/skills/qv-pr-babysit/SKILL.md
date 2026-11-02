---
name: qv-pr-babysit
description: Babysit a GitHub PR to merge — keep it up to date (GitHub merge, not rebase), wait for green CI, merge when approved + clean. Defaults to the current PR (from the conversation or the checked-out branch) when no PR is given. Use for "/qv-pr-babysit [pr]", "babysit this PR" or "babysit PR N through merge".
argument-hint: "[<pr-number>|<pr-url>] [<stop-time>|<duration>, default 2h]"
---

# PR Babysit

Drive a PR to merge on a fixed cadence. Do exactly ONE tick per invocation; a recurring CronCreate job supplies the cadence.

## Inputs
Both arguments are optional.
- `pr`: PR number or PR link. A link sets the repo; otherwise the repo is `tetherto/qvac`. When omitted, use the PR from the conversation, else the current branch's PR (`gh pr view --json number,url`). If neither exists, ask.
- `expiry`: a stop time or a duration (e.g. `17:30`, `4h`). Default: 2 hours from now. Get current time with `date` and convert to an absolute stop time once, at setup; ticks receive that absolute time so it does not move.

## Repo facts
- Every step runs through `gh` with `--repo tetherto/qvac` (or the repo from the PR link). The skill does no local git operations, so git remote names do not matter.
- Merge gate: 2 approvals incl. ≥1 codeowner. Auto-merge is disabled repo-wide, so merges are manual.
- `gh pr update-branch` performs a GitHub MERGE, never a rebase. Rebase is not allowed here.

## Setup
1. Recurring tick job: `CronCreate` with `*/3 * * * *`, prompt `/qv-pr-babysit <pr> <absolute stop time>`.
2. One-shot stop job: `CronCreate` with `recurring: false`, minute/hour/day-of-month/month pinned to the stop time, prompt telling Claude to `CronDelete` the tick job by its ID and report the PR's final state.

CronCreate has no expiry parameter, and recurring jobs only auto-expire after 7 days. The stop job enforces the stop time even when a tick fails before reaching the stop-time check.

**Cleanup** below means: `CronList`, then `CronDelete` both the tick job and the stop job.

## One tick
1. `gh pr view <pr> --repo tetherto/qvac --json state,mergeStateStatus,reviewDecision,mergeable`
2. If `state` != OPEN (merged/closed): DONE — Cleanup, report, stop.
3. If now is past the stop time: DONE — Cleanup, report the final state, stop.
4. If `reviewDecision` != `APPROVED`: nothing to do this tick — waiting for review. End the tick without changes.
5. Branch on `mergeStateStatus`:
   - **BEHIND** (approved, repo requires up-to-date): `gh pr update-branch <pr> --repo tetherto/qvac` (GitHub merge). A clean update does not dismiss reviews in this repo. If it FAILS with a merge conflict → STOP and prompt the user (do not resolve blindly). When the conflict is `pnpm-lock.yaml`, say so and name the cost: resolving it needs a local merge or rebase, and that push dismisses the approvals, so it is the user's call rather than a detail to absorb. The resolution itself is fixed — take the base's copy and regenerate, never a text merge:
     ```bash
     git checkout --ours pnpm-lock.yaml
     pnpm install --lockfile-only --ignore-scripts
     git add pnpm-lock.yaml
     ```
     Git text-merges the two revisions into duplicate keys, which pnpm rejects with `ERR_PNPM_BROKEN_LOCKFILE: duplicated mapping key`. Never drop the lockfile to get the update through: a version bump and its lockfile travel together, and landing them apart fails `pnpm install --frozen-lockfile` for everyone on the base branch.
   - **BLOCKED**: checks pending or a required check failing. Run `gh pr checks <pr> --repo tetherto/qvac`. If a required check FAILED and it's a known flake, rerun it: `gh run rerun <run-id> --failed` (NEVER apply a skip label, never disable a test). If it failed for a real reason in our code → STOP and prompt the user. If checks are merely pending → wait for the next tick.
   - **UNKNOWN**: GitHub is recomputing mergeability — wait and re-check next tick.
   - **CLEAN**: approved + all required checks green → `gh pr merge <pr> --repo tetherto/qvac --squash`. Then Cleanup and report merged.
6. **Never dismiss reviews.** If any step would require code changes, force-pushes, or would dismiss existing approvals, STOP and prompt the user before doing it. Conflicts and reviewer-requested changes are always a stop-and-ask, not an autonomous action.

## Cadence & lifetime
- **Tick every 2-3 minutes: `*/3 * * * *`.** A PR goes from approved to mergeable in seconds, and every tick spent waiting is merge latency. Do NOT apply the CronCreate tool's "avoid :00/:30, prefer hourly off-minutes" advice here — that is for daily digests and polling remote APIs, not for a merge queue. A tick is two `gh` calls and costs nothing.
- Both jobs are session-only: they are gone when the Claude session exits, and they fire only while the session is idle.
- Keep each tick cheap: query state, act only when there's a concrete action; otherwise end the tick quietly.

## Reporting
A tick that changed nothing gets NO user-facing message — no state line, no check tally, no "waiting for X". Silence is the correct output for "still pending". Speak only when:
- the PR merged or closed (final report, then Cleanup);
- a required check FAILED, or `update-branch` hit a conflict, or anything else needs the user's call (stop-and-ask);
- the stop time passed (final state, then Cleanup).
Per-tick status narration is pure token waste — the user can see the PR.
