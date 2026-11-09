# AI Infrastructure Digest 2026-11-10

> Generated: 2026-11-09 23:19 UTC | Projects covered: 6

- [vLLM](https://github.com/vllm-project/vllm)
- [SGLang](https://github.com/sgl-project/sglang)
- [llama.cpp](https://github.com/ggml-org/llama.cpp)
- [Ollama](https://github.com/ollama/ollama)
- [LiteLLM](https://github.com/BerriAI/litellm)
- [Unsloth](https://github.com/unslothai/unsloth)

---

## Cross-Project Comparison

# Cross-Project Comparison Report — AI Infrastructure Ecosystem
**Digest Date: 2026-11-10**

---

## 1. Ecosystem Overview

Today's activity confirms a two-tier infrastructure landscape: **serving engines (vLLM, SGLang) are in a hardware-enablement trench war** — SM120/GB10 consumer Blackwell and MI355X/MI450 now consume the majority of issue and PR bandwidth, with correctness regressions (silent empty completions, silent kernel output corruption) outnumbering feature work. The **local/edge tier (llama.cpp, Ollama, Unsloth)** is shipping at high velocity but is fragmenting along backends (Metal, Vulkan, SYCL, ROCm, OpenCL, XDNA), with reproducibility and KV-cache correctness as the recurring weak point. **Gateways (LiteLLM)** are consolidating on metering correctness — a stack of closed budget/rate-limit bypass bugs and a native Rust-backed `/v1/messages` path signal that billing accuracy is now the primary competitive axis. Across all layers, **speculative decoding is maturing into the default throughput lever** while simultaneously generating the largest cluster of correctness regressions (EAGLE/MTP/DFlash/DFlash2/DSpine divergence, acceptance collapse at TP>1). Notably absent: no releases from vLLM, SGLang, or Unsloth in the last 24h — the day's work is integration and bug-fixing, not shipping.

---

## 2. Activity Comparison

| Project | Layer | Issues Referenced | PRs Referenced | Release Status (24h) | Dominant Theme |
|---|---|---|---|---|---|
| **vLLM** | Serving engine | ~14 (6 issues + 8 regression items) | ~10 | **None** | Blackwell (SM120) readiness gaps; Marlin→Humming migration; spec-decode correctness |
| **SGLang** | Serving engine | ~13 | ~13 | **None** (regression flagged v0.5.22→v0.5.23) | CI flakiness; SM120 coverage; AMD MI355X/MI450 kernel work |
| **llama.cpp** | Local runtime | ~18 | ~18 | **Yes — 18 builds (b12219–b12236)** | Backend correctness (Metal FA OOB); audio resampling; JSON-schema server fix |
| **Ollama** | Local runtime | ~10 | ~7 | **Yes — v0.40.16-rc0** | OpenAI-compat surface; VRAM accounting/GPU splitting fix (#19632) |
| **LiteLLM** | Gateway | ~20 | ~10 | **Yes — v1.108.4** (cosign-signed images) | Cost/budget accounting correctness; native `/v1/messages` Rust path |
| **Unsloth** | Fine-tuning + local serving | ~12 | ~10 | **None** (typer 0.29 Windows breakage pinned) | Decode CUDA graphs (2–8× T4); Studio OpenAI-compat gateway bugs |

**Release cadence read:** llama.cpp is the only project shipping continuously (18 builds/day). LiteLLM ships on a weekly-ish cadence with a pre-announced 2.0.0 breaking change. vLLM/SGLang/Unsloth are in a feature-stabilization interlude — a pattern consistent with pre-release hardening cycles, and the highest-risk window for consumers pinning "latest."

---

## 3. Model Support Race

| Model / Architecture | vLLM | SGLang | llama.cpp | Ollama | LiteLLM | Unsloth |
|---|---|---|---|---|---|---|
| **DeepSeek-V4.1-Flash** | ⚠️ SM120 CUDA-graph broken (#56892) | ⚠️ SM90 decode regression v0.5.23 (#49336) | — | — | — | — |
| **Kimi K3.1** | ✅ PR #66532 (NVIDIA decode + ROCm TP8) | ✅ AITER MoE sort for K3 on MI355X (#43126) | ✅ Vision (#31980) | — | — | — |
| **Qwen3.8 / 3.5 / 3.6** | ⚠️ Flash-Next long-context bug (#62776); drafter blockers (#56088) | ⚠️ GDN prefill PRs (#38806, #48398) | — | ✅ Parallel-request unlock PR #17144 (3.5/MoE) | ✅ Tokenizer fix for custom deployments (#50734) | ✅ Decode graphs for Qwen3.5/3.6 + GDN hybrid (#17324, #17329) |
| **Gemma 4** | — | — | ✅ Audio streaming (cosine 0.993–0.997 vs non-stream) | — | — | ✅ Decode graph (#17329) |
| **MiniMax M3 / H3** | ✅ MSA indexer CP (#61929) | ✅ FP8 video VAE for H3 (#45180) | — | — | — | — |
| **Claude 5.x / Fable 5.2** | — | — | — | — | ✅ Vertex + Bedrock Converse support (#50742); prefill-400 fix PR #50701 | — |
| **GLM-5.x / 5.3-Flash** | — | ⚠️ SM120 fa4 crash (#42012) | — | — | ⚠️ `glm-5.2[1m]` unknown-model (#32218) | ✅ Decode graph (GLM-4 line) |
| **DFlash / DSpine drafters** | ⚠️ Collapses at TP>1 (#66387) | ✅ DSpark KV-write fix (#49386) | ✅ DFLASH2 (#31904), Tencent DSpine (#31941) | — | — | — |
| **Nemotron-H / 3.5 ASR** | ✅ Elastic widths (#66665); ASR request open | — | — | — | — | — |
| **Granite** | — | — | ✅ NVFP4 scales (b12223) | — | — | ✅ Decode graph |

**Who's ahead:** **vLLM** leads breadth (Kimi K3.1, MiniMax M3, Nemotron-H, DeepSeek-V4.1) but carries the most model-specific regressions on new hardware. **llama.cpp** leads long-tail architecture support (Gemma 4 audio, DFLASH2/DSpine, Granite NVFP4) and is the de facto reference for new drafter formats. **Unsloth** is the surprise second-place in *Qwen3.5/3.6 and Gemma 4 inference acceleration*, encroaching on serving-engine territory from the training side. **LiteLLM** is the only project adding cloud-provider model parity (Fable 5.2 on Vertex/Bedrock) — its race is at the API-shape layer, not the kernel layer. **Ollama** has the narrowest new-model surface and is still gated on Qwen3.5 MoE parallelism (`numParallel=1`).

---

## 4. Performance Frontier

| Optimization Domain | vLLM | SGLang | llama.cpp | Ollama | LiteLLM | Unsloth |
|---|---|---|---|---|---|---|
| **KV cache** | FlashInfer workspace shrink → **+21% KV capacity** (#63399); KV-offload DMA thresholds (#57555); saturated-admission TPOT inflation (#64460) | Supernode L1+L2 KV sharing roadmap (#43098); prompt-cache restore correctness | **Growable KV opt-in** (`UNSLOTH_GROWABLE_KV=1`) avoids StaticCache over-allocation | ⚠️ Vulkan prompt-cache **~30× slowdown on 2nd request** (#19201, closed) | Cache-read billing fix (#37823) | Growable KV cache (opt-in) |
| **Batching / scheduling** | HiSparse coordinator batching eliminates tens-of-ms stalls (#65607); admission control | PP scheduler stale DSpark fix (#49386); Agentic Cache Scheduling roadmap | — | sched.go `numParallel=1` unlock for Qwen3.5-MoE (#17144) | Wasted Redis counter writes at high throughput (#31880) | — |
| **Quantization** | Marlin→Humming migration (#65560); NVFP4 MoE silent-fail (#51525); AutoAWQ 2.5× slower on RDNA (#63455) | MXFP4 attention on NPU (#43367) | NVFP4 Granite scales (b12223); OpenCL int8 fused KQ (#30249) | — | — | — |
| **Distributed serving** | TP-rank autotune sync flag (#66526); Kimi K3.1 ROCm TP8 | Ring all-reduce + `2shot_push` for MI450 (#49377); DCP indexer row-splits (#47309) | — | Multi-GPU split accounting fix (#19632) | Native `/v1/messages` billing for abandoned streams (#50425) | — |
| **Kernels** | Triton autotune determinism; Eager attention view re-derivation (#64572) | Single-pass Triton/Gluon DSA top-k **1.5–1.9×** on MI355X (#47872); AITER asm sparse MLA | Metal FA OOB write patched (b12221); x86 BF16 dot product (#31878); ARM NEON IQ4_NL repack (#30165) | — | — | — |
| **Decode acceleration** | Spec-decode top-k/top-p at target logits (#66534) | EAGLE per-draft-position repetition penalty fix (#49404) | Adreno Vulkan scalar FA cap (#30829); MMVQ cutoff table (#28090) | — | — | **CUDA graphs 2–8× on T4** (#16587), model-specific extensions |

**Concentration read:** Three distinct battles are visible. **(a) AMD is a first-class performance target** — SGLang has the deepest MI355X/MI450 kernel pipeline (DSA top-k, ring all-reduce, AITER asm MLA); vLLM is chasing with ROCm AITER MLA (but hit a closed critical silent-corruption bug, #66141). **(b) KV-cache capacity is the universal bottleneck** — vLLM recovers it via workspace tuning, Unsloth via growable allocation, SGLang via supernode sharing; the Vulkan prompt-cache 30× regression in Ollama is the cautionary counterexample. **(c) Decode-graph adoption is spreading outward from training frameworks** — Unsloth's default-on CUDA graphs delivering 2–8× on T4 is the single largest headline throughput claim in the digest set, and it directly competes with serving engines for the "small-model, high-throughput" use case.

---

## 5. Layer Positioning

| Layer | Projects | Core Function | This Week's Strategic Signal |
|---|---|---|---|
| **Serving engine (datacenter)** | **vLLM, SGLang** | High-throughput, multi-GPU, PagedAttention-class serving; disaggregated prefill/decode (PD), TP/PP/DP | Both are **hardware-enablement constrained**: SM120/GB10 and MI-side gaps dominate. Feature velocity is downstream of backend bring-up. Differentiation is shifting to *correctness guarantees* (determinism flags, silent-failure elimination) rather than raw throughput. |
| **Local / edge runtime** | **llama.cpp, Ollama** | Single-node/consumer serving, GGUF quantization, multi-backend portability (Metal/CUDA/Vulkan/SYCL/OpenCL/NPU) | llama.cpp is the **backend portability leader** but fragmenting (18 builds/day across 10 backends); Ollama is a **consolidator/distribution layer** betting on OpenAI-compat and Docker-bundled MLX/Vulkan. Both face KV-cache correctness debt (#11970, #30961). |
| **Gateway / control plane** | **LiteLLM** | Routing, rate limiting, budgets, cost accounting, provider normalization, PII guardrails | The gateway is becoming a **metering and policy enforcement point** — this week's issue cluster (5 High-severity budget/rate-limit bypasses, all closed) proves the layer is still hardening. Native Rust `/v1/messages` (7-PR stack) is an **infrastructure-language migration** for the hot path. |
| **Fine-tuning → serving bridge** | **Unsloth** | SFT/RL fine-tuning, then local serving via `FastModel.generate()` and Studio | Unsloth is **vertically integrating upward**: decode CUDA graphs, growable KV, OpenAI-compatible Studio gateway. It is now a credible single-machine alternative to a serving engine for Qwen/Llama/Gemma-class workloads. Training-side correctness (CPU eval loss #17291) is the current gap. |

**Buyer's framing:** vLLM/SGLang = scale-out multi-tenant serving; llama.cpp = maximum hardware portability and model breadth; Ollama = developer-experience and distribution; LiteLLM = multi-provider governance and cost control; Unsloth = fine-tune-then-serve in one stack. These are **complementary, not substitutable** — the interesting overlap is Unsloth vs local serving engines, and LiteLLM vs native provider SDKs.

---

## 6. Trend Signals

**1. Silent correctness failures are the new availability risk.** Two CRITICAL-grade issues this week return *HTTP 200 with wrong content*: vLLM's NVFP4 MoE empty completions on SM120 (#51525, `content: null` + `finish_reason: length`) and the closed-but-verify ROCm AITER MLA uncorrelated output (#66141). Combined with llama.cpp's spec-decode temp-0 divergence (#31472) and SGLang's breakable prefill CUDA-graph divergence (#49133), the message is clear: **output validation must be part of the serving stack, not the application's assumption.**

**2. Speculative decoding is the throughput default *and* the correctness frontier.** Every project touched it today — vLLM (EAGLE/MTP prefix-cache block-drop, 30–40% batch loss; DFlash TP>1 collapse), SGLang (EAGLE repetition-penalty skew fix), llama.cpp (DFLASH2/DSpine, temp-0 divergence), Unsloth (decode graphs). Developers running reproducibility-sensitive evals or deterministic agents should **disable spec decode or pin versions until per-project fix matrix lands.**

**3. AMD is no longer a secondary target — it's a co-primary.** SGLang has a dense MI355X/MI450 PR stream; vLLM has MI355X performance trackers and a "MonoKernel" proposal (#60904); llama.cpp and Ollama carry ongoing ROCm/Vulkan issues (RDNA AWQ 2.5× slower, gfx1151 NaN, VRAM misreporting). **Fleet standardization should treat ROCm as a distinct, pinning-heavy environment** — not a drop-in for CUDA paths.

**4. Consumer Blackwell (SM120/GB10) is a broken tier out-of-the-box across both serving engines.** vLLM (CUDA-graph failures, silent empty completions) and SGLang (CUDA 13 wheel install fails; ~8× prefill regression from DSV4.1 DeepGEMM gate removal, #49173) independently confirm SM120 is **not production-ready for quantized MoE or long-context** today. Blackwell *datacenter* (SM100/SM90) is fine; consumer SKUs are the gap.

**5. Gateways are becoming the audited control plane.** LiteLLM's five closed High-severity budget/rate-limit bypasses (project budgets outside reservation path, stale-reset zeroing spend, deleted-team key auth-cache retention, per-team-model limit halving) plus the pre-announced **2.0.0 empty-`models` semantics change** mean agent/application developers should: audit limits post-upgrade, reconcile streaming usage against provider bills in chained-proxy topologies (#36114), and **enumerate allowed models explicitly** before the deprecation window closes.

**6. OpenAI compatibility is tablestakes, structure emission is the innovation.** llama.cpp's top-level `json_schema` fix (b12229), Ollama's `/v1/models` empty-array change and truncation forwarding, and Unsloth Studio's custom-endpoint gateway all converge on OpenAI wire-compat. The next step is **schema-enforced structured generation** — llama.cpp's token-aware chat parsing (#29307/#31978) is migrating `<think>`/tool-call parsing from client-side string matching to server-emitted fields. **Plan that migration for agent frameworks.**

**7. What to watch next week:**
- **vLLM:** whether Marlin→Humming migration lands with parity; SM120 CUDA-graph fix for DeepSeek-V4.1.
- **SGLang:** v0.5.22→v0.5.23 decode regression resolution (material for DeepSeek-V4.1 interactive agents); MI450 all-reduce landing.
- **llama.cpp:** KV-cache truncation (#11970) and prompt-cache restore (#30961) — both are long-standing HIGH and affect multi-turn correctness.
- **Ollama:** stable after v0.40.16-rc0 and the #19632 VRAM accounting fix — the strongest reason to upgrade this cycle.
- **LiteLLM:** 2.0.0 migration tooling; native `/v1/messages` stack completion for Anthropic-protocol clients.
- **Unsloth:** decode-graph GA with determinism verification; growable KV release; whether Studio matures into a credible agent-routing gateway or remains a dev tool.

---

*Report generated from 2026-11-10 digest snapshots across six projects. All issue/PR numbers reference the per-project digests above.*

---

## Per-Project Reports

<details>
<summary><strong>vLLM</strong> — <a href="https://github.com/vllm-project/vllm">vllm-project/vllm</a></summary>

# vLLM Digest — 2026-11-10

## 1. Today's Highlights

No new releases in the last 24h, but issue activity is dominated by **Blackwell (SM120) readiness gaps**: DeepSeek-V4.1-Flash users on RTX PRO 6000 report CUDA graph capture failures forcing `--enforce-eager` with severe decode throughput loss (#56892). On the ROCm side, AMD is actively driving MI355X performance trackers for Qwen3.8 model families (#57149, #59575) and proposing a formal "MonoKernel" standard for specialized decodes (#60904). A notable structural shift is underway: NVIDIA is proposing to **replace Marlin with Humming** across architectures (#65560), and a Kimi K3.1 model split has landed as a PR covering both NVIDIA specialized decode and ROCm TP8 (#66532).

## 2. Releases & Breaking Changes

*None today.* No new versions or API changes were published in the last 24h.

## 3. New Model & Hardware Support

- **[PR #66532] Kimi K3.1: NVIDIA specialized decode + AMD model updates, ROCm TP8 support** — split 4/6 of a larger change; brings Kimi K3.1 model files plus the minimum external changes needed to serve at TP8 on ROCm. [Link](https://github.com/vllm-project/vllm/pull/66532)
- **[PR #61929] MiniMax M3/3.1 indexer context parallelism** — opt-in P128 indexer CP for the MSA backend on single-node NVLink TP2/TP4, with FP8 index-K sharding and FlashInfer CAKE fused scoring. [Link](https://github.com/vllm-project/vllm/pull/61929)
- **[PR #66322] ROCm AITER MLA sparse: PCP/DCP support** for DeepSeek V3.2 on AMD. [Link](https://github.com/vllm-project/vllm/pull/66322)
- **[PR #66665] Nemotron-H Elastic request-level widths** (experimental) — serve full and nested-width checkpoints from one weight allocation. [Link](https://github.com/vllm-project/vllm/pull/66665)
- **[Issue #47455] Nemotron 3.5 ASR Streaming (0.6B)** feature request still open. [Link](https://github.com/vllm-project/vllm/issues/47455)
- **[Issue #56088] Qwen3.8-Flash-Next cannot serve DeepSpec DFlash/DSpark drafters** — five blockers identified, patches available. [Link](https://github.com/vllm-project/vllm/issues/56088)

## 4. Performance & Optimization

- **[Issue #53670] EAGLE/MTP prefix-cache last-block drop** causes 1,648-token recompute per hit on hybrid Qwen3.8 GDN layouts → **~30–40% batch throughput loss** on prefix-reusing speculative workloads. [Link](https://github.com/vllm-project/vllm/issues/53670)
- **[Issue #63399] FlashInfer workspace sizing**: shrinking startup workspace from 394→32 MiB recovered **+21.27% KV-cache capacity** (Qwen2.5-1.5B, RTX 4060 Laptop). [Link](https://github.com/vllm-project/vllm/issues/63399)
- **[PR #66534] Spec-decode top-k/top-p cutoffs applied at target-logits load** during verification — stacked on #65102. [Link](https://github.com/vllm-project/vllm/pull/66534)
- **[PR #66526] `VLLM_TRITON_SYNC_AUTOTUNE_TP`**: forces all TP ranks to use rank-0's Triton autotune timings, preventing divergent per-rank kernel selection. [Link](https://github.com/vllm-project/vllm/pull/66526)
- **[PR #66631 / #65607] HiSparse coordinator bookkeeping batching** — eliminates tens-of-ms Python stalls per `schedule()` on large resident contexts. [Link](https://github.com/vllm-project/vllm/pull/65607)
- **[Issue #64460] KV-pool-saturated admission retries inflate decode TPOT ~2.3x** (V1 scheduler, platform-independent; observed on Ascend). [Link](https://github.com/vllm-project/vllm/issues/64460)
- **[Issue #64572] Eager attention backends re-derive K/V cache views on every call** — pure overhead, fix in progress. [Link](https://github.com/vllm-project/vllm/issues/64572)
- **[Issue #57555] KV offload DMA/Triton threshold** tuned on H100 is wrong for other devices; needs per-device profiling. [Link](https://github.com/vllm-project/vllm/issues/57555)

## 5. Stability & Regressions

Ranked by severity:

1. **CRITICAL — [Issue #51525] NVFP4 MoE with `--moe-backend cutlass` on SM120 silently returns empty completions** (`content: null`, `finish_reason: length`), no exception. [Link](https://github.com/vllm-project/vllm/issues/51525)
2. **CRITICAL — [Issue #66141] [CLOSED] ROCm AITER MLA decode kernel silently returns uncorrelated output** (gqa_ratio=16, fp8 Q+KV, decode_qlen 3–4). Closed — verify fix landed. [Link](https://github.com/vllm-project/vllm/issues/66141)
3. **HIGH — [Issue #62776] Qwen3.8-Flash-Next on GB10: prompt content past ~2048 tokens is invisible** to the model (indexer budget). [Link](https://github.com/vllm-project/vllm/issues/62776)
4. **HIGH — [Issue #56892] DeepSeek-V4.1-Flash on SM120: CUDA graphs unusable**, decode throughput extremely low under `--enforce-eager`. [Link](https://github.com/vllm-project/vllm/issues/56892)
5. **HIGH — [Issue #66387] DFlash acceptance collapses at TP > 1** (MiMo-V2.6-Flash-MOPD; TP1 fine). [Link](https://github.com/vllm-project/vllm/issues/66387)
6. **MEDIUM — [Issue #63593] MTP spec decode with forced-reject diverges from target-only** on Qwen3.8-27B GDN hybrid at K≥5, n≥8, L≥192. [Link](https://github.com/vllm-project/vllm/issues/63593)
7. **MEDIUM — [Issue #52525] Marlin MoE output depends on within-expert token ordering** — batch-invariance violation. [Link](https://github.com/vllm-project/vllm/issues/52525)
8. **MEDIUM — [Issue #63455] ROCm: AutoAWQ checkpoints never hit the RDNA W4A16 kernel**; AWQ decodes 2.5x slower than BF16 on gfx1151. [Link](https://github.com/vllm-project/vllm/issues/63455)
9. **LOW — [PR #66445] Humming group sizes not tiling K blocks** now rejected (was silently wrong output). [Link](https://github.com/vllm-project/vllm/pull/66445)
10. **LOW — [Issue #59988] `--api-server-count > 1`: Prometheus gauges empty until first request**. [Link](https://github.com/vllm-project/vllm/issues/59988)
11. **LOW — [PR #66489] Empty `messages` list in ChatCompletionRequest** now rejected at validation (was HTTP 500 post-generation). [Link](https://github.com/vllm-project/vllm/pull/66489)

## 6. What This Means for Application Developers

- **Pin your stack per hardware.** SM120/Blackwell consumers (RTX 50xx, RTX PRO 6000) still face silent-empty-completion and CUDA-graph failures with quantized MoE and DeepSeek-V4.1. Validate outputs, not just HTTP 200s — these failures return `finish_reason: length` with null content rather than errors.
- **Long-context on new Qwen3.8-Flash-Next deployments needs an explicit truncation test** — content past ~2K tokens may be silently ignored on GB10 today (#62776).
- **Prefix caching + speculative decoding is still a throughput trap** on hybrid-attention models (#53670, #51771): expect 30–40% batch loss on prefix-reuse workloads; disable spec decode or pin to versions without the block-drop regression until #52244 lands.
- **Admission control matters at the edge.** KV-saturated servers inflate decode TPOT ~2.3x via retry storms (#64460) — front your fleet with queue-aware load balancing rather than naive round-robin.
- **Small operational wins are available:** FlashInfer workspace tuning can recover ~20% KV capacity on consumer GPUs; the new `VLLM_TRITON_SYNC_AUTOTUNE_TP` flag stabilizes TP-rank kernel selection for reproducibility-sensitive workloads.
- **RL/fine-tuning pipelines:** track the Direct Weight Reload RFC (#54477) and Load Plan API (#64418) — both target eliminating reload-related silent staleness in weight updates.

</details>

<details>
<summary><strong>SGLang</strong> — <a href="https://github.com/sgl-project/sglang">sgl-project/sglang</a></summary>

# SGLang Digest — 2026-11-10

## 1. Today's Highlights

No new releases in the last 24 hours, but issue activity is dominated by CI/runner flakiness and a cluster of SM120 (consumer Blackwell / GB10) coverage gaps that are causing prefill and attention-backend regressions. On the performance side, AMD contributors continue landing DSA/MLA and all-reduce kernel work for MI355X/MI450, while a new SM90 decode regression (v0.5.22 → v0.5.23) was reported against DeepSeek-V4.1-Flash.

## 2. Releases & Breaking Changes

None in the last 24h. Note that issue #49336 flags a performance regression introduced between v0.5.22 (`baa9befa38`) and v0.5.23 (`297e54fd10`) — see Stability section.

- [Issue #49336](https://github.com/sgl-project/sglang/issues/49336) — no API/config breakage reported, but a decode-latency regression tied to the v0.5.23 bump.

## 3. New Model & Hardware Support

- **DeepSeek image-chat routing (sgl-router):** PR #48937 adds prefix-cache-aware routing for multimodal DeepSeek chats via `ChatFormatter::encode_multimodal_request` (depends on #46562). [PR #48937](https://github.com/sgl-project/sglang/pull/48937)
- **Clover-35B-A3B-Preview (Diffuspace):** PR #46927 adds native serving plus self-speculative decoding using a shared model for parallel draft generation and AR verification. [PR #46927](https://github.com/sgl-project/sglang/pull/46927)
- **Cambricon MLU backend:** Roadmap issue #45615 proposes an MLU backend. [Issue #45615](https://github.com/sgl-project/sglang/issues/45615)
- **AMD gfx1250 (MI45x) AITER pinning:** Issue #47562 tracks manual validation of a separate AITER pin for gfx1250, since the Scout CI matrix only covers gfx942/gfx950. [Issue #47562](https://github.com/sgl-project/sglang/issues/47562)
- **XPU consolidation:** PR #49356 batches simple XPU model enablements. [PR #49356](https://github.com/sgl-project/sglang/pull/49356)
- **NPU diffusion:** PR #43367 adds Wan2.2-T2V-A14B W4A4F4 DAOS with MXFP4 attention on NPU. [PR #43367](https://github.com/sgl-project/sglang/pull/43367)
- **External model extension points:** Issue #48475 requests public hooks for runtime-registered models (registry, attention lookup, attention kernels, sampler logprobs) — currently four private touchpoints. [Issue #48475](https://github.com/sgl-project/sglang/issues/48475)

## 4. Performance & Optimization

**AMD / ROCm**
- PR #47309: row-splits the DCP indexer, runs owned sparse MLA on the AITER asm kernel, and pipelines the a2a (stacked on #47180 / #44322). [PR #47309](https://github.com/sgl-project/sglang/pull/47309)
- PR #47872: single-pass Triton/Gluon top-k for DSA decode — 58–82 µs vs 113–127 µs on MI355X at ≥72 rows/rank (1.5–1.9× faster). [PR #47872](https://github.com/sgl-project/sglang/pull/47872)
- PR #43126: emits AITER's MoE sort directly from the radix-4 router launch for Kimi-K3 on MI355X, targeting decode-bound AgentX latency (~80 tokens/MoE call at c16). [PR #43126](https://github.com/sgl-project/sglang/pull/43126)
- PR #49377: ring all-reduce and unrolled `2shot_push` for MI450 (gfx1250) large-tensor collectives. [PR #49377](https://github.com/sgl-project/sglang/pull/49377)
- PR #38806: Qwen3.5 GDN prefill — keeps Q/K/V/Z/B/A as strided views on ROCm and eliminates extra QKV split launches. [PR #38806](https://github.com/sgl-project/sglang/pull/38806)
- PR #48398: GDN Triton chunked prefill reads strided q/k/v and skips the dead output zero-fill. [PR #48398](https://github.com/sgl-project/sglang/pull/48398)

**Correctness / scheduling**
- PR #49404: fixes EAGLE verify applying one repetition-penalty row to all draft positions; now per-draft-position. [PR #49404](https://github.com/sgl-project/sglang/pull/49404)
- PR #49386: PP scheduler waits on the local PP forward before recording completion events and skips stale DSpark KV writes. [PR #49386](https://github.com/sgl-project/sglang/pull/49386)
- PR #45180: opt-in FP8 video VAE decoder profile (per-channel E4M3 weights, per-token activation scales, fused norm/act/quant) for MiniMax-H3. [PR #45180](https://github.com/sgl-project/sglang/pull/45180)

**Roadmaps**
- PD Disaggregation Q4 roadmap (#43699) and Agentic Cache Scheduling Q4 roadmap (#43098, supernode-wide L1+L2 KV sharing with router cache-location awareness). [Issue #43699](https://github.com/sgl-project/sglang/issues/43699) · [Issue #43098](https://github.com/sgl-project/sglang/issues/43098)

## 5. Stability & Regressions

Ranked by severity:

1. **SM90 decode regression v0.5.22 → v0.5.23** — +33–45% per-step decode time at concurrency 12 across 8K–64K contexts on 8×H20-3e with DeepSeek-V4.1-Flash + DSPARK; `use_deep_select_decode` also costs acceptance on Hopper. No fix PR yet. [Issue #49336](https://github.com/sgl-project/sglang/issues/49336)
2. **SM120 cannot run out-of-the-box** — CUDA 13 wheel install fails on RTX 50-series consumer Blackwell; related: #49173 (now closed) dropped SM120/SM121 from the DSV4.1 DeepGEMM indexer gates, causing an ~8× prefill regression on GB10 via per-layer `tolist()` torch fallback. [Issue #49209](https://github.com/sgl-project/sglang/issues/49209) · [Issue #49173](https://github.com/sgl-project/sglang/issues/49173)
3. **AMD gfx1151 (Strix Halo):** MLA-shaped Triton decode returns NaN/wrong rows on ragged long-sequence batches (#48967); `--enable-deterministic-inference` fails at startup because batch-invariant matmul needs 99 KB LDS vs 64 KB available (#48968). Both open, no fix linked. [Issue #48967](https://github.com/sgl-project/sglang/issues/48967) · [Issue #48968](https://github.com/sgl-project/sglang/issues/48968)
4. **Breakable prefill CUDA graph correctness** — short prefills padded to a capture bucket diverge from eager logprobs (Qwen3.8-27B-NVFP4, hybrid GDN, v0.5.23). [Issue #49133](https://github.com/sgl-project/sglang/issues/49133)
5. **GLM-5.3-Flash on SM120** — fa4 backend crashes at CUDA-graph capture on hybrid extend reshape; Triton is the only working path (v0.5.20 image). [Issue #42012](https://github.com/sgl-project/sglang/issues/42012)
6. **Qwen-VL parsing** — literal `<image>` text in message content aborts requests with a 500 token-mismatch error. [Issue #43979](https://github.com/sgl-project/sglang/issues/43979)
7. **CI/infra:** flaky-test tracker #42752 now at 191 comments; Xeon SPR jobs OOM-killed during KV allocation on sglang-spr08/18/19 (#48411); diffusion perf checks are host-dependent and in-job retries don't help (#48311). [Issue #42752](https://github.com/sgl-project/sglang/issues/42752) · [Issue #48411](https://github.com/sgl-project/sglang/issues/48411) · [Issue #48311](https://github.com/sgl-project/sglang/issues/48311)

Also recently closed as inactive: DeepSeek-V4 TP=8 245K-context decode hang on 8×H20 (#33549), Kimi-K3 `tool_choice=required` hangs in 2P2D TP8/DCP8 (#37430) — relevant if you hit either on older builds.

## 6. What This Means for Application Developers

- **Pin your versions deliberately.** If you serve DeepSeek-V4.1-Flash with spec decoding on Hopper, benchmark before adopting v0.5.23 — the reported 33–45% decode slowdown is material for interactive agents.
- **SM120 consumer Blackwell is not a drop-in target yet.** RTX 50-series and GB10 deployments should expect to pin older images, force Triton attention, and avoid relying on DeepGEMM indexer paths (see #49209, #49173, #42012).
- **EAGLE repetition penalty fix is worth tracking** if you run `repetition_penalty != 1` with speculative decoding — PR #49404 corrects a known quality skew in verify.
- **PD disaggregation and supernode KV cache sharing** (#43699, #43098) are the roadmap items that will most affect multi-tenant agent routing cost; cache-aware multimodal routing (#48937) is the first practical piece if you serve image chats.
- **Agent frameworks self-hosting diffusion/video models** get incremental wins: MiniMax-H3 FP8 VAE decode (#45180) and trajectory-only rollout responses for DMD2 training (#49143). [PR #49143](https://github.com/sgl-project/sglang/pull/49143)
- **If you maintain out-of-tree models,** watch #48475 — the current four private touchpoints (registry, attention lookup, attention kernels, logprob sampler) are a maintenance hazard until formalized.

</details>

<details>
<summary><strong>llama.cpp</strong> — <a href="https://github.com/ggml-org/llama.cpp">ggml-org/llama.cpp</a></summary>

# llama.cpp Digest — 2026-11-10

## 1. Today's Highlights

llama.cpp shipped a dense batch of releases (b12219–b12236) spanning multimodal audio resampling, external PLE/shard loading, and a long-standing server JSON-schema fix. Backend correctness saw meaningful attention: the Metal flash-attention out-of-bounds write was patched, T‑TRI gained a backward pass, and ggml-backend now skips uploading inputs that are never read. Model-side, Granite NVFP4 scale plumbing and DFlash2/Kimi K3.1 vision integrations are in flight.

## 2. Releases & Breaking Changes

- **b12236 — `mtmd_helper_resampler`** ([details](https://github.com/ggml-org/llama.cpp/releases)): New helper for raw PCM input paths that bypass `miniaudio`; fixes resampling for raw audio. Includes docs and tests.
- **b12233 — external PLE file loading** ([#31185](https://github.com/ggml-org/llama.cpp/pull/31185)): `--ple` is now an alias of `--shard` and accepts multiple shard paths; converter emits separate PLE exports. **Behavioral note:** existing single-arg `--ple` scripts remain valid, but multi-shard usage should be audited.
- **b12229 — server JSON schema fix** ([#28697](https://github.com/ggml-org/llama.cpp/pull/28697)): The OpenAI-compatible `/v1/chat/completions` endpoint now honors a **top-level `schema`** in `response_format: {type: "json_schema"}` rather than only the nested `json_schema` wrapper form. Clients using the nested form are unaffected.
- Other releases: b12230 (ggml-zdnn tensor validation), b12228 (TRI backward pass), b12224 (backend input upload pruning), b12223 (Granite NVFP4), b12221 (Metal FA OOB fix), b12220 (Metal TRI select), b12219 (OpenCL `climits` include).

## 3. New Model & Hardware Support

- **Kimi K3.1 vision** — PR [#31980](https://github.com/ggml-org/llama.cpp/pull/31980) (ngxson), aligned with vLLM outputs; fixes [#31963](https://github.com/ggml-org/llama.cpp/issues/31963).
- **DFLASH2 for Recursive Ripple-65B-Preview** — PR [#31904](https://github.com/ggml-org/llama.cpp/pull/31904), tested on Apple M5 Max / Metal with Q4_K target + Q8_0 drafter.
- **Tencent DSpine draft support** — PR [#31941](https://github.com/ggml-org/llama.cpp/pull/31941) (Qwen3-8B-DSpine-b16, Qwen3.8-27B-DSpine-b16), a DFlash block-draft variant with a code bridge per draft layer.
- **Gemma 4 audio streaming** — PR [#31965](https://github.com/ggml-org/llama.cpp/pull/31965) closed; causal encoder reuses `mtmd_audio_stream` at `step_ms=480`, worst-row cosine 0.993–0.997 vs non-stream. Resolves [#31561](https://github.com/ggml-org/llama.cpp/issues/31561).
- **Granite NVFP4 weight scales** — PR [#31449](https://github.com/ggml-org/llama.cpp/pull/31449) lands scale plumbing through `build_ffn` for all Granite architectures (landed in b12223).
- **OpenCL** — f32 `MUL_MAT_ID` added ([#31783](https://github.com/ggml-org/llama.cpp/pull/31783), merged), useful for `test-llama-archs`.
- **OpenVINO** — `FILL` as in-place recurrent-cache clear, with NaN stickiness fix ([#31977](https://github.com/ggml-org/llama.cpp/pull/31977)).
- **SYCL** — `ROLL` op now handles permuted sources ([#31799](https://github.com/ggml-org/llama.cpp/pull/31799)).
- **XDNA backend** feature request remains open with strong community interest ([#21725](https://github.com/ggml-org/llama.cpp/issues/21725), 👍 39).

## 4. Performance & Optimization

- **Backend upload pruning** ([#31419](https://github.com/ggml-org/llama.cpp/pull/31419), b12224): ggml-backend no longer uploads tensors whose contents are never read — direct bandwidth/H2D savings for multi-backend graphs.
- **Adreno Vulkan scalar FA cap** ([#30829](https://github.com/ggml-org/llama.cpp/pull/30829)): caps rows at 8 for head sizes >128 and KV ≥1024, preventing register spills (264 B/invocation observed on X1-85). Relevant to mobile/Qualcomm deployments.
- **OpenCL int8 fused KQ** ([#30249](https://github.com/ggml-org/llama.cpp/pull/30249)): stages Q per head-size slice, enables head size 512, and reduces local-memory growth with head size in decomposed prefill attention.
- **ARM NEON non-dotprod IQ4_NL_Q8 GEMM/GEMV 4×4 repack** ([#30165](https://github.com/ggml-org/llama.cpp/pull/30165)): extends fast paths to ARMv8 < 8.2-A (no `vdotq_laneq_s32`).
- **x86 BF16 dot product** ([#31878](https://github.com/ggml-org/llama.cpp/pull/31878)): keeps F32 activations on x86 without AVX512-BF16, avoiding repeated conversions and precision loss ([#31842](https://github.com/ggml-org/llama.cpp/issues/31842)).
- **MMVQ cutoff table** ([#28090](https://github.com/ggml-org/llama.cpp/issues/28090)): proposed `sm_86` entry for Q4_0 (~9.1% on A10); still open.
- **SYCL on Battlemage**: recurring reports of "brutally bad" performance ([#22413](https://github.com/ggml-org/llama.cpp/issues/22413), now closed) — expect no immediate fix unless a new issue is filed.

## 5. Stability & Regressions

Ranked by severity:

1. **HIGH — KV cache truncated incorrectly on `/v1/chat/completions`** ([#11970](https://github.com/ggml-org/llama.cpp/issues/11970), 50 comments, still open). Long-standing; affects correctness of multi-turn conversations. No fix PR referenced.
2. **HIGH — Prompt-cache restore fails with unified KV cache** ([#30961](https://github.com/ggml-org/llama.cpp/issues/30961)). Slot idle-clear ordering bug; impacts server restore latency.
3. **HIGH — Recurrent state with NaN not cleared** ([#29830](https://github.com/ggml-org/llama.cpp/issues/29830)). NaN propagation garbles subsequent requests on recurrent architectures.
4. **HIGH — Speculative decoding breaks temp‑0 identity** ([#31472](https://github.com/ggml-org/llama.cpp/issues/31472)). `draft-dflash` / `draft-dspark` diverge from autoregressive output even at 100% acceptance — **directly relevant to reproducibility-sensitive eval pipelines**. Backend-independent.
5. **MEDIUM — `ggml_backend_sched_alloc_splits` unexpected reallocation crash** ([#28753](https://github.com/ggml-org/llama.cpp/issues/28753)) on Intel Arc / IntelLLVM builds.
6. **MEDIUM — Logprobs nondeterministic on SYCL+MoE** ([#31206](https://github.com/ggml-org/llama.cpp/issues/31206)); request `seed` does not pin distribution.
7. **MEDIUM — Metal decode EOS after 1 token at long context** (Qwen3.8-Flash-Next) ([#28805](https://github.com/ggml-org/llama.cpp/issues/28805), closed stale) — threshold moves with quant/KV quant/n_ctx.
8. **MEDIUM — Windows AMD VRAM misreporting causes shared-memory spill** ([#30250](https://github.com/ggml-org/llama.cpp/issues/30250)).
9. **LOW — Vulkan Adreno pipeline creation failure** (`mul_mat_vec_q4_k_f32_f32`, shaderc/NDK SPIR-V mismatch) ([#28635](https://github.com/ggml-org/llama.cpp/issues/28635)).
10. **LOW — `ggml_gallocr` stale plan reuse on node identity change** ([#28448](https://github.com/ggml-org/llama.cpp/issues/28448), closed stale) — silent sparse-MoE corruption risk.

**Fixed today/this cycle:** Metal FA out-of-bounds write ([#31662](https://github.com/ggml-org/llama.cpp/pull/31662), b12221); Metal TRI ±inf→NaN ([#30711](https://github.com/ggml-org/llama.cpp/pull/30711), b12220); Qwen4exp merged-tensor assert ([#31189](https://github.com/ggml-org/llama.cpp/issues/31189), closed); json_schema response_format ([#10732](https://github.com/ggml-org/llama.cpp/issues/10732), closed).

## 6. What This Means for Application Developers

- **OpenAI compat is tightening.** Top-level `json_schema` in `response_format` now works (b12229) — you can drop wrapper hacks. But **KV-cache truncation (#11970) and prompt-cache restore failures (#30961) remain open**, so be conservative with long multi-turn sessions and consider fixed/expanded context on the server side.
- **Reproducibility caveat for spec decoding.** If you run evals or deterministic agents at `--temp 0`, avoid `draft-dflash`/`draft-dspark` on qwen35moe targets until [#31472](https://github.com/ggml-org/llama.cpp/issues/31472) is resolved — output is not token-identical to AR decode.
- **Nondeterministic logprobs on SYCL+MoE** ([#31206](https://github.com/ggml-org/llama.cpp/issues/31206)) means do not rely on `/v1/completions` logprobs for scoring/reranking on Intel iGPU + MoE configs.
- **Multimodal audio is getting first-class.** `mtmd_helper_resampler` (b12236) + Gemma 4 streaming (cosine ≥0.993 vs non-stream) make `/v1/audio/*` and realtime endpoints viable for production voice agents — expect further `/v1/audio/speech` work from [#31763](https://github.com/ggml-org/llama.cpp/pull/31763).
- **Token-aware chat parsing is coming** ([#29307](https://github.com/ggml-org/llama.cpp/pull/29307), [#31978](https://github.com/ggml-org/llama.cpp/pull/31978)). If you parse `<think>`/tool-call tokens yourself from raw output, plan to migrate to server-emitted structured fields once this lands — it will be more robust than string matching.
- **New draft-model archs** (DSpine, DFLASH2, Kimi K3.1 vision) keep expanding the spec-decoding and multimodal surface; verify against your target quant and backend — several are tested on Metal/CUDA only at present.
- **Windows AMD users**: VRAM misreporting (#30250) can silently push into shared memory and tank throughput; monitor your actual VRAM headroom rather than trusting runtime-reported budgets.

</details>

<details>
<summary><strong>Ollama</strong> — <a href="https://github.com/ollama/ollama">ollama/ollama</a></summary>

# Ollama Digest — 2026-11-10

## 1. Today's Highlights
Release candidate **v0.40.16-rc0** lands with OpenAI-compatibility work: forwarding the truncation option in `/v1/responses` requests and returning an empty array (rather than an error) for `/v1/models` when no models are installed. The biggest in-progress fix is PR **#19632**, a multi-issue "VRAM accounting and GPU splitting" correction targeting model eviction, load failures, and unnecessary multi-GPU spread. Meanwhile, several memory/scheduler regressions surfaced: intermittent CUDA init fallback on Windows, a ROCm/`ollama ps` VRAM reporting discrepancy, and a Vulkan/RADV ~30× generation slowdown after the first request.

## 2. Releases & Breaking Changes
- **v0.40.16-rc0** (v0.40.16) — https://github.com/ollama/ollama/pull/19501
  - `openai`: forward truncation option in responses requests (PR #19501).
  - `openai`: `/v1/models` returns an empty array when no models are installed (PR #19539) — clients that assumed load-time failure on empty model lists should verify handling of a `200` + `[]`.
  - `llam...` (release notes truncated); treat as a RC — no confirmed breaking paths beyond the `/v1/models` behavior change.
- Documentation/spec correction in progress: PR **#19626** proposes requiring `stop` to be an array in the native API spec (https://github.com/ollama/ollama/pull/19626) — string `stop` is currently accepted by the spec but rejected by the server. Watch for alignment in a future release.

## 3. New Model & Hardware Support
- **Qwen3.5 / Qwen3.5-MoE parallel requests**: PR **#17144** proposes lifting the hardcoded `numParallel = 1` blocklist in `server/sched.go` now that the upstream llama.cpp hybrid-architecture crash is fixed (2026-03-08) — https://github.com/ollama/ollama/pull/17144.
- **Docker MLX/Vulkan parity**: PR **#18592** adds missing libraries/configs so the MLX runner and Vulkan driver (incl. Nvidia Vulkan) work inside the container image — https://github.com/ollama/ollama/pull/18592.
- **Vulkan-only user complaint**: Issue **#19474** (closed) reports CUDA driver downloads on Vulkan-only systems (e.g., Intel) — no confirmed fix referenced — https://github.com/ollama/ollama/issues/19474.
- **MLX memory pre-flight**: PR **#19419** applies the llama-server memory pre-flight check to MLX loads on Apple Silicon — https://github.com/ollama/ollama/pull/19419.

## 4. Performance & Optimization
- **VRAM accounting & GPU splitting (in progress)**: PR **#19632** (issue **#19631**) fixes incorrect VRAM prediction, GPU overhead reservation, and unnecessary model splitting that caused wrongful eviction and load refusal — https://github.com/ollama/ollama/pull/19632
- **Vulkan prompt-cache regression**: Issue **#19201** (closed) documents a reproducible **~29.7× generation throughput collapse** on the second identical request with the bundled llama.cpp host-memory prompt cache enabled, AMD RX 6600 XT / Mesa RADV, Ollama 0.40.8 — https://github.com/ollama/ollama/issues/19201
- **`num_predict == -2`** dynamic token limit (fill context buffer) PR **#9547** closed — https://github.com/ollama/ollama/pull/9547
- **Qwen3.5 4B prompt-processing guidance** requested on 16GB M4 (100% GPU); no defect confirmed — https://github.com/ollama/ollama/issues/19282

## 5. Stability & Regressions
Ranked by severity:
1. **CUDA warmup failure + silent CPU fallback** — Issue **#17380** (open): intermittent `CUDA error: shared object initialization failed` on Windows/RTX 5070 Ti with `gpt-oss:20b`, correlated with silent `CUDA_Host` → `CPU` pinned-buffer fallback; no fix PR referenced — https://github.com/ollama/ollama/issues/17380
2. **Windows updater crash** — Issue **#19621** (open): `IsProcRunning` treats `EnumProcesses` byte count as PID count, panicking when >512 processes run; fix PR **#19625** opened same day — https://github.com/ollama/ollama/issues/19621 · https://github.com/ollama/ollama/pull/19625
3. **Qwen3.5 4B thinking-only responses** — Issue **#18916** (open): long conversations with `num_ctx:65536` return `done:true` but only `message.thinking`, no content/tool calls — https://github.com/ollama/ollama/issues/18916
4. **Tool-call compaction mismatches** — Issue **#19623** (open): repeated compaction retains function output without matching call in Codex Desktop + DeepSeek — https://github.com/ollama/ollama/issues/19623
5. **VRAM reporting discrepancy** — Issue **#17251** (open): `ollama ps` under-reports VRAM vs `rocm-smi` with MTP accelerator models — https://github.com/ollama/ollama/issues/17251
6. **Corporate proxy 403 on pull** — Issue **#19604** (closed): 403 on `ollama pull` behind Blue Coat proxy despite working `curl` — https://github.com/ollama/ollama/issues/19604
7. **Unicode corruption in Olmo3 tool args** — PR **#18906** (open) fixes UTF-8 truncation in `splitArguments` — https://github.com/ollama/ollama/pull/18906
8. **Modelfile glob path parsing** — PR **#19628** (open): relative wildcards resolve against literal dir when path contains glob metacharacters — https://github.com/ollama/ollama/pull/19628

## 6. What This Means for Application Developers
- **OpenAI-compat clients**: The `/v1/models` empty-array change is the one behavior to test in your client code — treat zero installed models as a valid state, not an error. Truncation forwarding in `/v1/responses` improves parity with OpenAI semantics.
- **Structured tool calling**: If you use Codex/DeepSeek-style compaction loops, pin a version and watch #19623 — orphaned function outputs can break agent state machines. Olmo3 users on non-ASCII args should track #18906.
- **VRAM-sensitive deployments**: The #19632 scheduler fix directly addresses wrongful evictions and unnecessary multi-GPU splitting — a strong reason to adopt the next stable after this RC. For now, validate `ollama ps` numbers against `rocm-smi`/`nvidia-smi` before sizing capacity.
- **Qwen3.5 parallelism**: Once #17144 lands, MoE serving throughput gains become available; until then, expect `numParallel=1` for these architectures.
- **Windows fleet ops**: Updater crashes above 512 processes (#19621/#19625) mean automated rollout tooling on busy hosts should be staged, not fully unattended, until the fix ships.

---
*Generated from github.com/ollama/ollama data snapshot for 2026-11-10.*

</details>

<details>
<summary><strong>LiteLLM</strong> — <a href="https://github.com/BerriAI/litellm">BerriAI/litellm</a></summary>

# LiteLLM Digest — 2026-11-10

## 1. Today's Highlights
Release v1.108.4 shipped with cosign-signed Docker images (verification key introduced in commit `e82a3c6`). The issue queue is dominated by cost/budgeting correctness problems: a rate limiter that enforces per-team per-model limits at half the configured value (now closed) and multiple budget-accounting bugs around DB-stored deployments, stale reset rows, and cache-eviction gaps. On the PR side, work is converging on the native (Rust-backed) `/v1/messages` proxy path — a seven-PR stack fixing logging, caching, streaming abandonment, and failure-row billing — plus backports to the 1.108.x and 1.109.0 release branches.

## 2. Releases & Breaking Changes
- **v1.108.4** — Docker image signing via cosign; see the release notes for the verification key. [Release](https://github.com/BerriAI/litellm/releases)
- **Upcoming 2.0.0 behavior change (pre-announced):** the legacy rule where an empty `models` list on a key or team means "all models" is being dropped. PR #50720 adds a boot-time warning counting keys/teams relying on this default and documents the migration path. [PR #50720](https://github.com/BerriAI/litellm/pull/50720)
- **`litellm.turn_on_message_logging` docs bug** closed — the actual API is `turn_off_message_logging`. [Issue #37143](https://github.com/BerriAI/litellm/issues/37143)

## 3. New Model & Hardware Support
- **Claude "fable-5-2" (Fable 5.2)** capability flags and prompt-cache pricing are being extended from the Anthropic API to **Vertex AI** and **Bedrock Converse**, with wire-format and cost tests added. [PR #50742](https://github.com/BerriAI/litellm/pull/50742)
- **Z.AI `glm-5.2[1m]`** (1M-context variant) is documented but returns `Unknown Model` through the proxy — still open. [Issue #32218](https://github.com/BerriAI/litellm/issues/32218)
- Tokenizer resolution for **custom-named deployments** (e.g., Azure AI Foundry `my-flash` serving DeepSeek-V4-Flash) now uses the configured `base_model` tokenizer instead of falling back to OpenAI's. [PR #50734](https://github.com/BerriAI/litellm/pull/50734)
- **Rust-backed pip binary** effort continues as the tracking issue for install problems. [Issue #31261](https://github.com/BerriAI/litellm/issues/31261)

## 4. Performance & Optimization
- **Wasted Redis writes:** the rate limiter unconditionally writes post-call counters even for keys/users/teams with no limits configured. Fix proposed to skip these writes at high throughput; PR is stale but the issue remains open. [Issue #31880](https://github.com/BerriAI/litellm/issues/31880)
- **Prompt/compression savings under cost-based routing:** `compression_savings_spend` and `prompt_caching_savings_spend` were always $0 with multiple deployments per model name — closed. [Issue #37117](https://github.com/BerriAI/litellm/issues/37117)
- **Cache-read billing:** `azure/us/gpt-4o-2024-11-20` and `azure/eu/...` were missing `cache_read_input_token_cost`, billing cache reads at zero — closed. [Issue #37823](https://github.com/BerriAI/litellm/issues/37823)
- **Native `/v1/messages` path:** cache entries now written after the router stamps the response (matching Python behavior), and mid-stream client disconnects are billed correctly — part of the seven-PR stack. [PR #50426](https://github.com/BerriAI/litellm/pull/50426), [PR #50425](https://github.com/BerriAI/litellm/pull/50425)

## 5. Stability & Regressions (ranked by severity)

**High**
- **Rate limiter halves per-team per-model limits:** `model_per_team` RPM/TPM of `N` 429s at ~`N/2`. Precise repro filed; closed. [Issue #34140](https://github.com/BerriAI/litellm/issues/34140)
- **Budget reset job silently zeroes spend forever:** a row with `budget_duration=null` but a stale past `budget_reset_at` is picked up every tick, wiping spend indefinitely. Closed. [Issue #39370](https://github.com/BerriAI/litellm/issues/39370)
- **Project budgets bypass atomic reservation:** project max budgets are read-time only and never part of the pre-call reservation path, so concurrent requests can overshoot. Closed. [Issue #34101](https://github.com/BerriAI/litellm/issues/34101)
- **End-user budget bypass via stale per-pod spend:** on a Redis counter clean-miss (60s TTL), a stale pod-local fallback value can let requests through. Closed. [Issue #34238](https://github.com/BerriAI/litellm/issues/34238)
- **Deleted team keys remain auth-valid:** `/team/delete` removes keys from DB but never evicts them from the auth cache. Closed. [Issue #34217](https://github.com/BerriAI/litellm/issues/34217)

**Medium**
- **Streaming usage severely undercounted** in chained proxy setups (Front → Upstream → Bedrock) despite correct non-streaming usage; root cause traced to stream aggregation, not provider transformation. Open. [Issue #36114](https://github.com/BerriAI/litellm/issues/36114)
- **JSON-RPC `id` stripped** by `pass_through_endpoints` due to collision with an internal param name — breaks JSON-RPC 2.0 payloads. Open. [Issue #49479](https://github.com/BerriAI/litellm/issues/49479)
- **Price reload wipes DB deployment cost maps:** with empty `model_list` + `STORE_MODEL_IN_DB`, the periodic cost-map reload drops runtime cost registrations (router not in `_live_routers`). Closed. [Issue #43444](https://github.com/BerriAI/litellm/issues/43444)
- **Presidio PII unmasking gaps:** tool-call arguments never unmasked ([#31950](https://github.com/BerriAI/litellm/issues/31950), open), and placeholders are numbered per-message causing cross-message corruption ([#31959](https://github.com/BerriAI/litellm/issues/31959), open).
- **`reasoning_effort=xhigh` silently downgraded** when the model map lacks capability, instead of being refused. Closed. [Issue #40471](https://github.com/BerriAI/litellm/issues/40471)
- **`completion_model` in `general_settings` overrides client model** rather than acting as fallback. Open, stale. [Issue #21554](https://github.com/BerriAI/litellm/issues/21554)
- **VSCode native model provider incompatible** with LiteLLM. Open. [Issue #35775](https://github.com/BerriAI/litellm/issues/35775)

**Low**
- `/v1/messages` streaming dropped the first chunk at content-block transitions on Bedrock reasoning models. Closed. [Issue #25214](https://github.com/BerriAI/litellm/issues/25214)
- Ollama streaming emitted `index=0` for all tool calls, merging parallel calls into malformed JSON. Closed. [Issue #33678](https://github.com/BerriAI/litellm/issues/33678)
- `token_counter` raised `KeyError: 'items'` on array tool params without `items` → 500 on `/v1/messages/count_tokens`. Closed. [Issue #46052](https://github.com/BerriAI/litellm/issues/46052)
- Azure requests leaked internal `model_alias_map` field, rejected upstream. Closed. [Issue #42409](https://github.com/BerriAI/litellm/issues/42409)
- Bedrock `count_tokens` returned 500 instead of 400 on malformed bodies; fixed in [PR #50708](https://github.com/BerriAI/litellm/pull/50708).
- Flaky MCP test `test_cold_concurrent_schema_validation_accepts_valid_arguments`. Closed. [Issue #46212](https://github.com/BerriAI/litellm/issues/46212)

**Fix PRs in flight**
- Bedrock pass-through rejects raw AWS model IDs (`POST /bedrock/model/us.anthropic.claude-haiku-5-5/converse` → 400) — [PR #50737](https://github.com/BerriAI/litellm/pull/50737)
- `/v1/messages` ignores `additional_drop_params` before unsupported-param checks (backport + main) — [PR #50755](https://github.com/BerriAI/litellm/pull/50755), [#50753](https://github.com/BerriAI/litellm/pull/50753)
- Fail-closed budget enforcement 503s team users lacking a membership row — [PR #50603](https://github.com/BerriAI/litellm/pull/50603)
- Empty final user message dropped on Bedrock Converse/Responses, causing Claude 5.x prefill 400s — [PR #50701](https://github.com/BerriAI/litellm/pull/50701)
- Router default metadata replaced caller metadata instead of merging — [PR #50700](https://github.com/BerriAI/litellm/pull/50700)
- Responses WebSocket: only the first refused turn got a failure row — [PR #50731](https://github.com/BerriAI/litellm/pull/50731)
- Claude Code subagent traffic overwrites main agent tier pins in complexity router — [PR #50741](https://github.com/BerriAI/litellm/pull/50741)

## 6. What This Means for Application Developers
- **Audit your rate limits if you use per-team per-model caps.** The halving bug (#34140) means effective throughput may be half of configured; recheck after upgrade and load-test before relying on `model_per_team` for SLA-style isolation.
- **Budget enforcement has historically been porous** in several paths (project budgets, stale reset rows, cache-miss fallbacks). If you depend on hard spend ceilings for tenant isolation, validate with concurrent load rather than trusting the dashboard, and keep external spend alerts as a backstop.
- **Deleting teams does not revoke keys immediately** on affected versions — rotate or explicitly `/key/delete` any keys from decommissioned teams.
- **Chained proxy deployments undercount streaming usage.** If you bill downstream on LiteLLM's usage numbers through a multi-hop setup, reconcile against provider-side billing (#36114).
- **PII guardrail users:** Presidio masking is incomplete for tool-call arguments and multi-message requests — do not rely on it as the sole DLP layer for agentic workloads with tools.
- **Plan for the 2.0.0 empty-`models` semantics change** now: explicitly enumerate allowed models on keys and teams, and watch the new boot warning (#50720) to find legacy configurations.
- **Native `/v1/messages` path is maturing** (billing on abandoned streams, cache write ordering, failure rows per WebSocket turn). If you use Claude Code or Anthropic-protocol clients at scale, expect improved metering accuracy in the 1.108.x/1.109 line.

</details>

<details>
<summary><strong>Unsloth</strong> — <a href="https://github.com/unslothai/unsloth">unslothai/unsloth</a></summary>

# Unsloth Digest — 2026-11-10

## 1. Today's Highlights

No new releases in the last 24h, but the `main` branch saw an intense stack of inference-engine work: Daniel Han landed a series of decode CUDA graph PRs (#16587 base, plus model-specific extensions for Gemma 4 and Qwen3.5/3.6), claiming **2–8x decode speedups on T4 with token-equivalent output**. Meanwhile, Studio's model-serving layer (the HTTP gateway fronting local models) accumulated several issues around OpenAI-compatible endpoints — notably video being silently rejected on custom/vLLM/Ollama connections, but a fix PR is open (#17322).

## 2. Releases & Breaking Changes

No releases. One dependency-pin landmine worth flagging: **typer 0.29.0 (published 2026-11-09 18:48 UTC) breaks Windows Studio installs** by holding `pydantic_core` open. A fix keeping typer `<0.29` is merged (#17319). If you build on the Studio installer, avoid unpinned `typer` on Windows.

## 3. New Model & Hardware Support

- **Gemma 4, Qwen3.5 / Qwen3.6 (`qwen3_5`, `qwen3_5_moe`)** — flex-attention decode steps under CUDA graphs ([#17329](https://github.com/unslothai/unsloth/pull/17329)).
- **Qwen3.5/3.6 hybrid linear-attention (Gated DeltaNet)** — decode graph support for `LinearAttentionLayer` static caches, previously refused outright ([#17324](https://github.com/unslothai/unsloth/pull/17324)).
- **Text-model broad coverage** in the base decode-graph PR: Gemma 3/4, Muse-Glimmer, Qwen3 / Qwen3-VL text, Llama, Mistral 3, Granite, Phi-4, OLMo 2, Cohere 2, GLM-4 ([#16587](https://github.com/unslothai/unsloth/pull/16587)).
- **Linux GPU detection regression** — issue reports CUDA-capable Linux hosts being forced onto CPU ([#17235](https://github.com/unslothai/unsloth/issues/17235), closed). Windows image-gen falling back to RAM on an RTX 3060 is also reported ([#16803](https://github.com/unslothai/unsloth/issues/16803), closed).

## 4. Performance & Optimization

- **Decode CUDA graphs on by default for all FastModel `model.generate()`** — replay a captured one-token step instead of paying kernel launch overhead; measured **2–8x on T4** with identical output tokens ([#16587](https://github.com/unslothai/unsloth/pull/16587)).
- **Opt-in growable KV cache** (`UNSLOTH_GROWABLE_KV=1`) — avoids `StaticCache` pre-allocating `prompt + max_new_tokens` for every full-attention layer. Off by default; zero behavior change when unset ([#17296](https://github.com/unslothai/unsloth/pull/17296)).
- **Studio / WebKit long-chat perf series** — multiple PRs targeting menu/dialog/browser-pane open costs scaling with conversation length: 150-turn benchmarks dropping from ~460–560 ms toward short-chat levels by eliminating per-frame thread work and re-layout ([#17282](https://github.com/unslothai/unsloth/pull/17282), [#17284](https://github.com/unslothai/unsloth/pull/17284), [#17323](https://github.com/unslothai/unsloth/pull/17323)).
- **MCP tool loop inefficiency** — with many MCP tools enabled, GGUF chats reprocess the entire prompt on nearly every message; open PR keeps deferred tool definitions in place ([#17326](https://github.com/unslothai/unsloth/pull/17326), tracked from [#17015](https://github.com/unslothai/unsloth/issues/17015)).

## 5. Stability & Regressions

Ranked by severity for serving workloads:

1. **Video uploads rejected on custom OpenAI-compatible connections (vLLM/OMLX/llama.cpp/Ollama)** even when the backend accepts video ([#17295](https://github.com/unslothai/unsloth/issues/17295)). Fix PR open: [#17322](https://github.com/unslothai/unsloth/pull/17322).
2. **Linux forced to CPU over GPU** — closed, but a hard downshift for anyone hitting it ([#17235](https://github.com/unslothai/unsloth/issues/17235)).
3. **HTML→Markdown converter bugs in Studio web fetch** — stray `;` after bare `&`, escaped code leaking out of code fences, malformed entities causing subsequent page content to render as raw HTML ([#17199](https://github.com/unslothai/unsloth/issues/17199)). Relevant if you rely on fetch-tool output quality or pipe it into downstream parsing.
4. **Managed-account folder browser 403** on `/api/models/browse-folders` when install is symlinked; affects multi-tenant Studio setups ([#17292](https://github.com/unslothai/unsloth/issues/17292), fix PR [#17321](https://github.com/unslothai/unsloth/pull/17321)).
5. **Windows install failures from typer 0.29** — fix merged ([#17319](https://github.com/unslothai/unsloth/pull/17319)).
6. **Attachment-note parser false positive** — file contents shaped like a sandbox attachment note get parsed as a second attachment ([#16913](https://github.com/unslothai/unsloth/issues/16913), closed).
7. **CPU SFTTrainer eval loss wrong under TRL 1.15** — divides by unshifted label count, understating `eval_loss` whenever the first token isn't masked ([#17291](https://github.com/unslothai/unsloth/pull/17291)). Fixes loss-curve distortion for CPU-only fine-tuning pipelines.
8. **ROCM TunableOp test regression** on `main` since #17299 — fix PR open ([#17325](https://github.com/unslothai/unsloth/pull/17325)).

## 6. What This Means for Application Developers

- **Decode throughput is about to step up materially for free.** If you serve Qwen3.x/Llama/Gemma-class models via `FastModel.generate()`, the default-on decode graphs should deliver multi-x token-rate gains on older GPUs (T4-class) without output changes. Worth benchmarking your p50/p99 latency after the next release; conversely, verify determinism-sensitive evals still match.
- **The growable KV cache opt-in is the one to watch for long-context serving.** Fixed `StaticCache` over-allocation has been a real ceiling for concurrent long-prompt workloads; if you run high `max_new_tokens` with modest actual generation, trial `UNSLOTH_GROWABLE_KV=1` once released.
- **Don't trust "custom OpenAI-compatible" for multimodal yet** — video is rejected client-side before it reaches vLLM/Ollama. Pin to the fix PR or validate on local model loads until it lands.
- **Studio is accumulating a real OpenAI-compatible gateway surface** (custom endpoints, MCP tools, web fetch, managed accounts). The bug density in that layer — permission modes being session-only ([#17297](https://github.com/unslothai/unsloth/issues/17297)), preference sync gaps ([#16912](https://github.com/unslothai/unsloth/issues/16912)), MCP prompt reprocessing — suggests treating it as maturing rather than hardened for production agent routing today.
- **If you're doing image-LoRA or large-corpus indexing on desktop**, resource management is still manual: project indexing has no pause/throttle and can starve inference ([#16992](https://github.com/unslothai/unsloth/issues/16992), closed), and image-LoRA training lacks reusable settings profiles ([#16979](https://github.com/unslothai/unsloth/issues/16979), closed).

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar).*