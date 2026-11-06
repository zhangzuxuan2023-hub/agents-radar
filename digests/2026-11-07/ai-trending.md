# AI 开源趋势日报 2026-11-07

> 数据来源: GitHub Trending + GitHub Search API | 生成时间: 2026-11-06 23:16 UTC

---

# AI 开源趋势报告 — 2026-11-07

## 1. 今日亮点

今日 GitHub trending 被一波新的 **“Agent Skills”** 工具所主导：`mattpocock/skills`（+2,058 stars）和 `cloudflare/security-audit-skill`（+2,524 stars）一夜之间爆发，标志着编码智能体的技能正成为一等开源产物。在趋势榜上，NVIDIA 的 `auto-ontology`（+2,005 stars）作为面向 LLM 与智能体工作流的严肃语义推理层格外引人注目，显示企业厂商正在推动确定性、可解释的上下文基础设施。AI 主题搜索显示，智能体框架与记忆/上下文工具持续占据主导，同时出现一个强劲的新子主题：token 压缩与上下文效率（`headroom`、`caveman`、`claude-mem`）。与此同时，RAG 正超越向量存储，向基于图与推理的检索演进（`graphify`、`PageIndex`、`OpenViking`）。

---

## 2. 按类别划分的热门项目

### 🔧 AI 基础设施

