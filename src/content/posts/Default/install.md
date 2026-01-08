---
title: Mac 搭建 Astro 博客实录：从 Node.js 到战胜 Git 报错
published: 2026-01-08
description: 本以为在 Mac 上搭建 Astro 博客会很丝滑，结果在 Git 推送和网络协议上踩了一堆坑。本文记录了从环境报错到成功部署的全过程。
image: ''
tags: [Astro, 教程, Mac, Git]
category: 技术
draft: false
---

最近心血来潮，想搭建一个属于自己的独立博客。作为一名对颜值有要求的设计师，我最终选中了 **Astro** 框架搭配高颜值的 **Fuwari** 主题，并计划使用 **Vercel** 进行自动部署。

本以为在 Mac 上操作会很丝滑，结果在环境配置，尤其是推送到 GitHub 的过程中，结结实实地踩了一堆坑。这篇文章把我的“踩坑”和“填坑”过程记录下来，希望能帮到同样使用 Mac 的朋友。

## 1. 起步：安装 Node.js 环境

因为 Astro 是基于 JavaScript 的框架，第一步必须给 Mac 装上 Node.js 环境。

对于 Mac 用户，最简单的方式其实是去 Node.js 官网下载 `.pkg` 安装包，或者如果你是开发者，可以使用 Homebrew 安装。

安装好后，打开终端验证一下：

```bash
node -v
# v24.x.x (成功显示版本号即为正常)
环境装好后，初始化博客项目非常快，直接运行官方命令：

```bash
npm create astro@latest -- --template yCENzh/Fuwari-yCENzh
``` 
2. 最大的拦路虎：Git 推送与权限验证
本地博客跑通了 (npm run dev)，效果很棒。但当我准备把它推送到 GitHub 上以便 Vercel 部署时，真正的折磨开始了。

遭遇战一：Git 不再支持密码验证
我在终端按照 GitHub 的提示运行 git push，输入了我的 GitHub 账号密码，结果直接报错：

、、、remote: Support for password authentication was removed on August 13, 2021. fatal: Authentication failed
、、、
原因：GitHub 为了安全，早就取消了在命令行直接输登录密码的方式，现在必须使用 Personal Access Token (个人令牌) 或者 SSH 密钥。

✅ 我的解决方案： 一开始我想去生成 Token，但在终端里粘贴 Token 总是因为格式或权限问题失败。后来我发现 VS Code 自带的“源代码管理” 才是神器：

点击 VS Code 左侧的“源代码管理”图标（那个像树杈一样的图标）。

点击 “发布分支” (Publish Branch) 或 “同步更改” (Sync Changes)。

VS Code 会自动跳出弹窗请求 GitHub 授权。

点击允许，浏览器自动完成登录验证。

根本不需要手输 Token，直接搞定身份验证！
遭遇战二：HTTP/2 网络协议报错
解决了身份验证，我以为稳了，结果进度条跑了一半又断了，出现了更诡异的报错：

fatal: unable to access '...': Error in the HTTP2 framing layer

或者： RPC failed; HTTP 400 curl 22 The requested URL returned error: 400

原因： 这是 Git 默认使用的 HTTP/2 协议在某些网络环境下（特别是在国内连接 GitHub 时）出现了“水土不服”，导致数据包传输中断或重置。

✅ 终极解决方案： 不需要开复杂的代理，也不需要改 hosts，只需要在终端执行一条命令，强制 Git 使用更老、更稳的 HTTP/1.1 协议：

```bash
git config --global http.version HTTP/1.1
```     
运行完这行命令后，再次点击推送，进度条瞬间跑满 Writing objects: 100%，上传成功！
遭遇战三：Git 忘记了我是谁
在某次提交时，我还遇到了这个经典的“新手”报错：

fatal: no email was given and auto-detection is disabled

这个问题最简单，Git 需要知道提交代码的人是谁。补上身份信息就行：

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```     
3. 上线：Vercel 一键部署
代码成功推送到 GitHub 后，剩下的事就非常简单了：

登录 Vercel 官网。

点击 "Add New Project" -> "Import from GitHub"。

选中我的博客仓库。

点击 Deploy。

等待大约一分钟，屏幕撒花，我的博客正式上线。