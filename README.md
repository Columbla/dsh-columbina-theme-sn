# Columbina Theme — Patch Edition

[**English**](README.md) | [**中文**](README.zh-CN.md)

A moonlit silver-blue glassmorphism skin for the DeepSeek Harness Web GUI, with an editable Columbina persona.

**This is a patch, not an original theme.** The bulk of the code comes from [Columbina-sn](https://github.com/Columbina-sn/dsh-columbina-theme), the font palette from [hyposelen1a](https://github.com/hyposelen1a/dsh-columbina-theme), and the host/client structure from [berhbro](https://github.com/berhbro/dsh-cyrene-theme). See [Credits](#credits).

## Disclaimer

This theme is pure CSS rendered by your browser, so the final look depends on your machine:

- **Glass blur** (`backdrop-filter`) needs Chromium 76+ / Edge / Chrome. Old engines, disabled hardware acceleration, some remote desktops, VMs or integrated-GPU drivers fall back to a flat translucent layer — no blur.
- **Gradient text** (`background-clip:text`) may fail to paint on a few GPU drivers. There is a `@supports` solid-colour fallback, but it looks different.
- **Light/dark** follows your system/Harness appearance (`prefers-color-scheme` and `body[data-ds-dark-theme]`), not a plugin switch.
- **High DPI** (125% / 150% / 200%) changes how the 72px avatar, 1px borders and 2px letter-spacing read; screenshot pixels are not CSS pixels.
- **Wide-gamut displays / colour management** make saturated purples such as `#78007a` look brighter or duller. The "same colour" never is, across screens.
- **Fonts**: the hero title prefers Source Han Serif / STSong and falls back to a system serif, which changes metrics.
- **Host wallpaper overlay**: if your Harness frontend ships its own full-screen wallpaper layer (e.g. `#dsh-wall-layer`, opaque, `z-index:0`), it paints over this theme's `z-index:-1` background layer. Since v0.2.2 the theme hides that layer while its own background image is active, and restores it in plain mode.

Screenshots are illustrative, not a guarantee of pixel-identical rendering.

## What it is

A DSH theme plugin: palette, background image, glass effects, editable persona and sticker conventions.
This patched edition adds a theme-takeover switch, background upload, blur/clarity sliders and an editable hero title.

## What's changed

| Change | Notes |
| --- | --- |
| **Theme takeover** | When two Columbina themes are installed they fight over the same tokens. Toggle on → the other package (`dsh-columbina-theme`) is disabled through the host `pluginManager`; toggle off → it is restored and this theme steps aside. Persisted across restarts. |
| **Background upload** | Upload from the panel; files land in `Background/` and become the anchor. Hover `×` on a thumbnail to delete (with confirm). Never overwrites existing files, duplicates get a suffix. |
| **Blur & clarity** | Two independent sliders: blur 0–40px (preview while dragging, persist on release; the layer scales by `1 + radius/200` to hide the soft edge) and clarity, the positive phrasing of the veil opacity. |
| **Editable hero title** | The title lives in the state file and is editable from the panel. Empty `hero.image` hides the avatar entirely, leaving one line of text. |
| **Font palette** | Label tokens now use hyposelen1a's palette: `#78007a` light, `#acabf2` dark, with secondary/tertiary derived at 0.82 / 0.6 alpha. The hero gradient follows. |
| **Viewer text colour** | The plain-text preview ships its own green (`#2b8a3e`) that a container-level `color` cannot beat, so it is pinned on `pre[class*="_plain"]`. Syntax-highlighted spans keep their own colours. |
| **Hero image cache** | The image URL carries `?v=<path>` so `Cache-Control: max-age=300` cannot serve a stale avatar after a swap. |
| **Host wallpaper overlay** | Hides a host-side full-screen wallpaper layer (`#dsh-wall-layer`) while the theme background is active (v0.2.2). |

Everything else (persona section, sticker manifest, fenced routes, slot registration, glass CSS) is unchanged from upstream.

## Install

Requirements: a DSH desktop or web profile that loads client plugins. Verified on **DSH 0.2.1-rc.2**; the Linux test also passed on **0.2.0-rc.2**.

`dsh plugin` needs **pnpm** on PATH. If it fails with `spawn pnpm ENOENT`, run `corepack enable pnpm` first.

From GitHub:

```powershell
dsh plugin --profile desktop add github:Columbla/dsh-columbina-theme-sn
```

From a local folder:

```powershell
dsh plugin --profile desktop add D:\path\to\dsh-columbina-theme-sn
```

Refresh the page afterwards (the client half is read on page load). Host-half changes need a plugin row reload or a DSH restart.

```powershell
dsh plugin --profile desktop remove dsh-columbina-theme-sn
```

## Quick start

1. A `☾ 哥伦比娅` button appears next to Settings at the sidebar foot; the same editor is also under Settings → 哥伦比娅 · 主题.
2. Top switches: persona / skin / **theme takeover** / stickers / proactive stickers / no-repeat.
3. Background block: upload, pick an anchor, drag blur and clarity.
4. `恢复默认` refills the default persona draft; `重新载入` discards unsaved edits.

### When changes apply

| What | Applies |
| --- | --- |
| Persona / switches / background settings (panel save) | Immediately, next turn |
| `lib/client.js` | Page refresh |
| `lib/index.js`, `cordis.patch.yml` | Plugin row reload or restart |
| `Background/` add / remove / rename | Page refresh |

## State & settings

The state file lives at `$DSH_HOME/columbina-theme.json`, or `~/.dsh/columbina-theme.json` when `DSH_HOME` is unset.

```json
{
  "persona": "…persona prompt…",
  "enabled": true,
  "theme": true,
  "takeover": true,
  "stickers": true,
  "proactive": true,
  "noRepeat": true,
  "stickerSize": 128,
  "customStickers": [],
  "stickerOverrides": {},
  "hero": { "image": "meme/星眸.png", "title": "今天的 token，能分我一口吗？" },
  "background": {
    "enabled": true, "current": "01-columbina-light.png",
    "rotate": false, "interval": 90, "dim": 0.6, "blur": 0
  },
  "rev": 3
}
```

| Field | Meaning |
| --- | --- |
| `persona` | Persona prompt; empty disables injection |
| `enabled` / `theme` | Persona switch / skin switch |
| `takeover` | `true` disables the rival theme |
| `stickers` / `proactive` / `noRepeat` | Sticker switches — prompt rules only, not message filters |
| `stickerSize` | Max sticker edge, 48–320px |
| `customStickers` / `stickerOverrides` | User-added entries / renames and hiding of bundled ones |
| `hero.image` | In-package relative path; **empty hides the avatar** |
| `hero.title` | The hero line |
| `background.dim` | Veil opacity 0–0.95, higher is paler; the panel's "clarity" = `1 - dim` |
| `background.blur` | Blur radius 0–40px |
| `rev` | Write revision; bump it when editing by hand |

Writes go through a temp file + rename and carry optimistic concurrency: missing `rev` → 400 `client-outdated`, stale → 409 `stale`.

## Theme takeover

Both packages override `--dsw-alias-*` and paint their own background layer, so only one can win. This fork does not fight over CSS specificity:

```js
const RIVAL_PACKAGE = 'dsh-columbina-theme'          // the other package
const manager = ctx.get('pluginManager')
await manager.setPluginEnabled(rival.entryId, !takeover)
```

- On → the rival row is written as `disabled: true` in the profile's `cordis.patch.yml` (persistent; it will not come back on reboot).
- Off → `disabled: false` is restored and this theme's `theme` flag is turned off too, so you never end up with no theme at all.
- 1.5s after host start, the plugin re-checks `takeover` from the state file.
- If the rival is absent or `pluginManager` is not available, the switch only reports a message — it never breaks the plugin.

## Background & blur

| Control | Range | Field |
| --- | --- | --- |
| Blur | 0–40 px | `background.blur` |
| Clarity | 5–100% | `background.dim = 1 - clarity/100` |
| Interval | 15–600 s | `background.interval` (only with `rotate`) |

Upload goes through `POST /columbina/backgrounds` (`data:image/*;base64`, ≤ 24MB). File names are stripped of path separators and reserved characters; duplicates get a suffix and existing files are never overwritten. Deletion uses `POST /columbina/backgrounds/remove` and only accepts names present in the scanned list.

## Assets

The repo ships a default asset set so it looks complete out of the box:

| Path | Content |
| --- | --- |
| `meme/` | 12 Columbina stickers (WebP) + hero avatar `星眸.png` |
| `Background/` | 3 presets: `01-columbina-light.png`, `02-columbina-dark.png`, `03-columbina-dusk.jpg` |

These images are **not** covered by MIT. Character art and official stickers belong to **miHoYo / HoYoverse**; the avatar and backgrounds belong to their respective artists. Non-commercial fan use only — open an issue and the assets will be removed on request. See [NOTICE.md](NOTICE.md).

To use your own images: drop them into `Background/` and refresh; name sticker files after `stickers.json`'s `items[].file`; point `hero.image` at any in-package path (a circular PNG with alpha at 72px works best).

## Known issues & fixes

| Symptom | Cause | Fix |
| --- | --- | --- |
| Hero avatar comes back after a restart | Before the state arrives `heroReady` defaults to `true`, so the browser paints a cached avatar | Fixed: nothing is rendered while `heroKey` is empty |
| Swapped avatar still shows the old image | `/columbina/hero` sends `Cache-Control: max-age=300` | Fixed: the URL carries `?v=<path>` |
| Switching the background image or dragging blur does nothing | The host frontend draws its own opaque wallpaper layer (`#dsh-wall-layer`) above this theme's `z-index:-1` layer | Fixed in v0.2.2: that layer is hidden while the theme background is active |
| `dsh plugin add` rejected with `Unexpected token '\uFEFF'` | `package.json` was saved with a UTF-8 BOM, which `JSON.parse` rejects | Fixed in v0.2.2; `.gitattributes` keeps it from coming back |
| Viewer text refused to change colour | The plain preview has its own green `#2b8a3e`; a container `color` loses | Fixed: pinned on `pre[class*="_plain"]` (`!important`, plain renderer only) |
| Two themes fight over colours | Both override `--dsw-alias-*`, last one wins | Turn on theme takeover |
| Whole window goes blurry | `backdrop-filter` on `[data-shell-overlay]`, which covers the frame | Only blur the direct children of floating layers |
| UI flickers when hovering the send button | `[data-composer-card]` became the containing block of a `position:fixed` tooltip | Paint the blur on `::before` |
| The collapsed right panel stays foggy | Blur applied to `[data-sidebar-right-panel]` | Blur the real pane `[data-dockkit-pane]` instead |
| Dark hero title turns into a pale block | `background` shorthand resets `background-clip:text` | Use `background-image` and re-declare the clip |
| Stickers do not show up | `stickers.json` paths do not match real files | Check `missing` from `/columbina/stickers` |
| Proactive stickers do nothing | The rule lives in the host half; a page refresh is not enough | Reload the plugin row or restart DSH |
| Dark primary text looks faint | `#acabf2` reaches 5.99:1 on this theme's darker backdrop (below the 7:1 the upstream targets, above AA 4.5:1) | Use `#c3c2fb` instead (7.55:1) |
| A CJK filename became `custom-xxxx` on upload | The request body was not UTF-8, so the name was stripped | Browsers are fine; scripts must send `charset=utf-8` |

Full reproduction guide for the "background / blur does nothing" class of bugs: [docs/repro-background-not-changing.md](docs/repro-background-not-changing.md) (English) · [中文版](docs/repro-background-not-changing.zh-CN.md).

## Compatibility

| Item | Notes |
| --- | --- |
| Engine | Chromium 76+ (Edge / Chrome / Electron); older engines lack `backdrop-filter` |
| GPU | With acceleration off, the glass degrades to a flat translucent layer |
| DPI | 100%–200% works; at 125% the 1px borders and 72px avatar read slightly differently |
| OS | Verified on Windows 10/11 and Alibaba Cloud Linux; macOS untested |
| DSH | Verified on 0.2.1-rc.2 and 0.2.0-rc.2; slots/APIs may differ in other versions |
| Fonts | The hero prefers Source Han Serif / STSong, falls back to a system serif |

## Tests

```powershell
node test/contrast.mjs      # contrast, CSS-only
node test/client.smoke.mjs  # client half with DOM stubs
node test/host.smoke.mjs    # host half, needs assets
```

Baseline: contrast **31/31**, client **130/130**, host **115/115** (host prints `SKIP` when assets are absent).

## Credits

| Project | Author | Used for |
| --- | --- | --- |
| [dsh-columbina-theme](https://github.com/Columbina-sn/dsh-columbina-theme) | **Columbina-sn** | The theme itself: host/client halves, fenced routes, slot registration, persona section, the "moonlit silver-blue" palette and glass CSS, sticker manifest, test suites |
| [dsh-columbina-theme](https://github.com/hyposelen1a/dsh-columbina-theme) | **hyposelen1a** | The font palette (`#78007a` / `#acabf2`) and the mask-dim idea; the package that theme takeover disables |
| [dsh-cyrene-theme](https://github.com/berhbro/dsh-cyrene-theme) | **berhbro** | Original host/client structure, `rev` optimistic concurrency, slot registration and the test harness |

The original MIT notice is kept in [LICENSE](LICENSE) (Copyright © 2026 berhbro).

**Character & art**: Columbina and all Genshin Impact character names, likenesses and official stickers belong to **miHoYo / HoYoverse** and are **not** covered by MIT. This project is not affiliated with, endorsed by, or sponsored by miHoYo, HoYoverse or DeepSeek. See [NOTICE.md](NOTICE.md).

## License

Code is MIT. Image assets are not.
