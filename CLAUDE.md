# ZLAR-LT — Project Guide

## What this is

ZLAR-LT is a zero-config governance layer for AI coding agents. One command installs it, auto-detects your frameworks (Claude Code, Cursor, Windsurf), generates Ed25519 signing keys, signs a deny-heavy policy, and configures hooks. Governance running in under 60 seconds.

## Architecture

ZLAR-LT is not a new gate engine. It packages the same `bin/zlar-gate` (857 lines) and `bin/zlar-policy` (726 lines) from ZLAR Gate with a zero-config installer and a deny-heavy default policy.

```
curl -fsSL https://zlar.ai/install.sh | bash
    ↓
install.sh
    ↓ preflight (OS, bash 4+, jq, openssl, curl)
    ↓ detect frameworks
    ↓ install to ~/.zlar-lt/
    ↓ generate Ed25519 keys
    ↓ sign default policy
    ↓ configure framework hooks
    ↓ self-test
    ↓
Governance running.
```

### Key difference from ZLAR Gate

- **No Telegram required.** "Ask" actions become "deny" (instant, not 5-minute hang).
- **No interactive setup.** Everything auto-detected and auto-configured.
- **Upgrade path built in.** `zlar-lt telegram` converts deny → ask for reviewable actions.

## Core principles

1. **Fail closed.** Unknown tools denied. Unmatched rules denied. Gate down = all denied.
2. **No intelligence in the gate.** Classify, halt, ask. No LLM, no ML, no heuristics.
3. **The policy is a human artifact.** Ed25519 signed. AI cannot modify the rules that govern it.
4. **Deterministic.** Same input, same output, always.
5. **Zero config.** Works out of the box. No decisions required to get governance running.

## Key files

- `install.sh` — the `curl | bash` entry point. Bash-3 compatible (macOS default).
- `uninstall.sh` — clean removal. Removes hooks, deletes `~/.zlar-lt/`, preserves signing key.
- `bin/zlar-lt` — convenience CLI (status, audit, policy, telegram, uninstall, version)
- `bin/zlar-gate` — core gate engine (identical to ZLAR Gate, DO NOT MODIFY)
- `bin/zlar-policy` — policy CLI (identical to ZLAR Gate, DO NOT MODIFY)
- `adapters/*/hook.sh` — framework adapters (identical to ZLAR Gate, DO NOT MODIFY)
- `etc/gate.lt.json` — LT config template (Telegram disabled)
- `etc/policies/lt-default.policy.json` — default policy (all "ask" → "deny")

## Default policy

The LT policy allows reads, writes, edits, and searches. Everything else is denied:

| Allowed | Denied |
|---------|--------|
| File reads/writes/edits | rm, rm -rf |
| Glob, grep searches | sudo, privilege escalation |
| Safe shell (ls, cat, pwd, git status) | curl, wget, ssh, network send |
| Web search | git push |
| | crontab, launchctl, persistence |
| | .ssh writes, .env writes |
| | MCP tools, unknown commands |

## Policy changes

```bash
# Edit the policy:
vi ~/.zlar-lt/etc/policies/active.policy.json

# Re-sign after changes:
~/.zlar-lt/bin/zlar-policy sign \
  --input ~/.zlar-lt/etc/policies/active.policy.json \
  --key ~/.zlar-signing.key
```

## Install location

Everything lives at `~/.zlar-lt/`. The gate engine resolves `PROJECT_DIR` from its own location (`dirname` of `bin/`), so all paths are relative and correct.
