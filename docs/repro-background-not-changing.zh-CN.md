# 复现手册 · 「切背景 / 拖模糊没反应」

> 适用：DSH Web GUI 装了本主题后，**样式生效**（配色、毛玻璃都在），但：
> ① 面板里切内置背景图 → 画面不变；② 拖 blur 滑杆 → 画面不变；③ 上传自己的图 → 也不显示。
>
> 一句话判据：**这些设置其实都保存成功了，只是你看到的「背景」不是主题的，是别人盖在上面的一层。**

[English](repro-background-not-changing.md) | [中文](repro-background-not-changing.zh-CN.md)

---

## 0. 10 秒判定（先做这个，决定要不要往下走）

打开 GUI，按 F12 → Console，粘这三行：

```js
// A. 有没有一个「外来」全屏层盖住主题背景
document.querySelectorAll('body > *').length && !![...document.querySelectorAll('body > *')]
  .find(e => /wall|bg|backdrop|cover/i.test(e.id + ' ' + e.className))

// B. 主题背景层在不在、z-index 多少
getComputedStyle(document.querySelector('.colu-bg')).zIndex

// C. 主题背景图到底有没有被设上
document.querySelector('.colu-bg-img[data-active="1"]')?.style.backgroundImage
```

判读表：

| A | B | C | 结论 |
|---|---|---|---|
| `true`（有外来层，通常 id 是 `dsh-wall-layer`） | `-1` | 有 URL | ✅ **就是本文档这个坑** → 走 §1 |
| `false` | `-1` | 有 URL | 不是覆盖问题，去查 CSS 是否被 `prefers-color-scheme` 分支吃掉 → §3 |
| 任意 | `null`（元素不存在） | 空 | 主题根本没启用 / `data-columbina-skin` 不是 `on` → §3 |

---

## 1. 复现步骤（能 100% 复现出「没反应」）

环境：Linux，`dsh 0.2.0-rc.2`，`dsh web --port <p>`；浏览器 Chromium。

1. 装主题（本地目录方式）：
   ```bash
   dsh plugin --profile web add /path/to/dsh-columbina-theme-sn
   ```
   > 若报 `spawn pnpm ENOENT`：`corepack enable pnpm`
   > 若报 `Cannot validate installed package … SyntaxError: Unexpected token '\uFEFF'`：`package.json` 带 BOM，见 §4

2. 重启 `dsh web`（宿主半要重新加载），浏览器 F5。

3. 确认主题已生效：`document.body.dataset.dshColumbina` 应为 `""`（属性存在即可），配色变银蓝。

4. **制造覆盖层**（这就是本机真实情况；若你的宿主前端自带壁纸层，此时天然就有）：
   ```js
   const l = document.createElement('div');
   l.id = 'dsh-wall-layer';
   l.style.cssText = 'position:fixed;inset:0;z-index:0;background:#04060c;pointer-events:none';
   document.body.insertBefore(l, document.body.firstChild);
   ```

5. 打开主题面板 → **切一张内置背景** → **画面没变化** ❌

6. **拖 blur 滑杆到 30** → **画面没变化** ❌
   - 但此时 `GET /columbina/state` 里 `background.blur` 已经是 30（**持久化是好的**）
   - `.colu-bg-img` 的 `rect` 会随 `--colu-bg-scale` 变化（1536×(1+30/200) ≈ 1766）→ **变量在生效，只是看不见**

7. 删掉那层再试：`document.getElementById('dsh-wall-layer').remove()` → **背景 / 模糊立刻正常** ✅
   —— 这一步就是根因的直接证明。

---

## 2. 根因与修复

```css
/* 外来层（宿主前端壁纸）：不透明、z-index:0 */
#dsh-wall-layer{ position:fixed; inset:0; z-index:0; background:#04060c }
/* 主题背景层：在它下面 */
.colu-bg{ position:fixed; inset:0; z-index:-1; }
```

`-1 < 0` + 不透明 ⇒ 主题背景层整层被盖住。本机真实来源：`dsh-web-frontend/dist/index.html` 无条件引入
`wallpaper.css` / `wallpaper.js`（`wallpaper.js:14` 默认 `mode='static'`，`:177` 挂到 body 上）。

修复（v0.2.2 起随主题发布，只改客户端 CSS）：

```css
body[data-dsh-columbina][data-columbina-skin="on"][data-colu-bg="on"] #dsh-wall-layer{
  display:none !important;
}
```

- 只作用于「主题开 + 背景启用」；切「简约（无图）」时外来层自动回来
- client-only ⇒ **F5 即生效**，不用重启 `dsh web`

**临时绕过（不打补丁）**：把外来壁纸关掉即可，例如

```js
localStorage.setItem('dsh.wall.mode','none'); location.reload()
```

或点宿主自己的壁纸按钮选「不要壁纸」。

---

## 3. 若不是覆盖问题，按这个顺序查

| 顺序 | 检查 | 命令 / 代码 | 期望 |
|---|---|---|---|
| 1 | 主题是否真的启用 | `document.body.dataset.columbinaSkin` | `'on'` |
| 2 | 背景开关是否开 | `document.body.dataset.coluBg` | `'on'` |
| 3 | 状态是否写进磁盘 | `curl -s <host>/columbina/state` | 200，返回 `value.background.current` |
| 4 | 背景图能否取到 | `curl -sI <host>/columbina/background/<id>` | 200 + 正确 Content-Length |
| 5 | CSS 变量是否应用 | `getComputedStyle(document.body).getPropertyValue('--colu-bg-blur')` | 与滑杆值一致 |
| 6 | 图片元素是否真有图 | `document.querySelector('.colu-bg-img[data-active="1"]').style.backgroundImage` | `url("…/columbina/background/<id>?v=…")` |

6 步全绿但画面不对 ⇒ 一定还有一层盖着（回到 §0 的 A 项，把所有 `position:fixed` 且 `inset:0` 的元素列出来）：

```js
[...document.querySelectorAll('body *')].filter(e => {
  const s = getComputedStyle(e);
  return s.position === 'fixed' && s.inset === '0px' && s.zIndex !== 'auto';
}).map(e => ({tag: e.tagName, id: e.id, cls: e.className, z: getComputedStyle(e).zIndex, bg: getComputedStyle(e).backgroundColor}));
```

---

## 4. 相关坑（同一次踩到的）

| 现象 | 原因 | 修 |
|---|---|---|
| `dsh plugin add` 报 `spawn pnpm ENOENT` | 机器上没有 pnpm | `corepack enable pnpm` |
| `installation rejected: SyntaxError: Unexpected token '\uFEFF'` | `package.json` 带 UTF-8 BOM，Node `JSON.parse` 不接受 | 去掉 BOM（v0.2.2 已修），并加 `.gitattributes: *.json text eol=lf` |
| 改完 `lib/client.js` 页面没变 | 浏览器缓存 | Ctrl+Shift+R；确认 F12 Network 里 client bundle 是新内容 |
| 改了宿主半（persona / sticker / slot）没变 | 宿主半只在启动时加载 | 重启 `dsh web` |

---

## 5. 一句话总结

**「设置没生效」和「设置生效但被挡住」是两件事。**

先看状态接口有没有值（有 → 写入正常），再看 DOM 里那张图有没有 URL（有 → 主题正常），
最后找「谁盖在上面」——十有八九是宿主或别的插件加的全屏不透明层，`z-index` 比主题背景层的 `-1` 高。
