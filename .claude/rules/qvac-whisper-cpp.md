# qvac-ext-lib-whisper.cpp changes

Scope: `**/qvac-ext-lib-whisper.cpp/**`

- **Add tests:** Add tests for every new model, quantization, feature, fix, or change.
- **Keep both README levels current:** When a change affects documented behavior, setup, public functionality, supported models, examples, or usage, update both the repository root `README.md` and the affected engine's `README.md`.
- **Verify documentation during review:** For every review, check whether both the root `README.md` and each affected engine `README.md` remain accurate and complete. Treat missing applicable updates as review findings.
- **End-to-end verification:** Once the work is complete, verify it end to end against the `qvac/` repo. Don't do this yourself unless authorized, but coordinate with the developer to:
  - Push the changes to their fork of `qvac-ext-lib-whisper.cpp`.
  - Push the corresponding changes to their fork of `qvac-registry-vcpkg`.
  - Push a branch to the `tetherto/qvac` repo to exercise the relevant `on-pr-*` workflow.
