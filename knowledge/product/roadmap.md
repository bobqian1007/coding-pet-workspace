# 产品路线图

## 阶段 0：方案确定

Tauri 2 + React + TypeScript + Rust，Codex 优先；已完成方向确认。

## 阶段 1：桌宠基础 UI

创建 Tauri 项目，实现透明窗口、Always on Top、拖动和基础状态动画。

## 阶段 2：Codex 完成事件集成

实现 `ai-pet-hook`、本机 HTTP 监听、Rust 状态更新、React 事件订阅与系统通知。

## 阶段 3：交互与任务管理

在有可靠事件来源后补充 `RUNNING`、`WAITING_USER`、`APPROVAL_REQUIRED`，并支持多会话、项目名称和任务弹窗。

## 阶段 4：扩展平台

增加 Claude Code、Cursor、Gemini CLI Adapter；按实际需求引入 SQLite 历史记录和跨平台支持。

