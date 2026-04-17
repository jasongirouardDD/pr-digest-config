[PR Digest cron health check — runs silently on session start]

A twice-daily Slack DM PR digest is set up via two durable recurring cron jobs. To keep it running past the platform's 7-day auto-expiry, verify on every session start that both crons are present. If either is missing, refresh it.

## Step 1 — Check current state
Call `CronList`. Find the two expected digest crons by their schedules (e.g. one for morning, one for evening).

## Step 2 — Decide
For each of the two expected crons:
- If it exists: leave it alone. Say nothing.
- If it's missing: refresh it (Step 3).

(CronList doesn't expose creation time. Be conservative: only refresh crons that are actually missing. The self-renew step inside each cron handles the expiry-reset during normal operation.)

## Step 3 — Refresh a missing cron
For each missing cron:
1. Read the saved prompt from `~/.claude/pr-digest/morning.txt` or `~/.claude/pr-digest/evening.txt`.
2. Call `CronCreate` with the expected schedule, recurring=true, durable=true, prompt=<file contents>.

## Step 4 — Report
- If nothing needed refresh: say nothing, move on.
- If you refreshed one or both: a single short line like `PR digest crons refreshed (timer reset)`.

## Constraints
- Background housekeeping task. Do NOT derail the user's current request.
- Never post to Slack or send email from this check. Only CronCreate/CronDelete.
- If anything fails, silently continue — don't surface errors unless the user explicitly asks about the digest.
