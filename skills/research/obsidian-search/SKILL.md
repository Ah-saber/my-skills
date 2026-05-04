---
name: obsidian-search
description: Obsidian Vault 笔记搜索技能。支持全文搜索、标签/属性/路径组合筛选、链接关系查询与图谱遍历、语义扩展搜索。当用户说"搜索笔记"、"找笔记"、"查看CV目录"、"有哪些Diffusion相关的"、"reading状态的论文"、"最近修改的笔记"、"谁链接到这篇笔记"、"和XX相关的笔记"、"笔记关联"，或任何需要在 Obsidian Vault 中查找笔记或探索笔记关联的场景时，必须使用此技能。
---

# Obsidian Vault 搜索技能

在 Obsidian Vault 中搜索笔记。支持全文搜索、结构化筛选、链接关系查询和语义搜索。

所有命令必须指定 `vault="MyNote_Obs"`。

---

## 决策树

根据用户意图选择引擎：

| 用户意图 | 引擎 | 命令模式 |
|---------|------|---------|
| 关键词明确的检索 | 全文搜索 | `obsidian search` |
| 按标签/属性/路径/状态 | 结构化查询 | `obsidian eval` |
| 多维度组合条件 | 结构化查询 | `obsidian eval` |
| 入链/出链/图谱遍历 | 链接查询 | `obsidian eval` |
| 模糊概念描述 | 语义搜索 | LLM展开 + `obsidian eval` |
| 标签或属性概览 | 统计查询 | `obsidian tags/properties` |

不确定时，优先用 `obsidian eval`（最灵活）。

---

## 1. 全文搜索

关键词明确的简单检索用 `obsidian search`：

```bash
obsidian vault="MyNote_Obs" search query="关键词" path="子路径" limit=10
```

- `query` — 搜索关键词
- `path` — 限定搜索范围（可选）
- `limit` — 返回数量限制

---

## 2. 结构化搜索（obsidian eval）

按标签、属性、路径等条件筛选。使用 eval 执行 JavaScript，访问 `metadataCache` API。

完整代码模板见 `references/eval-templates.md`。

### 基本语法

```bash
obsidian vault="MyNote_Obs" eval code="<IIFE代码>"
```

代码必须用 IIFE 包裹：`(() => { ... })()`

### 快速构造流程

1. 从 `references/eval-templates.md` 读取基础骨架
2. 替换"筛选条件区"为对应的条件片段
3. 按需添加排序
4. 拼接为完整命令执行

### 标签搜索

标签体系遵循层级规范（参考 `tags_database.md`），6大类：CV(任务)、Method(方法)、Math(理论)、Venue(场所)、Domain(领域)。

```javascript
// 精确匹配
allTags.includes('CV/Low-Level/Infrared/NUC')

// 层级前缀（匹配该层级下所有子标签）
allTags.some(t => String(t).startsWith('CV/Low-Level'))

// 多标签 OR
allTags.some(t => ['Diffusion', 'Flow'].some(k => String(t).includes(k)))
```

也可用 CLI 快速查看标签统计：

```bash
obsidian vault="MyNote_Obs" tags counts sort=count
```

### 属性筛选

不硬编码属性名。用户可查询任意 frontmatter 属性。

```javascript
// 精确值
fm.status === 'reading'

// 任意属性
fm['属性名'] === '值'

// 属性存在性
fm.status !== undefined

// 模糊匹配
String(fm.category).includes('恢复')
```

查看属性统计：

```bash
obsidian vault="MyNote_Obs" properties counts sort=count
```

### 路径筛选

```javascript
// 目录包含
f.path.startsWith('科研/CV')

// 排除特定目录
!f.path.includes('IDEA') && !f.path.includes('模板')

// 多路径 OR
f.path.startsWith('科研/CV') || f.path.startsWith('科研/Math')
```

浏览目录用 CLI：

```bash
obsidian vault="MyNote_Obs" files folder="科研/CV" ext=md
```

### 时间范围

```javascript
// 最近 N 天修改
(Date.now() - f.stat.mtime) < 30 * 86400000

// 日期属性范围
fm.date >= '2026-01-01' && fm.date <= '2026-03-31'
```

### 组合查询

条件片段可自由组合：

```javascript
// AND
f.path.startsWith('科研/CV') && fm.status === 'reading' && allTags.some(t => String(t).includes('Diffusion'))

// OR
fm.status === 'reading' || fm.status === 'done'

// NOT
!f.path.includes('模板') && !allTags.some(t => String(t).includes('GAN'))
```

### 关键注意事项

- tag 字段可能是字符串或数组，骨架已做兼容处理
- 结果默认限制 50 条，可调整 `slice(0, N)`
- 排序在 `return` 之前添加

