<!-- markdownlint-disable -->

# Hardening Report: github--command/v2.0.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **github--command/v2.0.2** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Direct expression interpolation of user-controlled `workflow_dispatch` inputs inside `run:` shell commands. In the 'tag new target' step, `${{ github.event.inputs.major_version_tag }}` and `${{ github.event.inputs.source_tag }}` are interpolated directly into the shell command `git tag -f ${{ github.event.inputs.major_version_tag }} ${{ github.event.inputs.source_tag }}`. In the 'push new tag' step, `${{ github.event.inputs.major_version_tag }}` is interpolated directly into `git push origin ${{ github.event.inputs.major_version_tag }} --force`. An attacker with workflow_dispatch access could inject arbitrary shell metacharacters via these inputs.

Locations:

- `.github/workflows/update-latest-release-tag.yml:32`
- `.github/workflows/update-latest-release-tag.yml:35`

### unpinned-uses (severity: high)

Multiple workflow files reference GitHub Actions using mutable tags (e.g. @v4, @v3) instead of immutable full 40-character commit SHAs. This exposes the workflow to supply-chain attacks if the tag is moved to a malicious commit. Unpinned references: actions-config-validation.yml: `actions/checkout@v4`; codeql-analysis.yml: `actions/checkout@v4`, `github/codeql-action/init@v3`, `github/codeql-action/autobuild@v3`, `github/codeql-action/analyze@v3`; lint.yml: `actions/checkout@v4`, `actions/setup-node@v4`; package-check.yml: `actions/checkout@v4`, `actions/setup-node@v4`; test.yml: `actions/checkout@v4`, `actions/setup-node@v4`; update-latest-release-tag.yml: `actions/checkout@v4`.

Locations:

- `.github/workflows/actions-config-validation.yml:16`
- `.github/workflows/codeql-analysis.yml:19`
- `.github/workflows/codeql-analysis.yml:23`
- `.github/workflows/codeql-analysis.yml:27`
- `.github/workflows/codeql-analysis.yml:31`
- `.github/workflows/lint.yml:14`
- `.github/workflows/lint.yml:17`
- `.github/workflows/package-check.yml:15`
- `.github/workflows/package-check.yml:18`
- `.github/workflows/test.yml:14`
- `.github/workflows/test.yml:17`
- `.github/workflows/update-latest-release-tag.yml:22`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses

**Notes:**

Fixed script-injection in update-latest-release-tag.yml by moving workflow_dispatch inputs (major_version_tag, source_tag) into step env: blocks and referencing them as double-quoted shell variables. Pinned all unpinned action references to full commit SHAs: actions/checkout@v4→34e114876b0b11c390a56381ad16ebd13914f8d5, actions/setup-node@v4→49933ea5288caeca8642d1e84afbd3f7d6820020, github/codeql-action/{init,autobuild,analyze}@v3→b7351df727350dca84cb9d725d57dcf5bc82ba26. All 6 workflow files updated.

