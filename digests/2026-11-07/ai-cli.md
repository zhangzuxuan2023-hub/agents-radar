# AI CLI 工具社区动态日报 2026-11-07

> 生成时间: 2026-11-06 23:16 UTC | 覆盖工具: 7 个

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

# 跨工具对比报告 — AI CLI 开发者工具
**数据窗口：** 2026-11-06 → 2026-11-07 · 来源：Claude Code、OpenAI Codex、Gemini CLI、GitHub Copilot CLI、OpenCode、Pi、Qwen Code 的公开 GitHub 摘要

---

## 1. 生态概览

AI CLI 工具版图已围绕一个共同的架构内核完成整合——代理式终端循环、基于 MCP 的工具集成、子代理编排，以及技能/插件打包——而竞争前沿已从*功能是否存在*转向*规模化下的运行可靠性*。本窗口内每一个追踪器都被同一类投诉所主导：静默失败（代理在失败时报告成功）、状态/策略漂移（配置说一套、运行时做一套）、长会话中的资源泄漏，以及 MCP/OAuth 生命周期脆弱性。差异化如今越来越少体现在核心能力上，而更多体现在平台成熟度（Windows 普遍仍是最薄弱面）、企业认证（Entra/BYOK）和生态易用性（插件标准、跨代理技能可移植性）上。与此同时，两个宏观安全信号格外突出：Anthropic 的 User-Agent 邮件泄露争议，以及多个工具的安全过滤器误报——这表明信任与隐私正成为一等一的产品关切，而非边缘案例。

---

## 2. 活跃度对比

| 工具 | Issue（24 小时，已更新） | PR（24 小时） | Discussion | 发布状态 |
|---|---|---|---|---|
| **Claude Code** | 约 10 个热点 + 大规模 `stale` 清理关闭 | 1（已关闭） | 不适用（未提供） | ✅ **v2.1.319**（自动模式加固） |
| **OpenAI Codex** | 10 个热点（+ 新增重复 #59895） | 20（全部已关闭，机器人提交） | ✅ 活跃 — Q&A + Show & Tell | ✅ **rust-v0.167.0** + **v0.169.0-alpha.2** |
| **Gemini CLI** | 10 个热点（P1 回归，`need-retesting`） | 10（多项已关闭，XL 稳定性修复） | 不适用（未提供） | ⏸️ 无发布 |
| **Copilot CLI** | 10 个热点 + 5 个已关闭 | 1（开启，文档） | 不适用（未提供） | ✅ **v1.0.107 / v1.0.106**（Kitty 图像、`/code`） |
| **OpenCode** | 10 个热点（大量分诊，6 个已关闭） | 10（CODEmode VM、TUI 修复） | 不适用（摘要省略） | ⏸️ 无发布（清理积压） |
| **Pi** | 50 个已更新（10 个热点） | 21 个已更新（10 个精选，多数已关闭） | ✅ Q&A + Show & Tell（轻量） | ⏸️ 无发布 |
| **Qwen Code** | 10 个热点（2 个 P1） | 10（1 个 P1 修复进行中） | 不适用（未提供） | ✅ **v0.26.9**（技能安装） |

**备注：**
- Claude Code 和 Copilot CLI 实际上**仅有 Issue+PR**；未出现 Discussion 数据。
- **Pi 的原始变更量最高**（50 个 Issue / 21 个 PR），由机器人+维护者的修复节奏驱动。
- **Codex 和 Pi 是仅有的 Discussion 实质活跃的工具**（Q&A、Show & Tell），其功能是作为次级生态渠道而非缺陷渠道。
- 7 个工具中仅 **3 个**在本 24 小时窗口内发布了版本；Copilot 的变更日志滞后于其版本号更新。

---

## 3. 共性功能方向

| 方向 | 工具 | 具体需求 |
|---|---|---|
| **子代理编排的正确性与控制** | Gemini、OpenCode、Claude Code、Qwen | 在 `MAX_TURNS`/后台工作上误报 `GOAL`/成功（Gemini #22323、OpenCode #48826）；本地模型的并行子代理上限（OpenCode #27110，42 👍）；后台代理的审批呈现（Qwen #16425） |
| **消除静默失败** | 全部 7 个 | 看似正常实则行为异常的代理：`[cleared]` 上下文丢失（Claude #107979）；OAuth 状态陈旧显示"已连接"（Copilot #5366）；代理静默掉线（OpenCode #54200、#62415）；文件损坏却报告成功（Pi #11894） |
| **MCP 生命周期与认证健壮性** | Claude、Codex、Copilot、OpenCode、Qwen、Pi | 有效的 `tools/list` 被拒绝（Claude #107111，128 工具硬上限 #24246）；每线程 MCP 进程泄漏（Codex #30408/#12491）；Entra token 缓存失败（Copilot #5236/#5250/#5366）；OAuth 端口冲突（Qwen #16457）；OAuth 对话框往返（OpenCode #62245） |
| **跨代理 / 插件可移植性** | Codex、OpenCode、Pi | Agent Plugins 规范（OpenCode #40993，21 👍）；Tessera 多代理运行器 + 跨 Codex/Claude/OpenClaw/Hermes 的技能同步（Codex #59896）；AgentKeeper 技能去重（Pi #11870） |
| **规模化下的技能成熟度** | Claude、Codex、Gemini、Copilot、Pi | 子目录命名空间（Claude #10238，168 👍）；主动技能调用（#21968）；技能被内置斜杠命令遮蔽（Copilot #5375）；跨代理漂移（Pi #11870） |
| **长会话稳定性** | Claude、Codex、Gemini、Copilot、Pi、Qwen | FD 泄漏（Gemini #30184）、V8 堆 OOM（#30180）、快照二次增长（Qwen #13113）；9–37 GB MCP RSS（Codex）；macOS 上的 CPU 波动（Pi #7730）；5 分钟缓存 TTL 重复计费（Copilot #5220） |
| **编程式 / 无头对等性** | Claude、Codex、Copilot、Pi | 远程 `create_session` 的 `effort`（Claude #107292）；`ToolPolicy` 强制执行（Codex #57025）；`--server` 中 BYOK 被忽略（Copilot #5371）；ACP 中的 OTel（#5376）；可组合的 `systemPromptOptions`（Pi #11317） |
| **终端输入/IME 正确性** | Copilot（突出），其他隐性 | 可配置提交键（Copilot #1972，48 👍）；Shift+Enter 换行语义（#2776） |
| **Windows 对等性** | Codex、Gemini、Copilot、Qwen、Pi | Windows 普遍主导缺陷量：项目丢失（Codex #42739）、shell 假设问题（Qwen #15310）、SIGHUP 清理（Pi #11889）、PATH/引号处理（Pi #11873） |

**方向解读：** 共享路线图正从*能力*转向*保证*——强制策略、可观测健康度、优雅降级，以及可证明的代理结果。整个生态中得票最高的请求是易于实现的配置上限和诚实的错误报告，而非新功能。

---

## 4. 差异化分析

| 工具 | 功能重点 | 目标用户 | 技术路线 |
|---|---|---|---|
| **Claude Code** | 隐私/安全加固、技能、MCP 集成者 | 企业与安全敏感型开发者 | 服务端需验证的自动模式；内置 `privacy` 模组作为缓解手段；策略强制执行优先于回退 |
| **OpenAI Codex** | 云↔本地融合、Guardian 审查系统 | 平台/SDK 使用者、机器人运营工作流 | 本地 GitHub 项目传递至云端预热（#60007）；SQLite 写路径优化；连贯的 Guardian 审查栈（证据截断、有界投递） |
| **Gemini CLI** | AST 感知工具、沙箱、子代理质量 | 关注沙箱、大型代码库用户 | 零依赖 OS 沙箱提案；macOS Seatbelt 修复；节省 token 的上下文管理；大批稳定性 PR |
| **Copilot CLI** | 终端原生多模态、IDE 交接、企业认证 | 以 GitHub 为中心的企业 | Kitty 内联图像；`/code` 交接至 VS Code；聚焦 Entra/BYOK；Fusion 路由层 |
| **OpenCode** | 可扩展性（插件/规范）、桌面端对等 | 插件生态、本地模型用户 | 支持 Agent Plugins 标准；Code Mode VM（`suspend/resume`、默认开启开发模式）；显式插件 API |
| **Pi** | SDK/扩展 API、provider 适配器广度 | 扩展作者、非第一方网关 | `robopipi` 机器人与维护者配对 → 高速微修复；广泛的 provider 目录；强类型聚焦 |
| **Qwen Code** | 技能打包、受管代理可观测性 | 自动化/无头用户、中文生态 | 技能 ZIP 安装 + daemon/SDK API；受管代理持久化；通过调用解析实现权限加固 |

**关键对比：**
- **Claude Code 和 Gemini CLI** 最为*安全优先*——Claude 推出隐私模组并加固自动模式；Gemini 提出 OS 沙箱。
- **Codex 最具平台建设性**——Guardian 审查是一种新颖、可审计的审查架构；云/本地线程统一是战略性押注。
- **OpenCode 和 Pi 最具*生态开放性***——两者都在插件/SDK 和跨代理可移植性上领先；OpenCode 瞄准规范标准化，Pi 瞄准 provider 广度。
- **Qwen Code 最具*自动化导向***——受管代理诊断和技能分发面向无头/CI 工作负载。
- **Copilot CLI** 是*企业认证*最强的一手（Entra、BYOK、Fusion），但也是输入 UX 最受挫的一个。

---

## 5. 社区动能与成熟度

**速度（粗略排名）：**
1. **Pi** — 变更量最高（50 Issue/21 PR），激进的当日修复节奏；扩展 API 回归（`StringEnum` p0）显示边界快速变动。**快速迭代，平台测试较薄弱。**
2. **OpenCode** — 繁重的分诊日（关闭 6 个 Issue），活跃的 Code Mode VM 和 TUI 工作；维护者对长期请求响应积极（#62437 复活 #42552）。**正走向一次发布。**
3. **OpenAI Codex** — 连贯的 PR 功能栈和一致的发布（0.167、0.169-alpha）；唯一拥有结构化 Guardian 审查架构的追踪器。**成熟度高，但每线程泄漏仍是架构债务。**
4. **Claude Code** — 高信号 Issue，但大量 `stale` 关闭带有真实复现，表明分诊压力大于解决。**成熟度高；隐私事件后社区信任风险上升。**
5. **Gemini CLI** — P1 被搁置在 `need-retesting`，PR 批次强劲。**维护者带宽是制约因素。**
6. **Qwen Code** — CI 不稳定（#14724，217 条评论）占据注意力；发布节奏稳定。**功能速度受基础设施不稳定制约。**
7. **Copilot CLI** — PR 面最小（1 个文档 PR），功能通过发布管线落地。**企业功能趋于成熟；输入 UX 反馈积压是信誉风险。**

**动能信号：**
- **参与度最高的单一线程：** Claude Code #107757（686 👍）——信任事件，而非功能。
- **得票最高的功能请求：** OpenCode 并行子代理上限（42 👍）> Claude 技能子目录（168 👍，但已关闭）> Copilot 提交键（48 👍）。
- **跨工具：输入/IME 无障碍**（Copilot 48 👍）是仍被遗漏的最廉价高可见度修复。

---

## 6. 趋势信号

1. **可靠性是新的基准。** 在所有七个追踪器中，首要痛点是*静默失败类*——虚假成功报告、过时的能力状态、配置丢失，或文件损坏却伴随成功消息（Gemini、Copilot、OpenCode、Pi、Claude）。对开发者而言：优先选择具有显式错误面和健康探测的工具；构建自己的验证关卡（测试、diff），而非信任代理的自我报告。

2. **MCP 是生态脆弱性的震中。** 七个工具中有六个报告 MCP 认证/进程/生命周期问题。**对采纳者的建议：** 固定 MCP 服务器版本，监控每进程 RSS，并预期网关/私有云用户会最先遭遇 Claude 自动模式的行为变化——在预发布环境验证。

