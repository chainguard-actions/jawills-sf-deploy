# Hardening Report: jawills--sf-deploy/v0.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `ff50f15e4b79bfbf764dafdfd2579175a6ea9771`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **jawills--sf-deploy/v0.4** was hardened automatically. 7 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

The 'Login to Environment' step directly interpolates `${{ inputs.SFDX_AUTH_URL }}` inside a `run:` shell command. An attacker-controlled input value is embedded directly into the shell command string without being assigned to an environment variable first, enabling script injection (e.g. command substitution or shell metacharacter injection).

Locations:

- `action.yml:33`

### script-injection (severity: high)

The 'Generate package.xml' step directly interpolates `${{ inputs.SOURCE_DIRECTORY }}` inside a `run:` shell command. An attacker-controlled input value is embedded directly into the shell command string without being assigned to an environment variable first, enabling script injection via shell metacharacters in the path value.

Locations:

- `action.yml:37`

### script-injection (severity: high)

The 'Deploy to Environment' step directly interpolates `${{ inputs.TEST_LEVEL }}` and `${{ inputs.DRY_RUN }}` inside a `run:` shell command. Both attacker-controlled input values are embedded directly into the shell command string without being assigned to environment variables first, enabling script injection.

Locations:

- `action.yml:43`
- `action.yml:46`

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

Fixed all 7 script injection findings in action.yml by moving ${{ inputs.SFDX_AUTH_URL }}, ${{ inputs.SOURCE_DIRECTORY }}, ${{ inputs.TEST_LEVEL }}, and ${{ inputs.DRY_RUN }} out of run: shell strings and into env: blocks on their respective steps. The shell scripts now reference these values as plain environment variables ($SFDX_AUTH_URL, $SOURCE_DIRECTORY, $TEST_LEVEL, $DRY_RUN), preventing shell metacharacter injection. Also added double-quotes around variable references in the shell scripts for additional safety.

