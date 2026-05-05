---
layout: default
title: Hermes Kanban 软件开发协作流程设计方案
permalink: /hermes-kanban-software-development-collaboration-design/
---

# Hermes Kanban 软件开发协作流程设计方案

## 0. 文档定位

本文档是当前 Hermes 软件开发看板协作流程的**规范性设计文档**，用于回答以下问题：

1. Hermes 原生的软件开发协作流程应如何分层。
2. `default` / `trading` 与内部开发团队的职责边界应如何划分。
3. `orchestrator / pm / architect / backend-eng / frontend-eng / reviewer / qa` 七类角色应如何协作。
4. 当前 live 模型与工具边界应如何匹配角色，而不是重新塌缩回单角色超人模式。
5. 这套设计与 OpenClaw 的 `main + architect + task hat + ACP 员工池` 思路有什么继承关系与明确差异。

本文档的**首要使用者**是 `orchestrator`。它在做任务分解、依赖设计、角色选择、返工回流和流程闭环时，应以本文档为完整参考。

`pm` 与 `architect` 可以在角色边界不清、任务链设计复杂、或需要核对验收/设计职责时参考本文档；实现型 worker 默认不需要把本文档长期塞进自己的常驻 prompt。

---

## 1. 设计目标与非目标

### 1.1 设计目标

本方案的目标是把软件开发任务从“入口 agent 临时兼任一切”升级为“**Hermes 原生 Kanban 团队协作**”。

具体目标如下：

- **目标 1：入口层与执行层解耦**
  - `default` / `trading` 负责接需求、创建任务、对用户交付。
  - 内部 worker 负责拆解、设计、实现、review、QA。

- **目标 2：让 Hermes Kanban 成为唯一 runtime contract**
  - 运行时只依赖 Hermes 自身的 `assignee / parents / status / summary / metadata / comments`。
  - 不额外再造一层 OpenClaw 风格的帽子状态机。

- **目标 3：把 OpenClaw 的高价值经验迁移进 Hermes 原生机制**
  - 保留 Architect 的技术 owner 精神。
  - 保留 Orchestrator 的“只拆、只派、只收，不抢活”边界。
  - 保留 handoff 的最低完整性要求：做了什么、如何验证、已知问题、下一步。

- **目标 4：防止组织重新塌缩成单点超人模式**
  - `architect` 不再默认承接全部实现。
  - `reviewer` 不再默认自己改代码。
  - `qa` 不再默认充当 PM 或 Reviewer。
  - `orchestrator` 不再默认下场实现。

- **目标 5：让协作流程可观察、可干预、可复盘**
  - 任务链、依赖、返工、block、验收节点都在 Kanban board 上显式呈现。
  - Dashboard 负责观察和人工干预，不负责决策逻辑本身。

### 1.2 非目标

本方案明确不追求以下事项：

- **非目标 1：把 ACP 员工池重新做成主组织抽象**
  - 外部执行器可以是第二层增强，但不是本设计的组织主骨架。

- **非目标 2：让每个 worker 都常驻独立 gateway**
  - worker profile 默认作为 Kanban assignee 被 dispatcher 拉起，无需各自长期在线。

- **非目标 3：任何小事都强行走完整链路**
  - 小 bug、小修补、小范围调整可以走更短路径。
  - 这套设计服务于质量与闭环，不服务于流程表演。

- **非目标 4：在 V1 阶段同步引入多层治理或自动化复杂度**
  - 不在第一版主设计中叠加复杂 cron、额外 ACP 管理层、或额外同步总线。

---

## 2. 设计原则

### 2.1 Hermes 原生优先

设计必须服从 Hermes 当前 Kanban 的实际运行方式：

- gateway 内置 dispatcher
- dispatcher 按 assignee 拉起 worker
- worker 在独立 profile 上运行
- 任务状态和依赖在看板中显式维护

因此组织设计必须优先适配：

- 显式 profile
- 持久 assignee
- Kanban 原生依赖与交接结构

而不是迁就 OpenClaw 时期的抽象便利。

### 2.2 Route, Don’t Execute

`orchestrator` 的核心纪律是：

- 拆解任务
- 设计依赖
- 选择角色
- 回收结果
- 汇总对外交付信息

它不是默认实现者，不是默认 reviewer，也不是默认 QA。

### 2.3 最小必要流程

