# AI 开源趋势日报 2026-11-10

> 数据来源: GitHub Trending + GitHub Search API | 生成时间: 2026-11-09 23:19 UTC

---

# AI 开源趋势报告 — 2026-11-10

## 1. 今日亮点

今日趋势榜单由 **Agent 工具层** 而非模型发布主导：Anthropic 的 `skills` 仓库（+1,052）和 `claude-quickstarts`（+560）表明该厂商如今在提供 API 的同时也在交付“使用指南”层；Vercel Labs 的 `agent-browser`（+914）和 Garry Tan 的 `gbrain`（+215）则显示生态正在竞相为 Agent 赋予记忆与浏览器能力。Meta 的 `astryx` 设计系统（+3,324，当日 AI 相关最大涨幅）是最清晰的信号，表明“Agent 就绪”正被写入 UI 基础设施，而不仅仅是后端。在话题搜索侧，RAG/知识集群如今挤满了 **Token 压缩与上下文基础设施** 类项目（`headroom`、`claude-mem`、`OpenViking`、`PageIndex`）——社区在优化上下文窗口，而不只是填充它。与此同时，`caveman`（病毒式传播，118k stars）通过降级 Agent 文风来宣称节省 65% Token——直白却极具启示性的市场信号。

---

## 2. 分类热门项目

### 🔧 AI 基础设施

