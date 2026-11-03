# AI CLI 工具社区动态日报 2026-11-03

> 生成时间: 2026-11-03 10:36 UTC | 覆盖工具: 7 个

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

# AI CLI 工具 — 跨工具对比报告
**数据日期：2026-11-03 | 来源：八份社区摘要**

---

## 1. 生态概览

指数中的所有八个工具都处于两股力量的持续拉扯之中：**日益成熟的智能体编排能力**（子智能体、后台任务、MCP、ACP 集成）与**基础可靠性债务**（会话持久性、上下文核算、平台特定启动失败）。没有任何一个工具报告了干净利落的发布周期而不同时伴随可靠性投诉；本窗口内的每一次发布——包括四个 Copilot CLI 构建、Claude Code v2.1.317、Gemini CLI nightly、Qwen Code v0.26.6 和 Pi v1.5.0——都以*打磨边缘*的工作为主，而非 headline 级能力发布。竞争前沿已从原始模型能力转向**治理、可观测性和跨界面一致性**：组织级产物策略、智能体生命周期韧性，以及缩小交互式 CLI 与程序化（ACP/SDK/headless）界面之间的差距。与此同时，**提供商集成核算**（token 预算、模型 ID 规范化、配额语义）在至少五个工具中浮现为定义信任的问题类别——用户现在以*静默成本回退*而非功能来衡量集成质量。

---

## 2. 活跃度对比

| 工具 | Issue（值得注意 / 最高互动） | PR（窗口内） | Discussions | 发布状态 |
|---|---|---|---|---|
| **Claude Code** | 10 个热门项；多为 CLOSED/陈旧；队列集中于智能体生命周期 + 组织治理 | 2（1 CLOSED，1 陈旧 OPEN） | 不适用（未提供） | ✅ v2.1.317 已发布 |
| **OpenAI Codex** | 10 个热门项；大量 Windows/附件集群 | 10+ 实质性（bot 撰写、云客户端、MCP/Guardian） | ✅ 4 Ideas，3 Q&A，5 Show-and-tell | ✅ rust-v0.167.0-alpha.4（alpha） |
| **Gemini CLI** | 10 热门 + 3 显著；智能体可靠性占主导 | 10+（健壮性、ACP、平台修复） | 不适用（未提供） | ✅ v0.67.0-nightly |
| **GitHub Copilot CLI** | 10 热门（键盘/MCP/ACP/Win） | 1（agent-monitor TUI） | 不适用（未提供） | ✅ 4 个构建（v1.0.106-3 → -6） |
| **OpenCode** | 10 热门；免费层 + 桌面稳定性占主导 | 10（压缩、SSE、shell、i18n） | 不适用（未提供） | ❌ 今日无发布 |
| **Pi** | 10 热门；RPC/schema/上下文预算 | 10（schema、MCP、更新、grep OOM） | ✅ 1 Ideas（👍47），1 Q&A，1 Show-and-tell | ✅ v1.5.0 |
| **Qwen Code** | 10 热门；CI 噪声 + 会话持久性 | 10（core/serve/managed-agent） | 不适用（未提供） | ✅ v0.26.6 + SDK v0.1.27 |

**数据说明：** Codex 的 GitHub Issues/PR 为*活跃*（非不适用）——该工具同时使用代码跟踪器和 Discussions。Claude Code、Gemini CLI、Copilot CLI、OpenCode 和 Qwen Code 在上游启用了 Discussions，但摘要未呈现其数据——标记为不适用而非“不活跃”。OpenCode 是窗口内唯一**无发布**的仓库，却拥有所有工具中最高的单帖互动量（#59387，230 条评论）。

**互动排名（最高单帖）：**
1. OpenCode — 230 条评论（免费层 mega-thread）
2. Qwen Code — 161 条评论（CI 门禁失败，bot）
3. Codex — 69 条评论（Windows 项目消失）
4. Claude Code — 13 条评论（Windows 压缩，已关闭）
5. Pi — 21 条评论（终端颜色查询回归）

> 注：互动*深度*与*广度*不同——OpenCode/Qwen 帖子为单一 issue 集中 / bot 噪声；Codex/Claude/Gemini 展现出最广泛的 issue 标题多样性。

---

## 3. 共享功能方向

出现在**≥3 个工具社区**中的需求——这些是行业真正的路线图：

| 方向 | 发出信号的工具 | 具体需求 |
|---|---|---|
| **智能体生命周期韧性** | Claude Code、Gemini CLI、Pi、OpenCode | 可恢复失败时暂停/恢复子智能体（CC #78224）；MAX_TURNS 被误报为成功（Gem CLI #22323）；RPC 重复 `session_start` / 关闭挂起（Pi #10470、#11599）；陈旧后台 `opencode-cli` 服务（OC #50633） |
| **上下文 / 压缩正确性** | Claude Code、OpenCode、Pi、Qwen Code、Codex | 空闲压缩与智能体竞争（CC #106402）；逐交换截断浪费（OC #61371）；压缩上限反转（OC #58492）；与 OpenRouter 的 token 预算不匹配（Pi #10497）；缺少 Anthropic 传输超时（QC #16193）；陈旧会话状态导致“容量”错误（Codex #59059、PR #59111） |
| **配置一致性遵从** | Gemini CLI、OpenCode、CC、Copilot、Codex | `settings.json` 被 Browser Agent 忽略（Gem #22267）；`shell` 设置被忽略（OC #51051）；`CLAUDE_CONFIG_DIR` 被违反（CC #98789）；ACP 设置缺口（Copilot）；模型目录刷新陈旧（Codex #59111） |
| **MCP 健壮性与权限粒度** | Codex、Copilot、Pi、Gemini | `tools/call` 时 OAuth 头被丢弃（Copilot #3015）；按运行禁用 MCP（Copilot #5336）；定制化 `doctor` 修复（Codex #59057）；子进程清理（Pi #11724）；>128 工具上限（Gem #24246）；沙箱边界设计（Codex Disc #58905） |
| **ACP / 程序化界面一致性** | Copilot、Pi、Qwen Code | ACP 设置/子智能体一致性（Copilot，今日大量关闭）；**44 票**支持采用 ACP 而非自定义 RPC（Pi #4444）；统一智能体循环 + ACP cron 轮次（QC #16190/#16192） |
| **可观测性 / 状态信号** | Copilot、Codex、Pi、Gemini | OSC 7501 终端状态（Copilot #5112）；实时类型化转录事件（Codex #59066/#59047）；UI 中子智能体工具可见性（Gem #30094）；扩展技能检查（Pi #11700） |
| **Token 效率 / 精确上下文** | Qwen Code、Gemini、Pi、OpenCode | 切片级文件/上下文引用（QC #16173）；AST 感知读取/搜索 EPIC（Gem #22745–#22747）；“Tactful Extraction”（Gem #19561）；逐轮漂移检查器（OC #58459） |
| **跨会话记忆 / 持久性** | Codex、OpenCode、Pi、Gemini | 决策图（Codex Disc #27754/#55901）；FTS5 转录搜索（OC #54863）；子会话可见性 / 持久重启（Pi #9847/#11635）；基于文件的任务状态（Gem #18836） |
| **Windows 与平台一致性** | Codex、CC、Copilot、Pi、Gemini | 项目消失（#42739）、MSIX 更新循环（#38843）、CRLF 损坏（Copilot #1148）、mintty/ConPTY 转义泄漏（Pi #10256）、WSL2 保活（Copilot #5304）、Wayland 浏览器失败（Gem #21983） |
| **误报安全过滤器** | Copilot、Codex、Gemini | 422 网络安全风险标记（Copilot #5335）；Guardian 拒绝并可选复议（Codex #59107）；破坏性 git 劝阻（Gem #22672） |

**普世主题：** 每个社区都在用不同措辞要求*同一件事*——**智能体循环在中断下必须可信，配置必须言出必行。**

---

## 4. 差异化分析

| 轴 | Claude Code | Codex | Gemini CLI | Copilot CLI | OpenCode | Pi | Qwen Code |
|---|---|---|---|---|---|---|---|
| **主要目标** | 企业团队、受治理组织 | 跨界面（Desktop+CLI+Work）、Office 中心用户 | 开发者自动化、智能体研究 | GitHub 原生开发者、ACP 客户端集成者 | 免费层 + 高级用户、SDK/bot 构建者 | 嵌入者、headless/RPC 集成者 | 渠道/Web Shell + managed-agent 运营者 |
| **功能焦点** | 治理（组织产物共享）、hook/权限一致性、`/schedule` 云任务 | 云客户端可观测性（类型化事件、分页历史）、Guardian 安全、MCP doctor | 智能体编排正确性、AST 感知上下文、沙箱策略 | 统一 `/settings` 仪表板、ACP 一致性、键盘 UX、MCP 认证 | 压缩算法、提供商边缘案例、桌面生命周期 | 扩展变更日志、RPC 生命周期、schema 解析 | Web Shell 侧边栏、managed-agent 共享、CI 健康 |
| **技术方法** | 基于 hook 的安全模型（扩展至 Bash）、基于 hook 的 SDK 控制 | Rust 客户端、gRPC 云历史、策略感知搜索提供商 | 维护者撰写的战略方向 issue（沙箱、AST）、路线图上的统一智能体循环 | 直接发布（无 PR 队列）、OSC 终端集成 | 提供商无关 harness、SSE 级修复、SQLite FTS5 | 自定义 RPC（推动 ACP）、bot 撰写 PR 流水线 | `QWEN_CODE_ENABLE_UNIFIED_AGENT_LOOP` 后的统一智能体循环推出 |
| **成熟度姿态** | 分诊疲劳 / 陈旧关闭严重（安全报告被陈旧关闭） | 高速 alpha、bot 生成 PR 爆发 | 夜间迭代、战略 EPIC | 通过发布交付、公开 PR 面低 | 无发布；桌面严重不稳定 | 小团队、高信号 PR、快速修复合并 | bot 重度 CI 噪声淹没真实 PR |
| **独特脆弱性** | Hook 覆盖盲点（Bash 绕过）；可见配置蔓延 | “容量”错误伪装为会话状态；Windows 桌面 | 误导性 `Termination Reason: GOAL` 成功掩盖 | Windows 行尾损坏；ACP 二等滞后 | 免费层配额核算可信度 | 上下文预算静默回退；更新信任 | CI token 速率限制自我中断；会话数据丢失 |

**关键差异化洞察：**
- **安全模型哲学分化：** Claude Code 以 hook 注册表为中心（并与其自身盲点作战）；Codex *面向 Guardian 安全*（拒绝复议、预备所有者权限）；Gemini 倾向*战略性*（维护者撰写的沙箱 + 意图路由）。
- **协议姿态：** Pi 公开推动 **ACP 标准化**，而 Copilot 正在*集成* ACP 但在交互一致性上滞后，Qwen 正在*统一其智能体循环*朝向 ACP 轮次——明确汇聚于 ACP 作为编辑器↔智能体传输，而 Claude Code 显著缺席此对话。
- **成功报告中的信任现已成为差异化因素：** Gemini 的 #22323（MAX_TURNS 时 GOAL）和 #21983（平台失败时 GOAL）是整个集合中对自动化用户最危险的单一类 bug——且只有 Gemini CLI 如此大声地暴露它。

---

## 5. 社区动能与成熟度

**最高原始互动 / 最大社区（按 issue 评论量）：**
1. **OpenCode** — 230 评论 mega-thread；最高原始互动，但集中于*支持积压*而非技术深度。也是今日唯一无发布的仓库——信号表明 issue 量正超过发布节奏。
2. **Qwen Code** — 161 评论 bot 提交 CI 门禁；第二高原始互动但*低信号*（bot 噪声膨胀）。
3. **Codex** — 69 评论 Windows issue；*最高质量广泛互动*，拥有最多样化的 issue/PR/discussion 组合。