不是所有任务都需要：

`pm -> architect -> backend/frontend -> reviewer -> qa -> pm`

流程应根据任务复杂度收缩：

- 小 bug：可以不经过 `pm` 与 `architect`
- 架构/接口变化：应显式引入 `architect`
- 纯文档/方案阶段：实现链可以暂不展开

### 2.4 角色边界先于模型差异

模型差异是为了提高单位成本效率，不是为了做“模型拼盘”。

角色先定义：

- 谁拥有什么判断
- 谁不应该抢谁的活
- 谁的产出需要谁验证

模型再服务于这些边界。

### 2.5 Prompt 预算要节制

完整设计文档不是每个 worker 都需要常驻加载。

- `orchestrator`：需要完整参考路径
- `pm` / `architect`：在边界模糊时可按需查阅
- `backend-eng` / `frontend-eng` / `reviewer` / `qa`：依靠精简角色 overlay 更合理

原因是：

- 常驻 prompt 越长，漂移和噪声越大
- 实现型角色应聚焦于当前任务，不应被组织治理细节淹没

---

## 3. 运行架构

### 3.1 分层架构

当前推荐采用三层结构：

#### A. 入口层

- `default`
- `trading`

职责：

- 接收用户需求
- 判断是否应进入 Kanban
- 创建根任务并交给 `orchestrator`
- 在内部团队完成后向用户回传结果

#### B. 编排层

- `orchestrator`

职责：

- 把需求转成任务图
- 判断是否需要 `pm` / `architect`
- 决定是否需要后端、前端并行
- 设计 review / QA / sign-off 的依赖顺序
- 管理返工回流与阻塞升级

#### C. 执行与验证层

- `pm`
- `architect`
- `backend-eng`
- `frontend-eng`
- `reviewer`
- `qa`

职责：

- 各自处理明确角色域内的工作
- 通过 Kanban 原生 handoff 结构对下游移交结果

### 3.2 Gateway 与 Dispatcher

当前 Hermes Kanban 的关键运行事实：

- 至少需要一个运行中的 gateway 承载 dispatcher。
- worker profile 不需要各自长期运行 gateway。
- dispatcher 在 gateway 进程中 tick，并按 assignee 拉起 worker。
- Kanban board 默认是**跨 profile 共享**的；`default`、`trading` 和各 worker 指向同一共享 `kanban.db`。
- 因此，如果 `default` 和 `trading` 两个 gateway 都在运行，且都保留 `kanban.dispatch_in_gateway=true`，那么**会有两个独立 dispatcher loop** 对着同一块 board tick。
- 这不会把同一任务重复执行，因为底层通过 SQLite WAL + claim lock + compare-and-swap 原子 claim 机制竞争 ready 任务；但这属于“安全但冗余”的运行形态。

因此推荐：

- `default` 作为主开发入口与首选 dispatcher host
- `trading` 作为业务域入口
- 如果追求更干净的拓扑，可让 `default` 作为唯一常规 dispatcher host，把 `trading` 保留为入口但关闭其 gateway 内嵌 dispatcher
- 内部 worker 作为 Kanban assignee 存在，但不独立承担常驻消息入口职责

### 3.3 Dashboard 的角色

Dashboard 的角色是：

- 观察任务状态
- 查看依赖关系
- 人工 unblock / reassign / archive
- 查看 handoff 与注释

Dashboard 不是：

- 看板任务拆解的主决策者
- 团队角色路由器
- 业务流程解释器

流程决策发生在入口层理解 + `orchestrator` 拆解阶段。

---

## 4. 角色定义与职责边界

## 4.1 `orchestrator`

**定位**：软件开发看板调度台。

**核心职责**：

- 把需求翻译成最小有效任务图。
- 判断是否需要 `pm`、`architect`、两者都要或两者都不要。
- 当确有并行价值时，将实现拆给 `backend-eng` 与 `frontend-eng`。
- 保持 `reviewer` 为独立技术质量门。
- 保持 `qa` 为验证执行节点。
- 保持 `pm` 为功能/产品 sign-off 节点。
- 汇总内部结果，交还入口层 profile 对外回复。

**不得做的事**：

- 不因为“看起来自己也能做”就吸收实现任务。
- 不把所有任务都硬塞给 `architect`。
- 不把 `reviewer` 与 `qa` 合并成一步。
- 不在 Hermes Kanban 之外再造第二套运行协议。

