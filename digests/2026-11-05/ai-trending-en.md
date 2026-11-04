# AI Open Source Trends 2026-11-05

> Sources: GitHub Trending + GitHub Search API | Generated: 2026-11-04 23:22 UTC

---

# AI Open Source Trends Report — 2026-11-05

## 1. Today's Highlights

Today's GitHub landscape is dominated by the **agent tooling layer**: persistent memory (`claude-mem`), computer-use CLIs (`agent-desktop`, +2,463 today), and ontology-backed reasoning (`NVIDIA/auto-ontology`, +2,053) all surged simultaneously — a clear signal that the community is racing to give agents *context, hands, and structure* rather than new base models. The single largest daily gain in the raw data belongs to `vercel-labs/agent-desktop`, suggesting Agent-to-Computer interaction is crossing from research demo into developer infrastructure. Meanwhile, RAG is visibly mutating into "context engineering": graph-based codebase indexing (`Graphify-Labs/graphify`), tool-output compression (`headroomlabs-ai/headroom`), and self-evolving context databases (`volcengine/OpenViking`) all sit in the top tier of the ⭐ rankings. A quirky systems-engineering highlight — `kimi-k3-in-c` claims 2.78T-parameter inference in 8.24 GB RAM on a single CPU — signals renewed appetite for extreme-quantization and portable inference. Overall, the day's theme is **infrastructure for agents**, not models themselves.

---

## 2. Top Projects by Category

### 🔧 AI Infrastructure

