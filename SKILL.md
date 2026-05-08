---
name: ubuntu-chromium-setup
description: Ubuntu/WSL Chromium 安装 + 中文字体配置，解决小红书等中文页面乱码
description_en: Install Chromium on Ubuntu/WSL + configure Chinese fonts to fix garbled text
triggers:
  - ubuntu 安装 chrome
  - ubuntu 安装 chromium
  - wsl ubuntu chrome
  - linux 中文乱码
  - 小红书 乱码
  - fontconfig 字体配置
  - ubuntu 字体
  - ubuntu install chrome
  - ubuntu install chromium
  - wsl ubuntu chrome
  - linux chinese garbled
category: devops
author: relunctance
created: 2026-05-08
updated: 2026-05-08
version: "1.0.0"
tags:
  - ubuntu
  - wsl
  - chromium
  - chrome
  - fontconfig
  - 中文乱码
  - fonts-noto-cjk
platforms:
  openclaw: true
  claude_code: true
  hermes: true
---

# ubuntu-chromium-setup

## About | 关于

`ubuntu-chromium-setup` provides two ways to install Chromium on Ubuntu/WSL Ubuntu Desktop and configure Chinese fonts to fix garbled text on Chinese websites (e.g., Xiaohongshu).

`ubuntu-chromium-setup` 提供两种方式在 Ubuntu / WSL Ubuntu 桌面版上安装 Chromium，并配置中文字体解决小红书等中文页面的乱码问题。

## 概述

包含两种安装方式和完整的中文字体配置：
- **方式 A**：安装 Linux 原生 Chromium（推荐）
- **方式 B**：WSL 复用 Windows Chrome

## 快速安装

### 方式 A：安装原生 Chromium

```bash
sudo apt update && sudo apt install chromium-browser
```

### 方式 B：WSL 复用 Windows Chrome

Windows Chrome 路径：`/mnt/c/Program Files/Google/Chrome/Application/chrome.exe`

### 字体配置

```bash
sudo apt install fonts-noto-cjk
mkdir -p ~/.config/fontconfig
# 写入 fonts.conf（见完整文档）
fc-cache -f
echo 'export LANG=zh_CN.UTF-8' >> ~/.bashrc
```

## 安装

```bash
# OpenClaw
clawhub install ubuntu-chromium-setup

# Claude Code
mkdir -p ~/claude/skills/ubuntu-chromium-setup
cp SKILL.md ~/claude/skills/ubuntu-chromium-setup/

# Hermes
mkdir -p ~/.hermes/skills/ubuntu-chromium-setup
cp SKILL.md ~/.hermes/skills/ubuntu-chromium-setup/
```

## 踩坑记录

| 坑 | 解决方案 |
|---|---|
| locale 是 `C.UTF-8` | `sudo locale-gen zh_CN.UTF-8` + 加 bashrc |
| wqy-zenhei 字体不全 | 安装 `fonts-noto-cjk` |
| `.desktop` 不继承 shell 环境变量 | Exec 里写 `env LANG=zh_CN.UTF-8` |
| WSL git push 超时 | `git config --global http.proxy http://192.168.1.109:10808` |
