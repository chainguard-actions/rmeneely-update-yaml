<!-- markdownlint-disable -->

# Hardening Report: rmeneely--update-yaml/v1.0.5

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **rmeneely--update-yaml/v1.0.5** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action uses `actions/setup-python@v4`, which is pinned to a mutable tag (`@v4`) rather than an immutable 40-character commit SHA. This means the dependency can be silently changed by the upstream repository, enabling supply-chain attacks.

Locations:

- `action.yml:23`

### script-injection (severity: high)

Two `run:` blocks directly interpolate `${{ ... }}` expressions inside shell command strings (sub-rule a). This allows script injection because the YAML template substitution happens before the shell parses the command, so an attacker-controlled value can inject arbitrary shell metacharacters.

1. Line 26: `python ${{ github.action_path }}/update-yaml.py -i ${{ inputs.infile }} -V "${{ inputs.varlist }}" > ${{ github.action_path }}/.update-yaml.tmp` — `inputs.infile` and `inputs.varlist` are caller-controlled and interpolated directly into the shell command.

2. Line 28: `updated=$((diff ${{ inputs.infile }} ${{ github.action_path }}/.update-yaml.tmp || true) | wc -l ...)` and `mv ${{ github.action_path }}/.update-yaml.tmp ${{ inputs.infile }}` — again `inputs.infile` is interpolated directly.

Fix: move all `${{ inputs.* }}` values into `env:` variables and reference them as double-quoted shell variables (e.g., `"$INFILE"`, `"$VARLIST"`).

Locations:

- `action.yml:26`
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

Fixed action.yml: (1) Pinned actions/setup-python@v4 to full commit SHA 7f4fc3e22c37d6ff65e88745f38bd3157c663f7c with # v4 comment. (2) Moved all ${{ inputs.infile }}, ${{ inputs.varlist }}, and ${{ github.action_path }} expressions from both run: blocks into env: blocks (ACTION_PATH, INFILE, VARLIST), referencing them as double-quoted shell variables to prevent script injection.

