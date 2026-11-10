# 系统架构

## 技术选型

| 模块 | 技术 | 职责 |
| --- | --- | --- |
| 桌面框架 | Tauri 2 | macOS 桌面应用与窗口能力 |
| 前端 | React + TypeScript | 桌宠动画和任务面板 |
| 后端 | Rust | 事件监听、状态管理与系统集成 |
| 平台集成 | Hooks / Notify | 接收 AI Coding 状态事件 |
| 本地通信 | HTTP / Unix Socket | Agent 与桌宠通信；MVP 使用 localhost HTTP |
| 持久化 | SQLite | 后续的任务记录和历史统计，不属于 MVP |
| 通知 | macOS Native Notification | 完成与操作提醒 |

React Native 不在当前方案中；React + Tauri 覆盖桌面 UI，Rust 承担操作系统能力。

## MVP 数据流

```text
Codex CLI
  -> notify = [".../ai-pet-hook"]
  -> ai-pet-hook
  -> POST http://127.0.0.1:17891/events
  -> Rust HTTP Listener
  -> Task State Manager
  -> Tauri Event
  -> React Desktop Pet + macOS Notification
```

## 组件边界

- `adapters/codex.rs`：解析 Codex 输入并生成统一事件。
- `server.rs`：仅接收回环地址请求、限制 payload 并交给 Adapter/状态层。
- `task_manager.rs`：维护内存状态，不解析平台私有格式。
- `notification.rs`：封装系统通知。
- React UI：消费统一状态，不包含 Codex 专用判断。
- `scripts/ai-pet-hook`：快速转发，失败时尽快退出，不能阻塞 Codex。

## 当前源码结构

```text
repositories/coding-pet/
  src/
    components/Pet.tsx
    App.tsx
  src-tauri/src/
    main.rs
    server.rs
    task_manager.rs
    notification.rs
    adapters/codex.rs
  scripts/ai-pet-hook
```

## 安全与稳定性

- 监听地址限定为 `127.0.0.1` 或等价回环接口。
- 对请求体设置明确大小上限，并拒绝格式不合法的事件。
- 不在 Hook 中执行长任务；桌宠未启动或请求失败时不影响 Codex 主流程。
- MVP 使用内存状态，避免提前引入数据库或消息队列。

## 当前工程基线

- Node.js `20.19.5`，npm `10.8.2`。
- Rust `1.90.0`，由应用仓库根目录 `repositories/coding-pet/rust-toolchain.toml` 固定。
- Tauri JavaScript API/CLI `2.12.4`，Rust crate `2.12.5`。
- Tauri Notification Plugin `2.8.0`，Axum `0.8.9`。
- React `19.3.1`、TypeScript `6.0.3`、Vite `8.3.6`；精确版本以 lockfile 为准。

## 桌宠窗口实现

- Tauri 主窗口静态配置使用 `decorations: false`、`transparent: true`、`shadow: false` 和 `alwaysOnTop: true`；不在 React 启动后重复调用窗口 setter。
- React 窗口根节点使用 `data-tauri-drag-region="deep"`，使卡片背景、宠物图形和状态文字等非交互后代都可拖动窗口。
- Tauri 深层拖拽区域内部仍调用原生 `window.start_dragging` 命令，因此主窗口 capability 必须保留 `core:window:allow-start-dragging`；删除该权限会产生 `window.start_dragging not allowed` 并导致拖拽失效。
- `.pet-card` 的圆角、半透明背景、边框和阴影属于应用内容视觉，不属于 macOS 原生窗口装饰。
- 2026-11-10 的 macOS debug 应用验证覆盖三类拖拽区域、无原生装饰、透明画布、Always on Top，以及合成 Codex 完成事件触发 React `COMPLETED` 状态；系统通知的发布包展示效果仍按“待补充”项处理。

## 当前监听约束

- HTTP Listener 固定绑定 `127.0.0.1:17891`，只接受 `POST /events`。
- 请求体上限为 64 KiB；JSON 或事件语义不合法时拒绝请求。
- 通知只显示固定完成文案，不包含 prompt、代码或 Assistant 输出。
- `ai-pet-hook` 连接超时 0.2 秒、总超时 1 秒，任何转发失败都以退出码 0 返回 Codex。

## 待补充

- 最低 macOS 版本和芯片支持范围。
- macOS 系统通知在不同签名、安装和系统通知设置下的实际展示行为仍需发布形态验证。

