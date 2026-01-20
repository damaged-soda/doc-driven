# 文档驱动 AI 编码规则 (V2 - Hybrid OpenSpec)

## 0) 空间与权限 (Space & Permissions)

* **文档空间** (Definition): `./docs/`
* **代码空间** (Implementation): `./src/` (禁止将代码写入 docs，反之亦然)
* **仓库映射**: `./docmap.yaml` (单一事实源，先读再动)
* **Git 规则**:
* **严禁** 直接执行任何 git 命令 (clone/commit/push)。

## 1) 目录本体论 (Directory Ontology)

* **SOT (真理源)**: `./docs/sot/` (当前系统的唯一权威描述)
* **WIP (工作区)**: `./docs/wip/YYYYMMDD-<topic>/`
* 每个 WIP 目录**必须**包含以下“三位一体”文件：
1. `intent.md`: **意图**。背景、目标、非目标 (Why & What)。
2. `spec_delta.md`: **规范增量**。对 SOT 的修改草案 (The Contract)。
3. `tasks.md`: **执行清单**。原子化的步骤 Checklists (The How)。


* **Archive (归档)**: `./docs/archive/YYYYMMDD-<topic>/`

## 2) 状态锚点 (State Anchor)

**【强制执行】** 每次回复的**第一段**必须输出以下锚点块，不得省略：

> ⚓ **锚点**: <当前 WIP 路径 | "无">
> 🚦 **阶段**: PROPOSAL | IMPLEMENT | VERIFY | ARCHIVE
> 📝 **下一步**: <一句话说明>

## 3) 启动序列 (Boot Sequence)

当会话启动时，按顺序执行：

1. 读取 `./docs/README.md` 与 `./docs/sot/index.md`。
2. 读取 `./docmap.yaml`，检查 `./src/` 下各仓库是否存在。
3. 扫描 `./docs/wip/`。若存在活跃目录，读取其 `tasks.md` 和 `spec_delta.md` 恢复上下文。
4. 若无活跃任务，进入待命状态。

## 4) 事务性工作流 (Transactional Workflow)

### 阶段一：提案 (PROPOSAL)

当用户提出变更请求时：

1. 创建目录 `./docs/wip/YYYYMMDD-<topic>/`。
2. **生成意图 (`intent.md`)**：描述变更原因、用户故事。
3. **生成规范增量 (`spec_delta.md`)**：
* 模拟 OpenSpec 格式，列出 `## ADDED Requirements`, `## MODIFIED Requirements`。
* **关键**：此时不修改 SOT，只在此文件中描述“如果不执行代码，SOT 应该变成什么样”。


4. **生成任务 (`tasks.md`)**：基于 `spec_delta.md` 拆解代码步骤。
5. **闸门**：请求用户审核。**未经确认，不得写一行业务代码。**

### 阶段二：实施 (IMPLEMENT)

仅在提案获批后进入：

1. 读取 `tasks.md`，逐条执行。
2. **原子化写入**：每次修改代码后，立即在 `tasks.md` 中打钩 `[x]`。
3. **规范对齐**：写代码时必须反复查阅 `spec_delta.md`，确保代码行为与规范一致。

### 阶段三：验证 (VERIFY)

1. 执行自测。
2. 输出**交付摘要**：
* 修改文件清单。
* 自测结果。
* 对照 `intent.md` 的验收结论。


3. **闸门**：询问用户“是否验收通过并归档？”

### 阶段四：归档 (ARCHIVE)

仅在用户明确输入“归档/Archive”后执行：

1. **SOT 合并 (Critical)**：
* 读取 `spec_delta.md`。
* 将增量内容**精确地**应用到 `./docs/sot/` 下的对应文件中。
* *注意*：这是最危险的步骤，必须先输出“SOT 变更预览”，用户确认后再写入。


2. **清理**：将 `tasks.md` 全勾选。
3. **移动**：`mv./docs/wip/<dir>./docs/archive/<dir>`。

## 5) 安全守则 (Safety Protocol)

1. **禁止盲写**：不知晓 `docmap.yaml` 定义的路径前，不得猜测文件位置。
2. **SOT 神圣性**：在 PROPOSAL 和 IMPLEMENT 阶段，**严禁修改** `./docs/sot/` 下的任何文件。SOT 的更新仅允许发生在 ARCHIVE 阶段。
3. **双重检查**：若用户指令与 `spec_delta.md` 冲突，必须先更新 Spec，再改代码。
