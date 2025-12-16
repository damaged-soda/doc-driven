# <主题> 技术变更计划（Plan）

## 0. 背景与目标（简短即可）
- 背景：TODO（为什么要改/当前痛点）
- 目标：TODO（改完要达到什么效果）
- 非目标：TODO（明确不做什么，避免范围漂移）

## 1. 影响范围（必须）
- 影响的 repo（来自 docmap，可多项）：TODO
- 影响的模块/目录/文件（按 repo 分组列出即可）：TODO
- 外部可见变化（如适用：API/CLI/配置/数据格式）：TODO

## 2. 方案与改动点（必须）
按 repo 分组写清楚“要改什么”，不写部署流程：

- repo: <repo-name-1>
  - 改动点：TODO
  - 新增/修改的接口或数据结构：TODO（如无写“无”）
  - 关键逻辑说明：TODO
- repo: <repo-name-2>（如有）
  - 改动点：TODO
  - 新增/修改的接口或数据结构：TODO
  - 关键逻辑说明：TODO

## 3. 自测与验收口径（必须，可执行）
- 本地自测步骤（命令/操作）：TODO
- 关键用例清单：TODO
- 通过标准：TODO（例如“某接口返回值满足…/某日志出现…/某测试全绿…”）

## 4. SOT 更新清单（必须）
实现完成后要把“最终事实”沉淀到 SOT（至少文件级，V1 不要求精确到小节）：

- docs/sot/overview.md：TODO（更新哪些事实）
- docs/sot/architecture.md：TODO（更新哪些事实）
- 其他：TODO（如无则删）

## 5. 完成后归档动作（固定）
实现完成并完成自测后：
1) 按第 4 节更新 SOT
2) 将整个目录从 docs/wip/YYYYMMDD-topic/ 移动到 docs/archive/YYYYMMDD-topic/
