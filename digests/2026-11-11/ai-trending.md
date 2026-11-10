# AI 开源趋势日报 2026-11-11

> 数据来源: GitHub Trending + GitHub Search API | 生成时间: 2026-11-10 23:14 UTC

---

# AI 开源趋势报告 — 2026-11-11

## 1. 今日亮点

今日 GitHub AI 动态由 **Agent 技能与运行框架生态** 主导：Anthropic 的 `skills`（+1,676）和 `claude-quickstarts`（+530），加上 Addy Osmani（+465）和 Matt Pocock（+1,223）的第三方技能库，表明“Agent Skills”正凝聚为一种共享的开放格式。**Agent 基础设施** 层同样火热——Vercel 的 `agent-browser`（+775）和 Home Assistant MCP 桥接（+792）显示 Agent 正延伸至浏览器和智能家居。与此同时，`meta/astryx`（今日 +3,111）以将设计系统定位为“Agent 就绪”而位居所有新项目之首，`billion-context`（+374）则瞄准长会话上下文窗口的实际痛点。长尾话题数据进一步印证了 **记忆/上下文压缩热潮**（`claude-mem`、`headroom`、`billion-context`、`cognee`），Rust 在推理和 Agent 工具领域持续扩大版图。

---

## 2. 分类热门项目

### 🔧 AI 基础设施

