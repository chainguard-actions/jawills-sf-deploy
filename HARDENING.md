<!-- markdownlint-disable -->

# Hardening Report: jawills--sf-deploy/v0.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **jawills--sf-deploy/v0.3** was hardened automatically. 5 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Multiple ${{ inputs.* }} expressions are directly interpolated inside run: shell command strings in action.yml. This allows an attacker-controlled value to be injected into the shell before quoting or sanitization occurs.

1. Line 40 (Login to Environment): `sf org login sfdx-url --set-default --sfdx-url-file <(echo "${{ inputs.SFDX_AUTH_URL }}")` — SFDX_AUTH_URL is a required string input that flows directly into a shell command.
2. Line 44 (Generate package.xml): `sf project generate manifest --source-dir ${{ inputs.SOURCE_DIRECTORY }} --output-dir manifest` — SOURCE_DIRECTORY is unquoted and directly interpolated, allowing shell metacharacter injection.
3. Line 51 (Deploy to Environment): `--test-level ${{ inputs.SOURCE_DIRECTORY }}` — SOURCE_DIRECTORY again directly interpolated inside an array literal.
4. Line 53 (Deploy to Environment): `if [ "${{ inputs.DRY_RUN }}" = "true" ]` — DRY_RUN is interpolated directly into a shell conditional.

Fix: Move each input into an env: block and reference it as a quoted shell variable (e.g., "$SOURCE_DIRECTORY").

Locations:

- `action.yml:40`
- `action.yml:44`
- `action.yml:51`
- `action.yml:53`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.SFDX_AUTH_URL }}" appears directly in run: block of step "Login to Environment"; move to env: map

Locations:

- `action.yml:41`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.SOURCE_DIRECTORY }}" appears directly in run: block of step "Generate package.xml"; move to env: map

Locations:

- `action.yml:45`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.SOURCE_DIRECTORY }}" appears directly in run: block of step "Deploy to Environment"; move to env: map

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

Fixed all script injection findings in action.yml by moving ${{ inputs.* }} expressions into env: blocks for each affected step:

1. **Login to Environment**: Added `env: SFDX_AUTH_URL: ${{ inputs.SFDX_AUTH_URL }}` and changed the run command to use `"$SFDX_AUTH_URL"` instead of `"${{ inputs.SFDX_AUTH_URL }}"`.

2. **Generate package.xml**: Added `env: SOURCE_DIRECTORY: ${{ inputs.SOURCE_DIRECTORY }}` and changed the run command to use `"$SOURCE_DIRECTORY"` (also added quotes around the variable for proper shell quoting).

3. **Deploy to Environment**: Added `env:` block with both `SOURCE_DIRECTORY: ${{ inputs.SOURCE_DIRECTORY }}` and `DRY_RUN: ${{ inputs.DRY_RUN }}`, then replaced all inline expressions with `"$SOURCE_DIRECTORY"` and `"$DRY_RUN"` in the shell script.

No other files required changes. The pre-existing bug (`--wait SFDX_AUTH_URL` literal string) was left as-is since it is not a security finding.

