# 领域概览

## 定位

AI Coding Desktop Pet 是运行在 macOS 桌面的 AI Coding Agent 状态伴侣。它把 Codex、Claude Code、Cursor、Gemini CLI 等工具的任务事件转换成直观的桌宠动画和本机通知，降低开发者频繁切回终端检查状态的成本。

## 用户与需求

- 目标用户：同时使用一个或多个 AI 编程工具的本地开发者。
- 核心需求：知道 Agent 是否仍在工作、是否完成、是否在等输入、是否需要授权、是否异常。
- 长期价值：从单一桌面提醒工具演进为多项目、多 Agent 的 AI Coding Agent Control Center。

## 边界

- 产品读取或接收 Agent 状态，不替用户执行审批或输入。
- 优先使用官方 Hook、Notify 或事件能力，不依赖屏幕识别和模拟点击。
- 首个 MVP 只验证 Codex 单轮完成事件，不承诺完整判断任务生命周期。

## 上下游

- 上游：AI Coding 平台发出的 Hook、Notify 或事件。
- 核心：平台 Adapter、任务状态管理器。
- 下游：桌宠动画、任务面板、macOS 系统通知；历史统计属于后续。

## 待补充

- 商业模式、开源策略、许可证和发布渠道：由项目负责人确认。
- 产品正式名称与品牌视觉：由项目负责人确认。

