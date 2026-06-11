<!-- markdownlint-disable -->

# Hardening Report: jawills--sf-deploy/v0.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **jawills--sf-deploy/v0.4** was hardened automatically. 5 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Multiple `run:` blocks in action.yml directly interpolate `${{ inputs.* }}` expressions into shell commands (rule a), allowing an attacker-controlled value to execute arbitrary shell code.

1. **Login to Environment** (line ~36): `sf org login sfdx-url --set-default --sfdx-url-file <(echo "${{ inputs.SFDX_AUTH_URL }}")` — the SFDX_AUTH_URL input is interpolated directly into a process substitution.

2. **Generate package.xml** (line ~40): `sf project generate manifest --source-dir ${{ inputs.SOURCE_DIRECTORY }} --output-dir manifest` — SOURCE_DIRECTORY is interpolated unquoted into the shell command, enabling word-splitting and glob expansion as well as command injection.

3. **Deploy to Environment** (line ~46): `--test-level ${{ inputs.TEST_LEVEL }}` — TEST_LEVEL is interpolated unquoted inside a bash array literal.

4. **Deploy to Environment** (line ~49): `if [ "${{ inputs.DRY_RUN }}" = "true" ]` — DRY_RUN is interpolated inside a quoted string in a test expression.

All four occurrences must be replaced with `env:` block mappings and the resulting shell variables must be properly double-quoted.

Locations:

- `action.yml:36`
- `action.yml:40`
- `action.yml:46`
- `action.yml:49`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.SFDX_AUTH_URL }}" appears directly in run: block of step "Login to Environment"; move to env: map

Locations:

- `action.yml:41`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.SOURCE_DIRECTORY }}" appears directly in run: block of step "Generate package.xml"; move to env: map

Locations:

- `action.yml:45`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.TEST_LEVEL }}" appears directly in run: block of step "Deploy to Environment"; move to env: map

Locations:

- `action.yml:52`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.DRY_RUN }}" appears directly in run: block of step "Deploy to Environment"; move to env: map

Locations:

- `action.yml:55`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection

**Notes:**

Fixed all four script injection issues in action.yml by moving ${{ inputs.* }} expressions into env: blocks for each affected step:

1. **Login to Environment** (line ~36): Moved `${{ inputs.SFDX_AUTH_URL }}` to `env: SFDX_AUTH_URL: ${{ inputs.SFDX_AUTH_URL }}` and replaced the inline expression with `"$SFDX_AUTH_URL"` in the process substitution.

2. **Generate package.xml** (line ~40): Moved `${{ inputs.SOURCE_DIRECTORY }}` to `env: SOURCE_DIRECTORY: ${{ inputs.SOURCE_DIRECTORY }}` and replaced the unquoted inline expression with `"$SOURCE_DIRECTORY"` (properly double-quoted).

3. **Deploy to Environment** - TEST_LEVEL (line ~46): Moved `${{ inputs.TEST_LEVEL }}` to `env: TEST_LEVEL: ${{ inputs.TEST_LEVEL }}` and replaced the unquoted inline expression with `"$TEST_LEVEL"` inside the bash array literal.

4. **Deploy to Environment** - DRY_RUN (line ~49): Moved `${{ inputs.DRY_RUN }}` to `env: DRY_RUN: ${{ inputs.DRY_RUN }}` and replaced the inline expression with `"$DRY_RUN"` in the test expression.

