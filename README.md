# Markdown → GitHub Pages 最小练习

网站：**https://01w-01.github.io/01w-pages-test/**

本仓库公开，只存放演示内容。使用 GitHub Pages 内置的 Jekyll 构建，无需本地安装 Ruby、Jekyll 或 Node.js。

## 文件导航

| 文件 | 作用 |
| --- | --- |
| `index.md` | 首页和手动维护的文章链接 |
| `hello.md` | Markdown 格式演示 |
| `next-step.md` | 新增、修改文章的练习 |
| `_config.yml` | 网站标题、主题、URL 与项目子路径 |
| `README.md` | 本说明；排除在网站构建之外 |

## 这次怎么搭的

1. 创建公开仓库 `01w-01/01w-pages-test`。
2. 写入上面的文件，为页面添加 Front Matter（开头两行 `---` 之间的 YAML）。
3. 将文件提交并推送到 `main` 分支。
4. 在 **Settings → Pages → Build and deployment** 中选择 **Deploy from a branch**，分支 `main`、目录 `/ (root)`，保存。本次通过 GitHub API 设置，效果相同。
5. GitHub 自动使用 Jekyll 构建、发布；到 **Actions** 查看 `pages build and deployment`。
6. 打开网站并检查首页、文章、主题样式与内部链接。

这种模式不需要自己编写 `.github/workflows/`。GitHub 仍会显示自动生成的 Pages 部署工作流。

## 明天先做一个小练习

直接在 GitHub 网页编辑 `index.md`，改一句话并提交。等 Actions 成功，刷新网站验证。然后照着网站第二篇文章新增 `my-first-note.md` 并添加首页链接。

也可在本地克隆仓库后编辑。以下命令在 **VPS 的仓库目录**运行；Windows PowerShell 安装 Git 并克隆后也可使用相同 Git 命令：

```bash
git pull --ff-only
# 编辑 index.md 后：
git diff
git add index.md
git commit -m "docs: 更新首页文字"
git push origin main
```

网页和本地交替编辑时，先 `git pull --ff-only`，避免遗漏网页上的新提交。

上面的命令假设 Git 作者身份和推送认证已配置。本次 VPS 未配置默认作者和 HTTPS 凭据助手，因此发布时使用命令级 `git -c user.name=... -c user.email=... commit ...`（GitHub noreply 邮箱），以及 `git -c credential.helper= -c 'credential.helper=!gh auth git-credential' push -u origin main`。没有修改全局 Git 配置，也没有把登录凭据写进仓库。明天第一次练习建议直接用 GitHub 网页编辑，最省事。

## 为什么配置 baseurl？

这是项目站点，不是账号根站点：

- `url`: `https://01w-01.github.io`
- `baseurl`: `/01w-pages-test`

模板链接使用 `relative_url`，会自动补上项目子路径。例如首页里的文章链接写成：

```liquid
[文章]({{ '/hello.html' | relative_url }})
```

否则直接写 `/hello.html` 会指向账号根目录，容易 404。

## 出问题去哪里看？

- **首次 404**：先看 Actions 是否完成，再看 Settings → Pages 给出的网址。首次发布或缓存更新可能需要几分钟，偶尔更久。
- **构建失败**：打开失败的 Actions 运行，展开红色步骤看日志；先检查 YAML 缩进和 Front Matter。
- **文章有地址，但首页没显示**：这个样例手动维护目录，新文章不会自动加入首页。
- **样式或链接 404**：检查 `_config.yml` 的 `baseurl`、路径大小写和输出扩展名 `.html`。
- **看到旧内容**：确认提交已推送且对应部署成功，再强制刷新。

网页能通过网址访问，不意味着搜索引擎已经收录。

## Jekyll 可以换吗？

可以。GitHub Pages 最终托管的是静态文件，并不强制使用 Jekyll。

| 选择 | 适合的方向 | 相比当前测试多出的工作 |
| --- | --- | --- |
| Jekyll 内置构建 | 最小 Markdown 网站、传统博客 | 当前已够用 |
| Hugo | 内容型博客、快速构建 | 选主题，配置 Hugo 和构建工作流 |
| Astro | 个性化博客、组件与页面设计 | Node.js 依赖和构建工作流 |
| VitePress | 文档、知识库 | Node.js 依赖、导航与构建工作流 |

迁移时正文通常能复用；Front Matter、模板语法和链接可能需要调整。本轮不配置域名、评论、统计或自动文章列表。

## 如何停止发布？

在 **Settings → Pages** 中使用 **Unpublish site**（若界面提供），或将发布来源的分支设为 **None**。保留仓库即可保留学习记录，无需删除历史。
