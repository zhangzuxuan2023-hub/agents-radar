# AI Open Source Trends 2026-11-09

> Sources: GitHub Trending + GitHub Search API | Generated: 2026-11-08 23:13 UTC

---

# AI Open Source Trends Report — 2026-11-09

## 1. Today's Highlights

Agent infrastructure is dominating today's ecosystem: from Vercel's `agent-browser` and Anthropic's `skills` repository to a wave of agent harnesses (ECC, hermes-agent, Codewhale), the community is racing to build the tooling layer around coding agents. A striking new signal is **context/token economics** — `billion-context`, `claude-mem`, `headroom`, and the viral `caveman` proxy all attack the same problem: keeping agent sessions cheap and persistent over long horizons. Anthropic had a strong day across two repos (`claude-quickstarts`, `skills`), reinforcing its push to standardize agent skill formats. Meanwhile, Meta's `astryx` marks "agent-ready" design systems as an emerging category, and NVIDIA's `nvke` signals bare-metal GPU Kubernetes as a first-class infrastructure concern. Notably, Meta's `astryx`, Vercel's `agent-browser` and the `caveman` token proxy appear for the first time, pointing to Big Tech moving deeper into agent tooling.

## 2. Top Projects by Category

### 🔧 AI Infrastructure

| Project | Lang | Stars (total / today) | Summary |
| :--- | :--- | ---: | :--- |
| [anthropics/claude-quickstarts](https://github.com/anthropics/claude-quickstarts) | Python | 0 (+28) | Official starter projects for building deployable applications with the Claude API. Worth attention as Anthropic's canonical onboarding path for new developers. |
| [vercel-labs/agent-browser](https://github.com/vercel-labs/agent-browser) | Rust | 0 (+812) | A browser automation CLI purpose-built for AI agents. Its Rust implementation and 812 stars in a single day signal strong demand for fast, dependable agent-browser infrastructure. |
| [meta/astryx](https://github.com/meta/astryx) | TypeScript | 0 (+863) | An open-source, fully customizable design system explicitly built to be "agent ready." Meta entering the agent-facing UI layer is a notable new direction. |
| [dsx-ai-factory/nvke](https://github.com/dsx-ai-factory/nvke) | Go | 0 (+345) | NVIDIA Kubernetes Engine, a management plane for GPU Kubernetes on bare metal. Reflects GPU cluster management maturing into dedicated open-source tooling. |
| [google-deepmind/mujoco](https://github.com/google-deepmind/mujoco) | C++ | 0 (+26) | A general-purpose physics simulator for multi-joint dynamics with contact. Foundational for robotics and embodied AI research. |
| [Mirrowel/LLM-API-Key-Proxy](https://github.com/Mirrowel/LLM-API-Key-Proxy) | Python | 560 | A universal LLM gateway offering one OpenAI/Anthropic-compatible API across providers with intelligent load-balancing. Useful plumbing for multi-model deployments. |
| [Picovoice/picollm](https://github.com/Picovoice/picollm) | Python | 319 | On-device LLM inference powered by X-bit quantization. Pushes private, local inference further into the edge. |

### 🤖 AI Agents / Workflows

| Project | Lang | Stars (total / today) | Summary |
| :--- | :--- | ---: | :--- |
| [affaan-m/ECC](https://github.com/affaan-m/ECC) | JavaScript | 299,694 | An agent harness performance optimization system covering skills, memory, security, and research-first development across Claude Code, Codex, Cursor and more. A standout hub for the coding-agent ecosystem. |
| [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent) | Python | 261,755 | "The agent that grows with you," from NousResearch. A major entrant in the open agent space with substantial community traction. |
| [browser-use/browser-use](https://github.com/browser-use/browser-use) | Python | 120,344 | An open-source Python library for AI browser agents, plus a cloud offering with stealth browsers and hosted agent API. One of the most mature browser-agent stacks. |
| [anthropics/skills](https://github.com/anthropics/skills) | Python | 0 (+793) | Anthropic's public repository for Agent Skills, standardizing how capabilities are packaged for agents. Important for anyone building on Claude or interoperable agent frameworks. |
| [zhayujie/CowAgent](https://github.com/zhayujie/CowAgent) | Python | 47,838 | An open-source personal AI assistant and agent harness with planning, tools, self-evolution, multi-agent and multi-channel support. Lightweight and one-line installable. |
| [CopilotKit/CopilotKit](https://github.com/CopilotKit/CopilotKit) | TypeScript | 38,568 | The frontend stack for agents and generative UI (React, Angular, Slack), and maintainers of the AG-UI Protocol. Bridges agent backends to user interfaces. |
| [career-ops-hq/career-ops](https://github.com/career-ops-hq/career-ops) | JavaScript | 81,189 | An open-source AI job search agent that scans boards, scores jobs against your CV, and tailors resumes. A strong example of vertical agent automation. |
| [codewhale-hq/Codewhale](https://github.com/codewhale-hq/Codewhale) | Rust | 41,159 | An open-source terminal coding agent written in Rust that works with any provider or model. Signals Rust gaining ground in agent tooling. |

### 📦 AI Applications

| Project | Lang | Stars (total / today) | Summary |
| :--- | :--- | ---: | :--- |
| [milind-soni/OpenMausBot](https://github.com/milind-soni/OpenMausBot) | TypeScript | 0 (+1,437) | An open-source Grok Bot alternative featuring a virtual machine that bots can use. Its 1,437 stars today make it one of the biggest single-day gainers. |
| [hugohe3/ppt-master](https://github.com/hugohe3/ppt-master) | Python | 74,888 | AI converts documents or topics into real, native PowerPoint decks with shapes, transitions, charts and audio narration. A polished vertical generative application. |
| [harry0703/MoneyPrinterTurbo](https://github.com/harry0703/MoneyPrinterTurbo) | Python | 141,040 | Generates HD short videos from a topic or keyword via an automated AI workflow. A high-traffic example of consumer-facing generative media tooling. |
| [HKUDS/DeepTutor](https://github.com/HKUDS/DeepTutor) | Python | 56,958 | A lifelong personalized tutoring system. Demonstrates LLMs moving into education verticals. |
| [ZhuLinsen/daily_stock_analysis](https://github.com/ZhuLinsen/daily_stock_analysis) | Python | 68,238 | An LLM-powered multi-market stock analysis system with dashboards and automated notifications. A representative finance-vertical agent application. |
| [TauricResearch/TradingAgents](https://github.com/TauricResearch/TradingAgents) | Python | 121,372 | A multi-agent LLM framework for financial trading. Notable for combining agent orchestration with a quant domain. |

### 🧠 LLMs / Training

| Project | Lang | Stars (total / today) | Summary |
| :--- | :--- | ---: | :--- |
| [huggingface/transformers](https://github.com/huggingface/transformers) | Python | 168,666 | The model-definition framework for state-of-the-art text, vision, audio and multimodal models. Remains the ecosystem's backbone for both inference and training. |
| [ollama/ollama](https://github.com/ollama/ollama) | Go | 186,380 | Local model runner supporting Kimi, GLM, MiniMax, DeepSeek, gpt-oss, Qwen, Gemma and more. The default on-ramp for running open models locally. |
| [rasbt/LLMs-from-scratch](https://github.com/rasbt/LLMs-from-scratch) | Jupyter Notebook | 108,030 | Build a ChatGPT-like LLM in PyTorch step by step. A leading educational resource for understanding training internals. |
| [skyzh/tiny-llm](https://github.com/skyzh/tiny-llm) | Python | 5,692 | Learn LLM inference systems on Apple Silicon by building a tiny vLLM + Qwen. A valuable systems-level learning path for engineers. |
| [open-compass/opencompass](https://github.com/open-compass/opencompass) | Python | 7,577 | An open LLM evaluation platform spanning OpenAI, Anthropic, Gemini, Qwen, GLM, DeepSeek and 100+ datasets. Critical for model comparison and safety checks. |
| [thinkwee/AgentsMeetRL](https://github.com/thinkwee/AgentsMeetRL) | HTML | 1,878 | An awesome list for agentic reinforcement learning. Reflects growing interest in RL-based agent training. |
| [0xPlaygrounds/rig](https://github.com/0xPlaygrounds/rig) | Rust | 9,095 | A Rust framework for building modular, scalable LLM applications. Part of the Rust-for-AI tooling wave. |

### 🔍 RAG / Knowledge

| Project | Lang | Stars (total / today) | Summary |
| :--- | :--- | ---: | :--- |
| [Shubhamsaboo/awesome-llm-apps](https://github.com/Shubhamsaboo/awesome-llm-apps) | Python | 144,212 | 100+ AI agents, agent skills and RAG apps, free and open source. A high-signal collection for builders evaluating patterns. |
| [Graphify-Labs/graphify](https://github.com/Graphify-Labs/graphify) | Python | 144,485 | Turns any codebase, docs, SQL schemas, configs and PDFs into a queryable knowledge graph with deterministic AST parsing and no vector store. An interesting counter-trend to vector-first RAG. |
| [infiniflow/ragflow](https://github.com/infiniflow/ragflow) | Go | 93,779 | A RAG engine that has grown into "agent-native context infrastructure." One of the most widely adopted open RAG platforms. |
| [unclecode/crawl4ai](https://github.com/unclecode/crawl4ai) | Python | 87,101 | An Apache-2.0 web crawler powering a hosted scraping/extraction/search API for AI agents. A key data-ingestion layer for RAG. |
| [mem0ai/mem0](https://github.com/mem0ai/mem0) | Python | 68,643 | A drop-in memory layer for AI agents delivering persistent context in production. Central to the emerging agent-memory category. |
| [thedotmack/claude-mem](https://github.com/thedotmack/claude-mem) | TypeScript | 114,881 | Captures and compresses agent session data, then re-injects relevant context into future sessions across many agent tools. A cross-tool memory solution with major traction. |
| [headroomlabs-ai/headroom](https://github.com/headroomlabs-ai/headroom) | Python | 79,160 | Compresses tool outputs, logs and RAG chunks before they reach the LLM, cutting 20-95% of tokens. Directly addresses the context-cost problem. |
| [milvus-io/milvus](https://github.com/milvus-io/milvus) | Go | 46,609 | A high-performance, cloud-native vector database for scalable ANN search. Remains a core piece of RAG infrastructure. |

## 3. Trend Signal Analysis

The most explosive attention today is on **agent-adjacent tooling**, not models themselves. Eight of nine top-trending repos touch agents, skills, or agent front-ends (agent-browser, skills, astryx, OpenMausBot, claude-quickstarts). Within this, a distinct sub-trend has crystallized: **context/token economics**. `billion-context` (+460 today), `claude-mem`, `headroom`, and the viral `caveman` proxy all promise token savings or long-horizon session memory — a clear response to the cost and context-window limits of coding agents running month-long sessions. This suggests the community treats memory and compression as first-class infrastructure rather than model features.

New tech stacks are also emerging: **Rust** appears repeatedly in agent infrastructure (agent-browser, Codewhale, rig, qdrant), signaling a shift toward performance-sensitive agent runtimes. `meta/astryx` introduces "agent-ready" design systems as a category, and `dsx-ai-factory/nvke` formalizes GPU Kubernetes management — a sign that AI infrastructure is moving down the stack toward hardware orchestration. Anthropic's dual appearance (`claude-quickstarts`, `skills`) points to deliberate ecosystem standardization around agent skill formats, likely tied to ongoing Claude/agent releases. The vector-first RAG assumption is also being challenged: `graphify` explicitly avoids vector stores in favor of deterministic knowledge graphs, and `PageIndex` markets "vectorless reasoning-based RAG."

## 4. Community Hot Spots

- **[vercel-labs/agent-browser](https://github.com/vercel-labs/agent-browser)** — Browser automation for agents in Rust; +812 stars today. A foundational primitive likely to be relied on by many agent projects.
- **[anthropics/skills](https://github.com/anthropics/skills)** — Standardizing agent skills could shape interoperability across frameworks. Watch for adoption beyond Claude.
- **[headroomlabs-ai/headroom](https://github.com/headroomlabs-ai/headroom) & [thedotmack/claude-mem](https://github.com/thedotmack/claude-mem)** — Context compression and persistent memory are the clearest pain points today; both are worth evaluating for any long-running agent.
- **[meta/astryx](https://github.com/meta/astryx)** — "Agent-ready" design systems from Meta; a new intersection of design tooling and agents.
- **[Graphify-Labs/graphify](https://github.com/Graphify-Labs/graphify)** — Vectorless, deterministic knowledge graphs as an alternative RAG architecture; a directional signal for retrieval systems.

---
*This digest is auto-generated by [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar).*