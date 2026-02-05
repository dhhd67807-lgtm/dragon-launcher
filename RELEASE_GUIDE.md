# Dragon Launcher v1.0.1 Release Guide

## Version Information

- **Version**: 1.0.1
- **Release Date**: February 5, 2026
- **Platforms**: macOS, Windows

## What's New in v1.0.1

### 🎨 Rebranding

- Rebranded from "Modrinth App" to "Dragon Launcher"
- New dragon logo with black background and rounded corners
- Updated all app icons for macOS and Windows
- Changed app identifier to `DragonLauncher`
- Updated deep-link scheme to `dragonlauncher://`

### ✨ New Features

- **Playing As Section**: Added account display in right sidebar showing current Minecraft account
- **Offline Mode Support**: Quick offline mode login with customizable username
- **Inline Username Editing**: Edit offline username directly with Save/Cancel buttons
- **Account Dropdown Menu**: Easy access to "Add another account" and "Logout" options
- **Login Requirements**: Users must sign in (Microsoft or Offline) before using launcher features

### 🐛 Bug Fixes

- Fixed logout button not working for offline mode accounts
- Fixed instance creation modal not opening from sidebar plus button
- Fixed production build issues with sidebar navigation
- Fixed login modal z-index issues
- Fixed library page hanging/black screen issue

### 🎯 UI Improvements

- Increased default window width to 1700px
- Removed Skins tab
- Compact and clean UI design
- Better visual hierarchy in sidebar

### 🔧 Technical Changes

- Wrapped async components in `<Suspense>` for proper loading
- Improved modal handling with retry logic
- Better error handling for offline mode
- Updated CSS to prevent tree-shaking issues in production builds

## Pre-Release Checklist

### 1. Update Version Numbers

- [x] Updated `apps/app-frontend/package.json` to 1.0.1
- [ ] Verify version in tauri.conf.json references package.json correctly

### 2. Build the Application

#### For macOS:

```bash
cd apps/app
pnpm tauri build
```

The build will be located at:

- `apps/app/target/release/bundle/dmg/Dragon Launcher_1.0.1_aarch64.dmg` (Apple Silicon)
- `apps/app/target/release/bundle/dmg/Dragon Launcher_1.0.1_x64.dmg` (Intel)

#### For Windows:

```bash
cd apps/app
pnpm tauri build
```

The build will be located at:

- `apps/app/target/release/bundle/nsis/Dragon Launcher_1.0.1_x64-setup.exe`

### 3. Create GitHub Repository

1. Go to https://github.com/new
2. Repository name: `dragon-launcher`
3. Description: "A custom Minecraft launcher based on Modrinth"
4. Choose Public or Private
5. Don't initialize with README (we already have one)
6. Click "Create repository"

### 4. Push Code to GitHub

```bash
# Initialize git if not already done
git init

# Add all files
git add .

# Commit changes
git commit -m "Release v1.0.1 - Dragon Launcher initial release"

# Add remote (replace YOUR_USERNAME with your GitHub username)
git remote add origin https://github.com/YOUR_USERNAME/dragon-launcher.git

# Push to GitHub
git branch -M main
git push -u origin main
```

### 5. Create GitHub Release

1. Go to your repository on GitHub
2. Click "Releases" → "Create a new release"
3. Click "Choose a tag" → Type `v1.0.1` → "Create new tag"
4. Release title: `Dragon Launcher v1.0.1`
5. Description: Copy from CHANGELOG.md (see below)
6. Upload the build files:
   - macOS DMG files
   - Windows installer EXE
7. Click "Publish release"

## Release Notes Template

```markdown
# Dragon Launcher v1.0.1

Dragon Launcher is a custom Minecraft launcher forked from Modrinth App, featuring a new dragon-themed design and enhanced offline mode support.

## 🎨 Highlights

- **New Branding**: Complete rebrand with dragon logo and custom icons
- **Offline Mode**: Full support for offline/cracked Minecraft with customizable usernames
- **Account Management**: Easy account switching and management in sidebar
- **Login Required**: Secure launcher requiring sign-in (Microsoft or Offline) before use

## 📥 Downloads

### macOS

- [Dragon Launcher (Apple Silicon)](link-to-aarch64.dmg)
- [Dragon Launcher (Intel)](link-to-x64.dmg)

### Windows

- [Dragon Launcher Setup](link-to-setup.exe)

## 🚀 Installation

### macOS

1. Download the appropriate DMG for your Mac
2. Open the DMG file
3. Drag Dragon Launcher to Applications
4. Right-click and select "Open" (first time only due to Gatekeeper)

### Windows

1. Download the setup executable
2. Run the installer
3. Follow the installation wizard
4. Launch Dragon Launcher from Start Menu

## ⚠️ Important Notes

- This is a custom launcher and is not affiliated with Modrinth or Mojang
- Offline mode is for testing purposes only
- Microsoft account login requires valid Minecraft license

## 🐛 Known Issues

- None reported yet

## 📝 Full Changelog

See [CHANGELOG.md](CHANGELOG.md) for complete list of changes.
```

## Post-Release

1. Test the release downloads on both platforms
2. Update any documentation
3. Announce on Discord/social media
4. Monitor for bug reports

## Build Commands Reference

```bash
# Development
pnpm app:dev

# Production build
cd apps/app
pnpm tauri build

# Clean build (if needed)
cargo clean
pnpm tauri build
```

## Troubleshooting

### Build fails on macOS

- Make sure Xcode Command Line Tools are installed: `xcode-select --install`
- Check Rust is up to date: `rustup update`

### Build fails on Windows

- Install Visual Studio Build Tools
- Make sure WebView2 is installed

### Icons not updating

- Clear icon cache: `rm -rf apps/app/target`
- Rebuild: `pnpm tauri build`
