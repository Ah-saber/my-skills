# My Skills

基于 [Matt Pocock Skills](https://github.com/mattpocock/skills) 设计模式构建的迭代工作流技能集合。覆盖从想法到归档的完整微观操作链，同时适配科研和项目两种场景。

## 设计文档

完整协议见 [Iteration Workflow Protocol](doc/iteration-workflow-protocol.md)。

## 工作流程

```
Phase 0         Phase 1              Phase 2     Phase 3        Phase 4    Phase 5
Warmup   →   想法 → Issue     →    状态机   →   实现循环   →   审查   →   归档
```

### Phase 0: 项目预热

首次使用时运行，4 个交互决策 → 生成配置文件和领域术语表。后续每次会话开始时运行，加载上下文、展示 Plan 进度。

```
/warmup
```

### Phase 1: 想法 → Issue

发散找方向，再收敛删不确定性，固化方案，拆成可执行的垂直切片 Issue。

```
想法
  → research-hours（可选，多角色发散找方向）
  → grill-me / grill-with-docs（逐问逐答，收敛）
  → codex（可选，外部意见——challenge 假设或 review 方案）
  → to-my-prd（固化 PRD，信息不足时追问）
  → to-my-issues（垂直拆解为 Issue）
```

### Phase 2: Issue 生命周期

`/triage` 定期扫描，驱动 Issue 状态机流转。Issue 从 unlabeled → needs-triage → ready-for-agent / ready-for-human / wontfix。执行侧状态由人或 Agent 手动变更。

### Phase 3: 实现循环

| 场景 | 方法 |
|------|------|
| 项目实现 | tdd（Red→Green→Refactor） |
| 科研实现 | 上下文驱动，Agent 辅助 |
| Bug | diagnose（6 Phase Gate） |

### Phase 4: 审查

`in-review` 状态触发并行审查（code-reviewer + security-reviewer）。

### Phase 5: 归档

```
archive/
├── adr/                  # 架构决策记录
├── out-of-scope/         # 被拒绝的方向
├── plan-NNNN-xxx/        # 一个 Plan 一个目录
│   ├── prd.md
│   ├── issues/
│   └── synthesis.md      # Plan 级合成
```

Issue done 时追加 Issue Summary（Purpose / Motivation / Approach / Result / Conclusion / Lessons Learned）。最末端 Issue done 时触发 Plan 合成。

## 安装

```bash
# 推荐
npx skills@latest add <path-to-my-skill>

# 手动
cp -r skills/engineering/* ~/.claude/skills/
```

## 技能列表

| 技能 | 来源 | 功能 |
|------|------|------|
| **warmup** | 新建 | 项目 setup（4 决策 → 5 配置 + CONTEXT.md）+ 会话预热（加载上下文、Plan 进度） |
| **research-hours** | 自建 | 学术版 Office Hours——资深 PI 视角研究诊断，发散找方向 |
| **grill-me** | 复用 Matt | 裸质询——逐问逐答，不依赖项目文档 |
| **grill-with-docs** | 复用 Matt | 文档驱动质询——交叉验证 CONTEXT.md + ADR |
| **to-my-prd** | 适配 Matt | 对话上下文 → 结构化 PRD，信息缺失时追问 |
| **to-my-issues** | 适配 Matt | PRD / Plan → 垂直切片 Issue |
| **triage** | 复用 Matt | Issue 状态机管理 |
| **tdd** | 复用 Matt | Red-Green-Refactor 循环 |
| **diagnose** | 复用 Matt | 6 Phase 诊断循环 + Feedback Loop |
| **improve-codebase-architecture** | 复用 Matt | 架构深化——浅模块 → 深模块重组 |
| **caveman** | 新建 | 压缩通信模式——砍 75% token，保留全部技术精度 |

### 外部工具（不随本项目安装）

| 工具 | 来源 | 功能 |
|------|------|------|
| **codex** | openai-codex 插件 | 多 AI 第二意见——review / challenge / consult 三模式，任意阶段可调用 |

## 设计原则

- **平台无关** — 不硬编码 GitHub/飞书/本地，由 warmup 配置驱动
- **耐久性优先** — 描述 interface/behavior/contract，不引用文件路径和行号
- **垂直切片** — 每个 Issue 切穿所有层，偏好薄片
- **约定驱动** — 状态转换由 Agent 按约定判断，不做硬编码门禁