| 项目 | 语言 | Stars（总计 / 今日） | 简介 |
| :--- | :--- | ---: | :--- |
| [anthropics/skills](https://github.com/anthropics/skills) | Python | 0 (+1,052) | Anthropic 的 Agent Skills 公开仓库——为基于 Claude 的 Agent 提供可复用、打包好的能力。今日新增 Star 数排名第一的 AI 趋势仓库，标志着厂商将“skill”抽象正式化为一种分发格式。 |
| [vercel-labs/agent-browser](https://github.com/vercel-labs/agent-browser) | Rust | 0 (+914) | 专为 AI Agent 打造的浏览器自动化 CLI，来自 Vercel Labs。以 Rust 编写且涨势强劲（+914），在性能与可部署性上直接对标 Python 老牌项目如 `browser-use`。 |
| [anthropics/claude-quickstarts](https://github.com/anthropics/claude-quickstarts) | Python | 0 (+560) | 一组用于在 Claude API 上构建可部署应用的入门项目。与 `skills` 同步上榜（+560）表明开发者正在积极上手 Claude 技术栈，而非只是点星围观。 |
| [ollama/ollama](https://github.com/ollama/ollama) | Go | 186,443 | 事实上的本地模型运行器，现已宣称支持 Kimi、GLM、MiniMax、DeepSeek、gpt-oss、Qwen 和 Gemma。其广泛的模型覆盖使其成为大多数本地 Agent 实验的稳定底座。 |
| [0xPlaygrounds/rig](https://github.com/0xPlaygrounds/rig) | Rust | 9,102 | 用于构建可扩展 LLM 应用的模块化 Rust 框架。随着 Rust LLM 技术栈（rig、agent-browser、Codewhale）明显成熟，值得关注。 |
| [Picovoice/picollm](https://github.com/Picovoice/picollm) | Python | 319 | 基于 X-bit 量化的端侧 LLM 推理。规模虽小，但战略性地处于边缘推理前沿。 |

### 🤖 AI Agent / 工作流

| 项目 | 语言 | Stars（总计 / 今日） | 简介 |
| :--- | :--- | ---: | :--- |
| [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent) | Python | 262,061 | “与你共同成长的 Agent”——来自 Nous Research 的持久化、自我改进型 Agent。262k stars 使其成为今日数据集中最大的单一 Agent 仓库。 |
| [affaan-m/ECC](https://github.com/affaan-m/ECC) | JavaScript | 300,214 | 一个“Agent harness 性能优化系统”，为 Claude Code、Codex、Cursor 等添加技能、直觉、记忆和安全能力。整个数据集中最大的仓库（300k）——harness 层工具如今已自成一类。 |
| [langchain-ai/langchain](https://github.com/langchain-ai/langchain) | Python | 149,283 | 长期存在的 Agent 工程平台，如今自我定位为 Agent 工程平台而非“LLM 框架”。尽管挑战者众多，仍是默认的集成层。 |
| [browser-use/browser-use](https://github.com/browser-use/browser-use) | Python | 120,447 | 用于 AI 浏览器 Agent 的开源 Python 库，另提供带隐身浏览器的托管云服务。正是 Vercel 的 Rust CLI 如今攻击的在位者。 |
| [Panniantong/Agent-Reach](https://github.com/Panniantong/Agent-Reach) | Python | 110,609 | 通过一个 CLI 让 Agent 以零 API 费用读取/搜索 Twitter、Reddit、YouTube、GitHub、Bilibili 和小红书。对 Agent 数据访问问题的务实解答。 |
| [HKUDS/nanobot](https://github.com/HKUDS/nanobot) | Python | 49,975 | 来自 HKUDS 的轻量级开源个人 Agent。值得注意之处在于与该实验室的 `Vibe-Trading` 和 `DeepTutor` 配合，形成向垂直 Agent 的更广泛推进。 |
| [CopilotKit/CopilotKit](https://github.com/CopilotKit/CopilotKit) | TypeScript | 38,584 | Agent 与生成式 UI 的前端技术栈，也是 AG-UI Protocol 的维护者。其相关性在于 Agent UX 正成为一个协议问题，而不仅仅是组件问题。 |
| [codewhale-hq/Codewhale](https://github.com/codewhale-hq/Codewhale) | Rust | 41,175 | 一个支持任意提供商或模型的 Rust 开源终端 Agent。与 `oh-my-pi` 和 `DeepSeek-Reasonix` 一同构成明显的 Rust 终端 Agent 浪潮。 |

### 📦 AI 应用

| 项目 | 语言 | Stars（总计 / 今日） | 简介 |
| :--- | :--- | ---: | :--- |
| [Shubhamsaboo/awesome-llm-apps](https://github.com/Shubhamsaboo/awesome-llm-apps) | Python | 144,299 | 收录 100+ AI Agent、Agent 技能和 RAG 应用的目录，免费开源。充当生态事实上的应用索引，也是构建者实际交付内容的有力风向标。 |
| [harry0703/MoneyPrinterTurbo](https://github.com/harry0703/MoneyPrinterTurbo) | Python | 141,594 | 通过自动化 AI 工作流从主题或关键词生成高清短视频。一个持久面向消费者的自动化爆款，拥有 141k stars。 |
| [TauricResearch/TradingAgents](https://github.com/TauricResearch/TradingAgents) | Python | 121,609 | 用于金融交易的多 Agent LLM 框架。锚定了金融垂直 Agent 集群，该集群还包括 `Vibe-Trading` 和 `daily_stock_analysis`。 |
| [career-ops-hq/career-ops](https://github.com/career-ops-hq/career-ops) | JavaScript | 81,484 | 一个开源 AI 求职 Agent，扫描招聘板、根据你的简历为职位打分，并定制 ATS 友好的简历。高价值、单一用途 Agent 配合人工介入提交的典范。 |
| [hugohe3/ppt-master](https://github.com/hugohe3/ppt-master) | Python | 75,245 | 将文档或主题转化为原生 PowerPoint 演示文稿，含真实形状、动画和图表。值得注意之处在于输出真正的 `.pptx` 而非图片——一个有意义的质量标杆。 |
| [ZhuLinsen/daily_stock_analysis](https://github.com/ZhuLinsen/daily_stock_analysis) | Python | 68,797 (+505) | 零成本、LLM 驱动的多市场股票分析，含新闻、仪表盘和定时通知。同时出现在趋势榜和话题搜索中，印证了真实采用势头。 |
| [HKUDS/Vibe-Trading](https://github.com/HKUDS/Vibe-Trading) | Python | 37,972 | 来自 HKUDS 的“个人交易 Agent”。进一步印证个人金融是当前应用型 Agent 最拥挤的垂直领域。 |
| [homeassistant-ai/ha-mcp](https://github.com/homeassistant-ai/ha-mcp) | Python | 0 (+31) | 用于通过 Claude、ChatGPT 及其他助手配置和自动化 Home Assistant 的 MCP 服务器。Star 数虽低，但战略性地处于智能家居 × Agent 的交汇点。 |

### 🧠 LLM / 训练

| 项目 | 语言 | Stars（总计 / 今日） | 简介 |
| :--- | :--- | ---: | :--- |
| [huggingface/transformers](https://github.com/huggingface/transformers) | Python | 168,707 | 面向文本、视觉、音频和多模态模型的模型定义框架，同时支持推理和训练。仍是几乎所有其他项目的参考实现层。 |
| [rasbt/LLMs-from-scratch](https://github.com/rasbt/LLMs-from-scratch) | Jupyter Notebook | 108,108 | 在 PyTorch 中逐步构建类 ChatGPT 的 LLM。其 108k stars 表明即便工具层将模型抽象化，从零实现的教育需求依然持久。 |
| [pytorch/pytorch](https://github.com/pytorch/pytorch) | Python | 105,299 | 张量与动态神经网络，具备强大的 GPU 加速。角色未变，仍是底座。 |
| [skyzh/tiny-llm](https://github.com/skyzh/tiny-llm) | Python | 5,699 | 在 Apple Silicon 上通过构建一个微型 vLLM + Qwen 来学习 LLM 推理系统。一个罕见的针对推理栈而非训练的系统教育项目。 |
| [open-compass/opencompass](https://github.com/open-compass/opencompass) | Python | 7,578 | 一个 LLM 评测平台，覆盖 OpenAI、Anthropic、Gemini、Qwen、GLM 和 DeepSeek，跨越 100+ 数据集。评测正日益成为交付的瓶颈，使其悄然重要。 |
| [AkaliKong/MiniOneRec](https://github.com/AkaliKong/MiniOneRec) | Python | 1,963 | OneRec 的最小化复现。规模虽小，但代表了推荐规模 LLM 工作中可复现性趋势。 |

### 🔍 RAG / 知识

| 项目 | 语言 | Stars（总计 / 今日） | 简介 |
| :--- | :--- | ---: | :--- |
| [thedotmack/claude-mem](https://github.com/thedotmack/claude-mem) | TypeScript | 115,384 | 捕获 Agent 所做的一切，用 AI 压缩，并将相关上下文重新注入未来会话——跨 Claude Code、Codex、Gemini、Copilot 等。115k stars 的跨会话持久记忆是该类别中最清晰的信号。 |
| [infiniflow/ragflow](https://github.com/infiniflow/ragflow) | Go | 93,893 | 一个 RAG 引擎，已重新定位为“Agent 原生数据库”和上下文基础设施。重新定位本身就是故事：RAG 厂商正将自己重新包装为上下文层。 |
| [unclecode/crawl4ai](https://github.com/unclecode/crawl4ai) | Python | 87,148 | 面向 AI Agent 的 Apache-2.0 爬虫，现提供托管抓取/提取 API 和免费额度。每个 RAG 流水线的摄取半壁，配有清晰的商业封装。 |
| [headroomlabs-ai/headroom](https://github.com/headroomlabs-ai/headroom) | Python | 79,250 | 在工具输出、日志、文件和 RAG 分块进入 LLM 之前进行压缩——编码 Agent 减少 20% Token，JSON 减少 60–95%。今日获得关注的若干 Token 效率项目之一。 |
| [mem0ai/mem0](https://github.com/mem0ai/mem0) | Python | 68,707 | 面向 AI Agent 和应用的开箱即用记忆基础设施。是 `claude-mem` 会话捕获方法的生产级对应物。 |
| [volcengine/OpenViking](https://github.com/volcengine/OpenViking) | Python | 41,873 | 一个自我演进的上下文数据库，统一 Agent 记忆、知识 RAG 和技能。字节跳动火山引擎进入上下文基础设施领域是一个值得注意的厂商信号。 |
| [VectifyAI/PageIndex](https://github.com/VectifyAI/PageIndex) | Python | 40,317 | “无向量、基于推理的 RAG”文档索引。作为对向量数据库共识的真正架构性反向立场，值得关注。 |
| [alibaba/zvec](https://github.com/alibaba/zvec) | C++ | 16,221 | 一个轻量、极速的进程内向量数据库。嵌入式数据库模式（另见 LanceDB）在 Agent 工作负载上明显正在胜过基于服务器的存储。 |

---

## 3. 趋势信号分析

今日的主导信号是 **harness 与上下文工程，而非模型能力**。趋势榜前列是 Anthropic 发布 `skills` 和 `claude-quickstarts`、Vercel 发布 `agent-browser`，以及第三方发布 `ECC`（300k stars）和 `hermes-agent`（262k）——全是围绕模型 *周边* 而非模型本身的工具。社区显然已得出结论：Agent 性能如今的瓶颈在于技能、记忆、工具访问和 Token 预算，而非原始权重。RAG 集群印证了这一点：`headroom`、`claude-mem`、`OpenViking` 和 `PageIndex` 都在攻击上下文效率或记忆持久化，而 `caveman` 病毒式传播的 65% Token 削减卖点——无论多么噱头——直接反映了团队愿意为缩短提示词付出多少代价。

第二个信号是 **技术栈多元化，尤其是 Rust**。`agent-browser`、`rig`、`Codewhale`、`DeepSeek-Reasonix` 以及检索领域的 `meilisearch`/`qdrant`/`lancedb` 都指向 Rust 正在巩固其在 AI 技术栈性能关键层的地位——CLI Agent、推理管道和嵌入式向量搜索。Go 在本地运行器（`ollama`）和 RAG 引擎（`ragflow`、`milvus`）方面依然强劲，而 Python 仍主导训练和脚本编写。

第三，**厂商主导的标准化正在加速**：Anthropic 的 Skills 格式、CopilotKit 的 AG-UI Protocol 以及 MCP 集成（`ha-mcp`）表明生态正从临时粘合走向已发布的接口。值得注意的是，今日热门榜单中缺席的是新的前沿模型权重——这与一个发布增量、注意力已转向建立在上一波浪潮之上的应用与集成层的时期相符。

---

## 4. 社区热点

- **[anthropics/skills](https://github.com/anthropics/skills) + [anthropics/claude-quickstarts](https://github.com/anthropics/claude-quickstarts)** — 厂商背书的 Claude 技术栈打包与上手指南。若 Skills 成为一种分发格式，早期技能作者将获得超额曝光；即便你基于其他模型构建，也值得研究该格式。
- **[headroomlabs-ai/headroom](https://github.com/headroomlabs-ai/headroom) + [thedotmack/claude-mem](https://github.com/thedotmack/claude-mem)** — 上下文压缩与持久记忆是生产级 Agent 两个最高杠杆的成本/延迟调节点。两者都开箱即用且模型无关，是低风险的采用押注。
- **[vercel-labs/agent-browser](https://github.com/vercel-labs/agent-browser)** — 来自主要平台厂商的 Rust 浏览器 CLI，进入 Python 主导的细分领域。关注它是否会取代 `browser-use`；其性能与部署故事可信。
- **[VectifyAI/PageIndex](https://github.com/VectifyAI/PageIndex) + [alibaba/zvec](https://github.com/alibaba/zvec)** — 两个架构逆向者：无向量、基于推理的检索，以及嵌入式/进程内向量搜索。两者都挑战 Agent 规模工作负载默认的“大型向量数据库”假设。
- **[affaan-m/ECC](https://github.com/affaan-m/ECC)** — 300k stars，跨 Claude Code、Codex、Cursor 和 Opencode 的 harness 层优化是本数据集中最大的单一仓库。无论它在做什么，都正是大量开发者当前关注之处。

---
*本日报由 [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar) 自动生成。*