# Hardening Report: rmeneely--update-yaml/v1.0.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `ff50f15e4b79bfbf764dafdfd2579175a6ea9771`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **rmeneely--update-yaml/v1.0.3** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Two run: steps in action.yml directly interpolate attacker-controlled expressions into shell commands without first assigning them to environment variables. In the first step (line 27), `${{ inputs.infile }}`, `${{ inputs.varlist }}`, and `${{ github.action_path }}` are embedded directly in the shell command string. In the second step (line 29), `${{ inputs.infile }}` and `${{ github.action_path }}` are again directly interpolated. An attacker who controls the `infile` or `varlist` inputs can inject arbitrary shell commands (e.g., by passing a value like `; malicious-command #`). These values must be assigned to environment variables via `env:` and referenced as `$ENV_VAR` in the run block.

Locations:

- `action.yml:27`
- `action.yml:29`

### unpinned-uses (severity: high)

The action uses `actions/setup-python@v4` (line 23), which references a mutable tag (`@v4`) rather than a full 40-character commit SHA. A mutable tag can be silently moved to point to a different (potentially malicious) commit, enabling a supply-chain attack. It should be pinned to a specific SHA, e.g., `actions/setup-python@a26af69be951a213d495a4c3e4e4022e16d87065 # v4`.

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

Fixed three categories of findings in action.yml: (1) Pinned actions/setup-python from mutable tag @v4 to full SHA @7f4fc3e22c37d6ff65e88745f38bd3157c663f7c # v4. (2) Moved all ${{ inputs.infile }}, ${{ inputs.varlist }}, and ${{ github.action_path }} expressions from both run: blocks into env: blocks (as INFILE, VARLIST, ACTION_PATH), referencing them as plain shell variables with proper quoting in the run scripts to prevent shell injection attacks.

