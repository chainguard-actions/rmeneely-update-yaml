<!-- markdownlint-disable -->

# Hardening Report: rmeneely--update-yaml/v1.0.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **rmeneely--update-yaml/v1.0.4** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The composite action uses `actions/setup-python@v4`, which is pinned to a mutable version tag rather than an immutable 40-character commit SHA. This means the referenced action can be silently changed by the upstream maintainer, enabling supply-chain attacks.

Locations:

- `action.yml:23`

### script-injection (severity: high)

Sub-rule (a): Multiple `${{ ... }}` expressions are interpolated directly inside `run:` shell command strings. In the first run block (line 26), `${{ inputs.infile }}`, `${{ inputs.varlist }}`, and `${{ github.action_path }}` are embedded directly in the shell command. `inputs.infile` and `inputs.varlist` are attacker-controlled and are passed unquoted (e.g., `-i ${{ inputs.infile }}`), allowing an attacker to inject arbitrary shell commands via a crafted input value. In the second run block (line 29), `${{ inputs.infile }}` and `${{ github.action_path }}` are again directly interpolated into the shell command, including in an unquoted `diff` argument and an unquoted `mv` destination. Any `${{ ... }}` expression inside a `run:` block is a script-injection risk regardless of context. Offending lines:
- Line 26: `python ${{ github.action_path }}/update-yaml.py -i ${{ inputs.infile }} -V "${{ inputs.varlist }}" > ${{ github.action_path }}/.update-yaml.tmp`
- Line 29: `updated=$((diff ${{ inputs.infile }} ${{ github.action_path }}/.update-yaml.tmp || true) | wc -l ...)`
- Line 32: `mv ${{ github.action_path }}/.update-yaml.tmp ${{ inputs.infile }}`

Locations:

- `action.yml:26`
- `action.yml:29`

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

Fixed action.yml with two categories of changes: (1) Pinned actions/setup-python@v4 to its immutable commit SHA 7f4fc3e22c37d6ff65e88745f38bd3157c663f7c with the tag preserved as a comment. (2) Moved all ${{ inputs.infile }}, ${{ inputs.varlist }}, and ${{ github.action_path }} expressions out of both run: shell blocks into env: maps (INFILE, VARLIST, ACTION_PATH), then referenced them as properly double-quoted shell variables in the run scripts to prevent script injection attacks.

