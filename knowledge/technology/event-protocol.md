# 统一事件协议

所有平台事件进入核心状态层前应转换为统一结构：

```json
{
  "version": 1,
  "source": "codex",
  "session_id": "abc123",
  "workspace": "/repo/backend",
  "event": "COMPLETED",
  "message": "Codex task finished",
  "timestamp": 1793952000
}
```

## 字段约束

- `version`：协议版本，当前为 `1`。
- `source`：事件来源平台；MVP 只接受 `codex`。
- `session_id`：来源会话标识；Codex notify 实际字段映射需以真实 payload 验证。
- `workspace`：相关工作区绝对路径；缺失时的策略待真实 payload 验证。
- `event`：统一状态事件；MVP 核心值为 `COMPLETED`。
- `message`：供 UI 或日志显示的简短说明。
- `timestamp`：Unix 时间戳；秒或毫秒单位必须在实现前固定并通过测试锁定。示例意图为秒级，但数值需验证。

## 验证原则

- Adapter 负责平台 payload 到统一协议的转换与缺失字段处理。
- Core 和 UI 只依赖统一协议，不读取 Codex 私有字段。
- 使用真实 Codex notify 样本建立 fixture 和解析测试；未知字段不应导致 Hook 阻塞。
- 对来源、版本、事件枚举、字符串长度和请求体总大小设置边界。

## Codex CLI 本机映射

本机原 Codex CLI `0.133.0` legacy notify 实现可确认包含以下字段：

- `type`：只接受 `agent-turn-complete`。
- `thread-id`：映射到统一协议 `session_id`。
- `turn-id`：当前接收但不进入 MVP 核心状态。
- `cwd`：映射到统一协议 `workspace`，必须为绝对路径。
- `input-messages`、`last-assistant-message`：Hook 可收到，但桌宠不保存、不记录、不显示。

解析测试使用与上述本机字段一致的合成 fixture。升级到 Codex CLI `0.167.0` 后，真实只读单轮任务产生的 notify 已被当前 Adapter 接收；验证过程未保存真实 payload。`timestamp` 固定为事件被 Adapter 接收时的 Unix 秒级时间戳。

## 已知限制

Codex `notify` 主要提供轮次完成事件。它不足以独立、准确地产生任务开始、等待用户输入或等待授权事件；这些状态必须等待其他可靠事件接口或会话监听能力。

