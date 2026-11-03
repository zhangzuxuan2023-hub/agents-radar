# OpenClaw 生态日报 2026-11-03

> Issues: 391 | PRs: 500 | 覆盖项目: 5 个 | 生成时间: 2026-11-03 10:36 UTC

- [OpenClaw](https://github.com/openclaw/openclaw)
- [Hermes Agent](https://github.com/nousresearch/hermes-agent)
- [IronClaw](https://github.com/nearai/ironclaw)
- [QwenPaw](https://github.com/agentscope-ai/QwenPaw)
- [ZeroClaw](https://github.com/zeroclaw-labs/zeroclaw)

---

## OpenClaw 项目深度报告

# OpenClaw 项目摘要 — 2026-11-03

## 1. 今日概览

OpenClaw 在过去 24 小时内保持高度活跃：**391 个 issue 有更新**（222 个 open/活跃，169 个 closed），**500 个 PR 有变动**（306 个 open，194 个 merged/closed），并发布了一个新版本（**v2026.10.10**）。整体走向明显偏向功能开发——今日关闭的 issue 中很大一部分是被清扫的 `stale` 项，而非新近修复，这掩盖了真实的修复率。稳定性问题集中在一个主题上：**会话回收中的崩溃循环/状态丢失故障、原生关闭卡死，以及 `doctor` 挂起**，其中若干被标记为 P0/P1 和发布阻断项。安全与隐私信号同样值得关注（浏览器 CDP 结果中的凭据泄露、transcript 脱敏污染、`tools.fs.workspaceOnly` 绕过）。整体项目健康度：**吞吐量高，但 P0/P1 积压深度偏高，且关闭速度依赖 stale 清扫**。

---

## 2. 发布 — v2026.10.10

**亮点**
- **备份安全**：经 secret 脱敏的 Git 备份现在覆盖 PostgreSQL 导出，并省略 bundled-plugin 凭据、webhook token 及 credential blob，同时保留非机密插件状态。（#174698、#186953）——感谢 @RomneyDa、@art22s、@jlapenna。
- **Codex**：changelog 被截断；所提供数据中未列出进一步变更。

**破坏性变更 / 迁移说明：** 所提供摘录中未明确记录。但请注意今日 issue/PR 数据中相邻的升级风险信号：
- **PR #191825**（open）报告从 2026.10.10 执行 `openclaw update` 时，在旧版 `exec-approvals.json` 迁移过程中卡在 `"database is locked"`。这是本版本的升级路径风险，本身不算破坏性变更，但对采用者相关。
- **Issue #187917**（P2）：2026.10.8 上的更新失败 `updater-runtime-retention`（linux/arm64，Node 26）。
- **Issue #169423**（closed，stale）：codex 插件迁移在 10.1 之后卡在 `unfinished`。

**采用建议：** 升级到 2026.10.10 以获取备份安全收益是合理的，但请配合执行一次 `openclaw doctor` 健全性检查，并在非生产 profile 上验证 exec-approvals 迁移。另见 §5 中的 P1 `doctor` 挂起报告。

---

## 3. 项目进展

**今日 merged/closed 的 PR（194 个 merged/closed；所提供样本中的代表性条目）：**
- **PR #191813** [CLOSED] — fix(backup)：在禁用 `fs-safe` 原生模块的情况下修复 macOS Git 备份清理测试失败（P3）。解除本地 `pnpm release:push` 门禁阻塞。
- **PR #191816** [CLOSED] — perf(ci)：降低 web-fetch provider 边界检查的内存保留（P2，证据充分）。
- **PR #191826** [CLOSED] — docs(testing)：将 mock 注解与基线更新保持在一起（XS）。

**已关闭 issue（今日 169 个；样本显示大量 `stale` 清扫）：**
- **#159356** [CLOSED，stale] — llama.cpp manager/embedding HTTP 500；报告了运维层面的解决（RAM 4 GB → 8 GB）。
- **#158966** [CLOSED，stale] — 浏览器凭据化 CDP WebSocket URL 进入了面向模型的标签页结果（安全相关，但似乎是通过 stale 关闭的——请核实修复情况）。
- **#169154** [CLOSED] — 包含 `Authorization: Bearer <token>` 的频道消息在 transcript 幂等性冲突上失败。
- **#161208** [CLOSED，stale] — 心跳未复用 Anthropic prompt 缓存（成本影响，约 $0.40–0.95/心跳）。
- **#156864** [CLOSED，stale] — `tools.alsoAllow: ["browser"]` 未授予 browser 工具。

**推进中的功能（证据充分、等待维护者查看的 open PR）：**
- **#191715** fix(oc-path)：完成多行 frontmatter 解析（diamond lobster）。
- **#191791** fix(knowledge)：编码哈希的 Markdown 文件名。
- **#181835** fix(ui)：在 Control UI 频道会话中展示 bounded-context 警告。
- **#191749** improve(runtime)：减少 agent 准备/状态读取中的等待。

**已合并功能说明：** 样本中的"merged/closed PR"集合以 XS/S 级 docs、perf 和测试卫生为主——这与一个由 changelog 驱动的大型发布（v2026.10.10）已经切割、后续加固工作刚刚开始的情况一致。

---

## 4. 社区热点话题

**Issue（按评论数）：**
1. **[#159356](https://github.com/openclaw/openclaw/issues/159356)** — 30 条评论，CLOSED（stale）— llama.cpp manager/embedding 就绪状态不匹配（`HTTP 500 Failed to read connection`）。**需求：** 为受管子进程提供可靠的就绪/存活信号；内存压力关联。
2. **[#177656](https://github.com/openclaw/openclaw/issues/177656)** — 25 条评论，OPEN，P1 — 原生 host exec 返回 `Direct shared-state reader requires current integrity admission` 且无子进程结果，在稳定版 2026.10.5 上出现 28+ 次失败。**需求：** 向调用方暴露确定性的错误；不发生静默的共享状态损坏。
3. **[#120385](https://github.com/openclaw/openclaw/issues/120385)** — 24 条评论，OPEN，P2 — Code Mode 工具目录在 cron/memory-flush 轮次中不完整 → `Unknown tool id`，仅以"Exec failed."形式暴露。**需求：** 为非交互轮次提供完整工具目录；可操作的错误展示。
4. **[#166634](https://github.com/openclaw/openclaw/issues/166634)** — 23 条评论，OPEN，**P0**，发布阻断项 — 延迟回收检查点失败导致崩溃恢复后 agent 不可用（`SQLite session reclamation commit checkpoint expired`）。**需求：** gateway 死亡后的持久恢复。
5. **[#178359](https://github.com/openclaw/openclaw/issues/178359)** — 23 条评论，OPEN，P1 — `doctor --fix --non-interactive` 空转 15+ 分钟且无输出（macOS）。**需求：** 长时修复路径中的进度/可观测性。

**PR（信号最强的 open 项）：**
- **[#163765](https://github.com/openclaw/openclaw/pull/163765)** — iOS 个人 Tailscale 身份；XL，高合并风险（安全边界、兼容性）。状态：需要证据。
- **[#191749](https://github.com/openclaw/openclaw/pull/191749)** — 减少 runtime 等待；XL，已可交由维护者查看。
- **[#186888](https://github.com/openclaw/openclaw/pull/186888)** — 长时命令自动化的实时输出；需要安全审查。
- **[#163267](https://github.com/openclaw/openclaw/pull/163267)** — 同频道线程召回，而无需 agent 全局可见性；兼容性风险，等待作者。

**深层分析：** 评论密集的 issue 并非功能请求——它们是**可诊断性问题**。用户反复报告"静默成功"（health 显示绿色但 agent 卡死）和"不透明失败"（泛化的 Exec failed、无输出挂起）。这表明维护者下一个高价值投入应是**不变式检查 + 暴露错误码**，而非净新增能力。

---

## 5. 缺陷与稳定性（按严重程度排序）

**P0 / 发布阻断：**
| Issue | 状态 | 描述 |
|---|---|---|
| **[#166634](https://github.com/openclaw/openclaw/issues/166634)** | OPEN，P0，阻断项 | 延迟回收提交检查点失败 → 崩溃恢复后 agent 永久 `UNAVAILABLE`。**未发现修复 PR。** |
| **[#169844](https://github.com/openclaw/openclaw/issues/169844)** | OPEN，P0，阻断项 | Windows 上 Control UI Gateway 更新卡在"Activating"；计划任务被留置为 Disabled，gateway 停止。 |
| **[#142821](https://github.com/openclaw/openclaw/issues/142821)** | OPEN，P0，安全审查 | 默认开启的 transcript 脱敏污染重放的上下文（掩码传播到命令/文件/回复中）。已关联 open PR。 |
| **[#164800](https://github.com/openclaw/openclaw/issues/164800)** | OPEN，P0，修复形态清晰 | 2026.9.7→2026.9.8 受管更新后激活驱动死亡，需重启主机方可恢复。 |

**P1：**
- **[#177656](https://github.com/openclaw/openclaw/issues/177656)** — 共享状态 integrity-admission 错误，无子进程结果（稳定版 2026.10.5）。
- **[#178359](https://github.com/openclaw/openclaw/issues/178359)** — `doctor --fix --non-interactive` 空转 15+ 分钟。
- **[#187900](https://github.com/openclaw/openclaw/issues/187900)** — `doctor` 在启动时无限挂起，所有模式（Linux，2026.10.8）。**与 #178359 为伴生问题——很可能同根。**
- **[#144336](https://github.com/openclaw/openclaw/issues/144336)** — SIGUSR1 延迟重启导致关闭失败（`service child cleanup identity lost`）——与 #177656 同族。
- **[#186684](https://github.com/openclaw/openclaw/issues/186684)** — claude-cli 工具列表变更使原生会话失效；历史重新播种被拒 → agent 无历史重启。
- **[#145309](https://github.com/openclaw/openclaw/issues/145309)** — claude-cli 忽略 `CLAUDE_CONFIG_DIR`；transcript 缺失导致重置。
- **[#169844](https://github.com/openclaw/openclaw/issues/169844)** — （同上）

**P2 / 值得注意的回归：**
- **[#182891](https://github.com/openclaw/openclaw/issues/182891)** — prompt 缓存从未复用（`cache read dropped N -> 0`）；成本影响。
- **[#154104](https://github.com/openclaw/openclaw/issues/154104)** — 空闲 Gateway 挂 4 个 Matrix E2EE 账户：约 50% CPU，约 52 MB/min 写入（相对 2026.7.1 的回归）。
- **[#138561](https://github.com/openclaw/openclaw/issues/138561)** — 活跃记忆召回在 2026.8.2 之后停止，info 级别下静默。
- **[#173058](https://github.com/openclaw/openclaw/issues/173058)** — 安全：通过将 state root 作为允许 cwd 绕过 `tools.fs.workspaceOnly`。
- **[#162585](https://github.com/openclaw/openclaw/issues/162585)** — Windows 插件源码捕获 staging 永不收敛（290 MB → 612 MB+/24 分钟）。
- **[#137613](https://github.com/openclaw/openclaw/issues/137613)** — CLI 后端上 pre-compaction 记忆刷新被禁用；明显修复会触发 `compactionCount` 陷阱。

**修复 PR 状态：** 仅 **#142821** 有已关联的 open PR；**#164800** 被标记为 fix-shape-clear。**#166634、#169844、#177656、#178359、#187900** 似乎**尚无上游修复 PR**——这一簇是该​​项目最紧迫的稳定性债务。

---

## 6. 功能请求与路线图信号

**高信号请求（有关联/open PR，即可能的下个版本候选）：**
- **[#155633](https://github.com/openclaw/openclaw/issues/155633)** — 将 Databricks Unity Gateway 作为官方 provider。**PR #155634 已 open，证据充分** → 近期可能落地。
- **[#128090](https://github.com/openclaw/openclaw/issues/128090)** — Review 文件中的渲染 Markdown 预览（👍4）。
- **[#138279](https://github.com/openclaw/openclaw/issues/138279)** — Linux aarch64 伴生构建（deb + AppImage）；Windows ARM 已发布 → 打包对称性请求。
- **[#186888](https://github.com/openclaw/openclaw/pull/186888)** — 运行中命令自动化的实时输出（PR open，安全审查）。
- **[#163267](https://github.com/openclaw/openclaw/pull/163267)** — 有作用域的同频道线程召回。

**路线图形态的请求（尚无 PR，标记为产品决策）：**
- **[#112372](https://github.com/openclaw/openclaw/issues/112372)** — 为 `sessions_send` 提供可选的 `wakeOnReply`（已推迟，origin-bound 完成）。
- **[#138990](https://github.com/openclaw/openclaw/issues/138990)** — 为长时前台 WebChat 运行提供可强制执行的检查点/有界让出。
- **[#158068](https://github.com/openclaw/openclaw/issues/158068)** — Swarm 群体的自适应测试时计算。
- **[#107433](https://github.com/openclaw/openclaw/issues/107433)** — `openclaw backup` 的 manifest 支撑的刻意省略（platinum hermit，安全审查）。
- **[#158800](https://github.com/openclaw/openclaw/issues/158800)** — 在 Control UI 中 `/new` 之后浏览/分叉先前的频道片段。
- **[#136271](https://github.com/openclaw/openclaw/issues/136271)** — 用于频道文件上传的 msteams 动态 SharePoint 站点解析。

**预测：** 下一个补丁版本可能落地 **Databricks provider（#155634）、实时自动化输出（#186888），以及 oc-path/knowledge 文件名修复（#191715、#191791）**。语言/locale 修复（#191806、#191823）表明一轮本地化工作正在进行中。

---

## 7. 用户反馈摘要

**痛点（反复出现、跨 issue）：**
1. **虚假绿色 health / 静默卡死。** #159789（agent 锁定时"health 保持绿色"）、#166634（永久 `UNAVAILABLE`）、#177656（无子进程结果）、#120385（仅"Exec failed"）。用户若不翻日志就无法区分健康与卡死。
2. **Doctor 是负担。** #178359 和 #187900 均报告 `doctor` 在多个 OS 上挂起且无输出——本应作为恢复工具的工具自身不可靠，加剧了故障。
3. **升级脆弱性。** #169844、#164800、#187917、#183479、#169423、#157812：Windows 计划任务挂起、激活驱动死亡、保留包阻塞、codex 迁移停滞。多跳升级（#169423）是真实的运维隐患。
4. **成本与性能回归。** #182891 和 #161208（prompt 缓存不复用）、#154104（Matrix E2EE 空闲消耗约 50% CPU / 52 MB/min）——给运维者带来可衡量的基础设施成本。
5. **安全面。** #158966（CDP 凭据流向模型）、#173058（`workspaceOnly` 绕过）、#142821（脱敏污染）——信任边界卫生是现实关切。

**数据中可见的使用场景：** 具有重度频道路由的多 agent 集群（Telegram/WeChat/Matrix/Slack）、长生命周期会话（#151962 中约 310k token）、由 Claude 订阅驱动的 CLI（#145309、#186684、#158922）、Windows 桌面/计划任务部署（#169844、#162585），以及气隙/LAN 本地模型服务器（llama.cpp、Ollama、llmman——见 #175923、#175414、#174877、#174861）。

**满意信号：** 在 provider 广度上有强劲的社区贡献（#155634）、有针对性的 UI/docs 打磨 PR（许多由 @ericcurtin、@vincentkoc 提交），以及被列为亮点的 2026.10.10 备份安全工作。**不满意**集中在运维可靠性与诊断不透明上——同样这些主题主导了评论数前 10 的榜单。

---

## 8. 积压关注（需维护者注意）

| 条目 | 存在时长 | 为何重要 |
|---|---|---|
| **[#142821](https://github.com/openclaw/openclaw/issues/142821)**（P0，安全，已关联 PR） | 约 2 个月 | P0 安全/数据丢失且有一个 *open* PR——尽管 P0 标签仍存在 stale-PR 风险。 |
| **[#112313](https://github.com/openclaw/openclaw/issues/112313)**（死信出站队列，永久性） | 约 3.5 个月 | Diamond-lobster 评级；条目不可读/不可删除；health 永远计入它们。无修复。 |
| **[#107433](https://github.com/openclaw/openclaw/issues/107433)**（备份省略，platinum，安全审查） | 约 4 个月 | 阻塞安全的备份/恢复工作流；产品 + 安全决策待定。 |
| **[#137613](https://github.com/openclaw/openclaw/issues/137613)**（CLI 后端记忆刷新禁用） | 约 2 个月 | Diamond-lobster，标记为产品决策；已知陷阱有文档但无负责人。 |
| **[#144797](https://github.com/openclaw/openclaw/issues/144797)**（复用的 claude-cli 会话保留已删除的 `--mcp-config`，工具 401） | 约 2 个月 | 陈旧但可复现；影响 auth-provider。 |
| **[#112372](https://github.com/openclaw/openclaw/issues/112372)**（推迟的 `wakeOnReply`） | 约 3.5 个月 | 陈旧，产品决策；是异步编排需求的基础。 |
| **[#90074](https://github.com/openclaw/openclaw/issues/90074)**（OpenAI 图像生成忽略 Codex OAuth） | 约 5 个月 | 非常旧的 diamond-lobster；1 👍。 |
| **[#163765](https://github.com/openclaw/openclaw/pull/163765)**（iOS 个人身份，XL）— 1 个月 | 长生命周期的 XL PR，带安全边界合并风险，"需要证据"。 |
| **[#155634](https://github.com/openclaw/openclaw/pull/155634)**（Databricks provider）— 6 周 | 证据充分的功能 PR，等待维护者查看。 |

**元观察：** 若干安全相关 issue（#158966、#161208）被作为 **stale** 关闭，而非修复——建议进行策略审查，确保 stale 清扫不会静默地让未解决的信任边界发现被搁置。

---

*数据窗口：2026-11-02 → 2026-11-03 UTC。评论数反映样本前 50 的 issue / 前 30 的 PR。PR 信息流中的 `Comments: undefined` 是数据字段缺失，而非零活动。*

---

## 横向生态对比

# 跨项目对比报告 — AI Agent / 个人 AI 助手开源生态
**数据窗口：2026-11-02 → 2026-11-03 UTC** | 项目：OpenClaw、Hermes Agent、IronClaw、QwenPaw、ZeroClaw

---

## 1. 生态概览

本次调研的五个项目处于同一收敛品类下的不同成熟阶段：持久化、多渠道、具备工具执行、记忆与网关编排能力的 AI Agent。活跃度呈现明显两极分化——OpenClaw、Hermes Agent、QwenPaw 与 ZeroClaw 均处于**迁移后或发布前加固阶段**（统一网关回归、v0.9.0 门禁、2.3.0 beta 稳定化），而 IronClaw 实际上处于休眠状态，仅有自动化依赖变更。整个生态的主导工程成本已不再是模型能力，而是**运行可靠性**：会话持久性、静默失败诊断与安全边界执行。社区成员的安全审计如今在至少三个项目中已成为常规的、逐行引用的活动，表明该生态正被作为真实基础设施而非玩具助手部署。贡献量与维护者审查/合并能力之间始终存在缺口。

---

## 2. 活跃度对比

| 项目 | Issue（触及 / 开启 / 关闭） | PR（触及 / 开启 / 合并-关闭） | 发布状态 | 健康评分（分析师） | 备注 |
|---|---|---|---|---|---|
| **OpenClaw** | 391 / 222 / 169 | 500 / 306 / 194 | **已发布 v2026.10.10**（备份安全） | **6.5 / 10** — 吞吐量高，P0/P1 积压深 | 关闭速度因陈旧清理而虚高；4 个未关闭 P0 阻塞项，其中若干无修复 PR |
| **Hermes Agent** | 50 / ~48 / 12+ | 50 / 38 / 12 | **v0.22.6** 汇总（约 776 个 PR），变更日志推迟至 v0.23.0 | **7.0 / 10** — 承压但可运转 | 统一网关（#106742）余波占主导；47 条评论的分诊帖 |
| **IronClaw** | 0 / 0 / 0 | 4 / 2 / 2 | 无 | **5.0 / 10** — 稳定但休眠 | 仅 dependabot 的 `rustls` 升级；**窗口内无人工创作活动** |
| **QwenPaw** | 18 / ~12 / 6 | 37 / ~26 / 11 | 无（2.3.0-beta.1–b3 测试中） | **7.5 / 10** — Issue→PR 转化强 | 当日快速修复；一个未处理的安全问题（#8513） |
| **ZeroClaw** | 50 / 48 / 2 | 50 / 40 / 10 | 无（`v0.9.0` 门禁进行中） | **6.0 / 10** — 贡献高，审查瓶颈 | 开启:关闭比 24:1；S1 发布门禁缺陷仍开启 |

*健康评分为吞吐量、积压深度、关键缺陷的修复 PR 覆盖率与发布节奏的定性综合——非项目背书的指标。*

**解读：** OpenClaw 与 ZeroClaw 存在相同的结构性问题——缺陷*发现*速度超过修复*吞吐量*与审查能力。QwenPaw 相对接单量拥有最佳的关闭纪律。Hermes Agent 拥有最高的绝对 PR 量，但正在消化一次大型架构迁移。IronClaw 的零人工活动窗口是这组中唯一的真正红旗。

---

## 3. OpenClaw 的定位

**相对同行的优势**
- **最大的绝对社区与吞吐量**——24 小时内触及 391 个 Issue / 500 个 PR，按窗口计约为 Hermes 或 ZeroClaw 的 8–10 倍，比 QwenPaw 高出数个数量级。
- **最成熟的发布纪律**——本窗口内唯一一个*附带已记录安全加固内容*的版本化发布（覆盖 PostgreSQL 与插件凭据的脱敏 Git 备份）。
- **最广的提供商与渠道覆盖**——Databricks provider PR 附带充分证明（#155634），另有 Telegram/WeChat/Matrix/Slack 路由已投入生产使用。
- **Issue 层面明确的安全文化**——P0 安全标签（`transcript redaction poisoning` #142821、`tools.fs.workspaceOnly` 绕过 #173058）以严重性标签跟踪，而非被埋没。

**看似强大实则薄弱之处**
- **陈旧清理式关闭模式是真实风险**：两个安全相关 Issue（#158966 CDP 凭据泄露、#161208 prompt-cache 成本）被以*陈旧*为由关闭，而非修复。这是同行（尤其是 ZeroClaw 逐行引用的审计文化）处理得更好的治理欠账。
- **P0 阻塞项缺乏修复 PR**（#166634 崩溃恢复后永久 `UNAVAILABLE`、#169844 Windows 激活挂起）——贡献者的广度并未转化为对最难项的归属。

**技术路线差异**
- OpenClaw 依托**丰富的插件/扩展模型，捆绑凭据并具备原生宿主执行路径**（`Direct shared-state reader`、完整性准入错误）——比 QwenPaw 的 Python/AgentScope 栈或 Hermes 的网关中心设计更重。
- 它是本组中唯一将**专用备份/恢复子系统附带安全审查**作为一等发布特性的项目——表明其有贴近企业的意图。

**社区规模对比：** OpenClaw ≫ Hermes ≈ ZeroClaw > QwenPaw ≫ IronClaw。贡献者 handle 的长尾（许多为顺手的文档/UI PR）表明 OpenClaw 已跨入广泛贡献者领域，而 Hermes 与 ZeroClaw 仍呈现集中、重复贡献者的模式。

---

## 4. 共同技术焦点领域

以下需求在**三个或更多项目**中反复出现，使其成为生态级而非项目特定的问题：

| 需求 | 项目 | 具体需求 |
|---|---|---|
| **消除静默失败 / 可诊断性** | OpenClaw（#166634、#177656、#120385）、Hermes（#143547）、QwenPaw（#8488、#8505、#8549）、ZeroClaw（#12014、#12194、#12203） | 在启动/配置时大声失败；呈现可操作的错误码而非 "Exec failed" 或 "unknown"；健康检查必须检测卡死状态，而非仅存活。 |
| **跨传输的会话/轮次持久性** | OpenClaw（#166634 崩溃恢复）、Hermes（#146344 admission_conflict、#145889 网关卡死）、ZeroClaw（#7759 轮次/WS 解耦）、QwenPaw（#8532 仅追加会话存储） | 长时间运行的轮次必须经受重载、断连、重启；仅追加/带检查点的会话状态正成为收敛设计。 |
| **对*已声明*配置的安全边界执行** | OpenClaw（#173058 workspaceOnly 绕过）、ZeroClaw（#9394 pairing_dashboard 未读取、#12052 XFF 伪造）、QwenPaw（#8513 无门禁 shell 操作）、Hermes（#135415 凭据回退） | 被接受但未读取的配置键；默认开启却污染上下文的保护；已记录却不运行的守卫。主题是**"宣称 ≠ 执行"。** |
| **上下文窗口 / 提供商配置正确性** | ZeroClaw（#12014/#12194 静默 32k 回退）、QwenPaw（#8496 max_tokens 注入、#8236 Ollama num_ctx）、OpenClaw（#182891 prompt-cache 未复用）、Hermes（#148380 Anthropic json_mode） | BYO-provider 用户遭遇静默错误；需要验证配置并对未知模型大声失败。 |
| **插件/依赖生命周期健壮性** | OpenClaw（捆绑插件备份）、Hermes（#148337 catalog 固定）、QwenPaw（#8265 热安装、#8553 DEPENDENCY_PLAN_UNVERIFIED、#8554 插件 CLI）、ZeroClaw（#11703 tool_search 缓存失效） | 共享依赖模型导致级联激活失败；插件加载必须隔离且发行版无关。 |
| **Windows 平台对等性** | Hermes（#68128、#144964）、QwenPaw（今日 6+ 个 Windows 缺陷）、OpenClaw（#162585、#169844）、ZeroClaw（#12126/#12127 UNC 凭据泄露） | CRLF、编码（GBK/CP932）、PyInstaller 资源、UNC 路径、计划任务——尽管有真实用户基础，Windows 始终是二等公民。 |

**跨领域根因：** 大多数"不稳定"报告实际上是**可观测性失败**——系统正在做某事（降级回退、静默跳过、陈旧配置），而用户无法将其与挂起或缺陷区分。

---

## 5. 差异化分析

| 维度 | OpenClaw | Hermes Agent | IronClaw | QwenPaw | ZeroClaw |
|---|---|---|---|---|---|
| **功能焦点** | 全栈助手：提供商、渠道、备份、宿主执行 | 桌面 + 机器人编排 + 插件 catalog | 基准测试/延迟工具 | 插件生态 + 长会话存储 + 渠道 | 安全优先守护进程：配对、OIDC、A2A、网关可靠性 |
| **目标用户** | 高级运维者、多 Agent 集群、自托管者 | 桌面知识工作者 + 多机器人协作、i18n 用户 | 内部/基准消费者 | 技术娴熟用户、中文 Windows、渠道密集部署 | 注重安全的部署者、代理后的多 Agent 拓扑 |
| **技术栈** | 原生宿主执行、完整性准入共享状态、捆绑插件 | 统一网关架构（近期迁移） | Rust（`rustls`、延迟运行器）、自动化维护 | Python/AgentScope、beta 桌面构建、SQLite 会话工作 | Rust、crate 拆分（`zeroclaw-a2a`）、distroless 镜像、OIDC |
| **发布姿态** | 版本化发布并附带安全内容 | 快速汇总补丁 | 无 | Beta 门禁稳定化 | 安全+可靠性发布门禁（`v0.9.0`） |
| **标志性优势** | 广度 + 备份安全 | Bot Mode 群聊 + 桌面打磨 | （休眠） | Issue→PR 速度 + i18n/渠道集成 | 严谨的社区安全审计 |
| **标志性弱点** | 陈旧清理掩盖未解决的安全问题 | 迁移余波；永久卡死缺陷（#145889） | 无活动 | 安全问题 #8513 未分配；Windows 缺陷 | 24:1 开启:关闭；审查能力 |

**架构分歧说明：** Hermes 与 ZeroClaw 近期都集中到**基于网关的会话准入模型**——两者现在都在支付回归代价（准入冲突、profile 作用域渗漏、worker 权限拒绝）。OpenClaw 使用**共享状态 + 完整性准入**模型，失效类别不同（检查点过期、身份丢失）。QwenPaw 正收敛于**仅追加会话存储**，若顺利落地，可能被证明是三种设计中最健壮的——值得作为参考实现关注。

---

## 6. 社区动能与成熟度

**第一梯队——高动能、广泛贡献者：** **OpenClaw。** 绝对活跃度最大；生产级贡献者多样性；但动能部分*人为*（陈旧清理），P0 归属薄弱。功能面成熟度高，可靠性中等。

**第二梯队——高动能、迁移承压：** **Hermes Agent** 与 **ZeroClaw。**
- Hermes 展现强劲的维护者吞吐量与高度参与的社区作为 QA 层运作（47 条评论分诊）。它正处于**架构迁移后的稳定化**——v0.23.0 将是稳定化发布。
- ZeroClaw 正**快速迭代迈向安全+可靠性 v0.9.0**，但受限于审查能力。其社区是本组中安全素养最高的。

**第二梯队 b——高动能、纪律性关闭：** **QwenPaw。** 绝对量低于第一/二梯队，但拥有最佳的 Issue 到 PR 转化与最干净的关闭比。处于**活跃 beta 稳定化**——大多数"缺陷"是 beta 测试面，而非架构腐化。本组中从报告到修复最快的路径。

**第三梯队——休眠：** **IronClaw。** 零人工创作活动；仅自动化依赖升级。安全上稳定低风险，但**本窗口无法评估为活跃维护**——在依赖它之前监控人工 PR 的回归。

**成熟度框定：** 功能成熟但可靠性不成熟（OpenClaw、Hermes、ZeroClaw）是常见模式；QwenPaw 最接近均衡；IronClaw 仅维护。**本组中无项目达到"可靠性成熟"。**

---

## 7. 趋势信号（面向 AI Agent 开发者）

提取自跨项目社区反馈，按信号强度排序：

1. **可靠性是新差异化因素——诊断不透明是首要抱怨。** 在所有四个活跃项目中，参与度最高的帖子是可诊断性问题，而非功能请求。*可操作项：* 在添加能力之前，投资于**不变量检查 + 呈现错误码 + 进度可观测性**（尤其在长时间运行的修复/doctor 路径中）。"虚假绿色健康"是信任杀手。

2. **会话/轮次持久性是架构战场。** 多个项目独立收敛于**仅追加、带检查点、传输解耦的会话状态**（ZeroClaw #7759、QwenPaw #8532、OpenClaw 崩溃恢复 #166634）。*可操作项：* 从第一天起就为经受重载/重启/断连的轮次设计；将 WS/socket 生命周期视为短暂。

3. **"宣称 ≠ 执行"是主导的安全失效模式。** 配置键被接受但未读取（ZeroClaw）、默认开启的保护污染数据（OpenClaw）、已声明的守卫未起门禁作用（QwenPaw）。*可操作项：* **在启动时验证并执行配置，对未知/未读取键大声失败，并审计安全开关确实运行。** 社区安全审计（逐行引用、可复现）如今已是预期——为其构建。

4. **BYO-provider 正确性是一类静默数据丢失。** 未知模型与缺失上下文窗口配置静默回退到错误默认值（ZeroClaw 32k、QwenPaw max_tokens 注入、OpenClaw 缓存未复用）。*可操作项：* **对模型/提供商配置进行大声验证**与明确的上下文窗口契约；不要假设。

5. **成本可观测性是一等用户需求。** Prompt-cache 未复用、空闲 CPU/GPU 消耗（Hermes 桌面 25–45%、OpenClaw Matrix E2EE 约 50% CPU）、心跳缓存未命中——用户测量并反感基础设施成本。*可操作项：* **呈现缓存命中率、空闲资源使用与每轮成本**；将缓存稳定性视为特性（例如延迟 MCP 加载导致缓存失效，ZeroClaw #11703）。

6. **插件/依赖隔离是生态健康的前提。** 共享依赖模型导致级联激活失败；catalog 固定与插件 CLI/扩展点正浮现。*可操作项：* **隔离插件依赖环境、固定 catalog 条目、暴露资源生命周期钩子**——插件生态是护城河，那里的脆弱会阻碍第三方增长。

7. **Windows 与 i18n 是真实、服务不足的细分市场。** 一致的 Windows 特定缺陷簇与明确的非英语部署需求（Hermes 17 语言对等、QwenPaw 中文 Windows 数据丢失修复）。*可操作项：* **将 Windows 与本地化作为一等测试目标**，而非事后考虑——编码（GBK/CP932/CRLF）、路径处理（UNC、中文路径）与打包完整性。

8. **维护者审查能力，而非贡献量，是生态瓶颈。** OpenClaw 无归属的 P0、ZeroClaw 停滞的 S1 修复 PR、QwenPaw 相互竞争的重复 CRLF PR 都指向同一方向。*对开发者的可操作项：* 提交可复现、逐行引用的报告（获得快速处理的都具备此特征）；对维护者：**对安全标签 Issue 设定明确分诊 SLA**并合并重复修复努力。

---

**决策者要点：** OpenClaw 在功能面上最广、最接近生产就绪，但背负未解决的可靠性与陈旧清理式安全欠账——采用时需以 `doctor` 优先的升级纪律并对"以陈旧关闭"的安全项进行独立验证。Hermes 与 ZeroClaw 正稳定化迈向可靠性发布，是优先考虑安全严谨与持久会话的团队的最佳选择，前提是能容忍迁移期摩擦。QwenPaw 为渠道/插件密集与中文 Windows 部署提供最佳关闭纪律与最快修复周期。IronClaw 在人工创作活动恢复前应视为休眠。贯穿所有项目：**将可观测性与会话持久性作为核心工程预算，而非打磨项。**

---

## 同赛道项目详细报告

<details>
<summary><strong>Hermes Agent</strong> — <a href="https://github.com/nousresearch/hermes-agent">nousresearch/hermes-agent</a></summary>

# Hermes Agent — 项目摘要
**日期：2026-11-03** | 仓库：[NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent)

---

## 1. 今日概览

Hermes Agent 正处于高速运转状态：过去 24 小时内有 50 个 issue 和 50 个 PR 被处理，同时发布了一个补丁版本（v0.22.6），汇总了自 v0.22.5 以来约 776 个已合并 PR。活动主要由 **统一网关迁移（#106742）** 的后续影响主导，该迁移持续在桌面端、会话、cron 和多配置文件场景中产生合并后回归问题（参见 #143547，47 条评论）。Issue 构成偏向*稳定性与平台特定 bug*（Windows、WSL2、macOS 桌面端），而非新功能开发，不过今天也有若干 Bot Mode 群聊功能 PR 落地。整体项目健康状况**强劲但承压**——维护者吞吐量很高，但统一网关的覆盖面正在产生长尾的会话状态与权限 bug，这很可能将主导 v0.23.0 的稳定化工作。

---

## 2. 发布

### v0.22.6 — 补丁版本（2026-11-02）
**类型：** 补丁/汇总发布（稳定渠道：CLI、安装器、Microsoft Store、Docker、Hermes Cloud）。

> "补丁版本。此标签将自 v0.22.5 以来合并的约 776 个 PR 汇总为一个稳定版本……本窗口的完整精选说明随 v0.23.0 一同发布。"

**备注：**
- 此版本**未宣布破坏性变更**；明确为稳定化汇总。
- **迁移说明：** 精选变更日志推迟至 v0.23.0。拉取 v0.22.6 的稳定渠道用户仍应注意，**v0.22.5 → v0.22.6 跨越了统一网关迁移窗口**，因此持有迁移前会话的桌面端用户可能会遇到下述遗留会话问题（#146344、#143547）。
- **对用户的影响：** 约 776 个 PR 的庞大体量被汇入一个补丁区间，进一步印证 v0.22.x 是 v0.23.0 功能发布之前的*稳定化线*。

🔗 [Release v0.22.6](https://github.com/NousResearch/hermes-agent/releases/tag/v0.22.6)

---

## 3. 项目进展

过去 24 小时内有 **12 个 PR 被合并/关闭**（38 个仍开放）。值得关注的已关闭项与活跃工作流：

- **#68927 [已关闭，P1]** — 桌面端：长任务后，Enter 到达后端但用户气泡未渲染 `comp/desktop, area/sessions`。一个高优先级的会话状态/UI 不同步 bug 已关闭。🔗 [#68927](https://github.com/NousResearch/hermes-agent/issues/68927)
- **#48411 [已关闭]** — `uv.exe` 被标记为恶意软件（Bitdefender/Defender 误报）。已在 main 上解决。🔗 [#48411](https://github.com/NousResearch/hermes-agent/issues/48411)
- **Bot Mode 群聊（活跃 PR）：**
  - **#148422** — 群组房间现在外观与原生聊天一致，并显示每个 bot 的实时工作/排队/通过状态。🔗 [#148422](https://github.com/NousResearch/hermes-agent/pull/148422)
  - **#148418** — 跨机器 bot 席位恢复（混合机器群聊）。🔗 [#148418](https://github.com/NousResearch/hermes-agent/pull/148418)
  - **#148436** — 重新启动后 Bots 面板不再固定显示"gateway not connected"。🔗 [#148436](https://github.com/NousResearch/hermes-agent/pull/148436)
- **托管群聊权限交接（修复）：** **#148438** — 房间在权限交接期间保持可服务（修复 `member.unavailable / authority epoch mismatch`）。🔗 [#148438](https://github.com/NousResearch/hermes-agent/pull/148438)
- **插件目录维护：** **#148337** 将 `tirith` 从 0.1.0 更新至 0.1.1（SHA 固定、人工审核策略）。🔗 [#148337](https://github.com/NousResearch/hermes-agent/pull/148337)

**主题：** 大部分已合并/开放的工作是**增量正确性修复**（权限消息、安装戳处理、heredoc 审批解析、推理超时匹配），而非全新功能——这与迁移后加固阶段相符。

---

## 4. 社区热点

按评论活动排序（过去 24 小时）：

| 排名 | 条目 | 评论数 | 信号 |
|---|---|---|---|
| 1 | **[#143547](https://github.com/NousResearch/hermes-agent/issues/143547)** — main 上统一网关合并后问题 | **47** | 参与度最高的 issue；社区正在积极分诊 #106742 合并后在 CLI/TUI/桌面端/API/bots/cron 上产生的回归。 |
| 2 | **[#127647](https://github.com/NousResearch/hermes-agent/issues/127647)** — 桌面端空闲资源消耗追踪（CPU/GPU/内存） | **35** | 对桌面端性能的持续担忧；范围明确的追踪器，附有关联 PR。 |
| 3 | **[#68128](https://github.com/NousResearch/hermes-agent/issues/68128)** — Windows 上 WhatsApp 桥接因 no-breakaway 作业对象报 `WinError 5` | **18** | 长期存在的 Windows 平台 bug；反应少但持续。 |
| 4 | **[#68927](https://github.com/NousResearch/hermes-agent/issues/68927)** — 桌面端长任务 Enter/气泡不同步 *（已关闭）* | **15** | 已解决；强大的社区压力奏效。 |
| 5 | **[#52554](https://github.com/NousResearch/hermes-agent/issues/52554)** — 选中文本上的浮动"Quote"按钮 | **9**（👍4） | 反应最高的功能请求；聊天引用的 UX 打磨。 |

**底层需求分析：**
- **#143547** 表明社区正在充当统一网关的*事实上的 QA 层*——用户在一个帖子中汇总跨组件故障。这表明迁移发布时在配置文件/cron/会话方面的预发布覆盖不足。
- **#127647** 和 **#52554** 表明桌面端用户希望获得**性能可预测性**和**原生感的聊天 UX**——桌面端正被当作主要客户端使用，而非次要客户端。

---

## 5. Bug 与稳定性

按严重程度排序（P1 → P3）。*在可识别处注明修复 PR。*

### 🔴 P1
- **#68927 [已关闭]** — 桌面端长任务 Enter 提交到后端但用户气泡从未渲染；文本停留在编辑器中。*今日关闭。* 🔗 [#68927](https://github.com/NousResearch/hermes-agent/issues/68927)

### 🟠 P2 — 会话状态与网关回归（最集中）
- **#143547** — 统一网关合并后问题（元 issue，47 条评论）。🔗 [#143547](https://github.com/NousResearch/hermes-agent/issues/143547)
- **#142032 [WSL2]** — 每个托管 worker（kanban/cron/delegation）在 9–65 秒后因 `btime` 在精确 `create_time` 相等时重新锚定而被以粘性 `permission_denied` 拒绝。**无关联修复 PR。** 🔗 [#142032](https://github.com/NousResearch/hermes-agent/issues/142032)
- **#87575** — 配置文件作用域泄漏：一个配置文件的 cron 任务导致另一个配置文件的实时轮次错误解析 `HERMES_HOME`。🔗 [#87575](https://github.com/NousResearch/hermes-agent/issues/87575)
- **#146344** — 遗留桌面通道会话：每次网关重启后出现 `admission_conflict`；仅焦点标签页可恢复；排队轮次挂起。🔗 [#146344](https://github.com/NousResearch/hermes-agent/issues/146344)
- **#145889** — 轮次关键路径上的内存提供者 `initialize()` 可能永久卡死网关；进程在持有 `gateway.lock` 时无法杀死（需重启）。**严重。** 🔗 [#145889](https://github.com/NousResearch/hermes-agent/issues/145889)
- **#135415** — 即使 `claude_code` 被抑制，Anthropic token 解析器仍回退到 Claude Code 凭据（安全边界）。🔗 [#135415](https://github.com/NousResearch/hermes-agent/issues/135415)
- **#75772** — 文件变更验证器页脚在 `[SILENT]` 之后追加，破坏了 cron 投递抑制。🔗 [#75772](https://github.com/NousResearch/hermes-agent/issues/75772)
- **#144964 [Windows]** — `--safe-mode` 配合 `openai-codex` 永久挂起（`version_info._run_git` 中孤立的 git 进程）。🔗 [#144964](https://github.com/NousResearch/hermes-agent/issues/144964)

### 🟡 P3 — 今日新增（2026-11-03 提交）
- **#148437** — `cron remove` 被陈旧写入器刷新静默复活（收缩合并保护仅限创建）。🔗 [#148437](https://github.com/NousResearch/hermes-agent/issues/148437)
- **#148434** — 已删除的 cron 任务 `workdir` 每次运行都在网关准入处因 `invalid_params` 失败。🔗 [#148434](https://github.com/NousResearch/hermes-agent/issues/148434)
- **#148433** — 重定向轮次期间关闭会用等待哨兵覆盖已流式传输的回复。🔗 [#148433](https://github.com/NousResearch/hermes-agent/issues/148433)
- **#148430** — 检查点 `DEFAULT_EXCLUDES` 遗漏图像/PDF 二进制文件；快照膨胀至 2+ GB。🔗 [#148430](https://github.com/NousResearch/hermes-agent/issues/148430)
- **#148380** — Anthropic 上的插件 `json_mode` 总是 400（缺少 `additionalProperties`）。🔗 [#148380](https://github.com/NousResearch/hermes-agent/issues/148380)
- **#148432** — 目录将 `hindsight` 固定在缺少 Windows cp932 编码修复的提交上。🔗 [#148432](https://github.com/NousResearch/hermes-agent/issues/148432)
- **#144879** — 当 `messages.id` 值较大时 `optimize-storage` 永不完成。🔗 [#144879](https://github.com/NousResearch/hermes-agent/issues/144879)

**进行中的修复 PR（开放）：**
- **#133597** — 隔离适配器构造失败（webhook/网关启动）。🔗 [#133597](https://github.com/NousResearch/hermes-agent/pull/133597)
- **#106221** — 将 Settings/会话变更保留在所有者网关上。🔗 [#106221](https://github.com/NousResearch/hermes-agent/pull/106221)
- **#135880** — 网关锁目录跟随真实主目录（容器修复）。🔗 [#135880](https://github.com/NousResearch/hermes-agent/pull/135880)
- **#138095** — 被拒绝的读取报告 `Permission denied` 而非 `File not found`。🔗 [#138095](https://github.com/NousResearch/hermes-agent/pull/138095)
- **#136550** — 分离启动不再被视为长期前台进程。🔗 [#136550](https://github.com/NousResearch/hermes-agent/pull/136550)
- **#135548** — 纯数据 heredoc 不再触发硬性下限。🔗 [#135548](https://github.com/NousResearch/hermes-agent/pull/135548)

**稳定性结论：** **统一网关是主要风险载体**——会话准入、配置文件作用域、worker 权限和锁处理均可追溯至它。永久卡死 bug（#145889）是运维上最严重的开放项。

---

## 6. 功能请求与路线图信号

| Issue | 诉求 | 下一版本可能性 |
|---|---|---|
| **[#52554](https://github.com/NousResearch/hermes-agent/issues/52554)**（👍4） | 选中聊天文本上的浮动"Quote"按钮 | **中** — UX 范围小、可见度高；桌面端打磨版本的合适候选。 |
| **[#148435](https://github.com/NousResearch/hermes-agent/issues/148435)** | PDF/DOCX/HTML 全窗口/全屏预览 + 弹出标签页 | **中** — 非开发者文档工作流是明确的增长细分。 |
| **[#148306](https://github.com/NousResearch/hermes-agent/issues/148306)** | 用于受保护决策复用的 agent-loop 钩子（跳过已决定的模型回调） | **低–中** — 插件生态赋能项，需要 API 设计。 |
| **[#148341](https://github.com/NousResearch/hermes-agent/issues/148341)** | 移动端 beta i18n — 发布与桌面端/web 相同的 17 语言 UI | **高** — 对等性差距令人尴尬；本地化基础设施已存在。 |
| **[#141738](https://github.com/NousResearch/hermes-agent/issues/141738)** | 视觉提示硬编码为英文（其中一个促使中文输出）；使其可配置 | **高** — 明确的类 bug i18n 缺陷，修复面极小。 |
| **[#135166](https://github.com/NousResearch/hermes-agent/issues/135166)** | 插件目录规则 16 澄清（来自同一仓库的第二个条目） | **低** — 政策问题，非路线图。 |

**预测：** v0.23.0 将**主要是统一网关稳定化版本**，同时发布 i18n 修复（#141738、#148341）和 Bot Mode 群聊对等性。较大的功能钩子（#148306）可能推迟至 v0.24+。

---

## 7. 用户反馈总结

**痛点（不满）：**
- **桌面端性能：** 在高 DPI Windows 上流式传输时 GPU 引擎占用 25–45%（#145979），以及空闲资源消耗追踪（#127647）——用户对桌面端开销相对于 TUI（0.1%）感到不满。
- **网关迁移影响：** 排队轮次挂起、重启后 `admission_conflict`、卡死进程需重启（#145889）——对网关可靠性的信任受到侵蚀。
- **跨连接中继：** SSH/遗留仪表板路由上的 Bot DM 静默永不到达（#146254）。
- **平台特定摩擦：** WinError 5 作业对象（#68128）、WSL2 权限拒绝（#142032）、`--safe-mode` 挂起（#144964）、应用内浏览器 MFA 失败（#119360）。
- **iOS beta 链接失效：** 官方 TestFlight 邀请返回"Beta Not Found"（#147556，👍2）——入门障碍。

**使用场景（满意信号）：**
- **非开发者文档工作流：** 桌面端用于生成客户就绪的 PDF/DOCX/HTML（#148435）——超越开发者的健康扩展。
- **多 bot 协作：** 用户跨机器构建群聊（#148418/#148422）——Bot Mode 正被用于真实编排。
- **i18n 需求** 来自非英文部署（#141738、#148341）——国际用户群真实且积极发声。
- **积极信号：** 47 条评论的众包分诊（#143547）显示了一个积极参与、愿意共同调试的社区——尽管有摩擦，这是强有力的留存指标。

---

## 8. 积压关注

长期未回应或高影响、需要维护者关注的条目：

| 条目 | 时长/状态 | 重要性 |
|---|---|---|
| **[#68128](https://github.com/NousResearch/hermes-agent/issues/68128)** | 自 **2026-07-20** 开放（约 3.5 个月），P3 | Windows WhatsApp 桥接 `WinError 5` — 反复出现的平台家族问题；有文档化回退方案但未实现。 |
| **[#87575](https://github.com/NousResearch/hermes-agent/issues/87575)** | 自 **2026-08-16** 开放，P2 | 多路复用配置文件作用域泄漏 — 静默数据路由错误，影响范围大，无修复 PR。 |
| **[#127647](https://github.com/NousResearch/hermes-agent/issues/127647)** | 追踪器自 **2026-09-29**，P2 | 桌面端空闲资源消耗 — 追踪器范围明确（35 条评论）但无统一修复。 |
| **[#75772](https://github.com/NousResearch/hermes-agent/issues/75772)** | 自 **2026-08-01** 开放，P2 | cron `[SILENT]` 抑制被破坏 — 消息静默泄漏到消息平台。 |
| **[#146344](https://github.com/NousResearch/hermes-agent/issues/146344)** | 2026-10-29 开放，P2 | 重启后遗留桌面会话 — 影响迁移群体；无关联修复 PR。 |
| **[#106221](https://github.com/NousResearch/hermes-agent/pull/106221)** | PR 自 **2026-09-09** 开放（约 2 个月） | 桌面端跨所有者 Settings/会话变更 — 长期运行的修复，需要审查/合并推进。 |
| **[#133597](https://github.com/NousResearch/hermes-agent/pull/133597)** | PR 自 **2026-10-06** 开放 | 网关适配器隔离 — 抢救的修复停滞在审查中。 |

**维护者行动呼吁：** **配置文件作用域泄漏（#87575）** 和 **cron 静默抑制（#75772）** 属于静默失败类别——它们不会崩溃，而是丢失或泄漏数据——应优先于可见但嘈杂的桌面端 UI bug。长期开放的 PR **#106221** 和 **#133597** 正在阻塞相关的网关/会话修复，明确的分类处理将有所裨益。

---

*本摘要基于 2026-11-03 的 GitHub 活动快照生成。所有链接指向 NousResearch/hermes-agent。*

</details>

<details>
<summary><strong>IronClaw</strong> — <a href="https://github.com/nearai/ironclaw">nearai/ironclaw</a></summary>

# IronClaw 项目摘要 — 2026-11-03

## 1. 今日概览
IronClaw 在本周期内呈现出安静的仅维护活动特征：零 issue 处理、零新版本发布，流水线中仅有自动化依赖 PR。过去 24 小时内有四个 PR 更新——两个新开的 dependabot 对 `rustls` 的升级（0.23.43 → 0.23.46），以及两个较早的 `rustls` 升级（至 0.23.45）已被关闭/取代。本窗口内没有功能开发、缺陷修复或社区讨论。整体项目健康状况稳定，但从开发角度看处于休眠状态，活动完全由供应链卫生所主导。

## 2. 版本发布
本周期内未发布新版本。

## 3. 项目进展
今日未发现推进功能或修复的已合并/已关闭 PR。两个已关闭的 PR（#8151、#8152）是升级至 `rustls` 0.23.45 的依赖更新，已被 0.23.46 的更新（#8165、#8166）取代，属于常规依赖更替而非功能性进展。净效果：项目的 Rust TLS 依赖在根 crate 和 `/harness/latency/runner` 子 crate 中均正向 0.23.46 推进。

## 4. 社区热点
本窗口内没有 Issue 或 PR 记录到评论或反应——最"活跃"的条目是四个 dependabot PR，全部零 👍 且无讨论：
- [PR #8165](https://github.com/nearai/ironclaw/pull/8165) — 将 `rustls` 升级至 0.23.46（根）
- [PR #8166](https://github.com/nearai/ironclaw/pull/8166) — 将 `rustls` 升级至 0.23.46（`/harness/latency/runner`）
- [PR #8151](https://github.com/nearai/ironclaw/pull/8151) — 已关闭，被取代的 0.23.45 升级（runner）
- [PR #8152](https://github.com/nearai/ironclaw/pull/8152) — 已关闭，被取代的 0.23.45 升级（根）

底层信号：今日未捕捉到面向用户的需求或争论；唯一的"话题"是自动化依赖维护，这暗示项目或处于成熟/稳定阶段，或本窗口内社区参与度较低。

## 5. 缺陷与稳定性
今日未报告缺陷、崩溃或回归。`rustls` 升级与安全/健壮性相关（TLS 握手处理——例如"Delay saving KX hint until handshake confirmation"提交），修复 PR 已开放（#8165、#8166）。严重程度：信息性；数据未暗示任何已知漏洞或服务中断。

## 6. 功能请求与路线图信号
本窗口内未提交功能请求。唯一的前瞻性信号是 `/harness/latency/runner` 组件的持续维护，暗示对延迟基准测试基础设施的持续投入。无法从今日数据中自信预测任何路线图事项。

## 7. 用户反馈摘要
今日 Issue 或 PR 评论中未出现用户反馈、痛点或用例报告。无法从本窗口评估满意度/不满意度；无开放 issue 可能表明健康的稳定状态，也可能仅反映报告量较低。

## 8. 积压关注
不存在长期未答复的 Issue（issue 总数：0）。唯一值得维护者轻度关注的事项是两个等待审查/合并的开放 dependabot PR：
- [PR #8165](https://github.com/nearai/ironclaw/pull/8165)（ opened 2026-11-02）
- [PR #8166](https://github.com/nearai/ironclaw/pull/8166)（ opened 2026-11-02）

两者均为低风险、已开放一天的自动化升级；及时合并可保持 TLS 依赖为最新版本，并避免根与延迟 runner 之间出现重复版本漂移。

---
**健康评估：** 稳定且低风险，但除自动化依赖管理外开发活动基本为零。建议监测人工编写的 PR/issue 是否回归，以确认项目并未停滞。

</details>

<details>
<summary><strong>QwenPaw</strong> — <a href="https://github.com/agentscope-ai/QwenPaw">agentscope-ai/QwenPaw</a></summary>

# QwenPaw 项目摘要 — 2026-11-03

## 1. 今日概览

QwenPaw 展现出**高速的开发活跃度**，过去 24 小时内有 18 个 issue 和 37 个 PR 更新，表明贡献者基础活跃、分诊周期积极。项目目前处于 **2.3.0 beta 稳定化阶段**（全篇提及 v2.3.0-beta.1 至 b3），beta 测试中涌现出大量 bug 报告——尤其集中在桌面构建、插件依赖解析和频道集成方面。今日合并或关闭了 11 个 PR，包括修复视觉压缩资源打包问题（#8514、#8488）以及文件工具中 CRLF/编码保留问题的修复。然而，**没有发布新版本**，且仍有若干高严重度 issue 处于开启状态，包括一个安全治理缺口（#8513）和一个插件激活阻塞问题（#8553）。整体项目健康状况**良好但需保持警惕**——大多数 bug 从 issue 到 PR 的快速周转表明维护响应及时，但 beta 测试面正暴露出跨平台特定路径的集成脆弱性。

---

## 2. 发布

**无。** 过去 24 小时内未发布新版本。

---

## 3. 项目进展

**今日合并/关闭的 PR（共 11 个）：**

- **#8514 [已关闭]** — `fix(desktop): bundle visual_compression glyph atlas assets` — 修复 PyInstaller 构建遗漏问题：`agents/context/visual_compression/assets` 未包含在 `_data_dirs` 中，导致桌面构建时出现 `FileNotFoundError`。直接解决 Issue #8488。[链接](https://github.com/agentscope-ai/QwenPaw/pull/8514)

- **#8556 [已关闭]** — `fix(console): select decision-model approval directly` — 简化决策模型工具审批 UX，移除冗余的“使用决策模型”开关；选择审批模式即为唯一的启用方式。[链接](https://github.com/agentscope-ai/QwenPaw/pull/8556)

- **#8463 [已关闭]** — `feat(models): run local Piccolo with bidirectional candidate scoring` — 使已下载的 Piccolo 模型能够为自动工具审查和 ReMe 上下文压缩提供原生 Jev 决策，使用双向提示注意力而非因果 Chat Completions 适配器。[链接](https://github.com/agentscope-ai/QwenPaw/pull/8463)

- **#8496 [已关闭]** — `fix(context): support slow summary models and consistent token limits` — 解决 OpenAI 兼容提供商在模型卡片设置 `generate_kwargs.max_tokens` 时出现的 HTTP 400 错误（AgentScope 2.0.10 注入 `max_completion_tokens`，而 Scroll 同时传递 `max_tokens`）。[链接](https://github.com/agentscope-ai/QwenPaw/pull/8496)

- **#8552 [已关闭]** — `fix(tests): reopen backup archive before Windows restore verification` — 仅测试修复，解决 Windows ZIP 句柄路径分隔符不匹配问题（`BadZipFile`）。[链接](https://github.com/agentscope-ai/QwenPaw/pull/8552)

**同样作为 issue 关闭（今日 6 个）：** #8265（插件热安装在 `importlib.invalidate_caches()` 处中止）、#8488（桌面视觉压缩资源）、#4312（agent.json 热重载范围）、#8013（技能池下载超时）、#8528（Markdown 预览相对路径重写）、#8540（滚动历史启动同步）。

**关键进展：** `edit_file` 的 CRLF/编码保留修复有**两个竞争的开放 PR**（#8235 和 #8433），针对 Issue #8414 和 #8415——这是 Windows 用户的热修复活跃区域。

---

## 4. 社区热点

| 条目 | 评论数 | 链接 | 底层需求 |
|------|----------|------|-----------------|
| **#8265** 插件热安装中止（`importlib.invalidate_caches` 抛出异常） | 5 | [Issue](https://github.com/agentscope-ai/QwenPaw/issues/8265) | **插件生态可靠性**——用户安装第三方插件，需要健壮且与发行版无关的加载机制。这是讨论最多的 issue，现已关闭。 |
| **#8488** 桌面构建遗漏 visual_compression 资源 | 3 | [Issue](https://github.com/agentscope-ai/QwenPaw/issues/8488) | **打包完整性**——桌面用户期望与源码构建功能对等；资源缺失会静默破坏 Visual Compact。 |
| **#8505** 网关裸 `APIError` 被归类为 `unknown`（无重试/回退） | 3 | [Issue](https://github.com/agentscope-ai/QwenPaw/issues/8505) | **对 OpenAI 兼容中继的弹性**——用户通过发出非标准错误形状的中国网关路由；模型回退至关重要。 |
| **#8553** QQ 频道插件 `DEPENDENCY_PLAN_UNVERIFIED`（aliyun-hub） | 2 | [Issue](https://github.com/agentscope-ai/QwenPaw/issues/8553) | **共享依赖隔离**——没有依赖环境的 hub 插件阻塞下游消费者；激活审计过于严格。 |
| **#8511** 飞书频道间歇性 `No active model configured` | 2 | [Issue](https://github.com/agentscope-ai/QwenPaw/issues/8511) | 频道消息处理中的**并发正确性**——入站消息与进行中的 agent 处理之间的竞态。 |
| **#8532** `feat(chat): append-only session storage, paginated history, native Turn forks` | — | [PR](https://github.com/agentscope-ai/QwenPaw/pull/8532) | **长对话可扩展性**——基于仅追加事实的持久每会话 SQLite，取代可变快照。XXXL 规模，审查中。 |
| **#8554** `feat(plugins): CLI commands and extension points` | — | [PR](https://github.com/agentscope-ai/QwenPaw/pull/8554) | **插件开发者工效学**——Click 命令注册、依赖检查、资源生命周期钩子。 |

**分析：** 社区正同时在**三个战线**推进：(1) 插件/频道集成可靠性，(2) 桌面分发对等性，(3) 长会话持久性。PR #8532（仅追加会话存储）和 #8554（插件 CLI）是进行中杠杆率最高的架构变更。

---

## 5. Bug 与稳定性

**按严重度排序：**

🔴 **严重 — 安全**

- **#8513 [开启]** — *Shell 包装的出站操作在 AUTO/SMART 下执行时无发现或审批* — `tool_guard` 和治理通道中的检测器缺口；SMTP 发送 / HTTP POST / `@file-to-network` 全部未受门控通过。在 `approval_level: AUTO` 下这是**数据外泄风险**。尚无修复 PR。[Issue](https://github.com/agentscope-ai/QwenPaw/issues/8513)

🟠 **高 — 数据丢失/损坏**

- **#8414 [开启]** — *`edit_file` 在定向编辑时将全部 CRLF 转换为 LF* — 重写整个文件的换行风格；Windows 用户丢失行尾约定。**修复 PR 已存在：#8235、#8433**（均在审查中，相互竞争）。[Issue](https://github.com/agentscope-ai/QwenPaw/issues/8414)

- **#8549 [开启]** — *模型配置写入路径绕过字段验证* — 无效的 `thinking_budget`/`max_input_length` 被持久化；**提供商在重启后静默消失**。修复 PR **#8550** 已开启（首次贡献者）。[Issue](https://github.com/agentscope-ai/QwenPaw/issues/8549)

🟠 **高 — 不稳定**

- **#8505 [开启]** — *网关裸 `APIError` 被归类为 `unknown`* — HTTP 502 中继上无重试、无模型回退。无修复 PR。[Issue](https://github.com/agentscope-ai/QwenPaw/issues/8505)

- **#8553 [开启]** — *QQ 频道 `DEPENDENCY_PLAN_UNVERIFIED`* — 当 hub 插件（aliyun-hub）没有依赖环境时激活中止。PR **#8527**（依赖状态根报告）相关但非直接修复。[Issue](https://github.com/agentscope-ai/QwenPaw/issues/8553)

- **#8511 [开启]** — *飞书频道在并发处理期间 `No active model configured`* — 在稳定版 2.2.2 上复现，非 beta 引入。竞态条件。[Issue](https://github.com/agentscope-ai/QwenPaw/issues/8511)

🟡 **中 — 平台/UX**

- **#8555 [开启]** — *新任务工作区选择在后续消息中恢复为默认值*（v2.3.0b3）[Issue](https://github.com/agentscope-ai/QwenPaw/issues/8555)
- **#8517 [开启]** — *Hub Docker 运行时非 root 下 `working/config.json` Permission denied* [Issue](https://github.com/agentscope-ai/QwenPaw/issues/8517)
- **#8502 [开启]** — *桌面 HTML 预览“在浏览器中打开”在中文路径/工作区外文件上失败* — 修复 PR **#8524** 已开启（解码本地路径）。[Issue](https://github.com/agentscope-ai/QwenPaw/issues/8502)
- **#8547 [开启]** — *Creator：捆绑的猫视角示例在 Windows 上仍无法打开（#7291 的回归）* [Issue](https://github.com/agentscope-ai/QwenPaw/issues/8547)
- **#8534 [开启]** — *Creator：TTS 无法使用阿里云 Token Plan 密钥* — `qwen-audio-tts` 使用不同的 DashScope 服务；UI 中缺少协议选项。[Issue](https://github.com/agentscope-ai/QwenPaw/issues/8534)
- **#8533 [开启]** — *Codex/Qoder 后端 cron 任务将非空请求误判为空* — `ValueError: Third-party agent requests require text or attachments`。[Issue](https://github.com/agentscope-ai/QwenPaw/issues/8533)

**修复覆盖率：** 10 个开放 bug 中有 4 个有相关修复 PR（#8414→#8235/#8433、#8549→#8550、#8502→#8524、#8488→#8514 已关闭）。**安全问题 #8513 无修复 PR**，应优先处理。

---

## 6. 功能请求与路线图信号

**进行中的功能 PR（可能进入下一版本）：**

- **#8532** [开启，XXXL] *仅追加会话存储 + 分页历史 + 原生 Turn 分叉* — 面向长对话的重大架构变更。**2.3.0 正式版或 2.4.0 的有力候选。** [PR](https://github.com/agentscope-ai/QwenPaw/pull/8532)
- **#8554** [开启，XXXL] *插件 CLI 命令与扩展点* — 通过 `register_cli(CliSpec(...))`、依赖检查、资源生命周期扩展 Click 树。**插件生态成熟度。** [PR](https://github.com/agentscope-ai/QwenPaw/pull/8554)
- **#8557** [开启，M] *cron 任务的每次运行会话* — 限制 cron 历史增长；目前每次运行都追加到一个带完整历史的专用每任务会话。[PR](https://github.com/agentscope-ai/QwenPaw/pull/8557)
- **#8444** [开启，L] *Jev 完成决策* — 可选的决策模型自检，当纯文本回复触发定性评分标准时，标记承诺但未交付的工作。[PR](https://github.com/agentscope-ai/QwenPaw/pull/8444)
- **#8599/#8581** [开启，S] *默认安装 Matrix E2EE 依赖* + 旧状态迁移简化 — Matrix 频道加固。[PR #8559](https://github.com/agentscope-ai/QwenPaw/pull/8559)
- **#8504** [开启，XXXL] *桌面审批通知* — 统一系统通知，用于审批请求、完成、后台任务，即使应用在后台。[PR](https://github.com/agentscope-ai/QwenPaw/pull/8504)
- **#8434** [开启，S] 模型页面上的*快速添加模型按钮* — 将跨 3 个屏幕的 5 次交互减少为单一操作。修复 #4036。[PR](https://github.com/agentscope-ai/QwenPaw/pull/8434)
- **#8236** [开启，L] *Ollama 上下文窗口使用 `num_ctx` 而非默认 128k* — 本地模型准确性修复。[PR](https://github.com/agentscope-ai/QwenPaw/pull/8236)

**预测：** 2.3.0 正式版可能随会话存储（#8532）、插件 CLI（#8554）以及累积的 beta bug 修复一起发布。Matrix E2EE 和桌面通知可能进入 2.4.0。

---

## 7. 用户反馈摘要

**痛点（逐字主题）：**

1. **插件依赖脆弱性**（#8265、#8553）：*“我的插件被这个卡了”*——用户遇到真实世界的发行版 backport 和 hub 依赖审计失败。共享依赖模型（`dependency_mode: shared`）造成级联激活失败。

2. **Windows 平台二等公民待遇**（#8488、#8414、#8502、#8547、#8528、#8552）：CRLF 规范化、中文路径、GBK 编码、PyInstaller 资源遗漏——今日报告或修复了 **6+ 个不同的 Windows 特定缺陷**。PR #8234（GBK/CP936 解码而非丢弃字节）解决了中文 Windows 用户长期存在的数据丢失问题。

3. **静默失败**（#8488、#8549、#8505）：Visual Compact 静默无操作；模型配置接受无效值后提供商消失；网关 502 变为 `unknown` 且无重试。用户希望**响亮、可操作的诊断**。

4. **第三方后端集成**（#8533、#8534）：Codex/Qoder cron 任务误判内容；阿里云 Token Plan 密钥与 TTS 不兼容。用户积极混用提供商并遇到接缝失败。

5. **频道并发**（#8511）：飞书用户在 agent 忙碌时发送消息会间歇性收到“Internal error”——被视为不稳定。

**满意度信号：** 高 issue 到 PR 的周转速度（多个当日修复）、首次贡献者参与（#8550）以及带源码级分析的详细 bug 报告（#8517、#8414、#8505）表明用户基础技术素养高且投入度高。**安全报告 #8513** 研究尤为深入，反映用户在负责任地披露而非抱怨。

---

## 8. 积压关注

**长期未答复/需要维护者关注：**

| 条目 | 年龄 | 重要性 |
|------|-----|----------------|
| **#8513** 安全：出站 shell 操作在 AUTO/SMART 下未门控 | 创建于 2026-10-30，1 条评论 | **无修复 PR；数据外泄风险。** 最高优先级。[Issue](https://github.com/agentscope-ai/QwenPaw/issues/8513) |
| **#8235 / #8433** 竞争的 CRLF `edit_file` 修复 | 均创建于 2026-10-14/26，审查中 | **重复工作**——维护者应合并以避免合并冲突和贡献者挫败感。[PR #8235](https://github.com/agentscope-ai/QwenPaw/pull/8235) · [PR #8433](https://github.com/agentscope-ai/QwenPaw/pull/8433) |
| **#8234** read/edit/grep 中的 GBK/CP936 解码 | 创建于 2026-10-14，审查中 | 中文 Windows 的数据丢失修复；已停滞 20 天。[PR](https://github.com/agentscope-ai/QwenPaw/pull/8234) |
| **#8236** Ollama `num_ctx` 上下文大小 | 创建于 2026-10-14，审查中 | 本地模型准确性；已停滞 20 天。[PR](https://github.com/agentscope-ai/QwenPaw/pull/8236) |
| **#4312** agent.json 热重载范围（MCP、Skills） | 创建于 2026-05-14，**今日关闭**，历经 6 个月 | 关闭良好，但表明积压深度——功能请求可能搁置数月。[Issue](https://github.com/agentscope-ai/QwenPaw/issues/4312) |
| **#8013** 技能池下载超时（12,994 个文件，80MB） | 创建于 2026-09-29，今日关闭 | 大型技能处理需要三层修复（前端 30 秒中止、后端复制、最终工作区落地）。[Issue](https://github.com/agentscope-ai/QwenPaw/issues/8013) |

**建议：** 维护者应 (1) 立即将 #8513 分配给具备安全能力的贡献者，(2) 选择一个 CRLF 修复（#8235 或 #8433）并关闭另一个并给予致谢，(3) 批量审查来自贡献者 `Li-john1021` 的三个停滞的 2026-10-14 PR（#8234、#8235、#8236）以保持参与度。

---

*摘要基于 2026-11-03 的 GitHub 活动数据生成。所有链接指向 `github.com/agentscope-ai/QwenPaw`。*

</details>

<details>
<summary><strong>ZeroClaw</strong> — <a href="https://github.com/zeroclaw-labs/zeroclaw">zeroclaw-labs/zeroclaw</a></summary>

# ZeroClaw 项目摘要 — 2026-11-03

## 1. 今日概览

ZeroClaw 今日开发活跃度极高：过去 24 小时更新了 50 个 issue（48 个开启/活跃，仅 2 个关闭）和 50 个 PR（40 个开启，10 个已合并/关闭），无新版本发布。项目显然处于 `v0.9.0` 前的加固阶段——多个 P1 标记项引用了 `release:v0.9.0`（配对强制、发布体积门控、Docker 源码构建），安全/身份访问相关工作主导了新缺陷流入。Issue 队列并未缩减——今日约 24:1 的开启/关闭比表明，相对于缺陷发现速度，分诊与修复吞吐量正成为日益增长的瓶颈。维护者注意力明显分散于网关安全（配对、OIDC、X-Forwarded-For 信任）、agent 回合生命周期以及 provider 上下文窗口正确性。整体项目健康度在贡献量上表现强劲，但在 issue 关闭和发布门控验证方面显现压力。

## 2. 版本发布

过去 24 小时无新版本发布。（issue 报告中已知的最新发布版本为 `zeroclaw v0.8.7` / web `v0.8.x`；`v0.9.0` 工作正在进行中。）

## 3. 项目进展

今日合并/关闭的 PR（共 10 个），值得注意的项目：

- **[PR #12208](https://github.com/zeroclaw-labs/zeroclaw/pull/12208)** — `fix(web): restore an in-flight turn after a reload`：持久化乐观提示词 + 部分答案/推理的节流 `localStorage` 快照，使聊天重新加载后不再丢失工作。直接补充网关 WS 生命周期 issue #7759。
- **[PR #12209](https://github.com/zeroclaw-labs/zeroclaw/pull/12209)** — `fix(web): require confirmation and neutral wording for stopped turns`：阻止意外的双击取消，并移除 `[interrupted by user]` 持久化噪音。
- **[PR #11054](https://github.com/zeroclaw-labs/zeroclaw/pull/11054)** — WhatsApp Web：主题分隔线（`---`、`***`、`___`）和 setext 标题现在正确渲染，不再作为字面标点泄漏（关闭 #11052）。
- **[PR #11739](https://github.com/zeroclaw-labs/zeroclaw/pull/11739)** — Telegram：回复完成时现在会撤回确认反应（修复了缺失的 `remove_reaction` 覆盖）。
- **[PR #11270](https://github.com/zeroclaw-labs/zeroclaw/pull/11270)** — CI：`zeroclaw-desktop` 测试现已在 macOS、Linux 和 Windows 上运行，填补了其他所有 workspace 任务均排除 desktop crate 的覆盖缺口。
- **[PR #12202](https://github.com/zeroclaw-labs/zeroclaw/pull/12202)** — 依赖升级（rust-all 组，35 项更新），同日被 **#12215** 取代。

**进行中的高价值工作：** PR [#11921](https://github.com/zeroclaw-labs/zeroclaw/pull/11921)（仅追加会话事件历史——架构性，XL）、[#11965](https://github.com/zeroclaw-labs/zeroclaw/pull/11965)（可选加入的 OIDC 仪表盘登录）、[#12213](https://github.com/zeroclaw-labs/zeroclaw/pull/12213)（强制执行 `[gateway.pairing_dashboard]`，修复 #9394）、[#11712](https://github.com/zeroclaw-labs/zeroclaw/pull/11712)（提取 `zeroclaw-a2a` crate）、[#11456](https://github.com/zeroclaw-labs/zeroclaw/pull/11456)（子进程内存看门狗）。

## 4. 社区热点

| 项目 | 活跃度 | 潜在需求 |
|---|---|---|
| [#7759](https://github.com/zeroclaw-labs/zeroclaw/issues/7759) — 将网关 WebSocket 生命周期与 agent 回合生命周期解耦 | 8 条评论，P1，进行中，风险:高 | 用户希望后台回合能在断连后存活——在不稳定/移动连接上长时间运行的 agent 工作的可靠性。 |
| [#10926](https://github.com/zeroclaw-labs/zeroclaw/issues/10926) — Matrix `send_via` 将 peer 用户身份视为房间目标 | 7 条评论，P2 | 跨通道寻址语义令人困惑；A2A/peer 消息传递需要精确的目标解析。 |
| [#12014](https://github.com/zeroclaw-labs/zeroclaw/issues/12014) — 自定义 OpenAI 兼容 provider 静默假定 32k 上下文 | 5 条评论，**S1** | 自带 provider 的用户遭遇有效回合被本地静默拒绝；需要明确、响亮的配置错误。 |
| [#9394](https://github.com/zeroclaw-labs/zeroclaw/issues/9394) — `pairing_dashboard` 被接受但未被读取；配对码永不过期 | 5 条评论，P1，`v0.9.0` | 安全期望：文档化的配置必须实际被强制执行；修复 PR **#12213** 已开启。 |
| [#11703](https://github.com/zeroclaw-labs/zeroclaw/issues/11703) — `tool_search` 激活破坏提示词缓存 | 4 条评论 | 对成本敏感且使用延迟 MCP 加载的用户；希望系统提示词稳定。 |

**模式：** 热门讨论串由*长时间运行回合的可靠性*、*自带模型配置正确性*以及*宣称但未强制执行的安全配置*主导。

## 5. 缺陷与稳定性

按严重程度排序；除注明外，均为今日新报告/刷新：

**S1 — 工作流受阻**
- [#12210](https://github.com/zeroclaw-labs/zeroclaw/issues/12210) — *今日新增。* 每个源码 Docker 镜像构建（`Dockerfile`、`.debian`、`.alpine`）在依赖预取阶段失败，因为未复制 `tests/fixtures/runtime-composition-consumer`。**发布门控风险（P1，`v0.9.0`）；尚无修复 PR。**
- [#12187](https://github.com/zeroclaw-labs/zeroclaw/issues/12187) — 通道缓存回合归一化将用户回合合并进 `role=tool` 信封，在严格 provider 上永久污染对话。**未识别到修复 PR。**
- [#12014](https://github.com/zeroclaw-labs/zeroclaw/issues/12014) — 32k 上下文窗口回退拒绝有效回合（见社区热点）。
- [#9859](https://github.com/zeroclaw-labs/zeroclaw/issues/9859) — Distroless 发布镜像缺少 `sh`，破坏 `runtime.shell` webhook（S1，自 8 月起持续）。
- [#12126](https://github.com/zeroclaw-labs/zeroclaw/issues/12126) — Windows UNC 附件路径可使守护进程向攻击者控制的 SMB/WebDAV 服务器认证（凭据泄露）。

**P1 / 高风险**
- [#12194](https://github.com/zeroclaw-labs/zeroclaw/issues/12194) — *今日新增。* 未知模型静默回退到 `UNCONFIGURED_CONTEXT_WINDOW_FALLBACK = 32_000`；与 #12014 同属根因家族——**修复可能应合并处理。**
- [#12203](https://github.com/zeroclaw-labs/zeroclaw/issues/12203) — `web_search_tool` 在无 `tools-external` 时被静默编译排除；未发出警告（#11221 的回归）。
- [#9394](https://github.com/zeroclaw-labs/zeroclaw/issues/9394) — 配对码永不过期（修复 PR #12213 已开启 ✅）。

**安全（身份/访问，S2 类）**
- [#12052](https://github.com/zeroclaw-labs/zeroclaw/issues/12052) — 限流器信任第一个 `X-Forwarded-For` 条目 → 在追加式代理后可伪造回环豁免。
- [#12050](https://github.com/zeroclaw-labs/zeroclaw/issues/12050) — OIDC 回调令牌页面无浏览器绑定 → 登录 CSRF。
- [#12053](https://github.com/zeroclaw-labs/zeroclaw/issues/12053) — `/api/docs` 在仪表盘源上加载未固定版本的第三方脚本（无 SRI）。
- [#12127](https://github.com/zeroclaw-labs/zeroclaw/issues/12127) — `git_operations` 在 Windows 上于授权前触碰 `gitdir:`/`commondir`/alternates 路径（UNC 凭据泄露向量）。

**S2/S3 降级行为**
- [#10926](https://github.com/zeroclaw-labs/zeroclaw/issues/10926)（Matrix）、[#10924](https://github.com/zeroclaw-labs/zeroclaw/issues/10924)（运行时命令回复进入语音路由）、[#12167](https://github.com/zeroclaw-labs/zeroclaw/issues/12167)（`send_message_to_peer` 忽略自主级别/操作预算——策略绕过）、[#12104](https://github.com/zeroclaw-labs/zeroclaw/issues/12104)（OpenAI Responses 丢弃工具结果图像）、[#12133](https://github.com/zeroclaw-labs/zeroclaw/issues/12133)（Windows 环境允许列表遗漏 `APPDATA`/`LOCALAPPDATA`）。
- [#9562](https://github.com/zeroclaw-labs/zeroclaw/issues/9562)（WebChat 自动滚动）、[#12186](https://github.com/zeroclaw-labs/zeroclaw/issues/12186)（`zerocode --agent` 被解析但未使用）、[#12087](https://github.com/zeroclaw-labs/zeroclaw/issues/12087)（工具开关双重保存）——S3 UX 问题。

**评估：** 安全集群（#12050/#12052/#12053/#12126/#12127）似乎出自同一报告者的单次审计系列，值得在 `v0.9.0` 前给出统一的安全发布响应。

## 6. 功能请求与路线图信号

- [#7759](https://github.com/zeroclaw-labs/zeroclaw/issues/7759) — 后台/可恢复网关回合（**P1，进行中**，web 侧支持已在 #12208/#12209 中合并）。**极有可能进入 v0.9.0。**
- [#10893](https://github.com/zeroclaw-labs/zeroclaw/issues/10893) — 通道消息的生成中途转向（状态:blocked；管道存在但无生产者）。#7759 的自然搭档。
- [#12183](https://github.com/zeroclaw-labs/zeroclaw/issues/12183) — 活跃 agent 的运行时提示词/命令拦截与注入——同一"飞行中可控性"主题。
- [#12181](https://github.com/zeroclaw-labs/zeroclaw/issues/12181) — Web 托管文档上传（`#9488` 第二阶段 Web 切片），默认关闭——可能是 OIDC 后的下一个小版本。
- [#12191](https://github.com/zeroclaw-labs/zeroclaw/issues/12191) — Qdrant 内存后端的混合密集+BM25 召回（与 SQLite 后端对齐）——一个合理的中期路线图项。
- [#12107](https://github.com/zeroclaw-labs/zeroclaw/issues/12107) — socket 重开时显示分离回合的答案——#11965 的后续，与 #7759 配对。

**对 v0.9.0 的预测：** 强制配对仪表盘（#12213）、OIDC 仪表盘登录（#11965）、网关回合解耦（#7759）以及发布/CI 修复（#12210、#11580、#11272）——即 v0.9.0 看起来是一个*安全 + 可靠性版本*，而非功能扩展版本。

## 7. 用户反馈摘要

- **配置信任是最响亮的抱怨。** 用户报告配置键"被接受但完全未被读取"（#9394）以及默认值静默假定错误值（#12014、#12194、#12203）——反复出现的关键词是*静默地*。未满足的期望：无效或降级的配置应在启动时响亮失败，而非在回合中途。
- **长时间运行的工作必须能在传输层存活。** 多个讨论串（#7759、#9562、#12107、#12208、#12209）追溯到用户将 web 聊天视为生产控制台，并被重新加载、断连或自动滚动抢走滚动位置所困扰——这是移动/不稳定网络上真实日常使用的明确迹象。
- **高级用户正在运行多 agent/多通道拓扑。** Matrix peer 路由（#10926）、`send_message_to_peer` 自主绕过（#12167）以及回合中途转向（#10893）来自运营 agent 组的用户，而非单一助手。
- **安全报告者正在进行仔细、逐行引用的审计**（#9394、#12050、#12052、#12126、#12127）——社区将 ZeroClaw 视为具有真实威胁模型（代理、OIDC、Windows UNC）的可部署守护进程，这是成熟的标志，但也表明积累了安全债务。
- **满意度信号：** web/聊天 UX 修复的快速周转（同日 PR #12208/#12209）表明维护者对用户最常提出的前端痛点反应迅速。

## 8. 积压关注

因年龄、严重程度或阻塞状态而需要维护者关注的项：

- **[#9859](https://github.com/zeroclaw-labs/zeroclaw/issues/9859)** — Distroless 镜像缺少 `sh`（S1，自 2026-08-09 开启，仅 2 条评论，1 个 👍）。长期存在的发布产物阻塞项，无关联修复。
- **[#11580](https://github.com/zeroclaw-labs/zeroclaw/issues/11580)** — x86_64 Linux 二进制约 0.7 MB，低于 64 MiB 上限；需要记录余量策略决策（`needs-maintainer-review`，`v0.9.0`）。这是门控决策，而非编码任务——适合本周维护者电话会议。
- **[#10893](https://github.com/zeroclaw-labs/zeroclaw/issues/10893)** — 转向管道存在但无生产者；**状态:blocked** 且无归属。值得一个设计决策或明确推迟。
- **[#9394](https://github.com/zeroclaw-labs/zeroclaw/issues/9394)** — 尽管修复 PR [#12213](https://github.com/zeroclaw-labs/zeroclaw/pull/12213) 已开启，该 issue 自 7 月以来一直悬而未决，标记 `release:v0.9.0`；审查容量是瓶颈。
- **[#11272](https://github.com/zeroclaw-labs/zeroclaw/pull/11272)** — Linux/Windows 桌面内核缺失仪表盘嵌入的发布修复，自 2026-09-30 开启且无评论；若未合并，已发布的桌面构建将明显损坏。
- **[#11456](https://github.com/zeroclaw-labs/zeroclaw/pull/11456)** — 子进程内存看门狗，`needs-maintainer-review`，自 10 月 2 日开启；对不可信 shell 安全有价值但停滞。
- **[#11052](https://github.com/zeroclaw-labs/zeroclaw/pull/11054)** — 今日关闭 ✅，仅作为解决说明列出。

---

**底线：** ZeroClaw 高度活跃，明显正收敛于一个安全与可靠性导向的 `v0.9.0`，但开启:关闭 issue 比（24:1）以及若干陈旧的 S1 项（#9859、#11272）表明，*审查/合并容量*——而非贡献量——才是项目当前的约束。

</details>

---
*本日报由 [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar) 自动生成。*