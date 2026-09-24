---
layout: default
title: 从命令到网页：本次搭建过程
---

# 从命令到网页：本次搭建过程

这份说明还原本次测试站的搭建步骤，重点是：**命令做了什么、GitHub 做了什么，以及它们如何连接起来。**

> 命令环境：VPS 上的 Bash。仓库已经创建，下面用于学习，不要从头重复执行。文中的用户、仓库名均使用本次真实名称；没有包含令牌。

## 目录

1. [先看全貌](#overview)
2. [确认登录](#login)
3. [创建远程仓库并克隆](#create)
4. [准备网站文件](#files)
5. [提交与推送](#git)
6. [通过 API 开启 Pages](#pages)
7. [GitHub 如何构建网站](#build)
8. [查看部署与验证网页](#verify)
9. [以后如何更新](#update)
10. [这次遇到的问题](#troubleshooting)

---

<a id="overview"></a>
## 1. 先看全貌

### 三个地方，各有职责

| 位置 | 职责 | 本次对应 |
| --- | --- | --- |
| VPS 工作目录 | 编辑文件、执行 Git 与 GitHub CLI | `/home/ubuntu/projects/01w-pages-test` |
| GitHub 仓库 | 保存源文件与提交历史 | `01w-01/01w-pages-test` |
| GitHub Pages | 对外提供构建后的静态网页 | `https://01w-01.github.io/01w-pages-test/` |

### 完整链路

```text
VPS：创建 .md 和配置文件
          │
          │ git add / git commit
          ▼
VPS：生成本地提交
          │
          │ git push
          ▼
GitHub：main 分支收到提交
          │
          │ Pages 自动触发构建
          ▼
GitHub：Jekyll 生成 HTML、CSS 等静态文件
          │
          │ 部署构建产物
          ▼
访客：通过 Pages 网址访问网站
```

**Git 管版本，`gh` 管 GitHub 功能，Jekyll 生成网页，Pages 发布网页。**

VPS 不是网站服务器。本次没有安装 Nginx、没有开放端口，也没有在 VPS 上启动常驻网站进程。

<a id="login"></a>
## 2. 确认 GitHub 登录

```bash
gh auth status
```

`gh` 是 GitHub 官方命令行工具。`auth status` 用于检查当前登录账号和认证状态。

本次 VPS 已登录 `01w-01`，因此可以直接创建仓库和调用 GitHub API。

如果换到尚未登录的电脑，通常需要先执行：

```bash
gh auth login
```

然后按提示完成授权。本次没有重新登录，也没有把令牌复制到代码或文档中。

> `gh` 已登录，不一定代表普通 `git push` 已经配置好认证。本次就遇到了这种情况，后面会解释。

<a id="create"></a>
## 3. 创建远程仓库，并克隆到 VPS

### 本次执行的命令

在 `/home/ubuntu/projects` 目录下执行：

```bash
gh repo create 01w-01/01w-pages-test \
  --public \
  --description 'GitHub Pages 与 Markdown 博客发布流程练习' \
  --clone
```

反斜线 `\` 是 Bash 的续行符，只是把一条长命令分成多行；它后面不要再加空格。

### 参数逐个看

| 部分 | 含义 |
| --- | --- |
| `gh repo create` | 在 GitHub 上创建仓库 |
| `01w-01/01w-pages-test` | 所有者 / 仓库名 |
| `--public` | 创建公开仓库，适用于免费账号的 Pages 测试 |
| `--description '…'` | 设置仓库简介 |
| `--clone` | 创建后立即克隆到当前目录 |

执行后得到两个东西：

```text
远程：https://github.com/01w-01/01w-pages-test
本地：/home/ubuntu/projects/01w-pages-test
```

克隆还会配置名为 `origin` 的远程地址。可以查看：

```bash
cd /home/ubuntu/projects/01w-pages-test
git remote -v
```

**创建仓库不会自动开启 Pages。** 此时它只是一个保存文件的 Git 仓库，还需要添加内容和配置发布来源。

<a id="files"></a>
## 4. 准备网站文件

### 初始目录

```text
01w-pages-test/
├── _config.yml       Jekyll 网站配置
├── index.md          首页
├── hello.md          Markdown 格式示例
├── next-step.md      后续练习
└── README.md         GitHub 仓库说明，不发布到站点
```

本次由编辑工具直接创建这些文本文件，**不是通过某个脚手架生成**。你也可以使用任意文本编辑器创建同名文件，效果一样。

没有执行 `npm install`，也没有安装本地 Jekyll。

### `_config.yml`：告诉 Jekyll 怎么生成网站

```yaml
title: 01w 的博客试验田
description: 从几个 Markdown 文件开始，练习一次完整的网站发布。
lang: zh-CN
theme: jekyll-theme-cayman
url: https://01w-01.github.io
baseurl: /01w-pages-test
exclude:
  - README.md
```

| 配置 | 作用 |
| --- | --- |
| `title`、`description` | 网站标题和简介，主题可以读取它们 |
| `lang` | 声明网站语言；具体输出取决于主题 |
| `theme` | 使用 GitHub Pages 支持的 Cayman 主题 |
| `url` | 网站域名部分，不包含项目路径 |
| `baseurl` | 项目站点的路径前缀 |
| `exclude` | 不纳入网站输出的文件 |

本次网址由两部分组成：

```text
https://01w-01.github.io  +  /01w-pages-test/
        账号域名                 仓库路径
```

因此 `baseurl` 不能随意删掉，否则部分链接和样式路径可能指向错误位置。

### `.md`：正文和页面元信息

最小页面可以这样写：

```markdown
---
layout: default
title: 我的页面
---

# 我的页面

这是一段 **Markdown** 正文。
```

开头两条 `---` 之间的内容叫 **Front Matter**：

- `layout: default`：使用主题提供的默认布局。
- `title`：设置页面标题元信息。

下面才是文章正文。正文里的 `#` 是一级标题，`**文字**` 表示加粗。

Jekyll 在本次配置下会把 `hello.md` 输出为 `hello.html`，`index.md` 输出为网站首页 `index.html`。

### 为什么页面链接不是 `.md`？

访客访问的是构建后的网页，因此链接指向 `.html`。

首页使用的写法是：

{% raw %}
```liquid
[第一篇文章]({{ '/hello.html' | relative_url }})
```
{% endraw %}

其中 `relative_url` 是 Jekyll 的模板过滤器，会根据 `baseurl` 补上项目路径。最终链接是：

```text
/01w-pages-test/hello.html
```

这样就不会误跳到账号域名根目录下的 `/hello.html`。

<a id="git"></a>
## 5. 提交文件，并推送到 GitHub

### 5.1 创建 `main` 分支

在新克隆的空仓库里执行：

```bash
git switch -c main
```

`switch` 切换分支，`-c` 表示新建分支。本次选择 `main` 作为源代码和 Pages 发布来源。

已有 `main` 的仓库不需要重复创建。

### 5.2 将文件加入暂存区

```bash
git add _config.yml index.md hello.md next-step.md README.md
git diff --cached --stat
```

| 命令 | 作用 |
| --- | --- |
| `git add …` | 选择哪些文件的改动进入下一次提交 |
| `git diff --cached --stat` | 查看已暂存改动的文件及行数摘要 |

暂存不等于提交，更不等于上传。

### 5.3 创建本地提交

通常使用：

```bash
git commit -m "feat: 创建 Markdown 博客与 Pages 入门样例"
```

`-m` 后面是提交说明。提交会生成一个有唯一编号的版本记录，但仍只在本地。

本次 VPS 没有默认 Git 作者身份，所以最初执行失败。最终使用了命令级配置：

```bash
USER_ID=$(gh api user --jq .id)

git -c user.name=01w-01 \
  -c user.email="${USER_ID}+01w-01@users.noreply.github.com" \
  commit -m "feat: 创建 Markdown 博客与 Pages 入门样例"
```

这里：

| 部分 | 含义 |
| --- | --- |
| `gh api user` | 查询当前登录的 GitHub 用户信息 |
| `--jq .id` | 从返回的 JSON 中取出数字用户 ID |
| `$(…)` | Bash 执行括号内命令，并取其输出 |
| `USER_ID=…` | 把结果保存到当前 Shell 变量 |
| `git -c 名称=值` | 只为这次 Git 命令提供配置 |
| `…@users.noreply.github.com` | 使用 GitHub noreply 邮箱，不暴露私人邮箱 |

**作者身份是提交署名，不是登录凭据。** 本次没有为方便提交而修改全局 Git 配置。

### 5.4 推送到 GitHub

一般情况下：

```bash
git push -u origin main
```

| 部分 | 含义 |
| --- | --- |
| `push` | 将本地提交发送到远程仓库 |
| `origin` | 克隆时设置的远程名称 |
| `main` | 要推送的分支 |
| `-u` | 设置跟踪关系，之后可简写为 `git push` |

本次普通推送未能读取 HTTPS 登录身份，因此改成：

```bash
git -c credential.helper= \
  -c 'credential.helper=!gh auth git-credential' \
  push -u origin main
```

第一项清空本次命令继承的凭据助手列表，第二项让 Git 通过已登录的 `gh` 获取认证。`!` 表示这个助手是 Shell 命令；外层单引号将它作为一个完整参数传入。

这只是复用现有授权，不会把令牌写进仓库，也没有永久修改凭据助手设置。

<a id="pages"></a>
## 6. 通过 API 开启 GitHub Pages

### 本次执行的命令

推送 `main` 后执行：

```bash
gh api --method POST repos/01w-01/01w-pages-test/pages \
  -f build_type=legacy \
  -f 'source[branch]=main' \
  -f 'source[path]=/' \
  --jq '{html_url,build_type,source,status}'
```

### 命令拆解

| 部分 | 含义 |
| --- | --- |
| `gh api` | 用当前 GitHub 登录身份调用 API |
| `--method POST` | 发送创建请求 |
| `repos/…/pages` | 这个仓库的 Pages API 端点 |
| `-f` | 向请求加入一个字符串字段 |
| `build_type=legacy` | 选择从分支发布的内置构建方式 |
| `source[branch]=main` | 发布源分支为 `main` |
| `source[path]=/` | 使用仓库根目录，而不是 `/docs` |
| `--jq '{…}'` | 只显示响应中关心的字段，不改变设置 |

`legacy` 是 API 对该模式的名称，不是要求安装旧软件。

### 等价的网页操作

```text
仓库 Settings
  → Pages
    → Build and deployment
      → Source: Deploy from a branch
      → Branch: main
      → Folder: / (root)
      → Save
```

也就是说，**命令行只是替你调用 GitHub 接口完成设置，并没有另一套神秘的部署机制。**

这个 `POST` 用于首次启用。仓库已经开启 Pages 后，不需要每次写文章都再执行一次。

只读检查当前配置可以用：

```bash
gh api repos/01w-01/01w-pages-test/pages \
  --jq '{html_url,build_type,source,status}'
```

<a id="build"></a>
## 7. GitHub 到底如何构建网站？

### 开启 Pages 后，GitHub 接手后续工作

本次使用“从分支发布”，没有手写 `.github/workflows/*.yml`。GitHub 自动提供 Pages 构建部署流程，在 **Actions** 中可以看到 `pages-build-deployment`。

它的核心步骤是：

```text
Checkout
  取出仓库中的指定提交
       ↓
Build with Jekyll
  读取 _config.yml、Markdown、主题
  把 Markdown 转成 HTML，处理布局和模板
       ↓
Upload artifact
  保存生成的静态站点文件，供部署步骤使用
       ↓
Deploy to GitHub Pages
  将构建产物发布为可访问的网站
```

### 构建前后有什么不同？

```text
源文件（Git 仓库）          构建产物（示意）

index.md            →     index.html
hello.md            →     hello.html
next-step.md        →     next-step.html
_config.yml         →     控制构建过程，不是文章页面
主题资源             →     assets/css/style.css 等
```

Jekyll 通常把产物放在 `_site` 目录中。这里构建发生在 GitHub 的运行环境里，**不是在 VPS 的工作目录中**。

生成的 HTML 不会自动提交回 `main`；它通过构建产物和部署流程交给 Pages。

### 主题从哪里来？

`jekyll-theme-cayman` 是 GitHub Pages 支持的主题。GitHub 的 Jekyll 构建环境可以使用它，我们只需在 `_config.yml` 中指定名称，无需把整套主题复制到仓库。

### 为什么下一次推送也会更新网站？

因为已经把 Pages 的来源设成 `main` 根目录。后续提交进入该来源分支，就会触发新的构建部署。

网站是**构建完成后才更新**，不是 `git push` 一结束就立即变化。若新构建失败，通常仍会保留上一次成功发布的网站。

<a id="verify"></a>
## 8. 查看部署进度，并验证网页

### 8.1 列出最近的运行记录

```bash
gh run list \
  --repo 01w-01/01w-pages-test \
  --limit 3 \
  --json databaseId,status,conclusion,url
```

| 字段 | 怎么看 |
| --- | --- |
| `databaseId` | 本次运行编号，用于查询或等待 |
| `status` | 如 `queued`、`in_progress`、`completed` |
| `conclusion` | 完成后的结果，如 `success`、`failure`、`cancelled` |
| `url` | GitHub 网页上的运行详情地址 |

### 8.2 等待指定运行结束

下面编号是本次成功部署的历史编号；以后要换成新运行的编号：

```bash
gh run watch 35903080320 \
  --repo 01w-01/01w-pages-test \
  --exit-status \
  --interval 10
```

`--interval 10` 表示每 10 秒刷新一次；`--exit-status` 会在运行失败时返回非零退出码，便于脚本判断。

这条命令是在观察现有任务，**不是启动构建**。退出观察也不等于取消 GitHub 上的部署。

如果失败，可查看失败步骤日志：

```bash
gh run view 运行编号 \
  --repo 01w-01/01w-pages-test \
  --log-failed
```

请把 `运行编号` 替换为实际数字。

### 8.3 检查公网地址

快速检查 HTTP 状态：

```bash
curl -I https://01w-01.github.io/01w-pages-test/
curl -I https://01w-01.github.io/01w-pages-test/hello.html
```

`-I` 只获取响应头。返回 `200` 表示地址成功响应，但不能单凭它确定正文和布局都正确。

本次实际使用 Python 发起 GET 请求，还检查了：

| 检查对象 | 验证内容 |
| --- | --- |
| 首页、两篇文章 | HTTP 200，包含预期文字 |
| 页面里的模板 | 没有残留未展开的模板表达式 |
| 站内绝对路径 | 包含 `/01w-pages-test/` 前缀 |
| 主题 CSS | 能成功获取，HTTP 200 |

这些是程序检查，不等于真实手机或浏览器的视觉验收；你打开网站观察排版，就是最后一环。

<a id="update"></a>
## 9. 以后怎么更新？

### 最简单：GitHub 网页编辑

```text
打开仓库中的 .md 文件
  → 点击编辑
  → 修改正文
  → 提交到 main
  → 等 Actions 成功
  → 刷新网站
```

无需再次创建仓库、开启 Pages 或安装 Jekyll。

### 使用 VPS 编辑

先同步，再编辑，再提交：

```bash
cd /home/ubuntu/projects/01w-pages-test
git pull --ff-only

# 用你喜欢的编辑器修改 index.md

git diff
git add index.md
git commit -m "docs: 更新首页内容"
git push
```

这里的普通 `commit` 和 `push` 假设作者及认证已经配置。如果 VPS 仍保持本次原状，就使用第 5 节的命令级 `-c` 写法；拉取若需要认证，也可使用相同凭据助手参数。

`--ff-only` 要求拉取只能快进更新；若本地和远程已经分叉，会停下来，而不是自动生成合并提交。

### 新增文章，还要更新首页链接

本次采用普通 Markdown 页面，没有自动文章列表。新增 `note.md` 后，别忘了在 `index.md` 添加通往 `note.html` 的链接。

<a id="troubleshooting"></a>
## 10. 本次实际遇到了什么？

| 现象 | 原因 | 处理 |
| --- | --- | --- |
| `Author identity unknown` | Git 没有默认提交作者 | 使用命令级 `user.name` 和 noreply 邮箱 |
| 推送无法读取 Username | 普通 Git 未接入现有 `gh` 认证 | 命令级指定 `gh auth git-credential` |
| 第一次构建被取消 | 补充说明后又推送了新提交，新运行取代旧运行 | 等待最新提交对应的部署，并确认成功 |

第一次取消不代表 Jekyll 配置错误。应查看运行注释和最新运行，区分“被新任务替代”和“真正构建失败”。

本次成功运行中，构建约 22 秒、部署约 29 秒；总耗时还包含排队、初始化等，所以以后不能保证每次相同。

---

## 最后记住四件事

1. **仓库是源文件，Pages 是发布服务**，两者不是同一个东西。
2. **Jekyll 负责把 Markdown 变成网站**，本次在 GitHub 上运行。
3. **Pages 来源只需设置一次**，以后主要是编辑、提交、推送。
4. **成功推送不等于成功发布**，要看 Actions 和实际网页。

[返回首页]({{ '/' | relative_url }}) · [查看仓库](https://github.com/01w-01/01w-pages-test)
