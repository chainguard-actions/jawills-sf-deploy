<!-- markdownlint-disable -->

# Hardening Report: jawills--sf-deploy/v2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **jawills--sf-deploy/v2.0** was hardened automatically. 15 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The 'Login to Environment' step directly interpolates `${{ inputs.SFDX_AUTH_URL }}` inside a `run:` shell command. An attacker-controlled input value is substituted into the shell script before execution, enabling arbitrary command injection. Offending line: `sf org login sfdx-url --set-default --sfdx-url-file <(echo "${{ inputs.SFDX_AUTH_URL }}")`

Locations:

- `action.yml:71`

### script-injection (severity: high)

Sub-rule (a): The 'Generate package.xml' step directly interpolates `${{ inputs.SOURCE_DIRECTORY }}` inside a `run:` shell command without quoting. An attacker-controlled input is substituted into the shell script before execution, enabling arbitrary command injection. Offending line: `sf project generate manifest --source-dir ${{ inputs.SOURCE_DIRECTORY }} --output-dir manifest`

Locations:

- `action.yml:76`

### script-injection (severity: high)

Sub-rule (a): The 'Start deployment' step directly interpolates multiple `inputs.*` expressions inside a `run:` shell block: `${{ inputs.MANIFEST_PATH }}` (twice), `${{ inputs.TEST_LEVEL }}` (unquoted), `${{ inputs.DRY_RUN }}`, `${{ inputs.DEPLOYMENT_ID }}` (twice, one unquoted), and `${{ inputs.POST_DESTRUCTIVE_CHANGES }}`. Each of these is substituted into the shell script before execution, enabling arbitrary command injection via any of these inputs.

Locations:

- `action.yml:79`

### script-injection (severity: high)

Sub-rule (a): The 'Resume Deployment' step directly interpolates `${{ inputs.WAIT }}` inside a `run:` shell command. An attacker-controlled input value is substituted into the shell script before execution, enabling arbitrary command injection. Offending line: `sf project deploy resume -i "$(cat deployment-id.txt)" --wait ${{ inputs.WAIT }} --json > resume-result.json`

Locations:

- `action.yml:94`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.SFDX_AUTH_URL }}" appears directly in run: block of step "Login to Environment"; move to env: map

Locations:

- `action.yml:71`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.SOURCE_DIRECTORY }}" appears directly in run: block of step "Generate package.xml"; move to env: map

Locations:

- `action.yml:76`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.MANIFEST_PATH }}" appears directly in run: block of step "Start deployment"; move to env: map

Locations:

- `action.yml:80`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.MANIFEST_PATH }}" appears directly in run: block of step "Start deployment"; move to env: map

Locations:

- `action.yml:81`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.TEST_LEVEL }}" appears directly in run: block of step "Start deployment"; move to env: map

Locations:

- `action.yml:87`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.DRY_RUN }}" appears directly in run: block of step "Start deployment"; move to env: map

Locations:

- `action.yml:92`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.DEPLOYMENT_ID }}" appears directly in run: block of step "Start deployment"; move to env: map

Locations:

- `action.yml:95`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.DEPLOYMENT_ID }}" appears directly in run: block of step "Start deployment"; move to env: map

Locations:

- `action.yml:96`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.POST_DESTRUCTIVE_CHANGES }}" appears directly in run: block of step "Start deployment"; move to env: map

Locations:

- `action.yml:98`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.POST_DESTRUCTIVE_CHANGES }}" appears directly in run: block of step "Start deployment"; move to env: map

Locations:

- `action.yml:99`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.WAIT }}" appears directly in run: block of step "Resume Deployment"; move to env: map

Locations:

- `action.yml:108`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection

**Notes:**

Fixed all 15 script injection findings in action.yml by moving ${{ inputs.* }} expressions from run: shell blocks into env: blocks for each affected step: (1) 'Login to Environment': SFDX_AUTH_URL → env block, shell uses $SFDX_AUTH_URL; (2) 'Generate package.xml': SOURCE_DIRECTORY → env block, shell uses "$SOURCE_DIRECTORY" with proper quoting; (3) 'Start deployment': MANIFEST_PATH, TEST_LEVEL, DRY_RUN, DEPLOYMENT_ID, POST_DESTRUCTIVE_CHANGES → env block, all referenced as properly double-quoted env vars in the shell script; (4) 'Resume Deployment': WAIT → env block, shell uses "$WAIT". All remaining ${{ inputs.* }} references in the file are in env: block assignments and if: conditions, which are safe.

