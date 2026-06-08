# Hardening Report: jawills--sf-deploy/v2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `ff50f15e4b79bfbf764dafdfd2579175a6ea9771`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **jawills--sf-deploy/v2.0** was hardened automatically. 12 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Multiple `run:` steps in action.yml directly interpolate `inputs.*` expressions into shell commands without first assigning them to environment variables. This allows an attacker who controls the input values to inject arbitrary shell commands.

- **Login to Environment** (line 63): `${{ inputs.SFDX_AUTH_URL }}` is interpolated directly into the shell command: `sf org login sfdx-url --set-default --sfdx-url-file <(echo "${{ inputs.SFDX_AUTH_URL }}")`
- **Generate package.xml** (line 68): `${{ inputs.SOURCE_DIRECTORY }}` is interpolated directly: `sf project generate manifest --source-dir ${{ inputs.SOURCE_DIRECTORY }} --output-dir manifest`
- **Start deployment** (lines 71–89): `${{ inputs.MANIFEST_PATH }}`, `${{ inputs.TEST_LEVEL }}`, `${{ inputs.DRY_RUN }}`, `${{ inputs.DEPLOYMENT_ID }}`, and `${{ inputs.POST_DESTRUCTIVE_CHANGES }}` are all interpolated directly into the shell script.
- **Resume Deployment** (line 96): `${{ inputs.WAIT }}` is interpolated directly: `sf project deploy resume -i "$(cat deployment-id.txt)" --wait ${{ inputs.WAIT }} --json`

Fix: assign each input to an `env:` variable on the step and reference `$ENV_VAR` in the shell script instead.

Locations:

- `action.yml:63`
- `action.yml:68`
- `action.yml:71`
- `action.yml:78`
- `action.yml:82`
- `action.yml:85`
- `action.yml:88`
- `action.yml:96`

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

Fixed all script injection vulnerabilities in action.yml by moving ${{ inputs.* }} expressions from run: shell blocks into env: blocks on each step:

1. **Login to Environment**: Moved `${{ inputs.SFDX_AUTH_URL }}` to `env: SFDX_AUTH_URL:` and replaced inline expression with `$SFDX_AUTH_URL`.

2. **Generate package.xml**: Moved `${{ inputs.SOURCE_DIRECTORY }}` to `env: SOURCE_DIRECTORY:` and replaced inline expression with `"$SOURCE_DIRECTORY"` (also added quotes for safety).

3. **Start deployment**: Moved all five inputs (`MANIFEST_PATH`, `TEST_LEVEL`, `DRY_RUN`, `DEPLOYMENT_ID`, `POST_DESTRUCTIVE_CHANGES`) to `env:` block and replaced all inline expressions with their corresponding environment variable references. Also added proper quoting around variable references.

4. **Resume Deployment**: Moved `${{ inputs.WAIT }}` to `env: WAIT:` and replaced inline expression with `"$WAIT"`.

No unpinned actions or missing permissions findings were present in this action (it uses `runs: using: composite` with no `uses:` references in steps).

