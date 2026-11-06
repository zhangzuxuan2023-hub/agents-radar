# OpenClaw Ecosystem Digest 2026-11-07

> Issues: 377 | PRs: 500 | Projects covered: 5 | Generated: 2026-11-06 23:16 UTC

- [OpenClaw](https://github.com/openclaw/openclaw)
- [Hermes Agent](https://github.com/nousresearch/hermes-agent)
- [IronClaw](https://github.com/nearai/ironclaw)
- [QwenPaw](https://github.com/agentscope-ai/QwenPaw)
- [ZeroClaw](https://github.com/zeroclaw-labs/zeroclaw)

---

## OpenClaw Deep Dive

# OpenClaw Project Digest — 2026-11-07

## 1. Today's Overview

OpenClaw remains in a high-velocity but stability-constrained phase: 377 issues and 500 PRs were updated in the last 24 hours, with 217 open/active issues and 361 open PRs versus 139 merged/closed PRs — a merge-to-open ratio that suggests review capacity is the bottleneck, not contributor supply. No new releases shipped, meaning the project is between release trains (current stable line is 2026.10.10, with 2026.11.5-beta.1 in prerelease). The dominant theme across today's activity is **regressions and lifecycle/resource-management defects** — gateway event-loop blocking, SQLite worker OOM, MCP child-process leaks, WAL checkpoint starvation, and prompt-cache invalidation — several of which are P0/P1 and marked as release blockers. Community engagement is workmanlike rather than euphoric: high comment counts on hard bugs (80 comments on one P0), near-zero reaction counts overall, and a large "stale" backlog of unresolved older reports.

## 2. Releases

No new releases in the last 24 hours. Context for readers tracking versions:
- Current stable referenced across issues: **2026.10.10**
- Fixes mentioned as shipping only in prerelease: **2026.11.5-beta.1** (e.g., #141474, #193962)
- Multiple open regressions are attributed to specific shipped versions (2026.9.5, 2026.9.6, 2026.9.8, 2026.10.5, 2026.10.10)

## 3. Project Progress

No merged-PR list was provided, but closed PRs today (139 merged/closed total) include several notable fixes:

- **[PR #194341](https://github.com/openclaw/openclaw/pull/194341) (closed)** — Honor trusted admin authority for eligible Access programs (security-boundary tagged).
- **[PR #194332](https://github.com/openclaw/openclaw/pull/194332) (closed)** — Fix Control UI input recall missing prompts after long answers (fixes #194327).
- **[PR #193450](https://github.com/openclaw/openclaw/pull/193450) (closed)** — iOS Files now show accurate read errors instead of false "binary/oversized" messages (fixes #193347).
- **[PR #194312](https://github.com/openclaw/openclaw/pull/194312) (closed)** — Hide snoozed pinned sessions in UI until wake.
- Closed issues with linked fix PRs: **#166017** (MCP `sessionIdleTtlMs` default not applied — spawn broker cap 256), **#178291** (2026.10.4→10.5 post-activation Doctor failure), **#164923** (short-term memory promotion 0/512 asymmetry), **#168794** (Codex relay 32 MiB image limit → 502s).

**In-flight feature work visible in open PRs:** Prometheus provider usage-window metrics ([#141276](https://github.com/openclaw/openclaw/pull/141276)), plugin-SDK access-policy revision publishing ([#182383](https://github.com/openclaw/openclaw/pull/182383)), skill bundle import from URLs ([#192781](https://github.com/openclaw/openclaw/pull/192781)), and session archive read-cache byte bounding ([#193343](https://github.com/openclaw/openclaw/pull/193343)).

## 4. Community Hot Topics

Ranked by comment activity:

1. **[#160959](https://github.com/openclaw/openclaw/issues/160959) — P0, 80 comments** — Gateway blocks for minutes capturing large external plugins (2026.9.6 regression). The single hottest thread; a confirmed release-blocking event-loop stall tied to plugin capture (#144252). High urgency, needs maintainer review.
2. **[#164294](https://github.com/openclaw/openclaw/issues/164294) — 39 comments** — SQLite WAL auto-checkpoint never completes, pinned by an in-process reader (17,091 log frames vs 866 checkpointed). Classic reader-starvation pathology.
3. **[#177656](https://github.com/openclaw/openclaw/issues/177656) — P1, 29 comments** — Native host exec returns shared-state "integrity admission" error on 2026.10.5 (≥28 failures).
4. **[#150635](https://github.com/openclaw/openclaw/issues/150635) — 25 comments** — Memory dreaming deep phase never promotes: short-term recall retention evicts recalled entries nightly. Memory-system correctness, diamond-lobster rated.
5. **[#191944](https://github.com/openclaw/openclaw/issues/191944) — 23 comments** — macOS `gateway stop` / `stop --force` both fail with "Process ancestry could not be fully inspected."

**Underlying needs:** The community is heavily invested in **operational reliability of the Gateway as a long-running daemon** — resource containment (plugins, MCP children, SQLite readers) and clean lifecycle commands. Memory-system behavior (dreaming/promotion/retention) is the second cluster, indicating that memory is a headline differentiator users are stress-testing in production.

## 5. Bugs & Stability

Severity-ranked, with fix-PR status noted:

**P0**
- **[#160959](https://github.com/openclaw/openclaw/issues/160959)** — Gateway event-loop block for minutes on large plugin capture (2026.9.6 regression). No fix PR linked. **Release blocker.**
- **[#178291](https://github.com/openclaw/openclaw/issues/178291)** — Closed today: 2026.10.4→10.5 chat update fails post-activation Doctor (offline-maintenance authority). Update-path blocker, resolved.

**P1 (regressions / crash-loops / message loss)**
- **[#191847](https://github.com/openclaw/openclaw/issues/191847)** — 2026.10.10: large session repeatedly OOMs the 512 MB SQLite worker (`ERR_WORKER_OUT_OF_MEMORY`); every turn fails with "admission is closed."
- **[#192959](https://github.com/openclaw/openclaw/issues/192959)** — Gateway refuses to start after Kubernetes pod restart: client credential directory rejected after fsGroup remount. Deployment blocker for K8s users.
- **[#166641](https://github.com/openclaw/openclaw/issues/166641)** — `web_fetch` cold fallback synchronously captures unrelated plugins on the Gateway main thread, causing liveness stalls (V8 profile attached).
- **[#185656](https://github.com/openclaw/openclaw/issues/185656)** — Large zstd transcript archive still exceeds Node string limit in media-persistence migration (regression of #170847), blocks update.
- **[#157182](https://github.com/openclaw/openclaw/issues/157182)** — Control UI lands users on `/chat/main` and retries forever when ambient main session isn't visible (security-tagged).
- **[#162087](https://github.com/openclaw/openclaw/issues/162087)** — Scheduled announce fails for pre-9.7 account-mode jobs (missing ownerOrigin + direct-key parse gap).
- **[#126950](https://github.com/openclaw/openclaw/issues/126950)** — Telegram opaque callback payloads (`tgcb1:`) silently dropped for non-plugin callers (message loss).
- **[#155563](https://github.com/openclaw/openclaw/issues/155563)** — Service-child relay/anchor and MCP groups retained after successful agent turn (2026.9.5 regression).
- **[#193962](https://github.com/openclaw/openclaw/issues/193962)** — The header-probe timeout fix (#187197) is unreachable from 2026.10.10 because package-update runs the installed updater's 10s probe. **Fix exists but cannot be delivered — a release-channel deadlock.**
- **[#182647](https://github.com/openclaw/openclaw/issues/182647)** — Cloud-worker turns fail before inference: "Session capability metadata requires worker preparation."

**P2 highlights (selected)**
- **[#182891](https://github.com/openclaw/openclaw/issues/182891)** — Prompt cache never reused; `cache read dropped N -> 0` every request (cost impact). Related to **[#143017](https://github.com/openclaw/openclaw/issues/143017)** (21.3M cache-write tokens measured from prefix rewrites).
- **[#112638](https://github.com/openclaw/openclaw/issues/112638)** — `session.maintenance` enforce mode doesn't bound the store (thread/channel entries exempt from reclaim).
- **[#166964](https://github.com/openclaw/openclaw/issues/166964)** — Subagent completion delivery fails permanently on requester authority revocation, no reason logged.
- **[#123792](https://github.com/openclaw/openclaw/issues/123792)** — Assistant turns render twice with CLI backends (live per-block + aggregate record).
- **[#124306](https://github.com/openclaw/openclaw/issues/124306)** — Exec approval card flags syntactic risks but never that a target path is a mount point (data-loss surface).

**Stability meta-observation:** Recurring root-cause classes today are (a) synchronous main-thread work on the Gateway, (b) SQLite reader/worker resource limits, and (c) child-process lifecycle leaks. These are architectural, not one-off, and should inform the next release's hardening theme.

## 6. Feature Requests & Roadmap Signals

- **[#13219](https://github.com/openclaw/openclaw/issues/13219) — Per-model usage logging for cost tracking** (10 comments, 👍1, open since Feb 2026). Strong candidate: pairs naturally with the cost-visibility pain in prompt-cache bugs.
- **[#163739](https://github.com/openclaw/openclaw/issues/163739) — Make Ctrl+Shift+A configurable** (browser shortcut collision). Small, low-risk, likely to land soon.
- **[#166678](https://github.com/openclaw/openclaw/issues/166678) — Hide agents from picker while keeping them spawnable.** Directly serves multi-agent operators; likely next-cycle UX work.
- **[#158800](https://github.com/openclaw/openclaw/issues/158800) — Browse/fork prior channel-chat segments after `/new`.** Aligns with the session-history direction in the open UI PRs.
- **[#136271](https://github.com/openclaw/openclaw/issues/136271) — msteams: resolve team SharePoint site dynamically** for channel uploads.
- **[#164620](https://github.com/openclaw/openclaw/issues/164620) — Setting to disable hosted plugin catalog feed** (privacy/air-gap need; URL currently hardcoded).
- **[#79223](https://github.com/openclaw/openclaw/issues/79223) — Configurable Dream Diary language/prompt** (hardcoded English prompt in `memory-core`).
- **[#103067](https://github.com/openclaw/openclaw/issues/103067) — Centralize chat-session naming; define subagent session lifetime.** Architectural; would resolve a family of naming/persistence complaints.
- **[#178373](https://github.com/openclaw/openclaw/issues/178373) — Run-level turn/token budget; tool-loop detection on by default; stop re-billing retained history.** This is arguably the most strategically important request on the board — it is the cost-control counterpart to #143017/#182891.

**Prediction for next releases:** near-term patches will be dominated by regression fixes (MCP TTL, update-path probe, SQLite worker memory) rather than features. Feature-wise, expect the Prometheus usage-window exporter (#141276) and small UI ergonomics fixes (#163739, #194332-adjacent work) to ship before larger items like #178373 or the session-model refactor (#103067).

## 7. User Feedback Summary

**Pain points expressed by real users:**
- **Upgrade reliability is the #1 frustration.** Multiple reports describe upgrades that fail post-activation (#178291), block on migrations (#185656), or cannot even reach their own fix (#193962). Users on production installs are effectively pinned.
- **Resource leaks erode trust in long-running Gateways.** Commenters quantify the damage themselves: ~144–145 MB per stdio MCP child (#164690), duplicated MCP spawns under `claude-cli` (#166017, #164690), cap-256 broker exhaustion (#166017). This is a sophisticated, production-operator audience.
- **Memory system behavior is confusing and lossy.** Users report `memory promote` returning zero candidates in one workspace while siblings promote 50–90% (#164923), and nightly dreaming that never promotes (#150635) — the flagship "dreaming" feature is not delivering visible value to some users.
- **Silent failures and opaque errors** recur as a theme: dropped Telegram callbacks (#126950), generic "changed while starting work" with no reason code across 16 call sites (#127195), unlabeled Code Mode internal errors (#167609), and revocations with no logged reason (#166964). Users are doing the diagnostic work the product should do.
- **Cost anxiety is tangible**: users are hand-measuring cache-write tokens (#143017) and pleading for per-model usage logs (#13219).
- **Mobile/UI friction** (tiny phone composer #139909, chat reload backtracking PR #193217, duplicate renders #123792) is a steady secondary stream.

**Satisfaction signal:** comment counts on hard bugs are high while reaction counts are near zero — users are engaged and diligent but not cheerleading. People clearly run OpenClaw in serious production configurations (Kubernetes, multi-agent orgs of 15 agents, Docker sandboxes), which is both a compliment to the project's capability and the source of its stability debt.

## 8. Backlog Watch

Long-lived, high-value items still open and needing maintainer attention:

- **[#13219](https://github.com/openclaw/openclaw/issues/13219)** — Per-model usage logging; open since **2026-02-10**. Cheapest high-impact cost feature available.
- **[#79223](https://github.com/openclaw/openclaw/issues/79223)** — Configurable Dream Diary language; open since **2026-05-08**. Simple fix (move a hardcoded prompt), persistent international-user friction.
- **[#103067](https://github.com/openclaw/openclaw/issues/103067)** — Centralize session naming / subagent lifetime; open since **2026-07-09**, needs product decision. Architectural debt that keeps generating smaller bugs.
- **[#112638](https://github.com/openclaw/openclaw/issues/112638)** — Session maintenance doesn't bound the store; open since **2026-07-22**. Silent unbounded growth for multi-tenant gateways.
- **[#127195](https://github.com/openclaw/openclaw/issues/127195)** — Sixteen identical error sites with no reason code (since **2026-08-21**, fix-shape-clear). A "diagnosability" quick win.
- **[#143017](https://github.com/openclaw/openclaw/issues/143017)** — Prompt-cache prefix rewriting, 21.3M cache-write tokens measured; open since **2026-09-09** with full live reproduction. Directly burns user money.
- **[#123792](https://github.com/openclaw/openclaw/issues/123792)** — Duplicate assistant-turn rendering with CLI backends; open since **2026-08-14**.
- **[#141474](https://github.com/openclaw/openclaw/issues/141474)** — `sessions_yield` strands `agents_wait` forever; remaining recovery ships only in `v2026.11.5-beta.1`. Maintainer action: ensure it reaches a **stable** release.

**Notable stale-but-important:** #160959 (P0) is correctly being worked, but #112638 and #13219 have sat for months with product-decision tags — these are the most likely candidates for the next maintainer triage sweep.

---

*Data source: github.com/openclaw/openclaw, issues/PRs updated 2026-11-06, digest generated 2026-11-07. Rankings based on comment counts as provided; closed items retained where they explain today's fixes.*

---

## Cross-Ecosystem Comparison

# Cross-Project Comparison Report — Personal AI Assistant / Agent Open-Source Ecosystem
**Snapshot date: 2026-11-07 | Projects: OpenClaw, Hermes Agent, IronClaw, QwenPaw, ZeroClaw**

---

## 1. Ecosystem Overview

The agent framework ecosystem is in a **post-feature, pre-hardening phase**: all four active projects have shipped their headline capabilities (multi-agent orchestration, memory/dreaming, plugins, channel gateways) and are now paying down the resulting stability debt. Activity is dominated by regressions and lifecycle bugs rather than new features, with security/authority models emerging as the new battleground (ZeroClaw) and operational reliability of long-running gateways as the universal pain point. The user base has matured into production operators running Kubernetes, multi-agent organizations, and hardened Docker deployments — their feedback is quantitative and diagnostic, not aspirational. Ecosystem health is bifurcated: contributor supply is strong everywhere, but **maintainer review capacity is the binding constraint**, evidenced by merge-to-open ratios well below 1.0 and months-old decision-blocked PRs. No project shipped a stable release in the window, suggesting the ecosystem is collectively between release trains.

---

## 2. Activity Comparison

| Project | Issues (open / total active 24h) | PRs (open / merged-closed 24h) | Release Status | Health Score* | Notes |
|---|---|---|---|---|---|
| **OpenClaw** | 217 open / 377 touched | 361 open / 139 merged-closed | Stable 2026.10.10; 2026.11.5-beta.1 in prerelease; **no release today** | **B–** | Highest absolute volume; review bottleneck acute (0.39 merge ratio); multiple P0 release blockers |
| **Hermes Agent** | 49 open / 50 touched | 45 open / 5 merged-closed | Digesting v0.22.3–v0.23; **no release today** | **C+** | Severe throughput imbalance (0.11 merge ratio); 1 closure vs 49 open; P0 Windows data-loss risk |
| **IronClaw** | 0 | 0 | None | **N/A** | No activity in 24h — dormant or between cycles |
| **QwenPaw** | ~18 open / 22 touched | 18 open / 20 merged-closed | 2.3.0b2 console / 2.4 backend metadata; **no release today** | **A–** | Best throughput balance (~1:1); multiple long-stale issues finally closed |
| **ZeroClaw** | 13 open / 25 touched | 37 open / 13 merged-closed | v0.9.0 release-gate underway; **no release today** | **B** | Strong security responsiveness; release-gate PRs (e.g., #9746) stalled 3 months |

*\*Health score is a composite judgment of throughput balance, severity of open blockers, release cadence, and maintainer responsiveness — not a formal metric.*

**Key takeaway:** QwenPaw is the only project with near-parity between incoming and outgoing work. OpenClaw and Hermes both show review-capacity starvation; ZeroClaw is gated on a single large security PR; IronClaw is silent.

---

## 3. OpenClaw's Position

**Advantages vs peers:**
- **Scale of community:** 377 issues + 500 PRs updated in 24h is roughly **7–10× the activity of Hermes or QwenPaw** and ~15× ZeroClaw. OpenClaw has the deepest contributor pool and the most sophisticated production operators (K8s deployments, 15-agent orgs, Docker sandboxes).
- **Broadest surface area:** memory/dreaming system, MCP integration, plugin SDK, multi-channel (Telegram, msteams, iOS), cloud workers, Code Mode, and a Control UI — no peer matches this breadth.
- **Reference status:** positioned as the "core reference" implementation; peers implicitly benchmark against it.

**Technical approach differences:**
- OpenClaw runs a **long-lived Gateway daemon** as its architectural center — this is the source of both its power (persistent sessions, scheduled jobs, channel fan-in) and its stability debt (event-loop blocking, SQLite worker OOM, MCP child-process leaks). Peers like QwenPaw are more request/session-oriented with a lighter runtime footprint.
- Heavier reliance on **SQLite + worker processes** and **prompt caching** for cost control — both currently pathological (cache invalidation #182891, worker OOM #191847), which is a cost and reliability exposure Hermes/ZeroClaw don't share at the same scale.
- Mature **plugin SDK + hosted catalog** (with an open request #164620 to disable the catalog feed for air-gapped use) — a governance surface peers are still building.

**Community size comparison:** OpenClaw's comment activity on single hard bugs (80 comments on #160959) exceeds the *entire daily engagement* of QwenPaw or ZeroClaw. Hermes's top thread (#97681, 40 comments) is half of OpenClaw's hottest thread. OpenClaw is the ecosystem's gravitational center; the risk is that its review capacity is not scaling with its community.

---

## 4. Shared Technical Focus Areas

The following needs recur across **three or more** projects, indicating ecosystem-wide requirements:

| Requirement | OpenClaw | Hermes | QwenPaw | ZeroClaw | Specific Manifestations |
|---|---|---|---|---|---|
| **Session/state persistence & synchronization** | SQLite worker OOM (#191847), WAL starvation (#164294) | state.db corruption (#147938), group-chat sync (#143303/#143302), session DB backend seam (#150332) | Windows `recall_history` break (#8585), chat-state cache (#8596) | Append-only event history (#10526), durable peer turns (#9597), idle RPC leak (#11962) | *Universal:* all four are rebuilding session state as a first-class, durable, cross-surface concern |
| **Authority / approval governance** | Exec approval gaps (#124306), Access-program authority (#194341) | Approval-gate bypass (#150329), Desktop approval modal (#84189), write-scope (#142933) | Provider config validation (#8549) | S0/S1 ownership bypasses (#11127, #11123, #12046, #12260), cron approval (#11149) | *Universal but most acute in ZeroClaw:* users want visible, enforceable control over what agents do and write |
| **Silent failure → diagnosability** | 16 identical error sites (#127195), dropped Telegram callbacks (#126950) | Silent drops (#148831), "NOT installed" misreport (#130072) | Empty api_key accepted (#8276), byte-dropping decode (#8234) | SOPs never load (#9779), quickstart misreports (#12155) | *Universal:* every project's users are doing diagnostic work the product should do |
| **Resource containment / lifecycle** | MCP child leaks (#164690, #166017), plugin capture stall (#160959) | Unbounded proposal queue (#75130), delegate context floor (#108891) | Matrix multi-workspace startup (#8538) | Idle RPC sessions never released (#11962), channel startup race (#12279) | *Universal:* long-running daemons leak; no peer has solved lifecycle hygiene |
| **Cost visibility / cache efficiency** | Prompt-cache invalidation (#182891, #143017), per-model usage logging (#13219) | — | — | Prompt-cache order bug (#12176) | *OpenClaw- and ZeroClaw-specific:* cache-driven cost anxiety is where prompt caching is used heavily |
| **Upgrade/update reliability** | Update-path probe deadlock (#193962), migration blocks (#185656) | Windows updater stale-version (#143656), stale-snapshot restore (#147938) | Version skew across 2.2/2.3/2.4 (#8580) | — | *Strong in OpenClaw/Hermes/QwenPaw:* updaters are the weakest link in the trust chain |

**Synthesis:** The ecosystem has converged on **four shared pain clusters** — durable session state, enforceable authority, diagnosable failures, and lifecycle resource hygiene. These are architectural, not one-off, and represent the next generation of framework work.

---

## 5. Differentiation Analysis

| Dimension | OpenClaw | Hermes Agent | QwenPaw | ZeroClaw |
|---|---|---|---|---|
| **Feature focus** | Broadest platform: memory/dreaming, MCP, plugins, multi-channel, cloud workers | Multi-agent collaboration across gateways (#97681), Desktop/Group Chat, plugin catalog | Channel plugins (Matrix/IM), memory retrieval/reranker, Creator UI | Security-first: principal/authority model, SOPs, cron approval, Rust/WASM UI |
| **Target users** | Production operators at scale (K8s, multi-agent orgs) | Multi-surface teams (Desktop + Webapp/PWA + mobile), bot operators | Enterprise IM users (Feishu/DingTalk/Matrix), Chinese Windows users, offline/air-gapped | Security-conscious deployments, proxy/gateway operators |
| **Technical architecture** | Long-lived Gateway daemon; SQLite + workers; heavy prompt caching; plugin SDK | Gateway + Desktop/Webapp surfaces; session-DB backend seam heading toward PostgreSQL (#150332) | Lighter request/session runtime; channel-plugin model; dynamic MCP | Rust-based; event-sourced session history direction (#10526); strict authority enforcement; Windows/Unix |
| **Release philosophy** | Beta trains alongside stable; rapid patches | Rapid minor surface expansion (v0.22→v0.23), high regression load | Console beta line + backend metadata; near-1:1 triage | Security-gate cuts (v0.9.0) blocking on large XL PRs |
| **Ecosystem model** | Hosted plugin catalog (opt-out requested) | Community plugin catalog (many PRs) | Plugin packages + Creator | Plugin registry origins (#12216), multi-model profiles (#9809) |

**Summary:** OpenClaw is the **platform play** (breadth + scale), Hermes is the **collaboration/surface play** (multi-gateway, multi-surface), QwenPaw is the **integration/localization play** (channels, Windows, offline), and ZeroClaw is the **security/architecture play** (Rust, authority, event sourcing). They are complementary more than directly competitive — but all compete for the same "personal agent runtime" slot.

---

## 6. Community Momentum & Maturity

**Activity tiers (by 24h touched items):**
- **Tier 1 — Hyperactive:** OpenClaw (~877 items). Highest momentum, highest review debt.
- **Tier 2 — Active:** Hermes (~100), QwenPaw (~60), ZeroClaw (~75). Healthy but manageable volume.
- **Tier 3 — Dormant:** IronClaw (0).

**Rapidly iterating:**
- **Hermes** — fastest surface expansion (Desktop, PWA, mobile, group chats), but paying for it in regressions and a 0.11 merge ratio. Iterating faster than it can stabilize.
- **QwenPaw** — iterating *and* closing; best triage cadence; multiple first-time contributors landing fixes.

**Stabilizing / hardening:**
- **OpenClaw** — between release trains, absorbing regressions; the next release will be patch-dominated (MCP TTL, update probe, SQLite memory).
- **ZeroClaw** — release-gated on v0.9.0 security cut; closed many S0/S1 findings same-day, indicating maturity in *security response* even while the authorization model itself is immature.

**Maturity signals:**
- **Highest maturity:** OpenClaw (scale, broadest operators, but strained review) and QwenPaw (balanced throughput).
- **Growing pains:** Hermes (review bottleneck, Windows data-integrity risk).
- **Security maturing fastest:** ZeroClaw (adversarial auditing accepted and closed rapidly — a sign of healthy triage over a model that shipped with gaps).
- **Unknown:** IronClaw (insufficient signal).

---

## 7. Trend Signals — Value for AI Agent Developers

Extracted from cross-project community feedback, ranked by strategic importance:

1. **Runtime reliability is the new feature.** Every project's top bugs are daemon-level: event-loop blocking, SQLite worker OOM, child-process leaks, idle-session leaks, startup races. **Developers should treat lifecycle resource management as a first-class design concern**, not an afterthought. Expect framework-level solutions (supervisors, bounded workers, backpressure) to differentiate winners.

2. **Authority and approval governance is the next competitive surface.** ZeroClaw's S0 ownership bypasses, Hermes's approval-gate bypass and Desktop approval requests, and OpenClaw's exec-approval gaps all point the same way: users want **visible, enforceable, diff-based control** over agent actions and memory/skill writes. Products that surface approval in the UI (not just CLI) and enforce at the write path will win trust.

3. **Session state is being re-architected as durable and replayable.** OpenClaw (SQLite worker/WAL), Hermes (state.db + PostgreSQL seam #150332), ZeroClaw (event-sourced history #10526, durable peer turns #9597), and QwenPaw (chat-state cache) are all converging on **event-sourced or append-only, cross-surface session stores**. This is the single biggest architectural trend in the window.

4. **Cost visibility and cache correctness are monetizable pain.** OpenClaw users hand-measure 21.3M cache-write tokens (#143017) and plead for per-model usage logs (#13219); ZeroClaw fixed a prompt-cache-order bug (#12176). **Prompt-cache discipline and usage metering are now table-stakes** for any framework used in production.

5. **Silent failures are the top trust-killer.** Across all projects, the most-cited frustration is not crashes but *silent* drops, misreports, and no-reason errors (OpenClaw #127195, Hermes #148831/#130072, QwenPaw #8276/#8234, ZeroClaw #9779/#12155). **Fail-fast validation with actionable reason codes is a cheap, high-impact differentiator.**

6. **Upgrade paths are the weakest link.** Update-path deadlocks (OpenClaw #193962), migration blocks (#185656), stale-snapshot restores (Hermes #147938), and version skew (QwenPaw 2.2/2.3/2.4). **Reliable, reversible, self-verifying updaters are an underserved requirement** — a viable investment area for tooling vendors.

7. **Multi-surface and cross-machine collaboration is the demand frontier.** Hermes's #97681 (40 comments, needs-decision) and the Desktop/group-chat regression cluster; OpenClaw's multi-agent operator requests (#166678 hide-from-picker, #103067 subagent lifetime). **Users are moving from single-agent to coordinated multi-agent, multi-surface deployments** faster than the frameworks' session/authority models can support.

8. **Localization and offline/air-gapped operation are real markets.** QwenPaw's offline-install (#1453), Windows/GBK issues (#8234, #8585), and OpenClaw's hosted-catalog-disable request (#164620) signal demand from **regulated, air-gapped, and non-English production environments** — an underserved segment.

**Bottom line for developers:** The ecosystem's center of gravity has shifted from *what agents can do* to *whether they can be trusted to keep doing it* — durably, safely, visibly, and affordably. Frameworks that solve session durability, authority enforcement, diagnosability, and update reliability will define the next release cycle; feature breadth is already commoditized.

---

*Report compiled from the 2026-11-07 digests of OpenClaw, Hermes Agent, IronClaw, QwenPaw, and ZeroClaw. Activity counts are 24-hour snapshots as reported; health scores are analyst composites.*

---

## Peer Project Reports

<details>
<summary><strong>Hermes Agent</strong> — <a href="https://github.com/nousresearch/hermes-agent">nousresearch/hermes-agent</a></summary>

# Hermes Agent — Project Digest
**Date: 2026-11-07**

---

## 1. Today's Overview

Hermes Agent remains in a high-velocity but issue-heavy phase: 50 issues and 50 PRs were updated in the last 24h, with 49 issues still open versus only 1 closure, and 45 open PRs against 5 merged/closed. No new release shipped today, and the project is still digesting the v0.22.3–v0.23 surface (Docker gateway, Desktop, Webapp/PWA, mobile beta) where most regressions cluster. Activity is dominated by long-running collaboration infrastructure work (#97681) and a steady stream of community plugin-catalog submissions, suggesting a healthy ecosystem but a maintainer review bottleneck. Overall health reads as **active and expanding, with elevated review debt and several P0/P2 correctness issues in session/state handling** that warrant attention before the next cut.

---

## 2. Releases

**None.** No new releases were published in the last 24h. (Latest referenced in issues: v0.22.3, v0.22.5, and a 0.23 Webapp.)

---

## 3. Project Progress

Merged/closed activity was light today (5 PRs merged/closed, 1 issue closed):

- **#143656 [CLOSED]** — Linux updater restart-before-install-stamp bug (gateway latching stale version in `/health`) was closed. [Issue #143656](https://github.com/NousResearch/hermes-agent/issues/143656)
- **#150348 [CLOSED]** — `fix(agent): compact bulky history in short conversations` was closed (not merged as-is); addresses context-window pressure when protected-head preferences + minimum-message gate block compression. [PR #150348](https://github.com/NousResearch/hermes-agent/pull/150348)

**In-flight work advancing (open PRs updated today):**
- **#150332** — session-database backend seam / portable-SQL groundwork (stage 1 of PostgreSQL state backend, RFC #23717). [PR #150332](https://github.com/NousResearch/hermes-agent/pull/150332)
- **#143334** — `fix(profiles)`: renamed profiles keep local chats (fixes `profile_mismatch` after `hermes profile rename`). [PR #143334](https://github.com/NousResearch/hermes-agent/pull/143334)
- **#149467** — gateway migration fix so Desktop Group Chats don't vanish on upgrade to `state.db`. [PR #149467](https://github.com/NousResearch/hermes-agent/pull/149467)
- **#149656** — Desktop SDK session tile docking / read-only pane layout for plugins (enables tiling session managers). [PR #149656](https://github.com/NousResearch/hermes-agent/pull/149656)
- **#150333** — `fix(agent)`: fire `post_api_request` for length-stopped attempts. [PR #150333](https://github.com/NousResearch/hermes-agent/pull/150333)

A notable share of PR volume is **plugin-catalog additions** (#149624, #150351, #150346, #150347, #150164, #150350, #150349, #150344, #150343, #149657) — ecosystem growth rather than core changes.

---

## 4. Community Hot Topics

| Item | Signal | Underlying need |
|---|---|---|
| **[#97681](https://github.com/NousResearch/hermes-agent/issues/97681)** — Let Bots collaborate across gateways (40 comments, 4 👍, `needs-decision`) | By far the most-discussed item | Users want **multi-agent collaboration across machines/owners without surrendering bot control**; foundational architecture debate (sessions, message delivery, gateway state). Design decision pending. |
| **[#75130](https://github.com/NousResearch/hermes-agent/issues/75130)** — Skill-proposal queue grows unbounded, 21% dead (7 comments) | Long-running bug | `skills.write_approval` workflow produces **unbounded pending queues that self-invalidate**; users need lifecycle/GC for proposals. |
| **[#84189](https://github.com/NousResearch/hermes-agent/issues/84189)** — Desktop inline approve-and-edit modal for memory/skill writes (4 👍, `needs-decision`) | High reaction-to-comment ratio | Approval workflows are **CLI-only today**; Desktop users want visible diff/approve/edit surface. |
| **[#147938](https://github.com/NousResearch/hermes-agent/issues/147938)** — Windows state.db "corruption" = live lock; restores stale snapshot (P0) | Severity-driven attention | Data-integrity trust on Windows updates. |

**Analysis:** The community's center of gravity is shifting from single-agent features to **coordination (cross-gateway bots, group chats) and governance (approval surfaces, skill/memory write review)**. The 4-reaction #84189 and 40-comment #97681 both point at the same theme: *users want control and visibility over what their agents do and write.*

---

## 5. Bugs & Stability

Ranked by severity (fix PR noted where it exists):

**P0 — Critical**
- **[#147938](https://github.com/NousResearch/hermes-agent/issues/147938)** (Windows): `state.db` verify mistakes a **live lock for corruption**, restores a months-stale snapshot over healthy data, and misreports the restore as failed. *Data-loss risk.* No linked fix PR in today's set.

**P2 — High**
- **[#142982](https://github.com/NousResearch/hermes-agent/issues/142982)** — Desktop cannot unpin legacy cron-run chat; pin silently returns. [No fix PR listed]
- **[#147626](https://github.com/NousResearch/hermes-agent/issues/147626)** — Attached clients see turn stream but **never the originating user prompt** (multi-surface sync). [No fix PR listed]
- **[#143303](https://github.com/NousResearch/hermes-agent/issues/143303)** — Group-chat Stop reports turn stopped while still running → overlapping turns, lost replies. [No fix PR listed]
- **[#143242](https://github.com/NousResearch/hermes-agent/issues/143242)** / **[#143241](https://github.com/NousResearch/hermes-agent/issues/143241)** — Duplicate gateway Group Chat rooms on retry / repeated "Start gateway group" clicks. [No fix PR listed]
- **[#143302](https://github.com/NousResearch/hermes-agent/issues/143302)** — Group-chat message sent as prior discussion settles **never gets a turn** (dropped message).
- **[#108891](https://github.com/NousResearch/hermes-agent/issues/108891)** — Delegate subagents capped at 20,000-char context floor (missing `context_length`). [No fix PR listed]
- **[#147923](https://github.com/NousResearch/hermes-agent/issues/147923)** — Desktop: assistant message after cron delivery not rendered until next turn.
- **[#150123](https://github.com/NousResearch/hermes-agent/issues/150123)** — Browser dashboard chat crash-loops (`gateway exited (1)`) on single-profile installs with active session. *(New today.)*

**P3 / Lower**
- **[#142743](https://github.com/NousResearch/hermes-agent/issues/142743)** — v0.22.3 Docker fresh gateway crash-loops on `unsafe_control_permissions` over its own `gateway.sock`.
- **[#141755](https://github.com/NousResearch/hermes-agent/issues/141755)** — Security scanner false-positives block legit community skill (`book-to-skill`, 132 findings).
- **[#150010](https://github.com/NousResearch/hermes-agent/issues/150010)** — Desktop TTS stalls ~1s at every sentence boundary. **Fix PR exists but is open and conflicting:** [#102928](https://github.com/NousResearch/hermes-agent/pull/102928).
- **[#149841](https://github.com/NousResearch/hermes-agent/issues/149841)** *(new)* — scratch prune deletes fresh copies when `mtime` is preserved (`cp -p`, `rsync -a`, `mv`).
- **[#150329](https://github.com/NousResearch/hermes-agent/issues/150329)** *(new, security)* — Approval gate bypass: shell function/alias hides the dangerous verb (`f(){ rm "$@"; }; f -rf /` passes hardline floor); also unflagged pipe-to-shell sinks.
- **[#148831](https://github.com/NousResearch/hermes-agent/issues/148831)** — `delegate_task` silently drops top-level routing field; child runs default route. *(Fix PR referenced by reporter.)*
- **[#143243](https://github.com/NousResearch/hermes-agent/issues/143243)** — Ended classic Group Chat resurrects on in-flight sync.
- **[#130072](https://github.com/NousResearch/hermes-agent/issues/130072)** — `hermes memory status` reports "NOT installed" when provider exists but fails to load.

**Assessment:** Stability burden is concentrated in **Desktop/gateway session-state synchronization** (multiple `dokterdok` reports) and **Windows update/state integrity**. Security-adjacent items (#150329, #141755) are worth triage given they touch the approval/guard boundary.

---

## 6. Feature Requests & Roadmap Signals

High-likelihood candidates for upcoming versions:

1. **Cross-gateway bot collaboration** — [#97681](https://github.com/NousResearch/hermes-agent/issues/97681) (`needs-decision`, most active): if approved, a multi-release architectural program (sessions, message delivery, gateway state flagged by sweepers).
2. **Ownership-scoped background skill writes** — [#142933](https://github.com/NousResearch/hermes-agent/issues/142933) (`skills.background_review_write_scope`): restrict background self-improvement to curator-managed skills. Likely small-to-medium config feature.
3. **Desktop inline approval/edit modal** — [#84189](https://github.com/NousResearch/hermes-agent/issues/84189): pairs naturally with #142933; high user demand.
4. **Email MCP integration package** — [#99030](https://github.com/NousResearch/hermes-agent/issues/99030) (AgentMail/IMAP/SMTP): ecosystem push toward first-class email.
5. **Read-only Kanban/run/memory API endpoints** — [#149815](https://github.com/NousResearch/hermes-agent/issues/149815): external integrations (Home Assistant "Hermes Voice") driving API surface requests.
6. **PWA installability for Webapp** — [#150345](https://github.com/NousResearch/hermes-agent/issues/150345): trivial (`manifest.json`), high mobile payoff — likely quick win.
7. **llama.cpp slot save/restore** — [#141605](https://github.com/NousResearch/hermes-agent/issues/141605) (local-model session continuity).
8. **Mobile voice bubbles + richer avatar** — [#148447](https://github.com/NousResearch/hermes-agent/issues/148447).
9. **One user-visible agent name everywhere** — [#146434](https://github.com/NousResearch/hermes-agent/issues/146434) (author wants to implement post-design).

**Prediction:** #150345 (PWA manifest) and #142933 (write-scope config) are the cheapest near-term wins; #84189 and #97681 are decision-gated and more likely to land in a following milestone.

---

## 7. User Feedback Summary

**Pain points:**
- **Data integrity & updates on Windows** (#147938): stale-snapshot restore erodes trust in the updater — the most safety-critical complaint.
- **Approval workflows are invisible on Desktop** (#84189, #142933): staged writes live only behind slash commands; users feel loss of control over agent self-modification.
- **Group-chat/Desktop sync is flaky** (#143303, #143302, #143242, #143241, #143243, #147626): duplicate rooms, dropped messages, phantom "stopped" turns — friction for multi-surface users.
- **Security scanner over-blocking** (#141755): legitimate community skills blocked, hurting ecosystem adoption.
- **Approval-gate bypasses** (#150329): security-minded users flag holes in hardline/permission detection.
- **Context/scale limits** (#108891 delegate 20k floor; #75130 unbounded proposal queue).

**Positive use cases & adoption:**
- External integrations being built on Hermes — **"Hermes Voice"** Home Assistant bridge (#149815), mobile Android beta feedback (#148447), PWA request (#150345) — show growing surface-area use.
- Vibrant **plugin ecosystem**: numerous catalog PRs (api-architect, BlazePhoenix, Construct, fal, CrashLab, Telos, lampstand, tiled-sessions, evalroute, lfg-alarm) indicate a healthy third-party developer community.

**Satisfaction:** Generally constructive; PR authors self-report AI-assisted, adversarially reviewed fixes (e.g., #148831) — a sign of engaged, technically sophisticated users. Dissatisfaction is mostly workflow friction and reliability, not fundamental capability gaps.

---

## 8. Backlog Watch

Long-unanswered or decision-blocked items needing maintainer attention:

- **[#75130](https://github.com/NousResearch/hermes-agent/issues/75130)** (open since 2026-07-31, 7 comments) — Unbounded/dead skill-proposal queue under `write_approval`; a long-standing reliability debt.
- **[#84189](https://github.com/NousResearch/hermes-agent/issues/84189)** (open since 2026-08-12, `needs-decision`, 4 👍) — Desktop approval modal; needs design decision.
- **[#97681](https://github.com/NousResearch/hermes-agent/issues/97681)** (open since 2026-08-29, 40 comments, `needs-decision`) — Cross-gateway collaboration; highest-engagement item but gated on decision.
- **[#86105](https://github.com/NousResearch/hermes-agent/pull/86105)** (open since 2026-08-14, `needs-decision`) — Force built-in memory enabled when external provider configured; small correctness fix awaiting decision.
- **[#85342](https://github.com/NousResearch/hermes-agent/pull/85342)** (open since 2026-08-13, `needs-decision`, security/compat risk) — Docker refuse persistent reuse on config drift; safety-relevant, stalled.
- **[#102928](https://github.com/NousResearch/hermes-agent/pull/102928)** — TTS prefetch fix; **open and conflicting with `main`**, blocking closure of #150010.
- **[#150329](https://github.com/NousResearch/hermes-agent/issues/150329)** *(new)* — Approval-gate bypass via function/alias; security triage recommended promptly.
- **#147938** (P0, Windows data integrity) — Needs immediate maintainer ownership given data-loss potential.

**Recommendation:** Prioritize triage of the P0 Windows state.db issue (#147938) and the security bypass (#150329), unblock #102928, and clear the `needs-decision` backlog (#97681, #84189, #86105, #85342), which is the primary drag on both merged-PR throughput and community sentiment.

---

*Digest generated from GitHub activity for 2026-11-07. Links reference the NousResearch/hermes-agent repository.*

</details>

<details>
<summary><strong>IronClaw</strong> — <a href="https://github.com/nearai/ironclaw">nearai/ironclaw</a></summary>

No activity in the last 24 hours.

</details>

<details>
<summary><strong>QwenPaw</strong> — <a href="https://github.com/agentscope-ai/QwenPaw">agentscope-ai/QwenPaw</a></summary>

# QwenPaw Project Digest — 2026-11-07

## 1. Today's Overview
QwenPaw shows **high maintenance velocity**: 22 issues and 38 PRs were touched in the last 24 hours, with a healthy 20 PRs merged/closed against 18 still open — a near 1:1 throughput-to-backlog ratio. Activity is dominated by **stability and correctness fixes** rather than new features: memory retrieval, provider config validation, channel plugins (Matrix), and Windows-specific path handling. No new release was cut, so the project sits on the `2.3.0b2` console line while `2.4` backend packages circulate in the field. Community engagement is modest per item (mostly 1–4 comments, zero reactions), indicating broad but shallow triage. Overall project health: **strong** — the maintainers are closing as fast as users file, and several long-standing issues (dating back to March 2026) were finally resolved.

---

## 2. Releases
**None.** No new releases in the last 24 hours. Note: the field is still split across `2.2.2`, `2.3.0b2` (console), and `2.4` backend metadata, and several bug reports stem from this version skew (e.g., #8580 reports UI v2.3.0b2 with backend 2.4 and `reme_ai` 0.4.2.0).

---

## 3. Project Progress
The following closed/merged items represent concrete forward motion today:

**Fixes landed (closed PRs / issues):**
- **Provider config validation** — [#8550](https://github.com/agentscope-ai/QwenPaw/pull/8550) (fixes [#8549](https://github.com/agentscope-ai/QwenPaw/issues/8549)): out-of-range `thinking_budget` / `max_input_length` are now rejected on the write path instead of silently persisting and breaking provider discovery after restart.
- **Matrix channel recovery** — [#8411](https://github.com/agentscope-ai/QwenPaw/pull/8411) (fixes [#8365](https://github.com/agentscope-ai/QwenPaw/issues/8365)): cached sessions restored and missing E2EE device keys re-uploaded after server-side device drops.
- **Third-party agent scheduled tasks** — [#8535](https://github.com/agentscope-ai/QwenPaw/pull/8535) (fixes [#8533](https://github.com/agentscope-ai/QwenPaw/issues/8533)): Codex/Qoder cron tasks no longer fail with `Third-party agent requests require text or attachments`.
- **Dynamic MCP persistence** — [#373](https://github.com/agentscope-ai/QwenPaw/pull/373) (first-time contributor, open since March): dynamically loaded MCP tools now persist across session turns.
- **Console chat-switch state** — [#8600](https://github.com/agentscope-ai/QwenPaw/pull/8600): `/clear` chats stay empty after reopening (fixes [#8596](https://github.com/agentscope-ai/QwenPaw/issues/8596)).
- **Test stability** — [#8604](https://github.com/agentscope-ai/QwenPaw/pull/8604), [#7858](https://github.com/agentscope-ai/QwenPaw/issues/7858): Windows shell cancellation hangs and unawaited-coroutine warnings resolved.

**Still under review (high-value):**
- [#8234](https://github.com/agentscope-ai/QwenPaw/pull/8234) — GBK/CP936 decoding in read/edit/grep (fixes silent byte loss on Chinese Windows).
- [#8457](https://github.com/agentscope-ai/QwenPaw/pull/8457) — automatic memory recall injected as reference context rather than synthetic assistant trajectory.
- [#8472](https://github.com/agentscope-ai/QwenPaw/pull/8472) — reranker model center with multi-query precision history.

---

## 4. Community Hot Topics
Engagement is spread thin; the most-commented items (4 comments each) cluster around memory tagging and chat-state behavior.

| Item | Comments | Link | Underlying Need |
|---|---|---|---|
| #8580 Memory tag index returns 503 | 4 | [link](https://github.com/agentscope-ai/QwenPaw/issues/8580) | Users enabling `auto_memory_generate_tags` expect tags to be **readable**, not just generated. Index rebuild reliability is a trust issue for the memory subsystem. |
| #8596 `/clear` message resurfaces | 4 | [link](https://github.com/agentscope-ai/QwenPaw/issues/8596) | Chat-state cache correctness — users rely on `/clear` semantics for privacy/reset workflows. |
| #1453 Offline install can't reach local Qwen | 4 | [link](https://github.com/agentscope-ai/QwenPaw/issues/1453) | **Offline/air-gapped deployment** is a real use case; local Qwen 3/3.5 connectivity parity with DeepSeek R1 matters. Closed after 8 months. |
| #8276 ReMe reranker accepts empty api_key | 4 | [link](https://github.com/agentscope-ai/QwenPaw/issues/8276) | Config validation gap causing cryptic "unexpected error" — users want fail-fast with actionable messages. |
| #7436 Compact tool call display in IM | 3 | [link](https://github.com/agentscope-ai/QwenPaw/issues/7436) | Chat-channel noisiness; enterprise IM (Feishu/DingTalk) users want terse rendering. |
| #5110 Quote-to-follow-up (Perplexity-style) | 3 | [link](https://github.com/agentscope-ai/QwenPaw/issues/5110) | Selection-based context injection is a common assistant UX expectation. |

**Observation:** A recurring theme is **"the feature exists but is unreadable/undiscoverable"** — memory tags generated but unreadable (#8580), default agent editable via API but disabled in UI (#8576), plugin settings routes losing nav context (#8590). This suggests a UI/backend contract drift worth a dedicated audit.

---

## 5. Bugs & Stability
Ranked by severity, with fix-PR status:

**Critical / Blocker**
1. **#8592 — Creator review round permanently undecidable** ([link](https://github.com/agentscope-ai/QwenPaw/issues/8592)): REJECT always returns 422 "visual artifact reference references missing id"; also bundles expired `source_url` 502 and `frame_stats` ignoring `CREATOR_FFMPEG_PATH`. **Hard blocker** — a review round can never be closed. No fix PR yet.
2. **#8603 — `run_code` invoked with empty args on custom OpenAI-compatible providers** ([link](https://github.com/agentscope-ai/QwenPaw/issues/8603)): every `run_code` call emitted as `{}` on `kimi` / `kimi-codingplan` with `k3.1-pro`; rejected at validation. **Blocks tool use entirely** for that provider config. No fix PR yet.
3. **#8580 — Memory tag index never rebuilds (503)** ([link](https://github.com/agentscope-ai/QwenPaw/issues/8580)): tags generated but unreadable. No fix PR yet.

**High**
4. **#8585 — `recall_history` broken on Windows** ([link](https://github.com/agentscope-ai/QwenPaw/issues/8585)): `\\?\` extended-length path breaks SQLite read-only URI. Cross-version (2.2.x onward). No fix PR.
5. **#8517 — Hub Docker fails as non-root** ([link](https://github.com/agentscope-ai/QwenPaw/issues/8517)): Permission denied on `config.json`. No fix PR — deployment blocker for hardened environments.
6. **#8538 — Matrix plugin fails when multiple workspaces enable it** ([link](https://github.com/agentscope-ai/QwenPaw/issues/8538)): 10s shared startup window only covers the first workspace. No fix PR yet (related #8411 closed a different Matrix issue).

**Medium**
7. **#8576 — Default agent edit disabled in console** ([link](https://github.com/agentscope-ai/QwenPaw/issues/8576)): **Fix PR exists**: [#8608](https://github.com/agentscope-ai/QwenPaw/pull/8608).
8. **#8595 — Plugin install fails ("No package metadata")** for `qwenpaw-creator` v2.0.4 ([link](https://github.com/agentscope-ai/QwenPaw/issues/8595)): relates to #8598 dependency-check refactor (closed). No direct fix PR confirmed.
9. **#8590 — Plugin settings route loses nav context** ([link](https://github.com/agentscope-ai/QwenPaw/issues/8590)): closed; no linked fix PR visible.

**Closed today (resolved):** #8549 (config validation, PR #8550), #8365 (Matrix keys, PR #8411), #8533 (cron requests, PR #8535), #8516 (SSE `X-Accel-Buffering` behind nginx), #7857 (ACP shutdown leak), #8596 (chat reopen).

---

## 6. Feature Requests & Roadmap Signals
| Request | Link | Signal Strength |
|---|---|---|
| `tool_call_format` config for compact IM tool display | [#7436](https://github.com/agentscope-ai/QwenPaw/issues/7436) | **Strong** — already implemented in open PR [#8610](https://github.com/agentscope-ai/QwenPaw/pull/8610). Likely in next minor. |
| Quote/reference text for follow-up context | [#5110](https://github.com/agentscope-ai/QwenPaw/issues/5110) | **Strong** — implemented in open PR [#8609](https://github.com/agentscope-ai/QwenPaw/pull/8609). |
| Browser tab title follows session name | [#8605](https://github.com/agentscope-ai/QwenPaw/issues/8605) | Moderate — small, well-scoped, multi-tab power users. |
| Creator UI fixes (button contrast, model/key independence) | [#8591](https://github.com/agentscope-ai/QwenPaw/issues/8591) | Moderate — UX debt in Creator; button is invisible on white bg. |
| Reranker model center | [#8472](https://github.com/agentscope-ai/QwenPaw/pull/8472) | Strong — large feature PR under review, likely 2.4.x. |

**Prediction for next version:** compact tool-call display (#8610), quote-to-follow-up (#8609), and default-agent editing (#8608) are all small, self-contained PRs ready to land — high probability for the next console release. Reranker model center (#8472) is larger and may slip.

---

## 7. User Feedback Summary
**Pain points:**
- **Version skew confusion** — multiple reports mix UI 2.3.0b2, backend 2.4, and `reme_ai` 0.4.2.0, making triage harder for users and maintainers alike.
- **Windows is a second-class citizen** — GBK encoding loss (#8234), `\\?\` path breakage (#8585), shell cancellation hangs (#8604). A dedicated Windows CI lane would pay dividends.
- **Silent failures** — empty `api_key` accepted (#8276), invalid config persisted (#8549), byte-dropping decode (#8234). Users consistently ask for **fail-fast validation with clear messages**.
- **UI/backend contract drift** — features work via API but are disabled or broken in UI (#8576, #8590, #8580).

**Positive signals:** Users are contributing fixes (multiple `first-time-contributor` PRs: #8606, #8550, #8535, #373), and long-stale issues are being closed (#1453 after 8 months, #373 after 8 months). Satisfaction appears **moderate-to-good** — frustration is concentrated in specific subsystems (memory index, Matrix, Creator) rather than the core agent loop.

---

## 8. Backlog Watch
Items needing maintainer attention:

- **[#373](https://github.com/agentscope-ai/QwenPaw/pull/373)** — Dynamic MCP per-session tool persistence. Open since **2026-03-02** (~8 months), first-time contributor, just closed today. Worth confirming the merge and thanking the contributor.
- **[#8234](https://github.com/agentscope-ai/QwenPaw/pull/8234)** — GBK/CP936 decoding fix. Open since **2026-10-14**, XXL size, under review. High user impact on Chinese Windows; needs review bandwidth.
- **[#8236](https://github.com/agentscope-ai/QwenPaw/pull/8236)** — Ollama `num_ctx` context sizing. Open since **2026-10-14**, XL. Local-model users are a growing segment.
- **[#8100](https://github.com/agentscope-ai/QwenPaw/pull/8100)** — Resolved media capabilities at runtime. Open since **2026-10-03**. Image-capable models silently rejected — correctness bug with a ready fix.
- **[#8274](https://github.com/agentscope-ai/QwenPaw/pull/8274)** — Preserve pending user input on chat switch. Open since **2026-10-16**. Data-loss-adjacent UX bug.
- **[#5110](https://github.com/agentscope-ai/QwenPaw/issues/5110)** — Quote-to-follow-up. Open since **2026-06-11** (~5 months); now has a PR (#8609), so closing the loop soon would be good hygiene.
- **[#8592](https://github.com/agentscope-ai/QwenPaw/issues/8592)** — Creator review blocker. Filed yesterday, no maintainer response yet — **highest-priority new arrival**.

**Recommendation:** Prioritize review of #8234 and #8100 (both ready, high-impact correctness fixes), and assign an owner to #8592 (Creator blocker) and #8585 (Windows `recall_history` regression, cross-version).

</details>

<details>
<summary><strong>ZeroClaw</strong> — <a href="https://github.com/zeroclaw-labs/zeroclaw">zeroclaw-labs/zeroclaw</a></summary>

# ZeroClaw Project Digest — 2026-11-07

## 1. Today's Overview

ZeroClaw is in a high-velocity security-hardening phase. Activity is very high: 25 issues and 50 PRs updated in the last 24 hours (13 issues closed, 13 PRs merged/closed, 37 PRs still open). The dominant theme is **identity/access control and authority enforcement** — multiple S0/S1-class findings (session-data ownership bypass, SOP wildcard tool selectors, cron approval bypass) are being closed, with corresponding fix PRs in review. A dependency security advisory (vodozemac) blocked CI and was remediated same-day. Overall project health is strong on responsiveness, but the volume of P1/risk:high security items landing in the `v0.9.0` milestone indicates a release-gate crunch is underway.

## 2. Releases

No new releases in the last 24h. Notably, several closed issues carry the `release:v0.9.0` label, indicating active release-gate work toward v0.9.0.

## 3. Project Progress

Closed issues today (13 total) include meaningful security and correctness fixes:

- **[#11127](https://github.com/zeroclaw-labs/zeroclaw/issues/11127)** [CLOSED, P1, S0] Session-data tools bypassed principal ownership checks (e.g., `sessions_history` readable cross-principal).
- **[#11123](https://github.com/zeroclaw-labs/zeroclaw/issues/11123)** [CLOSED, P1, S0] SOP execution accepted wildcard tool selectors without `tools:execute`.
- **[#12051](https://github.com/zeroclaw-labs/zeroclaw/issues/12051)** [CLOSED] `sop_workshop apply` allowed scoped principals to author shared SOPs executed with operator authority.
- **[#12046](https://github.com/zeroclaw-labs/zeroclaw/issues/12046)** [CLOSED] Wildcard tool selectors leaked principal-unaware tools (cron, schedule, peer messaging, SOP execution) to non-admin principals.
- **[#12272](https://github.com/zeroclaw-labs/zeroclaw/issues/12272)** [CLOSED] vodozemac 0.11.0 RUSTSEC advisories broke the Security quality gate; fixed via PR **[#12273](https://github.com/zeroclaw-labs/zeroclaw/pull/12273)** (bump to 0.11.2).
- **[#12176](https://github.com/zeroclaw-labs/zeroclaw/issues/12176)** [CLOSED] Deferred MCP tool list in HashMap order busted prompt caching each session.
- **[#11962](https://github.com/zeroclaw-labs/zeroclaw/issues/11962)** [CLOSED] Daemon's 64 live RPC sessions never released when idle.
- **[#12155](https://github.com/zeroclaw-labs/zeroclaw/issues/12155)**, **[#12178](https://github.com/zeroclaw-labs/zeroclaw/issues/12178)**, **[#12217](https://github.com/zeroclaw-labs/zeroclaw/issues/12217)**, **[#12248](https://github.com/zeroclaw-labs/zeroclaw/issues/12248)**, **[#12249](https://github.com/zeroclaw-labs/zeroclaw/issues/12249)** — assorted bug/doc/task closures (quickstart messaging, `deliver_file` description, email docs, approval gate for non-runnable tools, dashboard config-page visibility).

Key open PRs advancing major workstreams:

- **[#9746](https://github.com/zeroclaw-labs/zeroclaw/pull/9746)** Per-agent ownership scoping for session tools and `discord_search` (release-gate, XL).
- **[#11149](https://github.com/zeroclaw-labs/zeroclaw/pull/11149)** Enforce cron approval and authority at the write across RPC/tools/token revocation.
- **[#12239](https://github.com/zeroclaw-labs/zeroclaw/pull/12239)** Hold current authority through session effects (follow-up to #11234).
- **[#12270](https://github.com/zeroclaw-labs/zeroclaw/pull/12270)** Relay-served web dashboard per-node host (#10315 phase 2).
- **[#9809](https://github.com/zeroclaw-labs/zeroclaw/pull/9809)** Multi-model provider profiles (long-running, XL).

## 4. Community Hot Topics

Most active by comments/reactions:

1. **[#10526](https://github.com/zeroclaw-labs/zeroclaw/issues/10526)** (16 comments) — RFC: append-only session event history, deterministic replay, derived agent streams. **Closed** after review; signals the architecture direction toward event-sourced runtime state (complements durability work in #9597).
2. **[#8132](https://github.com/zeroclaw-labs/zeroclaw/issues/8132)** (13 comments, 1 👍) — Evaluate Rust/WASM UI before React/Vite migration. Long-running architectural debate about eliminating Node.js from build/runtime; currently needs-author-action.
3. **[#10926](https://github.com/zeroclaw-labs/zeroclaw/issues/10926)** (9 comments) — Matrix `send_via` treats peer identities as room destinations.
4. **[#9779](https://github.com/zeroclaw-labs/zeroclaw/issues/9779)** (7 comments, P1) — `sops_dir` documented default silently not honored by daemon.

**Underlying needs:** the community is pushing on (a) runtime observability/replayability for agent sessions, (b) supply-chain simplification (Node.js removal), and (c) consistent, documented behavior between config defaults and daemon startup paths.

## 5. Bugs & Stability

Ranked by severity (all reported/updated today unless noted):

| Severity | Issue | Status / Fix PR |
|---|---|---|
| **S1** | [#12272](https://github.com/zeroclaw-labs/zeroclaw/issues/12272) vodozemac RUSTSEC-2026-0445/-0451 breaks CI on all PRs | **Closed**; fix PR [#12273](https://github.com/zeroclaw-labs/zeroclaw/pull/12273) closed same day |
| **S1** | [#12263](https://github.com/zeroclaw-labs/zeroclaw/issues/12263) Strict provider permanently rejects long tool-heavy sessions (422 `tool_call_id`), conversation bricked | Open; no fix PR yet |
| **S2** | [#12260](https://github.com/zeroclaw-labs/zeroclaw/issues/12260) RPC `cron/add`/`cron/patch` treat request as operator shell approval | Open; covered by [#11149](https://github.com/zeroclaw-labs/zeroclaw/pull/11149) |
| **S2/S0** | [#12279](https://github.com/zeroclaw-labs/zeroclaw/issues/12279) Channel startup race can lose a message appended between transcript read and cap | Open; no fix PR |
| **S2** | [#9572](https://github.com/zeroclaw-labs/zeroclaw/issues/9572) Gateway WebSocket turns overflow Tokio worker stack (debug builds) | Open (P1, no-stale) |
| **S2** | [#12052](https://github.com/zeroclaw-labs/zeroclaw/issues/12052) Rate limiter keys on client-supplied first XFF hop → loopback exemption spoofable | Open; fix PR [#12114](https://github.com/zeroclaw-labs/zeroclaw/pull/12114) in review |
| **S2** | [#10926](https://github.com/zeroclaw-labs/zeroclaw/issues/10926) Matrix `send_via` misroutes to peer identities | Open |
| **P1** | [#9779](https://github.com/zeroclaw-labs/zeroclaw/issues/9779) SOPs silently never load when relying on documented `sops_dir` default | Open |

**Assessment:** Security-class issues are closing fast; functional S1s (#12263 bricked sessions) and startup data-loss (#12279) are the highest residual risk without fix PRs.

## 6. Feature Requests & Roadmap Signals

- **[#12181](https://github.com/zeroclaw-labs/zeroclaw/issues/12181)** Web-managed document upload (first Phase 2 web slice of #9488) — likely near-term; complements open PR [#12269](https://github.com/zeroclaw-labs/zeroclaw/pull/12269) (web chat voice controls) and [#11846](https://github.com/zeroclaw-labs/zeroclaw/pull/11846) (OpenRouter balance settings).
- **[#9597](https://github.com/zeroclaw-labs/zeroclaw/issues/9597)** Durable, attributable peer-agent turns — fits the event-history direction accepted in #10526; strong candidate for v0.9.x.
- **[#8132](https://github.com/zeroclaw-labs/zeroclaw/issues/8132)** Rust/WASM web UI — strategic, not imminent; blocked on author action.
- PR-driven features in flight: multi-model provider profiles ([#9809](https://github.com/zeroclaw-labs/zeroclaw/pull/9809)), multiple plugin registry origins ([#12216](https://github.com/zeroclaw-labs/zeroclaw/pull/12216)), relay-served dashboard ([#12270](https://github.com/zeroclaw-labs/zeroclaw/pull/12270)), Telegram voice-reply synthesis ([#12276](https://github.com/zeroclaw-labs/zeroclaw/pull/12276)).

**Prediction:** the next tagged release will most likely be the v0.9.0 security-gate cut (ownership scoping, cron authority, SOP authorization) plus relay dashboard and Telegram/Discord channel features.

## 7. User Feedback Summary

- **Security-focused contributors (Audacity88, IftekharUddin, JordanTheJet)** are systematically auditing the principal/authority model; findings are being accepted and closed rapidly — a sign of healthy triage, but also of an authorization model that shipped with gaps.
- **Operational pain points:** silent failures are a recurring complaint — SOPs not loading ([#9779](https://github.com/zeroclaw-labs/zeroclaw/issues/9779)), quickstart claiming "No config written" while writing one ([#12155](https://github.com/zeroclaw-labs/zeroclaw/issues/12155)), and a long session becoming permanently unusable ([#12263](https://github.com/zeroclaw-labs/zeroclaw/issues/12263)). Users value predictability over cleverness.
- **Proxy/deployment users** are hitting real-world gateway assumptions (XFF handling in [#12052](https://github.com/zeroclaw-labs/zeroclaw/issues/12052)); **channel users** report message-loss windows at boot ([#12279](https://github.com/zeroclaw-labs/zeroclaw/issues/12279)).
- Satisfaction signals: fast same-day closures of dependency security issues and proactive follow-up tasks (#12248, #12261, #12280) suggest maintainers are responsive; dissatisfaction centers on silent misconfigurations and edge-case session corruption.

## 8. Backlog Watch

Long-lived items needing maintainer attention:

- **[#8132](https://github.com/zeroclaw-labs/zeroclaw/issues/8132)** (open since 2026-06-22, `needs-author-action`) — WASM UI decision stalled; strategic fork in the road remains unresolved.
- **[#9572](https://github.com/zeroclaw-labs/zeroclaw/issues/9572)** (since 2026-07-31, P1, no-stale) — Tokio stack overflow in gateway WebSocket turns; accepted but unfixed.
- **[#9779](https://github.com/zeroclaw-labs/zeroclaw/issues/9779)** (since 2026-08-06, P1) — SOP default-dir silent failure; high user impact, no linked fix PR.
- **[#9746](https://github.com/zeroclaw-labs/zeroclaw/pull/9746)** (since 2026-08-04, XL, release-gate, `needs-author-action`) — core ownership-scoping PR; a release blocker that has sat three months.
- **[#9809](https://github.com/zeroclaw-labs/zeroclaw/pull/9809)** (since 2026-08-07, XL, `needs-author-action`) — multi-model provider profiles; large, valuable, and stalled.
- **[#10926](https://github.com/zeroclaw-labs/zeroclaw/issues/10926)** (since 2026-09-17, accepted, `follow-up`) — Matrix routing bug with no fix PR attached.

---
*Data source: github.com/zeroclaw-labs/zeroclaw, snapshot 2026-11-07.*

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar).*