## 4.2 `pm`

**定位**：产品生命周期 owner，负责一头一尾闭环：前端的创意/脑爆/产品 framing，以及尾端的产品签收与对外产品文档表达。

**核心职责**：

- 把模糊想法转成明确 deliverable、目标用户、定位和边界。
- 写出 acceptance criteria、in-scope、out-of-scope。
- 在任务早期承担产品脑爆、需求澄清、价值主张和表达框架整理。
- 代表真实用户和市场需求，避免 inside-out 的自说自话式产品定义。
- 在冻结产品方案前，必须先做方案调研：重点检查是否存在相似/类似的开源项目、成熟竞品、业内通行做法或可直接借鉴的产品方案。
- 方案调研必须看全、看细，而不是只看摘要：至少要覆盖能力边界、交互方式、配置/参数、约束、局限、部署与使用成本。
- 在范围冻结前调研成熟产品、竞品和相关开源方案，主动暴露隐藏需求、约束、实施风险与发布风险。
- 为工程、review、QA 建立共享产品上下文：这次为什么做、为谁做、成功标准是什么、哪些明确不做。
- 在实现、review、QA 完成后做功能/产品 sign-off。
- 当任务范围包含对外产品表达时，负责最终 PM-owned 交付物，例如：产品手册、用户手册、发布说明 framing、宣传文案、面向用户的产品说明文本。

**不得做的事**：

- 不替代 `reviewer` 做技术质量 gate。
- 不替代 `qa` 做验证执行。
- 不因为产品表达问题而越权改写技术架构。
- 不在 review / QA 证据不足时提前宣布产品可发布。

## 4.3 `architect`

**定位**：技术 owner / 设计 authority。

**核心职责**：

- 负责结构级判断：接口、边界、迁移、schema、contract、长期设计约束。
- 在新模块、跨服务问题、复杂重构、设计层失败时介入。
- 先想验证路径，再想实现路径。
- 在提出技术方案前，必须先做方案调研：重点检查是否存在相似/类似的开源项目、成熟系统做法、业内通行架构模式或可直接借鉴的实现基座。
- 方案调研必须看全、看细，而不是只看 README：至少要覆盖核心能力、接口/配置、约束、边界、运行要求、扩展性与已知局限。
- 在提出自研方案前，先检查是否存在成熟开源项目、现成产品模式或可借鉴实现，可通过直接采用、二次开发或思路借鉴来降低设计与开发成本。
- 对“采用/不采用某个开源基座”的判断给出明确理由，而不是默认从零设计。

**不得做的事**：

- 不默认接管所有实现。
- 不把简单 feature 和 routine bugfix 都吸进自己手里。
- 不用技术优雅凌驾于交付价值，而不显式说明代价。

## 4.4 `backend-eng`

**定位**：后端实现 owner。

**核心职责**：

- API、服务逻辑、数据层、后端 bugfix、后端测试。
- 做最小必要改动并留下可验证 handoff。

**不得做的事**：

- 不擅自改产品 scope。
- 不顺手重构无关后端代码。
- 不把自己变成 reviewer。

## 4.5 `frontend-eng`

**定位**：前端实现 owner。

**核心职责**：

- 页面、交互、组件、浏览器行为、前端测试。
- 保持与后端 contract 和 PM 验收标准一致。
- 报告 contract 不一致，而不是在本地偷偷补丁掩盖。

**不得做的事**：

- 不发明后端语义去补 API 缺口。
- 不用局部 hack 遮住真实 UX 问题。
- 不把自己变成 reviewer。

## 4.6 `reviewer`

**定位**：独立技术质量 gate owner。

**核心职责**：

- 检查实现是否符合需求、设计约束、边界处理和验证质量。
- 明确给出 pass 或 block。
- 区分真正 blocker 与风格偏好。
- 当实现影响用户可见行为、配置方式、入口流程或对外表达时，检查 PM-owned outward docs/copy 是否也需要同步更新。
- 把文档缺失、文档与真实行为不一致、或发布说明明显误导用户的情况视为真实交付问题，而不是可忽略的小瑕疵。

**不得做的事**：

- 不把 review 偷换成“我直接重写”。
- 不做无关 cleanup 扩 scope。
- 不在证据不足时批准“听起来不错”的交付。

