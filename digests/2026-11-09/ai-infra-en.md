# AI Infrastructure Digest 2026-11-09

> Generated: 2026-11-08 23:13 UTC | Projects covered: 6

- [vLLM](https://github.com/vllm-project/vllm)
- [SGLang](https://github.com/sgl-project/sglang)
- [llama.cpp](https://github.com/ggml-org/llama.cpp)
- [Ollama](https://github.com/ollama/ollama)
- [LiteLLM](https://github.com/BerriAI/litellm)
- [Unsloth](https://github.com/unslothai/unsloth)

---

## Cross-Project Comparison

# Cross-Project Comparison Report — 2026-11-09

## 1. Ecosystem Overview

Today's digests show a mature AI infrastructure stack converging on three fronts simultaneously: **Blackwell/SM120 consumer-GPU enablement** is still rocky across every serving engine (vLLM and SGLang both have open decode/prefill regressions on RTX PRO 6000 / GB10 hardware), **quantized MoE serving** has become the dominant correctness surface (SGLang reports two silent-garbage router-loading bugs; vLLM has an SM120+NVFP4+MTP illegal-memory-access regression), and **AMD/ROCm is receiving serious investment** (vLLM's persistent_topk and gfx942 FP8 MoE configs, SGLang's gfx1250 UALink and AITER FlyDSL paths). Meanwhile the gateway layer is absorbing more production responsibility — LiteLLM's queue shows spend-accounting and protocol-translation bugs mattering as much as routing throughput. Local runtimes (llama.cpp, Ollama) are converging on the same themes from the other direction: speculative-decoding correctness, MoE partial offload, and hardware-diversity backends (RISC-V, Rockchip NPU, SYCL/Vulkan).

## 2. Activity Comparison

| Project | Issues (24h) | PRs (24h) | Release Status | Notable Signal |
|---|---|---|---|---|
| **vLLM** | High (10 ranked regressions + active RFCs) | Heavy in-flight (Kimi K3.1 TP8 series, Marlin→Humming) | **No release in 24h**; v0.33.0 carries known SM120 regression | Largest structural optimization proposal in flight (Humming kernel migration) |
| **SGLang** | High (56-ticket-style backlog implied; 10 ranked) | Active (unified-memory refactor stack, Rust server follow-ups) | **No release in 24h**; v0.5.23 has GLM-5.3-Flash corruption | Highest-severity correctness bugs (silent MoE router failures) |
| **llama.cpp** | Moderate (10 ranked) | Heavy merge day (b12202–b12211 landed; new `clip.h` removal) | **~10 builds landed** (b12199–b12211) | Most releases of any project; deterministic Top-K is a reproducibility win |
| **Ollama** | Moderate (9 ranked) | Active (Rockchip NPU, FP8 import, VRAM prediction) | **No release in 24h** | Per-model `num_parallel` finally implemented (#9546) |
| **LiteLLM** | Very high (56 issues updated) | Very high (304 PRs touched) | **Dev tag v1.110.0-dev.2** (cosign-signed images) | Highest churn; billing correctness bugs dominate |
| **Unsloth** | Moderate (bug reports) | Very high (large same-day CLOSED batch + open follow-ups) | **No release in 24h** | Diffusion/Studio path is the active frontier |

**Reading the table:** LiteLLM's issue/PR volume is an order of magnitude above the serving engines — partly gateway-surface breadth, partly the cost of owning billing semantics. llama.cpp is the only project shipping continuous builds, which is consistent with its role as the low-level reference runtime.

## 3. Model Support Race

| Model / Architecture | vLLM | SGLang | llama.cpp | Ollama | Unsloth |
|---|---|---|---|---|---|
| **Kimi K3.1 (TP8)** | ✅ Split 3/5 in flight | — | — | — | — |
| **MiniMax M3 / M3.1** | ✅ Attention replication flag + NVFP4 index shard | — | — | — | — |
| **Nemotron 3.5 ASR streaming** | 🟡 FR + persistent-state PR | — | — | — | — |
| **Qwen3.5/3.8 (GDN hybrid)** | 🟡 MTP drafter fix | ✅ AITER causal conv1d prefill | ✅ SSM `d_state=64` fast path | — | ✅ Qwen-Image CFG fix |
| **DeepSeek-V4.1** | — | 🟡 8× prefill regression on SM120 | ✅ RPC load 5× faster (open) | — | — |
| **Mixtral / Llama 4 / Qwen3-MoE (FP8 routers)** | — | 🔴 Silent router bug | — | — | — |
| **GLM-5.3-Flash / GLM-V** | — | 🔴 v0.5.23 engine corruption | — | — | — |
| **FLUX.3 / HiDream / image-gen** | — | ✅ HiDream-O1-Image (in progress) | — | ❌ Unsupported on macOS | ✅ fp8/fp16/Turing paths |
| **InternLM3 / TeleChat2/3** | ✅ HF Transformers backend migration | — | — | — | — |
| **Rockchip RK3588/RK3576** | — | — | — | ✅ NPU prefill backend | — |

**Who is ahead:** vLLM leads on *breadth of new frontier-model support* (Kimi, MiniMax, Nemotron) and is standardizing on the HF Transformers backend for long-tail architectures — a strategic bet that reduces per-model maintenance. SGLang is ahead on *AMD-specific model paths* (AITER for Qwen GDN on gfx950) and diffusion. llama.cpp owns the *architecture-generic* wins (SSM scan, MXFP4 RISC-V repack). Ollama is the only project pushing to *non-GPU accelerators* (Rockchip NPU) — a distinct edge-deployment bet.

## 4. Performance Frontier

Optimization effort is concentrated in four clusters, with uneven distribution:

**KV cache — the hottest frontier.** vLLM has the most aggressive agenda: a proposed **3-bit Octave KV cache for AMD** (#59773), plus caching K/V view derivation in Triton/eager backends. llama.cpp has fused q8_0/q4_0 KV dequant inside CUDA `fattn-mma-f16` in progress (#31802) and a sticky-NaN fix for recurrent state (#31913). SGLang is attacking redundant prefill KV via same-batch prefix sharing (#48963). Ollama's contribution is *forecasting* — accurate hybrid/quantized-KV VRAM prediction (#19609).

**Quantization — where correctness risk lives.** SGLang's silent MoE router bugs (#49185, #49198) are today's most severe issue precisely because quantized routers fail without error. vLLM's Marlin→Humming migration (#65560) is the largest structural kernel change in flight, consolidating INT4/FP8/MXFP*/NVFP4 paths across NVIDIA architectures. Unsloth's INT8-fused MLP work (#17114) and Turing INT8 enablement (#17167) extend quantized training/inference to older hardware.

**Kernels & backends — AMD/Blackwell are the targets.** vLLM: ROCm persistent_topk (#63847), gfx942 FP8 MoE (+12–25% throughput, #63727). SGLang: FlyDSL causal conv1d, gfx1250 UALink all-reduce, FA3 kernel-selection tuning (1.5–3% gap). llama.cpp: SYCL Battlemage decode path, OpenCL q8_0 GEMV for speculative verification.

**Distributed serving & scheduling.** vLLM's MiniMax attention-replication flag is a pragmatic cross-node TP lever; SGLang's unified-memory/HiCache refactor (6 PRs) and Q4 P/D disaggregation roadmap are the most structured effort; Ollama's scheduler VRAM-overcommit fix (#19581) is the local-runtime analogue.

**Notable absence:** batching-level innovation is sparse today — most gains are kernel- and memory-layout-level, not scheduler-level (SGLang's `max_running_requests`/`cuda_graph_max_bs` interaction remains an absorbing-state tuning trap, #33483).

## 5. Layer Positioning

| Layer | Project(s) | Primary Value | Today's Focus |
|---|---|---|---|
| **Serving engine** (datacenter) | vLLM, SGLang | Throughput, frontier-model day-one support, distributed serving | Kimi/MiniMax support, Blackwell fixes, AMD kernels, silent-correctness bugs |
| **Local/edge runtime** | llama.cpp, Ollama | Portability, hardware diversity, single-binary/desktop UX | Deterministic sampling, SSM offload, Rockchip NPU, per-model parallelism |
| **LLM gateway** | LiteLLM | Routing, budgets, protocol translation, observability | Spend-accounting correctness, `/v1/messages` fidelity, hot-path Python elimination |
| **Training / fine-tuning** | Unsloth | Memory-efficient training, diffusion Studio, quantization-aware fine-tuning | Diffusion pipeline fixes, INT8/Turing, thread-safe concurrent loads |

The layers are **converging on overlapping responsibilities**: vLLM and SGLang now both implement OpenAI-ish API surfaces and gateway-like routing (P/D disaggregation, fallback); LiteLLM is moving hot paths into Rust to survive 1,000+ deployments; Ollama is absorbing runtime concerns (VRAM prediction, scheduler) that used to be implicit. Expect continued pressure on the gateway layer's traditional role as litellm pushes perf-critical logic into Rust and serving engines expose richer native APIs.

## 6. Trend Signals

**1. Silent correctness failures are the defining risk of quantized MoE.** SGLang's router bugs (#49185, #49198) serve garbage with no error; vLLM's SM120/NVFP4/MTP regression (#65139) and llama.cpp's spec-decode divergence on quantized targets (#25618) are the same class. **Action for developers:** build logit-parity or golden-output tests against a reference checkpoint before any quantized-MoE upgrade — liveness checks won't catch these.

**2. Budget enforcement cannot be trusted at the gateway layer alone.** LiteLLM's #50124/#50125/#50528 trio (missing usage → uncharged; over-budget admitted serially; disconnect leaves reservations) means `max_budget` is best-effort. **Action:** pair gateway budgets with upstream provider quotas and client-side total deadlines (`x-litellm-timeout` bounds per-attempt, not wall clock, #49751).

**3. Speculative decoding is a reproducibility hazard, not just a perf knob.** Two top-3 llama.cpp issues are spec-decode correctness/abort (#30860/#31914, #25618). **Action:** if you run eval harnesses, agent replay, or regression suites, either use bf16 targets or disable draft-MTP on quantized models and pin a build.

**4. Blackwell consumer GPUs (SM120) are not production-ready across engines.** vLLM decode collapse (#56892) and SGLang's 8× DSV4.1 prefill regression (#49173, from an over-strict `is_sm(10)` gate) both point to immature SM120 paths. **Action:** RTX 50xx / RTX PRO 6000 / GB10 deployments should validate against SM100-class reference numbers and track the fixes, not assume feature parity.

**5. AMD is becoming a first-class serving target — with model-specific tuning.** Octave 3-bit KV, gfx942 FP8 MoE configs, gfx1250 UALink, AITER GDN kernels. **Action:** if MI300X/MI355 or RDNA is in your roadmap, budget for engine-specific kernel enablement; don't assume CUDA-tuned configs transfer.

**6. Streaming/agent infrastructure is the next battleground.** vLLM's scheduler-owned persistent state (#65544) unlocks stateful ASR/voice agents; SGLang's cacheable-agent-prefix RFC (#47763) and same-batch KV sharing (#48963) target agent-loop TTFT; llama.cpp's UTF-8 tool-call sanitization (b12199) and grammar-ordering PRs address the most common agent failure mode. **Action:** agent harness builders should track these three threads — they directly affect TTFT, tool-call reliability, and multi-turn cost.

**7. Graceful operations are still under-served.** vLLM's `--shutdown-timeout` bug (#61562) means rolling restarts on DP>1 deployments drop in-flight requests with 500s; Ollama's runner-unload failure can overcommit VRAM (#19577). **Action:** for production rolling upgrades, validate drain behavior explicitly and expect 500s until the vLLM fix lands.

**8. Diffusion/image-gen is the emerging workload class in fine-tuning stacks.** Unsloth's same-day CLOSED batch is overwhelmingly diffusion (CFG pairing, NVFP4 decode, VAE tiling, device targeting), with open fp8-on-L4 and thread-safety regressions. **Action:** if Studio-driven image/video pipelines are in scope, pin commits and avoid concurrent diffusion model loads until #17171 lands.

---

## Per-Project Reports

<details>
<summary><strong>vLLM</strong> — <a href="https://github.com/vllm-project/vllm">vllm-project/vllm</a></summary>

# vLLM Digest — 2026-11-09

## 1. Today's Highlights

No new releases landed in the last 24h, but the issue/PR queue shows heavy activity around **Kimi K3.1 serving** (KV cache/attention split PRs) and a cluster of **SM120/Blackwell consumer-GPU problems** with CUDA graph capture and MTP speculative decoding. On the optimization side, the largest structural change in flight is the proposed **Marlin → Humming kernel migration** across NVIDIA architectures, alongside a new **3-bit Octave KV cache** RFC for AMD GPUs.

## 2. Releases & Breaking Changes

No new releases in the last 24h. Notable in-flight changes with compatibility implications:

- **RFC: Remove Group Activation Ordering (`actorder=group`)** — proposes dropping runtime actorder support from vLLM, compressed-tensors and llm-compressor, affecting GPTQ W4A16/W8A16 models quantized with `actorder=group`. Closed/stale, but migration-relevant for anyone with such checkpoints. [#48148](https://github.com/vllm-project/vllm/issues/48148)
- **`llguidance` is an unconditional dependency** in `vllm==0.33.0` and is imported by `from vllm import LLM`, which blocks install/import on glibc < 2.31. [#66345](https://github.com/vllm-project/vllm/issues/66345)

## 3. New Model & Hardware Support

- **Kimi K3.1 @ TP8** — split 3/5 of the K3.1 serving series adds the KV cache and attention changes needed to serve K3.1 at tensor-parallel 8 (splits 1/5 and 2/5 already merged). [#66037](https://github.com/vllm-project/vllm/pull/66037)
- **Nemotron 3.5 ASR Streaming (0.6B)** — feature request to support NVIDIA's streaming multilingual ASR model; a companion PR adds scheduler-owned persistent state for streaming models whose session state is fixed-size and non-recomputable. [#47455](https://github.com/vllm-project/vllm/issues/47455), [#65544](https://github.com/vllm-project/vllm/pull/65544), RFC [#65545](https://github.com/vllm-project/vllm/issues/65545)
- **MiniMax M3** — experimental `VLLM_MINIMAX_M3_REPLICATE_ATTN=1` replicates attention across TP ranks (skipping the attention all-reduce, MoE stays TP) for slow inter-node TP; separate PR shards MiniMax M3.1's NVFP4 index cache with indexer CP. [#66378](https://github.com/vllm-project/vllm/pull/66378), [#64631](https://github.com/vllm-project/vllm/pull/64631)
- **Transformers backend migration** — InternLM3, IQuestCoder, TeleChat2/2.5 and TeleChat3 moved off vLLM's Llama implementation onto the HF Transformers modeling backend (adds TeleChat2-115B support). [#66416](https://github.com/vllm-project/vllm/pull/66416)
- **ROCm: Mamba2 ReplaySSM + speculative decoding** on the Triton backend (previously FlashInfer/CUDA-only), with an MI355 mirror of the B200 nightly eval. [#66318](https://github.com/vllm-project/vllm/pull/66318)

## 4. Performance & Optimization

- **RFC: Replace Marlin with Humming across NVIDIA architectures** — Humming now covers Marlin's formats (INT4/INT8 GPTQ/AWQ, FP8, MXFP8, MXFP4, NVFP4, MXFP6) for dense and MoE layers; Marlin is SM80-era with no Hopper/Blackwell-specific paths. [#65560](https://github.com/vllm-project/vllm/issues/65560)
- **ROCm `persistent_topk`** port for sampling (FilteredTopK + sampled_topk tiers, wave64-safe HIP macros); gfx950 runs every batch through the large-LDS path. [#63847](https://github.com/vllm-project/vllm/pull/63847)
- **gfx942 FP8 W8A8 fused-MoE configs** for E=32, N=4096 (Muse Spark at TP8+EP): **+12–25% serving throughput at 64 concurrent requests** on MI300X, GSM8K unchanged. [#63727](https://github.com/vllm-project/vllm/pull/63727)
- **RFC: Octave KV, native 3-bit KV cache for AMD GPUs** — motivated by long-prompt Qwen3.8-27B W4A16 serving on 4× RX 7900 XTX. [#59773](https://github.com/vllm-project/vllm/issues/59773), PR [#59774](https://github.com/vllm-project/vllm/pull/59774)
- **Triton/eager attention backends re-derive K/V cache views on every call** — same class of overhead as the FlashAttention fix in #59534; caching the views is proposed. [#64572](https://github.com/vllm-project/vllm/issues/64572)
- **Speculative decoding: zero-bubble draft-token handoff** for structured outputs when `step_with_batch_queue` is used. [#64531](https://github.com/vllm-project/vllm/pull/64531)
- **RFC: ViT full CUDA Graph** — tracker for capturing the whole ViT forward (Qwen3-VL, GLM-V, Kimi K2.5) to cut launch overhead in multimodal serving. [#38175](https://github.com/vllm-project/vllm/issues/38175)

## 5. Stability & Regressions

Ranked by severity:

1. **SM120 decode throughput collapse / CUDA graphs unusable** — DeepSeek-V4.1-Flash on 8× RTX PRO 6000 Blackwell with `--enforce-eager` gives extremely low decode throughput; graph capture fix requested with priority. [#56892](https://github.com/vllm-project/vllm/issues/56892) (see also the working-config field report: [#56700](https://github.com/vllm-project/vllm/issues/56700))
2. **GDN decode degenerates to a single repeated token** — Qwen3-Next on ROCm (gfx906/MI50) with fp32 SSM cache once context grows; also reported as CUBLAS internal error / illegal memory access with prefix caching on GB10 (sm_121). [#54308](https://github.com/vllm-project/vllm/issues/54308), [#54173](https://github.com/vllm-project/vllm/issues/54173)
3. **Regression in v0.33.0: b12x attention illegal memory access** at the first MTP verify step (cold-JIT `PagedForwardKernel`), SM120 + NVFP4 MoE + fp8 KV + chunked prefill + prefix caching; worked in v0.32.0. [#65139](https://github.com/vllm-project/vllm/issues/65139)
4. **Multimodal encoder CUDA graph crash** with `enable_mm_embeds=True` + `cudagraph_mm_encoder=True` and precomputed embeddings — **fix PR open**. [#65531](https://github.com/vllm-project/vllm/issues/65531), [#65535](https://github.com/vllm-project/vllm/pull/65535)
5. **Compiled multi-module MTP drafters always run module 0** when a checkpoint has multiple MTP modules (Qwen3.5/Nex fix extended to remaining drafters) — **fix PR open**. [#66428](https://github.com/vllm-project/vllm/pull/66428)
6. **`--shutdown-timeout` never drains with `--api-server-count > 1`** — API servers stopped before engines, in-flight requests fail with `EngineDeadError` (HTTP 500); **fix PR open**. [#61562](https://github.com/vllm-project/vllm/pull/61562)
7. **KV-pool-saturated admission retries inflate decode TPOT ~2.3×** (V1 scheduler, Ascend P/D deployment) — request for automatic admission backoff. [#64460](https://github.com/vllm-project/vllm/issues/64460)
8. **UVA `--cpu-offload-gb` does not reduce peak device memory** for compressed-tensors WNA16 MoE on XPU. [#57008](https://github.com/vllm-project/vllm/issues/57008)
9. **Anthropic Messages API: `x-api-key` header unsupported** — only `Authorization: Bearer` works. [#51572](https://github.com/vllm-project/vllm/issues/51572)
10. **Whisper long-audio timestamps drift** (~0.5s per segment for audio >30s) — closed. [#32588](https://github.com/vllm-project/vllm/issues/32588)

## 6. What This Means for Application Developers

- **Pin your vLLM version deliberately.** v0.33.0 carries a real decoding regression on SM120 with MTP + prefix caching (#65139) and ships `llguidance` as an unconditional dependency that breaks import on older glibc (#66345). If you're on consumer Blackwell or RHEL/older base images, validate before upgrading.
- **Kimi K3.1 and MiniMax M3 are becoming first-class.** If you serve these families, watch the K3.1 TP8 series and the MiniMax attention-replication flag — the latter is a pragmatic lever for cross-node TP deployments where attention all-reduce dominates latency.
- **AMD users get meaningful wins soon** — persistent_topk sampling, tuned gfx942 FP8 MoE configs (+12–25% throughput), and a proposed 3-bit KV cache could materially cut cost per token for long-context workloads.
- **Streaming/ASR apps** should track the scheduler-owned persistent-state PR (#65544); it unlocks stateful streaming models that can't recompute session state from the prompt — relevant for real-time transcription and voice agents.
- **Operational hygiene:** the `--shutdown-timeout` multi-API-server bug means graceful drains silently fail on DP > 1 deployments today; until #61562 lands, expect 500s during rolling restarts.

</details>

<details>
<summary><strong>SGLang</strong> — <a href="https://github.com/sgl-project/sglang">sgl-project/sglang</a></summary>

# SGLang Digest — 2026-11-09

## 1. Today's Highlights

No new releases landed in the last 24 hours, but the issue tracker was busy: a cluster of **quantized router-loading bugs** in MoE models (compressed-tensors, FP8) was reported where the server silently serves garbage instead of erroring, and a **prefill CUDA-graph logprob divergence** bug was filed against Qwen3.8-NVFP4. On the performance side, the unified-memory / HiCache refactor stack is under active review, a Rust server follow-up tracker is open, and the Q4 Prefill-Decode Disaggregation roadmap is still live. Meanwhile, an 8× prefill regression on GB10 (SM120/121) was opened against the DSV4.1 DeepGEMM indexer gating change from #48399.

## 2. Releases & Breaking Changes

No new versions in the last 24h. Notable pending behavior-change items still under review:

- **`--boundary-reduction` default change for Qwen4-Exp** — PR #49203 proposes defaulting to reduce-scatter under DP attention instead of `ar`, changing the resolved path for `Qwen4ExpForConditionalGeneration` and `Qwen4ExpForCausalLMMTP`. This alters FFN exit behavior for existing Qwen4-Exp deployments ([PR #49203](https://github.com/sgl-project/sglang/pull/49203)).
- **Config-structure refactor pending**: `max_running_requests` (4096) and `cuda_graph_max_bs` (32) defaults are derived independently, and crossing the graph ceiling is described as an **absorbing state** — closed as inactive, but worth noting for tuning guidance ([Issue #33483](https://github.com/sgl-project/sglang/issues/33483)).

## 3. New Model & Hardware Support

- **SM12x (RTX 50xx / RTX PRO Blackwell)**: Feature request to make **FlashInfer cuTile NVFP4 MoE** the default MoE runner on SM12x ([Issue #49123](https://github.com/sgl-project/sglang/issues/49123)).
- **AMD gfx1250 / UALink**: carv2 custom all-reduce extended across gfx1250 UALink nodes ([PR #49003](https://github.com/sgl-project/sglang/pull/49003)).
- **AMD AITER FlyDSL causal conv1d prefill** for Qwen3.5/Qwen3.8 GDN layers on gfx950 (opt-in, ~1.1× over Triton path) ([PR #41879](https://github.com/sgl-project/sglang/pull/41879)).
- **AITER upgrade readiness tracker** remains open, tracking version-by-version feature dependencies ([Issue #21302](https://github.com/sgl-project/sglang/issues/21302)).
- **Diffusion / NPU**: HiDream-O1-Image support (DEV, DEV-2604, FULL TI2I) in progress ([PR #45137](https://github.com/sgl-project/sglang/pull/45137)).

## 4. Performance & Optimization

- **GB10 / SM120–121 DSV4.1 prefill regression (8×)**: #48399 replaced `is_sm100_or_newer()` with strict `current_arch().is_sm(10)`, dropping SM120/121 from the DeepGEMM indexer gates and forcing a torch fallback with per-layer `tolist()` calls ([Issue #49173](https://github.com/sgl-project/sglang/issues/49173)).
- **DeepSeek-V4 dsv4 prefill on SM120** still underperforming: ~2–7K tok/s vs ~12.5K reported via vLLM/marlin on identical 4× RTX PRO 6000 hardware — tuning guidance requested ([Issue #33422](https://github.com/sgl-project/sglang/issues/33422)).
- **FA3 kernel selection**: `sglang-kernel` FA3 picks `MmaPV_is_RS=false` for hdim 128 non-causal on H200, 1.5–3% slower than flash-attn's Hopper build ([Issue #47734](https://github.com/sgl-project/sglang/issues/47734)).
- **Prefill batch KV sharing**: proposal to share a prompt's KV among requests admitted in the same prefill batch, avoiding redundant full-prompt compute for identical prompts ([Issue #48963](https://github.com/sgl-project/sglang/issues/48963)).
- **Unified memory refactor stack** (6 PRs, #48009–#48014): per-window host pools/PD entries, single layout-search admission, and multi-window-sub-pool support in the Triton attention backend — targeted at `--enable-unified-memory` and HiCache paths ([PR #48014](https://github.com/sgl-project/sglang/pull/48014), [PR #48012](https://github.com/sgl-project/sglang/pull/48012)).
- **Rust server stack follow-ups** tracked for non-blocking perf items ([Issue #49055](https://github.com/sgl-project/sglang/issues/49055)).
- **Unified FP32 activation kernels** in `sgl_kernel/activation.cuh` with `kFastMath` switch and `silu_fast` for fused quant ([PR #48942](https://github.com/sgl-project/sglang/pull/48942)).

## 5. Stability & Regressions

Ranked by severity:

1. **Silent correctness failures on quantized MoE routers** — highest severity. A router stored quantized (compressed-tensors) is never loaded and the server answers garbage without error ([Issue #49185](https://github.com/sgl-project/sglang/issues/49185)); same pattern for FP8-stored routers in Mixtral, Llama 4, and FP8 Qwen3-MoE where the scale is dropped ([Issue #49198](https://github.com/sgl-project/sglang/issues/49198)).
2. **GLM-5.3-Flash engine corruption after a 1M-token request** on v0.5.23 (MTP accept rate 0.00, all subsequent requests degenerate); v0.5.22 is clean ([Issue #48964](https://github.com/sgl-project/sglang/issues/48964)). Tracked in the GLM-5.3-Flash bug tracker ([Issue #37524](https://github.com/sgl-project/sglang/issues/37524)).
3. **Prefill CUDA-graph numerical divergence**: short prefills padded to a capture bucket produce different logprobs than eager execution (Qwen3.8-27B-NVFP4, hybrid GDN, v0.5.23) ([Issue #49133](https://github.com/sgl-project/sglang/issues/49133)).
4. **Full prefill CUDA-graph capture crashes** for hybrid linear-attention models (GDN/KDA) — `IndexError` in `HybridLinearAttnBackend._replay_metadata` ([Issue #48593](https://github.com/sgl-project/sglang/issues/48593)).
5. **GB10 unified-memory exhaustion at >40k-token prefill** — worker rank silently killed with no traceback or OOM record; cross-stack control passes at 54k ([Issue #36941](https://github.com/sgl-project/sglang/issues/36941)).
6. **LoRA `use_rslora=True` served with wrong scale** ([Issue #40835](https://github.com/sgl-project/sglang/issues/40835)).
7. **CLOSED / inactive**: DeepSeek-V4 BF16 checkpoint routed to MXFP4 expert path ([Issue #44810](https://github.com/sgl-project/sglang/issues/44810)); HiCache host-tier load-back corrupting GLM-5.3-Flash generation ([Issue #38031](https://github.com/sgl-project/sglang/issues/38031)); GLM-V stray multimodal placeholder 500s ([Issue #37579](https://github.com/sgl-project/sglang/issues/37579)).

CI hygiene PR is in flight: scheduler stub needs a `PauseState` after #49144 ([PR #49178](https://github.com/sgl-project/sglang/pull/49178), closed). Several CI-flake fixes from hnyls2002 are open — Mooncake KV registration startup failure ([PR #49156](https://github.com/sgl-project/sglang/pull/49156)), Gemma4 AR fusion test deadlock ([PR #49160](https://github.com/sgl-project/sglang/pull/49160)), and diffusion Hub-API 429 fallback to cached snapshot ([PR #49155](https://github.com/sgl-project/sglang/pull/49155)).

## 6. What This Means for Application Developers

- **Audit any quantized MoE deployment before trusting outputs.** The router-loading bugs (#49185, #49198) fail silently — no error, just bad generations. If you run Mixtral, Llama 4, Qwen3-MoE, or compressed-tensors/FP8 checkpoints, validate logits against a known-good reference before upgrading.
- **Pin GLM-5.3-Flash to v0.5.22** if you serve long-context or 1M-token requests; v0.5.23 has a reproducible engine-state corruption path (#48964).
- **Disable or validate full prefill CUDA graphs** for hybrid linear-attention / GDN/KDA models and for NVFP4 checkpoints where bit-exact logprob parity matters (#48593, #49133).
- **GB10 / DGX Spark users**: cap prefill length below ~40k tokens or monitor host/unified memory manually — the OOM path kills the rank without diagnostics (#36941), and the DSV4.1 indexer regression (#49173) means dsv4 prefill on SM120 is currently 8× slower than it should be.
- **Agent harness builders**: watch RFC #47763 on keeping multi-turn agent prefixes cacheable, and #48963 on same-batch prefix KV sharing — both directly target agent-loop TTFT and redundant prefill cost ([Issue #47763](https://github.com/sgl-project/sglang/issues/47763)).

</details>

<details>
<summary><strong>llama.cpp</strong> — <a href="https://github.com/ggml-org/llama.cpp">ggml-org/llama.cpp</a></summary>

# llama.cpp Digest — 2026-11-09

## 1. Today's Highlights

A heavy day of correctness-neutral-ish infrastructure work: the radix Top-K kernels for CUDA/Metal were made deterministic for tied selections (closing a subtle source of non-reproducible sampling), and the mtmd multimodal stack was consolidated (removing `clip.h`). On the performance side, a CUDA fast path for `d_state=64` SSM scans landed (unblocking GPU-side recurrent scans for Zamba2-class architectures that previously fell back to CPU), and the `moe-cache` path got two important fixes: it no longer stalls the CPU on cached experts, and speculative decoding no longer applies the MoE cache to the draft model. The open issue queue remains dominated by speculative-decoding (`draft-mtp`) correctness/abort reports and Vulkan/SYCL backend corruption on Intel hardware.

## 2. Releases & Breaking Changes

No breaking API changes. Notable build/behavioral changes in this window:

- **b12211 — mtmd refactor**: `clip.h` removed; clip code consolidated into `mtmd`. Third-party consumers including `clip.h` directly (out-of-tree mmproj tooling) must migrate. ([PR #31865](https://github.com/ggml-org/llama.cpp/pull/31865))
- **b12208 — deterministic radix Top-K**: CUDA/Metal `TOP_K` now resolves ties by column order (block → warp → step → lane; earliest tie wins). Sampling with `top_k` may produce slightly different (but stable) outputs vs. prior builds. ([PR #31084](https://github.com/ggml-org/llama.cpp/pull/31084))
- **b12202 — precision alignment**: `ggml_prec_set_acc` calls now follow the reference implementation's precision; GLM4 down/output projections move to `GGML_PREC_BF16` where the reference does not keep F32. Expect minor numeric shifts on GLM4-family models. ([PR #31851](https://github.com/ggml-org/llama.cpp/pull/31851))
- **b12199 — PEG tool-call sanitization**: invalid UTF-8 in PEG tool-call arguments is now sanitized in `common`; downstream parsers no longer receive malformed byte sequences. ([PR #31861](https://github.com/ggml-org/llama.cpp/pull/31861))

## 3. New Model & Hardware Support

- **CUDA SSM scan `d_state=64` fast path (b12207)**: adds the missing fast path for state size 64 in the `SSM_SCAN` kernel. Zamba2-style recurrent architectures previously fell back to CPU on every scan step; now handled on-GPU. ([PR #31818](https://github.com/ggml-org/llama.cpp/pull/31818))
- **SpacemiT IME2 MXFP4 dense repack (b12210)**: CPU-side MXFP4 repacking for RISC-V SpacemiT IME2 accelerators. ([PR #31776](https://github.com/ggml-org/llama.cpp/pull/31776))
- **llamafile bf16 + q8_0 VXE (b12209)**: new vector-extension paths for bf16 and q8_0 in the llamafile runtime. ([PR #31866](https://github.com/ggml-org/llama.cpp/pull/31866))
- **OpenCL q8_0 GEMV + small-n MoE verification**: new `kernel_gemv_noshuffle_q8_0_f32_mc` and small-n MoE GEMV paths improve speculative-decode verification on OpenCL (Qualcomm co-authored). ([PR #31831](https://github.com/ggml-org/llama.cpp/pull/31831))

**In progress:**
- [PR #31802](https://github.com/ggml-org/llama.cpp/pull/31802) — fused q8_0/q4_0 KV dequant inside CUDA `fattn-mma-f16` kernels (eliminates the f16 scratch materialization pass for quantized KV cache).
- [PR #30606](https://github.com/ggml-org/llama.cpp/pull/30606) — SYCL: route Battlemage (BMG) D=256 single-query decode to the FA vector kernel; reported 832 µs vs 1150 µs per call at kv=65536, GQA4 on Arc Pro B70.
- [PR #31896](https://github.com/ggml-org/llama.cpp/pull/31896) — `MUL_MAT` src1 precision tests plus backend fallback-to-CPU gating for BF16/F32 precision requests (Metal already rejects unsupported cases).

## 4. Performance & Optimization

- **MoE cache CPU decoupling (b12206)**: the CPU portion no longer blocks waiting on cached experts, reducing per-token stalls for CPU+GPU hybrid MoE inference. ([PR #31888](https://github.com/ggml-org/llama.cpp/pull/31888))
- **SSM scan GPU offload (b12207)**: see above; eliminates CPU fallback for `d_state=64` models, which was the dominant bottleneck in generation for those architectures. ([PR #31818](https://github.com/ggml-org/llama.cpp/pull/31818))
- **CUDA/Metal Top-K determinism (b12208)**: not a speedup, but removes the prior "shift-skip" logic and thread-timing dependence, making kernel behavior reproducible across runs. ([PR #31084](https://github.com/ggml-org/llama.cpp/pull/31084))
- **RPC load-time improvement (open, [PR #29413](https://github.com/ggml-org/llama.cpp/pull/29413))**: ~5× faster `load_tensors` on 2× DGX Spark over RDMA for DeepSeek-V4-Flash (145.6 GiB).
- **IQ2 quant creation speedup (open, [PR #31910](https://github.com/ggml-org/llama.cpp/pull/31910))**: pattern caching for IQ2 creation; author reports byte-identical output across IQ2 types on 8 models / 5 architectures.
- **Persistent disk prompt cache (open, [PR #28092](https://github.com/ggml-org/llama.cpp/pull/28092))**: `--cache-disk` with hybrid/SWA checkpoint support, restart survival, corruption cleanup, and LRU disk-bound eviction.

## 5. Stability & Regressions

Ranked by severity:

1. **`draft-mtp` kpool assertion on `qwen4exp`** — server aborts with `GGML_ASSERT(buffer)` in `llm_graph_input_kpool::set_input` at load time. Actively re-reported ([#30860](https://github.com/ggml-org/llama.cpp/issues/30860), [#31914](https://github.com/ggml-org/llama.cpp/issues/31914)); a proposed fix exists in #30860 and reportedly applies cleanly to master.
2. **Speculative decoding output divergence on quantized targets** ([#25618](https://github.com/ggml-org/llama.cpp/issues/25618)) — under greedy sampling, draft-MTP/draft-dspark output diverges from non-spec runs when the target is quantized (matches on bf16). 39 comments; open and unresolved — relevant if you rely on reproducibility.
3. **Embedding request leak / silent server hang** ([#31704](https://github.com/ggml-org/llama.cpp/issues/31704)) — a cancelled long embedding is never released; the server then serves nothing while `/health` reports OK. No fix PR listed.
4. **Vulkan corruption on Intel Arc B-series under long-context/RAG** ([#31828](https://github.com/ggml-org/llama.cpp/issues/31828)) — persists despite `GGML_VK_DISABLE_COOPMAT`; open.
5. **Vulkan 78% decode cliff at 131k context on RX 7900 XTX** ([#27734](https://github.com/ggml-org/llama.cpp/issues/27734), closed/stale) — suballocation fragmentation; workaround `GGML_VK_SUBALLOCATION_BLOCK_SIZE=4 GiB`.
6. **SYCL AOT garbage output for models >4 GB** ([#31685](https://github.com/ggml-org/llama.cpp/issues/31685)) — `GGML_SYCL_DEVICE_ARCH=arl-h` builds emit `GGGG…`; mitigated by a buffer-size flag.
7. **Sticky NaN in recurrent state** (fix PR open, [#31913](https://github.com/ggml-org/llama.cpp/pull/31913)) — reset logic multiplied by 0 instead of writing 0, letting a single NaN persist forever; hit Qwen3.8-flash-next over RPC with q8 KV cache.
8. **Streaming responses close the TCP connection despite `Keep-Alive`** ([#29680](https://github.com/ggml-org/llama.cpp/issues/29680), [#31863](https://github.com/ggml-org/llama.cpp/issues/31863), closed) — breaks pooled HTTP clients. If you use keep-alive pools against llama-server, verify against your build.
9. **MoltenVK out-of-bounds shader writes** (fix PR open, [#31890](https://github.com/ggml-org/llama.cpp/pull/31890)) — sentinel mismatches in `test-backend-ops` on macOS Vulkan.
10. **SYCL `-sm tensor` suspected leak** ([#27845](https://github.com/ggml-org/llama.cpp/issues/27845), closed/stale) — dual Intel B70, reported during generation.

## 6. What This Means for Application Developers

- **Speculative decoding is still the riskiest knob.** If you depend on deterministic outputs (eval harnesses, regression tests, agent replay), either run bf16 targets, disable draft-MTP/dspark on quantized targets, or pin a known-good build. Two of today's top issues are spec-decode correctness/abort bugs.
- **Tool-calling robustness improved quietly.** The UTF-8 sanitization (b12199) and the open grammar-ordering PR ([#30193](https://github.com/ggml-org/llama.cpp/pull/30193), Qwen3-Coder/Nemotron argument key ordering) and Gemma4 loose-schema grammar work ([#31915](https://github.com/ggml-org/llama.cpp/pull/31915)) all target the most common agent failure mode: malformed tool arguments. If you run agents on Qwen/Nemotron/Gemma, track these.
- **Health checks are not sufficient.** Issue #31704 (embedding slot leak with healthy `/health`) is a reminder to monitor actual throughput/latency, not just liveness. The open UI performance view ([#31893](https://github.com/ggml-org/llama.cpp/pull/31893)) would surface `/metrics` data in llama-ui if merged.
- **Hybrid CPU/GPU MoE serving gets better.** b12206 removes a synchronization stall in `moe-cache`; combined with the `--moe-cache-cpu-share auto` work ([#31286](https://github.com/ggml-org/llama.cpp/pull/31286)), partial-offload MoE deployments should see steadier token pacing.
- **Persistent prompt cache is coming.** `--cache-disk` (#28092) would materially cut cold-start latency for RAG/agent workloads that replay long system prompts — worth prototyping if you restart servers frequently.
- **Out-of-tree mmproj integrators: act on the mtmd refactor now.** `clip.h` is gone as of b12211; any code including it will break on upgrade.

</details>

<details>
<summary><strong>Ollama</strong> — <a href="https://github.com/ollama/ollama">ollama/ollama</a></summary>

# Ollama Digest — 2026-11-09

## 1. Today's Highlights

No new releases landed in the last 24h, but the PR queue is active with backend and scheduler correctness work: Rockchip NPU support (#19480), lossless FP8 import with consistent MoE expert stacking (#19614), and a scheduler fix that keeps unstoppable runners in the loaded set to prevent VRAM overcommit (#19581). On the issue tracker, an AMD Vulkan regression on the 780M (#17748) and a corporate-proxy 403 on `ollama pull` (#19604) remain open, while the long-requested per-model `num_parallel` feature is now implemented in PR #9546.

## 2. Releases & Breaking Changes

None. No new versions in the last 24h; no API/config changes landed.

---

## 3. New Model & Hardware Support

- **Rockchip NPU backend (RK3588/RK3576)** — PR #19480 adds a `rocket`/`rknpu` backend that offloads prompt processing (`MUL_MAT`, `MUL_MAT_ID`, `FLASH_ATTN_EXT`) to the NPU while keeping single-token decode on CPU. [PR #19480](https://github.com/ollama/ollama/pull/19480)
- **Lossless FP8 import + consistent MoE expert stacking** — PR #19614 unifies expert stacking across all source types (fixing MoE models that previously shipped unstacked experts) and adds per-tensor FP8 import; block FP8 is no longer lossily re-quantized to mxfp8. [PR #19614](https://github.com/ollama/ollama/pull/19614)
- **Nvidia MLX/Vulkan in Docker** — PR #18592 adds missing libraries/configs so the MLX runner and Vulkan driver work with Nvidia GPUs inside the container. [PR #18592](https://github.com/ollama/ollama/pull/18592)
- **Image generation models** — currently unsupported on macOS: `ollama run x/flux2-klein` errors with "image generation models are not currently supported" (#19615, closed). Not yet a supported path. [Issue #19615](https://github.com/ollama/ollama/issues/19615)
- **Third-party architecture request** — a request for "OSADIA Axiom" architecture support (#19611) was filed; no indication of acceptance. [Issue #19611](https://github.com/ollama/ollama/issues/19611)

## 4. Performance & Optimization

- **Accurate VRAM prediction for hybrid models and quantized KV cache** — PR #19609 fixes `PredictServerVRAM`, which previously assumed full-context attention KV for every block (wrong for recurrent SSM blocks / `full_attention_interval`) and always billed KV as unquantized f16. This directly affects GPU/CPU placement decisions. [PR #19609](https://github.com/ollama/ollama/pull/19609)
- **Metal prefill chunk shrinking** — PR #19280 reduces prefill chunk size as context grows, preventing macOS GPU watchdog kills on very long prompts (e.g., large XML files). [PR #19280](https://github.com/ollama/ollama/pull/19280)
- **Per-model parallelism** — PR #9546 adds `num_parallel` controllable via env var and Modelfile, so small models (e.g., embeddings) no longer have their context tripled by a server-wide `OLLAMA_NUM_PARALLEL`. Addresses #8842 and the closed #11977/#5693. [PR #9546](https://github.com/ollama/ollama/pull/9546)
- **Context-buffer fill generation** — PR #9547 lets `num_predict==-2` generate until the context buffer is full, matching llama.cpp semantics. [PR #9547](https://github.com/ollama/ollama/pull/9547)
- **Benchmark tooling reliability** — PR #19600 makes `cmd/bench` exit non-zero on incomplete runs and truncate reused output files. [PR #19600](https://github.com/ollama/ollama/pull/19600)

## 5. Stability & Regressions

Ranked by severity:

1. **AMD Radeon 780M Vulkan regression (open, no fix PR)** — Ollama ≥0.32.10 fails on larger models with `radv/amdgpu: Not enough memory for command submission` / `ErrorDeviceLost`; same hardware works on older versions. [Issue #17748](https://github.com/ollama/ollama/issues/17748)
2. **Scheduler VRAM overcommit on failed unload (fix PR open)** — when killing a runner fails, the scheduler still frees the model, predicts against a full GPU, tilts to CPU, and can start a second `llama-server`. PR #19581 keeps the unstoppable runner in the loaded set. [Issue #19577](https://github.com/ollama/ollama/issues/19577) / [PR #19581](https://github.com/ollama/ollama/pull/19581)
3. **403 on `ollama pull` behind corporate proxy (open)** — Blue Coat / RHEL 9.2 setup where `curl` works but the systemd service gets 403; 4 comments, unresolved. [Issue #19604](https://github.com/ollama/ollama/issues/19604)
4. **Post-update resource regression report (open)** — user reports worse behavior after a version update on a 15.4G RAM / 8G RTX 5060 Laptop setup with ministral-3 14B: higher resource use and "more rigid" output. Needs triage. [Issue #19537](https://github.com/ollama/ollama/issues/19537)
5. **Gemma4 tool-call parser bug (fix PR open)** — 45 scalar strings followed by a two-string array can silently drop a valid tool call due to placeholder collision (`NUL,comma,NUL`). [PR #18355](https://github.com/ollama/ollama/pull/18355)
6. **Gemma4 QAT metadata typo (closed)** — `gemma4:12b-it-qat` Modelfile key was `toos` instead of `tools`, breaking tool support for that model. [Issue #19596](https://github.com/ollama/ollama/issues/19596)
7. **GGUF parsing hardening (PRs open)** — reject duplicate tensor names (#19571), zero-dimension tensors (#19607), and non-power-of-2 alignment (#19610); previously these passed metadata parsing and failed only at load/inspect time. [PR #19571](https://github.com/ollama/ollama/pull/19571) · [PR #19607](https://github.com/ollama/ollama/pull/19607) · [PR #19610](https://github.com/ollama/ollama/pull/19610)
8. **CLI noise** — `ollama -v/--version` emits "could not connect to a running Ollama instance" warnings; regression in output cleanliness. [Issue #19613](https://github.com/ollama/ollama/issues/19613)
9. **Context truncation drops last user message** — `chatPrompt()` preserves the last message by type-agnostic position, so trailing tool-call messages can displace the actual final user turn. [PR #19058](https://github.com/ollama/ollama/pull/19058)

Lower priority: `WriteWithBackup` timestamp collisions on rapid writes (#19608), swallowed server start errors in the app (#19179), and model thrashing from unset API fields (#10003).

## 6. What This Means for Application Developers

- **Multi-model deployments get finer control.** Once #9546 lands, you can tune `num_parallel` per model instead of paying the context-multiplier tax globally — important if you co-host an embedding model alongside a chat model.
- **Plan for known regressions if you're on AMD Vulkan.** Pin a version below 0.32.10 for 780M iGPU users, or track #17748 before upgrading.
- **Corporate/proxied environments:** `ollama pull` failures behind TLS-inspecting proxies (#19604) are still open; validate proxy CA handling for the systemd service, not just your shell.
- **Agent tool-calling on Gemma4:** watch #18355 (parser collision silently dropping tool calls) and #19596 (fixed `toos` metadata typo); re-pull QAT models if you rely on tools.
- **VRAM planning improves shortly.** The hybrid/quantized-KV VRAM prediction fix (#19609) will make GPU offload decisions more accurate for SSM-hybrid and quantized-cache models — expect fewer surprise CPU tilts after it merges.
- **Edge deployments expand:** the Rockchip NPU backend (#19480) is worth evaluating for RK3588/RK3576 boards where prefill is the bottleneck.

</details>

<details>
<summary><strong>LiteLLM</strong> — <a href="https://github.com/BerriAI/litellm">BerriAI/litellm</a></summary>

# LiteLLM Digest — 2026-11-09

## 1. Today's Highlights
The release stream continues with the `v1.110.0-dev.2` dev tag, now shipping cosign-signed Docker images with documented verification steps. Issue volume remains high (56 updated in 24h, 304 PRs touched), with the most significant activity centered on routing/callback hot-path performance, Anthropic `/v1/messages` translation fidelity (extra_body passthrough, fallback pricing), and a cluster of spend-accounting bugs where budgets fail to close when upstream usage is missing or malformed. A notable correctness theme today: streaming disconnect handling in `/v1/responses` and `/v1/messages` leaves stale budget reservations and upstream streams open.

## 2. Releases & Breaking Changes
- **v1.110.0-dev.2** — Dev build; Docker images signed with cosign using the key introduced in commit `e82a3c6`. Supply-chain verification guidance included in release notes. https://github.com/BerriAI/litellm/releases
- No config-schema breaking changes landed today, but two open PRs are marked breaking (`fix(responses)!`, `fix(router)!`) — see Stability below for details before upgrading past them.

## 3. New Model & Hardware Support
- **Mistral native token counting** — PR adds upstream token counting via `POST https://api.mistral.ai/v1/responses/input_tokens`. https://github.com/BerriAI/litellm/pull/50653
- **New OpenAI-compatible providers proposed/registered**:
  - Superbot (`superbot-gg`) with spend pricing — https://github.com/BerriAI/litellm/pull/47505
  - Open Uncensored (Chat Completions + native Responses routes) — https://github.com/BerriAI/litellm/pull/50564
  - OpenScale (DeepSeek V4.1 Flash fp8, GLM 5.3 Flash) — https://github.com/BerriAI/litellm/pull/50397
- **Per-deployment OAuth M2M for Databricks** (feature request, open): currently only a single global service principal is supported via `DATABRICKS_CLIENT_ID`/`SECRET`. https://github.com/BerriAI/litellm/issues/48765
- **LocalAI** provider request closed after a long tail — status worth checking if you depend on GGUF/llama.cpp backends. https://github.com/BerriAI/litellm/issues/1443

## 4. Performance & Optimization
- **Router callback filter + Anthropic capability probe caching** (open PR): targets two per-request Python hot paths — skipping unused deployment reads in `callback_filters` and caching capability probes in the Rust messages route. Motivation cited: Rust core comfortably handles 1,000 deployments only if it stops reading every deployment back into Python. https://github.com/BerriAI/litellm/pull/50641
- **SpendLogDelivery accounting metrics** (closed): `AccountingDeliveryRepository.metrics()` was scanning *all* retained `LiteLLM_SpendLogDelivery` rows on every flush; issue closed, implying a bounded-query fix. https://github.com/BerriAI/litellm/issues/50475
- **JSON log secret scanning** (open): redundant regex/secret scans of unchanged strings during JSON log formatting; distinct from the earlier pathological-regex issue #32353. https://github.com/BerriAI/litellm/issues/40247
- **Recursive tool schemas** (open, stale): `unpack_defs` still unbounded on `$defs`/`$ref` high-fan-in schemas; Bedrock & Vertex callers pass no byte budget. Multi-second-to-hang risk on adversarial or generated tool schemas. https://github.com/BerriAI/litellm/issues/34328

## 5. Stability & Regressions (ranked by severity)

**High — billing/correctness**
- **Key budgets not charged on missing/malformed usage** — When an OpenAI-compatible upstream omits `usage`, spend is never recorded and `max_budget` never closes; 20/20 calls succeeded on a budget sized for 9 (+100.5% upstream billing). Malformed `usage` (`{"prompt_tokens":"oops",...}`) shows the same failure. https://github.com/BerriAI/litellm/issues/50124
- **`/v1/responses` disconnect leaves full Redis budget reservation** — Streaming request cancelled pre-first-token records HTTP 499 but leaves `finalized=false` reservation intact. https://github.com/BerriAI/litellm/issues/50528
- **`max_budget` admits over-budget calls** — 11 calls admitted on a budget fitting 9 even serially (not just a burst race). https://github.com/BerriAI/litellm/issues/50125
- **Anthropic server-side fallback mispriced** — Each fallback attempt was priced at the *answering* model's rate, and unbilled refusals were counted. Fix PR open (breaking-tagged). https://github.com/BerriAI/litellm/pull/50556

**High — protocol/translation**
- **`/v1/messages` returns misleading 503** — Any non-`ProxyException` upstream failure (e.g. connection refused) surfaces as `"authentication database is temporarily unreachable"`, misdirecting operators; `/v1/chat/completions` returns correct 50x. https://github.com/BerriAI/litellm/issues/49998
- **Unparseable 200s raised as 500** — Provider returns HTTP 200 with non-JSON or JSON lacking `choices`; litellm raises an opaque APIError and drops `usage`, causing callers to retry an already-billed request. https://github.com/BerriAI/litellm/issues/49793
- **Bedrock Invoke `/v1/messages` stream not closed after detached-drain abort** — With `ANTHROPIC_MESSAGES_MAX_DETACHED_STREAM_DRAINS=0`, upstream keeps generating after client disconnect; httpx response never closed. https://github.com/BerriAI/litellm/issues/50514
- **`reasoning_effort="none"` on Claude** — Strips the thinking config instead of explicitly disabling, leaving upstream default-on thinking active (closed). https://github.com/BerriAI/litellm/issues/47924
- **Amazon Nova streaming 100% failure** — httpx zstd decoder reuses a finished `decompressobj` across Nova multi-frame SSE (closed). https://github.com/BerriAI/litellm/issues/35589
- **Deployment `extra_body` dropped on `/v1/messages`** — Anthropic server-side fallback configured per-deployment was silently ignored; fix PRs open at PR #50558 and the router-merge variant PR #49839 (breaking-tagged). https://github.com/BerriAI/litellm/pull/50558
- **`/v1/audio/speech` Qwen3-TTS** — Mandatory `voice` param and custom params stripped (open). https://github.com/BerriAI/litellm/issues/20078

**Medium — routing/observability**
- **Arize Phoenix double-counting** — Proxy parent span `litellm_proxy_request` duplicates LLM attributes, so Phoenix prices every call twice on the legacy path (`LITELLM_OTEL_V2` unset). Fix PR open. https://github.com/BerriAI/litellm/issues/50649 / https://github.com/BerriAI/litellm/pull/50655
- **Router retries ignore caller timeout** — `x-litellm-timeout` bounds each attempt only; `Retry-After` sleeps don't check remaining caller budget. https://github.com/BerriAI/litellm/issues/49751
- **MCP access group listing leaks scope** — `GET /v1/mcp/access_groups` returns groups on servers the key cannot see/call; fix PR open. https://github.com/BerriAI/litellm/pull/50650
- **MCP per-request auth header ignored** — `x-mcp-{alias}-authorization` resolves as no-auth when no static credential configured. https://github.com/BerriAI/litellm/issues/35403
- **`trim_messages` no-ops on prefixed models** — `openai/gpt-4o`, `anthropic/claude-haiku-4-5` skip trimming entirely; also fails on multimodal list content and `None` content. Fix PRs open. https://github.com/BerriAI/litellm/issues/50639 / https://github.com/BerriAI/litellm/pull/50645 / https://github.com/BerriAI/litellm/pull/50648
- **OpenAI Agents SDK + Claude on Bedrock** — Deferred tools not found after tool search, search record lost on later turns, compaction param dropped on `/v1/messages`. https://github.com/BerriAI/litellm/issues/50104

**Low**
- `ModelNotMappedError` in `utils.py` reported with no repro data. https://github.com/BerriAI/litellm/issues/47922
- `user_url_allowed_hosts` ignored under `litellm_settings` (closed). https://github.com/BerriAI/litellm/issues/35177
- MCP stdio server edit erases stored env vars. https://github.com/BerriAI/litellm/issues/28903
- UI callbacks overwrite config-defined callbacks (closed). https://github.com/BerriAI/litellm/issues/12118

## 6. What This Means for Application Developers
- **Do not rely on `max_budget` as a hard spend cap yet.** Three independent issues (#50124, #50125, #50528) show budgets over-serving when usage is absent, when concurrency is high, or when streams are cancelled. If you enforce cost ceilings, add an upstream-side guard (provider budget/quota) rather than trusting the gateway alone.
- **Retry logic needs awareness of the "200 that can't be parsed" case.** Issue #49793 means your SDK may see a 500-shaped error for a successfully billed upstream call; blindly retrying `acompletion` can double-charge. Consider idempotency keys or de-duplicating on `request_id` before retry.
- **Anthropic `/v1/messages` consumers should pin carefully.** Multiple in-flight fixes are tagged breaking (`fix(responses)!`, `fix(router)!`) and touch `extra_body` merge semantics. If you rely on per-deployment `extra_body` for Anthropic server-side fallback, it currently does not reach the wire on `/v1/messages` — test after upgrading past PR #50558 / #49839.
- **Caller-facing timeouts are not total-budget bounds.** Per issue #49751, `x-litellm-timeout` bounds each attempt; a burst of `Retry-After` retries can exceed your intended wall clock. Enforce total deadlines client-side.
- **Observability cost double-counting:** if you use Arize Phoenix via the legacy callback path, expect ~2× cost attribution until PR #50655 lands. Alternatively set `LITELLM_OTEL_V2`.
- **Tool-schema hygiene matters** — recursive `$defs`/`$ref` schemas with high fan-in can trip an unbounded `unpack_defs` path on Bedrock/Vertex; pre-flatten or bound generated schemas in agent tool definitions.

</details>

<details>
<summary><strong>Unsloth</strong> — <a href="https://github.com/unslothai/unsloth">unslothai/unsloth</a></summary>

# Unsloth Digest — 2026-11-09

## Today's Highlights
No new releases landed in the last 24 hours, but the PR queue is highly active with a large batch of danielhanchen-authored Studio/diffusion fixes merged (many CLOSED same-day) plus open follow-ups for Turing INT8 support and a thread-safety fix for concurrent model loads. The main thrust today is Studio's image/video diffusion path (fp8 denoisers, NVFP4, INT8 kernels, VAE decode fallbacks) plus UI polish around Settings→Appearance. Two open feature requests (`#16980`, `#17126`) signal growing pressure for checkpoint retention and image-editing endpoints in Studio.

## Releases & Breaking Changes
None in the last 24h. No API/config changes announced.

## New Model & Hardware Support
- **[OPEN] Studio: INT8 on T4/Turing (sm75)** — Lifts the `dense_transformer_supported` refusal so `transformer_quant="int8"` runs on Turing GPUs instead of erroring before load. [#17167](https://github.com/unslothai/unsloth/pull/17167)
- **[OPEN] FLUX.3 on fp16-only GPUs** — Keeps bf16 weights as stored and computes per-layer in float32, avoiding the full float32 host materialization introduced in #17124. [#17173](https://github.com/unslothai/unsloth/pull/17173)
- **[CLOSED] Per-card judgment of compiled ROCm W8A8 linear** — Previously cached per-process; now judges per card so an RDNA3-then-RDNA4 (or vice versa) sequence picks the correct kernel. [#17078](https://github.com/unslothai/unsloth/pull/17078)
- **[OPEN] LLM training page: `constant` and `constant_with_warmup` LR schedulers** — UI-only exposure of schedulers already accepted by the backend. [#17168](https://github.com/unslothai/unsloth/pull/17168)
- **[Bug] Intel Arc 140V (desktop app)** — Image generation models fail to load. [#17137](https://github.com/unslothai/unsloth/issues/17137)
- **[Bug] ROCm Docker on gfx1031 (RX 6700 XT)** — Startup check passes as "RDNA 2" but PyTorch segfaults on first GPU op. [#17147](https://github.com/unslothai/unsloth/issues/17147)

## Performance & Optimization
- **[CLOSED] Qwen-Image-2.1 CFG pairing** — Block-streamed renders with true CFG were uploading each streamed block twice per step; guidance branches are now paired per streamed block. [#17111](https://github.com/unslothai/unsloth/pull/17111)
- **[CLOSED] MiniMax-H3 conditioner memory** — Only the hidden state H3 actually reads (`hidden_states[50]`) is retained; **~2 GiB saved at 4096 tokens** on the 50-layer Qwen3-VL conditioner. [#17095](https://github.com/unslothai/unsloth/pull/17095)
- **[CLOSED] Load prefetch** — Skips chunks already in page cache via `mincore` and budgets per-worker read buffers (previously 64 MiB × 8 workers unconditionally). [#17112](https://github.com/unslothai/unsloth/pull/17112)
- **[CLOSED] Image postprocess** — `uint8_hwc` now converts in row strips with one float32 copy each instead of materializing the full batch. [#17091](https://github.com/unslothai/unsloth/pull/17091)
- **[CLOSED] INT8 fused MLPs / v2 Linears** — Inputs quantized with the probed one-pass Triton kernel where `_act_quant_device_ok` has verified bit-identity to torchao. [#17114](https://github.com/unslothai/unsloth/pull/17114)
- **[CLOSED] Queued image generations** — Now wait on their own pool rather than holding default-executor workers. [#17108](https://github.com/unslothai/unsloth/pull/17108)
- **[OPEN] Diffusion Triton kernels device targeting** — Nine launches in `diffusion_vae_fused.py` were implicitly launching on the thread's current CUDA device; PR #17099 (CLOSED) fixes to use the input tensor's card.
- **[CLOSED] NVFP4 protected branch** — Eliminates a recompile on the second identical call (cold decode-table memo) and accepts fp16 inputs. [#17081](https://github.com/unslothai/unsloth/pull/17081)

## Stability & Regressions
Ranked by severity:
1. **HIGH — fp8 denoiser hang on L4 (sm_89)**: FLUX.3 klein fp8 hangs forever at `captured Flux3Transformer2DModel graph 1/4` under torch < 2.12. **Fix PR open**: [#17166](https://github.com/unslothai/unsloth/pull/17166).
2. **HIGH — Concurrent diffusion load thread-unsafety**: `init_empty_weights` patches torch globally; concurrent skeleton builds can leave torch patched, causing intermittent `399 tensor(s) ...` load failures. **Fix PR open**: [#17171](https://github.com/unslothai/unsloth/pull/17171).
3. **MEDIUM — MoE router aux loss divergence on CPU backend**: With `UNSLOTH_FORCE_CPU=1`, pipeline parallel + grad accumulation > 1 trains a different loss than single-process; repo's own parity test reproduces. No fix PR yet. [#17054](https://github.com/unslothai/unsloth/issues/17054)
4. **MEDIUM — Managed accounts ignore saved HF token**: Gated models (e.g. FLUX.1-dev) refused despite a valid token. **Fix PR open**: [#17172](https://github.com/unslothai/unsloth/pull/17172).
5. **MEDIUM — Video VAE decode fallback only matched Windows allocator error**: Linux/macOS refused-allocator spelling not recognized. **Fixed (CLOSED)**: [#17075](https://github.com/unslothai/unsloth/pull/17075).
6. **LOW — LoRA fuse host backup could pin integrated GPU memory / exceed host RAM reserve**. **Fixed (CLOSED)**: [#17086](https://github.com/unslothai/unsloth/pull/17086).
7. **LOW — Studio Settings UI**: Palette and other appearance pickers close on every selection. **Fix PRs open**: [#17164](https://github.com/unslothai/unsloth/pull/17164), [#17170](https://github.com/unslothai/unsloth/pull/17170); duplicate glass material control [#17162](https://github.com/unslothai/unsloth/issues/17162), composer settings split across tabs [#17163](https://github.com/unslothai/unsloth/issues/17163).
8. **LOW — Model size mismatch** between Model Hub and Model Picker dropdown. [#17074](https://github.com/unslothai/unsloth/issues/17074)

## What This Means for Application Developers
- **Diffusion workloads are the active frontier.** Many same-day CLOSED PRs target image/video rendering (CFG pairing, NVFP4, INT8, VAE tiling, device targeting). If you run Studio for image/video generation, pin to a known-good commit — several fixes are fresh and the fp8/Turing paths still have open regressions.
- **Turing (T4) users gain INT8 and safe FLUX.3 fp16 rendering** in open PRs — relevant if you're serving on older cloud GPUs.
- **Concurrent loads are not yet safe.** If your deployment loads multiple diffusion models concurrently (or per-request), watch [#17171](https://github.com/unslothai/unsloth/pull/17171) before enabling that pattern.
- **Managed/org deployments**: gated repos require the fix in [#17172](https://github.com/unslothai/unsloth/pull/17172); until merged, managed accounts cannot use their own HF token.
- **MoE + CPU training** (pipeline parallel, grad accum > 1) is currently incorrect — do not rely on it for loss parity.
- **Feature asks to track**: checkpoint retention during Image LoRA runs ([#16980](https://github.com/unslothai/unsloth/issues/16980)) and image-edit endpoints (`/v1/images/edits` via external OpenAI-compatible servers, [#17126](https://github.com/unslothai/unsloth/issues/17126)) — both affect studio automation pipelines.

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar).*