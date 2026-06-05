# Hardening Report: rmeneely--update-yaml/v1.0.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `ff50f15e4b79bfbf764dafdfd2579175a6ea9771`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **rmeneely--update-yaml/v1.0.3** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Two `run:` blocks in action.yml directly interpolate attacker-controlled expressions into shell commands without first assigning them to environment variables. `${{ inputs.infile }}` and `${{ inputs.varlist }}` are caller-supplied inputs that can contain shell metacharacters or newlines, enabling arbitrary command injection. Line 26: `python ${{ github.action_path }}/update-yaml.py -i ${{ inputs.infile }} -V "${{ inputs.varlist }}" > ...`. Line 28: `diff ${{ inputs.infile }} ${{ github.action_path }}/.update-yaml.tmp` and `mv ${{ github.action_path }}/.update-yaml.tmp ${{ inputs.infile }}`. These should be passed via `env:` variables and referenced as `$ENV_VAR` in the shell.

Locations:

- `action.yml:26`
- `action.yml:28`

### unpinned-uses (severity: high)

The composite action references `actions/setup-python@v4` using a mutable version tag (`@v4`) instead of a full 40-character commit SHA. This means the action could be silently updated to a different (potentially malicious) version without the workflow author's knowledge, creating a supply-chain risk. It should be pinned to a specific SHA, e.g. `actions/setup-python@a26af69be951a213d495a4c3e4e4022e16d87065 # v4`.

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

Fixed all findings in action.yml:
1. Pinned `actions/setup-python@v4` to full SHA `7f4fc3e22c37d6ff65e88745f38bd3157c663f7c` with `# v4` comment.
2. Moved all `${{ inputs.infile }}`, `${{ inputs.varlist }}`, and `${{ github.action_path }}` expressions out of both `run:` blocks into `env:` blocks (as INFILE, VARLIST, ACTION_PATH respectively). Shell scripts now reference these as plain environment variables ($INFILE, $VARLIST, $ACTION_PATH), eliminating all script injection vectors.

