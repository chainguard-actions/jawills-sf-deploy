# Hardening Report: jawills--sf-deploy/v0.5

> This file was generated automatically by the hardening agent.

**Policy SHA:** `ff50f15e4b79bfbf764dafdfd2579175a6ea9771`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **jawills--sf-deploy/v0.5** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Multiple `run:` steps in action.yml directly interpolate `inputs.*` expressions inside shell commands instead of first assigning them to environment variables. This allows an attacker-controlled input value to break out of the intended command context and execute arbitrary shell code.

- Step "Login to Environment" (line 38): `${{ inputs.SFDX_AUTH_URL }}` is interpolated directly into the shell command.
- Step "Generate package.xml" (line 43): `${{ inputs.SOURCE_DIRECTORY }}` is interpolated directly into the shell command.
- Step "Deploy to Environment" (lines 49, 50, 52): `${{ inputs.WAIT }}`, `${{ inputs.TEST_LEVEL }}`, and `${{ inputs.DRY_RUN }}` are all interpolated directly into the shell command.

Fix: assign each input to an `env:` variable on the step and reference it as `$ENV_VAR` in the `run:` block.

Locations:

- `action.yml:38`
- `action.yml:43`
- `action.yml:49`
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

Fixed all 6 script injection findings in action.yml by moving ${{ inputs.* }} expressions out of run: shell blocks and into env: maps on each affected step:

1. 'Login to Environment' step: moved ${{ inputs.SFDX_AUTH_URL }} to env: SFDX_AUTH_URL, updated run: to use $SFDX_AUTH_URL.
2. 'Generate package.xml' step: moved ${{ inputs.SOURCE_DIRECTORY }} to env: SOURCE_DIRECTORY, updated run: to use "$SOURCE_DIRECTORY" (also added quotes for proper word splitting protection).
3. 'Deploy to Environment' step: moved ${{ inputs.WAIT }}, ${{ inputs.TEST_LEVEL }}, and ${{ inputs.DRY_RUN }} to env: WAIT, TEST_LEVEL, DRY_RUN respectively, updated run: to reference $WAIT, $TEST_LEVEL, and $DRY_RUN as plain environment variables.

