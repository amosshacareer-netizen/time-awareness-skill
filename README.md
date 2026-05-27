# time-awareness

A Claude Code skill that gives Claude precise knowledge of the current date, time, day of week, and timezone — solving the common problem where Claude carries stale time context between conversations.

一个让 Claude Code 精确感知当前时间的 skill——解决 Claude 在跨对话时"记错日期"的常见问题。

## The Problem / 问题

Claude often doesn't know what time it is. Worse, it frequently carries stale dates from previous conversations:

> You chatted with Claude on May 26. Two days later, you open a new conversation.
> Claude still thinks it's May 26.

Claude 经常不知道现在几点。更糟的是，它会把上次对话的日期带到新对话里：

> 你 5月26日 和 Claude 聊过。两天后开了新对话，Claude 还以为今天是 5月26日。

This skill fixes that by reading the actual system clock.

## What It Does / 功能

- **Reads current system time** — works on Windows, macOS, and Linux / 读取系统时间（跨平台）
- **Detects cross-conversation time gaps** — notices when the date has changed / 检测跨对话的时间断层
- **Checks memory staleness** — flags outdated information in memory files / 检测记忆文件是否过时
- **Calculates countdowns** — counts down to deadlines and milestones / 自动计算倒计时
- **Time-of-day awareness** — morning, afternoon, evening; weekday vs weekend / 时段感知（早中晚、工作日/周末）
- **Cross-timezone coordination** — converts between timezones / 跨时区换算

## Install / 安装

```bash
npx skills add amosshacareer-netizen/time-awareness-skill
```

## Example Output / 输出示例

```
2026-05-26 22:40:15 (Tuesday) | Timezone: -07:00
```

Claude then uses this context to / Claude 会利用时间信息来:
- Greet you appropriately based on time of day / 根据时段调整问候语
- Flag stale data ("this memory file was last updated 45 days ago") / 标记过时数据
- Calculate countdowns ("3 days until the deadline") / 计算倒计时
- Coordinate across timezones ("it's 1pm in Tokyo right now") / 跨时区协调

## Usage / 使用方式

The skill triggers automatically when you / 以下情况自动触发:

- Ask "what time is it?" / "几点了" / "今天几号"
- Mention deadlines, schedules, or countdowns / 提到 deadline、日程、倒计时
- Start a new conversation (if configured in CLAUDE.md) / 新对话开始时（需配置）

### Recommended CLAUDE.md setup / 推荐配置

Add this to your `~/.claude/CLAUDE.md` to enable automatic time injection:

把以下内容加到 `~/.claude/CLAUDE.md`，让每次新对话自动获取时间：

```markdown
> At the start of every new conversation:
> 1. Run `Get-Date` (Windows) or `date` (macOS/Linux) to get the current time
> 2. Then proceed with the user's request
```

## Contributing / 贡献

Issues and PRs welcome! If you have ideas for improving time awareness in Claude Code, feel free to contribute.

欢迎提 Issue 和 PR！如果你有改进 Claude Code 时间感知的想法，随时贡献。

## License

MIT
