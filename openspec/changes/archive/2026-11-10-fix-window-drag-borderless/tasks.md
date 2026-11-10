# Tasks

## 1. 深层拖拽实现

- [x] 1.1 在保留 `src/App.tsx` 其他用户改动的前提下，将窗口根节点改为深层拖拽区域，并移除本问题新增的 `getCurrentWindow` import、`startDragging()` 处理函数和 `onMouseDown` 绑定；通过 `npm run build` 验证 JSX 与 TypeScript 构建成功，并检查生成页面仍包含深层拖拽标记。
- [x] 1.2 在 `src-tauri/capabilities/default.json` 保留深层拖拽运行所需的 `core:window:allow-start-dragging` 最小权限及原有其他权限；通过 Tauri 配置生成或 Rust 构建验证 capability 可解析，并在实机启动日志中确认不再出现 `window.start_dragging not allowed`。
- [x] 1.3 核对 `tauri.conf.json` 仍保持 `decorations: false`、`transparent: true`、`shadow: false` 和 `alwaysOnTop: true`，并运行 `cargo test` 验证现有 Rust 事件与状态测试全部通过。

## 2. macOS 集成验收

- [x] 2.1 完全退出旧 Coding Pet 和 `tauri dev` 进程后，从 `repositories/coding-pet/` 重新启动应用；分别从宠物卡片背景、宠物图形和状态文字按住主鼠标键拖动，验证窗口均随指针移动。
- [x] 2.2 在重新启动的应用中验证没有 macOS 原生标题栏、红黄绿窗口按钮、系统窗口边框或原生阴影，同时卡片外区域透明且现有白色圆角卡片视觉保持不变。
- [x] 2.3 切换其他应用并观察 Coding Pet，验证 Always on Top 行为、状态动画和 Codex 完成事件链路没有因窗口改动发生回归。
