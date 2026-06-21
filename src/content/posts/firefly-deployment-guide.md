---
title: Firefly 博客部署实战指南 — 从零到上线
published: 2026-06-21
pinned: false
description: 记录一次从零开始，使用 Firefly 主题 + Cloudflare Workers 免费部署个人博客的完整过程。适合小白跟着操作。
tags: [Firefly, 博客, Cloudflare, 部署, 实战]
category: 博客指南
draft: false
image: ""
---

## 前言

本文记录了我通过 WorkBuddy AI 助手，从零开始部署一个 Firefly 博客的完整过程。

整个过程分为 7 步，最终博客成功上线，可通过 `https://firefly.848489301.workers.dev/` 访问。

---

## 效果预览

部署完成后，博客首页效果如下：

![博客首页效果](/firefly-homepage.jpg)

**站点信息**：

- **站点标题**：`dianddiai 的博客`
- **副标题**：`记录vibecoding`
- **作者**：`dianddiai`
- **签名**：`Hello, world.`
- **技术栈**：Firefly v6.12.3 / Astro v6.4.6 / Cloudflare Workers

---

## 准备工作

开始前，需要准备好以下几样东西：

| 项目 | 要求 |
|------|------|
| 域名 | 无需备案，可使用二级域名；若绑定自定义域名，建议托管在 Cloudflare |
| 系统 | Windows（本文以 Windows 为例） |
| GitHub 账号 | 用于托管源码 |
| Cloudflare 账号 | 用于托管站点，自动拉取 GitHub 源码部署 |

---

## 步骤一：环境检查

在本地操作前，先确认以下工具已安装且版本正确。

### 1. 安装 Node.js

- 推荐安装 **LTS 稳定版**，版本需要 **≥ 22**
- 下载地址：<https://nodejs.org/zh-cn/download>
- 安装完成后，打开终端验证：

```bash
node -v
npm -v
```

### 2. 安装 pnpm

```bash
npm install -g pnpm
pnpm -v
```

### 3. 安装 Git

- 下载地址：<https://git-scm.cn/install/windows>
- 安装完成后验证：

```bash
git --version
```

### 4. 安装 VS Code（可选）

- 下载地址：<https://code.visualstudio.com/Download>
- 用于修改配置文件、编写文章

---

## 步骤二：Fork Firefly 官方仓库

Firefly 是一个开源的 Astro 博客主题，需要先 Fork 到自己的 GitHub 账号下。

1. 打开 Firefly 官方仓库：<https://github.com/CuteLeaf/Firefly>
2. 点击右上角的 **Fork** 按钮
3. 填写仓库名称，点击 **Create Fork**
4. Fork 完成后，复制你自己的仓库 HTTPS 链接，格式类似：

```
https://github.com/你的用户名/Firefly.git
```

---

## 步骤三：克隆仓库并修改配置

### 1. 克隆仓库到本地

在目标目录（例如 `D:\workbuddycunchu\`）右键打开 Git Bash，执行：

```bash
git clone https://github.com/你的用户名/Firefly.git
cd Firefly
```

### 2. 安装依赖

```bash
pnpm install
```

> 注意：依赖较多（约 1165 个包），安装时间可能较长，耐心等待即可。

### 3. 修改站点配置

配置文件位于 `src/config/siteConfig.ts`，主要修改以下字段：

```ts
export const siteConfig: SiteConfig = {
    title: "你的博客标题",
    subtitle: "你的副标题",
    site_url: "https://你的域名或workers.dev地址",
    description: "站点描述",
    // ... 其他配置
}
```

同时修改 `src/config/profileConfig.ts`：

```ts
export const profileConfig: ProfileConfig = {
    avatar: "assets/images/avatar.avif",
    name: "你的名字",
    bio: "你的个性签名",
    links: [
        {
            name: "Email",
            icon: "fa7-solid:envelope",
            url: "mailto:你的邮箱",
            showName: false,
        },
    ],
}
```

### 4. 提交并推送配置修改

```bash
git add .
git commit -m "init: 配置 Firefly 博客"
git push
```

> 如果 `git push` 失败，可能是因为本地没有 GitHub 凭证。可以通过 GitHub Desktop 手动推送，或配置 Personal Access Token。

---

## 步骤四：本地预览

配置完成后，可以先在本地启动开发服务器预览效果。

```bash
pnpm dev
```

等待 10~30 秒，终端会显示访问地址：

```
  ┃  Local    http://localhost:4321/
