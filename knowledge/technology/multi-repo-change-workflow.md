# 多仓变更流程

## 目的与角色

`coding-pet-workspace` 是跨仓变更的控制仓，负责 `AGENTS.md`、知识库、OpenSpec 规格与变更历史、仓库清单和跨仓状态。`repositories/` 下的子仓包含可运行的产品代码，各自维护独立 Git 历史。

控制仓和子仓的主检出只作为稳定基线、只读分析来源和 worktree origin。需求实现必须在专用 worktree 中完成。

## 固定流程

1. 从控制仓默认分支解析并记录精确 base commit，创建 `codex/<change-id>` 分支和 `~/code/worktrees/<change-id>/coding-pet/` 控制 worktree。
2. 在控制 worktree 中运行 OpenSpec 工作流，完成需求评审、子仓代码检查、影响分析、proposal、spec、design、验收条件和 tasks。此阶段只读检查子仓主检出，不创建子仓开发分支。
3. 由技术设计列出受影响与仅检查的仓库，并评审受影响仓库集合。未通过该门禁，不创建子仓 worktree。
4. 仅为已确认受影响的子仓解析精确 base commit，创建变更分支和 worktree，并在 OpenSpec design 或其链接文档中更新变更映射。
5. 在子仓 worktree 中实施和验证。新增受影响仓库时，先更新设计并重新评审仓库集合。跨仓验证按需覆盖接口兼容、事件或数据契约、发布顺序和各仓测试。
6. 在控制 worktree 中记录子仓 commit 或 PR、验证证据与集成状态。所需实现和跨仓验证完成后，才归档 OpenSpec change；延后项必须显式记录。

## 变更映射

每个涉及子仓的 OpenSpec change 至少维护以下字段：

| Repository | Role in change | Base revision | Change branch | Worktree path | Status | Evidence |
| --- | --- | --- | --- | --- | --- | --- |
| `<repository>` | `<changed / inspected-only 及职责>` | `<full commit>` | `<branch or n/a>` | `<path or n/a>` | `<planned / active / verified / integrated / deferred>` | `<tests, commit, PR or review>` |

映射必须同时标识 OpenSpec change 和控制分支。`repositories/index.md` 只保存稳定仓库清单，不替代变更级映射。

## 安全与收尾

- 不为所有子仓预建 worktree，不直接在主检出实现需求，不复用非空目标目录或未确认用途的现有分支。
- 创建 worktree 前确认 source checkout、目标路径、分支或 commit；仓库范围变化时先更新 OpenSpec 设计。
- 控制 worktree 是跨仓状态和归档决策的事实来源。文档、代码或运行证据冲突时，记录差异并以当前可执行代码或运行证据为更强依据。
- 删除、移动或 prune worktree 必须单独取得授权，先检查控制仓和所有子仓 worktree 均无未提交改动；不得隐式删除分支。
