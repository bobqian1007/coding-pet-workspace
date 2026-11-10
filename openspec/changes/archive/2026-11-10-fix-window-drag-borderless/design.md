# Design

## Context

参见 `proposal.md` 的动机。当前 React 根节点使用普通 `data-tauri-drag-region`，该标记只命中事件目标本身；实际可见内容由其后代 `.pet-card`、宠物图形和文字组成，因此用户在主要内容上无法拖动。

Tauri 主窗口已在 `tauri.conf.json` 中配置 `decorations: false`、`transparent: true` 和 `shadow: false`，页面根元素也使用透明背景。项目当前使用 `@tauri-apps/api` 和 CLI 2.12.4，已具备 Tauri 2.11 引入的深层拖拽区域能力。业务子仓现有两处未提交尝试：React 手写 `startDragging()` 调用，以及对应的 `allow-start-dragging` capability 权限。实机启动证明深层拖拽区域内部仍调用同一原生命令，因此该权限是声明式方案的运行前提，而不只是手写 API 的权限。

## Goals / Non-Goals

**Goals:**

- 用最少代码让根拖拽区域覆盖所有非交互后代。
- 保持无原生装饰和透明画布配置，并明确区分原生窗口装饰与应用 CSS 卡片。
- 清理由本问题引入但不再需要的手动窗口 API 代码，同时保留原生拖拽命令所需的最小权限。
- 使用现有构建、Rust 测试和 macOS 实机检查完成验证。

**Non-Goals:**

- 不移除或重设计白色圆角宠物卡片。
- 不隐藏 macOS 屏幕顶部的全局菜单栏或 Dock。
- 不改变 Always on Top、窗口尺寸、状态动画、Codex Hook 或通知行为。
- 不为这一处声明式属性引入新的前端测试框架。

## Decisions

### 1. 在窗口根节点使用深层拖拽区域

将现有根节点拖拽标记设为 Tauri 的深层模式，使非交互后代自动继承拖拽行为，并在主窗口 capability 中保留 `core:window:allow-start-dragging`。未来若加入按钮、输入框等控件，应在控件局部显式退出拖拽区域，以满足 `desktop-pet-window` spec 的交互控件要求。

备选方案：

- 手写 `onMouseDown` 并调用 `startDragging()`：可以工作，但会额外引入窗口 API import、异步调用和错误处理；两种方案都需要同一权限，因此声明式深层拖拽仍是更小实现。
- 给每个卡片后代分别添加普通拖拽标记：容易遗漏新增节点，也会把结构细节散布到多个组件。

### 2. 无边框继续由静态 Tauri 窗口配置负责

保留现有 `decorations: false`、`transparent: true`、`shadow: false`，不在 React 启动时再次调用窗口 setter。静态配置在创建窗口前生效，避免首帧闪烁，也无需增加运行时窗口修改权限。

备选方案是在启动后调用 `setDecorations(false)` 和相关 setter；它重复已有配置，增加权限和时序风险，因此不采用。

### 3. 保留应用自身的卡片边框

`.pet-card` 的圆角、半透明背景、1px 边框和阴影属于内容设计，不属于 macOS 原生窗口装饰。本变更不修改 `Pet.css`；验收时分别检查原生标题栏/控制按钮与卡片视觉，避免把两者混为一谈。

### 4. 验证分为自动构建检查与 macOS 行为检查

运行现有前端构建以校验 JSX/TypeScript 和 Tauri 属性写法，运行 Rust 测试以防止业务链路回归。窗口拖动、透明合成和原生装饰属于 WebView 与 macOS 窗口管理器的集成行为，最终通过重新启动 Tauri 应用后进行实机检查，不引入脆弱的源码文本断言。

## Risks / Trade-offs

- [深层拖拽可能吞掉未来新增控件的操作] -> 新增交互控件时在局部显式退出拖拽区域，并按 spec 验证控件行为。
- [开发服务器热更新不能可靠重建 Tauri capability 或窗口配置] -> 验收前完全退出旧应用和 `tauri dev` 进程，再从业务子仓重新启动。
- [当前未提交尝试可能包含用户仍想保留的意图] -> 实施时只移除本问题新增的 `startDragging()` import、处理函数和事件绑定，保留经实机证明必需的权限，不改动其他用户内容。
- [缺少拖拽权限时深层区域静默失效并产生前端拒绝日志] -> capability 明确保留 `core:window:allow-start-dragging`，并把无权限错误检查纳入实机验收。
- [自动构建无法证明 macOS 原生拖拽行为] -> 将实机拖拽与原生装饰检查列为完成条件。

## Migration Plan

1. 在保留其他未提交改动的前提下，把根节点改为深层拖拽区域。
2. 移除因手写拖拽方案新增且不再使用的窗口 API 代码，保留 `allow-start-dragging` 权限。
3. 确认现有无边框、透明和无阴影配置未被改变。
4. 运行现有自动检查，完全重启桌宠后完成 macOS 实机验收。

如需回滚，仅恢复普通拖拽标记；窗口外观配置和业务事件链路无需迁移。
