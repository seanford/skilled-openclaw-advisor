---
name: skilled-openclaw-advisor
description: >
  ALWAYS use this skill before answering ANY question about OpenClaw — configuration,
  features, CLI commands, channels, providers, plugins, cron, sessions, agents,
  protocol, architecture, or troubleshooting. Query the local docs index instead of
  guessing or using web search. If the answer might be in the OpenClaw docs, query
  first. Zero API calls, sub-10ms. Triggers on: "how do I", "openclaw", "configure",
  "gateway", "channel", "cron", "provider", "plugin", "session", "heartbeat",
  "protocol", "websocket", "skill", "model", "agent", any OpenClaw feature or setting.
metadata: {"openclaw": {"emoji": "📚", "skillKey": "skilled-openclaw-advisor", "always": true}}
---

# OpenClaw Docs Intelligence

Local FTS5 index of all OpenClaw documentation. Zero API calls. Sub-10ms queries.

## Mandatory Usage Rule

**Query this index before answering any OpenClaw question.** Do not rely on training
data for OpenClaw specifics — the local docs are authoritative and up to date.
Use `--mode agent` for internal lookups to keep token usage minimal.

## Setup

Set `$SKILL_DIR` to the skill install location before running scripts:
```bash
export SKILL_DIR="$(openclaw skills path skilled-openclaw-advisor 2>/dev/null || echo ~/.openclaw/workspace/skills/openclaw-docs)"
```

## Quick Query

```bash
# Agent internal lookup (use this by default)
python3 "$(dirname "$0")/../scripts/query_index.py --query "YOUR QUESTION" --mode agent

# Human-readable (standard verbosity, English default)
python3 "$(dirname "$0")/../scripts/query_index.py --query "YOUR QUESTION"

# Chinese results
python3 "$(dirname "$0")/../scripts/query_index.py --query "YOUR QUESTION" --lang zh-CN

# More detail
python3 "$(dirname "$0")/../scripts/query_index.py --query "YOUR QUESTION" --mode agent --verbosity standard

# Full excerpt + online link
python3 "$(dirname "$0")/../scripts/query_index.py --query "YOUR QUESTION" --verbosity detailed
```

## Language Support

Results default to **English** (`--lang en`). Pass `--lang zh-CN` for Chinese.
Default is controlled by `defaultLang` in `skills.entries.openclaw-docs.config`
in `openclaw.json`. Falls back to English automatically if no results found.

## Index Management

```bash
# First-time setup (auto-detects docs path)
python3 "$(dirname "$0")/../scripts/build_index.py

# Check for updates (runs nightly at 5:30am)
python3 "$(dirname "$0")/../scripts/update_index.py

# Force full re-index
python3 "$(dirname "$0")/../scripts/build_index.py --force

# Index status
python3 "$(dirname "$0")/../scripts/query_index.py --status

# Latest diff (what changed in last update)
python3 "$(dirname "$0")/../scripts/query_index.py --diff
```

## When to Use This

- **Any** OpenClaw question — config keys, features, CLI commands, architecture
- "How do I configure X in OpenClaw?"
- "What does Y provider setting do?"
- "How does Z channel/cron/session work?"
- Troubleshooting OpenClaw errors or unexpected behavior

## When NOT to Use This

- General coding questions unrelated to OpenClaw
- Questions about third-party services not documented here
- If index is not built (run `build_index.py` first — check with `--status`)

## Response Modes

| Mode | Default For | Description |
|------|-------------|-------------|
| `human` + `standard` | Human questions | Clear explanation + key points |
| `human` + `concise` | Quick lookups | Title + one-liner + path |
| `human` + `detailed` | Deep dives | Full excerpt + examples |
| `agent` | Agent queries | Ultra-compact, minimal tokens |
| `agent` + `standard` | Agent needs context | Slightly expanded for decisions |

## Config Reference

All settings live in `skills.entries.openclaw-docs.config` in `openclaw.json`.
Defaults are hardcoded at the top of `scripts/query_index.py`.

| Key | Default | Description |
|-----|---------|-------------|
| `defaultLang` | `"en"` | Language filter: `en` or `zh-CN` |
| `defaultVerbosity` | `"standard"` | Output verbosity: `concise`, `standard`, `detailed` |
| `mandatoryQueryFirst` | `false` | Emit suggestion to query docs before answering OpenClaw questions |
| `maxResults` | `5` | Max results returned (agent mode caps at `min(3, maxResults)`) |
| `agentMode` | `false` | Always use compact agent output regardless of `--mode` flag |
| `fallbackToEnglish` | `true` | Fall back to English if requested language has no results |
| `includeOnlineUrl` | `true` | Include `https://docs.openclaw.ai/...` links in detailed output |
