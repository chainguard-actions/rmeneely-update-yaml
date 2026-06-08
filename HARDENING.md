# Hardening Report: rmeneely--update-yaml/v1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `ff50f15e4b79bfbf764dafdfd2579175a6ea9771`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **rmeneely--update-yaml/v1** was hardened automatically. 7 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Both `run:` blocks in action.yml directly interpolate attacker-controlled expressions `${{ inputs.infile }}` and `${{ inputs.varlist }}` inside shell command strings, without first assigning them to environment variables. An attacker can supply a malicious value for `inputs.infile` or `inputs.varlist` (e.g. containing shell metacharacters or command substitution) to execute arbitrary commands on the runner. The fix is to pass these values via `env:` and reference them as `$INFILE` / `$VARLIST` in the shell script.

Locations:

- `action.yml:27`
- `action.yml:30`

### unpinned-uses (severity: high)

The action references `actions/setup-python@v4` using a mutable version tag (`@v4`) instead of a full 40-character commit SHA. A tag can be moved to point to a different (potentially malicious) commit, enabling a supply-chain attack. Pin to a specific SHA, e.g. `actions/setup-python@a26af69be951a213d495a4c3e4e4022e16d87065 # v4`.

Locations:

- `action.yml:23`

### github-env-injection (severity: high)

The second `run:` step writes to `$GITHUB_OUTPUT` (line 32) and also directly interpolates the attacker-controlled expression `${{ inputs.infile }}` (lines 30 and 33) in the same shell block without sanitization (no `printf '%s' ... | tr -d '\n\r'` step). An attacker-controlled `inputs.infile` value containing newlines could inject arbitrary key=value pairs into `$GITHUB_OUTPUT`, potentially poisoning downstream steps. The value must be sanitized before use in any step that writes to special environment files.

Locations:

- `action.yml:30`
- `action.yml:32`

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

**Fixes applied:** unpinned-uses, script-injection, github-env-injection, static-inline-injection

**Notes:**

Fixed action.yml with three categories of changes: (1) Pinned actions/setup-python@v4 to full commit SHA 7f4fc3e22c37d6ff65e88745f38bd3157c663f7c. (2) Moved attacker-controlled inputs (inputs.infile, inputs.varlist) out of run: shell strings into env: blocks as INFILE and VARLIST, eliminating shell injection risk. (3) Added newline sanitization for INFILE via `printf '%s' "$INFILE" | tr -d '\n\r'` before using it in file operations and before writing to $GITHUB_OUTPUT, preventing github-env-injection.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed script injection in action.yml by moving `${{ github.action_path }}` out of both `run:` shell blocks and into their respective `env:` blocks as `ACTION_PATH`. Step 2 now uses `$ACTION_PATH/update-yaml.py` and `$ACTION_PATH/.update-yaml.tmp`, and Step 3 now uses `$ACTION_PATH/.update-yaml.tmp` in both the `diff` and `mv` commands — all referencing the plain environment variable instead of directly interpolating the GitHub expression into the shell string.

