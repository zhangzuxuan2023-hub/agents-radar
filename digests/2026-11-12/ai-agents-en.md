# OpenClaw Ecosystem Digest 2026-11-12

> Issues: 471 | PRs: 500 | Projects covered: 5 | Generated: 2026-11-11 23:28 UTC

- [OpenClaw](https://github.com/openclaw/openclaw)
- [Hermes Agent](https://github.com/nousresearch/hermes-agent)
- [IronClaw](https://github.com/nearai/ironclaw)
- [QwenPaw](https://github.com/agentscope-ai/QwenPaw)
- [ZeroClaw](https://github.com/zeroclaw-labs/zeroclaw)

---

## OpenClaw Deep Dive

# OpenClaw Project Digest — 2026-11-12

## 1. Today's Overview

OpenClaw is in a high-velocity, high-contention maintenance phase. Over the last 24 hours the repository saw **471 issue updates** (264 open/active, 207 closed) and **500 pull-request updates** (303 open, 197 merged/closed), with **zero new releases** — meaning all work is flowing through `main` and open branches rather than shipping artifacts. The dominant narrative is **quality debt from the 2026.9–2026.10 release trains**: a cluster of P0/P1 items around Gateway startup blocking, Windows service reliability, plugin source-capture staging, and Telegram/claude-cli message loss remain unresolved with long comment threads. Simultaneously, maintainers are landing a large volume of small-to-medium fixes (daemon installs, transcript rendering, plugin badges, compaction caching), which suggests the project is actively triaging rather than feature-building. Overall health: **strong throughput, concerning stability signal density in the Gateway/plugin/Windows paths**.

---

## 2. Releases

**None.** No new releases published in the last 24 hours. Notably, several open P0/P1 issues explicitly reference 2026.9.6–2026.10.10 regressions, and at least one (e.g., #191818) requests a **backport** of a fix (#180492) to a shipped release — indicating that release cadence and the fix pipeline are currently out of sync. Maintainers should expect pressure to cut a patch release once the Windows/Gateway blockers are resolved.

---

## 3. Project Progress

~197 PRs were merged/closed in the window. Representative closed items show steady hygiene fixes rather than headline features:

- **#197212 (closed)** — iOS refactor: consolidates ~10 separate `NodeAppModel` connection fields into one phase per Gateway session with a sticky shared pause. Reduces state divergence bugs. [PR #197212](https://github.com/openclaw/openclaw/pull/197212)
- **#197057 (closed)** — Tlon channel: inbound messages no longer lose native list contents (list-only messages no longer treated as empty). [PR #197057](https://github.com/openclaw/openclaw/pull/197057)
- **#197308 / #197398 (closed)** — Knowledge chat-export timestamp validation and `oc-path` dotfile/consecutive-dot filename resolution. Small correctness fixes. [PR #197308](https://github.com/openclaw/openclaw/pull/197308), [PR #197398](https://github.com/openclaw/openclaw/pull/197398)
- **#197234 (closed)** — Gateway approval race: a granted exec/plugin approval no longer fails when another session changes during the wait. [PR #197234](https://github.com/openclaw/openclaw/pull/197234)
- **#197392 (closed)** — CI diagnostics for recurring SIGUSR2 shutdown-proof timeouts.

**In-flight notable PRs** (not yet merged): macOS native dependency bundling before signing (#197383, P1), bounded managed-browser memory (#197408), bounded reloadable chat history (#187440), worktree template prewarming (#197301), and a security-boundary plugin progress-card SDK (#176273 linked proposal).

---

## 4. Community Hot Topics

Ranked by comment activity; these threads reveal the project's real friction points.

| Item | Comments | Signal |
|---|---|---|
| [#160959](https://github.com/openclaw/openclaw/issues/160959) — Gateway blocks for minutes capturing large external plugins (2026.9.6 regression) | 81 | **Top pain point.** P0, diamond-lobster rating, crash-loop impact. The event loop starvation affects any user with large plugin/dependency trees. |
| [#164294](https://github.com/openclaw/openclaw/issues/164294) — WAL auto-checkpoint never completes (pinned by in-process reader) | 40 | Silent DB degradation; users see growing WAL (17k frames). |
| [#170956](https://github.com/openclaw/openclaw/issues/170956) — Windows: slow startup, unclean plugin shutdown, `schtasks` permission errors | 31 | P0, ux-release-blocker. Restarts/updates unreliable on managed-service installs. |
| [#168955](https://github.com/openclaw/openclaw/issues/168955) — Startup regression 2026.10.1: ~1030s vs ~410s ready (Windows) | 21 | Sidecar asset phase `control-ui-assets` blocks 477s. |
| [#162585](https://github.com/openclaw/openclaw/issues/162585) — Plugin source-capture re-materializes plugin into `package-0/node_modules/<self>`; trees grow linearly | 16 | Memory/disk blowup on Windows; gateway never healthy. |
| [#125764](https://github.com/openclaw/openclaw/issues/125764) — Telegram outbound sends dead-lettered after a single network failure | 14 | Silent message loss on high-value announce/completion replies. |
| [#144809](https://github.com/openclaw/openclaw/issues/144809) — claude-cli turns > `RUN_STALE_TAKEOVER_MS` lose entire reply | 14 | "No active tool authority snapshot" — full generated output discarded. |
| [#101929](https://github.com/openclaw/openclaw/issues/101929) — Context-overflow estimator over-counts 2.3–2.6x vs billed usage | 13 | Cost/token accounting mismatch; partially addressed by merged #168338. |

**Underlying needs:** (a) predictable Gateway startup regardless of plugin size; (b) durable, retried outbound delivery for messaging adapters; (c) Windows parity with Linux/macOS for service management; (d) honest token accounting. These are all *correctness-of-operation* asks, not feature asks — a maturity signal.

---

## 5. Bugs & Stability

Ranked by severity (per labels and impact tags). **No fix PRs are linked to the top items**, which is the key risk.

**P0 / Release-blocking**
1. **#160959** — Gateway event-loop block during large-plugin capture (2026.9.6 regression). *No fix PR.* [link](https://github.com/openclaw/openclaw/issues/160959)
2. **#170956** — Windows managed-service restarts/updates unreliable (`schtasks` permission, unclean plugin shutdown). *No fix PR.* [link](https://github.com/openclaw/openclaw/issues/170956)
3. **#178441** — Owner Telegram conversation held by `providerReview` with no continuation/recovery; identity-alias false positive. *No fix PR.* [link](https://github.com/openclaw/openclaw/issues/178441)
4. **#194664** — Codex protected continuation blocks all follow-ups while session Stop reports no active run (2026.11.1). *No fix PR.* [link](https://github.com/openclaw/openclaw/issues/194664)
5. **#146791** — iOS App Store build never publishes `push.apns.register`; push/wake broken. *No fix PR.* [link](https://github.com/openclaw/openclaw/issues/146791)

**P1 / High**
- **#168955** — Windows startup regression (1030s vs 410s). *No fix PR.*
- **#162585** — Plugin source-capture staging explosion (290MB → 612MB+/24 min). *No fix PR.*
- **#125764** — Telegram single-attempt dead-lettering. *No fix PR.*
- **#144809** — claude-cli long-turn reply loss. *No fix PR.*
- **#181662** — npm update keeps Gateway stopped ~19 min in `activating`; downtime grows per release. *No fix PR.*
- **#170928** — Windows 2026.10.2 gateway takes 7–9 min to ready, then intermittently unresponsive. *No fix PR.*
- **#191818** — 2026.10.10 still ships claude-cli "no recorded inference account" automation failure; requests backport of #180492. *No fix PR.*
- **#194334** — MCP stdio runtimes permanently un-evictable when run context never clears (regression). *No fix PR.*

**Data-loss / session-state (P2) with wide blast radius**
- **#172986** — WebChat long-path rendering + intermittent payload mutation.
- **#141172** — Orphaned `agent.run.started` leaks, FK failures on trajectory flush.
- **#179676 (closed)** — Pasted text >1,000 chars lost on later turns (closed, presumed fixed).
- **#186973** — Stale update-history banner persists across restarts/reboot.

**Good news:** several correctness regressions were closed this window (#179481, #173095, #57278, #163125, #164907), and a compaction-caching fix (#197061) plus cache-correct manual compaction are in PR review. The **ratio of open P0/P1 bugs to open fix PRs remains unfavorable**, especially for Windows and messaging adapters.

---

## 6. Feature Requests & Roadmap Signals

Most-commented feature issues cluster around **operator control and multi-agent reliability**:

- **#192009** — Explicit authorization for cross-provider cron fallback accounts (OpenAI primary, xAI fallback). *Likely candidate for near-term merge* — low-risk config surface, clear user need.
- **#176273** — Native progress-card SDK + trusted-plugin admission for Claw Orchestrator. Security-boundary tagged; likely a **maintainer decision item**, not a quick fix.
- **#179954** — Configurable per-entry chunking paths for memory files beyond `MEMORY.md`/`USER.md`. *Linked PR open* — plausible next minor.
- **#170562** — Doctor recovery command for stranded requester settle wake (subagent run). Operator-recovery theme, pairs with #179481.
- **#143387** — Android notification QoL: session-aware alerts, source labels, cross-device suppression.
- **#154524** — Accessibility: expose chat messages as headings for screen-reader navigation.
- **#169428** — Expose serving auth profile (`model.auth_label`) in `/usage full` footer.

**Prediction:** expect #192009 and #179954-class items to land before any new large surface area. The plugin-SDK/security items (#176273, #164355 hook extension point) are the most likely to slip due to review depth.

---

## 7. User Feedback Summary

**Pain points (dissatisfaction):**
- **Windows users carry disproportionate pain** — startup times, service restarts, plugin staging blowups, `schtasks` permissions. Repeated phrasing like "pas na veel proberen" (only after many retries) signals real operational fatigue.
- **Silent message loss** on Telegram and claude-cli is the most trust-eroding class reported — replies generated but never delivered, with no user-visible error.
- **Upgrade downtime growth** (#181662) is an emerging complaint as releases accumulate.
- **Token/cost accounting distrust** (#101929) — users checking billed usage see a 2.3–2.6x over-count.

**Satisfaction indicators:**
- High-quality, well-instrumented bug reports (crash-loop labels, repro steps, version hashes) suggest an engaged, technically sophisticated user base invested in the project.
- Recent closures of pasted-text persistence, sidebar cold-start, and Google Chat 403 issues show maintainers are responsive to well-scoped reports.

**Use cases surfaced:** multi-agent rosters with cron/agentTurn automation, heavy multi-channel deployments (Telegram groups, Signal, Mattermost, Tlon), iOS/Mac client pairing, and plugin-heavy workspaces — the last being exactly where the top P0 lives.

---

## 8. Backlog Watch

Issues/PRs with age, high severity, and no linked fix — the maintainer attention list:

1. **#160959** (created 2026-09-29, **81 comments**, P0, no fix PR) — the single highest-leverage stability item; a months-old regression still open.
2. **#164294** (2026-10-03, 40 comments, P2 but silent DB degradation) — WAL checkpoint starvation; needs a decision on reader pinning.
3. **#125764** (2026-08-18, P1, 14 comments) — Telegram retry/dead-letter architecture; oldest unresolved message-loss item.
4. **#91931** (2026-06-10, P1, diamond lobster) — Preseeded SOUL/IDENTITY/USER files auto-delete user `BOOTSTRAP.md` before first run. Over **5 months old** with fix-shape-clear label and no merged fix — a data-loss-adjacent onboarding bug that deserves a decision.
5. **#132767** (2026-08-29, P2, diamond lobster) — CLI host omits assemble-before-prompt; systemPromptAddition reaches model one turn late; earlier fix PR (#100984) closed unmerged. **Orphaned fix.**
6. **#144736** (PR, open since 2026-09-11, P2, size M) — sandbox `scp` transfer without deadline leaks child processes. Stale PR with sufficient proof; low-risk merge candidate.
7. **#165805** (PR, open since 2026-10-05) — node directory listing pagination fix; needs-proof status but long-stale.
8. **#146791** (2026-09-13, P0, iOS APNs relay) — App Store push registration broken; no fix PR and mobile wake is a core value prop.

**Recommendation:** triage #160959, #91931, and #146791 to explicit owners this week; the first two are the clearest cases of "high-rated, old, unfixed" that erode community confidence despite otherwise strong throughput.

---

*Data source: github.com/openclaw/openclaw activity window 2026-11-11 → 2026-11-12. Comment counts reflect issue/PR threads; PR comment counts were null in the supplied dataset and are omitted.*

---

## Cross-Ecosystem Comparison

⚠️ Comparative analysis generation failed.

---

## Peer Project Reports

<details>
<summary><strong>Hermes Agent</strong> — <a href="https://github.com/nousresearch/hermes-agent">nousresearch/hermes-agent</a></summary>

# Hermes Agent — Project Digest for 2026-11-12

## 1. Today's Overview

Hermes Agent remains in a high-velocity, mostly-maintenance phase: 50 issues and 50 PRs were touched in the last 24 hours, with no new releases cut. Activity skews toward the plugin catalog (a large share of today's PRs are catalog pins/additions, likely driven by the ongoing Plugin Hackathon), alongside a substantial stacked refactor moving core modules into the `hermes_agent/` package ([#153151](https://github.com/NousResearch/hermes-agent/pull/153151), [#153154](https://github.com/NousResearch/hermes-agent/pull/153154), [#153155](https://github.com/NousResearch/hermes-agent/pull/153155), [#153160](https://github.com/NousResearch/hermes-agent/pull/153160)). Notably, 18 issues closed today, many of them from a security-audit campaign (EPIC #82591), indicating active triage of a backlog rather than purely new work. Overall health: **active and responsive, but carrying a meaningful tail of gateway/session-state and sandboxing bugs** that recur in user reports.

## 2. Releases

No new releases in the last 24 hours. Nothing to report on version changes, breaking changes, or migration notes.

## 3. Project Progress

Closed issues today were dominated by the security-audit campaign and dependency hygiene:
- [#16560](https://github.com/NousResearch/hermes-agent/issues/16560) — command injection via `shell=True` in `tui_gateway/server.py` (security).
- [#127882](https://github.com/NousResearch/hermes-agent/issues/127882) — `browser_vault_enter_code` TOTP origin-binding gap (P1 security).
- [#84253](https://github.com/NousResearch/hermes-agent/issues/84253), [#84269](https://github.com/NousResearch/hermes-agent/issues/84269), [#84256](https://github.com/NousResearch/hermes-agent/issues/84256) — profile/principal scoping of response history, run registries, and idempotency cache.
- [#106775](https://github.com/NousResearch/hermes-agent/issues/106775), [#103798](https://github.com/NousResearch/hermes-agent/issues/103798), [#51292](https://github.com/NousResearch/hermes-agent/issues/51292), [#78543](https://github.com/NousResearch/hermes-agent/issues/78543) — dependency/CVE and container-image advisories.
- [#62402](https://github.com/NousResearch/hermes-agent/issues/62402) — desktop approval card placeholder fix.

On the PR side, a large batch of **plugin-catalog changes** merged/closed today: [#153388](https://github.com/NousResearch/hermes-agent/pull/153388) (aamio 0.7.42), [#153377](https://github.com/NousResearch/hermes-agent/pull/153377) (mediarag 0.1.21), [#153373](https://github.com/NousResearch/hermes-agent/pull/153373) (nexus 0.22.48), [#153365](https://github.com/NousResearch/hermes-agent/pull/153365) (lumina), [#153363](https://github.com/NousResearch/hermes-agent/pull/153363) (phone-senses), [#153357](https://github.com/NousResearch/hermes-agent/pull/153357) (twring), plus [#153856](https://github.com/NousResearch/hermes-agent/pull/153856) fixing a background-review budget-stop edge case.

## 4. Community Hot Topics

| Item | Signal | Underlying need |
|---|---|---|
| [#153396](https://github.com/NousResearch/hermes-agent/issues/153396) Team Gateway: BYO Slack app per agent (8 comments, 7 👍) | Highest-engagement open issue | Users want per-agent bot identity/avatar and control over sleep/suspend behavior; today's shared-bot relay is a usability and cost concern. |
| [#142488](https://github.com/NousResearch/hermes-agent/issues/142488) Desktop model switch crash on `prepare_model` (7 comments) | Persistent crash, open since 2026-10-21 | Desktop reliability for profile-following sessions; a regression blocking normal model switching. |
| [#131326](https://github.com/NousResearch/hermes-agent/issues/131326) Sandboxed browser can't find Chromium (7 comments) | Documented-default-path breakage | Headless browser tooling in Docker sandbox is effectively broken on arm64; high impact for browser-agent users. |
| [#96027](https://github.com/NousResearch/hermes-agent/issues/96027) Gateway SQLite fd leak (7 comments) | Long-running stability | Gateway process slowly exhausts file descriptors over weeks; a poor fit for always-on deployments. |
| [#153727](https://github.com/NousResearch/hermes-agent/issues/153727) GIL-starved gateway event loop w/ 15 profiles (6 comments) | Perf/liveness | Multiplexed multi-profile deployments hit watchdog restarts and 30s timeouts under CPU pressure. |

**Pattern:** the most-discussed items cluster around the **gateway** — multi-profile scaling, fd/GIL resource leaks, and per-agent identity. Slack/Team Gateway configurability is the standout product request.

## 5. Bugs & Stability

Ranked by severity (as reported):

- **P1 / Security:** [#127882](https://github.com/NousResearch/hermes-agent/issues/127882) (TOTP origin binding) and [#44730](https://github.com/NousResearch/hermes-agent/issues/44730) (SimpleX allowlist bypass via display name) — both closed today; verify fixes landed in release notes.
- **P2 / Crash:** [#142488](https://github.com/NousResearch/hermes-agent/issues/142488) — desktop model switch raises `JsonRpcGatewayError`; open, no linked fix PR seen.
- **P2 / Functional:** [#131326](https://github.com/NousResearch/hermes-agent/issues/131326) — sandboxed Chromium not found; `AGENT_BROWSER_EXECUTABLE_PATH` dropped on sandbox path.
- **P2 / Stability:** [#96027](https://github.com/NousResearch/hermes-agent/issues/96027) — SQLite fd leak on `state.db`/`-wal` (48/46 fds after 22 days); interacts with launchd's 256-fd limit.
- **P2 / Perf:** [#153727](https://github.com/NousResearch/hermes-agent/issues/153727) — GIL starvation → exit 75 restarts, `session.resume` timeouts.
- **P2 / Logic:** [#73748](https://github.com/NousResearch/hermes-agent/issues/73748) — gateway turn lost after all model-chain failures (e.g., rate limit) with no retry; [#145920](https://github.com/NousResearch/hermes-agent/issues/145920) — background memory review cancelled by next message, never retried.
- **P2 / Billing:** [#152288](https://github.com/NousResearch/hermes-agent/issues/152288) — OpenRouter spend under-reported up to ~3x (model-level vs. endpoint pricing).
- **P3 / Platform:** [#152246](https://github.com/NousResearch/hermes-agent/issues/152246) (Windows Bitwarden SSH agent fails), [#135139](https://github.com/NousResearch/hermes-agent/issues/135139) (WhatsApp LID→phone resolution under non-owner profile).
- **UI:** [#148946](https://github.com/NousResearch/hermes-agent/issues/148946) (transcript image overlap), [#152279](https://github.com/NousResearch/hermes-agent/issues/152279) (Cmd+B sidebar contract) — low severity.

Fix PRs observed today primarily target the plugin catalog and a review-budget edge case; several of the high-impact bugs above **lack linked fix PRs** in today's data.

## 6. Feature Requests & Roadmap Signals

- **[#153396](https://github.com/NousResearch/hermes-agent/issues/153396) Team Gateway BYO Slack app** — strong 👍 signal; likely candidate for a near-term gateway release. The sleep/suspend interaction with relay-only messaging suggests a "bring your own token" identity feature.
- **[#153586](https://github.com/NousResearch/hermes-agent/issues/153586) `kanban_create` block reason/kind** — small CLI/cron enhancement, low risk; plausible quick win.
- **Plugin ecosystem expansion** — the hackathon submissions (lumina for Apple Photos, phone-senses for Android via Termux:API, twring for voice calls, habits, eselsbrücke/unblockbrücke/forgebrücke, ShapeRotator CAD) signal a roadmap toward **device/peripheral and self-hosted integrations** rather than core-only features.
- **Multi-profile scaling** (implied by #153727, #96027, #135139) — likely a refactor/performance theme rather than a single feature.

## 7. User Feedback Summary

**Pain points:**
- Always-on deployments (Slack/Team Gateway, launchd, Docker) expose resource-management weaknesses (fd leaks, suspend semantics, GIL starvation).
- Desktop reliability regressions (model switching, approval card rendering, transcript image painting) hurt everyday users.
- Sandbox/browser setup on arm64 remains fragile despite being the documented default.
- Cost transparency: OpenRouter spend reporting is inaccurate, which undermines trust in billing.
- Platform-specific friction: Windows SSH + Bitwarden agent, WhatsApp alias resolution under secondary profiles.

**Satisfaction signals:** Enthusiastic plugin contributions and hackathon participation indicate a healthy contributor community; users describe the project positively ("awesome project," "self-evolution concept is fascinating") even when filing security reports. No release-churn complaints surfaced today.

## 8. Backlog Watch

Long-lived, high-impact items that would benefit from maintainer attention:

- [#142488](https://github.com/NousResearch/hermes-agent/issues/142488) — open since **2026-10-21**, no fix PR; desktop model-switch crash.
- [#131326](https://github.com/NousResearch/hermes-agent/issues/131326) — open since **2026-10-02**; sandboxed browser breakage.
- [#96027](https://github.com/NousResearch/hermes-agent/issues/96027) — open since **2026-08-27**; slow fd leak (needs sustained-load repro).
- [#94353](https://github.com/NousResearch/hermes-agent/issues/94353) — open since **2026-08-25**; skill-approval deadlock (scanner verdict treated as hard failure with no override surface).
- [#73748](https://github.com/NousResearch/hermes-agent/issues/73748) — open since **2026-07-29**; lost in-flight gateway turns after provider exhaustion.

*Data notes: "Comments: undefined" appears for some PRs in the source feed; those are excluded from the hot-topic ranking. Item counts refer to the 24-hour window ending 2026-11-12.*

</details>

<details>
<summary><strong>IronClaw</strong> — <a href="https://github.com/nearai/ironclaw">nearai/ironclaw</a></summary>

No activity in the last 24 hours.

</details>

<details>
<summary><strong>QwenPaw</strong> — <a href="https://github.com/agentscope-ai/QwenPaw">agentscope-ai/QwenPaw</a></summary>

# QwenPaw Project Digest — 2026-11-12

## 1. Today's Overview

QwenPaw is in an active stabilization phase following the v2.3.0 stable release, with 33 PRs and 8 issues updated in the last 24 hours. The release introduced Piccolo local decision models, but the stable release's installation verification (#8681) is still open past its 4-hour deadline, which suggests possible platform verification friction. A notable cluster of channel-plugin startup failures (Feishu on Windows, Matrix multi-workspace, WeChat on Linux) points to systemic issues in the new plugin-based channel architecture. Positively, several targeted fix PRs addressing these regressions were opened and some already merged today, indicating responsive maintainer engagement. Overall activity is high, but stability signals are mixed: the channel plugin migration is clearly still maturing.

## 2. Releases

**v2.3.0 (Stable)** — Published, with installation verification issue #8681 still open.
- **Added:** Piccolo 4B, 9B, and 27B as local decision models for tool review and context compaction, with BF16 and Q8_0 recommendations.
- **Migration notes:** Supports runtime installation/updates and downloads via Hugging Face or ModelScope; users must have local inference runtime available.
- **Breaking changes:** None explicitly documented in the release notes, but the channel plugin architecture (introduced in the 2.3.0 beta line) is a de facto breaking operational change — see Bugs section.
- Links: [Release v2.3.0](https://github.com/agentscope-ai/QwenPaw/releases/tag/v2.3.0) · [Verification issue #8681](https://github.com/agentscope-ai/QwenPaw/issues/8681)

## 3. Project Progress

Merged/closed PRs today (8 total, notable items):
- **[#8675](https://github.com/agentscope-ai/QwenPaw/pull/8675)** (CLOSED) — Diagnoses channel startup failures and fixes Windows Feishu native library loading (PyCryptodome path with `\\?\` prefix + `..` causing `CHANNEL_NOT_CONNECTED`). Directly addresses the top open bug #8669.
- **[#8680](https://github.com/agentscope-ai/QwenPaw/pull/8680)** (CLOSED) — Fixes Feishu import timeout in Windows desktop.
- **[#8677](https://github.com/agentscope-ai/QwenPaw/pull/8677)** (CLOSED) — Extends macOS release build timeout to 90 minutes (CI reliability).
- **[#8542](https://github.com/agentscope-ai/QwenPaw/pull/8542)** (CLOSED) — Release notes update for v2.3.0.

In-flight feature work (open PRs advancing):
- **Plugin extraction refactors:** MCP into plugin ([#8469](https://github.com/agentscope-ai/QwenPaw/pull/8469)), third-party agent into plugins ([#8426](https://github.com/agentscope-ai/QwenPaw/pull/8426)), local model management into plugin ([#8499](https://github.com/agentscope-ai/QwenPaw/pull/8499)) — a coordinated architectural push toward a modular core.
- **Local engine stack (3 stacked PRs):** llama.cpp GPU controls + auto-update ([#8649](https://github.com/agentscope-ai/QwenPaw/pull/8649)), managed MLX ([#8647](https://github.com/agentscope-ai/QwenPaw/pull/8647)), Console controls ([#8648](https://github.com/agentscope-ai/QwenPaw/pull/8648)).

## 4. Community Hot Topics

- **[Issue #8669](https://github.com/agentscope-ai/QwenPaw/issues/8669)** (4 comments) — Feishu channel fails on every startup in packaged Windows desktop builds; `_wait_connected` treats initial `disconnected` state as fatal. **Underlying need:** users of bundled desktop builds need deterministic channel startup ordering, not race-dependent behavior. A fix PR (#8675) already exists.
- **[Issue #8538](https://github.com/agentscope-ai/QwenPaw/issues/8538)** (4 comments) — Matrix plugin fails when multiple workspaces enable it; the 10s shared startup window only covers the first workspace. **Underlying need:** multi-tenant/multi-workspace users need per-workspace startup budgets or concurrent activation.
- **[PR #8236](https://github.com/agentscope-ai/QwenPaw/pull/8236)** — Ollama context window sizing from `num_ctx` instead of 128k default. **Underlying need:** local-model users are hitting truncation because context discovery assumes cloud-sized windows.
- **[PR #8409](https://github.com/agentscope-ai/QwenPaw/pull/8409)** — QQ C2C streaming message support. **Underlying need:** parity of streaming UX across Chinese IM platforms.

## 5. Bugs & Stability

Ranked by severity:

1. **[#8616](https://github.com/agentscope-ai/QwenPaw/issues/8616) — CRITICAL regression:** 2.3.0-beta.1 fails to launch on Windows 11 IoT LTSC ("path not found" → AppHang), while 2.2.2 works on the same machine. No fix PR identified yet; release-blocking for that OS profile.
2. **[#8669](https://github.com/agentscope-ai/QwenPaw/issues/8669) — HIGH:** Feishu channel fails on *every* start in packaged desktop builds (CHANNEL_NOT_CONNECTED). **Fix exists:** [#8675](https://github.com/agentscope-ai/QwenPaw/pull/8675) (closed/merged today) and [#8680](https://github.com/agentscope-ai/QwenPaw/pull/8680).
3. **[#8682](https://github.com/agentscope-ai/QwenPaw/issues/8682) — HIGH:** Official channel plugin workers crash on Linux (`Could not determine home directory`) — neither launcher nor worker bootstrap delivers HOME/QWENPAW_WORKING_DIR. No fix PR yet; affects WeChat channel plugin migration.
4. **[#8538](https://github.com/agentscope-ai/QwenPaw/issues/8538) — HIGH:** Matrix plugin activation for multiple workspaces (only first covered by 10s window). No fix PR yet.
5. **[#8674](https://github.com/agentscope-ai/QwenPaw/issues/8674) — MEDIUM (resource leak):** Query-error diagnostic dumps write full conversation snapshots with no size/count/age/rate limits, growing temp directory to hundreds of MB. **Fix PR open:** [#8678](https://github.com/agentscope-ai/QwenPaw/pull/8678) (bounds dumps and retention).
6. **[#8663](https://github.com/agentscope-ai/QwenPaw/issues/8663) — LOW/MEDIUM (browser-specific):** Safari does not render message-queue action buttons (edit/interrupt & send/delete); Chrome works. No fix PR yet.

**Pattern observation:** three of the top bugs share a root cause category — the new channel-plugin architecture's startup/lifecycle handshake (env delivery, connection-state timing, per-workspace windows). This is the dominant stability theme this cycle.

## 6. Feature Requests & Roadmap Signals

- **Local model engine maturity:** The stacked trio [#8649](https://github.com/agentscope-ai/QwenPaw/pull/8649)/[#8647](https://github.com/agentscope-ai/QwenPaw/pull/8647)/[#8648](https://github.com/agentscope-ai/QwenPaw/pull/8648) (llama.cpp GPU controls, managed MLX, Console UI) strongly signals the next minor release will center on local inference ergonomics — likely landing in a v2.4 line.
- **Channel streaming parity:** QQ C2C streaming ([#8409](https://github.com/agentscope-ai/QwenPaw/pull/8409)) and DingTalk Markdown table rendering ([#8601](https://github.com/agentscope-ai/QwenPaw/pull/8601)) indicate continued investment in IM platform output fidelity.
- **Response integrity UX:** [#8676](https://github.com/agentscope-ai/QwenPaw/pull/8676) adds notification when output limits are reached and fixes empty-response handling as false success — a user-facing trust improvement likely to ship soon.
- **Plugin modularization:** The three "extract into plugin" PRs (#8426, #8469, #8499) predict a configurable/minimal-core distribution direction for the next major cycle.

## 7. User Feedback Summary

- **Pain points:** Windows desktop packaging reliability is the loudest theme (Feishu native library loading, beta launch hang, import timeouts). Users upgrading from 2.2.x to 2.3.x are reporting channel regressions, especially on Windows and multi-workspace setups. A Chinese-language report (#8674) highlights unbounded diagnostic dumps in a real enterprise-style integration ("WorkMate"), showing adoption beyond hobbyist use.
- **Use cases observed:** multi-workspace Matrix deployments (#8538), Telegram/Feishu/WeChat/QQ/DingTalk channel operators, local Ollama and llama.cpp users, and scheduled-task users whose failing runs amplify the diagnostic-dump leak.
- **Satisfaction signals:** Detailed, well-formed bug reports with repro steps and version matrices suggest an engaged, technical user base; quick maintainer fix PRs for Feishu Windows issues indicate reasonable responsiveness. Dissatisfaction concentrates on 2.3.0's channel-plugin migration friction and the open release verification (#8681) missing its deadline.

## 8. Backlog Watch

- **[#8616](https://github.com/agentscope-ai/QwenPaw/issues/8616)** (created 11-07, 3 comments) — Windows 11 IoT LTSC launch regression. Highest severity, no fix PR linked yet; needs maintainer triage and a platform-specific workaround or rollback guidance for affected users.
- **[#8538](https://github.com/agentscope-ai/QwenPaw/issues/8538)** (created 11-02, 4 comments) — Multi-workspace Matrix activation. Open for 10 days; requires a design decision on startup budget allocation.
- **[#8682](https://github.com/agentscope-ai/QwenPaw/issues/8682)** (created 11-11, 1 comment) — Linux channel worker HOME/env bootstrap. New but affects official plugin workers broadly; needs a fix before channel-plugin migration is complete.
- **[PR #8426](https://github.com/agentscope-ai/QwenPaw/pull/8426) & [#8469](https://github.com/agentscope-ai/QwenPaw/pull/8469)** (opened 10-26/10-28, size XXXL) — Large architectural plugin-extraction PRs open for 2+ weeks without visible review comments; these carry merge-order dependencies in the local-engine stack and could become merge bottlenecks.
- **[PR #8236](https://github.com/agentscope-ai/QwenPaw/pull/8236)** (opened 10-14) — Ollama `num_ctx` fix under review for nearly a month; long-lived and relevant to a large local-model user segment.

</details>

<details>
<summary><strong>ZeroClaw</strong> — <a href="https://github.com/zeroclaw-labs/zeroclaw">zeroclaw-labs/zeroclaw</a></summary>

# ZeroClaw Project Digest — 2026-11-12

## 1. Today's Overview

ZeroClaw is in a high-throughput maintenance phase ahead of the imminent v0.9.0 release: no new releases shipped today, but a v0.9.0 release candidate (`1a79f89d1e`) is clearly being stabilized, evidenced by translation pinning and a security dependency bump merged today. Issue activity is light (7 updated, all still open) but concentrated on high-severity runtime/daemon defects, several reported by the same production user, suggesting real deployment stress. PR volume is very heavy (50 touched in 24h; 46 open), dominated by long-running XL feature branches from a small set of prolific contributors, with only 4 merged/closed. Overall health is **mixed-to-positive**: velocity is high and release prep is visible, but reviewer bandwidth appears to be a bottleneck — many PRs carry `needs-author-action`/`needs-maintainer-review` tags and have been open for weeks. No crashes or security incidents beyond one already-patched advisory.

## 2. Releases

**No new releases today.** However, release-prep signals for **v0.9.0** are strong:
- PR #12380 (merged) pins the `docs/book/po` translation catalogues to v0.9.0, preparing the docs build for the tagged release.
- PR #12392 (merged) bumps `rustix` to 1.1.6 for **RUSTSEC-2026-0486** (xattr read vulnerability affecting 1.0.0–1.1.5) — a security fix expected to land in the next tag.

No breaking-change or migration notes to report yet.

## 3. Project Progress

Only 4 PRs were merged/closed in the last 24h:

- **[MERGED] #12392** — `fix(deps): bump rustix to 1.1.6 for RUSTSEC-2026-0486` ([link](https://github.com/zeroclaw-labs/zeroclaw/pull/12392)). Security hardening; closes a known advisory ahead of release.
- **[MERGED] #12380** — `chore(docs): pin translations to v0.9.0` ([link](https://github.com/zeroclaw-labs/zeroclaw/pull/12380)). Release-readiness for documentation.
- **[CLOSED] #12215** — Dependabot rust-all group (36 updates), superseded by **#12393** (35 updates) ([link](https://github.com/zeroclaw-labs/zeroclaw/pull/12393)).
- **[OPEN, advanced] #12395** — docs correction to the shell-grammar binding row, a prerequisite for the security-sensitive #11994 ([link](https://github.com/zeroclaw-labs/zeroclaw/pull/12395)).

Net: feature advancement was minimal today; the visible work was release hygiene and dependency/security housekeeping. The large feature branches (#11847 XMPP, #9809 multi-model providers, #10391 delegate hardening) remain open and unmerged.

## 4. Community Hot Topics

Activity is measured in comments; reaction counts are uniformly zero, which is notable (low community 👍 engagement despite high issue volume).

- **[#7461](https://github.com/zeroclaw-labs/zeroclaw/issues/7461)** — *Run the test suite on Windows and macOS in CI, not just Linux* (6 comments, in-progress, risk:high). Longest-running active issue (open since June). Underlying need: **cross-platform correctness guarantees** — Lint/Build already run on a 3-OS matrix but tests do not, so Windows/macOS regressions can slip through. Given multiple Windows-specific bugs reported today (#12391), this is now evidenced as a real gap.
- **[#9814](https://github.com/zeroclaw-labs/zeroclaw/issues/9814)** — *Native XMPP / Prosody channel* (4 comments, accepted, parking-lot). Need: **self-hosted, low-resource deployments** for home-lab users who want a lightweight chat backend instead of Matrix/Telegram. Paired with an open implementation PR #11847.
- **[#8794](https://github.com/zeroclaw-labs/zeroclaw/issues/8794)** — *Stopping the agent mid-work erases tool calls and thinking from context* (4 comments, P1, S1). Need: **reliable interruption semantics** — aborted turns should preserve partial reasoning/tool history, not silently discard it.

The most-discussed PRs are all from a single prolific author (Audacity88): #12258 (`sessions_prompt` inter-agent tool), #11730 (foreground delegate tool-call visibility), #11698 (stable personality files for prompt-cache). The recurring theme: **multi-agent orchestration and prompt-cache efficiency** are the community's dominant engineering interests.

## 5. Bugs & Stability

Ranked by severity, all reported/updated today:

1. **[#8794](https://github.com/zeroclaw-labs/zeroclaw/issues/8794) — S1, workflow blocked, P1.** Stopping the agent mid-work discards all tool calls and thinking from context, as if the step never happened. High user impact; no fix PR linked yet.
2. **[#12391](https://github.com/zeroclaw-labs/zeroclaw/issues/12391) — S2, degraded behavior.** `runtime-trace.jsonl` loses **hours** of events across daemon restarts (handoff buffer/queue drops/silent writer). Reproduced on a production **Windows service, 0.8.7**. Observability/audit integrity risk; no fix PR linked.
3. **[#12396](https://github.com/zeroclaw-labs/zeroclaw/issues/12396) — config correctness.** `history_pruning.keep_recent` and `collapse_tool_results` are **dead config knobs** (no readers since #8196). Silent misconfiguration — users believe they're tuning memory behavior when they aren't. No fix PR linked.
4. **[#12390](https://github.com/zeroclaw-labs/zeroclaw/issues/12390) — diagnostics quality.** The `shell` tool returns an **empty error string** on non-zero exit; stderr is swallowed, so the model only learns "something failed." Directly degrades agent self-correction. Version 0.8.7.

Observation: three of four bugs (#12391, #12396, #12390) plus the S2 trace gap originate from small numbers of reporters on 0.8.7, all in runtime/daemon/shell paths — a cluster worth a focused triage. Only #8794 has a stated severity escalation.

## 6. Feature Requests & Roadmap Signals

- **XMPP/Prosody native channel** ([#9814](https://github.com/zeroclaw-labs/zeroclaw/issues/9814), accepted, parking-lot) — implementation PR #11847 is open and updated today. **Likely for v0.10+**, not v0.9.0, given parking-lot status and XL size.
- **Windows/macOS CI test matrix** ([#7461](https://github.com/zeroclaw-labs/zeroclaw/issues/7461), in-progress) — small scope, high leverage; plausible **v0.9.x**.
- **Tool-ceiling sealing for SOP step agents** ([#12394](https://github.com/zeroclaw-labs/zeroclaw/issues/12394)) — security-adjacent hardening of delegated MCP tools; likely tied to the broader delegate-policy work in PR #10391.
- **Inter-agent session prompting** ([#12258](https://github.com/zeroclaw-labs/zeroclaw/pull/12258)) and **async `sessions_message` scaffold** ([#12259](https://github.com/zeroclaw-labs/zeroclaw/pull/12259)) — a coordinated multi-agent messaging direction (RFC #11027 / tracker #11119).
- **Multiple models per provider profile** ([#9809](https://github.com/zeroclaw-labs/zeroclaw/pull/9809)) — large schema-v4 rework; if merged, a major config-surface change to watch for migration notes.

**Prediction:** v0.9.0 likely ships with the docs pin, rustix security fix, and assorted small fixes already merged; larger items (XMPP, multi-model providers, session tools) slip to v0.10 given `needs-author-action`/review backlog.

## 7. User Feedback Summary

- **Pain: loss of work on interruption** (#8794) — users expect partial thinking/tool output to survive a stop; its loss makes long agent runs feel unreliable.
- **Pain: observability gaps in production** (#12391) — a real Windows-service operator reports provably-continuing work that leaves no trace, undermining debugging and audit.
- **Pain: silent misconfiguration** (#12396) — documented knobs that do nothing erode trust in config docs.
- **Pain: opaque shell failures** (#12390) — agents can't self-diagnose command failures, forcing manual intervention.
- **Demand: self-hosting / low-resource deployments** (#9814) — home-lab users want lightweight chat transports.
- **Satisfaction signal:** continued investment in CI robustness (#7461) and prompt-cache stability (#11698) shows engaged power users caring about performance and cross-platform parity. **Dissatisfaction signal:** three distinct silent-failure classes surfaced in one day on 0.8.7, plus a P1 unresolved since July.

## 8. Backlog Watch

Long-open, important items needing maintainer attention:

- **[#7461](https://github.com/zeroclaw-labs/zeroclaw/issues/7461)** — Windows/macOS CI tests; open since **2026-06-10** (~5 months), in-progress. Directly implicated by today's Windows bug #12391.
- **[#8794](https://github.com/zeroclaw-labs/zeroclaw/issues/8794)** — P1/S1 mid-work context erasure; open since **2026-07-07**. Highest-severity open bug with no linked fix PR.
- **[#9814](https://github.com/zeroclaw-labs/zeroclaw/issues/9814)** — XMPP channel; open since **2026-08-07**, parking-lot; implementation #11847 stalled at needs-maintainer-review.
- **[#9809](https://github.com/zeroclaw-labs/zeroclaw/pull/9809)** — Multi-model-per-provider profile; open since **2026-08-07**, XL, `needs-author-action` — high-value but seemingly stalled.
- **[#10391](https://github.com/zeroclaw-labs/zeroclaw/pull/10391)** — Bounded delegate workspace/tool-ceiling/command-policy fix; open since **2026-08-26**, `needs-author-action`, risk:high. Security-relevant and repeatedly rebased — a strong merge candidate once review clears.
- **[#11730](https://github.com/zeroclaw-labs/zeroclaw/pull/11730) / [#11698](https://github.com/zeroclaw-labs/zeroclaw/pull/11698)** — delegate tool-call visibility and prompt-cache stability, both `needs-author-action` since mid-October.

**Health note:** reviewer bandwidth, not contributor interest, is the binding constraint. Clearing the `needs-author-action` queue (especially #10391 and #9809) and triaging the four runtime bugs would materially improve project health heading into v0.9.0.

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar).*