# 哥伦比娅主题 · 补丁版

[**English**](README.md) | [**中文**](README.zh-CN.md)

给 DeepSeek Harness 的 Web GUI 换一套「月隐银蓝」毛玻璃皮肤，并附带一个可编辑的哥伦比娅人格。

**这是补丁版，不是原创主题。** 代码主体来自 [Columbina-sn](https://github.com/Columbina-sn/dsh-columbina-theme)，字体配色来自 [hyposelen1a](https://github.com/hyposelen1a/dsh-columbina-theme)，宿主/客户端双半结构源自 [berhbro](https://github.com/berhbro/dsh-cyrene-theme)。详见[致谢](#致谢)。

## 声明

这套主题是纯 CSS + 浏览器渲染，最终长相取决于你的机器：

- **毛玻璃**（`backdrop-filter`）需要 Chromium 76+ / Edge / Chrome。旧内核、关闭硬件加速、部分远程桌面、虚拟机或集成显卡驱动下会退化为一层半透明色块——**看不到模糊**。
- **渐变裁字**（`background-clip:text`）在少数 GPU 驱动上会整行不渲染。项目对 `@supports` 不支持的情况有纯色兜底，但观感不同。
- **深浅色**跟随系统 / Harness 的外观设置（`prefers-color-scheme` 与 `body[data-ds-dark-theme]`），不是插件自己判断的。
- **高 DPI**（125% / 150% / 200% 缩放）下，72px 头像、1px 边框、2px 字距的观感会有差异；截图像素不等于 CSS 像素。
- **广色域屏幕 / 色彩管理**会让 `#78007a` 这类高饱和紫显得更艳或更灰。不同屏幕上，「同一个颜色」本就不相同。
- **字体**：欢迎语优先思源宋体 / 华文宋体，机器上没有就回落到系统衬线，字宽与字距会变。
- **宿主壁纸层**：如果你的 Harness 前端自带一层全屏壁纸（如 `#dsh-wall-layer`，不透明、`z-index:0`），它会盖住本主题 `z-index:-1` 的背景层。自 v0.2.2 起，主题背景启用时会收起那层，切回简约模式自动恢复。

截图仅供参考，不构成「装完必然长这样」的承诺。

## 这是什么

一套 DSH 主题插件：换色、换背景、毛玻璃、可编辑人格、表情包约定。
补丁版额外提供：主题主权开关、背景上传、模糊/清晰度滑杆、可改欢迎标题。

## 相比上游改了什么

| 改动 | 说明 |
| --- | --- |
| **主题主权** | 两套哥伦比娅主题并存时会互相覆盖。打开 → 通过宿主 `pluginManager` 真停用另一套（`dsh-columbina-theme`）；关掉 → 恢复它并收起本主题样式。状态持久化，重启后自动恢复。 |
| **背景图上传** | 面板里选图上传，落进 `Background/` 并自动设为起点；缩略图右上角 `×` 可删（带确认）。重名不覆盖，自动加序号。 |
| **模糊 / 清晰度** | 两条独立滑杆：模糊 0–40px（拖动预览、松手落盘，图层按 `1 + 半径/200` 放大以盖住糊边）；清晰度是色纱浓度的正向表述。 |
| **可改欢迎标题** | 标题存在状态文件里，面板可改。`hero.image` 置空则整块头像不渲染，只留一行字。 |
| **字体配色** | label token 换成 hyposelen1a 版：浅色 `#78007a`、深色 `#acabf2`，次级/三级按 0.82 / 0.6 透明度派生；欢迎语渐变同步。 |
| **右侧查看区正文色** | 预览器的纯文本块自带绿色（`#2b8a3e`），容器级 `color` 压不住，因此直接钉到 `pre[class*="_plain"]`；语法高亮的行内彩色不受影响。 |
| **头像缓存** | 形象地址带 `?v=<路径>`，避免 `Cache-Control: max-age=300` 在换图后仍显示旧头像。 |
| **宿主壁纸层** | 主题背景启用时收起宿主的全屏壁纸层（`#dsh-wall-layer`），v0.2.2 起。 |

其余（人格 section、表情包清单、fenced 路由、槽位注册、毛玻璃 CSS）与上游一致。

## 安装

前置条件：DSH 的 desktop 或 web profile 能正常加载客户端插件。验证于 **DSH 0.2.1-rc.2**；Linux 实测 **0.2.0-rc.2** 也正常。

`dsh plugin` 依赖 **pnpm**；若报 `spawn pnpm ENOENT`，先执行 `corepack enable pnpm`。

从 GitHub 安装：

```powershell
dsh plugin --profile desktop add github:Columbla/dsh-columbina-theme-sn
```

本地目录安装：

```powershell
dsh plugin --profile desktop add D:\path\to\dsh-columbina-theme-sn
```

装完刷新一次页面（客户端半是页面加载时读的）；改了宿主半要重载插件行或重启 DSH。

```powershell
dsh plugin --profile desktop remove dsh-columbina-theme-sn
```

## 快速上手

1. 侧边栏底部、设置按钮旁出现「☾ 哥伦比娅」；「设置 → 哥伦比娅 · 主题」是同一份编辑器。
2. 顶部开关：人格 / 主题 / **主题主权** / 表情包 / 主动贴表情 / 防连发。
3. 背景区：上传、选起点、拖模糊与清晰度。
4. 「恢复默认」把默认人格底稿填回编辑框；「重新载入」丢弃未保存的改动。

### 什么时候生效

| 改什么 | 生效方式 |
| --- | --- |
| 人格 / 开关 / 背景设定（面板保存） | 立刻，下一轮对话 |
| `lib/client.js` | 刷新页面 |
| `lib/index.js`、`cordis.patch.yml` | 重载插件行或重启 |
| `Background/` 增删改名 | 刷新页面 |

## 状态文件与字段

状态文件默认在 `$DSH_HOME/columbina-theme.json`；没有 `DSH_HOME` 时是 `~/.dsh/columbina-theme.json`。

```json
{
  "persona": "……人格提示词……",
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

| 字段 | 含义 |
| --- | --- |
| `persona` | 人格提示词；空串 = 不注入 |
| `enabled` / `theme` | 人格开关 / 主题开关 |
| `takeover` | `true` 表示停用另一套主题 |
| `stickers` / `proactive` / `noRepeat` | 表情包开关——都是提示词规则，不是消息拦截器 |
| `stickerSize` | 贴图最大边长 48–320px |
| `customStickers` / `stickerOverrides` | 自加条目 / 包内条目的改名与隐藏 |
| `hero.image` | 包内相对路径；**空串不显示头像** |
| `hero.title` | 欢迎页那行字 |
| `background.dim` | 色纱浓度 0–0.95，越高越淡；面板「清晰度」= `1 - dim` |
| `background.blur` | 模糊半径 0–40px |
| `rev` | 写入版本号，手工改文件记得一起调大 |

写入走「临时文件 + rename」，并带 `rev` 乐观并发：缺 `rev` → 400 `client-outdated`，过期 → 409 `stale`。

## 主题主权

两个包都会覆写 `--dsw-alias-*` 并各自铺背景层，只能有一个说话。本版不靠 CSS 抢优先级：

```js
const RIVAL_PACKAGE = 'dsh-columbina-theme'          // 另一套的包名
const manager = ctx.get('pluginManager')
await manager.setPluginEnabled(rival.entryId, !takeover)
```

- 打开 → 另一套在 profile 的 `cordis.patch.yml` 里写成 `disabled: true`（持久，重启不会自己回来）。
- 关掉 → 恢复 `disabled: false`，同时把本主题的 `theme` 一起关掉，避免「两套都没了」的空窗。
- 宿主启动 1.5 秒后，按状态文件里的 `takeover` 再确认一次。
- 没装另一套、或 profile 里没有 `pluginManager` 时，开关只给一句提示，不会拖垮插件。

## 背景与模糊

| 旋钮 | 区间 | 字段 |
| --- | --- | --- |
| 模糊 | 0–40 px | `background.blur` |
| 清晰度 | 5–100% | `background.dim = 1 - clarity/100` |
| 间隔 | 15–600 s | `background.interval`（仅 `rotate` 开启时） |

上传走 `POST /columbina/backgrounds`（`data:image/*;base64`，≤ 24MB）。文件名会剥掉路径分隔符与保留字符，重名自动加序号，**永不覆盖**已有文件。删除走 `POST /columbina/backgrounds/remove`，只认清单里扫到的文件名。

## 素材

仓库附带一套默认素材，装完即有画面：

| 位置 | 内容 |
| --- | --- |
| `meme/` | 12 张哥伦比娅表情（WebP）+ 欢迎页头像 `星眸.png` |
| `Background/` | 3 张预设：`01-columbina-light.png`、`02-columbina-dark.png`、`03-columbina-dusk.jpg` |

这些图片**不在 MIT 范围内**。角色形象与官方表情版权归 **miHoYo / HoYoverse**，头像与背景图归各自原作者。仅限**非商业同人使用**；权利人要求即删除。详见 [NOTICE.md](NOTICE.md)。

换成自己的图：丢进 `Background/` 刷新即可；表情按 `stickers.json` 的 `items[].file` 命名；头像路径填进 `hero.image`（建议带 alpha 的圆形 PNG，72px 显示）。

## 已知问题与解决方案

| 现象 | 原因 | 解决 |
| --- | --- | --- |
| 重启后欢迎页头像自己回来了 | 页面加载时状态未回，`heroReady` 默认 `true`，浏览器拿缓存里的旧头像顶上 | 已修：`heroKey` 为空时根本不渲染 `<img>` |
| 换了头像还是旧图 | `/columbina/hero` 带 `Cache-Control: max-age=300` | 已修：地址带 `?v=<路径>` |
| 切换背景图、拖模糊都没反应 | 宿主前端自带的壁纸层（`#dsh-wall-layer`）盖住了本主题 `z-index:-1` 的背景层 | v0.2.2 已修：主题背景启用时收起那层 |
| `dsh plugin add` 报 `Unexpected token '\uFEFF'` | `package.json` 存成了带 BOM 的 UTF-8，`JSON.parse` 不认 | v0.2.2 已修；`.gitattributes` 防复发 |
| 右侧查看区文字没变色 | 预览器纯文本块自带绿色 `#2b8a3e`，容器级 `color` 压不住 | 已修：钉到 `pre[class*="_plain"]`（`!important`，仅纯文本渲染器） |
| 两套主题颜色互相打架 | 都覆写 `--dsw-alias-*`，谁在后面谁说话 | 打开「主题主权」 |
| 整个窗口发糊 | 给 `[data-shell-overlay]` 加了 `backdrop-filter`（它铺满整帧） | 只给浮层的直接子元素加 |
| 鼠标移到发送键上界面闪 | `[data-composer-card]` 成了 `position:fixed` 后代的包含块 | 毛玻璃画在 `::before` 上 |
| 右侧栏收起后那块一直发雾 | 给 `[data-sidebar-right-panel]` 加了模糊 | 改打真正的窗格 `[data-dockkit-pane]` |
| 深色欢迎语变成一块浅色方块 | 用了 `background` 简写，重置了 `background-clip:text` | 用 `background-image`，并重新声明裁字 |
| 表情包不显示 | `stickers.json` 的路径与实际文件对不上 | 看 `/columbina/stickers` 返回的 `missing` |
| 主动贴表情没反应 | 规则在宿主半，只刷页面不更新 | 重载插件行或重启 DSH |
| 深色主文字偏淡 | `#acabf2` 在本主题更深的底上只有 5.99:1（低于自定 7:1，高于 AA 4.5:1） | 换成同色系 `#c3c2fb`（7.55:1） |
| 中文文件名上传后变成 `custom-xxxx` | 请求体没按 UTF-8 编码，文件名被剥成空 | 浏览器 `fetch` 默认正确；脚本上传请带 `charset=utf-8` |

「切背景 / 拖模糊没反应」这类问题的完整复现手册：[docs/repro-background-not-changing.zh-CN.md](docs/repro-background-not-changing.zh-CN.md)（中文）· [English](docs/repro-background-not-changing.md)。

## 兼容性

| 项目 | 说明 |
| --- | --- |
| 内核 | Chromium 76+（Edge / Chrome / Electron）；旧内核无 `backdrop-filter` |
| 硬件加速 | 关闭硬件加速时，毛玻璃退化为半透明色块 |
| 缩放 | 100%–200% 均可；125% 下 1px 边框与 72px 头像观感略有差异 |
| 系统 | Windows 10/11 与阿里云 Linux 已验证；macOS 未测 |
| DSH | 0.2.1-rc.2 与 0.2.0-rc.2 已验证；其他版本槽位/API 可能不兼容 |
| 字体 | 欢迎语优先思源宋体 / 华文宋体，缺失时回落系统衬线 |

## 自检

```powershell
node test/contrast.mjs      # 对比度，纯 CSS 解析
node test/client.smoke.mjs  # 客户端半（DOM 桩）
node test/host.smoke.mjs    # 宿主半（需要素材）
```

基线：contrast **31/31**、client **130/130**、host **115/115**（缺素材时 host 会打印 `SKIP`）。

## 致谢

| 项目 | 作者 | 用到了什么 |
| --- | --- | --- |
| [dsh-columbina-theme](https://github.com/Columbina-sn/dsh-columbina-theme) | **Columbina-sn** | 主题本体：双半结构、fenced 路由、槽位注册、人格 section、「月隐银蓝」配色与毛玻璃、表情包清单、自检脚本 |
| [dsh-columbina-theme](https://github.com/hyposelen1a/dsh-columbina-theme) | **hyposelen1a** | 字体配色（`#78007a` / `#acabf2`）与「蒙版压暗」思路；主题主权开关停用的就是这一套 |
| [dsh-cyrene-theme](https://github.com/berhbro/dsh-cyrene-theme) | **berhbro** | 双半结构、`rev` 乐观并发、槽位注册与自检脚本的原始出处 |

原 MIT 声明保留在 [LICENSE](LICENSE)（Copyright © 2026 berhbro）。

**角色与美术**：哥伦比娅及《原神》的角色形象、名称、官方表情包版权归 **miHoYo / HoYoverse** 所有，**不在 MIT 范围内**。本项目与米哈游 / HoYoverse / DeepSeek 无隶属、合作或背书关系。详见 [NOTICE.md](NOTICE.md)。

## 许可

代码以 MIT 发布；图片素材不在 MIT 范围内。