## 4.7 `qa`

**定位**：功能验证与回归执行 owner。

**核心职责**：

- 按 PM 验收标准验证用户路径。
- 做关键回归、失败复现、未验证区域划分。
- 为 PM sign-off 生成证据。
- 当用户手册、产品手册、发布说明或 onboarding 文案在范围内时，验证真实行为是否与这些面向用户的材料一致。
- 把“功能可用但文档/说明不可用”与“功能不可用”区分开来，并显式汇报。

**不得做的事**：

- 不替代 `pm` 做最终产品签收。
- 不替代 `reviewer` 做技术质量 gate。
- 不在只有推断没有证据时宣称“已验证”。

## 4.8 `default` / `trading`

**定位**：对用户交付的入口 owner。

**核心职责**：

- 接收需求
- 决定是否进入看板
- 创建根任务给 `orchestrator`
- 在内部团队闭环后，对用户输出完整结果

**不得做的事**：

- 不长期吞掉复杂开发任务不下放
- 不代替内部团队装作已经完成细部工作

---

## 5. 验收、签收与 handoff 契约

### 5.1 内部责任链

正确责任链如下：

- `pm`：定义完成标准，并在 QA 后做功能/产品 sign-off
- `reviewer`：做技术质量 gate
- `qa`：做功能/行为验证执行
- `architect`：做系统级兜底（按需）
- `orchestrator`：保证流程走完并回收干净
- `default` / `trading`：向用户交付结论
- **用户**：最终 accept / reject

### 5.2 Hermes Kanban 原生 handoff 映射

OpenClaw 里高价值的交接纪律应映射为 Hermes 原生结构：

- `summary`
  - 一句话概括“做了什么”
- `metadata.artifacts`
  - 文件路径、分支、PR、URL、截图、说明文档
- `metadata.verification`
  - 测试命令、结果、手工验证路径
- `metadata.known_issues`
  - 已知问题与限制
- `metadata.next_steps`
  - 下一步建议
- `comments`
  - 长上下文补充、block 说明、返工原因

### 5.3 Review 阻塞处理

如果 `reviewer` block：

- 不建议简单重跑同一任务
- 应创建新的返工任务回流给原 implementer
- `orchestrator` 负责把返工依赖接对

### 5.4 QA 失败处理

如果 `qa` 失败：

- 若是局部功能缺陷，回流 implementer
- 若失败已上升为结构性问题，升级 `architect`
- `pm` 不应在 QA 失败时直接签收

---

## 6. 推荐任务流模板

### 6.1 中等复杂 feature

适用场景：

- 有明确产品目标
- 存在前后端协作
- 需要 review 与 QA
- 可能需要但不必然需要架构判断

建议任务图：

```text
T1  pm            澄清需求 + acceptance criteria
T2  architect     设计约束 / 接口边界（可选）
T3  backend-eng   后端实现
T4  frontend-eng  前端实现
T5  reviewer      技术质量 gate
T6  qa            验收与回归
T7  orchestrator  汇总并回传入口层
```

推荐依赖：

```text
T2 <- T1
T3 <- T1 (+ T2 when needed)
T4 <- T1 (+ T2 when needed)
T5 <- T3 + T4
T6 <- T5
T7 <- T6
```

### 6.2 小 bug

适用场景：

- 单点修复
- 范围明确
- 不涉及产品重新澄清或架构重画

建议任务图：

```text
T1  orchestrator  判断是否需要拆
T2  backend-eng 或 frontend-eng  修复
T3  reviewer      review
T4  qa            回归
```

原则：

- 默认不强上 `pm + architect` 全链路
- 但如果 bug 暴露出设计层问题，可升级引入 `architect`

### 6.3 架构/接口变化任务

适用场景：

- schema 变化
- contract 变化
- 新模块
- 跨服务协作

建议任务图：

```text
T1  pm            明确业务目标和验收边界
T2  architect     设计方案、迁移策略、接口边界
T3  backend-eng   后端实现 / 迁移
T4  frontend-eng  前端适配（按需）
T5  reviewer      结构与实现复核
T6  qa            路径验证 / 回归
T7  pm            产品 sign-off
T8  orchestrator  总结与回传
```

---

## 7. 模型与工具策略

### 7.1 模型分配原则

