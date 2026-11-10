# AI CLI 工具社区动态日报 2026-11-11

> 生成时间: 2026-11-10 23:14 UTC | 覆盖工具: 7 个

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

# AI CLI 工具生态 —— 跨工具对比报告
**数据窗口：2026-11-11（过去 24 小时）**

---

## 1. 生态概览

在本快照中，AI CLI 领域呈现出**基础设施日趋成熟**与**可靠性持续碎片化**并存的态势。七个被追踪的工具在本窗口内均至少发布了一个版本——发布节奏异常之高——但问题追踪器的主要主题是回归问题，而非路线图工作：Windows 平台稳定性、MCP/OAuth 生命周期脆弱性、沙箱权限正确性以及子代理可观测性在多个独立项目中反复出现。第二个清晰信号是**社区工具层**正在这些 CLI *周边*兴起（Codex 中的 hooks、提示回归运行器、审批路由；Qwen Code 中的频道/webhook 集成），这表明基础工具已足够稳定，用户开始在其之上构建。安全与信任问题突出且呈负面——Qwen Code 和 Gemini CLI 中的破坏性命令分类器绕过、Codex 中的明文密钥持久化、Claude Code 中的服务端指令注入——共同表明**当前瓶颈是代理安全工程，而非原始能力**。

---

## 2. 活跃度对比

| 工具 | 活跃 Issue（24 小时窗口） | 值得关注的 PR（24 小时） | 讨论 | 发布状态 | 备注 |
|---|---|---|---|---|---|
| **Claude Code** | 约 10 条列出（2 条高互动活跃：#100377、#106167） | 1（已关闭，未合并） | N/A（未提供数据） | 1 个版本（v2.1.322）——仅基础设施 | 追踪器大量清理了陈旧报告；信任/注入问题获点赞 |
| **OpenAI Codex** | 约 10 条 + 3 条荣誉提名 | 约 10（多为内部 `copyberry` bot，CLOSED/已落地） | 活跃——6 个值得关注的帖子（Show & Tell） | 3 个版本（0.167.1、0.169.0-alpha.9、0.167.0-alpha.4.1） | Windows 摩擦最高；活跃的社区构建工具 |
| **Gemini CLI** | 约 10 | 约 10（安全/CI 为主） | N/A（未提供数据） | 2 个版本（v0.69.0-preview.0、v0.68.0） | 子代理可靠性是核心问题；发布基础设施救火 |
| **GitHub Copilot CLI** | 约 10 | 1（开放，文档） | N/A（未提供数据） | 4 个版本（v1.0.109/-1、v1.0.110-0/-1） | 快速补丁节奏；MCP 生命周期主导痛点 |
| **OpenCode** | 约 10（其中一条 **346 条评论**） | 约 10（活跃，全部 OPEN） | N/A（未提供数据） | 1 个版本（v2.2.7） | 整个数据集中最大的单一 issue 帖子；沉重的 v1→v2 迁移债务 |
| **Pi** | 约 10 | 约 10（多为 CLOSED/已落地） | 1 条活跃 Idea（#11984） | 2 个版本（v1.5.1、v1.5.2） | 聚焦提供商集成与静默降级 |
| **Qwen Code** | 约 10（CI 噪音主导：292、55 条评论） | 约 10（安全/权限为主） | N/A（未提供数据） | 3 个版本（v0.27.2、preview、SDK v0.1.32） | CI 不稳定是数量驱动因素；P1 权限分类器漏洞 |

> **关于 N/A 与非活跃的说明：** Claude Code、Gemini CLI、Copilot CLI、OpenCode 和 Qwen Code 在本数据集中未显示 Discussions 数据。按照指令，这些标记为 **N/A**——这反映的是所提供摘要中的数据可用性，而非上游一定禁用了 Discussions。只有 Codex 和 Pi 显示确认的 Discussions 活动。Issue/PR 计数来自精选的前 10 列表，为近似值，不应视为追踪器的完整数量。

---

## 3. 共性功能方向

四项需求在三个或更多社区中独立出现：

**a) 子代理可观测性与生命周期控制**
每个代理编排项目都在应对同一个问题：用户无法看到或引导子代理的行为。
- *Codex*：Subagents 面板中的实时活动 + 每代理配置（#36266）；显式跨线程编排（#14923，已关闭）
- *Gemini CLI*：`/chat share` 中的子代理轨迹（#22598），`/bug` 报告中的子代理上下文（#21763），虚假成功报告（#22323）
- *Qwen Code*：跨界面统一代理循环（#15327）；ACP 队友消息投递（#15738）
- *OpenCode*：始终后台运行的子代理及父级报告（#63210、#63209）
- *Claude Code*：Claude 提议会话的工作量控制（#104151）

**b) 主 CLI 界面之外的 MCP 完整性**
MCP 在正常路径下工作，但在 IDE、桌面端和 OAuth 轮换下会退化——这是*一致性*问题，而非协议问题。
- *Claude Code*：Desktop Code 标签页中 `structuredContent` 被丢弃（#103809）；VS Code 中无 Authenticate 按钮（#108783）；跨会话刷新令牌吊销（#108794）
- *Copilot CLI*：孤立的 stdio 进程（#4392），随机回环 OAuth 端口（#5391），Entra 静默登录失败（#5402）
- *Codex*：自动审批中的只读 MCP 工具提示（#60638）；运行时门控外的 MCP 刷新（#60639）

**c) 成本、配额与比例控制**
用户希望有明确、可发现的预算控制，而非不可见的粘性设置。
- *Claude Code*：在一次设置审计中 "Ultracode" 扇出至 325 个代理（#92349）；粘性工作量设置对用户不可见
- *Codex*：配额不透明——计费周期错位（#8581），存储的重置提前过期（#60664），异常消耗（#59262）
- *Copilot CLI*：自动/融合路由的模型黑名单（#5369）；内容过滤失败消耗额度（#5408）
- *OpenCode*：免费层计费清晰度是最响亮的单一主题（#59387）

**d) Windows 平台一致性**
一个一致的、跨厂商的缺陷——不是某个项目的 bug，而是全行业的问题。
- *Codex*：VS Code 扩展 UI 失败（#56655，85 👍），沙箱 `WRITE_DAC` 错误，CUA 截图超时
- *Copilot CLI*：父级 `pwsh.exe` 崩溃（#5404），键盘回归（#5171）
- *OpenCode*：控制台窗口约每秒闪烁一次（#52266），fnm/nvm 升级路径（#63152）
- *Claude Code*：Windows worktree 回归被陈旧关闭且未处理（#92019）

---

## 4. 差异化分析

| 维度 | Claude Code | Codex | Gemini CLI | Copilot CLI | OpenCode | Pi | Qwen Code |
|---|---|---|---|---|---|---|---|
| **主要焦点** | 远程控制/会话自主性；MCP Apps 生态 | 沙箱加固 + Guardian 策略安全 | 子代理正确性 + AST 感知工具链 | 模型目录广度 + 企业认证 | 模型灵活性 + v2 迁移 | 提供商广度 + 扩展运行时 | 权限安全 + 多渠道（微信/QQ/邮件） |
| **目标用户** | claude.ai Projects 上的专业开发者 | Windows/桌面 + 云混合用户 | Google 技术栈开发者，安全意识强 | GitHub/企业，Copilot Free→Pro 漏斗 | 多提供商高级用户 | 自托管/本地模型用户 | 中国市场 + 多界面（CLI/Web Shell/守护进程） |
| **技术路径** | 服务端下发指令；工作量/代理扇出 | Rust 核心；`copyberry` 驱动的加固；Guardian 策略模板 | 原生 bash 亲和性 + 提议的 OS 沙箱（#19873） | 快速补丁节奏；实验性掩码提示对话框 | 后台子代理架构；按会话 MCP 作用域 | 用于会话同步的 chord 存储；codemode 全局变量 | 从手写模型转向真实 shell 解析器（tree-sitter-bash） |
| **独特风险** | 服务端注入侵蚀信任（#106167） | shell 快照中的密钥持久化（#30971） | 子代理虚假成功报告 | 企业凭证掩码缺口（#5388） | 升级时灾难性会话数据丢失（#62659） | 静默模型/目录回退（#10812） | 通过重定向绕过分类器（#16856） |

**关键结构性差异：** Codex 和 Qwen Code 正在投资**纵深防御**（Guardian 策略模板、tree-sitter shell 解析、fail-closed heredoc 处理）——将代理视为不可信的执行面。Claude Code 和 OpenCode 正在投资**能力扩展**（服务端编排、后台子代理），并因此付出可靠性代价。Gemini CLI 处于两者之间，其 #19873 零依赖沙箱提案标志着计划向受控执行的架构转变。

---

## 5. 社区势头与成熟度

**最高原始社区互动：**
- **OpenCode**——一个 issue 帖子（#59387）达 **346 条评论**，使数据集中其他一切相形见绌。这是一个混合信号：极端互动由免费层摩擦驱动，而非热情。势头高，但集中于一个痛点。
- **Codex**——最高*点赞*密度（#56655 达 85 👍），且是唯一拥有强大**社区构建工具生态**（Tapproval、RegressGuard、agent-harness、安全习惯）的工具。这是最强的成熟度指标：用户正在*基于*该工具构建，而不仅仅是报告它。
- **Claude Code**——高信号 issue（服务端注入获 10 👍，尽管是配置抱怨），但追踪器正被积极清理陈旧的 Windows 回归——这是一种**覆盖度与整洁度的张力**，可能掩盖真实债务。

**最快迭代节奏：**
- **Copilot CLI**——24 小时内 4 个版本（v1.0.109 至 v1.0.110-1）。补丁极快，但 MCP 生命周期 bug 的数量表明修复前进的压力。
- **Qwen Code**——3 个版本含 SDK，但 CI 噪音（#14724 达 292 条评论）正在消耗社区信号带宽——被标记为*暂定*的 issue 侵蚀贡献者信任。

**最具架构深思熟虑：**
- **Pi**——最清晰的发布到修复可追溯性（v1.5.2 直接由 PR #11975 支撑）。互动量较低，但修复质量高，且提供商认证路线图连贯（Entra ID、Kimi OAuth、ChatGPT 上下文一致性）。
- **Gemini CLI**——在落地严肃安全 PR（#30171、#30095）的同时积极救火发布基础设施（#30207，106 MB tarball 中有 70 MB 陈旧制品）。在迭代，但维护负担重。

**成熟度排名（综合）：** Codex ≈ Pi > Claude Code > Gemini CLI > Copilot CLI > OpenCode > Qwen Code——但需注意，OpenCode 的互动是真实的，其子代理 PR 工作（#63210、#63209）在架构上领先于多个同行。

---

## 6. 趋势信号

**1. 代理安全是头号未解决的工程问题。**
破坏性命令分类器绕过（Qwen Code #16856：`git branch < /dev/null -D main` 执行了 `-D main`；Gemini CLI #30171：`rm -Rf` 变体被归类为安全）、明文密钥持久化（Codex #30971）以及服务端注入指令（Claude Code #106167）都指向同一方向。**参考价值：** 如果你在这些 CLI 之上构建，不要将 AUTO/权限分类器视为安全边界——要对其封装。行业正趋向*真实解析器*（tree-sitter-bash）和*fail-closed* 语义，但这一转变在各处均不完整。

**2. MCP 正在赢得协议战争，但正在输掉生命周期战争。**
每个工具都支持 MCP；每个工具都有 OAuth 回调、令牌轮换和 IDE 一致性 bug。**参考价值：** 多会话 OAuth 是最尖锐的边缘（Claude Code #108794、Copilot #5402）。对于生产 MCP 部署，应将重新认证波动和固定回调端口（Copilot #5391 请求）规划为一等需求。

