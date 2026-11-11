# AI Infrastructure Digest 2026-11-12

> Generated: 2026-11-11 23:28 UTC | Projects covered: 6

- [vLLM](https://github.com/vllm-project/vllm)
- [SGLang](https://github.com/sgl-project/sglang)
- [llama.cpp](https://github.com/ggml-org/llama.cpp)
- [Ollama](https://github.com/ollama/ollama)
- [LiteLLM](https://github.com/BerriAI/litellm)
- [Unsloth](https://github.com/unslothai/unsloth)

---

## Cross-Project Comparison

⚠️ Comparative analysis generation failed.

---

## Per-Project Reports

<details>
<summary><strong>vLLM</strong> — <a href="https://github.com/vllm-project/vllm">vllm-project/vllm</a></summary>

# vLLM Digest — 2026-11-12

## 1. Today's Highlights

No new releases landed in the last 24h, but activity is heavily concentrated on AMD/ROCm optimization: MI355X (gfx950) trackers for Qwen3.8 and DeepSeek-V4.1 are very active, and new PRs push monokernel/fused-launch paths for DSpark and GLM-5.3. On the NVIDIA side, the plan to replace Marlin with the "Humming" kernel family across architectures gained traction, while several correctness bugs (MXFP8 activation-scheme selection, confidential-computing garbage output, detokenizer stalls) remain open.

## 2. Releases & Breaking Changes

None in the last 24h.

## 3. New Model & Hardware Support

- **NVIDIA Nemotron 3.5 ASR Streaming** — feature request for `nemotron-3.5-asr-streaming-0.6b` (600M multilingual streaming ASR). Still open, no implementation PR referenced. [#47455](https://github.com/vllm-project/vllm/issues/47455)
- **GLM-5.3 on HiSparse** — PR makes HiSparse functional on `GLM-5.3-Flash` (`Glm5NextForConditionalGeneration`), the first hybrid model on HiSparse (11/45 layers sparse MLA, rest KDA). Fixes startup failures and per-request GPU memory overhead. [#61243](https://github.com/vllm-project/vllm/pull/61243)
- **AMD ROCm**: AITER Triton MXFP8 dense linear kernel for CDNA4/gfx950 added to the ROCm MXFP8 kernel list. [#64089](https://github.com/vllm-project/vllm/pull/64089)
- **AMD ROCm**: Specialized decode path for GLM-5.3 layered on the GLM-5 decode work. [#67095](https://github.com/vllm-project/vllm/pull/67095)

## 4. Performance & Optimization

- **Marlin → Humming migration (NVIDIA)**: tracking RFC to retire the SM80-era Marlin kernels. Humming now covers INT4/INT8 GPTQ/AWQ, FP8, MXFP8/MXFP4/NVFP4, MXFP6, and INT8/FP8 activations for dense and MoE layers, with arch-specific paths for Hopper/Blackwell. [#65560](https://github.com/vllm-project/vllm/issues/65560)
- **PTX 9.4 `ldmatrix.s8.s4` for W4A8-INT8** (closed): hardware INT4→INT8 expanding load adopted in W4A8-INT8 paths. [#49529](https://github.com/vllm-project/vllm/issues/49529)
- **ROCm MI355X DeepSeek-V4.1**: PR shards the MXFP4 sparse-indexer decode rows across TP, eliminating redundant top-512 index computation on every rank (MI355X, TP8). [#66047](https://github.com/vllm-project/vllm/pull/66047)
- **ROCm DSpark monokernels**: one Triton launch per Markov sampling round (was per-round kernels) and one persistent FlyDSL launch for 5 draft layers (was ~70 kernels + 11 all-reduces). [#66051](https://github.com/vllm-project/vllm/pull/66051), [#65985](https://github.com/vllm-project/vllm/pull/65985)
- **Batch-invariance tuning**: optimized matmul configs for `Qwen2.5-0.5B` under `VLLM_BATCH_INVARIANT=1` claim up to **2.84× lower response latency** for small batches on sm120 (TTFT largely unchanged). [#67079](https://github.com/vllm-project/vllm/pull/67079)
- **Attention backend overhead**: report that Triton/eager attention backends re-derive K/V cache views per call — PyTorch `.contiguous()` on slices could run on the host and copy the whole KV cache; impact described as "substantial fractions" of step time. [#64572](https://github.com/vllm-project/vllm/issues/64572)
- **ROCm trackers** (open, stacked PRs): Qwen3.8-2.4T-A95B MI355X [#57149](https://github.com/vllm-project/vllm/issues/57149), Qwen3.8-Flash-Next MI355X [#59575](https://github.com/vllm-project/vllm/issues/59575), Muse Spark PTPC-FP8 gfx942 [#64018](https://github.com/vllm-project/vllm/issues/64018), DeepSeek-V4.1-Flash MI355X [#66587](https://github.com/vllm-project/vllm/issues/66587)
- **KV offloading**: TP-agnostic KV offloading tracker (cross-TP-size sharing of offloaded KV), seeking reviewers/owners. [#61886](https://github.com/vllm-project/vllm/issues/61886)

## 5. Stability & Regressions

Ranked by severity:

1. **Silent garbage output under Confidential Computing** — V2 model runner's UVA views of pinned host memory go stale under CC; works with `VLLM_USE_V2_MODEL_RUNNER=0`. Silent wrong outputs are the worst failure class. No fix PR shown. [#57224](https://github.com/vllm-project/vllm/issues/57224)
2. **MXFP8 weight-only checkpoints silently run W8A8** — `init_mxfp8_linear_kernel` lacks an `activation_quant_key` parameter (unlike MXFP4/MXFP6 siblings), so W8A16 checkpoints execute with wrong activation precision. Silent accuracy corruption. No fix PR shown. [#65193](https://github.com/vllm-project/vllm/issues/65193)
3. **Incremental detokenization is O(n²) on malformed UTF-8** — holds back and re-decodes the whole incomplete run each step; can stall the API server event loop (`/health` hangs). Fix PR open: [#63668](https://github.com/vllm-project/vllm/pull/63668) (issue [#63667](https://github.com/vllm-project/vllm/issues/63667))
4. **Qwen3.8-27B GDN hybrid, PP=3 on sm_86** — illegal memory access (Xid 31) with prefix caching, gibberish without; regression since v0.28.0 through v0.32.0, both model runners. (Closed; verify fix version.) [#63505](https://github.com/vllm-project/vllm/issues/63505)
5. **EAGLE draft acceptance collapse on MRV2** — target backends needing smaller kernel blocks than `--block-size` (e.g., MiniMax-M3's 128-token pages) break draft attention. Fix PR open: [#64777](https://github.com/vllm-project/vllm/pull/64777)
6. **ROCm `ROCM_ATTN` wrong attention / GPU faults on RDNA** — head size ≥96 with KV block size ≠16, and MHA head size 192/256. [#62697](https://github.com/vllm-project/vllm/issues/62697)
7. **GLM-5.3 TP8 B200 HiSparse launch failure** once KV spills to host (MNNVL all-reduce). Closed. [#64327](https://github.com/vllm-project/vllm/issues/64327)
8. **Qwen3.5-122B-A10B-FP8 GSM8K accuracy regression** at commit `0fae39a`. [#65906](https://github.com/vllm-project/vllm/issues/65906)
9. **Pythonic tool parsers drop streaming text starting with `[`** — non-tool-call bracketed text vanishes in streams but appears non-streamed. Fix PR open: [#67101](https://github.com/vllm-project/vllm/pull/67101)
10. **Other open fixes**: Gemini4DSpark weight-loader migration [#54069](https://github.com/vllm-project/vllm/pull/54069), Gemma 4 PLE CPU offload bypass [#62354](https://github.com/vllm-project/vllm/pull/62354), FlashInfer one-sided workspace teardown leak [#63874](https://github.com/vllm-project/vllm/pull/63874), ROCm AITER biased top-k NaN propagation [#67107](https://github.com/vllm-project/vllm/pull/67107)

## 6. What This Means for Application Developers

- **Pin and verify quantized deployments.** The W8A16→W8A8 MXFP8 bug (#65193) produces *silently wrong* outputs on weight-only checkpoints — no crash, no warning. Run a small eval suite (e.g., GSM8K subset) on any MXFP8 checkpoint after upgrading.
- **Audit Confidential Computing deployments.** If you run vLLM under TDX/CC mode, either pin to a runner config with V2 disabled or validate outputs, since garbage generation is silent (#57224).
- **Streaming tool-call agents**: if you use `--tool-call-parser pythonic` / `llama4_pythonic`, expect bracketed plain text at the start of a response to be dropped in streaming mode. The fix PR (#67101) is worth tracking if your agents emit citation-style `[1]` prefixes.
- **Long-running API servers** emitting byte-level tokenizers with occasional malformed UTF-8: the O(n²) detokenizer path can block the event loop and hang health checks — relevant for Kubernetes liveness probes (#63667).
- **AMD MI355X adopters** get a steady stream of perf uplifts across Qwen3.8, DeepSeek-V4.1, GLM-5.3 and Muse Spark; expect meaningful decode-path gains from sparse-indexer sharding and monokernel launches if serving those models.
- **Batch-invariance users** (`VLLM_BATCH_INVARIANT=1`) get a measurable latency win for small models/small batches; MEAN pooling also becomes reproducible under the flag (#60035, closed).

</details>

<details>
<summary><strong>SGLang</strong> — <a href="https://github.com/sgl-project/sglang">sgl-project/sglang</a></summary>

# SGLang Digest — 2026-11-12

## 1. Today's Highlights

No new releases landed in the last 24h; activity is dominated by a **probable SM90 decode regression introduced in v0.5.23** (#49336, +33–45% per-step decode time on DeepSeek-V4.1-Flash at concurrency 12) and a companion **engine-state corruption bug on GLM-5.3-Flash** after 1M-token requests in the same version (#48964). On the positive side, the **FlashInfer 0.7.4 bump** (#49771) and a batch of HiCache/DCP correctness fixes are progressing, and the **GLM-5.3-Flash / Qwen3.8-Flash-Next** support matrix continues to expand across Hopper and NPU backends.

---

## 2. Releases & Breaking Changes

No releases in the last 24h. Two items to flag as forward-looking compatibility signals:

- **PR #49771 — Bump FlashInfer to 0.7.4** (open): Updates pins in `docker/Dockerfile`, `docker/Dockerfile.cu134`, and related release scripts. Relevant for anyone building custom images. https://github.com/sgl-project/sglang/pull/49771
- **PR #49638 — `[AMD]` Reject explicit AITER fusion in deterministic mode** (open): An explicitly requested `--enable-aiter-allreduce-fusion` will now raise an actionable error under deterministic mode (including when implied by `--rl-on-policy-target`). Config that previously silently degraded will now fail fast. https://github.com/sgl-project/sglang/pull/49638

---

## 3. New Model & Hardware Support

**Models**
- **Phi-4-multimodal W4A16 (AutoRound) on CPU** — PR #49482 adds CPU support for `INCModel4/Phi-4-multimodal-instruct-W4A16-AutoRound`, working around a missing `audio_compression_rat...` field in `processor_config.json`. https://github.com/sgl-project/sglang/pull/49482
- **GLM-5.3-Flash on Hopper (SM90)** — active perf tracking (#45373); PCP support already landed (#42044). Bug-trapping work continues alongside it. https://github.com/sgl-project/sglang/issues/45373
- **Qwen3.8-Flash-Next on NPU** — PR #37570 adds graph replay, NEXTN/MTP speculative decoding, and Triton kernels for QSA. Depends on `sgl-kernel-npu#807`. https://github.com/sgl-project/sglang/pull/37570
- **Kimi-K3 PCP** — still open on the Prefill CP roadmap (#21788). https://github.com/sgl-project/sglang/issues/21788

**Hardware / kernels**
- **SM12x NVFP4 MoE via FlashInfer cuTile** — Feature request #49123 proposes making cuTile NVFP4 MoE the default runner on RTX 50xx / RTX PRO Blackwell. https://github.com/sgl-project/sglang/issues/49123
- **SM12x CUDA 13 out-of-the-box** — still broken: #49209 reports no model runs from a stock CUDA 13 wheel on sm_120. High-impact for consumer Blackwell adopters. https://github.com/sgl-project/sglang/issues/49209
- **XGrammar 0.2.9 + Lark grammar support** — PR #39380 wires `compile_lark` into the grammar API and pins XGrammar to 0.2.9. https://github.com/sgl-project/sglang/pull/39380
- **NVFP4 KV roadmap** — closed by inactivity (#29913), with SM120/B200 initial PRs referenced. https://github.com/sgl-project/sglang/issues/29913

---

## 4. Performance & Optimization

- **DSv4.1 image preprocessing caching** (PR #45287) — multi-turn agents that resend images currently re-decode and re-patchify on every turn in the tokenizer process; this caches by image bytes + patchify backend. Meaningful TTFT win for vision agents. https://github.com/sgl-project/sglang/pull/45287
- **KDA `fused_recurrent_kda` in-place state updates** (PR #48743, closed) — eliminates per-request gather/scatter of fp32 `[HV, V, K]` state (24 heads × 128) for KDA decode. https://github.com/sgl-project/sglang/pull/48743
- **KDA speculative ReplaySSM ring moved into request sub-pool** (PR #49720) — under `--enable-unified-memory`, cached prefix checkpoints no longer carry an unread ring per commit, reducing per-checkpoint footprint. https://github.com/sgl-project/sglang/pull/49720
- **Diffusion head-128 mean-square fusion** (PR #49308) — fuses cast/square/mean chain in nomas QK RMSNorm+RoPE on B300. https://github.com/sgl-project/sglang/pull/49308
- **Custom all-reduce v2: one symmetric workspace per group** (PR #49382) — decouples sizes and launch widths across kernel families. https://github.com/sgl-project/sglang/pull/49382
- **AMD Triton cache flooding fix** (PR #33506) — `_get_valid_kv_indices_kernel` was recompiling per distinct index shape in DSA aiter backends. https://github.com/sgl-project/sglang/pull/33506
- **Scheduler: UniBoost starvation-bounded policy RFC** (#48519) — proposes a wait-bounded policy for agentic workloads where `lpm`/`shortest-prefill-first` can starve long shared-prefix requests. https://github.com/sgl-project/sglang/issues/48519
- **`import sglang` latency** — still 12s on H100 (#10492, good-first-issue, high priority). https://github.com/sgl-project/sglang/issues/10492

---

## 5. Stability & Regressions

Ranked by severity:

1. **🔴 SM90 decode regression v0.5.22 → v0.5.23** (#49336): +33–45% per-step decode time on 8×H20-3e with DeepSeek-V4.1-Flash + DSPARK at concurrency 12, across 8K–64K contexts. `use_deep_select_decode` also costs acceptance on Hopper. No fix PR referenced yet — **pin to v0.5.22 if on SM90 + speculative decoding**. https://github.com/sgl-project/sglang/issues/49336
2. **🔴 GLM-5.3-Flash engine-state corruption after 1M-token request** (#48964): MTP accept rate goes to 0.00 and all subsequent requests degenerate; clean on v0.5.22. TP=4 H100, w4afp8, EAGLE. Long-running servers are at risk of silent degradation. https://github.com/sgl-project/sglang/issues/48964
3. **🟠 Silent accuracy loss on every prefix-cache hit** (#40903): DeepSeek MLA chunked-prefix prefill merges base-2 LSE into a natural-log `merge_state_v2` with no conversion. Long-standing, affects correctness of cached responses. https://github.com/sgl-project/sglang/issues/40903
4. **🟠 Cross-client token leak in multi-tokenizer mode** (#49903): duplicate `rid` state merging can serve one client's tokens to another. Security-relevant; newly filed. https://github.com/sgl-project/sglang/issues/49903
5. **🟠 GLM-4.7-Flash W4A16 compressed-tensors load failure** (#49369, closed): MLA post-load reads `kv_b_proj.weight` which packed layers lack. Fix landed. https://github.com/sgl-project/sglang/issues/49369
6. **🟠 Qwen4-Exp PLE: BF16 n-gram shards cast to FP8 with unit scale** (#49493): `ple_embedding_dtype: float8_e4m3fn` in config triggers wrong-cast on a ~6.7k-download checkpoint. https://github.com/sgl-project/sglang/issues/49493
7. **🟡 HiCache hit-rate dip under L2/L3 pressure** (#47481): reproducible transient drop from 87.4% with HiCache + Mooncake; watch for benchmark noise. https://github.com/sgl-project/sglang/issues/47481
8. **🟡 API inconsistency: `/v1/responses` streaming** returns HTTP 200 + `response.failed` for context-length-exceeded, while `/v1/chat/completions` correctly returns 400 (#48557). Client error-handling must special-case this. https://github.com/sgl-project/sglang/issues/48557
9. **🟡 Rust server: missing BOS** on `/generate` text prompts when tokenizer relies on `add_bos_token` (#49519). Fix in flight via HiCache/DCP PRs (#49943, #49917). https://github.com/sgl-project/sglang/issues/49519
10. **🟡 Encoder-decoder KV double-free** when `page_size > 1` (#38840, closed by inactivity — may still be live). https://github.com/sgl-project/sglang/issues/38840

Also notable: the flaky-CI tracker (#42752) has 198 comments and a new diffusion perf-runner flakiness issue (#48311) — CI signal is currently noisy, so treat green/red as less reliable than usual when gating upgrades.

---

## 6. What This Means for Application Developers

- **Do not blanket-upgrade to v0.5.23 on Hopper.** If you run DeepSeek-V4.1, GLM-5.3-Flash, or any EAGLE/MTP speculative decoding on SM90, stay on v0.5.22 until #49336 and #48964 have fixes. The GLM bug is especially nasty for long-lived agent servers: failures are silent (acceptance → 0), not crashes.
- **Prefix caching is not uniformly trustworthy yet.** #40903 implies measurable accuracy drift on DeepSeek MLA with chunked-prefix prefill + cache hits. If you depend on deterministic outputs, evaluate with prefix caching disabled or pin to a known-good version.
- **Multi-tokenizer deployments: audit request-ID generation.** #49903 is a cross-client data-isolation bug — exactly the class of issue that matters for multi-tenant gateways.
- **Agentic/vision workloads get cheaper:** the DSv4.1 image preprocessing cache (#45287) targets the dominant cost in multi-turn vision agents (re-encoding resent images every turn). Worth benchmarking once merged.
- **If you build custom containers, hold for FlashInfer 0.7.4** (#49771) to avoid churn across two bumps; the AITER deterministic-mode change (#49638) will surface previously silent config conflicts as hard errors — check your `--enable-aiter-allreduce-fusion` usage.
- **SM12x (RTX 50-series) is still not a supported path** out of the box (#49209); the cuTile NVFP4 MoE default (#49123) is the fix direction but not landed.

</details>

<details>
<summary><strong>llama.cpp</strong> — <a href="https://github.com/ggml-org/llama.cpp">ggml-org/llama.cpp</a></summary>

# llama.cpp Digest — 2026-11-12

## Today's Highlights

A heavy hardware-backend day: CUDA landed OCP FP8 support (with W8A16 for GEMV), Vulkan enabled dequantized KV-cache optimization under CoopMat2, and core llama landed FP8 KV-cache support. On the server side, the "layer-first prefill" proposal (#32082) claims roughly 2× prefill speedup for MoE models with `-cmoe` on PCIe-bound hosts. The issue tracker continues to accumulate quantization-related correctness reports (speculative decoding divergence on quantized targets, FP8/F16 accumulator overflows), several of which are already closed as stale.

---

## Releases & Breaking Changes

- **b12289** — tests now use only the public ggml API ([release](https://github.com/ggml-org/llama.cpp/releases)). Downstreams that patched ggml internals for testing will need to migrate.
- **b12277** — `mtmd` string-format API renamed to `mtmd_format` ([release](https://github.com/ggml-org/llama.cpp/releases)). Check multimodal header includes if you build against mtmd.
- **b12274** — FP8 KV-cache support; cache-scale naming normalized independently of projection layout ([release](https://github.com/ggml-org/llama.cpp/releases)). Any config or tooling that referenced prior cache-scale names should be updated.
- **b12282** — chat server now honors `response_format: json_schema` for Muse Spark ([release](https://github.com/ggml-org/llama.cpp/releases)); behavior change for clients relying on the previous ignore-semantics.

---

## New Model & Hardware Support

- **CUDA: OCP FP8** — #28413 adds OCP FP8 matmul with W8A16 dispatch in the GEMV case ([#28413](https://github.com/ggml-org/llama.cpp/pull/28413)); pairs with b12284.
- **FP8 KV-cache (core)** — #28392 adds FP8 KV cache plus scaled-backend tests ([#28392](https://github.com/ggml-org/llama.cpp/pull/28392)).
- **Vulkan**
  - `PAD_REFLECT_1D` webgpu op support added (b12292).
  - CM2 dequantized-KV optimization enabled behind a heuristic on query_tokens×GQA ratio (b12291, [#31526](https://github.com/ggml-org/llama.cpp/pull/31526)).
  - Device-fault vendor binary buffer allocation (b12290, [#32008](https://github.com/ggml-org/llama.cpp/pull/32008)).
  - CoopMat2 GDN (Gated Delta Net) implementation in review, ~40% average speedup on the workgroup-scope path ([#32111](https://github.com/ggml-org/llama.cpp/pull/32111)).
- **CUDA BF16 MMVF** — MMVF path for BF16 matrices with few rows at small batch sizes, targeting Qwen hyper-connection projections ([#31959](https://github.com/ggml-org/llama.cpp/pull/31959)).
- **New architectures in review**
  - TML Inkling (with banded FA kernel, int64 index fixes for large MoEs) ([#25731](https://github.com/ggml-org/llama.cpp/pull/25731)).
  - Ouro (ByteDance looped LM, 1.4B/2.6B) ([#29823](https://github.com/ggml-org/llama.cpp/pull/29823)).
- **OpenCL** — decomposed FA for Adreno ([#32046](https://github.com/ggml-org/llama.cpp/pull/32046)); Q4_0 non-MoE GEMM bin kernel for Adreno X1 ([#30947](https://github.com/ggml-org/llama.cpp/pull/30947)); on-disk FA program cache ([#31484](https://github.com/ggml-org/llama.cpp/pull/31484)).
- **Backend parity** — YaRN truncation config propagated to CANN, ET, Hexagon, Metal, OpenCL, virtgpu ([#32085](https://github.com/ggml-org/llama.cpp/pull/32085)).
- **SYCL** — `CLAMP` fixed to support strided views (b12283).
- **HIP** — GCN MMQ config retune in review ([#30021](https://github.com/ggml-org/llama.cpp/pull/30021)).

---

## Performance & Optimization

- **CUDA/HIP native quantized + BF16 KV in `flash_attn_tile`** — q4_0/q4_1/q5_0/q5_1/q8_0/bf16 combinable K/V, dequantized at tile load ([#31234](https://github.com/ggml-org/llama.cpp/pull/31234)).
- **Layer-first prefill** — reorders execution so all tokens of a layer run before advancing, halving expert re-uploads per ubatch for MoE with `-cmoe`; author reports ~2× prefill on QFN workloads ([#32082](https://github.com/ggml-org/llama.cpp/pull/32082)).
- **Vulkan CM2 dequantized KV** — gated by query_tokens×GQA heuristic to avoid regressing small-batch decode (b12291).
- **CoopMat2 GDN** — ~40% avg speedup over scalar path (Vulkan) ([#32111](https://github.com/ggml-org/llama.cpp/pull/32111)).
- **Graph metadata host memory** — avoid touching unused metadata pages; meaningful savings on MTP/DFlash2 workflows combined with #31274 ([#32043](https://github.com/ggml-org/llama.cpp/pull/32043)).
- **CUDA BF16 MMVF small-batch** — fills idle SMs on few-row BF16 matrices (b12287, [#31959](https://github.com/ggml-org/llama.cpp/pull/31959)).
- **Server I/O** — fix connection reuse after streamed responses; previously the final chunk marked the send as failed, closing reusable connections ([#30492](https://github.com/ggml-org/llama.cpp/pull/30492)).

---

## Stability & Regressions

Ranked by severity:

1. **gfx1201 head-size-256 WMMA FA dispatch threshold** — ~20% speculative-decoding regression since #28102 on Radeon ([#28867](https://github.com/ggml-org/llama.cpp/issues/28867)). Open; likely a one-line threshold fix.
2. **Speculative decoding divergence on quantized targets** — greedy output differs from vanilla decode with draft-mtp/DSpark over Q4_K_M targets, but matches on BF16 ([#25618](https://github.com/ggml-org/llama.cpp/issues/25618)). 42 comments; correctness-critical if you ship spec-decode with quantized models.
3. **CPU F16 accumulation overflows to inf/NaN** — one-chunk CPU flash-attention and ARM F16 dot-product paths use F16 accumulators ([#29774](https://github.com/ggml-org/llama.cpp/issues/29774), [#31734](https://github.com/ggml-org/llama.cpp/issues/31734)). Both closed on 2026-11-11 — verify against your build.
4. **CUDA illegal memory access** — MTP draft right after KV rollback/clear on Gemma 4 31B ([#31077](https://github.com/ggml-org/llama.cpp/issues/31077)). Closed; reproduce before assuming fixed in your build.
5. **ggml scheduler crash** — `ggml_backend_sched_alloc_splits: unexpected graph reallocation` on Intel Arc ([#28753](https://github.com/ggml-org/llama.cpp/issues/28753)). Open.
6. **HIP/gfx906 MoE prompt processing 15–50% slower** since #29948 ([#31599](https://github.com/ggml-org/llama.cpp/issues/31599)). Open.
7. **HIP gfx1200 `MUL_MAT` tolerance failures** (F16, deterministic) ([#31111](https://github.com/ggml-org/llama.cpp/issues/31111)). Open; potential silent correctness issue on RDNA4.
8. **RPC backend warmup crash** at first decode with large MoE across GB10/DGX Spark nodes ([#30052](https://github.com/ggml-org/llama.cpp/issues/30052)). Open.
9. **SYCL DFlash2 draft TDR** on dual Arc Pro B70 (Windows driver reset) ([#28778](https://github.com/ggml-org/llama.cpp/issues/28778)). Closed as stale; no clear root cause in the log.
10. **Vulkan CHAT/SWA crash** — `GGML_ABORT cache_k_l0` regression 2.40/2.41 vs 2.39 ([#29082](https://github.com/ggml-org/llama.cpp/issues/29082)). Closed.
11. **llama-server OOM** ("bad allocation") in long conversations, HIP ([#30091](https://github.com/ggml-org/llama.cpp/issues/30091)). Open.
12. **Minor / tooling** — `auto_fgdn` hardcoded false so fused GDN probe never runs ([#31987](https://github.com/ggml-org/llama.cpp/issues/31987), closed); router memory measurement omits draft model ([#31351](https://github.com/ggml-org/llama.cpp/issues/31351)); server doesn't validate Content-Type ([#31979](https://github.com/ggml-org/llama.cpp/issues/31979), closed); broken security-policy link ([#31972](https://github.com/ggml-org/llama.cpp/issues/31972)); chat tool-list visible but "zero tools" ([#29727](https://github.com/ggml-org/llama.cpp/issues/29727)).

---

## What This Means for Application Developers

- **Quantized KV + FP8 paths are becoming first-class.** With FP8 KV-cache in core (b12274) and native quantized KV in CUDA/HIP FA tiles (#31234) and Vulkan CM2 (b12291), you can now request aggressive KV compression on most production backends. Budget time for accuracy regression tests: the spec-decode divergence report (#25618) shows quantized targets can drift even under greedy sampling, so validate draft models against quantized targets explicitly.
- **MoE serving on PCIe hosts gets materially faster.** Layer-first prefill (#32082) and graph-metadata page savings (#32043) directly reduce host-side overhead for multi-GPU/SysMem setups. If you run `-cmoe` on consumer GPUs, benchmark this combination before your next capacity plan.
- **Server streaming reliability improved.** #30492 fixes connections being closed after streamed responses — relevant for clients behind keep-alive HTTP pools.
- **OpenAI-compatibility surface is tightening.** JSON-schema `response_format` now honored on Muse Spark (b12282); Content-Type validation is under discussion (#31979). Harden client headers accordingly.
- **Multimodal integrators** must update to the renamed `mtmd_format` API (b12277).
- **Track the regression list before upgrading** — particularly the gfx1201 FA threshold (#28867) if you use spec-decode on RDNA3, and the gfx1200 MUL_MAT tolerance failures (#31111) if you produce output-quality-sensitive workloads on RDNA4.

</details>

<details>
<summary><strong>Ollama</strong> — <a href="https://github.com/ollama/ollama">ollama/ollama</a></summary>

# Ollama Digest — 2026-11-12

## Today's Highlights
No new releases shipped in the last 24h, but infrastructure work is moving on the serving path: a PR to co-host the Claude Desktop gateway on Ollama's existing HTTP listener (#19657), a VRAM accounting/GPU-splitting fix bundle targeting four linked issues (#19632), and per-request speculative decoding metrics for API responses (#19368). On the stability side, Vulkan remains a sore spot for AMD hardware, with an open access-violation crash (#18557) alongside a closed 780M regression (#17748).

## Releases & Breaking Changes
None in the last 24h. Watch item for near-term migration: PR [#18882](https://github.com/ollama/ollama/pull/18882) (open) proposes removing llama.cpp compatibility patches and migrating legacy GGUFs on load — this will affect anyone with long-lived local model blobs.

## New Model & Hardware Support
- **Windows iGPU fix for AMD Strix Halo** — PR [#19636](https://github.com/ollama/ollama/pull/19636) stops pinning weights to host memory on Windows iGPUs, addressing failures loading models larger than ~half of system RAM on ROCm.
- **Hybrid model VRAM prediction** — PR [#19609](https://github.com/ollama/ollama/pull/19609) (closed) corrects VRAM prediction for hybrid SSM+transformer architectures and quantized KV cache, fixing overestimation for non-full-attention blocks.
- **Gemma3 LoRA merge question** — Issue [#12788](https://github.com/ollama/ollama/issues/12788) (open, updated) asks whether base safetensors + LoRA adapters can be merged into an Ollama model; no maintainer answer yet.
- **Tool-call parser gap for mimo-v2.6** — Issue [#19650](https://github.com/ollama/ollama/issues/19650): `mimo-v2.6:9b` (MLX runner, `parser=qwen3.5`) emits JSON args inside `<function=...>` and gets `arguments: {}` back.

## Performance & Optimization
- **Speculative decoding observability** — PR [#19368](https://github.com/ollama/ollama/pull/19368) reports drafted/accepted token counts in API responses and `ollama run --verbose`, moving beyond the misleading acceptance-rate-only number.
- **VRAM accounting & GPU splitting** — PR [#19632](https://github.com/ollama/ollama/pull/19632) fixes incorrect evictions, load refusals, and unnecessary multi-GPU spreading (issues #19463, #18679, #18349, #16599).
- **Embed throughput** — PR [#18397](https://github.com/ollama/ollama/pull/18397) removes `DisableKeepAlives: true` on the llama-server HTTP client, eliminating a `/health` + `/v1/embeddings` round-trip pair per embedding input under sustained `/api/embed` load.
- **Windows GPU discovery** — PR [#19080](https://github.com/ollama/ollama/pull/19080) suppresses flashing console windows from discovery subprocesses; PR [#19659](https://github.com/ollama/ollama/pull/19659) adds a GPU discovery timeout to avoid hangs.
- **Embedding input ordering** — PR [#19653](https://github.com/ollama/ollama/pull/19653) fixes nondeterministic image/audio ordering in multimodal `/api/embed` inputs caused by Go map iteration.

## Stability & Regressions
Ranked by severity:

1. **[HIGH] Vulkan access violation on AMD RX 6800 XT** — Issue [#18557](https://github.com/ollama/ollama/issues/18557) (open, needs more info): any model crashes `llama-server` with `0xc0000005` on the Vulkan backend; CPU works. Possibly same root cause as #18494. No fix PR linked.
2. **[MEDIUM] AMD Radeon 780M Vulkan regression (≥0.32.10)** — Issue [#17748](https://github.com/ollama/ollama/issues/17748) (closed): `ErrorDeviceLost` / "Not enough memory for command submission" on larger models; resolved in-thread but users on affected versions should upgrade.
3. **[MEDIUM] macOS updater loop demanding admin rights** — Issue [#19472](https://github.com/ollama/ollama/issues/19472) (open): installed 0.40.13 still prompts "Restart to update" and requests ownership change of `/Applications/Ollama.app`.
4. **[LOW] `--version` prints spurious warnings** — Issue [#19613](https://github.com/ollama/ollama/issues/19613) (closed): `-v`/`--version` emitted connection warnings; scripted version checks should be re-validated after upgrade.
5. **[LOW] Manifest/tag digest confusion** — Issue [#19518](https://github.com/ollama/ollama/issues/19518) (closed): re-pulled tags can silently point to new manifests; pin by digest, not tag, in production.

## What This Means for Application Developers
- **Pin model versions by digest** (#19518). Tag updates propagate silently; if you depend on reproducible behavior (especially with tool-call parsers like `qwen3.5`) record and lock digests in your config.
- **Speculative decoding will become observable** (#19368). Plan to consume drafted/accepted token fields to tune draft depth rather than relying on acceptance rate, which is misleading in isolation.
- **Claude Desktop / ChatGPT launch integrations are converging on the main server** (#19657, #19656, #19658, #19660). If you build multi-client routing, expect a single listener at `/api/claude` with shared model catalogs between CLI and app — avoid inventing your own gateway shim in the interim.
- **GPU-client behavior on Windows iGPUs and AMD Vulkan is still uneven** (#19636, #18557). If your users run on Strix Halo or RX 6000-series, offer a CPU or ROCm fallback path and detect crashes by exit code `0xc0000005`.
- **Embedding endpoints are getting throughput and determinism fixes** (#18397, #19653). Multimodal embedding consumers should stop relying on input ordering within a single item once the fix lands; until then, send image and audio in separate requests if order matters.

</details>

<details>
<summary><strong>LiteLLM</strong> — <a href="https://github.com/BerriAI/litellm">BerriAI/litellm</a></summary>

# LiteLLM Digest — 2026-11-12

## 1. Today's Highlights
A busy release day: v1.109.0 is out and v1.110.0-rc.1 is in flight, with a security backport (#51254) being cherry-picked onto both the 1.109 RC and 1.110 RC lines. Spend-tracking correctness remains the dominant theme — the long-running "spend is $0 when `custom_llm_provider` / `base_model` pricing doesn't match the deployment" class of bugs saw one issue closed (#47675) and a direct fix PR opened (#51267). Meanwhile the v3 TPM rate limiter and MCP gateway are showing regressions on recent upgrades.

## 2. Releases & Breaking Changes
- **v1.109.0** released (stable). All images (`litellm`, `litellm-database`, `litellm-non_root`, `docker.io/litellm/*`) are cosign-signed with the key from commit `e82a3c6`; verify before deploying. https://github.com/BerriAI/litellm/releases
- **v1.110.0-rc.1** published for testing. https://github.com/BerriAI/litellm/releases
- **v1.106.8** patch release (same cosign key). https://github.com/BerriAI/litellm/releases
- **Upgrade caution:** v1.108.0 introduced MCP `tools/list` latency regressions for legacy-protocol clients (see §5). If you run MCP behind the gateway with pre-2026 SDK clients (e.g., Open WebUI), benchmark before upgrading.
- **LTS maintenance:** 1.108.x line is receiving backports (Responses/Baseten fixes #51266, Vercel AI Gateway decision models #51250). Security fix #51254 backported to rc/1.109.0 (#51264) and rc/1.110.0 (#51263).
- **Repo restructuring in progress:** PRs #51006 and #51000 move `scripts/`, `backend/`, `migrations/`, `schema.prisma`, and workspace packages under `litellm/`. No runtime API change expected, but Docker build contexts and CI paths may shift — watch for breaking changes in self-hosted build pipelines.

## 3. New Model & Hardware Support
- **Outer Labs** added as an OpenAI-compatible provider (Claude + OpenAI models at fixed rates below list price), with per-request cost mapping. https://github.com/BerriAI/litellm/pull/51253
- **Qoder Cloud Agents** added to the Interactions API (async, streaming, session continuation); exposable via model alias with server-side credentials. https://github.com/BerriAI/litellm/pull/50041
- **Vercel AI Gateway decision models** backported to 1.108 LTS — `vercel_ai_gateway/` decision calls now route correctly and bill at Vercel's charged rate including cache hits. https://github.com/BerriAI/litellm/pull/51250
- **Databricks `claude-fable-5-2`** repriced from Databricks' proprietary pricing page (was inheriting Bedrock-inherited prices with incorrect `cache_read`). https://github.com/BerriAI/litellm/pull/51232
- **RealmLabs guardrail** (request/response screening, PII masking) proposed. https://github.com/BerriAI/litellm/pull/44102

## 4. Performance & Optimization
- **MCP `tools/list` latency regression (severe):** 18–50 s stalls on v1.108.0 for clients sending `notifications/initialized` with pre-2026 protocol versions (Python `mcp` SDK `ClientSession`, used by Open WebUI), caused by the legacy subscription listener. No fix PR linked yet. https://github.com/BerriAI/litellm/issues/50065
- **Streaming prompt_tokens inflation:** closed issue #12970 (reported prompt_tokens significantly higher than provider billing on streaming responses) — relevant for cost dashboards and budget enforcement. https://github.com/BerriAI/litellm/issues/12970
- **Arize Phoenix double-pricing:** legacy callback path exports duplicate LLM spans (`litellm_proxy_request` parent + `litellm_request` child), causing Phoenix to price every call twice. Workaround: enable `LITELLM_OTEL_V2`. https://github.com/BerriAI/litellm/issues/50649

## 5. Stability & Regressions
Ranked by severity:

1. **v3 rate limiter TPM counter leak (high):** With `LITELLM_TPM_TOKEN_RESERVATION_ENABLED=true` (default), keys/teams with both `rpm_limit` and `tpm_limit` get 429 `Limit type: tokens` well below their TPM limit under upstream 429s — the token counter outlives its window. Affects throughput for rate-limited production tenants. https://github.com/BerriAI/litellm/issues/49675
2. **End-user budget bypass via stale Redis counter (high):** After a clean-miss on `spend:end_user:{user_id}` (60 s TTL), fallback to stale per-pod spend can let a user exceed budget. Related: `/customer/delete` doesn't evict `spend:end_user:<id>`, so re-created users stay over budget (closed with fix). https://github.com/BerriAI/litellm/issues/34238 · https://github.com/BerriAI/litellm/issues/50908
3. **Anthropic `/v1/messages` reasoning stream corruption (high for Claude Code users):** `thinking_delta` emitted inside a text block plus duplicate `message_start` → empty content in Anthropic SDK/Claude Code when fronting reasoning models (GLM/DeepSeek-R1 on vLLM/SGLang). https://github.com/BerriAI/litellm/issues/32357
4. **MCP JWT signer clobbers static auth (medium):** `mcp_jwt_signer` guardrail overwrites a server's configured `auth_value` (bearer_token/basic/etc.), causing 401s on keyed MCP servers. https://github.com/BerriAI/litellm/issues/47988
5. **MCP multi-server aggregation broken (medium):** `resources/read` fails with "Multiple MCP servers configured" when an MCP-Apps UI server is aggregated with any other MCP server. https://github.com/BerriAI/litellm/issues/34906
6. **`cost_per_token` crash on None/string token counts (medium):** provider responses omitting token fields crash spend tracking instead of costing zero. https://github.com/BerriAI/litellm/issues/39618
7. **Anthropic pass-through ignores `model_list` custom prices (medium, fix PR open):** `/anthropic/v1/messages` billed at $0 for models missing from the cost map; #51267 fixes this. https://github.com/BerriAI/litellm/pull/51267
8. **OTel spans missing `gen_ai.usage` on timeout (low-medium):** if the backend finished prefill before LiteLLM timed out, tokens go unrecorded in telemetry. https://github.com/BerriAI/litellm/issues/51221
9. **Misc:** Responses bridge keeps only last reasoning item with `stream=False` (#43620); A2A `message/send` re-discovers agent cards instead of using `agent_card_params` (#40586); A2A agents return false `team_model_access_denied` via `/v1/chat/completions` (#50679); guardrail write-back collapses Anthropic `document` blocks to `image` on `/v1/messages` (#36524); unknown guardrail names silently accepted on key/team/user/project writes (fix PR #51080).

## 6. What This Means for Application Developers
- **Pin versions deliberately.** If you depend on MCP (any agent framework using the Python `mcp` SDK), stay on 1.107.x or wait for the `tools/list` regression fix; 1.108.0 can add tens of seconds to tool discovery. If you run Claude Code against a reasoning backend via `/v1/messages`, avoid the affected versions until #32357 lands.
- **Audit budget math before trusting it.** The 429-under-TPM and stale end-user spend bugs mean rate/budget enforcement is not airtight on v1.10x; for hard spend guarantees, add a provider-side cap and monitor `usage.cost` rather than relying solely on LiteLLM counters.
- **Check your spend accounting after upgrading.** Multiple fixes are landing for $0-spend edge cases (custom `base_model` + differing `custom_llm_provider`, Anthropic pass-through ignoring `model_list` prices). If your dashboards showed $0 for proxied models, re-validate pricing config; #51267 is the PR to track.
- **Useful frontend availability:** Anthropic `/v1/messages` endpoint continues to mature as a Claude-compatible façade over OpenAI-compatible backends — useful if your app is built on the Anthropic SDK but your inference fleet is OpenAI-shaped.
- **Observability:** If using Arize Phoenix, set `LITELLM_OTEL_V2` to avoid double-billed traces; if relying on OTel for token accounting, note that timed-out requests may lack `gen_ai.usage` attributes.

</details>

<details>
<summary><strong>Unsloth</strong> — <a href="https://github.com/unslothai/unsloth">unslothai/unsloth</a></summary>

# Unsloth Digest — 2026-11-12

## Today's Highlights
Unsloth shipped **v0.1.917-beta**, introducing **Dynamic v3.1 GGUFs** and local support for **Qwen3.8-27B**, alongside a substantial Studio UI overhaul (Glass rendering, revamped code blocks, Thinking UI, HTML/PDF chat exports, image generation and coding agent features). The issue tracker shows heavy activity around a recurring theme: **context-window handling and prompt sizing on self-hosted/custom connections** — many PRs today fix Studio bugs where prompts or tool results were sized to a fixed budget (16K/32K) regardless of the server's actual context. Several fixes are also landing for multi-provider correctness (Mistral, DeepSeek, Claude Code) and tool-call flows.

---

## Releases & Breaking Changes
- **v0.1.917-beta** — [repo](https://github.com/unslothai/unsloth): Dynamic v3.1 GGUFs; Qwen3.8-27B local inference; performance fixes; new Studio UI (Glass rendering, Thinking UI, code blocks), HTML/PDF chat exports, image generation, coding agents. No explicit breaking changes or migration notes published in the release snippet.

---

## New Model & Hardware Support
- **Qwen3.8-27B** with **Dynamic v3.1 GGUFs** — first-class local support via the new beta release. [Release](https://github.com/unslothai/unsloth/releases)
- **SYCL / Intel GPU support request** — [#17048](https://github.com/unslothai/unsloth/issues/17048): users want the ability to source `setvars.sh` before launching `llama-server` to unlock SYCL builds (substantial perf gain on Intel GPUs). Open feature request.
- **ROCm 7.14 / MI300X (gfx942)** — [#17548](https://github.com/unslothai/unsloth/issues/17548): stride assertion in `_scaled_dot_product_flash_attention_backward` forces MATH fallback (perf regression on AMD). Open.
- **Kromoss (MOSS-Image derivative)** — [#17552](https://github.com/unslothai/unsloth/issues/17552): GGUF quants rejected as unsupported architecture in Studio. Open.
- **DiffusionGemma** — [#17558](https://github.com/unslothai/unsloth/issues/17558): loads but ignores selected context (`--maxtok 4096` forced) and parallelism settings.

---

## Performance & Optimization
- **Dynamic v3.1 GGUFs** (v0.1.917-beta) — new quantization/format pipeline; release notes cite "major performance improvements" and smoother Glass rendering. [Release](https://github.com/unslothai/unsloth/releases)
- **Studio long-chat virtualization** — [#17504](https://github.com/unslothai/unsloth/pull/17504): `main` currently mounts every message, making every interaction cost scale with chat length (all on the web process main thread). PR introduces windowing near the reader.
- **Context-window–aware prompt sizing** (multiple PRs): 
  - Tool results fitted to server window — [#17553](https://github.com/unslothai/unsloth/pull/17553)
  - Deep Research prompts sized to served window — [#17554](https://github.com/unslothai/unsloth/pull/17554)
  - These fix the `request (N tokens) exceeds the available context size` class of failures (e.g., 5880 vs 4096 on llama.cpp).
- **Image generation cancellation latency** — [#17559](https://github.com/unslothai/unsloth/pull/17559): denoise loop now stays one step ahead of GPU, so Stop/eject takes effect within ~1 step instead of after full generation.

---

## Stability & Regressions (ranked by severity)

**High**
- **ROCm MI300X SDPA backward stride assertion** — [#17548](https://github.com/unslothai/unsloth/issues/17548): training/fine-tuning on gfx942 requires MATH fallback; correctness preserved but severe perf hit. No fix PR yet.
- **Pipeline-parallel MoE router aux loss divergence** (CPU backend, `UNSLOTH_FORCE_CPU=1` + grad accumulation > 1) — [#17054](https://github.com/unslothai/unsloth/issues/17054), **CLOSED**. Repo parity test reproduces the mismatch. Silent training-correctness issue — verify if you run distributed CPU MoE training.
- **Repeated full prompt reprocessing across turns with Qwen hybrid model after tool use** — [#17015](https://github.com/unslothai/unsloth/issues/17015): Studio + 65K context + MCP catalog; suspected interaction with `ToolLoopController` deferred-schema prefix preservation (#165xx). Open.

**Medium**
- **Safetensors fallback formatter strips angle-bracket text** — [#17562](https://github.com/unslothai/unsloth/pull/17562): user turns containing `std::vector<int>` or `<`/`>` comparisons are mangled (`re.sub(r"<[^>]+>", "", ...)`). Fix PR open.
- **Mistral reasoning models return internal error** — [#17542](https://github.com/unslothai/unsloth/pull/17542): thinking returned as parts-list breaks parsing. Fix PR open.
- **DeepSeek tool-call chats terminate** — [#17556](https://github.com/unslothai/unsloth/pull/17556): `reasoning_content` not passed back on tool continuation. Fix PR open.
- **Claude Code `MAX_THINKING_TOKENS=0` ignored on `/v1/messages`** — [#17557](https://github.com/unslothai/unsloth/pull/17557): Qwen3/GLM templates still reason. Fix PR open.
- **Tool-returned images break text-only connections** — [#17561](https://github.com/unslothai/unsloth/pull/17561): "Multimodal data provided, but model does not support…" Fix PR open.
- **Web fetch returning empty/garbage on JS-shell pages** — [#17545](https://github.com/unslothai/unsloth/pull/17545) (noscript content) and [#17543](https://github.com/unslothai/unsloth/pull/17543) (docs pages dominated by >1MB menus). Fixes open.

**Low / UX**
- **Python file deletion bypasses approval** — [#17541](https://github.com/unslothai/unsloth/pull/17541): `os.remove` via `map`, `functools.partial`, `Thread`, `executor.submit` skips the Allow/Deny card in "Approve for me" mode. **Security-relevant**; fix PR open.
- **MiniMax-H3-Image** — [#17563](https://github.com/unslothai/unsloth/pull/17563): wrong download selection (135 GB on 24–40 GB GPUs), FP8/INT8 and 16 MP sizes refused. Fix PR open.
- **Terminal tool output lost on timeout** — [#17555](https://github.com/unslothai/unsloth/pull/17555): streaming output not surfaced before the 300s cutoff. Fix PR open.
- **Eject control disappearing during audio tasks** — [#17564](https://github.com/unslothai/unsloth/pull/17564). Fix open.
- **Docker/ROCm tag confusion** — [#17470](https://github.com/unslothai/unsloth/issues/17470): tags don't map to releases. Open.

---

## What This Means for Application Developers
- **Pin to v0.1.916 if you depend on GGUF stability.** v0.1.917-beta introduces a new "Dynamic v3.1" GGUF format — validate your quantized artifacts against the new loader before upgrading production agents.
- **Context budgets are now server-relative, not fixed.** If you build on custom/Ollama/vLLM/llama.cpp connections, expect tool results and Deep Research prompts to be trimmed to the *served* window (PRs #17553, #17554). If your app depended on the previous 16K/32K defaults, audit token accounting.
- **Tool-call loops are the active regression surface.** Bugs #17015 (Qwen hybrid prefix reprocessing), #17556 (DeepSeek `reasoning_content`), and #17561 (tool-returned images on text-only models) all affect multi-turn agent reliability. If your agents use MCP catalogs or multimodal tools, test on the beta channel; fixes exist for two of three.
- **Thinking-mode controls are not universally honored yet.** Claude Code's `MAX_THINKING_TOKENS=0` is only addressed by open PR #17557 — do not assume the flag disables reasoning for Qwen3/GLM-class templates until merged.
- **Harden Python tool execution.** Approval bypass (#17541) means "Approve for me" is not a security boundary for indirect function calls (`map`, `partial`, thread targets, `executor.submit`). Treat the sandbox as untrusted.
- **AMD MI300X users:** fine-tuning via SDPA backward is degraded to MATH fallback (#17548). Budget accordingly, or watch for a fix.
- **Web-fetch agents:** expect noise on JS-shell (Discourse, Gemini docs) and large-menu docs sites until #17543/#17545 land; add post-processing or a fallback extraction path.

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar).*