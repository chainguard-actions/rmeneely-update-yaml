# Hardening Report: rmeneely--update-yaml/v1.0.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `ff50f15e4b79bfbf764dafdfd2579175a6ea9771`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **rmeneely--update-yaml/v1.0.4** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml references `actions/setup-python@v4` using a mutable tag (`@v4`) instead of a pinned 40-character commit SHA. This means the action could be silently updated to a different (potentially malicious) version without the workflow author's knowledge, creating a supply-chain risk.

Locations:

- `action.yml:22`

### script-injection (severity: high)

Two `run:` steps in action.yml directly interpolate attacker-controlled `inputs.*` expressions inside shell command strings without first assigning them to environment variables. Specifically:

1. Step 2 (run: python ...): `${{ inputs.infile }}` and `${{ inputs.varlist }}` are interpolated directly into the shell command — an attacker can supply values containing shell metacharacters (e.g. `; rm -rf /`) to execute arbitrary commands.
2. Step 3 (run: updated=...): `${{ inputs.infile }}` is again interpolated directly into shell commands (`diff` and `mv`), allowing the same injection.

Fix: assign inputs to environment variables (via `env:`) and reference them as `$INFILE` / `$VARLIST` in the shell script.

Locations:

- `action.yml:23`
- `action.yml:25`

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

Fixed action.yml: (1) Pinned actions/setup-python@v4 to full SHA 7f4fc3e22c37d6ff65e88745f38bd3157c663f7c. (2) Moved all ${{ inputs.infile }}, ${{ inputs.varlist }}, and ${{ github.action_path }} expressions from run: blocks into env: blocks (INFILE, VARLIST, ACTION_PATH), referencing them as plain shell variables to eliminate script injection risk.

