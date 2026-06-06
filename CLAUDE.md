# KBProIDE — Claude Context Guide

## Project Overview

KBProIDE (v1.2.0) is a cross-platform Electron desktop IDE for programming IoT/microcontroller boards (KidBright, Arduino, ESP32, etc.) built by Maker Asia. It supports both visual block-based programming (Blockly) and text-based programming (Monaco Editor), with a plugin architecture for extending board and component support.

**App ID:** `com.makerasia.ide`  
**Package name:** `kbide`  
**Electron version:** 9.1.0  
**Target OS:** Windows, macOS, Linux  
**GitHub:** https://github.com/MakerAsia/KBProIDE

---

## Tech Stack

| Layer | Library / Tool |
|---|---|
| Desktop wrapper | Electron 9.1.0 |
| Frontend framework | Vue.js 2.6.10 |
| UI components | Vuetify 1.5.16 (Material Design) |
| State management | Vuex 3.0.1 |
| Routing | Vue Router 3.0.1 |
| Code editor | Monaco Editor 0.17.1 + monaco-themes 0.3.3 |
| Visual programming | vue-blockly (GitHub fork — comdet/vue-blockly) |
| Rich text editor | Quill 1.3.6 + vue-quill-editor |
| Charts | Chart.js 2.8.0 + vue-chartjs |
| Syntax highlighting | Prism.js 1.15.0 + vue-highlightjs |
| Drag-and-drop | vuedraggable 2.17.0 |
| Form validation | vee-validate 2.1.4 |
| Color picker | vue-color 2.7.0 |
| Markdown rendering | vue-markdown 2.2.4 |
| Async queuing | p-queue 6.0.2 |
| ZIP extraction | yauzl 2.10.0 |
| Styles | Stylus (`.styl` files) |
| Fonts/Icons | Roboto, Font Awesome 4, Material Design Icons |
| Build tool | Vue CLI 3 + vue-cli-plugin-electron-builder 1.1.2 |
| Test runner | Jest (via `@vue/cli-plugin-unit-jest`) |
| Linter | ESLint + Prettier (babel-eslint parser) |
| CI | Travis CI (Node 8 and 10) |
| Backend API | Directus SDK JS 5.3.4 (`https://manage.kbide.org/`) |
| Analytics | electron-google-analytics (UA-140229781-1) |
| Error tracking | electron-unhandled + electron-util |
| Hot updates | electron-asar-hot-updater (GitHub fork) |
| Startup perf | v8-compile-cache |

**GitHub-fork dependencies** (pinned to specific commits):
- `vue-blockly` — comdet/vue-blockly
- `vue-multipane` — comdet/vue-multipane
- `vue-tour` — comdet/vue-tour
- `electron-asar-hot-updater` — comdet/electron-asar-hot-updater

---

## Repository Structure

```
KBProIDE-Master/
├── src/                    # All renderer-process source code
│   ├── main.js             # Vue app entry: globals, managers, Directus, analytics
│   ├── background.js       # Electron main process (window, IPC, protocol, menu)
│   ├── App.vue             # Root component: layout, tabs, drawers, dialogs
│   ├── menu.js             # Electron application menu template
│   ├── store.js            # Vuex store (rawCode state only)
│   ├── tour.js             # In-app guided tour configuration
│   ├── event.js            # Global Vue event bus
│   ├── registerServiceWorker.js
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
│   │   ├── AsyncComponent.vue  # Async component wrapper utility
│   │   ├── components/         # Feature-level Vue components
│   │   │   ├── board_selector/ # Board selection toolbar widget
│   │   │   ├── editor/Page.vue # Main editor (1300+ lines: Blockly + Monaco)
│   │   │   ├── examples/       # Example project selector
│   │   │   ├── package/        # Package manager UI
│   │   │   ├── plugin/         # Plugin management UI
│   │   │   ├── raw_code/       # Raw code mode toggle
│   │   │   └── setting/        # Settings drawer
│   │   ├── views/
│   │   │   ├── page/           # Login, NotFound (404), Deny (403), Error (500)
│   │   │   ├── dialog/         # PianoDialog, TTSDialog, VariableNamingDialog
│   │   │   ├── widgets/        # Piano, TreeMenu, Cards, Scrollbars
│   │   │   ├── AppToolbar.vue
│   │   │   ├── AppFooter.vue
│   │   │   ├── AppUpdater.vue
│   │   │   ├── ThemeSettings.vue
│   │   │   ├── Notification.vue
│   │   │   └── VWidget.vue     # Generic widget wrapper
│   │   ├── plugins/
│   │   │   └── vuetify.js      # Vuetify plugin setup
│   │   ├── updater/
│   │   │   └── DownloadAndExtract.js  # Hot-update download/extraction logic
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
│       ├── default.styl        # Main stylesheet (~6000 lines)
│       └── component-design.styl  # Component-specific styles (~1500 lines)
├── boards/                 # Git submodules — one per supported board (15 boards)
├── platforms/              # Git submodules — Arduino AVR, ESP32, ESP-IDF (3 platforms)
├── packages/               # Extension packs — kbide-extension-pack submodule
├── plugins/                # Reserved for runtime plugins (currently empty)
├── public/
│   ├── index.html          # HTML entry; loads Vuetify, Monaco, Tone.js externally
│   ├── icon.png/icns/ico   # App icons
│   └── manifest.json       # PWA manifest
├── tests/unit/             # Jest unit tests
│   ├── utils/
│   │   ├── utils.spec.js   # regex-parser.js tests
│   │   └── index.spec.js   # Core utility tests
│   └── setup.js            # Jest global setup
├── build/                  # Build scripts and icon builder output
├── docs/                   # Minimal documentation
├── project-configs/        # Project configuration files
├── migrate.js              # Migration utility script
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
npm run electron:generate-icons  # Generate app icons from public/icon.png

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

- **Main process** (`src/background.js`): Creates the `BrowserWindow`, registers `app://` custom protocol, handles `update-restart` IPC event, and loads `src/menu.js`. Installs Vue DevTools in development.
- **Renderer process** (`src/main.js` → `src/App.vue`): Full Vue app. `webSecurity` is disabled in `webPreferences` to allow local file loading.

