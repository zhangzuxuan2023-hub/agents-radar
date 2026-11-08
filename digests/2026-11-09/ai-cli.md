# AI CLI 工具社区动态日报 2026-11-09

> 生成时间: 2026-11-08 23:13 UTC | 覆盖工具: 7 个

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

# 跨工具对比报告：AI CLI 开发者工具生态
**日期：2026-11-09**

---

## 1. 生态概览

2026 年末的 AI CLI 工具格局已收敛到一套共同架构——智能体循环、基于 MCP 的工具扩展、子智能体委派、上下文压缩——如今正进入一个差异化不再主要体现在核心能力、而更多体现在可靠性、多身份人体工学和企业就绪度的阶段。值得注意的是，本窗口期内**七款工具均无新的稳定版本发布**，而各处的 issue 数量依然庞大。占主导的跨工具共性故障模式惊人地一致：MCP 生命周期 bug（工具列表陈旧、重连失败、认证丢失）、上下文压缩导致的数据丢失、多账户/多工作区摩擦，以及自动更新回归破坏原本可用的工作流。安全加固与 CI 供应链工作在各 PR 队列中占据显著位置（Gemini CLI、Qwen Code），表明项目正从"能否工作"成熟到"是否安全且可复现"。

---

## 2. 活跃度对比

| 工具 | Issue（24 小时内更新） | PR（24 小时内更新） | 讨论 | 发布状态 |
|---|---|---|---|---|
| **Claude Code** | ~20+（密集；约 20 条来自同一作者的重复 Fable 5.1 报告） | 0 | N/A（未提供） | 无发布 |
| **OpenAI Codex** | ~13+ 条显著（Windows 回归问题簇） | 10（多为机器人驱动的打磨，已关闭） | 8（3 条 Ideas，5 条 Show & Tell） | 无发布 |
| **Gemini CLI** | 10 条重点（智能体子系统密集） | 10（以安全为主；2 个已关闭的功能） | N/A（未提供） | 无发布 |
| **GitHub Copilot CLI** | 14 条更新（含 1 条已关闭） | 0 | N/A（未提供） | 无发布 |
| **OpenCode** | 10 条重点（#59387 有 333 条评论） | 10（功能 + 修复，若干因陈旧而关闭） | N/A（未提供） | 无发布 |
| **Pi** | 10 条已分流的聚类（原生 MCP 1.5.0 回归） | 4（全部已关闭；以维护为主） | 2（Show & Tell） | 无发布 |
| **Qwen Code** | 10 条重点 + CI 机器人噪音（#14724 有 277 条评论） | 10（托管智能体功能 + 修复） | N/A（未提供） | **3 次发布**：nightly、TS SDK 0.1.30、Desktop v0.27.0 |

**说明：**
- Issue 数量取自各摘要的"Hot Issues"部分，仅为指示性，并非详尽总数。
- **Claude Code** 的原始 issue 噪音最高，源于单一作者的重复爆发（约 20 条几乎相同的 Fable 5.1 截断报告）——社区信号被放大，而非真实覆盖面更广。
- **Qwen Code** 是唯一有发布活动的工具，交付了 nightly 以及 SDK 和桌面构建。
- **Pi** 当天明显以维护为主（4 个 PR 全部关闭），而 **OpenCode** 在较小项目中展现出最偏功能的 PR 队列。
- 讨论数据仅对 Codex 和 Pi 有记录；其他摘要未包含讨论数据（标记为 N/A，而非不活跃）。

---

## 3. 共性功能方向

若干需求在各社区独立反复出现——这是真实市场需求最强的信号。

### 多账户 / 多身份支持
- **Claude Code** #27302（453 👍，298 条评论——头号 issue）：同一连接器在不同账户下使用。
- **OpenAI Codex** #20500（118 👍）：按连接器的具名账户并带隐私边界；#51521 无需登出即可切换工作区。
- **Qwen Code / Copilot CLI**：隐含在项目/工作区协调 bug 中。
> **趋同需求：** 账户范围的认证与连接器绑定是跨社区最一致的头号诉求。

### MCP 生命周期健壮性
- **Claude Code**：陈旧的 `tools/list`（#100115、#88172）、会话启动时重连失败（#108451）。
- **Pi**：OAuth 头丢失（#11918）、顺序重连停滞（#11919）、`tool_search` 可见性（#11921）。
- **Qwen Code**：中断轮次后 MCP 工具卡在 DISCONNECTED（#12160 / PR #16661）。
- **OpenCode**：删除凭据后 `GET /provider` 陈旧（#62761）。
- **Gemini CLI**：MCP OAuth TOCTOU 缺口（PR #30071）；超过 128 个工具的上限（#24246）。
> **趋同需求：** 确定性的连接器刷新/重连与优雅降级是整个生态中摩擦最集中的领域。

### 上下文 / Token 经济学
- **OpenAI Codex**：压缩清空历史（#60293）、陈旧请求恢复（#54646）、token 消耗（#56866）、上下文缩减插件（#60297）。
- **Gemini CLI**：约 36.6k token 基线、AST 感知工具化 EPIC（#22745）、策略性提取（#19561）。
- **Pi**：重试错误后上下文用量高估（#10287）、提示缓存预热 PR（约降低 26% 开销）。
- **Qwen Code**：预检超大写入拒绝（#16417）、切片引用（#16173）。
- **OpenCode**：本地模型上压缩中断轮次（#62778）、提示缓存规则（#53927）。
> **趋同需求：** 精确的 token 计量、安全的压缩和缓存控制是普遍的成本/质量痛点。

### 子智能体可靠性与编排
- **Gemini CLI**：轮次上限被报告为成功（#22323）、通用智能体挂起（#21409）、忽略 `maxTurns`（#22267）。
- **OpenCode**：过早"完成"（#48826）、权限排序 bug（#62213）。
- **Qwen Code**：子智能体资源超订（#16526）、IDE 任务列表对齐（#8544）。
- **OpenAI Codex**：压缩恢复陈旧的委派请求（#54646）、智能体树 ID（PR #60190）。
> **趋同需求：** 子智能体是新前沿，也是新的成熟度鸿沟——可观测性、正确终止与并发控制。

### 可配置默认值与配置人体工学
- **Copilot CLI**：默认智能体（#5383）。
- **Claude Code**：可配置的 `@` 导入扩展（#108470）。
- **OpenCode**：拆分配置文件（#19353）、符号链接重载（PR #53932）。
- **Pi**：`/clear` 保留模型/级别（#11925）、条件式 `AGENTS.md` 文件（#11933）。

### 终端/编辑器生态集成
- **Copilot CLI** #5320（👍11）：OSC 7501 状态上报。
- **Gemini CLI** PR #29918：同样的 OSC 7501 请求（已关闭，需重新提交）。
- **Pi** #11934：全屏搜索的 mintty 按键解码。
> **注：** OSC 7501 独立出现在两个社区，是标准化终端集成诉求的强烈信号。

---

## 4. 差异化分析

| 维度 | Claude Code | OpenAI Codex | Gemini CLI | Copilot CLI | OpenCode | Pi | Qwen Code |
|---|---|---|---|---|---|---|---|
| **主要焦点** | 模型行为 + 认证/连接器深度 | 多端桌面/移动一致性 | 智能体运行时正确性 | 会话生命周期与企业级 | 多提供商广度 | 原生 MCP + 扩展性 | 托管智能体平台 |
| **目标用户** | 专业版/企业版 Claude 用户 | 跨设备工作流的专业消费者 | 高级用户、Linux 开发者 | 企业/集成 GitHub 的团队 | 多提供商高级用户 | 扩展/前端构建者 | 托管智能体采用者 |
| **技术姿态** | 模型输出的可靠性；安全分类器调优 | 加固自动更新；沙箱安全 | OS 级沙箱优于工具限制 | 自动化/CI 优先（`-p` 模式） | 提供商抽象深度 | 原生（而非适配器）MCP | 对齐 Codex 的工具模式 + 记忆 |
| **社区特征** | 被动功能点赞；易受刷帖 | 广泛但饱受回归困扰 | 智能体子系统深度剖析 | 来自单一作者的可靠性聚类 | 免费层大声量的长帖 | 已分流的 bug 聚类 | 以 CI 噪音为主 |

**关键差异：**

- **Claude Code** 独特地暴露于*模型行为*类问题（安全分类器过度拦截 #100212、粘贴截断），而非管道问题。其头号 issue 是纯粹的功能需求信号（多账户连接器，453 👍），表明用户群体成熟自信，诉求的是规模化而非修复。
- **OpenAI Codex** 是*多端*属性最强的产品（CLI、桌面、移动、VS Code、Computer Use）——因而也最饱受回归困扰。Windows 自动更新聚类是平台成熟度问题，而非能力问题。
- **Gemini CLI** 和 **Qwen Code** 最具*安全与 CI 意识*：两者都有活跃的供应链加固 PR（fork 密钥、TOCTOU），且都在投入架构转变（Gemini 的 OS 沙箱 + AST 工具化；Qwen 的托管智能体记忆 + 共享工作区）。Qwen 拥有本周期内其他工具无法比拟的、连贯的"托管智能体即产品"路线图。
- **Copilot CLI** 最*偏自动化*（prompt 模式、`--allow-tool`），使其会话生命周期和权限解析器 bug 对 CI 用户而言代价格外高昂。
- **OpenCode** 和 **Pi** 都强调*提供商/扩展性广度*（Venice E2EE、原生 MCP、插件钩子）——Pi 关于自动关闭报告的元 issue #11927 揭示出，尽管技术雄心强劲，治理成熟度仍有差距。
- **Pi** 是本周期内唯一有显著*流程信任*投诉的工具（自动关闭非贡献者的 bug），这是社区健康的警示信号。

---

## 5. 社区动能与成熟度

**最高原始互动：**
- **Claude Code**——单 issue 互动最高（#27302 达 453 👍 / 298 条评论），且 issue 量最密集（尽管有噪音）。功能需求基础成熟；受重复刷帖的卫生问题拖累。
- **OpenCode**——#59387 的 333 条评论免费层长帖是所有工具中单一最大聚类，但反映的是*故障驱动*的互动（配额/重置 bug），而非健康迭代。
- **OpenAI Codex**——互动面最广（issue + 讨论 + show-and-tell），PR 队列最活跃，但成熟度被接连的更新回归削弱。

**快速迭代（交付）：**
- **Qwen Code**——唯一有发布的工具（nightly + SDK + 桌面），且功能 PR 最实在（归档/MCP 自助服务、共享工作区、Mem0 记忆、混合代码模式）。本周期明确的迭代速度领先者。
- **Gemini CLI**——高质量的安全/架构 PR，但发布渠道沉寂。
- **OpenCode**——活跃的功能 PR（Venice E2EE、token 上报、缓存规则）被陈旧 PR 的清理工作所抵消。

