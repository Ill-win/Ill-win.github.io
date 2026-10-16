# 上线操作手册

从零开始，把这个博客发布到 GitHub Pages。全程不需要服务器，不需要花钱。

**预计耗时：15 分钟。**

---

## 第 0 步：前置准备

### 0.1 注册 GitHub 账号

如果还没有，去 https://github.com/signup 注册。记住你的**用户名**，后面到处都要用（下面用 `<你的用户名>` 代替）。

### 0.2 确认本机装了 Git

打开终端（Windows 用 PowerShell 或 Git Bash，macOS 用 Terminal），执行：

```bash
git --version
```

能打印出版本号（比如 `git version 2.55.0`）就 OK。

装的话：
- Windows：https://git-scm.com/download/win
- macOS：终端执行 `brew install git`，或者装 Xcode Command Line Tools

### 0.3 改掉站点里的占位信息

打开 `hugo.toml`，把下面这几处换成你自己的：

```toml
baseURL = "https://<你的用户名>.github.io/"   # ← 必改
title   = "拾光集"                              # ← 博客名，随便起
```

```toml
[params]
  author   = "Alex"                        # ← 你的名字
  initials = "AL"                          # ← 头像占位字母，两个大写字母
  github   = "https://github.com/<你的用户名>"
  email    = "you@example.com"
```

> `baseURL` 其实会被部署流程自动覆盖，但本地构建和 RSS 里会用到，建议改对。

---

## 第 1 步：在 GitHub 上创建仓库

1. 打开 https://github.com/new
2. **Repository name** 填：`<你的用户名>.github.io`
   - ⚠️ 这里必须**一模一样**。比如用户名是 `zhangsan`，就填 `zhangsan.github.io`。
   - 只有这样，你的博客地址才是 `https://zhangsan.github.io/`，不带子路径。
3. **Description** 随便填，可留空。
4. **Public / Private**：选 **Public**。
   - 免费账号的 GitHub Pages 只支持公开仓库。私有仓库需要付费订阅。
5. **不要**勾选 "Add a README file"、"Add .gitignore"、"Choose a license"——留空，否则推送时会冲突。
6. 点 **Create repository**。

创建完会看到一个空仓库页面，上面有 `https://github.com/<你的用户名>/<你的用户名>.github.io.git` 这样的地址，**记下来**，下一步要用。

---

## 第 2 步：配置 Git 身份并提交

在项目目录（`my-blog`）下打开终端，依次执行：

```bash
# 1. 告诉 Git 你是谁（全局只需配置一次）
git config --global user.name "你的名字"
git config --global user.email "你的邮箱"

# 2. 进入项目目录
cd D:/WBAIWorkSpace/my-blog

# 3. 提交所有文件
git commit -m "初始化博客"
```

> 项目里的 Git 仓库和分支（`main`）已经建好、文件也已经暂存好了，你只需要上面这三条命令。

执行 `git commit` 后如果输出里出现 `35 files changed` 之类的字样，就成功了。

---

## 第 3 步：推送到 GitHub

### 选择一种认证方式

GitHub 从 2021 年起就不支持用密码推送了，必须用下面三种方式之一。**推荐方式 A 或 C。**

---

### 方式 A：用 GitHub CLI（最省事）

1. 安装：https://cli.github.com/
2. 终端执行：

```bash
gh auth login
```

按提示选：`GitHub.com` → `HTTPS` → `Login with a web browser`，然后浏览器里授权一下。

3. 回到项目目录，添加远程仓库并推送：

```bash
git remote add origin https://github.com/<你的用户名>/<你的用户名>.github.io.git
git push -u origin main
```

---

### 方式 B：用 Personal Access Token（HTTPS）

1. 打开 https://github.com/settings/tokens?type=beta
2. 点 **Generate new token**，填个名字（比如 `blog-push`），**Expiration** 选 90 天或自定义
3. **Repository access** 选 `All repositories`（或只选这一个仓库）
4. **Permissions** → 展开 **Repository permissions** → 找到 **Contents** → 设为 **Read and write**
5. 点 **Generate token**，**立刻复制**那串 `github_pat_...`（离开页面就再也看不到了）

然后推送：

```bash
git remote add origin https://github.com/<你的用户名>/<你的用户名>.github.io.git
git push -u origin main
```

弹出登录框时：
- **Username**：填你的 GitHub 用户名
- **Password**：粘贴刚才那串 token（不是你的账号密码！）

> 如果提示 `remote origin already exists`，说明已经加过了，改用 `git remote set-url origin <地址>`。

---

### 方式 C：用 SSH 密钥（配置一次，长期免密）

```bash
# 1. 生成密钥（一路回车即可）
ssh-keygen -t ed25519 -C "你的邮箱"

# 2. 复制公钥内容
cat ~/.ssh/id_ed25519.pub
```

