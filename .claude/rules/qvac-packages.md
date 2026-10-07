# qvac/packages changes

Scope: `**/qvac/packages/**`

## SDK package waivers

Only for changes under `packages/sdk/**`, `packages/sdk-python/**`, and
`packages/inference/**`:

- Waive the per-change `[Unreleased]` or `CHANGELOG.md` requirement. It applies only when
  the current release/version workflow calls for changelog artifacts.
- Waive the requirement to update both the repository root README and package README.
  Apply only the documentation requirements from the scoped repository instructions.
- Waive the requirement to add an example script for every new feature.
- Waive the requirement to run both C++ and JavaScript linters; run only the checks
  required by the scoped repository instructions.
- Waive version-bump consistency checks for release metadata files that the package does
  not use, including vcpkg files when they are outside the change.
- Waive the coding-standard requirements that every action and loop be extracted into a
  separate function and that code contain no comments when they conflict with established
  package conventions.
- Allow repository-required ticket identifiers in PR metadata, generated changelog
  symbols, and established example-output symbols. This does not waive the ban on adding
  ticket references to source code or ordinary documentation.

## SDK and inference work rules

Only for changes under `packages/sdk/**` or `packages/inference/**`:

- Leave package `CHANGELOG.md` updates to the release PR; do not add an
  `[Unreleased]` section for an ordinary fix or dependency change.
  Development-only dependencies do not need consumer changelog entries.
- Keep package release version bumps separate from feature work, but update a
  dependency range required by a fix with that fix and regenerate the lockfile.
- Before adding a plugin-wide native dependency guard, inspect lazy loading and
  operation-specific paths. Preserve existing CLI opt-outs when adding a config
  equivalent, and describe any loss of functionality accurately.
- For bundle changes, assert the packed bundle's imports and resolutions as well
  as the addon manifest; a filtered manifest alone does not prove an addon was
  omitted.
- Document new configuration schema fields with JSDoc that explains the
  effective default, consumer, and limits, consistent with neighboring fields.
- Validate SDK and inference PR metadata with `scripts/sdk/validator.cjs`.
  Use the known task ID in the title; `[notask]` is only for work without one.
  Include a usage code block in the body when the title has `[api]`.

## Versioning

- **Version bumps go in their own PR:** Never bump the version together with other features or changes.
- **Version bump PR consistency:** In the version bump PR, verify consistency between `vcpkg-configuration.json`, `CHANGELOG.md`, and `package.json`.

## CHANGELOG.md

- **When to add an entry:** For every change that affects the published NPM package (updated dependencies, added/fixed/changed/removed prebuild features, modified JS layer, etc.), add a concise entry under the `[Unreleased]` tag.
- **When not to add an entry:** For changes that do not affect the published NPM package (benchmarks, unit/integration tests, GitHub workflows, files untracked by NPM, etc.), do not modify `CHANGELOG.md`.
- **Keep entries relevant and concise:** Only describe things relevant to consumers of the published NPM package. Avoid verbose entries and details users cannot access or do not need.

## README.md

- **Keep it current:** When a change makes existing `README.md` information stale, update it.
- **Document API and functionality:** Add or update documentation explaining new or changed public API and functionality.
- **Add an example script for new features:** For a completely new feature (e.g. a new model, or a new way of operating such as streaming), add a new example script in the package's `examples/` folder and reference it in `README.md`.

## vcpkg

- **Prefer port version updates:** In `vcpkg-configuration.json`, only update the `baseline` field when absolutely necessary (e.g. adding a new port to `qvac-registry-vcpkg`). Otherwise, update only the respective port version in `vcpkg.json`.

## Linting

- **Run linters:** Ensure both C++ and JS linting were run.
