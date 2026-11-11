# AI 开源趋势日报 2026-11-12

> 数据来源: GitHub Trending + GitHub Search API | 生成时间: 2026-11-11 23:28 UTC

---

# AI Open Source Trends Report — 2026-11-12

## 1. Today's Highlights

The dominant theme today is the **agent harness layer** — skills, memory, token compression, and browser/terminal control for coding agents — with several projects posting four-to-five-figure daily star gains. Notably, `affaan-m/ECC` (agent harness optimization: skills, instincts, memory, security) sits atop the LLM topic pool at ~301K stars, while token-economy projects like `JuliusBrussee/caveman` (cuts 65% of tokens) and `headroomlabs-ai/headroom` signal a maturing focus on **cost and context efficiency** rather than raw capability. Meta made an unusual entry with `meta/astryx`, an "agent-ready" design system, suggesting big-platform interest in the agent-facing UI layer. Meanwhile, vendor security tooling appears: OpenAI shipped `codex-security` for vulnerability discovery and fixing. The vector-database and RAG categories continue to fragment into specialized niches — graph-based context, vectorless reasoning RAG, and in-process databases.

---

## 2. Top Projects by Category

### 🔧 AI Infrastructure (frameworks, SDKs, inference engines, dev tools, CLI)

| Project | Lang | Stars (total / today) | Summary |
| :--- | :--- | ---: | :--- |
| [tensorflow/tensorflow](https://github.com/tensorflow/tensorflow) | C++ | 201,564 | The long-standing open-source ML framework, still the highest-starred project in the ML topic pool. Its continued presence anchors the infrastructure layer even as agent tooling dominates attention. |
| [huggingface/transformers](https://github.com/huggingface/transformers) | Python | 168,878 | The de facto model-definition framework for text, vision, audio, and multimodal models. Remains the default integration surface for nearly every new model release. |
| [pytorch/pytorch](https://github.com/pytorch/pytorch) | Python | 105,441 | Tensors and dynamic neural networks with strong GPU acceleration; the training backbone for most of the ecosystem. |
| [ollama/ollama](https://github.com/ollama/ollama) | Go | 186,686 | Local model runtime supporting Kimi, GLM, MiniMax, DeepSeek, gpt-oss, Qwen, and Gemma. The go-to path for running open-weight models locally. |
| [vercel-labs/agent-browser](https://github.com/vercel-labs/agent-browser) | Rust | 0 (+712 today) | A browser automation CLI purpose-built for AI agents. Vercel Labs entering the agent-tooling space with a Rust CLI is a notable platform signal today. |
| [openai/codex-security](https://github.com/openai/codex-security) | TypeScript | 0 (+20 today) | OpenAI's CLI and TypeScript SDK for finding, validating, and fixing security vulnerabilities. A first-party vendor push into agentic security workflows. |
| [0xPlaygrounds/rig](https://github.com/0xPlaygrounds/rig) | Rust | 9,115 | A Rust library for building modular, scalable LLM applications. Represents the growing Rust contingent in LLM app infrastructure. |
| [skyzh/tiny-llm](https://github.com/skyzh/tiny-llm) | Python | 5,719 | A teaching project building a tiny vLLM + Qwen inference system on Apple Silicon. Valuable for systems engineers learning inference internals. |

### 🤖 AI Agents / Workflows (agent frameworks, automation, multi-agent systems)

| Project | Lang | Stars (total / today) | Summary |
| :--- | :--- | ---: | :--- |
| [affaan-m/ECC](https://github.com/affaan-m/ECC) | JavaScript | 301,351 | An agent harness performance optimization system covering skills, instincts, memory, security, and research-first development for Claude Code, Codex, Cursor, and more. The single largest project in the LLM pool — a strong signal that harness-level optimization is the hottest layer. |
| [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent) | Python | 262,734 | "The agent that grows with you" — a general-purpose agent framework from Nous Research. Its scale reflects sustained appetite for open agent scaffolds. |
| [Significant-Gravitas/AutoGPT](https://github.com/Significant-Gravitas/AutoGPT) | Python | 187,999 | The original accessible-autonomy agent project, now a broad platform for building agents. Still a top-of-funnel entry point for new agent developers. |
| [langchain-ai/langchain](https://github.com/langchain-ai/langchain) | Python | 149,377 | The agent engineering platform, now positioned explicitly around agents rather than generic LLM chains. Its rebranding tracks the ecosystem's shift from RAG to agency. |
| [browser-use/browser-use](https://github.com/browser-use/browser-use) | Python | 120,739 | Open-source Python library for AI browser agents, plus a hosted cloud with stealth browsers. Browser control remains one of the most practical agent capabilities. |
| [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills) | JavaScript | 0 (+788 today) | Production-grade engineering skills for AI coding agents. Strong daily momentum (+788) suggests "skills" packaging is becoming a community convention. |
| [Panniantong/Agent-Reach](https://github.com/Panniantong/Agent-Reach) | Python | 111,125 | Gives agents read/search access to Twitter, Reddit, YouTube, GitHub, Bilibili, and XiaoHongShu via one CLI with zero API fees. Solves a real pain point: agent-accessible social/web data. |
| [paperclipai/paperclip](https://github.com/paperclipai/paperclip) | TypeScript | 113,182 | The open-source app for managing agents at work. Points to an emerging "agent operations/management" subcategory. |

### 📦 AI Applications (specific apps, vertical solutions)

| Project | Lang | Stars (total / today) | Summary |
| :--- | :--- | ---: | :--- |
| [open-webui/open-webui](https://github.com/open-webui/open-webui) | Svelte | 158,415 | A user-friendly AI interface supporting Ollama, OpenAI API, and more. The default self-hosted chat front end for many teams. |
| [harry0703/MoneyPrinterTurbo](https://github.com/harry0703/MoneyPrinterTurbo) | Python | 142,579 | Generates HD short videos from a topic or keyword using an automated AI workflow. A durable example of consumer-facing generative media apps. |
| [hugohe3/ppt-master](https://github.com/hugohe3/ppt-master) | Python | 75,956 | Turns documents or topics into native PowerPoint decks with real shapes, transitions, charts, and audio narration. Native-format output (not images) is the differentiator. |
| [ZhuLinsen/daily_stock_analysis](https://github.com/ZhuLinsen/daily_stock_analysis) | Python | 70,172 (+429 today) | LLM-powered multi-market stock analysis with real-time news, dashboards, and zero-cost scheduled runs. Appears on both the trending list and topic search — dual-signal momentum. |
| [career-ops-hq/career-ops](https://github.com/career-ops-hq/career-ops) | JavaScript | 81,996 | An open-source AI job search agent that scores jobs against your CV and tailors ATS-friendly applications. Vertical agent applications are clearly finding audiences. |
| [HKUDS/Vibe-Trading](https://github.com/HKUDS/Vibe-Trading) | Python | 38,058 | A personal trading agent from HKUDS. Another data point in the "personal finance agent" cluster alongside stock analysis tools. |
| [HKUDS/nanobot](https://github.com/HKUDS/nanobot) | Python | 50,020 | A lightweight open-source personal agent. Minimal-footprint agents are a counter-trend to heavyweight harnesses. |
| [homeassistant-ai/ha-mcp](https://github.com/homeassistant-ai/ha-mcp) | Python | 0 (+640 today) | Configure and automate Home Assistant with Claude, ChatGPT, and other assistants via MCP. Smart-home automation through MCP is a concrete, high-value use case. |

### 🧠 LLMs / Training (model weights, training frameworks, fine-tuning tools)

| Project | Lang | Stars (total / today) | Summary |
| :--- | :--- | ---: | :--- |
| [rasbt/LLMs-from-scratch](https://github.com/rasbt/LLMs-from-scratch) | Jupyter Notebook | 108,250 | Implements a ChatGPT-like LLM in PyTorch step by step. The leading educational resource for understanding LLM internals. |
| [open-compass/opencompass](https://github.com/open-compass/opencompass) | Python | 7,584 | An LLM evaluation platform spanning OpenAI, Anthropic, Gemini, Qwen, GLM, DeepSeek across 100+ datasets. Evaluation is increasingly the bottleneck for model adoption. |
| [genieincodebottle/generative-ai](https://github.com/genieincodebottle/generative-ai) | Jupyter Notebook | 2,669 | A comprehensive GenAI resource: roadmap, projects, interview prep. Educational content continues to accumulate stars steadily. |
| [thinkwee/AgentsMeetRL](https://github.com/thinkwee/AgentsMeetRL) | HTML | 1,879 | An awesome list for agentic reinforcement learning. Agentic RL is emerging as a distinct research direction worth tracking. |
| [Picovoice/picollm](https://github.com/Picovoice/picollm) | Python | 319 | On-device LLM inference powered by X-bit quantization. Small but notable for the edge-inference niche. |
| [zchoi/Awesome-Embodied-Robotics-and-Agent](https://github.com/zchoi/Awesome-Embodied-Robotics-and-Agent) | — | 1,916 | Curated research on embodied AI and robots with LLMs. Signals continued convergence of LLMs and robotics. |

### 🔍 RAG / Knowledge (vector databases, retrieval-augmented generation, knowledge management)

| Project | Lang | Stars (total / today) | Summary |
| :--- | :--- | ---: | :--- |
| [langgenius/dify](https://github.com/langgenius/dify) | Python | 160,992 | Builds agentic workflows and RAG pipelines on one collaborative workspace, deployable cloud/VPC/self-hosted. The leading all-in-one agent+RAG platform. |
| [Shubhamsaboo/awesome-llm-apps](https://github.com/Shubhamsaboo/awesome-llm-apps) | Python | 144,436 | 100+ AI agents, agent skills, and RAG apps, free and open source. A high-leverage starting point for builders. |
| [Graphify-Labs/graphify](https://github.com/Graphify-Labs/graphify) | Python | 146,153 | Turns any codebase, docs, SQL schemas, configs, and PDFs into a queryable knowledge graph, with no vector store. Graph-based context is a notable alternative to embedding-only RAG. |
| [thedotmack/claude-mem](https://github.com/thedotmack/claude-mem) | TypeScript | 116,217 | Persistent cross-session context for every agent — captures, compresses, and re-injects relevant context. Agent memory is consolidating into its own product category. |
| [infiniflow/ragflow](https://github.com/infiniflow/ragflow) | Go | 94,045 | A RAG engine that has grown into "context infrastructure," described as an agent-native database. Tracks the RAG-to-context-infrastructure evolution. |
| [headroomlabs-ai/headroom](https://github.com/headroomlabs-ai/headroom) | Python | 79,378 | Compresses tool outputs, logs, files, and RAG chunks before they reach the LLM — 60–95% fewer tokens for JSON. Directly addresses the context-cost problem. |
| [mem0ai/mem0](https://github.com/mem0ai/mem0) | Python | 68,825 | Drop-in memory infrastructure for AI agents and apps. Production-oriented memory is becoming table stakes. |
| [VectifyAI/PageIndex](https://github.com/VectifyAI/PageIndex) | Python | 40,372 | A document index for "vectorless, reasoning-based RAG." Represents experimentation beyond the vector-DB orthodoxy. |

---

## 3. Trend Signal Analysis

**The agent harness layer is the explosive category.** The numbers are unambiguous: `ECC` (301K), `hermes-agent` (263K), and a wave of harness-adjacent projects — skills (`agent-skills`, +788 today), memory (`claude-mem`), token compression (`caveman`, `headroom`), and agent data access (`Agent-Reach`) — dominate both star counts and daily momentum. The community has moved past "can an agent do this?" to "how do we make agents cheaper, more persistent, and more reliable?" Token-economy projects are especially telling: `caveman`'s 65% token cut and `headroom`'s 60–95% JSON reduction target the real cost driver in production agent deployments.

**New tech stacks and directions.** Three firsts stand out. First, **Rust is now a serious agent-infrastructure language** — `agent-browser`, `Codewhale`, and `rig` all ship agent tooling in Rust, prioritizing performance for CLI and runtime layers. Second, **graph and vectorless retrieval** (`graphify`, `PageIndex`) challenge embedding-only RAG, while `OpenViking` proposes a "self-evolving context database" unifying memory, RAG, and skills. Third, **agent-ready design systems** (`meta/astryx`) hint that UI frameworks will soon ship agent-native primitives, and `CopilotKit`'s AG-UI Protocol formalizes the frontend-agent contract.

**Industry connection.** The prominence of vendor security tooling (`openai/codex-security`) and MCP-based integrations (`ha-mcp`, `claude-context`) reflects the post-MCP-standardization phase: agents are being wired into real systems — homes, codebases, browsers — with security and context as the binding constraints. The eval and agentic-RL projects (`opencompass`, `AgentsMeetRL`) suggest the next frontier is measuring and training agents, not just prompting them.

---

## 4. Community Hot Spots

- **[affaan-m/ECC](https://github.com/affaan-m/ECC)** — At ~301K stars, the harness-optimization layer (skills + memory + security) is the highest-leverage place to build right now; expect fast-following competitors.
- **[headroomlabs-ai/headroom](https://github.com/headroomlabs-ai/headroom) & [JuliusBrussee/caveman](https://github.com/JuliusBrussee/caveman)** — Token/context compression is transitioning from hack to infrastructure. If you run agents in production, evaluate these before your next billing cycle.
- **[Graphify-Labs/graphify](https://github.com/Graphify-Labs/graphify) & [VectifyAI/PageIndex](https://github.com/VectifyAI/PageIndex)** — Graph-based and vectorless retrieval are credible alternatives to embedding-only RAG; worth benchmarking on codebase and document QA.
- **[vercel-labs/agent-browser](https://github.com/vercel-labs/agent-browser) & [homeassistant-ai/ha-mcp](https://github.com/homeassistant-ai/ha-mcp)** — Platform vendors (Vercel, Home Assistant) shipping first-party agent tooling signals the "agent integration SDK" market is opening.
- **[openai/codex-security](https://github.com/openai/codex-security)** — Early but strategically important: first-party agentic security scanning may become a default CI step; watch the SDK adoption curve.

---
*本日报由 [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar) 自动生成。*