把输出的那一整行（`ssh-ed25519 AAAA...` 开头）复制，然后：
- 打开 https://github.com/settings/keys
- 点 **New SSH key**，Title 随便填，Key 粘贴进去，保存

最后推送：

```bash
git remote add origin git@github.com:<你的用户名>/<你的用户名>.github.io.git
git push -u origin main
```

---

### 推送成功的标志

终端出现类似这样的输出：

```
Writing objects: 100% (35/35), 45.12 KiB | 2.30 MiB/s, done.
To https://github.com/xxx/xxx.github.io.git
 * [new branch]      main -> main
branch 'main' set up to track 'origin/main'.
```

---

## 第 4 步：开启 GitHub Pages（关键，最容易漏）

1. 打开你的仓库页面：`https://github.com/<你的用户名>/<你的用户名>.github.io`
2. 点顶部 **Settings**（设置）
3. 左侧栏找到 **Pages**（在 "Code and automation" 分组下）
4. **Build and deployment** → **Source** 下拉框
   - ⚠️ 选 **GitHub Actions**，**不要**选 "Deploy from a branch"
   - 这是最常见的坑。选错分支会导致页面一直 404。
5. 不需要点保存，选完即生效。

---

## 第 5 步：等待并验证

1. 点仓库顶部的 **Actions** 标签
2. 应该能看到一条名为「构建并部署到 GitHub Pages」的运行记录
   - 黄色圆点 = 正在跑
   - 绿色对勾 = 成功（约 1 分钟）
   - 红色叉 = 失败，点进去看日志（见下面的排查章节）
3. 绿了之后，浏览器打开：

```
https://<你的用户名>.github.io/
```

看到博客首页就成功了 🎉

> 首次部署有时要等 2–3 分钟才能访问，DNS 和 CDN 缓存需要一点时间。

---

## 以后怎么更新博客

流程非常简单，每次都是这三条：

```bash
cd D:/WBAIWorkSpace/my-blog
git add .
git commit -m "新增文章：xxx"
git push
```

推送后 GitHub Actions 会**自动**重新构建并发布，大约 1 分钟后线上就更新了。你不需要做任何其他操作。

### 写新文章

```bash
# 方式一：用 Hugo 命令生成（需要本地装了 Hugo）
hugo new content work/my-new-post.md

# 方式二：直接手动建文件
# 在 content/work/ （或 life/、tech/）下新建 xxx.md
```

文章头部格式：

```markdown
---
title: "文章标题"
date: 2026-10-16
tags: ["标签一", "标签二"]
summary: "一句话摘要，会显示在首页卡片上"
draft: false
---

正文用 Markdown 写。
```

`draft: true` 的文章不会发布到线上。搜索索引、标签页、归档都会自动更新，不用手动维护。

---

## 网络连不上 GitHub 怎么办

如果 `git push` 报 `Failed to connect to github.com:443` 或 `Could not connect to server`，说明你的网络到 GitHub 的 HTTPS 通道不通。国内很常见，不是你的配置问题。

### 怎么判断

```bash
curl -m 10 -o /dev/null -w "github.com     %{http_code}\n" https://github.com
curl -m 10 -o /dev/null -w "api.github.com %{http_code}\n" https://api.github.com
```

如果第一个返回 `000`、第二个返回 `200`，就是典型的「github.com 主站被阻断」。

⚠️ **注意：API 通不代表主站通**，这是两套完全不同的 IP。

### 解决方案：改用 SSH（推荐）

实测经验：**HTTPS 通道被阻断时，SSH 通道通常仍然可用**（22 端口和 443 端口都通）。切换步骤：

**1. 生成密钥**（若 `~/.ssh/id_ed25519` 已存在则跳过）

```bash
ssh-keygen -t ed25519 -C "你的邮箱" -f ~/.ssh/id_ed25519 -N ""
```

**2. 复制公钥**

```bash
cat ~/.ssh/id_ed25519.pub
```

复制输出的**一整行**（`ssh-ed25519 AAAA...` 开头）。

**3. 添加到 GitHub**

打开 https://github.com/settings/keys → **New SSH key** → Title 随便填（如 `my-laptop`）→ Key 粘贴 → **Add SSH key**

**4. 切换远程地址**

```bash
git remote set-url origin git@github.com:<你的用户名>/<你的用户名>.github.io.git
```

**5. 测试连通**

```bash
ssh -T git@github.com
```

看到 `Hi xxx! You've successfully authenticated, but GitHub does not provide shell access.` 就成功了。

**6. 推送**

```bash
git push -u origin main
```

### 如果 22 端口也不通

有些网络会封 22 端口，改走 443：

```bash
ssh -T -p 443 git@ssh.github.com
```

通了就编辑 `C:\Users\<你的用户名>\.ssh\config`（没有就新建），写入：

```
Host github.com
  HostName ssh.github.com
  Port 443
  User git
```

之后照常用 `git@github.com:...` 的地址即可，git 会自动走 443。

