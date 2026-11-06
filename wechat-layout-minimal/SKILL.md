---
name: wechat-layout-minimal
description: 生成公众号短小纯文本文章的极简排版 HTML（含一键复制按钮，粘贴进公众号后台保留样式）。当文章是几百到一千五百字的短散文、单场景小故事、全文只有段落流（无章节标题、无引用卡、无列表、无表格），或用户明确要求"极简排版""朴素排版""少装饰""不要花哨"时使用本 skill。文章需要章节标题、引用卡、金句、列表或表格时不要用本 skill，改用 wechat-layout。
---

# 公众号极简排版（短纯文本专用）

和 `wechat-layout`（常规版）是**二选一**关系，不是先后关系：

- 常规版：靠少量装饰组件推进阅读——章节序号（红/楷）、引用卡、红色预算、结尾 | END |。
- 本 skill：装饰降到零，**全文只有段落和留白**，适合几百字、单场景、一气呵成的小散文。

拿不准用哪版就问用户。文章明显长出结构（要分章、要引文卡、要列表表格）就回常规版。

## 品牌信息（首次使用先确认）

排版稿的固定家具里有账号信息，用占位符表示，**不编造、不沿用示例值**：

| 占位符 | 含义 | 用在 |
| --- | --- | --- |
| `{{ACCOUNT_NAME}}` | 公众号名称 | 顶部引导行、结尾署名、预览页顶栏 |
| `{{SLOGAN}}` | 账号 slogan（可选） | 顶部引导行 |
| `{{TAGLINE}}` | 结尾一句话宣言（可选） | 结尾署名第二行 |

- 对话或项目 `CLAUDE.md` 里已给出账号名 / slogan / tagline 的，替换占位符后正常输出。
- 没给出的：保留 `{{ACCOUNT_NAME}}` 占位符输出，交付时提醒用户替换；`{{SLOGAN}}` / `{{TAGLINE}}` 未提供则**整行省略**，不要留空占位。

## 工作流程

1. 复制 `assets/skeleton.html` 为文章目录下的 **`公众号排版-极简.html`**（文件名与常规版区分，同目录不互相覆盖），替换 `{{TITLE}}` 为文章标题、按品牌信息节替换账号占位符，`{{ARTICLE}}` 为正文 HTML。正文 HTML = 顶部引导区 + 字数行 + 段落流 + 结尾品牌区：前后家具原样照抄（下方代码块），只写中间段落。
2. 正文写在 `<section id="wx-article" style="...">` 里，**全部使用行内样式**——class 和 style 标签粘贴进公众号编辑器时会丢失；骨架页"复制全文"带走的是容器的 innerHTML，容器自身样式不进编辑器，所以每个组件必须自带完整行内样式（font-size/line-height/color），组件不写 font-family（进编辑器后由微信官方默认栈接管）。`data-fx` 属性和 `fx-slot` class 只服务于预览页（自动扣字数、画虚线框），粘贴时被编辑器丢弃；**正文段落不要用它们**。
3. 标题只出现在骨架的 `.doc-title` 里，不进正文（用户会单独填进公众号标题栏）。
4. 外链、@账号、媒体名一律转纯文字（muted 色），公众号不支持外链跳转。
5. 完成后告诉用户文件路径，并提醒：用浏览器打开 → 点"复制全文" → 粘贴进公众号编辑器 → 插入头图/名片/往期，删掉两行占位。

正文容器（与常规版一致，font-family 只保证预览观感接近真机）：

```html
<section
  id="wx-article"
  style="font-family:'PingFang SC',system-ui,-apple-system,BlinkMacSystemFont,'Helvetica Neue','Hiragino Sans GB','Microsoft YaHei UI','Microsoft YaHei',Arial,sans-serif;color:#3A3631;font-size:16px;line-height:1.9;"
></section>
```

## 官方硬规范（与常规版相同，简摘）

踩线会被编辑器自动删结构或真机翻车，先读再写：

- **行高**：多行文本 line-height 在 1.6–2.2 之间（本规范段落 1.9，天然满足）。极简版没有 `line-height:1` 的装饰行——不要发明这种结构。
- **宽度**：不设固定宽/高的大容器。
- **禁用清单**：`<pre>` 包正文、`text-align:start/end`、`!important`、opacity 藏字、SVG 动画交互、`leaf`/`nodeleaf` 属性——一律不用。
- **图片**：排版 HTML 里不写 `<img>`，配图由用户在编辑器里插入（外链图也会转存失败）。确需带图时，img 必须加 `data-w="原始像素宽度"`。
- **嵌套**：同标签+同样式的连续嵌套 ≤10 层（本规范最多 2 层，正常写不会超）。

## Dark Mode（微信自动转换，不干预）

发布后微信会用算法把文章转成暗色版（深色文字转浅、亮色背景压暗），正文和灰字被自动转换属正常，**不做任何处理**，不加 `data-no-dark` / `data-ignore-dm`。禁渐变背景、禁用图片承载文字。

## 样式规范（极简 = 零装饰）

常规版的思路是"装饰越少，重点越贵"；极简版再推进一步：**没有重点，只有节奏**。阅读完全靠段落长短变化和留白推进——这正是短散文在纸上本来的样子。

