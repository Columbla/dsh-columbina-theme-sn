# Reproduction guide — "switching the background / dragging blur does nothing"

> Applies when the theme **is** working (palette and glass effects are visible) but:
> ① picking another built-in background does nothing; ② dragging the blur slider does nothing; ③ uploaded images never show.
>
> One-line diagnosis: **the settings are saved correctly — what you see is not the theme's background, but a layer stacked on top of it.**

[English](repro-background-not-changing.md) | [中文](repro-background-not-changing.zh-CN.md)

---

## 0. Ten-second check (do this first)

Open the GUI, press F12 → Console, paste these three lines:

```js
// A. is there a foreign full-screen layer covering the theme background?
document.querySelectorAll('body > *').length && !![...document.querySelectorAll('body > *')]
  .find(e => /wall|bg|backdrop|cover/i.test(e.id + ' ' + e.className))

// B. does the theme background layer exist, and what is its z-index?
getComputedStyle(document.querySelector('.colu-bg')).zIndex

// C. is the theme background image actually set?
document.querySelector('.colu-bg-img[data-active="1"]')?.style.backgroundImage
```

Interpretation:

| A | B | C | Conclusion |
|---|---|---|---|
| `true` (foreign layer present, usually `dsh-wall-layer`) | `-1` | a URL | ✅ **this guide's bug** → go to §1 |
| `false` | `-1` | a URL | Not an overlay problem; check whether CSS is swallowed by a `prefers-color-scheme` branch → §3 |
| any | `null` (element missing) | empty | Theme not enabled, or `data-columbina-skin` is not `on` → §3 |

---

## 1. Reproduction (100% deterministic)

Environment: Linux, `dsh 0.2.0-rc.2`, `dsh web --port <p>`; Chromium-based browser.

1. Install (from a local folder):
   ```bash
   dsh plugin --profile web add /path/to/dsh-columbina-theme-sn
   ```
   > `spawn pnpm ENOENT` → `corepack enable pnpm`
   > `Cannot validate installed package … SyntaxError: Unexpected token '\uFEFF'` → `package.json` has a BOM, see §4

2. Restart `dsh web` (the host half loads at startup), then F5 in the browser.

3. Confirm the theme is active: `document.body.dataset.dshColumbina` should be `""` (attribute present), and the palette turns silver-blue.

4. **Create the overlay** (this mirrors the real machine; a host that ships its own wallpaper layer has it already):
   ```js
   const l = document.createElement('div');
   l.id = 'dsh-wall-layer';
   l.style.cssText = 'position:fixed;inset:0;z-index:0;background:#04060c;pointer-events:none';
   document.body.insertBefore(l, document.body.firstChild);
   ```

5. Open the theme panel → **pick another built-in background** → **nothing changes** ❌

6. **Drag blur to 30** → **nothing changes** ❌
   - Yet `GET /columbina/state` already reports `background.blur = 30` (**persistence is fine**)
   - `.colu-bg-img`'s rect does change with `--colu-bg-scale` (1536×(1+30/200) ≈ 1766) → **the variable works, you just cannot see it**

7. Remove the layer and try again: `document.getElementById('dsh-wall-layer').remove()` → **background and blur work immediately** ✅
   — this step is the direct proof of the root cause.

---

## 2. Root cause and fix

```css
/* foreign layer (host wallpaper): opaque, z-index:0 */
#dsh-wall-layer{ position:fixed; inset:0; z-index:0; background:#04060c }
/* theme background layer: underneath it */
.colu-bg{ position:fixed; inset:0; z-index:-1; }
```

`-1 < 0` plus opacity ⇒ the whole theme background layer is covered. On the test machine the source was `dsh-web-frontend/dist/index.html` pulling in `wallpaper.css` / `wallpaper.js` unconditionally (`wallpaper.js:14` defaults to `mode='static'`, `:177` mounts it on `body`).

Fix (shipped since v0.2.2, client CSS only):

```css
body[data-dsh-columbina][data-columbina-skin="on"][data-colu-bg="on"] #dsh-wall-layer{
  display:none !important;
}
```

- Only applies when "theme on + background enabled"; plain mode (no image) brings the foreign layer back
- Client-only ⇒ **F5 is enough**, no `dsh web` restart

**Temporary workaround (without patching)**: turn the foreign wallpaper off, e.g.

```js
localStorage.setItem('dsh.wall.mode','none'); location.reload()
```

or pick "no wallpaper" in the host's own wallpaper menu.

---

## 3. If it is not an overlay problem, check in this order

| # | Check | Command / code | Expected |
|---|---|---|---|
| 1 | Theme really enabled | `document.body.dataset.columbinaSkin` | `'on'` |
| 2 | Background switch on | `document.body.dataset.coluBg` | `'on'` |
| 3 | State persisted | `curl -s <host>/columbina/state` | 200 with `value.background.current` |
| 4 | Image reachable | `curl -sI <host>/columbina/background/<id>` | 200 and correct Content-Length |
| 5 | CSS variable applied | `getComputedStyle(document.body).getPropertyValue('--colu-bg-blur')` | matches the slider |
| 6 | Image element has a URL | `document.querySelector('.colu-bg-img[data-active="1"]').style.backgroundImage` | `url("…/columbina/background/<id>?v=…")` |

All six green but the screen still wrong ⇒ something is still stacked on top (back to §0 A; list every `position:fixed; inset:0` element):

```js
[...document.querySelectorAll('body *')].filter(e => {
  const s = getComputedStyle(e);
  return s.position === 'fixed' && s.inset === '0px' && s.zIndex !== 'auto';
}).map(e => ({tag: e.tagName, id: e.id, cls: e.className, z: getComputedStyle(e).zIndex, bg: getComputedStyle(e).backgroundColor}));
```

---

## 4. Related pitfalls (hit in the same session)

| Symptom | Cause | Fix |
|---|---|---|
| `dsh plugin add` fails with `spawn pnpm ENOENT` | pnpm missing | `corepack enable pnpm` |
| `installation rejected: SyntaxError: Unexpected token '\uFEFF'` | `package.json` saved with a UTF-8 BOM, rejected by `JSON.parse` | BOM removed in v0.2.2; `.gitattributes: *.json text eol=lf` prevents relapse |
| `lib/client.js` edits do not show up | Browser cache | Ctrl+Shift+R; check the client bundle in F12 Network |
| Host-half changes (persona / sticker / slot) do not show up | The host half only loads at startup | Restart `dsh web` |

---

## 5. One-line summary

**"The setting did not apply" and "the setting applied but is covered" are different problems.**

Check the state endpoint first (a value means persistence works), then check whether the image element carries a URL (a URL means the theme works), and finally look for **who is stacked on top** — nine times out of ten it is an opaque full-screen layer added by the host or another plugin, with a `z-index` higher than the theme background layer's `-1`.
