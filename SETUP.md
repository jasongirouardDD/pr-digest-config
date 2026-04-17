# Setup

~5 minutes end-to-end.

## 1. Prerequisites

Install [Claude Code](https://claude.com/claude-code). In Claude Code, type `/mcp` and connect:

- **GitHub** (required)
- **Slack** (required)
- **Linear** or your issue tracker (recommended)
- Employee directory MCP like Glean (optional — speeds up GitHub-handle discovery)

Find your **Slack user ID**:
1. Slack → click your profile picture → **View profile**
2. **⋯** (more) → **Copy member ID**
3. Looks like `U0XXXXXXXXX`

## 2. Open Claude Code and paste this setup prompt

Replace `<YOUR_SLACK_USER_ID>`, `<YOUR_GITHUB_ORG>`, and the team roster before pasting.

```
I want a twice-daily Slack DM summarizing PRs merged by my team.

Schedule:
- Morning digest around 7 AM local time, covering the past 24 hours
- Evening digest around 5 PM local time, covering merges since morning

Deliver as a Slack DM to my user ID: <YOUR_SLACK_USER_ID>
GitHub org to query: <YOUR_GITHUB_ORG>

Team roster (names + work emails — please discover GitHub handles automatically via the employee directory, then fall back to searching PRs):
- <Full Name> — <email>
- <Full Name> — <email>
- ...

For each PR, include:
- PR title (linked), repo, merge time in my local timezone
- *What:* 1-sentence technical change  *UX:* 1-sentence visible effect (or *n/a* for backend/proto/string PRs)
- *Why:* framed in PRODUCT terms ("Groundwork for enabling X"), NOT engineering terms ("Downstream services need Y")
- Linear ticket / PRD link if referenced

Slack formatting (Slack mrkdwn, NOT Markdown):
- *bold* single asterisks — never **bold**
- Links: <URL|text> — never [text](url)
- No italics
- Bullets: • top-level, ◦ sub-bullets indented 4 spaces

Each digest ends with a single line: *No PRs:* <First L>, <First L>, ... listing anyone who shipped nothing.

Before scheduling, run ONE test digest so I can verify the format. After I confirm, set up:
1. Two durable recurring cron jobs with the finalized prompts
2. A SessionStart hook in ~/.claude/settings.json that auto-refreshes the crons on launch (to work around the 7-day platform expiry)

Reference templates at https://github.com/jasongirouardDD/pr-digest-config if you need the exact prompt or hook structure.
```

## 3. Iterate on format

Claude sends a test digest. Give it feedback — it'll update the cron prompts automatically:

- "Put What + UX on one line to save vertical space"
- "Bold the PR titles, repo names, and timestamps too"
- "Drop the team section headers; list authors inline"
- "End with a single `No PRs:` footer instead of listing each zero-PR person separately"
- "Frame Why in product terms, not engineering terms"

## 4. Activate the SessionStart hook

After Claude sets up the hook, restart Claude Code or type `/hooks` once to reload config. On every launch thereafter, the hook silently verifies both crons exist and recreates them from `~/.claude/pr-digest/morning.txt` and `evening.txt` if missing.

## 5. Day-to-day maintenance

- **Add a teammate**: "Add <name> (<email>) to the PR digest."
- **Remove a teammate**: "Remove <name>."
- **Force-refresh crons**: "Refresh the PR digest crons."
- **Test run**: "Send me a test PR digest."

## Troubleshooting

**No digest arrived**
- Claude Code needs to be open and idle. If your laptop was closed or Claude Code wasn't running, the cron didn't fire.
- Ask Claude "list my crons" — if missing, say "refresh the PR digest crons."

**Format's slightly off**
- "Send me a test PR digest right now" and point at what's wrong. Claude updates the prompt.

**Want true 24/7 reliability**
- You'd need a cloud-hosted rebuild (GitHub Actions + Anthropic API). Not in this repo.

**Hook not firing**
- Check `~/.claude/settings.json` has a `SessionStart` entry. `/hooks` to reload, or restart.
