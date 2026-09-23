---
layout: default
title: 明天试着更新自己的博客
---

# 明天试着更新自己的博客

## 第一步：改一句话

打开 [首页源码](https://github.com/01w-01/01w-pages-test/blob/main/index.md)，登录后点击编辑按钮，修改首页的一句话，然后提交到 `main`。

去仓库的 **Actions** 页面查看发布进度。成功后重新打开首页，必要时刷新浏览器。

## 第二步：新增一篇文章

在 GitHub 仓库中选择 **Add file → Create new file**，文件名填 `my-first-note.md`，内容可以照着写：

```markdown
---
layout: default
title: 我的第一条笔记
---

# 我的第一条笔记

今天我学会了用 Markdown 发布网页。
```

提交后，这篇文章会出现在本站的 `my-first-note.html` 地址。

**这个最小样例不会自动列出新文章**：还要编辑 `index.md`，添加链接。在首页 Markdown 中这样写即可：

```markdown
[我的第一条笔记](my-first-note.html)
```

## 第三步：理解发布链路

```text
编辑 .md → Git 提交 → 推送到 main
                        ↓
                  GitHub 自动构建
                        ↓
                  GitHub Pages 发布
```

VPS 或你的电脑只负责编辑和推送；访客打开的是 GitHub 托管的网站。

## 以后再考虑什么？

先学会新增、修改、发布和排错。之后再选自动文章列表、独立域名、主题、RSS，或者改用 Hugo / Astro。

目前的文章是普通页面，不是 Jekyll 的 `_posts` 博客文章；这样便于理解。需要日期归档和文章列表时，再引入 `_posts/YYYY-MM-DD-title.md`。

[返回首页]({{ '/' | relative_url }})
