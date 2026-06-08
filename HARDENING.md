# Hardening Report: rmeneely--update-yaml/v1.0.5

> This file was generated automatically by the hardening agent.

**Policy SHA:** `ff50f15e4b79bfbf764dafdfd2579175a6ea9771`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **rmeneely--update-yaml/v1.0.5** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Both `run:` blocks in action.yml directly interpolate attacker-controlled expressions into shell command strings without first assigning them to environment variables. In the first run block (line 25), `${{ inputs.infile }}`, `${{ inputs.varlist }}`, and `${{ github.action_path }}` are interpolated directly into the shell command: `python ${{ github.action_path }}/update-yaml.py -i ${{ inputs.infile }} -V "${{ inputs.varlist }}" > ${{ github.action_path }}/.update-yaml.tmp`. In the second run block (line 27), `${{ inputs.infile }}` and `${{ github.action_path }}` are interpolated directly into: `diff ${{ inputs.infile }} ${{ github.action_path }}/.update-yaml.tmp` and `mv ${{ github.action_path }}/.update-yaml.tmp ${{ inputs.infile }}`. An attacker can supply a malicious value for `inputs.infile` or `inputs.varlist` (e.g., containing shell metacharacters) to execute arbitrary commands. These should be passed via `env:` variables instead.

Locations:

- `action.yml:25`
- `action.yml:27`

### unpinned-uses (severity: high)

The action references `actions/setup-python@v4` using a mutable version tag (`@v4`) instead of a pinned 40-character commit SHA. A mutable tag can be moved to point to a different (potentially malicious) commit, enabling supply-chain attacks. It should be pinned to a full SHA, e.g., `actions/setup-python@a26af69be951a213d495a4c3e4e4022e16d87065 # v4`.

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

Fixed action.yml: (1) Pinned actions/setup-python@v4 to full SHA 7f4fc3e22c37d6ff65e88745f38bd3157c663f7c. (2) Moved all ${{ inputs.infile }}, ${{ inputs.varlist }}, and ${{ github.action_path }} expressions out of both run: blocks into env: blocks (as INFILE, VARLIST, ACTION_PATH), then referenced them as quoted shell variables to prevent script injection via shell metacharacters.

