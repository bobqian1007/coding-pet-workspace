# Proposal

## Why

当前桌宠窗口虽然已配置为透明且无原生装饰，但拖拽标记只作用于外层容器，用户在宠物卡片及其子元素上按下时无法移动窗口。需要把桌宠窗口的拖拽与无边框表现明确为可验收的桌面窗口能力，并避免依赖多余的手动窗口 API 调用。

## What Changes

- 让桌宠窗口内所有非交互内容区域都可通过主鼠标键拖动窗口，包括宠物卡片、图形和文字。
- 保持 Tauri 主窗口无原生标题栏、无系统窗口边框、透明且无原生阴影。
- 保留现有白色圆角宠物卡片的 CSS 视觉，不把它视为系统窗口装饰。
- 使用 Tauri 原生深层拖拽区域能力，避免维护手写鼠标事件，同时保留执行原生拖拽命令所需的最小窗口权限。
- 使用现有构建与配置校验检查改动，并通过 macOS 实机启动验证最终窗口行为。

## Capabilities

### New Capabilities

- `desktop-pet-window`: 定义桌宠主窗口的可拖拽区域、无原生装饰、透明背景及视觉边界行为。

### Modified Capabilities

无。

## Impact

- 业务代码仓库：`repositories/coding-pet/`。
- 主要涉及 React 窗口根组件、Tauri capability 最小权限及 `tauri.conf.json` 的回归验证。
- 不改变 Codex Hook、事件协议、任务状态、通知链路或宠物卡片视觉设计。
- 当前工作树中手写 `startDragging()` 与 `allow-start-dragging` 的未提交尝试与本变更范围重叠；实施时用深层拖拽区域替代手写调用，但保留深层拖拽运行时同样需要的 `allow-start-dragging` 权限。
