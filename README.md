# LeaferJS Presentation

**可独立运行的网页版演示文稿生成器**

> 纯前端 + AIGC：把大纲、文章、报告或一句主题描述，生成一份可直接翻页的网页版演示文稿。
> 仓库包含 AIGC 结构化提示词、前端结果渲染组件与本地网页入口；AI 推理由 transweb.cn 的 serverless 服务提供。

![Pure Frontend](https://img.shields.io/badge/architecture-pure%20frontend-6f5cff)
![Vue SFC](https://img.shields.io/badge/component-Vue%20SFC-42b883)
![Serverless](https://img.shields.io/badge/AI-transweb.cn%20serverless-2ea44f)
![Reveal.js](https://img.shields.io/badge/render-Reveal.js-6f5cff)
![License: MIT](https://img.shields.io/badge/license-MIT-2ea44f)

---

## 项目简介

LeaferJS Presentation 是一个**可独立运行的网页版演示文稿生成器**，也是一个纯前端项目。

仓库内没有自建后端、没有数据库、没有构建配置，也不需要在本地安装依赖。打开 [`appPage.html`](./appPage.html)，输入主题或粘贴素材，前端会把用户输入与当前应用标识提交给 **transweb.cn 的 serverless 服务**；服务按 AIGC 结构化提示词调用大模型，流式返回演示文稿 JSON，前端组件解析后把结果渲染成可翻页的网页幻灯片。

一句话概括：

```text
本地网页（纯前端） ──► transweb.cn serverless ──► 大模型 ──► 幻灯片 JSON ──► 前端渲染
```

## 项目组成

| 组成 | 文件 | 职责 |
| --- | --- | --- |
| **AIGC 结构化提示词** | [`appPrompt.md`](./appPrompt.md) | 约束模型的角色、JSON 结构、页型/元素、图表规范与内容压缩规则 |
| **前端 AI 结果渲染组件** | [`appCode.vue`](./appCode.vue) | Vue 单文件组件：输入向导、流式状态、幻灯片渲染、版本历史、编辑与导出 |
| **本地浏览器网页** | [`appPage.html`](./appPage.html) | 本地入口页，打开即用；以 iframe 加载 transweb.cn 上的同款应用 |
| **元数据** | [`manifest.json`](./manifest.json) | 应用来源、创建时间与文件清单 |

## 架构与数据流

```text
┌────────────────────────── 浏览器（纯前端） ──────────────────────────┐
│  appPage.html（本地入口）                                            │
│        │ iframe                                                      │
│        ▼                                                             │
│  appCode.vue（Vue SFC）                                              │
│   ├─ 输入主题 / 大纲 / 文章                                          │
│   ├─ 提交 context + documentId                                       │
│   ├─ 接收流式结果、解析 JSON                                         │
│   └─ 渲染幻灯片 / 版本 / 编辑 / 导出                                 │
└───────────────┬──────────────────────────────────────────────────────┘
                │ HTTPS 流式请求
                ▼
   transweb.cn serverless（提示词编排 / 模型调用 / 结果存储 / 登录与额度）
                │
                ▼
             大模型 → 幻灯片 JSON
```

### 提示词与前端的关系

- `appPrompt.md` 是本项目的 **AIGC 结构化提示词**，也是模型输出必须遵守的契约。
- 在线应用按 `documentId` 绑定平台侧的提示词配置；前端提交的是用户输入（`context`）与应用标识，而不是每次都上传整份提示词。
- 修改仓库里的 `appPrompt.md` 不会自动改变线上应用；需要在平台更新提示词配置，或把提示词接入你自建的 serverless 服务。

## 为什么是「纯前端 + serverless」

| 维度 | 说明 |
| --- | --- |
| **无自建后端** | 仓库只有 HTML、Vue SFC、Markdown 与 JSON，没有服务端代码 |
| **无需构建** | 没有 `package.json`、打包器或编译步骤；打开网页即可使用 |
| **前端负责交互与渲染** | 输入、流式状态、JSON 解析、幻灯片渲染、版本与导出都在浏览器完成 |
| **AI 在 serverless** | 模型调用、流式返回、结果存储由 transweb.cn 承担，前端不持有模型密钥 |
| **静态可托管** | 入口页可放在任意静态服务器、对象存储或本地目录 |

**独立运行的含义**：不依赖本地开发环境、不需要部署自己的后端、不需要安装 npm 包。它不是「离线运行」，生成能力依赖 transweb.cn 的 serverless 服务与网络。

## 运行方式

### 方式一：直接打开（最简单）

双击 [`appPage.html`](./appPage.html)，或在浏览器里打开它。页面会以 iframe 加载 transweb.cn 上的同款应用。

### 方式二：本地静态服务（推荐）

某些浏览器对 `file://` 下的 iframe、剪贴板或跨域行为有限制，建议起一个静态服务：

```bash
# 在仓库根目录
python3 -m http.server 8080
# 打开 http://localhost:8080/appPage.html
```

或者使用任意静态服务器（Nginx、Caddy、VS Code Live Server 等）。

### 运行前提

- 能访问 `transweb.cn`（AI 生成与结果存储依赖该服务）
- 现代浏览器（Chrome、Edge、Safari、Firefox）
- 无需 Node.js、npm、构建工具
- 登录状态与调用额度以 transweb.cn 当前策略为准

## serverless 依赖说明

| 能力 | 是否在仓库内 | 说明 |
| --- | --- | --- |
| 模型调用 | 否 | 由 transweb.cn serverless 调用大模型 |
| 提示词编排 | 否 | 平台按 `documentId` 绑定 AIGC 提示词 |
| 流式返回 | 否 | 前端通过 `aigcInterface` 接收流式结果 |
| 结果存储与版本 | 否 | 历史版本由 transweb.cn 保存 |
| 输入、解析、渲染、导出 | 是 | `appCode.vue` 在浏览器内完成 |

前端不保存模型 API Key，也不直接连接模型服务；所有生成请求都经过 transweb.cn。服务不可用、未登录或额度不足时，页面会给出对应提示。

## 目录结构

```text
LeaferJS-presentation/
├── appPrompt.md     # AIGC 结构化提示词（模型契约）
├── appCode.vue      # 前端 AI 结果渲染组件（Vue SFC，基于 transweb 前端运行时）
├── appPage.html     # 本地浏览器入口（iframe 加载在线应用）
├── manifest.json    # 应用元数据与来源
├── LICENSE          # MIT 许可证
└── README.md        # 本文件
```

## 它生成什么

一份可直接放映的网页版演示文稿，而不是一段需要二次排版的内容：

- **四种页型**：`title` 封面、`content` 内容页、`split` 图文混排、`summary` 总结
- **三种元素**：`text` 段落、`list` 列表、`chart` 图表
- **Chart.js 图表**：折线、柱状、饼图、雷达、散点，`chartData` 直接对应 Chart.js 配置
- **Mermaid 图表**：Flowchart、Sequence、Mindmap、Timeline、Architecture
- **自动选型**：趋势/对比/占比用 Chart.js；流程/架构/时间线用 Mermaid
- **内容压缩**：单段 ≤ 200 字、列表 ≤ 6 项、每项 ≤ 30 字
- **图表配比**：图表页建议占 20%–40%，不每页都放图
- **网页放映**：前端把 JSON 渲染为 Reveal.js 幻灯片，支持翻页与分享链接
- **唯一 ID**：每个 slide 的 `id` 唯一，便于锚点跳转与增量更新

## AIGC 结构化提示词

[`appPrompt.md`](./appPrompt.md) 把模型设定为「演示文稿架构师」，并强制约束输出。核心协议如下。

### 顶层结构

```json
{
  "title": "演示文稿主题",
  "presenter": "汇报人",
  "slides": [
    {
      "id": "cover",
      "type": "title",
      "title": "封面",
      "elements": []
    }
  ]
}
```

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `title` | `string` | 演示文稿主题 |
| `presenter` | `string` | 汇报人 |
| `slides` | `array` | 幻灯片数组，顺序即播放顺序 |

每个 slide 包含 `id`、`type`、`title`、`elements`；`id` 必须唯一。

### Slide 类型

| 类型 | 用途 | 建议元素 |
| --- | --- | --- |
| `title` | 封面页 | 一句话点题的 `text` |
| `content` | 内容页 | `text` / `list` / `chart` |
| `split` | 图文混排页 | `text` + `chart` |
| `summary` | 总结页 | `list`，提炼核心结论 |

### Element 类型

**text** — 段落、观点、说明（单段 ≤ 200 字）：

```json
{ "type": "text", "content": "内容" }
```

**list** — 提纲、关键结论、总结（每页 ≤ 6 条）：

```json
{ "type": "list", "content": ["要点1", "要点2"] }
```

**chart** — 必须同时包含 `chartType` 与 `chartData`，否则视为无效图表：

```json
{
  "type": "chart",
  "content": "图表说明",
  "chartType": "chartjs",
  "chartData": {}
}
```

### Chart.js 规范

`chartData` 直接使用 Chart.js 的 `type` / `data` 结构，`labels` 与每个 `datasets[].data` 长度必须一致：

```json
{
  "type": "chart",
  "content": "月活趋势",
  "chartType": "chartjs",
  "chartData": {
    "type": "line",
    "data": {
      "labels": ["1月", "2月", "3月"],
      "datasets": [
        { "label": "MAU", "data": [120, 180, 240] }
      ]
    }
  }
}
```

支持的图表类型：`line`（时间趋势）、`bar`（对比/排名）、`pie`（份额/结构）、`radar`（能力评估）、`scatter`（相关性）。

数据要求：labels 完整、datasets 完整、长度一致、数值真实合理；有业务数据必须使用业务数据，没有才按行业常识生成模拟数据。

### Mermaid 规范

`chartData.code` 是完整的 Mermaid 源码（注意 JSON 中的 `\n` 换行）：

```json
{
  "type": "chart",
  "content": "用户注册流程",
  "chartType": "mermaid",
  "chartData": {
    "code": "flowchart TD\nA[访问网站] --> B[注册账号]\nB --> C[邮箱验证]\nC --> D[进入系统]"
  }
}
```

常用图型：`flowchart`（流程/架构）、`sequenceDiagram`（系统交互/API 调用）、`mindmap`（知识结构）、`timeline`（路线图）、`flowchart LR`（技术架构）。

### 输出铁律

1. 只输出一个合法 JSON 对象；禁止 Markdown、代码围栏、注释、解释、推理过程和前后语。
2. 输出必须能被 `JSON.parse(response)` 直接解析：双引号、无尾随逗号、无注释、括号闭合。
3. 无法生成时输出 `{}`。
4. 每个 slide 的 `id` 必须唯一，推荐语义化命名（`cover`、`agenda`、`market-analysis`、`agent-workflow`）。
5. 内容高度压缩：单段 ≤ 200 字、列表 ≤ 6 项、每项 ≤ 30 字。
6. 图表页占比 20%–40%；能用文字表达时优先文字，能用 Mermaid 表达结构时优先 Mermaid。

## 渲染约定

前端结果组件把 JSON 映射为 Reveal.js 幻灯片：

| JSON | 渲染结果 |
| --- | --- |
| `slides[]` | 每个元素对应一个 `<section>` |
| `type: "title"` | 封面页，居中大标题 |
| `type: "content"` | 标准内容页，元素纵向排列 |
| `type: "split"` | 左右两栏（文本 + 图表） |
| `type: "summary"` | 总结页，列表为主 |
| `element.type: "text"` | 段落 |
| `element.type: "list"` | 无序列表 |
| `element.chartType: "chartjs"` | `<canvas>` + Chart.js 实例 |
| `element.chartType: "mermaid"` | Mermaid 容器 + `mermaid.render` |

Reveal.js 负责分页、转场、键盘导航与全屏；Chart.js 与 Mermaid 负责图表渲染。

## 前端渲染组件

[`appCode.vue`](./appCode.vue) 是平台运行时下的 Vue 单文件组件，负责从输入到成品的完整前端链路：

- **输入**：主题、PPT 大纲、文章、报告、方案，`solo` 模式与嵌入模式自适应
- **生成**：调用 `aigcInterface.streamRequest` 发起流式请求，展示加载进度与阶段提示，支持中止
- **解析**：用 `WebTool.extractJSON` 提取 JSON，格式异常时提示并允许重试
- **渲染**：把 `slides` 交给结果面板渲染为 Reveal.js 网页幻灯片，支持翻页与错误面板
- **版本**：历史版本列表、版本对比、切换与恢复
- **编辑**：基于当前内容继续修改，生成新版本
- **导出**：存档 HTML、下载 Markdown / 图片、复制分享链接
- **反馈**：内置问题反馈入口

> 组件依赖 transweb 前端运行时与 serverless 接口，不是可以直接 `import` 到任意 Vue 工程的 npm 包；它的价值在于展示 AIGC 结果如何被解析、渲染和管理。

## 技术栈

| 层 | 选型 |
| --- | --- |
| 本地入口 | 原生 HTML + iframe |
| 前端组件 | Vue 单文件组件（Options API）、Element 组件库、Tailwind 原子类 |
| AIGC 提示词 | Markdown 结构化提示词（`appPrompt.md`） |
| AI 服务 | transweb.cn serverless，流式返回 |
| 幻灯片渲染 | Reveal.js |
| 图表渲染 | Chart.js + Mermaid |
| 数据格式 | JSON（`title` / `presenter` / `slides`） |
| 结果存储 | transweb.cn 版本与历史记录 |

## 独立运行的边界

| 可以 | 不可以 |
| --- | --- |
| 本地双击或静态托管 `appPage.html` | 完全离线生成（需要 transweb.cn） |
| 不安装 Node.js、不构建、不部署后端 | 自带模型或本地模型直接推理 |
| 浏览器内完成输入、放映、版本、导出 | 不依赖账号/额度无限调用 |
| 把生成的演示文稿存档、分享、嵌入 | 直接修改 `appPrompt.md` 就让线上生效 |

如果要把 AI 能力迁到自己的基础设施，需要实现与 `aigcInterface` 兼容的流式接口，或把提示词接入自己的模型网关；这部分不在本仓库内。

## 交付验收清单

- [ ] 打开 `appPage.html` 能看到应用界面，而不是白屏
- [ ] 输入主题后能看到流式生成状态
- [ ] 生成结果能在网页中正常翻页
- [ ] 输出是合法 JSON，可直接 `JSON.parse`
- [ ] 每个 slide 的 `id` 唯一
- [ ] `type` 只使用 `title` / `content` / `split` / `summary`
- [ ] 每个 `chart` 都同时有 `chartType` 与 `chartData`
- [ ] Chart.js 的 labels 与 datasets 长度一致
- [ ] Mermaid `code` 换行正确、语法可渲染
- [ ] 历史版本可切换，编辑后可生成新版本

## 常见问题

**打开 `appPage.html` 一直白屏或转圈？**
先确认能访问 `https://transweb.cn`，再打开浏览器控制台看 iframe 是否被拦截。必要时改用本地静态服务（见「运行方式」），或直接访问在线应用地址。

**需要安装 Node.js 或 npm 吗？**
不需要。项目没有构建步骤；本地静态服务只是可选的更稳妥的打开方式。

**生成的是 PPT 文件吗？**
不是。生成的是网页版演示文稿（JSON 驱动 Reveal.js 渲染），可在浏览器中直接放映；需要 PPTX 时，可在结果基础上再做一层转换。

**可以离线使用吗？**
不能离线生成。已生成的演示文稿可以在本地查看，但图表与放映依赖 Reveal.js、Chart.js、Mermaid 等前端资源。

**能换成自己的模型吗？**
可以，但需要替换 `aigcInterface` 适配层或自建兼容的 serverless 服务，并自行实现流式协议。仓库不包含这部分实现。

**为什么改了 `appPrompt.md` 线上没变化？**
线上应用读取的是平台侧绑定的提示词配置；仓库里的文件是提示词契约与版本记录。要生效，需要在平台更新配置，或接入你自建的生成服务。

## 相关链接

- 在线应用：<https://transweb.cn/agent.html?id=ec6d0ef4ddfde4d983fdf934e57e29bb>
- 本地入口：[`appPage.html`](./appPage.html)
- Reveal.js：<https://revealjs.com/>
- Chart.js：<https://www.chartjs.org/>
- Mermaid：<https://mermaid.js.org/>
- 本仓库：<https://github.com/liuwenjin/LeaferJS-presentation>

## 许可

本项目采用 [MIT License](./LICENSE)，与所引用的前端资源保持一致。

| 资源 | 许可证 | 用途 |
| --- | --- | --- |
| [Reveal.js](https://github.com/hakimel/reveal.js) | MIT | 网页幻灯片放映 |
| [Chart.js](https://github.com/chartjs/Chart.js) | MIT | 数据图表渲染 |
| [Mermaid](https://github.com/mermaid-js/mermaid) | MIT | 流程图、时序图、思维导图等 |
| Vue / Element 组件库 / [Tailwind CSS](https://github.com/tailwindlabs/tailwindcss) | MIT | 前端运行时与界面样式 |

`transweb.cn` 的 serverless 服务不在本仓库内，按其服务条款使用；本仓库的 MIT 许可仅覆盖仓库中的提示词、组件与页面源码。

Copyright (c) 2026 liuwenjin。

再分发时请保留所引用资源的版权与许可声明；生成内容的许可由使用者自行决定。
