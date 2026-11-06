# AI 基础设施日报 2026-11-07

> 生成时间: 2026-11-06 23:16 UTC | 覆盖项目: 6 个

- [vLLM](https://github.com/vllm-project/vllm)
- [SGLang](https://github.com/sgl-project/sglang)
- [llama.cpp](https://github.com/ggml-org/llama.cpp)
- [Ollama](https://github.com/ollama/ollama)
- [LiteLLM](https://github.com/BerriAI/litellm)
- [Unsloth](https://github.com/unslothai/unsloth)

---

## 横向对比

# 跨项目对比报告 — AI 基础设施文摘，2026-11-07

## 1. 生态概览

整个生态正处于 **Kimi-K3.1 与 DeepSeek-V4 bring-up 浪潮**的中段：推理引擎（vLLM、SGLang）与本地运行时层（llama.cpp）都在同一个 24 小时窗口内落地或评审同一批架构的 Day-0 支持，这已成为事实上的竞争战场。今天的主旋律是正确性而非吞吐量——vLLM 有多个未关闭的静默 KV 损坏和长上下文崩溃回归，SGLang 的 FP8 KV scale 被静默忽略，llama.cpp 的 spec-decode 在量化目标上出现偏差。网关层（LiteLLM）处于 LTS 回填模式，持续吸收 Anthropic↔OpenAI 转换保真度相关的 bug；而微调/桌面层（Unsloth）正在发布一个以性能为重点的 beta，但其 RAG 与多 GPU diffusion 流水线仍不成熟。

## 2. 活跃度对比

| 项目 | 浮现 issue 数（24h） | 浮现 PR 数（24h） | 发布状态 | 信号 |
|---|---|---|---|---|
| **vLLM** | ~15（8 个未关闭回归已排序） | ~12（含 5 部分 Kimi 栈） | 无发布；v0.32.0 存在未关闭的正确性回归 | 最高严重级别 bug 负载；大量模型 bring-up |
| **SGLang** | ~10（5 个新稳定性项） | ~15（500 已更新，~10 详细） | 无发布 | 大型 PR 队列；Day-0 Kimi 工作占主导 |
| **llama.cpp** | ~14 个未关闭回归 | ~15（10+ 个构建 b12167–b12179） | **10+ 个构建已落地** | 最高构建节奏；最广平台覆盖 |
| **Ollama** | ~10 | ~10（含上游 bump 至 b12177） | 无发布；0.40.x RC 反复变动 | 基于新 llama-server 基座的运行时稳定化 |
| **LiteLLM** | ~12 | ~12（LTS cherry-pick + 4 个修复 PR） | **v1.108.1 + v1.108.2 LTS** | 生产分支向 1.108 LTS 收敛 |
| **Unsloth** | ~10 | ~15（Studio/Desktop 为主） | **v0.1.916-beta** | 功能迭代快；稳定性债务累积 |

**解读：** vLLM 承载最多*严重*未关闭回归；llama.cpp 发布节奏最高；LiteLLM 是唯一积极发布补丁版本的项目，符合网关作为“无聊、必须正确”层的定位；Unsloth 当前功能优先、稳定性其次。

## 3. 模型支持竞赛

**Kimi-K3.1 是共识目标，vLLM + SGLang 实际上在前沿并驾齐驱，llama.cpp 以更窄范围紧随第三。**

- **vLLM**：5 部分堆叠 PR（#65685/#66037/#66027）覆盖 TP8 NVIDIA decode、ROCm、P/D 分离、sparse-MLA DCP。第 1/5 部分已合并；第 3–5 部分评审中。硬件/功能面比任何竞品都广。
- **SGLang**：单一整合 Day-0 PR #48414 打包 DSA + DCP、PD 分离、DFlash 投机解码、PP prefill、HiSparse、融合 kernel。单 PR 打包的合并风险更高，但落地更干净。
- **llama.cpp**：Kimi-K3.1-Pro **已合并**（#31779）——DSA/MLA 混合、top-k mask 复用。在两个推理引擎完成各自栈*之前*就已合并，但范围更窄（纯文本、单节点）的本地运行时面。
- **Ollama**：追踪上游（bump 至 b12177），自身不驱动 Kimi 支持。
- **LiteLLM**：无模型 bring-up，但 Gemini 目录同步（#50468）与 Vertex Claude 合作模型序列化工作（#49645）显示其跟踪的是模型目录漂移而非架构。

**次级竞赛：** DeepSeek-V4 流水线并行（vLLM #65766 vs SGLang 的 prefill/verify kernel 工作）——vLLM 在*结构性* PP 支持上领先；SGLang 在*内核级* V4 优化上领先。NVFP4 是双雄竞赛：vLLM #63203（面向 MLA 的 NVFP4 KV cache，每 token 约减少 39%）远超 llama.cpp 已关闭的 NVFP4 PR（#23572，声称 40% 提升，未落地）。

**结论：** vLLM 在广度上领先（多硬件、P/D、KV dtype），SGLang 在单 PR 功能完整性上领先，llama.cpp 在纯文本 Kimi 的*合并速度*上领先。

## 4. 性能前沿

优化工作集中在四个带，分布不均：

1. **KV cache 压缩** — 最具结构性的工作。vLLM 的 NVFP4 MLA KV（352 vs 576 B/token）是头条；SGLang 的 FP8 KV *正确性* bug（#48749）是警示性对照——量化被推以降低占用，而 scale 应用正确性仍在出问题。llama.cpp 的 ClassLRU MoE expert cache（#31233）在运行时层解决同一个“更少内存装更多”的问题。
2. **分布式服务 / P/D 与 PP** — vLLM 领先：面向 MegaMoE 的 DeepSeek-V4 SP+PP、sparse MLA DCP、MoE DP padding skip。但它也承担相应风险：NixlConnector 静默 KV 损坏（#61229）与 GLM-5.3 P/D 描述符提交瓶颈（#55434）——**描述符提交而非 fabric 带宽**正成为真正的 P/D 扩展极限。
3. **Kernel 融合** — SGLang 最激进（Gemma-4 TP all-reduce 与 epilogue 融合、Anima 融合 residual 每请求减少 8,400 次 launch、DeepSeek-V4 compact verify）。vLLM 以 Kimi K3 单 packed prefill 卷积和 ROCm FP8 MFMA 回应。内核级收益现在往往是*launch 次数*的减少，而非 FLOPs。
4. **量化策略** — 从一刀切转向 per-tensor：Ollama #18078 明确回退一刀切 NVFP4 以保长生成质量；LiteLLM 的 token 计数 #48830 提升预算预留准确性，这是一个讨论不足的成本维度。

**横切信号：** batch 不变性与可复现性正上升为一等关注点（vLLM Marlin MoE 排序 #52525、batch 不变模式 over-routing #65569、BYOG RFC #61698）——直接关系到 RL 训练循环与评测 harness。

## 5. 层次定位

| 层 | 项目 | 当前特征性故障模式 |
|---|---|---|
| **推理引擎（数据中心）** | vLLM、SGLang | 静默 KV 损坏、P/D 正确性、长上下文崩溃；高影响面 |
| **本地运行时（边缘/桌面）** | llama.cpp、Ollama | 平台特定崩溃（Vulkan/Metal/SYCL）、spec-decode 偏差、RAM-cache 跨租户串扰 |
| **网关 / 代理** | LiteLLM | 转换保真度、计费完整性、缓存投毒；正确性 bug 是*财务性*或*多租户*的 |
| **训练 / 微调 / Studio** | Unsloth | 静默 Trainer 行为变更、batch-1 RAG 吞吐、多 GPU diffusion 断言 |

各层有截然不同的**发布哲学**：推理引擎发得快、回归也重（vLLM v0.32.0 隐含为“避免”版本）；llama.cpp 以每构建粒度持续发布；LiteLLM 跑 LTS 分支因为网关必须无聊；Unsloth 发布带 beta 标签的功能并回填稳定性。

值得注意的是，**边界正在模糊**：Unsloth Studio 正成为面向编码 agent（Claude Agent、Codex、Hermes MCP）的本地 LLM 网关，而 Ollama 的底层引擎现在是 llama.cpp 的 server。“网关”功能同时出现在训练 IDE 与本地运行时内部。

## 6. 趋势信号

**行业在做什么：**
- **Day-0 模型支持就是产品。** Kimi-K3.1 与 DeepSeek-V4 支持在四个层同时竞速——模型发布节奏现在决定工程路线图。
- **量化正进入正确性债务阶段。** FP8 KV scale 静默（SGLang）、int8 KV 损坏（vLLM）、per-tensor NVFP4 回退（Ollama）、量化目标上的 spec-decode 偏差（llama.cpp）——今天*每个*项目都有未关闭的量化正确性问题。
- **P/D 分离是扩展断裂之处。** 描述符开销、lease 过期竞态、静默 KV 损坏——三类不同故障都专门在分离式服务中浮现。
- **Agentic 负载正在暴露协议保真度。** Responses API `previous_response_id` 上下文丢失（vLLM）、`server_tool_use` 误呈现与 thinking-signature 重放（LiteLLM）、`</think>` 解析器截断（SGLang）、GLM-5.3 多工具退化（SGLang）——多轮工具使用是当今最棘手的正确性面。
- **可复现性正成为一等要求**，面向评测与 RL，驱动 vLLM 的 batch 不变性工作。

**agent/应用开发者应关注：**
1. **显式固定推理版本。** 将 vLLM v0.32.0 视为已知风险版本（Qwen 混合崩溃、hybrid+PP 非法内存）；受影响负载回退到 v0.31.1。
2. **验证输出，而不只是 HTTP 200。** 分离式服务中的静默 KV 损坏是最糟故障模式；为 P/D 部署加输出级断言。
3. **不要轻信量化权重上的 spec-decode** 用于确定性敏感流水线（llama.cpp #25618、Ollama cuda_v13 grammar 崩溃 #17026）。
4. **显式设置 `max_tokens` 并将截断流视为错误** 在 Anthropic 后端与 Studio 路由上（Unsloth #16953、#16867）。
5. **在 `/v1/messages` 上禁用响应缓存** 并独立监控花费，直到 LiteLLM 预算与缓存投毒修复落地（#50469、#50057）。
6. **关注 NVFP4 KV cache PR（#63203）** 作为 MLA 类模型上下文长度/并发度的最大单一杠杆——并鉴于本轮量化的往绩，采用前按模型基准测试。

**底线：** 基础设施正被同步的模型代际浪潮压力测试，系统性风险已从分离式服务、量化 KV 与 agent 协议转换中的*吞吐量*转向*静默正确性*。规划 2027 容量的工程师应为输出验证基础设施编预算，而不只是更快的 kernel。

---

## 各项目详细报告

<details>
<summary><strong>vLLM</strong> — <a href="https://github.com/vllm-project/vllm">vllm-project/vllm</a></summary>

# vLLM 摘要 — 2026-11-07

## 1. 今日亮点

过去 24 小时无新版本发布，但 issue 和 PR 活动频繁。最大主题是 **Kimi K3.1 模型适配**（一个由 5 部分组成的堆叠 PR 系列，处于不同合并阶段），同时 **DeepSeek-V4 流水线并行修复**持续推进。**v0.32.0 中的一批正确性回归**——Qwen3.8/Qwen4Exp 混合模型崩溃以及前缀缓存下的 KV 缓存损坏——占据了 bug 报告的主流，而 **Rust 前端**作为 Python API 服务器的可选替代方案，正逐步接近功能对等。

## 2. 版本发布与破坏性变更

*过去 24 小时无新版本发布。* 有一个值得关注的配置面变更 PR：

- **[PR #64765](https://github.com/vllm-project/vllm/pull/64765) — flashinfer/b12x 打包变更。** `flashinfer-python` ≥0.7.2 现在将 `b12x` 作为顶层包发布，因此 vLLM 移除了独立的 `b12x==1.5.1` extra。依赖面有小幅变化；普通用户无需迁移，但如果你固定了 `b12x` extra，请核实。

## 3. 新模型与硬件支持

- **[PR #65685](https://github.com/vllm-project/vllm/pull/65685) / [#66037](https://github.com/vllm-project/vllm/pull/66037) / [#66027](https://github.com/vllm-project/vllm/pull/66027) — Kimi K3.1 Pro（5 部分堆叠）。** 第 1/5 部分已合并；第 3–5 部分在审查中。涵盖 NVIDIA 专用解码、TP8 服务下的 KV 缓存 + 注意力变更、ROCm 路径、P/D 分离以及稀疏 MLA DCP（解码上下文并行）优化。这是当前主要的模型适配工作流。
- **[PR #63203](https://github.com/vllm-project/vllm/pull/63203) — 面向稠密 MLA 的 NVFP4 KV 缓存。** 在 `FLASHINFER_MLA` 上为 DeepSeek-V3.x 和 Kimi-K3 新增 `--kv-cache-dtype nvfp4`：**352 B/token，相比 FP8 的 576 B/token**（512 潜在维度 NVFP4 + 64 RoPE 维度 FP8）。使用 FlashInfer Cake 解码内核。
- **[PR #65766](https://github.com/vllm-project/vllm/pull/65766) — DeepSeek-V4 序列并行 + MegaMoE 流水线并行。** 移除了在多级流水线上禁用 SP 的 PP=1 限制。
- **[Issue #47455](https://github.com/vllm-project/vllm/issues/47455) — Nemotron 3.5 ASR Streaming (0.6B) 功能请求。** 针对 `nemotron-3.5-asr-streaming-0.6b` 的开放请求；尚未实现。

## 4. 性能与优化

- **[PR #65972](https://github.com/vllm-project/vllm/pull/65972) — MoE DP 填充跳过。** 在数据并行下，DP rank 会填充至最大批次；此 PR 在 Blackwell 上跳过 FlashInfer TRT-LLM 单体 MoE 中的填充行，避免浪费的专家计算。
- **[PR #64817](https://github.com/vllm-project/vllm/pull/64817) — Kimi K3 单一打包预填充卷积。** 在打包的 q|k|v 投影上用一次卷积替代三次逐视图卷积；减少 FlashKDA 之前的内存物化。
- **[PR #66027](https://github.com/vllm-project/vllm/pull/66027) — 稀疏 MLA DCP 优化**（打包 top-k 合并、直接 A2A、DCP ShardQ）——可选启用，仅在解码上下文并行下应用。
- **[PR #65410](https://github.com/vllm-project/vllm/pull/65410) — ROCm Step5 原生 FP8 MFMA**，用于 gfx942（MI300X/MI325X）上的预填充索引器评分。
- **[Issue #63705](https://github.com/vllm-project/vllm/issues/63705) — MiniMax-M2.7 吞吐量回归**，在 TP4+EP 上跨 v0.29/0.31/0.32 相比早期版本报告——仍开放，如果你服务 M2.7 值得关注。
- **[Issue #58624](https://github.com/vllm-project/vllm/issues/58624) — MoE 解码在 SM12x 上慢约 15%**，自 #56876（DeepGEMM 连续布局对齐 64→128）以来。开放回归。
- **[Issue #55434](https://github.com/vllm-project/vllm/issues/55434) — GLM-5.3 P/D 在 GB200 上。** NIXL 每次 TP-rank 传输提交 **约 91k–120k 个 KV 描述符**；描述符提交（而非 fabric 带宽）是瓶颈，使得此工作负载下 MNNVL/cuda_ipc 比 RDMA 更慢。
- **[Issue #65569](https://github.com/vllm-project/vllm/issues/65569) — Hopper/Blackwell 上的批次不变模式**不必要地将 Linear 层路由到 Triton（在这些设置下 cuBLAS 已是批次不变的）。

## 5. 稳定性与回归

按严重程度排序：

1. **🔴 KV 缓存损坏（静默错误输出）** — **[Issue #61229](https://github.com/vllm-project/vllm/issues/61229)**（NixlConnector，1P1D）：KV 块在远程 READ 进行中时因租约到期被释放；解码消费了被覆盖的 KV。分离式服务中的静默正确性故障。未列出修复 PR。
2. **🔴 长上下文引擎崩溃** — **[Issue #62383](https://github.com/vllm-project/vllm/issues/62383)**（Qwen4Exp / Qwen3.8-Flash-Next）：单次预填充跨越 262,144 token 原生边界 → CUDA 非法内存访问。**v0.32.0 和 nightly 受影响；v0.31.1 正常。**
3. **🔴 混合+PP 中的非法内存访问** — **[Issue #63505](https://github.com/vllm-project/vllm/issues/63505)**（Qwen3.8-27B GDN 混合，PP=3，sm_86）：启用前缀缓存时 Xid 31，不启用时输出乱码。自 v0.28.0 起回归，存在于 v0.31/v0.32。
4. **🟠 TP 挂起 / EngineDeadError** — **[Issue #41530](https://github.com/vllm-project/vllm/issues/41530)**（DeepSeek-V4-Pro，TP=8，MTP 投机解码）：TP worker 上 `sample_tokens` 的 RPC 超时。长期存在（自 5 月起），仍开放。
5. **🟠 投机解码 + 结构化输出 500 错误** — **[PR #66081](https://github.com/vllm-project/vllm/pull/66081)** 提出修复：在异步调度下，EngineCore 在计算语法位掩码之前无法获取真实草稿 token ID。**修复 PR 已存在。**
6. **🟠 int8_per_token_head KV 损坏** — **[Issue #50702](https://github.com/vllm-project/vllm/issues/50702)** / **[#50749](https://github.com/vllm-project/vllm/issues/50749)**（Gemma-4 混合，Triton）：启用前缀缓存时输出损坏 / 不启用时首 token 损坏。**[Issue #63944](https://github.com/vllm-project/vllm/issues/63944) 已关闭**——scale-view 填充覆盖 Gated DeltaNet 状态（修复已落地）。
7. **🟠 解析器正确性** — **[Issue #43244](https://github.com/vllm-project/vllm/issues/43244)**：Responses API 中 `previous_response_id` 从存储上下文中丢弃 `function_call`/`function_call_output`——对多轮工具 agent 有影响。
8. **🟡 混合 + LoRA NaN 输出** — **[Issue #52568](https://github.com/vllm-project/vllm/issues/52568)**（Qwen3.5-9B GDN + 动态 LoRA，H20，长序列）：Punica Triton 内核 bug。
9. **🟡 Marlin MoE 批次依赖** — **[Issue #52525](https://github.com/vllm-project/vllm/issues/52525)**：输出依赖专家内 token 排序，破坏批次不变性。
10. **🟡 ROCm MTP 回归** — **[Issue #52833](https://github.com/vllm-project/vllm/issues/52833) 已关闭**（GLM-5.2 MTP，MI355X/gfx950）：0% 草稿接受率。

## 6. 对应用开发者的意义

- **有意识地固定版本。** v0.32.0 有多个开放的正确性回归（长上下文 Qwen4Exp 崩溃，混合+PP 非法内存访问）。如果你服务 Qwen3.8/4Exp 或混合注意力 DeepSeek 级模型，在 #62383 和 #63505 关闭之前，**v0.31.1 是更安全的默认选择**。
- **分离式服务需要仔细审查。** NixlConnector 租约到期 bug（#61229）导致*静默*错误输出，而非崩溃——最糟糕的故障模式。如果你运行 P/D 分离，请端到端验证输出，而非信任请求成功。
- **Agent 开发者：** Responses API 上下文 bug（#43244）会破坏依赖 `previous_response_id` 获取函数调用历史的多轮工具循环。投机解码 + 结构化输出（JSON schema）目前在异步调度下返回 500——修复在审查中（#66081）；合并之前，考虑为语法约束端点禁用投机解码。
- **KV 缓存占用可能显著下降。** MLA 模型的 NVFP4 KV（#63203）每 token 比 FP8 小约 39%——这是 DeepSeek-V3.x/Kimi 服务中上下文长度或并发的实际杠杆。
- **Rust 前端正在稳定但尚未完成。** 路线图 #44280 跟踪功能对等；`VLLM_USE_RUST_FRONTEND=1` 仍为实验性。暂不要将生产 API 服务器架构建立在其上。
- **关注批次不变性工作。** 批次不变配置哈希重构（#39479，已关闭）和 Marlin MoE 排序 bug（#52525）在你依赖跨批次大小可复现输出（评估、回归测试、RL 训练循环）时很重要。BYOG 可复现基准 RFC（#61698）是社区对此收紧的回应。

</details>

<details>
<summary><strong>SGLang</strong> — <a href="https://github.com/sgl-project/sglang">sgl-project/sglang</a></summary>

# SGLang 摘要 — 2026-11-07

## 1. 今日亮点
过去 24 小时内没有新版本发布，但 PR 队列非常活跃（500 个更新）。最重要的进行中工作是针对 **Kimi-K3.1** 的 Day-0 支持（PR #48414）以及一组 DeepSeek-V4 prefill/verify 内核优化。在稳定性方面，有人提交了一个严重的正确性问题：FP8 KV-cache scales 在多个模型系列中被静默忽略（#48749），另外新注册的一个 diffusion 测试在 main 上失败（#48892）。

## 2. 发布与破坏性变更
过去 24 小时内无。

## 3. 新模型与硬件支持
- **Kimi-K3.1** — Day-0 服务栈正在评审中，包括带 decode context parallelism 的 DSA、PD 分离、DFlash 投机解码、PP prefill、HiSparse 以及融合内核（[#48414](https://github.com/sgl-project/sglang/pull/48414)）。
- **面向昇腾的 MindIE-SD** — RFC 提议将 MindIE-SD 集成为 SGLang Diffusion 的可选加速库（dense quantized + sparse attention），定位类似于 NVIDIA 上的 FlashInfer（[#48582](https://github.com/sgl-project/sglang/issues/48582)）。
- **Apple Silicon** — 关于 Torch 自有 SRT 路径并导出整模型 MLX 区域的 RFC 正在推进（[#32321](https://github.com/sgl-project/sglang/issues/32321)）。
- **NPU / Diffusion** — Wan2.2-T2V-A14B W4A4F4 DAOS 及 MXFP4 attention 正在评审中（[#43367](https://github.com/sgl-project/sglang/pull/43367)）。
- **Gemma-4 / Nemotron Bolt E4B** — 针对共享 KV-cache 模型的可断点 prefill CUDA graph 修复（[#46663](https://github.com/sgl-project/sglang/pull/46663)）。

## 4. 性能与优化
- **DeepSeek-V4 compact verify** — 将 ratio-1/2 压缩器改为走融合内核，而非串联的小型 torch 内核（[#48832](https://github.com/sgl-project/sglang/pull/48832)）；`wo_b`/Engram KV MXFP8 GEMM 在 CC 10.7 且行数超过 8K 时路由至 cuDNN（[#48971](https://github.com/sgl-project/sglang/pull/48971)）。
- **Gemma-4 TP 融合** — all-reduce 与层 epilogue 及每 128 个元素的 FP8 量化在 NVLink multicast 上融合；在 TP4/H200 下每个 dense 层减少一次 NCCL all-reduce 和三个 epilogue 内核（[#48563](https://github.com/sgl-project/sglang/pull/48563)）。
- **Diffusion（Anima）** — 融合 gated residuals，每个 50 步 CFG 请求减少 8,400 次内核启动（[#48394](https://github.com/sgl-project/sglang/pull/48394)）。
- **Mooncake L3** — 并行化逐层 direct-linker 加载，解决约 12–14 GB/s 的串行 ranged-op 吞吐问题（[#45106](https://github.com/sgl-project/sglang/pull/45106)）。
- **权重缓存守护进程** — 路线图报告称，在 Qwen3-235B FP8 上通过 CUDA IPC 将加载时间从约 306–327 秒降至 **<1 秒**（[#33522](https://github.com/sgl-project/sglang/issues/33522)）。
- **AMD/HIP** — 为 Qwen3.5 提供 strided GDN prefill 投影视图，以对齐 CUDA 的零拷贝路径（[#41683](https://github.com/sgl-project/sglang/pull/41683)）。
- **持续调查中** — 在 SM120 上实测 DeepSeek-V4 prefill 约为 2–7K tok/s，而在相同硬件上通过 vLLM/marlin 报告约为 12.5K tok/s（[#33422](https://github.com/sgl-project/sglang/issues/33422)）。

## 5. 稳定性与回归（按严重程度排序）
1. **FP8 KV-cache scales 被静默忽略** — attention 构建时未传入 `quant_config`，影响 GLM-4.5、Llama 4、Qwen3-MoE、GPT-OSS、HunYuan、HY3、K-EXAONE、LFM2。存在正确性/精度风险，尚无修复 PR 关联（[#48749](https://github.com/sgl-project/sglang/issues/48749)）。
2. **推理解析器截断** — CoT 内部引用的字面量 `</think>` 会提前截断推理，导致 CoT 泄漏到 `content` 中。直接影响 agent/推理类应用（[#48791](https://github.com/sgl-project/sglang/issues/48791)）。
3. **模型网关丢弃 `cache_salt`** — 在 `/v1/chat/completions` 上，导致缓存隔离行为与直连后端不一致（[#48793](https://github.com/sgl-project/sglang/issues/48793)）。
4. **main 上的 Diffusion CI 失败** — 自 #48366 合入后 `test_kandinsky6_sr_decode_parallel` 出现张量不匹配；该测试新注册在 GPU 用例中（[#48892](https://github.com/sgl-project/sglang/issues/48892)）。
5. **GLM-5.3-Flash 退化** — 在多工具 agentic 提示下，thinking 输出坍缩为重复的 `!`；记录在 GLM bug 汇总中（[#36669](https://github.com/sgl-project/sglang/issues/36669)、[#37524](https://github.com/sgl-project/sglang/issues/37524)）。
6. **CI 不稳定** — 专用追踪器报告 1 个损坏 / 4 个 flaky / 1164 个近期已修复（[#42752](https://github.com/sgl-project/sglang/issues/42752)、[#17050](https://github.com/sgl-project/sglang/issues/17050)）。
7. **今日关闭**：PD router 断路器误调度（#31206）、`fast_topk_v2` 在 bucket 溢出时静默返回错误 top-k（#36807）、MTP draft 接受率随运行时长衰减（#37326）、MiniMax-M3 SM121 全 NUL 输出（#38143）。

## 6. 对应用开发者的意义
- **推理/工具调用流水线**：`</think>` 截断 bug（#48791）意味着解析出的 `reasoning_content` 可能被截断，且 CoT 可能泄漏到用户可见内容中——如果你的提示可能引用 think 标签，请验证解析逻辑，并关注 GLM-5.3-Flash 在多工具 schema 下的表现。
- **网关部署**：如果你依赖 `cache_salt` 实现租户/缓存隔离，通过 `sgl-model-gateway` 的请求当前会绕过它（#48793）——请直连后端或固定到包含修复的版本。
- **量化 FP8 KV-cache 用户**：checkpoint 提供的 FP8 scales 在所列模型系列上可能未被应用（#48749）；在这些架构上信任 FP8 KV 之前，请先做精度基准测试。
- **Kimi-K3.1 采用者**：Day-0 服务栈捆绑了 PD 分离、DFlash 投机解码和 HiSparse——一旦 #48414 合入，正是规划评估的好时机。
- **长时间运行的副本**：权重缓存守护进程路线图（<1 秒重载）和 MTP 接受率衰减修复表明重启/运行时长经济性正在改善；开放中的 NextN/MTP 衰减类问题值得在可观测性中持续关注。

</details>

<details>
<summary><strong>llama.cpp</strong> — <a href="https://github.com/ggml-org/llama.cpp">ggml-org/llama.cpp</a></summary>

# llama.cpp 摘要 — 2026-11-07

## 1. 今日亮点

过去 24 小时非常繁忙：共落地 10+ 个构建（b12167–b12179），涵盖 Hexagon CONV_3D 支持、Vulkan ReBAR 检测、新的 MoE 专家缓存（ClassLRU）以及 WebGPU 批量张量 API。模型方面，Kimi-K3.1-Pro 文本移植已合并，`mtmd` 的 Mage-VL-Cowork 移植已开启 PR。稳定性消息喜忧参半——若干投机解码正确性问题仍未解决，且一批 Vulkan/SYCL/Metal 平台缺陷重新浮现。

## 2. 发布与破坏性变更

- **b12179** — Hexagon：CONV_2D 优化 + CONV_3D 支持（复用 HTP_OP_CONV_2D，新增 HTP_OP_CONV_3D）。链接：https://github.com/ggml-org/llama.cpp/pull/31814
- **b12177** — CLI：修复提示词或文件中无效 UTF-8 导致的崩溃。链接：https://github.com/ggml-org/llama.cpp/pull/31563
- **b12176** — 测试：为兼容 NetBSD/GCC 14.4，使用 `std::` 限定 `isnan`/`signbit`。
- **b12175** — CI：为容器化 CPU runner 覆盖 `hardware_concurrency`（修复 test-llama-context 表模式的超额订阅问题）。
- **b12174** — CMake：直接生成 RC 文件（移除 `win-app.rc.in`）。
- **b12173** — OpenCL：为 f16/i32/i16/bf16 增加 `repeat` 支持。
- **b12172** — WebGPU：新增批量张量 API `get_tensor_n` / `set_tensor_n`。链接：https://github.com/ggml-org/llama.cpp/pull/31473
- **b12171** — Vulkan：检测 ReBAR，不可用时跳过 BAR 分配。链接：https://github.com/ggml-org/llama.cpp/pull/31658
- **b12170** — `moe-cache`：受 LRFU/MQ 启发的混合专家缓存（“ClassLRU”）。链接：https://github.com/ggml-org/llama.cpp/pull/31233
- **b12167** — CUDA：修复 Maxwell 构建回归。

此窗口内无公开 API 或配置破坏性变更；下文提到的 `n_ctx_slot` 上限移除仍是一个 PR，尚未合并。

## 3. 新模型与硬件支持

- **Kimi-K3.1-Pro** 文本模型已合并（CLOSED/可合并）。复用 K3，将全注意力 MLA 层替换为 DSA；复用 `build_attn` 中的 top-k mask 实现。链接：https://github.com/ggml-org/llama.cpp/pull/31779
- **Mage-VL-Cowork**（多模态）在 `mtmd` 中开启 PR；注意到 Metal 的 `kernel_mul_mm_f32/f16_f32` 不遵守 `set_prec`。链接：https://github.com/ggml-org/llama.cpp/pull/31819
- **Hexagon**：HTP/HMX 平铺上的 CONV_3D。链接：https://github.com/ggml-org/llama.cpp/pull/31814
- **Vulkan**：感知 ReBAR 的分配策略。链接：https://github.com/ggml-org/llama.cpp/pull/31658
- **OpenCL**：为 f16/i32/i16/bf16 增加 `repeat`；`arange` PR 已开启。链接：https://github.com/ggml-org/llama.cpp/pull/31829
- **WebGPU**：批量张量 get/set API。链接：https://github.com/ggml-org/llama.cpp/pull/31473
- **HIP/ROCm**：针对 CDNA 的 MMQ N-tile 启发式已合并。链接：https://github.com/ggml-org/llama.cpp/pull/28709
- **NVFP4 量化**（Blackwell FP4 MMA）PR 已关闭；基准测试指出“……40% 加速（长提示词上 20%）”——值得关注后续落地。链接：https://github.com/ggml-org/llama.cpp/pull/23572
- **外部 PLE（engram）分片**通过 `--shard` 加载（开启的 PR），用于多 GGUF 布局。链接：https://github.com/ggml-org/llama.cpp/pull/31185

## 4. 性能与优化

- **MoE 专家缓存（ClassLRU）**：新的 LRFU/MQ 风格混合缓存，以真实 token 计数为时钟。主要收益针对 `--n-cpu-moe` / 小显存 MoE 服务；尚未发布硬性数据。链接：https://github.com/ggml-org/llama.cpp/pull/31233
- **OpenCL q8_0 / MoE q4_k/q5_1 小 n GEMV**：为投机解码验证路径增加小 n 内核。链接：https://github.com/ggml-org/llama.cpp/pull/31831
- **HIP 针对 CDNA 的 MMQ N-tile**：来自参数扫描的经验启发式；改善了 RDNA 衍生调优效果参差的形状。链接：https://github.com/ggml-org/llama.cpp/pull/28709
- **Vulkan ReBAR**：不支持时跳过 BAR 分配——对 AMD DGPU“禁用 ReBAR 时变慢”这一类问题有针对性修复。链接：https://github.com/ggml-org/llama.cpp/pull/31658
- **NVFP4 / Blackwell**：已关闭的 PR #23572 声称 40% PP 提升（长提示词 20%）。关注其是否重新开启。
- **`n_ctx_slot` 上限移除**（开启的 PR）：允许 `-c` 在内存允许时超过 `n_ctx_train`（RoPE 缩放）。链接：https://github.com/ggml-org/llama.cpp/pull/31377
- 注意：**自 PDL 提交（`7b28f950`）以来 B200 上的 CUDA ADD/GELU 回归**仍未解决——性能损失，非功能性问题。链接：https://github.com/ggml-org/llama.cpp/issues/30004

## 5. 稳定性与回归

按用户影响排序（除非另有标注，否则均为未解决）：

1. **投机解码正确性——量化目标上的贪心发散**（#25618，38 条评论）。当目标为 Q4_K_M 等量化权重时，贪心输出与原生输出发散；bf16 则一致。这是量化权重上生产级投机解码的最大正确性风险。https://github.com/ggml-org/llama.cpp/issues/25618
2. **`--cache-ram` 跨对话 KV 串扰**（#27148）。在并发负载下，RAM 支持的提示缓存会将一个*无关*对话恢复到新槽位——对多租户服务器构成正确性/安全问题。https://github.com/ggml-org/llama.cpp/issues/27148
3. **CUDA 非法内存访问：Gemma 4 31B + draft-mtp 在 KV 回滚后**（#31077）。检查点恢复 / 使用 MTP 完整重新处理后崩溃。https://github.com/ggml-org/llama.cpp/issues/31077
4. **服务器解码中途卡死**（#27388）：`/health` 正常，`/slots` 挂起，需要 SIGKILL。无关联修复。https://github.com/ggml-org/llama.cpp/issues/27388
5. **Vulkan/ANV：FA 标量回退 → O(N²) PP + 设备丢失**，Intel Arc B580（#27638）。Vulkan Flash Attention 分发问题。https://github.com/ggml-org/llama.cpp/issues/27638
6. **Qwen3.6 上的 draft-mtp 改变确定性输出**（CLOSED #23335）。运行时缓解：需要确定性时禁用投机解码。
7. **SYCL 双 Arc B60 `--cpu-moe -sm layer` 卡顿**（#31510）：40 GB MoE 缓存相比 15 GB 出现 100–1000× 减速；全部时间花在串行专家 H2D 上。https://github.com/ggml-org/llama.cpp/issues/31510
8. **`/slots` 恢复对混合/循环 + SWA 丢失 KV 复用**（#28194）：混合模型的检查点未持久化。https://github.com/ggml-org/llama.cpp/issues/28194
9. **gfx1201 WMMA FA 分发阈值**（#28867）：自 #28102 以来投机解码约 20% 回归。https://github.com/ggml-org/llama.cpp/issues/28867
10. **投机解码对每个 token 返回 logprob 0.0（p=1.0）**（#27972）。破坏下游基于置信度的路由。https://github.com/ggml-org/llama.cpp/issues/27972
11. **draft-dflash 在混合 Qwen3.8 + `--parallel 8` 下 post_decode `seq_rm` 中止**（#31656）。https://github.com/ggml-org/llama.cpp/issues/31656
12. **CPU 后端 `supports_op` 对无 `vec_dot` 类型的 MUL_MAT 误报** → NULL 解引用；修复 PR 已开启。https://github.com/ggml-org/llama.cpp/pull/31832
13. **SYCL 超额提交内核恐慌**，超大设备缓冲区；防护 PR 已开启。https://github.com/ggml-org/llama.cpp/pull/31285
14. 仅构建修复：**CUDA Maxwell** 在 b12167 中修复；**CLI 无效 UTF-8 崩溃**在 b12177 中修复。

## 6. 对应用开发者的意义

- **在 #25618 解决之前，不要信任量化目标上的投机解码用于确定性敏感的工作负载**。bf16/FP16 目标可靠；量化权重上的 n-gram 投机没有问题。对于 B200/gfx1201，预计相比 #28102 之前的基线约有 20% 回归。
- **多租户服务器**：在依赖跨槽位 KV 复用之前，对照 #27148 审查 `--cache-ram` / `--cache-idle-slots` 的使用。最安全的姿态是在并发负载下将 RAM 提示缓存视为尽力而为；若无法禁用，请按槽位隔离租户。
- **小 GPU 上的 MoE 服务**：新的 ClassLRU 缓存（b12170）是对 `--n-cpu-moe` 吞吐的直接改进，并减少 #25859 中报告的串行专家 H2D 卡顿以及 #31510 中的 SYCL 病态。考虑重新基准测试你的卸载配置。
- **Intel/AMD 上的 Vulkan**：b12171 的 ReBAR 检测移除了 ReBAR 关闭时导致 AMD DGPU 生成缓慢的代码路径，但 ANV Flash Attention 回退（#27638）仍然存在——在 Intel 上固定关闭 Vulkan FA 直到修复。
- **服务器加固**：无效 UTF-8 崩溃（b12177）对任何转发用户提供提示字节的应用是真实的远程输入崩溃——请升级。`/v1/chat/completion/control` 的“inject”操作 PR（#27343）在你需要流中引导生成时会很有用。
- **模型覆盖**：Kimi-K3.1-Pro（DSA/MLA 混合）和 Mage-VL-Cowork 现已在 llama.cpp 路线上可行；如果你在 Qwen3-Coder/Nemotron 上构建 agent 工具调用器，PR #30193（无序 JSON 工具参数）值得关注以提升可靠性。
- **上下文大小**（#31377 开启）：一旦合并，服务器将在用户显式选择加入时遵守超过 `n_ctx_train` 的 `-c`——请据此规划配置，但暂时不要依赖它。
- **NVFP4**：40% 的 Blackwell 提升尚未进入构建；在承诺 Blackwell 专用量化策略前请先基准测试。

</details>

<details>
<summary><strong>Ollama</strong> — <a href="https://github.com/ollama/ollama">ollama/ollama</a></summary>

# Ollama 摘要 — 2026-11-07

## 1. 今日亮点
过去 24 小时内没有新版本发布，但 issue/PR 队列围绕基于 llama-server 的新 0.40.x 运行时相当活跃：一个在空闲 runner 无法被杀死时泄漏 VRAM 的调度器 bug 已有修复 PR 处于开启状态，CUDA/Vulkan 后端回归问题继续在特定 GPU 上浮现（配备 cuda_v13 的 RTX 40 系列、AMD Renoir 和 Intel Lunar Lake 核显）。模型侧的工作集中在 parser/renderer 正确性（qwen3-coder、gemma4、Nemotron）以及将 MLX 覆盖扩展到 dense Nemotron 架构。

## 2. 版本发布与破坏性变更
- 过去 24 小时内无新版本发布。
- 上游引擎升级进行中：[#19576](https://github.com/ollama/ollama/pull/19576) `llama.cpp: version bump b12177`（从 b12111 升级）。预计行为变更将延续到下一个构建版本中。

## 3. 新模型与硬件支持
- **Dense Nemotron (MLX)**：[#19567](https://github.com/ollama/ollama/pull/19567) 在 MLX runner 中注册了 `NemotronForCausalLM`，支持 Luciole 归一化和 Split+Metaspace tokenizer，打通了 Luciole 1B/23B 从 Safetensors 到 MLX 的直接转换。对应请求：[#19566](https://github.com/ollama/ollama/issues/19566)。
- **Nemotron-H 微调**：[#19570](https://github.com/ollama/ollama/pull/19570) 保留 checkpoint 自带的 chat template，而不再强制使用 NVIDIA 的 `nemotron-3-nano` renderer/parser。
- **Windows ARM64 CUDA**：[#19580](https://github.com/ollama/ollama/pull/19580) 为 ARM64 添加 CUDA 13.5 和 SM 103 支持，同时保留 SM 121。
- **Qwen3.8 Flash Next 的 MLX 内存效率优化**：[#18078](https://github.com/ollama/ollama/pull/18078) 将 QSA 投影和非专家 MTP 迁移至 BF16，其他 dense/control 权重使用 MXFP8（`qwen3.8-flash-next:125b-a6b-nvfp4` 和 `:125b-mlx` 均已重新发布）。

## 4. 性能与优化
- [#18078](https://github.com/ollama/ollama/pull/18078) 中的量化策略改进：一刀切的 NVFP4 会降低长序列生成质量，因此现在按 tensor 混合精度——这是 125B MoE 在内存占用与长上下文质量之间的具体权衡。
- 调度器 VRAM 核算的正确性（见 §5）直接影响吞吐量：空闲 VRAM 预测错误会导致 CPU offload 和第二次 runner 启动，从而使 tokens/sec 大幅下降。

## 5. 稳定性与回归
按严重程度排序：

1. **keep_alive 到期时的 runner 泄漏** — [#19577](https://github.com/ollama/ollama/issues/19577)：当 `Close` 失败时，Ollama 将模型标记为已卸载、丢弃 runner、不记录任何日志；VRAM 持续被占用，下次加载时倾向 CPU 并启动第二个 `llama-server`。**修复 PR 已开启**：[#19581](https://github.com/ollama/ollama/pull/19581)。
2. **CUDA 13 下长语法约束生成 → GPU 从总线上掉线** — [#17026](https://github.com/ollama/ollama/issues/17026)：`unspecified launch failure` → RTX 4070 Ti SUPER 上出现 Xid 79；相同请求在 cuda_v12 上正常。需要进一步排查。
3. **AMD Renoir 核显上的 Vulkan 崩溃** — [#19558](https://github.com/ollama/ollama/issues/19558)：在 0.40.14 和 0.40.15-rc2 中，`llama-server` 在 gfx90c 上加载模型时以 `0xe06d7363` 退出，所有模型均受影响。
4. **Intel Arc 130V（Lunar Lake）上的 Vulkan 设备丢失** — [#19158](https://github.com/ollama/ollama/issues/19158)：`gemma4:12b` 在任意 GPU offload 下相较 0.34.4 出现回归。
5. **macOS 上 `/api/chat` 间歇性 500** — [#19293](https://github.com/ollama/ollama/issues/19293)：长时间流式回复后出现 `apply-template … broken pipe`；重试即可成功。
6. **在禁用截断的情况下超大输入被静默截断** — [#19492](https://github.com/ollama/ollama/issues/19492)（已关闭）。
7. **Parser/renderer 正确性**（已关闭/进行中）：qwen3-coder 工具调用嵌套 [#17021](https://github.com/ollama/ollama/pull/17021)；gemma4 未闭合参数列表 [#19274](https://github.com/ollama/ollama/pull/19274)；游离的 `<channel|>` 标签 [#19574](https://github.com/ollama/ollama/pull/19574)；`ornith:35b` 原始 token/工具 XML [#16992](https://github.com/ollama/ollama/issues/16992)。
8. **OpenAI 兼容性**：零模型时 `/v1/models` 返回 `"data": null` — [#19381](https://github.com/ollama/ollama/issues/19381)（已关闭）。
9. **GGUF 加固**：元数据解析阶段拒绝重复的 tensor 名称 — [#19571](https://github.com/ollama/ollama/pull/19571)。
10. **网络**：在丢弃后量子 X25519MLKEM768 ClientHello 的网络上，`ollama pull` 出现 TLS 握手超时 — [#19430](https://github.com/ollama/ollama/issues/19430)。

## 6. 对应用开发者的意义
- **不要假设 VRAM 会被回收。** 如果你在共享 GPU 上管理长生命周期的 `keep_alive` 会话，请监控是否存在孤立的 `llama-server` 进程，直到 [#19581](https://github.com/ollama/ollama/pull/19581) 合入；泄漏的 runner 可能会悄无声息地将你的下一个模型推向 CPU。
- **如果你使用 cuda_v13 + 语法约束解码，请固定你的后端。** [#17026](https://github.com/ollama/ollama/issues/17026) 是硬件级故障，而非可重试错误——在受影响的 RTX 40 系列配置上强制使用 cuda_v12 或改用无约束输出。
- **macOS 上重试逻辑仍然重要**：[#19293](https://github.com/ollama/ollama/issues/19293) 是暂时性的，重试即可自愈；将 `apply-template` broken-pipe 500 视为可重试。
- **工具调用可靠性在改善，但因模型而异。** 如果你通过工具调用运行 qwen3-coder 或 gemma4，待合入的 parser PR 解决了真实的故障模式；请针对你的确切模型 tag 进行验证并有意识地升级。
- **OpenAI SDK 兼容性**：能容忍 `null` 的旧版客户端可能不受影响，但强类型（如 .NET/Java）SDK 在修复进入稳定版之前会失败——这是保持 Ollama 版本更新的一个理由。
- **MLX 用户**获得了 dense Nemotron/Luciole 支持以及 Qwen3.8 Flash Next 更好的内存表现；如果你在 Apple Silicon 上服务较大的 MoE 模型，这会很有用。

</details>

<details>
<summary><strong>LiteLLM</strong> — <a href="https://github.com/BerriAI/litellm">BerriAI/litellm</a></summary>

# LiteLLM 摘要 — 2026-11-07

## 1. 今日亮点

1.108 LTS 系列正处于活跃的向后移植模式：今天约有十几个 `lts/1.108.x` cherry-pick 合入，涵盖加密推理往返、护栏回写保真度、Bedrock 空 `api_base` 崩溃以及托管 MCP `tool_choice` 处理——这强烈表明 1.108 是大多数生产部署正在汇聚的分支。在协议转换方面，今日的主导主题是 **Anthropic `/v1/messages` ↔ OpenAI 互操作**：多个未关闭的 bug 和 PR 涉及推理块、`tool_result.is_error`、`output_config` 和 `server_tool_use` 在转换中被破坏的问题。计费完整性也受到关注，一个 PR 关闭了上游 `usage` 格式错误或缺失导致 $0 定价流量绕过密钥预算的缺口。

## 2. 发布与破坏性变更

- **v1.108.2 (LTS)** 和 **v1.108.1 (LTS)** 已发布。`ghcr.io/berriai/litellm:lts` 标签现在跟踪最新的 LTS 补丁；镜像经 cosign 签名，可对照已发布的 `cosign.pub` 验证。这些补丁中未宣布配置模式变更。→ [Releases](https://github.com/BerriAI/litellm/releases)
- **潜在破坏性（审查中）：** PR #45609 提议将 OpenAI 函数工具 + 推理请求桥接到 Bedrock 原生 `/responses` 而非 Converse——标记为 `feat(bedrock)!`。若合并，推理密集的 Bedrock 工具调用预计会出现行为差异。→ [PR #45609](https://github.com/BerriAI/litellm/pull/45609)
- **潜在破坏性（审查中）：** PR #48936 更改了托管文件/向量存储钩子的注册方式，将导入移至 `litellm_enterprise`。修复了 `pip install 'litellm[proxy,extra_proxy]'` 路径下 `POST /v1/files` 带 `target_model_names` 返回 500 的问题。→ [PR #48936](https://github.com/BerriAI/litellm/pull/48936)

## 3. 新模型与硬件支持

- **Gemini 模型目录同步**（PR #50468）：为五行 Gemini 记录添加了文档化的后继映射，未更改价格或关停日期。→ [PR #50468](https://github.com/BerriAI/litellm/pull/50468)
- **Vertex AI Claude 合作伙伴模型**（Issue #49645，未关闭）：请求体序列化目前将 `messages` 放在首位；功能请求将其重排至 `system`/`tools` 之后以兼容合作伙伴模型。→ [Issue #49645](https://github.com/BerriAI/litellm/issues/49645)
- 本窗口期内无新增 CUDA/ROCm/Metal 或量化支持；`hosted_vllm` 的 token 计数正确性正在 PR #48830 中改进（见 §4）。

## 4. 性能与优化

- **SAP GenAI Hub 延迟**（Issue #47096，未关闭）：每次 `sap/` 模型调用都会对 AICORE 管理 API 执行两次阻塞式 HTTP GET 以重新发现编排部署 URL——在负载下实测每次调用约 **870ms 开销**。尚无修复 PR；缓存部署 URL 是显而易见的补救措施。→ [Issue #47096](https://github.com/BerriAI/litellm/issues/47096)
- **通过后端进行 token 计数**（PR #48830）：Anthropic 计数路由现在通过实际后端计数，按像素面积对图像定价，并对未服务的模型返回 404——提升的是预算预留准确性而非原始延迟。→ [PR #48830](https://github.com/BerriAI/litellm/pull/48830)
- **Prisma 启动下载**（Issue #33167，因陈旧而关闭）：v1.92+ 在启动时获取 Prisma 二进制文件，在出口受限网络中失败——未合并修复即关闭；若你运行气隙环境，仍值得固定版本/离线预置。→ [Issue #33167](https://github.com/BerriAI/litellm/issues/33167)

## 5. 稳定性与回归

按严重程度排序：

1. **格式错误 usage 导致预算绕过**（PR #50469，未关闭）：OpenAI 兼容上游返回缺失/格式错误的 `usage` 时被定价为 $0 或返回 500，导致 `max_budget` 对该流量永不关闭。已有修复 PR。→ [PR #50469](https://github.com/BerriAI/litellm/pull/50469)
2. **空回复导致缓存污染**（PR #50057，未关闭）：流式 `/v1/messages` 调用非 Anthropic 模型返回空内容时被缓存，导致相同请求无限重放空答案。已有修复 PR。→ [PR #50057](https://github.com/BerriAI/litellm/pull/50057)
3. **凭据 CRUD 竞态**（PR #50464，未关闭）：并发周期重载可能撤销内存中的凭据创建/更新/删除。修复将数据库写入 + 内存更新包裹在 `MODEL_RECONCILE_LOCK` 中。→ [PR #50464](https://github.com/BerriAI/litellm/pull/50464)
4. **OpenRouter 流式幻影选项**（Issue #50156，未关闭，v1.108.0）：提供商计费尾部在 `finish_reason: stop` 之后、最终 usage 块之前变成一个额外的空 assistant 选项——破坏严格的流消费者。→ [Issue #50156](https://github.com/BerriAI/litellm/issues/50156)
5. **Anthropic→非 Anthropic 思考重放**（Issue #49969，已关闭，v1.106）：Bedrock Converse 将 `reasoningText.signature` 重放给 GLM 5.3，导致自动路由故障转移时 Bad Request。→ [Issue #49969](https://github.com/BerriAI/litellm/issues/49969)
6. **Vertex/Gemini cache_control 重排序**（Issue #47046，未关闭）：非前缀 `cache_control` 将消息移到历史之前并丢弃 `system_instruction`——静默正确性失败，模型回答上一轮。→ [Issue #47046](https://github.com/BerriAI/litellm/issues/47046)
7. **JSON 定义的提供商丢弃工具**（Issue #48145，未关闭，v1.106 相对 1.81 的回归）：通过 JSON 配置的 OpenAI 类提供商丢失工具定义，除非模型在目录中。→ [Issue #48145](https://github.com/BerriAI/litellm/issues/48145)
8. **`reasoning_effort: "none"`**（Issue #47924，未关闭）：剥离思考配置而非禁用它，导致 Claude 默认值保持开启。→ [Issue #47924](https://github.com/BerriAI/litellm/issues/47924)
9. **`token_counter` 拒绝 `detail: "original"`**（Issue #47327，未关闭）：调用前上下文检查记录 ERROR 并跳过强制执行。→ [Issue #47327](https://github.com/BerriAI/litellm/issues/47327)
10. **`/v1/images/edits` 缺失图像 → 500**（Issue #50173，未关闭）：应为 400；Vertex Gemini 上的客户端错误映射缺口。→ [Issue #50173](https://github.com/BerriAI/litellm/issues/50173)

另需注意：`COHERE_API_BASE` 对嵌入被忽略（Issue #50248）；非 OpenAI/Azure/Bedrock 提供商的 `httpx.Timeout` 连接超时被折叠（Issue #47822）；Anthropic `server_tool_use` 被呈现为待处理的 `tool_calls`，导致 agent 循环（Issue #48384，已关闭）；Langfuse trace 在 `/v1/decisions` 路由上被丢弃（Issue #45584）。

## 6. 这对应用开发者意味着什么

- **如果你使用 1.108 LTS：** 拉取 `:lts` 获取向后移植的修复，但需注意 LTS 分支仍在积累 Anthropic 桥接保真度补丁（PR #50456、#50462、#50449、#50460）。若你对 OpenAI 或 Bedrock 后端使用 `/v1/messages`，请固定版本并在每次 LTS 更新时重新测试。
- **对于 `usage` 报告不规范的上游，预算强制执行当前不可信。** 在 #50469 合并前，请独立监控支出或添加二级上限。
- **缓存 + 流式 agent：** 在 #50057 落地前对 `/v1/messages` 禁用响应缓存，否则可能遭遇粘性空响应。
- **跨模型家族的自动路由故障转移**（Claude → GLM/Gemini）是已知雷区：思考签名、`server_tool_use` 和模式净化均有未关闭或近期关闭的正确性问题。请显式测试故障转移路径，而非假设转换无损。
- **Anthropic 原生客户端（Claude Code 等）** 应对 `tool_result.is_error`、逐轮 `output_config` 及桥接中的加密推理预期日益增长但仍不完美的保真度——其中多项仅通过上述进行中的向后移植修复。
- **自托管/气隙环境：** Prisma 启动下载回归因陈旧而关闭，并未修复——升级至 v1.92 以上前请验证你的出口方案。

</details>

<details>
<summary><strong>Unsloth</strong> — <a href="https://github.com/unslothai/unsloth">unslothai/unsloth</a></summary>

# Unsloth 摘要 — 2026-11-07

## 1. 今日亮点
Unsloth 发布了 v0.1.916-beta，主打“大幅性能修复”——长对话中的输入/搜索更快、图片/视频加载更快、双 GPU 图片/视频生成，以及每个聊天独立浏览器窗口和浮动浏览器面板。活动主要集中于 Unsloth Studio/Desktop 的打磨：一波 WebKit 重样式/滚动延迟修复、多智能体 CLI 集成（Claude Agent、Codex、Hermes MCP），以及账户级设置同步。引擎侧有一项重要的外部提醒：transformers 5.23 将梯度检查点重命名为激活检查点，这会在四个审计点静默改变 Trainer 行为。

## 2. 发布与破坏性变更
- **v0.1.916-beta — 大幅性能修复**：长对话中的输入/搜索更快、图片/视频加载更快、双 GPU 图片/视频生成、每个聊天独立浏览器、浮动浏览器窗口。包含 Glass 主题优化。（仅发布说明；未记录 API 变更。）
- **transformers 5.23 兼容性（需采取行动）**：梯度检查点 API 重命名为激活检查点；旧名称在 v6 之前仍作为别名保留，但 `Trainer` 只读取新名称，改变了 Unsloth 中 4 个审计点的行为。Issue：[unslothai/unsloth#16667](https://github.com/unslothai/unsloth/issues/16667)（已关闭）。
- **v0.1.915-beta 中 Blender MCP 损坏**：MCP SDK 2.3.0 / FastMCP 4.1.6 不兼容，导致 Windows 11 上 Blender MCP 无法启动。[unslothai/unsloth#16878](https://github.com/unslothai/unsloth/issues/16878)（已关闭）。
- **版本升级回归风险**：部分桌面用户的 beta 更新路径失败——[unslothai/unsloth#16897](https://github.com/unslothai/unsloth/issues/16897)（开放中）。
- **扫描基线变动**：unsloth-zoo 2026.11.3 移动了 7 个此前已审查的站点，触发 `pip scan-packages :: hf-stack` 失败（3 个 CRITICAL，4 个 HIGH）。重新审查 PR：[#16922](https://github.com/unslothai/unsloth/pull/16922)。

## 3. 新模型与硬件支持
- **Studio 视频页面支持 Cosmos3（NVIDIA）**：256p 支持、NVIDIA 帧范围、按模型帧率选择器——[PR #16737](https://github.com/unslothai/unsloth/pull/16737)（堆叠；需先合并）。
- **Cosmos3-Nano 视频续接**：从片段的首/尾帧扩展视频——[PR #16741](https://github.com/unslothai/unsloth/pull/16741)（堆叠于 #16737 之上）。
- **Cosmos3-Nano-Sim-Transfer 重样式模式**于 sd-server（Canny 边缘条件视频重样式）——[PR #16975](https://github.com/unslothai/unsloth/pull/16975)。
- **Claude Agent 连接**通过本地 `claude` CLI（Pro/Max 套餐，无需 API key），使用 Anthropic Agent SDK——[PR #16963](https://github.com/unslothai/unsloth/pull/16963)。
- **麦克风到音频模型路由**：听写可将音频直接发送至任意音频输入模型（Qwen3-ASR、自定义端点或透传）——[PR #16976](https://github.com/unslothai/unsloth/pull/16976)。
- **gemma-4-thinking 聊天模板 bug**：推理内容被模板剥离——[issue #16699](https://github.com/unslothai/unsloth/issues/16699)（已关闭）。
- **多 GPU**：DiffusionGemma 在 2× CUDA（RTX 3060 12GB + 4060 Ti 16GB）上短提示可运行，但超过约 1792 token 时失败（见稳定性）。
- **硬件缺口被标记**：Windows 11 + RTX 3060 上图片生成回退到 CPU/RAM，而文本生成使用 GPU——[issue #16803](https://github.com/unslothai/unsloth/issues/16803)（开放中）。
- **功能请求**：用于低显存训练的 Transformer 块交换（层卸载到 RAM）——[issue #3997](https://github.com/unslothai/unsloth/issues/3997)（已关闭）。

## 4. 性能与优化
- **长对话 UI 卡顿已修复（发布级）**：v0.1.916 解决了输入/搜索和长对话卡顿；issue [#16603](https://github.com/unslothai/unsloth/issues/16603) 已关闭。
- **WebKit 重样式延迟**：长对话中关闭对话框/菜单耗时 5–7 秒（macOS/WebKitGTK）；Linux 上浏览器面板打开耗时超过 20 秒。修复阻止了侧边栏/菜单/运行设置切换时的整页重样式——[PR #16938](https://github.com/unslothai/unsloth/pull/16938)（已关闭），后续 [#16961](https://github.com/unslothai/unsloth/pull/16961)（开放中）。在 Chromium 上 150 轮时触发了约 9,200–18,400 个元素重样式。
- **RAG 摄取吞吐量**：`_embed_all` 一次嵌入一个文档；batch-of-1 路径测得 **约 99 ms/chunk，而批量为 2.07 ms**（小文件语料上约 48× 差距）。[issue #16715](https://github.com/unslothai/unsloth/issues/16715)（开放中）。
- **图片加载内存**：Studio 现在在图片加载后将释放的 glibc 堆归还 OS，而非在 arena 中保留暂存/临时块——[PR #16934](https://github.com/unslothai/unsloth/pull/16934)。
- **空闲卸载正确性/性能**：空闲卸载的 GGUF 使用原始启动设置（上下文大小、KV 缓存量化）重新加载，而非默认值——[PR #16927](https://github.com/unslothai/unsloth/pull/16927)。
- **朗读延迟**：每条回复的 TTS 启动延迟 10–20 秒，而专用页面约 0.5 秒；修复后在第一句后开始朗读——[PR #16955](https://github.com/unslothai/unsloth/pull/16955)。
- **语音模式栈**：audio.cpp 上的免提语音模式常驻于聊天模型旁（[PR #12384](https://github.com/unslothai/unsloth/pull/12384)），带思考填充 + 本地回合判断（[PR #14084](https://github.com/unslothai/unsloth/pull/14084)）——大型，仍开放。
- **多 LoRA 批次分页**：批次乱序开始时的游标排序修复——[#16719](https://github.com/unslothai/unsloth/issues/16719)、[#16720](https://github.com/unslothai/unsloth/issues/16720)（均已关闭）。

## 5. 稳定性与回归（按严重程度排序）
1. **HIGH — Windows 加载 Krea 2 Turbo 崩溃（0xc0000005）**：Studio 图片生成中可复现的后端崩溃——[issue #16892](https://github.com/unslothai/unsloth/issues/16892)（开放中，尚无修复 PR）。
2. **HIGH — DiffusionGemma 多 GPU 中止**：2 个 CUDA GPU 上提示超过约 1792 token 时 GGML_ASSERT `n_ubatch >= n_tokens`——[issue #16925](https://github.com/unslothai/unsloth/issues/16925)（开放中）。
3. **MEDIUM — CLI 静默接受截断流**：`unsloth inference`/`chat` 将未到 finish reason/`[DONE]` 即结束的流视为成功，污染下一轮历史——[issue #16867](https://github.com/unslothai/unsloth/issues/16867)（已关闭）。
4. **MEDIUM — 嵌入模型下载期间 RAG 索引永久失败**：每个文件报 `EmbeddingModelDownloadRequiredError`，无排队/重试——[issue #16714](https://github.com/unslothai/unsloth/issues/16714)（已关闭）。
5. **MEDIUM — 外部连接省略 `max_tokens` 时 Claude API 回答截断于 1,024 token**（`/v1/chat/completions`、`/v1/responses`）——[PR #16953](https://github.com/unslothai/unsloth/pull/16953)（修复开放中）。
6. **LOW — `search_conversation` 工具第三次消失**——[issue #16706](https://github.com/unslothai/unsloth/issues/16706)（开放中）。
7. **LOW — 安全加固缺口**：元数据主机黑名单仅匹配带点的 `169.254.169.254`；整数/十六进制/八进制写法可绕过——修复阻止替代编码——[PR #16937](https://github.com/unslothai/unsloth/pull/16937)。
8. **LOW — Studio web_search 始终返回“No results found”**：ddgs 缺少 `Accept` 头（DDG 202），且 yandex 不在引擎允许列表中——[issue #16664](https://github.com/unslothai/unsloth/issues/16664)（开放中）。
9. **LOW — S3 音频 JSONL 预处理在转录中的 U+2028 处中止**——[issue #16721](https://github.com/unslothai/unsloth/issues/16721)（已关闭）。
10. **UI 打磨（大多已修复）**：Glass 主题不一致（#16770）、全背景模糊（#16820）、壁纸 CSS 特异性冲突（#16736）——均已关闭；Blender MCP SDK 不兼容（#16878）已关闭。

## 6. 对应用开发者的意义
- **谨慎固定 transformers 版本**：若在 transformers ≥5.23 上使用 Unsloth 微调，请验证四个受影响点的梯度/激活检查点行为（[#16667](https://github.com/unslothai/unsloth/issues/16667)）；检查点内存预期可能静默变化。
- **流式客户端需自行做终止检查**：CLI 将部分流接受为成功（[#16867](https://github.com/unslothai/unsloth/issues/16867)）。若直接使用 Studio 的 HTTP API，请自行校验 finish reason 或 `[DONE]`，并在持久化历史前将截断回合视为错误。
- **在外部 Anthropic 连接上始终显式设置 `max_tokens`**，直至 [#16953](https://github.com/unslothai/unsloth/pull/16953) 落地，否则长输出将在 1,024 token 处截断。
- **多 GPU 视频扩散尚不稳健**：在 2-GPU DiffusionGemma 设置上保持提示低于约 1792 token（[#16925](https://github.com/unslothai/unsloth/issues/16925)）；在依赖 Cosmos3 工作流前，针对堆叠 PR 对（#16737 → #16741）进行测试。
- **RAG 流水线是最薄弱环节**：批量摄取比应有速度慢 48×（[#16715](https://github.com/unslothai/unsloth/issues/16715)），且模型下载期间索引硬失败且无重试（[#16714](https://github.com/unslothai/unsloth/issues/16714)）。请在嵌入模型就绪后再进行摄取，并考虑在上游批量处理文档。
- **长上下文 UX 在 v0.1.916-beta 中显著改善**，尤其是 WebKit（macOS/Linux 桌面）；WebKit 用户应升级以受益于重样式修复（[#16938](https://github.com/unslothai/unsloth/pull/16938)、[#16961](https://github.com/unslothai/unsloth/pull/16961)）。
- **智能体集成扩展**：Claude Agent（通过 CLI 登录使用 Pro/Max）、Codex `AGENTS.md`/技能继承，以及 Hermes MCP 配置继承，使 Studio 成为编码智能体可行的本地 LLM 网关——但 MCP SDK 2.3.0 仍会破坏 Blender MCP（[#16878](https://github.com/unslothai/unsloth/issues/16878)），因此在启用工具链前请审计 MCP 依赖。
- **若允许模型运行 Python/工具调用**，请审查 [#16937](https://github.com/unslothai/unsloth/pull/16937)：元数据端点阻断正在加固以覆盖非带点 IP 写法——若你的沙箱出口策略依赖主机名匹配，则与此相关。

</details>

---
*本日报由 [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar) 自动生成。*