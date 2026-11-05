# Columbina Theme — Patch Edition

A moonlit silver-blue glassmorphism skin for the DeepSeek Harness Web GUI, with an editable Columbina persona.
给 DeepSeek Harness 的 Web GUI 换一套「月隐银蓝」毛玻璃皮肤，并附带一个可编辑的哥伦比娅人格。

**This is a patch, not an original theme.** The bulk of the code comes from [Columbina-sn](https://github.com/Columbina-sn/dsh-columbina-theme), the font palette from [hyposelen1a](https://github.com/hyposelen1a/dsh-columbina-theme), and the host/client structure from [berhbro](https://github.com/berhbro/dsh-cyrene-theme). See [Credits](#credits).
**这是补丁版，不是原创主题。** 代码主体来自 Columbina-sn，字体配色来自 hyposelen1a，双半结构源自 berhbro。详见[致谢](#credits)。

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
截图仅供参考，不构成「装完必然长这样」的承诺。

## What it is

| English | 中文 |
| --- | --- |
| A DSH theme plugin: palette, background image, glass effects, editable persona, sticker conventions | 一套 DSH 主题插件：换色、换背景、毛玻璃、可编辑人格、表情包约定 |
| This patched edition adds: theme-takeover switch, background upload, blur/clarity sliders, editable hero title | 补丁版额外提供：主题主权开关、背景上传、模糊/清晰度滑杆、可改欢迎标题 |

## What's changed

| Change · 改动 | Notes · 说明 |
| --- | --- |
| **Theme takeover** | When two Columbina themes are installed they fight over the same tokens. Toggle on → the other package (`dsh-columbina-theme`) is disabled through the host `pluginManager`; toggle off → it is restored and this theme steps aside. Persisted across restarts.<br>两套主题并存时会互相覆盖。开关打开 → 通过 `pluginManager` 真停用另一套；关掉 → 还给它并收起本主题样式。持久化。 |
| **Background upload** | Upload from the panel; files land in `Background/` and become the anchor. Hover `×` on a thumbnail to delete (with confirm). Never overwrites existing files, duplicates get a suffix.<br>面板里选图上传，自动设为起点；缩略图右上角 `×` 可删；重名不覆盖，自动加序号。 |
| **Blur & clarity** | Two independent sliders: blur 0–40px (preview while dragging, persist on release; the layer scales by `1 + radius/200` to hide the soft edge) and clarity, the positive phrasing of the veil opacity.<br>模糊 0–40px + 清晰度（色纱浓度的正向表述），两条独立滑杆。 |
| **Editable hero title** | The title lives in the state file and is editable from the panel. Empty `hero.image` hides the avatar entirely, leaving one line of text.<br>标题存在状态文件里，面板可改；`hero.image` 置空 = 只留一行字。 |
| **Font palette** | Label tokens now use hyposelen1a's palette: `#78007a` light, `#acabf2` dark, with secondary/tertiary derived at 0.82 / 0.6 alpha. The hero gradient follows.<br>label token 换成 hyposelen1a 版配色，欢迎语渐变同步。 |
| **Viewer text colour** | The plain-text preview ships its own green (`#2b8a3e`) that a container-level `color` cannot beat, so it is pinned on `pre[class*="_plain"]`. Syntax-highlighted spans keep their own colours.<br>预览器纯文本块自带绿色，直接钉到 `pre` 本体；语法高亮不受影响。 |
| **Hero image cache** | The image URL carries `?v=<path>` so `Cache-Control: max-age=300` cannot serve a stale avatar after a swap.<br>换图后地址带 `?v=`，避免看到缓存里的旧头像。 |

Everything else (persona section, sticker manifest, fenced routes, slot registration, glass CSS) is unchanged from upstream.

## Install

Requires a DSH desktop or web profile that loads client plugins; verified on **DSH 0.2.1-rc.2** (the Linux report also passed on 0.2.0-rc.2).
`dsh plugin` needs **pnpm** on PATH; if it fails with `spawn pnpm ENOENT`, run `corepack enable pnpm` first.
需要能正常加载客户端插件的 desktop / web profile；验证于 0.2.1-rc.2。

```powershell
dsh plugin --profile desktop add github:Columbla/dsh-columbina-theme-sn
```

From a local folder · 本地目录安装:

```powershell
dsh plugin --profile desktop add D:\path\to\dsh-columbina-theme-sn
```

Refresh the page afterwards (the client half is read on page load). Host-half changes need a plugin row reload or a DSH restart.
装完 **F5** 刷新一次页面；改了宿主半要重载插件行或重启 DSH。

```powershell
dsh plugin --profile desktop remove dsh-columbina-theme-sn
```

## Quick start

1. A `☾ 哥伦比娅` button appears next to Settings at the sidebar foot; the same editor is also under Settings → 哥伦比娅 · 主题.
2. Top switches: persona / skin / **theme takeover** / stickers / proactive stickers / no-repeat.
3. Background block: upload, pick an anchor, drag blur and clarity.
4. `恢复默认` refills the default persona draft; `重新载入` discards unsaved edits.

### When changes apply · 什么时候生效

| What · 改什么 | Applies · 生效方式 |
| --- | --- |
| Persona / switches / background settings (panel save) | Immediately, next turn · 立刻，下一轮对话 |
| `lib/client.js` | Page refresh · 刷新页面 |
| `lib/index.js`, `cordis.patch.yml` | Plugin row reload or restart · 重载插件行或重启 |
| `Background/` add / remove / rename | Page refresh · 刷新页面 |

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

| Field · 字段 | Meaning · 含义 |
| --- | --- |
| `persona` | Persona prompt; empty disables injection · 空串 = 不注入 |
| `enabled` / `theme` | Persona switch / skin switch · 人格开关 / 主题开关 |
| `takeover` | `true` disables the rival theme · 主题主权 |
| `stickers` / `proactive` / `noRepeat` | Sticker switches — prompt rules only, not message filters · 提示词规则 |
| `stickerSize` | Max sticker edge, 48–320px |
| `customStickers` / `stickerOverrides` | User-added entries / renames and hiding of bundled ones |
| `hero.image` | In-package relative path; **empty hides the avatar** · 空串不显示头像 |
| `hero.title` | The hero line · 欢迎页那行字 |
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

| Control · 旋钮 | Range · 区间 | Field · 字段 |
| --- | --- | --- |
| Blur · 模糊 | 0–40 px | `background.blur` |
| Clarity · 清晰度 | 5–100% | `background.dim = 1 - clarity/100` |
| Interval · 间隔 | 15–600 s | `background.interval` (only with `rotate`) |

Upload goes through `POST /columbina/backgrounds` (`data:image/*;base64`, ≤ 24MB). File names are stripped of path separators and reserved characters; duplicates get a suffix and existing files are never overwritten. Deletion uses `POST /columbina/backgrounds/remove` and only accepts names present in the scanned list.

## Assets

The repo ships a default asset set so it looks complete out of the box · 仓库附带默认素材，装完即有画面:

| Path · 位置 | Content · 内容 |
| --- | --- |
| `meme/` | 12 Columbina stickers (WebP) + hero avatar `星眸.png` |
| `Background/` | 3 presets: `01-columbina-light.png`, `02-columbina-dark.png`, `03-columbina-dusk.jpg` |

These images are **not** covered by MIT. Character art and official stickers belong to **miHoYo / HoYoverse**; the avatar and backgrounds belong to their respective artists. Non-commercial fan use only — open an issue and the assets will be removed on request.
这些图片**不在 MIT 范围内**，仅供非商业同人使用；权利人要求即删除。详见 [NOTICE.md](NOTICE.md)。

To use your own images: drop them into `Background/` and refresh; name sticker files after `stickers.json`'s `items[].file`; point `hero.image` at any in-package path (a circular PNG with alpha at 72px works best).

## Known issues & fixes

| Symptom · 现象 | Cause · 原因 | Fix · 解决 |
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

## Compatibility

| Item · 项目 | Notes · 说明 |
| --- | --- |
| Engine · 内核 | Chromium 76+ (Edge / Chrome / Electron); older engines lack `backdrop-filter` |
| GPU · 硬件加速 | With acceleration off, the glass degrades to a flat translucent layer |
| DPI · 缩放 | 100%–200% works; at 125% the 1px borders and 72px avatar read slightly differently |
| OS · 系统 | Verified on Windows 10/11; Linux and macOS untested |
| DSH · Harness | Verified on 0.2.1-rc.2; slots/APIs may differ in other versions |
| Fonts · 字体 | The hero prefers Source Han Serif / STSong, falls back to a system serif |

## Tests

```powershell
node test/contrast.mjs      # contrast, CSS-only · 对比度，纯 CSS 解析
node test/client.smoke.mjs  # client half with DOM stubs · 客户端半（DOM 桩）
node test/host.smoke.mjs    # host half, needs assets · 宿主半（需要素材）
```

Baseline · 基线: contrast **31/31**, client **130/130**, host **115/115** (host prints `SKIP` when assets are absent).

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
代码以 MIT 发布；图片素材不在 MIT 范围内。
