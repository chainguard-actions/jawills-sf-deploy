<!-- markdownlint-disable -->

# Hardening Report: jawills--sf-deploy/v0.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **jawills--sf-deploy/v0.4** was hardened automatically. 5 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Multiple `run:` blocks in action.yml directly interpolate `${{ inputs.* }}` expressions into shell commands (rule a). This allows an attacker who controls the input values to inject arbitrary shell commands.

1. "Login to Environment" step: `sf org login sfdx-url --set-default --sfdx-url-file <(echo "${{ inputs.SFDX_AUTH_URL }}")` — the auth URL is interpolated directly into the shell command.
2. "Generate package.xml" step: `sf project generate manifest --source-dir ${{ inputs.SOURCE_DIRECTORY }} --output-dir manifest` — SOURCE_DIRECTORY is interpolated unquoted.
3. "Deploy to Environment" step: `--test-level ${{ inputs.TEST_LEVEL }}` — TEST_LEVEL is interpolated unquoted into an array element.
4. "Deploy to Environment" step: `if [ "${{ inputs.DRY_RUN }}" = "true" ]` — DRY_RUN is interpolated inside a test expression.

Fix: move each input into an `env:` block and reference it as a quoted shell variable (e.g. `"$SOURCE_DIRECTORY"`).

Locations:

- `action.yml:38`
- `action.yml:43`
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

Fixed all 5 findings (1 script-injection + 4 static-inline-injection) in action.yml by moving ${{ inputs.* }} expressions into env: blocks for each affected step:

1. 'Login to Environment' step: moved `${{ inputs.SFDX_AUTH_URL }}` to `env: SFDX_AUTH_URL:` and changed the run command to use `"$SFDX_AUTH_URL"`.
2. 'Generate package.xml' step: moved `${{ inputs.SOURCE_DIRECTORY }}` to `env: SOURCE_DIRECTORY:` and changed the run command to use `"$SOURCE_DIRECTORY"`.
3. 'Deploy to Environment' step: moved both `${{ inputs.TEST_LEVEL }}` and `${{ inputs.DRY_RUN }}` to `env:` block and changed the run script to reference `"$TEST_LEVEL"` and `"$DRY_RUN"` as quoted shell variables.

