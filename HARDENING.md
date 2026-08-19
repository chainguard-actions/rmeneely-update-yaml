<!-- markdownlint-disable -->

# Hardening Report: rmeneely--update-yaml/v1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **rmeneely--update-yaml/v1** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The composite action uses `actions/setup-python@v4`, which is pinned to a mutable version tag rather than an immutable 40-character commit SHA. This means the action could silently change if the tag is moved, enabling supply-chain attacks. It should be pinned to a full SHA, e.g. `actions/setup-python@<40-char-sha> # v4`.

Locations:

- `action.yml:23`

### script-injection (severity: high)

Rule (a): Multiple `${{ ... }}` expressions are interpolated directly inside `run:` shell command strings, bypassing shell quoting and allowing script injection.

Step 1 (line 25): `python ${{ github.action_path }}/update-yaml.py -i ${{ inputs.infile }} -V "${{ inputs.varlist }}" > ${{ github.action_path }}/.update-yaml.tmp` — `inputs.infile` and `inputs.varlist` are attacker-controlled inputs injected directly into the shell command. A value like `; malicious-command #` in `inputs.infile` would execute arbitrary code.

Step 2 (line 27): `updated=$((diff ${{ inputs.infile }} ${{ github.action_path }}/.update-yaml.tmp || true) | wc -l ...)` and `mv ${{ github.action_path }}/.update-yaml.tmp ${{ inputs.infile }}` — again `inputs.infile` is interpolated directly into the shell, enabling injection. All `${{ ... }}` values must be moved to `env:` variables and then double-quoted in the shell script.

Locations:

- `action.yml:25`
- `action.yml:27`

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

Fixed all findings in hardened/action/action.yml:
1. unpinned-uses: Pinned `actions/setup-python@v4` to full SHA `7f4fc3e22c37d6ff65e88745f38bd3157c663f7c` with `# v4` comment.
2. script-injection / static-inline-injection (lines 25, 27, 28, 30): Moved all `${{ github.action_path }}`, `${{ inputs.infile }}`, and `${{ inputs.varlist }}` expressions out of `run:` shell strings and into `env:` blocks as `ACTION_PATH`, `INFILE`, and `VARLIST` respectively. All shell references are now properly double-quoted to prevent word splitting and injection attacks.

