# LeaferJS Presentation

**可独立运行的演示文稿 JSON 生成器**

> 把 PPT 大纲、文章、报告、方案或一句主题描述，转换成 **可直接被 Reveal.js 渲染** 的结构化 JSON。
> 自带任意大模型即可运行；数据与渲染分离，不绑定任何平台。

![BYOM](https://img.shields.io/badge/model-any%20LLM-6f5cff)
![Reveal.js](https://img.shields.io/badge/target-Reveal.js-6f5cff)
![Chart.js](https://img.shields.io/badge/chart-Chart.js-2ea44f)
![Mermaid](https://img.shields.io/badge/diagram-Mermaid-2ea44f)

---

## 简介

**LeaferJS Presentation 是一个可独立运行的 AI 工具**，不是某个平台的插件，也不需要登录任何账号。

它的全部能力来自一份系统提示词 [`appPrompt.md`](./appPrompt.md)：把这份提示词交给任意支持长输出的 LLM，模型就会把素材变成一份结构化的幻灯片数据（`title` / `presenter` / `slides`），并以严格 JSON 交付。数据可直接被 Reveal.js 或任意前端查看器消费。

```text
大纲 / 文章 / 主题 ──► 任意 LLM ──► slides.json ──► Reveal.js / 自建查看器
                            ▲
                      appPrompt.md
```

与其他三个 LeaferJS 工具不同，这里**不生成 HTML 代码**，只生成渲染无关的数据层。这带来两个好处：同一份内容可以换任何主题、任何渲染器；模型输出更短、更稳定、更容易校验。

## 为什么它可以独立运行

| 维度 | 说明 |
| --- | --- |
| **模型自由** | OpenAI、DeepSeek、Claude、Gemini、Ollama、vLLM 均可；只要支持 system prompt 与长输出 |
| **无平台锁定** | 不调用 transweb.cn 或任何私有接口；平台页面只是同款提示词的参考实现 |
| **数据与渲染分离** | 输出是标准 JSON，可接 Reveal.js、自建组件或任意前端框架 |
| **本地执行** | 提示词、调用脚本、产物都在你的机器上；使用本地模型时可完全离线生成 |
| **结构可校验** | 页型、元素、图表字段都有固定协议，适合做 schema 校验与自动化流水线 |
| **过程可审计** | 提示词、输出协议和生成结果全部可见，可 diff、可版本化 |

### 独立运行的边界

- **生成层完全独立**：提示词 + LLM + 本地运行器即可产出 JSON。
- **渲染层需要消费者**：JSON 不会自己变成幻灯片，需要 Reveal.js 查看器或自建前端消费；本仓库只负责数据层。
- 这不是平台依赖，而是「数据 / 视图」的刻意分离；换一个查看器，内容不用重新生成。

## 它生成什么

一份可直接渲染的演示文稿数据：

- **四种页型**：`title` 封面、`content` 内容页、`split` 图文混排、`summary` 总结
- **三种元素**：`text` 段落、`list` 列表、`chart` 图表
- **Chart.js 图表**：折线、柱状、饼图、雷达、散点，`chartData` 直接对应 Chart.js 配置
- **Mermaid 图表**：Flowchart、Sequence、Mindmap、Timeline、Architecture
- **自动选型**：趋势/对比/占比用 Chart.js；流程/架构/时间线用 Mermaid
- **内容压缩**：单段 ≤ 200 字、列表 ≤ 6 项、每项 ≤ 30 字
- **图表配比**：图表页建议占 20%–40%，不每页都放图
- **唯一 ID**：每个 slide 的 `id` 唯一，便于锚点跳转与增量更新

## 快速开始（独立运行）

### 前置条件

- Node.js 18+（内置 `fetch`，无需 `npm install`）
- 一个 OpenAI 兼容的模型端点；建议单次输出上限 **≥ 8K tokens**

### 1. 获取工具

```bash
git clone https://github.com/liuwenjin/LeaferJS-presentation.git
cd LeaferJS-presentation
```

### 2. 创建本地运行器

把下面的代码保存为 `run.mjs`（放在仓库根目录）：

> 不想用 Node.js？任何 HTTP 客户端都可以：把 `appPrompt.md` 作为 system message、需求作为 user message 发送，再解析返回的 JSON。

```js
// run.mjs — Node.js 18+，零依赖
import fs from 'node:fs';

const system = fs.readFileSync('appPrompt.md', 'utf8');
const idea = process.argv.slice(2).join(' ') || '做一个关于「AI 应用工厂」的 8 页汇报，包含 2024–2026 产品路线图和 Agent 工作流。';
const base = (process.env.LLM_BASE_URL || 'https://api.deepseek.com').replace(/\/$/, '');

const res = await fetch(`${base}/chat/completions`, {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    Authorization: `Bearer ${process.env.LLM_API_KEY}`
  },
  body: JSON.stringify({
    model: process.env.LLM_MODEL || 'deepseek-chat',
    messages: [
      { role: 'system', content: system },
      { role: 'user', content: idea }
    ],
    max_tokens: Number(process.env.LLM_MAX_TOKENS || 8000),
    temperature: 0.7
  })
});

if (!res.ok) throw new Error(`HTTP ${res.status}: ${await res.text()}`);
const body = await res.json();
if (body.choices?.[0]?.finish_reason === 'length') {
  throw new Error('输出被截断：请提高 LLM_MAX_TOKENS，或换用输出上限更大的模型');
}

const raw = body.choices[0].message.content.trim()
  .replace(/^`{3}(?:json)?\s*/, '')
  .replace(/\s*`{3}$/, '');
const deck = JSON.parse(raw);
fs.writeFileSync('deck.json', JSON.stringify(deck, null, 2), 'utf8');
console.log(`✓ ${deck.title} → deck.json（${deck.slides.length} 页）`);
```

> 提示：提示词允许模型在无法生成时返回 `{}`。运行器可再加一条校验：`if (!deck.slides) throw new Error('模型返回空结果，请重试或换模型');`

### 3. 生成并查看

```bash
LLM_API_KEY=sk-... node run.mjs "做一个关于「AI 应用工厂」的 8 页汇报，包含团队 2024–2026 年的产品路线图和 Agent 工作流"
```

拿到 `deck.json` 后，可以直接接入你在用的 Reveal.js 查看器，或按下一节的映射自己渲染。

### 4. 换一个模型

| 服务 | `LLM_BASE_URL` | `LLM_MODEL` 示例 |
| --- | --- | --- |
| OpenAI | `https://api.openai.com/v1` | `gpt-4.1` |
| DeepSeek | `https://api.deepseek.com` | `deepseek-chat` |
| Ollama（本地） | `http://localhost:11434/v1` | `qwen2.5:14b` |
| vLLM（本地） | `http://localhost:8000/v1` | 你部署的模型名 |
| 其他网关 | 任意 OpenAI 兼容地址 | 以服务商文档为准 |

> Claude、Gemini 的原生 API 协议不同，可通过 OpenAI 兼容网关接入，或自行替换运行器中的请求部分。模型不是工具的一部分，可随时替换。

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

该 JSON 是**渲染无关的中间格式**。用 Reveal.js 消费时，按以下约定映射：

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

最小消费示例：

```js
import fs from 'node:fs';

const deck = JSON.parse(fs.readFileSync('deck.json', 'utf8'));
for (const slide of deck.slides) {
  // slide.type: title | content | split | summary  → 决定 section 布局
  // slide.elements: text | list | chart             → 决定内部渲染
  console.log(slide.id, slide.type, slide.elements.length);
}
```

Reveal.js 负责分页、转场、键盘导航与全屏；Chart.js 与 Mermaid 负责图表渲染。你也可以换成自建查看器、Vue/React 组件或服务端出图，数据不需要重新生成。

## 技术栈

| 层 | 选型 |
| --- | --- |
| 生成引擎 | `appPrompt.md`（系统提示词）+ 任意 LLM |
| 运行器 | 约 40 行 Node.js 脚本，仅用内置 `fetch` |
| 输出 | `deck.json`（渲染无关的结构化数据） |
| 目标渲染器 | Reveal.js + Chart.js + Mermaid |
| 产物 | 标准 JSON，可 diff、可校验、可进版本库 |

本工具**不包含** Reveal.js 查看器本体，这是数据层工具的刻意边界，不是平台依赖。

## 目录结构

```text
LeaferJS-presentation/
├── appPrompt.md     # 工具引擎：完整系统提示词（必需）
├── README.md        # 本文件
├── appCode.vue      # 可选参考实现：平台端 UI（独立运行不需要）
├── appPage.html     # 可选参考实现：iframe 嵌入页
└── manifest.json    # 元数据
```

`run.mjs` 不在仓库内，按「快速开始」创建即可；`appCode.vue` 与 `appPage.html` 只是同款提示词在 transweb.cn 上的 Web 外壳，**不参与**独立运行链路，删掉它们不影响工具使用。

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

## 常见问题

**为什么不直接生成 PPTX？**
JSON 更轻、更可控，也更容易换主题和渲染器。需要 PPTX 时，可以在 JSON 之上再加一层转换器，而不必重新生成内容。

**必须联网吗？**
生成阶段需要访问模型；查看阶段取决于你的渲染器。本地模型 + 本地查看器可完全离线。

**输出被截断怎么办？**
优先提高 `LLM_MAX_TOKENS`。长文稿可拆成多次生成再合并 `slides`，但要注意 `id` 唯一。

**可以商用吗？**
本仓库暂未声明许可证。提示词与生成数据的版权归各自作者所有，商用前请自行确认授权范围。

## 参考实现（可选）

- 在线效果参考：<https://transweb.cn/agent.html?id=ec6d0ef4ddfde4d983fdf934e57e29bb>
- `appPage.html` 通过 iframe 嵌入同一在线版本；它只用于预览，不是运行本工具的必要条件。

## 相关链接

- Reveal.js：<https://revealjs.com/>
- Chart.js：<https://www.chartjs.org/>
- Mermaid：<https://mermaid.js.org/>
- 本仓库：<https://github.com/liuwenjin/LeaferJS-presentation>

## 许可

本仓库暂未指定开源许可证。提示词文本与生成的演示文稿数据版权归各自作者所有；使用前请自行确认授权范围。
