---
title: Astro 文章目录规范
published: 2026-06-09
tags:
  - Astro
  - 博客
  - 教程
category: 教程
draft: false
---

# Astro 文章目录规范

## 重要注意事项

**文章必须放在 `src/content/posts/` 目录中才会被 Astro 正确识别和构建。**

## 目录结构

```
src/
└── content/
    └── posts/           ← 文章必须放在这里
        ├── 文章1.md
        ├── 文章2.md
        └── 文章3.mdx
```

## 常见错误

### ❌ 错误做法

将文章直接放在 `src/content/` 根目录：

```
src/
└── content/
    ├── 文章1.md         ← 不会被构建
    ├── 文章2.md         ← 不会被构建
    └── posts/
        └── 文章3.md
```

### ✅ 正确做法

所有文章都放在 `src/content/posts/` 目录中：

```
src/
└── content/
    └── posts/
        ├── 文章1.md     ← 会被构建
        ├── 文章2.md     ← 会被构建
        └── 文章3.md     ← 会被构建
```

## Frontmatter 规范

每个文章文件必须包含正确的 frontmatter：

```yaml
---
title: 文章标题
published: 2026-06-09
tags:
  - 标签1
  - 标签2
category: 分类名称
draft: false              # true 表示草稿，不会显示
---
```

### 字段说明

| 字段 | 必填 | 说明 |
|------|------|------|
| `title` | 是 | 文章标题 |
| `published` | 是 | 发布日期（YYYY-MM-DD 格式） |
| `tags` | 否 | 标签数组 |
| `category` | 否 | 分类名称 |
| `draft` | 否 | 是否为草稿（默认 false） |

## 草稿状态

- `draft: true` - 文章为草稿，**不会**被构建和显示
- `draft: false` 或不填 - 文章会正常构建和显示

## 验证方法

构建后检查 `dist/posts/` 目录：

```bash
# 构建项目
pnpm run build

# 检查生成的文章
ls -la dist/posts/
```

如果文章没有出现在 `dist/posts/` 目录中，请检查：

1. 文件是否在 `src/content/posts/` 目录中
2. frontmatter 格式是否正确
3. `draft` 字段是否为 `true`

## 相关链接

- [Astro Content Collections 文档](https://docs.astro.build/en/guides/content-collections/)
- [Firefly 博客主题](https://github.com/CuteLeaf/Firefly)