| 项目 | 语言 | Star（总数 / 今日） | 简介 |
| :--- | :--- | ---: | :--- |
| [vercel-labs/agent-browser](https://github.com/vercel-labs/agent-browser) | Rust | 0 (+775) | 专为 AI Agent 打造的浏览器自动化 CLI，使用 Rust 编写。其登上趋势榜表明 Agent 工具层正被以系统级语言重写，以追求速度与可靠性。 |
| [google-deepmind/mujoco](https://github.com/google-deepmind/mujoco) | C++ | 0 (+453) | DeepMind 的通用物理模拟器，用于带接触的多关节动力学仿真。持续上榜反映具身 AI 与机器人仿真的持久动能。 |
| [anthropics/claude-quickstarts](https://github.com/anthropics/claude-quickstarts) | Python | 0 (+530) | 使用 Claude API 构建可部署应用的入门项目。是开发者进入 Anthropic Agent 技术栈的最快上手路径。 |
| [0xPlaygrounds/rig](https://github.com/0xPlaygrounds/rig) | Rust | 9,111 | 模块化、可扩展的 Rust LLM 应用构建框架。它是 Rust 正成为一流 LLM 应用语言的重要信号。 |
| [skyzh/tiny-llm](https://github.com/skyzh/tiny-llm) | Python | 5,711 | 从零实现的“tiny vLLM + Qwen”推理系统，面向 Apple Silicon 上的系统工程师。它为日益增长的受众揭开了推理栈的神秘面纱。 |
| [Picovoice/picollm](https://github.com/Picovoice/picollm) | Python | 319 | 基于 X-bit 量化的端侧 LLM 推理。代表了向本地化、边缘友好型模型部署的推进。 |
| [samchon/nestia](https://github.com/samchon/nestia) | TypeScript | 2,181 | NestJS 辅助工具及 AI 聊天机器人开发脚手架。将主流 Web 后端框架与 Agent 功能相桥接。 |
| [codewhale-hq/Codewhale](https://github.com/codewhale-hq/Codewhale) | Rust | 41,173 | 使用 Rust 编写的开源终端 Agent，支持任意提供商或模型。是值得关注的 Rust 原生编码 Agent 竞争者。 |

### 🤖 AI Agent / 工作流

| 项目 | 语言 | Star（总数 / 今日） | 简介 |
| :--- | :--- | ---: | :--- |
| [anthropics/skills](https://github.com/anthropics/skills) | Python | 0 (+1,676) | Agent Skills 的公开仓库——为 Agent 提供可复用、可组合的能力。其热度表明“技能”正成为一种可移植、标准化的 Agent 原语。 |
| [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills) | JavaScript | 0 (+465) | 来自知名实践者的生产级 AI 编码 Agent 工程技能。强化了社区驱动的技能库趋势。 |
| [mattpocock/skills](https://github.com/mattpocock/skills) | Shell | 0 (+1,223) | “面向真正工程师的技能”，直接发布自作者的 `.agents` 目录。其快速攀升表明技能正像 dotfiles 一样被分享。 |
| [homeassistant-ai/ha-mcp](https://github.com/homeassistant-ai/ha-mcp) | Python | 0 (+792) | 通过 MCP 用 Claude、ChatGPT 等助手配置和自动化 Home Assistant。是 Agent 延伸至物理智能家居控制的典型范例。 |
| [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent) | Python | 262,434 | “与你共同成长的 Agent。”庞大的 Star 基数使其成为生态中关注度最高的 Agent 项目之一。 |
| [CopilotKit/CopilotKit](https://github.com/CopilotKit/CopilotKit) | TypeScript | 38,612 | Agent 与生成式 UI 的前端技术栈，也是 AG-UI 协议的创造者。它正在定义 Agent 如何呈现在真实产品 UI 中。 |
| [HKUDS/nanobot](https://github.com/HKUDS/nanobot) | Python | 49,996 | 轻量级开源个人 Agent。代表了“小型、本地个人 Agent”浪潮。 |
| [zhayujie/CowAgent](https://github.com/zhayujie/CowAgent) | Python | 47,871 | 个人 AI 助手与 Agent 运行框架，支持多 Agent、多模型和记忆。一行安装降低了自托管 Agent 的门槛。 |

### 📦 AI 应用

| 项目 | 语言 | Star（总数 / 今日） | 简介 |
| :--- | :--- | ---: | :--- |
| [meta/astryx](https://github.com/meta/astryx) | TypeScript | 0 (+3,111) | Meta 完全可定制的“Agent 就绪”开源设计系统。它以首日爆发式 Star 领跑今日全部趋势榜，暗示一个全新的前端品类：为 Agent 设计的 UI 系统。 |
| [ranxianglei/billion-context](https://github.com/ranxianglei/billion-context) | TypeScript | 0 (+374) | 面向整月会话、小窗口及约 5 倍 token 节省的上下文压缩插件。直接解决长时间运行 Agent 会话的成本/上下文痛点。 |
| [hugohe3/ppt-master](https://github.com/hugohe3/ppt-master) | Python | 75,632 | AI 生成原生 PowerPoint 演示文稿，包含真实形状、转场、图表和旁白。是高关注度的垂直 AI 生产力应用。 |
| [career-ops-hq/career-ops](https://github.com/career-ops-hq/career-ops) | JavaScript | 81,734 | 开源 AI 求职 Agent，根据你的简历为职位打分并定制申请。显示 AI Agent 正进入高频个人工作流。 |
| [ZhuLinsen/daily_stock_analysis](https://github.com/ZhuLinsen/daily_stock_analysis) | Python | 69,545 | LLM 驱动的多市场股票分析系统，带仪表盘和告警。是强有力的金融垂直 Agent 应用。 |
| [harry0703/MoneyPrinterTurbo](https://github.com/harry0703/MoneyPrinterTurbo) | Python | 141,970 | 通过自动化 AI 工作流从主题生成高清短视频。持续是最受 Star 的消费级 AI 应用之一。 |
| [HKUDS/DeepTutor](https://github.com/HKUDS/DeepTutor) | Python | 57,973 | Agent 驱动的终身个性化辅导。代表教育垂直领域日益成熟的 AI 产品。 |

### 🧠 LLM / 训练

| 项目 | 语言 | Star（总数 / 今日） | 简介 |
| :--- | :--- | ---: | :--- |
| [huggingface/transformers](https://github.com/huggingface/transformers) | Python | 168,753 | 文本、视觉、音频和多模态模型事实上的模型定义框架。仍是开放模型生态的支柱。 |
| [rasbt/LLMs-from-scratch](https://github.com/rasbt/LLMs-from-scratch) | Jupyter Notebook | 108,196 | 用 PyTorch 一步步构建类 ChatGPT 的 LLM。常青热门项目，反映学习 LLM 内部原理的持久需求。 |
| [open-compass/opencompass](https://github.com/open-compass/opencompass) | Python | 7,583 | 覆盖主要提供商、100+ 数据集的 LLM 评测平台。随着模型多样性爆发，评测日益关键。 |
| [ollama/ollama](https://github.com/ollama/ollama) | Go | 186,572 | 本地运行 Kimi、GLM、MiniMax、DeepSeek、gpt-oss、Qwen 等模型。是通往开放权重模型的默认入口。 |
| [AkaliKong/MiniOneRec](https://github.com/AkaliKong/MiniOneRec) | Python | 1,964 | OneRec 推荐模型的最小复现。表明对基于 LLM 的推荐系统的兴趣日增。 |
| [llm-jp/awesome-japanese-llm](https://github.com/llm-jp/awesome-japanese-llm) | TypeScript | 1,446 | 日本 LLM 概览。反映开放模型持续的区域化趋势。 |

### 🔍 RAG / 知识

| 项目 | 语言 | Star（总数 / 今日） | 简介 |
| :--- | :--- | ---: | :--- |
| [thedotmack/claude-mem](https://github.com/thedotmack/claude-mem) | TypeScript | 115,798 | 为每个 Agent 提供跨会话持久上下文，压缩会话活动并重新注入相关记忆。是 Agent 记忆浪潮的旗舰项目。 |
| [Graphify-Labs/graphify](https://github.com/Graphify-Labs/graphify) | Python | 145,625 | 无需向量库即可将任意代码库和文档转化为可查询知识图谱。其“图优于向量”的方法是一种值得注意的 RAG 替代方案。 |
| [headroomlabs-ai/headroom](https://github.com/headroomlabs-ai/headroom) | Python | 79,314 | 在工具输出、日志和 RAG 片段进入 LLM 之前进行压缩。token 效率工具显然需求旺盛。 |
| [mem0ai/mem0](https://github.com/mem0ai/mem0) | Python | 68,768 | 面向生产级 AI Agent 的即插即用记忆层。是最受 Star 的记忆基础设施项目之一。 |
| [infiniflow/ragflow](https://github.com/infiniflow/ragflow) | Go | 93,969 | 从 RAG 成长为上下文基础设施的 Agent 原生数据库。显示 RAG 引擎正演进为 Agent 上下文平台。 |
| [milvus-io/milvus](https://github.com/milvus-io/milvus) | Go | 46,632 | 高性能、云原生向量数据库，用于可扩展的 ANN 搜索。是检索栈的中坚力量。 |
| [qdrant/qdrant](https://github.com/qdrant/qdrant) | Rust | 35,502 | 高性能、大规模向量数据库与搜索引擎。基于 Rust 的向量搜索持续受到青睐。 |
| [VectifyAI/PageIndex](https://github.com/VectifyAI/PageIndex) | Python | 40,350 | 面向“无向量”、基于推理的 RAG 的文档索引。体现了新兴的非向量检索方向。 |

---

## 3. 趋势信号分析

今日最明显的爆发围绕 **Agent 技能与 Agent 运行框架**。Anthropic 的 `skills` 仓库加上两个独立的实践者技能库（Addy Osmani、Matt Pocock）同时登上趋势榜——这是“Agent Skills”正成为事实上的、可分享的能力单元的强烈信号，就像几年前的 prompts 一样。`ECC`（Star 数 300,778）以及更广泛的运行框架/优化系统集群进一步印证了这一点，表明社区现在优化的不仅是调用哪个模型，而是 *Agent 如何运行*。

第二个主导主题是 **记忆与上下文效率**。`claude-mem`、`headroom`、`billion-context`、`mem0` 和 `cognee` 共同代表了一个新的“上下文压缩 / 持久记忆”子层。随着 Agent 运行更长的会话，token 节省（5 倍更少 token，JSON 上 60–95%）和跨会话召回已成为具体的产品需求，而非研究构想。

**新技术栈/方向：** Rust 持续出现在各类基础设施中（agent-browser、Codewhale、qdrant、zvec、lancedb），表明性能敏感的 AI 工具正迁移至系统级语言。“无向量”检索（`PageIndex`、`graphify`）是一个真正新颖的方向，挑战着默认的向量数据库假设。Agent 也通过 Home Assistant MCP 延伸至物理世界，并通过 `astryx` 的“Agent 就绪”设计系统和 CopilotKit 的 AG-UI 协议延伸至前端 UI。

**与近期 LLM 发布的关联：** `ollama` 的列表（Kimi、GLM、MiniMax、DeepSeek、gpt-oss、Qwen）反映了一个拥挤的多提供商开放权重格局，推动了对评测（`opencompass`）、本地推理（`picollm`）以及跨提供商可移植技能的需求。整体图景是：生态正从 *模型访问* 转向 *Agent 运行时、记忆与互操作性*。

---

## 4. 社区热点

- **Agent Skills 标准化** — [anthropics/skills](https://github.com/anthropics/skills)、[addyosmani/agent-skills](https://github.com/addyosmani/agent-skills)、[mattpocock/skills](https://github.com/mattpocock/skills)：三个独立技能库同时上榜，暗示一种新兴的可移植技能格式，值得尽早基于其构建。
- **Agent 记忆与上下文压缩** — [thedotmack/claude-mem](https://github.com/thedotmack/claude-mem)、[headroomlabs-ai/headroom](https://github.com/headroomlabs-ai/headroom)、[mem0ai/mem0](https://github.com/mem0ai/mem0)：生产级 Agent 最具商业紧迫性的痛点，有明确的 token/成本节省声明待验证。
- **Agent 的浏览器与设备控制** — [vercel-labs/agent-browser](https://github.com/vercel-labs/agent-browser)、[browser-use/browser-use](https://github.com/browser-use/browser-use)、[homeassistant-ai/ha-mcp](https://github.com/homeassistant-ai/ha-mcp)：Agent 正从聊天走向真实工具、浏览器和智能家居。
- **Rust 原生 AI 基础设施** — [0xPlaygrounds/rig](https://github.com/0xPlaygrounds/rig)、[codewhale-hq/Codewhale](https://github.com/codewhale-hq/Codewhale)、[qdrant/qdrant](https://github.com/qdrant/qdrant)：性能关键的 LLM 和向量工作负载的一致技术栈信号。
- **无向量 / 基于图的检索** — [Graphify-Labs/graphify](https://github.com/Graphify-Labs/graphify)、[VectifyAI/PageIndex](https://github.com/VectifyAI/PageIndex)：对 RAG 架构中向量库默认方案的早期但值得注意的挑战。

---
*本日报由 [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar) 自动生成。*