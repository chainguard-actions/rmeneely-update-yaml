# Hardening Report: rmeneely--update-yaml/v1.0.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `ff50f15e4b79bfbf764dafdfd2579175a6ea9771`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **rmeneely--update-yaml/v1.0.4** was hardened automatically. 7 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

The first `run:` block (line 26) directly interpolates `${{ inputs.infile }}`, `${{ inputs.varlist }}`, and `${{ github.action_path }}` into the shell command string: `python ${{ github.action_path }}/update-yaml.py -i ${{ inputs.infile }} -V "${{ inputs.varlist }}" > ${{ github.action_path }}/.update-yaml.tmp`. The `inputs.*` values are attacker-controlled and can contain shell metacharacters, enabling arbitrary command injection. These must be passed via `env:` variables and referenced as `$VAR` in the shell script.

Locations:

- `action.yml:26`

### script-injection (severity: high)

The second `run:` block (line 28) directly interpolates `${{ inputs.infile }}` and `${{ github.action_path }}` into the shell command string: `updated=$((diff ${{ inputs.infile }} ${{ github.action_path }}/.update-yaml.tmp || true) | wc -l ...)` and `mv ${{ github.action_path }}/.update-yaml.tmp ${{ inputs.infile }}`. The `inputs.infile` value is attacker-controlled and can contain shell metacharacters, enabling arbitrary command injection. These must be passed via `env:` variables and referenced as `$VAR` in the shell script.

Locations:

- `action.yml:28`

### unpinned-uses (severity: high)

The `uses:` reference `actions/setup-python@v4` (line 23) uses a mutable version tag (`@v4`) instead of a full 40-character commit SHA. This is vulnerable to supply-chain attacks if the tag is moved to point to a different, potentially malicious commit. It should be pinned to a specific SHA, e.g. `actions/setup-python@<40-char-sha> # v4`.

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

Fixed all findings in action.yml: (1) Pinned actions/setup-python@v4 to full SHA 7f4fc3e22c37d6ff65e88745f38bd3157c663f7c with # v4 comment. (2) Moved all ${{ inputs.infile }}, ${{ inputs.varlist }}, and ${{ github.action_path }} expressions from both run: blocks into env: blocks (as INFILE, VARLIST, ACTION_PATH), referencing them as plain shell variables in the scripts to prevent script injection attacks.

