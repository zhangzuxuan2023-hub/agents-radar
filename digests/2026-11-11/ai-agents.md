# OpenClaw 生态日报 2026-11-11

> Issues: 287 | PRs: 500 | 覆盖项目: 5 个 | 生成时间: 2026-11-10 23:14 UTC

- [OpenClaw](https://github.com/openclaw/openclaw)
- [Hermes Agent](https://github.com/nousresearch/hermes-agent)
- [IronClaw](https://github.com/nearai/ironclaw)
- [QwenPaw](https://github.com/agentscope-ai/QwenPaw)
- [ZeroClaw](https://github.com/zeroclaw-labs/zeroclaw)

---

## OpenClaw 项目深度报告

# OpenClaw 项目摘要 — 2026-11-11

## 1. 今日概览

OpenClaw 依然是一个高速运转的项目：过去 24 小时内有 **287 个 issue** 和 **500 个 PR** 有更新，但流水线明显不对称——342 个 PR 仍处于打开状态，仅 158 个已被合并/关闭；活跃 issue 198 个，已关闭 89 个。本次有两个版本落地：一个补丁版本（v2026.11.3）和一个 `extended-stable` 维护性回移版本（v2026.9.37），表明 9 月线仍在双轨维护。活动评估：**非常活跃，但以维护为主**。评论最集中的条目围绕会话状态完整性、记忆/梦境提升以及更新/迁移失败，说明项目的稳定性风险集中在长生命周期会话和升级路径上，而非核心 agent 执行。

## 2. 版本发布

**v2026.11.3**（[发布说明](https://github.com/openclaw/openclaw/releases)）
- **私有任务结果：** 私有子 agent 的完成结果不再进入流式聊天回复（#188490，@fuller-stack-dev）——这对在共享聊天界面旁运行子 agent 的用户是一项隐私卫生修复。
- **可靠的子任务交接：** 父级在取消子任务后可以交接给替代工作，而不会让被取消的子任务悬空。这与长期存在的 #154572 / #194403 子 agent 权限集群相关，值得寻求修复者关注。
- 未声明破坏性变更或迁移说明。

**v2026.9.37**（[发布说明](https://github.com/openclaw/openclaw/releases)）
- 来自**已退役的 2026.9 维护线**的仅网关 `extended-stable` 版本，由维护者明确决定发布。包含 2026 年 9 月代码加上关键安全更新、可靠性/性能修复以及新模型支持。2026.9 线的运维者应将其视为最后一站回移版本，并计划迁移到 2026.10/11 线。

**注意：** 多份报告表明，对于受影响的安装，通往 2026.11.5-beta.1 的修复路径本身也被阻塞（见 #193962、#193157、#144739）。更新可靠性是一个现实的发布就绪问题。

## 3. 项目进展

本周期关闭/合并（来自可见样本）：

- **[#193962](https://github.com/openclaw/openclaw/issues/193962)（已关闭，P0，ux-release-blocker）：** SQLite 头部探测超时修复无法从 2026.10.10 触达——在 25 条评论后关闭；底层更新路径缺陷（#187197 / #187150 谱系）至少已完成分诊。
- **[#154572](https://github.com/openclaw/openclaw/issues/154572)（已关闭，P1）：** `sessions_spawn` 到 `claude-cli` 子任务失败并报 `SessionTranscriptWriterClaimReboundError`——一项有意义的子 agent 可靠性关闭。
- **[#149631](https://github.com/openclaw/openclaw/issues/149631)（已关闭）：** CI 预检 Node 矩阵上限（121 > 120 个作业）——CI 管道已修复。
- **[#134918](https://github.com/openclaw/openclaw/issues/134918)（已关闭）：** 嵌入式 agent 在 `gpt-5.6-sol` 上出现空/不完整回合——一个长期存在的模型特定可靠性问题已关闭。
- **[#175645](https://github.com/openclaw/openclaw/issues/175645)（已关闭，`not-repro-on-main`）：** `NO_REPLY` 子 agent 通告被误分类为 `empty_response`，消耗了回退链。
- **[#156898](https://github.com/openclaw/openclaw/issues/156898)（已关闭，P1）：** 用量成本后台刷新 worker OOM。
- **[#149221](https://github.com/openclaw/openclaw/issues/149221)（已关闭）：** 控制 UI 因“无活跃工具权限快照”竞态导致的双重渲染。
- **[#141115](https://github.com/openclaw/openclaw/issues/141115)（已关闭）：** 主模型 429 终止会话，而非查询回退。

PR 侧可见合并：[#191795](https://github.com/openclaw/openclaw/pull/191795)（网关运维者 Home 对受限访客的隐私保护，安全敏感）、[#196893](https://github.com/openclaw/openclaw/pull/196893)（macOS 重新打开上次选择的 Gateway）、[#196901](https://github.com/openclaw/openclaw/pull/196901)（测试夹具清理）、[#196898](https://github.com/openclaw/openclaw/pull/196898)（Kova 性能评估器 pin）。

**推进中的功能（打开的 PR）：** 带重连的 A2A 流式传输（[#196903](https://github.com/openclaw/openclaw/pull/196903)）、发布前的内联模板包审查（[#196427](https://github.com/openclaw/openclaw/pull/196427)）、可编辑的待处理聊天模板添加（[#196395](https://github.com/openclaw/openclaw/pull/196395)）、Git 共同作者资料配置（[#196867](https://github.com/openclaw/openclaw/pull/196867)），以及大规模 CI 后端迁移到“Boat”（[#193038](https://github.com/openclaw/openclaw/pull/193038)、[#196894](https://github.com/openclaw/openclaw/pull/196894)、[#196904](https://github.com/openclaw/openclaw/pull/196904)）。

## 4. 社区热点

1. **[#150635](https://github.com/openclaw/openclaw/issues/150635) — 27 条评论（打开，P2，“diamond lobster”）**：短期召回在 512 条上限处被驱逐，意味着“梦境深阶段”永远不会提升记忆。今日讨论最多的条目；底层需求是**持久、有界的记忆，并具备有意义的提升语义**——用户希望梦境是可观察且有效的，而不仅仅是一个管道阶段。
2. **[#193962](https://github.com/openclaw/openclaw/issues/193962) — 25 条评论（现已关闭，P0）**：更新路径无法将 SQLite 探测修复送达受影响的安装。反映了用户对**成熟安装上自更新可靠性**的挫败感。
3. **[#123792](https://github.com/openclaw/openclaw/issues/123792) — 18 条评论**：助手回合在 `claude-cli` 后端（UI + Android）上双重渲染，Telegram 不受影响。指向 **CLI 后端转录持久化通过两条路径写入**。
4. **[#154572](https://github.com/openclaw/openclaw/issues/154572) — 16 条评论（已关闭）**：子 agent spawn 到 CLI 支持的子任务快速失败。尽管已关闭，参与度仍高；2026.9.5 上的用户受到严重影响。
5. **[#151962](https://github.com/openclaw/openclaw/issues/151962) — 15 条评论**：“幽灵用户消息”——内部运行时字符串作为用户提示到达模型，但在 `transcript_events` 中缺失。这是最**侵蚀信任**的报告：用户看到的转录与模型看到的不一致。底层需求：**转录/审计保真度**。
6. PR 侧：[#188295](https://github.com/openclaw/openclaw/pull/188295)（静默 bootstrap 文件跳过警告）带有“diamond lobster”评级，已准备好供维护者查看。

**要点：** 社区精力集中在**会话状态可观测性和信任**——记忆提升、转录完整性和更新正确性——而非新的表面功能。

## 5. Bug 与稳定性

按严重程度排序（P0 → P1 → P2）：

**P0 / 发布阻塞：**
- **[#194664](https://github.com/openclaw/openclaw/issues/194664)**：Codex“受保护续接”在会话 Stop 报告无活跃运行时阻塞所有后续消息（2026.11.1）。每条消息在推理前即失败。无关联修复 PR——**正在主动阻塞用户**。
- **[#190563](https://github.com/openclaw/openclaw/issues/190563)**：无法恢复暂停的 Codex 聊天——提供方审查/原生线程不匹配（回归）。需要安全审查；无修复 PR。
- **[#193962](https://github.com/openclaw/openclaw/issues/193962)**：（今日已关闭）更新路径被头部探测超时阻塞。

**P1：**
- **[#194403](https://github.com/openclaw/openclaw/issues/194403)**：辅助完成续接使其自身权限失效，导致对话被阻塞（2026.11.1）。
- **[#157255](https://github.com/openclaw/openclaw/issues/157255)**：通道超时后回合声明未释放——会话卡住 90+ 分钟。
- **[#181459](https://github.com/openclaw/openclaw/issues/181459)**：3.5 GB 的 agent 数据库使启动成为赌博；`commit checkpoint expired`；嵌入存储 3 次。
- **[#91931](https://github.com/openclaw/openclaw/issues/91931)**：预置的 SOUL/IDENTITY/USER 文件导致 bootstrap 自动完成并在首次运行前**删除用户的 BOOTSTRAP.md**——数据丢失，UX 影响明确。有对应的修复 PR 在途：[#196411](https://github.com/openclaw/openclaw/pull/196411)（保留自定义 bootstrap 引导）。
- **[#168101](https://github.com/openclaw/openclaw/issues/168101)**：工具调用后 Retry-After 忽略 `maxRetryDelayMs`，在整个睡眠期间占用会话通道。

**P2，显著集群：**
- **记忆/梦境：** [#150635](https://github.com/openclaw/openclaw/issues/150635)（驱逐 vs. 提升）、[#138561](https://github.com/openclaw/openclaw/issues/138561)（活跃记忆召回在升级后静默停止）、[#151173](https://github.com/openclaw/openclaw/issues/151173)（召回再水化锚定在片段上）。
- **会话完整性 / 消息丢失：** [#144594](https://github.com/openclaw/openclaw/issues/144594)（pendingInputs 卡在 `interrupted`）、[#141172](https://github.com/openclaw/openclaw/issues/141172)（泄漏的 `agent.run.started`）、[#151962](https://github.com/openclaw/openclaw/issues/151962)（幽灵提示）。
- **存储膨胀：** [#143973](https://github.com/openclaw/openclaw/issues/143973) —— `view_image` 在 `transcript_events` 中持久化完整 base64（1.3% 的事件 = 80% 的字节）。无修复 PR；高杠杆的清理目标。
- **安全相关：** [#159230](https://github.com/openclaw/openclaw/issues/159230)（`message_tool_only` 搁浅回复恢复会用私有文本重新提示模型并强制发送英文错误 DM；无关闭开关）。打开的修复 PR：[#196848](https://github.com/openclaw/openclaw/pull/196848)（Discord 被移除 DM 发送者仍通过过期绑定运行命令）、[#196847](https://github.com/openclaw/openclaw/pull/196847)（自托管 web 工具重定向被跟随并访问私有网络）。
- **更新/迁移：** [#193157](https://github.com/openclaw/openclaw/issues/193157)（已退役的 `agents.defaults.heartbeat` 键拒绝 2026.11.5-beta.1）、[#144739](https://github.com/openclaw/openclaw/issues/144739)（2026.9.3→9.4 npm 更新用旧版本针对 schema-17 状态运行）。
- **性能/空闲 CPU：** [#192496](https://github.com/openclaw/openclaw/issues/192496)（20 个 agent 时空闲技能刷新约 100–170% 核心）、[#138918](https://github.com/openclaw/openclaw/issues/138918)（可见 WebChat 导致 macOS WindowServer CPU）。

**已存在的修复 PR：** [#196411](https://github.com/openclaw/openclaw/pull/196411)（bootstrap）、[#196254](https://github.com/openclaw/openclaw/pull/196254)（父级压缩/回退后生成会话记忆）、[#196853](https://github.com/openclaw/openclaw/pull/196853)（暂停目标仍运行回合）、[#196896](https://github.com/openclaw/openclaw/pull/196896)（流式激增会话工作减少）、[#187668](https://github.com/openclaw/openclaw/pull/187668)（ACP 失败身份保存）、[#165805](https://github.com/openclaw/openclaw/pull/165805)（节点目录列表分页）。

## 6. 功能请求与路线图信号

- **[#138366](https://github.com/openclaw/openclaw/issues/138366)** —— 让记忆槽所有者通过 `MemoryPluginCapability` 中的 `dreaming` 提供方驱动 Dreams 页面。若被处理，可能合理与 v11.3 子 agent 隐私流合并为即将到来的**记忆/扩展 API** 增量。
- **[#192009](https://github.com/openclaw/openclaw/issues/192009)** —— 显式跨提供方 cron 回退授权。运维者需求高；可能是配置模式新增而非新 UI。
- **[#140213](https://github.com/openclaw/openclaw/issues/140213)** —— 多个策略别名解析到一个配置模型；已有维护者决策包，是近期合并的有力候选。
- **[#151980](https://github.com/openclaw/openclaw/issues/151980)** —— 会话模型 pin 应继承 agent 回退链。直接补充 2026.9 时代的回退修复；实现成本低，运维价值高。
- **[#112372](https://github.com/openclaw/openclaw/issues/112372)** —— `sessions_send` 的可选 `wakeOnReply` 延迟完成（本周期已关闭，可能被取代）。
- **[#138990](https://github.com/openclaw/openclaw/issues/138990)** —— 长时间前台 WebChat 运行的可执行检查点/有界让出。
- **[#155039](https://github.com/openclaw/openclaw/issues/155039)** —— 通过同步偏好手动排序团队侧边栏 agent（UI 打磨）。
- **[#119294](https://github.com/openclaw/openclaw/issues/119294)** —— MCP 2.0（无状态规范）迁移跟踪；已关闭但预示未来破坏性变更轨道。

**预测：** 下一版本可见功能最可能是 **bootstrap 保留（[#196411](https://github.com/openclaw/openclaw/pull/196411)）、生成会话记忆谱系（[#196254](https://github.com/openclaw/openclaw/pull/196254)）、暂停目标支出停止（[#196853](https://github.com/openclaw/openclaw/pull/196853)）、A2A 流式传输（[#196903](https://github.com/openclaw/openclaw/pull/196903)）以及模板发布审查（[#196427](https://github.com/openclaw/openclaw/pull/196427)）**。梦境/提供方可扩展性是更长的弧线。

## 7. 用户反馈总结

**痛点：**
- **升级焦虑主导情绪。** 2026.10.10 上的用户无法触达修复；2026.9.x 上的用户遇到 schema-17 状态不匹配；2026.11.5-beta.1 被已退役配置键拒绝。更新路径是最一致的挫败来源。
- **记忆是用户最希望信任的功能。** 夜间驱逐击败梦境、升级后召回静默停止、3.5 GB 存储表明用户在生产中依赖记忆并感到其不透明。
- **转录与运行时分歧令运维者担忧。** 幽灵提示和双重渲染回合表明审计面与模型实际所见不一致。
- **大型安装的扩展问题：** 空闲时多 agent CPU、多 GB 数据库上长时间启动、转录中 base64 膨胀。

**满意信号：** P0/P1 的快速分诊（例如 #193962 约 4 天内以 25 条评论关闭）、对已退役 9.x 线的明确维护者支持回移政策，以及稳定的、署名良好的安全 PR 流（#196848、#196847）显示出受维护的安全态势。

**不满信号：** 若干 P1/P0 条目在数天至数周等待后仍无修复 PR（#194664、#190563、#157255、#181459）；像 #57278、#92473、#112313 这样标记为 stale 的 issue 已开放数月。

## 8. 待办观察

需要维护者关注的长期或高严重度条目：

- **[#91931](https://github.com/openclaw/openclaw/issues/91931)（P1，diamond lobster，自 2026-06-10）**：用户 `BOOTSTRAP.md` 删除——首次运行数据丢失。**[修复 PR #196411 已存在](https://github.com/openclaw/openclaw/pull/196411)**；审查者应优先处理。
- **[#112313](https://github.com/openclaw/openclaw/issues/112313)（自 2026-07-21，12 条评论）**：死信出站队列条目是永久性的且无法清除。需要在 TTL/CLI 清理上做产品决策。
- **[#112638](https://github.com/openclaw/openclaw/issues/112638)（自 2026-07-22）**：`session.maintenance` 的 `enforce` 模式未约束存储——线程/频道条目绕过所有回收。大型安装相关；需要产品决策。
- **[#92473](https://github.com/openclaw/openclaw/issues/92473)（自 2026-06-12）**：模型回退通知泄漏到用户可见频道（内部信息泄露）。修复形态似乎清晰但未合并。
- **[#57278](https://github.com/openclaw/openclaw/issues/57278)（自 2026-03-29，视野中最旧）**：Telegram 机器人照片回复上下文归因——报告的结论过于强烈；仍在等待维护者解决。
- **[#144594](https://github.com/openclaw/openclaw/issues/144594)（platinum hermit）**：`pendingInputs` 卡在 `interrupted` 且无重放——会话状态/消息丢失，修复形态清晰。
- **[#132767](https://github.com/openclaw/openclaw/issues/132767)**：CLI 宿主省略“assemble-before-prompt”，导致上下文引擎 `systemPromptAddition` 晚一回合到达；源修复曾存在（PR #100984）但被关闭未合并——原 PR 值得重新考虑。
- **[#143973](https://github.com/openclaw/openclaw/issues/143973)**：base64 图像载荷主导转录存储——对大型数据库高杠杆，无修复 PR。
- PR 侧：**[#171315](https://github.com/openclaw/openclaw/pull/171315)**（Telegram 附件准备钩子）已开放约 4 周，存在安全边界合并风险，需要证明；**#186966**（从浏览器面板复制文本）和 **#188295**（bootstrap 跳过警告）已标记为准备好供维护者查看。

---

**整体健康评估：** 速度健康，安全分诊活跃，但项目在**会话状态、记忆持久性和自更新路径**上承载集中债务。最清晰的近期胜利是合并已审查的修复（#196411、#196254、#196853），并在下一个稳定版发布前解决 P0 Codex 续接阻塞（#194664、#190563）。

---

## 横向生态对比

# 跨项目对比报告 — 个人 AI 助手 / Agent 开源生态
**日期：2026-11-11 | 项目：OpenClaw、Hermes Agent、IronClaw、QwenPaw、ZeroClaw**

---

## 1. 生态概览

个人 AI 助手 / Agent 开源领域正处于**整合与加固阶段**：四个活跃项目均已发布重大架构变更（网关统一、monorepo 迁移、插件运行时重构、OIDC 鉴权），目前正在消化由此带来的会话状态、记忆持久性和 provider 兼容性方面的缺陷。迭代速度依然极高——仅 OpenClaw 一个项目在 24 小时内就触及了 287 个 issue 和 500 个 PR——但活动的*性质*已从新增表面功能转向**可靠性、可观测性和升级路径正确性**。一个跨项目的一致痛点模式正在浮现，围绕**静默失败**（消息丢失、路径截断、用户数据被删除、配置误路由）和**自更新/迁移完整性**，这表明生态的下一个竞争轴心是信任与可运维性，而非原始能力。与此同时，**多渠道触达**（QQ、飞书、Telegram、XMPP、SimpleX）和**本地推理**（llama.cpp、MLX）是两条最清晰的扩展方向。

---

## 2. 活跃度对比

| 项目 | Issue（24h） | PR（24h） | 合并/关闭率（PR） | 发布状态 | 健康分* | 备注 |
|---|---|---|---|---|---|---|
| **OpenClaw** | 287 更新（198 开启 / 89 关闭） | 500 更新（342 开启 / 158 合并+关闭） | ~32% | v2026.11.3（补丁）+ v2026.9.37（extended-stable 回移） | **7.0 / 10** | 绝对速度最高；维护负担重；P0 Codex 阻塞未解决 |
| **Hermes Agent** | 50（23 开启 / 27 关闭） | 50（43 开启 / 7 合并+关闭） | ~14% | v0.23.1（约 618 个 PR 汇总，11 月 9 日） | **6.5 / 10** | issue 关闭率健康；PR 队列增长；网关 P1 无修复 PR |
| **IronClaw** | 0 | 0 | — | — | **N/A（休眠）** | 24 小时内无活动 |
| **QwenPaw** | 14（9 开启 / 5 关闭） | 39（21 开启 / 18 合并+关闭） | ~46% | v2.3.0-beta.4（11 月 10 日）；GA 升级 PR 已开启 | **7.5 / 10** | 合并率最佳；GA 前存在 beta 回归风险 |
| **ZeroClaw** | 19（12 关闭） | 50（9 合并+关闭 / 41 开启） | ~18% | v0.9.0 即将发布（升级 PR #12377） | **7.5 / 10** | 当日缺陷修复出色；CI 不稳定 + 老化 XL PR |

\* *健康分是分析师综合评分，基于：合并/关闭率、P0/P1 修复 PR 覆盖率、发布节奏、安全响应能力和积压时长。非维护者指标。*

**解读表格：** OpenClaw 在绝对数量上占主导，但背负着最重的开放 PR 债务（342 个开启）和最多的未解决 P0。QwenPaw 和 ZeroClaw 尽管绝对数字较小，但*比率*最健康（快速关闭、当日修复）。Hermes 在样本中 PR 合并率最弱，静默数据丢失缺口最严重。

---

## 3. OpenClaw 的定位

**相对同行的优势**
- **生态引力 / 参考地位：** 摘要将 OpenClaw 定位为"核心参考"，其功能面（A2A 流式传输、模板包发布、Git 共同作者配置、CI "Boat" 迁移）比同行领先一代。
- **双轨维护成熟度：** 针对已退役的 2026.9 系列提供显式的 `extended-stable` 回移（v2026.9.37），这是任何同行都未展现的发布工程纪律水平。ZeroClaw 和 QwenPaw 仍处于 GA 前的单轨阶段。
- **安全态势：** 可见且署名规范的安全 PR（#191795 网关操作员隐私、#196848 Discord 陈旧绑定、#196847 web 工具 SSRF）表明存在活跃的威胁审查流程。
- **绝对速度：** 500 PR / 24h 以数量级优势碾压所有同行。

**技术路线差异**
- OpenClaw **以网关 + 会话状态为中心**，具有显式的 `sessions_spawn` / 子代理权限模型和"dreaming"/记忆提升流水线。同行正在向类似的网关概念收敛（Hermes #106742 统一网关），但 OpenClaw 走得更远——相应地也更深地陷入了相关债务。
- 它运行**插件/记忆能力 API**（`MemoryPluginCapability`、`dreaming` provider）——这是 QwenPaw（#8426 extract-plugin）和 ZeroClaw 等同行刚刚开始的形式化。

**社区规模对比**
- OpenClaw 的 24 小时 issue+PR 量（787 项）约为 **Hermes（100）的 6.5 倍**、**QwenPaw（53）的 14 倍**、**ZeroClaw（69）的 11 倍**。其评论线程（27、25、18、16、15）是同类中最深的，表明存在庞大且活跃的运维者基础，而非小型维护者驱动的项目。

**注意：** OpenClaw 的优势是*压力下的广度*。其 P0 Codex 续接阻塞（#194664、#190563）**没有修复 PR**——这是规模更小、关闭更快的项目（ZeroClaw、QwenPaw）很可能已经修补的漏洞。OpenClaw 在能力和生态上胜出；在发布就绪度上处于中游。

---

## 4. 共性技术焦点领域（跨项目需求）

| 需求 | OpenClaw | Hermes | QwenPaw | ZeroClaw | 新兴需求 |
|---|---|---|---|---|---|
| **会话状态完整性 / 恢复** | #194664、#190563、#157255、#144594 | #148778（搁浅的 `unknown` 轮次）、#137652 | #8652（陈旧模型分发） | #12376（分离轮次丢失答案） | 可恢复、可观测的轮次生命周期；无静默卡死 |
| **消除静默失败** | #151962 幻影提示、#143973 base64 膨胀 | #137652 静默消息删除、#121078 静默插件遮蔽 | #8651 图片丢失、#8406 标志重置、#8655 路径截断 | #12361 缺失 `reasoning_content` | 用户要求对任何丢失、重置或截断给出显式警告/日志 |
| **升级 / 迁移完整性** | #193962、#193157、#144739（自更新阻塞） | #152008（旧更新器的兼容垫片） | beta 系列回归（#8655） | #12153 install.sh musl/glibc | 幂等、可降级、垫片支持的升级 |
| **记忆持久性与可观测性** | #150635（dreaming 与驱逐）、#138561、#181459（3.5 GB 数据库） | （压缩 #137652） | — | — | 有界、真正触发的提升；无静默召回停止 |
| **Provider 兼容性（非 OpenAI）** | gpt-5.6 / Codex 续接 | #148380 Anthropic `json_mode` | #8656 Mistral 422、#7990 阿里云推理元数据 | #12366/#12362 温度硬编码 | 停止硬编码 `temperature`/schema 假设；目录驱动的元数据 |
| **多渠道部署** | （Discord、web） | Telegram 浪潮（10 项修复） | QQ、飞书、微信、钉钉 | XMPP（#11847）、SimpleX（#12316）、Git 渠道 | 渠道启动可靠性是第一大集成投诉 |
| **本地推理作为一等公民** | — | Modal provider（#152951） | llama.cpp/MLX 栈（#8649/#8647/#8648） | — | GPU 感知、自动更新、控制台管理的本地引擎 |
| **CI/测试基础设施债务** | Node 矩阵上限（#149631） | — | — | #12351/#12350 不稳定/挂起测试 | 不稳定测试现在直接阻塞发布 |

**综合：** 四项需求在*每个*活跃项目中反复出现——**（1）轮次/会话可恢复性、（2）响亮优于静默的失败语义、（3）迁移正确性、（4）provider 参数灵活性**。这些不再是差异化因素，而是入场门槛。本地推理和多渠道是项目战略分化的两个领域。

---

## 5. 差异化分析

| 维度 | OpenClaw | Hermes | QwenPaw | ZeroClaw |
|---|---|---|---|---|
| **功能焦点** | 广泛 agent 平台：子代理、A2A、模板、dreaming 记忆 | 网关统一、Kanban 多 worker、插件目录 | 多渠道（QQ/飞书）+ 本地引擎 | 鉴权加固、文档/SEO、渠道广度 |
| **目标用户** | 运行多代理、多界面安装的高级运维者 | 并行 agent 工作流（Kanban）的高级用户 | 中国市场多渠道个人助手、桌面用户 | 多用户/团队部署、安全意识强的运维者 |
| **架构信号** | 网关 + 会话权限 + 插件/记忆能力 API | 单进程统一网关（#106742 之后） | 渠道-worker 插件运行时 + 托管本地引擎 | OIDC/设备证书鉴权、zerorelay 前门、zerocode CLI |
| **发布姿态** | 双轨成熟（补丁 + extended-stable） | 汇总列车（每补丁约 618 PR） | Beta 稳定化 → GA | v0.9.0 前冲刺 |
| **最大弱点** | 更新路径 + 未解决 P0 | 网关 P1 + 静默数据丢失 | Beta 回归 | CI 不稳定 + 老化 XL PR 队列 |
| **独特押注** | 记忆/dreaming 可扩展性作为护城河 | Kanban 并行 agent 工作流 | 本地推理 + 中国渠道触达 | 鉴权/安全 + 文档成熟度 |

**关键洞察：** 没有两个项目在同一主要轴上竞争。OpenClaw 押注**平台深度**；Hermes 押注**并行工作流（Kanban）**；QwenPaw 押注**渠道广度 + 本地模型**；ZeroClaw 押注**多用户鉴权/可运维性**。重叠在于*使能层*（网关、会话状态、升级），而这恰恰是四者都在积累债务的地方。

---

## 6. 社区动能与成熟度

**活跃度分层（按 24h 量）：**
- **第一层 — 高量平台：** OpenClaw（787 项/24h）。快速迭代，但维护主导；成熟度高，发布就绪度中。
- **第二层 — 专注冲刺者：** Hermes（100）、ZeroClaw（69）、QwenPaw（53）。三者都在**快速迭代迈向发布**（Hermes v0.24.0、ZeroClaw v0.9.0、QwenPaw v2.3.0 GA）。
- **第三层 — 休眠：** IronClaw（0/24h）。在活动恢复前应排除出活跃生态对比。

**快速迭代（功能导向）：**
- **QwenPaw** — 本地引擎栈（#8649/#8647/#8648）、插件提取（#8426）、技能分类（#8178）。Beta 节奏，当日修复关闭。
- **ZeroClaw** — OIDC 设备授权、XMPP/SimpleX 渠道、作用域密钥（#12373）。v0.9.0 即将发布。

**稳定化中（还债模式）：**
- **Hermes** — 消化统一网关的余波；Telegram 浪潮*正在关闭*但网关 P1 *仍开启*。真正处于稳定化中期。
- **OpenClaw** — 成熟但背负集中的会话/记忆/更新债务；"维护繁重"的自我评估与数据吻合。

**成熟度排名（发布工程，非功能）：**
1. **OpenClaw** — 双轨回移、显式 EOL 策略。
2. **ZeroClaw** — PR 上有记录的风险/规模标签、当日 S1 修复。
3. **QwenPaw** — 干净的 beta 门控但 beta 线易回归。
4. **Hermes** — issue 关闭率最强，PR 合并率和 P1 修复覆盖率最弱。

**动能判断：** ZeroClaw 和 QwenPaw 在其规模下拥有**最佳的关闭-开启平衡**，最有可能将动能转化为干净的 GA。Hermes 在其 P1 上存在**社区能量与维护者修复速度之间最尖锐的落差**。OpenClaw 动能最强，但在没有专门稳定化冲刺的情况下吸收它的余量最少。

---

## 7. 趋势信号（对 AI Agent 开发者的价值）

从跨项目社区反馈中提炼——对任何在此生态之上或针对此生态构建的人都是可操作的信号：

1. **信任是新功能。** 每个项目的首要投诉都是*静默*失败——用户消息被删除（Hermes #137652）、附件路径被截断（QwenPaw #8655）、幻影提示（OpenClaw #151962）、图片消息丢失（QwenPaw #8651）、`-w` 被忽略（Hermes #67458）。**Agent 开发者的设计原则：** 每一次变更、丢失、重置或截断都必须发出显式的、用户可见的警告和可恢复的产物。静默降级现在是竞争劣势。

2. **自更新是生态最薄弱的环节。** OpenClaw 无法向受影响的安装交付修复（#193962），Hermes 需要为旧更新器提供兼容垫片（#152008），ZeroClaw 的安装器误选 musl/glibc（#12153），QwenPaw 的 beta 线在升级时回归。**从第一天起就构建可降级、垫片支持、幂等的升级器**；将更新路径视为一等、经过测试的产品面。

3. **Provider 参数硬编码是反复出现的集成税。** 三个 separate 项目因硬编码 `temperature`（ZeroClaw #12366/#12362）、缺失 `reasoning_content`（ZeroClaw #12361）、schema `additionalProperties`（Hermes #148380）和缺失 `thinking_param_style`（QwenPaw #7990）而遭遇失败。**从目录元数据而非内联常量驱动 provider 行为**——并将推理强度控制暴露为一等 UX。

4. **记忆必须可观测、有界且提升诚实。** OpenClaw 的"dreaming 从不提升"（#150635）、3.5 GB agent 数据库（#181459）和静默召回停止（#138561）表明记忆在生产中被使用但不透明。**对记忆阶段进行埋点；让驱逐和提升可见；限制并回收存储。**

5. **转录/审计保真度是治理要求。** OpenClaw 的幻影提示和双重渲染报告（#151962、#123792）表明审计面与模型所见出现分歧。对于受监管或需审计的部署，**转录必须是基准真相**——通过单一路径写入。

6. **并行 agent 工作流（Kanban、子代理）撞上并发可靠性墙。** Hermes 的两个 P1 Kanban worker 崩溃（#145022、#139029）和 OpenClaw 的子代理权限集群（#154572 谱系）都源于**并发下的所有者仲裁和事件循环阻塞**。如果你构建多 agent 编排，**预先为所有者仲裁和非阻塞持久化工程留出预算**。

7. **本地推理正在成为一等公民。** QwenPaw 的 llama.cpp/MLX/控制台栈是同类中最大的单一路线图押注。预计**GPU 感知、自动更新、UI 管理的本地引擎**将成为基线期望，而非插件。

8. **多渠道是可靠性面，而非复选框。** Telegram（Hermes）、QQ/飞书（QwenPaw）、XMPP/SimpleX/Git（ZeroClaw）都产生了大多数集成缺陷——启动竞态（QwenPaw #8669）和静默丢失（#8651）占主导。**将每个渠道视为独立测试的适配器，带启动健康探针。**

9. **CI 不稳定现在阻塞发布。** ZeroClaw 挂起的 Telegram 测试和间歇性 Anthropic 回退测试（#12350/#12351）被明确列为发布延迟风险。**测试隔离和不稳定检测基础设施是发布关键。**

---

**给决策者的底线：** 生态的能力前沿正在快速推进，但*运营*前沿——升级、会话恢复、消除静默失败、provider 元数据和 CI 可靠性——是四个活跃项目都在流血的地方。**OpenClaw 在平台深度和生态引力上领先，但在下一个稳定版发布前需要一次稳定化冲刺**（合并 #196411/#196254/#196853，解决 #194664/#190563）。**ZeroClaw 和 QwenPaw 是动能领先者，通往健康发布的路径最干净**，而 **Hermes 承载着最高的静默数据丢失风险**，应在 v0.24.0 之前分诊。**IronClaw 处于休眠，排除在外。**

---

## 同赛道项目详细报告

<details>
<summary><strong>Hermes Agent</strong> — <a href="https://github.com/nousresearch/hermes-agent">nousresearch/hermes-agent</a></summary>

# Hermes Agent — 2026-11-11 项目摘要

## 1. 今日概览

Hermes Agent 展现出**高频、稳定的发布节奏**，v0.23.1 汇总了自 v0.23.0 以来约 618 个已合并 PR，同时 `main` 分支上的发布后稳定化工作仍在继续。活动分布均衡：24 小时内更新了 50 个 issue（23 个开放，27 个关闭）和 50 个 PR（43 个开放，7 个已合并/关闭），表明**关闭率健康，但开放 PR 队列在增长**。今日的主导主题是**统一网关（#106742）合并后的余波**——一组 P1/P2 级别的会话状态、Kanban-worker 和消息投递缺陷均可追溯至单进程网关架构。次要主题是 **Telegram 适配器可靠性**（今日大量修复关闭）和**插件目录扩展**（多个新目录 PR，可能由黑客松驱动）。总体而言，项目**功能上在推进，但网关/会话层存在较高的稳定性风险**。

---

## 2. 发布

**v0.23.1**（发布于 2026 年 11 月 9 日）——*补丁版本*
- 汇总了自 v0.23.0 以来合并的**约 618 个 PR**，形成稳定版本，覆盖：GitHub release（CLI + 安装器）、Microsoft Store 应用、Docker 镜像和 Hermes Cloud。
- **破坏性变更 / 迁移说明：** 此补丁中无相关文档。但一个相关的开放 PR（#152008）警告称，**v0.22.7–v0.23.1 的更新器需要生成的兼容垫片**才能接受 monorepo 模块迁移——如果没有根目录的 `hermes_state_common.py` 垫片，旧更新器会将迁移后的目录树视为降级而拒绝。对于使用旧版更新器的用户，这是一个**需要关注的迁移风险**。
- 完整的精选说明推迟至 v0.24.0。

> **注意：** 这是一个汇总版本；各修复的归属应在 v0.24.0 说明发布后从其读取。

---

## 3. 项目进展

**今日合并/关闭的 PR：7 个**（20 个 PR 样本中可获取的细节有限；所有抽样 PR 均为开放状态）。值得注意的推进信号：

- **Telegram 可靠性浪潮（今日关闭）：** #76022、#150861、#143122、#133164、#33270、#133093、#47093、#47415、#78719、#89615——大批 Telegram 适配器缺陷关闭（附件丢失、关闭时消息丢弃、媒体超时、骰子/群组照片处理）。
- **网关消息投递（已关闭）：** #72131（重复的提供商错误投递）、#126461（超时时重复的流式发送）、#145161（澄清期间 `/steer` 死锁）、#150153（API 会话边界 500）。
- **工具/基础设施（已关闭）：** #124357（`_ensure_mcp_sdk` 竞态）。
- **进行中的功能工作（开放 PR）：** monorepo 模块迁移工具（#151960、#152293、#151880、#151798、#152008）、Modal 推理提供商（#152951），以及大量**插件目录新增**（visual-memory、tripscout、loshify、agentline、lydite、kumi、EarnFi、pixel-hq 版本提升）。

**评估：** 适配器层面的修复进展强劲；monorepo 迁移工具是一项重大的持续性工程投入。

---

## 4. 社区热点

| 排名 | 条目 | 评论数 | 信号 |
|------|------|----------|--------|
| 1 | [#143547 — 统一网关合并后问题](https://github.com/NousResearch/hermes-agent/issues/143547)（P2，开放） | 60 | **参与度最高。** #106742 网关整合余波的中心跟踪 issue。 |
| 2 | [#145022 — Kanban worker 崩溃 "execution unknown; retained by owner"](https://github.com/NousResearch/hermes-agent/issues/145022)（P1，开放） | 20 | 严重：一次网关停顿导致 4 个 worker 被杀；进行中的工作丢失。 |
| 3 | [#67458 — `-w/--worktree` 在 `-z` 一次性模式下被静默忽略](https://github.com/NousResearch/hermes-agent/issues/67458)（P3，需决策） | 9 | 静默的数据安全陷阱：提交落在当前分支上。 |
| 4 | [#20510 — 所有配置的跨设备云同步](https://github.com/NousResearch/hermes-agent/issues/20510)（P2，**👍24**） | 9 | **样本中点赞最多的 issue**——强烈的跨设备需求。 |
| 5 | [#139029 — Kanban worker `WorkerPersistenceError: synchronous_rpc_on_event_loop`](https://github.com/NousResearch/hermes-agent/issues/139029)（P1，开放） | 8 | 第二种 P1 Kanban-worker 失败模式；事件循环阻塞。 |

**底层需求分析：**
- **统一网关**是社区的核心痛点——用户在汇集症状（会话、cron、插件、多平台），而非提交孤立缺陷，这表明是**架构性而非表面性的摩擦**。
- **Kanban 多 worker 可靠性**是一个独立的 P1 集群：两个 P1 issue（#145022、#139029）都涉及并发下的 worker 持久化/所有者仲裁。
- **配置可移植性**（👍24）反映了真实的多设备工作流和未满足的"漫游"期望。

---

## 5. 缺陷与稳定性

按严重程度排序（开放 = 未解决）：

| 严重程度 | Issue | 描述 | 修复状态 |
|----------|-------|-------------|-----------|
| **P1** | [#145022](https://github.com/NousResearch/hermes-agent/issues/145022) | Kanban worker 在网关停顿时崩溃（"retained by owner"）；进行中的工作丢失 | 开放，无关联 PR |
| **P1** | [#139029](https://github.com/NousResearch/hermes-agent/issues/139029) | `WorkerPersistenceError: synchronous_rpc_on_event_loop`——受管 worker 中的事件循环阻塞 | 开放，无关联 PR |
| **P1** | [#137652](https://github.com/NousResearch/hermes-agent/issues/137652) | 压缩**静默删除一条用户消息**，该消息在 todo 快照之后被合并 | 开放，无关联 PR |
| **P2** | [#148778](https://github.com/NousResearch/hermes-agent/issues/148778) | 结算崩溃导致回合滞留为 `unknown`——桌面端无恢复路径 | 开放 |
| **P2** | [#148380](https://github.com/NousResearch/hermes-agent/issues/148380) | Anthropic `json_mode` 总是返回 400（schema 缺少 `additionalProperties`） | 开放 |
| **P2** | [#121078](https://github.com/NousResearch/hermes-agent/issues/121078) | 插件清单名称冲突是**静默的**——`.bak` 目录可能遮蔽真实插件 | 开放 |
| **P2** | [#45106](https://github.com/NousResearch/hermes-agent/issues/45106) | SELinux 破坏 Docker 后端卷绑定 | 开放（长期存在，自 6 月起） |
| **P2** | [#150713](https://github.com/NousResearch/hermes-agent/issues/150713) | pnpm 迁移后 macOS 上 Nix `pnpm-deps` 哈希不匹配 | 开放 |
| **P2（修复 PR 开放）** | [#151399](https://github.com/NousResearch/hermes-agent/pull/151399) | 网关在准入路径上遗漏 `interim_assistant_callback` | **修复 PR 已存在** |
| **P2（修复 PR 开放）** | [#152106](https://github.com/NousResearch/hermes-agent/pull/152106) | Telegram 轮询冲突重连，带边界退避 | **修复 PR 已存在** |

**稳定性评估：** 三个 P1（两个 Kanban，一个压缩数据丢失）**均未解决且无可见的修复 PR**，代表最高的项目健康风险。压缩数据丢失（#137652）尤其令人担忧，因为它是**静默的**。Telegram 缺陷呈*关闭*趋势（良好），但网关/会话 P1 并非如此。

---

## 6. 功能请求与路线图信号

| Issue | 诉求 | 下一版本可能性 |
|-------|-----|----------------------------|
| [#20510](https://github.com/NousResearch/hermes-agent/issues/20510) | 云/跨设备配置同步（👍24） | **中长期**——需求高但属架构性；可能与 Hermes Cloud 对齐 |
| [#114029](https://github.com/NousResearch/hermes-agent/issues/114029) | 将推理强度选择器过滤为模型支持的级别 | **高**——UI 打磨，低风险，涉及多个界面 |
| [#137034](https://github.com/NousResearch/hermes-agent/issues/137034) | 输入框中的 Markdown 预览（👍3） | **中**——范围有限的桌面功能 |
| [#67458](https://github.com/NousResearch/hermes-agent/issues/67458) | 在一次性模式下遵循 `-w`（需决策） | **低-中**——受阻于设计决策 |
| [#55038](https://github.com/NousResearch/hermes-agent/issues/55038) | 修复文档：cron "3 分钟" vs 实际的 600 秒不活动超时 | **高**——仅文档，快速见效 |

**路线图信号：** 开放 PR 集（Modal 提供商 #152951、插件目录扩展、monorepo 垫片 #152008）表明**下一版本的轨迹集中在提供商广度、插件生态增长和模块迁移**，而非网关 P1——这可能与社区优先级不匹配。

---

## 7. 用户反馈总结

**痛点（不满）：**
- **网关整合的干扰**——用户经历广泛、跨领域的故障（会话、cron、Telegram、桌面端），感觉是系统性的而非局部性的。
- **数据丢失焦虑**——静默消息删除（#137652）、worktree 标志被忽略（#67458）、Kanban 进行中工作丢失（#145022）以及静默插件遮蔽（#121078）。多种"静默"失败模式是反复出现的信任主题。
- **恢复缺口**——滞留的 `unknown` 回合（#148778）让用户没有应用内补救手段。
- **长尾平台摩擦**——Telegram 吸收了很大一部分投诉，但*也*是今日大多数修复的对象（积极的响应性信号）。

**观察到的用例：**
- 多设备个人配置可移植性（#20510）。
- Kanban 驱动的并行 agent 工作流（多 worker）——一种严肃的高级用户模式，现已触及 P1 可靠性极限。
- 带有输入/状态指示器的长时间聊天会话。

**满意度信号：** 高缺陷关闭速度（今日关闭 27 个 issue）、点赞驱动的需求（#20510，👍24）以及活跃的黑客松驱动插件生态（众多目录 PR）表明社区参与度高、投入深。

---

## 8. 积压关注

需要维护者关注的重要条目（按积压时长 + 严重程度/参与度）：

| 条目 | 时长 / 状态 | 为何重要 |
|------|-------------|----------------|
| [#45106 — SELinux 破坏 Docker 后端](https://github.com/NousResearch/hermes-agent/issues/45106)（P2） | 自 **2026-06-12** 起开放 | 约 5 个月未处理；阻塞所有启用 SELinux 的发行版（OpenSUSE 等）。 |
| [#67458 — `-w` 在一次性模式下被忽略](https://github.com/NousResearch/hermes-agent/issues/67458)（P3，需决策） | 自 **2026-07-19** 起开放 | 数据安全陷阱，因设计决策而停滞；需要分诊负责人。 |
| [#55038 — cron 文档矛盾](https://github.com/NousResearch/hermes-agent/issues/55038)（文档） | 自 **2026-06-29** 起开放 | 微不足道的文档修复，已 4 个多月；快速赢得信誉。 |
| [#137652 — 压缩删除用户消息](https://github.com/NousResearch/hermes-agent/issues/137652)（**P1**） | 自 2026-10-13 起开放，无修复 PR | 静默数据丢失；积压中优先级最高的单项。 |
| [#139029 — Kanban `synchronous_rpc_on_event_loop`](https://github.com/NousResearch/hermes-agent/issues/139029)（**P1**） | 自 2026-10-15 起开放，无修复 PR | 第二个无关联修复的 P1；与 #145022 配对。 |

**建议：** 在 v0.24.0 说明发布前，分诊两个 **P1 无修复 PR** 条目（#137652、#139029）以及约 5 个月之久的 P2 #45106；文档 issue #55038 是低成本关闭项。

---

*摘要基于 2026-11-10 → 2026-11-11 的 GitHub 活动快照生成。PR/issue 详情反映所提供的前 N 个样本（按评论数计 30 个 issue、20 个 PR）；已关闭 PR 的详情未在源数据中逐一列举。*

</details>

<details>
<summary><strong>IronClaw</strong> — <a href="https://github.com/nearai/ironclaw">nearai/ironclaw</a></summary>

过去24小时无活动。

</details>

<details>
<summary><strong>QwenPaw</strong> — <a href="https://github.com/agentscope-ai/QwenPaw">agentscope-ai/QwenPaw</a></summary>

# QwenPaw 项目摘要 — 2026-11-11

## 1. 今日概览

QwenPaw 正处于 **v2.3.0** 系列的积极 beta 稳定化阶段，今日发布了第四个 beta 版本（v2.3.0-beta.4），并且一个 `chore: bump version to 2.3.0` PR 已开启（#8666），预示着正式版即将到来。活动量很高：24 小时内涉及 **39 个 PR**（18 个已合并/关闭，21 个开启）和 **14 个 issue** 有更新（5 个已关闭，9 个活跃）。主要主题是**打包版 Desktop 构建上的通道/插件运行时可靠性** —— 三个相关 issue（#8644、#8669，以及已关闭的修复链 #8653/#8659）追踪了围绕 `CHANNEL_NOT_CONNECTED` / `WORKER_START_FAILED` 的同一故障模式。2.3.0-beta 系列引入的一组回归问题（附件路径截断、Mission 模型切换失败）正在积极分诊中，表明发布前节奏健康但强度较高。

## 2. 发布

**v2.3.0-beta.4** — 发布于 2026-11-10（由 release-duty issue #8667 追踪）

- **fix(channels): 在更新间保留插件依赖并修复通道启动** — PR [#8653](https://github.com/agentscope-ai/QwenPaw/pull/8653)
  - 在轻量级依赖证据中包含解释器支持的 wheel 标签，使桌面通道 worker 无需 `WORKER_START_FAILED` 即可验证运行时身份（ABI 不匹配仍会被拒绝）。
  - 在插件更新间保留插件依赖，并恢复通道启动。
- **fix(plugins): 恢复被中断的增量依赖安装** — PR [#8659](https://github.com/agentscope-ai/QwenPaw/pull/8659)
  - 为依赖写入成功但验证失败时遗留的修复标记添加恢复机制，该标记此前会阻塞后续安装并可能导致启动失败。

**备注：** 这是 beta 版本；未声明破坏性变更或迁移步骤。不过，使用 2.3.0-beta.1–beta.3 的用户应注意**已知回归 #8655**（附件路径被截断到驱动器根目录，在 beta.3 上仍可复现）—— 建议升级到 beta.4 以获得通道/插件修复，并关注 #8655 的附件修复。版本号提升 PR [#8665](https://github.com/agentscope-ai/QwenPaw/pull/8665)（2.3.0b4，已关闭）和 [#8666](https://github.com/agentscope-ai/QwenPaw/pull/8666)（2.3.0，开启）确认了发布列车。

## 3. 项目进展

今日合并/关闭（精选）：

| PR | 变更 |
|---|---|
| [#8653](https://github.com/agentscope-ai/QwenPaw/pull/8653) | 通道插件依赖保留 + 启动修复（已在 beta.4 发布） |
| [#8659](https://github.com/agentscope-ai/QwenPaw/pull/8659) | 插件依赖安装恢复（已在 beta.4 发布） |
| [#8664](https://github.com/agentscope-ai/QwenPaw/pull/8664) | feat(community): 使用已保存凭据的远程密码登录 —— 支持无需本地回调浏览器认证的远程/Docker 部署 |
| [#8319](https://github.com/agentscope-ai/QwenPaw/pull/8319) | fix(qq): 处理 `GROUP_MESSAGE_CREATE` 网关事件（此前普通群消息被丢弃） |
| [#8665](https://github.com/agentscope-ai/QwenPaw/pull/8665) | 版本号提升至 2.3.0b4 |

已关闭并解决的 issue：**#8651**（QQ 通道丢弃仅图片 C2C 消息）、**#8406**（QQ 通道启用标志被静默重置）、**#8534**（Creator TTS 与阿里云 Token Plan 密钥不兼容）、**#8644**（通道插件在打包版 Desktop 上未激活）。

**进行中的重大工作：** 本地模型引擎的三 PR 堆栈 —— [#8649](https://github.com/agentscope-ai/QwenPaw/pull/8649)（llama.cpp GPU + 自动更新，优先合并）、[#8647](https://github.com/agentscope-ai/QwenPaw/pull/8647)（托管 MLX 引擎）、[#8648](https://github.com/agentscope-ai/QwenPaw/pull/8648)（本地引擎/GPU/自动更新的控制台控件）—— 代表向一流本地推理的重要推进。其他开启项：[#8426](https://github.com/agentscope-ai/QwenPaw/pull/8426)（将第三方 agent 提取为插件，XXL）和 [#8178](https://github.com/agentscope-ai/QwenPaw/pull/8178)（嵌套/分类技能目录）。

## 4. 社区热点

- **[#8651](https://github.com/agentscope-ai/QwenPaw/issues/8651)** — QQ 通道静默丢弃仅图片 C2C 消息（4 条评论，已关闭）。参与度高，因为这是在任何模型调用之前的*静默*数据丢失故障；引用 #8319 QQ 分发工作的修复 PR 现已关闭。
- **[#7990](https://github.com/agentscope-ai/QwenPaw/issues/7990)** — 模型目录缺少阿里云 Token Plan 模型的 `thinking_param_style`，导致 Console 思考控件隐藏（4 条评论，自 9 月 25 日开启）。反映了跨非 OpenAI 提供商对**一流推理控制 UX**的需求。
- **[#8406](https://github.com/agentscope-ai/QwenPaw/issues/8406)** — QQ 通道启用标志被静默重置为 false（3 条评论，已关闭）。另一个静默失败投诉 —— 用户希望在配置变更时获得明确警告。
- **[#8669](https://github.com/agentscope-ai/QwenPaw/issues/8669)** — 飞书通道启动竞态：`_wait_connected` 将初始 `disconnected` 视为致命 → 在打包版 Desktop 构建上每次启动都出现 `CHANNEL_NOT_CONNECTED`（2 条评论，开启，针对 2.3.0b4 报告）。是 #8644 的后续，表明通道启动修复可能未完全覆盖该竞态条件。
- **[#8644](https://github.com/agentscope-ai/QwenPaw/issues/8644)** — 所有通道插件在打包版 Desktop 构建上因 `validate_runtime_identity` AttributeError 而无法激活（2 条评论，已关闭）。

**底层需求：** 社区大量投入**多渠道部署（QQ、飞书、微信、钉钉）**，并报告了通道/插件初始化中的*静默失败*模式。用户希望获得可观测性 —— 当通道失败、丢弃消息或配置被重置时给出明确警告 —— 而非静默降级。

## 5. Bug 与稳定性

按严重程度排序：

1. **🔴 [#8655](https://github.com/agentscope-ai/QwenPaw/issues/8655) — 附件路径被截断到驱动器根目录（"C:\"）并传递给模型（自 2.3.0-beta.1 起的回归，beta.3 上仍存在）。** 文件保存正常但图片加载失败。**尚无修复 PR** —— 最高优先级回归；直接破坏多模态工作流。
2. **🟠 [#8669](https://github.com/agentscope-ai/QwenPaw/issues/8669) — 飞书通道在每次打包版 Desktop 启动时出现 `CHANNEL_NOT_CONNECTED`（确定性）。** 开启；与 #8653/#8659 修复链相关，但似乎是 `_wait_connected` 中的残余启动竞态。
3. **🟠 [#8652](https://github.com/agentscope-ai/QwenPaw/issues/8652) — Mission 任务在切换模型后使用过期模型分发 → 任务失败。** 开启；未见修复 PR。
4. **🟡 [#8661](https://github.com/agentscope-ai/QwenPaw/issues/8661) — 聊天中的 `/workspace/` 文件下载链接打开 SPA HTML 而非下载。** **已有修复 PR：**[#8662](https://github.com/agentscope-ai/QwenPaw/pull/8662)（路径段编码 + 现有认证下载流程，含回归测试和文档）。
5. **🟡 [#8663](https://github.com/agentscope-ai/QwenPaw/issues/8663) — Safari：消息队列操作按钮（编辑/中断/删除）未渲染；Chrome 正常。** 开启；浏览器特定的 Console 回归，尚无修复 PR。
6. **🟢 [#8650](https://github.com/agentscope-ai/QwenPaw/issues/8650) — 思考块与工具卡片之间的视觉不一致；长任务中左边缘抖动。** 开启的增强/打磨项；无修复 PR。

**今日已修复：** #8651（QQ 仅图片丢弃）、#8406（QQ 标志重置）、#8644（Desktop 通道激活）、#8534（Creator TTS / Token Plan）。净效果：通道稳定性在改善，但 2.3.0-beta 回归（#8655、#8652）仍是 2.3.0 GA 的主要阻塞项。

## 6. 功能请求与路线图信号

- **Mistral 提供商插件** — [#8656](https://github.com/agentscope-ai/QwenPaw/issues/8656) 请求纳入 Mistral 插件，解决 422 `extra_forbidden`（reasoning_content）、OpenAI SDK 拒绝 chunk-list 内容，以及 `prompt_cache_key` 被空 `cache_modes` 阻塞的问题。表明对**更广泛的提供商对等性**和插件目录策展的需求。
- **模型目录推理元数据** — [#7990](https://github.com/agentscope-ai/QwenPaw/issues/7990) 要求为阿里云 Token Plan 模型声明 `thinking_param_style`（`reasoning_effort` / `thinking_budget` / `enable_thinking`）。可能以目录 JSON 更新而非代码变更落地。
- **嵌套技能目录** — PR [#8178](https://github.com/agentscope-ai/QwenPaw/pull/8178) 将支持 `<root>/<category>/<skill>/SKILL.md`，与其他 agent 生态保持一致。
- **本地模型管理 UX** — #8649/#8647/#8648 堆栈（llama.cpp GPU、托管 MLX、自动更新、Console 控件）是最大的路线图信号：**本地推理正成为一流的部署目标**。
- **Shell 命令意图标题** — PR [#8638](https://github.com/agentscope-ai/QwenPaw/pull/8638) 为 shell 卡片添加仅显示的 `title`。
- **Office 技能同步** — PR [#8479](https://github.com/agentscope-ai/QwenPaw/pull/8479) 将 DOCX/PPTX/XLSX 技能与 Anthropic 2026 年 7 月更新同步。

**对 2.3.0 / 2.3.x 的预测：** 通道启动加固、附件路径回归修复、Mistral/Token-Plan 提供商元数据，以及（可能在 GA 后）本地引擎堆栈。技能目录分类和 Office 技能同步是 2.4.0 的合理候选。

## 7. 用户反馈摘要

- **痛点：** 静默失败占主导 —— 仅图片 QQ 消息消失（#8651）、通道启用标志无警告重置（#8406）、飞书通道启动失败（#8669）、附件路径被静默篡改（#8655）。用户明确指出*缺乏警告/日志*是加重因素。
- **使用场景：** 多渠道个人助理部署（QQ C2C/群、飞书租户机器人、钉钉 AI 卡片、微信、元宝）、Windows 上的打包版 Desktop 构建、远程 Docker/Hub 部署（推动 #8664 的密码登录），以及带模型切换的长时间运行 Mission 任务。
- **满意度：** 对响应速度正面 —— 多个 bug 报告当日关闭并关联 PR（#8651、#8406、#8644）。**不满**集中在 2.3.0-beta 系列引入的回归（附件截断、过期模型分发）和浏览器特定的 Console 故障（Safari 队列控件），表明 beta 通道和跨浏览器测试存在 QA 缺口。

## 8. 积压关注

- **[#7990](https://github.com/agentscope-ai/QwenPaw/issues/7990)** — 阿里云 Token Plan `thinking_param_style` 缺失（自 **2026-09-25** 开启，47 天，4 条评论）。小型目录修复，UX 影响巨大；需要负责人。
- **[#8178](https://github.com/agentscope-ai/QwenPaw/pull/8178)** — 嵌套技能目录（自 **2026-10-11** 开启，size/XXL，首次贡献者）。长期审查；存在贡献者流失风险。
- **[#8234](https://github.com/agentscope-ai/QwenPaw/pull/8234) / [#8236](https://github.com/agentscope-ai/QwenPaw/pull/8236)** — 文件工具中的 GBK/CP936 解码和 Ollama `num_ctx` 上下文大小设定（自 **2026-10-14** 开启，均为 size/XXL）。分别对中国 Windows 用户和本地 Ollama 用户的重要正确性修复；两者已审查约 4 周。
- **[#8426](https://github.com/agentscope-ai/QwenPaw/pull/8426)** — 将第三方 agent 提取为插件（自 **2026-10-26** 开启，size/XXXL）。架构意义重大；需要维护者对合并顺序给出方向。
- **[#8479](https://github.com/agentscope-ai/QwenPaw/pull/8479)** — Office 技能与 Anthropic 同步（自 **2026-10-28** 开启，size/XXXL，首次贡献者）。大型 diff，审查中 —— 值得优先考虑以提升跨 agent 技能兼容性。
- **[#8655](https://github.com/agentscope-ai/QwenPaw/issues/8655)** — 附件路径截断回归（尚无修复 PR）。**最高优先级的未分配项**；应作为 2.3.0 GA 的门禁。

---

**健康评估：** 速度快、issue 周转快，但 2.3.0-beta 系列显示出值得在 GA 前进行专门稳定化处理的回归率。通道/插件可靠性是明确的投资重点，本地引擎堆栈是标志性路线图举措。

</details>

<details>
<summary><strong>ZeroClaw</strong> — <a href="https://github.com/zeroclaw-labs/zeroclaw">zeroclaw-labs/zeroclaw</a></summary>

# ZeroClaw 项目摘要 — 2026-11-11

## 1. 今日概览

ZeroClaw 正处于冲刺 **v0.9.0** 的紧张预发布阶段，今天开启了版本号升级 PR（#12377），v0.9.0 发布标签也出现在众多 issue 和 PR 中。活动量非常高：24 小时内更新了 19 个 issue 和 50 个 PR，包括 12 个 issue 关闭和 9 个已合并/关闭的 PR——健康的关闭率表明分类响应及时，而非积压累积。今日工作主要围绕三大主题：**zerocode OIDC/设备证书认证加固**、**文档/SEO 基础设施修复**（规范 URL、404 处理、rustdoc 目录树的 noindex），以及一组**温度硬编码 bug**，这些 bug 会导致拒绝 `temperature` 参数的模型无法工作。然而，PR 队列严重偏向大型、长期运行的 XL 变更（部分自 8 月至 10 月起一直开放），两个 CI 测试稳定性问题（间歇性 Anthropic 回退测试、Telegram 测试挂起）表明基础设施债务可能延迟发布。整体健康度：**吞吐量强劲，但发布关键稳定性和审查带宽是需关注的事项。**

## 2. 发布

过去 24 小时内无新发布。然而，**PR #12377**（`chore(release): bump version to v0.9.0`）已开启，将工作区从 0.8.7 升级到 0.9.0，涉及 `Cargo.toml`、26 个路径依赖和 `Cargo.lock`——v0.9.0 发布在即。
- https://github.com/zeroclaw-labs/zeroclaw/pull/12377

## 3. 项目进展

**今日合并/关闭的 PR（9 个）：**
- **#12317** — feat(zerocode)：免配对 OIDC 登录和保存的远程凭据。重大认证 UX 改进：未知客户端现在无需手动复制 bearer token 即可登录远程守护进程。（风险：高，规模：XL）
  - https://github.com/zeroclaw-labs/zeroclaw/pull/12317
- **#12368** — fix(runtime)：停止在心跳决策和模型路由探测中硬编码 temperature（关闭 #12366）。
  - https://github.com/zeroclaw-labs/zeroclaw/pull/12368
- **#12328 / #12329 / #12327** — 文档基础设施：跨发布存活的稳定文档 URL、用于裁剪版本的根 `404.html`，以及 rustdoc API 树 + 错误页面上的 `noindex`。
- **#12364 / #12370** — docs(zerocode)：每次设备授权均使用 PKCE；设备证书生命周期终止和 JWKS `azp` 匹配。
- 其他支持认证和文档工作流的较小文档/配置关闭。

**推进功能的显著开放 PR：**
- **#12316** — SimpleX 消息频道，支持语音笔记和加密通话（可选启用）。
- **#11847** — 原生 Rust XMPP，可选传统 OMEMO（自 10 月 19 日开放，XL）。
- **#12269** — 配对设备语音设置和 Web 聊天中的浏览器听写/朗读。
- **#12376** — Web 修复：分离的聊天轮次在轮次中途丢失答案。

## 4. 社区热点

- **#7432 [Tracker] 运行时、仪表盘登录和网关交付** — 16 条评论，讨论最多的条目。长期运行的 v0.9.0 路线图跟踪器，负责 RFC #5574 中剩余的运行时/网关差距。其活跃度确认 v0.9.0 范围是社区的核心关注点。
  - https://github.com/zeroclaw-labs/zeroclaw/issues/7432
- **#11984 [Feature] 已登录非管理员账户的仪表盘聊天** — 今日在 2 条评论后关闭；“能登录但不能聊天”的差距是 OIDC 部署的明显摩擦点。
  - https://github.com/zeroclaw-labs/zeroclaw/issues/11984
- **PR #11847（XMPP/OMEMO）** 和 **PR #10407（持久会话提示附件，自 8 月 27 日开放）** 仍是互动最多的开放 PR，反映对广泛频道覆盖和持久会话状态的持续需求。

**底层需求：** 用户正推动 ZeroClaw 从“单操作员工具”转向**多用户、多渠道部署**（非管理员仪表盘用户、XMPP/SimpleX/Telegram、配对设备），认证/交付管道是瓶颈。

## 5. Bug 与稳定性

按严重程度排序：

1. **S1 — #12354（已关闭）：zerorelay 不刷新 TLS 写入** — 中继服务的仪表盘资源挂起直到 300 秒空闲超时；前门资源延迟到达。工作流阻塞。修复已落地（同日关闭）。
   - https://github.com/zeroclaw-labs/zeroclaw/issues/12354
2. **S2 — #12366（已关闭）：心跳第一阶段和模型路由探测硬编码 temperature 0.0** — 破坏拒绝 `temperature` 的提供商/模型。修复 PR **#12368** 已合并。
   - https://github.com/zeroclaw-labs/zeroclaw/issues/12366
3. **S2 — #12362（已关闭）：技能审查 fork 硬编码 `temperature: Some(0.3)`** — 在拒绝 temperature 的 Anthropic 风格模型上导致 HTTP 400；标记 v0.9.0。
   - https://github.com/zeroclaw-labs/zeroclaw/issues/12362
4. **S2 — #12357（已关闭）：zerocode 对受限（非管理员）账户在启动时退出** — 配置/日志面板将权限拒绝视为致命错误。
   - https://github.com/zeroclaw-labs/zeroclaw/issues/12357
5. **S2 — #12325（已关闭）：文档规范 URL 包含发布目录名** — 每次稳定发布都会移动 1,025 个规范 URL；由 #12328 修复。
6. **S3 — #12351（开放）：Anthropic 服务器回退测试中的间歇性 CI 测试失败** — 不稳定，尚无修复 PR。
   - https://github.com/zeroclaw-labs/zeroclaw/issues/12351
7. **S3 — #12350（开放）：Telegram 频道测试在 CI 并行运行时测试中挂起** 直到 30 分钟作业超时——尚无修复 PR；CI 风险。
   - https://github.com/zeroclaw-labs/zeroclaw/issues/12350
8. **S3 — #12361（开放）：多轮对话在要求助手历史中包含 `reasoning_content` 的提供商上失败**（Reflection AI / Beam）——`ChatMessage` 缺少该字段；互操作性差距，无修复 PR。
   - https://github.com/zeroclaw-labs/zeroclaw/issues/12361
9. **S3 — #12153（已关闭）：install.sh 在安装了 musl 包的 glibc 主机上选择 musl 构建**（Ubuntu 24.04）。
10. **S3 — #12347（已关闭）：zerocode 配置写入器可能丢失对 `zerocode-config.toml` 的并发更新**（读-改-写竞争）。

**评估：** 温度集群和认证 bug 的当日修复速度强劲；两个开放的 CI 不稳定问题是主要残留稳定性风险。

## 6. 功能请求与路线图信号

今日新开启的请求：
- **#12373 — 只写 WebUI 凭据管理和作用域密钥引用**（仅操作员密钥目录；作者明确要求在实现前讨论范围）。安全意识强，可能在 0.9.0 之后。
  - https://github.com/zeroclaw-labs/zeroclaw/issues/12373
- **#12372 — Git 频道 `draft_mode`**（占位评论代替流式进度）——减少 Git 锻造平台上的评论垃圾；小型，近期次要版本可能实现。
  - https://github.com/zeroclaw-labs/zeroclaw/issues/12372
- **#12353 — ZeroCode 和 Web 聊天中每次对话的运行估算成本**，复用现有成本账本。低风险 UX 功能；合理的 0.9.x 候选。
  - https://github.com/zeroclaw-labs/zeroclaw/issues/12353

**对 v0.9.0 的预测：** 发布将以**认证（OIDC/设备证书/PKCE）、非管理员用户的仪表盘访问和文档基础设施**为主——由 #7432、#11984、#12377、#12362、#12368、#12364、#12370 上的 v0.9.0 标签佐证。成本显示和 Git `draft_mode` 更可能是 0.9.x 后续。

## 7. 用户反馈摘要

- **痛点 — 认证摩擦：** 为远程守护进程复制 bearer token 很痛苦；#12317 的免配对 OIDC 登录直接解决了这个问题，#11984 的“能登录但不能聊天”差距让非管理员 OIDC 用户感到沮丧。
- **痛点 — 提供商兼容性：** 多份报告（#12366、#12362、#12361）显示运行非 OpenAI 风格提供商（Anthropic 变体、Reflection AI/Beam）的用户遇到硬编码参数假设——反复出现的集成质量投诉。
- **用例 — 团队/多用户部署：** 非管理员仪表盘账户、zerocode 中的受限主体和只写密钥管理（#12373）表明采用已超越单操作员使用。
- **满意度信号：** 报告的 bug 当日关闭和修复的快速合并表明用户看到响应式维护；范围明确的 issue 报告数量（主要来自 JordanTheJet 等重复贡献者）表明社区参与度高、技术成熟。
- **不满意度信号：** CI 不稳定（#12351、#12350）和长期 XL PR（#10407、#11847、#11869）可能让等待审查的贡献者感到沮丧。

## 8. 积压关注

长期未答复或停滞的高价值事项，需要维护者关注：
- **PR #10407** — 持久会话提示附件（自 **2026-08-27** 开放，XL，风险：高）。最古老的主要功能 PR；需要审查/决策。
  - https://github.com/zeroclaw-labs/zeroclaw/pull/10407
- **PR #11847** — 原生 Rust XMPP/OMEMO（自 **2026-10-19** 开放，XL，需要维护者审查）。
  - https://github.com/zeroclaw-labs/zeroclaw/pull/11847
- **PR #11869** — 可选任务拥有的 SSH 传输复用（自 **2026-10-19** 开放，XL）。
  - https://github.com/zeroclaw-labs/zeroclaw/pull/11869
- **PR #11911 / #11868** — 频道工具结果保留和重复工具调用预防（自 10 月 19-20 日开放，XL，风险：高）；#11868 关闭 #11866 但等待审查。
- **PR #12269** — 配对设备语音设置（自 11 月 6 日开放，XL）。
- **Issue #7432** — v0.9.0 运行时/网关跟踪器：发布关键协调项；继续监控验收证据。

**底线：** ZeroClaw 展现出高速度和出色的 bug 周转，但 v0.9.0 发布取决于清除开放的 CI 不稳定问题，并将维护者审查带宽投入到大型、老化的 PR 队列。

---
*数据来源：github.com/zeroclaw-labs/zeroclaw — 摘要生成于 2026-11-11。*

</details>

---
*本日报由 [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar) 自动生成。*