---
name: wechat-layout
description: 生成公众号文章的排版预览 HTML（含一键复制按钮，粘贴进公众号后台保留样式）。当用户要求"公众号排版""排版成公众号文章""生成公众号 HTML""复制到公众号后台"或写完一篇文章要出排版稿时，必须使用本 skill。样式规范已收敛（2 种字体、4 种颜色、红色预算），不要自由发挥。**选型**：短小纯文本、无章节、无引用卡需求的短文（几百到一千五百字的小散文）改用姊妹 skill `wechat-layout-minimal`；文章需要本规范里的章节标题/引用卡/金句/列表/表格任一件时，用本 skill。
---

# 公众号排版

把已成文的 markdown 文章排成可直接粘贴进公众号后台的 HTML 预览页。

> **选型**：短小纯文本、无章节、无引用卡需求的短文（几百到一千五百字的小散文）走姊妹 skill `wechat-layout-minimal`（零装饰、零红色、全文段落流）；文章需要本规范里的章节标题/引用卡/金句/列表/表格任一件时，用本 skill。

## 品牌信息（首次使用先确认）

排版稿的固定家具里有三处账号信息，用占位符表示，**不编造、不沿用示例值**：

| 占位符 | 含义 | 用在 |
| --- | --- | --- |
| `{{ACCOUNT_NAME}}` | 公众号名称 | 顶部引导行、结尾署名、预览页顶栏 |
| `{{SLOGAN}}` | 账号 slogan（可选） | 顶部引导第二行 |
| `{{TAGLINE}}` | 结尾一句话宣言（可选） | 结尾品牌区 |

- 对话或项目 `CLAUDE.md` 里已给出账号名 / slogan / tagline 的，替换占位符后正常输出。
- 没给出的：`{{ACCOUNT_NAME}}` 保留占位符输出，交付时提醒用户替换；`{{SLOGAN}}` / `{{TAGLINE}}` 未提供则**整行省略**，不要留空占位。

## 工作流程

1. 复制 `assets/skeleton.html` 为目标文件（通常放在文章所在目录，命名 `公众号排版.html`），替换两处 `{{TITLE}}` 为文章标题、按品牌信息节替换账号占位符，`{{ARTICLE}}` 为正文 HTML。正文 HTML = **顶部引导区 + 字数行 + 正文内容 + 结尾品牌区**：前后两块是固定家具（见下方对应小节），原样照抄，只写中间的正文内容。
2. 正文写在 `<section id="wx-article" style="...">` 里（见下），**全部使用行内样式**——class 和 style 标签在粘贴进公众号编辑器时会丢失，骨架页里的样式只服务于预览。"复制全文"带走的是容器的 **innerHTML**，容器自身的 style 不进编辑器：所以每个组件必须自带完整行内样式（font-size/line-height/color），不能依赖容器继承；组件不写 font-family，进编辑器后由官方默认栈接管。固定家具上的 `data-fx` 属性和占位符的 `fx-slot` class 只服务于预览页（自动扣字数、画虚线框），粘贴时被编辑器丢弃；**正文内容不要用它们**。
3. 标题只出现在骨架的 `.doc-title` 里，不进正文（用户会单独填进公众号标题栏）。
4. 外链、@账号、媒体名一律转纯文字（muted 色），公众号不支持外链跳转。
5. 完成后告诉用户文件路径，并提醒：用浏览器打开 → 点"复制全文" → 粘贴进公众号编辑器。

正文容器固定为：

```html
<!-- font-family 对齐微信官方默认栈（官方不建议自定义字体栈：iOS 17+ 会因字体映射出现字号、字间距漂移）。注意：复制带走的是容器 innerHTML，容器样式不进编辑器，这里的 font-family 只保证预览观感接近真机 -->
<section
  id="wx-article"
  style="font-family:'PingFang SC',system-ui,-apple-system,BlinkMacSystemFont,'Helvetica Neue','Hiragino Sans GB','Microsoft YaHei UI','Microsoft YaHei',Arial,sans-serif;color:#3A3631;font-size:16px;line-height:1.85;"
></section>
```

## 官方硬规范（对齐微信《编辑器插件开发规范》，2026-09 核对）

踩线会被编辑器自动删结构或真机翻车，先读再写：

