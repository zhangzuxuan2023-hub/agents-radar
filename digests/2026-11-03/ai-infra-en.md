# AI Infrastructure Digest 2026-11-03

> Generated: 2026-11-03 10:36 UTC | Projects covered: 6

- [vLLM](https://github.com/vllm-project/vllm)
- [SGLang](https://github.com/sgl-project/sglang)
- [llama.cpp](https://github.com/ggml-org/llama.cpp)
- [Ollama](https://github.com/ollama/ollama)
- [LiteLLM](https://github.com/BerriAI/litellm)
- [Unsloth](https://github.com/unslothai/unsloth)

---

## Cross-Project Comparison

# AI Infrastructure Ecosystem — Cross-Project Comparison
**Digest date: 2026-11-03**

---

## 1. Ecosystem Overview

The ecosystem is in a **correctness-consolidation phase** rather than a feature-expansion one: the major serving engines (vLLM, SGLang) are chasing batch-invariance, determinism and hardware-specific stability while landing incremental model support, and llama.cpp is shipping rolling per-commit builds at high cadence (10 builds in 24h). Two structural migrations dominate roadmap activity: **Rust frontends** at both vLLM (#44280) and LiteLLM (#31263, #49606), and SGLang's `api.v1` rust-server migration (#45816). Hardware fragmentation is now the dominant risk vector — SM120/GB10 Blackwell quirks in vLLM, NVFP4 MoE crashes in SGLang, oneAPI/SYCL and 170-SM tiling gaps in llama.cpp. Meanwhile the gateway layer (LiteLLM) is pivoting from request routing to **fleet control-plane** features, and the fine-tuning layer (Unsloth) is investing heavily in serving-adjacent UX (MLX load latency, prompt cache) — evidence that the "training tool" and "inference server" boundaries are blurring.

---

## 2. Activity Comparison

| Project | Layer | Releases (24h) | Issue Activity | PR Activity | Notable Volume Signals |
|---|---|---|---|---|---|
| **vLLM** | Serving engine | None | Very high — batch-invariance #27433 at 104 comments; multiple severe HW regressions open | Active — fix PRs open for SM120 (#65356), CPU `KeyError` (#65365) | DeepSeek-V4 cluster across versions; determinism bugs spanning 3+ model families |
| **SGLang** | Serving engine | **v0.5.23** (1007 PRs, 257 contributors) | High — 3 "High" severity open (#47916, #42510, #48108); CI trackers #26340 (353 cmts), #42752 (166 cmts) | Very active — 9-PR fix batch; rust-server migration | Largest single-day change surface of any project reviewed |
| **llama.cpp** | Local runtime | **10 builds** (b12054–b12066) | Moderate — SYCL #31064 open; RPC security PRs in review (#31592, #31584) | High — unified-KV rework (#29510), Clover arch (#31306), NCCL TP (#28967) | Rolling release cadence; broad backend spread (WebGPU, Hexagon, CANN, Vulkan, zDNN) |
| **Ollama** | Local runtime / UX | **v0.40.14** (patch) | Moderate — critical scheduler deadlock #17408 open; OpenAI-compat correctness tail | Moderate — 6+ fix PRs open (#19401, #18691, #19211) | Fix-PR-to-issue ratio healthy on compat layer; core scheduler gap unfixed |
| **LiteLLM** | Gateway | None | High — streaming reasoning bugs (#49795, #30765/#25214), rate-limiter (#49675) | Very high — 9-PR control-plane stack (#49654–#49662), Rust toggle (#49606/#49607) | Breaking change in flight: #49783 (`prompt_cache_breakpoint` → Claude `cache_control`) |
| **Unsloth** | Fine-tuning | None | High but **Studio-skewed** — 17x prompt-cache regression (#16538) is the standout | Active — MLX overhaul (#16536, #16517), NF4 expansion (#16066), MoE PEFT fix (#15159) | Issue mix shifted from core training to desktop/web UI |

**Reading the table:** SGLang has the highest raw throughput of change (release + migration + fix batch); llama.cpp the highest release cadence; vLLM and LiteLLM the highest *risk density* (severe open issues + breaking changes in flight). Ollama is the most "steady-state" — one patch and a long compat tail.

---

## 3. Model Support Race

| New model / arch | vLLM | SGLang | llama.cpp | Ollama | Unsloth |
|---|---|---|---|---|---|
| **DeepSeek-V4.x / V4.1-Flash** | ⚠️ Partial — LoRA merged (#57245); SM120 boot fix open (#65356); CPU broken v0.30–v0.33 (#65365) | ✅ FP4 indexer (with HBM caveat #39441) | — | — | — |
| **Gemma 4 (26B-A4B)** | ✅ MI350P MoE tuning (#65134); MTP crash #30698 in llama.cpp ecosystem | — | ⚠️ MTP+mmproj abort (#30698) | — | — |
| **Qwen3.5 / 3.8 / Next** | ✅ FLA on MI300X (#41446); nondeterminism bugs (#54521) | ✅ MTP shard selection (#44702); Flash-Next MI355X tracker (#44304) | ✅ Flash-Next 2% gen win (#31443) | ⚠️ 0.8B `q4_K_M` missing (#19436) | ✅ Qwen3 NF4 decode (#16066) |
| **MiniMax-M2.7 / M3.1** | ⚠️ NVFP4 TP=2 hang (#41725) | ✅ Unified-cache NVFP4 linker (#48125) | — | — | — |
| **GLM-5.3-Flash** | — | ✅ SM90 optimization tracker (#45373); MambaPool OOM (#47916) | ⚠️ Metal decode stall (#29867) | — | — |
| **Muse Spark Open / Beam-1.0** | — | ✅ v0.5.23 cookbook | — | — | — |
| **Clover / xing4_0 (new arches)** | — | — | ✅ Both landed (#31306, #31589) | — | — |
| **LFM3** | — | — | — | ⚠️ Tokenizer bug closed (#18785) | ❌ Request closed (#15588) |

**Who's ahead:**
- **SGLang** leads on breadth — it is the only project with day-one cookbook coverage of Muse Spark Open and Beam-1.0, and the only one shipping unified-cache NVFP4 for MiniMax-M3.1.
- **llama.cpp** is the *architecture laboratory* — it shipped two entirely new architectures (Clover, xing4_0 with MLA/MoE/MTP) in a single day, adding real ggml-op surface only where needed.
- **vLLM** leads on **hardware-qualified** support — its DeepSeek-V4.1/ROCm/Gemma-MI350P work is deeper per-model but trails on release-blocking bugs for those same models on SM120.
- **Unsloth** leads on **fine-tuning-time** family coverage (Qwen2/2.5/3, Mistral NF4 fused decode).
- **Notable gap:** DeepSeek-V4.x is the most *fragile* frontier model across ecosystems today — every engine has an open or just-fixed DeepSeek issue.

---

## 4. Performance Frontier

Optimization effort is concentrated in five bands, with clear project specialization:

| Band | vLLM | SGLang | llama.cpp | Unsloth / LiteLLM |
|---|---|---|---|---|
| **KV cache** | TurboQuant OSCAR 2–3 bit presets (#65354); prefix-cache determinism bugs | Radix partial eviction (#48096) — preserve long device prefixes | Unified-KV `flash_attn_ext_rows` (#29510); `get_n_kv_rows()` fix (#31570) | MLX attention memory estimate (#16517) |
| **Batching / scheduling** | Batch-invariance roadmap (#27433 — 104 cmts) | PD placement by projected token load (#48098); decode admission accounting (#48037) | Multi-sequence unified attention (batched serving) | — |
| **Quantization** | 2–3 bit KV; ROCm INT4 QuickReduce (#57547) | Quark MXFP4 GEMM selection (#43360); INT8 LightningIndexer on NPU (#42224) | WebGPU `ARANGE`; Vulkan K-quant MulMat transposed layout (#22970) | Fused NF4 batch-1 decode (#16066, opt-in) |
| **Distributed serving** | MoE expert offloading RFC (#38256) | Adaptive speculative decoding (#23705); disaggregation admission | **NCCL TP via RPC (#28967)**; Vulkan TP allreduce (#25051) | — |
| **Kernels / hardware** | MFMA autotune gfx942 (#62909); FlyDSL all-reduce fusion | SM90/GLM-5.3, gfx1250 paged MLADecode, gfx1151 split-K GEMV, Hexagon HVX, CANN FA fix | stream-k tiling (#30784 — 10–13% prefill gap on 170-SM cards); Vulkan coopmat | cuDNN SDPA guard (#15934) |

**Frontier observations:**
1. **AMD is the new battleground.** Both vLLM and SGLang have dedicated MI300X/MI350P/gfx1151 optimization lines; llama.cpp has HIP/CDNA2 (with a build break on gfx90a — #30869). ROCm is graduating from "supported" to "optimized," but stability lags NVIDIA.
2. **KV-cache compression is converging on sub-4-bit.** vLLM's 136–200 B/token/KV-head (vs 262 for 4-bit) and Unsloth's MLX estimate reductions both aim at the same 256K-context economics problem.
3. **Speculative decoding is the active batching lever** — but it is also today's biggest correctness hazard (see §6).
4. **Multi-node TP is arriving** in the local runtime (llama.cpp NCCL/RPC) — historically a serving-engine-only capability.

---

## 5. Layer Positioning

| Layer | Project | Core value | Today's risk profile |
|---|---|---|---|
| **High-throughput serving engine** | vLLM, SGLang | Max throughput/latency on datacenter GPUs; PagedAttention/MoE. **SGLang** leans scheduling + speculative depth adaptive; **vLLM** leans kernel/HW breadth + batch invariance. | Severe open regressions on frontier HW (SM120, GB10, NVFP4). SGLang's MambaPool OOM (#47916) is architectural, not a one-off bug. |
| **Local / edge runtime** | llama.cpp, Ollama | Portability across 10+ backends; minimal dependencies; UX. llama.cpp is backend-matryoshka; Ollama is a UX shell over llama.cpp. | llama.cpp: rolling builds mean bisect discipline required. Ollama: scheduler deadlock (#17408) is a **core liveness** bug — most serious single item in all digests. |
| **Gateway / control plane** | LiteLLM | Cross-provider translation, routing, budgets, rate limits, observability. Today: **moving up-stack** into fleet management (control plane, RBAC, OCSF audit) and down-stack into Rust forwarding (#31263). | Reasoning-model streaming is brittle (#49795, #30765); budget accounting drifts under concurrency (#49675). Breaking change incoming (#49783). |
| **Fine-tuning** | Unsloth | Memory-efficient LoRA/QLoRA; fused kernels; MLX path for Apple silicon. Today: **expanding into serving** (Studio's llama-server, prompt cache, MLX inference). | The serving features inherit serving-grade bugs: 17x prompt-cache invalidation (#16538) and silent data corruption (#16528) are serious for anyone using Studio as a frontend. |

**Key structural insight:** The layers are **converging**. LiteLLM is building a control plane above gateways; Unsloth is building a serving UI below fine-tuning; llama.cpp is adding TP (a serving-engine capability). Expect multi-layer products (e.g., "gateway + control plane" or "fine-tune + serve") rather than single-layer tools over the next two quarters.

---

## 6. Trend Signals

### T1. Determinism is the new performance metric
vLLM's batch-invariance roadmap (#27433) and its cluster of temperature=0 nondeterminism bugs (#54521, #53257, #40896) are the **single most-commented theme across all six digests**. SGLang has the same problem (non-reproducible greedy decoding with DFLASH, #39597). llama.cpp has historically struggled with KV-cache/prefix-cache determinism (closed: #28527, #29045).
**Action:** Treat bit-exact reproducibility as a *feature flag you pin*, not a baseline. For evals, run either isolated-engine pinned requests or non-speculative decode paths.

### T2. Speculative decoding is a correctness liability today
Crash and nondeterminism reports tied to MTP/EAGLE/DFLASH appear in **four of six projects**:
- vLLM: MTP illegal memory access on long seqs (#40756); TP worker hang (#41530)
- SGLang: EAGLE/MTP startup OOM (#42510); missing KVIndexTranslator (#38029); DFLASH nondeterminism (#39597)
- llama.cpp: Gemma 4 + MTP abort (#30698)
- Unsloth: streaming reasoning prefixes break fallback (#49795 — LiteLLM, but same model-level cause)

**Action:** If you enable spec decode in production, validate `num_spec_tokens`, pin the engine version, and test mid-stream failure injection.

### T3. Rust frontends everywhere
vLLM (#44280), SGLang (#45816), and LiteLLM (#31263, #49606) all have active Rust migrations. The motivation is uniform: **sub-1ms gateway/frontend overhead and a memory-safe concurrency model for multi-tenant routing**. This also creates an internal-API break surface for anyone integrating below the OpenAI-compatible line.

**Action:** If you call vLLM/SGLang/LiteLLM *below* the OpenAI-compatible surface (gRPC, router internals, Python server APIs), start an inventory now — those are the components moving first.

### T4. Hardware fragmentation tax is rising
SM120 (RTX PRO 6000) and GB10/DGX Spark cause vLLM's most severe reports; NVFP4 MoE crashes at warmup on Blackwell (#48108) in SGLang; 5 different backends have open or recently-closed issues in llama.cpp (SYCL/oneAPI, HIP/gfx90a, Vulkan, CANN, Metal). **The performance ceiling is now defined by the weakest-qualified device, not the fastest.**

**Action:** Provision Blackwell cautiously. For workstation-class (SM120/GB10), run a canary deployment per driver/matrix before rollout. For AMD, prefer gfx942/gfx950 for serving; gfx90a (MI210) has an open llama.cpp build break.

### T5. Gateway layer is becoming a platform
LiteLLM's 9-PR control-plane stack adds JWT auth, scope RBAC, fan-out aggregation, and OCSF audit to Splunk/Sentinel/S3 — this converts "gateway" into "AI access control plane." Combined with the Rust migration and the incoming breaking `prompt_cache_breakpoint`→`cache_control` change, **LiteLLM is on a fast upgrade treadmill.**

**Action:** If you depend on LiteLLM's translation surface, budget one upgrade cycle per quarter with regression tests against your reasoning-model workflows. Watch PR #49783 for the breaking migration note.

### T6. Serving is creeping into fine-tuning, and vice versa
Unsloth's biggest items today (#16536 MLX load latency, #16538 prompt-cache regression, #16509 PyTorch/Llama fallback) are **serving problems, not training problems**. Conversely, serving engines are adding fine-tuning-adjacent features (vLLM's LoRA adapter support including multimodal, #57245; MoE expert offloading for memory-constrained LoRA training). The "train here / serve there" boundary is dissolving.

**Action:** Technical decision-makers evaluating a "fine-tune on-prem + serve on-prem" stack should evaluate Unsloth's Studio alongside vLLM/SGLang — not separately — and budget for it having its own serving-grade bug surface.

### T7. Local runtime is becoming a distributed runtime
llama.cpp landed NCCL collectives wired into the RPC server (#28967) and a Vulkan TP allreduce (spin-wait / SFD semaphores, #25051). This is a precursor to multi-node edge serving. Combined with Ollama's Rockchip NPU backend PR (#19480), the "local" tier is splitting into **edge-accelerated** and **multi-node** sub-categories.

**Action:** Design deployment topology with eventual multi-node local runtime in mind — particularly if you're currently using llama.cpp RPC for 2-GPU serving.

---

## Bottom Line for Decision-Makers

| If you are… | Adopt today | Hold / watch |
|---|---|---|
| **Datacenter serving on NVIDIA** | SGLang v0.5.23 for new model coverage; vLLM with per-model version pinning | vLLM SM120/GB10 deployments; non-deterministic temperature=0 paths on both |
| **Datacenter serving on AMD** | Both projects' MI300X/MI350P paths are actively tuned | ROCm DCP (#38709, SGLang); gfx90a build (llama.cpp #30869) |
| **Gateway / multi-provider** | LiteLLM (accept upgrade cadence) | Reasoning-model streaming paths (#30765, #49795), budget accounting (#49179, #49675) |
| **Local / edge** | llama.cpp for backend breadth, Ollama for UX | Ollama scheduler deadlock (#17408) under high concurrency; SM120-class workstations on llama.cpp |
| **Fine-tune + serve on one box** | Unsloth on MLX (Apple), or PEFT + offload_layers (with #15159 patch) | Unsloth Studio for anything MCP- or prompt-cache-heavy (#16538); silent S3 audio manifest risk (#16528) |

**Overall verdict:** The 2026-11-03 landscape favors **conservative upgrade discipline** over aggressive adoption. Every project in this set has at least one open severity-"High" item affecting production-relevant paths, and the two most structural changes (Rust frontends, control planes) are still mid-flight. The wins — lower-bit KV, dynamic spec depth, expert offloading, NCCL TP — are real, but each must be adopted individually with a version pin and a rollback plan.

---

## Per-Project Reports

<details>
<summary><strong>vLLM</strong> — <a href="https://github.com/vllm-project/vllm">vllm-project/vllm</a></summary>

# vLLM Digest — 2026-11-03

## 1. Today's Highlights
No new releases in the last 24h, but the issue tracker is dominated by **batch-invariance and determinism work**: #27433 (104 comments) tracks an ambitious feature/performance effort toward fully batch-invariant inference, while several temperature=0 nondeterminism bugs remain open across DeepSeek-V4, Qwen3.8-Flash, and prefix-caching paths. Hardware-specific instability on **SM120 (RTX PRO 6000 Blackwell)** and **GB10/DGX Spark** continues to generate the most severe regression reports, including a DeepSeek-V4.1-Flash boot failure and decode-throughput collapse. The long-running **Rust frontend parity** roadmap (#44280) is also active, signaling continued investment in the Python API server replacement path.

## 2. Releases & Breaking Changes
None in the last 24h. Note for tracking: PR #65365 indicates DeepSeek-V4 on CPU raises `KeyError: 'c1a'` at first decode step in **every release from v0.30.0 to v0.33.0** — upgrade plans should wait for the fix. [PR #65365](https://github.com/vllm-project/vllm/pull/65365)

## 3. New Model & Hardware Support
- **DeepSeek-V4.1-Flash on SM120**: boot failure fix PR allows 64-page block size for sparse-MLA and indexer backends. [PR #65356](https://github.com/vllm-project/vllm/pull/65356)
- **DeepSeek-V4.1 LoRA**: language-model adapter support including multimodal inputs (closed). [PR #57245](https://github.com/vllm-project/vllm/pull/57245)
- **Gemma 4 26B-A4B on AMD MI350P (gfx950)**: fused MoE Triton config added. [PR #65134](https://github.com/vllm-project/vllm/pull/65134)
- **Qwen3.5 FLA kernels on MI300X (gfx942)**: GatedDeltaNet prefill optimizations, validated on Qwen3.5-122B-A10B-FP8. [PR #41446](https://github.com/vllm-project/vllm/pull/41446)
- **Parakeet-TDT ASR** request remains open with high community interest (46 👍). [Issue #23943](https://github.com/vllm-project/vllm/issues/23943)

## 4. Performance & Optimization
- **TurboQuant OSCAR KV presets** (2–3 bit K/V codes, 136–200 B/token/KV-head vs 262 B for turboquant_4bit) proposed. [PR #65354](https://github.com/vllm-project/vllm/pull/65354)
- **Gemma 4 MoE on MI350P**: +2.9% peak throughput from tuning. [PR #65134](https://github.com/vllm-project/vllm/pull/65134)
- **ROCm all-reduce + RMSNorm fusion**: FlyDSL INT4 QuickReduce path (requires AITER ≥0.1.27). [PR #57547](https://github.com/vllm-project/vllm/pull/57547)
- **GDN prefill on gfx942**: 16×16 MFMA autotune configs for the two slowest kernels. [PR #62909](https://github.com/vllm-project/vllm/pull/62909)
- **MoE expert offloading** RFC (GPU cache + async pipeline, LFRU eviction) in progress. [Issue #38256](https://github.com/vllm-project/vllm/issues/38256)
- **SM120 decode throughput** for DeepSeek-V4.1-Flash requested as priority fix (currently requires `--enforce-eager` with CUDA graphs unusable). [Issue #56892](https://github.com/vllm-project/vllm/issues/56892)

## 5. Stability & Regressions (ranked by severity)
1. **SM120 boot failure** — DeepSeek-V4.1-Flash fails initialization on RTX PRO 6000 Blackwell; fix PR open. [PR #65356](https://github.com/vllm-project/vllm/pull/65356) / [Issue #56892](https://github.com/vllm-project/vllm/issues/56892)
2. **DeepSeek-V4 CPU `KeyError: 'c1a'`** — affects all releases v0.30.0–v0.33.0; fix PR open. [PR #65365](https://github.com/vllm-project/vllm/pull/65365)
3. **MTP speculative decoding illegal memory access** on long sequences (Qwen3.6-27B-FP8, v0.19.1). [Issue #40756](https://github.com/vllm-project/vllm/issues/40756)
4. **GB10/SM12.x hangs** with `cudagraph_mode=FULL_AND_PIECEWISE` + chunked prefill after ~6 requests (DeepSeek-V4-Flash); recurring with MiniMax-M2.7-NVFP4 TP=2. [Issue #40969](https://github.com/vllm-project/vllm/issues/40969), [Issue #41725](https://github.com/vllm-project/vllm/issues/41725)
5. **Non-determinism at temperature=0**: Qwen3.8-Flash-Next QSA prefill (#54521), DeepSeek-V4-Flash scaling with concurrency (#53257), prefix-caching first-request divergence (#40896, #5543). Note fix PR for LoRA+MRV2 KV-sharing fast prefill (closed). [PR #61162](https://github.com/vllm-project/vllm/pull/61162)
6. **TP worker hang → EngineDeadError** (DeepSeek-V4-Pro, TP=8, MTP). [Issue #41530](https://github.com/vllm-project/vllm/issues/41530)
7. **Batch invariance broken** with torch.compile/CUDA graphs on SM<90. [Issue #39096](https://github.com/vllm-project/vllm/issues/39096)

## 6. What This Means for Application Developers
- **Avoid temperature=0 for strict reproducibility today** on Qwen3.8-Flash-Next, DeepSeek-V4-Flash, and prefix-cached workloads — the batch-invariance roadmap (#27433) is active but not complete. Where determinism matters, pin requests to isolated engines or await fixes.
- **Blackwell workstation users (SM120)** should hold off on DeepSeek-V4.1-Flash and verify CUDA graph mode until #65356/the SM120 graph capture work lands; DGX Spark/GB10 deployments should test cudagraph modes carefully.
- **Long-context speculative decoding** (MTP + FP8) carries crash risk on long sequences — validate `num_spec_tokens` settings before production.
- **KV/prefix caching + LoRA** on MRV2 had a correctness/CUDA fault; the fallback fix is merged-closed, so upgrade when available.
- **Cost/latency wins coming** from lower-bit KV (TurboQuant OSCAR), MoE expert offloading, and ROCm all-reduce fusion — worth benchmarking on MI300X/MI350P if you're on AMD.

</details>

<details>
<summary><strong>SGLang</strong> — <a href="https://github.com/sgl-project/sglang">sgl-project/sglang</a></summary>

# SGLang Digest — 2026-11-03

## Today's Highlights

SGLang shipped **v0.5.23**, a large release spanning 1007 PRs from 257 contributors, with new model support for Muse Spark Open and Beam-1.0. Activity across the tracker shows heavy work on Hopper/RDNA/ROCm performance paths (GLM-5.3-Flash SM90 tuning, gfx1151 GEMM, INT8 LightningIndexer on NPU) alongside a steady stream of correctness fixes in memory pooling, speculative decoding, and MoE routing. The rust-server `api.v1` migration is progressing through the processor and router components.

---

## Releases & Breaking Changes

- **v0.5.23** — 1007 PRs, 257 contributors. New model cookbook entries for Muse Spark Open (LLM/VLM) and Beam-1.0 (LLM/VLM). [Release](https://github.com/sgl-project/sglang/releases)
- **Migration in progress:** RFC #45816 (shared `api.v1.Generate` backend) is moving `sgl-processor` and `sgl-router` onto the unified rust-server API — relevant if you depend on internal router/processor interfaces. [#45816](https://github.com/sgl-project/sglang/issues/45816)
- **Refactor:** `TokenizerWorker` being folded into `TokenizerManager`; changes multi-tokenizer IPC behavior and PD bootstrap paths. [#48119](https://github.com/sgl-project/sglang/pull/48119)
- **Refactor:** Scheduler waiting queue and chunked slot converted to `RequestList` objects, simplifying abort handling. [#43910](https://github.com/sgl-project/sglang/pull/43910)

## New Model & Hardware Support

- **Muse Spark Open** (LLM/VLM) and **Beam-1.0** (LLM/VLM) added to cookbook in v0.5.23. [Cookbook](https://docs.sglang.io/cookbook/)
- **MiniMax-M3.1** unified-cache external linker support (NVFP4 pool groups: KV, indexer, SWA). [#48125](https://github.com/sgl-project/sglang/pull/48125)
- **Qwen MTP model-aware shard selection** in the loader, so main and draft models sharing a checkpoint can select different shards. [#44702](https://github.com/sgl-project/sglang/pull/44702)
- **AMD gfx1250**: paged Gluon MLA decode in the aiter backend (bf16 Q over bf16/FP8 KV). [#44458](https://github.com/sgl-project/sglang/pull/44458)
- **AMD gfx1151 (Strix Halo)**: split-K bf16 GEMV path for single-row decode. [#47077](https://github.com/sgl-project/sglang/pull/47077)
- **AMD ROCm Quark MXFP4** dense GEMM selection via `--fp4-gemm-backend aiter|triton`. [#43360](https://github.com/sgl-project/sglang/pull/43360)
- **NPU A2/A3**: INT8 LightningIndexer for DSA indexing. [#42224](https://github.com/sgl-project/sglang/pull/42224)

## Performance & Optimization

- **GLM-5.3-Flash SM90 (Hopper) optimization tracker** open as part of Q4 kernel roadmap; related SM120 tracking ongoing. [#45373](https://github.com/sgl-project/sglang/issues/45373)
- **Adaptive speculative decoding roadmap** — dynamic draft depth based on observed acceptance rates for agentic/mixed workloads. [#23705](https://github.com/sgl-project/sglang/issues/23705)
- **PD prefill placement by projected per-rank token load** to reduce cross-rank prefix misses in multi-turn conversations. [#48098](https://github.com/sgl-project/sglang/pull/48098)
- **Radix cache partial eviction** — opt-in tail chunk size preserves long device prefixes instead of dropping whole leaves. [#48096](https://github.com/sgl-project/sglang/pull/48096)
- **Decode admission accounting** cached across host-staged transfer passes (disaggregation). [#48037](https://github.com/sgl-project/sglang/pull/48037)
- **NPU/AMD perf trackers**: Qwen3.8-Flash-Next MI355X (gfx950) optimization open. [#44304](https://github.com/sgl-project/sglang/issues/44304)

## Stability & Regressions

Ranked by severity:

1. **High — CUDA OOM under admission bursts:** `MambaPool.copy_from` gathers all source temporal states into one `(n × state_size)` temp buffer; OOMs on hybrid mamba/KDA models (e.g., GLM-5.3-Flash) under parallel admission. No fix PR yet. [#47916](https://github.com/sgl-project/sglang/issues/47916)
2. **High — Startup OOM with EAGLE/MTP same-checkpoint drafts:** redundant `embed_tokens`/`lm_head` copies held during KV pool sizing under-size the pool. [#42510](https://github.com/sgl-project/sglang/issues/42510)
3. **High — Blackwell NVFP4 MoE crash at warmup:** `AttributeError: 'g1_scale_c'` with ModelOpt NVFP4 under default `--moe-runner-backend auto`. [#48108](https://github.com/sgl-project/sglang/issues/48108)
4. **Medium — ROCm DCP crash:** `--dcp-size > 1` crashes every rank on first forward (`forward_absorb_rocm_core` unpack mismatch). [#38709](https://github.com/sgl-project/sglang/issues/38709)
5. **Medium — ROCm DeepSeek-V4.1 FP4 indexer** still exhausts HBM under long-context AgentX despite #37660. [#39441](https://github.com/sgl-project/sglang/issues/39441)
6. **Medium — Non-reproducible greedy decoding** with DFLASH spec decoding + FlashInfer autotune across restarts. [#39597](https://github.com/sgl-project/sglang/issues/39597)
7. **Medium — TRT-LLM MoE expert recording lost:** FlashInfer TRT-LLM routing bypasses expert selection, breaking distribution recording. **Fix PR open:** [#48142](https://github.com/sgl-project/sglang/pull/48142)
8. **Medium — Graph batch capture crash:** `SGLANG_GRAPH_BATCH_CAPTURE` IndexError with DSA dual-graph / LoRA / PDMUX multi-graph buckets. [#39092](https://github.com/sgl-project/sglang/issues/39092)
9. **Low — EAGLE DSA draft** missing `KVIndexTranslator` in FP8 MHA prefix reads. [#38029](https://github.com/sgl-project/sglang/issues/38029)
10. **Low — qwen_vl:** literal `<image>` in text aborts requests with 500 (token/data mismatch). [#43979](https://github.com/sgl-project/sglang/issues/43979)
11. **Low — DeepGEMM import:** missing CUDA toolkit raises `AssertionError` and empties the model registry even for non-DeepGEMM models. **Fix PR open:** [#48140](https://github.com/sgl-project/sglang/pull/48140)

CI health: coredump tracker #26340 (353 comments) and flaky-test tracker #42752 (166 comments) remain active, indicating sustained CI instability in NVIDIA `PR Test Base`/`Extra` runs. [#26340](https://github.com/sgl-project/sglang/issues/26340) · [#42752](https://github.com/sgl-project/sglang/issues/42752)

## What This Means for Application Developers

- **Upgrade to v0.5.23** if you need Muse Spark Open / Beam-1.0 support; otherwise, the `api.v1` router migration is the main compatibility item to watch if you integrate below the OpenAI-compatible surface.
- **Agent/multi-turn workloads:** two in-flight changes directly target your pain points — dynamic speculative depth (#23705) and prefix-preserving placement across prefill ranks (#48098, #48096). If you serve long conversations with PD disaggregation, track these.
- **Hybrid mamba models:** avoid aggressive parallel admission settings until #47916 is fixed; similarly, size KV pools manually if using same-checkpoint EAGLE/MTP drafts (#42510).
- **Blackwell + NVFP4:** pin `--moe-runner-backend` explicitly rather than relying on `auto` until #48108 lands.
- **Deterministic evals:** if you rely on greedy decoding for reproducibility, disable FlashInfer autotune or avoid DFLASH+restart workflows (#39597).
- **Client disconnect handling:** a fix (#46586) is in flight to promptly abort orphaned streaming requests — relevant for cost control in chat UIs and gateways that cancel streams. [#46586](https://github.com/sgl-project/sglang/pull/46586)

</details>

<details>
<summary><strong>llama.cpp</strong> — <a href="https://github.com/ggml-org/llama.cpp">ggml-org/llama.cpp</a></summary>

# llama.cpp Digest — 2026-11-03

## 1. Today's Highlights

llama.cpp had a heavy release day with ten builds landing in 24 hours (b12054–b12066), headlined by a unified-KV performance rework for multi-sequence attention (`flash_attn_ext_rows`, #29510) and a new Clover MoE architecture (#31306). On the bug side, the SYCL backend drew sustained attention with a memory-leak report closed after 21 comments (#27845) and a new oneAPI 2026.2.0 segfault still open (#31064), while a CUDA stream-k tiling issue on RTX 5090 claims a 10–13% long-context prefill penalty (#30784). Backend work is noticeably active across WebGPU, Hexagon/HVX, CANN (Ascend), and Vulkan tensor parallelism.

## 2. Releases & Breaking Changes

Releases are rolling per-commit builds; no formal API break is announced, but note behavioral/config changes:

- **b12066** — WebGPU: `ARANGE` op added. [Release](https://github.com/ggml-org/llama.cpp/releases/tag/b12066)
- **b12065** — KV cache: `get_n_kv_rows()` no longer returns 0 for sequences without cells (fixes graph-build failure for cache-sharing drafters, e.g. Gemma 4 assistant, at server warmup). Relevant if you use speculative decoding with shared caches. [PR #31570](https://github.com/ggml-org/llama.cpp/pull/31570)
- **b12061** — `mtmd`: Pocket TTS voices without voice cloning (voice prompt stored as backbone KV state). [PR #31429](https://github.com/ggml-org/llama.cpp/pull/31429)
- **b12058** — `imatrix`: fix `--nextn` crash. [PR #31522](https://github.com/ggml-org/llama.cpp/pull/31522)
- **b12057** — Context: simplified setters refactor (internal API cleanup). [PR #29730](https://github.com/ggml-org/llama.cpp/pull/29730)
- **b12054** — `ggml-zdnn`: shared host buffers (IBM Z / zDNN accelerator). [PR #31481](https://github.com/ggml-org/llama.cpp/pull/31481)

Migration note: if you build graphs directly over RPC (not through the builder), review #31592 and #31584 below — both harden view/argument bounds and may reject previously accepted malformed graphs.

## 3. New Model & Hardware Support

- **Clover (new architecture)** — AR-only MoE with hybrid causal attention, GGUF conversion, loading, LoRA/recurrent-state reuse. [b12060 / PR #31306](https://github.com/ggml-org/llama.cpp/pull/31306)
- **xing4_0** — Architecture registration, HF→GGUF conversion, and mHC/MLA/MoE/MTP graphs; no new ggml ops. [PR #31589](https://github.com/ggml-org/llama.cpp/pull/31589)
- **Pocket TTS (kyutai)** — predefined voices without cloning via `mtmd`. [PR #31429](https://github.com/ggml-org/llama.cpp/pull/31429)
- **WebGPU** — `ARANGE` support landed ([b12066](https://github.com/ggml-org/llama.cpp/releases/tag/b12066)); `MEAN` (F32, contiguous rows, offset/strided views) in review. [PR #31591](https://github.com/ggml-org/llama.cpp/pull/31591)
- **Hexagon (Qualcomm HTP)** — tiled HVX transpose path for permuted CONT/CPY, replacing per-element DMA reshape fallback. [b12064 / PR #31446](https://github.com/ggml-org/llama.cpp/pull/31446)
- **Ascend NPU (CANN)** — Flash Attention NaN fix: pass `attenMask` instead of stuffing `-inf` into `pseShift`. [PR #31590](https://github.com/ggml-org/llama.cpp/pull/31590)
- **Vulkan** — 128×128 coopmat matmul tile re-enabled on fixed AMD proprietary (AMDVLK) drivers. [PR #31587](https://github.com/ggml-org/llama.cpp/pull/31587)
- **Multi-node** — NCCL collectives for tensor parallelism, wired into the RPC server (merged as #28967). [PR #28967](https://github.com/ggml-org/llama.cpp/pull/28967)

## 4. Performance & Optimization

- **Unified KV for multi-sequence** — `flash_attn_ext_rows` reuses `llm_attn_mask` + `kv_rows` for more efficient unified attention across sequences (batched serving). [b12059 / PR #29510](https://github.com/ggml-org/llama.cpp/pull/29510)
- **RTX 5090 long-context prefill** — stream-k block count not a multiple of `ntiles_dst` on 170-SM parts costs **10–13%** vs RTX 4090; author measures **+37–40%** when fixed. Open, code unchanged on master. [Issue #30784](https://github.com/ggml-org/llama.cpp/issues/30784)
- **Vulkan tensor parallelism** — allreduce kernel with spin-wait (NVIDIA) / SFD semaphores (AMD), optimized for 2 devices; ring-buffer and wider device counts to follow. [PR #25051](https://github.com/ggml-org/llama.cpp/pull/25051)
- **Vulkan K-quant prompt processing** — A-matrix transposed layout for Q4_K/Q5_K/Q6_K mul_mat. [PR #22970](https://github.com/ggml-org/llama.cpp/pull/22970)
- **Qwen3.8-Flash-Next** — conv-state tail write without intermediate `cont`, **~2% generation win**, verified on CUDA/CPU/meta. [PR #31443](https://github.com/ggml-org/llama.cpp/pull/31443)
- **CUDA Q8_1 correction sum** — align quantized-input summation with integer dot product and CPU behavior (accuracy, intermittent Q4_1 mul_mat). Closed. [PR #31247](https://github.com/ggml-org/llama.cpp/pull/31247)
- **Windows model load** — unbuffered (DirectIO-equivalent) reads in review; testers requested across devices. [PR #26014](https://github.com/ggml-org/llama.cpp/pull/26014)

## 5. Stability & Regressions

Ranked by severity:

1. **CUDA fatbin fatal error** — `fattn.cu:579` build/runtime failure with `GGML_CUDA_FA_ALL_QUANTS` + NCCL. Open, no fix PR. [Issue #24324](https://github.com/ggml-org/llama.cpp/issues/24324)
2. **SYCL segfault on oneAPI 2026.2.0** — Arc B580 + 5700X3D crashes. Open. [Issue #31064](https://github.com/ggml-org/llama.cpp/issues/31064)
3. **Gemma 4 + `--mmproj` + MTP startup abort** — `GGML_ASSERT(ubatch->embd_state)`, regression from #30257. Open, high-impact for multimodal + speculative users. [Issue #30698](https://github.com/ggml-org/llama.cpp/issues/30698)
4. **Gemma 4 MTP crash** — closed as stale; watch if you rely on MTP. [Issue #25522](https://github.com/ggml-org/llama.cpp/issues/25522)
5. **Metal decode stall, GLM-5.3-Flash** — fused Lightning Indexer falls back to CPU, decode stalls. Open. [Issue #29867](https://github.com/ggml-org/llama.cpp/issues/29867)
6. **Vulkan `--moe-cache-mib` load crash** — RX 6900 XT / Windows; closed. [Issue #30697](https://github.com/ggml-org/llama.cpp/issues/30697)
7. **HIP/CDNA2 build failure (gfx90a)** — MI210 compile dies. Open. [Issue #30869](https://github.com/ggml-org/llama.cpp/issues/30869)
8. **Vulkan build break (GCC 16/clang 22)** — `signbit`/`isnan`/`isinf` undeclared since #27512; closed. [Issue #31445](https://github.com/ggml-org/llama.cpp/issues/31445)
9. **Granite SWA per-layer RoPE theta ignored** — no released checkpoint affected today, but a trap for future `granite_swa` weights. [Issue #31585](https://github.com/ggml-org/llama.cpp/issues/31585)
10. **KV cache / prefix-cache determinism** — quantized K cache produced different output on repeat prefix hits (closed); iSWA `seq_pos_max` cell leak after partial `seq_rm` (closed). [#28527](https://github.com/ggml-org/llama.cpp/issues/28527), [#29045](https://github.com/ggml-org/llama.cpp/issues/29045)
11. **Security-adjacent hardening in review** — `ggml_argmax` OOB write on unchecked `src0->ne[1]` reachable via forged RPC graphs ([PR #31592](https://github.com/ggml-org/llama.cpp/pull/31592)); view bounds by stride-reach ([PR #31584](https://github.com/ggml-org/llama.cpp/pull/31584)); signed-overflow in `ggml_roll` bounds ([PR #30375](https://github.com/ggml-org/llama.cpp/pull/30375)). **If you expose RPC or accept client-built graphs, prioritize these.**

Long-standing server-uptime complaint resurfaced (Vulkan, RX 7900 XTX, slowdown over time, closed stale): [#22360](https://github.com/ggml-org/llama.cpp/issues/22360).

## 6. What This Means for Application Developers

- **Batched/multi-agent serving gets cheaper:** #29510 + #31570 together make unified KV across sequences more efficient and remove a warmup-era graph-build failure — safe to adopt for multi-session servers with shared-prefix drafters.
- **MoE and hybrid attention are still first-class:** Clover and xing4_0 (MLA/MoE/MTP graphs) land without new ops, meaning existing quantization and LoRA tooling should apply — but expect early arch support, so pin builds for production and test GGUF conversions yourself.
- **Don't assume RTX 5090 is fastest:** the stream-k tiling gap (#30784) is a real, measurable prefill regression at long context; benchmark per-GPU if you're provisioning 170-SM cards. A patch is proposed but not merged.
- **Multimodal + speculative decoding has a known abort** (#30698): if you ship Gemma 4 with `--mmproj` and `-md`, hold on the affected commit range or test startup explicitly in CI.
- **RPC-facing deployments:** three bound-check PRs (#31592, #31584, #30375) address OOB/UB reachable via crafted graphs. Track these — vetting untrusted graph input over RPC is the practical risk.
- **Multi-node GPU is coming:** NCCL collectives via RPC (#28967) and Vulkan allreduce (#25051) signal tensors-parallel serving moving from 2-GPU to multi-node; design your deployment topology with that trajectory in mind.
- **Frontend side:** the embedded server UI is maturing (PDF thumbnails, provider/model management, download-state fixes in [#31467](https://github.com/ggml-org/llama.cpp/pull/31467), [#31468](https://github.com/ggml-org/llama.cpp/pull/31468), [#31545](https://github.com/ggml-org/llama.cpp/pull/31545)) — if you'd otherwise build a console, evaluate it before writing one.

</details>

<details>
<summary><strong>Ollama</strong> — <a href="https://github.com/ollama/ollama">ollama/ollama</a></summary>

# Ollama Digest — 2026-11-03

## 1. Today's Highlights
Ollama shipped **v0.40.14**, a small patch release that fixes output-token limiting for `ollama launch claude` against cloud models, repairs gzip'd cloud web-search decoding on `/api/chat`, and tightens OpenAI-compatible endpoint behavior. Activity around the release is split between a **critical scheduler deadlock** (#17408) that remains unfixed on `main`, and a long tail of OpenAI-compat correctness issues — including a misleading 400 on tool results containing images (#19479) and unstable response IDs in the compat layer. Hardware coverage expands modestly with an in-flight Rockchip NPU backend PR, while a Pascal/CUDA regression in 0.40.13 appears already triaged.

## 2. Releases & Breaking Changes
- **v0.40.14 released** — patch release. Changes:
  - `ollama launch claude` now applies the model's output token limit, so cloud models no longer hit the 128k default cap in Claude Code.
  - Cloud web-search responses on `/api/chat` are decoded correctly, including compressed/gzip responses.
  - OpenAI-compatible endpoints adjusted (release notes truncated; verify behavior against your client).
  - No config/migration steps indicated.

## 3. New Model & Hardware Support
- **Rockchip NPU backend (proposed)** — PR #19480 adds a `rocket`/`rknpu` backend for RK3588/RK3576 SoCs on Linux, offloading prefill (`MUL_MAT`, `MUL_MAT_ID`, flash attention) to the NPU while keeping decode on CPU. Early-stage; not landed. https://github.com/ollama/ollama/pull/19480
- **Qwen3.8-27B hybrid-attention GGUF load failure (closed)** — `qwen3next` architecture import from `huihui-ai` GGUFs failed with missing `attn_qkv`/`attn_gate` projections at layer 64. Issue closed, but no linked fix PR; assume resolution status should be verified before relying on this architecture import path. https://github.com/ollama/ollama/issues/19459
- **Qwen3.5 0.8B Q4_K_M request (open)** — the only Qwen3.5 variant missing a `q4_K_M` tag; CPU-bound users are requesting it for ~2x throughput over q8. https://github.com/ollama/ollama/issues/19436

## 4. Performance & Optimization
- **Log verbosity filtering (open PR)** — #17913 filters per-request `llama-server` logs unless debug is enabled, currently ~20 lines of slot bookkeeping per request flowing into journald. Meaningful for log volume and I/O on busy servers, not throughput. https://github.com/ollama/ollama/pull/17913
- **Context length reporting (open PR)** — #19211 makes `/api/tags` report `min(native, OLLAMA_CONTEXT_LENGTH)` so clients stop assuming a context window the server won't honor. Prevents silent truncation and mis-sized client-side buffers. https://github.com/ollama/ollama/pull/19211
- **VRAM over-allocation (open, longstanding)** — #10128 reports Ollama using ~10GB on a 2xGPU setup for Qwen2.5 7B @ 262k context with `OLLAMA_KV_CACHE_TYPE=q8_0` and flash attention enabled — i.e., VRAM estimation undershoots severely relative to configured KV cache. Relevant for context-heavy serving. https://github.com/ollama/ollama/issues/10128

## 5. Stability & Regressions (ranked)
1. **Scheduler deadlock (open, critical)** — #17408: when the eviction target is resurrected by a concurrent request mid-load, the pending loop blocks forever and *all* subsequent load requests hang. Reported on 0.31.1; logic reportedly unchanged on `main`. No fix PR linked. This is the highest-severity item today for multi-tenant or high-concurrency deployments. https://github.com/ollama/ollama/issues/17408
2. **Pascal/CUDA regression in 0.40.13 (closed)** — #19485: CUDA error in `ggml-cuda.cu` on NVIDIA Pascal GPUs. Closed same day; 0.40.14 is the likely mitigation — validate on Pascal hardware before rolling out. https://github.com/ollama/ollama/issues/19485
3. **Vision inference 500 on GPU (open)** — #19482/#19483: `qwen2.5vl:3b` returns HTTP 500 "prediction aborted, token repeat limit reached" when the vision encoder runs on NVIDIA GPU; text-only GPU and full-vision CPU work. Duplicate filings suggest a real encoder/GPU path issue. https://github.com/ollama/ollama/issues/19482
4. **OpenAI-compat: images in tool results misparsed (open)** — #19479: a `role: "tool"` message with image parts yields a misleading 400 "Unexpected tool call id … in tool results" despite correct ID pairing. Blocks multimodal tool-use over `/v1`. https://github.com/ollama/ollama/issues/19479
5. **Compat response ID collisions (open PR)** — #19401: `chatcmpl-%d` etc. draw from `rand.Intn(999)` — 999 distinct IDs. Breaks client-side dedupe/tracing at scale. Fix PR exists. https://github.com/ollama/ollama/pull/19401
6. **`top_p` override (open PR)** — #18691: omitted `top_p` is forced to 1.0, overriding the Modelfile's `PARAMETER top_p`. Same bug class as the earlier `temperature` fix. https://github.com/ollama/ollama/pull/18691
7. **401 auth details dropped (open)** — #19489: `AuthorizationError`/`SigninURL` lost when a 401 body isn't valid JSON — cloud auth failures surface as opaque errors. https://github.com/ollama/ollama/issues/19489
8. **macOS updater loop (open)** — #19472: app offers "Restart to update" for the already-installed version and demands admin rights to chown `/Applications/Ollama.app`. Fix PRs #19487 and #19488 exist. https://github.com/ollama/ollama/issues/19472
9. **`HumanNumber` rounding overflow (open)** — #19490 / PR #19486: 999500 renders as "1000K" instead of "1M" in `/api/tags` parameter sizes, `ollama list` output. Cosmetic but visible to clients. https://github.com/ollama/ollama/pull/19486
10. **ROCm `SOLVE_TRI` failure (closed)** — #15343: Qwen models fail on RX 9060 XT with `hipErrorInvalidDeviceFunction`. Closed after ~7 months; ROCm users should retest on current release.
11. **Tokenizer edge case (closed)** — #18785: `lfm2:24b` silently drops the bare `"python"` token (no leading space) from output. Closed; verify fix in current build if you serve LFM2.

## 6. What This Means for Application Developers
- **Cloud via Claude Code now respects model output limits.** If you were truncating or hitting the 128k cap via `ollama launch claude`, upgrade to 0.40.14. Cloud web-search responses on `/api/chat` are also reliably decoded now — safe for RAG/agent search loops.
- **Avoid assuming scheduler liveness under load.** Until #17408 is fixed, if you run high-concurrency workloads with memory pressure and eviction (large models, tight VRAM, many concurrent users), consider serializing/queueing load requests at the application layer or pinning models (`OLLAMA_KEEP_ALIVE` long, pre-loaded) to avoid the eviction path entirely.
- **OpenAI-compat has rough edges you'll trip over.** Image-bearing tool results currently 400 (#19479); response IDs collide at scale (#19401); omitted `top_p`/`temperature` override your Modelfile defaults (#18691, #17763). Pin these expectations in your client and set sampling params explicitly rather than relying on defaults.
- **Treat `details.context_length` from `/api/tags` with suspicion** until #19211 lands — with `OLLAMA_CONTEXT_LENGTH` set below native, the API over-reports.
- **Pascal GPUs:** 0.40.13 was broken; take 0.40.14 and validate. **ROCm and Vulkan users:** Vulkan-only machines still get CUDA bloat with no Vulkan backend (#19474) — a gap for Intel/AMD iGPU fleets.
- **Model availability:** if you need Qwen3.5 0.8B on CPU, a `q4_K_M` tag doesn't exist yet (#19436) — import from HuggingFace or upvote.

---
*Sources: github.com/ollama/ollama — release v0.40.14, issues #17408, #10128, #19461, #18785, #19459, #19350, #19485, #15343, #19482, #19483, #19436, #19473, #19490, #19489, #19479, #19474, #19472; PRs #19233, #19488, #19487, #19211, #19486, #19484, #18691, #19481, #19480, #19478, #19476, #19477, #18990, #19153, #19460, #19470, #19475, #17913, #19401.*

</details>

<details>
<summary><strong>LiteLLM</strong> — <a href="https://github.com/BerriAI/litellm">BerriAI/litellm</a></summary>

# LiteLLM Digest — 2026-11-03

## 1. Today's Highlights

The dominant theme today is the Rust migration of the gateway: parent ticket #31263 continues to accumulate discussion (33 comments, 22 👍), and a coordinated batch of PRs (#49606, #49607) lands opt-in Rust forwarding supervision and Helm toggles. In parallel, BerriAI is pushing a nine-PR "control plane" stack (#49654–#49662) that adds a global fleet management API with JWT auth, scope RBAC, fan-out aggregation, and OCSF audit logging to Splunk/Sentinel/S3. No releases in the last 24h; the issue queue is active with translation-layer streaming bugs and rate-limiter accounting issues, with several fixes already up for review.

## 2. Releases & Breaking Changes

No new releases in the last 24 hours.

Notable in-flight breaking change:
- **PR #49783** — `feat(anthropic)!:` carries OpenAI's `prompt_cache_breakpoint` to Claude's `cache_control` across Anthropic, Bedrock, Vertex AI, and Azure AI. Marked breaking; per the summary, a marker Claude cannot honor will be handled differently than today. https://github.com/BerriAI/litellm/pull/49783

Config-surface change (opt-in, not breaking):
- **PR #49606** adds `LITELLM_RUST=1` to supervise an opt-in Rust forwarding server on the public port while Python keeps serving requests. https://github.com/BerriAI/litellm/pull/49606
- **PR #49607** exposes the same toggle via Helm values in both charts; unset preserves image defaults. https://github.com/BerriAI/litellm/pull/49607

## 3. New Model & Hardware Support

- **PR #39195** — Adds SLNG as a TTS/STT provider (13 TTS + 8 STT models) targeting EU/AU/regional infrastructure. https://github.com/BerriAI/litellm/pull/39195
- **Issue #20064** (open, 11 comments) — Feature request for wildcard-based model discovery (`my-custom-service/*`) in `config.yaml` to support custom providers without enumerating every model. https://github.com/BerriAI/litellm/issues/20064
- **Issue #33670** (closed) — Requests for OpenCode Zen and Agnes-AI provider endpoints and multi-key-per-model support; closed without landing per the summary. https://github.com/BerriAI/litellm/issues/33670

## 4. Performance & Optimization

- **Rust gateway migration (#31263)** — Parent ticket remains open with active discussion. The linked blog describes the goal of sub-1ms gateway overhead; an early beta tester group is being recruited. This is the project's primary latency-reduction track. https://github.com/BerriAI/litellm/issues/31263
- **Control plane fan-out (PR #49656)** — Introduces bounded scatter/gather across dataplanes with per-backend failure isolation, so one dead dataplane no longer fails the whole fleet view. Relevant for operators aggregating usage/models/teams across many proxies. https://github.com/BerriAI/litellm/pull/49656
- **No concrete benchmark numbers** were published in the last 24h.

## 5. Stability & Regressions

Ranked by severity:

1. **Streaming fallback lost on reasoning-only prefixes (#49795, open)** — `sent_first_chunk` flips on any delta including reasoning, so a mid-stream failure during a thinking block is misreported as post-first-chunk and the fallback is skipped even though the client received nothing. Directly impacts high-effort reasoning models behind the router. https://github.com/BerriAI/litellm/issues/49795
2. **v3 rate limiter TPM counter outlives its window (#49675, open)** — Under upstream 429s, keys/teams with both RPM and TPM limits are rejected below their configured `tpm_limit`. Affects admission control correctness under provider degradation. https://github.com/BerriAI/litellm/issues/49675
3. **`/v1/messages` streaming drops first text delta after thinking→text transition (#30765, open; duplicate report #25214)** — Breaks Claude Code and Bedrock reasoning-model streaming through the Anthropic-compatible proxy. Two separate reports, no fix PR visible. https://github.com/BerriAI/litellm/issues/30765 · https://github.com/BerriAI/litellm/issues/25214
4. **Budget window spend lost on `/key/update` during in-flight request (#49179, closed)** — Window total (`LiteLLM_BudgetWindowSpend`) misses the in-flight request's cost; `SpendLogs` and the key's `spend` column are correct. Closed 2026-11-02. https://github.com/BerriAI/litellm/issues/49179
5. **Gemini 3.x thought-signature corruption on router fallback (#32140, closed)** — `vertex_ai` ↔ `gemini` fallback replays endpoint-bound signatures, yielding 400 "Corrupted thought signature." Closed 2026-11-03. https://github.com/BerriAI/litellm/issues/32140
6. **`langfuse_otel` silently drops all traces (#43032, open)** — OTLP endpoint built without `/v1/traces`, so self-hosted Langfuse ingestion 404s every span. Observability blind spot; no fix PR referenced. https://github.com/BerriAI/litellm/issues/43032
7. **`safe_deep_copy`/`safe_dumps` crash under concurrent mutation (#34471, closed)** — `dictionary changed size during iteration` under load. Closed 2026-11-03. https://github.com/BerriAI/litellm/issues/34471
8. **Streaming Responses API `flex_unavailable` mapped to 500 instead of 429 (#49736, open)** — Non-streamed path correctly returns 429; streaming path misclassifies, defeating client retry/backoff logic. https://github.com/BerriAI/litellm/issues/49736
9. **Proxy strips `delta.reasoning_items` on fast-serialization path (#38907, closed)** — Encrypted reasoning state lost only via the proxy SSE path, not the SDK. Closed 2026-11-02. https://github.com/BerriAI/litellm/issues/38907

Fix PRs in review (not yet merged):
- **PR #40016** — Prevents double-counting Anthropic cache-read tokens when `custom_cost_per_token` sets `cache_read_input_token_cost`. https://github.com/BerriAI/litellm/pull/40016
- **PR #39515** — Stops empty `budget_duration` from being silently interpreted as a daily midnight-UTC reset (which wiped team/user/key spend every 24h). https://github.com/BerriAI/litellm/pull/39515
- **PR #39993** — Gates `budget_duration` behind admin check on `/key/update`. https://github.com/BerriAI/litellm/pull/39993
- **PR #39962** — Strips `reasoning_details` from messages for Fireworks AI. https://github.com/BerriAI/litellm/pull/39962
- **PR #40194** — Keeps `drop_params`/`additional_drop_params` out of `/v1/images/edits` multipart bodies without polluting `all_litellm_params`. https://github.com/BerriAI/litellm/pull/40194

## 6. What This Means for Application Developers

- **Reasoning-model streaming is the fragile spot.** If you route high-effort models (Gemini 3.x thinking, Bedrock reasoning models, Claude extended thinking) through `/v1/messages` or the fallback router, expect dropped post-thinking deltas (#30765/#25214) and missed fallbacks on reasoning-only prefixes (#49795). Test with mid-stream failure injection before relying on router fallback for UX-critical paths.
- **Retry logic may be misled on flex tier.** Streaming clients hitting `service_tier: "flex"` will see 500s where 429s are expected (#49736) — don't rely on status-code-based backoff alone for that path.
- **Claude prompt caching is coming for OpenAI-shaped apps.** PR #49783 lets `prompt_cache_breakpoint` survive translation to Claude backends; if you've been passing cache hints that were silently dropped, that's worth tracking. It's marked breaking, so review the migration note before upgrading.
- **Observability gap if you self-host Langfuse.** #43032 means traces are dropped at export with no error surfaced — validate your Langfuse ingest before trusting span coverage.
- **Multi-proxy operators get a path to fleet-level management.** The control-plane stack (#49654–#49662) adds authenticated cross-dataplane calls, RBAC-scoped management, and audit sinks to Splunk/Sentinel/S3 — enterprise-licensed, behind a `--control-plane` flag on the existing image. Worth watching if you currently fan out across regional proxies manually.
- **Budget/rate-limit bookkeeping has known edges.** In-flight `/key/update` races (#49179) and TPM-window leakage under 429s (#49675) mean budget enforcement can drift under concurrency; prefer per-request spend logs for billing reconciliation over window aggregates.

</details>

<details>
<summary><strong>Unsloth</strong> — <a href="https://github.com/unslothai/unsloth">unslothai/unsloth</a></summary>

# Unsloth Digest — 2026-11-03

## Today's Highlights
No releases landed in the last 24h, but the queue is unusually busy on the serving side: a substantial MLX backend overhaul (faster load/reload, corrected memory accounting) and a prompt-cache invalidation regression on hybrid/recurrent models under dynamic MCP loading are the two items most worth attention. On the training side, fused NF4 batch-1 decode is being widened from Llama to Mistral/Qwen2/Qwen3, and a MoE + `offload_layers` PEFT crash has a fix in flight. Issue volume skews heavily toward Unsloth Studio (desktop/web UI) rather than core fine-tuning.

## Releases & Breaking Changes
No new releases in the last 24h.

Config/interface changes in flight that affect users of Studio's llama-server integration:
- **`--chat-template-kwargs` merging** — Studio currently emits managed kwargs (e.g. `reasoning_effort: high`) followed by user extras containing a second `--chat-template-kwargs`, a duplicate-flag hazard. PR #16327 merges them into a single flag. — [PR #16327](https://github.com/unslothai/unsloth/pull/16327)
- **llama-server `-ngl all` / `-ngl auto` rejected** — Studio returns HTTP 400 on values llama.cpp itself accepts. Closed issue; users passing custom launch args should verify behavior on their build. — [Issue #16494](https://github.com/unslothai/unsloth/issues/16494)
- **PyTorch extras missing from PyPI** — `cu130-2130` / `cu130-2140` extras are present in `pyproject.toml` but absent from the published `2026.10.11` wheel. Open request. — [Issue #16520](https://github.com/unslothai/unsloth/issues/16520)

## New Model & Hardware Support
- **LFM3 family (LiquidAI)** — feature request for LFM3 support closed; the 1.7B backbone is the ask. — [Issue #15588](https://github.com/unslothai/unsloth/issues/15588)
- **Vulkan on RX 5700 XT (Windows 10 22H2)** — open bug; Vulkan backend not usable on this configuration. Relevant if you're targeting consumer AMD on Windows. — [Issue #16395](https://github.com/unslothai/unsloth/issues/16395)
- **DGX Spark guidance** — open question on whether the published DGX Spark fine-tuning guide still applies post-September Studio update / generic Docker container. — [Issue #16342](https://github.com/unslothai/unsloth/issues/16342)

## Performance & Optimization
- **MLX load/reload latency (PR #16536)** — 4B model load was ~38s even with warm page cache; root cause is serialized Hub round-trips between server and worker plus pairing overhead. Aims for "ready to serve when loaded." — [PR #16536](https://github.com/unslothai/unsloth/pull/16536)
- **MLX attention memory estimate (PR #16517)** — estimate assumed every long prefill chunk materialized a full score tensor; moving to mlx's fused kernel drops the Qwen3.5-4B @ 262K estimate from ~38 GB (truncated in source). Depends on unsloth-zoo #2367. — [PR #16517](https://github.com/unslothai/unsloth/pull/16517)
- **Fused NF4 batch-1 decode (PR #16066)** — opt-in `UNSLOTH_DECODE_FUSED_NF4=1` widened from Llama to Mistral, Qwen2/Qwen2.5 (q/k/v bias handled in the fused qkv path), and Qwen3. Stacked on #16032. — [PR #16066](https://github.com/unslothai/unsloth/pull/16066)
- **cuDNN SDPA guard (PR #15934)** — keeps decode and NaN-dQ call shapes off cuDNN attention on torch < 2.14/2.15, ahead of upstream pytorch#193893 (silent wrong attention output) and #196678 (NaN query grads). — [PR #15934](https://github.com/unslothai/unsloth/pull/15934)
- **Parallel web-page reads for local GGUF models (PR #16489)** — three 3s page fetches previously serialized to ~10s; now overlapped, matching cloud-model behavior. — [PR #16489](https://github.com/unslothai/unsloth/pull/16489)

## Stability & Regressions
Ranked by severity:

1. **Prompt cache invalidated by dynamic MCP loading on hybrid/recurrent models — 17x slowdown** (open, no fix PR). Highest-impact item today for anyone serving hybrid architectures with MCP tools enabled. — [Issue #16538](https://github.com/unslothai/unsloth/issues/16538)
2. **Silent training-data corruption: S3 audio manifest-relative path collisions** (open). A `clips/a.wav` manifest-relative reference can resolve to a *different* file at the prefix root; transcript stays unchanged, so you get a wrong training pair with no error. Fix PR rejects ambiguous references. — [Issue #16528](https://github.com/unslothai/unsloth/issues/16528) / [PR #16529](https://github.com/unslothai/unsloth/pull/16529)
3. **`get_peft_model` crash on MoE + `offload_layers`** (open, fix PR). Evicted expert weights surface as empty tensors during PEFT adapter creation; PR exposes original shapes and restores offload state. — [PR #15159](https://github.com/unslothai/unsloth/pull/15159)
4. **Studio: load-during-generation semantics inconsistent** (open). Unload refuses with 409 unless `force_cancel_active`; load silently cancels running chats. Related misleading "GPU memory not released" error on Windows 11 / RTX 50-series (closed). — [Issue #16479](https://github.com/unslothai/unsloth/issues/16479), [Issue #16436](https://github.com/unslothai/unsloth/issues/16436)
5. **Pipeline-parallel fp16 + fused optimizers: GradScaler skip disagreement** (closed, superseded). Fused SGD/Adam/AdamW never call `unscale_`, so per-stage `found_inf` all-reduce never fires. — [PR #16351](https://github.com/unslothai/unsloth/pull/16351)
6. **MLX CI red since unsloth-zoo #2365** (closed, fixed). Stub missing `_FIRST_USE_WORK` import broke `test_mlx_inference_backend.py` on every PR. — [PR #16537](https://github.com/unslothai/unsloth/pull/16537)
7. **Over-long request triggers 19–55 GB PyTorch Clef download** (open PR). With Decision API on Auto, a request exceeding llama.cpp context fell back to PyTorch Clef without checking disk residency, evicting llama.cpp and leaving PyTorch serving subsequent requests. PR gates the fallback on prior download. — [PR #16509](https://github.com/unslothai/unsloth/pull/16509)
8. **Image-bearing chats lose background generation on tab close** (open PR). Text-only chats already behaved correctly. — [PR #16491](https://github.com/unslothai/unsloth/pull/16491)
9. **Settings persisted only in browser `localStorage`** (closed). Appearance/System/Security/Browser settings silently reset per origin/browser instead of hitting server-side storage. — [Issue #16309](https://github.com/unslothai/unsloth/issues/16309)

Lower severity, UI/UX only: sent-message indentation stripped ([PR #16495](https://github.com/unslothai/unsloth/pull/16495)), TTS reading emoji descriptions aloud ([Issue #16472](https://github.com/unslothai/unsloth/issues/16472)), title regeneration ignoring conversation language ([Issue #16411](https://github.com/unslothai/unsloth/issues/16411)), Accounting-format spreadsheet cells indexed as raw values ([PR #16001](https://github.com/unslothai/unsloth/pull/16001)).

## What This Means for Application Developers
- **If you serve hybrid/recurrent models (Mamba, Jamba, Qwen3-Next-class) with MCP tools enabled in Studio, expect severe cache-hit degradation** until #16538 is addressed. Measure TTFT before and after enabling MCP; this is a 17x-class regression, not a rounding error.
- **Training pipelines pulling audio from S3 should audit manifest paths now.** The collision in #16528 fails silently — no exception, no transcript mismatch, just bad pairs. Pin to PR #16529 or enforce absolute/unique prefixes until merged.
- **MoE fine-tuning with `offload_layers` is currently broken for PEFT adapter loading.** If you're training fused-expert MoEs on constrained VRAM, either wait for #15159 or avoid the combination; the failure mode is a crash at `get_peft_model`, which is at least loud.
- **Fused NF4 decode is expanding but remains opt-in** (`UNSLOTH_DECODE_FUSED_NF4=1`). Worth benchmarking on Mistral/Qwen2.5/Qwen3 batch-1 serving; Llama has had it longer, and the new families reuse the same attention path with bias handling added.
- **Torch extras lag on PyPI.** If your Docker builds install `unsloth[cu130-2130]` from the index, you're likely silently getting a different torch than intended — install torch explicitly and pin.
- **MLX on Apple silicon is the actively optimized path right now.** #16536 and #16517 together target the two biggest complaints (cold-load latency, memory estimate pessimism); retest capacity planning for long-context (256K) workloads after they land, as the estimate was previously several GB conservative.

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar).*