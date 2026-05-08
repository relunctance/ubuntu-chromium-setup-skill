# ubuntu-chromium-setup

> 在 Ubuntu / WSL Ubuntu 桌面版上安装 Chromium + 配置中文字体的完整方案

## 包含内容

- 方式 A：安装 Linux 原生 Chromium（推荐）
- 方式 B：复用 Windows Chrome（WSL 专用）
- 桌面图标创建
- 字体配置（小红书等中文页面乱码问题）

## 快速开始

```bash
# 方式 A：安装原生 Chromium
sudo apt install chromium-browser

# 方式 B：WSL 只想用 Windows Chrome（无需安装）
# 跳过安装步骤，直接看「桌面图标」部分

# 字体配置（两种方式都需要）
sudo apt install fonts-noto-cjk
```

---

## 方式 A：安装 Linux Chromium

### 安装

```bash
sudo apt update
sudo apt install chromium-browser
```

### 验证

```bash
which chromium-browser
chromium-browser --version
```

### 桌面图标

```bash
cat > ~/Desktop/chromium.desktop << 'EOF'
[Desktop Entry]
Version=1.0
Type=Application
Name=Chromium
Comment=Chromium Web Browser
Icon=chromium-browser
Exec=env LANG=zh_CN.UTF-8 LC_ALL=zh_CN.UTF-8 chromium-browser %U
Terminal=false
StartupNotify=true
MimeType=text/html;text/xml;application/xhtml_xml;
EOF
chmod +x ~/Desktop/chromium.desktop
```

---

## 方式 B：WSL 复用 Windows Chrome

### Windows Chrome 路径

```
C:\Program Files\Google\Chrome\Application\chrome.exe
→ WSL 路径：/mnt/c/Program Files/Google/Chrome/Application/chrome.exe
```

### 桌面图标

```bash
cat > ~/Desktop/chromium.desktop << 'EOF'
[Desktop Entry]
Version=1.0
Type=Application
Name=Google Chrome
Comment=Google Chrome Web Browser (Windows)
Icon=/mnt/c/Program Files/Google/Chrome/Application/chrome.exe
Exec="/mnt/c/Program Files/Google/Chrome/Application/chrome.exe" %U
Terminal=false
StartupNotify=true
MimeType=text/html;text/xml;application/xhtml_xml;
EOF
chmod +x ~/Desktop/chromium.desktop
```

> 注意：WSL 里调用 Windows exe 本质上还是运行 Windows Chrome

---

## 字体配置（解决中文乱码）

### 问题症状

- 小红书等中文页面显示方块 / 乱码
- 原因：默认字体文泉驿（wqy-zenhei）覆盖不全，很多网络字体没有

### 踩坑记录

| 坑 | 说明 |
|---|---|
| locale 是 `C.UTF-8` | 不支持中文 locale，Chromium 字体 fallback 失效 |
| wqy-zenhei 字体不全 | 很多 CJK 字符没有，尤其是 emoji 和网络字体 |
| `.desktop` 文件不继承 shell 环境变量 | 必须显式传 `LANG=zh_CN.UTF-8` |
| `fonts.conf` 写错元素会静默失败 | `<blank>false</blank>` 是非法元素 |

### 安装思源黑体

```bash
sudo apt install fonts-noto-cjk
```

### 配置 fontconfig

```bash
mkdir -p ~/.config/fontconfig
```

```bash
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
```

### 刷新字体缓存

```bash
fc-cache -f
```

### 验证字体

```bash
fc-match sans-serif      # 期望：NotoSansCJK-Regular.ttc: "Noto Sans CJK SC" "Regular"
fc-match emoji           # 期望：NotoColorEmoji.ttf: "Noto Color Emoji" "Regular"
```

### 设置系统 locale

```bash
# 检查是否已有 zh_CN.UTF-8
locale -a | grep zh_CN

# 没有的话生成（需要 sudo）
sudo locale-gen zh_CN.UTF-8

# 加到 ~/.bashrc
echo 'export LANG=zh_CN.UTF-8' >> ~/.bashrc
source ~/.bashrc
```

### 更新 .desktop 使用正确字体路径

```bash
cat > ~/Desktop/chromium.desktop << 'EOF'
[Desktop Entry]
Version=1.0
Type=Application
Name=Chromium
Comment=Chromium Web Browser
Icon=chromium-browser
Exec=env LANG=zh_CN.UTF-8 LC_ALL=zh_CN.UTF-8 FONTCONFIG_PATH=/home/gql/.config/fontconfig chromium-browser %U
Terminal=false
StartupNotify=true
MimeType=text/html;text/xml;application/xhtml_xml;
EOF
chmod +x ~/Desktop/chromium.desktop
```

---

## 验证完整流程

```bash
# 1. 字体配置正确
fc-match sans-serif     # → Noto Sans CJK SC
fc-match emoji          # → Noto Color Emoji

# 2. Chromium 可用
chromium-browser --version

# 3. 桌面图标存在
ls -la ~/Desktop/chromium.desktop

# 4. 打开浏览器访问 https://www.xiaohongshu.com 验证中文显示
```

---

## WSL 特殊说明

WSL Ubuntu 桌面版使用 WSLg 运行图形程序，不需要额外 X Server。但字体渲染依赖 Linux fontconfig，所以字体配置是必须的。

如果用方式 B（Windows Chrome），浏览器进程运行在 Windows 侧，字体渲染由 Windows 负责，此时 Linux 字体配置不影响浏览器内显示。
