# AI 基础设施日报 2026-11-11

> 生成时间: 2026-11-10 23:14 UTC | 覆盖项目: 6 个

- [vLLM](https://github.com/vllm-project/vllm)
- [SGLang](https://github.com/sgl-project/sglang)
- [llama.cpp](https://github.com/ggml-org/llama.cpp)
- [Ollama](https://github.com/ollama/ollama)
- [LiteLLM](https://github.com/BerriAI/litellm)
- [Unsloth](https://github.com/unslothai/unsloth)

---

## 横向对比

# 跨项目对比报告 — 2026-11-11

## 1. 生态全景

今日的活动反映出一个正在成熟但仍不稳定的推理栈：五个项目均未发布重大功能版本，但生产路径上的回归（vLLM 结构化输出解码、SGLang SM90 解码、llama.cpp 量化投机解码）占据了最高严重级别的条目。AMD/ROCm 是 vLLM、SGLang、llama.cpp 和 Unsloth 新工作中最大的单一方向——跟踪器、CI 扩展和后端移植——标志着真正开始摆脱仅针对 NVIDIA 的优化。投机解码在每个服务引擎中同时是最大的性能杠杆和最大的正确性风险。LiteLLM 仍深陷 10 月供应链事件的余波（签名 LTS 版本、cosign 验证），而 Ollama 和 Unsloth 则推进本地/桌面用户体验和多模态。总体而言，生态系统处于“加固与移植”阶段，而非“新能力”阶段。

## 2. 活动对比

| 项目 | Issue（24小时） | PR（24小时） | 发布状态 |
|---|---|---|---|
| **vLLM** | 111 个 Issue 更新 | 集中在 Rust 前端、ROCm/AITER 注意力、采样器内核、KV-connector | 无发布；无破坏性变更 |
| **SGLang** | Issue 跟踪器活跃（SM120 缺口、SM90 回归） | 聚集于扩散内核融合、HiCache、Rust 服务器 | 无发布；main 分支上 `transformers` 5.12.1 固定版本不兼容 |
| **llama.cpp** | 中等 | 24 小时内 9 次构建 | **b12262**（最繁忙的发布者）；源码树重构破坏了树外 `common.cuh` 用户 |
| **Ollama** | Vulkan/MLX 内存回归、Cloud API 限制 | MLX 并发、Vulkan iGPU mmap、Unix 套接字 | **v0.40.17-rc0** + llama.cpp b12257 + MLX 升级 |
| **LiteLLM** | 约 196 个 Issue 更新 | Rust 热路径、Helm/Docker 整合、安全回补 | **v1.108.5 (LTS)**，回补至 v1.104.9 / v1.103.11 |
| **Unsloth** | 桌面/Studio UX bug 占主导 | 图像编辑、vLLM/SGLang token 预算、反向代理 | 无发布；行为变更：按需加载 PDF 依赖 |

**解读：** llama.cpp 和 Ollama 是今天唯一发布带标签产物的项目（每日构建 vs RC 节奏）。LiteLLM 在安全压力下发布。vLLM、SGLang 和 Unsloth 处于审查密集模式，无标签发布。

## 3. 模型支持竞赛

| 项目 | 今日新增模型/架构支持 | 值得注意 |
|---|---|---|
| **vLLM** | GLM 5.3（CUDA 优化跟踪器）、ROCm Qwen3.8-Flash-Next（gfx950）、Phi4Flash V1 移植、Gemma4/3n PLE 修复、MoeWNA16 AWQ 键 | 最广泛的架构覆盖；最深的 MoE/量化配置面 |
| **SGLang** | MI355X 上的 DeepSeek-V4.1-Flash 跟踪器、RDNA3/4 CI 提案、多模态 GPU 功能准入 | AMD 优先推进；扩散后端（AITER 环形注意力） |
| **llama.cpp** | **Kimi K3.1 视觉（mtmd）**、Qwen delta-net 的 Vulkan 分块转置、Hexagon HMX、OpenCL Adreno FA | 最快落地新视觉模型；最广的硬件矩阵 |
| **Ollama** | **MLX 上的 Nemotron 3.5 Bolt 74B-A9B**（3-bit 预量化、LiLiCorr 投机解码）、决策模型支持 | MLX 深度；消费级设备聚焦 |
| **LiteLLM** | `azure_ai/mistral-large-4-latest`、gpt-5.6 agent 流程的 Azure Responses `phase` 重放 | 目录/定价对齐，非引擎级 |
| **Unsloth** | **MiniMax-H3-Image**、外部 `/images/edits`、反向代理支持 | 图像/编辑流水线而非 LLM 架构 |

**谁领先：** vLLM 在 LLM 架构广度和量化配置灵活性上领先；llama.cpp 在最快合并新视觉/多模态支持上领先；Ollama 在 MLX 原生旗舰模型上领先；Unsloth 在图像生成流水线上领先。竞赛是**按层分化的**——每个项目占据不同的细分领域，而非正面竞争。

## 4. 性能前沿

优化工作集中在**五个不同的战场**：

- **采样/内核：** vLLM 的精确 Triton top-k/top-p 采样器（数据中心快 64–69%，B300 上 c256 时 2.5ms）是头条胜利；SGLang 的扩散内核融合集群（RMSNorm+残差、head-128 均方、打包图像 QK-norm）针对扩散工作负载；llama.cpp 的 granite-switch 通过消除 896 个无条件 `mul_mat_id` 操作报告 **11.1× batch-1 解码**。
- **KV 缓存 / HiCache / P/D 分离：** vLLM LMCache 可导出分配 + MLA/DCP 预填充查询收集；SGLang HiCache 固定内存索引暂存；vLLM MooncakeConnector ROCm RFC。
- **投机解码：** 既是性能杠杆（vLLM 预填充时缩短草稿、目标 logit 截断）也是回归来源（vLLM DFlash TP>1 崩溃、llama.cpp 量化目标发散、Unsloth MTP 接受率下降）。
- **分布式服务：** vLLM 将 ROCm CI P50 从 73 分钟缩减至约 30 分钟；SGLang UniBoost 饥饿有界调度器和多项目评分主机同步移除（约 1.53×）。
- **量化：** vLLM Triton FP8 MoE 专家路径（fp32 缩放 + 融合 SiLU+量化）、MoeWNA16 AWQ 准入。

**要点：** 前沿已从原始吞吐量转向**调度公平性、缓存经济学和采样器开销**——简单的 GEMM 优化已经完成。

## 5. 层级定位

| 层级 | 项目 | 区分角色 |
|---|---|---|
| **数据中心服务引擎** | vLLM、SGLang | 高吞吐、多 GPU、P/D 分离、投机解码、分布式 KV——在每 token 性能和硬件广度上竞争 |
| **本地/边缘运行时** | llama.cpp、Ollama | llama.cpp = 最广的硬件后端矩阵（CUDA/HIP/Vulkan/SYCL/OpenCL/Hexagon/CANN）；Ollama = 打包的本地用户体验（MLX、Vulkan iGPU、Unix 套接字） |
| **LLM 网关** | LiteLLM | 路由、限流、支出、提供商转换——正确性和供应链安全是其关键关切 |
| **训练/微调** | Unsloth | Studio 桌面 + GGUF 导出 + 微调；桥接训练和本地推理 |

各层是**互补而非替代的**：生产栈可能在边缘使用 LiteLLM，用 vLLM/SGLang 服务，用 Unsloth 微调→GGUF，用 llama.cpp/Ollama 本地运行。这就是跨项目回归很少相关的原因——但投机解码正确性正成为**跨层系统风险**。

## 6. 趋势信号

**今日可见的行业趋势：**

1. **AMD/ROCm 正从“跟踪器”升级为“一等公民”。** vLLM、SGLang、llama.cpp 和 Unsloth 今天都有 AMD 特定工作——gfx950/MI355X 性能跟踪器、RDNA CI 提案、ROCm 6.3 最低版本、gfx1031 段错误报告。规划 GPU 采购的团队应停止将 ROCm 视为实验性。
2. **投机解码是头号双用途风险。** 它同时是最大的延迟杠杆和 vLLM、SGLang、llama.cpp 和 Unsloth 中最高严重级别 bug 的来源。**在生产中信任它之前，请按模型×量化×TP 配置验证接受率。**
3. **供应链加固现已成为基本要求。** LiteLLM 事件后签名 LTS + cosign 要求应被视为每个基础设施依赖的模板——固定并验证镜像，审计锁文件。
4. **结构化输出/工具调用正确性是新的前沿。** vLLM 结构化输出回归、llama.cpp JSON/工具参数转义、LiteLLM 工具路由丢弃、SGLang `</think>` 解析器分裂——agent 可靠性取决于这些边缘情况，而非原始 tokens/秒。
5. **面向 agentic 工作负载的调度公平性正在兴起。** SGLang UniBoost 和 agent 前缀可缓存性 RFC 表明经典令牌桶调度不足以应对多轮 agent 流量。
6. **多模态在本地运行时上变得更便宜。** Kimi K3.1 视觉、共享 mtmd 缓冲区和 MiniMax-H3-Image 使多模型本地服务器更经济——重新测量 VRAM 预算。

**agent/应用开发者应关注：**
- **有意固定版本**——SGLang Hopper 用户保持 v0.5.22；LiteLLM 用户迁移到 `:lts` + cosign；llama.cpp 投机解码用户将 GGUF 量化 + 草稿头一起固定。
- **测试失败路径**——SSE keepalive 后错误（LiteLLM）、`/v1/responses` 返回 200 但状态为 `failed`（SGLang）、结构化输出吞吐（vLLM）：这些是 agent 静默降级的方式。
- **评估和重放工作负载优先使用批处理/投机安全解码**；将激进的投机解码保留给监控接受率的延迟敏感交互路径。
- **跟踪两个 RFC**——SGLang UniBoost 调度器和 agent 前缀可缓存性——作为 2027 年服务 agent 流量的可能蓝图。

---

## 各项目详细报告

<details>
<summary><strong>vLLM</strong> — <a href="https://github.com/vllm-project/vllm">vllm-project/vllm</a></summary>

# vLLM 摘要 — 2026-11-11

## 1. 今日亮点

今日 Issue 数量较多（共更新 111 个 Issue，主要是 ROCm/AMD 追踪器和量化 RFC），过去 24 小时内没有新版本发布。最具影响力的开放讨论包括：一个关于批量不变 cuBLAS 路由的已撤回提案（`#65569`）、一个改进 `MooncakeConnector` 以支持非 CUDA P/D 分离的 RFC（`#60523`），以及有信号表明结构化输出暂存导致了约 2 倍的解码性能回退（`#62442`）。在 PR 方面，工作集中在 Rust 前端正确性修复、ROCm/AITER 注意力路径、采样器内核重写以及 KV 连接器可导出分配。

## 2. 版本发布与破坏性变更

过去 24 小时内无。

## 3. 新模型与硬件支持

- **GLM 5.3 优化追踪器** — 与 GLM-5.2 相同的 `glm_moe_dsa` 基础；后续 CUDA 优化提案：[vllm#57406](https://github.com/vllm-project/vllm/issues/57406)
- **ROCm Qwen3.8-Flash-Next（gfx950 / MI355X）** — `amd/Qwen3.8-Flash-Next-Quark-MXFP4` 的性能追踪器（GatedDeltaNet、QuerySparse Attention、HyperConnection、MXFP4 MoE）：[vllm#59575](https://github.com/vllm-project/vllm/issues/59575)
- **Phi4Flash V1 移植**（V0 弃用）— 寻求帮助，Mamba 状态移植工作：[vllm#23957](https://github.com/vllm-project/vllm/issues/23957)
- **Gemma4 / Gemma3n PLE 缓存设备放置修复** — 逐层嵌入缓存现在分配在输入设备上：[vllm#62659](https://github.com/vllm-project/vllm/pull/62659)
- **MoeWNA16Config 现在接受 AWQ 键名**（`w_bit`/`q_group_size`），除 GPTQ 名称外：[vllm#66488](https://github.com/vllm-project/vllm/pull/66488)

## 4. 性能与优化

- **精确 Triton top-k/top-p 采样器内核** — 在 NVIDIA 数据中心 GPU 上提速 64–69%，RTX PRO 6000 上提速 42%，MI355X 上提速 29%；在 Qwen3.6 验证行（B300）上，c1 时快约 183µs，c256 时快 2.5ms：[vllm#66792](https://github.com/vllm-project/vllm/pull/66792)
- **ROCm 上用于大规模 fp8 解码批次的 AITER 汇编分页注意力** — 通过 `EXPORTS_FD` 实现 LMCache MP 可导出 KV 缓存分配：[vllm#66082](https://github.com/vllm-project/vllm/pull/66082)、[vllm#66674](https://github.com/vllm-project/vllm/pull/66674)
- **MLA/DCP 预填充查询收集** — 在 NoPE→KV-latent 扩展之前收集更窄的查询，减少查询负载：[vllm#66906](https://github.com/vllm-project/vllm/pull/66906)
- **投机解码：在预填充密集步骤缩短草稿**（`num_speculative_tokens_under_prefill_load`）以减少 TTFT/E2EL：[vllm#66226](https://github.com/vllm-project/vllm/pull/66226)
- **投机解码：在验证期间对目标 logits 加载应用 top-k/top-p 截断**：[vllm#66534](https://github.com/vllm-project/vllm/pull/66534)
- **Triton FP8 MoE 专家路径** — 保留 fp32 激活缩放并重新启用融合 SiLU+量化：[vllm#64906](https://github.com/vllm-project/vllm/pull/64906)
- **ROCm CI 信号时间** — RFC 将 P50 PR 等待时间从 73 分钟降至约 30 分钟：[vllm#66411](https://github.com/vllm-project/vllm/issues/66411)

## 5. 稳定性与回退

按严重程度排序：

1. **TP>1 时 DFlash 接受率崩溃**（MiMo-V2.6-Flash-MOPD，TP1 正常）— 张量并行下投机解码失效；未发现修复 PR：[vllm#66387](https://github.com/vllm-project/vllm/issues/66387)
2. **自 `#45424` 语法位掩码暂存以来约 2 倍的结构化输出解码回退**，`#49919` 未修复：[vllm#62442](https://github.com/vllm-project/vllm/issues/62442)
3. **SMG MoRI-IO P/D 分离连接器在 ROCm 上失效**（已关闭）：[vllm#59116](https://github.com/vllm-project/vllm/issues/59116)
4. **DSv4.1 K-cache int32 溢出** — 行写入超出边界约 4GiB；在 MI355X 上 TP8 时启动崩溃。修复 PR 使用 int64 偏移：[vllm#65926](https://github.com/vllm-project/vllm/pull/65926)
5. **0.26.0 DSv4 性能变更导致生产 2 节点部署投机解码接受率下降约 10.6%**（隔离 A/B 测试）：[vllm#49927](https://github.com/vllm-project/vllm/issues/49927)
6. **MiniMax-M2.7 TP4+EP 吞吐量回退**（v0.29–0.32）：[vllm#63705](https://github.com/vllm-project/vllm/issues/63705)
7. **批量不变路由到 Triton** — cuBLAS 路由提案已撤回（cuBLAS 并非对所有形状都批量不变）：[vllm#65569](https://github.com/vllm-project/vllm/issues/65569)
8. **Rust 前端**：顶层 `thinking` 被丢弃；`system.tools` 被丢弃。修复 PR 已开放：[vllm#66907](https://github.com/vllm-project/vllm/pull/66907)、[vllm#66908](https://github.com/vllm-project/vllm/pull/66908)
9. **FlashInfer 融合 MoE 终结非确定性** — 新增退出选项环境变量 `VLLM_FLASHINFER_MOE_FUSED_FINALIZE`：[vllm#66905](https://github.com/vllm-project/vllm/pull/66905)
10. **MTP 投机解码下混合 GDN 前缀缓存未命中**（Qwen3.5-122B-A10B）— 修复 PR 已开放：[vllm#52244](https://github.com/vllm-project/vllm/pull/52244)

## 6. 对应用开发者的意义

- **结构化输出**：如果你依赖语法约束解码，在当前构建上预计解码吞吐量会明显降低，直到暂存回退（`#62442`）解决；升级前请先做基准测试。
- **投机解码 + TP**：使用草稿模型的多 GPU 部署应验证接受率——DFlash 崩溃（`#66387`）和 MiniMax EP 回退（`#63705`）都影响生产路径。
- **批量不变性**：不要假设 cuBLAS 能处理所有形状；在 RL/验证循环中保持保守的 `batch_invariance` 设置，并参考 `#66550` 中提到的零不匹配 RL 配方。
- **Rust 前端用户**：thinking 控制和系统消息工具声明存在已知缺口——如果你的 agent 依赖这些功能，请固定使用 Python 前端，直到 `#66907`/`#66908` 落地。
- **采样密集型工作负载**：新的精确 Triton top-k/top-p 内核（`#66792`）为大批量采样提供了显著的加速；B300 上 c256 时 2.5ms 的改进值得高吞吐服务场景关注。

</details>

<details>
<summary><strong>SGLang</strong> — <a href="https://github.com/sgl-project/sglang">sgl-project/sglang</a></summary>

# SGLang 摘要 — 2026-11-11

## 1. 今日亮点
过去 24 小时内没有新版本发布，但 issue 追踪器被一个大型 SM120（RTX PRO 6000 Blackwell）注意力后端缺口以及一个新报告的 SM90 解码回归所主导——后者发生在 v0.5.22 到 v0.5.23 之间（DeepSeek-V4.1-Flash 在并发 12 时每步慢约 33–45%）。性能方面，一批扩散内核融合 PR 和 HiCache/索引上传暂存工作正在评审中，而 Rust 服务端栈随着错误类型化和 KV 分片可扩展性变更继续成熟。

## 2. 版本发布与破坏性变更
今日无（过去 24 小时无版本发布）。跟踪提示：issue [#38183](https://github.com/sgl-project/sglang/issues/38183) 报告 `transformers` 5.12.1 版本锁定不兼容，导致当前 main 分支上 `import sglang` 失败——与任何锁定依赖的人相关。

## 3. 新模型与硬件支持
- **[AMD] RDNA3/RDNA4（gfx1100/gfx1201）CI + 上游化计划** — issue [#46900](https://github.com/sgl-project/sglang/issues/46900) 提议将消费级 AMD GPU 纳入 CI。
- **[AMD][ROCm] MI355X（gfx950）上的 DeepSeek-V4.1-Flash** — 性能追踪 issue [#49327](https://github.com/sgl-project/sglang/issues/49327)，针对 TP4 文本+图像，含/不含 DSpark 投机解码。
- **[AMD] AITER 后端上的 Ring attention** — PR [#24053](https://github.com/sgl-project/sglang/pull/24053) 为 AMD 扩散工作负载启用 ring attention。
- **cuDNN 注意力后端** — 长期存在的增强 issue [#2272](https://github.com/sgl-project/sglang/issues/2272) 仍未关闭；将加入 NVIDIA 维护的 paged attention。
- **多模态 GPU 特性发布准入** — PR [#47769](https://github.com/sgl-project/sglang/pull/47769) 为多模态特性加入有界 CUDA IPC/VMM 准入控制。

## 4. 性能与优化
- **SM90 解码回归** — issue [#49336](https://github.com/sgl-project/sglang/issues/49336)：在 8×H20-3e 上运行 DeepSeek-V4.1-Flash、并发 12、上下文 8K–64K 时，v0.5.22 → v0.5.23 导致**每步解码时间增加 33–45%**；`use_deep_select_decode` 在 Hopper 上也失去接受率。未引用修复 PR——二分定位为高优先级。
- **SM120 上的 DeepSeek-V4 预填充** — issue [#33422](https://github.com/sgl-project/sglang/issues/33422)：在相同的 4×RTX PRO 6000 硬件上约 2–7K tok/s，而 vLLM/marlin 约 12.5K；请求调优指导。
- **扩散内核融合（BBuf）** — 多个 PR 落地：`ming` 中的 RMSNorm+残差 epilogue 融合（[#49456](https://github.com/sgl-project/sglang/pull/49456)）、`nomas` 中精确 head-128 均方归约融合（[#49308](https://github.com/sgl-project/sglang/pull/49308)）、`joy` 在 SM103 上的 packed-image QK-norm/RoPE（[#49417](https://github.com/sgl-project/sglang/pull/49417)），以及 `anima` 中跨串行 CFG 分支的 timestep 调制复用（[#49504](https://github.com/sgl-project/sglang/pull/49504)）。
- **HiCache 内核后端索引暂存** — PR [#49633](https://github.com/sgl-project/sglang/pull/49633) 将主机索引上传暂存于 pinned memory，修复此前 `move_indices` 的异步语义。
- **多条目评分移除主机同步** — PR [#49412](https://github.com/sgl-project/sglang/pull/49412)：启动后冻结 GC + 避免主机同步 → 离线 `Engine.score` 吞吐提升 **1.53×**（GPU 忙碌率 63–67% → 更高）。
- **MoE 路由 GEMM 统一** — PR [#49668](https://github.com/sgl-project/sglang/pull/49668) 将路由 GEMM 统一到共享的 `RouterGate` 之后，目标为长 CoT 路由稳定性和解码开销。
- **UniBoost 饥饿有界调度** — RFC [#48519](https://github.com/sgl-project/sglang/issues/48519) 提出一种带等待上限的调度策略，面向智能体工作负载。

## 5. 稳定性与回归
按严重程度排序：
1. **SM90 解码回归（#49336）** — v0.5.22→v0.5.23 慢 33–45%。对生产 Hopper 部署影响最大；尚无修复 PR。
2. **SM120 上 fa4 注意力崩溃（#42012）** — GLM-5.3-Flash 在 CUDA-graph 捕获时崩溃（混合 extend reshape）；在 RTX PRO 6000 上 triton 是唯一可用的后端。
3. **HiCache + Mooncake OOM 崩溃（#45989）** — `buffer_only` + L3 导致调度器准入一个无法完成的 chunked prefill，随后出现“Prefill out of memory”，且所有 radix 节点被锁定。在 v0.5.21 上报告。
4. **`/v1/responses` 流式在上下文长度超限时返回 HTTP 200 + `response.failed`**（#48557），与 `/v1/chat/completions` 返回 400 不一致——对智能体客户端而言是 API 契约问题。
5. **推理解析器在第一个字面 `</think>` 处分割**（#48791）——CoT 内引用的 think 标签会截断 `reasoning_content`，并将剩余内容泄漏到 `content`。
6. **Rust 服务端 `/generate` 缺少 BOS**（#49519）——v0.5.22 上文本提示跳过了 `add_bos_token` 分词器。
7. **Qwen4-Exp PLE BF16→FP8 误转换**（#49493）——当配置中设置 `ple_embedding_dtype` 时，n-gram 分片以单位缩放转换。
8. **GLM-4.7-Flash W4A16 MLA 加载失败**（#49369）——加载后步骤读取 packed 层中不存在的 `kv_b_proj.weight`。
9. **客户端断开后 `pause_generation(abort)` + `release_memory_occupation` 导致调度器崩溃**（#44840）。
10. **CI 不稳定性追踪（#42752）** — 192 条评论；B200 冷 JIT 超时（#49340）已解决。

## 6. 这对应用开发者意味着什么
- **如果你在 Hopper 上，请谨慎锁定 SGLang 版本**：对于 DeepSeek-V4.1-Flash + DSpark，v0.5.23 相比 v0.5.22 存在可测量的解码回归；在 #49336 解决前请保持 v0.5.22。
- **Blackwell/SM120 用户**：预计 fa4 在 GLM 类模型的混合 extend 上会失败；在 #42012 落地前请显式设置 triton 注意力后端。
- **智能体框架作者**：有两个 issue 直接影响你——`</think>` 解析器分割（#48791）会破坏推理输出，而 `/v1/responses` 返回 200 但状态为 failed 的行为（#48557）会破坏错误处理。智能体前缀可缓存性 RFC（[#47763](https://github.com/sgl-project/sglang/issues/47763)）和 UniBoost 调度器 RFC（[#48519](https://github.com/sgl-project/sglang/issues/48519)）预示着即将到来的对多轮前缀复用和过载下公平调度的一等支持。
- **HiCache/Mooncake L3 用户**：在 #45989 修复前避免 `buffer_only` 模式——它在负载下可能使调度器死锁。
- **评分工作负载**：多条目评分移除主机同步（#49412）是一个低风险、约 1.5× 的离线吞吐提升，值得跟踪合并。

</details>

<details>
<summary><strong>llama.cpp</strong> — <a href="https://github.com/ggml-org/llama.cpp">ggml-org/llama.cpp</a></summary>

# llama.cpp 摘要 — 2026-11-11

## 1. 今日亮点

今天是发布密集的一天（24 小时内 9 个构建），主要围绕多模态和 CUDA 的整理工作：Kimi K3.1 视觉支持落地 mtmd，mtmd 子上下文现在共享计算缓冲区，CUDA 图状态从 `common.cuh` 中移出以减少重建扇出。在正确性方面，两个修复针对工具调用的健壮性——工具参数中的控制字符转义和 `allOf` JSON-schema 语法 bug——两者都与 agent 框架直接相关。

## 2. 发布与破坏性变更

- **b12262 — CUDA：图复用时跳过图兼容性检查**（[#32004](https://github.com/ggml-org/llama.cpp/pull/32004)）。仅为内部行为变更；无 API 影响。
- **b12261 — mtmd：跨子上下文共享计算缓冲区**（[#31945](https://github.com/ggml-org/llama.cpp/pull/31945)）。为音频生成增加了加载时分配；如果加载多个 mtmd 子上下文，请关注内存占用。
- **b12257 — CUDA：图/流状态移出 `common.cuh`**（[#32001](https://github.com/ggml-org/llama.cpp/pull/32001)）。源代码树重构；树外代码若通过包含 `common.cuh` 来使用 `ggml_cuda_graph` / `ggml_cuda_stream_context`，将会中断。
- **b12254 / b12253 — 工具调用 JSON 修复**（[#31834](https://github.com/ggml-org/llama.cpp/pull/31834)、[#31937](https://github.com/ggml-org/llama.cpp/pull/31937)）。带普通类型的 `allOf` 不再产生空对象语法；原始控制字符（例如 pythonic 工具参数中的换行符）现在会被转义，客户端不再收到无效 JSON。
- **b12252 — CANN**：对不支持的缓冲区类型快速失败（[#31270](https://github.com/ggml-org/llama.cpp/pull/31270)）。
- **b12255 — plamo2**：停止重命名循环缓存张量（[#31984](https://github.com/ggml-org/llama.cpp/pull/31984)）。

## 3. 新模型与硬件支持

- **Kimi K3.1 视觉**已添加到 mtmd（[b12259, #31980](https://github.com/ggml-org/llama.cpp/pull/31980)）；同时修复了 K2.5 和 K3.1 的预处理以及投影器 MLP。
- **Vulkan**：用于 Qwen delta-net 卷积状态路径的平铺转置拼接着色器（[b12250, #30591](https://github.com/ggml-org/llama.cpp/pull/30591)）——避免了通用拼接内核路径。
- **OpenCL（Adreno）**：分解式 flash-attention 提案（[#32046](https://github.com/ggml-org/llama.cpp/pull/32046)）——融合 FA 在某些 Adreno 配置上受资源限制。
- **Hexagon**：在缺少 FP16 HMX 的 v73 SoC 上为 Q4_0 matmul + FA prefill 提供整数 HMX（[#29740](https://github.com/ggml-org/llama.cpp/pull/29740)）；解决了 SM7750 垃圾输出 bug。
- **HIP**：ROCm 最低版本提升至 6.3（[#31617](https://github.com/ggml-org/llama.cpp/pull/31617)）——构建流水线需注意。
- 进行中：**granite-switch** LoRA-delta 门控（[#32047](https://github.com/ggml-org/llama.cpp/pull/32047)）、Zaya1 8B 草稿（[#23112](https://github.com/ggml-org/llama.cpp/pull/23112)）。

## 4. 性能与优化

- **granite-switch**：基于批次 token 混合对适配器增量进行门控，通过在 30.6B 模型上每图消除 896 个无条件 `mul_mat_id` 操作，报告 **11.1× batch-1 解码**提升（[#32047](https://github.com/ggml-org/llama.cpp/pull/32047)，开放中）。
- **CUDA**：复用时跳过图兼容性检查（[#32004](https://github.com/ggml-org/llama.cpp/pull/32004)）；头文件拆分大幅缩小了 CUDA TU 的增量重建范围（[#32001](https://github.com/ggml-org/llama.cpp/pull/32001)）。
- **mtmd**：共享计算缓冲区减少了每子上下文的分配（[#31945](https://github.com/ggml-org/llama.cpp/pull/31945)）。
- **Vulkan**：coopmat1 FA 行平铺 RFC 将查询 token 与 GQA 头打包以共享 K/V 读取（[#30190](https://github.com/ggml-org/llama.cpp/pull/30190)，WIP）。

## 5. 稳定性与回归

按严重程度排序：

1. **严重 — 投机解码在量化目标上发散**（[#25618](https://github.com/ggml-org/llama.cpp/issues/25618)，40 条评论）。贪心（`temp=0, top_k=1`）draft-mtp/dspark 输出在 Q4_K_M 目标上与原生不同；bf16 匹配。Ngram 投机不受影响。未引用修复 PR——在正确性敏感路径中避免在量化目标上使用草稿模型投机解码。
2. **严重（遗留）— 投机解码段错误，405B + 8B 草稿**（[#9949](https://github.com/ggml-org/llama.cpp/issues/9949)）。
3. **高 — DFlash 投机解码 KV 污染**（[#32045](https://github.com/ggml-org/llama.cpp/pull/32045)，修复开放中）。被拒绝的草稿位置残留在缓存中，导致约 20 个 token 后出现 Metal 计算错误。变通方法：合并前禁用 DFlash。
4. **高 — DeepSeek V4 / Qwen4-Exp 嵌入崩溃**，因 MTP 隐藏状态宽度不匹配导致越界读取（[#32044](https://github.com/ggml-org/llama.cpp/pull/32044)，修复开放中）。
5. **中 — SYCL MoE + `-sm layer` 在双 Arc Pro B60 上使用 40 GB MoE 缓存时卡顿**（[#31510](https://github.com/ggml-org/llama.cpp/issues/31510)）；SYCL MoE 上 logprobs 不确定（[#31206](https://github.com/ggml-org/llama.cpp/issues/31206)）。
6. **中 — Vulkan/Adreno 驱动故障**：`vkCreateComputePipelines` `VK_ERROR_UNKNOWN` 与 shaderc/NDK SPIR-V 漂移相关（[#28635](https://github.com/ggml-org/llama.cpp/issues/28635)）；Adreno 840 在 27B TQ1_0 上于 `vkGetDeviceFaultInfoEXT` 中崩溃（[#30649](https://github.com/ggml-org/llama.cpp/issues/30649)）。
7. **中 — SM70 V100 CUDA FA 在非标准头维度上崩溃**（[#28661](https://github.com/ggml-org/llama.cpp/issues/28661)）。
8. **低 — llama-server 接受缺失/错误的 Content-Type**（[#31979](https://github.com/ggml-org/llama.cpp/issues/31979)）；`/v1/responses` 在推理项上拒绝 `summary: null`（[#29159](https://github.com/ggml-org/llama.cpp/issues/29159)，已关闭）。

## 6. 对应用开发者的意义

- **工具调用正在变得更安全**：b12253 和 b12254 关闭了两条向客户端产生无效 JSON 的路径——如果你严格解析工具参数（你应该这样做）或使用带原始类型的 `allOf` schema，请升级。
- **不要盲目信任投机解码**：如果你提供量化权重并依赖草稿模型投机解码来降低延迟，贪心输出可能会发散。对于评估/回放工作负载，固定使用原生解码或 bf16 目标；跟踪 #25618。
- **DFlash 有一个已知的缓存 bug 正在处理中**（[#32045](https://github.com/ggml-org/llama.cpp/pull/32045)）；在合并前用标志将其门控。
- **多模态流水线改进**（共享 mtmd 缓冲区、Kimi K3.1 视觉、通过 [#31837](https://github.com/ggml-org/llama.cpp/pull/31837) 在 Discover 中暴露音频能力）使多模型服务器运行成本更低——如果加载多个 mmproj 模型，请重新测量 VRAM 余量。
- **构建卫生**：HIP 用户需要 ROCm ≥ 6.3；CUDA 树外集成必须停止依赖 `common.cuh` 获取图/流类型。

</details>

<details>
<summary><strong>Ollama</strong> — <a href="https://github.com/ollama/ollama">ollama/ollama</a></summary>

# Ollama 摘要 — 2026-11-11

## 1. 今日亮点
Ollama 发布了候选版本 v0.40.17-rc0，核心亮点是一项针对 Vulkan 集成 GPU 使用 mmap 的修复——对共享内存 iGPU 而言是显著的内存占用优化。与此同时，llama.cpp 升级至 b12257，MLX/MLX-C 版本也同步更新，MLX runner 新增了用于基准测试的 draft 禁用功能，并发生成支持也已进入评审阶段。Issue 流量仍以 Vulkan/MLX 内存回归和 Cloud API 限制为主。

## 2. 版本发布与破坏性变更
- **v0.40.17-rc0** — 包含 ChatGPT CLI 恢复/优雅重启改进（[#19583](https://github.com/ollama/ollama/pull/19583)）、OpenCode 启动设置的 thinking 元数据（[#19409](https://github.com/ollama/ollama/pull/19409)），以及 Vulkan iGPU 的 mmap 支持（[#19640](https://github.com/ollama/ollama/pull/19640)）。
- **llama.cpp 升级至 b12257**（[#19646](https://github.com/ollama/ollama/pull/19646)）以及 **MLX/MLX-C 版本升级**（[#19647](https://github.com/ollama/ollama/pull/19647)）。
- 今日无已记录的 API 破坏性变更。注意 `--runner` 变体选择正在对外暴露（mlx/gguf），旧版 runner 名称仍可兼容使用（[#19644](https://github.com/ollama/ollama/pull/19644)）。
- **CLI 行为变更（已关闭）**：`-v/--version` 虚假"警告"文本问题已报告修复（[#19613](https://github.com/ollama/ollama/issues/19613)）。

## 3. 新模型与硬件支持
- **Nemotron 3.5 Bolt 74B-A9B** 登陆 MLX：逐层专家配置、LiLiCorr 投机解码、预量化 affine 3-bit 导入及混合精度专家堆叠（[#19229](https://github.com/ollama/ollama/pull/19229)）。
- **Vulkan 集成 GPU**：mmap 路径避免了主机缓冲区中 CPU 驻留张量导致的内存占用翻倍（[#19640](https://github.com/ollama/ollama/pull/19640)）。
- **Unix 域套接字**：通过 `OLLAMA_HOST=unix:///abs/path` 配置，权限为 `0600`（[#19467](https://github.com/ollama/ollama/pull/19467)）。
- 决策模型（`/v1/systemone`）支持：MLX 决策模型的长请求处理，超大负载返回 413（[#19408](https://github.com/ollama/ollama/pull/19408)）。

## 4. 性能与优化
- **MLX 并发生成**：通过 `OLLAMA_NUM_PARALLEL` 实现；就绪请求间共享 projection/FFN 计算——改善首 token 延迟（[#19197](https://github.com/ollama/ollama/pull/19197)）。
- **投机解码基准测试**：`draft_num_predict=0` 可按请求禁用 drafting（默认仍为 4）（[#19391](https://github.com/ollama/ollama/pull/19391)）。
- **`ollama load MODEL`** 预加载命令提案，无需交互会话即可预热模型（[#16207](https://github.com/ollama/ollama/pull/16207)）。
- **`max_completion_tokens`** 现已在 OpenAI 兼容的 chat completions 中生效，防止超出客户端预算的过度生成（[#19168](https://github.com/ollama/ollama/pull/19168)）。
- **Unix 套接字**传输降低了本地同机客户端的开销。

## 5. 稳定性与回归问题（按严重程度排序）
1. **MLX KV-cache 内存增长**：密集模型下每请求递增；除重启外无法释放（已关闭）（[#17875](https://github.com/ollama/ollama/issues/17875)）。
2. **Windows RTX 5090 runner 断开**：工具调用期间断开 → HTTP 500 `/tokenize`（开放中）（[#19439](https://github.com/ollama/ollama/issues/19439)）。
3. **Vulkan 回归**：AMD Radeon 780M 在 ≥0.32.10 出现 OOM/设备丢失（已关闭）（[#17748](https://github.com/ollama/ollama/issues/17748)）；Intel Iris Xe 在 0.33.3/0.34.2 上 KV-cache 分配失败，而 0.32.6 正常（已关闭）（[#18531](https://github.com/ollama/ollama/issues/18531)）——两者均已通过 Vulkan mmap 修复解决。
4. **qwen3next 第 2 层缺少 attn_qkv/attn_gate** projection（已关闭）（[#15372](https://github.com/ollama/ollama/issues/15372)）。
5. **gemma4 + `think:false`**：工具响应后返回空回复（开放中）（[#18861](https://github.com/ollama/ollama/issues/18861)）。
6. **Cloud `/api/chat` 400**：约 32 MiB 负载时触发，限制未文档化（开放中）（[#19597](https://github.com/ollama/ollama/issues/19597)）。
7. **Unicode 损坏**：olmo3 工具调用参数中的乱码（Zürich/日本/emoji）——字节拷贝 bug（开放中）（[#18906](https://github.com/ollama/ollama/pull/18906)）。

## 6. 对应用开发者的意义
- **Vulkan iGPU 部署**（迷你主机、Framework Desktop、笔记本）在 v0.40.17 发布后应能看到加载失败率显著下降——值得为受限硬件锁定该版本。
- **依赖 OpenAI 兼容接口的 Agent**：由于 `max_completion_tokens` 现已生效，建议采用以严格控制 token 预算；Windows + RTX 50 系列上的长工具调用会话仍有风险（关注 #19439）。
- **处理大型 base64 图像的 Cloud/API 客户端**必须将单请求保持在约 32 MiB 以下，直至该限制被文档化。
- **非 ASCII 工具参数**（i18n Agent、多语言工具）目前在 olmo3 系列模型上不安全——在 #18906 合并前需进行校验/清洗。
- **本地应用开发者**可为同机客户端迁移至 Unix 套接字，以削减 TCP 开销并收紧访问控制；**基准测试者**可按请求切换 MLX drafting，以量化投机解码收益。

</details>

<details>
<summary><strong>LiteLLM</strong> — <a href="https://github.com/BerriAI/litellm">BerriAI/litellm</a></summary>

# LiteLLM 摘要 — 2026-11-11

## 1. 今日焦点

2026 年 10 月供应链事件的余波仍在持续占据关注焦点（一个恶意 `1.107.0` 版本通过仿冒的 `Berri-AI/litellm` 仓库冒充该项目）：PyPI 和 GHCR 制品已被撤回，项目现在发布带签名的 LTS 版本，并在每个新标签上突出展示 cosign 验证说明。今日的发版列车新增了一个 LTS 补丁（`v1.108.5`），以及面向 `v1.104.9` 和 `v1.103.11` 的维护性回溯。与此同时，开放 issue 队列（24 小时内更新 196 个）主要集中在 Anthropic `/v1/messages` 转换层和令牌桶限流器的正确性缺陷上。

## 2. 发版与破坏性变更

- **v1.108.5（LTS）** — 新的 LTS 补丁。`lts` 镜像标签 `ghcr.io/berriai/litellm:lts` 现在跟踪此版本；固定使用 `lts` 的运维人员应预期正常的补丁级漂移。[Release](https://github.com/BerriAI/litellm/releases/tag/v1.108.5)
- **v1.104.9** 和 **v1.103.11** — 较旧 LTS 系列上的维护标签，携带 cosign 签名密钥通知（密钥引入于提交 [`e82a3c6`](https://github.com/BerriAI/litellm/commit/e82a3c646908ba0a6ee0beddc4310d7a6cac4065)）。如果你在 CI 中验证镜像，请更新你固定的公钥路径。[v1.104.9](https://github.com/BerriAI/litellm/releases/tag/v1.104.9) · [v1.103.11](https://github.com/BerriAI/litellm/releases/tag/v1.103.11)
- **破坏性变更（审查中）：单一交付制品。** PR [#43332](https://github.com/BerriAI/litellm/pull/43332) 将十个 Dockerfile 合并为一个非 root 镜像（uid 65532），使用单一入口点；PR [#43330](https://github.com/BerriAI/litellm/pull/43330) 移除 `litellm-helm`，改为一个可运行单体或组件化的 chart。这些标记为 `!`——任何固定按组件镜像的人应预期部署清单的变动。
- **破坏性变更（审查中）：Databricks 按部署的 OAuth M2M** — PR [#50675](https://github.com/BerriAI/litellm/pull/50675) 改变了 `litellm_params` 部署的凭据解析语义（每个部署一个 client_id/secret，而非全局一对）。
- **安全状态：** `1.107.0` 仍从 PyPI 和 Docker `latest`/`main-latest`/`main-stable` 标签中移除；见 [#47826](https://github.com/BerriAI/litellm/issues/47826)。将任何缓存的 `1.107.0` 制品视为不可信。

## 3. 新模型与硬件支持

- **Azure AI / Mistral：** `azure_ai/mistral-large-4-latest` 从 Azure Models API 添加到模型目录，定价来自 Mistral 的公开页面。[PR #51047](https://github.com/BerriAI/litellm/pull/51047)
- **Azure Responses API 对等性：** chat-completions↔Responses 桥接现在在 `azure/responses/` 模型上重放消息 `phase`（此前仅限 OpenAI），这对多轮 `gpt-5.6` agent 流程很重要。[PR #51059](https://github.com/BerriAI/litellm/pull/51059)
- 今日窗口内没有新的本地后端（CUDA/ROCm/Metal）或量化格式。

## 4. 性能与优化

- **`/v1/messages` 的 Rust 热路径：** PR [#51050](https://github.com/BerriAI/litellm/pull/51050) 在 `LITELLM_RUST=1` 下将简单的 Python 回退检测器移至 Rust 路径——这是一个 7-PR 栈的底部；预期未来几天会有增量检测器卸载。
- **类型检查门禁：** PR [#50711](https://github.com/BerriAI/litellm/pull/50711) 清除了 182 个文件中的 1,142 个 `basedpyright` `Any` 错误（无行为变化）；后续将收紧 CI 上限以防止回归。
- **已知性能隐患（未修复）：** 当 `enable_pre_call_checks=True` 且存在 `max_input_tokens` 部署时，异步路由器预调用路径上的同步 `token_counter` 会阻塞事件循环——[#36174](https://github.com/BerriAI/litellm/issues/36174)（已因陈旧关闭，无关联修复）。

## 5. 稳定性与回归

按影响范围排序：

1. **限流器正确性（高）。** 两个独立的 TPM/RPM 缺陷：使用内存缓存时，`tpm_limit` 永不滚动，并将密钥锁定直到 `/key/update`（[#47340](https://github.com/BerriAI/litellm/issues/47340)）；使用 v3 限流器 + Redis TPM 预留时，令牌计数器在上游 429 下超出其窗口存活，并在低于限制时拒绝（[#49675](https://github.com/BerriAI/litellm/issues/49675)）。另外：一旦虚拟密钥被缓存，按客户的 RPM 限制停止生效（[#39713](https://github.com/BerriAI/litellm/issues/39713)，已关闭）。
2. **支出核算（对 FinOps 为高）。** 当上游报告 `usage.cost: 0` 时，流式补全记录为 $0（[#48937](https://github.com/BerriAI/litellm/issues/48937)，已关闭）；当定价行位于另一提供商下时，`anthropic` 自定义提供商的 `response_cost` 为 null（[#47675](https://github.com/BerriAI/litellm/issues/47675)，已关闭）。预算执行位于这些之下游。
3. **Anthropic `/v1/messages` 转换（对 Claude Code 用户为中高）。** `thinking_delta` 在文本块内流式传输 + 重复的 `message_start` → Anthropic SDK 中内容为空（[#32357](https://github.com/BerriAI/litellm/issues/32357)，已关闭）；`reasoning_effort: "none"` 在默认开启的 Claude 模型上删除思考配置而非禁用它（[#47924](https://github.com/BerriAI/litellm/issues/47924)，已关闭）；SSE keepalive 在上游失败前提交 200，因此错误无法被抛出（[#49711](https://github.com/BerriAI/litellm/issues/49711)，开放）；`chatgpt/` 提供商在 `/v1/messages` 工具轮次中丢弃加密推理（[#49046](https://github.com/BerriAI/litellm/issues/49046)，已关闭）。
4. **工具路由（中）。** JSON 定义的 OpenAI 类提供商在模型不在目录中时静默丢弃 `tools`（[#48145](https://github.com/BerriAI/litellm/issues/48145)，开放——相对于 1.81/1.106 的回归）；MCP 执行失败丢弃 `is_error` 且不到达 `tool_result`（[#49359](https://github.com/BerriAI/litellm/issues/49359)，开放）；Mistral 丢弃命名 `tool_choice`（[#39736](https://github.com/BerriAI/litellm/issues/39736)，已关闭）；Ollama `tool_calls` 解析失败（[#24091](https://github.com/BerriAI/litellm/issues/24091)，开放）。
5. **可观测性正确性（中）。** `gen_ai.response.model` 携带别名而非服务模型（[#51029](https://github.com/BerriAI/litellm/issues/51029)，开放——今日提交）。OTel v2 按团队元数据键落在 [#50813](https://github.com/BerriAI/litellm/pull/50813)。
6. **其他：** Vertex/Gemini 缓存在 `cache_control` 非前缀时重排序 + 丢弃 `system_instruction`（[#47046](https://github.com/BerriAI/litellm/issues/47046)，开放）；`COHERE_API_BASE` 对嵌入被忽略（[#50248](https://github.com/BerriAI/litellm/issues/50248)，开放）；`token_counter` 拒绝图像 `detail: "original"`，跳过上下文窗口检查（[#47327](https://github.com/BerriAI/litellm/issues/47327)，开放）。

进行中的修复 PR：`PATCH /model/{id}/update` 的管理路由 403（[#51060](https://github.com/BerriAI/litellm/pull/51060)）；由别名目标判定的零成本预算豁免（[#51058](https://github.com/BerriAI/litellm/pull/51058)）；按用户支出分解回溯至 LTS（[#51043](https://github.com/BerriAI/litellm/pull/51043)）；MCP 回溯至 `lts/1.108.x`（[#51045](https://github.com/BerriAI/litellm/pull/51045)）；Vertex Gemini 图像/思考/优先令牌计费（[#51017](https://github.com/BerriAI/litellm/pull/51017)）；CI 针对失败单元运行的 tzdata 修复（[#51057](https://github.com/BerriAI/litellm/pull/51057)）。

## 6. 对应用开发者的意义

- **固定并验证。** 如果你消费镜像，请迁移到 `:lts`（现为 `1.108.5`），并使用 `e82a3c6` 密钥将 `cosign verify` 接入你的部署门禁。审计任何仍解析到 `1.107.0` 的层缓存或锁文件。
- **暂不要信任流式支出数字。** 如果你从 `LiteLLM_SpendLogs` 计量成本，请与提供商发票交叉核对——在 `usage.cost: 0` 下流式流量可能被记录为 $0（[#48937](https://github.com/BerriAI/litellm/issues/48937)）。基于这些日志构建的预算执行将少计。
- **如果你在没有 Redis 的内存缓存中运行，TPM/RPM 限制作为硬配额并不可靠**：密钥可能卡住直到 `/key/update`（[#47340](https://github.com/BerriAI/litellm/issues/47340)）。对于生产多租户网关，使用 Redis 支持的限流，并监控上游节流下的过早 429（[#49675](https://github.com/BerriAI/litellm/issues/49675)）。
- **Claude Code / Anthropic-SDK 客户端：** 若干 `/v1/messages` 适配器缺陷（推理编码、`reasoning_effort: "none"`、keepalive 后错误）仍开放或仅近期关闭——在升级前针对你固定的版本测试 agentic 工具循环和 SSE 失败路径。
- **自定义提供商：** 如果你通过 JSON `provider` 配置而非目录注册使用工具的模型，请在升级超过 1.81 后验证 `tools` 确实到达后端（[#48145](https://github.com/BerriAI/litellm/issues/48145)）——这是一种静默失败模式。
- **部署清单：** 如果你使用 Helm chart 或按组件镜像，现在就开始跟踪 [#43330](https://github.com/BerriAI/litellm/pull/43330)/[#43332](https://github.com/BerriAI/litellm/pull/43332)；合并为一个 chart 和一个非 root 镜像对覆盖和 init 容器是破坏性变更。

</details>

<details>
<summary><strong>Unsloth</strong> — <a href="https://github.com/unslothai/unsloth">unslothai/unsloth</a></summary>

# Unsloth 摘要 — 2026-11-11

## 今日亮点
过去 24 小时内没有新版本发布，但 Studio 界面进展迅速：大量 PR 已合入或刷新，涉及图像编辑（外部 `/images/edits` 支持、MiniMax-H3-Image）、vLLM/SGLang token 预算修复，以及反向代理/Cloudflare 隧道支持。新的 UD 3.1 GGUF 上 Qwen 混合模型 MTP 草稿接受率出现回归，任何运行投机解码的用户都值得关注。与此同时，bug 队列主要由桌面/Studio UX 问题（玻璃主题、模型大小、存储错误）占据，而非训练关键故障。

## 发布与破坏性变更
过去 24 小时内无。

- 注意（行为变更，已合入/关闭）：PR #17206 将 Studio 的 PDF 技术栈（PyMuPDF、pymupdf4llm）改为按需安装——首次使用 PDF/电子书功能时将触发依赖安装，而非在安装时即已存在。[PR #17206](https://github.com/unslothai/unsloth/pull/17206)

## 新模型与硬件支持
- **MiniMax-H3-Image（文生图 + 参考编辑，1–16 张参考图）**：新的 Studio Images 流水线，在可用时使用 diffusers transformer/VAE 类。[PR #17453](https://github.com/unslothai/unsloth/pull/17453)
- **通过 OpenAI 兼容的 `/images/edits` 使用外部图像编辑模型**：已连接的图像模型现在可用于编辑，而不仅是生成（报告使用 llama-swap → stable-diffusion.cpp / FLUX.2-klein-4B-GGUF）。[PR #17450](https://github.com/unslothai/unsloth/pull/17450) · [PR #17448](https://github.com/unslothai/unsloth/pull/17448)
- **桌面端反向代理/自定义域名支持**：白名单内的反向代理（例如带自定义域名的 Cloudflare Named Tunnel）现在可以服务打包的 Web UI；此前对 `X-Forwarded-Host` 返回 404。[PR #17435](https://github.com/unslothai/unsloth/pull/17435)（修复 [#17420](https://github.com/unslothai/unsloth/issues/17420)）
- **ROCm**：gfx1031（RX 6700 XT）在启动时被识别为 RDNA 2，但在 PyTorch 首次 GPU 操作时段错误——已报告并关闭；在修复确认之前，将 RDNA 2 视为 ROCm Docker 路径未经验证。[#17147](https://github.com/unslothai/unsloth/issues/17147)
- **Intel Arc 140V**：图像生成模型加载失败（已关闭）。[#17137](https://github.com/unslothai/unsloth/issues/17137)

## 性能与优化
- **vLLM/SGLang 上的上下文窗口预算**：工具请求和 `/v1/completions` 现在会降低 `max_tokens` 以适配窗口，而非在 prompt 可容纳但 prompt+max_tokens 不可容纳时返回 `400 context_length_exceeded`。[PR #17426](https://github.com/unslothai/unsloth/pull/17426)
- **投机解码回归（重要）**：Qwen3.8-Flash-Next UD 3.1 GGUF 搭配现有 MTP 草稿头，在 Ryzen AI Max+ 395 上生产温度下草稿接受率从约 74% 降至 56–66%。如果你依赖 MTP 降低延迟，请固定使用旧量化版本，直到匹配 3.1 的草稿头发布。[#17402](https://github.com/unslothai/unsloth/issues/17402)
- **Studio 可观测性**：通过从流式 usage chunk 合成的 `timings`，将 tok/s 徽章扩展到 API 提供商。[PR #16506](https://github.com/unslothai/unsloth/pull/16506)
- **自定义 OpenAI 兼容连接的真实 token 用量**：按连接可选启用 `stream_options`，实现准确的上下文条（此前仅估算）。[PR #16505](https://github.com/unslothai/unsloth/pull/16505)
- **GGUF 权重放置读数**：已加载 GGUF 的放置信息始终可见（此前隐藏在默认关闭的开关后）。[PR #17449](https://github.com/unslothai/unsloth/pull/17449)
- **按模型 `max_tokens` 默认值**：功能请求，从公布的上下文长度推断默认值，而非统一 32K。[#17444](https://github.com/unslothai/unsloth/issues/17444)

## 稳定性与回归
按严重程度排序：

1. **gfx1031 上的 ROCm 段错误** — 启动检查通过，PyTorch 在首次 GPU 操作时崩溃。已关闭，但部署前请在你的硬件上验证。[#17147](https://github.com/unslothai/unsloth/issues/17147)
2. **Qwen 混合 + MCP 工具循环**：在大型 MCP 工具目录和 64K 上下文下，工具使用后跨轮次重复完整 prompt 处理（llama-server，延迟 schema 路径）。开放中。[#17015](https://github.com/unslothai/unsloth/issues/17015)
3. **流水线并行 MoE 路由器辅助损失发散（CPU 后端）**：在 `UNSLOTH_FORCE_CPU=1` 且梯度累积 > 1 时，损失与单进程发散；仓库对等测试可复现。开放中。[#17054](https://github.com/unslothai/unsloth/issues/17054)
4. **Studio 网页抓取 HTML→Markdown 损坏**：裸 `&` 会多出 `;`，转义代码逃逸其块，畸形 `&#` 可能留下原始 HTML——如果你将抓取页面输入 prompt 则相关。已关闭。[#17199](https://github.com/unslothai/unsloth/issues/17199)
5. **MLX 视觉对话在附加图像后停止压缩**；对话最终以“Message too long”失败。修复 PR 开放中。[PR #17424](https://github.com/unslothai/unsloth/pull/17424)
6. **外部连接图像编辑被禁用**，针对支持此功能的服务器。修复 PR 开放中。[PR #17448](https://github.com/unslothai/unsloth/pull/17448) · [PR #17450](https://github.com/unslothai/unsloth/pull/17450)
7. **LAN 绑定到网络桥接 IP 失败**（桌面端）。已关闭。[#17017](https://github.com/unslothai/unsloth/issues/17017)
8. **桌面应用升级路径错误** — 已关闭，但需留意复发。[#17375](https://github.com/unslothai/unsloth/issues/17375)
9. **自定义连接视频上传被阻止**（OpenAI 兼容连接）。已关闭。[#17295](https://github.com/unslothai/unsloth/issues/17295)
10. **较轻的 Studio UX bug（已关闭）**：Hub 与选择器之间的模型大小不匹配 [#17074](https://github.com/unslothai/unsloth/issues/17074)；重复的玻璃材质设置 [#17162](https://github.com/unslothai/unsloth/issues/17162)；玻璃主题下 Hub 界面错乱 [#17417](https://github.com/unslothai/unsloth/issues/17417)；`Regenerate title` 复制用户文本 [#17332](https://github.com/unslothai/unsloth/issues/17332)；背景图存储错误 [#17365](https://github.com/unslothai/unsloth/issues/17365)；`nul` 文件写入 CWD [#16991](https://github.com/unslothai/unsloth/issues/16991)。
11. **导出/校准路径的正确性**：长不间断文本（CJK/泰文/长 token）的 PDF 导出被截断——修复 PR 开放中 [PR #17425](https://github.com/unslothai/unsloth/pull/17425)；通过 `splitlines()` 读取扩散 caption JSONL 在 U+2028/U+2029/U+0085 上中断并静默丢弃行——修复 PR 开放中 [PR #17436](https://github.com/unslothai/unsloth/pull/17436)；`push_to_hub_gguf(tags=...)` 从未写入 tags（错误的 `HfApi` 调用，异常被吞）——修复 PR 开放中 [PR #17438](https://github.com/unslothai/unsloth/pull/17438)。

## 对应用开发者的意义
- **如果你在 Qwen3.8-Flash-Next 上使用 MTP/投机解码**：将 GGUF 量化版本与草稿头一起固定。将 UD 3.1 权重与 3.1 之前的草稿头混用会显著降低接受率（约 74% → 56–66%），进而影响延迟。关注 [#17402](https://github.com/unslothai/unsloth/issues/17402)。
- **如果你通过 vLLM/SGLang 提供带工具的服务**：升级以获得 `max_tokens` 适配修复 [PR #17426](https://github.com/unslothai/unsloth/pull/17426)；当前工具调用上的 `400 context_length_exceeded` 失败是真正的 agent 循环破坏者。
- **如果你将 Unsloth Desktop 部署在 Cloudflare 或反向代理之后**：[PR #17435](https://github.com/unslothai/unsloth/pull/17435) 解锁自定义域名 Named Tunnel；在合入之前，`X-Forwarded-Host` 请求预期返回 404。
- **如果你基于自定义 OpenAI 兼容网关构建**：启用新的按连接 usage 流式传输选项 [PR #16505](https://github.com/unslothai/unsloth/pull/16505) 以获得准确的上下文/token 计量——但仅限接受 `stream_options` 的网关。
- **如果你使用 ROCm RDNA 2 或 Intel Arc**：将其视为 Docker/Studio 推理路径未经验证；gfx1031 在首次操作时段错误，Arc 140V 图像模型加载失败。
- **如果你导出对话或整理扩散数据集**：PDF 换行 [PR #17425](https://github.com/unslothai/unsloth/pull/17425) 和 caption 解析 [PR #17436](https://github.com/unslothai/unsloth/pull/17436) 修复可防止静默数据丢失——如果你批量执行这些操作，值得跟踪。

</details>

---
*本日报由 [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar) 自动生成。*