# OpenClaw 生态日报 2026-11-05

> Issues: 387 | PRs: 500 | 覆盖项目: 5 个 | 生成时间: 2026-11-04 23:22 UTC

- [OpenClaw](https://github.com/openclaw/openclaw)
- [Hermes Agent](https://github.com/nousresearch/hermes-agent)
- [IronClaw](https://github.com/nearai/ironclaw)
- [QwenPaw](https://github.com/agentscope-ai/QwenPaw)
- [ZeroClaw](https://github.com/zeroclaw-labs/zeroclaw)

---

## OpenClaw 项目深度报告

# OpenClaw 项目摘要 — 2026-11-05

---

## 1. 今日概览

OpenClaw 的维护者与贡献者吞吐量极高：过去 24 小时内有 **387 个 issue** 和 **500 个 PR** 被更新，其中 **194 个 PR 已合并/关闭**，并发布了 **1 个新的 beta 版本**（v2026.11.5-beta.1）。头条功能是引入 **“Knowledge Scribes”** —— 一套跨 agents、sessions 和 Control UI 构建的一流知识捕获工作流 —— 外加一个全新的 **PostgreSQL 支撑的状态平面**，标志着向企业级持久化迈进的战略举措。然而，issue 追踪器被 **P0/P1 稳定性问题**所主导（gateway 事件循环阻塞、Windows 服务不可靠、堆/RSS 失控、prompt-cache 回归），其中若干问题已持续 6–9 周仍未修复。项目显然处于高速开发阶段，但活跃 issue 中未关闭（226）与已关闭（161）的比例，以及 `clawsweeper:no-new-fix-pr` 标签的数量，表明维护债务的积累速度快于清理速度。

**活跃度评估：非常高（发布周 + 大量分诊量）。**

---

## 2. 发布

### v2026.11.5-beta.1 — `openclaw 2026.11.5-beta.1`

**亮点：**

- **Knowledge Scribes** — 新增一流的 知识捕获 和可复用的 “scribe” 工作流，横跨 agents、sessions 和 Control UI。通过六个 PR（#185583、#185637、#185974、#186270、#186455、#187659）交付，归功于 @jalehman。这是本版本的旗舰功能，标志着 OpenClaw 推动向持久、可复用的 agent 记忆工件而非临时会话上下文演进。
- **PostgreSQL 数据平面** — 新增 PostgreSQL 支撑的状态后端。*（提供的资料中发布说明被截断；完整的迁移/破坏性变更详情以及任何所需的配置/状态迁移步骤在本数据集中不可用，升级前应对照完整发布说明进行核实。）*

**运维人员注意事项：**
- 这是一个 **beta** 版本；使用 2026.10.x 的生产用户应关注后续 2026.11.x 修复，尤其是鉴于历史上伴随小版本升级出现的众多未关闭的状态迁移和插件重新物化 bug（例如 #161921、#162585）。
- 源数据中未提供明确的破坏性变更列表；**将 PostgreSQL 后端和插件状态迁移视为升级风险区域**，并在推出前审阅 #161921（`state-migrated-no-rollback`）。

---

## 3. 项目进展

**已合并/关闭的 PR（共 194 个；30 个 PR 窗口中的显著样本）：**

- **iOS/Control UI 打磨集群** — #158051（iOS 上 Cloudflare Access 嵌入式 Dashboard，已关闭）、#149321（Ask OpenClaw 旁的 Settings 布局，已关闭）、#144670（插件标签页内边距）、#132196（Sessions 标签页对齐）—— 由 @Patrick-Erichsen 等人进行的持续性 UI 一致性清理。
- **Web UI 稳健性** — #149581/#149169（Markdown 表格滚动性能）、#149981（Control UI 中 IndexedDB 不可用时的恢复）、#168524（Vitest 中单一 Lit 运行时 —— 减少 CI 不稳定的测试卫生）。
- **CI/依赖卫生** — #174510（vitest/vite CVE 升级，已关闭）、#193046（CLI fixture 与 Workshop 迁移隔离），以及来自 @vincentkoc 的若干 `fix(ci)` 隔离 PR。
- **依赖/导出清理** — #180693、#179811（来自定时 CI 报告的未使用 Control UI 导出）。

**值得关注的进行中功能工作（未关闭 PR）：**
- **GitHub 集成 → 插件迁移** — #192349（Plugin SDK host API 扩展）和 #192350（GitHub 仓库集成移至捆绑插件）。这是一次重大的架构重构；#192350 依赖 #192349 先合并。
- **Crabbox / 访客环境** — #193047（“Gateway 限定的访客 Crabbox 环境”）和 #193051（安全：在配置权限被撤销后停止设置）。
- **Boat CI 路由** — #193038（将符合条件的 Linux CI 作业通过 Boat 路由）。
- **QA-lab 契约** — #193020（分离的运行时对等数据契约）。

**评估：** 平台/插件架构（GitHub 插件迁移、插件 SDK）的功能速度强劲，而已合并工作的很大一部分是 UI 打磨和 CI 卫生 —— 这是一个项目在稳定 2026.11 发布前稳定化的典型特征。

---

## 4. 社区热点话题

按评论活动排序：

| 排名 | 项目 | 评论数 | 信号 |
|---|---|---|---|
| 1 | **[#160959](https://github.com/openclaw/openclaw/issues/160959)** — Gateway 在捕获大型外部插件时阻塞数分钟（P0，崩溃循环，自 2026.9.6 起的回归） | 70 | 🔥 参与度最高的 issue；插件捕获回归具有重大运营影响且仍未修复。 |
| 2 | **[#177656](https://github.com/openclaw/openclaw/issues/177656)** — 原生 host exec 在 2026.10.5 上返回共享状态完整性准入错误（P1） | 28 | 稳定版本上的共享状态/准入失败。 |
| 3 | **[#170956](https://github.com/openclaw/openclaw/issues/170956)** — Windows 启动缓慢、插件关闭不干净、schtasks 权限错误（P0） | 28 | Windows 可靠性集群；反复出现的主题。 |
| 4 | **[#120385](https://github.com/openclaw/openclaw/issues/120385)** — Code Mode 工具目录对 cron/memory-flush 轮次不完整（P2，作者已更正） | 25 | 长期存在的 issue，根因分析已修订；MCP 工具可见性缺口。 |
| 5 | **[#150635](https://github.com/openclaw/openclaw/issues/150635)** — 短期召回驱逐使 dreaming 深度阶段饥饿（P2） | 24 | 记忆/召回子系统正确性。 |
| 6 | **[#166634](https://github.com/openclaw/openclaw/issues/166634)** — 延迟回收检查点失败导致崩溃恢复后 agent 不可用 | 23 | 崩溃恢复持久性。 |

**底层需求分析：**
- **插件/会话状态完整性是主导主题** — #160959、#177656、#166634、#161921、#143640 和 #190307 都指向同一架构压力：共享状态 / 插件捕获 / 准入层在真实世界的插件规模和崩溃恢复下显得脆弱。用户正在用大型插件树运行生产工作负载，而启动捕获路径（#144252）是一次有意义的回归。
- **Windows 和 macOS 服务生命周期**（#170956、#169844、#191944、#163139）—— 一个持续的跨平台服务管理问题；用户无法可靠地重启或更新。
- **成本/效率痛点**（#161208、#182891、#178373、#161277）—— prompt-cache 未命中和每轮 token 开销导致真实美元成本（一位报告者：每次心跳 $0.40–0.95）。这是社区日益增长的关切。

---

## 5. Bug 与稳定性

**P0 / 严重（发布阻塞）：**

1. **[#160959](https://github.com/openclaw/openclaw/issues/160959)** — Gateway 事件循环在捕获大型外部插件时阻塞数分钟（2026.9.6 回归）。**无修复 PR。** 严重程度最高；影响启动和运行时重新发布。*70 条评论，约 5 周后仍开放。*
2. **[#170956](https://github.com/openclaw/openclaw/issues/170956)** — Windows 托管服务可靠性（schtasks、不干净关闭、启动缓慢）。**无修复 PR。**
3. **[#161921](https://github.com/openclaw/openclaw/issues/161921)** — `state-migrated-no-rollback` 在 `doctor --fix` 与 `plugins update` 之间死锁。**无修复 PR。** 阻塞升级。
4. **[#169844](https://github.com/openclaw/openclaw/issues/169844)** — Windows 上 Control UI Gateway 更新卡在“Activating”，导致计划任务被禁用。**无修复 PR。**

**P1 / 高严重程度：**

5. **[#177656](https://github.com/openclaw/openclaw/issues/177656)** — 2026.10.5 上原生 host exec 完整性准入错误。
6. **[#154812](https://github.com/openclaw/openclaw/issues/154812)** — V8 堆外的 RSS 失控 → 主机 OOM 和关闭超时。
7. **[#185578](https://github.com/openclaw/openclaw/issues/185578)** — 堆下限棘轮上升（约 25 MiB/h）直到 V8 上限中止。**无修复 PR。**
8. **[#148789](https://github.com/openclaw/openclaw/issues/148789)** — 模型回退链在 harness 级会话失败时耗尽所有候选，错误归因。
9. **[#190307](https://github.com/openclaw/openclaw/issues/190307)** — Gateway 重启使 agents 永久“降级”（准入超时）。**相关修复 PR：[#193049](https://github.com/openclaw/openclaw/pull/193049)**（开放，已就绪待维护者查看）。
10. **[#192141](https://github.com/openclaw/openclaw/issues/192141)** — 中断轮次续接丢弃原始用户消息文本。
11. **[#191401](https://github.com/openclaw/openclaw/issues/191401)** — Signal 在未完成的接收上永远挂起拆卸（fix-shape-clear，可排队）。

**回归 / 横切问题：**

- **[#182891](https://github.com/openclaw/openclaw/issues/182891)** — Prompt cache 从未被复用（`cache read dropped N -> 0`）—— 成本回归。
- **[#185656](https://github.com/openclaw/openclaw/issues/185656)（已关闭）** — zstd transcript 归档超过 Node 字符串限制；#170847 的回归持续到 2026.10.5。
- **[#162585](https://github.com/openclaw/openclaw/issues/162585)** — Windows 插件源捕获将插件重新物化到其自身的 `node_modules` 中；大型 npm 树线性爆炸（290 MB → 612 MB/24 分钟）。无修复。

**稳定性总结：** 在前约 20 个严重程度标记的 issue 中，**只有两个有相关的开放修复 PR**（#190307→#193049；#191401 标记为可排队）。其余的带有 `clawsweeper:no-new-fix-pr`，意味着自动分诊未找到候选修复 —— 这对 2026.11 稳定版发布是一个有意义的风险信号。v2026.11.5-beta.1 的发布说明**并未**列举 P0 集群的修复，因此运维人员不应假定它们已解决。

---

## 6. 功能请求与路线图信号

**最有可能在 2026.11.x 落地（近期）：**

- **Claude web_search 提供者** — [#192632](https://github.com/openclaw/openclaw/issues/192632)（2026-11-04 提交，P3，已有 9 条评论）。明确要求与 Gemini/Perplexity 提供者对等；表面面积小。**可能性高。**
- **选择性加入的反应触发 agent 轮次** — [#17840](https://github.com/openclaw/openclaw/issues/17840)（自 2026 年 2 月起，8 条评论，+1 👍）。长期存在的增强；作为选择加入标志是可行的。
- **Telegram `sendDice` 消息操作** — [#137110](https://github.com/openclaw/openclaw/issues/137110)（关联 PR 开放）。**可能很快。**
- **Control UI：在 `/new` 后浏览/分叉先前的频道聊天片段** — [#158800](https://github.com/openclaw/openclaw/issues/158800)。契合本版本宣布的 Knowledge Scribes 方向。

**中期 / 需要产品决策：**

- **`skills.workshop.allowSandboxedAgents`** — [#91676](https://github.com/openclaw/openclaw/issues/91676)（需要安全审查，2 👍）。沙箱策略放宽需要维护者安全签署。
- **Active Memory 升级提供者插件钩子** — [#151178](https://github.com/openclaw/openclaw/issues/151178)（关联 PR 开放）。
- **工具结果后内容注入的插件钩子** — [#164355](https://github.com/openclaw/openclaw/issues/164355)。可扩展性缺口；与 #192349 中的插件 SDK 扩展工作相关。
- **macOS/Talk 自动化管理员轮次** — [#139586](https://github.com/openclaw/openclaw/issues/139586)。
- **原生贡献者准入/生命周期契约** — [#158921](https://github.com/openclaw/openclaw/issues/158921)（已作为陈旧项关闭）。

**预测：** 2026.11.x 系列可能会优先考虑**提供者对等（Claude 搜索）、小型频道操作（Telegram 骰子、Matrix 媒体批处理 #161505）**和**插件 SDK 可扩展性**，而更大的功能（沙箱化技能工作坊、内存升级提供者）则等待安全/产品审查。**GitHub 集成为插件**重构（#192349/#192350）是进行中最具影响力的路线图项目，若合并，将实质性地重塑扩展模型。

---

## 7. 用户反馈总结

**痛点（不满）：**

- **升级和重启不可靠，尤其是在 Windows 和 macOS 上** — schtasks 权限错误、卡在“Activating”、macOS 上的 `ServiceInspectionError`（#170956、#169844、#191944）。多位用户报告需要多次重试（“pas na veel proberen”）。
- **成本低效** — 心跳和每轮信封重写上的 prompt-cache 未命中（#161208、#182891）；每轮 `requester_profile` 样板（#161277）；无运行级 token 预算和选择加入的循环检测（#178373）。用户为可避免的 token 支付真实费用。
- **幻影/重复消息和上下文泄漏** — #151962（来自内部运行时字符串的幻影用户提示）、#149153（最终回复渲染两次）、#165041（会话恢复注入在 UI 中可见）。这些侵蚀了对 transcript 保真度的信任。
- **静默失败** — #148308（Tailscale 声明静默丢失）、#150822（广告的工具无执行器）、#160610（Discord 尽管 bot 健康却“运行时降级”）。
- **记忆子系统正确性** — #150635（召回驱逐从不提升）、#176187（KNN 截止时间超限）、#143640（记忆发布阻塞 agent DB 写入）。

**积极信号 / 用例：**
- 对**多 agent、多渠道部署**的强劲需求（Telegram、Discord、WhatsApp、Signal、Matrix、WeChat 桥接均在评论中出现），通常有 3–50 个 agents 和长生命周期会话（一例为 13 天、约 310k token 的会话）。
- 用户正在推动**生产规模**配置（35 个 agents、284 MB SQLite 存储、18k 向量），这解释了反复出现的大型状态 bug —— 项目正被远超出业余规模的场景使用。
- 社区贡献者正在积极提交结构良好、带标签的报告（注意普遍存在的 `clawsweeper` 自动分诊元数据），表明用户基础参与度高且技术成熟。

**净情绪：** 挫败感集中在**运营可靠性和成本**上，被对新能力（Knowledge Scribes、插件生态系统）的热情所平衡。用户显然喜欢该产品，但正遭遇成熟规模下的运营问题。

---

## 8. 积压关注

长期未答复或关注不足、需要维护者注意的重要项目：

| Issue | 年龄 | 关切 |
|---|---|---|
| **[#91676](https://github.com/openclaw/openclaw/issues/91676)** — `skills.workshop.allowSandboxedAgents` 选择加入 | 约 5 个月（自 2026-06-09） | 等待安全审查；阻塞合法的沙箱扩展用例。 |
| **[#17840](https://github.com/openclaw/openclaw/issues/17840)** — 反应触发 agent 轮次 | 约 9 个月（自 2026-02-16） | 长期存在的增强，用例明确（emoji 轮询）。 |
| **[#160959](https://github.com/openclaw/openclaw/issues/160959)** — P0 gateway 事件循环阻塞 | 约 5 周，70 条评论 | **最高优先级的未修复 P0。** 2026.9.6 引入的回归；仍无修复 PR。 |
| **[#161921](https://github.com/openclaw/openclaw/issues/161921)** — state-migrated-no-rollback 死锁 | 约 5 周，P0 | 阻塞插件更新和升级；相互阻塞的命令是一种危险的失败模式。 |
| **[#162585](https://github.com/openclaw/openclaw/issues/162585)** — Windows 插件重新物化爆炸 | 约 5 周 | 大型 npm 树上的磁盘/时间爆炸；无修复。 |
| **[#154812](https://github.com/openclaw/openclaw/issues/154812)** — RSS 失控 → OOM | 约 7 周，P1 | 主机级中断风险；无修复。 |
| **[#164355](https://github.com/openclaw/openclaw/issues/164355)** — 工具结果后内容钩子缺口 | 约 1 个月 | 架构可扩展性缺口；与插件 SDK 工作相关。 |
| **[#150822](https://github.com/openclaw/openclaw/issues/150822)** — Codex children 广告工具而无执行器（安全审查） | 约 7 周 | 需要实时复现；潜在安全影响。 |

**需要维护者查看的 PR（就绪或接近就绪）：**

- **[#193049](https://github.com/openclaw/openclaw/pull/193049)** — 修复：启动准入期间的配置重载（P1，platinum hermit，已就绪待维护者查看）。*直接处理 #190307/#161921。*
- **[#191914](https://github.com/openclaw/openclaw/pull/191914)** — 修复：子进程 spawn 在 glibc < 2.31 上失败（P1，兼容性风险）—— 对 RHEL/CentOS/Rocky 8 用户重要。
- **[#191918](https://github.com/openclaw/openclaw/pull/191918)** — 澄清 Codex 可执行文件启动失败（小型，就绪）。
- **[#191517](https://github.com/openclaw/openclaw/pull/191517)** — iOS 原生播放恢复（草稿，等待原生验收）。
- **[#192350](https://github.com/openclaw/openclaw/pull/192350) / [#192349](https://github.com/openclaw/openclaw/pull/192349)** — GitHub 集成插件迁移（XL，需要安全审查，依赖有序）。

**建议：** P0 集群（#160959、#170956、#161921、#169844）代表了对 2026.11 稳定版发布的最明确风险。鉴于 24 小时内合并了 194 个 PR，维护者带宽显然很高 —— 但 `no-new-fix-pr` 标签在最高严重程度 issue 上的集中表明这些需要**专门的深度调查负责人**而非例行分诊。优先处理两个就绪的修复 PR（#193049、#191914）并为插件捕获/事件循环回归（#160959）指派负责人，将实质性地提升发布信心。

---

*数据来源：github.com/openclaw/openclaw，快照 2026-11-05。评论/反应计数如所提供；采样窗口的 PR 评论计数在源数据中不可用。*

---

## 横向生态对比

# 跨项目对比报告 — AI Agent 与个人助理开源生态
**快照日期：2026-11-05** · 项目：OpenClaw、Hermes Agent、IronClaw、QwenPaw、ZeroClaw

---

## 1. 生态概览

开源个人 AI 助理领域正围绕一套共同架构收敛——多通道网关（Telegram/Discord/Slack/Matrix）、可插拔的工具/技能与记忆后端、委派式多智能体执行，以及桌面/Control-UI 层——但各项目在成熟度与专注方向上差异显著。可以清晰看到三个成熟度梯队：**OpenClaw** 处于超大规模（11k+ 未关闭 issue，约 1,000 次 PR 事件/天），并怀有企业级野心（PostgreSQL 状态平面、插件 SDK 重构）；一个**稳定化梯队**（Hermes、QwenPaw、ZeroClaw）正在处理迁移或发布引发的回归，为整合版本做准备；以及处于低活跃维护模式的 **IronClaw**。在所有项目中，社区最主要的抱怨已从“缺少功能”转向**运行可靠性、安全隔离与成本效率**——这是该品类正从爱好者采用阶段（3–50 个智能体的家庭实验室、庞大的插件树、18 天网关在线时长）迈入生产使用的典型信号。插件生态正成为新兴的竞争护城河：黑客松驱动的目录增长（Hermes）、记忆插件后端（QwenPaw、OpenClaw Knowledge Scribes），以及注册表/市场诉求（ZeroClaw）都指向同一方向。

---

## 2. 活跃度对比

| 项目 | 更新的 issue 数（24 小时） | 更新的 PR 数（24 小时） | 已合并/关闭的 PR | 发布状态 | 健康评分 |
|---|---|---|---|---|---|
| **OpenClaw** | 387（226 个未关闭 / 161 个已关闭活跃） | 500 | 194 | **v2026.11.5-beta.1** 已发布（Knowledge Scribes、PostgreSQL 后端） | 🟡 **黄色** — 速度极高，但 P0 集群（4 个 issue）已 5–9 周未修复；最高严重度事项带有 `no-new-fix-pr` 标签 |
| **Hermes Agent** | 50 | 50 | 11 | 无；当前为 v0.22.5 / v0.21.6 | 🟡🟢 **黄绿色** — 贡献者动能强劲（插件黑客松），>1,000 个未关闭 issue，P1 静默失败类问题仍开放 |
| **IronClaw** | 0 | 1 | 0 | 无 | ⚪ **安静/稳定** — 维护模式；1 个 Dependabot PR（zip 8→9 主版本升级）；数据快照可能不完整 |
| **QwenPaw** | 10 | 50 | 13（关闭 3 个 issue） | 无；正围绕 **2.3.0b2** beta 迭代 | 🟢 **绿色** — 良好但噪音较多；3 个 2.3.0b2 高严重度回归，其中 2 个已有修复 PR 在处理中 |
| **ZeroClaw** | 16 | 50 | 4 | 无；**v0.9.0 发布门禁**正在集结 | 🟢 **绿色（含风险）** — 安全加固有纪律；46 个未关闭 PR = 评审带宽瓶颈 |

---

## 3. OpenClaw 的定位

**规模与参照地位。** OpenClaw 的吞吐量大约**比同类项目高一个数量级**（每日更新 387 个 issue / 500 个 PR，而其他项目为 10–50；每日 194 次合并）。它实际上是参照实现：11k+ 未关闭 issue 既说明采用规模巨大，也说明作为生态重心的负担。

**相对同类的优势：**
- **架构野心领先于领域** — 唯一交付企业级持久化层（PostgreSQL 状态平面）和正式插件 SDK host-API 迁移（GitHub 集成 → 内置插件，#192349/#192350）的项目。同类项目仍停留在基于文件/插件目录的模式。
- **旗舰差异化** — “Knowledge Scribes”（一等公民的可复用知识捕获）是同类项目无法匹敌的产品级功能；QwenPaw 的记忆插件工作和 Hermes 的 `auxiliary.skills_hub` 混乱表明同一需求被以更不连贯的方式处理。
- **贡献基础设施** — 自动化分诊（`clawsweeper` 元数据）、密集的 CI 卫生和专门的深度剖析 issue 文化，吸引了技术成熟的生产用户（35 个智能体、284 MB SQLite 部署）。

**技术路径差异：**
- 更重、更单体化的核心，插件捕获在网关启动时进行——这是其最严重 P0 的直接成因（#160959：大型外部插件捕获阻塞事件循环），这是一种更轻量同类项目可避免的脆弱性模式。
- 发布节奏采用 **beta 优先的次要版本线**（2026.11.5-beta.1），伴随状态迁移风险（#161921 `state-migrated-no-rollback` 阻塞升级）——这是同类项目尚未面临的企业级权衡。

**社区对比。** OpenClaw 的用户群生产规模最大（最大的插件树、最长的会话——一个 13 天/310k token 的会话），对成本的要求也最高（prompt-cache 回归 $0.40–0.95/心跳）。Hermes 拥有**最高的人均贡献者动能**（黑客松插件浪潮、`od -c` 质量的报告）；ZeroClaw 的反馈者最懂安全；QwenPaw 拥有最强的**首次贡献者管道**；IronClaw 基本沉寂。

**注意事项：** OpenClaw 的速度掩盖了维护债务——在其约 20 个最高严重度 issue 中，只有**两个**有开放的修复 PR。作为参照实现目前既是该项目最大的资产，*也是*其最大的发布风险（2026.11 稳定版）。

---

## 4. 共性技术焦点领域

五项需求在 ≥3 个项目中反复出现，表明存在真正的品类级压力：

| 需求 | 项目 | 具体信号 |
|---|---|---|
| **会话/状态完整性与崩溃恢复** | OpenClaw、Hermes、ZeroClaw、QwenPaw | OpenClaw #160959/#161921/#166634（共享状态准入、无回滚死锁）；Hermes #145807（损坏的 `active_sessions.json` 阻塞所有会话）、#148762（双配置会话 ID）；ZeroClaw RFC #10526（仅追加事件历史 + 确定性重放——架构层面的答案） |
| **委派工具的主体/租户隔离** | ZeroClaw、OpenClaw、Hermes | ZeroClaw #11198（记忆工具丢失主体作用域）、#11220（SOP 需要管理员权限）、#12227（模型写入的“approved”值）；OpenClaw #150822（工具被通告但没有执行器）；Hermes 委派子智能体可观测性 #149110 |
| **消除静默失败** | Hermes、QwenPaw、OpenClaw | Hermes #149085（命令被静默丢弃）、#149107（备用模型被悄悄替换）、#130272（审批超时被误标为“拒绝”）；QwenPaw #8278/#8579/#8580（Markdown 降级、通用“Internal error”、标签 503）；OpenClaw #148308/#150822 |
| **成本与上下文效率** | OpenClaw、Hermes、QwenPaw | OpenClaw prompt-cache 未命中 #182891、每轮 token 开销 #161208/#161277、无 token 预算 #178373；Hermes 压缩 bug #107628；QwenPaw cron 会话边界 #8557、媒体边界 #8034 |
| **可插拔记忆后端** | QwenPaw、OpenClaw、Hermes | QwenPaw OpenViking 插件 #7613 + Piccolo 运行时 #8573；OpenClaw Knowledge Scribes + memory-flush 召回问题 #150635；Hermes `skills_hub`/plugin-settings UX |
| **升级/迁移安全护栏** | Hermes、OpenClaw、ZeroClaw | Hermes 统一网关影响追踪 #143547（47 条评论）、#143150 存储路由；OpenClaw #161921 升级死锁；ZeroClaw #11579 schema 版本戳记数据丢失 |

---

## 5. 差异化分析

| 维度 | OpenClaw | Hermes Agent | QwenPaw | ZeroClaw | IronClaw |
|---|---|---|---|---|---|
| **重心** | 核心平台：智能体、会话、Control UI、插件 SDK | 统一多路复用网关 + 桌面 UX（简化模式） | 多通道广度（钉钉/飞书/微信/Matrix E2EE）+ 控制台插件主题化 | **安全优先**：主体作用域、SOP 权限、OIDC、确定性重放 | 信号极少 |
| **目标用户** | 生产规模运营者（10–50+ 智能体、大型插件树） | 多配置高级用户、黑客松/插件构建者 | 中文生态与多工作区通道用户；首次贡献者 | 自托管、注重安全、多租户部署 | 无法观测 |
| **架构信号** | 单体核心 + PostgreSQL 状态平面 + beta 优先发布线 | 多路复用配置/网关（迁移后、会话谱系围栏 PR） | 2.3.0b2 beta 迭代；按运行的 cron 会话；记忆生态主题 | Rust；RFC 驱动的仅追加事件账本；评审门禁的安全链 | Rust（zip crate）；休眠 |
| **独特优势** | Knowledge Scribes；插件 SDK host API | 插件目录速度 + 终端内图像预览 | 通道对等 + E2EE；贡献者入门 | 隔离保证（24 小时内关闭 3 个 P0/S0 安全事项） | 24 小时内无显现 |
| **已知盲点** | P0 未修复积压；Windows/macOS 服务生命周期 | 迁移长尾；静默失败 | 2.3.0b2 回归（TOPMOST、provider-disable） | 长期存在的 Tokio 栈溢出 #9572（3+ 个月） | 反馈渠道沉寂 |

**关键差异：** OpenClaw 与 Hermes 在**平台可扩展性**（智能体、插件、桌面 UX）上竞争；QwenPaw 在**通道覆盖**上竞争；ZeroClaw 在**信任与可审计性**上竞争——这一定位在战略上是互补而非正面交锋，且随着部署走向多租户，生态将需要这种能力。

---

## 6. 社区动能与成熟度

**第一梯队 — 超大规模迭代（OpenClaw）。** 发布周 + 每日 194 次合并。在稳定 UI/CI 的同时快速迭代平台架构（GitHub-as-plugin 重构、PostgreSQL）。成熟度风险：维护债务积累速度快于清理速度；需要在 2026.11 稳定版切分前为 P0 集群配备专门的深度剖析负责人。

**第二梯队 — 通过迁移/发布门禁稳定化（Hermes、QwenPaw、ZeroClaw）。**
- **Hermes** 处于迁移后阶段（统一网关，10 月 14 日合并）：下一个版本承载过高期望（整合 + 迁移指引 + 会话所有权围栏 PR #149055/#149132）。生态动能是本组最佳。
- **QwenPaw** 处于干净的 beta 稳定化窗口（2.3.0b2）：大多数高/中回归是 beta 特有的，两个 Windows Hub 修复已在处理中，首次贡献者流健康。有望迎来绿色的 2.3.0 正式版。
- **ZeroClaw** 正在为 v0.9.0 做发布门禁，安全关闭有纪律（P0/P1 数据丢失事项在 24 小时内修复）——强烈的健康信号——但 46 个未关闭 PR 带有堆叠的、受评审门禁的依赖，使**维护者评审带宽成为吞吐上限**。最高结构性风险：3 个月之久的 #9572 无修复。

**第三梯队 — 维护模式（IronClaw）。** 零 issue，一个 Dependabot 主版本 PR。无回归信号，但也无前进动能；稀疏数据集可能不完整，因此参与度结论置信度低。

**头条指标：** OpenClaw 的 500 次 PR 更新 vs. Hermes/QwenPaw/ZeroClaw 各约 50 次，确认 OpenClaw 为参照节点，而第二梯队三项目共同代表生态的**活跃稳定化与创新前沿**（黑客松插件、E2EE、安全加固）。

---

## 7. 趋势信号

**1. 可靠性是新的功能战场。** 每个活跃项目参与度最高的 issue 都是运维性的：升级死锁（OpenClaw #161921）、损坏的状态文件阻塞所有会话（Hermes #145807）、provider-disable 通用错误（QwenPaw #8579）、主体泄漏（ZeroClaw #11198）。*对开发者的价值：* 将**迁移/回滚方案、状态 schema 校验和可操作的错误界面**作为一等交付物——静默降级（Hermes #149085/#149107、QwenPaw #8278/#8580）惩罚最重，因为它破坏了人们对智能体基础设施的信任。

**2. 信任边界正移入模型的执行路径。** ZeroClaw 的发现——shell 审批可由模型写入的值驱动（#12227），委派工具可逃逸主体作用域（#11198/#12232）——加上 Hermes 的审批超时被误标为拒绝（#130272）和 OpenClaw 的工具被通告但无执行器（#150822），定义了一类新的**智能体原生安全 bug**——既非传统应用安全，也非提示注入，而是“工具调用边界处的权限混淆”。*对开发者的价值：* 从第一天起就将主体/权限传播构建进工具执行契约；ZeroClaw 的仅追加事件账本加确定性重放（RFC #10526）是新兴的最强可审计性模式。

**3. 成本可观测性是一项未被满足的产品需求。** Prompt-cache 回归和每轮开销正产生真实的美元抱怨（OpenClaw $0.40–0.95/心跳）。*对开发者的价值：* token/成本预算、缓存命中遥测和循环检测是差异化平台功能，而非优化项。

**4. 插件是生态护城河——也是下一个脆弱性来源。** 黑客松目录（Hermes）、记忆后端（QwenPaw/OpenClaw）、注册表共存（ZeroClaw #12109→#12216），以及插件 SDK host-API 迁移（OpenClaw #192349/#192350）表明插件生态将是锁定与排除的决定之地。同时，OpenClaw 的 #160959（插件捕获阻塞事件循环）和 #162585（Windows 重新物化撑爆磁盘）警告：**插件生命周期隔离——捕获、物化、沙箱化——是为规模而设计的关键子系统**，而非事后补丁。

**5. 可编辑、可回滚、可重放的会话。** QwenPaw #7997（消息撤回/回滚，参与度最高）、OpenClaw Knowledge Scribes + 分叉通道片段（#158800）、Hermes 会话谱系围栏（#148762），以及 ZeroClaw 的重放 RFC 汇聚于一项需求：**用户将智能体对话视为工作区，而非仅追加日志。** *对开发者的价值：* 对会话状态的快照/截断/回滚原语将成为下一代平台的基本要求。

**6. Windows 与受限环境是隐藏的采用税。** 持续的 Windows 可靠性集群（OpenClaw #170956/#162585、QwenPaw #8565、ZeroClaw #12119/#12218）、沙箱化 NAS（QwenPaw fnOS #8515）和 Termux（Hermes #149130）表明 Unix 桌面/服务器之外存在显著的真实需求。跨平台服务管理（schtasks、launchd/TCC、AppContainer）在各项目普遍系统性地投入不足——这对任何率先解决它的项目都是开放机会。

---

*方法论说明：健康评分综合自发布节奏、最高严重度 issue 的修复 PR 覆盖率、分诊延迟和贡献者广度。IronClaw 的结论因异常稀疏的快照（1 个 PR、0 个 issue、评论数未定义）而置信度低。*

---

## 同赛道项目详细报告

<details>
<summary><strong>Hermes Agent</strong> — <a href="https://github.com/nousresearch/hermes-agent">nousresearch/hermes-agent</a></summary>

# Hermes Agent — 项目摘要
**日期：2026-11-05** · 来源：github.com/NousResearch/hermes-agent

---

## 1. 今日概览

Hermes Agent 仍处于高活跃度的迁移后稳定化阶段。活动强劲且持续：过去 24 小时内共触及 50 个 issue 和 50 个 PR，其中 4 个 issue 已关闭，11 个 PR 已合并/关闭，但未发布新版本。当前主导议题仍是 **统一网关迁移（#106742，10 月 14 日合并）** 的后续影响——今日头号 issue（#143547，47 条评论）明确是一个跨网关、cron、插件、Desktop、Discord、Telegram、auth、sessions 和 profiles 的合并后问题追踪帖。Issue 追踪器已突破 **1,000+ 个未关闭 issue**（现已关闭的元 issue #7335），并且社区明显担忧分诊负载相比 OpenClaw（11k+ 未关闭 issue）等同类项目过重。社区活力很高——仅今天就带来了 **8+ 个插件目录 PR**（一场黑客松驱动的浪潮），表明即使在核心稳定性工作持续进行的同时，可扩展性生态依然健康。

---

## 2. 发布

**无。** 过去 24 小时无新版本发布。注：近期 bug 报告引用 **v0.22.5** 和 **v0.21.6** 作为当前发行版本，且至少有一个修复（#148759）专门针对旧版 git 上退化的更新检查行为——值得关注下一个版本。

---

## 3. 项目进展

**今日合并/关闭的 PR：** 11 个（合并/关闭合计）。Issue 追踪器中值得注意的关闭项：

- **#6675 [已关闭]** — 终端内截图与工具结果图片预览。一个自 4 月起长期存在的 CLI UX 请求现已解决。
- **#40703 / #142403 [已关闭]** — `auxiliary.skills_hub` 配置困惑已解决：该设置在选择器中有文档和暴露，但没有任何代码路径读取它。作为过期/保留文档清理而关闭。

**活跃功能推进（审查中的开放 PR）：**
- **#148153** — Desktop：可选的“简化 agent 模式”侧边栏，带嵌套聊天（固定聊天 → 网关 → agent → 聊天）。与 **#148247**（agent 设置概览）堆叠。
- **#149145** — Agent：上下文边界现在会重置工具循环守卫的无进展证据（针对压缩后循环的正确性修复）。
- **#149141** — 语音：超限的实时语音窗口现在保留 *最新* 轮次（此前保留头部）。
- **#149140** — 技能：在 Windows 写入时保留 LF 行尾（修复虚假的全文件 diff）。
- **#149099** — Chat Completions 空中工具调用 assistant 内容的线格式规范化。

**生态增长：** 今日 **插件目录 PR** 出现显著爆发：`undo-effects`（#149148）、`CaMeL Guard 1.1.0`（#149147，长期运行的 #3987 的配套）、`agentmail-frontdoor`/`odoo-prod-guard`/`fleet-memory`（#149142）、`voice-gate 1.3.5`（#149072）、`guardrails` 硬性支出限制插件（#149133），以及一个插件设置 UX 修复（#149134）。这强烈表明有一场活跃的 **Plugin Hackathon**（在 #149148 中被明确提及）在驱动社区贡献。

---

## 4. 社区热点

| 条目 | 评论数 | 链接 | 底层需求 |
|---|---|---|---|
| **#143547** 统一网关合并后问题 | **47** 👍1 | [链接](https://github.com/NousResearch/hermes-agent/issues/143547) | 最大的协调帖。跨网关、cron、插件、Desktop、Discord、Telegram、auth、sessions、profiles 的横切故障。用户需要 **迁移/回滚方案** 和统一的修复追踪器。 |
| **#7335** 超过 1000 个未关闭 issue（已关闭） | 22 | [链接](https://github.com/NousResearch/hermes-agent/issues/7335) | 社区对 **分诊规模** 和维护者带宽的焦虑；虽已关闭，但释放出流程关切信号。 |
| **#143150** Desktop 在 multiplex 迁移后恢复会话时 `storage_unavailable` | 7 | [链接](https://github.com/NousResearch/hermes-agent/issues/143150) | 具体的迁移破坏：独立→multiplex profile 迁移导致 `state_meta` 路由未迁移。用户需要 **安全的升级路径**。 |
| **#138105** Multiplex cron ticker 饿死 Discord 心跳 | 7 | [链接](https://github.com/NousResearch/hermes-agent/issues/138105) | multiplex 模型的性能/可扩展性：每 60 秒 O(profiles) 的工作量。需要缓存/退避。 |
| **#142574** 秘密脱敏器损坏非秘密内容 | 5 | [链接](https://github.com/NousResearch/hermes-agent/issues/142574) | 信任/安全张力：过度激进的脱敏破坏工具结果（`PASS=$VAR`、构建戳记），并可能将 `***` 占位符泄漏到磁盘。 |

**分析：** 社区对话被 **迁移成熟度** 主导，而非新功能。统一网关在架构上有野心，但产生了大量边缘情况回归（存储路由、Discord 心跳饿死、会话血缘）。插件 PR 量表明生态在核心动荡 *之下* 依然繁荣——一个积极的健康信号。

---

## 5. Bug 与稳定性

按严重性/影响排序：

**P1 级（数据完整性 / 静默失败）：**
- **#145807** — `active_sessions.json` 被一个杂散的 TLS `close_notify` 记录损坏；**网关拒绝每一个新会话**，直到手动删除文件。影响半径大。尚无关联修复 PR。[链接](https://github.com/NousResearch/hermes-agent/issues/145807)
- **#149085** — 终端预执行守卫 **静默卡死，丢弃命令**（每天 61 次，长期运行的 macOS 网关上泄漏 90 个线程）。静默命令丢失是正确性隐患。[链接](https://github.com/NousResearch/hermes-agent/issues/149085)
- **#107628** — 压缩在非标量内容部分 `type` 上崩溃，报 `TypeError: unhashable type: 'dict'`；会话变得 **永久不可压缩**。长期存在（9 月 10 日）。[链接](https://github.com/NousResearch/hermes-agent/issues/107628)

**P2 级（功能回归）：**
- **#143150** — Desktop 在 multiplex 迁移后恢复时 `storage_unavailable`。[链接](https://github.com/NousResearch/hermes-agent/issues/143150)
- **#148762** — 同一会话 ID 在两个 profile 存储中未结束；Desktop/CLI 恢复时较短血缘被竞态抢先显示。**已有修复 PR：#149055 和 #149132。** [Issue](https://github.com/NousResearch/hermes-agent/issues/148762) · [PR #149055](https://github.com/NousResearch/hermes-agent/pull/149055) · [PR #149132](https://github.com/NousResearch/hermes-agent/pull/149132)
- **#138105** — Cron ticker 饿死 Discord 心跳。[链接](https://github.com/NousResearch/hermes-agent/issues/138105)
- **#142574** — 秘密脱敏器损坏非秘密内容（间歇性，`***` 两次到达磁盘）。[链接](https://github.com/NousResearch/hermes-agent/issues/142574)
- **#130272** — 审批 **超时被报告给 agent 为“User denied”**——对处理敏感命令的 agent 而言是危险的错误信号。[链接](https://github.com/NousResearch/hermes-agent/issues/130272)
- **#148958** — Kanban worker 的网关所有者停滞退出被计为崩溃；连续两次即阻塞卡片。[链接](https://github.com/NousResearch/hermes-agent/issues/148958)
- **#149107** — `_api_kwargs_have_image_parts` 在带 `"type"` 属性的 JSON schema 上抛出 TypeError——**崩溃静默激活回退模型**（用户不知情的模型切换）。[链接](https://github.com/NousResearch/hermes-agent/issues/149107)
- **#149139** — 从 `~/Downloads`/`~/Desktop` 拖放 macOS Desktop 文件失败，报 `storage_unavailable`（自连接到 launchd 网关以来的 TCC/EPERM）。**回归。** [链接](https://github.com/NousResearch/hermes-agent/issues/149139)
- **#149057** — `context_compression_timeout` 自动重置使长期运行的 bot 聊天搁浅（血缘遍历器漏掉 `session_reset`）。[链接](https://github.com/NousResearch/hermes-agent/issues/149057)
- **#148759** — 在 git < 2.44 上 `hermes --version` 耗时约 40 秒（受影响的是 macOS 自带 git 2.39）。[链接](https://github.com/NousResearch/hermes-agent/issues/148759)
- **#149150** — Slack 在重启后对已应答消息回复 `admission_conflict` 错误。[链接](https://github.com/NousResearch/hermes-agent/issues/149150)

**进行中的修复 PR：** #149145（工具循环守卫重置）、#149137（kanban 回执超时协调，修复 #149126）、#149120（重连后批量澄清）、#89978（TUI 轮次状态真实性）、#149141（语音窗口）、#149140（Windows LF）、#149099（线格式规范化）。

**评估：** 稳定性负载偏高，且集中在 multiplex 网关和会话状态层。令人鼓舞的是，多数 P2 项要么有开放的修复 PR，要么已界定范围。**P1 静默失败类**（#145807、#149085、#149107、#130272）值得维护者优先处理——对 agent 基础设施而言，静默的错误远比响亮的崩溃代价更高。

---

## 6. 功能请求与路线图信号

**可能的下一版本（接近完成的活跃 PR）：**
- **简化 agent 模式** 侧边栏 + agent 设置概览（Desktop）——#148153、#148247。打磨过的 UX 栈很可能一起落地。
- 模型行上的 **提供商路由可见性**——#149144（多路由同模型设置的透明度）。
- **工具循环守卫正确性**——#149145（健壮性，可能很快合并）。
- **语音上下文窗口修复**——#149141。

**已请求、尚未实现：**
- **#149149** — 可配置的 Kanban 评审返工轮次上限（`kanban.max_review_rounds`）。直接回应无限的 `review_requested → changes_requested` 循环；#90574（无断路器）佐证。[链接](https://github.com/NousResearch/hermes-agent/issues/149149)
- **#149110** — 委派可观测性：子 agent 静默死亡、无停滞检测、实时监控薄弱、无逐子指标。**agent 编排可观测性** 的强路线图信号。[链接](https://github.com/NousResearch/hermes-agent/issues/149110)
- **#149146** — 为修订后的预检校验澄清终端审批超时语义。[链接](https://github.com/NousResearch/hermes-agent/issues/149146)
- **#149143** — 在无法配置必需检查的仓库（所有免费层私有仓库）上无法进行 Kanban PR 验收。[链接](https://github.com/NousResearch/hermes-agent/issues/149143)

**预测：** 下一版本可能捆绑 Desktop 简化模式、会话所有权围栏 PR（#149055/#149132）、工具循环/语音/Windows 修复，鉴于两个佐证 issue，还可能有 `kanban.max_review_rounds`。委派可观测性（#149110）是再下一个版本的强候选。

---

## 7. 用户反馈摘要

**痛点（不满）：**
- **迁移复杂性。** 多位用户（Relogys、kyanite-factory、KahChunRSGX、joerghaller）报告迁移到 multiplex 网关模型破坏了可用的设置——会话恢复、macOS 文件拖放、profile 迁移。用户想要 *更清晰的升级路径和安全护栏*。
- **静默失败侵蚀信任。** 命令被静默丢弃（#149085）、回退模型未经通知激活（#149107）、脱敏器损坏内容（#142574）、审批超时被误标为拒绝（#130272）。反复出现的主题：*“我需要知道出问题时出了什么问题。”*
- **分诊规模。** #7335 反映了社区担忧 1,000+ 未关闭 issue 正变得难以管理。
- **长期部署退化。** #149085（18 天运行时间的网关，90 个泄漏线程）和 #149057（搁浅的 bot 聊天）显示生产用户随时间遇到资源泄漏和状态问题。

**观察到的用例：** 生产网关拥有的会话，带 5–10 个并发委派子 agent（#149110）、多 profile 安装、Matrix/Discord/Telegram/Slack bot、macOS 桌面工作流、Termux/Android 安装（#149130——仓库密钥下载失败）。

**满意信号：** 贡献者活力非常高——黑客松插件 PR、带 `od -c` 转储和 commit 哈希的详细可复现 bug 报告，以及构建生产工具的用户（支付护栏、CaMeL 安全守卫、撤销中间件）。社区投入很深；诉求是作为回报获得 **稳定性与可预测性**。

---

## 8. 积压关注

需要维护者关注的条目：

| 条目 | 年龄 | 重要性 |
|---|---|---|
| **#107628** 压缩 `TypeError` → 永久不可压缩会话 | 约 8 周（9 月 10 日） | 有数据持久性影响的 P2 崩溃；#104840 只修复了 3 处中的 1 处。需要完成。[链接](https://github.com/NousResearch/hermes-agent/issues/107628) |
| **#130272** 审批超时被报告为“User denied” | 约 5 周（10 月 1 日） | 安全关键的误信号；无可见修复 PR。[链接](https://github.com/NousResearch/hermes-agent/issues/130272) |
| **#90574** Kanban 评审循环无断路器 | 约 11 周（8 月 20 日） | 被今日 #149149 佐证；配置上限是一个小而有价值的修复。[链接](https://github.com/NousResearch/hermes-agent/issues/90574) |
| **#3987** CaMeL Guard 插件 PR | **约 7 个月（3 月 30 日）** | 长期存在的安全插件 PR，今日刷新到新基线（#149147 依赖它）。需要维护者决策。[链接](https://github.com/NousResearch/hermes-agent/pull/3987) |
| **#89978** TUI 轮次状态真实性 PR | 约 11 周（8 月 19 日） | 修复“死 worker 将会话钉为工作中”——用户可见的困惑；仍开放。[链接](https://github.com/NousResearch/hermes-agent/pull/89978) |
| **#7335** Issue 增长控制（已关闭） | — | 关闭时无明显流程变更；建议公开分诊/标签路线图回应。 |
| **#149130** Termux 安装受阻（apt 密钥下载失败） | 新增 | 平台覆盖阻塞；可能是 `hermes-assets.nousresearch.com` 上的基础设施/配置修复。[链接](https://github.com/NousResearch/hermes-agent/issues/149130) |

---

### 健康判定
**黄绿色。** 生态势头极佳（插件黑客松、密集高质量报告、活跃修复 PR），团队也在关闭条目（图片预览、skills_hub 歧义）。然而，统一网关迁移的长尾——加上一组 **静默失败 P1 bug** 和超过 1,000 的 issue 计数——意味着项目近期健康取决于发一个带清晰迁移指南的整合版本，并优先处理静默错误类。鉴于此背景，24 小时无发布没问题，但下一个版本承载着超额期望。

</details>

<details>
<summary><strong>IronClaw</strong> — <a href="https://github.com/nearai/ironclaw">nearai/ironclaw</a></summary>

# IronClaw 项目摘要 — 2026-11-05

## 1. 今日概览

IronClaw 今日处于低活跃状态，过去 24 小时内零 issue 更新，也没有新版本发布。唯一的活动是一个开放的 Dependabot 拉取请求（#8168），将 Rust `zip` crate 从 8.6.0 升级到 9.0.5，这是一次大版本跳跃，值得审查。由于没有合并的 PR、没有关闭的 issue、也没有社区讨论，今日数据反映的是维护模式运营，而非活跃的功能开发。整体项目健康度看起来稳定但安静——没有回归或紧急 bug 的信号，但过去 24 小时内也没有前进动力的证据。

## 2. 版本发布

报告窗口内无新版本发布。

## 3. 项目进展

今日没有拉取请求被合并或关闭，也没有 issue 被解决。唯一的变动是 PR #8168（依赖升级）的开启/更新，该 PR 尚未被审查或合并。在这 24 小时窗口内，没有功能推进，也没有修复落地。

## 4. 社区热点

- **[PR #8168 — chore(deps): bump zip from 8.6.0 to 9.0.5](https://github.com/nearai/ironclaw/pull/8168)**（作者：dependabot[bot]；开启于 2026-11-04；0 条评论，0 👍）
  - 唯一活跃条目。这是一次针对 `zip` crate 的自动化依赖更新，跨越了大版本边界（8.x → 9.x）。PR 摘要引用了 v9.0.5 中的上游 bug 修复，但所提供数据中评论数不可用（"undefined"），因此无法评估社区反应。
  - **底层需求：** 常规依赖卫生与安全/维护时效性。由于这涉及大版本跳跃，维护者在合并前应核实变更日志中的破坏性 API 变更——跨大版本的自动化升级是静默破坏的常见来源。

当前数据集中不存在其他 issue 或 PR，因此没有额外的热点话题、反应或评论线程可供分析。

## 5. Bug 与稳定性

过去 24 小时内没有报告 bug、崩溃或回归。唯一的开放 PR 是依赖维护更新，而非 bug 修复。注意：`zip` 8→9 大版本升级（PR #8168）若未经 CI 验证即合并，是潜在的稳定性风险，但其本身并非已报告的 bug。没有修复 PR 关联到任何已报告的缺陷，因为不存在此类缺陷。

## 6. 功能请求与路线图信号

今日没有用户请求的功能被提交或讨论，因此 issue 跟踪器中没有路线图信号。唯一的前瞻性条目是 PR #8168 中的依赖升级，若合并，将使归档处理依赖现代化。仅凭今日数据无法对下一版本功能做出预测。

## 7. 用户反馈摘要

本窗口内没有用户反馈可总结：零 issue、零评论、唯一 PR 上零反应。没有浮现任何痛点、用例或满意度信号。这种缺失本身就是一个数据点——要么项目用户群当前未提交 issue，要么反馈渠道处于安静状态。

## 8. 积压关注

所提供数据集仅包含一个条目，且为近期创建（2026-11-04），因此没有长期未答复的 issue 或 PR 需要标记。**PR #8168**（[链接](https://github.com/nearai/ironclaw/pull/8168)）是本周期唯一需要维护者关注的条目，主要是审查大版本依赖升级。当前数据中看不到陈旧或被忽视的积压条目。

---

**数据说明：** 数据集报告 0 个 issue、整体仅 1 个 PR，对于一个已达 PR #8168 的项目而言异常稀疏。PR #8168 上"Comments: undefined"字段以及任何 issue 记录的缺失，表明数据快照可能不完整或经过过滤；在获得更完整数据集之前，关于社区参与度的结论应谨慎对待。

</details>

<details>
<summary><strong>QwenPaw</strong> — <a href="https://github.com/agentscope-ai/QwenPaw">agentscope-ai/QwenPaw</a></summary>

# QwenPaw 项目摘要 — 2026-11-05

## 1. 今日概览

QwenPaw 仍处于高速稳定化阶段：过去 24 小时内有 **50 个 PR 和 10 个 Issue** 被更新，但**未发布新版本**。活动主要偏向缺陷修复和对即将到来的 **2.3.0 beta 线**（2.3.0b2）的加固，多个 Windows、沙箱、通道和提供商配置的稳定性回归问题被报告，且已有若干修复 PR 提交。社区贡献健康——至少有四个首次贡献者 PR 活跃——维护者也在以合理节奏通过修复关闭 Issue（今日关闭 3 个 Issue）。整体健康度**良好但较为嘈杂**：核心正在成熟，而 beta 暴露出较少使用环境（沙箱化 NAS、AppContainer、受限 shell）中的边缘脆弱性。

---

## 2. 发布

**过去 24 小时内无新版本发布。** 项目实际上以 **2.3.0b2** 作为当前 beta 基线进行迭代；今日多个 Issue 直接引用此版本，表明正处于 2.3.0 稳定化窗口。待 Windows 和提供商配置缺陷解决后，关注下一个 beta/RC。

---

## 3. 项目进展

今日更新的已关闭/已合并条目（合并/关闭 13 个 PR，关闭 3 个 Issue）：

- **[#7613] feat(memory): 新增 OpenViking 记忆插件**（CLOSED，XXL，首次贡献者）——新增 OpenViking 记忆后端，支持自动召回、完成轮次持久化，以及通过 OpenViking REST API 的显式 `memory_search` 工具。一项重要的新记忆插件能力。[链接](https://github.com/agentscope-ai/QwenPaw/pull/7613)
- **[#8575] fix(desktop): 将自动更新清单限制为稳定版本**（CLOSED）——使桌面自动更新与浏览器控制台的仅稳定版策略对齐；预发布版仍仅限手动下载。减少意外的 beta 采用。[链接](https://github.com/agentscope-ai/QwenPaw/pull/8575)
- **[#8422] feat(console): 新增面向插件的主题令牌覆盖**（CLOSED，L）——插件现在可表达亮/暗主题令牌对，并触及布局角色（surface、border、text），遵循 #8071 的六令牌设计。[链接](https://github.com/agentscope-ai/QwenPaw/pull/8422)
- **[#8559] feat(matrix): 默认安装 E2EE 依赖**（CLOSED，S）——升级 matrix-nio 版本范围并自动安装 E2EE 依赖；加密仍默认关闭。[链接](https://github.com/agentscope-ai/QwenPaw/pull/8559)
- **[#8278] 钉钉 3500 字符静默降级**（Issue CLOSED）——长消息不再静默将 Markdown/表格降级为纯文本（修复已落地）。[链接](https://github.com/agentscope-ai/QwenPaw/issues/8278)
- **[#8515] fnOS 沙箱启动崩溃**（Issue CLOSED）——`dependency_maintenance` 锁根解析不再在受限环境中绕过 `$HOME`。[链接](https://github.com/agentscope-ai/QwenPaw/issues/8515)
- **[#8460] 待审批的可选浏览器/操作系统级通知**（Issue CLOSED，增强）——请求已接受/关闭。[链接](https://github.com/agentscope-ai/QwenPaw/issues/8460)

**净进展：** 记忆插件生态 +1，Matrix E2EE 可运行，插件控制台主题化，桌面更新卫生改善。

---

## 4. 社区热点

按讨论活跃度排序：

1. **[#7997] WebUI 消息撤回/编辑 + 工作区回滚**（9 条评论，OPEN，增强）——用户希望编辑/撤回消息，并自动截断对话及可选的文件快照回滚。底层需求：**对话卫生与确定性状态恢复**——用户将智能体会话视为可编辑工作区，而非仅追加日志。[链接](https://github.com/agentscope-ai/QwenPaw/issues/7997)
2. **[#8278] 钉钉长消息降级**（6 条评论）——超过 3500 字符时静默格式丢失；表格密集型报告是关键用例。底层需求：**通道对等保真**——产生长结构化输出（报告、表格）的智能体需要无损投递。[链接](https://github.com/agentscope-ai/QwenPaw/issues/8278)
3. **[#8565] Windows Hub Local 运行时无法启动**（4 条评论）——AppContainer 技能暂存、缺失 CPU 架构、反向隧道就绪超时。底层需求：**Windows 作为一等运行时**，而非事后考虑。[链接](https://github.com/agentscope-ai/QwenPaw/issues/8565)
4. **[#8538] Matrix 插件在多工作区下失败**（3 条评论）——仅有第一个工作区能在 10 秒共享启动窗口内胜出。底层需求：面向多租户或多项目用户的**多工作区隔离/隔离正确性**。[链接](https://github.com/agentscope-ai/QwenPaw/issues/8538)
5. **[#7613] OpenViking 记忆插件 PR**（XXL，已关闭）——本周期最大的单项贡献，表明社区对**可插拔记忆后端**的兴趣。[链接](https://github.com/agentscope-ai/QwenPaw/pull/7613)

**信号：** 社区精力集中在 (a) 使智能体的对话状态**可编辑且可逆**，以及 (b) **环境可移植性**（Windows、沙箱化 NAS、多工作区 Matrix）。

---

## 5. 缺陷与稳定性

按严重程度排序（均为今日报告/更新）：

| 严重程度 | Issue | 影响 | 修复 PR |
|---|---|---|---|
| **高** | [#8565](https://github.com/agentscope-ai/QwenPaw/issues/8565) Hub Local 运行时在 Windows 上无法启动——AppContainer 暂存、缺失 CPU 架构、反向隧道超时 | 在 Windows x64 上完全阻断 Hub 模式 | 是——[#8577](https://github.com/agentscope-ai/QwenPaw/pull/8577)（argv 令牌缺陷）+ [#8570](https://github.com/agentscope-ai/QwenPaw/pull/8570)（架构回退） |
| **高** | [#8579](https://github.com/agentscope-ai/QwenPaw/issues/8579) 禁用提供商后遗留过时智能体运行配置 → `ProviderError: Provider is disabled`，表现为无信息的“Internal error” | 在所有通道（飞书/钉钉/企业微信/微信）中断，直到重新绑定配置 | 尚无 PR |
| **高** | [#8538](https://github.com/agentscope-ai/QwenPaw/issues/8538) 多个工作区启用 Matrix 插件时失败（10 秒共享启动窗口） | 多工作区 Matrix 账户无法上线 | 相关：[#8558](https://github.com/agentscope-ai/QwenPaw/pull/8558)（旧版迁移/设备锁定） |
| **中** | [#8560](https://github.com/agentscope-ai/QwenPaw/issues/8560) 桌面窗口在 2.3.0-beta.2 中永久 `WS_EX_TOPMOST` | 覆盖全屏应用（游戏、视频、kiosk、幻灯片）——明显的 UX 回归 | 尚无 PR |
| **中** | [#8580](https://github.com/agentscope-ai/QwenPaw/issues/8580) 记忆标签索引从不重建；`/memory/tags` 返回 503 | 标签已生成但不可读——功能实际上已损坏 | 尚无 PR |
| **低** | [#8576](https://github.com/agentscope-ai/QwenPaw/issues/8576) 控制台中默认智能体编辑按钮被禁用，尽管 PUT API 允许 | UI/API 不一致；阻断编辑 `default` 智能体 | 尚无 PR |
| **低** | [#8564](https://github.com/agentscope-ai/QwenPaw/pull/8564) 序列化响应策略缓存引发 `'str' object has no attribute` | 间歇性通道运行时错误 | PR 开放中 |

**评估：** 四个高/中回归中有三个是 **2.3.0b2 特有**，符合 beta 暴露边缘用例的模式。Windows Hub 集群已有部分修复在途；提供商禁用和 TOPMOST 回归缺少修复 PR，应优先处理。

---

## 6. 功能请求与路线图信号

活跃的功能请求和新落地能力指向下一版本内容：

- **对话编辑/回滚**（[#7997](https://github.com/agentscope-ai/QwenPaw/issues/7997)）——参与度最高的增强；鉴于 schema/截断/快照复杂度，可能是 2.4+ 候选。
- **审批通知**（[#8460](https://github.com/agentscope-ai/QwenPaw/issues/8460)，已关闭）——待审批的浏览器/操作系统推送；可能很快落地到控制台。
- **可插拔记忆**——OpenViking 插件已合并（[#7613](https://github.com/agentscope-ai/QwenPaw/pull/7613)），加上 **Piccolo 运行时安装/选择**（[#8573](https://github.com/agentscope-ai/QwenPaw/pull/8573)）和 **过时每日笔记 Inbox 提醒**（[#8578](https://github.com/agentscope-ai/QwenPaw/pull/8578)），暗示 2.3.x/2.4 的**记忆生态主题**。
- **Cron/智能体会话卫生**（[#8557](https://github.com/agentscope-ai/QwenPaw/pull/8557) 每次运行的 cron 会话）——限制长运行作业历史增长；可扩展性信号。
- **Shell 内存限制**（[#8574](https://github.com/agentscope-ai/QwenPaw/pull/8574)）和**每请求内联媒体边界**（[#8034](https://github.com/agentscope-ai/QwenPaw/pull/8034)）——平台加固功能，可能进入稳定性版本。
- **Matrix 默认 E2EE**（已落地，[#8559](https://github.com/agentscope-ai/QwenPaw/pull/8559)）——E2EE 启用可能是下一步。

**下一版本预测（2.3.0 正式版 / 2.3.1）：** Windows Hub Local 修复、提供商配置校验（[#8550](https://github.com/agentscope-ai/QwenPaw/pull/8550)）、Matrix 多工作区修复，以及 cron 会话边界。对话编辑和 OpenViking 记忆可能瞄准更晚的次要版本。

---

## 7. 用户反馈摘要

**痛点（不满）：**
- **静默失败**是反复出现的主题：钉钉降级 Markdown 而不向智能体发信号（#8278）；提供商禁用表现为通用“Internal error”且无回退/重试（#8579）；记忆标签生成但不可读并返回 503（#8580）。用户想要**可操作的错误，而非静默降级**。
- **环境可移植性**：Windows Hub、fnOS 沙箱、受限 shell——用户在受限的真实环境中部署，遭遇导入时崩溃和启动失败。
- **beta 中的 UX 回归**：始终置顶的桌面窗口（#8560）是明显的烦扰，并附有复现步骤。
- **UI/API 不一致**：控制台阻止 API 允许的操作（#8576）。

**满意度 / 积极信号：**
- 高质量、详细的缺陷报告（多个包含环境、复现步骤和根因分析）表明**参与度高、技术成熟的用户群**。
- 稳定的首次贡献者参与（#7613、#8550、#8561、#8535）表明**友好的贡献者体验**。
- 功能请求建设性且范围明确（#7997、#8460），维护者及时关闭。

---

## 8. 积压关注

需要维护者关注的条目：

- **[#7997] WebUI 消息撤回/编辑 + 工作区回滚**——自 2026-09-27 开放，9 条评论，无关联 PR。参与度最高的增强；若无设计决策有停滞风险。[链接](https://github.com/agentscope-ai/QwenPaw/issues/7997)
- **[#8234] read/edit/grep 中的 GBK/CP936 解码**——PR 自 2026-10-14 开放（审核中，XXL），近期无合并信号；显著影响中文 Windows 用户的文件工具。[链接](https://github.com/agentscope-ai/QwenPaw/pull/8234)
- **[#8236] 从 `num_ctx` 获取 Ollama 上下文窗口**——PR 自 2026-10-14 开放（审核中，XL）；本地模型用户在错误窗口静默截断。[链接](https://github.com/agentscope-ai/QwenPaw/pull/8236)
- **[#8034] 限制每请求内联媒体**——XXL PR 自 2026-09-29 开放；数兆字节的请求体可能破坏网关；潜在的生产稳定性问题。[链接](https://github.com/agentscope-ai/QwenPaw/pull/8034)
- **[#8579] 过时提供商运行配置**——新提交，高严重度，**无修复 PR**；应尽快分诊，因为它在常规配置更改后中断所有通道消息。[链接](https://github.com/agentscope-ai/QwenPaw/issues/8579)

---

*摘要基于提供的 GitHub 活动快照生成。链接指向 github.com/agentscope-ai/QwenPaw 上引用的 Issue/PR。*

</details>

<details>
<summary><strong>ZeroClaw</strong> — <a href="https://github.com/zeroclaw-labs/zeroclaw">zeroclaw-labs/zeroclaw</a></summary>

# ZeroClaw 项目摘要 — 2026-11-05

## 1. 今日概览

ZeroClaw 在 v0.9.0 之前处于**发布门禁加固阶段**，过去 24 小时内关闭了 7 个 issue，合并/关闭了 4 个 PR——几乎全部与安全或 CI 相关。活动量很高（50 条 PR 更新、16 条 issue 更新），但严重偏向一个安全集群：委托工具的 principal/身份作用域、SOP 执行以及网关 bearer 验证。两个 P1 和若干 P0/S0 项（数据丢失/安全风险）今日推进至关闭，这是强烈的健康信号。然而，开放的 PR 队列很大（46 个开放），且项目未发布任何版本。总体而言：**健康且有纪律，但正处于高风险安全冲刺中，审查积压不断增长。**

## 2. 发布

过去 24 小时内无新发布。今日移动的 `release:v0.9.0` 标记项数量（见 #11198、#11579、#11961、#11230、#11220）表明 v0.9.0 正在积极组装中。

## 3. 项目进展

今日合并/关闭（7 个 issue、4 个 PR），集中在安全和 CI 卫生方面：

- **[#11198](https://github.com/zeroclaw-labs/zeroclaw/issues/11198)** [已关闭，P0] — 委托记忆工具丢失 principal 作用域（数据丢失/安全风险）。已修复。
- **[#11579](https://github.com/zeroclaw-labs/zeroclaw/issues/11579)** [已关闭，P1] — `save_dirty` 在 V1/V2 配置上错误地标记 `schema_version = 3`，导致迁移跳过后 agent 消失。已修复。
- **[#11951](https://github.com/zeroclaw-labs/zeroclaw/issues/11951)** [已关闭] — 网关 WebSocket 升级在内置 TLS 监听器上失败。已修复。
- **[#11961](https://github.com/zeroclaw-labs/zeroclaw/issues/11961)** [已关闭] — 仪表盘 OIDC 登录功能落地。
- **[#12218](https://github.com/zeroclaw-labs/zeroclaw/issues/12218)** [已关闭] 和 **[#12210](https://github.com/zeroclaw-labs/zeroclaw/issues/12210)** [已关闭，P1] — CI 回归（Windows Clippy 全历史 fetch 超时；Docker 源镜像构建工作区加载失败）。均已修复。
- **[#10526](https://github.com/zeroclaw-labs/zeroclaw/issues/10526)** [已关闭，RFC 已接受] — 仅追加会话事件历史与确定性重放 RFC，现由开放 PR **[#11921](https://github.com/zeroclaw-labs/zeroclaw/pull/11921)** 支撑。
- 合并/关闭的 PR：**[#11230](https://github.com/zeroclaw-labs/zeroclaw/pull/11230)**（对非管理员 principal 扣留会话数据工具）和 **[#11220](https://github.com/zeroclaw-labs/zeroclaw/pull/11220)**（SOP 执行需要管理员权限）——重要的安全遏制工作。

## 4. 社区热点

- **[#10526](https://github.com/zeroclaw-labs/zeroclaw/issues/10526)**（15 条评论，已关闭 RFC）— 仅追加会话事件历史与确定性状态重放。今日最大讨论。底层需求：agent 运行的**可审计性和可复现性**——超越可变对话日志，转向跨运行时、工具回执、成本记录和委托的可信事件账本。
- **[#6437](https://github.com/zeroclaw-labs/zeroclaw/issues/6437)**（5 条评论，搁置中）— Zulip 频道请求，定位为刚合并的 Rocket.Chat 频道的姊妹项。底层需求：**自托管聊天对等性**；今日出现了原生实现 PR **[#12230](https://github.com/zeroclaw-labs/zeroclaw/pull/12230)**。
- **[#11198](https://github.com/zeroclaw-labs/zeroclaw/issues/11198)**（5 条评论，P0）— 委托记忆工具中的 principal 作用域泄漏。底层需求：自有会话/子 agent 的**多租户隐私保证**。
- **[#12109](https://github.com/zeroclaw-labs/zeroclaw/issues/12109)**（2 条评论）— 多插件注册表（`ZEROCLAW_PLUGIN_REGISTRY_URL` 数组）。底层需求：**公共 + 私有注册表共存**；由开放 PR **[#12216](https://github.com/zeroclaw-labs/zeroclaw/pull/12216)** 处理。

另需注意：PR **[#11921](https://github.com/zeroclaw-labs/zeroclaw/pull/11921)** 是一个非常大的（size:XL）跨领域变更，几乎触及每个子系统标签——鉴于其影响范围，很可能是未来的热点话题。

## 5. Bug 与稳定性

按严重程度排序，今日新增/更新的 bug：

| 严重程度 | Issue | 描述 | 修复状态 |
|---|---|---|---|
| **S0 / P0** | [#11198](https://github.com/zeroclaw-labs/zeroclaw/issues/11198) | 委托记忆工具丢失 principal 作用域（数据丢失/安全） | ✅ 已关闭 |
| **S1 / P1** | [#12210](https://github.com/zeroclaw-labs/zeroclaw/issues/12210) | Docker 源镜像构建无法加载工作区 | ✅ 已关闭 |
| **S2 / P1** | [#9572](https://github.com/zeroclaw-labs/zeroclaw/issues/9572) | 调试网关 WebSocket 轮次溢出默认 Tokio worker 栈 | ⚠️ 开放，无 PR |
| **S2 / P1** | [#11579](https://github.com/zeroclaw-labs/zeroclaw/issues/11579) | `save_dirty` 在未迁移配置上标记 schema_version → agent 消失 | ✅ 已关闭 |
| **S2** | [#12119](https://github.com/zeroclaw-labs/zeroclaw/issues/12119) | Windows：shell/skill/coding-CLI 超时只杀死解释器，后代进程存活 | ⚠️ 开放 |
| **S2** | [#12228](https://github.com/zeroclaw-labs/zeroclaw/issues/12228) | `cron update --agent` 报告成功但从未重新分配 agent（今日新增） | ⚠️ 开放 |
| **S2** | [#12227](https://github.com/zeroclaw-labs/zeroclaw/issues/12227) | Shell 审批依赖模型编写的 "approved" 值（今日新增） | ⚠️ 开放 |
| **S2** | [#12225](https://github.com/zeroclaw-labs/zeroclaw/issues/12225) | `glob_search` 从不列出工作区外允许根下的文件（今日新增） | ⚠️ 开放 |

**显著模式：** 今日到达三个新 bug（#12225、#12227、#12228），全部开放且**零评论**——需要分诊关注。#12227（审批提示由模型控制的输入驱动）是一个**安全相关的信任边界问题**，尽管标记为 S2，仍应优先处理。#12119（Windows 超时时的进程树泄漏）是 Windows 平台上持续存在的沙箱弱点，无修复 PR。

## 6. 功能请求与路线图信号

v0.9.x/v1.0 候选，按势头排序：

1. **多插件注册表** — [#12109](https://github.com/zeroclaw-labs/zeroclaw/issues/12109) → PR **[#12216](https://github.com/zeroclaw-labs/zeroclaw/pull/12216)** 已开放。下个小版本的强烈信号。
2. **Zulip 频道** — [#6437](https://github.com/zeroclaw-labs/zeroclaw/issues/6437) → PR **[#12230](https://github.com/zeroclaw-labs/zeroclaw/pull/12230)** 今日开放。可能与其他频道工作一同发布。
3. **仅追加会话历史 / 确定性重放** — RFC [#10526](https://github.com/zeroclaw-labs/zeroclaw/issues/10526) 已接受，PR **[#11921](https://github.com/zeroclaw-labs/zeroclaw/pull/11921)** 进行中——架构上最重要的路线图项。
4. **长时间运行的 shell 任务，支持异步/同步模式 + 交互式终端** — [#11954](https://github.com/zeroclaw-labs/zeroclaw/issues/11954)；早期阶段，可能 v0.10+。
5. **`config migrate` 中的已退役环境变量警告** — [#12221](https://github.com/zeroclaw-labs/zeroclaw/issues/12221)，schema V4 工作的小型后续。

额外相邻信号：转录默认提供商回退 **[#12198](https://github.com/zeroclaw-labs/zeroclaw/pull/12198)** 和 Telegram 媒体回复上下文修复 **[#12197](https://github.com/zeroclaw-labs/zeroclaw/pull/12197)** 表明频道 UX 的持续打磨。

## 7. 用户反馈摘要

- **安全与信任是主要痛点。** 用户正在发现真实的隔离缺口：委托子 agent 继承所有者记忆（#11198）、SOP 执行无管理员权限（#11220）、"执行的工作不携带调用者 principal" 的工具（#12232），以及模型驱动的 shell 审批（#12227）。这是对**租户隔离保证**的不满，而非功能缺口。
- **配置可靠性很重要。** schema 版本标记 bug（#11579）是那种侵蚀信任的静默数据丢失体验——而且它被快速修复，这是积极信号。
- **Windows 上的平台摩擦**持续存在（#12119、#12218），表明 Windows 用户群的沙箱和 CI 体验落后于 Unix。
- **扩展性需求明确且具体**：用户想要私有 + 公共插件注册表（#12109）和更多自托管聊天频道（#6437）。
- **CI/构建稳定性**吸引了快速修复（#12210、#12218），表明维护者对工作流阻塞的快速响应。

## 8. 积压关注

- **[#9572](https://github.com/zeroclaw-labs/zeroclaw/issues/9572)**（自 2026-07-31 开放，P1，2 条评论）— 调试网关 WebSocket 轮次上的 Tokio worker 栈溢出。超过三个月且**无修复 PR**；间歇性网关中止是严重的稳定性问题。
- **[#6437](https://github.com/zeroclaw-labs/zeroclaw/issues/6437)**（自 2026-05-06 开放）— Zulip 频道，自 5 月起搁置在积压中；新 PR #12230 是生命迹象，但 issue 本身应被关联和跟踪。
- **[#12119](https://github.com/zeroclaw-labs/zeroclaw/issues/12119)** — Windows 超时时的后代进程泄漏；仅 1 条评论，无修复。安全相关的沙箱缺口。
- **[#11954](https://github.com/zeroclaw-labs/zeroclaw/issues/11954)** — 长时间运行的 shell 任务；仅 1 条评论，需要维护者界定范围。
- **今日新增，未分诊（各 0 条评论）**：[#12225](https://github.com/zeroclaw-labs/zeroclaw/issues/12225)、[#12227](https://github.com/zeroclaw-labs/zeroclaw/issues/12227)、[#12228](https://github.com/zeroclaw-labs/zeroclaw/issues/12228)——按明显的分诊规范，应在 24–48 小时内标记和分配。
- **PR 审查债务**：46 个开放 PR，若干 size:XL 和 risk:high（[#12232](https://github.com/zeroclaw-labs/zeroclaw/pull/12232)、[#12213](https://github.com/zeroclaw-labs/zeroclaw/pull/12213)、[#11266](https://github.com/zeroclaw-labs/zeroclaw/pull/11266)、[#11921](https://github.com/zeroclaw-labs/zeroclaw/pull/11921)）。由于审查门控的安全修复堆叠在依赖链中（例如 #11230 依赖 #11225/#11234），维护者审查带宽是进入 v0.9.0 的主要吞吐瓶颈。

</details>

---
*本日报由 [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar) 自动生成。*