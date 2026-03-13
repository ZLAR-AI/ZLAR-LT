# ZLAR-LT — Zero-Config Governance for AI Coding Agents

**One command. Your rules. Under 60 seconds.**

AI coding agents can run shell commands, write files, make network requests, and push code — all without asking. ZLAR-LT adds a policy layer that lets safe actions through instantly and blocks dangerous ones. No configuration needed.

```bash
curl -fsSL https://zlar.ai/install.sh | bash
```

That's it. Open your editor. ZLAR-LT is governing every tool call.

Verify it's working:
```bash
~/.zlar-lt/bin/zlar-lt status
```

---

## What it does

ZLAR-LT intercepts every action your AI coding agent takes — before it executes. Each action is matched against a signed policy. Safe actions pass through instantly. Dangerous actions are blocked with a clear reason.

| Allowed | Blocked |
|---------|---------|
| File reads, writes, edits | `rm`, `rm -rf` (file deletion) |
| Glob and grep searches | `sudo`, privilege escalation |
| `ls`, `cat`, `pwd`, `git status` | `curl`, `wget`, `ssh` (network send) |
| `git log`, `git diff`, `git branch` | `git push` (code deployment) |
| Web search | `crontab`, `launchctl` (persistence) |
| | `.ssh` directory writes |
| | `.env` file writes |
| | MCP tools (unknown domain) |
| | Unknown/compound commands |
| | Writes/edits to `~/.zlar-lt/` (self-protection) |
| | Reading the signing key |

**The agent can read, write, edit, and search freely. It cannot delete, escalate, persist, exfiltrate, or push.**

No noise. No permission fatigue. No 5-minute waits. Safe actions are instant. Dangerous actions are stopped.

---

## How it works

```
AI agent tries to run a command
    ↓
Framework hook intercepts the tool call
    ↓
ZLAR-LT gate classifies it (pattern matching, no AI)
    ↓
Matches against Ed25519-signed policy
    ↓
Allow → passes through instantly
Deny  → blocked with clear reason
```

The gate is 857 lines of bash + jq. No daemon, no server, no database, no AI. Deterministic: same input, same output, always. The process runs synchronously inside the framework's hook — if the gate crashes, the action is denied (fail-closed).

---

## Supported frameworks

| Framework | Status | Hook mechanism |
|-----------|--------|---------------|
| **Claude Code** | Verified | PreToolUse hooks |
| **Cursor** | Built from docs | beforeShellExecution, beforeReadFile, beforeMCPExecution |
| **Windsurf** | Built from docs | pre_run_command, pre_write_code, pre_read_code, pre_mcp_tool_use |

The installer auto-detects which frameworks you have and configures hooks for all of them. One policy governs all your editors.

> **Note:** Cursor and Windsurf adapters are built from framework hook documentation and have not yet been tested against live hook payloads. The Claude Code adapter is verified. If you encounter issues with Cursor or Windsurf, please [open an issue](https://github.com/ZLAR-AI/ZLAR-LT/issues) — your real-world payloads will help us verify and fix.

---

## Upgrade path

ZLAR-LT blocks dangerous actions outright. Want to review them case-by-case instead?

```bash
~/.zlar-lt/bin/zlar-lt telegram
```

This connects a Telegram bot. Blocked actions get sent to your phone for approve/deny instead of being instantly blocked. Same gate, same policy, same audit trail — just with human-in-the-loop for edge cases.

For full control (custom policies, availability modes, karma system), see [ZLAR Gate](https://github.com/ZLAR-AI/ZLAR-Gate).

---

## Commands

```bash
~/.zlar-lt/bin/zlar-lt status     # What's governed, policy summary, audit count
~/.zlar-lt/bin/zlar-lt audit      # Last 20 audit decisions
~/.zlar-lt/bin/zlar-lt policy     # Current rules summary
~/.zlar-lt/bin/zlar-lt telegram   # Set up Telegram approval
~/.zlar-lt/bin/zlar-lt uninstall  # Clean removal
~/.zlar-lt/bin/zlar-lt version    # Show version
```

---

## Uninstall

```bash
curl -fsSL https://zlar.ai/uninstall.sh | bash
```

Removes hooks from all frameworks, deletes `~/.zlar-lt/`. Preserves your signing key (`~/.zlar-signing.key`) in case you use it with other ZLAR products.

---

## Requirements

- **macOS** or **Linux**
- **bash 4+** (macOS default is 3.x — `brew install bash`)
- **jq** (`brew install jq` or `apt install jq`)
- **openssl** with Ed25519 support (1.1.1+)
- **curl**

---

## What ZLAR-LT doesn't do

- **It doesn't control the AI model.** It operates at the tool-call layer. The model's reasoning and outputs are outside the enforcement surface.
- **It doesn't guarantee containment.** No governance tool can. It reduces risk by intercepting known-dangerous patterns.
- **It doesn't use AI.** The gate is deterministic pattern matching. No LLM, no ML, no heuristics. Same input, same output, always.
- **It doesn't replace human judgment.** It's a structured mechanism that lets you set boundaries. The quality of governance depends on the policy you use.
- **Cursor and Windsurf adapters are built from framework documentation** and have not been tested against live hook payloads. If you try them and something breaks, tell us.

---

## How it's built

ZLAR-LT packages the same gate engine used by [ZLAR Gate](https://github.com/ZLAR-AI/ZLAR-Gate) and [ZLAR-CC](https://github.com/ZLAR-AI/ClaudeCode_ZLAR-CC). The innovation is the zero-config install — auto-detection, key generation, policy signing, and hook configuration, all in one command.

```
~/.zlar-lt/
├── bin/zlar-gate           # Core gate engine (857 lines, from ZLAR Gate)
├── bin/zlar-policy         # Policy CLI (keygen, sign, verify)
├── bin/zlar-lt             # Convenience CLI
├── adapters/               # Framework-specific hook translators
├── etc/gate.json           # Gate config (Telegram disabled by default)
├── etc/policies/           # Signed policy (deny-heavy default)
├── etc/keys/               # Ed25519 public key
└── var/log/                # Audit trail (JSONL, hash-chained)
```

---

## The ZLAR Family

| Product | Platform | What it does |
|---------|----------|-------------|
| **ZLAR-LT** (this repo) | Claude Code + Cursor + Windsurf | Zero-config governance — one command, instant protection, deny-heavy defaults |
| **[ZLAR Gate](https://github.com/ZLAR-AI/ZLAR-Gate)** | Claude Code + Cursor + Windsurf | Full-control gate — custom policies, Telegram approval, availability modes, karma |
| **[ZLAR-CC](https://github.com/ZLAR-AI/ClaudeCode_ZLAR-CC)** | Claude Code | The original — hook-based gate with Telegram approval, built for Claude Code |
| **[ZLAR-OC](https://github.com/ZLAR-AI/ZLAR-OC)** | OpenClaw | OS-level containment — user isolation, kernel sandbox, pf firewall, gate daemon |

**Pick the right layer:**
- Just want governance running NOW? → **ZLAR-LT** (you're here)
- Want full control over policy and approval? → **ZLAR Gate**
- Only use Claude Code and want the original? → **ZLAR-CC**
- Running autonomous agents that need OS-level containment? → **ZLAR-OC**

---

## License

[Apache License 2.0](LICENSE)

---

*Built by [ZLAR Inc.](https://zlar.ai) — governance infrastructure for AI coding agents.*