**可靠性关注：**
- **Gemini CLI** 和 **OpenCode** 都展现出智能体运行时的成熟度差距（静默的假成功、过早完成），这比表面 bug 更令人担忧——它们削弱了对智能体输出的程序化信任。
- **Pi** 和 **Qwen Code** 都受 CI/流程噪音困扰（自动关闭策略、机器人提交的失败），侵蚀着社区信任与分流清晰度。

**成熟度排名（本窗口期，指示性）：**
1. **Claude Code**——需求成熟，需改进 issue 卫生。
2. **OpenAI Codex**——覆盖面最广，回归脆弱。
3. **Gemini CLI**——安全态势强，运行时可靠性仍在推进。
4. **Qwen Code**——速度最高，平台雄心最大，CI 承压。
5. **Copilot CLI**——自动化用例聚焦，会话生命周期脆弱。
6. **OpenCode**——提供商/多提供商故事强，存在系统性配额/状态 bug。
7. **Pi**——技术雄心强，治理与 MCP 加固滞后。

---

## 6. 趋势信号

**致技术决策者与开发者：**

1. **多身份是下一个平台战场。** 在 Claude Code 和 Codex 中需求一致且点赞高。任何率先交付账户范围连接器/工作区的工具都将赢得企业和多租户工作流。预计这将成为 2027 路线图中的差异化点。

2. **MCP 已从"集成功能"变为"可靠性负债"。** 每个项目都有 MCP 生命周期 bug。各处的根本问题相同：连接器假设工具面静态、会话稳定。评估工具的团队应测试**运行时 MCP 服务器变更、重连和 OAuth 持久化**——这正是产品在健壮性上差异化之处，也是整个生态中最集中的开发者痛点。

3. **上下文压缩是关键安全子系统，而非便利功能。** 空摘要历史丢失（Codex）、陈旧请求恢复（Codex）、用量高估（Pi）和中断轮次的压缩（OpenCode）都指向一类数据丢失/安全 bug。这是新出现的最高严重性风险领域——建议将压缩视为"验证后才信任"的能力，尤其对无人值守和委派的智能体工作流。

4. **子智能体可靠性标志着成熟度壁垒。** 假成功报告（Gemini #22323）、过早完成（OpenCode #48826）、权限排序意外（OpenCode #62213）——以程序化方式消费智能体输出的程序尚不能信任终止状态。构建自动化流水线的团队应增加**对智能体自报成功的独立验证**，而非信任 `status: success`。

5. **自动更新渠道是信任负债。** Codex 的 Windows 回归聚类和 Qwen 的更新器文件锁 bug 表明，*更新后立即*发生的故障对信任的侵蚀不成比例。企业应锁定版本并分阶段推出，而非直接消费自动更新。

6. **Token 经济学正驱动真实的优化工作。** Pi 的提示缓存预热 PR 量化出单次修复可挽回约 26% 的开销；Gemini 和 Codex 都有关于 token 效率的 EPIC/讨论帖。预计提示缓存控制、预检预算检查和精确计量将成为必备功能。

7. **终端/编辑器集成正有机地标准化。** OSC 7501 独立出现在 Copilot CLI 和 Gemini CLI（外加 Qwen 中对 JetBrains ACP 对齐的诉求）中，表明智能体状态上报将收敛到一套通用协议。早期采用者将获得生态集成优势。

8. **安全与供应链加固现已是显性的 PR 优先项。** Gemini CLI 的 fork 密钥泄露修复和 TOCTOU DNS 重绑定补丁，加上 Qwen 的 CI 凭据固定，表明领先项目正将自身的构建/CI 视为攻击面。对采用者而言，这是积极的成熟度信号——优先选择安全响应速度活跃的工具。

9. **治理与流程卫生是次要但真实的信任信号。** Pi 的自动关闭策略投诉和 Qwen 以机器人噪音为主的追踪器都警示：社区信任既取决于分流纪律，也取决于功能交付。评估项目时，可将**其 issue 追踪器的信噪比**作为维护者响应度的代理指标。

10. **本地模型人体工学仍是未解前沿。** OpenCode（#62765、#62778）和 Qwen Code（#16526）都显示本地/自托管工作流缺乏护栏（压缩失败、超订、工具使用薄弱）。云与本地体验之间的差距，对能把小模型智能体行为做对的工具而言是持续的机会。

---

*本报告汇编自 2026-11-09 七个 AI CLI 仓库的社区摘要。计数反映所提供摘要，仅为指示性而非详尽；讨论仅在有数据提供时报告。*

---

## 各工具详细报告

<details>
<summary><strong>Claude Code</strong> — <a href="https://github.com/anthropics/claude-code">anthropics/claude-code</a></summary>

## Claude Code Skills 社区热点

