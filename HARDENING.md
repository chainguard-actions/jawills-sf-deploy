<!-- markdownlint-disable -->

# Hardening Report: jawills--sf-deploy/v0.5

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **jawills--sf-deploy/v0.5** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Multiple `${{ inputs.* }}` expressions are directly interpolated inside `run:` shell command strings, violating rule (a). An attacker-controlled caller workflow can supply values containing shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) that will be executed by the shell before any quoting takes effect.

1. Line 33 — `sf org login sfdx-url --set-default --sfdx-url-file <(echo "${{ inputs.SFDX_AUTH_URL }}")` — the auth URL is interpolated directly.
2. Line 37 — `sf project generate manifest --source-dir ${{ inputs.SOURCE_DIRECTORY }} --output-dir manifest` — SOURCE_DIRECTORY is interpolated directly AND unquoted (also rule b).
3. Line 44 — `--wait ${{ inputs.WAIT }}` — WAIT is interpolated directly.
4. Line 45 — `--test-level ${{ inputs.TEST_LEVEL }}` — TEST_LEVEL is interpolated directly.
5. Line 48 — `if [ "${{ inputs.DRY_RUN }}" = "true" ]` — DRY_RUN is interpolated directly.

Fix: move each input into an `env:` variable and reference the env var (double-quoted) in the shell script instead of using `${{ }}` directly in the `run:` block.

Locations:

- `action.yml:33`
- `action.yml:37`
- `action.yml:44`
- `action.yml:45`
- `action.yml:48`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.SFDX_AUTH_URL }}" appears directly in run: block of step "Login to Environment"; move to env: map

Locations:

- `action.yml:41`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.SOURCE_DIRECTORY }}" appears directly in run: block of step "Generate package.xml"; move to env: map

Locations:

- `action.yml:45`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.WAIT }}" appears directly in run: block of step "Deploy to Environment"; move to env: map

Locations:

- `action.yml:51`

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

Fixed all script injection findings in action.yml by moving all ${{ inputs.* }} expressions into env: blocks and referencing them as double-quoted environment variables in the shell scripts:

1. 'Login to Environment' step: Added `env: SFDX_AUTH_URL: ${{ inputs.SFDX_AUTH_URL }}` and replaced `${{ inputs.SFDX_AUTH_URL }}` with `"$SFDX_AUTH_URL"` in the run block.

2. 'Generate package.xml' step: Added `env: SOURCE_DIRECTORY: ${{ inputs.SOURCE_DIRECTORY }}` and replaced the unquoted `${{ inputs.SOURCE_DIRECTORY }}` with `"$SOURCE_DIRECTORY"` (also fixing the missing quotes issue).

3. 'Deploy to Environment' step: Added `env:` block with WAIT, TEST_LEVEL, and DRY_RUN variables mapped from their respective inputs, and replaced all three `${{ inputs.* }}` expressions with double-quoted `"$WAIT"`, `"$TEST_LEVEL"`, and `"$DRY_RUN"` references in the shell script.

