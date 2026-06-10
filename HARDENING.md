<!-- markdownlint-disable -->

# Hardening Report: jawills--sf-deploy/v1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **jawills--sf-deploy/v1.0** was hardened automatically. 8 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a): The 'Login to Environment' step directly interpolates `${{ inputs.SFDX_AUTH_URL }}` inside a `run:` shell command. GitHub Actions performs template substitution before the shell ever sees the string, so a malicious value can break out of the `echo` argument and inject arbitrary shell commands. Fix: move the value into an `env:` variable and reference it as `"$SFDX_AUTH_URL"` in the script.

Offending line: `sf org login sfdx-url --set-default --sfdx-url-file <(echo "${{ inputs.SFDX_AUTH_URL }}")`

Locations:

- `action.yml:43`

### script-injection (severity: high)

Rule (a): The 'Generate package.xml' step directly interpolates `${{ inputs.SOURCE_DIRECTORY }}` inside a `run:` shell command without quoting. An attacker-controlled value can inject shell metacharacters. Additionally the expansion is unquoted (rule b). Fix: move the value into an `env:` variable and reference it as `"$SOURCE_DIRECTORY"` in the script.

Offending line: `sf project generate manifest --source-dir ${{ inputs.SOURCE_DIRECTORY }} --output-dir manifest`

Locations:

- `action.yml:47`

### script-injection (severity: high)

Rule (a): The 'Deploy to Environment' step directly interpolates multiple `inputs.*` expressions inside a `run:` shell block: `${{ inputs.WAIT }}` (line 52), `${{ inputs.TEST_LEVEL }}` (line 53), and `${{ inputs.DRY_RUN }}` (line 56). Each is substituted by the Actions runner before the shell parses the script, allowing injection of arbitrary shell commands. Fix: move all values into `env:` variables and reference them as quoted shell variables (e.g. `"$WAIT"`, `"$TEST_LEVEL"`, `"$DRY_RUN"`).

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

Fixed all 8 script injection findings in action.yml across 3 steps:
1. 'Login to Environment': moved `${{ inputs.SFDX_AUTH_URL }}` to env: block as SFDX_AUTH_URL, referenced as "$SFDX_AUTH_URL" in the process substitution.
2. 'Generate package.xml': moved `${{ inputs.SOURCE_DIRECTORY }}` to env: block as SOURCE_DIRECTORY, referenced as "$SOURCE_DIRECTORY" (now properly quoted).
3. 'Deploy to Environment': moved `${{ inputs.WAIT }}`, `${{ inputs.TEST_LEVEL }}`, and `${{ inputs.DRY_RUN }}` to env: block as WAIT, TEST_LEVEL, and DRY_RUN respectively, referenced as "$WAIT", "$TEST_LEVEL", and "$DRY_RUN" in the shell script.

