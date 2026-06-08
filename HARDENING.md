# Hardening Report: jawills--sf-deploy/v0.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `ff50f15e4b79bfbf764dafdfd2579175a6ea9771`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **jawills--sf-deploy/v0.3** was hardened automatically. 7 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

The 'Login to Environment' step directly interpolates `${{ inputs.SFDX_AUTH_URL }}` inside a `run:` shell command. Attacker-controlled input values are embedded directly into the shell string rather than being passed via an `env:` variable, enabling shell injection attacks.

Locations:

- `action.yml:38`

### script-injection (severity: high)

The 'Generate package.xml' step directly interpolates `${{ inputs.SOURCE_DIRECTORY }}` inside a `run:` shell command. Attacker-controlled input values are embedded directly into the shell string rather than being passed via an `env:` variable, enabling shell injection attacks.

Locations:

- `action.yml:42`

### script-injection (severity: high)

The 'Deploy to Environment' step directly interpolates `${{ inputs.SOURCE_DIRECTORY }}` (line 51) and `${{ inputs.DRY_RUN }}` (line 53) inside a `run:` shell command. Attacker-controlled input values are embedded directly into the shell string rather than being passed via an `env:` variable, enabling shell injection attacks.

Locations:

- `action.yml:51`
- `action.yml:53`

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

Fixed all 7 script injection findings in action.yml by moving ${{ }} expressions out of run: shell blocks and into env: maps for each affected step:
1. 'Login to Environment': moved `${{ inputs.SFDX_AUTH_URL }}` to env block as SFDX_AUTH_URL, referenced as $SFDX_AUTH_URL in the shell command.
2. 'Generate package.xml': moved `${{ inputs.SOURCE_DIRECTORY }}` to env block as SOURCE_DIRECTORY, referenced as "$SOURCE_DIRECTORY" in the shell command (added quotes for word-splitting safety).
3. 'Deploy to Environment': moved both `${{ inputs.SOURCE_DIRECTORY }}` and `${{ inputs.DRY_RUN }}` to env block as SOURCE_DIRECTORY and DRY_RUN, referenced as "$SOURCE_DIRECTORY" and "$DRY_RUN" in the shell script.