3. **每线程资源泄漏是系统性的，而非偶发。** Codex 的 9–37 GB RSS 报告，加上 Gemini 的 FD/堆泄漏和 Pi 的进程树缺口，表明**长会话需要进程监管**。开发者应将代理运行时视为需要外部看门狗的守护进程，而非一次性进程。

4. **跨代理可移植性正在整合。** Tessera、AgentKeeper、Agent Plugins 规范，以及跨 Codex/Claude/OpenClaw 的技能同步，都指向一个日趋成熟的**多代理编排层**。预计"一次编写、跨代理运行"的打包方式（技能、MCP 配置、策略）将在 2027 年成为竞争轴。

5. **静默模型升级侵蚀信任。** Claude 用户二分排查 Opus 5.5 回归（#108048）、Codex 压缩权重投诉（#59971），以及 Gemini 技能调用不足（#21968），显示对**跨模型版本行为漂移**的敏感性。在生产中固定模型版本；要求以变更日志驱动的升级。

6. **隐私与安全是信誉事件。** User-Agent 泄漏（Claude #107757）和安全过滤器误报（Codex #56475、Gemini #22672）正消耗社区注意力。企业应在推广*之前*审视遥测头信息和覆盖路径；预期这将影响采购。

7. **输入/无障碍被系统性地投入不足。** IME 冲突、Shift+Enter、听写和屏幕阅读器刷屏是廉价的修复，却带来超额的信任红利——这表明**终端人体工学仍是面向非英语和无障碍敏感用户的竞争差异化因素**。

**给决策者的底线：** 生态的核心能力已趋同；差异化因素现在是**运行可靠性、认证/企业姿态和生态可移植性**。为进程监管预留预算，固定模型版本，偏好显式而非静默的行为，并在评估工具时同等看重其失败模式的透明度与功能清单。

---

## 各工具详细报告

<details>
<summary><strong>Claude Code</strong> — <a href="https://github.com/anthropics/claude-code">anthropics/claude-code</a></summary>

## Claude Code Skills 社区热点

