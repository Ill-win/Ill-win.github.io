# 拾光集

一个用 [Hugo](https://gohugo.io/) 构建的个人博客，主题覆盖 **工作 / 生活 / 技术** 三类内容。纯静态输出，托管在 GitHub Pages 上，不需要任何服务器。

## 功能

- 首页文章列表（三个板块混合按时间排序）+ 分页
- 文章详情页：Markdown 渲染、代码高亮、右侧自动目录（TOC）、上一篇 / 下一篇
- **站内搜索**：纯前端实现，支持标题 / 标签 / 正文全文匹配、结果高亮、URL 可分享
- 标签分类：标签云 + 单标签文章列表
- 关于页
- 深浅色主题切换（跟随系统，可手动切换并记住选择）
- RSS 订阅、SEO / Open Graph 元信息
- 响应式，手机端可用

## 目录结构

```
my-blog/
├── hugo.toml                     # 站点配置（标题、菜单、个人信息都在这里改）
├── DEPLOY.md                     # ← 从零到上线的详细操作手册
├── content/
│   ├── _index.md                 # 首页
│   ├── about.md                  # 关于页
│   ├── search.md                 # 搜索页
│   ├── work/                     # 「工作」板块
│   │   ├── _index.md             # 板块说明
│   │   └── *.md                  # 文章
│   ├── life/                     # 「生活」板块
│   └── tech/                     # 「技术」板块
├── layouts/                      # 模板
│   ├── baseof.html               # 页面骨架
│   ├── home.html                 # 首页
│   ├── home.json                 # 搜索用的 JSON 索引
│   ├── home.rss.xml              # RSS 输出
│   ├── single.html               # 文章详情
│   ├── section.html              # 板块列表页
│   ├── taxonomy.html             # /tags/ 标签总览
│   ├── term.html                 # /tags/xxx/ 单标签
│   ├── search/single.html        # 搜索页（含前端搜索逻辑）
│   └── _partials/                # 可复用片段
├── assets/css/                   # 样式（main.css + 代码高亮配色）
└── .github/workflows/deploy.yml  # 自动部署配置
```

## 本地预览

需要先安装 Hugo（extended 版）：

```bash
# macOS
brew install hugo

# Windows
winget install Hugo.Hugo.Extended

# 或者去 https://github.com/gohugoio/hugo/releases 下载
```

然后在项目根目录执行：

```bash
hugo server -D
```

打开 http://localhost:1313 即可。`-D` 表示同时渲染草稿文章。

> 如果暂时不想装 Hugo，也可以跳过本地预览，直接推到 GitHub 让 Actions 帮你构建。

## 写一篇新文章

```bash
hugo new content work/my-new-post.md
```

或者手动在 `content/work/`（或 `life/`、`tech/`）下新建 `.md` 文件：

```markdown
---
title: "文章标题"
date: 2026-10-16
tags: ["标签一", "标签二"]
summary: "一句话摘要，会显示在首页卡片上。"
draft: false
---

正文用 Markdown 写就行。

## 二级标题会自动进右侧目录

- 支持列表
- 支持 `行内代码`
- 支持表格、引用、代码块
```

`draft: true` 的文章不会发布到线上。

## 部署到 GitHub Pages

**详细的从零操作手册见 [DEPLOY.md](./DEPLOY.md)**，包含注册账号、建仓库、推送认证（Token / SSH / gh CLI 三种方式）、开启 Pages、排错等完整步骤。

速览版：

1. 在 GitHub 新建仓库，名字必须是 `<你的用户名>.github.io`（Public）
2. 改 `hugo.toml` 的 `baseURL`、`title` 和 `[params]` 里的个人信息
3. 提交并推送：

```bash
git config --global user.name "你的名字"
git config --global user.email "你的邮箱"
git commit -m "初始化博客"
git remote add origin https://github.com/<你的用户名>/<你的用户名>.github.io.git
git push -u origin main
```

4. 仓库 **Settings → Pages → Source** 选 **GitHub Actions**（不是选分支，这步最容易漏）
5. 等 Actions 跑完（约 1 分钟），访问 `https://<你的用户名>.github.io/`

**以后每次 `git push`，都会自动重新构建发布，不需要任何额外操作。**

## 绑定自己的域名（可选）

1. 在域名服务商处添加一条 CNAME 记录，指向 `<你的用户名>.github.io`
2. 仓库 Settings → Pages → Custom domain 填入你的域名
3. 勾选 Enforce HTTPS

仍然不需要服务器，也不需要额外费用。

## 常见问题

**推送后 Pages 还是 404？**
先确认 Settings → Pages 的 Source 选的是 `GitHub Actions`，再去 Actions 标签页看构建是否成功。第一次部署可能需要几分钟。

**想换博客名 / 头像字母？**
改 `hugo.toml` 的 `title` 和 `[params] initials`。

**想调整配色？**
改 `assets/css/main.css` 顶部的 CSS 变量，`--accent` 是主色。

**搜索搜不到新写的文章？**
搜索索引是构建时生成的 `/index.json`，推送到 GitHub 触发重新构建后会自动更新，不需要手动维护。本地预览时如果刚加完文章搜不到，重启一下 `hugo server`。

**搜索是全站的吗？**
是纯前端全文搜索，数据源就是 `layouts/home.json` 生成的索引，包含每篇文章的标题、标签、摘要和正文纯文本。文章多了之后索引文件会变大（几百篇文章大概几百 KB），对个人博客完全够用；真到几万篇再考虑换 Algolia 之类的方案。

**想加一个板块（比如「读书」）？**
新建 `content/books/_index.md` 和 `content/books/` 目录，然后在 `hugo.toml` 的 `[menu]` 里加一条，并把 `layouts/home.html` 里的 `slice "work" "life" "tech"` 加上新板块名。
