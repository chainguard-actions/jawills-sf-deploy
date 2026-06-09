# Hardening Report: jawills--sf-deploy/v0.5

> This file was generated automatically by the hardening agent.

**Policy SHA:** `ff50f15e4b79bfbf764dafdfd2579175a6ea9771`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **jawills--sf-deploy/v0.5** was hardened automatically. 8 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

The 'Login to Environment' step directly interpolates `${{ inputs.SFDX_AUTH_URL }}` into the shell command string. An attacker-controlled input value is embedded verbatim into the run: block without first being assigned to an environment variable, enabling script injection. The value should be passed via `env:` and referenced as `$SFDX_AUTH_URL`.

Locations:

- `action.yml:39`

### script-injection (severity: high)

The 'Generate package.xml' step directly interpolates `${{ inputs.SOURCE_DIRECTORY }}` into the shell command string. An attacker-controlled input value is embedded verbatim into the run: block without first being assigned to an environment variable, enabling script injection. The value should be passed via `env:` and referenced as `$SOURCE_DIRECTORY`.

Locations:

- `action.yml:43`

### script-injection (severity: high)

The 'Deploy to Environment' step directly interpolates `${{ inputs.WAIT }}`, `${{ inputs.TEST_LEVEL }}`, and `${{ inputs.DRY_RUN }}` into the shell command string. All three attacker-controlled input values are embedded verbatim into the run: block without first being assigned to environment variables, enabling script injection. Each value should be passed via `env:` and referenced as shell variables.

Locations:

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

Fixed all 8 script injection findings in action.yml across 3 steps:
1. 'Login to Environment': Moved `${{ inputs.SFDX_AUTH_URL }}` to `env: SFDX_AUTH_URL:` and updated the run block to use `$SFDX_AUTH_URL`.
2. 'Generate package.xml': Moved `${{ inputs.SOURCE_DIRECTORY }}` to `env: SOURCE_DIRECTORY:` and updated the run block to use `"$SOURCE_DIRECTORY"` (also added quotes for safety).
3. 'Deploy to Environment': Moved `${{ inputs.WAIT }}`, `${{ inputs.TEST_LEVEL }}`, and `${{ inputs.DRY_RUN }}` to `env:` block and updated the run block to reference `$WAIT`, `$TEST_LEVEL`, and `$DRY_RUN` as plain shell variables.

