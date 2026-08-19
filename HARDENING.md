<!-- markdownlint-disable -->

# Hardening Report: github--command/v2.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **github--command/v2.0.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Direct expression interpolation of attacker-controlled workflow_dispatch inputs inside run: shell commands. In the 'tag new target' step, `${{ github.event.inputs.major_version_tag }}` and `${{ github.event.inputs.source_tag }}` are interpolated directly into `git tag -f ...`, and in the 'push new tag' step `${{ github.event.inputs.major_version_tag }}` is interpolated into `git push origin ...`. A malicious actor with workflow_dispatch access could inject arbitrary shell commands via these inputs (e.g. supplying a tag value containing semicolons or backticks).

Locations:

- `.github/workflows/update-latest-release-tag.yml:31`
- `.github/workflows/update-latest-release-tag.yml:34`

### unpinned-uses (severity: high)

Multiple workflow files reference GitHub Actions using mutable version tags (@v4, @v3) instead of immutable 40-character commit SHAs. This exposes the workflow to supply-chain attacks if the referenced tag is moved to a malicious commit. Affected references: actions/checkout@v4, actions/setup-node@v4, github/codeql-action/init@v3, github/codeql-action/autobuild@v3, github/codeql-action/analyze@v3.

Locations:

- `.github/workflows/actions-config-validation.yml:17`
- `.github/workflows/codeql-analysis.yml:23`
- `.github/workflows/codeql-analysis.yml:26`
- `.github/workflows/codeql-analysis.yml:30`
- `.github/workflows/codeql-analysis.yml:33`
- `.github/workflows/lint.yml:15`
- `.github/workflows/lint.yml:18`
- `.github/workflows/package-check.yml:16`
- `.github/workflows/package-check.yml:19`
- `.github/workflows/test.yml:15`
- `.github/workflows/test.yml:18`
- `.github/workflows/update-latest-release-tag.yml:22`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses

**Notes:**

Fixed script-injection in update-latest-release-tag.yml by moving workflow_dispatch inputs (major_version_tag, source_tag) into step env: blocks and referencing them as double-quoted shell variables. Pinned all unpinned action references to full commit SHAs: actions/checkout@v4→34e114876b0b11c390a56381ad16ebd13914f8d5 (actions-config-validation.yml, codeql-analysis.yml, lint.yml, package-check.yml, test.yml, update-latest-release-tag.yml), actions/setup-node@v4→49933ea5288caeca8642d1e84afbd3f7d6820020 (lint.yml, package-check.yml, test.yml), github/codeql-action/{init,autobuild,analyze}@v3→b7351df727350dca84cb9d725d57dcf5bc82ba26 (codeql-analysis.yml).

