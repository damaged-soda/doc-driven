# 文档驱动 AI 编码规则（V1）

## 0）空间分离（必须遵守）
- 文档（定义空间）：./docs/
- 代码（实现空间）：./src/（每个 ./src/<repo> 可为独立 Git 仓库；禁止执行任何 git 命令）
- 仓库映射（单一事实源）：./docmap.yaml（先读它再行动，不得猜 repo 路径）

## 1）文档结构（目录即状态）
- SOT（当前事实，唯一权威）：./docs/sot/
- WIP（进行中变更）：./docs/wip/YYYYMMDD-topic/
  - 每个 WIP 需求目录必须包含：plan.md
- Archive（已完成归档）：./docs/archive/YYYYMMDD-topic/
  - 通过“完成后移动整个 WIP 目录”产生
- 模板：./docs/_templates/plan.md

## 2）会话启动默认动作（尽量静默）
当在项目根目录启动 Codex 时，按顺序执行：
1) 读取 ./docs/README.md
2) 读取 ./docs/sot/index.md 及其列出的 SOT 文件
3) 读取 ./docmap.yaml；列出可用 repo，并检查 ./src/<repo> 目录是否存在
   - 若 repo 目录不存在：提示用户自行 clone 到 ./src/；不得执行 git
4) 扫描 ./docs/wip/，列出当前进行中的需求目录（如有）

## 3）任何“变更请求”的强制流程
当用户提出任何会改变行为/代码的请求时，必须按顺序执行：
1) 创建需求目录：./docs/wip/YYYYMMDD-topic/
2) 基于 ./docs/_templates/plan.md 创建并填写：./docs/wip/YYYYMMDD-topic/plan.md
3) 请求用户审核并确认 plan.md
4) 仅在用户确认后，才允许在目标 repo 内实现（写入只发生在 ./src/<repo>/）
5) 实现完成并完成基本自测后，必须自动整理：
   - 按 plan.md 的 “SOT 更新清单” 更新 ./docs/sot/*（反映当前真实实现）
   - 将整个需求目录从 ./docs/wip/YYYYMMDD-topic/ 移动到 ./docs/archive/YYYYMMDD-topic/

## 4）plan.md 最低要求（不可省略）
plan.md 必须包含：
- 简短的背景/目标/非目标
- 影响范围：影响的 repo（来自 docmap）+ 影响模块/文件（按 repo 分组）
- 方案与改动点（按 repo 分组）
- 自测与验收口径（可执行）
- SOT 更新清单（至少文件级）

## 5）写入与安全闸门
- 写入/移动文件前，先用简短列表总结计划（路径 + 意图），并征求用户确认
- 严禁把代码写入 ./docs/；严禁把文档写入 ./src/
- V1 默认不引入/维护部署、上线、CI/CD、runbook 等文档（除非用户明确要求）
- 禁止执行任何 git 操作（clone/init/commit/push 等均不做）
