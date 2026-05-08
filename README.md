# ubuntu-chromium-setup

<div align="center">

![ubuntu-chromium-setup](assets/banner.svg)

</div>

<p align="center">

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![OpenClaw](https://img.shields.io/badge/OpenClaw-Supported-blueviolet)](https://github.com/openclaw)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-Compatible-green)](https://claude.ai)
[![Codex](https://img.shields.io/badge/Codex-Compatible-orange)](https://github.com)
[![Hermes](https://img.shields.io/badge/Hermes-Agent-blue)](https://github.com)

</p>

<div align="center">

# ubuntu-chromium-setup

Ubuntu / WSL Chromium 安装 + 中文字体配置，解决小红书等中文页面乱码

</div>

---

## 核心特性

| 特性 | 说明 |
|------|------|
| 🌐 **双模式安装** | 原生 Chromium 或复用 Windows Chrome |
| 🇨🇳 **中文优化** | 配置中文字体，彻底解决中文乱码 |
| 📱 **桌面图标** | 自动创建 Desktop 快捷方式 |

---

## 快速开始

```bash
# 方式 A：安装原生 Chromium
sudo apt update && sudo apt install chromium-browser

# 字体配置（解决中文乱码）
sudo apt install fonts-noto-cjk
mkdir -p ~/.config/fontconfig
# 完整配置见 SKILL.md
```

完整安装步骤请查阅 [SKILL.md](SKILL.md)。

---

## 触发条件

- ubuntu 安装 chrome
- ubuntu 安装 chromium
- wsl ubuntu chrome
- linux 中文乱码
- 小红书 乱码
- fontconfig 字体配置

---

## 平台支持

| 平台 | 状态 | 安装方式 |
|------|------|---------|
| OpenClaw | ✅ 支持 | `clawhub install ubuntu-chromium-setup` |
| Claude Code | ✅ 支持 | 复制 SKILL.md 到 `~/claude/skills/ubuntu-chromium-setup/` |
| Codex | ✅ 支持 | 复制 SKILL.md 到 `~/.codex/skills/ubuntu-chromium-setup/` |
| Hermes | ✅ 支持 | 复制 SKILL.md 到 `~/.hermes/skills/ubuntu-chromium-setup/` |

---

## 详见

完整的 SKILL.md 定义和踩坑记录请查阅 [SKILL.md](SKILL.md)。

---

<div align="center">

MIT License © [relunctance](https://github.com/relunctance)

</div>
