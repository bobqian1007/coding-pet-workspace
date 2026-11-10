# Git 仓库边界

## 根仓库

`/Users/weigeqian/code/coding-pet` 是工作区根 Git 项目，默认分支为 `main`。根仓库只管理工作区规则、知识库、Agent Skills、仓库清单和其他工作区元数据。业务源码、业务依赖与业务构建产物不得放在根目录。

## 子仓库

- 所有业务代码仓库统一放在 `repositories/<name>/`；当前桌宠应用位于 `repositories/coding-pet/`。
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

