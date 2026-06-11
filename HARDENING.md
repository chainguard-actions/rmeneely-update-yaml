<!-- markdownlint-disable -->

# Hardening Report: rmeneely--update-yaml/v1.0.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **rmeneely--update-yaml/v1.0.3** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action uses `actions/setup-python@v4`, which is pinned to a mutable tag rather than an immutable 40-character commit SHA. This means the referenced action could be silently changed by the upstream maintainer, enabling supply-chain attacks.

Locations:

- `action.yml:23`

### script-injection (severity: high)

Rule (a) violation: Multiple `${{ ... }}` expressions are interpolated directly inside `run:` shell command strings without going through an env: variable or any quoting protection.

Step at line 26:
  `python ${{ github.action_path }}/update-yaml.py -i ${{ inputs.infile }} -V "${{ inputs.varlist }}" > ${{ github.action_path }}/.update-yaml.tmp`
  — `inputs.infile` and `inputs.varlist` are attacker-controlled inputs injected directly into the shell command. Even `github.action_path` flows through YAML template substitution before the shell sees it.

Step at line 28:
  `updated=$((diff ${{ inputs.infile }} ${{ github.action_path }}/.update-yaml.tmp || true) | wc -l ...) ; ... mv ${{ github.action_path }}/.update-yaml.tmp ${{ inputs.infile }}`
  — Again, `inputs.infile` is directly interpolated, allowing an attacker to inject arbitrary shell commands via a crafted filename (e.g., `; malicious-command #`).

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

1. Pinned `actions/setup-python@v4` to its immutable SHA `7f4fc3e22c37d6ff65e88745f38bd3157c663f7c` with a `# v4` comment for readability.
2. Moved all `${{ github.action_path }}`, `${{ inputs.infile }}`, and `${{ inputs.varlist }}` expressions from both `run:` blocks into `env:` blocks (as ACTION_PATH, INFILE, VARLIST respectively), then referenced them as properly double-quoted shell variables to prevent script injection.
3. Also replaced the deprecated `::set-output` workflow command with the modern `$GITHUB_OUTPUT` file approach.

