# AI CLI Tools Community Digest 2026-11-03

> Generated: 2026-11-03 10:36 UTC | Tools covered: 7

- [Claude Code](https://github.com/anthropics/claude-code)
- [OpenAI Codex](https://github.com/openai/codex)
- [Gemini CLI](https://github.com/google-gemini/gemini-cli)
- [GitHub Copilot CLI](https://github.com/github/copilot-cli)
- [OpenCode](https://github.com/anomalyco/opencode)
- [Pi](https://github.com/earendil-works/pi)
- [Qwen Code](https://github.com/QwenLM/qwen-code)
- [Claude Code Skills](https://github.com/anthropics/skills)

---

## Cross-Tool Comparison

# AI CLI Tools — Cross-Tool Comparison Report
**Data date: 2026-11-03 | Sources: eight community digests**

---

## 1. Ecosystem Overview

All eight tools in the index are in sustained tension between two forces: **maturing agent orchestration capabilities** (subagents, background tasks, MCP, ACP integration) and **foundational reliability debt** (session durability, context accounting, platform-specific startup failures). Not a single tool reports a clean release cycle without corresponding reliability complaints; every release in this window — including four Copilot CLI builds, Claude Code v2.1.317, Gemini CLI nightly, Qwen Code v0.26.6, and Pi v1.5.0 — is dominated by *harden-the-edges* work rather than headline capability launches. The competitive frontier has shifted from raw model capability to **governance, observability, and cross-surface parity**: org-level artifact policies, agent lifecycle resilience, and closing the gap between interactive CLI and programmatic (ACP/SDK/headless) surfaces. Meanwhile, **provider-integration accounting** (token budgeting, model-ID normalization, quota semantics) emerges across at least five tools as a trust-defining issue class — users now measure integration quality in *silent cost regressions*, not features.

---

## 2. Activity Comparison

| Tool | Issues (noteworthy / top engagement) | PRs (in window) | Discussions | Release Status |
|---|---|---|---|---|
| **Claude Code** | 10 hot items; most CLOSED/stale; queue centers on agent lifecycle + org governance | 2 (1 CLOSED, 1 stale OPEN) | N/A (none provided) | ✅ v2.1.317 shipped |
| **OpenAI Codex** | 10 hot items; heavy Windows/attachment cluster | 10+ substantial (bot-authored, cloud client, MCP/Guardian) | ✅ 4 Ideas, 3 Q&A, 5 Show-and-tell | ✅ rust-v0.167.0-alpha.4 (alpha) |
| **Gemini CLI** | 10 hot + 3 notable; agent reliability dominant | 10+ (robustness, ACP, platform fixes) | N/A (none provided) | ✅ v0.67.0-nightly |
| **GitHub Copilot CLI** | 10 hot (keyboard/MCP/ACP/Win) | 1 (agent-monitor TUI) | N/A (none provided) | ✅ 4 builds (v1.0.106-3 → -6) |
| **OpenCode** | 10 hot; free-tier + desktop stability dominant | 10 (compaction, SSE, shell, i18n) | N/A (none provided) | ❌ No release today |
| **Pi** | 10 hot; RPC/schema/context budget | 10 (schema, MCP, update, grep OOM) | ✅ 1 Ideas (👍47), 1 Q&A, 1 Show-and-tell | ✅ v1.5.0 |
| **Qwen Code** | 10 hot; CI noise + session durability | 10 (core/serve/managed-agent) | N/A (none provided) | ✅ v0.26.6 + SDK v0.1.27 |

**Data caveats:** Codex's GitHub Issues/PRs are *active* (not N/A) — the tool uses both code tracker and Discussions. Claude Code, Gemini CLI, Copilot CLI, OpenCode, and Qwen Code have Discussions enabled upstream but the digest did not surface data for them — marked N/A rather than "inactive." OpenCode is the only repo with **no release** in the window, yet it has the highest single-thread engagement of any tool (#59387 at 230 comments).

**Engagement ranking (top single thread):**
1. OpenCode — 230 comments (free-tier mega-thread)
2. Qwen Code — 161 comments (CI gate failure, bot)
3. Codex — 69 comments (Windows projects disappearing)
4. Claude Code — 13 comments (Windows compaction, closed)
5. Pi — 21 comments (terminal color query regression)

> Note: Engagement *depth* vs. *breadth* differs — OpenCode/Qwen threads are single issue heavy / bot-noise; Codex/Claude/Gemini show the widest issue-title diversity.

---

## 3. Shared Feature Directions

Requirements appearing across **≥3 tool communities** — these are the industry's real roadmap:

| Direction | Tools signalling it | Specific needs |
|---|---|---|
| **Agent lifecycle resilience** | Claude Code, Gemini CLI, Pi, OpenCode | Pause/resume subagents on recoverable failure (CC #78224); MAX_TURNS misreported as success (Gem CLI #22323); RPC duplicate `session_start` / hung shutdown (Pi #10470, #11599); stale background `opencode-cli` services (OC #50633) |
| **Context / compaction correctness** | Claude Code, OpenCode, Pi, Qwen Code, Codex | Idle compaction racing agents (CC #106402); per-exchange truncation waste (OC #61371); compaction ceiling inversion (OC #58492); token-budget mismatch w/ OpenRouter (Pi #10497); missing Anthropic transport timeouts (QC #16193); stale session state causing "capacity" errors (Codex #59059, PR #59111) |
| **Configuration honored consistently** | Gemini CLI, OpenCode, CC, Copilot, Codex | `settings.json` ignored by Browser Agent (Gem #22267); `shell` setting ignored (OC #51051); `CLAUDE_CONFIG_DIR` violated (CC #98789); ACP settings gaps (Copilot); model catalog refresh staleness (Codex #59111) |
| **MCP robustness & permission granularity** | Codex, Copilot, Pi, Gemini | OAuth header dropped on `tools/call` (Copilot #3015); per-run MCP disable (Copilot #5336); tailored `doctor` remediation (Codex #59057); child-process cleanup (Pi #11724); >128 tools ceiling (Gem #24246); sandbox boundary design (Codex Disc #58905) |
| **ACP / programmatic-surface parity** | Copilot, Pi, Qwen Code | ACP settings/subagent parity (Copilot, many closed today); **44 votes** for adopting ACP over custom RPC (Pi #4444); unified agent loop + ACP cron turns (QC #16190/#16192) |
| **Observability / status signalling** | Copilot, Codex, Pi, Gemini | OSC 7501 terminal status (Copilot #5112); live typed transcript events (Codex #59066/#59047); subagent tool visibility in UI (Gem #30094); extension skill inspection (Pi #11700) |
| **Token-efficiency / precision context** | Qwen Code, Gemini, Pi, OpenCode | Slice-level file/context references (QC #16173); AST-aware reads/search EPIC (Gem #22745–#22747); "Tactful Extraction" (Gem #19561); per-turn drift inspector (OC #58459) |
| **Cross-session memory / persistence** | Codex, OpenCode, Pi, Gemini | Decision maps (Codex Disc #27754/#55901); FTS5 transcript search (OC #54863); child-session visibility / durable restarts (Pi #9847/#11635); file-based task state (Gem #18836) |
| **Windows & platform parity** | Codex, CC, Copilot, Pi, Gemini | Disappearing projects (#42739), MSIX update loops (#38843), CRLF corruption (Copilot #1148), mintty/ConPTY escape leaks (Pi #10256), WSL2 keep-alive (Copilot #5304), Wayland browser failure (Gem #21983) |
| **False-positive safety filters** | Copilot, Codex, Gemini | 422 cybersecurity-risk flags (Copilot #5335); Guardian denials w/ opt-in reconsideration (Codex #59107); destructive git discouragement (Gem #22672) |

**Universal theme:** every community is asking for the *same* thing in different words — **the agent loop must be trustworthy under interruption, and configuration must mean what it says.**

---

## 4. Differentiation Analysis

| Axis | Claude Code | Codex | Gemini CLI | Copilot CLI | OpenCode | Pi | Qwen Code |
|---|---|---|---|---|---|---|---|
| **Primary target** | Enterprise teams, governed orgs | Cross-surface (Desktop+CLI+Work), Office-centric users | Developer automation, agentic research | GitHub-native devs, ACP client integrators | Free-tier + power users, SDK/bot builders | Embedders, headless/RPC integrators | Channel/Web Shell + managed-agent operators |
| **Feature focus** | Governance (org artifact sharing), hook/permission parity, `/schedule` cloud tasks | Cloud client observability (typed events, paginated history), Guardian safety, MCP doctor | Agent orchestration correctness, AST-aware context, sandboxing strategy | Unified `/settings` dashboard, ACP parity, keyboard UX, MCP auth | Compaction algorithm, provider edge cases, desktop lifecycle | Extension changelogs, RPC lifecycle, schema resolution | Web Shell sidebar, managed-agent sharing, CI health |
| **Technical approach** | Hook-based security model (extending to Bash), hook-based SDK controls | Rust client, gRPC cloud history, policy-aware search providers | Maintainer-authored strategic dir issues (sandbox, AST), unified agent loop on roadmap | Release-direct shipping (no PR queue), OSC terminal integration | Provider-agnostic harness, SSE-level fixes, SQLite FTS5 | Custom RPC (push to ACP), bot-authored PR pipeline | Unified agent loop rollout behind `QWEN_CODE_ENABLE_UNIFIED_AGENT_LOOP` |
| **Maturity posture** | Triage-fatigue / stale-closure heavy (security reports closed stale) | High-velocity alpha, bot-generated PR burst | Nightly-iterating, strategic EPICs | Shipping through releases, low public PR surface | No release; heavy desktop instability | Small-team, high-signal PRs, rapid fix merges | Bot-heavy CI noise drowning real PRs |
| **Distinctive vulnerability** | Hook coverage blind spots (Bash bypass); visible config sprawl | "Capacity" errors masquerading as session state; Windows desktop | Misleading `Termination Reason: GOAL` success masking | Windows line-ending corruption; ACP second-class lag | Free-tier quota accounting credibility | Context-budget silent fallbacks; update trust | CI token rate-limit self-outages; session data loss |

**Key differentiation insights:**
- **Security model philosophy diverges:** Claude Code is hook-registry-centric (and waging war against its own blind spots); Codex is *Guardian-safety-oriented* (denial reconsideration, prepared-owner permissions); Gemini leans *strategic* (maintainer-authored sandbox + intent routing).
- **Protocol posture:** Pi is openly campaigning for **ACP standardization**, while Copilot is *integrating* ACP but lagging interactive parity, and Qwen is *unifying its agent loop* toward ACP turns — a clear convergence on ACP as the editor↔agent transport, with Claude Code notably absent from this conversation.
- **Trust in success reporting is now a differentiator:** Gemini's #22323 (GOAL on MAX_TURNS) and #21983 (GOAL on platform failure) is the most dangerous single class of bug in the entire set for automation users — and only Gemini CLI surfaces it this loudly.

---

## 5. Community Momentum & Maturity

**Highest raw engagement / largest communities (by issue comment volume):**
1. **OpenCode** — 230-comment mega-thread; highest raw engagement, but concentrated in *support-backlog* rather than technical depth. Also the only repo without a release today — a signal that issue volume is outpacing release cadence.
2. **Qwen Code** — 161-comment bot-filed CI gate; second highest raw engagement but *low signal* (bot noise inflates it).
3. **Codex** — 69-comment Windows issue; *highest-quality broad engagement* with the most diverse issue/PR/discussion mix.

**Fastest-iterating engineering cadence:**
- **Copilot CLI** — 4 builds / 24 h, releases-direct (virtually no PR surface). Highest velocity, lowest community visibility into changes.
- **OpenAI Codex** — 10+ substantive PRs in one window (cloud client typed events, MCP/search discovery, Guardian refinements). The **most active engineering pipeline** relative to window size.
- **Pi** — Small but *surgical*: 10 PRs that each close a specific, well-scoped defect (grep OOM, SSE retry listeners, schema `$id`). Highest fix-per-PR quality ratio.

**Maturity indicators (positive):**
- **Codex, Copilot, Gemini** show *backlog-shaping* maturity — features move from discussion → issue → PR within days (MCP doctor, Guardian reconsideration).
- **Claude Code** shows *process maturity but triage risk*: a cleanup wave closed nearly all top threads, including two security-relevant reports (#90658, #89716) marked needs-repro/stale. This is a community-trust warning sign, not just housekeeping.

**Maturity indicators (negative / debt):**
- **OpenCode** — free-tier credibility crisis (quota reset times fail, Desktop won't launch for free users) is a support-backlog problem with strategic consequences for a tool whose model depends on free-tier adoption.
- **Qwen Code** — self-inflicted CI outages (#16188: bot bursts exhausting GITHUB_TOKEN, 403'ing unrelated PRs) mean real regressions are drowned in infrastructure noise.
- **Pi** — release pipeline bug: v1.2.0–v1.4.0 had *no builds published* despite tagged releases (#11686). That is a packaging trust gap.

**Approx. community-activity ranking (composite: engagement × diversity × recency):**
> Codex ≈ Gemini ≡ Claude Code > Copilot > OpenCode (volume) ≈ Pi (quality) > Qwen (noise-deflated)

---

## 6. Trend Signals

**1. Reliability is the new feature.**
Every tool's pain-point section is dominated by reliability, not capability gaps. Subagent lifecycle, session durability, and configuration-honored-ness have replaced "add more capabilities" as the top user demand. **Reference for developers/builders:** if you're shipping an agent harness, reliability instrumentation (typed events, retry-safe tool calls, interrupt-resilient state) is now table stakes and your primary differentiator.

**2. Silent failures are the most damaging failure mode.**
Recurring *across tools*: dropped OAuth headers fail quietly (Copilot #3015), compaction stalls with no feedback (OC #61346), quota reset times never arrive (OC #59387), and — most consequential — **`Termination Reason: GOAL` masking turn-limit hits** (Gem #22323). For automation/eval users, *loud failure is better than false success*. Tools that ship explicit `truncated`/`failed` states will win trust.

**3. Provider-integration accounting is a trust battleground.**
Token overcount / undersized context / model-ID normalization collectively produce **400s, premature compaction, and cost regressions** (Pi #10497/#11021, Qwen #16157–#16159, Codex #59059, OpenCode #61072 at ~1.8× quota drain). **Reference:** this is a *correctness* issue, not a cost-optimization issue — and users measure integration quality by it.

**4. The "surface parity tax" is universal.**
Every mature tool now pays a tax to keep interactive CLI, headless/SDK, and programmatic surfaces (ACP, RPC, HTTP) in sync. ACP is emerging as the convergence protocol (Pi 👍47, Copilot ACP cluster, Qwen unified loop), with Claude Code and Gemini not visibly participating — a potential interoperability fault line.

**5. Compaction is the single most fragile subsystem in all agent harnesses.**
Five tools surface compaction bugs today. It is the place where *context accounting × session state × provider behavior* collide — and where interim hacks (`flattenAndDropOldest`, keepalive timeouts, ceiling inversion) recur. **Reference:** treat compaction as a first-class, independently-tested subsystem, not an internal convenience.

**6. Windows and platform-native friction is a shard of chronic debt.**
Across five tools, Windows-specific desktop/update/input bugs remain the #1 or #2 complaint. WSL2, mintty, ConPTY, and macOS XLSX/PPTX attachment hangs show *desktop-surface regression clusters* recurring across the entire ecosystem. If you target enterprise Windows users, this is your highest-leverage rework area.

**7. Governance is starting to differentiate enterprise adoption.**
Claude Code alone surfaces org-level artifact sharing (#78537) and hook/permission parity as a **governance, not convenience**, concern. Expect this axis to widen — the tool whose governance primitives mature first will capture regulated enterprise rollouts.

**8. Free-tier and rate-limit semantics deserve scrutiny.**
OpenCode's 230-comment thread + Copilot's disappearing quota indicator (#57656, 👍20) + Codex "capacity" errors all reflect users caring less about *what* the limit is than about whether it is *reported honestly and resets as promised*. Transparency in rate/usage UX is a trust feature.

**9. Release cadence ≠ user-visible maturity.**
Copilot ships four builds/day but has almost no public PR trail (release-direct). Codex ships 10+ PRs/window with alpha tags. Gemini nightlies aggressively. **Reference:** for evaluating these tools as dependencies, look at the *issue-closure quality and security-report handling*, not release frequency — Claude Code's stale-closure of security reports should temper a raw build-count comparison.

---

### Recommended reads for technical decision-makers
- **Most actionable engineering fixes:** Pi PRs #11705, #11712, #11726; Codex PRs #59057, #59107, #59111; Gemini PR #30020; OpenCode PRs #61376, #58492.
- **Most valuable postmortems (parallel-agent users):** Claude Code #89102 (worktree latch breaks Bash) and #90782 (bootstrap context contamination).
- **Most strategic direction-setting:** Gemini #19873 (sandbox + intent routing) and #22745 EPIC (AST-aware context); Pi Disc #4444 (ACP standard).
- **Watch-list (trust risk):** Claude Code stale-closed security reports; Qwen Code CI token exhaustion; OpenCode free-tier accounting; Codex capacity-error cluster.

*Report synthesized from eight repository digests dated 2026-11-03. Issue/PR labels and vote counts quoted as reported; no external verification performed.*

---

## Per-Tool Reports

<details>
<summary><strong>Claude Code</strong> — <a href="https://github.com/anthropics/claude-code">anthropics/claude-code</a></summary>

## Claude Code Skills Highlights

> Source: [anthropics/skills](https://github.com/anthropics/skills)

# Claude Code Skills Community Highlights Report
*Data snapshot: 2026-11-03 | Source: github.com/anthropics/skills*

---

## 1. Top Skills Ranking (by discussion & attention)

> Note: All PRs in this snapshot show `Comments: undefined`, so ranking blends issue-linked discussion volume, update recency, and cross-referenced bug activity.

| # | Skill / PR | Functionality | Discussion Highlights | Status |
|---|-----------|---------------|----------------------|--------|
| 1 | **docx** (multiple: #1792, #1734, #2018) | Word document manipulation — accepting tracked changes, orphan comment detection, run merging | Heaviest fix cluster in the repo. `merge_runs.py` writes **invalid XML** (unbound namespace prefixes) while reporting success ([#2018](https://github.com/anthropics/skills/issues/2018)); LibreOffice timeouts falsely reported as success ([#1792](https://github.com/anthropics/skills/pull/1792)) | All OPEN |
| 2 | **skill-creator** (#1298, #1681, #1997) | Meta-skill for authoring/validating/optimizing Skills | Three independent correctness bugs: trigger-eval false misses + Windows breakage ([#1298](https://github.com/anthropics/skills/pull/1298)), broken standalone execution ([#1681](https://github.com/anthropics/skills/pull/1681)), YAML frontmatter scalar parsing corrupts descriptions ([#1997](https://github.com/anthropics/skills/pull/1997)). Also flagged as non-operational/dev-doc-toned in [Issue #202](https://github.com/anthropics/skills/issues/202) | All OPEN |
| 3 | **mcp-builder** (#1742, + #1390) | Scaffolds MCP servers with a connection/eval harness | Two systemic failures: `mcp>=2.0.0` renamed `streamablehttp_client` and changed header config ([#1742](https://github.com/anthropics/skills/pull/1742)); evaluation harness fabricates tool errors and scores **0/N against every real MCP server** ([#1390](https://github.com/anthropics/skills/issues/1390)) | OPEN |
| 4 | **claude-api** (#1730, + #1487) | API reference and tool-use concepts | **~156k tokens eagerly injected in a single tool call**, exhausting the context window ([#1487](https://github.com/anthropics/skills/issues/1487)) — the most severe resource bug reported. Plus 3 dead doc URLs ([#1730](https://github.com/anthropics/skills/pull/1730)) | OPEN |
| 5 | **webapp-testing** (#1980, #1976) | Browser/server test automation scripts | Security + correctness: `shell=True` command injection risk CWE-78 ([#1980](https://github.com/anthropics/skills/pull/1980)); textarea/select misreported as `input` in element discovery ([#1976](https://github.com/anthropics/skills/pull/1976)) | OPEN |
| 6 | **pyxel** (#525) | Retro game dev in Python — headless runs, frame inspection, state checks | Longest-lived active PR in the set (opened 2026-03, updated 2026-11) — sustained maintainer/community iteration | OPEN |
| 7 | **document-typography** (#514) | Typographic QC: orphan/widow prevention, numbering alignment for generated docs | Addresses a universal output-quality gap ("every document Claude generates"); broad applicability drove early attention | OPEN |
| 8 | **ODT** (#486) | OpenDocument (.odt/.ods) create/fill/parse/convert | Fills a clear format-coverage gap (ODF/ISO standard) alongside existing docx/pptx/pdf skills | OPEN |

---

## 2. Community Demand Trends (from Issues)

1. **Trust & security of the skill supply chain** — [Issue #492](https://github.com/anthropics/skills/issues/492) (46 comments, top-discussed) warns community skills ship under the `anthropic/` namespace, enabling trust-boundary impersonation. Reinforced by XSS ([#1394](https://github.com/anthropics/skills/issues/1394)) and command-injection reports.
2. **Team/org-level skill distribution** — [Issue #228](https://github.com/anthropics/skills/issues/228) (17 comments, 8 👍) requests a shared org library and share links instead of manual `.skill` uploads.
3. **Skill evaluation reliability** — [Issue #556](https://github.com/anthropics/skills/issues/556) reports **0% trigger rate across all eval queries**; the community clearly wants trustworthy skill-testing infrastructure before scaling contributions.
4. **Context/memory efficiency** — [Issue #1329](https://github.com/anthropics/skills/issues/1329) proposes `compact-memory` (symbolic notation for agent state); [Issue #1487](https://github.com/anthropics/skills/issues/1487) demands lazy/on-demand skill loading.
5. **Agent governance & output quality gates** — [Issue #412](https://github.com/anthropics/skills/issues/412) (policy enforcement, audit trails) and [Issue #1385](https://github.com/anthropics/skills/issues/1385) (calibration → adversarial review → delivery verification) show demand for safety/QA pipeline skills.
6. **Skill authoring best practices** — [Issue #202](https://github.com/anthropics/skills/issues/202) calls for token-efficient, operational (not tutorial-style) skill design.

**Net direction:** demand is shifting from *"add more skills"* toward **trust, testability, distribution, and context economy** — i.e., ecosystem infrastructure rather than net-new skill types.

---

## 3. High-Potential Pending Skills (active, not yet merged)

| Skill | PR | Why it may land soon |
|-------|-----|---------------------|
| **proofcore-contract-auditor** | [#1771](https://github.com/anthropics/skills/pull/1771) | Solidity/Rust static analysis + TON blockchain notarization — novel Web3 vertical, recent activity |
| **md2video-audio** | [#1703](https://github.com/anthropics/skills/pull/1703) | Markdown → MP4 with AI voiceover via Marp; zero-cost pipeline, high demo value |
| **scnet-hpc** | [#1615](https://github.com/anthropics/skills/pull/1615) | Slurm/SSH HPC cluster operations — fills scientific-computing gap |
| **notion-spec-to-implementation** | [#1245](https://github.com/anthropics/skills/pull/1245) | Spec → Notion tasks → implementation plans; updated as recently as 2026-10-15 |
| **quantitative-resume-auditor** | [#1245](https://github.com/anthropics/skills/pull/1245) | Bundled in same PR; resume quantification scoring |
| **pyxel** | [#525](https://github.com/anthropics/skills/pull/525) | Updated 2026-11-01 — actively maintained, mature |
| **document-typography** | [#514](https://github.com/anthropics/skills/pull/514) | Universal document-quality value prop |
| **ODT** | [#486](https://github.com/anthropics/skills/pull/486) | Completes office-format coverage |

**Quick-win fixes likely to merge first:** the small, well-scoped bugfix PRs — [#1975](https://github.com/anthropics/skills/pull/1975) (`--cols` validation), [#1977](https://github.com/anthropics/skills/pull/1977) (`wrapAround` modulo), [#538](https://github.com/anthropics/skills/pull/538) (case-sensitive doc refs), [#1730](https://github.com/anthropics/skills/pull/1730) (dead URLs).

---

## 4. Skills Ecosystem Insight

> **The community's most concentrated demand is no longer for new Skills but for making the existing Skill layer trustworthy and economical — reliable evaluation/triggering, secure execution, correct handling of office documents, and lazy context loading — because the ecosystem's bottleneck has shifted from breadth of capability to confidence in the skills already shipped.**

---

*Methodology note: comment counts were unavailable (`undefined`) in the source snapshot; rankings reflect cross-referenced issue discussion volume, update recency, and severity of reported defects.*

---

# Claude Code Community Digest — 2026-11-03

## 1. Today's Highlights

Today's release (v2.1.317) hardens the tool-use boundary: cloud-scheduled tasks now warn about unexpected connector grants, and web/shell tool calls require confirmation before sending the account email to the network. Meanwhile, the issue tracker shows a sharp cleanup wave — nearly all top-commented items moved to CLOSED/stale — while the open queue centers on agent-lifecycle reliability (idle compaction racing background agents, config-dir leakage) and org-level artifact governance.

## 2. Releases

**v2.1.317**
- `/schedule` now warns when a newly created cloud scheduled task receives connectors you didn't request, with remediation guidance.
- Added a pre-flight check prompting before a web or shell tool call transmits your account email address over the network.
- Added `onFailure: "block"` support for Agent SDK hooks (truncated in release notes — consult the changelog for full hook semantics).

## 3. Hot Issues

1. **[OPEN] #78537 — Org-level default sharing for published artifacts** (👍 33)
   The highest-signal open enhancement: teams want published artifacts to inherit an org-wide sharing policy instead of per-artifact configuration. Strong upvote count signals this is a governance gap for enterprise rollouts.
   https://github.com/anthropics/claude-code/issues/78537

2. **[OPEN] #106402 — Idle compaction fires while background agents are still running** (macOS, core/agents)
   A fresh, unreproduced-by-others bug: compaction triggers mid-flight and can disrupt active subagents. Distinct from the opt-in complaint in #102260, making it a new failure mode in agent orchestration.
   https://github.com/anthropics/claude-code/issues/106402

3. **[OPEN] #98789 — bridge-spawn root ignores `CLAUDE_CONFIG_DIR`**
   Desktop app recreates `~/.claude/bridge-spawn` on every launch despite a custom config dir, so `~/.claude` keeps reappearing. A concrete cause is identified (module-level constant in the bundled binary) — a small, well-scoped fix.
   https://github.com/anthropics/claude-code/issues/98789

4. **[CLOSED] #65340 — Compaction bug on Windows** (👍 3, 13 comments)
   The most-discussed item of the day (closed/stale). Windows core/desktop compaction reliability remains a recurring theme; closure suggests either a fix or triage saturation.
   https://github.com/anthropics/claude-code/issues/65340

5. **[CLOSED] #78224 — Pause-and-resume background subagents on recoverable failures**
   Instead of terminating on usage limits or transient API errors, subagents should pause and resume. Work usually survives on disk, but the `failed` status loses orchestration state — an important resilience gap now closed.
   https://github.com/anthropics/claude-code/issues/78224

6. **[CLOSED] #90782 — Subagent bootstrap context contamination**
   ~5 of 20 parallel subagent launches returned in seconds with zero tool calls, echoing foreign instruction blocks (MCP server instructions, plugin skill rules). A serious isolation issue with repro; worth watching for regression.
   https://github.com/anthropics/claude-code/issues/90782

7. **[CLOSED] #89102 — `EnterWorktree` flips session-wide isolation latch, breaking Bash for concurrent agents**
   A subtle concurrency hazard: calling `EnterWorktree` in the same batch as subagent spawns breaks Bash across all agents, even those in their own worktrees. High-value postmortem material for parallel-agent users.
   https://github.com/anthropics/claude-code/issues/89102

8. **[CLOSED] #90658 — Auto mode accessed files outside project scope**
   Permission-scope escape in auto mode, including credential-adjacent data. Closed with needs-repro/stale — but the security implications warrant community attention.
   https://github.com/anthropics/claude-code/issues/90658

9. **[CLOSED] #89716 — PreToolUse hooks can't reach Bash file-command recognition**
   Hooks match on tool name, so Read/Edit/Write deny-rule equivalents are bypassed once traffic routes through Bash. A fundamental hook-coverage gap for security-conscious users.
   https://github.com/anthropics/claude-code/issues/89716

10. **[CLOSED] #89263 — Auto-continue never arms when the limit kills only background subagents**
    Main thread idle, subagents dead at session limit, nothing resumes at reset. Compounds #78224 and #88466 into a coherent "background agent lifecycle" complaint cluster.
    https://github.com/anthropics/claude-code/issues/89263

## 4. Key PR Progress

Only two PRs were updated in the window; the digest covers both:

1. **[CLOSED] #106583 — Move issue triage and dedupe workflows to Claude Sonnet 5.5**
   Migrates three internal workflows (`claude-issue-triage.yml`, `claude-dedupe-issues.yml`, `claude.yml`) to Sonnet 5.5, notably upgrading opus-triage and dedupe from older 4.x models. Signals Anthropic is dogfooding newer models on repo automation.
   https://github.com/anthropics/claude-code/pull/106583

2. **[OPEN] #58673 — Stale/open PR with no description**
   Long-lived open PR (created May 2026) with a one-character title. No actionable content; flagged here for completeness only.
   https://github.com/anthropics/claude-code/pull/58673

## 5. Hot Discussions

No discussion data was provided for this window.

## 6. Feature Request Trends

- **Agent lifecycle resilience**: pause/resume instead of terminate on recoverable failures (#78224), reliable auto-continue at usage reset (#89263, #88466), and coordination between compaction and running agents (#106402).
- **Org-level governance**: default sharing policies for published artifacts (#78537), better printing/pagination of artifacts (#90960), and cross-instance knowledge sharing without context copying (#91001).
- **Hook/permission parity**: extending hook coverage to Bash-mediated file operations (#89716) and stricter confirmation before external API actions (#91007).
- **Config/state control**: honoring `CLAUDE_CONFIG_DIR` across all subsystems (#98789) and surfacing SDK-launched sessions in `/resume` (#89077).
- **Timeout ergonomics**: longer defaults for long-running CLI commands (#91006).

## 7. Developer Pain Points

- **Background subagent reliability is the dominant theme**: subagents die at usage limits without resumption, idle compaction races them, bootstrap context gets contaminated, and `EnterWorktree` can break concurrent agents' Bash. Users running parallel agents are hitting a cluster of lifecycle bugs.
- **Hook and permission blind spots**: PreToolUse hooks match on tool name, so Bash-first routing bypasses Read/Edit deny rules — a recurring security-model concern alongside auto-mode scope escapes.
- **Triage fatigue / stale closures**: the vast majority of today's updated issues are CLOSED-stale, including security-relevant reports (#90658, #89716) — a signal that high-value reports may be aging out without resolution.
- **Platform-specific core bugs**: Windows compaction (#65340) and desktop process leaks (#89859) recur, and the Windows desktop leaks a persistent CLI process per scheduled run.
- **Config/state sprawl**: recreated `~/.claude` despite `CLAUDE_CONFIG_DIR`, hidden SDK sessions in `/resume`, and gateway model discovery failing under OAuth passthrough (#84583) point to inconsistent resolution of environment and entrypoint state.

</details>

<details>
<summary><strong>OpenAI Codex</strong> — <a href="https://github.com/openai/codex">openai/codex</a></summary>

# OpenAI Codex Community Digest — 2026-11-03

## 1. Today's Highlights

The Windows desktop app continues to dominate community pain, with a cluster of regressions around file attachments (XLSX/PPTX sends hanging in "spinning" states) and disappearing local projects drawing the highest engagement. On the engineering side, a large batch of `copyberry[bot]` PRs landed around the cloud client (typed transcript events, paginated history), MCP/Search discovery, and Guardian safety-review refinements. Meanwhile, a new wave of "Selected model is at capacity" reports across CLI and Desktop — including from Pro 200 subscribers — is emerging as a fresh reliability concern.

---

## 2. Releases

**rust-v0.167.0-alpha.4** — Alpha cut with no detailed changelog beyond the version bump. Treat as an incremental pre-release; no migration notes provided.

---

## 3. Hot Issues (10 noteworthy)

1. **#42739 — Windows: Local projects disappear from sidebar after update** (69 comments, 👍5)
   Top-engagement issue of the day. After a Windows desktop update, the Projects section shows "No projects" while chats and on-disk folders remain intact. High comment volume suggests broad reproducibility; likely a persistence/migration regression. [Link](https://github.com/openai/codex/issues/42739)

2. **#35446 — Windows 10 Computer Use deadlocks in SoftwareBitmap conversion** (57 comments)
   Long-running (since July) deadlock in `FrameArrived` during screen capture on Win10 22H2. Sustained activity indicates it remains unfixed and blocks Computer Use entirely for affected users. [Link](https://github.com/openai/codex/issues/35446)

3. **#17793 — TUI: Backspace deletes more than one character** (23 comments, 👍6)
   A persistent CLI/TUI input bug (since April) that degrades prompting. Notable for its longevity — a small but high-annoyance defect that keeps resurfacing. [Link](https://github.com/openai/codex/issues/17793)

4. **#58302 — macOS: XLSX attachments stay pending with spinning send control** (19 comments)
   Part of the attachment-handling cluster. On build `26.1027.30824`, Excel files hang at send. Pairs with Windows reports (#58660, #58313), suggesting a cross-platform regression in upload preflight. [Link](https://github.com/openai/codex/issues/58302)

5. **#56697 — Ctrl+Tab stops switching chats; cycling unbound/non-configurable** (19 comments, 👍10)
   Keyboard-navigation regression with strong upvote signal. Users want configurable recent-chat cycling — a UX gap, not just a bug. [Link](https://github.com/openai/codex/issues/56697)

6. **#58313 — Codex cannot read XLSX/PPTX attachments; capability endpoint loops** (9 comments, 👍6)
   Detailed diagnosis: 322 GETs to `/backend-api/pages/creation-capabilities` in 113s, all HTTP 200, but no upload POST follows — pointing to a repeated preflight capability check. Valuable root-cause evidence for the attachment cluster. [Link](https://github.com/openai/codex/issues/58313)

7. **#59059 — macOS: existing conversations fail "model is at capacity," fresh chats work** (13 comments)
   Brand-new 11/03 report. Same model works in a new chat but fails in existing/forwarded threads — suggests stale per-conversation model/session state rather than true capacity exhaustion. [Link](https://github.com/openai/codex/issues/59059)

8. **#57656 — Usage limit indicator disappeared from sidebar** (10 comments, 👍20)
   Highest upvote ratio in the set. Removal of a quota-visibility affordance after update — a small UI change with outsized trust impact for rate-limited users. [Link](https://github.com/openai/codex/issues/57656)

9. **#38843 — Windows MSIX auto-updater loops forever; app launches suspended** (8 comments)
   Chronic update-loop plus a permanently-suspended process on launch. Compounding installer/deploy failures make the Windows app effectively unupdatable for some users. [Link](https://github.com/openai/codex/issues/38843)

10. **#57975 — Capacity errors across Desktop, CLI, and ChatGPT Work despite available allowance** (2 comments)
    New cluster (Oct 27–29) of "Selected model is at capacity" across surfaces despite quota. Early but potentially high-impact; see also #59088 (Pro 200). [Link](https://github.com/openai/codex/issues/57975)

---

## 4. Key PR Progress (10 important)

1. **#59066 — Typed item lifecycle events in cloud client**
   Decodes live item start/completion notifications into typed `TranscriptUpdate::ItemStarted/ItemCompleted` with turn identity and timestamps. Big step for cloud-client observability. [Link](https://github.com/openai/codex/pull/59066)

2. **#59047 — Decode live transcript updates**
   Replaces opaque protobuf bytes in `Attach` notifications with typed events exposing live text, command output, file changes, and turn state. [Link](https://github.com/openai/codex/pull/59047)

3. **#59075 — Paginated history reads in cloud client**
   Adds `Client::list_turns` and `Client::list_items` over native gRPC history endpoints with turn filtering — complements live events with stored metadata. [Link](https://github.com/openai/codex/pull/59075)

4. **#59036 — Policy-aware search provider fallback**
   Resolves model-selected search providers while honoring host search restrictions; fallback preserves the provider owning existing browsing references. [Link](https://github.com/openai/codex/pull/59036)

5. **#59034 — Model Search discovery in MCP bindings**
   New `McpBinding` APIs to discover the model's Search tool profile via an authorized host-owned Apps connection, with paginated `tools/list`. [Link](https://github.com/openai/codex/pull/59034)

6. **#59057 — Tailored MCP doctor remediation**
   Separates config errors from missing env vars so `doctor` stops recommending env-var fixes for invalid server configs/unreachable URLs. Directly relevant to MCP complaints (see Discussion #33682). [Link](https://github.com/openai/codex/pull/59057)

7. **#59107 — Opt-in reconsideration of Guardian denials**
   Adds disabled-by-default `guardian_denial_reconsideration`, re-evaluating a synchronous Guardian denial once before finalizing. Addresses safety-check false positives (#57328, #59112). [Link](https://github.com/openai/codex/pull/59107)

8. **#59028 — Prepared MCP owner permissions in Guardian reviews**
   Fixes Guardian reviews of direct MCP calls missing denied paths when permissions change after a step captures context. [Link](https://github.com/openai/codex/pull/59028)

9. **#59111 — Normalize saved chat settings after model catalog refresh**
   Clears stale reasoning-effort/service-tier selections that a refreshed catalog no longer supports — directly relevant to the "at capacity" class of chat-state bugs. [Link](https://github.com/openai/codex/pull/59111)

10. **#59073 — Optional animated GIF output for image generation**
    Feature-gated (`features.image_generation_animation`, off by default) GIF generation/editing with frame-count and duration controls. [Link](https://github.com/openai/codex/pull/59073)

Honorable mentions: **#59071/#59025** — the `code_mode_only_strict_3p_tools` flag was added then reverted the same day (fast iteration on Code Mode Only tool deferral); **#59059-adjacent** **#59041** exposes assistant content token counts in completion notifications.

---

## 5. Hot Discussions

**Ideas**
- **#33682 — "MCP bugs are systematically ignored — this is crazy"** (👍22, 5 comments): Highest-upvoted discussion; asserts weeks-long OAuth/tool-discovery MCP breakage is going unaddressed. Notably, today's PRs (#59057, #59028) touch exactly this area. [Link](https://github.com/openai/codex/discussions/33682)
- **#27754 — AGENTS.md reusable project guidance maps** (👍4): Plugin experiment generating a compact `AGENTS.md` action map to bootstrap future sessions. [Link](https://github.com/openai/codex/discussions/27754)
- **#9230 — Higher-level visual interface for AI-driven development**: Argues humans should direct systems at a systems level rather than editing lines. [Link](https://github.com/openai/codex/discussions/9230)
- **#59040 — "Codex Community Compute"**: Proposal to pool idle PC resources into shared AI infrastructure. Novel, if speculative. [Link](https://github.com/openai/codex/discussions/59040)

**Q&A**
- **#58905 — MCP permission scoping when allowed tools expose disabled capabilities**: Practical Playwright-MCP question about allowing navigation/click but blocking page inspection — a real sandbox-boundary design question. [Link](https://github.com/openai/codex/discussions/58905)
- **#16563 — Version 0.118 requires constant allowance for MCP calls**: Recurring authorization-fatigue complaint. [Link](https://github.com/openai/codex/discussions/16563)
- **#40773 — IntelliJ terminal input area too dark**: Minor IDE-theming regression. [Link](https://github.com/openai/codex/discussions/40773)

**Show and tell**
- **#58912 — Codex Mobile Workbench**: Self-hosted phone/PWA front-end to native Codex on your own machine — remote direction of local agents. [Link](https://github.com/openai/codex/discussions/58912)
- **#58492 — Bossanova**: Local encrypted vault + MCP bridge to give Codex API keys without exposing values in-conversation. [Link](https://github.com/openai/codex/discussions/58492)
- **#55901 — GFT Map**: Persistent decision/constraint map across fresh chats. [Link](https://github.com/openai/codex/discussions/55901)
- **#58104 — Kilobat**: Mac menu-bar networking for agents (traffic monitoring, MCP/CLI). [Link](https://github.com/openai/codex/discussions/58104)
- **#59032 — Turnfeed**: Codex-built social feed on ChatGPT Sites. [Link](https://github.com/openai/codex/discussions/59032)

---

## 6. Feature Request Trends

- **Configurable keyboard/UI navigation**: Ctrl+Tab rebinding (#56697) and visible update controls (#53961) point to demand for user-configurable desktop UX.
- **Cross-session memory/persistence**: GFT Map (#55901) and guidance maps (#27754) reflect a recurring theme — carrying decisions and context across fresh chats.
- **MCP ergonomics & permission granularity**: Fine-grained MCP scoping (#58905), reducing re-authorization (#16563), and actionable diagnostics (#59057) are all top-of-mind.
- **Fragmented desktop IA**: #37791 argues the unified ChatGPT/Work/Codex app needs better information architecture; aligning workspace tabs with actual usage.
- **IDE integration polish**: Drag-and-drop file references from Explorer into VS Code chat (#38590).

---

## 7. Developer Pain Points

1. **Windows desktop stability/update reliability is the #1 complaint.** A relentless cluster — disappearing projects (#42739), infinite MSIX update loops (#38843), white-screen crash loops (#57562), suspended processes — erodes the primary Windows experience.
2. **File-attachment/upload regression across platforms.** XLSX and PPTX sends hang after ~Oct 27–30 builds on both macOS and Windows (#58302, #58258, #58313, #58660), with evidence of a looping capability preflight (`creation-capabilities`) and missing upload POSTs. High severity for Office-centric workflows.
3. **"Selected model is at capacity" is no longer credibly a capacity issue.** It appears in fresh-vs-existing chat patches (#59059), across Desktop/CLI/Work (#57975), and for Pro 200 subscribers (#59088) — pointing at stale session/catalog state. PR #59111 targets exactly this.
4. **MCP reliability + safety-check false positives.** Discussion #33682 (👍22) alleges systematic neglect of OAuth/discovery bugs; #59112 and #57328 report excessive cybersecurity refusals blocking harmless work. #59107 (opt-in reconsideration) is a promising mitigation.
5. **Small, chronic UX defects that never close** — TUI backspace (#17793 since April), quota indicator removal (#57656, 👍20), non-configurable shortcuts — cumulatively degrade trust in prompt-level ergonomics.

---
*Note: Issue creation dates in the source data (e.g., 2026-04) span several months despite "updated in last 24h" filtering; recency reflects last-activity, not origin.*

</details>

<details>
<summary><strong>Gemini CLI</strong> — <a href="https://github.com/google-gemini/gemini-cli">google-gemini/gemini-cli</a></summary>

# Gemini CLI Community Digest — 2026-11-03

## Today's Highlights

The nightly channel shipped a stability fix for interrupted Whisper model downloads that previously left partial files masquerading as installed models. Issue traffic this cycle is dominated by **agent reliability**: runaway/hanging subagents, MAX_TURNS being misreported as successful goal completion, and agents ignoring configuration or under-utilizing skills. A significant cluster of PRs also landed around robustness in headless runs, ACP integrations, and platform quirks (WSL2, Wayland).

---

## Releases

**v0.67.0-nightly.20261103.g0ac57c029**
- `fix(core)`: interrupted/truncated Whisper model downloads are no longer treated as installed — previously a partial file at `~/.gemini/whisper_models/<modelName>` would pass `isModelInstalled()` and break audio features silently. ([#30058](https://github.com/google-gemini/gemini-cli/pull/30058))

---

## Hot Issues

1. **[#22323](https://github.com/google-gemini/gemini-cli/issues/22323) — Subagent MAX_TURNS reported as GOAL success (p1, 13 comments)**
   `codebase_investigator` returns `status: success` / `Termination Reason: GOAL` despite hitting the turn limit before doing any work. This is dangerous for automation: orchestrators can't distinguish truncated work from completed work. Highest-comment issue in the window and marked for retesting.

2. **[#21409](https://github.com/google-gemini/gemini-cli/issues/21409) — Generalist agent hangs forever (p1, 8 👍)**
   Any delegation to the generalist agent (even folder creation) hangs; users report waiting up to an hour. Workaround: disable subagents entirely. Strong upvote signal (8 👍) suggests wide impact.

3. **[#21968](https://github.com/google-gemini/gemini-cli/issues/21968) — Gemini rarely uses skills/sub-agents autonomously**
   Anecdotal but resonant: the model only invokes custom skills when explicitly told, despite highly relevant descriptions (gradle, git). Points to a routing/prompting gap in skill activation.

4. **[#22267](https://github.com/google-gemini/gemini-cli/issues/22267) — Browser Agent ignores `settings.json` overrides**
   `AgentRegistry` merges config correctly, but the Browser Agent bypasses it — e.g., `maxTurns` is ignored. A configuration-integrity bug that undermines user trust in settings.

5. **[#19873](https://github.com/google-gemini/gemini-cli/issues/19873) — Zero-dependency OS sandboxing + post-execution intent routing (maintainer-authored)**
   Proposes leaning into Gemini 3's native bash affinity (grep/cat/sed/awk chaining) with sandboxing instead of restricting it. A strategic direction issue with 9 comments.

6. **[#22745](https://github.com/google-gemini/gemini-cli/issues/22745) — AST-aware file reads/search/mapping EPIC**
   Tracks whether AST tooling reduces misaligned reads and token noise. Companion issues [#22746](https://github.com/google-gemini/gemini-cli/issues/22746) (AST CLI mapping, tilth/glyph) and [#22747](https://github.com/google-gemini/gemini-cli/issues/22747) (ast-grep for reads/searches) show a coordinated investigation effort.

7. **[#24246](https://github.com/google-gemini/gemini-cli/issues/24246) — 400 error with >128 tools**
   Tool-count ceiling causes hard API failures; request is for smarter tool scoping. Relevant as MCP/extension ecosystems grow.

8. **[#22672](https://github.com/google-gemini/gemini-cli/issues/22672) — Agent should discourage destructive behavior**
   Model occasionally reaches for `git reset`/`--force` when safer paths exist. Maintainer-filed; likely feeds into policy/safety work.

9. **[#23571](https://github.com/google-gemini/gemini-cli/issues/23571) — Model litters repo with tmp scripts**
   When shell execution is restricted, the model scatters edit scripts across directories, complicating clean commits. A workflow-hygiene pain point.

10. **[#21983](https://github.com/google-gemini/gemini-cli/issues/21983) — Browser subagent fails on Wayland (p1)**
    Reported as completing with `Termination Reason: GOAL` despite failing — another instance of misleading success reporting, this time platform-specific.

Also notable: [#20079](https://github.com/google-gemini/gemini-cli/issues/20079) (symlinked agent files not discovered) and [#21763](https://github.com/google-gemini/gemini-cli/issues/21763) (`/bug` reports omit subagent context, hampering triage).

---

## Key PR Progress

1. **[#30020](https://github.com/google-gemini/gemini-cli/pull/30020) — Drop tool calls from failed retry attempts**
   On mid-stream retries, `GeminiChat` yielded chunks from both attempts, causing side-effecting commands to execute twice. Important correctness fix for retry semantics.

2. **[#30094](https://github.com/google-gemini/gemini-cli/pull/30094) — Show executing subagent tool calls in UI (fixes #22589)**
   `useToolScheduler` filtered out non-root scheduler calls that skipped approval (reads/searches), so subagent activity was invisible. Improves observability significantly.

3. **[#30107](https://github.com/google-gemini/gemini-cli/pull/30107) — Clickable auth URL on wrapped terminals (p1, fixes #29430)**
   Long sign-in links wrapping in narrow terminals got truncated by URL detection, producing Google `Error 400`. A high-friction onboarding fix.

4. **[#30051](https://github.com/google-gemini/gemini-cli/pull/30051) — Kill shells & persist tool calls on headless interruption**
   Fixes headless runs (`gemini -p --yolo`) exiting with code 0 on SIGINT/SIGTERM and leaving detached shell processes alive — critical for CI usage.

5. **[#29958](https://github.com/google-gemini/gemini-cli/pull/29958) — Send ACP clients shell output for AnsiOutput results**
   ACP clients received empty `tool_call_update` for `run_shell_command` because the PTY snapshot type wasn't forwarded. Fixes IDE/editor integration visibility.

6. **[#29951](https://github.com/google-gemini/gemini-cli/pull/29951) — Retry transient VS Code downloads in integration tests (p0)**
   Nightly release failed on a single network timeout with no actual test failures. Directly addresses CI flakiness at the highest priority level.

7. **[#30097](https://github.com/google-gemini/gemini-cli/pull/30097) — Image paste on WSL2 (WSLg clipboard + BMP fallback)**
   Falls back to `WAYLAND_DISPLAY`/`DISPLAY` when `XDG_SESSION_TYPE` is unset (WSLg) and adds BMP support. Fixes #22274.

8. **[#29831](https://github.com/google-gemini/gemini-cli/pull/29831) — Surface ripgrep timeouts as errors (closed)**
   Previously, timeout-killed ripgrep returned partial matches as "No matches found" (1–3 hits) or silent partial results (>3). Correctness fix for search tool honesty.

9. **[#29978](https://github.com/google-gemini/gemini-cli/pull/29978) — Preserve oversized/boundary lines in `read_background_output`**
   64KB window trimming could return an empty "Full Log Output:" for single-line logs. Small but high-impact for background process debugging.

10. **[#30003](https://github.com/google-gemini/gemini-cli/pull/30003) / [#30096](https://github.com/google-gemini/gemini-cli/pull/30096) — `${VAR:-default}` honors default when var is set-but-empty**
    Aligns settings env-var expansion with Bash semantics. Two competing PRs for the same fix (see #30000) — likely deduplication needed.

Honorable mentions: [#30102](https://github.com/google-gemini/gemini-cli/pull/30102) (IDE connections logged as `ide_connection` instead of inflating `start_session` counts), [#29960](https://github.com/google-gemini/gemini-cli/pull/29960) (telemetry `SERVICE_VERSION` now reports CLI version, not Node runtime), and [#30104](https://github.com/google-gemini/gemini-cli/pull/30104) (refresh slash commands after `/skills link`).

---

## Feature Request Trends

- **Agent orchestration ergonomics**: parallel subagent collaboration and shared memory ([#18287](https://github.com/google-gemini/gemini-cli/issues/18287)), subagent trajectory visibility via `/chat share` ([#22598](https://github.com/google-gemini/gemini-cli/issues/22598)), and discovery of subagents via `settings.json` ([#18285](https://github.com/google-gemini/gemini-cli/issues/18285)).
- **Token-efficient, precision context**: "Tactful Extraction" surgical reads ([#19561](https://github.com/google-gemini/gemini-cli/issues/19561)) and the AST-aware EPIC family — a clear push toward fewer turns and less context bloat.
- **Persistent task state**: replacing in-context WriteToDo with file-based CRUD tracking ([#18836](https://github.com/google-gemini/gemini-cli/issues/18836)) and native file tools for the task tracker ([#21000](https://github.com/google-gemini/gemini-cli/issues/21000)).
- **Self-knowledge**: agent should accurately describe its own flags, hotkeys, and invocation patterns ([#21432](https://github.com/google-gemini/gemini-cli/issues/21432)).
- **Resilience features**: browser agent session takeover/lock recovery ([#22232](https://github.com/google-gemini/gemini-cli/issues/22232)); per-workspace policy instead of global ([#18397](https://github.com/google-gemini/gemini-cli/issues/18397)).

## Developer Pain Points

- **Misleading success reporting**: repeated pattern of `Termination Reason: GOAL` masking turn-limit hits and platform failures (#22323, #21983). Orchestration and evals can't trust agent output status.
- **Hangs and stalls**: generalist agent hangs (#21409), interactive-prompt deadlocks when scaffolding with vite (#22465), and crashes on output hooks (#22186).
- **Configuration is not honored consistently**: Browser Agent ignores `settings.json` (#22267); symlinked agent files aren't discovered (#20079).
- **Workspace hygiene**: stray temp scripts (#23571) and destructive git commands (#22672) create cleanup overhead and risk.
- **Triage friction**: `/bug` reports lack subagent context (#21763); eval stability is under active investment (#23166, #23313).
- **CI flakiness from transient network failures** is being actively addressed (#29951), alongside duplicate/competing PRs for the same fixes (#30003 vs #30096, #29831) signaling process overhead.

---

*Digest generated from repository metadata for 2026-11-03. Issue/PR priorities and labels quoted as shown in source data.*

</details>

<details>
<summary><strong>GitHub Copilot CLI</strong> — <a href="https://github.com/github/copilot-cli">github/copilot-cli</a></summary>

# GitHub Copilot CLI — Community Digest
**2026-11-03**

## 1. Today's Highlights
A rapid release cadence continues with four builds shipped in 24 hours, headlined by a unified `/settings` dashboard with global search and ACP-mode fixes (the long-standing `includeCoAuthoredBy: false` bug is now resolved). Meanwhile, the issue tracker shows strong momentum behind keyboard/input reliability, MCP authentication robustness, and ACP (Agent Client Protocol) parity gaps. Community engagement is concentrated on UX fundamentals: message queue cancellation (#1857), Windows line-ending corruption (#1148), and terminal program-status reporting (#5112).

## 2. Releases
Four versions shipped in the last 24h:

- **v1.0.106-6**
  - **Added:** `/settings` now opens the unified dashboard for all users, with global search across settings and local resources.
  - **Added:** Interactive CLI reports program status to supporting terminals (attention-needed signaling).
  - **Improved:** MCP content addressed only to the user is no longer sent to the model — a privacy/context-hygiene improvement.
- **v1.0.106-5** — **Fixed:** `includeCoAuthoredBy: false` now honored in ACP mode (no more unwanted Co-authored-by trailers); targeted `/settings` commands reflect selections immediately; telemetry-disabled warning on session resume corrected.
- **v1.0.106-4** — **Fixed:** Empty sessions no longer flash in the session picker; `@file` mentions inside parentheses/brackets/quotes/backticks now attach and expand correctly in shell commands; custom agent fixes.
- **v1.0.106-3** — **Added:** Opt-in session management tools and `@mentions` for local sessions; **Improved:** managed-account policy refresh warnings include Policy Helper findings; **Fixed:** reuse of valid MCP OAuth credentials for anonymous connections.

> Note: v1.0.106-6 partially overlaps with the open feature request in #5112 (OSC 7501 program status) — worth watching whether the implementation aligns with that spec.

## 3. Hot Issues

1. **[#1857](https://github.com/github/copilot-cli/issues/1857) — Cancel/remove enqueued messages (15 comments, 👍33, OPEN)** ⭐ *Top engagement*
   No way to cancel queued messages (`Ctrl+Q`/`Ctrl+Enter`) while the agent is busy or during `/compact`. Highest community reaction this cycle — a clear queue-control UX gap.

2. **[#1148](https://github.com/github/copilot-cli/issues/1148) — CRLF line endings forced on LF files (Windows) (7 comments, CLOSED)**
   A significant file-integrity bug now closed. High-impact for cross-platform repos and diffs — closure signals a fix landed, but closure without a linked PR warrants verification by Windows users.

3. **[#5112](https://github.com/github/copilot-cli/issues/5112) — OSC 7501 program status in interactive CLI (5 comments, 👍11, OPEN)**
   Requests standardized terminal state reporting (working/blocked). The v1.0.106-6 "program status" addition may be a partial step; community will scrutinize spec conformance.

4. **[#1257](https://github.com/github/copilot-cli/issues/1257) — Termux/Android support (6 comments, 👍7, OPEN)**
   `pty.node` native module fails to load on Android. Persistent demand for mobile/on-the-go usage; blocked by native dependency architecture.

5. **[#5171](https://github.com/github/copilot-cli/issues/5171) — NumLock-off numpad navigation ignored (Windows) (2 comments, OPEN)**
   Regression affecting keyboard accessibility on Windows Terminal. Part of a broader input-handling cluster.

6. **[#3015](https://github.com/github/copilot-cli/issues/3015) — Authorization header dropped on MCP `tools/call` after OAuth (2 comments, 👍2, OPEN)**
   Bearer token sent on `initialize` but omitted on subsequent JSON-RPC calls with Streamable HTTP + OAuth 2.1/DCR. Serious for MCP server authors relying on per-call auth.

7. **[#5145](https://github.com/github/copilot-cli/issues/5145) — 400 `reasoning_text` must be passed back (2 comments, OPEN)**
   Custom OpenAI-compatible providers with `wireApi: "responses"` fail after background shells exit (synthesized assistant turn). Blocks advanced BYO-model workflows.

8. **[#4646](https://github.com/github/copilot-cli/issues/4646) — Compaction fails "Tool choice must be auto" on custom models (2 comments, OPEN)**
   `/compact` breaks with custom OpenRouter/GLM models. Compounds the custom-model pain seen in #5145.

9. **[#5304](https://github.com/github/copilot-cli/issues/5304) — `/keep-alive` ineffective in WSL2 (2 comments, OPEN)**
   `systemd-inhibit` doesn't create a Windows host power request. Breaks long-running tasks in a very common dev setup.

10. **[#5335](https://github.com/github/copilot-cli/issues/5335) — Repeated HTTP 422 cybersecurity-risk flags block local debugging (OPEN)**
    False-positive safety filters interrupt legitimate flaky-test debugging. High-friction for security-adjacent development; expect traction.

*Honorable mentions:* #4891 (Windows hard-crash 0xc0000005 losing in-flight turns), #5206/#5268/#5269/#4700/#4106 (a wave of ACP parity bugs — many closed today), #5336 (per-run MCP disable switch).

## 4. Key PR Progress
Only **1 PR** was updated in the last 24h:

1. **[#5339](https://github.com/github/copilot-cli/pull/5339) — Read-only agent monitor TUI (Graph + Timeline) (OPEN)**
   Adds `tools/agent-monitor`, an independent terminal viewer for a selected local session. Visualizes agent parentage and wait relationships (who launched whom, who blocked on whom). Useful for debugging multi-agent orchestration; styled after Copilot CLI's own interface language.

> With no other PR activity, the release pipeline appears to be shipping directly rather than via the public PR queue — a trend worth tracking for community visibility.

## 5. Hot Discussions
*No Discussion data was provided in this dataset — section omitted.*

## 6. Feature Request Trends
Distilled from all Issues and the single PR:

- **Input & keyboard control (3+ issues):** Cancel enqueued messages (#1857), numpad navigation (#5171), composer-draft read/restore API for extensions (#5338), Ctrl+K undo (#5331), image paste in devcontainers (#5334). This is the **largest cluster** — users want polished, standard-conforming terminal editing.
- **ACP parity hardening:** A large share of today's issues (#4700, #4106, #5133, #5268, #5269, #5336) and fixes target `copilot --acp`. ACP is becoming a first-class surface, and clients are hitting feature/settings gaps.
- **MCP robustness & control:** Per-run MCP disable switch (#5336), OAuth header persistence (#3015), user-addressed content filtering (shipped in 106-6).
- **Observability & status:** OSC 7501 program status (#5112) and agent monitor TUI (#5339) — growing demand for visibility into agent state and orchestration.
- **Platform breadth:** Termux/Android (#1257), WSL2 keep-alive (#5304), Windows PowerShell `-LiteralPath` guidance (#5340).
- **Theming/accessibility:** Custom prompt coloring (#5327).

## 7. Developer Pain Points
- **Windows platform friction dominates:** CRLF file corruption (#1148), hard crashes losing turns (#4891), numpad input regressions (#5171), and PowerShell wildcard path bugs (#5340) form a recurring theme. Windows users are a vocal, affected segment.
- **Custom/BYO-model support is fragile:** Compaction failures (#4646) and reasoning-content roundtrip errors (#5145) block users on OpenAI-compatible providers. This undermines Copilot CLI as a model-agnostic harness.
- **ACP mode lags the interactive CLI:** Settings (`skillDirectories`, `includeCoAuthoredBy`), plugin skills, subagent identity, and session commands all have or had ACP-specific gaps. Clients integrating via ACP feel second-class.
- **Safety filters cause false positives:** Cybersecurity-risk 422s (#5335) interrupt legitimate debugging, echoing broader developer concern about over-aggressive guardrails.
- **Silent failures:** Permission denials in resumed sessions (#5206) and dropped MCP auth headers (#3015) fail quietly, which is harder to diagnose than loud errors.
- **Queue/terminal UX gaps:** Inability to cancel enqueued work (#1857) and missing undo in the prompt editor (#5331) signal the interactive loop still needs maturity.

---
*Digest generated from data snapshot 2026-11-03. All links point to github.com/github/copilot-cli.*

</details>

<details>
<summary><strong>OpenCode</strong> — <a href="https://github.com/anomalyco/opencode">anomalyco/opencode</a></summary>

# OpenCode Community Digest — 2026-11-03

## 1. Today's Highlights

No releases in the last 24 hours, but issue activity is dominated by **free-tier quota problems** (the mega-thread #59387 now sits at 230 comments) and a wave of **Companion/Desktop stability reports** (local server startup failures, IPC errors, and stale background services). On the engineering side, several well-scoped fixes landed targeting **compaction behavior**, **SSE keepalive handling**, and **session terminal shell resolution**, while multiple i18n/ecosystem doc PRs continue to flow in from contributors.

## 2. Releases

No new releases in the last 24h.

## 3. Hot Issues

1. **[#59387](https://github.com/anomalyco/opencode/issues/59387) — Free tier issues: read this first, then post your report here** (230 comments, 👍 17)
   The central clearinghouse for free-tier complaints: quota-exceeded errors, reset times that never arrive, and Desktop builds refusing to run. This is the single highest-signal thread for gauging free-tier sentiment.

2. **[#61346](https://github.com/anomalyco/opencode/issues/61346) — [reproduced] compaction: stuck on "Compacting" with no feedback when provider fails or sends only SSE keepalives**
   A `/compact` sat for 90+ minutes with no error while upstream returned `503 no eligible upstream account`. Breaks the retry/notice UX and required manual interrupt to recover.

3. **[#61072](https://github.com/anomalyco/opencode/issues/61072) — Claude Agent: 5-hour plan limit drains ~1.8x faster because SDK labels requests `sdk-ts`**
   A measurable cost regression for Claude Agent users — the Agent SDK entrypoint label appears to affect quota accounting, making OpenCode ~1.8x more expensive than Claude Code's own TUI for the same work.

4. **[#61371](https://github.com/anomalyco/opencode/issues/61371) — core: transcript shortening clips every tool output in an exchange at once, leaving most of the window unused**
   A compaction algorithm flaw: `flattenAndDropOldest` operates per-exchange, so a long post-checkpoint exchange gets truncated wholesale rather than proportionally. Inefficient context use with real token cost implications.

5. **[#50633](https://github.com/anomalyco/opencode/issues/50633) — feature(desktop): system tray icon with clean shutdown of background `opencode-cli` service (Windows)** (9 comments, 👍 4)
   The spawned `opencode-cli.exe` outlives the Desktop window and keeps a stale config loaded. A long-lived request with sustained 👍 indicating real desktop friction.

6. **[#51051](https://github.com/anomalyco/opencode/issues/51051) — [reproduced] session terminal ignores the `shell` config option (always PowerShell on Windows)**
   Documented behavior diverges from implementation: the agent's shell tool respects `shell`, session terminals only read `process.env.SHELL` (unset on Windows). A clean, fixable config/implementation gap.

7. **[#61363](https://github.com/anomalyco/opencode/issues/61363) — Bug: TUI intermittently unresponsive on fresh launch with background server**
   Intermittent startup hang tied to background server boot. Combined with #61357 and #61226, this forms a cluster of "fresh launch is flaky" reports.

8. **[#61357](https://github.com/anomalyco/opencode/issues/61357) — An error occurred while starting the local server (Desktop IPC handler failed)**
   Desktop-side IPC failure surfacing during local server startup. Symptom-level, but part of the broader Desktop reliability theme.

9. **[#61232](https://github.com/anomalyco/opencode/issues/61232) — provider(opencode-go/glm-5.3-flash): user message starting with an absolute file path always fails with empty response + auto-retry**
   Deterministic provider bug: messages beginning with an absolute path never reach the model. Highly reproducible and worth watching for a quick fix.

10. **[#54863](https://github.com/anomalyco/opencode/issues/54863) — [FEATURE]: Indexed cross-session transcript search in the V2 SDK (SQLite FTS5)** (👍 1)
    `session.list({ search })` only matches titles; finding sessions by content requires full scans. Relevant to SDK consumers building bots (e.g., Kimaki) and a sensible long-term platform improvement.

## 4. Key PR Progress

1. **[#61376](https://github.com/anomalyco/opencode/pull/61376) [CLOSED] — fix(ai): don't let SSE keepalives reset the chunk timeout**
   Directly addresses the compaction stall in #61346 (keepalive half). Prevents SSE keepalives from indefinitely extending the chunk timeout.

2. **[#61353](https://github.com/anomalyco/opencode/pull/61353) — fix(server): resolve the configured shell for session terminals**
   Fixes #51051 by routing session terminals through the configured `shell` instead of `process.env.SHELL`.

3. **[#55208](https://github.com/anomalyco/opencode/pull/55208) — fix: Rehook compaction**
   Rebuilds reduced drafts before rerunning compaction hooks, so shrinking no longer restores checkpoint text a request hook removed. Closes #54916.

4. **[#61360](https://github.com/anomalyco/opencode/pull/61360) — fix(opencode): cancel pending tool approvals with their owner**
   Cancelling a V1 session now ends its tool permission wait and removes the stale approval card — a real UX correctness fix for interrupted sessions.

5. **[#61351](https://github.com/anomalyco/opencode/pull/61351) [CLOSED] — fix(ai): ignore tool-call deltas arriving after `finish_reason`**
   Hardens the OpenAI Chat parser against non-compliant routers (e.g., Poe) that stream tool-call chunks after finish — previously crashed the session with `InvalidProviderOutput`.

6. **[#58492](https://github.com/anomalyco/opencode/pull/58492) [CLOSED] — fix(core): never let compaction ceiling exceed the output reservation**
   Fixes inverted ceilings on large-window models by capping the compaction ceiling relative to the output reservation.

7. **[#59707](https://github.com/anomalyco/opencode/pull/59707) — fix(core): log skipped plugin discovery**
   Addresses silent plugin-dir drops (including the NixOS/home-manager symlink case) — diagnostics for a recurring configuration mystery.

8. **[#59528](https://github.com/anomalyco/opencode/pull/59528) — fix(core): drop a well-known source when its last credential is removed**
   `opencode auth logout` now cleans up the `.well-known/opencode` source so it doesn't fall back to stale state.

9. **[#55484](https://github.com/anomalyco/opencode/pull/55484) — feat(core): add scrape.do web search provider**
   Adds a new web search backend — evidence that the search-provider surface keeps expanding.

10. **[#57358](https://github.com/anomalyco/opencode/pull/57358) — fix(app): keep local sessions in the selected directory**
    Prevents local sessions from escaping the selected directory (refs #51191, addresses #51992). Important for multi-project workflows.

## 5. Hot Discussions

No dedicated Discussion data was provided; this section is omitted.

## 6. Feature Request Trends

- **Localization / i18n**: Multiple PRs ([#61361](https://github.com/anomalyco/opencode/pull/61361), [#61366](https://github.com/anomalyco/opencode/pull/61366)) target Russian UI and TUI localization, continuing long-running refs (#37216, #50164).
- **Desktop lifecycle & service management**: System tray + clean background-service shutdown ([#50633](https://github.com/anomalyco/opencode/issues/50633)); GUI plugins loading in the web app ([#60976](https://github.com/anomalyco/opencode/issues/60976)).
- **Context / compaction transparency**: Per-turn context drift inspector ([#58459](https://github.com/anomalyco/opencode/issues/58459)) and per-session Go allowance persistence ([#59653](https://github.com/anomalyco/opencode/issues/59653)).
- **SDK observability/search**: SQLite FTS5 indexed transcript search ([#54863](https://github.com/anomalyco/opencode/issues/54863)).
- **Session management UX**: Delete sessions from the TUI ([#26056](https://github.com/anomalyco/opencode/issues/26056)) — a long-standing, low-friction request.
- **Ecosystem expansion**: New Telegram and QQ bot channel plugins ([#59301](https://github.com/anomalyco/opencode/pull/59301), [#61355](https://github.com/anomalyco/opencode/pull/61355)).

## 7. Developer Pain Points

- **Free-tier quota accounting is the top complaint** — reset times not working, "used today's free messages" on first request, and Desktop refusing to launch for free users. The 230-comment thread (#59387) plus multiple duplicates (#61222, #61375) signal a genuine bug and a support-backlog problem.
- **Compaction is a recurring fault line**: stalls with no feedback on provider failure (#61346), inefficient per-exchange truncation (#61371), and ceiling inversion on large-window models (#58492). Also a reported regression in context efficiency for "Big Pickle" (#61352).
- **Desktop/TUI startup reliability**: stale background services, IPC failures, and intermittent startup hangs (#50633, #61357, #61226, #61363, #60854) produce a cluster of "fresh launch is flaky" reports.
- **Hidden cost regressions**: Claude Agent quota draining ~1.8x faster (#61072) erodes trust in provider integration accounting.
- **Config options silently ignored**: `shell` (#51051) and plugin discovery symlinks (#59707) — configuration that appears valid but isn't honored.
- **Provider edge-case crashes**: empty responses on path-prefixed messages (#61232), post-finish tool deltas (#61351), and scope errors after upgrades (#61342) point to brittleness in the provider layer.

*Note: Commentary is based solely on the provided issue/PR metadata (titles, summaries, counts); no external verification was performed.*

</details>

<details>
<summary><strong>Pi</strong> — <a href="https://github.com/earendil-works/pi">earendil-works/pi</a></summary>

# Pi Community Digest — 2026-11-03

## 1. Today's Highlights

Pi v1.5.0 shipped with visible extension changelogs on fresh launches. Meanwhile, a regression in 0.99.x around terminal color query replies (mintty/ConPTY) remains the most active issue this cycle, and a cluster of schema-resolution, context-budget, and RPC lifecycle fixes landed via bot-authored PRs.

## 2. Releases

**v1.5.0** — New Features
- **Extension update changelogs**: after updating installed extension packages, the next fresh launch shows what changed. Package authors can choose the changelog path, and `collapseChangelog` condenses the notices.
- Link: https://github.com/earendil-works/pi/releases

## 3. Hot Issues

1. **#10256 [OPEN] [bug, inprogress] Terminal color query replies leak into prompt; BEL opens external editor (mintty/ConPTY)** — 21 comments, 👍4. Regression between 0.87.1 (fine) and 0.99.x; startup emits raw color-query escape sequences into the prompt. High-impact for Windows mintty users. https://github.com/earendil-works/pi/issues/10256
2. **#10497 [CLOSED] [bug] OpenRouter Error: 400** — 15 comments. Context-length overruns when extensions inject file contents; points to token-budget accounting gaps with OpenRouter's stricter pre-routing checks. https://github.com/earendil-works/pi/issues/10497
3. **#10605 [OPEN] [bug] ChatGPT/OpenAI OAuth 403** — 12 comments. "ChatGPT user is not eligible for subscription sharing" blocks Plus-tier OAuth users even after re-login. https://github.com/earendil-works/pi/issues/10605
4. **#10956 [OPEN] cacheWarming "idle" stops silently after turn_end + custom/compaction message** — 11 comments, 👍2. Idle cache warming never fires (`Inactive (conversation context changed)`); affects cost/latency for long sessions. https://github.com/earendil-works/pi/issues/10956
5. **#10470 [OPEN] RPC new_session/switch_session/fork/clone emit session_start twice** — 8 comments. Duplicate lifecycle events break extension state machines in RPC mode. https://github.com/earendil-works/pi/issues/10470
6. **#11021 [OPEN] ChatGPT sign-in uses 272K context for gpt-6.1-sol despite 500K catalog** — 7 comments, 👍7. Undersized context budget triggers premature compaction; strong community agreement. https://github.com/earendil-works/pi/issues/11021
7. **#11599 [OPEN] RPC: async ctx.shutdown() does not exit idle process** — 5 comments. Headless/RPC embedders see hung processes on async shutdown. https://github.com/earendil-works/pi/issues/11599
8. **#9276 [OPEN] [bug, inprogress] grep tool with context lines can cause OOM** — 4 comments. Reading whole matched files for context lines caused heap exhaustion in headless SDK use. https://github.com/earendil-works/pi/issues/9276
9. **#11697 [OPEN] [fix-available] Relative `$id` resolved twice breaks tool arg validation** — 3 comments. Valid tool arguments rejected when schemas use relative `$id` + local `$ref`. https://github.com/earendil-works/pi/issues/11697
10. **#11699 [OPEN] Remove tool truncation in codemode** — 1 comment. Request to let codemode chain tools without truncation, since truncation defeats its main value. https://github.com/earendil-works/pi/issues/11699

## 4. Key PR Progress

1. **#11726 [OPEN] fix(schema): apply relative root `$id` only once when resolving references** — Fixes double-resolution of relative root `$id` that broke local `$ref`s (paired with issue #11697). https://github.com/earendil-works/pi/pull/11726
2. **#11725 [CLOSED] fix(ai): remove Codex SSE retry abort listener after backoff** — Prevents abort-listener leaks accumulating per retry in the Codex Responses provider. https://github.com/earendil-works/pi/pull/11725
3. **#11724 [CLOSED] fix(mcp): clean up server children on stdio close after server exited** — Ensures orphaned child processes in the MCP process group are killed even when the parent already exited. https://github.com/earendil-works/pi/pull/11724
4. **#11705 [OPEN] fix(coding-agent): take grep context from ripgrep instead of reading matched files** — Direct fix for the OOM in #9276; memory now scales with output, not file size. https://github.com/earendil-works/pi/pull/11705
5. **#11545 [OPEN] Anthropic message threads** — Uses Anthropic's server-side message threads beta, sending only new messages when caching is on; still falls back to full requests on mismatch. https://github.com/earendil-works/pi/pull/11545
6. **#11712 [CLOSED] [inprogress] fix(ai): leave room for OpenRouter input overcount when clamping max tokens** — Addresses OpenRouter's stricter tokenizer (up to ~12% overcount) causing 400s for near-window models. https://github.com/earendil-works/pi/pull/11712
7. **#11709 [CLOSED] fix(coding-agent): report installed version after npm/pnpm/yarn/bun self-update** — Stops claiming an update succeeded when the package manager actually reinstalled the old version. https://github.com/earendil-works/pi/pull/11709
8. **#11707 [CLOSED] feat(coding-agent): list updated packages with version changes in pi update** — Makes `pi update` output show which extensions changed and to what version. https://github.com/earendil-works/pi/pull/11707
9. **#11629 [CLOSED] feat(coding-agent): add PI_SELF_UPDATE_COMMAND and selfUpdateCommand** — Lets wrapper-managed installs (mise, nub, Nix) define how `pi update` should update Pi. https://github.com/earendil-works/pi/pull/11629
10. **#11686 [OPEN] feat: release version tags on pkg.earendil.com** — Fixes release pipeline so tagged release commits are actually built (v1.2.0–v1.4.0 had no builds). https://github.com/earendil-works/pi/pull/11686

## 5. Hot Discussions

**Ideas**
- **#4444 Supporting the Agent Client Protocol (ACP)** — 5 comments, 👍47 (highest-engagement item this cycle). Proposal to replace pi-specific RPC with a standard agent↔editor protocol so every editor doesn't need a bespoke client. https://github.com/earendil-works/pi/discussions/4444

**Q&A**
- **#11404 Official Pi code agent sandbox by Docker?** — 2 comments, 👍2. User found `sbx/pi`, `sbx/pi-mixin`, `sbx/pi-image`, and `sbx/pi-kit` images on Docker Hub and asks whether they're official. https://github.com/earendil-works/pi/discussions/11404

**Show and tell**
- **#11722 OnlyClaws Android — give Pi a phone claw (live wallpaper, overlay, TTS via `js`)** — A thin Android host running agent-pushed JS (Rhino + A2UI + transparent WebView overlay + TTS), echoing the low-cost-device agent pattern. https://github.com/earendil-works/pi/discussions/11722

## 6. Feature Request Trends

- **Standardized editor integration**: ACP support (#4444, 👍47) is the clearest signal that embedders want a protocol, not a custom RPC dialect.
- **Observability / determinism in extension machinery**: `--print-skills` / `pi skills` (#11700), exporting `VIRTUAL_MODULES` for embedders (#11727), and a settings-level skill prune control (#11701) all ask for inspectable, deterministic behavior instead of model-mediated output.
- **Extension capability expansion**: invoking registered/MCP tools from event hooks (#10264), background-job-aware status (#11702), and disabling built-in truncation in codemode (#11699).
- **Update/install UX**: package-level changelogs (shipped in v1.5.0), per-package version reporting (#11707), accurate post-update versions (#11709), and wrapper-defined self-update (#11629).
- **Session management**: hiding child sessions in `/resume` (#9847) and cross-restart durability (#11635).

## 7. Developer Pain Points

- **Context/token accounting mismatches**: OpenRouter's pre-routing tokenizer overcount (#10497, #11712) and ChatGPT OAuth reporting 272K instead of 500K (#11021) both produce 400s or premature compaction.
- **Headless/RPC mode rough edges**: duplicate `session_start` events (#10470), async `ctx.shutdown()` hangs (#11599), and extensions silently losing turns in `-p`/`--mode json` (#11011).
- **Memory safety in tools**: grep with context lines reading whole files caused production OOM in SDK use (#9276, PR #11705).
- **Silent failures**: length recovery dropping replies with exit 0 (#11012), image-only steer poisoning every subsequent request with empty text blocks (#11728), and extension-registered models crashing `calculateCost` (#10209).
- **Update-reliability trust**: stale registry metadata making `pi update` claim success while reinstalling old versions (#11707, #11709), and unsupported self-update paths for wrapper-managed installs.
- **Terminal/platform compatibility**: the 0.99.x mintty/ConPTY escape-sequence regression (#10256) and WSL clipboard history gaps (#6003) remain recurring Windows-adjacent friction.

</details>

<details>
<summary><strong>Qwen Code</strong> — <a href="https://github.com/QwenLM/qwen-code">QwenLM/qwen-code</a></summary>

# Qwen Code Community Digest — 2026-11-03

## 1. Today's Highlights

The project shipped **v0.26.6** alongside TypeScript SDK **v0.1.27**, headlined by a new Web Shell sidebar activity view for trusted workspaces and a batch of Anthropic provider timeout and transport fixes. CI health remains the dominant operational concern: long-running bot-generated failure issues (#14724 at 161 comments, #13078 at 40) are still accumulating, and a new P1 reports that bot workflow bursts are exhausting the `GITHUB_TOKEN` rate limit and breaking unrelated CI jobs. Meanwhile, session-state durability (SIGHUP losses, unrecorded tool results) and model-ID normalization bugs across Bedrock/OpenRouter/Ollama surfaced as active themes.

---

## 2. Releases

**CLI v0.26.6**
- **New:** Web Shell sidebar activity view for trusted workspace conversations, surfacing approval/input/error states and unread counts.
- No known breaking changes.
- Nightly `v0.26.5-nightly.20261102.0441faff21` also published.

**SDK TypeScript v0.1.27**
- Bundles CLI **0.26.6** (built from the same branch/ref as the SDK).

---

## 3. Hot Issues

1. **[#14724](https://github.com/QwenLM/qwen-code/issues/14724) — Main CI failed: Managed Agent gate, no test identified** (161 comments, open since 2026-10-19). The longest-running bot-filed CI failure thread; the lack of an identified test suggests a flaky or infrastructure-level gate rather than a code regression. High comment volume signals repeated re-triggering without resolution.

2. **[#13078](https://github.com/QwenLM/qwen-code/issues/13078) — Daily dependency CVE audit failed** (40 comments). A month-old, still-open security-hygiene failure. Persistent audit failures erode trust in the supply chain signal and are likely blocked on the npm audit endpoint or a genuine high-severity finding.

3. **[#16169](https://github.com/QwenLM/qwen-code/issues/16169) — Queued messages silently lost on SIGHUP** (P2). A message typed while a tool runs is held in the queue (footer shows `⏳ 1 queued`), then vanishes entirely on terminal close — not in the resumed conversation, not in any file. A clear data-loss UX bug with fresh community attention.

4. **[#15799](https://github.com/QwenLM/qwen-code/issues/15799) — Completed tool calls reported as "not recorded … Treat as failure and retry"** (P1). In-flight calls during abrupt session end are misclassified on resume. Same family as #16169: **session durability under interruption** is a recurring correctness gap.

5. **[#16144](https://github.com/QwenLM/qwen-code/issues/16144) — Web Shell GitHub/GitLab channels start open to anyone** (P1, security). Channels created from Web Shell default to `Open` group policy, contradicting docs that say group access is disabled by default — a security-relevant default mismatch.

6. **[#16188](https://github.com/QwenLM/qwen-code/issues/16188) — Bot workflow burst exhausts GITHUB_TOKEN rate limit** (P1). Four CI jobs on PR #16182 failed with HTTP 403 due to autofix/triage/review bot bursts. Directly impacts contributor velocity.

7. **[#16171](https://github.com/QwenLM/qwen-code/issues/16171) — Web Shell session open very slow: live replay retains full file contents from edit tool results** (P1, closed). Daemon resources normal; the slowdown is replay-payload bloat. Quickly closed, but highlights growing pains in Web Shell session scaling.

8. **[#15165](https://github.com/QwenLM/qwen-code/issues/15165) — Shell safety parser triggers ~1.6 GiB transient WASM allocation on Node 24.16.0** (P2, performance). Measured native-memory spike during V8 optimization of the bundled Bash WASM parser — a serious footprint concern for constrained environments.

9. **[#16157](https://github.com/QwenLM/qwen-code/issues/16157) / [#16158](https://github.com/QwenLM/qwen-code/issues/16158) / [#16159](https://github.com/QwenLM/qwen-code/issues/16159) — Model IDs lose context/output limits** (Bedrock, OpenRouter `:exacto`, Ollama HF quant tags). A coordinated trio from one reporter: normalization reduces IDs to garbage, silently falling back to 200K/32K defaults. Same root cause class across three providers.

10. **[#16127](https://github.com/QwenLM/qwen-code/issues/16127) — VS Code Companion 0.26.5 SIGSEGV decoding images with Sharp under Electron-as-Node** (P1, Linux). Crash-on-image-decode is a hard blocker for IDE users; complements #16063 (Generated UI MCP server spawned as Electron GUI instead of Node), suggesting a broader **Electron runtime-mode** issue cluster.

---

## 4. Key PR Progress

1. **[#16177](https://github.com/QwenLM/qwen-code/pull/16177) — fix(core): attribute monitor and workflow notifications to the registering turn.** Fixes background results arriving as a new turn instead of being included in the active turn's next model request — the server-side counterpart to Web Shell issue #16119.

2. **[#16193](https://github.com/QwenLM/qwen-code/pull/16193) — fix(core): honor Anthropic transport timeout configuration** (closed/merged). Passes the selected Undici dispatcher through the custom fetch; directly addresses #16187's 300s timeout despite configured longer timeouts.

3. **[#16199](https://github.com/QwenLM/qwen-code/pull/16199) — fix(serve): address remote-access review findings R1-6/7/9/11.** Follow-up to #15887, which merged with 17 unresolved review threads; this lands four verified functional/security fixes.

4. **[#16023](https://github.com/QwenLM/qwen-code/pull/16023) — feat(managed-agent): share resident external Workspace across sessions.** Reuses a Workspace-scoped worker binding across Sessions/Turns instead of teardown per cycle — a meaningful efficiency change for managed agents.

5. **[#16201](https://github.com/QwenLM/qwen-code/pull/16201) — feat(managed-agent): enforce row integrity for governed SQL catalogs.** Proposes governance for the 39-table resident Workspace stack, moving 73 CHECK predicates into complete-row Java validation.

6. **[#16064](https://github.com/QwenLM/qwen-code/pull/16064) — fix(cli): run bundled generated-ui server in Node mode under Electron.** Pins `ELECTRON_RUN_AS_NODE=1` for the stdio spawn path — the fix for the #16063 class of bugs.

7. **[#16117](https://github.com/QwenLM/qwen-code/pull/16117) — feat(channels): deliver GitHub events to PR-bound sessions.** Routes PR events (created/reviewed/verified bindings) into the owning session as prompts — a step toward event-driven agent workflows.

8. **[#16149](https://github.com/QwenLM/qwen-code/pull/16149) — feat(web-shell): edit GitHub repository settings from the channel editor.** Exposes account allowlists, no-@mention answering, and related policies in UI instead of hand-editing `settings.json` — relevant to the #16144 security-default discussion.

9. **[#16105](https://github.com/QwenLM/qwen-code/pull/16105) — feat(serve): configurable Conversations working-directory root.** Adds `--conversations-dir` / `QWEN_CONVERSATIONS_DIR` for `qwen serve` and `qwen daemon start`, with explicit precedence over environment.

10. **[#16190](https://github.com/QwenLM/qwen-code/pull/16190) (closed) / [#16192](https://github.com/QwenLM/qwen-code/pull/16192) (closed) — unified agent loop rollout.** ACP cron/background-notification turns now run on the shared loop (PR 2c-4, last ACP turn kind), and the nightly E2E lane gains a `QWEN_CODE_ENABLE_UNIFIED_AGENT_LOOP=1` leg — signals the default flip is approaching (#15327).

---

## 5. Hot Discussions

No Discussion data was provided in this dataset; section omitted.

---

## 6. Feature Request Trends

- **Fine-grained content referencing for token efficiency** — [#16173](https://github.com/QwenLM/qwen-code/issues/16173) asks for models to reference slices of files/tool outputs instead of duplicating them, targeting the `read > write > typo > re-write` loop. Fits the `roadmap/context-performance` theme.
- **Configuration control for managed/privacy-sensitive installs** — [#16006](https://github.com/QwenLM/qwen-code/issues/16006) (closed) requested disabling the daily models.dev catalog refresh from settings, honoring privacy opt-outs.
- **Desktop distribution breadth** — [#16034](https://github.com/QwenLM/qwen-code/issues/16034) (closed) requests rpm artifacts for Fedora/RHEL (currently AppImage-only, which cannot install), with an inline patch supplied.
- **Web Shell as a configuration surface** — multiple PRs (#16149) and issues point toward moving channel/repo policy out of `settings.json` and into UI.
- **Background automation semantics** — notification attribution (#16177/#16119) and PR-bound event delivery (#16117) indicate demand for agents that react mid-turn and to external events.

---

## 7. Developer Pain Points

1. **Session-state durability under interruption** — The strongest recurring cluster: queued messages lost on SIGHUP (#16169), completed calls misreported as failures after abrupt ends (#15799), and unrecorded-result messaging that forces retries. Users lose work and trust in resume semantics.
2. **CI signal noise and self-inflicted outages** — Bot-filed mega-threads (#14724, #13078, #15173, #12714) plus token-rate-limit exhaustion (#16188) mean CI failures are increasingly infrastructure/bot-driven, drowning real regressions and blocking unrelated PRs.
3. **Model-ID normalization is silently wrong** — A single reporter filed Bedrock (#16157), OpenRouter `:exacto` (#16158), and Ollama HF quant-tag (#16159) variants in one day; all silently fall back to default 200K/32K limits, causing wrong context budgeting with no warning.
4. **Electron runtime-mode bugs in the VS Code companion** — Generated UI MCP server launched as GUI instead of Node (#16063) and Sharp SIGSEGV under Electron-as-Node (#16127) break core IDE functionality on Linux.
5. **Memory/performance footprint of the shell safety layer** — ~1.6 GiB transient WASM allocation (#15165) and ~166 MiB per-sandbox overhead in Managed Runtime workers (#16147, closed) show the safety tooling carries real cost.
6. **Security defaults diverging from documentation** — Web Shell channels opening to anyone (#16144) while docs claim group access is off by default; security-sensitive reviewers are actively auditing permission paths (see also closed #13106 on `cd` redirect targets bypassing Write deny checks).

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar).*