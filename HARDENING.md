<!-- markdownlint-disable -->

# Hardening Report: rmeneely--update-yaml/v1.0.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **rmeneely--update-yaml/v1.0.3** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action uses `actions/setup-python@v4`, which is pinned to a mutable tag (`v4`) rather than an immutable 40-character commit SHA. A tag can be moved to point to a different (potentially malicious) commit at any time, enabling a supply-chain attack. It should be pinned to a full SHA, e.g. `actions/setup-python@a26af69be951a213d495a4c3e4e4022e16d87065 # v4`.

Locations:

- `action.yml:22`

### script-injection (severity: high)

Sub-rule (a): Two `run:` blocks directly interpolate GitHub Actions expressions inside shell command strings without routing them through env vars first.

**Step 1 (line 24):** `python ${{ github.action_path }}/update-yaml.py -i ${{ inputs.infile }} -V "${{ inputs.varlist }}" > ${{ github.action_path }}/.update-yaml.tmp` — `inputs.infile` and `inputs.varlist` are caller-controlled and are interpolated directly into the shell command. A value like `; malicious-command #` in `inputs.infile` would execute arbitrary shell code. `github.action_path` is also a context expression that passes through YAML template substitution before the shell processes it.

**Step 2 (line 26):** `diff ${{ inputs.infile }} ${{ github.action_path }}/.update-yaml.tmp` and `mv ${{ github.action_path }}/.update-yaml.tmp ${{ inputs.infile }}` — same issue: `inputs.infile` is interpolated directly into the shell command string, allowing command injection.

Fix: move all expressions into `env:` variables and reference them as double-quoted shell variables, e.g. `env:\n  INFILE: ${{ inputs.infile }}` and then use `"$INFILE"` in the run script.

Locations:

- `action.yml:24`
- `action.yml:26`

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

Fixed action.yml: (1) Pinned actions/setup-python@v4 to full SHA 7f4fc3e22c37d6ff65e88745f38bd3157c663f7c. (2) Moved all ${{ inputs.infile }}, ${{ inputs.varlist }}, and ${{ github.action_path }} expressions out of run: shell strings into env: blocks (INFILE, VARLIST, ACTION_PATH), then referenced them as double-quoted shell variables to prevent script/command injection.

