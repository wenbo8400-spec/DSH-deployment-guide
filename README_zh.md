# DeepSeek Harness (DSH) 部署指南

> [English](README.md) | [简体中文](README_zh.md)

本文档记录了如何在 **WSL2 (本地)** 和 **Remote SSH (远程服务器/容器)** 环境下部署 DSH，并结合 **VS Code** 实现丝滑的 Web UI 开发体验。

## 🛠️ 前置要求

- **Node.js**: 版本需满足 `^22.19.0 || >=24.0.0`（建议使用 Node 24）。
- **VS Code**: 已安装并配置好 WSL 或 Remote-SSH 扩展。

---

## 场景一：在 WSL2 中部署

### 1. 安装 Node.js

如果尚未安装 Node.js，推荐使用 NodeSource 源安装：

```bash
curl -fsSL https://deb.nodesource.com/setup_24.x | sudo -E bash -
sudo apt install -y nodejs
```

### 2. 全局安装 DSH 并放行构建脚本

由于 npm 全局安装需要权限，请使用 `sudo`。

```bash
# 全局安装
sudo npm install -g @deepseek-ai/dsh

# 放行并触发原生模块编译
sudo npm install -g --allow-scripts=@deepseek-ai/dsh-subprocess-local,koffi,node-pty,@google/genai,protobufjs @deepseek-ai/dsh
sudo npm rebuild -g
```

### 3. 启动 DSH

使用 `--no-open` 参数可以避免直接调用默认浏览器加载：

```bash
dsh web --no-open
```

### 4. 访问 Web 界面

由于 WSL2 的 localhost 自动转发特性，复制终端输出的地址，直接在 Windows 浏览器中打开或者在vscode左键打开。

---

## 场景二：在 Remote SSH (Linux/Docker) 中部署

### 1. 环境处理（针对 Docker / Root 用户）

如果你在 Docker 容器内且以 `root` 运行，可能没有 `sudo` 且存在代理冲突。建议直接下载 Node 二进制包：

```bash
# 下载并解压 Node 24
curl -fsSL https://nodejs.org/dist/v24.22.1/node-v24.22.1-linux-x64.tar.xz -o /tmp/node.tar.xz
tar -xJf /tmp/node.tar.xz -C /usr/local --strip-components=1
```

### 2. 安装 DSH（Root 环境）

```bash
# root 用户无需 sudo
npm install -g @deepseek-ai/dsh
npm install -g --allow-scripts=@deepseek-ai/dsh-subprocess-local,koffi,node-pty,@google/genai,protobufjs @deepseek-ai/dsh
npm rebuild -g
```

### 3. 启动 DSH 并转发端口

在 SSH 服务器终端启动：

```bash
dsh web --no-open
```

**在 VS Code 中快速打开**：复制终端输出的带 Token 链接，在 VS Code 的集成终端中按住 `Ctrl` 并点击该链接，即可直接在本地浏览器中打开。

---

## ⚠️ 常见问题排查 (Troubleshooting)

### 1. 报错 `npm ERR! EACCES: permission denied`

- **原因**：无全局写入权限。
- **解决**：使用 `sudo npm install`，或修改 npm 默认的全局路径。

### 2. 报错 `Error: Cannot find module 'node-pty'`

- **原因**：npm 默认阻止了构建脚本的运行。
- **解决**：使用 `--allow-scripts` 参数重新安装，并执行 `npm rebuild -g`。
