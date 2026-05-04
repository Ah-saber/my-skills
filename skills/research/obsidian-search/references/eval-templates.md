# Eval Query Templates

obsidian eval 代码模板库。所有模板必须用 IIFE 包裹，所有命令指定 `vault="MyNote_Obs"`。

---

## 1. 基础骨架

所有结构化查询的起点。替换"筛选条件区"实现不同查询。

```javascript
(() => {
  const files = app.vault.getMarkdownFiles();
  const results = [];
  for (const f of files) {
    const cache = app.metadataCache.getFileCache(f);
    const fm = cache?.frontmatter || {};
    const allTags = [
      ...(Array.isArray(fm.tags) ? fm.tags : []),
      ...(Array.isArray(fm.tag) ? fm.tag : []),
      ...(fm.tags && !Array.isArray(fm.tags) ? [fm.tags] : []),
      ...(fm.tag && !Array.isArray(fm.tag) ? [fm.tag] : [])
    ];
    // === 筛选条件区 ===
    if (true) {
      results.push({
        path: f.path,
        name: f.basename,
        tags: allTags,
        status: fm.status || '',
        category: fm.category || '',
        date: fm.date || '',
        mtime: f.stat.mtime
      });
    }
  }
  return JSON.stringify({total: results.length, results: results.slice(0, 50)}, null, 2);
})()
```

---

## 2. 筛选条件片段

插入骨架的"筛选条件区"，可自由组合。

### 标签匹配

```javascript
// 精确匹配
allTags.includes('CV/Low-Level/Infrared/NUC')

// 层级前缀（匹配所有子标签）
allTags.some(t => String(t).startsWith('CV/Low-Level'))

// 多标签 OR
allTags.some(t => ['Diffusion', 'Flow'].some(k => String(t).includes(k)))

// 排除标签
!allTags.some(t => String(t).includes('GAN'))
```

### 属性筛选

```javascript
// 精确值
fm.status === 'reading'

// 任意属性（不硬编码属性名）
fm['属性名'] === '值'

// 属性存在性
fm.status !== undefined

// 模糊匹配
String(fm.category).includes('恢复')
```

### 路径筛选

```javascript
// 目录包含
f.path.startsWith('科研/CV')

// 排除目录
!f.path.includes('IDEA') && !f.path.includes('模板')

// 多路径 OR
f.path.startsWith('科研/CV') || f.path.startsWith('科研/Math')
```

### 时间范围

```javascript
// 最近 N 天修改
(Date.now() - f.stat.mtime) < 30 * 86400000

// 日期属性范围
fm.date >= '2026-01-01' && fm.date <= '2026-03-31'
```

### 组合逻辑

```javascript
// AND
condition1 && condition2

// OR
condition1 || condition2

// NOT
!condition
```

---

## 3. 排序片段

插入骨架的 `return` 语句之前。

```javascript
// 修改时间降序
results.sort((a, b) => (b.mtime || 0) - (a.mtime || 0));

// 日期属性降序
results.sort((a, b) => String(b.date || '').localeCompare(String(a.date || '')));

// 标签数降序
results.sort((a, b) => (b.tags?.length || 0) - (a.tags?.length || 0));
```

---

## 4. 链接查询模板

### 4.1 入链查询（谁链接到目标笔记）

```javascript
(() => {
  const targetPath = '路径/文件名.md';
  const target = app.vault.getAbstractFileByPath(targetPath);
  if (!target) return JSON.stringify({error: 'file not found', path: targetPath});
  const bl = app.metadataCache.getBacklinksForFile(target);
  const results = bl ? Array.from(bl.data.entries())
    .map(([path, links]) => ({path, count: links.length}))
    .sort((a, b) => b.count - a.count) : [];
  return JSON.stringify({target: targetPath, total: results.length, results: results.slice(0, 30)}, null, 2);
})()
```

### 4.2 出链查询（目标笔记链接到谁）

