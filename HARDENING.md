# Hardening Report: rmeneely--update-yaml/v1.0.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `ff50f15e4b79bfbf764dafdfd2579175a6ea9771`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **rmeneely--update-yaml/v1.0.2** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Two `run:` steps in action.yml directly interpolate attacker-controlled expressions into shell commands without first assigning them to environment variables. Specifically, `${{ inputs.infile }}` and `${{ inputs.varlist }}` (both user-supplied inputs) are embedded directly in shell command strings. A malicious caller could supply values like `; rm -rf /` or use newlines/special characters to inject arbitrary shell commands. The fix is to assign these values to `env:` variables and reference them as `$INFILE` / `$VARLIST` in the shell script.

Locations:

- `action.yml:26`
- `action.yml:28`

### unpinned-uses (severity: high)

The action references `actions/setup-python@v4` using a mutable tag (`v4`) instead of a pinned 40-character commit SHA. A tag can be moved to point to a different (potentially malicious) commit at any time, enabling a supply-chain attack. It should be pinned to a full SHA, e.g. `actions/setup-python@a26af69be951a213d495a4c3e4e4022e16d87065 # v4`.

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

Fixed action.yml: (1) Pinned actions/setup-python@v4 to full SHA 7f4fc3e22c37d6ff65e88745f38bd3157c663f7c. (2) Moved all ${{ inputs.infile }}, ${{ inputs.varlist }}, and ${{ github.action_path }} expressions from run: blocks into env: blocks (as INFILE, VARLIST, ACTION_PATH), and updated shell scripts to reference them as plain environment variables to prevent script injection.

