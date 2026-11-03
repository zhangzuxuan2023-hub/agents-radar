# AI 基础设施日报 2026-11-03

> 生成时间: 2026-11-03 10:36 UTC | 覆盖项目: 6 个

- [vLLM](https://github.com/vllm-project/vllm)
- [SGLang](https://github.com/sgl-project/sglang)
- [llama.cpp](https://github.com/ggml-org/llama.cpp)
- [Ollama](https://github.com/ollama/ollama)
- [LiteLLM](https://github.com/BerriAI/litellm)
- [Unsloth](https://github.com/unslothai/unsloth)

---

## 横向对比

# AI 基础设施生态 —— 跨项目对比
**摘要日期：2026-11-03**

---

## 1. 生态概览

当前生态处于**正确性整合阶段**，而非功能扩张阶段：主流服务引擎（vLLM、SGLang）正在追求批次不变性、确定性和特定硬件的稳定性，同时逐步落地增量模型支持；llama.cpp 则以高频率发布滚动式逐提交构建（24 小时内 10 个构建）。路线图活动由两项结构性迁移主导：vLLM（#44280）和 LiteLLM（#31263、#49606）的 **Rust 前端**，以及 SGLang 的 `api.v1` rust-server 迁移（#45816）。硬件碎片化现已成为主要风险来源——vLLM 中的 SM120/GB10 Blackwell 怪异问题、SGLang 中的 NVFP4 MoE 崩溃、llama.cpp 中的 oneAPI/SYCL 与 170-SM 分块缺口。与此同时，网关层（LiteLLM）正从请求路由转向**集群控制平面**功能，微调层（Unsloth）则大举投入服务相邻的用户体验（MLX 加载延迟、prompt cache）——这印证了"训练工具"与"推理服务器"的边界正在模糊。

---

## 2. 活跃度对比

| 项目 | 层级 | 发布（24 小时） | Issue 活跃度 | PR 活跃度 | 显著体量信号 |
|---|---|---|---|---|---|
| **vLLM** | 服务引擎 | 无 | 极高——批次不变性 #27433 达 104 条评论；多个严重硬件回归处于开启状态 | 活跃——针对 SM120（#65356）、CPU `KeyError`（#65365）的修复 PR 已开启 | DeepSeek-V4 集群横跨多个版本；确定性缺陷涉及 3+ 个模型家族 |
| **SGLang** | 服务引擎 | **v0.5.23**（1007 个 PR，257 位贡献者） | 高——3 个"高"严重度开启（#47916、#42510、#48108）；CI 追踪 #26340（353 条评论）、#42752（166 条评论） | 非常活跃——9 个 PR 的修复批次；rust-server 迁移 | 本次评审的所有项目中单日变更面最大 |
| **llama.cpp** | 本地运行时 | **10 个构建**（b12054–b12066） | 中等——SYCL #31064 开启；RPC 安全 PR 审查中（#31592、#31584） | 高——统一 KV 重构（#29510）、Clover 架构（#31306）、NCCL TP（#28967） | 滚动发布节奏；后端分布广泛（WebGPU、Hexagon、CANN、Vulkan、zDNN） |
| **Ollama** | 本地运行时 / UX | **v0.40.14**（补丁） | 中等——严重调度器死锁 #17408 开启；OpenAI 兼容性正确性长尾 | 中等——6+ 个修复 PR 开启（#19401、#18691、#19211） | 兼容层修复 PR 与 Issue 比例健康；核心调度器缺口未修复 |
| **LiteLLM** | 网关 | 无 | 高——流式推理缺陷（#49795、#30765/#25214）、限流器（#49675） | 极高——9 个 PR 的控制平面栈（#49654–#49662）、Rust 开关（#49606/#49607） | 破坏性变更进行中：#49783（`prompt_cache_breakpoint` → Claude `cache_control`） |
| **Unsloth** | 微调 | 无 | 高但**偏向 Studio**——17 倍 prompt-cache 回归（#16538）最为突出 | 活跃——MLX 大修（#16536、#16517）、NF4 扩展（#16066）、MoE PEFT 修复（#15159） | Issue 构成从核心训练转向桌面/Web UI |

**解读表格：** SGLang 的变更原始吞吐量最高（发布 + 迁移 + 修复批次）；llama.cpp 发布节奏最高；vLLM 和 LiteLLM 的*风险密度*最高（严重开启 Issue + 进行中的破坏性变更）。Ollama 最为"稳态"——一个补丁加一条长长的兼容性长尾。

---

## 3. 模型支持竞赛

| 新模型 / 架构 | vLLM | SGLang | llama.cpp | Ollama | Unsloth |
|---|---|---|---|---|---|
| **DeepSeek-V4.x / V4.1-Flash** | ⚠️ 部分——LoRA 已合入（#57245）；SM120 启动修复开启（#65356）；CPU 在 v0.30–v0.33 损坏（#65365） | ✅ FP4 indexer（含 HBM 注意事项 #39441） | — | — | — |
| **Gemma 4（26B-A4B）** | ✅ MI350P MoE 调优（#65134）；llama.cpp 生态中 MTP 崩溃 #30698 | — | ⚠️ MTP+mmproj 中止（#30698） | — | — |
| **Qwen3.5 / 3.8 / Next** | ✅ MI300X 上的 FLA（#41446）；非确定性缺陷（#54521） | ✅ MTP 分片选择（#44702）；Flash-Next MI355X 追踪（#44304） | ✅ Flash-Next 生成提速 2%（#31443） | ⚠️ 0.8B `q4_K_M` 缺失（#19436） | ✅ Qwen3 NF4 解码（#16066） |
| **MiniMax-M2.7 / M3.1** | ⚠️ NVFP4 TP=2 挂起（#41725） | ✅ 统一缓存 NVFP4 链接器（#48125） | — | — | — |
| **GLM-5.3-Flash** | — | ✅ SM90 优化追踪（#45373）；MambaPool OOM（#47916） | ⚠️ Metal 解码停滞（#29867） | — | — |
| **Muse Spark Open / Beam-1.0** | — | ✅ v0.5.23 cookbook | — | — | — |
| **Clover / xing4_0（新架构）** | — | — | ✅ 两者均已落地（#31306、#31589） | — | — |
| **LFM3** | — | — | — | ⚠️ 分词器缺陷已关闭（#18785） | ❌ 请求已关闭（#15588） |

**谁领先：**
- **SGLang** 在广度上领先——它是唯一对 Muse Spark Open 和 Beam-1.0 提供首日 cookbook 覆盖的项目，也是唯一为 MiniMax-M3.1 交付统一缓存 NVFP4 的项目。
- **llama.cpp** 是*架构实验室*——它在一天内交付了两个全新架构（Clover、带 MLA/MoE/MTP 的 xing4_0），仅在必要时增加真正的 ggml-op 面。
- **vLLM** 在**硬件验证**支持上领先——其 DeepSeek-V4.1/ROCm/Gemma-MI350P 工作按模型而言更深入，但在这些模型于 SM120 上的发布阻断缺陷上落后。
- **Unsloth** 在**微调时**家族覆盖上领先（Qwen2/2.5/3、Mistral NF4 融合解码）。
- **显著缺口：** DeepSeek-V4.x 是当今各生态中最*脆弱*的前沿模型——每个引擎都有一个开启或刚修复的 DeepSeek Issue。

---

## 4. 性能前沿

优化工作集中在五个方向，项目专业化明显：

| 方向 | vLLM | SGLang | llama.cpp | Unsloth / LiteLLM |
|---|---|---|---|---|
| **KV 缓存** | TurboQuant OSCAR 2–3 bit 预设（#65354）；前缀缓存确定性缺陷 | 基数部分驱逐（#48096）——保留长设备前缀 | 统一 KV `flash_attn_ext_rows`（#29510）；`get_n_kv_rows()` 修复（#31570） | MLX 注意力内存估算（#16517） |
| **批处理 / 调度** | 批次不变性路线图（#27433——104 条评论） | 按预计 token 负载进行 PD 放置（#48098）；解码准入核算（#48037） | 多序列统一注意力（批处理服务） | — |
| **量化** | 2–3 bit KV；ROCm INT4 QuickReduce（#57547） | Quark MXFP4 GEMM 选择（#43360）；NPU 上的 INT8 LightningIndexer（#42224） | WebGPU `ARANGE`；Vulkan K-quant MulMat 转置布局（#22970） | 融合 NF4 batch-1 解码（#16066，可选启用） |
| **分布式服务** | MoE 专家卸载 RFC（#38256） | 自适应投机解码（#23705）；分离式准入 | **通过 RPC 的 NCCL TP（#28967）**；Vulkan TP allreduce（#25051） | — |
| **内核 / 硬件** | MFMA 自动调优 gfx942（#62909）；FlyDSL all-reduce 融合 | SM90/GLM-5.3、gfx1250 分页 MLADecode、gfx1151 split-K GEMV、Hexagon HVX、CANN FA 修复 | stream-k 分块（#30784——170-SM 卡上 prefill 差距 10–13%）；Vulkan coopmat | cuDNN SDPA 守卫（#15934） |

**前沿观察：**
1. **AMD 是新的战场。** vLLM 和 SGLang 都有专门的 MI300X/MI350P/gfx1151 优化线；llama.cpp 有 HIP/CDNA2（但 gfx90a 上有构建中断——#30869）。ROCm 正从"受支持"走向"受优化"，但稳定性落后于 NVIDIA。
2. **KV 缓存压缩正趋同于 4-bit 以下。** vLLM 的 136–200 B/token/KV-head（对比 4-bit 的 262）和 Unsloth 的 MLX 估算削减都瞄准同一个 256K 上下文经济性问题。
3. **投机解码是当前的活跃批处理杠杆**——但它也是当今最大的正确性隐患（见 §6）。
4. **多节点 TP 正在到来**于本地运行时（llama.cpp NCCL/RPC）——这历史上是服务引擎独有的能力。

---

## 5. 层级定位

| 层级 | 项目 | 核心价值 | 当前风险画像 |
|---|---|---|---|
| **高吞吐服务引擎** | vLLM、SGLang | 数据中心 GPU 上的最大吞吐/延迟；PagedAttention/MoE。**SGLang** 偏重调度 + 投机深度自适应；**vLLM** 偏重内核/硬件广度 + 批次不变性。 | 前沿硬件（SM120、GB10、NVFP4）上有严重开启回归。SGLang 的 MambaPool OOM（#47916）是架构性的，而非一次性缺陷。 |
| **本地 / 边缘运行时** | llama.cpp、Ollama | 跨 10+ 后端的可移植性；最小依赖；UX。llama.cpp 是后端套娃；Ollama 是 llama.cpp 之上的 UX 外壳。 | llama.cpp：滚动构建意味着需要 bisect 纪律。Ollama：调度器死锁（#17408）是一个**核心活性**缺陷——所有摘要中最严重的单项。 |
| **网关 / 控制平面** | LiteLLM | 跨提供商翻译、路由、预算、限流、可观测性。当前：**向上层移动**进入集群管理（控制平面、RBAC、OCSF 审计），并向下层移动进入 Rust 转发（#31263）。 | 推理模型流式脆弱（#49795、#30765）；预算核算在并发下漂移（#49675）。破坏性变更即将到来（#49783）。 |
| **微调** | Unsloth | 内存高效的 LoRA/QLoRA；融合内核；Apple silicon 的 MLX 路径。当前：**扩展进入服务**（Studio 的 llama-server、prompt cache、MLX 推理）。 | 服务功能继承了服务级缺陷：17 倍 prompt-cache 失效（#16538）和静默数据损坏（#16528）对任何将 Studio 用作前端的人而言都很严重。 |

**关键结构性洞见：** 各层正在**趋同**。LiteLLM 正在网关之上构建控制平面；Unsloth 正在微调之下构建服务 UI；llama.cpp 正在增加 TP（一项服务引擎能力）。未来两个季度，预期看到多层产品（例如"网关 + 控制平面"或"微调 + 服务"），而非单层工具。

---

## 6. 趋势信号

### T1. 确定性是新的性能指标
vLLM 的批次不变性路线图（#27433）及其一组 temperature=0 非确定性缺陷（#54521、#53257、#40896）是**所有六份摘要中评论最多的主题**。SGLang 有同样的问题（DFLASH 下不可复现的贪心解码，#39597）。llama.cpp 历史上一直在 KV 缓存/前缀缓存确定性上挣扎（已关闭：#28527、#29045）。
**行动：** 将逐位精确可复现性视为*你钉住的功能标志*，而非基线。对于评估，运行隔离引擎的钉住请求或非投机解码路径。

### T2. 投机解码当前是正确性负担
与 MTP/EAGLE/DFLASH 相关的崩溃和非确定性报告出现在**六个项目中的四个**：
- vLLM：长序列上的 MTP 非法内存访问（#40756）；TP worker 挂起（#41530）
- SGLang：EAGLE/MTP 启动 OOM（#42510）；缺少 KVIndexTranslator（#38029）；DFLASH 非确定性（#39597）
- llama.cpp：Gemma 4 + MTP 中止（#30698）
- Unsloth：流式推理前缀破坏回退（#49795——属 LiteLLM，但同一模型级原因）

**行动：** 如果在生产环境启用投机解码，请验证 `num_spec_tokens`、钉住引擎版本，并测试流中途故障注入。

### T3. Rust 前端遍地开花
vLLM（#44280）、SGLang（#45816）和 LiteLLM（#31263、#49606）都有活跃的 Rust 迁移。动机一致：**亚 1ms 网关/前端开销，以及面向多租户路由的内存安全并发模型**。这也为任何在 OpenAI 兼容线以下集成的人制造了内部 API 断裂面。

**行动：** 如果你在 OpenAI 兼容面*以下*调用 vLLM/SGLang/LiteLLM（gRPC、路由器内部、Python 服务器 API），立即开始清点——这些是首先迁移的组件。

### T4. 硬件碎片化税正在上升
SM120（RTX PRO 6000）和 GB10/DGX Spark 导致 vLLM 最严重的报告；SGLang 中 Blackwell 上预热时 NVFP4 MoE 崩溃（#48108）；llama.cpp 中 5 个不同后端有开启或最近关闭的 Issue（SYCL/oneAPI、HIP/gfx90a、Vulkan、CANN、Metal）。**性能天花板现在由资格最弱的设备定义，而非最快的设备。**

**行动：** 谨慎配置 Blackwell。对于工作站级（SM120/GB10），在推广前按驱动/矩阵运行金丝雀部署。对于 AMD，服务优先选 gfx942/gfx950；gfx90a（MI210）有开启的 llama.cpp 构建中断。

### T5. 网关层正成为平台
LiteLLM 的 9 个 PR 控制平面栈为 Splunk/Sentinel/S3 增加了 JWT 认证、作用域 RBAC、扇出聚合和 OCSF 审计——这将"网关"转化为"AI 访问控制平面"。结合 Rust 迁移和即将到来的破坏性 `prompt_cache_breakpoint`→`cache_control` 变更，**LiteLLM 正处于快速升级跑步机上。**

**行动：** 如果你依赖 LiteLLM 的翻译面，每季度预算一个升级周期，并针对你的推理模型工作流做回归测试。关注 PR #49783 的破坏性迁移说明。

### T6. 服务正爬入微调，反之亦然
Unsloth 今天最大的项目（#16536 MLX 加载延迟、#16538 prompt-cache 回归、#16509 PyTorch/Llama 回退）是**服务问题，而非训练问题**。反过来，服务引擎正在增加微调相邻功能（vLLM 的 LoRA 适配器支持，包括多模态，#57245；面向内存受限 LoRA 训练的 MoE 专家卸载）。"在此训练 / 在彼服务"的边界正在消融。

**行动：** 评估"本地微调 + 本地服务"栈的技术决策者应*同时*评估 Unsloth 的 Studio 与 vLLM/SGLang——而非分开评估——并为其拥有自己的服务级缺陷面做预算。

### T7. 本地运行时正成为分布式运行时
llama.cpp 将 NCCL 集合通信接入 RPC 服务器（#28967）以及 Vulkan TP allreduce（自旋等待 / SFD 信号量，#25051）。这是多节点边缘服务的前兆。结合 Ollama 的 Rockchip NPU 后端 PR（#19480），"本地"层正分裂为**边缘加速**和**多节点**两个子类别。

**行动：** 设计部署拓扑时考虑最终的多节点本地运行时——尤其是如果你目前使用 llama.cpp RPC 做 2-GPU 服务。

---

## 决策者要点

| 如果你是…… | 今天采用 | 观望 / 关注 |
|---|---|---|
| **NVIDIA 上的数据中心服务** | SGLang v0.5.23 用于新模型覆盖；vLLM 配合按模型版本钉住 | vLLM SM120/GB10 部署；两者上的非确定性 temperature=0 路径 |
| **AMD 上的数据中心服务** | 两个项目的 MI300X/MI350P 路径都在积极调优 | ROCm DCP（#38709，SGLang）；gfx90a 构建（llama.cpp #30869） |
| **网关 / 多提供商** | LiteLLM（接受升级节奏） | 推理模型流式路径（#30765、#49795）、预算核算（#49179、#49675） |
| **本地 / 边缘** | llama.cpp 用于后端广度，Ollama 用于 UX | 高并发下的 Ollama 调度器死锁（#17408）；llama.cpp 上的 SM120 级工作站 |
| **单机微调 + 服务** | Apple 上用 MLX 的 Unsloth，或 PEFT + offload_layers（带 #15159 补丁） | 任何 MCP 或 prompt-cache 密集的场景用 Unsloth Studio（#16538）；S3 音频清单静默风险（#16528） |

**总体判断：** 2026-11-03 的格局有利于**保守的升级纪律**而非激进采用。本集合中的每个项目都至少有一个影响生产相关路径的开启"高"严重度项目，而两个最具结构性的变更（Rust 前端、控制平面）仍在进行中。收益——更低比特 KV、动态投机深度、专家卸载、NCCL TP——是真实的，但每项都必须单独采用，配合版本钉住和回滚计划。

---

## 各项目详细报告

<details>
<summary><strong>vLLM</strong> — <a href="https://github.com/vllm-project/vllm">vllm-project/vllm</a></summary>

# vLLM 摘要 — 2026-11-03

## 1. 今日亮点
过去 24 小时内无新版本发布，但 issue 跟踪器被 **batch-invariance 与确定性工作**占据： #27433（104 条评论）追踪一项雄心勃勃的功能/性能工作，目标是实现完全 batch-invariant 推理；同时，在 DeepSeek-V4、Qwen3.8-Flash 和 prefix-caching 路径上仍有多项 temperature=0 非确定性 bug 未关闭。 **SM120（RTX PRO 6000 Blackwell）** 和 **GB10/DGX Spark** 上的硬件特定不稳定问题继续产生最严重的回归报告，包括 DeepSeek-V4.1-Flash 启动失败和解码吞吐量骤降。长期进行的 **Rust 前端对齐**路线图（#44280）也仍在活跃，表明对 Python API 服务器替代路径的持续投入。

## 2. 版本发布与破坏性变更
过去 24 小时内无。跟踪提示：PR #65365 表明，在 CPU 上运行 DeepSeek-V4 时，在 **从 v0.30.0 到 v0.33.0 的每个版本**中，首个解码步骤都会抛出 `KeyError: 'c1a'` —— 升级计划应等待修复。 [PR #65365](https://github.com/vllm-project/vllm/pull/65365)

## 3. 新模型与硬件支持
- **SM120 上的 DeepSeek-V4.1-Flash**：启动失败修复 PR 允许 sparse-MLA 和 indexer 后端使用 64 页 block size。 [PR #65356](https://github.com/vllm-project/vllm/pull/65356)
- **DeepSeek-V4.1 LoRA**：语言模型 adapter 支持，包括多模态输入（已关闭）。 [PR #57245](https://github.com/vllm-project/vllm/pull/57245)
- **AMD MI350P（gfx950）上的 Gemma 4 26B-A4B**：新增 fused MoE Triton 配置。 [PR #65134](https://github.com/vllm-project/vllm/pull/65134)
- **MI300X（gfx942）上的 Qwen3.5 FLA kernels**：GatedDeltaNet prefill 优化，已在 Qwen3.5-122B-A10B-FP8 上验证。 [PR #41446](https://github.com/vllm-project/vllm/pull/41446)
- **Parakeet-TDT ASR** 请求仍开放，社区兴趣浓厚（46 👍）。 [Issue #23943](https://github.com/vllm-project/vllm/issues/23943)

## 4. 性能与优化
- 提出 **TurboQuant OSCAR KV 预设**（2–3 bit K/V 编码，136–200 B/token/KV-head，而 turboquant_4bit 为 262 B）。 [PR #65354](https://github.com/vllm-project/vllm/pull/65354)
- **MI350P 上的 Gemma 4 MoE**：调优带来峰值吞吐量 +2.9%。 [PR #65134](https://github.com/vllm-project/vllm/pull/65134)
- **ROCm all-reduce + RMSNorm 融合**：FlyDSL INT4 QuickReduce 路径（需要 AITER ≥0.1.27）。 [PR #57547](https://github.com/vllm-project/vllm/pull/57547)
- **gfx942 上的 GDN prefill**：为两个最慢的 kernel 提供 16×16 MFMA autotune 配置。 [PR #62909](https://github.com/vllm-project/vllm/pull/62909)
- **MoE expert offloading** RFC（GPU 缓存 + 异步流水线，LFRU 驱逐）进行中。 [Issue #38256](https://github.com/vllm-project/vllm/issues/38256)
- **DeepSeek-V4.1-Flash 的 SM120 解码吞吐量**被要求作为优先修复项（目前需要 `--enforce-eager`，CUDA graphs 不可用）。 [Issue #56892](https://github.com/vllm-project/vllm/issues/56892)

## 5. 稳定性与回归（按严重程度排序）
1. **SM120 启动失败** —— DeepSeek-V4.1-Flash 在 RTX PRO 6000 Blackwell 上初始化失败；修复 PR 已开放。 [PR #65356](https://github.com/vllm-project/vllm/pull/65356) / [Issue #56892](https://github.com/vllm-project/vllm/issues/56892)
2. **DeepSeek-V4 CPU `KeyError: 'c1a'`** —— 影响 v0.30.0–v0.33.0 的所有版本；修复 PR 已开放。 [PR #65365](https://github.com/vllm-project/vllm/pull/65365)
3. **MTP 投机解码在长序列上非法内存访问**（Qwen3.6-27B-FP8，v0.19.1）。 [Issue #40756](https://github.com/vllm-project/vllm/issues/40756)
4. **GB10/SM12.x 挂起**，使用 `cudagraph_mode=FULL_AND_PIECEWISE` + chunked prefill，约 6 个请求后发生（DeepSeek-V4-Flash）；在 MiniMax-M2.7-NVFP4 TP=2 上反复出现。 [Issue #40969](https://github.com/vllm-project/vllm/issues/40969), [Issue #41725](https://github.com/vllm-project/vllm/issues/41725)
5. **temperature=0 时的非确定性**：Qwen3.8-Flash-Next QSA prefill（#54521）、DeepSeek-V4-Flash 随并发扩展（#53257）、prefix-caching 首次请求分歧（#40896、#5543）。注意 LoRA+MRV2 KV-sharing fast prefill 的修复 PR（已关闭）。 [PR #61162](https://github.com/vllm-project/vllm/pull/61162)
6. **TP worker 挂起 → EngineDeadError**（DeepSeek-V4-Pro，TP=8，MTP）。 [Issue #41530](https://github.com/vllm-project/vllm/issues/41530)
7. **SM<90 上使用 torch.compile/CUDA graphs 时 batch invariance 被破坏**。 [Issue #39096](https://github.com/vllm-project/vllm/issues/39096)

## 6. 这对应用开发者意味着什么
- **目前，在 Qwen3.8-Flash-Next、DeepSeek-V4-Flash 和 prefix-cached 工作负载上，若需要严格可复现性，应避免使用 temperature=0** —— batch-invariance 路线图（#27433）仍在进行但尚未完成。在确定性至关重要的场景，应将请求固定到隔离的引擎，或等待修复。
- **Blackwell 工作站用户（SM120）** 应暂缓使用 DeepSeek-V4.1-Flash，并在 #65356/SM120 graph capture 工作落地前验证 CUDA graph 模式；DGX Spark/GB10 部署应仔细测试 cudagraph 模式。
- **长上下文投机解码**（MTP + FP8）在长序列上存在崩溃风险 —— 在生产前验证 `num_spec_tokens` 设置。
- MRV2 上的 **KV/prefix caching + LoRA** 曾出现正确性/CUDA 故障；回退修复已合并关闭，因此可用时应升级。
- **成本/延迟收益即将到来**，来自更低比特 KV（TurboQuant OSCAR）、MoE expert offloading 和 ROCm all-reduce 融合 —— 如果你使用 AMD，值得在 MI300X/MI350P 上做基准测试。

</details>

<details>
<summary><strong>SGLang</strong> — <a href="https://github.com/sgl-project/sglang">sgl-project/sglang</a></summary>

# SGLang 摘要 — 2026-11-03

## 今日亮点

SGLang 发布了 **v0.5.23**，这是一个大型版本，包含来自 257 位贡献者的 1007 个 PR，新增了对 Muse Spark Open 和 Beam-1.0 的模型支持。整个追踪器上的活动显示，围绕 Hopper/RDNA/ROCm 性能路径（GLM-5.3-Flash SM90 调优、gfx1151 GEMM、NPU 上的 INT8 LightningIndexer）有大量工作，同时还有持续不断的正确性修复，涉及内存池化、投机解码和 MoE 路由。rust-server 的 `api.v1` 迁移正在处理器和路由器组件中推进。

---

## 版本发布与破坏性变更

- **v0.5.23** — 1007 个 PR，257 位贡献者。新增 Muse Spark Open（LLM/VLM）和 Beam-1.0（LLM/VLM）的模型 cookbook 条目。[Release](https://github.com/sgl-project/sglang/releases)
- **迁移进行中：** RFC #45816（共享 `api.v1.Generate` 后端）正在将 `sgl-processor` 和 `sgl-router` 迁移到统一的 rust-server API 上——如果你依赖内部路由器/处理器接口，这与你相关。[#45816](https://github.com/sgl-project/sglang/issues/45816)
- **重构：** `TokenizerWorker` 正被并入 `TokenizerManager`；这会改变多 tokenizer IPC 行为和 PD 启动路径。[#48119](https://github.com/sgl-project/sglang/pull/48119)
- **重构：** 调度器等待队列和分块槽位转换为 `RequestList` 对象，简化了中止处理。[#43910](https://github.com/sgl-project/sglang/pull/43910)

## 新模型与硬件支持

- **Muse Spark Open**（LLM/VLM）和 **Beam-1.0**（LLM/VLM）已在 v0.5.23 中加入 cookbook。[Cookbook](https://docs.sglang.io/cookbook/)
- **MiniMax-M3.1** 统一缓存外部链接器支持（NVFP4 池组：KV、indexer、SWA）。[#48125](https://github.com/sgl-project/sglang/pull/48125)
- **Qwen MTP 模型感知分片选择** 在加载器中实现，使共享同一检查点的主模型和草稿模型可以选择不同的分片。[#44702](https://github.com/sgl-project/sglang/pull/44702)
- **AMD gfx1250**：aiter 后端中的分页 Gluon MLA 解码（bf16 Q 对 bf16/FP8 KV）。[#44458](https://github.com/sgl-project/sglang/pull/44458)
- **AMD gfx1151（Strix Halo）**：用于单行解码的 split-K bf16 GEMV 路径。[#47077](https://github.com/sgl-project/sglang/pull/47077)
- **AMD ROCm Quark MXFP4** 稠密 GEMM 选择，通过 `--fp4-gemm-backend aiter|triton` 配置。[#43360](https://github.com/sgl-project/sglang/pull/43360)
- **NPU A2/A3**：用于 DSA 索引的 INT8 LightningIndexer。[#42224](https://github.com/sgl-project/sglang/pull/42224)

## 性能与优化

- **GLM-5.3-Flash SM90（Hopper）优化追踪器** 作为 Q4 内核路线图的一部分开放；相关的 SM120 追踪仍在进行中。[#45373](https://github.com/sgl-project/sglang/issues/45373)
- **自适应投机解码路线图** — 基于观测到的接受率，为智能体/混合工作负载动态调整草稿深度。[#23705](https://github.com/sgl-project/sglang/issues/23705)
- **PD 预填充放置按每 rank 预计 token 负载进行**，以减少多轮对话中的跨 rank 前缀未命中。[#48098](https://github.com/sgl-project/sglang/pull/48098)
- **Radix 缓存部分驱逐** — 可选的尾部块大小保留长设备前缀，而不是丢弃整个叶子。[#48096](https://github.com/sgl-project/sglang/pull/48096)
- **解码准入计数** 在主机暂存传输阶段之间缓存（分离式）。[#48037](https://github.com/sgl-project/sglang/pull/48037)
- **NPU/AMD 性能追踪器**：Qwen3.8-Flash-Next MI355X（gfx950）优化开放。[#44304](https://github.com/sgl-project/sglang/issues/44304)

## 稳定性与回归

按严重程度排序：

1. **高 — 准入突发下的 CUDA OOM：** `MambaPool.copy_from` 将所有源时间状态收集到一个 `(n × state_size)` 临时缓冲区中；在并行准入下，混合 mamba/KDA 模型（如 GLM-5.3-Flash）会发生 OOM。尚无修复 PR。[#47916](https://github.com/sgl-project/sglang/issues/47916)
2. **高 — 使用 EAGLE/MTP 同检查点草稿模型时启动 OOM：** 在 KV 池大小计算期间保留的冗余 `embed_tokens`/`lm_head` 副本导致池大小不足。[#42510](https://github.com/sgl-project/sglang/issues/42510)
3. **高 — Blackwell NVFP4 MoE 预热时崩溃：** 在默认 `--moe-runner-backend auto` 下，ModelOpt NVFP4 出现 `AttributeError: 'g1_scale_c'`。[#48108](https://github.com/sgl-project/sglang/issues/48108)
4. **中 — ROCm DCP 崩溃：** `--dcp-size > 1` 在首次前向传播时使每个 rank 崩溃（`forward_absorb_rocm_core` 解包不匹配）。[#38709](https://github.com/sgl-project/sglang/issues/38709)
5. **中 — ROCm DeepSeek-V4.1 FP4 indexer** 在长上下文 AgentX 下仍然耗尽 HBM，尽管有 #37660。[#39441](https://github.com/sgl-project/sglang/issues/39441)
6. **中 — 不可复现的贪婪解码** 在 DFLASH 投机解码 + FlashInfer 自动调优下跨重启出现。[#39597](https://github.com/sgl-project/sglang/issues/39597)
7. **中 — TRT-LLM MoE 专家记录丢失：** FlashInfer TRT-LLM 路由绕过了专家选择，破坏了分布记录。**修复 PR 已开放：** [#48142](https://github.com/sgl-project/sglang/pull/48142)
8. **中 — 图批捕获崩溃：** `SGLANG_GRAPH_BATCH_CAPTURE` 在 DSA 双图 / LoRA / PDMUX 多图桶下出现 IndexError。[#39092](https://github.com/sgl-project/sglang/issues/39092)
9. **低 — EAGLE DSA 草稿** 在 FP8 MHA 前缀读取中缺少 `KVIndexTranslator`。[#38029](https://github.com/sgl-project/sglang/issues/38029)
10. **低 — qwen_vl：** 文本中的字面 `<image>` 会导致请求以 500 中止（token/数据不匹配）。[#43979](https://github.com/sgl-project/sglang/issues/43979)
11. **低 — DeepGEMM 导入：** 缺少 CUDA 工具包会引发 `AssertionError` 并使模型注册表为空，即使对于非 DeepGEMM 模型也是如此。**修复 PR 已开放：** [#48140](https://github.com/sgl-project/sglang/pull/48140)

CI 健康状况：coredump 追踪器 #26340（353 条评论）和不稳定测试追踪器 #42752（166 条评论）仍然活跃，表明 NVIDIA `PR Test Base`/`Extra` 运行中 CI 持续不稳定。[#26340](https://github.com/sgl-project/sglang/issues/26340) · [#42752](https://github.com/sgl-project/sglang/issues/42752)

## 这对应用开发者意味着什么

- **升级到 v0.5.23** 如果你需要 Muse Spark Open / Beam-1.0 支持；否则，`api.v1` 路由器迁移是你在 OpenAI 兼容接口之下集成时需要注意的主要兼容性事项。
- **智能体/多轮工作负载：** 两个进行中的变更直接针对你的痛点——动态投机深度（#23705）和跨预填充 rank 的前缀保留放置（#48098、#48096）。如果你使用 PD 分离提供长对话服务，请关注这些。
- **混合 mamba 模型：** 在 #47916 修复之前，避免激进的并行准入设置；同样，如果使用同检查点 EAGLE/MTP 草稿模型，请手动设置 KV 池大小（#42510）。
- **Blackwell + NVFP4：** 显式固定 `--moe-runner-backend`，而不是依赖 `auto`，直到 #48108 落地。
- **确定性评估：** 如果你依赖贪婪解码来保证可复现性，请禁用 FlashInfer 自动调优或避免 DFLASH+重启工作流（#39597）。
- **客户端断开连接处理：** 一个修复（#46586）正在推进中，将及时中止孤立的流式请求——这对于取消流的聊天 UI 和网关的成本控制相关。[#46586](https://github.com/sgl-project/sglang/pull/46586)

</details>

<details>
<summary><strong>llama.cpp</strong> — <a href="https://github.com/ggml-org/llama.cpp">ggml-org/llama.cpp</a></summary>

# llama.cpp 摘要 — 2026-11-03

## 1. 今日亮点

llama.cpp 迎来了发布密集的一天，24 小时内落地了十个构建版本（b12054–b12066），其中最引人注目的是针对多序列注意力的统一 KV 性能重构（`flash_attn_ext_rows`，#29510）以及全新的 Clover MoE 架构（#31306）。在缺陷方面，SYCL 后端持续受到关注：一个内存泄漏报告在 21 条评论后关闭（#27845），而一个新的 oneAPI 2026.2.0 段错误仍处于开放状态（#31064）；此外，RTX 5090 上的 CUDA stream-k 分块问题声称造成 10–13% 的长上下文预填充性能损失（#30784）。后端工作在 WebGPU、Hexagon/HVX、CANN（Ascend）和 Vulkan 张量并行方面明显活跃。

## 2. 发布与破坏性变更

发布采用基于每次提交的滚动构建；未宣布正式的 API 破坏，但请注意行为/配置变更：

- **b12066** — WebGPU：新增 `ARANGE` 算子。[发布](https://github.com/ggml-org/llama.cpp/releases/tag/b12066)
- **b12065** — KV 缓存：`get_n_kv_rows()` 对没有 cell 的序列不再返回 0（修复了缓存共享草稿模型——例如 Gemma 4 assistant——在服务器预热时的图构建失败）。如果你使用带共享缓存的推测解码，此项与你相关。[PR #31570](https://github.com/ggml-org/llama.cpp/pull/31570)
- **b12061** — `mtmd`：Pocket TTS 声音，无需声音克隆（声音提示以骨干 KV 状态存储）。[PR #31429](https://github.com/ggml-org/llama.cpp/pull/31429)
- **b12058** — `imatrix`：修复 `--nextn` 崩溃。[PR #31522](https://github.com/ggml-org/llama.cpp/pull/31522)
- **b12057** — Context：简化的 setter 重构（内部 API 清理）。[PR #29730](https://github.com/ggml-org/llama.cpp/pull/29730)
- **b12054** — `ggml-zdnn`：共享主机缓冲区（IBM Z / zDNN 加速器）。[PR #31481](https://github.com/ggml-org/llama.cpp/pull/31481)

迁移说明：如果你直接通过 RPC 构建图（而非通过构建器），请审阅下方的 #31592 和 #31584——两者都加强了视图/参数边界，可能会拒绝此前被接受的畸形图。

## 3. 新模型与硬件支持

- **Clover（新架构）** — 仅自回归的 MoE，采用混合因果注意力，支持 GGUF 转换、加载、LoRA/循环状态复用。[b12060 / PR #31306](https://github.com/ggml-org/llama.cpp/pull/31306)
- **xing4_0** — 架构注册、HF→GGUF 转换，以及 mHC/MLA/MoE/MTP 图；无新增 ggml 算子。[PR #31589](https://github.com/ggml-org/llama.cpp/pull/31589)
- **Pocket TTS（kyutai）** — 通过 `mtmd` 提供预定义声音，无需克隆。[PR #31429](https://github.com/ggml-org/llama.cpp/pull/31429)
- **WebGPU** — `ARANGE` 支持已落地（[b12066](https://github.com/ggml-org/llama.cpp/releases/tag/b12066)）；`MEAN`（F32、连续行、偏移/跨步视图）正在审阅中。[PR #31591](https://github.com/ggml-org/llama.cpp/pull/31591)
- **Hexagon（Qualcomm HTP）** — 针对置换 CONT/CPY 的分块 HVX 转置路径，替代逐元素 DMA reshape 回退方案。[b12064 / PR #31446](https://github.com/ggml-org/llama.cpp/pull/31446)
- **Ascend NPU（CANN）** — Flash Attention NaN 修复：传递 `attenMask`，而非将 `-inf` 塞入 `pseShift`。[PR #31590](https://github.com/ggml-org/llama.cpp/pull/31590)
- **Vulkan** — 在修复后的 AMD 专有（AMDVLK）驱动上重新启用 128×128 coopmat matmul 分块。[PR #31587](https://github.com/ggml-org/llama.cpp/pull/31587)
- **多节点** — 用于张量并行的 NCCL 集合通信，已接入 RPC 服务器（作为 #28967 合并）。[PR #28967](https://github.com/ggml-org/llama.cpp/pull/28967)

## 4. 性能与优化

- **多序列统一 KV** — `flash_attn_ext_rows` 复用 `llm_attn_mask` + `kv_rows`，在跨序列的统一注意力中实现更高效的批处理服务。[b12059 / PR #29510](https://github.com/ggml-org/llama.cpp/pull/29510)
- **RTX 5090 长上下文预填充** — 在 170-SM 部件上，stream-k 块数不是 `ntiles_dst` 的整数倍，相比 RTX 4090 造成 **10–13%** 的性能损失；作者测得修复后可提升 **+37–40%**。开放中，master 上代码未改动。[Issue #30784](https://github.com/ggml-org/llama.cpp/issues/30784)
- **Vulkan 张量并行** — 采用自旋等待（NVIDIA）/ SFD 信号量（AMD）的 allreduce 内核，针对 2 设备优化；环形缓冲和更多设备数将后续跟进。[PR #25051](https://github.com/ggml-org/llama.cpp/pull/25051)
- **Vulkan K-quant 提示处理** — 针对 Q4_K/Q5_K/Q6_K mul_mat 的 A 矩阵转置布局。[PR #22970](https://github.com/ggml-org/llama.cpp/pull/22970)
- **Qwen3.8-Flash-Next** — 卷积状态尾部写入无需中间 `cont`，**生成性能提升约 2%**，已在 CUDA/CPU/meta 上验证。[PR #31443](https://github.com/ggml-org/llama.cpp/pull/31443)
- **CUDA Q8_1 校正求和** — 使量化输入求和与整数点积及 CPU 行为对齐（精度问题，间歇性 Q4_1 mul_mat）。已关闭。[PR #31247](https://github.com/ggml-org/llama.cpp/pull/31247)
- **Windows 模型加载** — 无缓冲（等同 DirectIO）读取正在审阅中；请求跨设备测试。[PR #26014](https://github.com/ggml-org/llama.cpp/pull/26014)

## 5. 稳定性与回归

按严重程度排序：

1. **CUDA fatbin 致命错误** — `fattn.cu:579` 在 `GGML_CUDA_FA_ALL_QUANTS` + NCCL 下出现构建/运行时失败。开放中，无修复 PR。[Issue #24324](https://github.com/ggml-org/llama.cpp/issues/24324)
2. **oneAPI 2026.2.0 上的 SYCL 段错误** — Arc B580 + 5700X3D 崩溃。开放中。[Issue #31064](https://github.com/ggml-org/llama.cpp/issues/31064)
3. **Gemma 4 + `--mmproj` + MTP 启动中止** — `GGML_ASSERT(ubatch->embd_state)`，由 #30257 引入的回归。开放中，对多模态 + 推测解码用户影响重大。[Issue #30698](https://github.com/ggml-org/llama.cpp/issues/30698)
4. **Gemma 4 MTP 崩溃** — 因过期而关闭；如果你依赖 MTP，请留意。[Issue #25522](https://github.com/ggml-org/llama.cpp/issues/25522)
5. **Metal 解码停滞，GLM-5.3-Flash** — 融合 Lightning Indexer 回退到 CPU，解码停滞。开放中。[Issue #29867](https://github.com/ggml-org/llama.cpp/issues/29867)
6. **Vulkan `--moe-cache-mib` 加载崩溃** — RX 6900 XT / Windows；已关闭。[Issue #30697](https://github.com/ggml-org/llama.cpp/issues/30697)
7. **HIP/CDNA2 构建失败（gfx90a）** — MI210 编译失败。开放中。[Issue #30869](https://github.com/ggml-org/llama.cpp/issues/30869)
8. **Vulkan 构建中断（GCC 16/clang 22）** — 自 #27512 起 `signbit`/`isnan`/`isinf` 未声明；已关闭。[Issue #31445](https://github.com/ggml-org/llama.cpp/issues/31445)
9. **Granite SWA 逐层 RoPE theta 被忽略** — 今日无已发布检查点受影响，但对未来的 `granite_swa` 权重是个陷阱。[Issue #31585](https://github.com/ggml-org/llama.cpp/issues/31585)
10. **KV 缓存 / 前缀缓存确定性** — 量化 K 缓存在重复前缀命中时产生不同输出（已关闭）；iSWA `seq_pos_max` 在部分 `seq_rm` 后出现 cell 泄漏（已关闭）。[#28527](https://github.com/ggml-org/llama.cpp/issues/28527)、[#29045](https://github.com/ggml-org/llama.cpp/issues/29045)
11. **审阅中的安全相关加固** — `ggml_argmax` 在未检查 `src0->ne[1]` 时发生越界写入，可通过伪造 RPC 图触达（[PR #31592](https://github.com/ggml-org/llama.cpp/pull/31592)）；按跨步可达范围检查视图边界（[PR #31584](https://github.com/ggml-org/llama.cpp/pull/31584)）；`ggml_roll` 边界中的有符号溢出（[PR #30375](https://github.com/ggml-org/llama.cpp/pull/30375)）。**如果你暴露 RPC 或接受客户端构建的图，请优先处理这些。**

长期存在的服务器运行时间投诉再次浮现（Vulkan、RX 7900 XTX、随时间变慢、因过期关闭）：[#22360](https://github.com/ggml-org/llama.cpp/issues/22360)。

## 6. 对应用开发者的意义

- **批处理/多智能体服务成本更低：** #29510 + #31570 共同使跨序列的统一 KV 更高效，并消除了预热期的图构建失败——对于带共享前缀草稿模型的多会话服务器，可以放心采用。
- **MoE 和混合注意力仍是一等公民：** Clover 和 xing4_0（MLA/MoE/MTP 图）落地时无需新增算子，意味着现有的量化和 LoRA 工具应可适用——但早期架构支持需谨慎，生产环境请固定构建版本，并自行测试 GGUF 转换。
- **不要假定 RTX 5090 最快：** stream-k 分块差距（#30784）是长上下文下真实可测的预填充回归；如果你要配置 170-SM 显卡，请逐 GPU 基准测试。已有补丁提出但尚未合并。
- **多模态 + 推测解码存在已知中止问题**（#30698）：如果你发布带 `--mmproj` 和 `-md` 的 Gemma 4，请避开受影响的提交范围，或在 CI 中显式测试启动。
- **面向 RPC 的部署：** 三个边界检查 PR（#31592、#31584、#30375）解决了可通过构造图触达的越界/未定义行为。请跟踪这些——通过 RPC 审查不受信任的图输入是实际风险。
- **多节点 GPU 即将到来：** 通过 RPC 的 NCCL 集合通信（#28967）和 Vulkan allreduce（#25051）表明张量并行服务正从 2-GPU 向多节点演进；设计部署拓扑时请考虑这一趋势。
- **前端方面：** 内嵌服务器 UI 日趋成熟（PDF 缩略图、提供商/模型管理、下载状态修复见 [#31467](https://github.com/ggml-org/llama.cpp/pull/31467)、[#31468](https://github.com/ggml-org/llama.cpp/pull/31468)、[#31545](https://github.com/ggml-org/llama.cpp/pull/31545)）——如果你原本打算自建控制台，请先评估它再动手。

</details>

<details>
<summary><strong>Ollama</strong> — <a href="https://github.com/ollama/ollama">ollama/ollama</a></summary>

# Ollama 摘要 — 2026-11-03

## 1. 今日亮点
Ollama 发布了 **v0.40.14**，这是一个小型补丁版本，修复了 `ollama launch claude` 针对云端模型的输出 token 限制问题，修复了 `/api/chat` 上 gzip 压缩的云端网络搜索响应的解码问题，并收紧了 OpenAI 兼容端点的行为。围绕此版本的活动分为两部分：一方面是 **关键调度器死锁**（#17408）在 `main` 上仍未修复，另一方面是一长串 OpenAI 兼容性正确性问题——包括工具结果中包含图像时产生误导性 400 错误（#19479），以及兼容层中不稳定的响应 ID。硬件覆盖范围略有扩展，有一个进行中的 Rockchip NPU 后端 PR，而 0.40.13 中的 Pascal/CUDA 回归问题似乎已被分诊。

## 2. 版本发布与破坏性变更
- **v0.40.14 发布** — 补丁版本。变更内容：
  - `ollama launch claude` 现在会应用模型的输出 token 限制，因此云端模型在 Claude Code 中不再触发 128k 的默认上限。
  - `/api/chat` 上的云端网络搜索响应现在能正确解码，包括压缩/gzip 响应。
  - OpenAI 兼容端点已调整（发布说明被截断；请针对你的客户端验证行为）。
  - 未指示需要配置/迁移步骤。

## 3. 新模型与硬件支持
- **Rockchip NPU 后端（提议中）** — PR #19480 为 Linux 上的 RK3588/RK3576 SoC 添加了 `rocket`/`rknpu` 后端，将预填充（`MUL_MAT`、`MUL_MAT_ID`、flash attention）卸载到 NPU，同时将解码保留在 CPU 上。早期阶段；尚未合入。https://github.com/ollama/ollama/pull/19480
- **Qwen3.8-27B 混合注意力 GGUF 加载失败（已关闭）** — 从 `huihui-ai` 的 GGUF 导入 `qwen3next` 架构时失败，第 64 层缺少 `attn_qkv`/`attn_gate` 投影。Issue 已关闭，但没有关联的修复 PR；在依赖此架构导入路径之前，应验证解决状态。https://github.com/ollama/ollama/issues/19459
- **Qwen3.5 0.8B Q4_K_M 请求（开放中）** — 这是唯一缺少 `q4_K_M` 标签的 Qwen3.5 变体；CPU 受限的用户请求提供该版本，以获得约 2 倍于 q8 的吞吐量。https://github.com/ollama/ollama/issues/19436

## 4. 性能与优化
- **日志详细程度过滤（开放 PR）** — #17913 在未启用调试时过滤每请求的 `llama-server` 日志，目前每个请求约有 20 行槽位记账信息流入 journald。对繁忙服务器上的日志量和 I/O 有意义，但不影响吞吐量。https://github.com/ollama/ollama/pull/17913
- **上下文长度报告（开放 PR）** — #19211 使 `/api/tags` 报告 `min(native, OLLAMA_CONTEXT_LENGTH)`，以便客户端不再假设服务器不会兑现的上下文窗口。防止静默截断和客户端缓冲区大小不匹配。https://github.com/ollama/ollama/pull/19211
- **VRAM 过度分配（开放中，长期存在）** — #10128 报告 Ollama 在双 GPU 配置上为 Qwen2.5 7B @ 262k 上下文使用约 10GB 显存，配置为 `OLLAMA_KV_CACHE_TYPE=q8_0` 并启用 flash attention——即 VRAM 估算相对于配置的 KV 缓存严重偏低。对上下文密集型服务 relevant。https://github.com/ollama/ollama/issues/10128

## 5. 稳定性与回归（按严重程度排序）
1. **调度器死锁（开放中，关键）** — #17408：当驱逐目标在加载过程中被并发请求复活时，待处理循环永久阻塞，*所有*后续加载请求都会挂起。报告于 0.31.1；据称 `main` 上的逻辑未变。没有关联的修复 PR。这是今天对多租户或高并发部署最高严重性的问题。https://github.com/ollama/ollama/issues/17408
2. **0.40.13 中的 Pascal/CUDA 回归（已关闭）** — #19485：NVIDIA Pascal GPU 上 `ggml-cuda.cu` 出现 CUDA 错误。同日关闭；0.40.14 可能是缓解措施——在 Pascal 硬件上验证后再推广。https://github.com/ollama/ollama/issues/19485
3. **GPU 上视觉推理 500（开放中）** — #19482/#19483：`qwen2.5vl:3b` 在 NVIDIA GPU 上运行视觉编码器时返回 HTTP 500 "prediction aborted, token repeat limit reached"；纯文本 GPU 和全视觉 CPU 可正常工作。重复提交表明存在真实的编码器/GPU 路径问题。https://github.com/ollama/ollama/issues/19482
4. **OpenAI 兼容：工具结果中的图像被错误解析（开放中）** — #19479：带有图像部分的 `role: "tool"` 消息尽管 ID 配对正确，仍产生误导性的 400 "Unexpected tool call id … in tool results"。阻碍了通过 `/v1` 的多模态工具使用。https://github.com/ollama/ollama/issues/19479
5. **兼容响应 ID 冲突（开放 PR）** — #19401：`chatcmpl-%d` 等从 `rand.Intn(999)` 取值——仅 999 个不同 ID。在大规模下破坏客户端去重/追踪。已有修复 PR。https://github.com/ollama/ollama/pull/19401
6. **`top_p` 覆盖（开放 PR）** — #18691：省略的 `top_p` 被强制设为 1.0，覆盖了 Modelfile 中的 `PARAMETER top_p`。与之前的 `temperature` 修复属于同一类 bug。https://github.com/ollama/ollama/pull/18691
7. **401 认证详情丢失（开放中）** — #19489：当 401 响应体不是有效 JSON 时，`AuthorizationError`/`SigninURL` 丢失——云端认证失败表现为不透明错误。https://github.com/ollama/ollama/issues/19489
8. **macOS 更新器循环（开放中）** — #19472：应用为已安装的版本提供"重启以更新"，并要求管理员权限来 chown `/Applications/Ollama.app`。已有修复 PR #19487 和 #19488。https://github.com/ollama/ollama/issues/19472
9. **`HumanNumber` 舍入溢出（开放中）** — #19490 / PR #19486：999500 在 `/api/tags` 参数大小和 `ollama list` 输出中渲染为 "1000K" 而非 "1M"。外观问题但对客户端可见。https://github.com/ollama/ollama/pull/19486
10. **ROCm `SOLVE_TRI` 失败（已关闭）** — #15343：Qwen 模型在 RX 9060 XT 上因 `hipErrorInvalidDeviceFunction` 失败。约 7 个月后关闭；ROCm 用户应在当前版本上重新测试。
11. **分词器边缘情况（已关闭）** — #18785：`lfm2:24b` 静默从输出中丢弃裸 `"python"` token（无前导空格）。已关闭；如果你服务 LFM2，请在当前构建中验证修复。

## 6. 对应用开发者的意义
- **通过 Claude Code 的云端现在遵守模型输出限制。** 如果你之前通过 `ollama launch claude` 出现截断或触及 128k 上限，请升级到 0.40.14。`/api/chat` 上的云端网络搜索响应现在也能可靠解码——对 RAG/agent 搜索循环安全。
- **避免在负载下假设调度器活性。** 在 #17408 修复之前，如果你运行高并发工作负载且存在内存压力和驱逐（大模型、紧张 VRAM、大量并发用户），考虑在应用层串行化/排队加载请求，或固定模型（`OLLAMA_KEEP_ALIVE` 设长、预加载）以完全避开驱逐路径。
- **OpenAI 兼容层有你会踩到的粗糙边缘。** 带图像的工具结果目前返回 400（#19479）；响应 ID 在大规模下冲突（#19401）；省略的 `top_p`/`temperature` 会覆盖你的 Modelfile 默认值（#18691、#17763）。在客户端中固定这些预期，并显式设置采样参数而非依赖默认值。
- **对 `/api/tags` 的 `details.context_length` 保持怀疑**，直到 #19211 合入——当 `OLLAMA_CONTEXT_LENGTH` 设置为低于原生值时，API 会过度报告。
- **Pascal GPU：** 0.40.13 有问题；采用 0.40.14 并验证。**ROCm 和 Vulkan 用户：** 仅 Vulkan 的机器仍会获得 CUDA 膨胀且没有 Vulkan 后端（#19474）——这对 Intel/AMD iGPU 集群是一个缺口。
- **模型可用性：** 如果你需要在 CPU 上运行 Qwen3.5 0.8B，`q4_K_M` 标签尚不存在（#19436）——从 HuggingFace 导入或投票支持。

---
*来源：github.com/ollama/ollama — 发布 v0.40.14，issues #17408、#10128、#19461、#18785、#19459、#19350、#19485、#15343、#19482、#19483、#19436、#19473、#19490、#19489、#19479、#19474、#19472；PRs #19233、#19488、#19487、#19211、#19486、#19484、#18691、#19481、#19480、#19478、#19476、#19477、#18990、#19153、#19460、#19470、#19475、#17913、#19401。*

</details>

<details>
<summary><strong>LiteLLM</strong> — <a href="https://github.com/BerriAI/litellm">BerriAI/litellm</a></summary>

# LiteLLM 摘要 — 2026-11-03

## 1. 今日亮点

今日的主导主题是网关的 Rust 迁移：父工单 #31263 的讨论持续升温（33 条评论，22 个 👍），同时一批协同 PR（#49606、#49607）落地了可选的 Rust 转发监督机制和 Helm 开关。与此同时，BerriAI 正在推进一个包含九个 PR 的“控制平面”栈（#49654–#49662），新增全局舰队管理 API，具备 JWT 认证、作用域 RBAC、扇出聚合，以及将 OCSF 审计日志输出到 Splunk/Sentinel/S3。过去 24 小时无新版本发布；issue 队列活跃，涉及翻译层流式传输 bug 和限流器计量问题，已有若干修复进入审查。

## 2. 版本发布与破坏性变更

过去 24 小时无新版本发布。

值得关注的进行中破坏性变更：
- **PR #49783** — `feat(anthropic)!:` 将 OpenAI 的 `prompt_cache_breakpoint` 映射到 Claude 的 `cache_control`，覆盖 Anthropic、Bedrock、Vertex AI 和 Azure AI。标记为破坏性变更；根据摘要，Claude 无法识别的标记将采取与当前不同的处理方式。https://github.com/BerriAI/litellm/pull/49783

配置面变更（可选启用，非破坏性）：
- **PR #49606** 新增 `LITELLM_RUST=1`，在公共端口上监督一个可选启用的 Rust 转发服务器，同时 Python 继续处理请求。https://github.com/BerriAI/litellm/pull/49606
- **PR #49607** 通过两个 chart 中的 Helm values 暴露同一开关；未设置时保留镜像默认值。https://github.com/BerriAI/litellm/pull/49607

## 3. 新模型与硬件支持

- **PR #39195** — 新增 SLNG 作为 TTS/STT 提供商（13 个 TTS + 8 个 STT 模型），面向欧盟/澳大利亚/区域基础设施。https://github.com/BerriAI/litellm/pull/39195
- **Issue #20064**（开放中，11 条评论）— 功能请求：在 `config.yaml` 中基于通配符的模型发现（`my-custom-service/*`），以支持自定义提供商而无需逐一枚举每个模型。https://github.com/BerriAI/litellm/issues/20064
- **Issue #33670**（已关闭）— 请求支持 OpenCode Zen 和 Agnes-AI 提供商端点以及每模型多密钥支持；根据摘要，未落地即关闭。https://github.com/BerriAI/litellm/issues/33670

## 4. 性能与优化

- **Rust 网关迁移（#31263）** — 父工单仍开放，讨论活跃。关联博客描述了将网关开销降至亚 1ms 的目标；正在招募早期 beta 测试组。这是该项目主要的延迟降低方向。https://github.com/BerriAI/litellm/issues/31263
- **控制平面扇出（PR #49656）** — 引入跨数据平面的有界 scatter/gather，具备逐后端故障隔离，因此单个数据平面宕机不再导致整个舰队视图失败。对需要跨多个代理聚合用量/模型/团队的运维人员很有价值。https://github.com/BerriAI/litellm/pull/49656
- 过去 24 小时**未发布具体基准测试数据**。

## 5. 稳定性与回归

按严重程度排序：

1. **仅推理前缀导致流式回退丢失（#49795，开放中）** — `sent_first_chunk` 在任何 delta（包括推理内容）上都会翻转，因此思考块期间的流中途失败会被误报为已收到首块之后，即使客户端未收到任何内容也会跳过回退。直接影响路由器后端的高推理强度模型。https://github.com/BerriAI/litellm/issues/49795
2. **v3 限流器 TPM 计数器超出其窗口存活（#49675，开放中）** — 在上游 429 情况下，同时配置 RPM 和 TPM 限制的密钥/团队会在低于其配置的 `tpm_limit` 时被拒绝。影响提供商降级时的准入控制正确性。https://github.com/BerriAI/litellm/issues/49675
3. **`/v1/messages` 流式传输在 thinking→text 转换后丢弃首个文本 delta（#30765，开放中；重复报告 #25214）** — 破坏通过 Anthropic 兼容代理的 Claude Code 和 Bedrock 推理模型流式传输。两份独立报告，未见修复 PR。https://github.com/BerriAI/litellm/issues/30765 · https://github.com/BerriAI/litellm/issues/25214
4. **进行中请求期间 `/key/update` 导致预算窗口支出丢失（#49179，已关闭）** — 窗口总计（`LiteLLM_BudgetWindowSpend`）遗漏进行中请求的成本；`SpendLogs` 和密钥的 `spend` 列正确。2026-11-02 关闭。https://github.com/BerriAI/litellm/issues/49179
5. **路由器回退时 Gemini 3.x 思考签名损坏（#32140，已关闭）** — `vertex_ai` ↔ `gemini` 回退重放端点绑定的签名，导致 400 “Corrupted thought signature.”。2026-11-03 关闭。https://github.com/BerriAI/litellm/issues/32140
6. **`langfuse_otel` 静默丢弃所有追踪（#43032，开放中）** — OTLP 端点构建时缺少 `/v1/traces`，导致自托管 Langfuse 摄取对每个 span 返回 404。可观测性盲点；未引用修复 PR。https://github.com/BerriAI/litellm/issues/43032
7. **`safe_deep_copy`/`safe_dumps` 在并发修改下崩溃（#34471，已关闭）** — 高负载下出现 `dictionary changed size during iteration`。2026-11-03 关闭。https://github.com/BerriAI/litellm/issues/34471
8. **流式 Responses API 将 `flex_unavailable` 映射为 500 而非 429（#49736，开放中）** — 非流式路径正确返回 429；流式路径分类错误，破坏客户端重试/退避逻辑。https://github.com/BerriAI/litellm/issues/49736
9. **代理在快速序列化路径上剥离 `delta.reasoning_items`（#38907，已关闭）** — 加密推理状态仅通过代理 SSE 路径丢失，SDK 不受影响。2026-11-02 关闭。https://github.com/BerriAI/litellm/issues/38907

审查中的修复 PR（尚未合并）：
- **PR #40016** — 防止当 `custom_cost_per_token` 设置 `cache_read_input_token_cost` 时对 Anthropic 缓存读取 token 重复计数。https://github.com/BerriAI/litellm/pull/40016
- **PR #39515** — 阻止空的 `budget_duration` 被静默解释为每日 UTC 午夜重置（此前会每 24 小时清空团队/用户/密钥支出）。https://github.com/BerriAI/litellm/pull/39515
- **PR #39993** — 在 `/key/update` 上将 `budget_duration` 置于管理员检查之后。https://github.com/BerriAI/litellm/pull/39993
- **PR #39962** — 为 Fireworks AI 从消息中剥离 `reasoning_details`。https://github.com/BerriAI/litellm/pull/39962
- **PR #40194** — 将 `drop_params`/`additional_drop_params` 排除在 `/v1/images/edits` multipart 请求体之外，且不污染 `all_litellm_params`。https://github.com/BerriAI/litellm/pull/40194

## 6. 对应用开发者的意义

- **推理模型流式传输是最脆弱之处。** 如果你通过 `/v1/messages` 或回退路由器路由高推理强度模型（Gemini 3.x thinking、Bedrock 推理模型、Claude 扩展思考），预期会出现 thinking 后 delta 丢失（#30765/#25214）以及仅推理前缀上回退被跳过（#49795）。在依赖路由器回退处理 UX 关键路径之前，请用流中途故障注入进行测试。
- **flex 层级的重试逻辑可能被误导。** 命中 `service_tier: "flex"` 的流式客户端会在预期 429 的地方看到 500（#49736）——不要仅依赖基于状态码的退避来处理该路径。
- **OpenAI 形态的应用即将获得 Claude 提示缓存支持。** PR #49783 让 `prompt_cache_breakpoint` 在翻译到 Claude 后端时得以保留；如果你一直传入被静默丢弃的缓存提示，值得关注。该 PR 标记为破坏性变更，升级前请查看迁移说明。
- **自托管 Langfuse 时存在可观测性缺口。** #43032 意味着追踪在导出时被丢弃且不暴露错误——在信任 span 覆盖率之前请验证你的 Langfuse 摄取。
- **多代理运维人员获得舰队级管理路径。** 控制平面栈（#49654–#49662）新增经过认证的跨数据平面调用、RBAC 作用域管理和审计输出到 Splunk/Sentinel/S3——企业许可，通过现有镜像上的 `--control-plane` 标志启用。如果你目前手动跨区域代理扇出，值得关注。
- **预算/限流记账存在已知边界。** 进行中 `/key/update` 竞态（#49179）和 429 下的 TPM 窗口泄漏（#49675）意味着预算执行在并发下可能漂移；计费对账时优先使用逐请求支出日志而非窗口聚合。

</details>

<details>
<summary><strong>Unsloth</strong> — <a href="https://github.com/unslothai/unsloth">unslothai/unsloth</a></summary>

# Unsloth 摘要 — 2026-11-03

## 今日亮点
过去 24 小时内没有新版本发布，但服务端队列异常繁忙：一次大规模的 MLX 后端重构（更快的加载/重载、修正内存核算）以及动态 MCP 加载下混合/循环模型的提示缓存失效回归，是今天最值得关注的两项。训练侧，融合 NF4 batch-1 解码正从 Llama 扩展到 Mistral/Qwen2/Qwen3，MoE + `offload_layers` 的 PEFT 崩溃已有修复在路上。Issue 数量严重偏向 Unsloth Studio（桌面/Web UI），而非核心微调。

## 发布与破坏性变更
过去 24 小时内无新版本发布。

影响 Studio 的 llama-server 集成用户的进行中配置/接口变更：
- **`--chat-template-kwargs` 合并** — Studio 目前先输出托管 kwargs（例如 `reasoning_effort: high`），随后跟用户额外参数，其中包含第二个 `--chat-template-kwargs`，存在重复标志风险。PR #16327 将它们合并为单个标志。 — [PR #16327](https://github.com/unslothai/unsloth/pull/16327)
- **llama-server `-ngl all` / `-ngl auto` 被拒绝** — Studio 对 llama.cpp 本身接受的值返回 HTTP 400。Issue 已关闭；传递自定义启动参数的用户应在其构建上验证行为。 — [Issue #16494](https://github.com/unslothai/unsloth/issues/16494)
- **PyPI 缺少 PyTorch extras** — `cu130-2130` / `cu130-2140` extras 存在于 `pyproject.toml` 中，但未出现在已发布的 `2026.10.11` wheel 中。请求待处理。 — [Issue #16520](https://github.com/unslothai/unsloth/issues/16520)

## 新模型与硬件支持
- **LFM3 系列（LiquidAI）** — LFM3 支持的功能请求已关闭；诉求是 1.7B 骨干。 — [Issue #15588](https://github.com/unslothai/unsloth/issues/15588)
- **RX 5700 XT 上的 Vulkan（Windows 10 22H2）** — 未解决的 bug；Vulkan 后端在此配置上不可用。若你面向 Windows 上的消费级 AMD 硬件，此项相关。 — [Issue #16395](https://github.com/unslothai/unsloth/issues/16395)
- **DGX Spark 指南** — 未解决的问题：已发布的 DGX Spark 微调指南在九月 Studio 更新 / 通用 Docker 容器之后是否仍然适用。 — [Issue #16342](https://github.com/unslothai/unsloth/issues/16342)

## 性能与优化
- **MLX 加载/重载延迟（PR #16536）** — 4B 模型加载即使页面缓存已热仍需约 38 秒；根因是服务器与 worker 之间串行的 Hub 往返以及配对开销。目标是"加载完成即就绪服务"。 — [PR #16536](https://github.com/unslothai/unsloth/pull/16536)
- **MLX 注意力内存估算（PR #16517）** — 估算假设每个长预填充块都物化完整 score 张量；改用 mlx 的融合内核后，Qwen3.5-4B @ 262K 的估算从约 38 GB 下降（源中已截断）。依赖 unsloth-zoo #2367。 — [PR #16517](https://github.com/unslothai/unsloth/pull/16517)
- **融合 NF4 batch-1 解码（PR #16066）** — 可选启用 `UNSLOTH_DECODE_FUSED_NF4=1`，从 Llama 扩展到 Mistral、Qwen2/Qwen2.5（q/k/v 偏置在融合 qkv 路径中处理）以及 Qwen3。堆叠于 #16032 之上。 — [PR #16066](https://github.com/unslothai/unsloth/pull/16066)
- **cuDNN SDPA 防护（PR #15934）** — 在 torch < 2.14/2.15 上将解码和 NaN-dQ 调用形状排除在 cuDNN 注意力之外，以规避上游 pytorch#193893（注意力输出静默错误）和 #196678（查询梯度 NaN）。 — [PR #15934](https://github.com/unslothai/unsloth/pull/15934)
- **本地 GGUF 模型的并行网页读取（PR #16489）** — 三次 3 秒的页面抓取此前串行化至约 10 秒；现在重叠执行，与云模型行为一致。 — [PR #16489](https://github.com/unslothai/unsloth/pull/16489)

## 稳定性与回归
按严重程度排序：

1. **混合/循环模型上动态 MCP 加载导致提示缓存失效 — 17 倍减速**（未解决，无修复 PR）。今日对任何在启用 MCP 工具下服务混合架构的用户影响最大。 — [Issue #16538](https://github.com/unslothai/unsloth/issues/16538)
2. **训练数据静默损坏：S3 音频清单相对路径冲突**（未解决）。清单相对引用 `clips/a.wav` 可能解析到前缀根下的*不同*文件；转录保持不变，因此你会得到错误的训练对且无任何错误。修复 PR 拒绝有歧义的引用。 — [Issue #16528](https://github.com/unslothai/unsloth/issues/16528) / [PR #16529](https://github.com/unslothai/unsloth/pull/16529)
3. **MoE + `offload_layers` 上 `get_peft_model` 崩溃**（未解决，有修复 PR）。被驱逐的专家权重在 PEFT 适配器创建期间表现为空张量；PR 暴露原始形状并恢复 offload 状态。 — [PR #15159](https://github.com/unslothai/unsloth/pull/15159)
4. **Studio：生成期间加载的语义不一致**（未解决）。卸载除非 `force_cancel_active` 否则以 409 拒绝；加载则静默取消运行中的对话。相关的 Windows 11 / RTX 50 系列上误导性的"GPU 内存未释放"错误（已关闭）。 — [Issue #16479](https://github.com/unslothai/unsloth/issues/16479), [Issue #16436](https://github.com/unslothai/unsloth/issues/16436)
5. **流水线并行 fp16 + 融合优化器：GradScaler 跳过判断不一致**（已关闭，被取代）。融合 SGD/Adam/AdamW 从不调用 `unscale_`，因此每阶段的 `found_inf` all-reduce 从不触发。 — [PR #16351](https://github.com/unslothai/unsloth/pull/16351)
6. **MLX CI 自 unsloth-zoo #2365 起持续红**（已关闭，已修复）。Stub 缺少 `_FIRST_USE_WORK` 导入，导致每个 PR 上的 `test_mlx_inference_backend.py` 失败。 — [PR #16537](https://github.com/unslothai/unsloth/pull/16537)
7. **超长请求触发 19–55 GB 的 PyTorch Clef 下载**（PR 未解决）。在 Decision API 设为 Auto 时，超出 llama.cpp 上下文的请求回退到 PyTorch Clef 而未检查磁盘驻留，驱逐 llama.cpp 并让 PyTorch 服务后续请求。PR 将回退限制在先前的下载之上。 — [PR #16509](https://github.com/unslothai/unsloth/pull/16509)
8. **带图像的对话在关闭标签页时丢失后台生成**（PR 未解决）。纯文本对话此前已行为正确。 — [PR #16491](https://github.com/unslothai/unsloth/pull/16491)
9. **设置仅持久化在浏览器 `localStorage`**（已关闭）。外观/系统/安全/浏览器设置会按源/浏览器静默重置，而非写入服务端存储。 — [Issue #16309](https://github.com/unslothai/unsloth/issues/16309)

较低严重程度，仅 UI/UX：已发送消息缩进被剥离（[PR #16495](https://github.com/unslothai/unsloth/pull/16495)）、TTS 朗读 emoji 描述（[Issue #16472](https://github.com/unslothai/unsloth/issues/16472)）、标题重新生成忽略对话语言（[Issue #16411](https://github.com/unslothai/unsloth/issues/16411)）、Accounting 格式的电子表格单元格被索引为原始值（[PR #16001](https://github.com/unslothai/unsloth/pull/16001)）。

## 这对应用开发者意味着什么
- **如果你在 Studio 中启用 MCP 工具服务混合/循环模型（Mamba、Jamba、Qwen3-Next 级），预计缓存命中率会严重退化**，直到 #16538 解决。在启用 MCP 前后测量 TTFT；这是 17 倍级回归，不是舍入误差。
- **从 S3 拉取音频的训练流水线应立即审计清单路径。** #16528 中的冲突静默失败——无异常、无转录不匹配，只是错误的训练对。固定到 PR #16529 或强制使用绝对/唯一前缀，直到合并。
- **使用 `offload_layers` 的 MoE 微调目前在 PEFT 适配器加载上已损坏。** 如果你在受限 VRAM 上训练融合专家 MoE，要么等待 #15159，要么避免此组合；失败模式是在 `get_peft_model` 处崩溃，至少是显式的。
- **融合 NF4 解码正在扩展但仍为可选**（`UNSLOTH_DECODE_FUSED_NF4=1`）。值得在 Mistral/Qwen2.5/Qwen3 batch-1 服务上基准测试；Llama 拥有它更久，新系列复用相同注意力路径并增加了偏置处理。
- **PyPI 上的 Torch extras 滞后。** 如果你的 Docker 构建从索引安装 `unsloth[cu130-2130]`，你很可能静默获得与预期不同的 torch——请显式安装 torch 并固定版本。
- **Apple 芯片上的 MLX 目前是积极优化的路径。** #16536 和 #16517 共同针对两大抱怨（冷加载延迟、内存估算悲观）；它们落地后重新测试长上下文（256K）工作负载的容量规划，因为此前估算保守了数 GB。

</details>

---
*本日报由 [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar) 自动生成。*