---
name: codex-mvp-integration
description: Implement or verify the coding-pet Codex MVP path from Codex notify through the localhost Rust listener and Tauri state event to the React completion animation and macOS notification. Use for Codex adapter, hook, event-listener, task-state, completion UI, or MVP end-to-end work in this repository.
---

# Codex MVP Integration

Implement the smallest verified path for the current MVP. Do not expand the task into multi-platform support or a complete task-lifecycle detector.

## Read first

1. Read `knowledge/product/codex-mvp.md` for scope and acceptance criteria.
2. Read `knowledge/product/task-states.md` for status semantics.
3. Read `knowledge/technology/architecture.md` and `knowledge/technology/event-protocol.md` for boundaries and protocol constraints.
4. Treat `repositories/coding-pet/` as the application repository. Verify it exists and has its own `.git`, then inspect its current code before selecting commands or file paths.

## Required inputs

- The requested slice: UI, hook, listener, Adapter, state manager, notification, or end-to-end.
- A real or explicitly synthetic Codex notify payload sample when implementing field mapping.
- Current Tauri/Rust/Node versions from the repository once initialized.

If a payload or version is unknown, mark it `待补充` or use a named test fixture assumption. Do not present an inferred Codex schema as fact.

## Procedure

1. Translate the request into a narrow, testable acceptance condition.
2. Run Git and Node commands inside `repositories/coding-pet/`. Run Cargo commands inside `repositories/coding-pet/src-tauri/` so Rustup resolves the repository's `rust-toolchain.toml`; never create application code in the workspace root.
3. Inspect relevant files and existing tests; preserve current structure and style.
4. For a bug or parser change, add the smallest failing test or fixture first.
5. Keep Codex-specific parsing inside the Adapter or Hook boundary.
6. Keep the state manager and React UI dependent only on the unified event/status model.
7. Bind listeners to loopback, enforce a payload limit, and make Hook forwarding failures non-blocking for Codex.
8. Run focused tests, then the smallest available project-wide check covering the changed layer.
9. For end-to-end completion, verify the real chain: Codex notify -> Hook -> listener -> state -> Tauri event -> animation and macOS notification.

## Safety and scope boundaries

- Do not edit the user's global `~/.codex/config.toml` without explicit authorization; provide the exact snippet and absolute Hook path for review.
- Do not infer `RUNNING`, `WAITING_USER`, or `APPROVAL_REQUIRED` from `agent-turn-complete`.
- Do not add SQLite, queues, multi-agent dashboards, or non-Codex Adapters to satisfy the MVP.
- Do not expose the listener beyond loopback or log sensitive prompt/code payloads by default.
- Do not let notification delivery failure turn a completed Codex task into an application crash.

## Output and verification report

Report:

- The exact slice implemented and files changed.
- Tests/checks run and their results.
- Whether verification used a synthetic payload or a real Codex CLI event.
- Remaining unknowns, especially payload fields and platform permissions.

The MVP is complete only when a real Codex CLI completion produces both the completion animation and a macOS system notification.