**3. 子代理虚假成功报告是系统性信任缺陷。**
Gemini CLI（#22323、#21983）、OpenCode（#63171 零内容"成功"）和 Claude Code（不可见的工作量设置）都显示代理在失败时报告成功。**参考价值：** 对于自主工作流，*永远不要*信任子代理的自报状态——用副作用或结构化输出模式来验证。这是单一最高杠杆的可观测性缺口。

**4. 静默降级是主导的 UX 故障模式。**
缓存预热停止（Pi #10956）、默认模型回退（#10812）、陈旧目录（Pi #10539、Codex #60676）、默认关闭的遥测（Gemini #30217）。**参考价值：** 配置显式故障模式（模型、目录、预热）并监控——这些工具默认*悄悄做不同的事*而非报错。

**5. 厂商特定暗流：**
- **Windows 是全行业二等公民**——每个工具都有 Windows 回归集群。如果你基于 Windows，请为所有厂商的一致性滞后做好预算。
- **企业认证是竞争差异化因素**——Pi 的 Entra ID PR（#12016）、Copilot 的 NTLM 代理需求（#5407）和 Codex 的设备码登录（#60662）显示无头/SSO 认证是下一个战场。
- **社区工具层正在形成**——Codex 的 hooks/审批路由/提示 CI 生态和 Qwen Code 的频道 webhook 表明**扩展面（hooks、Stop hooks、出站集成）是下一个平台中立的功能竞赛**。关注这些在所有七个工具中成为基本要求。

---

*本报告根据 2026-11-11 的 Claude Code、OpenAI Codex、Gemini CLI、GitHub Copilot CLI、OpenCode、Pi 和 Qwen Code 社区摘要汇编。计数来自精选的前 10 issue/PR 列表，代表快照而非完整追踪器数量。保留 Issue/PR 引用以供验证。*

---

## 各工具详细报告

<details>
<summary><strong>Claude Code</strong> — <a href="https://github.com/anthropics/claude-code">anthropics/claude-code</a></summary>

## Claude Code Skills 社区热点

