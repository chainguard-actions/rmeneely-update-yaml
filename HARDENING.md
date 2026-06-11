<!-- markdownlint-disable -->

# Hardening Report: rmeneely--update-yaml/v1.0.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **rmeneely--update-yaml/v1.0.2** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Two run: blocks in action.yml directly interpolate GitHub Actions expressions into shell command strings (sub-rule a). In the first run step (line 26), `${{ inputs.infile }}`, `${{ inputs.varlist }}`, and `${{ github.action_path }}` are embedded directly in the shell command: `python ${{ github.action_path }}/update-yaml.py -i ${{ inputs.infile }} -V "${{ inputs.varlist }}" > ${{ github.action_path }}/.update-yaml.tmp`. In the second run step (line 29), `${{ inputs.infile }}` and `${{ github.action_path }}` are again embedded directly: `updated=$((diff ${{ inputs.infile }} ${{ github.action_path }}/.update-yaml.tmp || true) | wc -l ...)` and `mv ${{ github.action_path }}/.update-yaml.tmp ${{ inputs.infile }}`. The `inputs.infile` and `inputs.varlist` values are fully attacker-controlled and can contain shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) that will be interpreted by bash before any quoting takes effect. These must be moved to env: variables and then double-quoted in the shell script.

Locations:

- `action.yml:26`
- `action.yml:29`

### unpinned-uses (severity: high)

The composite action references `actions/setup-python@v4` using a mutable version tag instead of a pinned 40-character commit SHA. A tag can be moved to point to a different (potentially malicious) commit at any time, enabling a supply-chain attack. It should be pinned to a full SHA, e.g. `actions/setup-python@a26af69be951a213d495a4c3e4e4022e16d87065 # v4`.

Locations:

- `action.yml:23`

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

Fixed three categories of findings in action.yml: (1) Pinned actions/setup-python from mutable tag @v4 to full commit SHA @7f4fc3e22c37d6ff65e88745f38bd3157c663f7c with # v4 comment. (2) Moved all ${{ inputs.infile }}, ${{ inputs.varlist }}, and ${{ github.action_path }} expressions out of both run: blocks into env: blocks (as INFILE, VARLIST, ACTION_PATH), then double-quoted all variable references in the shell scripts to prevent shell metacharacter injection attacks.

