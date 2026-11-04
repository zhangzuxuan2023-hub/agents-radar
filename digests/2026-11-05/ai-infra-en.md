# AI Infrastructure Digest 2026-11-05

> Generated: 2026-11-04 23:22 UTC | Projects covered: 6

- [vLLM](https://github.com/vllm-project/vllm)
- [SGLang](https://github.com/sgl-project/sglang)
- [llama.cpp](https://github.com/ggml-org/llama.cpp)
- [Ollama](https://github.com/ollama/ollama)
- [LiteLLM](https://github.com/BerriAI/litellm)
- [Unsloth](https://github.com/unslothai/unsloth)

---

## Cross-Project Comparison

# Cross-Project Comparison Report — AI Infrastructure Digest
**Date: 2026-11-05** | Prepared for infrastructure engineers and technical decision-makers

---

## 1. Ecosystem Overview

Today's landscape shows a two-tier market operating in tension. The high-throughput serving engines (vLLM, SGLang) are in a **hardening phase**: no releases shipped, but maturity is being tested by serious correctness bugs — cross-request KV contamination in P/D disaggregation, silent KV-page-stride corruption in Mooncake paths, and multi-model release-tag regressions. Simultaneously, the local/edge runtime (llama.cpp) shipped its **v0.9.0 kernel-level release** (flash_attn_ext_rows, FP8 E4M3), while the gateway (LiteLLM) and training (Unsloth) layers focused on **billing/streaming correctness** and **CUDA-graph decode acceleration**, respectively. The common thread across all five projects: **reasoning-model behavior and P/D + cache infrastructure are where failures now concentrate**, and correctness — not raw throughput — is the dominant risk for production deployments.

---

## 2. Activity Comparison

| Project | Layer | Issues Activity | PR Activity | Release Status (24h) |
|---|---|---|---|---|
| **vLLM** | Serving engine | High — dominated by DeepSeek-V4/V4.1, Qwen3.8 bring-up; 1 CRITICAL open bug | Active — perf PRs (KV checksums, Triton vectorization), model portfolios | **No release.** v0.32.0 flagged as risky (regression trio) |
| **SGLang** | Serving engine | High (61 updated) | Very high (500 updated) — heaviest PR throughput | **No release.** FlashInfer 0.7.3 migration in flight (breaking) |
| **llama.cpp** | Local runtime | Moderate — Intel GPU regression cluster | Active — backend tuning (OpenCL, Vulkan, HIP) | **v0.9.0 shipped** (b12110+): FP8 E4M3, `flash_attn_ext_rows`, 4 architectures |
| **Ollama** | Local runtime / distribution | High — tool-calling + API-correctness regressions on v0.40.13/14 | Moderate — fix PRs on tool-result handling, SSE flush | **No release.** CUDA v13 grammar crash (Xid 79) is highest-severity open item |
| **LiteLLM** | Gateway | Very high — streaming/billing correctness dominates | Active — 9-PR "control plane" stack in review | **3 releases:** v1.108.0 (stable), v1.106.7 (patch), v1.109.0-rc.1 — all cosign-signed |
| **Unsloth** | Fine-tuning / training | Moderate — desktop/Studio regressions | Active — CUDA-graph decode, multi-GPU diffusion | **v0.1.915-beta** (Qwen3.8-Flash-Next, Glass themes) |

**Read-out:** LiteLLM is the most release-active (3 releases, all signature-gated); llama.cpp and Unsloth shipped meaningful version bumps; the two heavyweight serving engines shipped nothing — both are absorbing breaking migrations or fixing critical correctness bugs. **SGLang's PR volume (500) dwarfs all others**, indicating unusually heavy in-flight refactoring.

---

## 3. Model Support Race

| Model / Architecture | vLLM | SGLang | llama.cpp | Ollama | Unsloth |
|---|---|---|---|---|---|
| **DeepSeek-V4 / V4.1** | ✅ Bring-up active (but SM8x missing; block-size broken on v0.32.0) | ✅ TRTLLM sparse MLA, FP8 KV layout | — | — | — |
| **Qwen3.8 family (2.4T-A95B, Flash-Next)** | ✅ AMD gfx950 trackers + optimizations | ✅ HiCache backup (with known bug #48145) | — | — | ✅ **Flash-Next in v0.1.915-beta** |
| **Kimi K3.1 Pro / K3** | ✅ Broad PR (ROCm, spec decode, Rust, Mooncake/NIXL) | ✅ PCP KV sharding + HiCache + DFlash | — | — | — |
| **GLM-5.2 / 5.3** | ✅ Optimization tracker | ✅ Breakable prefill CUDA graphs | — | — | — |
| **Mistral Large 4** | — | — | ✅ **v0.9.0** | — | — |
| **Clover / Limite / Metis-1.6** | — | — | ✅ **v0.9.0** (4 architectures) | — | — |
| **Nemotron 3.5 ASR Streaming** | 🔶 Requested (#47455) | — | — | — | — |
| **Drama-3 TTS** | — | — | ✅ Audio support (b12116) | — | — |
| **Inception Mercury (diffusion LLM)** | — | — | — | — | ✅ Preset + live streaming |
| **Qwen3 / 3.5 (YaRN, RoPE)** | — | — | — | ✅ MLX YaRN scaling | — |

**Who's ahead:**
- **Serving engines (vLLM, SGLang)** are locked in a **frontier-model arms race** — both are racing on the same new flagships (DeepSeek-V4, Qwen3.8, Kimi K3, GLM-5.x). vLLM is ahead on **breadth of hardware backends** (ROCm, Rust frontend, Mooncake/NIXL); SGLang is ahead on **deep architectural integration** (TRTLLM sparse MLA, PCP KV sharding).
- **llama.cpp** owns the **broad-tail architecture adoption** niche — 4 new architectures + audio + new dense/ternary models in a single release. It is the clear leader for model-format coverage breadth.
- **Unsloth** is the fastest to **fine-tuning presets** for new diffusion LLMs and Qwen3.8-Flash-Next.
- **Gap signal:** vLLM's Ampere (SM8x) support for DeepSeek-V4 remains **open** (#50576) — a meaningful capacity-planning constraint for cost-sensitive fleets.

---

## 4. Performance Frontier

The optimization effort is **concentrated in KV cache and disaggregation**, with kernels and quantization close behind. Distribution of effort across the layer stack:

**KV cache & P/D disaggregation (highest activity, highest risk):**
- vLLM: connector-agnostic KV checksums (#62211), MooncakeConnector RFC, scheduler stall elimination (1.0–1.8s).
- SGLang: Mooncake page-stride fix, Kimi-K3 PCP KV sharding, HiCache host-tier work — *but* this is also where the most dangerous bugs live (silent corruption).
- llama.cpp: `flash_attn_ext_rows` for efficient unified-KV multi-sequence serving — a foundational primitive.

**Kernels & attention:**
- Triton KV-slot vectorization (vLLM #62113), AITER prefill attention on gfx950 (**9.3s → 4.1s** on MI350X, SGLang), FP8-Q ASM MLA verify (P90 ITL **6.77ms → 6.18ms**, SGLang), Vulkan Intel prefill FA kernel (llama.cpp).

**CUDA graphs & decode loops:**
- Unsloth's **CUDA-graph decode by default in `generate()`** (bitwise-identical, ~675 kernels/step eliminated on Qwen3-0.6B) is today's most under-appreciated win — a free speedup for T4-class consumer hardware. SGLang extends breakable prefill CUDA graphs to interleave CP prefill.

**Quantization:**
- llama.cpp **OCP FP8 E4M3** (v0.9.0) + CUDA TQ2_0 for ternary/BitNet models. vLLM ModelOpt FP8/NVFP4 weight-only embeddings rebase. Unsloth FP8 + int8-vs-bf16 ConvRot analysis (quality gap quantified at LPIPS 0.0865).

**Distributed serving & AMD/ROCm (emerging theme):**
- Both serving engines are investing heavily in **AMD gfx950/MI355X** — vLLM's Qwen3.8 AMD trackers and SGLang's AITER/MLA wins signal ROCm is moving from "supported" to "optimized." FlashInfer 0.7.3's removal of `MoeAlltoAll` is a **forced migration** for SGLang's NVLink one-sided all-to-all.

**Net:** Optimization ROI is still highest in **KV/disaggregation data movement** — but that same region is where today's most severe correctness risks live.

---

## 5. Layer Positioning

| Layer | Project(s) | Core Function | Primary Optimization Axis | Risk Profile |
|---|---|---|---|---|
| **Serving engine (cluster)** | **vLLM**, **SGLang** | Max-throughput multi-tenant inference, P/D disaggregation, prefix caching | KV movement, batching, kernel fusion, large-model bring-up | **Highest blast radius** — single bugs (KV contamination, corruption) affect many tenants; critical data-leak class |
| **Local runtime** | **llama.cpp**, **Ollama** | Single-node/edge inference, GGUF distribution, consumer hardware | Architecture coverage, quantization formats, backend kernels (OpenCL/Vulkan/Hexagon) | Correctness on non-NVIDIA backends (Intel Vulkan nondeterminism); Ollama adds **distribution/tooling** surface |
| **Gateway** | **LiteLLM** | Multi-provider routing, billing, quotas, guardrails, observability | Correctness of usage accounting, streaming/fallback semantics, fleet control plane | **Billing/quota correctness** — silent cost gaps; agent-loop UX regressions |
| **Training / fine-tuning** | **Unsloth** | LoRA/QLoRA fine-tuning, diffusion model training, presets | Decode kernel capture, memory footprint, checkpoint compat | **Training-data corruption** (S3 manifest bugs) and framework-rename drift |

**Key distinctions for decision-makers:**
- **vLLM vs SGLang** are substitutes competing on the same layer — vLLM leads on hardware breadth and ecosystem (Rust frontend, NIXL), SGLang leads on deep model-specific kernel integration and AMD determinism. Choosing between them increasingly means choosing a **release cadence and bug-surface profile**.
- **llama.cpp vs Ollama**: llama.cpp is the **engine**; Ollama is the **distribution + API + UX layer** built on it. Many Ollama regressions (tool calling, Responses API) are its own plumbing, not llama.cpp.
- **LiteLLM is the only control-plane layer** in this set, and it is the only one shipping stable releases today — reflecting a **different maturity phase** (operational stability vs frontier model chasing).
- **Unsloth sits upstream** (model creation) and increasingly overlaps serving (CUDA-graph decode, browser agents) — watch this convergence.

---

## 6. Trend Signals — What to Watch

**Trend 1 — P/D disaggregation is production-ready in design, but not yet in correctness.**
Both serving engines have severe, silent-failure bugs in disaggregated KV paths: vLLM cross-request contamination (#61160, data-leak class) and SGLang Mooncake stride corruption (#48230). **Watch:** whether `--no-enable-prefix-caching`-style mitigations become standard defaults. *Agent/app developers:* if you run P/D, disable prefix caching until #61160 lands and audit router versions (#31206 dead-prefill).

**Trend 2 — The "release tag regression" problem is acute.**
vLLM v0.32.0 has a regression trio; Ollama v0.40.13/14 broke native tool calling. **Watch:** pinning discipline. *App developers:* the safest release is frequently the **previous one** (vLLM v0.31.1 is the recommended pin for long-context Qwen). Build pinning into deployment, not into hope.

**Trend 3 — Reasoning models stress every layer of the streaming stack.**
LiteLLM's streamed-reasoning fallback bugs (#49795), SGLang's EAGLE PD decode corruption, and vLLM's reasoning-parser whitespace bug all stem from reasoning-token handling. **Watch:** SGLang's new **time-to-first-answer-token histogram** (#48466) — this separates reasoning latency from answer latency and is the right observability primitive for reasoning SLOs. Adopt it.

**Trend 4 — AMD/ROCm is graduating from "supported" to "optimized."**
gfx950/MI355X appears across vLLM (Qwen3.8 trackers), SGLang (AITER, MLA), llama.cpp (HIP fast-math). **Watch:** for regulated/reproducibility workloads, SGLang's **deterministic-mode AITER prefill** is notable. For capacity planning, AMD is a credible Hopper/Blackwell alternative *now* for specific models.

**Trend 5 — Gateway ↔ agent-loop interoperability is fragile.**
LiteLLM's `server_tool_use`-as-pending-toolcall (#48384), dropped encrypted reasoning (#49046), and Ollama's tool-template JSON corruption (#19529) all break Claude-Code-style agent clients. **Watch:** tool-call ID matching (Ollama #18767) and explicit parser pinning (SGLang `--tool-call-parser auto` unreliable, #48172). *Agent developers:* **never rely on auto-detection in production** — pin explicit parsers and normalize tool results client-side.

**Trend 6 — Control-plane consolidation is coming.**
LiteLLM's 9-PR stack (dataplane registry → OCSF audit → SIEM sinks → OTel spans) points to **one control plane governing many dataplanes** — a fleet-management future. Nothing to adopt yet, but multi-cluster gateway operators should track #49655.

**Trend 7 — Free performance is still shipping at the edge.**
Unsloth's bitwise-equal CUDA-graph decode and llama.cpp's `flash_attn_ext_rows` both deliver **no-code-change** gains on consumer/edge hardware. *App developers:* local serving is quietly getting faster; re-benchmark before scaling cloud capacity for latency-sensitive local workloads.

---

### Bottom line for decision-makers
- **Cluster serving:** hold on vLLM v0.32.0 for DeepSeek/long-context; disable P/D + prefix caching until critical KV bugs close. SGLang offers deeper AMD determinism but carries more in-flight breaking migrations (FlashInfer 0.7.3, `api.v1`).
- **Edge/local:** upgrade llama.cpp to v0.9.0 (validate FP8 E4M3); tighten `-hf` quant tags; avoid Intel Vulkan coopmat for classifiers.
- **Gateway:** v1.108.0 is stable with cosign verification as the contract — but **do not trust usage counts for aborted streams**, and pin model IDs (EU Haiku profile removal is a live footgun).
- **Training:** pin transformers <5.23 until the checkpointing rename is absorbed; audit S3/audio manifests for silent data corruption.

---

## Per-Project Reports

<details>
<summary><strong>vLLM</strong> — <a href="https://github.com/vllm-project/vllm">vllm-project/vllm</a></summary>

# vLLM Digest — 2026-11-05

## 1. Today's Highlights
No new releases landed in the last 24 hours, but issue activity is dominated by DeepSeek-V4/V4.1 and Qwen3.8-class model bring-up, with several correctness regressions surfacing on SM120 (Blackwell) and in P/D-disaggregated deployments. A potentially serious KV-cache cross-request contamination bug in prefix-cached P/D setups (#61160) remains open, while new perf PRs target KV-transfer verification and Triton attention vectorization. AMD continues pushing gfx950/MI355X optimization trackers for the Qwen3.8 family.

## 2. Releases & Breaking Changes
No new releases in the last 24h. Relevant compatibility notes from open items:
- **v0.32.0 regression trio**: block-size failures on the release tag for DeepSeek-V4.1 (#59203), Qwen4Exp 262K-prefill CUDA illegal memory access that works on v0.31.1 (#62383), and SM120 rejection of `enable_adaptive_verification` (#63343) — users on v0.31.x may want to hold upgrades pending fixes.
- PR to ship missing `vllm/utils/numa_wrapper.sh` in wheels — NUMA-bound launches abort at startup on git-less builds ([#65756](https://github.com/vllm-project/vllm/pull/65756)).
- Default CUDA build moving 13.0 → 13.2 to match pinned torch 2.13.0 wheels ([#55387](https://github.com/vllm-project/vllm/pull/55387)).

## 3. New Model & Hardware Support
- **SM8x (A100/A800, RTX 30xx) support for DeepSeek-V4-Flash / -0731** — hottest issue of the day (128 comments); neither checkpoint runs on Ampere today ([#50576](https://github.com/vllm-project/vllm/issues/50576)).
- **Kimi K3.1 Pro** model PR opened with a very broad tag footprint (ROCm, spec decode, KV connectors, Rust frontend, Mooncake/NIXL) ([#65685](https://github.com/vllm-project/vllm/pull/65685)).
- **RDNA 3.5 (gfx115x) hybrid W4A16 MoE backend** — fused HIP decode + Triton prefill ([#65754](https://github.com/vllm-project/vllm/pull/65754)).
- **NVIDIA Nemotron 3.5 ASR Streaming (0.6B)** support requested ([#47455](https://github.com/vllm-project/vllm/issues/47455)).
- **ModelOpt FP8/NVFP4 weight-only embeddings** loading PR rebased ([#42791](https://github.com/vllm-project/vllm/pull/42791)).
- **Rust frontend tokenizer parity test** covering 8 tokenizers / 35 recipes, with two pinned divergences ([#65757](https://github.com/vllm-project/vllm/pull/65757)).

## 4. Performance & Optimization
- **GLM-5.3 optimization tracker** (same `glm_moe_dsa` base as 5.2) ([#57406](https://github.com/vllm-project/vllm/issues/57406)).
- **Qwen3.8-2.4T-A95B and Qwen3.8-Flash-Next gfx950/MI355X** optimization plans landing as stacked PRs on AMD ([#57149](https://github.com/vllm-project/vllm/issues/57149), [#59575](https://github.com/vllm-project/vllm/issues/59575)).
- **Batch-invariant mode on Hopper/Blackwell** reportedly routes Linear layers to Triton even though cuBLAS is already batch-invariant under the mode — avoidable perf loss ([#65569](https://github.com/vllm-project/vllm/issues/65569)).
- **16-byte-aligned per-token-head KV slots** so Triton vectorizes loads in `TRITON_ATTN`; measured on RTX 3080 for int8/fp8 paths ([#62113](https://github.com/vllm-project/vllm/pull/62113)).
- **Connector-agnostic KV checksums** for P/D disaggregation — producer/consumer checksum match, mismatch treated as KV load failure ([#62211](https://github.com/vllm-project/vllm/pull/62211)).
- **Scheduler**: eliminating 1.0–1.8s re-queueing stall for completed async KV pulls via front-of-queue re-admission ([#62532](https://github.com/vllm-project/vllm/issues/62532)).
- **MooncakeConnector RFC** — primary P/D path on non-CUDA platforms (RDMA/TCP/Ascend) ([#60523](https://github.com/vllm-project/vllm/issues/60523)).

## 5. Stability & Regressions
Ranked by severity:
1. **CRITICAL — Cross-request context contamination** in PD disaggregation + automatic prefix caching (DeepSeek-V4.1-Flash): responses intermittently contain other requests' content; scriptable reproducer; workaround `--no-enable-prefix-caching` ([#61160](https://github.com/vllm-project/vllm/issues/61160)).
2. **HIGH — Qwen4Exp 262K single-prefill CUDA illegal memory access** on v0.32.0 and nightly, OK on v0.31.1 ([#62383](https://github.com/vllm-project/vllm/issues/62383)).
3. **HIGH — No working block size on v0.32.0** for DeepSeek-V4.1 (both 64 and 128 fail); fix tracked in #60762 ([#59203](https://github.com/vllm-project/vllm/issues/59203)).
4. **MEDIUM — DSpark draft 0% acceptance** for DeepSeek-V4 due to missing `packed_modules_mapping`; fused layers ignore `ignored_layers` ([#65618](https://github.com/vllm-project/vllm/issues/65618)).
5. **MEDIUM — SM120 rejects `enable_adaptive_verification`** (device-query-lens gated to SM90/SM100) ([#63343](https://github.com/vllm-project/vllm/issues/63343)).
6. **MEDIUM — Batch-invariance drift** on Qwen3.6 GDN greedy DFlash2 under preemption/recompute ([#62851](https://github.com/vllm-project/vllm/issues/62851)).
7. **LOW–MEDIUM — torch.compile stride-mismatch crash** at warmup for Qwen3.5/Qwen3-Next GDN attention (recurrence of #29014) ([#50046](https://github.com/vllm-project/vllm/issues/50046)).
8. **LOW — `--reasoning-parser qwen3`** leaves leading `\n\n` in content after stripping `<think>` ([#61972](https://github.com/vllm-project/vllm/issues/61972)).
9. **FIXED/CLOSED — Non-streaming client disconnect not detected**; engine runs to `max_tokens` and tokens count as delivered ([#61918](https://github.com/vllm-project/vllm/issues/61918)).
10. **CLOSED — PR fixing multimodal dummy-input validation against mm limits** (Pixtral embedding-only startup) ([#65689](https://github.com/vllm-project/vllm/pull/65689)).

## 6. What This Means for Application Developers
- **If you run P/D disaggregation with prefix caching, disable it until #61160 is fixed.** Cross-request crosstalk is a data-leak class bug, not just a quality bug.
- **Treat v0.32.0 with caution** for DeepSeek-V4 and long-context Qwen deployments; v0.31.1 is the safer pin for the 262K-prefill case, and block-size configs on the release tag are broken.
- **Ampere owners should not expect DeepSeek-V4-Flash** until #50576 lands — plan capacity on Hopper/Blackwell or AMD MI355X instead.
- **Batch-invariant mode has an avoidable perf tax** on Hopper/Blackwell; if you rely on it for reproducible evals, benchmark against defaults before assuming it's free.
- **KV checksums (#62211)** will give you a correctness backstop for multi-node P/D transfers — worth tracking if you operate NIXL/Mooncake topologies.
- **Request-ID-only logging (#64654)** is still pending; if you need gateway-to-engine correlation without content logging, it isn't available natively yet.
- **Smaller wins**: the wheel-shipping fix (#65756) matters if you build vLLM from source without `.git`; pin to a fixed release once available.

</details>

<details>
<summary><strong>SGLang</strong> — <a href="https://github.com/sgl-project/sglang">sgl-project/sglang</a></summary>

# SGLang Digest — 2026-11-05

## 1. Today's Highlights

No new releases landed in the last 24 hours, but issue and PR activity stayed high (61 updated issues, 500 updated PRs). The dominant themes are **PD disaggregation correctness** (a router circuit-breaker bug, a Mooncake KV-page-stride mismatch, an EAGLE decode corruption after retraction) and **AMD/Hopper kernel tuning** via AITER and FlashInfer 0.7.3. On the infra side, the CI flakes tracker (#42752, now 176 comments) and the Q4 PD disaggregation roadmap (#43699) remain the most active coordination threads.

## 2. Releases & Breaking Changes

No new releases today. Two forward-looking items with migration implications:

- **RFC: Complete migration to the shared `api.v1` backend API** ([#45816](https://github.com/sgl-project/sglang/issues/45816)) — moves `sgl-processor`, `sgl-router`, and the Rust server onto a unified `api.v1.Generate` schema. Expect interface churn in the router/processor path.
- **FlashInfer bump to 0.7.3 + `flashinfer.moe_ep` migration** ([#48468](https://github.com/sgl-project/sglang/pull/48468)) — 0.7.3 removes `flashinfer.comm.MoeAlltoAll`, so the bump and the NVLink one-sided all-to-all migration must land together. Relevant if you build against FlashInfer's MoE comm APIs.

## 3. New Model & Hardware Support

- **Kimi-K3.1 PCP KV sharding with HiCache + DFlash** ([#48469](https://github.com/sgl-project/sglang/pull/48469)) — shards persistent DSA KV pages across ranks instead of replicating, assembled for TRTLLM DSA attention.
- **Muse Spark Open cookbook: TPU v7** ([#48277](https://github.com/sgl-project/sglang/pull/48277)) — single host, 4 chips / 8 TensorCores, `--tp-size 8` serving NVFP4 via SGLang-TorchTPU.
- **GLM-5.2 / GLM-5.3 (`GlmMoeDsaForCausalLM`)**: breakable prefill CUDA graphs extended under interleave context-parallel prefill with fp8 KV + trtllm kernels ([#46890](https://github.com/sgl-project/sglang/pull/46890)).
- **DeepSeek-V4 TRTLLM sparse MLA**: caller-owned multi-CTA KV counter buffer replacing a private FlashInfer workaround ([#48467](https://github.com/sgl-project/sglang/pull/48467)); this also unblocks the PD-disaggregation uniform-FP8 KV layout request ([#37838](https://github.com/sgl-project/sglang/issues/37838), now closed inactive).

## 4. Performance & Optimization

- **AMD AITER prefill attention on gfx950 in deterministic mode** ([#45601](https://github.com/sgl-project/sglang/pull/45601)) — on MI350X, Qwen3-8B @ 32k input drops Triton prefill attention from **9.3s → 4.1s** with AITER batch prefill.
- **Kimi-K3 12-head MLA on gfx950 (FP8 KV)** ([#45320](https://github.com/sgl-project/sglang/pull/45320)) — moving decode + 8-token verify to fp8-Q ASM improves P90 ITL from **6.77ms → 6.18ms** on AgentX c1 traffic.
- **Paged experts without staging copy** ([#48082](https://github.com/sgl-project/sglang/pull/48082)) — reduces host memory footprint during checkpoint load by eliminating the pageable staging duplication.
- **`--routed-experts-dtype int16`** ([#47833](https://github.com/sgl-project/sglang/pull/47833)) — halves the routed-expert host cache for `--enable-return-routed-experts`.
- **LingBot-Video MoE TMA tuning for B300** ([#48470](https://github.com/sgl-project/sglang/pull/48470)) — MoE is ~39% of summed kernel time at 832×480×81; enabling existing up/down TMA paths targets that region.
- **Diffusion: image-prefix sequence parallelism for ERNIE-Image** ([#48218](https://github.com/sgl-project/sglang/pull/48218)) — fixes 4D spatial latent sharding that previously failed Ulysses2 requests pre-execution.

## 5. Stability & Regressions

Ranked by severity:

1. **Router circuit breaker dispatches to a dead decode, creating a permanently fake-dead prefill** ([#31206](https://github.com/sgl-project/sglang/issues/31206)) — sgl-router v0.2.4 PD mode; a single stuck prefill can poison cluster state. No fix PR referenced; open since July.
2. **Mooncake PD: mismatched KV page stride silently corrupts decode KV and overruns layer buffers** ([#48231](https://github.com/sgl-project/sglang/pull/48231), fixes [#48230](https://github.com/sgl-project/sglang/issues/48230)) — **fix PR open**. High impact: silent corruption, not a crash.
3. **Qwen EAGLE PD decode produces corrupted output after retraction** ([#48356](https://github.com/sgl-project/sglang/issues/48356)) — new yesterday; no fix PR yet.
4. **DSpark + TP: grammar-constrained request batched with any other request deadlocks all ranks** ([#41449](https://github.com/sgl-project/sglang/issues/41449)) — collective hang, both overlap and non-overlap modes.
5. **HiCache on hybrid GDN: host-tier hits drop to zero past ~3 long prefixes** ([#45400](https://github.com/sgl-project/sglang/issues/45400)) — host mamba pool exhaustion while KV remains on host; prefix-cache capacity cliff for long-context hybrid models.
6. **Mooncake HiCache backup fails for Qwen3.8-Flash-Next-FP8** ([#48145](https://github.com/sgl-project/sglang/issues/48145)) and **mamba lazy boundary snapshot overwrites the keep slot** ([#48328](https://github.com/sgl-project/sglang/pull/48328), fix PR open) — checkpointing integrity in hybrid/Mamba paths.
7. **`--tool-call-parser auto` finds no parser on Qwen2.5-0.5B, Qwen3 2507, Ling-mini-2.0** ([#48172](https://github.com/sgl-project/sglang/issues/48172)) — tool calls return as plain text; affects agent integrations on smaller models.
8. **DSV4 prefill throughput gap on SM120** ([#33422](https://github.com/sgl-project/sglang/issues/33422)) — ~2–7K tok/s vs ~12.5K with vLLM/marlin on 4× RTX PRO 6000; still unresolved, tuning guidance requested.

## 6. What This Means for Application Developers

- **If you run PD disaggregation**, audit your router version and Mooncake registration path: #31206 and #48231 are both silent-failure classes (one permanent dead-prefill state, one KV corruption). If you use EAGLE under PD, watch #48356 before rolling out retraction-heavy workloads.
- **Tool-calling agents**: `--tool-call-parser auto` is unreliable on several smaller/base models (#48172). Pin an explicit parser in production rather than relying on auto-detection.
- **Long-context hybrid-model users**: the HiCache host-tier cliff (#45400) means prefix reuse degrades sharply once evicted working sets exceed a few long prefixes — size host mamba pools and long-prefix concurrency accordingly.
- **AMD MI3xx/ROCm deployments** are getting real attention: deterministic-mode prefill attention and MLA verify kernels now have concrete latency wins in review, which matters for reproducibility-sensitive regulated workloads.
- **Observability**: a new time-to-first-answer-token histogram ([#48466](https://github.com/sgl-project/sglang/pull/48466)) will separate reasoning-token latency from answer latency — worth adopting for reasoning-model SLOs once merged.

</details>

<details>
<summary><strong>llama.cpp</strong> — <a href="https://github.com/ggml-org/llama.cpp">ggml-org/llama.cpp</a></summary>

# llama.cpp Digest — 2026-11-05

## 1. Today's Highlights
llama.cpp shipped **v0.9.0**, headlined by OCP FP8 E4M3 quantization, a new `flash_attn_ext_rows` op enabling efficient unified-KV multi-sequence inference, and a generic batched tensor copy API. Alongside this, four new architectures landed (Clover, Limite, Metis-1.6, Mistral Large 4), plus audio support for Drama-3 TTS. The release cycle was otherwise dominated by backend tuning (OpenCL/Adreno, Vulkan Intel) and a cluster of Intel GPU correctness/regression reports still under triage.

## 2. Releases & Breaking Changes
- **v0.9.0** (b12110 bumps the version): https://github.com/ggml-org/llama.cpp/releases — adds OCP FP8 E4M3 quant, `flash_attn_ext_rows`, batched tensor copy API, 4 new architectures, audio, Drama-3 TTS (b12116).
- **Unified KV cache efficiency for multi-sequence inference** via `flash_attn_ext_rows` — relevant to anyone running `llama-server` with `--parallel N>1`; this is a capability change worth validating against your batching paths.
- **`-hf` quant tag matching fix** (b12100): tags like `:F16` no longer match `BF16`, and `:Q4_XS` no longer matches `IQ4_XS` (now token-boundary anchored). If your tooling relied on loose tag matching, pin exact quant tags. https://github.com/ggml-org/llama.cpp/pull/31620
- **Server realtime hardening** (b12119): websocket handlers now route through `ex_wrapper` and emit error events instead of crashing on exceptions — behavior change for clients that previously saw a dropped connection on malformed tool schemas (see #31626). https://github.com/ggml-org/llama.cpp/pull/31632

## 3. New Model & Hardware Support
- **New architectures**: Clover, Limite, Metis-1.6, Mistral Large 4 (v0.9.0).
- **Drama-3 TTS** audio support in `mtmd` (b12116): https://github.com/ggml-org/llama.cpp/pull/31613
- **ricursiveAI Ripple‑65B** (new dense architecture, converter + graph, no new ops) — open PR: https://github.com/ggml-org/llama.cpp/pull/31692
- **CUDA TQ2_0 dequant + MMVQ** for ternary models (BitNet b1.58, BitCPM, Falcon3-1.58bit) so weights stay on GPU instead of CPU fallback — open PR: https://github.com/ggml-org/llama.cpp/pull/30278
- **Hexagon SIN/COS/MEAN ops** — open PR: https://github.com/ggml-org/llama.cpp/pull/31674
- **WebGPU**: group norm and timestep embedding kernels (for stable-diffusion.cpp downstream) — PRs #31490, #31695
- **SpacemiT vendor instruction prefixing** for upstream binutils compatibility — PR #31677
- Long-running request: **XDNA backend** (#21725, 37 comments, 39 👍) still open.

## 4. Performance & Optimization
- **OpenCL decode perf with FA** (b12118): more workgroups for Q4_K gate-up + GLU, FA handling for GQA 2/6 with head size 256 — Qualcomm-contributed Adreno path: https://github.com/ggml-org/llama.cpp/pull/31633
- **OpenCL Adreno X2**: f32 and Q5_K multi-column GEMV enabled by default for faster MTP verification — PR #31694; non-contiguous sigmoid — PR #31687; Q4_0 non-MoE GEMM bin kernel for Adreno X1 — PR #30947
- **GGUF writer**: host-buffer tensors written directly, skipping the staging copy (b12101) — meaningful for large model conversion/write throughput: https://github.com/ggml-org/llama.cpp/pull/31144
- **HIP fast-math enabled without regressions** — open PR: https://github.com/ggml-org/llama.cpp/pull/31625
- **Vulkan Intel prefill FA kernel** — open PR: https://github.com/ggml-org/llama.cpp/pull/29357
- **MTP conv snapshots batched into a single `set_rows`** for recurrent models (qwen4exp, glm5-*) — PR #31660, follow-up on #30878/#31443/#31584

## 5. Stability & Regressions (ranked)
1. **CUDA illegal memory access** — Gemma 4 31B + draft-MTP after KV rollback/clear on RTX 4060 Ti (#31077); also **qwen4exp + draft-MTP** abort with `GGML_ASSERT(buffer)` on server start (#30860). No fix PR linked yet — high severity for MTP users.
2. **Vulkan Intel nondeterminism** — KHR_coopmat returns different/wrong results on Arc Pro B70 (BMG G31, Mesa 26.2.5) for BERT-style decision models in F16 and Q8_0 (#31560); shared-memory budget undercount in coopmat_stage on RTX 4090 (#31629). Correctness — treat Intel Vulkan coopmat as unsafe for decision/classifier workloads until fixed.
3. **Vulkan Intel regressions** — Arc Pro B60: tg128 20.1→13.0 t/s, pp512 1117→821 t/s between 65840ed and 29c720e (#31557); Gemma 4 decode −5% bisected to 0bb496db (#30018); ANV FA scalar-path fallback causing O(N²) PP degradation (#27638).
4. **SYCL memory** — `--fit` undercounts usage leading to runtime overflow (#27595); prior extreme scratchpad with ngram-mod closed stale (#28860).
5. **Vulkan device loss** on AMD RX 6900 XT (#27076, stale).
6. **Server correctness** — `/v1/responses` reports truncated tool calls as completed and never emits `function_call_arguments.done` (#30773, contract violation); `/slots` restore yields no KV reuse on hybrid/recurrent/SWA models (#28194); router scheduler race on concurrent cold-start with `--models-max 1` (#28774).
7. **Fixed today**: realtime crash on invalid tool schema (#31626, closed via b12119); GGUFReader scalar-array OOB loop (PR #31627, closed).
8. **Hexagon** — HTP v73 MUL_MAT inf for n≥5 and FA/DeltaNet failures (#29473); vision encoder corruption by image shape on HTP v75 (#31104). Snapdragon 7-series effectively non-functional for now.

## 6. What This Means for Application Developers
- **Upgrade to v0.9.0 if you serve multi-sequence workloads** — `flash_attn_ext_rows` makes unified KV efficient under parallel decoding, directly improving `llama-server --parallel` memory/throughput. Validate FP8 E4M3 quant paths against your eval set before rolling out.
- **Tighten `-hf` quant tags**: the new token-boundary matching fixes a real footgun where `:F16` silently pulled BF16 weights. Audit any pinned model references.
- **MTP/speculative decoding remains the top risk area**: two open CUDA abort/IMA bugs (#31077, #30860) affect Gemma 4 and qwen4exp with `--spec-type draft-mtp`. If you rely on speculative decoding in production, pin to a known-good build and gate MTP behind a fallback to no-spec.
- **Avoid Intel Vulkan coopmat for classifier/decision models** until #31560 is resolved — nondeterministic outputs are worse than slow outputs for agent routing layers. Arc B-series also shows measurable regressions (#31557).
- **Realtime/websocket endpoints got safer**: invalid tool schemas now surface as error events rather than crashing the server — update client error handling to consume these events.
- **OpenAI-compat gaps matter for agent frameworks**: `/v1/responses` streaming lacks the terminal `function_call_arguments.done` event (#30773). If your SDK waits on it, you'll hang — either patch client-side or track the issue.

</details>

<details>
<summary><strong>Ollama</strong> — <a href="https://github.com/ollama/ollama">ollama/ollama</a></summary>

# Ollama Digest — 2026-11-05

## Today's Highlights
No new releases in the last 24h, but the issue tracker shows a cluster of native tool-calling and API-correctness regressions in v0.40.13/0.40.14 (Windows RTX 5090 runner disconnects, Qwen tool-call failures, Responses API truncation). On the PR side, MLX Qwen3/3.5 YaRN scaling landed, VRAM prediction for hybrid models is flagged as over-counting, and several fixes target OpenAI-compatible tool-result handling and SSE flushing.

## Releases & Breaking Changes
None. No new version tags in the last 24h. Note: several reports reference v0.40.13 and v0.40.14 as current; cloud `/api/usage` appears to have regressed (see Stability).

## New Model & Hardware Support
- **MLX Qwen3/Qwen3.5 YaRN scaling** — PR #19231 (closed) honors explicit RoPE scaling metadata, applies YaRN frequencies/amplitude to text and mRoPE while preserving non-rotary dims. Fixes #18262 (YaRN ignored above 262K) and #19129. https://github.com/ollama/ollama/pull/19231
- **MLX CUDA device rejection** — PR #19521 (closed) rejects unsupported CUDA architectures up front instead of failing later with a confusing load error. https://github.com/ollama/ollama/pull/19521
- **AutoTrust decision models on MLX** — PR #19399 (closed) adds text/vision AutoTrust routing. https://github.com/ollama/ollama/pull/19399
- **WSL2 RTX Spark N1X GPU memory discovery** — PR #19520 (open) patches GPU memory detection for unified-memory laptops under WSL2. https://github.com/ollama/ollama/pull/19520
- **Linux/Windows video input** — #19503 (open) requests MP4/MOV support for llama-server GGUF models (currently macOS/MLX only). https://github.com/ollama/ollama/issues/19503
- **Installer size** — #12268 and #19512 propose splitting GPU runtime bundles (CUDA 12/13, ROCm, Vulkan, CPU-only) to shrink Linux/Windows installers. https://github.com/ollama/ollama/issues/12268

## Performance & Optimization
- **VRAM prediction over-counts KV cache 4–8x for hybrid/SSM models** (Qwen3.5/3.8, Qwen3-Next) and ignores `OLLAMA_KV_CACHE_TYPE`, always billing f16 — issue #19463 (open). Causes premature eviction of `keep_alive=-1` runners. https://github.com/ollama/ollama/issues/19463
- **GGUF pull verification** — #19504 proposes verifying blobs during parallel Range download to avoid costly post-pull full re-hash ("verifying sha256 digest"). https://github.com/ollama/ollama/issues/19504
- **SSE streaming flush** — PR #17027 (open) flushes OpenAI-compatible streaming chunks promptly to fix apparent UI hangs (Chatbox/OpenWebUI). https://github.com/ollama/ollama/pull/17027
- **Apple Silicon GPU memory docs** — PR #19527 documents the unified-memory GPU cap and how to raise it. https://github.com/ollama/ollama/pull/19527
- **Sleep prevention during inference** — PR #19525 (open) adds server-side sleep inhibition. https://github.com/ollama/ollama/pull/19525

## Stability & Regressions
Ranked by severity:

1. **CUDA v13 grammar-constrained crash → Xid 79 (GPU off bus)** — #17026 (open). Long `format`-constrained generation on cuda_v13 crashes; identical workloads fine on cuda_v12. Highest severity (hardware-level). https://github.com/ollama/ollama/issues/17026
2. **Windows RTX 5090 runner disconnects during tool calling (HTTP 500 /tokenize)** — #19439 (open). Intermittent on 0.40.13 with Open WebUI. https://github.com/ollama/ollama/issues/19439
3. **Qwen2.5-Coder / Qwen3 fail native tool calling on 0.40.13** — #19524 (open). https://github.com/ollama/ollama/issues/19524
4. **Responses API silently truncates oversized input with truncation disabled** — #19492 (open), repro on 0.40.13 and 0.40.14. https://github.com/ollama/ollama/issues/19492
5. **Vision inference 500 on GPU** — `qwen2.5vl:3b` fails "token repeat limit reached" on GPU vision encoder; CPU works. #19483 (open). https://github.com/ollama/ollama/issues/19483
6. **Qwen2.5 template renders Go struct dump instead of JSON for `{{ .Function }}`** — #19529 (open); tool names misspelled, calls dropped. https://github.com/ollama/ollama/issues/19529
7. **Cloud `/api/usage` regression** — #19511 (open): session/weekly percentage fields dropped, only request counts returned; likely unintended regression. https://github.com/ollama/ollama/issues/19511
8. **Cloud `deepseek-v4-pro` rejects `max_tokens` > 65536** despite ~384k output capability — #19517 (open). https://github.com/ollama/ollama/issues/19517
9. **Long-standing TLS pull failure** — #3504 (open, since Apr 2024): `pull` manifest TLS handshake timeout, still reproducing. https://github.com/ollama/ollama/issues/3504

Fix PRs in flight: #19522 (keep tool-result text+image in one message, fixes #19479), #18767 (match tool result groups by `tool_call_id`, fixes #18762), #19526 (legacy templates preserve helpers/`define`), #19528 (Modelfile `FROM` quoting round-trip), #19408 (bound MLX System One prompt expansion, 413 on oversize).

## What This Means for Application Developers
- **Tool calling is the fragile surface right now.** If you depend on native tool calls with Qwen2.5/Qwen3 on Windows/GPU, pin to a known-good version and validate tool-name fidelity server-side; template JSON corruption (#19529) and runner disconnects (#19439) will surface as dropped or malformed calls.
- **Parallel tool results need `tool_call_id` matching.** Until #18767 lands, order-sensitive backends can misattribute results — send tool results in call order or normalize client-side.
- **Responses API truncation is silent** (#19492): enforce client-side input length checks rather than relying on server truncation flags.
- **Sizing estimates for hybrid/SSM models are unreliable** (#19463): budget VRAM conservatively and don't assume `OLLAMA_KV_CACHE_TYPE` is honored until fixed.
- **Cloud usage telemetry changed shape** (#19511, #12532): don't hard-code percentage fields in dashboards; request counts only may be available via API.
- **Streaming clients** should tolerate delayed chunk delivery until #17027 merges — long apparent hangs may be buffering, not inference stalls.

</details>

<details>
<summary><strong>LiteLLM</strong> — <a href="https://github.com/BerriAI/litellm">BerriAI/litellm</a></summary>

# LiteLLM Digest — 2026-11-05

## 1. Today's Highlights

LiteLLM pushed three releases in the last 24h (v1.108.0 stable, v1.106.7 patch, v1.109.0-rc.1), all gated on cosign image signature verification. The issue queue remains dominated by **streaming correctness and billing-accuracy bugs** — early client disconnects, mid-stream fallbacks, and budget-window accounting are all producing silent cost/quota gaps. A large 9-PR "control plane" stack (dataplane registry, OCSF audit events, SIEM sinks, fleet aggregation) is in active review, signaling a push toward multi-dataplane fleet management.

## 2. Releases & Breaking Changes

| Release | Notes |
|---|---|
| **v1.108.0** | Stable. All Docker images (`litellm`, `litellm-database`, `litellm-non_root`, `docker.io/litellm/*`) now signed with cosign; verify against key in commit `e82a3c6`. [Release](https://github.com/BerriAI/litellm/releases/tag/v1.108.0) |
| **v1.106.7** | Patch on the 1.106 line with the same cosign verification command. [Release](https://github.com/BerriAI/litellm/releases/tag/v1.106.7) |
| **v1.109.0-rc.1** | Release candidate — same signature story; expect API surface changes to settle here. [Release](https://github.com/BerriAI/litellm/releases/tag/v1.109.0-rc.1) |

**Model-map regression to watch:** `eu.anthropic.claude-haiku-4-5-20251001-v1:0` was removed from `model_prices_and_context_window.json` and the model map in #49113/23595f7fe, but AWS still serves it as an EU inference profile — deployments pinned to that ID will fail resolution. [#49312](https://github.com/BerriAI/litellm/issues/49312)

## 3. New Model & Hardware Support

- **QwenCloud migration path** requested for the existing DashScope provider (OpenAI- and Anthropic-compatible endpoints for Qwen, Wan, CosyVoice). [#36150](https://github.com/BerriAI/litellm/issues/36150) — closed.
- **Cloudflare Workers AI** image and audio support requests closed as stale/duplicate — not landing. [#35055](https://github.com/BerriAI/litellm/issues/35055), [#35056](https://github.com/BerriAI/litellm/issues/35056)
- **Rust router core** proposed as an alternative to the Python core, with the host as the only boundary (`PythonHost` bridge, no native executor path). In review. [#49513](https://github.com/BerriAI/litellm/pull/49513)
- No new CUDA/ROCm/Metal or quantization-format work surfaced today.

## 4. Performance & Optimization

- **MCP `tools/list` latency regression:** after upgrading v1.106.6 → v1.108.0, `tools/list` through `/mcp/` blocks **18–50s** for clients sending `notifications/initialized` with pre-2026 protocol versions (e.g. Python `mcp` SDK `ClientSession`, used by Open WebUI) — traced to the legacy subscription listener. [#50065](https://github.com/BerriAI/litellm/issues/50065)
- **Least-busy routing stampede fix:** requests arriving together read identical in-flight counts and pile onto one deployment; PR reserves each pick at selection time so the next request sees it as busy. [#47484](https://github.com/BerriAI/litellm/pull/47484)
- **Health probes evicting context:** with concurrency 1 against a 262k-context local model, health checks reportedly cause context eviction and periodic latency spikes as context fills. [#49930](https://github.com/BerriAI/litellm/issues/49930)
- **Prompt-cache misses on tool loops:** `/v1/messages` → Vertex Claude sends `messages` before `system`/`tools`, breaking prompt-cache hits across tool-loop rounds. [#50070](https://github.com/BerriAI/litellm/issues/50070)

## 5. Stability & Regressions

**Critical — billing/usage correctness:**
- **Streaming usage loss on early disconnect:** if a client aborts before the final usage chunk, all token/cost tracking for that request is dropped — billing gaps and inaccurate quotas. High severity for any metered deployment. [#14457](https://github.com/BerriAI/litellm/issues/14457) (open)
- **Budget-window spend drops in-flight requests:** `/key/update` during a live request means that request's cost never reaches `LiteLLM_BudgetWindowSpend` (spend logs and key `spend` are correct; only the window total is wrong). [#49179](https://github.com/BerriAI/litellm/issues/49179) — closed.
- **Session budget bypass under concurrency:** admission checks recorded spend but don't reserve capacity for in-flight calls, so concurrent requests can exceed `max_budget_per_session`. [#34732](https://github.com/BerriAI/litellm/issues/34732) (open)
- **Regional/speed multipliers lost on custom per-call prices** since #49532 — OpenAI data-residency, Vertex regional, and Anthropic geo-routing uplifts all dropped. Fix PR: [#50063](https://github.com/BerriAI/litellm/pull/50063). Related batch-cache pricing fallback: [#50062](https://github.com/BerriAI/litellm/pull/50062).

**High — streaming/fallback correctness:**
- **Fallback lost after reasoning-only prefix:** `sent_first_chunk` flips on any delta including reasoning, so a mid-stream failure during thinking is misreported as post-first-chunk and the fallback is skipped even though the client received nothing. [#49795](https://github.com/BerriAI/litellm/issues/49795) (open)
- **SSE keepalive commits a 200 before upstream failure:** with `sse_keepalive_ping_interval_seconds`, the first `: ping` commits the response, so later upstream errors can only be written into the body — Anthropic clients on `/v1/messages` get an SSE error payload instead of a proper failure. [#49711](https://github.com/BerriAI/litellm/issues/49711) (open)
- **Responses bridge duplicates tool-call arguments** in `/v1/chat/completions` streaming for `azure/responses`: first delta carries full arguments JSON, then argument deltas re-send fragments. [#48773](https://github.com/BerriAI/litellm/issues/48773) — closed.
- **Anthropic `server_tool_use` surfaced as pending tool_call** makes agent clients loop (finish_reason `stop` with both content and tool_calls). [#48384](https://github.com/BerriAI/litellm/issues/48384) (open)

**Medium — translation/provider edge cases:**
- `chatgpt/` provider drops encrypted reasoning on `/v1/messages` → never replayed between tool calls. [#49046](https://github.com/BerriAI/litellm/issues/49046)
- Azure Messages→Responses detour drops encrypted reasoning on resumed tool-call history (follow-up to #40451). [#50024](https://github.com/BerriAI/litellm/issues/50024)
- Bedrock Converse replays `reasoningText.signature` to non-Anthropic models (e.g. `zai.glm-5.3`) → 400. [#49969](https://github.com/BerriAI/litellm/issues/49969)
- Groq rejects history after streamed reasoning reply because `reasoning_content` is echoed back. [#47931](https://github.com/BerriAI/litellm/issues/47931) — closed.
- Databricks tool-only responses (no `content` key) raise `KeyError` in `resolve_reasoning_and_content`. [#48637](https://github.com/BerriAI/litellm/issues/48637)
- `text.format: json_schema` forwarded verbatim to providers that only support `json_object` → 400 surfaced as 500. [#49198](https://github.com/BerriAI/litellm/issues/49198)
- `bedrock/openai` invoke route ignores `model_id` (application inference profile ARN) → 400 `Unknown parameter`. [#49083](https://github.com/BerriAI/litellm/issues/49083)
- OTel `set_attributes` calls `.get()` on non-dict responses (`DecisionsResponse`, MCP `CallToolResult`, list) → crash. [#48719](https://github.com/BerriAI/litellm/issues/48719)
- Sticky sessions not honored — router sends to a different endpoint. [#50029](https://github.com/BerriAI/litellm/issues/50029)

**Guardrails:**
- MCP tools sent via Anthropic `/v1/messages` are invisible to `custom_code` and `tool_permission` guardrails in `pre_call` — they can't be inspected or blocked. [#40583](https://github.com/BerriAI/litellm/issues/40583) — closed.
- Streamed multi-choice completions inspect all choices' tool calls together, causing missed masking and false per-choice limit trips. Fix PR: [#46860](https://github.com/BerriAI/litellm/pull/46860).

## 6. What This Means for Application Developers

1. **Don't trust usage numbers for aborted streams.** If your app cancels streaming requests (timeouts, user navigations, agent step budgets), those tokens may never be billed or counted against quotas. Budget for this gap until #14457 lands, and avoid relying on LiteLLM-side spend as your sole source of truth for usage-based pricing.
2. **Mid-stream reasoning fallbacks are unreliable.** With reasoning models, a provider failure while "thinking" will not trigger your configured fallback (#49795), and keepalive-enabled streams can't surface upstream errors cleanly (#49711). If you need resilient reasoning-model serving, prefer non-streaming or implement client-side retry/fallback.
3. **Pin model IDs, verify images.** v1.108.0 is stable; the Claude Haiku EU inference-profile removal (#49312) is a live footgun for anyone using regional Bedrock IDs. Verify cosign signatures before rollout — that's now the documented release contract.
4. **Agent-loop UX regressions are real.** Anthropic `server_tool_use` as pending tool_call (#48384) and dropped encrypted reasoning on the `chatgpt/` path (#49046, #50024) both cause looping or lost reasoning history for Claude Code-style clients on `/v1/messages`. If you route agent traffic through the Anthropic-compatible endpoint, test tool loops explicitly.
5. **MCP gateway latency:** if you upgraded to v1.108.0 and use MCP with the Python SDK client, expect multi-tens-of-seconds `tools/list` stalls (#50065) — pin v1.106.6 or wait for a fix if MCP discovery is on your critical path.
6. **Control-plane direction:** the 9-PR stack (dataplane registry → fan-out aggregates → audit/OCSF/Splunk/Sentinel/S3-Object-Lock sinks → OTel spans) points to a future where one LiteLLM control plane governs many dataplanes. Worth tracking if you run multi-cluster gateway fleets; nothing to adopt yet. Start at [#49655](https://github.com/BerriAI/litellm/pull/49655).

</details>

<details>
<summary><strong>Unsloth</strong> — <a href="https://github.com/unslothai/unsloth">unslothai/unsloth</a></summary>

# Unsloth Digest — 2026-11-05

## 1. Today's Highlights
Unsloth shipped **v0.1.915-beta**, adding **Qwen3.8-Flash-Next** support alongside Glass theme polish, a Composer position slider, and built-in browser file interaction. On the infrastructure side, the big in-flight work is **CUDA-graph decoding enabled by default in plain `model.generate()`** ([#16691](https://github.com/unslothai/unsloth/pull/16691)) plus a wave of **multi-GPU diffusion placement** PRs from danielhanchen. Stability remains a concern: a cluster of desktop/Studio regressions landed today, most notably **UI lag on long conversations** ([#16603](https://github.com/unslothai/unsloth/issues/16603)) and a recurring **disappearing `search_conversation` tool** ([#16602](https://github.com/unslothai/unsloth/issues/16602), [#16706](https://github.com/unslothai/unsloth/issues/16706)), with a **transformers 5.23 compatibility break** flagged for the training stack.

## 2. Releases & Breaking Changes
- **v0.1.915-beta** — Improved Customization + Glass + Browser Use: adds Qwen3.8-Flash-Next, Glass themes (Beta), Composer position slider, lower idle memory, **preserved chat context on GGUF model switch**, faster MLX loading, FP8 improvements. No documented breaking API changes.
- ⚠️ **transformers 5.23 renamed gradient checkpointing → activation checkpointing** ([#16667](https://github.com/unslothai/unsloth/issues/16667)): old names remain as aliases until v6, but Trainer now reads only new names. **4 spots in Unsloth change behaviour** — audit before bumping transformers.
- **Install/docs mismatch**: advertised `brew install --cask unsloth` fails — no Cask exists yet ([#16672](https://github.com/unslothai/unsloth/issues/16672)).
- **Torch 2.13/2.14 extras missing from PyPI** ([#16520](https://github.com/unslothai/unsloth/issues/16520), closed) — verify wheel availability before relying on `cu130-2130`-style extras.

## 3. New Model & Hardware Support
- **Qwen3.8-Flash-Next** — added in v0.1.915-beta.
- **Inception Mercury (diffusion LLM) preset** with live diffusion streaming ([#16508](https://github.com/unslothai/unsloth/pull/16508)).
- **Multi-GPU diffusion (FLUX.1/FLUX.2)**: automatic partner-card selection and second-card copy ([#16682](https://github.com/unslothai/unsloth/pull/16682), [#16687](https://github.com/unslothai/unsloth/pull/16687)).
- **HunyuanVideo-1.5 small-host planner** fixes on 15 GB cards / 31 GB hosts (Kaggle T4) ([#16700](https://github.com/unslothai/unsloth/pull/16700)).
- **Qwen-Image** now keeps bf16 text encoder automatically when VRAM allows ([#16678](https://github.com/unslothai/unsloth/pull/16678)).
- Backends touched: CUDA, MLX (faster loading), FP8, GGUF.

## 4. Performance & Optimization
- **CUDA-graph decode by default in `generate()`** ([#16691](https://github.com/unslothai/unsloth/pull/16691)): Qwen3-0.6B launches ~675 kernels/step (1070–1430 with LoRA); on T4 GPU busy only a fraction of each 30–75 ms step. Captured decode is **bitwise equal** to eager, with memory release and eager fallback.
- **RoPE kernel Triton specialization fix** ([#16690](https://github.com/unslothai/unsloth/pull/16690)): marks `seqlen` as non-specialized to avoid duplicate kernel variants between decode (len=1) and prefill.
- **SDXL pipeline load: 24–25 s → faster** on L4 by reading v-prediction markers once and skipping the UNet scheme probe (~6 s saved) ([#16696](https://github.com/unslothai/unsloth/pull/16696)).
- **Qwen-Image quality/latency**: int8 ConvRot encoder was most of the LPIPS gap to bf16 (0.0865 mean, 0.401 worst prompt); auto-bf16 when resident ([#16678](https://github.com/unslothai/unsloth/pull/16678)).
- **Lower idle memory + faster MLX loading** in v0.1.915-beta.
- **tok/s badges for API providers** ([#16506](https://github.com/unslothai/unsloth/pull/16506)) and **real token usage/context window for custom connections** ([#16505](https://github.com/unslothai/unsloth/pull/16605)) — observability improvements.

## 5. Stability & Regressions
Ranked by severity:
1. **UI lag on long conversations (web + desktop)** — open regression, no fix PR yet ([#16603](https://github.com/unslothai/unsloth/issues/16603)).
2. **`search_conversation` tool disappears (2nd and 3rd occurrences)** — recurring; recurrence after closure suggests incomplete fix ([#16602](https://github.com/unslothai/unsloth/issues/16602), [#16706](https://github.com/unslothai/unsloth/issues/16706)).
3. **Chat template drops arbitrary `chat_template_kwargs`** (only 3 keys pass through; breaks e.g. Sage-A1.5 `verification`) ([#16545](https://github.com/unslothai/unsloth/issues/16545), closed).
4. **gemma-4-thinking chat template removes reasoning** ([#16699](https://github.com/unslothai/unsloth/issues/16699)).
5. **Studio `web_search` always "No results found"** — missing Accept header (DDG 202) + yandex missing from allow-list ([#16664](https://github.com/unslothai/unsloth/issues/16664)).
6. **S3 audio ingestion correctness**: single-record `.json` manifests skip path rewriting ([#16630](https://github.com/unslothai/unsloth/issues/16630)); prefix-root collisions silently select wrong recording ([#16528](https://github.com/unslothai/unsloth/issues/16528)) — **silent training-data corruption**.
7. **Fine-tuning jobs API drops cancelled queued job** when a newer job starts ([#16632](https://github.com/unslothai/unsloth/issues/16632)).
8. **Mixed ShareGPT + native tool calls lose tool-call metadata** ([#16051](https://github.com/unslothai/unsloth/issues/16051), closed).
9. **AppImage fails on Slackware64-current** (GLib/AppIndicator symbol lookup) ([#16605](https://github.com/unslothai/unsloth/issues/16605)).
10. **Native quant test order-dependence** fixed ([#16704](https://github.com/unslothai/unsloth/pull/16704), closed); several desktop update-failure reports ([#16573](https://github.com/unslothai/unsloth/issues/16573), [#16561](https://github.com/unslothai/unsloth/issues/16561), closed).

## 6. What This Means for Application Developers
- **Free default speedup for local serving**: the CUDA-graph decode path in plain `generate()` is bitwise-identical and opt-out via eager fallback — expect material tok/s gains on consumer GPUs (T4-class) without code changes.
- **Pin transformers <5.23** until the checkpointing rename is fully absorbed; the Trainer behaviour change can silently alter training results.
- **Don't trust surface-level chat-template behavior**: `chat_template_kwargs` filtering means provider-specific flags (e.g. verification/agentic modes) are dropped — validate end-to-end if you depend on them.
- **Gateway integrations**: custom headers for remote providers are still unsupported ([#14811](https://github.com/unslothai/unsloth/issues/14811), closed) — plan LiteLLM-style tracking/feature-selection headers accordingly; custom-connection usage/context reporting is improving.
- **Agent tooling**: browser script execution is getting an "Always allow" option ([#16707](https://github.com/unslothai/unsloth/pull/16707)) — relevant for unattended agents; sandbox granularity requests ([#16625](https://github.com/unslothai/unsloth/issues/16625)) and persistent project/agent memory ([#10775](https://github.com/unslothai/unsloth/issues/10775)) signal where the agent platform is heading.
- **Training pipelines**: audit S3/single-record manifest handling before pushing datasets — the two audio bugs produce incorrect training pairs without errors.

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar).*