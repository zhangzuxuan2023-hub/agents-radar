# AI 开源趋势日报 2026-11-05

> 数据来源: GitHub Trending + GitHub Search API | 生成时间: 2026-11-04 23:22 UTC

---

# AI 开源趋势报告 — 2026-11-05

## 1. 今日亮点

今日的 GitHub 版图由**智能体工具层**主导：持久记忆（`claude-mem`）、计算机使用 CLI（`agent-desktop`，今日 +2,463）、本体驱动的推理（`NVIDIA/auto-ontology`，+2,053）同时飙升——这清晰表明，社区正在竞相为智能体赋予*上下文、双手与结构*，而非新的基础模型。原始数据中单日增幅最大的是 `vercel-labs/agent-desktop`，暗示智能体与计算机的交互正从研究演示跨越为开发者基础设施。与此同时，RAG 正明显演变为“上下文工程”：基于图的代码库索引（`Graphify-Labs/graphify`）、工具输出压缩（`headroomlabs-ai/headroom`），以及自进化上下文数据库（`volcengine/OpenViking`）均位居 ⭐ 排行榜前列。一个颇具趣味的系统工程亮点——`kimi-k3-in-c` 声称在单颗 CPU、8.24 GB 内存上实现 2.78 万亿参数推理——释放出对极端量化与可移植推理重燃的兴趣。总体而言，今日的主题是**为智能体构建的基础设施**，而非模型本身。

---

## 2. 按类别划分的热门项目

### 🔧 AI 基础设施

