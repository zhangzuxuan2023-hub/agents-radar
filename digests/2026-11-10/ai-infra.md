# AI 基础设施日报 2026-11-10

> 生成时间: 2026-11-09 23:19 UTC | 覆盖项目: 6 个

- [vLLM](https://github.com/vllm-project/vllm)
- [SGLang](https://github.com/sgl-project/sglang)
- [llama.cpp](https://github.com/ggml-org/llama.cpp)
- [Ollama](https://github.com/ollama/ollama)
- [LiteLLM](https://github.com/BerriAI/litellm)
- [Unsloth](https://github.com/unslothai/unsloth)

---

## 横向对比

# 跨项目对比报告 — AI 基础设施生态
**摘要日期：2026-11-10**

---

## 1. 生态概览

今日的动态印证了一个双层基础设施格局：**服务引擎（vLLM、SGLang）正处于硬件适配的堑壕战之中**——SM120/GB10 消费级 Blackwell 与 MI355X/MI450 如今消耗了大部分 issue 和 PR 带宽，且正确性回归（静默空补全、内核输出静默损坏）的数量已超过功能开发。**本地/边缘层（llama.cpp、Ollama、Unsloth）** 迭代速度很快，但正沿后端（Metal、Vulkan、SYCL、ROCm、OpenCL、XDNA）走向碎片化，可复现性与 KV 缓存正确性成为反复出现的薄弱环节。**网关层（LiteLLM）** 正围绕计量正确性进行整合——一批已关闭的预算/限流绕过缺陷，加上原生 Rust 后端的 `/v1/messages` 路径，表明计费准确性已成为主要竞争轴。在所有层级中，**投机解码正成熟为默认的吞吐量杠杆**，同时也在产生最大的一簇正确性回归（EAGLE/MTP/DFlash/DFlash2/DSpine 分歧、TP>1 时接受率崩塌）。值得注意的是：过去 24 小时内 vLLM、SGLang 或 Unsloth 均无发布——今日的工作是集成与缺陷修复，而非发版。

---

## 2. 活跃度对比

| 项目 | 层级 | 涉及 Issue 数 | 涉及 PR 数 | 发布状态（24 小时） | 主导主题 |
|---|---|---|---|---|---|
| **vLLM** | 服务引擎 | ~14（6 个 issue + 8 个回归项） | ~10 | **无** | Blackwell（SM120）就绪度缺口；Marlin→Humming 迁移；投机解码正确性 |
| **SGLang** | 服务引擎 | ~13 | ~13 | **无**（标记 v0.5.22→v0.5.23 回归） | CI 不稳定；SM120 覆盖；AMD MI355X/MI450 内核工作 |
| **llama.cpp** | 本地运行时 | ~18 | ~18 | **有 — 18 个构建（b12219–b12236）** | 后端正确性（Metal FA 越界）；音频重采样；JSON-schema 服务端修复 |
| **Ollama** | 本地运行时 | ~10 | ~7 | **有 — v0.40.16-rc0** | OpenAI 兼容面；VRAM 核算/GPU 拆分修复（#19632） |
| **LiteLLM** | 网关 | ~20 | ~10 | **有 — v1.108.4**（cosign 签名镜像） | 成本/预算核算正确性；原生 `/v1/messages` Rust 路径 |
| **Unsloth** | 微调 + 本地服务 | ~12 | ~10 | **无**（typer 0.29 Windows 故障已锁定版本） | 解码 CUDA 图（T4 上 2–8×）；Studio OpenAI 兼容网关缺陷 |

**发布节奏解读：** llama.cpp 是唯一持续发版的项目（每天 18 个构建）。LiteLLM 以约每周一次的节奏发版，并已预告 2.0.0 破坏性变更。vLLM/SGLang/Unsloth 正处于功能稳定化的间歇期——这一模式与发布前加固周期一致，也是对锁定“最新版”的消费者而言风险最高的窗口。

---

## 3. 模型支持竞赛

| 模型 / 架构 | vLLM | SGLang | llama.cpp | Ollama | LiteLLM | Unsloth |
|---|---|---|---|---|---|---|
| **DeepSeek-V4.1-Flash** | ⚠️ SM120 CUDA 图损坏（#56892） | ⚠️ SM90 解码回归 v0.5.23（#49336） | — | — | — | — |
| **Kimi K3.1** | ✅ PR #66532（NVIDIA 解码 + ROCm TP8） | ✅ MI355X 上 K3 的 AITER MoE 排序（#43126） | ✅ 视觉（#31980） | — | — | — |
| **Qwen3.8 / 3.5 / 3.6** | ⚠️ Flash-Next 长上下文缺陷（#62776）；drafter 阻塞项（#56088） | ⚠️ GDN prefill PR（#38806、#48398） | — | ✅ 并行请求解锁 PR #17144（3.5/MoE） | ✅ 自定义部署的 tokenizer 修复（#50734） | ✅ Qwen3.5/3.6 + GDN 混合解码图（#17324、#17329） |
| **Gemma 4** | — | — | ✅ 音频流式（余弦 0.993–0.997 对比非流式） | — | — | ✅ 解码图（#17329） |
| **MiniMax M3 / H3** | ✅ MSA 索引器 CP（#61929） | ✅ H3 的 FP8 视频 VAE（#45180） | — | — | — | — |
| **Claude 5.x / Fable 5.2** | — | — | — | — | ✅ Vertex + Bedrock Converse 支持（#50742）；prefill-400 修复 PR #50701 | — |
| **GLM-5.x / 5.3-Flash** | — | ⚠️ SM120 fa4 崩溃（#42012） | — | — | ⚠️ `glm-5.2[1m]` 未知模型（#32218） | ✅ 解码图（GLM-4 系列） |
| **DFlash / DSpine drafters** | ⚠️ TP>1 时崩塌（#66387） | ✅ DSpark KV 写入修复（#49386） | ✅ DFLASH2（#31904）、腾讯 DSpine（#31941） | — | — | — |
| **Nemotron-H / 3.5 ASR** | ✅ 弹性宽度（#66665）；ASR 请求开放中 | — | — | — | — | — |
| **Granite** | — | — | ✅ NVFP4 缩放（b12223） | — | — | ✅ 解码图 |

**谁领先：** **vLLM** 在广度上领先（Kimi K3.1、MiniMax M3、Nemotron-H、DeepSeek-V4.1），但在新硬件上承载了最多的模型专属回归。**llama.cpp** 在长尾架构支持上领先（Gemma 4 音频、DFLASH2/DSpine、Granite NVFP4），并且是新 drafter 格式事实上的参考实现。**Unsloth** 在 *Qwen3.5/3.6 与 Gemma 4 推理加速* 上意外位居第二，正从训练侧向服务引擎的领地渗透。**LiteLLM** 是唯一在添加云厂商模型对齐（Fable 5.2 于 Vertex/Bedrock）的项目——它的竞赛发生在 API 形态层，而非内核层。**Ollama** 的新模型覆盖面最窄，且仍受制于 Qwen3.5 MoE 并行（`numParallel=1`）。

---

## 4. 性能前沿

| 优化领域 | vLLM | SGLang | llama.cpp | Ollama | LiteLLM | Unsloth |
|---|---|---|---|---|---|---|
| **KV 缓存** | FlashInfer 工作区收缩 → **KV 容量 +21%**（#63399）；KV 卸载 DMA 阈值（#57555）；饱和准入下 TPOT 膨胀（#64460） | Supernode L1+L2 KV 共享路线图（#43098）；prompt 缓存恢复正确性 | **可增长 KV 可选项**（`UNSLOTH_GROWABLE_KV=1`）避免 StaticCache 过度分配 | ⚠️ Vulkan prompt 缓存**第 2 次请求约 30× 减速**（#19201，已关闭） | 缓存读取计费修复（#37823） | 可增长 KV 缓存（可选项） |
| **批处理 / 调度** | HiSparse 协调器批处理消除数十毫秒停顿（#65607）；准入控制 | PP 调度器陈旧 DSpark 修复（#49386）；Agentic 缓存调度路线图 | — | sched.go 为 Qwen3.5-MoE 解锁 `numParallel=1`（#17144） | 高吞吐下 Redis 计数器无效写入（#31880） | — |
| **量化** | Marlin→Humming 迁移（#65560）；NVFP4 MoE 静默失败（#51525）；AutoAWQ 在 RDNA 上慢 2.5×（#63455） | NPU 上的 MXFP4 注意力（#43367） | NVFP4 Granite 缩放（b12223）；OpenCL int8 融合 KQ（#30249） | — | — | — |
| **分布式服务** | TP-rank 自动调优同步标志（#66526）；Kimi K3.1 ROCm TP8 | MI450 的 Ring all-reduce + `2shot_push`（#49377）；DCP 索引器行拆分（#47309） | — | 多 GPU 拆分核算修复（#19632） | 放弃流的原生 `/v1/messages` 计费（#50425） | — |
| **内核** | Triton 自动调优确定性；Eager 注意力视图重新推导（#64572） | 单遍 Triton/Gluon DSA top-k 在 MI355X 上 **1.5–1.9×**（#47872）；AITER 汇编稀疏 MLA | Metal FA 越界写入已修补（b12221）；x86 BF16 点积（#31878）；ARM NEON IQ4_NL 重打包（#30165） | — | — | — |
| **解码加速** | 目标 logits 上的投机解码 top-k/top-p（#66534） | EAGLE 逐 draft 位置重复惩罚修复（#49404） | Adreno Vulkan 标量 FA 上限（#30829）；MMVQ 截断表（#28090） | — | — | **T4 上 CUDA 图 2–8×**（#16587），模型专属扩展 |

**集中度解读：** 可见三场不同的战役。**(a) AMD 已成为一等性能目标**——SGLang 拥有最深的 MI355X/MI450 内核流水线（DSA top-k、ring all-reduce、AITER 汇编 MLA）；vLLM 正以 ROCm AITER MLA 追赶（但遭遇了一个已关闭的严重静默损坏缺陷 #66141）。**(b) KV 缓存容量是普遍瓶颈**——vLLM 通过工作区调优回收，Unsloth 通过可增长分配，SGLang 通过 supernode 共享；Ollama 的 Vulkan prompt 缓存 30× 回归是警示性的反例。**(c) 解码图正从训练框架向外扩散**——Unsloth 默认开启的 CUDA 图在 T4 上带来 2–8×，是本摘要集中最大的头条吞吐量声明，且它直接与服务引擎争夺“小模型、高吞吐”用例。

---

## 5. 层级定位

| 层级 | 项目 | 核心功能 | 本周战略信号 |
|---|---|---|---|
| **服务引擎（数据中心）** | **vLLM、SGLang** | 高吞吐、多 GPU、PagedAttention 级服务；分离式 prefill/decode（PD）、TP/PP/DP | 两者均**受制于硬件适配**：SM120/GB10 与 MI 侧缺口占主导。功能迭代速度是后端 bring-up 的下游。差异化正转向*正确性保障*（确定性标志、消除静默失败），而非原始吞吐量。 |
| **本地 / 边缘运行时** | **llama.cpp、Ollama** | 单节点/消费级服务、GGUF 量化、多后端可移植性（Metal/CUDA/Vulkan/SYCL/OpenCL/NPU） | llama.cpp 是**后端可移植性领先者**但正碎片化（每天 18 个构建横跨 10 个后端）；Ollama 是**整合者/分发层**，押注 OpenAI 兼容与 Docker 捆绑的 MLX/Vulkan。两者均面临 KV 缓存正确性债务（#11970、#30961）。 |
| **网关 / 控制平面** | **LiteLLM** | 路由、限流、预算、成本核算、提供商归一化、PII 护栏 | 网关正成为**计量与策略执行点**——本周的 issue 簇（5 个高危预算/限流绕过，均已关闭）证明该层仍在加固。原生 Rust `/v1/messages`（7 个 PR 的栈）是热路径的**基础设施语言迁移**。 |
| **微调 → 服务桥梁** | **Unsloth** | SFT/RL 微调，随后通过 `FastModel.generate()` 与 Studio 进行本地服务 | Unsloth 正**纵向向上整合**：解码 CUDA 图、可增长 KV、OpenAI 兼容的 Studio 网关。对于 Qwen/Llama/Gemma 级工作负载，它如今已是可信的单机服务引擎替代方案。训练侧正确性（CPU 评估损失 #17291）是当前的缺口。 |

**采购视角：** vLLM/SGLang = 横向扩展的多租户服务；llama.cpp = 最大硬件可移植性与模型广度；Ollama = 开发者体验与分发；LiteLLM = 多提供商治理与成本控制；Unsloth = 一站式微调后服务。这些**互补而非可替代**——有意思的重叠在于 Unsloth 对本地服务引擎，以及 LiteLLM 对原生提供商 SDK。

---

## 6. 趋势信号

**1. 静默正确性失败是新的可用性风险。** 本周两个 CRITICAL 级 issue 返回*HTTP 200 但内容错误*：vLLM 的 NVFP4 MoE 在 SM120 上空补全（#51525，`content: null` + `finish_reason: length`）以及已关闭但待验证的 ROCm AITER MLA 不相关输出（#66141）。结合 llama.cpp 的投机解码 temp-0 分歧（#31472）与 SGLang 可破坏的 prefill CUDA 图分歧（#49133），信息很明确：**输出校验必须成为服务栈的一部分，而不是应用的假设。**

**2. 投机解码既是吞吐量默认项，也是正确性前沿。** 今天每个项目都触及了它——vLLM（EAGLE/MTP 前缀缓存块丢弃，30–40% 批损失；DFlash TP>1 崩塌）、SGLang（EAGLE 重复惩罚偏斜修复）、llama.cpp（DFLASH2/DSpine、temp-0 分歧）、Unsloth（解码图）。运行对可复现性敏感的评估或确定性 agent 的开发者应**禁用投机解码或锁定版本，直到各项目的修复矩阵落地。**

**3. AMD 不再是次要目标——它是共同一等公民。** SGLang 有密集的 MI355X/MI450 PR 流；vLLM 有 MI355X 性能追踪器与“MonoKernel”提案（#60904）；llama.cpp 与 Ollama 承载着持续的 ROCm/Vulkan 问题（RDNA AWQ 慢 2.5×、gfx1151 NaN、VRAM 误报）。**集群标准化应将 ROCm 视为一个独立的、重度依赖版本锁定的环境**——而非 CUDA 路径的直接替代。

**4. 消费级 Blackwell（SM120/GB10）在两个服务引擎上开箱即属损坏层级。** vLLM（CUDA 图失败、静默空补全）与 SGLang（CUDA 13 wheel 安装失败；因移除 DSV4.1 DeepGEMM 门控导致约 8× prefill 回归，#49173）独立证实 SM120 当前**对量化 MoE 或长上下文尚未生产就绪**。Blackwell *数据中心*（SM100/SM90）没问题；消费级 SKU 才是缺口。

**5. 网关正成为受审计的控制平面。** LiteLLM 的五个已关闭高危预算/限流绕过（预留路径之外的项目预算、陈旧重置将支出清零、已删除团队密钥的认证缓存残留、按团队模型限制减半）加上预先宣布的 **2.0.0 空 `models` 语义变更**，意味着 agent/应用开发者应：升级后审计限额，在链式代理拓扑中对照提供商账单核对流式用量（#36114），并在弃用窗口关闭前**显式枚举允许的模型**。

**6. OpenAI 兼容是入场券，结构化输出才是创新。** llama.cpp 的顶层 `json_schema` 修复（b12229）、Ollama 的 `/v1/models` 空数组变更与截断转发，以及 Unsloth Studio 的自定义端点网关，都汇聚于 OpenAI 线格式兼容。下一步是**模式强制的结构化生成**——llama.cpp 的 token 感知聊天解析（#29307/#31978）正将 `<think>`/工具调用解析从客户端字符串匹配迁移到服务端发出的字段。**为 agent 框架规划这一迁移。**

**7. 下周关注：**
- **vLLM：** Marlin→Humming 迁移是否能以同等性能落地；DeepSeek-V4.1 的 SM120 CUDA 图修复。
- **SGLang：** v0.5.22→v0.5.23 解码回归的解决（对 DeepSeek-V4.1 交互式 agent 至关重要）；MI450 all-reduce 落地。
- **llama.cpp：** KV 缓存截断（#11970）与 prompt 缓存恢复（#30961）——两者均为长期 HIGH 且影响多轮正确性。
- **Ollama：** 在 v0.40.16-rc0 与 #19632 VRAM 核算修复之后趋于稳定——这是本周期升级的最强理由。
- **LiteLLM：** 2.0.0 迁移工具；面向 Anthropic 协议客户端的原生 `/v1/messages` 栈完成。
- **Unsloth：** 带确定性验证的解码图 GA；可增长 KV 发布；Studio 是成熟为可信的 agent 路由网关，还是仍停留在开发工具。

---

*本报告基于 2026-11-10 六个项目的摘要快照生成。所有 issue/PR 编号引用上文各项目摘要。*

---

## 各项目详细报告

<details>
<summary><strong>vLLM</strong> — <a href="https://github.com/vllm-project/vllm">vllm-project/vllm</a></summary>

# vLLM 摘要 — 2026-11-10

## 1. 今日亮点

过去 24 小时内没有新版本发布，但 issue 活动主要由 **Blackwell（SM120）就绪度缺口**主导：在 RTX PRO 6000 上使用 DeepSeek-V4.1-Flash 的用户报告 CUDA graph 捕获失败，被迫使用 `--enforce-eager`，导致解码吞吐严重损失（#56892）。在 ROCm 方面，AMD 正在积极推动 Qwen3.8 模型系列的 MI355X 性能跟踪器（#57149、#59575），并提出针对专用解码的正式 “MonoKernel” 标准（#60904）。一项值得注意的结构性转变正在发生：NVIDIA 提议跨架构**用 Humming 替换 Marlin**（#65560），而 Kimi K3.1 模型拆分已作为 PR 落地，覆盖 NVIDIA 专用解码和 ROCm TP8（#66532）。

## 2. 版本发布与破坏性变更

*今日无。* 过去 24 小时内没有发布新版本或 API 变更。

## 3. 新模型与硬件支持

- **[PR #66532] Kimi K3.1：NVIDIA 专用解码 + AMD 模型更新，ROCm TP8 支持** — 是更大变更的 4/6 拆分；引入 Kimi K3.1 模型文件及在 ROCm 上以 TP8 服务所需的最小外部改动。[链接](https://github.com/vllm-project/vllm/pull/66532)
- **[PR #61929] MiniMax M3/3.1 indexer 上下文并行** — 面向 MSA 后端的可选 P128 indexer CP，适用于单节点 NVLink TP2/TP4，支持 FP8 index-K 分片和 FlashInfer CAKE 融合打分。[链接](https://github.com/vllm-project/vllm/pull/61929)
- **[PR #66322] ROCm AITER MLA 稀疏：PCP/DCP 支持**，面向 AMD 上的 DeepSeek V3.2。[链接](https://github.com/vllm-project/vllm/pull/66322)
- **[PR #66665] Nemotron-H 弹性请求级宽度**（实验性）— 从单份权重分配中同时服务完整宽度和嵌套宽度检查点。[链接](https://github.com/vllm-project/vllm/pull/66665)
- **[Issue #47455] Nemotron 3.5 ASR 流式（0.6B）** 功能请求仍处于开放状态。[链接](https://github.com/vllm-project/vllm/issues/47455)
- **[Issue #56088] Qwen3.8-Flash-Next 无法服务 DeepSpec DFlash/DSpark drafter** — 已识别出五个阻塞项，补丁已可用。[链接](https://github.com/vllm-project/vllm/issues/56088)

## 4. 性能与优化

- **[Issue #53670] EAGLE/MTP 前缀缓存末块丢弃**导致混合 Qwen3.8 GDN 布局下每次命中重算 1,648 token → 前缀复用投机负载下**批吞吐损失约 30–40%**。[链接](https://github.com/vllm-project/vllm/issues/53670)
- **[Issue #63399] FlashInfer 工作区大小调整**：将启动工作区从 394 MiB 缩减至 32 MiB，恢复了 **+21.27% 的 KV 缓存容量**（Qwen2.5-1.5B，RTX 4060 Laptop）。[链接](https://github.com/vllm-project/vllm/issues/63399)
- **[PR #66534] 投机解码 top-k/top-p 截断在验证期间的目标 logits 加载时应用** — 叠加在 #65102 之上。[链接](https://github.com/vllm-project/vllm/pull/66534)
- **[PR #66526] `VLLM_TRITON_SYNC_AUTOTUNE_TP`**：强制所有 TP rank 使用 rank-0 的 Triton autotune 计时，防止各 rank 内核选择出现分歧。[链接](https://github.com/vllm-project/vllm/pull/66526)
- **[PR #66631 / #65607] HiSparse 协调器簿记批处理** — 消除大常驻上下文下每次 `schedule()` 数十毫秒的 Python 停顿。[链接](https://github.com/vllm-project/vllm/pull/65607)
- **[Issue #64460] KV 池饱和时的准入重试使解码 TPOT 膨胀约 2.3 倍**（V1 调度器，平台无关；在 Ascend 上观测到）。[链接](https://github.com/vllm-project/vllm/issues/64460)
- **[Issue #64572] Eager 注意力后端在每次调用时重新推导 K/V 缓存视图** — 纯属额外开销，修复进行中。[链接](https://github.com/vllm-project/vllm/issues/64572)
- **[Issue #57555] KV 卸载 DMA/Triton 阈值**在 H100 上调优，对其他设备不适用；需要按设备进行性能分析。[链接](https://github.com/vllm-project/vllm/issues/57555)

## 5. 稳定性与回归

按严重程度排序：

1. **严重 — [Issue #51525] SM120 上使用 `--moe-backend cutlass` 的 NVFP4 MoE 静默返回空补全**（`content: null`，`finish_reason: length`），无异常。[链接](https://github.com/vllm-project/vllm/issues/51525)
2. **严重 — [Issue #66141] [已关闭] ROCm AITER MLA 解码内核静默返回不相关输出**（gqa_ratio=16，fp8 Q+KV，decode_qlen 3–4）。已关闭 — 请验证修复已落地。[链接](https://github.com/vllm-project/vllm/issues/66141)
3. **高 — [Issue #62776] GB10 上的 Qwen3.8-Flash-Next：超过约 2048 token 的提示内容对模型不可见**（indexer 预算）。[链接](https://github.com/vllm-project/vllm/issues/62776)
4. **高 — [Issue #56892] SM120 上的 DeepSeek-V4.1-Flash：CUDA graph 不可用**，`--enforce-eager` 下解码吞吐极低。[链接](https://github.com/vllm-project/vllm/issues/56892)
5. **高 — [Issue #66387] TP > 1 时 DFlash 接受率崩溃**（MiMo-V2.6-Flash-MOPD；TP1 正常）。[链接](https://github.com/vllm-project/vllm/issues/66387)
6. **中 — [Issue #63593] 强制拒绝的 MTP 投机解码与仅目标解码在 Qwen3.8-27B GDN 混合模型上于 K≥5、n≥8、L≥192 时出现分歧**。[链接](https://github.com/vllm-project/vllm/issues/63593)
7. **中 — [Issue #52525] Marlin MoE 输出依赖专家内 token 顺序** — 违反批不变性。[链接](https://github.com/vllm-project/vllm/issues/52525)
8. **中 — [Issue #63455] ROCm：AutoAWQ 检查点从未命中 RDNA W4A16 内核**；在 gfx1151 上 AWQ 解码比 BF16 慢 2.5 倍。[链接](https://github.com/vllm-project/vllm/issues/63455)
9. **低 — [PR #66445] Humming 组大小未对 K 块分块**现被拒绝（此前会静默产生错误输出）。[链接](https://github.com/vllm-project/vllm/pull/66445)
10. **低 — [Issue #59988] `--api-server-count > 1`：Prometheus 指标在首个请求前为空**。[链接](https://github.com/vllm-project/vllm/issues/59988)
11. **低 — [PR #66489] ChatCompletionRequest 中的空 `messages` 列表**现在在验证阶段被拒绝（此前在生成后返回 HTTP 500）。[链接](https://github.com/vllm-project/vllm/pull/66489)

## 6. 对应用开发者的意义

- **按硬件锁定你的技术栈。** SM120/Blackwell 消费级用户（RTX 50xx、RTX PRO 6000）在量化 MoE 和 DeepSeek-V4.1 上仍面临静默空补全和 CUDA graph 失败。请验证输出内容，而不仅仅是 HTTP 200 — 这些失败返回 `finish_reason: length` 和 null 内容，而非错误。
- **新 Qwen3.8-Flash-Next 部署上的长上下文需要显式截断测试** — 目前 GB10 上超过约 2K token 的内容可能被静默忽略（#62776）。
- **前缀缓存 + 投机解码在混合注意力模型上仍是吞吐陷阱**（#53670、#51771）：前缀复用负载下预计批吞吐损失 30–40%；在 #52244 落地前，请禁用投机解码或锁定到不含该末块丢弃回归的版本。
- **边缘场景下准入控制很重要。** KV 饱和的服务器通过重试风暴使解码 TPOT 膨胀约 2.3 倍（#64460）— 请在前端使用队列感知的负载均衡，而非简单的轮询。
- **小型运维收益可期：** FlashInfer 工作区调优可在消费级 GPU 上恢复约 20% 的 KV 容量；新的 `VLLM_TRITON_SYNC_AUTOTUNE_TP` 标志可稳定 TP rank 内核选择，适合对可复现性敏感的工作负载。
- **RL/微调流水线：** 关注 Direct Weight Reload RFC（#54477）和 Load Plan API（#64418）— 两者都旨在消除权重更新中与重载相关的静默陈旧问题。

</details>

<details>
<summary><strong>SGLang</strong> — <a href="https://github.com/sgl-project/sglang">sgl-project/sglang</a></summary>

# SGLang 摘要 — 2026-11-10

## 1. 今日亮点

过去 24 小时内没有新版本发布，但 issue 活动主要由 CI/runner 不稳定以及一组 SM120（消费级 Blackwell / GB10）覆盖缺口主导，这些缺口正在导致 prefill 和 attention 后端回归。在性能方面，AMD 贡献者继续为 MI355X/MI450 落地 DSA/MLA 和 all-reduce kernel 工作，同时有报告称 DeepSeek-V4.1-Flash 上出现新的 SM90 decode 回归（v0.5.22 → v0.5.23）。

## 2. 版本发布与破坏性变更

过去 24 小时内无。注意 issue #49336 指出 v0.5.22（`baa9befa38`）与 v0.5.23（`297e54fd10`）之间引入的性能回归——见稳定性部分。

- [Issue #49336](https://github.com/sgl-project/sglang/issues/49336) — 未报告 API/配置破坏，但存在与 v0.5.23 升级相关的 decode 延迟回归。

## 3. 新模型与硬件支持

- **DeepSeek 图像聊天路由（sgl-router）：** PR #48937 通过 `ChatFormatter::encode_multimodal_request` 为多模态 DeepSeek 聊天添加前缀缓存感知路由（依赖 #46562）。[PR #48937](https://github.com/sgl-project/sglang/pull/48937)
- **Clover-35B-A3B-Preview（Diffuspace）：** PR #46927 添加原生 serving 以及使用共享模型进行并行 draft 生成和 AR 验证的自推测解码。[PR #46927](https://github.com/sgl-project/sglang/pull/46927)
- **寒武纪 MLU 后端：** Roadmap issue #45615 提议 MLU 后端。[Issue #45615](https://github.com/sgl-project/sglang/issues/45615)
- **AMD gfx1250（MI45x）AITER 固定：** Issue #47562 跟踪针对 gfx1250 的独立 AITER 固定版本的手动验证，因为 Scout CI 矩阵仅覆盖 gfx942/gfx950。[Issue #47562](https://github.com/sgl-project/sglang/issues/47562)
- **XPU 整合：** PR #49356 批量处理简单的 XPU 模型启用。[PR #49356](https://github.com/sgl-project/sglang/pull/49356)
- **NPU 扩散：** PR #43367 在 NPU 上为 Wan2.2-T2V-A14B W4A4F4 DAOS 添加 MXFP4 attention。[PR #43367](https://github.com/sgl-project/sglang/pull/43367)
- **外部模型扩展点：** Issue #48475 请求为运行时注册模型提供公共钩子（registry、attention 查找、attention kernel、sampler logprobs）——目前有四个私有触点。[Issue #48475](https://github.com/sgl-project/sglang/issues/48475)

## 4. 性能与优化

**AMD / ROCm**
- PR #47309：对 DCP indexer 进行行拆分，在 AITER asm kernel 上运行自有 sparse MLA，并对 a2a 进行流水线化（基于 #47180 / #44322 堆叠）。[PR #47309](https://github.com/sgl-project/sglang/pull/47309)
- PR #47872：DSA decode 的单遍 Triton/Gluon top-k——在 MI355X 上 ≥72 行/rank 时为 58–82 µs 对比 113–127 µs（快 1.5–1.9×）。[PR #47872](https://github.com/sgl-project/sglang/pull/47872)
- PR #43126：在 MI355X 上为 Kimi-K3 直接从 radix-4 router 启动发出 AITER 的 MoE sort，目标是 decode 受限的 AgentX 延迟（c16 时每次 MoE 调用约 80 tokens）。[PR #43126](https://github.com/sgl-project/sglang/pull/43126)
- PR #49377：为 MI450（gfx1250）大张量集合通信提供 ring all-reduce 和展开的 `2shot_push`。[PR #49377](https://github.com/sgl-project/sglang/pull/49377)
- PR #38806：Qwen3.5 GDN prefill——在 ROCm 上将 Q/K/V/Z/B/A 保持为 strided view，并消除额外的 QKV 拆分启动。[PR #38806](https://github.com/sgl-project/sglang/pull/38806)
- PR #48398：GDN Triton 分块 prefill 读取 strided q/k/v 并跳过无效的输出零填充。[PR #48398](https://github.com/sgl-project/sglang/pull/48398)

**正确性 / 调度**
- PR #49404：修复 EAGLE verify 将单个 repetition-penalty 行应用于所有 draft 位置的问题；现在按 draft 位置应用。[PR #49404](https://github.com/sgl-project/sglang/pull/49404)
- PR #49386：PP 调度器在记录完成事件前等待本地 PP forward，并跳过过期的 DSpark KV 写入。[PR #49386](https://github.com/sgl-project/sglang/pull/49386)
- PR #45180：为 MiniMax-H3 提供可选的 FP8 视频 VAE 解码器配置（逐通道 E4M3 权重、逐 token 激活缩放、融合 norm/act/quant）。[PR #45180](https://github.com/sgl-project/sglang/pull/45180)

**路线图**
- PD 分离 Q4 路线图（#43699）和 Agentic Cache Scheduling Q4 路线图（#43098，超节点级 L1+L2 KV 共享，具备 router 缓存位置感知）。[Issue #43699](https://github.com/sgl-project/sglang/issues/43699) · [Issue #43098](https://github.com/sgl-project/sglang/issues/43098)

## 5. 稳定性与回归

按严重程度排序：

1. **SM90 decode 回归 v0.5.22 → v0.5.23** — 在 8×H20-3e 上使用 DeepSeek-V4.1-Flash + DSPARK，并发 12 时，8K–64K 上下文范围内每步 decode 时间增加 33–45%；`use_deep_select_decode` 在 Hopper 上也会损失接受率。尚无修复 PR。[Issue #49336](https://github.com/sgl-project/sglang/issues/49336)
2. **SM120 无法开箱即用** — CUDA 13 wheel 在 RTX 50 系列消费级 Blackwell 上安装失败；相关：#49173（现已关闭）从 DSV4.1 DeepGEMM indexer 门控中移除了 SM120/SM121，导致 GB10 上通过逐层 `tolist()` torch 回退出现约 8× prefill 回归。[Issue #49209](https://github.com/sgl-project/sglang/issues/49209) · [Issue #49173](https://github.com/sgl-project/sglang/issues/49173)
3. **AMD gfx1151（Strix Halo）：** MLA 形状的 Triton decode 在不规则长序列批次上返回 NaN/错误行（#48967）；`--enable-deterministic-inference` 在启动时失败，因为 batch-invariant matmul 需要 99 KB LDS，而可用仅 64 KB（#48968）。两者均未关闭，无关联修复。[Issue #48967](https://github.com/sgl-project/sglang/issues/48967) · [Issue #48968](https://github.com/sgl-project/sglang/issues/48968)
4. **Breakable prefill CUDA graph 正确性** — 填充到捕获 bucket 的短 prefill 与 eager logprobs 不一致（Qwen3.8-27B-NVFP4、混合 GDN、v0.5.23）。[Issue #49133](https://github.com/sgl-project/sglang/issues/49133)
5. **SM120 上的 GLM-5.3-Flash** — fa4 后端在混合 extend reshape 的 CUDA-graph 捕获时崩溃；Triton 是唯一可用路径（v0.5.20 镜像）。[Issue #42012](https://github.com/sgl-project/sglang/issues/42012)
6. **Qwen-VL 解析** — 消息内容中的字面 `<image>` 文本会导致请求以 500 token 不匹配错误中止。[Issue #43979](https://github.com/sgl-project/sglang/issues/43979)
7. **CI/基础设施：** flaky-test 跟踪器 #42752 现已有 191 条评论；Xeon SPR 任务在 sglang-spr08/18/19 上 KV 分配期间被 OOM kill（#48411）；扩散性能检查依赖主机，任务内重试无帮助（#48311）。[Issue #42752](https://github.com/sgl-project/sglang/issues/42752) · [Issue #48411](https://github.com/sgl-project/sglang/issues/48411) · [Issue #48311](https://github.com/sgl-project/sglang/issues/48311)

最近也因不活跃而关闭：8×H20 上 DeepSeek-V4 TP=8 245K 上下文 decode 挂起（#33549）、Kimi-K3 `tool_choice=required` 在 2P2D TP8/DCP8 中挂起（#37430）——如果你在旧版本上遇到其中任一问题，则相关。

## 6. 对应用开发者的意义

- **有意识地固定版本。** 如果你在 Hopper 上使用推测解码服务 DeepSeek-V4.1-Flash，在采用 v0.5.23 前先做基准测试——报告的 33–45% decode 减速对交互式 agent 而言影响重大。
- **SM120 消费级 Blackwell 尚不是开箱即用的目标。** RTX 50 系列和 GB10 部署应预期需要固定旧镜像、强制 Triton attention，并避免依赖 DeepGEMM indexer 路径（见 #49209、#49173、#42012）。
- **EAGLE repetition penalty 修复值得关注**，如果你在使用推测解码时设置 `repetition_penalty != 1`——PR #49404 修正了 verify 中已知的质量偏差。
- **PD 分离和超节点 KV 缓存共享**（#43699、#43098）是最影响多租户 agent 路由成本的路线图项；缓存感知多模态路由（#48937）是你服务图像聊天时的第一个实用组件。
- **自托管扩散/视频模型的 agent 框架**获得增量收益：MiniMax-H3 FP8 VAE 解码（#45180）和用于 DMD2 训练的仅轨迹 rollout 响应（#49143）。[PR #49143](https://github.com/sgl-project/sglang/pull/49143)
- **如果你维护树外模型，** 请关注 #48475——当前四个私有触点（registry、attention 查找、attention kernel、logprob sampler）在正式化之前是维护隐患。

</details>

<details>
<summary><strong>llama.cpp</strong> — <a href="https://github.com/ggml-org/llama.cpp">ggml-org/llama.cpp</a></summary>

# llama.cpp 摘要 — 2026-11-10

## 1. 今日亮点

llama.cpp 密集发布了一批版本（b12219–b12236），涵盖多模态音频重采样、外部 PLE/分片加载，以及一个长期存在的服务器 JSON-schema 修复。后端正确性获得了实质性关注：Metal flash-attention 越界写入已修补，T-TRI 获得了反向传播，ggml-backend 现在会跳过上传从未被读取的输入。模型侧，Granite NVFP4 缩放管道和 DFlash2/Kimi K3.1 视觉集成正在进行中。

## 2. 版本发布与破坏性变更

- **b12236 — `mtmd_helper_resampler`**（[详情](https://github.com/ggml-org/llama.cpp/releases)）：为绕过 `miniaudio` 的原始 PCM 输入路径新增的辅助函数；修复了原始音频的重采样问题。包含文档和测试。
- **b12233 — 外部 PLE 文件加载**（[#31185](https://github.com/ggml-org/llama.cpp/pull/31185)）：`--ple` 现在是 `--shard` 的别名，并接受多个分片路径；转换器会输出独立的 PLE 导出。**行为说明：** 现有的单参数 `--ple` 脚本仍然有效，但多分片用法应予以审查。
- **b12229 — 服务器 JSON schema 修复**（[#28697](https://github.com/ggml-org/llama.cpp/pull/28697)）：OpenAI 兼容的 `/v1/chat/completions` 端点现在会遵循 `response_format: {type: "json_schema"}` 中的**顶层 `schema`**，而不再仅支持嵌套的 `json_schema` 包装形式。使用嵌套形式的客户端不受影响。
- 其他版本：b12230（ggml-zdnn 张量校验）、b12228（TRI 反向传播）、b12224（后端输入上传剪枝）、b12223（Granite NVFP4）、b12221（Metal FA 越界修复）、b12220（Metal TRI 选择）、b12219（OpenCL `climits` 引入）。

## 3. 新模型与硬件支持

- **Kimi K3.1 视觉** — PR [#31980](https://github.com/ggml-org/llama.cpp/pull/31980)（ngxson），与 vLLM 输出对齐；修复 [#31963](https://github.com/ggml-org/llama.cpp/issues/31963)。
- **面向 Recursive Ripple-65B-Preview 的 DFLASH2** — PR [#31904](https://github.com/ggml-org/llama.cpp/pull/31904)，已在 Apple M5 Max / Metal 上以 Q4_K 目标 + Q8_0 草稿模型测试。
- **腾讯 DSpine 草稿支持** — PR [#31941](https://github.com/ggml-org/llama.cpp/pull/31941)（Qwen3-8B-DSpine-b16、Qwen3.8-27B-DSpine-b16），一种 DFlash 块草稿变体，每个草稿层带有一个代码桥接。
- **Gemma 4 音频流式** — PR [#31965](https://github.com/ggml-org/llama.cpp/pull/31965) 已关闭；因果编码器在 `step_ms=480` 下复用 `mtmd_audio_stream`，最差行余弦相似度为 0.993–0.997（对比非流式）。解决 [#31561](https://github.com/ggml-org/llama.cpp/issues/31561)。
- **Granite NVFP4 权重缩放** — PR [#31449](https://github.com/ggml-org/llama.cpp/pull/31449) 通过 `build_ffn` 为所有 Granite 架构落地缩放管道（已在 b12223 中合入）。
- **OpenCL** — 新增 f32 `MUL_MAT_ID`（[#31783](https://github.com/ggml-org/llama.cpp/pull/31783)，已合并），对 `test-llama-archs` 有用。
- **OpenVINO** — `FILL` 作为原地循环缓存清除，并修复 NaN 粘滞问题（[#31977](https://github.com/ggml-org/llama.cpp/pull/31977)）。
- **SYCL** — `ROLL` 算子现在能处理置换源（[#31799](https://github.com/ggml-org/llama.cpp/pull/31799)）。
- **XDNA 后端**功能请求仍然开放，社区兴趣浓厚（[#21725](https://github.com/ggml-org/llama.cpp/issues/21725)，👍 39）。

## 4. 性能与优化

- **后端上传剪枝**（[#31419](https://github.com/ggml-org/llama.cpp/pull/31419)，b12224）：ggml-backend 不再上传内容从未被读取的张量——对多后端图直接节省带宽/H2D 开销。
- **Adreno Vulkan 标量 FA 上限**（[#30829](https://github.com/ggml-org/llama.cpp/pull/30829)）：对 head size >128 且 KV ≥1024 的情况将行数上限设为 8，防止寄存器溢出（在 X1-85 上观察到每次调用 264 B）。与移动端/Qualcomm 部署相关。
- **OpenCL int8 融合 KQ**（[#30249](https://github.com/ggml-org/llama.cpp/pull/30249)）：按 head-size 切片暂存 Q，启用 head size 512，并在分解式 prefill attention 中减少随 head size 增长的本地内存。
- **ARM NEON 非 dotprod IQ4_NL_Q8 GEMM/GEMV 4×4 重打包**（[#30165](https://github.com/ggml-org/llama.cpp/pull/30165)）：将快速路径扩展到 ARMv8 < 8.2-A（无 `vdotq_laneq_s32`）。
- **x86 BF16 点积**（[#31878](https://github.com/ggml-org/llama.cpp/pull/31878)）：在不支持 AVX512-BF16 的 x86 上保持 F32 激活，避免重复转换和精度损失（[#31842](https://github.com/ggml-org/llama.cpp/issues/31842)）。
- **MMVQ 截断表**（[#28090](https://github.com/ggml-org/llama.cpp/issues/28090)）：提议为 Q4_0 添加 `sm_86` 条目（在 A10 上约 9.1%）；仍开放。
- **Battlemage 上的 SYCL**：反复出现“极其糟糕”性能的报告（[#22413](https://github.com/ggml-org/llama.cpp/issues/22413)，现已关闭）——除非提交新 issue，否则预计不会有即时修复。

## 5. 稳定性与回归

按严重程度排序：

1. **高 — `/v1/chat/completions` 上 KV 缓存被错误截断**（[#11970](https://github.com/ggml-org/llama.cpp/issues/11970)，50 条评论，仍开放）。长期存在；影响多轮对话的正确性。未引用修复 PR。
2. **高 — 统一 KV 缓存下提示缓存恢复失败**（[#30961](https://github.com/ggml-org/llama.cpp/issues/30961)）。槽位空闲清除顺序 bug；影响服务器恢复延迟。
3. **高 — 含 NaN 的循环状态未被清除**（[#29830](https://github.com/ggml-org/llama.cpp/issues/29830)）。NaN 传播会在循环架构上扰乱后续请求。
4. **高 — 投机解码破坏 temp‑0 一致性**（[#31472](https://github.com/ggml-org/llama.cpp/issues/31472)）。`draft-dflash` / `draft-dspark` 即使在 100% 接受率下也偏离自回归输出——**与对可复现性敏感的评估管道直接相关**。与后端无关。
5. **中 — `ggml_backend_sched_alloc_splits` 意外重分配崩溃**（[#28753](https://github.com/ggml-org/llama.cpp/issues/28753)），出现在 Intel Arc / IntelLLVM 构建上。
6. **中 — SYCL+MoE 上 logprobs 非确定性**（[#31206](https://github.com/ggml-org/llama.cpp/issues/31206)）；请求 `seed` 无法固定分布。
7. **中 — Metal 长上下文下 1 个 token 后解码 EOS**（Qwen3.8-Flash-Next）（[#28805](https://github.com/ggml-org/llama.cpp/issues/28805)，因过期关闭）——阈值随量化/KV 量化/n_ctx 变化。
8. **中 — Windows AMD VRAM 误报导致共享内存溢出**（[#30250](https://github.com/ggml-org/llama.cpp/issues/30250)）。
9. **低 — Vulkan Adreno 管道创建失败**（`mul_mat_vec_q4_k_f32_f32`，shaderc/NDK SPIR-V 不匹配）（[#28635](https://github.com/ggml-org/llama.cpp/issues/28635)）。
10. **低 — `ggml_gallocr` 在节点身份变化时复用过期计划**（[#28448](https://github.com/ggml-org/llama.cpp/issues/28448)，因过期关闭）——存在静默稀疏 MoE 损坏风险。

**今日/本周期已修复：** Metal FA 越界写入（[#31662](https://github.com/ggml-org/llama.cpp/pull/31662)，b12221）；Metal TRI ±inf→NaN（[#30711](https://github.com/ggml-org/llama.cpp/pull/30711)，b12220）；Qwen4exp 合并张量断言（[#31189](https://github.com/ggml-org/llama.cpp/issues/31189)，已关闭）；json_schema response_format（[#10732](https://github.com/ggml-org/llama.cpp/issues/10732)，已关闭）。

## 6. 这对应用开发者意味着什么

- **OpenAI 兼容性正在收紧。** `response_format` 中的顶层 `json_schema` 现在可用（b12229）——你可以去掉包装 hack。但 **KV 缓存截断（#11970）和提示缓存恢复失败（#30961）仍然开放**，因此对长多轮会话要保守，并考虑在服务器端使用固定/扩展上下文。
- **投机解码的可复现性注意事项。** 如果你在 `--temp 0` 下运行评估或确定性 agent，在 [#31472](https://github.com/ggml-org/llama.cpp/issues/31472) 解决之前，避免在 qwen35moe 目标上使用 `draft-dflash`/`draft-dspark`——输出与 AR 解码并非 token 级一致。
- **SYCL+MoE 上的非确定性 logprobs**（[#31206](https://github.com/ggml-org/llama.cpp/issues/31206)）意味着在 Intel iGPU + MoE 配置上，不要依赖 `/v1/completions` 的 logprobs 进行评分/重排序。
- **多模态音频正在获得一等公民地位。** `mtmd_helper_resampler`（b12236）+ Gemma 4 流式（余弦 ≥0.993 对比非流式）使 `/v1/audio/*` 和实时端点对生产语音 agent 变得可行——预计 [#31763](https://github.com/ggml-org/llama.cpp/pull/31763) 会带来更多 `/v1/audio/speech` 工作。
- **token 感知的聊天解析即将到来**（[#29307](https://github.com/ggml-org/llama.cpp/pull/29307)、[#31978](https://github.com/ggml-org/llama.cpp/pull/31978)）。如果你从原始输出中自行解析 `<think>`/工具调用 token，请计划在它落地后迁移到服务器发出的结构化字段——这将比字符串匹配更健壮。
- **新的草稿模型架构**（DSpine、DFLASH2、Kimi K3.1 视觉）持续扩展投机解码和多模态面；请针对你的目标量化和后端进行验证——目前有几个仅在 Metal/CUDA 上测试过。
- **Windows AMD 用户**：VRAM 误报（#30250）可能悄悄推入共享内存并拖垮吞吐量；请监控你实际的 VRAM 余量，而不是信任运行时报告的内存预算。

</details>

<details>
<summary><strong>Ollama</strong> — <a href="https://github.com/ollama/ollama">ollama/ollama</a></summary>

# Ollama 摘要 — 2026-11-10

## 1. 今日亮点
候选版本 **v0.40.16-rc0** 发布，包含 OpenAI 兼容性工作：在 `/v1/responses` 请求中转发截断选项，以及当未安装任何模型时 `/v1/models` 返回空数组（而非错误）。进行中最重要的修复是 PR **#19632**，这是一个多问题的“VRAM 核算与 GPU 拆分”修正，针对模型驱逐、加载失败和不必要的多 GPU 分散问题。与此同时，浮现出若干内存/调度器回归：Windows 上间歇性 CUDA 初始化回退、ROCm/`ollama ps` VRAM 报告差异，以及 Vulkan/RADV 在首次请求后约 30 倍的生成速度下降。

## 2. 发布与破坏性变更
- **v0.40.16-rc0** (v0.40.16) — https://github.com/ollama/ollama/pull/19501
  - `openai`：在 responses 请求中转发截断选项（PR #19501）。
  - `openai`：当未安装任何模型时，`/v1/models` 返回空数组（PR #19539）——假设空模型列表会在加载时失败的客户端应验证其对 `200` + `[]` 的处理。
  - `llam...`（发布说明被截断）；视为 RC——除 `/v1/models` 行为变更外，无已确认的破坏性路径。
- 文档/规范修正在进行中：PR **#19626** 提议在原生 API 规范中要求 `stop` 为数组（https://github.com/ollama/ollama/pull/19626）——字符串形式的 `stop` 目前被规范接受，但被服务器拒绝。关注未来版本中的对齐。

## 3. 新模型与硬件支持
- **Qwen3.5 / Qwen3.5-MoE 并行请求**：PR **#17144** 提议解除 `server/sched.go` 中硬编码的 `numParallel = 1` 黑名单，因为上游 llama.cpp 混合架构崩溃已修复（2026-03-08）—— https://github.com/ollama/ollama/pull/17144。
- **Docker MLX/Vulkan 对等**：PR **#18592** 添加缺失的库/配置，使 MLX runner 和 Vulkan 驱动（包括 Nvidia Vulkan）在容器镜像内可用—— https://github.com/ollama/ollama/pull/18592。
- **仅 Vulkan 用户投诉**：Issue **#19474**（已关闭）报告在仅 Vulkan 系统（如 Intel）上下载 CUDA 驱动——未引用已确认的修复—— https://github.com/ollama/ollama/issues/19474。
- **MLX 内存预检**：PR **#19419** 将 llama-server 内存预检应用于 Apple Silicon 上的 MLX 加载—— https://github.com/ollama/ollama/pull/19419。

## 4. 性能与优化
- **VRAM 核算与 GPU 拆分（进行中）**：PR **#19632**（issue **#19631**）修复错误的 VRAM 预测、GPU 开销预留以及不必要的模型拆分，后者导致了错误的驱逐和加载拒绝—— https://github.com/ollama/ollama/pull/19632
- **Vulkan 提示缓存回归**：Issue **#19201**（已关闭）记录了在启用捆绑的 llama.cpp 主机内存提示缓存时，第二次相同请求出现可复现的**约 29.7 倍生成吞吐量崩塌**，AMD RX 6600 XT / Mesa RADV，Ollama 0.40.8—— https://github.com/ollama/ollama/issues/19201
- **`num_predict == -2`** 动态 token 限制（填充上下文缓冲区）PR **#9547** 已关闭—— https://github.com/ollama/ollama/pull/9547
- **Qwen3.5 4B 提示处理指导** 在 16GB M4（100% GPU）上被请求；未确认缺陷—— https://github.com/ollama/ollama/issues/19282

## 5. 稳定性与回归
按严重程度排序：
1. **CUDA 预热失败 + 静默 CPU 回退** — Issue **#17380**（开放）：Windows/RTX 5070 Ti 上使用 `gpt-oss:20b` 时间歇性出现 `CUDA error: shared object initialization failed`，与静默的 `CUDA_Host` → `CPU` 固定缓冲区回退相关；未引用修复 PR—— https://github.com/ollama/ollama/issues/17380
2. **Windows 更新器崩溃** — Issue **#19621**（开放）：`IsProcRunning` 将 `EnumProcesses` 的字节计数当作 PID 计数，当运行超过 512 个进程时 panic；同日打开修复 PR **#19625**—— https://github.com/ollama/ollama/issues/19621 · https://github.com/ollama/ollama/pull/19625
3. **Qwen3.5 4B 仅思考响应** — Issue **#18916**（开放）：使用 `num_ctx:65536` 的长对话返回 `done:true` 但只有 `message.thinking`，无内容/工具调用—— https://github.com/ollama/ollama/issues/18916
4. **工具调用压缩不匹配** — Issue **#19623**（开放）：在 Codex Desktop + DeepSeek 中重复压缩保留了函数输出但没有匹配的调用—— https://github.com/ollama/ollama/issues/19623
5. **VRAM 报告差异** — Issue **#17251**（开放）：使用 MTP 加速器模型时，`ollama ps` 相对于 `rocm-smi` 低报了 VRAM—— https://github.com/ollama/ollama/issues/17251
6. **企业代理拉取 403** — Issue **#19604**（已关闭）：在 Blue Coat 代理后 `ollama pull` 返回 403，尽管 `curl` 正常工作—— https://github.com/ollama/ollama/issues/19604
7. **Olmo3 工具参数中的 Unicode 损坏** — PR **#18906**（开放）修复 `splitArguments` 中的 UTF-8 截断—— https://github.com/ollama/ollama/pull/18906
8. **Modelfile glob 路径解析** — PR **#19628**（开放）：当路径包含 glob 元字符时，相对通配符针对字面目录解析—— https://github.com/ollama/ollama/pull/19628

## 6. 对应用开发者的意义
- **OpenAI 兼容客户端**：`/v1/models` 空数组变更是在客户端代码中需要测试的一项行为——将零安装模型视为有效状态，而非错误。`/v1/responses` 中的截断转发提升了与 OpenAI 语义的对等性。
- **结构化工具调用**：如果你使用 Codex/DeepSeek 风格的压缩循环，请固定版本并关注 #19623——孤立的函数输出可能破坏代理状态机。使用非 ASCII 参数的 Olmo3 用户应跟踪 #18906。
- **VRAM 敏感部署**： #19632 调度器修复直接解决了错误的驱逐和不必要的多 GPU 拆分——这是采用此 RC 之后下一个稳定版的强有力理由。目前，在确定容量之前，请对照 `rocm-smi`/`nvidia-smi` 验证 `ollama ps` 数字。
- **Qwen3.5 并行性**：一旦 #17144 落地，MoE 服务吞吐量增益将可用；在此之前，这些架构预期 `numParallel=1`。
- **Windows 集群运维**：超过 512 个进程的更新器崩溃（#19621/#19625）意味着繁忙主机上的自动部署工具应分阶段进行，而非完全无人值守，直到修复发布。

---
*由 github.com/ollama/ollama 2026-11-10 数据快照生成。*

</details>

<details>
<summary><strong>LiteLLM</strong> — <a href="https://github.com/BerriAI/litellm">BerriAI/litellm</a></summary>

# LiteLLM 摘要 — 2026-11-10

## 1. 今日要点
v1.108.4 发布，附带 cosign 签名的 Docker 镜像（验证密钥在 commit `e82a3c6` 中引入）。Issue 队列被成本/预算正确性问题占据：一个按团队按模型限流器以配置值的一半执行限制（已关闭），以及围绕 DB 存储部署、过期重置行和缓存驱逐缺口的多起预算核算 bug。PR 方面，工作正收敛于原生（Rust 后端）`/v1/messages` 代理路径——一个由七个 PR 组成的栈，修复日志、缓存、流式中断和失败行计费——外加向 1.108.x 和 1.109.0 发布分支的回移。

## 2. 发布与破坏性变更
- **v1.108.4** — 通过 cosign 对 Docker 镜像签名；验证密钥见发布说明。[Release](https://github.com/BerriAI/litellm/releases)
- **即将到来的 2.0.0 行为变更（已预告）：** 密钥或团队上空的 `models` 列表表示“所有模型”的遗留规则将被移除。PR #50720 增加了启动时警告，统计依赖此默认值的密钥/团队，并记录了迁移路径。[PR #50720](https://github.com/BerriAI/litellm/pull/50720)
- **`litellm.turn_on_message_logging` 文档 bug** 已关闭 — 实际 API 是 `turn_off_message_logging`。[Issue #37143](https://github.com/BerriAI/litellm/issues/37143)

## 3. 新模型与硬件支持
- **Claude “fable-5-2”（Fable 5.2）** 的能力标志和提示缓存定价正从 Anthropic API 扩展到 **Vertex AI** 和 **Bedrock Converse**，并添加了线格式和成本测试。[PR #50742](https://github.com/BerriAI/litellm/pull/50742)
- **Z.AI `glm-5.2[1m]`**（1M 上下文变体）已有文档，但通过代理返回 `Unknown Model` — 仍处于打开状态。[Issue #32218](https://github.com/BerriAI/litellm/issues/32218)
- **自定义命名部署**（例如 Azure AI Foundry 上服务 DeepSeek-V4-Flash 的 `my-flash`）的 tokenizer 解析现在使用配置的 `base_model` tokenizer，而不是回退到 OpenAI 的。[PR #50734](https://github.com/BerriAI/litellm/pull/50734)
- **Rust 后端 pip 二进制**工作继续作为安装问题的跟踪 issue。[Issue #31261](https://github.com/BerriAI/litellm/issues/31261)

## 4. 性能与优化
- **浪费的 Redis 写入：** 限流器无条件写入调用后计数器，即使对于未配置任何限制的密钥/用户/团队也是如此。已提议修复以在高吞吐量下跳过这些写入；PR 已陈旧，但 issue 仍处于打开状态。[Issue #31880](https://github.com/BerriAI/litellm/issues/31880)
- **基于成本路由下的提示/压缩节省：** 当同一模型名有多个部署时，`compression_savings_spend` 和 `prompt_caching_savings_spend` 始终为 $0 — 已关闭。[Issue #37117](https://github.com/BerriAI/litellm/issues/37117)
- **缓存读取计费：** `azure/us/gpt-4o-2024-11-20` 和 `azure/eu/...` 缺少 `cache_read_input_token_cost`，导致缓存读取按零计费 — 已关闭。[Issue #37823](https://github.com/BerriAI/litellm/issues/37823)
- **原生 `/v1/messages` 路径：** 缓存条目现在在路由器标记响应之后写入（与 Python 行为一致），流中途客户端断开连接也能正确计费 — 这是七个 PR 栈的一部分。[PR #50426](https://github.com/BerriAI/litellm/pull/50426), [PR #50425](https://github.com/BerriAI/litellm/pull/50425)

## 5. 稳定性与回归（按严重程度排序）

**高**
- **限流器将按团队按模型限制减半：** `model_per_team` 的 RPM/TPM 为 `N` 时在约 `N/2` 处返回 429。已提交精确复现；已关闭。[Issue #34140](https://github.com/BerriAI/litellm/issues/34140)
- **预算重置任务静默地将支出永久清零：** 一行 `budget_duration=null` 但 `budget_reset_at` 为过期过去时间的记录每个 tick 都被拾取，无限期清除支出。已关闭。[Issue #39370](https://github.com/BerriAI/litellm/issues/39370)
- **项目预算绕过原子预留：** 项目最大预算仅在读取时生效，从未参与调用前预留路径，因此并发请求可能超支。已关闭。[Issue #34101](https://github.com/BerriAI/litellm/issues/34101)
- **通过过期的单 pod 支出绕过最终用户预算：** 在 Redis 计数器干净未命中（60s TTL）时，过期的 pod 本地回退值可能放行请求。已关闭。[Issue #34238](https://github.com/BerriAI/litellm/issues/34238)
- **已删除的团队密钥仍具认证有效性：** `/team/delete` 从 DB 中移除密钥，但从未将其从认证缓存中驱逐。已关闭。[Issue #34217](https://github.com/BerriAI/litellm/issues/34217)

**中**
- **流式用量在链式代理设置**（Front → Upstream → Bedrock）中严重少计，而非流式用量正确；根因追溯到流聚合，而非提供商转换。打开。[Issue #36114](https://github.com/BerriAI/litellm/issues/36114)
- **JSON-RPC `id` 被剥离**，由 `pass_through_endpoints` 因与内部参数名冲突导致 — 破坏 JSON-RPC 2.0 载荷。打开。[Issue #49479](https://github.com/BerriAI/litellm/issues/49479)
- **价格重载清除 DB 部署成本映射：** 在空 `model_list` + `STORE_MODEL_IN_DB` 下，定期成本映射重载丢弃运行时成本注册（路由器不在 `_live_routers` 中）。已关闭。[Issue #43444](https://github.com/BerriAI/litellm/issues/43444)
- **Presidio PII 去掩码缺口：** 工具调用参数从未去掩码（[#31950](https://github.com/BerriAI/litellm/issues/31950)，打开），且占位符按消息编号导致跨消息损坏（[#31959](https://github.com/BerriAI/litellm/issues/31959)，打开）。
- **`reasoning_effort=xhigh` 在模型映射缺少能力时被静默降级**，而非被拒绝。已关闭。[Issue #40471](https://github.com/BerriAI/litellm/issues/40471)
- **`general_settings` 中的 `completion_model` 覆盖客户端模型**，而非作为回退。打开，陈旧。[Issue #21554](https://github.com/BerriAI/litellm/issues/21554)
- **VSCode 原生模型提供商与 LiteLLM 不兼容**。打开。[Issue #35775](https://github.com/BerriAI/litellm/issues/35775)

**低**
- `/v1/messages` 流式在 Bedrock 推理模型的内容块转换处丢弃第一个 chunk。已关闭。[Issue #25214](https://github.com/BerriAI/litellm/issues/25214)
- Ollama 流式为所有工具调用发出 `index=0`，将并行调用合并为畸形 JSON。已关闭。[Issue #33678](https://github.com/BerriAI/litellm/issues/33678)
- `token_counter` 在数组工具参数缺少 `items` 时抛出 `KeyError: 'items'` → `/v1/messages/count_tokens` 上 500。已关闭。[Issue #46052](https://github.com/BerriAI/litellm/issues/46052)
- Azure 请求泄漏内部 `model_alias_map` 字段，被上游拒绝。已关闭。[Issue #42409](https://github.com/BerriAI/litellm/issues/42409)
- Bedrock `count_tokens` 在畸形请求体上返回 500 而非 400；已在 [PR #50708](https://github.com/BerriAI/litellm/pull/50708) 中修复。
- 不稳定的 MCP 测试 `test_cold_concurrent_schema_validation_accepts_valid_arguments`。已关闭。[Issue #46212](https://github.com/BerriAI/litellm/issues/46212)

**进行中的修复 PR**
- Bedrock 直通拒绝原始 AWS 模型 ID（`POST /bedrock/model/us.anthropic.claude-haiku-5-5/converse` → 400）— [PR #50737](https://github.com/BerriAI/litellm/pull/50737)
- `/v1/messages` 在不受支持参数检查之前忽略 `additional_drop_params`（回移 + 主分支）— [PR #50755](https://github.com/BerriAI/litellm/pull/50755), [#50753](https://github.com/BerriAI/litellm/pull/50753)
- 故障关闭预算执行对缺少成员行的团队用户返回 503 — [PR #50603](https://github.com/BerriAI/litellm/pull/50603)
- Bedrock Converse/Responses 上空的最终用户消息被丢弃，导致 Claude 5.x 预填充 400 — [PR #50701](https://github.com/BerriAI/litellm/pull/50701)
- 路由器默认元数据替换而非合并调用方元数据 — [PR #50700](https://github.com/BerriAI/litellm/pull/50700)
- Responses WebSocket：仅第一个被拒绝的轮次获得失败行 — [PR #50731](https://github.com/BerriAI/litellm/pull/50731)
- Claude Code 子代理流量在复杂度路由器中覆盖主代理层级固定 — [PR #50741](https://github.com/BerriAI/litellm/pull/50741)

## 6. 这对应用开发者意味着什么
- **如果你使用按团队按模型上限，请审计你的限流。** 减半 bug（#34140）意味着有效吞吐量可能只有配置的一半；升级后重新检查，并在依赖 `model_per_team` 实现 SLA 式隔离前进行负载测试。
- **预算执行在历史上多条路径上存在漏洞**（项目预算、过期重置行、缓存未命中回退）。如果你依赖硬性支出上限实现租户隔离，请通过并发负载验证而非信任仪表盘，并保留外部支出告警作为兜底。
- **删除团队不会立即撤销密钥**（在受影响版本上）— 对已停用团队的密钥进行轮换或显式 `/key/delete`。
- **链式代理部署会少计流式用量。** 如果你在多跳设置中基于 LiteLLM 的用量数字向下游计费，请与提供商侧账单对账（#36114）。
- **PII 护栏用户：** Presidio 掩码对工具调用参数和多消息请求不完整 — 不要将其作为带工具的代理工作负载的唯一 DLP 层。
- **现在为 2.0.0 空 `models` 语义变更做规划：** 在密钥和团队上显式枚举允许的模型，并关注新的启动警告（#50720）以发现遗留配置。
- **原生 `/v1/messages` 路径正在成熟**（被放弃流的计费、缓存写入顺序、每 WebSocket 轮次的失败行）。如果你大规模使用 Claude Code 或 Anthropic 协议客户端，预计在 1.108.x/1.109 系列中计量准确性会有所改善。

</details>

<details>
<summary><strong>Unsloth</strong> — <a href="https://github.com/unslothai/unsloth">unslothai/unsloth</a></summary>

# Unsloth 摘要 — 2026-11-10

## 1. 今日亮点

过去 24 小时没有新版本发布，但 `main` 分支上堆叠了大量推理引擎相关的工作：Daniel Han 提交了一系列 decode CUDA graph PR（#16587 为基础，外加针对 Gemma 4 和 Qwen3.5/3.6 的模型专属扩展），声称**在 T4 上实现 2–8 倍 decode 加速，且输出 token 完全一致**。与此同时，Studio 的模型服务层（面向前端本地模型的 HTTP 网关）积累了几个与 OpenAI 兼容端点相关的 issue——尤其是自定义/vLLM/Ollama 连接下视频被静默拒绝的问题，但修复 PR 已开放（#17322）。

## 2. 版本发布与破坏性变更

无新版本发布。有一个依赖锁定隐患值得注意：**typer 0.29.0（发布于 2026-11-09 18:48 UTC）会导致 Windows 上的 Studio 安装失败**，原因是它持有 `pydantic_core` 不放。将 typer 限制在 `<0.29` 的修复已合并（#17319）。如果你基于 Studio 安装器构建，在 Windows 上请避免使用未锁定版本的 `typer`。

## 3. 新模型与硬件支持

- **Gemma 4、Qwen3.5 / Qwen3.6（`qwen3_5`、`qwen3_5_moe`）** — CUDA graph 下的 flex-attention decode 步骤（[#17329](https://github.com/unslothai/unsloth/pull/17329)）。
- **Qwen3.5/3.6 混合线性注意力（Gated DeltaNet）** — 为 `LinearAttentionLayer` 静态缓存提供 decode graph 支持，此前该场景被直接拒绝（[#17324](https://github.com/unslothai/unsloth/pull/17324)）。
- **基础 decode-graph PR 中的文本模型广泛覆盖**：Gemma 3/4、Muse-Glimmer、Qwen3 / Qwen3-VL text、Llama、Mistral 3、Granite、Phi-4、OLMo 2、Cohere 2、GLM-4（[#16587](https://github.com/unslothai/unsloth/pull/16587)）。
- **Linux GPU 检测回归** — issue 报告支持 CUDA 的 Linux 主机被强制使用 CPU（[#17235](https://github.com/unslothai/unsloth/issues/17235)，已关闭）。另有报告称 Windows 图像生成在 RTX 3060 上回退到内存（[#16803](https://github.com/unslothai/unsloth/issues/16803)，已关闭）。

## 4. 性能与优化

- **所有 FastModel `model.generate()` 默认启用 decode CUDA graph** — 重放捕获的单 token 步骤，而非承担内核启动开销；在 T4 上实测**2–8 倍**提升，输出 token 完全一致（[#16587](https://github.com/unslothai/unsloth/pull/16587)）。
- **可选启用的可增长 KV 缓存**（`UNSLOTH_GROWABLE_KV=1`）— 避免 `StaticCache` 为每个全注意力层预分配 `prompt + max_new_tokens`。默认关闭；未设置时行为零变化（[#17296](https://github.com/unslothai/unsloth/pull/17296)）。
- **Studio / WebKit 长对话性能系列** — 多个 PR 针对菜单/对话框/浏览器面板打开成本随对话长度增长的问题：通过消除每帧线程工作和重新布局，150 轮基准从约 460–560 ms 降至短对话水平（[#17282](https://github.com/unslothai/unsloth/pull/17282)、[#17284](https://github.com/unslothai/unsloth/pull/17284)、[#17323](https://github.com/unslothai/unsloth/pull/17323)）。
- **MCP 工具循环低效** — 启用大量 MCP 工具时，GGUF 对话几乎每条消息都重新处理整个 prompt；开放中的 PR 保留延迟工具定义（[#17326](https://github.com/unslothai/unsloth/pull/17326)，追踪自 [#17015](https://github.com/unslothai/unsloth/issues/17015)）。

## 5. 稳定性与回归

按服务型工作负载的严重程度排序：

1. **自定义 OpenAI 兼容连接（vLLM/OMLX/llama.cpp/Ollama）下视频上传被拒绝**，即使后端支持视频（[#17295](https://github.com/unslothai/unsloth/issues/17295)）。修复 PR 已开放：[#17322](https://github.com/unslothai/unsloth/pull/17322)。
2. **Linux 被强制从 GPU 降级到 CPU** — 已关闭，但对受影响者而言是硬性降级（[#17235](https://github.com/unslothai/unsloth/issues/17235)）。
3. **Studio 网页抓取的 HTML→Markdown 转换器 bug** — 裸 `&` 后残留 `;`、代码围栏内逃逸内容泄漏、畸形实体导致后续页面内容按原始 HTML 渲染（[#17199](https://github.com/unslothai/unsloth/issues/17199)）。如果你依赖抓取工具的输出质量或将其送入下游解析，此问题值得关注。
4. **托管账户文件夹浏览 403** — 安装为符号链接时 `/api/models/browse-folders` 返回 403；影响多租户 Studio 部署（[#17292](https://github.com/unslothai/unsloth/issues/17292)，修复 PR [#17321](https://github.com/unslothai/unsloth/pull/17321)）。
5. **typer 0.29 导致 Windows 安装失败** — 修复已合并（[#17319](https://github.com/unslothai/unsloth/pull/17319)）。
6. **附件注释解析器误报** — 文件内容形似沙箱附件注释时被解析为第二个附件（[#16913](https://github.com/unslothai/unsloth/issues/16913)，已关闭）。
7. **TRL 1.15 下 CPU SFTTrainer 评估损失错误** — 除以未移位的标签数量，导致首个 token 未被掩码时 `eval_loss` 被低估（[#17291](https://github.com/unslothai/unsloth/pull/17291)）。修复了纯 CPU 微调流程的损失曲线失真问题。
8. **ROCM TunableOp 测试回归** — 自 #17299 起 `main` 上出现回归，修复 PR 已开放（[#17325](https://github.com/unslothai/unsloth/pull/17325)）。

## 6. 对应用开发者的意义

- **Decode 吞吐量即将免费获得实质性提升。** 如果你通过 `FastModel.generate()` 服务 Qwen3.x/Llama/Gemma 级模型，默认启用的 decode graph 应在较旧 GPU（T4 级）上带来多倍 token 速率提升，且输出不变。下个版本发布后值得基准测试你的 p50/p99 延迟；同时，请验证对确定性敏感的评估仍然一致。
- **可增长 KV 缓存的可选启用值得长上下文服务关注。** 固定 `StaticCache` 的过度分配一直是并发长 prompt 工作负载的真实瓶颈；如果你运行高 `max_new_tokens` 但实际生成量不大，发布后可试用 `UNSLOTH_GROWABLE_KV=1`。
- **目前还不要信任"自定义 OpenAI 兼容"的多模态能力** — 视频在客户端就被拒绝，根本到不了 vLLM/Ollama。在修复落地前，请锁定到修复 PR 或在本地模型加载上验证。
- **Studio 正在积累一个真正的 OpenAI 兼容网关表面**（自定义端点、MCP 工具、网页抓取、托管账户）。该层的 bug 密度——权限模式仅限会话（[#17297](https://github.com/unslothai/unsloth/issues/17297)）、偏好同步缺口（[#16912](https://github.com/unslothai/unsloth/issues/16912)）、MCP prompt 重复处理——表明它目前仍在成熟过程中，尚未达到可用于生产级 agent 路由的加固程度。
- **如果你在桌面上做图像 LoRA 或大规模语料索引**，资源管理仍需手动：项目索引没有暂停/限流机制，可能饿死推理（[#16992](https://github.com/unslothai/unsloth/issues/16992)，已关闭），图像 LoRA 训练也缺少可复用的设置配置（[#16979](https://github.com/unslothai/unsloth/issues/16979)，已关闭）。

</details>

---
*本日报由 [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar) 自动生成。*