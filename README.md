# LeaferJS Presentation · 演示文稿 JSON 生成器

> 把 PPT 大纲、文章、报告、方案或一句主题描述，转换成**可直接被 Reveal.js 渲染**的结构化 JSON。
> 只输出 JSON，不写 HTML、不写 Markdown、不做解释 —— 让「内容 → 幻灯片」这一步变成可程序化消费的数据。

![Reveal.js](https://img.shields.io/badge/target-Reveal.js-6f5cff)
![Chart.js](https://img.shields.io/badge/chart-Chart.js-2ea44f)
![Mermaid](https://img.shields.io/badge/diagram-Mermaid-2ea44f)

---

## 这是什么

本仓库是一套**面向大模型的演示文稿生成提示词**（`appPrompt.md`）及其配套页面。它把模型设定为「演示文稿架构师」，任务是把用户提供的素材转成结构化 JSON，兼顾：

- **信息准确性**：不编造事实，业务数据优先
- **演讲叙事逻辑**：封面 → 内容 → 图文混排 → 总结
- **页面可视化表达**：文字、列表、图表各司其职
- **图表可直接渲染**：Chart.js 与 Mermaid 都有固定数据结构

与其他三个 LeaferJS 工具不同，这里**不生成 HTML 代码**，只生成幻灯片数据；渲染由 Reveal.js 查看器完成。

## 在线体验

- 平台内体验：<https://transweb.cn/agent.html?id=ec6d0ef4ddfde4d983fdf934e57e29bb>
- 本仓库的 `appPage.html` 以 `iframe` 嵌入同一在线应用，可直接用浏览器打开。

## 核心特性

| 能力 | 说明 |
| --- | --- |
| 纯 JSON 输出 | 只允许一个合法 JSON 对象，可直接 `JSON.parse` |
| 四种页型 | `title` 封面、`content` 内容页、`split` 图文混排、`summary` 总结 |
| 三种元素 | `text` 段落、`list` 列表、`chart` 图表 |
| Chart.js 图表 | 折线、柱状、饼图、雷达、散点，`chartData` 直接对应 Chart.js 配置 |
| Mermaid 图表 | Flowchart、Sequence、Mindmap、Timeline、Architecture |
| 自动选型 | 趋势/对比/占比用 Chart.js；流程/架构/时间线用 Mermaid |
| 内容压缩 | 单段 ≤ 200 字、列表 ≤ 6 项、每项 ≤ 30 字 |
| 图表配比 | 图表页建议占 20%–40%，不每页都放图 |
| 唯一 ID | 每个 slide 的 `id` 唯一，便于锚点跳转与增量更新 |

## 交付协议（硬约束）

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

每个 slide 包含 `id`、`type`、`title`、`elements`。`id` 必须唯一。

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

该 JSON 是**渲染无关的中间格式**，查看器按以下约定映射到 Reveal.js：

| JSON | Reveal.js |
| --- | --- |
| `slides[]` | 每个元素对应一个 `<section>` |
| `type: "title"` | 封面 section，居中大标题 |
| `type: "content"` | 标准内容 section，元素纵向排列 |
| `type: "split"` | 左右两栏 section（文本 + 图表） |
| `type: "summary"` | 总结 section，列表为主 |
| `element.type: "text"` | 段落 |
| `element.type: "list"` | 无序列表 |
| `element.chartType: "chartjs"` | `<canvas>` + Chart.js 实例 |
| `element.chartType: "mermaid"` | Mermaid 容器 + `mermaid.render` |

Reveal.js 负责分页、转场、键盘导航与全屏；Chart.js 与 Mermaid 负责图表渲染。平台端已内置这套查看器，`appCode.vue` 是它的 UI 壳。

## 快速开始

### 1. 准备提示词

将 [`appPrompt.md`](./appPrompt.md) 全文作为**系统提示词**（System Prompt）。它包含角色、JSON 铁律、数据结构、图表规范、编排规则与压缩规则。

### 2. 提供素材

可以直接给主题：

> 做一个关于「AI 应用工厂」的 8 页汇报，包含团队 2024–2026 年的产品路线图和 Agent 工作流。

也可以粘贴大纲、文章或报告。模型会自行决定页型、元素与图表类型，**不会反问**。

### 3. 解析 JSON

```js
const deck = JSON.parse(responseText);
console.log(deck.title, deck.slides.length);
```

### 4. 渲染

- 在 transweb.cn 的演示文稿应用中直接查看；
- 或把 `slides` 映射到你的 Reveal.js / 任意前端渲染器。

## 目录结构

```text
LeaferJS-presentation/
├── README.md        # 本文件
├── appPrompt.md     # 完整提示词（协议、图表规范、编排规则）
├── appCode.vue      # 平台端 Vue 单文件组件（在线应用的 UI 壳）
├── appPage.html     # iframe 嵌入页，指向 transweb.cn 在线应用
└── manifest.json    # 应用元数据与来源
```

说明：本工具的输出是 JSON 数据，不包含可直接打开的 HTML；`appCode.vue` 是平台侧交互界面，不负责渲染幻灯片。

## 完整示例

```json
{
  "title": "AI 应用工厂",
  "presenter": "Transweb",
  "slides": [
    {
      "id": "cover",
      "type": "title",
      "title": "AI 应用工厂",
      "elements": [
        { "type": "text", "content": "把一句话变成可上线的应用" }
      ]
    },
    {
      "id": "market",
      "type": "split",
      "title": "市场增长",
      "elements": [
        { "type": "text", "content": "过去三年，AI 应用需求持续增长。" },
        {
          "type": "chart",
          "content": "年度应用数量",
          "chartType": "chartjs",
          "chartData": {
            "type": "bar",
            "data": {
              "labels": ["2024", "2025", "2026"],
              "datasets": [
                { "label": "应用数量", "data": [1200, 3800, 9600] }
              ]
            }
          }
        }
      ]
    },
    {
      "id": "workflow",
      "type": "content",
      "title": "Agent 工作流",
      "elements": [
        {
          "type": "chart",
          "content": "从需求到上线",
          "chartType": "mermaid",
          "chartData": {
            "code": "flowchart LR\nUser --> Agent\nAgent --> LLM\nLLM --> App\nApp --> Deploy"
          }
        }
      ]
    },
    {
      "id": "summary",
      "type": "summary",
      "title": "总结",
      "elements": [
        {
          "type": "list",
          "content": ["一句话生成应用", "工作流可编排", "一键部署上线"]
        }
      ]
    }
  ]
}
```

## 内容编排建议

- **封面**：一句话点题，不堆副标题。
- **内容页**：一页一个观点；能用列表就不用长段落。
- **图文混排**：文字给结论，图表给证据；不要让图表解释文字。
- **总结页**：3–5 条结论，可执行、可记忆。
- **标题**：短、具体、有信息量；避免「背景介绍」「相关分析」这类空标题。

## 交付验收清单

- [ ] 输出是唯一一个合法 JSON，可直接 `JSON.parse`
- [ ] 顶层只有 `title`、`presenter`、`slides`
- [ ] 每个 slide 的 `id` 唯一
- [ ] `type` 只使用 `title` / `content` / `split` / `summary`
- [ ] 每个 `chart` 都同时有 `chartType` 与 `chartData`
- [ ] Chart.js 的 labels 与 datasets 长度一致
- [ ] Mermaid `code` 换行正确、语法可渲染
- [ ] 单段 ≤ 200 字、列表 ≤ 6 项、每项 ≤ 30 字
- [ ] 图表页占比 20%–40%，不是每页都有图

## 已知限制

- 本仓库不包含 Reveal.js 查看器，需要平台或自建渲染器消费 JSON。
- 模型可能输出非法 JSON（尾随逗号、未转义换行），建议在应用侧做解析重试。
- 业务数据缺失时，提示词允许生成合理模拟数据，正式汇报前需人工核对。

## 相关链接

- Reveal.js：<https://revealjs.com/>
- Chart.js：<https://www.chartjs.org/>
- Mermaid：<https://mermaid.js.org/>
- 在线应用：<https://transweb.cn/agent.html?id=ec6d0ef4ddfde4d983fdf934e57e29bb>

## 许可

本仓库暂未指定开源许可证。提示词文本与生成的演示文稿数据版权归各自作者所有；使用前请自行确认授权范围。
