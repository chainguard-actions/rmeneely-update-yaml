<!-- markdownlint-disable -->

# Hardening Report: rmeneely--update-yaml/v1.0.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **rmeneely--update-yaml/v1.0.2** was hardened automatically. 7 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The composite action uses `actions/setup-python@v4`, which is pinned to a mutable version tag rather than an immutable 40-character commit SHA. If the tag is moved (e.g. by a supply-chain compromise), the action will silently execute different code. Fix: pin to a full SHA, e.g. `actions/setup-python@a26af69be951a213d495a4c3e4e4022e16d87065 # v4`.

Locations:

- `action.yml:23`

### script-injection (severity: high)

Sub-rule (a): Multiple GitHub Actions expressions are interpolated directly into the `run:` shell command string. `${{ inputs.infile }}` and `${{ inputs.varlist }}` are attacker-controlled inputs that are substituted verbatim into the shell before it executes, enabling command injection (e.g. a value like `; malicious-cmd #` in `inputs.infile`). `${{ github.action_path }}` is also interpolated directly. Offending line: `python ${{ github.action_path }}/update-yaml.py -i ${{ inputs.infile }} -V "${{ inputs.varlist }}" > ${{ github.action_path }}/.update-yaml.tmp`. Fix: move all expressions into `env:` variables and reference them as double-quoted shell variables (`"$INPUT_INFILE"`, `"$INPUT_VARLIST"`, `"$GITHUB_ACTION_PATH"`).

Locations:

- `action.yml:26`

### script-injection (severity: high)

Sub-rule (a): The second `run:` block also interpolates `${{ inputs.infile }}` and `${{ github.action_path }}` directly into shell commands. `${{ inputs.infile }}` appears unquoted in both a `diff` command and a `mv` command, allowing an attacker-controlled filename to inject shell metacharacters. Offending lines include: `updated=$((diff ${{ inputs.infile }} ${{ github.action_path }}/.update-yaml.tmp || true) | wc -l ...)` and `mv ${{ github.action_path }}/.update-yaml.tmp ${{ inputs.infile }}`. Fix: move expressions into `env:` variables and double-quote all shell expansions.

Locations:

- `action.yml:28`

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

1. Pinned actions/setup-python@v4 to full commit SHA 7f4fc3e22c37d6ff65e88745f38bd3157c663f7c with # v4 comment. 2. In the first run: block, moved ${{ github.action_path }}, ${{ inputs.infile }}, and ${{ inputs.varlist }} into env: variables (ACTION_PATH, INPUT_INFILE, INPUT_VARLIST) and double-quoted all shell expansions. 3. In the second run: block, moved ${{ github.action_path }} and ${{ inputs.infile }} into env: variables (ACTION_PATH, INPUT_INFILE) and double-quoted all shell expansions in the diff, mv, and other commands.

