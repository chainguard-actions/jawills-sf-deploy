# Hardening Report: jawills--sf-deploy/v0.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `ff50f15e4b79bfbf764dafdfd2579175a6ea9771`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **jawills--sf-deploy/v0.4** was hardened automatically. 7 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

The 'Login to Environment' step directly interpolates `${{ inputs.SFDX_AUTH_URL }}` into the shell command string inside a run: block. An attacker-controlled input value is embedded directly in the shell command rather than being passed via an env: variable, enabling script injection.

Locations:

- `action.yml:39`

### script-injection (severity: high)

The 'Generate package.xml' step directly interpolates `${{ inputs.SOURCE_DIRECTORY }}` into the shell command string inside a run: block. An attacker-controlled input value is embedded directly in the shell command rather than being passed via an env: variable, enabling script injection.

Locations:

- `action.yml:43`

### script-injection (severity: high)

The 'Deploy to Environment' step directly interpolates `${{ inputs.TEST_LEVEL }}` and `${{ inputs.DRY_RUN }}` into the shell command string inside a run: block. Attacker-controlled input values are embedded directly in the shell command rather than being passed via env: variables, enabling script injection.

Locations:

- `action.yml:50`
- `action.yml:52`

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

Fixed all 7 script injection findings in action.yml by moving ${{ inputs.* }} expressions from run: blocks into env: blocks for each affected step:

1. 'Login to Environment' step: Moved `${{ inputs.SFDX_AUTH_URL }}` to env block as `SFDX_AUTH_URL`, updated run: to use `$SFDX_AUTH_URL`.
2. 'Generate package.xml' step: Moved `${{ inputs.SOURCE_DIRECTORY }}` to env block as `SOURCE_DIRECTORY`, updated run: to use `"$SOURCE_DIRECTORY"` (quoted for safety).
3. 'Deploy to Environment' step: Moved `${{ inputs.TEST_LEVEL }}` and `${{ inputs.DRY_RUN }}` to env block as `TEST_LEVEL` and `DRY_RUN`, updated run: to use `"$TEST_LEVEL"` and `"$DRY_RUN"` respectively.

No other findings (unpinned-uses, missing-permissions) were present in the findings list.

