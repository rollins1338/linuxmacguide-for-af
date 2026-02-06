# 🛠️ Building AudioForge Pro

Simple guide to build from source on macOS & Linux.

---

## Quick Start

```bash
git clone https://github.com/rollins1338/audio-merger.git
cd audio-merger
npm install
npm run electron:build
```

**Output:** `dist/` folder contains your build

---

## Prerequisites

**Required:**
- Node.js 16+ ([download](https://nodejs.org/))
- npm 8+
- Git

**Check versions:**
```bash
node --version  # Should be v16+ 
npm --version   # Should be 8+
```

### Platform Setup

**macOS:**
```bash
xcode-select --install
```

**Ubuntu/Debian:**
```bash
sudo apt update
sudo apt install -y build-essential git libgtk-3-0
```

**Fedora:**
```bash
sudo dnf groupinstall -y "Development Tools"
sudo dnf install -y git gtk3
```

---

## Building

### 1. Clone & Install

```bash
git clone https://github.com/rollins1338/audio-merger.git
cd audio-merger
npm install
```

### 2. Build

```bash
# Build for your current platform
npm run electron:build
```

**Build specific format:**
```bash
# macOS only
npm run electron:build -- --mac

# Linux AppImage
npm run electron:build -- --linux AppImage

# Linux .deb package
npm run electron:build -- --linux deb
```

### 3. Find Your Build

```bash
ls dist/

# macOS outputs:
# - AudioForge-Pro-1.3.0.dmg

# Linux outputs:
# - AudioForge-Pro-1.3.0.AppImage
# - audioforge-pro_1.3.0_amd64.deb
```

---

## Installation

### macOS
```bash
open dist/AudioForge-Pro-1.3.0.dmg
# Drag to Applications folder
```

### Linux (AppImage)
```bash
chmod +x dist/AudioForge-Pro-1.3.0.AppImage
./dist/AudioForge-Pro-1.3.0.AppImage
```

### Linux (.deb)
```bash
sudo dpkg -i dist/audioforge-pro_1.3.0_amd64.deb
```

---

## Common Issues

**Build fails with "node-gyp error":**
```bash
# macOS
xcode-select --install

# Linux
sudo apt install build-essential
```

**Permission errors:**
```bash
npm install --unsafe-perm
```

**Out of memory:**
```bash
export NODE_OPTIONS="--max-old-space-size=4096"
npm run electron:build
```

**Clean rebuild:**
```bash
rm -rf node_modules package-lock.json
npm install
npm run electron:build
```

---

## Development Mode

Test before building:

```bash
npm run electron:dev
```

Press `Ctrl+C` to stop.

---

## Need Help?

- **Issues:** https://github.com/rollins1338/audio-merger/issues
- **Docs:** [Electron Builder](https://www.electron.build/)

---

Built with ❤️ by [RxxFii](https://github.com/rollins1338)