### Vue App Bootstrap (`src/main.js`)

Bootstraps asynchronously in a top-level `async` IIFE. Initialization order:

1. `v8-compile-cache` — loaded first for startup performance
2. Directus SDK clients registered on Vue prototype
3. Google Analytics configured
4. `electron-unhandled` error tracker registered
5. `ComponentManager.listComponent()` — loads all component configs
6. `BoardManager.listBoard()` + `loadBoardManagerConfig()` — board discovery
7. `UIManager` config loaded
8. Persistence watchers set up (`localStorage` sync via `addWatcher`)
9. `Vue.prototype.$global` created as a reactive Vue instance with all state
10. URL hash config parsed and applied (file/mode/persistence)
11. `Vue.prototype.$engine` created as a reactive Vue instance
12. `new Vue(...)` mounted on `#app`

**Vue prototype globals:**

| Global | Type | Purpose |
|---|---|---|
| `$db2` | Directus client | Authenticated production backend (uses `localStorage` token) |
| `$db_dev` | Directus client | Dev backend with hardcoded token |
| `$track` | Analytics | Google Analytics tracker (`UA-140229781-1`) |
| `$global` | Reactive Vue | Shared cross-component state (board, editor, ui, packages) |
| `$engine` | Reactive Vue | Manager instances (see below) |

**`$engine` properties:**

| Key | Value |
|---|---|
| `util` | `src/engine/utils/index.js` |
| `compiler` | `src/engine/Compiler.js` |
| `componentManager` | `ComponentManager` instance |
| `boardManager` | `BoardManager` instance |
| `platformManager` | `PlatformManager` instance |
| `uiManager` | `UIManager` instance |
| `pluginManager` | `PluginManager` instance |

### URL Hash Configuration

The app reads query parameters from the URL hash fragment on startup (`#/editor?key=value`). Supported keys:

| Param | Effect |
|---|---|
| `file=<path>` | Pre-loads a `.bly` or `.ino` file into the editor |
| `mode=<int>` | Sets the initial editor mode |
| `persistence=false` | Disables localStorage writes; adds "Example Mode" to window title |

### Manager Pattern (Engine)

All major subsystems are encapsulated in manager classes under `src/engine/`:

| Manager | Responsibility |
|---|---|
| `BoardManager` | Discovers boards in the `boards/` directory, reads `config.js` per board, manages board packages |
| `PluginManager` | Scans plugin directories, extracts Blockly block definitions and JavaScript generators |
| `PackageManager` | Downloads and installs board packages; resolves dependencies |
| `PlatformManager` | Loads platform toolchain definitions from `platforms/` |
| `ComponentManager` | Tracks and loads UI components; reads config from each component directory |
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

Most application state lives in `$global` (a reactive Vue instance, not Vuex). Components read/write `$global.<component>.<field>`. Persistence is handled by the watcher system in `main.js` which mirrors watched paths to `localStorage`.

The Vue event bus (`src/event.js`) is used for cross-component communication that doesn't go through `$global`.

### Editor Component (`src/engine/components/editor/Page.vue`)

The central component (~1300 lines). It hosts:
- **Blockly workspace** for visual block programming
- **Monaco Editor** for text/raw code editing
- Mode switching between Blockly and Monaco
- Board selection, compile, and upload actions
- Serial monitor integration

### Backend Integration (Directus)

The app connects to `https://manage.kbide.org/` via the Directus SDK (`@directus/sdk-js`). Two clients are registered:

- `$db2` — production client; uses `localStorage` for token persistence (user auth)
- `$db_dev` — dev client; uses a hardcoded static token

These are used for fetching remote data (examples, packages, user data). Do not remove these globals — components depend on `this.$db2`.

### Routing

Routes are defined in `src/router/paths.js`. The main app always redirects `/` → `/editor`. All other routes are error/auth pages. NProgress is triggered on route transitions.

