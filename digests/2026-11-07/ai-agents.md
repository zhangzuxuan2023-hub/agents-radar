# OpenClaw 生态日报 2026-11-07

> Issues: 377 | PRs: 500 | 覆盖项目: 5 个 | 生成时间: 2026-11-06 23:16 UTC

- [OpenClaw](https://github.com/openclaw/openclaw)
- [Hermes Agent](https://github.com/nousresearch/hermes-agent)
- [IronClaw](https://github.com/nearai/ironclaw)
- [QwenPaw](https://github.com/agentscope-ai/QwenPaw)
- [ZeroClaw](https://github.com/zeroclaw-labs/zeroclaw)

---

## OpenClaw 项目深度报告

# OpenClaw 项目摘要 — 2026-11-07

## 1. 今日概览

OpenClaw 仍处于高速推进但受稳定性制约的阶段：过去 24 小时内有 377 个 issue 和 500 个 PR 被更新，其中 217 个未关闭/活跃 issue、361 个未关闭 PR，而已合并/关闭 PR 为 139 个——这一合并与未关闭之比表明，瓶颈在于评审能力，而非贡献者供给。今日没有新版本发布，说明项目正处于两个发布列车之间（当前稳定线为 2026.10.10，2026.11.5-beta.1 处于预发布状态）。今日活动的主导主题是**回归与生命周期/资源管理缺陷**——网关事件循环阻塞、SQLite worker OOM、MCP 子进程泄漏、WAL checkpoint 饥饿以及提示缓存失效——其中多项为 P0/P1 并被标记为发布阻塞项。社区参与度是务实型的而非狂热型：困难 bug 的评论数很高（某个 P0 有 80 条评论），但整体反应数接近零，同时存在大量未解决的旧报告“陈旧”积压。

## 2. 发布

过去 24 小时无新版本发布。供跟踪版本的读者参考的背景信息：
- 各 issue 中引用的当前稳定版：**2026.10.10**
- 被提及仅在预发布中修复的问题：**2026.11.5-beta.1**（例如 #141474、#193962）
- 多个未关闭的回归被归因于特定已发布版本（2026.9.5、2026.9.6、2026.9.8、2026.10.5、2026.10.10）

## 3. 项目进展

未提供已合并 PR 列表，但今日关闭的 PR（共 139 个已合并/关闭）包含若干值得注意的修复：

- **[PR #194341](https://github.com/openclaw/openclaw/pull/194341)（已关闭）**——对符合条件的 Access 程序遵循受信任的管理员权限（安全边界标签）。
- **[PR #194332](https://github.com/openclaw/openclaw/pull/194332)（已关闭）**——修复 Control UI 在长回答后输入回溯缺失提示的问题（修复 #194327）。
- **[PR #193450](https://github.com/openclaw/openclaw/pull/193450)（已关闭）**——iOS Files 现在显示准确的读取错误，而非错误的“二进制/超大”消息（修复 #193347）。
- **[PR #194312](https://github.com/openclaw/openclaw/pull/194312)（已关闭）**——在 UI 中隐藏已暂停的固定会话，直到唤醒。
- 已关闭且关联修复 PR 的 issue：**#166017**（MCP `sessionIdleTtlMs` 默认值未生效——spawn broker 上限 256）、**#178291**（2026.10.4→10.5 激活后 Doctor 失败）、**#164923**（短期记忆提升 0/512 不对称）、**#168794**（Codex relay 32 MiB 图像限制 → 502）。

**未关闭 PR 中可见的在途功能工作：** Prometheus provider 使用窗口指标（[#141276](https://github.com/openclaw/openclaw/pull/141276)）、plugin-SDK 访问策略修订发布（[#182383](https://github.com/openclaw/openclaw/pull/182383)）、从 URL 导入技能包（[#192781](https://github.com/openclaw/openclaw/pull/192781)），以及会话归档读取缓存字节限制（[#193343](https://github.com/openclaw/openclaw/pull/193343)）。

## 4. 社区热点话题

按评论活动排序：

1. **[#160959](https://github.com/openclaw/openclaw/issues/160959) — P0，80 条评论** — 网关在捕获大型外部插件时阻塞数分钟（2026.9.6 回归）。最热门的单一帖子；一个已确认的发布阻塞级事件循环停滞，与插件捕获相关（#144252）。高度紧急，需要维护者评审。
2. **[#164294](https://github.com/openclaw/openclaw/issues/164294) — 39 条评论** — SQLite WAL 自动 checkpoint 永不完成，被一个进程内读取器钉住（17,091 个日志帧 vs 866 个已 checkpoint）。典型的读取器饥饿病理。
3. **[#177656](https://github.com/openclaw/openclaw/issues/177656) — P1，29 条评论** — 原生宿主 exec 在 2026.10.5 上返回共享状态“完整性准入”错误（≥28 次失败）。
4. **[#150635](https://github.com/openclaw/openclaw/issues/150635) — 25 条评论** — 记忆 dreaming 深度阶段永不提升：短期回忆保留每晚驱逐已回忆条目。记忆系统正确性问题，diamond-lobster 评级。
5. **[#191944](https://github.com/openclaw/openclaw/issues/191944) — 23 条评论** — macOS `gateway stop` / `stop --force` 均失败，报错“Process ancestry could not be fully inspected.”

**底层需求：** 社区高度关注**网关作为长期运行守护进程的运维可靠性**——资源遏制（插件、MCP 子进程、SQLite 读取器）和干净的生命周期命令。记忆系统行为（dreaming/提升/保留）是第二集群，表明记忆是用户在生产中重点压力测试的招牌差异化功能。

## 5. Bug 与稳定性

按严重程度排序，附修复 PR 状态：

**P0**
- **[#160959](https://github.com/openclaw/openclaw/issues/160959)** — 网关事件循环在大型插件捕获时阻塞数分钟（2026.9.6 回归）。无关联修复 PR。**发布阻塞项。**
- **[#178291](https://github.com/openclaw/openclaw/issues/178291)** — 今日关闭：2026.10.4→10.5 聊天更新在激活后 Doctor 失败（离线维护权限）。更新路径阻塞项，已解决。

**P1（回归 / 崩溃循环 / 消息丢失）**
- **[#191847](https://github.com/openclaw/openclaw/issues/191847)** — 2026.10.10：大会话反复使 512 MB SQLite worker OOM（`ERR_WORKER_OUT_OF_MEMORY`）；每一轮都失败并报“admission is closed.”
- **[#192959](https://github.com/openclaw/openclaw/issues/192959)** — Kubernetes pod 重启后网关拒绝启动：fsGroup 重新挂载后客户端凭据目录被拒绝。K8s 用户的部署阻塞项。
- **[#166641](https://github.com/openclaw/openclaw/issues/166641)** — `web_fetch` 冷回退在网关主线程上同步捕获无关插件，导致存活停滞（已附 V8 profile）。
- **[#185656](https://github.com/openclaw/openclaw/issues/185656)** — 大型 zstd transcript 归档在媒体持久化迁移中仍超出 Node 字符串限制（#170847 的回归），阻塞更新。
- **[#157182](https://github.com/openclaw/openclaw/issues/157182)** — 当环境主会话不可见时，Control UI 将用户落在 `/chat/main` 并无限重试（安全标签）。
- **[#162087](https://github.com/openclaw/openclaw/issues/162087)** — 9.7 之前账户模式作业的定时公告失败（缺少 ownerOrigin + direct-key 解析缺口）。
- **[#126950](https://github.com/openclaw/openclaw/issues/126950)** — Telegram 不透明回调载荷（`tgcb1:`）对非插件调用方被静默丢弃（消息丢失）。
- **[#155563](https://github.com/openclaw/openclaw/issues/155563)** — 成功 agent 轮次后服务子进程 relay/anchor 和 MCP 组被保留（2026.9.5 回归）。
- **[#193962](https://github.com/openclaw/openclaw/issues/193962)** — header-probe 超时修复（#187197）从 2026.10.10 无法触达，因为包更新运行已安装更新器的 10s 探测。**修复存在但无法交付——发布通道死锁。**
- **[#182647](https://github.com/openclaw/openclaw/issues/182647)** — 云 worker 轮次在推理前失败：“Session capability metadata requires worker preparation.”

**P2 重点（选摘）**
- **[#182891](https://github.com/openclaw/openclaw/issues/182891)** — 提示缓存永不重用；每个请求都 `cache read dropped N -> 0`（成本影响）。与 **[#143017](https://github.com/openclaw/openclaw/issues/143017)** 相关（从前缀重写测得 21.3M 缓存写入 token）。
- **[#112638](https://github.com/openclaw/openclaw/issues/112638)** — `session.maintenance` enforce 模式不限制存储（线程/频道条目豁免回收）。
- **[#166964](https://github.com/openclaw/openclaw/issues/166964)** — 请求者权限撤销时子 agent 完成交付永久失败，无原因日志。
- **[#123792](https://github.com/openclaw/openclaw/issues/123792)** — 使用 CLI 后端时助手轮次渲染两次（实时逐块 + 聚合记录）。
- **[#124306](https://github.com/openclaw/openclaw/issues/124306)** — 执行审批卡片标记语法风险，但从不标记目标路径是挂载点（数据丢失面）。

**稳定性元观察：** 今日反复出现的根因类别是 (a) 网关上的同步主线程工作，(b) SQLite 读取器/worker 资源限制，以及 (c) 子进程生命周期泄漏。这些是架构性的而非一次性的，应指导下一版本的加固主题。

## 6. 功能请求与路线图信号

- **[#13219](https://github.com/openclaw/openclaw/issues/13219) — 按模型使用日志用于成本跟踪**（10 条评论，👍1，自 2026 年 2 月开放）。强候选：与提示缓存 bug 中的成本可见性痛点自然配对。
- **[#163739](https://github.com/openclaw/openclaw/issues/163739) — 使 Ctrl+Shift+A 可配置**（浏览器快捷键冲突）。小、低风险，可能很快落地。
- **[#166678](https://github.com/openclaw/openclaw/issues/166678) — 从选择器隐藏 agent 但保持可 spawn。** 直接服务多 agent 操作者；可能是下一周期 UX 工作。
- **[#158800](https://github.com/openclaw/openclaw/issues/158800) — `/new` 后浏览/fork 先前频道聊天片段。** 与未关闭 UI PR 中的会话历史方向一致。
- **[#136271](https://github.com/openclaw/openclaw/issues/136271) — msteams：为频道上传动态解析团队 SharePoint 站点。**
- **[#164620](https://github.com/openclaw/openclaw/issues/164620) — 禁用托管插件目录源的设置**（隐私/气隙需求；URL 当前硬编码）。
- **[#79223](https://github.com/openclaw/openclaw/issues/79223) — 可配置 Dream Diary 语言/提示**（`memory-core` 中硬编码英文提示）。
- **[#103067](https://github.com/openclaw/openclaw/issues/103067) — 集中聊天会话命名；定义子 agent 会话生命周期。** 架构性；将解决一系列命名/持久化投诉。
- **[#178373](https://github.com/openclaw/openclaw/issues/178373) — 轮次级 turn/token 预算；默认开启工具循环检测；停止对保留历史重复计费。** 这可以说是板上最具战略重要性的请求——它是 #143017/#182891 的成本控制对应物。

**对下一版本的预测：** 近期补丁将以回归修复为主（MCP TTL、更新路径探测、SQLite worker 内存）而非功能。功能方面，预计 Prometheus 使用窗口导出器（#141276）和小型 UI 人体工学修复（#163739、#194332 相邻工作）会先于 #178373 或会话模型重构（#103067）等较大项目发布。

## 7. 用户反馈摘要

**真实用户表达的痛点：**
- **升级可靠性是头号挫败。** 多份报告描述升级在激活后失败（#178291）、在迁移上阻塞（#185656），甚至无法触达自身修复（#193962）。生产安装上的用户实际上被钉住。
- **资源泄漏侵蚀对长期运行网关的信任。** 评论者自行量化损害：每个 stdio MCP 子进程约 144–145 MB（#164690）、`claude-cli` 下重复 MCP spawn（#166017、#164690）、上限 256 的 broker 耗尽（#166017）。这是一个成熟的生产操作者受众。
- **记忆系统行为令人困惑且有损。** 用户报告 `memory promote` 在一个工作区返回零候选，而兄弟工作区提升 50–90%（#164923），以及夜间 dreaming 永不提升（#150635）——旗舰“dreaming”功能对部分用户未交付可见价值。
- **静默失败和不透明错误**反复成为主题：Telegram 回调被丢弃（#126950）、跨 16 个调用点的通用“changed while starting work”无原因码（#127195）、无标签的 Code Mode 内部错误（#167609），以及无日志原因的撤销（#166964）。用户在做产品本应做的诊断工作。
- **成本焦虑是切实的**：用户手工测量缓存写入 token（#143017）并恳求按模型使用日志（#13219）。
- **移动端/UI 摩擦**（手机小输入框 #139909、聊天重载回溯 PR #193217、重复渲染 #123792）是稳定的次要流。

**满意度信号：** 困难 bug 的评论数很高而反应数接近零——用户投入且勤勉，但并非喝彩。人们显然在严肃的生产配置中运行 OpenClaw（Kubernetes、15 个 agent 的多 agent 组织、Docker 沙箱），这既是对项目能力的赞美，也是其稳定性债务的来源。

## 8. 积压关注

长期存在、高价值、仍开放且需要维护者关注的条目：

- **[#13219](https://github.com/openclaw/openclaw/issues/13219)** — 按模型使用日志；自 **2026-02-10** 开放。最便宜的高影响成本功能。
- **[#79223](https://github.com/openclaw/openclaw/issues/79223)** — 可配置 Dream Diary 语言；自 **2026-05-08** 开放。简单修复（移动硬编码提示），持续的国际用户摩擦。
- **[#103067](https://github.com/openclaw/openclaw/issues/103067)** — 集中会话命名 / 子 agent 生命周期；自 **2026-07-09** 开放，需要产品决策。持续产生较小 bug 的架构债务。
- **[#112638](https://github.com/openclaw/openclaw/issues/112638)** — 会话维护不限制存储；自 **2026-07-22** 开放。多租户网关的静默无界增长。
- **[#127195](https://github.com/openclaw/openclaw/issues/127195)** — 十六个相同错误点无原因码（自 **2026-08-21**，修复形态清晰）。一个“可诊断性”快速胜利。
- **[#143017](https://github.com/openclaw/openclaw/issues/143017)** — 提示缓存前缀重写，测得 21.3M 缓存写入 token；自 **2026-09-09** 开放，附完整实时复现。直接烧用户的钱。
- **[#123792](https://github.com/openclaw/openclaw/issues/123792)** — 使用 CLI 后端时助手轮次重复渲染；自 **2026-08-14** 开放。
- **[#141474](https://github.com/openclaw/openclaw/issues/141474)** — `sessions_yield` 使 `agents_wait` 永久搁浅；剩余恢复仅在 `v2026.11.5-beta.1` 中发布。维护者行动：确保其进入**稳定**版本。

**值得注意的陈旧但重要：** #160959（P0）正在被正确处理，但 #112638 和 #13219 已搁置数月并带有产品决策标签——这些是最可能成为下一次维护者分诊清扫的候选。

---

*数据来源：github.com/openclaw/openclaw，issues/PRs 更新于 2026-11-06，摘要生成于 2026-11-07。排名基于所提供的评论数；已关闭条目在能解释今日修复之处予以保留。*

---

## 横向生态对比

# 跨项目对比报告 — 个人 AI 助手 / Agent 开源生态
**快照日期：2026-11-07 | 项目：OpenClaw、Hermes Agent、IronClaw、QwenPaw、ZeroClaw**

---

## 1. 生态概览

Agent 框架生态正处于**功能已交付、加固尚未完成的阶段**：四个活跃项目都已发布其标志性能力（多 Agent 编排、记忆/梦境、插件、渠道网关），如今正在偿还由此产生的稳定性债务。活动以回归和生命周期 Bug 为主，而非新功能；安全/权限模型正成为新的战场（ZeroClaw），而长期运行网关的运维可靠性则是普遍痛点。用户群体已成熟为生产环境运维者——他们运行 Kubernetes、多 Agent 组织和加固的 Docker 部署，其反馈是量化的、诊断性的，而非愿景式的。生态健康度呈两极分化：各项目的贡献者供给都很充足，但**维护者的评审能力才是真正的约束**，体现为远低于 1.0 的合并/开启比，以及积压数月、因决策受阻的 PR。本窗口期内没有任何项目发布稳定版本，表明整个生态集体处于发布列车之间的间隙。

---

## 2. 活动对比

| 项目 | Issue（开启 / 24h 内活跃总数） | PR（开启 / 24h 内合并-关闭） | 发布状态 | 健康评分* | 备注 |
|---|---|---|---|---|---|
| **OpenClaw** | 217 开启 / 377 有更新 | 361 开启 / 139 合并-关闭 | 稳定版 2026.10.10；2026.11.5-beta.1 处于预发布；**今日无发布** | **B–** | 绝对量最高；评审瓶颈严重（合并比 0.39）；多个 P0 发布阻塞项 |
| **Hermes Agent** | 49 开启 / 50 有更新 | 45 开启 / 5 合并-关闭 | 正在消化 v0.22.3–v0.23；**今日无发布** | **C+** | 吞吐严重失衡（合并比 0.11）；1 个关闭 vs 49 个开启；P0 级 Windows 数据丢失风险 |
| **IronClaw** | 0 | 0 | 无 | **N/A** | 24h 内无活动——休眠或处于周期之间 |
| **QwenPaw** | 约 18 开启 / 22 有更新 | 18 开启 / 20 合并-关闭 | 2.3.0b2 控制台 / 2.4 后端元数据；**今日无发布** | **A–** | 吞吐最为平衡（约 1:1）；多个长期积压 Issue 终于关闭 |
| **ZeroClaw** | 13 开启 / 25 有更新 | 37 开启 / 13 合并-关闭 | v0.9.0 发布闸门进行中；**今日无发布** | **B** | 安全响应能力强；发布闸门 PR（如 #9746）停滞 3 个月 |

*\*健康评分是对吞吐平衡、开启阻塞项严重程度、发布节奏和维护者响应能力的综合判断，并非正式指标。*

**核心结论：** QwenPaw 是唯一一个输入与产出工作接近持平的项目。OpenClaw 和 Hermes 都表现出评审能力饥饿；ZeroClaw 被单个大型安全 PR 卡住；IronClaw 无声无息。

---

## 3. OpenClaw 的定位

**相对同行的优势：**
- **社区规模：** 24h 内 377 个 Issue + 500 个 PR 有更新，约为 **Hermes 或 QwenPaw 活动量的 7–10 倍**，约 15 倍于 ZeroClaw。OpenClaw 拥有最深厚的贡献者池和最成熟的生产运维者（K8s 部署、15 Agent 组织、Docker 沙箱）。
- **最广的覆盖面：** 记忆/梦境系统、MCP 集成、插件 SDK、多渠道（Telegram、msteams、iOS）、云 worker、Code Mode 以及 Control UI——无同行能匹敌这一广度。
- **参考实现地位：** 被定位为"核心参考"实现；同行隐含地以其为基准。

**技术路线差异：**
- OpenClaw 以**长期运行的 Gateway 守护进程**为架构中心——这既是其力量来源（持久会话、定时任务、渠道汇聚），也是其稳定性债务来源（事件循环阻塞、SQLite worker OOM、MCP 子进程泄漏）。QwenPaw 等同行更偏向请求/会话导向，运行时足迹更轻。
- 更重地依赖 **SQLite + worker 进程**和**提示缓存**来控制成本——二者当前都处于病态（缓存失效 #182891、worker OOM #191847），这是 Hermes/ZeroClaw 在同规模下不具备的成本与可靠性风险敞口。
- 成熟的**插件 SDK + 托管目录**（附有开放请求 #164620，要求为气隙环境禁用目录源）——这是同行仍在建设中的治理面。

**社区规模对比：** OpenClaw 单个硬 Bug 的评论活动（#160959 上有 80 条评论）超过 QwenPaw 或 ZeroClaw 的*全天互动总量*。Hermes 最热帖子（#97681，40 条评论）只有 OpenClaw 最热帖子的一半。OpenClaw 是生态的引力中心；风险在于其评审能力未能随社区同步扩展。

---

## 4. 共同技术焦点领域

以下需求在**三个或更多**项目中反复出现，表明这是生态级需求：

| 需求 | OpenClaw | Hermes | QwenPaw | ZeroClaw | 具体表现 |
|---|---|---|---|---|---|
| **会话/状态持久化与同步** | SQLite worker OOM（#191847）、WAL 饥饿（#164294） | state.db 损坏（#147938）、群聊同步（#143303/#143302）、会话 DB 后端接缝（#150332） | Windows `recall_history` 断裂（#8585）、聊天状态缓存（#8596） | 仅追加事件历史（#10526）、持久对等回合（#9597）、空闲 RPC 泄漏（#11962） | *普遍性：* 四个项目都在将会话状态重建为一等、持久、跨表面的关注点 |
| **权限 / 审批治理** | Exec 审批缺口（#124306）、Access 程序权限（#194341） | 审批闸门绕过（#150329）、桌面审批弹窗（#84189）、写入范围（#142933） | Provider 配置校验（#8549） | S0/S1 所有权绕过（#11127、#11123、#12046、#12260）、cron 审批（#11149） | *普遍但 ZeroClaw 最突出：* 用户希望对 Agent 的行为和写入拥有可见、可强制执行的管控 |
| **静默失败 → 可诊断性** | 16 处相同错误点（#127195）、Telegram 回调丢失（#126950） | 静默丢弃（#148831）、"未安装"误报（#130072） | 接受空 api_key（#8276）、解码丢字节（#8234） | SOP 从未加载（#9779）、快速启动误报（#12155） | *普遍性：* 每个项目的用户都在做本应由产品完成的诊断工作 |
| **资源遏制 / 生命周期** | MCP 子进程泄漏（#164690、#166017）、插件捕获停滞（#160959） | 无界提案队列（#75130）、委托上下文下限（#108891） | Matrix 多工作区启动（#8538） | 空闲 RPC 会话永不释放（#11962）、渠道启动竞态（#12279） | *普遍性：* 长期运行的守护进程都会泄漏；无同行解决了生命周期卫生问题 |
| **成本可见性 / 缓存效率** | 提示缓存失效（#182891、#143017）、按模型用量日志（#13219） | — | — | 提示缓存顺序 Bug（#12176） | *OpenClaw 与 ZeroClaw 特有：* 大量使用提示缓存之处正是缓存驱动的成本焦虑所在 |
| **升级/更新可靠性** | 更新路径探测死锁（#193962）、迁移阻塞（#185656） | Windows 更新器版本陈旧（#143656）、陈旧快照恢复（#147938） | 2.2/2.3/2.4 之间的版本偏斜（#8580） | — | *OpenClaw/Hermes/QwenPaw 显著：* 更新器是信任链中最薄弱的一环 |

**综合：** 生态已收敛到**四个共同痛点簇**——持久会话状态、可强制执行的权限、可诊断的失败、生命周期资源卫生。这些是架构性的，而非一次性的，代表着下一代框架工作。

---

## 5. 差异化分析

| 维度 | OpenClaw | Hermes Agent | QwenPaw | ZeroClaw |
|---|---|---|---|---|
| **功能焦点** | 最广的平台：记忆/梦境、MCP、插件、多渠道、云 worker | 跨网关多 Agent 协作（#97681）、桌面/群聊、插件目录 | 渠道插件（Matrix/IM）、记忆检索/重排、Creator UI | 安全优先：主体/权限模型、SOP、cron 审批、Rust/WASM UI |
| **目标用户** | 大规模生产运维者（K8s、多 Agent 组织） | 多表面团队（桌面 + Webapp/PWA + 移动端）、Bot 运维者 | 企业 IM 用户（飞书/钉钉/Matrix）、中文 Windows 用户、离线/气隙环境 | 安全意识强的部署、代理/网关运维者 |
| **技术架构** | 长期运行 Gateway 守护进程；SQLite + worker；重提示缓存；插件 SDK | Gateway + 桌面/Webapp 表面；会话 DB 后端接缝正走向 PostgreSQL（#150332） | 更轻的请求/会话运行时；渠道插件模型；动态 MCP | 基于 Rust；事件溯源会话历史方向（#10526）；严格权限执行；Windows/Unix |
| **发布理念** | Beta 列车与稳定版并行；快速打补丁 | 快速扩展次要表面（v0.22→v0.23），回归负载高 | 控制台 beta 线 + 后端元数据；接近 1:1 的分诊 | 安全闸门式切割（v0.9.0）被大型 XL PR 阻塞 |
| **生态模型** | 托管插件目录（已有退出请求） | 社区插件目录（大量 PR） | 插件包 + Creator | 插件注册源（#12216）、多模型配置（#9809） |

**总结：** OpenClaw 是**平台型打法**（广度 + 规模），Hermes 是**协作/表面型打法**（多网关、多表面），QwenPaw 是**集成/本地化型打法**（渠道、Windows、离线），ZeroClaw 是**安全/架构型打法**（Rust、权限、事件溯源）。它们更多是互补而非直接竞争——但都在争夺同一个"个人 Agent 运行时"的位置。

---

## 6. 社区动能与成熟度

**活跃层级（按 24h 内有更新的条目）：**
- **第一梯队 — 超活跃：** OpenClaw（约 877 项）。动能最高，评审债务也最高。
- **第二梯队 — 活跃：** Hermes（约 100）、QwenPaw（约 60）、ZeroClaw（约 75）。量健康且可控。
- **第三梯队 — 休眠：** IronClaw（0）。

**快速迭代中：**
- **Hermes** — 表面扩展最快（桌面、PWA、移动端、群聊），但为此付出回归和 0.11 合并比的代价。迭代速度快于其稳定速度。
- **QwenPaw** — 既在迭代*也在*关闭；分诊节奏最佳；多位首次贡献者的修复成功落地。

**稳定 / 加固中：**
- **OpenClaw** — 处于发布列车之间，正在吸收回归；下次发布将以补丁为主（MCP TTL、更新探测、SQLite 内存）。
- **ZeroClaw** — 被 v0.9.0 安全切割的发布闸门卡住；同日关闭了许多 S0/S1 发现项，表明其在*安全响应*上已成熟，尽管授权模型本身尚不成熟。

**成熟度信号：**
- **成熟度最高：** OpenClaw（规模、运维者最广，但评审紧张）和 QwenPaw（吞吐平衡）。
- **成长阵痛：** Hermes（评审瓶颈、Windows 数据完整性风险）。
- **安全成熟最快：** ZeroClaw（对抗性审计被接受并快速关闭——这是分诊健康的标志，而非模型出厂即有缺口）。
- **未知：** IronClaw（信号不足）。

---

## 7. 趋势信号 — 对 AI Agent 开发者的价值

从跨项目社区反馈中提炼，按战略重要性排序：

1. **运行时可靠性就是新功能。** 每个项目的头号 Bug 都是守护进程级的：事件循环阻塞、SQLite worker OOM、子进程泄漏、空闲会话泄漏、启动竞态。**开发者应将生命周期资源管理视为一等设计关注点**，而非事后补丁。预计框架级方案（监督器、有界 worker、背压）将成为赢家的差异化因素。

2. **权限与审批治理是下一个竞争面。** ZeroClaw 的 S0 所有权绕过、Hermes 的审批闸门绕过和桌面审批请求、OpenClaw 的 exec 审批缺口都指向同一方向：用户希望对 Agent 的行为和记忆/技能写入拥有**可见、可强制执行、基于 diff 的管控**。在 UI（而非仅 CLI）中呈现审批、并在写入路径强制执行的产品将赢得信任。

3. **会话状态正被重构为持久且可重放。** OpenClaw（SQLite worker/WAL）、Hermes（state.db + PostgreSQL 接缝 #150332）、ZeroClaw（事件溯源历史 #10526、持久对等回合 #9597）和 QwenPaw（聊天状态缓存）都在向**事件溯源或仅追加、跨表面会话存储**收敛。这是本窗口期最大的单一架构趋势。

4. **成本可见性与缓存正确性是可变现的痛点。** OpenClaw 用户手动测量出 2130 万缓存写入 token（#143017），并恳求按模型用量日志（#13219）；ZeroClaw 修复了提示缓存顺序 Bug（#12176）。**提示缓存纪律与用量计量现在已是任何生产框架的入场门槛。**

5. **静默失败是信任的头号杀手。** 在所有项目中，被提及最多的挫败不是崩溃，而是*静默*丢弃、误报和无原因错误（OpenClaw #127195，Hermes #148831/#130072，QwenPaw #8276/#8234，ZeroClaw #9779/#12155）。**带可操作原因码的快速失败校验是廉价、高影响的差异化因素。**

6. **升级路径是最薄弱的一环。** 更新路径死锁（OpenClaw #193962）、迁移阻塞（#185656）、陈旧快照恢复（Hermes #147938）、版本偏斜（QwenPaw 2.2/2.3/2.4）。**可靠、可回滚、可自校验的更新器是被忽视的需求**——对工具供应商而言是一个可行的投资领域。

7. **多表面与跨机器协作是需求前沿。** Hermes 的 #97681（40 条评论，待决策）以及桌面/群聊回归簇；OpenClaw 的多 Agent 运维者请求（#166678 从选择器隐藏、#103067 子 Agent 生命周期）。**用户正从单 Agent 转向协调的多 Agent、多表面部署**，速度快于框架的会话/权限模型所能支撑的程度。

8. **本地化与离线/气隙运行是真实市场。** QwenPaw 的离线安装（#1453）、Windows/GBK 问题（#8234、#8585），以及 OpenClaw 的禁用托管目录请求（#164620）表明来自**受监管、气隙和非英语生产环境**的需求——这是一个被忽视的细分市场。

**对开发者的底线：** 生态的重心已从*Agent 能做什么*转向*能否被信任持续做下去*——持久地、安全地、可见地、可负担地。解决会话持久性、权限执行、可诊断性和更新可靠性的框架将定义下一个发布周期；功能广度已被商品化。

---

*本报告依据 2026-11-07 的 OpenClaw、Hermes Agent、IronClaw、QwenPaw 和 ZeroClaw 摘要汇编。活动计数为所报告的 24 小时快照；健康评分为分析师综合判断。*

---

## 同赛道项目详细报告

<details>
<summary><strong>Hermes Agent</strong> — <a href="https://github.com/nousresearch/hermes-agent">nousresearch/hermes-agent</a></summary>

# Hermes Agent — 项目摘要
**日期：2026-11-07**

---

## 1. 今日概览

Hermes Agent 仍处于高速迭代但问题密集的阶段：过去 24 小时内有 50 个 issue 和 50 个 PR 更新，其中 49 个 issue 仍处于开启状态，仅 1 个关闭；45 个 PR 处于开启状态，5 个已合并/关闭。今日无新版本发布，项目仍在消化 v0.22.3–v0.23 版本面（Docker gateway、Desktop、Webapp/PWA、移动端 beta），大多数回归问题集中于此。活动主要由长期协作基础设施工作（#97681）和源源不断的社区插件目录提交所主导，这表明生态健康，但维护者审查存在瓶颈。整体健康度可概括为**活跃且持续扩张，但审查债务偏高，且会话/状态处理中存在若干 P0/P2 正确性问题**，值得在下个版本发布前予以关注。

---

## 2. 发布

**无。** 过去 24 小时内未发布新版本。（issue 中引用的最新版本：v0.22.3、v0.22.5 以及 0.23 Webapp。）

---

## 3. 项目进展

今日合并/关闭活动较少（5 个 PR 合并/关闭，1 个 issue 关闭）：

- **#143656 [已关闭]** — Linux 更新器在安装标记之前重启的 bug（gateway 在 `/health` 中锁定过期版本）已关闭。[Issue #143656](https://github.com/NousResearch/hermes-agent/issues/143656)
- **#150348 [已关闭]** — `fix(agent): compact bulky history in short conversations` 已关闭（未按原样合并）；该 PR 解决的是受保护头部偏好 + 最小消息门控阻止压缩时产生的上下文窗口压力。[PR #150348](https://github.com/NousResearch/hermes-agent/pull/150348)

**推进中的工作（今日更新的开启 PR）：**
- **#150332** — 会话数据库后端接缝 / 可移植 SQL 基础工作（PostgreSQL 状态后端第一阶段，RFC #23717）。[PR #150332](https://github.com/NousResearch/hermes-agent/pull/150332)
- **#143334** — `fix(profiles)`：重命名后的 profile 保留本地聊天（修复 `hermes profile rename` 后的 `profile_mismatch`）。[PR #143334](https://github.com/NousResearch/hermes-agent/pull/143334)
- **#149467** — gateway 迁移修复，使 Desktop Group Chats 在升级到 `state.db` 时不会消失。[PR #149467](https://github.com/NousResearch/hermes-agent/pull/149467)
- **#149656** — Desktop SDK 会话磁贴停靠 / 插件只读面板布局（支持平铺式会话管理器）。[PR #149656](https://github.com/NousResearch/hermes-agent/pull/149656)
- **#150333** — `fix(agent)`：为因长度停止的尝试触发 `post_api_request`。[PR #150333](https://github.com/NousResearch/hermes-agent/pull/150333)

PR 数量中有相当一部分是**插件目录新增**（#149624、#150351、#150346、#150347、#150164、#150350、#150349、#150344、#150343、#149657）——这属于生态增长，而非核心变更。

---

## 4. 社区热点

| 条目 | 信号 | 潜在需求 |
|---|---|---|
| **[#97681](https://github.com/NousResearch/hermes-agent/issues/97681)** — 让 Bot 跨 gateway 协作（40 条评论，4 👍，`needs-decision`） | 讨论量遥遥领先 | 用户希望**在不放弃 bot 控制权的前提下实现跨机器/跨所有者的多 agent 协作**；这是基础架构层面的辩论（会话、消息投递、gateway 状态）。设计决策待定。 |
| **[#75130](https://github.com/NousResearch/hermes-agent/issues/75130)** — 技能提案队列无限增长，21% 已失效（7 条评论） | 长期存在的 bug | `skills.write_approval` 工作流产生**无限增长的待处理队列且会自我失效**；用户需要提案的生命周期管理/GC。 |
| **[#84189](https://github.com/NousResearch/hermes-agent/issues/84189)** — Desktop 内存/技能写入的内联批准并编辑弹窗（4 👍，`needs-decision`） | 反应与评论比高 | 审批工作流**目前仅限 CLI**；Desktop 用户希望有可见的 diff/批准/编辑界面。 |
| **[#147938](https://github.com/NousResearch/hermes-agent/issues/147938)** — Windows state.db“损坏”实为活跃锁；恢复了过期快照（P0） | 严重性驱动关注 | Windows 更新中的数据完整性信任问题。 |

**分析：** 社区的重心正从单 agent 功能转向**协调（跨 gateway bot、群聊）与治理（审批界面、技能/内存写入审查）**。4 个反应的 #84189 和 40 条评论的 #97681 都指向同一主题：*用户希望对自己的 agent 做了什么、写了什么拥有控制权和可见性。*

---

## 5. Bug 与稳定性

按严重性排序（存在修复 PR 的已注明）：

**P0 — 严重**
- **[#147938](https://github.com/NousResearch/hermes-agent/issues/147938)**（Windows）：`state.db` 校验将**活跃锁误判为损坏**，用数月前的过期快照覆盖健康数据，并将恢复误报为失败。*存在数据丢失风险。* 今日集合中无关联修复 PR。

**P2 — 高**
- **[#142982](https://github.com/NousResearch/hermes-agent/issues/142982)** — Desktop 无法取消固定旧版 cron 运行聊天；固定操作静默返回。[未列出修复 PR]
- **[#147626](https://github.com/NousResearch/hermes-agent/issues/147626)** — 已附加客户端能看到回合流，但**始终看不到发起用户提示**（多界面同步）。[未列出修复 PR]
- **[#143303](https://github.com/NousResearch/hermes-agent/issues/143303)** — 群聊 Stop 报告回合已停止但实际仍在运行 → 回合重叠、回复丢失。[未列出修复 PR]
- **[#143242](https://github.com/NousResearch/hermes-agent/issues/143242)** / **[#143241](https://github.com/NousResearch/hermes-agent/issues/143241)** — 重试时重复创建 gateway Group Chat 房间 / 重复点击“Start gateway group”。[未列出修复 PR]
- **[#143302](https://github.com/NousResearch/hermes-agent/issues/143302)** — 在先前讨论结束时发送的群聊消息**永远得不到回合**（消息被丢弃）。
- **[#108891](https://github.com/NousResearch/hermes-agent/issues/108891)** — Delegate 子 agent 上下文被限制在 20,000 字符下限（缺少 `context_length`）。[未列出修复 PR]
- **[#147923](https://github.com/NousResearch/hermes-agent/issues/147923)** — Desktop：cron 投递后的助手消息在下一个回合前不渲染。
- **[#150123](https://github.com/NousResearch/hermes-agent/issues/150123)** — 浏览器 dashboard 聊天在单 profile 安装且会话活跃时崩溃循环（`gateway exited (1)`）。*（今日新增。）*

**P3 / 较低**
- **[#142743](https://github.com/NousResearch/hermes-agent/issues/142743)** — v0.22.3 Docker 全新 gateway 因其自身 `gateway.sock` 上的 `unsafe_control_permissions` 而崩溃循环。
- **[#141755](https://github.com/NousResearch/hermes-agent/issues/141755)** — 安全扫描器误报阻止合法社区技能（`book-to-skill`，132 项发现）。
- **[#150010](https://github.com/NousResearch/hermes-agent/issues/150010)** — Desktop TTS 在每个句子边界停顿约 1 秒。**修复 PR 存在但处于开启且冲突状态：** [#102928](https://github.com/NousResearch/hermes-agent/pull/102928)。
- **[#149841](https://github.com/NousResearch/hermes-agent/issues/149841)** *（新增）* — 当 `mtime` 被保留时（`cp -p`、`rsync -a`、`mv`），scratch prune 会删除新鲜副本。
- **[#150329](https://github.com/NousResearch/hermes-agent/issues/150329)** *（新增，安全）* — 审批门控绕过：shell 函数/别名隐藏危险动词（`f(){ rm "$@"; }; f -rf /` 通过 hardline 下限）；还有未标记的管道到 shell 汇聚点。
- **[#148831](https://github.com/NousResearch/hermes-agent/issues/148831)** — `delegate_task` 静默丢弃顶层路由字段；子运行使用默认路由。*（报告者引用了修复 PR。）*
- **[#143243](https://github.com/NousResearch/hermes-agent/issues/143243)** — 已结束的经典 Group Chat 在飞行中同步时复活。
- **[#130072](https://github.com/NousResearch/hermes-agent/issues/130072)** — 当 provider 存在但加载失败时，`hermes memory status` 报告“未安装”。

**评估：** 稳定性负担集中在 **Desktop/gateway 会话状态同步**（多份 `dokterdok` 报告）和 **Windows 更新/状态完整性**。安全相关条目（#150329、#141755）值得优先分诊，因为它们触及审批/防护边界。

---

## 6. 功能请求与路线图信号

下个版本的高可能性候选：

1. **跨 gateway bot 协作** — [#97681](https://github.com/NousResearch/hermes-agent/issues/97681)（`needs-decision`，最活跃）：若获批，将是一个跨多个版本的架构计划（会话、消息投递、gateway 状态由 sweeper 标记）。
2. **所有权范围的背景技能写入** — [#142933](https://github.com/NousResearch/hermes-agent/issues/142933)（`skills.background_review_write_scope`）：将背景自我改进限制在 curator 管理的技能。可能是小到中型配置功能。
3. **Desktop 内联批准/编辑弹窗** — [#84189](https://github.com/NousResearch/hermes-agent/issues/84189)：与 #142933 天然配对；用户需求高。
4. **Email MCP 集成包** — [#99030](https://github.com/NousResearch/hermes-agent/issues/99030)（AgentMail/IMAP/SMTP）：生态推动一等公民级邮件支持。
5. **只读 Kanban/run/memory API 端点** — [#149815](https://github.com/NousResearch/hermes-agent/issues/149815)：外部集成（Home Assistant “Hermes Voice”）驱动 API 界面请求。
6. **Webapp 的 PWA 可安装性** — [#150345](https://github.com/NousResearch/hermes-agent/issues/150345)：简单（`manifest.json`），移动端收益高——可能是快速胜利。
7. **llama.cpp 槽位保存/恢复** — [#141605](https://github.com/NousResearch/hermes-agent/issues/141605)（本地模型会话连续性）。
8. **移动端语音气泡 + 更丰富的头像** — [#148447](https://github.com/NousResearch/hermes-agent/issues/148447)。
9. **处处统一的用户可见 agent 名称** — [#146434](https://github.com/NousResearch/hermes-agent/issues/146434)（作者希望在设计后实现）。

**预测：** #150345（PWA manifest）和 #142933（写入范围配置）是成本最低的近期胜利；#84189 和 #97681 受决策门控，更可能落入后续里程碑。

---

## 7. 用户反馈摘要

**痛点：**
- **Windows 上的数据完整性与更新**（#147938）：过期快照恢复侵蚀了对更新器的信任——最安全关键的投诉。
- **审批工作流在 Desktop 上不可见**（#84189、#142933）：分阶段写入仅存在于斜杠命令之后；用户感觉对 agent 自我修改失去了控制。
- **群聊/Desktop 同步不稳定**（#143303、#143302、#143242、#143241、#143243、#147626）：重复房间、消息丢失、幽灵“已停止”回合——多界面用户摩擦大。
- **安全扫描器过度拦截**（#141755）：合法社区技能被阻止，损害生态采用。
- **审批门控绕过**（#150329）：注重安全的用户标记了 hardline/权限检测的漏洞。
- **上下文/规模限制**（#108891 delegate 20k 下限；#75130 无限提案队列）。

**积极用例与采用：**
- 基于 Hermes 构建的外部集成——**“Hermes Voice”** Home Assistant 桥接（#149815）、移动端 Android beta 反馈（#148447）、PWA 请求（#150345）——显示使用面不断扩大。
- 活跃的**插件生态**：大量目录 PR（api-architect、BlazePhoenix、Construct、fal、CrashLab、Telos、lampstand、tiled-sessions、evalroute、lfg-alarm）表明第三方开发者社区健康。

**满意度：** 总体建设性；PR 作者自述其修复由 AI 辅助并经过对抗性审查（例如 #148831）——这是用户投入度高、技术成熟的标志。不满主要来自工作流摩擦和可靠性，而非根本性的能力差距。

---

## 8. 积压关注

长期未响应或受决策阻塞、需维护者关注的事项：

- **[#75130](https://github.com/NousResearch/hermes-agent/issues/75130)**（自 2026-07-31 开启，7 条评论）— `write_approval` 下无限/失效的技能提案队列；长期可靠性债务。
- **[#84189](https://github.com/NousResearch/hermes-agent/issues/84189)**（自 2026-08-12 开启，`needs-decision`，4 👍）— Desktop 审批弹窗；需要设计决策。
- **[#97681](https://github.com/NousResearch/hermes-agent/issues/97681)**（自 2026-08-29 开启，40 条评论，`needs-decision`）— 跨 gateway 协作；参与度最高的条目但受决策门控。
- **[#86105](https://github.com/NousResearch/hermes-agent/pull/86105)**（自 2026-08-14 开启，`needs-decision`）— 外部 provider 已配置时强制启用内置内存；小型正确性修复等待决策。
- **[#85342](https://github.com/NousResearch/hermes-agent/pull/85342)**（自 2026-08-13 开启，`needs-decision`，安全/兼容性风险）— Docker 在配置漂移时拒绝持久复用；安全相关，停滞中。
- **[#102928](https://github.com/NousResearch/hermes-agent/pull/102928)** — TTS 预取修复；**处于开启状态且与 `main` 冲突**，阻塞 #150010 的关闭。
- **[#150329](https://github.com/NousResearch/hermes-agent/issues/150329)** *（新增）* — 通过函数/别名的审批门控绕过；建议尽快进行安全分诊。
- **#147938**（P0，Windows 数据完整性）— 鉴于潜在数据丢失，需维护者立即接手。

**建议：** 优先分诊 P0 Windows state.db 问题（#147938）和安全绕过（#150329），解除 #102928 的阻塞，并清理 `needs-decision` 积压（#97681、#84189、#86105、#85342），这是合并 PR 吞吐量和社区情绪的主要拖累。

---

*摘要基于 2026-11-07 的 GitHub 活动生成。链接指向 NousResearch/hermes-agent 仓库。*

</details>

<details>
<summary><strong>IronClaw</strong> — <a href="https://github.com/nearai/ironclaw">nearai/ironclaw</a></summary>

过去24小时无活动。

</details>

<details>
<summary><strong>QwenPaw</strong> — <a href="https://github.com/agentscope-ai/QwenPaw">agentscope-ai/QwenPaw</a></summary>

# QwenPaw 项目摘要 — 2026-11-07

## 1. 今日概览
QwenPaw 展现出**高维护活跃度**：过去 24 小时内有 22 个 issue 和 38 个 PR 被处理，其中 20 个 PR 已合并/关闭，18 个仍处于打开状态——吞吐量与积压量接近 1:1。活动以**稳定性与正确性修复**为主，而非新功能开发：内存检索、provider 配置校验、通道插件（Matrix）以及 Windows 特有的路径处理。本次没有发布新版本，因此项目仍停留在 `2.3.0b2` 控制台版本线，而 `2.4` 后端包已在现场流通。社区互动按单项来看较为温和（大多为 1–4 条评论，零反应），表明分流覆盖面广但深度较浅。整体项目健康度：**强劲**——维护者的关闭速度与用户的提交速度相当，多个长期遗留 issue（可追溯至 2026 年 3 月）终于得到解决。

---

## 2. 发布
**无。** 过去 24 小时内没有新发布。注意：现场仍分散在 `2.2.2`、`2.3.0b2`（控制台）和 `2.4` 后端元数据之间，若干缺陷报告正源于此版本错配（例如 #8580 报告 UI v2.3.0b2 搭配后端 2.4 和 `reme_ai` 0.4.2.0）。

---

## 3. 项目进展
以下已关闭/已合并事项代表了今日的实质性推进：

**已落地的修复（已关闭的 PR / issue）：**
- **Provider 配置校验** — [#8550](https://github.com/agentscope-ai/QwenPaw/pull/8550)（修复 [#8549](https://github.com/agentscope-ai/QwenPaw/issues/8549)）：超出范围的 `thinking_budget` / `max_input_length` 现在会在写入路径被拒绝，而不再静默持久化并在重启后破坏 provider 发现。
- **Matrix 通道恢复** — [#8411](https://github.com/agentscope-ai/QwenPaw/pull/8411)（修复 [#8365](https://github.com/agentscope-ai/QwenPaw/issues/8365)）：在服务端设备丢失后，缓存会话得到恢复，缺失的 E2EE 设备密钥被重新上传。
- **第三方 agent 定时任务** — [#8535](https://github.com/agentscope-ai/QwenPaw/pull/8535)（修复 [#8533](https://github.com/agentscope-ai/QwenPaw/issues/8533)）：Codex/Qoder cron 任务不再因 `Third-party agent requests require text or attachments` 而失败。
- **动态 MCP 持久化** — [#373](https://github.com/agentscope-ai/QwenPaw/pull/373)（首次贡献者，自 3 月起打开）：动态加载的 MCP 工具现在可跨会话轮次持久化。
- **控制台聊天切换状态** — [#8600](https://github.com/agentscope-ai/QwenPaw/pull/8600)：`/clear` 的聊天在重新打开后保持为空（修复 [#8596](https://github.com/agentscope-ai/QwenPaw/issues/8596)）。
- **测试稳定性** — [#8604](https://github.com/agentscope-ai/QwenPaw/pull/8604)、[#7858](https://github.com/agentscope-ai/QwenPaw/issues/7858)：Windows shell 取消挂起和未 await 协程警告已解决。

**仍在审查中（高价值）：**
- [#8234](https://github.com/agentscope-ai/QwenPaw/pull/8234) — read/edit/grep 中的 GBK/CP936 解码（修复中文 Windows 上的静默字节丢失）。
- [#8457](https://github.com/agentscope-ai/QwenPaw/pull/8457) — 自动记忆召回以参考上下文形式注入，而非合成 assistant 轨迹。
- [#8472](https://github.com/agentscope-ai/QwenPaw/pull/8472) — 带多查询精度历史的 reranker 模型中心。

---

## 4. 社区热点话题
互动分散；评论最多的条目（各 4 条评论）集中在内存标记和聊天状态行为上。

| 条目 | 评论数 | 链接 | 潜在需求 |
|---|---|---|---|
| #8580 内存标签索引返回 503 | 4 | [链接](https://github.com/agentscope-ai/QwenPaw/issues/8580) | 启用 `auto_memory_generate_tags` 的用户期望标签**可读**，而不仅仅是生成。索引重建的可靠性是内存子系统的信任问题。 |
| #8596 `/clear` 消息重新出现 | 4 | [链接](https://github.com/agentscope-ai/QwenPaw/issues/8596) | 聊天状态缓存正确性——用户依赖 `/clear` 语义进行隐私/重置工作流。 |
| #1453 离线安装无法连接本地 Qwen | 4 | [链接](https://github.com/agentscope-ai/QwenPaw/issues/1453) | **离线/隔离网络部署**是真实用例；本地 Qwen 3/3.5 与 DeepSeek R1 的连接性对等很重要。8 个月后关闭。 |
| #8276 ReMe reranker 接受空 api_key | 4 | [链接](https://github.com/agentscope-ai/QwenPaw/issues/8276) | 配置校验缺口导致晦涩的 "unexpected error"——用户希望快速失败并给出可操作的消息。 |
| #7436 IM 中紧凑工具调用显示 | 3 | [链接](https://github.com/agentscope-ai/QwenPaw/issues/7436) | 聊天通道噪音；企业 IM（飞书/钉钉）用户希望简洁渲染。 |
| #5110 引用转后续追问（Perplexity 风格） | 3 | [链接](https://github.com/agentscope-ai/QwenPaw/issues/5110) | 基于选区的上下文注入是常见的 assistant UX 预期。 |

**观察：** 一个反复出现的主题是**"功能存在但不可读/不可发现"**——内存标签已生成但不可读（#8580）、默认 agent 可通过 API 编辑但在 UI 中被禁用（#8576）、插件设置路由丢失导航上下文（#8590）。这表明 UI/后端契约漂移值得专门审计。

---

## 5. 缺陷与稳定性
按严重程度排序，附修复 PR 状态：

**严重 / 阻断**
1. **#8592 — 创作者审核轮次永久无法裁定**（[链接](https://github.com/agentscope-ai/QwenPaw/issues/8592)）：REJECT 始终返回 422 "visual artifact reference references missing id"；同时捆绑了过期的 `source_url` 502 和忽略 `CREATOR_FFMPEG_PATH` 的 `frame_stats`。**硬阻断**——审核轮次永远无法关闭。尚无修复 PR。
2. **#8603 — 在自定义 OpenAI 兼容 provider 上 `run_code` 以空参数调用**（[链接](https://github.com/agentscope-ai/QwenPaw/issues/8603)）：在 `kimi` / `kimi-codingplan` 搭配 `k3.1-pro` 时，每次 `run_code` 调用都发出 `{}`；在校验时被拒绝。**完全阻断该 provider 配置的工具使用**。尚无修复 PR。
3. **#8580 — 内存标签索引从不重建（503）**（[链接](https://github.com/agentscope-ai/QwenPaw/issues/8580)）：标签已生成但不可读。尚无修复 PR。

**高**
4. **#8585 — `recall_history` 在 Windows 上失效**（[链接](https://github.com/agentscope-ai/QwenPaw/issues/8585)）：`\\?\` 扩展长度路径破坏了 SQLite 只读 URI。跨版本（2.2.x 起）。尚无修复 PR。
5. **#8517 — Hub Docker 以非 root 运行失败**（[链接](https://github.com/agentscope-ai/QwenPaw/issues/8517)）：`config.json` 权限被拒绝。尚无修复 PR——加固环境的部署阻断。
6. **#8538 — 多个工作区启用时 Matrix 插件失败**（[链接](https://github.com/agentscope-ai/QwenPaw/issues/8538)）：10 秒共享启动窗口仅覆盖第一个工作区。尚无修复 PR（相关 #8411 关闭了另一个 Matrix 问题）。

**中**
7. **#8576 — 控制台中默认 agent 编辑被禁用**（[链接](https://github.com/agentscope-ai/QwenPaw/issues/8576)）：**已有修复 PR**：[#8608](https://github.com/agentscope-ai/QwenPaw/pull/8608)。
8. **#8595 — `qwenpaw-creator` v2.0.4 插件安装失败（"No package metadata"）**（[链接](https://github.com/agentscope-ai/QwenPaw/issues/8595)）：与 #8598 依赖检查重构（已关闭）相关。尚无确认的直接修复 PR。
9. **#8590 — 插件设置路由丢失导航上下文**（[链接](https://github.com/agentscope-ai/QwenPaw/issues/8590)）：已关闭；无可见的关联修复 PR。

**今日关闭（已解决）：** #8549（配置校验，PR #8550）、#8365（Matrix 密钥，PR #8411）、#8533（cron 请求，PR #8535）、#8516（nginx 后 SSE `X-Accel-Buffering`）、#7857（ACP 关闭泄漏）、#8596（聊天重新打开）。

---

## 6. 功能请求与路线图信号
| 请求 | 链接 | 信号强度 |
|---|---|---|
| 用于紧凑 IM 工具显示的 `tool_call_format` 配置 | [#7436](https://github.com/agentscope-ai/QwenPaw/issues/7436) | **强**——已在打开的 PR [#8610](https://github.com/agentscope-ai/QwenPaw/pull/8610) 中实现。可能进入下一个小版本。 |
| 用于后续上下文的引用/参考文本 | [#5110](https://github.com/agentscope-ai/QwenPaw/issues/5110) | **强**——已在打开的 PR [#8609](https://github.com/agentscope-ai/QwenPaw/pull/8609) 中实现。 |
| 浏览器标签标题跟随会话名称 | [#8605](https://github.com/agentscope-ai/QwenPaw/issues/8605) | 中等——小型、范围明确，面向多标签页重度用户。 |
| 创作者 UI 修复（按钮对比度、模型/密钥独立性） | [#8591](https://github.com/agentscope-ai/QwenPaw/issues/8591) | 中等——创作者中的 UX 债务；按钮在白色背景上不可见。 |
| Reranker 模型中心 | [#8472](https://github.com/agentscope-ai/QwenPaw/pull/8472) | 强——大型功能 PR 审查中，可能进入 2.4.x。 |

**对下一版本的预测：** 紧凑工具调用显示（#8610）、引用转后续追问（#8609）和默认 agent 编辑（#8608）都是小型、自包含的 PR，已可落地——进入下一个控制台版本的概率很高。Reranker 模型中心（#8472）较大，可能推迟。

---

## 7. 用户反馈总结
**痛点：**
- **版本错配困惑**——多份报告混合了 UI 2.3.0b2、后端 2.4 和 `reme_ai` 0.4.2.0，使分流对用户和维护者都更加困难。
- **Windows 是二等公民**——GBK 编码丢失（#8234）、`\\?\` 路径破坏（#8585）、shell 取消挂起（#8604）。专门的 Windows CI 通道将带来回报。
- **静默失败**——接受空 `api_key`（#8276）、持久化无效配置（#8549）、丢弃字节的解码（#8234）。用户一致要求**带清晰消息的快速失败校验**。
- **UI/后端契约漂移**——功能通过 API 可用但在 UI 中被禁用或损坏（#8576、#8590、#8580）。

**积极信号：** 用户正在贡献修复（多个 `first-time-contributor` PR：#8606、#8550、#8535、#373），长期停滞的 issue 正在关闭（#1453 在 8 个月后、#373 在 8 个月后）。满意度似乎**中等偏好**——挫败感集中在特定子系统（内存索引、Matrix、创作者），而非核心 agent 循环。

---

## 8. 待办关注
需要维护者关注的事项：

- **[#373](https://github.com/agentscope-ai/QwenPaw/pull/373)** — 动态 MCP 按会话工具持久化。自 **2026-03-02** 打开（约 8 个月），首次贡献者，今日刚关闭。值得确认合并并感谢贡献者。
- **[#8234](https://github.com/agentscope-ai/QwenPaw/pull/8234)** — GBK/CP936 解码修复。自 **2026-10-14** 打开，XXL 规模，审查中。对中文 Windows 用户影响大；需要审查带宽。
- **[#8236](https://github.com/agentscope-ai/QwenPaw/pull/8236)** — Ollama `num_ctx` 上下文大小。自 **2026-10-14** 打开，XL。本地模型用户是增长中的群体。
- **[#8100](https://github.com/agentscope-ai/QwenPaw/pull/8100)** — 运行时解析媒体能力。自 **2026-10-03** 打开。支持图像的模型被静默拒绝——正确性缺陷，修复已就绪。
- **[#8274](https://github.com/agentscope-ai/QwenPaw/pull/8274)** — 聊天切换时保留待处理的用户输入。自 **2026-10-16** 打开。与数据丢失相邻的 UX 缺陷。
- **[#5110](https://github.com/agentscope-ai/QwenPaw/issues/5110)** — 引用转后续追问。自 **2026-06-11** 打开（约 5 个月）；现已有 PR（#8609），尽快闭环将是良好习惯。
- **[#8592](https://github.com/agentscope-ai/QwenPaw/issues/8592)** — 创作者审核阻断。昨日提交，尚无维护者回应——**最高优先级的新到项**。

**建议：** 优先审查 #8234 和 #8100（均已就绪、高影响正确性修复），并为 #8592（创作者阻断）和 #8585（Windows `recall_history` 回归，跨版本）指派负责人。

</details>

<details>
<summary><strong>ZeroClaw</strong> — <a href="https://github.com/zeroclaw-labs/zeroclaw">zeroclaw-labs/zeroclaw</a></summary>

# ZeroClaw 项目摘要 — 2026-11-07

## 1. 今日概览

ZeroClaw 正处于高速安全加固阶段。活动非常活跃：过去 24 小时内更新了 25 个 issue 和 50 个 PR（13 个 issue 已关闭，13 个 PR 已合并/关闭，37 个 PR 仍处于开放状态）。主旋律是**身份/访问控制与权限强制执行**——多个 S0/S1 级发现（会话数据所有权绕过、SOP 通配符工具选择器、cron 审批绕过）正在被关闭，相应的修复 PR 正在审查中。一个依赖安全公告（vodozemac）阻断了 CI，并于当天完成修复。整体项目健康度在响应速度方面表现强劲，但大量 P1/风险：高 的安全项涌入 `v0.9.0` 里程碑，表明发布门禁 crunch 正在进行中。

## 2. 发布

过去 24 小时内无新版本发布。值得注意的是，多个已关闭的 issue 带有 `release:v0.9.0` 标签，表明针对 v0.9.0 的发布门禁工作正在积极推进。

## 3. 项目进展

今日关闭的 issue（共 13 个）包含有意义的安全与正确性修复：

- **[#11127](https://github.com/zeroclaw-labs/zeroclaw/issues/11127)** [已关闭, P1, S0] 会话数据工具绕过了主体所有权检查（例如，`sessions_history` 可跨主体读取）。
- **[#11123](https://github.com/zeroclaw-labs/zeroclaw/issues/11123)** [已关闭, P1, S0] SOP 执行接受了通配符工具选择器，而未要求 `tools:execute`。
- **[#12051](https://github.com/zeroclaw-labs/zeroclaw/issues/12051)** [已关闭] `sop_workshop apply` 允许受限主体编写以操作员权限执行的共享 SOP。
- **[#12046](https://github.com/zeroclaw-labs/zeroclaw/issues/12046)** [已关闭] 通配符工具选择器将不感知主体的工具（cron、schedule、对等消息传递、SOP 执行）泄露给非管理员主体。
- **[#12272](https://github.com/zeroclaw-labs/zeroclaw/issues/12272)** [已关闭] vodozemac 0.11.0 RUSTSEC 公告破坏了安全质量门禁；已通过 PR **[#12273](https://github.com/zeroclaw-labs/zeroclaw/pull/12273)** 修复（升级至 0.11.2）。
- **[#12176](https://github.com/zeroclaw-labs/zeroclaw/issues/12176)** [已关闭] 延迟 MCP 工具列表以 HashMap 顺序排列，导致每个会话的 prompt 缓存失效。
- **[#11962](https://github.com/zeroclaw-labs/zeroclaw/issues/11962)** [已关闭] 守护进程的 64 个活跃 RPC 会话在空闲时从未释放。
- **[#12155](https://github.com/zeroclaw-labs/zeroclaw/issues/12155)**、**[#12178](https://github.com/zeroclaw-labs/zeroclaw/issues/12178)**、**[#12217](https://github.com/zeroclaw-labs/zeroclaw/issues/12217)**、**[#12248](https://github.com/zeroclaw-labs/zeroclaw/issues/12248)**、**[#12249](https://github.com/zeroclaw-labs/zeroclaw/issues/12249)** — 各类 bug/文档/任务关闭（快速入门消息传递、`deliver_file` 描述、邮件文档、不可运行工具的审批门禁、仪表盘配置页面可见性）。

推进主要工作流的关键开放 PR：

- **[#9746](https://github.com/zeroclaw-labs/zeroclaw/pull/9746)** 会话工具和 `discord_search` 的按代理所有权作用域（发布门禁，XL）。
- **[#11149](https://github.com/zeroclaw-labs/zeroclaw/pull/11149)** 在 RPC/工具/令牌撤销的写入点强制执行 cron 审批与权限。
- **[#12239](https://github.com/zeroclaw-labs/zeroclaw/pull/12239)** 通过会话效果保持当前权限（#11234 的后续）。
- **[#12270](https://github.com/zeroclaw-labs/zeroclaw/pull/12270)** 中继服务的 Web 仪表盘按节点主机（#10315 第二阶段）。
- **[#9809](https://github.com/zeroclaw-labs/zeroclaw/pull/9809)** 多模型提供商配置（长期运行，XL）。

## 4. 社区热点

按评论/反应最活跃：

1. **[#10526](https://github.com/zeroclaw-labs/zeroclaw/issues/10526)**（16 条评论）— RFC：仅追加的会话事件历史、确定性重放、派生代理流。审查后**已关闭**；标志着架构方向转向事件溯源运行时状态（补充 #9597 中的持久性工作）。
2. **[#8132](https://github.com/zeroclaw-labs/zeroclaw/issues/8132)**（13 条评论，1 👍）— 在 React/Vite 迁移之前评估 Rust/WASM UI。关于从构建/运行时中移除 Node.js 的长期架构辩论；目前需要作者行动。
3. **[#10926](https://github.com/zeroclaw-labs/zeroclaw/issues/10926)**（9 条评论）— Matrix `send_via` 将对等身份视为房间目标。
4. **[#9779](https://github.com/zeroclaw-labs/zeroclaw/issues/9779)**（7 条评论，P1）— 文档中记录的 `sops_dir` 默认值被守护进程静默忽略。

**潜在需求：** 社区正在推动 (a) 代理会话的运行时可观测性/可重放性，(b) 供应链简化（移除 Node.js），以及 (c) 配置默认值与守护进程启动路径之间一致、有文档记录的行为。

## 5. Bug 与稳定性

按严重程度排序（除非另有说明，均为今日报告/更新）：

| 严重程度 | Issue | 状态 / 修复 PR |
|---|---|---|
| **S1** | [#12272](https://github.com/zeroclaw-labs/zeroclaw/issues/12272) vodozemac RUSTSEC-2026-0445/-0451 在所有 PR 上破坏 CI | **已关闭**；修复 PR [#12273](https://github.com/zeroclaw-labs/zeroclaw/pull/12273) 同日关闭 |
| **S1** | [#12263](https://github.com/zeroclaw-labs/zeroclaw/issues/12263) 严格提供商永久拒绝长工具密集型会话（422 `tool_call_id`），对话被砖化 | 开放；尚无修复 PR |
| **S2** | [#12260](https://github.com/zeroclaw-labs/zeroclaw/issues/12260) RPC `cron/add`/`cron/patch` 将请求视为操作员 shell 审批 | 开放；由 [#11149](https://github.com/zeroclaw-labs/zeroclaw/pull/11149) 覆盖 |
| **S2/S0** | [#12279](https://github.com/zeroclaw-labs/zeroclaw/issues/12279) 通道启动竞态可能丢失在转录读取与上限之间追加的消息 | 开放；无修复 PR |
| **S2** | [#9572](https://github.com/zeroclaw-labs/zeroclaw/issues/9572) 网关 WebSocket 轮次溢出 Tokio 工作线程栈（调试构建） | 开放（P1，no-stale） |
| **S2** | [#12052](https://github.com/zeroclaw-labs/zeroclaw/issues/12052) 速率限制器以客户端提供的首个 XFF 跳为键 → 回环豁免可被欺骗 | 开放；修复 PR [#12114](https://github.com/zeroclaw-labs/zeroclaw/pull/12114) 审查中 |
| **S2** | [#10926](https://github.com/zeroclaw-labs/zeroclaw/issues/10926) Matrix `send_via` 错误路由到对等身份 | 开放 |
| **P1** | [#9779](https://github.com/zeroclaw-labs/zeroclaw/issues/9779) 依赖文档中 `sops_dir` 默认值时 SOP 静默从不加载 | 开放 |

**评估：** 安全类 issue 正在快速关闭；功能性 S1（#12263 砖化会话）和启动数据丢失（#12279）是最高剩余风险，且尚无修复 PR。

## 6. 功能请求与路线图信号

- **[#12181](https://github.com/zeroclaw-labs/zeroclaw/issues/12181)** Web 管理文档上传（#9488 的第一个第二阶段 Web 切片）— 可能近期实现；补充开放 PR [#12269](https://github.com/zeroclaw-labs/zeroclaw/pull/12269)（Web 聊天语音控制）和 [#11846](https://github.com/zeroclaw-labs/zeroclaw/pull/11846)（OpenRouter 余额设置）。
- **[#9597](https://github.com/zeroclaw-labs/zeroclaw/issues/9597)** 持久、可归属的对等代理轮次 — 符合 #10526 中接受的事件历史方向；v0.9.x 的强候选。
- **[#8132](https://github.com/zeroclaw-labs/zeroclaw/issues/8132)** Rust/WASM Web UI — 战略性，非迫近；受阻于作者行动。
- PR 驱动的在途功能：多模型提供商配置（[#9809](https://github.com/zeroclaw-labs/zeroclaw/pull/9809)）、多插件注册表来源（[#12216](https://github.com/zeroclaw-labs/zeroclaw/pull/12216)）、中继服务仪表盘（[#12270](https://github.com/zeroclaw-labs/zeroclaw/pull/12270)）、Telegram 语音回复合成（[#12276](https://github.com/zeroclaw-labs/zeroclaw/pull/12276)）。

**预测：** 下一个标记版本很可能是 v0.9.0 安全门禁裁剪（所有权作用域、cron 权限、SOP 授权）加上中继仪表盘和 Telegram/Discord 通道功能。

## 7. 用户反馈摘要

- **以安全为核心的贡献者（Audacity88、IftekharUddin、JordanTheJet）** 正在系统地审计主体/权限模型；发现被快速接受并关闭——这是健康分诊的标志，但也表明授权模型发布时存在缺口。
- **运维痛点：** 静默失败是反复出现的抱怨——SOP 不加载（[#9779](https://github.com/zeroclaw-labs/zeroclaw/issues/9779)）、快速入门声称“未写入配置”却写入了（[#12155](https://github.com/zeroclaw-labs/zeroclaw/issues/12155)）、长会话变得永久不可用（[#12263](https://github.com/zeroclaw-labs/zeroclaw/issues/12263)）。用户重视可预测性胜过聪明。
- **代理/部署用户** 遇到现实世界的网关假设（[#12052](https://github.com/zeroclaw-labs/zeroclaw/issues/12052) 中的 XFF 处理）；**通道用户** 报告启动时的消息丢失窗口（[#12279](https://github.com/zeroclaw-labs/zeroclaw/issues/12279)）。
- 满意度信号：依赖安全 issue 的快速同日关闭和主动跟进任务（#12248、#12261、#12280）表明维护者响应迅速；不满意度集中在静默错误配置和边缘情况会话损坏。

## 8. 积压关注

需要维护者关注的长期项目：

- **[#8132](https://github.com/zeroclaw-labs/zeroclaw/issues/8132)**（自 2026-06-22 开放，`needs-author-action`）— WASM UI 决策停滞；战略岔路口仍未解决。
- **[#9572](https://github.com/zeroclaw-labs/zeroclaw/issues/9572)**（自 2026-07-31，P1，no-stale）— 网关 WebSocket 轮次中的 Tokio 栈溢出；已接受但未修复。
- **[#9779](https://github.com/zeroclaw-labs/zeroclaw/issues/9779)**（自 2026-08-06，P1）— SOP 默认目录静默失败；用户影响高，无关联修复 PR。
- **[#9746](https://github.com/zeroclaw-labs/zeroclaw/pull/9746)**（自 2026-08-04，XL，发布门禁，`needs-author-action`）— 核心所有权作用域 PR；一个已搁置三个月的发布阻塞项。
- **[#9809](https://github.com/zeroclaw-labs/zeroclaw/pull/9809)**（自 2026-08-07，XL，`needs-author-action`）— 多模型提供商配置；大型、有价值且停滞。
- **[#10926](https://github.com/zeroclaw-labs/zeroclaw/issues/10926)**（自 2026-09-17，已接受，`follow-up`）— Matrix 路由 bug，无关联修复 PR。

---
*数据来源：github.com/zeroclaw-labs/zeroclaw，快照 2026-11-07。*

</details>

---
*本日报由 [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar) 自动生成。*