> 数据来源: [anthropics/skills](https://github.com/anthropics/skills)

# Claude Code Skills —— 社区亮点报告
*数据快照：2026-11-09 | 来源：github.com/anthropics/skills*

---

## 1. 热门 Skill 排名

> 注：源数据中未暴露 PR 的评论数（显示为 `undefined`）。以下排名依据 issue 关联情况、更新时效性及交叉引用的讨论量推断得出。

| # | Skill / PR | 功能说明 | 讨论亮点 | 状态 |
|---|---|---|---|---|
| 1 | [mcp-builder 修复 #1742](https://github.com/anthropics/skills/pull/1742) | 修复 MCP ≥2.0 的导入重命名（`streamablehttp_client` → `streamable_http_client`），并通过 `create_mcp_http_client` 配置请求头 | 关联 #1668；经过 1 个月修订后升级 | OPEN |
| 2 | [skill-creator eval 隔离 #1298](https://github.com/anthropics/skills/pull/1298) | 修复触发评估中的误判：多 worker 探测争用、Windows 管道失败、运行时错误误分类 | 已活跃 3 个月以上；与 #556、#1352、#1394 存在重叠 | OPEN |
| 3 | [notion-spec-to-implementation + quantitative-resume-auditor #1245](https://github.com/anthropics/skills/pull/1245) | 将 Notion 规格转为带验收标准的可实施任务；简历评分 skill | 多 skill 打包 PR，10 月 15 日更新 | OPEN |
| 4 | [webapp-testing：移除 shell=True #1980](https://github.com/anthropics/skills/pull/1980) | 消除 `with_server.py` 中的 CWE-78 命令注入 | 出于安全动机；11 月 1 日更新 | OPEN |
| 5 | [docx LibreOffice 超时修复 #1792](https://github.com/anthropics/skills/pull/1792) | `soffice` 超时时上报真实错误；在宣布成功前验证修订标记已移除 | 正确性修复，11 月 1 日更新 | OPEN |
| 6 | [pyxel 复古游戏 skill #525](https://github.com/anthropics/skills/pull/525) | 引导 Claude 通过无头帧检查来构建/调试/验证复古 Python 游戏 | 长期存在（自 3 月起），11 月 1 日仍活跃 | OPEN |
| 7 | [proofcore-contract-auditor #1771](https://github.com/anthropics/skills/pull/1771) | Solidity/Rust 合约静态分析 + TON 上的 Merkle 审计证明 | 新颖的 Web3 垂直方向 | OPEN |
| 8 | [xlsx 外部链接解析 #2019](https://github.com/anthropics/skills/pull/2019) | 解析 `external_links_at_risk` 中工作表作用域的定义名称/别名 | 修复 #2013，11 月 3 日更新 | OPEN |

**要点：** 关注度最高的 PR 绝大多数是**针对现有 skill 的 bug/安全修复**（mcp-builder、skill-creator、webapp-testing、docx、xlsx），而非新 skill 提交——这表明社区已进入*加固*阶段。

---

## 2. 社区需求趋势

从评论最多的 Issue 中提炼出五大需求集群：

| 主题 | 代表性 Issue | 信号 |
|---|---|---|
| **信任与安全边界** | [#492](https://github.com/anthropics/skills/issues/492)（46 条评论）——社区 skill 冒充 `anthropic/` 命名空间 | 仓库中互动量最高的 issue；要求命名空间/验证控制 |
| **组织级 skill 共享 / 分发** | [#228](https://github.com/anthropics/skills/issues/228)（17 👍 8）——在 Claude.ai 组织内共享 skill，无需手动上传 .skill 文件 | 强烈的产品功能需求 |
| **可靠的评估 / 优化工具** | [#556](https://github.com/anthropics/skills/issues/556)（0% 触发率）、[#1352](https://github.com/anthropics/skills/issues/1352)（并行 worker 误判） | 社区正在*测试测试框架本身*——对 skill 质量基础设施的深度投入 |
| **上下文 / token 效率** | [#1487](https://github.com/anthropics/skills/issues/1487)——`claude-api` 急切注入约 156k token | 人们希望 skill 采用惰性 / 渐进式披露 |
| **治理与推理质量** | [#412](https://github.com/anthropics/skills/issues/412) agent 治理、[#1385](https://github.com/anthropics/skills/issues/1385) 质量门禁流水线 | 新兴的“元 skill”方向：安全、审计追踪、对抗性审查 |

次要需求：**文档排版质量**（[#514](https://github.com/anthropics/skills/pull/514)）、**外部 skill 贡献**（[#1328](https://github.com/anthropics/skills/issues/1328)/[#1329](https://github.com/anthropics/skills/issues/1329) `compact-memory`）以及**开发者体验**（[#202](https://github.com/anthropics/skills/issues/202)——skill-creator 应具备可操作性，而非教学性质）。

---

## 3. 高潜力待合并 Skill

尚未合并但势头良好的活跃 PR（近期更新 + 关联 issue 修复）：

1. **[#1980 webapp-testing 安全修复](https://github.com/anthropics/skills/pull/1980)**——CWE-78 修复；小而聚焦、高价值。*可能最早合并。*
2. **[#2019 xlsx 外部链接解析](https://github.com/anthropics/skills/pull/2019)**——修复已提交的 issue（#2013）；11 月 3 日更新。范围清晰的 bug 修复。
3. **[#1977 algorithmic-art wrapAround](https://github.com/anthropics/skills/pull/1977)**、**[#1976 element_discovery](https://github.com/anthropics/skills/pull/1976)**、**[#1975 pptx --cols 校验](https://github.com/anthropics/skills/pull/1975)**——来自同一贡献者（gerardrecinto）的三个紧凑修复，各自关闭一个有编号的 issue。*可批量合并的候选。*
4. **[#1792 docx 超时验证](https://github.com/anthropics/skills/pull/1792)**——正确性 + 验证逻辑；11 月 1 日更新。
5. **[#1742 mcp-builder MCP≥2 支持](https://github.com/anthropics/skills/pull/1742)**——破坏性变更兼容层；关联未关闭的 issue #1668。
6. **[#1681 skill-creator 直接执行](https://github.com/anthropics/skills/pull/1681)** 与 **[#1997 YAML frontmatter 解析](https://github.com/anthropics/skills/pull/1997)**——互补的 skill-creator 修复；均解决真实的 CLI 故障。
7. **[#514 document-typography skill](https://github.com/anthropics/skills/pull/514)**——新 skill 提案；解决普遍存在的输出质量问题（孤行、寡行、编号）。
8. **[#2108 docs：澄清快照/同步状态](https://github.com/anthropics/skills/pull/2108)**——针对同步 skill 模型的文档澄清；低风险。

---

## 4. Skill 生态洞察

> **社区最集中的需求并非新的 skill 类型，而是针对现有 skill 集的*信任、正确性与评估可靠性***——命名空间伪造（#492）、评估误判（#556/#1352）、上下文爆炸（#1487）以及命令注入风险（#1980）共同主导了讨论，这表明生态已从“Claude 能做什么？”转向“我们如何保证现有 skill 安全且可预测地运行？”

---

*所有链接：`https://github.com/anthropics/skills/pull/<n>` 与 `/issues/<n>`。*

---

# Claude Code 社区摘要 — 2026-11-09

## 1. 今日亮点

过去 24 小时内没有新的版本发布或拉取请求合入，但 issue 活动异常活跃。最主要的事件是**约 20 条重复报告集中爆发**，内容均关于 “Fable 5.1” 截断粘贴文本，全部由同一位作者（`Nahuel990`）在几分钟内提交——这一情况值得维护者关注。与此同时，得票最高的开放请求（#27302，多账户 Connector 支持）持续获得社区强力支持，已达 453 👍 和 298 条评论。

---

## 2. 版本发布

过去 24 小时内无新版本发布。

---

## 3. 热门 Issue

1. **[#27302 — 支持多个 Connector 账户](https://github.com/anthropics/claude-code/issues/27302)** `enhancement, area:auth`
   社区明确最青睐的需求：用户希望在 Claude 以及 Web 端 Claude Code 中以不同账户连接同一个 connector。凭借 **453 👍 和 298 条评论**，这是本时间窗口内互动量最高的 issue，表明对多租户/按认证隔离的 connector 支持有强烈需求。

2. **[#100212 — 安全分类器拒绝响应医疗文档相关请求](https://github.com/anthropics/claude-code/issues/100212)** `bug, platform:windows, area:model`
   安全分类器误报，阻断了合法的临床文档工作。对专业用户而言严重性较高——在受监管领域过度拦截是一个反复出现的信任问题。

3. **[#100115 — 桌面端 Code 标签页：MCP 工具列表不更新](https://github.com/anthropics/claude-code/issues/100115)** `bug, platform:macos, area:mcp, area:desktop`
   服务器新增工具后，connector 工具列表不会刷新；关闭再开启 connector 也无济于事。直接影响桌面端 MCP 工作流。

4. **[#88172 — MCP connector 在应用重启前一直返回过期的 `tools/list`](https://github.com/anthropics/claude-code/issues/88172)** `bug, platform:macos, area:mcp`
   与 #100115 相关但更早、范围更广：即使在线路上发起 `server/discover` 调用，也无法更新可用的工具面。这暗示 connector 生命周期管理中存在缓存层 bug。

5. **[#108446 — Fable 5.1 在开头之后停止读取粘贴文本](https://github.com/anthropics/claude-code/issues/108446)** `bug, area:model`
   今日重复爆发中的“标准”报告：模型只读取粘贴内容开头，并基于部分上下文行事。它是少数带有评论的报告之一——值得作为参考 issue 跟踪。

6. **[#108451 — 定时任务：远程 MCP connector 在会话开始时未重新连接](https://github.com/anthropics/claude-code/issues/108451)** `bug, area:mcp, area:routines`
   当可缩容至零的 MCP 服务器（如 Cloud Run）在会话开始时尚未就绪，无头/云端运行会失败，且本轮内不会重试。对无人值守自动化而言是明显的可靠性缺口。

7. **[#108470 — 使 `CLAUDE.md` 中 `@` 导入的文件扩展名可配置](https://github.com/anthropics/claude-code/issues/108470)** `enhancement, area:core`
   请求让 `CLAUDE.md` 中 `@` 导入可解析的文件扩展名变为可配置。改动虽小但实用——与 monorepo 和非标准文件类型相关。

8. **[#108469 — GitHub 集成错误（GH-HMWV22）](https://github.com/anthropics/claude-code/issues/108469)** `bug, github-integration`
   从设置页提交的错误报告，诊断信息极少。细节不足，但 GitHub 集成故障会阻断受影响用户的 PR/issue 工作流。

9. **[#108462 — Fable 5.1 截断（含复现）](https://github.com/anthropics/claude-code/issues/108462)** `bug, has repro, area:model`
   在重复报告中因带有 `has repro` 标签而值得注意——是分诊时最具可操作性的条目。

10. **[#108431 — Fable 5.1 截断（较早的重复）](https://github.com/anthropics/claude-code/issues/108431)** `bug, area:model`
    最早带评论的重复报告之一，有助于还原报告时间线。

> ⚠️ **分诊提示：** Issue #108445–#108470（不含 #108451 和 #108470）是来自同一作者的近乎相同的 Fable 5.1 报告。建议合并至 #108446/#108462，其余作为重复关闭。

---

## 4. 关键 PR 进展

过去 24 小时内无拉取请求更新。

---

## 5. 热门讨论

本时间窗口未提供讨论数据。

---

## 6. 功能需求趋势

- **多账户 / 多租户 connector**（#27302）：最强的单一信号——用户希望同一个 connector 绑定不同账户，意味着在 Web 和 CLI 场景下都需要按账户隔离的认证。
- **可配置的上下文摄取**（#108470）：将 `CLAUDE.md` 的 `@` 导入行为扩展到任意文件扩展名——属于更广泛的“让上下文组装可编程”主题的一部分。
- **MCP 生命周期健壮性**：虽然以 bug 形式提交，但工具过期与重连问题（#100115、#88172、#108451）指向一个隐含的功能诉求——确定性的 connector 刷新/重连语义。

---

## 7. 开发者痛点

1. **MCP connector 过期与生命周期 bug** —— 三个独立 issue（#100115、#88172、#108451）描述了工具不出现、不刷新或不重连的问题。这是开发者摩擦最集中的领域：运行时发生变化的 MCP 服务器破坏了与 agent 之间的信任契约。
2. **安全分类器过度拦截**（#100212）—— 合法的专业工作（医疗文档）被拒绝响应，削弱了对无人值守使用的信心。
3. **长粘贴时模型上下文截断** —— Fable 5.1 系列尽管存在重复刷屏，但反映了真实的工作流中断：用户粘贴日志/代码后模型只基于部分输入行事，迫使用户重新粘贴。
4. **Issue 卫生 / 重复刷屏** —— 一天内同一作者提交约 20 条相同报告；社区信号被稀释，维护者分诊成本上升。建议按标题相似度进行限流或自动去重。

---

*基于 github.com/anthropics/claude-code 在 2026-11-09 的活动生成。无版本发布或 PR 可报告；issue 量主要由模型行为报告和长期存在的 MCP/认证请求构成。*

</details>

<details>
<summary><strong>OpenAI Codex</strong> — <a href="https://github.com/openai/codex">openai/codex</a></summary>

# OpenAI Codex 社区摘要 — 2026-11-09

## 1. 今日亮点
过去 24 小时内没有新版本发布，但问题追踪器被与近期自动更新相关的 Windows 桌面回归报告所占据——浏览器/Computer Use 初始化失败、本地项目消失，以及 `CreateProcessSecurityEnvironment` 阻止所有本地命令执行。除了这些回归之外，社区继续推动多账户/工作区易用性和上下文管理痛点（token 消耗、压缩行为），而 PR 队列显示出稳定的、主要由 `copyberry[bot]` 驱动的打磨工作，涉及 MCP 启动、侧边栏导航和提供商登录。

## 2. 版本发布
过去 24 小时内没有新版本发布。

## 3. 热点问题

1. **[#57641](https://github.com/openai/codex/issues/57641) — 终端 CLI @Browser 失败并提示"browser access host is unavailable"（Windows，Brave）** — 48 条评论，29 👍。今日最热帖：一位 Pro 用户在 Codex CLI 0.164.0 上报告，桌面应用更新至 26.1023.6123.0 后浏览器工具链损坏。高互动量表明该问题在 Windows 上可广泛复现。

2. **[#20500](https://github.com/openai/codex/issues/20500) — 支持每个应用/连接器多个命名账户** — 33 条评论，118 👍（本批次最高点赞数）。一个长期存在的增强请求，要求按连接器选择账户并具有严格的隐私边界——不同于配置文件切换。社区需求强烈。

3. **[#59895](https://github.com/openai/codex/issues/59895) — Windows：应用更新并切回个人账户后本地项目消失** — 33 条评论。Windows 包 26.1103.3517.0 上的严重数据可见性回归。主题上与 #54155 中的 macOS 报告相关。

4. **[#37967](https://github.com/openai/codex/issues/37967) — [已关闭] 远程控制无法附加到实时 CLI 会话** — 27 条评论，36 👍。本周期关闭。影响工作站为主的工作流（CLI 在桌面端，手机用于监控/审批）。关闭对远程工作流用户是一个积极信号。

5. **[#57032](https://github.com/openai/codex/issues/57032) — VS Code/Linux：Codex 在扩展 26.1021.22027 中无法启动其 UI** — 20 条评论，16 👍。Fedora 43 上 VS Code 1.141.1 的扩展启动失败——值得注意，因为 Linux IDE 用户是一个较小但发声的群体。

6. **[#31383](https://github.com/openai/codex/issues/31383) — 启动时市场自动升级后插件钩子以退出码 1 失败** — 18 条评论。Windows 上插件市场自动升级路径中的陈旧缓存/版本不匹配 bug；对任何在类 CI 工作流中依赖钩子的人都很重要。

7. **[#56866](https://github.com/openai/codex/issues/56866) — Codex 5 小时限额在 10 分钟内耗尽** — 11 条评论。使用 gpt-5.6 Terra 时的速率限制消耗。点赞数低但严重性感知高；属于反复出现的速率限制主题（另见 #48394）。

8. **[#59941](https://github.com/openai/codex/issues/59941) — Windows Desktop 26.1103.31037：CreateProcessSecurityEnvironment 0x8007000F 阻止所有本地命令** — 4 条评论。沙箱/安全环境故障，甚至阻止 `Get-Location`；更新后 Windows 桌面用户的硬性阻断。

9. **[#60293](https://github.com/openai/codex/issues/60293) — 手动压缩使用空摘要清空历史记录** — 2 条评论。关于源码构建的新报告：空压缩摘要用空内容替换整个对话历史。上下文管理中的数据丢失类 bug。

10. **[#54646](https://github.com/openai/codex/issues/54646) — 压缩在只读委托研究期间恢复了一个 24 天前的写请求** — 1 条评论。安全相关：一个被委托的只读 agent 在压缩后基于陈旧请求编辑了六个文件。凸显了子 agent 工作流中压缩/上下文隔离的风险。

*其他值得注意的：* [#60209](https://github.com/openai/codex/issues/60209)（Linux 桌面内存泄漏）、[#60250](https://github.com/openai/codex/issues/60250)（Computer Use 捕获错误的 WSLg 窗口）、[#60236](https://github.com/openai/codex/issues/60236)（安全审查阻止 SketchUp SDK 工作流且无恢复路径）。

## 4. 关键 PR 进展

1. **[#60286](https://github.com/openai/codex/pull/60286) — 从命令中心搜索任务历史**（已关闭）。扫描后端历史以进行不区分大小写的任务名称/提示匹配，使较旧的任务在已加载页面之外也可被发现。

2. **[#60280](https://github.com/openai/codex/pull/60280) — 修复附加到共享应用服务器时的 MCP 启动跟踪**（已关闭）。防止客户端在具有不同本地 MCP 配置的情况下附加到共享应用服务器时 TUI 卡在忙碌状态。

3. **[#31045](https://github.com/openai/codex/pull/31045) — 将 MCP 认证发现推迟到启动失败时**（开放）。将依赖网络的 OAuth 发现移出会话初始化，使不可用的可选 HTTP MCP 服务器不会延迟线程创建——直接解决了报告的 Windows Desktop 超时问题。

4. **[#60271](https://github.com/openai/codex/pull/60271) — 当没有可返回的聊天时保持 agent 概览打开**（已关闭）。UX 修复：仅当显示聊天或启动待定时才允许取消以关闭概览；隐藏误导性的 `esc back` 提示。

5. **[#60270](https://github.com/openai/codex/pull/60270) — 当 CLI 模型不在 ChatGPT 模型列表中时发出警告**（已关闭）。当 CLI 模型覆盖不在模型列表中时，为 ChatGPT 账户用户提供启动警告，并给出拼写建议。

6. **[#60190](https://github.com/openai/codex/pull/60190) — 暴露 agent 树 ID 和环境移除**（已关闭）。添加 `CodexThread::agent_tree_id()`（由根和原生子级共享，树加载时生成新 ID）和 `EnvironmentManager::remove_environment()`——对编排器有用的 API 表面。

7. **[#60188](https://github.com/openai/codex/pull/60188) — 允许为增量 Code Mode 工具消息覆盖模型**（已关闭）。添加可配置的 `exec_tools_update_hint` / `exec_tools_removed_definitions_header`，用于渲染 Code Mode 目录更新和移除。

8. **[#60186](https://github.com/openai/codex/pull/60186) — 允许在登录期间创建自定义提供商**（已关闭）。在登录选择器中添加"创建自定义提供商或网关"，移除了先前需要预保存网关配置的要求。

9. **[#60181](https://github.com/openai/codex/pull/60181) — 侧边栏标签的键盘和鼠标导航**（已关闭）。添加可配置的 `sidebar_up`/`sidebar_down` 操作，支持循环、leader 快捷键（`Shift-Tab`/`Tab`、`Up`/`Down`、`k`/`j`）和鼠标滚轮标签切换。

10. **[#60206](https://github.com/openai/codex/pull/60206) — 将 Bazel 核心单元测试分片从 8 增加到 16**（已关闭）。CI 吞吐量改进；虽小但表明测试套件规模在增长。

## 5. 热门讨论

**想法**
- **[#60240](https://github.com/openai/codex/discussions/60240) — 跨 Codex 和 Work 的个人 dot 的可配置权限** — 提议按服务范围设置确认每个操作/受限/完全访问层级。
- **[#60239](https://github.com/openai/codex/discussions/60239) — 独立于 Windows 的 OpenAI 原生工作区** — 对 Windows 权限提示和交互中断感到沮丧；要求平台中立的工作区。
- **[#60238](https://github.com/openai/codex/discussions/60238) — 在外部应用中识别个人 dot 的安全方式** — 请求为 agent 发起的第三方应用请求提供可验证的身份/授权。

**展示与分享**
- **[#60264](https://github.com/openai/codex/discussions/60264) — Codex 将报告发布为网页并回复链接（远程 MCP）** — BrewPage MCP 集成（托管，无需 API 密钥）以替代大段文字；作者披露了关联关系。
- **[#60116](https://github.com/openai/codex/discussions/60116) — Pushary：从手机回答 Codex 审批** — 付费托管服务，使用原生钩子 + MCP 将 shell/`apply_patch`/MCP 审批路由到移动端；作者披露了商业利益。
- **[#60185](https://github.com/openai/codex/discussions/60185) — agent-recap：跨 Codex 和 Claude Code 转录的本地搜索** — 基于浏览器，无需账户，可搜索两个工具的 JSONL 转录。
- **[#60297](https://github.com/openai/codex/discussions/60297) — Better Codex Context (BCC) 插件** — 旨在减少 token 使用并解决大型、长期项目中的"卡住"目标。
- **[#60282](https://github.com/openai/codex/discussions/60282) — Worktree receipt** — Python/Git 检查同一提交下 worktree 之间未跟踪的本地设置文件漂移；作者披露了 Zozo HQ 关联关系。

*（此时间段内无问答帖。）*

## 6. 功能请求趋势

- **多身份与多工作区易用性**：每个连接器的命名账户（#20500，118 👍）、无需登出即可切换工作区（#51521，现已关闭），以及跨 Desktop/Mobile 的项目/账户协调（#35972、#54155）。#59895 和 #54155 报告显示这也是一个数据可见性 bug 来源。
- **上下文与记忆管理**：压缩正确性（#60293、#54646）、token 成本降低插件（#60297），以及长期运行 agent 的持久记忆耐久性（#59687）。
- **编排与子 agent**：用于协调多个 Codex 对话的"Master Chat"/项目编排器（#60088），以及 API 级支持（#60190 agent 树 ID）。
- **速率限制控制**：明确选择退出自动消耗已购买积分（#48394），以及对限额快速耗尽的担忧（#56866）。
- **权限与身份范围**：按服务可配置的权限和外部应用的可验证 agent 身份（#60238–#60240）。

## 7. 开发者痛点

- **自动更新后的 Windows 桌面回归**是最响亮的集群：浏览器主机不可用（#57641）、Computer Use/Chrome 初始化失败（#56924、#60250、#60254）、`CreateProcessSecurityEnvironment` 阻止本地命令（#59941）、npm 启动器 `EPERM: lstat 'C:\'`（#60281），以及本地项目消失（#59895）。这种模式——更新 26.10xx/26.11xx 后立即出现故障——侵蚀了对自动更新渠道的信任。
- **沙箱/审批摩擦**：已批准的 `filesystem.write` 仍在 `git init` 时因 EPERM 失败（#60162）、安全审查在无解释或恢复的情况下阻止先前可用的工作流（#60236），以及凭据交接被自动审查阻止（#53487）。
- **上下文完整性**：空压缩摘要破坏历史记录（#60293）和压缩后陈旧请求恢复（#54646）是最令人担忧的——两者都是数据丢失/安全类问题，而非表面问题。
- **跨界面状态分歧**：移动端 Remote 与 Desktop 项目分配（#35972），以及 UI 声称附加了本地文件夹而聊天在云工作区中运行（#58710）。
- **资源卫生**：Linux 桌面在切换聊天时内存泄漏（#60209），以及 macOS 上 `node_repl` 累积数千个空 `.tmpXXXXXX` 目录（#53196）。
- **插件市场自动升级中的陈旧缓存/版本偏差**在会话中期破坏钩子（#31383）。

---
*摘要根据 2026-11-09 的 github.com/openai/codex 活动汇编。评论/点赞数反映所提供快照。*

</details>

<details>
<summary><strong>Gemini CLI</strong> — <a href="https://github.com/google-gemini/gemini-cli">google-gemini/gemini-cli</a></summary>

# Gemini CLI 社区摘要 — 2026-11-09

## 1. 今日亮点

过去 24 小时内没有新版本发布，但 issue 活动仍集中在 **agent 子系统**——子代理可靠性、轮次限制处理以及工具/技能发现占据了队列前列。若干 P1 级 bug（通用代理挂起、误报“GOAL”成功、Wayland 浏览器失败）处于 `need-retesting` 状态，表明修复工作正在进行中。在 PR 方面，安全加固（Windows git diff 绕过、fork 的 CI 密钥暴露、MCP OAuth 调度器路由）领跑队列。

## 2. 版本发布

过去 24 小时内无新版本发布。

## 3. 热门 Issue

1. **[#22323](https://github.com/google-gemini/gemini-cli/issues/22323) — 子代理 MAX_TURNS 恢复被报告为 GOAL 成功（P1，13 条评论，👍2）**
   `codebase_investigator` 子代理在尚未进行任何分析就触及轮次限制的情况下，却报告 `status: "success"` / `Termination Reason: "GOAL"`。这掩盖了真实失败并损害了对 agent 输出的信任——这是当日评论最多的 issue，对于任何以编程方式依赖子代理结果的用户来说都是一个严重的正确性 bug。

2. **[#21409](https://github.com/google-gemini/gemini-cli/issues/21409) — 通用代理挂起（P1，8 条评论，👍8）**
   任何对通用代理的委派（甚至简单的文件夹创建）都会无限期挂起。队列中 👍 数最高，表明用户影响面广泛；变通方法是指示模型不要委派给子代理，但这会削弱预期的 agent 架构。

3. **[#19873](https://github.com/google-gemini/gemini-cli/issues/19873) — 零依赖 OS 沙箱与执行后意图路由（P2，9 条评论）**
   提议顺应 Gemini 3 对 bash 的原生亲和力，在操作系统层面对 shell 执行进行沙箱化，而不是限制工具。具有重要架构意义——若被采纳，将重塑 CLI 在能力与安全之间的权衡方式。

4. **[#21968](https://github.com/google-gemini/gemini-cli/issues/21968) — Gemini 未充分利用技能和子代理（P2，7 条评论）**
   虽为轶事但颇具说服力：自定义技能（“gradle”、“git”）除非显式调用否则不会被使用。这是一个提示/工具选择对齐问题，直接限制了可扩展性系统的价值。

5. **[#22745](https://github.com/google-gemini/gemini-cli/issues/22745) — 评估 AST 感知的文件读取、搜索与映射（P2，7 条评论）**
   该 EPIC 正在研究 AST 感知工具是否能减少因读取错位而浪费的轮次和 token 噪音。这是一个强烈信号，表明团队正在工具层而非仅在提示层思考上下文效率。

6. **[#22267](https://github.com/google-gemini/gemini-cli/issues/22267) — 浏览器代理忽略 `settings.json` 覆盖（P2，5 条评论）**
   `AgentRegistry` 正确读取了合并后的设置，但 `BrowserManager` 将其丢弃（例如 `maxTurns`）。静默不生效的配置是经典的开发者信任杀手。

7. **[#21983](https://github.com/google-gemini/gemini-cli/issues/21983) — 浏览器子代理在 Wayland 上失败（P1，4 条评论）**
   Linux/Wayland 用户得到的是“GOAL”终止，没有可用的浏览器会话。平台特定的 P1，阻碍了日益增长的桌面 Linux 用户群体。

8. **[#24246](https://github.com/google-gemini/gemini-cli/issues/24246) — 工具数超过 128 时出现 400 错误（P2，3 条评论）**
   工具数量爆炸（来自 MCP 服务器和技能）触及硬性 API 上限，且无优雅降级。随着生态增长，这将成为高级用户的扩展性壁垒。

9. **[#22672](https://github.com/google-gemini/gemini-cli/issues/22672) — 代理应停止/劝阻破坏性行为（P2，3 条评论）**
   模型偶尔在存在更安全替代方案时诉诸 `git reset`/`--force`。与安全相关，对于针对真实仓库运行 CLI 的团队高度相关。

10. **[#18836](https://github.com/google-gemini/gemini-cli/issues/18836) — 用基于文件的持久化任务追踪替换 WriteToDo（P3，2 条评论）**
    上下文内的 todo 列表会遭受“上下文腐化”并在会话间消失。基于文件的 CRUD 追踪器可解决记忆丢失和 token 成本问题——这是长时间运行任务的基础性改进。

## 4. 关键 PR 进展

1. **[#30095](https://github.com/google-gemini/gemini-cli/pull/30095) — fix: 防止 Windows git diff 权限绕过（P1，安全）**
   提取共享的 git 安全验证，并拒绝在 Windows 上被误分类为只读的危险参数。队列中优先级最高的 PR；直接堵住一个安全漏洞。

2. **[#30090](https://github.com/google-gemini/gemini-cli/pull/30090) — ci(chained_e2e): 不向 fork 控制的代码暴露 GEMINI_API_KEY**
   根据触发运行的 head 仓库对 artifact 和 API key 进行门控，堵住 `workflow_run` 链中的供应链风险。关键 CI 加固。

3. **[#30071](https://github.com/google-gemini/gemini-cli/pull/30071) — 通过安全调度器路由 MCP OAuth 获取**
   修复 TOCTOU DNS 重绑定缺口：MCP OAuth 元数据/注册获取在连接时绕过了重新验证。对任何使用远程 MCP 服务器的用户都具有安全相关性。

4. **[#30068](https://github.com/google-gemini/gemini-cli/pull/30068) — fix(cli): 将上下文管理设置转发至核心**
   已配置的 400k token 输出掩码阈值被静默丢弃，改用 50k 默认值。改动很小，但对长会话行为影响巨大。

5. **[#27423](https://github.com/google-gemini/gemini-cli/pull/27423) — feat(core): 可配置的每工具调用超时（`tools.callTimeout`）**
   为任何单次工具调用设置墙钟时间上限，解决无界 shell 命令问题。长期请求（#3375）且带有 `help wanted` 标签——适合首次贡献的目标。

6. **[#29918](https://github.com/google-gemini/gemini-cli/pull/29918) — feat(cli): OSC 7501 终端状态报告（已关闭）**
   向支持的终端报告 working/blocked/idle 状态，使多标签页用户能看到哪个会话需要关注。对高级用户的良好 UX 打磨；虽已关闭但值得关注重新提交。

7. **[#29919](https://github.com/google-gemini/gemini-cli/pull/29919) — feat(cli): `--append-system-prompt`（已关闭）**
   允许用户添加项目上下文而不替换整个系统提示（不同于 `GEMINI_SYSTEM_MD`）。已关闭，但底层需求——增量式提示定制——仍未解决。

8. **[#29912](https://github.com/google-gemini/gemini-cli/pull/29912) — fix(core): 从 `web_fetch` URL 中去除尾部标点（已关闭）**
   逗号分隔的 URL 列表产生了受污染的 URL（`https://example.com/,`），导致下游失败。针对常见 agent 模式的小型正确性修复。

9. **[#30066](https://github.com/google-gemini/gemini-cli/pull/30066) — ci: package.json scripts 中的惰性金丝雀（安全 PoC，请勿合并）**
   故意的 PoC，证明链式 E2E 工作流会在自托管 runner 上执行 fork 分支代码。与 #30090 配套；此处的安全响应比 PR 本身更重要。

10. **[#29258](https://github.com/google-gemini/gemini-cli/pull/29258) 与 [#29062](https://github.com/google-gemini/gemini-cli/pull/29062) — 陈旧的 nightly 版本升级**
    自动发布 PR 处于陈旧状态（0.61.0/0.56.0 nightlies）。值得作为发布卫生噪音标记；#29062 已作为陈旧关闭。

## 5. 热门讨论

本期未提供讨论数据；本节省略。

## 6. 功能请求趋势

- **基于文件的持久化任务/记忆追踪**（#18836、#18287）：替换上下文内 todo 列表，并探索跨并行子代理的共享记忆。
- **AST 感知工具**（#22745、#22746、#22747）：一个协调的 EPIC，推动语法感知的读取、搜索和代码库映射，以减少轮次和 token 膨胀。
- **节省 token 的上下文管理**（#19561）："tactful extraction" 层级（grep 优先、精准读取）以对抗每轮约 36.6k token 的基线。
- **代理自我意识与可发现性**（#21432、#18283、#18285）：CLI 应了解自身的标志/热键，附带示例子代理，并支持通过 `settings.json` 发现子代理。
- **子代理可观测性**（#22598、#21763）：子代理轨迹应出现在 `/chat share` 和 `/bug` 报告中。
- **安全护栏优于硬性限制**（#19873、#22672）：沙箱加意图路由而非工具排除；劝阻破坏性 git/数据库操作。
- **按工作区策略**（#18397）：范围化的自动批准策略而非单一全局策略。

## 7. 开发者痛点

- **子代理可靠性是首要挫败点。** 委派时挂起（#21409，👍8）、触及轮次限制时误报成功（#22323）、Wayland 浏览器失败（#21983）以及被忽略的 `maxTurns` 覆盖（#22267）都指向 agent 运行时的成熟度差距。用户因静默失败而损失数小时。
- **不生效的配置。** 浏览器代理丢弃 `settings.json` 覆盖（#22267）以及 CLI 丢弃 `contextManagement` 阈值（#30068）侵蚀了对文档化配置项的信任。
- **上下文与 token 经济学。** 约 36.6k token 的基线、消防水管式的文件读取（每轮 +15k token）以及 todo 列表中的上下文腐化（#19561、#18836）是反复出现的成本/质量投诉。
- **技能/子代理利用不足。** Gemini 很少在未被提示的情况下调用自定义技能或子代理（#21968），使可扩展性投入显得浪费。
- **规模上限。** 工具数超过 128 时的 400 错误（#24246）是随着 MCP 服务器和技能倍增而出现的壁垒。
- **工作区卫生。** 模型在各目录散落临时脚本（#23571）在干净提交前产生清理开销。
- **代理自我知识缺口。** 不准确的 CLI 标志/热键指引（#21432）和不完整的 `/bug` 上下文（#21763）使该工具无法很好地指导自身。

---
*摘要基于 2026-11-08/09 的 github.com/google-gemini/gemini-cli 活动生成。注：所示 issue/PR 队列高度集中于 agent 和安全；发布渠道平静。*

</details>

<details>
<summary><strong>GitHub Copilot CLI</strong> — <a href="https://github.com/github/copilot-cli">github/copilot-cli</a></summary>

# GitHub Copilot CLI — 社区文摘
**日期：2026-11-09**

---

## 1. 今日亮点

过去 24 小时内没有新版本发布，但 issue 追踪器依然活跃，共有 14 个 issue 有更新，其中包括一个已 **CLOSED** 的修复，解决了捆绑的 ripgrep 在 16KB 页 ARM64 内核（Asahi Linux）上崩溃的问题。大部分活动集中在**会话管理可靠性**上——来自同一作者（@nima-ap）的多份报告描述了扩展、技能和会话无法正确初始化或恢复的问题。一个颇受欢迎的终端集成功能请求（OSC 7501 状态报告，👍11）也获得了更多关注。

---

## 2. 版本发布

*过去 24 小时内无新版本发布。*

---

## 3. 热门 Issue

1. **[#4977] [CLOSED] 捆绑的 ripgrep 在 16KB 页 ARM64 内核（Asahi Linux）上因 jemalloc “Unsupported system page size” 而中止** — *本批次中唯一已关闭的条目。* jemalloc 页大小不匹配导致 `rg` 在 Apple Silicon 上的 Linux 环境中无法使用。社区反应：👍1，3 条评论。重要性在于它阻碍了日益增长的 ARM64 Linux 用户群体的工具链。 [链接](https://github.com/github/copilot-cli/issues/4977)

2. **[#5320] [OPEN] 支持为 herdr-app/herdr-core 提供 OSC 7501 agent 状态报告** — 本周期互动量最高的 issue（👍11，3 条评论）。请求原生终端状态报告，以便终端能够显示 Copilot 会话活动。这表明对更深入的终端生态集成的需求。 [链接](https://github.com/github/copilot-cli/issues/5320)

3. **[#5219] [OPEN] 会话卡死：使用未知模型 id 的 task 子代理回退到 128k 默认上限并导致回合失败** — 使用无法识别模型 id 的 `task` 子代理会默认使用 128k 上下文，并以提示预算错误导致整个回合失败。对长时间运行的交互式会话而言严重性较高。4 条评论。 [链接](https://github.com/github/copilot-cli/issues/5219)

4. **[#5100] [OPEN] 一次 120 秒 host-ack 超时后，会话事件投递永久失败** — 单次 host-ack 超时就会使会话在恢复之前无法使用。这是影响长时间交互式会话的可靠性问题。3 条评论。 [链接](https://github.com/github/copilot-cli/issues/5100)

5. **[#4378] [OPEN] 云代理：在 GHEC 数据驻留环境下，MCP 注册表策略获取因 401/403 失败** — 在启用数据驻留的 GHEC（`*.ghe.com`）上，所有用户配置的 MCP 服务器都被静默丢弃。影响企业用户，且由于是静默失败而难以诊断。 [链接](https://github.com/github/copilot-cli/issues/4378)

6. **[#5380] [OPEN] 通过 `create_session` 创建的会话在用户切换到它们之前不会启动扩展** — 通过编程方式创建的新会话启动时没有扩展工具、技能或钩子。这是 `create_session` 一系列 bug 的一部分。 [链接](https://github.com/github/copilot-cli/issues/5380)

7. **[#5386] [OPEN] 通过 `create_session` 创建的会话在用户切换过去后，执行 `close_session` 后无法恢复** — 与 #5380 配套；`/resume` 无法重新关联本地会话。进一步印证了会话生命周期可靠性这一主题。 [链接](https://github.com/github/copilot-cli/issues/5386)

8. **[#5384] [OPEN] 会话状态工件存储将项目交付物遗留在活动 Git 仓库之外** — 代理将交付物写入 `~/.copilot/session-state/<id>/files/` 而非工作树，导致文件对 Git 不可见。这是实际工作流/数据丢失方面的隐患。 [链接](https://github.com/github/copilot-cli/issues/5384)

9. **[#5383] [OPEN] 允许设置默认 Agent** — 功能请求：配置默认 agent，而非按会话激活，与现有的模型/推理/上下文默认设置保持一致。摩擦低，一致性论据清晰。 [链接](https://github.com/github/copilot-cli/issues/5383)

10. **[#5381] [OPEN] 在提示模式下，扩展工具权限请求永久等待；`--allow-tool` 无法指定扩展工具** — `copilot -p` 在扩展注册的工具权限请求上无限挂起，且 `--allow-tool` 无法预先授权这些工具。破坏了非交互式自动化。 [链接](https://github.com/github/copilot-cli/issues/5381)

*同样值得注意：* [#5379] PowerShell 审批解析器对简单的 git 命令过度提示（Windows 权限摩擦），以及 [#5382] 扩展技能在 `new_context` 之后消失。

---

## 4. 关键 PR 进展

*过去 24 小时内没有 pull request 更新（0 项）。*

---

## 5. 热门讨论

*本周期未提供讨论数据。*

---

## 6. 功能请求趋势

- **配置默认值与一致性：** 请求将按会话的设置（例如默认 agent，#5383）提升为一等默认值，与现有的模型/推理/上下文默认设置保持一致。
- **终端与编辑器生态集成：** OSC 7501 状态报告（#5320）以及在桌面应用中渲染 `postToolUse` 钩子消息（#5374）表明，对 Copilot CLI 将状态和输出呈现到宿主环境的需求日益增长。
- **企业/MCP 可配置性：** GHEC 数据驻留的 MCP 策略支持（#4378）反映了企业部署需求。
- **扩展/技能生命周期控制：** 多个 issue 请求在会话创建、上下文清除和恢复过程中，扩展和技能行为可预测。

---

## 7. 开发者痛点

- **会话生命周期脆弱：** 本周期的主导主题。会话因子代理模型解析而卡死（#5219），在一次 host-ack 超时后变得不可用（#5100），`create_session` 会话无法启动扩展（#5380）、关闭后无法恢复（#5386），或在 `new_context` 后无法保留技能（#5382）。这些问题共同侵蚀了人们对长时间运行和编程式工作流的信任。
- **非交互/自动化受阻：** 提示模式在扩展工具权限上挂起，且无法通过 `--allow-tool` 预先授权（#5381）——这是 CI 和脚本化使用的阻塞点。
- **Windows 上的权限提示疲劳：** 过于激进的 PowerShell 审批解析迫使对简单的 git 命令进行完整脚本审查（#5379）。
- **企业环境中的静默失败：** 在 GHEC 数据驻留环境下，MCP 服务器在没有明确错误的情况下被丢弃（#4378）。
- **工件放置混乱：** 交付物被遗留在 Git 工作树之外（#5384），存在输出丢失或未被追踪的风险。

---

*本文摘基于过去 24 小时内更新的 14 个 issue 和 0 个 PR 生成。此期间无版本发布或讨论可用。*

</details>

<details>
<summary><strong>OpenCode</strong> — <a href="https://github.com/anomalyco/opencode">anomalyco/opencode</a></summary>

# OpenCode 社区文摘 — 2026-11-09

## 今日亮点

过去 24 小时内没有新版本发布，但 issue 追踪器被两大主题占据：一个规模庞大的免费额度故障帖（#59387，目前已达 333 条评论），它已成为配额问题的实际上的 bug 报告中心；以及一波围绕工具权限、子代理生命周期和提供商路由的 V2 桌面/TUI 回归问题。在 PR 方面，维护工作包括针对托管代理的法律/隐私文档工作、向 OpenCode Console 报告 token 使用量，以及新增提供商支持（Venice E2EE）和针对嵌入式仓库的快照修复。

---

## 版本发布

过去 24 小时内没有新版本发布。

---

## 热门 Issue

1. **[#59387](https://github.com/anomalyco/opencode/issues/59387) — [zen] 免费额度问题：请先阅读此帖（333 条评论，👍25）** — 免费额度问题的中心讨论帖。用户遇到“Free usage exceeded”/“limit reached”错误，而每日重置实际上从未发生，此外还有 V1→V2 桌面版兼容性错误。如此大的讨论量表明这是一个持续存在的系统性配额/重置 bug，而非孤立报告。

2. **[#62761](https://github.com/anomalyco/opencode/issues/62761) — `DELETE /auth/{id}` 导致 `GET /provider` 状态陈旧（严重程度：高）** — `opencode serve` 删除了磁盘上的凭据，但在重启之前仍继续报告该提供商已连接，并且对未知 ID 返回 `200 true`。这是一个真实影响无头/HTTP 集成的服务器状态正确性 bug。

3. **[#62708](https://github.com/anomalyco/opencode/issues/62708) — 第二个 openai-compatible 提供商被路由到第一个提供商的 baseURL** — 在 Windows 上，第二个 `@opencode/ai/providers/openai-compatible` 提供商会在无提示的情况下将请求发送到错误的 baseURL。对于该包而言，多提供商配置实际上已失效。

4. **[#48826](https://github.com/anomalyco/opencode/issues/48826) — V2 子代理在有待处理后台工作时被过早标记为“已完成”** — 启动后台 shell 或嵌套子代理的子代理会过早终止其回合，并在收集结果之前报告完成。对于任何基于 V2 后台执行构建的用户来说，这是一个正确性问题。

5. **[#62213](https://github.com/anomalyco/opencode/issues/62213) — 内置 tester 策略覆盖代理写入许可（last-match-wins）** — `opencode run --agent` 会拒绝非测试路径上的 `write`/`edit`，因为注入的 `edit * deny` 在 last-match-wins 排序下获胜。对于自定义子代理来说，这种权限行为令人困惑且难以调试。

6. **[#23655](https://github.com/anomalyco/opencode/issues/23655) — 为 Go 服务添加 Responses API 支持（👍31）** — 本批次中得票最高的开放功能请求。用户希望 Go 服务能够提供 OpenAI Responses API；兴趣强烈且持续。

7. **[#62556](https://github.com/anomalyco/opencode/issues/62556) — Gemini `cachedContent` 导致每次请求失败** — 将 `cachedContent` 与 `systemInstruction`/`tools` 一起设置会从 Gemini/Vertex 收到 400。对于受影响的配置，这完全破坏了 Gemini 提示缓存。

8. **[#62619](https://github.com/anomalyco/opencode/issues/62619) — Google 内容过滤完成被静默重试约 24 次，每 10 秒一次（v1.16.2）** — 确定性的内容过滤失败进入静默无限重试循环，没有错误日志、没有消息记录、没有退避。浪费配额并掩盖真实故障。

9. **[#62778](https://github.com/anomalyco/opencode/issues/62778) — 自动压缩以“Nothing to compact yet”中止活动回合** — 在本地提供商（Ollama）达到上下文上限时，自动压缩可能失败并终止整个回合。这是本地模型用户长时间会话的阻塞性问题。

10. **[#62765](https://github.com/anomalyco/opencode/issues/62765) — 没有可用的本地模型（qwen3、qwen3-coder、llama3.1）** — 用户报告本地模型只能读写文件，且“什么都解决不了”。这反映出对小规模本地模型工具使用质量的持续不满；已作为待分类关闭。

*同样值得关注：* [#62644](https://github.com/anomalyco/opencode/issues/62644)（Edit 工具在多个 LLM 上持续失败）、[#62666](https://github.com/anomalyco/opencode/issues/62666)（TUI 插件的 solid-js 信号无法重新渲染 npm 包插件）、[#55529](https://github.com/anomalyco/opencode/issues/55529)（codemode 在每次树变更时重新注入约 8.8k 字符的工具目录）。

---

## 关键 PR 进展

1. **[#62788](https://github.com/anomalyco/opencode/pull/62788) — feat(core): 向 OpenCode Console 报告订阅提供商 token 使用量** — 向 Console 报告订阅 token 使用量；是 opencode-console#6356 的配套变更。为订阅用户提供用量可见性。

2. **[#62786](https://github.com/anomalyco/opencode/pull/62786) — feat(ai): 支持 Venice 端到端加密模型** — 添加 Venice `e2ee-*` 模型支持。扩展注重隐私的提供商选项。

3. **[#62785](https://github.com/anomalyco/opencode/pull/62785) — fix(core): 嵌入式仓库状态失败时捕获快照** — 修复 #61383；处理被记录为 gitlink 的未跟踪嵌套仓库，使快照捕获不再因 `git diff-files` 失败。

4. **[#62790](https://github.com/anomalyco/opencode/pull/62790) — fix(site): 将法律历史中的托管代理条目指向 dev 的提交** — 重新生成法律历史，使托管代理条目引用正确的 squash 提交。文档维护。

5. **[#62789](https://github.com/anomalyco/opencode/pull/62789) — docs(legal): 涵盖托管代理会话与保留** — 更新托管代理（OpenCode Console）的隐私政策。与任何使用托管会话的用户相关。

6. **[#62784](https://github.com/anomalyco/opencode/pull/62784) — docs(console): 记录 Watches** — 添加 `console/watches.mdx` 客户文档；明确等到 Watches 向客户开放后才发布。预示着即将推出的 Console 功能。

7. **[#53975](https://github.com/anomalyco/opencode/pull/53975) — fix(core): 将无法识别的 OpenAI 兼容模型设置作为请求体字段转发** — 将 AI SDK 的 `openai-compatible` 替换为原生包，以便未知模型设置能够传递。修复 #53677；与 #62708 中的路由 bug 相关。

8. **[#53927](https://github.com/anomalyco/opencode/pull/53927) — feat(core): 添加提示缓存规则** — 添加顶层 `cache` 配置（参考 #51109）。直接响应提示缓存控制请求。

9. **[#53973](https://github.com/anomalyco/opencode/pull/53973) — fix(app): 避免为被动关注启动历史位置** — 防止恢复的非活动标签页和重连扫描启动冷 Location 图。修复 #44272；性能导向。

10. **[#53932](https://github.com/anomalyco/opencode/pull/53932) — fix(core): 重新加载符号链接的配置目标** — 配置监视现在会解析链接目录之外的符号链接目标。修复 #53920。

*注意：* 若干较早的 PR（#53906、#53913、#53925、#53929、#53970、#53997、#54011）在此窗口期内被 `automated-pr-cleanup` 移至 CLOSED——这很可能是陈旧 PR 清理，而非拒绝底层修复。

---

## 热门讨论

本期未提供 Discussions 数据。

---

## 功能请求趋势

- **模型/提供商广度：** Go 服务的 Responses API 支持（#23655，得票最高），以及提供商特定的缓存修复（Gemini `cachedContent` #62556）和 Venice E2EE 支持（#62786）。
- **配置易用性：** 将 `opencode.jsonc` 拆分到多个文件（#19353）以应对大型 MCP/agent 配置；符号链接配置重载（#53932）；提示缓存规则（#53927）。
- **TUI/桌面 UX 打磨：** 切换 plan/build 时保留模型（#54375）、更紧凑的编辑器（#52339）、可重排的会话标签页（#62646）、真正可用的 which-key 面板（#62650）。
- **插件/代理可扩展性：** 按代理过滤工具可见性（#24953）、重放期间正确的插件 plan 投影（#53929）、TUI 插件信号响应性（#62666）。
- **本地优先工作流：** 让已配置的本地模型无需发现即可用（#54011）、修复本地提供商上的自动压缩（#62778）。

---

## 开发者痛点

1. **免费额度配额/重置已损坏且投诉量大。** #59387 已膨胀至 333 条评论，另有 #58687 和 #62658 中的变体（俄语报告称自 2026-10-31 起额度永久耗尽）。重置从不发生的行为是讨论量最高的单一投诉。
2. **多提供商与认证状态不可靠。** `DELETE /auth` 后 `GET /provider` 状态陈旧（#62761），以及 Windows 上 openai-compatible baseURL 交叉错接（#62708），使提供商配置变得脆弱。
3. **V2 子代理/工具权限语义令人困惑。** 子代理提前完成（#48826）和 last-match-wins 编辑拒绝（#62213）造成仅凭配置难以推理的行为。
4. **静默失败掩盖真实错误。** 无日志的内容过滤重试循环（#62619）、Edit 工具失败（#62644）以及压缩中止回合（#62778）都缺乏可操作的诊断信息。
5. **小型本地模型在代理任务上表现不佳。** #62765 捕捉到一种反复出现的不满：qwen/llama 级本地模型能读写文件，但不能有意义地解决任务——这推动了对更好工具使用可靠性和模型指导的需求。

</details>

<details>
<summary><strong>Pi</strong> — <a href="https://github.com/earendil-works/pi">earendil-works/pi</a></summary>

# Pi 社区摘要 — 2026-11-09

## 1. 今日亮点

2026-11-08 涌现了大量已分类的 issue，其中最引人注目的是 Pi 1.5.0 中一系列原生 MCP 问题——OAuth 头丢失、串行重连超时，以及 `tool_search` 无法解析直接暴露的工具。提供商兼容性同样引发关注：OpenRouter 的 `native_finish_reason` 被丢弃，Mistral 的数组式 `delta.content` 被渲染为 `[object Object]`。一个值得注意的元 issue（#11927）指出该仓库将 bug 报告自动关闭为“not planned”的做法。

## 2. 版本发布

过去 24 小时内无新版本发布。

## 3. 热门 Issue

1. **[#10287](https://github.com/earendil-works/pi/issues/10287) — 可重试网络错误后 `getContextUsage()` 严重高估上下文用量**（OPEN，👍2）
   WebSocket/fetch 失败后 token 统计从约 42k 跳升至 330k，这很可能破坏自动压缩决策。严重性高，因为长时间运行的 agent 依赖准确的上下文统计。

2. **[#11918](https://github.com/earendil-works/pi/issues/11918) — 尽管 OAuth token 有效，MCP 工具调用仍返回空结果**
   对于 Google Workspace MCP 服务器，登录后 session/auth 头未被携带。包含跨三台服务器的详细字段证据——是 1.5.0 中高影响认证回归的有力复现。

3. **[#11919](https://github.com/earendil-works/pi/issues/11919) — MCP 重连在未认证服务器上阻塞 2–4 分钟**
   重新认证一台服务器会触发所有服务器的串行重新握手，每个都停滞至超时。多服务器 MCP 用户每次会话损失数分钟。

4. **[#11921](https://github.com/earendil-works/pi/issues/11921) — `tool_search` 对直接 MCP 工具回答“No matching tools found”**
   直接暴露的服务器从未在系统提示中被命名（`<tools>` 仅列出带有 `promptSnippet` 的工具），因此搜索无法找到它们。一个微妙的发现类 bug，揭示了工具暴露语义的可见性问题。

5. **[#10162](https://github.com/earendil-works/pi/issues/10162) — 输入图片过多导致 agent 任务停止**（OPEN，👍0）
   在压缩机制已存在的情况下，这削弱了无限运行/“托管”用例。长时间运行的 agent 是核心差异化功能，因此这是一个显著的可靠性缺口。

6. **[#11926](https://github.com/earendil-works/pi/issues/11926) — 瞬态状态被当作永久错误处理**
   429 被渲染为红色并永久记录到会话中，将显示与重试逻辑混为一谈。对处于限流本地后端之后的用户而言是体验问题。

7. **[#11931](https://github.com/earendil-works/pi/issues/11931) — `openai-completions` 丢弃 OpenRouter 的 `native_finish_reason`**
   Gemini 的 `MALFORMED_FUNCTION_CALL` 被隐藏为通用的 `finish_reason: error`，移除了可操作的诊断信息。提供商级故障的可观测性丧失。

8. **[#11916](https://github.com/earendil-works/pi/issues/11916) — Mistral GLM 数组被渲染为 `[object Object]` 刷屏**
   解析器将数组式 `delta.content` 字符串化。对 Mistral 自定义提供商用户而言是可见的提供商兼容性破坏。

9. **[#11932](https://github.com/earendil-works/pi/issues/11932) — 用于自动重试决策的扩展钩子**（OPEN）
   请求一个 `auto_retry_decision` 事件，以便扩展可以设置退避或停止重试。这是请求更细粒度扩展钩子的更广泛趋势的一部分。

10. **[#11927](https://github.com/earendil-works/pi/issues/11927) — Bug 报告不应被自动关闭为“not planned”**
    一个引发社区共鸣的流程抱怨：所有非贡献者新提交的 bug 都被自动关闭为“not planned”，这扭曲了分类可见性。作为治理信号值得关注。

附加：**[#4918](https://github.com/earendil-works/pi/issues/4918) — Shift+Enter 不添加换行**（👍4）已关闭，修复 PR 已合入（见下文）。

## 4. 关键 PR 进展

1. **[#11934](https://github.com/earendil-works/pi/pull/11934) — `tui: 将 mintty LF 解码为 Shift+Enter`**（CLOSED）
   修复 Cygwin/MSYS2 mintty 中的 Shift+Enter，使全屏搜索可以导航到上一个匹配项。与 issue #11935 配套。

2. **[#11857](https://github.com/earendil-works/pi/pull/11857) — `feat: p2 中的提示缓存预热`**（CLOSED）
   解决 Anthropic 提示缓存 5 分钟后过期的问题；作者报告在约 3 周内 789 次重写中额外花费约 $1,430（占 p2 支出的 26%）。显著的成本优化。

3. **[#11920](https://github.com/earendil-works/pi/pull/11920) — `fix(codemode): 允许脚本声明自己的 tools 和 console 绑定`**（CLOSED）
   修复 codemode 脚本声明顶层 `tools`/`console` 时的 `SyntaxError: invalid redefinition of parameter name`。修复 #11226。

4. **[#11900](https://github.com/earendil-works/pi/pull/11900) — `docs: 修复 CONTRIBUTING.md 中的 RFC 链接`**（CLOSED）
   琐碎但有用：RFC 链接中的尾部斜杠导致 404，已为贡献者修复。

注意：窗口期内仅四个 PR 有更新，现均已关闭。无长期功能 PR 活跃，表明这是以维护为主的一天。

## 5. 热门讨论

**展示与分享**

- **[#11930](https://github.com/earendil-works/pi/discussions/11930) — Pi Durable 的 Assistant UI 适配器**（👍1）
  一个 React 运行时钩子，将 Durable 宿主连接到 Assistant UI 组件，具有转录渲染、流式更新和线程导航。表明生态系统对自定义前端的兴趣日益增长。
- **[#11922](https://github.com/earendil-works/pi/discussions/11922) — omp-web：Pi 的自托管浏览器工作区**（👍1）
  一个用于 agent CLI（Pi、Oh My Pi、Claude Code、Codex）的 Web 工作区，可从笔记本电脑或手机对常开 Linux 机器使用。反映了对 Pi 会话远程/移动访问的需求。

## 6. 功能请求趋势

- **更细粒度的扩展钩子**：`auto_retry_decision`（#11932）、为 `ui.custom` 携带 `blocked` 的 `ui_prompt_start`（#11924），以及为自定义页脚发布 MCP 连接状态（#10871）。扩展作者越来越希望事件级控制，而非 UI 级变通方案。
- **上下文/会话人体工学**：保留模型和思考级别的 `/clear`（#11925）以及模型选择器中按模型选择思考级别（#11929），都指向常规工作流中的调优摩擦。
- **条件式指令文件**：根据任务上下文引用 `AGENTS.md` 及其同类文件（`AGENTS_JAVA.md`、`AGENTS_INFRA.md`）（#11933），解决指令文件膨胀问题。
- **更丰富的多模态输入**：视频/音频附件（#2809），跟进 Gemini 3 能力的对等性。
- **对 monorepo 友好的安装**：`pi install git:` 的子路径支持（#4851），对于位于 monorepo 中的扩展生态系统很重要。

## 7. 开发者痛点

- **1.5.0 中的 MCP 可靠性**是主导主题：认证状态在登录后未持久化（#11918）、串行阻塞重连（#11919），以及直接/延迟暴露之间的工具可见性不匹配（#11921）。这一集群表明原生 MCP 层需要在更广泛采用之前进行加固。
- **重试语义渗入用户体验**：瞬态错误被显示为永久失败（#11926），而上下文用量恰好在可重试网络错误后损坏（#10287）。重试处理似乎触及了比应有的更多的子系统。
- **`openai-completions` 中的提供商兼容性债务**：OpenRouter 的 `native_finish_reason` 被丢弃（#11931），Mistral 的数组式 `delta.content` 未处理（#11916）。OpenAI 兼容解析器是反复出现的破坏和诊断丢失来源。
- **终端/输入边缘情况**：Shift+Enter 换行（#4918）、mintty 按键解码（#11935/#11934），以及退格损坏粘贴标记（#11923），表明 TUI 输入处理是跨平台持续的小痛点来源。
- **流程摩擦**：将社区 bug 报告自动关闭为“not planned”（#11927）侵蚀贡献者信任，并将合法 issue 从分类中隐藏——值得维护者回应。

</details>

<details>
<summary><strong>Qwen Code</strong> — <a href="https://github.com/QwenLM/qwen-code">QwenLM/qwen-code</a></summary>

# Qwen Code 社区文摘 — 2026-11-09

## 1. 今日亮点

发布活动密集：一个新的 nightly 构建、一个捆绑 CLI 0.27.0 的 TypeScript SDK 版本更新，以及首个 **Qwen Code Desktop v0.27.0** 构建，均在 24 小时内落地。问题追踪器仍被 CI 基础设施噪音主导——持续存在的“Main CI failed”机器人问题（#14724 已有 277 条评论，#11680、#11678）继续累积，同时若干真实的 P1/P2 缺陷（Windows 桌面更新、GitHub 频道游标竞态）已关闭。在 PR 方面，贡献者正在推进大量托管代理功能（归档 + MCP 自助服务、共享工作区、基于 Mem0 的记忆），同时还有一波频道/MCP/ACP 正确性小修复。

## 2. 发布

**v0.26.9-nightly.20261107.65e1f1aeaa** — Nightly 构建；包含 OpenAI 兼容提供商下 Wally 的提供商文档以及核心修复。

**sdk-typescript-v0.1.30** — TypeScript SDK 发布，捆绑 CLI **0.27.0**（在匹配的分支/ref 上从源码构建）。

**desktop-v0.27.0** — 桌面客户端发布。显著变更包括修复 GitHub 入站频道在读取失败后仍保持恢复机制激活（#16402），以及强化核心续期测试。

> 注意：Desktop v0.27.0 与未解决的 Windows 更新器缺陷 #15276 一同发布（已关闭但标记为 ready-for-human）——Windows 用户应留意应用内更新期间的 `node.exe` 文件锁定错误。

## 3. 热点 Issue

1. **[#14724](https://github.com/QwenLM/qwen-code/issues/14724)** — *Main CI failed: Managed Agent gate — no test identified*（277 条评论）。仓库中流量最高的问题；一个长期运行的机器人跟踪 CI 失败，未识别出具体测试。这反映的是 CI 分诊债务而非产品缺陷。
2. **[#13078](https://github.com/QwenLM/qwen-code/issues/13078)** — *Daily dependency CVE audit failed*（45 条评论）。反复出现的供应链审计失败，可能是过期的 npm audit 端点或真实的新高危安全公告。与安全相关且长期存在。
3. **[#16417](https://github.com/QwenLM/qwen-code/issues/16417)** — *Reject an oversized `write_file` before generating it*（P2，token 管理）。模型生成完整响应后，因 `Response truncated due to token limit` 被丢弃——没有针对输出预算的预检。真实工作流痛点，可复现。
4. **[#16227](https://github.com/QwenLM/qwen-code/issues/16227)** — *GitHub channel can silently skip a comment at the poll-cursor boundary second*（已关闭，P2）。微妙的竞态导致永久错过用户评论；已关闭，这对频道可靠性是好消息。
5. **[#15276](https://github.com/QwenLM/qwen-code/issues/15276)** — *Windows desktop update fails: bundled `node.exe` locked*（已关闭，P1，ready-for-human）。Windows 桌面用户的高影响升级阻碍。
6. **[#12113](https://github.com/QwenLM/qwen-code/issues/12113)** — *ACP reports `end_turn` after repeated `finish_reason=length`*（P2）。IDE 客户端被告知轮次已正常结束，而输出实际上被截断——这是一个影响 JetBrains/ACP 消费者的集成正确性问题。
7. **[#12160](https://github.com/QwenLM/qwen-code/issues/12160)** — *MCP tools stay disconnected after an aborted turn*（P2）。中止路径跳过了 `mcp-tool.ts` 中的会话修复，导致 HTTP MCP 服务器永久处于 `DISCONNECTED`。由 PR #16661 直接解决。
8. **[#16526](https://github.com/QwenLM/qwen-code/issues/16526)** — *Parallel sub-agents can oversubscribe a local model*（P3）。全局并发限制遗漏了前台启动；本地单 GPU 用户会遇到代理排队。随着子代理使用增长，这一问题日益相关。
9. **[#16173](https://github.com/QwenLM/qwen-code/issues/16173)** — *Allow models to refer to slices of files/tool outputs*（P3，need-discussion）。针对 `read > write > typo > re-write` 循环，通过避免在上下文中重复大段内容来解决。
10. **[#8544](https://github.com/QwenLM/qwen-code/issues/8544)** — *ACP task list not rendered in JetBrains*（P2，长期存在）。Claude Code 和 Codex 在相同的 JetBrains ACP UI 中渲染实时待办列表；Qwen Code 则不会。这是 IDE 用户的竞争对等差距。

## 4. 关键 PR 进展

1. **[#16661](https://github.com/QwenLM/qwen-code/pull/16661)** — *fix(mcp): repair the MCP connection torn down by an aborted tool call.* 激活中止路径，在 `onerror` 将服务器翻转为 `DISCONNECTED` 后恢复传输。直接修复 #12160。
2. **[#16679](https://github.com/QwenLM/qwen-code/pull/16679)** — *fix(acp): surface failed `todo_write` calls.* 发出带 `status: failed` 的终端 `tool_call_update`，而非丢弃该帧——提升 ACP 透明度。
3. **[#16500](https://github.com/QwenLM/qwen-code/pull/16500)** — *feat(managed-agent): archive and MCP self-service installation.* 无守护进程的 ZIP/tar.gz 准备、摘要固定的检查、加密的 Workspace 设置、一次确认安装。重要的托管代理能力。
4. **[#16023](https://github.com/QwenLM/qwen-code/pull/16023)** — *feat(managed-agent): share resident external Workspace across sessions.* 工作区范围的绑定在会话间复用，而非每个周期拆除。大型架构变更，Java 电池组回归问题跟踪于 #16605。
5. **[#16488](https://github.com/QwenLM/qwen-code/pull/16488)** — *feat(managed-agent): opt-in personal memory backed by Mem0.* 每租户 `MemoryStore`，在会话创建时不可变绑定。标志着托管代理的记忆路线图。
6. **[#11854](https://github.com/QwenLM/qwen-code/pull/11854)** — *feat: hybrid code mode.* 添加 `tools.mode` 枚举（`direct` / `code_mode` / `code_mode_only`）以及隔离的 `exec` JavaScript 工具——与 Codex 对齐的工具执行模型。
7. **[#15923](https://github.com/QwenLM/qwen-code/pull/15923)** — *fix(core): resolve MSYS paths in file tools on Windows.* Git Bash 的 `/tmp` 映射到操作系统临时目录，但文件工具将其字面解析为 `<drive>:\tmp`。修复 `read_file` 的误报。
8. **[#16439](https://github.com/QwenLM/qwen-code/pull/16439)** — *fix(core): replay the persisted startup prelude on resume.* 恢复的会话现在复用原始前奏（日期、树、git 快照、MCP/技能），而非重建——提升恢复确定性。
9. **[#16561](https://github.com/QwenLM/qwen-code/pull/16561)** — *test(ci): pin credential-bearing lanes to hosted runners.* 将 web-shell 视觉发布器（`CI_BOT_PAT`、OSS 密钥）和失败报告器锁定在共享池之外。CI 安全加固。
10. **[#16681](https://github.com/QwenLM/qwen-code/pull/16681)** — *feat(web-shell): commit only selected files.* 提交视图中通过复选框选择文件；未勾选的文件保留在工作树中。对 Web Shell 用户的实用 UX 改进。

## 5. 热点讨论

源数据集未提供 Discussion 数据；本节省略。

## 6. 功能需求趋势

- **Token/输出预算管理** — 对超大写入的预检（#16417）、通过切片引用避免重复（#16173）、以及截断感知的 ACP 报告（#12113），都指向围绕上下文/输出限制日益增长的摩擦。
- **子代理并发控制** — 请求让全局子代理限制感知前台启动和本地模型容量（#16526）。
- **托管代理平台成熟化** — 归档、MCP 自助服务、共享常驻工作区和个人记忆（PR #16500、#16023、#16488）构成了连贯的“托管代理即产品”方向。
- **IDE/ACP 对等** — JetBrains 中的实时任务列表渲染（#8544）和准确的停止原因（#12113）是反复出现的集成需求。
- **UI 渲染保真度** — 将 `exec`/代码模式工具调用渲染为 JavaScript 而非 JSON（#16675），以及 Split View 中的滚动到底部能力（PR #16658）。

## 7. 开发者痛点

- **CI 噪音淹没追踪器** — 机器人提交的“Main CI failed”问题（#14724、#11680、#11678、#16045、#16191、#16672、#16673）主导了评论量，许多标记为 *Provisional* 并自行解决。维护者和贡献者面临真实的分诊疲劳。
- **不稳定/跨平台测试失败** — macOS 上 `bun-pty` flood 测试反复失败（#16091）、Windows Node 22 测试运行（#11678）、以及 E2E 沙箱分片（#11680、#15185）。Windows 和沙箱通道受到不成比例的影响。
- **堆叠 PR 的 CI 盲区** — PR #16634 报告称，针对非 `main`/`release` 基底的 PR 完全不触发 Java 门禁，因此堆叠 PR 上的绿色检查并非健康证据。
- **发布流水线不稳定** — nightly 发布失败（#16031）以及持续不断的托管代理门禁失败，表明发布/集成矩阵承受压力。
- **平台特定正确性** — Windows 路径处理（MSYS，#15923）、Windows Terminal 渲染（DECSET 2026，#11929）、以及桌面更新器文件锁定（#15276）反复构成一组 Windows 痛点。
- **本地模型人体工学** — 超额订阅（#16526）和输出截断处理（#16417、#12113）表明本地/自托管工作流仍缺乏云模型用户不会注意到的护栏。

</details>

---
*本日报由 [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar) 自动生成。*