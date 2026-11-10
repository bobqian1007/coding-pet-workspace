# 代码仓库索引

| 名称 | 职责 | 工作区路径 | 来源 | 接入方式 | 状态 |
| --- | --- | --- | --- | --- | --- |
| `coding-pet-workspace` | 工作区规则、知识库、Agent Skills 与仓库清单 | `.` | `git@github.com:bobqian1007/coding-pet-workspace.git` | existing checkout | `main` 跟踪 `origin/main`；不存放业务源码 |
| `coding-pet` | Tauri 2 + React 桌宠应用、Rust 状态服务与 Codex Hook | `repositories/coding-pet/` | `git@github.com:bobqian1007/coding-pet.git` | existing checkout | 独立 Git 项目，`main` 跟踪 `origin/main`；发布包视觉效果待人工验收 |

## 接入约定

- 当前目录是根 Git 仓库和领域工作区；默认分支为 `main`，不得存放业务源码、业务依赖或业务构建产物。
- 业务仓库统一位于 `repositories/<name>/`，每个目录都是具有独立 `.git` 和提交历史的 Git 项目。
- 根仓库只跟踪本清单，通过 `.gitignore` 忽略 `repositories/*/` 下的全部子仓文件；不得强制把子仓文件加入根仓。
- 对子仓执行 Git 操作时使用 `git -C repositories/<name> ...`，并分别检查、提交和推送。
- clone 或创建 worktree 前必须确认来源、目标路径和分支/提交并获得用户授权。
- 目标子仓不存在或来源不明确时应停止并确认，不得退回根目录创建业务项目。
- 状态变化后同步更新本文件和根目录 `AGENTS.md`。

## 新工作区接入

克隆主工作区后，在工作区根目录接入桌宠子仓：

```bash
git clone git@github.com:bobqian1007/coding-pet.git repositories/coding-pet
```

子仓由自己的 Git 仓库管理，根仓不会跟踪其文件。

