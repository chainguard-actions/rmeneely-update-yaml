# Hardening Report: rmeneely--update-yaml/v1.0.5

> This file was generated automatically by the hardening agent.

**Policy SHA:** `ff50f15e4b79bfbf764dafdfd2579175a6ea9771`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **rmeneely--update-yaml/v1.0.5** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Two `run:` blocks in action.yml directly interpolate attacker-controlled expressions inside shell command strings without first assigning them to environment variables.

Step 1 (line ~22): `python ${{ github.action_path }}/update-yaml.py -i ${{ inputs.infile }} -V "${{ inputs.varlist }}" > ${{ github.action_path }}/.update-yaml.tmp` — `inputs.infile` and `inputs.varlist` are caller-supplied and interpolated directly into the shell command, enabling shell injection via crafted values (e.g. a varlist containing `; malicious-command`).

Step 2 (line ~24): `diff ${{ inputs.infile }} ${{ github.action_path }}/.update-yaml.tmp` and `mv ${{ github.action_path }}/.update-yaml.tmp ${{ inputs.infile }}` — again `inputs.infile` is interpolated directly into the shell command.

Fix: assign all expressions to `env:` variables and reference them as `$VAR` in the shell script.

Locations:

- `action.yml:22`
- `action.yml:24`

### unpinned-uses (severity: high)

The composite action uses `actions/setup-python@v4`, which references a mutable tag (`@v4`) rather than a pinned 40-character commit SHA. A tag can be moved to point to a different (potentially malicious) commit at any time, creating a supply-chain risk. It should be pinned to a full SHA, e.g. `actions/setup-python@a26af69be951a213d495a4c3e4e4022e16d87065 # v4`.

Locations:

- `action.yml:21`

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

Fixed action.yml: (1) Pinned actions/setup-python@v4 to full SHA @7f4fc3e22c37d6ff65e88745f38bd3157c663f7c # v4. (2) In step 1 (python invocation), moved ${{ github.action_path }}, ${{ inputs.infile }}, and ${{ inputs.varlist }} to env: block as ACTION_PATH, INFILE, VARLIST and referenced them as $ACTION_PATH, $INFILE, $VARLIST in the shell script. (3) In step 2 (diff/mv), moved ${{ github.action_path }} and ${{ inputs.infile }} to env: block as ACTION_PATH and INFILE and referenced them as $ACTION_PATH and $INFILE in the shell script. All shell variables are quoted to prevent word-splitting issues.

