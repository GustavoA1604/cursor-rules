---
name: pr-self-review
description: Review your OWN changes before opening or updating a PR, then fix what is confirmed and loop until a pass finds nothing new. Use before pushing a branch or requesting review. Can modify the working tree.
argument-hint: "[branch] (defaults to current branch vs main)"
---

# PR self-review

Review the changes on your own branch, fix what survives verification, and repeat until a
fresh pass finds nothing new. This is the loop the team should run **before** asking a
human to review, so that a human reviewer rarely finds anything.

Applies to your own work only — it may modify the working tree. To review someone else's
PR without touching code, use `pr-review` instead.

## Load first

- The applicable rule files for the touched paths (see the table in `CLAUDE.md`), and the
  team coding standards.
- `pr-review-core` — triage, lens selection, adversarial verification, and the cost rules.
  This skill follows that engine; it does not restate it.

## Inputs

- Optional branch name. Default: current branch compared against `main`.
- Get the diff: `git diff main...HEAD` (current) or `git diff main...<branch>`.
- If the diff is empty, report "No changes to review" and stop.

## Workflow

1. **Triage** the diff (core Step A). Announce the tier and, for Tier 0/1, that no
   subagents will be spawned.
2. **Find** — Tier 0: run the single applicable rule checklist inline. Tier 1: one inline
   general reviewer pass. Tier 2: fan out the selected lenses (core Step B).
3. **Verify** surviving High/Medium findings (core Step C). Drop what does not survive.
4. **Present** the confirmed findings with a short fix plan, grouped by severity.
5. **Fix loop:**
   - Fix each confirmed finding, staying within the scope of that finding. Follow the team
     coding standards: small single-purpose functions, loops in their own functions, named
     constants, English, no explanatory comments, and a test for every behavior change.
   - After fixing, re-run a **fresh** review pass over the changed areas only — reason as if
     you had not written the fix. Confirm each fix is real and introduced no regression.
   - Repeat until a round produces no new High/Medium finding, or after **2 rounds**,
     whichever comes first.
6. **Test coverage** — ensure every behavior added, changed, or fixed has a corresponding
   test at the right level. A behavior change without a test is itself a finding to fix.
7. **Report** — what was found, what was fixed, and what remains open with the reason.
   Do not commit and do not push; that is the developer's step.

## Guardrails

- Never delete, disable, skip, or weaken a test to make a check pass. Fix the code or the
  test; if neither is possible, stop and report.
- Keep each fix within its finding. Do not refactor unrelated code or widen scope.
- If a confirmed finding needs an architectural change, stop and report it rather than
  forcing a local patch.
- Do not commit or push.
