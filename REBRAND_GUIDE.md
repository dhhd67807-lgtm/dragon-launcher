# Modrinth → Obsidian Rebranding Guide

This guide outlines all the changes needed to rebrand Modrinth to Obsidian.

## ⚠️ Important Notes

1. **Legal Considerations**: Ensure you have the right to fork and rebrand this project according to the license
2. **Backend Required**: You'll need to either:
   - Run your own backend API server (labrinth)
   - Point to Modrinth's production API (but this may violate their terms)
3. **Trademark**: "Obsidian" is already used by Obsidian.md - consider a different name to avoid conflicts

## 🎯 Key Files to Modify

### 1. App Configuration
- `apps/app/tauri.conf.json` - App name, identifier, window titles
- `apps/app/package.json` - Package name
- `Cargo.toml` - Repository URL and workspace metadata

### 2. Environment Variables
- `packages/app-lib/.env.local` - API URLs (point to your backend)
- `packages/app-lib/.env.prod` - Production API URLs
- `packages/app-lib/.env.staging` - Staging API URLs

### 3. Branding Assets
- `apps/app/icons/` - Replace all app icons
- `apps/app-frontend/src/assets/` - Logo, images, branding
- `apps/frontend/src/assets/` - Web interface assets

### 4. Code References
Search and replace "Modrinth" → "Obsidian" in:
- All Rust files (`packages/app-lib/src/**/*.rs`)
- All TypeScript/Vue files (`apps/app-frontend/src/**/*`)
- All configuration files
- README files and documentation

### 5. URLs and Domains
- Deep link scheme: `modrinth://` → `obsidian://`
- API endpoints in environment files
- CSP (Content Security Policy) domains in tauri.conf.json

### 6. File Associations
- `.mrpack` → Consider `.obpack` or keep `.mrpack` for compatibility

## 🚀 Quick Start Rebranding

### Step 1: Update App Identity

Edit `apps/app/tauri.conf.json`:
```json
{
  "productName": "Obsidian App",
  "mainBinaryName": "Obsidian App",
  "identifier": "ObsidianApp",
  "plugins": {
    "deep-link": {
      "desktop": {
        "schemes": ["obsidian"]
      }
    }
  },
  "app": {
    "windows": [{
      "title": "Obsidian App"
    }]
  }
}
```

### Step 2: Update Package Names

Edit `apps/app/package.json`:
```json
{
  "name": "@obsidian/app"
}
```

### Step 3: Update Environment Variables

Edit `packages/app-lib/.env.local`:
```env
# Option A: Point to your own backend (requires running labrinth)
MODRINTH_URL=http://localhost:3000/
MODRINTH_API_URL=http://127.0.0.1:8000/v2/
MODRINTH_API_URL_V3=http://127.0.0.1:8000/v3/
MODRINTH_SOCKET_URL=ws://127.0.0.1:8000/
MODRINTH_LAUNCHER_META_URL=https://launcher-meta.modrinth.com/

# Option B: Use Modrinth's production API (may violate ToS)
# MODRINTH_URL=https://modrinth.com/
# MODRINTH_API_URL=https://api.modrinth.com/v2/
# MODRINTH_API_URL_V3=https://api.modrinth.com/v3/
# MODRINTH_SOCKET_URL=wss://api.modrinth.com/
# MODRINTH_LAUNCHER_META_URL=https://launcher-meta.modrinth.com/
```

### Step 4: Replace Icons and Logos

Replace files in:
- `apps/app/icons/` - App icons (PNG, ICO, ICNS formats)
- `apps/app-frontend/src/assets/images/` - In-app branding
- `apps/frontend/src/assets/` - Web interface branding

### Step 5: Global Text Replacement

Run these commands to replace text throughout the codebase:

```bash
# Replace in Rust files
find packages/app-lib/src -type f -name "*.rs" -exec sed -i '' 's/Modrinth/Obsidian/g' {} +

# Replace in TypeScript/Vue files
find apps/app-frontend/src -type f \( -name "*.ts" -o -name "*.vue" \) -exec sed -i '' 's/Modrinth/Obsidian/g' {} +

# Replace in config files
sed -i '' 's/modrinth/obsidian/g' apps/app/tauri.conf.json
sed -i '' 's/modrinth/obsidian/g' apps/app/package.json

# Update deep link scheme
find . -type f -name "*.rs" -exec sed -i '' 's/modrinth:\/\//obsidian:\/\//g' {} +
```

### Step 6: Update CSP and Security Settings

Edit `apps/app/tauri.conf.json` security section to allow your domains:
```json
{
  "app": {
    "security": {
      "csp": {
        "connect-src": "ipc: http://ipc.localhost https://yourdomain.com https://*.yourdomain.com ..."
      }
    }
  }
}
```

## 📝 Detailed File List

### Critical Files (Must Change)
1. `apps/app/tauri.conf.json` - App configuration
2. `apps/app/package.json` - Package metadata
3. `packages/app-lib/.env.local` - Development environment
4. `packages/app-lib/.env.prod` - Production environment
5. `Cargo.toml` - Workspace configuration
6. `README.md` - Project documentation

### Branding Files (Visual Identity)
1. `apps/app/icons/*` - All icon files
2. `apps/app-frontend/src/assets/images/logo.svg`
3. `apps/app-frontend/src/assets/images/wordmark.svg`
4. `apps/frontend/public/favicon.ico`

### Code Files (Text References)
- All `.rs` files in `packages/app-lib/src/`
- All `.vue` files in `apps/app-frontend/src/`
- All `.ts` files in `apps/app-frontend/src/`

## 🎨 Color Scheme

Modrinth uses a green/teal color scheme. To change to Obsidian's purple theme:

1. Find CSS variables in `apps/app-frontend/src/assets/stylesheets/`
2. Update color values:
   - Primary: `#1bd96a` → Your purple color
   - Accent colors throughout the stylesheets

## 🔧 Backend Setup

If running your own backend:

1. Clone and set up the labrinth API server
2. Set up a PostgreSQL database
3. Configure Redis for caching
4. Update environment variables to point to your backend
5. Set up CDN for mod file hosting

## ⚠️ Legal Compliance

1. Review the LICENSE file in the repository
2. Update copyright notices
3. Add attribution to Modrinth if required by license
4. Ensure you're not violating any trademarks
5. Don't use Modrinth's API without permission

## 🚦 Testing Checklist

After rebranding:
- [ ] App launches with new name
- [ ] Icons display correctly
- [ ] Deep links work with new scheme
- [ ] API connections work
- [ ] File associations work
- [ ] Updater works (if using)
- [ ] All UI text updated
- [ ] No "Modrinth" references remain

## 📚 Additional Resources

- Tauri Documentation: https://tauri.app/
- Rust Documentation: https://doc.rust-lang.org/
- Vue.js Documentation: https://vuejs.org/

---

**Note**: This is a complex rebrand involving hundreds of files. Consider using automated tools and thorough testing before releasing.
