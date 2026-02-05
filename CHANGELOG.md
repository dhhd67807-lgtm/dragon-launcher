# Changelog

All notable changes to Dragon Launcher will be documented in this file.

## [1.0.1] - 2026-02-05

### Added

- **Playing As Section**: New account display in right sidebar showing current Minecraft account with avatar and username
- **Offline Mode Support**: Quick offline mode login with customizable username (up to 16 characters)
- **Inline Username Editing**: Edit offline username directly with Save/Cancel buttons and keyboard shortcuts (Enter to save, Escape to cancel)
- **Account Dropdown Menu**: Easy access to "Add another account", "Edit username" (offline), and "Logout" options
- **Login Requirements**: Users must sign in (Microsoft or Offline mode) before creating instances, installing mods, or running games
- **Dragon Logo**: New dragon-themed branding with custom logo and icons
- **Documentation**: Added INSTANCE_CREATION_MODAL_FIX.md, OFFLINE_MODE_CHANGES.md, REBRAND_GUIDE.md, and RELEASE_GUIDE.md

### Changed

- **Rebranded** from "Modrinth App" to "Dragon Launcher"
- **App Icons**: All icons now feature dragon logo with solid black background and rounded corners (22% radius)
- **Window Size**: Increased default width from 1280px to 1700px
- **App Identifier**: Changed to `DragonLauncher`
- **Deep-link Scheme**: Changed to `dragonlauncher://`
- **Welcome Message**: Changed to always show "Welcome back!" instead of conditional "Welcome to Obsidian App!"
- **Loader Icons**: Converted Fabric, Forge, Quilt, and Vanilla loader icons from SVG to PNG/Vue components

### Fixed

- **Logout Button**: Fixed logout functionality for offline mode accounts (now properly clears localStorage)
- **Instance Creation Modal**: Fixed modal not opening from sidebar plus button by wrapping in `<Suspense>` component
- **Production Build**: Fixed sidebar navigation buttons not showing by removing `@apply` directives and using standard CSS
- **Login Modal**: Fixed z-index issues preventing modal from appearing (increased from 9997-9998 to 99997-99999)
- **Library Page**: Fixed hanging/black screen issue by adding null checks for instances array
- **CSS Tree-shaking**: Added `cssCodeSplit: true` to vite.config.ts to prevent aggressive tree-shaking in production

### Removed

- **Skins Tab**: Removed from left sidebar navigation
- **Modrinth Branding**: Removed modrinth_app.svg, app_logo.svg, and sad-modrinth-bot.webp
- **OS Version Display**: Removed from App Settings modal

### Technical

- Wrapped `InstanceCreationModal` in `<Suspense>` to handle async component loading
- Added retry logic with 5-second timeout for modal initialization
- Improved error handling for offline mode in ErrorModal.vue
- Added login checks to Instance.vue, InstanceCreationModal.vue, ModInstallModal.vue, and InstallConfirmModal.vue
- Updated NavButton.vue with explicit CSS visibility rules

## [1.0.0] - Initial Fork

- Forked from Modrinth App
- Base functionality for Minecraft instance management
