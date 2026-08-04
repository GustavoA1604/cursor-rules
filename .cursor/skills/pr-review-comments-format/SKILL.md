---
name: pr-review-comments-format
description: Format pull request review findings as file name, line number, and a self-contained comment to post. Use when reviewing a PR, writing code review feedback, or preparing inline review comments.
---

# PR Review Comments Format

When delivering code review feedback, output each finding as the file name, line number, and comment to post.

## Rules

- One entry per finding. Each entry must be self-contained so it can be pasted directly as an inline PR comment without extra context.
- Line numbers refer to the PR head, on the after side of the diff. For added lines, point at the new line and quote the anchored code so the location is unambiguous.
- State the issue, why it matters, and the concrete suggested change. Do not reference other comments unless that comment is also being posted.
- Every finding must carry a severity and, when applicable, a source tag.
- Do not add headings or summaries around the findings.
- After the list, offer to post the comments.

## Required analyses

Always run these checks in addition to the normal correctness and quality review. Each gap becomes a finding in the standard output format.

Before reviewing, load the workspace coding guidelines from `.cursor/rules/*.mdc`. Apply an always-on rule to every review and a glob-scoped rule only when the PR touches its paths. The authoritative rule files win if this skill disagrees with them.

### Test coverage

- Every behavior change must have a corresponding test.
- For every behavior-changing hunk, identify the test that exercises it. If none exists, name the specific function, branch, or edge case left untested and the test to add.
- New functions, branches, fixes, changed return values or error handling, and runtime configuration changes affect behavior.
- Formatting, documentation, comments, and behavior-preserving renames do not require new tests.

### qvac-ext-lib-whisper.cpp

- Require tests for every new model, quantization, feature, fix, or change.
- Verify that applicable changes update both the repository root `README.md` and each affected engine's `README.md`.
- Require end-to-end evidence against `qvac/`: a fork of `qvac-ext-lib-whisper.cpp`, corresponding `qvac-registry-vcpkg` changes, and a `tetherto/qvac` branch exercising the relevant `on-pr-*` workflow.

### Team coding standards

- All names, messages, and documentation in code must be English.
- Keep functions small and single-purpose. Put each loop in a separate function.
- Refactor explanatory comments into clear code and keep nearby touched comments current.
- Replace hardcoded values with named constants, configuration, or parameters.
- Do not add emojis or Asana references.
- Test every behavior change.

### qvac monorepo and packages

- Verify that applicable changes update both the qvac root `README.md` and every affected package's `README.md`.
- Keep version bumps in their own PR.
- In version bump PRs, verify consistency among `vcpkg-configuration.json`, `CHANGELOG.md`, and `package.json`.
- Require a concise `[Unreleased]` entry for consumer-affecting package changes and no entry for changes outside the published package.
- Change the vcpkg baseline only when necessary.
- Confirm both C++ and JavaScript linting were run.

### qvac-registry-vcpkg

- Verify each changed portfile archive `SHA512` against the exact archive URL used by vcpkg.
- Verify each new version database `git-tree` against the corresponding port directory tree.
- Require exactly one new version per touched port with no duplicate version and port-version pair.
- Verify consistency among `vcpkg.json`, the version database, `versions/baseline.json`, and applicable dependent constraints.
- Verify that ports sharing an upstream commit use the same `REF` and `SHA512`.

## Output format

For each finding:

```
**[<severity>]<source tag> `<file path>` — line <n>** (`<anchored code>`)

> <comment: the issue, why it matters, and the concrete suggested change>
```

- Severity is required and is exactly one of: `critical`, `medium`, `low`, or `nit`.
- Use `[coding-standards]` immediately after the severity only when the finding comes from `coding-standards.mdc`.
