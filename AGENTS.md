# 文档驱动 AI 编码规则 (V3 - Hybrid OpenSpec, Anti-Fragmentation)

本文件定义“文档驱动 + 事务式变更”的协作协议，用于控制变更质量、避免 SOT 碎片化，并确保实现与规范一致。

---

## 0) 空间与权限 (Space & Permissions)

- **文档空间** (Definition): `./docs/`
- **代码空间** (Implementation): `./src/`  
  - 禁止将业务代码写入 docs，禁止将规范写入 src（除非是代码注释/README 等代码侧文档）。
- **仓库映射** (Single Source of Truth): `./docmap.yaml`  
  - 一切路径/模块定位以 `docmap.yaml` 为准：先读再动，禁止猜测文件位置。

---

## 1) 目录本体论 (Directory Ontology)

- **SOT (真理源)**: `./docs/sot/`  
  - 当前系统的唯一权威描述（仅承载稳定、可复用、对外可依赖的契约）。
- **WIP (工作区)**: `./docs/wip/YYYYMMDD-<topic>/`  
  - 变更提案与实施的临时工作目录。
- **Archive (归档)**: `./docs/archive/YYYYMMDD-<topic>/`  
  - 已完成变更的留痕与审计材料。

### WIP 目录“三位一体” (Required Triad)

每个 WIP 目录必须包含以下文件（缺一不可）：

1. `intent.md`：**意图**。背景、目标、非目标（Why & What）。
2. `spec_delta.md`：**规范增量**。对 SOT 的修改草案（The Contract）。
3. `tasks.md`：**执行清单**。原子化步骤 Checklist（The How）。

---

## 1.1) SOT 内容边界与粒度 (SOT Scope & Granularity)

为解决 `./docs/sot/` 过度碎片化问题，SOT 仅承载“长期稳定、可复用、对未来决策有约束力”的系统契约；任何短期/局部/纯实现细节不进入 SOT。

### 变更分级 (Change Levels)

`spec_delta.md` 中每条变更必须标注以下标签之一：

- **[SOT]**：必须合并进 SOT（归档时会写入 `./docs/sot/`）。满足任一条件即为 [SOT]：
  1) **对外契约变化**：接口/CLI/配置面/数据模型/返回语义/可观测行为变化  
  2) **全局不变量**：安全、幂等、容错、一致性、顺序性、资源边界等必须始终满足的约束  
  3) **跨模块/跨目录协作协议**：docs/src 边界、docmap 约束、事务闸门、调用约定  
  4) **稳定且长期保留的约定**：命名/目录本体论/核心抽象层定义（能被后续多人复用）

- **[ARCHIVE-ONLY]**：不合并进 SOT，仅在归档中留痕（归档时不会写入 SOT）：
  1) **纯重构/改名/格式与文案调整**（不改变对外行为）  
  2) **一次性或短期策略**（临时参数、实验性开关、某次任务局部约束）  
  3) **局部实现细节**（不构成跨模块契约，未来可随实现演进）

- **[CANDIDATE]**：候选规范。默认不进 SOT；至少经过 1–2 次 WIP 验证后再考虑提升为 [SOT]。

### Spec Delta 标注与默认规则 (Mandatory Tagging)

- `spec_delta.md` 中**每条**变更必须带标签前缀：`[SOT]` / `[ARCHIVE-ONLY]` / `[CANDIDATE]`。
- **未标注的条目一律视为 `[ARCHIVE-ONLY]`**（防止碎片规则无意进入 SOT）。
- 若一项变更同时包含“契约变化 + 实现细节”，必须拆成两条：  
  - 契约部分标 `[SOT]`  
  - 实现细节标 `[ARCHIVE-ONLY]`

### 去碎片化约束 (Anti-Fragmentation Constraints)

- SOT 合并时必须**并入既有章节**，禁止为了单条微规则新增小节。
- 同一主题若新增超过 3 条规则，必须压缩为“**1 条总规则 + 2–3 条子规则**”，避免条目爆炸。
- 任何“偏好/习惯/一次性补丁”默认进入 Archive-only，不上升为 SOT。

---

## 2) 状态锚点 (State Anchor)

**【强制执行】** 每次回复的**第一段**必须输出以下锚点块，不得省略：

> ⚓ **锚点**: <当前 WIP 路径 | "无">  
> 🚦 **阶段**: PROPOSAL | IMPLEMENT | VERIFY | ARCHIVE  
> 📝 **下一步**: <一句话说明>

---

## 3) 启动序列 (Boot Sequence)

当会话启动时，按顺序执行：

1. 读取 `./docs/README.md` 与 `./docs/sot/index.md`。
2. 读取 `./docmap.yaml`，检查 `./src/` 下各仓库是否存在且路径可达。
3. 扫描 `./docs/wip/`：  
   - 若存在活跃目录，读取其 `tasks.md` 与 `spec_delta.md` 恢复上下文。  
   - 若多个活跃目录并存，优先选择最近日期目录作为锚点，并在锚点块中明确说明。