**最快迭代工程节奏：**
- **Copilot CLI** — 4 构建 / 24 小时，直接发布（几乎无 PR 面）。最高速度，最低社区对变更的可见性。
- **OpenAI Codex** — 单窗口 10+ 实质性 PR（云客户端类型化事件、MCP/搜索发现、Guardian 改进）。相对于窗口大小的**最活跃工程流水线**。
- **Pi** — 小而*精准*：10 个 PR 各自关闭一个特定、范围明确的缺陷（grep OOM、SSE 重试监听器、schema `$id`）。最高每 PR 修复质量比。

**成熟度指标（正面）：**
- **Codex、Copilot、Gemini** 展现*积压塑造*成熟度——功能在数天内从讨论 → issue → PR（MCP doctor、Guardian 复议）。
- **Claude Code** 展现*流程成熟但分诊风险*：清理浪潮关闭了几乎所有顶级帖子，包括两个安全相关报告（#90658、#89716）标记为 needs-repro/陈旧。这是社区信任警告信号，而非仅仅是家务整理。

**成熟度指标（负面 / 债务）：**
- **OpenCode** — 免费层可信度危机（配额重置时间失败、桌面对免费用户无法启动）是支持积压问题，对依赖免费层采用的模型具有战略后果。
- **Qwen Code** — 自我造成的 CI 中断（#16188：bot 爆发耗尽 GITHUB_TOKEN，403 无关 PR）意味着真实回归被基础设施噪声淹没。
- **Pi** — 发布流水线 bug：v1.2.0–v1.4.0 *无构建发布*尽管有标签发布（#11686）。这是打包信任缺口。

**近似社区活跃度排名（综合：互动 × 多样性 × 时效）：**
> Codex ≈ Gemini ≡ Claude Code > Copilot > OpenCode（数量）≈ Pi（质量）> Qwen（噪声通缩）

---

## 6. 趋势信号

**1. 可靠性是新功能。**
每个工具的痛点部分都由可靠性而非能力缺口主导。子智能体生命周期、会话持久性和配置遵从性已取代“添加更多能力”成为首要用户需求。**对开发者/构建者的参考：** 如果你在发布智能体 harness，可靠性仪表化（类型化事件、重试安全工具调用、中断韧性状态）现已是入场门槛和你的主要差异化因素。

**2. 静默失败是最具破坏性的失败模式。**
*跨工具*反复出现：丢弃的 OAuth 头静默失败（Copilot #3015）、压缩停滞无反馈（OC #61346）、配额重置时间永不到达（OC #59387），以及——最具后果的——**`Termination Reason: GOAL` 掩盖轮次限制命中**（Gem #22323）。对自动化/评估用户，*响亮失败优于虚假成功*。发布显式 `truncated`/`failed` 状态的工具将赢得信任。

**3. 提供商集成核算是信任战场。**
Token 过计 / 上下文不足 / 模型 ID 规范化共同产生 **400s、过早压缩和成本回退**（Pi #10497/#11021、Qwen #16157–#16159、Codex #59059、OpenCode #61072 约 1.8× 配额消耗）。**参考：** 这是*正确性*问题，非成本优化问题——用户以此衡量集成质量。

**4. “界面一致性税”是普世的。**
每个成熟工具现都支付税款以保持交互式 CLI、headless/SDK 和程序化界面（ACP、RPC、HTTP）同步。ACP 正浮现为汇聚协议（Pi 👍47、Copilot ACP 集群、Qwen 统一循环），而 Claude Code 和 Gemini 未明显参与——潜在互操作性断层线。

**5. 压缩是所有智能体 harness 中最脆弱的单一子系统。**
五个工具今日暴露压缩 bug。这是*上下文核算 × 会话状态 × 提供商行为*碰撞之处——也是临时 hack（`flattenAndDropOldest`、保活超时、上限反转）反复出现之处。**参考：** 将压缩视为一等、独立测试的子系统，而非内部便利功能。

**6. Windows 和平台原生摩擦是慢性债务的碎片。**
跨五个工具，Windows 特定桌面/更新/输入 bug 仍为 #1 或 #2 投诉。WSL2、mintty、ConPTY 和 macOS XLSX/PPTX 附件挂起显示*桌面界面回归集群*在整个生态中反复出现。若你瞄准企业 Windows 用户，这是你最高杠杆的重工区域。

**7. 治理正开始差异化企业采用。**
仅 Claude Code 浮现组织级产物共享（#78537）和 hook/权限一致性作为**治理而非便利**关切。预期此轴将扩大——治理原语最先成熟的工具将捕获受监管企业推广。

**8. 免费层和速率限制语义值得审视。**
OpenCode 的 230 评论帖子 + Copilot 消失的配额指示器（#57656，👍20）+ Codex “容量”错误都反映用户较少关心限制*是什么*，而更关心是否*诚实报告并如承诺重置*。速率/使用 UX 透明度是信任功能。

**9. 发布节奏 ≠ 用户可见成熟度。**
Copilot 每天发布四个构建但几乎无公开 PR 轨迹（直接发布）。Codex 每窗口 10+ PR 带 alpha 标签。Gemini 夜间激进。**参考：** 为评估这些工具作为依赖，看*issue 关闭质量和安全报告处理*，而非发布频率——Claude Code 对安全报告的陈旧关闭应缓和原始构建数比较。

---

### 技术决策者推荐阅读
- **最具可操作性的工程修复：** Pi PRs #11705、#11712、#11726；Codex PRs #59057、#59107、#59111；Gemini PR #30020；OpenCode PRs #61376、#58492。
- **最有价值的 postmortem（并行智能体用户）：** Claude Code #89102（worktree 锁存破坏 Bash）和 #90782（bootstrap 上下文污染）。
- **最具战略方向设定：** Gemini #19873（沙箱 + 意图路由）和 #22745 EPIC（AST 感知上下文）；Pi Disc #4444（ACP 标准）。
- **观察列表（信任风险）：** Claude Code 陈旧关闭的安全报告；Qwen Code CI token 耗尽；OpenCode 免费层核算；Codex 容量错误集群。

*报告综合自八份日期为 2026-11-03 的仓库摘要。Issue/PR 标签和投票数按报告引用；未执行外部验证。*

---

## 各工具详细报告

<details>
<summary><strong>Claude Code</strong> — <a href="https://github.com/anthropics/claude-code">anthropics/claude-code</a></summary>

## Claude Code Skills 社区热点

