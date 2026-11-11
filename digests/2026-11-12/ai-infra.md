# AI 基础设施日报 2026-11-12

> 生成时间: 2026-11-11 23:28 UTC | 覆盖项目: 6 个

- [vLLM](https://github.com/vllm-project/vllm)
- [SGLang](https://github.com/sgl-project/sglang)
- [llama.cpp](https://github.com/ggml-org/llama.cpp)
- [Ollama](https://github.com/ollama/ollama)
- [LiteLLM](https://github.com/BerriAI/litellm)
- [Unsloth](https://github.com/unslothai/unsloth)

---

## 横向对比

⚠️ 横向对比生成失败。

---

## 各项目详细报告

<details>
<summary><strong>vLLM</strong> — <a href="https://github.com/vllm-project/vllm">vllm-project/vllm</a></summary>

# vLLM 摘要 — 2026-11-12

## 1. 今日亮点

过去 24 小时内没有新版本发布，但活动高度集中在 AMD/ROCm 优化上：Qwen3.8 和 DeepSeek-V4.1 的 MI355X（gfx950）跟踪器非常活跃，新 PR 为 DSpark 和 GLM-5.3 推进 monokernel/fused-launch 路径。在 NVIDIA 方面，用“Humming”内核系列跨架构替换 Marlin 的计划获得了更多关注，而若干正确性 bug（MXFP8 激活方案选择、机密计算垃圾输出、detokenizer 卡顿）仍未解决。

## 2. 版本发布与破坏性变更

过去 24 小时内无。

## 3. 新模型与硬件支持

- **NVIDIA Nemotron 3.5 ASR Streaming** — 针对 `nemotron-3.5-asr-streaming-0.6b`（600M 多语言流式 ASR）的功能请求。仍处于开放状态，未引用实现 PR。[#47455](https://github.com/vllm-project/vllm/issues/47455)
- **HiSparse 上的 GLM-5.3** — PR 使 HiSparse 在 `GLM-5.3-Flash`（`Glm5NextForConditionalGeneration`）上可用，这是 HiSparse 上的首个混合模型（11/45 层为稀疏 MLA，其余为 KDA）。修复了启动失败和每请求 GPU 内存开销问题。[#61243](https://github.com/vllm-project/vllm/pull/61243)
- **AMD ROCm**：面向 CDNA4/gfx950 的 AITER Triton MXFP8 稠密线性内核已添加到 ROCm MXFP8 内核列表中。[#64089](https://github.com/vllm-project/vllm/pull/64089)
- **AMD ROCm**：在 GLM-5 解码工作的基础上，为 GLM-5.3 添加了专用解码路径。[#67095](https://github.com/vllm-project/vllm/pull/67095)

## 4. 性能与优化

- **Marlin → Humming 迁移（NVIDIA）**：跟踪 RFC，旨在淘汰 SM80 时代的 Marlin 内核。Humming 现已覆盖 INT4/INT8 GPTQ/AWQ、FP8、MXFP8/MXFP4/NVFP4、MXFP6，以及面向稠密层和 MoE 层的 INT8/FP8 激活，并为 Hopper/Blackwell 提供架构专属路径。[#65560](https://github.com/vllm-project/vllm/issues/65560)
- **面向 W4A8-INT8 的 PTX 9.4 `ldmatrix.s8.s4`**（已关闭）：在 W4A8-INT8 路径中采用了硬件 INT4→INT8 扩展加载。[#49529](https://github.com/vllm-project/vllm/issues/49529)
- **ROCm MI355X DeepSeek-V4.1**：PR 将 MXFP4 稀疏索引器解码行在 TP 间分片，消除了每个 rank 上冗余的 top-512 索引计算（MI355X，TP8）。[#66047](https://github.com/vllm-project/vllm/pull/66047)
- **ROCm DSpark monokernel**：每轮 Markov 采样只需一次 Triton 启动（此前为每轮多个内核），5 个 draft 层只需一次持久化 FlyDSL 启动（此前约 70 个内核 + 11 次 all-reduce）。[#66051](https://github.com/vllm-project/vllm/pull/66051)、[#65985](https://github.com/vllm-project/vllm/pull/65985)
- **批处理不变性调优**：在 `VLLM_BATCH_INVARIANT=1` 下为 `Qwen2.5-0.5B` 优化的 matmul 配置，在 sm120 上声称小批量响应延迟最多降低 **2.84×**（TTFT 基本不变）。[#67079](https://github.com/vllm-project/vllm/pull/67079)
- **注意力后端开销**：报告指出 Triton/eager 注意力后端每次调用都会重新推导 K/V 缓存视图——对切片调用 PyTorch `.contiguous()` 可能在主机上执行并复制整个 KV 缓存；影响被描述为步骤时间的“相当大一部分”。[#64572](https://github.com/vllm-project/vllm/issues/64572)
- **ROCm 跟踪器**（开放，堆叠 PR）：Qwen3.8-2.4T-A95B MI355X [#57149](https://github.com/vllm-project/vllm/issues/57149)、Qwen3.8-Flash-Next MI355X [#59575](https://github.com/vllm-project/vllm/issues/59575)、Muse Spark PTPC-FP8 gfx942 [#64018](https://github.com/vllm-project/vllm/issues/64018)、DeepSeek-V4.1-Flash MI355X [#66587](https://github.com/vllm-project/vllm/issues/66587)
- **KV 卸载**：TP 无关的 KV 卸载跟踪器（跨 TP 规模共享已卸载的 KV），正在寻求审阅者/负责人。[#61886](https://github.com/vllm-project/vllm/issues/61886)

## 5. 稳定性与回归

按严重程度排序：

1. **机密计算下的静默垃圾输出** — V2 模型运行器对固定主机内存的 UVA 视图在 CC 下会失效；使用 `VLLM_USE_V2_MODEL_RUNNER=0` 可正常工作。静默错误输出是最严重的故障类别。未显示修复 PR。[#57224](https://github.com/vllm-project/vllm/issues/57224)
2. **MXFP8 仅权重量化检查点静默地以 W8A8 运行** — `init_mxfp8_linear_kernel` 缺少 `activation_quant_key` 参数（与 MXFP4/MXFP6 同类不同），因此 W8A16 检查点会以错误的激活精度执行。静默精度损坏。未显示修复 PR。[#65193](https://github.com/vllm-project/vllm/issues/65193)
3. **增量 detokenization 在畸形 UTF-8 上为 O(n²)** — 每一步都会保留并重新解码整个不完整运行；可能阻塞 API 服务器事件循环（`/health` 挂起）。修复 PR 已开放：[#63668](https://github.com/vllm-project/vllm/pull/63668)（issue [#63667](https://github.com/vllm-project/vllm/issues/63667)）
4. **Qwen3.8-27B GDN 混合模型，sm_86 上 PP=3** — 启用前缀缓存时出现非法内存访问（Xid 31），不启用时输出乱码；自 v0.28.0 至 v0.32.0 的回归，两种模型运行器均受影响。（已关闭；请验证修复版本。）[#63505](https://github.com/vllm-project/vllm/issues/63505)
5. **MRV2 上 EAGLE draft 接受率骤降** — 需要比 `--block-size` 更小内核块的目标后端（例如 MiniMax-M3 的 128 token 页）会破坏 draft 注意力。修复 PR 已开放：[#64777](https://github.com/vllm-project/vllm/pull/64777)
6. **ROCm `ROCM_ATTN` 在 RDNA 上注意力错误 / GPU 故障** — KV 块大小 ≠16 时 head size ≥96，以及 MHA head size 192/256。[#62697](https://github.com/vllm-project/vllm/issues/62697)
7. **GLM-5.3 TP8 B200 HiSparse 启动失败**，一旦 KV 溢出到主机（MNNVL all-reduce）。已关闭。[#64327](https://github.com/vllm-project/vllm/issues/64327)
8. **Qwen3.5-122B-A10B-FP8 在提交 `0fae39a` 处 GSM8K 精度回归**。[#65906](https://github.com/vllm-project/vllm/issues/65906)
9. **Pythonic 工具解析器丢弃以 `[` 开头的流式文本** — 非工具调用的方括号文本在流式模式下消失，但在非流式模式下出现。修复 PR 已开放：[#67101](https://github.com/vllm-project/vllm/pull/67101)
10. **其他开放修复**：Gemini4DSpark 权重加载器迁移 [#54069](https://github.com/vllm-project/vllm/pull/54069)、Gemma 4 PLE CPU 卸载绕过 [#62354](https://github.com/vllm-project/vllm/pull/62354)、FlashInfer 单边 workspace 拆除泄漏 [#63874](https://github.com/vllm-project/vllm/pull/63874)、ROCm AITER 有偏 top-k NaN 传播 [#67107](https://github.com/vllm-project/vllm/pull/67107)

## 6. 对应用开发者的意义

- **固定并验证量化部署。** W8A16→W8A8 MXFP8 bug（#65193）会在仅权重量化检查点上产生*静默错误*输出——不崩溃、无警告。升级后请对任何 MXFP8 检查点运行小型评估套件（例如 GSM8K 子集）。
- **审计机密计算部署。** 如果你在 TDX/CC 模式下运行 vLLM，要么固定到禁用 V2 的运行器配置，要么验证输出，因为垃圾生成是静默的（#57224）。
- **流式工具调用 agent**：如果你使用 `--tool-call-parser pythonic` / `llama4_pythonic`，请注意响应开头的方括号纯文本在流式模式下会被丢弃。如果你的 agent 会输出引用风格的 `[1]` 前缀，值得跟踪修复 PR（#67101）。
- **长时间运行的 API 服务器**若使用字节级 tokenizer 并偶尔出现畸形 UTF-8：O(n²) detokenizer 路径可能阻塞事件循环并导致健康检查挂起——这与 Kubernetes 存活探针相关（#63667）。
- **AMD MI355X 采用者**将在 Qwen3.8、DeepSeek-V4.1、GLM-5.3 和 Muse Spark 上获得持续的性能提升；如果服务这些模型，预计会从稀疏索引器分片和 monokernel 启动中获得显著的解码路径收益。
- **批处理不变性用户**（`VLLM_BATCH_INVARIANT=1`）在小模型/小批量下获得可测量的延迟收益；MEAN 池化在该标志下也变得可复现（#60035，已关闭）。

</details>

<details>
<summary><strong>SGLang</strong> — <a href="https://github.com/sgl-project/sglang">sgl-project/sglang</a></summary>

# SGLang 摘要 — 2026-11-12

## 1. 今日亮点

过去 24 小时内没有新的版本发布；活动主要集中在**一个可能由 v0.5.23 引入的 SM90 解码回归**（#49336，在并发 12 下，DeepSeek-V4.1-Flash 的每步解码时间增加 33–45%）以及同一版本中 **GLM-5.3-Flash 在 100 万 token 请求后出现的引擎状态损坏 bug**（#48964）。积极的一面是，**FlashInfer 0.7.4 版本升级**（#49771）和一批 HiCache/DCP 正确性修复正在推进，**GLM-5.3-Flash / Qwen3.8-Flash-Next** 支持矩阵也在 Hopper 和 NPU 后端持续扩展。

---

## 2. 版本发布与破坏性变更

过去 24 小时内没有版本发布。有两项需要标记为前瞻性兼容性信号：

- **PR #49771 — 将 FlashInfer 升级至 0.7.4**（开放中）：更新了 `docker/Dockerfile`、`docker/Dockerfile.cu134` 及相关发布脚本中的版本锁定。对构建自定义镜像的用户有影响。https://github.com/sgl-project/sglang/pull/49771
- **PR #49638 — `[AMD]` 在确定性模式下拒绝显式 AITER 融合**（开放中）：显式请求的 `--enable-aiter-allreduce-fusion` 现在会在确定性模式下抛出可操作的错误（包括由 `--rl-on-policy-target` 隐含的情况）。此前会静默降级的配置现在将快速失败。https://github.com/sgl-project/sglang/pull/49638

---

## 3. 新模型与硬件支持

**模型**
- **Phi-4-multimodal W4A16 (AutoRound) 在 CPU 上** — PR #49482 为 `INCModel4/Phi-4-multimodal-instruct-W4A16-AutoRound` 添加了 CPU 支持，绕过了 `processor_config.json` 中缺失的 `audio_compression_rat...` 字段问题。https://github.com/sgl-project/sglang/pull/49482
- **GLM-5.3-Flash 在 Hopper (SM90) 上** — 正在积极跟踪性能（#45373）；PCP 支持已经落地（#42044）。Bug 排查工作同步进行中。https://github.com/sgl-project/sglang/issues/45373
- **Qwen3.8-Flash-Next 在 NPU 上** — PR #37570 添加了图重放、NEXTN/MTP 投机解码以及用于 QSA 的 Triton 内核。依赖 `sgl-kernel-npu#807`。https://github.com/sgl-project/sglang/pull/37570
- **Kimi-K3 PCP** — 仍在 Prefill CP 路线图上开放（#21788）。https://github.com/sgl-project/sglang/issues/21788

**硬件 / 内核**
- **通过 FlashInfer cuTile 实现 SM12x NVFP4 MoE** — 功能请求 #49123 提议将 cuTile NVFP4 MoE 作为 RTX 50xx / RTX PRO Blackwell 上的默认运行器。https://github.com/sgl-project/sglang/issues/49123
- **SM12x CUDA 13 开箱即用** — 仍然存在问题：#49209 报告在 sm_120 上使用标准 CUDA 13 wheel 无法运行任何模型。对消费级 Blackwell 用户影响较大。https://github.com/sgl-project/sglang/issues/49209
- **XGrammar 0.2.9 + Lark 语法支持** — PR #39380 将 `compile_lark` 接入语法 API，并将 XGrammar 锁定到 0.2.9。https://github.com/sgl-project/sglang/pull/39380
- **NVFP4 KV 路线图** — 因不活跃而关闭（#29913），其中引用了 SM120/B200 的初始 PR。https://github.com/sgl-project/sglang/issues/29913

---

## 4. 性能与优化

- **DSv4.1 图像预处理缓存**（PR #45287） — 多轮智能体重复发送图像时，目前会在 tokenizer 进程中每轮重新解码和重新 patchify；此 PR 按图像字节 + patchify 后端进行缓存。对视觉智能体的 TTFT 有显著收益。https://github.com/sgl-project/sglang/pull/45287
- **KDA `fused_recurrent_kda` 原地状态更新**（PR #48743，已关闭） — 消除了 KDA 解码中每个请求对 fp32 `[HV, V, K]` 状态（24 头 × 128）的 gather/scatter 操作。https://github.com/sgl-project/sglang/pull/48743
- **KDA 投机 ReplaySSM 环形缓冲区移入请求子池**（PR #49720） — 在 `--enable-unified-memory` 下，缓存的前缀检查点不再每次提交都携带一个未被读取的环形缓冲区，减少了每个检查点的内存占用。https://github.com/sgl-project/sglang/pull/49720
- **Diffusion head-128 均方融合**（PR #49308） — 在 B300 上融合了 nomas QK RMSNorm+RoPE 中的 cast/square/mean 链。https://github.com/sgl-project/sglang/pull/49308
- **自定义 all-reduce v2：每组一个对称工作区**（PR #49382） — 解耦了不同内核族之间的大小和启动宽度。https://github.com/sgl-project/sglang/pull/49382
- **AMD Triton 缓存泛滥修复**（PR #33506） — `_get_valid_kv_indices_kernel` 在 DSA aiter 后端中会因不同的索引形状而反复重新编译。https://github.com/sgl-project/sglang/pull/33506
- **调度器：UniBoost 饥饿有界策略 RFC**（#48519） — 为智能体工作负载提出了一种等待有界策略，因为 `lpm`/`shortest-prefill-first` 可能导致长共享前缀请求饥饿。https://github.com/sgl-project/sglang/issues/48519
- **`import sglang` 延迟** — 在 H100 上仍为 12 秒（#10492，good-first-issue，高优先级）。https://github.com/sgl-project/sglang/issues/10492

---

## 5. 稳定性与回归

按严重程度排序：

1. **🔴 SM90 解码回归 v0.5.22 → v0.5.23**（#49336）：在 8×H20-3e 上使用 DeepSeek-V4.1-Flash + DSPARK、并发 12 时，每步解码时间增加 33–45%，覆盖 8K–64K 上下文。`use_deep_select_decode` 在 Hopper 上也会降低接受率。尚无修复 PR 引用 — **如果在 SM90 + 投机解码上运行，请锁定到 v0.5.22**。https://github.com/sgl-project/sglang/issues/49336
2. **🔴 GLM-5.3-Flash 在 100 万 token 请求后引擎状态损坏**（#48964）：MTP 接受率降至 0.00，所有后续请求退化；在 v0.5.22 上正常。TP=4 H100，w4afp8，EAGLE。长时间运行的服务器面临静默降级风险。https://github.com/sgl-project/sglang/issues/48964
3. **🟠 每次前缀缓存命中时的静默精度损失**（#40903）：DeepSeek MLA 分块前缀预填充将 base-2 LSE 合并到自然对数 `merge_state_v2` 中，未做转换。长期存在，影响缓存响应的正确性。https://github.com/sgl-project/sglang/issues/40903
4. **🟠 多 tokenizer 模式下的跨客户端 token 泄漏**（#49903）：重复 `rid` 状态合并可能导致一个客户端的 token 被提供给另一个客户端。安全相关；新提交。https://github.com/sgl-project/sglang/issues/49903
5. **🟠 GLM-4.7-Flash W4A16 compressed-tensors 加载失败**（#49369，已关闭）：MLA 加载后读取 `kv_b_proj.weight`，而打包层缺少该权重。修复已落地。https://github.com/sgl-project/sglang/issues/49369
6. **🟠 Qwen4-Exp PLE：BF16 n-gram 分片以单位缩放转换为 FP8**（#49493）：配置中 `ple_embedding_dtype: float8_e4m3fn` 在约 6.7k 下载量的检查点上触发错误转换。https://github.com/sgl-project/sglang/issues/49493
7. **🟡 L2/L3 压力下 HiCache 命中率下降**（#47481）：HiCache + Mooncake 下可复现的从 87.4% 的瞬态下降；注意基准测试噪声。https://github.com/sgl-project/sglang/issues/47481
8. **🟡 API 不一致：`/v1/responses` 流式** 对上下文长度超限返回 HTTP 200 + `response.failed`，而 `/v1/chat/completions` 正确返回 400（#48557）。客户端错误处理需特殊处理。https://github.com/sgl-project/sglang/issues/48557
9. **🟡 Rust 服务器：`/generate` 文本提示缺少 BOS**，当 tokenizer 依赖 `add_bos_token` 时（#49519）。修复正在通过 HiCache/DCP PR 推进（#49943、#49917）。https://github.com/sgl-project/sglang/issues/49519
10. **🟡 编码器-解码器 KV 双重释放**，当 `page_size > 1` 时（#38840，因不活跃而关闭 — 可能仍然存在）。https://github.com/sgl-project/sglang/issues/38840

另需注意：flaky-CI 跟踪器（#42752）已有 198 条评论，以及一个新的 diffusion 性能运行器 flakiness 问题（#48311）— CI 信号目前噪声较大，在把关升级时将绿/红视为比平时更不可靠。

---

## 6. 对应用开发者的意义

- **不要在 Hopper 上全面升级到 v0.5.23。** 如果你在 SM90 上运行 DeepSeek-V4.1、GLM-5.3-Flash 或任何 EAGLE/MTP 投机解码，请留在 v0.5.22，直到 #49336 和 #48964 有修复。GLM bug 对长生命周期智能体服务器尤其棘手：故障是静默的（接受率 → 0），而非崩溃。
- **前缀缓存尚未完全可信。** #40903 意味着 DeepSeek MLA 在分块前缀预填充 + 缓存命中时存在可测量的精度漂移。如果你依赖确定性输出，请在禁用前缀缓存的情况下评估，或锁定到已知良好的版本。
- **多 tokenizer 部署：审计请求 ID 生成。** #49903 是一个跨客户端数据隔离 bug — 正是多租户网关最关心的问题类别。
- **智能体/视觉工作负载成本降低：** DSv4.1 图像预处理缓存（#45287）针对多轮视觉智能体的主要成本（每轮重新编码重发的图像）。合并后值得进行基准测试。
- **如果构建自定义容器，等待 FlashInfer 0.7.4**（#49771）以避免两次升级带来的变动；AITER 确定性模式变更（#49638）会将此前静默的配置冲突暴露为硬错误 — 检查你的 `--enable-aiter-allreduce-fusion` 使用情况。
- **SM12x（RTX 50 系列）仍不是开箱即用的支持路径**（#49209）；cuTile NVFP4 MoE 默认（#49123）是修复方向但尚未落地。

</details>

<details>
<summary><strong>llama.cpp</strong> — <a href="https://github.com/ggml-org/llama.cpp">ggml-org/llama.cpp</a></summary>

# llama.cpp 摘要 — 2026-11-12

## 今日亮点

硬件后端繁忙的一天：CUDA 落地了 OCP FP8 支持（GEMV 场景配合 W8A16），Vulkan 在 CoopMat2 下启用了反量化 KV-cache 优化，核心 llama 也落地了 FP8 KV-cache 支持。服务端方面，"层优先预填充"提案（#32082）声称在 PCIe 受限主机上配合 `-cmoe` 可为 MoE 模型带来约 2 倍预填充加速。问题追踪器持续累积量化相关的正确性报告（量化目标上的投机解码发散、FP8/F16 累加器溢出），其中若干已作为陈旧问题关闭。

---

## 版本发布与破坏性变更

- **b12289** — 测试现仅使用公开的 ggml API（[release](https://github.com/ggml-org/llama.cpp/releases)）。为测试而 patch 过 ggml 内部实现的下游项目需要迁移。
- **b12277** — `mtmd` 字符串格式化 API 重命名为 `mtmd_format`（[release](https://github.com/ggml-org/llama.cpp/releases)）。若你基于 mtmd 构建，请检查多模态头文件包含。
- **b12274** — 支持 FP8 KV-cache；缓存缩放命名已规范化，不再依赖投影布局（[release](https://github.com/ggml-org/llama.cpp/releases)）。任何引用旧缓存缩放名称的配置或工具都应更新。
- **b12282** — 聊天服务器现对 Muse Spark 遵循 `response_format: json_schema`（[release](https://github.com/ggml-org/llama.cpp/releases)）；依赖此前忽略语义的客户端行为将发生变化。

---

## 新模型与硬件支持

- **CUDA: OCP FP8** — #28413 新增 OCP FP8 matmul，在 GEMV 场景下采用 W8A16 分派（[#28413](https://github.com/ggml-org/llama.cpp/pull/28413)）；与 b12284 配套。
- **FP8 KV-cache（核心）** — #28392 新增 FP8 KV cache 及缩放后端测试（[#28392](https://github.com/ggml-org/llama.cpp/pull/28392)）。
- **Vulkan**
  - 新增 `PAD_REFLECT_1D` webgpu 算子支持（b12292）。
  - CM2 反量化 KV 优化在基于 query_tokens×GQA 比例的启发式条件下启用（b12291，[#31526](https://github.com/ggml-org/llama.cpp/pull/31526)）。
  - 设备故障厂商二进制缓冲区分配（b12290，[#32008](https://github.com/ggml-org/llama.cpp/pull/32008)）。
  - CoopMat2 GDN（Gated Delta Net）实现审查中，workgroup 作用域路径平均约 40% 加速（[#32111](https://github.com/ggml-org/llama.cpp/pull/32111)）。
- **CUDA BF16 MMVF** — 针对小批量下少行 BF16 矩阵的 MMVF 路径，面向 Qwen 超连接投影（[#31959](https://github.com/ggml-org/llama.cpp/pull/31959)）。
- **审查中的新架构**
  - TML Inkling（含带状 FA kernel、面向大型 MoE 的 int64 索引修复）（[#25731](https://github.com/ggml-org/llama.cpp/pull/25731)）。
  - Ouro（字节跳动循环式 LM，1.4B/2.6B）（[#29823](https://github.com/ggml-org/llama.cpp/pull/29823)）。
- **OpenCL** — Adreno 上的分解式 FA（[#32046](https://github.com/ggml-org/llama.cpp/pull/32046)）；Adreno X1 的 Q4_0 非 MoE GEMM bin kernel（[#30947](https://github.com/ggml-org/llama.cpp/pull/30947)）；磁盘 FA 程序缓存（[#31484](https://github.com/ggml-org/llama.cpp/pull/31484)）。
- **后端一致性** — YaRN 截断配置传播至 CANN、ET、Hexagon、Metal、OpenCL、virtgpu（[#32085](https://github.com/ggml-org/llama.cpp/pull/32085)）。
- **SYCL** — `CLAMP` 修复以支持 strided 视图（b12283）。
- **HIP** — GCN MMQ 配置重调审查中（[#30021](https://github.com/ggml-org/llama.cpp/pull/30021)）。

---

## 性能与优化

- **CUDA/HIP 在 `flash_attn_tile` 中原生量化 + BF16 KV** — q4_0/q4_1/q5_0/q5_1/q8_0/bf16 可组合 K/V，在 tile 加载时反量化（[#31234](https://github.com/ggml-org/llama.cpp/pull/31234)）。
- **层优先预填充** — 重排执行顺序，使一层的所有 token 先运行完再推进，配合 `-cmoe` 将每 ubatch 的专家重上传次数减半；作者报告在 QFN 工作负载上预填充约 2 倍加速（[#32082](https://github.com/ggml-org/llama.cpp/pull/32082)）。
- **Vulkan CM2 反量化 KV** — 以 query_tokens×GQA 启发式条件控制，避免小批量解码回退（b12291）。
- **CoopMat2 GDN** — 相较标量路径平均约 40% 加速（Vulkan）（[#32111](https://github.com/ggml-org/llama.cpp/pull/32111)）。
- **图元数据主机内存** — 避免触碰未使用的元数据页；与 #31274 结合在 MTP/DFlash2 工作流上有可观节省（[#32043](https://github.com/ggml-org/llama.cpp/pull/32043)）。
- **CUDA BF16 MMVF 小批量** — 在少行 BF16 矩阵上填充空闲 SM（b12287，[#31959](https://github.com/ggml-org/llama.cpp/pull/31959)）。
- **服务端 I/O** — 修复流式响应后的连接复用；此前最后一块被标记为发送失败，导致可复用连接被关闭（[#30492](https://github.com/ggml-org/llama.cpp/pull/30492)）。

---

## 稳定性与回归

按严重程度排序：

1. **gfx1201 head-size-256 WMMA FA 分派阈值** — 自 #28102 起在 Radeon 上投机解码约 20% 回归（[#28867](https://github.com/ggml-org/llama.cpp/issues/28867)）。开放中；很可能是单行阈值修复。
2. **量化目标上的投机解码发散** — 在 Q4_K_M 目标上配合 draft-mtp/DSpark 时贪心输出与原生解码不同，但在 BF16 上一致（[#25618](https://github.com/ggml-org/llama.cpp/issues/25618)）。42 条评论；若你以量化模型交付投机解码，此为正确性关键问题。
3. **CPU F16 累加溢出为 inf/NaN** — 单 chunk CPU flash-attention 与 ARM F16 点积路径使用 F16 累加器（[#29774](https://github.com/ggml-org/llama.cpp/issues/29774)，[#31734](https://github.com/ggml-org/llama.cpp/issues/31734)）。二者均于 2026-11-11 关闭 — 请针对你的构建验证。
4. **CUDA 非法内存访问** — Gemma 4 31B 上 KV 回滚/清除后立即 MTP 草稿（[#31077](https://github.com/ggml-org/llama.cpp/issues/31077)）。已关闭；在假定你的构建已修复前请先复现。
5. **ggml 调度器崩溃** — Intel Arc 上 `ggml_backend_sched_alloc_splits: unexpected graph reallocation`（[#28753](https://github.com/ggml-org/llama.cpp/issues/28753)）。开放中。
6. **HIP/gfx906 MoE 提示处理慢 15–50%** — 自 #29948 起（[#31599](https://github.com/ggml-org/llama.cpp/issues/31599)）。开放中。
7. **HIP gfx1200 `MUL_MAT` 容差失败**（F16，确定性）（[#31111](https://github.com/ggml-org/llama.cpp/issues/31111)）。开放中；RDNA4 上潜在的静默正确性问题。
8. **RPC 后端预热崩溃** — 跨 GB10/DGX Spark 节点的大型 MoE 首次解码时（[#30052](https://github.com/ggml-org/llama.cpp/issues/30052)）。开放中。
9. **SYCL DFlash2 草稿 TDR** — 双 Arc Pro B70 上（Windows 驱动重置）（[#28778](https://github.com/ggml-org/llama.cpp/issues/28778)）。作为陈旧问题关闭；日志中无明确根因。
10. **Vulkan CHAT/SWA 崩溃** — `GGML_ABORT cache_k_l0` 在 2.40/2.41 相对 2.39 回归（[#29082](https://github.com/ggml-org/llama.cpp/issues/29082)）。已关闭。
11. **llama-server OOM**（"bad allocation"）— 长对话中，HIP（[#30091](https://github.com/ggml-org/llama.cpp/issues/30091)）。开放中。
12. **次要 / 工具类** — `auto_fgdn` 硬编码为 false，导致融合 GDN 探测从不运行（[#31987](https://github.com/ggml-org/llama.cpp/issues/31987)，已关闭）；路由内存测量遗漏草稿模型（[#31351](https://github.com/ggml-org/llama.cpp/issues/31351)）；服务器不校验 Content-Type（[#31979](https://github.com/ggml-org/llama.cpp/issues/31979)，已关闭）；安全策略链接失效（[#31972](https://github.com/ggml-org/llama.cpp/issues/31972)）；聊天工具列表可见但显示"零工具"（[#29727](https://github.com/ggml-org/llama.cpp/issues/29727)）。

---

## 对应用开发者的意义

- **量化 KV + FP8 路径正成为一等公民。** 随着核心中的 FP8 KV-cache（b12274）以及 CUDA/HIP FA tile 中的原生量化 KV（#31234）和 Vulkan CM2（b12291），你现在可以在大多数生产后端上请求激进的 KV 压缩。请为精度回归测试预留时间：投机解码发散报告（#25618）表明量化目标即使在贪心采样下也可能漂移，因此请明确针对量化目标验证草稿模型。
- **PCIe 主机上的 MoE 服务显著提速。** 层优先预填充（#32082）与图元数据页节省（#32043）直接降低多 GPU/SysMem 配置的主机侧开销。若你在消费级 GPU 上运行 `-cmoe`，请在下次容量规划前对该组合进行基准测试。
- **服务端流式可靠性改善。** #30492 修复了流式响应后连接被关闭的问题 — 这对处于 keep-alive HTTP 连接池后的客户端很重要。
- **OpenAI 兼容面正在收紧。** Muse Spark 现遵循 JSON-schema `response_format`（b12282）；Content-Type 校验正在讨论中（#31979）。请相应加固客户端头。
- **多模态集成方**必须更新至重命名后的 `mtmd_format` API（b12277）。
- **升级前请跟踪回归列表** — 特别是若你在 RDNA3 上使用投机解码，请关注 gfx1201 FA 阈值（#28867）；若你在 RDNA4 上产出对输出质量敏感的工作负载，请关注 gfx1200 MUL_MAT 容差失败（#31111）。

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
*本日报由 [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar) 自动生成。*