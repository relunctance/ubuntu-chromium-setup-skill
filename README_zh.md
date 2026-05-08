# ubuntu-chromium-setup

> Ubuntu / WSL Ubuntu 桌面版安装 Chromium + 配置中文字体，解决小红书等中文页面乱码

## 关于 | About

`ubuntu-chromium-setup` 提供两种方式在 Ubuntu / WSL Ubuntu 桌面版上安装 Chromium，并配置中文字体解决小红书等中文页面的乱码问题。

`ubuntu-chromium-setup` provides two ways to install Chromium on Ubuntu/WSL Ubuntu Desktop and configure Chinese fonts to fix garbled text on Chinese websites (e.g., Xiaohongshu).

## 触发条件

- ubuntu 安装 chrome
- ubuntu 安装 chromium
- wsl ubuntu chrome
- linux 中文乱码
- 小红书 乱码
- fontconfig 字体配置
- ubuntu 字体

## 快速安装

### 方式 A：安装原生 Chromium

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
# 1. 安装思源黑体
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
fc-match sans-serif  # → Noto Sans CJK SC
fc-match emoji       # → Noto Color Emoji

# 5. 设置 locale
echo 'export LANG=zh_CN.UTF-8' >> ~/.bashrc
source ~/.bashrc
```

## 验证步骤

```bash
# 字体配置正确
fc-match sans-serif  # → Noto Sans CJK SC
fc-match emoji       # → Noto Color Emoji

# Chromium 可用
chromium-browser --version

# 桌面图标存在
ls -la ~/Desktop/chromium.desktop

# 打开浏览器访问 https://www.xiaohongshu.com 验证中文显示
```

## 踩坑记录

| 坑 | 说明 | 解决方案 |
|---|---|---|
| locale 是 `C.UTF-8` | Chromium 字体 fallback 失效 | `sudo locale-gen zh_CN.UTF-8` + 加 bashrc |
| wqy-zenhei 字体不全 | 很多 CJK 字符没有 | 安装 `fonts-noto-cjk` |
| `.desktop` 不继承 shell 环境变量 | 必须显式传 `LANG=zh_CN.UTF-8` | Exec 里面写死 env |
| `fonts.conf` 写错元素静默失败 | `<blank>false</blank>` 是非法元素 | 删除该元素 |
| WSL git push 超时 | 网络不通 GitHub | 配置 http.proxy |

## 安装

### OpenClaw
```bash
clawhub install ubuntu-chromium-setup
```

### Claude Code
```bash
mkdir -p ~/claude/skills/ubuntu-chromium-setup
cp SKILL.md ~/claude/skills/ubuntu-chromium-setup/
```

### Hermes
```bash
mkdir -p ~/.hermes/skills/ubuntu-chromium-setup
cp SKILL.md ~/.hermes/skills/ubuntu-chromium-setup/
```

### 手动（GitHub）
```bash
git clone https://github.com/relunctance/ubuntu-chromium-setup.git
```

## 平台支持

| 平台 | 状态 |
|------|------|
| OpenClaw | ✅ 支持 |
| Claude Code | ✅ 支持 |
| Hermes | ✅ 支持 |

## 相关文档

- [English README](README.md)
