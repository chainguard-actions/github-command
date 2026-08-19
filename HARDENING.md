<!-- markdownlint-disable -->

# Hardening Report: github--command/v2.0.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **github--command/v2.0.3** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference GitHub Actions using mutable tags (e.g. @v4, @v5, @v6) instead of immutable full 40-character commit SHAs. This exposes the workflow to supply-chain attacks if the tag is moved to a malicious commit.

Failing references:
- actions-config-validation.yml: `actions/checkout@v5`
- codeql-analysis.yml: `actions/checkout@v5`, `github/codeql-action/init@v4`, `github/codeql-action/autobuild@v4`, `github/codeql-action/analyze@v4`
- lint.yml: `actions/checkout@v5`, `actions/setup-node@v6`
- package-check.yml: `actions/checkout@v5`, `actions/setup-node@v6`
- test.yml: `actions/checkout@v5`, `actions/setup-node@v6`
- update-latest-release-tag.yml: `actions/checkout@v5`

Locations:

- `.github/workflows/actions-config-validation.yml:17`
- `.github/workflows/codeql-analysis.yml:24`
- `.github/workflows/codeql-analysis.yml:27`
- `.github/workflows/codeql-analysis.yml:31`
- `.github/workflows/codeql-analysis.yml:34`
- `.github/workflows/lint.yml:16`
- `.github/workflows/lint.yml:19`
- `.github/workflows/package-check.yml:18`
- `.github/workflows/package-check.yml:21`
- `.github/workflows/test.yml:16`
- `.github/workflows/test.yml:19`
- `.github/workflows/update-latest-release-tag.yml:22`

### script-injection (severity: high)

The workflow `update-latest-release-tag.yml` directly interpolates user-controlled `workflow_dispatch` inputs into `run:` shell commands via `${{ github.event.inputs.major_version_tag }}` and `${{ github.event.inputs.source_tag }}`. An attacker with permission to trigger the workflow can supply values containing shell metacharacters (e.g. `;`, `&&`, backticks) to achieve arbitrary command execution on the runner. Rule (a) violated: `${{ ... }}` expressions appear directly inside `run:` blocks.

Offending lines:
- Line 32: `run: git tag -f ${{ github.event.inputs.major_version_tag }} ${{ github.event.inputs.source_tag }}`
- Line 35: `run: git push origin ${{ github.event.inputs.major_version_tag }} --force`

Fix: route inputs through `env:` variables and double-quote them in the shell script, e.g.:
```yaml
env:
  MAJOR_VERSION_TAG: ${{ github.event.inputs.major_version_tag }}
  SOURCE_TAG: ${{ github.event.inputs.source_tag }}
run: git tag -f "$MAJOR_VERSION_TAG" "$SOURCE_TAG"
```

Locations:

- `.github/workflows/update-latest-release-tag.yml:32`
- `.github/workflows/update-latest-release-tag.yml:35`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Pinned all unpinned action references to full commit SHAs: actions/checkout@v5 → fbc6f3992d24b796d5a048ff273f7fcc4a7b6c09, actions/setup-node@v6 → 249970729cb0ef3589644e2896645e5dc5ba9c38, github/codeql-action/{init,autobuild,analyze}@v4 → e0647621c2984b5ed2f768cb892365bf2a616ad1. Fixed script injection in update-latest-release-tag.yml by moving github.event.inputs.major_version_tag and github.event.inputs.source_tag into env: blocks and referencing them as double-quoted shell variables ($MAJOR_VERSION_TAG, $SOURCE_TAG) in the run: steps.

