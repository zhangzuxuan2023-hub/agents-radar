# AI Infrastructure Digest 2026-11-07

> Generated: 2026-11-06 23:16 UTC | Projects covered: 6

- [vLLM](https://github.com/vllm-project/vllm)
- [SGLang](https://github.com/sgl-project/sglang)
- [llama.cpp](https://github.com/ggml-org/llama.cpp)
- [Ollama](https://github.com/ollama/ollama)
- [LiteLLM](https://github.com/BerriAI/litellm)
- [Unsloth](https://github.com/unslothai/unsloth)

---

## Cross-Project Comparison

# Cross-Project Comparison Report — AI Infrastructure Digest, 2026-11-07

## 1. Ecosystem Overview

The ecosystem is mid-cycle on a **Kimi-K3.1 and DeepSeek-V4 bring-up wave**: serving engines (vLLM, SGLang) and the local runtime layer (llama.cpp) are all landing or reviewing Day-0 support for the same architectures within the same 24-hour window, which is now the de facto competitive battleground. Correctness, not throughput, is the dominant theme today — vLLM has multiple open silent-KV-corruption and long-context crash regressions, SGLang has FP8 KV scales being silently ignored, and llama.cpp's spec-decode diverges on quantized targets. The gateway layer (LiteLLM) is in LTS backport mode, absorbing a steady stream of Anthropic↔OpenAI translation fidelity bugs, while the fine-tuning/desktop layer (Unsloth) is shipping a performance-focused beta but with RAG and multi-GPU diffusion pipelines still immature.

## 2. Activity Comparison

| Project | Issues surfaced (24h) | PRs surfaced (24h) | Release status | Signal |
|---|---|---|---|---|
| **vLLM** | ~15 (8 open regressions ranked) | ~12 (incl. 5-part Kimi stack) | No release; v0.32.0 has open correctness regressions | Highest-severity bug load; heavy model bring-up |
| **SGLang** | ~10 (5 new stability items) | ~15 (500 updated, ~10 detailed) | No release | Large PR queue; Day-0 Kimi work dominates |
| **llama.cpp** | ~14 open regressions | ~15 (10+ builds b12167–b12179) | **10+ builds landed** | Highest build cadence; broadest platform coverage |
| **Ollama** | ~10 | ~10 (incl. upstream bump to b12177) | No release; 0.40.x RC churn | Runtime stabilization on new llama-server base |
| **LiteLLM** | ~12 | ~12 (LTS cherry-picks + 4 fix PRs) | **v1.108.1 + v1.108.2 LTS** | Production branch converging on 1.108 LTS |
| **Unsloth** | ~10 | ~15 (Studio/Desktop heavy) | **v0.1.916-beta** | Feature velocity high; stability debt accumulating |

**Read:** vLLM carries the most *severe* open regressions; llama.cpp has the highest shipping cadence; LiteLLM is the only project actively publishing patch releases, consistent with gateway being a "boring, must-be-correct" layer; Unsloth is feature-first and stability-second right now.

## 3. Model Support Race

**Kimi-K3.1 is the consensus target and vLLM + SGLang are effectively tied at the front, with llama.cpp a close third on narrower scope.**

- **vLLM**: 5-part stacked PR (#65685/#66037/#66027) covering TP8 NVIDIA decode, ROCm, P/D disaggregation, sparse-MLA DCP. Split 1/5 merged; splits 3–5 in review. Broader hardware/feature surface than any competitor.
- **SGLang**: Single consolidated Day-0 PR #48414 bundling DSA + DCP, PD disaggregation, DFlash speculative decoding, PP prefill, HiSparse, fused kernels. Single-PR packaging risks a harder merge but a cleaner landing.
- **llama.cpp**: Kimi-K3.1-Pro **merged** (#31779) — DSA/MLA hybrid, top-k mask reuse. Merged *before* either serving engine completed its stack, but on the narrower (text-only, single-node) local-runtime surface.
- **Ollama**: Tracing upstream (bump to b12177), not driving Kimi support itself.
- **LiteLLM**: No model bring-up, but Gemini catalog sync (#50468) and Vertex Claude partner-model serialization work (#49645) show it tracks model-catalog drift rather than architectures.

**Secondary races:** DeepSeek-V4 pipeline parallelism (vLLM #65766 vs SGLang's prefill/verify kernel work) — vLLM is ahead on *structural* PP support; SGLang is ahead on *kernel-level* V4 optimization. NVFP4 is a two-horse race: vLLM #63203 (NVFP4 KV cache for MLA, ~39% per-token reduction) is far ahead of llama.cpp's closed NVFP4 PR (#23572, 40% claimed uplift, not landed).

**Verdict:** vLLM leads on breadth (multi-hardware, P/D, KV dtype), SGLang leads on single-PR feature completeness, llama.cpp leads on *time-to-merge* for text-only Kimi.

## 4. Performance Frontier

Optimization effort is concentrated in four bands, unevenly distributed:

1. **KV cache compaction** — The most structural work. vLLM's NVFP4 MLA KV (352 vs 576 B/token) is the headline; SGLang's FP8 KV *correctness* bug (#48749) is the cautionary counterpoint — quantization is being pushed for footprint while scale-application correctness is still breaking. llama.cpp's ClassLRU MoE expert cache (#31233) attacks the same "fit more in less memory" problem at the runtime layer.
2. **Distributed serving / P/D & PP** — vLLM leads: DeepSeek-V4 SP+PP for MegaMoE, sparse MLA DCP, MoE DP padding skip. But it also owns the corresponding risk: NixlConnector silent KV corruption (#61229) and the GLM-5.3 P/D descriptor-submission bottleneck (#55434) — **descriptor submission, not fabric bandwidth**, is emerging as the real P/D scaling limit.
3. **Kernel fusion** — SGLang is most aggressive (Gemma-4 TP all-reduce fused with epilogue, Anima fused residuals removing 8,400 launches per request, DeepSeek-V4 compact verify). vLLM answers with Kimi K3 single packed prefill convolution and ROCm FP8 MFMA. Kernel-level gains are now often *launch-count* reductions, not FLOPs.
4. **Quantization strategy** — Moving from blanket to per-tensor: Ollama #18078 explicitly walks back blanket NVFP4 for long-generation quality; LiteLLM's token-counting #48830 improves budget-reservation accuracy, an underdiscussed cost dimension.

**Cross-cutting signal:** batch-invariance and reproducibility are rising as first-class concerns (vLLM Marlin MoE ordering #52525, batch-invariant mode over-routing #65569, BYOG RFC #61698) — directly relevant to RL training loops and eval harnesses.

## 5. Layer Positioning

| Layer | Projects | Characteristic failure modes today |
|---|---|---|
| **Serving engine (datacenter)** | vLLM, SGLang | Silent KV corruption, P/D correctness, long-context crashes; high blast radius |
| **Local runtime (edge/desktop)** | llama.cpp, Ollama | Platform-specific crashes (Vulkan/Metal/SYCL), spec-decode divergence, RAM-cache cross-tenant bleed |
| **Gateway / proxy** | LiteLLM | Translation fidelity, billing integrity, cache poisoning; correctness bugs are *financial* or *multi-tenant* |
| **Training / fine-tuning / Studio** | Unsloth | Silent Trainer behavior changes, batch-1 RAG throughput, multi-GPU diffusion asserts |

The layers have distinct **release philosophies**: serving engines ship fast and regress hard (vLLM v0.32.0 is implicitly a "avoid" version); llama.cpp ships continuously with per-build granularity; LiteLLM runs an LTS branch because gateways must be boring; Unsloth ships beta-tagged features with a stability backfill.

Notably, **the boundaries are blurring**: Unsloth Studio is becoming a local LLM gateway for coding agents (Claude Agent, Codex, Hermes MCP), and Ollama's underlying engine is now llama.cpp's server. The "gateway" function is appearing inside both the training IDE and the local runtime.

## 6. Trend Signals

**What the industry is doing:**
- **Day-0 model support is the product.** Kimi-K3.1 and DeepSeek-V4 support is being raced across all four layers simultaneously — model release cadence now dictates engineering roadmap.
- **Quantization is entering a correctness-debt phase.** FP8 KV scale silence (SGLang), int8 KV corruption (vLLM), per-tensor NVFP4 walkback (Ollama), spec-decode divergence on quantized targets (llama.cpp) — *every* project has a quantization-correctness story open today.
- **P/D disaggregation is where scale breaks.** Descriptor overhead, lease-expiry races, silent KV corruption — all three distinct failure classes are surfacing in disaggregated serving specifically.
- **Agentic workloads are exposing protocol fidelity.** Responses API `previous_response_id` context drops (vLLM), `server_tool_use` mis-surfacing and thinking-signature replay (LiteLLM), `</think>` parser truncation (SGLang), GLM-5.3 multi-tool degeneration (SGLang) — multi-turn tool use is today's toughest correctness surface.
- **Reproducibility is becoming a first-class requirement** for evals and RL, driving batch-invariance work in vLLM.

**What agent/application developers should watch:**
1. **Pin serving versions explicitly.** Treat vLLM v0.32.0 as a known-risk release (Qwen hybrid crashes, hybrid+PP illegal memory); fall back to v0.31.1 for affected workloads.
2. **Validate outputs, not just HTTP 200s.** Silent KV corruption in disaggregated serving is the worst failure mode; add output-level assertions for P/D deployments.
3. **Don't trust spec-decode on quantized weights** for determinism-sensitive pipelines (llama.cpp #25618, Ollama cuda_v13 grammar crashes #17026).
4. **Set `max_tokens` explicitly and treat truncated streams as errors** on Anthropic-backed and Studio routes (Unsloth #16953, #16867).
5. **Disable response caching on `/v1/messages`** and monitor spend independently until LiteLLM budget and cache-poisoning fixes land (#50469, #50057).
6. **Watch the NVFP4 KV cache PR (#63203)** as the biggest single lever for context length / concurrency on MLA-class models — and benchmark per-model before adopting, given quantization's track record this cycle.

**Bottom line:** the infrastructure is being stress-tested by a synchronized model-generation wave, and the systemic risk has shifted from *throughput* to *silent correctness* in disaggregated serving, quantized KV, and agent protocol translation. Engineers planning 2027 capacity should budget for output-validation infrastructure, not just faster kernels.

---

## Per-Project Reports

<details>
<summary><strong>vLLM</strong> — <a href="https://github.com/vllm-project/vllm">vllm-project/vllm</a></summary>

# vLLM Digest — 2026-11-07

## 1. Today's Highlights

No new releases in the last 24h, but issue and PR activity is heavy. The biggest theme is **Kimi K3.1 model bring-up** (a 5-part stacked PR series at various merge stages) alongside continued **DeepSeek-V4 pipeline-parallelism fixes**. A cluster of **correctness regressions in v0.32.0** — Qwen3.8/Qwen4Exp hybrid-model crashes and KV cache corruption under prefix caching — are dominating bug reports, while the **Rust frontend** moves closer to feature parity as an opt-in replacement for the Python API server.

## 2. Releases & Breaking Changes

*No new releases in the last 24h.* One PR worth flagging for config-surface changes:

- **[PR #64765](https://github.com/vllm-project/vllm/pull/64765) — flashinfer/b12x packaging change.** `flashinfer-python` ≥0.7.2 now ships `b12x` as a top-level package, so vLLM drops the standalone `b12x==1.5.1` extra. Minor dependency-surface shift; no migration needed for normal users but verify if you pin the `b12x` extra.

## 3. New Model & Hardware Support

- **[PR #65685](https://github.com/vllm-project/vllm/pull/65685) / [#66037](https://github.com/vllm-project/vllm/pull/66037) / [#66027](https://github.com/vllm-project/vllm/pull/66027) — Kimi K3.1 Pro (5-part stack).** Split 1/5 merged; splits 3–5 in review. Covers NVIDIA specialized decode, KV cache + attention changes for TP8 serving, ROCm path, P/D disaggregation, and sparse MLA DCP (decode context parallelism) optimizations. This is the main model bring-up workstream right now.
- **[PR #63203](https://github.com/vllm-project/vllm/pull/63203) — NVFP4 KV cache for dense MLA.** Adds `--kv-cache-dtype nvfp4` for DeepSeek-V3.x and Kimi-K3 on `FLASHINFER_MLA`: **352 B/token vs 576 B/token for FP8** (512 latent dims NVFP4 + 64 RoPE dims FP8). Uses FlashInfer Cake decode kernel.
- **[PR #65766](https://github.com/vllm-project/vllm/pull/65766) — DeepSeek-V4 sequence parallelism + pipeline parallelism for MegaMoE.** Removes the PP=1 restriction that disabled SP on multi-stage pipelines.
- **[Issue #47455](https://github.com/vllm-project/vllm/issues/47455) — Nemotron 3.5 ASR Streaming (0.6B) feature request.** Open request for `nemotron-3.5-asr-streaming-0.6b`; not yet implemented.

## 4. Performance & Optimization

- **[PR #65972](https://github.com/vllm-project/vllm/pull/65972) — MoE DP padding skip.** Under data parallelism, DP ranks pad to the largest batch; this PR skips padding rows in FlashInfer TRT-LLM monolithic MoE on Blackwell, avoiding wasted expert compute.
- **[PR #64817](https://github.com/vllm-project/vllm/pull/64817) — Kimi K3 single packed prefill convolution.** Replaces three per-view convolutions with one on the packed q|k|v projection; reduces memory materialization before FlashKDA.
- **[PR #66027](https://github.com/vllm-project/vllm/pull/66027) — Sparse MLA DCP optimizations** (packed top-k merge, direct A2A, DCP ShardQ) — opt-in, applied only under decode context parallelism.
- **[PR #65410](https://github.com/vllm-project/vllm/pull/65410) — ROCm Step5 native FP8 MFMA** in prefill indexer scores on gfx942 (MI300X/MI325X).
- **[Issue #63705](https://github.com/vllm-project/vllm/issues/63705) — MiniMax-M2.7 throughput regression** reported across v0.29/0.31/0.32 on TP4+EP vs earlier versions — still open, worth watching if you serve M2.7.
- **[Issue #58624](https://github.com/vllm-project/vllm/issues/58624) — MoE decode ~15% slower on SM12x** since #56876 (DeepGEMM contiguous-layout alignment 64→128). Open regression.
- **[Issue #55434](https://github.com/vllm-project/vllm/issues/55434) — GLM-5.3 P/D on GB200.** NIXL submits **~91k–120k KV descriptors per TP-rank transfer**; descriptor submission (not fabric bandwidth) is the bottleneck, making MNNVL/cuda_ipc slower than RDMA for this workload.
- **[Issue #65569](https://github.com/vllm-project/vllm/issues/65569) — batch-invariant mode on Hopper/Blackwell** routes Linear layers to Triton unnecessarily (cuBLAS already batch-invariant under those settings).

## 5. Stability & Regressions

Ranked by severity:

1. **🔴 KV cache corruption (silent wrong output)** — **[Issue #61229](https://github.com/vllm-project/vllm/issues/61229)** (NixlConnector, 1P1D): KV blocks released on lease expiry while remote READ is in flight; decode consumes overwritten KV. Silent correctness failure in disaggregated serving. No fix PR listed.
2. **🔴 Engine crash at long context** — **[Issue #62383](https://github.com/vllm-project/vllm/issues/62383)** (Qwen4Exp / Qwen3.8-Flash-Next): single prefill crossing the 262,144-token native boundary → CUDA illegal memory access. **v0.32.0 and nightly affected; OK on v0.31.1.**
3. **🔴 Illegal memory access in hybrid+PP** — **[Issue #63505](https://github.com/vllm-project/vllm/issues/63505)** (Qwen3.8-27B GDN hybrid, PP=3, sm_86): Xid 31 with prefix caching, gibberish without it. Regression since v0.28.0, present in v0.31/v0.32.
4. **🟠 TP hang / EngineDeadError** — **[Issue #41530](https://github.com/vllm-project/vllm/issues/41530)** (DeepSeek-V4-Pro, TP=8, MTP speculative decoding): RPC timeout to `sample_tokens` on TP worker. Long-running (since May), still open.
5. **🟠 Spec-decode + structured output 500s** — **[PR #66081](https://github.com/vllm-project/vllm/pull/66081)** proposes a fix: under async scheduling, EngineCore fails to retrieve real draft token IDs before computing the grammar bitmask. **Fix PR exists.**
6. **🟠 int8_per_token_head KV corruption** — **[Issue #50702](https://github.com/vllm-project/vllm/issues/50702)** / **[#50749](https://github.com/vllm-project/vllm/issues/50749)** (Gemma-4 hybrid, Triton): corrupts output with prefix caching on / first tokens with it off. **[Issue #63944](https://github.com/vllm-project/vllm/issues/63944) CLOSED** — scale-view fill overwriting Gated DeltaNet state (fix landed).
7. **🟠 Parser correctness** — **[Issue #43244](https://github.com/vllm-project/vllm/issues/43244)**: `previous_response_id` drops `function_call`/`function_call_output` from stored context in the Responses API — matters for multi-turn tool agents.
8. **🟡 Hybrid + LoRA NaN output** — **[Issue #52568](https://github.com/vllm-project/vllm/issues/52568)** (Qwen3.5-9B GDN + dynamic LoRA, H20, long sequences): Punica Triton kernel bug.
9. **🟡 Marlin MoE batch dependency** — **[Issue #52525](https://github.com/vllm-project/vllm/issues/52525)**: output depends on within-expert token ordering, breaking batch invariance.
10. **🟡 ROCm MTP regression** — **[Issue #52833](https://github.com/vllm-project/vllm/issues/52833) CLOSED** (GLM-5.2 MTP, MI355X/gfx950): 0% draft acceptance.

## 6. What This Means for Application Developers

- **Pin your version deliberately.** v0.32.0 has multiple open correctness regressions (long-context Qwen4Exp crashes, hybrid+PP illegal memory access). If you serve Qwen3.8/4Exp or hybrid-attention DeepSeek-class models, **v0.31.1 is the safer default** until #62383 and #63505 close.
- **Disaggregated serving needs scrutiny.** The NixlConnector lease-expiry bug (#61229) causes *silent* wrong output, not a crash — the worst failure mode. If you run P/D disaggregation, validate outputs end-to-end rather than trusting request success.
- **Agent developers:** the Responses API context bug (#43244) will break multi-turn tool loops that rely on `previous_response_id` for function-call history. Spec-decode + structured outputs (JSON schemas) currently 500s under async scheduling — fix in review (#66081); until merged, consider disabling spec decode for grammar-constrained endpoints.
- **KV cache footprint may drop meaningfully.** NVFP4 KV for MLA models (#63203) is ~39% smaller per token than FP8 — a real lever for context length or concurrency on DeepSeek-V3.x/Kimi serving.
- **The Rust frontend is stabilizing but not done.** Roadmap #44280 tracks parity; `VLLM_USE_RUST_FRONTEND=1` remains experimental. Don't base production API-server architecture on it yet.
- **Watch batch-invariance work.** The batch-invariant config hashing refactor (#39479, closed) and the Marlin MoE ordering bug (#52525) matter if you depend on reproducible outputs across batch sizes (evals, regression testing, RL training loops). The BYOG reproducible-benchmark RFC (#61698) is the community's answer to tightening this.

</details>

<details>
<summary><strong>SGLang</strong> — <a href="https://github.com/sgl-project/sglang">sgl-project/sglang</a></summary>

# SGLang Digest — 2026-11-07

## 1. Today's Highlights
No releases landed in the last 24h, but the PR queue is highly active (500 updated). The most consequential in-flight work is Day-0 support for **Kimi-K3.1** (PR #48414) and a set of DeepSeek-V4 prefill/verify kernel optimizations. On the stability side, a serious correctness issue was filed for FP8 KV-cache scales being silently ignored across several model families (#48749), and a newly registered diffusion test is failing on main (#48892).

## 2. Releases & Breaking Changes
None in the last 24h.

## 3. New Model & Hardware Support
- **Kimi-K3.1** — Day-0 serving stack in review, including DSA with decode context parallelism, PD disaggregation, DFlash speculative decoding, PP prefill, HiSparse, and fused kernels ([#48414](https://github.com/sgl-project/sglang/pull/48414)).
- **MindIE-SD for Ascend** — RFC to integrate MindIE-SD as an optional acceleration library for SGLang Diffusion (dense quantized + sparse attention), positioned similarly to FlashInfer on NVIDIA ([#48582](https://github.com/sgl-project/sglang/issues/48582)).
- **Apple Silicon** — RFC progressing on a Torch-owned SRT path with an exported whole-model MLX region ([#32321](https://github.com/sgl-project/sglang/issues/32321)).
- **NPU / Diffusion** — Wan2.2-T2V-A14B W4A4F4 DAOS with MXFP4 attention in review ([#43367](https://github.com/sgl-project/sglang/pull/43367)).
- **Gemma-4 / Nemotron Bolt E4B** — breakable prefill CUDA graph fix for shared-KV-cache models ([#46663](https://github.com/sgl-project/sglang/pull/46663)).

## 4. Performance & Optimization
- **DeepSeek-V4 compact verify** — routes ratio-1/2 compressors through fused kernels instead of chained small torch kernels ([#48832](https://github.com/sgl-project/sglang/pull/48832)); `wo_b`/Engram KV MXFP8 GEMMs routed to cuDNN on CC 10.7 above 8K rows ([#48971](https://github.com/sgl-project/sglang/pull/48971)).
- **Gemma-4 TP fusion** — all-reduce fused with layer epilogue + per-128 FP8 quant on NVLink multicast; removes an NCCL all-reduce and three epilogue kernels per dense layer at TP4/H200 ([#48563](https://github.com/sgl-project/sglang/pull/48563)).
- **Diffusion (Anima)** — fused gated residuals remove 8,400 kernel launches per 50-step CFG request ([#48394](https://github.com/sgl-project/sglang/pull/48394)).
- **Mooncake L3** — parallelized layer-wise direct-linker loads to address ~12–14 GB/s serialized ranged-op throughput ([#45106](https://github.com/sgl-project/sglang/pull/45106)).
- **Weight cache daemon** — roadmap reports load time dropping from ~306–327s to **<1s** on Qwen3-235B FP8 via CUDA IPC ([#33522](https://github.com/sgl-project/sglang/issues/33522)).
- **AMD/HIP** — strided GDN prefill projection views for Qwen3.5 to match CUDA's zero-copy path ([#41683](https://github.com/sgl-project/sglang/pull/41683)).
- **Ongoing investigation** — DeepSeek-V4 prefill on SM120 measured at ~2–7K tok/s vs ~12.5K tok/s reported via vLLM/marlin on identical hardware ([#33422](https://github.com/sgl-project/sglang/issues/33422)).

## 5. Stability & Regressions (ranked)
1. **FP8 KV-cache scales silently ignored** — attention built without `quant_config` affects GLM-4.5, Llama 4, Qwen3-MoE, GPT-OSS, HunYuan, HY3, K-EXAONE, LFM2. Correctness/accuracy risk, no fix PR linked ([#48749](https://github.com/sgl-project/sglang/issues/48749)).
2. **Reasoning parser truncation** — literal `</think>` quoted inside CoT splits reasoning early, leaking CoT into `content`. Directly impacts agent/reasoning apps ([#48791](https://github.com/sgl-project/sglang/issues/48791)).
3. **Model gateway drops `cache_salt`** on `/v1/chat/completions`, causing cache-isolation behavior divergence vs direct backend ([#48793](https://github.com/sgl-project/sglang/issues/48793)).
4. **Diffusion CI failure on main** — `test_kandinsky6_sr_decode_parallel` tensor mismatch since #48366 merged; test newly registered in GPU cases ([#48892](https://github.com/sgl-project/sglang/issues/48892)).
5. **GLM-5.3-Flash degeneration** — thinking output collapses to repeated `!` under multi-tool agentic prompts; tracked in the GLM bug umbrella ([#36669](https://github.com/sgl-project/sglang/issues/36669), [#37524](https://github.com/sgl-project/sglang/issues/37524)).
6. **CI flakiness** — dedicated tracker reports 1 broken / 4 flaky / 1164 recently fixed ([#42752](https://github.com/sgl-project/sglang/issues/42752), [#17050](https://github.com/sgl-project/sglang/issues/17050)).
7. **Closed today**: PD router open-circuit-breaker misdispatch (#31206), `fast_topk_v2` silent wrong top-k under bucket overflow (#36807), MTP draft acceptance decay over uptime (#37326), MiniMax-M3 SM121 all-NUL output (#38143).

## 6. What This Means for Application Developers
- **Reasoning/tool-use pipelines**: the `</think>` splitting bug (#48791) means parsed `reasoning_content` can be truncated and CoT can leak into user-visible content — validate parsing if your prompts may quote think tags, and watch GLM-5.3-Flash under multi-tool schemas.
- **Gateway deployments**: if you rely on `cache_salt` for tenant/cache isolation, requests through `sgl-model-gateway` currently bypass it (#48793) — route directly or pin to a version with the fix.
- **Quantized FP8 KV-cache users**: checkpoint-provided FP8 scales may not be applied for the listed model families (#48749); benchmark accuracy before trusting FP8 KV on those architectures.
- **Kimi-K3.1 adopters**: the Day-0 stack bundles PD disaggregation, DFlash speculative decoding, and HiSparse — a good moment to plan evaluation once #48414 merges.
- **Long-running replicas**: the weight-cache daemon roadmap (<1s reloads) and the MTP acceptance-decay fix signal improving restart/uptime economics; the open NextN/MTP decay class of issues is worth monitoring in your observability.

</details>

<details>
<summary><strong>llama.cpp</strong> — <a href="https://github.com/ggml-org/llama.cpp">ggml-org/llama.cpp</a></summary>

# llama.cpp Digest — 2026-11-07

## 1. Today's Highlights

A very busy 24-hour window: 10+ builds landed (b12167–b12179), spanning Hexagon CONV_3D support, Vulkan ReBAR detection, a new MoE expert-cache (ClassLRU), and WebGPU batched tensor APIs. On the model side, a Kimi-K3.1-Pro text port was merged and an `mtmd` Mage-VL-Cowork port opened. Stability news is mixed — several speculative-decoding correctness issues remain open, and a batch of Vulkan/SYCL/Metal platform bugs resurfaced.

## 2. Releases & Breaking Changes

- **b12179** — Hexagon: CONV_2D optimizations + CONV_3D support (shares HTP_OP_CONV_2D, new HTP_OP_CONV_3D). Link: https://github.com/ggml-org/llama.cpp/pull/31814
- **b12177** — CLI: fix crash on invalid UTF-8 in prompt or files. Link: https://github.com/ggml-org/llama.cpp/pull/31563
- **b12176** — Tests: qualify `isnan`/`signbit` with `std::` for NetBSD/GCC 14.4 compat.
- **b12175** — CI: override `hardware_concurrency` for containerized CPU runners (fixes test-llama-context table-mode oversubscription).
- **b12174** — CMake: generate RC file directly (drops `win-app.rc.in`).
- **b12173** — OpenCL: `repeat` support for f16/i32/i16/bf16.
- **b12172** — WebGPU: new batched tensor API `get_tensor_n` / `set_tensor_n`. Link: https://github.com/ggml-org/llama.cpp/pull/31473
- **b12171** — Vulkan: detect ReBAR and skip BAR allocations if unavailable. Link: https://github.com/ggml-org/llama.cpp/pull/31658
- **b12170** — `moe-cache`: LRFU/MQ-inspired hybrid expert cache ("ClassLRU"). Link: https://github.com/ggml-org/llama.cpp/pull/31233
- **b12167** — CUDA: fix Maxwell build regression.

No public API or config-breaking changes in this window; the `n_ctx_slot` cap removal (below) is still a PR, not merged.

## 3. New Model & Hardware Support

- **Kimi-K3.1-Pro** text model merged (CLOSED/merge-ready). Reuses K3, replaces full-attention MLA layers with DSA; reuses top-k mask impl from `build_attn`. Link: https://github.com/ggml-org/llama.cpp/pull/31779
- **Mage-VL-Cowork** (multi-modal) opened in `mtmd`; notes Metal `kernel_mul_mm_f32/f16_f32` don't respect `set_prec`. Link: https://github.com/ggml-org/llama.cpp/pull/31819
- **Hexagon**: CONV_3D on HTP/HMX tiling. Link: https://github.com/ggml-org/llama.cpp/pull/31814
- **Vulkan**: ReBAR-aware allocation strategy. Link: https://github.com/ggml-org/llama.cpp/pull/31658
- **OpenCL**: `repeat` for f16/i32/i16/bf16; `arange` PR open. Link: https://github.com/ggml-org/llama.cpp/pull/31829
- **WebGPU**: batched tensor get/set API. Link: https://github.com/ggml-org/llama.cpp/pull/31473
- **HIP/ROCm**: MMQ N-tile heuristic for CDNA merged. Link: https://github.com/ggml-org/llama.cpp/pull/28709
- **NVFP4 quantization** (Blackwell FP4 MMA) PR closed; benchmark noted "…40% speedup (20% on longer prompts)" — worth tracking for a follow-up landing. Link: https://github.com/ggml-org/llama.cpp/pull/23572
- **External PLE (engram) shards** loading via `--shard` (open PR) for multi-GGUF layouts. Link: https://github.com/ggml-org/llama.cpp/pull/31185

## 4. Performance & Optimization

- **MoE expert cache (ClassLRU)**: new LRFU/MQ-style hybrid clocked by real token counts. Primary win for `--n-cpu-moe` / small-VRAM MoE serving; no hard numbers released yet. Link: https://github.com/ggml-org/llama.cpp/pull/31233
- **OpenCL q8_0 / MoE q4_k/q5_1 small-n GEMV**: adds small-n kernels for spec-decode verification path. Link: https://github.com/ggml-org/llama.cpp/pull/31831
- **HIP MMQ N-tiles for CDNA**: empirical heuristic from parameter sweep; improves shapes where RDNA-derived tuning was mixed. Link: https://github.com/ggml-org/llama.cpp/pull/28709
- **Vulkan ReBAR**: skips BAR allocations when unsupported — relevant fixes for the AMD DGPU "slow with ReBAR disabled" family. Link: https://github.com/ggml-org/llama.cpp/pull/31658
- **NVFP4 / Blackwell**: 40% PP uplift (20% long-prompt) claimed in closed PR #23572. Track whether it reopens.
- **`n_ctx_slot` cap removal** (open PR): lets `-c` exceed `n_ctx_train` when memory allows (RoPE scaling). Link: https://github.com/ggml-org/llama.cpp/pull/31377
- Note: **CUDA ADD/GELU regression on B200** since PDL commit (`7b28f950`) still open — perf hit, not functional. Link: https://github.com/ggml-org/llama.cpp/issues/30004

## 5. Stability & Regressions

Ranked by user impact (all open unless marked):

1. **Speculative decoding correctness — greedy divergence on quantized targets** (#25618, 38 comments). Greedy output diverges from vanilla when target is Q4_K_M etc.; bf16 matches. This is the single biggest correctness risk for production spec-decode on quantized weights. https://github.com/ggml-org/llama.cpp/issues/25618
2. **`--cache-ram` cross-conversation KV bleed** (#27148). RAM-backed prompt cache restores an *unrelated* conversation into a fresh slot under concurrent load — correctness/security concern for multi-tenant servers. https://github.com/ggml-org/llama.cpp/issues/27148
3. **CUDA illegal memory access: Gemma 4 31B + draft-mtp after KV rollback** (#31077). Crash after checkpoint restore / full re-process with MTP. https://github.com/ggml-org/llama.cpp/issues/31077
4. **Server wedge mid-decode** (#27388): `/health` OK, `/slots` hangs, requires SIGKILL. No fix linked. https://github.com/ggml-org/llama.cpp/issues/27388
5. **Vulkan/ANV: FA scalar fallback → O(N²) PP + device loss** on Intel Arc B580 (#27638). Vulkan Flash Attention dispatch. https://github.com/ggml-org/llama.cpp/issues/27638
6. **draft-mtp on Qwen3.6 changes deterministic output** (CLOSED #23335). Runtime mitigation: disable spec-decode when determinism required.
7. **SYCL dual Arc B60 `--cpu-moe -sm layer` stall** (#31510): 100–1000× slowdown with 40 GB MoE cache vs 15 GB; all time in serial expert H2D. https://github.com/ggml-org/llama.cpp/issues/31510
8. **`/slots` restore loses KV reuse for hybrid/recurrent + SWA** (#28194): checkpoints not persisted for hybrid models. https://github.com/ggml-org/llama.cpp/issues/28194
9. **gfx1201 WMMA FA dispatch threshold** (#28867): ~20% spec-decode regression since #28102. https://github.com/ggml-org/llama.cpp/issues/28867
10. **Spec-decode returns logprob 0.0 (p=1.0) for every token** (#27972). Breaks downstream confidence-based routing. https://github.com/ggml-org/llama.cpp/issues/27972
11. **draft-dflash post_decode `seq_rm` abort on hybrid Qwen3.8 + `--parallel 8`** (#31656). https://github.com/ggml-org/llama.cpp/issues/31656
12. **CPU backend `supports_op` false-positive on MUL_MAT** for types without `vec_dot` → NULL deref; fix PR open. https://github.com/ggml-org/llama.cpp/pull/31832
13. **SYCL overcommit kernel panic** on oversized device buffers; guard PR open. https://github.com/ggml-org/llama.cpp/pull/31285
14. Build fix only: **CUDA Maxwell** repaired in b12167; **CLI invalid UTF-8 crash** repaired in b12177.

## 6. What This Means for Application Developers

- **Do not trust spec-decode on quantized targets for determinism-sensitive workloads** until #25618 is resolved. bf16/FP16 targets are reliable; n-gram speculation on quantized weights is fine. For B200/gfx1201, expect ~20% regression versus pre-#28102 baselines.
- **Multi-tenant servers**: audit `--cache-ram` / `--cache-idle-slots` usage against #27148 before relying on cross-slot KV reuse. Safest posture is to treat the RAM prompt cache as best-effort under concurrent load; segment tenants by slot if you cannot disable it.
- **MoE serving on small GPUs**: the new ClassLRU cache (b12170) is a direct improvement to `--n-cpu-moe` throughput and reduces the serial-expert-H2D stall reported in #25859 and the SYCL pathology in #31510. Consider re-benchmarking your offload config.
- **Vulkan on Intel/AMD**: b12171 ReBAR detection removes the code path responsible for slow AMD DGPU generation when ReBAR is off, but the ANV Flash Attention fallback (#27638) remains — pin Vulkan FA off for Intel until fixed.
- **Server hardening**: the invalid UTF-8 crash (b12177) is a real remote-input crash for any app forwarding user-supplied prompt bytes — upgrade. The `/v1/chat/completion/control` "inject" action PR (#27343) will be useful if you need to steer generation mid-stream.
- **Model coverage**: Kimi-K3.1-Pro (DSA/MLA hybrid) and Mage-VL-Cowork are now viable on llama.cpp routes; if you're building agent tool-callers on Qwen3-Coder/Nemotron, PR #30193 (unordered JSON tool args) is worth tracking for reliability.
- **Context sizing** (#31377 open): once merged, servers will honor `-c` beyond `n_ctx_train` with explicit user opt-in — plan configs accordingly, but do not depend on it yet.
- **NVFP4**: the 40% Blackwell uplift is not yet in a build; benchmark before committing to Blackwell-specific quantization strategy.

</details>

<details>
<summary><strong>Ollama</strong> — <a href="https://github.com/ollama/ollama">ollama/ollama</a></summary>

# Ollama Digest — 2026-11-07

## 1. Today's Highlights
No releases landed in the last 24h, but the issue/PR queue was active around the new llama-server-based 0.40.x runtime: a scheduler bug that leaks VRAM when an idle runner can't be killed has a fix PR open, and CUDA/Vulkan backend regressions continue to surface on specific GPUs (RTX 40-series with cuda_v13, AMD Renoir and Intel Lunar Lake iGPUs). Model-side work is concentrated on parser/renderer correctness (qwen3-coder, gemma4, Nemotron) and expanding MLX coverage to dense Nemotron architectures.

## 2. Releases & Breaking Changes
- No new releases in the last 24h.
- Upstream engine bump in progress: [#19576](https://github.com/ollama/ollama/pull/19576) `llama.cpp: version bump b12177` (from b12111). Expect behavior changes to carry through in the next build.

## 3. New Model & Hardware Support
- **Dense Nemotron (MLX)**: [#19567](https://github.com/ollama/ollama/pull/19567) registers `NemotronForCausalLM` in the MLX runner with Luciole normalization and Split+Metaspace tokenizer, unblocking direct Safetensors→MLX conversions of Luciole 1B/23B. Corresponding request: [#19566](https://github.com/ollama/ollama/issues/19566).
- **Nemotron-H fine-tunes**: [#19570](https://github.com/ollama/ollama/pull/19570) preserves a checkpoint's own chat template instead of forcing NVIDIA's `nemotron-3-nano` renderer/parser.
- **Windows ARM64 CUDA**: [#19580](https://github.com/ollama/ollama/pull/19580) adds CUDA 13.5 and SM 103 for ARM64, retaining SM 121.
- **MLX memory efficiency for Qwen3.8 Flash Next**: [#18078](https://github.com/ollama/ollama/pull/18078) moves QSA projections and non-expert MTP to BF16, uses MXFP8 for other dense/control weights (both `qwen3.8-flash-next:125b-a6b-nvfp4` and `:125b-mlx` republished).

## 4. Performance & Optimization
- Quantization strategy refinement in [#18078](https://github.com/ollama/ollama/pull/18078): blanket NVFP4 degraded long generations, so precision is now mixed per-tensor — a concrete trade-off between memory footprint and long-context quality for the 125B MoE.
- Scheduler VRAM accounting correctness (see §5) directly affects throughput: mispredicted free VRAM causes CPU offload and a second runner spawn, tanking tokens/sec.

## 5. Stability & Regressions
Ranked by severity:

1. **Runner leak on keep_alive expiry** — [#19577](https://github.com/ollama/ollama/issues/19577): when `Close` fails, Ollama marks the model unloaded, drops the runner, logs nothing; VRAM stays held, next load tilts to CPU and spawns a second `llama-server`. **Fix PR open**: [#19581](https://github.com/ollama/ollama/pull/19581).
2. **CUDA 13 long grammar-constrained generation → GPU falls off the bus** — [#17026](https://github.com/ollama/ollama/issues/17026): `unspecified launch failure` → Xid 79 on RTX 4070 Ti SUPER; same requests fine on cuda_v12. Needs triage.
3. **Vulkan crash on AMD Renoir iGPU** — [#19558](https://github.com/ollama/ollama/issues/19558): `llama-server` exits `0xe06d7363` at model load on gfx90c in 0.40.14 and 0.40.15-rc2, all models.
4. **Vulkan device lost on Intel Arc 130V (Lunar Lake)** — [#19158](https://github.com/ollama/ollama/issues/19158): `gemma4:12b` regressed vs 0.34.4 with any GPU offload.
5. **Intermittent 500 on `/api/chat` (macOS)** — [#19293](https://github.com/ollama/ollama/issues/19293): `apply-template … broken pipe` after a long streamed reply; retry succeeds.
6. **Oversized input silently truncated with truncation disabled** — [#19492](https://github.com/ollama/ollama/issues/19492) (closed).
7. **Parser/renderer correctness** (closed/in-progress): qwen3-coder tool-call nesting [#17021](https://github.com/ollama/ollama/pull/17021); gemma4 unclosed argument lists [#19274](https://github.com/ollama/ollama/pull/19274); stray `<channel|>` tags [#19574](https://github.com/ollama/ollama/pull/19574); `ornith:35b` raw tokens/tool XML [#16992](https://github.com/ollama/ollama/issues/16992).
8. **OpenAI-compat**: `/v1/models` returned `"data": null` with zero models — [#19381](https://github.com/ollama/ollama/issues/19381) (closed).
9. **GGUF hardening**: duplicate tensor names rejected at metadata parse — [#19571](https://github.com/ollama/ollama/pull/19571).
10. **Networking**: `ollama pull` TLS handshake timeout on networks dropping post-quantum X25519MLKEM768 ClientHello — [#19430](https://github.com/ollama/ollama/issues/19430).

## 6. What This Means for Application Developers
- **Don't assume VRAM is reclaimed.** If you manage long-lived `keep_alive` sessions on shared GPUs, monitor for orphaned `llama-server` processes until [#19581](https://github.com/ollama/ollama/pull/19581) lands; a leaked runner can silently push your next model to CPU.
- **Pin your backend if you're on cuda_v13 + grammar-constrained decoding.** [#17026](https://github.com/ollama/ollama/issues/17026) is a hardware-level failure, not a retryable error — force cuda_v12 or use unconstrained output on affected RTX 40-series setups.
- **Retry logic still matters on macOS**: [#19293](https://github.com/ollama/ollama/issues/19293) is transient and self-heals on retry; treat `apply-template` broken-pipe 500s as retryable.
- **Tool-calling reliability is improving but model-specific.** If you run qwen3-coder or gemma4 via tool calls, the pending parser PRs address real failure modes; validate against your exact model tag and upgrade deliberately.
- **OpenAI SDK compatibility**: older clients tolerant of `null` may pass, but strongly-typed (e.g. .NET/Java) SDKs will fail until the fix ships in a stable build — a reason to keep your Ollama version current.
- **MLX users** gain dense Nemotron/Luciole support and better memory behavior for Qwen3.8 Flash Next; useful if you're serving larger MoE models on Apple Silicon.

</details>

<details>
<summary><strong>LiteLLM</strong> — <a href="https://github.com/BerriAI/litellm">BerriAI/litellm</a></summary>

# LiteLLM Digest — 2026-11-07

## 1. Today's Highlights

The 1.108 LTS line is in active backport mode: roughly a dozen `lts/1.108.x` cherry-picks landed today covering encrypted reasoning round-trips, guardrail write-back fidelity, Bedrock empty `api_base` crashes, and hosted MCP `tool_choice` handling — a strong signal that 1.108 is the branch most production deployments are converging on. On the protocol-translation side, the day's dominant theme is **Anthropic `/v1/messages` ↔ OpenAI interop**: multiple open bugs and PRs concern reasoning blocks, `tool_result.is_error`, `output_config`, and `server_tool_use` being mangled in translation. Billing integrity also got attention, with a PR closing the gap where malformed or missing upstream `usage` led to $0-priced traffic bypassing key budgets.

## 2. Releases & Breaking Changes

- **v1.108.2 (LTS)** and **v1.108.1 (LTS)** published. The `ghcr.io/berriai/litellm:lts` tag now tracks the newest LTS patch; images are cosign-signed and verifiable against the published `cosign.pub`. No config-schema changes announced in these patches. → [Releases](https://github.com/BerriAI/litellm/releases)
- **Potentially breaking (in review):** PR #45609 proposes bridging OpenAI function-tool + reasoning requests to Bedrock's native `/responses` instead of Converse — marked `feat(bedrock)!`. Expect behavioral differences for reasoning-heavy Bedrock tool calls if merged. → [PR #45609](https://github.com/BerriAI/litellm/pull/45609)
- **Potentially breaking (in review):** PR #48936 changes how managed files/vector-store hooks are registered, moving imports to `litellm_enterprise`. Fixes the `pip install 'litellm[proxy,extra_proxy]'` path where `POST /v1/files` with `target_model_names` 500s. → [PR #48936](https://github.com/BerriAI/litellm/pull/48936)

## 3. New Model & Hardware Support

- **Gemini model catalog sync** (PR #50468): adds documented successor mappings for five Gemini rows without altering prices or shutdown dates. → [PR #50468](https://github.com/BerriAI/litellm/pull/50468)
- **Vertex AI Claude partner models** (Issue #49645, open): request body serialization currently places `messages` first; feature request to reorder after `system`/`tools` for partner-model compatibility. → [Issue #49645](https://github.com/BerriAI/litellm/issues/49645)
- No new CUDA/ROCm/Metal or quantization support in this window; `hosted_vllm` token counting correctness is being improved in PR #48830 (see §4).

## 4. Performance & Optimization

- **SAP GenAI Hub latency** (Issue #47096, open): every `sap/` model call performs two blocking HTTP GETs against the AICORE management API to rediscover the orchestration deployment URL — measured ~**870ms overhead per call** under load. No fix PR yet; caching the deployment URL is the obvious remediation. → [Issue #47096](https://github.com/BerriAI/litellm/issues/47096)
- **Token counting via backend** (PR #48830): Anthropic count route now counts through the actual backend, prices images by pixel area, and 404s unserved models — improves budget-reservation accuracy rather than raw latency. → [PR #48830](https://github.com/BerriAI/litellm/pull/48830)
- **Prisma startup downloads** (Issue #33167, closed as stale): v1.92+ fetched Prisma binaries at boot and failed in egress-restricted networks — closed without a merged fix; still worth pinning/offline-provisioning if you run air-gapped. → [Issue #33167](https://github.com/BerriAI/litellm/issues/33167)

## 5. Stability & Regressions

Ranked by severity:

1. **Budget bypass on malformed usage** (PR #50469, open): OpenAI-compatible upstreams returning missing/malformed `usage` were priced at $0 or 500'd, so `max_budget` never closed on that traffic. Fix PR exists. → [PR #50469](https://github.com/BerriAI/litellm/pull/50469)
2. **Cache poisoning with empty replies** (PR #50057, open): streamed `/v1/messages` calls to non-Anthropic models that returned empty were cached, so identical requests replayed the empty answer indefinitely. Fix PR exists. → [PR #50057](https://github.com/BerriAI/litellm/pull/50057)
3. **Credential CRUD race** (PR #50464, open): concurrent periodic reload could undo in-memory credential create/update/delete. Fix wraps DB write + memory update in `MODEL_RECONCILE_LOCK`. → [PR #50464](https://github.com/BerriAI/litellm/pull/50464)
4. **OpenRouter streaming phantom choice** (Issue #50156, open, v1.108.0): provider accounting trailer becomes an extra empty assistant choice after `finish_reason: stop` before the final usage chunk — breaks strict stream consumers. → [Issue #50156](https://github.com/BerriAI/litellm/issues/50156)
5. **Anthropic→non-Anthropic thinking replay** (Issue #49969, closed, v1.106): Bedrock Converse replayed `reasoningText.signature` to GLM 5.3, causing Bad Request on auto-router failover. → [Issue #49969](https://github.com/BerriAI/litellm/issues/49969)
6. **Vertex/Gemini cache_control reordering** (Issue #47046, open): non-prefix `cache_control` moves the message ahead of history and drops `system_instruction` — silent correctness failure, model answers the previous turn. → [Issue #47046](https://github.com/BerriAI/litellm/issues/47046)
7. **JSON-defined providers drop tools** (Issue #48145, open, v1.106 regression vs 1.81): OpenAI-like providers configured via JSON lose tool definitions unless the model is in the catalog. → [Issue #48145](https://github.com/BerriAI/litellm/issues/48145)
8. **`reasoning_effort: "none"`** (Issue #47924, open): strips thinking config instead of disabling it, leaving Claude defaults on. → [Issue #47924](https://github.com/BerriAI/litellm/issues/47924)
9. **`token_counter` rejects `detail: "original"`** (Issue #47327, open): pre-call context checks log ERROR and skip enforcement. → [Issue #47327](https://github.com/BerriAI/litellm/issues/47327)
10. **`/v1/images/edits` missing image → 500** (Issue #50173, open): should be 400; client-error mapping gap on Vertex Gemini. → [Issue #50173](https://github.com/BerriAI/litellm/issues/50173)

Also of note: `COHERE_API_BASE` ignored for embeddings (Issue #50248); `httpx.Timeout` connect timeout collapsed for non-OpenAI/Azure/Bedrock providers (Issue #47822); Anthropic `server_tool_use` surfaced as pending `tool_calls`, causing agent loops (Issue #48384, closed); Langfuse trace dropped on `/v1/decisions` routes (Issue #45584).

## 6. What This Means for Application Developers

- **If you're on 1.108 LTS:** pull `:lts` for the backported fixes, but be aware the LTS branch is still accumulating Anthropic-bridge fidelity patches (PRs #50456, #50462, #50449, #50460). If you use `/v1/messages` against OpenAI or Bedrock backends, pin and retest on each LTS bump.
- **Budget enforcement is not trustworthy today** for upstreams with sloppy `usage` reporting. Until #50469 merges, monitor spend independently or add a secondary cap.
- **Cache + streaming agents:** disable response caching for `/v1/messages` until #50057 lands, or you risk sticky empty responses.
- **Auto-router failovers across model families** (Claude → GLM/Gemini) are a known minefield: thinking signatures, `server_tool_use`, and schema sanitization all have open or recently-closed correctness issues. Test failover paths explicitly rather than assuming translation is lossless.
- **Anthropic-native clients (Claude Code, etc.)** should expect growing but imperfect fidelity on `tool_result.is_error`, per-turn `output_config`, and encrypted reasoning across the bridge — several of these are fixed only via the in-flight backports listed above.
- **Self-hosted / air-gapped:** the Prisma startup download regression was closed as stale, not fixed — validate your egress story before upgrading past v1.92.

</details>

<details>
<summary><strong>Unsloth</strong> — <a href="https://github.com/unslothai/unsloth">unslothai/unsloth</a></summary>

# Unsloth Digest — 2026-11-07

## 1. Today's Highlights
Unsloth shipped v0.1.916-beta, headlined "Large Performance Fixes" — faster typing/search in long chats, faster image/video loads, dual-GPU image/video generation, and per-chat browser windows with a floating browser pane. Activity is dominated by Unsloth Studio/Desktop polish: a wave of WebKit restyle/scroll-latency fixes, multi-agent CLI integration (Claude Agent, Codex, Hermes MCP), and account-level settings sync. On the engine side, an important external heads-up: transformers 5.23 renames gradient checkpointing to activation checkpointing, which silently changes Trainer behavior in four audit sites.

## 2. Releases & Breaking Changes
- **v0.1.916-beta — Large Performance Fixes**: faster typing/search in long chats, faster image/video loads, two-GPU image/video generation, separate browsers per chat, floating browser window. Glass theme optimizations included. (Release notes only; no API changes documented.)
- **transformers 5.23 compatibility (action required)**: gradient checkpointing API renamed to activation checkpointing; old names remain aliases until v6, but `Trainer` reads only the new names, changing behavior at 4 audited sites in Unsloth. Issue: [unslothai/unsloth#16667](https://github.com/unslothai/unsloth/issues/16667) (CLOSED).
- **Blender MCP breakage in v0.1.915-beta**: MCP SDK 2.3.0 / FastMCP 4.1.6 incompatibility blocks Blender MCP startup on Windows 11. [unslothai/unsloth#16878](https://github.com/unslothai/unsloth/issues/16878) (CLOSED).
- **Version-bump regression risk**: beta update path failing for some users on desktop — [unslothai/unsloth#16897](https://github.com/unslothai/unsloth/issues/16897) (OPEN).
- **Scan baseline churn**: unsloth-zoo 2026.11.3 moved 7 previously-reviewed sites, triggering `pip scan-packages :: hf-stack` failures (3 CRITICAL, 4 HIGH). Re-review PR: [#16922](https://github.com/unslothai/unsloth/pull/16922).

## 3. New Model & Hardware Support
- **Cosmos3 (NVIDIA) in Studio Video page**: 256p support, NVIDIA frame ranges, per-model frame-rate picker — [PR #16737](https://github.com/unslothai/unsloth/pull/16737) (stacked; merge first).
- **Cosmos3-Nano video continuation**: extend a clip from its last/first frames — [PR #16741](https://github.com/unslothai/unsloth/pull/16741) (stacked on #16737).
- **Cosmos3-Nano-Sim-Transfer restyle mode** on sd-server (Canny-edge conditioned video restyle) — [PR #16975](https://github.com/unslothai/unsloth/pull/16975).
- **Claude Agent connection** via local `claude` CLI (Pro/Max plans, no API key needed), using Anthropic Agent SDK — [PR #16963](https://github.com/unslothai/unsloth/pull/16963).
- **Mic-to-audio-model routing**: dictation can send audio directly to any audio-input model (Qwen3-ASR, custom endpoints, or pass-through) — [PR #16976](https://github.com/unslothai/unsloth/pull/16976).
- **gemma-4-thinking chat template bug**: reasoning stripped by template — [issue #16699](https://github.com/unslothai/unsloth/issues/16699) (CLOSED).
- **Multi-GPU**: DiffusionGemma on 2× CUDA (RTX 3060 12GB + 4060 Ti 16GB) works for short prompts but fails >~1792 tokens (see Stability).
- **Hardware gap flagged**: image generation falls back to CPU/RAM on Windows 11 + RTX 3060 while text generation uses GPU — [issue #16803](https://github.com/unslothai/unsloth/issues/16803) (OPEN).
- **Feature request**: Transformer block swap (layer offload to RAM) for low-VRAM training — [issue #3997](https://github.com/unslothai/unsloth/issues/3997) (CLOSED).

## 4. Performance & Optimization
- **Long-chat UI lag fixed (release-level)**: v0.1.916 addresses typing/search and long-conversation lag; issue [#16603](https://github.com/unslothai/unsloth/issues/16603) closed.
- **WebKit restyle latency**: closing dialogs/menus took 5–7s on long chats (macOS/WebKitGTK); browser pane open took >20s on Linux. Fixes stop full-page restyles on sidebar/menu/run-settings toggles — [PR #16938](https://github.com/unslothai/unsloth/pull/16938) (CLOSED), follow-up [#16961](https://github.com/unslothai/unsloth/pull/16961) (OPEN). Opened ~9,200–18,400 element restyles at 150 turns on Chromium.
- **RAG ingestion throughput**: `_embed_all` embeds one document at a time; batch-of-1 path measures **~99 ms/chunk vs 2.07 ms batched** (≈48× gap on small-file corpora). [issue #16715](https://github.com/unslothai/unsloth/issues/16715) (OPEN).
- **Image-load memory**: Studio now returns freed glibc heap to the OS after image loads instead of retaining staging/temporary chunks in arenas — [PR #16934](https://github.com/unslothai/unsloth/pull/16934).
- **Idle-unload correctness/perf**: idle-unloaded GGUFs reload with original launch settings (context size, KV cache quantization) instead of defaults — [PR #16927](https://github.com/unslothai/unsloth/pull/16927).
- **Read-aloud latency**: TTS per-reply start delayed 10–20s vs ~0.5s on the dedicated page; fix starts speaking after the first sentence — [PR #16955](https://github.com/unslothai/unsloth/pull/16955).
- **Voice mode stack**: hands-free Voice mode on audio.cpp kept resident beside the chat model ([PR #12384](https://github.com/unslothai/unsloth/pull/12384)), with thinking-filler + local turn-judge ([PR #14084](https://github.com/unslothai/unsloth/pull/14084)) — large, still open.
- **Multi-LoRA batch pagination**: cursor ordering fixes when batches start out of order — [#16719](https://github.com/unslothai/unsloth/issues/16719), [#16720](https://github.com/unslothai/unsloth/issues/16720) (both CLOSED).

## 5. Stability & Regressions (ranked)
1. **HIGH — Windows crash loading Krea 2 Turbo (0xc0000005)**: reproducible backend crash in Studio image generation — [issue #16892](https://github.com/unslothai/unsloth/issues/16892) (OPEN, no fix PR yet).
2. **HIGH — DiffusionGemma multi-GPU abort**: GGML_ASSERT `n_ubatch >= n_tokens` on prompts >~1792 tokens across 2 CUDA GPUs — [issue #16925](https://github.com/unslothai/unsloth/issues/16925) (OPEN).
3. **MEDIUM — CLI silently accepts truncated streams**: `unsloth inference`/`chat` treat a stream ending before finish reason/`[DONE]` as success, poisoning next-turn history — [issue #16867](https://github.com/unslothai/unsloth/issues/16867) (CLOSED).
4. **MEDIUM — RAG indexing permanently fails during embedding-model download**: every file errors with `EmbeddingModelDownloadRequiredError`, no queueing/retry — [issue #16714](https://github.com/unslothai/unsloth/issues/16714) (CLOSED).
5. **MEDIUM — Claude API answers truncated at 1,024 tokens** when `max_tokens` omitted on external connections (`/v1/chat/completions`, `/v1/responses`) — [PR #16953](https://github.com/unslothai/unsloth/pull/16953) (fix open).
6. **LOW — `search_conversation` tool vanished** for the third time — [issue #16706](https://github.com/unslothai/unsloth/issues/16706) (OPEN).
7. **LOW — Security hardening gap**: metadata-host blocklist only matched dotted `169.254.169.254`; integer/hex/octal spellings could bypass — fix blocks alternate encodings — [PR #16937](https://github.com/unslothai/unsloth/pull/16937).
8. **LOW — Studio web_search always returns "No results found"**: ddgs missing `Accept` header (DDG 202) and yandex absent from engine allow-list — [issue #16664](https://github.com/unslothai/unsloth/issues/16664) (OPEN).
9. **LOW — S3 audio JSONL prep aborts** on U+2028 inside transcripts — [issue #16721](https://github.com/unslothai/unsloth/issues/16721) (CLOSED).
10. **UI polish (mostly fixed)**: Glass theme inconsistency (#16770), whole-background blur (#16820), wallpaper CSS specificity conflict (#16736) — all CLOSED; Blender MCP SDK incompatibility (#16878) CLOSED.

## 6. What This Means for Application Developers
- **Pin transformers carefully**: if you fine-tune with Unsloth on transformers ≥5.23, verify gradient/activation checkpointing behavior at the four affected sites ([#16667](https://github.com/unslothai/unsloth/issues/16667)); checkpoint-memory expectations may shift silently.
- **Streaming clients need their own termination checks**: the CLI accepted partial streams as success ([#16867](https://github.com/unslothai/unsloth/issues/16867)). If you use Studio's HTTP API directly, validate for finish reason or `[DONE]` yourself and treat truncated turns as errors before persisting history.
- **Always set `max_tokens` explicitly** on external Anthropic-backed connections until [#16953](https://github.com/unslothai/unsloth/pull/16953) lands, or long outputs will cut at 1,024 tokens.
- **Multi-GPU video diffusion is not yet robust**: keep prompts under ~1792 tokens on 2-GPU DiffusionGemma setups ([#16925](https://github.com/unslothai/unsloth/issues/16925)); test Cosmos3 workflows against the stacked PR pair (#16737 → #16741) before depending on them.
- **RAG pipelines are the weakest link**: batch ingestion is 48× slower than it should be ([#16715](https://github.com/unslothai/unsloth/issues/16715)), and indexing during model download fails hard with no retry ([#16714](https://github.com/unslothai/unsloth/issues/16714)). Gate ingestion on embedding-model readiness and consider batching documents upstream.
- **Long-context UX is materially better** in v0.1.916-beta, especially on WebKit (macOS/Linux desktop); WebKit users should upgrade to benefit from the restyle fixes ([#16938](https://github.com/unslothai/unsloth/pull/16938), [#16961](https://github.com/unslothai/unsloth/pull/16961)).
- **Agent integrations expanding**: Claude Agent (Pro/Max via CLI login), Codex `AGENTS.md`/skills carry-over, and Hermes MCP config carry-over make Studio a viable local LLM gateway for coding agents — but MCP SDK 2.3.0 still breaks Blender MCP ([#16878](https://github.com/unslothai/unsloth/issues/16878)), so audit MCP dependencies before enabling toolchains.
- **If you allow model-run Python/tool calls**, review [#16937](https://github.com/unslothai/unsloth/pull/16937): metadata-endpoint blocking is being hardened to cover non-dotted IP spellings — relevant if your sandbox egress policy relies on hostname matching.

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar).*