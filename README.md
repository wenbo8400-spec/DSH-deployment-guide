🛠️ 前置要求
Node.js: 版本需满足 ^22.19.0 || >=24.0.0（建议使用 Node 24）。

VS Code: 已安装并配置好 WSL 或 Remote-SSH 扩展。

场景一：在 WSL2 中部署
1. 安装 Node.js
如果尚未安装 Node.js，推荐使用 NodeSource 源安装：

bash
curl -fsSL https://deb.nodesource.com/setup_24.x | sudo -E bash -
sudo apt install -y nodejs
2. 全局安装 DSH 并放行构建脚本
由于 npm 全局安装需要权限，请使用 sudo。注意：必须放行原生模块的构建脚本，否则 DSH 启动时会因为缺少 node-pty 报错。

bash
# 全局安装
sudo npm install -g @deepseek-ai/dsh

# 放行并触发原生模块编译
sudo npm install -g --allow-scripts=@deepseek-ai/dsh-subprocess-local,koffi,node-pty,@google/genai,protobufjs @deepseek-ai/dsh
sudo npm rebuild -g
3. 启动 DSH
使用 --no-open 参数避免尝试调用图形界面浏览器：

bash
dsh web --no-open
4. 访问 Web 界面
由于 WSL2 的 localhost 自动转发特性，复制终端输出的地址，直接在 Windows 浏览器、vscode中打开即可。

场景二：在 Remote SSH (Linux/Docker) 中部署
1. 环境处理（针对 Docker / Root 用户）
如果你在 Docker 容器内且以 root 运行，可能没有 sudo 且存在代理冲突。建议直接下载 Node 二进制包：

bash

# 下载并解压 Node 24
curl -fsSL https://nodejs.org/dist/v24.22.1/node-v24.22.1-linux-x64.tar.xz -o /tmp/node.tar.xz
tar -xJf /tmp/node.tar.xz -C /usr/local --strip-components=1
2. 安装 DSH（Root 环境）
bash
# root 用户无需 sudo
npm install -g @deepseek-ai/dsh
npm install -g --allow-scripts=@deepseek-ai/dsh-subprocess-local,koffi,node-pty,@google/genai,protobufjs @deepseek-ai/dsh
npm rebuild -g
3. 启动 DSH 并转发端口
在 SSH 服务器终端启动：

bash
dsh web --no-open
复制连接在本地浏览器打开或ctrl＋左键在vscode直接打开。
建脚本的运行。

解决：使用 --allow-script
⚠️ 常见问题排查 (Troubleshooting)
npm ERR! EACCES: permission denied

原因：无全局写入权限。

解决：使用 sudo npm install，或修改 npm 默认全局路径。

Error: Cannot find module 'node-pty'

原因：npm 默认阻止了构s 参数重新安装并执行 npm rebuild -g。


//////////////////////////////////////////////////////////////////////////////////////////////////////////


📄 English Version: DeepSeek Harness (DSH) Cross-Environment Deployment Guide
Introduction
DeepSeek Harness (DSH) is a powerful AI programming assistant. This document covers how to deploy DSH in WSL2 (Local) and Remote SSH (Server/Docker) environments, and integrate it with VS Code for a seamless Web UI development experience.

🛠️ Prerequisites
Node.js: Version ^22.19.0 || >=24.0.0 (Node 24 recommended).

VS Code: Installed with WSL or Remote-SSH extensions.

Scenario 1: Deployment in WSL2
1. Install Node.js
If Node.js is not installed, use NodeSource:

bash
curl -fsSL https://deb.nodesource.com/setup_24.x | sudo -E bash -
sudo apt install -y nodejs
2. Global Install DSH & Allow Scripts
sudo is required for global installation. Important: You must allow native module build scripts, otherwise DSH will fail to start due to missing node-pty.

bash
# Global installation
sudo npm install -g @deepseek-ai/dsh

# Allow scripts and trigger native module compilation
sudo npm install -g --allow-scripts=@deepseek-ai/dsh-subprocess-local,koffi,node-pty,@google/genai,protobufjs @deepseek-ai/dsh
sudo npm rebuild -g
3. Start DSH
Use the --no-open flag to prevent it from attempting to launch a GUI browser:

bash
dsh web --no-open
4. Access the Web UI
Due to WSL2's automatic localhost forwarding, copy the URL printed in the terminal ，open it in your Windows browser or open it directly.

Scenario 2: Deployment in Remote SSH (Linux/Docker)
1. Environment Handling (Docker / Root Users)
If you are in a Docker container as root, you may lack sudo and face proxy issues. Download the Node binary directly:

bash
# Download and extract Node 24
curl -fsSL https://nodejs.org/dist/v24.22.1/node-v24.22.1-linux-x64.tar.xz -o /tmp/node.tar.xz
tar -xJf /tmp/node.tar.xz -C /usr/local --strip-components=1
2. Install DSH (Root Environment)
bash
# No sudo needed for root
npm install -g @deepseek-ai/dsh
npm install -g --allow-scripts=@deepseek-ai/dsh-subprocess-local,koffi,node-pty,@google/genai,protobufjs @deepseek-ai/dsh
npm rebuild -g
3. Start DSH & Port Forwarding
Start DSH in the SSH server terminal:

bash
dsh web --no-open

Copy the Token URL from DSH,  and open it in your local browser.

⚠️ Troubleshooting
npm ERR! EACCES: permission denied

Cause: No write permission for global directories.

Fix: Use sudo npm install, or change npm's default global prefix.
