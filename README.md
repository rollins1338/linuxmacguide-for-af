# 🛠️ Building AudioForge Pro on macOS & Linux

Complete guide to building AudioForge Pro from source on Unix-based systems.

---

## 📋 Table of Contents

- [Prerequisites](#prerequisites)
- [Quick Build (TL;DR)](#quick-build-tldr)
- [macOS Build Guide](#macos-build-guide)
- [Linux Build Guide](#linux-build-guide)
- [Troubleshooting](#troubleshooting)
- [Advanced Configuration](#advanced-configuration)
- [Distribution](#distribution)

---

## Prerequisites

### System Requirements

| Requirement | Version | Check Command |
|-------------|---------|---------------|
| **Node.js** | 16.x - 20.x | `node --version` |
| **npm** | 8.x or higher | `npm --version` |
| **Python** | 2.7 or 3.x | `python --version` |
| **Git** | Any recent | `git --version` |

### Platform-Specific Tools

<details>
<summary><b>macOS</b></summary>

```bash
# Xcode Command Line Tools (required for native modules)
xcode-select --install

# Verify installation
xcode-select -p
# Should output: /Library/Developer/CommandLineTools
```

**Optional but Recommended:**
```bash
# Homebrew (for easier dependency management)
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

</details>

<details>
<summary><b>Linux (Debian/Ubuntu)</b></summary>

```bash
# Essential build tools
sudo apt update
sudo apt install -y build-essential git curl

# Required libraries for Electron
sudo apt install -y libgtk-3-0 libnotify4 libnss3 libxss1 \
  libxtst6 xdg-utils libatspi2.0-0 libdrm2 libgbm1 libxcb-dri3-0

# Additional dependencies for packaging
sudo apt install -y rpm fakeroot dpkg
```

</details>

<details>
<summary><b>Linux (Fedora/RHEL)</b></summary>

```bash
# Development tools
sudo dnf groupinstall -y "Development Tools"
sudo dnf install -y git curl

# Electron dependencies
sudo dnf install -y gtk3 libnotify nss libXScrnSaver libXtst xdg-utils \
  at-spi2-atk libdrm mesa-libgbm libxcb

# Packaging tools
sudo dnf install -y rpm-build dpkg
```

</details>

<details>
<summary><b>Linux (Arch/Manjaro)</b></summary>

```bash
# Base development
sudo pacman -S base-devel git curl

# Electron runtime dependencies
sudo pacman -S gtk3 libnotify nss libxss libxtst xdg-utils \
  at-spi2-core libdrm mesa libxcb

# Packaging
sudo pacman -S rpm-tools dpkg fakeroot
```

</details>

---

## Quick Build (TL;DR)

```bash
# Clone repository
git clone https://github.com/rollins1338/audio-merger.git
cd audio-merger

# Install dependencies
npm install

# Build for your platform
npm run electron:build

# Output: dist/AudioForge-Pro-1.3.0.dmg (macOS)
#     or: dist/AudioForge-Pro-1.3.0.AppImage (Linux)
```

**Build outputs:** `dist/` directory

---

## macOS Build Guide

### Step 1: Clone Repository

```bash
# Navigate to your projects directory
cd ~/Projects  # or wherever you keep projects

# Clone the repository
git clone https://github.com/rollins1338/audio-merger.git
cd audio-merger

# Verify you're on the main branch
git branch
```

### Step 2: Install Dependencies

```bash
# Install all npm dependencies
npm install

# This will install:
# - React dependencies
# - Electron
# - FFmpeg static binaries
# - TypeScript compiler
# - Build tools
```

**Expected output:**
```
added 1523 packages, and audited 1524 packages in 2m

156 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
```

### Step 3: Test Development Mode (Optional)

Before building, test that everything works:

```bash
# Start development server
npm run electron:dev
```

This will:
1. Start React dev server on `http://localhost:3000`
2. Launch Electron window automatically
3. Enable hot-reload for development

**Press `Ctrl+C` to stop when done testing**

### Step 4: Build the Application

```bash
# Clean build (recommended for first build)
npm run clean-dupes
npm run build
npm run electron:build

# Or use the combined command:
npm run electron:build
```

**Build Process Steps:**
1. `clean-dupes` - Removes duplicate dependencies
2. `build` - Compiles React app to `build/` directory
3. `electron:build` - Packages everything into distributable

**Progress Output:**
```
• electron-builder  version=24.9.1 os=darwin
• loaded configuration  file=package.json ("build" field)
• description is missed in the package.json  appPackageFile=package.json
• author is set to  author=AudioForge Team <support@audioforge.pro>
• packaging       platform=darwin arch=arm64 electron=28.1.0 appOutDir=dist/mac-arm64
• building        target=DMG arch=arm64 file=dist/AudioForge-Pro-1.3.0-arm64.dmg
• building block map  blockMapFile=dist/AudioForge-Pro-1.3.0-arm64.dmg.blockmap
```

### Step 5: Locate Your Build

```bash
# List build outputs
ls -lh dist/

# Typical output:
# AudioForge-Pro-1.3.0-arm64.dmg       (Apple Silicon)
# AudioForge-Pro-1.3.0-x64.dmg         (Intel Mac)
# AudioForge-Pro-1.3.0-universal.dmg   (Universal Binary)
```

### Step 6: Install & Test

```bash
# Open the DMG
open dist/AudioForge-Pro-1.3.0-arm64.dmg

# Or install directly
sudo cp -R dist/mac-arm64/AudioForge\ Pro.app /Applications/
```

**Test the installation:**
```bash
# Launch from terminal (to see console output)
open -a "AudioForge Pro"

# Or launch normally from Applications folder
```

### macOS-Specific Configuration

<details>
<summary><b>Building for Specific Architectures</b></summary>

```bash
# Apple Silicon (M1/M2/M3) only
npm run electron:build -- --mac --arm64

# Intel only
npm run electron:build -- --mac --x64

# Universal Binary (both architectures)
npm run electron:build -- --mac --universal
```

</details>

<details>
<summary><b>Code Signing (for distribution)</b></summary>

If you plan to distribute outside the App Store:

1. **Get Developer Certificate:**
   - Join Apple Developer Program ($99/year)
   - Create Developer ID Application certificate

2. **Set Environment Variables:**
   ```bash
   export CSC_LINK=/path/to/certificate.p12
   export CSC_KEY_PASSWORD=your_certificate_password
   export APPLE_ID=your@apple.id
   export APPLE_ID_PASSWORD=app_specific_password
   ```

3. **Build with Signing:**
   ```bash
   npm run electron:build
   ```

</details>

<details>
<summary><b>Notarization (for macOS 10.15+)</b></summary>

Required for Gatekeeper approval:

```bash
# Install notarization tool
npm install -D electron-notarize

# Build will auto-notarize if env vars are set
npm run electron:build
```

Add to `package.json`:
```json
{
  "build": {
    "mac": {
      "hardenedRuntime": true,
      "gatekeeperAssess": false,
      "entitlements": "build/entitlements.mac.plist",
      "entitlementsInherit": "build/entitlements.mac.plist"
    },
    "afterSign": "scripts/notarize.js"
  }
}
```

</details>

---

## Linux Build Guide

### Step 1: Clone Repository

```bash
# Navigate to your projects directory
cd ~/projects

# Clone the repository
git clone https://github.com/rollins1338/audio-merger.git
cd audio-merger

# Verify files
ls -la
```

### Step 2: Install Dependencies

```bash
# Install npm packages
npm install

# Verify FFmpeg binaries are installed
ls -la node_modules/ffmpeg-static/
ls -la node_modules/ffprobe-static/
```

**Common Issues:**
- If `npm install` fails with permission errors, use: `npm install --unsafe-perm`
- If you see Python errors, ensure Python is installed

### Step 3: Test Development Mode (Optional)

```bash
# Start development environment
npm run electron:dev
```

**Troubleshooting Display Issues:**
```bash
# If Electron window doesn't appear:
export DISPLAY=:0
npm run electron:dev

# For Wayland users:
export GDK_BACKEND=x11
npm run electron:dev
```

### Step 4: Build the Application

```bash
# Full build process
npm run electron:build

# Or build specific formats:
npm run electron:build -- --linux AppImage
npm run electron:build -- --linux deb
npm run electron:build -- --linux rpm
```

**Build Process:**
```
• electron-builder  version=24.9.1 os=linux
• loaded configuration  file=package.json ("build" field)
• packaging       platform=linux arch=x64 electron=28.1.0
• building        target=AppImage arch=x64 file=dist/AudioForge-Pro-1.3.0.AppImage
• building        target=deb arch=x64 file=dist/audioforge-pro_1.3.0_amd64.deb
```

### Step 5: Locate Your Build

```bash
# List build outputs
ls -lh dist/

# Typical outputs:
# AudioForge-Pro-1.3.0.AppImage
# audioforge-pro_1.3.0_amd64.deb
# audioforge-pro-1.3.0.x86_64.rpm
```

### Step 6: Install & Test

<details>
<summary><b>AppImage (Universal)</b></summary>

```bash
# Make executable
chmod +x dist/AudioForge-Pro-1.3.0.AppImage

# Run directly
./dist/AudioForge-Pro-1.3.0.AppImage

# Or move to applications
mkdir -p ~/.local/bin
cp dist/AudioForge-Pro-1.3.0.AppImage ~/.local/bin/
```

**Create Desktop Entry:**
```bash
cat > ~/.local/share/applications/audioforge-pro.desktop << 'EOF'
[Desktop Entry]
Name=AudioForge Pro
Comment=Professional Audio Merger
Exec=/home/$USER/.local/bin/AudioForge-Pro-1.3.0.AppImage
Icon=audioforge-pro
Type=Application
Categories=Audio;AudioVideo;
EOF
```

</details>

<details>
<summary><b>.deb Package (Debian/Ubuntu)</b></summary>

```bash
# Install
sudo dpkg -i dist/audioforge-pro_1.3.0_amd64.deb

# Fix missing dependencies (if any)
sudo apt install -f

# Launch
audioforge-pro
# Or from applications menu

# Uninstall
sudo apt remove audioforge-pro
```

</details>

<details>
<summary><b>.rpm Package (Fedora/RHEL)</b></summary>

```bash
# Install
sudo rpm -i dist/audioforge-pro-1.3.0.x86_64.rpm

# Or with dnf
sudo dnf install dist/audioforge-pro-1.3.0.x86_64.rpm

# Launch
audioforge-pro

# Uninstall
sudo rpm -e audioforge-pro
```

</details>

### Linux-Specific Configuration

<details>
<summary><b>Building for Different Architectures</b></summary>

```bash
# x64 (standard)
npm run electron:build -- --linux --x64

# ARM64 (Raspberry Pi 4, etc.)
npm run electron:build -- --linux --arm64

# ARMv7 (Raspberry Pi 3, older ARM devices)
npm run electron:build -- --linux --armv7l
```

</details>

<details>
<summary><b>Custom Icon Setup</b></summary>

Ensure you have proper icon files:

```bash
# Icon should be in public/
ls -la public/icon.png
# Should be 512x512 or higher PNG

# For better integration, create icon set:
mkdir -p build/icons
for size in 16 32 48 64 128 256 512; do
  convert public/icon.png -resize ${size}x${size} build/icons/${size}x${size}.png
done
```

</details>

<details>
<summary><b>Snap Package (Optional)</b></summary>

To build a Snap package:

1. **Install snapcraft:**
   ```bash
   sudo snap install snapcraft --classic
   ```

2. **Modify package.json:**
   ```json
   {
     "build": {
       "linux": {
         "target": ["AppImage", "deb", "snap"]
       }
     }
   }
   ```

3. **Build:**
   ```bash
   npm run electron:build
   ```

</details>

---

## Troubleshooting

### Common Build Errors

<details>
<summary><b>Error: `node-gyp` rebuild failed</b></summary>

**Cause:** Missing build tools

**macOS Solution:**
```bash
xcode-select --install
npm rebuild
```

**Linux Solution:**
```bash
sudo apt install build-essential  # Debian/Ubuntu
sudo dnf groupinstall "Development Tools"  # Fedora
npm rebuild
```

</details>

<details>
<summary><b>Error: `EACCES` permission denied</b></summary>

**Cause:** npm trying to install globally without permission

**Solution:**
```bash
# Fix npm permissions
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'

# Add to PATH (add to ~/.bashrc or ~/.zshrc)
export PATH=~/.npm-global/bin:$PATH

# Reinstall
npm install
```

</details>

<details>
<summary><b>Error: FFmpeg binaries not found</b></summary>

**Verification:**
```bash
# Check if binaries exist
ls -la node_modules/ffmpeg-static/
ls -la node_modules/ffprobe-static/

# Reinstall if missing
npm uninstall ffmpeg-static ffprobe-static
npm install ffmpeg-static@5.2.0 ffprobe-static@3.1.0
```

**Alternative (use system FFmpeg):**
```bash
# macOS
brew install ffmpeg

# Linux
sudo apt install ffmpeg  # Debian/Ubuntu
sudo dnf install ffmpeg  # Fedora
```

</details>

<details>
<summary><b>Error: `Electron failed to install correctly`</b></summary>

**Solution:**
```bash
# Clear cache and reinstall
rm -rf node_modules package-lock.json
npm cache clean --force
npm install
```

</details>

<details>
<summary><b>Error: Build succeeds but app won't launch</b></summary>

**Linux Troubleshooting:**
```bash
# Check library dependencies
ldd dist/linux-unpacked/audioforge-pro

# Install missing libraries
# Example for missing libgtk-3:
sudo apt install libgtk-3-0
```

**macOS Troubleshooting:**
```bash
# Check for codesign issues
codesign -dv --verbose=4 dist/mac/AudioForge\ Pro.app

# Remove quarantine attribute
xattr -cr dist/mac/AudioForge\ Pro.app
```

</details>

<details>
<summary><b>Warning: `deprecated` packages</b></summary>

**These warnings are usually safe to ignore:**
```
npm WARN deprecated stable@0.1.8: Modern JS already guarantees Array#sort() is a stable sort
```

**If concerned:**
```bash
# Audit dependencies
npm audit

# Fix automatically
npm audit fix
```

</details>

### Performance Issues

<details>
<summary><b>Build is very slow</b></summary>

**Optimization:**
```bash
# Use faster npm client
npm install -g pnpm
pnpm install
pnpm run electron:build

# Or use yarn
npm install -g yarn
yarn install
yarn electron:build

# Enable parallel builds
export ELECTRON_BUILDER_ALLOW_UNRESOLVED_DEPENDENCIES=true
npm run electron:build -- --parallel
```

</details>

<details>
<summary><b>Out of memory during build</b></summary>

**Increase Node.js memory:**
```bash
export NODE_OPTIONS="--max-old-space-size=4096"
npm run electron:build
```

</details>

---

## Advanced Configuration

### Custom Build Options

<details>
<summary><b>Modify Output Directory</b></summary>

Edit `package.json`:
```json
{
  "build": {
    "directories": {
      "output": "release"
    }
  }
}
```

</details>

<details>
<summary><b>Add Custom Build Scripts</b></summary>

Create `scripts/build.sh`:
```bash
#!/bin/bash
set -e

echo "🧹 Cleaning previous builds..."
rm -rf dist/ build/

echo "📦 Installing dependencies..."
npm ci

echo "🔨 Building React app..."
npm run build

echo "📱 Packaging Electron app..."
npm run electron:build

echo "✅ Build complete! Check dist/ folder"
ls -lh dist/
```

Make executable:
```bash
chmod +x scripts/build.sh
./scripts/build.sh
```

</details>

<details>
<summary><b>Environment-Specific Builds</b></summary>

Create `.env.production`:
```bash
REACT_APP_ENV=production
REACT_APP_API_URL=https://api.audioforge.pro
GENERATE_SOURCEMAP=false
```

Build with environment:
```bash
source .env.production
npm run electron:build
```

</details>

### CI/CD Integration

<details>
<summary><b>GitHub Actions Build Matrix</b></summary>

Create `.github/workflows/build.yml`:
```yaml
name: Build AudioForge Pro

on:
  push:
    tags:
      - 'v*'

jobs:
  build:
    strategy:
      matrix:
        os: [macos-latest, ubuntu-latest]
    
    runs-on: ${{ matrix.os }}
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: 18
          
      - name: Install dependencies
        run: npm ci
        
      - name: Build
        run: npm run electron:build
        
      - name: Upload artifacts
        uses: actions/upload-artifact@v3
        with:
          name: ${{ matrix.os }}-build
          path: dist/*
```

</details>

---

## Distribution

### Creating Release Packages

<details>
<summary><b>Version Bumping</b></summary>

```bash
# Update version in package.json
npm version patch  # 1.3.0 -> 1.3.1
npm version minor  # 1.3.1 -> 1.4.0
npm version major  # 1.4.0 -> 2.0.0

# Rebuild with new version
npm run electron:build
```

</details>

<details>
<summary><b>Generating Checksums</b></summary>

```bash
# macOS
shasum -a 256 dist/*.dmg > dist/checksums.txt

# Linux
sha256sum dist/*.AppImage dist/*.deb > dist/checksums.txt

# Verify
cat dist/checksums.txt
```

</details>

<details>
<summary><b>Upload to GitHub Releases</b></summary>

```bash
# Install GitHub CLI
brew install gh  # macOS
sudo apt install gh  # Ubuntu

# Create release
gh release create v1.3.0 \
  dist/AudioForge-Pro-1.3.0.dmg \
  dist/AudioForge-Pro-1.3.0.AppImage \
  dist/audioforge-pro_1.3.0_amd64.deb \
  --title "AudioForge Pro v1.3.0" \
  --notes "Release notes here"
```

</details>

---

## Build Verification Checklist

After building, verify:

- [ ] Application launches without errors
- [ ] FFmpeg version detected in About panel
- [ ] File uploads work (both files and folders)
- [ ] Drag & drop functionality works
- [ ] Merge process completes successfully
- [ ] Output files play correctly
- [ ] Application icon displays properly
- [ ] Menu items function correctly
- [ ] Quit/Close works properly

**Test Build:**
```bash
# macOS
open dist/mac/AudioForge\ Pro.app

# Linux AppImage
./dist/AudioForge-Pro-1.3.0.AppImage
```

---

## Additional Resources

- **Electron Builder Docs**: https://www.electron.build/
- **Node.js Downloads**: https://nodejs.org/
- **FFmpeg Documentation**: https://ffmpeg.org/documentation.html
- **Project Repository**: https://github.com/rollins1338/audio-merger
- **Issue Tracker**: https://github.com/rollins1338/audio-merger/issues

---

## Getting Help

If you encounter issues:

1. **Check this guide** thoroughly
2. **Search existing issues**: https://github.com/rollins1338/audio-merger/issues
3. **Create new issue** with:
   - OS and version
   - Node.js version (`node --version`)
   - npm version (`npm --version`)
   - Full error output
   - Build command used

---

<div align="center">

**Happy Building! 🚀**


</div>
