# OpenClaw Ecosystem Digest 2026-11-10

> Issues: 500 | PRs: 500 | Projects covered: 5 | Generated: 2026-11-09 23:19 UTC

- [OpenClaw](https://github.com/openclaw/openclaw)
- [Hermes Agent](https://github.com/nousresearch/hermes-agent)
- [IronClaw](https://github.com/nearai/ironclaw)
- [QwenPaw](https://github.com/agentscope-ai/QwenPaw)
- [ZeroClaw](https://github.com/zeroclaw-labs/zeroclaw)

---

## OpenClaw Deep Dive

# OpenClaw Project Digest — 2026-11-10

## 1. Today's Overview

OpenClaw remains in a high-velocity maintenance phase with **no new releases** in the last 24 hours, but substantial triage and engineering throughput: 500 issues and 500 PRs were touched, with **208 issues closed** and **131 PRs merged/closed**. The open backlog skews toward stability work — crash-loop/restart reliability, SQLite/session-state integrity, and Windows/Kubernetes deployment failures dominate the top-commented issues. PR activity is concentrated in small, surgical fixes (CLI error surfacing, WebChat rendering, Android/Telegram channel behavior) rather than large features. Overall health is **moderate-to-good**: strong maintainer responsiveness and many "ready for maintainer look" PRs, but a persistent cluster of P0/P1 reliability defects (Windows startup, update-after-crash paths, session wedging) suggests the update/restart subsystem is the project's current weak point.

## 2. Releases

**None.** No new releases or pre-releases were published in the last 24 hours.

## 3. Project Progress

131 PRs were merged/closed. Notable closed items signaling forward movement:

- **Issue #172628 [CLOSED, P1]** — Fresh Gateway start leaving main-agent admission superseded until restart (fix-shape clear, queueable). [Link](https://github.com/openclaw/openclaw/issues/172628)
- **Issue #167506 [CLOSED, P0]** — Deleted temporary handoff DB blocking package recovery and future updates. [Link](https://github.com/openclaw/openclaw/issues/167506)
- **Issue #192959 [CLOSED, P1]** — Kubernetes fsGroup remount causing credential-directory rejection. [Link](https://github.com/openclaw/openclaw/issues/192959)
- **Issue #175926 / #158966 / #168794 [CLOSED]** — SQLite snapshot reclamation, CDP credential leak, and Codex relay 502 issues respectively.
- **PR #196294 [CLOSED]** — Knowledge memory save latency on large vaults (~11s → ~2s). [Link](https://github.com/openclaw/openclaw/pull/196294)

Progress is incremental: many closures appear to be stale-bot or resolution triage rather than shipped fixes (several closed items carry `stale` labels and no linked fix PR), so "closed" should not be read uniformly as "fixed."

## 4. Community Hot Topics

| Rank | Item | Comments | Signal |
|---|---|---|---|
| 1 | [#170956](https://github.com/openclaw/openclaw/issues/170956) Windows slow startup / unclean plugin shutdown / schtasks permission error | 30 | P0, UX release-blocker |
| 2 | [#166634](https://github.com/openclaw/openclaw/issues/166634) SQLite session reclamation checkpoint failure → agent unavailable after crash | 27 | Session-state |
| 3 | [#193962](https://github.com/openclaw/openclaw/issues/193962) Header-probe timeout fix unreachable via `openclaw update` from 2026.10.10 | 21 | P0, diamond lobster |
| 4 | [#166133](https://github.com/openclaw/openclaw/issues/166133) MCP integer args coerced to string | 16 | P2, needs product decision |
| 5 | [#164394](https://github.com/openclaw/openclaw/issues/164394) WebChat transcript jitter (now CLOSED) | 15 | UX friction |
| 6 | [#144336](https://github.com/openclaw/openclaw/issues/144336) Deferred config-reload restart fails shutdown step | 14 | P1, crash-loop |

**Underlying needs:** The top threads cluster around a single theme — **trustworthy restarts and updates**. Users cannot reliably recover from crashes or apply fixes, which both blocks adoption and turns transient bugs into permanent outages. The second theme is **session durability**: reclamation/lock/checkpoint failures strand agents until manual intervention. The MCP coercion thread reflects growing third-party-integration demand and the need for a product decision on the tool-call adapter.

## 5. Bugs & Stability

Ranked by severity (open items today):

**P0 / release-blocking**
- [#170956](https://github.com/openclaw/openclaw/issues/170956) — Windows: slow startup, unclean plugin shutdown, schtasks permission error; restarts/updates unreliable. **No fix PR linked.**
- [#193962](https://github.com/openclaw/openclaw/issues/193962) — Fix for SQLite header-probe timeout unreachable on affected installs because package-update runs the *installed* updater's 10s probe. Source-repro. **No fix PR linked.**

**P1 / crash-loop, data-loss, session-state**
- [#144336](https://github.com/openclaw/openclaw/issues/144336) — Deferred SIGUSR1 restart fails shutdown ("singleton lifecycle state lost"). Needs live repro.
- [#157255](https://github.com/openclaw/openclaw/issues/157255) — Turn claim not released after lane timeout; 90+ min session wedge.
- [#190307](https://github.com/openclaw/openclaw/issues/190307) — Gateway restart leaves many agents permanently degraded (admission timeout).
- [#174895](https://github.com/openclaw/openclaw/issues/174895) — Live Gateway state owner judged stale; lock reclaimed by second process. Windows.
- [#166641](https://github.com/openclaw/openclaw/issues/166641) — `web_fetch` cold fallback synchronously loads plugins on main thread → liveness stall.
- [#191841](https://github.com/openclaw/openclaw/issues/191841) — 2026.10.10 regression: native child reaping unavailable on glibc < 2.31 (RHEL 8/al8); all `exec` fails.
- [#167472](https://github.com/openclaw/openclaw/issues/167472) — Subagent exec approvals fail (`missing scope: operator.approvals`), fail-closed, no approval card.
- [#192141](https://github.com/openclaw/openclaw/issues/192141) — Interrupted-turn continuation drops original user text.

**P2 / notable**
- [#182891](https://github.com/openclaw/openclaw/issues/182891) — Prompt cache never reused (`cache read dropped N -> 0`).
- [#176187](https://github.com/openclaw/openclaw/issues/176187) — Sparse vec0 allocation pushes `memory_search` past 30s deadline.
- [#194662](https://github.com/openclaw/openclaw/issues/194662) — Notification wake invents a sender; main session keeps phantom thread alive.

**Assessment:** Several of the highest-severity issues have **no linked fix PR** and require live repro or product decisions, indicating a support bottleneck rather than an engineering one. Notably, #193962 is a *meta-bug*: the recovery path for a prior fix is itself broken, which is the most dangerous pattern in the set.

## 6. Feature Requests & Roadmap Signals

- **CJK/Chinese search support** — [#170997](https://github.com/openclaw/openclaw/issues/170997) requests a `bigram` FTS tokenizer (trigram cannot match 2-char queries; unicode61 doesn't segment CJK). Companion issue [#173839](https://github.com/openclaw/openclaw/issues/173839) on CJK payload prechecks suggests a coherent i18n/APAC push.
- **Rendered Markdown preview in Review files** — [#128090](https://github.com/openclaw/openclaw/issues/128090), 4 👍, fix-shape clear. Low-risk, likely candidate.
- **Android parity** — [#175826](https://github.com/openclaw/openclaw/issues/175826) design-acceptance request for in-chat questions/approvals; PR activity (#195979, #195928, #196300) confirms active Android investment.
- **Per-automation web-search provider** — [#173518](https://github.com/openclaw/openclaw/issues/173518) (closed, product decision pending).
- **Operator recovery tooling** — [#170562](https://github.com/openclaw/openclaw/issues/170562) for stranded requester settle wakes; [#165685](https://github.com/openclaw/openclaw/issues/165685) machine-readable collector reasons.

**Prediction:** Near-term releases will likely prioritize (a) CJK search/tokenizer work, (b) Android feature parity, and (c) small Control UI/WebChat polish PRs. Memory/REM exclusion semantics ([#172547](https://github.com/openclaw/openclaw/issues/172547)) is a plausible medium-term item.

## 7. User Feedback Summary

**Pain points**
- **Update/restart reliability is the dominant frustration** — users describe requiring "many retries" and hours-long misdiagnoses. #173313 (config get masking sensitive values) reportedly caused a 4+ hour outage verification loop during a Funnel recovery.
- **Silent degradation**: sessions wedge for 90+ minutes; agents become "degraded" after restart; crash recovery leaves agents permanently refusing `chat.history`/`models.list`.
- **Diagnostic traps**: `status --deep` and `status --agent` swallow real errors (addressed by PRs [#196073](https://github.com/openclaw/openclaw/pull/196073), [#196081](https://github.com/openclaw/openclaw/pull/196081)); `gateway restart` reports failure yet exits 0 ([#161158](https://github.com/openclaw/openclaw/issues/161158)).
- **Deployment friction** on Windows (schtasks), Kubernetes (fsGroup), and RHEL 8 (glibc).

**Positive signals**
- Maintainers are visibly engaging on high-severity threads (30/27/21 comments), and many PRs are tagged "ready for maintainer look" or carry screenshot/proof evidence.
- Users file detailed, high-quality reproductions with environment and version data, suggesting a technically invested user base.

**Sentiment:** Frustrated but engaged. Satisfaction hinges on restart/update reliability; there is clear appetite for fixing the operational substrate before adding features.

## 8. Backlog Watch

Long-unanswered or decision-blocked items needing maintainer attention:

| Item | Age | Blocker |
|---|---|---|
| [#92473](https://github.com/openclaw/openclaw/issues/92473) Model fallback notice leaks to user-facing channels | ~5 months | `needs-product-decision` |
| [#91931](https://github.com/openclaw/openclaw/issues/91931) Preseeded identity files auto-complete bootstrap, delete user BOOTSTRAP.md | ~5 months | P1, data-loss, needs product decision; fix PR [#196302](https://github.com/openclaw/openclaw/pull/196302) now open |
| [#137613](https://github.com/openclaw/openclaw/issues/137613) Pre-compaction memory flush disabled on CLI backends | ~2 months | Product decision |
| [#166133](https://github.com/openclaw/openclaw/issues/166133) MCP integer→string coercion | ~1 month | Product decision, 16 comments |
| [#128090](https://github.com/openclaw/openclaw/issues/128090) Markdown preview in Review | ~2.5 months | Fix-shape clear, awaiting maintainer |
| [#173057](https://github.com/openclaw/openclaw/issues/173057) Shared `agents.defaults.workspace` treated as live workspace | ~1 month | Needs live repro |
| [#151923](https://github.com/openclaw/openclaw/pull/151923) Telegram large-media 30s timeout (PR) | ~2 months | Waiting on maintainer |
| [#178677](https://github.com/openclaw/openclaw/pull/178677) Cron jobs blocked on missing creator channel (PR) | ~3 weeks | Waiting on author, security review required |

**Priority recommendation:** #193962 and #170956 warrant immediate maintainer ownership — they are P0 recovery-path failures with no fix PRs and directly compound user dissatisfaction with updates. #91931 is notable because a fix PR now exists after five months; fast-tracking it would close a long-standing data-loss complaint.

---
*Data source: github.com/openclaw/openclaw, 24h window ending 2026-11-09. Comment/reaction counts reflect items updated in that period; "no fix PR linked" references the `clawsweeper:no-new-fix-pr` label where present.*

---

## Cross-Ecosystem Comparison

# Cross-Project Comparison Report — Personal AI Assistant / Agent OSS Ecosystem
**Snapshot date:** 2026-11-10 | **Projects:** OpenClaw, Hermes Agent, IronClaw, QwenPaw, ZeroClaw

---

## 1. Ecosystem Overview

The personal AI assistant open-source landscape is bifurcating into two camps: **mature "Claw-family" runtimes** (OpenClaw as reference implementation, plus derivatives Hermes, ZeroClaw, IronClaw) built around agent runtimes, messaging gateways, and plugin ecosystems — and **application-layer assistants** (QwenPaw) focused on local model runtimes and console UX. The dominant engineering burden across the ecosystem has shifted from *feature acquisition* to *operational reliability*: crash recovery, restart/update paths, session-state durability, and security isolation now consume the majority of maintainer attention. Security review is maturing rapidly — ZeroClaw shows a formal S0-severity review pipeline against every open PR, and Hermes/OpenClaw both closed security-boundary bugs this cycle. Local-first inference (MLX, llama.cpp, Ollama) and plugin/marketplace distribution are the two clearest greenfield vectors, while Windows remains the ecosystem's most fragile platform.

---

## 2. Activity Comparison

| Project | Issues (24h touched) | PRs (24h touched) | Merge/Close Throughput | Release Status | Health Score* |
|---|---|---|---|---|---|
| **OpenClaw** | 500 (208 closed) | 500 (131 merged/closed) | Very high, triage-heavy | None (no tag in 24h) | **6.5 / 10** — high velocity, but P0 restart/update cluster with no fix PRs |
| **Hermes Agent** | 50 (32 closed) | 50 (14 merged/closed) | High, converging | None (0.23.0 line per reports) | **7.5 / 10** — strong close rate, 2 parked P0 `needs-decision` PRs |
| **IronClaw** | 0 | 0 | None | None | **N/A** — dormant in window |
| **QwenPaw** | 17 | 50 (9 merged/closed) | High (beta cycle) | **v2.3.0-beta.3 shipped** | **7.0 / 10** — active, but beta regressions on packaged builds |
| **ZeroClaw** | 35 | 50 | **Blocked** (merge queue stalled) | **Blocked** by CI gate | **5.5 / 10** — healthy contributor output, deadlocked pipeline |

*\*Health score is an analyst composite of release cadence, fix velocity, P0/P1 burden, and merge-queue liveness — directional, not a vendor metric.*

**Key readout:** OpenClaw operates at ~10× the raw issue/PR volume of peers — it is the ecosystem's gravity well. QwenPaw is the only project shipping tagged releases this cycle. ZeroClaw is the only project with a *fully blocked* merge queue, making its high activity deceptive.

---

## 3. OpenClaw's Position

**Advantages vs. peers**
- **Scale of surface area:** 500 issues + 500 PRs touched in 24h vs. 50/50 for Hermes and ZeroClaw. This produces the fastest discovery-to-fix loop in the ecosystem — e.g., PR #196294 cutting knowledge-memory save latency from ~11s to ~2s.
- **Ecosystem gravity:** ZeroClaw ships an explicit `zeroclaw migrate openclaw-config` importer (#12348), and Hermes/ZeroClaw both inherit Claw-family architectural assumptions. OpenClaw is the *de facto* config and concept standard.
- **Breadth of integrations:** Control UI, WebChat, Android, Telegram, Kubernetes, MCP, SQLite-backed memory/search — no peer matches this integration matrix in a single repo.

**Technical approach differences**
- **SQLite-centric state model** (session reclamation, checkpointing, vec0 memory search) — this is OpenClaw's signature bet and also its signature liability (see the P0 session-wedge cluster). Peers like Hermes lean on a REST session API (`/api/sessions`) and QwenPaw on a local runtime stack.
- **Update/restart subsystem as first-class concern** — no peer exposes as much operational tooling (`status --deep`, `gateway restart`, `openclaw update`), and none has as many bugs in it. The #193962 "meta-bug" (the fix for a prior fix is unreachable via the updater) is uniquely an OpenClaw-scale problem.

**Community size comparison**
- Issue engagement confirms a larger, more technically invested user base: top threads carry 30/27/21 comments with detailed environment reproductions, versus Hermes' 11/10/9 and QwenPaw's 4-comment ceiling. OpenClaw's users are operating it in production (K8s, RHEL 8, Windows enterprise) — a maturity signal peers have not yet reached.

---

## 4. Shared Technical Focus Areas

| Requirement | Projects | Specific Needs |
|---|---|---|
| **Trustworthy restart/update & crash recovery** | OpenClaw (P0: #170956, #193962, #144336), Hermes (resume-provider fix #151956), QwenPaw (#8616 startup AppHang) | Restarts must not strand agents; updaters must be able to deliver their own fixes; clean shutdown on SIGUSR1. |
| **Session-state durability / no silent loss** | OpenClaw (#157255 90-min wedge, #192141 dropped user text), Hermes (#143290 stale `/stop` drops next turn, #121750 P0 compression archives unseen turns), QwenPaw (#8521 intermittent empty responses) | Delivery guarantees over features; fork/reset semantics must be unambiguous across CLI, REST, and desktop. |
| **Security isolation & approval-scope correctness** | ZeroClaw (S0 batch: #12304–#12311), Hermes (#152125 approver binding, #53674 channel-post bypass), QwenPaw (#8642 MCP policy fencing) | Sandbox file access, cross-session attach prevention, approval binding to the *requester*, fail-closed defaults. |
| **Windows / enterprise-SKU reliability** | OpenClaw (schtasks, ~5 months of Windows issues), QwenPaw (LTSC startup, long-path, GBK encoding #8234, Chinese-path #8502), Hermes (#152202 non-interactive plugin update) | Non-default locales, legacy encodings, frozen-build dependency verification. |
| **Third-party / OpenAI-compatible provider compatibility** | QwenPaw (#8603 tool-call serialization, #8512/#8615 gateway error formats), Hermes (#149511 pricing coverage), OpenClaw (#166133 MCP integer coercion) | Graceful handling of gateway-specific error formats; correct tool-call adapters; per-automation provider routing. |
| **Plugin/marketplace distribution integrity** | QwenPaw (#8595 metadata missing, #8644 channel activation), Hermes (4 catalog PRs in one day), OpenClaw (MCP adapter demand) | Install paths must work via both UI and CLI; plugin lifecycle must be framework-managed. |
| **Local model runtimes** | QwenPaw (MLX + llama.cpp GPU stack #8647–#8649, Ollama `num_ctx` #8236), OpenClaw (memory/search tuning) | First-party Apple Silicon support, GPU-offload visibility, correct context-window sizing. |

**Cross-cutting insight:** Four of five active projects independently hit *the same class of bug* — a recovery or continuation path that silently loses data. This is an ecosystem-wide architectural gap, not a per-project defect.

---

## 5. Differentiation Analysis

| Dimension | OpenClaw | Hermes Agent | QwenPaw | ZeroClaw |
|---|---|---|---|---|
| **Primary focus** | Full-stack agent runtime + Control UI + memory | Messaging gateway hardening (Telegram-first), plugin catalog | Desktop assistant + local model runtime + console UX | Secure multi-channel runtime, remote OIDC onboarding |
| **Target users** | Production operators, K8s/enterprise, self-hosters | Group-chat / multi-surface messaging users, plugin authors | Desktop/Apple Silicon users, Chinese-locale Windows users | Security-sensitive self-hosted daemon operators |
| **Architecture bet** | SQLite state + gateway + MCP adapter | Lease-based room/profile model + REST session contract | Frozen desktop builds + MLX/llama.cpp local stack | Native Rust channels (XMPP/OMEMO), sandboxed execution |
| **Release rhythm** | High-velocity maintenance, no tag this cycle | Converging toward 0.23 line | Fast beta cadence (beta.3 shipped) | Blocked pending CI gate |
| **Distinct risk** | Operational substrate fragility at scale | Parked P0 decision-blocked PRs | Beta regressions in packaging/dependency path | Review-finding security debt + merge deadlock |

**Notable divergence:** ZeroClaw is the only project treating **security review as a pre-merge gate producing S0 findings** — a materially different engineering culture from OpenClaw's triage-driven model. QwenPaw is the only project investing in **first-party local inference**, which positions it against OpenClaw's cloud-adapter assumptions.

---

## 6. Community Momentum & Maturity

**Activity tiers**
- **Tier 1 — Ecosystem anchor:** OpenClaw (500/500, triage-dominant). Mature but carrying operational debt.
- **Tier 2 — Converging stabilizers:** Hermes (32 issue closures, Telegram sweep, 14 merges) and QwenPaw (release-shipping beta cycle, 9 merges, 3 first-time contributors). Both are *closing faster than they open*.
- **Tier 3 — Blocked/dormant:** ZeroClaw (contributor output high, **merge queue dead** — momentum is real but unrealizable) and IronClaw (no activity; likely dormant or pre-launch).

**Iterating vs. stabilizing**
- **Rapidly iterating:** QwenPaw (new MLX/GPU runtime stack in flight, UX polish PRs same-day) and Hermes (plugin catalog expansion — 4 submissions in one day).
- **Stabilizing under load:** OpenClaw and Hermes are both in *hardening* phases — OpenClaw on restarts/sessions, Hermes on Telegram correctness. Neither is adding major features.
- **Stalled:** ZeroClaw needs a single dependency decision (#12320) to convert a large backlog of verified fixes into releases. This is the highest-leverage unblock in the ecosystem.

**Maturity ranking (composite):** OpenClaw > Hermes ≈ QwenPaw > ZeroClaw >> IronClaw. Notably, QwenPaw's *release-gating discipline* (auto-opened 4-hour cross-platform verification issue #8643) is more mature than its bug volume suggests.

---

## 7. Trend Signals (for AI agent developers)

1. **Reliability is the new feature.** Across OpenClaw, Hermes, and QwenPaw, users explicitly prioritize *delivery guarantees over new capabilities* — silent message loss, stale-stop dropping turns, and post-crash wedges are the loudest complaints. **Action:** design idempotent continuation and explicit "message settled" acknowledgements into any agent loop.
2. **Recovery-path regressions are the most dangerous bug class.** OpenClaw's #193962 (fix unreachable via the updater) and ZeroClaw's #12330 (silent local-daemon fallback) show that *the path back to health* is under-tested. **Action:** test the updater with the updater; treat failover as a first-class state machine, not a catch block.
3. **Security review is shifting left, pre-merge.** ZeroClaw's S0 findings against open PR heads (sandbox dotfile access, approval-scope bypass, cross-session attach) set a new bar. **Action:** require approval binding to the *requesting identity* and sandbox-scoped file access by default.
4. **Local-first inference is arriving in the assistant layer.** QwenPaw's MLX/llama.cpp/GPU-control stack and Ollama `num_ctx` fix signal that desktop assistants will own their inference runtime. **Action:** abstract model backends early; do not hard-code cloud adapter assumptions.
5. **Platform parity is a trust issue, not a nicety.** Windows (schtasks, long paths, GBK), RHEL 8 (glibc), iPhone Safari (`requestIdleCallback`), and Linux translucency all generate outsized frustration. **Action:** treat non-default locale/encoding and frozen-build environments as tier-1 test targets.
6. **Ecosystem interop is consolidating around OpenClaw conventions.** ZeroClaw's migration importer and shared Claw-family config concepts suggest a de facto standard is forming. **Action:** for new entrants, align config/session semantics with OpenClaw to inherit tooling and migrate-in paths.
7. **Plugin marketplaces are the next reliability frontier.** Install-path failures (#8595), frozen-build activation regressions (#8644), and framework-managed lifecycle PRs (#8431) all point the same way. **Action:** design plugin dependency verification and lifecycle management before scaling a catalog.

---

**Bottom line for decision-makers:** OpenClaw is the ecosystem's reference implementation and should be tracked as the compatibility baseline; Hermes and QwenPaw are the healthiest active bets for messaging-gateway and desktop/local-runtime use cases respectively; ZeroClaw carries the strongest security posture but needs its CI gate resolved before it can ship; IronClaw shows no signal this cycle. The defining 2026 engineering problem is **operational trust** — restarts, recovery, and delivery guarantees — not feature breadth.

---

## Peer Project Reports

<details>
<summary><strong>Hermes Agent</strong> — <a href="https://github.com/nousresearch/hermes-agent">nousresearch/hermes-agent</a></summary>

# Hermes Agent — Project Digest
**Date:** 2026-11-10 | **Repo:** [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent)

---

## 1. Today's Overview

Hermes Agent shows **high activity with net-positive throughput**: 50 issues and 50 PRs touched in the last 24h, balanced toward closure (32 issues closed vs. 18 open/active; 14 PRs merged/closed vs. 36 open). No new releases were cut, but the merge queue is heavily oriented toward **Telegram platform hardening** — fallback network paths, duplicate-send prevention, forum-topic isolation, and approval-scope security fixes — suggesting a sustained stabilization push on the messaging gateway ahead of a possible tagged release. Open work is dominated by P2 bugs across the gateway, desktop shell, and session state, with several long-lived Telegram issues from July–September finally closing today. Overall project health reads **active and converging**, though a small cluster of P0/needs-decision compression and session-state PRs remains parked.

---

## 2. Releases

**No new releases in the last 24 hours.** (Latest known line in the wild is v0.22.2 stable and v0.23.0 per issue reports; no tag published 2026-11-09 → 2026-11-10.)

---

## 3. Project Progress

**14 PRs merged/closed today.** Notable closed items:

| PR | What it closed | Impact |
|---|---|---|
| [#152262](https://github.com/NousResearch/hermes-agent/pull/152262) | Group Chat turns queued behind a busy profile now keep their room lease and settle | Fixes silent reply discard ("indeterminate") when multiple members share a profile |
| [#152125](https://github.com/NousResearch/hermes-agent/pull/152125) | `approvals.approvers: []` binds to real sender + one admin scope on every surface | Security hardening — closes gap where non-requesters could approve risky commands |
| [#142512](https://github.com/NousResearch/hermes-agent/pull/142512) | Hosted-room terminal settle alive across plain lease expiry | Fixes load-sensitive flaky test / wedged "running" state |

**Issue closures (32 total)** were overwhelmingly Telegram-related: flood-control/typing cooldown (#99643, #107612), duplicate-send on network error (#64238), webhook re-registration (#141397), channel-post gating (#53674), fork/topic routing (#58850, #36992, #65202), and redaction false-positives (#145664) — a broad **Telegram reliability and correctness sweep**.

Still open and advancing: profile provisioning refactor ([#149075](https://github.com/NousResearch/hermes-agent/pull/149075)), Bots room-view delta reads ([#149210](https://github.com/NousResearch/hermes-agent/pull/149210)), and pricing coverage for OpenCode Zen routes ([#149511](https://github.com/NousResearch/hermes-agent/pull/149511)).

---

## 4. Community Hot Topics

| Rank | Item | Comments | Signal |
|---|---|---|---|
| 1 | [#141988](https://github.com/NousResearch/hermes-agent/issues/141988) — `process_manage` TypeError paging background output | 11 | Regression blocking log retrieval on macOS; long-running (since 10-20) |
| 2 | [#145183](https://github.com/NousResearch/hermes-agent/issues/145183) — Desktop translucent on Linux, no UI toggle | 10 (👍1) | UI/UX mismatch; effect cannot be disabled |
| 3 | [#142064](https://github.com/NousResearch/hermes-agent/issues/142064) — `/api/sessions` omits `_reset_from` | 9 | REST client contract inconsistency (fork vs. `/new` mislabeled) |
| 4 | [#142488](https://github.com/NousResearch/hermes-agent/issues/142488) — model switch crash on tuple `moved` | 8 | Desktop model-switch blocker for profile-following sessions |
| 5 | [#143290](https://github.com/NousResearch/hermes-agent/issues/143290) — stale `/stop` interrupts next turn | 8 | **Data-loss class**: message silently dropped |

**Underlying needs analysis:** The hottest threads cluster around **session-state correctness** (fork semantics, reset markers, stale stop signals) and **platform parity** (desktop settings not reflecting backend capability). Users are integrating via REST clients and desktop UI, not just the CLI — the `/api/sessions` contract and desktop Settings parity are becoming first-class concerns.

---

## 5. Bugs & Stability

Ranked by severity:

**P1 / Data-loss & security**
- [#143290](https://github.com/NousResearch/hermes-agent/issues/143290) (P2 but data-loss impact) — `POST /v1/runs/{id}/stop` on a just-finished run interrupts the *next* turn; new message silently dropped. **No fix PR visible.**
- [#53674](https://github.com/NousResearch/hermes-agent/issues/53674) — Telegram channel posts bypass `allowed_chats`/`require_mention`, treated as DMs — **CLOSED today** (security boundary fix landed).
- [#151762](https://github.com/NousResearch/hermes-agent/issues/151762) — Discord DM-only admin can approve group approval via buttons — **CLOSED today**; #152125 merged as the fix.

**P2 / Functional regressions**
- [#141988](https://github.com/NousResearch/hermes-agent/issues/141988) — `process_manage` `TypeError: slice indices must be integers` — **open, 11 comments, no fix PR yet** (oldest high-traffic open bug).
- [#145183](https://github.com/NousResearch/hermes-agent/issues/145183) — Linux desktop translucency with hidden Settings toggle — **open**.
- [#142488](https://github.com/NousResearch/hermes-agent/issues/142488) — Desktop model switch crash (`moved` rename-map shadowed) — **open**.
- [#142064](https://github.com/NousResearch/hermes-agent/issues/142064) — `/api/sessions` missing `_reset_from` — **open**.
- [#152148](https://github.com/NousResearch/hermes-agent/issues/152148) — Fresh Telegram DM topics invisible/unopenable — **open** (new today, `cause/upstream` tagged).
- [#148538](https://github.com/NousResearch/hermes-agent/issues/148538) — iPhone Safari: `requestIdleCallback` undefined, pages fail to render — **open**.

**Fix PRs in flight (good stability signal):**
- [#151833](https://github.com/NousResearch/hermes-agent/pull/151833), [#151801](https://github.com/NousResearch/hermes-agent/pull/151801), [#151930](https://github.com/NousResearch/hermes-agent/pull/151930), [#151772](https://github.com/NousResearch/hermes-agent/pull/151772) — a coherent Telegram fallback/duplicate-send/forum-isolation patchset (all `ci-reviewed`).
- [#151956](https://github.com/NousResearch/hermes-agent/pull/151956) — resumed chats keep custom provider (fixes #132648).
- [#152230](https://github.com/NousResearch/hermes-agent/pull/152230) — errors name the actual cause (better diagnostics).

---

## 6. Feature Requests & Roadmap Signals

- **Plugin catalog expansion is the dominant feature vector**: [#151013](https://github.com/NousResearch/hermes-agent/pull/151013) (`hermes-audio-api`, OpenAI-compatible audio endpoints), [#151014](https://github.com/NousResearch/hermes-agent/pull/151014) (`hermes-fal-video`, three FAL video tools + bundled skill), [#152195](https://github.com/NousResearch/hermes-agent/pull/152195) (`borrowed-hands`, Xcode compile offload from cloud Hermes), [#152201](https://github.com/NousResearch/hermes-agent/pull/152201) (FollowThrough commitments plugin). All require Hermes ≥0.23.0 — suggests **0.23 is the target release line** for catalog-compatible plugins.
- **Bots/Group Chat architecture step 2** in [#149210](https://github.com/NousResearch/hermes-agent/pull/149210) — log-delta room views + `groups.changed` push; roadmap artifact linked in PR.
- **Dismissible todo/previous-tasks UI** requested in [#127429](https://github.com/NousResearch/hermes-agent/issues/127429) (open, 8 comments).
- **Pricing coverage** for OpenCode Zen in [#149511](https://github.com/NousResearch/hermes-agent/pull/149511).

**Prediction:** Next release will emphasize Telegram gateway reliability + plugin catalog additions; Bots architecture improvements are staged for a follow-on.

---

## 7. User Feedback Summary

**Pain points:**
- *"Silent loss"* is the recurring theme — dropped messages after stale `/stop`, archived unseen turns under compression ([#121750](https://github.com/NousResearch/hermes-agent/pull/121750), P0), undelivered `send_path_degraded` replies ([#123898](https://github.com/NousResearch/hermes-agent/issues/123898)). Users prioritize **delivery guarantees over new features**.
- **Platform parity friction**: Linux users stuck with unwanted translucency; iPhone Safari route failures; Windows non-interactive `plugins update` blocked ([#152202](https://github.com/NousResearch/hermes-agent/issues/152202)).
- **Cross-surface contract drift**: REST clients misrender forks due to missing `_reset_from`; TUI/desktop disagreement on provider on resume.

**Satisfaction signals:** Multiple long-standing Telegram bugs and a security-boundary issue (#53674) closed with linked fixes — strong evidence maintainers are responsive to well-tagged reports. Community plugin authors actively submitting (4 catalog PRs today), indicating a healthy extension ecosystem.

---

## 8. Backlog Watch

Items needing maintainer attention (old, high-comment, or P0/needs-decision):

- 🔴 [#121750](https://github.com/NousResearch/hermes-agent/pull/121750) — **P0, needs-decision**, compression archives unseen turns (open since 2026-09-24, **47 days**). Data-loss risk.
- 🔴 [#132948](https://github.com/NousResearch/hermes-agent/pull/132948) — **P0, needs-decision**, webui-ng YOLO resume / `failure_reason` / fork prompt races (open since 10-04).
- 🟠 [#141988](https://github.com/NousResearch/hermes-agent/issues/141988) — 11 comments, 21 days open, no fix PR — top open bug by engagement.
- 🟠 [#127429](https://github.com/NousResearch/hermes-agent/issues/127429) — dismiss/clear previous-tasks UI, 42 days open.
- 🟠 [#142064](https://github.com/NousResearch/hermes-agent/issues/142064) — `/api/sessions` contract gap, 21 days open.
- 🟡 [#152202](https://github.com/NousResearch/hermes-agent/issues/152202) — non-interactive `plugins update` (new, but blocks SSH/CI automation workflows).

**Health verdict:** Project is actively maintained with a strong close rate and a clear Telegram-focused fix wave. The main risk is the **two parked P0 `needs-decision` PRs** related to session state and compression — these are decision-blocked, not implementation-blocked, and warrant maintainer resolution before the next tagged release.

---
*Generated from GitHub activity 2026-11-09 → 2026-11-10. All counts per provided dataset.*

</details>

<details>
<summary><strong>IronClaw</strong> — <a href="https://github.com/nearai/ironclaw">nearai/ironclaw</a></summary>

No activity in the last 24 hours.

</details>

<details>
<summary><strong>QwenPaw</strong> — <a href="https://github.com/agentscope-ai/QwenPaw">agentscope-ai/QwenPaw</a></summary>

# QwenPaw Project Digest — 2026-11-10

---

## 1. Today's Overview

QwenPaw is in an active beta stabilization cycle following the v2.3.0-beta.3 release. Activity remains very high: 50 PRs and 17 issues were updated in the last 24h, including 9 merged/closed PRs. The dominant theme is the new "QwenPaw Local" model-runtime stack (llama.cpp GPU controls, managed MLX), split across three stacked PRs (#8649 → #8647 → #8648). Alongside new features, a cluster of Windows-specific packaging and channel-plugin activation bugs continues to surface in the 2.3.0 betas, and memory/search performance issues are being addressed by external contributors. Overall project health is good—active maintainer review, rapid issue triage, and healthy first-time-contributor participation—though the volume of beta regressions suggests the 2.3.0 GA release may need additional hardening time.

---

## 2. Releases

### v2.3.0-beta.3 ([release page](https://github.com/agentscope-ai/QwenPaw/releases/tag/v2.3.0-beta.3))

**Changes:**
- `docs(memory)`: Added separate bilingual blogs for multimodal memory and Memory Tags ([#8506](https://github.com/agentscope-ai/QwenPaw/pull/8506))
- `fix(channels)`: Resolved desktop plugin installation and repair failures ([PR referenced in release notes](https://github.com/agentscope-ai/QwenPaw/releases/tag/v2.3.0-beta.3))

**Notes:**
- This is a beta release; no explicit breaking changes or migration notes were published in the changelog.
- A [Release Duty installation-verification issue (#8643)](https://github.com/agentscope-ai/QwenPaw/issues/8643) was auto-opened with a 4-hour verification deadline across platforms; this is a good signal of mature release gating.
- **Caution for upgraders:** beta.3 is generating its own new regression reports (see §5), notably channel-plugin activation failures on packaged Desktop builds ([#8644](https://github.com/agentscope-ai/QwenPaw/issues/8644)). Users on stable 2.2.2 may want to wait for beta.4 or GA.

---

## 3. Project Progress

Nine PRs were merged/closed in the last 24h, concentrated on test infrastructure, CI reliability, and cleanup:

| PR | Type | Summary |
|---|---|---|
| [#8639](https://github.com/agentscope-ai/QwenPaw/pull/8639) | test fix | Fixed BOM decoding in dependency installation test |
| [#8640](https://github.com/agentscope-ai/QwenPaw/pull/8640) | test fix | Fixed Windows permission (`0600`) and UTF-8 BOM assertions — improves cross-platform CI determinism |
| [#8641](https://github.com/agentscope-ai/QwenPaw/pull/8641) | CI fix | Extended macOS desktop build timeout to 90 minutes — likely unblocks slow macOS packaging pipeline |
| [#8645](https://github.com/agentscope-ai/QwenPaw/pull/8645) | feature (superseded) | Closed in favor of the re-split MLX stack (#8647/#8648/#8649) |

**Significant in-flight work advanced today (high comment/update activity):**
- **Local model runtime overhaul** — the three stacked PRs [#8649](https://github.com/agentscope-ai/QwenPaw/pull/8649) (llama.cpp GPU + auto-update), [#8647](https://github.com/agentscope-ai/QwenPaw/pull/8647) (managed MLX), [#8648](https://github.com/agentscope-ai/QwenPaw/pull/8648) (console controls) form a coherent feature set and are likely the headline of the next release.
- **Security fix in MCP driver policy publication** — [#8642](https://github.com/agentscope-ai/QwenPaw/pull/8642) fences stale policy publication so a slow MCP reload cannot restore a revoked allow-policy. This is a meaningful access-control correctness fix.
- **Provider compatibility fixes** — [#8512](https://github.com/agentscope-ai/QwenPaw/pull/8512) (gateway status-code extraction) and [#8615](https://github.com/agentscope-ai/QwenPaw/pull/8615) (recognizing gateway context-overflow 400s) improve robustness against third-party OpenAI-compatible gateways.
- **Memory search performance** — [#8623](https://github.com/agentscope-ai/QwenPaw/pull/8623) (first-time contributor) eliminates duplicate turn-boundary resolution.

---

## 4. Community Hot Topics

The most-discussed items (by comment count) reveal three underlying community needs: **beta reliability on Windows**, **plugin ecosystem integrity**, and **agent UX transparency**.

1. **[#8595 — Plugin install fails: "No package metadata was found" for qwenpaw-creator](https://github.com/agentscope-ai/QwenPaw/issues/8595)** (4 comments, OPEN)
   *Need:* The official plugin install path is broken via both UI and CLI. This undermines the plugin marketplace as a distribution channel — users cannot reliably install first-party extensions.

2. **[#8521 — Missing responses in chat + Ctrl+C copy requires multiple presses + new UI slower](https://github.com/agentscope-ai/QwenPaw/issues/8521)** (4 comments, OPEN)
   *Need:* A compound UX regression report: reliability of chat output, clipboard behavior across Chrome/Edge, and a request to toggle back to the old UI. Signals dissatisfaction with the 2.x console redesign and possible rendering/streaming issues.

3. **[#8627 — Checkpoint service fails on Git long-path + WeChat channel stuck at DEPENDENCY_ENVIRONMENT_AMBIGUOUS](https://github.com/agentscope-ai/QwenPaw/issues/8627)** (2 comments, OPEN)
   *Need:* Windows long-path environment handling and opaque dependency-verification states. Users need actionable diagnostics rather than ambiguous status codes.

4. **[#8616 — 2.3.0-beta.1 fails to start on Windows 11 IoT LTSC, AppHang](https://github.com/agentscope-ai/QwenPaw/issues/8616)** (2 comments, OPEN)
   *Need:* Regression relative to 2.2.2 on the same machine — a hard startup blocker for enterprise LTSC deployments.

5. **MLX/GPU feature stack ([#8646 issue](https://github.com/agentscope-ai/QwenPaw/issues/8646) + PRs [#8647](https://github.com/agentscope-ai/QwenPaw/pull/8647)/[#8648](https://github.com/agentscope-ai/QwenPaw/pull/8648)/[#8649](https://github.com/agentscope-ai/QwenPaw/pull/8649))** — Apple Silicon users want first-party MLX support and visibility into GPU offload; the maintainer-authored PR stack shows this is being actively delivered.

---

## 5. Bugs & Stability

Ranked by severity (user impact × breadth), with fix status:

### Critical
- **[#8616 — Desktop 2.3.0-beta.1 will not start on Windows 11 IoT LTSC ("path not found" → AppHang)](https://github.com/agentscope-ai/QwenPaw/issues/8616)** — *Regression vs. 2.2.2.* Hard blocker; no fix PR identified yet.
- **[#8644 — All channel plugins (feishu, wechat, yuanbao) fail to activate on packaged Desktop builds (2.3.0b3)](https://github.com/agentscope-ai/QwenPaw/issues/8644)** — `AttributeError` in `validate_runtime_identity` reading `supported_tags` from `DependencyReadEvidence`. *This is a regression in the just-released beta.3* and affects every messaging integration on frozen builds. Highest-priority fix candidate.

### High
- **[#8595 — Plugin install fails with "No package metadata was found"](https://github.com/agentscope-ai/QwenPaw/issues/8595)** — Blocks plugin installation entirely; note the beta.3 changelog claims a channels/plugin-install fix, so verify whether this persists.
- **[#8627 — Checkpoint service fails on Git long-path + WeChat channel stuck at DEPENDENCY_ENVIRONMENT_AMBIGUOUS](https://github.com/agentscope-ai/QwenPaw/issues/8627)** — Windows environment edge case; related dependency-verification logic overlaps with #8644/#8553.
- **[#8603 — `run_code` invoked with empty arguments on custom OpenAI-compatible providers](https://github.com/agentscope-ai/QwenPaw/issues/8603)** — Every tool call rejected with `'code' is a required property` on user-defined providers (kimi, k3.1-pro). Breaks a core agent capability for third-party model users.
- **[#8629 — Oversized historical image re-sent every model call; stripped retry retains misleading "Image loaded" text](https://github.com/agentscope-ai/QwenPaw/issues/8629)** — Wastes tokens/quota and misleads users about request state.

### Medium
- **[#8619 — Repeated turn boundary queries for same search hits](https://github.com/agentscope-ai/QwenPaw/issues/8619)** — Performance bug; **fix PR exists: [#8623](https://github.com/agentscope-ai/QwenPaw/pull/8623).**
- **[#8620 — SQLite errors masked as TimeoutError on elapsed search deadline](https://github.com/agentscope-ai/QwenPaw/issues/8620)** — Diagnostic quality issue in `MemorySpace.search`.
- **[#8521 — Intermittent empty chat responses + Ctrl+C copy failures](https://github.com/agentscope-ai/QwenPaw/issues/8521)** — Reproduction unclear; could be model-side or UI-side.

### Fixed / Closed today
- **[#8585 — `recall_history` broken on Windows: `\\?\` extended-length path breaks SQLite read-only URI](https://github.com/agentscope-ai/QwenPaw/issues/8585)** — ✅ CLOSED
- **[#8553 — QQ channel fails with DEPENDENCY_PLAN_UNVERIFIED when hub plugin lacks dependency env](https://github.com/agentscope-ai/QwenPaw/issues/8553)** — ✅ CLOSED
- **[#8502 — Desktop HTML preview "open in browser" broken for Chinese paths](https://github.com/agentscope-ai/QwenPaw/issues/8502)** — ✅ CLOSED

**Stability assessment:** The 2.3.0 beta line has a recurring theme — packaged/frozen Desktop builds and dependency-environment verification are fragile (see #8644, #8627, #8553, #8616). Maintainers appear to be fixing these reactively per-beta; a dedicated audit of the frozen-build identity/dependency path would likely prevent further regressions before GA.

---

## 6. Feature Requests & Roadmap Signals

| Request | Issue | Likelihood / Signal |
|---|---|---|
| **Managed MLX engine + GPU controls for QwenPaw Local** (Apple Silicon) | [#8646](https://github.com/agentscope-ai/QwenPaw/issues/8646) | **Very high** — already implemented in the maintainer PR stack [#8647](https://github.com/agentscope-ai/QwenPaw/pull/8647)/[#8648](https://github.com/agentscope-ai/QwenPaw/pull/8648)/[#8649](https://github.com/agentscope-ai/QwenPaw/pull/8649). Expect in next beta. |
| **UX: consistent visual style for thinking blocks vs. tool cards; stop left-edge jumping** | [#8650](https://github.com/agentscope-ai/QwenPaw/issues/8650) | Medium — small, well-scoped UI polish; fits a patch release. |
| **UX: "execution-type" tool calls should show human-readable intent** | [#8612](https://github.com/agentscope-ai/QwenPaw/issues/8612) | **High, already in flight** — [#8638](https://github.com/agentscope-ai/QwenPaw/pull/8638) adds an optional `title` param to `execute_shell_command` (also fixes a validation issue). |
| **Browser tab title should reflect current session name** | [#8605](https://github.com/agentscope-ai/QwenPaw/issues/8605) | Medium — simple console change; plausible near-term. |
| **Quote assistant response selections into follow-up drafts** | (PR [#8609](https://github.com/agentscope-ai/QwenPaw/pull/8609)) | Implemented in PR; likely next release. |
| **UI toggle to revert to the old console layout** (implicit in #8521) | [#8521](https://github.com/agentscope-ai/QwenPaw/issues/8521) | Low/uncertain — historically maintainers rarely ship dual-UI modes; more likely they address specific friction (model switching). |

**Prediction for v2.3.0 GA / beta.4:** Local MLX + GPU controls, shell-card intent titles, quote-to-draft, and the memory-search performance fix are the strongest candidates. UX consistency (#8650) is plausible as a fast-follow.

---

## 7. User Feedback Summary

**Pain points:**
- **Windows is the dominant friction surface.** Startup failures on LTSC (#8616), long-path/checkpoint issues (#8627), extended-path SQLite bugs (#8585, now fixed), and Chinese-path HTML preview failures (#8502, now fixed) all cluster on Windows configurations — especially non-default locales and enterprise SKUs.
- **Plugin/channel ecosystem reliability.** Multiple independent reports (#8595, #8644, #8553, #8627) show plugin installation and channel activation are the least stable subsystem in 2.3.x betas.
- **New console UX dissatisfaction.** Users report the redesigned UI feels slower and less information-dense than the old one, with specific complaints about model switching ergonomics and per-press clipboard failures (#8521). Tool-card readability is a recurring theme (#8612, #8650).
- **Third-party provider compatibility.** Users running custom OpenAI-compatible endpoints (kimi, gateways like Strata) hit broken tool-call serialization (#8603) and unrecognized gateway error formats (#8512, #8615).

**Positive signals:**
- Users are filing *high-quality, well-scoped* bug reports with environment details and reproduction steps, which correlates with maintainer responsiveness (19 of 20 top PRs updated today).
- Three first-time-contributor PRs are active ([#8606](https://github.com/agentscope-ai/QwenPaw/pull/8606) docs dead-link fix, [#8623](https://github.com/agentscope-ai/QwenPaw/pull/8623) memory perf, [#8642](https://github.com/agentscope-ai/QwenPaw/pull/8642) MCP policy fencing) — a healthy sign of community investment.
- Apple Silicon users are engaged and their top request (MLX) is being delivered by the maintainer team, which should convert to satisfaction.

---

## 8. Backlog Watch

Items needing maintainer attention (older or lower-visibility but consequential):

1. **[#8234 — fix(tools): decode GBK/CP936 instead of silently dropping bytes in read/edit/grep](https://github.com/agentscope-ai/QwenPaw/pull/8234)** — Open since **2026-10-14** (~27 days). Addresses silent data loss for Chinese Windows users' legacy text files. High user value for a large locale segment; has been "Under Review" for weeks.
2. **[#8236 — fix(ollama): size context window from `num_ctx` instead of 128k default](https://github.com/agentscope-ai/QwenPaw/pull/8236)** — Open since **2026-10-14**. Local Ollama users get incorrect context-window assumptions, causing truncation or overflow errors. Directly relevant to the local-models push; should be coordinated with the #8647–#8649 stack.
3. **[#8170 — fix(console): tolerate DOM-mutation render errors + chunk-error retry (rebased #7889)](https://github.com/agentscope-ai/QwenPaw/pull/8170)** — Open since **2026-10-10** (~31 days), already rebased once. Render-error tolerance plausibly relates to the intermittent UI failures in #8521; worth prioritizing given user complaints about console stability.
4. **[#8431 — feat: convert local Whisper into an SDK plugin with framework-managed lifecycle](https://github.com/agentscope-ai/QwenPaw/pull/8431)** — Large (XXXL) PR open since **2026-10-26**; touches plugin lifecycle, which is exactly the subsystem generating the most beta bugs (#8644, #8553). Needs architectural review before it compounds with the plugin-dependency refactor.
5. **[#8606 — docs: fix 5 dead external links](https://github.com/agentscope-ai/QwenPaw/pull/8606)** — First-time contributor, small and low-risk; quick merge would be a good onboarding signal.

**Maintainer recommendation:** Prioritize (a) the #8644 packaged-build channel-activation regression as a beta.3 hotfix, (b) merging the long-pending #8234/#8236 localization/local-model fixes, and (c) a focused audit of the frozen-build dependency-verification path to stop the recurring Windows beta regressions before GA.

---

*Data source: github.com/agentscope-ai/QwenPaw, activity window 2026-11-09 → 2026-11-10. All links point to the corresponding GitHub issue/PR pages.*

</details>

<details>
<summary><strong>ZeroClaw</strong> — <a href="https://github.com/zeroclaw-labs/zeroclaw">zeroclaw-labs/zeroclaw</a></summary>

# ZeroClaw Project Digest — 2026-11-10

## 1. Today's Overview

ZeroClaw is in a **blocked-release state**: Issue #12320 reports that `RUSTSEC-2026-0459` (libsqlite3-sys 0.38.2 / bundled SQLite 3.53.2) fails the `Security` job on every CI run, which cascades into `CI Required Gate` failure — meaning **no PR can merge and the merge queue is stalled**. Activity remains high (35 issues and 50 PRs touched in 24h) but is heavily skewed toward review findings: a single reviewer (IftekharUddin) filed a batch of S0 security findings against open PR heads (#12304–#12312, #12305, #12306, #12309, #12310, #12311) and `tidux` landed a large zerocode OIDC login PR (#12317) with a cluster of follow-up issues (#12331–#12340). No releases were cut, consistent with the blocked gate. Net assessment: **healthy contributor throughput, degraded merge health due to a CI blocker plus several critical pre-merge security findings.**

## 2. Releases

None in this window. Note for planning: all release-tagged work is currently gated behind #12320.

## 3. Project Progress

Merged/closed PRs today:

- **#12348** (closed, XL) — `feat(migrate): import OpenClaw config, agents, and workspaces`: adds `zeroclaw migrate openclaw-config`, mapping providers, fallback chains, and agents (balanced risk preset). Significant ecosystem-interop feature.
- **#11933** (closed, XS) — `fix(web): keep the chat header on screen in narrow layouts`: responsive fix for mobile chat.
- **#11920** (closed, XS) — `feat(web): toggle to keep the live Thinking box collapsed` (closes #11919): UX improvement for reasoning models.
- **#12342** (closed, XS) — docs: bounded runtime placement exception for zerocode OIDC (#12317), unblocking that feature's merge contract.

Closed issues: #10359 (audit-log hash-chain poisoning — pre-existing, now closed), #11009 (alias rename not cascading permission profiles), #12064 (Discord peer-bot approval bypass), #12087 (tool toggle double-save), #11952 (`-`-prefixed relative data dir), #12312 (OIDC discovery endpoint policy gap), #11919 (Thinking box).

## 4. Community Hot Topics

1. **[Issue #12320](https://github.com/zeroclaw-labs/zeroclaw/issues/12320)** — CI security gate failure (S1, 3 comments). The single highest-leverage thread: every release and merge depends on resolving the RUSTSEC advisory. Needs either a dependency bump or a scoped, documented advisory exception.
2. **[PR #12317](https://github.com/zeroclaw-labs/zeroclaw/pull/12317)** — `feat(zerocode): pairing-free OIDC login and saved remote credentials` (XL). Attracted 10+ follow-up issues (#12330–#12340) — proof of high interest in frictionless remote onboarding, but also of review load: the PR head has known rough edges (ledger actor double-prefix, smoke-test ordering, cert-without-CA semantics, credential pruning).
3. **[PR #11847](https://github.com/zeroclaw-labs/zeroclaw/pull/11847)** — native Rust XMPP with optional OMEMO (XL, high risk) and companion #12015 (ChaCha20Poly1305 unification) and #12319 (duplicate-crate allowances). Community push for channel breadth; this is one of the largest open feature trains.
4. **[PR #12244](https://github.com/zeroclaw-labs/zeroclaw/pull/12244)** — scoped dashboard chat through core RPC (XL, do-not-merge, blocked). Long-running core architecture PR.

Underlying needs: (a) an unblocked release pipeline, (b) lower-friction remote login, (c) broader/more modern channel support, (d) consolidation of duplicated crypto/dependency surfaces.

## 5. Bugs & Stability

Ranked by severity (all filed/updated 2026-11-09 unless noted):

- **S1 / workflow-blocking:** [#12320](https://github.com/zeroclaw-labs/zeroclaw/issues/12320) — `RUSTSEC-2026-0459` fails Security job on every build; merge queue and release are deadlocked. **No fix PR yet.**
- **S0 security (review findings against open PR heads; fix = revise the PR before merge):**
  - [#12311](https://github.com/zeroclaw-labs/zeroclaw/issues/12311) — macOS Seatbelt profile grants sandboxed commands read access to all home dotfiles incl. `~/.zeroclaw` (PR #12150).
  - [#12310](https://github.com/zeroclaw-labs/zeroclaw/issues/12310) — MCP config-control bridge grants model unrestricted config writes with no approval (PR #12275).
  - [#12309](https://github.com/zeroclaw-labs/zeroclaw/issues/12309) — `sessions_prompt` target scoping unenforced; PR head doesn't compile (PR #12258).
  - [#12308](https://github.com/zeroclaw-labs/zeroclaw/issues/12308) — session delete/TTL leaves transcripts and payloads readable; no erasure path.
  - [#12307](https://github.com/zeroclaw-labs/zeroclaw/issues/12307) — gateway sessions lose `gw_` namespace → cross-session attach by paired callers.
  - [#12306](https://github.com/zeroclaw-labs/zeroclaw/issues/12306) — Matrix Goal parent turns bypass `effort_routing` allowlist (`cloud_escalation = never`).
  - [#12305](https://github.com/zeroclaw-labs/zeroclaw/issues/12305) — daemon-wide cost-limit override approvable by any allowlisted peer on non-Telegram channels.
  - [#12304](https://github.com/zeroclaw-labs/zeroclaw/issues/12304) — colony channel replies leak operator messages to external members (PR #11560).
- **S2:** [#12330](https://github.com/zeroclaw-labs/zeroclaw/issues/12330) — zerocode silently falls back to local daemon when `zerocode-config.toml` is unreadable/unparseable (risk of wrong-endpoint connections); [#12325](https://github.com/zeroclaw-labs/zeroclaw/issues/12325) — docs canonical URLs point at a release dir, so every release reshuffles SEO URLs.
- **S3:** [#12347](https://github.com/zeroclaw-labs/zeroclaw/issues/12347) — zerocode config writers can lose concurrent updates (read-modify-write, no lock); [#12326](https://github.com/zeroclaw-labs/zeroclaw/issues/12326) and [#12324](https://github.com/zeroclaw-labs/zeroclaw/issues/12324) — docs 404s for pruned versions and indexable rustdoc/404 pages.

## 6. Feature Requests & Roadmap Signals

- **Remote access without pairing ceremony** — #12317's OIDC self-service device certificate bootstrap, gated behind operator opt-in per [#12337](https://github.com/zeroclaw-labs/zeroclaw/issues/12337). Strong candidate for v0.9.0 once #12320 clears.
- **Channel breadth & security** — XMPP/OMEMO (#11847), owner notifications for unauthorized inbound messages ([PR #12346](https://github.com/zeroclaw-labs/zeroclaw/pull/12346)), Matrix routing hardening (#12306).
- **Migration/interop** — OpenClaw import (#12348, already closed).
- **Web UX** — voice settings for paired devices ([PR #12269](https://github.com/zeroclaw-labs/zeroclaw/pull/12269)), collapsed Thinking toggle (#11919, done), narrow-layout fixes (#11933, done).
- **Credential hygiene** — pruning superseded OIDC credential generations ([#12339](https://github.com/zeroclaw-labs/zeroclaw/issues/12339)) and defining cert-without-CA semantics ([#12338](https://github.com/zeroclaw-labs/zeroclaw/issues/12338)).

Prediction: next release will be dominated by security hardening (sandbox, session isolation, approval paths), zerocode OIDC onboarding, and channel work — contingent entirely on #12320.

## 7. User Feedback Summary

- **Frustration:** release/CI blockage (#12320) is the loudest signal — nothing can land; contributors are producing verified fixes with nowhere to merge.
- **Security-sensitive users** (self-hosted/daemon operators) are surfacing concrete isolation concerns: sandbox file access, cross-session attach, group-chat approval abuse, and message leakage. These read as real deployment pain, not theoretical.
- **Mobile/web users:** satisfied with responsive and UX fixes (#11933, #11920), indicating active dashboard usage on phones.
- **Onboarding friction:** zerocode users want pairing-free login and are willing to accept opt-in defaults (#12337); the volume of follow-ups shows the feature is wanted but the current head is not production-ready.
- **Silent-failure anxiety:** #12330 (config parse failure → local daemon fallback) and #12347 (lost config writes) are the kind of subtle degradations that erode trust in the TUI client.

## 8. Backlog Watch

- **[PR #11868](https://github.com/zeroclaw-labs/zeroclaw/pull/11868)** (open since 2026-10-19, needs-maintainer-review) — repeated-call suppression before dispatch; blocks related #11911 and has companion docs PR #12265 awaiting action.
- **[PR #11317](https://github.com/zeroclaw-labs/zeroclaw/pull/11317)** (open since 2026-10-01, XS docs) — bounded plugin webhook RPC placement exception; a month-old one-row docs PR still unreviewed.
- **[PR #11847](https://github.com/zeroclaw-labs/zeroclaw/pull/11847)** (open since 2026-10-19, XL, needs-maintainer-review) — XMPP channel train; needs review bandwidth to avoid bit-rot against master.
- **[PR #12244](https://github.com/zeroclaw-labs/zeroclaw/pull/12244)** (blocked, do-not-merge) — dashboard chat via core RPC; high risk and long-lived, at risk of conflict churn.
- **[Issue #12320](https://github.com/zeroclaw-labs/zeroclaw/issues/12320)** — not old, but the most urgent: it is the keystone blocking the entire merge queue and next release.

---
*All links reference `github.com/zeroclaw-labs/zeroclaw`. Data snapshot: 2026-11-09/10 activity window.*

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar).*