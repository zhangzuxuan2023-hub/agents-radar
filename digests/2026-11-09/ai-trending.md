# AI 开源趋势日报 2026-11-09

> 数据来源: GitHub Trending + GitHub Search API | 生成时间: 2026-11-08 23:13 UTC

---

# AI 开源趋势报告 — 2026-11-09

## 1. 今日亮点

Agent 基础设施正在主导今日的生态：从 Vercel 的 `agent-browser` 和 Anthropic 的 `skills` 仓库，到一波 agent harness（ECC、hermes-agent、Codewhale），社区正在竞相构建围绕编码 agent 的工具层。一个引人注目的新信号是 **上下文/token 经济学** —— `billion-context`、`claude-mem`、`headroom` 以及爆火的 `caveman` 代理都瞄准同一个问题：让 agent 会话在长周期内保持低成本和持久性。Anthropic 在两个仓库（`claude-quickstarts`、`skills`）上表现强劲，强化了其推动 agent 技能格式标准化的努力。与此同时，Meta 的 `astryx` 标志着“agent 就绪”设计系统成为一个新兴品类，NVIDIA 的 `nvke` 则表明裸金属 GPU Kubernetes 已成为一等基础设施关注点。值得注意的是，Meta 的 `astryx`、Vercel 的 `agent-browser` 和 `caveman` token 代理均为首次亮相，指向大厂正更深入地进军 agent 工具领域。

## 2. 各类别热门项目

### 🔧 AI 基础设施

