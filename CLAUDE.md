# KBProIDE — Claude Context Guide

## Project Overview

KBProIDE (v1.2.0) is a cross-platform Electron desktop IDE for programming IoT/microcontroller boards (KidBright, Arduino, ESP32, etc.) built by Maker Asia. It supports both visual block-based programming (Blockly) and text-based programming (Monaco Editor), with a plugin architecture for extending board and component support.

**App ID:** `com.makerasia.ide`  
**Electron version:** 9.1.0  
**Target OS:** Windows, macOS, Linux

---

## Tech Stack

| Layer | Library / Tool |
|---|---|
| Desktop wrapper | Electron 9.1.0 |
| Frontend framework | Vue.js 2.6.10 |
| UI components | Vuetify 1.5.16 (Material Design) |
| State management | Vuex 3.0.1 |
| Routing | Vue Router 3.0.1 |
| Code editor | Monaco Editor 0.17.1 |
| Visual programming | vue-blockly (GitHub fork) |
| Styles | Stylus (`.styl` files) |
| Build tool | Vue CLI 3 + vue-cli-plugin-electron-builder 1.1.2 |
| Test runner | Jest (via `@vue/cli-plugin-unit-jest`) |
| Linter | ESLint + Prettier (babel-eslint parser) |
| CI | Travis CI (Node 8 and 10) |

---

## Repository Structure

```
KBProIDE-Master/
├── src/                    # All renderer-process source code
│   ├── main.js             # Vue app entry: registers globals, loads boards/platforms
│   ├── background.js       # Electron main process (window creation, IPC, menu)
│   ├── App.vue             # Root component: layout, tabs, drawers, dialogs
│   ├── menu.js             # Electron application menu template
│   ├── store.js            # Vuex store (rawCode state)
│   ├── tour.js             # In-app guided tour configuration
│   ├── event.js            # Global Vue event bus
│   ├── router/
│   │   ├── index.js        # Router setup with NProgress guards
│   │   └── paths.js        # Route definitions
│   ├── engine/
│   │   ├── BoardManager.js     # Board discovery, config loading, package management
│   │   ├── PluginManager.js    # Plugin loading, Blockly block/generator extraction
│   │   ├── PackageManager.js   # Board package download and dependency resolution
│   │   ├── PlatformManager.js  # Hardware platform management (AVR, ESP32, IDF)
│   │   ├── ComponentManager.js # UI component management
│   │   ├── UIManager.js        # UI state and layout management
│   │   ├── Compiler.js         # Stub — not yet implemented
│   │   ├── components/         # Feature-level Vue components
│   │   │   ├── board_selector/ # Board selection toolbar widget
│   │   │   ├── editor/Page.vue # Main editor (~1100 lines: Blockly + Monaco)
│   │   │   ├── examples/       # Example project selector
│   │   │   ├── package/        # Package manager UI
│   │   │   ├── plugin/         # Plugin management UI
│   │   │   ├── raw_code/       # Raw code mode toggle
│   │   │   └── setting/        # Settings drawer
│   │   ├── views/
│   │   │   ├── page/           # Login, NotFound (404), Deny (403), Error (500)
│   │   │   ├── dialog/         # PianoDialog, TTSDialog, VariableNamingDialog
│   │   │   ├── widgets/        # Piano, TreeMenu(2), SmoothScrollbar, UserSelectCard
│   │   │   ├── AppToolbar.vue
│   │   │   ├── AppFooter.vue
│   │   │   ├── ThemeSettings.vue
│   │   │   ├── VWidget.vue     # Reusable card-with-slots widget wrapper
│   │   │   └── Notification.vue
│   │   ├── plugins/
│   │   │   └── vuetify.js      # Vuetify plugin setup
│   │   ├── updater/
│   │   │   ├── AppUpdater.vue          # Hot-update UI (progress, restart prompt)
│   │   │   └── DownloadAndExtract.js   # Hot-update download/extraction logic
│   │   └── utils/
│   │       ├── index.js        # Core utilities: path handling, file ops, Vue loading
│   │       ├── blockly.js      # Blockly-specific helpers
│   │       ├── compiler.js     # Compilation helpers
│   │       ├── unzip.js        # ZIP extraction (yauzl-based)
│   │       ├── ui.js           # UI utilities
│   │       ├── file.js         # File system operations
│   │       ├── codeformat.js   # Code formatting utilities
│   │       └── regex-parser.js # URL/regex validation
│   └── theme/
│       ├── default.styl        # Main stylesheet (~270 lines)
│       └── component-design.styl  # Component-specific styles (~110 lines)
├── boards/                 # Git submodules — one per supported board
├── platforms/              # Git submodules — Arduino AVR, ESP32, ESP-IDF
├── packages/               # Extension packs and tools (clang-format, etc.)
├── plugins/                # Reserved for runtime plugins (currently empty)
├── public/
│   ├── index.html          # HTML entry; loads Vuetify, Monaco, Tone.js
│   ├── icon.png/icns/ico   # App icons
│   └── manifest.json       # PWA manifest
├── tests/unit/             # Jest unit tests
├── build/                  # Build scripts and icon builder output
├── docs/                   # Minimal documentation
├── vue.config.js           # Vue CLI + Electron Builder configuration
├── jest.config.js          # Jest configuration
├── babel.config.js         # Babel preset: @vue/app
├── postcss.config.js       # Autoprefixer
├── .eslintrc.js            # ESLint: vue/essential + prettier, babel-eslint parser
├── .travis.yml             # CI: test:unit on Node 8 and 10
└── .gitmodules             # 19 submodule definitions
```

