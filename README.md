# time-awareness

A Claude Code skill that gives Claude precise knowledge of the current date, time, day of week, and timezone — solving the common problem where Claude carries stale time context between conversations.

## The Problem

Claude often doesn't know what time it is. Worse, it frequently carries stale dates from previous conversations:

> You chatted with Claude on May 26. Two days later, you open a new conversation.
> Claude still thinks it's May 26.

This skill fixes that by reading the actual system clock.

## What It Does

- **Reads current system time** (works on Windows, macOS, and Linux)
- **Detects cross-conversation time gaps** — notices when the date has changed since the last conversation
- **Checks memory staleness** — compares current date against `last_updated` fields in memory files
- **Calculates countdowns** — automatically counts down to deadlines, milestones, and important dates
- **Time-of-day awareness** — knows if it's morning, afternoon, or evening; weekday or weekend
- **Cross-timezone coordination** — converts between timezones for international communication

## Install

```bash
npx skills add amosshacareer-netizen/time-awareness-skill
```

## Example Output

```
2026-05-26 22:40:15 (Tuesday) | Timezone: -07:00
```

Claude then uses this context to:
- Greet you appropriately ("Amos哥 这么晚还在忙")
- Flag stale data ("this memory file was last updated 45 days ago")
- Calculate countdowns ("18 days until graduation")
- Coordinate across timezones ("it's 1pm in Singapore right now")

## Usage

The skill triggers automatically when you:
- Ask "what time is it?" / "几点了" / "今天几号"
- Mention deadlines, schedules, or countdowns
- Start a new conversation (if configured in CLAUDE.md)

### Recommended CLAUDE.md setup

Add this to your `~/.claude/CLAUDE.md` to enable automatic time injection at conversation start:

```markdown
> At the start of every new conversation:
> 1. Run `Get-Date` (Windows) or `date` (macOS/Linux) to get the current time
> 2. Then proceed with the user's request
```

## License

MIT
