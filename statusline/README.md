# Statusline

[![en](https://img.shields.io/badge/lang-en-blue.svg)](README.md)
[![ru](https://img.shields.io/badge/lang-ru-green.svg)](README.ru.md)

Custom statusline for Claude Code showing context usage in tokens, rate limits, and session cost. Zero network calls — everything comes from the JSON payload Claude Code pipes into the script.

## Preview

```
project • Fable 5 • 260k/1M • [ h 27% 46m │ W 36% ] • $3.43
```

- **project** — Current directory name (git branch shown only if it's not `main`/`master`)
- **Fable 5** — Current model (dim)
- **260k/1M** — Context window: tokens used / window size. The headline — always bold
- **[ h 27% 46m │ W 36% ]** — Rate limits block: 5-hour window used % + time until reset, weekly window used %
- **$3.43** — Session cost (dim, trailing)

## Visual hierarchy

Tokens are the loudest element; everything calm recedes into dim gray. No green anywhere — low usage is non-information.

| Used | Tokens | Limits |
|---|---|---|
| < 50% | bold white | dim gray |
| 50–80% | bold yellow | yellow |
| > 80% | bold red | bold red |

## Requirements

- `jq` — JSON processing
- Claude Code **v2.1.80+** — `rate_limits` in the statusline payload
- Claude Pro/Max subscription — `rate_limits` is only sent for subscribers

```bash
brew install jq
```

## Installation

1. Copy script to Claude config:

```bash
cp statusline.sh ~/.claude/
chmod +x ~/.claude/statusline.sh
```

2. Add to `~/.claude/settings.json`:

```json
{
  "statusLine": {
    "type": "command",
    "command": "~/.claude/statusline.sh"
  }
}
```

3. Restart Claude Code.

## How It Works

The script receives JSON from Claude Code via stdin and reads everything from it:

1. `context_window.total_input_tokens` / `context_window_size` → token counter (`260k/1M`)
2. `rate_limits.five_hour` / `rate_limits.seven_day` → used % and reset countdown. Available since v2.1.80; appears after the first API response in a session, so the last seen value is cached in `/tmp/claude-rate-limits.json` and used as a fallback
3. `cost.total_cost_usd` → session cost

No OAuth tokens, no API polling, no python — just `jq` and bash.
