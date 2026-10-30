# Cuneiform

[![Release](https://img.shields.io/github/v/release/QTS-Vibe-Coding/CuneiformMarkdownReader)](https://github.com/QTS-Vibe-Coding/CuneiformMarkdownReader/releases/latest)
![macOS 14+](https://img.shields.io/badge/macOS-14%2B-lightgrey)
[![License: WTFPL](https://img.shields.io/badge/license-WTFPL-blue.svg)](LICENSE)

<!-- banner -->

Cuneiform is a macOS app that previews Markdown files and does nothing else: it opens a `.md` or `.markdown` file read-only and renders it, so long documents such as AI-generated notes and drafts are quick to read. It has no editor, no network access of its own and no settings to save.

<!-- screenshots -->

## Status

Cuneiform is a personal project, used by its author to read Markdown files. It is early (version 0.2) and small on purpose. It runs on macOS 14 or later. The menus are mostly in English; the empty window, error messages, alerts and the 设为默认 Markdown 阅读器 (Set as Default Markdown Viewer) command are in Chinese.

Releases are published on [GitHub Releases](https://github.com/QTS-Vibe-Coding/CuneiformMarkdownReader/releases) as a DMG that is Developer ID signed and notarized. There is no auto-update; download a new DMG to upgrade.

## How it is built

Most of the code is written by coding agents following `AGENTS.md`. It fixes the product identity, requires `./scripts/check.sh` (tests, build, bundle and DMG checks) to pass before a change is complete, keeps the image and link rules in `ResourcePolicy` for every renderer, and lists the release steps: a startup performance gate, Developer ID signing, notarization and DMG packaging. Design notes and plans are in `docs/`. See [CONTRIBUTING.md](CONTRIBUTING.md) to report a bug or send a fix, and [SECURITY.md](SECURITY.md) for security problems.

## Features

- Renders `.md` and `.markdown` files, preview only: headings, lists, task lists, tables, code blocks and quotes.
- Opens files from Finder, by drag and drop onto the window, from the command line (the first argument) and with **File › Open...** (⌘O).
- **View › Reload** (⌘R) re-reads the file from disk.
- **View › Increase Text Size** and **Decrease Text Size**, and **System Appearance**, **Light Appearance** and **Dark Appearance**. These last until the app quits.
- 设为默认 Markdown 阅读器 (Set as Default Markdown Viewer), in the app menu, makes Cuneiform open `.md` and `.markdown` files when you double-click them.

## Install

1. Download `Cuneiform-v<version>.dmg` from the [latest release](https://github.com/QTS-Vibe-Coding/CuneiformMarkdownReader/releases/latest). The release notes list its SHA-256.
2. Open the DMG and drag `Cuneiform.app` to `Applications`.
3. Optional: choose 设为默认 Markdown 阅读器 (Set as Default Markdown Viewer) from the app menu. If that fails, use Finder's **Get Info › Open with** on a Markdown file instead.

## Privacy and file access

- Cuneiform reads the Markdown file you open and the local images it references. Images load only if they are PNG, JPEG, GIF or WebP files in the document's folder or below it. Remote images, `data:` and `file:` URLs are not loaded.
- Raw HTML in a document is not run.
- Clicking an `http`, `https` or `mailto` link hands it to your default browser or mail app. Cuneiform itself makes no network requests.
- It writes nothing: no preferences file, no cache, no logs (unless you set the startup-measurement variables described under Development).
- The only system change it makes is the default Markdown viewer, and only when you choose that command.

## Uninstall

Quit Cuneiform and move `Cuneiform.app` from `Applications` to the Trash. If you made it the default Markdown viewer, pick another app for `.md` files with Finder's **Get Info › Open with › Change All**.

## Development

Run the full local gate before considering changes complete:

```bash
./scripts/check.sh
```

Build the app bundle:

```bash
./scripts/build_app.sh
```

The development app bundle is written to:

```bash
.build/app/Cuneiform.app
```

### Renderer fallback

Cuneiform uses the native Markdown renderer by default. To compare or debug the legacy WebView renderer from Terminal:

```bash
CUNEIFORM_RENDERER=webview /Applications/Cuneiform.app/Contents/MacOS/Cuneiform path/to/file.md
```

### Startup measurement

Measure startup against the installed app bundle opened through LaunchServices:

```bash
CUNEIFORM_RENDERER=native CUNEIFORM_APP=/Applications/Cuneiform.app ITERATIONS=10 ./scripts/measure_startup.sh path/to/file.md
CUNEIFORM_RENDERER=webview CUNEIFORM_APP=/Applications/Cuneiform.app ITERATIONS=10 ./scripts/measure_startup.sh path/to/file.md
```

Before treating measurements as product evidence, rebuild with `./scripts/check.sh`, install `.build/app/Cuneiform.app` to `/Applications/Cuneiform.app`, verify the default Markdown viewer, and confirm the installed bundle matches `.build/app`.

Run the release startup performance gate before signing and publishing:

```bash
ITERATIONS=20 ./scripts/gate_startup_performance.sh path/to/file.md
```

The release gate measures `/Applications/Cuneiform.app` through LaunchServices and fails when Cuneiform external cold-start `p50` is above `400ms`.

## Release (maintainer)

`scripts/build_app.sh` produces an adhoc-signed development bundle. For public distribution, rebuild, sign with Developer ID, notarize, staple, then package the DMG:

```bash
./scripts/build_app.sh
ITERATIONS=20 ./scripts/gate_startup_performance.sh path/to/file.md

codesign --force \
  --deep \
  --timestamp \
  --options runtime \
  --sign "Developer ID Application: Guan Xiaoyu (T7976FL2LP)" \
  .build/app/Cuneiform.app

ditto -c -k --keepParent .build/app/Cuneiform.app .build/app/Cuneiform.zip

xcrun notarytool submit .build/app/Cuneiform.zip \
  --keychain-profile cuneiform-notary \
  --wait

xcrun stapler staple .build/app/Cuneiform.app

codesign --verify --deep --strict --verbose=2 .build/app/Cuneiform.app
xcrun stapler validate .build/app/Cuneiform.app
spctl -a -vvv -t exec .build/app/Cuneiform.app

./scripts/package_dmg.sh
```

The release DMG is written to:

```bash
.build/release/Cuneiform.dmg
```

## Project Notes

- Product name: `Cuneiform`
- Bundle identifier: `io.damao.cuneiform`
- Swift target/module names intentionally remain `SimpleMarkdownPreviewerApp` and `SimpleMarkdownPreviewerCore`.
- Icon source: `Resources/Cuneiform.png`
- Bundle icon: `Resources/Cuneiform.icns`

## License

WTFPL. See `LICENSE`.