4. 若无活跃任务，进入待命状态。

---

## 4) 事务性工作流 (Transactional Workflow)

### 阶段一：提案 (PROPOSAL)

当用户提出变更请求时：

1. 创建目录 `./docs/wip/YYYYMMDD-<topic>/`。
2. 生成 `intent.md`：  
   - 背景与动机（Why）  
   - 目标（Goals）与非目标（Non-Goals）  
   - 验收标准（Acceptance Criteria）
3. 生成 `spec_delta.md`（OpenSpec 风格）：  
   - `## ADDED Requirements`  
   - `## MODIFIED Requirements`  
   - `## REMOVED Requirements`（如适用）  
   - **关键**：此阶段不修改 SOT；只描述“如果不执行代码，SOT 应该如何变化”。  
   - **强制**：每条 Requirement 必须标注 `[SOT]` / `[ARCHIVE-ONLY]` / `[CANDIDATE]`。
4. 生成 `tasks.md`：  
   - 依据 `spec_delta.md` 拆解为原子步骤（每步可单独提交/回滚/验证）。  
   - 每条任务用 `- [ ]` checklist 表达。
5. **闸门（必须）**：请求用户审核 `spec_delta.md`。  
   - **未经确认，不得写一行业务代码**。  
   - 若用户要求“直接改代码”，也必须先产出最小可用 spec_delta 并请求确认。

> 规范粒度提示：  
> - [SOT] 仅写“必须长期稳定”的契约句子；  
> - 任何实现选择、局部重构、代码风格微调，一律写为 [ARCHIVE-ONLY] 或 [CANDIDATE]。

---

### 阶段二：实施 (IMPLEMENT)

仅在提案获批后进入：

1. 读取 `tasks.md`，逐条执行。
2. **原子化写入**：每次修改代码后，立即在 `tasks.md` 中打钩 `[x]` 并补充必要的简短备注（改了什么、为什么、影响面）。
3. **规范对齐**：实现过程中必须反复查阅 `spec_delta.md`，确保行为与 `[SOT]` 条目一致。
4. 若实现中发现 spec 与现实冲突：  
   - **先更新 `spec_delta.md`**（并重新闸门确认，如涉及 [SOT]），再改代码。

---

### 阶段三：验证 (VERIFY)

1. 执行自测（单测/集成测试/手工验证均可，视项目而定）。
2. 输出**交付摘要**（必须包含）：  
   - 修改文件清单  
   - 自测结果与证据（命令/日志/截图路径等）  
   - 对照 `intent.md` 的验收结论（逐条对应）
3. **闸门（必须）**：询问用户“是否验收通过并归档？”

---

### 阶段四：归档 (ARCHIVE)

仅在用户明确输入“归档/Archive”后执行：

1. **SOT 合并 (Critical)**  
   - 读取 `spec_delta.md`。  
   - **仅将 `[SOT]` 标记的增量内容**精确应用到 `./docs/sot/` 的对应文件中。  
   - `[ARCHIVE-ONLY]` 与 `[CANDIDATE]` 条目仅保留在归档目录，作为留痕与复盘材料，不进入 SOT。  
   - 合并前必须先输出 **“SOT 变更预览（仅含 [SOT] 条目）”**，用户确认后再写入。  
   - **去碎片化约束**：  
     - 合并必须并入既有章节，禁止为单条微规则新建小节；  
     - 同主题新增超过 3 条时，必须压缩为“总规则 + 子规则”。

2. **清理**：确保 `tasks.md` 全部勾选完成（或明确记录未完成原因）。
3. **移动**：将 `./docs/wip/<dir>` 移动到 `./docs/archive/<dir>`。

---

## 5) 安全守则 (Safety Protocol)

1. **禁止盲写**：不知晓 `docmap.yaml` 定义的路径前，不得猜测文件位置或擅自创建结构。
2. **SOT 神圣性**：在 PROPOSAL 和 IMPLEMENT 阶段，严禁修改 `./docs/sot/` 下任何文件。  
   - SOT 更新仅允许发生在 ARCHIVE 阶段，并遵守“仅合并 [SOT]”规则。
3. **双重检查**：若用户指令与 `spec_delta.md` 冲突，必须先更新 Spec，再改代码。
4. **最小不可逆原则**：任何不可逆或高风险变更（数据迁移、删除、权限、安全）必须在 spec_delta 中单独列出 `[SOT]` 条目，并在 VERIFY 中提供明确验证证据。
5. **默认保守**：不确定是否应进入 SOT 的规则，默认标注 `[ARCHIVE-ONLY]` 或 `[CANDIDATE]`，避免污染 SOT。

---