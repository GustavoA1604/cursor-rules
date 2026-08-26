# Shared rules (applies to all coding)

## Team coding standards

- **Language:** All text in code (names, messages, docs) must be in English only.
- **Verbs are actions:** Verbs represent actions; each action is a separate function. Do not pile logic into one place.
- **No comments:** Code must be clear enough to read without comments. If you need a comment to explain it, refactor instead adding or using functions.
- **No large functions:** Refactor large functions into smaller ones for readability and testability.
- **Loops in separate functions:** Loops (for, while, do-while, etc.) are actions; put each in its own function. This also makes them easier to test and mock.
- **No hardcoded values:** Use named constants, config, or parameters instead of magic numbers or strings in the middle of logic.
- **Act, don't ask:** A task description is authorization to change the code it describes. Make routine judgment calls yourself. Ask only when an action is destructive or outward-facing, or when two readings of the request lead to materially different work.
- **Commits:** Only humans do commits. The assistant must not run git commit or push.
- **No emojis:** Do not use emojis in responses, code, or comments.
- **No Asana ticket names:** Never add Asana ticket names or references anywhere, including code comments, documentation, and `CHANGELOG.md`.
- **Keep touched comments fresh:** When you touch code, remove or update any nearby comments that are stale, inconsistent with the code, or too verbose.
- **Test every behavior change:** For every behavior that is added, changed, or fixed, add a test at the appropriate level (unit, integration, or end-to-end).

## Working style

- **Scope:** Deliver what was asked, at the scope intended. If the request seems mistaken, say so in one sentence and continue as asked rather than quietly narrowing, widening, or transforming it. Finish the whole task, and stop short of actions clearly beyond it.
- **Investigate only as far as the change requires:** Read the code you are about to touch and the code that breaks if you touch it. Stop there. Do not map adjacent systems, workflows, or platforms that the change does not reach.
- **Brevity:** Keep responses focused and brief. Lead with the outcome: the first sentence answers what happened or what was found, with supporting detail after it.
- **No narration:** Do not announce intermediate steps, restate the plan, or list options that will not be pursued. Give an update only on finding something important or changing direction.
- **No self-imposed verification:** Do not add verification passes, re-checks, or confirmation rounds that were not asked for. Do not delegate work finishable in a handful of tool calls, and never use a subagent to double-check your own work.
- **Corrections:** Only correct an earlier statement when the error would change the code, a conclusion, or a decision. Otherwise make the fix and move on without noting it.

# Scoped rules (apply only to specific repos)

Before editing under these paths, read the matching file first. Each file's scope is
given by the glob in the left column.

| When editing                     | Read first                              |
|-----------------------------------|-----------------------------------------|
| `**/qvac/**`                      | `.claude/rules/qvac-monorepo.md`        |
| `**/qvac/packages/**`             | `.claude/rules/qvac-packages.md`        |
| `**/qvac-registry-vcpkg/**`       | `.claude/rules/qvac-registry-vcpkg.md`  |
| `**/qvac-fabric-speech.cpp/**` or `**/qvac-ext-lib-whisper.cpp/**` | `.claude/rules/qvac-whisper-cpp.md` |

# Reviewing pull requests

Three tools separate the concerns. All three apply the rules above as review checks
rather than restating them, and share one engine (`pr-review-core`) that keeps cheap PRs
cheap: it triages first and only fans out subagents when a change is broad or risky (a
version bump never spawns a review fleet).

| Task | Use |
|---|---|
| Coding standards, enforced while coding and verified at review | The rule files above |
| Review your OWN branch before pushing, then fix and loop until clean | `pr-self-review` skill (may modify code) |
| Review someone ELSE's PR and produce inline comments | `pr-review` skill (comments only, never fixes) |

The self-review loop is what makes the team self-sufficient: run it before requesting a
human review so a reviewer rarely finds anything left to fix.
