# 换电脑 / 新环境搭建指南

**结论：所有内容都在 GitHub 上，新电脑上克隆下来就能继续用。**

线上博客、文章、模板、样式、配置、提交历史——一个都不会丢。

---

## 会自动带过去的东西

| 内容 | 在哪 |
|---|---|
| 全部文章、模板、样式、配置 | GitHub 仓库里 |
| 完整的提交历史 | GitHub 仓库里 |
| GitHub Pages 设置 | GitHub 服务器上 |
| Actions 自动部署工作流 | GitHub 服务器上 |
| **线上博客本身** | GitHub 服务器上（换电脑不影响访问） |

## 不会带过去、需要重做的（只有 3 件）

| 内容 | 原因 |
|---|---|
| Git 身份配置 | 存在本机 `~/.gitconfig`，不在仓库里 |
| SSH 密钥 | 存在本机 `~/.ssh/`，出于安全**不应该**放进仓库 |
| Hugo（可选） | 本地预览用，不装也能写文章、能部署 |

---

## 新电脑上的完整步骤

### 第 1 步：安装 Git

- Windows：https://git-scm.com/download/win
- macOS：`brew install git`

验证：`git --version`

### 第 2 步：配置身份

```bash
git config --global user.name "Ill-win"
git config --global user.email "584892239@qq.com"
```

### 第 3 步：配置 SSH 密钥

**方式 A（推荐）：在新电脑上生成新密钥**

```bash
ssh-keygen -t ed25519 -C "584892239@qq.com" -f ~/.ssh/id_ed25519 -N ""
cat ~/.ssh/id_ed25519.pub
```

复制输出的那一整行，打开 https://github.com/settings/keys → **New SSH key** → 粘贴 → 保存。

> 旧电脑上的那把密钥可以继续留着用，也可以在 https://github.com/settings/keys 里删掉。

**方式 B：把旧电脑的密钥拷过来**

从旧电脑复制这两个文件到新电脑的 `C:\Users\<你的用户名>\.ssh\`：

```
id_ed25519        私钥
id_ed25519.pub    公钥
```

拷过来就能直接用，不用再往 GitHub 上加。

### 第 4 步：克隆仓库

```bash
cd D:/            # 或你想放的任何目录
git clone git@github.com:Ill-win/Ill-win.github.io.git my-blog
cd my-blog
```

> 如果 SSH 连不上（国内网络常见），先跑 `ssh -T git@github.com` 测试。
> 报 `Failed to connect to github.com:443` 就看 [DEPLOY.md](./DEPLOY.md) 的「网络连不上 GitHub 怎么办」章节。

### 第 5 步：装 Hugo（可选，仅本地预览需要）

```bash
# Windows
winget install Hugo.Hugo.Extended

# macOS
brew install hugo
```

然后：

```bash
hugo server -D
```

打开 http://localhost:1313 就能看到博客。

> 不装也行——写文章用记事本就能写，推送到 GitHub 后 Actions 会自动构建。

---

## 验证清单

在新电脑上跑一遍这些，全通过就说明环境搭好了：

```bash
git --version                        # 有版本号
git config --global user.name        # 显示 Ill-win
ssh -T git@github.com                # 显示 Hi Ill-win! You've successfully authenticated
git log --oneline | wc -l            # 显示 5（或更多）
git remote -v                        # 显示 git@github.com:Ill-win/Ill-win.github.io.git
git status                           # 显示 clean
```

---

## 日常使用（新电脑上完全一样）

```bash
# 写新文章
hugo new content work/my-new-post.md

# 发布
git add .
git commit -m "新增文章：xxx"
git push
```

推送后 GitHub Actions 自动构建发布，约 1 分钟线上更新。

---

## 常见疑问

**线上博客会因为我换电脑而消失吗？**
不会。博客是 GitHub 服务器上的静态文件，跟你的电脑没有任何关系。

**旧电脑上的 `public/` 目录要拷吗？**
不用。那是构建产物，新电脑上跑一次 `hugo` 就会重新生成。

**旧电脑上的 `.workbuddy-ai` 目录要拷吗？**
不用。那是 AI 助手的工作记录，跟博客无关。

**我需要重新建仓库、重新开 Pages 吗？**
都不用。这些都在 GitHub 服务器上，跟你换不换电脑无关。

**换电脑后文章会丢吗？**
不会。只要 `git push` 过，文章就在 GitHub 上。唯一可能丢的情况是**本地有没推送的提交**——所以换电脑前务必先执行 `git status` 和 `git push` 确认干净。
