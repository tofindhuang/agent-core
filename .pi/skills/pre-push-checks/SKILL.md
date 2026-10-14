---
name: pre-push-checks
description: Push 前按 outgoing diff 选最小覆盖面跑检查——命中哪个模块跑哪样，绝不反射性跑全量。当 agent 准备 push 或提 PR 前使用。
---

# Push 前最小覆盖面

push 前只回答一个问题：**本次 outgoing diff（`origin/main...HEAD`）碰了哪个模块？** 只跑命中项；全量是 CI 的事。

| outgoing 命中 | 本地必跑 | 说明 |
|---|---|---|
| `gateway/**` | `cd gateway && npm run typecheck` | 秒级 |
| `gateway/**` 且改了会话池/工具/prompt | 追加手动冒烟：起 `npm run dev`，`curl` 建会话 + 一条 prompt，确认 SSE 流到 `prompt_settled`（复用 README「已验证」的步骤） | 需本机模型凭证 |
| `server/**` | `cd server && mvn -q compile` | 需 JDK 21；本机无 JDK 时如实标注「由 CI 兜底」，不得声称通过 |
| `server/**` 且有测试 | `mvn -q test -Dtest=<改动相关的测试类>` 单文件跑，全量推 CI | |
| `web/**` | `cd web && npm run typecheck` | 秒级 |
| `web/**` 且改了页面逻辑/构建配置 | 追加 `npm run build` | ~1min |
| `sandbox/**` | `docker build` 一次验证 Dockerfile 可构建（可选，CI 兜底） | |
| 仅 `.md` / `docs/` | 无需构建检查 | |
| `.github/workflows/` | 本地无手段，语法自查后靠 CI | |

## 规则

- **最小覆盖面**：只跑命中模块的命中项；「反射性跑全量」（gateway + server + web 一把梭）只在 PR 声称「重构 / 迁移 / 全仓扫改」时才做。
- **真实跑过才能声明通过**——按上表选最小集合，如实报告跑了什么、什么没跑（没跑的写明「由 CI 兜底」）。绝不把「应该能过」说成「已通过」。
- 分支刚 rebase / 合并过 main：outgoing 变了，重算命中再跑，别沿用上次结论。
- 外部依赖变更（任何 `package.json` / `pom.xml` 变动）必须在本地真实 install/compile 过一次再 push；gateway 的 pi 依赖家族有整族锁版本约束（见 `docs/adrs/0001`）。
