# AI 基础设施日报 2026-11-05

> 生成时间: 2026-11-04 23:22 UTC | 覆盖项目: 6 个

- [vLLM](https://github.com/vllm-project/vllm)
- [SGLang](https://github.com/sgl-project/sglang)
- [llama.cpp](https://github.com/ggml-org/llama.cpp)
- [Ollama](https://github.com/ollama/ollama)
- [LiteLLM](https://github.com/BerriAI/litellm)
- [Unsloth](https://github.com/unslothai/unsloth)

---

## 横向对比

# 跨项目对比报告 — AI 基础设施文摘
**日期：2026-11-05** | 面向基础设施工程师与技术决策者

---

## 1. 生态概览

当前格局呈现出一个处于张力之中的双层市场。高吞吐服务引擎（vLLM、SGLang）正处于**加固阶段**：没有发布新版本，但成熟度正受到严重正确性缺陷的考验——P/D 分离架构中的跨请求 KV 污染、Mooncake 路径中静默的 KV 页步长损坏，以及多模型发布标签回归。与此同时，本地/边缘运行时（llama.cpp）发布了其 **v0.9.0 内核级版本**（flash_attn_ext_rows、FP8 E4M3），而网关（LiteLLM）与训练（Unsloth）层则分别聚焦于**计费/流式正确性**与**CUDA 图解码加速**。贯穿全部五个项目的共同主线是：**推理模型行为以及 P/D + 缓存基础设施正是故障集中的地方**，而对于生产部署而言，正确性——而非原始吞吐——才是主导性风险。

---

## 2. 活动对比

| 项目 | 层级 | Issue 活动 | PR 活动 | 发布状态（24 小时） |
|---|---|---|---|---|
| **vLLM** | 服务引擎 | 高——以 DeepSeek-V4/V4.1、Qwen3.8 引入为主；1 个 CRITICAL 未修复缺陷 | 活跃——性能 PR（KV 校验和、Triton 向量化）、模型组合 | **无发布。** v0.32.0 被标记为有风险（三重回归） |
| **SGLang** | 服务引擎 | 高（61 条更新） | 极高（500 条更新）——PR 吞吐量最大 | **无发布。** FlashInfer 0.7.3 迁移进行中（破坏性） |
| **llama.cpp** | 本地运行时 | 中等——Intel GPU 回归集群 | 活跃——后端调优（OpenCL、Vulkan、HIP） | **v0.9.0 已发布**（b12110+）：FP8 E4M3、`flash_attn_ext_rows`、4 种架构 |
| **Ollama** | 本地运行时 / 分发 | 高——v0.40.13/14 上的工具调用 + API 正确性回归 | 中等——工具结果处理、SSE 刷新的修复 PR | **无发布。** CUDA v13 语法崩溃（Xid 79）是最高严重级别的未决项 |
| **LiteLLM** | 网关 | 极高——流式/计费正确性占主导 | 活跃——9 个 PR 的“控制平面”技术栈正在评审中 | **3 个发布：** v1.108.0（稳定）、v1.106.7（补丁）、v1.109.0-rc.1——均经 cosign 签名 |
| **Unsloth** | 微调 / 训练 | 中等——桌面/Studio 回归 | 活跃——CUDA 图解码、多 GPU 扩散 | **v0.1.915-beta**（Qwen3.8-Flash-Next、Glass 主题） |

**解读：** LiteLLM 发布最活跃（3 个发布，均以签名为门槛）；llama.cpp 与 Unsloth 发布了有意义的版本升级；两个重量级服务引擎则毫无发布——两者都在消化破坏性迁移或修复关键正确性缺陷。**SGLang 的 PR 数量（500）远超其他所有项目**，表明进行中的重构异常繁重。

---

## 3. 模型支持竞赛

| 模型 / 架构 | vLLM | SGLang | llama.cpp | Ollama | Unsloth |
|---|---|---|---|---|---|
| **DeepSeek-V4 / V4.1** | ✅ 引入进行中（但缺少 SM8x；v0.32.0 上 block-size 损坏） | ✅ TRTLLM 稀疏 MLA、FP8 KV 布局 | — | — | — |
| **Qwen3.8 系列（2.4T-A95B、Flash-Next）** | ✅ AMD gfx950 跟踪器 + 优化 | ✅ HiCache 备份（含已知缺陷 #48145） | — | — | ✅ **Flash-Next 已进入 v0.1.915-beta** |
| **Kimi K3.1 Pro / K3** | ✅ 广泛的 PR（ROCm、投机解码、Rust、Mooncake/NIXL） | ✅ PCP KV 分片 + HiCache + DFlash | — | — | — |
| **GLM-5.2 / 5.3** | ✅ 优化跟踪器 | ✅ 可中断 prefill CUDA 图 | — | — | — |
| **Mistral Large 4** | — | — | ✅ **v0.9.0** | — | — |
| **Clover / Limite / Metis-1.6** | — | — | ✅ **v0.9.0**（4 种架构） | — | — |
| **Nemotron 3.5 ASR Streaming** | 🔶 已请求（#47455） | — | — | — | — |
| **Drama-3 TTS** | — | — | ✅ 音频支持（b12116） | — | — |
| **Inception Mercury（扩散 LLM）** | — | — | — | — | ✅ 预设 + 实时流式 |
| **Qwen3 / 3.5（YaRN、RoPE）** | — | — | — | ✅ MLX YaRN 缩放 | — |

**谁领先：**
- **服务引擎（vLLM、SGLang）**深陷一场**前沿模型军备竞赛**——两者都在同一批新旗舰模型（DeepSeek-V4、Qwen3.8、Kimi K3、GLM-5.x）上竞速。vLLM 在**硬件后端广度**上领先（ROCm、Rust 前端、Mooncake/NIXL）；SGLang 在**深度架构集成**上领先（TRTLLM 稀疏 MLA、PCP KV 分片）。
- **llama.cpp** 占据了**长尾架构采纳**的细分领域——单个版本中新增 4 种架构 + 音频 + 新的稠密/三值模型。它显然是模型格式覆盖广度上的领先者。
- **Unsloth** 是**微调预设**跟进最快的——针对新的扩散 LLM 与 Qwen3.8-Flash-Next。
- **差距信号：** vLLM 对 DeepSeek-V4 的 Ampere（SM8x）支持仍**未决**（#50576）——这对成本敏感型集群而言是一项有意义的容量规划约束。

---

## 4. 性能前沿

优化努力**集中在 KV 缓存与分离架构**上，内核与量化紧随其后。各层努力分布如下：

**KV 缓存与 P/D 分离（活动最频繁、风险最高）：**
- vLLM：连接器无关的 KV 校验和（#62211）、MooncakeConnector RFC、调度器停顿消除（1.0–1.8s）。
- SGLang：Mooncake 页步长修复、Kimi-K3 PCP KV 分片、HiCache 主机层工作——*但*这里也正是最危险缺陷的所在（静默损坏）。
- llama.cpp：`flash_attn_ext_rows` 用于高效统一 KV 多序列服务——一项基础原语。

**内核与注意力：**
- Triton KV 槽向量化（vLLM #62113）、gfx950 上的 AITER prefill 注意力（MI350X 上 **9.3s → 4.1s**，SGLang）、FP8-Q ASM MLA 验证（P90 ITL **6.77ms → 6.18ms**，SGLang）、Vulkan Intel prefill FA 内核（llama.cpp）。

**CUDA 图与解码循环：**
- Unsloth 的**在 `generate()` 中默认启用 CUDA 图解码**（逐位一致，Qwen3-0.6B 上每步消除约 675 个内核）是当下最被低估的收益——对 T4 级消费硬件而言是免费加速。SGLang 将可中断 prefill CUDA 图扩展至交错 CP prefill。

**量化：**
- llama.cpp **OCP FP8 E4M3**（v0.9.0）+ 面向三值/BitNet 模型的 CUDA TQ2_0。vLLM ModelOpt FP8/NVFP4 权重-only 嵌入 rebase。Unsloth FP8 + int8 对 bf16 ConvRot 分析（质量差距量化为 LPIPS 0.0865）。

**分布式服务与 AMD/ROCm（新兴主题）：**
- 两个服务引擎都在大力投入 **AMD gfx950/MI355X**——vLLM 的 Qwen3.8 AMD 跟踪器与 SGLang 的 AITER/MLA 收益表明 ROCm 正从“受支持”迈向“已优化”。FlashInfer 0.7.3 移除 `MoeAlltoAll` 对 SGLang 的 NVLink 单边全对全而言是一次**强制迁移**。

**净结论：** 优化 ROI 仍然最高的是 **KV/分离架构的数据搬运**——但同一区域也正是当下最严重正确性风险所在。

---

## 5. 层级定位

| 层级 | 项目 | 核心功能 | 主要优化轴 | 风险画像 |
|---|---|---|---|---|
| **服务引擎（集群）** | **vLLM**、**SGLang** | 最大吞吐多租户推理、P/D 分离、前缀缓存 | KV 搬运、批处理、内核融合、大模型引入 | **爆炸半径最大**——单个缺陷（KV 污染、损坏）影响众多租户；关键数据泄漏类别 |
| **本地运行时** | **llama.cpp**、**Ollama** | 单节点/边缘推理、GGUF 分发、消费级硬件 | 架构覆盖、量化格式、后端内核（OpenCL/Vulkan/Hexagon） | 非 NVIDIA 后端上的正确性（Intel Vulkan 非确定性）；Ollama 额外带来**分发/工具**面 |
| **网关** | **LiteLLM** | 多提供商路由、计费、配额、护栏、可观测性 | 用量核算的正确性、流式/回退语义、集群控制平面 | **计费/配额正确性**——静默成本缺口；agent 循环 UX 回归 |
| **训练 / 微调** | **Unsloth** | LoRA/QLoRA 微调、扩散模型训练、预设 | 解码内核捕获、内存占用、检查点兼容性 | **训练数据损坏**（S3 manifest 缺陷）与框架重命名漂移 |

**面向决策者的关键区别：**
- **vLLM 与 SGLang** 是同一层级上相互竞争的替代品——vLLM 在硬件广度与生态（Rust 前端、NIXL）上领先，SGLang 在深度模型专属内核集成与 AMD 确定性上领先。二者之间的选择日益意味着选择一种**发布节奏与缺陷面画像**。
- **llama.cpp 与 Ollama**：llama.cpp 是**引擎**；Ollama 是构建于其上的**分发 + API + UX 层**。Ollama 的许多回归（工具调用、Responses API）是它自身的管道问题，而非 llama.cpp。
- **LiteLLM 是本组中唯一的控制平面层**，也是今天唯一发布稳定版本的项目——反映出一种**不同的成熟阶段**（运营稳定性 vs 前沿模型追逐）。
- **Unsloth 位于上游**（模型创建），并日益与服务（CUDA 图解码、浏览器 agent）重叠——关注这一融合。

---

## 6. 趋势信号 — 值得关注什么

**趋势 1 — P/D 分离在设计上已生产就绪，但在正确性上尚未。**
两个服务引擎在分离式 KV 路径中都有严重、静默失败的缺陷：vLLM 跨请求污染（#61160，数据泄漏类别）与 SGLang Mooncake 步长损坏（#48230）。**关注：** `--no-enable-prefix-caching` 式缓解措施是否会成为标准默认值。*Agent/应用开发者：*若运行 P/D，在 #61160 落地前禁用前缀缓存并审计路由器版本（#31206 dead-prefill）。

**趋势 2 — “发布标签回归”问题十分尖锐。**
vLLM v0.32.0 有三重回归；Ollama v0.40.13/14 破坏了原生工具调用。**关注：** 锁版本纪律。*应用开发者：*最安全的版本往往是**上一个版本**（vLLM v0.31.1 是长上下文 Qwen 的推荐锁定版本）。把锁版本内建到部署中，而不是寄望于运气。

**趋势 3 — 推理模型对流式栈的每一层都构成压力。**
LiteLLM 的流式推理回退缺陷（#49795）、SGLang 的 EAGLE PD 解码损坏，以及 vLLM 的推理解析器空白字符缺陷都源于推理 token 处理。**关注：** SGLang 新的**首答案 token 时间直方图**（#48466）——它将推理延迟与答案延迟分离，是面向推理 SLO 的正确可观测性原语。采纳它。

**趋势 4 — AMD/ROCm 正从“受支持”毕业为“已优化”。**
gfx950/MI355X 出现在 vLLM（Qwen3.8 跟踪器）、SGLang（AITER、MLA）、llama.cpp（HIP fast-math）中。**关注：** 对于受监管/可复现性工作负载，SGLang 的**确定性模式 AITER prefill**值得注意。对于容量规划，AMD 在特定模型上*现在*已是可信的 Hopper/Blackwell 替代方案。

**趋势 5 — 网关 ↔ agent 循环互操作性脆弱。**
LiteLLM 的 `server_tool_use` 当作待处理工具调用（#48384）、丢弃加密推理（#49046），以及 Ollama 的工具模板 JSON 损坏（#19529）都会破坏 Claude-Code 风格的 agent 客户端。**关注：** 工具调用 ID 匹配（Ollama #18767）与显式解析器锁定（SGLang `--tool-call-parser auto` 不可靠，#48172）。*Agent 开发者：***切勿在生产中依赖自动检测**——锁定显式解析器并在客户端侧规范化工具结果。

**趋势 6 — 控制平面整合即将到来。**
LiteLLM 的 9 个 PR 技术栈（数据平面注册表 → OCSF 审计 → SIEM 汇聚 → OTel span）指向**一个控制平面治理多个数据平面**——一个集群管理的未来。目前尚无可用之物，但多集群网关运营者应跟踪 #49655。

**趋势 7 — 边缘仍在持续获得免费性能。**
Unsloth 的逐位一致 CUDA 图解码与 llama.cpp 的 `flash_attn_ext_rows` 都能在消费级/边缘硬件上带来**无需改代码**的收益。*应用开发者：*本地服务正在悄然变快；在为延迟敏感的本地工作负载扩展云容量之前，重新做基准测试。

---

### 面向决策者的底线
- **集群服务：** 在 DeepSeek/长上下文场景下对 vLLM v0.32.0 保持观望；在关键 KV 缺陷关闭前禁用 P/D + 前缀缓存。SGLang 提供更深的 AMD 确定性，但承载更多进行中的破坏性迁移（FlashInfer 0.7.3、`api.v1`）。
- **边缘/本地：** 将 llama.cpp 升级至 v0.9.0（验证 FP8 E4M3）；收紧 `-hf` 量化标签；分类器避免使用 Intel Vulkan coopmat。
- **网关：** v1.108.0 稳定，以 cosign 验证作为契约——但**不要信任已中止流的用量计数**，并锁定模型 ID（移除 EU Haiku 配置是一个现实存在的隐患）。
- **训练：** 在检查点重命名被消化之前锁定 transformers <5.23；审计 S3/音频 manifest 的静默数据损坏。

---

## 各项目详细报告

<details>
<summary><strong>vLLM</strong> — <a href="https://github.com/vllm-project/vllm">vllm-project/vllm</a></summary>

# vLLM 文摘 — 2026-11-05

## 1. 今日亮点
过去 24 小时内没有新版本发布，但 issue 动态主要围绕 DeepSeek-V4/V4.1 和 Qwen3.8 级模型的接入，同时在 SM120（Blackwell）和 P/D 分离部署上出现了若干正确性回归。前缀缓存 P/D 配置中一个可能严重的 KV 缓存跨请求污染 bug（#61160）仍未关闭，而新的性能 PR 则聚焦于 KV 传输校验和 Triton attention 向量化。AMD 继续推进 Qwen3.8 系列的 gfx950/MI355X 优化跟踪。

## 2. 版本发布与破坏性变更
过去 24 小时无新版本发布。开放事项中的相关兼容性说明：
- **v0.32.0 回归三连**：DeepSeek-V4.1 在发布标签上的 block-size 失败（#59203）、Qwen4Exp 262K 预填充 CUDA 非法内存访问（在 v0.31.1 上正常，#62383），以及 SM120 拒绝 `enable_adaptive_verification`（#63343）——使用 v0.31.x 的用户可能希望暂缓升级，等待修复。
- 修复 wheel 中缺失 `vllm/utils/numa_wrapper.sh` 的 PR——在无 git 的构建中，NUMA 绑定启动会在初始化时中止（[#65756](https://github.com/vllm-project/vllm/pull/65756)）。
- 默认 CUDA 构建从 13.0 迁移至 13.2，以匹配锁定的 torch 2.13.0 wheel（[#55387](https://github.com/vllm-project/vllm/pull/55387)）。

## 3. 新模型与硬件支持
- **DeepSeek-V4-Flash / -0731 的 SM8x（A100/A800、RTX 30xx）支持**——今日最热 issue（128 条评论）；目前两个 checkpoint 均无法在 Ampere 上运行（[#50576](https://github.com/vllm-project/vllm/issues/50576)）。
- **Kimi K3.1 Pro** 模型 PR 已开启，标签覆盖面非常广（ROCm、投机解码、KV 连接器、Rust 前端、Mooncake/NIXL）（[#65685](https://github.com/vllm-project/vllm/pull/65685)）。
- **RDNA 3.5（gfx115x）混合 W4A16 MoE 后端**——融合 HIP 解码 + Triton 预填充（[#65754](https://github.com/vllm-project/vllm/pull/65754)）。
- 请求支持 **NVIDIA Nemotron 3.5 ASR Streaming（0.6B）**（[#47455](https://github.com/vllm-project/vllm/issues/47455)）。
- **ModelOpt FP8/NVFP4 纯权重 embedding** 加载 PR 已 rebase（[#42791](https://github.com/vllm-project/vllm/pull/42791)）。
- **Rust 前端 tokenizer 一致性测试**，覆盖 8 个 tokenizer / 35 个 recipe，并固定了两处分歧（[#65757](https://github.com/vllm-project/vllm/pull/65757)）。

## 4. 性能与优化
- **GLM-5.3 优化跟踪**（与 5.2 相同的 `glm_moe_dsa` 基础）（[#57406](https://github.com/vllm-project/vllm/issues/57406)）。
- **Qwen3.8-2.4T-A95B 和 Qwen3.8-Flash-Next 的 gfx950/MI355X** 优化计划以堆叠 PR 形式在 AMD 上落地（[#57149](https://github.com/vllm-project/vllm/issues/57149)、[#59575](https://github.com/vllm-project/vllm/issues/59575)）。
- 据报告，**Hopper/Blackwell 上的 batch-invariant 模式**会将 Linear 层路由到 Triton，尽管 cuBLAS 在该模式下已经是 batch-invariant 的——这是可避免的性能损失（[#65569](https://github.com/vllm-project/vllm/issues/65569)）。
- **16 字节对齐的 per-token-head KV 槽位**，使 Triton 在 `TRITON_ATTN` 中能够向量化加载；已在 RTX 3080 上针对 int8/fp8 路径测量（[#62113](https://github.com/vllm-project/vllm/pull/62113)）。
- **与连接器无关的 KV 校验和**，用于 P/D 分离——生产者/消费者校验和匹配，不匹配则视为 KV 加载失败（[#62211](https://github.com/vllm-project/vllm/pull/62211)）。
- **调度器**：通过队列前部重新准入，消除已完成的异步 KV 拉取导致的 1.0–1.8s 重新排队停顿（[#62532](https://github.com/vllm-project/vllm/issues/62532)）。
- **MooncakeConnector RFC**——非 CUDA 平台（RDMA/TCP/Ascend）上的主要 P/D 路径（[#60523](https://github.com/vllm-project/vllm/issues/60523)）。

## 5. 稳定性与回归
按严重程度排序：
1. **严重 — 跨请求上下文污染**，出现在 PD 分离 + 自动前缀缓存（DeepSeek-V4.1-Flash）中：响应间歇性包含其他请求的内容；有可脚本化的复现用例；规避方法为 `--no-enable-prefix-caching`（[#61160](https://github.com/vllm-project/vllm/issues/61160)）。
2. **高 — Qwen4Exp 262K 单次预填充 CUDA 非法内存访问**，在 v0.32.0 和 nightly 上出现，在 v0.31.1 上正常（[#62383](https://github.com/vllm-project/vllm/issues/62383)）。
3. **高 — v0.32.0 上 DeepSeek-V4.1 无可用 block size**（64 和 128 均失败）；修复跟踪于 #60762（[#59203](https://github.com/vllm-project/vllm/issues/59203)）。
4. **中 — DeepSeek-V4 的 DSpark 草稿 0% 接受率**，原因是缺少 `packed_modules_mapping`；融合层忽略了 `ignored_layers`（[#65618](https://github.com/vllm-project/vllm/issues/65618)）。
5. **中 — SM120 拒绝 `enable_adaptive_verification`**（device-query-lens 仅限 SM90/SM100）（[#63343](https://github.com/vllm-project/vllm/issues/63343)）。
6. **中 — Qwen3.6 GDN 贪心 DFlash2 在抢占/重算下的 batch-invariance 漂移**（[#62851](https://github.com/vllm-project/vllm/issues/62851)）。
7. **低–中 — Qwen3.5/Qwen3-Next GDN attention 在 warmup 时 torch.compile stride 不匹配崩溃**（#29014 的复发）（[#50046](https://github.com/vllm-project/vllm/issues/50046)）。
8. **低 — `--reasoning-parser qwen3`** 在剥离 `<think>` 后会在内容中留下前导 `\n\n`（[#61972](https://github.com/vllm-project/vllm/issues/61972)）。
9. **已修复/已关闭 — 非流式客户端断开未被检测**；引擎运行至 `max_tokens`，token 被计为已交付（[#61918](https://github.com/vllm-project/vllm/issues/61918)）。
10. **已关闭 — 修复多模态 dummy 输入针对 mm 限制的校验**（Pixtral 仅 embedding 启动）的 PR（[#65689](https://github.com/vllm-project/vllm/pull/65689)）。

## 6. 对应用开发者的意义
- **如果你运行带前缀缓存的 P/D 分离，请在 #61160 修复前禁用它。** 跨请求串扰属于数据泄露类 bug，而不仅仅是质量问题。
- **对 DeepSeek-V4 和长上下文 Qwen 部署，谨慎对待 v0.32.0**；对于 262K 预填充场景，v0.31.1 是更安全的固定版本，且发布标签上的 block-size 配置已损坏。
- **Ampere 用户不要期待 DeepSeek-V4-Flash**，直到 #50576 落地——请改为在 Hopper/Blackwell 或 AMD MI355X 上规划容量。
- **batch-invariant 模式在 Hopper/Blackwell 上存在可避免的性能税**；如果你依赖它进行可复现评测，请先与默认配置做基准对比，再假定它是免费的。
- **KV 校验和（#62211）** 将为多节点 P/D 传输提供正确性兜底——如果你运维 NIXL/Mooncake 拓扑，值得跟踪。
- **仅记录 Request-ID（#64654）** 仍待处理；如果你需要在不记录内容的情况下进行网关到引擎的关联，目前尚未原生支持。
- **较小的收益**：wheel 打包修复（#65756）在你从源码构建 vLLM 且没有 `.git` 时很重要；一旦有修复版本发布，请固定到该版本。

</details>

<details>
<summary><strong>SGLang</strong> — <a href="https://github.com/sgl-project/sglang">sgl-project/sglang</a></summary>

# SGLang 摘要 — 2026-11-05

## 1. 今日亮点

过去 24 小时内没有新版本发布，但 issue 和 PR 活动依然活跃（61 个更新的 issue，500 个更新的 PR）。主要主题是 **PD 分离正确性**（一个 router 熔断器 bug、一个 Mooncake KV page stride 不匹配、一个 EAGLE 在 retraction 后解码损坏）以及通过 AITER 和 FlashInfer 0.7.3 进行的 **AMD/Hopper kernel 调优**。在基础设施方面，CI flakes 追踪器（#42752，目前 176 条评论）和 Q4 PD 分离路线图（#43699）仍是最活跃的协调线程。

## 2. 版本发布与破坏性变更

今日无新版本发布。两项具有迁移影响的前瞻性事项：

- **RFC：完整迁移到共享的 `api.v1` 后端 API**（[#45816](https://github.com/sgl-project/sglang/issues/45816)）—— 将 `sgl-processor`、`sgl-router` 和 Rust 服务器迁移到统一的 `api.v1.Generate` schema。预计 router/processor 路径会出现接口变动。
- **FlashInfer 升级至 0.7.3 + `flashinfer.moe_ep` 迁移**（[#48468](https://github.com/sgl-project/sglang/pull/48468)）—— 0.7.3 移除了 `flashinfer.comm.MoeAlltoAll`，因此升级和 NVLink 单边 all-to-all 迁移必须一起落地。如果你基于 FlashInfer 的 MoE 通信 API 构建，此项与你相关。

## 3. 新模型与硬件支持

- **Kimi-K3.1 PCP KV 分片，配合 HiCache + DFlash**（[#48469](https://github.com/sgl-project/sglang/pull/48469)）—— 将持久化 DSA KV 页跨 rank 分片而非复制，为 TRTLLM DSA attention 组装。
- **Muse Spark Open cookbook：TPU v7**（[#48277](https://github.com/sgl-project/sglang/pull/48277)）—— 单主机，4 芯片 / 8 TensorCore，`--tp-size 8` 通过 SGLang-TorchTPU 服务 NVFP4。
- **GLM-5.2 / GLM-5.3（`GlmMoeDsaForCausalLM`）**：可中断 prefill CUDA graph 在交织上下文并行 prefill 下扩展，配合 fp8 KV + trtllm kernel（[#46890](https://github.com/sgl-project/sglang/pull/46890)）。
- **DeepSeek-V4 TRTLLM 稀疏 MLA**：调用方拥有的 multi-CTA KV 计数器缓冲区，替代私有的 FlashInfer workaround（[#48467](https://github.com/sgl-project/sglang/pull/48467)）；这也解锁了 PD 分离 uniform-FP8 KV 布局请求（[#37838](https://github.com/sgl-project/sglang/issues/37838)，现已因不活跃关闭）。

## 4. 性能与优化

- **AMD AITER 在 gfx950 上确定性模式下的 prefill attention**（[#45601](https://github.com/sgl-project/sglang/pull/45601)）—— 在 MI350X 上，Qwen3-8B @ 32k 输入使用 AITER batch prefill 将 Triton prefill attention 从 **9.3s → 4.1s**。
- **Kimi-K3 12-head MLA 在 gfx950 上（FP8 KV）**（[#45320](https://github.com/sgl-project/sglang/pull/45320)）—— 将解码 + 8 token 验证迁移到 fp8-Q ASM，在 AgentX c1 流量上 P90 ITL 从 **6.77ms → 6.18ms**。
- **无暂存拷贝的 paged experts**（[#48082](https://github.com/sgl-project/sglang/pull/48082)）—— 通过消除可分页暂存复制，降低 checkpoint 加载期间的 host 内存占用。
- **`--routed-experts-dtype int16`**（[#47833](https://github.com/sgl-project/sglang/pull/47833)）—— 将 `--enable-return-routed-experts` 的 routed-expert host 缓存减半。
- **LingBot-Video MoE 针对 B300 的 TMA 调优**（[#48470](https://github.com/sgl-project/sglang/pull/48470)）—— 在 832×480×81 下 MoE 约占 kernel 总时间的 39%；启用现有的 up/down TMA 路径针对该区域。
- **扩散：ERNIE-Image 的图像前缀序列并行**（[#48218](https://github.com/sgl-project/sglang/pull/48218)）—— 修复此前在执行前导致 Ulysses2 请求失败的 4D 空间 latent 分片问题。

## 5. 稳定性与回归

按严重程度排序：

1. **Router 熔断器分发到已死的 decode，创建永久假死的 prefill**（[#31206](https://github.com/sgl-project/sglang/issues/31206)）—— sgl-router v0.2.4 PD 模式；单个卡住的 prefill 可毒化集群状态。未引用修复 PR；自 7 月以来开放。
2. **Mooncake PD：KV page stride 不匹配静默损坏 decode KV 并溢出 layer 缓冲区**（[#48231](https://github.com/sgl-project/sglang/pull/48231)，修复 [#48230](https://github.com/sgl-project/sglang/issues/48230)）—— **修复 PR 已开放**。高影响：静默损坏，而非崩溃。
3. **Qwen EAGLE PD 解码在 retraction 后产生损坏输出**（[#48356](https://github.com/sgl-project/sglang/issues/48356)）—— 昨日新增；尚无修复 PR。
4. **DSpark + TP：语法约束请求与任何其他请求批处理会导致所有 rank 死锁**（[#41449](https://github.com/sgl-project/sglang/issues/41449)）—— 集合通信挂起，overlap 和非 overlap 模式均受影响。
5. **混合 GDN 上的 HiCache：超过约 3 个长前缀后 host 层命中降至零**（[#45400](https://github.com/sgl-project/sglang/issues/45400)）—— host mamba 池耗尽而 KV 仍在 host 上；长上下文混合模型的 prefix-cache 容量悬崖。
6. **Mooncake HiCache 对 Qwen3.8-Flash-Next-FP8 备份失败**（[#48145](https://github.com/sgl-project/sglang/issues/48145)）以及 **mamba 惰性边界快照覆盖 keep slot**（[#48328](https://github.com/sgl-project/sglang/pull/48328)，修复 PR 已开放）—— 混合/Mamba 路径中的 checkpoint 完整性。
7. **`--tool-call-parser auto` 在 Qwen2.5-0.5B、Qwen3 2507、Ling-mini-2.0 上找不到 parser**（[#48172](https://github.com/sgl-project/sglang/issues/48172)）—— 工具调用以纯文本返回；影响较小模型上的 agent 集成。
8. **SM120 上的 DSV4 prefill 吞吐差距**（[#33422](https://github.com/sgl-project/sglang/issues/33422)）—— 在 4× RTX PRO 6000 上约 2–7K tok/s，而 vLLM/marlin 约 12.5K；仍未解决，请求调优指导。

## 6. 对应用开发者的意义

- **如果你运行 PD 分离**，审计你的 router 版本和 Mooncake 注册路径：#31206 和 #48231 都是静默失败类别（一个是永久死 prefill 状态，一个是 KV 损坏）。如果你在 PD 下使用 EAGLE，在推出 retraction 密集的工作负载前关注 #48356。
- **工具调用 agent**：`--tool-call-parser auto` 在若干较小/基础模型上不可靠（#48172）。在生产中固定显式 parser，而非依赖自动检测。
- **长上下文混合模型用户**：HiCache host 层悬崖（#45400）意味着一旦被驱逐的工作集超过几个长前缀，前缀复用会急剧下降——相应调整 host mamba 池和长前缀并发。
- **AMD MI3xx/ROCm 部署**正获得实质关注：确定性模式 prefill attention 和 MLA 验证 kernel 现已在审查中有具体的延迟收益，这对可复现性敏感的受监管工作负载很重要。
- **可观测性**：新的首答案 token 时间直方图（[#48466](https://github.com/sgl-project/sglang/pull/48466)）将推理 token 延迟与答案延迟分开——合并后值得为推理模型 SLO 采用。

</details>

<details>
<summary><strong>llama.cpp</strong> — <a href="https://github.com/ggml-org/llama.cpp">ggml-org/llama.cpp</a></summary>

# llama.cpp 摘要 — 2026-11-05

## 1. 今日亮点
llama.cpp 发布了 **v0.9.0**，核心亮点包括 OCP FP8 E4M3 量化、新增 `flash_attn_ext_rows` 算子（实现高效的统一 KV 多序列推理）以及通用批量张量拷贝 API。与此同时，还合入了四个新架构（Clover、Limite、Metis-1.6、Mistral Large 4），并为 Drama-3 TTS 增加了音频支持。本次发布周期其余部分主要由后端调优（OpenCL/Adreno、Vulkan Intel）以及一批仍在分诊中的 Intel GPU 正确性/回归问题报告所占据。

## 2. 发布与破坏性变更
- **v0.9.0**（b12110 版本号提升）：https://github.com/ggml-org/llama.cpp/releases —— 新增 OCP FP8 E4M3 量化、`flash_attn_ext_rows`、批量张量拷贝 API、4 个新架构、音频、Drama-3 TTS（b12116）。
- **通过 `flash_attn_ext_rows` 实现多序列推理的统一 KV 缓存效率** —— 与任何以 `--parallel N>1` 运行 `llama-server` 的用户相关；这是一项能力变更，值得针对你的批处理路径进行验证。
- **`-hf` 量化标签匹配修复**（b12100）：`:F16` 之类的标签不再匹配 `BF16`，`:Q4_XS` 不再匹配 `IQ4_XS`（现已改为按 token 边界锚定）。如果你的工具依赖宽松的标签匹配，请固定为精确的量化标签。https://github.com/ggml-org/llama.cpp/pull/31620
- **服务器实时功能加固**（b12119）：websocket 处理器现在通过 `ex_wrapper` 路由，并在异常时发出错误事件而不是崩溃 —— 对于此前在工具 schema 格式错误时会看到连接断开的客户端而言，这是行为变更（见 #31626）。https://github.com/ggml-org/llama.cpp/pull/31632

## 3. 新模型与硬件支持
- **新架构**：Clover、Limite、Metis-1.6、Mistral Large 4（v0.9.0）。
- **Drama-3 TTS** 在 `mtmd` 中的音频支持（b12116）：https://github.com/ggml-org/llama.cpp/pull/31613
- **ricursiveAI Ripple‑65B**（新稠密架构，转换器 + 计算图，无新增算子）—— 开放 PR：https://github.com/ggml-org/llama.cpp/pull/31692
- **CUDA TQ2_0 反量化 + MMVQ** 面向三值模型（BitNet b1.58、BitCPM、Falcon3-1.58bit），使权重保留在 GPU 上而非回退到 CPU —— 开放 PR：https://github.com/ggml-org/llama.cpp/pull/30278
- **Hexagon SIN/COS/MEAN 算子** —— 开放 PR：https://github.com/ggml-org/llama.cpp/pull/31674
- **WebGPU**：group norm 与 timestep embedding 内核（供下游 stable-diffusion.cpp 使用）—— PR #31490、#31695
- **SpacemiT 厂商指令前缀** 以兼容上游 binutils —— PR #31677
- 长期未决请求：**XDNA 后端**（#21725，37 条评论，39 👍）仍处于开放状态。

## 4. 性能与优化
- **OpenCL 配合 FA 的解码性能**（b12118）：为 Q4_K gate-up + GLU 分配更多工作组，FA 处理 head size 为 256 的 GQA 2/6 —— 高通贡献的 Adreno 路径：https://github.com/ggml-org/llama.cpp/pull/31633
- **OpenCL Adreno X2**：默认启用 f32 与 Q5_K 多列 GEMV 以加速 MTP 验证 —— PR #31694；非连续 sigmoid —— PR #31687；面向 Adreno X1 的 Q4_0 非 MoE GEMM bin 内核 —— PR #30947
- **GGUF writer**：宿主缓冲区张量直接写出，跳过暂存拷贝（b12101）—— 对大型模型转换/写入吞吐量有显著意义：https://github.com/ggml-org/llama.cpp/pull/31144
- **HIP fast-math 在无回归的情况下启用** —— 开放 PR：https://github.com/ggml-org/llama.cpp/pull/31625
- **Vulkan Intel prefill FA 内核** —— 开放 PR：https://github.com/ggml-org/llama.cpp/pull/29357
- **MTP conv 快照批量合并为单个 `set_rows`** 用于循环模型（qwen4exp、glm5-*）—— PR #31660，#30878/#31443/#31584 的后续工作

## 5. 稳定性与回归（按严重程度排序）
1. **CUDA 非法内存访问** —— Gemma 4 31B + draft-MTP 在 RTX 4060 Ti 上执行 KV 回滚/清除后触发（#31077）；此外 **qwen4exp + draft-MTP** 在服务器启动时以 `GGML_ASSERT(buffer)` 中止（#30860）。尚无关联修复 PR —— 对 MTP 用户而言为高严重度。
2. **Vulkan Intel 非确定性** —— KHR_coopmat 在 Arc Pro B70（BMG G31，Mesa 26.2.5）上对 BERT 类决策模型在 F16 和 Q8_0 下返回不同/错误的结果（#31560）；coopmat_stage 在 RTX 4090 上的共享内存预算低估（#31629）。正确性问题 —— 在修复前，将 Intel Vulkan coopmat 视为对决策/分类器工作负载不安全。
3. **Vulkan Intel 回归** —— Arc Pro B60：在 65840ed 与 29c720e 之间 tg128 从 20.1→13.0 t/s，pp512 从 1117→821 t/s（#31557）；Gemma 4 解码 −5% 二分定位至 0bb496db（#30018）；ANV FA 标量路径回退导致 O(N²) PP 性能下降（#27638）。
4. **SYCL 内存** —— `--fit` 低估使用量导致运行时溢出（#27595）；此前 ngram-mod 的极端 scratchpad 问题已作为陈旧问题关闭（#28860）。
5. **Vulkan 设备丢失** 于 AMD RX 6900 XT（#27076，陈旧）。
6. **服务器正确性** —— `/v1/responses` 将截断的工具调用报告为已完成且从不发出 `function_call_arguments.done`（#30773，违反契约）；`/slots` 恢复在混合/循环/SWA 模型上不产生 KV 复用（#28194）；路由器调度器在 `--models-max 1` 的并发冷启动时存在竞态（#28774）。
7. **今日已修复**：无效工具 schema 导致的实时崩溃（#31626，经 b12119 关闭）；GGUFReader 标量数组越界循环（PR #31627，已关闭）。
8. **Hexagon** —— HTP v73 在 n≥5 时 MUL_MAT 出现 inf 以及 FA/DeltaNet 失败（#29473）；HTP v75 上视觉编码器因图像形状而损坏（#31104）。骁龙 7 系列目前实际上不可用。

## 6. 对应用开发者的意义
- **如果你提供多序列工作负载，请升级到 v0.9.0** —— `flash_attn_ext_rows` 使统一 KV 在并行解码下更高效，直接改善 `llama-server --parallel` 的内存/吞吐量。在全面推广前，请针对你的评估集验证 FP8 E4M3 量化路径。
- **收紧 `-hf` 量化标签**：新的 token 边界匹配修复了一个真实陷阱 —— `:F16` 曾会静默拉取 BF16 权重。请审计任何已固定的模型引用。
- **MTP/投机解码仍是最大风险领域**：两个未决的 CUDA 中止/IMA bug（#31077、#30860）影响 Gemma 4 和 qwen4exp 配合 `--spec-type draft-mtp` 使用。如果你在生产中依赖投机解码，请固定到已知良好的构建，并将 MTP 置于可回退到无投机解码的开关之后。
- **对分类器/决策模型避免使用 Intel Vulkan coopmat**，直到 #31560 解决 —— 对 agent 路由层而言，非确定性输出比慢速输出更糟。Arc B 系列也显示出可测量的回归（#31557）。
- **实时/websocket 端点变得更安全**：无效工具 schema 现在以错误事件形式呈现，而不是使服务器崩溃 —— 请更新客户端错误处理以消费这些事件。
- **OpenAI 兼容性缺口对 agent 框架很重要**：`/v1/responses` 流式传输缺少终止性的 `function_call_arguments.done` 事件（#30773）。如果你的 SDK 等待该事件，将会挂起 —— 要么在客户端打补丁，要么跟踪该 issue。

---

规则：
- 仅输出译文。无前言、无解释、无包围整个输出的 markdown 代码围栏。
- 严格保留 Markdown 结构：标题、表格（包括列对齐行）、列表、引用块、粗体/斜体、水平分割线、emoji。
- 保留 URL、链接目标、代码段、代码块、数字和日期原样。
- 项目名称、仓库 slug、用户名、版本标签、文件路径和 API/配置标识符保留原形式 —— 不要翻译。
- 诸如 #12345 的 issue/PR 引用及其链接文本保持原样。
- 使用自然的技术中文，以中文开发者通讯的语域为准 —— 而非逐字直译。

</details>

<details>
<summary><strong>Ollama</strong> — <a href="https://github.com/ollama/ollama">ollama/ollama</a></summary>

# Ollama 摘要 — 2026-11-05

## 今日亮点
过去 24 小时内没有新版本发布，但 issue 跟踪器显示 v0.40.13/0.40.14 中出现了一组原生工具调用和 API 正确性回归问题（Windows RTX 5090 runner 断连、Qwen 工具调用失败、Responses API 截断）。在 PR 方面，MLX Qwen3/3.5 YaRN 缩放已合入，混合模型的 VRAM 预测被标记为过度计算，另有多个修复针对 OpenAI 兼容的工具结果处理和 SSE 刷新。

## 版本发布与破坏性变更
无。过去 24 小时内没有新版本标签。注意：多份报告将 v0.40.13 和 v0.40.14 视为当前版本；云端 `/api/usage` 似乎出现了回归（见稳定性部分）。

## 新模型与硬件支持
- **MLX Qwen3/Qwen3.5 YaRN 缩放** — PR #19231（已关闭）遵循显式 RoPE 缩放元数据，对文本和 mRoPE 应用 YaRN 频率/幅度，同时保留非旋转维度。修复了 #18262（YaRN 在 262K 以上被忽略）和 #19129。https://github.com/ollama/ollama/pull/19231
- **MLX CUDA 设备拒绝** — PR #19521（已关闭）提前拒绝不支持的 CUDA 架构，而不是在后续加载时以令人困惑的错误失败。https://github.com/ollama/ollama/pull/19521
- **MLX 上的 AutoTrust 决策模型** — PR #19399（已关闭）新增文本/视觉 AutoTrust 路由。https://github.com/ollama/ollama/pull/19399
- **WSL2 RTX Spark N1X GPU 显存发现** — PR #19520（开放中）修补了 WSL2 下统一内存笔记本的 GPU 显存检测。https://github.com/ollama/ollama/pull/19520
- **Linux/Windows 视频输入** — #19503（开放中）请求为 llama-server GGUF 模型支持 MP4/MOV（目前仅限 macOS/MLX）。https://github.com/ollama/ollama/issues/19503
- **安装包体积** — #12268 和 #19512 提议拆分 GPU 运行时包（CUDA 12/13、ROCm、Vulkan、纯 CPU）以缩小 Linux/Windows 安装包。https://github.com/ollama/ollama/issues/12268

## 性能与优化
- **混合/SSM 模型的 VRAM 预测将 KV cache 高估 4–8 倍**（Qwen3.5/3.8、Qwen3-Next），且忽略 `OLLAMA_KV_CACHE_TYPE`，始终按 f16 计算 — issue #19463（开放中）。导致 `keep_alive=-1` 的 runner 被过早驱逐。https://github.com/ollama/ollama/issues/19463
- **GGUF 拉取校验** — #19504 提议在并行 Range 下载过程中校验 blob，以避免拉取后昂贵的全量重新哈希（"verifying sha256 digest"）。https://github.com/ollama/ollama/issues/19504
- **SSE 流式刷新** — PR #17027（开放中）及时刷新 OpenAI 兼容的流式 chunk，以修复明显的 UI 卡顿（Chatbox/OpenWebUI）。https://github.com/ollama/ollama/pull/17027
- **Apple Silicon GPU 显存文档** — PR #19527 记录了统一内存 GPU 上限及如何提高该上限。https://github.com/ollama/ollama/pull/19527
- **推理期间防止休眠** — PR #19525（开放中）新增服务端休眠抑制。https://github.com/ollama/ollama/pull/19525

## 稳定性与回归
按严重程度排序：

1. **CUDA v13 语法约束崩溃 → Xid 79（GPU 掉总线）** — #17026（开放中）。在 cuda_v13 上进行长时间 `format` 约束生成时崩溃；相同工作负载在 cuda_v12 上正常。最高严重级别（硬件级）。https://github.com/ollama/ollama/issues/17026
2. **Windows RTX 5090 runner 在工具调用期间断连（HTTP 500 /tokenize）** — #19439（开放中）。在 0.40.13 配合 Open WebUI 时间歇性出现。https://github.com/ollama/ollama/issues/19439
3. **Qwen2.5-Coder / Qwen3 在 0.40.13 上原生工具调用失败** — #19524（开放中）。https://github.com/ollama/ollama/issues/19524
4. **Responses API 在禁用截断的情况下仍静默截断超大输入** — #19492（开放中），在 0.40.13 和 0.40.14 上可复现。https://github.com/ollama/ollama/issues/19492
5. **GPU 上视觉推理 500** — `qwen2.5vl:3b` 在 GPU 视觉编码器上出现 "token repeat limit reached" 失败；CPU 正常。#19483（开放中）。https://github.com/ollama/ollama/issues/19483
6. **Qwen2.5 模板对 `{{ .Function }}` 渲染 Go 结构体转储而非 JSON** — #19529（开放中）；工具名拼写错误，调用被丢弃。https://github.com/ollama/ollama/issues/19529
7. **云端 `/api/usage` 回归** — #19511（开放中）：会话/每周百分比字段丢失，仅返回请求计数；可能为非预期回归。https://github.com/ollama/ollama/issues/19511
8. **云端 `deepseek-v4-pro` 拒绝 `max_tokens` > 65536**，尽管其输出能力约为 384k — #19517（开放中）。https://github.com/ollama/ollama/issues/19517
9. **长期存在的 TLS 拉取失败** — #3504（开放中，自 2024 年 4 月起）：`pull` manifest TLS 握手超时，仍在复现。https://github.com/ollama/ollama/issues/3504

进行中的修复 PR：#19522（将工具结果文本+图像保留在单条消息中，修复 #19479）、#18767（按 `tool_call_id` 匹配工具结果组，修复 #18762）、#19526（旧版模板保留 helpers/`define`）、#19528（Modelfile `FROM` 引号往返）、#19408（限制 MLX System One 提示扩展，超大时返回 413）。

## 对应用开发者的意义
- **工具调用目前是最脆弱的环节。** 如果你在 Windows/GPU 上依赖 Qwen2.5/Qwen3 的原生工具调用，请锁定到已知良好的版本，并在服务端验证工具名保真度；模板 JSON 损坏（#19529）和 runner 断连（#19439）会表现为调用丢失或格式错误。
- **并行工具结果需要 `tool_call_id` 匹配。** 在 #18767 合入之前，对顺序敏感的后端可能会错误归因结果 — 请按调用顺序发送工具结果或在客户端进行规范化。
- **Responses API 截断是静默的**（#19492）：请在客户端强制实施输入长度检查，而不是依赖服务端截断标志。
- **混合/SSM 模型的容量估算不可靠**（#19463）：请保守预留 VRAM，在修复前不要假设 `OLLAMA_KV_CACHE_TYPE` 会被遵循。
- **云端用量遥测的形态已改变**（#19511、#12532）：不要在仪表板中硬编码百分比字段；API 可能仅提供请求计数。
- **流式客户端**应容忍延迟的 chunk 交付，直到 #17027 合入 — 长时间的明显卡顿可能是缓冲造成的，而非推理停滞。

</details>

<details>
<summary><strong>LiteLLM</strong> — <a href="https://github.com/BerriAI/litellm">BerriAI/litellm</a></summary>

# LiteLLM 摘要 — 2026-11-05

## 1. 今日亮点

LiteLLM 在过去 24 小时内发布了三个版本（v1.108.0 稳定版、v1.106.7 补丁版、v1.109.0-rc.1），全部以 cosign 镜像签名验证作为发布门槛。问题队列仍以**流式正确性与计费准确性缺陷**为主——客户端提前断开、流中途回退以及预算窗口核算都在产生静默的成本/配额缺口。一个包含 9 个 PR 的大型“控制平面”堆栈（数据平面注册表、OCSF 审计事件、SIEM 接收器、集群聚合）正在积极评审中，标志着向多数据平面集群管理推进。

## 2. 版本发布与破坏性变更

| 版本 | 说明 |
|---|---|
| **v1.108.0** | 稳定版。所有 Docker 镜像（`litellm`、`litellm-database`、`litellm-non_root`、`docker.io/litellm/*`）现已使用 cosign 签名；请使用提交 `e82a3c6` 中的密钥进行验证。[发布说明](https://github.com/BerriAI/litellm/releases/tag/v1.108.0) |
| **v1.106.7** | 1.106 系列的补丁版本，使用相同的 cosign 验证命令。[发布说明](https://github.com/BerriAI/litellm/releases/tag/v1.106.7) |
| **v1.109.0-rc.1** | 候选发布版——签名情况相同；预计 API 表面变更将在此版本稳定。[发布说明](https://github.com/BerriAI/litellm/releases/tag/v1.109.0-rc.1) |

**需关注的模型映射回归：** `eu.anthropic.claude-haiku-4-5-20251001-v1:0` 已从 `model_prices_and_context_window.json` 及 #49113/23595f7fe 中的模型映射中移除，但 AWS 仍将其作为 EU 推理配置文件提供——固定使用该 ID 的部署将解析失败。[#49312](https://github.com/BerriAI/litellm/issues/49312)

## 3. 新模型与硬件支持

- 现有 DashScope 提供商请求支持 **QwenCloud 迁移路径**（Qwen、Wan、CosyVoice 的 OpenAI 和 Anthropic 兼容端点）。[#36150](https://github.com/BerriAI/litellm/issues/36150) — 已关闭。
- **Cloudflare Workers AI** 图像和音频支持请求因过期/重复而关闭——不会落地。[#35055](https://github.com/BerriAI/litellm/issues/35055)、[#35056](https://github.com/BerriAI/litellm/issues/35056)
- 提议以 **Rust 路由核心**替代 Python 核心，仅以宿主作为边界（`PythonHost` 桥接，无原生执行器路径）。评审中。[#49513](https://github.com/BerriAI/litellm/pull/49513)
- 今日未出现新的 CUDA/ROCm/Metal 或量化格式相关工作。

## 4. 性能与优化

- **MCP `tools/list` 延迟回归：** 从 v1.106.6 升级到 v1.108.0 后，对于发送 2026 之前协议版本的 `notifications/initialized` 的客户端（例如 Python `mcp` SDK 的 `ClientSession`，Open WebUI 使用的就是它），通过 `/mcp/` 的 `tools/list` 会阻塞 **18–50 秒**——追踪到遗留订阅监听器。[#50065](https://github.com/BerriAI/litellm/issues/50065)
- **最小繁忙路由踩踏修复：** 同时到达的请求读取到相同的在途计数并全部涌向同一部署；该 PR 在选择时预留每次选择，使下一个请求将其视为繁忙。[#47484](https://github.com/BerriAI/litellm/pull/47484)
- **健康探针驱逐上下文：** 在针对 262k 上下文本地模型、并发为 1 的情况下，健康检查据报告会导致上下文驱逐，并随上下文填满而出现周期性延迟尖峰。[#49930](https://github.com/BerriAI/litellm/issues/49930)
- **工具循环中的提示缓存未命中：** `/v1/messages` → Vertex Claude 在 `system`/`tools` 之前发送 `messages`，破坏了工具循环轮次间的提示缓存命中。[#50070](https://github.com/BerriAI/litellm/issues/50070)

## 5. 稳定性与回归

**严重——计费/用量正确性：**
- **提前断开时流式用量丢失：** 如果客户端在最终用量块之前中止，该请求的所有 token/成本跟踪都会被丢弃——造成计费缺口和配额不准确。对任何计量部署均为高严重性。[#14457](https://github.com/BerriAI/litellm/issues/14457)（开放）
- **预算窗口支出丢弃在途请求：** 在请求进行中执行 `/key/update` 意味着该请求的成本永远不会计入 `LiteLLM_BudgetWindowSpend`（支出日志和密钥 `spend` 是正确的；只有窗口总额错误）。[#49179](https://github.com/BerriAI/litellm/issues/49179) — 已关闭。
- **并发下的会话预算绕过：** 准入检查记录了支出但未为在途调用预留容量，因此并发请求可能超出 `max_budget_per_session`。[#34732](https://github.com/BerriAI/litellm/issues/34732)（开放）
- **自 #49532 起自定义按调用价格丢失区域/速度乘数** — OpenAI 数据驻留、Vertex 区域和 Anthropic 地理路由的加价全部被丢弃。修复 PR：[#50063](https://github.com/BerriAI/litellm/pull/50063)。相关批量缓存定价回退：[#50062](https://github.com/BerriAI/litellm/pull/50062)。

**高——流式/回退正确性：**
- **仅推理前缀后回退丢失：** `sent_first_chunk` 在任何增量（包括推理）时翻转，因此思考期间的中流故障被误报为首块之后，即使客户端未收到任何内容也会跳过回退。[#49795](https://github.com/BerriAI/litellm/issues/49795)（开放）
- **SSE 保活在上游故障前提交 200：** 使用 `sse_keepalive_ping_interval_seconds` 时，第一个 `: ping` 即提交响应，因此后续上游错误只能写入响应体——`/v1/messages` 上的 Anthropic 客户端会收到 SSE 错误载荷而非正常的失败。[#49711](https://github.com/BerriAI/litellm/issues/49711)（开放）
- **Responses 桥接在 `/v1/chat/completions` 流式传输中重复工具调用参数**（针对 `azure/responses`）：第一个增量携带完整参数 JSON，随后参数增量再次发送片段。[#48773](https://github.com/BerriAI/litellm/issues/48773) — 已关闭。
- **Anthropic `server_tool_use` 被呈现为待定 tool_call** 导致代理客户端循环（finish_reason 为 `stop`，同时包含 content 和 tool_calls）。[#48384](https://github.com/BerriAI/litellm/issues/48384)（开放）

**中——翻译/提供商边缘情况：**
- `chatgpt/` 提供商在 `/v1/messages` 上丢弃加密推理 → 工具调用之间从不重放。[#49046](https://github.com/BerriAI/litellm/issues/49046)
- Azure Messages→Responses 绕行在恢复的工具调用历史中丢弃加密推理（#40451 的后续）。[#50024](https://github.com/BerriAI/litellm/issues/50024)
- Bedrock Converse 向非 Anthropic 模型（例如 `zai.glm-5.3`）重放 `reasoningText.signature` → 400。[#49969](https://github.com/BerriAI/litellm/issues/49969)
- Groq 在流式推理回复后拒绝历史记录，因为 `reasoning_content` 被回显。[#47931](https://github.com/BerriAI/litellm/issues/47931) — 已关闭。
- Databricks 仅工具响应（无 `content` 键）在 `resolve_reasoning_and_content` 中引发 `KeyError`。[#48637](https://github.com/BerriAI/litellm/issues/48637)
- `text.format: json_schema` 被原样转发给仅支持 `json_object` 的提供商 → 400 被呈现为 500。[#49198](https://github.com/BerriAI/litellm/issues/49198)
- `bedrock/openai` 调用路由忽略 `model_id`（应用程序推理配置文件 ARN）→ 400 `Unknown parameter`。[#49083](https://github.com/BerriAI/litellm/issues/49083)
- OTel `set_attributes` 对非字典响应（`DecisionsResponse`、MCP `CallToolResult`、列表）调用 `.get()` → 崩溃。[#48719](https://github.com/BerriAI/litellm/issues/48719)
- 粘性会话未生效——路由器发送到不同端点。[#50029](https://github.com/BerriAI/litellm/issues/50029)

**护栏：**
- 通过 Anthropic `/v1/messages` 发送的 MCP 工具对 `pre_call` 中的 `custom_code` 和 `tool_permission` 护栏不可见——无法检查或阻止它们。[#40583](https://github.com/BerriAI/litellm/issues/40583) — 已关闭。
- 流式多选完成会一起检查所有选项的工具调用，导致掩码遗漏和错误的按选项限制触发。修复 PR：[#46860](https://github.com/BerriAI/litellm/pull/46860)。

## 6. 对应用开发者的意义

1. **不要信任中止流的用量数字。** 如果你的应用会取消流式请求（超时、用户导航、代理步骤预算），这些 token 可能永远不会被计费或计入配额。在 #14457 落地之前请为此缺口做预算，并避免将 LiteLLM 侧支出作为基于用量定价的唯一真实来源。
2. **流中推理回退不可靠。** 对于推理模型，“思考”期间的提供商故障不会触发你配置的回退（#49795），而启用保活的流无法干净地呈现上游错误（#49711）。如果你需要弹性的推理模型服务，优先使用非流式或在客户端实现重试/回退。
3. **固定模型 ID，验证镜像。** v1.108.0 是稳定版；Claude Haiku EU 推理配置文件的移除（#49312）对任何使用区域 Bedrock ID 的人都是活生生的陷阱。在推广前验证 cosign 签名——这现在是文档化的发布契约。
4. **代理循环 UX 回归是真实存在的。** Anthropic `server_tool_use` 作为待定 tool_call（#48384）以及 `chatgpt/` 路径上丢失加密推理（#49046、#50024）都会导致 Claude Code 风格客户端在 `/v1/messages` 上循环或丢失推理历史。如果你将代理流量路由到 Anthropic 兼容端点，请显式测试工具循环。
5. **MCP 网关延迟：** 如果你升级到 v1.108.0 并使用 Python SDK 客户端配合 MCP，预期会出现数十秒的 `tools/list` 停滞（#50065）——如果 MCP 发现位于你的关键路径上，请固定 v1.106.6 或等待修复。
6. **控制平面方向：** 这个 9-PR 堆栈（数据平面注册表 → 扇出聚合 → 审计/OCSF/Splunk/Sentinel/S3-Object-Lock 接收器 → OTel span）指向一个未来：一个 LiteLLM 控制平面管理多个数据平面。如果你运行多集群网关集群，值得关注；目前尚无可用内容。从 [#49655](https://github.com/BerriAI/litellm/pull/49655) 开始。

</details>

<details>
<summary><strong>Unsloth</strong> — <a href="https://github.com/unslothai/unsloth">unslothai/unsloth</a></summary>

# Unsloth 文摘 — 2026-11-05

## 1. 今日亮点
Unsloth 发布了 **v0.1.915-beta**，新增 **Qwen3.8-Flash-Next** 支持，同时完善了 Glass 主题、新增 Composer 位置滑块，并内置了浏览器文件交互功能。在基础设施方面，正在进行的主要工作是 **在纯 `model.generate()` 中默认启用 CUDA-graph 解码**（[#16691](https://github.com/unslothai/unsloth/pull/16691)），以及 danielhanchen 提交的一批 **多 GPU 扩散放置** PR。稳定性仍然令人担忧：今天集中出现了一批桌面端/Studio 回归问题，最突出的是 **长对话下的 UI 卡顿**（[#16603](https://github.com/unslothai/unsloth/issues/16603)）以及反复出现的 **`search_conversation` 工具消失**（[#16602](https://github.com/unslothai/unsloth/issues/16602)、[#16706](https://github.com/unslothai/unsloth/issues/16706)），此外还标记了一个 **transformers 5.23 兼容性破坏**问题，影响训练栈。

## 2. 发布与破坏性变更
- **v0.1.915-beta** — 改进自定义 + Glass + 浏览器使用：新增 Qwen3.8-Flash-Next、Glass 主题（Beta）、Composer 位置滑块、更低的空闲内存占用、**切换 GGUF 模型时保留聊天上下文**、更快的 MLX 加载、FP8 改进。无已记录的破坏性 API 变更。
- ⚠️ **transformers 5.23 将 gradient checkpointing 重命名为 activation checkpointing**（[#16667](https://github.com/unslothai/unsloth/issues/16667)）：旧名称在 v6 之前仍作为别名保留，但 Trainer 现在只读取新名称。**Unsloth 中有 4 处行为发生变化**——升级 transformers 前请先审查。
- **安装/文档不一致**：宣传的 `brew install --cask unsloth` 会失败——目前尚不存在 Cask（[#16672](https://github.com/unslothai/unsloth/issues/16672)）。
- **PyPI 上缺少 Torch 2.13/2.14 extras**（[#16520](https://github.com/unslothai/unsloth/issues/16520)，已关闭）——依赖 `cu130-2130` 风格的 extras 前请先确认 wheel 是否可用。

## 3. 新模型与硬件支持
- **Qwen3.8-Flash-Next** — 在 v0.1.915-beta 中新增。
- **Inception Mercury（扩散 LLM）预设**，支持实时扩散流式输出（[#16508](https://github.com/unslothai/unsloth/pull/16508)）。
- **多 GPU 扩散（FLUX.1/FLUX.2）**：自动选择配对显卡并复制到第二张卡（[#16682](https://github.com/unslothai/unsloth/pull/16682)、[#16687](https://github.com/unslothai/unsloth/pull/16687)）。
- **HunyuanVideo-1.5 小主机规划器**修复，适用于 15 GB 显卡 / 31 GB 主机（Kaggle T4）（[#16700](https://github.com/unslothai/unsloth/pull/16700)）。
- **Qwen-Image** 现在在显存允许时自动保留 bf16 文本编码器（[#16678](https://github.com/unslothai/unsloth/pull/16678)）。
- 涉及的后端：CUDA、MLX（更快的加载）、FP8、GGUF。

## 4. 性能与优化
- **`generate()` 中默认启用 CUDA-graph 解码**（[#16691](https://github.com/unslothai/unsloth/pull/16691)）：Qwen3-0.6B 每步启动约 675 个 kernel（使用 LoRA 时为 1070–1430）；在 T4 上，GPU 在每个 30–75 ms 的步骤中仅有部分时间处于忙碌状态。捕获的解码与 eager 模式**逐位相等**，并支持内存释放和 eager 回退。
- **RoPE kernel Triton 特化修复**（[#16690](https://github.com/unslothai/unsloth/pull/16690)）：将 `seqlen` 标记为非特化，以避免解码（len=1）与预填充之间产生重复的 kernel 变体。
- **SDXL 管线加载：在 L4 上从 24–25 秒提速**，通过一次性读取 v-prediction 标记并跳过 UNet scheme 探测（节省约 6 秒）（[#16696](https://github.com/unslothai/unsloth/pull/16696)）。
- **Qwen-Image 质量/延迟**：int8 ConvRot 编码器是相对于 bf16 的 LPIPS 差距的主要来源（均值 0.0865，最差提示词 0.401）；常驻时自动使用 bf16（[#16678](https://github.com/unslothai/unsloth/pull/16678)）。
- **v0.1.915-beta 中更低的空闲内存 + 更快的 MLX 加载**。
- **API 提供商的 tok/s 徽章**（[#16506](https://github.com/unslothai/unsloth/pull/16506)）以及 **自定义连接的真实 token 用量/上下文窗口**（[#16505](https://github.com/unslothai/unsloth/pull/16605)）——可观测性改进。

## 5. 稳定性与回归
按严重程度排序：
1. **长对话下的 UI 卡顿（Web + 桌面端）** — 未解决的回归，尚无修复 PR（[#16603](https://github.com/unslothai/unsloth/issues/16603)）。
2. **`search_conversation` 工具消失（第 2 次和第 3 次出现）** — 反复出现；关闭后再次出现表明修复不完整（[#16602](https://github.com/unslothai/unsloth/issues/16602)、[#16706](https://github.com/unslothai/unsloth/issues/16706)）。
3. **聊天模板丢弃任意 `chat_template_kwargs`**（仅 3 个键能通过；例如会破坏 Sage-A1.5 的 `verification`）（[#16545](https://github.com/unslothai/unsloth/issues/16545)，已关闭）。
4. **gemma-4-thinking 聊天模板移除推理内容**（[#16699](https://github.com/unslothai/unsloth/issues/16699)）。
5. **Studio `web_search` 始终显示 "No results found"** — 缺少 Accept 头（DDG 202）+ yandex 未在允许列表中（[#16664](https://github.com/unslothai/unsloth/issues/16664)）。
6. **S3 音频摄取正确性**：单记录 `.json` 清单跳过路径重写（[#16630](https://github.com/unslothai/unsloth/issues/16630)）；前缀根冲突会静默选择错误的录音（[#16528](https://github.com/unslothai/unsloth/issues/16528)）——**静默的训练数据损坏**。
7. **微调任务 API 在新任务启动时丢弃已取消的排队任务**（[#16632](https://github.com/unslothai/unsloth/issues/16632)）。
8. **混合 ShareGPT + 原生工具调用丢失工具调用元数据**（[#16051](https://github.com/unslothai/unsloth/issues/16051)，已关闭）。
9. **AppImage 在 Slackware64-current 上失败**（GLib/AppIndicator 符号查找）（[#16605](https://github.com/unslothai/unsloth/issues/16605)）。
10. **原生量化测试的顺序依赖问题**已修复（[#16704](https://github.com/unslothai/unsloth/pull/16704)，已关闭）；另有若干桌面端更新失败报告（[#16573](https://github.com/unslothai/unsloth/issues/16573)、[#16561](https://github.com/unslothai/unsloth/issues/16561)，已关闭）。

## 6. 对应用开发者的意义
- **本地服务默认免费提速**：纯 `generate()` 中的 CUDA-graph 解码路径逐位相同，并可通过 eager 回退选择退出——预计在消费级 GPU（T4 级别）上无需修改代码即可获得显著的 tok/s 提升。
- **固定 transformers <5.23**，直到 checkpointing 重命名被完全吸收；Trainer 的行为变化可能静默改变训练结果。
- **不要轻信表面上的聊天模板行为**：`chat_template_kwargs` 过滤意味着提供商特定的标志（例如 verification/agentic 模式）会被丢弃——如果你依赖它们，请进行端到端验证。
- **网关集成**：远程提供商的自定义请求头仍不受支持（[#14811](https://github.com/unslothai/unsloth/issues/14811)，已关闭）——请相应地规划 LiteLLM 风格的追踪/特征选择请求头；自定义连接的用量/上下文报告正在改进。
- **Agent 工具**：浏览器脚本执行将获得 "Always allow" 选项（[#16707](https://github.com/unslothai/unsloth/pull/16707)）——与无人值守 agent 相关；沙箱粒度请求（[#16625](https://github.com/unslothai/unsloth/issues/16625)）和持久化项目/agent 记忆（[#10775](https://github.com/unslothai/unsloth/issues/10775)）预示着 agent 平台的发展方向。
- **训练管线**：推送数据集前请审查 S3/单记录清单处理——这两个音频 bug 会在不报错的情况下产生错误的训练对。

</details>

---
*本日报由 [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar) 自动生成。*