| 项目 | 语言 | Star（总计 / 今日） | 摘要 |
| :--- | :--- | ---: | :--- |
| [anthropics/claude-quickstarts](https://github.com/anthropics/claude-quickstarts) | Python | 0 (+28) | 使用 Claude API 构建可部署应用的官方入门项目。作为 Anthropic 面向新开发者的标准上手路径，值得关注。 |
| [vercel-labs/agent-browser](https://github.com/vercel-labs/agent-browser) | Rust | 0 (+812) | 专为 AI agent 打造的浏览器自动化 CLI。其 Rust 实现和单日 812 颗星表明市场对快速、可靠的 agent 浏览器基础设施需求强劲。 |
| [meta/astryx](https://github.com/meta/astryx) | TypeScript | 0 (+863) | 一个开源、完全可定制的设计系统，明确以“agent 就绪”为目标构建。Meta 进入面向 agent 的 UI 层是一个值得关注的新方向。 |
| [dsx-ai-factory/nvke](https://github.com/dsx-ai-factory/nvke) | Go | 0 (+345) | NVIDIA Kubernetes Engine，裸金属上 GPU Kubernetes 的管理平面。反映 GPU 集群管理正成熟为专用的开源工具。 |
| [google-deepmind/mujoco](https://github.com/google-deepmind/mujoco) | C++ | 0 (+26) | 用于带接触的多关节动力学的通用物理模拟器。机器人学和具身 AI 研究的基础设施。 |
| [Mirrowel/LLM-API-Key-Proxy](https://github.com/Mirrowel/LLM-API-Key-Proxy) | Python | 560 | 一个通用 LLM 网关，跨提供商提供统一的 OpenAI/Anthropic 兼容 API，并具备智能负载均衡。多模型部署的实用管道。 |
| [Picovoice/picollm](https://github.com/Picovoice/picollm) | Python | 319 | 由 X-bit 量化驱动的端侧 LLM 推理。将私有化本地推理进一步推向边缘。 |

### 🤖 AI Agent / 工作流

| 项目 | 语言 | Star（总计 / 今日） | 摘要 |
| :--- | :--- | ---: | :--- |
| [affaan-m/ECC](https://github.com/affaan-m/ECC) | JavaScript | 299,694 | 一个 agent harness 性能优化系统，涵盖技能、记忆、安全以及研究优先的开发方式，支持 Claude Code、Codex、Cursor 等。编码 agent 生态的标杆枢纽。 |
| [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent) | Python | 261,755 | NousResearch 出品的“与你共同成长的 agent”。开放 agent 领域的重要新入局者，拥有可观的社区热度。 |
| [browser-use/browser-use](https://github.com/browser-use/browser-use) | Python | 120,344 | 用于 AI 浏览器 agent 的开源 Python 库，另提供含隐身浏览器和托管 agent API 的云服务。最成熟的浏览器 agent 技术栈之一。 |
| [anthropics/skills](https://github.com/anthropics/skills) | Python | 0 (+793) | Anthropic 的 Agent Skills 公共仓库，标准化 agent 能力的打包方式。对任何基于 Claude 或可互操作 agent 框架构建的人都很重要。 |
| [zhayujie/CowAgent](https://github.com/zhayujie/CowAgent) | Python | 47,838 | 一个开源个人 AI 助手和 agent harness，具备规划、工具、自我进化、多 agent 和多渠道支持。轻量且可一行命令安装。 |
| [CopilotKit/CopilotKit](https://github.com/CopilotKit/CopilotKit) | TypeScript | 38,568 | 面向 agent 和生成式 UI 的前端技术栈（React、Angular、Slack），也是 AG-UI 协议的维护者。连接 agent 后端与用户界面。 |
| [career-ops-hq/career-ops](https://github.com/career-ops-hq/career-ops) | JavaScript | 81,189 | 一个开源 AI 求职 agent，可扫描招聘板、根据简历为职位打分并定制简历。垂直 agent 自动化的一个有力范例。 |
| [codewhale-hq/Codewhale](https://github.com/codewhale-hq/Codewhale) | Rust | 41,159 | 一个用 Rust 编写的开源终端编码 agent，可与任意提供商或模型配合使用。表明 Rust 在 agent 工具领域正获得更多阵地。 |

### 📦 AI 应用

| 项目 | 语言 | Star（总计 / 今日） | 摘要 |
| :--- | :--- | ---: | :--- |
| [milind-soni/OpenMausBot](https://github.com/milind-soni/OpenMausBot) | TypeScript | 0 (+1,437) | 一个开源的 Grok Bot 替代品，特色是提供一个可供 bot 使用的虚拟机。今日 1,437 颗星使其成为单日涨幅最大的项目之一。 |
| [hugohe3/ppt-master](https://github.com/hugohe3/ppt-master) | Python | 74,888 | AI 将文档或主题转换为真正的原生 PowerPoint 演示文稿，含形状、转场、图表和音频旁白。一个精致的垂直生成式应用。 |
| [harry0703/MoneyPrinterTurbo](https://github.com/harry0703/MoneyPrinterTurbo) | Python | 141,040 | 通过自动化 AI 工作流从主题或关键词生成高清短视频。面向消费者的生成式媒体工具中高流量的典范。 |
| [HKUDS/DeepTutor](https://github.com/HKUDS/DeepTutor) | Python | 56,958 | 一个终身个性化辅导系统。展示 LLM 正进入教育垂直领域。 |
| [ZhuLinsen/daily_stock_analysis](https://github.com/ZhuLinsen/daily_stock_analysis) | Python | 68,238 | 一个 LLM 驱动的多市场股票分析系统，含仪表盘和自动通知。金融垂直 agent 应用的代表。 |
| [TauricResearch/TradingAgents](https://github.com/TauricResearch/TradingAgents) | Python | 121,372 | 一个用于金融交易的多 agent LLM 框架。以将 agent 编排与量化领域结合而著称。 |

### 🧠 LLM / 训练

| 项目 | 语言 | Star（总计 / 今日） | 摘要 |
| :--- | :--- | ---: | :--- |
| [huggingface/transformers](https://github.com/huggingface/transformers) | Python | 168,666 | 用于最先进文本、视觉、音频和多模态模型的模型定义框架。仍是生态在推理与训练方面的中流砥柱。 |
| [ollama/ollama](https://github.com/ollama/ollama) | Go | 186,380 | 本地模型运行器，支持 Kimi、GLM、MiniMax、DeepSeek、gpt-oss、Qwen、Gemma 等。本地运行开放模型的默认入口。 |
| [rasbt/LLMs-from-scratch](https://github.com/rasbt/LLMs-from-scratch) | Jupyter Notebook | 108,030 | 在 PyTorch 中一步步构建类 ChatGPT 的 LLM。理解训练内部机制的首选教育资源。 |
| [skyzh/tiny-llm](https://github.com/skyzh/tiny-llm) | Python | 5,692 | 通过在 Apple Silicon 上构建一个微型 vLLM + Qwen 来学习 LLM 推理系统。对工程师而言宝贵的系统级学习路径。 |
| [open-compass/opencompass](https://github.com/open-compass/opencompass) | Python | 7,577 | 一个开放 LLM 评估平台，覆盖 OpenAI、Anthropic、Gemini、Qwen、GLM、DeepSeek 及 100+ 数据集。对模型对比和安全检查至关重要。 |
| [thinkwee/AgentsMeetRL](https://github.com/thinkwee/AgentsMeetRL) | HTML | 1,878 | 一个 agentic 强化学习的 awesome 列表。反映对基于 RL 的 agent 训练日益增长的兴趣。 |
| [0xPlaygrounds/rig](https://github.com/0xPlaygrounds/rig) | Rust | 9,095 | 一个用于构建模块化、可扩展 LLM 应用的 Rust 框架。Rust-for-AI 工具浪潮的一部分。 |

### 🔍 RAG / 知识

| 项目 | 语言 | Star（总计 / 今日） | 摘要 |
| :--- | :--- | ---: | :--- |
| [Shubhamsaboo/awesome-llm-apps](https://github.com/Shubhamsaboo/awesome-llm-apps) | Python | 144,212 | 100+ AI agent、agent 技能和 RAG 应用，免费且开源。供构建者评估模式的高信号合集。 |
| [Graphify-Labs/graphify](https://github.com/Graphify-Labs/graphify) | Python | 144,485 | 将任意代码库、文档、SQL schema、配置和 PDF 转化为可查询的知识图谱，采用确定性 AST 解析且无需向量存储。向量优先 RAG 的一个有趣反向趋势。 |
| [infiniflow/ragflow](https://github.com/infiniflow/ragflow) | Go | 93,779 | 一个 RAG 引擎，已发展为“agent 原生上下文基础设施”。采用最广泛的开源 RAG 平台之一。 |
| [unclecode/crawl4ai](https://github.com/unclecode/crawl4ai) | Python | 87,101 | 一个 Apache-2.0 网络爬虫，为 AI agent 提供托管的抓取/提取/搜索 API。RAG 的关键数据摄取层。 |
| [mem0ai/mem0](https://github.com/mem0ai/mem0) | Python | 68,643 | 一个即插即用的 AI agent 记忆层，在生产环境中提供持久上下文。新兴 agent 记忆品类的核心。 |
| [thedotmack/claude-mem](https://github.com/thedotmack/claude-mem) | TypeScript | 114,881 | 捕获并压缩 agent 会话数据，然后将相关上下文重新注入跨多种 agent 工具的未来会话中。一个具有重大热度的跨工具记忆方案。 |
| [headroomlabs-ai/headroom](https://github.com/headroomlabs-ai/headroom) | Python | 79,160 | 在工具输出、日志和 RAG 片段到达 LLM 之前进行压缩，削减 20-95% 的 token。直接解决上下文成本问题。 |
| [milvus-io/milvus](https://github.com/milvus-io/milvus) | Go | 46,609 | 一个高性能、云原生向量数据库，用于可扩展的 ANN 搜索。仍是 RAG 基础设施的核心组件。 |

## 3. 趋势信号分析

今日最爆炸性的关注集中在 **agent 周边工具**，而非模型本身。九个趋势榜仓库中有八个涉及 agent、技能或 agent 前端（agent-browser、skills、astryx、OpenMausBot、claude-quickstarts）。其中，一个独特的子趋势已成型：**上下文/token 经济学**。`billion-context`（今日 +460）、`claude-mem`、`headroom` 以及爆火的 `caveman` 代理都承诺节省 token 或长周期会话记忆 —— 这是对编码 agent 运行数月长会话所面临的成本和上下文窗口限制的明确回应。这表明社区将记忆和压缩视为一等基础设施，而非模型特性。

新技术栈也在涌现：**Rust** 在 agent 基础设施中反复出现（agent-browser、Codewhale、rig、qdrant），标志着向性能敏感的 agent 运行时转变。`meta/astryx` 将“agent 就绪”设计系统引入为一个品类，`dsx-ai-factory/nvke` 将 GPU Kubernetes 管理正式化 —— 这是 AI 基础设施向栈下方、朝硬件编排移动的迹象。Anthropic 的双重亮相（`claude-quickstarts`、`skills`）指向围绕 agent 技能格式的有意生态标准化，很可能与正在进行的 Claude/agent 发布相关。向量优先的 RAG 假设也正受到挑战：`graphify` 明确避开向量存储，转而采用确定性知识图谱，`PageIndex` 则主打“无向量、基于推理的 RAG”。

## 4. 社区热点

- **[vercel-labs/agent-browser](https://github.com/vercel-labs/agent-browser)** —— 用 Rust 实现的 agent 浏览器自动化；今日 +812 颗星。一个可能被众多 agent 项目依赖的基础原语。
- **[anthropics/skills](https://github.com/anthropics/skills)** —— 标准化 agent 技能可能塑造跨框架的互操作性。关注其在 Claude 之外的采用情况。
- **[headroomlabs-ai/headroom](https://github.com/headroomlabs-ai/headroom) 与 [thedotmack/claude-mem](https://github.com/thedotmack/claude-mem)** —— 上下文压缩和持久记忆是今日最清晰的痛点；对任何长期运行的 agent 都值得评估。
- **[meta/astryx](https://github.com/meta/astryx)** —— 来自 Meta 的“agent 就绪”设计系统；设计工具与 agent 的新交汇点。
- **[Graphify-Labs/graphify](https://github.com/Graphify-Labs/graphify)** —— 无向量、确定性知识图谱作为 RAG 架构的替代方案；检索系统的方向性信号。

---
*本日报由 [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar) 自动生成。*