---

## Development Commands

```bash
# Development
npm run electron:serve       # Start Electron app in dev mode with hot-reload
npm run serve                # Web-only dev server (no Electron)

# Build
npm run electron:build       # Build for current OS
npm run build-win32          # Windows 32-bit installer (.exe via NSIS)
npm run build-win64          # Windows 64-bit installer
npm run build-linux32        # Linux 32-bit AppImage
npm run build-linux64        # Linux 64-bit AppImage
npm run build-macos          # macOS .dmg
npm run build-mac            # macOS: copy boards/platforms into .app bundle

# Native modules
npm run electron:rebuild     # Rebuild all native modules for Electron
npm run electron:rebuild-serial  # Rebuild serialport specifically

# Testing
npm run test:unit            # Run Jest unit tests once
npx vue-cli-service test:unit --watchAll  # Jest in watch mode

# Linting
npm run lint                 # ESLint check + auto-fix
```

**After cloning**, always initialise submodules before `npm install`:
```bash
git submodule init
git submodule update
npm install
npm run electron:rebuild
npm run electron:rebuild-serial
```

**On Windows** (before `npm install`), run CMD as Administrator:
```bash
npm install --global --production windows-build-tools
```

**On Linux**, add serial port permissions:
```bash
sudo adduser $USER dialout
```

---

## Architecture & Key Patterns

### Electron Process Split

- **Main process** (`src/background.js`): Creates the `BrowserWindow`, registers `app://` protocol, handles `update-restart` IPC event, and loads `src/menu.js`.
- **Renderer process** (`src/main.js` → `src/App.vue`): Full Vue app. `webSecurity` is disabled in `webPreferences` to allow local file loading.

### Vue App Bootstrap (`src/main.js`)

The app registers several globals on the Vue prototype at startup:
- `Vue.prototype.$global` — shared reactive object for cross-component state
- `Vue.prototype.$engine` — the engine manager instances
- Board and platform managers are instantiated and loaded before `new Vue()`

### Manager Pattern (Engine)

All major subsystems are encapsulated in manager classes under `src/engine/`:

| Manager | Responsibility |
|---|---|
| `BoardManager` | Discovers boards in the `boards/` directory, reads `config.js` per board, manages board packages |
| `PluginManager` | Scans plugin directories, extracts Blockly block definitions and JavaScript generators |
| `PackageManager` | Downloads and installs board packages; resolves dependencies |
| `PlatformManager` | Loads platform toolchain definitions from `platforms/` |
| `ComponentManager` | Tracks UI components registered by plugins |
| `UIManager` | Controls layout/drawer state |

### State Management

Vuex store (`src/store.js`) is minimal — only tracks **raw code mode** state:

```js
state.rawCode = {
  mode: Boolean,          // is raw code mode active
  rollbackMode: Number,   // previous mode for rollback
  display: Boolean,       // visibility of raw code panel
}
```

Most state is passed via Vue prototype globals (`$global`) or direct component props/events. The Vue event bus (`src/event.js`) is used for cross-component communication.

### Editor Component (`src/engine/components/editor/Page.vue`)

The central component (~1100 lines). It hosts:
- **Blockly workspace** for visual block programming
- **Monaco Editor** for text/raw code editing
- Mode switching between Blockly and Monaco
- Board selection, compile, and upload actions
- Serial monitor integration

