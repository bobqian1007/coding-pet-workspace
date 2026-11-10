# 代码仓库索引

| 名称 | 职责 | 主检出路径 | 来源 / worktree origin | 默认分支 | 接入方式 | 状态与未决项 |
| --- | --- | --- | --- | --- | --- | --- |
| `coding-pet-workspace` | 工作区规则、知识库、OpenSpec、Agent Skills 与跨仓变更协调 | `.` | `git@github.com:bobqian1007/coding-pet-workspace.git` | `main` | existing checkout | `main` 跟踪 `origin/main`；控制分支使用 `codex/<change-id>`；不存放业务源码 |
| `coding-pet` | Tauri 2 + React 桌宠应用、Rust 状态服务与 Codex Hook | `repositories/coding-pet/` | 本地 `repositories/coding-pet/`；远程 `git@github.com:bobqian1007/coding-pet.git` | `main` | existing checkout | `main` 跟踪 `origin/main`；开发分支命名约定待补充；合成与真实 Codex 完成事件链路均已通过；发布包视觉效果待人工验收 |

## 接入约定

- 当前目录是根 Git 仓库和领域工作区；默认分支为 `main`，不得存放业务源码、业务依赖或业务构建产物。
- 业务仓库统一位于 `repositories/<name>/`，每个目录都是具有独立 `.git` 和提交历史的 Git 项目。
- 根仓库只跟踪本清单，通过 `.gitignore` 忽略 `repositories/*/` 下的全部子仓文件；不得强制把子仓文件加入根仓。
- 对子仓执行 Git 操作时使用 `git -C repositories/<name> ...`，并分别检查、提交和推送。
- clone 或创建 worktree 前必须确认来源、目标路径和分支/提交并获得用户授权。
- 目标子仓不存在或来源不明确时应停止并确认，不得退回根目录创建业务项目。
- 状态变化后同步更新本文件和根目录 `AGENTS.md`。
- 本清单只记录稳定仓库事实；具体变更的 base revision、分支、worktree、状态和证据记录在对应 OpenSpec change 的设计或链接文档中。

## Worktree 与分支约定

- 控制仓 worktree 根目录为 `~/code/worktrees/`，路径为 `~/code/worktrees/<change-id>/coding-pet/`，分支使用 `codex/<change-id>`。
- 子仓 worktree 仅在 OpenSpec 设计确认其受影响后创建，优先放在控制 worktree 的 `repositories/<name>/`。
- `coding-pet` 子仓开发分支的统一命名约定待项目负责人确认；在此之前，应在创建 worktree 前明确记录所选分支。
- 完整门禁、变更映射和清理规则见 `knowledge/technology/multi-repo-change-workflow.md`。

## 新工作区接入

克隆主工作区后，在工作区根目录接入桌宠子仓：

```bash
git clone git@github.com:bobqian1007/coding-pet.git repositories/coding-pet
```

子仓由自己的 Git 仓库管理，根仓不会跟踪其文件。

