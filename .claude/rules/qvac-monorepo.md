# qvac monorepo changes

Scope: `**/qvac/**`

## Pull requests

- **Speech-team branches stay in the monorepo:** PRs authored by `Zbig9000`, `ogad-tether`, `freddy311082`, `ishanvohra2`, `RamazTs`, `Alok-Ranjan23`, `pratiknarola-t`, or `mexxik` must be opened from a branch in the monorepo (`tetherto/qvac`), not from a fork.
- **Check the PR source and author first:** If a qvac PR was opened from a fork and its author's GitHub login matches one of the listed speech-team accounts, case-insensitively, stop immediately: do not review the changes, and ask the author to reopen the PR from a branch in the monorepo. Continue reviewing fork PRs from all other authors normally.

## Documentation

- **Keep both README levels current:** When a change affects documented behavior, setup, public functionality, supported features, examples, or usage, update both the root `README.md` and every affected package's `README.md`.
- **Verify documentation during review:** For every review, check whether the root `README.md` and each affected package `README.md` remain accurate and complete. Treat missing applicable updates as review findings.

## Related rules

- **Apply package-specific rules:** For changes under `qvac/packages/**`, also read and follow `.claude/rules/qvac-packages.md`.
