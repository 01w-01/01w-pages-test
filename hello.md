---
layout: default
title: 只写 Markdown，也能拥有网页
---

# 只写 Markdown，也能拥有网页

这是第一篇测试文章。它不是在网页编辑器里排出来的，而是由一个普通的 Markdown 文件生成的。

## 试试常用格式

这是**加粗**，这是*斜体*，这是 `行内代码`。

- 写下一个想法
- 保存成 Markdown
- 提交到 GitHub
- 等待网站更新

> 不必先把博客系统研究透，先发布一小段文字就很好。

### 代码块

```python
print("Hello, blog!")
```

### 表格

| 文件 | 作用 |
| --- | --- |
| `index.md` | 首页 |
| `hello.md` | 当前文章 |
| `_config.yml` | 标题、主题与网址配置 |

## 文件开头的三条横线是什么？

它们包住一小段 YAML 元信息，叫做 **Front Matter**。在这个样例里，它告诉 Jekyll 使用默认布局，以及页面叫什么。

```yaml
---
layout: default
title: 我的文章标题
---
```

文章正文写在第二条 `---` 后面即可。

[返回首页]({{ '/' | relative_url }}) · [下一篇：动手更新]({{ '/next-step.html' | relative_url }})