- **行高**：多行文本 line-height 不得小于 1（本规范是 1.6–2.2，天然满足）；`line-height:1` 只允许用于单行装饰行（章节序号行、往期栏头行），且**装饰行容器必须显式 font-size = 行内最大字号**——不设的话容器按继承字号（16px）算出行高，会小于行内 20px 的楷体序号，微信编辑器报「行高小于字体大小」告警（实测命中过；补 font-size 后行盒高度不变，零视觉差异）。
- **宽度**：不设固定宽/高的大容器；表格列宽用百分比、不锁像素，防窄屏溢出截断。装饰性 inline-block（64px 短横线）不受限。
- **禁用清单**：`<pre>` 包正文、`text-align:start/end`、`!important`、opacity 藏字、SVG 动画交互、`leaf`/`nodeleaf` 属性——一律不用。
- **图片**：排版 HTML 里不写 `<img>`，配图由用户在编辑器里插入（外链图也会转存失败）。确需带图时，img 必须加 `data-w="原始像素宽度"`。
- **嵌套**：同标签+同样式的连续嵌套 ≤10 层（本规范最深 3 层，正常写不会超）。

## Dark Mode（微信自动转换，不干预）

发布后微信会用算法把文章转成暗色版（深色文字转浅、亮色背景压暗）：

- 正文 `#3A3631`、灰字、卡片底被自动转换属正常，**不做任何处理**，不加 `data-no-dark` / `data-ignore-dm`。
- 引用卡保持「背景容器 + 内部文本节点」结构——这是官方推荐结构，别把背景色拆到每个 `<p>` 上。
- 禁渐变背景、禁用图片承载文字；红色在暗色下由算法调亮，属正常。

## 样式规范（收敛版，严格执行）

这套规范的核心思路：**阅读靠段落和留白推进，不靠颜色推。装饰越少，重点越贵。**

### 字体：只有 2 种

- 正文/标题/数字一律系统黑体。**不要用 Georgia 等衬线体堆数字**。组件不自带 font-family——预览时继承容器（官方默认栈），进编辑器后由微信官方默认栈接管，这正是官方"不建议设置任何 font-family"的推荐做法。
- 楷体 `'KaiTi','STKaiti','Kaiti SC','KaiTi_GB2312',serif` 只用于：章节序号、章节小标题、结尾署名行（账号名那一行，落款感）。它是账号的"人格字体"，用多了就不珍贵了。**它是全文唯一的自定义字体栈**，仅限这几处短文本装饰（官方警告自定义字体栈在 iOS 17+ 有字号/字间距漂移风险，短文本可控；`Kaiti SC` 命中 macOS/iOS 的楷体，都没有时退到衬线体，可接受）。严禁用于正文段落。
- 若账号不想用楷体人格字体，删掉楷体栈、这几处改用系统黑体加粗即可，其余规范不变。

### 颜色：只有 4 种 + 2 个结构色

| 用途       | 色值      | 说明                                    |
| ---------- | --------- | --------------------------------------- |
| 正文       | `#3A3631` | 所有段落默认                            |
| 强调       | `#2F2B27` | **强调的默认手段：深色加粗**，不用红色  |
| 点缀（红） | `#B4532C` | 见下方"红色预算"                        |
| 次级（灰） | `#827A6D` | 来源标注、@账号、媒体名、图注、数据出处 |
| 线条       | `#E7DFD2` | 分隔线、表格边框                        |
| 卡片底     | `#F9F6F0` | 引用卡底色                              |

### 红色预算（最重要的一条）

红色 `#B4532C` 只允许出现在：

1. 章节序号（每个章节那一个汉字）；
2. 引用卡的左侧竖线；
3. **全文 1–2 处真正的关键结论**（`<strong>` 加粗 + 红色）。

除此之外一律不用红。普通重点 = `font-weight:700;color:#2F2B27`。如果发现自己标了第三处红色重点，回去删掉一处——重点多了等于没有重点。

### 段落（唯一高频组件）

```html
<p
  style="margin:0 0 22px;font-size:16px;line-height:1.85;letter-spacing:0.5px;color:#3A3631;text-align:justify;"
>
  正文……<strong style="font-weight:700;color:#2F2B27;">普通重点</strong>……
</p>
```

### 顶部引导区（固定家具，每篇必带，正文最前）

参考"点击上方蓝字 + slogan"的开场：两行灰色小字居中，是顶部的品牌时刻。"蓝字"指标题栏上方的官方账号名（微信自动染蓝），引导行本身保持全灰，不为此引入新颜色。账号名写 `{{ACCOUNT_NAME}}`（或已确认的账号名）。

