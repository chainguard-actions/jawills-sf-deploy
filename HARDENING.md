# Hardening Report: jawills--sf-deploy/v1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `ff50f15e4b79bfbf764dafdfd2579175a6ea9771`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **jawills--sf-deploy/v1.0** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Multiple `run:` steps in action.yml directly interpolate `inputs.*` expressions into shell commands without first assigning them to environment variables. An attacker who controls these input values can inject arbitrary shell commands.

- Line 43 (Login to Environment): `${{ inputs.SFDX_AUTH_URL }}` interpolated directly into `sf org login` command
- Line 47 (Generate package.xml): `${{ inputs.SOURCE_DIRECTORY }}` interpolated directly into `sf project generate manifest`
- Line 53 (Deploy to Environment): `${{ inputs.WAIT }}` interpolated directly into shell array
- Line 54 (Deploy to Environment): `${{ inputs.TEST_LEVEL }}` interpolated directly into shell array
- Line 57 (Deploy to Environment): `${{ inputs.DRY_RUN }}` interpolated directly into shell conditional

Fix: assign each input to an env var (e.g. `env: SFDX_AUTH_URL: ${{ inputs.SFDX_AUTH_URL }}`) and reference `$SFDX_AUTH_URL` in the run block instead.

Locations:

- `action.yml:43`
- `action.yml:47`
- `action.yml:53`
- `action.yml:54`
- `action.yml:57`

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

Fixed all 6 script injection findings in action.yml by moving all ${{ inputs.* }} expressions out of run: blocks and into env: blocks for each affected step:
1. 'Login to Environment': Added env block with SFDX_AUTH_URL=${{ inputs.SFDX_AUTH_URL }}, replaced inline expression with $SFDX_AUTH_URL
2. 'Generate package.xml': Added env block with SOURCE_DIRECTORY=${{ inputs.SOURCE_DIRECTORY }}, replaced inline expression with "$SOURCE_DIRECTORY"
3. 'Deploy to Environment': Added env block with WAIT=${{ inputs.WAIT }}, TEST_LEVEL=${{ inputs.TEST_LEVEL }}, DRY_RUN=${{ inputs.DRY_RUN }}, replaced all inline expressions with their corresponding environment variable references

