---
name: ubuntu-chromium-setup
description: Ubuntu/WSL Chromium 安装 + 中文字体配置，解决小红书等中文页面乱码问题
triggers:
  - ubuntu 安装 chrome
  - ubuntu 安装 chromium
  - wsl ubuntu chrome
  - linux 中文乱码
  - 小红书 乱码
  - fontconfig 字体配置
  - ubuntu 字体
category: devops
author: relunctance
created: 2026-05-08
updated: 2026-05-08
tags:
  - ubuntu
  - wsl
  - chromium
  - chrome
  - fontconfig
  - 中文乱码
  - fonts-noto-cjk
---

# ubuntu-chromium-setup

> 在 Ubuntu / WSL Ubuntu 桌面版上安装 Chromium + 配置中文字体的完整方案

## 触发条件

用户提到以下任意关键词时使用本 skill：
- `ubuntu 安装 chrome` / `ubuntu 安装 chromium`
- `wsl ubuntu chrome`
- `linux 中文乱码` / `小红书 乱码`
- `fontconfig 字体配置` / `ubuntu 字体`

## 快速执行

### 方式 A：安装 Linux 原生 Chromium（推荐）

```bash
# 安装
sudo apt update && sudo apt install chromium-browser

# 桌面图标
cat > ~/Desktop/chromium.desktop << 'EOF'
[Desktop Entry]
Version=1.0
Type=Application
Name=Chromium
Icon=chromium-browser
Exec=env LANG=zh_CN.UTF-8 LC_ALL=zh_CN.UTF-8 chromium-browser %U
Terminal=false
StartupNotify=true
MimeType=text/html;text/xml;application/xhtml_xml;
EOF
chmod +x ~/Desktop/chromium.desktop
```

### 方式 B：WSL 复用 Windows Chrome

```bash
cat > ~/Desktop/chromium.desktop << 'EOF'
[Desktop Entry]
Version=1.0
Type=Application
Name=Google Chrome
Icon=/mnt/c/Program Files/Google/Chrome/Application/chrome.exe
Exec="/mnt/c/Program Files/Google/Chrome/Application/chrome.exe" %U
Terminal=false
StartupNotify=true
MimeType=text/html;text/xml;application/xhtml_xml;
EOF
chmod +x ~/Desktop/chromium.desktop
```

### 字体配置（解决中文乱码）

```bash
# 1. 安装思源黑体（必须）
sudo apt install fonts-noto-cjk

# 2. 配置 fontconfig
mkdir -p ~/.config/fontconfig
cat > ~/.config/fontconfig/fonts.conf << 'FONTSCONF'
<?xml version="1.0"?>
<!DOCTYPE fontconfig SYSTEM "fonts.dtd">
<fontconfig>
  <match target="pattern">
    <edit name="lang" mode="prepend"><string>zh_CN</string></edit>
  </match>

  <alias>
    <family>sans-serif</family>
    <prefer>
      <family>Noto Sans CJK SC</family>
      <family>WenQuanYi Zen Hei</family>
    </prefer>
  </alias>
  <alias>
    <family>serif</family>
    <prefer>
      <family>Noto Serif CJK SC</family>
      <family>WenQuanYi Zen Hei</family>
    </prefer>
  </alias>
  <alias>
    <family>monospace</family>
    <prefer>
      <family>Noto Sans Mono</family>
    </prefer>
  </alias>

  <match target="font">
    <edit name="antialias" mode="assign"><bool>true</bool></edit>
    <edit name="hinting" mode="assign"><bool>true</bool></edit>
    <edit name="hintstyle" mode="assign"><const>hintslight</const></edit>
  </match>
</fontconfig>
FONTSCONF

# 3. 刷新字体缓存
fc-cache -f

# 4. 验证
fc-match sans-serif    # 应返回 Noto Sans CJK SC
fc-match emoji         # 应返回 Noto Color Emoji

# 5. 加到 ~/.bashrc（永久生效）
echo 'export LANG=zh_CN.UTF-8' >> ~/.bashrc
```

## 踩坑记录

| 坑 | 说明 | 解决方案 |
|---|---|---|
| locale 是 `C.UTF-8` | Chromium 字体 fallback 失效 | `sudo locale-gen zh_CN.UTF-8` + 加 bashrc |
| wqy-zenhei 字体不全 | 很多 CJK 字符没有，尤其是 emoji | 安装 `fonts-noto-cjk` |
| `.desktop` 不继承 shell 环境变量 | 必须显式传 `LANG=zh_CN.UTF-8` | Exec 里面写死 env |
| `fonts.conf` 写错元素静默失败 | `<blank>false</blank>` 是非法元素 | 删除该元素 |
| WSL git push 超时 | 网络不通 GitHub | 配置 http.proxy |

## 验证步骤

```bash
# 字体配置正确
fc-match sans-serif  # → NotoSansCJK-Regular.ttc: "Noto Sans CJK SC"
fc-match emoji       # → NotoColorEmoji.ttf: "Noto Color Emoji"

# Chromium 可用
chromium-browser --version

# 桌面图标存在
ls -la ~/Desktop/chromium.desktop

# 打开浏览器访问 https://www.xiaohongshu.com 验证中文显示
```

## WSL 特殊说明

WSL Ubuntu 桌面版使用 WSLg 运行图形程序，不需要额外 X Server。字体渲染依赖 Linux fontconfig。

- 方式 A（原生 Chromium）：字体配置影响浏览器内显示
- 方式 B（Windows Chrome）：浏览器进程运行在 Windows 侧，Linux 字体配置不影响浏览器内显示