```html
<p
  data-fx
  style="margin:0 0 6px;text-align:center;font-size:12.5px;letter-spacing:1.5px;color:#827A6D;"
>
  ☝ 点击上方蓝字"{{ACCOUNT_NAME}}"
</p>
<p
  data-fx
  style="margin:0 0 24px;text-align:center;font-size:12.5px;letter-spacing:2px;color:#827A6D;"
>
  {{SLOGAN}}
</p>
```

`{{SLOGAN}}` 未提供则删掉第二行，第一行 `margin` 改为 `0 0 24px`。

品牌头图（可选）不写进 HTML——用户粘贴后在编辑器里插到这两行之下、字数行之前。

### 字数 · 阅读时长行（每篇必带，顶部引导区之后、引用卡之前）

居中、小号、灰色——读者把它当"家具"，一眼扫过不抢戏，但能帮推荐流里的读者做"要不要读完"的决定。数字由骨架页的 JS 自动计算填入，随"复制全文"一起带进公众号；粘贴后在编辑器里若再改正文，记得手动改这行数字（或删掉整行）。

```html
<p
  id="wx-stat-line"
  data-fx
  style="margin:0 0 26px;text-align:center;font-size:12.5px;letter-spacing:1.5px;color:#827A6D;"
>
  全文 <span id="wx-stat-chars">—</span> 字 · 阅读约
  <span id="wx-stat-time">—</span> 分钟
</p>
```

它是引用卡之前最后一个家具元素，所以紧随其后的引用卡 `margin` 用 `30px 0 28px`，不再用 `0 0 28px`。

### 章节标题

序号用汉字（一二三……），楷体红色，两侧短横线；小标题楷体深色。散文类文章可以只要序号行、去掉小标题（`margin` 相应改为 `42px 0 28px`）。

```html
<section style="margin:42px 0 28px;text-align:center;">
  <section style="font-size:20px;line-height:1;">
    <span
      style="display:inline-block;width:64px;height:1px;background:#E7DFD2;vertical-align:middle;"
    ></span>
    <span
      style="display:inline-block;margin:0 16px;font-family:'KaiTi','STKaiti','Kaiti SC','KaiTi_GB2312',serif;font-size:20px;font-weight:700;color:#B4532C;vertical-align:middle;"
      >一</span
    >
    <span
      style="display:inline-block;width:64px;height:1px;background:#E7DFD2;vertical-align:middle;"
    ></span>
  </section>
  <p
    style="margin:14px 0 0;font-family:'KaiTi','STKaiti','Kaiti SC','KaiTi_GB2312',serif;font-size:18px;font-weight:700;line-height:1.6;letter-spacing:2px;color:#2F2B27;"
  >
    小标题写在这里
  </p>
</section>
```

首个章节标题的 `margin` 用 `0 0 28px`。

### 引用卡（全文唯一的"卡片"）

引言、对话、值得单独呈现的话用它。左侧红竖线是它唯一的装饰。**不要**再发明标签徽章、双栏统计卡、带色块的列表卡。

```html
<section
  style="margin:30px 0 28px;padding:22px 24px;background:#F9F6F0;border-left:3px solid #B4532C;border-radius:0 4px 4px 0;"
>
  <p
    style="margin:0;font-size:16.5px;line-height:1.95;letter-spacing:0.5px;color:#3A3631;text-align:justify;"
  >
    "引用的话。"
  </p>
</section>
```

### 列表

不用卡片、不用红色符号。加粗的深色引导词 + 普通段落即可；条目多时每条一个 `<p>`，引导词后接句号：

```html
<p
  style="margin:0 0 22px;font-size:16px;line-height:1.85;letter-spacing:0.5px;color:#3A3631;text-align:justify;"
>
  <strong style="font-weight:700;color:#2F2B27;">第一，怕温差。</strong
  >气温变化超过 5 度，鼻子立马有反应。
</p>
```

### 金句（可选，全文最多 1 处）

适合散文收尾或转折处的一句话总结。◆ 用红色（不计入"红色重点"预算，但也因此别再滥用）：

```html
<section style="margin:34px 0 26px;text-align:center;">
  <p style="margin:0 0 10px;font-size:12px;letter-spacing:2px;color:#B4532C;">
    ◆
  </p>
  <p
    style="margin:0;font-size:17.5px;line-height:1.8;font-weight:600;letter-spacing:1px;color:#2F2B27;"
  >
    那句话。
  </p>
</section>
```

### 表格（仅资讯类必需时）

表头浅底、边框 `#E7DFD2`、数字用正文黑体加粗（深色），不要衬线体、不要红色数字。斑马纹 `#FAF7F1`。

