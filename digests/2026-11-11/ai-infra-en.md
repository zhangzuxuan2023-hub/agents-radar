# AI Infrastructure Digest 2026-11-11

> Generated: 2026-11-10 23:14 UTC | Projects covered: 6

- [vLLM](https://github.com/vllm-project/vllm)
- [SGLang](https://github.com/sgl-project/sglang)
- [llama.cpp](https://github.com/ggml-org/llama.cpp)
- [Ollama](https://github.com/ollama/ollama)
- [LiteLLM](https://github.com/BerriAI/litellm)
- [Unsloth](https://github.com/unslothai/unsloth)

---

## Cross-Project Comparison

# Cross-Project Comparison Report — 2026-11-11

## 1. Ecosystem Overview

Today's activity reflects a maturing but still volatile inference stack: none of the five projects shipped a major feature release, yet regressions in production paths (vLLM structured-output decode, SGLang SM90 decode, llama.cpp quantized spec-decode) dominate the highest-severity items. AMD/ROCm is the single largest axis of new work across vLLM, SGLang, llama.cpp and Unsloth — trackers, CI expansion and backend ports — signaling a real shift away from NVIDIA-only optimization. Speculative decoding is simultaneously the top performance lever and the top correctness risk in every serving engine. LiteLLM remains consumed by the October supply-chain incident fallout (signed LTS releases, cosign verification), while Ollama and Unsloth push local/desktop UX and multimodality. Overall, the ecosystem is in a "harden and port" phase rather than a "new capabilities" phase.

## 2. Activity Comparison

| Project | Issues (24h) | PRs (24h) | Release status |
|---|---|---|---|
| **vLLM** | 111 issues updated | Concentrated in Rust frontend, ROCm/AITER attention, sampler kernels, KV-connector | No release; no breaking changes |
| **SGLang** | Issue tracker active (SM120 gap, SM90 regression) | Clustered diffusion-kernel fusion, HiCache, Rust server | No release; `transformers` 5.12.1 pin incompatibility on main |
| **llama.cpp** | Moderate | 9 builds in 24h | **b12262** (busiest releaser); source-tree refactor breaks out-of-tree `common.cuh` users |
| **Ollama** | Vulkan/MLX memory regressions, Cloud API limits | MLX concurrency, Vulkan iGPU mmap, Unix sockets | **v0.40.17-rc0** + llama.cpp b12257 + MLX bumps |
| **LiteLLM** | ~196 issues updated | Rust hot path, Helm/Docker consolidation, security backports | **v1.108.5 (LTS)**, backports to v1.104.9 / v1.103.11 |
| **Unsloth** | Desktop/Studio UX bugs dominate | Image editing, vLLM/SGLang token budgets, reverse-proxy | No release; behavioral change: on-demand PDF deps |

**Reading:** llama.cpp and Ollama are the only projects shipping tagged artifacts today (daily-build vs RC cadence). LiteLLM is releasing under security pressure. vLLM, SGLang and Unsloth are in review-heavy mode with no tags.

## 3. Model Support Race

| Project | New model/arch support today | Notable |
|---|---|---|
| **vLLM** | GLM 5.3 (CUDA opt tracker), ROCm Qwen3.8-Flash-Next (gfx950), Phi4Flash V1 port, Gemma4/3n PLE fix, MoeWNA16 AWQ keys | Broadest architecture coverage; deepest MoE/quant config surface |
| **SGLang** | DeepSeek-V4.1-Flash on MI355X tracker, RDNA3/4 CI proposal, multimodal GPU feature admission | AMD-first push; diffusion backends (AITER ring attention) |
| **llama.cpp** | **Kimi K3.1 vision (mtmd)**, Vulkan tiled-transpose for Qwen delta-net, Hexagon HMX, OpenCL Adreno FA | Fastest to land new vision models; widest hardware matrix |
| **Ollama** | **Nemotron 3.5 Bolt 74B-A9B on MLX** (3-bit prequant, LiLiCorr spec decode), decision-model support | MLX depth; consumer-device focus |
| **LiteLLM** | `azure_ai/mistral-large-4-latest`, Azure Responses `phase` replay for gpt-5.6 agent flows | Catalog/pricing parity, not engine-level |
| **Unsloth** | **MiniMax-H3-Image**, external `/images/edits`, reverse-proxy support | Image/editing pipeline rather than LLM archs |

**Who's ahead:** vLLM on LLM architecture breadth and quantization config flexibility; llama.cpp on fastest-to-merge new vision/multimodal support; Ollama on MLX-native flagship models; Unsloth on image-generation pipelines. The race is **fragmented by layer** — each project owns a different niche rather than competing head-to-head.

## 4. Performance Frontier

Optimization effort concentrates in **five distinct theaters**:

- **Sampling/kernels:** vLLM's exact Triton top-k/top-p sampler (64–69% faster datacenter, 2.5ms at c256 on B300) is the headline win; SGLang's diffusion-kernel fusion cluster (RMSNorm+residual, head-128 mean-square, packed-image QK-norm) targets diffusion workloads; llama.cpp's granite-switch reports **11.1× batch-1 decode** by eliminating 896 unconditional `mul_mat_id` ops.
- **KV cache / HiCache / P/D disaggregation:** vLLM LMCache exportable allocation + MLA/DCP prefill query gathering; SGLang HiCache pinned-memory index staging; vLLM MooncakeConnector ROCm RFC.
- **Speculative decoding:** both a perf lever (vLLM shorten-drafts-on-prefill, target-logit cutoff) and a regression source (vLLM DFlash TP>1 collapse, llama.cpp quantized-target divergence, Unsloth MTP acceptance drop).
- **Distributed serving:** vLLM trims ROCm CI P50 from 73→~30 min; SGLang UniBoost starvation-bounded scheduler and multi-item scoring host-sync removal (~1.53×).
- **Quantization:** vLLM Triton FP8 MoE expert path (fp32 scales + fused SiLU+quant), MoeWNA16 AWQ acceptance.

**Takeaway:** the frontier has moved from raw throughput to **scheduling fairness, cache economics and sampler overhead** — the easy GEMM wins are done.

## 5. Layer Positioning

| Layer | Projects | Distinguishing role |
|---|---|---|
| **Datacenter serving engines** | vLLM, SGLang | High-throughput, multi-GPU, P/D disagg, spec decode, distributed KV — compete on perf-per-token and hardware breadth |
| **Local/edge runtime** | llama.cpp, Ollama | llama.cpp = widest hardware backend matrix (CUDA/HIP/Vulkan/SYCL/OpenCL/Hexagon/CANN); Ollama = packaged local UX (MLX, Vulkan iGPU, Unix sockets) |
| **LLM gateway** | LiteLLM | Routing, rate limiting, spend, provider translation — correctness and supply-chain security are its critical concerns |
| **Training/fine-tuning** | Unsloth | Studio desktop + GGUF export + fine-tuning; bridges training and local inference |

The layers are **complementary, not substitutive**: a production stack might use LiteLLM at the edge, vLLM/SGLang for serving, Unsloth for fine-tune→GGUF, and llama.cpp/Ollama for local. This is why cross-project regressions rarely correlate — but spec-decode correctness is emerging as a **cross-layer system risk**.

## 6. Trend Signals

**Industry trends visible today:**

1. **AMD/ROCm is graduating from "tracker" to "first-class."** vLLM, SGLang, llama.cpp and Unsloth all have AMD-specific work today — gfx950/MI355X perf trackers, RDNA CI proposals, ROCm 6.3 minimum, gfx1031 segfault reporting. Teams planning GPU procurement should stop treating ROCm as experimental.
2. **Speculative decoding is the #1 dual-use risk.** It is simultaneously the biggest latency lever and the source of the highest-severity bugs across vLLM, SGLang, llama.cpp and Unsloth. **Validate acceptance rates per model×quant×TP configuration before trusting it in production.**
3. **Supply-chain hardening is now table stakes.** LiteLLM's signed LTS + cosign requirement post-incident should be read as a template for every infra dependency — pin and verify images, audit lockfiles.
4. **Structured output / tool-calling correctness is the new frontier.** vLLM structured-output regression, llama.cpp JSON/tool-arg escapes, LiteLLM tool routing drops, SGLang `</think>` parser split — agent reliability depends on these edge cases, not on raw tokens/sec.
5. **Scheduler fairness for agentic workloads is emerging.** SGLang UniBoost and agent-prefix-cacheability RFCs signal that classic token-bucket scheduling is inadequate for multi-turn agent traffic.
6. **Multimodality cheapens on the local runtime.** Kimi K3.1 vision, shared mtmd buffers and MiniMax-H3-Image make multi-model local servers more affordable — re-measure VRAM budgets.

**What agent/application developers should watch:**
- **Pin versions deliberately** — SGLang Hopper users hold v0.5.22; LiteLLM users move to `:lts` + cosign; llama.cpp spec-decode users pin together GGUF quant + draft heads.
- **Test the failure paths** — SSE keepalive-then-error (LiteLLM), `/v1/responses` 200-with-`failed` (SGLang), structured-output throughput (vLLM): these are the ways agents silently degrade.
- **Prefer batch/spec-safe decoding for eval and replay workloads**; reserve aggressive spec decode for latency-sensitive interactive paths where acceptance is monitored.
- **Track the two RFCs** — SGLang UniBoost scheduler and agent-prefix cacheability — as the likely blueprint for serving agent traffic in 2027.

---

## Per-Project Reports

<details>
<summary><strong>vLLM</strong> — <a href="https://github.com/vllm-project/vllm">vllm-project/vllm</a></summary>

# vLLM Digest — 2026-11-11

## 1. Today's Highlights

A high-volume issue day (111 issues updated, largely ROCm/AMD trackers and quantization RFCs) with no new releases in the last 24h. The most consequential open threads are a withdrawn proposal on batch-invariant cuBLAS routing (`#65569`), an RFC to improve `MooncakeConnector` for non-CUDA P/D disaggregation (`#60523`), and a signal that structured-output staging has caused a ~2x decode regression (`#62442`). On the PR side, work is concentrated in Rust frontend correctness fixes, ROCm/AITER attention paths, sampler kernel rewrites, and KV-connector exportable allocation.

## 2. Releases & Breaking Changes

None in the last 24h.

## 3. New Model & Hardware Support

- **GLM 5.3 optimization tracker** — same `glm_moe_dsa` base as GLM-5.2; follow-up CUDA optimizations proposed: [vllm#57406](https://github.com/vllm-project/vllm/issues/57406)
- **ROCm Qwen3.8-Flash-Next (gfx950 / MI355X)** — performance tracker for `amd/Qwen3.8-Flash-Next-Quark-MXFP4` (GatedDeltaNet, QuerySparse Attention, HyperConnection, MXFP4 MoE): [vllm#59575](https://github.com/vllm-project/vllm/issues/59575)
- **Phi4Flash V1 port** (V0 deprecation) — help wanted, Mamba state porting work: [vllm#23957](https://github.com/vllm-project/vllm/issues/23957)
- **Gemma4 / Gemma3n PLE cache device placement fix** — per-layer-embedding cache now allocated on input device: [vllm#62659](https://github.com/vllm-project/vllm/pull/62659)
- **MoeWNA16Config now accepts AWQ key names** (`w_bit`/`q_group_size`) in addition to GPTQ names: [vllm#66488](https://github.com/vllm-project/vllm/pull/66488)

## 4. Performance & Optimization

- **Exact Triton top-k/top-p sampler kernel** — 64–69% faster on NVIDIA datacenter GPUs, 42% on RTX PRO 6000, 29% on MI355X; ~183µs faster at c1 and 2.5ms at c256 on Qwen3.6 verify rows (B300): [vllm#66792](https://github.com/vllm-project/vllm/pull/66792)
- **AITER asm paged attention for large fp8 decode batches** on ROCm — LMCache MP exportable KV cache allocation via `EXPORTS_FD`: [vllm#66082](https://github.com/vllm-project/vllm/pull/66082), [vllm#66674](https://github.com/vllm-project/vllm/pull/66674)
- **MLA/DCP prefill query gathering** — gathers narrower queries before NoPE→KV-latent expansion, reducing query payload: [vllm#66906](https://github.com/vllm-project/vllm/pull/66906)
- **Spec decode: shorten drafts on prefill-heavy steps** (`num_speculative_tokens_under_prefill_load`) to reduce TTFT/E2EL: [vllm#66226](https://github.com/vllm-project/vllm/pull/66226)
- **Spec decode: apply top-k/top-p cutoffs on target logits load** during verification: [vllm#66534](https://github.com/vllm-project/vllm/pull/66534)
- **Triton FP8 MoE expert path** — keep fp32 activation scales and re-enable fused SiLU+quant: [vllm#64906](https://github.com/vllm-project/vllm/pull/64906)
- **ROCm CI time-to-signal** — RFC to reduce P50 PR wait from 73 min to ~30 min: [vllm#66411](https://github.com/vllm-project/vllm/issues/66411)

## 5. Stability & Regressions

Ranked by severity:

1. **DFlash acceptance collapse at TP>1** (MiMo-V2.6-Flash-MOPD, TP1 fine) — speculative decoding broken under tensor parallelism; no fix PR noted: [vllm#66387](https://github.com/vllm-project/vllm/issues/66387)
2. **~2x structured-output decode regression** since `#45424` grammar-bitmask staging, not fixed by `#49919`: [vllm#62442](https://github.com/vllm-project/vllm/issues/62442)
3. **SMG MoRI-IO P/D disagg connector broken on ROCm** (closed): [vllm#59116](https://github.com/vllm-project/vllm/issues/59116)
4. **DSv4.1 K-cache int32 overflow** — rows written ~4GiB out of bounds; crashes startup at TP8 on MI355X. Fix PR uses int64 offsets: [vllm#65926](https://github.com/vllm-project/vllm/pull/65926)
5. **0.26.0 DSv4 perf changes cost ~10.6% spec-decode acceptance** on production 2-node deployment (isolated A/B): [vllm#49927](https://github.com/vllm-project/vllm/issues/49927)
6. **MiniMax-M2.7 TP4+EP throughput regression** in v0.29–0.32: [vllm#63705](https://github.com/vllm-project/vllm/issues/63705)
7. **Batch-invariant routing to Triton** — cuBLAS-routing proposal withdrawn (cuBLAS not batch-invariant for all shapes): [vllm#65569](https://github.com/vllm-project/vllm/issues/65569)
8. **Rust frontend**: top-level `thinking` discarded; `system.tools` dropped. Fix PRs open: [vllm#66907](https://github.com/vllm-project/vllm/pull/66907), [vllm#66908](https://github.com/vllm-project/vllm/pull/66908)
9. **FlashInfer fused MoE finalize nondeterminism** — new opt-out env var `VLLM_FLASHINFER_MOE_FUSED_FINALIZE`: [vllm#66905](https://github.com/vllm-project/vllm/pull/66905)
10. **Hybrid GDN prefix-cache misses under MTP spec decode** (Qwen3.5-122B-A10B) — fix PR open: [vllm#52244](https://github.com/vllm-project/vllm/pull/52244)

## 6. What This Means for Application Developers

- **Structured outputs**: if you rely on grammar-constrained decoding, expect materially lower decode throughput on current builds until the staging regression (`#62442`) is resolved; benchmark before upgrading.
- **Speculative decoding + TP**: multi-GPU deployments with draft models should validate acceptance rates — both DFlash collapse (`#66387`) and MiniMax EP regressions (`#63705`) hit production paths.
- **Batch invariance**: don't assume cuBLAS handles all shapes; keep `batch_invariance` settings conservative for RL/verification loops, and consult the zero-mismatch-rl recipes referenced in `#66550`.
- **Rust frontend users**: thinking controls and system-message tool declarations have known gaps — pin to Python frontend if your agent depends on these until `#66907`/`#66908` land.
- **Sampler-heavy workloads**: the new exact Triton top-k/top-p kernel (`#66792`) offers meaningful speedups for large-batch sampling; 2.5ms at c256 on B300 is worth flagging for high-throughput serving.

</details>

<details>
<summary><strong>SGLang</strong> — <a href="https://github.com/sgl-project/sglang">sgl-project/sglang</a></summary>

# SGLang Digest — 2026-11-11

## 1. Today's Highlights
No new releases landed in the last 24h, but the issue tracker is dominated by a large SM120 (RTX PRO 6000 Blackwell) attention-backend gap and a freshly reported SM90 decode regression between v0.5.22 and v0.5.23 (~33–45% per-step slowdown on DeepSeek-V4.1-Flash at concurrency 12). On the performance side, a cluster of diffusion-kernel fusion PRs and HiCache/index-upload staging work are in review, while the Rust server stack continues to mature with error-typing and KV-sharding extensibility changes.

## 2. Releases & Breaking Changes
None today (no releases in the last 24h). Note for tracking: issue [#38183](https://github.com/sgl-project/sglang/issues/38183) reports `transformers` 5.12.1 pin incompatibility breaking `import sglang` on current main — relevant to anyone pinning dependencies.

## 3. New Model & Hardware Support
- **[AMD] RDNA3/RDNA4 (gfx1100/gfx1201) CI + upstreaming plan** — issue [#46900](https://github.com/sgl-project/sglang/issues/46900) proposes bringing consumer AMD GPUs into CI.
- **[AMD][ROCm] DeepSeek-V4.1-Flash on MI355X (gfx950)** — performance tracker [#49327](https://github.com/sgl-project/sglang/issues/49327) for TP4 text+image with/without DSpark speculative decoding.
- **[AMD] Ring attention on AITER backend** — PR [#24053](https://github.com/sgl-project/sglang/pull/24053) enables ring attention for AMD diffusion workloads.
- **cuDNN attention backend** — long-standing enhancement [#2272](https://github.com/sgl-project/sglang/issues/2272) still open; would add NVIDIA-maintained paged attention.
- **Multimodal GPU feature publication admission** — PR [#47769](https://github.com/sgl-project/sglang/pull/47769) adds bounded CUDA IPC/VMM admission for multimodal features.

## 4. Performance & Optimization
- **SM90 decode regression** — issue [#49336](https://github.com/sgl-project/sglang/issues/49336): v0.5.22 → v0.5.23 costs **33–45% per-step decode time** at concurrency 12 across 8K–64K contexts on 8×H20-3e with DeepSeek-V4.1-Flash; `use_deep_select_decode` also loses acceptance on Hopper. No fix PR referenced — high priority to bisect.
- **DeepSeek-V4 prefill on SM120** — issue [#33422](https://github.com/sgl-project/sglang/issues/33422): ~2–7K tok/s vs ~12.5K via vLLM/marlin on identical 4×RTX PRO 6000 hardware; tuning guidance requested.
- **Diffusion kernel fusion (BBuf)** — several PRs landing: RMSNorm+residual epilogue fusion in `ming` ([#49456](https://github.com/sgl-project/sglang/pull/49456)), exact head-128 mean-square reduction fusion in `nomas` ([#49308](https://github.com/sgl-project/sglang/pull/49308)), packed-image QK-norm/RoPE on SM103 for `joy` ([#49417](https://github.com/sgl-project/sglang/pull/49417)), and timestep-modulation reuse across serial CFG branches in `anima` ([#49504](https://github.com/sgl-project/sglang/pull/49504)).
- **HiCache kernel-backend index staging** — PR [#49633](https://github.com/sgl-project/sglang/pull/49633) stages host index uploads in pinned memory, fixing async semantics of the prior `move_indices`.
- **Multi-item scoring host-sync removal** — PR [#49412](https://github.com/sgl-project/sglang/pull/49412): freeze GC post-startup + avoid host syncs → **1.53×** offline `Engine.score` throughput (GPU busy 63–67% → higher).
- **MoE router GEMM unification** — PR [#49668](https://github.com/sgl-project/sglang/pull/49668) unifies router GEMM behind a shared `RouterGate`, targeting long-CoT routing stability and decode overhead.
- **UniBoost starvation-bounded scheduling** — RFC [#48519](https://github.com/sgl-project/sglang/issues/48519) proposes a schedule policy with wait bounds for agentic workloads.

## 5. Stability & Regressions
Ranked by severity:
1. **SM90 decode regression (#49336)** — 33–45% slowdown v0.5.22→v0.5.23. Highest impact for production Hopper deployments; no fix PR yet.
2. **fa4 attention crash on SM120 (#42012)** — GLM-5.3-Flash crashes at CUDA-graph capture (hybrid extend reshape); triton is the only working backend on RTX PRO 6000.
3. **HiCache + Mooncake OOM crash (#45989)** — `buffer_only` + L3 causes scheduler to admit an unfinishable chunked prefill, then "Prefill out of memory" with all radix nodes locked. Reported on v0.5.21.
4. **`/v1/responses` streaming returns HTTP 200 + `response.failed`** for context-length-exceeded (#48557), inconsistent with `/v1/chat/completions` returning 400 — API-contract issue for agent clients.
5. **Reasoning parser splits on first literal `</think>`** (#48791) — quoted think tags inside CoT truncate `reasoning_content` and leak remainder into `content`.
6. **Rust server `/generate` missing BOS** (#49519) — text prompts skip `add_bos_token` tokenizers on v0.5.22.
7. **Qwen4-Exp PLE BF16→FP8 miscast** (#49493) — n-gram shards cast with unit scale when `ple_embedding_dtype` set in config.
8. **GLM-4.7-Flash W4A16 MLA load failure** (#49369) — post-load step reads `kv_b_proj.weight` absent in packed layers.
9. **Scheduler crash on `pause_generation(abort)` + `release_memory_occupation`** after client disconnect (#44840).
10. **CI flakiness tracker (#42752)** — 192 comments; B200 cold-JIT timeouts (#49340) resolved.

## 6. What This Means for Application Developers
- **Pin your SGLang version carefully** if you're on Hopper: v0.5.23 has a measurable decode regression vs v0.5.22 for DeepSeek-V4.1-Flash + DSpark; hold v0.5.22 until #49336 is resolved.
- **Blackwell/SM120 users**: expect fa4 to fail on hybrid-extend for GLM-class models; set the triton attention backend explicitly until #42012 lands.
- **Agent harness authors**: two issues directly affect you — the `</think>` parser split (#48791) can corrupt reasoning output, and the `/v1/responses` 200-with-failed behavior (#48557) breaks error handling. The agent-prefix cacheability RFC ([#47763](https://github.com/sgl-project/sglang/issues/47763)) and UniBoost scheduler RFC ([#48519](https://github.com/sgl-project/sglang/issues/48519)) signal upcoming first-class support for multi-turn prefix reuse and fair scheduling under overload.
- **HiCache/Mooncake L3 users**: avoid `buffer_only` mode until #45989 is fixed — it can deadlock the scheduler under load.
- **Scoring workloads**: the multi-item-scoring host-sync removal (#49412) is a low-risk ~1.5× offline throughput win worth tracking for merge.

</details>

<details>
<summary><strong>llama.cpp</strong> — <a href="https://github.com/ggml-org/llama.cpp">ggml-org/llama.cpp</a></summary>

# llama.cpp Digest — 2026-11-11

## 1. Today's Highlights

A heavy release day (9 builds in 24h), dominated by multimodal and CUDA housekeeping: Kimi K3.1 vision support landed in mtmd, mtmd subcontexts now share a compute buffer, and CUDA graph state was moved out of `common.cuh` to cut rebuild fan-out. On the correctness side, two fixes target tool-calling robustness — control-character escaping in tool arguments and an `allOf` JSON-schema grammar bug — both directly relevant to agent frameworks.

## 2. Releases & Breaking Changes

- **b12262 — CUDA: skip graph compat check on graph reuse** ([#32004](https://github.com/ggml-org/llama.cpp/pull/32004)). Internal behavior change only; no API impact.
- **b12261 — mtmd: shared compute buffer across subcontexts** ([#31945](https://github.com/ggml-org/llama.cpp/pull/31945)). Adds load-time allocation for audio generation; watch memory footprint if you load multiple mtmd subcontexts.
- **b12257 — CUDA: graph/stream state moved out of `common.cuh`** ([#32001](https://github.com/ggml-org/llama.cpp/pull/32001)). A source-tree refactor; out-of-tree code that included `common.cuh` for `ggml_cuda_graph` / `ggml_cuda_stream_context` will break.
- **b12254 / b12253 — tool-calling JSON fixes** ([#31834](https://github.com/ggml-org/llama.cpp/pull/31834), [#31937](https://github.com/ggml-org/llama.cpp/pull/31937)). `allOf` with plain types no longer yields an empty-object grammar; raw control characters (e.g. newlines in pythonic tool args) are now escaped, so clients no longer receive invalid JSON.
- **b12252 — CANN**: fail-fast on unsupported buffer types ([#31270](https://github.com/ggml-org/llama.cpp/pull/31270)).
- **b12255 — plamo2**: stop renaming recurrent cache tensors ([#31984](https://github.com/ggml-org/llama.cpp/pull/31984)).

## 3. New Model & Hardware Support

- **Kimi K3.1 vision** added to mtmd ([b12259, #31980](https://github.com/ggml-org/llama.cpp/pull/31980)); also fixes preprocessing for both K2.5 and K3.1 and the projector MLP.
- **Vulkan**: tiled-transpose concat shader for Qwen delta-net conv-state paths ([b12250, #30591](https://github.com/ggml-org/llama.cpp/pull/30591)) — avoids the generic concat kernel path.
- **OpenCL (Adreno)**: decomposed flash-attention proposal ([#32046](https://github.com/ggml-org/llama.cpp/pull/32046)) — fused FA is resource-constrained on certain Adreno configs.
- **Hexagon**: integer HMX for Q4_0 matmul + FA prefill on v73 SoCs lacking FP16 HMX ([#29740](https://github.com/ggml-org/llama.cpp/pull/29740)); addresses the SM7750 garbage-output bug.
- **HIP**: ROCm minimum bumped to 6.3 ([#31617](https://github.com/ggml-org/llama.cpp/pull/31617)) — note for build pipelines.
- In progress: **granite-switch** LoRA-delta gating ([#32047](https://github.com/ggml-org/llama.cpp/pull/32047)), Zaya1 8B draft ([#23112](https://github.com/ggml-org/llama.cpp/pull/23112)).

## 4. Performance & Optimization

- **granite-switch**: gating adapter deltas on batch token mix reports **11.1× batch-1 decode** by eliminating 896 unconditional `mul_mat_id` ops per graph on the 30.6B model ([#32047](https://github.com/ggml-org/llama.cpp/pull/32047), open).
- **CUDA**: graph compat check skipped on reuse ([#32004](https://github.com/ggml-org/llama.cpp/pull/32004)); header split reduces incremental rebuild scope substantially for CUDA TUs ([#32001](https://github.com/ggml-org/llama.cpp/pull/32001)).
- **mtmd**: shared compute buffer reduces per-subcontext allocation ([#31945](https://github.com/ggml-org/llama.cpp/pull/31945)).
- **Vulkan**: coopmat1 FA row-tiling RFC packing query tokens + GQA heads to share K/V reads ([#30190](https://github.com/ggml-org/llama.cpp/pull/30190), WIP).

## 5. Stability & Regressions

Ranked by severity:

1. **Critical — speculative decoding diverges on quantized targets** ([#25618](https://github.com/ggml-org/llama.cpp/issues/25618), 40 comments). Greedy (`temp=0, top_k=1`) draft-mtp/dspark output differs from vanilla on Q4_K_M targets; bf16 matches. Ngram speculation unaffected. No fix PR cited — avoid draft-model spec decode on quantized targets in correctness-sensitive paths.
2. **Critical (legacy) — segfault in speculative decoding, 405B + 8B draft** ([#9949](https://github.com/ggml-org/llama.cpp/issues/9949)).
3. **High — DFlash spec decode KV contamination** ([#32045](https://github.com/ggml-org/llama.cpp/pull/32045), fix open). Rejected draft positions linger in cache, causing Metal compute errors after ~20 tokens. Workaround: disable DFlash until merged.
4. **High — DeepSeek V4 / Qwen4-Exp embeddings crash** on OOB read from MTP hidden-state width mismatch ([#32044](https://github.com/ggml-org/llama.cpp/pull/32044), fix open).
5. **Medium — SYCL MoE + `-sm layer` stalls** with 40 GB MoE cache on dual Arc Pro B60 ([#31510](https://github.com/ggml-org/llama.cpp/issues/31510)); nondeterministic logprobs on SYCL MoE ([#31206](https://github.com/ggml-org/llama.cpp/issues/31206)).
6. **Medium — Vulkan/Adreno driver failures**: `vkCreateComputePipelines` `VK_ERROR_UNKNOWN` tied to shaderc/NDK SPIR-V drift ([#28635](https://github.com/ggml-org/llama.cpp/issues/28635)); Adreno 840 crash in `vkGetDeviceFaultInfoEXT` on 27B TQ1_0 ([#30649](https://github.com/ggml-org/llama.cpp/issues/30649)).
7. **Medium — SM70 V100 CUDA FA crash** on non-standard head dims ([#28661](https://github.com/ggml-org/llama.cpp/issues/28661)).
8. **Low — llama-server accepts missing/incorrect Content-Type** ([#31979](https://github.com/ggml-org/llama.cpp/issues/31979)); `/v1/responses` rejects `summary: null` on reasoning items ([#29159](https://github.com/ggml-org/llama.cpp/issues/29159), closed).

## 6. What This Means for Application Developers

- **Tool calling is getting safer**: b12253 and b12254 close two paths that produced invalid JSON to clients — upgrade if you parse tool arguments strictly (you should) or use `allOf` schemas with primitive types.
- **Don't trust speculative decoding uniformly**: if you serve quantized weights and rely on draft-model spec decode for latency, greedy outputs may diverge. Pin to vanilla decoding or bf16 targets for eval/replay workloads; track #25618.
- **DFlash has a known cache bug in flight** ([#32045](https://github.com/ggml-org/llama.cpp/pull/32045)); gate it behind a flag until merged.
- **Multimodal pipeline improvements** (shared mtmd buffers, Kimi K3.1 vision, audio capability surfacing in Discover via [#31837](https://github.com/ggml-org/llama.cpp/pull/31837)) make multi-model servers cheaper to run — re-measure VRAM headroom if you load several mmproj models.
- **Build hygiene**: HIP users need ROCm ≥ 6.3; CUDA out-of-tree integrations must stop depending on `common.cuh` for graph/stream types.

</details>

<details>
<summary><strong>Ollama</strong> — <a href="https://github.com/ollama/ollama">ollama/ollama</a></summary>

# Ollama Digest — 2026-11-11

## 1. Today's Highlights
Ollama shipped release candidate v0.40.17-rc0, headlined by a fix that uses mmap for Vulkan integrated GPUs — a meaningful memory-footprint win for shared-memory iGPUs. In parallel, a version bump to llama.cpp b12257 and MLX/MLX-C landed, and the MLX runner gained draft-disabling for benchmarking plus concurrent-generation support now in review. Issue traffic remains dominated by Vulkan/MLX memory regressions and Cloud API limits.

## 2. Releases & Breaking Changes
- **v0.40.17-rc0** — bundles ChatGPT CLI restore/graceful-restart improvements ([#19583](https://github.com/ollama/ollama/pull/19583)), thinking-metadata for OpenCode launch settings ([#19409](https://github.com/ollama/ollama/pull/19409)), and mmap-for-Vulkan-iGPU ([#19640](https://github.com/ollama/ollama/pull/19640)).
- **llama.cpp bump to b12257** ([#19646](https://github.com/ollama/ollama/pull/19646)) and **MLX/MLX-C version bump** ([#19647](https://github.com/ollama/ollama/pull/19647)).
- No documented API breakages today. Note `--runner` variant selection is being exposed (mlx/gguf) with legacy runner names still accepted ([#19644](https://github.com/ollama/ollama/pull/19644)).
- **CLI behavior change (closed)**: `-v/--version` spurious "warning" text reported fixed ([#19613](https://github.com/ollama/ollama/issues/19613)).

## 3. New Model & Hardware Support
- **Nemotron 3.5 Bolt 74B-A9B** on MLX: per-layer expert configs, LiLiCorr speculative decoding, prequantized affine 3-bit import with mixed-precision expert stacking ([#19229](https://github.com/ollama/ollama/pull/19229)).
- **Vulkan integrated GPUs**: mmap path avoids doubling footprint from CPU-resident tensors in host buffers ([#19640](https://github.com/ollama/ollama/pull/19640)).
- **Unix domain sockets** via `OLLAMA_HOST=unix:///abs/path` with `0600` permissions ([#19467](https://github.com/ollama/ollama/pull/19467)).
- Decision-model (`/v1/systemone`) support: long-request handling for MLX decision models with 413 on oversized payloads ([#19408](https://github.com/ollama/ollama/pull/19408)).

## 4. Performance & Optimization
- **MLX concurrent generation** via `OLLAMA_NUM_PARALLEL`; shared projection/FFN work across ready requests — improves first-token latency ([#19197](https://github.com/ollama/ollama/pull/19197)).
- **Speculative decoding benchmarking**: `draft_num_predict=0` disables drafting per request (default stays 4) ([#19391](https://github.com/ollama/ollama/pull/19391)).
- **`ollama load MODEL`** preload command proposed to warm models without an interactive session ([#16207](https://github.com/ollama/ollama/pull/16207)).
- **`max_completion_tokens`** now honored in OpenAI-compatible chat completions, preventing over-generation vs. client budget ([#19168](https://github.com/ollama/ollama/pull/19168)).
- **Unix socket** transport reduces overhead for local co-located clients.

## 5. Stability & Regressions (ranked)
1. **MLX KV-cache memory growth** per-request on dense models; not released short of restart (closed) ([#17875](https://github.com/ollama/ollama/issues/17875)).
2. **Windows RTX 5090 runner disconnect** during tool calling → HTTP 500 `/tokenize` (open) ([#19439](https://github.com/ollama/ollama/issues/19439)).
3. **Vulkan regressions**: AMD Radeon 780M OOM/device-lost ≥0.32.10 (closed) ([#17748](https://github.com/ollama/ollama/issues/17748)); Intel Iris Xe KV-cache alloc failure on 0.33.3/0.34.2 vs. working 0.32.6 (closed) ([#18531](https://github.com/ollama/ollama/issues/18531)) — both addressed by the mmap Vulkan fix.
4. **qwen3next layer-2 missing attn_qkv/attn_gate** projections (closed) ([#15372](https://github.com/ollama/ollama/issues/15372)).
5. **gemma4 + `think:false`** returns empty reply after tool response (open) ([#18861](https://github.com/ollama/ollama/issues/18861)).
6. **Cloud `/api/chat` 400** at ~32 MiB payload, undocumented limit (open) ([#19597](https://github.com/ollama/ollama/issues/19597)).
7. **Unicode corruption** in olmo3 tool-call args (Zürich/日本/emoji) — byte-copy bug (open) ([#18906](https://github.com/ollama/ollama/pull/18906)).

## 6. What This Means for Application Developers
- **Vulkan iGPU deployments** (mini-PCs, Framework Desktop, laptops) should see materially lower load-failure rates once v0.40.17 ships — worth pinning for constrained hardware.
- **Agents relying on OpenAI-compat**: adopt `max_completion_tokens` for strict token budgets now that it's honored; long tool-calling sessions on Windows + RTX 50-series remain risky (track #19439).
- **Cloud/API clients** handling large base64 images must stay under ~32 MiB per request until the limit is documented.
- **Non-ASCII tool arguments** (i18n agents, multilingual tools) are currently unsafe on olmo3-family models — validate/sanitize until #18906 merges.
- **Local-app builders** can move to Unix sockets for co-located clients to cut TCP overhead and tighten access control; **benchmarkers** can toggle MLX drafting per request to quantify speculative-decoding gains.

</details>

<details>
<summary><strong>LiteLLM</strong> — <a href="https://github.com/BerriAI/litellm">BerriAI/litellm</a></summary>

# LiteLLM Digest — 2026-11-11

## 1. Today's Highlights

The fallout from the October 2026 supply-chain incident (a malicious `1.107.0` impersonating the project under a lookalike `Berri-AI/litellm` repo) continues to dominate attention: the PyPI and GHCR artifacts have been yanked, and the project is now shipping signed LTS releases with cosign verification instructions front and center on every new tag. Today's release train adds a fresh LTS patch (`v1.108.5`) plus maintenance backports to `v1.104.9` and `v1.103.11`. Meanwhile, the open-issue queue (196 updated in 24h) is heavy on correctness bugs in the Anthropic `/v1/messages` translation layer and the token-bucket rate limiter.

## 2. Releases & Breaking Changes

- **v1.108.5 (LTS)** — New LTS patch. The `lts` image tag `ghcr.io/berriai/litellm:lts` now tracks this release; operators pinned to `lts` should expect the normal patch-level drift. [Release](https://github.com/BerriAI/litellm/releases/tag/v1.108.5)
- **v1.104.9** and **v1.103.11** — Maintenance tags on the older LTS lines, carrying the cosign signing-key notice (key introduced in commit [`e82a3c6`](https://github.com/BerriAI/litellm/commit/e82a3c646908ba0a6ee0beddc4310d7a6cac4065)). If you verify images in CI, update your pinned public key path. [v1.104.9](https://github.com/BerriAI/litellm/releases/tag/v1.104.9) · [v1.103.11](https://github.com/BerriAI/litellm/releases/tag/v1.103.11)
- **Breaking (in review): single shipping artifact.** PR [#43332](https://github.com/BerriAI/litellm/pull/43332) collapses ten Dockerfiles into one non-root image (uid 65532) with a single entrypoint; PR [#43330](https://github.com/BerriAI/litellm/pull/43330) removes `litellm-helm` in favor of one chart that runs monolith or componentized. These are `!`-flagged — expect deploy-manifest churn for anyone pinning per-component images.
- **Breaking (in review): Databricks per-deployment OAuth M2M** — PR [#50675](https://github.com/BerriAI/litellm/pull/50675) changes credential resolution semantics for `litellm_params` deployments (one client_id/secret per deployment rather than a global pair).
- **Security status:** `1.107.0` remains removed from PyPI and the Docker `latest`/`main-latest`/`main-stable` tags; see [#47826](https://github.com/BerriAI/litellm/issues/47826). Treat any cached `1.107.0` artifact as untrusted.

## 3. New Model & Hardware Support

- **Azure AI / Mistral:** `azure_ai/mistral-large-4-latest` added to the model catalog from the Azure Models API, priced from Mistral's published page. [PR #51047](https://github.com/BerriAI/litellm/pull/51047)
- **Azure Responses API parity:** the chat-completions↔Responses bridge now replays message `phase` on `azure/responses/` models (previously OpenAI-only), which matters for multi-turn `gpt-5.6` agent flows. [PR #51059](https://github.com/BerriAI/litellm/pull/51059)
- No new local backends (CUDA/ROCm/Metal) or quantization formats in today's window.

## 4. Performance & Optimization

- **Rust hot path for `/v1/messages`:** PR [#51050](https://github.com/BerriAI/litellm/pull/51050) moves the easy Python-fallback detectors to the Rust path under `LITELLM_RUST=1` — the bottom of a 7-PR stack; expect incremental detector offload over the coming days.
- **Type-check gate:** PR [#50711](https://github.com/BerriAI/litellm/pull/50711) clears 1,142 `basedpyright` `Any` errors across 182 files (no behavior change); a follow-up tightens the CI cap to prevent regression.
- **Known perf hazard (unfixed):** sync `token_counter` on the async router pre-call path blocks the event loop when `enable_pre_call_checks=True` with `max_input_tokens` deployments — [#36174](https://github.com/BerriAI/litellm/issues/36174) (closed stale, no fix linked).

## 5. Stability & Regressions

Ranked by blast radius:

1. **Rate limiter correctness (high).** Two independent TPM/RPM bugs: with in-memory cache, `tpm_limit` never rolls and locks keys out until `/key/update` ([#47340](https://github.com/BerriAI/litellm/issues/47340)); with v3 limiter + Redis TPM reservation, the token counter outlives its window under upstream 429s and rejects below limit ([#49675](https://github.com/BerriAI/litellm/issues/49675)). Also: per-customer RPM limits stop applying once virtual keys are cached ([#39713](https://github.com/BerriAI/litellm/issues/39713), closed).
2. **Spend accounting (high for FinOps).** Streamed completions logged at $0 when upstream reports `usage.cost: 0` ([#48937](https://github.com/BerriAI/litellm/issues/48937), closed); `response_cost` null for `anthropic` custom provider when pricing row lives under another provider ([#47675](https://github.com/BerriAI/litellm/issues/47675), closed). Budget enforcement is downstream of these.
3. **Anthropic `/v1/messages` translation (medium-high for Claude Code users).** `thinking_delta` streamed inside a text block + duplicate `message_start` → empty content in the Anthropic SDK ([#32357](https://github.com/BerriAI/litellm/issues/32357), closed); `reasoning_effort: "none"` deletes thinking config instead of disabling it on default-on Claude models ([#47924](https://github.com/BerriAI/litellm/issues/47924), closed); SSE keepalive commits a 200 before upstream failure, so errors can't be raised ([#49711](https://github.com/BerriAI/litellm/issues/49711), open); `chatgpt/` provider drops encrypted reasoning across `/v1/messages` tool turns ([#49046](https://github.com/BerriAI/litellm/issues/49046), closed).
4. **Tool routing (medium).** JSON-defined OpenAI-like providers silently drop `tools` when the model isn't in the catalog ([#48145](https://github.com/BerriAI/litellm/issues/48145), open — regression vs 1.81/1.106); MCP execution failures drop `is_error` and don't reach `tool_result` ([#49359](https://github.com/BerriAI/litellm/issues/49359), open); Mistral drops named `tool_choice` ([#39736](https://github.com/BerriAI/litellm/issues/39736), closed); Ollama `tool_calls` parse failure ([#24091](https://github.com/BerriAI/litellm/issues/24091), open).
5. **Observability correctness (medium).** `gen_ai.response.model` carries the alias, not the serving model ([#51029](https://github.com/BerriAI/litellm/issues/51029), open — filed today). OTel v2 per-team metadata keys land in [#50813](https://github.com/BerriAI/litellm/pull/50813).
6. **Other:** Vertex/Gemini cache reordering + dropped `system_instruction` when `cache_control` isn't a prefix ([#47046](https://github.com/BerriAI/litellm/issues/47046), open); `COHERE_API_BASE` ignored for embeddings ([#50248](https://github.com/BerriAI/litellm/issues/50248), open); `token_counter` rejects image `detail: "original"`, skipping context-window checks ([#47327](https://github.com/BerriAI/litellm/issues/47327), open).

Fix PRs in flight: management-route 403 for `PATCH /model/{id}/update` ([#51060](https://github.com/BerriAI/litellm/pull/51060)); zero-cost budget waiver judged by alias target ([#51058](https://github.com/BerriAI/litellm/pull/51058)); per-user spend breakdown backport to LTS ([#51043](https://github.com/BerriAI/litellm/pull/51043)); MCP backport to `lts/1.108.x` ([#51045](https://github.com/BerriAI/litellm/pull/51045)); Vertex Gemini image/thinking/priority token billing ([#51017](https://github.com/BerriAI/litellm/pull/51017)); CI tzdata fix for failing unit runs ([#51057](https://github.com/BerriAI/litellm/pull/51057)).

## 6. What This Means for Application Developers

- **Pin and verify.** If you consume images, move to `:lts` (now `1.108.5`) and wire `cosign verify` into your deploy gate using the `e82a3c6` key. Audit any layer cache or lockfile still resolving `1.107.0`.
- **Don't trust streaming spend numbers yet.** If you meter costs from `LiteLLM_SpendLogs`, cross-check against provider invoices — streamed traffic may be recorded at $0 under `usage.cost: 0` ([#48937](https://github.com/BerriAI/litellm/issues/48937)). Budget enforcement built on those logs will under-count.
- **TPM/RPM limits are not reliable as a hard quota** if you run in-memory cache without Redis: keys can wedge until `/key/update` ([#47340](https://github.com/BerriAI/litellm/issues/47340)). For production multi-tenant gateways, use Redis-backed limiting and monitor for premature 429s under upstream throttling ([#49675](https://github.com/BerriAI/litellm/issues/49675)).
- **Claude Code / Anthropic-SDK clients:** several `/v1/messages` adapter bugs (reasoning encoding, `reasoning_effort: "none"`, keepalive-then-error) remain open or only recently closed — test agentic tool loops and SSE failure paths against your pinned version before upgrading.
- **Custom providers:** if you register tool-using models via JSON `provider` config rather than the catalog, verify `tools` actually reach the backend after upgrading past 1.81 ([#48145](https://github.com/BerriAI/litellm/issues/48145)) — this is a silent failure mode.
- **Deploy manifests:** if you use the Helm chart or per-component images, start tracking [#43330](https://github.com/BerriAI/litellm/pull/43330)/[#43332](https://github.com/BerriAI/litellm/pull/43332) now; the consolidation to one chart and one non-root image is a breaking change for overrides and init containers.

</details>

<details>
<summary><strong>Unsloth</strong> — <a href="https://github.com/unslothai/unsloth">unslothai/unsloth</a></summary>

# Unsloth Digest — 2026-11-11

## Today's Highlights
No new releases dropped in the last 24h, but the Studio surface is moving fast: a large batch of PRs landed or refreshed around image editing (external `/images/edits` support, MiniMax-H3-Image), vLLM/SGLang token-budget fixes, and reverse-proxy/Cloudflare tunneling support. A regression in Qwen hybrid-model MTP draft acceptance on the new UD 3.1 GGUFs is worth flagging for anyone running speculative decoding. Meanwhile the bug queue is dominated by desktop/Studio UX issues (glass theme, model sizing, storage errors) rather than training-critical failures.

## Releases & Breaking Changes
None in the last 24h.

- Note (behavioral, merged/closed): PR #17206 moved Studio's PDF stack (PyMuPDF, pymupdf4llm) to on-demand installation — first-use of PDF/ebook features will now trigger a dependency install rather than being present at install time. [PR #17206](https://github.com/unslothai/unsloth/pull/17206)

## New Model & Hardware Support
- **MiniMax-H3-Image (text-to-image + reference editing, 1–16 reference images)**: new Studio Images pipeline, using diffusers transformer/VAE classes when available. [PR #17453](https://github.com/unslothai/unsloth/pull/17453)
- **External image-edit models via OpenAI-compatible `/images/edits`**: connected image models can now be used for edits, not just generation (reported with llama-swap → stable-diffusion.cpp / FLUX.2-klein-4B-GGUF). [PR #17450](https://github.com/unslothai/unsloth/pull/17450) · [PR #17448](https://github.com/unslothai/unsloth/pull/17448)
- **Desktop reverse-proxy / custom-domain support**: allowlisted reverse proxies (e.g., Cloudflare Named Tunnel with custom domain) can now serve the packaged web UI; previously returned 404 on `X-Forwarded-Host`. [PR #17435](https://github.com/unslothai/unsloth/pull/17435) (fixes [#17420](https://github.com/unslothai/unsloth/issues/17420))
- **ROCm**: gfx1031 (RX 6700 XT) is detected as RDNA 2 at startup but segfaults in PyTorch on first GPU op — reported and closed; treat RDNA 2 as unvalidated for the ROCm Docker path until a fix is confirmed. [#17147](https://github.com/unslothai/unsloth/issues/17147)
- **Intel Arc 140V**: image-generation models fail to load (closed). [#17137](https://github.com/unslothai/unsloth/issues/17137)

## Performance & Optimization
- **Context-window budgeting on vLLM/SGLang**: tool requests and `/v1/completions` now lower `max_tokens` to fit the window instead of returning `400 context_length_exceeded` when prompt fits but prompt+max_tokens doesn't. [PR #17426](https://github.com/unslothai/unsloth/pull/17426)
- **Speculative decoding regression (important)**: Qwen3.8-Flash-Next UD 3.1 GGUFs paired with existing MTP draft heads show draft acceptance dropping from ~74% to 56–66% at production temperature on Ryzen AI Max+ 395. If you rely on MTP for latency, pin to the prior quant until 3.1-matched heads ship. [#17402](https://github.com/unslothai/unsloth/issues/17402)
- **Studio observability**: tok/s badges extended to API providers via synthesized `timings` from streamed usage chunks. [PR #16506](https://github.com/unslothai/unsloth/pull/16506)
- **Real token usage for custom OpenAI-compatible connections**: opt-in `stream_options` per connection, enabling accurate context bars (previously estimate-only). [PR #16505](https://github.com/unslothai/unsloth/pull/16505)
- **GGUF weight-placement readout**: always-visible placement for loaded GGUF (previously hidden behind an off-by-default switch). [PR #17449](https://github.com/unslothai/unsloth/pull/17449)
- **Per-model `max_tokens` defaults**: feature request to infer defaults from advertised context length instead of a flat 32K. [#17444](https://github.com/unslothai/unsloth/issues/17444)

## Stability & Regressions
Ranked by severity:

1. **ROCm segfault on gfx1031** — startup check passes, PyTorch crashes on first GPU op. Closed, but verify on your hardware before deploying. [#17147](https://github.com/unslothai/unsloth/issues/17147)
2. **Qwen hybrid + MCP tool loop**: repeated full prompt processing across turns after tool use with large MCP tool catalogs and 64K context (llama-server, deferred-schema path). Open. [#17015](https://github.com/unslothai/unsloth/issues/17015)
3. **Pipeline-parallel MoE router aux loss divergence (CPU backend)**: with `UNSLOTH_FORCE_CPU=1` and grad accumulation > 1, loss diverges from single-process; repo parity test reproduces. Open. [#17054](https://github.com/unslothai/unsloth/issues/17054)
4. **Studio web-fetch HTML→Markdown corruption**: bare `&` gains stray `;`, escaped code escapes its block, malformed `&#` can leave raw HTML — relevant if you feed fetched pages into prompts. Closed. [#17199](https://github.com/unslothai/unsloth/issues/17199)
5. **MLX vision chats stop compacting once an image is attached**; conversation eventually fails with "Message too long". Fix PR open. [PR #17424](https://github.com/unslothai/unsloth/pull/17424)
6. **External connection image edits disabled** for capable servers. Fix PRs open. [PR #17448](https://github.com/unslothai/unsloth/pull/17448) · [PR #17450](https://github.com/unslothai/unsloth/pull/17450)
7. **LAN bind to network-bridge IP fails** (desktop). Closed. [#17017](https://github.com/unslothai/unsloth/issues/17017)
8. **Upgrade path errors** on desktop app — closed, but watch for recurrence. [#17375](https://github.com/unslothai/unsloth/issues/17375)
9. **Custom-connection video upload blocked** (OpenAI-compatible connections). Closed. [#17295](https://github.com/unslothai/unsloth/issues/17295)
10. **Lesser Studio UX bugs (closed)**: model size mismatch between Hub and picker [#17074](https://github.com/unslothai/unsloth/issues/17074); duplicate glass-material setting [#17162](https://github.com/unslothai/unsloth/issues/17162); mangled UI on Hub with glass theme [#17417](https://github.com/unslothai/unsloth/issues/17417); `Regenerate title` copying user text [#17332](https://github.com/unslothai/unsloth/issues/17332); background-image storage error [#17365](https://github.com/unslothai/unsloth/issues/17365); `nul` files written into CWD [#16991](https://github.com/unslothai/unsloth/issues/16991).
11. **Correctness in export/calibration paths**: PDF export of long unbroken text (CJK/Thai/long tokens) truncated — fix PR open [PR #17425](https://github.com/unslothai/unsloth/pull/17425); diffusion caption JSONL read via `splitlines()` breaks on U+2028/U+2029/U+0085 and silently drops rows — fix PR open [PR #17436](https://github.com/unslothai/unsloth/pull/17436); `push_to_hub_gguf(tags=...)` never wrote tags (wrong `HfApi` call, swallowed exception) — fix PR open [PR #17438](https://github.com/unslothai/unsloth/pull/17438).

## What This Means for Application Developers
- **If you use MTP/speculative decoding on Qwen3.8-Flash-Next**: pin GGUF quant versions together with draft heads. Mixing UD 3.1 weights with pre-3.1 heads measurably degrades acceptance (~74% → 56–66%) and therefore latency. Watch [#17402](https://github.com/unslothai/unsloth/issues/17402).
- **If you serve via vLLM/SGLang with tools**: upgrade for the `max_tokens`-fitting fix [PR #17426](https://github.com/unslothai/unsloth/pull/17426); today's `400 context_length_exceeded` failures on tool calls are a real agent-loop breaker.
- **If you deploy Unsloth Desktop behind Cloudflare or a reverse proxy**: [PR #17435](https://github.com/unslothai/unsloth/pull/17435) unblocks custom-domain Named Tunnels; until merged, expect 404s on `X-Forwarded-Host` requests.
- **If you build on custom OpenAI-compatible gateways**: enable the new per-connection usage-streaming opt-in [PR #16505](https://github.com/unslothai/unsloth/pull/16505) to get accurate context/token accounting — but only for gateways that accept `stream_options`.
- **If you're on ROCm RDNA 2 or Intel Arc**: treat these as unvalidated for the Docker/Studio inference path; gfx1031 segfaults at first op and Arc 140V fails image-model load.
- **If you export chats or curate diffusion datasets**: the PDF wrap [PR #17425](https://github.com/unslothai/unsloth/pull/17425) and caption-parsing [PR #17436](https://github.com/unslothai/unsloth/pull/17436) fixes prevent silent data loss — worth tracking if you batch these operations.

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar).*