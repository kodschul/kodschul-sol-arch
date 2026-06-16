---
description: "Generate a commented PowerShell PAC CLI deployment script for Power Platform solutions. Covers auth, export from source environment, import as managed to target environment, with error handling."
agent: agent
argument-hint: "Describe the deployment scenario: which environments, solution name, any special steps"
---
Generate a PowerShell PAC CLI deployment script for the following scenario:

${input}

## Requirements for the script

1. **Variables block at the top** — all environment URLs, solution name, publisher prefix as `$` variables
2. **Auth setup** — `pac auth create` for each environment, `pac auth select` before each operation
3. **Export** — export the solution as **Unmanaged** from source (DEV) environment
4. **Import** — import as **Managed** into target (TEST or PROD) environment with `--publish-changes`
5. **Error handling** — `$ErrorActionPreference = "Stop"` at the top; `try/catch` around critical steps
6. **Logging** — `Write-Host` with timestamps for each step
7. **Safety comment** at the end:
   ```
   # ⚠ PRODUCTION REVIEW REQUIRED — do not run against PROD without SA sign-off
   ```

## VisitTrack defaults (use unless overridden in scenario)
- Solution name: `VisitTrack`
- Publisher prefix: `medpharma`
- Source env variable: `$DEV_ENV`
- Target env variable: `$TEST_ENV`

## Output format
One complete, runnable PowerShell script with inline comments explaining each section.
