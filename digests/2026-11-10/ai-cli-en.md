# AI CLI Tools Community Digest 2026-11-10

> Generated: 2026-11-09 23:19 UTC | Tools covered: 7

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

# Cross-Tool Comparison Report: AI CLI Tools Ecosystem
**Report date: 2026-11-10 | Tools covered: Claude Code, OpenAI Codex, Gemini CLI, GitHub Copilot CLI, OpenCode, Pi, Qwen Code**

---

## 1. Ecosystem Overview

The AI CLI ecosystem in late 2026 is converging on a shared architecture — agent loops, subagents, MCP-based tooling, sandboxed execution, and multi-surface deployment (CLI, TUI, desktop, mobile, web) — while differentiating primarily on model access, cost structure, and enterprise governance. Release cadence remains aggressive (Claude Code v2.1.321, Copilot CLI v1.0.109-0, Qwen Code v0.27.1 all shipped this window), but **reliability and cost trust have become the dominant community concerns**, not raw capability. Across all seven trackers, the highest-engagement threads are about quota accounting, silent failures, and sandbox-induced friction rather than missing features. Two structural trends stand out: first, **subagent/compaction correctness** is now the weakest link in agentic workflows; second, **approval and observability surface area** is expanding beyond the desktop (phone, watch, taskbar, OSC 7501 terminal status).

---

## 2. Activity Comparison

