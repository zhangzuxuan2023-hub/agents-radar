# AI CLI 工具社区动态日报 2026-11-12

> 生成时间: 2026-11-11 23:28 UTC | 覆盖工具: 7 个

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

⚠️ 横向对比生成失败。

---

## 各工具详细报告

<details>
<summary><strong>Claude Code</strong> — <a href="https://github.com/anthropics/claude-code">anthropics/claude-code</a></summary>

## Claude Code Skills 社区热点

> 数据来源: [anthropics/skills](https://github.com/anthropics/skills)

# Claude Code Skills — 社区亮点报告
*数据截至 2026-11-12 · 来源：github.com/anthropics/skills*

---

## 1. 热门 Skill 排名

> 注：PR 数据集中 `Comments: undefined`，因此以下排名依据 Issue 级讨论量及对关联 Issue/PR 的交叉引用，并结合近期活跃度排序。所列 PR 均为 **OPEN** 状态。

| # | Skill / PR | 功能 | 讨论亮点 | 状态 |
|---|---|---|---|---|
| 1 | **[mcp-builder: mcp>=2 streamable HTTP client + custom headers](https://github.com/anthropics/skills/pull/1742)** — *Kuldeeep18* | 修复 `mcp>=2.0.0` 下的 MCP 连接构建器（`streamablehttp_client` → `streamable_http_client`，通过 `create_mcp_http_client` 传递 headers） | 修复速度最快；解决 #1668 中 SDK 重命名导致的破坏性变更；更新于 2026-10-08 | OPEN |
| 2 | **[skill-creator: isolate trigger evals + Windows/runtime failure handling](https://github.com/anthropics/skills/pull/1298)** — *MartinCajiao* | 让触发评估打分更可靠：按 worker 进行命令探测、Windows 下对管道使用 `select()`、隔离无关工具的失败 | 直接回应 #1352（并行 worker 假阴性）和 #556（0% 触发率） | OPEN |
| 3 | **[Add notion-spec-to-implementation + quantitative-resume-auditor](https://github.com/anthropics/skills/pull/1245)** — *mrdesouzaphd-cmyk* | 将规格说明拆解为 Notion 任务并附验收标准；简历评分/审计 | 双 Skill 提交；仍在活跃（更新于 2026-10-15） | OPEN |
| 4 | **[webapp-testing: eliminate `shell=True` in with_server.py](https://github.com/anthropics/skills/pull/1980)** — *Pcmhacker-piro* | 用参数列表执行替代 shell 启动，防止 CWE-78 命令注入 | 测试框架的安全加固；更新于 2026-11-01 | OPEN |
| 5 | **[docx: report LibreOffice timeout as error + verify no revision marks](https://github.com/anthropics/skills/pull/1792)** — *TINGyu123644* | `accept_changes.py` 在 `soffice` 超时时不再误报成功；校验 `w:ins`/`w:del` 已移除 | 旗舰文档 Skill 的正确性修复 | OPEN |
| 6 | **[proofcore-contract-auditor (smart-contract notarization)](https://github.com/anthropics/skills/pull/1771)** — *ProofCore-Protocol* | Solidity/Rust 合约静态分析 + 锚定于 TON 的 Merkle 证明 | 新颖的 Web3 领域 Skill；零存储公证 | OPEN |
| 7 | **[pyxel: retro game development skill](https://github.com/anthropics/skills/pull/525)** — *kitao* | 指导 Pyxel 游戏创作、无头输入驱动运行、帧检查、状态校验 | 长期运行的 PR（自 2026 年 3 月起），仍于 2026-11-01 更新——持续受到关注 | OPEN |
| 8 | **[xlsx: worksheet-scoped defined names in external_links_at_risk](https://github.com/anthropics/skills/pull/2019)** — *reginaldalfret* | 修复 `recalc.py` 中通过工作表作用域别名触达外部链接时的假阴性 | 修复 #2013；更新于 2026-11-03 | OPEN |

---

## 2. 社区需求趋势

依据 Issue 评论量及反复出现的信号提炼：

- **安全与信任边界** — [#492（46 条评论）](https://github.com/anthropics/skills/issues/492)：以 `anthropic/` 命名空间发布的社区 Skill 冒充官方 Skill；用户可能过度授予权限。*这是数据集中讨论量最高的单一 Issue。* 并由 [#1980](https://github.com/anthropics/skills/pull/1980)（命令注入）等 PR 进一步印证。
- **Skill 评估可靠性** — [#556（13）](https://github.com/anthropics/skills/issues/556)：`run_eval.py` 下 0% 触发率；[#1352（6）](https://github.com/anthropics/skills/issues/1352)：并行 worker 的 UUID 交叉匹配问题。需求：在优化 Skill 之前，先有*可信的触发率测量*。
- **组织级分发与共享** — [#228（17）](https://github.com/anthropics/skills/issues/228)：组织级 Skill 库与分享链接，而非手动上传 `.skill` 文件。相关：[#189（6）](https://github.com/anthropics/skills/issues/189)：重叠插件导致的重复 Skill。
- **上下文效率** — [#1487（5）](https://github.com/anthropics/skills/issues/1487)：`claude-api` 在一次工具调用中急切削入约 156k tokens；[#202（8，已关闭）](https://github.com/anthropics/skills/issues/202)：skill-creator 应是操作性的，而非教程式散文。
- **质量门禁 / 智能体推理流水线** — [#1385（7）](https://github.com/anthropics/skills/issues/1385)：任务前校准 → 对抗性审查 → 交付验证。
- **压缩式智能体记忆** — [#1329（10）](https://github.com/anthropics/skills/issues/1329)：用符号记法压缩长时间运行的智能体状态。
- **智能体系统治理** — [#412（6，已关闭）](https://github.com/anthropics/skills/issues/412)：策略执行、威胁检测、审计追踪。

**新兴方向：** 文档 Skill 快照卫生（[#2108](https://github.com/anthropics/skills/pull/2108)）、frontmatter/description 质量以指导何时使用（[#2064](https://github.com/anthropics/skills/issues/2064)）、docx/xlsx 正确性修复（[#2018](https://github.com/anthropics/skills/issues/2018)、[#1868](https://github.com/anthropics/skills/issues/1868)）。

---

## 3. 高潜力待合并 Skill

评论活跃、尚未合并但很可能很快落地的 PR：

1. **[#1742 — mcp-builder mcp>=2 修复](https://github.com/anthropics/skills/pull/1742)** — 解决硬性破坏性导入问题；合并概率最高。
2. **[#1298 — skill-creator 触发评估隔离](https://github.com/anthropics/skills/pull/1298)** — 为仓库最活跃的 bug 集群（#556、#1352）画上句号。
3. **[#1980 — webapp-testing 移除 `shell=True`](https://github.com/anthropics/skills/pull/1980)** — 影响面小的安全修复。
4. **[#2019 — xlsx 外部链接别名解析](https://github.com/anthropics/skills/pull/2019)** — 范围清晰、有关联 Issue 的修复（#2013）。
5. **[#1792 — docx 超时/校验](https://github.com/anthropics/skills/pull/1792)** — 描述完善的正确性修复。
6. **[#1977 / #1976 / #1975 — gerardrecinto 的杂项修复](https://github.com/anthropics/skills/pull/1977)**（wrapAround、textarea/select 发现、`--cols` 校验）— 关联至 Issue，小而易于审查。
7. **[#1681 — skill-creator 独立执行 `package_skill.py`](https://github.com/anthropics/skills/pull/1681)** — 修复 `ModuleNotFoundError` 并更新 CLI 路径。
8. **[#525 — pyxel 复古游戏 Skill](https://github.com/anthropics/skills/pull/525)** — 生命周期最长的新 Skill PR，持续更新；领域新颖性强。

*观察名单（活跃度较低、新颖度较高）：* [#1771 proofcore-contract-auditor](https://github.com/anthropics/skills/pull/1771)、[#1615 scnet-hpc](https://github.com/anthropics/skills/pull/1615)、[#1703 md2video-audio](https://github.com/anthropics/skills/pull/1703)。

---

## 4. Skill 生态洞察

**社区最集中的需求是信任与可靠性基础设施——安全、命名空间正确、具备可验证触发评估的 Skill——而非更多 Skill：对评估流水线和信任边界的修复，在讨论量上超过每一个全新 Skill 提案。**

---

# Claude Code 社区摘要 — 2026-11-12

## 1. 今日亮点
过去 24 小时内没有新版本发布，但 issue 追踪器经历了一轮大规模维护清理：一大批陈旧 issue（许多来自 8 月至 9 月）被关闭，同时围绕 v2.1.322 模型选择器和 VS Code 插件同步涌现出一组新的 OPEN bug。两个高信号的新回归值得立即分诊——VS Code 中静默的遥测门控插件同步失败，以及 `/model` 选择器遗漏 Fable 5.2。

## 2. 版本发布
过去 24 小时无新版本发布。

## 3. 热门 Issue

1. **[#45810](https://github.com/anthropics/claude-code/issues/45810) — 版本已过期但市场更新按钮被禁用**（CLOSED，18 条评论，👍8）
   今日评论最多的 issue。插件市场中的一个 UI 阻塞性 bug：无论版本状态如何，更新按钮都呈灰色不可用。以重复/陈旧为由关闭，暗示存在更早的权威报告——但 8 个赞表明用户对插件更新的实际摩擦。

2. **[#108955](https://github.com/anthropics/claude-code/issues/108955) — `/model` 选择器遗漏 Fable 5.2，而斜杠命令可用**（OPEN，👍4）
   v2.1.322 上的 Max 套餐用户可以直接调用 `claude-fable-5-2`，但选择器只显示 5.1。典型的 UI/模型注册表不同步——值得关注，因为它影响高级用户的模型发现。

3. **[#108745](https://github.com/anthropics/claude-code/issues/108745) — VS Code 遥测关闭后静默禁用 claude.ai 插件同步**（OPEN，回归）
   自扩展 2.1.322 起，设置 `telemetry.telemetryLevel: off` 会静默终止组织管理的插件同步——MCP 服务器、agent 和 skill 无声消失，没有任何 UI 提示。这是一个严重的企业回归：注重隐私的组织在不知情的情况下失去其受管工具链。

4. **[#108839](https://github.com/anthropics/claude-code/issues/108839) — `claude --resume` 附加到 Remote Control 服务器而非会话**（OPEN，Linux）
   由 `claude --bg remote-control` 生成的会话注册的是服务器的 job ID，因此 resume 和 attach 都指向服务器。破坏了远程控制用户预期的后台会话工作流。

5. **[#109036](https://github.com/anthropics/claude-code/issues/109036) — Chrome 中的 Claude 桥接在空闲后需要会话中途重新认证**（OPEN，Linux）
   桌面端 + CLI 共享同一个 Chrome 配置文件时，Chrome 空闲或关闭后会丢失桥接授权，表现为虚假的登出。影响依赖浏览器上下文的长时间桌面会话。

6. **[#109141](https://github.com/anthropics/claude-code/issues/109141) — 让 statusLine 与模式徽章共享一行**（OPEN，增强）
   自定义状态行总是独占一行页脚，导致两行页脚。虽小但具体的 TUI 打磨请求，诉求明确：可配置的徽章隐藏或行共享。

7. **[#81814](https://github.com/anthropics/claude-code/issues/81814) — VS Code 聊天输入框原生拼写检查**（OPEN，👍11）
   本批次中得票最高的开放增强。在大多数编辑器中微不足道，但此处缺失；11 个赞表明这是扩展用户的日常小痛点。

8. **[#81351](https://github.com/anthropics/claude-code/issues/81351) — Sonnet 5 生成了约 1000 个 agent，消耗 1300 万 token**（CLOSED，陈旧）
   一份戏剧性的 agent 失控成本报告。以陈旧为由关闭而非修复，但这是一个反复出现的主题（见下方 #73307 和 #92602），值得一等公民级的护栏。

9. **[#73307](https://github.com/anthropics/claude-code/issues/73307) — 请求：为失控 agent 提供 `doom_loop` 式权限门控**（CLOSED，陈旧）
   提议一个受 OpenCode 启发的权限键，以中止 subagent 递归和工具调用风暴。以陈旧关闭，但与 #81351 配对来看，它是对缺失安全原语最清晰的表述。

10. **[#92573](https://github.com/anthropics/claude-code/issues/92573) — Worktree 槽位在会话中途被复用/重置，约 5,900 个文件丢失**（CLOSED，数据丢失，有复现）
    本批次中最严重的已关闭 issue：`.claude/worktrees/*` 池复用导致会话活跃期间大量文件被删除。即使以陈旧关闭，这也是一个数据丢失类 bug，值得追踪以防复发。

## 4. 关键 PR 进展

窗口期内仅有一个 PR 更新：

1. **[#26230](https://github.com/anthropics/claude-code/pull/26230) — Feature/path validation hooks**（CLOSED）
   阻止 agent 工具调用中的相对路径以强化沙箱。经过漫长（2 月 → 11 月）的生命周期后关闭。如果该方案以其他形式落地，它与今日其他地方提出的沙箱和 agent 隔离 issue（#92573、#92609）直接相关。无其他 PR 可报告。

## 5. 热门讨论
本窗口期未提供讨论数据。

## 6. 功能请求趋势

- **Agent 护栏与成本安全**：`doom_loop` 式权限门控（#73307）、失控 agent 上限（#81351）和循环检测（#92602）。社区希望获得针对无界 subagent 递归和 token 消耗的一等公民保护。
- **插件/市场可靠性**：更新按钮交互性（#45810）和静默同步失败（#90329、#108745）表明需要可见的同步状态和诚实的错误报告。
- **沙箱粒度**：为 JVM 项目解耦文件系统隔离与网络隔离的请求（#92609）和路径验证钩子（PR #26230）显示对更细粒度、默认安全的沙箱的需求。
- **TUI/IDE 人体工学**：拼写检查（#81814）和 statusLine 行共享（#109141）——小而高频的 UX 小痛点。
- **上下文可观测性**：关于 VS Code 中 `/context` 行为的文档请求（#92607）凸显了对透明上下文/用量报告的更广泛诉求。

## 7. 开发者痛点

- **静默失败是主导性挫败来源。** 插件同步（#90329、#108745）、artifact 评论（#92618）和流式转录丢失（#90802）在 UI 上都“成功”完成，而底层什么都没发生。用户反复要求可见的错误状态。
- **Agent 失控与成本。** 多份报告称 agent 循环、过度生成或基于错误假设行动（#81351、#73307、#92602、#92575）——最高严重度的可靠性主题。
- **Worktree/隔离数据丢失。** Worktree 池复用（#92573）和基于 origin/HEAD 的基线（#77671）侵蚀了对并行 sub-agent 工作流的信任。
- **认证/会话状态混乱。** Chrome 桥接重新认证（#109036）和 Remote Control resume 附加到错误目标（#108839）使会话身份不可预测。
- **未解决即陈旧关闭。** 今日关闭的很大一部分是陈旧标记，包括数据丢失和成本 bug。这带来重复报告的风险，并向开发者发出信号：高影响 issue 未被分诊。

---
*注：追踪器显示“Total: 50 items; showing top 30”——本摘要反映可见样本。本窗口期无版本发布或讨论数据可用。*

</details>

<details>
<summary><strong>OpenAI Codex</strong> — <a href="https://github.com/openai/codex">openai/codex</a></summary>

# OpenAI Codex 社区文摘 — 2026-11-12

## 1. 今日亮点

Codex 发布了一个稳定补丁（`rust-v0.167.2`），使增量工具声明在 Google Cloud Vertex AI 上成为默认行为，并提供配置退出选项；与此同时，0.169/0.170 alpha 系列继续其快速迭代节奏。社区关注焦点集中在 Codex Desktop 上一个高互动量的 macOS 性能回归问题（#25719，398 👍），该问题影响 `syspolicyd`/`trustd`，此外还有密集的 Windows 沙箱和状态损坏报告。PR 队列显示出稳步的加固工作：MCP OAuth 写入安全、WebSocket 消息大小处理、Unicode 感知的线程搜索，以及 Windows 原子写入修复。

---

## 2. 版本发布

**`rust-v0.167.2`** — 稳定补丁。
- Codex 现在**在 Google Cloud Vertex AI 上默认以增量方式发送工具更新**。如需退出，请在 `config.toml` 的 `[features]` 下设置 `incremental_tools_legacy_gcp = false`。（[#60617](https://github.com/openai/codex/pull/60617)、[#60848](https://github.com/openai/codex/pull/60848)）

**Alpha 通道** — `rust-v0.170.0-alpha.1`、`rust-v0.169.0-alpha.11.1`、`rust-v0.169.0-alpha.11`、`rust-v0.169.0-alpha.10`、`rust-v0.167.0-alpha.4.2` — 常规增量版本，无面向用户的说明。

---

## 3. 热门 Issue

1. **[#25719](https://github.com/openai/codex/issues/25719)** — *macOS 桌面版触发 `syspolicyd`/`trustd` CPU/内存失控。* 得票最高的开放 Issue（398 👍，92 条评论），也是 macOS 桌面版系统性性能问题最明确的信号；该问题已持续活跃超过五个月仍未解决。
2. **[#58447](https://github.com/openai/codex/issues/58447)** — *macOS `dot` 线程读取返回 `DesktopThreadReadUnavailableError`。* 即使 `threadId`/`environmentId` 正确，也会破坏文档中记录的 `cloud_threads.read` 路由；阻塞跨界面线程访问。
3. **[#52713](https://github.com/openai/codex/issues/52713)** — *Windows 全局状态损坏导致项目和机器重置。* 加载时因空字节 JSON 解析失败导致静默数据丢失并覆盖回退状态 — 对 Windows 用户而言是持久性隐患。
4. **[#43194](https://github.com/openai/codex/issues/43194)** — *实验性上下文管理：Pro + Astra 上原生 notes/history 返回 404。* 当历史端点失败时，`new_context` 可能丢弃任务状态，对选择性启用的上下文管理构成严重的正确性风险。
5. **[#60867](https://github.com/openai/codex/issues/60867)** — *Windows CLI 0.167.1 在干净的沙箱预检下 `exec_command` CreateProcess 失败。* 可在 GitHub Actions `windows-2022` runner 上以惰性模型 fixture 复现 — 是一个有力且清晰隔离的沙箱/执行回归信号。
6. **[#60648](https://github.com/openai/codex/issues/60648)** — *Windows `dot` PC 访问回归：沙箱后端拒绝 Unrestricted/Enabled/Disabled。* 此前可用的远程 Windows 工作流现在在命令执行前即失败。
7. **[#58312](https://github.com/openai/codex/issues/58312)** — *Plus 五小时配额在 2026 年 10 月消耗速度加快约 5.6 倍。* 要求对速率限制计量变更提供透明度；与套餐规模及 agentic 使用模式相关。
8. **[#45367](https://github.com/openai/codex/issues/45367)** — *`threads.title`、`.preview`、`.first_user_message` 各自存储完整首条消息（3 倍，最高 1 MB）。* `state_5.sqlite` 中的存储膨胀缺陷，对 app-server 有直接的成本/性能影响。
9. **[#60212](https://github.com/openai/codex/issues/60212)** — *桌面版项目线程列表在“显示更多”后无法收缩回来。* 问题虽小但在 Pro 账户上广泛可感知的 UI 回归。
10. **[#60868](https://github.com/openai/codex/issues/60868)** — *长对话反复中断压缩；回答一句问候耗时 3 分 16 秒。* 说明 GPT-6.1 Sol Medium 上仍存在上下文/压缩不稳定性。

*其他值得注意的项目：* [#22085](https://github.com/openai/codex/issues/22085)（Windows Git 进程 CPU 风暴，现已关闭）、[#60894](https://github.com/openai/codex/issues/60894)（agent 安全取证报告：在 `0xC000021A` 之前修复在未经验证回滚的情况下继续进行），以及 `dovvnloading` 提交的一批精确的 CLI 边缘案例报告（[#60876](https://github.com/openai/codex/issues/60876) `apply_patch` 缺少空白上下文行、[#60900](https://github.com/openai/codex/issues/60900) 市场覆盖、[#60896](https://github.com/openai/codex/issues/60896) 路径中的 `#` 被解析为 Git ref）。

---

## 4. 关键 PR 进展

1. **[#60849](https://github.com/openai/codex/pull/60849)** — *保护 MCP OAuth 持久化免受过期凭据写入的影响。* 对存储的凭据进行加锁和重新验证，使待处理的刷新无法覆盖较新的登录或在登出后复活凭据。
2. **[#60872](https://github.com/openai/codex/pull/60872)** — *修复 Windows 原子写入并刷新构建维护。* 改用 `std::fs::rename` 进行原子替换，支持 Windows 上的重叠读取 — 与状态损坏报告直接相关。
3. **[#60841](https://github.com/openai/codex/pull/60841)** /**[#60840](https://github.com/openai/codex/pull/60840)** — *遵循通告的 WebSocket 消息大小限制；超大请求立即回退到 HTTP。* 在握手时读取 `x-codex-websocket-max-message-bytes` 并分类关闭码 `1009`，以避免徒劳的 WebSocket 重试。
4. **[#60848](https://github.com/openai/codex/pull/60848)** — *为 GCP 默认启用增量工具声明。* 0.167.2 发布说明背后的 PR；通过 `incremental_tools_legacy_gcp` 将 GCP 行为与 Amazon Bedrock 解耦。
5. **[#60759](https://github.com/openai/codex/pull/60759)** — *修复线程搜索中的 Unicode 无大小写匹配。* 为 `thread/searchOccurrences` 添加 Unicode 默认大小写折叠（希腊字母 sigma 变体、`ß`↔`ss`）。
6. **[#60845](https://github.com/openai/codex/pull/60845)** — *去重重叠根目录的文件搜索结果。* 防止重复/嵌套根目录占用结果槽位并虚增匹配计数。
7. **[#60842](https://github.com/openai/codex/pull/60842)** — *使 TUI 线程标题对生成失败具有韧性。* 立即显示占位标题，并将隐藏的标题生成请求排除在前台对话之外。
8. **[#60763](https://github.com/openai/codex/pull/60763)** — *在新会话中通过附件交接已批准的方案。* “是的，清除上下文并实施”现在将方案保存为 app-server 附件；新增实验性 `thread/prepareApprovedPlan`。
9. **[#60770](https://github.com/openai/codex/pull/60770)** — *从类型化请求派生 history 和 notes 工具 schema。* 用 `serde`/`schemars` Rust 类型替换手写 JSON schema — 降低 schema 漂移风险。
10. **[#60839](https://github.com/openai/codex/pull/60839)** — *在 rollout 重放中保留保留的 assistant 修订。* 捕获并持久化保留的来源，使重放不再分配新修订。

*同样值得关注：* [#60871](https://github.com/openai/codex/pull/60871)（初始化握手竞态）、[#60784](https://github.com/openai/codex/pull/60784)（集中化 Guardian 审查者选择）、[#60771](https://github.com/openai/codex/pull/60771)（面向模型的 ID 分配序号）。

---

## 5. 热门讨论

**问答**
- **[#24717](https://github.com/openai/codex/discussions/24717)** — *处理过时或不正确记忆的推荐方式。* 要求为记忆功能提供清除/重置/检查/选择性删除控制；六个月后仍未解决，表明存在持续缺口。
- **[#34373](https://github.com/openai/codex/discussions/34373)** — *[已答复] 如何卸载 Codex CLI？*（1 条评论）— 提醒人们基于 curl 的安装器缺乏明显的卸载路径，尤其是在 Arch Linux 上。

**展示与分享**
- **[#60861](https://github.com/openai/codex/discussions/60861)** — `claude-bulkhead`：用于共存 agent 会话的防护钩子（例如防止一个 agent 的 `pkill node` 杀死另一个 agent 的服务器）。
- **[#60834](https://github.com/openai/codex/discussions/60834)** — `Concord`：用于多 agent 协调的开源 MCP 服务器 — 共享存在、文件声明、交接。
- **[#60852](https://github.com/openai/codex/discussions/60852)** — 本地 Windows 插件，用于从 Codex 驱动 RDP 测试机（截图 + 输入）。
- **[#60818](https://github.com/openai/codex/discussions/60818)** — 在一个终端中于 Windows 上运行 5–10 个 Codex/Claude Code agent 的经验（来自 Lichen 的作者）。
- **[#60762](https://github.com/openai/codex/discussions/60762)** — `AGENTS.md Check`：MIT 许可的离线指令文件 linter（冲突标记、双向控制字符、无效 UTF-8）。
- **[#60722](https://github.com/openai/codex/discussions/60722)** — `TreeHarbor`：在工作区清理后保留 agent 补丁，并为每个补丁打开草稿 PR。

---

## 6. 功能请求趋势

- **记忆/上下文生命周期控制** — 明确要求可检查、可删除的记忆（#24717）以及可信的上下文管理端点（#43194、#60868）。用户希望上下文成为一等、可审计的资源。
- **多 agent 协调原语** — 多个讨论（#60861、#60834、#60818、#60722）汇聚于同一需求：当多个 agent 共享一台机器或一个仓库时，需要文件声明、工作区隔离、共享任务状态和补丁保留。
- **桌面 UI 透明度** — 恢复始终可见的分支名称/选择（#58814）、暴露有效图像模型和选择器（#43965），以及更清晰的反馈/线程列表行为（#60212、#36889）。
- **跨界面远程控制** — Android↔桌面↔无头 Linux 配对和远程任务控制仍是反复出现的诉求（#60348、#60310、#60648）。
- **配额透明度** — 对速率限制消耗变更提供明确计量说明（#58312）。

---

## 7. 开发者痛点

- **Windows 可靠性是主导主题**：沙箱后端拒绝（#60648）、`exec_command` CreateProcess 失败（#60867）、关机后全局状态损坏（#52713）、孤立的 worktree 包装器（#59934），以及消失的 Goals（#59366）。
- **macOS 桌面资源行为**：长期存在的 `syspolicyd`/`trustd` 失控（#25719）以及浏览器扩展在后台激活 Brave（#48173）表明操作系统集成的沙箱化不足。
- **CLI 边缘案例正确性**：`apply_patch` 静默接受不匹配的 hunk（#60876）、市场路径解析（#60896、#60900）、profile 作用域 MCP 移除提示（#60893），以及 SDK 文档缺口（#60895、#60892、#60899）— 一位持续贡献者（dovvnloading）正在提交高质量、低噪音的 bug，暗示测试覆盖存在缺口。
- **上下文与压缩不稳定性**：上下文端点返回 404 加上压缩中断长会话，侵蚀了对长期运行的 agentic 工作流的信任。
- **远程配对摩擦**：手动远程配对在 Android/无头 Linux 授权后失败（#60348），以及 Windows→Android（#60310），CLI 与守护进程之间的版本偏差（0.166.0 vs 0.167.0）被列为可疑原因。
- **Agent 安全期望**：Windows 系统修复取证报告（#60894）以及未经批准控制的自动审查拒绝（#60277）表明用户希望高影响操作具备可验证的回滚和覆盖路径。

---

*文摘基于截至 2026-11-12 的 github.com/openai/codex 数据生成。PR 描述基于提交的摘要；所列 PR 中有若干标记为 CLOSED，可能已被合并或取代。*

</details>

<details>
<summary><strong>Gemini CLI</strong> — <a href="https://github.com/google-gemini/gemini-cli">google-gemini/gemini-cli</a></summary>

# Gemini CLI 社区摘要 — 2026-11-12

## 1. 今日亮点

过去 24 小时内没有新版本发布，但活动仍集中在智能体可靠性上：子智能体终止语义、浏览器智能体健壮性以及 Windows/ACP 对等性修复占据了队列主导地位。来自单一贡献者（`dongs0104`）的一组引人注目的 PR 涉及二进制注入、ACP 工具截断和并行工具执行——表明正在集中推动让 ACP/非交互路径与交互式 CLI 保持一致。认证流程（可点击的换行 URL）和 PowerShell 事件循环阻塞也在积极修复中。

## 2. 版本发布

过去 24 小时内没有新版本发布。

## 3. 热门议题

1. **[#22323](https://github.com/google-gemini/gemini-cli/issues/22323)** — *子智能体 MAX_TURNS 被报告为 GOAL 成功*（P1，13 条评论）。`codebase_investigator` 子智能体在耗尽轮次预算且未进行任何分析后，仍声称 `status: "success"` / `Termination Reason: "GOAL"`。今日评论最多的议题；这是一个语义正确性问题，会破坏任何依赖子智能体状态的自动化流程。

2. **[#21409](https://github.com/google-gemini/gemini-cli/issues/21409)** — *通用智能体挂起*（P1，8 条评论，8 个 👍）。通用子智能体在琐碎任务（例如创建文件夹）上无限期挂起；用户报告等待长达一小时。今日反响最多的议题——强烈表明影响广泛。

3. **[#19873](https://github.com/google-gemini/gemini-cli/issues/19873)** — *零依赖操作系统沙箱与执行后意图路由*（P2，9 条评论）。提议让 Gemini 3 依托其原生 bash 亲和力（`grep`、`sed`、`awk`），同时通过沙箱控制风险。一个值得关注的设计方向议题。

4. **[#22745](https://github.com/google-gemini/gemini-cli/issues/22745)** — *AST 感知的文件读取、搜索与映射*（P2，7 条评论）。EPIC 正在研究语法感知工具是否能减少浪费的轮次和 token 噪声。与效率和上下文膨胀问题直接相关。

5. **[#21968](https://github.com/google-gemini/gemini-cli/issues/21968)** — *Gemini 未充分利用技能/子智能体*（P2，7 条评论）。虽为轶事但广泛共鸣：自定义技能和子智能体除非明确请求，否则不会被使用。指向工具选择/引导方面的缺口。

6. **[#22267](https://github.com/google-gemini/gemini-cli/issues/22267)** — *浏览器智能体忽略 `settings.json` 覆盖*（P2，5 条评论）。注册表正确读取了设置，但它们从未到达智能体——这是一个配置管道 bug，使浏览器智能体调优不可靠。

7. **[#21983](https://github.com/google-gemini/gemini-cli/issues/21983)** — *浏览器子智能体在 Wayland 上失败*（P1，4 条评论）。Linux 桌面用户遇到浏览器智能体故障；对于日益增长的用户群体而言，这是一个值得注意的平台覆盖缺口。

8. **[#24246](https://github.com/google-gemini/gemini-cli/issues/24246)** — *超过 128 个工具时出现 400 错误*（P2，3 条评论）。随着启用工具增多，工具作用域未能优雅降级。对任何堆叠扩展/MCP 服务器的人来说都很重要。

9. **[#22672](https://github.com/google-gemini/gemini-cli/issues/22672)** — *智能体应劝阻破坏性行为*（P2，3 条评论）。模型在存在更安全路径时偶尔会使用 `git reset`/`--force`。与安全相关，可能影响策略工作。

10. **[#18836](https://github.com/google-gemini/gemini-cli/issues/18836)** — *用持久化基于文件的任务跟踪替换 WriteToDo*（P3，2 条评论）。阐述了持久任务状态的“上下文腐化”/token 成本论点——这是长会话的基础性设计问题。

## 4. 关键 PR 进展

1. **[#30250](https://github.com/google-gemini/gemini-cli/pull/30250)**（已关闭）— 修复 `${VAR:-default}`，当变量已设置但为空时替换为默认值，符合 POSIX 语义。虽小但纠正了一个真实的配置陷阱。

2. **[#30247](https://github.com/google-gemini/gemini-cli/pull/30247)**（开放，P2）— 避免在 Windows 上进行 shell 策略检查时 PowerShell 解析期间阻塞 Node 事件循环；应能在 Shell 面板出现前解除 Thinking 加载动画的卡顿。

3. **[#30228](https://github.com/google-gemini/gemini-cli/pull/30228)**（已关闭，P2）— 在工具轮次的空模型响应后保留被中断的占位符，修复 `400 "Requests ending with a model turn are not supported"`。

4. **[#30241](https://github.com/google-gemini/gemini-cli/pull/30241)**（开放）— 截断工具响应时保持注入的二进制数据完整；修复大型音频/视频 `read_file` 调用上的 `400 "Base64 decoding failed"`。

5. **[#30230](https://github.com/google-gemini/gemini-cli/pull/30230)**（开放，P1）— 保留合成二进制确认，使注入的音频/视频在 Gemini 3 + Code Assist 下保持在其独立轮次中。与 #30241 密切相关；两者都针对二进制注入管道。

6. **[#30234](https://github.com/google-gemini/gemini-cli/pull/30234)**（开放）— ACP 现在像 CLI 一样截断大型工具输出。据报道，一次 `seq 1 60000` 调用在 ACP 上消耗了 369k 输入 token；这是一项重大的成本修复。

7. **[#30236](https://github.com/google-gemini/gemini-cli/pull/30236)**（开放）— ACP 将可并行的工具调用一起运行，镜像 CLI 的 `Scheduler`；串行等待此前抬高了轮次延迟。

8. **[#30232](https://github.com/google-gemini/gemini-cli/pull/30232)**（开放）— 为 ACP 实现 `session/close`，使客户端可以释放会话配置、聊天和 MCP 连接，而不是泄漏它们。

9. **[#30107](https://github.com/google-gemini/gemini-cli/pull/30107)**（开放，P1）— 当终端换行时使认证 URL 可点击，修复 Google 在窄终端上的 `400 Access blocked`。常见的入门障碍。

10. **[#29546](https://github.com/google-gemini/gemini-cli/pull/29546)**（开放，P2，需要帮助）— 通过注册 `SkillCommandLoader` 在非交互模式下启用 `/skill-name` 激活。弥合技能的交互式/非交互式差距。

## 5. 热门讨论

本数据集未提供讨论数据；本节省略。

## 6. 功能请求趋势

- **更智能、更廉价的上下文处理** — AST 感知的读取/搜索/映射（#22745、#22746、#22747）和精准的“巧妙提取”读取（#19561）反复成为主导的效率主题。
- **持久化、基于文件的任务状态** — 用 CRUD 任务跟踪替换上下文内的 `WriteToDo`（#18836）；为任务跟踪器试验原生文件工具（#21000）。
- **更好的子智能体可观测性** — 通过 `/chat share` 呈现子智能体轨迹（#22598），并在 `/bug` 报告中包含子智能体上下文（#21763）。
- **沙箱作为赋能者而非限制** — 让模型安全地发挥其 bash 优势（#19873）；劝阻破坏性的 git/数据库命令（#22672）。
- **自我感知的 CLI** — 准确的标志/热键和自我执行指导（#21432）。
- **并行/共享内存子智能体** — 探索性工作以并行子智能体支持为前提（#18287）。

## 7. 开发者痛点

- **智能体循环的可靠性**：挂起（#21409）、假成功终止（#22323）和卡住的交互式提示（#22465）侵蚀了对无人值守运行的信任。
- **不生效的配置**：浏览器智能体忽略 `settings.json`（#22267）；符号链接的智能体文件不被识别（#20079）；工具作用域在超过约 128 个工具后失败（#24246）。
- **Windows/Linux 平台对等性**：PowerShell 事件循环阻塞（#30247）、Wayland 浏览器故障（#21983）以及 `/ide install` 中的 cmd.exe 路径引用（#30246）。
- **二进制/媒体处理的脆弱性**：在 Code Assist 下通过 `read_file` 处理音频/视频时反复出现 400（#30230、#30241）以及空响应轮次处理（#30228）。
- **上下文/token 经济性**：每轮基线约 36.6k token 和“消防水管式”读取（#19561），加上无界的 ACP 工具输出（#30234），导致成本投诉。
- **认证/入门摩擦**：换行的登录 URL 导致 `400 Access blocked`（#30107）仍然是反复出现的首次运行障碍。

</details>

<details>
<summary><strong>GitHub Copilot CLI</strong> — <a href="https://github.com/github/copilot-cli">github/copilot-cli</a></summary>

# GitHub Copilot CLI 社区摘要 — 2026-11-12

## 1. 今日亮点

仓库今日动静不大：发布了一个小型预发布版本（`v1.0.110-2`），包含两项针对中断响应和工作目录被删除场景的定向稳健性修复。Issue 活跃度仍然很高（24 小时内更新 42 条），社区关注点分散于**新版简洁转录模式的预发布回归**、**MCP/会话恢复不稳定**，以及 Windows、WSL2、macOS 和 Linux ARM64 上的**平台特定故障**。过去 24 小时内没有 Pull Request 更新，因此本轮所有进展仅通过发布和 Issue 分诊体现。

## 2. 发布

**v1.0.110-2** — 补丁版本：
- **中断/失败尝试产生的部分响应**现在会被标记为*不完整*，同时保留其文本内容，因此被中止的轮次不再看起来像正常完成。
- **已删除工作目录的处理**：从不存在的目录启动 `copilot` 时，现在会输出清晰的“目录已不存在”消息及恢复指引，而不是 Node 堆栈跟踪。

🔗 https://github.com/github/copilot-cli/releases

## 3. 热门 Issue

1. **#3534 [CLOSED] WSL2 ARM64 `/copy` 失败，报 `clip.exe exited with code 1`** — 通过 `cmd.exe` 包装器写入剪贴板因引号处理而损坏；9 条评论和 7 个 👍 使其成为当日关闭前互动最多的问题。对日常依赖 `/copy` 的 ARM64 WSL 用户影响重大。🔗 https://github.com/github/copilot-cli/issues/3534
2. **#4802 [OPEN] PRU 配额被清零，可能与 Assisted Permissions 有关** — 一位 Pro/legacy PRU 用户报告在启用辅助权限后配额消失。涉及计费/信任的高风险问题，已有 9 条评论；值得关注官方根因说明。🔗 https://github.com/github/copilot-cli/issues/4802
3. **#5171 [OPEN] NumLock 关闭时小键盘导航和 Delete 键被忽略（Windows）** — 1.0.98 引入的回归，影响 Windows Terminal 用户；虽小但持续复现的典型小毛病。🔗 https://github.com/github/copilot-cli/issues/5171
4. **#5053 [OPEN] ACP 会话停止在 `session-store.db` 中索引历史/用量（1.0.89 回归）** — 破坏 ACP 用户的本地会话历史和用量追踪；典型的“静默回归”，侵蚀对非交互式工作流的信任。🔗 https://github.com/github/copilot-cli/issues/5053
5. **#4606 [OPEN] Google Workspace MCP OAuth 因 issuer 尾部斜杠不匹配而失败** — 原生 HTTP MCP 认证在浏览器流程开始前就因 OIDC issuer 规范化问题失败；完全阻断 Google Workspace MCP 集成。🔗 https://github.com/github/copilot-cli/issues/4606
6. **#2955 [OPEN] `/allow-all` 未抑制 bash 工具提示** — 长期存在的权限 bug（自 4 月起开放），用户执行 `/allow-all` 后仍会收到逐命令批准对话框；2 个 👍 和持续评论表明它仍在困扰用户。🔗 https://github.com/github/copilot-cli/issues/2955
7. **#5312 [CLOSED] 上下文窗口滚动仍然失败；1.0.105 上缺少 `checkpoint.md`** — 在先前 Issue（#5205）以“已完成”关闭但未关联修复或发布后，用户沮丧地提交；既是 bug 也是流程透明度投诉。🔗 https://github.com/github/copilot-cli/issues/5312
8. **#5335 [OPEN] 反复出现的 HTTP 422 网络安全风险标记阻断合法本地调试** — 模型侧安全分类器反复拒绝良性的本地不稳定测试调查；模型护栏误报直接损害开发者工作流。🔗 https://github.com/github/copilot-cli/issues/5335
9. **#5145 [OPEN] 思考模式下必须回传 400 `reasoning_text`（自定义 OpenAI 兼容提供商）** — 由后台 shell 退出后运行时合成的助手轮次触发；破坏 BYOK/DeepSeek 风格提供商会话。🔗 https://github.com/github/copilot-cli/issues/5145
10. **#5256 [CLOSED] 未登录时 `gh auth token` 紧密重试循环导致高 CPU** — 后台认证探测无退避重试，在 BYOK/无 GitHub 账户会话中消耗 CPU；已关闭，但代表了认证探测加固需求。🔗 https://github.com/github/copilot-cli/issues/5256

*荣誉提名（同日、分诊新鲜）：* #5426（简洁转录在 `showReasoning`/Ctrl+T 下仍丢弃推理行）、#5427（折叠的“N commands”组渲染未截断文本）、#5424（linux-arm64 二进制在 Linux 6.12 aarch64/4K pages 上 `ENOEXEC`）、#5423（IDE 连接时文件链接不在 VS Code 中打开）。

## 4. 关键 PR 进展

过去 24 小时内没有 Pull Request 更新；本轮无 PR 进展可报告。

## 5. 热门讨论

本时间窗口未提供讨论数据；本节省略。

## 6. 功能请求趋势

- **配置与对等性**：对统一交互式 `/config` 编辑器（#3352）和禁用内置斜杠命令（#5375）的请求表明，对 CLI 可配置性和 Claude Code 对等性的需求日益增长。
- **语音/输入人体工学**：使 `/voice` 转录语言可配置而非硬编码 `"auto"`（#5226），以及持续的小键盘/键绑定修复（#5171）。
- **会话 UX**：在 `/session` 选择器中内联会话摘要（#2330）——更好地发现会话名称/最近性/意图。
- **MCP 生命周期管理**：客户端过期时 DCR 重新注册（#5284），以及启动间 MCP 工具集变化时的韧性（#5359）。
- **转录渲染控制**：对简洁转录行为的细粒度控制（推理可见性、命令组截断——#5426、#5427）。

## 7. 开发者痛点

- **跨版本回归疲劳**：多个 Issue 明确引用版本回归（1.0.89 ACP 索引、1.0.98 Windows 小键盘、1.0.105 上下文滚动），且至少一位提交者因先前 Issue 关闭但未关联修复而表达沮丧（#5312）。发布说明卫生和 Issue 关闭透明度是反复出现的痛点。
- **MCP 和会话恢复脆弱性**：MCP 服务器在会话 ID 交换期间被静默丢弃（#4920）、恢复会话因“tool reference not found”失败（#5359），以及 OAuth 边缘情况（#4606、#5284），使 MCP 成为最脆弱的表面区域。
- **平台特定故障**：WSL2 ARM64 剪贴板（#3534）、带 dotnet/MSBuild 的 Windows 沙箱构建（#5421）、macOS 12.7.6 原生插件加载失败（#5418），以及 linux-arm64 `ENOEXEC`（#5424），显示非主流平台上的打包/运行时缺口。
- **权限系统不一致**：`/allow-all` 未抑制提示（#2955）和权限请求投递超时自动取消会话（#5018），削弱了对权限流程的信任。
- **模型侧护栏误报**：网络安全风险 422（#5335）和自定义提供商推理文本 400（#5145）阻断合法调试和 BYOK 使用。
- **资源泄漏与可靠性**：无界 `gh auth token` 重试循环（#5256）和构建协调器未经批准被停止（#5420），引发对长时间运行会话稳定性的担忧。

---
*摘要基于 github.com/github/copilot-cli 2026-11-12 数据生成。注：源数据中无 Discussions 或 PR 活动；相应章节已省略或相应标注。*

</details>

<details>
<summary><strong>OpenCode</strong> — <a href="https://github.com/anomalyco/opencode">anomalyco/opencode</a></summary>

# OpenCode 社区文摘 — 2026-11-12

## 今日亮点

免费套餐体验持续占据社区关注焦点，超级讨论帖 #59387 的评论数已达 354 条，且不断有新的报告称队列消失、限制被静默执行。工程方面，团队发布了 `process-daemon-v0.5.2`（协议 8），并正在积极落地一系列核心修复，涉及子代理唤醒恢复、面向插件的会话历史 API，以及 Windows 上持久化 shell 执行的支持。桌面端可靠性仍是薄弱环节，围绕后台进程、RPC 挂起和编辑器集成出现了多个高信噪比的缺陷报告。

## 版本发布

- **[process-daemon-v0.5.2](https://github.com/anomalyco/opencode/releases)** — `opencode-pty` 0.5.2（协议 8），已以 `@opencode/pty@0.5.2` 发布至 npm。该版本现已被 core、CLI 和 server 固定依赖，并为可在服务器重启后存活的 shell 命令启用了 Windows 本地守护进程路径（见 PR #63409）。

## 热门 Issue

1. **[#59387 — [zen] 免费套餐问题：请先阅读此文](https://github.com/anomalyco/opencode/issues/59387)**（354 条评论，27 👍）— 免费套餐投诉的权威讨论帖。用户报告"Free usage exceeded"错误、永不触发的重置，以及 Desktop 版本不匹配。如此大的讨论量表明，免费套餐的提示信息和配额重置仍是信任问题，而不仅仅是容量问题。

2. **[#63393 — 切换模型导致排队请求消失](https://github.com/anomalyco/opencode/issues/63393)** — 达到限制后切换模型会静默丢弃队列。报告者从"收件箱表"中恢复了请求，这意味着数据并未丢失，但用户体验很危险。修复复杂度低，但情感影响大。

3. **[#63368 — 提案：保留一个始终可用、非前沿的免费模型](https://github.com/anomalyco/opencode/issues/63368)** — 一周内有两个免费模型被弃用（Pulse Alpha 11 月 6 日，Crouton Alpha 11 月 9 日），且只有 LongCat 2.5 公布了具体限制。该请求追求的是可预测性而非前沿能力——一个合理的社区折中方案。

4. **[#63336 — serve 守护进程泄漏每个会话的 stdio MCP 服务器进程](https://github.com/anomalyco/opencode/issues/63336)** — 关闭 TUI 窗口后，MCP 传输仍在 `opencode serve --service` 下运行。这是一类资源泄漏缺陷，对于长时间运行的用户会不断累积。

5. **[#63092 — MCP 超时文档写的是 5 秒，客户端却用 30 秒；标量超时会覆盖 12 小时窗口](https://github.com/anomalyco/opencode/issues/63092)** — 复现了已发布 schema 与捆绑客户端行为之间的不一致。配置语义与文档背离是用户困惑的反复来源。

6. **[#63384 — `experimental.browser/attach` 无限挂起，导致 Desktop 会话超时](https://github.com/anomalyco/opencode/issues/63384)** — Desktop 在会话恢复期间调用此接口；挂起会级联为连接断开与重连。单个阻塞性 RPC 卡住应用启动，是严重的可靠性问题。

7. **[#63385 — `iceman-contributor-free` 因 `reasoning_display` 错误而失败](https://github.com/anomalyco/opencode/issues/63385)** — 上游拒绝了客户端从未发送的参数，指向提供商侧或转换层的错配。值得注意的是，它会在每一轮完全破坏一个免费模型。

8. **[#63171 — 在推理过程中停止的轮次被判定为“成功”但内容为零](https://github.com/anomalyco/opencode/issues/63171)** — 静默数据丢失：`finish: "stop"`、`tokens.output: 0`、`outcome: succeeded`。这会破坏对话记录和遥测数据，应优先分诊处理。

9. **[#63331 — 独立模式下缺少 `github-copilot/auto`](https://github.com/anomalyco/opencode/issues/63331)** — 共享服务器模式与独立模式之间的模型发现存在差异，阻断了 Copilot Student 用户（其套餐仅允许 `auto` 路由）。

10. **[#63195 — 提供商 `usage.cost` 未经单位检查即被当作美元](https://github.com/anomalyco/opencode/issues/63195)** — 以微美元计价的提供商会将会话成本夸大约 1e6 倍。这是计费显示中的正确性缺陷，会削弱对成本追踪的信任。

## 关键 PR 进展

1. **[#63416 — fix(core)：在子代理报告确认后恢复父级唤醒](https://github.com/anomalyco/opencode/pull/63416)** — #63209 的后续；处理父级处于空闲状态、有待处理通知却无执行声明的崩溃窗口。对后台子代理的可靠性很重要。

2. **[#63415 — fix(core)：重启后恢复直接驱动的子会话](https://github.com/anomalyco/opencode/pull/63415)** — 关闭 #63414。确保在服务器停止前被直接继续的子会话不会被陈旧声明清扫所孤立。

3. **[#63413 — refactor(core)：移除子代理的 `notify` 选项](https://github.com/anomalyco/opencode/pull/63413)** — 现在每个有父级的子代理都会在空闲时报告；不再支持静默子代理。这是对子代理契约的有意简化。

4. **[#63410 — feat(plugin)：暴露分页的会话消息](https://github.com/anomalyco/opencode/pull/63410)** — 为插件 API 新增 `ctx.session.message.list`，让插件可以构建历史搜索工具（与 #63411 配套）。

5. **[#63411 — feat(core)：会话历史搜索与检查工具](https://github.com/anomalyco/opencode/pull/63411)** — 消费 #63410，让代理可以搜索自身的会话历史，位于 `experimental.session_search` 后面。这是迈向代理自我上下文管理的有意义一步。

6. **[#63409 — feat(core)：在进程守护进程中运行本地 Windows shell 命令](https://github.com/anomalyco/opencode/pull/63409)** — 固定守护进程 0.5.2，并将持久化 shell 执行扩展到 Windows。直接解决了一个长期存在的平台空白。

7. **[#63412 — fix(mcp)：传播活跃的 trace 上下文](https://github.com/anomalyco/opencode/pull/63412)** — 关闭 #46856。在 MCP 工具、提示、资源和目录请求之间转发 W3C trace 上下文——这是迟来的可观测性工作。

8. **[#63030 — fix(core)：尾部读取不再将整个文件保留在内存中](https://github.com/anomalyco/opencode/pull/63030)** — #62147 的后续；对大文件（示例为 108 MiB）分页读取时不再加载整个文件。实实在在的内存占用收益。

9. **[#63406 — fix(desktop)：“打开方式”中仅列出已安装的编辑器](https://github.com/anomalyco/opencode/pull/63406)** — 关闭 #63194；`checkAppExists` 现在在 Linux/Windows 上真正进行检查，且 VSCodium 可被识别。小改动但解决了高摩擦的桌面问题。

10. **[#62995 — feat(vscode)：新增侧边栏 UI](https://github.com/anomalyco/opencode/pull/62995)** — 关闭 #11176；将 OpenCode 带入 VS Code 的次级侧边栏。这是社区呼声很高的 IDE 集成，可扩大覆盖范围。

## 功能请求趋势

- **可预测的免费套餐政策**：一个稳定、有文档、配额可见的免费模型（#63368、#59387），而非轮换的限时模型。
- **会话/队列持久性**：跨模型切换保留队列（#63393），以及可恢复/可纠正的会话状态（移动的会话更新项目，#63188）。
- **插件 API 扩展**：分页的会话历史与嵌入支持（`experimental.generate.embed`，#62794）——插件作者希望获得更丰富的上下文访问。
- **桌面/IDE 打磨**：可调整的会话内容宽度（#53382）、VS Code 侧边栏（#62995）、Windows 文件树刷新（#63214）、VSCodium 支持（#63194）。
- **自定义提供商可配置性**：在 v2 中恢复自定义提供商选项（#59603）。

## 开发者痛点

- **免费套餐配额不透明**：不触发的重置、未记录的上限和弃用节奏侵蚀信任；那条 354 条评论的讨论帖是最清晰的信号。
- **静默的数据/状态丢失**：消失的队列（#63393）、零内容的“成功”轮次（#63171）以及被丢弃的请求是最具破坏性的一类缺陷，因为在恢复之前它们都不可见。
- **桌面端生命周期可靠性**：窗口关闭后仍存活的后台进程（#60983）、泄漏的 MCP 服务器（#63336），以及阻塞会话恢复的挂起 RPC（#63384）聚集成一种生命周期管理欠债的模式。
- **配置与文档背离**：MCP 超时（#63092）和用量成本单位（#63195）表明已发布的 schema 与运行时行为不符——对任何调整配置的人代价高昂。
- **平台对等性差距**：独立模式与共享服务器模式的模型发现（#63331）以及 Windows/Linux 编辑器检测（#63406）反复困扰非 macOS 环境的用户。

</details>

<details>
<summary><strong>Pi</strong> — <a href="https://github.com/earendil-works/pi">earendil-works/pi</a></summary>

# Pi 社区摘要 — 2026-11-12

## 1. 今日亮点

今天是密集修 bug 的一天：`robopipi` bot 提交了一批针对性修复，涵盖 TUI 渲染（Kitty 图像重绘、导出中的 OSC 8 超链接）、持久化存储竞态、恢复时 MCP 工具还原，以及独立二进制的扩展解析。与此同时，Vercel AI Gateway 集成获得关注，新增 PR 将 OpenAI 模型通过 Responses 路由，并持久化托管 tool-search 历史。过去 24 小时无新版本发布。

## 2. 版本发布

过去 24 小时内无发布。

## 3. 热门 Issue

1. **[#11956] 文件打开期间取消读取导致崩溃**（已关闭，p0）——当 codemode 的 `Promise.all` 取消同级读取时，未捕获的 `AbortError` 可能直接杀死 Pi。严重性高，因为 codemode 工作流很容易触发。 [链接](https://github.com/earendil-works/pi/issues/11956)

2. **[#12068] `pi -p` 在 Windows 上永不退出（1.5.1/1.5.2）**（已关闭）——当发生工具调用且扩展调用 `pi.exec()` 时，打印模式挂起；1.5.0 正常。这是影响 Windows 上 CI/脚本化使用的回归。 [链接](https://github.com/earendil-works/pi/issues/12068)

3. **[#10162] 输入图像过多会中止 agent 任务**（开放，9 条评论）——长时间运行的 agent 会话在图像密集输入下会中断，尽管有自动压缩。这直接削弱了“无限期照看 PR”的工作流。 [链接](https://github.com/earendil-works/pi/issues/10162)

4. **[#11026] pi-durable：永久删除对话**（开放，6 👍）——请求 `harness.deleteConversation(id)`，并在运行中安全拒绝。社区对持久化存储的数据生命周期控制有显著支持。 [链接](https://github.com/earendil-works/pi/issues/11026)

5. **[#10386] Durable：托管编码 agent 扩展**（开放）——要求提供基于 `AgentSession` 的运行时或 hook 对等性，使扩展能在 pi-durable 上运行。这是统一跨运行时扩展生态的关键。 [链接](https://github.com/earendil-works/pi/issues/10386)

6. **[#12019] github-copilot：Gemini 模型永远拿不到 `reasoning_effort`**（开放，p0）——由于 `compat.supportsReasoningEffort: false`，`--thinking` 对 Copilot Gemini 模型静默无效。这是误导性的用户可见行为。 [链接](https://github.com/earendil-works/pi/issues/12019)

7. **[#11999] ant-ling 提供商损坏——迁移到 Ling 3.x Flash**（开放，p0）——目录仍附带 Ling/Ring 2.6 ID，但上游已无法解析；该提供商端到端不可用。作者愿意提供 PR。 [链接](https://github.com/earendil-works/pi/issues/11999)

8. **[#12035] 虚拟模型的阈值压缩远低于窗口就触发**（开放）——`_checkCompaction` 将未知上下文窗口视为极小值，导致过早压缩。影响所有使用虚拟模型的用户。 [链接](https://github.com/earendil-works/pi/issues/12035)

9. **[#11977] `/login` 在屏幕上绘制 API key 并将其存储为配置值**（已关闭）——一个安全相关的 UX 缺陷：密钥在面板中可见，并以明文配置持久化。 [链接](https://github.com/earendil-works/pi/issues/11977)

10. **[#12023] `.pi/agents/` 未受信任门控；子 agent 审批门可被绕过**（已关闭）——对于仅附带 agent 文件的仓库，项目信任默认为 true，且示例审批门可被模型禁用且对符号链接视而不见。这是值得注意的供应链隐患。 [链接](https://github.com/earendil-works/pi/issues/12023)

## 4. 关键 PR 进展

1. **[#12069] 将 Vercel OpenAI 模型通过 Responses 路由**——在 Vercel AI Gateway 上将 `openai/*` 目录模型路由到 `openai-responses`，解锁网关背后的 Responses 特性。 [链接](https://github.com/earendil-works/pi/pull/12069)

2. **[#12071] 保留托管 Responses tool-search 历史**——保留 `tool_search_call`/`tool_search_output` 项，使无状态后续请求保留工具加载历史和缓存前缀。 [链接](https://github.com/earendil-works/pi/pull/12071)

3. **[#12072] 将延迟 Responses 工具分组到命名空间**——可选的 `toolSearch.deferredTools` 命名空间化降低大型工具目录的输入 token 成本。 [链接](https://github.com/earendil-works/pi/pull/12072)

4. **[#12054] 当扩展仅隐藏自身工具时保留恢复的 MCP 工具**——修复在恢复/重载时通过 `tool_search` 加载的 MCP 工具被丢弃的问题，解决 #11996。 [链接](https://github.com/earendil-works/pi/pull/12054)

5. **[#12046] 修复独立二进制通过 `main`/`exports` 解析扩展依赖**——启用 `--compile-autoload-package-json`，使 bun 编译的二进制遵循包入口点；解决 #12012（Shiki 失败）。 [链接](https://github.com/earendil-works/pi/pull/12046)

6. **[#12052] 在 HTML 导出中转换 OSC 8 超链接**——`/export` 现在输出可点击链接而非原始转义序列，修复 #12003。 [链接](https://github.com/earendil-works/pi/pull/12052)

7. **[#12047] 在全屏完整重绘中保留 Kitty 图像**——围绕 `ESC[2J` 重新排序图像删除/放置，以停止调整大小时的图像闪烁。 [链接](https://github.com/earendil-works/pi/pull/12047)

8. **[#12055] Durable：跳过 `listDir` 枚举期间被移除的条目**——修复在实时写入器旁打开的只读 JSONL 存储的 `ENOENT ... .reclaim` 竞态（#11994）。 [链接](https://github.com/earendil-works/pi/pull/12055)

9. **[#12042 / #12059] P2 备份协调器 + Radius 后端**——为 Chord 存储会话历史添加 `P2_BACKUP_DIR` 本地备份和 `P2_BACKUP=radius` 远程后端。 [链接](https://github.com/earendil-works/pi/pull/12042) · [链接](https://github.com/earendil-works/pi/pull/12059)

10. **[#12051] 使用运行中的 Node.js 验证托管更新**——修复 Windows 上 `pi update` 验证问题，其中 npm shim 选用了与托管启动器不同的 Node。 [链接](https://github.com/earendil-works/pi/pull/12051)

## 5. 热门讨论

**想法**
- **[#12063] Pi 与 Concord 集成**——提议 Pi 加入一个跨 harness 的共享工作状态 MCP 服务器（在线状态、文件声明、任务上下文、交接），与 Claude Code 和 Cursor 并列。早期信号（1 👍，0 条评论），但指向多 agent 协调需求增长。 [链接](https://github.com/earendil-works/pi/discussions/12063)

## 6. 功能请求趋势

- **持久化存储生命周期与可扩展性**——对话删除（#11026）、扩展托管/hook 对等性（#10386），以及 P2/Radius 备份工作（#12042、#12059、#11990）显示对 pi-durable 作为一等运行时的持续投入。
- **扩展 API 表面**——原子入队并唤醒完成 API（#12073）和请求准入门（#12057）表明扩展作者希望对会话调度有更多控制。
- **终端与剪贴板互操作**——Kitty 剪贴板协议（#10089）、Cmd+V 图像粘贴（#4332）和 alacritty 键盘标志收窄（#12050）反映对跨终端/SSH 稳健 TUI 行为的需求。
- **UX 噪音可配置性**——可切换更新横幅（#3005）和条件化 `AGENTS.md` 加载（#11933）显示用户希望更少侵入、更情境化的默认值。
- **跨 harness 协作**——Concord 讨论表明对 Pi 与其他编码 agent 之间共享状态的兴趣。

## 7. 开发者痛点

- **Windows 打印模式可靠性**——`pi -p` 挂起（#12068）和托管更新 Node 不匹配（#12051）指向脚本化/CI 场景下脆弱的 Windows 体验。
- **恢复/重载状态丢失**——恢复时 MCP 工具被丢弃（#11996）、重放的提示模板展开（#12013），以及 MCP 连接后缓存系统前缀失效（#12015）都源于会话再水合边缘情况。
- **静默错误行为而非硬错误**——Copilot 模型的推理努力被忽略（#12019）、过长尾部导致空 bash 输出（#12062），以及虚拟模型过早压缩（#12035）都静默失败，这比显式错误更难诊断。
- **提供商目录漂移**——损坏的 ant-ling ID（#11999）和隐藏的存储模型覆盖（#12037）显示目录陈旧/合并是反复出现的维护负担。
- **数据卫生**——工具调用参数中缺少孤立代理项剥离（#12014）会破坏所有后续请求，这是一个小解析缺口但影响范围巨大。

</details>

<details>
<summary><strong>Qwen Code</strong> — <a href="https://github.com/QwenLM/qwen-code">QwenLM/qwen-code</a></summary>

# Qwen Code 社区简报 — 2026-11-12

## 1. 今日亮点

Qwen Code 发布了 **v1.0.0-preview** 版本，统一了 Qoder 和 Qwen Code 的智能体能力，覆盖代码理解、实现、调试与测试——这是一个重要的里程碑，但附带稳定性方面的注意事项。与此同时，问题追踪器仍然被 **CI 不稳定和主分支失败** 所主导（若干由机器人提交、评论数达数百的问题），此外还有一系列由社区提出的 **Web Shell 用户体验优化** 和 **核心智能体循环机制**（先验证后修复、程序化工具调用）。

---

## 2. 版本发布

**v1.0.0-preview** — [Release](https://github.com/QwenLM/qwen-code/releases)
- 整合了 **Qoder** 和 **Qwen Code** 的智能体能力，用于代码理解、实现、调试和测试。
- 支持 Qwen 和 Qoder 账户。
- ⚠️ 预览版注意事项：在预览期间，接口、配置选项和行为可能发生变化。开发者应固定版本并预期变动。

---

## 3. 热门 Issue

1. **[#14724](https://github.com/QwenLM/qwen-code/issues/14724)** — *主 CI 失败：Managed Agent 门禁——未识别到测试*（324 条评论）。仓库中流量最高的问题；这是一个持续存在的 CI 门禁失败，自 10 月中旬开放以来持续产生机器人噪声。表明托管智能体测试门禁存在不稳定性。

2. **[#11680](https://github.com/QwenLM/qwen-code/issues/11680)** — *主 CI 失败：E2E 测试——未识别到测试*（48 条评论）。一个长期存在的 E2E 沙箱失败（docker 分片），按提交追踪；表明 E2E 通道不稳定并反复提交新问题。

3. **[#16425](https://github.com/QwenLM/qwen-code/issues/16425)** — *工作流智能体权限审批永不渲染；运行无限挂起*（已关闭，10 条评论）。一个严重的 P1 核心/交互缺陷：工作流运行器智能体到达权限提示，测试框架触发 `PermissionRequest`，但没有任何 UI 渲染，运行挂起。在 0.26.6–0.26.8 中均可复现。其关闭是一项值得注意的可靠性胜利。

4. **[#16915](https://github.com/QwenLM/qwen-code/issues/16915)** — *任务报告两个错误且无法继续*（7 条评论）。面向用户的会话管理缺陷，模型服务连接中断会阻止任务继续；需要更多信息，但反映了长时间运行任务的真实摩擦。

5. **[#124](https://github.com/QwenLM/qwen-code/issues/124)** — *使用自托管 Qwen3-Coder 的工具调用失败（几乎总是）*（已关闭，7 条评论，7 个 👍）。自 2025 年 7 月以来长期存在的社区痛点，面向自托管用户；其关闭对开源/自托管群体意义重大。

6. **[#15861](https://github.com/QwenLM/qwen-code/issues/15861)** — *web-shell 主机目录选择器冒烟测试超时，与 PR 无关*（7 条评论）。一个确定性的 E2E 冒烟测试失败，使每个 PR 都承担重新归因的开销——维护者标记的明确 CI 质量问题。

7. **[#14361](https://github.com/QwenLM/qwen-code/issues/14361)** — *会话存储在 Hosted Shell 轮次中偶发返回 400 拒绝（41% 的 SDK Java 运行）*（7 条评论）。Java SDK 托管 shell 生命周期恢复中的 P2 可靠性缺陷；高偶发率使 SDK 消费者付出高昂代价。

8. **[#16851](https://github.com/QwenLM/qwen-code/issues/16851)** — *Web UI：每周使用限额卡住，未重置*（3 条评论）。coder.qwen.ai 上的配额/重置缺陷，影响真实用户；属于支持类问题，但指向账户状态不一致。

9. **[#16921](https://github.com/QwenLM/qwen-code/issues/16921)** — *glob 永远无法匹配真实目录（`nodir:true` + `follow:false`）*（3 条评论）。一个核心工具正确性缺陷：目录形态的 glob 模式返回“未找到文件”，误导模型认为路径不存在。

10. **[#16954](https://github.com/QwenLM/qwen-code/issues/16954)** — *`scripts/lint.js` 入口守卫通过符号链接静默无操作（macOS）*（3 条评论）。当通过符号链接路径调用时，lint 未运行即以 0 退出——本地开发中一个棘手的静默通过失败模式。

---

## 4. 关键 PR 进展

1. **[#16594](https://github.com/QwenLM/qwen-code/pull/16594)** — *fix(cli)：永不在分离时终态释放配置作用域的 Hook 所有者*。加固 lifecycle-v1/v2 运行时排空，使重新计算的所有者不会被过早释放——对 hook 和会话生命周期正确性很重要。

2. **[#16849](https://github.com/QwenLM/qwen-code/pull/16849)** — *fix(acp)：在会话重载之间投递队友报告并重新附加持久化团队*。将智能体团队协调接入 ACP 接口（`qwen serve --web`/`--acp`），使队友收件箱报告作为延续轮次到达领导者。

3. **[#16895](https://github.com/QwenLM/qwen-code/pull/16895)** — *fix(core)：以上下文窗口为界限制压缩后恢复*。使最近文件恢复具备预算感知（窗口相对、CJK 感知）——直接解决压缩后的上下文膨胀问题。

4. **[#14332](https://github.com/QwenLM/qwen-code/pull/14332)** — *让工具输出截断取决于剩余上下文窗口*。用与剩余上下文绑定的动态限制替代固定字符上限——智能体循环质量的实质性改进。

5. **[#16972](https://github.com/QwenLM/qwen-code/pull/16972)** — *fix(mcp)：在显式重新发现时恢复会话目录*。修复 MCP 工具/提示/资源缓存在重新发现时丢失的问题；复用传输并保留无关目录。

6. **[#16979](https://github.com/QwenLM/qwen-code/pull/16979)** — *fix(web-shell)：清理停靠在 terminal session_closed 上的分屏窗格*。清理分屏视图中陈旧的“Session closed”窗格——一个可见的 UX 优化项。

7. **[#16779](https://github.com/QwenLM/qwen-code/pull/16779)** — *feat(web-shell)：改进会话搜索体验*。将会话搜索移入专用对话框，支持片段、键盘选择、焦点恢复以及加载/重试状态。

8. **[#16973](https://github.com/QwenLM/qwen-code/pull/16973)** — *fix(ci)：通过符号链接入口路径运行 lint*。#16954 的配套修复；确保入口为符号链接时 lint 实际执行。

9. **[#16974](https://github.com/QwenLM/qwen-code/pull/16974)** — *fix(ci)：限制运行器监控并在 Docker 故障时退避*。减少运行器监控噪声，并在 Docker 检查失败时添加退避——CI 基础设施加固。

10. **[#16946](https://github.com/QwenLM/qwen-code/pull/16946)** — *ci：将 lint 门禁新鲜度收窄到 ci.yml 定义的 lint 切片*。防止无关的 CI 定义编辑使 lint 门禁新鲜度守卫失效——贡献者开发体验改进。

---

## 5. 热门讨论

*源数据集中未提供 Discussion 数据；本节省略。*

---

## 6. 功能请求趋势

从过去 24 小时内更新的 Issue 和 PR 中提炼：

- **核心智能体循环机制**：同一作者的两项值得注意的提案将验证/编排推入核心而非技能——[使用干净上下文验证器的先验证后修复（#16941）](https://github.com/QwenLM/qwen-code/issues/16941) 和 [沙箱中的程序化工具调用（#16944）](https://github.com/QwenLM/qwen-code/issues/16944)。两者都针对上下文性能（`roadmap/context-performance`）。
- **Web Shell / 桌面 UX**：一组密集的 UI 请求——[模型选择器中的主机内容槽位（#16893）](https://github.com/QwenLM/qwen-code/issues/16893)、[工作区选择后聚焦编辑器（#16980）](https://github.com/QwenLM/qwen-code/issues/16980)、[修复分屏视图中被裁剪的 TodoPanel 工具提示（#16970）](https://github.com/QwenLM/qwen-code/issues/16970)，以及 [消息选择菜单中的“在侧任务中提问”（#16964）](https://github.com/QwenLM/qwen-code/issues/16964)。
- **守护进程 / 渠道集成**：[从观察到的联系人中选取渠道允许列表条目，而非输入平台 ID（#16931）](https://github.com/QwenLM/qwen-code/issues/16931)——减少消息集成的操作摩擦。
- **文档正确性**：[MCP 文档声称需要重启，但 `mcpServers` 会热重载（#16925）](https://github.com/QwenLM/qwen-code/issues/16925)——文档与行为不匹配。

---

## 7. 开发者痛点

- **CI 不稳定与主分支红灯**：最大的噪声来源。机器人提交的“主 CI 失败”问题（#14724 有 324 条评论，#11680、#11678、#13009、#15574、#15698）加上确定性冒烟测试失败（#15861）和不稳定测试标记（#16975、#16959）主导了追踪器。每个无关 PR 都付出重新归因的代价。
- **静默通过 / 正确性陷阱**：#16954（lint 通过符号链接以 0 退出）和 #16921（glob 无法匹配目录）都 *静默* 失败，这对将“未找到文件”解释为事实的智能体驱动工作流尤其有害。
- **会话/连接不稳定**：多份报告称任务在运行中挂起或中断（#16915、#16908、#16425），指向模型服务连接处理和权限提示渲染是脆弱环节。
- **压缩后的上下文窗口管理**：PR #16895 和 #14332 显示持续努力使恢复和工具输出限制具备窗口感知——这是智能体行为退化的反复来源。
- **macOS 特有的粗糙边缘**：符号链接检出（#16954）和 macOS 单元测试不稳定（#16091）表明 macOS 上的开发环境需要关注。

---

*本简报基于 2026-11-12 QwenLM/qwen-code 的公开 GitHub 活动生成。*

</details>

---
*本日报由 [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar) 自动生成。*