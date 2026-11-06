# 公众号排版 Skill

把写好的 Markdown 文章排成**可直接粘贴进微信公众号后台**的 HTML：浏览器打开预览页 → 点「复制全文」→ 粘贴进公众号编辑器，样式随行内样式完整保留。

含两个 skill，按文章形态二选一，适配 Claude Code（`SKILL.md` + `assets/` 格式）。

## 两个版本，二选一

| Skill | 适用文章 | 特点 |
| --- | --- | --- |
| `wechat-layout`（常规版） | 有章节、引用、金句、列表或表格的文章 | 2 种字体、4 种颜色、红色预算；章节序号 + 引用卡 + 结尾品牌区 |
| `wechat-layout-minimal`（极简版） | 几百到一千五百字的纯段落短散文 | 零装饰、零红色、零自定义字体，全文靠段落和留白推进 |

判断标准：文章需要章节标题/引用卡/金句/列表/表格任一件 → 常规版；全文只有段落 → 极简版；拿不准 Claude 会问。

## 安装

```bash
git clone https://github.com/<你的GitHub用户名>/wechat-layout-skill.git
cp -r wechat-layout-skill/wechat-layout wechat-layout-skill/wechat-layout-minimal ~/.claude/skills/
```

- 全局安装：`~/.claude/skills/`（Windows：`C:\Users\<你>\.claude\skills\`）
- 项目安装：`<项目>/.claude/skills/`

装完新开会话即可生效。

## 使用

在 Claude Code 里直接说：

- 「把这篇文章排版成公众号 HTML」
- 「这篇是短散文，走极简排版」
- 「写完了，出公众号排版稿」

Claude 会生成 `公众号排版.html`（极简版为 `公众号排版-极简.html`），浏览器打开 → 复制全文 → 粘贴进公众号后台。

## 配置账号信息

排版稿的固定家具（顶部引导、结尾署名、预览页顶栏）里有三处账号信息，skill 用占位符表示：

| 占位符 | 含义 | 出现位置 |
| --- | --- | --- |
| `{{ACCOUNT_NAME}}` | 公众号名称 | 顶部引导行、结尾署名、预览页顶栏 |
| `{{SLOGAN}}` | 账号 slogan（可选） | 顶部引导第二行 |
| `{{TAGLINE}}` | 结尾一句话宣言（可选） | 结尾品牌区 |

三种给法任选其一：

1. 对话里直接告诉 Claude；
2. 写进项目的 `CLAUDE.md`（一次配置，长期生效）；
3. 什么都不做——Claude 保留占位符，并在交付时提醒你替换。

## 设计要点

- **全行内样式**：class 和 `<style>` 粘贴进公众号编辑器会丢失，所以每个组件自带完整行内样式；「复制全文」带走的是正文容器的 innerHTML（富文本 + 纯文本双格式，另有全选兜底）。
- **对齐微信官方硬规范**：行高、宽度、禁用清单（`<pre>`、`!important`、`text-align:start/end`、SVG 动画等）、嵌套层数、图片 `data-w`，均已对照《编辑器插件开发规范》核对。
- **暗色模式不干预**：发布后微信算法自动把文章转成暗色版，按官方推荐结构写即可，无需加任何属性。
- **字数 / 阅读时长自动计算**：骨架页 JS 排除固定家具后统计正文字数，随复制一起带进公众号。
- **红色预算**：红色只给章节序号、引用卡竖线和全文 1–2 处关键结论——重点多了等于没有重点（极简版零红色）。
- **名片 / 往期占位行**：粘贴后在编辑器对应位置插入名片和往期推荐，删掉占位行即可；不伪造 `<a>` 外链（公众号不支持外链跳转）。

## 校验（可选）

官方开源校验工具 [verify-article-structure-spec](https://github.com/wechatjs/verify-article-structure-spec)：

```bash
npm run check 公众号排版.html
```

覆盖宽高、行高、暗色等规则。官方规范全文：[微信公众平台《编辑器插件开发规范》](https://developers.weixin.qq.com/doc/service/guide/product/plugin_spec.html)。

## 目录结构

```
wechat-layout-skill/
├── README.md
├── wechat-layout/
│   ├── SKILL.md
│   └── assets/skeleton.html
└── wechat-layout-minimal/
    ├── SKILL.md
    └── assets/skeleton.html
```

> 说明：预览页标题字体走 Google Fonts（Noto Serif SC），加载失败自动退到系统宋体，不影响排版和复制。
