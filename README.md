# Team PR Digest — Claude Code Config

Get a twice-daily Slack DM summarizing every merged PR from your team. Built on top of [Claude Code](https://claude.com/claude-code) + MCPs.

## What you get

- **Morning**: digest of PRs merged in the past 24h
- **Evening**: digest of PRs merged since that morning

Each PR entry includes:
- PR title (linked), repo, merge time in your local timezone
- **What** changed + **UX** impact on one line
- **Why** — framed in product terms, not engineering terms
- Linear / PRD link if referenced

Ends with a single `*No PRs:*` footer line so you see who shipped nothing at a glance.

## Requirements

- [Claude Code](https://claude.com/claude-code) installed
- MCPs connected via `/mcp`:
  - **GitHub** (required)
  - **Slack** (required)
  - **Linear** or similar issue tracker (recommended)
  - **Glean** or employee directory (optional — speeds up GitHub-handle discovery)

## Quick start

See **[SETUP.md](SETUP.md)** — about 5 minutes.

## Files

| File | Purpose |
|---|---|
| [`morning.txt`](morning.txt) | Template cron prompt for the morning digest |
| [`evening.txt`](evening.txt) | Template cron prompt for the evening digest |
| [`startup-check.md`](startup-check.md) | SessionStart hook instructions (auto-renew crons past 7-day expiry) |
| [`session-start-hook.json`](session-start-hook.json) | Hook JSON to merge into `~/.claude/settings.json` |
| [`team_github_handles.md.example`](team_github_handles.md.example) | Roster template |
| [`SETUP.md`](SETUP.md) | Full setup guide |

## How it works

1. **Claude Code's built-in cron** (`CronCreate`) schedules two durable recurring jobs.
2. When each fires, Claude runs the prompt in `morning.txt` / `evening.txt`: queries GitHub for merged PRs by your team, enriches with Linear ticket context, formats Slack mrkdwn, DMs you.
3. **7-day expiry workaround**: each run ends with `CronDelete` + `CronCreate` to reset the clock. If Claude Code is closed for >7 days, the SessionStart hook rebuilds the crons on next launch from the saved prompts.
4. All state is local — under `~/.claude/`. No cloud service.

## Customization

- **Weekly wrap-up** — add a third cron for Friday afternoon covering the past week
- **Group by project** — edit the prompt to section by Linear project instead of roster order
- **Slack channel instead of DM** — swap `channel_id` for a channel ID (digests can be long; use shared channels sparingly)
- **Email fallback** — use the Gmail MCP as a secondary delivery

## License

MIT