- 不把 `gpt-5.4` 设成所有 profile 默认。
- 最强模型优先给高杠杆判断位。
- 实现位优先考虑编码迭代效率。
- 验证位优先考虑覆盖率、成本和多模态需求。

### 7.2 当前推荐模型矩阵

- `orchestrator` → `newapi / minimax-m2.5`
  - 适合高频分解、依赖设计、状态管理
- `pm` → `newapi / kimi-k2.5`
  - 适合规格化表达、较长上下文梳理
- `architect` → `newapi-openai / gpt-5.4`
  - 适合高风险结构级判断
- `backend-eng` → `newapi / deepseek-v4-pro`
  - 适合高密度实现与调试 loop
- `frontend-eng` → `newapi-gemini / gemini-3-pro`
  - 适合 UI/交互/浏览器与视觉相关判断
- `reviewer` → `newapi-openai / gpt-5.4`
  - 适合独立技术质量 gate
- `qa` → `newapi / glm-5`
  - 适合广覆盖验证；多模态场景可按需升级 Gemini

### 7.3 工具边界原则

#### `orchestrator`
应保持轻量：

- 保留：`skills`、`memory`、`session_search`、`clarify`
- Kanban worker 运行时注入 `kanban` 能力
- 不保留：`terminal`、`file`、`browser`、`code_execution` 等实现型工具

#### `pm`
应以需求澄清和证据读取为主：

- 可保留：`web`、`browser`、`file`、`vision`、`clarify`
- 不保留：`terminal`、`code_execution`

#### `architect`
应具备设计和现场诊断能力，但不鼓励变成默认 implementer：

- 可保留：`terminal`、`file`、`web`、`browser`、`vision`
- `code_execution` 可谨慎限制

#### `backend-eng` / `frontend-eng`
应保留实现所需主工具：

- `backend-eng`：`terminal`、`file`、`code_execution`、`web`
- `frontend-eng`：`terminal`、`file`、`browser`、`vision`、`code_execution`、`web`

#### `reviewer`
应偏检查和验证，不偏实现：

- 可保留：`terminal`、`file`、`web`、`browser`、`vision`
- 默认不保留：`code_execution`

#### `qa`
应偏验证执行：

- 可保留：`browser`、`vision`、`terminal`、`file`、`web`
- 默认不保留：`code_execution`

---

## 8. 与 OpenClaw 的关系

### 8.1 继承的部分

本方案明确继承 OpenClaw 的高价值经验：

- **Architect = 技术 owner，而不是通用助手**
- **Orchestrator = 只拆、只派、只收，不抢活**
- **handoff 必须带验证与后续动作**
- **不是所有任务都要强行全编排**

### 8.2 不直接照搬的部分

本方案明确不照搬以下 OpenClaw 机制：

- `main` 长期兼任 orchestrator 人格
- `architect` 默认包打天下
- PM / FE / BE / QA 只作为任务帽子
- ACP 员工池直接成为组织主骨架

### 8.3 根本差异

OpenClaw 的组织抽象更像：

- 少数 owner 常驻
- 大量职责通过 task hat 和 ACP 员工池动态拼装

Hermes 本方案更像：

- 入口层 profile
- 显式 Kanban 团队
- 固定 assignee
- 角色主路模型
- 可选外部执行器作为第二层增强

### 8.4 关于 task hat 的保留方式

在本方案中：

- task hat 可以保留为 `orchestrator` 的**思考语言**
- 但运行时必须完全尊重 Hermes Kanban 的原生 contract

也就是说：

- 分解时可以思考“这是 PM 帽 / Reviewer 帽 / QA 帽”
- 落到系统里时，必须写成真实 `assignee`、依赖和 handoff

---

## 9. 运行建议

### 9.1 入口建议

- 主开发入口：`default`
- 业务域开发入口：`trading`

建议避免形成两套彼此分裂的开发看板；更好的做法是：

- `default` 承担主开发入口
- `trading` 仅在业务域上下文明显相关时作为入口
- 内部仍共享同一套开发团队角色体系

入口层的标准 Kanban 动作应明确为：

- 当用户明确要求“交给看板处理 / 走 kanban / 用看板完成这个需求”时，入口层应**创建一个真实的 root task**，而不是只在会话中说“我转交给 orchestrator”。
- 该 root task 默认应：
  - `assignee = orchestrator`
  - 进入可运行状态（通常为 `ready`）
  - 在 `body` 中保留用户原始需求、限制、上下文与任何验收要求
