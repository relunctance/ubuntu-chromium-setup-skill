# ubuntu-chromium-setup

> Install Chromium on Ubuntu / WSL Ubuntu Desktop + configure Chinese fonts to fix garbled text (e.g., Xiaohongshu)

## About | 关于

`ubuntu-chromium-setup` provides two ways to install Chromium on Ubuntu/WSL Ubuntu Desktop and configure Chinese fonts to fix garbled text on Chinese websites (e.g., Xiaohongshu).

`ubuntu-chromium-setup` 提供两种方式在 Ubuntu / WSL Ubuntu 桌面版上安装 Chromium，并配置中文字体解决小红书等中文页面的乱码问题。

## Triggers

- ubuntu install chrome
- ubuntu install chromium
- wsl ubuntu chrome
- linux chinese garbled
- 小红书乱码
- fontconfig fonts
- ubuntu fonts

## Quick Setup

### Method A: Native Chromium

```bash
# Install
sudo apt update && sudo apt install chromium-browser

# Desktop icon
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

### Method B: Windows Chrome (WSL only)

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

### Font Configuration (Fix Chinese Garbled Text)

```bash
# 1. Install Noto CJK fonts
sudo apt install fonts-noto-cjk

# 2. Configure fontconfig
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

# 3. Refresh font cache
fc-cache -f

# 4. Verify
fc-match sans-serif  # → Noto Sans CJK SC
fc-match emoji       # → Noto Color Emoji

# 5. Set locale
echo 'export LANG=zh_CN.UTF-8' >> ~/.bashrc
source ~/.bashrc
```

## Verification

```bash
# Font config
fc-match sans-serif  # → NotoSansCJK-Regular.ttc: "Noto Sans CJK SC"
fc-match emoji       # → NotoColorEmoji.ttf: "Noto Color Emoji"

# Chromium
chromium-browser --version

# Desktop icon
ls -la ~/Desktop/chromium.desktop

# Test: open https://www.xiaohongshu.com in browser
```

## Pitfalls

| Issue | Cause | Solution |
|-------|-------|----------|
| locale is `C.UTF-8` | Chromium font fallback fails | `sudo locale-gen zh_CN.UTF-8` + add to bashrc |
| wqy-zenhei font incomplete | Many CJK chars missing | Install `fonts-noto-cjk` |
| `.desktop` ignores shell env vars | Must pass `LANG` explicitly | Set in Exec line |
| `<blank>false</blank>` in fonts.conf | Invalid element, silent failure | Remove it |
| WSL git push timeout | Network to GitHub blocked | Configure `git config --global http.proxy http://192.168.1.109:10808` |

## Installation

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

### Manual (GitHub)
```bash
git clone https://github.com/relunctance/ubuntu-chromium-setup.git
```

## Platforms

| Platform | Status |
|----------|--------|
| OpenClaw | ✅ Supported |
| Claude Code | ✅ Supported |
| Hermes | ✅ Supported |

## See Also

- [中文文档 (Chinese README)](README_zh.md)