### 来源/说明行

```html
<p
  style="margin:10px 0 22px;font-size:12.5px;line-height:1.8;letter-spacing:0.5px;color:#827A6D;"
>
  数据来源：……
</p>
```

### 结尾品牌区（固定家具，每篇必带）

参考"分隔线 → END → 感谢 → 署名 → 名片 → 往期"的收尾动线。分隔线先给正文画句号；署名块是全文唯一一次"我是谁"的自我介绍，楷体只给第一行名字（落款感），后两行黑体灰。**名片和往期推荐不写内容，只留占位行**——用户粘贴后在编辑器里对应位置插入名片组件和往期链接，插好后删掉占位行。

```html
<hr data-fx style="margin:46px 0 0;border:0;border-top:1px solid #E7DFD2;" />

<section data-fx style="margin:32px 0 0;text-align:center;">
  <p
    style="margin:0;font-size:14px;font-weight:700;letter-spacing:4px;color:#827A6D;"
  >
    | END |
  </p>
  <p
    style="margin:12px 0 0;font-size:14px;font-weight:700;letter-spacing:3px;color:#827A6D;"
  >
    感谢你看到这里
  </p>
</section>

<section data-fx style="margin:28px 0 0;text-align:center;">
  <p
    style="margin:0;font-size:14px;line-height:2.1;letter-spacing:1px;color:#827A6D;"
  >
    {{TAGLINE}}
  </p>
</section>

<p
  data-fx
  style="margin:28px 0 0;text-align:center;font-size:14.5px;letter-spacing:1px;color:#3A3631;"
>
  点击下方名片 获取更多好文
</p>

<section data-fx class="fx-slot" style="margin:22px 0 0;text-align:center;">
  <p style="margin:0;font-size:12.5px;letter-spacing:2px;color:#827A6D;">
    〔 在此插入公众号名片 〕
  </p>
</section>

<section data-fx class="fx-slot" style="margin:16px 0 0;text-align:center;">
  <p style="margin:0;font-size:12.5px;letter-spacing:2px;color:#827A6D;">
    〔 在此插入往期文章推荐 〕
  </p>
</section>
```

`{{TAGLINE}}` 未提供则整段删掉，"点击下方名片"行的 `margin` 相应改为 `32px 0 0`。若账号想在署名块加账号名，可在 `{{TAGLINE}}` 段内加一行楷体署名（账号名，`font-size:14.5px;font-weight:700;letter-spacing:2px;color:#3A3631;`，放宣言行之前）——这是楷体的第三个允许位置。

资讯类的"本文信息截至……"说明行放在分隔线**之前**、作为正文最后一个段落（散文类省略）。若用户明确要纯文字往期栏，栏头楷体灰、条目深色居中纯文字标题，仍不写 `<a>`。

## 自检清单（写完过一遍）

- [ ] 正文里出现了除 4+2 色之外的颜色吗？
- [ ] 红色重点 ≤ 2 处？红色只出现在允许的 3 个位置？
- [ ] 字体只有系统黑体 + 楷体（数字没用衬线体）？
- [ ] 没有标签徽章、统计卡等多余组件？
- [ ] 正文最前是顶部引导区（引导行→slogan→字数行），都是灰色家具、原样照抄？账号占位符已按品牌信息节处理？
- [ ] 结尾品牌区完整：分隔线→| END |→感谢→署名→名片引导→两个占位行？名片和往期只是占位行，没有写真内容、没写 `<a>`？
- [ ] 字数·阅读时长行在顶部引导区之后？灰色小字居中、数字已由页面填好？
- [ ] 正文全部行内样式，无 style 标签、无外链 `<a>`？（data-fx / fx-slot 只出现在固定家具上）
- [ ] 标题只在 `.doc-title`，正文里没有重复标题？
- [ ] 没有 `!important`、`<pre>`、`text-align:start/end`、固定宽/高大容器、`<img>`？
- [ ] 多行文本行高均 ≥1（`line-height:1` 只出现在单行装饰行，且装饰行容器已显式 font-size = 行内最大字号）？
- [ ] 楷体只出现在允许位置（序号/小标题/结尾署名行），没有用于正文段落？

---

官方规范：微信公众平台《编辑器插件开发规范》 https://developers.weixin.qq.com/doc/service/guide/product/plugin_spec.html ；官方开源校验工具 verify-article-structure-spec（github.com/wechatjs/verify-article-structure-spec，可选：`npm run check 排版.html`，覆盖宽高/行高/暗色规则）。
