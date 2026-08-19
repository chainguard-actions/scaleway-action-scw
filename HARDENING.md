<!-- markdownlint-disable -->

# Hardening Report: scaleway--action-scw/v0.0.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **scaleway--action-scw/v0.0.3** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Multiple `run:` blocks in `.github/workflows/test.yml` directly interpolate `${{ steps.cli.outputs.json }}` and `${{ steps.cli_manual.outputs.json }}` inside shell command strings. The `steps.*.outputs.*` context flows through YAML template substitution before the shell ever sees it, allowing a malicious step output to inject arbitrary shell commands. Offending lines:
- Line 59: `[ "$( echo '${{ steps.cli.outputs.json }}' | jq -r 'type')" = "array" ]`
- Line 70: `[ "$( echo '${{ steps.cli_manual.outputs.json }}' | jq -r 'type')" = "array" ]`
- Line 93: `[ "$( echo '${{ steps.cli.outputs.json }}' | jq -r 'type')" = "array" ]`
- Line 99: `[ "$( echo '${{ steps.cli_manual.outputs.json }}' | jq -r 'type')" = "array" ]`
- Line 129: `[ "$( echo '${{ steps.cli_manual.outputs.json }}' | jq -r 'type')" = "array" ]`
- Line 145: `[ "$( echo '${{ steps.cli.outputs.json }}' | jq -r 'type')" = "array" ]`
Fix: assign the output to an env var and reference it as a quoted shell variable, e.g. `env: CLI_JSON: ${{ steps.cli.outputs.json }}` then `echo "$CLI_JSON"` in the run block.

Locations:

- `.github/workflows/test.yml:59`
- `.github/workflows/test.yml:70`
- `.github/workflows/test.yml:93`
- `.github/workflows/test.yml:99`
- `.github/workflows/test.yml:129`
- `.github/workflows/test.yml:145`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed all 6 script injection occurrences in .github/workflows/test.yml. Each `${{ steps.cli.outputs.json }}` and `${{ steps.cli_manual.outputs.json }}` expression that was directly interpolated inside `run:` shell strings has been moved to the step's `env:` block (as `CLI_JSON` and `CLI_MANUAL_JSON` respectively), and the shell scripts now reference them as plain quoted environment variables (`"$CLI_JSON"` and `"$CLI_MANUAL_JSON"`). This prevents attacker-controlled step output values from being interpreted as shell commands via YAML template substitution.

