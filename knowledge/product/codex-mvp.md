# Codex MVP

## 目标

启动桌宠后，开发者在本地 Codex CLI 中执行一个 Coding 任务；该 Agent 轮次完成时，桌宠自动切换为完成动画，并发送 macOS 系统通知。

## 用户链路

1. 用户启动 AI Coding Desktop Pet。
2. 用户在 Codex CLI 发起任务。
3. Codex 调用配置中的 `notify` 程序。
4. `ai-pet-hook` 将事件发送到本机监听服务。
5. 桌宠切换完成动画，macOS 发出系统通知。

## 第一版范围

- 桌宠基础窗口：透明、Always on Top、可拖动、具备基础状态动画。
- Codex `notify` 完成事件转发。
- Rust 本机 HTTP Listener 和内存态 Task State Manager。
- Tauri 事件驱动 React UI 更新。
- macOS 原生完成通知。

## 非目标

- 不实现 Claude Code、Cursor、Gemini CLI Adapter。
- 不实现 SQLite、历史统计、多任务控制中心。
- 不通过一次完成通知推断完整的 `RUNNING`、`WAITING_USER` 或 `APPROVAL_REQUIRED` 生命周期。

## 验收标准

在真实 Codex CLI 任务上验证：单轮完成事件经 Hook 到达本地应用，桌宠进入完成状态且系统通知出现。Hook 失败不得阻塞 Codex CLI。

