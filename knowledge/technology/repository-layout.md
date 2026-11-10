# Git 仓库边界

## 根仓库

`/Users/weigeqian/code/coding-pet` 是工作区根 Git 项目，默认分支为 `main`。根仓库只管理工作区规则、知识库、Agent Skills、仓库清单和其他工作区元数据。业务源码、业务依赖与业务构建产物不得放在根目录。

## 子仓库

- 所有业务代码仓库统一放在 `repositories/<name>/`；当前桌宠应用位于 `repositories/coding-pet/`，来源为 `git@github.com:bobqian1007/coding-pet.git`。
- 每个子仓都是独立 Git 项目，必须拥有自己的 `.git`、分支、远程地址和提交历史。
- 根仓库的 `.gitignore` 使用 `repositories/*/` 忽略所有子仓目录，只版本化 `repositories/index.md`。
- 不使用 `git add -f` 绕过该规则，也不把子仓作为普通目录提交进根仓。
- 子仓的安装依赖、构建产物和本地配置由该子仓自己的 `.gitignore` 管理。
- 如果目标子仓尚未接入或路径不明确，应先确认仓库清单和接入方式，不得在根目录创建替代脚手架。

## 操作约定

- 根仓操作：在工作区根目录执行 `git ...`。
- 子仓操作：执行 `git -C repositories/<name> ...`，提交和推送均在子仓中独立完成。
- Node/Tauri 前端命令在 `repositories/coding-pet/` 执行；Rust/Cargo 命令在 `repositories/coding-pet/src-tauri/` 执行，使 Rustup 能读取上级目录中的 `rust-toolchain.toml`。不要从工作区根目录仅靠 `--manifest-path` 运行，否则可能误用系统默认工具链。
- 增加子仓前，先确认仓库名称、职责、来源 URL 或本地路径，以及 clone、worktree 或现有 checkout 的接入方式。
- 接入状态发生变化后，同步更新 `repositories/index.md` 和根目录 `AGENTS.md`。

## Worktree 布局与开发流程

- 所有开发 worktree 的统一根目录为 `~/code/worktrees/`。
- 一个需求对应一个完整工作区，根仓 worktree 路径为 `~/code/worktrees/<change-id>/coding-pet/`。
- 控制 worktree 使用 `codex/<change-id>` 分支，不以 detached HEAD 协调变更。
- 开发顺序固定为：创建根仓 worktree，使用 OpenSpec 完成需求评审、跨仓代码分析和技术设计，评审受影响子仓集合，再在该工作区的 `repositories/<子仓名>/` 创建受影响子仓的 worktree 和开发分支。
- 子仓 worktree 嵌入对应需求工作区后，仍由子仓自己的 Git 仓库管理；根仓继续通过 `.gitignore` 忽略其内容。
- Codex 默认的 `~/.codex/worktrees/` 只可作为临时创建位置；若工具先在该目录生成 worktree，应迁移到 `~/code/worktrees/` 后再开始开发。
- 具体变更的仓库、基线、分支、路径、状态和证据记录在对应 OpenSpec change 中；完整门禁见 [多仓变更流程](multi-repo-change-workflow.md)。

新开发者克隆工作区后，可在工作区根目录执行：

```bash
git clone git@github.com:bobqian1007/coding-pet.git repositories/coding-pet
```

