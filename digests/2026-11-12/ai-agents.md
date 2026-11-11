# OpenClaw 生态日报 2026-11-12

> Issues: 471 | PRs: 500 | 覆盖项目: 5 个 | 生成时间: 2026-11-11 23:28 UTC

- [OpenClaw](https://github.com/openclaw/openclaw)
- [Hermes Agent](https://github.com/nousresearch/hermes-agent)
- [IronClaw](https://github.com/nearai/ironclaw)
- [QwenPaw](https://github.com/agentscope-ai/QwenPaw)
- [ZeroClaw](https://github.com/zeroclaw-labs/zeroclaw)

---

## OpenClaw 项目深度报告

# OpenClaw 项目摘要 — 2026-11-12

## 1. 今日概览

OpenClaw 正处于高速运转、高争议的维护阶段。过去 24 小时内，仓库共记录 **471 条 Issue 更新**（264 条开放/活跃，207 条已关闭）和 **500 条 Pull Request 更新**（303 条开放，197 条已合并/已关闭），**零个新版本发布**——这意味着所有工作都通过 `main` 和开放分支流转，而非产出发布制品。当前的主线叙事是 **2026.9–2026.10 发布列车遗留的质量债务**：围绕 Gateway 启动阻塞、Windows 服务可靠性、插件源码捕获暂存以及 Telegram/claude-cli 消息丢失的一批 P0/P1 问题仍未解决，评论区讨论绵长。与此同时，维护者正在合入大量中小型修复（守护进程安装、转录渲染、插件徽章、压缩缓存），这表明项目正在积极分类处理而非构建新功能。整体健康度：**吞吐量强劲，但 Gateway/插件/Windows 路径上的稳定性信号密度令人担忧**。

---

## 2. 版本发布

**无。** 过去 24 小时内未发布新版本。值得注意的是，若干开放的 P0/P1 Issue 明确引用了 2026.9.6–2026.10.10 的回归问题，且至少一个（如 #191818）请求将某项修复（#180492）**回移植**到已发布的版本——这表明发布节奏与修复管线目前并不同步。维护者应预期在 Windows/Gateway 阻塞项解决后，将面临发布补丁版本的压力。

---

## 3. 项目进展

本窗口内约 197 个 PR 被合并/关闭。代表性已关闭项显示的是稳步的卫生修复，而非头条功能：

- **#197212（已关闭）** — iOS 重构：将约 10 个独立的 `NodeAppModel` 连接字段整合为每个 Gateway 会话一个阶段，并带有粘性共享暂停。减少状态分歧缺陷。[PR #197212](https://github.com/openclaw/openclaw/pull/197212)
- **#197057（已关闭）** — Tlon 频道：入站消息不再丢失原生列表内容（仅含列表的消息不再被视为空）。[PR #197057](https://github.com/openclaw/openclaw/pull/197057)
- **#197308 / #197398（已关闭）** — Knowledge 聊天导出时间戳校验以及 `oc-path` 点文件/连续点文件名解析。小型正确性修复。[PR #197308](https://github.com/openclaw/openclaw/pull/197308)、[PR #197398](https://github.com/openclaw/openclaw/pull/197398)
- **#197234（已关闭）** — Gateway 审批竞态：已授予的 exec/插件审批在等待期间另一会话发生变更时不再失败。[PR #197234](https://github.com/openclaw/openclaw/pull/197234)
- **#197392（已关闭）** — 针对反复出现的 SIGUSR2 关机验证超时的 CI 诊断。

**进行中的重要 PR**（尚未合并）：签名前 macOS 原生依赖打包（#197383，P1）、有界托管浏览器内存（#197408）、有界可重载聊天历史（#187440）、工作树模板预热（#197301），以及一个安全边界插件进度卡 SDK（#176273 关联提案）。

---

## 4. 社区热点话题

按评论活跃度排序；这些帖子揭示了项目真正的摩擦点。

| 条目 | 评论数 | 信号 |
|---|---|---|
| [#160959](https://github.com/openclaw/openclaw/issues/160959) — Gateway 在捕获大型外部插件时阻塞数分钟（2026.9.6 回归） | 81 | **最大痛点。** P0，钻石龙虾评级，崩溃循环影响。事件循环饥饿影响任何拥有大型插件/依赖树的用户。 |
| [#164294](https://github.com/openclaw/openclaw/issues/164294) — WAL 自动检查点永不完成（被进程内读取器钉住） | 40 | 静默数据库退化；用户看到 WAL 持续增长（17k 帧）。 |
| [#170956](https://github.com/openclaw/openclaw/issues/170956) — Windows：启动缓慢、插件关闭不干净、`schtasks` 权限错误 | 31 | P0，ux-release-blocker。托管服务安装下重启/更新不可靠。 |
| [#168955](https://github.com/openclaw/openclaw/issues/168955) — 2026.10.1 启动回归：就绪约 1030 秒 vs 约 410 秒（Windows） | 21 | Sidecar 资产阶段 `control-ui-assets` 阻塞 477 秒。 |
| [#162585](https://github.com/openclaw/openclaw/issues/162585) — 插件源码捕获将插件重新物化到 `package-0/node_modules/<self>`；树线性增长 | 16 | Windows 上内存/磁盘膨胀；gateway 永不健康。 |
| [#125764](https://github.com/openclaw/openclaw/issues/125764) — Telegram 出站发送在单次网络故障后被死信化 | 14 | 高价值公告/完成回复的静默消息丢失。 |
| [#144809](https://github.com/openclaw/openclaw/issues/144809) — claude-cli 轮次超过 `RUN_STALE_TAKEOVER_MS` 时丢失整个回复 | 14 | “无活跃工具权限快照”——完整生成的输出被丢弃。 |
| [#101929](https://github.com/openclaw/openclaw/issues/101929) — 上下文溢出估算器相比计费用量高估 2.3–2.6 倍 | 13 | 成本/token 计量不匹配；已由合并的 #168338 部分解决。 |

**底层需求：**（a）无论插件大小如何，Gateway 启动可预测；（b）消息适配器具备持久、可重试的出站投递；（c）Windows 在服务管理上与 Linux/macOS 对等；（d）诚实的 token 计量。这些都是*运行正确性*诉求，而非功能诉求——一个成熟度信号。

---

## 5. 缺陷与稳定性

按严重程度排序（依据标签和影响标记）。**顶级条目均未关联修复 PR**，这是关键风险。

**P0 / 发布阻塞**
1. **#160959** — 大型插件捕获期间 Gateway 事件循环阻塞（2026.9.6 回归）。*无修复 PR。* [链接](https://github.com/openclaw/openclaw/issues/160959)
2. **#170956** — Windows 托管服务重启/更新不可靠（`schtasks` 权限、插件关闭不干净）。*无修复 PR。* [链接](https://github.com/openclaw/openclaw/issues/170956)
3. **#178441** — 所有者 Telegram 会话被 `providerReview` 扣留，无延续/恢复；身份别名误报。*无修复 PR。* [链接](https://github.com/openclaw/openclaw/issues/178441)
4. **#194664** — Codex 受保护延续在会话 Stop 报告无活跃运行时阻塞所有后续操作（2026.11.1）。*无修复 PR。* [链接](https://github.com/openclaw/openclaw/issues/194664)
5. **#146791** — iOS App Store 构建从不发布 `push.apns.register`；推送/唤醒失效。*无修复 PR。* [链接](https://github.com/openclaw/openclaw/issues/146791)

**P1 / 高**
- **#168955** — Windows 启动回归（1030 秒 vs 410 秒）。*无修复 PR。*
- **#162585** — 插件源码捕获暂存爆炸（290MB → 612MB+/24 分钟）。*无修复 PR。*
- **#125764** — Telegram 单次尝试死信化。*无修复 PR。*
- **#144809** — claude-cli 长轮次回复丢失。*无修复 PR。*
- **#181662** — npm 更新使 Gateway 在 `activating` 状态停留约 19 分钟；停机时间随每个版本增长。*无修复 PR。*
- **#170928** — Windows 2026.10.2 gateway 就绪需 7–9 分钟，随后间歇性无响应。*无修复 PR。*
- **#191818** — 2026.10.10 仍附带 claude-cli “无记录推理账户”自动化失败；请求回移植 #180492。*无修复 PR。*
- **#194334** — MCP stdio 运行时在运行上下文永不清理时永久无法驱逐（回归）。*无修复 PR。*

**数据丢失 / 会话状态（P2），影响面广**
- **#172986** — WebChat 长路径渲染 + 间歇性载荷变异。
- **#141172** — 孤立 `agent.run.started` 泄漏，轨迹刷新时外键失败。
- **#179676（已关闭）** — 粘贴文本超过 1,000 字符在后续轮次丢失（已关闭，推定为已修复）。
- **#186973** — 陈旧更新历史横幅在重启/重开机后持续存在。

**好消息：** 本窗口关闭了若干正确性回归（#179481、#173095、#57278、#163125、#164907），且一个压缩缓存修复（#197061）以及缓存正确的手动压缩正在 PR 审查中。**开放 P0/P1 缺陷与开放修复 PR 的比例仍不理想**，尤其是 Windows 和消息适配器方面。

---

## 6. 功能请求与路线图信号

评论最多的功能 Issue 集中在**操作员控制与多智能体可靠性**：

- **#192009** — 跨提供商 cron 回退账户的显式授权（OpenAI 主用，xAI 回退）。*近期合并的可能候选*——低风险配置面，用户需求明确。
- **#176273** — Claw Orchestrator 的原生进度卡 SDK + 可信插件准入。标记为安全边界；可能是**维护者决策项**，而非快速修复。
- **#179954** — 为 `MEMORY.md`/`USER.md` 之外的内存文件提供可配置的逐条分块路径。*已关联开放 PR*——可能的下一个小版本。
- **#170562** — 针对搁浅请求方结算唤醒（子智能体运行）的 Doctor 恢复命令。操作员恢复主题，与 #179481 配对。
- **#143387** — Android 通知 QoL：会话感知提醒、来源标签、跨设备抑制。
- **#154524** — 无障碍：将聊天消息暴露为标题以供屏幕阅读器导航。
- **#169428** — 在 `/usage full` 页脚暴露服务认证配置（`model.auth_label`）。

**预测：** 预期 #192009 和 #179954 类条目将在任何新的大表面积之前落地。插件 SDK/安全类条目（#176273、#164355 钩子扩展点）最可能因审查深度而延期。

---

## 7. 用户反馈摘要

**痛点（不满）：**
- **Windows 用户承受不成比例的痛苦**——启动时间、服务重启、插件暂存爆炸、`schtasks` 权限。反复出现的措辞如“pas na veel proberen”（仅在多次重试后）表明真实的运维疲劳。
- **Telegram 和 claude-cli 上的静默消息丢失**是报告中最侵蚀信任的一类——回复已生成但从未投递，且无用户可见错误。
- **升级停机时间增长**（#181662）是随着版本累积而新兴的抱怨。
- **token/成本计量不信任**（#101929）——用户核对计费用量时发现 2.3–2.6 倍的高估。

**满意指标：**
- 高质量、仪器化良好的缺陷报告（崩溃循环标签、复现步骤、版本哈希）表明用户群体参与度高、技术成熟且对项目有投入。
- 近期关闭的粘贴文本持久化、侧边栏冷启动和 Google Chat 403 问题表明维护者对范围明确的报告响应积极。

**浮现的使用场景：** 带 cron/agentTurn 自动化的多智能体名册、重型多渠道部署（Telegram 群组、Signal、Mattermost、Tlon）、iOS/Mac 客户端配对，以及插件密集的工作区——最后一项正是顶级 P0 所在之处。

---

## 8. 积压观察

具有年龄、高严重度且无关联修复的 Issue/PR——维护者关注清单：

1. **#160959**（创建于 2026-09-29，**81 条评论**，P0，无修复 PR）——单一最高杠杆的稳定性条目；一个数月之久的回归仍未关闭。
2. **#164294**（2026-10-03，40 条评论，P2 但静默数据库退化）——WAL 检查点饥饿；需要就读取器钉住做出决策。
3. **#125764**（2026-08-18，P1，14 条评论）——Telegram 重试/死信架构；最古老的未解决消息丢失条目。
4. **#91931**（2026-06-10，P1，钻石龙虾）——预置的 SOUL/IDENTITY/USER 文件在首次运行前自动删除用户的 `BOOTSTRAP.md`。已超过 **5 个月**，带有 fix-shape-clear 标签且无合并修复——一个数据丢失相关的入门缺陷，值得决策。
5. **#132767**（2026-08-29，P2，钻石龙虾）——CLI 宿主省略提示前组装；systemPromptAddition 晚一轮到达模型；早先的修复 PR（#100984）关闭未合并。**孤儿修复。**
6. **#144736**（PR，自 2026-09-11 开放，P2，规模 M）——沙箱 `scp` 传输无截止时间泄漏子进程。陈旧 PR 且有充分证明；低风险合并候选。
7. **#165805**（PR，自 2026-10-05 开放）——节点目录列表分页修复；需要证明状态但长期陈旧。
8. **#146791**（2026-09-13，P0，iOS APNs 中继）——App Store 推送注册损坏；无修复 PR，而移动唤醒是核心价值主张。

**建议：** 本周将 #160959、#91931 和 #146791 分派给明确的负责人；前两者是“高评级、陈旧、未修复”的最明显案例，尽管吞吐量强劲，它们仍在侵蚀社区信心。

---

*数据来源：github.com/openclaw/openclaw 活动窗口 2026-11-11 → 2026-11-12。评论数反映 Issue/PR 帖子；PR 评论数在提供的数据集中为 null，故省略。*

---

## 横向生态对比

⚠️ 横向对比生成失败。

---

## 同赛道项目详细报告

<details>
<summary><strong>Hermes Agent</strong> — <a href="https://github.com/nousresearch/hermes-agent">nousresearch/hermes-agent</a></summary>

# Hermes Agent — 2026-11-12 项目摘要

## 1. 今日概览

Hermes Agent 仍处于高速推进、以维护为主的阶段：过去 24 小时内有 50 个 issue 和 50 个 PR 被处理，未发布新版本。活动主要集中在插件目录（今日 PR 中有相当大一部分是目录固定/新增，很可能由正在进行的 Plugin Hackathon 推动），同时还有一项大规模堆叠重构，将核心模块迁入 `hermes_agent/` 包（[#153151](https://github.com/NousResearch/hermes-agent/pull/153151)、[#153154](https://github.com/NousResearch/hermes-agent/pull/153154)、[#153155](https://github.com/NousResearch/hermes-agent/pull/153155)、[#153160](https://github.com/NousResearch/hermes-agent/pull/153160)）。值得注意的是，今日关闭了 18 个 issue，其中许多来自一次安全审计行动（EPIC #82591），表明团队正在积极清理积压，而非单纯开展新工作。整体健康状况：**活跃且响应及时，但仍存在一批不容忽视的 gateway/会话状态与沙箱相关 bug**，在用户报告中反复出现。

## 2. 发布

过去 24 小时无新版本发布。无版本变更、破坏性变更或迁移说明需要报告。

## 3. 项目进展

今日关闭的 issue 以安全审计行动和依赖卫生为主：
- [#16560](https://github.com/NousResearch/hermes-agent/issues/16560) — `tui_gateway/server.py` 中通过 `shell=True` 造成的命令注入（安全）。
- [#127882](https://github.com/NousResearch/hermes-agent/issues/127882) — `browser_vault_enter_code` TOTP 来源绑定缺口（P1 安全）。
- [#84253](https://github.com/NousResearch/hermes-agent/issues/84253)、[#84269](https://github.com/NousResearch/hermes-agent/issues/84269)、[#84256](https://github.com/NousResearch/hermes-agent/issues/84256) — 响应历史、运行注册表和幂等缓存的 profile/principal 作用域问题。
- [#106775](https://github.com/NousResearch/hermes-agent/issues/106775)、[#103798](https://github.com/NousResearch/hermes-agent/issues/103798)、[#51292](https://github.com/NousResearch/hermes-agent/issues/51292)、[#78543](https://github.com/NousResearch/hermes-agent/issues/78543) — 依赖/CVE 与容器镜像安全通告。
- [#62402](https://github.com/NousResearch/hermes-agent/issues/62402) — 桌面端审批卡片占位符修复。

PR 方面，今日合并/关闭了一大批**插件目录变更**：[#153388](https://github.com/NousResearch/hermes-agent/pull/153388)（aamio 0.7.42）、[#153377](https://github.com/NousResearch/hermes-agent/pull/153377)（mediarag 0.1.21）、[#153373](https://github.com/NousResearch/hermes-agent/pull/153373)（nexus 0.22.48）、[#153365](https://github.com/NousResearch/hermes-agent/pull/153365)（lumina）、[#153363](https://github.com/NousResearch/hermes-agent/pull/153363)（phone-senses）、[#153357](https://github.com/NousResearch/hermes-agent/pull/153357)（twring），以及 [#153856](https://github.com/NousResearch/hermes-agent/pull/153856) 修复了后台审查预算停止的一个边界情况。

## 4. 社区热门话题

| 条目 | 信号 | 潜在需求 |
|---|---|---|
| [#153396](https://github.com/NousResearch/hermes-agent/issues/153396) Team Gateway：每个 agent 自带 Slack 应用（8 条评论，7 👍） | 互动量最高的开放 issue | 用户希望每个 agent 拥有独立的机器人身份/头像，并能控制休眠/挂起行为；当前共享机器人中继方案在可用性和成本上令人担忧。 |
| [#142488](https://github.com/NousResearch/hermes-agent/issues/142488) 桌面端在 `prepare_model` 时切换模型崩溃（7 条评论） | 持续性崩溃，自 2026-10-21 起开放 | 跟随 profile 的会话在桌面端的可靠性；一个阻碍正常模型切换的回归问题。 |
| [#131326](https://github.com/NousResearch/hermes-agent/issues/131326) 沙箱浏览器找不到 Chromium（7 条评论） | 文档所述默认路径失效 | Docker 沙箱中的无头浏览器工具在 arm64 上实际已不可用；对浏览器 agent 用户影响很大。 |
| [#96027](https://github.com/NousResearch/hermes-agent/issues/96027) Gateway SQLite fd 泄漏（7 条评论） | 长期运行稳定性 | Gateway 进程在数周内缓慢耗尽文件描述符；不适合常驻部署。 |
| [#153727](https://github.com/NousResearch/hermes-agent/issues/153727) 15 个 profile 下 GIL 饥饿的 gateway 事件循环（6 条评论） | 性能/存活 | 多路复用的多 profile 部署在 CPU 压力下触发看门狗重启和 30 秒超时。 |

**规律：**讨论最集中的条目都围绕 **gateway**——多 profile 扩展、fd/GIL 资源泄漏以及每 agent 身份。Slack/Team Gateway 的可配置性是其中最突出的产品需求。

## 5. Bug 与稳定性

按严重程度排序（依报告所述）：

- **P1 / 安全：** [#127882](https://github.com/NousResearch/hermes-agent/issues/127882)（TOTP 来源绑定）和 [#44730](https://github.com/NousResearch/hermes-agent/issues/44730)（SimpleX 允许列表可通过显示名绕过）——均于今日关闭；请核实修复已进入发布说明。
- **P2 / 崩溃：** [#142488](https://github.com/NousResearch/hermes-agent/issues/142488) — 桌面端切换模型抛出 `JsonRpcGatewayError`；仍开放，未见到关联修复 PR。
- **P2 / 功能：** [#131326](https://github.com/NousResearch/hermes-agent/issues/131326) — 沙箱内找不到 Chromium；`AGENT_BROWSER_EXECUTABLE_PATH` 在沙箱路径上被丢弃。
- **P2 / 稳定性：** [#96027](https://github.com/NousResearch/hermes-agent/issues/96027) — `state.db`/`-wal` 上的 SQLite fd 泄漏（22 天后为 48/46 个 fd）；与 launchd 的 256-fd 限制相互作用。
- **P2 / 性能：** [#153727](https://github.com/NousResearch/hermes-agent/issues/153727) — GIL 饥饿 → 以退出码 75 重启、`session.resume` 超时。
- **P2 / 逻辑：** [#73748](https://github.com/NousResearch/hermes-agent/issues/73748) — 所有模型链失败（例如限流）后 gateway 轮次丢失且不重试；[#145920](https://github.com/NousResearch/hermes-agent/issues/145920) — 后台记忆审查被下一条消息取消，且永不重试。
- **P2 / 计费：** [#152288](https://github.com/NousResearch/hermes-agent/issues/152288) — OpenRouter 支出最高少报约 3 倍（模型级与端点定价）。
- **P3 / 平台：** [#152246](https://github.com/NousResearch/hermes-agent/issues/152246)（Windows 上 Bitwarden SSH agent 失败）、[#135139](https://github.com/NousResearch/hermes-agent/issues/135139)（非所有者 profile 下 WhatsApp LID→手机号解析）。
- **UI：** [#148946](https://github.com/NousResearch/hermes-agent/issues/148946)（转录图像重叠）、[#152279](https://github.com/NousResearch/hermes-agent/issues/152279)（Cmd+B 侧边栏契约）——严重程度低。

今日观察到的修复 PR 主要针对插件目录和一个审查预算边界情况；上述若干高影响 bug 在今日数据中**没有关联的修复 PR**。

## 6. 功能请求与路线图信号

- **[#153396](https://github.com/NousResearch/hermes-agent/issues/153396) Team Gateway 自带 Slack 应用** — 👍 信号强烈；很可能是近期 gateway 版本的候选功能。休眠/挂起与仅中继消息的交互，暗示了一个“自带 token”的身份功能。
- **[#153586](https://github.com/NousResearch/hermes-agent/issues/153586) `kanban_create` 阻塞原因/类型** — 小型 CLI/cron 增强，风险低；可能是快速见效项。
- **插件生态扩展** — hackathon 投稿（lumina 用于 Apple Photos、phone-senses 通过 Termux:API 用于 Android、twring 用于语音通话、habits、eselsbrücke/unblockbrücke/forgebrücke、ShapeRotator CAD）表明路线图正朝着**设备/外设与自托管集成**方向发展，而非仅核心功能。
- **多 profile 扩展**（由 #153727、#96027、#135139 暗示）— 更可能是重构/性能主题，而非单一功能。

## 7. 用户反馈总结

**痛点：**
- 常驻部署（Slack/Team Gateway、launchd、Docker）暴露了资源管理弱点（fd 泄漏、挂起语义、GIL 饥饿）。
- 桌面端可靠性回归（模型切换、审批卡片渲染、转录图像绘制）影响日常用户。
- arm64 上的沙箱/浏览器配置尽管是文档所述默认方案，仍然脆弱。
- 成本透明度：OpenRouter 支出报告不准确，削弱了对计费的信任。
- 平台特定摩擦：Windows SSH + Bitwarden agent、次级 profile 下的 WhatsApp 别名解析。

**满意信号：** 热情的插件贡献和 hackathon 参与表明贡献者社区健康；用户即使在提交安全报告时也对项目给予正面评价（“很棒的项目”“自进化概念很吸引人”）。今日未出现关于发布频繁变动的抱怨。

## 8. 积压关注

长期存在、高影响、值得维护者关注的事项：

- [#142488](https://github.com/NousResearch/hermes-agent/issues/142488) — 自 **2026-10-21** 起开放，无修复 PR；桌面端模型切换崩溃。
- [#131326](https://github.com/NousResearch/hermes-agent/issues/131326) — 自 **2026-10-02** 起开放；沙箱浏览器失效。
- [#96027](https://github.com/NousResearch/hermes-agent/issues/96027) — 自 **2026-08-27** 起开放；缓慢 fd 泄漏（需要持续负载复现）。
- [#94353](https://github.com/NousResearch/hermes-agent/issues/94353) — 自 **2026-08-25** 起开放；技能审批死锁（扫描器判定被视为硬失败，且无覆盖入口）。
- [#73748](https://github.com/NousResearch/hermes-agent/issues/73748) — 自 **2026-07-29** 起开放；提供商耗尽后 gateway 在途轮次丢失。

*数据说明：源数据中部分 PR 出现“Comments: undefined”；这些已从热门话题排名中排除。条目计数指截至 2026-11-12 的 24 小时窗口。*

</details>

<details>
<summary><strong>IronClaw</strong> — <a href="https://github.com/nearai/ironclaw">nearai/ironclaw</a></summary>

过去24小时无活动。

</details>

<details>
<summary><strong>QwenPaw</strong> — <a href="https://github.com/agentscope-ai/QwenPaw">agentscope-ai/QwenPaw</a></summary>

# QwenPaw 项目摘要 — 2026-11-12

## 1. 今日概览

在 v2.3.0 稳定版发布后，QwenPaw 正处于积极的稳定化阶段，过去 24 小时内有 33 个 PR 和 8 个 issue 得到更新。此次发布引入了 Piccolo 本地决策模型，但稳定版的安装验证（#8681）已超过 4 小时期限仍未关闭，这表明可能存在平台验证方面的摩擦。一组值得注意的渠道插件启动失败问题（Windows 上的飞书、Matrix 多工作区、Linux 上的微信）指向了新的基于插件的渠道架构中的系统性问题。积极的一面是，针对这些回归问题，今天已提交了若干针对性修复 PR，其中部分已经合并，表明维护者响应积极。整体活跃度很高，但稳定性信号喜忧参半：渠道插件迁移显然仍在成熟过程中。

## 2. 发布

**v2.3.0（稳定版）** — 已发布，安装验证 issue #8681 仍处于开放状态。
- **新增：** Piccolo 4B、9B 和 27B 作为本地决策模型，用于工具审查和上下文压缩，推荐使用 BF16 和 Q8_0。
- **迁移说明：** 支持运行时安装/更新，并可通过 Hugging Face 或 ModelScope 下载；用户需具备可用的本地推理运行时。
- **破坏性变更：** 发布说明中未明确记录，但渠道插件架构（在 2.3.0 beta 系列中引入）实际上是一项破坏性的运维变更——参见缺陷部分。
- 链接：[Release v2.3.0](https://github.com/agentscope-ai/QwenPaw/releases/tag/v2.3.0) · [Verification issue #8681](https://github.com/agentscope-ai/QwenPaw/issues/8681)

## 3. 项目进展

今日合并/关闭的 PR（共 8 个，值得关注的项目）：
- **[#8675](https://github.com/agentscope-ai/QwenPaw/pull/8675)**（已关闭）— 诊断渠道启动失败，并修复 Windows 飞书原生库加载问题（PyCryptodome 路径中带 `\\?\` 前缀 + `..` 导致 `CHANNEL_NOT_CONNECTED`）。直接解决了最受关注的开放缺陷 #8669。
- **[#8680](https://github.com/agentscope-ai/QwenPaw/pull/8680)**（已关闭）— 修复 Windows 桌面端飞书导入超时问题。
- **[#8677](https://github.com/agentscope-ai/QwenPaw/pull/8677)**（已关闭）— 将 macOS 发布构建超时延长至 90 分钟（CI 可靠性）。
- **[#8542](https://github.com/agentscope-ai/QwenPaw/pull/8542)**（已关闭）— v2.3.0 发布说明更新。

进行中的功能工作（推进中的开放 PR）：
- **插件抽取重构：** MCP 转为插件（[#8469](https://github.com/agentscope-ai/QwenPaw/pull/8469)）、第三方 agent 转为插件（[#8426](https://github.com/agentscope-ai/QwenPaw/pull/8426)）、本地模型管理转为插件（[#8499](https://github.com/agentscope-ai/QwenPaw/pull/8499)）——这是一次协调推进模块化核心的架构工作。
- **本地引擎技术栈（3 个堆叠 PR）：** llama.cpp GPU 控制 + 自动更新（[#8649](https://github.com/agentscope-ai/QwenPaw/pull/8649)）、托管 MLX（[#8647](https://github.com/agentscope-ai/QwenPaw/pull/8647)）、Console 控制（[#8648](https://github.com/agentscope-ai/QwenPaw/pull/8648)）。

## 4. 社区热点

- **[Issue #8669](https://github.com/agentscope-ai/QwenPaw/issues/8669)**（4 条评论）— 在打包的 Windows 桌面构建中，飞书渠道每次启动都失败；`_wait_connected` 将初始 `disconnected` 状态视为致命错误。**底层需求：** 使用捆绑桌面构建的用户需要确定性的渠道启动顺序，而非依赖竞态的行为。修复 PR（#8675）已存在。
- **[Issue #8538](https://github.com/agentscope-ai/QwenPaw/issues/8538)**（4 条评论）— 当多个工作区启用 Matrix 插件时失败；10 秒的共享启动窗口仅覆盖第一个工作区。**底层需求：** 多租户/多工作区用户需要按工作区分配的启动预算或并发激活能力。
- **[PR #8236](https://github.com/agentscope-ai/QwenPaw/pull/8236)** — 根据 `num_ctx` 而非默认 128k 来设置 Ollama 上下文窗口大小。**底层需求：** 本地模型用户因上下文发现假设了云端规模的窗口而遭遇截断。
- **[PR #8409](https://github.com/agentscope-ai/QwenPaw/pull/8409)** — QQ C2C 流式消息支持。**底层需求：** 在中国 IM 平台上实现流式 UX 的一致性。

## 5. 缺陷与稳定性

按严重程度排序：

1. **[#8616](https://github.com/agentscope-ai/QwenPaw/issues/8616) — 严重回归：** 2.3.0-beta.1 在 Windows 11 IoT LTSC 上无法启动（"path not found" → AppHang），而 2.2.2 在同一台机器上可正常工作。尚未确定修复 PR；对该操作系统配置而言属于发布阻断问题。
2. **[#8669](https://github.com/agentscope-ai/QwenPaw/issues/8669) — 高：** 在打包的桌面构建中，飞书渠道在*每次*启动时都失败（CHANNEL_NOT_CONNECTED）。**修复已存在：** [#8675](https://github.com/agentscope-ai/QwenPaw/pull/8675)（今日关闭/合并）和 [#8680](https://github.com/agentscope-ai/QwenPaw/pull/8680)。
3. **[#8682](https://github.com/agentscope-ai/QwenPaw/issues/8682) — 高：** 官方渠道插件 worker 在 Linux 上崩溃（`Could not determine home directory`）——启动器和 worker 引导均未提供 HOME/QWENPAW_WORKING_DIR。尚无修复 PR；影响微信渠道插件迁移。
4. **[#8538](https://github.com/agentscope-ai/QwenPaw/issues/8538) — 高：** Matrix 插件多工作区激活问题（10 秒窗口仅覆盖第一个工作区）。尚无修复 PR。
5. **[#8674](https://github.com/agentscope-ai/QwenPaw/issues/8674) — 中（资源泄漏）：** 查询错误诊断转储会写入完整对话快照，且没有大小/数量/时长/速率限制，导致临时目录增长至数百 MB。**修复 PR 已开放：** [#8678](https://github.com/agentscope-ai/QwenPaw/pull/8678)（限制转储大小和保留时间）。
6. **[#8663](https://github.com/agentscope-ai/QwenPaw/issues/8663) — 低/中（浏览器特定）：** Safari 不渲染消息队列操作按钮（编辑/中断并发送/删除）；Chrome 正常。尚无修复 PR。

**模式观察：** 排名前三的缺陷中有三个共享同一根因类别——新的渠道插件架构的启动/生命周期握手（环境变量传递、连接状态时序、按工作区窗口）。这是本周期的主要稳定性主题。

## 6. 功能请求与路线图信号

- **本地模型引擎成熟化：** 堆叠三件套 [#8649](https://github.com/agentscope-ai/QwenPaw/pull/8649)/[#8647](https://github.com/agentscope-ai/QwenPaw/pull/8647)/[#8648](https://github.com/agentscope-ai/QwenPaw/pull/8648)（llama.cpp GPU 控制、托管 MLX、Console UI）强烈表明下一个次要版本将以本地推理易用性为核心——可能落在 v2.4 系列。
- **渠道流式一致性：** QQ C2C 流式（[#8409](https://github.com/agentscope-ai/QwenPaw/pull/8409)）和钉钉 Markdown 表格渲染（[#8601](https://github.com/agentscope-ai/QwenPaw/pull/8601)）表明对 IM 平台输出保真度的持续投入。
- **响应完整性 UX：** [#8676](https://github.com/agentscope-ai/QwenPaw/pull/8676) 在达到输出限制时添加通知，并修复将空响应处理为虚假成功的问题——这是一项面向用户的信任改进，可能很快发布。
- **插件模块化：** 三个"抽取为插件"PR（#8426、#8469、#8499）预示着下一个主要周期将朝可配置/最小核心的发行方向演进。

## 7. 用户反馈摘要

- **痛点：** Windows 桌面打包可靠性是最突出的主题（飞书原生库加载、beta 启动挂起、导入超时）。从 2.2.x 升级到 2.3.x 的用户报告了渠道回归问题，尤其是在 Windows 和多工作区环境中。一份中文报告（#8674）突出了在真实企业级集成（"WorkMate"）中无限制的诊断转储问题，表明采用已超出爱好者使用的范畴。
- **观察到的使用场景：** 多工作区 Matrix 部署（#8538）、Telegram/飞书/微信/QQ/钉钉渠道运营者、本地 Ollama 和 llama.cpp 用户，以及因运行失败而放大诊断转储泄漏问题的定时任务用户。
- **满意度信号：** 详细、格式良好的缺陷报告附带复现步骤和版本矩阵，表明用户群体积极参与且具备技术素养；维护者针对飞书 Windows 问题的快速修复 PR 表明响应能力合理。不满主要集中在 2.3.0 的渠道插件迁移摩擦以及开放中的发布验证（#8681）错过截止期限。

## 8. 待办事项关注

- **[#8616](https://github.com/agentscope-ai/QwenPaw/issues/8616)**（创建于 11-07，3 条评论）— Windows 11 IoT LTSC 启动回归。严重程度最高，尚无关联修复 PR；需要维护者分诊，并为受影响用户提供平台特定的变通方案或回滚指导。
- **[#8538](https://github.com/agentscope-ai/QwenPaw/issues/8538)**（创建于 11-02，4 条评论）— 多工作区 Matrix 激活问题。已开放 10 天；需要对启动预算分配做出设计决策。
- **[#8682](https://github.com/agentscope-ai/QwenPaw/issues/8682)**（创建于 11-11，1 条评论）— Linux 渠道 worker HOME/环境变量引导问题。虽为新问题，但广泛影响官方插件 worker；需要在渠道插件迁移完成前修复。
- **[PR #8426](https://github.com/agentscope-ai/QwenPaw/pull/8426) 和 [#8469](https://github.com/agentscope-ai/QwenPaw/pull/8469)**（创建于 10-26/10-28，规模 XXXL）— 大型架构性插件抽取 PR 已开放 2 周以上且无可见审查意见；这些 PR 在本地引擎技术栈中具有合并顺序依赖关系，可能成为合并瓶颈。
- **[PR #8236](https://github.com/agentscope-ai/QwenPaw/pull/8236)**（创建于 10-14）— Ollama `num_ctx` 修复已审查近一个月；存在时间较长且与大量本地模型用户群体相关。

</details>

<details>
<summary><strong>ZeroClaw</strong> — <a href="https://github.com/zeroclaw-labs/zeroclaw">zeroclaw-labs/zeroclaw</a></summary>

# ZeroClaw 项目摘要 — 2026-11-12

## 1. 今日概览

ZeroClaw 正处于 v0.9.0 发布前夕的高吞吐维护阶段：今日未发布新版本，但 v0.9.0 候选版本（`1a79f89d1e`）显然正在趋于稳定，今日合并的翻译锁定和安全依赖升级即为佐证。Issue 活动较少（7 条更新，全部仍为打开状态），但集中在高严重级别的运行时/守护进程缺陷上，其中多条由同一位生产用户报告，表明真实部署环境正承受压力。PR 数量非常大（24 小时内涉及 50 个；46 个打开），以少数高产贡献者提交的长期 XL 特性分支为主，仅 4 个被合并/关闭。整体健康状况为**喜忧参半偏正面**：推进速度快，发布准备工作可见，但审查人力似乎是瓶颈——许多 PR 带有 `needs-author-action`/`needs-maintainer-review` 标签，且已开放数周。除一个已修补的安全公告外，无崩溃或安全事件。

## 2. 版本发布

**今日无新版本发布。** 不过，**v0.9.0** 的发布准备信号很强：
- PR #12380（已合并）将 `docs/book/po` 翻译目录锁定到 v0.9.0，为打标签发布准备文档构建。
- PR #12392（已合并）将 `rustix` 升级到 1.1.6，以修复 **RUSTSEC-2026-0486**（影响 1.0.0–1.1.5 的 xattr 读取漏洞）——该安全修复预计将进入下一个标签版本。

暂无破坏性变更或迁移说明需要报告。

## 3. 项目进展

过去 24 小时内仅合并/关闭了 4 个 PR：

- **[已合并] #12392** — `fix(deps): bump rustix to 1.1.6 for RUSTSEC-2026-0486`（[链接](https://github.com/zeroclaw-labs/zeroclaw/pull/12392)）。安全加固；在发布前关闭了一个已知安全公告。
- **[已合并] #12380** — `chore(docs): pin translations to v0.9.0`（[链接](https://github.com/zeroclaw-labs/zeroclaw/pull/12380)）。文档发布就绪。
- **[已关闭] #12215** — Dependabot rust-all 组（36 项更新），被 **#12393**（35 项更新）取代（[链接](https://github.com/zeroclaw-labs/zeroclaw/pull/12393)）。
- **[打开，有进展] #12395** — 对 shell 语法绑定行的文档更正，是安全敏感的 #11994 的前置条件（[链接](https://github.com/zeroclaw-labs/zeroclaw/pull/12395)）。

净结论：今日特性推进极少；可见工作主要是发布卫生和依赖/安全清理。大型特性分支（#11847 XMPP、#9809 多模型提供方、#10391 委托加固）仍处于打开且未合并状态。

## 4. 社区热点

活动以评论数衡量；反应计数一律为零，这一点值得注意（尽管 Issue 数量高，社区 👍 互动却很低）。

- **[#7461](https://github.com/zeroclaw-labs/zeroclaw/issues/7461)** — *在 CI 中于 Windows 和 macOS 上运行测试套件，而不仅是 Linux*（6 条评论，进行中，风险：高）。运行时间最长的活跃 Issue（自六月起打开）。潜在需求：**跨平台正确性保证**——Lint/Build 已在三操作系统矩阵上运行，但测试没有，因此 Windows/macOS 回归可能漏过。鉴于今日报告的多个 Windows 专属缺陷（#12391），这现已被证实为真实缺口。
- **[#9814](https://github.com/zeroclaw-labs/zeroclaw/issues/9814)** — *原生 XMPP / Prosody 频道*（4 条评论，已接受，parking-lot）。需求：面向希望使用轻量聊天后端而非 Matrix/Telegram 的家庭实验室用户的**自托管、低资源部署**。配有打开的实现 PR #11847。
- **[#8794](https://github.com/zeroclaw-labs/zeroclaw/issues/8794)** — *在工作进行中停止代理会从上下文中抹除工具调用和思考内容*（4 条评论，P1，S1）。需求：**可靠的中断语义**——被中止的轮次应保留部分推理/工具历史，而非静默丢弃。

讨论最多的 PR 均来自同一位高产作者（Audacity88）：#12258（`sessions_prompt` 代理间工具）、#11730（前台委托工具调用可见性）、#11698（用于提示缓存的稳定人格文件）。反复出现的主题是：**多代理编排和提示缓存效率**是社区的主导工程兴趣。

## 5. 缺陷与稳定性

按严重程度排序，均为今日报告/更新：

1. **[#8794](https://github.com/zeroclaw-labs/zeroclaw/issues/8794) — S1，工作流阻塞，P1。** 在工作进行中停止代理会丢弃上下文中的所有工具调用和思考内容，仿佛该步骤从未发生。用户影响大；尚无关联修复 PR。
2. **[#12391](https://github.com/zeroclaw-labs/zeroclaw/issues/12391) — S2，行为降级。** `runtime-trace.jsonl` 在守护进程重启期间丢失**数小时**的事件（交接缓冲区/队列丢弃/静默写入器）。在生产 **Windows 服务，0.8.7** 上复现。可观测性/审计完整性风险；尚无关联修复 PR。
3. **[#12396](https://github.com/zeroclaw-labs/zeroclaw/issues/12396) — 配置正确性。** `history_pruning.keep_recent` 和 `collapse_tool_results` 是**无效配置项**（自 #8196 起无读取方）。静默错误配置——用户以为在调整记忆行为，实则不然。尚无关联修复 PR。
4. **[#12390](https://github.com/zeroclaw-labs/zeroclaw/issues/12390) — 诊断质量。** `shell` 工具在非零退出时返回**空错误字符串**；stderr 被吞掉，模型只能得知“某处失败了”。直接削弱代理的自我纠正能力。版本 0.8.7。

观察：四个缺陷中的三个（#12391、#12396、#12390）加上 S2 追踪缺口，均来自 0.8.7 上的少数报告者，且都位于运行时/守护进程/shell 路径——这一簇值得集中分诊。仅 #8794 有明确的严重级别升级。

## 6. 特性请求与路线图信号

- **XMPP/Prosody 原生频道**（[#9814](https://github.com/zeroclaw-labs/zeroclaw/issues/9814)，已接受，parking-lot）——实现 PR #11847 处于打开状态且今日有更新。鉴于 parking-lot 状态和 XL 规模，**可能面向 v0.10+**，而非 v0.9.0。
- **Windows/macOS CI 测试矩阵**（[#7461](https://github.com/zeroclaw-labs/zeroclaw/issues/7461)，进行中）——范围小、杠杆高；可能进入 **v0.9.x**。
- **SOP 步骤代理的工具上限封禁**（[#12394](https://github.com/zeroclaw-labs/zeroclaw/issues/12394)）——与安全相关的委托 MCP 工具加固；可能与 PR #10391 中更广泛的委托策略工作相关。
- **代理间会话提示**（[#12258](https://github.com/zeroclaw-labs/zeroclaw/pull/12258)）和**异步 `sessions_message` 脚手架**（[#12259](https://github.com/zeroclaw-labs/zeroclaw/pull/12259)）——协调一致的多代理消息方向（RFC #11027 / 跟踪器 #11119）。
- **每个提供方配置支持多个模型**（[#9809](https://github.com/zeroclaw-labs/zeroclaw/pull/9809)）——大型 schema-v4 重构；若合并，将是需关注迁移说明的重大配置面变更。

**预测：** v0.9.0 可能随已合并的文档锁定、rustix 安全修复及各类小修复一同发布；较大项（XMPP、多模型提供方、会话工具）因 `needs-author-action`/审查积压而推迟到 v0.10。

## 7. 用户反馈总结

- **痛点：中断时工作丢失**（#8794）——用户期望部分思考/工具输出在停止后保留；其丢失使长时间代理运行显得不可靠。
- **痛点：生产环境可观测性缺口**（#12391）——一位真实的 Windows 服务运维者报告可证明仍在继续的工作却未留下任何痕迹，削弱了调试和审计能力。
- **痛点：静默错误配置**（#12396）——文档化却毫无作用的配置项侵蚀了对配置文档的信任。
- **痛点：不透明的 shell 失败**（#12390）——代理无法自行诊断命令失败，被迫人工干预。
- **需求：自托管/低资源部署**（#9814）——家庭实验室用户想要轻量聊天传输。
- **满意信号：** 对 CI 健壮性（#7461）和提示缓存稳定性（#11698）的持续投入表明活跃的高级用户关心性能和跨平台一致性。**不满信号：** 一天之内在 0.8.7 上浮现三类不同的静默失败，外加一个自七月起未解决的 P1。

## 8. 积压关注

长期开放、需要维护者关注的重要事项：

- **[#7461](https://github.com/zeroclaw-labs/zeroclaw/issues/7461)** — Windows/macOS CI 测试；自 **2026-06-10** 打开（约 5 个月），进行中。与今日的 Windows 缺陷 #12391 直接相关。
- **[#8794](https://github.com/zeroclaw-labs/zeroclaw/issues/8794)** — P1/S1 工作中上下文抹除；自 **2026-07-07** 打开。最高严重级别的打开缺陷，无关联修复 PR。
- **[#9814](https://github.com/zeroclaw-labs/zeroclaw/issues/9814)** — XMPP 频道；自 **2026-08-07** 打开，parking-lot；实现 #11847 停滞于 needs-maintainer-review。
- **[#9809](https://github.com/zeroclaw-labs/zeroclaw/pull/9809)** — 每个提供方多模型配置；自 **2026-08-07** 打开，XL，`needs-author-action`——高价值但似乎停滞。
- **[#10391](https://github.com/zeroclaw-labs/zeroclaw/pull/10391)** — 有界委托工作区/工具上限/命令策略修复；自 **2026-08-26** 打开，`needs-author-action`，风险：高。与安全相关且多次变基——一旦审查通过是强有力的合并候选。
- **[#11730](https://github.com/zeroclaw-labs/zeroclaw/pull/11730) / [#11698](https://github.com/zeroclaw-labs/zeroclaw/pull/11698)** — 委托工具调用可见性和提示缓存稳定性，自十月中旬起均为 `needs-author-action`。

**健康提示：** 约束因素是审查人力，而非贡献者兴趣。清理 `needs-author-action` 队列（尤其是 #10391 和 #9809）并对四个运行时缺陷进行分诊，将实质性地改善项目进入 v0.9.0 时的健康状况。

</details>

---
*本日报由 [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar) 自动生成。*