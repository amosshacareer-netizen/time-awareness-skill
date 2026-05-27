# time-awareness

[English README](./README.md)

一个让 Claude Code 精确感知当前时间的 skill——解决 Claude 在跨对话时"记错日期"的常见问题。

## 解决什么问题

Claude 经常不知道现在几点。更糟的是，它会把上次对话的日期带到新对话里：

> 你 5月26日 和 Claude 聊过。两天后开了新对话，Claude 还以为今天是 5月26日。

这个 skill 通过读取系统真实时钟来修复这个问题。

## 功能

- **读取系统时间** — 支持 Windows、macOS、Linux
- **跨对话时间断层检测** — 发现上次对话和这次之间过了多久
- **记忆保鲜度检查** — 对比当前日期和记忆文件的 `last_updated` 字段，标记过时信息
- **关键日期倒计时** — 自动计算距离 deadline、里程碑还有多久
- **时段感知** — 知道现在是早上、下午还是晚上；工作日还是周末
- **跨时区换算** — 自动换算不同时区的当前时间

## 安装

```bash
npx skills add amosshacareer-netizen/time-awareness-skill
```

## 输出示例

```
2026-05-26 22:40:15 (Tuesday) | Timezone: -07:00
```

Claude 会利用这些时间信息来：
- 根据时段调整问候语（晚上 11 点不会说"早上好"）
- 标记过时数据（"这个记忆文件 45 天前更新的，可能需要确认"）
- 计算倒计时（"距 deadline 还有 3 天"）
- 跨时区协调（"东京现在下午 1 点，可以打电话"）

## 使用方式

以下情况会自动触发：

- 问"几点了"、"今天几号"、"现在什么时间"
- 提到 deadline、日程、倒计时
- 新对话开始时（需在 CLAUDE.md 中配置）

### 推荐配置

把以下内容加到 `~/.claude/CLAUDE.md`，让每次新对话自动获取时间：

```markdown
> 每次新对话开始时：
> 1. 运行 `Get-Date`（Windows）或 `date`（macOS/Linux）获取当前时间
> 2. 然后再开始回答用户的问题
```

## 贡献

欢迎提 Issue 和 PR！如果你有改进 Claude Code 时间感知的想法，随时贡献。

## License

MIT
