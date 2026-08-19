<!-- markdownlint-disable -->

# Hardening Report: rmeneely--update-yaml/v1.0.5

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **rmeneely--update-yaml/v1.0.5** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The composite action uses `actions/setup-python@v4`, which is pinned to a mutable tag (`@v4`) rather than an immutable 40-character commit SHA. This means the referenced action could be silently replaced with a different (potentially malicious) version without any change to this file.

Locations:

- `action.yml:23`

### script-injection (severity: high)

Rule (a) violation: Two `run:` blocks directly interpolate GitHub Actions expressions inside shell command strings, enabling script injection. An attacker who controls `inputs.infile` or `inputs.varlist` can inject arbitrary shell commands.

Step 1 (line 26): `python ${{ github.action_path }}/update-yaml.py -i ${{ inputs.infile }} -V "${{ inputs.varlist }}" > ${{ github.action_path }}/.update-yaml.tmp` — `inputs.infile` and `inputs.varlist` are caller-controlled and interpolated directly into the shell command before the shell parses it.

Step 2 (line 28): `updated=$((diff ${{ inputs.infile }} ${{ github.action_path }}/.update-yaml.tmp || true) | wc -l ...)` and `mv ${{ github.action_path }}/.update-yaml.tmp ${{ inputs.infile }}` — `inputs.infile` is again interpolated directly. All `${{ ... }}` expressions should be moved to `env:` variables and then referenced as double-quoted shell variables (e.g., `"$INPUT_INFILE"`).

Locations:

- `action.yml:26`
- `action.yml:28`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.infile }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:28`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.varlist }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:28`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.infile }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:30`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.infile }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:30`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, static-inline-injection

**Notes:**

Fixed all findings in hardened/action/action.yml:
1. unpinned-uses: Pinned `actions/setup-python@v4` to full commit SHA `7f4fc3e22c37d6ff65e88745f38bd3157c663f7c` with `# v4` comment.
2. script-injection / static-inline-injection (lines 26, 28, 30): Moved all `${{ github.action_path }}`, `${{ inputs.infile }}`, and `${{ inputs.varlist }}` expressions out of `run:` blocks into `env:` blocks as `ACTION_PATH`, `INPUT_INFILE`, and `INPUT_VARLIST`. All shell references are now double-quoted (`"$ACTION_PATH"`, `"$INPUT_INFILE"`, `"$INPUT_VARLIST"`) to prevent word-splitting and injection attacks.

