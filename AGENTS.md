# AI Coding Desktop Pet Workspace

## 领域说明

本工作区用于开发 AI Coding Desktop Pet（暂称 AI Coding Companion / AI Pet）：一个面向本地开发者的 macOS 桌面宠物，通过 AI 编程平台提供的 Hook、Notify 或事件接口感知任务状态，并用动画和系统通知反馈运行中、完成、等待输入、需要授权和异常等状态。

当前产品边界是本机 AI Coding Agent 的状态呈现与提醒，不负责执行、代理审批或修改 Agent 任务。首个 MVP 只支持 Codex CLI 的 `agent-turn-complete` 通知链路。

### 角色、系统与关键概念

- 主要用户：在 macOS 上使用 AI 编程工具的开发者。
- 上游系统：Codex CLI（MVP）；Claude Code、Cursor、Gemini CLI（后续）。
- 下游能力：React 桌宠 UI、任务面板、macOS 原生通知；SQLite 历史记录属于后续范围。
- Platform Adapter：把各平台事件转换为统一事件协议。
- Task State Manager：维护 Session、Project、Status 与事件状态。
- `agent-turn-complete`：Codex 的一次 Agent 轮次完成，不等同于整个任务或会话结束。

## 工作区结构

- `knowledge/`：可复用的业务、产品与技术知识；查询从 `knowledge/index.md` 开始。
- `.agents/skills/`：仓库级 Agent Skills；每个技能由自己的 `SKILL.md` 声明。
- `repositories/`：`index.md` 保存领域仓库清单；`repositories/<name>/` 中的子仓代码由各自独立 Git 仓库管理，并被根仓库忽略。
- `runtime/`：可删除的生成物、调试输出和临时报告；不得存放长期知识或正式源码。

## 代码仓库

| 名称 | 职责 | 工作区路径 | 来源 | 接入方式 | 状态 |
| --- | --- | --- | --- | --- | --- |
| `coding-pet-workspace` | 工作区规则、知识库、Agent Skills 与仓库清单 | `.` | `/Users/weigeqian/code/coding-pet` | existing checkout | `main`，尚无首个提交；不存放业务源码 |
| `coding-pet` | Tauri 2 + React 桌宠应用、Rust 事件监听与 Codex Hook | `repositories/coding-pet/` | `/Users/weigeqian/code/coding-pet/repositories/coding-pet` | existing checkout | 独立 Git 项目，`main`，尚无首个提交；合成与真实 Codex 完成事件链路均已通过 |

仓库状态必须与 `repositories/index.md` 和实际文件系统一致。未经用户授权，不 clone 仓库、不创建 worktree、不修改外部仓库状态。

### 根仓与子仓边界

- 根 Git 仓库只管理工作区规则、知识库、Agent Skills、仓库清单和其他工作区元数据；根目录不得放置业务源码、依赖或构建产物。
- 所有业务代码仓库统一放在 `repositories/<name>/`，每个子仓必须拥有自己的 `.git` 和独立提交历史。
- 根仓库通过 `.gitignore` 忽略 `repositories/*/`；只跟踪 `repositories/index.md`，不得使用 `git add -f` 把子仓代码加入根仓。
- 对子仓执行 Git 命令时使用 `git -C repositories/<name> ...`，避免误操作根仓。
- 新增、移除或变更子仓来源与状态时，同步更新 `repositories/index.md` 和本文件。
- 开始业务开发前，先从仓库清单选择目标子仓并在该目录内工作；不得因为目标子仓不存在或路径不明确而在根目录创建项目脚手架。

## 知识查询规则

回答领域问题或开始开发前：

1. 先读 `knowledge/index.md`，根据问题选择业务、产品或技术维度。
2. 再读对应维度的 `index.md`，缩小到相关源文档；不要仅依据索引摘要作答。
3. 使用 `rg -n "<关键词>" knowledge/<维度>` 定向搜索并阅读命中文档。
4. 技术问题同时检查相关代码。文档与当前代码或运行证据冲突时，明确差异，并以可执行代码或当前运行证据为更强依据。
5. 回答中引用工作区相对路径，区分已记录事实、基于代码的推断和未知项。
6. 不得编造缺失知识；以“待补充”标记，并说明可用于补充的信息来源。
7. 新增、移动或废弃长期知识时，同步更新相应 `index.md`。

## 开发规则

- MVP 验收目标：Codex CLI 完成一次 Agent 轮次后，桌宠自动切换完成动画并发送 macOS 系统通知。
- MVP 只实现 Codex 完成事件，不提前实现多平台、多会话、SQLite 或完整状态推断。
- Adapter 与核心状态管理、UI 解耦；跨平台事件先转换为统一协议。
- 本地监听只绑定回环地址，限制 payload 大小；Hook 转发失败不得阻塞 Codex。
- 不从完成通知推断 `RUNNING`、`WAITING_USER` 或 `APPROVAL_REQUIRED`。
- 开始 Codex MVP 集成时使用 `.agents/skills/codex-mvp-integration/SKILL.md`，业务代码目标为 `repositories/coding-pet/`。
- 长期事实写入 `knowledge/`；过程日志和一次性产物写入 `runtime/`。

