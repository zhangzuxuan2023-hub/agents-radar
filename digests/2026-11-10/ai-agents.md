# OpenClaw 生态日报 2026-11-10

> Issues: 500 | PRs: 500 | 覆盖项目: 5 个 | 生成时间: 2026-11-09 23:19 UTC

- [OpenClaw](https://github.com/openclaw/openclaw)
- [Hermes Agent](https://github.com/nousresearch/hermes-agent)
- [IronClaw](https://github.com/nearai/ironclaw)
- [QwenPaw](https://github.com/agentscope-ai/QwenPaw)
- [ZeroClaw](https://github.com/zeroclaw-labs/zeroclaw)

---

## OpenClaw 项目深度报告

# OpenClaw 项目摘要 — 2026-11-10

## 1. 今日概览

OpenClaw 仍处于高速维护阶段，过去 24 小时内**没有新版本发布**，但分类处置与工程吞吐量可观：共触及 500 个 issue 和 500 个 PR，其中 **208 个 issue 已关闭**、**131 个 PR 已合并/关闭**。未决积压倾向于稳定性工作——崩溃循环/重启可靠性、SQLite/会话状态完整性，以及 Windows/Kubernetes 部署失败主导了评论最多的 issue。PR 活动集中于小而精准的修复（CLI 错误暴露、WebChat 渲染、Android/Telegram 渠道行为），而非大型功能。整体健康度**中等偏良好**：维护者响应积极，许多 PR 已“可供维护者审阅”，但持续存在的一批 P0/P1 可靠性缺陷（Windows 启动、崩溃后更新路径、会话卡死）表明更新/重启子系统是项目当前的薄弱环节。

## 2. 版本发布

**无。** 过去 24 小时内未发布任何新版本或预发布版本。

## 3. 项目进展

131 个 PR 已合并/关闭。标志前进态势的值得注意的已关闭项：

- **Issue #172628 [已关闭, P1]** — 全新 Gateway 启动后主 agent 准入被取代，直至重启（修复形态明确，可排队）。[链接](https://github.com/openclaw/openclaw/issues/172628)
- **Issue #167506 [已关闭, P0]** — 已删除的临时交接 DB 阻塞包恢复及未来更新。[链接](https://github.com/openclaw/openclaw/issues/167506)
- **Issue #192959 [已关闭, P1]** — Kubernetes fsGroup 重新挂载导致凭据目录被拒绝。[链接](https://github.com/openclaw/openclaw/issues/192959)
- **Issue #175926 / #158966 / #168794 [已关闭]** — 分别为 SQLite 快照回收、CDP 凭据泄露和 Codex 中继 502 问题。
- **PR #196294 [已关闭]** — 大型 vault 上的知识记忆保存延迟（约 11s → 约 2s）。[链接](https://github.com/openclaw/openclaw/pull/196294)

进展是渐进式的：许多关闭似乎源于 stale-bot 或解决分类，而非已交付的修复（若干已关闭项带有 `stale` 标签且无关联修复 PR），因此“已关闭”不应一律解读为“已修复”。

## 4. 社区热点话题

| 排名 | 条目 | 评论数 | 信号 |
|---|---|---|---|
| 1 | [#170956](https://github.com/openclaw/openclaw/issues/170956) Windows 启动缓慢 / 插件关闭不干净 / schtasks 权限错误 | 30 | P0，UX 发布阻塞项 |
| 2 | [#166634](https://github.com/openclaw/openclaw/issues/166634) SQLite 会话回收检查点失败 → 崩溃后 agent 不可用 | 27 | 会话状态 |
| 3 | [#193962](https://github.com/openclaw/openclaw/issues/193962) Header-probe 超时修复在 2026.10.10 上无法通过 `openclaw update` 获取 | 21 | P0，diamond lobster |
| 4 | [#166133](https://github.com/openclaw/openclaw/issues/166133) MCP 整数参数被强制转换为字符串 | 16 | P2，需产品决策 |
| 5 | [#164394](https://github.com/openclaw/openclaw/issues/164394) WebChat 对话抖动（现已关闭） | 15 | UX 摩擦 |
| 6 | [#144336](https://github.com/openclaw/openclaw/issues/144336) 延迟配置重载重启在关闭步骤失败 | 14 | P1，崩溃循环 |

**底层需求：** 最热帖围绕单一主题——**可信赖的重启与更新**。用户无法可靠地从崩溃中恢复或应用修复，这既阻碍采用，又将瞬时 bug 变成永久性中断。第二个主题是**会话持久性**：回收/锁/检查点失败使 agent 搁浅，直至人工干预。MCP 强制转换帖反映了日益增长的第三方集成需求，以及就工具调用适配器做出产品决策的必要性。

## 5. Bug 与稳定性

按严重程度排序（今日未决项）：

**P0 / 发布阻塞**
- [#170956](https://github.com/openclaw/openclaw/issues/170956) — Windows：启动缓慢、插件关闭不干净、schtasks 权限错误；重启/更新不可靠。**无关联修复 PR。**
- [#193962](https://github.com/openclaw/openclaw/issues/193962) — SQLite header-probe 超时的修复在受影响安装上无法获取，因为包更新运行的是*已安装*更新器的 10s 探测。源码复现。**无关联修复 PR。**

**P1 / 崩溃循环、数据丢失、会话状态**
- [#144336](https://github.com/openclaw/openclaw/issues/144336) — 延迟 SIGUSR1 重启在关闭时失败（“singleton 生命周期状态丢失”）。需实机复现。
- [#157255](https://github.com/openclaw/openclaw/issues/157255) — 通道超时后回合认领未释放；会话卡死 90+ 分钟。
- [#190307](https://github.com/openclaw/openclaw/issues/190307) — Gateway 重启后许多 agent 永久降级（准入超时）。
- [#174895](https://github.com/openclaw/openclaw/issues/174895) — 活跃 Gateway 状态所有者被判定为陈旧；锁被第二个进程回收。Windows。
- [#166641](https://github.com/openclaw/openclaw/issues/166641) — `web_fetch` 冷回退在主线程同步加载插件 → 活性停顿。
- [#191841](https://github.com/openclaw/openclaw/issues/191841) — 2026.10.10 回归：glibc < 2.31（RHEL 8/al8）上原生子进程回收不可用；所有 `exec` 失败。
- [#167472](https://github.com/openclaw/openclaw/issues/167472) — 子 agent exec 审批失败（`missing scope: operator.approvals`），失败关闭，无审批卡片。
- [#192141](https://github.com/openclaw/openclaw/issues/192141) — 中断回合的续接丢弃原始用户文本。

**P2 / 值得注意**
- [#182891](https://github.com/openclaw/openclaw/issues/182891) — 提示缓存从不复用（`cache read dropped N -> 0`）。
- [#176187](https://github.com/openclaw/openclaw/issues/176187) — 稀疏 vec0 分配使 `memory_search` 超过 30s 期限。
- [#194662](https://github.com/openclaw/openclaw/issues/194662) — 通知唤醒虚构发送者；主会话保持幻影线程存活。

**评估：** 若干最高严重程度的 issue **无关联修复 PR**，需要实机复现或产品决策，表明瓶颈在支持而非工程。值得注意的是，#193962 是一个*元 bug*：先前修复的恢复路径本身已损坏，这是该组中最危险的模式。

## 6. 功能请求与路线图信号

- **CJK/中文搜索支持** — [#170997](https://github.com/openclaw/openclaw/issues/170997) 请求 `bigram` FTS 分词器（trigram 无法匹配 2 字符查询；unicode61 不对 CJK 分词）。配套 issue [#173839](https://github.com/openclaw/openclaw/issues/173839) 关于 CJK 载荷预检，表明存在连贯的 i18n/APAC 推进。
- **Review 文件中的渲染 Markdown 预览** — [#128090](https://github.com/openclaw/openclaw/issues/128090)，4 👍，修复形态明确。低风险，可能候选。
- **Android 功能对齐** — [#175826](https://github.com/openclaw/openclaw/issues/175826) 关于聊天内问题/审批的设计验收请求；PR 活动（#195979、#195928、#196300）证实 Android 投入活跃。
- **按自动化配置的 web 搜索提供商** — [#173518](https://github.com/openclaw/openclaw/issues/173518)（已关闭，产品决策待定）。
- **运维恢复工具** — [#170562](https://github.com/openclaw/openclaw/issues/170562) 针对搁浅的请求方结算唤醒；[#165685](https://github.com/openclaw/openclaw/issues/165685) 机器可读收集器原因。

**预测：** 近期版本可能优先考虑 (a) CJK 搜索/分词器工作，(b) Android 功能对齐，以及 (c) 小型 Control UI/WebChat 打磨 PR。记忆/REM 排除语义（[#172547](https://github.com/openclaw/openclaw/issues/172547)）是合理的中期事项。

## 7. 用户反馈摘要

**痛点**
- **更新/重启可靠性是主要挫败来源** — 用户描述需要“多次重试”和数小时的误诊。#173313（配置获取屏蔽敏感值）据称在一次 Funnel 恢复期间导致 4+ 小时的中断验证循环。
- **静默降级**：会话卡死 90+ 分钟；重启后 agent 变为“降级”；崩溃恢复使 agent 永久拒绝 `chat.history`/`models.list`。
- **诊断陷阱**：`status --deep` 和 `status --agent` 吞掉真实错误（已由 PR [#196073](https://github.com/openclaw/openclaw/pull/196073)、[#196081](https://github.com/openclaw/openclaw/pull/196081) 处理）；`gateway restart` 报告失败却以 0 退出（[#161158](https://github.com/openclaw/openclaw/issues/161158)）。
- **部署摩擦** 存在于 Windows（schtasks）、Kubernetes（fsGroup）和 RHEL 8（glibc）。

**积极信号**
- 维护者在高严重程度帖中明显参与（30/27/21 条评论），许多 PR 被标记为“可供维护者审阅”或附带截图/证据。
- 用户提交详细、高质量的复现，包含环境和版本数据，表明技术投入度高的用户群。

**情绪：** 沮丧但参与。满意度取决于重启/更新可靠性；在添加功能之前修复运维基座有明确意愿。

## 8. 积压关注

长期未答复或决策受阻、需维护者关注的条目：

| 条目 | 时长 | 阻塞因素 |
|---|---|---|
| [#92473](https://github.com/openclaw/openclaw/issues/92473) 模型回退通知泄露到用户可见渠道 | 约 5 个月 | `needs-product-decision` |
| [#91931](https://github.com/openclaw/openclaw/issues/91931) 预置身份文件自动完成 bootstrap，删除用户 BOOTSTRAP.md | 约 5 个月 | P1，数据丢失，需产品决策；修复 PR [#196302](https://github.com/openclaw/openclaw/pull/196302) 现已开启 |
| [#137613](https://github.com/openclaw/openclaw/issues/137613) CLI 后端上预压缩记忆刷写被禁用 | 约 2 个月 | 产品决策 |
| [#166133](https://github.com/openclaw/openclaw/issues/166133) MCP 整数→字符串强制转换 | 约 1 个月 | 产品决策，16 条评论 |
| [#128090](https://github.com/openclaw/openclaw/issues/128090) Review 中的 Markdown 预览 | 约 2.5 个月 | 修复形态明确，等待维护者 |
| [#173057](https://github.com/openclaw/openclaw/issues/173057) 共享 `agents.defaults.workspace` 被当作活跃工作区 | 约 1 个月 | 需实机复现 |
| [#151923](https://github.com/openclaw/openclaw/pull/151923) Telegram 大媒体 30s 超时（PR） | 约 2 个月 | 等待维护者 |
| [#178677](https://github.com/openclaw/openclaw/pull/178677) Cron 任务因创建者渠道缺失而阻塞（PR） | 约 3 周 | 等待作者，需安全审查 |

**优先级建议：** #193962 和 #170956 值得维护者立即接手——它们是 P0 恢复路径故障，无修复 PR，且直接加剧用户对更新的不满。#91931 值得注意，因为五个月后现在已有修复 PR；快速推进将关闭一个长期存在的数据丢失投诉。

---
*数据来源：github.com/openclaw/openclaw，截至 2026-11-09 的 24 小时窗口。评论/反应计数反映该期间更新的条目；"无关联修复 PR"在存在 `clawsweeper:no-new-fix-pr` 标签处引用该标签。*

---

## 横向生态对比

# 跨项目对比报告 — 个人 AI 助手 / Agent 开源生态
**快照日期：** 2026-11-10 | **项目：** OpenClaw、Hermes Agent、IronClaw、QwenPaw、ZeroClaw

---

## 1. 生态概览

个人 AI 助手开源领域正分化为两大阵营：**成熟的 "Claw 家族"运行时**（以 OpenClaw 为参考实现，加上衍生项目 Hermes、ZeroClaw、IronClaw），围绕 agent 运行时、消息网关和插件生态构建；以及**应用层助手**（QwenPaw），聚焦本地模型运行时和控制台 UX。整个生态的主要工程负担已从*功能获取*转向*运维可靠性*：崩溃恢复、重启/更新路径、会话状态持久化和安全隔离如今占据了维护者的大部分精力。安全审查正在快速成熟——ZeroClaw 对每个开放的 PR 都执行正式的 S0 严重级别审查流水线，Hermes/OpenClaw 本周期也都修复了安全边界 bug。本地优先推理（MLX、llama.cpp、Ollama）和插件/市场分发是两条最清晰的绿地赛道，而 Windows 仍是生态中最脆弱的平台。

---

## 2. 活跃度对比

| 项目 | Issue（24 小时触及） | PR（24 小时触及） | 合并/关闭吞吐 | 发布状态 | 健康分* |
|---|---|---|---|---|---|
| **OpenClaw** | 500（208 已关闭） | 500（131 已合并/关闭） | 极高，分流密集 | 无（24 小时内无 tag） | **6.5 / 10** — 高速推进，但 P0 重启/更新问题簇无修复 PR |
| **Hermes Agent** | 50（32 已关闭） | 50（14 已合并/关闭） | 高，趋于收敛 | 无（据报告为 0.23.0 线） | **7.5 / 10** — 关闭率高，2 个 P0 `needs-decision` PR 被搁置 |
| **IronClaw** | 0 | 0 | 无 | 无 | **N/A** — 窗口期内休眠 |
| **QwenPaw** | 17 | 50（9 已合并/关闭） | 高（beta 周期） | **v2.3.0-beta.3 已发布** | **7.0 / 10** — 活跃，但打包构建存在 beta 回归 |
| **ZeroClaw** | 35 | 50 | **受阻**（合并队列停滞） | **被 CI 门禁阻塞** | **5.5 / 10** — 贡献者产出健康，流水线死锁 |

*\*健康分是分析师基于发布节奏、修复速度、P0/P1 负担和合并队列活跃度的综合评分——仅作方向性参考，非厂商指标。*

**关键解读：** OpenClaw 的原始 Issue/PR 体量约为同行的 10 倍——它是生态的引力中心。QwenPaw 是本周期唯一交付带 tag 发布的项目。ZeroClaw 是唯一合并队列*完全阻塞*的项目，使其高活跃度具有欺骗性。

---

## 3. OpenClaw 的定位

**相对同行的优势**
- **表面积规模：** 24 小时内触及 500 个 Issue + 500 个 PR，而 Hermes 和 ZeroClaw 为 50/50。这造就了生态中最快的“发现到修复”闭环——例如 PR #196294 将知识记忆保存延迟从约 11 秒降至约 2 秒。
- **生态引力：** ZeroClaw 提供显式的 `zeroclaw migrate openclaw-config` 导入器（#12348），Hermes/ZeroClaw 均继承 Claw 家族的架构假设。OpenClaw 是*事实上的*配置与概念标准。
- **集成广度：** Control UI、WebChat、Android、Telegram、Kubernetes、MCP、SQLite 支撑的记忆/搜索——没有同行能在单一仓库中匹配这一集成矩阵。

**技术路线差异**
- **以 SQLite 为中心的状态模型**（会话回收、检查点、vec0 记忆搜索）——这是 OpenClaw 的标志性押注，也是其标志性负债（见 P0 会话卡死问题簇）。Hermes 等同行依赖 REST 会话 API（`/api/sessions`），QwenPaw 则依赖本地运行时栈。
- **将更新/重启子系统作为一等关注点**——没有同行暴露如此多的运维工具（`status --deep`、`gateway restart`、`openclaw update`），也没有同行在其中存在如此多的 bug。#193962“元 bug”（对先前修复的修复无法通过更新器触达）是 OpenClaw 规模独有的问题。

**社区规模对比**
- Issue 互动印证了更大、技术投入更深的用户群：头部讨论串有 30/27/21 条评论，包含详细的环境复现，而 Hermes 为 11/10/9，QwenPaw 上限为 4 条评论。OpenClaw 的用户在生产环境中运行它（K8s、RHEL 8、Windows 企业版）——这是同行尚未达到的成熟度信号。

---

## 4. 共同技术焦点领域

| 需求 | 项目 | 具体需求 |
|---|---|---|
| **可信的重启/更新与崩溃恢复** | OpenClaw（P0：#170956、#193962、#144336）、Hermes（恢复 provider 修复 #151956）、QwenPaw（#8616 启动 AppHang） | 重启不得使 agent 搁浅；更新器必须能交付自身的修复；SIGUSR1 时干净关闭。 |
| **会话状态持久化 / 无静默丢失** | OpenClaw（#157255 90 分钟卡死、#192141 丢弃用户文本）、Hermes（#143290 过期 `/stop` 丢弃下一轮、#121750 P0 压缩归档未见的轮次）、QwenPaw（#8521 间歇性空响应） | 投递保证优先于功能；fork/reset 语义在 CLI、REST 和桌面端必须无歧义。 |
| **安全隔离与审批范围正确性** | ZeroClaw（S0 批次：#12304–#12311）、Hermes（#152125 审批者绑定、#53674 频道发帖绕过）、QwenPaw（#8642 MCP 策略围栏） | 沙箱文件访问、跨会话附加防护、审批绑定到*请求者*、失败即关闭的默认值。 |
| **Windows / 企业 SKU 可靠性** | OpenClaw（schtasks，约 5 个月的 Windows 问题）、QwenPaw（LTSC 启动、长路径、GBK 编码 #8234、中文路径 #8502）、Hermes（#152202 非交互式插件更新） | 非默认区域设置、遗留编码、冻结构建的依赖校验。 |
| **第三方 / OpenAI 兼容 provider 兼容性** | QwenPaw（#8603 工具调用序列化、#8512/#8615 网关错误格式）、Hermes（#149511 定价覆盖）、OpenClaw（#166133 MCP 整数强制转换） | 优雅处理网关特有的错误格式；正确的工具调用适配器；按自动化配置 provider 路由。 |
| **插件/市场分发完整性** | QwenPaw（#8595 元数据缺失、#8644 频道激活）、Hermes（一天内 4 个目录 PR）、OpenClaw（MCP 适配器需求） | 安装路径必须同时通过 UI 和 CLI 可用；插件生命周期必须由框架管理。 |
| **本地模型运行时** | QwenPaw（MLX + llama.cpp GPU 栈 #8647–#8649、Ollama `num_ctx` #8236）、OpenClaw（记忆/搜索调优） | 第一方 Apple Silicon 支持、GPU 卸载可见性、正确的上下文窗口大小设置。 |

**跨领域洞察：** 五个活跃项目中有四个独立地撞上*同一类 bug*——一条静默丢失数据的恢复或续接路径。这是生态级的架构缺口，而非单个项目的缺陷。

---

## 5. 差异化分析

| 维度 | OpenClaw | Hermes Agent | QwenPaw | ZeroClaw |
|---|---|---|---|---|
| **主要焦点** | 全栈 agent 运行时 + Control UI + 记忆 | 消息网关加固（Telegram 优先）、插件目录 | 桌面助手 + 本地模型运行时 + 控制台 UX | 安全多频道运行时、远程 OIDC 接入 |
| **目标用户** | 生产运维者、K8s/企业、自托管者 | 群聊/多界面消息用户、插件作者 | 桌面/Apple Silicon 用户、中文区域 Windows 用户 | 对安全敏感的自托管守护进程运维者 |
| **架构押注** | SQLite 状态 + 网关 + MCP 适配器 | 基于租约的房间/配置模型 + REST 会话契约 | 冻结桌面构建 + MLX/llama.cpp 本地栈 | 原生 Rust 频道（XMPP/OMEMO）、沙箱执行 |
| **发布节奏** | 高速维护，本周期无 tag | 趋于 0.23 线 | 快速 beta 节奏（已发布 beta.3） | 被 CI 门禁阻塞 |
| **独特风险** | 规模化下运维基座脆弱 | 被搁置的 P0 决策阻塞 PR | 打包/依赖路径的 beta 回归 | 审查发现的安全债务 + 合并死锁 |

**显著分歧：** ZeroClaw 是唯一将**安全审查作为产出入 S0 发现的合并前门禁**的项目——与 OpenClaw 分流驱动的模式在工程文化上存在实质差异。QwenPaw 是唯一投入**第一方本地推理**的项目，这使其与 OpenClaw 的云适配器假设形成对立。

---

## 6. 社区动能与成熟度

**活跃度分层**
- **第一层 — 生态锚点：** OpenClaw（500/500，分流主导）。成熟但背负运维债务。
- **第二层 — 收敛中的稳定器：** Hermes（32 个 issue 关闭、Telegram 清扫、14 个合并）和 QwenPaw（发布 beta 周期、9 个合并、3 位首次贡献者）。两者都在*关闭速度快于开启速度*。
- **第三层 — 受阻/休眠：** ZeroClaw（贡献者产出高，**合并队列死亡**——动能真实但无法兑现）和 IronClaw（无活动；可能休眠或尚未发布）。

**迭代 vs. 稳定**
- **快速迭代：** QwenPaw（新的 MLX/GPU 运行时栈在推进，UX 打磨 PR 当天提交）和 Hermes（插件目录扩张——一天内 4 个提交）。
- **负载下稳定：** OpenClaw 和 Hermes 都处于*加固*阶段——OpenClaw 在重启/会话上，Hermes 在 Telegram 正确性上。两者都未添加重大功能。
- **停滞：** ZeroClaw 需要一个依赖决策（#12320）才能将大量已验证修复的积压转化为发布。这是生态中杠杆率最高的解锁点。

**成熟度排名（综合）：** OpenClaw > Hermes ≈ QwenPaw > ZeroClaw >> IronClaw。值得注意的是，QwenPaw 的*发布门禁纪律*（自动开启 4 小时跨平台验证 issue #8643）比其 bug 体量所暗示的更成熟。

---

## 7. 趋势信号（面向 AI agent 开发者）

1. **可靠性就是新功能。** 在 OpenClaw、Hermes 和 QwenPaw 中，用户明确将*投递保证置于新能力之上*——静默消息丢失、过期 stop 丢弃轮次、崩溃后卡死是呼声最高的抱怨。**行动：** 在任何 agent 循环中设计幂等续接和显式的“消息已落定”确认。
2. **恢复路径回归是最危险的 bug 类别。** OpenClaw 的 #193962（修复无法通过更新器触达）和 ZeroClaw 的 #12330（静默回退到本地守护进程）表明*回归健康的路径*测试不足。**行动：** 用更新器测试更新器；将故障转移视为一等状态机，而非 catch 块。
3. **安全审查正在左移，前置到合并前。** ZeroClaw 针对开放 PR head 的 S0 发现（沙箱点文件访问、审批范围绕过、跨会话附加）设定了新标杆。**行动：** 要求审批绑定到*请求身份*，并默认使用沙箱范围的文件访问。
4. **本地优先推理正在进入助手层。** QwenPaw 的 MLX/llama.cpp/GPU 控制栈和 Ollama `num_ctx` 修复表明桌面助手将拥有自己的推理运行时。**行动：** 尽早抽象模型后端；不要硬编码云适配器假设。
5. **平台对等是信任问题，而非锦上添花。** Windows（schtasks、长路径、GBK）、RHEL 8（glibc）、iPhone Safari（`requestIdleCallback`）和 Linux 半透明都产生了超额挫败感。**行动：** 将非默认区域/编码和冻结构建环境视为一级测试目标。
6. **生态互操作正在围绕 OpenClaw 约定整合。** ZeroClaw 的迁移导入器和共享的 Claw 家族配置概念表明事实标准正在形成。**行动：** 对新进入者，将配置/会话语义与 OpenClaw 对齐，以继承工具链和迁入路径。
7. **插件市场是下一个可靠性前沿。** 安装路径失败（#8595）、冻结构建激活回归（#8644）和框架管理的生命周期 PR（#8431）都指向同一方向。**行动：** 在扩展目录之前设计插件依赖校验和生命周期管理。

---

**给决策者的底线：** OpenClaw 是生态的参考实现，应作为兼容性基线跟踪；Hermes 和 QwenPaw 分别是消息网关和桌面/本地运行时用例最健康的活跃押注；ZeroClaw 拥有最强的安全姿态，但需要先解决其 CI 门禁才能发布；IronClaw 本周期无信号。2026 年决定性的工程问题是**运维信任**——重启、恢复和投递保证——而非功能广度。

---

## 同赛道项目详细报告

<details>
<summary><strong>Hermes Agent</strong> — <a href="https://github.com/nousresearch/hermes-agent">nousresearch/hermes-agent</a></summary>

# Hermes Agent — 项目摘要
**日期：** 2026-11-10 | **仓库：** [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent)

---

## 1. 今日概览

Hermes Agent 表现出**高活跃度与净正向吞吐**：过去 24 小时内涉及 50 个 issue 和 50 个 PR，整体偏向关闭（32 个 issue 已关闭 vs. 18 个新开/活跃；14 个 PR 已合并/关闭 vs. 36 个待处理）。没有发布新版本，但合并队列明显集中于 **Telegram 平台加固**——回退网络路径、重复发送防护、论坛主题隔离以及审批范围安全修复——表明在可能发布带标签版本之前，消息网关正在进行持续的稳定化推进。待处理工作以网关、桌面外壳和会话状态中的 P2 缺陷为主，若干从 7 月至 9 月遗留的长期 Telegram issue 今日终于关闭。整体项目健康度读作**活跃且趋于收敛**，不过一小簇 P0/待决策的压缩与会话状态 PR 仍处于搁置状态。

---

## 2. 发布

**过去 24 小时内无新版本发布。**（据 issue 报告，当前线上已知最新版本线为 v0.22.2 稳定版和 v0.23.0；2026-11-09 → 2026-11-10 期间未发布任何标签。）

---

## 3. 项目进展

**今日合并/关闭 14 个 PR。** 值得关注的已关闭项：

| PR | 关闭内容 | 影响 |
|---|---|---|
| [#152262](https://github.com/NousResearch/hermes-agent/pull/152262) | 在繁忙 profile 后排队的 Group Chat 轮次现在会保留其房间租约并完成结算 | 修复多成员共享同一 profile 时回复被静默丢弃（"indeterminate"）的问题 |
| [#152125](https://github.com/NousResearch/hermes-agent/pull/152125) | `approvals.approvers: []` 在每个界面上绑定到真实发送者 + 单一管理员范围 | 安全加固——堵住非请求者可审批高风险命令的漏洞 |
| [#142512](https://github.com/NousResearch/hermes-agent/pull/142512) | 托管房间终端结算在普通租约过期后仍存活 | 修复对负载敏感的偶发测试失败 / 卡死的"running"状态 |

**Issue 关闭（共 32 个）**绝大多数与 Telegram 相关：洪水控制/输入冷却（#99643、#107612）、网络错误时的重复发送（#64238）、webhook 重新注册（#141397）、频道帖子门控（#53674）、分叉/主题路由（#58850、#36992、#65202）以及脱敏误报（#145664）——这是一次广泛的 **Telegram 可靠性与正确性清扫**。

仍在推进中的开放项：profile 供应重构（[#149075](https://github.com/NousResearch/hermes-agent/pull/149075)）、Bots 房间视图增量读取（[#149210](https://github.com/NousResearch/hermes-agent/pull/149210)）以及 OpenCode Zen 路由的定价覆盖（[#149511](https://github.com/NousResearch/hermes-agent/pull/149511)）。

---

## 4. 社区热门话题

| 排名 | 条目 | 评论数 | 信号 |
|---|---|---|---|
| 1 | [#141988](https://github.com/NousResearch/hermes-agent/issues/141988) — `process_manage` 分页后台输出时 TypeError | 11 | 阻塞 macOS 上日志检索的回归；长期存在（自 10-20） |
| 2 | [#145183](https://github.com/NousResearch/hermes-agent/issues/145183) — Linux 上桌面端半透明，无 UI 开关 | 10（👍1） | UI/UX 不匹配；效果无法禁用 |
| 3 | [#142064](https://github.com/NousResearch/hermes-agent/issues/142064) — `/api/sessions` 省略 `_reset_from` | 9 | REST 客户端契约不一致（分叉 vs. `/new` 被错误标记） |
| 4 | [#142488](https://github.com/NousResearch/hermes-agent/issues/142488) — 元组 `moved` 导致模型切换崩溃 | 8 | 跟随 profile 的会话在桌面端切换模型的阻塞项 |
| 5 | [#143290](https://github.com/NousResearch/hermes-agent/issues/143290) — 过期的 `/stop` 中断下一轮次 | 8 | **数据丢失类**：消息被静默丢弃 |

**底层需求分析：** 最热门的讨论串集中在**会话状态正确性**（分叉语义、重置标记、过期停止信号）和**平台一致性**（桌面设置未反映后端能力）。用户正通过 REST 客户端和桌面 UI 进行集成，而不只是 CLI——`/api/sessions` 契约和桌面 Settings 一致性正成为一等关注点。

---

## 5. 缺陷与稳定性

按严重程度排序：

**P1 / 数据丢失与安全**
- [#143290](https://github.com/NousResearch/hermes-agent/issues/143290)（标记为 P2 但具有数据丢失影响）— 对刚完成的运行调用 `POST /v1/runs/{id}/stop` 会中断*下一*轮次；新消息被静默丢弃。**未见修复 PR。**
- [#53674](https://github.com/NousResearch/hermes-agent/issues/53674) — Telegram 频道帖子绕过 `allowed_chats`/`require_mention`，被当作私聊处理——**今日已关闭**（安全边界修复已落地）。
- [#151762](https://github.com/NousResearch/hermes-agent/issues/151762) — 仅私聊的管理员可通过按钮审批群组审批——**今日已关闭**；#152125 已作为修复合并。

**P2 / 功能性回归**
- [#141988](https://github.com/NousResearch/hermes-agent/issues/141988) — `process_manage` `TypeError: slice indices must be integers`——**开放，11 条评论，尚无修复 PR**（评论最多的最老开放缺陷）。
- [#145183](https://github.com/NousResearch/hermes-agent/issues/145183) — Linux 桌面端半透明且 Settings 开关被隐藏——**开放**。
- [#142488](https://github.com/NousResearch/hermes-agent/issues/142488) — 桌面端模型切换崩溃（`moved` 重命名映射被遮蔽）——**开放**。
- [#142064](https://github.com/NousResearch/hermes-agent/issues/142064) — `/api/sessions` 缺少 `_reset_from`——**开放**。
- [#152148](https://github.com/NousResearch/hermes-agent/issues/152148) — 新建 Telegram 私聊主题不可见/无法打开——**开放**（今日新增，标记 `cause/upstream`）。
- [#148538](https://github.com/NousResearch/hermes-agent/issues/148538) — iPhone Safari：`requestIdleCallback` 未定义，页面无法渲染——**开放**。

**进行中的修复 PR（良好稳定性信号）：**
- [#151833](https://github.com/NousResearch/hermes-agent/pull/151833)、[#151801](https://github.com/NousResearch/hermes-agent/pull/151801)、[#151930](https://github.com/NousResearch/hermes-agent/pull/151930)、[#151772](https://github.com/NousResearch/hermes-agent/pull/151772)——一组连贯的 Telegram 回退/重复发送/论坛隔离补丁集（均为 `ci-reviewed`）。
- [#151956](https://github.com/NousResearch/hermes-agent/pull/151956)——恢复的聊天保留自定义 provider（修复 #132648）。
- [#152230](https://github.com/NousResearch/hermes-agent/pull/152230)——错误信息指明实际原因（更好的诊断）。

---

## 6. 功能请求与路线图信号

- **插件目录扩展是主导性功能向量**：[#151013](https://github.com/NousResearch/hermes-agent/pull/151013)（`hermes-audio-api`，OpenAI 兼容音频端点）、[#151014](https://github.com/NousResearch/hermes-agent/pull/151014)（`hermes-fal-video`，三个 FAL 视频工具 + 捆绑技能）、[#152195](https://github.com/NousResearch/hermes-agent/pull/152195)（`borrowed-hands`，从云端 Hermes 卸载 Xcode 编译）、[#152201](https://github.com/NousResearch/hermes-agent/pull/152201)（FollowThrough 承诺插件）。全部要求 Hermes ≥0.23.0——表明 **0.23 是目录兼容插件的目标版本线**。
- **Bots/Group Chat 架构第二步**见 [#149210](https://github.com/NousResearch/hermes-agent/pull/149210)——日志增量房间视图 + `groups.changed` 推送；路线图工件已在 PR 中链接。
- **可关闭的待办/历史任务 UI** 请求见 [#127429](https://github.com/NousResearch/hermes-agent/issues/127429)（开放，8 条评论）。
- **定价覆盖** OpenCode Zen 见 [#149511](https://github.com/NousResearch/hermes-agent/pull/149511)。

**预测：** 下一个版本将侧重 Telegram 网关可靠性 + 插件目录新增；Bots 架构改进为后续版本做准备。

---

## 7. 用户反馈摘要

**痛点：**
- *"静默丢失"*是反复出现的主题——过期 `/stop` 后消息被丢弃、压缩下未见轮次被归档（[#121750](https://github.com/NousResearch/hermes-agent/pull/121750)，P0）、`send_path_degraded` 回复未送达（[#123898](https://github.com/NousResearch/hermes-agent/issues/123898)）。用户优先关注**投递保证而非新功能**。
- **平台一致性摩擦**：Linux 用户受困于不想要的半透明效果；iPhone Safari 路由失败；Windows 非交互式 `plugins update` 被阻塞（[#152202](https://github.com/NousResearch/hermes-agent/issues/152202)）。
- **跨界面契约漂移**：REST 客户端因缺少 `_reset_from` 而错误渲染分叉；TUI/桌面端在恢复时对 provider 存在分歧。

**满意度信号：** 多个长期存在的 Telegram 缺陷和一个安全边界问题（#53674）已关闭并附有链接修复——有力证明维护者对标记良好的报告响应积极。社区插件作者积极提交（今日 4 个目录 PR），表明扩展生态健康。

---

## 8. 积压关注

需要维护者关注的事项（陈旧、高评论或 P0/待决策）：

- 🔴 [#121750](https://github.com/NousResearch/hermes-agent/pull/121750)——**P0，待决策**，压缩归档未见轮次（自 2026-09-24 开放，**47 天**）。数据丢失风险。
- 🔴 [#132948](https://github.com/NousResearch/hermes-agent/pull/132948)——**P0，待决策**，webui-ng YOLO 恢复 / `failure_reason` / 分叉提示竞态（自 10-04 开放）。
- 🟠 [#141988](https://github.com/NousResearch/hermes-agent/issues/141988)——11 条评论，开放 21 天，无修复 PR——参与度最高的开放缺陷。
- 🟠 [#127429](https://github.com/NousResearch/hermes-agent/issues/127429)——关闭/清除历史任务 UI，开放 42 天。
- 🟠 [#142064](https://github.com/NousResearch/hermes-agent/issues/142064)——`/api/sessions` 契约缺口，开放 21 天。
- 🟡 [#152202](https://github.com/NousResearch/hermes-agent/issues/152202)——非交互式 `plugins update`（新增，但阻塞 SSH/CI 自动化工作流）。

**健康度判定：** 项目维护活跃，关闭率高，且有明确的 Telegram 聚焦修复浪潮。主要风险是**两个搁置的 P0 `待决策` PR**，涉及会话状态与压缩——它们受决策阻塞而非实现阻塞，值得在下一个带标签版本发布前由维护者解决。

---
*基于 2026-11-09 → 2026-11-10 的 GitHub 活动生成。所有计数依据所提供数据集。*

</details>

<details>
<summary><strong>IronClaw</strong> — <a href="https://github.com/nearai/ironclaw">nearai/ironclaw</a></summary>

过去24小时无活动。

</details>

<details>
<summary><strong>QwenPaw</strong> — <a href="https://github.com/agentscope-ai/QwenPaw">agentscope-ai/QwenPaw</a></summary>

# QwenPaw 项目摘要 — 2026-11-10

---

## 1. 今日概览

QwenPaw 正处于 v2.3.0-beta.3 发布后的活跃 beta 稳定化周期。项目活跃度依然非常高：过去 24 小时内有 50 个 PR 和 17 个 issue 被更新，其中包括 9 个已合并/已关闭的 PR。核心主题是全新的 "QwenPaw Local" 模型运行时栈（llama.cpp GPU 控制、托管式 MLX），拆分为三个堆叠 PR（#8649 → #8647 → #8648）。除新功能外，2.3.0 beta 系列中仍不断浮现一批 Windows 特有的打包与渠道插件激活缺陷，同时外部贡献者正在处理内存/搜索性能问题。项目整体健康状况良好——维护者积极审查、issue 分诊迅速、首次贡献者参与度健康——但 beta 回归问题的数量表明 2.3.0 GA 发布可能需要额外的加固时间。

---

## 2. 发布

### v2.3.0-beta.3（[发布页面](https://github.com/agentscope-ai/QwenPaw/releases/tag/v2.3.0-beta.3)）

**变更：**
- `docs(memory)`：为多模态记忆和 Memory Tags 新增了独立的中英双语博客（[#8506](https://github.com/agentscope-ai/QwenPaw/pull/8506)）
- `fix(channels)`：修复了桌面端插件安装与修复失败的问题（[发布说明中引用的 PR](https://github.com/agentscope-ai/QwenPaw/releases/tag/v2.3.0-beta.3)）

**备注：**
- 这是一个 beta 版本；变更日志中未发布明确的破坏性变更或迁移说明。
- 一个[发布值班安装验证 issue（#8643）](https://github.com/agentscope-ai/QwenPaw/issues/8643)被自动开启，要求跨平台在 4 小时内完成验证；这是发布门禁成熟度良好的信号。
- **升级者注意：** beta.3 正在产生其自身的新回归报告（见 §5），尤其是打包版 Desktop 构建上的渠道插件激活失败（[#8644](https://github.com/agentscope-ai/QwenPaw/issues/8644)）。使用稳定版 2.2.2 的用户可能希望等待 beta.4 或 GA。

---

## 3. 项目进展

过去 24 小时内有 9 个 PR 被合并/关闭，集中在测试基础设施、CI 可靠性和清理方面：

| PR | 类型 | 摘要 |
|---|---|---|
| [#8639](https://github.com/agentscope-ai/QwenPaw/pull/8639) | 测试修复 | 修复了依赖安装测试中的 BOM 解码问题 |
| [#8640](https://github.com/agentscope-ai/QwenPaw/pull/8640) | 测试修复 | 修复了 Windows 权限（`0600`）和 UTF-8 BOM 断言 — 提升了跨平台 CI 的确定性 |
| [#8641](https://github.com/agentscope-ai/QwenPaw/pull/8641) | CI 修复 | 将 macOS 桌面构建超时延长至 90 分钟 — 很可能解除了缓慢的 macOS 打包流水线阻塞 |
| [#8645](https://github.com/agentscope-ai/QwenPaw/pull/8645) | 功能（已被取代） | 已关闭，改为重新拆分的 MLX 栈（#8647/#8648/#8649） |

**今日推进的重要在途工作（评论/更新活跃度高）：**
- **本地模型运行时重构** — 三个堆叠 PR [#8649](https://github.com/agentscope-ai/QwenPaw/pull/8649)（llama.cpp GPU + 自动更新）、[#8647](https://github.com/agentscope-ai/QwenPaw/pull/8647)（托管式 MLX）、[#8648](https://github.com/agentscope-ai/QwenPaw/pull/8648)（控制台控制）构成一个连贯的功能集，很可能是下个版本的头条。
- **MCP 驱动策略发布的安全修复** — [#8642](https://github.com/agentscope-ai/QwenPaw/pull/8642) 对陈旧的策略发布进行防护，使缓慢的 MCP 重载无法恢复已撤销的允许策略。这是一项有意义的访问控制正确性修复。
- **提供商兼容性修复** — [#8512](https://github.com/agentscope-ai/QwenPaw/pull/8512)（网关状态码提取）和 [#8615](https://github.com/agentscope-ai/QwenPaw/pull/8615)（识别网关上下文溢出 400 错误）提升了对第三方 OpenAI 兼容网关的鲁棒性。
- **内存搜索性能** — [#8623](https://github.com/agentscope-ai/QwenPaw/pull/8623)（首次贡献者）消除了重复的轮次边界解析。

---

## 4. 社区热点话题

讨论最多的话题（按评论数）揭示了三个底层社区需求：**Windows 上的 beta 可靠性**、**插件生态完整性**以及**智能体 UX 透明度**。

1. **[#8595 — 插件安装失败：qwenpaw-creator 报 "No package metadata was found"](https://github.com/agentscope-ai/QwenPaw/issues/8595)**（4 条评论，OPEN）
   *需求：* 官方插件安装路径通过 UI 和 CLI 均无法正常工作。这削弱了插件市场作为分发渠道的价值——用户无法可靠地安装第一方扩展。

2. **[#8521 — 聊天中缺失响应 + Ctrl+C 复制需要多次按键 + 新 UI 更慢](https://github.com/agentscope-ai/QwenPaw/issues/8521)**（4 条评论，OPEN）
   *需求：* 一份复合 UX 回归报告：聊天输出的可靠性、Chrome/Edge 上的剪贴板行为，以及请求切换回旧 UI 的诉求。表明对 2.x 控制台重新设计的不满，以及可能的渲染/流式传输问题。

3. **[#8627 — Checkpoint 服务在 Git 长路径下失败 + 微信渠道卡在 DEPENDENCY_ENVIRONMENT_AMBIGUOUS](https://github.com/agentscope-ai/QwenPaw/issues/8627)**（2 条评论，OPEN）
   *需求：* Windows 长路径环境处理和晦涩的依赖验证状态。用户需要可操作的诊断信息，而非含糊的状态码。

4. **[#8616 — 2.3.0-beta.1 在 Windows 11 IoT LTSC 上无法启动，AppHang](https://github.com/agentscope-ai/QwenPaw/issues/8616)**（2 条评论，OPEN）
   *需求：* 相对于同一机器上的 2.2.2 的回归 — 对企业 LTSC 部署构成硬性启动阻塞。

5. **MLX/GPU 功能栈（[#8646 issue](https://github.com/agentscope-ai/QwenPaw/issues/8646) + PR [#8647](https://github.com/agentscope-ai/QwenPaw/pull/8647)/[#8648](https://github.com/agentscope-ai/QwenPaw/pull/8648)/[#8649](https://github.com/agentscope-ai/QwenPaw/pull/8649)）** — Apple Silicon 用户希望获得第一方 MLX 支持和对 GPU 卸载的可观测性；由维护者编写的 PR 栈表明这正在积极交付中。

---

## 5. 缺陷与稳定性

按严重程度排序（用户影响 × 广度），附修复状态：

### 严重
- **[#8616 — 桌面版 2.3.0-beta.1 在 Windows 11 IoT LTSC 上无法启动（"path not found" → AppHang）](https://github.com/agentscope-ai/QwenPaw/issues/8616)** — *相对 2.2.2 的回归。* 硬性阻塞；尚未确定修复 PR。
- **[#8644 — 所有渠道插件（feishu、wechat、yuanbao）在打包版 Desktop 构建（2.3.0b3）上激活失败](https://github.com/agentscope-ai/QwenPaw/issues/8644)** — `validate_runtime_identity` 中读取 `DependencyReadEvidence` 的 `supported_tags` 时出现 `AttributeError`。*这是刚发布的 beta.3 中的回归*，影响冻结构建上的所有消息集成。最高优先级修复候选。

### 高
- **[#8595 — 插件安装失败，报 "No package metadata was found"](https://github.com/agentscope-ai/QwenPaw/issues/8595)** — 完全阻塞插件安装；注意 beta.3 变更日志声称已修复渠道/插件安装问题，请验证此问题是否仍然存在。
- **[#8627 — Checkpoint 服务在 Git 长路径下失败 + 微信渠道卡在 DEPENDENCY_ENVIRONMENT_AMBIGUOUS](https://github.com/agentscope-ai/QwenPaw/issues/8627)** — Windows 环境边缘情况；相关依赖验证逻辑与 #8644/#8553 重叠。
- **[#8603 — 在自定义 OpenAI 兼容提供商上 `run_code` 以空参数调用](https://github.com/agentscope-ai/QwenPaw/issues/8603)** — 在用户自定义提供商（kimi、k3.1-pro）上，每次工具调用均被拒绝，报 `'code' is a required property`。破坏了第三方模型用户的核心智能体能力。
- **[#8629 — 超大历史图片在每次模型调用时被重新发送；剥离重试后仍保留误导性的 "Image loaded" 文本](https://github.com/agentscope-ai/QwenPaw/issues/8629)** — 浪费 token/配额，并对请求状态产生误导。

### 中
- **[#8619 — 相同搜索命中重复进行轮次边界查询](https://github.com/agentscope-ai/QwenPaw/issues/8619)** — 性能缺陷；**已有修复 PR：[#8623](https://github.com/agentscope-ai/QwenPaw/pull/8623)。**
- **[#8620 — SQLite 错误在搜索截止时间到期时被掩盖为 TimeoutError](https://github.com/agentscope-ai/QwenPaw/issues/8620)** — `MemorySpace.search` 中的诊断质量问题。
- **[#8521 — 间歇性空聊天响应 + Ctrl+C 复制失败](https://github.com/agentscope-ai/QwenPaw/issues/8521)** — 复现不明确；可能是模型侧或 UI 侧问题。

### 今日已修复 / 已关闭
- **[#8585 — `recall_history` 在 Windows 上失效：`\\?\` 扩展长度路径破坏 SQLite 只读 URI](https://github.com/agentscope-ai/QwenPaw/issues/8585)** — ✅ 已关闭
- **[#8553 — 当 hub 插件缺少依赖环境时，QQ 渠道报 DEPENDENCY_PLAN_UNVERIFIED 失败](https://github.com/agentscope-ai/QwenPaw/issues/8553)** — ✅ 已关闭
- **[#8502 — 桌面版 HTML 预览的"在浏览器中打开"对中文路径失效](https://github.com/agentscope-ai/QwenPaw/issues/8502)** — ✅ 已关闭

**稳定性评估：** 2.3.0 beta 系列存在一个反复出现的主题——打包/冻结的 Desktop 构建和依赖环境验证较为脆弱（见 #8644、#8627、#8553、#8616）。维护者似乎正逐 beta 被动修复这些问题；对冻结构建的身份/依赖路径进行专项审计，很可能在 GA 之前阻止进一步的回归。

---

## 6. 功能请求与路线图信号

| 请求 | Issue | 可能性 / 信号 |
|---|---|---|
| **QwenPaw Local 的托管式 MLX 引擎 + GPU 控制**（Apple Silicon） | [#8646](https://github.com/agentscope-ai/QwenPaw/issues/8646) | **非常高** — 维护者 PR 栈 [#8647](https://github.com/agentscope-ai/QwenPaw/pull/8647)/[#8648](https://github.com/agentscope-ai/QwenPaw/pull/8648)/[#8649](https://github.com/agentscope-ai/QwenPaw/pull/8649) 中已实现。预计下个 beta 发布。 |
| **UX：思考块与工具卡的视觉风格一致；停止左边缘跳动** | [#8650](https://github.com/agentscope-ai/QwenPaw/issues/8650) | 中 — 小型、范围明确的 UI 打磨；适合补丁版本。 |
| **UX："执行型"工具调用应显示人类可读的意图** | [#8612](https://github.com/agentscope-ai/QwenPaw/issues/8612) | **高，已在推进中** — [#8638](https://github.com/agentscope-ai/QwenPaw/pull/8638) 为 `execute_shell_command` 添加了可选的 `title` 参数（同时修复了一个验证问题）。 |
| **浏览器标签页标题应反映当前会话名称** | [#8605](https://github.com/agentscope-ai/QwenPaw/issues/8605) | 中 — 简单的控制台变更；近期可实现。 |
| **将助手回复的选中内容引用到后续草稿中** | （PR [#8609](https://github.com/agentscope-ai/QwenPaw/pull/8609)） | PR 中已实现；可能下个版本发布。 |
| **UI 切换开关以恢复旧控制台布局**（隐含于 #8521） | [#8521](https://github.com/agentscope-ai/QwenPaw/issues/8521) | 低/不确定 — 历史上维护者很少提供双 UI 模式；更可能是解决具体的摩擦点（模型切换）。 |

**对 v2.3.0 GA / beta.4 的预测：** 本地 MLX + GPU 控制、shell 卡意图标题、引用到草稿以及内存搜索性能修复是最强的候选。UX 一致性（#8650）有可能作为快速跟进项。

---

## 7. 用户反馈总结

**痛点：**
- **Windows 是主要的摩擦面。** LTSC 上的启动失败（#8616）、长路径/checkpoint 问题（#8627）、扩展路径 SQLite 缺陷（#8585，现已修复）以及中文路径 HTML 预览失败（#8502，现已修复）均集中在 Windows 配置上——尤其是非默认区域设置和企业 SKU。
- **插件/渠道生态可靠性。** 多份独立报告（#8595、#8644、#8553、#8627）表明插件安装和渠道激活是 2.3.x beta 中最不稳定的子系统。
- **新控制台 UX 不满。** 用户报告重新设计的 UI 感觉比旧版更慢、信息密度更低，具体抱怨包括模型切换的人体工学以及每次按键的剪贴板失败（#8521）。工具卡可读性是一个反复出现的主题（#8612、#8650）。
- **第三方提供商兼容性。** 运行自定义 OpenAI 兼容端点（kimi、Strata 等网关）的用户遇到工具调用序列化损坏（#8603）和无法识别的网关错误格式（#8512、#8615）。

**积极信号：**
- 用户提交的缺陷报告*质量高、范围明确*，附带环境详情和复现步骤，这与维护者的响应速度相关（今日更新的前 20 个 PR 中有 19 个）。
- 三个首次贡献者 PR 处于活跃状态（[#8606](https://github.com/agentscope-ai/QwenPaw/pull/8606) 文档死链修复、[#8623](https://github.com/agentscope-ai/QwenPaw/pull/8623) 内存性能、[#8642](https://github.com/agentscope-ai/QwenPaw/pull/8642) MCP 策略防护）——这是社区投入的健康信号。
- Apple Silicon 用户积极参与，其首要请求（MLX）正由维护者团队交付，这应能转化为满意度。

---

## 8. 待办事项关注

需要维护者关注的事项（较旧或可见度较低但影响重大）：

1. **[#8234 — fix(tools)：在 read/edit/grep 中解码 GBK/CP936 而非静默丢弃字节](https://github.com/agentscope-ai/QwenPaw/pull/8234)** — 自 **2026-10-14** 开启（约 27 天）。解决中文 Windows 用户旧版文本文件的静默数据丢失问题。对庞大区域用户群体具有高价值；已处于"审查中"状态数周。
2. **[#8236 — fix(ollama)：根据 `num_ctx` 设定上下文窗口大小而非默认 128k](https://github.com/agentscope-ai/QwenPaw/pull/8236)** — 自 **2026-10-14** 开启。本地 Ollama 用户获得错误的上下文窗口假设，导致截断或溢出错误。与本地模型推进直接相关；应与 #8647–#8649 栈协调。
3. **[#8170 — fix(console)：容忍 DOM 变更渲染错误 + chunk 错误重试（已 rebase #7889）](https://github.com/agentscope-ai/QwenPaw/pull/8170)** — 自 **2026-10-10** 开启（约 31 天），已 rebase 一次。渲染错误容忍可能与 #8521 中的间歇性 UI 失败相关；鉴于用户对控制台稳定性的抱怨，值得优先处理。
4. **[#8431 — feat：将本地 Whisper 转换为框架管理生命周期的 SDK 插件](https://github.com/agentscope-ai/QwenPaw/pull/8431)** — 大型（XXXL）PR，自 **2026-10-26** 开启；涉及插件生命周期，而这正是产生最多 beta 缺陷的子系统（#8644、#8553）。在与插件依赖重构叠加之前需要架构审查。
5. **[#8606 — docs：修复 5 个失效外部链接](https://github.com/agentscope-ai/QwenPaw/pull/8606)** — 首次贡献者，小型低风险；快速合并将是一个良好的入门信号。

**维护者建议：** 优先处理 (a) #8644 打包构建渠道激活回归作为 beta.3 热修复，(b) 合并长期待处理的 #8234/#8236 本地化/本地模型修复，以及 (c) 对冻结构建依赖验证路径进行专项审计，以在 GA 之前阻止反复出现的 Windows beta 回归。

---

*数据来源：github.com/agentscope-ai/QwenPaw，活动窗口 2026-11-09 → 2026-11-10。所有链接指向相应的 GitHub issue/PR 页面。*

</details>

<details>
<summary><strong>ZeroClaw</strong> — <a href="https://github.com/zeroclaw-labs/zeroclaw">zeroclaw-labs/zeroclaw</a></summary>

# ZeroClaw 项目摘要 — 2026-11-10

## 1. 今日概览

ZeroClaw 当前处于**发布阻塞状态**：Issue #12320 报告称 `RUSTSEC-2026-0459`（libsqlite3-sys 0.38.2 / 捆绑 SQLite 3.53.2）在每次 CI 运行时都导致 `Security` 任务失败，进而级联引发 `CI Required Gate` 失败——这意味着**没有任何 PR 可以合并，合并队列已停滞**。活动量仍然很高（24 小时内涉及 35 个 issue 和 50 个 PR），但严重偏向审查发现：单一审查者（IftekharUddin）针对开放的 PR head 提交了一批 S0 安全发现（#12304–#12312、#12305、#12306、#12309、#12310、#12311），`tidux` 提交了一个大型 zerocode OIDC 登录 PR（#12317）并伴随一系列后续 issue（#12331–#12340）。未发布任何版本，与门禁阻塞状态一致。总体评估：**贡献者吞吐量健康，但由于 CI 阻塞及若干关键的前合并安全发现，合并健康度下降。**

## 2. 发布

本窗口内无发布。规划提示：所有带发布标签的工作当前均被 #12320 阻塞。

## 3. 项目进展

今日合并/关闭的 PR：

- **#12348**（已关闭，XL）— `feat(migrate): import OpenClaw config, agents, and workspaces`：新增 `zeroclaw migrate openclaw-config`，映射 providers、fallback chains 和 agents（balanced risk preset）。重要的生态互操作功能。
- **#11933**（已关闭，XS）— `fix(web): keep the chat header on screen in narrow layouts`：移动端聊天的响应式修复。
- **#11920**（已关闭，XS）— `feat(web): toggle to keep the live Thinking box collapsed`（关闭 #11919）：推理模型的 UX 改进。
- **#12342**（已关闭，XS）— 文档：zerocode OIDC 的有界运行时放置例外（#12317），解除该功能的合并契约阻塞。

已关闭 issue：#10359（审计日志哈希链投毒——先前存在，现已关闭）、#11009（别名重命名未级联权限配置）、#12064（Discord 对等机器人审批绕过）、#12087（工具开关双重保存）、#11952（`-` 前缀的相对数据目录）、#12312（OIDC 发现端点策略缺口）、#11919（Thinking 框）。

## 4. 社区热点

1. **[Issue #12320](https://github.com/zeroclaw-labs/zeroclaw/issues/12320)** — CI 安全门禁失败（S1，3 条评论）。杠杆率最高的线程：每个发布和合并都依赖解决该 RUSTSEC 公告。需要要么升级依赖，要么提供有范围、有文档的公告例外。
2. **[PR #12317](https://github.com/zeroclaw-labs/zeroclaw/pull/12317)** — `feat(zerocode): pairing-free OIDC login and saved remote credentials`（XL）。吸引了 10+ 后续 issue（#12330–#12340）——证明了对无摩擦远程 onboarding 的高兴趣，但也反映了审查负担：PR head 存在已知粗糙边缘（ledger actor 双前缀、smoke-test 顺序、cert-without-CA 语义、凭证修剪）。
3. **[PR #11847](https://github.com/zeroclaw-labs/zeroclaw/pull/11847)** — 原生 Rust XMPP，可选 OMEMO（XL，高风险）及配套 #12015（ChaCha20Poly1305 统一）和 #12319（重复 crate 允许列表）。社区推动渠道广度；这是最大的开放功能列车之一。
4. **[PR #12244](https://github.com/zeroclaw-labs/zeroclaw/pull/12244)** — 通过核心 RPC 的范围化仪表盘聊天（XL，do-not-merge，已阻塞）。长期运行的核心架构 PR。

底层需求：(a) 畅通的发布流水线，(b) 更低摩擦的远程登录，(c) 更广泛/更现代的渠道支持，(d) 整合重复的加密/依赖面。

## 5. Bug 与稳定性

按严重程度排序（除非注明，均为 2026-11-09 提交/更新）：

- **S1 / 工作流阻塞：** [#12320](https://github.com/zeroclaw-labs/zeroclaw/issues/12320) — `RUSTSEC-2026-0459` 在每次构建中导致 Security 任务失败；合并队列和发布陷入死锁。**尚无修复 PR。**
- **S0 安全（针对开放 PR head 的审查发现；修复 = 合并前修订 PR）：**
  - [#12311](https://github.com/zeroclaw-labs/zeroclaw/issues/12311) — macOS Seatbelt 配置文件授予沙箱命令读取所有 home dotfiles（包括 `~/.zeroclaw`）的权限（PR #12150）。
  - [#12310](https://github.com/zeroclaw-labs/zeroclaw/issues/12310) — MCP 配置控制桥接授予模型无限制的配置写入权限，且无审批（PR #12275）。
  - [#12309](https://github.com/zeroclaw-labs/zeroclaw/issues/12309) — `sessions_prompt` 目标范围未强制执行；PR head 无法编译（PR #12258）。
  - [#12308](https://github.com/zeroclaw-labs/zeroclaw/issues/12308) — 会话删除/TTL 后转录和载荷仍可读；无擦除路径。
  - [#12307](https://github.com/zeroclaw-labs/zeroclaw/issues/12307) — 网关会话丢失 `gw_` 命名空间 → 对等调用者可跨会话附加。
  - [#12306](https://github.com/zeroclaw-labs/zeroclaw/issues/12306) — Matrix Goal 父轮次绕过 `effort_routing` 允许列表（`cloud_escalation = never`）。
  - [#12305](https://github.com/zeroclaw-labs/zeroclaw/issues/12305) — 守护进程级成本限制覆盖可由非 Telegram 渠道上的任何允许列表对等方批准。
  - [#12304](https://github.com/zeroclaw-labs/zeroclaw/issues/12304) — colony 渠道回复将操作员消息泄露给外部成员（PR #11560）。
- **S2：** [#12330](https://github.com/zeroclaw-labs/zeroclaw/issues/12330) — 当 `zerocode-config.toml` 不可读/不可解析时，zerocode 静默回退到本地守护进程（存在错误端点连接风险）；[#12325](https://github.com/zeroclaw-labs/zeroclaw/issues/12325) — 文档规范 URL 指向发布目录，因此每次发布都会重排 SEO URL。
- **S3：** [#12347](https://github.com/zeroclaw-labs/zeroclaw/issues/12347) — zerocode 配置写入者可能丢失并发更新（读-改-写，无锁）；[#12326](https://github.com/zeroclaw-labs/zeroclaw/issues/12326) 和 [#12324](https://github.com/zeroclaw-labs/zeroclaw/issues/12324) — 已修剪版本和可索引的 rustdoc/404 页面出现文档 404。

## 6. 功能请求与路线图信号

- **无需配对仪式的远程访问** — #12317 的 OIDC 自助设备证书引导，按 [#12337](https://github.com/zeroclaw-labs/zeroclaw/issues/12337) 由操作员 opt-in 门控。一旦 #12320 清除，是 v0.9.0 的有力候选。
- **渠道广度与安全** — XMPP/OMEMO（#11847）、未授权入站消息的所有者通知（[PR #12346](https://github.com/zeroclaw-labs/zeroclaw/pull/12346)）、Matrix 路由加固（#12306）。
- **迁移/互操作** — OpenClaw 导入（#12348，已关闭）。
- **Web UX** — 配对设备的语音设置（[PR #12269](https://github.com/zeroclaw-labs/zeroclaw/pull/12269)）、折叠 Thinking 开关（#11919，已完成）、窄布局修复（#11933，已完成）。
- **凭证卫生** — 修剪被取代的 OIDC 凭证代次（[#12339](https://github.com/zeroclaw-labs/zeroclaw/issues/12339)）并定义 cert-without-CA 语义（[#12338](https://github.com/zeroclaw-labs/zeroclaw/issues/12338)）。

预测：下一个版本将以安全加固（沙箱、会话隔离、审批路径）、zerocode OIDC onboarding 和渠道工作为主——完全取决于 #12320。

## 7. 用户反馈摘要

- **挫败感：** 发布/CI 阻塞（#12320）是最响亮的信号——什么都无法落地；贡献者产出了经过验证的修复却无处合并。
- **安全敏感用户**（自托管/守护进程操作员）提出了具体的隔离关切：沙箱文件访问、跨会话附加、群聊审批滥用和消息泄露。这些读起来是真实的部署痛点，而非理论问题。
- **移动/Web 用户：** 对响应式和 UX 修复（#11933、#11920）表示满意，表明仪表盘在手机上有活跃使用。
- **Onboarding 摩擦：** zerocode 用户希望无配对登录，并愿意接受 opt-in 默认值（#12337）；后续 issue 的数量表明该功能被需要，但当前 head 尚未达到生产就绪。
- **静默失败焦虑：** #12330（配置解析失败 → 本地守护进程回退）和 #12347（丢失配置写入）是那种侵蚀 TUI 客户端信任的微妙降级。

## 8. 待办关注

- **[PR #11868](https://github.com/zeroclaw-labs/zeroclaw/pull/11868)**（自 2026-10-19 开放，needs-maintainer-review）— 调度前的重复调用抑制；阻塞相关 #11911，并有配套文档 PR #12265 等待处理。
- **[PR #11317](https://github.com/zeroclaw-labs/zeroclaw/pull/11317)**（自 2026-10-01 开放，XS 文档）— 有界插件 webhook RPC 放置例外；一个月前的一行文档 PR 仍未审查。
- **[PR #11847](https://github.com/zeroclaw-labs/zeroclaw/pull/11847)**（自 2026-10-19 开放，XL，needs-maintainer-review）— XMPP 渠道列车；需要审查带宽以避免相对 master 的位腐。
- **[PR #12244](https://github.com/zeroclaw-labs/zeroclaw/pull/12244)**（已阻塞，do-not-merge）— 通过核心 RPC 的仪表盘聊天；高风险且长期存在，面临冲突搅动风险。
- **[Issue #12320](https://github.com/zeroclaw-labs/zeroclaw/issues/12320)** — 不算旧，但最紧迫：它是阻塞整个合并队列和下一发布的基石。

---
*所有链接引用 `github.com/zeroclaw-labs/zeroclaw`。数据快照：2026-11-09/10 活动窗口。*

</details>

---
*本日报由 [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar) 自动生成。*