---

## 3. 链接关系查询

查询笔记间的链接关系。当前 vault 的 wikilink 较少，但此功能为未来双链网络预留。

完整代码模板见 `references/eval-templates.md` 第 4 节。

### 入链查询（谁链接到目标笔记）

```javascript
(() => {
  const target = app.vault.getAbstractFileByPath('路径/文件名.md');
  if (!target) return JSON.stringify({error: 'file not found'});
  const bl = app.metadataCache.getBacklinksForFile(target);
  const results = bl ? Array.from(bl.data.entries())
    .map(([path, links]) => ({path, count: links.length})) : [];
  return JSON.stringify({total: results.length, results: results.slice(0, 30)}, null, 2);
})()
```

### 出链查询（目标笔记链接到谁）

```javascript
(() => {
  const f = app.vault.getAbstractFileByPath('路径/文件名.md');
  if (!f) return JSON.stringify({error: 'file not found'});
  const cache = app.metadataCache.getFileCache(f);
  const links = (cache?.links || []).map(l => ({link: l.link, display: l.displayText}));
  const embeds = (cache?.embeds || []).map(e => ({link: e.link, display: e.displayText}));
  return JSON.stringify({
    links: {total: links.length, items: links.slice(0, 30)},
    embeds: {total: embeds.length, items: embeds.slice(0, 10)}
  }, null, 2);
})()
```

### N 跳图谱遍历

从起点出发，沿双向链接关系遍历指定跳数，发现间接关联的知识网络。

模板见 `references/eval-templates.md` 第 4.4 节。参数：
- `startPath` — 起点笔记路径
- `maxHops` — 遍历跳数（建议 1-3）

### 链接查询适用场景

- "哪些笔记引用了这篇论文" → 入链查询
- "这篇笔记关联了什么" → 出链查询
- "和 XX 笔记间接相关的笔记" → N 跳遍历
- 构建知识图谱时 → N 跳遍历，导出 nodes + edges

---

## 4. 语义搜索

当用户给出模糊概念描述时，用 LLM 扩展搜索词后执行结构化查询。

适用场景：
- "图像恢复相关的笔记"
- "生成模型方向有哪些"
- "和扩散采样相关的"

### 执行步骤

1. **分析概念**：从用户描述中提取核心概念
2. **展开搜索词**：
   - 相关关键词（中文 + 英文）
   - 对应标签（参考 tags_database.md）
   - 同义词和缩写
3. **构造 eval 查询**：同时匹配标签、文件名、category 属性
4. **补充全文搜索**：`obsidian search` 作为辅助
5. **合并去重**：按匹配度排序输出

### 展开示例

用户说"图像恢复" → 展开：
- 关键词：image restoration, 图像恢复, 逆问题, inverse problem
- 标签：`CV/Low-Level/Restoration`, `CV/Low-Level/Infrared`, `CV/Generation/Restoration`
- category：图像恢复, 逆问题

构造 eval 查询同时搜索这些维度。

---

## 5. 输出格式

### 默认（简洁卡片）

```
[1] 笔记标题              状态: reading | 标签: #CV/Low-Level #Diffusion
[2] 另一篇笔记            状态: done | 标签: #Method/Generation
共 X 条结果
```

### 链接查询

```
入链 → 笔记A (3个链接), 笔记B (1个链接)
出链 → 笔记C, 笔记D, ![嵌入图片]
共 X 篇关联笔记
```

### 图谱遍历

```
起点: 笔记X (hop 0)
  ├─ 笔记A (hop 1, 出链)
  ├─ 笔记B (hop 1, 入链)
  │   └─ 笔记C (hop 2)
  └─ 笔记D (hop 1)
节点: N, 边: M
```

### 无结果

建议：使用语义搜索扩大范围、放宽筛选条件、检查标签名称。

---

## 6. 快速参考

```bash
# 全文搜索
obsidian vault="MyNote_Obs" search query="关键词" limit=10

# 标签统计
obsidian vault="MyNote_Obs" tags counts sort=count

# 属性统计
obsidian vault="MyNote_Obs" properties counts sort=count

# 目录浏览
obsidian vault="MyNote_Obs" files folder="科研/CV" ext=md

# 结构化查询（替换 code 内容）
obsidian vault="MyNote_Obs" eval code="(() => { ... })()"
```

## 参考资源

- **eval 代码模板**：`references/eval-templates.md`（骨架、条件片段、链接查询、排序）
- **标签规范**：项目 `tags_database.md`（6大类层级标签体系）
- **Obsidian CLI 技能**：`obsidian-cli` skill（CLI 语法参考）
