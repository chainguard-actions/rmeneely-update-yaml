<!-- markdownlint-disable -->

# Hardening Report: rmeneely--update-yaml/v1.0.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **rmeneely--update-yaml/v1.0.4** was hardened automatically. 7 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action uses `actions/setup-python@v4`, which is pinned to a mutable tag (`v4`) rather than a full 40-character commit SHA. A tag can be moved to point to a different (potentially malicious) commit at any time, enabling a supply-chain attack.

Locations:

- `action.yml:23`

### script-injection (severity: high)

Sub-rule (a): The first `run:` block (line 26) directly interpolates GitHub Actions expressions into the shell command string: `${{ github.action_path }}`, `${{ inputs.infile }}`, and `${{ inputs.varlist }}`. The attacker-controlled inputs `inputs.infile` and `inputs.varlist` are substituted by the Actions runner before the shell ever sees them, allowing an attacker to inject arbitrary shell commands. For example, a value like `; rm -rf / #` for `inputs.infile` would be executed verbatim. The full offending line is: `python ${{ github.action_path }}/update-yaml.py -i ${{ inputs.infile }} -V "${{ inputs.varlist }}" > ${{ github.action_path }}/.update-yaml.tmp`

Locations:

- `action.yml:26`

### script-injection (severity: high)

Sub-rule (a): The second `run:` block (line 29) also directly interpolates `${{ inputs.infile }}` and `${{ github.action_path }}` into the shell command string. The attacker-controlled `inputs.infile` appears twice — once in the `diff` command and once in the `mv` command — without any quoting or env-var indirection. An attacker can supply a crafted filename to inject arbitrary shell commands. The offending lines include: `updated=$((diff ${{ inputs.infile }} ${{ github.action_path }}/.update-yaml.tmp || true) | wc -l ...)` and `mv ${{ github.action_path }}/.update-yaml.tmp ${{ inputs.infile }}`

Locations:

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

1. Pinned `actions/setup-python@v4` to full commit SHA `7f4fc3e22c37d6ff65e88745f38bd3157c663f7c` with `# v4` comment for readability.
2. Moved all `${{ github.action_path }}`, `${{ inputs.infile }}`, and `${{ inputs.varlist }}` expressions out of both `run:` blocks into `env:` maps (`ACTION_PATH`, `INFILE`, `VARLIST`). Shell scripts now reference these as properly double-quoted environment variables, preventing script injection.
3. Also modernized the deprecated `::set-output::` command to use `$GITHUB_OUTPUT` file append syntax.

