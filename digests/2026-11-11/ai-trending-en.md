# AI Open Source Trends 2026-11-11

> Sources: GitHub Trending + GitHub Search API | Generated: 2026-11-10 23:14 UTC

---

# AI Open Source Trends Report — 2026-11-11

## 1. Today's Highlights

Today's GitHub AI activity is dominated by the **agent skills & harness ecosystem**: Anthropic's `skills` (+1,676) and `claude-quickstarts` (+530), plus third-party skill libraries from Addy Osmani (+465) and Matt Pocock (+1,223), signal that "Agent Skills" is crystallizing into a shared open format. The **agent infrastructure** layer is equally hot — Vercel's `agent-browser` (+775) and the Home Assistant MCP bridge (+792) show agents reaching into browsers and smart homes. Meanwhile, `meta/astryx` (+3,111 today) tops all newcomers by framing a design system as "agent ready," and `billion-context` (+374) targets the practical pain of long-session context windows. The longer-tail topic data confirms a **memory/context-compression surge** (`claude-mem`, `headroom`, `billion-context`, `cognee`), with Rust continuing to gain ground in inference and agent tooling.

---

## 2. Top Projects by Category

### 🔧 AI Infrastructure

| Project | Lang | Stars (total / today) | Summary |
| :--- | :--- | ---: | :--- |
| [vercel-labs/agent-browser](https://github.com/vercel-labs/agent-browser) | Rust | 0 (+775) | A browser-automation CLI purpose-built for AI agents, written in Rust. Its debut on trending shows the agent-tooling layer is being rebuilt in systems languages for speed and reliability. |
| [google-deepmind/mujoco](https://github.com/google-deepmind/mujoco) | C++ | 0 (+453) | DeepMind's general-purpose physics simulator for multi-joint dynamics with contact. Continued trending reflects sustained momentum in embodied-AI and robotics simulation. |
| [anthropics/claude-quickstarts](https://github.com/anthropics/claude-quickstarts) | Python | 0 (+530) | Starter projects for building deployable apps with the Claude API. It's the fastest onboarding path for developers entering Anthropic's agent stack. |
| [0xPlaygrounds/rig](https://github.com/0xPlaygrounds/rig) | Rust | 9,111 | A modular, scalable framework for building LLM applications in Rust. It's a leading signal that Rust is becoming a first-class LLM app language. |
| [skyzh/tiny-llm](https://github.com/skyzh/tiny-llm) | Python | 5,711 | A from-scratch "tiny vLLM + Qwen" inference system aimed at systems engineers on Apple Silicon. It demystifies the inference stack for a growing audience. |
| [Picovoice/picollm](https://github.com/Picovoice/picollm) | Python | 319 | On-device LLM inference powered by X-bit quantization. Represents the push toward local, edge-friendly model deployment. |
| [samchon/nestia](https://github.com/samchon/nestia) | TypeScript | 2,181 | NestJS helper plus AI chatbot development scaffolding. Bridges mainstream web backend frameworks with agent features. |
| [codewhale-hq/Codewhale](https://github.com/codewhale-hq/Codewhale) | Rust | 41,173 | An open-source terminal agent written in Rust supporting any provider or model. A notable Rust-native coding-agent contender. |

### 🤖 AI Agents / Workflows

| Project | Lang | Stars (total / today) | Summary |
| :--- | :--- | ---: | :--- |
| [anthropics/skills](https://github.com/anthropics/skills) | Python | 0 (+1,676) | The public repository for Agent Skills — reusable, composable capabilities for agents. Its traction signals that "skills" are becoming a portable, standardized agent primitive. |
| [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills) | JavaScript | 0 (+465) | Production-grade engineering skills for AI coding agents from a high-profile practitioner. Reinforces the community-driven skill-library trend. |
| [mattpocock/skills](https://github.com/mattpocock/skills) | Shell | 0 (+1,223) | "Skills for Real Engineers," published straight from the author's `.agents` directory. Its rapid climb shows skills are being shared like dotfiles. |
| [homeassistant-ai/ha-mcp](https://github.com/homeassistant-ai/ha-mcp) | Python | 0 (+792) | Configure and automate Home Assistant with Claude, ChatGPT, and other assistants via MCP. A strong example of agents extending into physical smart-home control. |
| [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent) | Python | 262,434 | "The agent that grows with you." Enormous star base makes it one of the most-followed agent projects in the ecosystem. |
| [CopilotKit/CopilotKit](https://github.com/CopilotKit/CopilotKit) | TypeScript | 38,612 | The frontend stack for agents and generative UI, and maker of the AG-UI Protocol. It's defining how agents surface in real product UIs. |
| [HKUDS/nanobot](https://github.com/HKUDS/nanobot) | Python | 49,996 | A lightweight open-source personal agent. Represents the "small, local personal agent" wave. |
| [zhayujie/CowAgent](https://github.com/zhayujie/CowAgent) | Python | 47,871 | A personal AI assistant and agent harness with multi-agent, multi-model, and memory. One-line install lowers the barrier to self-hosted agents. |

### 📦 AI Applications

| Project | Lang | Stars (total / today) | Summary |
| :--- | :--- | ---: | :--- |
| [meta/astryx](https://github.com/meta/astryx) | TypeScript | 0 (+3,111) | Meta's fully customizable, "agent-ready" open-source design system. It led all of today's trending with explosive first-day stars, hinting at a new frontend category: UI systems designed for agents. |
| [ranxianglei/billion-context](https://github.com/ranxianglei/billion-context) | TypeScript | 0 (+374) | A context-compression plugin for month-long sessions, small windows, and ~5x token savings. Directly addresses the cost/context pain of long-running agent sessions. |
| [hugohe3/ppt-master](https://github.com/hugohe3/ppt-master) | Python | 75,632 | AI generates native PowerPoint decks with real shapes, transitions, charts, and narration. A high-visibility vertical AI productivity app. |
| [career-ops-hq/career-ops](https://github.com/career-ops-hq/career-ops) | JavaScript | 81,734 | An open-source AI job-search agent that scores jobs against your CV and tailors applications. Shows AI agents moving into high-volume personal workflows. |
| [ZhuLinsen/daily_stock_analysis](https://github.com/ZhuLinsen/daily_stock_analysis) | Python | 69,545 | An LLM-powered multi-market stock analysis system with dashboards and alerts. A strong finance-vertical agent application. |
| [harry0703/MoneyPrinterTurbo](https://github.com/harry0703/MoneyPrinterTurbo) | Python | 141,970 | Generates HD short videos from a topic via an automated AI workflow. Continues to be one of the most-starred consumer AI apps. |
| [HKUDS/DeepTutor](https://github.com/HKUDS/DeepTutor) | Python | 57,973 | Lifelong personalized tutoring powered by agents. Represents the education vertical's maturing AI offerings. |

### 🧠 LLMs / Training

| Project | Lang | Stars (total / today) | Summary |
| :--- | :--- | ---: | :--- |
| [huggingface/transformers](https://github.com/huggingface/transformers) | Python | 168,753 | The de facto model-definition framework for text, vision, audio, and multimodal models. Remains the backbone of the open model ecosystem. |
| [rasbt/LLMs-from-scratch](https://github.com/rasbt/LLMs-from-scratch) | Jupyter Notebook | 108,196 | Build a ChatGPT-like LLM in PyTorch step by step. A perennial favorite reflecting durable demand for learning LLM internals. |
| [open-compass/opencompass](https://github.com/open-compass/opencompass) | Python | 7,583 | An LLM evaluation platform spanning major providers across 100+ datasets. Evaluation is increasingly critical as model variety explodes. |
| [ollama/ollama](https://github.com/ollama/ollama) | Go | 186,572 | Run Kimi, GLM, MiniMax, DeepSeek, gpt-oss, Qwen, and more locally. The default gateway to open-weight models. |
| [AkaliKong/MiniOneRec](https://github.com/AkaliKong/MiniOneRec) | Python | 1,964 | A minimal reproduction of the OneRec recommendation model. Signals growing interest in LLM-based recommendation systems. |
| [llm-jp/awesome-japanese-llm](https://github.com/llm-jp/awesome-japanese-llm) | TypeScript | 1,446 | An overview of Japanese LLMs. Reflects the continuing regionalization of open models. |

### 🔍 RAG / Knowledge

| Project | Lang | Stars (total / today) | Summary |
| :--- | :--- | ---: | :--- |
| [thedotmack/claude-mem](https://github.com/thedotmack/claude-mem) | TypeScript | 115,798 | Persistent cross-session context for every agent, compressing session activity and re-injecting relevant memory. A flagship of the agent-memory wave. |
| [Graphify-Labs/graphify](https://github.com/Graphify-Labs/graphify) | Python | 145,625 | Turns any codebase and docs into a queryable knowledge graph with no vector store. Its "graph over vectors" approach is a notable RAG alternative. |
| [headroomlabs-ai/headroom](https://github.com/headroomlabs-ai/headroom) | Python | 79,314 | Compresses tool outputs, logs, and RAG chunks before they hit the LLM. Token-efficiency tooling is clearly in demand. |
| [mem0ai/mem0](https://github.com/mem0ai/mem0) | Python | 68,768 | A drop-in memory layer for production AI agents. One of the most-starred memory-infrastructure projects. |
| [infiniflow/ragflow](https://github.com/infiniflow/ragflow) | Go | 93,969 | An agent-native database that grew from RAG into context infrastructure. Shows RAG engines evolving toward agent context platforms. |
| [milvus-io/milvus](https://github.com/milvus-io/milvus) | Go | 46,632 | A high-performance, cloud-native vector database for scalable ANN search. A mainstay of the retrieval stack. |
| [qdrant/qdrant](https://github.com/qdrant/qdrant) | Rust | 35,502 | A high-performance, massive-scale vector database and search engine. Rust-based vector search keeps gaining favor. |
| [VectifyAI/PageIndex](https://github.com/VectifyAI/PageIndex) | Python | 40,350 | A document index for "vectorless," reasoning-based RAG. Exemplifies the emerging non-vector retrieval direction. |

---

## 3. Trend Signal Analysis

The clearest explosion today is around **agent skills and agent harnesses**. Anthropic's `skills` repo plus two independent practitioner skill libraries (Addy Osmani, Matt Pocock) all hit trending simultaneously — a strong sign that "Agent Skills" is becoming a de facto, shareable unit of capability, much like prompts were a few years ago. This is reinforced by `ECC` (star count 300,778) and the broader cluster of harness/optimization systems, indicating the community is now optimizing *how agents run*, not just which model they call.

A second dominant theme is **memory and context efficiency**. `claude-mem`, `headroom`, `billion-context`, `mem0`, and `cognee` collectively represent a new "context compression / persistent memory" sublayer. As agents run longer sessions, token savings (5x fewer tokens, 60–95% on JSON) and cross-session recall have become concrete product needs rather than research ideas.

**New tech stacks/directions:** Rust keeps appearing across infrastructure (agent-browser, Codewhale, qdrant, zvec, lancedb), suggesting performance-sensitive AI tooling is migrating to systems languages. "Vectorless" retrieval (`PageIndex`, `graphify`) is a genuinely novel direction challenging the default vector-DB assumption. Agents also reached into the physical world via Home Assistant MCP and into frontend UI via `astryx`'s "agent-ready" design system and CopilotKit's AG-UI protocol.

**Tie to recent LLM releases:** The `ollama` listing (Kimi, GLM, MiniMax, DeepSeek, gpt-oss, Qwen) reflects a crowded multi-provider open-weight landscape driving demand for evaluation (`opencompass`), local inference (`picollm`), and portable skills that work across providers. The overall picture: the ecosystem is shifting from *model access* to *agent runtime, memory, and interoperability*.

---

## 4. Community Hot Spots

- **Agent Skills standardization** — [anthropics/skills](https://github.com/anthropics/skills), [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills), [mattpocock/skills](https://github.com/mattpocock/skills): three independent skill libraries trending at once suggests an emerging portable skill format worth building against early.
- **Agent memory & context compression** — [thedotmack/claude-mem](https://github.com/thedotmack/claude-mem), [headroomlabs-ai/headroom](https://github.com/headroomlabs-ai/headroom), [mem0ai/mem0](https://github.com/mem0ai/mem0): the most commercially acute pain point for production agents, with clear token/cost savings claims to validate.
- **Browser & device control for agents** — [vercel-labs/agent-browser](https://github.com/vercel-labs/agent-browser), [browser-use/browser-use](https://github.com/browser-use/browser-use), [homeassistant-ai/ha-mcp](https://github.com/homeassistant-ai/ha-mcp): agents moving from chat into real tools, browsers, and smart homes.
- **Rust-native AI infrastructure** — [0xPlaygrounds/rig](https://github.com/0xPlaygrounds/rig), [codewhale-hq/Codewhale](https://github.com/codewhale-hq/Codewhale), [qdrant/qdrant](https://github.com/qdrant/qdrant): a consistent stack signal for performance-critical LLM and vector workloads.
- **Vectorless / graph-based retrieval** — [Graphify-Labs/graphify](https://github.com/Graphify-Labs/graphify), [VectifyAI/PageIndex](https://github.com/VectifyAI/PageIndex): an early but notable challenge to vector-store defaults in RAG architecture.

---
*This digest is auto-generated by [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar).*