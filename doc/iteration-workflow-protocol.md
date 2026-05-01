---
title: 具体迭代工作流协议
date: 2026-05-01
tags:
  - workflow
  - iteration
  - issue-tracking
  - tdd
  - documentation
  - matt-pocock
aliases:
  - Iteration Workflow Protocol
  - 迭代协议
cssclasses:
  - documentation
---

# 具体迭代工作流协议

> 基于 Matt Pocock Skills 设计模式，构建科研+项目的微观迭代操作协议。

## 与宏观工作流的关系

```
宏观工作流模型（Project_Workflow_Model / Research_Workflow_Model）
  告诉你在哪个阶段、用什么工具
          ↓
本协议（Iteration Workflow Protocol）
  告诉你在每个阶段内部，如何具体操作一个迭代单元
```

宏观模型是地图，本协议是驾驶手册。

## 目录

- [[#设计原则]]
- [[#Phase 0 项目预热 Warmup]]
- [[#Phase 1 想法到 Issue]]
- [[#Phase 2 Issue 生命周期状态机]]
- [[#Phase 3 实现循环]]
- [[#Phase 4 审查协议]]
- [[#Phase 5 归档与知识沉淀]]
- [[#附录 A Agent Brief 撰写规范]]
- [[#附录 B ADR 撰写规范]]
- [[#附录 C triage-context.md 状态语义表]]
- [[#附录 D 各阶段速查表]]

---

## 设计原则

### 1. 平台无关 (Config-Driven)

本协议不硬编码 GitHub、GitLab 或任何特定平台。每个项目在 Phase 0 (Warmup) 时声明自己的后端。所有后续 Phase 通过抽象角色名引用概念，由项目配置映射到具体实现。

### 2. 耐久性优先于精确性 (Durable over Precise)

所有书面产物（Issue Body、Agent Brief、ADR、PRD）禁止引用：
- 文件路径（会改名）
- 行号（会漂移）
- 当前实现细节（会过时）

正确做法：描述 **interface**、**behavior**、**contract**。

### 3. 垂直切片 (Vertical Slicing)

每个工作单元切穿所有集成层。偏好多薄片而非少厚片。每个切片完成后可独立验证。

```
水平: 先做所有数据层 → 再做所有 API 层 → 再做所有 UI 层
垂直: Slice1(schema+API+UI+test) → Slice2 → Slice3
```

### 4. Phase Gate 门禁

每个 Phase 有明确的入口条件和出口条件。不满足条件则不得前进。关键决策点必须暂停并等待人类批准（HITL）。

### 5. 领域语言即真相来源 (Domain Language as Source of Truth)

`CONTEXT.md` + `docs/adr/` 构成项目的概念骨架。代码结构应反映领域语言，而非反过来。所有质询、审查、文档更新都围绕领域术语表展开。

### 6. 约定驱动 (Convention-Driven)

状态转换和 Agent 行为由 skill 描述中的约定驱动，不做硬编码的规则引擎门禁。Agent 依据上下文自行判断"是否够格"，与 Matt Pocock 体系保持一致。

---

## Phase 0: 项目预热 (Warmup)

> 新项目首次使用本协议时必须执行。已配置项目可跳过。幂等操作——重复运行会更新而非覆盖。

### 目标

生成其他所有 Phase 所依赖的项目配置，使协议适配到具体项目的平台和约定。

### 入口条件

- 项目根目录已初始化（git 或其他 VCS）
- 已知项目的基本技术栈和领域

### 出口条件

以下文件全部就位：

| 文件 | 用途 |
|------|------|
| `docs/agents/issue-tracker.md` | Issue 追踪后端声明 |
| `docs/agents/triage-labels.md` | Canonical role → 实际标签字符串映射 |
| `docs/agents/domain.md` | 领域文档布局规则 |
| `docs/agents/triage-context.md` | 状态语义表 + Agent Brief 字段语义（按项目类型） |
| `CONTEXT.md` | 项目领域术语表（初始骨架） |

### 四项交互决策

| 决策 | 选项 | 默认 |
|------|------|------|
| **Issue 追踪后端** | GitHub Issues / 本地 markdown (`.scratch/issues/`) / 飞书 / 其他 | GitHub |
| **Triage 标签映射** | 将 canonical roles 映射到项目实际标签字符串 | 直接使用 canonical 名称 |
| **领域文档布局** | 单文件 `CONTEXT.md` / 多文件 `CONTEXT-MAP.md` | 单文件 |
| **项目类型** | 科研 (Research) / 开源维护 (OSS) / 内部开发 (Dev) | — |

### 依赖分类

| 类别 | Phase | 行为 |
|------|-------|------|
| **Hard 依赖** | Phase 1 (to-issues、to-prd)、Phase 2 (triage 状态操作) | 缺少配置时输出会出错，必须指向 Phase 0 |
| **Soft 依赖** | Phase 3 (实现)、Phase 4 (审查)、Phase 5 (归档) | 缺少配置时降级运行，用 vague prose 引用领域术语 |

### CONTEXT.md 初始骨架

```markdown
# CONTEXT.md — [项目名] 领域术语表

## 核心概念

### [概念A]
- **定义**: ...
- **边界**: 包含/不包含什么
- **代码映射**: 对应的模块/包（不写具体文件路径）

### [概念B]
- **定义**: ...
- **边界**: ...

## 术语对照

| 领域术语 | 英文 | 代码中的命名 |
|----------|------|-------------|
| ... | ... | ... |
```

---

## Phase 1: 想法 → Issue

> 把模糊的想法、需求、Bug 报告转化为结构化的、可执行的工作单元。

### 流程总览

```
想法/需求/Bug
    │
    ├── 是 Bug？→ 直接进入 to-issues 写 Issue（无需 grill）
    │
    └── 是 Feature/改进？
         ├── office-hours / research-hours（可选前置，发散找方向）
         │     └── 多角色讨论，拓宽视角
         │
         ├── grill-me / grill-with-docs（收敛，删除不确定性）
         │     └── 逐问逐答，一次一个问题，你说停才停
         │
         ├── to-prd（固化方案，不新增问题）
         │
         └── to-issues（垂直拆解为 Issue）
```

### 1.1 office-hours / research-hours（可选前置）

当方向不明确时，先通过多角色讨论（同事/mentor/PI 视角）发散探索，找到大致方向后再进入 grill 收敛。

不是必须步骤——如果方向已经明确，直接进入 grill。

### 1.2 grill-me / grill-with-docs — 质询协议

**核心机制**：逐问逐答的决策树深度优先遍历。Agent 每次只问当前最大的不确定性，基于你的回答往下走，直到你说"够了"或所有分支被走完。

**保持 Matt Pocock 原样**：不设固定的提问清单。Agent 动态判断该问什么——固定清单会限制 Agent 根据具体想法做出有价值追问的能力。

#### grill-me — 裸质询

适用：快速验证一个想法，不涉及项目文档。

参考材料：只有你对想法的描述。

出口条件：用户说"够了，开始写 Issue"或"这个想法不做了"。

#### grill-with-docs — 文档驱动质询

适用：涉及架构决策、领域概念变更、多系统交互的功能。

参考材料：CONTEXT.md + ADR + 代码库实际状态。

额外能力：
- 术语冲突检测——对照 CONTEXT.md 逐项检查
- 代码交叉验证——用户说法 vs 代码实际行为
- 实时更新 CONTEXT.md——新术语被实时收录
- 按需触发 ADR——满足三条件时创建

出口条件：所有模糊点被澄清，无未解决的术语冲突。

### 1.3 to-prd — 合成 PRD（可选）

仅在大型功能、多系统交互、或需要留下正式书面记录时使用。

**原则**：不询问用户新问题——仅从对话上下文中合成已知信息。grill 讨论完已经很清楚，不想再被追问，只想快速固化。

**与 /plan / planner 的区别**：to-prd 是合成，不是规划。planner 会追问和设计步骤；to-prd 只格式化已讨论的内容。

**PRD 结构**：

```markdown
# PRD: [功能名称]

## Problem Statement
[一句话描述问题]

## Current State
[现有系统的相关行为]

## Proposed Solution
[高层次方案描述，不涉及实现细节]

## User Stories
- [ ] [Story 1]
- [ ] [Story 2]

## Integration Points
[与哪些现有系统/模块交互]

## Acceptance Criteria
- [ ] [可独立验证的验收条件 1]
- [ ] [可独立验证的验收条件 2]

## Out of Scope
- 明确不做的事情

## Decisions
[需要 ADR 记录的决策及理由]
```

### 1.4 to-issues — 垂直拆解为 Issue

**核心原则**：
- 每个 Issue 切穿所有集成层（schema → logic → API → UI → tests），完成后可独立演示或验证
- 偏好**多薄片**而非**少厚片**
- 按依赖顺序创建（blocker 优先）
- 科研实验以"一个实验一个切片（数据→模型→训练→评估→分析）"为示例性指导，具体由上下文决定

**Issue 分类**（继承 Matt Pocock）：

| 类型 | 全称 | 含义 | 示例 |
|------|------|------|------|
| **AFK** | Away From Keyboard | AI 可独立完成 | CRUD 接口、纯后端逻辑、Bug fix |
| **HITL** | Human In The Loop | 需要人类决策或审查 | 架构决策、UI 设计、实验设计 |

> 偏好 AFK，除非确实需要人类参与。

---

## Phase 2: Issue 生命周期状态机

> 管理 Issue 从创建到关闭的完整生命周期。

### 核心机制

`/triage` 是核心驱动命令——它定期扫描，推进所有能推进的 Issue。但它只做**评估和路由**，不执行实现。

### 角色分工

| 状态层 | 驱动方式 | 负责 |
|--------|---------|------|
| **评估侧**（unlabeled / needs-triage / needs-info / ready-* / wontfix） | `/triage` 定期扫描 | triage Agent |
| **执行侧**（in-progress / in-review / done / changes-requested） | 执行者手动变更 | 人或 Agent |

### 状态机定义

```
unlabeled
    ├──→ needs-triage        (首次查看 → 待评估)
    ├──→ ready-for-agent     (已充分指定 → 附 Agent Brief)
    ├──→ ready-for-human     (需人类实现 → 附摘要)
    └──→ wontfix             (垃圾/重复/超出范围)

needs-triage
    ├──→ needs-info          (信息不足 → 附 Triage Notes)
    ├──→ ready-for-agent     (质询完成 → 附 Agent Brief)
    ├──→ ready-for-human     (需人类 → 附摘要)
    └──→ wontfix

needs-info
    └──→ needs-triage        (信息补充后 → 重新评估)

ready-for-agent
    ├──→ in-progress         (人/Agent 开始工作)
    └──→ blocked             (被其他 Issue 阻塞)

ready-for-human
    └──→ in-progress

in-progress
    ├──→ in-review           (实现完成，等待审查)
    └──→ blocked

in-review
    ├──→ changes-requested   (审查不通过)
    └──→ done                (审查通过)

changes-requested
    └──→ in-progress         (修改后重新提交)

blocked
    └──→ ready-for-agent | ready-for-human  (阻塞解除)

done
    └── [终态] 归档，必要时触发 Phase 5
```

### 一个 Issue 的完整生命周期

```
1. 创建 Issue — 人（或 to-issues）创建，初始状态 unlabeled

2. 人触发 /triage — triage Agent 扫描 + 分类打标签
   → ready-for-agent 的 Issue 附 Agent Brief
   → wontfix 写入 .out-of-scope/
   → needs-info 附 Triage Notes
   → 结束。不执行任何 Issue 内的实际工作

3. 人决定"做这个 Issue" — 人或 Agent 改为 in-progress，开始实现

4. 实现完成 — 执行者改为 in-review

5. 审查 — 通过 → done / 不通过 → changes-requested

6. done — 触发 Phase 5 归档
```

### 状态语义表

状态含义由 `docs/agents/triage-context.md` 按项目类型定义。见 [[#附录 C triage-context.md 状态语义表]]。

### Agent Brief

AFK Issue 的结构化合约。保持 Matt Pocock 的 5 字段结构，不同场景的语义由 triage-context.md 解释。

| 字段 | 项目语义 | 科研语义 |
|------|---------|---------|
| **Context** | 为什么需要这个功能 | 为什么需要这个研究任务，对应哪个假设 |
| **Contract** | 对外暴露的接口+不变量+依赖 | 输入数据格式+前提条件 → 输出产物形式 |
| **Expected Behavior** | Happy Path / Error Cases / Boundary | 预期结果 / 什么情况否定假设 / 方法适用范围边界 |
| **Acceptance Criteria** | 可独立验证的完成条件 | 同左 |
| **Out of Scope** | 不做的事 | 不研究的方向 |

完整模板见 [[#附录 A Agent Brief 撰写规范]]。

### .out-of-scope/ 知识库

持久化记录被拒绝的请求。一个文件一个 concept，相同 concept 的多次拒绝合并到同一文件。每次 `/triage` 自动检查，防止重复讨论。

---

## Phase 3: 实现循环

> 从 `ready-for-agent` 取走 Issue，完成实现，推到 `in-review`。

### 实现路径

| 场景 | 方法 | 说明 |
|------|------|------|
| **项目实现** | TDD (Red→Green→Refactor) | 垂直切片，一次一条 AC |
| **科研实现** | 上下文驱动，Agent 辅助 | 不规定具体流程，由 Issue 上下文和用户需求决定 |
| **Bug（统一）** | Diagnose (6 Phase Gate) | 项目和科研共用 |

### 3.1 TDD 循环

```
RED:   写一个测试 → 失败
GREEN: 最小代码 → 通过
        ↓
      循环（一个测试一个 cycle）
        ↓
REFACTOR: 去重 → 深化 → 审视新代码揭示的问题
```

**垂直切片执行**：每次 RED→GREEN 是一个完整的垂直切片，切穿所有集成层。不做"先写所有测试再写所有实现"的水平批处理。

**测试规范**：
- Good test：像一条规范——"user can checkout with valid cart"
- Bad test：与实现耦合——mock 内部 collaborator、测试 private method
- Mock 规则：只在系统边界 mock（外部 API、数据库、时间/随机数）；禁止 mock 自己的 class/module
- 测试命名：使用项目领域术语表中的词汇

### 3.2 Diagnose — 诊断循环

> 当 TDD 无法直接解决问题（复杂 Bug、多系统交互问题、性能回退）时使用。项目和科研共用。

#### 六阶段流程

| Phase | 名称 | 核心动作 | 门禁条件 |
|-------|------|----------|----------|
| 1 | **Build Feedback Loop** | 构造快速、确定性的 pass/fail 信号 | 必须有一个可信的 loop |
| 2 | **Reproduce** | 运行 loop，确认复现了用户描述的 Bug | 确认是同一个 Bug（非附近） |
| 3 | **Hypothesise** | 生成 3-5 个**可证伪**的排序假设 | 向用户展示排名 |
| 4 | **Instrument** | 每次改一个变量，针对性探测 | 只用 debugger/目标 log |
| 5 | **Fix + Regression Test** | 先在正确 seam 写回归测试，再修 | 存在正确 seam 或有文档记录缺失 |
| 6 | **Cleanup + Post-mortem** | 清理调试代码 + 询问"什么能阻止这个 Bug" | 6 项 checklist 全部打勾 |

#### Feedback Loop 构建优先级（从高到低）

1. Failing test — 在 Bug 可达的任一层级
2. Curl / HTTP 脚本 — 对运行的 dev server
3. CLI 调用 — 用固定输入 + diff 已知正确输出
4. Headless browser — Playwright/Puppeteer
5. Replay captured trace — 录制真实请求并重放
6. Throwaway harness — 最小子系统
7. Property / fuzz loop — 1000 次随机输入寻失败
8. Bisection harness — `git bisect run` 自动化
9. Differential loop — 新旧版输出 diff
10. HITL bash script (最后手段)

> "A 30-second flaky loop is barely better than no loop. A 2-second deterministic loop is a debugging superpower."

### 3.3 辅助工具

| 场景 | 工具 |
|------|------|
| 不熟悉代码区域 | zoom-out — 使用 codebase-onboarding 或 graphify 获取模块关系总览 |
| 发现架构味道 | 暂停实现，触发架构深化（improve-codebase-architecture） |

**架构深化触发条件**：
1. 写测试时发现无法在不 mock 内部模块的情况下测试
2. 新代码需要在 3 个以上地方复制相似逻辑
3. 修改现有代码时发现接口泄露了实现细节
4. Deletion Test 失败——删除某个 module 后 N 个调用者重现了相同复杂性
5. 新功能与现有 CONTEXT.md 中的术语定义冲突

---

## Phase 4: 审查协议

> 代码完成、推到 `in-review` 后进入。

### 审查机制

`in-review` 状态触发并行审查。审查标准由审查 Agent 按上下文判断，不写死固定的审查矩阵。

审查流程：

```
in-review
    │
    ├── 并行审查
    │   ├── code-reviewer Agent
    │   └── security-reviewer Agent
    │
    ├── 汇总审查意见
    │
    ├── CRITICAL 问题 → changes-requested（必须修）
    ├── HIGH 问题 → changes-requested（应该修）
    ├── MEDIUM 问题 → 记录
    └── 全部通过 → done
```

---

## Phase 5: 归档与知识沉淀

> Issue 进入 `done` 后，触发知识沉淀流程。

### 5.1 目录结构

```
archive/
├── context.md            (共享：项目级领域术语，同 CONTEXT.md)
├── adr/                  (共享：架构决策记录)
├── out-of-scope/         (共享：被拒绝的方向，按 concept 分文件)
│
├── plan-NNNN-xxx/        (一个 plan 一个目录)
│   ├── prd.md            (grill 的固化产出)
│   ├── issues/
│   │   ├── NNNN-xxx.md   (issue 详情，done 时末尾追加 Issue Summary)
│   │   └── ...
│   └── synthesis.md      (Plan 级合成：所有 issue done 后汇总)
```

### 5.2 Issue Summary（单 Issue 完成时）

Issue done 时，在 Issue 文档末尾追加以下 6 字段：

```markdown
## Issue Summary

- **Purpose**：出于什么目的做这个 Issue
- **Motivation**：出于什么原因决定按这个做法做
- **Approach**：具体做了什么
- **Result**：定量/定性结果
- **Conclusion**：对假设的影响（supported / refuted / inconclusive），后续方向影响
- **Lessons Learned**：技术上踩了什么坑、什么可以复用
```

### 5.3 Plan 级合成 (synthesis.md)

**触发条件**：最末端 Issue（不被同 Plan 下任何其他 Issue 依赖的那个）进入 `done` 时，Agent 扫描同 Plan 目录下所有 Issue，若全部 done，则写 synthesis.md。

无中央调度器——Plan 完整性靠依赖链隐式保证，与 Matt Pocock 体系一致。

**内容**：将各 Issue Summary 的 Conclusion 串联，形成这个 Plan 的整体结论。一页足够。

### 5.4 ADR 创建条件

**三条件必须全满足**才创建 ADR：

| 条件 | 说明 | 反例 |
|------|------|------|
| **难逆转** | 改变主意的成本有意义 | "换个变量名"（容易） |
| **缺上下文令人惊讶** | 未来的读者会疑惑为什么这么做 | "用了最常见的模式"（显而易见） |
| **真实权衡** | 有替代方案，选了其一 | "唯一可选项"（没有选择） |

不满足的记录在 commit message 或 PR description 即可。

ADR 模板见 [[#附录 B ADR 撰写规范]]。

### 5.5 CONTEXT.md 更新节奏

| 触发事件 | 更新内容 | 时机 |
|----------|---------|------|
| 新概念引入 | 添加术语定义、边界 | 合并 PR 时 |
| 概念边界变化 | 更新定义、标注变更日期 | 合并 PR 时 |
| 概念被弃用 | 标注 deprecated + 替代概念 | 合并 PR 时 |
| 概念拆分为多个 | 拆分为子条目 | 合并 PR 时 |

### 5.6 .out-of-scope/ 维护

- **查询时机**：每次 `/triage` 新 Issue 时自动检查，防止重复讨论
- **写入时机**：Issue 被标记为 `wontfix` 时
- **组织方式**：按 concept 分文件，不是按 Issue 分文件

---

## 附录 A: Agent Brief 撰写规范

### 设计原则

1. 描述 interface，不描述文件路径/行号
2. 描述 what，不规定 how
3. 完整验收标准——每个标准可独立验证
4. 明确 scope 边界

### 模板

```markdown
# Agent Brief: [Issue Title]

## Context Summary
[1-2 句话：为什么需要这个改动，它在整个系统中的角色]

## Contract
### 对外暴露
- [函数签名 / API endpoint / 数据结构]
- [不变量：调用者可以依赖什么？]
- [错误模式：什么情况下会失败？如何失败？]

### 依赖
- [需要哪些外部系统/模块的什么 interface？]

## Expected Behavior
### Happy Path
1. [步骤 1]
2. [步骤 2]
3. [预期结果]

### Error Cases
- [错误场景 1] → [预期行为]
- [错误场景 2] → [预期行为]

### Boundary Conditions
- [边界条件 1]：[预期行为]

## Acceptance Criteria
- [ ] [可独立验证的条件 1]
- [ ] [可独立验证的条件 2]

## Out of Scope
- [明确不做的事情]
```

### 科研示例

```markdown
# Agent Brief: 验证增益优化对去噪效果的影响

## Context Summary
当前噪声建模假设均匀增益，实际红外图像存在像素级增益差异（FPN）。
本实验验证添加可优化 gain 参数是否能提升去噪效果。

## Contract
### 对外暴露
- `optimize_gain: bool` — 是否优化增益参数
- gain 参数 shape 与输入 channel 维度一致

### 依赖
- 现有去噪模块的 forward() 接口不变
- 数据集：红外图像 test set，100 张

## Expected Behavior
### Happy Path
1. 设置 optimize_gain=True
2. 训练 100 epochs
3. gain 参数收敛至非全 1 值，PSNR 相比 baseline 提升

### Boundary Conditions
- 如果 gain 和 offset 同时优化，参数可能耦合，收敛变慢

## Acceptance Criteria
- [ ] optimize_gain=True 时 gain 参数参与梯度更新
- [ ] 与 baseline 相比 PSNR 不下降（优化收敛后）
- [ ] gain 参数收敛值的可视化图表产出

## Out of Scope
- 自适应 gain/offset 区域划分
- 多帧联合 gain 估计
```

---

## 附录 B: ADR 撰写规范

### 模板

```markdown
# ADR-NNNN: [简短标题]

## 日期
YYYY-MM-DD

## 状态
proposed | accepted | deprecated | superseded

## 上下文
[为什么需要做这个决策？什么约束导致了现在的问题？]

## 决策
[我们做了什么选择]

## 替代方案
### 方案 A: [名称]
- **优点**: ...
- **缺点**: ...
- **为什么没选**: ...

### 方案 B: [名称]
- ...

## 后果
### 正面
- ...

### 负面
- ...

### 中性
- ...
```

### 命名规范

```
docs/adr/0001-use-pglite-for-local-testing.md
docs/adr/0002-domain-model-separates-physical-from-logical.md
```

编号连续，标题用 dash-case，动词开头。

---

## 附录 C: triage-context.md 状态语义表

### 状态语义

| Canonical Role | 科研 (Research) | 开源维护 (OSS) | 内部开发 (Dev) |
|---------------|-----------------|---------------|---------------|
| `unlabeled` | 新 Idea/问题，尚未评估 | 新 Issue，无人查看 | 新任务，未分配 |
| `needs-triage` | 已记录，等待分类评估 | 已被看到，待分类 | 已入队列，待评估优先级 |
| `needs-info` | **需要进一步调研/文献查证/预实验** | **等待报告者补充信息** | **需要调查代码/日志/数据** |
| `ready-for-agent` | 规格充分，Agent 可执行 | 规格充分，AI 可独立实现 | 规格充分，AI 可执行 |
| `ready-for-human` | 需人类操作（湿实验/硬件调试/手动标注） | 需人类实现 | 需人类实现 |
| `in-progress` | 正在执行中 | 正在实现 | 正在开发 |
| `blocked` | 等待依赖（数据收集/前置实验/外部结果） | 被其他 Issue 阻塞 | 被其他任务阻塞 |
| `in-review` | 结果待审查（实验可复现？结论有效？） | 代码待审查 | 代码待审查 |
| `changes-requested` | 需修正/补充实验/补充分析 | 需修改代码 | 需修改代码 |
| `wontfix` | 拒绝：方向不成熟/超出范围/不可行 | 拒绝：垃圾/重复/超出范围 | 拒绝：不做/暂缓 |
| `done` | 完成归档（实验结论+经验沉淀） | 完成合并 | 完成上线 |

### Agent Brief 字段语义

| 字段 | 项目语义 | 科研语义 |
|------|---------|---------|
| **Context** | 为什么需要这个功能 | 为什么需要这个研究任务，对应哪个假设 |
| **Contract** | 对外暴露的接口+不变量+依赖 | 输入数据格式+前提条件 → 输出产物形式 |
| **Expected Behavior** | Happy Path / Error Cases / Boundary | 预期结果 / 什么情况否定假设 / 方法适用范围边界 |
| **Acceptance Criteria** | 可独立验证的完成条件 | 同左 |
| **Out of Scope** | 不做的事 | 不研究的方向 |

---

## 附录 D: 各阶段速查表

### 按场景查 Phase

| 我想…… | 进入 Phase |
|--------|-----------|
| 新项目初始化工作流配置 | Phase 0: Warmup |
| 提交一个新功能请求 | Phase 1: 想法→Issue |
| 报告一个 Bug | Phase 1: 直接写 Issue |
| 评审一个设计方案 | Phase 1: grill-with-docs |
| 把大计划拆成可执行的 Issue | Phase 1: to-issues |
| 分类和管理 Issue | Phase 2: 状态机（/triage） |
| 开始写代码 | Phase 3: 实现循环 |
| 调试一个复杂 Bug | Phase 3: Diagnose |
| 代码写完了，需要审查 | Phase 4: 审查协议 |
| Issue 完成了，需要归档 | Phase 5: 归档与沉淀 |

### 按 Phase 查入口/出口

| Phase | 入口条件 | 出口产物 |
|-------|---------|---------|
| 0 Warmup | 项目已初始化、已知技术栈 | `docs/agents/*.md` + `CONTEXT.md` 初始骨架 |
| 1 Idea→Issue | 有想法/需求/Bug 描述 | Issue(s) + 可选 PRD + 可选 ADR |
| 2 状态机 | Issue 已创建 | Agent Brief / Triage Notes / .out-of-scope/ 条目 |
| 3 实现循环 | Issue 状态为 `ready-for-agent`、有 Agent Brief | 通过测试的代码 / 科研成果 + 回归测试 |
| 4 审查 | 代码已推送、Issue 状态为 `in-review` | 审查意见 + 通过/修改标记 |
| 5 归档 | Issue 状态为 `done` 或 `wontfix` | 更新的 CONTEXT.md + ADR + .out-of-scope/ + Issue Summary + synthesis.md |

---

## 相关文档

- [[mattpocock-skills]] — Matt Pocock Skills 深度解析
- [[Project_Workflow_Model]] — 宏观项目工作流
- [[Research_Workflow_Model]] — 宏观科研工作流
- [[Document_Workflow_Model]] — 宏观文档工作流

---

*设计完成时间：2026-05-01 | 基于 Matt Pocock Skills 设计模式适配 | 经两轮 Codex 审查*
