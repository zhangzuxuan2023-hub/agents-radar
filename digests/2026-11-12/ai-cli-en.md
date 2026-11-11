# AI CLI Tools Community Digest 2026-11-12

> Generated: 2026-11-11 23:28 UTC | Tools covered: 7

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

⚠️ Comparative analysis generation failed.

---

## Per-Tool Reports

<details>
<summary><strong>Claude Code</strong> — <a href="https://github.com/anthropics/claude-code">anthropics/claude-code</a></summary>

## Claude Code Skills Highlights

> Source: [anthropics/skills](https://github.com/anthropics/skills)

# Claude Code Skills — Community Highlights Report
*Data as of 2026-11-12 · Source: github.com/anthropics/skills*

---

## 1. Top Skills Ranking

> Note: The PR dataset exposes `Comments: undefined`, so the ranking below is ordered by issue-level discussion volume and cross-referencing to linked Issues/PRs, plus recency of activity. All listed PRs are **OPEN**.

| # | Skill / PR | Functionality | Discussion Highlights | Status |
|---|---|---|---|---|
| 1 | **[mcp-builder: mcp>=2 streamable HTTP client + custom headers](https://github.com/anthropics/skills/pull/1742)** — *Kuldeeep18* | Fixes MCP connection builder for `mcp>=2.0.0` (`streamablehttp_client` → `streamable_http_client`, headers via `create_mcp_http_client`) | Highest-velocity fix; addresses breaking SDK rename in #1668; updated 2026-10-08 | OPEN |
| 2 | **[skill-creator: isolate trigger evals + Windows/runtime failure handling](https://github.com/anthropics/skills/pull/1298)** — *MartinCajiao* | Makes trigger-eval scoring reliable: per-worker command probes, `select()` on pipes on Windows, unrelated-tool failure isolation | Directly responds to #1352 (parallel-worker false negatives) and #556 (0% trigger rate) | OPEN |
| 3 | **[Add notion-spec-to-implementation + quantitative-resume-auditor](https://github.com/anthropics/skills/pull/1245)** — *mrdesouzaphd-cmyk* | Spec→Notion task breakdown with acceptance criteria; resume scoring/auditing | Dual-skill submission; still active (updated 2026-10-15) | OPEN |
| 4 | **[webapp-testing: eliminate `shell=True` in with_server.py](https://github.com/anthropics/skills/pull/1980)** — *Pcmhacker-piro* | Replaces shell-spawn with arg-list execution to prevent CWE-78 command injection | Security hardening of the testing harness; updated 2026-11-01 | OPEN |
| 5 | **[docx: report LibreOffice timeout as error + verify no revision marks](https://github.com/anthropics/skills/pull/1792)** — *TINGyu123644* | `accept_changes.py` no longer reports false success when `soffice` times out; validates `w:ins`/`w:del` removal | Correctness fix for a flagship document skill | OPEN |
| 6 | **[proofcore-contract-auditor (smart-contract notarization)](https://github.com/anthropics/skills/pull/1771)** — *ProofCore-Protocol* | Static analysis of Solidity/Rust contracts + Merkle proofs anchored on TON | Novel Web3 domain skill; zero-storage notarization | OPEN |
| 7 | **[pyxel: retro game development skill](https://github.com/anthropics/skills/pull/525)** — *kitao* | Guides Pyxel game creation, headless input-driven runs, frame inspection, state checks | Long-running PR (since Mar 2026), still updated 2026-11-01 — sustained interest | OPEN |
| 8 | **[xlsx: worksheet-scoped defined names in external_links_at_risk](https://github.com/anthropics/skills/pull/2019)** — *reginaldalfret* | Fixes false negatives when external links are reached via worksheet-scope aliases in `recalc.py` | Fixes #2013; updated 2026-11-03 | OPEN |

---

## 2. Community Demand Trends

Distilled from Issues by comment volume and recurring signals:

- **Security & trust boundaries** — [#492 (46 comments)](https://github.com/anthropics/skills/issues/492): community skills shipped under the `anthropic/` namespace impersonate official skills; users may over-grant permissions. *The single most-discussed issue in the dataset.* Reinforced by PRs like [#1980](https://github.com/anthropics/skills/pull/1980) (command injection).
- **Skill-evaluation reliability** — [#556 (13)](https://github.com/anthropics/skills/issues/556) 0% trigger rate under `run_eval.py`; [#1352 (6)](https://github.com/anthropics/skills/issues/1352) parallel-worker UUID cross-matching. Demand: *trustworthy trigger-rate measurement* before skills can be optimized.
- **Organization-level distribution & sharing** — [#228 (17)](https://github.com/anthropics/skills/issues/228): org-wide skill libraries and share links instead of manual `.skill` file uploads. Related: [#189 (6)](https://github.com/anthropics/skills/issues/189) duplicate skills from overlapping plugins.
- **Context efficiency** — [#1487 (5)](https://github.com/anthropics/skills/issues/1487): `claude-api` eagerly injects ~156k tokens in one tool call; [#202 (8, closed)](https://github.com/anthropics/skills/issues/202): skill-creator should be operational, not tutorial-style prose.
- **Quality-gate / agent-reasoning pipelines** — [#1385 (7)](https://github.com/anthropics/skills/issues/1385): pre-task calibration → adversarial review → delivery verification.
- **Compressed agent memory** — [#1329 (10)](https://github.com/anthropics/skills/issues/1329): symbolic notation to shrink long-running agent state.
- **Governance for agent systems** — [#412 (6, closed)](https://github.com/anthropics/skills/issues/412): policy enforcement, threat detection, audit trails.

**Emerging directions:** document-skill snapshot hygiene ([#2108](https://github.com/anthropics/skills/pull/2108)), frontmatter/description quality for when-to-use guidance ([#2064](https://github.com/anthropics/skills/issues/2064)), docx/xlsx correctness fixes ([#2018](https://github.com/anthropics/skills/issues/2018), [#1868](https://github.com/anthropics/skills/issues/1868)).

---

## 3. High-Potential Pending Skills

Active-comment PRs not yet merged that are likely to land soon:

1. **[#1742 — mcp-builder mcp>=2 fix](https://github.com/anthropics/skills/pull/1742)** — resolves a hard breaking import; highest merge probability.
2. **[#1298 — skill-creator trigger-eval isolation](https://github.com/anthropics/skills/pull/1298)** — closes loop on the repo's most active bug cluster (#556, #1352).
3. **[#1980 — webapp-testing `shell=True` removal](https://github.com/anthropics/skills/pull/1980)** — security fix with small blast radius.
4. **[#2019 — xlsx external-links alias resolution](https://github.com/anthropics/skills/pull/2019)** — cleanly scoped, linked fix (#2013).
5. **[#1792 — docx timeout/verification](https://github.com/anthropics/skills/pull/1792)** — well-described correctness fix.
6. **[#1977 / #1976 / #1975 — gerardrecinto's misc fixes](https://github.com/anthropics/skills/pull/1977)** (wrapAround, textarea/select discovery, `--cols` validation) — linked to issues, small and reviewable.
7. **[#1681 — skill-creator standalone `package_skill.py` execution](https://github.com/anthropics/skills/pull/1681)** — `ModuleNotFoundError` fix with CLI-path updates.
8. **[#525 — pyxel retro game skill](https://github.com/anthropics/skills/pull/525)** — longest-lived new-skill PR with continued updates; strong domain novelty.

*Watchlist (lower activity, higher novelty):* [#1771 proofcore-contract-auditor](https://github.com/anthropics/skills/pull/1771), [#1615 scnet-hpc](https://github.com/anthropics/skills/pull/1615), [#1703 md2video-audio](https://github.com/anthropics/skills/pull/1703).

---

## 4. Skills Ecosystem Insight

**The community's most concentrated demand is trust and reliability infrastructure — secure, correctly-namespaced skills with verifiable trigger evaluation — rather than more skills: fixes to the evaluation pipeline and trust boundary outrank every net-new skill proposal in discussion volume.**

---

# Claude Code Community Digest — 2026-11-12

## 1. Today's Highlights
No new releases landed in the last 24 hours, but the issue tracker saw a heavy maintenance sweep: a large batch of stale issues (many from August–September) was closed, while a cluster of fresh OPEN bugs surfaced around the v2.1.322 model picker and VS Code plugin sync. Two high-signal new regressions — silent telemetry-gated plugin sync failures in VS Code and a `/model` picker omitting Fable 5.2 — are worth immediate triage.

## 2. Releases
No new releases in the last 24h.

## 3. Hot Issues

1. **[#45810](https://github.com/anthropics/claude-code/issues/45810) — Marketplace Update button disabled despite outdated version** (CLOSED, 18 comments, 👍8)
   The most-commented issue of the day. A UI-blocking bug in the plugin marketplace where the Update button is greyed out regardless of version state. Closed as duplicate/stale, suggesting an earlier canonical report exists — but the 8 upvotes signal real user friction with plugin updates.

2. **[#108955](https://github.com/anthropics/claude-code/issues/108955) — `/model` picker omits Fable 5.2 while the slash command works** (OPEN, 👍4)
   Max-plan users on v2.1.322 can invoke `claude-fable-5-2` directly but the picker only shows 5.1. A classic UI/model-registry desync — worth watching as it affects model discovery for power users.

3. **[#108745](https://github.com/anthropics/claude-code/issues/108745) — VS Code telemetry off silently disables claude.ai plugin sync** (OPEN, regression)
   Since extension 2.1.322, setting `telemetry.telemetryLevel: off` silently kills org-managed plugin sync — MCP servers, agents, and skills vanish with no UI message. This is a serious enterprise regression: privacy-conscious orgs lose their managed tooling invisibly.

4. **[#108839](https://github.com/anthropics/claude-code/issues/108839) — `claude --resume` attaches to Remote Control server instead of session** (OPEN, Linux)
   Sessions spawned by `claude --bg remote-control` register the server's job ID, so resume and attach both target the server. Breaks the expected background-session workflow for remote-control users.

5. **[#109036](https://github.com/anthropics/claude-code/issues/109036) — Claude in Chrome bridge requires re-auth mid-session after idle** (OPEN, Linux)
   Desktop + CLI sharing one Chrome profile lose the bridge authorization after Chrome idles or closes, presenting as a spurious sign-out. Impacts long-running desktop sessions that depend on browser context.

6. **[#109141](https://github.com/anthropics/claude-code/issues/109141) — Let statusLine share a row with mode badges** (OPEN, enhancement)
   A custom status line always occupies its own footer row, forcing a 2-row footer. Small but concrete TUI polish request with a clear ask: configurable badge hiding or row sharing.

7. **[#81814](https://github.com/anthropics/claude-code/issues/81814) — Native spellcheck in VS Code chat input** (OPEN, 👍11)
   Highest-upvoted open enhancement in this batch. Trivial in most editors but missing here; 11 upvotes show it's a daily papercut for extension users.

8. **[#81351](https://github.com/anthropics/claude-code/issues/81351) — Sonnet 5 spawned ~1000 agents, burned 13M tokens** (CLOSED, stale)
   A dramatic runaway-agent cost report. Closed as stale rather than fixed, but it's a recurring theme (see #73307 and #92602 below) that deserves a first-class guardrail.

9. **[#73307](https://github.com/anthropics/claude-code/issues/73307) — Request: `doom_loop`-style permission gate for runaway agents** (CLOSED, stale)
   Proposes an OpenCode-inspired permission key to halt subagent recursion and tool-call storms. Closed stale, yet paired with #81351 it's the clearest articulation of a missing safety primitive.

10. **[#92573](https://github.com/anthropics/claude-code/issues/92573) — Worktree slot reused/reset mid-session, ~5,900 files lost** (CLOSED, data-loss, has repro)
    The most severe closed issue in the batch: `.claude/worktrees/*` pool reuse caused mass file deletion while a session was active. Even closed-stale, it's a data-loss class bug worth tracking for recurrence.

## 4. Key PR Progress

Only one PR updated in the window:

1. **[#26230](https://github.com/anthropics/claude-code/pull/26230) — Feature/path validation hooks** (CLOSED)
   Blocks relative paths from agent tool calls to strengthen sandboxing. Closed after a long (Feb → Nov) lifecycle. If the approach lands in another form, it's directly relevant to the sandbox and agent-isolation issues (#92573, #92609) raised elsewhere today. No other PRs to report.

## 5. Hot Discussions
No discussion data was provided for this window.

## 6. Feature Request Trends

- **Agent guardrails & cost safety**: `doom_loop`-style permission gates (#73307), runaway agent caps (#81351), and loop detection (#92602). The community wants first-class protection against unbounded subagent recursion and token burn.
- **Plugin/marketplace reliability**: update-button interactivity (#45810) and silent sync failures (#90329, #108745) point to a need for visible sync state and honest error reporting.
- **Sandbox granularity**: requests to decouple filesystem isolation from network isolation for JVM projects (#92609) and path validation hooks (PR #26230) show demand for finer-grained, secure-by-default sandboxing.
- **TUI/IDE ergonomics**: spellcheck (#81814) and statusLine row sharing (#109141) — small, high-frequency UX papercuts.
- **Observability of context**: documentation request for `/context` behavior in VS Code (#92607) underlines a broader ask for transparent context/usage reporting.

## 7. Developer Pain Points

- **Silent failures are the dominant frustration.** Plugin sync (#90329, #108745), artifact comments (#92618), and stream transcript drops (#90802) all complete "successfully" in the UI while nothing happens underneath. Users repeatedly ask for visible error state.
- **Agent runaway and cost.** Multiple reports of agents looping, over-spawning, or acting on wrong assumptions (#81351, #73307, #92602, #92575) — the highest-severity reliability theme.
- **Worktree/isolation data loss.** Worktree pool reuse (#92573) and origin/HEAD-based bases (#77671) erode trust in parallel sub-agent workflows.
- **Auth/session state confusion.** Chrome bridge re-auth (#109036) and Remote Control resume attaching to the wrong target (#108839) make session identity unpredictable.
- **Stale-closing without resolution.** A large share of today's closures are stale-tagged, including data-loss and cost bugs. This risks recurring reports and signals to developers that high-impact issues aren't being triaged.

---
*Note: The tracker showed "Total: 50 items; showing top 30" — the digest reflects the visible sample. No release or discussion data was available this window.*

</details>

<details>
<summary><strong>OpenAI Codex</strong> — <a href="https://github.com/openai/codex">openai/codex</a></summary>

# OpenAI Codex Community Digest — 2026-11-12

## 1. Today's Highlights

Codex shipped a stable patch (`rust-v0.167.2`) making incremental tool declarations the default on Google Cloud Vertex AI, with a config opt-out, while the 0.169/0.170 alpha line continues its rapid cadence. Community attention is dominated by a high-engagement macOS performance regression in Codex Desktop (#25719, 398 👍) affecting `syspolicyd`/`trustd`, alongside a dense cluster of Windows sandbox and state-corruption reports. The PR queue shows steady hardening work: MCP OAuth write safety, WebSocket message-size handling, Unicode-aware thread search, and Windows atomic-write fixes.

---

## 2. Releases

**`rust-v0.167.2`** — Stable patch.
- Codex now sends tool updates **incrementally by default on Google Cloud Vertex AI**. To opt out, set `incremental_tools_legacy_gcp = false` under `[features]` in `config.toml`. ([#60617](https://github.com/openai/codex/pull/60617), [#60848](https://github.com/openai/codex/pull/60848))

**Alpha channel** — `rust-v0.170.0-alpha.1`, `rust-v0.169.0-alpha.11.1`, `rust-v0.169.0-alpha.11`, `rust-v0.169.0-alpha.10`, `rust-v0.167.0-alpha.4.2` — routine incrementals with no user-facing notes.

---

## 3. Hot Issues

1. **[#25719](https://github.com/openai/codex/issues/25719)** — *macOS Desktop triggers `syspolicyd`/`trustd` CPU/memory runaway.* The most-upvoted open issue (398 👍, 92 comments) and the clearest signal of a systemic macOS desktop performance problem; it has stayed active for over five months with no resolution.
2. **[#58447](https://github.com/openai/codex/issues/58447)** — *macOS `dot` thread read returns `DesktopThreadReadUnavailableError`.* Breaks the documented `cloud_threads.read` route even with correct `threadId`/`environmentId`; blocks cross-surface thread access.
3. **[#52713](https://github.com/openai/codex/issues/52713)** — *Windows corrupted global state resets projects and machines.* Null-byte JSON parse failures on load cause silent data loss and overwrite fallback state — a durability concern for Windows users.
4. **[#43194](https://github.com/openai/codex/issues/43194)** — *Experimental context management: native notes/history 404 on Pro + Astra.* `new_context` can discard task state when history endpoints fail, a serious correctness risk for opt-in context management.
5. **[#60867](https://github.com/openai/codex/issues/60867)** — *Windows CLI 0.167.1 `exec_command` CreateProcess failure with clean sandbox preflight.* Reproducible on a GitHub Actions `windows-2022` runner with an inert model fixture — a strong, cleanly isolated sandbox/exec regression signal.
6. **[#60648](https://github.com/openai/codex/issues/60648)** — *Windows `dot` PC access regression: sandbox backend rejects Unrestricted/Enabled/Disabled.* Previously working remote Windows workflows now fail before command execution.
7. **[#58312](https://github.com/openai/codex/issues/58312)** — *Plus five-hour quota depletes ~5.6x faster in October 2026.* Requests transparency on rate-limit accounting changes; relevant to plan sizing and agentic usage patterns.
8. **[#45367](https://github.com/openai/codex/issues/45367)** — *`threads.title`, `.preview`, `.first_user_message` each store full first message (3×, up to 1 MB).* Storage-bloat bug in `state_5.sqlite` with direct cost/performance implications for the app-server.
9. **[#60212](https://github.com/openai/codex/issues/60212)** — *Desktop project thread list cannot shrink back after "Show more".* Small but widely felt UI regression on Pro accounts.
10. **[#60868](https://github.com/openai/codex/issues/60868)** — *Long conversation repeatedly interrupts compaction; 3m16s to answer a greeting.* Illustrates lingering context/compaction instability on GPT-6.1 Sol Medium.

*Other notable items:* [#22085](https://github.com/openai/codex/issues/22085) (Windows Git process CPU storm, now CLOSED), [#60894](https://github.com/openai/codex/issues/60894) (agent-safety forensic report: repair continued without verified rollback before `0xC000021A`), and a batch of precise CLI edge-case reports from `dovvnloading` ([#60876](https://github.com/openai/codex/issues/60876) `apply_patch` missing blank context line, [#60900](https://github.com/openai/codex/issues/60900) marketplace overwrite, [#60896](https://github.com/openai/codex/issues/60896) `#` in paths parsed as Git refs).

---

## 4. Key PR Progress

1. **[#60849](https://github.com/openai/codex/pull/60849)** — *Protect MCP OAuth persistence from stale credential writes.* Locks and revalidates stored credentials so pending refreshes can't overwrite newer logins or resurrect credentials after logout.
2. **[#60872](https://github.com/openai/codex/pull/60872)** — *Fix Windows atomic writes and refresh build maintenance.* Switches to `std::fs::rename` for atomic replacement supporting overlapping readers on Windows — directly relevant to the state-corruption reports.
3. **[#60841](https://github.com/openai/codex/pull/60841)** /**[#60840](https://github.com/openai/codex/pull/60840)** — *Honor advertised WebSocket message-size limits; fall back to HTTP immediately for oversized requests.* Reads `x-codex-websocket-max-message-bytes` at handshake and classifies close code `1009` to avoid futile WebSocket retries.
4. **[#60848](https://github.com/openai/codex/pull/60848)** — *Enable incremental tool declarations by default for GCP.* The PR behind the 0.167.2 release note; decouples GCP behavior from Amazon Bedrock via `incremental_tools_legacy_gcp`.
5. **[#60759](https://github.com/openai/codex/pull/60759)** — *Fix Unicode caseless matching in thread search.* Adds Unicode default case folding (Greek sigma variants, `ß`↔`ss`) to `thread/searchOccurrences`.
6. **[#60845](https://github.com/openai/codex/pull/60845)** — *Deduplicate file search results from overlapping roots.* Prevents repeated/nested roots from consuming result slots and inflating match counts.
7. **[#60842](https://github.com/openai/codex/pull/60842)** — *Make TUI thread titles resilient to generation failures.* Immediate placeholder titles plus keeping hidden title-generation requests out of foreground conversation.
8. **[#60763](https://github.com/openai/codex/pull/60763)** — *Hand off approved plans through attachments in fresh sessions.* "Yes, clear context and implement" now saves the plan as an app-server attachment; adds experimental `thread/prepareApprovedPlan`.
9. **[#60770](https://github.com/openai/codex/pull/60770)** — *Derive history and notes tool schemas from typed requests.* Replaces handwritten JSON schemas with `serde`/`schemars` Rust types — reduces schema drift risk.
10. **[#60839](https://github.com/openai/codex/pull/60839)** — *Preserve retained assistant revisions across rollout replay.* Captures and persists retained sources so replays no longer allocate new revisions.

*Also worth tracking:* [#60871](https://github.com/openai/codex/pull/60871) (initialization handshake race), [#60784](https://github.com/openai/codex/pull/60784) (centralized Guardian reviewer selection), [#60771](https://github.com/openai/codex/pull/60771) (model-facing ID allocation ordinals).

---

## 5. Hot Discussions

**Q&A**
- **[#24717](https://github.com/openai/codex/discussions/24717)** — *Recommended way to handle outdated or incorrect memories.* Asks for clear/reset/inspect/selective-delete controls for the memory feature; still open after six months, indicating a persistent gap.
- **[#34373](https://github.com/openai/codex/discussions/34373)** — *[ANSWERED] How to uninstall Codex CLI?* (1 comment) — a reminder that the curl-based installer lacks an obvious uninstall path, especially on Arch Linux.

**Show and tell**
- **[#60861](https://github.com/openai/codex/discussions/60861)** — `claude-bulkhead`: guard hooks for co-existing agent sessions (e.g., preventing one agent's `pkill node` from killing another's server).
- **[#60834](https://github.com/openai/codex/discussions/60834)** — `Concord`: open-source MCP server for multi-agent coordination — shared presence, file claims, handoffs.
- **[#60852](https://github.com/openai/codex/discussions/60852)** — Local Windows plugin to drive an RDP test machine (screenshots + input) from Codex.
- **[#60818](https://github.com/openai/codex/discussions/60818)** — Lessons running 5–10 Codex/Claude Code agents on Windows in one terminal (from the author of Lichen).
- **[#60762](https://github.com/openai/codex/discussions/60762)** — `AGENTS.md Check`: MIT-licensed offline linter for instruction files (conflict markers, bidi controls, invalid UTF-8).
- **[#60722](https://github.com/openai/codex/discussions/60722)** — `TreeHarbor`: retains agent patches after workspace cleanup and opens per-patch draft PRs.

---

## 6. Feature Request Trends

- **Memory/context lifecycle controls** — Clear requests for inspectable, deletable memory (#24717) and trustworthy context-management endpoints (#43194, #60868). Users want context to be a first-class, auditable resource.
- **Multi-agent coordination primitives** — Multiple discussions (#60861, #60834, #60818, #60722) converge on the same need: file claims, workspace isolation, shared task state, and patch retention when several agents share one machine or repo.
- **Desktop UI transparency** — Restore always-visible branch names/selection (#58814), expose the effective image model and selector (#43965), and clearer feedback/thread-list behavior (#60212, #36889).
- **Cross-surface remote control** — Android↔desktop↔headless-Linux pairing and remote task control remain a recurring ask (#60348, #60310, #60648).
- **Quota transparency** — Explicit accounting for rate-limit consumption changes (#58312).

---

## 7. Developer Pain Points

- **Windows reliability is the dominant theme**: sandbox backend rejections (#60648), `exec_command` CreateProcess failures (#60867), global-state corruption after shutdowns (#52713), orphaned worktree wrappers (#59934), and disappeared Goals (#59366).
- **macOS desktop resource behavior**: the long-running `syspolicyd`/`trustd` runaway (#25719) and browser extension activating Brave in the background (#48173) point to insufficient sandboxing of OS integrations.
- **CLI edge-case correctness**: `apply_patch` silently accepting mismatched hunks (#60876), marketplace path parsing (#60896, #60900), profile-scoped MCP removal hints (#60893), and SDK documentation gaps (#60895, #60892, #60899) — a consistent contributor (dovvnloading) is filing high-quality, low-noise bugs that suggest test coverage gaps.
- **Context and compaction instability**: 404s on context endpoints plus compaction interrupting long sessions erode trust in long-running agentic workflows.
- **Remote pairing friction**: manual Remote pairing fails post-authorization across Android/headless Linux (#60348) and Windows→Android (#60310), with version skew between CLI and daemon (0.166.0 vs 0.167.0) cited as a suspect.
- **Agent safety expectations**: the Windows system-repair forensic report (#60894) and auto-review denial without approval control (#60277) show users want verifiable rollback and override paths for high-impact actions.

---

*Digest generated from github.com/openai/codex data as of 2026-11-12. PR descriptions are based on submitted summaries; several listed PRs are marked CLOSED and may have been merged or superseded.*

</details>

<details>
<summary><strong>Gemini CLI</strong> — <a href="https://github.com/google-gemini/gemini-cli">google-gemini/gemini-cli</a></summary>

# Gemini CLI Community Digest — 2026-11-12

## 1. Today's Highlights

No new releases landed in the last 24 hours, but activity remains concentrated on agent reliability: subagent termination semantics, browser agent robustness, and Windows/ACP parity fixes dominate the queue. A notable cluster of PRs from a single contributor (`dongs0104`) addresses binary injection, ACP tool truncation, and parallel tool execution — suggesting a focused push on making the ACP/non-interactive path match the interactive CLI. Auth flow (clickable wrapped URLs) and PowerShell event-loop blocking are also under active repair.

## 2. Releases

No new releases in the last 24 hours.

## 3. Hot Issues

1. **[#22323](https://github.com/google-gemini/gemini-cli/issues/22323)** — *Subagent MAX_TURNS reported as GOAL success* (P1, 13 comments). The `codebase_investigator` subagent claims `status: "success"` / `Termination Reason: "GOAL"` even after exhausting its turn budget before doing any analysis. Highest-comment issue today; a semantic correctness problem that undermines any automation relying on subagent status.

2. **[#21409](https://github.com/google-gemini/gemini-cli/issues/21409)** — *Generalist agent hangs* (P1, 8 comments, 8 👍). The generalist subagent hangs indefinitely on trivial tasks (e.g., folder creation); user reports waiting up to an hour. Highest-reaction issue today — strong signal of broad impact.

3. **[#19873](https://github.com/google-gemini/gemini-cli/issues/19873)** — *Zero-dependency OS sandboxing & post-execution intent routing* (P2, 9 comments). Proposes letting Gemini 3 lean on its native bash affinity (`grep`, `sed`, `awk`) while containing risk through sandboxing. A design-direction issue worth watching.

4. **[#22745](https://github.com/google-gemini/gemini-cli/issues/22745)** — *AST-aware file reads, search, and mapping* (P2, 7 comments). EPIC investigating whether syntax-aware tooling reduces wasted turns and token noise. Directly tied to efficiency and context-bloat concerns.

5. **[#21968](https://github.com/google-gemini/gemini-cli/issues/21968)** — *Gemini doesn't use skills/sub-agents enough* (P2, 7 comments). Anecdotal but widely resonant: custom skills and subagents go unused unless explicitly requested. Points to a tool-selection/steering gap.

6. **[#22267](https://github.com/google-gemini/gemini-cli/issues/22267)** — *Browser Agent ignores `settings.json` overrides* (P2, 5 comments). Registry reads settings correctly but they never reach the agent — a config-plumbing bug that makes browser agent tuning unreliable.

7. **[#21983](https://github.com/google-gemini/gemini-cli/issues/21983)** — *Browser subagent fails on Wayland* (P1, 4 comments). Linux desktop users hit browser agent failure; notable platform-coverage gap for a growing user segment.

8. **[#24246](https://github.com/google-gemini/gemini-cli/issues/24246)** — *400 error with >128 tools* (P2, 3 comments). Tool-scoping doesn't degrade gracefully as enabled tools grow. Matters for anyone stacking extensions/MCP servers.

9. **[#22672](https://github.com/google-gemini/gemini-cli/issues/22672)** — *Agent should discourage destructive behavior* (P2, 3 comments). Model occasionally reaches for `git reset`/`--force` when safer paths exist. Safety-relevant and likely to shape policy work.

10. **[#18836](https://github.com/google-gemini/gemini-cli/issues/18836)** — *Replace WriteToDo with persistent file-based task tracking* (P3, 2 comments). Frames the "context rot" / token-cost argument for durable task state — a foundational design question for long sessions.

## 4. Key PR Progress

1. **[#30250](https://github.com/google-gemini/gemini-cli/pull/30250)** (CLOSED) — Fix `${VAR:-default}` to substitute the default when the variable is set but empty, matching POSIX semantics. Small but corrects a real config footgun.

2. **[#30247](https://github.com/google-gemini/gemini-cli/pull/30247)** (OPEN, P2) — Avoid blocking the Node event loop during PowerShell parsing for shell policy checks on Windows; should unstick the Thinking spinner before the Shell panel appears.

3. **[#30228](https://github.com/google-gemini/gemini-cli/pull/30228)** (CLOSED, P2) — Preserve interrupted placeholder after empty model responses to tool turns, fixing `400 "Requests ending with a model turn are not supported"`.

4. **[#30241](https://github.com/google-gemini/gemini-cli/pull/30241)** (OPEN) — Keep injected binary data intact when truncating tool responses; fixes `400 "Base64 decoding failed"` on large audio/video `read_file` calls.

5. **[#30230](https://github.com/google-gemini/gemini-cli/pull/30230)** (OPEN, P1) — Retain the synthetic binary ack so injected audio/video stays in its own turn under Gemini 3 + Code Assist. Closely related to #30241; both target the binary-injection pipeline.

6. **[#30234](https://github.com/google-gemini/gemini-cli/pull/30234)** (OPEN) — ACP now truncates large tool outputs like the CLI. One `seq 1 60000` call reportedly cost 369k input tokens over ACP; a significant cost fix.

7. **[#30236](https://github.com/google-gemini/gemini-cli/pull/30236)** (OPEN) — ACP runs parallelizable tool calls together, mirroring the CLI `Scheduler`; serial awaits were inflating turn latency.

8. **[#30232](https://github.com/google-gemini/gemini-cli/pull/30232)** (OPEN) — Implement `session/close` for ACP so clients can release session config, chat, and MCP connections instead of leaking them.

9. **[#30107](https://github.com/google-gemini/gemini-cli/pull/30107)** (OPEN, P1) — Make the auth URL clickable when the terminal wraps it, fixing Google's `400 Access blocked` on narrow terminals. Common onboarding blocker.

10. **[#29546](https://github.com/google-gemini/gemini-cli/pull/29546)** (OPEN, P2, help wanted) — Enable `/skill-name` activation in non-interactive mode by registering `SkillCommandLoader`. Bridges the interactive/non-interactive gap for skills.

## 5. Hot Discussions

No Discussion data was provided in this dataset; section omitted.

## 6. Feature Request Trends

- **Smarter, cheaper context handling** — AST-aware reads/search/mapping (#22745, #22746, #22747) and surgical "Tactful Extraction" reads (#19561) recur as the dominant efficiency theme.
- **Persistent, file-based task state** — Replace in-context `WriteToDo` with CRUD task tracking (#18836); experiment with native file tools for the task tracker (#21000).
- **Better subagent observability** — Surface subagent trajectories via `/chat share` (#22598) and include subagent context in `/bug` reports (#21763).
- **Sandboxing as an enabler, not a restriction** — Let the model use its bash strengths safely (#19873); discourage destructive git/DB commands (#22672).
- **Self-aware CLI** — Accurate flags/hotkeys and self-execution guidance (#21432).
- **Parallel/shared-memory subagents** — Exploratory work gated on parallel subagent support (#18287).

## 7. Developer Pain Points

- **Reliability of the agent loop**: hangs (#21409), false-success termination (#22323), and stuck interactive prompts (#22465) erode trust in unattended runs.
- **Config that doesn't take effect**: browser agent ignoring `settings.json` (#22267); symlinked agent files not recognized (#20079); tool-scoping failing past ~128 tools (#24246).
- **Windows/Linux platform parity**: PowerShell event-loop blocking (#30247), Wayland browser failures (#21983), and cmd.exe path quoting in `/ide install` (#30246).
- **Binary/media handling fragility**: repeated 400s on audio/video via `read_file` under Code Assist (#30230, #30241) and empty-response turn handling (#30228).
- **Context/token economics**: baseline ~36.6k tokens per turn and firehose reads (#19561), plus unbounded ACP tool output (#30234) driving cost complaints.
- **Auth/onboarding friction**: wrapped login URLs causing `400 Access blocked` (#30107) remains a recurring first-run blocker.

</details>

<details>
<summary><strong>GitHub Copilot CLI</strong> — <a href="https://github.com/github/copilot-cli">github/copilot-cli</a></summary>

# GitHub Copilot CLI Community Digest — 2026-11-12

## 1. Today's Highlights

A modest day on the repo: one small prerelease (`v1.0.110-2`) landed with two targeted robustness fixes around interrupted responses and deleted working directories. Issue activity remains high (42 updated in 24h), with the community's attention split between **prerelease regressions in the new concise transcript mode**, **MCP/session resume instability**, and **platform-specific breakage** on Windows, WSL2, macOS, and Linux ARM64. No pull requests were updated in the last 24 hours, so all forward motion this cycle is visible only through releases and issue triage.

## 2. Releases

**v1.0.110-2** — patch release:
- **Partial responses from interrupted/failed attempts** are now labeled *incomplete* while preserving their text, so aborted turns no longer look like clean completions.
- **Deleted working directory handling**: launching `copilot` from a directory that no longer exists now emits a clear "directory no longer exists" message with recovery guidance instead of a Node stack trace.

🔗 https://github.com/github/copilot-cli/releases

## 3. Hot Issues

1. **#3534 [CLOSED] WSL2 ARM64 `/copy` fails with `clip.exe exited with code 1`** — Clipboard writes through the `cmd.exe` wrapper were broken by quoting; 9 comments and 7 👍 made this the most-engaged issue of the day before closure. Matters for ARM64 WSL users who rely on `/copy` daily. 🔗 https://github.com/github/copilot-cli/issues/3534
2. **#4802 [OPEN] PRU quota wiped out, likely tied to Assisted Permissions** — A Pro/legacy PRU user reports quota vanishing after enabling assisted permissions. High-stakes billing/trust issue with 9 comments; worth watching for an official root cause. 🔗 https://github.com/github/copilot-cli/issues/4802
3. **#5171 [OPEN] NumLock-off numpad navigation and Delete ignored (Windows)** — A regression in 1.0.98 affecting Windows Terminal users; small but persistent papercut class that keeps resurfacing. 🔗 https://github.com/github/copilot-cli/issues/5171
4. **#5053 [OPEN] ACP sessions stop indexing history/usage in `session-store.db` (regression in 1.0.89)** — Breaks local session history and usage tracking for ACP users; a classic "silent regression" that erodes trust in non-interactive workflows. 🔗 https://github.com/github/copilot-cli/issues/5053
5. **#4606 [OPEN] Google Workspace MCP OAuth fails on trailing-slash issuer mismatch** — Native HTTP MCP auth fails before the browser flow even starts due to OIDC issuer normalization; blocks Google Workspace MCP integration entirely. 🔗 https://github.com/github/copilot-cli/issues/4606
6. **#2955 [OPEN] `/allow-all` does not suppress bash tool prompts** — Long-standing permissions bug (open since April) where users still get per-command approval dialogs after `/allow-all`; 2 👍 and steady comments signal it's still biting people. 🔗 https://github.com/github/copilot-cli/issues/2955
7. **#5312 [CLOSED] Context window rollover still fails; `checkpoint.md` absent on 1.0.105** — Filed in frustration after the prior issue (#5205) was closed as completed with no linked fix or release; a process-transparency complaint as much as a bug. 🔗 https://github.com/github/copilot-cli/issues/5312
8. **#5335 [OPEN] Repeated HTTP 422 cybersecurity-risk flags block legitimate local debugging** — Model-side safety classifier repeatedly rejects benign local flaky-test investigation; a model-guardrail false-positive that directly harms developer workflows. 🔗 https://github.com/github/copilot-cli/issues/5335
9. **#5145 [OPEN] 400 `reasoning_text` must be passed back in thinking mode (custom OpenAI-compatible providers)** — Triggered by runtime-synthesized assistant turns after background shells exit; breaks BYOK/DeepSeek-style provider sessions. 🔗 https://github.com/github/copilot-cli/issues/5145
10. **#5256 [CLOSED] High CPU from tight `gh auth token` retry loop when not signed in** — Background auth probe retried with no backoff, burning CPU in BYOK/no-GitHub-account sessions; closed but representative of auth-probe hardening needs. 🔗 https://github.com/github/copilot-cli/issues/5256

*Honorable mentions (same-day, triage-fresh):* #5426 (concise transcript drops reasoning rows despite `showReasoning`/Ctrl+T), #5427 (collapsed "N commands" groups render untruncated text), #5424 (linux-arm64 binary `ENOEXEC` on Linux 6.12 aarch64/4K pages), #5423 (file links don't open in VS Code when IDE-connected).

## 4. Key PR Progress

No pull requests were updated in the last 24 hours; there is no PR progress to report for this cycle.

## 5. Hot Discussions

No discussion data was provided for this window; section omitted.

## 6. Feature Request Trends

- **Configuration and parity**: Requests for a unified interactive `/config` editor (#3352) and the ability to disable built-in slash commands (#5375) point to growing demand for CLI configurability and Claude Code parity.
- **Voice/input ergonomics**: Making `/voice` transcription language configurable instead of hard-coded `"auto"` (#5226), plus continued numpad/keybinding fixes (#5171).
- **Session UX**: Inline session summaries in the `/session` picker (#2330) — better discoverability of session name/recency/intent.
- **MCP lifecycle management**: DCR re-registration when clients expire (#5284) and resilience when MCP tool sets change between launches (#5359).
- **Transcript rendering controls**: Fine-grained control over concise transcript behavior (reasoning visibility, command-group truncation — #5426, #5427).

## 7. Developer Pain Points

- **Regression fatigue across releases**: Multiple issues explicitly cite version regressions (1.0.89 ACP indexing, 1.0.98 Windows numpad, 1.0.105 context rollover), and at least one filer expressed frustration that a prior issue was closed without a linked fix (#5312). Release-note hygiene and issue-close transparency are recurring sore spots.
- **MCP and session-resume fragility**: MCP servers silently dropped during session-ID swaps (#4920), resumed sessions failing with "tool reference not found" (#5359), and OAuth edge cases (#4606, #5284) make MCP the most fragile surface area.
- **Platform-specific breakage**: WSL2 ARM64 clipboard (#3534), Windows sandbox builds with dotnet/MSBuild (#5421), macOS 12.7.6 native addon load failure (#5418), and linux-arm64 `ENOEXEC` (#5424) show packaging/runtime gaps across non-mainstream platforms.
- **Permission system inconsistency**: `/allow-all` not suppressing prompts (#2955) and permission-request delivery timeouts auto-cancelling sessions (#5018) undermine trust in the permission flow.
- **Model-side guardrail false positives**: Cybersecurity-risk 422s (#5335) and reasoning-text 400s with custom providers (#5145) block legitimate debugging and BYOK usage.
- **Resource leaks and reliability**: Unbounded `gh auth token` retry loops (#5256) and build coordinators being stopped without approval (#5420) raise concerns about long-running session stability.

---
*Digest generated from github.com/github/copilot-cli data for 2026-11-12. Note: no Discussions or PR activity was present in the source data; corresponding sections are omitted or marked accordingly.*

</details>

<details>
<summary><strong>OpenCode</strong> — <a href="https://github.com/anomalyco/opencode">anomalyco/opencode</a></summary>

# OpenCode Community Digest — 2026-11-12

## Today's Highlights

The free-tier experience continues to dominate community attention, with the mega-thread #59387 now at 354 comments and multiple fresh reports of vanishing queues and silent limit enforcement. On the engineering side, the team shipped `process-daemon-v0.5.2` (protocol 8) and is actively landing a wave of core fixes around subagent wake recovery, session history APIs for plugins, and Windows support for persistent shell execution. Desktop reliability remains a weak spot, with several high-signal bug reports around background processes, RPC hangs, and editor integration.

## Releases

- **[process-daemon-v0.5.2](https://github.com/anomalyco/opencode/releases)** — `opencode-pty` 0.5.2 (protocol 8), published to npm as `@opencode/pty@0.5.2`. This version is now pinned by core, CLI, and server, and enables the Windows local-daemon path for shell commands that survive server restarts (see PR #63409).

## Hot Issues

1. **[#59387 — [zen] Free tier issues: read this first](https://github.com/anomalyco/opencode/issues/59387)** (354 comments, 27 👍) — The canonical free-tier complaint thread. Users report "Free usage exceeded" errors, resets that never fire, and Desktop version mismatches. The volume signals that free-tier messaging and quota resets remain a trust problem, not just a capacity problem.

2. **[#63393 — Changing the model vanished queued requests](https://github.com/anomalyco/opencode/issues/63393)** — Switching models after hitting limits silently drops the queue. The reporter recovered requests from an "inbox table," implying the data isn't lost but the UX is dangerous. High emotional impact for a low-complexity fix.

3. **[#63368 — Proposal: keep one always-available, non-frontier free model](https://github.com/anomalyco/opencode/issues/63368)** — Two free models were deprecated in one week (Pulse Alpha Nov 6, Crouton Alpha Nov 9), and only LongCat 2.5 publishes a concrete limit. The request is for predictability over frontier capability — a reasonable community compromise.

4. **[#63336 — serve daemon leaks per-session stdio MCP server processes](https://github.com/anomalyco/opencode/issues/63336)** — Closing a TUI window leaves MCP transports running under `opencode serve --service`. A resource-leak class bug that will compound for long-running users.

5. **[#63092 — MCP timeout docs say 5s, client uses 30s; scalar timeout overwrites 12h window](https://github.com/anomalyco/opencode/issues/63092)** — Reproduced mismatch between published schema and bundled client behavior. Config semantics diverging from docs is a recurring source of user confusion.

6. **[#63384 — `experimental.browser/attach` hangs indefinitely, causing Desktop session timeouts](https://github.com/anomalyco/opencode/issues/63384)** — Desktop calls this during session restore; the hang cascades into connection teardown and reconnects. A single blocking RPC gating app startup is a serious reliability issue.

7. **[#63385 — `iceman-contributor-free` fails with `reasoning_display` error](https://github.com/anomalyco/opencode/issues/63385)** — Upstream rejects a parameter the client never sends, pointing to a provider-side or translation-layer mismatch. Notable because it breaks a free model entirely on every turn.

8. **[#63171 — Turn stopping inside reasoning settles as "succeeded" with zero content](https://github.com/anomalyco/opencode/issues/63171)** — Silent data loss: `finish: "stop"`, `tokens.output: 0`, `outcome: succeeded`. This corrupts transcripts and telemetry and deserves priority triage.

9. **[#63331 — `github-copilot/auto` missing in standalone mode](https://github.com/anomalyco/opencode/issues/63331)** — Model discovery diverges between shared-server and standalone modes, blocking Copilot Student users whose plans only allow the `auto` route.

10. **[#63195 — Provider `usage.cost` trusted as USD without unit check](https://github.com/anomalyco/opencode/issues/63195)** — Micro-dollar providers inflate session cost ~1e6×. A correctness bug in billing display that undermines trust in cost tracking.

## Key PR Progress

1. **[#63416 — fix(core): recover parent wakes after subagent report acknowledgement](https://github.com/anomalyco/opencode/pull/63416)** — Follow-up to #63209; handles the crash window where a parent is left idle with a pending notice and no execution claim. Important for background subagent reliability.

2. **[#63415 — fix(core): resume directly driven child sessions after restart](https://github.com/anomalyco/opencode/pull/63415)** — Closes #63414. Ensures child sessions directly continued before a server stop aren't orphaned by the stale-claim sweep.

3. **[#63413 — refactor(core): remove the subagent `notify` option](https://github.com/anomalyco/opencode/pull/63413)** — Every child with a parent now reports on idle; silencing subagents is no longer supported. A deliberate simplification of the subagent contract.

4. **[#63410 — feat(plugin): expose paginated session messages](https://github.com/anomalyco/opencode/pull/63410)** — Adds `ctx.session.message.list` to the plugin API, letting plugins build history-search tools (paired with #63411).

5. **[#63411 — feat(core): session history search and inspect tools](https://github.com/anomalyco/opencode/pull/63411)** — Consumes #63410 to let agents search their own session history, gated behind `experimental.session_search`. A meaningful step toward agent self-context management.

6. **[#63409 — feat(core): run local Windows shell commands in the process daemon](https://github.com/anomalyco/opencode/pull/63409)** — Pins daemon 0.5.2 and extends persistent shell execution to Windows. Directly addresses a long-standing platform gap.

7. **[#63412 — fix(mcp): propagate active trace context](https://github.com/anomalyco/opencode/pull/63412)** — Closes #46856. Forwards W3C trace context across MCP tools, prompts, resources, and catalog requests — overdue observability work.

8. **[#63030 — fix(core): stop keeping whole file in memory for tail reads](https://github.com/anomalyco/opencode/pull/63030)** — Follow-up to #62147; paging through large files (108 MiB example) no longer loads the entire file. A concrete memory-footprint win.

9. **[#63406 — fix(desktop): only list installed editors in "Open in"](https://github.com/anomalyco/opencode/pull/63406)** — Closes #63194; `checkAppExists` now actually checks on Linux/Windows and VSCodium is recognized. Small but high-friction desktop fix.

10. **[#62995 — feat(vscode): add sidebar UI](https://github.com/anomalyco/opencode/pull/62995)** — Closes #11176; brings OpenCode into VS Code's secondary sidebar. Community-requested IDE integration that broadens reach.

## Feature Request Trends

- **Predictable free-tier policy**: A stable, documented free model with visible quotas (#63368, #59387) rather than rotating limited-time models.
- **Session/queue durability**: Queue preservation across model changes (#63393) and resumable/correctable session state (moved sessions updating project, #63188).
- **Plugin API expansion**: Paginated session history and embedding support (`experimental.generate.embed`, #62794) — plugin authors want richer context access.
- **Desktop/IDE polish**: Resizable session content width (#53382), VS Code sidebar (#62995), Windows file-tree refresh (#63214), VSCodium support (#63194).
- **Custom provider configurability**: Restoring the custom provider option in v2 (#59603).

## Developer Pain Points

- **Free-tier quota opacity**: Resets that don't fire, undocumented cutoffs, and deprecation cadence erode trust; the 354-comment thread is the clearest signal.
- **Silent data/state loss**: Vanished queues (#63393), zero-content "successful" turns (#63171), and dropped requests are the most damaging class of bugs because they're invisible until recovery.
- **Desktop lifecycle reliability**: Background processes surviving window close (#60983), leaked MCP servers (#63336), and hanging RPCs blocking session restore (#63384) cluster into a pattern of lifecycle management debt.
- **Config vs. docs divergence**: MCP timeouts (#63092) and usage-cost units (#63195) show that published schemas aren't matching runtime behavior — costly for anyone tuning configs.
- **Platform parity gaps**: Standalone vs. shared-server model discovery (#63331) and Windows/Linux editor detection (#63406) repeatedly bite users on non-macOS setups.

</details>

<details>
<summary><strong>Pi</strong> — <a href="https://github.com/earendil-works/pi">earendil-works/pi</a></summary>

# Pi Community Digest — 2026-11-12

## 1. Today's Highlights

A heavy bug-fix day: the `robopipi` bot landed a batch of targeted fixes spanning TUI rendering (Kitty image redraws, OSC 8 hyperlinks in exports), durable storage races, MCP tool restoration on resume, and standalone-binary extension resolution. Meanwhile, the Vercel AI Gateway integration got attention with new PRs routing OpenAI models through Responses and persisting hosted tool-search history. No new releases in the last 24h.

## 2. Releases

None in the last 24 hours.

## 3. Hot Issues

1. **[#11956] Crash on cancelling a read during file open** (CLOSED, p0) — An uncaught `AbortError` could kill Pi entirely when codemode's `Promise.all` cancelled a sibling read. High severity because codemode workflows make this easy to hit. [Link](https://github.com/earendil-works/pi/issues/11956)

2. **[#12068] `pi -p` never exits on Windows (1.5.1/1.5.2)** (CLOSED) — Print-mode hangs when a tool call occurred and an extension calls `pi.exec()`; 1.5.0 was fine. A regression affecting CI/scripted use on Windows. [Link](https://github.com/earendil-works/pi/issues/12068)

3. **[#10162] Too many input images stop the agent task** (OPEN, 9 comments) — Long-running agent sessions break with image-heavy inputs despite auto-compaction. Directly undermines "babysit a PR indefinitely" workflows. [Link](https://github.com/earendil-works/pi/issues/10162)

4. **[#11026] pi-durable: permanently delete a conversation** (OPEN, 6 👍) — Requests `harness.deleteConversation(id)` with safe refusal during active runs. Notable community support for data lifecycle control in durable storage. [Link](https://github.com/earendil-works/pi/issues/11026)

5. **[#10386] Durable: host coding-agent extensions** (OPEN) — Asks for `AgentSession`-backed runtime or hook parity so extensions can run on pi-durable. Key to unifying the extension ecosystem across runtimes. [Link](https://github.com/earendil-works/pi/issues/10386)

6. **[#12019] github-copilot: Gemini models never get `reasoning_effort`** (OPEN, p0) — `--thinking` silently does nothing for Copilot Gemini models due to `compat.supportsReasoningEffort: false`. Misleading user-visible behavior. [Link](https://github.com/earendil-works/pi/issues/12019)

7. **[#11999] ant-ling provider broken — migrate to Ling 3.x Flash** (OPEN, p0) — Catalog still ships Ling/Ring 2.6 IDs that no longer resolve upstream; provider is non-functional end-to-end. Author offering a PR. [Link](https://github.com/earendil-works/pi/issues/11999)

8. **[#12035] Threshold compaction fires far below the window for virtual models** (OPEN) — `_checkCompaction` treats unknown context windows as tiny, triggering premature compaction. Affects anyone using virtual models. [Link](https://github.com/earendil-works/pi/issues/12035)

9. **[#11977] `/login` draws API keys on screen and stores them as config values** (CLOSED) — A security-relevant UX flaw: keys visible in the pane and persisted as plain config. [Link](https://github.com/earendil-works/pi/issues/11977)

10. **[#12023] `.pi/agents/` not trust-gated; subagent approval gate bypasses** (CLOSED) — Project trust defaults to true for repos shipping only agent files, and the example approval gate is model-disableable and symlink-blind. Notable supply-chain concern. [Link](https://github.com/earendil-works/pi/issues/12023)

## 4. Key PR Progress

1. **[#12069] Route Vercel OpenAI models through Responses** — Routes `openai/*` catalog models to `openai-responses` on the Vercel AI Gateway, unlocking Responses features behind the gateway. [Link](https://github.com/earendil-works/pi/pull/12069)

2. **[#12071] Preserve hosted Responses tool-search history** — Retains `tool_search_call`/`tool_search_output` items so stateless follow-up requests keep tool-loading history and cached prefixes. [Link](https://github.com/earendil-works/pi/pull/12071)

3. **[#12072] Group deferred Responses tools into namespaces** — Opt-in `toolSearch.deferredTools` namespacing reduces input-token cost for large tool catalogs. [Link](https://github.com/earendil-works/pi/pull/12072)

4. **[#12054] Keep restored MCP tools when an extension hides only its own tool** — Fixes `tool_search`-loaded MCP tools being dropped on resume/reload, resolving #11996. [Link](https://github.com/earendil-works/pi/pull/12054)

5. **[#12046] Fix standalone binary resolving extension deps via `main`/`exports`** — Enables `--compile-autoload-package-json` so bun-compiled binaries honor package entry points; resolves #12012 (Shiki failures). [Link](https://github.com/earendil-works/pi/pull/12046)

6. **[#12052] Convert OSC 8 hyperlinks in HTML export** — `/export` now outputs clickable links instead of raw escape sequences, fixing #12003. [Link](https://github.com/earendil-works/pi/pull/12052)

7. **[#12047] Keep Kitty images across fullscreen full redraws** — Reorders image deletion/placement around `ESC[2J` to stop image flicker on resize. [Link](https://github.com/earendil-works/pi/pull/12047)

8. **[#12055] Durable: skip entries removed during `listDir` enumeration** — Fixes the `ENOENT ... .reclaim` race for read-only JSONL stores opened beside a live writer (#11994). [Link](https://github.com/earendil-works/pi/pull/12055)

9. **[#12042 / #12059] P2 backup coordinator + Radius backend** — Adds `P2_BACKUP_DIR` local backups and a `P2_BACKUP=radius` remote backend for Chord store session history. [Link](https://github.com/earendil-works/pi/pull/12042) · [Link](https://github.com/earendil-works/pi/pull/12059)

10. **[#12051] Verify managed updates with the running Node.js** — Fixes `pi update` validation on Windows where the npm shim picks up a different Node than the managed launcher. [Link](https://github.com/earendil-works/pi/pull/12051)

## 5. Hot Discussions

**Ideas**
- **[#12063] Pi integration for Concord** — Proposal for Pi to join a cross-harness shared work-state MCP server (presence, file claims, task context, handoffs) alongside Claude Code and Cursor. Early signal (1 👍, 0 comments) but points at growing demand for multi-agent coordination. [Link](https://github.com/earendil-works/pi/discussions/12063)

## 6. Feature Request Trends

- **Durable-storage lifecycle & extensibility** — Conversation deletion (#11026), extension hosting/hook parity (#10386), and P2/Radius backup work (#12042, #12059, #11990) show sustained investment in pi-durable as a first-class runtime.
- **Extension API surface** — Atomic enqueue-and-wake completion APIs (#12073) and request-admission gates (#12057) indicate extension authors want more control over session dispatch.
- **Terminal & clipboard interoperability** — Kitty clipboard protocol (#10089), Cmd+V image paste (#4332), and alacritty keyboard-flag narrowing (#12050) reflect demand for robust TUI behavior across terminals/SSH.
- **Configurability of UX noise** — Toggleable update banners (#3005) and conditional `AGENTS.md` loading (#11933) show users want less intrusive, more contextual defaults.
- **Cross-harness collaboration** — The Concord discussion signals interest in shared state between Pi and other coding agents.

## 7. Developer Pain Points

- **Windows print-mode reliability** — The `pi -p` hang (#12068) and managed-update Node mismatch (#12051) point to a fragile Windows story in scripted/CI contexts.
- **Resume/reload state loss** — MCP tools dropped on resume (#11996), replayed prompt-template expansions (#12013), and cached system-prefix invalidation after MCP connect (#12015) all stem from session-rehydration edge cases.
- **Silent misbehavior over hard errors** — Reasoning effort ignored for Copilot models (#12019), empty bash output on over-long tails (#12062), and premature compaction for virtual models (#12035) fail quietly, which is harder to diagnose than explicit errors.
- **Provider catalog drift** — Broken ant-ling IDs (#11999) and hidden stored-model overlays (#12037) show catalog staleness/merging is a recurring maintenance burden.
- **Data hygiene** — Lone surrogate stripping missing from tool-call arguments (#12014) breaks all subsequent requests, a small parsing gap with outsized blast radius.

</details>

<details>
<summary><strong>Qwen Code</strong> — <a href="https://github.com/QwenLM/qwen-code">QwenLM/qwen-code</a></summary>

# Qwen Code Community Digest — 2026-11-12

## 1. Today's Highlights

Qwen Code shipped its **v1.0.0-preview** release, unifying Qoder and Qwen Code agent capabilities for code understanding, implementation, debugging, and testing — a significant milestone with a stabilization caveat. Meanwhile, the issue tracker continues to be dominated by **CI flakiness and main-branch failures** (several bot-filed issues with hundreds of comments), alongside a cluster of **Web Shell UX refinements** and **core agent-loop mechanisms** (verify-then-repair, programmatic tool calling) proposed by the community.

---

## 2. Releases

**v1.0.0-preview** — [Release](https://github.com/QwenLM/qwen-code/releases)
- Brings together the agent capabilities of **Qoder** and **Qwen Code** for code understanding, implementation, debugging, and testing.
- Supports Qwen and Qoder accounts.
- ⚠️ Preview caveat: interfaces, configuration options, and behaviors may change during the preview period. Developers should pin versions and expect churn.

---

## 3. Hot Issues

1. **[#14724](https://github.com/QwenLM/qwen-code/issues/14724)** — *Main CI failed: Managed Agent gate — no test identified* (324 comments). The highest-traffic issue in the repo; a persistent CI gate failure that has been open since mid-October and continues to generate bot churn. Signals instability in the managed-agent test gating.

2. **[#11680](https://github.com/QwenLM/qwen-code/issues/11680)** — *Main CI failed: E2E Tests — no test identified* (48 comments). A long-running E2E sandbox failure (docker shard) tracked per-commit; indicative of a flaky E2E lane that keeps re-filing.

3. **[#16425](https://github.com/QwenLM/qwen-code/issues/16425)** — *Workflow agent permission approval never renders; run hangs indefinitely* (CLOSED, 10 comments). A serious P1 core/interactive bug: a workflow-runner agent reaches a permission prompt, the harness fires `PermissionRequest`, but no UI renders and the run hangs. Reproduced across 0.26.6–0.26.8. Its closure is a notable reliability win.

4. **[#16915](https://github.com/QwenLM/qwen-code/issues/16915)** — *Task reports two errors and cannot continue* (7 comments). User-facing session-management bug where model-service connection interruptions block task continuation; needs-info but reflects real friction with long-running tasks.

5. **[#124](https://github.com/QwenLM/qwen-code/issues/124)** — *Tool calls with self-hosted Qwen3-Coder fail (almost always)* (CLOSED, 7 comments, 7 👍). A long-standing community pain point (since July 2025) for self-hosters; its closure is meaningful for the OSS/self-hosting crowd.

6. **[#15861](https://github.com/QwenLM/qwen-code/issues/15861)** — *web-shell host-directory-picker smoke times out, PR-independent* (7 comments). A deterministic E2E smoke failure that taxes every PR with re-attribution rounds — a clear CI-quality concern flagged by a maintainer.

7. **[#14361](https://github.com/QwenLM/qwen-code/issues/14361)** — *Session store intermittently refuses Hosted Shell turn with 400 (41% SDK Java runs)* (7 comments). P2 reliability defect in the Java SDK's hosted-shell lifecycle recovery; high flake rate makes it costly for SDK consumers.

8. **[#16851](https://github.com/QwenLM/qwen-code/issues/16851)** — *Web UI: weekly usage limit stuck, not resetting* (3 comments). Quota/reset bug on coder.qwen.ai affecting real users; support-type but points to account-state inconsistencies.

9. **[#16921](https://github.com/QwenLM/qwen-code/issues/16921)** — *glob can never match a real directory (`nodir:true` + `follow:false`)* (3 comments). A core tool-correctness bug: directory-shaped glob patterns return "No files found," misleading the model into thinking paths don't exist.

10. **[#16954](https://github.com/QwenLM/qwen-code/issues/16954)** — *`scripts/lint.js` entry guard silently no-ops through symlinks (macOS)* (3 comments). Lint exits 0 without running when invoked via a symlinked path — a nasty silent-pass failure mode in local dev.

---

## 4. Key PR Progress

1. **[#16594](https://github.com/QwenLM/qwen-code/pull/16594)** — *fix(cli): never terminally release a config-scoped Hook owner on a detach*. Hardens lifecycle-v1/v2 runtime drains so recomputed owners aren't prematurely released — important for hook and session lifecycle correctness.

2. **[#16849](https://github.com/QwenLM/qwen-code/pull/16849)** — *fix(acp): deliver teammate reports and reattach persisted teams across session reloads*. Wires agent-team coordination into the ACP surface (`qwen serve --web`/`--acp`) so teammate inbox reports reach the leader as continuation turns.

3. **[#16895](https://github.com/QwenLM/qwen-code/pull/16895)** — *fix(core): bound post-compaction restoration by the context window*. Makes recent-file restoration budget-aware (window-relative, CJK-aware) — directly addresses context-blowup after compaction.

4. **[#14332](https://github.com/QwenLM/qwen-code/pull/14332)** — *Make tool output truncation depend on the remaining context window*. Replaces a fixed character cap with a dynamic limit tied to remaining context — a meaningful agent-loop quality improvement.

5. **[#16972](https://github.com/QwenLM/qwen-code/pull/16972)** — *fix(mcp): restore session catalogs on explicit rediscovery*. Fixes MCP tool/prompt/resource caches being lost on re-discovery; reuses transport and preserves unrelated catalogs.

6. **[#16979](https://github.com/QwenLM/qwen-code/pull/16979)** — *fix(web-shell): prune split-view panes parked on terminal session_closed*. Cleans up stale "Session closed" panes in Split View — a visible UX polish item.

7. **[#16779](https://github.com/QwenLM/qwen-code/pull/16779)** — *feat(web-shell): improve session search experience*. Moves session search into a dedicated dialog with snippets, keyboard selection, focus restoration, and loading/retry states.

8. **[#16973](https://github.com/QwenLM/qwen-code/pull/16973)** — *fix(ci): run lint through symlinked entry paths*. Companion fix to #16954; ensures lint actually executes when the entry is symlinked.

9. **[#16974](https://github.com/QwenLM/qwen-code/pull/16974)** — *fix(ci): bound runner monitoring and back off Docker outages*. Reduces runner-monitoring noise and adds backoff when Docker inspection fails — CI infrastructure hardening.

10. **[#16946](https://github.com/QwenLM/qwen-code/pull/16946)** — *ci: narrow lint gate freshness to the lint slice ci.yml defines*. Prevents unrelated CI-definition edits from invalidating the lint-gate freshness guard — developer-experience improvement for contributors.

---

## 5. Hot Discussions

*No Discussion data was provided in the source dataset; this section is omitted.*

---

## 6. Feature Request Trends

Distilled from Issues and PRs updated in the last 24h:

- **Core agent-loop mechanisms**: Two notable proposals from the same author push verification/orchestration into the core rather than skills — [verify-then-repair with a clean-context verifier (#16941)](https://github.com/QwenLM/qwen-code/issues/16941) and [programmatic tool calling in the sandbox (#16944)](https://github.com/QwenLM/qwen-code/issues/16944). Both target context-performance (`roadmap/context-performance`).
- **Web Shell / Desktop UX**: A strong cluster of UI requests — [host content slots in the model picker (#16893)](https://github.com/QwenLM/qwen-code/issues/16893), [focus composer after workspace selection (#16980)](https://github.com/QwenLM/qwen-code/issues/16980), [fix clipped TodoPanel tooltip in Split View (#16970)](https://github.com/QwenLM/qwen-code/issues/16970), and ["Ask in side task" in message selection menu (#16964)](https://github.com/QwenLM/qwen-code/issues/16964).
- **Daemon / channel integrations**: [Pick channel allowlist entries from observed contacts instead of typing platform IDs (#16931)](https://github.com/QwenLM/qwen-code/issues/16931) — reducing operator friction for messaging integrations.
- **Docs correctness**: [MCP docs claim a restart is required, but `mcpServers` hot-reload (#16925)](https://github.com/QwenLM/qwen-code/issues/16925) — a documentation-behavior mismatch.

---

## 7. Developer Pain Points

- **CI flakiness and main-branch red**: The single largest source of noise. Bot-filed "Main CI failed" issues (#14724 at 324 comments, #11680, #11678, #13009, #15574, #15698) plus deterministic smoke failures (#15861) and flaky test flags (#16975, #16959) dominate the tracker. Every unrelated PR pays re-attribution cost.
- **Silent-pass / correctness traps**: #16954 (lint exits 0 through symlinks) and #16921 (glob can't match directories) both fail *silently*, which is especially damaging for agent-driven workflows that interpret "no files found" as ground truth.
- **Session/connection instability**: Multiple reports of tasks hanging or being interrupted mid-run (#16915, #16908, #16425), pointing to model-service connection handling and permission-prompt rendering as fragile seams.
- **Context-window management after compaction**: PRs #16895 and #14332 show sustained effort to make restoration and tool-output limits window-aware — a recurring source of degraded agent behavior.
- **macOS-specific rough edges**: Symlinked checkouts (#16954) and macOS unit-test flakiness (#16091) suggest the dev environment on macOS needs attention.

---

*Digest generated from public GitHub activity on QwenLM/qwen-code for 2026-11-12.*

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar).*