```

在浏览器打开 `http://localhost:4321/`，确认页面标题、作者信息、样式等都符合预期。

---

## 步骤五：推送到 GitHub

本地确认无误后，将代码推送到 GitHub，为后续的自动部署做准备。

如果使用 GitHub Desktop：

1. 打开 GitHub Desktop
2. 左侧仓库列表选择 `Firefly`
3. 右侧会显示 `1 commit to push`
4. 点击 **Push origin** 按钮
5. 等待推送完成

推送完成后，打开你的 GitHub 仓库页面，确认最新提交已出现。

---

## 步骤六：部署到 Cloudflare Workers

### 1. 登录 Cloudflare 控制台

打开 <https://dash.cloudflare.com/>，输入账号密码登录。

### 2. 创建 Worker 应用

1. 左侧菜单点击 **Workers 和 Pages**
2. 点击 **创建应用程序**
3. 选择 **连接到 Git**，授权 Cloudflare 访问你的 GitHub 账号
4. 选择你 Fork 的 Firefly 仓库
5. 配置构建设置：
   - **Build command**：`pnpm build`
   - **Deploy command**：`npx wrangler deploy`
6. 点击 **部署**，等待自动构建完成

### 3. 验证部署是否成功

部署完成后，Cloudflare 会分配一个临时域名，格式为：

```
https://你的Worker名称.你的子账户.workers.dev
```

在浏览器打开该地址，如果页面与本地预览一致，说明部署成功。

---

## 步骤七：绑定自定义域名（可选）

如果你有自己的域名，可以绑定到 Cloudflare Worker。

### 方式一：域名已在 Cloudflare 托管

直接在 Worker 的 **Domains** 页面添加域名即可。

### 方式二：域名不在 Cloudflare

1. 在 Cloudflare 中添加域名
2. 按提示修改域名的 DNS 服务器为 Cloudflare 提供的地址
3. DNS 生效后再到 Worker 中绑定域名

---

## 如何修改博客内容

博客部署完成后，后续的内容修改都在本地进行，流程如下：

### 1. 写新文章

在 `src/content/posts/` 目录下新建 `.md` 文件，格式如下：

```md
---
title: 文章标题
published: 2026-06-21
description: 文章简介
tags: [标签1, 标签2]
category: 分类名
draft: false
---

这里是 Markdown 正文内容。
```

### 2. 本地预览

```bash
pnpm dev
```

访问 `http://localhost:4321/` 确认效果。

### 3. 推送发布

```bash
git add .
git commit -m "新增/修改文章"
git push
```

推送后，Cloudflare 会自动重新构建并部署，一般 1~3 分钟即可在线上看到更新。

---

## 常见问题

### Q：站点 URL 配置错了怎么办？

修改 `src/config/siteConfig.ts` 中的 `site_url` 字段，重新推送即可。注意 `site_url` 需要与实际访问地址一致，否则分享到社交媒体时链接可能错误。

### Q：如何确认 Cloudflare 已开启自动部署？

在 Cloudflare Workers 控制台的 **Deployments** 页面，查看部署记录中是否有来自 `Git push` 的自动部署记录。如果没有，需要在 Workers 设置中重新连接 GitHub 仓库。

### Q：本地 `pnpm dev` 启动后访问不了？

检查终端输出，确认是否显示 `Local    http://localhost:4321/`。如果端口被占用，可以尝试修改 `package.json` 中的 `dev` 脚本，指定其他端口。

---

## 总结

通过 Firefly 主题 + Cloudflare Workers，可以非常快速地部署一个美观的个人博客：

- ✅ 免费托管，无需自备服务器
- ✅ 自动部署，推送代码后自动上线
- ✅ 主题美观，基于 Material Design 3 设计
- ✅ 功能丰富，支持文章加密、评论、RSS、Mermaid 图表等

整个流程熟练后，从零到上线可以在 30 分钟内完成。

---

*本文基于 2026 年 6 月的实际操作记录整理，如有变动请以官方文档为准。*

*Firefly 官方文档：<https://docs-firefly.cuteleaf.cn>*