### 备选方案

**用代理**：如果有 Clash / v2rayN 等代理软件，让 git 走代理：

```bash
git config --global http.proxy http://127.0.0.1:7890    # 端口换成你代理的实际端口
git config --global https.proxy http://127.0.0.1:7890
```

取消代理：

```bash
git config --global --unset http.proxy
git config --global --unset https.proxy
```

**换网络**：手机热点有时能通，可以应急推一次。

**改 hosts**：把 github.com 指向可用 IP。但如果阻断发生在 TLS 层（TCP 能连、握手卡死），改 hosts 是无效的。

> 好消息：GitHub Pages 的访问地址（`xxx.github.io`）和 github.com 主站是**不同的 IP 段**。主站不通时，你的博客页面往往仍然能正常访问。

---

## 常见问题排查

### 访问页面是 404

按顺序检查：

1. **仓库名对不对**：必须是 `<用户名>.github.io`，一个字符都不能差。**多一个点、少一个点、大小写不对都不行**——GitHub 只认这个精确格式，名字不对就不会把它当用户主页站点，必然 404。
   - 怎么验证：把仓库名复制出来逐字符比对。比如用户名 `zhangsan`，仓库名必须是 `zhangsan.github.io`，不能是 `zhangsan..github.io`、`zhangsan-github.io` 或 `zhangsan.github.io.bak`。
   - **改法**：仓库页面 → **Settings** → 最上方 **Repository name** → 改成正确名字 → 点 **Rename**。GitHub 会为旧名字保留跳转。
   - 改完记得同步本地远程地址：`git remote set-url origin https://github.com/<用户名>/<用户名>.github.io.git`
2. **仓库是不是空的**：如果打开仓库首页看到的是 "Quick setup" 引导页，说明代码还没推上去，自然也没有 Actions 记录。
3. **Settings → Pages → Source** 是否选了 `GitHub Actions`
4. **Actions 是否成功**：去 Actions 标签页看，红色就是构建失败了
5. **仓库是不是 Public**：免费账号的 Pages 不支持私有仓库
6. 刚部署完等 2–3 分钟再刷新，或者用无痕窗口打开（避开缓存）

### Actions 页面显示 "Get started with GitHub Actions"

这不是报错，而是说明 **GitHub 在仓库里没找到任何 workflow 文件**。通常是：

- 代码还没推送成功（仓库是空的）
- `.github/workflows/deploy.yml` 没被提交上去

验证方法：打开仓库首页，看看有没有 `.github` 目录。没有就是没推上去。

### 推送时报 Repository not found

两种可能：
- 本地 `origin` 地址和实际仓库名不一致 → 用 `git remote -v` 看一下，再用 `git remote set-url origin <正确地址>` 修正
- 仓库名拼错了 → 参考上面第 1 条改仓库名

### Actions 构建失败

点进失败的那次运行，展开红色的步骤看报错。常见原因：

- **YAML 缩进错误**：`.github/workflows/deploy.yml` 用的是空格缩进，不能混用 Tab
- **Hugo 版本不匹配**：workflow 里锁的是 `0.168.0`，如果你本地 Hugo 更新了，两边模板语法可能有差异。改 `deploy.yml` 里的 `hugo-version` 即可
- **模板语法错误**：本地执行 `hugo` 会直接报出文件和行号

### 推送时提示 authentication failed

说明认证没过。GitHub 不支持密码推送：
- 用 HTTPS：密码栏要填 **Personal Access Token**，不是账号密码
- 或者改用 SSH 方式（见第 3 步方式 C）
- 或者直接用 `gh auth login`（方式 A）

### 提示 `failed to push some refs` / `rejected`

通常是远程仓库里有你本地没有的提交（比如建仓库时勾了 README）。解决：

```bash
git pull --rebase origin main
git push
```

### 本地预览中文显示乱码 / 样式不对

确认本地 Hugo 是 **extended** 版本（`hugo version` 输出里带 `extended`）。本项目用了 CSS 的现代特性，建议用较新版本的浏览器。

### 想改博客地址 / 换主题色

- 博客名、作者、简介：改 `hugo.toml`
- 主色调：改 `assets/css/main.css` 顶部的 `--accent` 变量
- 导航菜单：改 `hugo.toml` 的 `[menu]` 段

---

## 附：绑定自己的域名（可选）

想要 `blog.你的域名.com` 这种地址：

1. 去你的域名服务商（阿里云 / 腾讯云 / Cloudflare 等）添加一条 DNS 记录：
   - 类型 `CNAME`
   - 主机记录 `blog`（或你想要的子域名）
   - 记录值 `<你的用户名>.github.io`
2. 仓库 **Settings → Pages → Custom domain** 填入 `blog.你的域名.com`，保存
3. 等 DNS 生效（几分钟到几小时），勾选 **Enforce HTTPS**

依然不需要服务器，也不需要额外费用。