### Routing

Routes are defined in `src/router/paths.js`. The main app always redirects `/` → `/editor`. All other routes are error/auth pages. NProgress is triggered on route transitions.

### File Formats

- `.bly` — Blockly project files (base64-encoded XML)
- `.ino` — Arduino sketch files
- Project persistence via `localStorage`

### Board & Platform System

Each board lives in `boards/<board-name>/` as a git submodule containing:
- `config.js` — board metadata, package list, platform reference
- Blockly block definitions
- Code generators

Platforms (`platforms/`) provide the toolchain (compiler, uploader) for a hardware family. `boards/config.js` sets the default board (`kidbright-arduino`).

### Styling

All styles are written in **Stylus** (`.styl` files). The main stylesheet is `src/theme/default.styl`. Component-specific styles are in `src/theme/component-design.styl`. Do not use plain CSS in new components — use Stylus.

### Build Output

Electron Builder outputs to `dist_electron/`. Platform packaging:
- **Windows:** NSIS installer (one-click disabled, per-machine disabled)
- **macOS:** `.dmg` disk image
- **Linux:** AppImage

Extra resources (`boards/`, `platforms/`, `packages/`, `plugins/`) are bundled into the app via `extraResources` in `vue.config.js`.

---

## Testing

- **Framework:** Jest with `@vue/test-utils`
- **Test files:** `tests/unit/**/*.spec.js`
- **Module alias:** `@` → `src/`
- **Setup file:** `tests/unit/setup.js`
- **Coverage:** Disabled by default

Current tests focus on utility functions:
- `tests/unit/utils/utils.spec.js` — URL/GitHub validation and regex parsing from `src/engine/utils/regex-parser.js`
- `tests/unit/utils/index.spec.js` — string-transform helpers (e.g. `camelActual`) from `src/engine/utils/index.js`

When adding tests, place them in `tests/unit/` mirroring the `src/` path, suffixed with `.spec.js`.

---

## Linting & Code Style

- **ESLint config:** `.eslintrc.js` — `plugin:vue/essential` + `@vue/prettier`
- **Parser:** `babel-eslint`
- **Prettier** handles formatting (run via `npm run lint`)
- Component files use **single-file component** (`.vue`) format
- Script sections use `export default { ... }` (Options API, not Composition API)
- No TypeScript — plain JavaScript throughout

---

## Git Submodules

The repository has 19 submodules — boards (`boards/kidbright`, `kbpro`, `kbx`, `kidbright-arduino`, `arduino-uno`, `arduino-nano`, `arduino-mega`, `ipst-wifi`, `openkb`, `kbm5stack`, `ttgo-t-watcher-v1.2`, `ttgo-t-watcher-v1.3`, `ttgo-t8-v1.1`, `ttgo-t8-v1.3`, `ttgo-t8-v1.7`), platforms (`platforms/arduino-avr`, `arduino-esp32`, `esp-idf`), and packages (`packages/kbide-extension-pack`). When updating board or platform code:

```bash
# Update all submodules to their latest remote tracking branch
git submodule update --recursive --remote
git submodule foreach "(git checkout master; git pull)"

# Or configure automatic recursive pull
git config submodule.recurse true
git pull --recurse-submodules
```

---

## Release Process

1. Build for target platform (`npm run electron:build` or platform-specific variant)
2. Copy `app.asar` to `update.asar` for hot-update distribution
3. Zip with version string: e.g. `zip -9 1.2.0-darwin.zip update.asar`
4. Distribute via the configured update server (electron-asar-hot-updater)

---

## Key Gotchas

- `webSecurity: false` is set in `BrowserWindow` — intentional for local resource loading, not a bug to fix.
- `Compiler.js` is an empty stub. Compilation is handled externally by platform toolchains, not in JS.
- Several npm dependencies are installed from GitHub forks (`vue-blockly`, `vue-multipane`, `vue-tour`, `electron-asar-hot-updater`) — these are pinned to specific commits via `package.json` git URLs.
- `IS_ELECTRON` is injected as a webpack `DefinePlugin` constant in the renderer process — use it to guard Electron-only code paths.
- Native modules (`serialport`) must be rebuilt after any `npm install` via `npm run electron:rebuild-serial`.
- The app targets **Node 8 and 10** in CI (Travis). Avoid Node APIs not available in Node 8.
