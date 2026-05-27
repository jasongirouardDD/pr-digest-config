[PR Digest morning cron health check — runs silently on session start]

A daily 7:03 AM Slack DM PR digest is set up via a durable recurring cron job. To keep it running past the platform's 7-day auto-expiry, verify on every session start that the cron is present. If missing, refresh it.

## Step 1 — Check current state
Call `CronList`. Find the morning digest cron by its schedule (`3 7 * * *`).

## Step 2 — Decide
- If it exists: leave it alone. Say nothing.
- If it's missing: refresh it (Step 3).

(CronList doesn't expose creation time. Be conservative: only refresh crons that are actually missing. The self-renew step inside each cron handles the expiry-reset during normal operation.)

## Step 3 — Refresh the missing cron
1. Read the saved prompt from `~/.claude/routines/pr-digest-morning.txt`.
2. Call `CronCreate` with schedule `3 7 * * *`, recurring=true, durable=true, prompt=<file contents>.

## Step 4 — Report
- If nothing needed refresh: say nothing, move on.
- If you refreshed it: a single short line like `PR digest morning cron refreshed (timer reset)`.

## Constraints
- Background housekeeping task. Do NOT derail the user's current request.
- Never post to Slack or send any messages. Only CronCreate/CronDelete.
- If anything fails, silently continue — don't surface errors unless the user explicitly asks about the digest.
- No repo attachment needed. Uses GitHub MCP, Linear MCP, and Slack MCP at cron fire time.