| Route | Component | Notes |
|---|---|---|
| `/editor` | `editor/Page.vue` | Lazy-loaded; hides navigation drawer |
| `/login` | `page/Login.vue` | Public |
| `/404` | `page/NotFound.vue` | Public catch-all |
| `/403` | `page/Deny.vue` | Access denied |
| `/500` | `page/Error.vue` | Server error |
| `*` | — | Redirects to `/404` |

### File Formats

- `.bly` — Blockly project files (base64-encoded XML)
- `.ino` — Arduino sketch files
- Project persistence via `localStorage`

### Supported Boards (15 submodules)

| Board directory | GitHub repository |
|---|---|
| `boards/kidbright` | comdet/kidbright-board-kbide |
| `boards/kbpro` | MakerAsia/kbpro-board |
| `boards/kbx` | cmmakerclub/kbx-board |
| `boards/kidbright-arduino` | MakerAsia/kidbright-arduino-board |
| `boards/ttgo-t-watcher-v1.2` | cmmakerclub/ttgo-t-watcher-v1.2-board |
| `boards/ttgo-t-watcher-v1.3` | cmmakerclub/ttgo-t-watcher-v1.3-board |
| `boards/ttgo-t8-v1.1` | cmmakerclub/ttgo-t8-v1.1-board |
| `boards/ttgo-t8-v1.3` | cmmakerclub/ttgo-t8-v1.3-board |
| `boards/ttgo-t8-v1.7` | cmmakerclub/ttgo-t8-v1.7-board |
| `boards/arduino-uno` | KhonKaenMakerClub/kbide-arduino-uno |
| `boards/ipst-wifi` | MakerAsia/ipst-wifi-board |
| `boards/arduino-nano` | cmmakerclub/arduino-nano-board |
| `boards/arduino-mega` | cmmakerclub/arduino-mega-board |
| `boards/openkb` | MakerAsia/openkb |
| `boards/kbm5stack` | MakerAsia/kbm5stack |

### Supported Platforms (3 submodules)

| Platform directory | GitHub repository |
|---|---|
| `platforms/arduino-avr` | MakerAsia/kbide-arduino-avr-platform |
| `platforms/arduino-esp32` | MakerAsia/kbpro-arduino-platform |
| `platforms/esp-idf` | MakerAsia/kbpro-esp-idf-platform |

The default board (`kidbright-arduino`) is set in `boards/config.js`.

### Styling

All styles are written in **Stylus** (`.styl` files). The main stylesheet is `src/theme/default.styl`. Component-specific styles are in `src/theme/component-design.styl`. Do not use plain CSS in new components — use Stylus.

### Build Output

Electron Builder outputs to `dist_electron/`. Platform packaging:
- **Windows:** NSIS installer (one-click disabled, per-machine disabled, no elevation required)
- **macOS:** `.dmg` disk image; category `public.app-category.productivity`
- **Linux:** AppImage (ia32 or x64)

Extra resources (`boards/`, `platforms/`, `packages/`, `plugins/`) are bundled into the app via `extraResources` in `vue.config.js`.

`IS_ELECTRON=true` is injected as a webpack `DefinePlugin` constant in the renderer — use it to guard Electron-only code paths.

---

## Testing

- **Framework:** Jest with `@vue/test-utils` (beta 29)
- **Test files:** `tests/unit/**/*.spec.js`
- **Module alias:** `@` → `src/`
- **Setup file:** `tests/unit/setup.js`
- **Coverage:** Disabled by default (configured but not collected by default)
- **Watch plugins:** typeahead for filename and test name

**Current test files:**
- `tests/unit/utils/utils.spec.js` — URL/GitHub validation and regex parsing (`src/engine/utils/regex-parser.js`)
- `tests/unit/utils/index.spec.js` — Core utility function tests

When adding tests, place them in `tests/unit/` mirroring the `src/` path, suffixed with `.spec.js`.

---

## Linting & Code Style

- **ESLint config:** `.eslintrc.js` — `plugin:vue/essential` + `@vue/prettier`
- **Parser:** `babel-eslint`
- **Prettier** handles formatting (run via `npm run lint`)
- `no-console` and `no-debugger` are errors in production, off in development
- Component files use **single-file component** (`.vue`) format
- Script sections use `export default { ... }` (Options API, not Composition API)
- No TypeScript — plain JavaScript throughout
- `runtimeCompiler: true` is set in `vue.config.js` — Vue templates can be compiled at runtime

---

## Git Submodules

The repository has **19 submodules** (15 boards + 3 platforms + 1 extension package). When updating board or platform code:

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
- **First-run detection:** On first launch, `main.js` checks for a file named `INSTALLED` in `util.baseDir`. If absent, it writes the current version there and clears `localStorage`. Do not remove this logic.
- **`$db` vs `$db2`:** The error tracker in `main.js` has a stale reference to `Vue.prototype.$db` (which does not exist). The correct globals are `$db2` (production) and `$db_dev` (dev). This is a known bug; do not introduce new `$db` references.
- **Submodule count:** `.gitmodules` has 19 entries — 15 boards, 3 platforms, 1 package (`packages/kbide-extension-pack`).
- **`postinstall` / `postuninstall` hooks:** Both run `electron-builder install-app-deps` automatically after `npm install`/`npm uninstall`.
