# Dragon Launcher Auto-Update Setup Guide

## Current Status

❌ Auto-updates are **NOT enabled** yet

## How Auto-Updates Work

When users open Dragon Launcher, it checks for new versions and shows a notification:

- "Update available" toast with "Update" button
- Clicking "Update" downloads and installs the new version
- After installation, user clicks "Restart" to apply the update

## Setup Options

### Option 1: GitHub Releases (Recommended - Free & Simple)

Use GitHub Releases to host your updates.

#### Step 1: Update Tauri Config

Add this to `apps/app/tauri.conf.json`:

```json
{
	"plugins": {
		"updater": {
			"active": true,
			"endpoints": ["https://github.com/dhhd67807-lgtm/dragon-launcher/releases/latest/download/latest.json"],
			"dialog": false,
			"pubkey": "YOUR_PUBLIC_KEY_HERE"
		}
	}
}
```

#### Step 2: Generate Update Keys

```bash
# Install Tauri CLI if not already installed
cargo install tauri-cli

# Generate keypair for signing updates
pnpm tauri signer generate -w ~/.tauri/dragonlauncher.key

# This creates:
# - Private key: ~/.tauri/dragonlauncher.key (keep secret!)
# - Public key: printed to console (add to tauri.conf.json)
```

#### Step 3: Update GitHub Workflow

The workflow needs to:

1. Sign the update files
2. Create a `latest.json` manifest
3. Upload to GitHub Release

Add to `.github/workflows/dragon-launcher-build.yml`:

```yaml
- name: Sign updates
  env:
    TAURI_SIGNING_PRIVATE_KEY: ${{ secrets.TAURI_PRIVATE_KEY }}
    TAURI_SIGNING_PRIVATE_KEY_PASSWORD: ${{ secrets.TAURI_KEY_PASSWORD }}
  run: |
    # Signing happens automatically during build

- name: Create update manifest
  run: |
    # Create latest.json for updater
    cat > latest.json <<EOF
    {
      "version": "v1.0.1",
      "notes": "Update available for Dragon Launcher",
      "pub_date": "$(date -u +%Y-%m-%dT%H:%M:%SZ)",
      "platforms": {
        "darwin-x86_64": {
          "signature": "$(cat target/universal-apple-darwin/release/bundle/macos/Dragon\ Launcher.app.tar.gz.sig)",
          "url": "https://github.com/dhhd67807-lgtm/dragon-launcher/releases/download/v1.0.1/Dragon-Launcher-macOS.app.tar.gz"
        },
        "darwin-aarch64": {
          "signature": "$(cat target/universal-apple-darwin/release/bundle/macos/Dragon\ Launcher.app.tar.gz.sig)",
          "url": "https://github.com/dhhd67807-lgtm/dragon-launcher/releases/download/v1.0.1/Dragon-Launcher-macOS.app.tar.gz"
        },
        "windows-x86_64": {
          "signature": "$(cat target/release/bundle/nsis/Dragon\ Launcher_1.0.1_x64-setup.nsis.zip.sig)",
          "url": "https://github.com/dhhd67807-lgtm/dragon-launcher/releases/download/v1.0.1/Dragon-Launcher-Windows-setup.nsis.zip"
        }
      }
    }
    EOF

- name: Upload to release
  uses: softprops/action-gh-release@v1
  with:
    files: |
      latest.json
      target/universal-apple-darwin/release/bundle/macos/*.app.tar.gz
      target/universal-apple-darwin/release/bundle/macos/*.app.tar.gz.sig
      target/release/bundle/nsis/*.nsis.zip
      target/release/bundle/nsis/*.nsis.zip.sig
```

#### Step 4: Add Secrets to GitHub

1. Go to: https://github.com/dhhd67807-lgtm/dragon-launcher/settings/secrets/actions
2. Click "New repository secret"
3. Add:
   - `TAURI_PRIVATE_KEY`: Content of `~/.tauri/dragonlauncher.key`
   - `TAURI_KEY_PASSWORD`: Password you used when generating the key

### Option 2: Custom Update Server

Host your own update server with the `updates.json` manifest.

**Pros:**

- Full control
- Can add analytics
- Custom update logic

**Cons:**

- Need to host a server
- More complex setup
- Costs money

## How Users Experience Updates

### When Update is Available:

1. User opens Dragon Launcher
2. App checks for updates in background
3. Toast notification appears: "Update available - Dragon Launcher v1.0.2"
4. User clicks "Update" button
5. Download starts (shows progress)
6. When complete, shows "Restart to apply update"
7. User clicks "Restart"
8. App closes and reopens with new version

### Update Check Frequency:

The app checks for updates:

- On startup
- Every time the app window gains focus (if 1+ hours since last check)

## Testing Updates

### Test Update Flow:

1. Build version 1.0.1 and install it
2. Create a new release v1.0.2 with updated files
3. Open the installed v1.0.1 app
4. It should detect v1.0.2 and show update notification

### Manual Update Check:

Users can manually check for updates in Settings → About

## Important Notes

### Security:

- ⚠️ **NEVER commit your private key to git**
- Store it in GitHub Secrets only
- The public key in tauri.conf.json is safe to commit

### Version Format:

- Must use semantic versioning: `v1.0.1`, `v1.0.2`, etc.
- The `v` prefix is required

### File Sizes:

- Updates download the `.app.tar.gz` (macOS) or `.nsis.zip` (Windows)
- These are smaller than the full installers
- Typical size: 50-100 MB

### Signature Files:

- `.sig` files are automatically generated during build
- They verify the update hasn't been tampered with
- Required for security

## Disabling Auto-Updates

If you don't want auto-updates, you can:

1. Remove the `updater` plugin from `tauri.conf.json`
2. Users will need to manually download new versions

## Current Modrinth Approach

Modrinth uses:

- Custom update server at `launcher-files.modrinth.com`
- S3/CDN for hosting files
- Custom `updates.json` format
- Automatic signing in CI/CD

You can use the same approach but with GitHub Releases instead of S3.

## Next Steps

1. ✅ Generate signing keys
2. ✅ Add public key to tauri.conf.json
3. ✅ Add private key to GitHub Secrets
4. ✅ Update workflow to create latest.json
5. ✅ Test with a new release

Would you like me to set this up for you?
