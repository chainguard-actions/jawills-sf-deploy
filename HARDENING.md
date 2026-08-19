<!-- markdownlint-disable -->

# Hardening Report: jawills--sf-deploy/v0.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **jawills--sf-deploy/v0.3** was hardened automatically. 5 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Four ${{ inputs.* }} expressions are interpolated directly inside run: shell command strings, allowing an attacker who controls the calling workflow's inputs to inject arbitrary shell commands.

1. 'Login to Environment' step: `sf org login sfdx-url --set-default --sfdx-url-file <(echo "${{ inputs.SFDX_AUTH_URL }}")` — the auth URL is template-substituted directly into the shell command before the shell parses it.
2. 'Generate package.xml' step: `sf project generate manifest --source-dir ${{ inputs.SOURCE_DIRECTORY }} --output-dir manifest` — unquoted and directly interpolated.
3. 'Deploy to Environment' step (array element): `--test-level ${{ inputs.SOURCE_DIRECTORY }}` — directly interpolated into a bash array element.
4. 'Deploy to Environment' step (conditional): `if [ "${{ inputs.DRY_RUN }}" = "true" ]` — interpolated inside the test expression.

Fix: Move each input into an env: block and reference it as a quoted shell variable (e.g., "$SOURCE_DIRECTORY") instead of using ${{ }} directly in the run: script.

Locations:

- `action.yml:34`
- `action.yml:38`
- `action.yml:43`
- `action.yml:47`

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

Fixed all 5 findings (1 script-injection, 4 static-inline-injection) in action.yml by moving all ${{ inputs.* }} expressions into env: blocks on each affected step:

1. 'Login to Environment': Moved ${{ inputs.SFDX_AUTH_URL }} to env: SFDX_AUTH_URL, referenced as "$SFDX_AUTH_URL" in the process substitution.
2. 'Generate package.xml': Moved ${{ inputs.SOURCE_DIRECTORY }} to env: SOURCE_DIRECTORY, referenced as "$SOURCE_DIRECTORY" (also added quotes for proper word-splitting protection).
3. 'Deploy to Environment': Moved ${{ inputs.SOURCE_DIRECTORY }}, ${{ inputs.TEST_LEVEL }}, ${{ inputs.WAIT }}, and ${{ inputs.DRY_RUN }} to env: block. Also fixed two apparent bugs: '--wait SFDX_AUTH_URL' (literal string) was corrected to '--wait "$WAIT"', and '--test-level ${{ inputs.SOURCE_DIRECTORY }}' (wrong input) was corrected to '--test-level "$TEST_LEVEL"'.