| Project | Lang | Stars (total / today) | Summary |
| :--- | :--- | ---: | :--- |
| [vercel-labs/agent-desktop](https://github.com/vercel-labs/agent-desktop) | Rust | 0 (+2,463) | A computer-use CLI that lets AI agents drive a desktop environment. It posted today's single largest star gain, a strong signal that "agent + computer use" is becoming a first-class developer primitive. |
| [NVIDIA/auto-ontology](https://github.com/NVIDIA/auto-ontology) | Python | 0 (+2,053) | A Python framework for building semantic, graph-based reasoning layers over data, enabling deterministic query generation and explainable LLM/agent workflows. NVIDIA entering the agent-context space with a +2,053 debut makes this a notable vendor-backed signal. |
| [max-sixty/worktrunk](https://github.com/max-sixty/worktrunk) | Rust | 0 (+524) | A CLI for Git worktree management purpose-built for parallel AI agent workflows. It reflects the emerging need to run multiple agents on isolated branches simultaneously. |
| [mattpocock/skills](https://github.com/mattpocock/skills) | Shell | 0 (+1,376) | A curated set of "skills for real engineers" extracted from the author's `.agents` directory. Its rapid traction underscores how "agent skills" are becoming a shareable asset format. |
| [garrytan/gstack](https://github.com/garrytan/gstack) | TypeScript | 0 (+285) | A reproduction of Garry Tan's opinionated Claude Code setup with 23 tools acting as CEO, designer, eng manager, release manager, and QA. It's a high-profile example of "agent org charts" as reusable configuration. |
| [FareedKhan-dev/kimi-k3-in-c](https://github.com/FareedKhan-dev/kimi-k3-in-c) | C | 0 (+351) | Portable C99 inference for a claimed 2.78-trillion-parameter Kimi K3 on a single CPU in 8.24 GB RAM — no BLAS, no framework, no GPU. Whether or not the claim survives scrutiny, it spotlights demand for extreme quantization and framework-free inference. |
| [scenario-labs/skills](https://github.com/scenario-labs/skills) | Python | 0 (+224) | Production-ready image, video, audio, and 3D generation skills for any AI agent, with cost-aware model selection and brand/character consistency via the Scenario MCP. It shows media-generation being packaged as agent-callable skills. |

### 🤖 AI Agents / Workflows

| Project | Lang | Stars (total / today) | Summary |
| :--- | :--- | ---: | :--- |
| [thedotmack/claude-mem](https://github.com/thedotmack/claude-mem) | TypeScript | 112,805 (+1,165) | Captures everything an agent does during a session, AI-compresses it, and injects relevant context into future sessions across Claude Code, Codex, Gemini, Copilot, and more. It is the clearest expression of the "persistent agent memory" category now going mainstream. |
| [affaan-m/ECC](https://github.com/affaan-m/ECC) | JavaScript | 297,226 | An agent-harness performance optimization system covering skills, instincts, memory, security, and research-first development for Claude Code, Codex, Cursor, and others. Its massive star count makes it a de facto reference point for harness engineering. |
| [firecrawl/firecrawl](https://github.com/firecrawl/firecrawl) | TypeScript | 198,643 | A web-data library for AI agents that turns the open web into agent-ready structured input. It remains the canonical "agent data layer" project with enormous community gravity. |
| [langchain-ai/langgraph](https://github.com/langchain-ai/langgraph) | Python | 44,472 | A framework for building resilient, stateful agents as graphs. It anchors the production-grade agent orchestration segment. |
| [CopilotKit/CopilotKit](https://github.com/CopilotKit/CopilotKit) | TypeScript | 38,518 | The frontend stack for agents and generative UI across React, Angular, mobile, and Slack, and steward of the AG-UI Protocol. It marks agent UX becoming its own protocolized layer. |
| [esengine/DeepSeek-Reasonix](https://github.com/esengine/DeepSeek-Reasonix) | Go | 36,079 | A reliable coding agent for complex software engineering tasks. Go-based coding agents are a newer stack pattern worth watching. |
| [codewhale-hq/Codewhale](https://github.com/codewhale-hq/Codewhale) | Rust | 41,157 | An open-source, provider-agnostic terminal coding agent written in Rust. It fits the trend of high-performance Rust CLIs replacing Python prototypes. |
| [The-PR-Agent/pr-agent](https://github.com/The-PR-Agent/pr-agent) | Python | 0 (+279) | The original open-source PR reviewer agent, explicitly positioned as not the Qodo free tier. It shows agentic code review remaining a durable niche. |

### 📦 AI Applications

| Project | Lang | Stars (total / today) | Summary |
| :--- | :--- | ---: | :--- |
| [harry0703/MoneyPrinterTurbo](https://github.com/harry0703/MoneyPrinterTurbo) | Python | 138,913 (+615) | Generates HD short videos from a topic or keyword via an automated AI workflow. It continues to demonstrate strong consumer demand for one-click generative video pipelines. |
| [TauricResearch/TradingAgents](https://github.com/TauricResearch/TradingAgents) | Python | 120,631 | A multi-agent LLM framework for financial trading. It represents the maturing "agent teams for verticals" pattern. |
| [career-ops-hq/career-ops](https://github.com/career-ops-hq/career-ops) | JavaScript | 80,184 | An open-source AI job-search agent that scores jobs against your CV, tailors resumes, and tracks applications while you press Submit. Vertical agentic applications like this are gaining serious traction. |
| [hugohe3/ppt-master](https://github.com/hugohe3/ppt-master) | Python | 73,495 | Turns documents or topics into native PowerPoint decks with real shapes, transitions, charts, and narration. Native-format generation (rather than image slides) is the differentiator. |
| [HKUDS/Vibe-Trading](https://github.com/HKUDS/Vibe-Trading) | Python | 37,652 | A personal trading agent from HKUDS. It is one of several finance-focused agents showing vertical consolidation. |
| [HKUDS/DeepTutor](https://github.com/HKUDS/DeepTutor) | Python | 55,040 | Lifelong personalized tutoring powered by LLM/RAG techniques. It highlights education as an emerging vertical for agentic apps. |
| [Thysrael/Horizon](https://github.com/Thysrael/Horizon) | Python | 0 (+121) | An AI-powered personal news radar producing daily briefings in English and Chinese. It's a small but representative example of the personal-monitoring agent niche. |

### 🧠 LLMs / Training

| Project | Lang | Stars (total / today) | Summary |
| :--- | :--- | ---: | :--- |
| [huggingface/transformers](https://github.com/huggingface/transformers) | Python | 168,547 | The de facto model-definition framework for text, vision, audio, and multimodal models. It remains the ecosystem's stable center of gravity. |
| [ollama/ollama](https://github.com/ollama/ollama) | Go | 185,933 | Local inference for Kimi, GLM, MiniMax, DeepSeek, gpt-oss, Qwen, Gemma, and more. It's the default local-runtime gateway for the long tail of open models. |
| [rasbt/LLMs-from-scratch](https://github.com/rasbt/LLMs-from-scratch) | Jupyter Notebook | 107,822 | A step-by-step PyTorch implementation of a ChatGPT-like LLM. It remains the most-recommended learning path for LLM internals. |
| [skyzh/tiny-llm](https://github.com/skyzh/tiny-llm) | Python | 5,554 | Teaches LLM inference systems on Apple Silicon by building a tiny vLLM + Qwen. Edge/Apple-Silicon inference education is a new and growing niche. |
| [0xPlaygrounds/rig](https://github.com/0xPlaygrounds/rig) | Rust | 9,064 | A Rust library for building modular, scalable LLM applications. Rust is increasingly the language of choice for LLM infrastructure. |
| [Picovoice/picollm](https://github.com/Picovoice/picollm) | Python | 318 | On-device LLM inference powered by X-bit quantization. It fits the broader shift toward private, local inference. |
| [open-compass/opencompass](https://github.com/open-compass/opencompass) | Python | 7,564 | An LLM evaluation platform covering 100+ datasets across knowledge, reasoning, coding, and safety. Evaluation tooling remains an essential but under-hyped layer. |

### 🔍 RAG / Knowledge

| Project | Lang | Stars (total / today) | Summary |
| :--- | :--- | ---: | :--- |
| [Graphify-Labs/graphify](https://github.com/Graphify-Labs/graphify) | Python | 142,420 | Turns codebases, docs, SQL schemas, configs, and PDFs into a queryable knowledge graph via deterministic AST parsing — no vector store. It is the leading "vectorless RAG" contender and a strong signal against pure embedding pipelines. |
| [Mintplex-Labs/anything-llm](https://github.com/Mintplex-Labs/anything-llm) | JavaScript | 67,943 | A local-first, self-hosted agent experience with built-in RAG and document handling. "Own your intelligence" positioning resonates with privacy-conscious teams. |
| [unclecode/crawl4ai](https://github.com/unclecode/crawl4ai) | Python | 86,905 | An Apache-2.0 crawler with an official hosted cloud offering for agent-ready web extraction and search. It shows open-core models maturing in the RAG ingestion layer. |
| [headroomlabs-ai/headroom](https://github.com/headroomlabs-ai/headroom) | Python | 78,856 | Compresses tool outputs, logs, files, and RAG chunks before they hit the LLM — 20% fewer tokens for coding agents, 60–95% for JSON. Context compression as a standalone layer is a genuinely new category. |
| [mem0ai/mem0](https://github.com/mem0ai/mem0) | Python | 68,407 | Drop-in memory infrastructure for AI agents and apps, built for production. It is the commercial-grade counterpart to the open memory experiments on today's trending list. |
| [volcengine/OpenViking](https://github.com/volcengine/OpenViking) | Python | 41,539 | A self-evolving context database unifying agent memory, knowledge RAG, and skills. ByteDance/Volcengine's entry signals hyperscaler interest in agent context infrastructure. |
| [weaviate/weaviate](https://github.com/weaviate/weaviate) | Go | 16,937 | An open-source vector database combining vector search with structured filtering at cloud scale. It remains a reference implementation for production vector search. |
| [VectifyAI/PageIndex](https://github.com/VectifyAI/PageIndex) | Python | 39,987 | A document index for vectorless, reasoning-based RAG. Together with graphify, it confirms a visible "post-vector" RAG undercurrent. |

---

## 3. Trend Signal Analysis

The explosive attention today is concentrated in **agent context and control infrastructure** — not models. The biggest movers (`agent-desktop`, `auto-ontology`, `claude-mem`, `worktrunk`) are all about giving agents *hands* (computer use), *structure* (ontologies/graphs), *memory* (persistent context), and *parallelism* (worktrees). This is the classic pattern of an ecosystem that has settled on capable-enough base models and is now fighting over the harness around them. The sheer volume of "skills" repositories (`mattpocock/skills`, `scenario-labs/skills`, `gstack`) suggests that **skills are becoming a distributable artifact format**, analogous to npm packages for agents.

Two new technology directions appear for the first time in this dataset. First, **vectorless RAG** is explicitly represented by `graphify` (AST-based knowledge graphs, "no vector store") and `PageIndex` (reasoning-based indexing) — a reaction to embedding-pipeline brittleness for code and structured documents. Second, **context compression as infrastructure** (`headroom`) and **ontology-backed deterministic retrieval** (`auto-ontology`) point toward a discipline best described as *context engineering*, distinct from classic RAG.

Language and stack signals are notable: **Rust appears repeatedly** (`agent-desktop`, `worktrunk`, `Codewhale`, `rig`, `meilisearch`, `qdrant`, `weaviate`), confirming Rust as the default for high-performance agent and search infrastructure. **Go** shows up in coding agents (`DeepSeek-Reasonix`) and LLM runtimes (`ollama`). The extreme-quantization angle (`kimi-k3-in-c`, `picollm`) connects to the broader industry push toward local, private inference and ever-larger open models (Kimi K3, DeepSeek, Qwen, GLM all named in `ollama`'s supported list). The multi-agent verticals — trading (`TradingAgents`, `Vibe-Trading`), job search (`career-ops`), education (`DeepTutor`), video (`MoneyPrinterTurbo`) — show agent frameworks graduating into domain products.

---

## 4. Community Hot Spots

- **[thedotmack/claude-mem](https://github.com/thedotmack/claude-mem)** — Persistent cross-session memory is the most-cited missing piece in agent tooling; its cross-harness support (Claude Code, Codex, Gemini, Copilot) positions it as a potential standard rather than a single-vendor feature.
- **[Graphify-Labs/graphify](https://github.com/Graphify-Labs/graphify) + [VectifyAI/PageIndex](https://github.com/VectifyAI/PageIndex)** — The vectorless/graph-based RAG direction deserves close tracking; for codebases and structured docs it may outperform embedding pipelines on explainability and determinism.
- **[vercel-labs/agent-desktop](https://github.com/vercel-labs/agent-desktop)** — Computer-use as a CLI is the most likely path to mainstream agent automation; today's +2,463 star spike makes it the project to benchmark against.
- **[headroomlabs-ai/headroom](https://github.com/headroomlabs-ai/headroom)** — Token-cost optimization is quietly becoming a budget line item for every agent team; 60–95% reduction on JSON payloads is a compelling, measurable win.
- **[NVIDIA/auto-ontology](https://github.com/NVIDIA/auto-ontology)** — Vendor-backed ontology layers could become the "schema" layer for enterprise agents; worth watching for integration with graph databases and MCP tooling.

*Note: star counts are reproduced verbatim from the supplied data; several trending-list entries report "0" total stars, which likely reflects API reporting for newly indexed or reset repositories rather than actual project age.*

---
*This digest is auto-generated by [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar).*