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

这个 skill 让 Claude Code 通过读取系统时钟来精确感知当前时间。

## The core problem this solves / 核心问题

Claude often carries stale time context between conversations. A common failure mode:

> User had a conversation on May 26. Two days later, opens a new conversation on May 28.
> Claude still thinks it's May 26 because it's referencing old context instead of checking the real clock.

Claude 经常在跨对话时携带过时的时间上下文：

> 用户 5月26日 聊了一次。两天后新开对话，Claude 还以为是 5月26日——因为它引用的是旧上下文，而不是真实时钟。

This skill prevents that by reading the actual system time every time.

## How to use / 使用方法

Run the appropriate command based on the operating system:

### Windows (PowerShell)
```powershell
"$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss (dddd)') | Timezone: $(Get-Date -Format 'zzz')"
```

### macOS / Linux (Bash)
```bash
date "+%Y-%m-%d %H:%M:%S (%A) | Timezone: %Z (UTC%z)"
```

### Output example / 输出示例
```
2026-05-26 22:40:15 (Tuesday) | Timezone: -07:00
```

## After getting the time: what to do with it / 拿到时间后怎么用

Don't just dump the raw output. Use the time intelligently across these dimensions:

不要只是输出原始结果，要在以下维度智能地使用时间：

### 1. Cross-conversation time gap detection / 跨对话时间断层检测 (most important / 最重要)

Compare the current time against any date context you see (from system prompts, memory files, or prior conversation context). If there's a mismatch:

对比当前时间和你看到的任何日期上下文（系统提示、记忆文件、之前的对话）。如果有偏差：

- **State it explicitly / 明确说出来**: "It's now May 28. The last conversation was May 26 — 2 days ago." / "现在是 5月28日。上次对话是 5月26日，已经过了 2 天。"
- **Reassess assumptions / 重新评估假设**: Anything that was "today" or "tomorrow" in a previous context is now stale. Don't carry forward time-relative references from old conversations. / 上次对话里的"今天"、"明天"现在已经过时了，不要延续旧对话的时间引用。
- **Check memory staleness / 检查记忆保鲜度**: Compare current date against `last_updated` fields in memory files. Flag anything older than 30 days as potentially outdated. / 对比当前日期和记忆文件的 `last_updated` 字段，超过 30 天的标记为可能过时。

### 2. Key date countdowns / 关键日期倒计时

If the user has important dates stored in memory (project launches, contract renewals, deadlines), proactively calculate and mention countdowns when relevant:

如果用户的记忆中有重要日期（项目上线、合同到期、deadline），相关时主动计算倒计时：

- "3 days until the deadline" / "距 deadline 还有 3 天"
- "The contract expires in 2 weeks" / "合同 2 周后到期"
- "Your presentation is tomorrow" / "你的演示是明天"

Don't spam every countdown every time — only mention the ones relevant to the current conversation topic.

不要每次都列出所有倒计时——只提与当前话题相关的。

### 3. Day-of-week and time-of-day awareness / 星期与时段感知

Use this context to give situationally appropriate responses:

利用时间上下文给出符合情境的回复：

| Time context / 时间维度 | How to use it / 如何使用 |
|---|---|
| **Weekday vs weekend / 工作日 vs 周末** | Weekday → suitable for outreach, follow-ups. Weekend → don't suggest sending work emails / 工作日适合联络和跟进，周末别建议发工作邮件 |
| **Morning / Afternoon / Evening / 早中晚** | Adjust greetings and tone. Don't say "good morning" at 11pm / 调整问候语和语气，晚上 11 点别说"早上好" |
| **Business hours / 工作时间** | Consider whether it's business hours in the recipient's timezone before suggesting outreach / 建议联系别人前，考虑对方时区是否在工作时间 |

### 4. Cross-timezone coordination / 跨时区协调

The user's timezone is in the output (e.g., `-07:00` = US Pacific). When the conversation involves people in other timezones, calculate and mention the time difference:

输出中包含用户时区（如 `-07:00` = 美国太平洋时间）。当对话涉及其他时区的人时，计算并提及时差：

- **Common timezone offsets / 常用时区换算**:
  - UTC+8 (China, Singapore, Taiwan, HK): If it's 10pm UTC-7, it's 1pm next day UTC+8
  - UTC+9 (Japan, Korea): +16 hours from US Pacific
  - UTC-4/-5 (US East Coast): +3 hours from US Pacific
  - UTC+0/+1 (UK, Western Europe): +7/+8 hours from US Pacific

- Mention timezone differences when relevant: "It's 1pm in Tokyo right now — good time to call" / "东京现在下午 1 点，可以打电话"

### 5. Information freshness check / 信息保鲜检查

When reading memory files or any stored data with timestamps:

读取带时间戳的记忆文件或存储数据时：

- `last_updated` within 7 days → information is fresh, use confidently / 7 天内 → 信息新鲜，放心使用
- `last_updated` 7-30 days ago → probably still valid, but mention the age / 7-30 天 → 可能仍有效，但提及更新时间
- `last_updated` 30+ days ago → flag it: "This info was updated X days ago — might need verification" / 30 天以上 → 标记："这条信息 X 天前更新的，可能需要确认"

Pay special attention to fast-changing fields like: job search status, current role, project status, course enrollment, visa/legal status.

特别关注快速变化的字段：求职状态、当前职位、项目状态、课程注册、签证/法律状态。

## When to run proactively / 何时主动运行

Run this at the very start of a conversation if:

在以下情况下，对话一开始就运行：

- The CLAUDE.md instructions say to get the time at conversation start (follow this always) / CLAUDE.md 指示在对话开始时获取时间（始终遵守）
- The user's question involves anything time-sensitive / 用户的问题涉及任何时间敏感内容
- You see a date in the system prompt or context that might be stale / 你看到系统提示或上下文中的日期可能过时
- The user references deadlines, schedules, countdowns, or "how long until X" / 用户提到 deadline、日程、倒计时
- You need to assess whether stored information is current / 你需要评估存储的信息是否仍然有效
