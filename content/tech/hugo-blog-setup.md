---
title: "用 Hugo 30 分钟搭一个零成本博客"
date: 2026-10-12
tags: ["Hugo", "部署", "工具链"]
summary: "从零开始，用 Hugo + GitHub Pages 把博客跑起来，全程不需要服务器，也不需要花钱。"
---

我一直想有个自己的地方写字，但每次都被「先搭个网站」这一步劝退。直到用上 Hugo，从 `git init` 到线上能访问，前后不到半小时。

## 为什么是 Hugo

静态站点生成器不少，选 Hugo 的理由很实在：

| 方案 | 构建速度 | 上手难度 | 是否需要服务器 |
|---|---|---|---|
| Hugo | 毫秒级 | 低 | 否 |
| Jekyll | 秒级 | 中 | 否 |
| WordPress | 动态渲染 | 低 | 是 |

Hugo 是编译型的——它把你写的 Markdown 一次性编译成纯 HTML 文件。没有数据库，没有运行时，没有需要维护的服务。

## 安装与初始化

```bash
hugo new site my-blog
cd my-blog
git init
```

然后把主题和内容放进去，本地预览：

```bash
hugo server -D
```

> `-D` 参数会同时渲染草稿文章，方便你边写边看效果。

## 写第一篇文章

在 `content/posts/` 下新建一个 `.md` 文件，头部写上 front matter：

```markdown
---
title: "文章标题"
date: 2026-10-12
tags: ["标签一", "标签二"]
draft: false
---

正文直接用 Markdown 写。
```

Hugo 会自动生成标签页、归档页和 RSS 订阅，这些都不需要你手动配置。

## 部署上线

这一步是整个流程里最让人惊喜的部分。你只需要把源码推送到 GitHub，剩下的交给 GitHub Actions：

1. 推送代码到 `main` 分支
2. Actions 自动运行 `hugo --minify`
3. 构建产物发布到 GitHub Pages

整个过程免费，而且每次推送都会自动重新部署。具体配置我写在另一篇里了。

## 一点感想

工具的选择上，我越来越倾向于「构建完就不需要再操心」的方案。Hugo 生成的产物就是一堆静态文件，扔到哪里都能跑，十年后大概也还能跑。

这比什么功能都重要。
