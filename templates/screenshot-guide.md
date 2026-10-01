# 📸 Screenshot Standard

## When to Capture (4 Moments Per Alert)
1. **Alert Open** — dashboard with ID, severity, timestamp
2. **Evidence** — the log line / lookup / artifact that decided it
3. **Verdict** — final classification screen
4. **ATT&CK** — technique mapping view

## Naming Convention
`XX-alertID-stage.png`

Examples:
- `01-alert-1042-dashboard.png`
- `01-alert-1042-rawlogs.png`
- `01-alert-1042-virustotal.png`
- `01-alert-1042-verdict.png`

## Redaction Rules (NON-NEGOTIABLE)
- ❌ Never capture API keys, tokens, credentials
- ❌ Never capture real client/user data (platform ToS)
- ⚠️ Blur internal hostnames if sensitive
- ⚠️ Truncate user emails to `abc***@domain.com`
- ⚠️ Crop out browser tabs / personal info
- ✅ Crop tight — no desktop wallpaper, no other windows

## Storage
`projects/XX-project-name/screenshots/`

## Commit Message for Screenshots
`Add triage evidence for alert XXX`