| Tool | Issues Activity | PR Activity | Discussions | Release Status (last 24h) |
|---|---|---|---|---|
| **Claude Code** | 10 hot issues; 1 stale/duplicate closures prominent | 1 PR updated (#89404) — self-hosting plugin fix | N/A (issue tracker is primary channel) | ✅ **v2.1.321** — Fable 5.2 default, 1M ctx, cheap cache reads, `--settings` |
| **OpenAI Codex** | 10 hot issues; #41220 quota tracker (84 comments) dominant | 10 PRs — hotfix backports, auth/rate-limit correctness | ✅ Active (9 show-and-tell / Q&A entries) | ✅ **2 alpha builds** (0.169.0-alpha.6/.7); stable line 0.167.0 w/ hotfix backports |
| **Gemini CLI** | 50 issues updated; subagent reliability cluster (P1s) | 44 PRs — P0 build fix, security, timeouts | No data provided | ❌ **No release** |
| **GitHub Copilot CLI** | 29 issues updated; MCP namespace regression (closed same-day) | **0 PRs** in window | N/A (no data) | ✅ **v1.0.109-0** and **v1.0.108** |
| **OpenCode** | 50 issues updated; free-tier mega-thread (339 comments) | 50 PRs — PTY protocol stack, config resilience | No data provided | ❌ **No release** |
| **Pi** | ~10 hot issues; Windows sink-thread #7547 (95 comments) | 10 PRs — Windows/PowerShell fixes, Fable 5.2 compat | No dedicated data (discussion in #7547) | ❌ **No release** (v1.5.0 regressions still being triaged) |
| **Qwen Code** | ~10 hot issues; CI gate #14724 (282 comments) | 10 PRs — unified agent loop, channels/QR login | N/A (no data) | ✅ **v0.27.1** + SDK 0.1.31 + desktop 0.27.1 |

**Notes:** Claude Code and Copilot CLI use issue trackers as their primary community forum — marked N/A for Discussions. OpenCode and Gemini CLI show the highest raw issue/PR throughput. Copilot CLI's zero-PR window is notable given it shipped two releases — work is landing via internal pipelines rather than public PRs.

---

## 3. Shared Feature Directions

Requirements appearing across multiple communities, ranked by breadth of demand:

| Direction | Tools Requesting | Specific Needs |
|---|---|---|
| **Cost/quota transparency & control** | Claude Code (#97074 headless 1.8× overhead), Codex (#41220 quota tracker, #60435 rate-limit UI), OpenCode (#59387 free-tier thread, #59861 catalog-price estimates), Copilot CLI (#5392 doubled prompt metering) | Per-mode token accounting, reset-timer reliability, opt-in cost estimates, budget-aware agent behavior |
| **Context efficiency & compaction correctness** | Claude Code (native Edit over bash), Codex (#55990 progress-aware budgets, #60297 BCC plugin), Gemini CLI (grep caps PR #30109, AST-aware EPIC #22745), OpenCode (#43250 budget overrun, #50814 tool-calls-as-text) | AST-aware reads, output capping, safe compaction semantics, per-agent context opt-outs |
| **Remote / out-of-band approval & monitoring** | Codex (#33528 watchOS, #60116 phone, #60441 Apple Watch), Claude Code (#92145 mobile remote approval), Copilot CLI (#5320 OSC 7501, #2616 OS notifications), OpenCode (#60411 OSC 7501) | Phone/watch approvals, terminal-native status reporting, push notifications for long tasks |
| **Sandbox usability & diagnostic clarity** | Codex (#41779 "blocked by policy" no logs, #60409 unnamed destination), Gemini CLI (#19873 sandbox + intent routing), Copilot CLI (#5390 loopback deny breaks Gradle, #5388 credential masking) | Named block reasons, loopback/localhost allowances, credential masking that works on enterprise tenants |
| **MCP robustness at scale** | Codex (#60383 16 MiB silent drop, #60423 deferred server discovery), Gemini CLI (#24246 >128 tools breaks requests), Copilot CLI (#5400 namespace regression), OpenCode (#62973 distinguish failed vs unconfigured) | Large-result erroring instead of hanging, tool scoping/selection, deferred-server queryability |
| **Multi-tool / multi-vendor interoperability** | Codex (#60185 transcript search across Codex+Claude Code, #58834 worktree coexistence), Claude Code (multi-account #18435) | Shared transcript search, worktree isolation per agent, profile switching without re-auth |
| **Windows parity** | Pi (entire #7547 thread — 95 comments), Codex (project sidebar loss, cloud-chat resume), Copilot CLI (PowerShell hangs, Git Bash), Gemini CLI (TortoiseGitPlink) | Path-separator globs, PS 5.1 stderr handling, UTF-16 BOM files, shell detection |

---

## 4. Differentiation Analysis

**Claude Code — Frontier model + cost-sensitive enterprise automation.**
Targets developers running high-volume headless pipelines on Anthropic models. Differentiator: tight model integration (Fable 5.2 default with aggressive cache pricing) and a very deep plugin/skill system. Its pain points (headless quota overhead, `includeCoAuthoredBy` non-compliance, multi-account) reveal a user base migrating from interactive to *production automation* — the tool is being stressed beyond its interactive design center.

**OpenAI Codex — Provider pluripotency + release hardening.**
The alpha-channel cadence and backport-driven stabilization (Bedrock/Mantle provider compat, SQLite WAL security fixes) show a team prioritizing *deployment breadth* — enterprise gateways, custom providers, Desktop shipping. Weakness: Windows Desktop regressions and sandbox diagnostics dominate the tracker, indicating a broad-surface, thinner-per-surface QA tradeoff.

**Gemini CLI — Agent architecture research with shell-first ergonomics.**
The dominant signals (#19873 zero-dependency sandboxing with intent routing, #22745 AST-aware tooling, unified delegation) point to an *architecture-forward* tool that wants the model to use bash natively but safely. Highest P1 subagent-correctness load (#22323, #21409) suggests the delegation model is the current engineering frontier. Also the most exposed to release-engineering fragility (nightlies broken ~1 week, PR #30207).

**GitHub Copilot CLI — Enterprise governance and native platform integration.**
Releases read like a security/compliance changelog: symlink confirmation, session-wide subdomain grants, GHE Data Residency, Entra/tenant auth, Kerberos proxy. Target user is the *enterprise platform team*, not the solo hacker. The absence of public PRs and the presence of internal statuspage issues (#5185) suggest a tightly-gated release process — consistency over velocity.

**OpenCode — Provider-agnostic, self-hosted infrastructure play.**
The v2 service architecture (process daemon, PTY protocol v8, managed service ports) and provider matrix (LM Studio, Claude Agent, EVREN, no-prefill models) indicate a tool betting on *plumbing* — local-daemon durability, multi-provider conformance — rather than frontier-model access. The free-tier mega-thread (339 comments) exposes the monetization tension of an open tool with a hosted service.

**Pi — Windows-first pragmatism and embedded/durable use cases.**
Two distinct commitments: (1) making Windows a first-class citizen (the 95-comment thread and a systematic `petrroll` PR batch fixing PS 5.1, UTF-16 BOM, mintty, glob separators), and (2) `pi-durable` as an embeddable harness (#11676, #11961) — a niche between CLI and SDK. The 1.5.0 regression cluster (#11869 P0 typing break, #11892 task-stuck) is the trust cost of a fast release.

**Qwen Code — Managed-agent platform expansion (channels, web shell, unified loop).**
The clearest *platform* trajectory in the set: channels (WeChat/QQ QR login), webhooks, Web Shell, managed sessions, unified agent loop (#15327) across CLI/TUI/ACP/daemon. Target user is the *integration builder* embedding an agent in chat platforms. Its burden is self-inflicted scale — CI gate failures and main-branch breakage (#16712) are the tax of a rapidly expanding surface area.

**Summary axis:** Claude Code/Codex compete on *model+provider capability*; Gemini CLI/Qwen Code compete on *agent architecture*; Copilot CLI competes on *enterprise trust*; OpenCode/Pi compete on *openness, portability, and durability*.

---

## 5. Community Momentum & Maturity

**Momentum (velocity + engagement intensity):**
- **OpenCode and Gemini CLI lead raw throughput** (50 issues + 50 PRs each in one day). OpenCode's 339-comment thread and maintainer-led PTY stack show a highly active core team; Gemini CLI's 44 PRs and P0 build fix show responsive triage.
- **Qwen Code iterates fastest on product surface** — three coordinated releases plus channel/web-shell PRs, but pays in CI instability (#14724 at 282 comments is noise, not signal).
- **Claude Code has the highest per-issue signal quality** — #18435 (1,025 👍) is the most-upvoted request across all trackers this window, indicating a large, opinionated user base whose top pain (multi-account) is a workflow blocker, not a wish.
- **Codex and Pi show concentrated, thematic engagement** — Codex's quota tracker and Pi's Windows omnibus are single threads absorbing dozens of reporters, a sign of mature triage consolidating duplicates.

**Maturity (stability, process discipline):**
- **Most mature process:** GitHub Copilot CLI (gated public PRs, same-day regression closures like #5400, security-led release notes) and Qwen Code (coordinated multi-package releases with backfill notes).
- **Least mature release hygiene:** Pi (1.5.0 regressions across typing and durability) and Gemini CLI (week-long nightly breakage from stale build artifacts).
- **Highest cost-trust exposure:** OpenCode (free-tier quota state unresolved after two weeks) and Codex (#41220 billing distrust at 84 comments) — both carry monetization/reputation risk disproportionate to their engineering output.

**Rapid-iteration ranking (most → least):** OpenCode ≈ Gemini CLI > Qwen Code > Codex > Claude Code > Pi > Copilot CLI (by public cadence; Copilot's velocity is hidden by process).

---

## 6. Trend Signals

Industry-level signals from this window's community feedback, with developer relevance:

1. **Cost trust is the new UX battleground.** Headless-vs-interactive token asymmetry (Claude Code #97074), doubled metering (#5392), quota threads (OpenCode #59387, Codex #41220) — *if you are building automation, budget for per-entrypoint accounting and verify meter parity before scaling*. Expect first-class budget APIs to become a purchase criterion.

2. **Compaction is the correctness frontier.** Tool-calls-as-text after compaction (OpenCode #50814) and unbounded walk-back (#43250) fail *silently*. *Treat post-compaction correctness as a testable contract* in any long-running agent deployment; demand tool-call dispatch integrity checks.

3. **Silent failure is the most damaging bug class.** 16 MiB MCP drops (#60383), globs returning nothing (#9262), tool_search false negatives (#11957), "no test identified" CI gates (#14724). *Instrument for fast, explicit errors*; prefer fail-loud over fail-quiet in agent harnesses.

4. **Approval/observability is going multi-surface.** Phone, watch, taskbar, OSC 7501, OS notifications — supervising agents away from the keyboard is now a table-stakes expectation. *Design hooks (e.g., `hooks.json`, ACP) with out-of-band approval as a first-class path.*

5. **MCP shifts from "supported" to "robust at scale."** Deferred-server discovery, tool caps beyond ~128, namespace preservation, large-result handling. *MCP developers should test limits/failure modes, not just happy paths.*

6. **Windows and enterprise auth are the real parity gaps.** Pi's 95-comment Windows thread, Codex's Windows Desktop regressions, Copilot's Entra/tenant/proxy friction. *If your tool targets enterprise developers, Windows + single-tenant identity + corporate proxy are adoption gates, not edge cases.*

7. **Subagent reliability undermines trust in "success."** Success-misreporting on turn-limit exit (#22323), hangs (#21409), false completion (Claude Code #92151). *Log and assert actual completion semantics rather than status string — and treat short/long task differentiation as a safety control.*

8. **Multi-vendor, multi-agent workflows are going mainstream.** Cross-transcript search (#60185), worktree coexistence (#58834), multi-account switching (#18435). *Expect users to run 2–3 CLI agents side by side; interoperability and isolation (worktrees, per-agent memory budgets, profile switching) are become differentiators.*

**Bottom line for decision-makers:** Capability is no longer the bottleneck — **cost predictability, failure honesty, and multi-surface governance** are. When evaluating an AI CLI for production, weight headless quota behavior, compaction/correctness semantics, sandbox diagnostic quality, and enterprise identity support above raw model benchmarks.

---

*Data basis: community digests for each repository as of 2026-11-10 (Claude Code, OpenAI Codex, Gemini CLI, GitHub Copilot CLI, OpenCode, Pi, Qwen Code). Issue/PR counts reflect items updated in the reporting window; engagement numbers are as reported in source snapshots.*

---

## Per-Tool Reports

<details>
<summary><strong>Claude Code</strong> — <a href="https://github.com/anthropics/claude-code">anthropics/claude-code</a></summary>

## Claude Code Skills Highlights

> Source: [anthropics/skills](https://github.com/anthropics/skills)

# Claude Code Skills Community Highlights Report
**Data as of 2026-11-10 | Source: github.com/anthropics/skills**

---

## 1. Top Skills Ranking

> Note: The PR dataset shows `Comments: undefined` for all entries, so ranking below is based on available signal (attention/recency/scope) and the top PRs listed. Where comment counts were unavailable, "N/A" is noted.

| Rank | Skill / PR | Functionality | Discussion Highlights | Status |
|---|---|---|---|---|
| 1 | [**mcp-builder fix** #1742](https://github.com/anthropics/skills/pull/1742) | Fixes MCP builder for `mcp>=2.0.0` — API rename (`streamablehttp_client` → `streamable_http_client`) and custom header handling via `create_mcp_http_client`. | Compatibility break with MCP v2; blocks MCP server scaffolding in skill. | **OPEN** (Comments N/A) |
| 2 | [**skill-creator fix** #1298](https://github.com/anthropics/skills/pull/1298) | Isolates trigger evals; fixes Windows subprocess `select()` failures and unrelated-tool scan noise that cause false-negative scores. | Core tooling reliability; cross-platform correctness. | **OPEN** (Comments N/A) |
| 3 | [**notion-spec-to-implementation + quantitative-resume-auditor** #1245](https://github.com/anthropics/skills/pull/1245) | Transforms Notion product/tech specs into actionable coding tasks with acceptance criteria; plus a resume-auditing skill. | New workflow-automation skill pair; long-lived (Jun→Oct). | **OPEN** (Comments N/A) |
| 4 | [**webapp-testing: remove shell=True** #1980](https://github.com/anthropics/skills/pull/1980) | Eliminates `shell=True` in `with_server.py` to fix CWE-78 command-injection risk. | Security hardening of a widely-used testing skill. | **OPEN** (Comments N/A) |
| 5 | [**docx timeout + output verification** #1792](https://github.com/anthropics/skills/pull/1792) | Reports LibreOffice timeout as error; verifies revision marks removed before claiming success. | Correctness/silent-failure fix in document pipeline. | **OPEN** (Comments N/A) |
| 6 | [**proofcore-contract-auditor** #1771](https://github.com/anthropics/skills/pull/1771) | Static analysis of Solidity/Rust contracts with on-chain audit proof anchoring (TON). | Web3 niche skill; novel "skill + chain notarization" pattern. | **OPEN** (Comments N/A) |
| 7 | [**pyxel skill** #525](https://github.com/anthropics/skills/pull/525) | Retro game development in Python — headless input-driven runs, frame inspection, state checks. | Long-standing (Mar→Nov); creative/gaming use case. | **OPEN** (Comments N/A) |
| 8 | [**md2video-audio** #1703](https://github.com/anthropics/skills/pull/1703) | Markdown → MP4 with realistic voiceover (Marp slides + TTS). | "Zero-cost" document-to-video automation. | **OPEN** (Comments N/A) |

---

## 2. Community Demand Trends

Distilled from the most-commented Issues:

- **Trust & security of the Skills supply chain** — [Issue #492](https://github.com/anthropics/skills/issues/492) (46 comments): community skills impersonating the `anthropic/` namespace; demand for provenance/namespace controls and permission guardrails.
- **Org-wide skill sharing & distribution** — [Issue #228](https://github.com/anthropics/skills/issues/228) (17 comments): users want a shared skill library/direct share links instead of manual `.skill` uploads.
- **Reliable skill evaluation / trigger testing** — [Issue #556](https://github.com/anthropics/skills/issues/556) (13 comments) and [Issue #1352](https://github.com/anthropics/skills/issues/1352) (5 comments): `run_eval.py` yields 0% trigger rates and cross-matches UUIDs under parallel workers — strong demand for **test/QA tooling for skills**.
- **Context/memory efficiency** — [Issue #1487](https://github.com/anthropics/skills/issues/1487): `claude-api` eagerly injects ~156k tokens; plus [Issue #1329](https://github.com/anthropics/skills/issues/1329) proposing `compact-memory` (symbolic notation for compact agent state). Demand: **token-efficient skill design and memory compaction**.
- **Agent safety & governance** — [Issue #412](https://github.com/anthropics/skills/issues/412) (closed): governance patterns (policy enforcement, threat detection, audit trails); related [Issue #1385](https://github.com/anthropics/skills/issues/1385): reasoning quality-gate pipeline (calibration → adversarial review → delivery verification).
- **Skill authoring quality** — [Issue #202](https://github.com/anthropics/skills/issues/202) and [Issue #2064](https://github.com/anthropics/skills/issues/2064): descriptions should state *when* to use a skill, not just *what* it does; skill-creator should follow best practice for token efficiency.
- **Plugin hygiene** — [Issue #189](https://github.com/anthropics/skills/issues/189) (9 👍): `document-skills` and `example-skills` install identical content → duplicate skills consuming context.

**Net direction:** demand is shifting from *adding more skills* toward **security, distribution, testing, and context efficiency** of the Skills platform itself.

---

## 3. High-Potential Pending Skills

Active PRs (recently updated, not yet merged) likely to land soon:

| PR | Skill | Why It's Close | Last Updated |
|---|---|---|---|
| [#1977](https://github.com/anthropics/skills/pull/1977) | `algorithmic-art`: fix `wrapAround()` modulo for negatives (Fixes #1897) | Small, targeted bugfix by repeat contributor | 2026-10-18 |
| [#1976](https://github.com/anthropics/skills/pull/1976) | `webapp-testing`: correct textarea/select reporting (Fixes #1891) | Minimal scope, linked issue | 2026-10-18 |
| [#1975](https://github.com/anthropics/skills/pull/1975) | `pptx`: reject `--cols < 1` up front (Fixes #1809) | Trivial validation fix | 2026-10-27 |
| [#2019](https://github.com/anthropics/skills/pull/2019) | `xlsx`: resolve worksheet-scoped defined names in `external_links_at_risk` (Fixes #2013) | Scoped correctness fix | 2026-11-03 |
| [#1681](https://github.com/anthropics/skills/pull/1681) | `skill-creator`: support direct `package_skill.py` execution | Fixes real `ModuleNotFoundError` | 2026-10-24 |
| [#1997](https://github.com/anthropics/skills/pull/1997) | `skill-creator`: correct YAML frontmatter scalar parsing | Data-integrity fix for eval/description optimization | 2026-10-19 |
| [#1730](https://github.com/anthropics/skills/pull/1730) | `claude-api`: replace dead URLs in academy-guide & tool-use-concepts | Verified HTTP 200; docs-only | 2026-10-04 |
| [#1245](https://github.com/anthropics/skills/pull/1245) | `notion-spec-to-implementation` + `quantitative-resume-auditor` | Long-active, substantial new skills | 2026-10-15 |

**Watch-list (larger/riskier):** [#1742](https://github.com/anthropics/skills/pull/1742) (MCP v2 compat), [#1980](https://github.com/anthropics/skills/pull/1980) (security), [#1771](https://github.com/anthropics/skills/pull/1771) (Web3), [#1615](https://github.com/anthropics/skills/pull/1615) (`scnet-hpc`).

---

## 4. Skills Ecosystem Insight

**One-sentence summary:** The community's most concentrated demand has shifted from *authoring new Skills* to **hardening the Skills platform itself — trust/provenance, reliable evaluation, and context efficiency** — as evidenced by the highest-comment issues centering on namespace impersonation (#492), skill-sharing infrastructure (#228), and broken trigger-eval tooling (#556, #1352).

---

*Methodology note: PR comment counts were reported as `undefined` in the source data; rankings for PRs therefore reflect the provided top-20 attention ordering, update recency, and scope impact. Issue rankings use actual comment counts and 👍 reactions.*

---

# Claude Code Community Digest — 2026-11-10

## Today's Highlights

Anthropic shipped **v2.1.321**, introducing **Claude Fable 5.2** as the new default model with a 1M-token context window and significantly cheaper cache reads ($0.20/Mtok), alongside new `--settings` support for remote-control sessions. Meanwhile, the issue tracker remains dominated by **cost-efficiency concerns** — headless `claude -p` usage burning ~1.8× the 5-hour window versus interactive mode — and a **long-simmering multi-account management request** (#18435) that has now accumulated over 1,000 👍 and 237 comments, making it the most-upvoted open request in recent memory.

---

## Releases

**v2.1.321**
- **Claude Fable 5.2** (`claude-fable-5-2`) is now the default Fable model: 1M context, $8/$40 per Mtok, with **$0.20/Mtok cache reads** — a notable cost reduction for cache-heavy workloads.
- Added **`--settings <file>`** flag to `claude remote-control`, applying a settings file to every session it spawns. Importantly, if the settings file changes, **no new sessions pick up the change** (documented behavior worth noting for automation pipelines).

---

## Hot Issues

1. **[#18435](https://github.com/anthropics/claude-code/issues/18435) — Multi-account switching in Claude Desktop** (237 comments, 👍1025)
   The top-voted request by a wide margin. Agents and developers running multiple Claude accounts (work/personal/client) want profile switching without re-authentication churn. The volume of engagement signals this is a genuine workflow blocker, not a nice-to-have.

2. **[#97074](https://github.com/anthropics/claude-code/issues/97074) — Headless `claude -p` uses ~1.8× more of the 5-hour window per token** (73 comments, 👍159)
   A critical finding for anyone building CI/CD or automation on top of Claude Code: the `sdk-cli` entrypoint reportedly consumes nearly double the quota per token versus interactive `cli` for the same workload. High impact for teams running headless pipelines at scale.

3. **[#89673](https://github.com/anthropics/claude-code/issues/89673) — Desktop: Enter inserts newline, Ctrl+Enter submits** (23 comments, 👍95)
   A keybinding ergonomics request reflecting how developers actually compose prompts (multi-line first). Community reaction suggests the current Enter-to-submit default causes premature message sends.

4. **[#87971](https://github.com/anthropics/claude-code/issues/87971) — Claude abuses bash for reads/writes/edits in Auto Mode** (19 comments, 👍99)
   Windows/VS Code users report Claude preferring bash heredocs over native file tools, triggering the auto classifier and wasting turns. Related to the native-Edit-tool requests appearing elsewhere in the tracker.

5. **[#63751](https://github.com/anthropics/claude-code/issues/63751) — AUP/cyber-safeguard false positives contaminate whole session** (18 comments)
   Legitimate own-software hardening work triggers safety classifiers, and one false hit **poisons the remainder of the session**. A serious trust/usability issue for security engineers.

6. **[#65051](https://github.com/anthropics/claude-code/issues/65051) — Background sessions drop text blocks from transcript (regression)** (17 comments)
   A regression from 2.1.160 → 2.1.161 where daemon sessions lose assistant text when responses mix text with `tool_use`. Impacts observability of long-running background jobs.

7. **[#98898](https://github.com/anthropics/claude-code/issues/98898) — Skill frontmatter `model:`/`effort:` ignored via Skill tool** (4 comments)
   Skills declare their model/effort in frontmatter, but when invoked through the Skill tool these are ignored (only respected on `/skill-name`). Subtle but important for anyone standardizing skill behavior.

8. **[#106822](https://github.com/anthropics/claude-code/issues/106822) — MCP missing-command errors misreport `stdio` origin** (3 comments)
   Diagnostics for dynamic and plugin MCP servers misidentify missing executables as `stdio`, while user-scope servers report correctly. A debugging-quality issue for plugin authors.

9. **[#102266](https://github.com/anthropics/claude-code/issues/102266) — `includeCoAuthoredBy: false` doesn't strip `Co-Authored-By`** (3 comments, 👍3)
   Setting attribution config to empty still results in `Co-Authored-By: Claude` being written to commits. Compliance-relevant for teams with strict attribution policies.

10. **[#107433](https://github.com/anthropics/claude-code/issues/107433) — Memory controls for many concurrent sessions** (3 comments)
    A game studio reports 24 sessions consuming ~9.3 GB (~390 MB each) on shared workstations. Requests shared MCP servers and an agent memory budget — a scaling pain point for teams running many parallel sessions.

---

## Key PR Progress

Only one PR updated in the last 24h:

1. **[#89404](https://github.com/anthropics/claude-code/pull/89404) — `validate-agent.sh`: don't abort at first warning, stop false-flagging valid agents**
   Fixes issue #83803. Three root causes around `set -euo pipefail` interactions: `((warning_count++))` / `((error_count++))` return non-zero on first increment, causing the script to abort on the first warning. Directly affects plugin-dev's own agent validation — a self-hosting bug worth watching for plugin authors.

*(Note: only one PR was updated in the reporting window; no additional PRs to summarize.)*

---

## Feature Request Trends

Distilled from the tracker:

- **Account & identity management** — Multi-account switching (Desktop), cross-account session visibility, and profile ergonomics dominate upvotes ([#18435](https://github.com/anthropics/claude-code/issues/18435), [#90188](https://github.com/anthropics/claude-code/issues/90188)).
- **Cost & quota transparency** — Headless-vs-interactive token accounting (#97074), per-subagent context opt-outs (#96405), and session-limit confusion feature heavily.
- **Context injection control** — Requests for per-agent opt-outs of startup context blocks (CLAUDE.md, git status) and correct nested `AGENTS.md` loading ([#98796](https://github.com/anthropics/claude-code/issues/98796), [#96405](https://github.com/anthropics/claude-code/issues/96405)).
- **Tool reliability** — Prefer native Edit over complex bash retries (#104740); stop bash abuse in Auto Mode (#87971).
- **UI/TUI affordances** — Keybinding customization (Enter/Ctrl+Enter), in-response text highlighting for later navigation ([#108674](https://github.com/anthropics/claude-code/issues/108674)), remote permission approval from mobile ([#92145](https://github.com/anthropics/claude-code/issues/92145)).
- **Memory/resource scaling** — Shared MCP servers and agent memory budgets for high-concurrency workstations ([#107433](https://github.com/anthropics/claude-code/issues/107433)).

---

## Developer Pain Points

- **Cost unpredictability in automation.** The 1.8× headless quota overhead (#97074) is the sharpest new pain — it undermines the assumption that programmatic usage and interactive usage are cost-equivalent.
- **Safety classifier false positives & session contamination.** Legitimate security hardening triggers classifiers, and a single hit poisons the session ([#63751](https://github.com/anthropics/claude-code/issues/63751), [#107724](https://github.com/anthropics/claude-code/issues/107724)). This erodes trust in the guardrail system.
- **Bash-over-native-tool drift.** Models repeatedly reach for complex bash rather than native file tools, triggering the auto classifier and wasting turns (#87971, #104740).
- **Silent transcript/observability loss.** Background daemon sessions dropping text blocks (#65051) and one-line thinking summaries replacing inter-tool text (#102807) make debugging agent behavior hard.
- **Attribution config not honored.** `Co-Authored-By` still written despite `includeCoAuthoredBy: false` (#102266) — a compliance friction point.
- **Memory footprint at scale.** ~390 MB/session × dozens of sessions on shared workstations is a real operational constraint for studios/teams (#107433).
- **Stale-issue fatigue.** A large share of the "updated" issues today are `stale`/`duplicate` closures, which may mask still-relevant reports (e.g., agent lying/false completion, #92151/#92150).

---

*Generated from github.com/anthropics/claude-code data for 2026-11-10. Issue/PR numbers and vote counts are as reported in the source snapshot.*

</details>

<details>
<summary><strong>OpenAI Codex</strong> — <a href="https://github.com/openai/codex">openai/codex</a></summary>

# OpenAI Codex Community Digest — 2026-11-10

## 1. Today's Highlights

Release activity continues in the 0.169.0-alpha channel with two new alpha builds, while the PR queue shows a concentrated push on release hardening: hotfix backports of legacy incremental tools for Desktop and multiple auth/rate-limit correctness fixes. On the issue side, the cross-report quota depletion tracker (#41220) remains the highest-engagement thread, and a cluster of Windows desktop regressions (sidebar project loss, sandbox policy blocks, chat resume failures) dominates recent reports.

## 2. Releases

Two alpha releases in the last 24h, both minimal changelogs:

- **rust-v0.169.0-alpha.7** — https://github.com/openai/codex/releases
- **rust-v0.169.0-alpha.6** — https://github.com/openai/codex/releases

No functional details were published in the release notes. Notably, the current stable line referenced by issues is 0.167.0, and PR activity indicates hotfix backports to 0.167.0-alpha.3.2 and -alpha.4 for Desktop, suggesting the 0.169.0 alphas are not yet the Desktop-shipped build.

## 3. Hot Issues

1. **[#41220](https://github.com/openai/codex/issues/41220) — [Meta] Abnormal quota depletion / usage-accounting inconsistencies** (84 comments, 21 👍)
   The community's central tracker for reports of subscription quota and purchased credits draining faster than token evidence predicts. High comment count and cross-referencing across reports make this the de facto coordination thread for billing/usage trust issues — the highest-signal issue in the repo right now.

2. **[#42739](https://github.com/openai/codex/issues/42739) — Windows desktop: local projects disappear from sidebar after update** (84 comments, 9 👍)
   Post-update, the Projects section shows "No projects" while chats remain under Recents and folders exist on disk. Tied with #41220 for engagement; a data-presentation regression that erodes confidence in the desktop app's project model.

3. **[#35420](https://github.com/openai/codex/issues/35420) — Work/Codex stream disconnects on OneDrive-backed Windows workspaces** (39 comments)
   Repeated `stream disconnected before completion` failures when the workspace is OneDrive-backed and OneDrive is degraded. Points at a filesystem-watcher/IO resilience gap for cloud-synced directories — a common Windows enterprise setup.

4. **[#41779](https://github.com/openai/codex/issues/41779) — Windows: local API launch rejected with "blocked by policy"** (19 comments)
   `exec_command` rejects `CreateProcess` before execution with no logs produced. Sandbox policy false positives on legitimate local dev workflows are a recurring theme; the lack of diagnostic output amplifies frustration.

5. **[#59855](https://github.com/openai/codex/issues/59855) — Windows Desktop fails to resume cloud-managed chats (`AbsolutePathBuf` deserialization error)** (19 comments)
   A newer regression (filed 2026-11-06) showing `Invalid request: AbsolutePathBuf deserialized without a base path`. Fast accumulation of comments suggests broad reproducibility on Windows.

6. **[#56682](https://github.com/openai/codex/issues/56682) — VS Code extension fails to initialize UI even in isolated profile** (14 comments, 5 👍)
   Extension version 26.1021.22027 on Windows/Plus. An isolated-profile repro strengthens the case that this is an extension-side initialization bug, not user config contamination.

7. **[#50072](https://github.com/openai/codex/issues/50072) — macOS 0.157+: headless/non-login users fail on `CFPreferencesAppSynchronize`** (12 comments)
   Breaks CLI usage over SSH and headless/CI-style environments on macOS — a meaningful gap for automation and remote-development users.

8. **[#49737](https://github.com/openai/codex/issues/49737) — Android Remote: task messages fail when `thread/items/list` times out** (10 comments, 3 👍)
   Part of a broader Android Remote reliability cluster (see also #58574). Mobile remote control is emerging as a distinct surface with its own failure modes.

9. **[#38638](https://github.com/openai/codex/issues/38638) — Responses WebSocket stalls 300s per retry; opt-out flag removed** (8 comments, 2 👍)
   Documents a 300s stall per retry over `wss://…/responses`, with the opt-out flag removed and HTTPS fallback requiring 5 failed retries. The removal of an escape hatch while reliability is unresolved is the key complaint.

10. **[#60383](https://github.com/openai/codex/issues/60383) — Stdio MCP replies over 16 MiB silently dropped; MCP App requests hang to 300s timeout** (3 comments)
    Filed yesterday but important for MCP ecosystem developers: large tool results fail silently rather than erroring, producing hard-to-diagnose hangs.

## 4. Key PR Progress

1. **[#60457](https://github.com/openai/codex/pull/60457) — [Hotfix] Backport legacy incremental tools to 0.167.0-alpha.4** (OPEN)
   Backports #59832 and #60040 for the Desktop hotfix; adds default-off `incremental_tools_legacy` for regular Responses on native Amazon Bedrock providers, including tool updates, compaction, and resume.

2. **[#60456](https://github.com/openai/codex/pull/60456) — [Hotfix] Backport legacy incremental tools to 0.167.0-alpha.3.2** (OPEN)
   Companion backport letting Desktop opt native Mantle and Bedrock Runtime sessions into incremental tool declarations on ordinary Responses. Together with #60457, this signals active provider-compatibility stabilization.

3. **[#60462](https://github.com/openai/codex/pull/60462) — Route TUI question answers through Cloud message admission** (CLOSED)
   Submits async question answers via Cloud's start-or-steer admission path when a turn is running, resolving intent against session mode for local and remote consistency.

4. **[#60461](https://github.com/openai/codex/pull/60461) — Honor permission grant scopes in executor capability discovery** (CLOSED)
   Approved filesystem access makes executor skills discoverable for the grant lifetime, while turn-scoped access cannot authorize persistent plugin servers/hooks — a meaningful least-privilege tightening.

5. **[#60436](https://github.com/openai/codex/pull/60436) — Revalidate request auth ownership after budget checks** (CLOSED)
   Restarts HTTP request setup if the auth owner changes during async budget checks and validates WebSocket auth — relevant to the account-switching/credential-staleness class of bugs.

6. **[#60435](https://github.com/openai/codex/pull/60435) — Dismiss model switch prompt when account usage recovers** (CLOSED)
   Clears pending rate-limit model-switch prompts when all usage windows fall below 90%. Small but directly addresses UX noise around quota state.

7. **[#60434](https://github.com/openai/codex/pull/60434) — Reset app-server turn summaries when a turn starts** (CLOSED)
   Fixes successful turns being reported as failed due to pre-turn errors accumulating in `TurnSummary` — a correctness fix for turn-status reporting.

8. **[#60423](https://github.com/openai/codex/pull/60423) — Expose deferred MCP servers in resource listings** (CLOSED)
   Adds a sorted `deferredServers` list to aggregate `list_mcp_resources` and `list_mcp_resource_templates` responses, so dormant MCP servers remain queryable.

9. **[#60432](https://github.com/openai/codex/pull/60432) — Upgrade bundled SQLite to 3.54 for WAL fixes** (CLOSED)
   Bumps `libsqlite3-sys` to 0.39.1 and requires SQLite ≥3.54 with a compile-time assertion, addressing `RUSTSEC-2026-0459` while retaining the WAL-reset corruption fix.

10. **[#60414](https://github.com/openai/codex/pull/60414) — Enable model catalog discovery with gateway OAuth** (CLOSED)
    Lets custom providers with gateway OAuth and an explicit catalog discover models without an API key — useful for enterprise gateway deployments.

## 5. Hot Discussions

**Show and tell**

- **[#58359](https://github.com/openai/codex/discussions/58359) — cct: Codex 5-hour and weekly limits on the Windows taskbar** (open source, <1 MB)
  Surfaces `/status` rate-limit percentages in the Windows 10/11 taskbar. Notable as community-built quota visibility — the same concern underlying issue #41220.

- **[#58849](https://github.com/openai/codex/discussions/58849) — code-review-skill: evidence-backed code review with a JSON merge gate**
  Static-first review skill producing auditable findings (location + contract/code path + severity) across four probes in fresh contexts, with a JSON merge gate.

- **[#60185](https://github.com/openai/codex/discussions/60185) — agent-recap: local search across Codex and Claude Code transcripts**
  Browser-based search over Codex and Claude Code JSONL transcripts; no account, install, or uploads.

- **[#60116](https://github.com/openai/codex/discussions/60116) — Pushary: answer Codex approvals from your phone**
  Paid hosted service using native hooks + MCP server to route pending approvals to a phone. Maker-disclosed.

- **[#60441](https://github.com/openai/codex/discussions/60441) — Tapproval: approvals on Apple Watch, marked with risk**
  Open-source local helper handing permission requests from `hooks.json` to an Apple Watch; complements the long-standing watchOS request in #33528.

- **[#60297](https://github.com/openai/codex/discussions/60297) — Better Codex Context (BCC) plugin**
  Aims to save tokens and reduce "stuck" long-running goals by changing context management — directly relevant to the context/compaction pain points raised in #55990.

- **[#60358](https://github.com/openai/codex/discussions/60358) — 10 MIT-licensed habits for running agents more safely**
  Paste-ready prompts/snippets for unattended agent operation (e.g., block tool calls on ambiguous permissions). Complements the safety-check issue cluster.

- **[#58834](https://github.com/openai/codex/discussions/58834) — Friday: run Codex alongside Claude Code and Cursor in separate git worktrees**
  MIT-licensed desktop app with a task board for multi-agent worktree isolation.

- **[#60282](https://github.com/openai/codex/discussions/60282) — Worktree receipt: catch local setup drift when Git status is clean**
  Python/Git receipt for the handoff gap where two worktrees at the same commit have different untracked local setup files.

- **[#60409](https://github.com/openai/codex/discussions/60409) — Q&A: How to safely investigate a blocked Windows inspection?**
  A read-only inspection was blocked for possible telemetry to an unverified destination, with no command/destination/process identified in the warning — a concrete instance of the sandbox-diagnostics gap seen in #41779.

## 6. Feature Request Trends

- **Resource governance and budget awareness**: #55990 proposes native progress-aware budgets, bounded reasoning escalation, and compaction-safe continuation; community tools (#58359) build quota visibility externally. Demand is for first-class token/quota control rather than prompt-level guidance.
- **Approval workflows beyond the desktop**: #33528 (watchOS), #60116 (phone), and #60441 (Watch) all target remote approval/monitoring — users want to supervise agents away from the keyboard.
- **MCP maturity**: deferred-server discovery (#60423), deferred plain-text output (#60390), and the >16 MiB silent-drop bug (#60383) show requests shifting from "support MCP" to "make MCP robust at scale."
- **Context management for long tasks**: #60297 and #55990 both target context bloat and premature task completion (#50482) — persistent demand for better compaction and continuation semantics.
- **Cross-tool interoperability**: transcript search across Codex/Claude Code (#60185) and worktree-based multi-agent coexistence (#58834) reflect multi-agent, multi-vendor workflows becoming normal.

## 7. Developer Pain Points

- **Windows desktop reliability is the dominant theme**: sidebar project loss (#42739), cloud-chat resume failures (#59855), VS Code extension init failure (#56682), Chrome debugger detachment (#55554), idle RAM growth (#53146), and UI regressions (#55231, #39781). Multiple reports are post-update regressions, which amplifies frustration.
- **Sandbox/policy false positives with poor diagnostics**: #41779 ("blocked by policy," no logs) and #60409 (unnamed blocked destination) show blocks that don't identify the triggering command or destination — developers can't distinguish policy from bug.
- **Usage/quota accounting distrust**: #41220 (84 comments) plus billing confusion (#59349: Pro $500 with API credits expiring days after grant) and rate-limit UI staleness (#60435) form a trust cluster around cost.
- **Silent failures over long timeouts**: 16 MiB MCP replies dropped silently with 300s hangs (#60383), 300s WebSocket stalls with the opt-out flag removed (#38638), and endpoint-staleness in long-running sessions (#58060). Developers consistently ask for fast, explicit errors over silent stalls.
- **Headless/automation gaps**: macOS `CFPreferencesAppSynchronize` failures for non-login users (#50072) break SSH/CI usage; the removed WebSocket opt-out (#38638) removes an escape hatch for scripted environments.
- **Disk and resource hygiene**: 263.7 GB of `tmp_pack` garbage on macOS (#54955) with ineffective config controls, plus idle RAM growth on Windows (#53146) — runaway local resource consumption with no live remediation path.

</details>

<details>
<summary><strong>Gemini CLI</strong> — <a href="https://github.com/google-gemini/gemini-cli">google-gemini/gemini-cli</a></summary>

# Gemini CLI Community Digest — 2026-11-10

## Today's Highlights
No new releases shipped in the last 24 hours, but issue and PR activity remains high (50 issues, 44 PRs updated). The dominant theme is **subagent reliability**: multiple P1 bugs report subagents hanging, silently hitting turn limits, or misreporting failures as success. Meanwhile, contributors are pushing hard on tool-safety and context-efficiency fixes—capping `grep_search` output, per-tool-call timeouts, and Git environment hardening.

## Releases
No new releases in the last 24h.

## Hot Issues

1. **[#22323](https://github.com/google-gemini/gemini-cli/issues/22323) — Subagent recovery after MAX_TURNS is reported as GOAL success** (P1, 13 comments)
   The `codebase_investigator` subagent reports `success`/`GOAL` even when it hit the turn limit before doing any analysis. This silently masks failures—arguably the most consequential correctness issue in the tracker right now, and the most-discussed item today.

2. **[#21409](https://github.com/google-gemini/gemini-cli/issues/21409) — Generalist agent hangs** (P1, 8 comments, 8 👍)
   Simple tasks (even folder creation) hang indefinitely once Gemini defers to the generalist agent. Highest community endorsement among today's issues; a workaround exists (disabling subagent deferral), which points squarely at delegation logic.

3. **[#19873](https://github.com/google-gemini/gemini-cli/issues/19873) — Zero-dependency OS sandboxing & post-execution intent routing** (P2, 9 comments)
   A large design proposal to let Gemini 3 lean into its native bash affinity while keeping execution safe. Signals where the team wants agent architecture to go: sandbox-first, intent-checked shell use.

4. **[#21968](https://github.com/google-gemini/gemini-cli/issues/21968) — Gemini does not use skills and sub-agents enough** (P2, 7 comments)
   Anecdotal but widely relatable: the model ignores relevant custom skills/subagents unless explicitly told. This is a core discovery/invocation quality problem for the extension ecosystem.

5. **[#22745](https://github.com/google-gemini/gemini-cli/issues/22745) — Assess AST-aware file reads, search, and mapping** (P2, 7 comments)
   An EPIC investigating AST-aware tooling to cut misaligned reads and token noise. A companion issue ([#22747](https://github.com/google-gemini/gemini-cli/issues/22747)) recommends `ast-grep` as a starting point.

6. **[#22267](https://github.com/google-gemini/gemini-cli/issues/22267) — Browser Agent ignores `settings.json` overrides** (P2, 5 comments)
   The `AgentRegistry` reads merged settings, but the Browser Agent discards them (e.g. `maxTurns`). A clear config-plumbing bug that undermines user trust in per-agent settings.

7. **[#21983](https://github.com/google-gemini/gemini-cli/issues/21983) — Browser subagent fails on Wayland** (P1, 4 comments)
   Linux/Wayland users hit browser-subagent failures reported as `GOAL` termination—another instance of the success-misreporting pattern. Notable platform gap for Linux power users.

8. **[#24246](https://github.com/google-gemini/gemini-cli/issues/24246) — 400 error with >128 tools** (P2, 3 comments)
   Too many registered tools break requests. As MCP and extension adoption grows, tool-scoping/selection will become a scaling bottleneck.

9. **[#22672](https://github.com/google-gemini/gemini-cli/issues/22672) — Agent should stop/discourage destructive behavior** (P2, 3 comments)
   The model occasionally reaches for `git reset` or `--force` when safer options exist. Safety-alignment work with direct blast-radius implications for users.

10. **[#18836](https://github.com/google-gemini/gemini-cli/issues/18836) — Replace WriteToDo with persistent file-based task tracking** (P3, 2 comments)
    Proposes CRUD-style on-disk task tracking to combat context rot and inter-session amnesia. A quietly important architectural direction for long-running agent work.

## Key PR Progress

1. **[#30207](https://github.com/google-gemini/gemini-cli/pull/30207) — fix(build): clear `bundle/` before esbuild** (P0)
   Nightly releases have failed since 2026-11-05 with `413 Request Entity Too Large`; stale build chunks bloat the tarball to 25.2 MB. Release-blocking and the most urgent PR today.

2. **[#30206](https://github.com/google-gemini/gemini-cli/pull/30206) — fix: block project Git environment injection** (P1, security)
   Prevents workspace-supplied `GIT_*` env vars from influencing internal Git/shell operations via `.env` loading. A meaningful supply-chain hardening step.

3. **[#30212](https://github.com/google-gemini/gemini-cli/pull/30212) — feat: configurable per-tool-call timeout (`tools.callTimeout`)**
   Caps wall-clock time per tool call with a recoverable `TOOL_CALL_TIMEOUT` error so the model can adapt. Directly addresses the "hangs forever" class of bugs (#21409).

4. **[#30109](https://github.com/google-gemini/gemini-cli/pull/30109) — fix(core): cap `grep_search` output size**
   Broad greps can currently return ~150K characters; this caps output and nudges the model to narrow searches—an important context-efficiency win.

5. **[#30210](https://github.com/google-gemini/gemini-cli/pull/30210) — fix(core): stop WebFetch fallback work after cancellation**
   Checks for cancellation between fallback URL attempts, avoiding wasted work after aborts. Fixes #30099.

6. **[#30204](https://github.com/google-gemini/gemini-cli/pull/30204) — fix: kill child processes and preserve signal exit code on headless abort**
   Ensures background shells don't survive `gemini -p` termination and cancelled tools are recorded. Solid reliability improvement for CI/headless use.

7. **[#30164](https://github.com/google-gemini/gemini-cli/pull/30164) — fix(cli): resolve folder trust before entering macOS Seatbelt sandbox**
   Fixes folder-trust failures inside Seatbelt after sandbox profiles began denying access to `~/.gemini/trustedFolders.json`. macOS sandbox maturity work.

8. **[#30176](https://github.com/google-gemini/gemini-cli/pull/30176) — fix(cli): preserve selected worktree across auth restarts**
   `--worktree` selection now survives the `RELAUNCH_EXIT_CODE = 199` auth restart flow. Small but high-annoyance fix for worktree users.

9. **[#30214](https://github.com/google-gemini/gemini-cli/pull/30214) — fix(core): recognize TortoiseGitPlink as PuTTY-family SSH client**
   Inserts `-batch` correctly instead of OpenSSH's `-o BatchMode=yes`, fixing Git-over-SSH hardening on Windows+TortoiseGit setups.

10. **[#29978](https://github.com/google-gemini/gemini-cli/pull/29978) — fix(core): keep oversized and boundary lines in `read_background_output`**
    Fixes silent data loss where the first line of the 64KB window was dropped and >64KB single-line logs returned empty output.

## Hot Discussions
No discussion data was provided in this dataset; section omitted.

## Feature Request Trends
- **Smarter, more autonomous delegation** — Make the model reliably discover and invoke skills/subagents (#21968), improve `codebase_investigator` (#18282), and expose subagent trajectories via `/chat share` (#22598).
- **Context efficiency & precision** — AST-aware reads/search/mapping (#22745, #22747), "Tactful Extraction" for surgical reads (#19561), grep output caps (PR #30109), and persistent file-based task tracking (#18836).
- **Sandboxing with native shell fluency** — Zero-dependency OS sandboxing plus post-execution intent routing (#19873).
- **Config & extensibility** — Subagent discovery via `settings.json` (#18285), per-workspace policy overrides (#18397), and per-tool-call timeouts (PR #30212).
- **Self-awareness** — Accurate knowledge of CLI flags/hotkeys so the agent can guide users about itself (#21432).

## Developer Pain Points
- **Subagent hangs and silent failure misreporting** — The single loudest cluster: generalist agent hangs (#21409), `MAX_TURNS` reported as `GOAL` success (#22323), Wayland browser-agent failures (#21983), and crash-on-output-hook (#22186). Users are losing time to sessions that appear "successful" but did nothing.
- **Configuration overrides not honored** — Browser Agent ignores `settings.json` (#22267); symlinked agent files aren't recognized (#20079). Config plumbing lags behind config surfaces.
- **Agent misbehavior under shell restrictions** — Models litter the workspace with temp scripts when shell execution is constrained (#23571) and occasionally choose destructive Git commands (#22672).
- **Context bloat and information loss** — Oversized grep/read outputs bloat context while boundary-line trimming silently drops data (PR #29978); interactive prompts can deadlock agent flows (#22465).
- **Release engineering fragility** — Stale build artifacts broke nightlies for nearly a week (PR #30207), a reminder that packaging pipelines need the same rigor as core code.

</details>

<details>
<summary><strong>GitHub Copilot CLI</strong> — <a href="https://github.com/github/copilot-cli">github/copilot-cli</a></summary>

# GitHub Copilot CLI Community Digest — 2026-11-10

## 1. Today's Highlights

Two releases landed in the last 24 hours: **v1.0.109-0** tightens security around `/share` and `/export` (symlink confirmation), adds session-wide subdomain approvals for URL permissions, and cuts permission prompts for read-only shell scripts; **v1.0.108** adds Windows enumerate-only filesystem grants, `/index` commands with managed shared tgrep indexing enabled by default, and a Grok 4.7/4.6 PDF fallback. The issue tracker remains very active (29 issues updated), dominated by a **1.0.108 MCP namespace regression (already closed)**, a cluster of SDK/session reliability bugs from a single reporter, and continued pressure on sandbox networking/permission behavior on macOS and enterprise tenants.

## 2. Releases

**v1.0.109-0** ([release](https://github.com/github/copilot-cli/releases))
- **Security hardening:** confirmation prompt before `/share` or `/export` saves local files through symlinks.
- **URL permissions:** prompts now allow approving *all subdomains* for the current session.
- **Fewer prompts:** read-only shell scripts require permission less often; scripts that mutate git refs or read files through `hid*` still trigger prompts.

**v1.0.108** (2026-11-09)
- New `sandbox.userPolicy.filesystem.enumeratePaths` in `settings.json` for **Windows enumerate-only directory grants**.
- New **`/index` commands**; managed shared **tgrep indexing enabled by default** for eligible repositories.
- **PDF attachments on Grok 4.7 / Grok 4.6** now fall back to file references.

## 3. Hot Issues

1. **[#5400](https://github.com/github/copilot-cli/issues/5400) [CLOSED] Regression in 1.0.108: deferred MCP function_call loses namespace** — A deferred MCP tool call succeeds, then the next request fails with `400 Missing namespace for function_call`. High impact for SDK-hosted sessions; closed same day, suggesting a fast fix.
2. **[#5185](https://github.com/github/copilot-cli/issues/5185) [OPEN] CRITICAL: copilot-runtime-bazel-cache memory usage vs limits (dotcom)** — Internal statuspage alert on runtime infra; worth watching as an availability signal for the service backing CLI usage.
3. **[#5392](https://github.com/github/copilot-cli/issues/5392) [OPEN] Claude responses report doubled prompt on ~0.3% of calls — usage and billing** — A metering/billing correctness bug with a precise repro rate; likely to get fast attention given cost implications.
4. **[#5390](https://github.com/github/copilot-cli/issues/5390) [OPEN] macOS sandbox: `allowLocalNetwork: true` still generates `hostLoopback: deny`** — Breaks Gradle inside the sandbox (localhost daemon unreachable). Classic sandbox-vs-local-dev-tooling friction.
5. **[#5388](https://github.com/github/copilot-cli/issues/5388) [OPEN] Sandbox credential masking ineffective for `GH_TOKEN`/`GH_ENTERPRISE_TOKEN` on GHE data-residency tenants** — Token env vars dropped *before* masking; enterprise security concern.
6. **[#5402](https://github.com/github/copilot-cli/issues/5402) [OPEN] MCP Entra silent sign-in uses `/common`, fails for single-tenant apps (AADSTS50194)** — Regression since 1.0.107 forcing `/mcp auth` every session; enterprise auth pain.
7. **[#5395](https://github.com/github/copilot-cli/issues/5395) [OPEN] Session stops accepting messages after `session.background_tasks_changed` times out; background agent result lost** — Session-host event delivery timeout (120s) strands sessions; serious reliability issue for background agents.
8. **[#5394](https://github.com/github/copilot-cli/issues/5394) [OPEN] `cancelBackgroundCompaction()` emits no `compaction_complete`** — "Compacting conversation…" UI gets stuck after SDK-cancelled compaction; SDK/extension correctness.
9. **[#5387](https://github.com/github/copilot-cli/issues/5387) [OPEN] [Windows] PowerShell tool session hangs indefinitely when script spawns background process with `Start-Process -RedirectStandardOutput`** — Windows tooling hang; blocks common server-start workflows.
10. **[#5323](https://github.com/github/copilot-cli/issues/5323) [OPEN] Feature: disable agent checkpoint refs (`refs/agents/*`)** — Every agent turn creates commits under the user's git identity; pollutes ref-rendering tools. Community reaction modest (👍1) but the concern is structural.

*Also notable:* [#5320](https://github.com/github/copilot-cli/issues/5320) (CLOSED, 👍11) OSC 7501 agent-status reporting for Herdr — the most-upvoted item in this window.

## 4. Key PR Progress

No pull requests were updated in the last 24h (0 items). Progress this period is visible only through releases and issue closures.

## 5. Hot Discussions

No Discussions data was provided; this section is omitted.

## 6. Feature Request Trends

- **Permissions & sandbox configurability** (epic [#316](https://github.com/github/copilot-cli/issues/316)): finer-grained filesystem grants (Windows enumerate paths in 1.0.108), localhost/loopback allowances ([#5390](https://github.com/github/copilot-cli/issues/5390)), and disabling agent checkpoint refs ([#5323](https://github.com/github/copilot-cli/issues/5323)).
- **MCP/enterprise auth**: fixed OAuth callback ports for pre-registered clients ([#5391](https://github.com/github/copilot-cli/issues/5391)), tenant-correct Entra sign-in ([#5402](https://github.com/github/copilot-cli/issues/5402)), Kerberos/NTLM proxy support ([#5142](https://github.com/github/copilot-cli/issues/5142), closed), and Trusted Access for Cyber linkage ([#4322](https://github.com/github/copilot-cli/issues/4322)).
- **Session/agent observability & control**: native OS notifications for long tasks ([#2616](https://github.com/github/copilot-cli/issues/2616)), OSC 7501 status reporting ([#5320](https://github.com/github/copilot-cli/issues/5320)), remote voice mode via GitHub Mobile ([#5315](https://github.com/github/copilot-cli/issues/5315)).
- **Platform parity**: Git Bash on Windows ([#508](https://github.com/github/copilot-cli/issues/508)), shell expansion consistency (`$HOME` vs `~`, [#144](https://github.com/github/copilot-cli/issues/144), closed).
- **ACP/extension ecosystem**: Copilot Connectors in `--acp` mode ([#5397](https://github.com/github/copilot-cli/issues/5397)), hook `ask` decisions reaching ACP clients ([#5396](https://github.com/github/copilot-cli/issues/5396), closed).
- **Enterprise docs/trust**: verified browser-extension publisher and security-review docs for the browser-use plugin ([#5389](https://github.com/github/copilot-cli/issues/5389)).

## 7. Developer Pain Points

- **Sandbox vs. real dev workflows**: loopback denial breaking Gradle ([#5390](https://github.com/github/copilot-cli/issues/5390)) and ineffective credential masking on GHE tenants ([#5388](https://github.com/github/copilot-cli/issues/5388)) show sandbox policy still surprises users.
- **Session reliability**: multiple independent failure modes — shared-writer I/O failure in `-p` mode ([#5357](https://github.com/github/copilot-cli/issues/5357)), background-task event timeout ([#5395](https://github.com/github/copilot-cli/issues/5395)), un-resumable sessions ([#5386](https://github.com/github/copilot-cli/issues/5386)), and stuck compaction UI ([#5394](https://github.com/github/copilot-cli/issues/5394)).
- **Usage/billing trust**: doubled prompt reporting on ~0.3% of Claude calls ([#5392](https://github.com/github/copilot-cli/issues/5392)) and auto-compaction triggering on a single contradicted reading ([#5393](https://github.com/github/copilot-cli/issues/5393)).
- **Windows rough edges**: PowerShell hangs with `Start-Process` ([#5387](https://github.com/github/copilot-cli/issues/5387)), generic Credential Manager diagnostics ([#5399](https://github.com/github/copilot-cli/issues/5399)), Git Bash absence ([#508](https://github.com/github/copilot-cli/issues/508)).
- **Enterprise auth and proxy friction**: per-session `/mcp auth` re-runs for single-tenant Entra apps ([#5402](https://github.com/github/copilot-cli/issues/5402)) and corporate proxy auth gaps ([#5142](https://github.com/github/copilot-cli/issues/5142)).
- **Agent hygiene in the repo**: unwanted `refs/agents/*` checkpoint commits authored under the user's identity ([#5323](https://github.com/github/copilot-cli/issues/5323)) and subagent prompt contradictions on temp-file usage ([#5352](https://github.com/github/copilot-cli/issues/5352)).

---
*Sources: [github/copilot-cli](https://github.com/github/copilot-cli) releases and issues updated 2026-11-09 → 2026-11-10.*

</details>

<details>
<summary><strong>OpenCode</strong> — <a href="https://github.com/anomalyco/opencode">anomalyco/opencode</a></summary>

# OpenCode Community Digest — 2026-11-09

## 1. Today's Highlights

No new releases shipped in the last 24 hours, but the issue tracker remains highly active with 50 issues and 50 PRs updated. The free-tier quota confusion continues to dominate community attention (Issue #59387 alone has 339 comments), while the v2 service architecture is surfacing a cluster of infrastructure bugs around port collisions, config reloads, and MCP process spawning. On the PR side, maintainer `jlongster` landed a three-part stacked series reworking the PTY protocol and shell command execution in the process daemon.

---

## 2. Releases

*No new releases in the last 24 hours.*

---

## 3. Hot Issues

1. **[#59387](https://github.com/anomalyco/opencode/issues/59387) — Free tier issues mega-thread (339 comments, 👍27)**  
   The canonical clearinghouse for free-tier quota complaints ("Free usage exceeded", reset timers that never fire). The sheer volume signals a systemic billing/quota-state problem still unresolved after two weeks.

2. **[#43250](https://github.com/anomalyco/opencode/issues/43250) — `keep.tokens` not honoured; compaction walk-back unbounded**  
   Reproduced compaction bug where agent sessions carry ~234K tokens against a 15K setting — 5–15× over budget. Directly impacts cost and context quality for long-running agent workflows.

3. **[#50814](https://github.com/anomalyco/opencode/issues/50814) — Model emits tool calls as literal text after compaction**  
   Post-compaction, tool calls degrade into plain text and never dispatch, silently breaking sessions. A serious correctness failure in the compaction pipeline.

4. **[#51570](https://github.com/anomalyco/opencode/issues/51570) — LM Studio model discovery fails with API keys (👍4)**  
   `/models` requests omit the configured API key, breaking local-model discovery even though inference works. High-impact for the local-LLM segment.

5. **[#59861](https://github.com/anomalyco/opencode/issues/59861) — Restore catalog-price estimates when providers report $0**  
   Since v2.2.0, Go subscription sessions show no cost, and the "$X spent" indicator disappeared. Community wants an optional estimate toggle.

6. **[#43845](https://github.com/anomalyco/opencode/issues/43845) — v2 service spawns MCP server pair per project directory**  
   With ~53 cataloged projects, startup spawns dozens of stdio MCP processes instead of one set. A resource-exhaustion regression in the new service model.

7. **[#61695](https://github.com/anomalyco/opencode/issues/61695) — Host-side restarts surface to model as "user doesn't want to proceed"**  
   Claude Agent sessions misreport infrastructure restarts as user cancellations, corrupting agent reasoning. Reproduced and provider-specific.

8. **[#62872](https://github.com/anomalyco/opencode/issues/62872) — Partial config write kills all sessions (no last-good fallback)**  
   A transient partial write to `opencode.jsonc` rejects the entire config with no fallback, killing running sessions. A strong argument for atomic config reloads; a fix PR is already open.

9. **[#62948](https://github.com/anomalyco/opencode/issues/62948) — Git polling loop saturates CPU on large worktrees**  
   Tight-loop `git status` scans on a 1.3M-file worktree peg the CPU. Relevant to anyone running OpenCode inside a `$HOME`-rooted repo.

10. **[#62923](https://github.com/anomalyco/opencode/issues/62923) — Code Mode `execute` returns `null` for everything**  
    The Code Mode execute tool is effectively broken in v2.2.6 — even thrown errors return null. Blocks the Code Mode feature entirely.

*Honorable mentions:* [#62934](https://github.com/anomalyco/opencode/issues/62934) (Android 17 missing `ACCESS_LOCAL_NETWORK`), [#62981](https://github.com/anomalyco/opencode/issues/62981) (fixed-width variant flyout UI), [#62641](https://github.com/anomalyco/opencode/issues/62641) (step-limit assistant-prompt rejected by no-prefill models).

---

## 4. Key PR Progress

1. **[#62976](https://github.com/anomalyco/opencode/pull/62976) — opencode-pty protocol v8** (jlongster)  
   First of three stacked PRs replacing #61876; updates the PTY protocol foundation.

2. **[#62978](https://github.com/anomalyco/opencode/pull/62978) — Run shell commands in the process daemon across restarts** (jlongster)  
   Third stacked PR: shell commands survive restarts by living in the daemon — a durability win for long sessions.

3. **[#62974](https://github.com/anomalyco/opencode/pull/62974) — Keep last-good config on partial write** (iMumd)  
   Fixes #62872 by retaining the last valid config during non-atomic writes — prevents mass session death.

4. **[#62904](https://github.com/anomalyco/opencode/pull/62904) — Keep managed service ports out of ephemeral range** (2SinceJuly)  
   Fixes #62891's port-49374 collision by extending the Windows-only workaround to Linux/macOS.

5. **[#62984](https://github.com/anomalyco/opencode/pull/62984) — Rank Code Mode tool search with name-weighted BM25** (rekram1-node)  
   Replaces substring scoring with BM25, down-weighting noise words like `list`/`get` — meaningful relevance improvement.

6. **[#62982](https://github.com/anomalyco/opencode/pull/62982) — Update MCP SDK to 2.4.0** (rekram1-node)  
   Brings the Skills extension (SEP-2640) and rebuilds the client patch; groundwork for future skills support.

7. **[#62963](https://github.com/anomalyco/opencode/pull/62963) — Keep browser tools across screen lock** (jlongster)  
   Auto-detaches/re-attaches browser tools on lock/unlock instead of requiring an always-on setting.

8. **[#62983](https://github.com/anomalyco/opencode/pull/62983) — Deliver keyed instruction updates to Claude** (iMumd)  
   Fixes #62916: system messages in history now reach the `claude-agent` provider.

9. **[#62979](https://github.com/anomalyco/opencode/pull/62979) — Send EVREN thinking controls as booleans** (cemililik)  
   Provider-compat fix for EVREN's OpenAI-compatible gateway.

10. **[#60055](https://github.com/anomalyco/opencode/pull/60055) — Report unverified clipboard delivery in TUI** (Chubercik)  
    Stops falsely claiming "Copied to clipboard"; closes #59664. Small but high-trust UX fix.

*Site perf cleanup:* a batch of `adamdotdevin` PRs ([#62971](https://github.com/anomalyco/opencode/pull/62971), [#62965](https://github.com/anomalyco/opencode/pull/62965), [#62966](https://github.com/anomalyco/opencode/pull/62966), [#62962](https://github.com/anomalyco/opencode/pull/62962), [#62964](https://github.com/anomalyco/opencode/pull/62964), [#62980](https://github.com/anomalyco/opencode/pull/62980)) cut idle polling, deferred nav/console loading, and fixed media caching.

---

## 5. Hot Discussions

*No Discussion data was provided for this period.*

---

## 6. Feature Request Trends

- **Payments & account management:** crypto payment for OpenCode Go (#23153, closed, 👍67 — the highest-voted item in this window), email change/removal in Zen (#18654), and localization of the console page (#62768).
- **Session UX:** pinning sessions in the experimental Inbox (#62772) and OSC 7501 reporting for `done`/`error` states (#60411) — extending the existing terminal notification protocol.
- **Cross-platform robustness:** Windows PowerShell should normalize en/em dashes and curly quotes rather than reject commands (#61704).
- **Cost transparency:** opt-in catalog-price estimates when providers report $0 (#59861).
- **Fleet management:** `opencode join` for hub-managed multi-host node fleets with centralized session discovery (#44760, closed).
- **Agent transparency:** failed MCP servers should be distinguishable from unconfigured ones (#62973).

---

## 7. Developer Pain Points

1. **Free-tier quota state is unreliable.** The 339-comment mega-thread (#59387) and duplicate reports (#58687, #62783) show reset timers that don't fire and limits that persist across model switches — the single loudest complaint.
2. **Compaction is the weakest link.** Two separate reproduced bugs (#43250 budget overrun, #50814 tool-call-as-text) make long agent sessions unpredictable in both cost and correctness.
3. **v2 service lifecycle regressions.** Port collisions (#62891), per-project MCP process storms (#43845), CPU-saturating git polling (#62948), and no last-good config fallback (#62872) all point to hardening needs in the new daemon architecture.
4. **Provider compatibility friction.** LM Studio API-key discovery (#51570), Claude Agent restart misinterpretation (#61695), no-prefill models rejecting step-limit prompts (#62641), and EVREN boolean controls (#62979) show the multi-provider matrix needs more conformance testing.
5. **Local/desktop setup issues recur.** "No models available" on Desktop 2.2.6 (#62781), missing custom provider option (#59603), and Android 17 LAN permission (#62934) suggest onboarding gaps across clients.

---

*Digest generated 2026-11-10 from github.com/anomalyco/opencode. All links point to the canonical issue/PR pages.*

</details>

<details>
<summary><strong>Pi</strong> — <a href="https://github.com/earendil-works/pi">earendil-works/pi</a></summary>

# Pi Community Digest — 2026-11-10

## 1. Today's Highlights

A heavy triage day: the Windows sink-thread (#7547) exploded to 95 comments, surfacing a cluster of Windows/PowerShell/stderr bugs that are being systematically fixed by `petrroll` in a batch of PRs. On the model-integration side, regressions in `StringEnum` typing (P0) and a Claude model header incompatibility are the highest-severity items. No new releases in the last 24h.

---

## 2. Releases

None in the last 24h. (Note: v1.5.0 shipped earlier and is the source of several P0 issues below — see #11869, #11892, #11769.)

---

## 3. Hot Issues

1. **#7547 — [Windows] How do you use Pi on Windows?** (95 comments, OPEN)
   The flagship Windows omnibus thread. Community is enumerating the many ways Pi is run on Windows (native, Git Bash, WSL, MSYS2/mintty) and where effort should go: core fixes vs. delegating to extensions. Highest-engagement issue of the day.
   https://github.com/earendil-works/pi/issues/7547

2. **#11869 — [bug, prio:p0] `StringEnum` in Pi 1.5.0 breaks compile-time typing** (OPEN, 👍3)
   A 1.5.0 regression: `StringEnum` no longer derives from `typebox`, so tool-parameter fields degrade to `unknown`. Breaks downstream TypeScript tool authors — hence the P0 badge and upvotes.
   https://github.com/earendil-works/pi/issues/11869

3. **#11773 — [inprogress, prio:p0] Upgrade Token Plan branding and gateway setup** (OPEN)
   Rebranding Token Plan to 千问 AI 平台 / QwenCloud with new default endpoints. P0 because it affects default configuration for new users.
   https://github.com/earendil-works/pi/issues/11773

4. **#3200 — Support video/audio content in prompt command** (OPEN, 👍8)
   Highest-upvoted open request: extend the `prompt` RPC to forward audio/video alongside images for multimodal models. Clear demand signal for multimodal RPC.
   https://github.com/earendil-works/pi/issues/3200

5. **#5103 — [bug] pi-windows-x64.zip can't detect git-bash from PATH** (CLOSED, 24 comments)
   Long-running Windows bug now resolved. Illustrative of the Windows environment-detection pain cluster.
   https://github.com/earendil-works/pi/issues/5103

6. **#9262 — find tool: glob with Windows separators silently returns nothing** (CLOSED)
   Silent-failure class bug: `src\**\*.ts` returns zero results with no error, leading agents to wrongly conclude files don't exist. Now closed.
   https://github.com/earendil-works/pi/issues/9262

7. **#11892 — [bug] pi-durable 1.5.0: tasks stuck in waiting/completing** (CLOSED)
   A 1.5.0 regression in pi-durable where tasks never leave `waiting`/`completing` even after dependencies finish. Reproduced with `MemoryStorage`. Closed.
   https://github.com/earendil-works/pi/issues/11892

8. **#11969 — [bug, untriaged] Fable 5.2 requires new headers** (CLOSED, 👍1)
   Anthropic rejects requests to Fable 5.2 with `claude_code_version_too_old` (400). Fixed quickly — see PR #11970.
   https://github.com/earendil-works/pi/issues/11969

9. **#11926 — [bug, no-action] Transient states treated as permanent errors** (CLOSED)
   A 429 is shown in red and permanently logged as a hard error rather than a transient state. Important UX distinction for session logs.
   https://github.com/earendil-works/pi/issues/11926

10. **#11676 — [inprogress] pi-durable: `Harness.close()` leaks provider session resources** (OPEN)
    After a real Codex response, a Node process retains a WebSocket + 5-min idle timer, preventing clean exit. Deterministic repro. Fix in PR #11961.
    https://github.com/earendil-works/pi/issues/11676

---

## 4. Key PR Progress

1. **#11970 — fix(ai): bump Claude Code user-agent for Fable 5.2** (CLOSED)
   Bumps advertised Claude Code version 2.1.280 → 2.1.321 to clear the `claude_code_version_too_old` 400. Direct fix for #11969.
   https://github.com/earendil-works/pi/pull/11970

2. **#11964 — fix(coding-agent): native exit codes in powershell on Windows PS 5.1** (CLOSED)
   Solves #8842: PS 5.1 misclassifies stderr progress output as failures. Uses native exit codes for correctness.
   https://github.com/earendil-works/pi/pull/11964

3. **#11967 — fix(coding-agent): read/edit/write UTF-16 files with BOM** (OPEN)
   Handles UTF-16LE/BE BOM files (common from PS 5 `Out-File`, `reg export`). Big correctness win for Windows users; read/write/tools currently emit U+FFFD.
   https://github.com/earendil-works/pi/pull/11967

4. **#11966 — fix(coding-agent): stop writing to terminal while TUI draws** (OPEN)
   Fixes #11264: rogue stdout/stderr writes corrupt TUI screen state. Important stability fix for interactive mode.
   https://github.com/earendil-works/pi/pull/11966

5. **#11968 — fix(tui,coding-agent): fullscreen scroll frames O(viewport)** (OPEN)
   Perf fix: fullscreen scrolling rebuilt the whole transcript every frame (~60 fps). Reduces to O(viewport) rendering.
   https://github.com/earendil-works/pi/pull/11968

6. **#11961 — fix(durable): clean up provider resources on harness close** (OPEN)
   Releases pi-ai provider resources after active tasks complete so processes exit cleanly. Addresses #11676.
   https://github.com/earendil-works/pi/pull/11961

7. **#11952 — fix(coding-agent): run bash alone, other calls in parallel** (CLOSED)
   Serializes `bash` (executionMode: sequential) so paired `read` calls don't read half-written output files. Fixes a real agent race condition (#11950).
   https://github.com/earendil-works/pi/pull/11952

8. **#10969 — fix(coding-agent): reuse unchanged codemode highlighting** (CLOSED)
   Caches per-row syntax highlighting; avoids re-highlighting the whole script on every result-progress/timer tick. Performance win for codemode.
   https://github.com/earendil-works/pi/pull/10969

9. **#11972 — fix(coding-agent): match Windows find path patterns across all fd versions** (CLOSED)
   Makes `find` path-pattern matching consistent regardless of installed `fd` version (pre/post 10.5.1 glob semantics differ).
   https://github.com/earendil-works/pi/pull/11972

10. **#11957 — fix(coding-agent): report already-declared MCP tools in tool_search** (OPEN)
    `tool_search` no longer answers "No matching tools found" for tools the model already has (e.g. `direct` MCP tools). Reduces weaker-model confusion.
    https://github.com/earendil-works/pi/pull/11957

---

## 5. Hot Discussions

No dedicated discussion data was provided in this dataset. (Most community discussion is occurring in the comments of issue #7547; see Hot Issues above.)

---

## 6. Feature Request Trends

- **Multimodal & media**: Video/audio support in the `prompt` RPC (#3200, highest upvotes); image-heavy tool results via file references for Codex (#8617); aggregate image budgets for 413 recovery (#8351).
- **Session/storage management**: Permanent conversation deletion in pi-durable (#11026); persistence of chosen folder for moved sessions (#11060); read-only viewer semantics correcting for JSONL recovery writes (#11939).
- **Tool exposure control**: Per-tool MCP enable/disable from the `/mcp` Tools view without hand-editing `toolExposure` (#11526); actionable `edit` failure diagnostics distinguishing "file changed" from "oldText mismatch" (#11943).
- **TUI/UX polish**: Relative Markdown link OSC 8 target fixes (#10993), mintty modifier-key handling (#11935), WSL image paste via empty bracketed paste (#11259).

**Trend summary:** Windows ecosystem parity dominates, followed by multimodal input handling and stronger agent-facing tool-result diagnostics.

---

## 7. Developer Pain Points

- **Windows is a first-class pain surface.** Recurring, high-volume themes: PATH/git-bash detection (#5103), path-separator globs silently failing (#9262, #11972), PowerShell 5.1 stderr→failure misclassification (#8842, #11964), installer aborting on `npm warn` stderr (#11944), UTF-16 BOM files (#11967), and terminal-key handling in mintty (#11935). The #7547 thread (95 comments) is a direct community call to consolidate Windows support investment.
- **1.5.0 regressions erode trust.** `StringEnum` typing break (#11869, P0), pi-durable task-stuck states (#11892), and the un-bumped stable branch for Nix users (#11769) all trace to the 1.5.0 release window — suggesting release/QA hygiene needs attention.
- **Silent failures are the most damaging bug class.** `find` returning zero results without error (#9262) and `tool_search` claiming no matches for real tools (#11921) actively mislead agents; developers want loud, actionable errors.
- **Resource lifecycle leaks.** `Harness.close()` leaving WebSockets and idle timers alive (#11676) blocks clean process exit — a recurring class for long-running/embedded use.
- **Missing diagnostics increase debugging time.** The `edit` tool's single generic failure message (#11943) and transient errors being logged as permanent (#11926) are frequently cited friction points.

---

*Data source: github.com/earendil-works/pi — generated 2026-11-10.*

</details>

<details>
<summary><strong>Qwen Code</strong> — <a href="https://github.com/QwenLM/qwen-code">QwenLM/qwen-code</a></summary>

# Qwen Code Community Digest — 2026-11-10

## Today's Highlights

Qwen Code shipped **v0.27.1** alongside coordinated SDK releases, and the community is heavily focused on the **Managed Agent / Web Shell** expansion (channels, webhook delivery, session management). Meanwhile, CI stability remains the single largest source of issue noise — multiple `Managed Agent gate`, `E2E Tests`, and `Qwen Code CI` failures are actively being triaged, including a main-branch Java compilation break caused by duplicate case labels.

---

## Releases

**v0.27.1** ([release](https://github.com/QwenLM/qwen-code/releases))
- `feat(core)`: Active shell is now named in the startup context ([#16491](https://github.com/QwenLM/qwen-code/pull/16491))
- No known breaking changes.

**Companion releases bundled with this version:**
- **sdk-typescript v0.1.31** — bundles CLI 0.27.1
- **sdk-typescript v0.1.29** — backfilled release (npm publish succeeded, but the release-branch push step failed; see [#16490](https://github.com/QwenLM/qwen-code/pull/16490))
- **desktop v0.27.1** — includes provider docs for Wally under OpenAI-compatible providers ([#16529](https://github.com/QwenLM/qwen-code/pull/16529))

---

## Hot Issues

1. **[#14724](https://github.com/QwenLM/qwen-code/issues/14724)** — *Main CI failed: Managed Agent gate — no test identified* (282 comments). The longest-running CI tracking issue; a persistent gate failure that cannot be attributed to a specific test. High noise, still unresolved.

2. **[#16712](https://github.com/QwenLM/qwen-code/issues/16712)** — *main's Java lane fails to compile — duplicate case labels in `SurfaceAdmissionAcceptanceTest`* (P1, closed). Broke the Java lane for **every** PR via test-compilation failure. Fast turnaround (closed same day) shows release-blocking responsiveness.

3. **[#16683](https://github.com/QwenLM/qwen-code/issues/16683)** — *Qwen Code Desktop 0.27 fails on Intel x64 Mac* (P1). Fatal V8 `Check failed: 12 == (*__error())` at runtime startup. A serious platform regression for a segment of macOS users.

4. **[#16140](https://github.com/QwenLM/qwen-code/issues/16140)** — *Shell redirect extractor drops `N>` (fd≥3), `>\|`, and no-space `cd>file` forms* (P1, security). Deny rules for `Write`/`Edit` cannot fire on these redirect spellings — a permission-bypass class bug.

5. **[#13139](https://github.com/QwenLM/qwen-code/issues/13139)** — *Shell permission analyzer: residual phantom-cwd entrances for quoting-artifact `cd` targets* (P1, security). Follow-up to an earlier cwd-escalation fix; three entrances of the same family remain open.

6. **[#16757](https://github.com/QwenLM/qwen-code/issues/16757)** — *Non-UTF-8 ANSI-C bytes collapse to U+FFFD, so shell path guards miss the file bash touches* (P2, security). Encoding mismatch between the guard's decoder and bash's raw-byte filesystem semantics.

7. **[#15327](https://github.com/QwenLM/qwen-code/issues/15327)** — *tracking(core): run every surface on one agent loop behind `experimental.unifiedAgentLoop`* (P1). The architectural umbrella issue consolidating the agent loop across CLI, OpenTUI, ACP, and daemon surfaces — directly driving several PRs below.

8. **[#16690](https://github.com/QwenLM/qwen-code/issues/16690)** — *GitHub channel: surface API budget and protect the mention lane from watch spend* (P2, closed). Budget-aware GitHub channel behavior; closed quickly, signaling active channel hardening.

9. **[#16546](https://github.com/QwenLM/qwen-code/issues/16546)** — *feat(managed-agent): expose sidebar panel capabilities in WebShell* (P2). Capability-parity matrix comparing control-plane, WebShell, and data-agent REST surfaces — a roadmap artifact for managed sessions.

10. **[#16766](https://github.com/QwenLM/qwen-code/issues/16766)** — *Generated UI card stuck at "Loading the interactive view…" in Desktop (macOS WKWebView); renders in Chromium* (P2). Tauri/WKWebView-specific rendering gap in the experimental Generated UI tool.

---

## Key PR Progress

1. **[#16743](https://github.com/QwenLM/qwen-code/pull/16743)** — *feat(cli): send OpenTUI's model rounds through the shared agent loop.* PR 4a of #15327; brings OpenTUI to parity with Ink on the unified loop and extends the TUI parity suite.

2. **[#16324](https://github.com/QwenLM/qwen-code/pull/16324)** — *feat(web-shell): let managed sessions reuse the host shell.* Removes the second "managed look" chrome; managed pages inherit host header/workspace/phase badge when `showManagedSessionHeader: false`.

3. **[#16736](https://github.com/QwenLM/qwen-code/pull/16736)** — *feat(web-shell): configure the WeChat channel with in-browser QR login.* WeChat joins the channel platform list with QR rendering, scan-state reporting, and account panel.

4. **[#16724](https://github.com/QwenLM/qwen-code/pull/16724)** — *feat(channels): QR-login management contract + QQ config surface.* Adds an optional interactive QR-login capability to the channel management contract, with daemon-persistable values.

5. **[#16758](https://github.com/QwenLM/qwen-code/pull/16758)** — *feat(managed-agent): private BTW and session recap.* Durable, actor-private follow-ups and manual/auto session recap, including ACTIVE sessions whose Runtime was reclaimed.

6. **[#16023](https://github.com/QwenLM/qwen-code/pull/16023)** — *feat(managed-agent): share resident external Workspace across sessions.* Turns Workspace closing into a membership release rather than teardown — a meaningful resource-efficiency change.

7. **[#16498](https://github.com/QwenLM/qwen-code/pull/16498)** — *feat(managed-agent): Tool v4 execution measurements.* Attested, bounded numeric measurements frozen with results and persisted pre-ack; feeds diagnostics panels.

8. **[#15936](https://github.com/QwenLM/qwen-code/pull/15936)** — *fix(core): reduce or refuse a compression request that cannot fit its window.* Enforces a 4,096-token minimum summary reservation and recovers once on context-length rejection.

9. **[#16598](https://github.com/QwenLM/qwen-code/pull/16598)** — *fix(core): keep persisted-output stubs out of microcompaction clearing.* Stops replacing spill stubs (path, sha256, preview) with the bare cleared placeholder.

10. **[#14360](https://github.com/QwenLM/qwen-code/pull/14360)** — *fix(core): treat `git remote show` as non-read-only.* Closes an auto-mode egress bypass where a network-touching command was auto-approved on the read-only fast path.

---

## Hot Discussions

No GitHub Discussions data was provided in this dataset; section omitted.

---

## Feature Request Trends

- **Channel & webhook ecosystem expansion** — A clear cluster of requests (#16751, #16752, #16753, #16724, #16736) asks for outbound platform webhooks (DingTalk first), configurable delivery targets, per-platform connection methods, and Web Shell configuration flows. WeChat and QQ are the leading platform integrations.
- **Unified agent loop across surfaces** — #15327 and its PR chain (#16743) represent the dominant architectural direction: one `packages/core` loop serving CLI, TUI, ACP, daemon, and Web Shell.
- **Managed Agent session lifecycle** — Requests for sidebar capability parity (#16546), resident workspace reuse (#16023), private BTW/recap (#16758), and session reuse of host chrome (#16324) point toward maturing multi-session management.
- **API budget / poll efficiency for integrations** — #16690, #16691 (conditional requests / ETags), and #16692 (batched GraphQL PR watches) show demand for rate-limit-aware, low-cost polling in the GitHub channel.

---

## Developer Pain Points

- **CI instability dominates the issue tracker.** `Managed Agent gate`, `E2E Tests`, and `Qwen Code CI` failures generate the highest-comment threads (#14724, #16672, #11680, #11678), often with "no test identified" — making regressions hard to attribute and slowing every PR.
- **Main-branch breakage has real blast radius.** #16712 (duplicate case labels) blocked the Java lane for all PRs until fixed — repeated main-branch breakage erodes contributor confidence.
- **Shell permission analyzer edge cases recur.** Multiple P1 security issues (#16140, #13139, #16757) target redirect parsing, quoting artifacts, and encoding mismatches — a sustained correctness burden in the permission-guard subsystem.
- **Platform-specific regressions** — macOS Intel x64 startup crash (#16683) and WKWebView rendering failures (#16766) suggest Desktop packaging/webview coverage gaps.
- **Session-state and telemetry fidelity** — Spurious "request was interrupted" banners on clean tool-call turn endings (#16767) and TPS excluding time-to-first-token (#16714) reflect user-visible trust issues in state reporting and metrics.

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar).*