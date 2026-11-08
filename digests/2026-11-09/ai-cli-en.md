# AI CLI Tools Community Digest 2026-11-09

> Generated: 2026-11-08 23:13 UTC | Tools covered: 7

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

# Cross-Tool Comparison Report: AI CLI Developer Tools Ecosystem
**Date: 2026-11-09**

---

## 1. Ecosystem Overview

The AI CLI tooling landscape in late 2026 has converged on a common architecture — agentic loops, MCP-based tool extensibility, subagent delegation, and context compaction — and is now entering a phase where differentiation happens less on core capability and more on reliability, multi-identity ergonomics, and enterprise readiness. Notably, **no new stable releases landed across any of the seven tools** in this window, while issue volume remained heavy everywhere. The dominant cross-cutting failure modes are strikingly consistent: MCP lifecycle bugs (stale tool lists, reconnect failures, dropped auth), context-compaction data loss, multi-account/multi-workspace friction, and auto-update regressions breaking previously working workflows. Security hardening and CI supply-chain work feature prominently in PR queues (Gemini CLI, Qwen Code), signaling that projects are maturing from "does it work" to "is it safe and reproducible."

---

## 2. Activity Comparison

| Tool | Issues (updated 24h) | PRs (updated 24h) | Discussions | Release Status |
|---|---|---|---|---|
| **Claude Code** | ~20+ (heavy; ~20 duplicate Fable 5.1 reports from one author) | 0 | N/A (not provided) | No release |
| **OpenAI Codex** | ~13+ notable (Windows regression cluster) | 10 (mostly bot-driven polish, CLOSED) | 8 (3 Ideas, 5 Show & Tell) | No release |
| **Gemini CLI** | 10 highlighted (agent subsystem heavy) | 10 (security-led; 2 closed features) | N/A (not provided) | No release |
| **GitHub Copilot CLI** | 14 updated (incl. 1 CLOSED) | 0 | N/A (not provided) | No release |
| **OpenCode** | 10 highlighted (#59387 at 333 comments) | 10 (feature + fixes, several stale-closed) | N/A (not provided) | No release |
| **Pi** | 10 triaged cluster (native MCP 1.5.0 regressions) | 4 (all CLOSED; maintenance-heavy) | 2 (Show & Tell) | No release |
| **Qwen Code** | 10 highlighted + CI bot noise (#14724 at 277 comments) | 10 (managed-agent features + fixes) | N/A (not provided) | **3 releases**: nightly, TS SDK 0.1.30, Desktop v0.27.0 |

**Notes:**
- Issue counts are drawn from each digest's "Hot Issues" section and are indicative, not exhaustive totals.
- **Claude Code** shows the highest raw issue noise due to a single-author duplicate burst (~20 near-identical Fable 5.1 truncation reports) — community signal is inflated, not genuinely broader.
- **Qwen Code** is the only tool with release activity, shipping a nightly plus SDK and desktop builds.
- **Pi** had a notably maintenance-focused day (all 4 PRs closed), while **OpenCode** shows the most feature-oriented PR queue among the smaller projects.
- Discussions are only captured for Codex and Pi; other digests did not include discussion data (marked N/A, not inactive).

---

## 3. Shared Feature Directions

Several requests recur independently across communities — the strongest signal of genuine market demand.

### Multi-account / multi-identity support
- **Claude Code** #27302 (453 👍, 298 comments — top issue): same connector under different accounts.
- **OpenAI Codex** #20500 (118 👍): per-connector named accounts with privacy boundaries; #51521 workspace switching without sign-out.
- **Qwen Code / Copilot CLI**: implicit in project/workspace reconciliation bugs.
> **Converging need:** account-scoped auth and connector binding is the single most consistent cross-community ask.

### MCP lifecycle robustness
- **Claude Code**: stale `tools/list` (#100115, #88172), reconnect-at-session-start failures (#108451).
- **Pi**: dropped OAuth headers (#11918), sequential reconnect stalls (#11919), `tool_search` visibility (#11921).
- **Qwen Code**: MCP tools stuck DISCONNECTED after aborted turn (#12160 / PR #16661).
- **OpenCode**: stale `GET /provider` after credential delete (#62761).
- **Gemini CLI**: MCP OAuth TOCTOU gap (PR #30071); >128-tool ceiling (#24246).
> **Converging need:** deterministic connector refresh/reconnect and graceful degradation are the most concentrated area of friction across the entire ecosystem.

### Context / token economics
- **OpenAI Codex**: compaction wipes history (#60293), stale-request resumption (#54646), token drain (#56866), context-reduction plugins (#60297).
- **Gemini CLI**: ~36.6k-token baseline, AST-aware tooling EPIC (#22745), tactful extraction (#19561).
- **Pi**: context-usage overestimation after retry errors (#10287), prompt-cache warming PR (~26% spend reduction).
- **Qwen Code**: pre-flight oversized-write rejection (#16417), slice references (#16173).
- **OpenCode**: compaction aborting turns on local models (#62778), prompt-cache rules (#53927).
> **Converging need:** accurate token accounting, safe compaction, and cache control are universal cost/quality pain.

### Subagent reliability & orchestration
- **Gemini CLI**: turn-limit reported as success (#22323), generalist hangs (#21409), ignored `maxTurns` (#22267).
- **OpenCode**: premature "completed" (#48826), permission-ordering bugs (#62213).
- **Qwen Code**: subagent power oversubscription (#16526), IDE task-list parity (#8544).
- **OpenAI Codex**: compaction resuming stale delegated requests (#54646), agent-tree IDs (PR #60190).
> **Converging need:** subagents are the new frontier and the new maturity gap — observability, correct termination, and concurrency control.

### Configurable defaults & config ergonomics
- **Copilot CLI**: default agent (#5383).
- **Claude Code**: configurable `@`-import extensions (#108470).
- **OpenCode**: split config files (#19353), symlink reload (PR #53932).
- **Pi**: `/clear` preserving model/level (#11925), conditional `AGENTS.md` files (#11933).

### Terminal/editor ecosystem integration
- **Copilot CLI** #5320 (👍11): OSC 7501 status reporting.
- **Gemini CLI** PR #29918: same OSC 7501 request (closed, needs resubmission).
- **Pi** #11934: mintty key decoding for fullscreen search.
> **Note:** OSC 7501 appearing independently in two communities is a strong signal of a standardized terminal-integration desire.

---

## 4. Differentiation Analysis

| Dimension | Claude Code | OpenAI Codex | Gemini CLI | Copilot CLI | OpenCode | Pi | Qwen Code |
|---|---|---|---|---|---|---|---|
| **Primary focus** | Model behavior + auth/connector depth | Multi-surface desktop/mobile parity | Agent runtime correctness | Session lifecycle & enterprise | Multi-provider breadth | Native MCP + extensibility | Managed-agent platform |
| **Target user** | Pro/enterprise Claude users | Prosumer cross-device workflows | Power users, Linux devs | Enterprise/GitHub-integrated teams | Multi-provider power users | Extension/frontend builders | Managed-agent adopters |
| **Technical posture** | Reliability of model outputs; safety-classifier tuning | Hardening auto-update; sandbox security | OS-level sandboxing over tool restriction | Automation/CI first (`-p` mode) | Provider abstraction depth | Native (not adapter) MCP | Codex-aligned tool modes + memory |
| **Community signature** | Passive feature upvoting; spam-prone | Broad but regression-battered | Agent-subsystem deep-dives | Reliability clusters from one author | Loud free-tier thread | Triaged bug clusters | CI-noise dominated |

**Key differences:**

- **Claude Code** is uniquely exposed to *model-behavior* issues (safety-classifier over-blocking #100212, paste truncation) rather than plumbing. Its top issue is a pure feature-demand signal (multi-account connectors, 453 👍), suggesting a mature, confident user base asking for scale, not fixes.
- **OpenAI Codex** is the most *multi-surface* product (CLI, desktop, mobile, VS Code, Computer Use) — and consequently the most regression-battered. The Windows auto-update cluster is a platform-maturity problem, not a capability problem.
- **Gemini CLI** and **Qwen Code** are the most *security- and CI-conscious*: both have active supply-chain hardening PRs (fork secrets, TOCTOU) and both are investing in architectural shifts (OS sandboxing + AST tooling for Gemini; managed-agent memory + shared workspaces for Qwen). Qwen has a coherent "managed agent as a product" roadmap that no other tool matches this cycle.
- **Copilot CLI** is the most *automation-oriented* (prompt mode, `--allow-tool`), making its session-lifecycle and permission-parser bugs disproportionately costly for CI users.
- **OpenCode** and **Pi** both emphasize *provider/extensibility breadth* (Venice E2EE, native MCP, plugin hooks) — Pi's meta-issue #11927 on auto-closing reports reveals a governance maturity gap despite strong technical ambition.
- **Pi** is the only tool this cycle with a notable *process-trust* complaint (auto-closing non-contributor bugs), a warning sign for community health.

---

## 5. Community Momentum & Maturity

**Highest raw engagement:**
- **Claude Code** — highest single-issue engagement (#27302 at 453 👍 / 298 comments) and heaviest (if noisy) issue volume. Mature feature-demand base; hurt by duplicate-flooding hygiene.
- **OpenCode** — #59387's 333-comment free-tier thread is the largest single cluster across all tools, but it reflects *outage-driven* engagement (quota/reset bug) rather than healthy iteration.
- **OpenAI Codex** — broadest engagement surface (issues + discussions + show-and-tell), most active PR queue, but maturity undercut by serial update regressions.

**Rapid iteration (shipping):**
- **Qwen Code** — the only tool with releases (nightly + SDK + desktop) and the most substantial feature PRs (archive/MCP self-service, shared workspaces, Mem0 memory, hybrid code mode). Clear velocity leader this cycle.
- **Gemini CLI** — high-quality security/architecture PRs but a quiet release channel.
- **OpenCode** — active feature PRs (Venice E2EE, token reporting, cache rules) offset by stale-PR housekeeping.

**Reliability watch:**
- **Gemini CLI** and **OpenCode** both show agent-runtime maturity gaps (silent false successes, premature completions) that are more concerning than cosmetic bugs — these undermine programmatic trust in agent output.
- **Pi** and **Qwen Code** both suffer from CI/process noise (auto-close policies, bot-filed failures) that erodes community trust and triage clarity.

**Maturity ranking (this window, indicative):**
1. **Claude Code** — mature demand, needs issue hygiene.
2. **OpenAI Codex** — broadest surface, regression-fragile.
3. **Gemini CLI** — strong security posture, runtime reliability work-in-progress.
4. **Qwen Code** — highest velocity, most platform ambition, CI strained.
5. **Copilot CLI** — focused automation use case, session-lifecycle fragility.
6. **OpenCode** — strong provider/multi-provider story, systemic quota/state bugs.
7. **Pi** — technically ambitious, governance and MCP-hardening lag.

---

## 6. Trend Signals

**For technical decision-makers and developers:**

1. **Multi-identity is the next platform battleground.** Consistent, high-upvote-demand across Claude Code and Codex. Any tool shipping account-scoped connectors/workspaces first will win enterprise and multi-tenant workflows. Watch for this as a differentiator in 2027 roadmaps.

2. **MCP has moved from "integration feature" to "reliability liability."** Every project has MCP lifecycle bugs. The underlying problem is the same everywhere: connectors assume static tool surfaces and stable sessions. Teams evaluating tools should test **runtime MCP server changes, reconnection, and OAuth persistence** — this is where products differentiate on robustness, and it is the single most concentrated developer pain point in the entire ecosystem.

3. **Context compaction is a safety-critical subsystem, not a convenience.** Empty-summary history loss (Codex), stale-request resumption (Codex), usage overestimation (Pi), and turn-aborting compaction (OpenCode) all point to a class of data-loss/safety bugs. This is the highest-severity emerging risk area — recommend treating compaction as a proven-before-trust capability, especially for unattended and delegated agent workflows.

4. **Subagent reliability signals a maturity wall.** False success reporting (Gemini #22323), premature completion (OpenCode #48826), permission-ordering surprises (OpenCode #62213) — programs that consume agent output programmatically cannot yet trust termination states. Teams building automated pipelines should add **independent verification of agent-reported success** rather than trusting `status: success`.

5. **Auto-update channels are a trust liability.** Codex's Windows regression cluster and Qwen's updater file-lock bug show that breakage *immediately following update* erodes trust disproportionately. Enterprises should pin versions and stage rollouts rather than consuming auto-updates.

6. **Token economics is driving real optimization work.** Pi's prompt-cache warming PR quantifies ~26% of spend recoverable from one fix; Gemini and Codex both have EPICs/threads on token efficiency. Expect prompt-cache control, pre-flight budget checks, and accurate accounting to become table-stakes features.

7. **Terminal/editor integration is standardizing organically.** OSC 7501 appearing independently in Copilot CLI and Gemini CLI (plus JetBrains ACP parity asks in Qwen) suggests agent-status reporting will converge on a common protocol. Early adopters get ecosystem-integration advantage.

8. **Security and supply-chain hardening is now a visible PR priority.** Gemini CLI's fork-secret exposure fix and TOCTOU DNS-rebinding patch, plus Qwen's CI credential pinning, show leading projects are treating their own build/CI as an attack surface. For adopters, this is a positive maturity signal — prefer tools with active security-response velocity.

9. **Governance and process hygiene are secondary but real trust signals.** Pi's auto-close policy complaint and Qwen's bot-noise-dominated tracker both warn that community trust depends as much on triage discipline as on shipping features. Evaluate projects on **signal-to-noise in their issue trackers** as a proxy for maintainer responsiveness.

10. **Local-model ergonomics remain an unsolved frontier.** OpenCode (#62765, #62778) and Qwen Code (#16526) both show local/self-hosted workflows lacking guardrails (compaction failures, oversubscription, weak tool-use). The gap between cloud and local experience is a persistent opportunity for tooling that gets small-model agentic behavior right.

---

*Report compiled from 2026-11-09 community digests across seven AI CLI repositories. Counts reflect the digests as provided and are indicative rather than exhaustive; discussions are reported only where data was supplied.*

---

## Per-Tool Reports

<details>
<summary><strong>Claude Code</strong> — <a href="https://github.com/anthropics/claude-code">anthropics/claude-code</a></summary>

## Claude Code Skills Highlights

> Source: [anthropics/skills](https://github.com/anthropics/skills)

# Claude Code Skills — Community Highlights Report
*Data snapshot: 2026-11-09 | Source: github.com/anthropics/skills*

---

## 1. Top Skills Ranking

> Note: comment counts on PRs are not exposed in the source data (`undefined`). Ranking below is inferred from issue-linkage, update recency, and cross-referenced discussion volume.

| # | Skill / PR | What It Does | Discussion Highlights | Status |
|---|---|---|---|---|
| 1 | [mcp-builder fix #1742](https://github.com/anthropics/skills/pull/1742) | Fixes MCP ≥2.0 import rename (`streamablehttp_client` → `streamable_http_client`) and header config via `create_mcp_http_client` | Tied to #1668; upgraded after 1 month of revision | OPEN |
| 2 | [skill-creator eval isolation #1298](https://github.com/anthropics/skills/pull/1298) | Fixes false misses in trigger evals: per-worker probe contention, Windows pipe failures, runtime-error misclassification | Active 3+ months; overlaps with #556, #1352, #1394 | OPEN |
| 3 | [notion-spec-to-implementation + quantitative-resume-auditor #1245](https://github.com/anthropics/skills/pull/1245) | Converts Notion specs into implementable tasks w/ acceptance criteria; resume scoring skill | Bundled multi-skill PR, updated Oct 15 | OPEN |
| 4 | [webapp-testing: remove shell=True #1980](https://github.com/anthropics/skills/pull/1980) | Eliminates CWE-78 command injection in `with_server.py` | Security-motivated; updated Nov 1 | OPEN |
| 5 | [docx LibreOffice timeout fix #1792](https://github.com/anthropics/skills/pull/1792) | Reports real errors on `soffice` timeout; verifies revision marks removed before declaring success | Correctness fix, updated Nov 1 | OPEN |
| 6 | [pyxel retro-game skill #525](https://github.com/anthropics/skills/pull/525) | Guides Claude to build/debug/verify retro Python games with headless frame inspection | Long-lived (since March), still active Nov 1 | OPEN |
| 7 | [proofcore-contract-auditor #1771](https://github.com/anthropics/skills/pull/1771) | Static analysis of Solidity/Rust contracts + Merkle audit proofs on TON | Novel Web3 vertical | OPEN |
| 8 | [xlsx external-link resolution #2019](https://github.com/anthropics/skills/pull/2019) | Resolves worksheet-scoped defined names/aliases in `external_links_at_risk` | Fixes #2013, updated Nov 3 | OPEN |

**Takeaway:** The most-watched PRs are overwhelmingly **bug/security fixes to existing skills** (mcp-builder, skill-creator, webapp-testing, docx, xlsx), not new skill submissions — a signal that the community is now in a *hardening* phase.

---

## 2. Community Demand Trends

Distilled from the top-commented Issues, five demand clusters emerge:

| Theme | Representative Issue | Signal |
|---|---|---|
| **Trust & security boundaries** | [#492](https://github.com/anthropics/skills/issues/492) (46 comments) — community skills impersonating `anthropic/` namespace | Highest-engagement issue in the repo; demands namespace/verification controls |
| **Org-wide skill sharing / distribution** | [#228](https://github.com/anthropics/skills/issues/228) (17 👍 8) — share skills inside Claude.ai orgs without manual .skill uploads | Strong product-feature demand |
| **Reliable eval / optimization tooling** | [#556](https://github.com/anthropics/skills/issues/556) (0% trigger rate), [#1352](https://github.com/anthropics/skills/issues/1352) (parallel-worker false negatives) | Community is *testing the test harness* — deep investment in skill quality infra |
| **Context/token efficiency** | [#1487](https://github.com/anthropics/skills/issues/1487) — `claude-api` injects ~156k tokens eagerly | People want lazy / progressive-disclosure skills |
| **Governance & reasoning quality** | [#412](https://github.com/anthropics/skills/issues/412) agent-governance, [#1385](https://github.com/anthropics/skills/issues/1385) quality-gate pipeline | Emerging "meta-skill" direction: safety, audit trails, adversarial review |

Secondary demand: **document typography quality** ([#514](https://github.com/anthropics/skills/pull/514)), **external skill contributions** ([#1328](https://github.com/anthropics/skills/issues/1328)/[#1329](https://github.com/anthropics/skills/issues/1329) `compact-memory`), and **developer ergonomics** ([#202](https://github.com/anthropics/skills/issues/202) — skill-creator should be operational, not educational).

---

## 3. High-Potential Pending Skills

Active PRs not yet merged but showing momentum (recent updates + linked issue fixes):

1. **[#1980 webapp-testing security fix](https://github.com/anthropics/skills/pull/1980)** — CWE-78 remediation; small, focused, high-value. *Likely earliest merge.*
2. **[#2019 xlsx external-link resolution](https://github.com/anthropics/skills/pull/2019)** — Fixes a filed issue (#2013); updated Nov 3. Clean-scoped bug fix.
3. **[#1977 algorithmic-art wrapAround](https://github.com/anthropics/skills/pull/1977)**, **[#1976 element_discovery](https://github.com/anthropics/skills/pull/1976)**, **[#1975 pptx --cols validation](https://github.com/anthropics/skills/pull/1975)** — three tight fixes from one contributor (gerardrecinto), each closing a numbered issue. *Batch-merge candidate.*
4. **[#1792 docx timeout verification](https://github.com/anthropics/skills/pull/1792)** — Correctness + verification logic; updated Nov 1.
5. **[#1742 mcp-builder MCP≥2 support](https://github.com/anthropics/skills/pull/1742)** — Breaking-change shim; tied to open issue #1668.
6. **[#1681 skill-creator direct execution](https://github.com/anthropics/skills/pull/1681)** & **[#1997 YAML frontmatter parsing](https://github.com/anthropics/skills/pull/1997)** — Complementary skill-creator fixes; both address real CLI failures.
7. **[#514 document-typography skill](https://github.com/anthropics/skills/pull/514)** — New skill proposal; addresses universal output-quality complaint (orphans, widows, numbering).
8. **[#2108 docs: clarify snapshot/sync status](https://github.com/anthropics/skills/pull/2108)** — Documentation clarity for the synced-skills model; low-risk.

---

## 4. Skills Ecosystem Insight

> **The community's most concentrated demand is not new skill types but *trust, correctness, and eval reliability* for the existing skill set** — namespace spoofing (#492), false-negative evals (#556/#1352), context blowouts (#1487), and command-injection risks (#1980) together dominate discussion, indicating the ecosystem has shifted from "what can Claude do?" to "how do we guarantee existing skills behave safely and predictably."

---

*All links: `https://github.com/anthropics/skills/pull/<n>` and `/issues/<n>`.*

---

# Claude Code Community Digest — 2026-11-09

## 1. Today's Highlights

No new releases or pull requests landed in the last 24 hours, but issue activity was unusually heavy. The dominant story is a **spam-like burst of ~20 duplicate reports** about "Fable 5.1" truncating pasted text, all filed by the same author (`Nahuel990`) within minutes — a case worth flagging for maintainers. Meanwhile, the top-voted open request (#27302, multi-account Connector support) continues to accumulate strong community support at 453 👍 and 298 comments.

---

## 2. Releases

No new releases in the last 24 hours.

---

## 3. Hot Issues

1. **[#27302 — Support multiple Connector accounts](https://github.com/anthropics/claude-code/issues/27302)** `enhancement, area:auth`
   The clear community favorite: users want to connect the same connector under different accounts in Claude and Claude Code on the web. With **453 👍 and 298 comments**, this is the highest-engagement issue in the window and signals strong demand for multi-tenant/auth-scoped connector support.

2. **[#100212 — Safety classifier withholds healthcare documentation responses](https://github.com/anthropics/claude-code/issues/100212)** `bug, platform:windows, area:model`
   A safety classifier false-positive blocking legitimate clinical documentation work. High severity for professional users — over-blocking in regulated domains is a recurring trust concern.

3. **[#100115 — Desktop Code tab: MCP tool list stays stale](https://github.com/anthropics/claude-code/issues/100115)** `bug, platform:macos, area:mcp, area:desktop`
   Connector tool lists don't refresh after a server adds tools; toggling the connector off/on doesn't help. Directly impacts desktop MCP workflows.

4. **[#88172 — MCP connector serves stale `tools/list` until app relaunch](https://github.com/anthropics/claude-code/issues/88172)** `bug, platform:macos, area:mcp`
   Related to #100115 but older and broader: even `server/discover` calls on the wire don't update the usable tool surface. Suggests a caching-layer bug in connector lifecycle management.

5. **[#108446 — Fable 5.1 stops reading pasted text after the start](https://github.com/anthropics/claude-code/issues/108446)** `bug, area:model`
   The "canonical" report of today's duplicate burst: the model reads only the beginning of a paste and acts on partial context. One of the few with a comment — worth tracking as the reference issue.

6. **[#108451 — Scheduled tasks: remote MCP connectors not reconnected at session start](https://github.com/anthropics/claude-code/issues/108451)** `bug, area:mcp, area:routines`
   Headless/cloud runs fail when scale-to-zero MCP servers (e.g., Cloud Run) aren't ready at session start, and there's no retry within the turn. A meaningful reliability gap for unattended automation.

7. **[#108470 — Make `@` import file extensions in `CLAUDE.md` configurable](https://github.com/anthropics/claude-code/issues/108470)** `enhancement, area:core`
   Requests configurability for which file extensions `@`-imports resolve in `CLAUDE.md`. Small but practical — relevant to monorepos and non-standard file types.

8. **[#108469 — GitHub integration error (GH-HMWV22)](https://github.com/anthropics/claude-code/issues/108469)** `bug, github-integration`
   A filed-from-settings error report with minimal diagnostics. Low detail, but GitHub integration failures block PR/issue workflows for affected users.

9. **[#108462 — Fable 5.1 truncation (has repro)](https://github.com/anthropics/claude-code/issues/108462)** `bug, has repro, area:model`
   Notable among the duplicates for carrying the `has repro` label — the most actionable entry for triage.

10. **[#108431 — Fable 5.1 truncation (early duplicate)](https://github.com/anthropics/claude-code/issues/108431)** `bug, area:model`
    One of the first duplicates with a comment, useful for establishing the report timeline.

> ⚠️ **Triage note:** Issues #108445–#108470 (excluding #108451 and #108470) are near-identical Fable 5.1 reports from a single author. Recommend consolidating into #108446/#108462 and closing the rest as duplicates.

---

## 4. Key PR Progress

No pull requests were updated in the last 24 hours.

---

## 5. Hot Discussions

No discussion data was provided for this window.

---

## 6. Feature Request Trends

- **Multi-account / multi-tenant connectors** (#27302): The single strongest signal — users want the same connector bound to different accounts, implying demand for account-scoped auth in both web and CLI contexts.
- **Configurable context ingestion** (#108470): Extending `CLAUDE.md` `@`-import behavior to arbitrary file extensions — part of a broader "make context assembly programmable" theme.
- **MCP lifecycle robustness**: Though filed as bugs, the stale-tool and reconnect issues (#100115, #88172, #108451) point to an implicit feature ask — deterministic connector refresh/reconnect semantics.

---

## 7. Developer Pain Points

1. **MCP connector staleness and lifecycle bugs** — Three separate issues (#100115, #88172, #108451) describe tools not appearing, not refreshing, or not reconnecting. This is the most concentrated area of developer friction: MCP servers that change at runtime break the trust contract with the agent.
2. **Safety classifier over-blocking** (#100212) — Legitimate professional work (healthcare documentation) being withheld erodes confidence in unattended use.
3. **Model context truncation on long pastes** — The Fable 5.1 cluster, despite duplicate spam, reflects a real workflow break: users paste logs/code and the model acts on partial input, forcing re-pastes.
4. **Issue hygiene / duplicate flooding** — ~20 identical reports in one day from one author; community signal is diluted and maintainer triage cost rises. Consider rate-limiting or auto-dedup on title similarity.

---

*Generated from github.com/anthropics/claude-code activity for 2026-11-09. No releases or PRs to report; issue volume dominated by model-behavior reports and long-standing MCP/auth requests.*

</details>

<details>
<summary><strong>OpenAI Codex</strong> — <a href="https://github.com/openai/codex">openai/codex</a></summary>

# OpenAI Codex Community Digest — 2026-11-09

## 1. Today's Highlights
No new releases landed in the last 24 hours, but the issue tracker is dominated by Windows desktop regression reports tied to recent auto-updates — browser/Computer Use initialization failures, disappearing local projects, and `CreateProcessSecurityEnvironment` blocking all local command execution. Alongside these regressions, the community continues to push on multi-account/workspace ergonomics and context-management pain (token drain, compaction behavior), while the PR queue shows steady, mostly `copyberry[bot]`-driven polish work on MCP startup, sidebar navigation, and provider sign-in.

## 2. Releases
No new releases in the last 24 hours.

## 3. Hot Issues

1. **[#57641](https://github.com/openai/codex/issues/57641) — Terminal CLI @Browser fails with "browser access host is unavailable" (Windows, Brave)** — 48 comments, 29 👍. The hottest thread of the day: a Pro user on Codex CLI 0.164.0 reports browser tooling breaking after desktop app update 26.1023.6123.0. High engagement suggests it's broadly reproducible on Windows.

2. **[#20500](https://github.com/openai/codex/issues/20500) — Support multiple named accounts per app/connector** — 33 comments, 118 👍 (highest upvote count in the batch). A long-running enhancement asking for per-connector account selection with hard privacy boundaries — distinct from profile switching. Strong community demand.

3. **[#59895](https://github.com/openai/codex/issues/59895) — Windows: local projects disappear after app update and switching back to personal account** — 33 comments. A serious data-visibility regression on Windows package 26.1103.3517.0. Related in theme to the macOS report in #54155.

4. **[#37967](https://github.com/openai/codex/issues/37967) — [CLOSED] Remote Control cannot attach to live CLI sessions** — 27 comments, 36 👍. Closed this cycle. Affects the workstation-primary workflow (CLI on desktop, phone for monitoring/approval). Closure is a positive signal for remote-workflow users.

5. **[#57032](https://github.com/openai/codex/issues/57032) — VS Code/Linux: Codex could not start its UI in extension 26.1021.22027** — 20 comments, 16 👍. Extension startup failure on Fedora 43 with VS Code 1.141.1 — notable because Linux IDE users are a smaller but vocal cohort.

6. **[#31383](https://github.com/openai/codex/issues/31383) — Plugin hooks fail with exit 1 after startup marketplace auto-upgrade** — 18 comments. A stale-cache/version-mismatch bug in the plugin marketplace auto-upgrade path on Windows; matters for anyone relying on hooks in CI-like workflows.

7. **[#56866](https://github.com/openai/codex/issues/56866) — Codex 5h limit drained in 10 minutes** — 11 comments. Rate-limit burn with gpt-5.6 Terra. Low upvotes but high severity perception; part of a recurring rate-limit theme (see also #48394).

8. **[#59941](https://github.com/openai/codex/issues/59941) — Windows Desktop 26.1103.31037: CreateProcessSecurityEnvironment 0x8007000F blocks all local commands** — 4 comments. Sandbox/security-environment failure that blocks even `Get-Location`; a hard blocker for Windows desktop users post-update.

9. **[#60293](https://github.com/openai/codex/issues/60293) — Manual compaction with empty summary wipes history** — 2 comments. Fresh report on a source build: an empty compaction summary replaces the entire conversation history with nothing. Data-loss class bug in context management.

10. **[#54646](https://github.com/openai/codex/issues/54646) — Compaction resumes a 24-day-old write request during read-only delegated research** — 1 comment. Safety-relevant: a delegated read-only agent, post-compaction, edited six files based on a stale request. Highlights compaction/context-isolation risks in subagent workflows.

*Other notable:* [#60209](https://github.com/openai/codex/issues/60209) (Linux desktop memory leak), [#60250](https://github.com/openai/codex/issues/60250) (Computer Use captures wrong WSLg window), [#60236](https://github.com/openai/codex/issues/60236) (safety review blocks SketchUp SDK workflow without recovery path).

## 4. Key PR Progress

1. **[#60286](https://github.com/openai/codex/pull/60286) — Search task history from the command center** (CLOSED). Scans backend history for case-insensitive task-name/prompt matches, making older tasks discoverable beyond the loaded page.

2. **[#60280](https://github.com/openai/codex/pull/60280) — Fix MCP startup tracking when attaching to shared app servers** (CLOSED). Prevents the TUI from getting stuck busy when clients attach to a shared app server with a different local MCP config.

3. **[#31045](https://github.com/openai/codex/pull/31045) — Defer MCP auth discovery until startup failure** (OPEN). Moves network-dependent OAuth discovery out of session init so unavailable optional HTTP MCP servers don't delay thread creation — directly addresses a reported Windows Desktop timeout.

4. **[#60271](https://github.com/openai/codex/pull/60271) — Keep agents overview open when there's no chat to return to** (CLOSED). UX fix: only allow cancel to dismiss the overview when a chat is displayed or startup is pending; hides the misleading `esc back` hint.

5. **[#60270](https://github.com/openai/codex/pull/60270) — Warn when a CLI model is absent from the ChatGPT model list** (CLOSED). Startup warning for ChatGPT-account users when a CLI model override isn't in the model list, with a spelling suggestion.

6. **[#60190](https://github.com/openai/codex/pull/60190) — Expose agent tree IDs and environment removal** (CLOSED). Adds `CodexThread::agent_tree_id()` (shared by root and native children, new ID on tree load) and `EnvironmentManager::remove_environment()` — useful API surface for orchestrators.

7. **[#60188](https://github.com/openai/codex/pull/60188) — Allow model overrides for incremental Code Mode tool messages** (CLOSED). Adds configurable `exec_tools_update_hint` / `exec_tools_removed_definitions_header` for rendering Code Mode catalog updates and removals.

8. **[#60186](https://github.com/openai/codex/pull/60186) — Allow creating custom providers during sign-in** (CLOSED). Adds "Create a custom provider or gateway" to the sign-in picker, removing the prior requirement for a pre-saved gateway config.

9. **[#60181](https://github.com/openai/codex/pull/60181) — Keyboard and mouse navigation for sidebar tabs** (CLOSED). Adds configurable `sidebar_up`/`sidebar_down` actions with wraparound, leader shortcuts (`Shift-Tab`/`Tab`, `Up`/`Down`, `k`/`j`), and mouse-wheel tab cycling.

10. **[#60206](https://github.com/openai/codex/pull/60206) — Increase Bazel core unit test shards from 8 to 16** (CLOSED). CI throughput improvement; minor but indicative of growing test-suite scale.

## 5. Hot Discussions

**Ideas**
- **[#60240](https://github.com/openai/codex/discussions/60240) — Configurable permissions for a personal dot across Codex and Work** — Proposes confirm-every-action / limited / full-access tiers scoped per service.
- **[#60239](https://github.com/openai/codex/discussions/60239) — An OpenAI-native workspace independent of Windows** — Frustration with Windows permission prompts and interrupted interactions; asks for a platform-neutral workspace.
- **[#60238](https://github.com/openai/codex/discussions/60238) — A secure way to identify a personal dot in external applications** — Requests verifiable identity/authorization for agent-originated requests to third-party apps.

**Show and tell**
- **[#60264](https://github.com/openai/codex/discussions/60264) — Codex publishes reports as web pages and replies with the link (remote MCP)** — BrewPage MCP integration (hosted, no API key) to replace walls of text; author discloses affiliation.
- **[#60116](https://github.com/openai/codex/discussions/60116) — Pushary: answer Codex approvals from your phone** — Paid hosted service using native hooks + MCP to route shell/`apply_patch`/MCP approvals to mobile; author discloses commercial interest.
- **[#60185](https://github.com/openai/codex/discussions/60185) — agent-recap: local search across Codex and Claude Code transcripts** — Browser-based, no-account JSONL transcript search across both tools.
- **[#60297](https://github.com/openai/codex/discussions/60297) — Better Codex Context (BCC) plugin** — Aims to reduce token usage and "unstuck" goals on large, long-running projects.
- **[#60282](https://github.com/openai/codex/discussions/60282) — Worktree receipt** — Python/Git check for untracked local setup-file drift between worktrees at the same commit; author discloses Zozo HQ affiliation.

*(No Q&A threads in this window.)*

## 6. Feature Request Trends

- **Multi-identity & multi-workspace ergonomics**: named accounts per connector (#20500, 118 👍), switching workspaces without sign-out (#51521, now closed), and project/account reconciliation across Desktop/Mobile (#35972, #54155). The #59895 and #54155 reports show this is also a data-visibility bug source.
- **Context & memory management**: compaction correctness (#60293, #54646), token-cost reduction plugins (#60297), and persistent-memory durability for long-running agents (#59687).
- **Orchestration & subagents**: a "Master Chat"/Project Orchestrator for coordinating multiple Codex conversations (#60088), plus API-level support (#60190 agent tree IDs).
- **Rate-limit controls**: explicit opt-out of auto-consuming purchased credits (#48394), and concerns about rapid limit drain (#56866).
- **Permission & identity scoping**: configurable per-service permissions and verifiable agent identity for external apps (#60238–#60240).

## 7. Developer Pain Points

- **Windows desktop regressions after auto-update** are the single loudest cluster: browser host unavailable (#57641), Computer Use/Chrome init failures (#56924, #60250, #60254), `CreateProcessSecurityEnvironment` blocking local commands (#59941), npm launcher `EPERM: lstat 'C:\'` (#60281), and vanishing local projects (#59895). The pattern — breakage immediately following update 26.10xx/26.11xx — erodes trust in the auto-update channel.
- **Sandbox/approval friction**: approved `filesystem.write` still failing `git init` with EPERM (#60162), safety review blocking previously working workflows without explanation or recovery (#60236), and credential handoff blocked by automatic review (#53487).
- **Context integrity**: empty compaction summaries destroying history (#60293) and stale-request resumption post-compaction (#54646) are the most alarming — both are data-loss/safety class, not cosmetic.
- **Cross-surface state divergence**: mobile Remote vs. Desktop project assignment (#35972), and UI claiming local-folder attachment while the chat runs in a cloud workspace (#58710).
- **Resource hygiene**: Linux desktop memory leak on chat switching (#60209) and `node_repl` accumulating thousands of empty `.tmpXXXXXX` dirs on macOS (#53196).
- **Stale-cache/version skew** in plugin marketplace auto-upgrades breaking hooks mid-session (#31383).

---
*Digest compiled from github.com/openai/codex activity for 2026-11-09. Comment/upvote counts reflect the snapshot provided.*

</details>

<details>
<summary><strong>Gemini CLI</strong> — <a href="https://github.com/google-gemini/gemini-cli">google-gemini/gemini-cli</a></summary>

# Gemini CLI Community Digest — 2026-11-09

## 1. Today's Highlights

No new releases landed in the last 24 hours, but issue activity remains concentrated on the **agent subsystem** — subagent reliability, turn-limit handling, and tool/skill discovery dominate the top of the queue. Several P1 bugs (generalist agent hangs, false "GOAL" success reporting, Wayland browser failures) are in `need-retesting`, suggesting active fixes are in flight. On the PR side, security hardening (Windows git diff bypass, CI secret exposure for forks, MCP OAuth dispatcher routing) leads the queue.

## 2. Releases

No new releases in the last 24 hours.

## 3. Hot Issues

1. **[#22323](https://github.com/google-gemini/gemini-cli/issues/22323) — Subagent MAX_TURNS recovery reported as GOAL success (P1, 13 comments, 👍2)**
   The `codebase_investigator` subagent reports `status: "success"` / `Termination Reason: "GOAL"` despite hitting its turn limit before doing any analysis. This masks real failures and undermines trust in agent output — the most-commented issue of the day and a serious correctness bug for anyone relying on subagent results programmatically.

2. **[#21409](https://github.com/google-gemini/gemini-cli/issues/21409) — Generalist agent hangs (P1, 8 comments, 👍8)**
   Any delegation to the generalist agent (even simple folder creation) hangs indefinitely. Highest 👍 count in the queue signals broad user impact; the workaround is instructing the model not to defer to subagents, which cripples the intended agent architecture.

3. **[#19873](https://github.com/google-gemini/gemini-cli/issues/19873) — Zero-dependency OS sandboxing & post-execution intent routing (P2, 9 comments)**
   Proposal to lean into Gemini 3's native bash affinity by sandboxing shell execution at the OS level rather than restricting tools. Architecturally significant — if adopted, it reshapes how the CLI trades off capability against safety.

4. **[#21968](https://github.com/google-gemini/gemini-cli/issues/21968) — Gemini does not use skills and subagents enough (P2, 7 comments)**
   Anecdotal but compelling: custom skills ("gradle", "git") go unused unless explicitly invoked. This is a prompt/tool-selection alignment problem that directly limits the value of the extensibility system.

5. **[#22745](https://github.com/google-gemini/gemini-cli/issues/22745) — Assess AST-aware file reads, search, and mapping (P2, 7 comments)**
   EPIC investigating whether AST-aware tooling reduces wasted turns and token noise from misaligned reads. A strong signal that the team is thinking about context efficiency at the tool layer, not just the prompt layer.

6. **[#22267](https://github.com/google-gemini/gemini-cli/issues/22267) — Browser Agent ignores `settings.json` overrides (P2, 5 comments)**
   `AgentRegistry` reads merged settings correctly, but `BrowserManager` discards them (e.g., `maxTurns`). Configuration that silently doesn't apply is a classic developer-trust killer.

7. **[#21983](https://github.com/google-gemini/gemini-cli/issues/21983) — Browser subagent fails on Wayland (P1, 4 comments)**
   Linux/Wayland users get a "GOAL" termination with no useful browser session. Platform-specific P1 blocking a growing desktop Linux segment.

8. **[#24246](https://github.com/google-gemini/gemini-cli/issues/24246) — 400 error with >128 tools (P2, 3 comments)**
   Tool-count explosion (from MCP servers and skills) hits a hard API ceiling with no graceful degradation. As the ecosystem grows, this becomes a scaling wall for power users.

9. **[#22672](https://github.com/google-gemini/gemini-cli/issues/22672) — Agent should stop/discourage destructive behavior (P2, 3 comments)**
   Model occasionally reaches for `git reset`/`--force` when safer alternatives exist. Safety-adjacent and highly relevant to teams running the CLI against real repos.

10. **[#18836](https://github.com/google-gemini/gemini-cli/issues/18836) — Replace WriteToDo with persistent file-based task tracking (P3, 2 comments)**
    In-context todo lists suffer "context rot" and vanish between sessions. A file-based CRUD tracker would fix memory loss and token cost — a foundational improvement for long-running tasks.

## 4. Key PR Progress

1. **[#30095](https://github.com/google-gemini/gemini-cli/pull/30095) — fix: prevent Windows git diff permission bypass (P1, security)**
   Extracts shared git-safety validation and rejects dangerous arguments misclassified as read-only on Windows. Highest-priority PR in the queue; directly closes a safety hole.

2. **[#30090](https://github.com/google-gemini/gemini-cli/pull/30090) — ci(chained_e2e): don't expose GEMINI_API_KEY to fork-controlled code**
   Gates the artifact and API key on the triggering run's head repo, closing a supply-chain risk in the `workflow_run` chain. Critical CI hardening.

3. **[#30071](https://github.com/google-gemini/gemini-cli/pull/30071) — Route MCP OAuth fetches through the safe dispatcher**
   Fixes a TOCTOU DNS-rebinding gap: MCP OAuth metadata/registration fetches bypassed re-validation at connect time. Security-relevant for anyone using remote MCP servers.

4. **[#30068](https://github.com/google-gemini/gemini-cli/pull/30068) — fix(cli): forward context management settings to core**
   A configured 400k-token output-masking threshold was silently discarded in favor of the 50k default. Small diff, large behavioral impact on long sessions.

5. **[#27423](https://github.com/google-gemini/gemini-cli/pull/27423) — feat(core): configurable per-tool-call timeout (`tools.callTimeout`)**
   Caps wall-clock time for any single tool call, addressing unbounded shell commands. Long-standing request (#3375) with `help wanted` — good first-contribution target.

6. **[#29918](https://github.com/google-gemini/gemini-cli/pull/29918) — feat(cli): OSC 7501 terminal status reporting (closed)**
   Reports working/blocked/idle to supporting terminals so multi-tab users can see which session needs attention. Nice UX polish for power users; closed but worth tracking for re-submission.

7. **[#29919](https://github.com/google-gemini/gemini-cli/pull/29919) — feat(cli): `--append-system-prompt` (closed)**
   Lets users add project context without replacing the entire system prompt (unlike `GEMINI_SYSTEM_MD`). Closed, but the underlying need — additive prompt customization — remains unaddressed.

8. **[#29912](https://github.com/google-gemini/gemini-cli/pull/29912) — fix(core): strip trailing punctuation from `web_fetch` URLs (closed)**
   Comma-separated URL lists produced polluted URLs (`https://example.com/,`) that failed downstream. Small correctness fix for a common agent pattern.

9. **[#30066](https://github.com/google-gemini/gemini-cli/pull/30066) — ci: inert canary in package.json scripts (security PoC, do not merge)**
   Deliberate PoC demonstrating that chained E2E workflows execute fork-branch code on self-hosted runners. Pairs with #30090; the security response here matters more than the PR itself.

10. **[#29258](https://github.com/google-gemini/gemini-cli/pull/29258) & [#29062](https://github.com/google-gemini/gemini-cli/pull/29062) — stale nightly version bumps**
    Automated release PRs sitting stale (0.61.0/0.56.0 nightlies). Worth flagging as release-hygiene noise; #29062 has been closed as stale.

## 5. Hot Discussions

No discussion data was provided for this period; section omitted.

## 6. Feature Request Trends

- **Persistent, file-based task/memory tracking** (#18836, #18287): replace in-context todo lists and explore shared memory across parallel subagents.
- **AST-aware tooling** (#22745, #22746, #22747): a coordinated EPIC pushing toward syntax-aware reads, searches, and codebase mapping to cut turns and token bloat.
- **Token-frugal context management** (#19561): "tactful extraction" hierarchies (grep-first, surgical reads) to combat the ~36.6k-token baseline per turn.
- **Agent self-awareness and discoverability** (#21432, #18283, #18285): the CLI should know its own flags/hotkeys, ship example subagents, and support subagent discovery via `settings.json`.
- **Subagent observability** (#22598, #21763): subagent trajectories should surface in `/chat share` and `/bug` reports.
- **Safety rails over hard restrictions** (#19873, #22672): sandboxing plus intent routing instead of tool exclusion; discouraging destructive git/DB operations.
- **Per-workspace policy** (#18397): scoped auto-approval policies rather than a single global policy.

## 7. Developer Pain Points

- **Subagent reliability is the dominant frustration.** Hangs on delegation (#21409, 👍8), false success reporting on turn-limit hits (#22323), Wayland browser failures (#21983), and ignored `maxTurns` overrides (#22267) all point to a maturity gap in the agent runtime. Users are losing hours to silent failures.
- **Configuration that doesn't take effect.** `settings.json` overrides discarded by the Browser Agent (#22267) and `contextManagement` thresholds dropped by the CLI (#30068) erode trust in documented knobs.
- **Context and token economics.** The ~36.6k-token baseline, firehose file reads (+15k tokens/turn), and context rot in todo lists (#19561, #18836) are recurring cost/quality complaints.
- **Skill/subagent under-utilization.** Gemini rarely invokes custom skills or subagents unprompted (#21968), making the extensibility investment feel wasted.
- **Scale ceilings.** The >128-tool 400 error (#24246) is an emerging wall as MCP servers and skills multiply.
- **Workspace hygiene.** The model scattering temp scripts across directories (#23571) creates cleanup overhead before clean commits.
- **Agent self-knowledge gaps.** Inaccurate CLI flags/hotkey guidance (#21432) and incomplete `/bug` context (#21763) make the tool a poor guide to itself.

---
*Digest generated from github.com/google-gemini/gemini-cli activity on 2026-11-08/09. Note: the issue/PR queue shown is heavily agent- and security-focused; release channel is quiet.*

</details>

<details>
<summary><strong>GitHub Copilot CLI</strong> — <a href="https://github.com/github/copilot-cli">github/copilot-cli</a></summary>

# GitHub Copilot CLI — Community Digest
**Date: 2026-11-09**

---

## 1. Today's Highlights

No new releases landed in the past 24 hours, but the issue tracker stayed active with 14 issues updated, including a **CLOSED** fix for a bundled ripgrep crash on 16KB-page ARM64 kernels (Asahi Linux). The bulk of activity centers on **session management reliability** — several reports from the same author (@nima-ap) describe extensions, skills, and sessions failing to initialize or resume correctly. A popular terminal-integration feature request (OSC 7501 status reporting, 👍11) also gained traction.

---

## 2. Releases

*No new releases in the last 24 hours.*

---

## 3. Hot Issues

1. **[#4977] [CLOSED] Bundled ripgrep aborts with jemalloc 'Unsupported system page size' on 16KB-page ARM64 kernels (Asahi Linux)** — *The only closed item in this batch.* A jemalloc page-size mismatch broke `rg` on Linux-on-Apple-Silicon. Community reaction: 👍1, 3 comments. Important because it blocked the toolchain for a growing ARM64 Linux user base. [Link](https://github.com/github/copilot-cli/issues/4977)

2. **[#5320] [OPEN] Support OSC 7501 agent-status reporting for herdr-app/herdr-core** — Highest-engagement issue this cycle (👍11, 3 comments). Requests native terminal status reporting so terminals can display Copilot session activity. Signals demand for deeper terminal-ecosystem integration. [Link](https://github.com/github/copilot-cli/issues/5320)

3. **[#5219] [OPEN] Session wedged: task subagent with unknown model id falls back to 128k default caps and fails turn** — A `task` subagent with an unrecognized model id defaults to 128k context and fails the entire turn with a prompt-budget error. High severity for long-running interactive sessions. 4 comments. [Link](https://github.com/github/copilot-cli/issues/5219)

4. **[#5100] [OPEN] Session event delivery permanently fails after one 120s host-ack timeout** — A single host-ack timeout renders a session unusable until resume. Reliability issue affecting long interactive sessions. 3 comments. [Link](https://github.com/github/copilot-cli/issues/5100)

5. **[#4378] [OPEN] Cloud agent: MCP registry policy fetch fails with 401/403 on GHEC data residency** — All user-configured MCP servers are silently dropped on GHEC-with-data-residency (`*.ghe.com`). Enterprise-impacting and hard to diagnose due to silent failure. [Link](https://github.com/github/copilot-cli/issues/4378)

6. **[#5380] [OPEN] Extensions don't start for sessions created with `create_session` until user switches to them** — New programmatically-created sessions launch without extension tools, skills, or hooks. Part of a cluster of `create_session` bugs. [Link](https://github.com/github/copilot-cli/issues/5380)

7. **[#5386] [OPEN] Session created with `create_session` can't be resumed after `close_session` if user switched to it** — Companion to #5380; `/resume` fails to re-associate the local session. Reinforces the session-lifecycle reliability theme. [Link](https://github.com/github/copilot-cli/issues/5386)

8. **[#5384] [OPEN] Session-state artifact storage strands project deliverables outside the active Git repo** — Agents write deliverables into `~/.copilot/session-state/<id>/files/` instead of the worktree, causing files to be invisible to Git. Practical workflow/data-loss concern. [Link](https://github.com/github/copilot-cli/issues/5384)

9. **[#5383] [OPEN] Allow setting default Agent** — Feature request to configure a default agent rather than activating per-session, mirroring existing defaults for model/reasoning/context. Low friction, clear consistency argument. [Link](https://github.com/github/copilot-cli/issues/5383)

10. **[#5381] [OPEN] In prompt mode, extension tool permission request waits forever; `--allow-tool` can't name an extension tool** — `copilot -p` hangs indefinitely on extension-registered tool permission requests, and `--allow-tool` has no way to pre-authorize them. Breaks non-interactive automation. [Link](https://github.com/github/copilot-cli/issues/5381)

*Also notable:* [#5379] PowerShell approval parser over-prompts for simple git commands (Windows permission friction), and [#5382] extension skills vanish after `new_context`.

---

## 4. Key PR Progress

*No pull requests were updated in the last 24 hours (0 items).*

---

## 5. Hot Discussions

*No discussion data was provided for this cycle.*

---

## 6. Feature Request Trends

- **Configuration defaults & parity:** Requests to make per-session settings (e.g., a default agent, #5383) first-class defaults, consistent with existing model/reasoning/context defaults.
- **Terminal & editor ecosystem integration:** OSC 7501 status reporting (#5320) and rendering `postToolUse` hook messages in the desktop app (#5374) point to growing demand for Copilot CLI to surface state and output into host environments.
- **Enterprise/MCP configurability:** GHEC data-residency MCP policy support (#4378) reflects enterprise deployment needs.
- **Extension/skill lifecycle control:** Multiple issues request predictable extension and skill behavior across session creation, context clearing, and resume.

---

## 7. Developer Pain Points

- **Session lifecycle fragility:** The dominant theme this cycle. Sessions wedge on subagent model resolution (#5219), become unusable after a single host-ack timeout (#5100), and `create_session` sessions fail to start extensions (#5380), resume after close (#5386), or retain skills after `new_context` (#5382). These collectively erode trust in long-running and programmatic workflows.
- **Non-interactive/automation breakage:** Prompt mode hangs on extension tool permissions and can't pre-authorize them via `--allow-tool` (#5381) — a blocker for CI and scripted use.
- **Permission-prompt fatigue on Windows:** Over-eager PowerShell approval parsing forces full-script review for trivial git commands (#5379).
- **Silent failures in enterprise setups:** MCP servers dropped without clear errors on GHEC data residency (#4378).
- **Artifact placement confusion:** Deliverables stranded outside the Git worktree (#5384), risking lost or untracked outputs.

---

*Digest generated from 14 issues and 0 PRs updated in the last 24h. No releases or discussions available for this period.*

</details>

<details>
<summary><strong>OpenCode</strong> — <a href="https://github.com/anomalyco/opencode">anomalyco/opencode</a></summary>

# OpenCode Community Digest — 2026-11-09

## Today's Highlights

No new releases landed in the last 24 hours, but the issue tracker is dominated by two themes: a massive free-tier outage thread (#59387, now at 333 comments) that's become the de facto bug-report hub for quota problems, and a wave of V2 desktop/TUI regressions around tool permissions, subagent lifecycle, and provider routing. On the PR side, maintenance includes legal/privacy doc work for hosted agents and token-usage reporting to OpenCode Console, plus new provider support (Venice E2EE) and a snapshot fix for embedded repos.

---

## Releases

No new releases in the last 24h.

---

## Hot Issues

1. **[#59387](https://github.com/anomalyco/opencode/issues/59387) — [zen] Free tier issues: read this first (333 comments, 👍25)** — The central free-tier trouble thread. Users are hitting "Free usage exceeded" / "limit reached" errors where the daily reset never actually occurs, plus a V1→V2 Desktop compatibility error. The volume signals a persistent, systemic quota/reset bug rather than isolated reports.

2. **[#62761](https://github.com/anomalyco/opencode/issues/62761) — `DELETE /auth/{id}` leaves `GET /provider` stale (severity: high)** — `opencode serve` deletes credentials on disk but continues reporting the provider as connected until restart, and returns `200 true` for unknown IDs. A real server-state correctness bug affecting headless/HTTP integrations.

3. **[#62708](https://github.com/anomalyco/opencode/issues/62708) — Second openai-compatible provider routes to first provider's baseURL** — On Windows, a second `@opencode/ai/providers/openai-compatible` provider silently sends requests to the wrong baseURL. Multi-provider setups are effectively broken for this package.

4. **[#48826](https://github.com/anomalyco/opencode/issues/48826) — V2 subagent with pending background work marked "completed" early** — Subagents launching background shells or nested subagents terminate their turn prematurely and report completion before results are collected. A correctness issue for anyone building on V2 background execution.

5. **[#62213](https://github.com/anomalyco/opencode/issues/62213) — Built-in tester policy overrides agent write allowances (last-match-wins)** — `opencode run --agent` denies `write`/`edit` on non-test paths because an injected `edit * deny` wins under last-match-wins ordering. Confusing, hard-to-debug permission behavior for custom subagents.

6. **[#23655](https://github.com/anomalyco/opencode/issues/23655) — Add Responses API support for Go service (👍31)** — The highest-upvoted open feature request in this batch. Users want the OpenAI Responses API surfaced through the Go service; strong sustained interest.

7. **[#62556](https://github.com/anomalyco/opencode/issues/62556) — Gemini `cachedContent` fails every request** — Setting `cachedContent` together with `systemInstruction`/`tools` gets a 400 from Gemini/Vertex. Breaks Gemini prompt caching entirely for affected configs.

8. **[#62619](https://github.com/anomalyco/opencode/issues/62619) — Google content-filter finish silently retried ~24× every 10s (v1.16.2)** — Deterministic content-filter failures enter a silent infinite retry loop with no error log, no message record, no backoff. Wastes quota and hides the real failure.

9. **[#62778](https://github.com/anomalyco/opencode/issues/62778) — Automatic compaction aborts active turn with "Nothing to compact yet"** — On local providers (Ollama) at the context limit, auto-compaction can fail and kill the whole turn. A blocker for local-model users on long sessions.

10. **[#62765](https://github.com/anomalyco/opencode/issues/62765) — No usable local models (qwen3, qwen3-coder, llama3.1)** — User reports local models only read/write files and "solve absolutely NOTHING." Reflects ongoing frustration with tool-use quality for small local models; closed as triaging.

*Also worth tracking:* [#62644](https://github.com/anomalyco/opencode/issues/62644) (Edit tool consistently failing across LLMs), [#62666](https://github.com/anomalyco/opencode/issues/62666) (TUI plugin solid-js signals don't re-render npm-package plugins), [#55529](https://github.com/anomalyco/opencode/issues/55529) (codemode re-injects ~8.8k-char tool catalog on every tree change).

---

## Key PR Progress

1. **[#62788](https://github.com/anomalyco/opencode/pull/62788) — feat(core): report subscription provider token usage to OpenCode Console** — Reports subscription token usage to Console; companion to opencode-console#6356. Enables usage visibility for subscription users.

2. **[#62786](https://github.com/anomalyco/opencode/pull/62786) — feat(ai): support Venice end-to-end encrypted models** — Adds Venice `e2ee-*` model support. Expands privacy-focused provider options.

3. **[#62785](https://github.com/anomalyco/opencode/pull/62785) — fix(core): capture snapshots when embedded repo status fails** — Fixes #61383; handles untracked nested repos recorded as gitlinks so snapshot capture no longer fails on `git diff-files`.

4. **[#62790](https://github.com/anomalyco/opencode/pull/62790) — fix(site): point legal history's hosted agents entry at dev's commit** — Regenerates legal history so the hosted-agents entry references the correct squash commit. Docs housekeeping.

5. **[#62789](https://github.com/anomalyco/opencode/pull/62789) — docs(legal): cover hosted agent sessions and retention** — Updates privacy policy for hosted agents (OpenCode Console). Relevant to anyone using hosted sessions.

6. **[#62784](https://github.com/anomalyco/opencode/pull/62784) — docs(console): document Watches** — Adds `console/watches.mdx` customer docs; explicitly held until Watches opens to customers. Signals an upcoming Console feature.

7. **[#53975](https://github.com/anomalyco/opencode/pull/53975) — fix(core): forward unrecognized OpenAI-compatible model settings as body fields** — Swaps AI SDK's `openai-compatible` for native package so unknown model settings pass through. Fixes #53677; related to the routing bug in #62708.

8. **[#53927](https://github.com/anomalyco/opencode/pull/53927) — feat(core): add prompt cache rules** — Adds a top-level `cache` config (refs #51109). Directly addresses prompt-cache control requests.

9. **[#53973](https://github.com/anomalyco/opencode/pull/53973) — fix(app): avoid booting historical locations for passive attention** — Prevents restored inactive tabs and reconnect sweeps from starting cold Location graphs. Fixes #44272; perf-oriented.

10. **[#53932](https://github.com/anomalyco/opencode/pull/53932) — fix(core): reload symlinked config targets** — Config watches now resolve symlink targets outside the link's directory. Fixes #53920.

*Note:* Several older PRs (#53906, #53913, #53925, #53929, #53970, #53997, #54011) were moved to CLOSED under `automated-pr-cleanup` in this window — likely stale-PR housekeeping rather than rejection of the underlying fixes.

---

## Hot Discussions

No Discussions data was provided for this period.

---

## Feature Request Trends

- **Model/provider breadth:** Responses API support for the Go service (#23655, highest upvotes), plus provider-specific caching fixes (Gemini `cachedContent` #62556) and Venice E2EE support (#62786).
- **Config ergonomics:** Splitting `opencode.jsonc` across multiple files (#19353) for large MCP/agent setups; symlinked config reload (#53932); prompt cache rules (#53927).
- **TUI/Desktop UX polish:** Preserve model when switching plan/build (#54375), more compact composer (#52339), reorderable session tabs (#62646), which-key panel that actually works (#62650).
- **Plugin/agent extensibility:** Per-agent tool visibility filtering (#24953), correct plugin plan projection during replay (#53929), TUI plugin signal reactivity (#62666).
- **Local-first workflows:** Keeping configured local models available without discovery (#54011), fixing auto-compaction on local providers (#62778).

---

## Developer Pain Points

1. **Free-tier quota/reset is broken and loud.** #59387 has ballooned to 333 comments, with variants in #58687 and #62658 (Russian-language report of a permanently exhausted limit since 2026-10-31). The reset-never-happens behavior is the single highest-volume complaint.
2. **Multi-provider and auth state are unreliable.** Stale `GET /provider` after `DELETE /auth` (#62761) and cross-wired openai-compatible baseURLs on Windows (#62708) make provider configuration fragile.
3. **V2 subagent/tool permission semantics are confusing.** Early subagent completion (#48826) and last-match-wins edit denial (#62213) create behavior that's hard to reason about from config alone.
4. **Silent failures hide real errors.** Content-filter retry loops with no logs (#62619), Edit-tool failures (#62644), and compaction aborting turns (#62778) all lack actionable diagnostics.
5. **Small local models underperform on agentic tasks.** #62765 captures a recurring sentiment that qwen/llama-class local models can read/write files but don't meaningfully solve tasks — driving demand for better tool-use reliability and model guidance.

</details>

<details>
<summary><strong>Pi</strong> — <a href="https://github.com/earendil-works/pi">earendil-works/pi</a></summary>

# Pi Community Digest — 2026-11-09

## 1. Today's Highlights

A surge of triaged issues landed on 2026-11-08, headlined by a cluster of native MCP problems in Pi 1.5.0 — dropped OAuth headers, sequential reconnection timeouts, and `tool_search` failing to resolve direct-exposed tools. Provider compatibility also drew attention: OpenRouter's `native_finish_reason` is being discarded, and Mistral's array-style `delta.content` renders as `[object Object]`. A notable meta-issue (#11927) calls out the repo's practice of auto-closing bug reports as "not planned."

## 2. Releases

No new releases in the last 24 hours.

## 3. Hot Issues

1. **[#10287](https://github.com/earendil-works/pi/issues/10287) — `getContextUsage()` massively overestimates context after retryable network error** (OPEN, 👍2)
   Token accounting jumps from ~42k to 330k after a WebSocket/fetch failure, which likely breaks auto-compaction decisions. High-severity because long-running agents depend on accurate context accounting.

2. **[#11918](https://github.com/earendil-works/pi/issues/11918) — MCP tool calls return empty results despite valid OAuth token**
   The session/auth header isn't carried after sign-in for Google Workspace MCP servers. Includes detailed field evidence across three servers — a strong repro for a high-impact auth regression in 1.5.0.

3. **[#11919](https://github.com/earendil-works/pi/issues/11919) — MCP reconnection blocks 2–4 minutes on unauthenticated servers**
   Re-auth of one server triggers sequential re-handshakes of all servers, each stalling until timeout. Multi-server MCP users lose minutes per session.

4. **[#11921](https://github.com/earendil-works/pi/issues/11921) — `tool_search` answers "No matching tools found" for direct MCP tools**
   Direct-exposed servers are never named in the system prompt (`<tools>` only lists tools with a `promptSnippet`), so search can't find them. A subtle discovery bug with visibility into tool exposure semantics.

5. **[#10162](https://github.com/earendil-works/pi/issues/10162) — Too many input images stop the agent task** (OPEN, 👍0)
   Undermines the indefinite-run/"baby-sitting" use case now that compaction exists. Long-running agents are a core differentiator, so this is a notable reliability gap.

6. **[#11926](https://github.com/earendil-works/pi/issues/11926) — Transient states treated as permanent errors**
   A 429 is rendered red and permanently logged to the session, conflating display with retry logic. Quality-of-life for users behind rate-limited local backends.

7. **[#11931](https://github.com/earendil-works/pi/issues/11931) — `openai-completions` drops OpenRouter `native_finish_reason`**
   A Gemini `MALFORMED_FUNCTION_CALL` is hidden as a generic `finish_reason: error`, removing the actionable diagnostic. Loss of observability for provider-level failures.

8. **[#11916](https://github.com/earendil-works/pi/issues/11916) — Mistral GLM arrays render as `[object Object]` spam**
   Parser stringifies array-style `delta.content`. A visible provider-compat break for Mistral custom-provider users.

9. **[#11932](https://github.com/earendil-works/pi/issues/11932) — Extension hook for auto-retry decisions** (OPEN)
   Requests an `auto_retry_decision` event so extensions can set backoff or stop retries. Part of a broader trend of requesting finer-grained extension hooks.

10. **[#11927](https://github.com/earendil-works/pi/issues/11927) — Bug reports shouldn't be auto-closed as "not planned"**
    A process complaint with community resonance: all newly filed bugs from non-contributors are auto-closed as "not planned," which skews triage visibility. Worth watching as a governance signal.

Bonus: **[#4918](https://github.com/earendil-works/pi/issues/4918) — Shift+Enter doesn't add newline** (👍4) was closed, with a fix PR landed (see below).

## 4. Key PR Progress

1. **[#11934](https://github.com/earendil-works/pi/pull/11934) — `tui: decode mintty LF as Shift+Enter`** (CLOSED)
   Fixes Shift+Enter in Cygwin/MSYS2 mintty so fullscreen search can navigate to previous matches. Paired with issue #11935.

2. **[#11857](https://github.com/earendil-works/pi/pull/11857) — `feat: prompt cache warming in p2`** (CLOSED)
   Addresses the 5-minute Anthropic prompt cache going stale; author reports ~$1,430 extra spend (26% of p2 spend) across 789 rewrites in ~3 weeks. Significant cost optimization.

3. **[#11920](https://github.com/earendil-works/pi/pull/11920) — `fix(codemode): let scripts declare their own tools and console bindings`** (CLOSED)
   Fixes a `SyntaxError: invalid redefinition of parameter name` when codemode scripts declare top-level `tools`/`console`. Fixes #11226.

4. **[#11900](https://github.com/earendil-works/pi/pull/11900) — `docs: fix RFC link in CONTRIBUTING.md`** (CLOSED)
   Trivial but useful: trailing slash in the RFC link 404'd, fixed for contributors.

Note: only four PRs were updated in the window, all now closed. No long-running feature PRs were active, suggesting a maintenance-heavy day.

## 5. Hot Discussions

**Show and tell**

- **[#11930](https://github.com/earendil-works/pi/discussions/11930) — Assistant UI adapter for Pi Durable** (👍1)
  A React runtime hook connecting a Durable host to Assistant UI components, with transcript rendering, streaming updates, and thread navigation. Signals growing ecosystem interest in custom frontends.
- **[#11922](https://github.com/earendil-works/pi/discussions/11922) — omp-web: self-hosted browser workspace for Pi** (👍1)
  A web workspace for agent CLIs (Pi, Oh My Pi, Claude Code, Codex) usable from laptop or phone against an always-on Linux box. Reflects demand for remote/mobile access to Pi sessions.

## 6. Feature Request Trends

- **Finer-grained extension hooks**: `auto_retry_decision` (#11932), `ui_prompt_start` carrying `blocked` for `ui.custom` (#11924), and publishing MCP connection status for custom footers (#10871). Extension authors increasingly want event-level control rather than UI-level workarounds.
- **Context/session ergonomics**: a `/clear` that preserves model and thinking level (#11925) and per-model thinking-level selection in the model picker (#11929) both point to tuning friction in routine workflows.
- **Conditional instruction files**: referencing `AGENTS.md` and siblings (`AGENTS_JAVA.md`, `AGENTS_INFRA.md`) based on task context (#11933), addressing instruction-file bloat.
- **Richer multimodal input**: video/audio attachments (#2809), tracking parity with Gemini 3's capabilities.
- **Monorepo-friendly installs**: subpath support for `pi install git:` (#4851), important for extension ecosystems living in monorepos.

## 7. Developer Pain Points

- **MCP reliability in 1.5.0** is the dominant theme: auth state not persisted across sign-in (#11918), sequential blocking reconnects (#11919), and tool visibility mismatches between direct/deferred exposure (#11921). This cluster suggests the native MCP layer needs hardening before wider adoption.
- **Retry semantics bleed into UX**: transient errors are shown as permanent failures (#11926), while context usage is corrupted precisely after retryable network errors (#10287). Retry handling appears to touch more subsystems than it should.
- **Provider compatibility debt** in `openai-completions`: dropped `native_finish_reason` from OpenRouter (#11931) and unhandled array `delta.content` from Mistral (#11916). The OpenAI-compatible parser is a recurring source of breakage and lost diagnostics.
- **Terminal/input edge cases**: Shift+Enter newline (#4918), mintty key decoding (#11935/#11934), and backspace corrupting paste markers (#11923) show TUI input handling as a steady source of paper cuts across platforms.
- **Process friction**: auto-closing community bug reports as "not planned" (#11927) erodes contributor trust and hides legitimate issues from triage — worth a maintainer response.

</details>

<details>
<summary><strong>Qwen Code</strong> — <a href="https://github.com/QwenLM/qwen-code">QwenLM/qwen-code</a></summary>

# Qwen Code Community Digest — 2026-11-09

## 1. Today's Highlights

Release activity is heavy: a new nightly, a TypeScript SDK bump bundling CLI 0.27.0, and the first **Qwen Code Desktop v0.27.0** build all landed within 24 hours. The issue tracker remains dominated by CI infrastructure noise — the persistent "Main CI failed" bot issues (#14724 at 277 comments, #11680, #11678) continue to accumulate, while several genuine P1/P2 bugs (Windows desktop updates, GitHub channel cursor races) were closed. On the PR side, contributors are pushing substantial managed-agent features (archive + MCP self-service, shared workspaces, Mem0-backed memory) alongside a wave of small channel/MCP/ACP correctness fixes.

## 2. Releases

**v0.26.9-nightly.20261107.65e1f1aeaa** — Nightly build; includes provider documentation for Wally under OpenAI-compatible providers and core fixes.

**sdk-typescript-v0.1.30** — TypeScript SDK release bundling CLI **0.27.0** (built from source on the matching branch/ref).

**desktop-v0.27.0** — Desktop client release. Notable changes include a fix so the GitHub inbound channel keeps its recovery mechanism armed after a failed read (#16402) and hardened core renewal tests.

> Note: Desktop v0.27.0 ships alongside the unresolved Windows updater bug #15276 (closed but flagged ready-for-human) — users on Windows should watch for the `node.exe` file-lock error during in-app updates.

## 3. Hot Issues

1. **[#14724](https://github.com/QwenLM/qwen-code/issues/14724)** — *Main CI failed: Managed Agent gate — no test identified* (277 comments). The highest-traffic issue in the repo; a long-running bot-tracked CI failure with no identified test. Symptomatic of CI triage debt rather than a product bug.
2. **[#13078](https://github.com/QwenLM/qwen-code/issues/13078)** — *Daily dependency CVE audit failed* (45 comments). Recurring supply-chain audit failure, possibly a stale npm audit endpoint or a genuine new high-severity advisory. Security-relevant and long-lived.
3. **[#16417](https://github.com/QwenLM/qwen-code/issues/16417)** — *Reject an oversized `write_file` before generating it* (P2, token-management). Model generates an entire response, then it's discarded with `Response truncated due to token limit` — no pre-flight check against the output budget. Real workflow pain, reproducible.
4. **[#16227](https://github.com/QwenLM/qwen-code/issues/16227)** — *GitHub channel can silently skip a comment at the poll-cursor boundary second* (CLOSED, P2). Subtle race causing permanently missed user comments; closed, which is good news for channel reliability.
5. **[#15276](https://github.com/QwenLM/qwen-code/issues/15276)** — *Windows desktop update fails: bundled `node.exe` locked* (CLOSED, P1, ready-for-human). High-impact upgrade blocker for desktop users on Windows.
6. **[#12113](https://github.com/QwenLM/qwen-code/issues/12113)** — *ACP reports `end_turn` after repeated `finish_reason=length`* (P2). IDE clients are told the turn ended cleanly when output was actually truncated — an integration correctness issue affecting JetBrains/ACP consumers.
7. **[#12160](https://github.com/QwenLM/qwen-code/issues/12160)** — *MCP tools stay disconnected after an aborted turn* (P2). Abort path skips session repair in `mcp-tool.ts`, leaving HTTP MCP servers permanently `DISCONNECTED`. Directly addressed by PR #16661.
8. **[#16526](https://github.com/QwenLM/qwen-code/issues/16526)** — *Parallel sub-agents can oversubscribe a local model* (P3). Global concurrency limit misses foreground launches; local single-GPU users get queued agents. Increasingly relevant as sub-agent usage grows.
9. **[#16173](https://github.com/QwenLM/qwen-code/issues/16173)** — *Allow models to refer to slices of files/tool outputs* (P3, need-discussion). Targets the `read > write > typo > re-write` cycle by avoiding duplication of large content in context.
10. **[#8544](https://github.com/QwenLM/qwen-code/issues/8544)** — *ACP task list not rendered in JetBrains* (P2, long-lived). Claude Code and Codex render live todo lists in the same JetBrains ACP UI; Qwen Code does not. Competitive-parity gap for IDE users.

## 4. Key PR Progress

1. **[#16661](https://github.com/QwenLM/qwen-code/pull/16661)** — *fix(mcp): repair the MCP connection torn down by an aborted tool call.* Arms the abort path to restore the transport after `onerror` flips the server to `DISCONNECTED`. Direct fix for #12160.
2. **[#16679](https://github.com/QwenLM/qwen-code/pull/16679)** — *fix(acp): surface failed `todo_write` calls.* Emits a terminal `tool_call_update` with `status: failed` instead of dropping the frame — improves ACP transparency.
3. **[#16500](https://github.com/QwenLM/qwen-code/pull/16500)** — *feat(managed-agent): archive and MCP self-service installation.* Daemon-free ZIP/tar.gz preparation, digest-pinned inspection, encrypted Workspace settings, one-confirmation install. Major managed-agent capability.
4. **[#16023](https://github.com/QwenLM/qwen-code/pull/16023)** — *feat(managed-agent): share resident external Workspace across sessions.* Workspace-scoped bindings reused across Sessions instead of torn down per cycle. Large architectural change with Java battery fallout tracked in #16605.
5. **[#16488](https://github.com/QwenLM/qwen-code/pull/16488)** — *feat(managed-agent): opt-in personal memory backed by Mem0.* Per-tenant `MemoryStore`, immutably bound at session creation. Signals a memory roadmap for managed agents.
6. **[#11854](https://github.com/QwenLM/qwen-code/pull/11854)** — *feat: hybrid code mode.* Adds `tools.mode` enum (`direct` / `code_mode` / `code_mode_only`) with an isolated `exec` JavaScript tool — Codex-aligned tool execution model.
7. **[#15923](https://github.com/QwenLM/qwen-code/pull/15923)** — *fix(core): resolve MSYS paths in file tools on Windows.* Git Bash `/tmp` maps to the OS temp dir, but file tools resolved it literally to `<drive>:\tmp`. Fixes `read_file` false negatives.
8. **[#16439](https://github.com/QwenLM/qwen-code/pull/16439)** — *fix(core): replay the persisted startup prelude on resume.* Resumed sessions now reuse the original prelude (date, tree, git snapshot, MCP/skills) rather than rebuilding it — better resume determinism.
9. **[#16561](https://github.com/QwenLM/qwen-code/pull/16561)** — *test(ci): pin credential-bearing lanes to hosted runners.* Locks the web-shell visuals publisher (`CI_BOT_PAT`, OSS keys) and failure reporter off the shared pool. Security-hardening for CI.
10. **[#16681](https://github.com/QwenLM/qwen-code/pull/16681)** — *feat(web-shell): commit only selected files.* Checkbox file selection in the commit view; unchecked files stay in the working tree. Practical UX improvement for Web Shell users.

## 5. Hot Discussions

No Discussion data was provided in the source dataset; this section is omitted.

## 6. Feature Request Trends

- **Token/output budget management** — Pre-flight checks for oversized writes (#16417), slice references to avoid duplication (#16173), and truncation-aware ACP reporting (#12113) all point to growing friction around context/output limits.
- **Sub-agent concurrency control** — Requests to make the global sub-agent limit aware of foreground launches and local-model capacity (#16526).
- **Managed-agent platform maturation** — Archive, MCP self-service, shared resident workspaces, and personal memory (PRs #16500, #16023, #16488) form a coherent "managed agent as a product" direction.
- **IDE/ACP parity** — Live task-list rendering in JetBrains (#8544) and accurate stop reasons (#12113) are recurring integration asks.
- **UI rendering fidelity** — Rendering `exec`/code-mode tool calls as JavaScript instead of JSON (#16675) and scroll-to-bottom affordances in Split View (PR #16658).

## 7. Developer Pain Points

- **CI noise drowning the tracker** — Bot-filed "Main CI failed" issues (#14724, #11680, #11678, #16045, #16191, #16672, #16673) dominate comment volume, many marked *Provisional* and self-resolving. Maintainers and contributors face real triage fatigue.
- **Flaky/cross-platform test failures** — Repeated failures in `bun-pty` flood tests on macOS (#16091), Windows Node 22 test runs (#11678), and E2E sandbox shards (#11680, #15185). Windows and sandbox lanes are disproportionately affected.
- **Stacked-PR CI blind spots** — PR #16634 reports that PRs targeting non-`main`/`release` bases trigger no Java gate at all, so green checks on stacked PRs are not health evidence.
- **Release pipeline instability** — Failed nightly release (#16031) and a steady stream of managed-agent gate failures suggest the release/integration matrix is under strain.
- **Platform-specific correctness** — Windows path handling (MSYS, #15923), Windows Terminal rendering (DECSET 2026, #11929), and desktop updater file locks (#15276) recur as a cluster of Windows pain points.
- **Local-model ergonomics** — Oversubscription (#16526) and output-truncation handling (#16417, #12113) indicate the local/self-hosted workflow still lacks guardrails that cloud-model users don't notice.

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar).*