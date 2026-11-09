# AI CLI 工具社区动态日报 2026-11-10

> 生成时间: 2026-11-09 23:19 UTC | 覆盖工具: 7 个

- [Claude Code](https://github.com/anthropics/claude-code)
- [OpenAI Codex](https://github.com/openai/codex)
- [Gemini CLI](https://github.com/google-gemini/gemini-cli)
- [GitHub Copilot CLI](https://github.com/github/copilot-cli)
- [OpenCode](https://github.com/anomalyco/opencode)
- [Pi](https://github.com/earendil-works/pi)
- [Qwen Code](https://github.com/QwenLM/qwen-code)
- [Claude Code Skills](https://github.com/anthropics/skills)

---

## 横向对比

# 跨工具对比报告：AI CLI 工具生态
**报告日期：2026-11-10 | 覆盖工具：Claude Code、OpenAI Codex、Gemini CLI、GitHub Copilot CLI、OpenCode、Pi、Qwen Code**

---

## 1. 生态概览

2026 年末的 AI CLI 生态正收敛到一套共享架构——智能体循环、子智能体、基于 MCP 的工具链、沙箱化执行，以及多端部署（CLI、TUI、桌面、移动端、Web）——差异化主要体现在模型访问、成本结构和企业治理上。发版节奏依然激进（Claude Code v2.1.321、Copilot CLI v1.0.109-0、Qwen Code v0.27.1 均在本窗口内发布），但**可靠性与成本信任已成为社区的首要关切**，而非原始能力。在全部七个跟踪源中，互动量最高的帖子都围绕配额核算、静默失败和沙箱引发的摩擦，而非缺失的功能。两个结构性趋势尤为突出：第一，**子智能体/压缩的正确性**已成为智能体工作流中最薄弱的环节；第二，**审批与可观测性的覆盖面**正从桌面扩展到手机、手表、任务栏和 OSC 7501 终端状态。

---

## 2. 活跃度对比

| 工具 | Issue 活跃度 | PR 活跃度 | Discussions | 发版状态（近 24 小时） |
|---|---|---|---|---|
| **Claude Code** | 10 个热门 issue；1 个过期/重复关闭较为突出 | 1 个 PR 更新（#89404）——自托管插件修复 | N/A（issue tracker 是主要渠道） | ✅ **v2.1.321** —— Fable 5.2 默认、1M 上下文、低成本缓存读取、`--settings` |
| **OpenAI Codex** | 10 个热门 issue；#41220 配额跟踪帖（84 条评论）居首 | 10 个 PR —— 热修复回填、认证/限流正确性 | ✅ 活跃（9 条 show-and-tell / 问答） | ✅ **2 个 alpha 构建**（0.169.0-alpha.6/.7）；稳定线 0.167.0 带热修复回填 |
| **Gemini CLI** | 50 个 issue 更新；子智能体可靠性问题集群（P1） | 44 个 PR —— P0 构建修复、安全、超时 | 未提供数据 | ❌ **无发版** |
| **GitHub Copilot CLI** | 29 个 issue 更新；MCP 命名空间回归（当日关闭） | 窗口内 **0 个 PR** | N/A（无数据） | ✅ **v1.0.109-0** 和 **v1.0.108** |
| **OpenCode** | 50 个 issue 更新；免费层巨型帖（339 条评论） | 50 个 PR —— PTY 协议栈、配置韧性 | 未提供数据 | ❌ **无发版** |
| **Pi** | 约 10 个热门 issue；Windows sink 线程 #7547（95 条评论） | 10 个 PR —— Windows/PowerShell 修复、Fable 5.2 兼容 | 无专项数据（讨论见 #7547） | ❌ **无发版**（v1.5.0 回归仍在分诊中） |
| **Qwen Code** | 约 10 个热门 issue；CI 门禁 #14724（282 条评论） | 10 个 PR —— 统一智能体循环、channels/QR 登录 | N/A（无数据） | ✅ **v0.27.1** + SDK 0.1.31 + 桌面端 0.27.1 |

**说明：** Claude Code 与 Copilot CLI 以 issue tracker 作为主要社区论坛——Discussions 一栏标记为 N/A。OpenCode 与 Gemini CLI 的原始 issue/PR 吞吐量最高。Copilot CLI 零 PR 窗口值得注意，因为它发布了两个版本——工作通过内部流水线落地，而非公开 PR。

---

## 3. 共性功能方向

在多个社区中同时出现的需求，按需求广度排序：

| 方向 | 提出工具 | 具体需求 |
|---|---|---|
| **成本/配额透明与可控** | Claude Code（#97074 headless 1.8× 开销）、Codex（#41220 配额跟踪、#60435 限流 UI）、OpenCode（#59387 免费层帖、#59861 目录价估算）、Copilot CLI（#5392 提示计量翻倍） | 分模式 token 核算、重置计时器可靠性、可选成本估算、预算感知的智能体行为 |
| **上下文效率与压缩正确性** | Claude Code（原生 Edit 替代 bash）、Codex（#55990 进度感知预算、#60297 BCC 插件）、Gemini CLI（grep 上限 PR #30109、AST 感知 EPIC #22745）、OpenCode（#43250 预算超支、#50814 工具调用被当作文本） | AST 感知读取、输出截断、安全的压缩语义、按智能体选择性退出上下文 |
| **远程/带外审批与监控** | Codex（#33528 watchOS、#60116 手机、#60441 Apple Watch）、Claude Code（#92145 移动端远程审批）、Copilot CLI（#5320 OSC 7501、#2616 系统通知）、OpenCode（#60411 OSC 7501） | 手机/手表审批、终端原生状态上报、长任务推送通知 |
| **沙箱可用性与诊断清晰度** | Codex（#41779 "被策略阻止"无日志、#60409 未命名目标）、Gemini CLI（#19873 沙箱 + 意图路由）、Copilot CLI（#5390 回环拒绝破坏 Gradle、#5388 凭据遮蔽） | 具名阻止原因、回环/localhost 白名单、在企业租户上有效的凭据遮蔽 |
| **大规模下的 MCP 健壮性** | Codex（#60383 16 MiB 静默丢弃、#60423 延迟服务器发现）、Gemini CLI（#24246 >128 工具导致请求失败）、Copilot CLI（#5400 命名空间回归）、OpenCode（#62973 区分失败与未配置） | 大结果报错而非挂起、工具作用域/选择、延迟服务器的可查询性 |
| **多工具/多厂商互操作** | Codex（#60185 跨 Codex+Claude Code 会话记录搜索、#58834 worktree 共存）、Claude Code（多账号 #18435） | 共享会话记录搜索、按智能体隔离 worktree、无需重新认证的配置切换 |
| **Windows 一致性** | Pi（整个 #7547 线程——95 条评论）、Codex（项目侧边栏丢失、云聊天恢复）、Copilot CLI（PowerShell 挂起、Git Bash）、Gemini CLI（TortoiseGitPlink） | 路径分隔符 glob、PS 5.1 stderr 处理、UTF-16 BOM 文件、shell 检测 |

---

## 4. 差异化分析

**Claude Code —— 前沿模型 + 成本敏感的企业自动化。**
面向在 Anthropic 模型上运行高吞吐 headless 流水线的开发者。差异化：紧密的模型集成（Fable 5.2 默认，配合激进的缓存定价）和非常深的插件/技能体系。其痛点（headless 配额开销、`includeCoAuthoredBy` 不合规、多账号）揭示了一个正从交互式使用迁移到*生产自动化*的用户群——该工具正被推到其交互式设计中心之外。

**OpenAI Codex —— 供应商多元性 + 发版加固。**
alpha 渠道的节奏和以回填驱动的稳定化（Bedrock/Mantle 供应商兼容、SQLite WAL 安全修复）显示团队优先考虑*部署广度*——企业网关、自定义供应商、桌面端发布。弱点：Windows 桌面端回归和沙箱诊断占据 tracker 主导，表明这是一种广覆盖、单面 QA 较薄的权衡。

**Gemini CLI —— 智能体架构研究，shell 优先的人体工学。**
主导信号（#19873 零依赖沙箱与意图路由、#22745 AST 感知工具链、统一委派）指向一个*架构先行*的工具，希望模型原生但安全地使用 bash。P1 子智能体正确性负载最高（#22323、#21409），说明委派模型是当前的工程前沿。同时也是最易受发布工程脆弱性影响的（nightly 构建中断约一周，PR #30207）。

**GitHub Copilot CLI —— 企业治理与原生平台集成。**
发版说明读起来像安全/合规变更日志：符号链接确认、会话级子域授权、GHE 数据驻留、Entra/租户认证、Kerberos 代理。目标用户是*企业平台团队*，而非单打独斗的开发者。公开 PR 的缺席和内部 statuspage issue（#5185）的出现，表明存在严格门控的发布流程——一致性优先于速度。

**OpenCode —— 供应商无关、自托管的基础设施路线。**
v2 服务架构（进程守护、PTY 协议 v8、托管服务端口）和供应商矩阵（LM Studio、Claude Agent、EVREN、无 prefill 模型）表明这是一个押注*底层管道*的工具——本地守护进程持久性、多供应商一致性——而非前沿模型访问。免费层巨型帖（339 条评论）暴露了开源工具附带托管服务时的变现张力。

**Pi —— Windows 优先的务实主义与嵌入式/持久化用例。**
两项明确承诺：（1）让 Windows 成为一等公民（95 条评论的帖子，以及 `petrroll` 系统性的 PR 批次修复 PS 5.1、UTF-16 BOM、mintty、glob 分隔符），以及（2）将 `pi-durable` 作为可嵌入的测试框架（#11676、#11961）——CLI 与 SDK 之间的细分定位。1.5.0 回归集群（#11869 P0 键入中断、#11892 任务卡死）是快速发布的信任代价。

**Qwen Code —— 托管智能体平台扩张（channels、web shell、统一循环）。**
本组中最清晰的*平台*轨迹：channels（微信/QQ 扫码登录）、webhooks、Web Shell、托管会话、跨 CLI/TUI/ACP/daemon 的统一智能体循环（#15327）。目标用户是将智能体嵌入聊天平台的*集成开发者*。其负担是自找的规模——CI 门禁失败和主分支中断（#16712）是表面积快速扩张的税。

**总结轴：** Claude Code/Codex 在*模型+供应商能力*上竞争；Gemini CLI/Qwen Code 在*智能体架构*上竞争；Copilot CLI 在*企业信任*上竞争；OpenCode/Pi 在*开放性、可移植性与持久性*上竞争。

---

## 5. 社区动能与成熟度

**动能（速度 + 互动强度）：**
- **OpenCode 与 Gemini CLI 在原始吞吐量上领先**（各自单日 50 个 issue + 50 个 PR）。OpenCode 339 条评论的帖子和维护者主导的 PTY 栈显示核心团队高度活跃；Gemini CLI 的 44 个 PR 和 P0 构建修复显示分诊响应及时。
- **Qwen Code 在产品表面积上迭代最快**——三次协同发布加上 channel/web-shell PR，但代价是 CI 不稳定（#14724 的 282 条评论是噪音，不是信号）。
- **Claude Code 的单 issue 信号质量最高**——#18435（1,025 👍）是本窗口全部跟踪源中点赞最多的请求，表明一个庞大且有主见的用户群，其头号痛点（多账号）是工作流阻塞，而非愿望清单。
- **Codex 与 Pi 呈现集中、主题化的互动**——Codex 的配额跟踪帖和 Pi 的 Windows 汇总帖是各自吸收数十名报告者的单一帖子，是成熟分诊合并重复项的迹象。

**成熟度（稳定性、流程纪律）：**
- **流程最成熟：** GitHub Copilot CLI（门控公开 PR、当日关闭回归如 #5400、以安全为先的发版说明）和 Qwen Code（多包协同发布并附回填说明）。
- **发版卫生最不成熟：** Pi（1.5.0 在键入与持久性上的回归）和 Gemini CLI（因过期构建产物导致 nightly 中断长达一周）。
- **成本信任暴露最高：** OpenCode（免费层配额状态两周未解决）和 Codex（#41220 计费不信任，84 条评论）——二者的变现/声誉风险都与其工程产出不成比例。

**快速迭代排名（从多到少）：** OpenCode ≈ Gemini CLI > Qwen Code > Codex > Claude Code > Pi > Copilot CLI（按公开节奏；Copilot 的速度被流程隐藏）。

---

## 6. 趋势信号

来自本窗口社区反馈的行业级信号，及其对开发者的相关性：

1. **成本信任是新的 UX 战场。** headless 与交互式的 token 不对称（Claude Code #97074）、计量翻倍（#5392）、配额帖（OpenCode #59387、Codex #41220）——*如果你在做自动化，请在扩容前为按入口核算做预算，并验证计量一致性*。预计一等公民的预算 API 将成为采购标准。

2. **压缩是正确性前沿。** 压缩后工具调用被当作文本（OpenCode #50814）和无界回退（#43250）会*静默*失败。*在任何长时运行的智能体部署中，把压缩后正确性当作可测试的契约*；要求工具调用分发的完整性检查。

3. **静默失败是最具破坏性的 bug 类别。** 16 MiB MCP 丢弃（#60383）、glob 返回空（#9262）、tool_search 假阴性（#11957）、"未识别到测试"的 CI 门禁（#14724）。*为快速、显式的错误做埋点*；在智能体框架中宁可大声失败，不要安静失败。

4. **审批/可观测性正走向多端。** 手机、手表、任务栏、OSC 7501、系统通知——离开键盘监督智能体现已成为基本预期。*设计钩子（如 `hooks.json`、ACP）时，将带外审批作为一等路径。*

5. **MCP 从"已支持"转向"大规模下健壮"。** 延迟服务器发现、超过约 128 的工具上限、命名空间保留、大结果处理。*MCP 开发者应测试极限/失败模式，而不只是顺利路径。*

6. **Windows 与企业认证是真正的一致性缺口。** Pi 的 95 条评论 Windows 帖、Codex 的 Windows 桌面端回归、Copilot 的 Entra/租户/代理摩擦。*如果你的工具面向企业开发者，Windows + 单租户身份 + 企业代理是采用门槛，而非边缘情况。*

7. **子智能体可靠性侵蚀了对"成功"的信任。** 轮次上限退出时误报成功（#22323）、挂起（#21409）、虚假完成（Claude Code #92151）。*记录并断言真实的完成语义，而非状态字符串——并将短/长任务区分作为安全控制。*

8. **多厂商、多智能体工作流正走向主流。** 跨会话记录搜索（#60185）、worktree 共存（#58834）、多账号切换（#18435）。*预计用户会并行运行 2–3 个 CLI 智能体；互操作性与隔离（worktree、按智能体内存预算、配置切换）正成为差异化因素。*

**给决策者的底线：** 能力不再是瓶颈——**成本可预测性、失败诚实性和多端治理**才是。为生产环境评估 AI CLI 时，请将 headless 配额行为、压缩/正确性语义、沙箱诊断质量和企业身份支持的权重置于原始模型基准之上。

---

*数据基础：截至 2026-11-10 各仓库的社区摘要（Claude Code、OpenAI Codex、Gemini CLI、GitHub Copilot CLI、OpenCode、Pi、Qwen Code）。Issue/PR 计数反映报告窗口内更新的条目；互动数字为源快照中所报告的值。*

---

## 各工具详细报告

<details>
<summary><strong>Claude Code</strong> — <a href="https://github.com/anthropics/claude-code">anthropics/claude-code</a></summary>

## Claude Code Skills 社区热点

> 数据来源: [anthropics/skills](https://github.com/anthropics/skills)

# Claude Code Skills 社区亮点报告
**数据截至 2026-11-10 | 来源：github.com/anthropics/skills**

---

## 1. 热门 Skills 排名

> 注：PR 数据集中所有条目的 `Comments` 均显示为 `undefined`，因此以下排名基于可用信号（关注度/时效性/影响范围）及所列热门 PR 得出。评论数不可用之处标注为“N/A”。

| 排名 | Skill / PR | 功能 | 讨论亮点 | 状态 |
|---|---|---|---|---|
| 1 | [**mcp-builder 修复** #1742](https://github.com/anthropics/skills/pull/1742) | 修复 `mcp>=2.0.0` 下的 MCP builder——API 重命名（`streamablehttp_client` → `streamable_http_client`）以及通过 `create_mcp_http_client` 处理自定义 header。 | 与 MCP v2 的兼容性断裂；阻塞了 skill 中的 MCP server 脚手架搭建。 | **OPEN**（评论数 N/A） |
| 2 | [**skill-creator 修复** #1298](https://github.com/anthropics/skills/pull/1298) | 隔离触发评估；修复 Windows 下 subprocess `select()` 失败以及无关工具扫描噪声导致的假阴性评分。 | 核心工具链可靠性；跨平台正确性。 | **OPEN**（评论数 N/A） |
| 3 | [**notion-spec-to-implementation + quantitative-resume-auditor** #1245](https://github.com/anthropics/skills/pull/1245) | 将 Notion 产品/技术规格转化为带验收标准的可执行编码任务；另附一个简历审计 skill。 | 新的工作流自动化 skill 组合；生命周期长（6 月→10 月）。 | **OPEN**（评论数 N/A） |
| 4 | [**webapp-testing：移除 shell=True** #1980](https://github.com/anthropics/skills/pull/1980) | 消除 `with_server.py` 中的 `shell=True`，修复 CWE-78 命令注入风险。 | 对广泛使用的测试 skill 进行安全加固。 | **OPEN**（评论数 N/A） |
| 5 | [**docx 超时 + 输出校验** #1792](https://github.com/anthropics/skills/pull/1792) | 将 LibreOffice 超时报告为错误；在声称成功之前校验修订标记已被移除。 | 文档流水线中的正确性/静默失败修复。 | **OPEN**（评论数 N/A） |
| 6 | [**proofcore-contract-auditor** #1771](https://github.com/anthropics/skills/pull/1771) | 对 Solidity/Rust 合约进行静态分析，并将审计证明锚定上链（TON）。 | Web3 细分 skill；新颖的“skill + 链上公证”模式。 | **OPEN**（评论数 N/A） |
| 7 | [**pyxel skill** #525](https://github.com/anthropics/skills/pull/525) | 用 Python 进行复古游戏开发——无头输入驱动运行、帧检查、状态校验。 | 长期存在（3 月→11 月）；创意/游戏用例。 | **OPEN**（评论数 N/A） |
| 8 | [**md2video-audio** #1703](https://github.com/anthropics/skills/pull/1703) | Markdown → MP4，带逼真配音（Marp 幻灯片 + TTS）。 | “零成本”文档转视频自动化。 | **OPEN**（评论数 N/A） |

---

## 2. 社区需求趋势

提炼自评论最多的 Issues：

- **Skills 供应链的信任与安全** —— [Issue #492](https://github.com/anthropics/skills/issues/492)（46 条评论）：社区 skill 冒充 `anthropic/` 命名空间；呼吁来源/命名空间管控与权限护栏。
- **组织级 skill 共享与分发** —— [Issue #228](https://github.com/anthropics/skills/issues/228)（17 条评论）：用户希望有共享 skill 库/直接分享链接，而非手动上传 `.skill` 文件。
- **可靠的 skill 评估 / 触发测试** —— [Issue #556](https://github.com/anthropics/skills/issues/556)（13 条评论）与 [Issue #1352](https://github.com/anthropics/skills/issues/1352)（5 条评论）：`run_eval.py` 触发率为 0%，且并行 worker 下 UUID 交叉匹配——对 **skill 测试/QA 工具** 需求强烈。
- **上下文/内存效率** —— [Issue #1487](https://github.com/anthropics/skills/issues/1487)：`claude-api` 急切注入约 156k tokens；另有 [Issue #1329](https://github.com/anthropics/skills/issues/1329) 提议 `compact-memory`（用符号记法压缩 agent 状态）。需求：**token 高效的 skill 设计与内存压缩**。
- **Agent 安全与治理** —— [Issue #412](https://github.com/anthropics/skills/issues/412)（已关闭）：治理模式（策略执行、威胁检测、审计追踪）；相关 [Issue #1385](https://github.com/anthropics/skills/issues/1385)：推理质量门流水线（校准 → 对抗性评审 → 交付校验）。
- **Skill 编写质量** —— [Issue #202](https://github.com/anthropics/skills/issues/202) 与 [Issue #2064](https://github.com/anthropics/skills/issues/2064)：描述应说明*何时*使用 skill，而非仅说明*做什么*；skill-creator 应遵循 token 效率最佳实践。
- **插件卫生** —— [Issue #189](https://github.com/anthropics/skills/issues/189)（9 👍）：`document-skills` 与 `example-skills` 安装相同内容 → 重复 skill 消耗上下文。

**净方向：** 需求正从*增加更多 skill* 转向 **Skills 平台自身的安全、分发、测试与上下文效率**。

---

## 3. 高潜力待合并 Skills

活跃 PR（近期有更新、尚未合并），有望很快落地：

| PR | Skill | 为何接近合并 | 最后更新 |
|---|---|---|---|
| [#1977](https://github.com/anthropics/skills/pull/1977) | `algorithmic-art`：修复负数下 `wrapAround()` 取模（Fixes #1897） | 由重复贡献者提交的小型针对性 bugfix | 2026-10-18 |
| [#1976](https://github.com/anthropics/skills/pull/1976) | `webapp-testing`：修正 textarea/select 上报（Fixes #1891） | 范围极小，已关联 issue | 2026-10-18 |
| [#1975](https://github.com/anthropics/skills/pull/1975) | `pptx`：提前拒绝 `--cols < 1`（Fixes #1809） | 简单的校验修复 | 2026-10-27 |
| [#2019](https://github.com/anthropics/skills/pull/2019) | `xlsx`：在 `external_links_at_risk` 中解析工作表作用域的定义名称（Fixes #2013） | 范围明确的正确性修复 | 2026-11-03 |
| [#1681](https://github.com/anthropics/skills/pull/1681) | `skill-creator`：支持直接执行 `package_skill.py` | 修复真实的 `ModuleNotFoundError` | 2026-10-24 |
| [#1997](https://github.com/anthropics/skills/pull/1997) | `skill-creator`：修正 YAML frontmatter 标量解析 | 针对评估/描述优化的数据完整性修复 | 2026-10-19 |
| [#1730](https://github.com/anthropics/skills/pull/1730) | `claude-api`：替换 academy-guide 与 tool-use-concepts 中的失效 URL | 已验证 HTTP 200；仅文档改动 | 2026-10-04 |
| [#1245](https://github.com/anthropics/skills/pull/1245) | `notion-spec-to-implementation` + `quantitative-resume-auditor` | 长期活跃、体量可观的新 skill | 2026-10-15 |

**观察名单（更大/更高风险）：** [#1742](https://github.com/anthropics/skills/pull/1742)（MCP v2 兼容）、[#1980](https://github.com/anthropics/skills/pull/1980)（安全）、[#1771](https://github.com/anthropics/skills/pull/1771)（Web3）、[#1615](https://github.com/anthropics/skills/pull/1615)（`scnet-hpc`）。

---

## 4. Skills 生态洞察

**一句话总结：** 社区最集中的需求已从*编写新 Skill* 转向 **加固 Skills 平台自身——信任/来源、可靠评估与上下文效率**——最高评论数的 issues 集中于命名空间冒充（#492）、skill 共享基础设施（#228）以及损坏的触发评估工具链（#556、#1352），即为明证。

---

*方法论说明：源数据中 PR 评论数报告为 `undefined`；因此 PR 排名反映所提供的前 20 关注度排序、更新时效性与影响范围。Issue 排名使用实际评论数与 👍 反应数。*

---

# Claude Code 社区文摘 — 2026-11-10

## 今日亮点

Anthropic 发布了 **v2.1.321**，引入 **Claude Fable 5.2** 作为新的默认模型，具备 1M token 上下文窗口和显著更低的缓存读取费用（$0.20/Mtok），同时为远程控制会话新增了 `--settings` 支持。与此同时，问题追踪器仍被**成本效率担忧**所主导——无头模式 `claude -p` 的使用消耗约为交互模式 5 小时窗口的 1.8 倍——以及一个**酝酿已久的**多账户管理请求（#18435），该请求目前已累积超过 1,000 个 👍 和 237 条评论，成为近期记忆中获得最多投票的开放请求。

---

## 版本发布

**v2.1.321**
- **Claude Fable 5.2**（`claude-fable-5-2`）现已成为默认 Fable 模型：1M 上下文，$8/$40 每 Mtok，缓存读取 **$0.20/Mtok**——对缓存密集型工作负载而言是显著的成本降低。
- 为 `claude remote-control` 新增了 **`--settings <file>`** 标志，将设置文件应用于其生成的每个会话。需要注意的是，如果设置文件发生变更，**不会有新会话拾取该变更**（这一有文档记录的行为值得自动化流水线注意）。

---

## 热门 Issue

1. **[#18435](https://github.com/anthropics/claude-code/issues/18435) — Claude Desktop 中的多账户切换**（237 条评论，👍1025）
   以巨大优势成为得票最高的请求。运行多个 Claude 账户（工作/个人/客户）的智能体和开发者希望在不反复重新认证的情况下切换配置文件。如此高的参与量表明这是一个真实的工作流阻碍，而非锦上添花的功能。

2. **[#97074](https://github.com/anthropics/claude-code/issues/97074) — 无头模式 `claude -p` 每 token 消耗约 1.8 倍的 5 小时窗口**（73 条评论，👍159）
   对于任何在 Claude Code 之上构建 CI/CD 或自动化的人来说，这是一个关键发现：据报道，`sdk-cli` 入口点在相同工作负载下每 token 消耗的配额几乎是交互式 `cli` 的两倍。对大规模运行无头流水线的团队影响巨大。

3. **[#89673](https://github.com/anthropics/claude-code/issues/89673) — 桌面端：Enter 插入换行，Ctrl+Enter 提交**（23 条评论，👍95）
   一个按键绑定人体工学请求，反映了开发者实际编写提示词的方式（多行优先）。社区反应表明当前 Enter 提交的默认行为会导致消息过早发送。

4. **[#87971](https://github.com/anthropics/claude-code/issues/87971) — Claude 在自动模式下滥用 bash 进行读/写/编辑**（19 条评论，👍99）
   Windows/VS Code 用户报告 Claude 偏好 bash heredoc 而非原生文件工具，触发自动分类器并浪费轮次。这与追踪器其他地方出现的原生 Edit 工具请求相关。

5. **[#63751](https://github.com/anthropics/claude-code/issues/63751) — AUP/网络安全防护误报污染整个会话**（18 条评论）
   合法的自有软件加固工作触发安全分类器，而一次误报就会**毒化会话的剩余部分**。对安全工程师而言是一个严重的信任/可用性问题。

6. **[#65051](https://github.com/anthropics/claude-code/issues/65051) — 后台会话从转录中丢失文本块（回归）**（17 条评论）
   从 2.1.160 → 2.1.161 的回归，守护进程会话在响应混合文本与 `tool_use` 时丢失助手文本。影响长时间运行后台任务的可观测性。

7. **[#98898](https://github.com/anthropics/claude-code/issues/98898) — 通过 Skill 工具调用时 Skill frontmatter 的 `model:`/`effort:` 被忽略**（4 条评论）
   Skill 在 frontmatter 中声明其 model/effort，但通过 Skill 工具调用时这些被忽略（仅在 `/skill-name` 时被遵守）。这一点微妙但对任何标准化 Skill 行为的人都很重要。

8. **[#106822](https://github.com/anthropics/claude-code/issues/106822) — MCP 缺少命令错误误报 `stdio` 来源**（3 条评论）
   动态和插件 MCP 服务器的诊断将缺失的可执行文件误识别为 `stdio`，而用户作用域服务器报告正确。对插件作者而言是一个调试质量问题。

9. **[#102266](https://github.com/anthropics/claude-code/issues/102266) — `includeCoAuthoredBy: false` 未剥离 `Co-Authored-By`**（3 条评论，👍3）
   将归属配置设为空仍然导致 `Co-Authored-By: Claude` 被写入提交。对于有严格归属政策的团队而言具有合规相关性。

10. **[#107433](https://github.com/anthropics/claude-code/issues/107433) — 多并发会话的内存控制**（3 条评论）
    一家游戏工作室报告 24 个会话在共享工作站上消耗约 9.3 GB（每个约 390 MB）。请求共享 MCP 服务器和智能体内存预算——这是运行大量并行会话的团队的扩展性痛点。

---

## 关键 PR 进展

过去 24 小时内仅有一个 PR 更新：

1. **[#89404](https://github.com/anthropics/claude-code/pull/89404) — `validate-agent.sh`：不要在第一个警告处中止，停止误标有效智能体**
   修复 issue #83803。围绕 `set -euo pipefail` 交互的三个根本原因：`((warning_count++))` / `((error_count++))` 在首次递增时返回非零值，导致脚本在第一个警告处中止。直接影响 plugin-dev 自身的智能体验证——一个值得插件作者关注的自托管 bug。

*（注：报告窗口内仅有一个 PR 更新；无其他 PR 可供总结。）*

---

## 功能请求趋势

从追踪器提炼：

- **账户与身份管理** — 多账户切换（桌面端）、跨账户会话可见性和配置文件人体工学主导投票（[#18435](https://github.com/anthropics/claude-code/issues/18435)、[#90188](https://github.com/anthropics/claude-code/issues/90188)）。
- **成本与配额透明度** — 无头 vs 交互式 token 计费（#97074）、每子智能体上下文退出（#96405）和会话限制困惑占据大量比重。
- **上下文注入控制** — 请求按智能体退出启动上下文块（CLAUDE.md、git status）以及正确加载嵌套 `AGENTS.md`（[#98796](https://github.com/anthropics/claude-code/issues/98796)、[#96405](https://github.com/anthropics/claude-code/issues/96405)）。
- **工具可靠性** — 偏好原生 Edit 而非复杂 bash 重试（#104740）；停止自动模式下的 bash 滥用（#87971）。
- **UI/TUI 交互能力** — 按键绑定自定义（Enter/Ctrl+Enter）、响应内文本高亮以便后续导航（[#108674](https://github.com/anthropics/claude-code/issues/108674)）、从移动端远程权限批准（[#92145](https://github.com/anthropics/claude-code/issues/92145)）。
- **内存/资源扩展** — 面向高并发工作站的共享 MCP 服务器和智能体内存预算（[#107433](https://github.com/anthropics/claude-code/issues/107433)）。

---

## 开发者痛点

- **自动化中的成本不可预测性。** 1.8 倍的无头配额开销（#97074）是最尖锐的新痛点——它削弱了程序化使用与交互式使用成本等价的假设。
- **安全分类器误报与会话污染。** 合法的安全加固触发分类器，而一次命中就会毒化会话（[#63751](https://github.com/anthropics/claude-code/issues/63751)、[#107724](https://github.com/anthropics/claude-code/issues/107724)）。这侵蚀了对防护系统的信任。
- **bash 优先于原生工具的漂移。** 模型反复使用复杂 bash 而非原生文件工具，触发自动分类器并浪费轮次（#87971、#104740）。
- **静默的转录/可观测性丢失。** 后台守护进程会话丢失文本块（#65051）以及一行思考摘要替代工具间文本（#102807）使调试智能体行为变得困难。
- **归属配置未被遵守。** 尽管设置了 `includeCoAuthoredBy: false`，`Co-Authored-By` 仍被写入（#102266）——一个合规摩擦点。
- **规模化下的内存占用。** 在共享工作站上约 390 MB/会话 × 数十个会话是工作室/团队的真实运营约束（#107433）。
- **陈旧 issue 疲劳。** 今天“更新”的 issue 中有很大一部分是 `stale`/`duplicate` 关闭，这可能掩盖仍然相关的报告（例如，智能体撒谎/虚假完成，#92151/#92150）。

---

*基于 github.com/anthropics/claude-code 2026-11-10 数据生成。Issue/PR 编号和投票数如源快照中所报告。*

</details>

<details>
<summary><strong>OpenAI Codex</strong> — <a href="https://github.com/openai/codex">openai/codex</a></summary>

# OpenAI Codex 社区文摘 — 2026-11-10

## 1. 今日亮点

0.169.0-alpha 通道的发布活动仍在继续，新增两个 alpha 构建；与此同时，PR 队列显示出一轮集中于发布加固的推进：为 Desktop 回移（backport）旧版增量工具的热修复，以及多项认证/速率限制正确性修复。在 issue 方面，跨报告的配额耗尽追踪帖（#41220）仍是互动量最高的讨论串，而一组 Windows 桌面端回归问题（侧边栏项目丢失、沙箱策略拦截、聊天恢复失败）主导了近期的报告。

## 2. 发布

过去 24 小时内有两次 alpha 发布，更新日志均极为简略：

- **rust-v0.169.0-alpha.7** — https://github.com/openai/codex/releases
- **rust-v0.169.0-alpha.6** — https://github.com/openai/codex/releases

发布说明中未公布任何功能细节。值得注意的是，issue 中引用的当前稳定版本线为 0.167.0，而 PR 活动表明正在向 0.167.0-alpha.3.2 和 -alpha.4 回移 Desktop 热修复，这说明 0.169.0 的 alpha 版本尚不是 Desktop 实际分发的构建。

## 3. 热门 Issue

1. **[#41220](https://github.com/openai/codex/issues/41220) — [Meta] 配额异常耗尽 / 用量计量不一致**（84 条评论，21 👍）
   社区关于订阅配额和购买额度消耗速度快于 token 证据所预测情况的中心追踪帖。高评论数和跨报告的交叉引用使其成为计费/用量信任问题的实际协调帖——目前仓库中信号最强的 issue。

2. **[#42739](https://github.com/openai/codex/issues/42739) — Windows 桌面端：更新后本地项目从侧边栏消失**（84 条评论，9 👍）
   更新后，Projects 区域显示“No projects”，而聊天仍保留在 Recents 下，文件夹也仍存在于磁盘上。与 #41220 互动量持平；这是一个数据呈现层面的回归，侵蚀了用户对桌面应用项目模型的信心。

3. **[#35420](https://github.com/openai/codex/issues/35420) — Work/Codex 流在 OneDrive 支持的 Windows 工作区上断开连接**（39 条评论）
   当工作区由 OneDrive 支持且 OneDrive 处于降级状态时，反复出现 `stream disconnected before completion` 失败。这指向云同步目录的文件系统监视器/IO 韧性缺口——这是 Windows 企业环境的常见配置。

4. **[#41779](https://github.com/openai/codex/issues/41779) — Windows：本地 API 启动被“blocked by policy”拒绝**（19 条评论）
   `exec_command` 在执行前拒绝 `CreateProcess`，且不产生任何日志。沙箱策略对合法本地开发工作流的误报是一个反复出现的主题；诊断输出的缺失加剧了用户的不满。

5. **[#59855](https://github.com/openai/codex/issues/59855) — Windows Desktop 无法恢复云托管聊天（`AbsolutePathBuf` 反序列化错误）**（19 条评论）
   一个较新的回归（提交于 2026-11-06），显示 `Invalid request: AbsolutePathBuf deserialized without a base path`。评论的快速积累表明该问题在 Windows 上具有广泛可复现性。

6. **[#56682](https://github.com/openai/codex/issues/56682) — VS Code 扩展即使在隔离配置下也无法初始化 UI**（14 条评论，5 👍）
   Windows/Plus 上的扩展版本 26.1021.22027。隔离配置下的复现进一步证明这是扩展侧的初始化 bug，而非用户配置污染。

7. **[#50072](https://github.com/openai/codex/issues/50072) — macOS 0.157+：无头/非登录用户在 `CFPreferencesAppSynchronize` 上失败**（12 条评论）
   破坏了 macOS 上通过 SSH 和无头/CI 类环境使用 CLI 的能力——这对自动化和远程开发用户是一个实质性缺口。

8. **[#49737](https://github.com/openai/codex/issues/49737) — Android Remote：`thread/items/list` 超时时任务消息失败**（10 条评论，3 👍）
   属于更广泛的 Android Remote 可靠性问题簇的一部分（另见 #58574）。移动端远程控制正成为一个具有自身故障模式的独立界面。

9. **[#38638](https://github.com/openai/codex/issues/38638) — Responses WebSocket 每次重试停滞 300 秒；退出开关被移除**（8 条评论，2 👍）
   记录了通过 `wss://…/responses` 每次重试停滞 300 秒的问题，退出开关已被移除，HTTPS 回退需要 5 次失败重试。在可靠性尚未解决的情况下移除逃生通道是核心抱怨点。

10. **[#60383](https://github.com/openai/codex/issues/60383) — 超过 16 MiB 的 Stdio MCP 回复被静默丢弃；MCP App 请求挂起至 300 秒超时**（3 条评论）
    昨日提交，但对 MCP 生态开发者很重要：大型工具结果静默失败而非报错，导致难以诊断的挂起。

## 4. 关键 PR 进展

1. **[#60457](https://github.com/openai/codex/pull/60457) — [Hotfix] 将旧版增量工具回移至 0.167.0-alpha.4**（OPEN）
   为 Desktop 热修复回移 #59832 和 #60040；在原生 Amazon Bedrock 提供商的常规 Responses 上新增默认关闭的 `incremental_tools_legacy`，包括工具更新、压缩和恢复。

2. **[#60456](https://github.com/openai/codex/pull/60456) — [Hotfix] 将旧版增量工具回移至 0.167.0-alpha.3.2**（OPEN）
   配套回移，让 Desktop 可将原生 Mantle 和 Bedrock Runtime 会话在常规 Responses 上选择启用增量工具声明。与 #60457 一起，这表明提供商兼容性稳定化工作正在积极推进。

3. **[#60462](https://github.com/openai/codex/pull/60462) — 将 TUI 问题回答路由至 Cloud 消息准入**（CLOSED）
   当一轮对话正在运行时，通过 Cloud 的 start-or-steer 准入路径提交异步问题回答，并针对会话模式解析意图，以保证本地与远程的一致性。

4. **[#60461](https://github.com/openai/codex/pull/60461) — 在执行器能力发现中遵循权限授予范围**（CLOSED）
   已批准的文件系统访问使执行器技能在授权有效期内可被发现，而轮次范围的访问无法授权持久化插件服务器/钩子——这是一次有意义的最小权限收紧。

5. **[#60436](https://github.com/openai/codex/pull/60436) — 在预算检查后重新验证请求认证归属**（CLOSED）
   如果在异步预算检查期间认证归属发生变化，则重启 HTTP 请求设置，并验证 WebSocket 认证——这与账号切换/凭据过期类 bug 相关。

6. **[#60435](https://github.com/openai/codex/pull/60435) — 账号用量恢复时关闭模型切换提示**（CLOSED）
   当所有用量窗口降至 90% 以下时，清除待处理的速率限制模型切换提示。改动虽小，但直接解决了配额状态相关的 UX 噪音。

7. **[#60434](https://github.com/openai/codex/pull/60434) — 轮次开始时重置 app-server 轮次摘要**（CLOSED）
   修复了因轮次前错误累积在 `TurnSummary` 中而导致成功轮次被报告为失败的问题——这是轮次状态报告的一项正确性修复。

8. **[#60423](https://github.com/openai/codex/pull/60423) — 在资源列表中暴露延迟加载的 MCP 服务器**（CLOSED）
   在聚合的 `list_mcp_resources` 和 `list_mcp_resource_templates` 响应中新增排序后的 `deferredServers` 列表，使休眠的 MCP 服务器仍可被查询。

9. **[#60432](https://github.com/openai/codex/pull/60432) — 将内置 SQLite 升级至 3.54 以修复 WAL 问题**（CLOSED）
   将 `libsqlite3-sys` 升级至 0.39.1，并通过编译时断言要求 SQLite ≥3.54，在解决 `RUSTSEC-2026-0459` 的同时保留 WAL 重置损坏修复。

10. **[#60414](https://github.com/openai/codex/pull/60414) — 启用带网关 OAuth 的模型目录发现**（CLOSED）
    让具有网关 OAuth 和显式目录的自定义提供商无需 API key 即可发现模型——对企业网关部署很有用。

## 5. 热门讨论

**展示与分享**

- **[#58359](https://github.com/openai/codex/discussions/58359) — cct：在 Windows 任务栏上显示 Codex 5 小时和每周限额**（开源，<1 MB）
  在 Windows 10/11 任务栏上呈现 `/status` 速率限制百分比。值得注意的是这是社区构建的配额可见性工具——与 issue #41220 背后的关注点相同。

- **[#58849](https://github.com/openai/codex/discussions/58849) — code-review-skill：带 JSON 合并门禁的证据支撑代码审查**
  静态优先的审查技能，在全新上下文中通过四个探针产出可审计的发现（位置 + 契约/代码路径 + 严重性），并配有 JSON 合并门禁。

- **[#60185](https://github.com/openai/codex/discussions/60185) — agent-recap：跨 Codex 和 Claude Code 转录的本地搜索**
  基于浏览器的 Codex 和 Claude Code JSONL 转录搜索；无需账号、安装或上传。

- **[#60116](https://github.com/openai/codex/discussions/60116) — Pushary：从手机回答 Codex 审批**
  付费托管服务，使用原生钩子 + MCP 服务器将待处理审批路由至手机。由开发者本人披露。

- **[#60441](https://github.com/openai/codex/discussions/60441) — Tapproval：在 Apple Watch 上审批，并标注风险**
  开源本地助手，将来自 `hooks.json` 的权限请求转交至 Apple Watch；补充了 #33528 中长期存在的 watchOS 请求。

- **[#60297](https://github.com/openai/codex/discussions/60297) — Better Codex Context (BCC) 插件**
  旨在通过改变上下文管理来节省 token 并减少长时间运行目标“卡住”的情况——与 #55990 中提出的上下文/压缩痛点直接相关。

- **[#60358](https://github.com/openai/codex/discussions/60358) — 更安全运行智能体的 10 个 MIT 许可习惯**
  可直接粘贴使用的提示词/片段，用于无人值守的智能体操作（例如在权限不明确时阻止工具调用）。补充了安全检查相关的 issue 簇。

- **[#58834](https://github.com/openai/codex/discussions/58834) — Friday：在独立 git worktree 中与 Claude Code 和 Cursor 并行运行 Codex**
  MIT 许可的桌面应用，带有任务板，用于多智能体 worktree 隔离。

- **[#60282](https://github.com/openai/codex/discussions/60282) — Worktree receipt：在 Git 状态干净时捕获本地设置漂移**
  Python/Git 回执，针对两个 worktree 处于同一提交但具有不同未跟踪本地设置文件的交接缺口。

- **[#60409](https://github.com/openai/codex/discussions/60409) — 问答：如何安全调查被阻止的 Windows 检查？**
  一次只读检查因可能向未验证目标发送遥测而被阻止，但警告中未指明命令/目标/进程——这是 #41779 中所见沙箱诊断缺口的一个具体实例。

## 6. 功能请求趋势

- **资源治理与预算感知**：#55990 提议原生进度感知预算、有界推理升级和压缩安全的延续；社区工具（#58359）在外部构建配额可见性。需求是一流的 token/配额控制，而非提示词层面的指导。
- **桌面端之外的审批工作流**：#33528（watchOS）、#60116（手机）和 #60441（Watch）都面向远程审批/监控——用户希望离开键盘也能监督智能体。
- **MCP 成熟度**：延迟服务器发现（#60423）、延迟纯文本输出（#60390）以及 >16 MiB 静默丢弃 bug（#60383）表明请求正从“支持 MCP”转向“使 MCP 在大规模下稳健”。
- **长任务的上下文管理**：#60297 和 #55990 都针对上下文膨胀和任务过早完成（#50482）——对更好的压缩和延续语义的持续需求。
- **跨工具互操作性**：跨 Codex/Claude Code 的转录搜索（#60185）和基于 worktree 的多智能体共存（#58834）反映出多智能体、多供应商工作流正成为常态。

## 7. 开发者痛点

- **Windows 桌面端可靠性是主导主题**：侧边栏项目丢失（#42739）、云聊天恢复失败（#59855）、VS Code 扩展初始化失败（#56682）、Chrome 调试器脱离（#55554）、空闲 RAM 增长（#53146）以及 UI 回归（#55231、#39781）。多份报告是更新后的回归，这加剧了不满。
- **沙箱/策略误报且诊断信息差**：#41779（“blocked by policy”，无日志）和 #60409（未指明被阻止的目标）显示拦截未指明触发命令或目标——开发者无法区分策略与 bug。
- **用量/配额计量不信任**：#41220（84 条评论）加上计费困惑（#59349：Pro $500 赠送的 API 额度在发放数天后即过期）和速率限制 UI 陈旧（#60435）构成了围绕成本的信任问题簇。
- **长超时下的静默失败**：16 MiB MCP 回复被静默丢弃并伴随 300 秒挂起（#60383）、300 秒 WebSocket 停滞且退出开关被移除（#38638），以及长时间运行会话中的端点陈旧（#58060）。开发者一致要求快速、明确的错误，而非静默停滞。
- **无头/自动化缺口**：macOS 上非登录用户的 `CFPreferencesAppSynchronize` 失败（#50072）破坏了 SSH/CI 使用；被移除的 WebSocket 退出开关（#38638）移除了脚本化环境的逃生通道。
- **磁盘与资源卫生**：macOS 上 263.7 GB 的 `tmp_pack` 垃圾（#54955）且配置控制无效，加上 Windows 上的空闲 RAM 增长（#53146）——本地资源失控消耗且无实时补救途径。

</details>

<details>
<summary><strong>Gemini CLI</strong> — <a href="https://github.com/google-gemini/gemini-cli">google-gemini/gemini-cli</a></summary>

# Gemini CLI 社区文摘 — 2026-11-10

## 今日亮点
过去 24 小时内没有发布新版本，但 issue 和 PR 活动依然活跃（50 个 issue、44 个 PR 有更新）。主导话题是**子代理可靠性**：多个 P1 缺陷报告子代理挂起、静默触达轮次上限，或将失败误报为成功。与此同时，贡献者们正大力推进工具安全性和上下文效率方面的修复——限制 `grep_search` 输出、为每次工具调用设置超时，以及强化 Git 环境。

## 版本发布
过去 24 小时内无新版本发布。

## 热门 Issue

1. **[#22323](https://github.com/google-gemini/gemini-cli/issues/22323) — 子代理在 MAX_TURNS 后恢复被报告为 GOAL 成功**（P1，13 条评论）
   `codebase_investigator` 子代理在尚未进行任何分析就触达轮次上限时，仍报告 `success`/`GOAL`。这静默掩盖了失败——可以说是当前跟踪器中后果最严重的正确性问题，也是今天讨论最多的条目。

2. **[#21409](https://github.com/google-gemini/gemini-cli/issues/21409) — 通用代理挂起**（P1，8 条评论，8 个 👍）
   一旦 Gemini 交由通用代理处理，简单任务（甚至创建文件夹）就会无限挂起。在今日 issue 中获得最高社区支持；存在一个变通方案（禁用子代理延迟处理），这直接将矛头指向委派逻辑。

3. **[#19873](https://github.com/google-gemini/gemini-cli/issues/19873) — 零依赖操作系统沙箱与执行后意图路由**（P2，9 条评论）
   一项大型设计方案，旨在让 Gemini 3 充分发挥其原生 bash 亲和力，同时保持执行安全。这揭示了团队希望代理架构的走向：沙箱优先、意图校验的 shell 使用。

4. **[#21968](https://github.com/google-gemini/gemini-cli/issues/21968) — Gemini 未充分使用技能和子代理**（P2，7 条评论）
   虽是轶事，但引起广泛共鸣：除非明确告知，模型会忽略相关的自定义技能/子代理。这是扩展生态系统的核心发现/调用质量问题。

5. **[#22745](https://github.com/google-gemini/gemini-cli/issues/22745) — 评估 AST 感知的文件读取、搜索和映射**（P2，7 条评论）
   一项 EPIC，研究 AST 感知工具以减少错位读取和 token 噪声。配套 issue（[#22747](https://github.com/google-gemini/gemini-cli/issues/22747)）建议以 `ast-grep` 作为起点。

6. **[#22267](https://github.com/google-gemini/gemini-cli/issues/22267) — 浏览器代理忽略 `settings.json` 覆盖**（P2，5 条评论）
   `AgentRegistry` 读取合并后的设置，但浏览器代理丢弃了它们（例如 `maxTurns`）。这是一个明显的配置管道缺陷，削弱了用户对每代理设置的信任。

7. **[#21983](https://github.com/google-gemini/gemini-cli/issues/21983) — 浏览器子代理在 Wayland 上失败**（P1，4 条评论）
   Linux/Wayland 用户遇到浏览器子代理失败，却被报告为 `GOAL` 终止——这是成功误报模式的又一实例。对 Linux 高级用户而言是一个显著平台缺口。

8. **[#24246](https://github.com/google-gemini/gemini-cli/issues/24246) — 工具超过 128 个时出现 400 错误**（P2，3 条评论）
   注册工具过多会导致请求失败。随着 MCP 和扩展采用的增长，工具作用域划分/选择将成为扩展瓶颈。

9. **[#22672](https://github.com/google-gemini/gemini-cli/issues/22672) — 代理应停止/劝阻破坏性行为**（P2，3 条评论）
   当存在更安全的选项时，模型偶尔会选择 `git reset` 或 `--force`。这是对用户有直接爆炸半径影响的安全对齐工作。

10. **[#18836](https://github.com/google-gemini/gemini-cli/issues/18836) — 用基于文件的持久化任务跟踪替换 WriteToDo**（P3，2 条评论）
    提议采用 CRUD 风格的磁盘任务跟踪，以对抗上下文腐化和跨会话失忆。这是长时间运行的代理工作的一个低调但重要的架构方向。

## 关键 PR 进展

1. **[#30207](https://github.com/google-gemini/gemini-cli/pull/30207) — fix(build): 在 esbuild 前清理 `bundle/`**（P0）
   自 2026-11-05 起，每夜构建因 `413 Request Entity Too Large` 失败；陈旧的构建分块将 tarball 膨胀至 25.2 MB。这是发布阻断问题，也是今天最紧急的 PR。

2. **[#30206](https://github.com/google-gemini/gemini-cli/pull/30206) — fix: 阻止项目 Git 环境注入**（P1，安全）
   防止工作区提供的 `GIT_*` 环境变量通过 `.env` 加载影响内部 Git/shell 操作。这是供应链加固的重要一步。

3. **[#30212](https://github.com/google-gemini/gemini-cli/pull/30212) — feat: 可配置的每次工具调用超时（`tools.callTimeout`）**
   为每次工具调用设置墙钟时间上限，并抛出可恢复的 `TOOL_CALL_TIMEOUT` 错误，使模型能够适应。直接解决“无限挂起”类缺陷（#21409）。

4. **[#30109](https://github.com/google-gemini/gemini-cli/pull/30109) — fix(core): 限制 `grep_search` 输出大小**
   目前宽泛的 grep 可能返回约 150K 字符；此 PR 限制输出并引导模型缩小搜索范围——这是上下文效率的重要胜利。

5. **[#30210](https://github.com/google-gemini/gemini-cli/pull/30210) — fix(core): 取消后停止 WebFetch 回退工作**
   在回退 URL 尝试之间检查取消状态，避免中止后的无效工作。修复 #30099。

6. **[#30204](https://github.com/google-gemini/gemini-cli/pull/30204) — fix: 无头中止时终止子进程并保留信号退出码**
   确保后台 shell 不会在 `gemini -p` 终止后存活，并记录已取消的工具。对 CI/无头使用是可靠的可靠性改进。

7. **[#30164](https://github.com/google-gemini/gemini-cli/pull/30164) — fix(cli): 在进入 macOS Seatbelt 沙箱前解析文件夹信任**
   修复了沙箱配置文件开始拒绝访问 `~/.gemini/trustedFolders.json` 后，Seatbelt 内文件夹信任失败的问题。macOS 沙箱成熟度工作。

8. **[#30176](https://github.com/google-gemini/gemini-cli/pull/30176) — fix(cli): 在认证重启间保留所选 worktree**
   `--worktree` 选择现在能在 `RELAUNCH_EXIT_CODE = 199` 认证重启流程中保留。对 worktree 用户而言是小而高烦恼度的修复。

9. **[#30214](https://github.com/google-gemini/gemini-cli/pull/30214) — fix(core): 将 TortoiseGitPlink 识别为 PuTTY 家族 SSH 客户端**
   正确插入 `-batch` 而非 OpenSSH 的 `-o BatchMode=yes`，修复了 Windows+TortoiseGit 环境下的 Git-over-SSH 加固问题。

10. **[#29978](https://github.com/google-gemini/gemini-cli/pull/29978) — fix(core): 在 `read_background_output` 中保留超长行和边界行**
    修复了静默数据丢失问题：64KB 窗口的第一行被丢弃，且超过 64KB 的单行日志返回空输出。

## 热门讨论
本数据集未提供讨论数据；本节省略。

## 功能需求趋势
- **更智能、更自主的委派** — 让模型可靠地发现和调用技能/子代理（#21968），改进 `codebase_investigator`（#18282），并通过 `/chat share` 暴露子代理轨迹（#22598）。
- **上下文效率与精度** — AST 感知的读取/搜索/映射（#22745、#22747），用于精准读取的“Tactful Extraction”（#19561），grep 输出限制（PR #30109），以及基于文件的持久化任务跟踪（#18836）。
- **兼具原生 shell 流畅性的沙箱** — 零依赖操作系统沙箱加执行后意图路由（#19873）。
- **配置与可扩展性** — 通过 `settings.json` 发现子代理（#18285），每工作区策略覆盖（#18397），以及每次工具调用超时（PR #30212）。
- **自我认知** — 准确了解 CLI 标志/热键，使代理能够向用户介绍自身（#21432）。

## 开发者痛点
- **子代理挂起与静默失败误报** — 最响亮的集群：通用代理挂起（#21409），`MAX_TURNS` 被报告为 `GOAL` 成功（#22323），Wayland 浏览器代理失败（#21983），以及输出钩子崩溃（#22186）。用户因看似“成功”但实际什么都没做的会话而浪费时间。
- **配置覆盖未被遵守** — 浏览器代理忽略 `settings.json`（#22267）；符号链接的代理文件无法被识别（#20079）。配置管道滞后于配置界面。
- **shell 限制下的代理不当行为** — 当 shell 执行受限时，模型会用临时脚本弄乱工作区（#23571），并偶尔选择破坏性 Git 命令（#22672）。
- **上下文膨胀与信息丢失** — 过大的 grep/读取输出膨胀上下文，而边界行裁剪静默丢弃数据（PR #29978）；交互式提示可能死锁代理流程（#22465）。
- **发布工程脆弱性** — 陈旧的构建产物导致每夜构建中断近一周（PR #30207），提醒人们打包流水线需要与核心代码同等的严谨性。

</details>

<details>
<summary><strong>GitHub Copilot CLI</strong> — <a href="https://github.com/github/copilot-cli">github/copilot-cli</a></summary>

# GitHub Copilot CLI 社区简报 — 2026-11-10

## 1. 今日亮点

过去 24 小时内有两次版本发布：**v1.0.109-0** 加强了 `/share` 和 `/export` 的安全防护（符号链接确认），为 URL 权限新增了会话级子域名批准，并减少了只读 shell 脚本的权限提示；**v1.0.108** 新增了 Windows 仅枚举文件系统授权、`/index` 命令（默认启用托管的共享 tgrep 索引），以及 Grok 4.7/4.6 的 PDF 回退方案。问题追踪器依然非常活跃（29 个 issue 有更新），主要集中在 **1.0.108 MCP 命名空间回归（已关闭）**、来自同一报告者的一组 SDK/会话可靠性 bug，以及 macOS 和企业租户上围绕沙箱网络/权限行为的持续压力。

## 2. 版本发布

**v1.0.109-0**（[发布说明](https://github.com/github/copilot-cli/releases)）
- **安全加固：** `/share` 或 `/export` 通过符号链接保存本地文件前会弹出确认提示。
- **URL 权限：** 提示现允许为当前会话批准*所有子域名*。
- **更少提示：** 只读 shell 脚本触发权限的频率降低；修改 git ref 或通过 `hid*` 读取文件的脚本仍会触发提示。

**v1.0.108**（2026-11-09）
- `settings.json` 新增 `sandbox.userPolicy.filesystem.enumeratePaths`，用于 **Windows 仅枚举目录授权**。
- 新增 **`/index` 命令**；对符合条件的仓库默认启用托管的共享 **tgrep 索引**。
- **Grok 4.7 / Grok 4.6 上的 PDF 附件**现回退为文件引用。

## 3. 热门 Issue

1. **[#5400](https://github.com/github/copilot-cli/issues/5400) [已关闭] 1.0.108 回归：延迟的 MCP function_call 丢失命名空间** — 延迟的 MCP 工具调用成功后，下一个请求失败并报 `400 Missing namespace for function_call`。对 SDK 托管的会话影响较大；当天即关闭，说明修复迅速。
2. **[#5185](https://github.com/github/copilot-cli/issues/5185) [开放] 严重：copilot-runtime-bazel-cache 内存使用与限制（dotcom）** — 运行时基础设施的内部 statuspage 告警；作为支撑 CLI 使用的服务可用性信号值得关注。
3. **[#5392](https://github.com/github/copilot-cli/issues/5392) [开放] Claude 响应在约 0.3% 的调用中报告重复 prompt — 用量与计费** — 计量/计费正确性 bug，复现率精确；考虑到成本影响，可能会很快得到关注。
4. **[#5390](https://github.com/github/copilot-cli/issues/5390) [开放] macOS 沙箱：`allowLocalNetwork: true` 仍生成 `hostLoopback: deny`** — 导致沙箱内 Gradle 无法工作（localhost 守护进程不可达）。典型的沙箱与本地开发工具摩擦。
5. **[#5388](https://github.com/github/copilot-cli/issues/5388) [开放] 在 GHE 数据驻留租户上，沙箱凭据掩码对 `GH_TOKEN`/`GH_ENTERPRISE_TOKEN` 无效** — Token 环境变量在掩码*之前*就被丢弃；企业安全问题。
6. **[#5402](https://github.com/github/copilot-cli/issues/5402) [开放] MCP Entra 静默登录使用 `/common`，对单租户应用失败（AADSTS50194）** — 自 1.0.107 起的回归，导致每个会话都需强制运行 `/mcp auth`；企业认证痛点。
7. **[#5395](https://github.com/github/copilot-cli/issues/5395) [开放] `session.background_tasks_changed` 超时后会话停止接受消息；后台 agent 结果丢失** — 会话宿主事件投递超时（120 秒）使会话陷入僵局；对后台 agent 而言是严重的可靠性问题。
8. **[#5394](https://github.com/github/copilot-cli/issues/5394) [开放] `cancelBackgroundCompaction()` 不发出 `compaction_complete`** — SDK 取消压缩后，“Compacting conversation…” UI 卡住；SDK/扩展正确性问题。
9. **[#5387](https://github.com/github/copilot-cli/issues/5387) [开放] [Windows] PowerShell 工具会话在脚本使用 `Start-Process -RedirectStandardOutput` 启动后台进程时无限挂起** — Windows 工具链挂起；阻塞常见的服务器启动工作流。
10. **[#5323](https://github.com/github/copilot-cli/issues/5323) [开放] 功能请求：禁用 agent checkpoint ref（`refs/agents/*`）** — 每个 agent 轮次都会以用户的 git 身份创建提交；污染 ref 渲染工具。社区反应一般（👍1），但该问题具有结构性。

*另值得注意：* [#5320](https://github.com/github/copilot-cli/issues/5320)（已关闭，👍11）面向 Herdr 的 OSC 7501 agent 状态上报 — 本时间窗内获赞最多的条目。

## 4. 关键 PR 进展

过去 24 小时内无 pull request 更新（0 项）。本期的进展仅通过版本发布和 issue 关闭体现。

## 5. 热门讨论

未提供 Discussions 数据；本节省略。

## 6. 功能请求趋势

- **权限与沙箱可配置性**（史诗 [#316](https://github.com/github/copilot-cli/issues/316)）：更细粒度的文件系统授权（1.0.108 中的 Windows 枚举路径）、localhost/loopback 允许（[#5390](https://github.com/github/copilot-cli/issues/5390)），以及禁用 agent checkpoint ref（[#5323](https://github.com/github/copilot-cli/issues/5323)）。
- **MCP/企业认证**：为预注册客户端固定 OAuth 回调端口（[#5391](https://github.com/github/copilot-cli/issues/5391)）、租户正确的 Entra 登录（[#5402](https://github.com/github/copilot-cli/issues/5402)）、Kerberos/NTLM 代理支持（[#5142](https://github.com/github/copilot-cli/issues/5142)，已关闭），以及 Trusted Access for Cyber 关联（[#4322](https://github.com/github/copilot-cli/issues/4322)）。
- **会话/agent 可观测性与控制**：长任务的原生 OS 通知（[#2616](https://github.com/github/copilot-cli/issues/2616)）、OSC 7501 状态上报（[#5320](https://github.com/github/copilot-cli/issues/5320)）、通过 GitHub Mobile 的远程语音模式（[#5315](https://github.com/github/copilot-cli/issues/5315)）。
- **平台一致性**：Windows 上的 Git Bash（[#508](https://github.com/github/copilot-cli/issues/508)）、shell 展开一致性（`$HOME` vs `~`，[#144](https://github.com/github/copilot-cli/issues/144)，已关闭）。
- **ACP/扩展生态**：`--acp` 模式下的 Copilot Connectors（[#5397](https://github.com/github/copilot-cli/issues/5397)）、hook 的 `ask` 决策传达至 ACP 客户端（[#5396](https://github.com/github/copilot-cli/issues/5396)，已关闭）。
- **企业文档/信任**：浏览器使用插件的已验证浏览器扩展发布者和安全审查文档（[#5389](https://github.com/github/copilot-cli/issues/5389)）。

## 7. 开发者痛点

- **沙箱与实际开发工作流**：loopback 拒绝导致 Gradle 无法工作（[#5390](https://github.com/github/copilot-cli/issues/5390)），以及 GHE 租户上凭据掩码无效（[#5388](https://github.com/github/copilot-cli/issues/5388)），表明沙箱策略仍会让用户感到意外。
- **会话可靠性**：多种独立故障模式 — `-p` 模式下的共享写入器 I/O 失败（[#5357](https://github.com/github/copilot-cli/issues/5357)）、后台任务事件超时（[#5395](https://github.com/github/copilot-cli/issues/5395)）、会话无法恢复（[#5386](https://github.com/github/copilot-cli/issues/5386)），以及压缩 UI 卡住（[#5394](https://github.com/github/copilot-cli/issues/5394)）。
- **用量/计费信任**：约 0.3% 的 Claude 调用报告重复 prompt（[#5392](https://github.com/github/copilot-cli/issues/5392)），以及基于单一矛盾读数触发自动压缩（[#5393](https://github.com/github/copilot-cli/issues/5393)）。
- **Windows 粗糙边缘**：PowerShell 配合 `Start-Process` 挂起（[#5387](https://github.com/github/copilot-cli/issues/5387)）、通用的 Credential Manager 诊断（[#5399](https://github.com/github/copilot-cli/issues/5399)）、缺少 Git Bash（[#508](https://github.com/github/copilot-cli/issues/508)）。
- **企业认证与代理摩擦**：单租户 Entra 应用需每会话重跑 `/mcp auth`（[#5402](https://github.com/github/copilot-cli/issues/5402)），以及企业代理认证缺口（[#5142](https://github.com/github/copilot-cli/issues/5142)）。
- **仓库中的 agent 卫生**：以用户身份撰写的不需要的 `refs/agents/*` checkpoint 提交（[#5323](https://github.com/github/copilot-cli/issues/5323)），以及子 agent 在临时文件使用上的 prompt 矛盾（[#5352](https://github.com/github/copilot-cli/issues/5352)）。

---
*来源：[github/copilot-cli](https://github.com/github/copilot-cli) 于 2026-11-09 → 2026-11-10 更新的发布与 issue。*

</details>

<details>
<summary><strong>OpenCode</strong> — <a href="https://github.com/anomalyco/opencode">anomalyco/opencode</a></summary>

# OpenCode 社区文摘 — 2026-11-09

## 1. 今日亮点

过去 24 小时内没有发布新版本，但 issue 跟踪器依然十分活跃，共有 50 个 issue 和 50 个 PR 得到更新。免费额度混乱问题继续主导社区注意力（仅 Issue #59387 就有 339 条评论），而 v2 服务架构正暴露出一批基础设施 bug，涉及端口冲突、配置重载和 MCP 进程派生。在 PR 方面，维护者 `jlongster` 合入了一组由三部分组成的堆叠系列，重构了进程守护进程中的 PTY 协议和 shell 命令执行。

---

## 2. 版本发布

*过去 24 小时内没有新版本发布。*

---

## 3. 热门 Issue

1. **[#59387](https://github.com/anomalyco/opencode/issues/59387) — 免费额度问题巨型讨论帖（339 条评论，👍27）**  
   免费额度投诉（“Free usage exceeded”、永不触发的重置计时器）的权威集散地。如此庞大的数量表明，系统性的计费/额度状态问题在两周后仍未解决。

2. **[#43250](https://github.com/anomalyco/opencode/issues/43250) — `keep.tokens` 未生效；压缩回退无边界**  
   已复现的压缩 bug：agent 会话在设置为 15K 的情况下携带约 234K token——超出预算 5–15 倍。直接影响长时间运行的 agent 工作流的成本和上下文质量。

3. **[#50814](https://github.com/anomalyco/opencode/issues/50814) — 压缩后模型将工具调用输出为字面文本**  
   压缩之后，工具调用退化为纯文本且永远不会被派发，悄无声息地破坏会话。这是压缩流水线中严重的正确性故障。

4. **[#51570](https://github.com/anomalyco/opencode/issues/51570) — LM Studio 模型发现因 API key 而失败（👍4）**  
   `/models` 请求遗漏了已配置的 API key，导致本地模型发现失败，尽管推理本身可用。对本地 LLM 用户群体影响很大。

5. **[#59861](https://github.com/anomalyco/opencode/issues/59861) — 当提供商报告 $0 时恢复目录价格估算**  
   自 v2.2.0 起，Go 订阅会话不显示费用，“$X spent”指示器也消失了。社区希望提供一个可选的估算开关。

6. **[#43845](https://github.com/anomalyco/opencode/issues/43845) — v2 服务为每个项目目录派生一对 MCP 服务器**  
   在约 53 个已编目项目的情况下，启动时会派生数十个 stdio MCP 进程，而不是一组。这是新服务模型中的资源耗尽回归。

7. **[#61695](https://github.com/anomalyco/opencode/issues/61695) — 宿主侧重启在模型看来是“用户不想继续”**  
   Claude Agent 会话将基础设施重启误报为用户取消，破坏了 agent 的推理。已复现且特定于该提供商。

8. **[#62872](https://github.com/anomalyco/opencode/issues/62872) — 部分配置写入会杀死所有会话（没有 last-good 回退）**  
   对 `opencode.jsonc` 的一次瞬态部分写入会导致整个配置被拒绝且没有回退，从而杀死正在运行的会话。这是支持原子配置重载的有力论据；修复 PR 已经开启。

9. **[#62948](https://github.com/anomalyco/opencode/issues/62948) — Git 轮询循环在大型工作树上占满 CPU**  
   在 130 万文件的工作树上紧密循环执行 `git status` 扫描会占满 CPU。任何在根植于 `$HOME` 的仓库内运行 OpenCode 的人都会受影响。

10. **[#62923](https://github.com/anomalyco/opencode/issues/62923) — Code Mode 的 `execute` 对一切返回 `null`**  
    Code Mode 的 execute 工具在 v2.2.6 中实际上已损坏——甚至抛出的错误也返回 null。完全阻断了 Code Mode 功能。

*荣誉提名：* [#62934](https://github.com/anomalyco/opencode/issues/62934)（Android 17 缺少 `ACCESS_LOCAL_NETWORK`）、[#62981](https://github.com/anomalyco/opencode/issues/62981)（固定宽度变体浮出 UI）、[#62641](https://github.com/anomalyco/opencode/issues/62641)（step-limit 助手提示被 no-prefill 模型拒绝）。

---

## 4. 关键 PR 进展

1. **[#62976](https://github.com/anomalyco/opencode/pull/62976) — opencode-pty 协议 v8**（jlongster）  
   三个堆叠 PR 中的第一个，替代 #61876；更新 PTY 协议基础。

2. **[#62978](https://github.com/anomalyco/opencode/pull/62978) — 在进程守护进程中跨重启运行 shell 命令**（jlongster）  
   第三个堆叠 PR：shell 命令通过驻留在守护进程中而能在重启后存活——对长会话而言是持久性提升。

3. **[#62974](https://github.com/anomalyco/opencode/pull/62974) — 部分写入时保留 last-good 配置**（iMumd）  
   通过在非原子写入期间保留最后一个有效配置来修复 #62872——防止大规模会话死亡。

4. **[#62904](https://github.com/anomalyco/opencode/pull/62904) — 让托管服务端口远离临时端口范围**（2SinceJuly）  
   通过将仅限 Windows 的变通方案扩展到 Linux/macOS，修复 #62891 的端口 49374 冲突。

5. **[#62984](https://github.com/anomalyco/opencode/pull/62984) — 使用名称加权 BM25 对 Code Mode 工具搜索排序**（rekram1-node）  
   用 BM25 替代子串打分，降低 `list`/`get` 等噪声词的权重——相关性得到实质性改进。

6. **[#62982](https://github.com/anomalyco/opencode/pull/62982) — 将 MCP SDK 更新至 2.4.0**（rekram1-node）  
   引入 Skills 扩展（SEP-2640）并重建客户端补丁；为未来的 skills 支持奠定基础。

7. **[#62963](https://github.com/anomalyco/opencode/pull/62963) — 跨屏幕锁定保留浏览器工具**（jlongster）  
   在锁定/解锁时自动分离/重新附加浏览器工具，而无需常开设置。

8. **[#62983](https://github.com/anomalyco/opencode/pull/62983) — 向 Claude 交付带键的指令更新**（iMumd）  
   修复 #62916：历史中的系统消息现在能到达 `claude-agent` 提供商。

9. **[#62979](https://github.com/anomalyco/opencode/pull/62979) — 将 EVREN thinking 控制以布尔值发送**（cemililik）  
   针对 EVREN 的 OpenAI 兼容网关的提供商兼容性修复。

10. **[#60055](https://github.com/anomalyco/opencode/pull/60055) — 在 TUI 中报告未经验证的剪贴板交付**（Chubercik）  
    不再虚假声称“Copied to clipboard”；关闭 #59664。小而高信任度的 UX 修复。

*站点性能清理：* 一批 `adamdotdevin` 的 PR（[#62971](https://github.com/anomalyco/opencode/pull/62971)、[#62965](https://github.com/anomalyco/opencode/pull/62965)、[#62966](https://github.com/anomalyco/opencode/pull/62966)、[#62962](https://github.com/anomalyco/opencode/pull/62962)、[#62964](https://github.com/anomalyco/opencode/pull/62964)、[#62980](https://github.com/anomalyco/opencode/pull/62980)）削减了空闲轮询、推迟了导航/控制台加载，并修复了媒体缓存。

---

## 5. 热门讨论

*本周期未提供 Discussion 数据。*

---

## 6. 功能请求趋势

- **支付与账户管理：** OpenCode Go 的加密货币支付（#23153，已关闭，👍67——本窗口内得票最高的条目）、Zen 中的邮箱修改/移除（#18654），以及控制台页面的本地化（#62768）。
- **会话 UX：** 在实验性 Inbox 中固定会话（#62772），以及针对 `done`/`error` 状态的 OSC 7501 报告（#60411）——扩展现有的终端通知协议。
- **跨平台健壮性：** Windows PowerShell 应规范化 en/em 破折号和弯引号，而不是拒绝命令（#61704）。
- **成本透明度：** 当提供商报告 $0 时可选启用目录价格估算（#59861）。
- **集群管理：** `opencode join` 用于 hub 管理的多主机节点集群，具备集中式会话发现（#44760，已关闭）。
- **Agent 透明度：** 失败的 MCP 服务器应能与未配置的服务器区分开来（#62973）。

---

## 7. 开发者痛点

1. **免费额度状态不可靠。** 339 条评论的巨型讨论帖（#59387）和重复报告（#58687、#62783）显示重置计时器不触发，且限制在切换模型后依然存在——这是最响亮的投诉。
2. **压缩是最薄弱的环节。** 两个分别复现的 bug（#43250 预算超支、#50814 工具调用变文本）使长时间 agent 会话在成本和正确性上都变得不可预测。
3. **v2 服务生命周期回归。** 端口冲突（#62891）、每项目 MCP 进程风暴（#43845）、占满 CPU 的 git 轮询（#62948），以及没有 last-good 配置回退（#62872），都指向新守护进程架构需要加固。
4. **提供商兼容性摩擦。** LM Studio API-key 发现（#51570）、Claude Agent 重启误判（#61695）、no-prefill 模型拒绝 step-limit 提示（#62641），以及 EVREN 布尔控制（#62979），表明多提供商矩阵需要更多一致性测试。
5. **本地/桌面设置问题反复出现。** Desktop 2.2.6 上的“No models available”（#62781）、缺少自定义提供商选项（#59603），以及 Android 17 局域网权限（#62934），表明各客户端在入门引导上存在缺口。

---

*文摘于 2026-11-10 从 github.com/anomalyco/opencode 生成。所有链接指向规范的 issue/PR 页面。*

</details>

<details>
<summary><strong>Pi</strong> — <a href="https://github.com/earendil-works/pi">earendil-works/pi</a></summary>

# Pi 社区摘要 — 2026-11-10

## 1. 今日亮点

今天是一个繁重的分类处理日：Windows sink 线程（#7547）的评论数暴涨至 95 条，暴露出了一批 Windows/PowerShell/stderr 相关的 bug，`petrroll` 正在通过一系列 PR 系统性地修复这些问题。在模型集成方面，`StringEnum` 类型回归（P0）和 Claude 模型请求头不兼容是最高优先级的项目。过去 24 小时内无新版本发布。

---

## 2. 版本发布

过去 24 小时内无新版本。（注：v1.5.0 已于此前发布，是下方多个 P0 问题的根源——参见 #11869、#11892、#11769。）

---

## 3. 热门 Issue

1. **#7547 — [Windows] 如何在 Windows 上使用 Pi？**（95 条评论，OPEN）
   Windows 旗舰综合讨论帖。社区正在列举在 Windows 上运行 Pi 的各种方式（原生、Git Bash、WSL、MSYS2/mintty），以及精力应该投向何处：核心修复还是委托给扩展。今日互动量最高的 Issue。
   https://github.com/earendil-works/pi/issues/7547

2. **#11869 — [bug, prio:p0] Pi 1.5.0 中的 `StringEnum` 破坏了编译时类型检查**（OPEN，👍3）
   一个 1.5.0 回归：`StringEnum` 不再继承自 `typebox`，导致工具参数字段退化为 `unknown`。破坏了下游 TypeScript 工具作者的使用——因此被标记为 P0 并获得点赞。
   https://github.com/earendil-works/pi/issues/11869

3. **#11773 — [inprogress, prio:p0] 升级 Token Plan 品牌和网关配置**（OPEN）
   将 Token Plan 更名为千问 AI 平台 / QwenCloud，并启用新的默认端点。P0 是因为它影响新用户的默认配置。
   https://github.com/earendil-works/pi/issues/11773

4. **#3200 — 在 prompt 命令中支持视频/音频内容**（OPEN，👍8）
   获赞最多的开放请求：扩展 `prompt` RPC 以在图像之外转发音频/视频，供多模态模型使用。多模态 RPC 的需求信号明确。
   https://github.com/earendil-works/pi/issues/3200

5. **#5103 — [bug] pi-windows-x64.zip 无法从 PATH 检测到 git-bash**（CLOSED，24 条评论）
   长期存在的 Windows bug 现已解决。体现了 Windows 环境检测痛点集群的典型情况。
   https://github.com/earendil-works/pi/issues/5103

6. **#9262 — find 工具：使用 Windows 路径分隔符的 glob 静默返回空结果**（CLOSED）
   静默失败类 bug：`src\**\*.ts` 返回零结果且无报错，导致 agent 错误地得出文件不存在的结论。现已关闭。
   https://github.com/earendil-works/pi/issues/9262

7. **#11892 — [bug] pi-durable 1.5.0：任务卡在 waiting/completing 状态**（CLOSED）
   pi-durable 中的一个 1.5.0 回归：即使依赖已完成，任务也永远不会离开 `waiting`/`completing` 状态。已用 `MemoryStorage` 复现。已关闭。
   https://github.com/earendil-works/pi/issues/11892

8. **#11969 — [bug, untriaged] Fable 5.2 需要新的请求头**（CLOSED，👍1）
   Anthropic 以 `claude_code_version_too_old`（400）拒绝发往 Fable 5.2 的请求。已快速修复——参见 PR #11970。
   https://github.com/earendil-works/pi/issues/11969

9. **#11926 — [bug, no-action] 瞬态状态被当作永久错误处理**（CLOSED）
   429 被显示为红色并永久记录为硬错误，而非瞬态状态。这对会话日志的 UX 区分很重要。
   https://github.com/earendil-works/pi/issues/11926

10. **#11676 — [inprogress] pi-durable：`Harness.close()` 泄漏 provider 会话资源**（OPEN）
    在一次真实的 Codex 响应之后，Node 进程会保留一个 WebSocket + 5 分钟空闲计时器，阻止进程干净退出。可确定性复现。修复见 PR #11961。
    https://github.com/earendil-works/pi/issues/11676

---

## 4. 关键 PR 进展

1. **#11970 — fix(ai)：为 Fable 5.2 升级 Claude Code user-agent**（CLOSED）
   将声明的 Claude Code 版本从 2.1.280 升至 2.1.321，以消除 `claude_code_version_too_old` 400 错误。直接修复 #11969。
   https://github.com/earendil-works/pi/pull/11970

2. **#11964 — fix(coding-agent)：Windows PS 5.1 上 PowerShell 的原生退出码**（CLOSED）
   解决 #8842：PS 5.1 将 stderr 进度输出误判为失败。使用原生退出码以确保正确性。
   https://github.com/earendil-works/pi/pull/11964

3. **#11967 — fix(coding-agent)：读取/编辑/写入带 BOM 的 UTF-16 文件**（OPEN）
   处理 UTF-16LE/BE BOM 文件（常见于 PS 5 的 `Out-File`、`reg export`）。对 Windows 用户是重大的正确性提升；目前读/写/工具会输出 U+FFFD。
   https://github.com/earendil-works/pi/pull/11967

4. **#11966 — fix(coding-agent)：TUI 绘制时停止向终端写入**（OPEN）
   修复 #11264：失控的 stdout/stderr 写入会破坏 TUI 屏幕状态。交互模式的重要稳定性修复。
   https://github.com/earendil-works/pi/pull/11966

5. **#11968 — fix(tui,coding-agent)：全屏滚动帧 O(viewport)**（OPEN）
   性能修复：全屏滚动每帧都会重建整个会话记录（约 60 fps）。降低为 O(viewport) 渲染。
   https://github.com/earendil-works/pi/pull/11968

6. **#11961 — fix(durable)：harness 关闭时清理 provider 资源**（OPEN）
   在活动任务完成后释放 pi-ai provider 资源，使进程干净退出。解决 #11676。
   https://github.com/earendil-works/pi/pull/11961

7. **#11952 — fix(coding-agent)：bash 单独运行，其他调用并行**（CLOSED）
   将 `bash` 串行化（executionMode: sequential），使配对的 `read` 调用不会读取写到一半的输出文件。修复了一个真实的 agent 竞态条件（#11950）。
   https://github.com/earendil-works/pi/pull/11952

8. **#10969 — fix(coding-agent)：复用未变更的 codemode 高亮**（CLOSED）
   缓存逐行语法高亮；避免在每次结果进度/计时器 tick 时重新高亮整个脚本。codemode 的性能提升。
   https://github.com/earendil-works/pi/pull/10969

9. **#11972 — fix(coding-agent)：在所有 fd 版本上匹配 Windows find 路径模式**（CLOSED）
   使 `find` 路径模式匹配无论安装的 `fd` 版本如何都保持一致（10.5.1 前后 glob 语义不同）。
   https://github.com/earendil-works/pi/pull/11972

10. **#11957 — fix(coding-agent)：在 tool_search 中报告已声明的 MCP 工具**（OPEN）
    `tool_search` 不再对模型已拥有的工具（例如 `direct` MCP 工具）回答“未找到匹配工具”。减少较弱模型的困惑。
    https://github.com/earendil-works/pi/pull/11957

---

## 5. 热门讨论

本数据集中未提供专门的讨论数据。（大部分社区讨论发生在 issue #7547 的评论中；参见上方热门 Issue。）

---

## 6. 功能请求趋势

- **多模态与媒体**：`prompt` RPC 中的视频/音频支持（#3200，获赞最多）；通过文件引用为 Codex 提供图像密集型工具结果（#8617）；用于 413 恢复的聚合图像预算（#8351）。
- **会话/存储管理**：pi-durable 中的永久对话删除（#11026）；为移动的会话持久化所选文件夹（#11060）；修正 JSONL 恢复写入的只读查看器语义（#11939）。
- **工具暴露控制**：在 `/mcp` Tools 视图中按工具启用/禁用 MCP，无需手动编辑 `toolExposure`（#11526）；可操作的 `edit` 失败诊断，区分“文件已更改”和“oldText 不匹配”（#11943）。
- **TUI/UX 打磨**：相对 Markdown 链接 OSC 8 目标修复（#10993）、mintty 修饰键处理（#11935）、通过空 bracketed paste 实现 WSL 图像粘贴（#11259）。

**趋势总结：** Windows 生态对齐占主导，其次是多模态输入处理和更强的面向 agent 的工具结果诊断。

---

## 7. 开发者痛点

- **Windows 是一等痛点领域。** 反复出现的高频主题：PATH/git-bash 检测（#5103）、路径分隔符 glob 静默失败（#9262、#11972）、PowerShell 5.1 stderr→失败误判（#8842、#11964）、安装程序因 `npm warn` stderr 中止（#11944）、UTF-16 BOM 文件（#11967），以及 mintty 中的终端按键处理（#11935）。#7547 线程（95 条评论）是社区要求整合 Windows 支持投入的直接呼声。
- **1.5.0 回归侵蚀信任。** `StringEnum` 类型破坏（#11869，P0）、pi-durable 任务卡死状态（#11892），以及 Nix 用户稳定分支未升级（#11769）都可追溯到 1.5.0 发布窗口——表明发布/QA 卫生需要关注。
- **静默失败是危害最大的 bug 类别。** `find` 无错误地返回零结果（#9262）和 `tool_search` 对真实工具声称无匹配（#11921）会主动误导 agent；开发者希望得到响亮、可操作的错误。
- **资源生命周期泄漏。** `Harness.close()` 留下 WebSocket 和空闲计时器未关闭（#11676），阻碍进程干净退出——这是长期运行/嵌入式使用中的反复出现的一类问题。
- **缺失诊断增加调试时间。** `edit` 工具单一的通用失败消息（#11943）和瞬态错误被记录为永久错误（#11926）是经常被提及的摩擦点。

---

*数据来源：github.com/earendil-works/pi — 生成于 2026-11-10。*

</details>

<details>
<summary><strong>Qwen Code</strong> — <a href="https://github.com/QwenLM/qwen-code">QwenLM/qwen-code</a></summary>

# Qwen Code 社区文摘 — 2026-11-10

## 今日亮点

Qwen Code 发布了 **v0.27.1**，并同步推出配套 SDK 版本。社区高度聚焦于 **Managed Agent / Web Shell** 的扩展（渠道、webhook 投递、会话管理）。与此同时，CI 稳定性仍是问题噪声的最大单一来源——多个 `Managed Agent gate`、`E2E Tests` 和 `Qwen Code CI` 失败正在积极排查中，其中包括因重复 case 标签导致的主分支 Java 编译中断。

---

## 发布

**v0.27.1**（[release](https://github.com/QwenLM/qwen-code/releases)）
- `feat(core)`：活跃 shell 现在会在启动上下文中命名（[#16491](https://github.com/QwenLM/qwen-code/pull/16491)）
- 无已知破坏性变更。

**随此版本捆绑的配套发布：**
- **sdk-typescript v0.1.31** — 捆绑 CLI 0.27.1
- **sdk-typescript v0.1.29** — 补发版本（npm 发布成功，但 release 分支推送步骤失败；见 [#16490](https://github.com/QwenLM/qwen-code/pull/16490)）
- **desktop v0.27.1** — 包含 OpenAI 兼容提供商下 Wally 的提供商文档（[#16529](https://github.com/QwenLM/qwen-code/pull/16529)）

---

## 热门 Issue

1. **[#14724](https://github.com/QwenLM/qwen-code/issues/14724)** — *Main CI 失败：Managed Agent gate — 未识别到测试*（282 条评论）。持续时间最长的 CI 追踪 issue；一个无法归因到具体测试的持续性 gate 失败。噪声高，仍未解决。

2. **[#16712](https://github.com/QwenLM/qwen-code/issues/16712)** — *main 的 Java lane 编译失败 — `SurfaceAdmissionAcceptanceTest` 中重复 case 标签*（P1，已关闭）。通过测试编译失败阻断了**每个** PR 的 Java lane。快速周转（当天关闭）体现了发布阻断问题的响应能力。

3. **[#16683](https://github.com/QwenLM/qwen-code/issues/16683)** — *Qwen Code Desktop 0.27 在 Intel x64 Mac 上失败*（P1）。运行时启动时 V8 致命错误 `Check failed: 12 == (*__error())`。对部分 macOS 用户而言是严重的平台回归。

4. **[#16140](https://github.com/QwenLM/qwen-code/issues/16140)** — *Shell 重定向提取器丢弃 `N>`（fd≥3）、`>\|` 以及无空格的 `cd>file` 形式*（P1，安全）。针对这些重定向写法的 `Write`/`Edit` 拒绝规则无法触发——一类权限绕过 bug。

5. **[#13139](https://github.com/QwenLM/qwen-code/issues/13139)** — *Shell 权限分析器：引号伪影 `cd` 目标残留的幻影 cwd 入口*（P1，安全）。此前 cwd 提权修复的后续；同一家族的三个入口仍未关闭。

6. **[#16757](https://github.com/QwenLM/qwen-code/issues/16757)** — *非 UTF-8 ANSI-C 字节折叠为 U+FFFD，导致 shell 路径守卫错过 bash 实际触碰的文件*（P2，安全）。守卫解码器与 bash 原始字节文件系统语义之间的编码不匹配。

7. **[#15327](https://github.com/QwenLM/qwen-code/issues/15327)** — *tracking(core)：在 `experimental.unifiedAgentLoop` 背后让每个 surface 运行于同一 agent loop*（P1）。跨 CLI、OpenTUI、ACP 和 daemon surface 整合 agent loop 的架构总括 issue——直接驱动了下方多个 PR。

8. **[#16690](https://github.com/QwenLM/qwen-code/issues/16690)** — *GitHub 渠道：暴露 API 预算并保护 mention lane 免受 watch 开销*（P2，已关闭）。预算感知的 GitHub 渠道行为；快速关闭，表明渠道加固正在进行中。

9. **[#16546](https://github.com/QwenLM/qwen-code/issues/16546)** — *feat(managed-agent)：在 WebShell 中暴露侧边栏面板能力*（P2）。对比 control-plane、WebShell 和 data-agent REST surface 的能力一致性矩阵——managed 会话的路线图产物。

10. **[#16766](https://github.com/QwenLM/qwen-code/issues/16766)** — *生成的 UI 卡片在 Desktop（macOS WKWebView）中卡在 "Loading the interactive view…"；在 Chromium 中正常渲染*（P2）。实验性 Generated UI 工具中 Tauri/WKWebView 特有的渲染缺口。

---

## 关键 PR 进展

1. **[#16743](https://github.com/QwenLM/qwen-code/pull/16743)** — *feat(cli)：让 OpenTUI 的模型轮次通过共享 agent loop 发送。* #15327 的 PR 4a；使 OpenTUI 在统一 loop 上与 Ink 达到一致，并扩展 TUI 一致性测试套件。

2. **[#16324](https://github.com/QwenLM/qwen-code/pull/16324)** — *feat(web-shell)：允许 managed 会话复用宿主 shell。* 移除第二个 "managed look" chrome；当 `showManagedSessionHeader: false` 时，managed 页面继承宿主 header/workspace/phase 徽章。

3. **[#16736](https://github.com/QwenLM/qwen-code/pull/16736)** — *feat(web-shell)：通过浏览器内二维码登录配置微信渠道。* 微信加入渠道平台列表，支持二维码渲染、扫描状态上报和账户面板。

4. **[#16724](https://github.com/QwenLM/qwen-code/pull/16724)** — *feat(channels)：二维码登录管理契约 + QQ 配置 surface。* 为渠道管理契约增加可选的交互式二维码登录能力，值可持久化于 daemon。

5. **[#16758](https://github.com/QwenLM/qwen-code/pull/16758)** — *feat(managed-agent)：私有 BTW 与会话回顾。* 持久、参与者私有的后续跟进以及手动/自动会话回顾，包括 Runtime 已被回收的 ACTIVE 会话。

6. **[#16023](https://github.com/QwenLM/qwen-code/pull/16023)** — *feat(managed-agent)：跨会话共享常驻外部 Workspace。* 将 Workspace 关闭变为成员关系释放而非拆除——一项有意义的资源效率变更。

7. **[#16498](https://github.com/QwenLM/qwen-code/pull/16498)** — *feat(managed-agent)：Tool v4 执行度量。* 经证明的、有界的数值度量，随结果冻结并在 pre-ack 前持久化；为诊断面板提供数据。

8. **[#15936](https://github.com/QwenLM/qwen-code/pull/15936)** — *fix(core)：减少或拒绝无法适配窗口的压缩请求。* 强制执行 4,096 token 的最小摘要预留，并在上下文长度被拒时恢复一次。

9. **[#16598](https://github.com/QwenLM/qwen-code/pull/16598)** — *fix(core)：将持久化输出存根排除在 microcompaction 清理之外。* 停止用裸 cleared 占位符替换溢出存根（路径、sha256、预览）。

10. **[#14360](https://github.com/QwenLM/qwen-code/pull/14360)** — *fix(core)：将 `git remote show` 视为非只读。* 关闭了一个自动模式出口绕过——一个涉及网络的命令曾在只读快速路径上被自动批准。

---

## 热门讨论

本数据集中未提供 GitHub Discussions 数据；本节省略。

---

## 功能请求趋势

- **渠道与 webhook 生态扩展** — 一组明确的请求（#16751、#16752、#16753、#16724、#16736）要求出站平台 webhook（钉钉优先）、可配置投递目标、各平台连接方式以及 Web Shell 配置流程。微信和 QQ 是领先的平台集成。
- **跨 surface 的统一 agent loop** — #15327 及其 PR 链（#16743）代表了主导架构方向：一个 `packages/core` loop 服务于 CLI、TUI、ACP、daemon 和 Web Shell。
- **Managed Agent 会话生命周期** — 对侧边栏能力一致性（#16546）、常驻 workspace 复用（#16023）、私有 BTW/回顾（#16758）以及会话复用宿主 chrome（#16324）的请求，指向多会话管理的成熟化。
- **集成的 API 预算 / 轮询效率** — #16690、#16691（条件请求 / ETag）和 #16692（批量 GraphQL PR watch）显示出对 GitHub 渠道中速率限制感知、低成本轮询的需求。

---

## 开发者痛点

- **CI 不稳定主导 issue 追踪器。** `Managed Agent gate`、`E2E Tests` 和 `Qwen Code CI` 失败产生了评论最多的线程（#14724、#16672、#11680、#11678），常常是 "no test identified"——使回归难以归因，拖慢每个 PR。
- **主分支中断具有真实爆炸半径。** #16712（重复 case 标签）在修复前阻断了所有 PR 的 Java lane——反复的主分支中断侵蚀贡献者信心。
- **Shell 权限分析器边界情况反复出现。** 多个 P1 安全 issue（#16140、#13139、#16757）针对重定向解析、引号伪影和编码不匹配——权限守卫子系统中持续的正确性负担。
- **平台特定回归** — macOS Intel x64 启动崩溃（#16683）和 WKWebView 渲染失败（#16766）表明 Desktop 打包/webview 覆盖存在缺口。
- **会话状态与遥测保真度** — 在干净的工具调用轮次结束时出现虚假的 "request was interrupted" 横幅（#16767），以及 TPS 排除首 token 时间（#16714），反映了状态上报和指标方面用户可见的信任问题。

</details>

---
*本日报由 [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar) 自动生成。*