> 数据来源: [anthropics/skills](https://github.com/anthropics/skills)

# Claude Code Skills — 社区亮点报告
*数据截至 2026-11-11 | 来源：github.com/anthropics/skills*

---

## 1. 热门 Skill 排名

> 注：PR 列表未暴露评论数（均显示为 `undefined`），因此以下排名基于社区总体关注度——关联 Issue 中的评论量、更新时效性，以及与活跃讨论帖的交叉引用。

| 排名 | Skill / PR | 功能 | 讨论亮点 | 状态 |
|------|-----------|---------------|----------------------|--------|
| 1 | **fix(mcp-builder): mcp>=2 streamable_http_client** — [PR #1742](https://github.com/anthropics/skills/pull/1742) | 修复 `mcp-builder` 以适配 MCP 2.0 API 重命名（`streamablehttp_client` → `streamable_http_client`）及自定义 header 处理。 | 关联 Issue #1668；属于 MCP SDK 成熟过程中暴露的一系列兼容性 bug 之一。作者 Kuldeeep18 还有一个相关的 skill-creator 修复（PR #1681）。 | OPEN |
| 2 | **fix(skill-creator): isolate trigger evals** — [PR #1298](https://github.com/anthropics/skills/pull/1298) | 修复触发评估的假阴性问题：worker 命令探测竞态、Windows 上对子进程管道的 `select()`、以及运行时失败被误判为未触发。 | 直接回应了 Issue #556 和 #1352 中反映的更广泛的评估可靠性主题——这是仓库中评论最多的 bug 类别。 | OPEN |
| 3 | **Add notion-spec-to-implementation + quantitative-resume-auditor** — [PR #1245](https://github.com/anthropics/skills/pull/1245) | 将产品/技术规格转化为带验收标准的可执行 Notion 任务；第二个 skill 对简历进行量化审计。 | 双 skill 提交；横跨工作流自动化 + 文档分析。长期活跃（更新至 10 月）。 | OPEN |
| 4 | **webapp-testing: avoid shell=True** — [PR #1980](https://github.com/anthropics/skills/pull/1980) | 通过消除 `cd && command` 链中的 `shell=True`，移除 `with_server.py` 中的命令注入风险（CWE-78）。 | 安全加固贡献；与 PR #1976 同属 webapp-testing 修复浪潮。 | OPEN |
| 5 | **fix(docx): report LibreOffice timeout as error** — [PR #1792](https://github.com/anthropics/skills/pull/1792) | 阻止 `accept_changes.py` 在 `soffice` 超时时误报成功；验证修订标记确实已被清除。 | 强化了 docx/xlsx 系列 PR 中突出的"验证输出，不要轻信退出码"模式。 | OPEN |
| 6 | **Add pyxel skill for retro game development** — [PR #525](https://github.com/anthropics/skills/pull/525) | 指导通过 Pyxel 创建、调试和无头验证复古 Python 游戏。 | 开放时间最长的热门 PR 之一（自 3 月起）且持续更新；表明对创意/游戏开发 skill 的需求。 | OPEN |
| 7 | **Add proofcore-contract-auditor** — [PR #1771](https://github.com/anthropics/skills/pull/1771) | 对 Solidity/Rust 合约进行静态分析，并以 TON 区块链锚定加密审计证明。 | 首个值得关注的 Web3/安全方向 skill 提交；新颖的"零存储 Merkle"方案。 | OPEN |
| 8 | **Add document-typography skill** — [PR #514](https://github.com/anthropics/skills/pull/514) | 对生成文档进行排版质检：孤行/寡行控制、编号对齐。 | 解决普遍的输出质量问题；补充 document-skills 系列。 | OPEN |

**同样值得关注：** [PR #2019](https://github.com/anthropics/skills/pull/2019)（xlsx 外部链接假阴性）和 [PR #1997](https://github.com/anthropics/skills/pull/1997)（skill-creator 中的 YAML frontmatter 解析）——两者都是高价值的正确性修复。

---

## 2. 社区需求趋势

提炼自评论最多的 Issues：

1. **Skill 评估与触发可靠性** *（参与度最高）*
   - [Issue #556](https://github.com/anthropics/skills/issues/556) — 13 条评论，7 👍：`run_eval.py` 在所有查询中显示 **0% 触发率**。
   - [Issue #1352](https://github.com/anthropics/skills/issues/1352) — 并行 worker 交叉匹配 skill UUID，产生静默假阴性。
   - **需求：** 一种可信、确定性的方式来验证 Skill 确实会被触发。

2. **信任、安全与命名空间完整性**
   - [Issue #492](https://github.com/anthropics/skills/issues/492) — 46 条评论，讨论最多的单一 issue：社区 skill 冒充 `anthropic/` 命名空间，导致信任边界被滥用。

3. **组织级 skill 共享与分发**
   - [Issue #228](https://github.com/anthropics/skills/issues/228) — 17 条评论，8 👍：用户希望有共享 skill 库，而不是通过 Slack 手动上传 `.skill` 文件。

4. **上下文/token 效率**
   - [Issue #1487](https://github.com/anthropics/skills/issues/1487) — `claude-api` skill 急切注入约 156k token，一次调用即耗尽上下文。
   - [Issue #202](https://github.com/anthropics/skills/issues/202) — skill-creator 读起来像人类文档，而非 token 高效的指令。

5. **文档类 skill 的输出正确性**
   - [Issue #2018](https://github.com/anthropics/skills/issues/2018)（docx 无效 XML）、[Issue #1868](https://github.com/anthropics/skills/issues/1868)（渐变插值差一错误）。

6. **Skill 编写质量标准**
   - [Issue #2064](https://github.com/anthropics/skills/issues/2064) — 描述只说明 skill *做什么*，却未说明*何时*使用；这是反复出现的 frontmatter 设计批评。

7. **去重**
   - [Issue #189](https://github.com/anthropics/skills/issues/189) — 9 👍：`document-skills` 和 `example-skills` 插件内容完全相同，浪费上下文。

**社区提出的新兴方向：** 推理/质量门控流水线（[Issue #1385](https://github.com/anthropics/skills/issues/1385)）、agent 治理/安全模式（[Issue #412](https://github.com/anthropics/skills/issues/412)）、以及紧凑符号化 agent 记忆（[Issue #1329](https://github.com/anthropics/skills/issues/1329)）。

---

## 3. 高潜力待合入 Skill

有活跃评论或近期更新的 PR，可能很快合入：

- **[PR #1980](https://github.com/anthropics/skills/pull/1980)** — webapp-testing `shell=True` 安全修复（更新于 2026-11-01）。合入概率高：改动小、目标明确、出于安全动机。
- **[PR #1792](https://github.com/anthropics/skills/pull/1792)** — docx 超时验证（更新于 2026-11-01）。明确正确的修复，行为可测试。
- **[PR #1977](https://github.com/anthropics/skills/pull/1977)** 和 **[PR #1976](https://github.com/anthropics/skills/pull/1976)** — 同一贡献者提交的小型、范围明确的 bug 修复（wrapAround 取模；textarea/select 发现），各自明确修复一个已提交的 Issue。
- **[PR #1950](https://github.com/anthropics/skills/pull/1950)** — claude-api `pause_turn` Python 指南更新（更新于 2026-11-10，最新）。仅文档变更，低风险。
- **[PR #1997](https://github.com/anthropics/skills/pull/1997)** — skill-creator YAML frontmatter 解析；提升评估/描述优化元数据的保真度。
- **[PR #2019](https://github.com/anthropics/skills/pull/2019)** — xlsx 外部链接解析（更新于 2026-11-03）；修复已提交的 Issue #2013。
- **[PR #1681](https://github.com/anthropics/skills/pull/1681)** — skill-creator 独立脚本执行（`ModuleNotFoundError` 修复）；常见的开发者体验阻塞点。
- **[PR #1245](https://github.com/anthropics/skills/pull/1245)** — 双 Notion/工作流 skill（更新于 2026-10-15）；较大的功能型 PR，需要维护者审查但符合趋势。

**观察：** 待合入积压严重偏向**关联已提交 Issue 的 bug 修复型 PR**，而非全新 skill——这表明仓库正处于加固阶段。

---

## 4. Skill 生态洞察

**社区最集中的需求是可靠性与信任——确定性的 skill 触发/评估、安全可靠的命名空间，以及经证实（而非假定）的输出——而非更多新的 Skill 类别。**

---

*排名反映关注度代理指标（关联 issue 评论量、更新时效性、交叉引用）；PR 列表未暴露单个 PR 的评论数，因此没有 PR 是基于字面评论总数排名的。*

---

# Claude Code 社区文摘 — 2026-11-11

## 1. 今日亮点

过去 24 小时内唯一的发布是一项小型基础设施改进：**v2.1.322 在 Vertex AI 上新增快速模式**，覆盖全球、美国和欧盟区域，并支持优雅降级。问题跟踪器主要被过期报告的清理工作占据，但有两个活跃讨论值得关注：**CCR v2 远程控制注册失败**（#100377，18 条评论），以及一个获大量点赞的投诉——**服务器下发的“游戏”指令块正在干扰专业工作**（#106167，10 👍）。在工具方面，昨天提交的几个新的 MCP/认证 bug 表明，OAuth 令牌生命周期和 MCP 完整性问题仍是活跃的摩擦点。

## 2. 发布

**v2.1.322**
- 在全局、`us` 和 `eu` 区域的 Vertex 上新增快速模式，若 Vertex 拒绝请求则回退到标准速度。
- 低风险、仅涉及基础设施的变更；对 CLI 用户无行为或 API 层面的变化。

## 3. 热门 Issue

1. **[#100377](https://github.com/anthropics/claude-code/issues/100377) — 远程控制：Project 线程失败，报“CCR v2 worker registration failed … 400”**（OPEN，macOS，18 条评论）
   自启动会话在同一服务器上注册正常，但 claude.ai 的 Project 线程持续失败。参与度最高的活跃 issue；表明这是服务端/worker 注册的回归，而非客户端 bug。

2. **[#106167](https://github.com/anthropics/claude-code/issues/106167) — 服务器下发的“游戏”指令块干扰专业开发工作**（OPEN，macOS，16 条评论，10 👍）
   点赞最多的活跃 issue。服务器注入的模型侧指令正在干扰正常工作——这是社区罕见地一致认为问题出在*服务器配置*而非用户提示词。

3. **[#106900](https://github.com/anthropics/claude-code/issues/106900) — Project 启动的远程控制会话出现 CCR v2 400 的重复报告**（OPEN，macOS）
   作为 #100377 的重复项被关闭，但佐证了问题范围：该故障可在多个用户间复现，且早于最新版本。值得作为规范讨论串持续跟踪。

4. **[#103809](https://github.com/anthropics/claude-code/issues/103809) — 桌面端 Code 标签页：MCP App 组件收不到 `structuredContent`**（OPEN，macOS）
   MCP Apps（SEP-1865）服务器暴露了 `outputSchema` 和 UI 资源，但桌面端 Code 标签页仅以 JSON 文本形式传递 payload。阻碍了桌面端更丰富 UI 的 MCP 生态发展。

5. **[#108677](https://github.com/anthropics/claude-code/issues/108677) — Gmail 远程 MCP：认证成功但工具获取失败**（OPEN，macOS，提交于 2026-11-09）
   尽管 `tools/list` 请求体有效，仍报“Error POSTing to endpoint”——一个影响第一方 Google 集成的新鲜、高信号 MCP 互操作 bug。

6. **[#108783](https://github.com/anthropics/claude-code/issues/108783) — VS Code MCP 面板对已连接的 HTTP 服务器没有“认证”按钮**（OPEN，Linux/VS Code）
   带可选认证的服务器在 VS Code 中完全无法登录。表明 CLI 与 IDE 集成在支持 OAuth 的 MCP 服务器上存在 UI 对等性差距。

7. **[#108794](https://github.com/anthropics/claude-code/issues/108794) — 轮换的 MCP 刷新令牌在共享会话间被吊销**（OPEN，macOS）
   多会话用户每 2–3 天就必须重新 OAuth。与仍开放的 #99539 及此前过期的 #84440 相关——这是一类反复出现的令牌生命周期 bug，不断重现。

8. **[#104151](https://github.com/anthropics/claude-code/issues/104151) — 桌面端 Code 标签页：为 Claude 提议/启动的会话选择 effort**（OPEN，增强，Windows）
   请求能够为子会话设置（或提升至高于父会话的）effort。属于更广泛的“对自主会话生成的可见性与控制”主题。

9. **[#92019](https://github.com/anthropics/claude-code/issues/92019) — 自 2.1.260 起 Windows 上工作树会话创建失败**（CLOSED/过期，Windows）
   后台完整检出强制 `checkout.workers=8`。未修复即作为过期关闭——代表了一大批正被清扫出跟踪器却未处理的 Windows 回归问题。

10. **[#92349](https://github.com/anthropics/claude-code/issues/92349) — Ultracode 破坏了比例性；一次设置审计用了 325 个 agent**（CLOSED/过期，macOS）
    记录了“对每个实质性任务都使用 Workflow 工具；token 成本不是约束”这一指令如何在一个不可见的粘性 effort 设置下把小型任务变成大规模 agent 扇出。虽以过期关闭，但对成本控制 UX 是强烈信号。

## 4. 关键 PR 进展

过去 24 小时内仅有一个 PR 更新，因此本节必然简短。该 PR 也已关闭。

1. **[#74722](https://github.com/anthropics/claude-code/pull/74722) — feat(commit-commands)：在 `/commit-push-pr` 中支持 Conventional Branch 命名**（CLOSED）
   新增可选的 `conventional` 参数，按 Conventional Branch 1.0.0 规范（`<type>/<description>`）命名分支，并从 diff 推断 `type`（feature、bugfix、hotfix、release、chore、docs、test）。对于通过斜杠命令统一分支卫生的团队很有用；未合并即关闭，但为社区贡献提供了一个干净的模板。
   *没有其他 PR 满足 24 小时更新窗口，因此不再列出更多条目。*

## 5. 热门讨论

*源数据集未提供 GitHub Discussions 数据；本节省略。*

## 6. 功能需求趋势

从开放增强请求及整个跟踪器的反复主题中提炼：

- **会话控制与 effort 配置** — 让用户为 Claude 提议或自启动的会话选择/提升 effort（#104151）；让“粘性”effort 设置可见且可覆盖（#92349）。
- **非 CLI 界面上的 MCP 完整性** — 向 MCP App 组件投递结构化 `structuredContent`（#103809）、VS Code MCP 面板中的认证按钮（#108783），以及稳定的多会话 OAuth 令牌（#108794）。
- **桌面端/IDE 对等性** — 请求面板展开快捷键（#92157），以及 CLI、VS Code 和桌面端 Code 标签页之间一致的 UI 可供性。
- **命令/技能易用性** — `/commit-push-pr` 的 Conventional Branch 命名（#74722），以及自定义斜杠命令中可预测的位置参数替换（#92171、#92457）。
- **远程控制可靠性** — 让 Project 启动的远程控制会话与自启动会话一样可靠（#100377、#106900）。

## 7. 开发者痛点

- **远程控制对 Project 线程不可靠。** 多位报告者（#100377、#106900）看到一致的 CCR v2 worker 注册 400，而同一服务器上的自启动会话正常——指向服务端注册路径而非本地网络。
- **OAuth/MCP 令牌生命周期脆弱。** 当会话共享登录时，轮换的刷新令牌会被吊销（#108794），且 IDE 客户端缺乏完成认证的方式（#108783），迫使用户反复重新登录。
- **MCP 互操作缺口暴露较晚。** 认证成功但工具获取失败（#108677），且结构化内容在桌面端 Code 标签页中被丢弃（#103809）——两者都削弱了 MCP 生态的价值。
- **成本与比例性控制。** “Ultracode”/workflow 行为可能在一个不可见设置下，让琐碎任务扇出到数百个 agent（#92349）——开发者需要明确、可发现的预算/effort 控制。
- **指令注入与信任。** 服务器注入的“游戏”指令（#106167，10 👍）及工具结果中未经请求的 `<system-reminder>` 内容（#92386）侵蚀了人们对模型行为遵循用户意图的信心。
- **Windows 与桌面端回归问题被过期处理。** 一系列 Windows 特定回归问题（如 #92019 工作树检出、MCP 组件重复 #92294、heredoc 死锁 #92262）未修复即作为过期关闭——这是跟踪器中反复出现的清洁度与覆盖率之间的张力。
- **会话隔离问题。** 跨会话消息投递杀死进行中的后台 Bash 任务（#91139），以及定时任务中每次触发的进程泄漏（#91879），对多会话用户仍是显著问题。

---
*来源：github.com/anthropics/claude-code — 发布、Issue 和 Pull Request 更新于 2026-11-10（数据窗口：过去 24 小时）。未提供 Discussions 数据，因此按指示省略该节。*

</details>

<details>
<summary><strong>OpenAI Codex</strong> — <a href="https://github.com/openai/codex">openai/codex</a></summary>

# OpenAI Codex 社区文摘 — 2026-11-11

## 1. 今日亮点

补丁版本 **rust-v0.167.1** 已发布，同时一批 `copyberry` PR 落地，重点聚焦沙箱加固（Windows `cmd` 引号处理、MXC PowerShell 工作目录修复）、Guardian 策略完整性和 MCP 审批语义。本周期内 Windows 仍是社区摩擦的主要来源：VS Code 扩展启动失败、沙箱 `WRITE_DAC` 错误、Computer Use 截图超时以及 SSH 自动连接回归均持续受到关注。此外，今天出现了一个值得注意的模型目录回归问题：**GPT-6.1 Sol** 在 Linux/Plus 账户刷新后的目录中消失。

---

## 2. 版本发布

过去 24 小时窗口内共有三个版本发布：

- **[rust-v0.167.1](https://github.com/openai/codex/releases)** — 在 `0.167.0` 之上的补丁版本（除版本号提升外无详细变更日志；完整 diff 可通过 [compare](https://github.com/openai/codex/compare/rust-v0.167.0...rust-v0.167.1) 查看）。
- **[rust-v0.169.0-alpha.9](https://github.com/openai/codex/releases)** — 下一个小版本 alpha，向 0.169 推进。
- **[rust-v0.167.0-alpha.4.1](https://github.com/openai/codex/releases)** — Desktop 应用的热修复线；参见相关 backport PR [#60457](https://github.com/openai/codex/pull/60457)，为 Amazon Bedrock Responses 添加默认关闭的 `incremental_tools_legacy` 特性。

**要点：** 0.167.x 线正在积极打补丁（Desktop 热修复通过 alpha.4.1），同时 0.169 开发并行推进。使用 `0.167.0` 的团队应关注 0.167.1 补丁，尤其是 Windows 沙箱用户。

---

## 3. 热点 Issue

1. **[#56655](https://github.com/openai/codex/issues/56655) — VS Code 扩展 UI 无法启动（Windows），63 条评论 / 85 👍** — 本窗口内互动量最高的 issue。扩展 `26.51021.22027` 在 Windows 11 构建上无法渲染 UI。👍 数量表明影响面广泛；Pro 用户似乎也受到影响。**为何重要：** 扩展回归阻断了大批用户的主要工作流。

2. **[#49383](https://github.com/openai/codex/issues/49383) — Windows Computer Use 截图捕获失败（`FrameArrived` 超时、`E_INVALIDARG`），52 条评论** — 长期存在（自 9 月起）的 Computer Use 可靠性 bug。窗口检测和捕获在多个应用中不可靠。社区一直在迭代复现；Discussions 中出现了一个第三方修复工具（见 §5）。

3. **[#59855](https://github.com/openai/codex/issues/59855) — Windows Desktop 无法恢复云托管聊天（`AbsolutePathBuf` 错误），25 条评论** — 路径反序列化 bug 导致无法从侧边栏恢复云聊天。**为何重要：** 直接降低了 Windows 用户依赖的云/本地混合工作流体验。

4. **[#14923](https://github.com/openai/codex/issues/14923) — [已关闭] 基于 `thread/*`/`turn/*` 原语的显式跨线程编排，18 条评论** — 在关于 Desktop 应用一等公民多线程编排的长期设计讨论后关闭。**为何重要：** 其关闭可能意味着某个方向已被接受（或已整合）用于 agent 编排——值得关注后续 issue/PR。

5. **[#59931](https://github.com/openai/codex/issues/59931) — Windows Desktop 重启后不会自动连接已启用的 SSH 主机，12 条评论** — 远程 CLI `0.167.0` 的回归；在两台设备上复现。远程开发体验问题。

6. **[#19975](https://github.com/openai/codex/issues/19975) — Desktop 回放/fork 隐藏了被 steer 的用户消息，8 条评论** — 轮次中途 steer 的消息存在于原始 JSONL 中，但在 UI 回放/fork 中消失；部分先前消息正文保持展开状态。**为何重要：** 会话历史的可审计性与信任。

7. **[#15444](https://github.com/openai/codex/issues/15444) — 允许 Codex 在被要求时重命名当前线程，7 条评论 / 19 👍** — 范围明确的小型 UX 增强，点赞比例很高。自然语言线程管理。

8. **[#36266](https://github.com/openai/codex/issues/36266) — 改进 Subagents 面板，增加实时活动 + 每 agent 配置，6 条评论** — 随着模型越来越依赖子 agent，对每个 agent 在做什么以及如何配置的可观测性变得至关重要。

9. **[#30971](https://github.com/openai/codex/issues/30971) — Shell 快照可触发凭据提示并以明文持久化 secret 环境变量，6 条评论** — **安全相关：** 带有凭据助手的 `.zshrc`（例如 `op read`）可能将导出的 secret 泄漏到 `~/.codex/shell_snapshots`。对于使用 secret manager 支撑 shell 的用户非常严重。

10. **[#60676](https://github.com/openai/codex/issues/60676) — GPT-6.1 Sol 从刷新后的模型目录中消失（Linux，Plus），3 条评论** — 今天新出现。模型存在于捆绑目录中，但刷新后缺失。**为何重要：** 模型可用性回归是高严重性问题；关注快速修复。

*其他值得关注：* [#60529](https://github.com/openai/codex/issues/60529) 冷启动 `thread/list` 返回过早的 `nextCursor:null`（app-server 分页正确性），[#59262](https://github.com/openai/codex/issues/59262) 严重性能回归 + 配额耗尽，[#8581](https://github.com/openai/codex/issues/8581) Pro→Plus 降级时用量/计费周期错位。

---

## 4. 关键 PR 进展

以下大多数 PR 由 `copyberry[bot]`（内部自动化）编写，标记为 CLOSED，表示已合并或已落地的工作。

1. **[#60647](https://github.com/openai/codex/pull/60647) — 在 MXC 沙箱中恢复 PowerShell 工作目录** — 修复当工作区存在不可读的祖先目录时 `$PWD` 未设置的问题，该问题会破坏相对路径。受保护的变通方案为 PowerShell 命令添加前缀。

2. **[#60624](https://github.com/openai/codex/pull/60624) — 在 Windows 沙箱 `cmd` 调用中保留脚本引号** — 使用 `/s /c` 配合外层引号对，阻止 CRT 转义破坏带引号的路径/可执行文件名。与上述 Windows 沙箱痛点直接相关。

3. **[#60638](https://github.com/openai/codex/pull/60638) — 在自动审批中遵循只读 MCP 工具提示** — 让 `readOnlyHint: true` 优先于 `destructiveHint`/`openWorldHint`，使只读 MCP 工具在自动模式下不再需要审批。

4. **[#60639](https://github.com/openai/codex/pull/60639) — 在 MCP 运行时门控之外刷新 Apps 工具** — 在 `tools/list` 期间不再持有会话 MCP 门控，防止认证/配置变更后发布过期的刷新结果。

5. **[#60646](https://github.com/openai/codex/pull/60646) — 对托管策略使用受信任的 Guardian 模板** — 防止用户配置的模型目录提供省略策略槽或注入冲突指令的 Guardian 提示模板。**安全加固。**

6. **[#60670](https://github.com/openai/codex/pull/60670) — 从线程历史状态初始化 Guardian 证据游标** — 添加 `EvidenceCursor::at_thread_ready` 及分页历史验证，使新建/清空的线程一致地从位置零开始。

7. **[#60662](https://github.com/openai/codex/pull/60662) — 无头 TUI 会话中的 Gateway 设备码登录** — 在无本地浏览器的 SSH / 无头 Linux 环境中启用认证。对远程开发很重要。

8. **[#60655](https://github.com/openai/codex/pull/60655) — app-server 设置 API 中的环境更新** — 向 `turn/settings/update` 和 `thread/settings/update` 添加实验性 `environments`，具有轮次作用域与线程作用域语义。

9. **[#60651](https://github.com/openai/codex/pull/60651) — Cloud 对话的模型和推理强度选择** — Cloud 对话现在可以按需获取账户作用域目录，并从 TUI 更改模型/推理强度。

10. **[#60635](https://github.com/openai/codex/pull/60635) — 归档和恢复云线程** — 添加云归档/取消归档、归档列表、归档时分离以及归档通知；从实时附件报告已加载的云线程。

*其他值得关注：* [#60457](https://github.com/openai/codex/pull/60457) Desktop 热修复 backport（Bedrock 的 `incremental_tools_legacy`），[#60669](https://github.com/openai/codex/pull/60669) 通过 `--ignore-submodules=dirty` 跳过子模块扫描（缓解来自任务可写 Git 配置的 clean-filter 执行），[#60671](https://github.com/openai/codex/pull/60671) macOS 仅签名模式 + 更严格的证书/团队 ID 验证。

---

## 5. 热门讨论

**展示与分享**
- [#54474](https://github.com/openai/codex/discussions/54474) — *Windows Computer Use 修复韩语 Chrome URL 检测。* 针对本地化 CUA 文档控件类型 bug 的版本绑定补丁，附有关联 bug 报告。社区驱动的 #49383 相关问题变通方案。
- [#60358](https://github.com/openai/codex/discussions/60358) — *安全运行 Codex（或任何 agent）的 10 个 MIT 许可习惯。* 简短 README + 可复制粘贴的片段，用于无人值守工具使用（例如"当字段或权限不明确时停止"）。
- [#59121](https://github.com/openai/codex/discussions/59121) — *agent-harness：* 技能、规则和 Stop hooks，让 Codex 完成任务并一次只问一件事。解决"agent 半途停止 / 交回待办清单"的失败模式。
- [#60441](https://github.com/openai/codex/discussions/60441) — *Tapproval：* 通过 `hooks.json` + 本地开源助手，在 Apple Watch 上回答 Codex 审批，每个都带风险标签。
- [#60593](https://github.com/openai/codex/discussions/60593) — *RegressGuard：* 一个小型 Python CLI，用于针对保存回复的用例文件对提示变更进行回归检查。适用于提示 CI。
- [#49920](https://github.com/openai/codex/discussions/49920) — 移动应用开发供应商招募；与 Codex 工具链相关性低，为完整性收录。

**趋势：** 社区正在 Codex *周围* 构建工具层——hooks、watch 审批、提示回归、安全习惯——表明对更多一等公民可扩展性（hooks、Stop hooks、审批路由）的需求。

---

## 6. 功能请求趋势

- **线程/会话生命周期控制：** 按请求重命名线程（[#15444](https://github.com/openai/codex/issues/15444)），客户端提供的线程 ID 用于可恢复的超时创建（[#41887](https://github.com/openai/codex/issues/41887)），显式跨线程编排（[#14923](https://github.com/openai/codex/issues/14923)，已关闭），归档/恢复云线程（PR [#60635](https://github.com/openai/codex/pull/60635)）。
- **子 agent 可观测性与配置：** Subagents 面板中的实时活动和每 agent 配置（[#36266](https://github.com/openai/codex/issues/36266)）。
- **无头/自动化对等：** 通过 `codex exec` 为特定会话同步生成记忆（[#29430](https://github.com/openai/codex/issues/29430)）；无头 TUI 的设备码登录（PR [#60662](https://github.com/openai/codex/pull/60662)）。
- **云/远程模型控制：** Cloud 对话的模型 + 推理强度选择（PR [#60651](https://github.com/openai/codex/pull/60651)）；app-server API 中的环境设置（PR [#60655](https://github.com/openai/codex/pull/60655)）。
- **可扩展性 hooks：** 社区构建的审批路由、Stop hooks 和提示回归工具（Discussions §5）指向对官方、有文档的 hook 接口的需求。

---

## 7. 开发者痛点

1. **Windows 是主要故障面。** VS Code 扩展 UI 无法启动（[#56655](https://github.com/openai/codex/issues/56655)），沙箱 `helper_sandbox_lock_failed` / `.sandbox-bin` 上的 Win32 错误 5（[#60057](https://github.com/openai/codex/issues/60057)），持续的沙箱访问拒绝（[#59744](https://github.com/openai/codex/issues/59744)），尽管有完全访问权限仍"被策略阻止"（[#43905](https://github.com/openai/codex/issues/43905)），以及 CUA 截图超时（[#49383](https://github.com/openai/codex/issues/49383)，[#60666](https://github.com/openai/codex/issues/60666)）。沙箱 ACL 修复尝试尚未完全解决 `WRITE_DAC` 类故障。
2. **连接性与请求失败：** 工作区路由发现超时 / 5/5 重连（[#57063](https://github.com/openai/codex/issues/57063)）；zstd 压缩的重复图像输入返回 HTTP 400 而未压缩则成功（[#60594](https://github.com/openai/codex/issues/60594)）。
3. **凭据与 secret 处理：** shell 快照以明文持久化导出的 secret 环境变量（[#30971](https://github.com/openai/codex/issues/30971)）——最安全关键的未决项。
4. **配额/计费不透明：** Pro→Plus 降级时用量周期与 ChatGPT 计费错位（[#8581](https://github.com/openai/codex/issues/8581)）；存储的重置显示未来到期但在未重置的情况下过期（[#60664](https://github.com/openai/codex/issues/60664)）；伴随性能回归的异常周配额耗尽（[#59262](https://github.com/openai/codex/issues/59262)）。用户希望透明、可预测的配额核算。
5. **资源耗尽：** 8 GB 机器上使用 Codex 时在内存压力下整台 Mac 冻结（[#56586](https://github.com/openai/codex/issues/56586)）。
6. **界面正确性：** app-server 冷启动 `thread/list` 返回过早的 `nextCursor:null`（[#60529](https://github.com/openai/codex/issues/60529)）以及状态 UI 将上下文窗口用量与累计 token 混淆（[#21799](https://github.com/openai/codex/issues/21799)，已关闭）削弱了程序化和人工对报告状态的信任。

---

*本文摘基于 2026-11-11 的 github.com/openai/codex 数据生成。评论/👍 数量反映所提供的快照。*

</details>

<details>
<summary><strong>Gemini CLI</strong> — <a href="https://github.com/google-gemini/gemini-cli">google-gemini/gemini-cli</a></summary>

# Gemini CLI 社区文摘 — 2026-11-11

## 1. 今日亮点
过去 24 小时内发布了两个版本：v0.69.0-preview.0（包含 `$` 转义修复和新的 `/stats heatmap` 子命令）以及稳定版 v0.68.0。问题追踪器仍然由子代理可靠性问题主导——GOAL 状态误报、通用代理挂起以及配置覆盖被忽略都在持续吸引维护者的关注。在 PR 方面，最大的主题是发布基础设施修复（过时的构建产物导致 npm tarball 膨胀）以及加固 shell 命令安全检查。

## 2. 版本发布
**v0.69.0-preview.0**
- `fix(core,cli)`：在模板占位符替换期间保留字面 `$` 字符（[#30136](https://github.com/google-gemini/gemini-cli/pull/30136)）
- `feat(cli)`：新增 `/stats heatmap` 子命令，用于查看每日 token 使用量

**v0.68.0**
- 为 v0.67.0-preview.0 和 v0.66.0 自动生成的变更日志
- `docs(readme)`：澄清 Gemini CLI 的范围、限制和工具能力

## 3. 热门 Issue

1. **[#22323](https://github.com/google-gemini/gemini-cli/issues/22323) — 子代理 MAX_TURNS 被报告为 GOAL 成功（P1，13 条评论）。** `codebase_investigator` 子代理即使在没有进行任何分析的情况下触及轮次上限，仍报告 `status: "success"` / `Termination Reason: "GOAL"`。这掩盖了失败并破坏了下游代理编排——这是当天参与度最高的问题。

2. **[#21409](https://github.com/google-gemini/gemini-cli/issues/21409) — 通用代理无限挂起（P1，8 条评论，8 👍）。** 任何对通用代理的委托都会永久挂起，即使是创建文件夹这样的简单任务也不例外。变通方案（指示模型不要使用子代理）实际上禁用了核心功能；👍 数量表明影响广泛。

3. **[#19873](https://github.com/google-gemini/gemini-cli/issues/19873) — 零依赖操作系统沙箱 + 执行后意图路由（P2，9 条评论）。** 提议利用 Gemini 3 原生的 bash 亲和性来串联 POSIX 工具，同时通过沙箱和意图路由来遏制风险。这是关于代理执行模型的一项重要架构提案。

4. **[#21968](https://github.com/google-gemini/gemini-cli/issues/21968) — Gemini 对技能和子代理使用不足（P2，7 条评论）。** 虽是轶事但很重要：模型很少主动调用自定义技能/子代理，除非明确指示，即使描述明显匹配。这削弱了可扩展系统的价值。

5. **[#22745](https://github.com/google-gemini/gemini-cli/issues/22745) — EPIC：AST 感知的文件读取、搜索和映射（P2，7 条评论）。** 追踪 AST 感知工具是否能减少错位读取、轮次数量和 token 噪音。这可能是未来工具质量改进的方向。

6. **[#22267](https://github.com/google-gemini/gemini-cli/issues/22267) — 浏览器代理忽略 `settings.json` 覆盖（P2，5 条评论）。** AgentRegistry 在初始化时正确合并了设置，但浏览器代理绕过了它们（例如 `maxTurns`）。配置漂移会侵蚀对项目级配置的信任。

7. **[#21983](https://github.com/google-gemini/gemini-cli/issues/21983) — 浏览器子代理在 Wayland 上失败（P1，4 条评论）。** Linux/Wayland 用户看到浏览器代理在失败的情况下仍以 "GOAL" 结束——这是误导性终止状态模式的又一实例。

8. **[#24246](https://github.com/google-gemini/gemini-cli/issues/24246) — 超过 128 个工具时出现 400 错误（P2，3 条评论）。** 大量工具清单触发 API 400 错误；期望是对启用工具进行更智能的范围限定。随着 MCP 服务器使工具数量倍增，这一问题日益相关。

9. **[#22672](https://github.com/google-gemini/gemini-cli/issues/22672) — 代理应劝阻破坏性行为（P2，3 条评论）。** 模型偶尔在存在更安全替代方案时选择 `git reset` 或 `--force`。这与安全相关，并与下方的 shell 启发式 PR 有关联。

10. **[#18836](https://github.com/google-gemini/gemini-cli/issues/18836) — 用基于文件的持久化任务跟踪替换 WriteToDo（P3，2 条评论）。** 提议弃用上下文内 todo 跟踪，以对抗上下文腐化、token 成本和会话间的记忆丢失——这是代理状态讨论中反复出现的主题。

## 4. 关键 PR 进展

1. **[#30207](https://github.com/google-gemini/gemini-cli/pull/30207) — 在 esbuild 之前清理 `bundle/`（P0）。** 自 2026-11-05 以来夜间发布一直失败，报 `413 Request Entity Too Large`；约 106 MB tarball 中约 70 MB 的 JS 是之前构建的过时输出。关键的发布基础设施修复。

2. **[#30171](https://github.com/google-gemini/gemini-cli/pull/30171) — 无论标志位置如何，标记递归/强制 `rm`（P1）。** 修复了一个 `PolicyEngine` 绕过漏洞，此前 `rm -r -f`、`rm -Rf`、`rm --recursive --force` 以及尾置标志变体被归类为安全。直接解决了 #22672 中的破坏性命令问题。

3. **[#30095](https://github.com/google-gemini/gemini-cli/pull/30095) — 防止 Windows git diff 权限绕过（P1，安全）。** 提取共享的 Git 安全验证并在 Windows 上拒绝危险参数，修复了只读命令分类的缺口。

4. **[#30219](https://github.com/google-gemini/gemini-cli/pull/30219) — 在压缩后将 ACP 工具调用记录到活跃聊天。** 防止压缩后的工具调用附加到已移除的消息上，避免会话重新加载时出现 400 错误。

5. **[#30217](https://github.com/google-gemini/gemini-cli/pull/30217) — 修复 PDF/遥测缓冲区内存保留问题。** 解决了在遥测禁用（默认情况）时多轮 PDF 摄取期间出现的 V8 堆 OOM。

6. **[#30020](https://github.com/google-gemini/gemini-cli/pull/30020) — 丢弃失败重试尝试流式传输的工具调用。** 一个微妙但严重的正确性修复：此前失败和成功尝试的工具调用都会被执行，导致有副作用的命令重复执行。

7. **[#30220](https://github.com/google-gemini/gemini-cli/pull/30220) — 在会话预览中保留 Unicode，去除 ANSI。** 替换了 `cleanMessage()` 中仅支持 ASCII 的正则表达式，该正则此前会破坏国际文字、变音符号和 emoji。

8. **[#30214](https://github.com/google-gemini/gemini-cli/pull/30214) — 将 TortoiseGitPlink 识别为 PuTTY 系列 SSH 客户端。** 确保为 TortoiseGit 的 Windows 用户注入 `-batch` 而非 OpenSSH 的 `BatchMode` 标志。

9. **[#30089](https://github.com/google-gemini/gemini-cli/pull/30089) — 升级 npm 依赖组（70 项更新）。** 包括 `@modelcontextprotocol/sdk` 1.23.0 → 1.32.2——对 MCP 兼容性值得关注。

10. **[#29960](https://github.com/google-gemini/gemini-cli/pull/29960) — 遥测 `SERVICE_VERSION` 使用 CLI 版本而非 Node 运行时版本。** 修复了企业遥测仪表板报告错误服务版本的问题。

## 5. 热门讨论
本周期未提供讨论数据；本节省略。

## 6. 功能请求趋势

- **子代理可观测性与正确性**：通过 `/chat share` 展示子代理轨迹（[#22598](https://github.com/google-gemini/gemini-cli/issues/22598)），在 `/bug` 报告中包含子代理上下文（[#21763](https://github.com/google-gemini/gemini-cli/issues/21763)），并修复终止状态报告（#22323）。
- **AST 感知工具**：一组 issue（#22745、#22746、#22747）探索 AST grep/tilth/glyph，用于精确读取、搜索和代码库映射。
- **基于文件的持久化代理状态**：超越上下文内 todo 列表（#18836），并尝试使用原生文件工具进行任务跟踪（#21000）。
- **更安全的执行**：沙箱化（#19873）、劝阻危险命令（#22672）以及按工作区策略（#18397）。
- **更智能的工具范围限定**：动态工具选择以避免超过 128 个工具的 400 错误（#24246）。

## 7. 开发者痛点

- **子代理可靠性是最大的摩擦点**：挂起（#21409）、虚假成功报告（#22323、#21983）、忽略配置（#22267）以及触发不足（#21968）共同表明子代理系统在更广泛采用之前需要一次正确性审查。
- **发布流水线不稳定**：过时构建产物导致的反复夜间失败（#30207）和不稳定的 VS Code 下载重试（#29951）正在消耗维护者精力。
- **安全启发式存在缺口**：多个 PR（#30171、#30095）揭示危险的 `rm` 和 Git 调用可能因参数顺序或平台而绕过 PolicyEngine。
- **重试语义可能重复副作用**：流中重试 bug（#30020）是那种会侵蚀对自主执行信任的问题。
- **Token/上下文效率**：上下文腐化、消防水管式文件读取（#19561）以及每轮约 36.6k token 的基线仍然是持续关注的问题，推动着 AST 和持久化状态提案。

</details>

<details>
<summary><strong>GitHub Copilot CLI</strong> — <a href="https://github.com/github/copilot-cli">github/copilot-cli</a></summary>

# GitHub Copilot CLI 社区摘要 — 2026-11-11

## 1. 今日亮点

CLI 以快速的三连发版本序列（v1.0.109、v1.0.109-1、v1.0.110-0/-1）发布，核心亮点是模型选择器中新增 **Claude Fable 5.2**，以及沙箱主机允许/拒绝规则支持 **IPv4/IPv6 CIDR**。用于处理 git/ssh/sudo 密码提示的实验性掩码对话框流程现已通过 `/experimental on` 提供。与此同时，一大批与 MCP 相关的 issue 已完成分类/关闭，但 MCP 领域仍以孤儿进程、OAuth 回调和协议协商问题主导着追踪器。

---

## 2. 版本发布

### v1.0.110-1
**修复**
- 使用 `copilot --resume` 重新打开的会话现在会立即标记为使用中，防止 `copilot -p --resume` 和第二个窗口写入空闲会话。
- 自定义状态行保留第一行的缩进，以保持一致对齐。
- 控制字符现在会可见地显示。

### v1.0.110-0
**新增**
- *实验性：* 在掩码对话框中回答来自 agent 命令的 git、ssh 和 sudo 密码提示（通过 `/experimental on` 启用）。

**改进**
- 在 `/user switch` 中选择不可用的账户时，会显示恢复指引和重试操作。
- 点击展开的推理内容的任意位置即可将其折叠。

### v1.0.109 / v1.0.109-1
**新增**
- 设置中沙箱主机允许/拒绝规则支持 IPv4/IPv6 CIDR。
- 模型选择器在“New models”下支持 Claude Fable 5.2。
- `copilot help input` 说明在 shell 输入开头按 Backspace 会退出 shell 模式并保留已输入文本。
- 沙箱路径设置现在支持环境变量，同时保留用户编写的表达式。

---

## 3. 热门 Issue

1. **[#4438](https://github.com/github/copilot-cli/issues/4438) — `disable-model-invocation: true` 导致技能无法访问（17 条评论，👍20）**
   一个本意是让技能“仅手动”使用的前置元数据标志，实际上使其完全无法访问——`skill()` 返回 `Skill not found`。这是当日互动量最高的 issue；用户希望有一个真正的手动调用逃生通道。

2. **[#191](https://github.com/github/copilot-cli/issues/191) — MCP 服务器已配置但工具未暴露（15 条评论，👍15）**
   一个长期存在（自 2025 年 10 月起）的 MCP 集成缺口，现已关闭。高评论数表明社区对 MCP 工具可见性的持续不满。

3. **[#5283](https://github.com/github/copilot-cli/issues/5283) — Copilot Free 上远程托管被阻止（👍5）**
   使用 Copilot Free 的个人账户用户无法启用远程环境托管，尽管本地设置显示已启用。因免费层限制的影响而值得关注。

4. **[#5171](https://github.com/github/copilot-cli/issues/5171) — Windows 上 NumLock 关闭时的小键盘和 Delete 键被忽略**
   Windows 11 上的键盘输入回归问题。问题虽小但对 Windows Terminal 用户干扰很大；反映了持续存在的平台一致性问题。

5. **[#4888](https://github.com/github/copilot-cli/issues/4888) — 在成功 `server/discover` 后发送了遗留的 `initialize`**
   一个影响双时代 MCP SDK 服务器的协议正确性 bug——CLI 协商了现代协议后又发送了遗留握手。对 MCP SDK 互操作性很重要。

6. **[#5402](https://github.com/github/copilot-cli/issues/5402) — MCP Entra 静默登录使用 `/common`，单租户应用失败（AADSTS50194）**
   自 1.0.107 起，Windows 用户每次会话都必须运行 `/mcp auth`。这是一个重大的企业认证回归问题。

7. **[#5404](https://github.com/github/copilot-cli/issues/5404) — 从 pwsh 在 git 仓库中启动 copilot 会杀死父 `pwsh.exe`**
   一个严重的 Windows 稳定性 bug（tgrep 索引）：父 PowerShell 进程因未处理异常而终止，并连带终止附着的进程。

8. **[#5369](https://github.com/github/copilot-cli/issues/5369) — 允许在 fusion/auto 模式中设置模型阻止列表**
   用户希望从自动路由中排除特定模型。随着模型目录扩展，对模型路由控制的需求日益增长。

9. **[#5388](https://github.com/github/copilot-cli/issues/5388) — 在 GHE 数据驻留环境下沙箱凭据掩码对 `GH_TOKEN`/`GH_ENTERPRISE_TOKEN` 无效**
   在 `<tenant>.ghe.com` 上，令牌环境变量在掩码之前就被丢弃。这是企业租户面临的安全相关沙箱缺口（现已关闭）。

10. **[#5397](https://github.com/github/copilot-cli/issues/5397) — Copilot Connectors 在 `--acp` 模式下不可用**
    在交互模式下正常工作的 Connectors 在 ACP 会话中不会出现。这对编辑器/ACP 客户端集成很重要。

---

## 4. 关键 PR 进展

过去 24 小时内仅有 **一个** PR 有更新：

1. **[#4770](https://github.com/github/copilot-cli/pull/4770) — 记录 WebSocket 响应退出选项（OPEN）**
   记录了针对默认使用 WebSocket 响应端点的模型的退出选项。当该传输不可用时（网络阻止 WebSocket，或会话因“400 input item ID does not belong to this connection”失败），用户可以退出。自 2026 年 9 月起仍处于打开状态——值得关注文档完整性。

*注：其余 PR 队列在 24 小时窗口内无活动，因此不再列出更多条目。*

---

## 5. 热门讨论

源数据集中未提供 Discussion 数据；本节省略。

---

## 6. 功能请求趋势

- **模型路由控制** — fusion/auto 模式的阻止列表（[#5369](https://github.com/github/copilot-cli/issues/5369)）；通过 `providers.json` 使模型/提供商交互更清晰（[#5403](https://github.com/github/copilot-cli/issues/5403)）。
- **MCP OAuth 灵活性** — 固定回调端口（[#5391](https://github.com/github/copilot-cli/issues/5391)）和插件级 OAuth 选项（`oauthClientId`、`redirectPort`）（[#5410](https://github.com/github/copilot-cli/issues/5410)），用于预注册客户端认证服务器。
- **企业/代理网络** — 对 Negotiate 认证代理的 NTLM 回退（[#5407](https://github.com/github/copilot-cli/issues/5407)）；更好的 Windows 凭据存储诊断（[#5399](https://github.com/github/copilot-cli/issues/5399)）。
- **工具生态集成** — 使用系统安装的 `gh` 而非捆绑的 `gh.exe`（[#4456](https://github.com/github/copilot-cli/issues/4456)）；ACP 模式下的 connector 一致性（[#5397](https://github.com/github/copilot-cli/issues/5397)）。
- **终端 UX** — 将用户输入的 `!` shell 命令排除在自动 Shell 分组之外（[#5409](https://github.com/github/copilot-cli/issues/5409)）。

---

## 7. 开发者痛点

- **MCP 生命周期与清理** — 认证重建后遗留的 stdio 进程（[#4392](https://github.com/github/copilot-cli/issues/4392)）、会话关闭后仍存活的 Docker MCP 容器（[#4461](https://github.com/github/copilot-cli/issues/4461)），以及 Linux 上 inotify 限制（`ENOSPC`）导致监视失败并阻塞会话（[#5242](https://github.com/github/copilot-cli/issues/5242)）。
- **MCP 认证摩擦** — 每次会话重复认证（[#5402](https://github.com/github/copilot-cli/issues/5402)）、随机回环 OAuth 端口破坏预注册客户端（[#5391](https://github.com/github/copilot-cli/issues/5391)），以及注册表可达性问题（[#4949](https://github.com/github/copilot-cli/issues/4949)）。
- **Windows 平台稳定性** — 父 `pwsh.exe` 崩溃（[#5404](https://github.com/github/copilot-cli/issues/5404)）、键盘输入回归（[#5171](https://github.com/github/copilot-cli/issues/5171)），以及窗口回调显示 bug（[#5398](https://github.com/github/copilot-cli/issues/5398)）。
- **沙箱安全正确性** — GHE 数据驻留租户上的凭据掩码缺口（[#5388](https://github.com/github/copilot-cli/issues/5388)）；仍是敏感领域。
- **工具输出保真度** — 图像像素未能到达模型（`view`、Playwright 截图）（[#5405](https://github.com/github/copilot-cli/issues/5405)）；内容过滤失败消耗 AI 额度（[#5408](https://github.com/github/copilot-cli/issues/5408)）。

---

*摘要基于截至 2026-11-11 的 github.com/github/copilot-cli 数据编制。*

</details>

<details>
<summary><strong>OpenCode</strong> — <a href="https://github.com/anomalyco/opencode">anomalyco/opencode</a></summary>

# OpenCode 社区简报 — 2026-11-11

## 今日亮点
OpenCode 发布了 v2.2.7，核心亮点是 **GitHub Copilot “Auto” 模型选项**，允许 Copilot 的路由器按会话自动选择模型并享受 Auto 折扣，路由失败时可优雅回退。与此同时，问题追踪器仍被尚未解决的**免费层用量限制问题（#59387，346 条评论）**所主导，另有一批 v2.2.x 回归问题，涉及 Plan 模式强制执行、Windows 控制台窗口闪烁以及插件缓存过期。在 PR 方面，团队正在积极推进后台子代理以及桌面端/CLI 健壮性相关的工作。

---

## 版本发布

**v2.2.7** — [发布页面](https://github.com/anomalyco/opencode/releases)
- **GitHub Copilot Auto 模型选择**（[#62997](https://github.com/anomalyco/opencode/pull/62997)）：当你的 Copilot 账户拥有 Auto 池时，在模型选择器中选择 **Auto** 即可让 Copilot 的路由器按会话自动选择模型并享受 Auto 折扣。若路由失败，OpenCode 会回退到该账户的默认聊天模型。

---

## 热门 Issue

1. **[#59387 [OPEN] 免费层问题 — 请先阅读](https://github.com/anomalyco/opencode/issues/59387)** — 免费层故障（“用量超限”、限制永不重置、桌面版门控）的核心分诊帖。以 **346 条评论**成为流量最高的未关闭 Issue；应将其视为市场推广/计费摩擦的权威信号。
2. **[#60250 [OPEN] v2.2.1 中 Plan 模式强制执行失效](https://github.com/anomalyco/opencode/issues/60250)** — Plan 代理会立即提议实施编辑，而非仅做规划。对于依赖“先规划后执行”纪律的用户而言，这是核心工作流回归。
3. **[#62659 [CLOSED] 所有会话丢失 — 数据目录被重建（2.2.5→2.2.6）](https://github.com/anomalyco/opencode/issues/62659)** — 升级时因 schema 引导从零开始而导致完整对话记录丢失。严重程度极高（永久性数据丢失），尽管现已关闭 — 仍值得在 2.2.7 中验证修复情况。
4. **[#52266 [OPEN] Windows 控制台窗口闪烁（约每秒 1 次）](https://github.com/anomalyco/opencode/issues/52266)** — 子进程启动缺少 `CREATE_NO_WINDOW`，导致每秒弹出 `git.exe` 控制台窗口。这是此前问题的再次报告；属于长期存在的 Windows 平台债务。
5. **[#59461 [OPEN] 启动时崩溃（Error 134 / 缺少 SSE4.2）](https://github.com/anomalyco/opencode/issues/59461)** — 新的 Bun 运行时要求 SSE4.2，导致较旧 CPU 在桌面客户端上崩溃。硬件兼容性回归。
6. **[#49742 [OPEN] CLI v2 中缺少消息时间戳切换](https://github.com/anomalyco/opencode/issues/49742)** — v1 中的 `Ctrl+P` 功能在 v2.0.8 中被移除。代表了 v1→v2 功能对等差距的累积（8 👍）。
7. **[#63188 [OPEN] `session.move` 不更新项目归属](https://github.com/anomalyco/opencode/issues/63188)** — 移动后的会话保留旧的 `projectID`，因此仍归档在错误的项目下。这是 sessions API 中的数据模型正确性 bug。
8. **[#63075 [OPEN] [FEATURE] 拒绝规则应向模型传递消息](https://github.com/anomalyco/opencode/issues/63075)** — 拒绝规则命中时返回固定的 `Permission denied` 字符串，没有原因/替代方案，与 `ask` 流程不同。直接影响代理的自我纠正质量。
9. **[#63174 [OPEN] [已复现] 无效的 `retry-after-ms` 覆盖了有效的 `Retry-After`](https://github.com/anomalyco/opencode/issues/63174)** — 空值/负值的 `retry-after-ms` 会产生 0 ms 延迟而非遵循该头部，导致激进重试。这是影响提供商弹性的网络正确性问题。
10. **[#63171 [CLOSED] 轮次以“成功”结束但内容为零](https://github.com/anomalyco/opencode/issues/63171)** — 回答完全被困在推理通道中却被记录为正常完成。这是静默失败的可观测性缺口。

---

## 关键 PR 进展

1. **[#63210 [OPEN] 始终在后台运行子代理](https://github.com/anomalyco/opencode/pull/63210)** — 移除 `background` 选项；每次子代理调用立即返回 `sessionID`。基于 #63209 叠加。
2. **[#63209 [OPEN] 每次空闲时向父级报告子代理结果](https://github.com/anomalyco/opencode/pull/63209)** — 使后台子代理在一次性任务观察器之外也可观测，从而支持交互式/可输入的子代理。
3. **[#63196 [OPEN] Windows 上的管道模式（进程守护）](https://github.com/anomalyco/opencode/pull/63196)** — 新增带 `CREATE_NO_WINDOW` 的 `create_pipe`，直接解决控制台闪烁类 bug（#52266）。
4. **[#63208 [OPEN] 停止在登录 shell 环境探测期间冻结主进程](https://github.com/anomalyco/opencode/pull/63208)** — 将 `spawnSync` 环境捕获替换为异步方式；修复启动 UI 最多 5 秒的冻结。
5. **[#63213 [OPEN] 将客户端 MCP 连接限定到单个会话](https://github.com/anomalyco/opencode/pull/63213)** — 为客户端 MCP 连接新增可选的 `sessionID` 参数；改善多会话 MCP 使用时的隔离性。
6. **[#63197 [OPEN] 保留 Claude 订阅限制错误](https://github.com/anomalyco/opencode/pull/63197)** — 重新分类来自 Claude Agent 的普通 `Error`，使限制不再被呈现为 `Unknown`。
7. **[#62729 [OPEN] 事件流始终无法打开时 CLI 直接失败而非挂起](https://github.com/anomalyco/opencode/pull/62729)** — 关闭 #62681；修复 `opencode run` / ACP / TUI 重连循环中的无界等待。
8. **[#63152 [OPEN] 通过各自的 prefix 更新 npm 安装](https://github.com/anomalyco/opencode/pull/63152)** — 通过使用安装自身的 npm 而非 PATH 中的 npm，修复 fnm/nvm 下 `opencode upgrade` 的问题。
9. **[#61299 [OPEN] @-提及项目目录之外的文件（TUI）](https://github.com/anomalyco/opencode/pull/61299)** — 将 V1 行为（#16464）移植到 V2；这是 frequently requested 的易用性改进。
10. **[#60767 [OPEN] 处理无消息的流错误](https://github.com/anomalyco/opencode/pull/60767)** — 修复 schema 拒绝缺少 `message` 字段的 OpenAI 兼容错误事件的问题。

---

## 功能请求趋势

- **模型目录透明度与部署控制**：固定/验证确切的模型检查点并部署特定构建（[#41969](https://github.com/anomalyco/opencode/issues/41969)、[#63110](https://github.com/anomalyco/opencode/issues/63110)）。
- **代理上下文与权限控制**：编辑/删除上下文消息（[#7712](https://github.com/anomalyco/opencode/issues/7712)），以及让拒绝规则向模型解释自身（[#63075](https://github.com/anomalyco/opencode/issues/63075)）。
- **桌面端 UX 打磨**：键盘快捷键帮助浮层（[#63190](https://github.com/anomalyco/opencode/issues/63190)）、代理运行时保持唤醒（[#63082](https://github.com/anomalyco/opencode/issues/63082)）。
- **计费灵活性**：Console 额度 / Go 订阅支持 PayPal（[#62639](https://github.com/anomalyco/opencode/issues/62639)）。
- **国际化**：iOS 上的语音识别语言设置（[#56973](https://github.com/anomalyco/opencode/issues/56973)）。

---

## 开发者痛点

- **免费层可靠性与计费清晰度**是呼声最高的信号（#59387），再加上对付费计划透明模型版本/检查点的请求，使问题更加突出。
- **v2.x 迁移摩擦**：缺失的 v1 CLI 功能（时间戳）、Plan 模式强制执行回归，以及灾难性的会话数据丢失升级路径（#62659）削弱了升级信心。
- **Windows 平台质量**：控制台窗口闪烁、fnm/nvm 下的路径过期升级，以及旧 CPU/SSE4.2 崩溃在多个版本中持续存在。
- **静默/模糊失败**：零内容“成功”轮次、`DELETE /auth` 后认证状态过期、MCP/插件缓存未失效，以及重试头部解析 bug，都降低了对代理结果的信任。
- **子代理与 MCP 易用性**：后台执行和按会话 MCP 作用域是团队本周正在处理的活跃前沿。

</details>

<details>
<summary><strong>Pi</strong> — <a href="https://github.com/earendil-works/pi">earendil-works/pi</a></summary>

# Pi 社区文摘 — 2026-11-11

## 1. 今日亮点

Pi 发布了 v1.5.1 和 v1.5.2，新增了独立的 shell 认证命令（`pi auth login/logout`），以及针对工作目录已被移动或删除的会话的 fork 流程。Issue 活动主要集中在提供商集成摩擦上：Anthropic OAuth 版本固定、缺失的 Azure/OpenAI 认证路径，以及过时的模型目录。围绕会话状态、codemode 全局变量和工具搜索排名，落地了一批重要修复。

## 2. 版本发布

**v1.5.2**
- **文件夹已消失的会话可 fork**：原始目录被移动/删除的会话现在可以 fork 到当前文件夹，而不再只能继续，从而消除了反复出现的恢复提示。
  https://github.com/earendil-works/pi/releases/tag/v1.5.2

**v1.5.1**
- **Shell 登录与登出**：`pi auth login <provider>` / `pi auth logout <provider>` 现在无需启动会话即可使用，支持 API key 和无浏览器登录选项。
  https://github.com/earendil-works/pi/releases/tag/v1.5.1

## 3. 热门 Issue

1. **#10956 — `cacheWarming: "idle"` 静默停止**（13 条评论，👍3）。一旦某个扩展处理了 `turn_end` 且上下文包含自定义/压缩消息，空闲缓存预热就再也不会触发——记录了 507 次决策，全部处于 `continuationProbability: 1`。对扩展用户而言是高频的成本/延迟回归。
   https://github.com/earendil-works/pi/issues/10956
2. **#10074 — Anthropic 损坏非 ASCII 编辑参数**（已关闭，12 条评论）。`\uXXXX` 中丢失 `u` 会静默将韩文文本编辑转换为 `\b`/`\f` 控制字符，偶尔损坏文件。这是一个耗时数周才定位的正确性/数据完整性问题。
   https://github.com/earendil-works/pi/issues/10074
3. **#11021 — ChatGPT 登录报告 GPT-6.1 Sol 为 272K，而目录为 500K**（10 条评论，👍8）。Pi 宣称的上下文/压缩预算小于账户实际目录。社区高度认同；直接影响长上下文工作流。
   https://github.com/earendil-works/pi/issues/11021
4. **#7062 — openai-completions 数组内容 / 缺失 finish_reason**（9 条评论，👍3）。非标准的 Databricks 流式响应产生 `[object Object]` 内容。长期存在的提供商兼容性缺口。
   https://github.com/earendil-works/pi/issues/7062
5. **#10812 — 过时的默认模型静默回退**（7 条评论）。`settings.json` 中被重命名的模型会导致静默切换到另一个模型且无任何警告——令人意外且难以调试。
   https://github.com/earendil-works/pi/issues/10812
6. **#11112 — 让 `prepareArguments` 修复无法解析的工具参数**（已关闭，6 条评论）。Sonnet 5 的笔误如 `"oldText">` 会塌缩为 `arguments: {}`；提议的修复钩子可以恢复这些轮次。
   https://github.com/earendil-works/pi/issues/11112
7. **#10536 — 虚拟模型的缓存预热停止**（4 条评论，👍1）。`registerVirtualModel` 会话报告预热器未激活，提示"对话上下文已更改"——与 #10956 同一根因家族。
   https://github.com/earendil-works/pi/issues/10536
8. **#10539 — Cloudflare AI Gateway 目录跳过 DeepSeek/xAI/Qwen**（3 条评论）。`/model` 仅列出 `openai/*`、`anthropic/*`、`workers-ai/*`，尽管网关支持和 models.dev 覆盖均已具备。
   https://github.com/earendil-works/pi/issues/10539
9. **#11989 — Anthropic OAuth 固定 `claude-cli/2.1.280`，被拒绝（需要 2.1.290）**（已关闭，3 条评论）。由于硬编码的 CLI 版本字符串，订阅 OAuth 在较新模型上被阻止。
   https://github.com/earendil-works/pi/issues/11989
10. **#11956 — 文件打开期间取消读取导致 P0 崩溃**（2 条评论）。读取在 stat 中途被取消时，未捕获的 `AbortError` 会终止 Pi；通过 codemode `Promise.all` 暴露。
    https://github.com/earendil-works/pi/issues/11956

## 4. 关键 PR 进展

1. **#12016 — Azure OpenAI 的 Microsoft Entra ID 认证**（已关闭）。新增 `DefaultAzureCredential` 支持，使 Foundry 无需 API key 即可使用——填补了一个重要的企业级缺口。
   https://github.com/earendil-works/pi/pull/12016
2. **#11975 — 会话 cwd 不存在时提供 fork 选项**（已关闭）。支撑 v1.5.2 的头号特性；此前"继续"选项仅存在于内存中。
   https://github.com/earendil-works/pi/pull/11975
3. **#11961 — Harness.close() 释放提供商资源**（已关闭）。修复了 Codex 响应后泄漏的 WebSocket/定时器导致 Node 进程无法退出的问题。
   https://github.com/earendil-works/pi/pull/11961
4. **#11986 — 工具搜索为读动词查询排名只读工具**（已关闭）。修复了 `gmail_read_email` 因名称不在搜索文档中而无法匹配"read my email"的问题。
   https://github.com/earendil-works/pi/pull/11986
5. **#11985 — Codemode 包装器不再遮蔽 prelude 全局变量**（已关闭）。移除了遮蔽 `globalThis` 定义的冗余 `tools`/`console` 参数。
   https://github.com/earendil-works/pi/pull/11985
6. **#11978 — 展开自定义 `shellPath` 中的环境变量**（已关闭）。修复了 Windows 上 `%LOCALAPPDATA%` 风格路径失败的问题。
   https://github.com/earendil-works/pi/pull/11978
7. **#11998 — ant-ling 中的 Ling 3.x Flash 模型**（已关闭）。用 Ling 3.0–3.2 Flash 替换已弃用的 Ling/Ring 2.6；3.2 新增图像输入。
   https://github.com/earendil-works/pi/pull/11998
8. **#12002 / #12011 — 会话目录、分享、子代理迁移至 Chord 存储**（已关闭）。使导出/导入/删除包含会话列表，并启用基于 Chord outbox 的同步。
   https://github.com/earendil-works/pi/pull/12002 · https://github.com/earendil-works/pi/pull/12011
9. **#11990 — Radius 会话同步适配器原型**（开放中）。引入后端边界用于会话备份/恢复，而不耦合到 Radius HTTP API。
   https://github.com/earendil-works/pi/pull/11990
10. **#12001 / #12005 — llama.cpp 默认端口 + 简化安装指南**（开放中）。统一使用端口 `9931` 和 `llama serve` 作为统一入口。
    https://github.com/earendil-works/pi/pull/12001 · https://github.com/earendil-works/pi/pull/12005

## 5. 热门讨论

**想法**
- **#11984 — 为 Pi 状态管理引入 Redux/Redux-Saga**。提出基于效应的代理编排模型；作者引用了一个 Redux-Saga 讨论帖，但承认自己没有实现手段。处于早期阶段，值得关注其架构辩论。
  https://github.com/earendil-works/pi/discussions/11984

## 6. 功能请求趋势

- **提供商认证广度**：Entra ID/Azure（#12016）、Kimi OAuth 主机迁移（#9819）和 ChatGPT 登录上下文一致性（#11021）均指向对一等公民、非 API key 认证路径的需求。
- **目录保真度**：多份报告（#10539、#10683、#10812、#11021）指出模型目录与提供商/账户实际提供的内容存在偏差——用户希望获得实时目录，并在配置过时时大声报错。
- **扩展易用性**：通过 `main`/`exports` 的 npm 解析（#9817）、独立构建中的 Shiki 解析（#12012）以及 `StringEnum` 类型回归（#11869）表明扩展面需要加固。
- **缓存预热可靠性**：#10956 和 #10536 是同一类 bug，分别涉及自定义消息和虚拟模型；预计会出现合并修复请求。
- **信任/安全默认值**：#12023 主张 `.pi/agents/` 应要求信任，且子代理审批门控不应可被模型禁用——这一政策讨论可能会扩大。

## 7. 开发者痛点

- **静默降级**：缓存预热停止、默认模型回退和过时目录都会静默失败，迫使用户深挖日志（`/session`、`cache_warming_decision` 事件）。
- **提供商兼容脆弱性**：非标准流式形状（Databricks）、头部/版本固定（Anthropic OAuth、Fable 5.2）以及 PII 脱敏边缘情况（#11928）消耗了大量分诊精力。
- **参数解析韧性**：损坏的 Unicode、孤立代理项（#12014）和模型产生的畸形 JSON 反复破坏工具调用——用户希望有修复层而非重启会话。
- **进程生命周期泄漏**：#11676/#11961 凸显 harness 关闭时的资源清理不完整，影响嵌入和持久化工作流。
- **独立二进制依赖解析**：依赖未随二进制打包的包的扩展（Shiki）在运行时失败，这是一个反复出现的打包投诉（#12012）。
- **小终端 UX**：窄屏/移动终端上的页脚截断（#12022）仍是一个未解决的生活质量问题。

</details>

<details>
<summary><strong>Qwen Code</strong> — <a href="https://github.com/QwenLM/qwen-code">QwenLM/qwen-code</a></summary>

# Qwen Code 社区简报 — 2026-11-11

## 1. 今日亮点

项目发布了 **v0.27.2**，同时发布 **v0.27.2-preview.0** 和 **SDK TypeScript v0.1.32**（捆绑 CLI 0.27.2）。Web Shell 新增了面向微信、QQ 和邮件的多渠道配置（扫码登录、身份展示、登出）。与此同时，安全与权限分类缺陷占据了 issue 流量主流：多个 P1 报告显示 AUTO 模式分类器批准了破坏性命令（`git branch -D`）并误读了重定向，相应的修复 PR 已开启。

---

## 2. 发布

**v0.27.2** — 稳定版发布（发布说明待生成）。

**v0.27.2-preview.0** — 预览构建，包含：
- `fix(ci)`：在 base tip 读取 prettier 的完整忽略集（[#16729](https://github.com/QwenLM/qwen-code/pull/16729)）
- `fix(cli,core)`：允许 Agent Host 重试注册

**sdk-typescript-v0.1.32** — 捆绑 CLI 0.27.2（从与 SDK 相同的分支/ref 构建）。

**Web Shell 渠道（发布说明重点）** — 微信、QQ 和邮件渠道配置，包括扫码登录、扫描进度、身份展示，以及平台支持时的登出功能（[#16736](https://github.com/QwenLM/qwen-code/pull/16736)、[#16724](https://github.com/QwenLM/qwen-code/pull/16724)）。

---

## 3. 热门 Issue

1. **[#14724](https://github.com/QwenLM/qwen-code/issues/14724)** — *主 CI 失败：Managed Agent gate — 未识别到测试*（292 条评论）。一个长期运行的 CI 跟踪 issue，失败持续累积；评论量之大表明 managed-agent gate 存在持续不稳定，而非一次性故障。

2. **[#16672](https://github.com/QwenLM/qwen-code/issues/16672)** — *Managed Agent gate：MySQL 8.4 上 HostedExtensionsIT 失败及另外 11 项*（55 条评论）。托管扩展测试在 harness 替换/取消场景下失败；影响范围波及 Java SDK 全套测试。

3. **[#8662](https://github.com/QwenLM/qwen-code/issues/8662)** — *将 TUI 渲染从 ink 迁移至 OpenTUI（跟踪）*（36 条评论）。终端 UX 路线图的重要事项；检查点记录显示实现批次已合并，当前 gate 处于安装/平台验收阶段。

4. **[#16787](https://github.com/QwenLM/qwen-code/issues/16787)** — *E2E：tui-agent-loop-parity — 批准写入后又拒绝*（34 条评论）。交互式审批流程中的 TUI/agent-loop 一致性回归——直接影响用户可见行为。

5. **[#16425](https://github.com/QwenLM/qwen-code/issues/16425)** — *P1：Workflow agent 权限审批从不渲染；运行挂起*（7 条评论）。在 0.26.6 和 0.26.8 上复现；状态栏显示待处理徽标但审批 UI 不出现，导致多 agent 工作流死锁。

6. **[#16856](https://github.com/QwenLM/qwen-code/issues/16856)** — *P1 安全：命令中输入重定向对只读分类器隐藏了字词*（2 条评论）。示例：`git branch < /dev/null -D main` 执行了 `-D main`，而分类器只看到前缀——`evaluateCommandSafety` 中存在真实的安全漏洞。

7. **[#16496](https://github.com/QwenLM/qwen-code/issues/16496)** — *AUTO 模式分类器被告知待处理调用已执行*（5 条评论）。转录将正在被分类的调用回显为 `Prior action`，扭曲了审批决策。

8. **[#15660](https://github.com/QwenLM/qwen-code/issues/15660)** — *Subagent 以来源不明的巨大上下文（~105k）启动*（6 条评论）。父 agent 为 42k 而 subagent 为 105k——token 计量与上下文继承需要调查。

9. **[#15327](https://github.com/QwenLM/qwen-code/issues/15327)** — *P1 跟踪：在 `experimental.unifiedAgentLoop` 后让所有界面运行于单一 agent loop*（4 条评论）。跨 CLI、Web Shell 和 daemon 的 agent loop 架构整合——对 #16787 这类一致性修复具有高杠杆作用。

10. **[#16824](https://github.com/QwenLM/qwen-code/issues/16824)** — *干净重装后模型反复拼错项目名（"Lulumi" vs "Luminum"）*（3 条评论）。持续的长上下文/模型质量投诉；标记为 `model/long-context`，暗示缓存或上下文损坏的角度。

---

## 4. 关键 PR 进展

1. **[#16857](https://github.com/QwenLM/qwen-code/pull/16857)** — *fix(permissions)：将待处理调用排除在分类器的先前动作之外。* 将调用 id 贯穿调度器和 ACP 会话，使转录构建器跳过进行中的 `functionCall`。直接修复 #16496。

2. **[#16862](https://github.com/QwenLM/qwen-code/pull/16862)** — *fix(ci)：发布 Docker 测试套件在瞬时失败时重试一次。* 添加单次重试并清理容器，范围限定于 job 所有者；针对发布流水线的抖动。

3. **[#16574](https://github.com/QwenLM/qwen-code/pull/16574)** — *fix(cli)：会话内 MCP 启用解除 glob 排除；限制重复 WS 拒绝日志的速率。* `qwen serve` 的两项运维修复。

4. **[#9417](https://github.com/QwenLM/qwen-code/pull/9417)** — *fix(cli)：worktree guard 中对 heredoc 正文展开采取失败关闭。* 未加引号的 heredoc 分隔符让 bash 展开了被 guard 视为惰性而剥离的正文——一项安全相关的加固。

5. **[#16664](https://github.com/QwenLM/qwen-code/pull/16664)** — *fix(acp)：输出恢复导致答案被截断时报告 `max_tokens`。* 将截断作为停止原因暴露，而非静默结束该轮。

6. **[#16607](https://github.com/QwenLM/qwen-code/pull/16607)** — *fix(cli)：将 provider 警告 URL 清理限定于 URL authority。* 以 `sanitizeProviderBaseUrl` 替换定制的 `indexOf` 启发式，堵住 `/status` 中的凭证泄露边缘情况。

7. **[#16669](https://github.com/QwenLM/qwen-code/pull/16669)** — *fix(cli)：在 Windows Terminal 1.23+ 上启用同步输出。* DECSET 2026 检测涵盖原生 Windows 和 WSL2 构建号路径——减少 TUI 闪烁。

8. **[#16055](https://github.com/QwenLM/qwen-code/pull/16055)** — *fix(cli)：静态转录文本的显式换行宽度。* 修复 #14075 报告的词中断裂/孤立字符 bug。

9. **[#16845](https://github.com/QwenLM/qwen-code/pull/16845)** — *fix(web-shell)：接纳 GitLab 为仅仓库渠道。* 将渠道模型推广至 GitHub 之外；隐藏惰性 DM 控件并接受嵌套允许列表。

10. **[#16811](https://github.com/QwenLM/qwen-code/pull/16811)** — *test(generated-ui)：#14019 的验收评估。* 通过 `qwen serve`、Web Shell、捆绑的 Generated UI provider 和 Chromium 进行端到端运行——仅替换模型。

---

## 5. 热门讨论

本数据集未提供讨论数据；本节省略。

---

## 6. 功能请求趋势

- **渠道与集成扩展** — 通过 Channel 投递的出站平台 webhook（钉钉自定义机器人），配合 Web Shell 配置与计划任务流程（[#16751](https://github.com/QwenLM/qwen-code/issues/16751)）；GitLab 渠道支持通过 PR 落地。
- **Web Shell UX 成熟化** — 自定义 provider 的模型发现，支持刷新并选择（[#16785](https://github.com/QwenLM/qwen-code/issues/16785)），专门的居中会话搜索对话框（[#16778](https://github.com/QwenLM/qwen-code/issues/16778)），以及具备原生组件和隔离 HTML 可视化的有状态"智能 UI"（[#14019](https://github.com/QwenLM/qwen-code/issues/14019)）。
- **跨界面统一 agent loop** — 将 CLI、Web Shell 和 daemon 整合到 `packages/core` 中的单一 loop（[#15327](https://github.com/QwenLM/qwen-code/issues/15327)）。
- **以真实 shell 解析替代手写模型** — 以 tree-sitter-bash / mvdan/sh 加 AST 归属替换 `shell-semantics.ts`（[#16604](https://github.com/QwenLM/qwen-code/issues/16604)）。
- **规模化性能** — 为 GitHub 渠道提供批量 GraphQL CI 复检 leg（[#16692](https://github.com/QwenLM/qwen-code/issues/16692)）。
- **终端 UX 迁移** — OpenTUI 渲染层跟踪（[#8662](https://github.com/QwenLM/qwen-code/issues/8662)）。

---

## 7. 开发者痛点

- **权限分类器的正确性是首要关切。** 三份独立报告（[#16856](https://github.com/QwenLM/qwen-code/issues/16856)、[#16496](https://github.com/QwenLM/qwen-code/issues/16496)、[#16497](https://github.com/QwenLM/qwen-code/issues/16497)）显示 AUTO 模式要么看到错误的命令文本，要么根据模糊目标推断并批准破坏性操作（"清理我的分支" → `git branch -D`）。手写的 shell 模型被反复修补又反复绕过——推动真实解析器（#16604）反映了评审者的疲劳。
- **Workflow/多 agent 挂起。** 审批提示从不渲染（[#16425](https://github.com/QwenLM/qwen-code/issues/16425)），以及 ACP 模式下队友消息从不送达 leader（[#15738](https://github.com/QwenLM/qwen-code/issues/15738)），使多 agent 实验不可靠。
- **CI 噪音与抖动。** 机器人生成的 CI issue 主导了数量（#14724 有 292 条评论，#16672 有 55 条，#16787 有 34 条），其中多项标记为 *Provisional*——报告提交前主分支已越过 head。这侵蚀了贡献者的信号。
- **会话状态锁。** `/cd` 和 `/branch` 即使在空闲时也因"response or tool call in progress"失败（[#12224](https://github.com/QwenLM/qwen-code/issues/12224)、[#16700](https://github.com/QwenLM/qwen-code/issues/16700)）——一类反复出现的陈旧进行中状态 bug。
- **上下文/token 计量不透明。** Subagent 膨胀至约 105k token（[#15660](https://github.com/QwenLM/qwen-code/issues/15660)），以及重装后模型质量漂移（[#16824](https://github.com/QwenLM/qwen-code/issues/16824)）。
- **渲染边缘情况。** 零高度视口使虚拟化转录空白（[#16707](https://github.com/QwenLM/qwen-code/pull/16707)）、静态模式换行孤立字符（[#14075](https://github.com/QwenLM/qwen-code/issues/14075)），以及 Windows Terminal 同步输出缺口（[#16669](https://github.com/QwenLM/qwen-code/pull/16669)）表明长尾 TUI 打磨工作仍然可观。

</details>

---
*本日报由 [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar) 自动生成。*