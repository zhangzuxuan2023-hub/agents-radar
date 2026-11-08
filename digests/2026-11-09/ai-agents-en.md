# OpenClaw Ecosystem Digest 2026-11-09

> Issues: 500 | PRs: 500 | Projects covered: 5 | Generated: 2026-11-08 23:13 UTC

- [OpenClaw](https://github.com/openclaw/openclaw)
- [Hermes Agent](https://github.com/nousresearch/hermes-agent)
- [IronClaw](https://github.com/nearai/ironclaw)
- [QwenPaw](https://github.com/agentscope-ai/QwenPaw)
- [ZeroClaw](https://github.com/zeroclaw-labs/zeroclaw)

---

## OpenClaw Deep Dive

# OpenClaw Project Digest — 2026-11-09

## 1. Today's Overview

OpenClaw remains highly active, with 500 issues and 500 PRs touched in the last 24 hours. However, the ratio skews toward backlog churn rather than forward momentum: 333 PRs are open versus 167 merged/closed, and 378 issues are open/active versus 122 closed. A significant share of closed items today are tagged `stale`, indicating cleanup rather than resolution. One patch release (v2026.11.2) shipped, focused narrowly on privacy/trust-boundary hardening rather than functional fixes. Overall health reads as *stable but strained*: core stability issues around SQLite contention, gateway event-loop stalls, and memory growth are recurring, long-lived, and still open.

## 2. Releases

**v2026.11.2** — Security/privacy patch, no breaking changes announced.
- Keeps configuration secrets, conversation content, tool errors, and channel payloads out of diagnostic logs.
- Enforces selected provider and navigation trust boundaries.
- Releases MCP grants after warm CLI turns.
- References: #191318, #174998
- **Migration notes:** None stated. Operators relying on diagnostic logs for debugging secret-adjacent flows should expect redaction.
- **Caveat:** v2026.11.2 does not address the open P0 updater issue (#193962), where the fix for the SQLite header-probe timeout can't be reached from 2026.10.10 via `openclaw update`.

## 3. Project Progress

167 PRs were merged/closed today (full list not provided; sampled PRs are overwhelmingly open). Notable in-flight work visible in the open PR set:
- **Control UI permissions inspection** — read-only People/Roles views (#195408), a meaningful step toward multi-user/admin capability.
- **Android reliability cluster** — pending-input display and receipt-based send recovery (#191610), pre-ack steer ownership (#195402), file-allowlist messaging (#191316), and accessibility fixes (#195194).
- **Memory efficiency** — dropping duplicate embedding cache copies (#195369) and reducing live skill-catalog memory (#195418), both directly targeting the memory-growth issues below.
- **Sandbox/network** — preventing multi-GB history downloads in shallow sandbox checkouts (#195433).
- **CLI/UX polish** — several small fixes improving error messages for unknown providers/worktrees (#195430, #195447) and profile-aware workspace paths (#195448).

Several PRs were closed by the repository's "active-PR limit" and resubmitted (#195077, #195178, #195073), suggesting contributor throughput is outpacing review capacity.

## 4. Community Hot Topics

| Item | Comments | Signal |
|---|---|---|
| [#166634](https://github.com/openclaw/openclaw/issues/166634) — checkpoint failure after crash leaves agent permanently `UNAVAILABLE` | 26 | Highest-engagement issue; agent becomes unrecoverable |
| [#191944](https://github.com/openclaw/openclaw/issues/191944) — macOS `gateway stop` fails with process-inspection error | 25 | Basic lifecycle command broken on Intel macOS |
| [#38327](https://github.com/openclaw/openclaw/issues/38327) — "Cannot convert undefined or null to object" with google-vertex | 18 (👍3) | Long-lived P1 regression since 2026.3.2 |
| [#53408](https://github.com/openclaw/openclaw/issues/53408) — tool params silently dropped after long conversations | 17 (👍2) | Closed as stale despite serious data-integrity implication |
| [#67413](https://github.com/openclaw/openclaw/issues/67413) — per-agent dreaming config | 16 (👍5) | Highest 👍 count; OOM from parallel dreaming |

**Underlying needs:** Users are hitting *recovery and resource-isolation* problems — agents that won't come back after a crash, restarts/stops that misreport, and memory/tooling behavior that degrades over long sessions. The stale-closure of #53408 (silent parameter loss) is a notable community-friction risk: high-impact bugs are aging out without fixes.

## 5. Bugs & Stability

Ranked by severity (as labeled):

**P0 / Release blockers**
- [#193962](https://github.com/openclaw/openclaw/issues/193962) — updater probe timeout blocks reaching the fix from 2026.10.10. Fix exists upstream (#187197, in 2026.11.5-beta.1) but is unreachable via `openclaw update`.
- [#162211](https://github.com/openclaw/openclaw/issues/162211) — startup blocks event loop 40–200s; health monitor misreads stall as disconnect → restart loop. No fix PR.
- [#158592](https://github.com/openclaw/openclaw/issues/158592) — post-sleep/wake runtime publication timeout; all inbound messages fail until restart. No fix PR.
- [#137177](https://github.com/openclaw/openclaw/issues/137177) — built-in WeCom plugin uninstallable.

**P1 / Crash loops & data integrity**
- [#181432](https://github.com/openclaw/openclaw/issues/181432) — baseline RSS ~1.9 GB (from ~300 MB) on ARM64; SQLite timeouts.
- [#185578](https://github.com/openclaw/openclaw/issues/185578) — heap floor ratchets ~25 MiB/h until V8 abort.
- [#182369](https://github.com/openclaw/openclaw/issues/182369) — off-heap RSS growth ~0.1 GiB/h, monitor never fires.
- [#191847](https://github.com/openclaw/openclaw/issues/191847) — SQLite worker OOM-killed at 512 MB; session permanently "admission is closed."
- [#117262](https://github.com/openclaw/openclaw/issues/117262) — 3 concurrent SQLite write handles → ~33s event-loop stalls.
- [#157255](https://github.com/openclaw/openclaw/issues/157255) — turn claim not released after lane timeout; 90+ min session wedge.
- [#163566](https://github.com/openclaw/openclaw/issues/163566) — `session-resource-loader` burns ~100s CPU per turn post-repair.
- [#126906](https://github.com/openclaw/openclaw/issues/126906) — denying `write` tool silently disables memory persistence *and* reports success (data-loss + silent failure).

**Fix PRs exist (open):** #195446 (prepared-runtime staleness), #195452/#195451 (doctor exit-non-zero blocking Telegram/rescue), #195369 (memory cache duplication), #195418 (skill catalog memory), #195178 (withdrawn plugin requests). No fix PRs are linked for the P0 crash-loop cluster.

**Pattern:** SQLite concurrency, memory growth (both heap and off-heap), and post-update/repair degradation form the dominant stability theme.

## 6. Feature Requests & Roadmap Signals

- **Per-agent dreaming configuration** ([#67413](https://github.com/openclaw/openclaw/issues/67413)) — highest 👍 (5); memory-spike/OOM driven. Closed stale, but likely to resurface.
- **SecretRef support in MCP env/headers** ([#76493](https://github.com/openclaw/openclaw/issues/76493), 👍6) — secret-management best practices blocked by schema.
- **Opt-in personal identity on iOS/macOS** ([#162164](https://github.com/openclaw/openclaw/issues/162164)) — multi-user identity roadmap signal, already partially merged (#164593).
- **Tool schema token overhead reduction** ([#14785](https://github.com/openclaw/openclaw/issues/14785), ~3,500 tok/session) — cost-efficiency request.
- **Markdown preview in Review** ([#128090](https://github.com/openclaw/openclaw/issues/128090), 👍4); **Android notification QoL** ([#143387](https://github.com/openclaw/openclaw/issues/143387)); **onboarding memory/embedding step** ([#16670](https://github.com/openclaw/openclaw/issues/16670)).
- **Provider-aware backpressure for subagents** ([#126727](https://github.com/openclaw/openclaw/issues/126727)); **backup manifest omissions** ([#107433](https://github.com/openclaw/openclaw/issues/107433)).

**Prediction:** The already-in-flight Control UI permissions work (#195408) and the memory-efficiency PR cluster (#195369, #195418) are the most likely to land next, given active PRs. SecretRef (#76493) and per-agent dreaming (#67413) are strong candidates for a near-term minor release if maintainers prioritize security/ops ergonomics.

## 7. User Feedback Summary

- **Reliability under recovery:** Users report agents that never return after crashes (#166634), sleeps (#158592), or lane timeouts (#157255) — with no self-healing path.
- **Silent failures:** Tool params dropped (#53408), memory saves falsely reported successful (#126906), and generic "CLI command failed" messages (multiple PRs today) erode trust.
- **Resource exhaustion:** Memory growth on long-running gateways is a recurring operational pain (three separate P1 memory issues).
- **Platform gaps:** macOS Intel service management (#191944) and Windows Docker bind-mount startup (#193262) break basic operations.
- **Positive signal:** Sustained contributor volume (500 PRs/day touched), rapid Android/web-UI polish, and resubmission behavior indicate an engaged community despite review bottlenecks.

## 8. Backlog Watch

Long-unanswered high-value items needing maintainer attention:

- [#38327](https://github.com/openclaw/openclaw/issues/38327) — P1 regression open since March 2026 (8+ months), diamond-lobster rated, with a linked open PR. Longest-standing high-severity item.
- [#14785](https://github.com/openclaw/openclaw/issues/14785) — token overhead, open since Feb 2026, needs product decision.
- [#107433](https://github.com/openclaw/openclaw/issues/107433) — backup manifest omissions, open since July, tagged needs-security-review and needs-live-repro.
- [#76493](https://github.com/openclaw/openclaw/issues/76493) — SecretRef in MCP schema, open since May, needs security review.
- [#137613](https://github.com/openclaw/openclaw/issues/137613) — pre-compaction memory flush disabled on CLI backends, open since Sept.
- [#161158](https://github.com/openclaw/openclaw/issues/161158) — gateway restart misreports failure while exiting 0; hardcoded 60s budget.
- **Process concern:** the `clawsweeper:no-new-fix-pr` tag appears on the vast majority of top issues, and several serious bugs were closed `stale` (#53408, #67413). The maintainer-review queue appears to be the primary constraint on project throughput.

---
*Data source: github.com/openclaw/openclaw, snapshot 2026-11-09. Totals reflect items updated in the prior 24h; PR comment counts were not present in the source data.*

---

## Cross-Ecosystem Comparison

# Cross-Project Comparison Report — Personal AI Assistant / Agent Open-Source Ecosystem
**Snapshot date: 2026-11-09** · Projects: OpenClaw, Hermes Agent, IronClaw, QwenPaw, ZeroClaw

---

## 1. Ecosystem Overview

The personal AI assistant/agent open-source landscape in November 2026 is characterized by a **large, engaged contributor base but a systemic review-capacity bottleneck**: across the five projects, ~140 PRs and ~70 issues were touched in 24 hours, yet only one project (ZeroClaw) merged a meaningful PR set and only one (OpenClaw) shipped a release. Projects are converging on a common architecture — gateway/daemon core, pluggable channel adapters (Telegram/Discord/Slack/WeCom/QQ), MCP tool integration, persistent memory, and sandboxed multi-agent orchestration — while differentiating mainly in **identity/access models, memory implementation, and platform coverage**. The dominant community-reported risk themes are **silent failures** (config resets, dropped tool params, undetected fallbacks), **resource growth** (SQLite contention, heap/off-heap RSS ratcheting), and **multi-tenant isolation** (shared memory leaking across users in group channels). Security hardening — secret redaction in logs, trust-boundary enforcement, A2A auth — is the fastest-moving change category across the board.

---

## 2. Activity Comparison

| Project | Issues (24h, open/total) | PRs (24h, open/merged-closed) | Releases (24h) | Health Score* | Notes |
|---|---|---|---|---|---|
| **OpenClaw** | 378 open / 500 touched; 122 closed | 333 open / 167 merged-closed | **v2026.11.2** (privacy patch) | **6.5 / 10** | Highest raw volume; stable but strained. P0 updater unreachable; large stale-closure of serious bugs; review queue is primary constraint. |
| **Hermes Agent** | Majority open, 5 closed | 50 open / **0 merged** | None | **5.5 / 10** | High engagement, zero integration velocity. 7-month-old security fix PRs unmerged. Two security issues triaged/closed. |
| **IronClaw** | 0 updated | 5 open (Dependabot) / 1 closed (superseded) | None | **4.5 / 10** | Operationally quiet; pure dependency churn. Longest open PR 78 days (wasm runtime upgrade). |
| **QwenPaw** | 7 active / all open | 15 open / **0 merged** | None | **5.5 / 10** | Strong same-day issue→PR loop (txohyeah cluster) but no merges. Duplicate-fix PR pair (#8622/#8624). |
| **ZeroClaw** | 14 updated / ~7 closed | 43 open / 7 merged-closed | None (v0.9.0 gated) | **7.5 / 10** | Highest effective velocity; organized milestone (#7432), stacked PRs, prompt bug fixes. Two new S2 auth/data bugs unfixed. |

*Health score is a synthesized 1–10 judgment weighting: merged-PR throughput, severity of open bugs, release cadence, contributor responsiveness, and backlog staleness. It is not a maintainer-published metric.*

**Aggregate:** 4 of 5 projects had **zero or near-zero merge throughput** in the window — the single most consistent ecosystem-wide signal.

---

## 3. OpenClaw's Position

**Advantages vs peers**
- **Scale and maturity:** 500 issues + 500 PRs touched/day is an order of magnitude above every peer (next largest: Hermes at 50/50; ZeroClaw at 14/50). Only project shipping a release in-window.
- **Breadth of surface:** enterprise channels (WeCom), Android/web/desktop clients, CLI updater, multi-user Control UI — a superset of peer feature scope.
- **Android + web-UI polish velocity** is unmatched (pending-input recovery, pre-ack steering, accessibility fixes all in open PRs today).

**Technical approach differences**
- Ships as a **Node/TS runtime with SQLite-backed state** — peer projects are Rust (IronClaw/ZeroClaw) or Python (Hermes/QwenPaw). This correlates directly with its signature failure mode: **SQLite write-handle contention causing multi-second to ~33s event-loop stalls** — a class of bug largely absent in the Rust projects and handled differently in Python.
- **Updater-integrated release pipeline** (`openclaw update`) is unique but currently its Achilles' heel: P0 #193962 blocks reaching the fix from 2026.10.10.

**Community-size comparison**
- OpenClaw's daily touch volume (~1,000 items) implies a contributor base roughly **10× Hermes, ~20× QwenPaw/ZeroClaw, and >100× IronClaw**. However, this volume is *not* translating into merged output (167 closed vs 333 open, with many closures tagged `stale`) — growth is outpacing governance.

**Caveat for decision-makers:** OpenClaw is the **reference implementation by adoption**, but its reliability posture (P0 crash loops without fix PRs, high-impact bugs closed as stale — e.g., #53408 silent tool-param loss) means adopters should pin versions and avoid relying on `openclaw update` until #193962 clears.

---

## 4. Shared Technical Focus Areas

| Requirement | Projects | Specific Needs |
|---|---|---|
| **Multi-tenant / per-user memory isolation** | Hermes (#17254, #28226), OpenClaw (#162164 identity work), ZeroClaw (roster identities #11264–#11313) | Per-`user_id` memory namespacing; scoped dashboard chat for non-admins; shared-channel preference bleed is the #1 latent demand in Hermes. |
| **Silent-failure elimination / observability** | QwenPaw (#8475, #8103), OpenClaw (#126906, #53408), Hermes (#148961 A2A auth degrades silently) | Warning logs on config mutation; explicit fallback-model signaling; no success-report on failed writes. |
| **Resource growth & concurrency correctness** | OpenClaw (P1 heap #185578, off-heap #182369, SQLite #117262), Hermes (cron session leaks #145314/#145791), QwenPaw (SQLite error masking #8620) | Bounded RSS; lease release on lane timeout; duplicate-query elimination; real-error propagation. |
| **MCP ecosystem hardening** | OpenClaw (#76493 SecretRef in MCP schema), Hermes (#118572 MCP metadata sanitization), ZeroClaw (MCP image results #10501) | Secret management in env/headers; unicode-tag sanitization; OpenAI-provider interop. |
| **Identity & gateway trust boundaries** | OpenClaw (v2026.11.2 trust boundaries), ZeroClaw (dashboard sign-in, JWKS), Hermes (A2A auth #148961) | Sign-in lifecycle, authorization on disabled agents, config-write refusal on relay-served dashboards. |
| **Multi-agent orchestration safety** | ZeroClaw (delegation workspace #9872, peer-turn durability #9597), OpenClaw (per-agent dreaming OOM #67413), QwenPaw (/steer #8318) | Workspace sandboxing, attributable peer messaging, hop limits, mid-execution steering. |
| **Cross-platform parity** | Hermes (Windows ACP hang #135977), OpenClaw (macOS Intel #191944), ZeroClaw (CI matrix #7461), QwenPaw (Windows desktop #8627) | Windows/macOS test matrices; launchd/service lifecycle; long-path handling. |
| **Cost/token efficiency** | OpenClaw (#14785 ~3.5k tok/session), QwenPaw (cron thinking levels #8626, row-only recall #8625), Hermes (KV-cache window #5306) | Schema token overhead reduction; per-job model selection; retrieval optimization. |

---

## 5. Differentiation Analysis

| Dimension | OpenClaw | Hermes Agent | IronClaw | QwenPaw | ZeroClaw |
|---|---|---|---|---|---|
| **Core stack** | Node/TS + SQLite | Python | Rust (wasm runtime) | Python | Rust |
| **Primary focus** | Full-surface personal assistant (channels + clients + admin) | Message-platform agent w/ memory OS ambitions | NEAR-ecosystem agent runtime | Pragmatic chat-ops + Desktop beta | Security-hardened gateway & multi-agent identity |
| **Target users** | Power users, teams, multi-channel operators | Community/group-chat deployments, Telegram/Discord/Feishu | Blockchain/AI infra developers | Cost-conscious production pipelines | Self-hosted, SSO/OIDC operators, multi-agent teams |
| **Distinctive bet** | Breadth + updater as a platform | Multi-tenant memory as headline feature | Runtime currency (wasm, dependency hygiene) | Fast contributor loop, Desktop UX | Identity/access control as first-class product |
| **Signature weakness** | SQLite/event-loop contention; stale-closure of high-severity bugs | Review freeze (0 merges; 7-month security PRs) | Near-zero feature velocity; bot-only activity | Unmerged backlog despite strong PR supply | XL stacked-PR dependency chains aging 5–6 wk |
| **Release signal** | v2026.11.2 privacy patch | Biweekly historically, paused | None | 2.2.2.post1 stable / 2.3.0-beta.2 | v0.9.0 gated on tracker #7432 |

**Architecture differentiation summary:**
- **Rust (IronClaw, ZeroClaw)** trades development velocity for runtime stability — notably absent from the memory-growth and event-loop-stall bug classes that dominate OpenClaw's P1 list. ZeroClaw converts this into a security-first posture.
- **Python (Hermes, QwenPaw)** enables fast contributor turnaround (QwenPaw's same-day issue→PR pairs) but exhibits Windows/platform fragility and concurrency leak patterns (cron leaks, worker-thread middleware skips).
- **Node/TS (OpenClaw)** delivers the widest client surface at the cost of SQLite/event-loop contention and the heaviest review burden.

---

## 6. Community Momentum & Maturity

**Activity tiers:**
- **Tier 1 — Ecosystem anchor:** OpenClaw (~1,000 daily touches, shipping releases, but governance-strained).
- **Tier 2 — Active but integration-bound:** ZeroClaw (best velocity-to-organization ratio; milestone-driven), Hermes Agent (high engagement, zero merges — the clearest *stalled-integration* case).
- **Tier 3 — Niche/early:** QwenPaw (responsive contributors, Desktop beta, no merges in-window), IronClaw (maintenance-only this period).

**Rapidly iterating:** ZeroClaw (v0.9.0 identity/gateway stack, stacked-PR discipline), OpenClaw's Android/web-UI surface, QwenPaw's bug-fix loop.

**Stabilizing / consolidating:** IronClaw (dependency churn only), OpenClaw's core (recurring long-lived stability issues indicate architectural limits now in maintenance mode rather than rapid evolution).

**Maturity read:**
- **Process maturity:** ZeroClaw > OpenClaw > Hermes ≈ QwenPaw > IronClaw (milestone trackers and dependency-annotated stacked PRs vs. stale-closure and active-PR-limit churn).
- **Codebase maturity (stability):** ZeroClaw ≈ IronClaw (Rust) > QwenPaw > Hermes > OpenClaw (most feature-rich, most stability debt).
- **Contributor retention risk:** Highest at Hermes (7-month-old security PRs) and QwenPaw (3-month-old first-contributor auth fix #7066); lowest at ZeroClaw (explicit follow-up culture).

---

## 7. Trend Signals — Value for AI Agent Developers

1. **Silent failure is the new top-tier bug class.** Across QwenPaw (#8475, #8103), OpenClaw (#126906, #53408), and Hermes (#148961), users are reporting *behavior changes without signals* — config resets, model swaps, dropped params, auth downgrades. **Actionable:** design agents with explicit fallback metadata, config-mutation warnings, and fail-loud write paths; treat "reports success but didn't" as a P0-class defect. This is a trust-architecture requirement, not a UX nicety.

2. **Review capacity, not contributor supply, is the ecosystem ceiling.** Four of five projects had zero/near-zero merges despite 100+ PRs in flight. **Actionable:** adopt auto-merge for patch-level dependency groups (IronClaw pattern), consolidate stacked-PR dependencies (ZeroClaw pain), and run focused security-merge sprints (Hermes has 4 of its 5 oldest PRs as security fixes). For adopters: **judge projects by merged security-fix latency, not issue volume.**

3. **Multi-tenant memory is the next platform battleground.** Hermes (#17254 + Memory OS #28226), OpenClaw identity work, and ZeroClaw roster identities all point the same direction. **Actionable:** build memory with `user_id`/`tenant_id` namespacing from day one; retrofitting isolation into a shared `USER.md` model is a multi-month drag.

4. **SQLite is a scaling cliff for agent state.** OpenClaw's P1 cluster (write-handle contention, worker OOM, ~33s stalls) and QwenPaw's error-masking bug both stem from SQLite under concurrent agent workloads. **Actionable:** for multi-agent/concurrent deployments, evaluate Rust-native or externalized state stores, or strictly serialize writes with bounded queues and real-error propagation.

5. **Security hardening is the fastest-shipping change category.** OpenClaw's only in-window release was privacy/trust-boundary; Hermes closed two security issues and has a backlog of redaction fixes; ZeroClaw is shipping JWKS/authorization policy. **Actionable:** expect breaking config changes around trust boundaries (e.g., ZeroClaw #11406 `!` macOS broad-root refusal) — pin configs and read release notes.

6. **Identity-aware, scoped agent operations are entering the release pipeline** (ZeroClaw v0.9.0 scoped dashboard chat, JWKS validation, relay-served config-write refusal). **Actionable:** if deploying agents to shared dashboards or group channels, plan for OIDC/JWKS integration and per-scope authorization now; "disabled ≠ inert" must be enforced at the gateway layer (see #12289).

7. **Cost/latency engineering is becoming a production requirement** (per-job thinking levels, row-only recall, token-schema reduction, KV-cache budgeting). **Actionable:** expose model-selection and retrieval-granularity controls; users are running pipelines, not demos.

8. **Platform parity is a competitive differentiator, not a checkbox.** Windows is a second-class citizen across Hermes (ACP hang), QwenPaw (desktop/git long-path), and OpenClaw (macOS Intel lifecycle); only ZeroClaw actively tracks a CI matrix fix (#7461). **Actionable:** treat cross-OS CI as release-gating for desktop/gateway products.

---

**Bottom line for decision-makers:** OpenClaw is the reference platform by adoption but carries the heaviest reliability and governance debt — pin versions, avoid the in-app updater until #193962 clears, and independently verify "closed" serious bugs. ZeroClaw is the best-organized project for security/identity-sensitive deployments. Hermes is the strongest multi-tenant memory bet *if* review capacity recovers. QwenPaw offers fast community responsiveness for cost-sensitive Python deployments. IronClaw is operationally quiet and best evaluated as a stable Rust runtime component rather than a feature platform.

---

## Peer Project Reports

<details>
<summary><strong>Hermes Agent</strong> — <a href="https://github.com/nousresearch/hermes-agent">nousresearch/hermes-agent</a></summary>

# Hermes Agent — Project Digest
**Date: 2026-11-09** · Source: `github.com/NousResearch/hermes-agent`

---

## 1. Today's Overview

Hermes Agent shows **very high community activity but near-zero merge throughput**: 50 issues and 50 PRs were touched in the last 24h, yet **0 PRs were merged or closed** and **no releases were published**. The issue mix skews toward bugs (majority of the top-commented items carry `type/bug`), with a long tail of P2/P3 regressions across cron, gateway, desktop, and platform adapters. Notably, **five issues were closed today** (including `type/security` items #80936 and #118572), indicating triage is active even if integration is not. The 50-item open PR backlog with **no merges** is the clearest health signal: review bandwidth is the current bottleneck, not contributor supply. Overall: **healthy engagement, stalled integration velocity.**

---

## 2. Releases

**No new releases in the last 24h.** (Recent versions referenced in issues include v0.20.0, v0.22.5, v0.22.7, and v0.23.0, suggesting a roughly biweekly cadence prior to this window.)

---

## 3. Project Progress

**Merged/closed PRs today: 0.** No features advanced to `main` via PR merge in this window.

**Issues closed today (5):**
- [#88579](https://github.com/NousResearch/hermes-agent/issues/88579) — Slack clarify button labels truncate on mobile *(fixed)*
- [#151237](https://github.com/NousResearch/hermes-agent/issues/151237) — Missing labels on recent issues *(labeling pipeline restored)*
- [#80936](https://github.com/NousResearch/hermes-agent/issues/80936) — `redact_sensitive_text` misses cookie-shaped credentials *(security hardening)*
- [#118572](https://github.com/NousResearch/hermes-agent/issues/118572) — MCP server metadata bypasses unicode-tag sanitization *(security)*
- [#80483](https://github.com/NousResearch/hermes-agent/issues/80483) — Cron misparses named Telegram private-DM topics *(fixed)*

**Assessment:** Two security-boundary issues resolved is a positive signal, but closure without accompanying merged PRs (or at least none visible in this window) means the fixes may be landing via direct commits or were resolved as already-fixed/duplicate. This should be verified by maintainers.

---

## 4. Community Hot Topics

Ranked by comment count:

| # | Item | Comments | Signal |
|---|------|----------|--------|
| 1 | [#80280](https://github.com/NousResearch/hermes-agent/issues/80280) Kanban stale/superseded worker process groups | 7 👍1 | **Concurrency correctness** — stale + replacement workers mutate the same worktree. Serious data-integrity risk for agent orchestration. |
| 2 | [#17254](https://github.com/NousResearch/hermes-agent/issues/17254) Multi-user preference memory | 7 | **Multi-tenant memory isolation** — shared `USER.md` leaks preferences across users in Feishu/Discord/Telegram groups. High-demand, needs-decision tag. |
| 3 | [#145314](https://github.com/NousResearch/hermes-agent/issues/145314) Quiet cron leaves permanent empty session | 7 | **Cron hygiene** — session leak per firing. |
| 4 | [#135977](https://github.com/NousResearch/hermes-agent/issues/135977) `hermes acp` hangs on Windows | 6 | **Platform-blocking** — `session/new` never returns; Windows users blocked. |
| 5 | [#148414](https://github.com/NousResearch/hermes-agent/issues/148414) Webapp menu overflow at non-100% UI scale | 4 | **Desktop UX** — visible, reproducible. |
| 6 | [#144754](https://github.com/NousResearch/hermes-agent/issues/144754) launchd update-restart undischargeable | 4 | **macOS update lifecycle** — fails closed on supervised hosts. |

**Underlying needs analysis:**
- **Two of the top three threads concern multi-user/multi-tenant isolation** (#17254, plus the related Memory OS proposal #28226). This is the loudest latent demand: Hermes is being deployed in shared channels and the memory model assumes a single user.
- **Agent process lifecycle correctness** (#80280) is the most technically consequential — concurrent process trees mutating shared worktrees is a silent corruption path, not just a nuisance.
- **Cron session leakage** appears twice in the top tier (#145314, #145791), suggesting a systemic resource-leak theme rather than one-off bugs.

---

## 5. Bugs & Stability

Ranked by severity (per reporter-assigned priority and blast radius):

**P2 — High**
- [#151280](https://github.com/NousResearch/hermes-agent/issues/151280) **Stale-stream watchdog kills live streams.** Ignores SSE `ping`/comment keepalives; terminates a live `chat_completions` stream at 240s during long Claude Opus thinking behind OpenAI-compatible proxies. *No fix PR linked.*
- [#148961](https://github.com/NousResearch/hermes-agent/issues/148961) **🔒 A2A auth silently degrades to localhost-only / no auth** on systemd installs (env scrubbing of `*TOKEN*`). Security-relevant misconfiguration that fails open in a subtle way.
- [#151246](https://github.com/NousResearch/hermes-agent/issues/151246) **🔒 Routed-profile middleware skipped on worker threads** → privacy plugin fails open. Profile isolation broken on Desktop multiplex.
- [#151247](https://github.com/NousResearch/hermes-agent/issues/151247) Routed-profile child env strips `MNEMOSYNE_EMBEDDING_*` → memories stored **without vectors**. Silent data-quality degradation.
- [#151351](https://github.com/NousResearch/hermes-agent/issues/151351) Desktop Windows ticket bridge kills python helper on timers → full boot re-run from one slow ticket.
- [#145314](https://github.com/NousResearch/hermes-agent/issues/145314) / [#145791](https://github.com/NousResearch/hermes-agent/issues/145791) Cron empty-session leaks (two variants).
- [#151385](https://github.com/NousResearch/hermes-agent/issues/151385) Removing/pausing cron job orphans non-terminal incidents — ledger unreconcilable.
- [#144728](https://github.com/NousResearch/hermes-agent/issues/144728) `session.resume` omits `running`/`status` for live unpersisted sessions → composer locks.
- [#135977](https://github.com/NousResearch/hermes-agent/issues/135977) Windows ACP hang (platform-blocking).
- [#82255](https://github.com/NousResearch/hermes-agent/issues/82255) Env strip removes `CONDA_PREFIX` but leaves `CONDA_SHLVL` → breaks conda.

**P3 — Moderate**
- [#149026](https://github.com/NousResearch/hermes-agent/issues/149026) Cookie-auth writes 403 behind TLS-terminating router (Origin stripped).
- [#149621](https://github.com/NousResearch/hermes-agent/issues/149621) Empty `~/.bash_profile` shadows `~/.profile` → `hermes` missing from PATH.
- [#143001](https://github.com/NousResearch/hermes-agent/issues/143001) Telegram document albums rejected (`media_text_inlined_length`).
- [#151053](https://github.com/NousResearch/hermes-agent/issues/151053) Completion notification becomes empty for long single-line output.
- [#148414](https://github.com/NousResearch/hermes-agent/issues/148414) Webapp menu overflow at non-100% UI scale.

**Fix PRs visible:** The top 20 PRs are predominantly older (April–August 2026) bug/security fixes still awaiting merge. **None of today's P2 bugs appear to have a linked fix PR yet** — a concerning gap given several are security-boundary issues.

> **Note on spam:** Two issues opened today ([#151517](https://github.com/NousResearch/hermes-agent/issues/151517), [#151518](https://github.com/NousResearch/hermes-agent/issues/151518)) are empty "probe" issues from `kyanite-factory-bot[bot]`. Recommend rate-limiting/reporting.

---

## 6. Feature Requests & Roadmap Signals

**Most requested:**
1. **Multi-user preference memory** ([#17254](https://github.com/NousResearch/hermes-agent/issues/17254)) — per-`user_id` memory isolation with independent storage. Paired with the broader **Memory OS acceptance layer** proposal ([#28226](https://github.com/NousResearch/hermes-agent/issues/28226)) covering diagnostics, inventory, and isolation tests. *Prediction: multi-tenant memory isolation is the single most likely headline feature for the next minor release given duplicate demand and `needs-decision` labeling.*
2. **UI localization / i18n** ([#114195](https://github.com/NousResearch/hermes-agent/issues/114195)) — `display.language` in `config.yaml`, pt-BR requested first. Low-effort, high-reach; plausible near-term.

**PR-side feature candidates awaiting merge:**
- Per-topic skill binding + auto-discovery for Telegram forums ([#5489](https://github.com/NousResearch/hermes-agent/pull/5489))
- Dual message queue: follow-up (Alt+Enter) + steering ([#5319](https://github.com/NousResearch/hermes-agent/pull/5319))
- Sliding-window context budget for KV-cache prefix optimization ([#5306](https://github.com/NousResearch/hermes-agent/pull/5306))
- Raise default memory char limits ([#5323](https://github.com/NousResearch/hermes-agent/pull/5323))
- One-shot handoff for next-session tasks ([#5310](https://github.com/NousResearch/hermes-agent/pull/5310))

**Ecosystem signal:** Multiple *plugin-catalog PRs landed today* — `seedguard` (#151543), `salts-secrets` (#151411), `proof-run` (#151252), plus a three-plugin SHA bump (#149734). The plugin ecosystem is warming up, with a notable theme of **secrets firewalls and identity/context isolation** — corroborating the multi-tenant demand above.

---

## 7. User Feedback Summary

**Pain points expressed:**
- **Shared-agent deployments break the memory model.** Users running Hermes in group chats report cross-user preference bleed — a fundamental fit issue for team/community use cases.
- **Windows is a second-class platform right now.** ACP hang, desktop ticket-bridge instability, and process-tree issues are concentrated there.
- **Cron is leaky.** Practitioners running scheduled/monitor jobs accumulate empty sessions and orphaned incidents — operational hygiene debt.
- **Security-conscious users are finding real gaps** (cookie redaction, A2A auth degradation, profile middleware skip, MCP metadata injection). These are reported with care and process-level evidence, indicating sophisticated users, not noise.
- **Update lifecycle friction** on macOS launchd and Linux PATH — install/upgrade ergonomics need attention.

**Satisfaction signal:** Reports are detailed, reproducible, and often include proposed patches (e.g., #82255 discloses AI-assisted authorship with a patch attached). Community members are doing triage-grade work — a sign of investment, but also of review-capacity shortfall.

---

## 8. Backlog Watch

**Oldest high-value PRs awaiting merge** (all created ~2026-04-05/06, still open after ~7 months):

| PR | Age | Why it matters |
|----|-----|----------------|
| [#5516](https://github.com/NousResearch/hermes-agent/pull/5516) `_detect_tool_failure` structured-JSON handling | ~7 mo | Correctness of failure detection — `blast-broad` risk tag. |
| [#5459](https://github.com/NousResearch/hermes-agent/pull/5459) 🔒 Redact signed vision image URLs | ~7 mo | Security; signed URLs leak in logs. |
| [#5418](https://github.com/NousResearch/hermes-agent/pull/5418) 🔒 Harden Solana skill (SSRF/URL injection) | ~7 mo | Security; environment-controlled RPC_URL. |
| [#5341](https://github.com/NousResearch/hermes-agent/pull/5341) 🔒 WeCom credential log redaction | ~7 mo | Security; access tokens in INFO logs. |
| [#5300](https://github.com/NousResearch/hermes-agent/pull/5300) 🔒 Mattermost media URL redaction | ~7 mo | Security; bearer credentials in logs. |

**Also watch:**
- [#80280](https://github.com/NousResearch/hermes-agent/issues/80280) Kanban worker process-group race — top-commented issue, no fix PR, high corruption risk.
- [#17254](https://github.com/NousResearch/hermes-agent/issues/17254) Multi-user memory — `needs-decision`; needs maintainer steering to avoid another 7-month wait.
- [#28226](https://github.com/NousResearch/hermes-agent/issues/28226) Memory OS acceptance layer — design doc that could absorb #17254.

**Recommendation:** The backlog pattern — *four of the five oldest open PRs are security fixes* — is the most actionable finding. A focused security-merge sprint would both reduce risk and signal responsiveness to the community's highest-quality contributions, while unblocking the 50-PR queue that has gone merge-dry for 24h.

</details>

<details>
<summary><strong>IronClaw</strong> — <a href="https://github.com/nearai/ironclaw">nearai/ironclaw</a></summary>

# IronClaw Project Digest — 2026-11-09

## 1. Today's Overview

IronClaw shows low but steady maintenance activity: no issues were updated in the last 24 hours, while six PRs saw activity and one was closed. All visible activity is dependency automation via Dependabot across Rust crates and GitHub Actions — there are no feature, bug-fix, or user-facing PRs in today's window. The closure of PR #8160 (a 43-package dependency bump) was immediately superseded by PR #8169 (the same group with 44 updates), indicating routine bot churn rather than substantive review. Overall project health appears stable but operationally quiet; the key risk signal is a growing backlog of long-lived, still-open dependency PRs (one since August), which suggests maintainer bandwidth is directed elsewhere.

## 2. Releases

No new releases in this period.

## 3. Project Progress

- **[PR #8160](https://github.com/nearai/ironclaw/pull/8160)** [CLOSED] — Dependency group bump (43 updates, "everything-else" group including `thiserror` 2.0.20→2.0.21, `uuid` 1.24.0→1.28.0, `base64`). Closed on 2026-11-08 after being open since 2026-10-25; effectively superseded by #8169.
- No merged feature or bug-fix PRs today. No user-facing features advanced.

## 4. Community Hot Topics

No issues or PRs show comment counts or reactions in the provided data (all comment fields are `undefined`, all 👍 = 0). The most notable items by recency/volume:

- **[PR #8169](https://github.com/nearai/ironclaw/pull/8169)** — [OPEN] newest dependency bump, 44 packages, including `uuid` 1.24.0→1.29.0.
- **[PR #8159](https://github.com/nearai/ironclaw/pull/8159)** — [OPEN] GitHub Actions group, 11 updates, notably `anthropics/claude-code-action` 1.0.183→1.0.259 (a large jump suggesting AI-agent CI tooling is actively evolving upstream).
- **[PR #7834](https://github.com/nearai/ironclaw/pull/7834)** — [OPEN] wasm group (`wasmtime`, `wasmtime-wasi`, `wit-component`, `wit-parser`), labeled `size: L`, `risk: medium`, `contributor: experienced`.

**Underlying need:** There is no visible community discussion to analyze; engagement data (comments, reactions) is absent, so demand signals cannot be inferred from today's data. The CI action bump to `claude-code-action` hints at the project's AI-assisted development workflow, but this is infrastructure, not community demand.

## 5. Bugs & Stability

No bugs, crashes, or regressions were reported or updated today. No fix PRs exist in today's window.

Stability-adjacent note: **[PR #7834](https://github.com/nearai/ironclaw/pull/7834)** (wasm runtime upgrades) carries a `risk: medium` label and has been open since 2026-08-23 — the longest-running open PR. The wasm runtime is a core execution surface for an agent platform; delayed upgrades there could carry latent security/compatibility exposure, though no specific vulnerability is cited in the data.

## 6. Feature Requests & Roadmap Signals

No feature requests were filed or updated in the last 24 hours. No roadmap signals are inferable from today's dataset. The only forward-looking technical signal is the pending wasm toolchain upgrade (#7834), which would improve runtime currency rather than add features — a plausible candidate for the next release cycle once reviewed.

## 7. User Feedback Summary

No user feedback is present in today's data (zero issues updated). Pain points, use cases, and satisfaction metrics cannot be assessed from this window. The complete absence of issue traffic — neither new reports nor closures — may indicate a quiet user base, effective triage elsewhere in the tracker, or reduced user engagement; the data does not distinguish between these.

## 8. Backlog Watch

Items needing maintainer attention, ranked by age/staleness:

1. **[PR #7834](https://github.com/nearai/ironclaw/pull/7834)** — Open **78 days** (since 2026-08-23). Wasm runtime group upgrade, flagged `size: L` and `risk: medium`. Longest-stalled item; likely requires careful review due to runtime risk.
2. **[PR #8140](https://github.com/nearai/ironclaw/pull/8140)** — Open 22 days. `serde_json` 1.0.151→1.0.154 (serialization group). Low-risk, single-crate bump that should be trivially mergeable.
3. **[PR #8141](https://github.com/nearai/ironclaw/pull/8141)** — Open 22 days. Tokio ecosystem group (5 updates, incl. `tokio` 1.53.1→1.53.3). Patch-level runtime fixes; low risk.
4. **[PR #8159](https://github.com/nearai/ironclaw/pull/8159)** — Open 15 days. Actions group (11 updates). The large `claude-code-action` version jump warrants a check for breaking CI behavior.

**Pattern:** Five open Dependabot PRs spanning three dependency groups suggest a merge bottleneck on routine maintenance. Consolidating reviews or enabling auto-merge for patch-level groups (#8140, #8141) would reduce backlog and free maintainer time for feature work.

---

*Note: All activity, comment, and reaction data is derived solely from the provided snapshot; engagement metrics were unavailable (null/zero) for every item, so community sentiment could not be independently corroborated.*

</details>

<details>
<summary><strong>QwenPaw</strong> — <a href="https://github.com/agentscope-ai/QwenPaw">agentscope-ai/QwenPaw</a></summary>

# QwenPaw Project Digest — 2026-11-09

## 1. Today's Overview

QwenPaw shows **high-velocity, maintenance-heavy activity** with 7 active issues and 15 open PRs updated in the last 24 hours, but **zero merges and zero releases** — a pure intake day with no completions. The issue queue is dominated by community reporters (notably `txohyeah` filing three related memory/search bugs and their corresponding fix PRs the same day), suggesting strong contributor engagement but strained maintainer throughput. Two distinct bug clusters emerged: **silent configuration/state failures** (QQ channel `enabled` reset, silent model fallback) and **memory/recall search correctness** (SQLite error masking, duplicate turn-boundary queries). Notably, several same-day issue→PR pairs indicate a healthy contributor loop, but the **all-open, zero-merge pattern** raises a throughput risk if it persists.

---

## 2. Releases

**None.** No new releases in the last 24h. Current reference versions in circulation per issue reports: `2.2.2.post1` (stable pip) and `2.3.0-beta.2` (Desktop beta).

---

## 3. Project Progress

**No PRs were merged or closed today** — all 15 updated PRs remain OPEN. Feature work advanced only in the sense of new submissions:

- **[#8628](https://github.com/agentscope-ai/QwenPaw/pull/8628)** — Adds model fallback metadata (`model_used`, `model_requested`, etc.) to chat responses; directly complements open issue #8103.
- **[#8626](https://github.com/agentscope-ai/QwenPaw/pull/8626)** — Per-job `thinking_level` override for cron jobs (cost/latency optimization).
- **[#8625](https://github.com/agentscope-ai/QwenPaw/pull/8625)** — Exposes `include_turn` on `recall_history`; same-day response to issue #8621.
- **[#8630](https://github.com/agentscope-ai/QwenPaw/pull/8630)** — Fixes partial channel updates resetting `enabled` to default; same-day response to issue #8475.
- **[#8624](https://github.com/agentscope-ai/QwenPaw/pull/8624) / [#8622](https://github.com/agentscope-ai/QwenPaw/pull/8622)** — Two independent fixes for SQLite error masking (issue #8620).
- **[#8623](https://github.com/agentscope-ai/QwenPaw/pull/8623)** — Eliminates duplicate turn-boundary resolution (issue #8619).

**Observation:** The `txohyeah` cluster (issues #8619–#8621 + PRs #8623–#8625) is a model of same-day triage-and-fix, but all remain unmerged.

---

## 4. Community Hot Topics

Activity is low in absolute engagement (0 reactions, ≤2 comments per item), but the most-discussed threads reveal core reliability concerns:

| Item | Comments | Link |
|---|---|---|
| #8475 QQ channel `enabled` silently reset | 2 | [Issue #8475](https://github.com/agentscope-ai/QwenPaw/issues/8475) |
| #8103 Silent model fallback notification | 2 | [Issue #8103](https://github.com/agentscope-ai/QwenPaw/issues/8103) |

**Underlying needs:** Both top threads share one theme — **the system changes behavior silently and the user is never told.** Users are asking not just for fixes but for **observability and trust**: warning logs when config mutates, and explicit "a fallback model answered this" signals. The same-day PRs (#8630, #8628) confirm contributors recognize this.

---

## 5. Bugs & Stability

Ranked by severity:

1. 🔴 **HIGH — [#8475](https://github.com/agentscope-ai/QwenPaw/issues/8475): QQ channel `enabled` silently reset to `false` on config reload/restart.** Complete loss of QQ message send/receive with no warning. *Fix PR exists: [#8630](https://github.com/agentscope-ai/QwenPaw/pull/8630) (partial-update merge).*
2. 🟠 **MEDIUM-HIGH — [#8629](https://github.com/agentscope-ai/QwenPaw/issues/8629): Oversized historical image re-sent on every model call; stripped retry retains misleading "Image loaded" text.** Wastes tokens/bandwidth and misleads the model. *No fix PR yet.*
3. 🟠 **MEDIUM — [#8627](https://github.com/agentscope-ai/QwenPaw/issues/8627): Checkpoint service fails on Git long-path prefix + WeChat channel stuck in `DEPENDENCY_ENVIRONMENT_AMBIGUOUS` (Windows 11, 2.3.0-beta.2).** Affects the Desktop beta on Windows. *No fix PR yet.*
4. 🟡 **LOW-MEDIUM — [#8620](https://github.com/agentscope-ai/QwenPaw/issues/8620): SQLite `OperationalError` masked as `TimeoutError` after deadline.** Hides real lock/busy errors. *Two fix PRs: [#8624](https://github.com/agentscope-ai/QwenPaw/pull/8624), [#8622](https://github.com/agentscope-ai/QwenPaw/pull/8622) — potential duplicate effort to reconcile.*
5. 🟡 **LOW — [#8619](https://github.com/agentscope-ai/QwenPaw/issues/8619): Repeated turn-boundary queries per search hit.** Performance inefficiency. *Fix PR: [#8623](https://github.com/agentscope-ai/QwenPaw/pull/8623).*

**Pattern:** 4 of 5 bugs have fix PRs in flight within 0–1 days — strong responsiveness, but **none merged**, so users remain exposed today.

---

## 6. Feature Requests & Roadmap Signals

- **Fallback transparency ([#8103](https://github.com/agentscope-ai/QwenPaw/issues/8103))** — likely to land next given ready PR [#8628](https://github.com/agentscope-ai/QwenPaw/pull/8628).
- **Row-only `recall_history` search ([#8621](https://github.com/agentscope-ai/QwenPaw/issues/8621))** — efficiency win for memory-heavy agents; PR #8625 ready.
- **Mid-execution steering `/steer` command ([#8318](https://github.com/agentscope-ai/QwenPaw/pull/8318))** — long-running PR (since 2026-10-19) addressing issue #1775; a meaningful agent-control feature if it lands.
- **Per-job thinking levels ([#8626](https://github.com/agentscope-ai/QwenPaw/pull/8626))** and **Quick Add Model UX ([#8434](https://github.com/agentscope-ai/QwenPaw/pull/8434))** signal demand for **cost control and reduced configuration friction**.

**Prediction for next minor/beta:** fallback metadata + channel `enabled` fix + recall search optimizations are the highest-probability inclusions; `/steer` and cron thinking-level may slip to a later release.

---

## 7. User Feedback Summary

- **Pain point #1 — Silent failures erode trust.** Users repeatedly encounter behavior changes (channel disabled, model swapped, image dropped) **with no log or UI signal**. The recurring phrase "no warning log" in #8475 and #8103 reflects a demand for auditable, transparent agent behavior.
- **Pain point #2 — Windows/Desktop beta instability.** Issue #8627 shows environment-dependent failures (Git long paths, DAACL/temp-dir restrictions in [#8568](https://github.com/agentscope-ai/QwenPaw/pull/8568)) that block Windows Hub Local users from installing default skills.
- **Pain point #3 — Cost/latency sensitivity.** Users are optimizing token spend (cron thinking levels, row-only recall, avoiding oversized image retries), indicating production/pipeline usage rather than hobby experimentation.
- **Satisfaction signal:** same-day issue-to-PR turnaround by multiple contributors (e.g., `txohyeah`, `AdityaxDeore`) suggests a responsive, engaged community — but unmerged backlog is the visible friction point.

---

## 8. Backlog Watch

Long-unanswered items needing maintainer attention:

- **[#7066](https://github.com/agentscope-ai/QwenPaw/pull/7066)** — OAuth2 rotated `refresh_token` persistence (first-time contributor, opened **2026-08-16**, nearly 3 months stale). Fixes #7053; a security-adjacent auth bug left pending.
- **[#7307](https://github.com/agentscope-ai/QwenPaw/pull/7307)** — Chain provider config into model management (opened 2026-08-26, ~2.5 months). UX consolidation for a known five-step friction.
- **[#8318](https://github.com/agentscope-ai/QwenPaw/pull/8318)** — `/steer` mid-execution guidance (opened 2026-10-19). Feature-sized, needs review capacity.
- **[#8524](https://github.com/agentscope-ai/QwenPaw/pull/8524)** — Desktop HTML preview path decoding on Windows (opened 2026-11-01, Under Review). Windows regression risk if left stale.
- **[#8103](https://github.com/agentscope-ai/QwenPaw/issues/8103)** — Observability issue open since 2026-10-04; fix now available via #8628, should be linked/closed.

**Health note:** Contributor supply is healthy; the bottleneck is **review/merge bandwidth**. Clearing the >60-day PRs (#7066, #7307) and resolving the duplicate #8622/#8624 pair would materially improve project velocity and contributor retention.

</details>

<details>
<summary><strong>ZeroClaw</strong> — <a href="https://github.com/zeroclaw-labs/zeroclaw">zeroclaw-labs/zeroclaw</a></summary>

# ZeroClaw Project Digest — 2026-11-09

## 1. Today's Overview

ZeroClaw shows **very high development velocity** with 50 PRs and 14 issues updated in the last 24 hours, indicating an active, healthy open-source project. The activity is heavily concentrated around the **v0.9.0 release milestone**, specifically gateway/dashboard sign-in, scoped dashboard chat, and identity-access work. Notably, no new releases were published today. The open-PR backlog is large (43 open vs. 7 merged/closed), suggesting review capacity may be a bottleneck relative to contribution volume. Security-, identity-, and platform-portability work dominates the pipeline.

---

## 2. Releases

*No new releases in the last 24 hours. Omit.*

---

## 3. Project Progress

No PRs were merged/closed with publicly reported summaries beyond the following closed items today:

- **#10501 → #10502 (Closed)** — MCP tool-result images no longer return HTTP 400 on OpenAI-compatible providers by relocating image parts to a `user` message. Fix PR #10502 was closed alongside the issue. This closes a real interoperability bug affecting MCP workflows.
- **#11406 (Closed)** — `fix(config)!: refuse macOS broad roots in the filesystem channel`, completing the platform-parity story started by Windows broad-root validation (#11394). The `!` marker denotes a **breaking config change**, so maintainers should verify release notes capture it.
- **#11268 (Closed)** — Documentation alignment for gateway policy publication guidance.

**Features advancing (open but active):**
- Scoped dashboard chat for non-admin accounts via shared RPC client: **#12244**, with a review-inclusive successor **#12298** (both tagged `release:v0.9.0`).
- Plugin webhook forwarding over core RPC: **#11320**, **#11322** (stacked, dependent).
- Local password-auth provider for roster identities: **#11264**, **#11265**, **#11313** (identity-access stack).

---

## 4. Community Hot Topics

| Item | Signal | Underlying Need |
|------|--------|-----------------|
| [#7432](https://github.com/zeroclaw-labs/zeroclaw/issues/7432) — Runtime, dashboard sign-in & gateway delivery tracker | 16 comments, updated daily; `risk:high`, `release:v0.9.0` | **The v0.9.0 release is gated on this tracker.** Community/maintainers are coordinating sequencing and acceptance evidence for dashboard auth + gateway delivery. |
| [#11322](https://github.com/zeroclaw-labs/zeroclaw/pull/11322) & [#11320](https://github.com/zeroclaw-labs/zeroclaw/pull/11320) — Plugin webhooks over RPC | Large stacked PRs, `size:XL`, `needs-author-action` | Users want **plugin webhooks** delivered through the core with bounded scope; complexity is driving an explicit placement-exception proposal (#11317). |
| [#9872](https://github.com/zeroclaw-labs/zeroclaw/issues/9872) — Bounded delegate resolves to wrong workspace | 5 comments, `priority:p1`, `risk:high` | **Multi-agent delegation sandboxing** is a trust-critical feature; agents writing to the wrong workspace is a serious safety concern for collaborative agent setups. |
| [#9597](https://github.com/zeroclaw-labs/zeroclaw/issues/9597) — Durable/attributable peer-agent turns | 5 comments, `topic:agent-loop`, `risk:high` | Users need **auditable, lifecycle-bound peer messaging** — today peer turns are detached `process_message` calls. Spawned follow-up #12287 (relay-chain hop limit). |
| [#7461](https://github.com/zeroclaw-labs/zeroclaw/issues/7461) — CI matrix on Windows/macOS | 5 comments, `priority:p2` | **Cross-platform reliability** — the test suite only runs on Linux while Lint/Build already run on three OSes. |

**Analysis:** The dominant theme is **multi-tenant, identity-aware agent operations** (dashboard sign-in, scoped chat, roster passwords, relay-served dashboards). The second theme is **multi-agent safety and attribution** (delegation sandboxing, peer turn durability, relay-chain bounding). Maintainer attention is being pulled toward security hardening of the gateway/RPC boundary.

---

## 5. Bugs & Stability

Ranked by severity:

1. **S2 — [#12289](https://github.com/zeroclaw-labs/zeroclaw/issues/12289): Disabled agents still run turns** via operator WebSocket chat and restricted RPC. *No fix PR linked yet.* This is a **security-relevant authorization gap** (disabled ≠ inert) and should be prioritized.
2. **S2 — [#9872](https://github.com/zeroclaw-labs/zeroclaw/issues/9872): Bounded delegate writes to delegator's workspace** instead of its own. `priority:p1`, `risk:high`. No fix PR yet; sandbox-isolation regression for multi-agent setups.
3. **S2 — [#12288](https://github.com/zeroclaw-labs/zeroclaw/issues/12288): "Discard all" retains drafts later persisted by a section Save** — web dashboard data-integrity bug (a discarded value can be saved).
4. **P1 — [#10501](https://github.com/zeroclaw-labs/zeroclaw/issues/10501) (FIXED via #10502):** MCP image tool-results caused HTTP 400 on OpenAI-compatible providers.
5. **#12096** — Dashboard sign-ins dropped during IdP outages/429/5xx (re-verification conflates unreachable with refused). Reliability/UX risk for self-hosted SSO.
6. **#11868** (PR) — Agent may repeat identical tool calls before dispatch; PR proposes pre-dispatch refusal plus closeout retention.

**Takeaway:** Two open S2 issues (#12289, #12288) touch **authorization** and **data persistence**, both introduced recently (dated 2026-11-08). The disabled-agent bug is the most urgent stability/security item of the day.

---

## 6. Feature Requests & Roadmap Signals

| Request | Link | Likely v0.9.0? |
|---------|------|----------------|
| Scoped dashboard chat for non-admin accounts | [#11984](https://github.com/zeroclaw-labs/zeroclaw/issues/11984) | **Yes** — tagged `release:v0.9.0`; implementation in #12244/#12298 |
| Gateway tests: JWKS validation, failed writes, retirement race | [#11985](https://github.com/zeroclaw-labs/zeroclaw/issues/11985) | **Likely** alongside scoped chat |
| Non-admin agent creation with bounded risky fields | [#12290](https://github.com/zeroclaw-labs/zeroclaw/issues/12290) | Possible follow-on |
| Refuse credential/sign-in config writes on relay-served dashboards | [#12285](https://github.com/zeroclaw-labs/zeroclaw/issues/12285) | Security hardening, likely v0.9.x |
| Bash/zsh grammar-aware command policy | [#12296](https://github.com/zeroclaw-labs/zeroclaw/issues/12296), PRs [#11994](https://github.com/zeroclaw-labs/zeroclaw/pull/11994), [#12295](https://github.com/zeroclaw-labs/zeroclaw/pull/12295) | In progress |
| Peer-relay chain hop bounds (A→B→A) | [#12287](https://github.com/zeroclaw-labs/zeroclaw/issues/12287) | Follow-up to #9597, maybe v0.9.x |
| Durable, attributable peer-agent turns | [#9597](https://github.com/zeroclaw-labs/zeroclaw/issues/9597) | Larger effort; may miss v0.9.0 |

**Prediction:** v0.9.0 will be anchored by **dashboard identity + scoped chat + gateway security policy publication**, with shell-grammar policy improvements and platform CI matrix as stabilizing companions. Peer-agent durability and relay-chain bounds are more likely v0.9.x/v0.10.

---

## 7. User Feedback Summary

- **Pain points surfaced today:** multi-agent delegation writing to the wrong workspace (#9872), dashboard form drafts not truly discarded (#12288), disabled agents still being reachable (#12289), and sign-ins dropping during identity-provider outages (#12096). These are **safety, correctness, and reliability** complaints from users operating agents in shared/dashboard contexts.
- **Use cases implied:** multi-agent "delegator/executive-assistant → researcher" topologies; self-hosted dashboard with OIDC; MCP tool pipelines carrying images; Bash/zsh shell policy on macOS defaults.
- **Satisfaction signal:** constructive, follow-up-oriented issue culture (maintainer-requested follow-ups like #12287 from #9597; stacked PR discipline with explicit dependency notes) suggests engaged, satisfied contributors even where bugs are found.
- **Dissatisfaction signal:** review latency — many `needs-author-action`/`do-not-merge` XL PRs sit open since late September/October, which can frustrate authors (e.g., #11226, #11265, #11313).

---

## 8. Backlog Watch

Long-lived, high-importance items needing maintainer attention:

1. **[#7432](https://github.com/zeroclaw-labs/zeroclaw/issues/7432)** — release-blocking v0.9.0 tracker (open since 2026-06-09). Its progress directly determines release timing.
2. **[#7461](https://github.com/zeroclaw-labs/zeroclaw/issues/7461)** — Windows/macOS CI matrix (open since 2026-06-10, `in-progress`, 5 comments). Cross-platform regressions remain unguarded until this lands.
3. **[#11322](https://github.com/zeroclaw-labs/zeroclaw/pull/11322) / [#11320](https://github.com/zeroclaw-labs/zeroclaw/pull/11320)** — plugin webhook RPC (open since 2026-10-01, `size:XL`, stacked, `needs-author-action`). The dependency chain (#11319→#11339→#11165→#11186→#11320→#11322) is a maintenance burden; a maintainer review/merge cadence would unblock a large feature cluster.
4. **[#11265](https://github.com/zeroclaw-labs/zeroclaw/pull/11265) / [#11264](https://github.com/zeroclaw-labs/zeroclaw/pull/11264) / [#11313](https://github.com/zeroclaw-labs/zeroclaw/pull/11313)** — password lifecycle/authorization publication stack (open since Sept 30–Oct 1, `risk:high`, stacked). Identity-access is a strategic area; aging XL PRs here risk bit-rot.
5. **[#11301](https://github.com/zeroclaw-labs/zeroclaw/pull/11301)** — agent rename committed-rename recovery across CLI/gateway/RPC (`priority:p1`, `size:XL`, open since 2026-10-01). Addresses state stranding on failed rename — high user impact if unresolved.

---

### Health Assessment

**Overall: Healthy and highly active, with review-capacity risk.** Security and identity work is well-organized via trackers and stacked PRs, and bugs are being fixed promptly (#10501). However, the ratio of open (43) to closed (7) PRs, combined with multiple `XL`/`needs-author-action` PRs aging 5–6 weeks, suggests **bottlenecked maintainer review**. The two new S2 bugs (#12289, #12288) are recent and unfixed — they should be triaged ahead of the v0.9.0 gateway feature work to avoid shipping authorization gaps in the release.

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar).*