> 数据来源: [anthropics/skills](https://github.com/anthropics/skills)

# Claude Code Skills 社区亮点报告
*数据快照：2026-11-03 | 来源：github.com/anthropics/skills*

---

## 1. 热门 Skills 排名（按讨论度与关注度）

> 注：本快照中所有 PR 的 `Comments` 均显示为 `undefined`，因此排名综合了关联 Issue 的讨论量、更新时效性以及交叉引用的 bug 活动。

| # | Skill / PR | 功能 | 讨论亮点 | 状态 |
|---|-----------|------|---------|------|
| 1 | **docx**（多个：#1792、#1734、#2018） | Word 文档操作——接受修订、孤立批注检测、run 合并 | 仓库中修复最密集的集群。`merge_runs.py` 写出**无效 XML**（未绑定命名空间前缀）却报告成功（[#2018](https://github.com/anthropics/skills/issues/2018)）；LibreOffice 超时被误报为成功（[#1792](https://github.com/anthropics/skills/pull/1792)） | 全部 OPEN |
| 2 | **skill-creator**（#1298、#1681、#1997） | 用于编写/校验/优化 Skills 的元技能 | 三个独立的正确性 bug：触发评估假阴性 + Windows 下失效（[#1298](https://github.com/anthropics/skills/pull/1298)）、独立执行损坏（[#1681](https://github.com/anthropics/skills/pull/1681)）、YAML frontmatter 标量解析破坏描述字段（[#1997](https://github.com/anthropics/skills/pull/1997)）。另在 [Issue #202](https://github.com/anthropics/skills/issues/202) 中被指出不可操作、文风偏开发文档 | 全部 OPEN |
| 3 | **mcp-builder**（#1742，+ #1390） | 搭建带连接/评估 harness 的 MCP 服务器 | 两处系统性故障：`mcp>=2.0.0` 重命名了 `streamablehttp_client` 并更改了 header 配置（[#1742](https://github.com/anthropics/skills/pull/1742)）；评估 harness 伪造工具错误，**对每个真实 MCP 服务器均得 0/N 分**（[#1390](https://github.com/anthropics/skills/issues/1390)） | OPEN |
| 4 | **claude-api**（#1730，+ #1487） | API 参考与工具使用概念 | **单次工具调用中急切注入约 156k tokens**，耗尽上下文窗口（[#1487](https://github.com/anthropics/skills/issues/1487)）——已报告的最严重资源 bug。另有 3 个失效文档 URL（[#1730](https://github.com/anthropics/skills/pull/1730)） | OPEN |
| 5 | **webapp-testing**（#1980、#1976） | 浏览器/服务器测试自动化脚本 | 安全 + 正确性：`shell=True` 命令注入风险 CWE-78（[#1980](https://github.com/anthropics/skills/pull/1980)）；元素发现中 textarea/select 被误报为 `input`（[#1976](https://github.com/anthropics/skills/pull/1976)） | OPEN |
| 6 | **pyxel**（#525） | Python 复古游戏开发——无头运行、帧检查、状态校验 | 本组中存续最久的活跃 PR（2026-03 开启，2026-11 更新）——维护者/社区持续迭代 | OPEN |
| 7 | **document-typography**（#514） | 排版质检：生成的文档中孤行/寡行预防、编号对齐 | 填补普遍存在的输出质量缺口（“Claude 生成的每一份文档”）；广泛适用性带来早期关注 | OPEN |
| 8 | **ODT**（#486） | OpenDocument（.odt/.ods）创建/填充/解析/转换 | 在现有 docx/pptx/pdf skills 之外填补明显的格式覆盖空白（ODF/ISO 标准） | OPEN |

---

## 2. 社区需求趋势（来自 Issues）

1. **技能供应链的信任与安全**——[Issue #492](https://github.com/anthropics/skills/issues/492)（46 条评论，讨论量最高）警告社区技能以 `anthropic/` 命名空间发布，存在信任边界冒充风险。XSS（[#1394](https://github.com/anthropics/skills/issues/1394)）与命令注入报告进一步印证了这一点。
2. **团队/组织级技能分发**——[Issue #228](https://github.com/anthropics/skills/issues/228)（17 条评论，8 👍）请求共享组织库与分享链接，而非手动上传 `.skill` 文件。
3. **技能评估可靠性**——[Issue #556](https://github.com/anthropics/skills/issues/556) 报告**所有评估查询的触发率均为 0%**；社区显然希望在扩大贡献规模之前先有可信的技能测试基础设施。
4. **上下文/内存效率**——[Issue #1329](https://github.com/anthropics/skills/issues/1329) 提出 `compact-memory`（用符号化记法表示 agent 状态）；[Issue #1487](https://github.com/anthropics/skills/issues/1487) 要求惰性/按需加载技能。
5. **Agent 治理与输出质量门禁**——[Issue #412](https://github.com/anthropics/skills/issues/412)（策略执行、审计追踪）与 [Issue #1385](https://github.com/anthropics/skills/issues/1385)（校准 → 对抗性审查 → 交付验证）显示出对安全/QA 流水线技能的需求。
6. **技能编写最佳实践**——[Issue #202](https://github.com/anthropics/skills/issues/202) 呼吁设计省 token、可操作（而非教程式）的技能。

**净方向：** 需求正从*“增加更多技能”*转向**信任、可测试性、分发与上下文经济性**——即生态系统基础设施，而非全新的技能类型。

---

## 3. 高潜力待合并 Skills（活跃，尚未合并）

| Skill | PR | 为何可能很快落地 |
|-------|-----|-----------------|
| **proofcore-contract-auditor** | [#1771](https://github.com/anthropics/skills/pull/1771) | Solidity/Rust 静态分析 + TON 区块链存证——新颖的 Web3 垂直领域，近期有活动 |
| **md2video-audio** | [#1703](https://github.com/anthropics/skills/pull/1703) | Markdown → MP4，通过 Marp 配 AI 配音；零成本流水线，演示价值高 |
| **scnet-hpc** | [#1615](https://github.com/anthropics/skills/pull/1615) | Slurm/SSH HPC 集群运维——填补科学计算空白 |
| **notion-spec-to-implementation** | [#1245](https://github.com/anthropics/skills/pull/1245) | 规格 → Notion 任务 → 实施计划；最近更新于 2026-10-15 |
| **quantitative-resume-auditor** | [#1245](https://github.com/anthropics/skills/pull/1245) | 捆绑于同一 PR；简历量化评分 |
| **pyxel** | [#525](https://github.com/anthropics/skills/pull/525) | 更新于 2026-11-01——活跃维护，成熟 |
| **document-typography** | [#514](https://github.com/anthropics/skills/pull/514) | 通用的文档质量价值主张 |
| **ODT** | [#486](https://github.com/anthropics/skills/pull/486) | 补齐办公格式覆盖 |

**可能率先合并的快速修复：** 小型、范围明确的 bugfix PR——[#1975](https://github.com/anthropics/skills/pull/1975)（`--cols` 校验）、[#1977](https://github.com/anthropics/skills/pull/1977)（`wrapAround` 取模）、[#538](https://github.com/anthropics/skills/pull/538)（文档引用大小写敏感）、[#1730](https://github.com/anthropics/skills/pull/1730)（失效 URL）。

---

## 4. Skills 生态洞察

> **社区最集中的需求已不再是新 Skills，而是让现有 Skill 层变得可信且经济——可靠的评估/触发、安全的执行、办公文档的正确处理，以及惰性上下文加载——因为生态的瓶颈已从能力广度转向对已发布技能的信心。**

---

*方法说明：源快照中评论数不可用（`undefined`）；排名反映交叉引用的 Issue 讨论量、更新时效性及所报告缺陷的严重程度。*

---

# Claude Code 社区文摘 — 2026-11-03

## 1. 今日亮点

今日发布的版本（v2.1.317）强化了工具使用边界：云端定时任务现在会在收到意外连接器授权时发出警告，Web/Shell 工具调用在将账户邮箱发送到网络之前需要确认。与此同时，问题跟踪器出现了一波显著的清理潮——几乎所有评论数最高的条目都已移至 CLOSED/stale 状态——而开放队列则集中在智能体生命周期可靠性（空闲压缩与后台智能体竞争、配置目录泄漏）以及组织级制品治理上。

## 2. 版本发布

**v2.1.317**
- `/schedule` 现在会在新建的云端定时任务收到你未请求的连接器时发出警告，并提供修复指导。
- 新增预检提示：在 Web 或 Shell 工具调用通过网络传输你的账户邮箱地址之前进行确认。
- 为 Agent SDK 钩子新增 `onFailure: "block"` 支持（发布说明中已截断——完整钩子语义请查阅变更日志）。

## 3. 热门问题

1. **[OPEN] #78537 — 已发布制品的组织级默认共享**（👍 33）
   信号最强的开放增强请求：团队希望已发布制品继承组织级共享策略，而非逐制品配置。高点赞数表明这是企业推广中的治理缺口。
   https://github.com/anthropics/claude-code/issues/78537

2. **[OPEN] #106402 — 后台智能体仍在运行时触发空闲压缩**（macOS，core/agents）
   一个全新的、他人未能复现的缺陷：压缩在运行中途触发，可能干扰活跃的子智能体。与 #102260 中的选择性加入投诉不同，这构成了智能体编排中的一种新故障模式。
   https://github.com/anthropics/claude-code/issues/106402

3. **[OPEN] #98789 — bridge-spawn 根目录忽略 `CLAUDE_CONFIG_DIR`**
   桌面应用每次启动都会重建 `~/.claude/bridge-spawn`，即使配置了自定义配置目录，导致 `~/.claude` 反复出现。已定位具体原因（打包二进制中的模块级常量）——一个小而范围明确的修复。
   https://github.com/anthropics/claude-code/issues/98789

4. **[CLOSED] #65340 — Windows 上的压缩缺陷**（👍 3，13 条评论）
   当日讨论最多的条目（已关闭/stale）。Windows core/desktop 压缩可靠性仍是反复出现的主题；关闭表明要么已修复，要么分诊已饱和。
   https://github.com/anthropics/claude-code/issues/65340

5. **[CLOSED] #78224 — 在可恢复故障时暂停并恢复后台子智能体**
   子智能体不应在使用限制或瞬态 API 错误时终止，而应暂停并恢复。工作通常能在磁盘上存活，但 `failed` 状态会丢失编排状态——这是一个重要的韧性缺口，现已关闭。
   https://github.com/anthropics/claude-code/issues/78224

6. **[CLOSED] #90782 — 子智能体引导上下文污染**
   约 20 个并行子智能体启动中有 5 个在数秒内返回且零工具调用，回显了外来的指令块（MCP 服务器指令、插件技能规则）。一个带有复现的严重隔离问题；值得关注是否回归。
   https://github.com/anthropics/claude-code/issues/90782

7. **[CLOSED] #89102 — `EnterWorktree` 翻转会话级隔离锁存，破坏并发智能体的 Bash**
   一个微妙的并发隐患：在同一批次中调用 `EnterWorktree` 与子智能体生成会破坏所有智能体的 Bash，即使是那些位于自己 worktree 中的智能体。对并行智能体用户而言是高价值的复盘材料。
   https://github.com/anthropics/claude-code/issues/89102

8. **[CLOSED] #90658 — 自动模式访问了项目范围之外的文件**
   自动模式下的权限范围逃逸，包括凭证相关数据。以 needs-repro/stale 关闭——但其安全影响值得社区关注。
   https://github.com/anthropics/claude-code/issues/90658

9. **[CLOSED] #89716 — PreToolUse 钩子无法触及 Bash 文件命令识别**
   钩子按工具名称匹配，因此一旦流量经由 Bash 路由，Read/Edit/Write 拒绝规则的等价物便被绕过。对注重安全的用户而言是一个根本性的钩子覆盖缺口。
   https://github.com/anthropics/claude-code/issues/89716

10. **[CLOSED] #89263 — 当限制仅杀死后台子智能体时，自动继续从不触发**
    主线程空闲，子智能体在会话限制时死亡，重置时无任何恢复。与 #78224 和 #88466 合并成一个连贯的“后台智能体生命周期”投诉集群。
    https://github.com/anthropics/claude-code/issues/89263

## 4. 关键 PR 进展

窗口期内仅有两个 PR 更新；本文摘涵盖两者：

1. **[CLOSED] #106583 — 将问题分诊与去重工作流迁移至 Claude Sonnet 5.5**
   将三个内部工作流（`claude-issue-triage.yml`、`claude-dedupe-issues.yml`、`claude.yml`）迁移至 Sonnet 5.5，特别是将 opus-triage 和 dedupe 从较旧的 4.x 模型升级。表明 Anthropic 正在仓库自动化上内部试用较新模型。
   https://github.com/anthropics/claude-code/pull/106583

2. **[OPEN] #58673 — 无描述的 Stale/开放 PR**
   长期开放的 PR（创建于 2026 年 5 月），标题仅一个字符。无可操作内容；此处仅为完整性而标注。
   https://github.com/anthropics/claude-code/pull/58673

## 5. 热门讨论

本窗口期未提供讨论数据。

## 6. 功能请求趋势

- **智能体生命周期韧性**：在可恢复故障时暂停/恢复而非终止（#78224），使用重置时可靠的自动继续（#89263、#88466），以及压缩与运行中智能体之间的协调（#106402）。
- **组织级治理**：已发布制品的默认共享策略（#78537），更好的制品打印/分页（#90960），以及无需复制上下文的跨实例知识共享（#91001）。
- **钩子/权限对等**：将钩子覆盖扩展到 Bash 中介的文件操作（#89716），以及外部 API 操作前更严格的确认（#91007）。
- **配置/状态控制**：在所有子系统遵循 `CLAUDE_CONFIG_DIR`（#98789），以及在 `/resume` 中显示 SDK 启动的会话（#89077）。
- **超时人体工学**：为长时间运行的 CLI 命令提供更长的默认值（#91006）。

## 7. 开发者痛点

- **后台子智能体可靠性是主导主题**：子智能体在使用限制时死亡且无法恢复，空闲压缩与它们竞争，引导上下文被污染，`EnterWorktree` 可能破坏并发智能体的 Bash。运行并行智能体的用户正遭遇一系列生命周期缺陷。
- **钩子与权限盲点**：PreToolUse 钩子按工具名称匹配，因此 Bash 优先路由绕过了 Read/Edit 拒绝规则——这是一个反复出现的安全模型担忧，与自动模式范围逃逸并列。
- **分诊疲劳/陈旧关闭**：今日更新的问题绝大多数为 CLOSED-stale，包括安全相关报告（#90658、#89716）——这表明高价值报告可能在未解决的情况下逐渐老化。
- **平台特定的核心缺陷**：Windows 压缩（#65340）和桌面进程泄漏（#89859）反复出现，Windows 桌面每次定时运行泄漏一个持久 CLI 进程。
- **配置/状态蔓延**：尽管设置了 `CLAUDE_CONFIG_DIR` 仍重建 `~/.claude`，`/resume` 中隐藏的 SDK 会话，以及 OAuth 透传下网关模型发现失败（#84583），都指向环境与入口点状态解析不一致。

</details>

<details>
<summary><strong>OpenAI Codex</strong> — <a href="https://github.com/openai/codex">openai/codex</a></summary>

# OpenAI Codex 社区文摘 — 2026-11-03

## 1. 今日亮点

Windows 桌面应用依然是社区痛点的主要来源，围绕文件附件的一系列回归问题（XLSX/PPTX 发送卡在“转圈”状态）以及本地项目消失的问题获得了最高互动量。在工程方面，一大批 `copyberry[bot]` 的 PR 落地，涉及云客户端（类型化转录事件、分页历史记录）、MCP/Search 发现以及 Guardian 安全审查的改进。与此同时，CLI 和桌面端新出现一波“所选模型已达容量上限”的报告——包括来自 Pro 200 订阅用户的反馈——正成为一个新的可靠性隐忧。

---

## 2. 版本发布

**rust-v0.167.0-alpha.4** — Alpha 版本发布，除版本号提升外无详细更新日志。视为增量预发布版本；未提供迁移说明。

---

## 3. 热门 Issue（10 条值得关注）

1. **#42739 — Windows：更新后本地项目从侧边栏消失**（69 条评论，👍5）
   今日互动量最高的 Issue。Windows 桌面端更新后，Projects 区域显示“无项目”，而聊天记录和磁盘上的文件夹均完好无损。大量评论表明问题具有广泛可复现性；很可能是持久化/迁移回归。[链接](https://github.com/openai/codex/issues/42739)

2. **#35446 — Windows 10 Computer Use 在 SoftwareBitmap 转换中死锁**（57 条评论）
   自 7 月以来长期存在的死锁问题，发生在 Win10 22H2 上屏幕捕获期间的 `FrameArrived` 中。持续的活跃度表明该问题仍未修复，完全阻断了受影响用户的 Computer Use 功能。[链接](https://github.com/openai/codex/issues/35446)

3. **#17793 — TUI：退格键删除多个字符**（23 条评论，👍6）
   自 4 月以来持续存在的 CLI/TUI 输入 bug，影响提示输入体验。因其长期未修复而值得关注——一个小而高烦扰度的缺陷反复出现。[链接](https://github.com/openai/codex/issues/17793)

4. **#58302 — macOS：XLSX 附件保持待处理状态，发送控件持续转圈**（19 条评论）
   附件处理问题集群的一部分。在构建版本 `26.1027.30824` 上，Excel 文件在发送时卡住。与 Windows 报告（#58660、#58313）配对出现，表明上传预检存在跨平台回归。[链接](https://github.com/openai/codex/issues/58302)

5. **#56697 — Ctrl+Tab 停止切换聊天；循环切换未绑定/不可配置**（19 条评论，👍10）
   键盘导航回归，获得强烈点赞信号。用户希望最近聊天循环切换可配置——这是 UX 缺口，而非单纯的 bug。[链接](https://github.com/openai/codex/issues/56697)

6. **#58313 — Codex 无法读取 XLSX/PPTX 附件；能力端点循环调用**（9 条评论，👍6）
   详细诊断：113 秒内向 `/backend-api/pages/creation-capabilities` 发起 322 次 GET 请求，全部返回 HTTP 200，但之后没有上传 POST——指向重复的预检能力检查。为附件问题集群提供了有价值的根因证据。[链接](https://github.com/openai/codex/issues/58313)

7. **#59059 — macOS：已有对话报“模型已达容量上限”，新聊天正常**（13 条评论）
   11/03 的全新报告。同一模型在新聊天中正常工作，但在已有/转发的线程中失败——表明是陈旧的按对话模型/会话状态而非真正的容量耗尽。[链接](https://github.com/openai/codex/issues/59059)

8. **#57656 — 使用量限制指示器从侧边栏消失**（10 条评论，👍20）
   本组中点赞比例最高。更新后移除了配额可见性功能——一个小 UI 改动，但对受速率限制的用户产生了超出比例的信任影响。[链接](https://github.com/openai/codex/issues/57656)

9. **#38843 — Windows MSIX 自动更新器无限循环；应用启动后挂起**（8 条评论）
   长期存在的更新循环加上启动时永久挂起的进程。安装/部署失败的叠加使得部分用户的 Windows 应用实际上无法更新。[链接](https://github.com/openai/codex/issues/38843)

10. **#57975 — 桌面端、CLI 和 ChatGPT Work 均出现容量错误，尽管配额可用**（2 条评论）
    新集群（10 月 27–29 日），各端均出现“所选模型已达容量上限”的报错，尽管配额充足。尚属早期但可能影响较大；另见 #59088（Pro 200）。[链接](https://github.com/openai/codex/issues/57975)

---

## 4. 关键 PR 进展（10 条重要）

1. **#59066 — 云客户端中的类型化条目生命周期事件**
   将实时条目开始/完成通知解码为类型化的 `TranscriptUpdate::ItemStarted/ItemCompleted`，包含回合标识和时间戳。云客户端可观测性的重大进步。[链接](https://github.com/openai/codex/pull/59066)

2. **#59047 — 解码实时转录更新**
   将 `Attach` 通知中的不透明 protobuf 字节替换为类型化事件，暴露实时文本、命令输出、文件变更和回合状态。[链接](https://github.com/openai/codex/pull/59047)

3. **#59075 — 云客户端中的分页历史记录读取**
   新增 `Client::list_turns` 和 `Client::list_items`，基于原生 gRPC 历史端点并支持回合过滤——以存储的元数据补充实时事件。[链接](https://github.com/openai/codex/pull/59075)

4. **#59036 — 策略感知的搜索提供者回退**
   在遵守主机搜索限制的前提下解析模型选择的搜索提供者；回退时保留拥有现有浏览引用的提供者。[链接](https://github.com/openai/codex/pull/59036)

5. **#59034 — MCP 绑定中的模型 Search 发现**
   新增 `McpBinding` API，通过授权的宿主自有 Apps 连接发现模型的 Search 工具配置，支持分页 `tools/list`。[链接](https://github.com/openai/codex/pull/59034)

6. **#59057 — 定制化 MCP doctor 修复建议**
   将配置错误与缺失环境变量分离，使 `doctor` 不再为无效的服务器配置/不可达 URL 推荐环境变量修复。与 MCP 相关投诉直接相关（见讨论 #33682）。[链接](https://github.com/openai/codex/pull/59057)

7. **#59107 — 选择性重新审视 Guardian 拒绝**
   新增默认关闭的 `guardian_denial_reconsideration`，在最终确定前对同步 Guardian 拒绝进行一次重新评估。针对安全检查误报（#57328、#59112）。[链接](https://github.com/openai/codex/pull/59107)

8. **#59028 — Guardian 审查中预备 MCP 所有者权限**
   修复当步骤捕获上下文后权限发生变化时，Guardian 对直接 MCP 调用的审查遗漏被拒绝路径的问题。[链接](https://github.com/openai/codex/pull/59028)

9. **#59111 — 模型目录刷新后规范化已保存的聊天设置**
   清除刷新后的目录不再支持的陈旧推理强度/服务层级选择——直接关联“容量上限”类聊天状态 bug。[链接](https://github.com/openai/codex/pull/59111)

10. **#59073 — 图像生成的可选动画 GIF 输出**
    功能门控（`features.image_generation_animation`，默认关闭）的 GIF 生成/编辑，支持帧数和时长控制。[链接](https://github.com/openai/codex/pull/59073)

荣誉提名：**#59071/#59025** — `code_mode_only_strict_3p_tools` 标志当天添加后又回滚（Code Mode Only 工具延迟的快速迭代）；**#59059 相关** **#59041** 在完成通知中暴露助手内容 token 计数。

---

## 5. 热门讨论

**Ideas**
- **#33682 — “MCP bug 被系统性忽视——这太离谱了”**（👍22，5 条评论）：点赞最高的讨论；声称持续数周的 OAuth/工具发现 MCP 故障未得到处理。值得注意的是，今日的 PR（#59057、#59028）恰好涉及这一领域。[链接](https://github.com/openai/codex/discussions/33682)
- **#27754 — AGENTS.md 可复用项目指南地图**（👍4）：插件实验，生成紧凑的 `AGENTS.md` 操作地图以引导未来会话。[链接](https://github.com/openai/codex/discussions/27754)
- **#9230 — AI 驱动开发的更高层可视化界面**：主张人类应在系统层面指导系统，而非逐行编辑。[链接](https://github.com/openai/codex/discussions/9230)
- **#59040 — “Codex 社区算力”**：提议将闲置 PC 资源池化为共享 AI 基础设施。新颖但偏 speculative。[链接](https://github.com/openai/codex/discussions/59040)

**Q&A**
- **#58905 — 允许的工具暴露禁用能力时的 MCP 权限范围划定**：关于 Playwright-MCP 的实际问题——允许导航/点击但阻止页面检查——一个真实的沙箱边界设计问题。[链接](https://github.com/openai/codex/discussions/58905)
- **#16563 — 版本 0.118 要求 MCP 调用持续授权**：反复出现的授权疲劳投诉。[链接](https://github.com/openai/codex/discussions/16563)
- **#40773 — IntelliJ 终端输入区域太暗**：轻微的 IDE 主题回归。[链接](https://github.com/openai/codex/discussions/40773)

**Show and tell**
- **#58912 — Codex Mobile Workbench**：自托管的手机/PWA 前端，连接你本机上的原生 Codex——远程指挥本地 agent。[链接](https://github.com/openai/codex/discussions/58912)
- **#58492 — Bossanova**：本地加密保险库 + MCP 桥接，让 Codex 获取 API 密钥而无需在对话中暴露值。[链接](https://github.com/openai/codex/discussions/58492)
- **#55901 — GFT Map**：跨新对话的持久决策/约束地图。[链接](https://github.com/openai/codex/discussions/55901)
- **#58104 — Kilobat**：Mac 菜单栏 agent 网络工具（流量监控、MCP/CLI）。[链接](https://github.com/openai/codex/discussions/58104)
- **#59032 — Turnfeed**：用 Codex 在 ChatGPT Sites 上构建的社交信息流。[链接](https://github.com/openai/codex/discussions/59032)

---

## 6. 功能需求趋势

- **可配置的键盘/UI 导航**：Ctrl+Tab 重新绑定（#56697）和可见的更新控件（#53961）指向对用户可配置桌面 UX 的需求。
- **跨会话记忆/持久化**：GFT Map（#55901）和指南地图（#27754）反映了一个反复出现的主题——在新对话之间携带决策和上下文。
- **MCP 易用性与权限粒度**：细粒度 MCP 范围划定（#58905）、减少重复授权（#16563）和可操作的诊断（#59057）都是关注焦点。
- **碎片化的桌面信息架构**： #37791 认为统一的 ChatGPT/Work/Codex 应用需要更好的信息架构；使工作区标签与实际使用方式对齐。
- **IDE 集成打磨**：从资源管理器拖放文件引用到 VS Code 聊天（#38590）。

---

## 7. 开发者痛点

1. **Windows 桌面稳定性/更新可靠性是头号投诉。** 一连串问题——项目消失（#42739）、MSIX 无限更新循环（#38843）、白屏崩溃循环（#57562）、进程挂起——持续侵蚀 Windows 的主要体验。
2. **跨平台的文件附件/上传回归。** 在 macOS 和 Windows 上，约 10 月 27–30 日之后的构建中 XLSX 和 PPTX 发送卡住（#58302、#58258、#58313、#58660），有证据表明存在循环的能力预检（`creation-capabilities`）和缺失的上传 POST。对以 Office 为核心的工作流而言严重性很高。
3. **“所选模型已达容量上限”已不再可信地是容量问题。** 它出现在新对话 vs 已有对话的对比中（#59059），跨桌面端/CLI/Work（#57975），以及 Pro 200 订阅用户（#59088）——指向陈旧的会话/目录状态。PR #59111 正是针对此问题。
4. **MCP 可靠性 + 安全检查误报。** 讨论 #33682（👍22）声称 OAuth/发现 bug 被系统性忽视；#59112 和 #57328 报告过多的网络安全拒绝阻断了无害工作。#59107（选择性重新审视）是一个有前景的缓解措施。
5. **小而慢性的 UX 缺陷始终不关闭**——TUI 退格键（#17793 自 4 月起）、配额指示器移除（#57656，👍20）、不可配置的快捷键——累积起来削弱了对提示级人机工效的信任。

---
*注：源数据中的 Issue 创建日期（如 2026-04）跨越数月，尽管有“过去 24 小时内更新”的过滤条件；新近度反映的是最后活动时间，而非创建时间。*

</details>

<details>
<summary><strong>Gemini CLI</strong> — <a href="https://github.com/google-gemini/gemini-cli">google-gemini/gemini-cli</a></summary>

# Gemini CLI 社区文摘 — 2026-11-03

## 今日亮点

nightly 通道发布了一项稳定性修复，解决了 Whisper 模型下载中断后，残留的部分文件被误认为已安装模型的问题。本周期的问题流量主要集中在 **agent 可靠性** 上：子 agent 失控/挂起、MAX_TURNS 被误报为目标成功完成，以及 agent 忽略配置或技能利用率不足。此外，还有一大批 PR 围绕无头运行的健壮性、ACP 集成以及平台特异问题（WSL2、Wayland）落地。

---

## 发布

**v0.67.0-nightly.20261103.g0ac57c029**
- `fix(core)`：中断/截断的 Whisper 模型下载不再被视为已安装——此前 `~/.gemini/whisper_models/<modelName>` 处的部分文件会通过 `isModelInstalled()` 检查，导致音频功能静默失效。（[#30058](https://github.com/google-gemini/gemini-cli/pull/30058)）

---

## 热门 Issue

1. **[#22323](https://github.com/google-gemini/gemini-cli/issues/22323) — 子 agent 的 MAX_TURNS 被报告为 GOAL 成功（p1，13 条评论）**
   `codebase_investigator` 在尚未执行任何工作就触及轮次上限的情况下，仍返回 `status: success` / `Termination Reason: GOAL`。这对自动化来说很危险：编排器无法区分被截断的工作和已完成的工作。这是本时间窗口内评论最多的 issue，已标记待重新测试。

2. **[#21409](https://github.com/google-gemini/gemini-cli/issues/21409) — 通用 agent 永久挂起（p1，8 👍）**
   任何委派给通用 agent 的操作（甚至创建文件夹）都会挂起；用户报告等待长达一小时。变通方案：完全禁用子 agent。强烈的点赞信号（8 👍）表明影响广泛。

3. **[#21968](https://github.com/google-gemini/gemini-cli/issues/21968) — Gemini 很少自主使用技能/子 agent**
   虽是轶事，但引起共鸣：尽管描述高度相关（gradle、git），模型只有在明确指示时才会调用自定义技能。这指向技能激活中的路由/提示缺口。

4. **[#22267](https://github.com/google-gemini/gemini-cli/issues/22267) — Browser Agent 忽略 `settings.json` 覆盖**
   `AgentRegistry` 正确合并了配置，但 Browser Agent 绕过了它——例如 `maxTurns` 被忽略。这是一个配置完整性 bug，会削弱用户对设置的信任。

5. **[#19873](https://github.com/google-gemini/gemini-cli/issues/19873) — 零依赖 OS 沙箱 + 执行后意图路由（维护者撰写）**
   提议顺应 Gemini 3 对原生 bash 的亲和力（grep/cat/sed/awk 链式调用），配合沙箱化而非限制它。这是一个带有 9 条评论的战略方向 issue。

6. **[#22745](https://github.com/google-gemini/gemini-cli/issues/22745) — AST 感知的文件读取/搜索/映射 EPIC**
   追踪 AST 工具是否能减少错位读取和 token 噪音。配套 issue [#22746](https://github.com/google-gemini/gemini-cli/issues/22746)（AST CLI 映射，tilth/glyph）和 [#22747](https://github.com/google-gemini/gemini-cli/issues/22747)（用于读取/搜索的 ast-grep）显示出协调一致的调查工作。

7. **[#24246](https://github.com/google-gemini/gemini-cli/issues/24246) — 超过 128 个工具时出现 400 错误**
   工具数量上限导致硬性 API 失败；请求更智能的工具范围限定。随着 MCP/扩展生态增长，这愈发相关。

8. **[#22672](https://github.com/google-gemini/gemini-cli/issues/22672) — Agent 应劝阻破坏性行为**
   模型偶尔在存在更安全路径时仍使用 `git reset`/`--force`。维护者提交；可能纳入策略/安全工作。

9. **[#23571](https://github.com/google-gemini/gemini-cli/issues/23571) — 模型在仓库中散落临时脚本**
   当 shell 执行受限时，模型会在各目录中散布编辑脚本，使干净的提交变得复杂。这是一个工作流卫生痛点。

10. **[#21983](https://github.com/google-gemini/gemini-cli/issues/21983) — Browser 子 agent 在 Wayland 上失败（p1）**
    报告称尽管失败仍以 `Termination Reason: GOAL` 完成——这是误导性成功报告的又一实例，此次为平台特异问题。

其他值得注意：[#20079](https://github.com/google-gemini/gemini-cli/issues/20079)（符号链接的 agent 文件未被发现）和 [#21763](https://github.com/google-gemini/gemini-cli/issues/21763)（`/bug` 报告遗漏子 agent 上下文，妨碍分诊）。

---

## 关键 PR 进展

1. **[#30020](https://github.com/google-gemini/gemini-cli/pull/30020) — 从失败的重试尝试中丢弃工具调用**
   在流中途重试时，`GeminiChat` 会同时产出两次尝试的 chunk，导致有副作用的命令被执行两次。这是重试语义的重要正确性修复。

2. **[#30094](https://github.com/google-gemini/gemini-cli/pull/30094) — 在 UI 中显示执行中的子 agent 工具调用（修复 #22589）**
   `useToolScheduler` 过滤掉了跳过审批的非根调度器调用（读取/搜索），因此子 agent 活动不可见。显著改善了可观测性。

3. **[#30107](https://github.com/google-gemini/gemini-cli/pull/30107) — 换行终端上可点击的认证 URL（p1，修复 #29430）**
   在窄终端中换行的长登录链接被 URL 检测截断，产生 Google `Error 400`。这是一个高摩擦的入门修复。

4. **[#30051](https://github.com/google-gemini/gemini-cli/pull/30051) — 无头中断时杀死 shell 并持久化工具调用**
   修复了无头运行（`gemini -p --yolo`）在 SIGINT/SIGTERM 时以代码 0 退出并留下分离的 shell 进程仍存活的问题——对 CI 使用至关重要。

5. **[#29958](https://github.com/google-gemini/gemini-cli/pull/29958) — 向 ACP 客户端发送 AnsiOutput 结果的 shell 输出**
   ACP 客户端对 `run_shell_command` 收到空的 `tool_call_update`，因为 PTY 快照类型未被转发。修复了 IDE/编辑器集成的可见性。

6. **[#29951](https://github.com/google-gemini/gemini-cli/pull/29951) — 在集成测试中重试瞬态 VS Code 下载（p0）**
   nightly 发布因单次网络超时而失败，实际并无测试失败。以最高优先级直接解决 CI 不稳定性。

7. **[#30097](https://github.com/google-gemini/gemini-cli/pull/30097) — WSL2 上的图像粘贴（WSLg 剪贴板 + BMP 回退）**
   当 `XDG_SESSION_TYPE` 未设置（WSLg）时回退到 `WAYLAND_DISPLAY`/`DISPLAY`，并添加 BMP 支持。修复 #22274。

8. **[#29831](https://github.com/google-gemini/gemini-cli/pull/29831) — 将 ripgrep 超时暴露为错误（已关闭）**
   此前，超时被杀死的 ripgrep 会将部分匹配返回为"未找到匹配"（1–3 个命中）或静默的部分结果（>3）。这是搜索工具诚实性的正确性修复。

9. **[#29978](https://github.com/google-gemini/gemini-cli/pull/29978) — 在 `read_background_output` 中保留超大/边界行**
   64KB 窗口修剪可能对单行日志返回空的"Full Log Output:"。虽小但对后台进程调试影响很大。

10. **[#30003](https://github.com/google-gemini/gemini-cli/pull/30003) / [#30096](https://github.com/google-gemini/gemini-cli/pull/30096) — 当变量已设置但为空时，`${VAR:-default}` 遵循默认值**
    使设置环境变量扩展与 Bash 语义对齐。同一修复有两个竞争 PR（见 #30000）——可能需要去重。

荣誉提名：[#30102](https://github.com/google-gemini/gemini-cli/pull/30102)（IDE 连接记录为 `ide_connection` 而非虚增 `start_session` 计数）、[#29960](https://github.com/google-gemini/gemini-cli/pull/29960)（遥测 `SERVICE_VERSION` 现在报告 CLI 版本而非 Node 运行时）以及 [#30104](https://github.com/google-gemini/gemini-cli/pull/30104)（在 `/skills link` 后刷新斜杠命令）。

---

## 功能请求趋势

- **Agent 编排易用性**：并行子 agent 协作与共享内存（[#18287](https://github.com/google-gemini/gemini-cli/issues/18287)）、通过 `/chat share` 查看子 agent 轨迹（[#22598](https://github.com/google-gemini/gemini-cli/issues/22598)）以及通过 `settings.json` 发现子 agent（[#18285](https://github.com/google-gemini/gemini-cli/issues/18285)）。
- **Token 高效、精准上下文**："Tactful Extraction"精准读取（[#19561](https://github.com/google-gemini/gemini-cli/issues/19561)）和 AST 感知 EPIC 系列——明显朝着更少轮次和更少上下文膨胀的方向推进。
- **持久任务状态**：用基于文件的 CRUD 追踪替代上下文内 WriteToDo（[#18836](https://github.com/google-gemini/gemini-cli/issues/18836)）以及任务追踪器的原生文件工具（[#21000](https://github.com/google-gemini/gemini-cli/issues/21000)）。
- **自我认知**：agent 应准确描述自身的标志、快捷键和调用模式（[#21432](https://github.com/google-gemini/gemini-cli/issues/21432)）。
- **韧性功能**：浏览器 agent 会话接管/锁恢复（[#22232](https://github.com/google-gemini/gemini-cli/issues/22232)）；按工作区而非全局的策略（[#18397](https://github.com/google-gemini/gemini-cli/issues/18397)）。

## 开发者痛点

- **误导性成功报告**：`Termination Reason: GOAL` 掩盖轮次上限命中和平台失败的反复模式（#22323、#21983）。编排和评估无法信任 agent 的输出状态。
- **挂起和停滞**：通用 agent 挂起（#21409）、使用 vite 脚手架时交互式提示死锁（#22465）以及输出钩子崩溃（#22186）。
- **配置未被一致遵循**：Browser Agent 忽略 `settings.json`（#22267）；符号链接的 agent 文件未被发现（#20079）。
- **工作区卫生**：散落的临时脚本（#23571）和破坏性 git 命令（#22672）造成清理开销和风险。
- **分诊摩擦**：`/bug` 报告缺少子 agent 上下文（#21763）；评估稳定性正积极投入（#23166、#23313）。
- **瞬态网络故障导致的 CI 不稳定**正被积极解决（#29951），同时同一修复存在重复/竞争 PR（#30003 vs #30096、#29831），表明流程开销。

---

*本文摘由 2026-11-03 的仓库元数据生成。Issue/PR 优先级和标签按源数据所示引用。*

</details>

<details>
<summary><strong>GitHub Copilot CLI</strong> — <a href="https://github.com/github/copilot-cli">github/copilot-cli</a></summary>

# GitHub Copilot CLI — 社区文摘
**2026-11-03**

## 1. 今日亮点
快速发布节奏持续，24 小时内发布了四个构建，其中最引人注目的是带全局搜索的统一 `/settings` 面板以及 ACP 模式修复（长期存在的 `includeCoAuthoredBy: false` 缺陷现已解决）。与此同时，问题跟踪器显示键盘/输入可靠性、MCP 认证健壮性和 ACP（Agent Client Protocol）功能对齐差距方面势头强劲。社区参与集中在 UX 基础功能上：消息队列取消（#1857）、Windows 行尾符损坏（#1148）以及终端程序状态报告（#5112）。

## 2. 版本发布
过去 24 小时内发布了四个版本：

- **v1.0.106-6**
  - **新增：** `/settings` 现为所有用户打开统一面板，支持跨设置和本地资源的全局搜索。
  - **新增：** 交互式 CLI 向支持的终端报告程序状态（需要注意的信号提示）。
  - **改进：** 仅面向用户的 MCP 内容不再发送给模型——这是一项隐私/上下文卫生改进。
- **v1.0.106-5** — **修复：** `includeCoAuthoredBy: false` 现在在 ACP 模式下生效（不再出现不需要的 Co-authored-by 尾注）；定向 `/settings` 命令立即反映选择；会话恢复时禁用遥测的警告已修正。
- **v1.0.106-4** — **修复：** 空会话不再在会话选择器中闪现；括号/方括号/引号/反引号内的 `@file` 提及现在能在 shell 命令中正确附加和展开；自定义 agent 修复。
- **v1.0.106-3** — **新增：** 可选的会话管理工具和本地会话的 `@mentions`；**改进：** 托管账户策略刷新警告包含 Policy Helper 发现；**修复：** 匿名连接复用有效的 MCP OAuth 凭据。

> 注意：v1.0.106-6 与 #5112 中开放的功能请求（OSC 7501 程序状态）部分重叠——值得关注其实现是否与该规范一致。

## 3. 热门问题

1. **[#1857](https://github.com/github/copilot-cli/issues/1857) — 取消/移除已入队消息（15 条评论，👍33，OPEN）** ⭐ *参与度最高*
   当 agent 忙碌或 `/compact` 期间无法取消已入队消息（`Ctrl+Q`/`Ctrl+Enter`）。本周期社区反应最高——明确的队列控制 UX 差距。

2. **[#1148](https://github.com/github/copilot-cli/issues/1148) — Windows 上 LF 文件被强制使用 CRLF 行尾（7 条评论，CLOSED）**
   一个重要的文件完整性缺陷现已关闭。对跨平台仓库和 diff 影响很大——关闭表明修复已落地，但未关联 PR 的关闭值得 Windows 用户验证。

3. **[#5112](https://github.com/github/copilot-cli/issues/5112) — 交互式 CLI 中的 OSC 7501 程序状态（5 条评论，👍11，OPEN）**
   请求标准化的终端状态报告（工作中/阻塞）。v1.0.106-6 的“程序状态”新增可能是部分进展；社区将审视其规范一致性。

4. **[#1257](https://github.com/github/copilot-cli/issues/1257) — Termux/Android 支持（6 条评论，👍7，OPEN）**
   `pty.node` 原生模块在 Android 上加载失败。移动/随时随地使用的持续需求；受限于原生依赖架构。

5. **[#5171](https://github.com/github/copilot-cli/issues/5171) — Windows 上 NumLock 关闭时数字键盘导航被忽略（2 条评论，OPEN）**
   影响 Windows Terminal 键盘可访问性的回归。更广泛的输入处理问题群的一部分。

6. **[#3015](https://github.com/github/copilot-cli/issues/3015) — OAuth 后 MCP `tools/call` 丢失 Authorization 头（2 条评论，👍2，OPEN）**
   Bearer token 在 `initialize` 时发送，但在 Streamable HTTP + OAuth 2.1/DCR 的后续 JSON-RPC 调用中被省略。对依赖每次调用认证的 MCP 服务器作者来说很严重。

7. **[#5145](https://github.com/github/copilot-cli/issues/5145) — 400 `reasoning_text` 必须回传（2 条评论，OPEN）**
   使用 `wireApi: "responses"` 的自定义 OpenAI 兼容提供商在后台 shell 退出后失败（合成的 assistant 轮次）。阻碍高级 BYO 模型工作流。

8. **[#4646](https://github.com/github/copilot-cli/issues/4646) — 自定义模型上压缩失败“Tool choice must be auto”（2 条评论，OPEN）**
   `/compact` 在自定义 OpenRouter/GLM 模型上出错。加剧了 #5145 中的自定义模型痛点。

9. **[#5304](https://github.com/github/copilot-cli/issues/5304) — `/keep-alive` 在 WSL2 中无效（2 条评论，OPEN）**
   `systemd-inhibit` 不会创建 Windows 主机电源请求。在非常常见的开发环境中破坏长时间运行任务。

10. **[#5335](https://github.com/github/copilot-cli/issues/5335) — 反复出现的 HTTP 422 网络安全风险标记阻碍本地调试（OPEN）**
    误报安全过滤器中断合法的偶发测试调试。对安全相关开发摩擦很大；预计会获得关注。

*荣誉提名：* #4891（Windows 硬崩溃 0xc0000005 丢失进行中的轮次）、#5206/#5268/#5269/#4700/#4106（一波 ACP 功能对齐缺陷——今天许多已关闭）、#5336（按运行禁用 MCP 的开关）。

## 4. 关键 PR 进展
过去 24 小时内仅更新了 **1 个 PR**：

1. **[#5339](https://github.com/github/copilot-cli/pull/5339) — 只读 agent 监控 TUI（图表 + 时间线）（OPEN）**
   添加 `tools/agent-monitor`，一个用于选定本地会话的独立终端查看器。可视化 agent 父子关系和等待关系（谁启动了谁、谁阻塞了谁）。对调试多 agent 编排很有用；风格仿照 Copilot CLI 自身的界面语言。

> 没有其他 PR 活动，发布流水线似乎直接发布而非通过公开 PR 队列——这一趋势值得关注，因其影响社区可见性。

## 5. 热门讨论
*本数据集中未提供讨论数据——本节省略。*

## 6. 功能请求趋势
从所有 Issue 和单个 PR 中提炼：

- **输入与键盘控制（3+ 个 issue）：** 取消已入队消息（#1857）、数字键盘导航（#5171）、扩展的 composer 草稿读取/恢复 API（#5338）、Ctrl+K 撤销（#5331）、devcontainers 中的图片粘贴（#5334）。这是**最大的集群**——用户想要精致、符合标准的终端编辑体验。
- **ACP 功能对齐加固：** 今天大量 issue（#4700、#4106、#5133、#5268、#5269、#5336）和修复针对 `copilot --acp`。ACP 正成为一等表面，客户端遇到功能/设置差距。
- **MCP 健壮性与控制：** 按运行禁用 MCP 开关（#5336）、OAuth 头持久化（#3015）、面向用户的内容过滤（已在 106-6 中发布）。
- **可观测性与状态：** OSC 7501 程序状态（#5112）和 agent 监控 TUI（#5339）——对 agent 状态和编排可见性的需求增长。
- **平台广度：** Termux/Android（#1257）、WSL2 keep-alive（#5304）、Windows PowerShell `-LiteralPath` 指导（#5340）。
- **主题/可访问性：** 自定义提示着色（#5327）。

## 7. 开发者痛点
- **Windows 平台摩擦占主导：** CRLF 文件损坏（#1148）、丢失轮次的硬崩溃（#4891）、数字键盘输入回归（#5171）以及 PowerShell 通配符路径缺陷（#5340）构成反复出现的主题。Windows 用户是积极发声且受影响的群体。
- **自定义/BYO 模型支持脆弱：** 压缩失败（#4646）和推理内容往返错误（#5145）阻碍使用 OpenAI 兼容提供商的用户。这削弱了 Copilot CLI 作为模型无关 harness 的定位。
- **ACP 模式落后于交互式 CLI：** 设置（`skillDirectories`、`includeCoAuthoredBy`）、插件技能、子 agent 身份和会话命令都存在或曾存在 ACP 特定差距。通过 ACP 集成的客户端感觉是二等公民。
- **安全过滤器导致误报：** 网络安全风险 422（#5335）中断合法调试，呼应了开发者对过于激进护栏的更广泛担忧。
- **静默失败：** 恢复会话中的权限拒绝（#5206）和丢失的 MCP 认证头（#3015）静默失败，比响亮错误更难诊断。
- **队列/终端 UX 差距：** 无法取消已入队工作（#1857）和提示编辑器缺少撤销（#5331）表明交互循环仍需成熟。

---
*文摘基于 2026-11-03 数据快照生成。所有链接指向 github.com/github/copilot-cli。*

</details>

<details>
<summary><strong>OpenCode</strong> — <a href="https://github.com/anomalyco/opencode">anomalyco/opencode</a></summary>

# OpenCode 社区文摘 — 2026-11-03

## 1. 今日亮点

过去 24 小时内没有新版本发布，但议题活动主要由**免费额度问题**（巨型讨论帖 #59387 目前已有 230 条评论）和一波 **Companion/Desktop 稳定性报告**（本地服务器启动失败、IPC 错误以及残留的后台服务）所主导。在工程方面，若干范围明确的修复已落地，针对**压缩行为**、**SSE keepalive 处理**以及**会话终端 shell 解析**，同时多个 i18n/生态文档 PR 持续从贡献者处流入。

## 2. 版本发布

过去 24 小时内无新版本发布。

## 3. 热门议题

1. **[#59387](https://github.com/anomalyco/opencode/issues/59387) — 免费额度问题：请先阅读此内容，然后在此处发布你的报告**（230 条评论，👍 17）
   免费额度投诉的核心集散地：配额超限错误、永远不会到来的重置时间，以及 Desktop 版本拒绝运行。这是衡量免费额度用户情绪的唯一最高信号帖。

2. **[#61346](https://github.com/anomalyco/opencode/issues/61346) — [已复现] 压缩：当提供商失败或仅发送 SSE keepalive 时，卡在“Compacting”且无任何反馈**
   一次 `/compact` 在上游返回 `503 no eligible upstream account` 的情况下停滞了 90 多分钟且无报错。破坏了重试/通知的用户体验，需要手动中断才能恢复。

3. **[#61072](https://github.com/anomalyco/opencode/issues/61072) — Claude Agent：5 小时计划限额消耗速度约快 1.8 倍，因为 SDK 将请求标记为 `sdk-ts`**
   Claude Agent 用户可测量的成本回归——Agent SDK 入口点标签似乎影响了配额核算，使得 OpenCode 在完成相同工作时比 Claude Code 自身的 TUI 贵约 1.8 倍。

4. **[#61371](https://github.com/anomalyco/opencode/issues/61371) — core：转录缩短会一次性裁剪一次交互中的每个工具输出，导致大部分窗口未被使用**
   压缩算法缺陷：`flattenAndDropOldest` 按交互操作，因此检查点之后的一个长交互会被整体截断而非按比例处理。上下文利用效率低下，具有实际的 token 成本影响。

5. **[#50633](https://github.com/anomalyco/opencode/issues/50633) — feature(desktop)：系统托盘图标，可干净关闭后台 `opencode-cli` 服务（Windows）**（9 条评论，👍 4）
   被派生的 `opencode-cli.exe` 在 Desktop 窗口关闭后仍然存活，并持续加载过期的配置。一个长期存在的请求，持续的 👍 表明真实的桌面端摩擦。

6. **[#51051](https://github.com/anomalyco/opencode/issues/51051) — [已复现] 会话终端忽略 `shell` 配置选项（Windows 上始终使用 PowerShell）**
   文档化行为与实现不一致：agent 的 shell 工具遵循 `shell`，但会话终端只读取 `process.env.SHELL`（在 Windows 上未设置）。一个清晰、可修复的配置/实现差距。

7. **[#61363](https://github.com/anomalyco/opencode/issues/61363) — Bug：TUI 在带后台服务器全新启动时间歇性无响应**
   与后台服务器启动相关的间歇性启动挂起。结合 #61357 和 #61226，这形成了一组“全新启动不稳定”的报告。

8. **[#61357](https://github.com/anomalyco/opencode/issues/61357) — 启动本地服务器时发生错误（Desktop IPC 处理程序失败）**
   Desktop 侧 IPC 失败在本地服务器启动期间浮现。属于症状层面，但属于更广泛的 Desktop 可靠性主题的一部分。

9. **[#61232](https://github.com/anomalyco/opencode/issues/61232) — provider(opencode-go/glm-5.3-flash)：以绝对文件路径开头的用户消息总是失败，返回空响应并自动重试**
   确定性的提供商 bug：以绝对路径开头的消息永远无法到达模型。高度可复现，值得关注以期待快速修复。

10. **[#54863](https://github.com/anomalyco/opencode/issues/54863) — [功能请求]：V2 SDK 中的索引式跨会话转录搜索（SQLite FTS5）**（👍 1）
    `session.list({ search })` 仅匹配标题；按内容查找会话需要全量扫描。与构建机器人（如 Kimaki）的 SDK 消费者相关，是一项合理的长期平台改进。

## 4. 关键 PR 进展

1. **[#61376](https://github.com/anomalyco/opencode/pull/61376) [已关闭] — fix(ai)：不让 SSE keepalive 重置分块超时**
   直接解决了 #61346 中的压缩停滞问题（keepalive 部分）。防止 SSE keepalive 无限延长分块超时。

2. **[#61353](https://github.com/anomalyco/opencode/pull/61353) — fix(server)：为会话终端解析配置的 shell**
   通过将会话终端路由到配置的 `shell` 而非 `process.env.SHELL` 来修复 #51051。

3. **[#55208](https://github.com/anomalyco/opencode/pull/55208) — fix：重新挂接压缩**
   在重新运行压缩钩子之前重建缩减后的草稿，因此收缩不再恢复被请求钩子移除的检查点文本。关闭 #54916。

4. **[#61360](https://github.com/anomalyco/opencode/pull/61360) — fix(opencode)：随其所有者一起取消待处理的工具审批**
   取消 V1 会话现在会结束其工具权限等待并移除过期的审批卡片——这是针对被中断会话的真实 UX 正确性修复。

5. **[#61351](https://github.com/anomalyco/opencode/pull/61351) [已关闭] — fix(ai)：忽略在 `finish_reason` 之后到达的工具调用增量**
   增强了 OpenAI Chat 解析器对不合规路由（如 Poe）的鲁棒性，这些路由在 finish 之后仍流式发送工具调用分块——此前会导致会话因 `InvalidProviderOutput` 崩溃。

6. **[#58492](https://github.com/anomalyco/opencode/pull/58492) [已关闭] — fix(core)：绝不让压缩上限超过输出预留**
   通过相对于输出预留来限制压缩上限，修复了大窗口模型上的上限倒置问题。

7. **[#59707](https://github.com/anomalyco/opencode/pull/59707) — fix(core)：记录被跳过的插件发现**
   解决了插件目录静默丢失问题（包括 NixOS/home-manager 符号链接情况）——为反复出现的配置谜团提供诊断。

8. **[#59528](https://github.com/anomalyco/opencode/pull/59528) — fix(core)：当最后一个凭据被移除时丢弃一个 well-known 源**
   `opencode auth logout` 现在会清理 `.well-known/opencode` 源，使其不会回退到过期状态。

9. **[#55484](https://github.com/anomalyco/opencode/pull/55484) — feat(core)：添加 scrape.do 网页搜索提供商**
   添加了一个新的网页搜索后端——证明搜索提供商接口在持续扩展。

10. **[#57358](https://github.com/anomalyco/opencode/pull/57358) — fix(app)：将本地会话保持在选定目录内**
    防止本地会话逃逸出选定目录（引用 #51191，解决 #51992）。对多项目工作流很重要。

## 5. 热门讨论

未提供专门的 Discussion 数据；本节省略。

## 6. 功能请求趋势

- **本地化 / i18n**：多个 PR（[#61361](https://github.com/anomalyco/opencode/pull/61361)、[#61366](https://github.com/anomalyco/opencode/pull/61366)）针对俄语 UI 和 TUI 本地化，延续长期引用（#37216、#50164）。
- **Desktop 生命周期与服务管理**：系统托盘 + 干净的后台服务关闭（[#50633](https://github.com/anomalyco/opencode/issues/50633)）；GUI 插件在 Web 应用中加载（[#60976](https://github.com/anomalyco/opencode/issues/60976)）。
- **上下文 / 压缩透明度**：每轮上下文漂移检查器（[#58459](https://github.com/anomalyco/opencode/issues/58459)）以及每会话 Go 配额持久化（[#59653](https://github.com/anomalyco/opencode/issues/59653)）。
- **SDK 可观测性/搜索**：SQLite FTS5 索引式转录搜索（[#54863](https://github.com/anomalyco/opencode/issues/54863)）。
- **会话管理 UX**：从 TUI 删除会话（[#26056](https://github.com/anomalyco/opencode/issues/26056)）——一个长期存在、低摩擦的请求。
- **生态扩展**：新的 Telegram 和 QQ 机器人频道插件（[#59301](https://github.com/anomalyco/opencode/pull/59301)、[#61355](https://github.com/anomalyco/opencode/pull/61355)）。

## 7. 开发者痛点

- **免费额度配额核算是头号投诉**——重置时间不工作、首次请求即提示“已用完今日免费消息”，以及 Desktop 拒绝为免费用户启动。230 条评论的帖子（#59387）加上多个重复项（#61222、#61375）表明这是一个真实的 bug 以及支持积压问题。
- **压缩是反复出现的问题线**：提供商失败时无反馈停滞（#61346）、低效的按交互截断（#61371），以及大窗口模型上的上限倒置（#58492）。还有关于“Big Pickle”上下文效率回归的报告（#61352）。
- **Desktop/TUI 启动可靠性**：残留的后台服务、IPC 失败以及间歇性启动挂起（#50633、#61357、#61226、#61363、#60854）产生了一组“全新启动不稳定”的报告。
- **隐藏的成本回归**：Claude Agent 配额消耗速度约快 1.8 倍（#61072），侵蚀了对提供商集成核算的信任。
- **配置选项被静默忽略**：`shell`（#51051）和插件发现符号链接（#59707）——看似有效但未被遵循的配置。
- **提供商边缘情况崩溃**：路径前缀消息返回空响应（#61232）、finish 后的工具增量（#61351），以及升级后的作用域错误（#61342）指向提供商层的脆弱性。

*注：评论仅基于所提供的议题/PR 元数据（标题、摘要、计数）；未进行外部验证。*

</details>

<details>
<summary><strong>Pi</strong> — <a href="https://github.com/earendil-works/pi">earendil-works/pi</a></summary>

# Pi 社区文摘 — 2026-11-03

## 1. 今日亮点

Pi v1.5.0 发布，全新启动时可显示扩展变更日志。与此同时，0.99.x 中围绕终端颜色查询回复（mintty/ConPTY）的回归问题仍是本周期最活跃的议题，另有一批 schema 解析、上下文预算和 RPC 生命周期修复通过机器人提交的 PR 落地。

## 2. 版本发布

**v1.5.0** — 新功能
- **扩展更新变更日志**：更新已安装的扩展包后，下次全新启动会显示变更内容。包作者可选择变更日志路径，`collapseChangelog` 可精简通知。
- 链接：https://github.com/earendil-works/pi/releases

## 3. 热门议题

1. **#10256 [OPEN] [bug, inprogress] 终端颜色查询回复泄漏进提示符；BEL 打开外部编辑器（mintty/ConPTY）** — 21 条评论，👍4。0.87.1（正常）与 0.99.x 之间的回归；启动时向提示符输出原始颜色查询转义序列。对 Windows mintty 用户影响较大。https://github.com/earendil-works/pi/issues/10256
2. **#10497 [CLOSED] [bug] OpenRouter 错误：400** — 15 条评论。扩展注入文件内容时上下文长度超限；指向与 OpenRouter 更严格的路由前检查之间的 token 预算核算缺口。https://github.com/earendil-works/pi/issues/10497
3. **#10605 [OPEN] [bug] ChatGPT/OpenAI OAuth 403** — 12 条评论。“ChatGPT user is not eligible for subscription sharing” 即使重新登录后仍阻止 Plus 层级 OAuth 用户。https://github.com/earendil-works/pi/issues/10605
4. **#10956 [OPEN] cacheWarming “idle” 在 turn_end + 自定义/压缩消息后静默停止** — 11 条评论，👍2。空闲缓存预热从不触发（`Inactive (conversation context changed)`）；影响长会话的成本/延迟。https://github.com/earendil-works/pi/issues/10956
5. **#10470 [OPEN] RPC new_session/switch_session/fork/clone 重复发出 session_start** — 8 条评论。重复的生命周期事件破坏 RPC 模式下的扩展状态机。https://github.com/earendil-works/pi/issues/10470
6. **#11021 [OPEN] ChatGPT 登录对 gpt-6.1-sol 使用 272K 上下文，尽管目录标明 500K** — 7 条评论，👍7。上下文预算偏小导致过早压缩；社区强烈认同。https://github.com/earendil-works/pi/issues/11021
7. **#11599 [OPEN] RPC：异步 ctx.shutdown() 无法退出空闲进程** — 5 条评论。无头/RPC 嵌入方在异步关闭时遇到进程挂起。https://github.com/earendil-works/pi/issues/11599
8. **#9276 [OPEN] [bug, inprogress] 带上下文行的 grep 工具可能导致 OOM** — 4 条评论。为获取上下文行而读取整个匹配文件，在无头 SDK 使用中导致堆内存耗尽。https://github.com/earendil-works/pi/issues/9276
9. **#11697 [OPEN] [fix-available] 相对 `$id` 被解析两次破坏工具参数校验** — 3 条评论。当 schema 使用相对 `$id` + 本地 `$ref` 时，有效的工具参数被拒绝。https://github.com/earendil-works/pi/issues/11697
10. **#11699 [OPEN] 移除 codemode 中的工具截断** — 1 条评论。请求让 codemode 在不截断的情况下链式调用工具，因为截断破坏了其核心价值。https://github.com/earendil-works/pi/issues/11699

## 4. 关键 PR 进展

1. **#11726 [OPEN] fix(schema): 解析引用时仅应用一次相对根 `$id`** — 修复相对根 `$id` 的双重解析问题，该问题会破坏本地 `$ref`（与议题 #11697 配套）。https://github.com/earendil-works/pi/pull/11726
2. **#11725 [CLOSED] fix(ai): 退避后移除 Codex SSE 重试中止监听器** — 防止 Codex Responses 提供方每次重试累积中止监听器泄漏。https://github.com/earendil-works/pi/pull/11725
3. **#11724 [CLOSED] fix(mcp): 服务器退出后 stdio 关闭时清理服务器子进程** — 确保即使父进程已退出，MCP 进程组中孤立的子进程也会被杀死。https://github.com/earendil-works/pi/pull/11724
4. **#11705 [OPEN] fix(coding-agent): 从 ripgrep 获取 grep 上下文而非读取匹配文件** — 直接修复 #9276 中的 OOM；内存现在随输出规模而非文件大小增长。https://github.com/earendil-works/pi/pull/11705
5. **#11545 [OPEN] Anthropic 消息线程** — 使用 Anthropic 服务端消息线程 beta，在启用缓存时仅发送新消息；不匹配时仍回退到完整请求。https://github.com/earendil-works/pi/pull/11545
6. **#11712 [CLOSED] [inprogress] fix(ai): 钳制 max tokens 时为 OpenRouter 输入多计留出余量** — 解决 OpenRouter 更严格的 tokenizer（最多约 12% 多计）导致接近窗口上限的模型返回 400 的问题。https://github.com/earendil-works/pi/pull/11712
7. **#11709 [CLOSED] fix(coding-agent): npm/pnpm/yarn/bun 自更新后报告已安装版本** — 避免在包管理器实际重装了旧版本时声称更新成功。https://github.com/earendil-works/pi/pull/11709
8. **#11707 [CLOSED] feat(coding-agent): 在 pi update 中列出已更新包及版本变更** — 让 `pi update` 输出显示哪些扩展发生了变化及变为何版本。https://github.com/earendil-works/pi/pull/11707
9. **#11629 [CLOSED] feat(coding-agent): 添加 PI_SELF_UPDATE_COMMAND 和 selfUpdateCommand** — 让包装器管理的安装（mise、nub、Nix）定义 `pi update` 应如何更新 Pi。https://github.com/earendil-works/pi/pull/11629
10. **#11686 [OPEN] feat: 在 pkg.earendil.com 上发布版本标签** — 修复发布流水线，使带标签的发布提交真正被构建（v1.2.0–v1.4.0 无构建）。https://github.com/earendil-works/pi/pull/11686

## 5. 热门讨论

**想法**
- **#4444 支持 Agent Client Protocol (ACP)** — 5 条评论，👍47（本周期互动量最高）。提议用标准化的 agent↔编辑器协议取代 pi 专有 RPC，使每个编辑器无需定制客户端。https://github.com/earendil-works/pi/discussions/4444

**问答**
- **#11404 官方 Pi 代码 agent 沙箱由 Docker 提供？** — 2 条评论，👍2。用户在 Docker Hub 上发现 `sbx/pi`、`sbx/pi-mixin`、`sbx/pi-image` 和 `sbx/pi-kit` 镜像，询问它们是否官方。https://github.com/earendil-works/pi/discussions/11404

**展示与分享**
- **#11722 OnlyClaws Android — 给 Pi 一只手机爪（动态壁纸、悬浮层、通过 `js` 的 TTS）** — 一个轻量 Android 宿主，运行 agent 推送的 JS（Rhino + A2UI + 透明 WebView 悬浮层 + TTS），呼应低成本设备 agent 模式。https://github.com/earendil-works/pi/discussions/11722

## 6. 功能请求趋势

- **标准化编辑器集成**：ACP 支持（#4444，👍47）是最明确的信号，表明嵌入方想要协议而非定制 RPC 方言。
- **扩展机制的可观测性/确定性**：`--print-skills` / `pi skills`（#11700）、为嵌入方导出 `VIRTUAL_MODULES`（#11727），以及设置级 skill 修剪控制（#11701），都要求可检查、确定性的行为而非模型中介的输出。
- **扩展能力扩展**：从事件钩子调用已注册/MCP 工具（#10264）、感知后台任务的状态（#11702），以及禁用 codemode 中的内置截断（#11699）。
- **更新/安装体验**：包级变更日志（已在 v1.5.0 发布）、逐包版本报告（#11707）、更新后准确版本（#11709），以及包装器定义的自更新（#11629）。
- **会话管理**：在 `/resume` 中隐藏子会话（#9847）和跨重启持久性（#11635）。

## 7. 开发者痛点

- **上下文/token 核算不匹配**：OpenRouter 路由前 tokenizer 多计（#10497、#11712）和 ChatGPT OAuth 报告 272K 而非 500K（#11021）都会导致 400 或过早压缩。
- **无头/RPC 模式的粗糙边缘**：重复的 `session_start` 事件（#10470）、异步 `ctx.shutdown()` 挂起（#11599），以及扩展在 `-p`/`--mode json` 中静默丢失轮次（#11011）。
- **工具的内存安全**：带上下文行的 grep 读取整个文件，在 SDK 使用中造成生产环境 OOM（#9276，PR #11705）。
- **静默失败**：长度恢复以退出码 0 丢弃回复（#11012）、纯图像 steer 用空文本块污染后续每个请求（#11728），以及扩展注册的模型使 `calculateCost` 崩溃（#10209）。
- **更新可靠性信任**：过期的注册表元数据使 `pi update` 声称成功却重装旧版本（#11707、#11709），以及包装器管理的安装不支持自更新路径。
- **终端/平台兼容性**：0.99.x mintty/ConPTY 转义序列回归（#10256）和 WSL 剪贴板历史缺口（#6003）仍是反复出现的 Windows 相关摩擦。

</details>

<details>
<summary><strong>Qwen Code</strong> — <a href="https://github.com/QwenLM/qwen-code">QwenLM/qwen-code</a></summary>

# Qwen Code 社区简报 — 2026-11-03

## 1. 今日亮点

项目发布了 **v0.26.6**，同时发布了 TypeScript SDK **v0.1.27**，主要亮点是为受信任工作区新增的 Web Shell 侧边栏活动视图，以及一批 Anthropic 提供商的超时和传输修复。CI 健康状况仍是主要的运营关注点：机器人生成的长期失败 issue（#14724 有 161 条评论，#13078 有 40 条）仍在累积，并且一个新的 P1 报告称机器人工作流爆发正在耗尽 `GITHUB_TOKEN` 速率限制，并破坏无关的 CI 任务。与此同时，会话状态的持久性（SIGHUP 丢失、未记录的工具结果）以及跨 Bedrock/OpenRouter/Ollama 的模型 ID 规范化 bug 成为活跃主题。

---

## 2. 发布

**CLI v0.26.6**
- **新增：** 为受信任工作区对话提供 Web Shell 侧边栏活动视图，展示审批/输入/错误状态和未读计数。
- 无已知破坏性变更。
- 同时发布了每夜构建 `v0.26.5-nightly.20261102.0441faff21`。

**SDK TypeScript v0.1.27**
- 捆绑 CLI **0.26.6**（与 SDK 从同一分支/ref 构建）。

---

## 3. 热门 Issue

1. **[#14724](https://github.com/QwenLM/qwen-code/issues/14724) — 主 CI 失败：Managed Agent 门禁，未识别到测试**（161 条评论，自 2026-10-19 起开放）。运行时间最长的机器人提交的 CI 失败线程；未识别到测试表明这是一个不稳定或基础设施层面的门禁，而非代码回归。高评论量表明反复重新触发但未解决。

2. **[#13078](https://github.com/QwenLM/qwen-code/issues/13078) — 每日依赖 CVE 审计失败**（40 条评论）。一个已存在一个月、仍未关闭的安全卫生失败。持续的审计失败会侵蚀对供应链信号的信任，可能受阻于 npm audit 端点或一个真实的高危发现。

3. **[#16169](https://github.com/QwenLM/qwen-code/issues/16169) — 排队消息在 SIGHUP 时静默丢失**（P2）。在工具运行时输入的消息会保留在队列中（页脚显示 `⏳ 1 queued`），然后在终端关闭时完全消失——既不在恢复的对话中，也不在任何文件中。一个明确的数据丢失 UX bug，受到社区新鲜关注。

4. **[#15799](https://github.com/QwenLM/qwen-code/issues/15799) — 已完成的工具调用被报告为“未记录……视为失败并重试”**（P1）。在会话突然结束时的进行中调用在恢复时被错误分类。与 #16169 同族：**中断下的会话持久性**是一个反复出现的正确性缺口。

5. **[#16144](https://github.com/QwenLM/qwen-code/issues/16144) — Web Shell GitHub/GitLab 频道默认对任何人开放**（P1，安全）。从 Web Shell 创建的频道默认使用 `Open` 组策略，与文档声称组访问默认禁用的说法矛盾——一个与安全相关的默认值不匹配。

6. **[#16188](https://github.com/QwenLM/qwen-code/issues/16188) — 机器人工作流爆发耗尽 GITHUB_TOKEN 速率限制**（P1）。PR #16182 上的四个 CI 任务因 autofix/triage/review 机器人爆发而失败，返回 HTTP 403。直接影响贡献者速度。

7. **[#16171](https://github.com/QwenLM/qwen-code/issues/16171) — Web Shell 会话打开非常慢：实时回放保留了编辑工具结果中的完整文件内容**（P1，已关闭）。守护进程资源正常；变慢是回放负载膨胀所致。很快关闭，但凸显了 Web Shell 会话扩展中的成长烦恼。

8. **[#15165](https://github.com/QwenLM/qwen-code/issues/15165) — Shell 安全解析器在 Node 24.16.0 上触发约 1.6 GiB 的瞬时 WASM 分配**（P2，性能）。在 V8 优化捆绑的 Bash WASM 解析器期间测得原生内存峰值——对受限环境而言是一个严重的内存占用问题。

9. **[#16157](https://github.com/QwenLM/qwen-code/issues/16157) / [#16158](https://github.com/QwenLM/qwen-code/issues/16158) / [#16159](https://github.com/QwenLM/qwen-code/issues/16159) — 模型 ID 丢失上下文/输出限制**（Bedrock、OpenRouter `:exacto`、Ollama HF 量化标签）。来自同一报告者的协调三连：规范化将 ID 缩减为垃圾值，静默回退到 200K/32K 默认值。三个提供商中同一根因类别。

10. **[#16127](https://github.com/QwenLM/qwen-code/issues/16127) — VS Code Companion 0.26.5 在 Electron-as-Node 下用 Sharp 解码图像时发生 SIGSEGV**（P1，Linux）。图像解码崩溃是 IDE 用户的硬性阻碍；与 #16063（生成的 UI MCP 服务器作为 Electron GUI 而非 Node 启动）相呼应，表明存在更广泛的 **Electron 运行时模式**问题集群。

---

## 4. 关键 PR 进展

1. **[#16177](https://github.com/QwenLM/qwen-code/pull/16177) — fix(core)：将监控和工作流通知归属到注册轮次。** 修复后台结果作为新轮次到达而非被包含在活动轮次的下一个模型请求中的问题——这是 Web Shell issue #16119 的服务端对应修复。

2. **[#16193](https://github.com/QwenLM/qwen-code/pull/16193) — fix(core)：遵循 Anthropic 传输超时配置**（已关闭/已合并）。将选定的 Undici 调度器传递到自定义 fetch 中；直接解决 #16187 中尽管配置了更长超时却仍为 300s 超时的问题。

3. **[#16199](https://github.com/QwenLM/qwen-code/pull/16199) — fix(serve)：处理远程访问审查发现 R1-6/7/9/11。** 是 #15887 的后续，后者合并时留有 17 个未解决的审查线程；此 PR 落地了四个经验证的功能/安全修复。

4. **[#16023](https://github.com/QwenLM/qwen-code/pull/16023) — feat(managed-agent)：跨会话共享常驻外部 Workspace。** 在 Sessions/Turns 之间复用 Workspace 范围的 worker 绑定，而非每周期拆除——对托管代理而言是一项有意义的效率变更。

5. **[#16201](https://github.com/QwenLM/qwen-code/pull/16201) — feat(managed-agent)：为受治理的 SQL 目录强制执行行完整性。** 提议对 39 表常驻 Workspace 栈进行治理，将 73 个 CHECK 谓词移入完整的行级 Java 验证。

6. **[#16064](https://github.com/QwenLM/qwen-code/pull/16064) — fix(cli)：在 Electron 下以 Node 模式运行捆绑的 generated-ui 服务器。** 为 stdio spawn 路径固定 `ELECTRON_RUN_AS_NODE=1`——这是 #16063 类 bug 的修复。

7. **[#16117](https://github.com/QwenLM/qwen-code/pull/16117) — feat(channels)：将 GitHub 事件投递到绑定 PR 的会话。** 将 PR 事件（创建/审查/验证绑定）作为提示路由到所属会话——迈向事件驱动代理工作流的一步。

8. **[#16149](https://github.com/QwenLM/qwen-code/pull/16149) — feat(web-shell)：从频道编辑器编辑 GitHub 仓库设置。** 在 UI 中暴露账户允许列表、无 @提及应答及相关策略，而非手动编辑 `settings.json`——与 #16144 安全默认值讨论相关。

9. **[#16105](https://github.com/QwenLM/qwen-code/pull/16105) — feat(serve)：可配置的 Conversations 工作目录根。** 为 `qwen serve` 和 `qwen daemon start` 添加 `--conversations-dir` / `QWEN_CONVERSATIONS_DIR`，并明确优先于环境变量。

10. **[#16190](https://github.com/QwenLM/qwen-code/pull/16190)（已关闭）/ [#16192](https://github.com/QwenLM/qwen-code/pull/16192)（已关闭）— 统一代理循环推广。** ACP cron/后台通知轮次现在运行在共享循环上（PR 2c-4，最后一种 ACP 轮次类型），并且每夜 E2E 通道新增了一个 `QWEN_CODE_ENABLE_UNIFIED_AGENT_LOOP=1` 分支——表明默认切换正在临近（#15327）。

---

## 5. 热门讨论

本数据集未提供 Discussion 数据；本节省略。

---

## 6. 功能需求趋势

- **细粒度内容引用以提升 token 效率** — [#16173](https://github.com/QwenLM/qwen-code/issues/16173) 要求模型引用文件/工具输出的切片而非复制它们，针对 `read > write > typo > re-write` 循环。契合 `roadmap/context-performance` 主题。
- **托管/隐私敏感安装的配置控制** — [#16006](https://github.com/QwenLM/qwen-code/issues/16006)（已关闭）请求从设置中禁用每日 models.dev 目录刷新，尊重隐私退出选项。
- **桌面分发广度** — [#16034](https://github.com/QwenLM/qwen-code/issues/16034)（已关闭）请求为 Fedora/RHEL 提供 rpm 构件（目前仅 AppImage，无法安装），并附带了内联补丁。
- **Web Shell 作为配置界面** — 多个 PR（#16149）和 issue 指向将频道/仓库策略从 `settings.json` 移到 UI 中。
- **后台自动化语义** — 通知归属（#16177/#16119）和 PR 绑定事件投递（#16117）表明对能在轮次中对外部事件做出反应的代理有需求。

---

## 7. 开发者痛点

1. **中断下的会话状态持久性** — 最强的反复出现集群：排队消息在 SIGHUP 时丢失（#16169），已完成的调用在突然结束后被误报为失败（#15799），以及未记录结果的消息强制重试。用户丢失工作，并对恢复语义失去信任。
2. **CI 信号噪声和自伤式中断** — 机器人提交的巨型线程（#14724、#13078、#15173、#12714）加上 token 速率限制耗尽（#16188）意味着 CI 失败越来越多地由基础设施/机器人驱动，淹没真实回归并阻塞无关 PR。
3. **模型 ID 规范化静默出错** — 同一报告者在一天内提交了 Bedrock（#16157）、OpenRouter `:exacto`（#16158）和 Ollama HF 量化标签（#16159）变体；全部静默回退到默认 200K/32K 限制，导致错误的上下文预算且无任何警告。
4. **VS Code 伴侣中的 Electron 运行时模式 bug** — 生成的 UI MCP 服务器作为 GUI 而非 Node 启动（#16063）以及 Electron-as-Node 下 Sharp SIGSEGV（#16127）破坏了 Linux 上的核心 IDE 功能。
5. **Shell 安全层的内存/性能占用** — 约 1.6 GiB 的瞬时 WASM 分配（#15165）和 Managed Runtime worker 中每个沙箱约 166 MiB 的开销（#16147，已关闭）表明安全工具承载了真实成本。
6. **安全默认值与文档背离** — Web Shell 频道对任何人开放（#16144），而文档声称组访问默认关闭；安全敏感的审查者正在积极审计权限路径（另见已关闭的 #13106，关于 `cd` 重定向目标绕过 Write 拒绝检查）。

</details>

---
*本日报由 [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar) 自动生成。*