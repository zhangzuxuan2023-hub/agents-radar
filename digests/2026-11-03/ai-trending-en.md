# AI Open Source Trends 2026-11-03

> Sources: GitHub Trending + GitHub Search API | Generated: 2026-11-03 10:36 UTC

---

# AI Open Source Trends Report — 2026-11-03

---

## 1. Today's Highlights

Today's GitHub AI landscape is dominated by the **agent harness and context-management layer** rather than new models. The single strongest signal is agent memory/context infrastructure: `thedotmack/claude-mem`, `mem0`, and `cognee` all point to persistent context as the new battleground. Local and efficient inference continues to surge with `FreeToken`, `colibri`, and `nanochat` making datacenter-scale serving a desktop reality. The Claude Code / Codex skills ecosystem is exploding (`ECC`, `mattpocock/skills`, `Claude-Code-Game-Studios`), suggesting the community is now shipping *configuration and orchestration*, not just code. Finally, token-efficiency hacks (`heretic`, `caveman`, `headroom`) reveal a maturing cost-consciousness across the agent stack.

---

## 2. Top Projects by Category

### 🔧 AI Infrastructure (frameworks, SDKs, inference engines, dev tools, CLI)

| Project | Lang | Stars (total / today) | Summary |
| :--- | :--- | ---: | :--- |
| [karpathy/nanochat](https://github.com/karpathy/nanochat) | Python | 0 (+321) | "The best ChatGPT that $100 can buy" — a minimal, educational LLM training/inference stack from Karpathy. Worth attention for anyone learning the full model pipeline end-to-end. |
| [FlashML-org/FreeToken](https://github.com/FlashML-org/FreeToken) | Python | 0 (+653) | Brings datacenter-scale model serving to the desktop for running massive models locally. A strong signal that local-first inference is becoming polished. |
| [JustVugg/colibri](https://github.com/JustVugg/colibri) | C | 0 (+675) | Hierarchical inference engine for frontier MoE models: hot experts on GPU, warm in RAM, cold streamed from NVMe. Written in C for commodity hardware. |
| [vercel-labs/agent-desktop](https://github.com/vercel-labs/agent-desktop) | Rust | 0 (+2,275) | Computer-use CLI for AI agents. Today's top trending repo — highlights explosive interest in giving agents real desktop control surfaces. |
| [max-sixty/worktrunk](https://github.com/max-sixty/worktrunk) | Rust | 0 (+77) | CLI for Git worktree management designed for parallel AI agent workflows. Small but targeted at the emerging multi-agent dev workflow. |
| [0xPlaygrounds/rig](https://github.com/0xPlaygrounds/rig) | Rust | 9,054 | Build modular, scalable LLM applications in Rust. Continues the Rust-in-AI-stack momentum. |
| [netdata/netdata](https://github.com/netdata/netdata) | Go | 81,261 | AI-powered full stack observability, relevant as agent-infra monitoring grows. |

### 🤖 AI Agents / Workflows

| Project | Lang | Stars (total / today) | Summary |
| :--- | :--- | ---: | :--- |
| [affaan-m/ECC](https://github.com/affaan-m/ECC) | JavaScript | 296,039 | Agent harness performance optimization system — skills, instincts, memory, security for Claude Code, Codex, Cursor and more. Massive star count signals the harness-tuning wave. |
| [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent) | Python | 260,045 | "The agent that grows with you" — a general-purpose extensible agent. A top-tier momentum signal in the agent category. |
| [Panniantong/Agent-Reach](https://github.com/Panniantong/Agent-Reach) | Python | 108,243 | Gives AI agents eyes to read/search Twitter, Reddit, YouTube, GitHub, Bilibili, XiaoHongShu via one CLI with zero API fees. |
| [browser-use/browser-use](https://github.com/browser-use/browser-use) | Python | 119,914 | Open-source Python library for AI browser agents plus hosted cloud. A pillar of the web-agent stack. |
| [Donchitos/Claude-Code-Game-Studios](https://github.com/Donchitos/Claude-Code-Game-Studios) | Shell | 0 (+1,379) | Turn Claude Code into a full game dev studio — 49 AI agents, 72 workflow skills, coordination system. Shows how far the skills-orchestration meta has gone. |
| [CopilotKit/CopilotKit](https://github.com/CopilotKit/CopilotKit) | TypeScript | 38,487 | Frontend stack for agents & generative UI, makers of the AG-UI Protocol. |
| [codewhale-hq/Codewhale](https://github.com/codewhale-hq/Codewhale) | Rust | 41,163 | Open-source terminal agent in Rust, provider/model agnostic. |

### 📦 AI Applications (specific apps, vertical solutions)

| Project | Lang | Stars (total / today) | Summary |
| :--- | :--- | ---: | :--- |
| [shiyu-coder/Kronos](https://github.com/shiyu-coder/Kronos) | Python | 0 (+857) | Foundation model for the "language of financial markets." Vertical domain foundation models are a notable emerging direction. |
| [harry0703/MoneyPrinterTurbo](https://github.com/harry0703/MoneyPrinterTurbo) | Python | 137,875 | One-click HD short video generation from a topic via automated AI workflow. Persistent popular consumer AI app. |
| [paperclipai/paperclip](https://github.com/paperclipai/paperclip) | TypeScript | 0 (+412) | The open-source app everyone uses to manage agents at work — an agent management console for teams. |
| [career-ops-hq/career-ops](https://github.com/career-ops-hq/career-ops) | JavaScript | 79,689 | AI job search agent: scans boards, scores jobs vs your CV, tailors resumes. Clear vertical-agent play. |
| [hugohe3/ppt-master](https://github.com/hugohe3/ppt-master) | Python | 72,873 | Turns documents/topics into native PowerPoint decks with charts, narration, templates. |
| [ZhuLinsen/daily_stock_analysis](https://github.com/ZhuLinsen/daily_stock_analysis) | Python | 67,489 | LLM-powered multi-market stock analysis with dashboards and scheduled runs. |
| [HKUDS/Vibe-Trading](https://github.com/HKUDS/Vibe-Trading) | Python | 37,525 | "Your Personal Trading Agent." Financial vertical agents clustering in the top ranks. |

### 🧠 LLMs / Training (model weights, training frameworks, fine-tuning)

| Project | Lang | Stars (total / today) | Summary |
| :--- | :--- | ---: | :--- |
| [huggingface/transformers](https://github.com/huggingface/transformers) | Python | 168,485 | The model-definition framework for SOTA text/vision/audio/multimodal models. Evergreen backbone of the ecosystem. |
| [rasbt/LLMs-from-scratch](https://github.com/rasbt/LLMs-from-scratch) | Jupyter | 107,734 | Build a ChatGPT-like LLM in PyTorch step by step. Remains the canonical learning resource. |
| [ollama/ollama](https://github.com/ollama/ollama) | Go | 185,764 | Run Kimi, GLM, MiniMax, DeepSeek, Qwen, Gemma and others locally. The de-facto local model runner. |
| [p-e-w/heretic](https://github.com/p-e-w/heretic) | Python | 0 (+420) | Training-free LM modification: removes censorship, suppresses slop, reduces sycophancy. A signal of interest in post-hoc model steering. |
| [Picovoice/picollm](https://github.com/Picovoice/picollm) | Python | 318 | On-device LLM inference powered by X-bit quantization. Edge inference continues to mature. |
| [skyzh/tiny-llm](https://github.com/skyzh/tiny-llm) | Python | 5,530 | Learn LLM inference systems on Apple Silicon by building a tiny vLLM + Qwen. |
| [open-compass/opencompass](https://github.com/open-compass/opencompass) | Python | 7,563 | LLM evaluation platform across 100+ datasets and many providers. |

### 🔍 RAG / Knowledge (vector DBs, RAG, knowledge management)

| Project | Lang | Stars (total / today) | Summary |
| :--- | :--- | ---: | :--- |
| [thedotmack/claude-mem](https://github.com/thedotmack/claude-mem) | TypeScript | 111,233 (+1,803) | Persistent context across sessions for every agent — captures, AI-compresses, and re-injects context. Today's defining memory project, works across Claude Code, Codex, Gemini, Copilot and more. |
| [Graphify-Labs/graphify](https://github.com/Graphify-Labs/graphify) | Python | 141,484 | Turns any codebase/docs/SQL/PDFs into a queryable knowledge graph with no vector store, via deterministic AST parsing. Notable "vectorless" RAG direction. |
| [headroomlabs-ai/headroom](https://github.com/headroomlabs-ai/headroom) | Python | 78,694 | Compresses tool outputs, logs and RAG chunks before hitting the LLM (20–95% token savings). Token-efficiency infrastructure. |
| [mem0ai/mem0](https://github.com/mem0ai/mem0) | Python | 68,321 | The memory layer for AI agents — drop-in persistent context infrastructure for production. |
| [infiniflow/ragflow](https://github.com/infiniflow/ragflow) | Go | 93,418 | Agent-native RAG engine that grew into context infrastructure. |
| [VectifyAI/PageIndex](https://github.com/VectifyAI/PageIndex) | Python | 39,962 | "Vectorless," reasoning-based RAG document index. Another data point in the anti-vector-store trend. |
| [StarTrail-org/LEANN](https://github.com/StarTrail-org/LEANN) | Python | 13,760 | MLsys2026 Best Paper — 97% storage savings for private, on-device RAG. |
| [volcengine/OpenViking](https://github.com/volcengine/OpenViking) | Python | 41,426 | Self-evolving context database unifying agent memory, knowledge RAG and skills. |

---

## 3. Trend Signal Analysis

The dominant theme today is **agent context and memory as first-class infrastructure**. `claude-mem` (+1,803 today) and the broader cluster of `mem0`, `cognee`, `OpenViking`, and `headroom` show the community moving past "build an agent" toward "make an agent remember and stay cheap." The second explosive category is **agent harness tuning and skills** (`ECC` at 296k stars, `mattpocock/skills`, `Claude-Code-Game-Studios`), reflecting that Claude Code / Codex / Cursor are now ecosystems people configure as vigorously as they code.

A genuinely new direction is the **"vectorless" RAG** turn: `graphify` (knowledge graphs) and `PageIndex` (reasoning-based document indexing) explicitly reject vector stores, suggesting fatigue with embedding pipelines. Simultaneously, **token-efficiency hacks** (`heretic` 65% token cuts via "caveman" prompting, `headroom` compression) are treating context as a scarce budget.

On stacks, **Rust and C** keep encroaching on the AI stack (`agent-desktop`, `colibri`, `rig`, `Codewhale`) for performance and hardware-efficiency reasons, while `FreeToken`, `nanochat`, and `picollm` push the **local/edge inference** wave. Finally, **vertical foundation models** (`Kronos` for finance) and a **cluster of financial agents** (`Vibe-Trading`, `daily_stock_analysis`) signal that domain-specific AI is the next frontier beyond generic assistants.

---

## 4. Community Hot Spots

- **Agent memory & context layers** — `claude-mem` and `mem0` are the fastest way to add persistence; expect rapid standardization here. ([link](https://github.com/thedotmack/claude-mem), [link](https://github.com/mem0ai/mem0))
- **Vectorless RAG** — `graphify` and `PageIndex` challenge the default vector-DB assumption; worth prototyping if you're wrestling with embedding cost/latency. ([link](https://github.com/Graphify-Labs/graphify), [link](https://github.com/VectifyAI/PageIndex))
- **Agent harness optimization / skills** — `ECC` and `Claude-Code-Game-Studios` show the meta of tuning coding agents is itself a product category. ([link](https://github.com/affaan-m/ECC), [link](https://github.com/Donchitos/Claude-Code-Game-Studios))
- **Efficient local/MoE inference** — `colibri` and `FreeToken` bring frontier-scale serving to commodity hardware; watch for production adoption. ([link](https://github.com/JustVugg/colibri), [link](https://github.com/FlashML-org/FreeToken))
- **Token-efficiency tooling** — `headroom` and `heretic` attack the cost of context, a pragmatic trend with immediate ROI for agent builders. ([link](https://github.com/headroomlabs-ai/headroom), [link](https://github.com/p-e-w/heretic))

---
*This digest is auto-generated by [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar).*