---
name: time-awareness
description: >
  Inject the current system date, time, day of week, and timezone into the conversation.
  Use this skill whenever the user asks "what time is it", "what's today's date",
  "what day is it", or any time-related question. Also use proactively at the start of
  every new conversation to establish time context — this prevents the common problem
  where Claude thinks it's still the date of a previous conversation. Essential for
  scheduling, deadlines, time-sensitive decisions, staleness detection in memory files,
  and cross-timezone coordination.
  Trigger on: "几点了", "今天几号", "现在什么时间", "what time", "what date",
  "today's date", "current time", "what day", "schedule", "deadline", "due date",
  "还有几天", "多久了", "countdown", "when is", "how long until",
  or any context where knowing the precise current time would improve the response.
---

# Time Awareness

This skill gives Claude Code precise knowledge of the current date and time by reading it from the user's system clock.

## The core problem this solves

Claude often carries stale time context between conversations. A common failure mode:

> User had a conversation on May 26. Two days later, opens a new conversation on May 28.
> Claude still thinks it's May 26 because it's referencing old context instead of checking the real clock.

This skill prevents that by reading the actual system time every time.

## How to use

Run the appropriate command based on the operating system:

### Windows (PowerShell)
```powershell
"$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss (dddd)') | Timezone: $(Get-Date -Format 'zzz')"
```

### macOS / Linux (Bash)
```bash
date "+%Y-%m-%d %H:%M:%S (%A) | Timezone: %Z (UTC%z)"
```

### Output example
```
2026-05-26 22:40:15 (Tuesday) | Timezone: -07:00
```

## After getting the time: what to do with it

Don't just dump the raw output. Use the time intelligently across these dimensions:

### 1. Cross-conversation time gap detection (most important)

Compare the current time against any date context you see (from system prompts, memory files, or prior conversation context). If there's a mismatch:

- **State it explicitly**: "现在是 5月28日。上次对话是 5月26日，已经过了 2 天。"
- **Reassess assumptions**: Anything that was "today" or "tomorrow" in a previous context is now stale. Don't carry forward time-relative references from old conversations.
- **Check memory staleness**: Compare current date against `last_updated` fields in memory files. Flag anything older than 30 days as potentially outdated.

### 2. Key date countdowns

If the user has important dates stored in memory (graduation, OPT start, deadlines), proactively calculate and mention countdowns when relevant:

- "距毕业还有 19 天"
- "OPT 62 天后生效"
- "这个 deadline 是后天"

Don't spam every countdown every time — only mention the ones relevant to the current conversation topic.

### 3. Day-of-week and time-of-day awareness

Use this context to give situationally appropriate responses:

| Time context | How to use it |
|---|---|
| **Weekday vs weekend** | Weekday → suitable for outreach, follow-ups, scheduling. Weekend → don't suggest sending cold emails |
| **Morning / Afternoon / Evening** | Adjust greetings and tone. 晚上 11 点别说"早上好" |
| **Business hours** | If suggesting someone reach out to a contact, consider whether it's business hours in the recipient's timezone |

### 4. Cross-timezone coordination

The user's timezone is in the output (e.g., `-07:00` = US Pacific). When the conversation involves people in other timezones:

- **China (UTC+8)**: Pacific + 15 hours. If it's 10pm Pacific, it's 1pm next day in China.
- **Singapore (UTC+8)**: Same as China.
- **US East Coast (UTC-4/-5)**: Pacific + 3 hours.

Mention timezone differences when relevant (e.g., "现在新加坡那边是下午 1 点，可以联系").

### 5. Information freshness check

When reading memory files or any stored data with timestamps:

- `last_updated` is within 7 days → information is fresh, use confidently
- `last_updated` is 7-30 days ago → probably still valid, but mention the age
- `last_updated` is 30+ days ago → flag it: "这条信息是 X 天前更新的，可能需要确认是否还准确"

Pay special attention to fast-changing fields like: job search status, current role, relationship status, course enrollment, visa status.

## When to run proactively

Run this at the very start of a conversation if:
- The CLAUDE.md instructions say to get the time at conversation start (follow this always)
- The user's question involves anything time-sensitive
- You see a date in the system prompt or context that might be stale
- The user references deadlines, schedules, countdowns, or "how long until X"
- You need to assess whether stored information is current