### 字体：只有 1 种

全文系统黑体，**零自定义字体栈——连楷体都不用**。组件不自带 font-family——预览时继承容器，进编辑器后由微信官方默认栈接管，这正是官方"不建议设置任何 font-family"的推荐做法（自定义字体栈在 iOS 17+ 有字号/字间距漂移风险）。

### 颜色：2 种正文色 + 2 个结构色

| 用途   | 色值      | 说明                                         |
| ------ | --------- | -------------------------------------------- |
| 正文   | `#3A3631` | 所有段落默认                                 |
| 强调   | `#2F2B27` | 深色加粗，**全文最多 1 处**（多数短文 0 处）|
| 次级   | `#827A6D` | 只给固定家具：顶部引导、字数行、结尾副行、占位行 |
| 线条   | `#E7DFD2` | 结尾分隔线                                   |

**零红色预算：任何位置都不出现 `#B4532C`。** 红色是常规版的人格色；极简版的人格是"什么都没有"。对话、金句、转折，一律让段落本身承担——想让人截图的金句，就让它**单独成段**，不加任何样式。

### 段落（唯一组件，打天下）

```html
<p
  style="margin:0 0 24px;font-size:16px;line-height:1.9;letter-spacing:0.5px;color:#3A3631;text-align:justify;"
>
  正文……
</p>
```

- 行高 1.9、段距 24px，比常规版（1.85 / 22px）略松——装饰全拆了，靠留白撑节奏。
- 对话就是普通段落，带引号即可，**不建引用卡**。
- 无章节标题、无列表样式、无表格、无金句卡、无 ◆。文章若长出这些需求，回常规版。

### 顶部引导区（固定家具，每篇必带）

比常规版两行合并成一行：

```html
<p
  data-fx
  style="margin:0 0 24px;text-align:center;font-size:12.5px;letter-spacing:1.5px;color:#827A6D;"
>
  ☝ 点击上方蓝字"{{ACCOUNT_NAME}}"｜{{SLOGAN}}
</p>
```

slogan 未提供则删掉 `｜{{SLOGAN}}`。品牌头图（可选）不写进 HTML——用户粘贴后在编辑器里插到这行之下、字数行之前。

### 字数 · 阅读时长行（固定家具，每篇必带，原样照抄）

居中、小号、灰色——读者把它当"家具"，一眼扫过不抢戏。数字由骨架页的 JS 自动计算填入，随"复制全文"一起带进公众号；粘贴后在编辑器里若再改正文，记得手动改这行数字（或删掉整行）。

```html
<p
  id="wx-stat-line"
  data-fx
  style="margin:0 0 30px;text-align:center;font-size:12.5px;letter-spacing:1.5px;color:#827A6D;"
>
  全文 <span id="wx-stat-chars">—</span> 字 · 阅读约
  <span id="wx-stat-time">—</span> 分钟
</p>
```

### 结尾品牌区（固定家具，每篇必带）

比常规版少了 `| END |`、"感谢你看到这里"和楷体署名——只留"线 + 名字 + 名片入口"。名片和往期推荐不写内容，只留占位行：用户粘贴后在编辑器里对应位置插入名片组件和往期链接，插好后删掉占位行，不写 `<a>`。

```html
<hr data-fx style="margin:40px 0 0;border:0;border-top:1px solid #E7DFD2;" />

<section data-fx style="margin:24px 0 0;text-align:center;">
  <p
    style="margin:0;font-size:14px;font-weight:700;letter-spacing:2px;color:#2F2B27;"
  >
    我是{{ACCOUNT_NAME}}
  </p>
  <p
    style="margin:4px 0 0;font-size:13px;line-height:1.9;letter-spacing:1px;color:#827A6D;"
  >
    {{TAGLINE}}
  </p>
</section>

<p
  data-fx
  style="margin:24px 0 0;text-align:center;font-size:14.5px;letter-spacing:1px;color:#3A3631;"
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

`{{TAGLINE}}` 未提供则删掉第二行即可，布局不受影响。

## 自检清单（写完过一遍）

- [ ] 全文只有段落？无章节标题/引用卡/金句卡/列表/表格/◆？
- [ ] 零红色（`#B4532C` 一次都没出现）？零楷体、零自定义字体栈？
- [ ] 深色加粗强调 ≤1 处？
- [ ] 家具齐全且原样：顶部一行引导 → 字数行 → 段落流 → 分隔线 → 署名两行 → 名片引导 → 两个占位行？账号占位符已按品牌信息节处理？
- [ ] 全部行内样式，无 style 标签、无 `<a>`、无 `<img>`、无 `!important`、无 `<pre>`、无固定宽高大容器？
- [ ] 标题只在 `.doc-title`，正文没有重复标题？
- [ ] `data-fx` / `fx-slot` 只出现在固定家具上，正文段落没用？
- [ ] 字数行数字已由页面填好？

---

官方规范：微信公众平台《编辑器插件开发规范》 https://developers.weixin.qq.com/doc/service/guide/product/plugin_spec.html ；官方开源校验工具 verify-article-structure-spec（github.com/wechatjs/verify-article-structure-spec，可选：`npm run check 公众号排版-极简.html`，覆盖宽高/行高/暗色规则）。
