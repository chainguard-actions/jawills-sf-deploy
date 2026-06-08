# Hardening Report: jawills--sf-deploy/v1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `ff50f15e4b79bfbf764dafdfd2579175a6ea9771`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **jawills--sf-deploy/v1.0** was hardened automatically. 8 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

The 'Login to Environment' step directly interpolates `${{ inputs.SFDX_AUTH_URL }}` inside a `run:` shell command. An attacker-controlled input value is embedded directly into the shell string, enabling arbitrary command injection. The input should be assigned to an environment variable via `env:` and referenced as `$SFDX_AUTH_URL` instead.

Locations:

- `action.yml:43`

### script-injection (severity: high)

The 'Generate package.xml' step directly interpolates `${{ inputs.SOURCE_DIRECTORY }}` inside a `run:` shell command. An attacker-controlled input value is embedded directly into the shell string, enabling arbitrary command injection (e.g. path traversal or shell metacharacters). The input should be assigned to an environment variable via `env:` and referenced as `$SOURCE_DIRECTORY` instead.

Locations:

- `action.yml:47`

### script-injection (severity: high)

The 'Deploy to Environment' step directly interpolates `${{ inputs.WAIT }}`, `${{ inputs.TEST_LEVEL }}`, and `${{ inputs.DRY_RUN }}` inside a `run:` shell command. Attacker-controlled input values are embedded directly into the shell string, enabling arbitrary command injection via shell metacharacters. All three inputs should be assigned to environment variables via `env:` and referenced as shell variables instead.

Locations:

- `action.yml:52`
- `action.yml:53`
- `action.yml:56`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.SFDX_AUTH_URL }}" appears directly in run: block of step "Login to Environment"; move to env: map

Locations:

- `action.yml:44`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.SOURCE_DIRECTORY }}" appears directly in run: block of step "Generate package.xml"; move to env: map

Locations:

- `action.yml:48`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.WAIT }}" appears directly in run: block of step "Deploy to Environment"; move to env: map

Locations:

- `action.yml:54`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.TEST_LEVEL }}" appears directly in run: block of step "Deploy to Environment"; move to env: map

Locations:

- `action.yml:55`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.DRY_RUN }}" appears directly in run: block of step "Deploy to Environment"; move to env: map

Locations:

- `action.yml:58`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection

**Notes:**

Fixed all 8 findings (3 script-injection + 5 static-inline-injection) across 3 steps in action.yml:
1. 'Login to Environment': moved `${{ inputs.SFDX_AUTH_URL }}` to `env: SFDX_AUTH_URL:` and referenced as `$SFDX_AUTH_URL` in the run block.
2. 'Generate package.xml': moved `${{ inputs.SOURCE_DIRECTORY }}` to `env: SOURCE_DIRECTORY:` and referenced as `"$SOURCE_DIRECTORY"` in the run block.
3. 'Deploy to Environment': moved `${{ inputs.WAIT }}`, `${{ inputs.TEST_LEVEL }}`, and `${{ inputs.DRY_RUN }}` to `env:` block and referenced as `"$WAIT"`, `"$TEST_LEVEL"`, and `"$DRY_RUN"` respectively in the run block.

