# AI 基础设施日报 2026-11-09

> 生成时间: 2026-11-08 23:13 UTC | 覆盖项目: 6 个

- [vLLM](https://github.com/vllm-project/vllm)
- [SGLang](https://github.com/sgl-project/sglang)
- [llama.cpp](https://github.com/ggml-org/llama.cpp)
- [Ollama](https://github.com/ollama/ollama)
- [LiteLLM](https://github.com/BerriAI/litellm)
- [Unsloth](https://github.com/unslothai/unsloth)

---

## 横向对比

# 跨项目对比报告 — 2026-11-09

## 1. 生态概览

今日的摘要显示，一个成熟的 AI 基础设施栈正在三个方向上同时收敛：**Blackwell/SM120 消费级 GPU 支持**在所有服务引擎上仍然不稳定（vLLM 和 SGLang 在 RTX PRO 6000 / GB10 硬件上都存在未解决的 decode/prefill 回归），**量化 MoE 服务**已成为主要的正确性风险面（SGLang 报告了两个静默垃圾输出的 router 加载 bug；vLLM 存在 SM120+NVFP4+MTP 非法内存访问回归），而 **AMD/ROCm 正在获得大量投入**（vLLM 的 persistent_topk 和 gfx942 FP8 MoE 配置、SGLang 的 gfx1250 UALink 和 AITER FlyDSL 路径）。与此同时，网关层正在承接更多生产职责——LiteLLM 的队列显示，计费核算和协议转换 bug 与路由吞吐同样重要。本地运行时（llama.cpp、Ollama）正从另一个方向收敛到相同主题：投机解码正确性、MoE 部分卸载，以及硬件多样性后端（RISC-V、Rockchip NPU、SYCL/Vulkan）。

## 2. 活跃度对比

| 项目 | Issue（24 小时） | PR（24 小时） | 发布状态 | 值得关注的信号 |
|---|---|---|---|---|
| **vLLM** | 高（10 个已排序回归 + 活跃 RFC） | 大量进行中（Kimi K3.1 TP8 系列、Marlin→Humming） | **24 小时内无发布**；v0.33.0 带有已知 SM120 回归 | 进行中最大的结构性优化提案（Humming kernel 迁移） |
| **SGLang** | 高（隐含 56 张工单式积压；10 个已排序） | 活跃（统一内存重构栈、Rust server 后续工作） | **24 小时内无发布**；v0.5.23 存在 GLM-5.3-Flash 损坏 | 最高严重级别的正确性 bug（静默 MoE router 故障） |
| **llama.cpp** | 中等（10 个已排序） | 密集合并日（b12202–b12211 已落地；新的 `clip.h` 移除） | **约 10 个构建已落地**（b12199–b12211） | 所有项目中发布最多；确定性 Top-K 是可复现性的胜利 |
| **Ollama** | 中等（9 个已排序） | 活跃（Rockchip NPU、FP8 导入、VRAM 预测） | **24 小时内无发布** | 按模型的 `num_parallel` 终于实现（#9546） |
| **LiteLLM** | 非常高（56 个 issue 更新） | 非常高（304 个 PR 涉及） | **开发标签 v1.110.0-dev.2**（cosign 签名镜像） | 变动最频繁；计费正确性 bug 占主导 |
| **Unsloth** | 中等（bug 报告） | 非常高（同日大量 CLOSED 批次 + 开放后续工作） | **24 小时内无发布** | 扩散/Studio 路径是活跃前沿 |

**解读表格：** LiteLLM 的 issue/PR 量比服务引擎高一个数量级——部分原因是网关面广，部分原因是承担计费语义的成本。llama.cpp 是唯一持续发布构建的项目，这与其作为底层参考运行时的角色一致。

## 3. 模型支持竞赛

| 模型 / 架构 | vLLM | SGLang | llama.cpp | Ollama | Unsloth |
|---|---|---|---|---|---|
| **Kimi K3.1（TP8）** | ✅ 3/5 拆分进行中 | — | — | — | — |
| **MiniMax M3 / M3.1** | ✅ Attention 复制标志 + NVFP4 索引分片 | — | — | — | — |
| **Nemotron 3.5 ASR 流式** | 🟡 FR + 持久状态 PR | — | — | — | — |
| **Qwen3.5/3.8（GDN 混合）** | 🟡 MTP drafter 修复 | ✅ AITER causal conv1d prefill | ✅ SSM `d_state=64` 快速路径 | — | ✅ Qwen-Image CFG 修复 |
| **DeepSeek-V4.1** | — | 🟡 SM120 上 8× prefill 回归 | ✅ RPC 加载快 5×（开放） | — | — |
| **Mixtral / Llama 4 / Qwen3-MoE（FP8 router）** | — | 🔴 静默 router bug | — | — | — |
| **GLM-5.3-Flash / GLM-V** | — | 🔴 v0.5.23 引擎损坏 | — | — | — |
| **FLUX.3 / HiDream / 图像生成** | — | ✅ HiDream-O1-Image（进行中） | — | ❌ macOS 不支持 | ✅ fp8/fp16/Turing 路径 |
| **InternLM3 / TeleChat2/3** | ✅ HF Transformers 后端迁移 | — | — | — | — |
| **Rockchip RK3588/RK3576** | — | — | — | ✅ NPU prefill 后端 | — |

**谁领先：** vLLM 在*新前沿模型支持的广度*上领先（Kimi、MiniMax、Nemotron），并正在将长尾架构标准化到 HF Transformers 后端——这是一个减少逐模型维护成本战略赌注。SGLang 在*AMD 特定模型路径*（gfx950 上 Qwen GDN 的 AITER）和扩散方面领先。llama.cpp 拥有*架构通用*的胜利（SSM scan、MXFP4 RISC-V 重打包）。Ollama 是唯一推进到*非 GPU 加速器*（Rockchip NPU）的项目——一个独特的边缘部署赌注。

## 4. 性能前沿

优化工作集中在四个集群，分布不均：

**KV cache——最热前沿。** vLLM 有最激进的议程：提议的 **AMD 3-bit Octave KV cache**（#59773），以及在 Triton/eager 后端缓存 K/V 视图派生。llama.cpp 正在 CUDA `fattn-mma-f16` 内融合 q8_0/q4_0 KV 反量化（#31802），并修复循环状态的 sticky-NaN（#31913）。SGLang 正在通过同批次前缀共享（#48963）攻击冗余 prefill KV。Ollama 的贡献是*预测*——准确的混合/量化 KV VRAM 预测（#19609）。

**量化——正确性风险所在。** SGLang 的静默 MoE router bug（#49185、#49198）是今天最严重的 issue，正是因为量化 router 失败时不会报错。vLLM 的 Marlin→Humming 迁移（#65560）是进行中最大的结构性 kernel 变更，在 NVIDIA 各架构上整合 INT4/FP8/MXFP*/NVFP4 路径。Unsloth 的 INT8 融合 MLP 工作（#17114）和 Turing INT8 启用（#17167）将量化训练/推理扩展到更旧的硬件。

**Kernel 与后端——AMD/Blackwell 是目标。** vLLM：ROCm persistent_topk（#63847）、gfx942 FP8 MoE（+12–25% 吞吐，#63727）。SGLang：FlyDSL causal conv1d、gfx1250 UALink all-reduce、FA3 kernel 选择调优（1.5–3% 差距）。llama.cpp：SYCL Battlemage decode 路径、用于投机验证的 OpenCL q8_0 GEMV。

**分布式服务与调度。** vLLM 的 MiniMax attention 复制标志是一个务实的跨节点 TP 杠杆；SGLang 的统一内存/HiCache 重构（6 个 PR）和 Q4 P/D 分离路线图是最结构化的努力；Ollama 的调度器 VRAM 超额提交修复（#19581）是本地运行时的对应物。

**值得注意的缺失：** 批处理层面的创新今天很稀疏——大多数收益是 kernel 和内存布局层面的，而非调度器层面的（SGLang 的 `max_running_requests`/`cuda_graph_max_bs` 交互仍然是一个吸收态调优陷阱，#33483）。

## 5. 层级定位

| 层级 | 项目 | 主要价值 | 今日焦点 |
|---|---|---|---|
| **服务引擎**（数据中心） | vLLM、SGLang | 吞吐、前沿模型首日支持、分布式服务 | Kimi/MiniMax 支持、Blackwell 修复、AMD kernel、静默正确性 bug |
| **本地/边缘运行时** | llama.cpp、Ollama | 可移植性、硬件多样性、单二进制/桌面 UX | 确定性采样、SSM 卸载、Rockchip NPU、按模型并行 |
| **LLM 网关** | LiteLLM | 路由、预算、协议转换、可观测性 | 计费核算正确性、`/v1/messages` 保真度、热路径 Python 消除 |
| **训练 / 微调** | Unsloth | 内存高效训练、扩散 Studio、量化感知微调 | 扩散管线修复、INT8/Turing、线程安全并发加载 |

各层正在**收敛到重叠的职责**：vLLM 和 SGLang 现在都实现了类 OpenAI API 表面和类网关路由（P/D 分离、回退）；LiteLLM 正在将热路径迁移到 Rust 以支撑 1,000+ 部署；Ollama 正在吸收过去隐含的运行时关注点（VRAM 预测、调度器）。随着 litellm 将性能关键逻辑推入 Rust，服务引擎暴露更丰富的原生 API，网关层传统角色的压力将持续存在。

## 6. 趋势信号

**1. 静默正确性故障是量化 MoE 的决定性风险。** SGLang 的 router bug（#49185、#49198）输出垃圾且无错误；vLLM 的 SM120/NVFP4/MTP 回归（#65139）和 llama.cpp 在量化目标上的投机解码分歧（#25618）属于同一类。**给开发者的行动：** 在任何量化 MoE 升级前，针对参考检查点构建 logit 一致性或黄金输出测试——活性检查不会捕获这些。

**2. 预算执行不能仅信任网关层。** LiteLLM 的 #50124/#50125/#50528 三件套（缺失用量 → 未计费；超预算串行准入；断连留下预留）意味着 `max_budget` 是尽力而为。**行动：** 将网关预算与上游提供商配额和客户端总截止时间配对（`x-litellm-timeout` 限制每次尝试，而非墙钟时间，#49751）。

**3. 投机解码是可复现性风险，而不仅仅是性能旋钮。** llama.cpp 前三大 issue 中有两个是投机解码正确性/中止（#30860/#31914、#25618）。**行动：** 如果你运行评估框架、agent 重放或回归套件，要么使用 bf16 目标，要么在量化模型上禁用 draft-MTP 并固定构建版本。

**4. Blackwell 消费级 GPU（SM120）在各引擎上尚未生产就绪。** vLLM decode 崩溃（#56892）和 SGLang 的 8× DSV4.1 prefill 回归（#49173，源于过严的 `is_sm(10)` 门控）都指向不成熟的 SM120 路径。**行动：** RTX 50xx / RTX PRO 6000 / GB10 部署应针对 SM100 级参考数字进行验证并跟踪修复，不要假设功能对等。

**5. AMD 正在成为一等服务目标——且带模型特定调优。** Octave 3-bit KV、gfx942 FP8 MoE 配置、gfx1250 UALink、AITER GDN kernel。**行动：** 如果 MI300X/MI355 或 RDNA 在你的路线图中，为引擎特定 kernel 启用预留预算；不要假设 CUDA 调优配置可以迁移。

**6. 流式/agent 基础设施是下一个战场。** vLLM 的调度器拥有持久状态（#65544）解锁有状态 ASR/语音 agent；SGLang 的可缓存 agent 前缀 RFC（#47763）和同批次 KV 共享（#48963）针对 agent 循环 TTFT；llama.cpp 的 UTF-8 工具调用清理（b12199）和语法排序 PR 解决最常见的 agent 失败模式。**行动：** agent 框架构建者应跟踪这三条线索——它们直接影响 TTFT、工具调用可靠性和多轮成本。

**7. 优雅运维仍然服务不足。** vLLM 的 `--shutdown-timeout` bug（#61562）意味着 DP>1 部署上的滚动重启会以 500 丢弃在途请求；Ollama 的 runner 卸载失败可能超额提交 VRAM（#19577）。**行动：** 对于生产滚动升级，明确验证排空行为，并预期在 vLLM 修复落地前出现 500。

**8. 扩散/图像生成是微调栈中新兴的工作负载类别。** Unsloth 的同日 CLOSED 批次绝大多数是扩散（CFG 配对、NVFP4 解码、VAE 分块、设备定位），带有开放的 fp8-on-L4 和线程安全回归。**行动：** 如果 Studio 驱动的图像/视频管线在范围内，固定提交并在 #17171 落地前避免并发扩散模型加载。

---

## 各项目详细报告

<details>
<summary><strong>vLLM</strong> — <a href="https://github.com/vllm-project/vllm">vllm-project/vllm</a></summary>

# vLLM 摘要 — 2026-11-09

## 1. 今日要闻

过去 24 小时内没有新版本发布，但 issue/PR 队列显示围绕 **Kimi K3.1 服务**（KV cache/attention 拆分 PR）以及一组 **SM120/Blackwell 消费级 GPU 问题**（涉及 CUDA graph 捕获和 MTP 投机解码）的活动非常密集。在优化方面，正在进行中最大的结构性变更是提出的跨 NVIDIA 架构的 **Marlin → Humming 内核迁移**，同时还有针对 AMD GPU 的新 **3-bit Octave KV cache** RFC。

## 2. 版本发布与破坏性变更

过去 24 小时内没有新版本发布。值得注意的进行中变更及其兼容性影响：

- **RFC：移除 Group Activation Ordering（`actorder=group`）** — 提议从 vLLM、compressed-tensors 和 llm-compressor 中移除运行时 actorder 支持，影响使用 `actorder=group` 量化的 GPTQ W4A16/W8A16 模型。已关闭/停滞，但对拥有此类 checkpoint 的用户具有迁移相关性。[#48148](https://github.com/vllm-project/vllm/issues/48148)
- **`llguidance` 在 `vllm==0.33.0` 中成为无条件依赖**，并且被 `from vllm import LLM` 导入，这会在 glibc < 2.31 的环境上阻塞安装/导入。[#66345](https://github.com/vllm-project/vllm/issues/66345)

## 3. 新模型与硬件支持

- **Kimi K3.1 @ TP8** — K3.1 服务系列的第 3/5 部分拆分，添加了在张量并行 8 下服务 K3.1 所需的 KV cache 和 attention 变更（第 1/5 和 2/5 部分已合并）。[#66037](https://github.com/vllm-project/vllm/pull/66037)
- **Nemotron 3.5 ASR Streaming（0.6B）** — 功能请求，支持 NVIDIA 的流式多语言 ASR 模型；配套 PR 为会话状态固定大小且不可重计算的流式模型添加调度器持有的持久状态。[#47455](https://github.com/vllm-project/vllm/issues/47455)、[#65544](https://github.com/vllm-project/vllm/pull/65544)、RFC [#65545](https://github.com/vllm-project/vllm/issues/65545)
- **MiniMax M3** — 实验性 `VLLM_MINIMAX_M3_REPLICATE_ATTN=1` 在 TP rank 间复制 attention（跳过 attention all-reduce，MoE 保持 TP），适用于跨节点 TP 较慢的场景；另一个 PR 将 MiniMax M3.1 的 NVFP4 index cache 与 indexer CP 分片。[#66378](https://github.com/vllm-project/vllm/pull/66378)、[#64631](https://github.com/vllm-project/vllm/pull/64631)
- **Transformers 后端迁移** — InternLM3、IQuestCoder、TeleChat2/2.5 和 TeleChat3 从 vLLM 的 Llama 实现迁移到 HF Transformers 建模后端（新增 TeleChat2-115B 支持）。[#66416](https://github.com/vllm-project/vllm/pull/66416)
- **ROCm：Mamba2 ReplaySSM + 投机解码** 在 Triton 后端上（此前仅限 FlashInfer/CUDA），并附带 B200 nightly 评估的 MI355 镜像。[#66318](https://github.com/vllm-project/vllm/pull/66318)

## 4. 性能与优化

- **RFC：跨 NVIDIA 架构用 Humming 替换 Marlin** — Humming 现已覆盖 Marlin 的格式（INT4/INT8 GPTQ/AWQ、FP8、MXFP8、MXFP4、NVFP4、MXFP6），适用于 dense 和 MoE 层；Marlin 属于 SM80 时代，没有 Hopper/Blackwell 专用路径。[#65560](https://github.com/vllm-project/vllm/issues/65560)
- **ROCm `persistent_topk`** 采样移植（FilteredTopK + sampled_topk 层级，wave64 安全的 HIP 宏）；gfx950 的每个 batch 都走大 LDS 路径。[#63847](https://github.com/vllm-project/vllm/pull/63847)
- **gfx942 FP8 W8A8 fused-MoE 配置** 针对 E=32、N=4096（Muse Spark 在 TP8+EP 下）：在 MI300X 上 **64 并发请求时服务吞吐提升 12–25%**，GSM8K 不变。[#63727](https://github.com/vllm-project/vllm/pull/63727)
- **RFC：Octave KV，AMD GPU 的原生 3-bit KV cache** — 动机来自在 4× RX 7900 XTX 上服务长 prompt 的 Qwen3.8-27B W4A16。[#59773](https://github.com/vllm-project/vllm/issues/59773)、PR [#59774](https://github.com/vllm-project/vllm/pull/59774)
- **Triton/eager attention 后端在每次调用时重新推导 K/V cache 视图** — 与 #59534 中 FlashAttention 修复同类开销；提议缓存这些视图。[#64572](https://github.com/vllm-project/vllm/issues/64572)
- **投机解码：零气泡 draft-token 交接**，用于使用 `step_with_batch_queue` 时的结构化输出。[#64531](https://github.com/vllm-project/vllm/pull/64531)
- **RFC：ViT 完整 CUDA Graph** — 跟踪捕获整个 ViT 前向（Qwen3-VL、GLM-V、Kimi K2.5）以减少多模态服务中的启动开销。[#38175](https://github.com/vllm-project/vllm/issues/38175)

## 5. 稳定性与回归

按严重程度排序：

1. **SM120 解码吞吐崩溃 / CUDA graph 不可用** — 在 8× RTX PRO 6000 Blackwell 上运行 DeepSeek-V4.1-Flash 并加 `--enforce-eager` 时解码吞吐极低；请求优先修复 graph 捕获。[#56892](https://github.com/vllm-project/vllm/issues/56892)（另见可用配置的现场报告：[#56700](https://github.com/vllm-project/vllm/issues/56700)）
2. **GDN 解码退化为单个重复 token** — Qwen3-Next 在 ROCm（gfx906/MI50）上使用 fp32 SSM cache，上下文增长后出现；在 GB10（sm_121）上启用 prefix caching 时也报告为 CUBLAS 内部错误 / 非法内存访问。[#54308](https://github.com/vllm-project/vllm/issues/54308)、[#54173](https://github.com/vllm-project/vllm/issues/54173)
3. **v0.33.0 回归：b12x attention 非法内存访问** 在第一个 MTP verify 步骤（冷 JIT `PagedForwardKernel`），SM120 + NVFP4 MoE + fp8 KV + chunked prefill + prefix caching；在 v0.32.0 中正常。[#65139](https://github.com/vllm-project/vllm/issues/65139)
4. **多模态编码器 CUDA graph 崩溃**，当 `enable_mm_embeds=True` + `cudagraph_mm_encoder=True` 并使用预计算 embeddings 时 — **修复 PR 已开放**。[#65531](https://github.com/vllm-project/vllm/issues/65531)、[#65535](https://github.com/vllm-project/vllm/pull/65535)
5. **编译后的多模块 MTP drafter 在 checkpoint 包含多个 MTP 模块时总是运行模块 0**（Qwen3.5/Nex 修复扩展到其余 drafter）— **修复 PR 已开放**。[#66428](https://github.com/vllm-project/vllm/pull/66428)
6. **`--shutdown-timeout` 在 `--api-server-count > 1` 时从不排空** — API server 在引擎之前停止，进行中的请求以 `EngineDeadError`（HTTP 500）失败；**修复 PR 已开放**。[#61562](https://github.com/vllm-project/vllm/pull/61562)
7. **KV pool 饱和时的准入重试使解码 TPOT 膨胀约 2.3 倍**（V1 调度器，Ascend P/D 部署）— 请求自动准入退避。[#64460](https://github.com/vllm-project/vllm/issues/64460)
8. **UVA `--cpu-offload-gb` 在 XPU 上不降低压缩张量 WNA16 MoE 的峰值设备内存**。[#57008](https://github.com/vllm-project/vllm/issues/57008)
9. **Anthropic Messages API：不支持 `x-api-key` 请求头** — 仅 `Authorization: Bearer` 可用。[#51572](https://github.com/vllm-project/vllm/issues/51572)
10. **Whisper 长音频时间戳漂移**（音频 >30s 时每段约 0.5s）— 已关闭。[#32588](https://github.com/vllm-project/vllm/issues/32588)

## 6. 对应用开发者的意义

- **有意识地固定你的 vLLM 版本。** v0.33.0 在 SM120 上配合 MTP + prefix caching 存在真实的解码回归（#65139），并将 `llguidance` 作为无条件依赖发布，在较旧 glibc 上会破坏导入（#66345）。如果你使用消费级 Blackwell 或 RHEL/较旧基础镜像，升级前请先验证。
- **Kimi K3.1 和 MiniMax M3 正成为一等公民。** 如果你服务这些系列，请关注 K3.1 TP8 系列和 MiniMax attention 复制标志——后者是跨节点 TP 部署中 attention all-reduce 主导延迟时的务实杠杆。
- **AMD 用户很快将获得有意义的收益** — persistent_topk 采样、调优的 gfx942 FP8 MoE 配置（吞吐 +12–25%）以及提议的 3-bit KV cache 可能大幅降低长上下文工作负载的每 token 成本。
- **流式/ASR 应用**应跟踪调度器持有的持久状态 PR（#65544）；它解锁了无法从 prompt 重新计算会话状态的有状态流式模型——对实时转录和语音代理相关。
- **运维卫生：** `--shutdown-timeout` 多 API server bug 意味着当前在 DP > 1 部署中优雅排空会静默失败；在 #61562 落地之前，滚动重启期间预期会出现 500。

</details>

<details>
<summary><strong>SGLang</strong> — <a href="https://github.com/sgl-project/sglang">sgl-project/sglang</a></summary>

# SGLang 摘要 — 2026-11-09

## 1. 今日要点

过去 24 小时内没有新的版本发布，但 issue 跟踪器相当繁忙：MoE 模型中报告了一组**量化路由器加载 bug**（compressed-tensors、FP8），服务器会静默输出垃圾结果而非报错；同时针对 Qwen3.8-NVFP4 提交了一个 **prefill CUDA graph logprob 偏差** bug。在性能方面，统一内存 / HiCache 重构 PR 栈正在积极评审中，Rust 服务器后续跟踪器已开启，Q4 Prefill-Decode 分离路线图仍在推进。与此同时，针对 #48399 中 DSV4.1 DeepGEMM indexer 门控变更，报告了 GB10（SM120/121）上 8 倍的 prefill 性能回退。

## 2. 版本发布与破坏性变更

过去 24 小时无新版本。仍在评审中的重要行为变更项：

- **Qwen4-Exp 的 `--boundary-reduction` 默认值变更** — PR #49203 提议在 DP attention 下默认使用 reduce-scatter 而非 `ar`，改变 `Qwen4ExpForConditionalGeneration` 和 `Qwen4ExpForCausalLMMTP` 的解析路径。这会改变现有 Qwen4-Exp 部署的 FFN 出口行为（[PR #49203](https://github.com/sgl-project/sglang/pull/49203)）。
- **配置结构重构待定**：`max_running_requests`（4096）和 `cuda_graph_max_bs`（32）的默认值独立推导，超过 graph 上限被描述为**吸收态** — 已作为不活跃关闭，但值得在调优指南中注意（[Issue #33483](https://github.com/sgl-project/sglang/issues/33483)）。

## 3. 新模型与硬件支持

- **SM12x（RTX 50xx / RTX PRO Blackwell）**：功能请求将 **FlashInfer cuTile NVFP4 MoE** 设为 SM12x 上的默认 MoE runner（[Issue #49123](https://github.com/sgl-project/sglang/issues/49123)）。
- **AMD gfx1250 / UALink**：carv2 自定义 all-reduce 扩展至 gfx1250 UALink 节点（[PR #49003](https://github.com/sgl-project/sglang/pull/49003)）。
- **AMD AITER FlyDSL causal conv1d prefill** 用于 gfx950 上 Qwen3.5/Qwen3.8 GDN 层（可选启用，相较 Triton 路径约 1.1 倍加速）（[PR #41879](https://github.com/sgl-project/sglang/pull/41879)）。
- **AITER 升级就绪跟踪器**仍在开放中，逐版本跟踪功能依赖（[Issue #21302](https://github.com/sgl-project/sglang/issues/21302)）。
- **Diffusion / NPU**：HiDream-O1-Image 支持（DEV、DEV-2604、FULL TI2I）进行中（[PR #45137](https://github.com/sgl-project/sglang/pull/45137)）。

## 4. 性能与优化

- **GB10 / SM120–121 DSV4.1 prefill 回退（8 倍）**：#48399 将 `is_sm100_or_newer()` 替换为严格的 `current_arch().is_sm(10)`，将 SM120/121 从 DeepGEMM indexer 门控中移除，强制回退到 torch 路径并逐层调用 `tolist()`（[Issue #49173](https://github.com/sgl-project/sglang/issues/49173)）。
- **SM120 上 DeepSeek-V4 dsv4 prefill** 仍表现不佳：在相同的 4× RTX PRO 6000 硬件上约 2–7K tok/s，而通过 vLLM/marlin 报告约 12.5K — 请求调优指导（[Issue #33422](https://github.com/sgl-project/sglang/issues/33422)）。
- **FA3 kernel 选择**：`sglang-kernel` FA3 在 H200 上对 hdim 128 非 causal 选择 `MmaPV_is_RS=false`，比 flash-attn 的 Hopper 构建慢 1.5–3%（[Issue #47734](https://github.com/sgl-project/sglang/issues/47734)）。
- **Prefill 批次 KV 共享**：提议在同一 prefill 批次中准入的请求间共享 prompt 的 KV，避免相同 prompt 的冗余全量 prefill 计算（[Issue #48963](https://github.com/sgl-project/sglang/issues/48963)）。
- **统一内存重构 PR 栈**（6 个 PR，#48009–#48014）：按窗口的 host 池/PD 条目、单一布局搜索准入、Triton attention 后端中的多窗口子池支持 — 面向 `--enable-unified-memory` 和 HiCache 路径（[PR #48014](https://github.com/sgl-project/sglang/pull/48014)、[PR #48012](https://github.com/sgl-project/sglang/pull/48012)）。
- **Rust 服务器栈后续项**跟踪非阻塞性能事项（[Issue #49055](https://github.com/sgl-project/sglang/issues/49055)）。
- **统一 FP32 激活 kernel** 位于 `sgl_kernel/activation.cuh`，带 `kFastMath` 开关和用于融合量化的 `silu_fast`（[PR #48942](https://github.com/sgl-project/sglang/pull/48942)）。

## 5. 稳定性与回退

按严重程度排序：

1. **量化 MoE 路由器的静默正确性失败** — 严重程度最高。以量化形式（compressed-tensors）存储的路由器从未被加载，服务器返回垃圾结果而不报错（[Issue #49185](https://github.com/sgl-project/sglang/issues/49185)）；Mixtral、Llama 4 和 FP8 Qwen3-MoE 中 FP8 存储的路由器存在相同模式，scale 被丢弃（[Issue #49198](https://github.com/sgl-project/sglang/issues/49198)）。
2. **GLM-5.3-Flash 引擎在一次 1M token 请求后损坏**（v0.5.23，MTP 接受率 0.00，所有后续请求退化）；v0.5.22 正常（[Issue #48964](https://github.com/sgl-project/sglang/issues/48964)）。在 GLM-5.3-Flash bug 跟踪器中跟踪（[Issue #37524](https://github.com/sgl-project/sglang/issues/37524)）。
3. **Prefill CUDA graph 数值偏差**：短 prefill 填充到捕获桶后产生与 eager 执行不同的 logprob（Qwen3.8-27B-NVFP4、混合 GDN、v0.5.23）（[Issue #49133](https://github.com/sgl-project/sglang/issues/49133)）。
4. **完整 prefill CUDA graph 捕获崩溃** 用于混合线性注意力模型（GDN/KDA）— `HybridLinearAttnBackend._replay_metadata` 中 `IndexError`（[Issue #48593](https://github.com/sgl-project/sglang/issues/48593)）。
5. **GB10 统一内存在 >40k token prefill 时耗尽** — worker rank 被静默杀死，无 traceback 或 OOM 记录；跨栈控制在 54k 时通过（[Issue #36941](https://github.com/sgl-project/sglang/issues/36941)）。
6. **LoRA `use_rslora=True` 以错误 scale 服务**（[Issue #40835](https://github.com/sgl-project/sglang/issues/40835)）。
7. **已关闭 / 不活跃**：DeepSeek-V4 BF16 checkpoint 被路由到 MXFP4 expert 路径（[Issue #44810](https://github.com/sgl-project/sglang/issues/44810)）；HiCache host 层回载损坏 GLM-5.3-Flash 生成（[Issue #38031](https://github.com/sgl-project/sglang/issues/38031)）；GLM-V 多余多模态占位符导致 500（[Issue #37579](https://github.com/sgl-project/sglang/issues/37579)）。

CI 卫生 PR 正在进行：scheduler stub 在 #49144 后需要 `PauseState`（[PR #49178](https://github.com/sgl-project/sglang/pull/49178)，已关闭）。来自 hnyls2002 的若干 CI flake 修复已开放 — Mooncake KV 注册启动失败（[PR #49156](https://github.com/sgl-project/sglang/pull/49156)）、Gemma4 AR 融合测试死锁（[PR #49160](https://github.com/sgl-project/sglang/pull/49160)）、以及 diffusion Hub-API 429 回退到缓存快照（[PR #49155](https://github.com/sgl-project/sglang/pull/49155)）。

## 6. 对应用开发者的意义

- **在信任输出前审计任何量化 MoE 部署。** 路由器加载 bug（#49185、#49198）会静默失败 — 没有报错，只是生成质量差。如果你运行 Mixtral、Llama 4、Qwen3-MoE 或 compressed-tensors/FP8 checkpoint，升级前请对照已知良好参考验证 logits。
- **将 GLM-5.3-Flash 固定到 v0.5.22**，如果你服务长上下文或 1M token 请求；v0.5.23 存在可复现的引擎状态损坏路径（#48964）。
- **对混合线性注意力 / GDN/KDA 模型以及需要比特精确 logprob 一致性的 NVFP4 checkpoint，禁用或验证完整 prefill CUDA graph**（#48593、#49133）。
- **GB10 / DGX Spark 用户**：将 prefill 长度限制在 ~40k token 以下，或手动监控 host/统一内存 — OOM 路径会杀死 rank 且无诊断信息（#36941），而 DSV4.1 indexer 回退（#49173）意味着 SM120 上 dsv4 prefill 目前比应有速度慢 8 倍。
- **Agent harness 构建者**：关注关于保持多轮 agent 前缀可缓存的 RFC #47763，以及关于同批次前缀 KV 共享的 #48963 — 两者都直接针对 agent 循环的 TTFT 和冗余 prefill 成本（[Issue #47763](https://github.com/sgl-project/sglang/issues/47763)）。

</details>

<details>
<summary><strong>llama.cpp</strong> — <a href="https://github.com/ggml-org/llama.cpp">ggml-org/llama.cpp</a></summary>

# llama.cpp 摘要 — 2026-11-09

## 1. 今日亮点

今天主要是围绕正确性中立的基础设施工作：CUDA/Metal 的 radix Top-K 内核在并列选择时已实现确定性（消除了采样不可复现的一个隐蔽来源），mtmd 多模态栈完成了整合（移除了 `clip.h`）。性能方面，针对 `d_state=64` SSM 扫描的 CUDA 快速路径已落地（为 Zamba2 类架构解锁了此前回退到 CPU 的 GPU 端循环扫描），`moe-cache` 路径获得了两项重要修复：不再因缓存专家而阻塞 CPU，且投机解码不再将 MoE 缓存应用于草稿模型。开放 issue 队列仍以投机解码（`draft-mtp`）的正确性/中止报告以及 Intel 硬件上的 Vulkan/SYCL 后端损坏为主。

## 2. 发布与破坏性变更

无破坏性 API 变更。本窗口内值得注意的构建/行为变更：

- **b12211 — mtmd 重构**：移除 `clip.h`；clip 代码整合进 `mtmd`。直接包含 `clip.h` 的第三方消费者（树外 mmproj 工具）必须迁移。（[PR #31865](https://github.com/ggml-org/llama.cpp/pull/31865)）
- **b12208 — 确定性 radix Top-K**：CUDA/Metal 的 `TOP_K` 现在按列顺序解决并列（block → warp → step → lane；最早者胜出）。使用 `top_k` 的采样输出可能与旧构建略有不同（但稳定）。（[PR #31084](https://github.com/ggml-org/llama.cpp/pull/31084)）
- **b12202 — 精度对齐**：`ggml_prec_set_acc` 调用现在遵循参考实现的精度；GLM4 的 down/output 投影在参考实现不保留 F32 的地方改为 `GGML_PREC_BF16`。GLM4 系列模型预计会有轻微数值偏移。（[PR #31851](https://github.com/ggml-org/llama.cpp/pull/31851)）
- **b12199 — PEG 工具调用清洗**：PEG 工具调用参数中的无效 UTF-8 现已在 `common` 中清洗；下游解析器不再收到格式错误的字节序列。（[PR #31861](https://github.com/ggml-org/llama.cpp/pull/31861)）

## 3. 新模型与硬件支持

- **CUDA SSM 扫描 `d_state=64` 快速路径（b12207）**：为 `SSM_SCAN` 内核中状态大小为 64 的情况补上了缺失的快速路径。Zamba2 风格的循环架构此前每个扫描步都会回退到 CPU；现在在 GPU 上处理。（[PR #31818](https://github.com/ggml-org/llama.cpp/pull/31818)）
- **SpacemiT IME2 MXFP4 稠密重打包（b12210）**：面向 RISC-V SpacemiT IME2 加速器的 CPU 端 MXFP4 重打包。（[PR #31776](https://github.com/ggml-org/llama.cpp/pull/31776)）
- **llamafile bf16 + q8_0 VXE（b12209）**：llamafile 运行时中针对 bf16 和 q8_0 的新向量扩展路径。（[PR #31866](https://github.com/ggml-org/llama.cpp/pull/31866)）
- **OpenCL q8_0 GEMV + 小 n MoE 验证**：新增 `kernel_gemv_noshuffle_q8_0_f32_mc` 和小 n MoE GEMV 路径，改善 OpenCL 上的投机解码验证（Qualcomm 联合贡献）。（[PR #31831](https://github.com/ggml-org/llama.cpp/pull/31831)）

**进行中：**
- [PR #31802](https://github.com/ggml-org/llama.cpp/pull/31802) — 在 CUDA `fattn-mma-f16` 内核内融合 q8_0/q4_0 KV 反量化（消除量化 KV 缓存的 f16 暂存物化过程）。
- [PR #30606](https://github.com/ggml-org/llama.cpp/pull/30606) — SYCL：将 Battlemage（BMG）D=256 单查询解码路由到 FA 向量内核；报告在 Arc Pro B70、kv=65536、GQA4 下每次调用 832 µs 对比 1150 µs。
- [PR #31896](https://github.com/ggml-org/llama.cpp/pull/31896) — `MUL_MAT` src1 精度测试，以及针对 BF16/F32 精度请求的后端回退到 CPU 的门控（Metal 已拒绝不支持的情况）。

## 4. 性能与优化

- **MoE 缓存 CPU 解耦（b12206）**：CPU 部分不再阻塞等待缓存专家，减少 CPU+GPU 混合 MoE 推理的每 token 停顿。（[PR #31888](https://github.com/ggml-org/llama.cpp/pull/31888)）
- **SSM 扫描 GPU 卸载（b12207）**：见上文；消除了 `d_state=64` 模型的 CPU 回退，这曾是这些架构生成时的主要瓶颈。（[PR #31818](https://github.com/ggml-org/llama.cpp/pull/31818)）
- **CUDA/Metal Top-K 确定性（b12208）**：并非提速，但移除了此前的“shift-skip”逻辑和线程时序依赖，使内核行为在多次运行间可复现。（[PR #31084](https://github.com/ggml-org/llama.cpp/pull/31084)）
- **RPC 加载时间改进（开放，[PR #29413](https://github.com/ggml-org/llama.cpp/pull/29413)）**：在 2× DGX Spark 上通过 RDMA 加载 DeepSeek-V4-Flash（145.6 GiB）时，`load_tensors` 快约 5×。
- **IQ2 量化创建提速（开放，[PR #31910](https://github.com/ggml-org/llama.cpp/pull/31910)）**：为 IQ2 创建引入模式缓存；作者报告在 8 个模型 / 5 种架构上各 IQ2 类型输出字节一致。
- **持久化磁盘提示缓存（开放，[PR #28092](https://github.com/ggml-org/llama.cpp/pull/28092)）**：`--cache-disk` 支持混合/SWA 检查点、重启存活、损坏清理和 LRU 磁盘上限驱逐。

## 5. 稳定性与回归

按严重程度排序：

1. **`qwen4exp` 上的 `draft-mtp` kpool 断言** — 服务器在加载时于 `llm_graph_input_kpool::set_input` 中以 `GGML_ASSERT(buffer)` 中止。持续被重新报告（[#30860](https://github.com/ggml-org/llama.cpp/issues/30860)、[#31914](https://github.com/ggml-org/llama.cpp/issues/31914)）；#30860 中有提议的修复，据报告可干净地应用到 master。
2. **量化目标上的投机解码输出偏差**（[#25618](https://github.com/ggml-org/llama.cpp/issues/25618)）— 在贪心采样下，当目标模型为量化时，draft-MTP/draft-dspark 输出与非投机运行产生偏差（bf16 下一致）。39 条评论；开放且未解决 — 若你依赖可复现性则相关。
3. **嵌入请求泄漏 / 服务器静默挂起**（[#31704](https://github.com/ggml-org/llama.cpp/issues/31704)）— 被取消的长嵌入永不会被释放；随后服务器不再提供服务，而 `/health` 仍报告 OK。无修复 PR。
4. **Intel Arc B 系列在长上下文/RAG 下的 Vulkan 损坏**（[#31828](https://github.com/ggml-org/llama.cpp/issues/31828)）— 尽管设置了 `GGML_VK_DISABLE_COOPMAT` 仍存在；开放。
5. **RX 7900 XTX 上 131k 上下文的 Vulkan 78% 解码断崖**（[#27734](https://github.com/ggml-org/llama.cpp/issues/27734)，已关闭/失效）— 子分配碎片化；变通方案 `GGML_VK_SUBALLOCATION_BLOCK_SIZE=4 GiB`。
6. **SYCL AOT 对 >4 GB 模型输出乱码**（[#31685](https://github.com/ggml-org/llama.cpp/issues/31685)）— `GGML_SYCL_DEVICE_ARCH=arl-h` 构建输出 `GGGG…`；通过缓冲区大小标志缓解。
7. **循环状态中的粘性 NaN**（修复 PR 开放，[#31913](https://github.com/ggml-org/llama.cpp/pull/31913)）— 重置逻辑乘以 0 而非写入 0，使单个 NaN 永久持续；在 RPC 上以 q8 KV 缓存命中 Qwen3.8-flash-next。
8. **流式响应尽管有 `Keep-Alive` 仍关闭 TCP 连接**（[#29680](https://github.com/ggml-org/llama.cpp/issues/29680)、[#31863](https://github.com/ggml-org/llama.cpp/issues/31863)，已关闭）— 破坏池化 HTTP 客户端。若你对 llama-server 使用 keep-alive 池，请针对你的构建验证。
9. **MoltenVK 着色器越界写入**（修复 PR 开放，[#31890](https://github.com/ggml-org/llama.cpp/pull/31890)）— macOS Vulkan 上 `test-backend-ops` 中的哨兵不匹配。
10. **SYCL `-sm tensor` 疑似泄漏**（[#27845](https://github.com/ggml-org/llama.cpp/issues/27845)，已关闭/失效）— 双 Intel B70，报告于生成期间。

## 6. 对应用开发者的意义

- **投机解码仍是最有风险的旋钮。** 若你依赖确定性输出（评估框架、回归测试、agent 回放），要么使用 bf16 目标，要么在量化目标上禁用 draft-MTP/dspark，要么固定一个已知良好的构建。今日两大 issue 中有两个是投机解码正确性/中止 bug。
- **工具调用鲁棒性悄然改善。** UTF-8 清洗（b12199）以及开放的语法排序 PR（[#30193](https://github.com/ggml-org/llama.cpp/pull/30193)，Qwen3-Coder/Nemotron 参数键排序）和 Gemma4 宽松 schema 语法工作（[#31915](https://github.com/ggml-org/llama.cpp/pull/31915)）都针对最常见的 agent 失败模式：格式错误的工具参数。若你在 Qwen/Nemotron/Gemma 上运行 agent，请跟踪这些。
- **健康检查并不充分。** Issue #31704（嵌入槽泄漏而 `/health` 健康）提醒你要监控实际吞吐/延迟，而不仅是存活。开放的 UI 性能视图（[#31893](https://github.com/ggml-org/llama.cpp/pull/31893)）若合并，将在 llama-ui 中展示 `/metrics` 数据。
- **混合 CPU/GPU MoE 服务变得更好。** b12206 消除了 `moe-cache` 中的同步停顿；结合 `--moe-cache-cpu-share auto` 工作（[#31286](https://github.com/ggml-org/llama.cpp/pull/31286)），部分卸载的 MoE 部署应能看到更平稳的 token 节奏。
- **持久化提示缓存即将到来。** `--cache-disk`（#28092）将为重放长系统提示的 RAG/agent 工作负载显著降低冷启动延迟 — 若你频繁重启服务器，值得原型验证。
- **树外 mmproj 集成者：现在就处理 mtmd 重构。** 自 b12211 起 `clip.h` 已移除；任何包含它的代码在升级时都会中断。

</details>

<details>
<summary><strong>Ollama</strong> — <a href="https://github.com/ollama/ollama">ollama/ollama</a></summary>

# Ollama 摘要 — 2026-11-09

## 1. 今日亮点

过去 24 小时内没有新版本发布，但 PR 队列活跃，主要集中在后端与调度器的正确性工作上：Rockchip NPU 支持（#19480）、无损 FP8 导入并保持一致的 MoE 专家堆叠（#19614），以及一个调度器修复，使无法停止的 runner 保留在已加载集合中以防止 VRAM 超额分配（#19581）。在 Issue 跟踪器中，780M 上的 AMD Vulkan 回归（#17748）和企业代理环境下 `ollama pull` 返回 403（#19604）仍然处于开放状态，而长期被请求的按模型 `num_parallel` 功能现已在 PR #9546 中实现。

## 2. 版本发布与破坏性变更

无。过去 24 小时内没有新版本；没有 API/配置变更落地。

---

## 3. 新模型与硬件支持

- **Rockchip NPU 后端（RK3588/RK3576）** — PR #19480 新增了 `rocket`/`rknpu` 后端，将提示处理（`MUL_MAT`、`MUL_MAT_ID`、`FLASH_ATTN_EXT`）卸载到 NPU，同时将单 token 解码保留在 CPU 上。[PR #19480](https://github.com/ollama/ollama/pull/19480)
- **无损 FP8 导入 + 一致的 MoE 专家堆叠** — PR #19614 统一了所有源类型的专家堆叠方式（修复了此前以未堆叠专家形式发布的 MoE 模型），并新增了逐张量 FP8 导入；块 FP8 不再被有损地重新量化为 mxfp8。[PR #19614](https://github.com/ollama/ollama/pull/19614)
- **Docker 中的 Nvidia MLX/Vulkan** — PR #18592 补充了缺失的库/配置，使 MLX runner 和 Vulkan 驱动在容器内配合 Nvidia GPU 正常工作。[PR #18592](https://github.com/ollama/ollama/pull/18592)
- **图像生成模型** — 目前在 macOS 上不受支持：`ollama run x/flux2-klein` 会报错“image generation models are not currently supported”（#19615，已关闭）。尚不是受支持的路径。[Issue #19615](https://github.com/ollama/ollama/issues/19615)
- **第三方架构请求** — 有人提交了对“OSADIA Axiom”架构支持的请求（#19611）；没有迹象表明会被接受。[Issue #19611](https://github.com/ollama/ollama/issues/19611)

## 4. 性能与优化

- **混合模型与量化 KV 缓存的精确 VRAM 预测** — PR #19609 修复了 `PredictServerVRAM`，该方法此前对每个块都假定全上下文注意力 KV（对循环 SSM 块 / `full_attention_interval` 而言是错误的），并且始终将 KV 按未量化的 f16 计费。这直接影响 GPU/CPU 放置决策。[PR #19609](https://github.com/ollama/ollama/pull/19609)
- **Metal 预填充分块收缩** — PR #19280 随着上下文增长而减小预填充分块大小，防止 macOS GPU 看门狗在超长提示（例如大型 XML 文件）下杀死进程。[PR #19280](https://github.com/ollama/ollama/pull/19280)
- **按模型并行度** — PR #9546 新增了可通过环境变量和 Modelfile 控制的 `num_parallel`，因此小模型（例如嵌入模型）的上下文不再被服务器级的 `OLLAMA_NUM_PARALLEL` 三倍放大。解决了 #8842 以及已关闭的 #11977/#5693。[PR #9546](https://github.com/ollama/ollama/pull/9546)
- **上下文缓冲区填充生成** — PR #9547 允许 `num_predict==-2` 生成直到上下文缓冲区填满，与 llama.cpp 语义一致。[PR #9547](https://github.com/ollama/ollama/pull/9547)
- **基准测试工具可靠性** — PR #19600 使 `cmd/bench` 在运行不完整时以非零状态退出，并截断复用的输出文件。[PR #19600](https://github.com/ollama/ollama/pull/19600)

## 5. 稳定性与回归

按严重程度排序：

1. **AMD Radeon 780M Vulkan 回归（开放，无修复 PR）** — Ollama ≥0.32.10 在较大模型上失败，报 `radv/amdgpu: Not enough memory for command submission` / `ErrorDeviceLost`；相同硬件在旧版本上正常。[Issue #17748](https://github.com/ollama/ollama/issues/17748)
2. **卸载失败时的调度器 VRAM 超额分配（修复 PR 已开放）** — 当杀死 runner 失败时，调度器仍会释放模型，按满 GPU 进行预测，倾向 CPU，并可能启动第二个 `llama-server`。PR #19581 将无法停止的 runner 保留在已加载集合中。[Issue #19577](https://github.com/ollama/ollama/issues/19577) / [PR #19581](https://github.com/ollama/ollama/pull/19581)
3. **企业代理后 `ollama pull` 返回 403（开放）** — Blue Coat / RHEL 9.2 环境中 `curl` 正常但 systemd 服务返回 403；4 条评论，尚未解决。[Issue #19604](https://github.com/ollama/ollama/issues/19604)
4. **更新后资源回归报告（开放）** — 用户报告在 15.4G RAM / 8G RTX 5060 Laptop 配置上运行 ministral-3 14B，版本更新后行为变差：资源占用更高且输出“更僵硬”。需要分诊。[Issue #19537](https://github.com/ollama/ollama/issues/19537)
5. **Gemma4 工具调用解析器 bug（修复 PR 已开放）** — 45 个标量字符串后跟一个双字符串数组时，可能因占位符冲突（`NUL,comma,NUL`）而静默丢弃一个有效的工具调用。[PR #18355](https://github.com/ollama/ollama/pull/18355)
6. **Gemma4 QAT 元数据拼写错误（已关闭）** — `gemma4:12b-it-qat` 的 Modelfile 键为 `toos` 而非 `tools`，破坏了该模型的工具支持。[Issue #19596](https://github.com/ollama/ollama/issues/19596)
7. **GGUF 解析加固（PR 已开放）** — 拒绝重复张量名（#19571）、零维张量（#19607）和非 2 的幂对齐（#19610）；此前这些能通过元数据解析，仅在加载/检查时失败。[PR #19571](https://github.com/ollama/ollama/pull/19571) · [PR #19607](https://github.com/ollama/ollama/pull/19607) · [PR #19610](https://github.com/ollama/ollama/pull/19610)
8. **CLI 噪音** — `ollama -v/--version` 会输出“could not connect to a running Ollama instance”警告；输出整洁性回归。[Issue #19613](https://github.com/ollama/ollama/issues/19613)
9. **上下文截断丢弃最后一条用户消息** — `chatPrompt()` 按类型无关的位置保留最后一条消息，因此尾部的工具调用消息可能挤掉实际的最终用户轮次。[PR #19058](https://github.com/ollama/ollama/pull/19058)

较低优先级：快速写入时 `WriteWithBackup` 时间戳冲突（#19608）、应用中吞掉服务器启动错误（#19179），以及未设置 API 字段导致的模型抖动（#10003）。

## 6. 对应用开发者的意义

- **多模型部署获得更精细的控制。** 一旦 #9546 落地，你可以按模型调整 `num_parallel`，而不必全局承担上下文倍数税——如果你将嵌入模型与聊天模型共同托管，这一点很重要。
- **如果你使用 AMD Vulkan，请为已知回归做好准备。** 780M iGPU 用户请固定使用 0.32.10 以下的版本，或在升级前跟踪 #17748。
- **企业/代理环境：** TLS 检查代理后 `ollama pull` 失败（#19604）仍然开放；请验证 systemd 服务的代理 CA 处理，而不仅仅是你的 shell。
- **Gemma4 上的 Agent 工具调用：** 关注 #18355（解析器冲突静默丢弃工具调用）和 #19596（已修复的 `toos` 元数据拼写错误）；如果你依赖工具，请重新拉取 QAT 模型。
- **VRAM 规划很快会改善。** 混合/量化 KV 的 VRAM 预测修复（#19609）将使 SSM 混合模型和量化缓存模型的 GPU 卸载决策更准确——合并后预期 CPU 倾向的意外会减少。
- **边缘部署扩展：** Rockchip NPU 后端（#19480）值得在预填充成为瓶颈的 RK3588/RK3576 板卡上评估。

</details>

<details>
<summary><strong>LiteLLM</strong> — <a href="https://github.com/BerriAI/litellm">BerriAI/litellm</a></summary>

# LiteLLM 摘要 — 2026-11-09

## 1. 今日亮点
发布流继续推进，推出了 `v1.110.0-dev.2` 开发标签，现已发布由 cosign 签名的 Docker 镜像，并附有验证步骤文档。问题数量依然居高不下（24 小时内更新 56 个，涉及 304 个 PR），最显著的活动集中在路由/回调热路径性能、Anthropic `/v1/messages` 转换保真度（`extra_body` 透传、回退定价），以及一组支出核算缺陷——当上游用量缺失或格式异常时预算无法关闭。今日一个值得注意的正确性主题是：`/v1/responses` 和 `/v1/messages` 中的流式断连处理会遗留过期的预算预留并保持上游流打开。

## 2. 发布与破坏性变更
- **v1.110.0-dev.2** — 开发构建；Docker 镜像使用提交 `e82a3c6` 中引入的密钥通过 cosign 签名。发布说明中包含供应链验证指南。https://github.com/BerriAI/litellm/releases
- 今日没有配置 schema 的破坏性变更落地，但有两个开放的 PR 被标记为破坏性（`fix(responses)!`、`fix(router)!`）——升级越过它们之前请参阅下文“稳定性”部分了解详情。

## 3. 新模型与硬件支持
- **Mistral 原生 token 计数** — PR 通过 `POST https://api.mistral.ai/v1/responses/input_tokens` 添加上游 token 计数。https://github.com/BerriAI/litellm/pull/50653
- **提议/注册的新 OpenAI 兼容提供商**：
  - Superbot（`superbot-gg`），含支出定价 — https://github.com/BerriAI/litellm/pull/47505
  - Open Uncensored（Chat Completions + 原生 Responses 路由）— https://github.com/BerriAI/litellm/pull/50564
  - OpenScale（DeepSeek V4.1 Flash fp8、GLM 5.3 Flash）— https://github.com/BerriAI/litellm/pull/50397
- **Databricks 按部署的 OAuth M2M**（功能请求，开放中）：目前仅通过 `DATABRICKS_CLIENT_ID`/`SECRET` 支持单一全局服务主体。https://github.com/BerriAI/litellm/issues/48765
- **LocalAI** 提供商请求在漫长尾期后关闭——如果你依赖 GGUF/llama.cpp 后端，值得检查其状态。https://github.com/BerriAI/litellm/issues/1443

## 4. 性能与优化
- **路由器回调过滤 + Anthropic 能力探测缓存**（开放 PR）：针对两个每请求 Python 热路径——在 `callback_filters` 中跳过未使用的部署读取，并在 Rust messages 路由中缓存能力探测。动机：Rust 核心只有在停止将每个部署读回 Python 时，才能从容处理 1,000 个部署。https://github.com/BerriAI/litellm/pull/50641
- **SpendLogDelivery 核算指标**（已关闭）：`AccountingDeliveryRepository.metrics()` 在每次 flush 时扫描*所有*保留的 `LiteLLM_SpendLogDelivery` 行；问题已关闭，暗示采用了有界查询修复。https://github.com/BerriAI/litellm/issues/50475
- **JSON 日志密钥扫描**（开放）：在 JSON 日志格式化期间对未变更字符串进行冗余的正则/密钥扫描；与早前的病态正则问题 #32353 不同。https://github.com/BerriAI/litellm/issues/40247
- **递归工具 schema**（开放，已停滞）：`unpack_defs` 在 `$defs`/`$ref` 高扇入 schema 上仍然无界；Bedrock 和 Vertex 调用方不传递字节预算。在对抗性或生成的工具 schema 上存在数秒至挂起的风险。https://github.com/BerriAI/litellm/issues/34328

## 5. 稳定性与回归（按严重程度排序）

**高 — 计费/正确性**
- **用量缺失/格式异常时密钥预算未扣费** — 当 OpenAI 兼容上游省略 `usage` 时，支出永不记录，`max_budget` 永不关闭；在按 9 次规模设置的预算上，20/20 次调用成功（上游计费 +100.5%）。格式异常的 `usage`（`{"prompt_tokens":"oops",...}`）表现出相同故障。https://github.com/BerriAI/litellm/issues/50124
- **`/v1/responses` 断连遗留完整 Redis 预算预留** — 在首 token 前取消的流式请求记录 HTTP 499，但保留 `finalized=false` 的预留不变。https://github.com/BerriAI/litellm/issues/50528
- **`max_budget` 允许超预算调用** — 在适合 9 次的预算上，即使串行也有 11 次调用被允许（不仅仅是突发竞态）。https://github.com/BerriAI/litellm/issues/50125
- **Anthropic 服务端回退定价错误** — 每次回退尝试都按*应答*模型的费率定价，且未计费的拒绝被计入。修复 PR 开放中（标记为破坏性）。https://github.com/BerriAI/litellm/pull/50556

**高 — 协议/转换**
- **`/v1/messages` 返回误导性 503** — 任何非 `ProxyException` 的上游故障（例如连接被拒绝）都表现为 `"authentication database is temporarily unreachable"`，误导运维人员；`/v1/chat/completions` 返回正确的 50x。https://github.com/BerriAI/litellm/issues/49998
- **无法解析的 200 被抛出为 500** — 提供商返回 HTTP 200 但非 JSON 或 JSON 缺少 `choices`；litellm 抛出晦涩的 APIError 并丢弃 `usage`，导致调用方重试一个已计费的请求。https://github.com/BerriAI/litellm/issues/49793
- **Bedrock Invoke `/v1/messages` 流在分离排空中止后未关闭** — 当 `ANTHROPIC_MESSAGES_MAX_DETACHED_STREAM_DRAINS=0` 时，客户端断连后上游继续生成；httpx 响应永不关闭。https://github.com/BerriAI/litellm/issues/50514
- **Claude 上的 `reasoning_effort="none"`** — 剥离 thinking 配置而非显式禁用，导致上游默认开启的 thinking 保持活跃（已关闭）。https://github.com/BerriAI/litellm/issues/47924
- **Amazon Nova 流式 100% 失败** — httpx zstd 解码器在 Nova 多帧 SSE 间复用已完成的 `decompressobj`（已关闭）。https://github.com/BerriAI/litellm/issues/35589
- **`/v1/messages` 上部署级 `extra_body` 被丢弃** — 按部署配置的 Anthropic 服务端回退被静默忽略；修复 PR 开放于 PR #50558 及路由器合并变体 PR #49839（标记为破坏性）。https://github.com/BerriAI/litellm/pull/50558
- **`/v1/audio/speech` Qwen3-TTS** — 强制 `voice` 参数和自定义参数被剥离（开放）。https://github.com/BerriAI/litellm/issues/20078

**中 — 路由/可观测性**
- **Arize Phoenix 重复计数** — 代理父 span `litellm_proxy_request` 重复 LLM 属性，因此 Phoenix 在旧路径（未设置 `LITELLM_OTEL_V2`）上对每次调用定价两次。修复 PR 开放中。https://github.com/BerriAI/litellm/issues/50649 / https://github.com/BerriAI/litellm/pull/50655
- **路由器重试忽略调用方超时** — `x-litellm-timeout` 仅约束每次尝试；`Retry-After` 休眠不检查调用方剩余预算。https://github.com/BerriAI/litellm/issues/49751
- **MCP 访问组列表泄露范围** — `GET /v1/mcp/access_groups` 返回密钥无法查看/调用的服务器上的组；修复 PR 开放中。https://github.com/BerriAI/litellm/pull/50650
- **MCP 每请求认证头被忽略** — 当未配置静态凭据时，`x-mcp-{alias}-authorization` 解析为无认证。https://github.com/BerriAI/litellm/issues/35403
- **`trim_messages` 在带前缀模型上无操作** — `openai/gpt-4o`、`anthropic/claude-haiku-4-5` 完全跳过裁剪；在多模态列表内容和 `None` 内容上也会失败。修复 PR 开放中。https://github.com/BerriAI/litellm/issues/50639 / https://github.com/BerriAI/litellm/pull/50645 / https://github.com/BerriAI/litellm/pull/50648
- **OpenAI Agents SDK + Bedrock 上的 Claude** — 工具搜索后找不到延迟工具，搜索记录在后续轮次丢失，压缩参数在 `/v1/messages` 上被丢弃。https://github.com/BerriAI/litellm/issues/50104

**低**
- `utils.py` 中的 `ModelNotMappedError` 报告无复现数据。https://github.com/BerriAI/litellm/issues/47922
- `user_url_allowed_hosts` 在 `litellm_settings` 下被忽略（已关闭）。https://github.com/BerriAI/litellm/issues/35177
- 编辑 MCP stdio 服务器会擦除存储的环境变量。https://github.com/BerriAI/litellm/issues/28903
- UI 回调覆盖配置定义的回调（已关闭）。https://github.com/BerriAI/litellm/issues/12118

## 6. 对应用开发者的意义
- **目前不要将 `max_budget` 视为硬性支出上限。** 三个独立问题（#50124、#50125、#50528）表明，当用量缺失、并发高或流被取消时，预算会超额服务。如果你要强制执行成本上限，请在上游侧添加防护（提供商预算/配额），而不是仅信任网关。
- **重试逻辑需要感知“无法解析的 200”情况。** 问题 #49793 意味着你的 SDK 可能对一次成功计费的上游调用看到 500 形态的错误；盲目重试 `acompletion` 可能导致双重计费。考虑使用幂等键或在重试前按 `request_id` 去重。
- **Anthropic `/v1/messages` 消费者应谨慎固定版本。** 多个进行中的修复被标记为破坏性（`fix(responses)!`、`fix(router)!`）并触及 `extra_body` 合并语义。如果你依赖按部署的 `extra_body` 实现 Anthropic 服务端回退，它目前在 `/v1/messages` 上不会到达线上——升级越过 PR #50558 / #49839 后请测试。
- **面向调用方的超时不是总预算边界。** 根据问题 #49751，`x-litellm-timeout` 约束每次尝试；一连串 `Retry-After` 重试可能超出你预期的挂钟时间。请在客户端强制执行总截止时间。
- **可观测性成本重复计数：** 如果你通过旧回调路径使用 Arize Phoenix，在 PR #50655 落地前预计会有约 2 倍的成本归属。或者设置 `LITELLM_OTEL_V2`。
- **工具 schema 卫生很重要** — 具有高扇入的递归 `$defs`/`$ref` schema 可能在 Bedrock/Vertex 上触发无界的 `unpack_defs` 路径；请在 agent 工具定义中预展平或约束生成的 schema。

</details>

<details>
<summary><strong>Unsloth</strong> — <a href="https://github.com/unslothai/unsloth">unslothai/unsloth</a></summary>

# Unsloth 摘要 — 2026-11-09

## 今日亮点
过去 24 小时内没有新版本发布，但 PR 队列非常活跃：一大批由 danielhanchen 编写的 Studio/扩散修复被合并（许多在当天即 CLOSED），此外还有针对 Turing INT8 支持和并发模型加载线程安全修复的开放后续 PR。今天的主线是 Studio 的图像/视频扩散路径（fp8 降噪器、NVFP4、INT8 内核、VAE 解码回退）以及围绕“设置→外观”的 UI 打磨。两个开放的功能请求（`#16980`、`#17126`）表明，对 Studio 中检查点保留和图像编辑端点的需求正在增长。

## 发布与破坏性变更
过去 24 小时内无。未公布 API/配置变更。

## 新模型与硬件支持
- **[OPEN] Studio：T4/Turing 上的 INT8（sm75）** — 解除 `dense_transformer_supported` 拒绝，使 `transformer_quant="int8"` 能在 Turing GPU 上运行，而不是在加载前报错。[#17167](https://github.com/unslothai/unsloth/pull/17167)
- **[OPEN] 仅支持 fp16 的 GPU 上的 FLUX.3** — 按存储保留 bf16 权重，并逐层以 float32 计算，避免 #17124 引入的完整 float32 主机内存物化。[#17173](https://github.com/unslothai/unsloth/pull/17173)
- **[CLOSED] 编译版 ROCm W8A8 线性的按卡判定** — 此前按进程缓存；现在按卡判定，因此 RDNA3→RDNA4（或反之）的序列能选中正确的内核。[#17078](https://github.com/unslothai/unsloth/pull/17078)
- **[OPEN] LLM 训练页面：`constant` 和 `constant_with_warmup` 学习率调度器** — 仅 UI 层面暴露后端已接受的调度器。[#17168](https://github.com/unslothai/unsloth/pull/17168)
- **[Bug] Intel Arc 140V（桌面应用）** — 图像生成模型无法加载。[#17137](https://github.com/unslothai/unsloth/issues/17137)
- **[Bug] gfx1031（RX 6700 XT）上的 ROCm Docker** — 启动检查以“RDNA 2”通过，但 PyTorch 在首次 GPU 操作时段错误。[#17147](https://github.com/unslothai/unsloth/issues/17147)

## 性能与优化
- **[CLOSED] Qwen-Image-2.1 CFG 配对** — 启用真实 CFG 的块流式渲染此前每一步都会将每个流式块上传两次；现在引导分支按流式块配对。[#17111](https://github.com/unslothai/unsloth/pull/17111)
- **[CLOSED] MiniMax-H3 条件器内存** — 仅保留 H3 实际读取的隐藏状态（`hidden_states[50]`）；在 50 层 Qwen3-VL 条件器上，**4096 token 时节省约 2 GiB**。[#17095](https://github.com/unslothai/unsloth/pull/17095)
- **[CLOSED] 加载预取** — 通过 `mincore` 跳过已在页缓存中的块，并按工作线程预算读缓冲区（此前无条件为 64 MiB × 8 工作线程）。[#17112](https://github.com/unslothai/unsloth/pull/17112)
- **[CLOSED] 图像后处理** — `uint8_hwc` 现在按行条带转换，每行条带各做一次 float32 拷贝，而不是物化整个批次。[#17091](https://github.com/unslothai/unsloth/pull/17091)
- **[CLOSED] INT8 融合 MLP / v2 Linear** — 在 `_act_quant_device_ok` 已验证与 torchao 位级一致的情况下，输入使用探测的单遍 Triton 内核量化。[#17114](https://github.com/unslothai/unsloth/pull/17114)
- **[CLOSED] 排队的图像生成** — 现在等待各自的池，而不是占用默认执行器的工作线程。[#17108](https://github.com/unslothai/unsloth/pull/17108)
- **[OPEN] 扩散 Triton 内核的设备定向** — `diffusion_vae_fused.py` 中的九处启动此前隐式地在当前线程的 CUDA 设备上启动；PR #17099（CLOSED）修复为使用输入张量所在的卡。
- **[CLOSED] NVFP4 保护分支** — 消除第二次相同调用时的重新编译（冷解码表记忆），并接受 fp16 输入。[#17081](https://github.com/unslothai/unsloth/pull/17081)

## 稳定性与回归
按严重程度排序：
1. **高 — L4（sm_89）上的 fp8 降噪器挂起**：FLUX.3 klein fp8 在 torch < 2.12 下于 `captured Flux3Transformer2DModel graph 1/4` 处无限挂起。**修复 PR 已开放**：[#17166](https://github.com/unslothai/unsloth/pull/17166)。
2. **高 — 并发扩散加载线程不安全**：`init_empty_weights` 全局修补 torch；并发骨架构建可能使 torch 保持被修补状态，导致间歇性 `399 tensor(s) ...` 加载失败。**修复 PR 已开放**：[#17171](https://github.com/unslothai/unsloth/pull/17171)。
3. **中 — CPU 后端上 MoE 路由器辅助损失发散**：在 `UNSLOTH_FORCE_CPU=1` 下，流水线并行 + 梯度累积 > 1 训练出的损失与单进程不同；仓库自带的奇偶校验测试可复现。尚无修复 PR。[#17054](https://github.com/unslothai/unsloth/issues/17054)
4. **中 — 托管账户忽略已保存的 HF token**：尽管 token 有效，门控模型（如 FLUX.1-dev）仍被拒绝。**修复 PR 已开放**：[#17172](https://github.com/unslothai/unsloth/pull/17172)。
5. **中 — 视频 VAE 解码回退仅匹配 Windows 分配器错误**：未识别 Linux/macOS 的分配器拒绝拼写。**已修复（CLOSED）**：[#17075](https://github.com/unslothai/unsloth/pull/17075)。
6. **低 — LoRA fuse 主机备份可能钉住集成 GPU 内存 / 超出主机 RAM 预留**。**已修复（CLOSED）**：[#17086](https://github.com/unslothai/unsloth/pull/17086)。
7. **低 — Studio 设置 UI**：调色板及其他外观选择器在每次选择后关闭。**修复 PR 已开放**：[#17164](https://github.com/unslothai/unsloth/pull/17164)、[#17170](https://github.com/unslothai/unsloth/pull/17170)；重复的玻璃材质控件 [#17162](https://github.com/unslothai/unsloth/issues/17162)，撰写器设置分散在多个标签页 [#17163](https://github.com/unslothai/unsloth/issues/17163)。
8. **低 — 模型大小不匹配**：Model Hub 与 Model Picker 下拉菜单之间不一致。[#17074](https://github.com/unslothai/unsloth/issues/17074)

## 对应用开发者的意义
- **扩散工作负载是当前活跃的前沿。** 许多当天 CLOSED 的 PR 针对图像/视频渲染（CFG 配对、NVFP4、INT8、VAE 分块、设备定向）。如果你用 Studio 做图像/视频生成，请固定到已知良好的提交——若干修复刚落地，且 fp8/Turing 路径仍有未解决的回归。
- **Turing（T4）用户在开放 PR 中获得 INT8 和安全的 FLUX.3 fp16 渲染**——如果你在较旧的云端 GPU 上提供服务，这与你相关。
- **并发加载尚不安全。** 如果你的部署会并发（或按请求）加载多个扩散模型，请在启用该模式前关注 [#17171](https://github.com/unslothai/unsloth/pull/17171)。
- **托管/组织部署**：门控仓库需要 [#17172](https://github.com/unslothai/unsloth/pull/17172) 中的修复；在合并之前，托管账户无法使用自己的 HF token。
- **MoE + CPU 训练**（流水线并行、梯度累积 > 1）目前不正确——不要依赖它来保证损失奇偶一致性。
- **值得跟踪的功能请求**：图像 LoRA 运行期间的检查点保留（[#16980](https://github.com/unslothai/unsloth/issues/16980)）和图像编辑端点（通过外部 OpenAI 兼容服务器的 `/v1/images/edits`，[#17126](https://github.com/unslothai/unsloth/issues/17126)）——两者都会影响 Studio 自动化流水线。

</details>

---
*本日报由 [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar) 自动生成。*