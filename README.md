# DeepSeek Harness (DSH) Deployment Guide

> [English](README.md) | [简体中文](README_zh.md)

This document covers how to deploy DSH in **WSL2 (Local)** and **Remote SSH (Server/Docker)** environments, and integrate it with **VS Code** for a seamless Web UI development experience.

## 🛠️ Prerequisites

- **Node.js**: Version `^22.19.0 || >=24.0.0` (Node 24 recommended).
- **VS Code**: Installed with WSL or Remote-SSH extensions.

---

## Scenario 1: Deployment in WSL2

### 1. Install Node.js

If Node.js is not installed, use NodeSource:

```bash
curl -fsSL https://deb.nodesource.com/setup_24.x | sudo -E bash -
sudo apt install -y nodejs
```

### 2. Global Install DSH & Allow Scripts

`sudo` is required for global installation. **Important: You must allow native module build scripts, otherwise DSH will fail to start due to missing `node-pty`.**

```bash
# Global installation
sudo npm install -g @deepseek-ai/dsh

# Allow scripts and trigger native module compilation
sudo npm install -g --allow-scripts=@deepseek-ai/dsh-subprocess-local,koffi,node-pty,@google/genai,protobufjs @deepseek-ai/dsh
sudo npm rebuild -g
```

### 3. Start DSH

Use the `--no-open` flag to prevent it from attempting to launch a GUI browser:

```bash
dsh web --no-open
```

### 4. Access the Web UI

Due to WSL2's automatic localhost forwarding, copy the URL printed in the terminal  and open it directly in your Windows browser.

---

## Scenario 2: Deployment in Remote SSH (Linux/Docker)

### 1. Environment Handling (Docker / Root Users)

If you are in a Docker container as `root`, you may lack `sudo` and face proxy issues. Download the Node binary directly:

```bash
# Download and extract Node 24
curl -fsSL https://nodejs.org/dist/v24.22.1/node-v24.22.1-linux-x64.tar.xz -o /tmp/node.tar.xz
tar -xJf /tmp/node.tar.xz -C /usr/local --strip-components=1
```

### 2. Install DSH (Root Environment)

```bash
# No sudo needed for root
npm install -g @deepseek-ai/dsh
npm install -g --allow-scripts=@deepseek-ai/dsh-subprocess-local,koffi,node-pty,@google/genai,protobufjs @deepseek-ai/dsh
npm rebuild -g
```

### 3. Start DSH & Port Forwarding

Start DSH in the SSH server terminal:

```bash
dsh web --no-open
```

**Open directly in VS Code**: Copy the Token URL from DSH. In the VS Code terminal, hold `Ctrl` and click the link to open it directly in your local browser.

---

## ⚠️ Troubleshooting

### 1. Error `npm ERR! EACCES: permission denied`

- **Cause**: No write permission for global directories.
- **Fix**: Use `sudo npm install`, or change npm's default global prefix.
