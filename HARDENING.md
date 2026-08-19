<!-- markdownlint-disable -->

# Hardening Report: jawills--sf-deploy/v0.5

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **jawills--sf-deploy/v0.5** was hardened automatically. 8 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a): The 'Login to Environment' step directly interpolates `${{ inputs.SFDX_AUTH_URL }}` inside a `run:` shell command. This allows an attacker-controlled value to be injected into the shell before quoting or escaping occurs. The offending line is: `sf org login sfdx-url --set-default --sfdx-url-file <(echo "${{ inputs.SFDX_AUTH_URL }}")`. Fix: move the value into an `env:` variable and reference it as a quoted shell variable, e.g. `env: SFDX_AUTH_URL: ${{ inputs.SFDX_AUTH_URL }}` and then `<(echo "$SFDX_AUTH_URL")`.

Locations:

- `action.yml:39`

### script-injection (severity: high)

Rule (a) and (b): The 'Generate package.xml' step directly interpolates `${{ inputs.SOURCE_DIRECTORY }}` inside a `run:` shell command AND the value is unquoted, allowing shell metacharacter injection. The offending line is: `sf project generate manifest --source-dir ${{ inputs.SOURCE_DIRECTORY }} --output-dir manifest`. Fix: move the value into an `env:` variable and reference it as a double-quoted shell variable, e.g. `env: SOURCE_DIRECTORY: ${{ inputs.SOURCE_DIRECTORY }}` and then `--source-dir "$SOURCE_DIRECTORY"`.

Locations:

- `action.yml:43`

### script-injection (severity: high)

Rule (a): The 'Deploy to Environment' step directly interpolates three `inputs.*` expressions inside a `run:` shell command: `${{ inputs.WAIT }}` (line 48), `${{ inputs.TEST_LEVEL }}` (line 49), and `${{ inputs.DRY_RUN }}` (line 51). All three are substituted into the shell script before the shell parses it, enabling command injection. Fix: move each value into an `env:` block and reference them as double-quoted shell variables (e.g. `"$WAIT"`, `"$TEST_LEVEL"`, `"$DRY_RUN"`).

Locations:

- `action.yml:48`
- `action.yml:49`
- `action.yml:51`

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

Fixed all 8 script injection findings in hardened/action/action.yml across 3 steps:
1. 'Login to Environment': Added `env: SFDX_AUTH_URL: ${{ inputs.SFDX_AUTH_URL }}` and changed the run command to use `$SFDX_AUTH_URL` instead of the inline expression.
2. 'Generate package.xml': Added `env: SOURCE_DIRECTORY: ${{ inputs.SOURCE_DIRECTORY }}` and changed the run command to use `"$SOURCE_DIRECTORY"` (double-quoted) instead of the inline expression.
3. 'Deploy to Environment': Added `env:` block with WAIT, TEST_LEVEL, and DRY_RUN mapped from their respective inputs, and updated the run script to reference `"$WAIT"`, `"$TEST_LEVEL"`, and `"$DRY_RUN"` as quoted shell variables instead of inline expressions.

