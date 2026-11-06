# AI Open Source Trends 2026-11-07

> Sources: GitHub Trending + GitHub Search API | Generated: 2026-11-06 23:16 UTC

---

# AI Open Source Trends Report — 2026-11-07

## 1. Today's Highlights

Today's GitHub trending is dominated by a new wave of **"Agent Skills"** tooling: `mattpocock/skills` (+2,058 stars) and `cloudflare/security-audit-skill` (+2,524 stars) both exploded overnight, signaling that coding-agent skills are becoming a first-class open-source artifact. On the trending list, NVIDIA's `auto-ontology` (+2,005 stars) stands out as a serious semantic-reasoning layer for LLM and agent workflows, showing enterprise vendors pushing deterministic, explainable context infrastructure. The AI topic search reveals continued dominance of agent frameworks and memory/context tooling, with a strong new sub-theme: token compression and context-efficiency (`headroom`, `caveman`, `claude-mem`). Meanwhile, RAG evolves beyond vector stores toward graph- and reasoning-based retrieval (`graphify`, `PageIndex`, `OpenViking`).

---

## 2. Top Projects by Category

### 🔧 AI Infrastructure

| Project | Lang | Stars (total / today) | Summary |
| :--- | :--- | ---: | :--- |
| [NVIDIA/auto-ontology](https://github.com/NVIDIA/auto-ontology) | Python | 0 (+2,005) | A framework for building semantic, graph-based reasoning layers over data, enabling deterministic query generation and explainable AI for LLM/agent workflows. Its 2,005 stars in a single day mark one of the largest single-day spikes on today's list. |
| [google/skills](https://github.com/google/skills) | Python | 0 (+532) | Agent Skills for Google products and technologies — a vendor-backed skill library for coding agents. The +532 daily gain shows Google formalizing agent-skill distribution. |
| [cloudflare/security-audit-skill](https://github.com/cloudflare/security-audit-skill) | JavaScript | 0 (+2,524) | A coding-agent skill for multi-phase security audits with independently verified, machine-readable findings. Topping today's trending at +2,524 stars, it signals agent-driven security as a breakout use case. |
| [mattpocock/skills](https://github.com/mattpocock/skills) | Shell | 0 (+2,058) | "Skills for Real Engineers," extracted straight from the author's `.agents` directory. Its +2,058 daily stars confirm agent skills as a viral community format. |
| [Picovoice/picollm](https://github.com/Picovoice/picollm) | Python | 319 | On-device LLM inference powered by X-bit quantization. A small but notable signal for edge/on-device inference tooling. |
| [FonaTech/Project_Chronos](https://github.com/FonaTech/Project_Chronos) | Python | 207 | Zero-stall MoE inference via lookahead prediction and async DMA prefetching, optimized for SSD I/O with hybrid MLA + sliding-window attention. Early-stage but points at inference-system optimization as a live frontier. |

### 🤖 AI Agents / Workflows

| Project | Lang | Stars (total / today) | Summary |
| :--- | :--- | ---: | :--- |
| [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent) | Python | 261,198 | "The agent that grows with you" — Nous Research's general-purpose agent framework. With 261k stars it anchors the agent-framework tier. |
| [Panniantong/Agent-Reach](https://github.com/Panniantong/Agent-Reach) | Python | 109,672 | Gives AI agents read/search access to Twitter, Reddit, YouTube, GitHub, Bilibili and XiaoHongShu via one CLI with zero API fees. A strong signal that agent data-access layers are commoditizing. |
| [affaan-m/ECC](https://github.com/affaan-m/ECC) | JavaScript | 298,559 | An "agent harness performance optimization system" — skills, instincts, memory, security and research-first development for Claude Code, Codex, Cursor and beyond. The highest-starred AI repo in today's dataset. |
| [HKUDS/nanobot](https://github.com/HKUDS/nanobot) | Python | 49,884 | A lightweight open-source personal agent from HKUDS. Represents the "personal agent" wave alongside CowAgent and QwenPaw. |
| [zhayujie/CowAgent](https://github.com/zhayujie/CowAgent) | Python | 47,819 | Open-source personal AI assistant and agent harness with planning, tools/skills, memory, multi-agent and multi-model support. One-line install lowers the barrier to self-hosted agents. |
| [CopilotKit/CopilotKit](https://github.com/CopilotKit/CopilotKit) | TypeScript | 38,548 | The frontend stack for agents and generative UI (React, Angular, Mobile, Slack), and steward of the AG-UI Protocol. Key for the agent-UI standardization story. |
| [codewhale-hq/Codewhale](https://github.com/codewhale-hq/Codewhale) | Rust | 41,156 | An open-source terminal coding agent in Rust supporting any provider or model. Adds to the Rust-based agent-tooling trend. |
| [0xPlaygrounds/rig](https://github.com/0xPlaygrounds/rig) | Rust | 9,081 | Modular, scalable LLM application framework in Rust. A representative of the growing Rust LLM-app stack. |

### 📦 AI Applications

| Project | Lang | Stars (total / today) | Summary |
| :--- | :--- | ---: | :--- |
| [career-ops-hq/career-ops](https://github.com/career-ops-hq/career-ops) | JavaScript | 80,721 | An open-source AI job-search agent that scans boards, scores jobs against your CV, tailors resumes and tracks applications locally. A vivid example of vertical agent apps hitting massive traction. |
| [hugohe3/ppt-master](https://github.com/hugohe3/ppt-master) | Python | 74,260 | Turns documents or topics into native PowerPoint decks with shapes, transitions, charts and narration. Document-to-deck generation remains a high-demand vertical. |
| [ZhuLinsen/daily_stock_analysis](https://github.com/ZhuLinsen/daily_stock_analysis) | Python | 67,648 | LLM-powered multi-market stock analysis with real-time news, dashboards and automated push notifications. Finance remains a top vertical for agentic apps. |
| [HKUDS/Vibe-Trading](https://github.com/HKUDS/Vibe-Trading) | Python | 37,792 | "Your Personal Trading Agent" from HKUDS. Extends the HKUDS portfolio into finance automation. |
| [TauricResearch/TradingAgents](https://github.com/TauricResearch/TradingAgents) | Python | 121,010 | Multi-agent LLM financial trading framework. A mature benchmark for multi-agent finance research. |
| [harry0703/MoneyPrinterTurbo](https://github.com/harry0703/MoneyPrinterTurbo) | Python | 140,241 | One-click HD short-video generation from a topic or keyword via AI workflow. Persistent high-star presence shows generative media apps retain momentum. |
| [HKUDS/DeepTutor](https://github.com/HKUDS/DeepTutor) | Python | 55,916 | Lifelong personalized tutoring system. Education is emerging as a durable agent-app vertical. |

### 🧠 LLMs / Training

| Project | Lang | Stars (total / today) | Summary |
| :--- | :--- | ---: | :--- |
| [huggingface/transformers](https://github.com/huggingface/transformers) | Python | 168,606 | The model-definition framework for state-of-the-art text, vision, audio and multimodal models. Remains the canonical LLM foundation library. |
| [ollama/ollama](https://github.com/ollama/ollama) | Go | 186,141 | Local model runner supporting Kimi, GLM, MiniMax, DeepSeek, gpt-oss, Qwen and Gemma. The default on-ramp for local LLM experimentation. |
| [rasbt/LLMs-from-scratch](https://github.com/rasbt/LLMs-from-scratch) | Jupyter Notebook | 107,928 | Step-by-step PyTorch implementation of a ChatGPT-like LLM. Enduring demand for from-scratch education signals a maturing practitioner base. |
| [skyzh/tiny-llm](https://github.com/skyzh/tiny-llm) | Python | 5,678 | Learn LLM inference systems on Apple Silicon by building a tiny vLLM + Qwen. A niche but notable systems-education project. |
| [open-compass/opencompass](https://github.com/open-compass/opencompass) | Python | 7,569 | LLM evaluation platform covering OpenAI, Anthropic, Gemini, Qwen, GLM, DeepSeek and 100+ datasets. Evaluation infrastructure keeps gaining importance as model count grows. |
| [Mirrowel/LLM-API-Key-Proxy](https://github.com/Mirrowel/LLM-API-Key-Proxy) | Python | 559 | Universal LLM gateway with OpenAI/Anthropic-compatible endpoints, multi-provider translation and intelligent load balancing. Reflects multi-model routing as a standard need. |

### 🔍 RAG / Knowledge

| Project | Lang | Stars (total / today) | Summary |
| :--- | :--- | ---: | :--- |
| [open-webui/open-webui](https://github.com/open-webui/open-webui) | Svelte | 157,951 | User-friendly AI interface supporting Ollama and OpenAI APIs. The de facto self-hosted chat UI and RAG front end. |
| [langchain-ai/langchain](https://github.com/langchain-ai/langchain) | Python | 149,138 | The agent engineering platform, now positioned around agents and retrieval. Still the ecosystem's connective tissue. |
| [Graphify-Labs/graphify](https://github.com/Graphify-Labs/graphify) | Python | 143,533 | Turns any codebase (docs, SQL schemas, configs, PDFs) into a queryable knowledge graph via a `/graphify` skill, with deterministic AST parsing and no vector store. A flagship of the "vectorless RAG" turn. |
| [thedotmack/claude-mem](https://github.com/thedotmack/claude-mem) | TypeScript | 113,997 | Persistent cross-session context for agents — captures session activity, compresses it with AI and reinjects relevant context. Memory-as-infrastructure is a major theme. |
| [infiniflow/ragflow](https://github.com/infiniflow/ragflow) | Go | 93,693 | An agent-native database that grew from a RAG engine into context infrastructure. Repositions RAG as context infra rather than a pipeline. |
| [mem0ai/mem0](https://github.com/mem0ai/mem0) | Python | 68,531 | Drop-in memory layer for AI agents and apps, built for production. The leading dedicated agent-memory product. |
| [headroomlabs-ai/headroom](https://github.com/headroomlabs-ai/headroom) | Python | 79,020 | Compresses tool outputs, logs, files and RAG chunks before they reach the LLM (20% fewer tokens for coding agents, 60–95% for JSON). Token-efficiency middleware is a fresh, fast-growing category. |
| [VectifyAI/PageIndex](https://github.com/VectifyAI/PageIndex) | Python | 40,145 | Document index for vectorless, reasoning-based RAG. Another clear signal that reasoning-based retrieval is challenging pure vector search. |

---

## 3. Trend Signal Analysis

**Agent skills are the breakout format.** The single strongest signal today is the eruption of "skill" repositories: `cloudflare/security-audit-skill` (+2,524), `mattpocock/skills` (+2,058) and `google/skills` (+532) all appeared on trending simultaneously. Skills — packaged, machine-readable capabilities dropped into a coding agent's directory — are becoming the distribution unit for agent behavior, much as npm packages were for JavaScript. Vendors (Google, Cloudflare) and individual practitioners are converging on the same format, which suggests an emerging de facto standard and a coming skill-registry ecosystem.

**Context efficiency is the new optimization frontier.** A cluster of fast-rising projects — `headroom` (token compression middleware), `caveman` (a "talk like a caveman" proxy cutting 65% of tokens), `claude-mem` and `mem0` (persistent memory) — all attack the same bottleneck: context windows and token cost. As agents run longer and multi-turn, context engineering has split into two camps: compression and persistence.

**RAG is going vectorless and graph-based.** `graphify` (143k stars, deterministic knowledge graphs with no vector store), `PageIndex` (reasoning-based document indexing) and `OpenViking` (self-evolving context database) all de-emphasize embeddings. NVIDIA's `auto-ontology` reinforces this: semantic graph layers for deterministic, explainable retrieval. This is likely a response to LLM releases with stronger long-context and reasoning capabilities, which reduce reliance on dense vector similarity.

**Rust and Go keep encroaching on the AI stack.** `Codewhale`, `rig`, `qdrant`, `meilisearch` and `Project_Chronos` show systems languages taking over agent CLIs, inference optimization and vector/search infrastructure. Meanwhile Python remains dominant for app-layer and RAG projects.

---

## 4. Community Hot Spots

- **Agent skills & harnesses** — [`cloudflare/security-audit-skill`](https://github.com/cloudflare/security-audit-skill), [`mattpocock/skills`](https://github.com/mattpocock/skills), [`affaan-m/ECC`](https://github.com/affaan-m/ECC): the fastest-moving layer today; skills are becoming the packaging standard for agent capabilities, and harness optimization (ECC) is attracting the largest star counts.
- **Context & memory infrastructure** — [`headroomlabs-ai/headroom`](https://github.com/headroomlabs-ai/headroom), [`thedotmack/claude-mem`](https://github.com/thedotmack/claude-mem), [`mem0ai/mem0`](https://github.com/mem0ai/mem0): token compression and persistent memory are the highest-leverage ways to make agents cheaper and more capable; expect rapid consolidation here.
- **Vectorless / graph RAG** — [`Graphify-Labs/graphify`](https://github.com/Graphify-Labs/graphify), [`VectifyAI/PageIndex`](https://github.com/VectifyAI/PageIndex), [`NVIDIA/auto-ontology`](https://github.com/NVIDIA/auto-ontology): deterministic, explainable retrieval is gaining ground against pure embedding search, especially for codebases and structured documents.
- **Vertical agent applications** — [`career-ops-hq/career-ops`](https://github.com/career-ops-hq/career-ops), [`ZhuLinsen/daily_stock_analysis`](https://github.com/ZhuLinsen/daily_stock_analysis), [`hugohe3/ppt-master`](https://github.com/hugohe3/ppt-master): job search, finance and document generation show that narrowly scoped, locally runnable agents can rack up tens of thousands of stars.
- **Rust agent & inference tooling** — [`codewhale-hq/Codewhale`](https://github.com/codewhale-hq/Codewhale), [`0xPlaygrounds/rig`](https://github.com/0xPlaygrounds/rig), [`FonaTech/Project_Chronos`](https://github.com/FonaTech/Project_Chronos): a smaller but technically deep community building performance-critical agent and inference infrastructure outside Python.

---
*This digest is auto-generated by [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar).*