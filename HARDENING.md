<!-- markdownlint-disable -->

# Hardening Report: jawills--sf-deploy/v2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **jawills--sf-deploy/v2.0** was hardened automatically. 15 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The 'Login to Environment' step interpolates `${{ inputs.SFDX_AUTH_URL }}` directly inside a `run:` shell command string (`sf org login sfdx-url --set-default --sfdx-url-file <(echo "${{ inputs.SFDX_AUTH_URL }}")`). Any value supplied by the calling workflow is expanded by the YAML template engine before the shell sees it, enabling command injection.

Locations:

- `action.yml:62`

### script-injection (severity: high)

Sub-rule (a): The 'Generate package.xml' step interpolates `${{ inputs.SOURCE_DIRECTORY }}` directly and unquoted inside a `run:` shell command string (`sf project generate manifest --source-dir ${{ inputs.SOURCE_DIRECTORY }} --output-dir manifest`). An attacker-controlled value can inject shell metacharacters.

Locations:

- `action.yml:67`

### script-injection (severity: high)

Sub-rule (a): The 'Start deployment' step interpolates multiple `inputs.*` expressions directly inside a `run:` shell command string without routing through env vars: `${{ inputs.MANIFEST_PATH }}` (twice), `${{ inputs.TEST_LEVEL }}` (unquoted), `${{ inputs.DRY_RUN }}`, `${{ inputs.DEPLOYMENT_ID }}` (unquoted), and `${{ inputs.POST_DESTRUCTIVE_CHANGES }}`. Each of these allows an attacker-controlled value to be expanded by the YAML template engine before the shell parses it, enabling command injection.

Locations:

- `action.yml:71`

### script-injection (severity: high)

Sub-rule (a): The 'Resume Deployment' step interpolates `${{ inputs.WAIT }}` directly inside a `run:` shell command string (`sf project deploy resume -i "$(cat deployment-id.txt)" --wait ${{ inputs.WAIT }} --json > resume-result.json`). The value is unquoted and expanded before the shell parses the command.

Locations:

- `action.yml:92`

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

Fixed all script injection vulnerabilities in action.yml by moving ${{ inputs.* }} expressions from run: shell command strings into env: blocks for each affected step:

1. 'Login to Environment' step: Added env block with SFDX_AUTH_URL=${{ inputs.SFDX_AUTH_URL }}, replaced inline expression with $SFDX_AUTH_URL in the run: block.

2. 'Generate package.xml' step: Added env block with SOURCE_DIRECTORY=${{ inputs.SOURCE_DIRECTORY }}, replaced unquoted inline expression with quoted "$SOURCE_DIRECTORY" in the run: block.

3. 'Start deployment' step: Added env block with MANIFEST_PATH, TEST_LEVEL, DRY_RUN, DEPLOYMENT_ID, and POST_DESTRUCTIVE_CHANGES all mapped from their respective inputs. Replaced all inline expressions with properly quoted environment variable references in the run: block.

4. 'Resume Deployment' step: Added env block with WAIT=${{ inputs.WAIT }}, replaced unquoted inline expression with quoted "$WAIT" in the run: block.

All remaining ${{ inputs.* }} expressions in the file are in env: map values or if: conditions, which are safe.