```javascript
(() => {
  const targetPath = '路径/文件名.md';
  const f = app.vault.getAbstractFileByPath(targetPath);
  if (!f) return JSON.stringify({error: 'file not found', path: targetPath});
  const cache = app.metadataCache.getFileCache(f);
  const links = (cache?.links || []).map(l => ({link: l.link, display: l.displayText}));
  const embeds = (cache?.embeds || []).map(e => ({link: e.link, display: e.displayText}));
  return JSON.stringify({
    target: targetPath,
    links: {total: links.length, items: links.slice(0, 30)},
    embeds: {total: embeds.length, items: embeds.slice(0, 10)}
  }, null, 2);
})()
```

### 4.3 双向链接概览

```javascript
(() => {
  const targetPath = '路径/文件名.md';
  const f = app.vault.getAbstractFileByPath(targetPath);
  if (!f) return JSON.stringify({error: 'file not found'});
  const cache = app.metadataCache.getFileCache(f);
  const outCount = (cache?.links || []).length;
  const bl = app.metadataCache.getBacklinksForFile(f);
  const inCount = bl ? bl.data.size : 0;
  const inFiles = bl ? Array.from(bl.data.keys()).slice(0, 10) : [];
  const outFiles = (cache?.links || []).map(l => l.link).slice(0, 10);
  return JSON.stringify({
    path: targetPath,
    backlinks: {count: inCount, files: inFiles},
    outlinks: {count: outCount, files: outFiles}
  }, null, 2);
})()
```

### 4.4 N 跳图谱遍历

从起点出发，沿双向链接关系遍历指定跳数，构建知识图谱。

```javascript
(() => {
  const startPath = '路径/起点.md';
  const maxHops = 2;
  const visited = new Set();
  const frontier = [startPath];
  const nodes = {};
  const edges = [];
  for (let hop = 0; hop < maxHops && frontier.length > 0; hop++) {
    const next = [];
    for (const p of frontier) {
      if (visited.has(p)) continue;
      visited.add(p);
      const f = app.vault.getAbstractFileByPath(p);
      if (!f) continue;
      const cache = app.metadataCache.getFileCache(f);
      // 出链
      const outLinks = (cache?.links || []).map(l => l.link);
      for (const ol of outLinks) {
        edges.push({from: p, to: ol, direction: 'out'});
        if (!visited.has(ol)) next.push(ol);
      }
      // 入链
      const bl = app.metadataCache.getBacklinksForFile(f);
      if (bl) {
        for (const [src] of bl.data) {
          edges.push({from: src, to: p, direction: 'in'});
          if (!visited.has(src)) next.push(src);
        }
      }
      nodes[p] = {hop, outDegree: outLinks.length, inDegree: bl ? bl.data.size : 0};
    }
    frontier.length = 0;
    frontier.push(...next);
  }
  return JSON.stringify({
    start: startPath,
    nodeCount: Object.keys(nodes).length,
    edgeCount: edges.length,
    nodes,
    edges: edges.slice(0, 100)
  }, null, 2);
})()
```

---

## 5. 完整示例

### 标签 + 路径 + 状态组合查询

```javascript
(() => {
  const files = app.vault.getMarkdownFiles();
  const results = [];
  for (const f of files) {
    if (!f.path.startsWith('科研/CV')) continue;
    const cache = app.metadataCache.getFileCache(f);
    const fm = cache?.frontmatter || {};
    const allTags = [
      ...(Array.isArray(fm.tags) ? fm.tags : fm.tags ? [fm.tags] : []),
      ...(Array.isArray(fm.tag) ? fm.tag : fm.tag ? [fm.tag] : [])
    ];
    if (allTags.some(t => String(t).includes('Diffusion')) && fm.status === 'reading') {
      results.push({path: f.path, name: f.basename, tags: allTags, status: fm.status});
    }
  }
  return JSON.stringify({total: results.length, results: results.slice(0, 30)}, null, 2);
})()
```

### 最近修改的笔记

```javascript
(() => {
  const files = app.vault.getMarkdownFiles();
  const results = [];
  const cutoff = Date.now() - 30 * 86400000;
  for (const f of files) {
    if (f.stat.mtime < cutoff) continue;
    const cache = app.metadataCache.getFileCache(f);
    const fm = cache?.frontmatter || {};
    results.push({path: f.path, name: f.basename, mtime: f.stat.mtime, status: fm.status || ''});
  }
  results.sort((a, b) => b.mtime - a.mtime);
  return JSON.stringify({total: results.length, results: results.slice(0, 20)}, null, 2);
})()
```