| 项目 | 语言 | Star 数（总数 / 今日） | 摘要 |
| :--- | :--- | ---: | :--- |
| [vercel-labs/agent-desktop](https://github.com/vercel-labs/agent-desktop) | Rust | 0 (+2,463) | 一个计算机使用 CLI，让 AI 智能体能够驱动桌面环境。它创下今日单日最大 Star 增幅，强烈表明“智能体 + 计算机使用”正成为一等公民的开发者原语。 |
| [NVIDIA/auto-ontology](https://github.com/NVIDIA/auto-ontology) | Python | 0 (+2,053) | 一个 Python 框架，用于在数据之上构建语义化、基于图的推理层，实现确定性查询生成与可解释的 LLM/智能体工作流。NVIDIA 以 +2,053 的首秀进军智能体上下文领域，使其成为一个值得关注的厂商背书信号。 |
| [max-sixty/worktrunk](https://github.com/max-sixty/worktrunk) | Rust | 0 (+524) | 一个专为并行 AI 智能体工作流打造的 Git worktree 管理 CLI。它反映了在隔离分支上同时运行多个智能体的新兴需求。 |
| [mattpocock/skills](https://github.com/mattpocock/skills) | Shell | 0 (+1,376) | 一套从作者 `.agents` 目录中提炼出的“面向真正工程师的技能”精选集。其迅速蹿红凸显了“智能体技能”正成为一种可共享的资产格式。 |
| [garrytan/gstack](https://github.com/garrytan/gstack) | TypeScript | 0 (+285) | 对 Garry Tan 颇具个人风格的 Claude Code 配置的复刻，包含 23 个分别担任 CEO、设计师、工程经理、发布经理和 QA 的工具。它是“智能体组织架构图”作为可复用配置的高知名度范例。 |
| [FareedKhan-dev/kimi-k3-in-c](https://github.com/FareedKhan-dev/kimi-k3-in-c) | C | 0 (+351) | 声称在单颗 CPU、8.24 GB 内存上实现 2.78 万亿参数 Kimi K3 的可移植 C99 推理——无 BLAS、无框架、无 GPU。无论这一声明能否经受推敲，它都凸显了对极端量化和无框架推理的需求。 |
| [scenario-labs/skills](https://github.com/scenario-labs/skills) | Python | 0 (+224) | 面向任意 AI 智能体的生产级图像、视频、音频和 3D 生成技能，具备成本感知的模型选择，并通过 Scenario MCP 实现品牌/角色一致性。它展现了媒体生成正被封装为可被智能体调用的技能。 |

### 🤖 AI 智能体 / 工作流

| 项目 | 语言 | Star 数（总数 / 今日） | 摘要 |
| :--- | :--- | ---: | :--- |
| [thedotmack/claude-mem](https://github.com/thedotmack/claude-mem) | TypeScript | 112,805 (+1,165) | 捕捉智能体在会话期间的一切操作，AI 压缩后注入未来会话的相关上下文，支持 Claude Code、Codex、Gemini、Copilot 等。它是“持久化智能体记忆”这一品类走向主流的最清晰表达。 |
| [affaan-m/ECC](https://github.com/affaan-m/ECC) | JavaScript | 297,226 | 一个智能体 harness 性能优化系统，涵盖技能、本能、记忆、安全以及面向 Claude Code、Codex、Cursor 等的调研优先开发。其庞大的 Star 数使其成为 harness 工程事实上的参照点。 |
| [firecrawl/firecrawl](https://github.com/firecrawl/firecrawl) | TypeScript | 198,643 | 一个面向 AI 智能体的网页数据库，将开放网络转化为智能体可用的结构化输入。它仍是拥有巨大社区引力的“智能体数据层”标杆项目。 |
| [langchain-ai/langgraph](https://github.com/langchain-ai/langgraph) | Python | 44,472 | 一个以图结构构建弹性、有状态智能体的框架。它锚定了生产级智能体编排细分领域。 |
| [CopilotKit/CopilotKit](https://github.com/CopilotKit/CopilotKit) | TypeScript | 38,518 | 面向智能体和生成式 UI 的前端技术栈，覆盖 React、Angular、移动端和 Slack，并是 AG-UI 协议的守护者。它标志着智能体 UX 正成为独立且协议化的一层。 |
| [esengine/DeepSeek-Reasonix](https://github.com/esengine/DeepSeek-Reasonix) | Go | 36,079 | 一个面向复杂软件工程任务的可靠编码智能体。基于 Go 的编码智能体是一种值得关注的新兴技术栈模式。 |
| [codewhale-hq/Codewhale](https://github.com/codewhale-hq/Codewhale) | Rust | 41,157 | 一个用 Rust 编写的开源、供应商无关的终端编码智能体。它契合了高性能 Rust CLI 取代 Python 原型的大趋势。 |
| [The-PR-Agent/pr-agent](https://github.com/The-PR-Agent/pr-agent) | Python | 0 (+279) | 最初的开源 PR 审阅智能体，明确声明并非 Qodo 免费版。它表明智能体化的代码审查仍是一个持久的细分领域。 |

### 📦 AI 应用

| 项目 | 语言 | Star 数（总数 / 今日） | 摘要 |
| :--- | :--- | ---: | :--- |
| [harry0703/MoneyPrinterTurbo](https://github.com/harry0703/MoneyPrinterTurbo) | Python | 138,913 (+615) | 通过自动化 AI 工作流，根据主题或关键词生成高清短视频。它持续展现出消费者对一键式生成式视频流水线的强劲需求。 |
| [TauricResearch/TradingAgents](https://github.com/TauricResearch/TradingAgents) | Python | 120,631 | 一个面向金融交易的多智能体 LLM 框架。它代表了“面向垂直领域的智能体团队”模式的成熟。 |
| [career-ops-hq/career-ops](https://github.com/career-ops-hq/career-ops) | JavaScript | 80,184 | 一个开源 AI 求职智能体，依据你的简历为职位打分、定制简历，并在你点击 Submit 时追踪申请进度。此类垂直智能体应用正获得可观的关注度。 |
| [hugohe3/ppt-master](https://github.com/hugohe3/ppt-master) | Python | 73,495 | 将文档或主题转化为原生 PowerPoint 演示文稿，包含真实形状、切换、图表和旁白。原生格式生成（而非图片幻灯片）是其差异化优势。 |
| [HKUDS/Vibe-Trading](https://github.com/HKUDS/Vibe-Trading) | Python | 37,652 | 来自 HKUDS 的个人交易智能体。它是若干聚焦金融的智能体之一，展现出垂直领域的整合趋势。 |
| [HKUDS/DeepTutor](https://github.com/HKUDS/DeepTutor) | Python | 55,040 | 由 LLM/RAG 技术驱动的终身个性化辅导。它凸显了教育作为智能体应用新兴垂直领域的地位。 |
| [Thysrael/Horizon](https://github.com/Thysrael/Horizon) | Python | 0 (+121) | 一个 AI 驱动的个人新闻雷达，以中英文生成每日简报。它是个人监控智能体细分领域中一个小而有代表性的例子。 |

### 🧠 LLM / 训练

| 项目 | 语言 | Star 数（总数 / 今日） | 摘要 |
| :--- | :--- | ---: | :--- |
| [huggingface/transformers](https://github.com/huggingface/transformers) | Python | 168,547 | 文本、视觉、音频和多模态模型事实上的模型定义框架。它仍是生态系统中稳定的重心。 |
| [ollama/ollama](https://github.com/ollama/ollama) | Go | 185,933 | 面向 Kimi、GLM、MiniMax、DeepSeek、gpt-oss、Qwen、Gemma 等的本地推理。它是长尾开源模型默认的本地运行时网关。 |
| [rasbt/LLMs-from-scratch](https://github.com/rasbt/LLMs-from-scratch) | Jupyter Notebook | 107,822 | 逐步用 PyTorch 实现类 ChatGPT 的 LLM。它仍是最受推荐的 LLM 内部原理学习路径。 |
| [skyzh/tiny-llm](https://github.com/skyzh/tiny-llm) | Python | 5,554 | 通过在 Apple Silicon 上构建一个微型 vLLM + Qwen 来教授 LLM 推理系统。边缘/Apple Silicon 推理教育是一个新兴且不断增长的细分领域。 |
| [0xPlaygrounds/rig](https://github.com/0xPlaygrounds/rig) | Rust | 9,064 | 一个用于构建模块化、可扩展 LLM 应用的 Rust 库。Rust 正日益成为 LLM 基础设施的首选语言。 |
| [Picovoice/picollm](https://github.com/Picovoice/picollm) | Python | 318 | 由 X-bit 量化驱动的端侧 LLM 推理。它契合了向私有化、本地推理的更广泛转变。 |
| [open-compass/opencompass](https://github.com/open-compass/opencompass) | Python | 7,564 | 一个 LLM 评测平台，覆盖知识、推理、编码和安全领域的 100 多个数据集。评测工具仍是必不可少但被低估的一层。 |

### 🔍 RAG / 知识

| 项目 | 语言 | Star 数（总数 / 今日） | 摘要 |
| :--- | :--- | ---: | :--- |
| [Graphify-Labs/graphify](https://github.com/Graphify-Labs/graphify) | Python | 142,420 | 通过确定性的 AST 解析，将代码库、文档、SQL schema、配置和 PDF 转化为可查询的知识图谱——无需向量存储。它是领先的“无向量 RAG”竞争者，也是反对纯嵌入流水线的强烈信号。 |
| [Mintplex-Labs/anything-llm](https://github.com/Mintplex-Labs/anything-llm) | JavaScript | 67,943 | 一个本地优先、自托管的智能体体验，内置 RAG 和文档处理。“拥有你自己的智能”这一定位引起了注重隐私的团队的共鸣。 |
| [unclecode/crawl4ai](https://github.com/unclecode/crawl4ai) | Python | 86,905 | 一个 Apache-2.0 爬虫，并提供官方托管云服务，用于智能体可用的网页提取与搜索。它展现了开放核心模式在 RAG 摄取层的成熟。 |
| [headroomlabs-ai/headroom](https://github.com/headroomlabs-ai/headroom) | Python | 78,856 | 在工具输出、日志、文件和 RAG 分块进入 LLM 之前进行压缩——编码智能体减少 20% token，JSON 减少 60–95%。上下文压缩作为独立一层是一个真正全新的品类。 |
| [mem0ai/mem0](https://github.com/mem0ai/mem0) | Python | 68,407 | 面向 AI 智能体和应用的即插即用记忆基础设施，为生产环境而构建。它是今日趋势榜上开源记忆实验的商业级对应物。 |
| [volcengine/OpenViking](https://github.com/volcengine/OpenViking) | Python | 41,539 | 一个自进化的上下文数据库，统一智能体记忆、知识 RAG 和技能。字节跳动/火山引擎的入局表明超大规模厂商对智能体上下文基础设施的兴趣。 |
| [weaviate/weaviate](https://github.com/weaviate/weaviate) | Go | 16,937 | 一个开源向量数据库，在云规模下将向量搜索与结构化过滤相结合。它仍是生产级向量搜索的参考实现。 |
| [VectifyAI/PageIndex](https://github.com/VectifyAI/PageIndex) | Python | 39,987 | 一个面向无向量、基于推理的 RAG 的文档索引。与 graphify 一道，它印证了一股明显的“后向量”RAG 暗流。 |

---

## 3. 趋势信号分析

今日的爆炸性关注集中在**智能体上下文与控制基础设施**——而非模型。涨幅最大的项目（`agent-desktop`、`auto-ontology`、`claude-mem`、`worktrunk`）都在于为智能体赋予*双手*（计算机使用）、*结构*（本体/图谱）、*记忆*（持久上下文）和*并行性*（worktree）。这是一个生态系统的经典模式：它已在足够强大的基础模型上安定下来，如今正围绕其 harness 展开争夺。大量“技能”仓库（`mattpocock/skills`、`scenario-labs/skills`、`gstack`）表明，**技能正成为一种可分发的产物格式**，类似于面向智能体的 npm 包。

本数据集中首次出现两个新技术方向。第一，**无向量 RAG** 由 `graphify`（基于 AST 的知识图谱，“无向量存储”）和 `PageIndex`（基于推理的索引）明确代表——这是对嵌入流水线在代码和结构化文档上脆弱性的一种回应。第二，**作为基础设施的上下文压缩**（`headroom`）和**本体支持的确定性检索**（`auto-ontology`）指向一门最适合称为*上下文工程*的学科，与经典 RAG 截然不同。

语言与技术栈信号值得注意：**Rust 反复出现**（`agent-desktop`、`worktrunk`、`Codewhale`、`rig`、`meilisearch`、`qdrant`、`weaviate`），印证了 Rust 作为高性能智能体与搜索基础设施默认选择的地位。**Go** 出现在编码智能体（`DeepSeek-Reasonix`）和 LLM 运行时（`ollama`）中。极端量化角度（`kimi-k3-in-c`、`picollm`）与更广泛的行业趋势相关联：面向本地、私有推理以及日益庞大的开源模型（Kimi K3、DeepSeek、Qwen、GLM 均出现在 `ollama` 的支持列表中）。多智能体垂直领域——交易（`TradingAgents`、`Vibe-Trading`）、求职（`career-ops`）、教育（`DeepTutor`）、视频（`MoneyPrinterTurbo`）——表明智能体框架正逐步毕业为领域产品。

---

## 4. 社区热点

- **[thedotmack/claude-mem](https://github.com/thedotmack/claude-mem)** — 跨会话持久记忆是智能体工具中被提及最多的缺失环节；其跨 harness 支持（Claude Code、Codex、Gemini、Copilot）使其有望成为标准，而非单一厂商的功能。
- **[Graphify-Labs/graphify](https://github.com/Graphify-Labs/graphify) + [VectifyAI/PageIndex](https://github.com/VectifyAI/PageIndex)** — 无向量/基于图的 RAG 方向值得密切关注；对于代码库和结构化文档，它在可解释性和确定性上可能胜过嵌入流水线。
- **[vercel-labs/agent-desktop](https://github.com/vercel-labs/agent-desktop)** — 作为 CLI 的计算机使用是通往主流智能体自动化最可能的路径；今日 +2,463 的 Star 飙升使其成为可供对标基准的项目。
- **[headroomlabs-ai/headroom](https://github.com/headroomlabs-ai/headroom)** — Token 成本优化正悄然成为每个智能体团队的一项预算条目；JSON 负载减少 60–95% 是一个有说服力且可衡量的收益。
- **[NVIDIA/auto-ontology](https://github.com/NVIDIA/auto-ontology)** — 厂商支持的本体层可能成为企业智能体的“schema”层；值得关注其与图数据库和 MCP 工具的集成。

*注：Star 数均按所提供数据原样复现；若干趋势榜条目报告总 Star 数为“0”，这很可能反映的是 API 对新近索引或重置的仓库的报告，而非项目的实际年龄。*

---
*本日报由 [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar) 自动生成。*