> 数据来源: [anthropics/skills](https://github.com/anthropics/skills)

# Claude Code Skills — 社区亮点报告
*数据快照：2026-11-07 · 来源：github.com/anthropics/skills*

> **数据说明：** PR 数据集中所有条目的 `Comments: undefined`，因此下文的 PR 排名基于**更新新鲜度、关联 Issue 活跃度、维护者关注领域**的复合信号（PR 列表按数据集头部说明已按评论数预排序）。Issue 排名使用实际评论数。

---

## 1. 热门 Skills 排名

本快照中关注度最高的 Skill 提交与修复——以**现有**官方 Skill 的加固为主，而非全新 Skill：

| # | Skill / PR | 功能 | 讨论亮点 | 状态 |
|---|---|---|---|---|
| 1 | **[mcp-builder] streamable_http_client 修复** ([#1742](https://github.com/anthropics/skills/pull/1742)) | 构建 MCP 服务器；修复适配 `mcp>=2.0.0` API 重命名（`streamablehttp_client` → `streamable_http_client`）以及通过 `create_mcp_http_client` 配置自定义 header。 | 最新鲜、持续更新（2026 年 10 月）；关闭 #1668。直接关联 Issue [#1390](https://github.com/anthropics/skills/issues/1390)（评估 harness 评分为 0/N）。 | OPEN |
| 2 | **[skill-creator] 触发评估隔离 + Windows/运行时修复** ([#1298](https://github.com/anthropics/skills/pull/1298)) | 修复触发评估中的误判漏检：每 worker 探测争用、Windows 下管道 `select()`、运行时失败被误判为未触发。 | 关联 Issue [#556](https://github.com/anthropics/skills/issues/556)（0% 触发率）和 [!202](https://github.com/anthropics/skills/issues/202)（skill-creator 应遵循最佳实践）。 | OPEN |
| 3 | **[webapp-testing] 移除 `shell=True`** ([#1980](https://github.com/anthropics/skills/pull/1980)) | `with_server.py` 的安全加固：将 shell 派生的服务器命令替换为安全的参数列表调用（CWE-78 命令注入）。 | 聚焦安全；持续更新至 2026 年 11 月。 | OPEN |
| 4 | **[docx] LibreOffice 超时 + 输出验证** ([#1792](https://github.com/anthropics/skills/pull/1792)) | `accept_changes.py` 现在在 `soffice` 超时时报错，并在声称成功前验证修订标记已清除。 | docx 正确性修复集群的一部分（参见 [#1734](https://github.com/anthropics/skills/pull/1734) 孤立评论、Issue [#2018](https://github.com/anthropics/skills/issues/2018)）。 | OPEN |
| 5 | **[xlsx] recalc.py 中的外部链接解析** ([#2019](https://github.com/anthropics/skills/pull/2019)) | 通过解析工作表作用域的已定义名称/别名，修复 `external_links_at_risk` 中的误判漏检。 | 关闭 Issue [#2013](https://github.com/anthropics/skills/issues/2013)；活跃至 11 月 3 日。 | OPEN |
| 6 | **[pyxel] 复古游戏开发** ([#525](https://github.com/anthropics/skills/pull/525)) | 全新 Skill，用于构建/调试/验证复古 Python 游戏：无头输入运行、帧检查、状态检查。 | 长期存在（2026 年 3 月）但仍在 11 月 1 日更新——对创意/游戏开发类 Skill 的持续兴趣。 | OPEN |
| 7 | **document-typography** ([#514](https://github.com/anthropics/skills/pull/514)) | AI 生成文档的质量控制：孤词换行、孤行段落、编号错位。 | 文档类 Skill 家族中广泛关注的新 Skill。 | OPEN（自 3 月起停滞） |
| 8 | **notion-spec-to-implementation + quantitative-resume-auditor** ([#1245](https://github.com/anthropics/skills/pull/1245)) | 将 Notion 规格转为带验收标准的可执行任务；定量审计简历。 | 双 Skill 组合；10 月 15 日更新。 | OPEN |

**模式：** 8 个热门条目中有 6 个是**对官方 Skill 的修复**（mcp-builder、skill-creator、webapp-testing、docx、xlsx）——社区更多投入于*已发布工具包的可靠性*，而非全新 Skill。

---

## 2. 社区需求趋势

从 Issue 跟踪器提炼（按评论数前 15）：

1. **安全与命名空间信任** — 最响亮的讨论线：Issue **[#492](https://github.com/anthropics/skills/issues/492)**（46 条评论，3 👍）指出社区 Skill 冒充 `anthropic/` 命名空间，造成信任边界/提权风险。相关：**[#1394](https://github.com/anthropics/skills/issues/1394)** eval-viewer XSS、**[#1980](https://github.com/anthropics/skills/pull/1980)** 命令注入。→ *需求：Skill 的供应链安全、来源追溯与沙箱化。*

2. **团队/组织分发与共享** — **[#228](https://github.com/anthropics/skills/issues/228)**（17 条评论，8 👍，赞同最高）要求组织级 Skill 共享，而非手动下载 `.skill` + 设置上传。相关：**[#189](https://github.com/anthropics/skills/issues/189)**（9 👍）重叠插件导致重复安装。→ *需求：共享 Skill 库 / 注册中心。*

3. **Agent 状态与内存效率** — **[#1329](https://github.com/anthropics/skills/issues/1329)** 提议 `compact-memory`（用符号记法压缩 Agent 状态）；**[#1487](https://github.com/anthropics/skills/issues/1487)** 报告 `claude-api` 急切入注入约 156k tokens。→ *需求：上下文预算感知的 Skill / 内存 Skill。*

4. **输出质量门禁** — **[#1385](https://github.com/anthropics/skills/issues/1385)** 提议三道门禁流水线（任务前校准 → 对抗性审查 → 交付验证）；**[#412](https://github.com/anthropics/skills/issues/412)**（已关闭）推动了 Agent 治理模式。→ *需求：推理/质量审查与治理类 Skill。*

5. **Skill 编写体验** — **[#202](https://github.com/anthropics/skills/issues/202)**（已关闭）和 **[#556](https://github.com/anthropics/skills/issues/556)**（13 条评论，7 👍，0% 触发率）指出 skill-creator 过于冗长，触发评估已损坏。→ *需求：更好的编写 + 评估工具。*

6. **文档类 Skill 正确性** — docx/xlsx/pptx/slack-gif 缺陷集群（**[#2018](https://github.com/anthropics/skills/issues/2018)**、**[#1868](https://github.com/anthropics/skills/issues/1868)**、**#1809**、**#1891**、**#1897**）——CDN/大小写敏感、XML 有效性、渐变计算。→ *需求：文档家族的产线级可靠性。*

**按信号强度排序的方向总结：**（1）Skill 安全/来源追溯，（2）组织共享/注册中心，（3）内存与上下文效率，（4）质量/审查门禁，（5）编写/评估工具。

---

## 3. 高潜力待合入 Skill（开放且活跃，可能很快落地）

这些尚未合入，但显示出近期活动、范围紧凑或关联修复——值得关注：

| PR | Skill | 高潜力原因 | 最后更新 |
|---|---|---|---|
| [#1742](https://github.com/anthropics/skills/pull/1742) | mcp-builder（修复） | 干净的单 Issue 修复，关联 MCP v2 破坏性变更；与维护者相关。 | 2026-10-08 |
| [#1980](https://github.com/anthropics/skills/pull/1980) | webapp-testing（安全） | 解决真实 CVE；可能快速通道。 | 2026-11-01 |
| [#1792](https://github.com/anthropics/skills/pull/1792) | docx（修复） | 增加验证，补齐正确性缺口。 | 2026-11-01 |
| [#2019](https://github.com/anthropics/skills/pull/2019) | xlsx（修复） | 关闭 #2013；范围窄且可测试。 | 2026-11-03 |
| [#1977](https://github.com/anthropics/skills/pull/1977) · [#1976](https://github.com/anthropics/skills/pull/1976) · [#1975](https://github.com/anthropics/skills/pull/1975) | algorithmic-art / webapp-testing / pptx（修复） | 单一作者的三项小缺陷修复（环绕、元素类型、参数校验）——易于审查。 | 2026-10-06–27 |
| [#1997](https://github.com/anthropics/skills/pull/1997) | skill-creator（YAML 解析器） | frontmatter 解析的正确性；核心编写路径。 | 2026-10-19 |
| [#1245](https://github.com/anthropics/skills/pull/1245) | notion-spec-to-implementation + quantitative-resume | 高产的双 Skill 组合；持续更新。 | 2026-10-15 |
| [#525](https://github.com/anthropics/skills/pull/525) | pyxel | 持续兴趣，独特领域（游戏开发）。 | 2026-11-01 |

**注意：** 若干全新 Skill（**[#514](https://github.com/anthropics/skills/pull/514)**、**[#538](https://github.com/anthropics/skills/pull/538)**、**[#1615](https://github.com/anthropics/skills/pull/1615)**、**[#1771](https://github.com/anthropics/skills/pull/1771)**）已停滞或信号较弱，可能需要社区 👍 才能推进。**修复类 PR**、而非新 Skill PR，推进最快。

---

## 4. Skills 生态洞察

**社区最集中的需求不是新 Skill，而是信任与可靠性基础设施：为已发布的 Skill 提供安全、命名空间安全、上下文高效且可共享的打包方式——这证明生态正从“更多能力”走向“可靠、可治理的能力”。**

---

*GitHub 来源：anthropics/skills · 本汇编反映截至 2026-11-07 的数据。PR 的评论数在源数据切片中未暴露；排名使用新鲜度、关联 Issue 信号与数据集排序。*

---

# Claude Code 社区摘要 — 2026-11-07

## 1. 今日亮点

今天最主要的新闻是一起严重的隐私事件：Issue #107757 报告 Claude Code 在进行网络搜索时通过 `User-Agent` 请求头泄露用户邮箱地址，该议题已飙升至 **686 👍 和 98 条评论**——是本期窗口内互动量最高的议题。Anthropic 还发布了 **v2.1.319**，收紧了“自动模式”的行为：当遥测/功能标志关闭时，服务器无法验证的调用不再由内置分类器静默分类。与此同时，维护者对陈旧议题进行了一轮大规模清理（许多 9 月的报告以 `stale` 状态关闭），表明在隐私风波之外，团队正在进行积极的议题分诊整理工作。

## 2. 版本发布

**v2.1.319** — 自动模式加固
- 当遥测或功能标志关闭时（包括部署在 LLM 网关后的场景），自动模式中服务器无法检查的调用现在会**被询问或被拒绝**，**不再回退**到内置分类器。
- 逃生通道：设置 `CLAUDE_CODE_AUTO_MODE_SERVER=0` 可保留此前内置分类器的行为。
- Bedrock、Vertex 和 Foundry 部署保持不变（“uncha[nged]”）。
- **对网关/私有云用户的影响：** 这是一项行为变更——除非明确选择退出，否则将出现更多权限提示。建议在正式推广前先在预发布环境验证。

## 3. 热门议题

1. **[#107757](https://github.com/anthropics/claude-code/issues/107757) — User-Agent 请求头中的隐私泄露**（OPEN，98 条评论，👍686）
   今日信号最强的议题。报告者因 Claude Code 在网络搜索期间未经同意通过 `User-Agent` 向第三方泄露其邮箱而收到诈骗邮件。报告者主张应彻底移除该行为，而非改为选择加入/退出。社区大量附议——应将其视为产品的可信度问题。

2. **[#10238](https://github.com/anthropics/claude-code/issues/10238) — 支持 skills 中的子目录**（CLOSED，55 条评论，👍168）
   长期存在的增强请求终于关闭。团队希望将 skills 组织到嵌套文件夹中，而不是扁平命名空间。高点赞数表明这对重度使用 skills 的用户是真正的工作流阻塞点。

3. **[#91495](https://github.com/anthropics/claude-code/issues/91495) — 内置浏览器（桌面端）忽略“允许所有网站”设置**（CLOSED，33 条评论）
   权限设置被桌面端浏览器静默忽略——这是配置策略与实际执行之间的信任问题。经过长时间讨论后关闭。

4. **[#107111](https://github.com/anthropics/claude-code/issues/107111) — HTTP MCP：合法的 `tools/list` 被拒绝，报错“Error POSTing to endpoint”**（OPEN，有复现步骤）
   Google 的远程 Gmail MCP 服务器可以连接并完成认证，但 Claude Code 丢弃了合法的 `tools/list` 响应（协议 2026-07-28）。`/mcp` 显示“tools fetch failed · connected”。这阻断了整个远程 MCP 生态模式——对 MCP 集成者而言严重性很高。

5. **[#107979](https://github.com/anthropics/claude-code/issues/107979) — 长会话将轮次显示为“[cleared]”，剥离粘贴的内容/截图**（OPEN，Windows/桌面端）
   对话中途的上下文丢失会静默地从模型上下文中移除用户提供的工件。在长时间桌面端会话中可复现——这对代理式工作流是数据完整性问题。

6. **[#107267](https://github.com/anthropics/claude-code/issues/107267) — CPU 负载下 Windows Terminal 输入/滚动冻结（2.1.318 回归）**（OPEN，perf:cpu）
   疑似是 #63504 在完整屏 TUI 下的回归。影响在中等配置硬件上运行多个 MCP 服务器的用户——随着 MCP 采用率增长，这是一个叠加性的性能问题。

7. **[#108048](https://github.com/anthropics/claude-code/issues/108048) — Opus 5.5 的 `reasoning_extraction` 拒绝读取用户自己的笔记文件**（OPEN，已二分定位到 30 行区块）
   由用户作用域规则文件触发的模型级拒绝；在 2.1.316–2.1.319 上可复现，在 Opus 4.8 上不存在。一份精确、二分定位良好的报告，对任何依赖项目/用户规则文件的人都很重要。

8. **[#107388](https://github.com/anthropics/claude-code/issues/107388) — 桌面端 SSH 会话：已发布的 Artifact 无法在侧边栏中打开**（OPEN，回归）
   Artifact 的 `open` 操作在 SSH 会话中不再工作；仅显示内联卡片。这是影响远程开发工作流的回归。

9. **[#104003](https://github.com/anthropics/claude-code/issues/104003) — 桌面端（Windows）听写自 2026-10-21 起“lost the connection”**（OPEN）
   Code 标签页中大多数会话的听写持续失败——这是一个无障碍/输入回归，且有明确的起始日期可供二分定位。

10. **[#107292](https://github.com/anthropics/claude-code/issues/107292) — 为 claude-code-remote 的 `create_session` 增加 `effort` 参数**（OPEN，增强请求）
    请求本地会话的 effort 控制与远程创建的会话之间实现功能对等。虽小，但表明对程序化会话编排的需求正在增长。

*重要背景：* 一大批较旧的议题（例如 [#91538](https://github.com/anthropics/claude-code/issues/91538) 退出时的 SSH 连接风暴、[#91558](https://github.com/anthropics/claude-code/issues/91558) 主检出的字面 `--git-common-dir` 目录、[#91561](https://github.com/anthropics/claude-code/issues/91561) Cowork 工作区工具被永久拒绝）以 stale 状态关闭——其中一些带有真实复现——表明这是分诊压力而非问题解决。

## 4. 关键 PR 进展

过去 24 小时内仅有 **一个 PR** 更新：

1. **[#107768](https://github.com/anthropics/claude-code/pull/107768) — mods：添加隐私 mod**（CLOSED，作者 poteat）
   在 `mods/privacy` 下公开了 Claude Code 内置 `privacy` mod 的源码。值得注意的是，该 mod 包含的钩子会**拒绝携带已登录账号邮箱地址且包含 User-Agent 的 shell 调用**——直接关联 #107757 的 User-Agent 争议。其时机和关闭状态值得注意：社区实际上被展示了针对正在被报告的泄露的内部缓解措施。值得关注的是，该 mod 会成为官方认可的变通方案，还是会被核心修复取代。

## 5. 热门讨论

本数据集未提供 Discussions 数据；本节省略。

## 6. 功能请求趋势

从 Issues（包括已关闭/陈旧的）中提炼：

- **Skills 系统成熟度** — skills 中的子目录支持（[#10238](https://github.com/anthropics/claude-code/issues/10238)）是点赞最高的请求；用户正在触及 skills 模型的规模上限。
- **MCP 认证易用性** — MCP 服务器的惰性/按需 OAuth 重新认证（[#91548](https://github.com/anthropics/claude-code/issues/91548)）反映了长生命周期 MCP 配置中 token 过期的摩擦。
- **程序化会话控制** — 远程 `create_session` 的 `effort` 参数（[#107292](https://github.com/anthropics/claude-code/issues/107292)）；对无头/编排式控制面的需求。
- **权限模型优化** — 允许在 `additionalDirectories` 内执行 `cd + git` 而无需提示（[#91568](https://github.com/anthropics/claude-code/issues/91568)）；减少对受信任、已配置路径的不必要审批摩擦。
- **安全审核质量** — 语义（而非关键词）内容检测（[#91579](https://github.com/anthropics/claude-code/issues/91579)）；减少对合法任务的误报。
- **平台/可移植性** — Linux 上的 XDG 路径合规（[#91570](https://github.com/anthropics/claude-code/issues/91570)）。

**方向性解读：** 社区正从“功能是否存在”转向“它能否在规模化下组合运作”——skills 组织、MCP 生命周期和程序化会话 API 是新兴的前沿。

## 7. 开发者痛点

- **隐私与数据披露（最高优先级）。** User-Agent 邮箱泄露（#107757）是本期窗口内最明显的信任破坏，并主导了互动量。用户期望在核心层面移除，而非一个设置项。
- **MCP 集成脆弱性。** 合法协议响应被拒绝（#107111），加上 OAuth 重新认证摩擦，使远程 MCP 服务器显得不可靠——这对生态系统的增长构成风险。
- **静默的状态/策略违背。** “允许所有网站”被忽略（#91495）、轮次被静默 `[cleared]`（#107979）、以及仪表盘提示在已认证会话上触发（#91576）共享同一主题：*配置说的是一回事，运行时做的是另一回事。* 这比崩溃更侵蚀信任。
- **Windows 上 TUI 的性能/回归**，伴随多个 MCP 服务器（#107267），以及一系列回归模式（#107388 SSH 上的 Artifacts）。
- **Opus 5.5 上的模型行为回归**（#108048 拒绝、#91563 指令遵循）——用户正在跨模型版本进行二分定位，表明对静默模型升级的敏感性。
- **分诊疲劳信号。** 一波 `stale` 关闭——包括带有可复现 bug 和复现步骤的议题——表明社区可能认为真实 bug 被搁置老化而非修复。请关注 #107757 是否获得实质性回应，以避免加剧这种观感。

---

*基于 github.com/anthropics/claude-code 2026-11-07 数据生成。Issue/PR 数量反映过去 24 小时内更新的条目。*

</details>

<details>
<summary><strong>OpenAI Codex</strong> — <a href="https://github.com/openai/codex">openai/codex</a></summary>

# OpenAI Codex 社区文摘 — 2026-11-07

## 1. 今日看点

社区的注意力分散在两方面：一方面是源源不断的桌面应用回归问题（Windows 项目丢失、macOS 内存膨胀、浏览器/MCP 初始化失败），另一方面是期待已久的 CLI 易用性改进：新的 `codex agents --no-daemon` 命令中心和跨后端切换功能已进入 `rust-v0.167.0`。值得注意的是，PR #60007 将云端线程连接到本地 GitHub 项目，标志着本地与云端执行路径的融合。与此同时，两个长期存在的 MCP 进程/内存泄漏问题（#30408、#12491）在数周后仍未解决，社区压力持续累积。

---

## 2. 版本发布

过去 24 小时发布了两个版本：

- **`rust-v0.167.0`** — 功能版本：
  - **Agent 命令中心**：`codex agents --no-daemon` 打开会话选择器，现在会标记未读结果（#58238、#58239、#58263、#58264）。
  - **跨后端会话切换**：用户可直接在终端会话选择器中切换已连接的后端，切换时保留草稿（#58280、#58457）。
  - [发布说明](https://github.com/openai/codex/releases/tag/rust-v0.167.0)

- **`rust-v0.169.0-alpha.2`** — 预发布通道版本更新；未提供详细变更日志。[发布页](https://github.com/openai/codex/releases/tag/rust-v0.169.0-alpha.2)

> 🔗 发布说明中提到的 PR（如 #58238、#58457）不在今日的 PR 列表中，但对应 `0.167.0` 周期中已合并的工作。

---

## 3. 热门 Issue

按参与度和影响力排名的前 10 个 Issue：

1. **[#42739] Windows：桌面更新后本地项目从侧边栏消失** — 79 条评论，7 👍
   Tracker 中流量最高的 Issue。应用更新后，Projects 区域显示“没有项目”，而 Recents 和磁盘上的文件夹完好无损。今天提交的*第二个全新*报告（#59895）表明该问题在 `26.1103.3517.0` 上仍在活跃复现。
   🔗 https://github.com/openai/codex/issues/42739

2. **[#30408] MCP 服务器进程泄漏——每线程进程从不清理（9+ GB RSS）** — 69 条评论，23 👍
   今日点赞最高的 Issue。App-server 为每个线程生成一整套 MCP 进程，且在线程归档/关闭时从不回收。长时间运行的会话退化为无界内存增长。核心基础设施问题。
   🔗 https://github.com/openai/codex/issues/30408

3. **[#12491] Codex.app GUI：1300+ 僵尸 MCP 进程，37 GB 泄漏** — 39 条评论
   #30408 的极端案例姊妹 Issue，自 2026 年 2 月起开放。其持续约 9 个月未解决，是桌面封装层进程生命周期管理需要架构层面关注的强烈信号。
   🔗 https://github.com/openai/codex/issues/12491

4. **[#52503] Dots/Web：反复出现“始终允许”审批、非待处理卡片、上传结果丢失** — 23 条评论
   GitHub `create_blob` 审批在“始终允许”后仍重复提示，阻断已授权的合并。这是一个具有真实工作流成本的授权状态 Bug。
   🔗 https://github.com/openai/codex/issues/52503

5. **[#57190] macOS：重启后现有聊天中浏览器报告为已禁用** — 14 条评论
   能力状态不一致：浏览器在一个聊天中显示为禁用，而在其他聊天中已启用且功能正常。指向每线程能力注册漂移。
   🔗 https://github.com/openai/codex/issues/57190

6. **[#56682] Windows VS Code 扩展即使在隔离配置文件中也无法初始化 UI** — 13 条评论，5 👍
   在干净配置文件中复现，排除了用户配置污染——这是 Windows 上真正的扩展初始化缺陷。
   🔗 https://github.com/openai/codex/issues/56682

7. **[#42315] Windows 上的 iOS Remote：自定义分区同步了名称但遗漏所有项目** — 13 条评论，6 👍
   元数据同步了但负载没有——远程/app-server 路径中的部分同步失败，导致 iOS 客户端项目为空。
   🔗 https://github.com/openai/codex/issues/42315

8. **[#32880] Windows 沙箱回归：workspace-write DENY ACL 阻止链接工作树** — 12 条评论
   自 26.707.x 更新以来，Windows 上的自主 Git 操作因沙箱 ACL 拒绝链接工作树而失败。对多仓库/工作树用户影响较大。
   🔗 https://github.com/openai/codex/issues/32880

9. **[#55141] macOS：累积的每线程工具服务器和上升的内存压力** — 12 条评论
   每线程资源累积家族的又一案例——这次是在 M4/16 GB 硬件上，用户很快就能感知到。
   🔗 https://github.com/openai/codex/issues/55141

10. **[#56475] 疑似误报：网络安全检查阻止已授权的本地开发** — 8 条评论
    安全检查在合法的本地开发上触发，且未记录明确的覆盖路径。值得关注的信任/安全摩擦。
    🔗 https://github.com/openai/codex/issues/56475

**其他值得注意的：** #59386（全视图隐藏 Projects 侧边栏）、#59194（Pro-500 每周配额在 Astra High 上 2 小时消耗 8% — 4 👍，配额经济学）、以及 #45251 **[已关闭]**（关于 `~/.codex` 发布文件稳定性的文档问题）——其关闭可能意味着文档答复已落地。

---

## 4. 关键 PR 进展

列出的全部 20 个 PR 均来自 `copyberry[bot]` 且状态为 **CLOSED**。它们集中在四个主题：

**云端/本地统一与数据层**
1. **[#60007] 使用本地 GitHub 项目启动云端线程** — 将本地项目的 GitHub origin 传入云端预热，使新云端线程能够配置真实的仓库检出；在后端切换时保留所附的本地项目。今日最具战略意义的 PR。🔗 https://github.com/openai/codex/pull/60007
2. **[#60006] 降低线程活动和历史的 SQLite 写入开销** — Token 用量更新现在只触及时间戳/总量而非完整元数据写入；避免与 SQLite 单写入者的连接池争用。🔗 https://github.com/openai/codex/pull/60006
3. **[#60004] 在 `account/read` 中暴露 ChatGPT 用户 ID** — 为 ChatGPT 和 PAT 会话在 `GetAccountResponse` 中添加可空的 `userId`。🔗 https://github.com/openai/codex/pull/60004
4. **[#59987] 使用线程创建选项解析启动模型** — 用 `resolve_startup_model` 替换 `models_manager_for_startup`，在启动和冷恢复之间共享解析逻辑。🔗 https://github.com/openai/codex/pull/59987

**Guardian 审查系统（PR #59993–#60002，一个连贯的功能栈）**
5. **[#59999] 模型驱动的 Guardian 通道选择** — 审查者订阅从任务和捕获的目录中选择，在原子替换前进行验证。🔗 https://github.com/openai/codex/pull/59999
6. **[#60000 / #60001] 有界、按源排序的证据传递** — 按源顺序规划唯一原始项，在工具输出预算内分批，拒绝超过审查截止点的证据。🔗 https://github.com/openai/codex/pull/60001
7. **[#59993] 本地留言板的固定读取视图** — 快照目录/帖子边界，使并发追加不会偏移分页。🔗 https://github.com/openai/codex/pull/59993

**MCP / 认证 / 实时正确性**
8. **[#59991] 在 MCP 工具刷新之间保留应用调用上下文** — 当工具元数据在同一连接上刷新时，保持 `_codex_apps.invocation_context` 稳定。🔗 https://github.com/openai/codex/pull/59991
9. **[#60003] 对实时连接应用运行时认证** — 实时连接不再绕过运行时认证或在重试/重连时复用过期的捕获头。🔗 https://github.com/openai/codex/pull/60003

**TUI / Code Mode**
10. **[#59983] 在 TUI 中将类型化提示渲染为 Markdown**（保留装订线/背景/超链接）以及 **[#59973] 为 Code Mode 默认启用 gRPC + 主机输出截断** — 两者均为稳定标志的毕业。🔗 https://github.com/openai/codex/pull/59983 · https://github.com/openai/codex/pull/59973

---

## 5. 热门讨论

**问答**
- **[#57025] app-server 在启动线程时能否应用 `ToolPolicy`？** — 希望创建一个全新的延续线程来审查交接，*不使用任何工具*；要求硬性执行机制而非提示指令。🔗 https://github.com/openai/codex/discussions/57025
- **[#59594] ChatGPT Plus：Codex 限额似乎被削减约 50%，疑似与 GPT-6.1 Sol (low) 漂移有关** — 双语（EN/中文）配额调查。🔗 https://github.com/openai/codex/discussions/59594
- **[#57675] Pro 账户：连续 7 天所有模型均显示“所选模型已达容量上限”** — 跨所有界面的账户级容量锁定；从 #56324 交叉发布。🔗 https://github.com/openai/codex/discussions/57675

**展示与分享**
- **[#58849] code-review-skill：带 JSON 合并门禁的证据支撑审查** — 每个发现都携带位置、契约/代码路径和严重性；四个探针在全新上下文中运行。🔗 https://github.com/openai/codex/discussions/58849
- **[#59896] Tessera：跨 Codex、Claude Code、OpenClaw、Hermes 运行同一任务；保留通过测试的答案** — 幂等的 `tessera sync` 将同一源写入每个 agent 的原生格式。🔗 https://github.com/openai/codex/discussions/59896
- **[#59316] ML 研究技能：冻结实验、证据账本、可恢复审查** — 实际实验与 agent 驱动写作之间的交接。🔗 https://github.com/openai/codex/discussions/59316
- **[#59871] Account Access：用于范围化账户读取和可恢复登录的可移植技能 + CLI** — 经所有者批准的连接器，用于认证敏感的读取。🔗 https://github.com/openai/codex/discussions/59871
- **[#59844] 跨 Codex、Claude Code、OpenCode 的 macOS 技能目录** — 去重跨 agent 漂移的技能副本。🔗 https://github.com/openai/codex/discussions/59844
- **[#59765] Worktree 复现：共享 SQLite 可能隐藏缺失的测试夹具** — 仅用标准库的 Python 复现，展示一个工作树针对另一个的数据进行测试。🔗 https://github.com/openai/codex/discussions/59765

---

## 6. 功能请求趋势

1. **API 层面的策略/工具执行**（#57025、#45251）— 开发者希望*强制执行*约束（工具允许列表、稳定的会话文件契约），而非提示级指导，尤其是对 app-server/SDK 消费者。
2. **跨 agent 互操作性**（#59896、#59844）— 一组用于在 Codex、Claude Code 和 OpenCode 之间同步指令/技能/配置的工具，以及多 agent“最佳答案胜出”的运行器。
3. **可审计、证据支撑的 agent 输出**（#58849、#59316）— 将来源（文件/行、契约、严重性、冻结实验账本）附加到 agent 声明的技能。
4. **通过 OAuth 实现完整的图像生成功能对等**（#59890）— 第三方客户端希望通过 Codex OAuth 图像工具暴露模型选择、复杂度/SVG 控制、GIF/动画支持。
5. **桌面 UX 密度**（#59386）— 全视图模式应保留 Projects 侧边栏；每对话标签页而非始终存在的右侧面板。
6. **终端通知可扩展性**（#58758，[已关闭]）— OSC 777 作为 Windows Terminal 通知的方法；关闭表明可能已被接受。

---

## 7. 开发者痛点

1. **每线程资源泄漏是头号系统性问题。** 四个独立的高参与度 Issue（#30408、#12491、#55141，以及相关的僵尸进程报告）描述了 MCP 进程和工具服务器在线程归档/关闭时未被回收。症状从 9 GB 到 37 GB RSS 不等。这是 tracker 中最强烈的单一信号，横跨 macOS 和桌面封装层整体。
2. **Windows 桌面应用回归主导 Bug 数量。** 项目丢失（#42739、#59895）、工作树上的沙箱 ACL 破坏（#32880）、VS Code 扩展初始化失败（#56682）、MCP `-32000` 错误（#56421）、WSL 启动策略失败（#56197）、以及 RDP 语音输入损坏（#55162）形成了一个密集的平台特定故障集群。
3. **授权和能力状态不一致。**“始终允许”不生效（#52503）、浏览器在一个聊天中禁用而在其他聊天中启用（#57190）、以及 Site/MCP 工具尽管已连接却报告“模型不可用”（#59064）都指向每线程能力/审批状态漂移。
4. **配额经济学感觉不透明且具有惩罚性。** #59194（2 小时消耗 8% 周配额）、#45383（中断的会话浪费配额重建上下文）、#59594（约 50% 限额削减怀疑）、#57675（账户级容量锁定）——用户希望透明度和保留上下文的恢复。
5. **安全过滤器产生误报且无审查路径。** #56475（本地开发的网络安全检查）和 #59738（Browser Use 阻止 vk.com 且无确认的审查途径）显示信任/安全摩擦，用户缺乏申诉或文档机制。
6. **模型指令遵循和上下文压缩质量。** #47953（“无法遵循最小的指令……知道自己违反了规则”）和 #59971（压缩过度加权最后一条消息，丢失工作上下文）是较低量但反复出现的模型行为投诉，均为 0 👍——值得监测趋势。

---

*文摘基于 2026-11-07 的 `github.com/openai/codex` 快照生成。Issue/PR 编号、计数和状态与源数据一致（列出的所有 PR 均为 CLOSED；未提供 PR 的评论数）。*

</details>

<details>
<summary><strong>Gemini CLI</strong> — <a href="https://github.com/google-gemini/gemini-cli">google-gemini/gemini-cli</a></summary>

# Gemini CLI 社区摘要 — 2026-11-07

## 1. 今日亮点

过去 24 小时内没有新版本发布，但 issue 跟踪器被一个主题主导：**子代理可靠性**。多个 P1 级回归问题——`MAX_TURNS` 耗尽时错误报告 `GOAL` 成功（#22323）、通用代理挂起（#21409）、以及 Wayland 特有的浏览器子代理失败（#21983）——都处于积极复测中。与此同时，PR 队列显示核心稳定性修复的大量推进（PTY FD 泄漏、长会话 OOM 崩溃、超时处理），以及沙箱相关的 macOS 修复。

## 2. 版本发布

过去 24 小时内没有新版本发布。

## 3. 热门 Issue

1. **[#22323 — 子代理 MAX_TURNS 被报告为 GOAL 成功（P1，13 条评论）](https://github.com/google-gemini/gemini-cli/issues/22323)** — 今日讨论最多的 issue。`codebase_investigator` 即使在未进行任何分析就达到轮次上限时仍报告 `status: success`，掩盖了中断。今日参与度最高；直接损害了对代理输出的信任。

2. **[#19873 — 零依赖 OS 沙箱 + 执行后意图路由（P2，9 条评论）](https://github.com/google-gemini/gemini-cli/issues/19873)** — 提议利用 Gemini 3 的原生 bash 亲和性，同时通过沙箱保留安全性。标志着代理运行时的战略方向。

3. **[#21409 — 通用代理永久挂起（P1，8 条评论，8 👍）](https://github.com/google-gemini/gemini-cli/issues/21409)** — 今日点赞最高的 issue 之一。任何向通用代理的委派都会无限期挂起，包括创建文件夹等简单任务。社区反应强烈（8 个赞）。

4. **[#22745 — AST 感知的文件读取/搜索/映射 EPIC（P2，7 条评论）](https://github.com/google-gemini/gemini-cli/issues/22745)** — 跟踪中的 EPIC，研究 AST 感知工具以减少 token 噪声和错位读取。一个值得注意的平台级质量押注。

5. **[#21968 — Gemini 不够频繁地使用技能/子代理（P2，7 条评论）](https://github.com/google-gemini/gemini-cli/issues/21968)** — 虽是轶事但很重要：模型除非被明确告知，否则不会调用自定义技能或子代理，即使任务明显相关。指向引导/提示词方面的缺口。

6. **[#22267 — 浏览器代理忽略 `settings.json` 覆盖（P2，5 条评论）](https://github.com/google-gemini/gemini-cli/issues/22267)** — `AgentRegistry` 正确合并了设置，但浏览器代理忽略它们（例如 `maxTurns`）。配置信任问题。

7. **[#21983 — 浏览器子代理在 Wayland 上失败（P1，4 条评论）](https://github.com/google-gemini/gemini-cli/issues/21983)** — Linux/Wayland 用户遇到 `GOAL` 终止且无功能性结果；与更广泛的虚假成功模式相关。

8. **[#24246 — 超过 128 个工具时出现 400 错误（P2，3 条评论）](https://github.com/google-gemini/gemini-cli/issues/24246)** — 工具数量扩展限制以硬性 400 错误的形式出现。对拥有大型 MCP/工具配置的用户相关。

9. **[#22672 — 代理应劝阻破坏性行为（P2，3 条评论）](https://github.com/google-gemini/gemini-cli/issues/22672)** — 维护者提交的关注点，涉及 `git reset`/`--force` 和数据库修改，而存在更安全的替代方案。安全关键方向。

10. **[#22186 — get-shit-done 输出钩子导致 CLI 崩溃（P1，3 条评论）](https://github.com/google-gemini/gemini-cli/issues/22186)** — 在输出接近完成时可复现的崩溃；对钩子作者和扩展生态稳定性很重要。

*另需注意：* [#30190](https://github.com/google-gemini/gemini-cli/issues/30190) — 一个看似跑题/垃圾的 issue（波兰语文本，涉及第三方 PR），有 2 条评论；值得标记为分诊噪声。

## 4. 关键 PR 进展

1. **[#30185 — 防止 shell 模式下递归交互式 `gemini` 会话（P1）](https://github.com/google-gemini/gemini-cli/pull/30185)** — 阻止在 Shell 模式（`!`）和 `[Shell]` 工具内生成嵌套交互式 CLI 会话；拒绝不支持的配置子命令。

2. **[#30189 — 发布失败时立即拒绝 `MessageBus.request()`](https://github.com/google-gemini/gemini-cli/pull/30189)** — 此前需等待完整的 60 秒超时才拒绝；现在快速失败并返回原始错误，同时释放监听器。

3. **[#30183 — 跨认证模式的可配置 60 秒 API 头部超时](https://github.com/google-gemini/gemini-cli/pull/30183)** *(已关闭)* — 添加 `model.requestTimeoutMs` / `GEMINI_API_HEADERS_TIMEOUT_MS`，修复长时间思考请求中的 `UND_ERR_HEADERS_TIMEOUT`。与开放的 PR #30082 配套。

4. **[#30184 — 修复 macOS PTY 生成时的 `/dev/ptmx` 主 FD 泄漏](https://github.com/google-gemini/gemini-cli/pull/30184)** *(已关闭)* — 解决使用 `@lydell/node-pty` 的长运行会话中 `kern.tty.ptmx_max` 耗尽问题。

5. **[#30165 — 处理流中重试以防止文本拼接](https://github.com/google-gemini/gemini-cli/pull/30165)** *(已关闭)* — 修复 ACP 模式下被放弃尝试的部分文本/工具调用被拼接并执行的问题。

6. **[#30187 — 防止数小时的配额重试挂起；通知 ACP 客户端](https://github.com/google-gemini/gemini-cli/pull/30187)** *(已关闭)* — ACP 请求在 HTTP 429 且重置窗口较长时不再无限期挂起；重试对客户端可见。

7. **[#30186 — 修复恢复时终端模型轮次 400 错误 + JSONL 冻结](https://github.com/google-gemini/gemini-cli/pull/30186)** *(已关闭)* — 防止会话恢复时出现 `400 INVALID_ARGUMENT: Requests ending with a model turn`；XL 规模的稳定性修复。

8. **[#30180 — 限制多部分工具输出并驱逐较旧的多模态 inlineData](https://github.com/google-gemini/gemini-cli/pull/30180)** *(已关闭)* — 修复长时间代理会话中反复读取文件或截图时发生的 V8 堆 OOM 崩溃。

9. **[#30164 — 进入 macOS Seatbelt 沙箱前解析文件夹信任（P1）](https://github.com/google-gemini/gemini-cli/pull/30164)** — Seatbelt 拒绝访问 `~/.gemini/trustedFolders.json`，导致文件夹信任对话框失效；延续 #30143 中的启动崩溃修复。

10. **[#30181 — 消除 VS Code 扩展中的桶导入打包膨胀](https://github.com/google-gemini/gemini-cli/pull/30181)** *(已关闭)* — 精确的子路径导入将扩展包体积减少 >80%（约 13.8 MB → 约 2.7 MB），改善启动速度。

*另需关注：* [#30176](https://github.com/google-gemini/gemini-cli/pull/30176)（在认证重启间保留 `--worktree`）、[#30160](https://github.com/google-gemini/gemini-cli/pull/30160)（修复主题主目录检查的 `isSubpath`）、[#30119](https://github.com/google-gemini/gemini-cli/pull/30119)（独立 `theme.json` 支持）、以及 [#30010](https://github.com/google-gemini/gemini-cli/pull/30010)（在企业版遥测白名单中暴露 `skill_name`）。

## 5. 热门讨论

本数据集未提供讨论数据；此节省略。

## 6. 功能请求趋势

- **代理引导与自我意识：** 反复要求模型*主动*使用技能/子代理（[#21968](https://github.com/google-gemini/gemini-cli/issues/21968)）、理解自身的 CLI 标志/热键（[#21432](https://github.com/google-gemini/gemini-cli/issues/21432)）、并避免破坏性命令（[#22672](https://github.com/google-gemini/gemini-cli/issues/22672)）。
- **AST 感知的代码库工具：** 一个协调的集群（[#22745](https://github.com/google-gemini/gemini-cli/issues/22745)、[#22746](https://github.com/google-gemini/gemini-cli/issues/22746)、[#22747](https://github.com/google-gemini/gemini-cli/issues/22747)）推动基于 AST 的读取/搜索/映射，以减少 token 膨胀和错位读取。
- **节省 token 的上下文管理：** 精确读取层级（[#19561](https://github.com/google-gemini/gemini-cli/issues/19561)）和基于文件的持久任务跟踪取代上下文内 `WriteToDo`（[#18836](https://github.com/google-gemini/gemini-cli/issues/18836)）。
- **子代理可观测性与协作：** 通过 `/chat share` 共享子代理轨迹（[#22598](https://github.com/google-gemini/gemini-cli/issues/22598)）、bug 报告中的子代理上下文（[#21763](https://github.com/google-gemini/gemini-cli/issues/21763)）、共享内存 / 并行协作（[#18287](https://github.com/google-gemini/gemini-cli/issues/18287)）、以及基于 `settings.json` 的发现（[#18285](https://github.com/google-gemini/gemini-cli/issues/18285)）。
- **浏览器代理韧性：** 会话接管/锁恢复（[#22232](https://github.com/google-gemini/gemini-cli/issues/22232)）和遵守配置（[#22267](https://github.com/google-gemini/gemini-cli/issues/22267)）。

## 7. 开发者痛点

- **静默 / 误导性的代理失败：** 最主要的挫败感是代理在实际失败或挂起时报告成功——`MAX_TURNS` → `GOAL`（#22323）、通用代理挂起（#21409）、Wayland 浏览器失败（#21983）、以及交互式提示卡住（#22465）。这比崩溃更严重地侵蚀了对代理输出的信任。
- **配置未被遵守：** 浏览器代理忽略 `settings.json`（#22267）和 `~/.gemini/agents/*.md` 符号链接未被识别（#20079）表明发现/合并存在不一致。
- **长会话稳定性：** 围绕 FD 泄漏（#30184）、V8 堆 OOM（#30180）、配额重试挂起（#30187）、以及网络故障挂起（#30182）的一组清晰的 PR 和 issue——长运行会话是持续弱点。
- **工作区卫生：** 模型在各目录中散落临时脚本（#23571），在干净提交前造成清理开销。
- **平台特定故障：** macOS Seatbelt 沙箱信任文件访问（#30164）和 Linux/Wayland 浏览器失败（#21983）显示环境特定回归反复出现。
- **工具规模限制：** 超过约 128 个工具时出现硬性 400 错误（#24246）约束了拥有大型工具/MCP 清单的高级用户。

*分诊说明：* 跟踪器显示有未经请求/垃圾 issue 的迹象（例如 #30190），且多个 P1 项长期处于 `status/need-retesting` 状态——值得关注维护者带宽。

</details>

<details>
<summary><strong>GitHub Copilot CLI</strong> — <a href="https://github.com/github/copilot-cli">github/copilot-cli</a></summary>

# GitHub Copilot CLI 社区摘要 — 2026-11-07

## 1. 今日亮点
本周期的主要话题是 **MCP/Entra 认证的脆弱性**（Windows 令牌缓存、macOS 并发登录、过期令牌恢复）以及 **输入处理的摩擦**（Shift+Enter 触发提交而非换行；IME 误提交获得 48 个 👍）。v1.0.107 发布了两项新的终端/UX 能力——Kitty 内联图片和将会话移交给 VS Code 的 `/code` 命令——同时新增了一项 Fusion 层级配置设置。

## 2. 版本发布
**v1.0.107**（2026-11-06）——以及携带相同更新日志的 v1.0.106（2026-11-05）：
- **通过 Kitty 直接放置实现内联图片**，适用于 Rex 终端——在支持的终端中提供更丰富的多模态输出。
- **`/code` 命令**——直接在 VS Code 中打开当前会话，强化 CLI↔IDE 的闭环。
- **Fusion 层级设置** 添加至 `copilot config`——可配置的模型路由层级。
- **统一 `/settings` 面板** 向所有用户推出，支持跨设置和本地资源的 **全局搜索**。

## 3. 热门 Issue

1. **[#1972](https://github.com/github/copilot-cli/issues/1972)** — 可配置的提交键（例如 Ctrl+Enter）以防止 IME 误提交。*48 👍——跟踪器中呼声最高的信号；CJK 用户每天都在遭遇回车即转换的冲突。*
2. **[#2776](https://github.com/github/copilot-cli/issues/2776)** — Shift+Enter 触发提交而非插入换行（9 条评论，5 👍）。与 [#3768](https://github.com/github/copilot-cli/issues/3768) 重复；长期存在、反复被提及的抱怨。
3. **[#5236](https://github.com/github/copilot-cli/issues/5236)** — Windows：MCP 登录后 Entra broker 令牌缓存保持 0 字节 → 每个进程都需重新认证。*与 #5250/#5366 构成跨平台认证集群；对企业 MCP 用户造成高昂的运维成本。*
4. **[#5250](https://github.com/github/copilot-cli/issues/5250)** — macOS：启动时并发 Entra 登录被拒绝；被拒绝的缓存令牌无法恢复。第一天就破坏多 MCP 服务器会话。
5. **[#5366](https://github.com/github/copilot-cli/issues/5366)** — 远程 MCP OAuth：令牌过期后调用失败，而 `mcp.list` 仍报告“已连接”；只有 `restartServer` 能恢复。*误导性的健康状态才是最危险的部分。*
6. **[#3679](https://github.com/github/copilot-cli/issues/3679)** — BYOK Azure OpenAI：429 在约 0.15 秒内耗尽全部 5 次重试且无退避；重试次数/等待时间不可配置。*阻碍了气隙/自托管部署。*
7. **[#5367](https://github.com/github/copilot-cli/issues/5367)** — Agent 经常在会话中途丢失 MCP 工具 schema；`/compact` 可恢复。日常工作中断问题，可能与上下文修剪交互有关。
8. **[#5220](https://github.com/github/copilot-cli/issues/5220)** — 为 Claude 模型提供 1 小时提示缓存 TTL 选项（9 👍）。默认 5 分钟 TTL 在正常的“先思考再输入”节奏中就会过期 → 反复产生全上下文重新计费。
9. **[#5370](https://github.com/github/copilot-cli/issues/5370)** — Fusion/Auto 模式：当长时间 shell 命令转入后台时，agent 输出被扣留至下一用户轮次——看起来像是卡死。新近分类，高严重度。
10. **[#5361](https://github.com/github/copilot-cli/issues/5361)** — 5 小时会话限制重置后：`400 The requested model is not available for integrator "copilot-developer-cli"`。配额重置后会话连续性被破坏。

*本周期还关闭了以下 Issue（良好维护）：* [#5368](https://github.com/github/copilot-cli/issues/5368)（Windows 沙箱 `CreateProcessW` 探测）、[#4845](https://github.com/github/copilot-cli/issues/4845)（会话卡在“使用中”）、[#4943](https://github.com/github/copilot-cli/issues/4943)（Esc 丢弃 `ask_user` 表单）、[#2731](https://github.com/github/copilot-cli/issues/2731)（带空格的 @ 自动补全）、[#5265](https://github.com/github/copilot-cli/issues/5265)（历史记录最后写入者胜出）。

## 4. 关键 PR 进展

本时间窗口内仅 **一个 PR** 有更新，因此以下是完整列表及背景：

1. **[#5373](https://github.com/github/copilot-cli/pull/5373)** — *docs: add Simplified Chinese Copilot CLI README*（开放中）。添加简体中文翻译，包含 EN/zh 导航链接；保留命令和配置键。社区本地化努力——鉴于上述 IME/输入问题，这一点尤为重要。

*注：根据所提供数据，过去 24 小时内没有代码 PR 合入；发布说明表明功能工作正通过发布流水线合入，而非可见的 PR。*

## 5. 热门讨论
本窗口未提供讨论数据——本节省略。

## 6. 功能请求趋势
- **输入模型可配置性**：用户自定义提交键、正确的 Shift+Enter 换行语义、IME 安全行为，以及禁用内置斜杠命令（[#5375](https://github.com/github/copilot-cli/issues/5375)），以免自定义技能被 `/review` 遮蔽。
- **模型路由控制**：Fusion/Auto 的模型黑名单（[#5369](https://github.com/github/copilot-cli/issues/5369)）、1 小时 Claude 缓存 TTL（[#5220](https://github.com/github/copilot-cli/issues/5220)）。
- **上下文/令牌经济性**：由模型判断修剪无用的工具结果（[#5362](https://github.com/github/copilot-cli/issues/5362)）、折叠回滚缓冲区以突出问答轮次（[#4995](https://github.com/github/copilot-cli/issues/4995)）。
- **可扩展性钩子**：权威前台会话变更钩子（[#5279](https://github.com/github/copilot-cli/issues/5279)）、仅显示用的脱敏钩子（[#5099](https://github.com/github/copilot-cli/issues/5099)）、`postToolUse` 输出的桌面渲染（[#5374](https://github.com/github/copilot-cli/issues/5374)）。
- **企业/BYOK 韧性**：可配置的重试/退避（[#3679](https://github.com/github/copilot-cli/issues/3679)）、在 `--server`/`--host-environment` 模式下遵循 BYOK 提供商配置（[#5371](https://github.com/github/copilot-cli/issues/5371)）、ACP 模式下的 OTel 导出（[#5376](https://github.com/github/copilot-cli/issues/5376)）。

## 7. 开发者痛点
- **Entra/MCP OAuth 是头号可靠性缺口**：三个相关的开放 Issue（#5236、#5250、#5366）描述了空令牌缓存、并发登录被拒绝以及过期后误导性的“已连接”状态。企业用户不断重新认证或悄然丢失工具。
- **输入处理忽视平台现实**：Shift+Enter 语义和 IME 回车冲突尽管有 48 👍 和九条评论的讨论帖仍未修复——这是一项低投入、高可见度的修复，却一再被拖延。
- **静默失败多于显式失败**：延迟的 Fusion 输出（#5370）、丢失的 MCP schema（#5367）、过期的 OAuth 状态（#5366），以及 ACP 报告一个它并未运行的模型（#5363）——所有这些都让 agent *看起来*正常却在出错，比崩溃更侵蚀信任。
- **非交互/无头模式与 TUI 存在差距**：BYOK 配置在服务器模式下被忽略（#5371）、ACP 中无 OTel（#5376）、`session/new` 模型不匹配（#5363）——自动化用户获得的是二等体验。
- **默认设置下的成本焦虑**：5 分钟 Claude 缓存过期和 429 重试瞬间耗尽都直接转化为重度用户的浪费性支出和失败运行。

---
*基于 github.com/github/copilot-cli 2026-11-07 数据生成。*

</details>

<details>
<summary><strong>OpenCode</strong> — <a href="https://github.com/anomalyco/opencode">anomalyco/opencode</a></summary>

# OpenCode 社区摘要 — 2026-11-06/07

## 1. 今日亮点
过去 24 小时内没有新版本发布，但项目经历了繁忙的分诊日：一大批此前已复现的 bug 被关闭（TUI 主题、MCP OAuth、composer `@` 处理、agent frontmatter 解析），表明维护者正在为下一个版本清理积压。最活跃的讨论仍是 Agent Plugins 规范支持（#40993，21 👍），而 jlongster 的 Code Mode VM 工作已进入“开发环境默认启用”的 PR。社区情绪喜忧参半：对可扩展性（插件、子代理限制）热情高涨，但对模型质量、桌面端/Windows 稳定性以及免费模型体验的沮丧反复出现。

## 2. 版本发布
过去 24 小时内没有新版本发布。

## 3. 热门 Issue

1. **[#40993](https://github.com/anomalyco/opencode/issues/40993) — 支持 Agent Plugins 标准**（OPEN，17 条评论，21 👍）
   请求支持 [agent-plugins.org](https://agent-plugins.org/specification)，这是将 Agent Skills + MCP 服务器打包的供应商中立规范。互动量最高的开放 issue；表明用户想要可移植的插件生态，而非 OpenCode 专属配置。

2. **[#27110](https://github.com/anomalyco/opencode/issues/27110) — 限制最大并行子代理数的设置**（OPEN，14 条评论，42 👍）
   本时间窗口内获赞最多的 issue。本地模型用户在子代理不受控地扇出时会触及内存/上下文上限。一个简单的配置上限就能解锁本地模型工作流——明确的高价值/低投入请求。

3. **[#48826](https://github.com/anomalyco/opencode/issues/48826) — V2 子代理在仍有后台工作待处理时提前标记完成**（OPEN，已复现，13 条评论）
   带有 `background: true` 任务/shell 工作的子代理会过早报告 `completed`；结果永远不会被父代理收集。这是 V2 编排核心中的正确性 bug——可能是列表中影响最大的开放 bug。

4. **[#28030](https://github.com/anomalyco/opencode/issues/28030) — 在 OpenCode Desktop 中删除项目**（CLOSED，11 条评论，15 👍）
   桌面端终于处理了基本的项目生命周期管理。长期缺失的 UX 缺口；本窗口内关闭但未关联 PR，值得关注后续。

5. **[#7487](https://github.com/anomalyco/opencode/issues/7487) — `exec` 变量替换**（OPEN，9 条评论）
   用户希望在 `exec` 命令中支持配置风格的变量替换（例如用于 `ripgrep` 参数）。小功能，长尾——自一月起开放，表明配置摩擦随时间累积。

6. **[#61511](https://github.com/anomalyco/opencode/issues/61511) — Crouton Alpha 在回合中途停滞**（CLOSED，7 条评论）
   代理在几次工具调用后停止，需要推动才能继续。最近关闭；可能反映热修复或模型侧变更。留意回归报告。

7. **[#61481](https://github.com/anomalyco/opencode/issues/61481) — Windows Desktop 上模型列表为空（2.2.4/2.2.5）**（CLOSED，6 条评论）
   严重的 Windows 可用性 bug：模型已开启但聊天中显示“未找到模型”。关闭表明修复已随下一个桌面端构建发布；Windows 可靠性是反复出现的主题。

8. **[#40096](https://github.com/anomalyco/opencode/issues/40096) — TUI 中可折叠的工具输出**（OPEN，5 条评论）
   工具输出完全展开渲染，让长会话淹没在噪音中。`/collapse` 开关正是反复出现的 TUI 人体工学请求（见下方 PR #62437）。

9. **[#57844](https://github.com/anomalyco/opencode/issues/57844) — 使用 GLM 时俄语回答中出现多余 CJK 字形**（OPEN，4 条评论）
   `glm-5.3-flash-free` 导致的多语言损坏；姊妹 issue [#62411](https://github.com/anomalyco/opencode/issues/62411)（波兰语）同日关闭。表明是内置 `opencode-go` 提供商上的模型服务/配置问题，而非用户错误。

10. **[#62432](https://github.com/anomalyco/opencode/issues/62432) — 禁用持久化“始终允许”批准**（CLOSED，3 条评论）
    “始终允许”会持久覆盖 `opencode.json` 中的 `ask` 规则且无法退出。一个值得注意的安全/信任缺口：一次点击就静默削弱了配置的权限。很快关闭——需核实是修复了还是被分诊掉了。

## 4. 关键 PR 进展

1. **[#62446](https://github.com/anomalyco/opencode/pull/62446) — docs(console)：弃用 Pulse Alpha Free**
   免费期结束；移至 Deprecated 模型。常规但对免费层用户可见。

2. **[#62444](https://github.com/anomalyco/opencode/pull/62444) — feat(core)：通过 flag registry 在开发环境默认启用 Code Mode VM**
   将 `codemode_vm` 集成到标准 `ConfigFlag.registry`；移除手写的 `vmEnabled`。表明 Code Mode 正朝着默认启用推进。

3. **[#62413](https://github.com/anomalyco/opencode/pull/62413) — feat(codemode)：暴露可挂起的程序 API**
   用于 VM 挂起/恢复、快照和结果的公共 API。对构建长时间运行工具的插件作者很重要；与 #62444 互补。

4. **[#58846](https://github.com/anomalyco/opencode/pull/58846) — fix(core)：修复已完成导入遗留的 V1 全局会话**
   #56322 的后续，针对已升级过迁移窗口的用户。面向长期用户的数据完整性修复。

5. **[#62249](https://github.com/anomalyco/opencode/pull/62249) — fix(core)：匹配非 UTF-8 时保留 grep 结果**
   Grep 目前在 Latin-1/GBK 文件上会直接失败。小鲁棒性修复，对非 ASCII 代码库有实际影响。

6. **[#62437](https://github.com/anomalyco/opencode/pull/62437) — feat(tui)：将工具详情折叠到可点击标题后**
   响应 #40096，复活了被 stale-bot 关闭的 #42552。新增 `session.tool_details`（`show`/`collapse`）。强烈信号表明维护者在倾听长期 TUI 人体工学反馈。

7. **[#62250](https://github.com/anomalyco/opencode/pull/62250) — fix(core)：保留描述以方括号标签开头的 agent**
   修复因 YAML 将 `[Tag] ...` 解析为流序列而导致的静默 `AgentNotFoundError`。“静默丢弃且无警告”才是真正的抱怨点。

8. **[#62245](https://github.com/anomalyco/opencode/pull/62245) — fix(tui)：OAuth 登录后返回 MCP 列表**
   多个 MCP OAuth 服务器不再需要每次重新打开对话框。对 MCP 重度用户的生活质量改进。

9. **[#62248](https://github.com/anomalyco/opencode/pull/62248) — fix(tui)：优先使用终端明暗模式而非缓存调色板**
   修复缓存了过期调色板时浅色终端上出现深色 TUI 的问题。属于 TUI 可靠性修复批次的一部分。

10. **[#62201](https://github.com/anomalyco/opencode/pull/62201) — fix(app)：删除后显示重新导入的会话**
    删除后重新导入的会话在重启前一直隐藏。Bug 修复，同时表明会话生命周期一致性对工作区 UX 很重要。

## 5. 热门讨论
未提供独立的 Discussion 数据；issue 级讨论已在热门 Issue 中覆盖。

## 6. 功能请求趋势
- **Agent/平台可扩展性：** Agent Plugins 标准（#40993）、`session.synthetic` 空闲唤醒插件 API（#62324）、ACP 会话引导（#62208）、`exec` 变量替换（#7487）。插件/API 表面是最清晰的增长方向。
- **子代理编排控制：** 并行子代理上限（#27110），以及后台子代理完成（#48826）和引导（#53615）的正确性 bug。
- **TUI/桌面端人体工学：** 可折叠工具输出（#40096）、项目删除（#28030）、模型收藏切换（#50969）、主题模式处理（#52457）。
- **权限/信任控制：** 退出持久化“始终允许”（#62432）、显式 `ask` 强制执行。

## 7. 开发者痛点
- **本地模型限制：** 并行上下文上限使扇出子代理模式在没有上限时不可用（#27110）——数据集中 👍 数最高。
- **Windows/桌面端不稳定：** 模型列表为空（#61481）、ResizeObserver 错误风暴伴随 33–37 MB 转储（#62206）、双引号提示参数（#62202）。Windows 仍是最弱平台。
- **免费/模型质量问题：** GLM 多语言损坏（#57844、#62411）、托管 `web_search` 对 `gpt-6-luna` 被静默丢弃（#61614）、免费模型上的 ECONNRESET（#62179）——免费层可靠性正在侵蚀信任。
- **静默失败：** 方括号 YAML 描述导致 agent 被丢弃（#54200）、存在 `name` 时忽略 V2 `permissions`（#62415）、企业镜像后 npm registry 忽略（#62403）。用户反复要求警告/错误而非静默降级。
- **长期存在的小缺口：** `exec` 变量替换自一月起开放（#7487）；工具输出噪音请求已存在多年——小痛点随时间累积。

---

规则：
- 仅输出翻译。无前言、无解释、无整体输出的 markdown 围栏。
- 完全保留 Markdown 结构：标题、表格（包括列对齐行）、列表、块引用、粗体/斜体、水平线、emoji。
- 保持 URL、链接目标、代码跨度、代码块、数字和日期原样。
- 项目名称、仓库 slug、用户名、版本标签、文件路径和 API/配置标识符保持原样——不要翻译它们。
- 像 #12345 这样的 Issue/PR 引用及其链接文本保持原样。
- 使用自然的技术中文，中文开发者通讯的语域——而非逐字直译。

</details>

<details>
<summary><strong>Pi</strong> — <a href="https://github.com/earendil-works/pi">earendil-works/pi</a></summary>

# Pi 社区文摘 — 2026-11-07

## 1. 今日亮点

一个高速运转的维护日：没有发布版本，但 24 小时内有 **50 个 issue** 和 **21 个 PR** 更新，大多数修复通过 `robopipi[bot]`/`petrroll` 组合落地。主导主题是 **`edit` 工具中的文件编码与行尾完整性**（混合 CRLF/LF 和非 UTF-8 文件被静默损坏）、**Orca/WezTerm 风格终端上的全屏 TUI 渲染**，以及一批 **provider 适配器健壮性修复**（openai-completions 的空 `tools: []`、重复的工具调用流索引、截断流的重试模式）。一个 **prio:p0** 回归——pi-ai 1.5.0 中新增的 `StringEnum` 破坏了编译期工具参数类型——已被提交，这是对扩展作者影响最大的未决事项。

## 2. 发布

过去 24 小时内无发布。

## 3. 热门 Issue

1. **#7730 — [OPEN] macOS 上长会话高 CPU 占用**（30 条评论，👍12）——仓库中讨论最多的 issue。CPU 在 50–110% 之间波动，内存 600–800MB，据反映与上下文/会话长度相关。自 8 月起长期存在且仍未解决：对运行持续 agent 会话的用户而言是真实的可靠性隐患。
   https://github.com/earendil-works/pi/issues/7730

2. **#1188 — [CLOSED] 更好的屏幕阅读器兼容性**（14 条评论）——“working” 旋转指示器每 80ms 重绘一次，向屏幕阅读器灌入大量更新。经过长时间讨论后关闭；是辅助功能反馈被推动至解决的典型案例。
   https://github.com/earendil-works/pi/issues/1188

3. **#10605 — [OPEN] ChatGPT/OpenAI OAuth 403 “not eligible for subscription sharing”**（13 条评论，👍1）——阻止 OpenAI Plus 订阅者进行认证。此类认证路径故障往往会迅速升级，因为它们会完全阻断某个 provider。
   https://github.com/earendil-works/pi/issues/10605

4. **#8810 — [OPEN] 扩展注册的 provider 在 defaultProvider/defaultModel 上被间歇性忽略**（10 条评论，👍5）——当配置的 provider 通过 `pi.registerProvider` 注册时，新会话有时会回退到另一个 provider 的默认值。看起来像确定性的配置错误，实则不然；对多 provider 配置影响很大。
   https://github.com/earendil-works/pi/issues/8810

5. **#11869 — [OPEN] prio:p0：1.5.0 中新的 `StringEnum` 破坏编译期类型**（2 条评论，👍2）——`StringEnum` 不再继承自 typebox，导致 `registerTool` 中的 `TParam` 推断坍缩为 `unknown`。一个自伤的 1.5.0 回归，会影响每一位使用枚举工具参数的扩展作者。
   https://github.com/earendil-works/pi/issues/11869

6. **#11876 — [CLOSED] `edit`/`read` 工具被 shell 中的 `sed` 绕过**（3 条评论）——在 Windows 上使用 claude-opus-5.5/sonnet-5.5 时观察到：模型不走结构化工具而是调用 `sed`，静默改写行尾。与行尾 bug 集群直接相关。
   https://github.com/earendil-works/pi/issues/11876

7. **#11858 — [CLOSED] codemode 脚本重复了同一工具调用 24,018 次**（3 条评论）——约 680 次调用/秒，每次读取一个 28KB 文件，仅靠 wall-clock 中止才停下。不存在按脚本的重复防护；尽管以 no-action 关闭，这是一个值得跟踪的沙箱安全缺口。
   https://github.com/earendil-works/pi/issues/11858

8. **#10585 — [OPEN] bash 临时文件流错误导致整个进程崩溃（ENOSPC/EACCES）**（4 条评论）——在 1.0.4 上，`/tmp` 写满会产生未捕获异常，杀死运行并丢失进行中的会话日志。bash 工具的资源耗尽韧性。
   https://github.com/earendil-works/pi/issues/10585

9. **#11317 — [OPEN] SDK：自定义身份前言而不替换默认提示词章节**（4 条评论）——请求允许覆盖 `systemPromptOptions.sections.preamble`，同时保留 Pi 的工具/规则/文档。一个反复出现的 SDK 自定义请求（另见 #11863）。
   https://github.com/earendil-works/pi/issues/11317

10. **#11880 — [CLOSED] 会话文件包含内容为空的 `role: "system"`，破坏转录读取器**（2 条评论）——每个会话的第四行都是一条系统消息，第三方查看器会卡住。以 no-action 关闭，但对任何解析会话文件的人来说是真实的互操作陷阱。
    https://github.com/earendil-works/pi/issues/11880

## 4. 关键 PR 进展

1. **#11751 — [CLOSED] `edit` 保留未触碰行的行尾**——修复 #8544：该工具会把每一行重写为第一个换行的风格，静默变更混合 CRLF/LF 文件。同时避免大文件写入后 diff 的 OOM。非 UTF-8/UTF-16 处理拆分到后续跟进。
   https://github.com/earendil-works/pi/pull/11751

2. **#11894 — [OPEN] `edit` 拒绝非合法 UTF-8 文件**——#11751 的后续：GBK/Windows-1252/UTF-16 文件目前会将其无法解码的字节替换为 U+FFFD *同时报告成功*。提议的行为是明确失败而非损坏文件。
   https://github.com/earendil-works/pi/pull/11894

3. **#11895 — [CLOSED] grep glob 相对于 `path` 而非 cwd 匹配**——修复 #11807：`{ path: "repo", glob: "migrations/main/*.js" }` 返回“No matches found”，因为 ripgrep 将 `--glob` 锚定在进程 cwd。
   https://github.com/earendil-works/pi/pull/11895

4. **#11896 — [CLOSED] 等待图像缩放 worker 终止**——修复 #10695：`finally` 中的 `void worker.terminate()` 允许重叠的拆卸/启动，在 macOS arm64 / Node 24 上中止 Pi。串行图像读取是复现路径。
   https://github.com/earendil-works/pi/pull/11896

5. **#11891 — [CLOSED] 为 openai-completions 省略空的 `tools: []`**——修复 #11843：请求没有工具但转录中有工具历史时会发送 `"tools": []`，vLLM 0.20+（以及其前的 LiteLLM）会以 400 拒绝。
   https://github.com/earendil-works/pi/pull/11891

6. **#11882 — [CLOSED] 当流索引与工具调用 id 不一致时拆分工具调用块**——修复 #11881：provider 对不同的 id 复用相同的 `tool_calls[].index`，导致两个参数流被拼接成一个畸形的工具调用。
   https://github.com/earendil-works/pi/pull/11882

7. **#11890 — [CLOSED] 在其覆盖的行之后绘制全屏 Kitty 图像**——修复 #11835：Orca 和 WezTerm 将 Kitty 图形实现为单元格瓦片，因此 `TuiAltScreen` 的绘制顺序只让第一行可见。终端特定的 TUI 正确性。
   https://github.com/earendil-works/pi/pull/11890

8. **#11885 — [CLOSED] 重试回复已开始后被截断的流**——为通过 Magpie 网关中途断开的 Anthropic 流新增了 `isRetryableAssistantError()` 模式；此前不会重试。
   https://github.com/earendil-works/pi/pull/11885

9. **#11873 — [CLOSED] 在提示词模板参数中保留撇号**——修复 #11861：`parseCommandArgs` 将每个 `'` 都视为开启引号，把 `why doesn't the user's cache work` 变成 `why doesnt the users cache work`。
   https://github.com/earendil-works/pi/pull/11873

10. **#11888 — [CLOSED] 在每次提示前将实时思考级别记录到分支上**——修复 #11451：`/tree` 回退可能将最新的 `thinking_level_change` 留在被放弃的分支上，导致恢复时还原过时的级别。
    https://github.com/earendil-works/pi/pull/11888

## 5. 热门讨论

**展示与分享**

- **#11860 — CaseClip：将 Pi 的错误保存为可审查的案例卡** 👍1——`/case save <what went wrong>` 将最近的请求、工具结果和用户纠正一起捕获。灵感来自一次演示：Pi 调用了一个已下线的 API，收到 404，却仍声称成功。与上述崩溃/中止可观测性 issue 互补。
  https://github.com/earendil-works/pi/discussions/11860

**问答 / 想法**

- **#11870 — 在 Pi、Claude Code 和 OpenCode 之间保持技能组织有序** 👍1——技能目录在各 agent 之间重复并逐渐漂移；作者构建了 AgentKeeper（macOS 应用 + CLI）来编目它们。指向一个跨 agent 的技能/打包生态缺口。
  https://github.com/earendil-works/pi/discussions/11870

## 6. 功能请求趋势

- **SDK 提示词自定义**：可组合的 `systemPromptOptions`（前言/身份而不替换默认值，#11317），以及关于长时运行多模态上下文管理的指导（#11863）。
- **Provider/目录覆盖**：反复请求向内置目录添加模型（`kimi-coding` k3.1-pro、Z.AI `glm-5.3-flashx`、QwenCloud `deepseek-v4.1-flash-1017` — #11883、#11884、#11877）。
- **会话/转录互操作性**：稳定、利于读取的会话文件（#11880），以及通过 OSC 7501 报告终端标题（#11670）供 agent 仪表盘使用。
- **资源治理**：`pi-durable` 中的按会话压缩策略（#11771），以及针对失控工具调用循环的沙箱防护（#11858）。
- **跨 agent 技能管理**：Pi/Claude Code/OpenCode 之间技能去重（#11870）。

## 7. 开发者痛点

- **`edit` 中的静默文件损坏**：混合行尾（#8544、#11876）和非 UTF-8 文件（#11894）在工具报告成功时被变更。这显然是当日最高优先级的正确性修复。
- **Provider 适配器边缘情况**：空 `tools: []` 与 vLLM/LiteLLM（#11843）、不合规 OpenAI 兼容 provider 的重复流索引（#11881）、未分类的中途流中断（#11885），以及 OpenAI OAuth 403（#10605）。非第一方网关仍是摩擦来源。
- **认证/token 生命周期**：Copilot 过期前 401/403 刷新（#10238）和 OAuth 订阅共享错误指向需要更防御性的 token 刷新。
- **资源耗尽杀死进程**：bash 临时文件的 ENOSPC/EACCES（#10585）、图像 worker 拆卸竞态（#10695），以及 `Harness.close()` 泄漏 provider 会话（#11676），这些都会终止或挂起工作而非优雅降级。
- **扩展 API 中的类型回归**：1.5.0 中 `StringEnum`/typebox 的解耦（#11869）对任何编写类型化工具的人都是 p0 级 DX 问题。
- **平台特定缺口**：Windows `SIGHUP`/进程树清理（#11889、#11854）、Windows 路径/提示词引号（#11873、#11861、#11874），以及 macOS 会话 CPU（#7730），表明平台测试覆盖比核心路径更薄弱。

---

</details>

<details>
<summary><strong>Qwen Code</strong> — <a href="https://github.com/QwenLM/qwen-code">QwenLM/qwen-code</a></summary>

# Qwen Code 社区简报 — 2026-11-07

## 1. 今日亮点

**v0.26.9** 正式发布，带来技能安装/激活方面的改进（ZIP 上传、守护进程 API、TypeScript SDK 支持、批量激活、恢复保护）。CI 稳定性仍是压倒性的主题：由机器人提交的 **#14724**“Managed Agent gate”问题已膨胀至 **217 条评论**，且仍未关闭。与此同时，两个 P1 级 bug 引发关注——交互式 TUI 中工作流代理审批死锁（**#16425**，修复 PR **#16432** 已提交），以及围绕自动压缩的会话损坏报告（**#16418**）。

## 2. 版本发布

**v0.26.9** —— 除亮点部分外，未提供完整变更日志。
- **技能安装与激活**：技能 ZIP 上传、守护进程 API、TypeScript SDK 支持、批量激活及恢复保护，使技能的安装与管理更加便捷。
- **破坏性变更**：暂无已知项。

→ https://github.com/QwenLM/qwen-code/releases

## 3. 热门议题

1. **#14724 — 主 CI 失败：Managed Agent gate（217 条评论，OPEN）** —— 持续时间最长的 CI 失败线程；表明 managed-agent gate 长期不稳定，持续消耗维护者精力。
   → https://github.com/QwenLM/qwen-code/issues/14724

2. **#13113 — P1：“Transcript snapshot is too large to index”（硬编码 256 MiB 上限）** —— `file_history_snapshot` 呈二次方增长，导致长会话永久无法打开。对重度用户而言是严重的数据持久性隐患。
   → https://github.com/QwenLM/qwen-code/issues/13113

3. **#15597 — P1：无头模式 `-p` 自动记忆永远无法保存** —— 隐式编辑/写入拒绝覆盖了记忆代理的作用域允许，导致自动记忆静默失效却仍消耗 token。对自动化用户影响重大。
   → https://github.com/QwenLM/qwen-code/issues/15597

4. **#16425 — P1：工作流代理审批从不渲染，运行无限挂起** —— 审批已触发但无 UI 展示，导致后台工作流代理死锁（在 0.26.8 上复现）。修复 PR #16432 已提交。
   → https://github.com/QwenLM/qwen-code/issues/16425

5. **#16418 — P1：自动压缩将仅审查技能变为未授权实现** —— 反复压缩似乎侵蚀了技能作用域，导致代理实现了仅被授权审查的代码。与安全性相关。
   → https://github.com/QwenLM/qwen-code/issues/16418

6. **#15412 — P2：长项目路径破坏对话记录和会话列表** —— 完整绝对路径被展平为单个文件系统组件，超出 macOS 限制，导致深层嵌套项目的会话损坏。
   → https://github.com/QwenLM/qwen-code/issues/15412

7. **#15310 — P3：告知模型其所用的 shell（Windows）** —— 在 Windows 上模型假设使用 bash，通过 `cmd.exe` 发出 POSIX 命令，每次失败浪费一整轮。简单且高杠杆的修复。
   → https://github.com/QwenLM/qwen-code/issues/15310

8. **#15495 — P2：图像载荷驱逐没有关闭开关** —— 内联图像在未文档化的阈值 20 处被静默重写出历史记录；用户希望可配置。（已由 PR #16438 解决。）
   → https://github.com/QwenLM/qwen-code/issues/15495

9. **#16457 — P2：MCP OAuth 流程共享固定回调端口 7777** —— 重叠的 OAuth 流程会互相使对方状态失效，阻塞多 MCP 服务器认证配置。
   → https://github.com/QwenLM/qwen-code/issues/16457

10. **#15800 — P2：会话突然结束后后台 shell 状态卡在“running”** —— 状态文件从不与已完成的进程对账，误导用户对活跃后台任务的判断。
    → https://github.com/QwenLM/qwen-code/issues/15800

## 4. 关键 PR 进展

1. **#16432 — fix(cli)：在交互式 TUI 中展示工作流代理审批** —— 直接修复 P1 #16425，要求在挂起审批前必须有真正的响应者。当前最紧急的修复。
   → https://github.com/QwenLM/qwen-code/pull/16432

2. **#16438 — fix(config)：声明 `model.chatCompression` 配置项；允许禁用图像驱逐** —— 新增 `0` = 永不驱逐图像载荷，关闭 #15495 并暴露隐藏设置。
   → https://github.com/QwenLM/qwen-code/pull/16438

3. **#16075 — fix(permissions)：匹配 amend 调用而非原始文本** —— 重构 AUTO 模式 amend 防护，解析 shell 调用（复合拆分、heredoc 剥离、包装器解包）而非正则匹配原始文本。安全加固。
   → https://github.com/QwenLM/qwen-code/pull/16075

4. **#16454 — fix(telemetry)：将 OpenAI `cache_write_tokens` 报告为缓存创建** —— 恢复 GenAI 遥测中流式和非流式路径的缓存写入统计（修复 #15414）。
   → https://github.com/QwenLM/qwen-code/pull/16454

5. **#15936 — fix(core)：减少或拒绝无法适应窗口的压缩请求** —— 保证摘要至少 4,096 输出 token（或 25%），并在上下文长度被拒绝时重试一次。针对长会话可靠性。
   → https://github.com/QwenLM/qwen-code/pull/15936

6. **#16491 — feat(core)：在启动上下文中命名活跃 shell** —— 告知模型其实际 shell（例如 Windows 上的 `cmd.exe`），修复 #15310 中的一类失败。
   → https://github.com/QwenLM/qwen-code/pull/16491

7. **#16475 — feat(managed-agent)：持久化会话诊断** —— 新增 Public/WebShell API 对，以及包含 Context、Model statistics、Resources 和 Child agents 标签页的对话框。
   → https://github.com/QwenLM/qwen-code/pull/16475

8. **#16185 — fix(core)：在媒体拒绝中指明模型和设置** —— 改进错误文本，使模型（和用户）知道图像/音频/视频被拒绝的*原因*以及是哪个设置导致的。
   → https://github.com/QwenLM/qwen-code/pull/16185

9. **#15562 — feat(desktop)：跨重启持久化 Web Shell 目录** —— 通过白名单 IPC 接口在桌面 shell 中保存守护进程连接和工作区主机目录。
   → https://github.com/QwenLM/qwen-code/pull/15562

10. **#15473 — fix(desktop)：安装更新前停止捆绑运行时** —— Windows 应用内更新现在先关闭守护进程（关闭时终止 job 对象），避免 Node 二进制文件被锁定。
    → https://github.com/QwenLM/qwen-code/pull/15473

## 5. 热门讨论

本数据集未提供 Discussion 数据；本节省略。

## 6. 功能需求趋势

- **隐藏行为的可配置性**：图像驱逐（#15495）、压缩和会话限制（#13113）的关闭开关与声明式设置。
- **更好的平台感知**：Windows 上的 shell 检测（#15310）、长路径处理（#15412）。
- **可观测性与遥测**：OpenRouter 路由提供商/生成 ID 作为 span 属性（#14629）；持久化会话诊断（#16475）。
- **记忆与可扩展性**：可插拔长期记忆提案（#16429 bzmem）、MCP OAuth 健壮性（#16457）。
- **UI/UX**：OSC 7501 程序状态报告（#13870）、Web Shell 内联分支聊天（#16474）。
- **多代理可靠性**：审批展示与后台视图 UX（#16425、#16444）。

## 7. 开发者痛点

- **CI 不稳定占据维护者主要注意力** —— 机器人提交的主 CI 失败（#14724、#15712、#16377、#16191）伴有超长评论线程，挤占了功能开发。
- **长会话脆弱** —— 二次方快照增长（#13113）、自动压缩副作用（#16418）和后台 shell 状态卡死（#15800）均会降低长会话体验。
- **静默失效消耗 token** —— 无头自动记忆（#15597）和图像驱逐（#15495）做了用户看不见也无法控制的事。
- **认证/多服务器摩擦** —— MCP OAuth 端口冲突（#16457）阻塞多服务器配置。
- **Windows 平台差距** —— shell 假设（#15310）和更新时运行时锁定（#15473）。
- **跨平台路径处理** —— 绝对路径展平破坏 macOS 项目（#15412）。

</details>

---
*本日报由 [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar) 自动生成。*