- 只有在用户只是暂存一个粗糙想法、并未要求开始推进执行时，才优先考虑 triage 路径。
- 如果任务是从 Dashboard 手工创建的，运行时含义与入口层自动创建相同：本质上都是在共享 board 上创建 `assignee=orchestrator` 的真实任务。

### 9.2 Worker 是否需要独立 gateway

默认不需要。

原因：

- Hermes Kanban 已通过 dispatcher 拉起 worker
- 单独让每个 worker 带 gateway 会显著增加运行复杂度
- 对于当前目标，组织协作收益远大于消息常驻收益

### 9.3 用户侧心智模型

理想使用方式是：

- 用户只说“交给看板处理这个需求：……”
- 入口层负责**创建 root task 并指定 `assignee=orchestrator`**
- `orchestrator` 决定图怎么拆
- Dashboard 用于观察和人工干预

---

## 10. 文档消费策略

### 10.1 必须长期保留引用路径的角色

#### `orchestrator`
必须长期保留本文档的引用路径。

原因：

- 它负责任务拆解与角色选择
- 它最容易因为“短 prompt 下的临场判断”而偏离既定团队协作设计
- 它需要在复杂任务时重新核对：
  - 哪些角色应该出现
  - 哪些角色默认不该出现
  - review / QA / PM sign-off 的顺序
  - 何时该引入 `architect`
  - 何时不应强上完整链路

### 10.2 不建议默认长期保留引用路径的角色

#### `backend-eng` / `frontend-eng`
不建议默认长期挂本文档路径。

原因：

- 他们主要关心当前实现任务，不关心整套组织治理设计
- 长期挂载会增加 prompt 噪声，降低执行聚焦度

#### `reviewer` / `qa`
不建议默认长期挂本文档路径。

原因：

- 他们需要的是清晰的角色边界和输出契约
- 当前 role overlay 已足够表达其主责
- 遇到争议时可由 `orchestrator` 或入口层转述，不必常驻加载全量文档

### 10.3 可按需参考但不建议常驻挂载的角色

#### `pm` / `architect`
不建议默认常驻挂载，但允许在以下场景按需查阅：

- `pm`：对签收链、scope 边界、角色分工存在疑问时
- `architect`：对是否需要自身介入、边界升级、或系统级兜底范围存在疑问时

原因：

- 他们属于决策型角色，但不是流程编排 owner
- 按需查阅比常驻注入更节省 prompt 预算

---

## 11. 验收标准

当本方案被视为“已落地”时，应满足以下条件：

### 11.1 Profile 层

- 7 个内部 worker profile 均存在
- 各自 `SOUL.md` 已定义边界
- 默认模型与本文档一致
- 配置结构和运行时校验通过

### 11.2 组织层

- `default` / `trading` 仍作为入口层存在
- worker 无需独立 gateway
- 至少一个 gateway 可承载 dispatcher

### 11.3 流程层

- `orchestrator` 能稳定拆图
- `backend-eng / frontend-eng` 能作为 assignee 执行实现
- `reviewer` 能独立做质量 gate
- `qa` 能独立做验证执行
- `pm` 能在 QA 后做功能/产品 sign-off
- 返工回流与 block 路径清晰

### 11.4 交付层

- 内部 handoff 使用 Hermes Kanban 原生结构
- 入口层能把内部结果翻译成对用户可消费的交付
- 用户仍保留最终 accept / reject 拍板权

---

## 12. 最终结论

一句话概括本方案：

**OpenClaw 更像“owner + 任务帽 + ACP 员工池”的 AI 公司式执行抽象；当前 Hermes 更适合“入口层 + 显式 Kanban 团队 + 角色主路模型”的软件开发协作流程。**

在这个框架下：

- `orchestrator` 是流程编排 owner
- `pm` 是需求与产品签收 owner
- `architect` 是技术 owner
- `backend-eng / frontend-eng` 是实现 owner
- `reviewer` 是技术质量 gate owner
- `qa` 是验证执行 owner
- `default / trading` 是对用户交付的入口 owner

这套设计更贴合 Hermes 当前的 dispatcher / assignee / worker profile 运行方式，也更适合长期把 `default` / `trading` 作为稳定需求入口来持续驱动开发任务。