| 项目 | 语言 | Stars（总数 / 今日） | 摘要 |
| :--- | :--- | ---: | :--- |
| [NVIDIA/auto-ontology](https://github.com/NVIDIA/auto-ontology) | Python | 0 (+2,005) | 一个用于在数据之上构建语义化、基于图的推理层的框架，可为 LLM/智能体工作流实现确定性查询生成与可解释 AI。其单日 2,005 stars 是今日榜单上单日涨幅最大的项目之一。 |
| [google/skills](https://github.com/google/skills) | Python | 0 (+532) | 面向 Google 产品与技术的 Agent Skills——一个由厂商支持的编码智能体技能库。+532 的日增量显示 Google 正在将智能体技能分发正式化。 |
| [cloudflare/security-audit-skill](https://github.com/cloudflare/security-audit-skill) | JavaScript | 0 (+2,524) | 一个用于多阶段安全审计的编码智能体技能，具备独立验证、机器可读的发现结果。以 +2,524 stars 登顶今日趋势榜，标志着智能体驱动的安全成为突破性用例。 |
| [mattpocock/skills](https://github.com/mattpocock/skills) | Shell | 0 (+2,058) | “面向真正工程师的技能”，直接提取自作者的 `.agents` 目录。其 +2,058 的日增 stars 印证了智能体技能作为一种病毒式传播的社区格式。 |
| [Picovoice/picollm](https://github.com/Picovoice/picollm) | Python | 319 | 由 X-bit 量化驱动的端侧 LLM 推理。规模虽小，却是边缘/端侧推理工具的一个重要信号。 |
| [FonaTech/Project_Chronos](https://github.com/FonaTech/Project_Chronos) | Python | 207 | 通过前瞻预测与异步 DMA 预取实现零停顿 MoE 推理，针对 SSD I/O 优化，采用混合 MLA + 滑动窗口注意力。虽处早期阶段，但指向推理系统优化这一活跃前沿。 |

### 🤖 AI 智能体 / 工作流

| 项目 | 语言 | Stars（总数 / 今日） | 摘要 |
| :--- | :--- | ---: | :--- |
| [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent) | Python | 261,198 | “与你共同成长的智能体”——Nous Research 的通用智能体框架。凭借 261k stars 稳居智能体框架层级之首。 |
| [Panniantong/Agent-Reach](https://github.com/Panniantong/Agent-Reach) | Python | 109,672 | 通过单一 CLI 让 AI 智能体以零 API 费用读取/搜索 Twitter、Reddit、YouTube、GitHub、Bilibili 和小红书。这是智能体数据访问层正在商品化的强烈信号。 |
| [affaan-m/ECC](https://github.com/affaan-m/ECC) | JavaScript | 298,559 | 一个“智能体 harness 性能优化系统”——面向 Claude Code、Codex、Cursor 及更多工具的 skills、instincts、memory、security 与研究优先开发。今日数据集中 star 数最高的 AI 仓库。 |
| [HKUDS/nanobot](https://github.com/HKUDS/nanobot) | Python | 49,884 | 来自 HKUDS 的轻量级开源个人智能体。与 CowAgent 和 QwenPaw 一同代表“个人智能体”浪潮。 |
| [zhayujie/CowAgent](https://github.com/zhayujie/CowAgent) | Python | 47,819 | 开源个人 AI 助手与智能体 harness，支持规划、工具/技能、记忆、多智能体与多模型。一行安装降低了自托管智能体的门槛。 |
| [CopilotKit/CopilotKit](https://github.com/CopilotKit/CopilotKit) | TypeScript | 38,548 | 面向智能体与生成式 UI 的前端技术栈（React、Angular、Mobile、Slack），也是 AG-UI Protocol 的管理者。对智能体-UI 标准化叙事至关重要。 |
| [codewhale-hq/Codewhale](https://github.com/codewhale-hq/Codewhale) | Rust | 41,156 | 一个用 Rust 编写的开源终端编码智能体，支持任意提供商或模型。为基于 Rust 的智能体工具趋势再添一例。 |
| [0xPlaygrounds/rig](https://github.com/0xPlaygrounds/rig) | Rust | 9,081 | Rust 中模块化、可扩展的 LLM 应用框架。是日益壮大的 Rust LLM 应用栈的代表。 |

### 📦 AI 应用

| 项目 | 语言 | Stars（总数 / 今日） | 摘要 |
| :--- | :--- | ---: | :--- |
| [career-ops-hq/career-ops](https://github.com/career-ops-hq/career-ops) | JavaScript | 80,721 | 一个开源 AI 求职智能体，可扫描招聘板、根据你的简历为职位打分、定制简历并在本地跟踪申请。是垂直智能体应用获得巨大关注的生动例证。 |
| [hugohe3/ppt-master](https://github.com/hugohe3/ppt-master) | Python | 74,260 | 将文档或主题转化为原生 PowerPoint 演示文稿，包含形状、切换、图表与旁白。文档转演示文稿生成仍是高需求垂直领域。 |
| [ZhuLinsen/daily_stock_analysis](https://github.com/ZhuLinsen/daily_stock_analysis) | Python | 67,648 | 由 LLM 驱动的多市场股票分析，具备实时新闻、仪表盘与自动化推送通知。金融仍是智能体应用的热门垂直领域。 |
| [HKUDS/Vibe-Trading](https://github.com/HKUDS/Vibe-Trading) | Python | 37,792 | 来自 HKUDS 的“你的个人交易智能体”。将 HKUDS 的产品组合延伸至金融自动化。 |
| [TauricResearch/TradingAgents](https://github.com/TauricResearch/TradingAgents) | Python | 121,010 | 多智能体 LLM 金融交易框架。是多智能体金融研究的成熟基准。 |
| [harry0703/MoneyPrinterTurbo](https://github.com/harry0703/MoneyPrinterTurbo) | Python | 140,241 | 通过 AI 工作流从主题或关键词一键生成高清短视频。其持续的高 star 存在感显示生成式媒体应用仍保持势头。 |
| [HKUDS/DeepTutor](https://github.com/HKUDS/DeepTutor) | Python | 55,916 | 终身个性化辅导系统。教育正成为持久的智能体应用垂直领域。 |

### 🧠 LLM / 训练

| 项目 | 语言 | Stars（总数 / 今日） | 摘要 |
| :--- | :--- | ---: | :--- |
| [huggingface/transformers](https://github.com/huggingface/transformers) | Python | 168,606 | 面向最先进文本、视觉、音频与多模态模型的模型定义框架。仍是最权威的 LLM 基础库。 |
| [ollama/ollama](https://github.com/ollama/ollama) | Go | 186,141 | 本地模型运行器，支持 Kimi、GLM、MiniMax、DeepSeek、gpt-oss、Qwen 与 Gemma。是本地 LLM 实验的默认入口。 |
| [rasbt/LLMs-from-scratch](https://github.com/rasbt/LLMs-from-scratch) | Jupyter Notebook | 107,928 | 逐步用 PyTorch 实现类 ChatGPT 的 LLM。对从零构建的教育内容需求持久，表明从业者群体正走向成熟。 |
| [skyzh/tiny-llm](https://github.com/skyzh/tiny-llm) | Python | 5,678 | 通过在 Apple Silicon 上构建一个微型 vLLM + Qwen 来学习 LLM 推理系统。是一个小众但值得注意的系统教育项目。 |
| [open-compass/opencompass](https://github.com/open-compass/opencompass) | Python | 7,569 | LLM 评估平台，覆盖 OpenAI、Anthropic、Gemini、Qwen、GLM、DeepSeek 及 100+ 数据集。随着模型数量增长，评估基础设施愈发重要。 |
| [Mirrowel/LLM-API-Key-Proxy](https://github.com/Mirrowel/LLM-API-Key-Proxy) | Python | 559 | 通用 LLM 网关，具备 OpenAI/Anthropic 兼容端点、多提供商转换与智能负载均衡。反映多模型路由已成为标准需求。 |

### 🔍 RAG / 知识

| 项目 | 语言 | Stars（总数 / 今日） | 摘要 |
| :--- | :--- | ---: | :--- |
| [open-webui/open-webui](https://github.com/open-webui/open-webui) | Svelte | 157,951 | 用户友好的 AI 界面，支持 Ollama 与 OpenAI API。是事实上的自托管聊天 UI 与 RAG 前端。 |
| [langchain-ai/langchain](https://github.com/langchain-ai/langchain) | Python | 149,138 | 智能体工程平台，如今围绕智能体与检索定位。仍是生态系统的连接组织。 |
| [Graphify-Labs/graphify](https://github.com/Graphify-Labs/graphify) | Python | 143,533 | 通过 `/graphify` 技能将任意代码库（文档、SQL schema、配置、PDF）转化为可查询的知识图谱，采用确定性 AST 解析，无需向量存储。是“无向量 RAG”转向的旗舰项目。 |
| [thedotmack/claude-mem](https://github.com/thedotmack/claude-mem) | TypeScript | 113,997 | 为智能体提供跨会话的持久上下文——捕获会话活动，用 AI 压缩并重新注入相关上下文。记忆即基础设施是一大主题。 |
| [infiniflow/ragflow](https://github.com/infiniflow/ragflow) | Go | 93,693 | 一个智能体原生数据库，从 RAG 引擎成长为上下文基础设施。将 RAG 重新定位为上下文基础设施而非流水线。 |
| [mem0ai/mem0](https://github.com/mem0ai/mem0) | Python | 68,531 | 面向 AI 智能体与应用的即插即用记忆层，为生产环境而构建。是领先的专用智能体记忆产品。 |
| [headroomlabs-ai/headroom](https://github.com/headroomlabs-ai/headroom) | Python | 79,020 | 在工具输出、日志、文件与 RAG 分块到达 LLM 之前进行压缩（编码智能体减少 20% token，JSON 减少 60–95%）。token 效率中间件是一个新兴且快速增长的类别。 |
| [VectifyAI/PageIndex](https://github.com/VectifyAI/PageIndex) | Python | 40,145 | 面向无向量、基于推理的 RAG 的文档索引。是推理型检索正在挑战纯向量搜索的又一明确信号。 |

---

## 3. 趋势信号分析

**智能体技能是突破性格式。** 今日最强的单一信号是“技能”类仓库的爆发：`cloudflare/security-audit-skill`（+2,524）、`mattpocock/skills`（+2,058）和 `google/skills`（+532）同时登上趋势榜。技能——打包好、机器可读的能力，放入编码智能体的目录中——正成为智能体行为的分布单元，就像 npm 包之于 JavaScript。厂商（Google、Cloudflare）与个人从业者正汇聚于同一格式，这暗示着一个新兴的事实标准以及即将到来的技能注册表生态。

**上下文效率是新的优化前沿。** 一批快速崛起的项目——`headroom`（token 压缩中间件）、`caveman`（一个“像穴居人一样说话”的代理，可削减 65% token）、`claude-mem` 与 `mem0`（持久记忆）——都在攻克同一个瓶颈：上下文窗口与 token 成本。随着智能体运行时间更长、多轮交互更多，上下文工程已分化为两大阵营：压缩与持久化。

**RAG 正走向无向量与基于图。** `graphify`（143k stars，确定性知识图谱，无向量存储）、`PageIndex`（基于推理的文档索引）和 `OpenViking`（自演化上下文数据库）都在弱化嵌入。NVIDIA 的 `auto-ontology` 强化了这一点：面向确定性、可解释检索的语义图层。这很可能是对具备更强长上下文与推理能力的 LLM 发布的回应，这些能力降低了对稠密向量相似度的依赖。

**Rust 与 Go 持续侵入 AI 技术栈。** `Codewhale`、`rig`、`qdrant`、`meilisearch` 和 `Project_Chronos` 显示系统级语言正在接管智能体 CLI、推理优化与向量/搜索基础设施。与此同时，Python 在应用层与 RAG 项目中仍占主导。

---

## 4. 社区热点

- **智能体技能与 harness** — [`cloudflare/security-audit-skill`](https://github.com/cloudflare/security-audit-skill)、[`mattpocock/skills`](https://github.com/mattpocock/skills)、[`affaan-m/ECC`](https://github.com/affaan-m/ECC)：今日变化最快的层级；技能正成为智能体能力的打包标准，而 harness 优化（ECC）正吸引最大的 star 数。
- **上下文与记忆基础设施** — [`headroomlabs-ai/headroom`](https://github.com/headroomlabs-ai/headroom)、[`thedotmack/claude-mem`](https://github.com/thedotmack/claude-mem)、[`mem0ai/mem0`](https://github.com/mem0ai/mem0)：token 压缩与持久记忆是让智能体更便宜、更强大的最高杠杆手段；预计此处将快速整合。
- **无向量 / 图 RAG** — [`Graphify-Labs/graphify`](https://github.com/Graphify-Labs/graphify)、[`VectifyAI/PageIndex`](https://github.com/VectifyAI/PageIndex)、[`NVIDIA/auto-ontology`](https://github.com/NVIDIA/auto-ontology)：确定性、可解释的检索正相对纯嵌入搜索取得进展，尤其是在代码库与结构化文档方面。
- **垂直智能体应用** — [`career-ops-hq/career-ops`](https://github.com/career-ops-hq/career-ops)、[`ZhuLinsen/daily_stock_analysis`](https://github.com/ZhuLinsen/daily_stock_analysis)、[`hugohe3/ppt-master`](https://github.com/hugohe3/ppt-master)：求职、金融与文档生成表明，范围狭窄、可本地运行的智能体能够积累数万 stars。
- **Rust 智能体与推理工具** — [`codewhale-hq/Codewhale`](https://github.com/codewhale-hq/Codewhale)、[`0xPlaygrounds/rig`](https://github.com/0xPlaygrounds/rig)、[`FonaTech/Project_Chronos`](https://github.com/FonaTech/Project_Chronos)：一个规模较小但技术深厚的社区，在 Python 之外构建性能关键的智能体与推理基础设施。

---
*本日报由 [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar) 自动生成。*