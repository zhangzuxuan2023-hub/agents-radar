# OpenClaw 生态日报 2026-11-09

> Issues: 500 | PRs: 500 | 覆盖项目: 5 个 | 生成时间: 2026-11-08 23:13 UTC

- [OpenClaw](https://github.com/openclaw/openclaw)
- [Hermes Agent](https://github.com/nousresearch/hermes-agent)
- [IronClaw](https://github.com/nearai/ironclaw)
- [QwenPaw](https://github.com/agentscope-ai/QwenPaw)
- [ZeroClaw](https://github.com/zeroclaw-labs/zeroclaw)

---

## OpenClaw 项目深度报告

# OpenClaw 项目摘要 — 2026-11-09

## 1. 今日概览

OpenClaw 依然保持高度活跃，过去 24 小时内有 500 个 issue 和 500 个 PR 被触及。但比例偏向于积压清理而非前进势头：333 个 PR 处于打开状态，167 个已合并/关闭；378 个 issue 处于打开/活跃状态，122 个已关闭。今日关闭的项目中有相当大一部分带有 `stale` 标签，表明这是清理而非解决。发布了一个补丁版本（v2026.11.2），仅聚焦于隐私/信任边界加固，而非功能修复。整体健康状况可概括为*稳定但承压*：围绕 SQLite 争用、网关事件循环停滞和内存增长的核心稳定性问题反复出现、长期存在，且仍未关闭。

## 2. 版本发布

**v2026.11.2** — 安全/隐私补丁，未宣布破坏性变更。
- 将配置密钥、对话内容、工具错误和通道负载排除在诊断日志之外。
- 强制执行选定的提供商和导航信任边界。
- 在热 CLI 轮次后释放 MCP 授权。
- 参考：#191318、#174998
- **迁移说明：** 未提及。依赖诊断日志调试涉及密钥流程的运维人员应预期内容会被脱敏。
- **注意事项：** v2026.11.2 并未解决未关闭的 P0 更新器问题（#193962），即 SQLite 头部探测超时的修复无法从 2026.10.10 通过 `openclaw update` 触达。

## 3. 项目进展

今日合并/关闭了 167 个 PR（未提供完整列表；抽样 PR 绝大多数为打开状态）。打开 PR 集中可见的值得关注的在途工作：
- **Control UI 权限检查** — 只读的 People/Roles 视图（#195408），朝多用户/管理员能力迈出的有意义一步。
- **Android 可靠性集群** — 待处理输入显示与基于回执的发送恢复（#191610）、预确认转向所有权（#195402）、文件允许列表消息传递（#191316）以及无障碍修复（#195194）。
- **内存效率** — 去除重复的嵌入缓存副本（#195369）和减少活跃技能目录内存（#195418），两者都直接针对下述内存增长问题。
- **沙箱/网络** — 防止浅沙箱检出中的多 GB 历史下载（#195433）。
- **CLI/UX 打磨** — 若干小修复，改进未知提供商/工作树的错误消息（#195430、#195447）以及配置文件感知的工作区路径（#195448）。

若干 PR 因仓库的“活跃 PR 上限”被关闭并重新提交（#195077、#195178、#195073），表明贡献者吞吐量超过了审查能力。

## 4. 社区热点

| 项目 | 评论数 | 信号 |
|---|---|---|
| [#166634](https://github.com/openclaw/openclaw/issues/166634) — 崩溃后检查点失败导致 agent 永久 `UNAVAILABLE` | 26 | 参与度最高的 issue；agent 变得不可恢复 |
| [#191944](https://github.com/openclaw/openclaw/issues/191944) — macOS `gateway stop` 因进程检查错误而失败 | 25 | Intel macOS 上基础生命周期命令损坏 |
| [#38327](https://github.com/openclaw/openclaw/issues/38327) — google-vertex 出现“Cannot convert undefined or null to object” | 18（👍3） | 自 2026.3.2 起长期存在的 P1 回归 |
| [#53408](https://github.com/openclaw/openclaw/issues/53408) — 长对话后工具参数被静默丢弃 | 17（👍2） | 尽管涉及严重数据完整性问题，仍以 stale 关闭 |
| [#67413](https://github.com/openclaw/openclaw/issues/67413) — 按 agent 的 dreaming 配置 | 16（👍5） | 👍 数最高；并行 dreaming 导致 OOM |

**底层需求：** 用户正遭遇*恢复与资源隔离*问题——崩溃后无法恢复的 agent、误报的重启/停止，以及长会话中逐渐退化的内存/工具行为。#53408（静默参数丢失）以 stale 关闭是一个显著的社区摩擦风险：高影响 bug 在未修复的情况下逐渐老化。

## 5. Bug 与稳定性

按严重程度排序（依标签）：

**P0 / 发布阻断**
- [#193962](https://github.com/openclaw/openclaw/issues/193962) — 更新器探测超时阻止从 2026.10.10 触达修复。上游已有修复（#187197，在 2026.11.5-beta.1 中），但无法通过 `openclaw update` 触达。
- [#162211](https://github.com/openclaw/openclaw/issues/162211) — 启动阻塞事件循环 40–200 秒；健康监控将停滞误读为断开 → 重启循环。无修复 PR。
- [#158592](https://github.com/openclaw/openclaw/issues/158592) — 睡眠/唤醒后运行时发布超时；所有入站消息失败直至重启。无修复 PR。
- [#137177](https://github.com/openclaw/openclaw/issues/137177) — 内置 WeCom 插件无法卸载。

**P1 / 崩溃循环与数据完整性**
- [#181432](https://github.com/openclaw/openclaw/issues/181432) — ARM64 上基线 RSS 约 1.9 GB（从约 300 MB 增长）；SQLite 超时。
- [#185578](https://github.com/openclaw/openclaw/issues/185578) — 堆下限以约 25 MiB/小时棘轮式增长，直至 V8 中止。
- [#182369](https://github.com/openclaw/openclaw/issues/182369) — 堆外 RSS 增长约 0.1 GiB/小时，监控从不触发。
- [#191847](https://github.com/openclaw/openclaw/issues/191847) — SQLite worker 在 512 MB 时被 OOM 杀死；会话永久处于“admission is closed”。
- [#117262](https://github.com/openclaw/openclaw/issues/117262) — 3 个并发 SQLite 写句柄 → 约 33 秒事件循环停滞。
- [#157255](https://github.com/openclaw/openclaw/issues/157255) — 通道超时后转向声明未释放；90 分钟以上会话卡死。
- [#163566](https://github.com/openclaw/openclaw/issues/163566) — `session-resource-loader` 在修复后每轮消耗约 100 秒 CPU。
- [#126906](https://github.com/openclaw/openclaw/issues/126906) — 拒绝 `write` 工具会静默禁用内存持久化*并*报告成功（数据丢失 + 静默失败）。

**已有修复 PR（打开）：** #195446（准备好的运行时陈旧）、#195452/#195451（doctor 非零退出阻塞 Telegram/rescue）、#195369（内存缓存重复）、#195418（技能目录内存）、#195178（撤回的插件请求）。P0 崩溃循环集群没有关联的修复 PR。

**模式：** SQLite 并发、内存增长（堆内和堆外）以及更新后/修复后退化构成了主要的稳定性主题。

## 6. 功能请求与路线图信号

- **按 agent 的 dreaming 配置**（[#67413](https://github.com/openclaw/openclaw/issues/67413)）— 👍 最高（5）；由内存峰值/OOM 驱动。以 stale 关闭，但很可能再次出现。
- **MCP env/headers 中的 SecretRef 支持**（[#76493](https://github.com/openclaw/openclaw/issues/76493)，👍6）— 密钥管理最佳实践被 schema 阻塞。
- **iOS/macOS 上的可选个人身份**（[#162164](https://github.com/openclaw/openclaw/issues/162164)）— 多用户身份路线图信号，已部分合并（#164593）。
- **减少工具 schema token 开销**（[#14785](https://github.com/openclaw/openclaw/issues/14785)，约 3,500 tok/会话）— 成本效率请求。
- **Review 中的 Markdown 预览**（[#128090](https://github.com/openclaw/openclaw/issues/128090)，👍4）；**Android 通知 QoL**（[#143387](https://github.com/openclaw/openclaw/issues/143387)）；** onboarding 内存/嵌入步骤**（[#16670](https://github.com/openclaw/openclaw/issues/16670)）。
- **子 agent 的提供商感知背压**（[#126727](https://github.com/openclaw/openclaw/issues/126727)）；**备份清单遗漏**（[#107433](https://github.com/openclaw/openclaw/issues/107433)）。

**预测：** 鉴于活跃 PR，已在途的 Control UI 权限工作（#195408）和内存效率 PR 集群（#195369、#195418）最有可能接下来落地。如果维护者优先考虑安全/运维人机工程学，SecretRef（#76493）和按 agent dreaming（#67413）是近期小版本的强候选。

## 7. 用户反馈总结

- **恢复下的可靠性：** 用户报告 agent 在崩溃（#166634）、睡眠（#158592）或通道超时（#157255）后永不返回——且没有自愈路径。
- **静默失败：** 工具参数被丢弃（#53408）、内存保存错误地报告成功（#126906），以及通用的“CLI command failed”消息（今日多个 PR）侵蚀信任。
- **资源耗尽：** 长时间运行的网关上的内存增长是反复出现的运维痛点（三个独立的 P1 内存 issue）。
- **平台缺口：** macOS Intel 服务管理（#191944）和 Windows Docker 绑定挂载启动（#193262）破坏基础操作。
- **积极信号：** 持续的贡献者数量（每日触及 500 个 PR）、快速的 Android/web-UI 打磨以及重新提交行为表明，尽管存在审查瓶颈，社区仍保持参与。

## 8. 积压关注

长期未响应的高价值事项，需要维护者关注：

- [#38327](https://github.com/openclaw/openclaw/issues/38327) — P1 回归自 2026 年 3 月起打开（8+ 个月），diamond-lobster 评级，有关联的打开 PR。存在时间最长的高严重度事项。
- [#14785](https://github.com/openclaw/openclaw/issues/14785) — token 开销，自 2026 年 2 月起打开，需要产品决策。
- [#107433](https://github.com/openclaw/openclaw/issues/107433) — 备份清单遗漏，自 7 月起打开，标记为 needs-security-review 和 needs-live-repro。
- [#76493](https://github.com/openclaw/openclaw/issues/76493) — MCP schema 中的 SecretRef，自 5 月起打开，需要安全审查。
- [#137613](https://github.com/openclaw/openclaw/issues/137613) — CLI 后端上压缩前内存刷新被禁用，自 9 月起打开。
- [#161158](https://github.com/openclaw/openclaw/issues/161158) — 网关重启在退出码 0 时误报失败；硬编码 60 秒预算。
- **流程担忧：** `clawsweeper:no-new-fix-pr` 标签出现在绝大多数顶级 issue 上，且若干严重 bug 以 `stale` 关闭（#53408、#67413）。维护者审查队列似乎是项目吞吐量的主要约束。

---
*数据来源：github.com/openclaw/openclaw，快照 2026-11-09。总计反映过去 24 小时内更新的项目；源数据中未包含 PR 评论数。*

---

## 横向生态对比

# 跨项目对比报告 — 个人 AI 助手 / Agent 开源生态
**快照日期：2026-11-09** · 项目：OpenClaw、Hermes Agent、IronClaw、QwenPaw、ZeroClaw

---

## 1. 生态概览

2026 年 11 月的个人 AI 助手/Agent 开源格局呈现出**贡献者基数庞大且活跃，但审查能力存在系统性瓶颈**的特征：五个项目在 24 小时内共涉及约 140 个 PR 和约 70 个 issue，但仅有一个项目（ZeroClaw）合并了有意义的 PR 集合，也仅有一个项目（OpenClaw）发布了版本。各项目正趋同于一套通用架构——网关/守护进程核心、可插拔渠道适配器（Telegram/Discord/Slack/WeCom/QQ）、MCP 工具集成、持久化记忆、沙箱化多 Agent 编排——差异主要体现在**身份/访问模型、记忆实现方式和平台覆盖范围**上。社区反馈的风险主题集中在**静默失败**（配置重置、工具参数丢失、未检测到的降级回退）、**资源增长**（SQLite 争用、堆/堆外 RSS 棘轮式增长）和**多租户隔离**（群组渠道中共享记忆跨用户泄漏）。安全加固——日志中的密钥脱敏、信任边界强制执行、A2A 认证——是所有项目中推进最快的变更类别。

---

## 2. 活跃度对比

| 项目 | Issue（24 小时，开放/总计） | PR（24 小时，开放/已合并-关闭） | 发布（24 小时） | 健康评分* | 备注 |
|---|---|---|---|---|---|
| **OpenClaw** | 378 开放 / 500 涉及；122 已关闭 | 333 开放 / 167 已合并-关闭 | **v2026.11.2**（隐私补丁） | **6.5 / 10** | 原始体量最高；稳定但承压。P0 更新器不可达；大量严重 bug 被 stale 关闭；审查队列是主要制约因素。 |
| **Hermes Agent** | 多数开放，5 已关闭 | 50 开放 / **0 已合并** | 无 | **5.5 / 10** | 参与度高，集成速度为零。7 个月前的安全修复 PR 仍未合并。两个安全 issue 已分类/关闭。 |
| **IronClaw** | 0 更新 | 5 开放（Dependabot）/ 1 已关闭（被取代） | 无 | **4.5 / 10** | 运维层面安静；纯依赖更新。最长开放的 PR 已 78 天（wasm 运行时升级）。 |
| **QwenPaw** | 7 活跃 / 全部开放 | 15 开放 / **0 已合并** | 无 | **5.5 / 10** | 当日 issue→PR 闭环强（txohyeah 集群）但无合并。重复修复 PR 对（#8622/#8624）。 |
| **ZeroClaw** | 14 更新 / ~7 已关闭 | 43 开放 / 7 已合并-关闭 | 无（v0.9.0 受门控） | **7.5 / 10** | 有效速度最高；有组织的里程碑（#7432）、堆叠 PR、提示词 bug 修复。两个新的 S2 认证/数据 bug 未修复。 |

*健康评分是一个综合 1–10 的判断，权重包括：已合并 PR 吞吐量、开放 bug 的严重程度、发布节奏、贡献者响应速度、积压陈旧度。它不是维护者发布的指标。*

**汇总：** 5 个项目中有 4 个在窗口期内**合并吞吐量为零或接近零**——这是全生态最一致的单一信号。

---

## 3. OpenClaw 的定位

**相对同行的优势**
- **规模与成熟度：** 每日涉及 500 个 issue + 500 个 PR，比每个同行高一个数量级（次大：Hermes 50/50；ZeroClaw 14/50）。是窗口期内唯一发布版本的项目。
- **表面广度：** 企业渠道（WeCom）、Android/web/桌面客户端、CLI 更新器、多用户 Control UI——是同侪功能范围的超集。
- **Android + Web UI 打磨速度**无可匹敌（待处理输入恢复、预确认转向、无障碍修复均在今日的开放 PR 中）。

**技术路线差异**
- 以 **Node/TS 运行时 + SQLite 支撑的状态**交付——同行项目为 Rust（IronClaw/ZeroClaw）或 Python（Hermes/QwenPaw）。这与其标志性故障模式直接相关：**SQLite 写句柄争用导致数秒至约 33 秒的事件循环停顿**——这类 bug 在 Rust 项目中基本不存在，在 Python 中处理方式也不同。
- **更新器集成的发布流水线**（`openclaw update`）独一无二，但目前是其阿喀琉斯之踵：P0 #193962 阻碍从 2026.10.10 获取修复。

**社区规模对比**
- OpenClaw 的每日涉及量（约 1,000 项）意味着贡献者基数约为 **Hermes 的 10 倍、QwenPaw/ZeroClaw 的约 20 倍、IronClaw 的 100 倍以上**。然而，这一体量*并未*转化为合并产出（167 关闭 vs 333 开放，且许多关闭标记为 `stale`）——增长已超出治理能力。

**决策者注意事项：** OpenClaw 是**按采用度衡量的参考实现**，但其可靠性态势（P0 崩溃循环无修复 PR、高影响 bug 被 stale 关闭——例如 #53408 静默工具参数丢失）意味着采用者应锁定版本，并在 #193962 清除前避免依赖 `openclaw update`。

---

## 4. 共享技术重点领域

| 需求 | 项目 | 具体需求 |
|---|---|---|
| **多租户 / 每用户记忆隔离** | Hermes（#17254、#28226）、OpenClaw（#162164 身份工作）、ZeroClaw（花名册身份 #11264–#11313） | 按 `user_id` 记忆命名空间化；非管理员的限定范围仪表盘聊天；共享渠道偏好泄漏是 Hermes 的头号潜在需求。 |
| **静默失败消除 / 可观测性** | QwenPaw（#8475、#8103）、OpenClaw（#126906、#53408）、Hermes（#148961 A2A 认证静默降级） | 配置变更时警告日志；显式回退模型信号；写入失败时不上报成功。 |
| **资源增长与并发正确性** | OpenClaw（P1 堆 #185578、堆外 #182369、SQLite #117262）、Hermes（cron 会话泄漏 #145314/#145791）、QwenPaw（SQLite 错误掩盖 #8620） | 有界 RSS；通道超时时释放租约；消除重复查询；真实错误传播。 |
| **MCP 生态加固** | OpenClaw（#76493 MCP schema 中的 SecretRef）、Hermes（#118572 MCP 元数据清理）、ZeroClaw（MCP 图像结果 #10501） | env/headers 中的密钥管理；unicode 标签清理；OpenAI provider 互操作。 |
| **身份与网关信任边界** | OpenClaw（v2026.11.2 信任边界）、ZeroClaw（仪表盘登录、JWKS）、Hermes（A2A 认证 #148961） | 登录生命周期、禁用 Agent 的授权、中继服务仪表盘上的配置写入拒绝。 |
| **多 Agent 编排安全** | ZeroClaw（委派工作区 #9872、对等轮次持久性 #9597）、OpenClaw（每 Agent dreaming OOM #67413）、QwenPaw（/steer #8318） | 工作区沙箱化、可归因的对等消息传递、跳数限制、执行中转向。 |
| **跨平台一致性** | Hermes（Windows ACP 挂起 #135977）、OpenClaw（macOS Intel #191944）、ZeroClaw（CI 矩阵 #7461）、QwenPaw（Windows 桌面 #8627） | Windows/macOS 测试矩阵；launchd/服务生命周期；长路径处理。 |
| **成本/token 效率** | OpenClaw（#14785 每会话约 3.5k tok）、QwenPaw（cron 思考等级 #8626、仅行召回 #8625）、Hermes（KV-cache 窗口 #5306） | 降低 schema token 开销；按作业模型选择；检索优化。 |

---

## 5. 差异化分析

| 维度 | OpenClaw | Hermes Agent | IronClaw | QwenPaw | ZeroClaw |
|---|---|---|---|---|---|
| **核心栈** | Node/TS + SQLite | Python | Rust（wasm 运行时） | Python | Rust |
| **主要焦点** | 全表面个人助手（渠道 + 客户端 + 管理） | 消息平台 Agent，带记忆 OS 野心 | NEAR 生态 Agent 运行时 | 务实聊天运维 + Desktop beta | 安全加固网关与多 Agent 身份 |
| **目标用户** | 高级用户、团队、多渠道运营者 | 社区/群聊部署、Telegram/Discord/飞书 | 区块链/AI 基础设施开发者 | 成本敏感的生产流水线 | 自托管、SSO/OIDC 运营者、多 Agent 团队 |
| **独特押注** | 广度 + 更新器作为平台 | 多租户记忆作为头条功能 | 运行时时效性（wasm、依赖卫生） | 快速贡献者闭环、Desktop UX | 身份/访问控制作为一等产品 |
| **标志性弱点** | SQLite/事件循环争用；高严重度 bug 被 stale 关闭 | 审查冻结（0 合并；7 个月安全 PR） | 功能速度近乎为零；仅机器人活动 | 尽管 PR 供给强但积压未合并 | XL 堆叠 PR 依赖链老化 5–6 周 |
| **发布信号** | v2026.11.2 隐私补丁 | 历史上双周，已暂停 | 无 | 2.2.2.post1 稳定 / 2.3.0-beta.2 | v0.9.0 受门控于 tracker #7432 |

**架构差异化总结：**
- **Rust（IronClaw、ZeroClaw）** 以开发速度换取运行时稳定性——显著缺席于主导 OpenClaw P1 列表的记忆增长和事件循环停顿 bug 类别。ZeroClaw 将其转化为安全优先姿态。
- **Python（Hermes、QwenPaw）** 实现快速贡献者周转（QwenPaw 的当日 issue→PR 对）但表现出 Windows/平台脆弱性和并发泄漏模式（cron 泄漏、worker 线程中间件跳过）。
- **Node/TS（OpenClaw）** 交付最广泛的客户端表面，代价是 SQLite/事件循环争用和最重的审查负担。

---

## 6. 社区动能与成熟度

**活跃度层级：**
- **第 1 层 — 生态锚点：** OpenClaw（每日约 1,000 项涉及、发布版本，但治理承压）。
- **第 2 层 — 活跃但受集成制约：** ZeroClaw（速度与组织比最佳；里程碑驱动）、Hermes Agent（参与度高、零合并——最清晰的*集成停滞*案例）。
- **第 3 层 — 利基/早期：** QwenPaw（贡献者响应快、Desktop beta、窗口内无合并）、IronClaw（本周期仅维护）。

**快速迭代中：** ZeroClaw（v0.9.0 身份/网关栈、堆叠 PR 纪律）、OpenClaw 的 Android/Web UI 表面、QwenPaw 的 bug 修复闭环。

**稳定/整合中：** IronClaw（仅依赖更新）、OpenClaw 核心（反复出现的长期稳定性问题表明架构限制现已进入维护模式而非快速演进）。

**成熟度解读：**
- **流程成熟度：** ZeroClaw > OpenClaw > Hermes ≈ QwenPaw > IronClaw（里程碑跟踪器和带依赖注释的堆叠 PR vs. stale 关闭和活跃 PR 限制搅动）。
- **代码库成熟度（稳定性）：** ZeroClaw ≈ IronClaw（Rust）> QwenPaw > Hermes > OpenClaw（功能最丰富，稳定性债务最多）。
- **贡献者留存风险：** Hermes 最高（7 个月安全 PR）和 QwenPaw（3 个月首位贡献者认证修复 #7066）；ZeroClaw 最低（显式跟进文化）。

---

## 7. 趋势信号 — 对 AI Agent 开发者的价值

1. **静默失败是新的顶级 bug 类别。** 在 QwenPaw（#8475、#8103）、OpenClaw（#126906、#53408）和 Hermes（#148961）中，用户报告的是*无信号的行为变化*——配置重置、模型切换、参数丢失、认证降级。**可操作建议：** 设计 Agent 时提供显式回退元数据、配置变更警告和失败即响的写入路径；将“报告成功但未成功”视为 P0 级缺陷。这是信任架构要求，而非 UX 锦上添花。

2. **审查能力而非贡献者供给是生态天花板。** 五个项目中有四个尽管有 100+ PR 在途仍零/近零合并。**可操作建议：** 对补丁级依赖组采用自动合并（IronClaw 模式）、整合堆叠 PR 依赖（ZeroClaw 痛点）、开展聚焦安全合并冲刺（Hermes 最老的 5 个 PR 中有 4 个是安全修复）。对采用者：**以已合并安全修复延迟而非 issue 体量评判项目。**

3. **多租户记忆是下一个平台战场。** Hermes（#17254 + Memory OS #28226）、OpenClaw 身份工作和 ZeroClaw 花名册身份都指向同一方向。**可操作建议：** 从第一天就以 `user_id`/`tenant_id` 命名空间构建记忆；将隔离改造进共享 `USER.md` 模型是数月的拖累。

4. **SQLite 是 Agent 状态的扩展悬崖。** OpenClaw 的 P1 集群（写句柄争用、worker OOM、约 33 秒停顿）和 QwenPaw 的错误掩盖 bug 都源于并发 Agent 工作负载下的 SQLite。**可操作建议：** 对多 Agent/并发部署，评估 Rust 原生或外部化状态存储，或用有界队列和真实错误传播严格串行化写入。

5. **安全加固是发布最快的变更类别。** OpenClaw 窗口内唯一发布是隐私/信任边界；Hermes 关闭了两个安全 issue 并有脱敏修复积压；ZeroClaw 正在发布 JWKS/授权策略。**可操作建议：** 预期信任边界周围的破坏性配置变更（例如 ZeroClaw #11406 `!` macOS 广泛 root 拒绝）——锁定配置并阅读发布说明。

6. **身份感知、限定范围的 Agent 操作正进入发布流水线**（ZeroClaw v0.9.0 限定范围仪表盘聊天、JWKS 验证、中继服务配置写入拒绝）。**可操作建议：** 若将 Agent 部署到共享仪表盘或群组渠道，现在就规划 OIDC/JWKS 集成和按范围授权；“禁用 ≠ 惰性”必须在网关层强制执行（见 #12289）。

7. **成本/延迟工程正成为生产要求**（按作业思考等级、仅行召回、token-schema 缩减、KV-cache 预算）。**可操作建议：** 暴露模型选择和检索粒度控制；用户运行的是流水线，不是演示。

8. **平台一致性是竞争差异化因素，而非勾选项。** Windows 在 Hermes（ACP 挂起）、QwenPaw（桌面/git 长路径）和 OpenClaw（macOS Intel 生命周期）中都是二等公民；只有 ZeroClaw 积极跟踪 CI 矩阵修复（#7461）。**可操作建议：** 将跨 OS CI 视为桌面/网关产品的发布门控。

---

**决策者底线：** OpenClaw 是按采用度衡量的参考平台，但承载最重的可靠性和治理债务——锁定版本、在 #193962 清除前避免应用内更新器，并独立验证“已关闭”的严重 bug。ZeroClaw 是安全/身份敏感部署中组织最好的项目。Hermes 是最强的多租户记忆押注——*如果*审查能力恢复。QwenPaw 为成本敏感的 Python 部署提供快速的社区响应。IronClaw 运维安静，最好作为稳定的 Rust 运行时组件而非功能平台来评估。

---

## 同赛道项目详细报告

<details>
<summary><strong>Hermes Agent</strong> — <a href="https://github.com/nousresearch/hermes-agent">nousresearch/hermes-agent</a></summary>

# Hermes Agent — 项目摘要
**日期：2026-11-09** · 来源：`github.com/NousResearch/hermes-agent`

---

## 1. 今日概览

Hermes Agent 呈现出**社区活跃度极高但合并吞吐量近乎为零**的状态：过去 24 小时内有 50 个 issue 和 50 个 PR 被触及，但 **0 个 PR 被合并或关闭**，且**未发布任何版本**。Issue 构成偏向 bug（评论最多的条目多数带有 `type/bug` 标签），并伴有跨 cron、gateway、desktop 及平台适配器的长尾 P2/P3 回归问题。值得注意的是，**今日关闭了五个 issue**（包括 `type/security` 类的 #80936 和 #118572），表明即便集成停滞，分诊工作仍在积极进行。50 个开放 PR 积压且**零合并**是最清晰的健康信号：当前瓶颈在于评审带宽，而非贡献者供给。总体而言：**参与度健康，集成速度停滞。**

---

## 2. 版本发布

**过去 24 小时内无新版本发布。**（issue 中提及的近期版本包括 v0.20.0、v0.22.5、v0.22.7 和 v0.23.0，表明在本窗口之前大致为双周发布节奏。）

---

## 3. 项目进展

**今日合并/关闭的 PR：0 个。** 本窗口内没有功能通过 PR 合并进入 `main`。

**今日关闭的 issue（5 个）：**
- [#88579](https://github.com/NousResearch/hermes-agent/issues/88579) — Slack 澄清按钮标签在移动端被截断 *（已修复）*
- [#151237](https://github.com/NousResearch/hermes-agent/issues/151237) — 近期 issue 缺少标签 *（标签流水线已恢复）*
- [#80936](https://github.com/NousResearch/hermes-agent/issues/80936) — `redact_sensitive_text` 遗漏 cookie 形式的凭证 *（安全加固）*
- [#118572](https://github.com/NousResearch/hermes-agent/issues/118572) — MCP 服务器元数据绕过 unicode-tag 净化 *（安全）*
- [#80483](https://github.com/NousResearch/hermes-agent/issues/80483) — Cron 错误解析命名的 Telegram 私聊话题 *（已修复）*

**评估：** 两个安全边界问题得到解决是积极信号，但在没有伴随合并 PR 的情况下关闭（或至少本窗口内未见），意味着修复可能通过直接提交落地，或被判定为已修复/重复而关闭。维护者应予以核实。

---

## 4. 社区热点话题

按评论数排序：

| # | 条目 | 评论数 | 信号 |
|---|------|----------|--------|
| 1 | [#80280](https://github.com/NousResearch/hermes-agent/issues/80280) Kanban 陈旧/被取代的 worker 进程组 | 7 👍1 | **并发正确性** — 陈旧 worker 与替代 worker 同时修改同一 worktree。对 agent 编排构成严重的数据完整性风险。 |
| 2 | [#17254](https://github.com/NousResearch/hermes-agent/issues/17254) 多用户偏好记忆 | 7 | **多租户记忆隔离** — 共享的 `USER.md` 在飞书/Discord/Telegram 群组中跨用户泄露偏好。高需求，带 needs-decision 标签。 |
| 3 | [#145314](https://github.com/NousResearch/hermes-agent/issues/145314) 静默 cron 留下永久空会话 | 7 | **Cron 卫生** — 每次触发泄漏一个会话。 |
| 4 | [#135977](https://github.com/NousResearch/hermes-agent/issues/135977) `hermes acp` 在 Windows 上挂起 | 6 | **平台阻断** — `session/new` 永不返回；Windows 用户被阻塞。 |
| 5 | [#148414](https://github.com/NousResearch/hermes-agent/issues/148414) Webapp 菜单在非 100% UI 缩放下溢出 | 4 | **桌面 UX** — 可见、可复现。 |
| 6 | [#144754](https://github.com/NousResearch/hermes-agent/issues/144754) launchd 更新重启无法卸载 | 4 | **macOS 更新生命周期** — 在受监管主机上失败关闭。 |

**底层需求分析：**
- **排名前三的话题中有两个涉及多用户/多租户隔离**（#17254，以及相关的 Memory OS 提案 #28226）。这是最强烈的潜在需求：Hermes 正被部署在共享频道中，而记忆模型假设的是单用户。
- **Agent 进程生命周期正确性**（#80280）在技术上影响最大——并发进程树修改共享 worktree 是一条静默损坏路径，而不仅仅是恼人的问题。
- **Cron 会话泄漏**在前列出现两次（#145314、#145791），暗示这是系统性的资源泄漏主题，而非孤立 bug。

---

## 5. Bug 与稳定性

按严重程度排序（依据报告者指定的优先级和影响范围）：

**P2 — 高**
- [#151280](https://github.com/NousResearch/hermes-agent/issues/151280) **陈旧流看门狗杀死活跃流。** 忽略 SSE `ping`/注释保活；在 OpenAI 兼容代理后、Claude Opus 长时间思考期间，于 240 秒时终止活跃的 `chat_completions` 流。*无关联修复 PR。*
- [#148961](https://github.com/NousResearch/hermes-agent/issues/148961) **🔒 A2A 认证在 systemd 安装下静默降级为仅 localhost / 无认证**（对 `*TOKEN*` 的环境变量清洗）。一种以微妙方式失败开放的安全相关错误配置。
- [#151246](https://github.com/NousResearch/hermes-agent/issues/151246) **🔒 路由 profile 中间件在 worker 线程上被跳过** → 隐私插件失败开放。Desktop 多路复用下 profile 隔离失效。
- [#151247](https://github.com/NousResearch/hermes-agent/issues/151247) 路由 profile 子环境剥离 `MNEMOSYNE_EMBEDDING_*` → 记忆存储**无向量**。静默的数据质量降级。
- [#151351](https://github.com/NousResearch/hermes-agent/issues/151351) Desktop Windows 票据桥在定时器上杀死 python 辅助进程 → 一个慢票据触发完整启动重跑。
- [#145314](https://github.com/NousResearch/hermes-agent/issues/145314) / [#145791](https://github.com/NousResearch/hermes-agent/issues/145791) Cron 空会话泄漏（两种变体）。
- [#151385](https://github.com/NousResearch/hermes-agent/issues/151385) 移除/暂停 cron 任务会遗留非终态事件——账本无法对账。
- [#144728](https://github.com/NousResearch/hermes-agent/issues/144728) `session.resume` 对活跃未持久化会话省略 `running`/`status` → 输入框锁定。
- [#135977](https://github.com/NousResearch/hermes-agent/issues/135977) Windows ACP 挂起（平台阻断）。
- [#82255](https://github.com/NousResearch/hermes-agent/issues/82255) 环境变量剥离移除 `CONDA_PREFIX` 但保留 `CONDA_SHLVL` → 破坏 conda。

**P3 — 中等**
- [#149026](https://github.com/NousResearch/hermes-agent/issues/149026) Cookie 认证在 TLS 终止路由后写入 403（Origin 被剥离）。
- [#149621](https://github.com/NousResearch/hermes-agent/issues/149621) 空的 `~/.bash_profile` 遮蔽 `~/.profile` → `hermes` 不在 PATH 中。
- [#143001](https://github.com/NousResearch/hermes-agent/issues/143001) Telegram 文档相册被拒绝（`media_text_inlined_length`）。
- [#151053](https://github.com/NousResearch/hermes-agent/issues/151053) 长单行输出的完成通知变为空。
- [#148414](https://github.com/NousResearch/hermes-agent/issues/148414) Webapp 菜单在非 100% UI 缩放下溢出。

**可见的修复 PR：** 前 20 个 PR 主要是较旧的（2026 年 4 月至 8 月）bug/安全修复，仍在等待合并。**今日的 P2 bug 似乎尚无关联修复 PR**——鉴于其中数个为安全边界问题，这一缺口令人担忧。

> **关于垃圾信息的说明：** 今日打开的两个 issue（[#151517](https://github.com/NousResearch/hermes-agent/issues/151517)、[#151518](https://github.com/NousResearch/hermes-agent/issues/151518)）是来自 `kyanite-factory-bot[bot]` 的空「探测」issue。建议限流/举报。

---

## 6. 功能请求与路线图信号

**需求最高：**
1. **多用户偏好记忆**（[#17254](https://github.com/NousResearch/hermes-agent/issues/17254)）——按 `user_id` 隔离记忆，独立存储。与更广泛的 **Memory OS 验收层**提案（[#28226](https://github.com/NousResearch/hermes-agent/issues/28226)）配套，后者涵盖诊断、清单和隔离测试。*预测：鉴于重复需求和 `needs-decision` 标记，多租户记忆隔离是下一个次要版本最有可能的头条功能。*
2. **UI 本地化 / i18n**（[#114195](https://github.com/NousResearch/hermes-agent/issues/114195)）——在 `config.yaml` 中配置 `display.language`，首先请求 pt-BR。低工作量、高覆盖；近期可行。

**PR 侧待合并功能候选：**
- 按话题绑定技能 + Telegram 论坛自动发现（[#5489](https://github.com/NousResearch/hermes-agent/pull/5489)）
- 双消息队列：跟进（Alt+Enter）+ 引导（[#5319](https://github.com/NousResearch/hermes-agent/pull/5319)）
- 用于 KV-cache 前缀优化的滑动窗口上下文预算（[#5306](https://github.com/NousResearch/hermes-agent/pull/5306)）
- 提高默认记忆字符限制（[#5323](https://github.com/NousResearch/hermes-agent/pull/5323)）
- 下一会话任务的一次性交接（[#5310](https://github.com/NousResearch/hermes-agent/pull/5310)）

**生态信号：** 今日有*多个插件目录 PR 落地*——`seedguard`（#151543）、`salts-secrets`（#151411）、`proof-run`（#151252），以及一个三插件 SHA 更新（#149734）。插件生态正在升温，一个显著主题是**密钥防火墙与身份/上下文隔离**——印证了上述多租户需求。

---

## 7. 用户反馈总结

**表达的痛点：**
- **共享 agent 部署打破记忆模型。** 在群聊中运行 Hermes 的用户报告跨用户偏好串扰——这对团队/社区用例是根本性的适配问题。
- **Windows 目前是二等平台。** ACP 挂起、桌面票据桥不稳定和进程树问题集中于此。
- **Cron 存在泄漏。** 运行定时/监控任务的实践者会累积空会话和孤立事件——运维卫生债务。
- **注重安全的用户正在发现真实缺口**（cookie 脱敏、A2A 认证降级、profile 中间件跳过、MCP 元数据注入）。这些报告细致且带有进程级证据，表明是资深用户而非噪音。
- **更新生命周期摩擦**在 macOS launchd 和 Linux PATH 上——安装/升级体验需要关注。

**满意度信号：** 报告详尽、可复现，且常附有建议补丁（例如 #82255 披露了 AI 辅助编写并附有补丁）。社区成员正在做分诊级别的工作——这是投入的标志，但也反映出评审能力不足。

---

## 8. 积压观察

**最古老的待合并高价值 PR**（均创建于约 2026-04-05/06，约 7 个月后仍开放）：

| PR | 年龄 | 重要性 |
|----|-----|----------------|
| [#5516](https://github.com/NousResearch/hermes-agent/pull/5516) `_detect_tool_failure` 结构化 JSON 处理 | ~7 个月 | 故障检测的正确性——`blast-broad` 风险标签。 |
| [#5459](https://github.com/NousResearch/hermes-agent/pull/5459) 🔒 脱敏签名视觉图像 URL | ~7 个月 | 安全；签名 URL 在日志中泄漏。 |
| [#5418](https://github.com/NousResearch/hermes-agent/pull/5418) 🔒 加固 Solana 技能（SSRF/URL 注入） | ~7 个月 | 安全；环境控制的 RPC_URL。 |
| [#5341](https://github.com/NousResearch/hermes-agent/pull/5341) 🔒 WeCom 凭证日志脱敏 | ~7 个月 | 安全；INFO 日志中的访问令牌。 |
| [#5300](https://github.com/NousResearch/hermes-agent/pull/5300) 🔒 Mattermost 媒体 URL 脱敏 | ~7 个月 | 安全；日志中的 bearer 凭证。 |

**另需关注：**
- [#80280](https://github.com/NousResearch/hermes-agent/issues/80280) Kanban worker 进程组竞态——评论最多的 issue，无修复 PR，高损坏风险。
- [#17254](https://github.com/NousResearch/hermes-agent/issues/17254) 多用户记忆——`needs-decision`；需要维护者引导，以免再等 7 个月。
- [#28226](https://github.com/NousResearch/hermes-agent/issues/28226) Memory OS 验收层——可吸收 #17254 的设计文档。

**建议：** 积压模式——*五个最古老开放 PR 中有四个是安全修复*——是最具可操作性的发现。一次聚焦安全的合并冲刺既能降低风险，又能向社区最高质量的贡献表明响应性，同时疏通已 24 小时无合并的 50 个 PR 队列。

</details>

<details>
<summary><strong>IronClaw</strong> — <a href="https://github.com/nearai/ironclaw">nearai/ironclaw</a></summary>

# IronClaw 项目摘要 — 2026-11-09

## 1. 今日概览

IronClaw 维护活动较低但保持稳定：过去 24 小时内没有 issue 更新，六个 PR 有活动，其中一个已关闭。所有可见活动均为 Dependabot 在 Rust crate 和 GitHub Actions 上的依赖自动化——今日窗口内没有功能、缺陷修复或面向用户的 PR。PR #8160（43 个包的依赖批量升级）关闭后立即被 PR #8169（同一组依赖，44 项更新）取代，表明这是例行机器人更替而非实质性审查。整体项目健康状况看似稳定但运营层面较为安静；关键风险信号是长期存活的、仍未关闭的依赖 PR 积压不断增加（其中一个自八月起就存在），这表明维护者的精力投向了其他地方。

## 2. 发布

本周期内无新发布。

## 3. 项目进展

- **[PR #8160](https://github.com/nearai/ironclaw/pull/8160)** [已关闭] — 依赖组批量升级（43 项更新，“everything-else”组，包括 `thiserror` 2.0.20→2.0.21、`uuid` 1.24.0→1.28.0、`base64`）。自 2026-10-25 开启后于 2026-11-08 关闭；实际上已被 #8169 取代。
- 今日无合并的功能或缺陷修复 PR。无面向用户的功能推进。

## 4. 社区热点

所提供数据中没有 issue 或 PR 显示评论数或反应（所有评论字段均为 `undefined`，所有 👍 = 0）。按时间/数量来看最值得注意的条目：

- **[PR #8169](https://github.com/nearai/ironclaw/pull/8169)** — [开启] 最新的依赖批量升级，44 个包，包括 `uuid` 1.24.0→1.29.0。
- **[PR #8159](https://github.com/nearai/ironclaw/pull/8159)** — [开启] GitHub Actions 组，11 项更新，特别是 `anthropics/claude-code-action` 1.0.183→1.0.259（跨度很大，表明上游 AI 代理 CI 工具正在积极演进）。
- **[PR #7834](https://github.com/nearai/ironclaw/pull/7834)** — [开启] wasm 组（`wasmtime`、`wasmtime-wasi`、`wit-component`、`wit-parser`），标记为 `size: L`、`risk: medium`、`contributor: experienced`。

**深层需求：** 没有可见的社区讨论可供分析；互动数据（评论、反应）缺失，因此无法从今日数据推断需求信号。CI action 升级至 `claude-code-action` 暗示了项目 AI 辅助的开发工作流，但这属于基础设施，而非社区需求。

## 5. 缺陷与稳定性

今日没有报告或更新缺陷、崩溃或回归。今日窗口内不存在修复 PR。

稳定性相关说明：**[PR #7834](https://github.com/nearai/ironclaw/pull/7834)**（wasm 运行时升级）带有 `risk: medium` 标签，自 2026-08-23 起一直开启——是运行时间最长的开放 PR。wasm 运行时是代理平台的核心执行面；那里的升级延迟可能带来潜在的安全/兼容性风险敞口，尽管数据中未引用具体漏洞。

## 6. 功能请求与路线图信号

过去 24 小时内没有提交或更新功能请求。无法从今日数据集推断路线图信号。唯一的前瞻性技术信号是待处理的 wasm 工具链升级（#7834），它将改善运行时的时效性而非增加功能——一旦审查通过，是下一个发布周期的合理候选。

## 7. 用户反馈摘要

今日数据中不存在用户反馈（零 issue 更新）。无法从此窗口评估痛点、用例和满意度指标。issue 流量的完全缺失——既无新报告也无关闭——可能表明用户群体安静、跟踪器其他位置的分诊有效，或用户参与度降低；数据无法区分这些情况。

## 8. 积压关注

需要维护者关注的条目，按存在时长/陈旧程度排序：

1. **[PR #7834](https://github.com/nearai/ironclaw/pull/7834)** — 已开启 **78 天**（自 2026-08-23）。Wasm 运行时组升级，标记为 `size: L` 和 `risk: medium`。停滞最久的条目；由于运行时风险，可能需要仔细审查。
2. **[PR #8140](https://github.com/nearai/ironclaw/pull/8140)** — 已开启 22 天。`serde_json` 1.0.151→1.0.154（序列化组）。低风险的单 crate 升级，应该可以轻松合并。
3. **[PR #8141](https://github.com/nearai/ironclaw/pull/8141)** — 已开启 22 天。Tokio 生态组（5 项更新，包括 `tokio` 1.53.1→1.53.3）。补丁级运行时修复；低风险。
4. **[PR #8159](https://github.com/nearai/ironclaw/pull/8159)** — 已开启 15 天。Actions 组（11 项更新）。`claude-code-action` 的大幅版本跳跃值得检查是否存在破坏性 CI 行为。

**模式：** 五个开放的 Dependabot PR 横跨三个依赖组，表明例行维护存在合并瓶颈。合并审查或为补丁级组（#8140、#8141）启用自动合并将减少积压，并释放维护者的时间用于功能工作。

---

*注：所有活动、评论和反应数据仅来自所提供的快照；每个条目的互动指标均不可用（null/零），因此无法独立佐证社区情绪。*

</details>

<details>
<summary><strong>QwenPaw</strong> — <a href="https://github.com/agentscope-ai/QwenPaw">agentscope-ai/QwenPaw</a></summary>

# QwenPaw 项目摘要 — 2026-11-09

## 1. 今日概览

QwenPaw 呈现出**高活跃度、以维护为主的活动**：过去 24 小时内有 7 个活跃 issue 和 15 个已更新的 open PR，但**零合并、零发布**——这是纯粹的接收日，没有任何完成项。issue 队列主要由社区报告者主导（尤其是 `txohyeah` 在同一天提交了三个相关的内存/搜索 bug 及其对应的修复 PR），这表明贡献者参与度高，但维护者的处理吞吐量承压。出现了两个明显的 bug 集群：**静默的配置/状态失败**（QQ 频道 `enabled` 被重置、静默模型回退）以及**内存/召回搜索正确性**（SQLite 错误被掩盖、重复的轮次边界查询）。值得注意的是，多个同日 issue→PR 配对表明贡献者循环是健康的，但**全部 open、零合并的模式**若持续存在，将带来吞吐量风险。

---

## 2. 发布

**无。** 过去 24 小时内没有新发布。根据 issue 报告，当前流通的参考版本为：`2.2.2.post1`（稳定版 pip）和 `2.3.0-beta.2`（桌面版 beta）。

---

## 3. 项目进展

**今日没有 PR 被合并或关闭**——全部 15 个已更新的 PR 仍为 OPEN。功能工作仅以新提交的形式推进：

- **[#8628](https://github.com/agentscope-ai/QwenPaw/pull/8628)** — 为聊天响应添加模型回退元数据（`model_used`、`model_requested` 等）；直接补充了 open issue #8103。
- **[#8626](https://github.com/agentscope-ai/QwenPaw/pull/8626)** — 为 cron 任务提供按任务级别的 `thinking_level` 覆盖（成本/延迟优化）。
- **[#8625](https://github.com/agentscope-ai/QwenPaw/pull/8625)** — 在 `recall_history` 上暴露 `include_turn`；对 issue #8621 的同日响应。
- **[#8630](https://github.com/agentscope-ai/QwenPaw/pull/8630)** — 修复部分频道更新将 `enabled` 重置为默认值的问题；对 issue #8475 的同日响应。
- **[#8624](https://github.com/agentscope-ai/QwenPaw/pull/8624) / [#8622](https://github.com/agentscope-ai/QwenPaw/pull/8622)** — 针对 SQLite 错误掩盖（issue #8620）的两个独立修复。
- **[#8623](https://github.com/agentscope-ai/QwenPaw/pull/8623)** — 消除重复的轮次边界解析（issue #8619）。

**观察：** `txohyeah` 集群（issues #8619–#8621 + PRs #8623–#8625）是同日分诊与修复的典范，但全部仍未合并。

---

## 4. 社区热点

绝对互动量较低（0 个反应，每项 ≤2 条评论），但讨论最多的主题揭示了核心可靠性关切：

| 项目 | 评论数 | 链接 |
|---|---|---|
| #8475 QQ 频道 `enabled` 被静默重置 | 2 | [Issue #8475](https://github.com/agentscope-ai/QwenPaw/issues/8475) |
| #8103 静默模型回退通知 | 2 | [Issue #8103](https://github.com/agentscope-ai/QwenPaw/issues/8103) |

**潜在需求：** 两个热门主题共享一个主题——**系统静默地改变行为，而用户从未被告知。** 用户不仅要求修复，还要求**可观测性与信任**：配置变更时输出警告日志，以及明确的"由回退模型回答了此请求"信号。同日 PR（#8630、#8628）证实贡献者已认识到这一点。

---

## 5. Bug 与稳定性

按严重程度排序：

1. 🔴 **高 — [#8475](https://github.com/agentscope-ai/QwenPaw/issues/8475)：QQ 频道 `enabled` 在配置重载/重启时被静默重置为 `false`。** QQ 消息收发完全丢失且无警告。*已有修复 PR：[#8630](https://github.com/agentscope-ai/QwenPaw/pull/8630)（部分更新合并）。*
2. 🟠 **中高 — [#8629](https://github.com/agentscope-ai/QwenPaw/issues/8629)：超大历史图片在每次模型调用时被重新发送；剥离重试后仍保留误导性的"Image loaded"文本。** 浪费 token/带宽并误导模型。*尚无修复 PR。*
3. 🟠 **中 — [#8627](https://github.com/agentscope-ai/QwenPaw/issues/8627)：Checkpoint 服务在 Git 长路径前缀下失败 + 微信频道卡在 `DEPENDENCY_ENVIRONMENT_AMBIGUOUS`（Windows 11，2.3.0-beta.2）。** 影响 Windows 上的桌面版 beta。*尚无修复 PR。*
4. 🟡 **低中 — [#8620](https://github.com/agentscope-ai/QwenPaw/issues/8620)：SQLite `OperationalError` 在超时后被掩盖为 `TimeoutError`。** 隐藏了真实的锁/繁忙错误。*两个修复 PR：[#8624](https://github.com/agentscope-ai/QwenPaw/pull/8624)、[#8622](https://github.com/agentscope-ai/QwenPaw/pull/8622)——需协调潜在的重复工作。*
5. 🟡 **低 — [#8619](https://github.com/agentscope-ai/QwenPaw/issues/8619)：每次搜索命中重复执行轮次边界查询。** 性能低效。*修复 PR：[#8623](https://github.com/agentscope-ai/QwenPaw/pull/8623)。*

**模式：** 5 个 bug 中有 4 个在 0–1 天内已有修复 PR 在途——响应性强，但**均未合并**，因此用户今日仍暴露于风险之中。

---

## 6. 功能请求与路线图信号

- **回退透明度（[#8103](https://github.com/agentscope-ai/QwenPaw/issues/8103)）** — 鉴于已有就绪 PR [#8628](https://github.com/agentscope-ai/QwenPaw/pull/8628)，很可能下一个落地。
- **仅行级 `recall_history` 搜索（[#8621](https://github.com/agentscope-ai/QwenPaw/issues/8621)）** — 对内存密集型 agent 的效率提升；PR #8625 已就绪。
- **执行中引导 `/steer` 命令（[#8318](https://github.com/agentscope-ai/QwenPaw/pull/8318)）** — 长期 PR（自 2026-10-19 起），处理 issue #1775；若落地将是有意义的 agent 控制功能。
- **按任务思考级别（[#8626](https://github.com/agentscope-ai/QwenPaw/pull/8626)）** 和 **快速添加模型 UX（[#8434](https://github.com/agentscope-ai/QwenPaw/pull/8434)）** 表明对**成本控制和减少配置摩擦**的需求。

**对下一个 minor/beta 的预测：** 回退元数据 + 频道 `enabled` 修复 + 召回搜索优化是最有可能纳入的内容；`/steer` 和 cron 思考级别可能推迟到后续版本。

---

## 7. 用户反馈总结

- **痛点 #1 — 静默失败侵蚀信任。** 用户反复遇到行为变化（频道被禁用、模型被替换、图片被丢弃）**且没有任何日志或 UI 信号**。#8475 和 #8103 中反复出现的"无警告日志"一语反映了对可审计、透明 agent 行为的需求。
- **痛点 #2 — Windows/桌面版 beta 不稳定。** Issue #8627 显示环境相关的失败（Git 长路径、[#8568](https://github.com/agentscope-ai/QwenPaw/pull/8568) 中的 DAACL/临时目录限制）阻碍 Windows Hub Local 用户安装默认技能。
- **痛点 #3 — 成本/延迟敏感。** 用户正在优化 token 支出（cron 思考级别、仅行级召回、避免超大图片重试），表明是生产/流水线用途而非业余实验。
- **满意度信号：** 多位贡献者（如 `txohyeah`、`AdityaxDeore`）的同日 issue 到 PR 周转表明社区响应迅速、参与积极——但未合并的积压是可见的摩擦点。

---

## 8. 积压关注

需要维护者关注的长期未响应项：

- **[#7066](https://github.com/agentscope-ai/QwenPaw/pull/7066)** — OAuth2 轮换 `refresh_token` 持久化（首次贡献者，开设于 **2026-08-16**，已近 3 个月未处理）。修复 #7053；一个安全相关的认证 bug 仍待处理。
- **[#7307](https://github.com/agentscope-ai/QwenPaw/pull/7307)** — 将 provider 配置串联到模型管理（开设于 2026-08-26，约 2.5 个月）。针对已知五步摩擦的 UX 整合。
- **[#8318](https://github.com/agentscope-ai/QwenPaw/pull/8318)** — `/steer` 执行中引导（开设于 2026-10-19）。功能规模较大，需要评审容量。
- **[#8524](https://github.com/agentscope-ai/QwenPaw/pull/8524)** — Windows 上桌面版 HTML 预览路径解码（开设于 2026-11-01，Under Review）。若长期搁置存在 Windows 回归风险。
- **[#8103](https://github.com/agentscope-ai/QwenPaw/issues/8103)** — 可观测性 issue 自 2026-10-04 起 open；修复现已通过 #8628 提供，应关联/关闭。

**健康度说明：** 贡献者供给健康；瓶颈是**评审/合并带宽**。清理超过 60 天的 PR（#7066、#7307）并解决重复的 #8622/#8624 配对，将实质性提升项目速度和贡献者留存。

</details>

<details>
<summary><strong>ZeroClaw</strong> — <a href="https://github.com/zeroclaw-labs/zeroclaw">zeroclaw-labs/zeroclaw</a></summary>

# ZeroClaw 项目摘要 — 2026-11-09

## 1. 今日概览

ZeroClaw 展现出**极高的开发活跃度**，过去 24 小时内有 50 个 PR 和 14 个 issue 更新，表明这是一个活跃且健康的开源项目。活动高度集中在 **v0.9.0 发布里程碑**周围，具体涉及网关/仪表盘登录、受限仪表盘聊天以及身份访问相关的工作。值得注意的是，今日没有发布新版本。开放的 PR 积压量很大（43 个开放 vs. 7 个已合并/关闭），表明相对于贡献量而言，审查能力可能是一个瓶颈。安全、身份和平台可移植性相关的工作主导着流水线。

---

## 2. 发布

*过去 24 小时内无新发布。略过。*

---

## 3. 项目进展

除以下今日关闭的项目外，没有已合并/关闭的 PR 附带公开报告的摘要：

- **#10501 → #10502（已关闭）** — 通过将图片部分重新定位到 `user` 消息中，MCP 工具结果图片在 OpenAI 兼容提供商上不再返回 HTTP 400。修复 PR #10502 与该 issue 一同关闭。这修复了一个影响 MCP 工作流的真实互操作性 bug。
- **#11406（已关闭）** — `fix(config)!: refuse macOS broad roots in the filesystem channel`，完成了由 Windows 宽根验证（#11394）开启的平台对齐工作。`!` 标记表示**破坏性配置变更**，因此维护者应确认发布说明中已记录此项。
- **#11268（已关闭）** — 网关策略发布指南的文档对齐。

**推进中的功能（开放但活跃）：**
- 通过共享 RPC 客户端为非管理员账户提供受限仪表盘聊天：**#12244**，以及包含审查的后续版本 **#12298**（两者均标记为 `release:v0.9.0`）。
- 通过核心 RPC 的插件 webhook 转发：**#11320**、**#11322**（堆叠、有依赖关系）。
- 用于名册身份的本地密码认证提供商：**#11264**、**#11265**、**#11313**（身份访问栈）。

---

## 4. 社区热点

| 项目 | 信号 | 潜在需求 |
|------|--------|-----------------|
| [#7432](https://github.com/zeroclaw-labs/zeroclaw/issues/7432) — 运行时、仪表盘登录与网关交付跟踪器 | 16 条评论，每日更新；`risk:high`、`release:v0.9.0` | **v0.9.0 发布受此跟踪器门控。** 社区/维护者正在协调仪表盘认证 + 网关交付的排序和验收证据。 |
| [#11322](https://github.com/zeroclaw-labs/zeroclaw/pull/11322) 和 [#11320](https://github.com/zeroclaw-labs/zeroclaw/pull/11320) — 基于 RPC 的插件 webhook | 大型堆叠 PR，`size:XL`、`needs-author-action` | 用户希望**插件 webhook** 通过核心以有限范围交付；复杂性正推动一项明确的放置例外提案（#11317）。 |
| [#9872](https://github.com/zeroclaw-labs/zeroclaw/issues/9872) — 有界委托解析到错误的工作区 | 5 条评论，`priority:p1`、`risk:high` | **多代理委托沙箱化**是一项信任关键功能；代理写入错误工作区对协作代理设置而言是严重的安全隐患。 |
| [#9597](https://github.com/zeroclaw-labs/zeroclaw/issues/9597) — 持久/可归因的对等代理轮次 | 5 条评论，`topic:agent-loop`、`risk:high` | 用户需要**可审计、生命周期绑定的对等消息传递** — 目前对等轮次是分离的 `process_message` 调用。已派生后续 #12287（中继链跳数限制）。 |
| [#7461](https://github.com/zeroclaw-labs/zeroclaw/issues/7461) — Windows/macOS 上的 CI 矩阵 | 5 条评论，`priority:p2` | **跨平台可靠性** — 测试套件仅在 Linux 上运行，而 Lint/Build 已在三个操作系统上运行。 |

**分析：** 主导主题是**多租户、身份感知的代理运维**（仪表盘登录、受限聊天、名册密码、中继提供的仪表盘）。第二个主题是**多代理安全与归因**（委托沙箱化、对等轮次持久性、中继链边界限定）。维护者的注意力正被拉向网关/RPC 边界的安全加固。

---

## 5. Bug 与稳定性

按严重程度排序：

1. **S2 — [#12289](https://github.com/zeroclaw-labs/zeroclaw/issues/12289)：已禁用的代理仍通过操作员 WebSocket 聊天和受限 RPC 运行轮次。** *尚未关联修复 PR。* 这是一个**安全相关的授权缺口**（禁用 ≠ 不活跃），应优先处理。
2. **S2 — [#9872](https://github.com/zeroclaw-labs/zeroclaw/issues/9872)：有界委托写入委托者的工作区**而非其自身工作区。`priority:p1`、`risk:high`。尚无修复 PR；多代理设置的沙箱隔离回归。
3. **S2 — [#12288](https://github.com/zeroclaw-labs/zeroclaw/issues/12288)："全部丢弃"保留了随后被区块保存所持久化的草稿** — Web 仪表盘数据完整性 bug（已丢弃的值仍可被保存）。
4. **P1 — [#10501](https://github.com/zeroclaw-labs/zeroclaw/issues/10501)（已通过 #10502 修复）：** MCP 图片工具结果在 OpenAI 兼容提供商上导致 HTTP 400。
5. **#12096** — 仪表盘登录在 IdP 故障/429/5xx 期间被丢弃（重新验证将不可达与拒绝混为一谈）。自托管 SSO 的可靠性/用户体验风险。
6. **#11868**（PR）— 代理可能在派发前重复相同的工具调用；PR 提议在派发前拒绝并保留关闭记录。

**要点：** 两个开放的 S2 issue（#12289、#12288）涉及**授权**和**数据持久化**，均为近期引入（日期为 2026-11-08）。已禁用代理 bug 是今日最紧急的稳定性/安全事项。

---

## 6. 功能请求与路线图信号

| 请求 | 链接 | 可能纳入 v0.9.0？ |
|---------|------|----------------|
| 为非管理员账户提供受限仪表盘聊天 | [#11984](https://github.com/zeroclaw-labs/zeroclaw/issues/11984) | **是** — 标记为 `release:v0.9.0`；实现在 #12244/#12298 |
| 网关测试：JWKS 验证、失败写入、退役竞态 | [#11985](https://github.com/zeroclaw-labs/zeroclaw/issues/11985) | **很可能**与受限聊天一同进行 |
| 非管理员创建代理，带有界风险字段 | [#12290](https://github.com/zeroclaw-labs/zeroclaw/issues/12290) | 可能的后续工作 |
| 在中继提供的仪表盘上拒绝凭证/登录配置写入 | [#12285](https://github.com/zeroclaw-labs/zeroclaw/issues/12285) | 安全加固，可能 v0.9.x |
| Bash/zsh 语法感知命令策略 | [#12296](https://github.com/zeroclaw-labs/zeroclaw/issues/12296)、PR [#11994](https://github.com/zeroclaw-labs/zeroclaw/pull/11994)、[#12295](https://github.com/zeroclaw-labs/zeroclaw/pull/12295) | 进行中 |
| 对等中继链跳数边界（A→B→A） | [#12287](https://github.com/zeroclaw-labs/zeroclaw/issues/12287) | #9597 的后续，可能 v0.9.x |
| 持久、可归因的对等代理轮次 | [#9597](https://github.com/zeroclaw-labs/zeroclaw/issues/9597) | 较大工程；可能错过 v0.9.0 |

**预测：** v0.9.0 将以**仪表盘身份 + 受限聊天 + 网关安全策略发布**为核心，shell 语法策略改进和平台 CI 矩阵作为稳定性配套。对等代理持久性和中继链边界更可能落在 v0.9.x/v0.10。

---

## 7. 用户反馈摘要

- **今日浮现的痛点：** 多代理委托写入错误工作区（#9872）、仪表盘表单草稿未被真正丢弃（#12288）、已禁用代理仍可被访问（#12289）、身份提供商故障期间登录被丢弃（#12096）。这些是来自在共享/仪表盘上下文中运行代理的用户的**安全性、正确性和可靠性**投诉。
- **隐含的使用场景：** 多代理"委托者/执行助理 → 研究员"拓扑；带 OIDC 的自托管仪表盘；携带图片的 MCP 工具流水线；macOS 默认设置上的 Bash/zsh shell 策略。
- **满意度信号：** 建设性、以后续为导向的 issue 文化（维护者请求的后续如从 #9597 派生的 #12287；带有明确依赖说明的堆叠 PR 纪律）表明即使发现 bug，贡献者仍然积极参与且满意。
- **不满意度信号：** 审查延迟 — 许多 `needs-author-action`/`do-not-merge` 的 XL PR 自九月下旬/十月以来一直开放，这可能会令作者沮丧（例如 #11226、#11265、#11313）。

---

## 8. 积压关注

需要维护者关注的长期、高重要性事项：

1. **[#7432](https://github.com/zeroclaw-labs/zeroclaw/issues/7432)** — 发布阻塞的 v0.9.0 跟踪器（自 2026-06-09 开放）。其进展直接决定发布时间。
2. **[#7461](https://github.com/zeroclaw-labs/zeroclaw/issues/7461)** — Windows/macOS CI 矩阵（自 2026-06-10 开放，`in-progress`，5 条评论）。在此落地之前，跨平台回归仍无防护。
3. **[#11322](https://github.com/zeroclaw-labs/zeroclaw/pull/11322) / [#11320](https://github.com/zeroclaw-labs/zeroclaw/pull/11320)** — 插件 webhook RPC（自 2026-10-01 开放，`size:XL`、堆叠、`needs-author-action`）。依赖链（#11319→#11339→#11165→#11186→#11320→#11322）是维护负担；维护者的审查/合并节奏将解锁一个大型功能集群。
4. **[#11265](https://github.com/zeroclaw-labs/zeroclaw/pull/11265) / [#11264](https://github.com/zeroclaw-labs/zeroclaw/pull/11264) / [#11313](https://github.com/zeroclaw-labs/zeroclaw/pull/11313)** — 密码生命周期/授权发布栈（自 9 月 30 日–10 月 1 日开放，`risk:high`、堆叠）。身份访问是一个战略领域；此处老化的 XL PR 存在腐化风险。
5. **[#11301](https://github.com/zeroclaw-labs/zeroclaw/pull/11301)** — 跨 CLI/网关/RPC 的代理重命名已提交重命名恢复（`priority:p1`、`size:XL`，自 2026-10-01 开放）。解决重命名失败时的状态滞留问题 — 若不解决将产生高用户影响。

---

### 健康评估

**总体：健康且高度活跃，存在审查能力风险。** 安全和身份工作通过跟踪器和堆叠 PR 组织良好，bug 正在被及时修复（#10501）。然而，开放（43）与关闭（7）PR 的比例，加上多个 `XL`/`needs-author-action` PR 已老化 5–6 周，表明**维护者审查存在瓶颈**。两个新的 S2 bug（#12289、#12288）是近期且未修复的 — 它们应在 v0.9.0 网关功能工作之前进行分诊，以避免在发布中带入授权缺口。

</details>

---
*本日报由 [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar) 自动生成。*