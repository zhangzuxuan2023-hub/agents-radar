# OpenClaw Ecosystem Digest 2026-11-05

> Issues: 387 | PRs: 500 | Projects covered: 5 | Generated: 2026-11-04 23:22 UTC

- [OpenClaw](https://github.com/openclaw/openclaw)
- [Hermes Agent](https://github.com/nousresearch/hermes-agent)
- [IronClaw](https://github.com/nearai/ironclaw)
- [QwenPaw](https://github.com/agentscope-ai/QwenPaw)
- [ZeroClaw](https://github.com/zeroclaw-labs/zeroclaw)

---

## OpenClaw Deep Dive

# OpenClaw Project Digest — 2026-11-05

---

## 1. Today's Overview

OpenClaw shows very high maintainer and contributor throughput: **387 issues** and **500 PRs** were updated in the last 24 hours, with **194 PRs merged/closed** and **1 new beta release** (v2026.11.5-beta.1). The headline feature is the introduction of **"Knowledge Scribes"** — first-class knowledge capture workflows built across agents, sessions, and the Control UI — plus a new **PostgreSQL-backed state plane**, indicating a strategic move toward enterprise-grade persistence. However, the issue tracker is dominated by **P0/P1 stability problems** (gateway event-loop blocking, Windows service unreliability, heap/RSS runaway, prompt-cache regression), several of which have persisted for 6–9 weeks without fixes. The project is clearly in a high-velocity development phase, but the ratio of open (226) to closed (161) active issues and the volume of `clawsweeper:no-new-fix-pr` tags suggest maintenance debt is accumulating faster than it is being cleared.

**Activity assessment: Very high (release week + heavy triage volume).**

---

## 2. Releases

### v2026.11.5-beta.1 — `openclaw 2026.11.5-beta.1`

**Highlights:**

- **Knowledge Scribes** — Adds first-class knowledge capture and reusable "scribe" workflows spanning agents, sessions, and the Control UI. Delivered across six PRs (#185583, #185637, #185974, #186270, #186455, #187659), credited to @jalehman. This is the flagship feature of the release and signals OpenClaw's push toward durable, reusable agent memory artifacts rather than ephemeral session context.
- **PostgreSQL data plane** — Adds a PostgreSQL-backed state backend. *(Release notes truncated in provided data; full migration/breaking-change details and any required config/state-migration steps are not available in this dataset and should be verified against the full release notes before upgrading.)*

**Notes for operators:**
- This is a **beta** release; production users on 2026.10.x should watch for follow-up 2026.11.x fixes, especially given the number of open state-migration and plugin re-materialization bugs (e.g., #161921, #162585) that historically accompanied minor upgrades.
- No explicit breaking-change list was provided in the source data; **treat the PostgreSQL backend and plugin state migration as upgrade-risk areas** and review #161921 (`state-migrated-no-rollback`) before rolling out.

---

## 3. Project Progress

**Merged/closed PRs (194 total; notable samples from the 30-PR window):**

- **iOS/Control UI polish cluster** — #158051 (Cloudflare Access embedded Dashboard on iOS, closed), #149321 (Settings layout beside Ask OpenClaw, closed), #144670 (plugin tab padding), #132196 (Sessions tab alignment) — a sustained UI-consistency cleanup by @Patrick-Erichsen and others.
- **Web UI robustness** — #149581/#149169 (Markdown table scrolling performance), #149981 (IndexedDB-unavailable recovery in Control UI), #168524 (single Lit runtime in Vitest — test hygiene reducing flaky CI).
- **CI/dependency hygiene** — #174510 (vitest/vite CVE bumps, closed), #193046 (CLI fixture isolation from Workshop migration), plus several `fix(ci)` isolation PRs from @vincentkoc.
- **Dependency/export cleanup** — #180693, #179811 (unused Control UI exports from scheduled CI reports).

**Notable in-flight feature work (open PRs):**
- **GitHub integration → plugin migration** — #192349 (Plugin SDK host API extension) and #192350 (GitHub repository integration moved to a bundled plugin). This is a significant architectural refactor; #192350 depends on #192349 merging first.
- **Crabbox / guest environments** — #193047 ("Gateway-qualified guest Crabbox environments") and #193051 (security: stop setup after provisioning authority revoked).
- **Boat CI routing** — #193038 (route eligible Linux CI jobs through Boat).
- **QA-lab contracts** — #193020 (separate runtime parity data contracts).

**Assessment:** Feature velocity is strong in platform/plugin architecture (GitHub plugin migration, plugin SDK), while a large share of merged work is UI polish and CI hygiene — typical for a project stabilizing before a stable 2026.11 release.

---

## 4. Community Hot Topics

Ranked by comment activity:

| Rank | Item | Comments | Signal |
|---|---|---|---|
| 1 | **[#160959](https://github.com/openclaw/openclaw/issues/160959)** — Gateway blocks for minutes capturing large external plugins (P0, crash-loop, regression since 2026.9.6) | 70 | 🔥 Highest-engagement issue; plugin-capture regression has major operational impact and remains unfixed. |
| 2 | **[#177656](https://github.com/openclaw/openclaw/issues/177656)** — Native host exec returns shared-state integrity-admission error on 2026.10.5 (P1) | 28 | Shared-state/admission failures on stable releases. |
| 3 | **[#170956](https://github.com/openclaw/openclaw/issues/170956)** — Windows slow startup, unclean plugin shutdown, schtasks permission error (P0) | 28 | Windows reliability cluster; recurring theme. |
| 4 | **[#120385](https://github.com/openclaw/openclaw/issues/120385)** — Code Mode tool catalog incomplete for cron/memory-flush turns (P2, author-corrected) | 25 | Long-running issue with revised root-cause analysis; MCP tool visibility gap. |
| 5 | **[#150635](https://github.com/openclaw/openclaw/issues/150635)** — short-term recall eviction starves dreaming deep phase (P2) | 24 | Memory/recall subsystem correctness. |
| 6 | **[#166634](https://github.com/openclaw/openclaw/issues/166634)** — Late reclamation checkpoint failure leaves agent unavailable after crash recovery | 23 | Crash-recovery durability. |

**Underlying needs analysis:**
- **Plugin/session state integrity is the dominant theme** — #160959, #177656, #166634, #161921, #143640, and #190307 all point at the same architectural pressure: the shared-state / plugin-capture / admission layers are fragile under real-world plugin sizes and crash recovery. Users are running production workloads with large plugin trees, and the startup capture path (#144252) was a meaningful regression.
- **Windows and macOS service lifecycle** (#170956, #169844, #191944, #163139) — a persistent cross-platform service-management problem; users cannot reliably restart or update.
- **Cost/efficiency pain** (#161208, #182891, #178373, #161277) — prompt-cache misses and per-turn token overhead are leading to real dollar costs (one reporter: $0.40–0.95 per heartbeat). This is a growing community concern.

---

## 5. Bugs & Stability

**P0 / Critical (release-blocking):**

1. **[#160959](https://github.com/openclaw/openclaw/issues/160959)** — Gateway event loop blocks for minutes on large external plugin capture (2026.9.6 regression). **No fix PR.** Highest severity; affects startup and runtime republication. *70 comments, still open after ~5 weeks.*
2. **[#170956](https://github.com/openclaw/openclaw/issues/170956)** — Windows managed-service reliability (schtasks, unclean shutdown, slow startup). **No fix PR.**
3. **[#161921](https://github.com/openclaw/openclaw/issues/161921)** — `state-migrated-no-rollback` deadlock between `doctor --fix` and `plugins update`. **No fix PR.** Blocks upgrades.
4. **[#169844](https://github.com/openclaw/openclaw/issues/169844)** — Control UI Gateway update hangs at "Activating" on Windows, leaves Scheduled Task disabled. **No fix PR.**

**P1 / High severity:**

5. **[#177656](https://github.com/openclaw/openclaw/issues/177656)** — Native host exec integrity-admission error on 2026.10.5.
6. **[#154812](https://github.com/openclaw/openclaw/issues/154812)** — Runaway RSS outside V8 heap → host OOM and shutdown timeout.
7. **[#185578](https://github.com/openclaw/openclaw/issues/185578)** — Heap floor ratchets up (~25 MiB/h) until V8 cap aborts. **No fix PR.**
8. **[#148789](https://github.com/openclaw/openclaw/issues/148789)** — Model fallback chain exhausts all candidates on harness-level session failure, misattributes error.
9. **[#190307](https://github.com/openclaw/openclaw/issues/190307)** — Gateway restart leaves agents permanently "degraded" (admission timeout). **Related fix PR: [#193049](https://github.com/openclaw/openclaw/pull/193049)** (open, ready for maintainer look).
10. **[#192141](https://github.com/openclaw/openclaw/issues/192141)** — Interrupted-turn continuation drops original user message text.
11. **[#191401](https://github.com/openclaw/openclaw/issues/191401)** — Signal teardown hangs forever on unfinished receive (fix-shape-clear, queueable).

**Regressions / cross-cutting:**

- **[#182891](https://github.com/openclaw/openclaw/issues/182891)** — Prompt cache never reused (`cache read dropped N -> 0`) — cost regression.
- **[#185656](https://github.com/openclaw/openclaw/issues/185656) (CLOSED)** — zstd transcript archive exceeds Node string limit; regression of #170847 persisted into 2026.10.5.
- **[#162585](https://github.com/openclaw/openclaw/issues/162585)** — Windows plugin source-capture re-materializes plugin into its own `node_modules`; large npm trees explode linearly (290 MB → 612 MB/24 min). No fix.

**Stability summary:** Of the top ~20 severity-tagged issues, **only two have associated open fix PRs** (#190307→#193049; #191401 flagged queueable). The remainder carry `clawsweeper:no-new-fix-pr`, meaning the automated triage found no candidate fix — a meaningful risk signal for the 2026.11 stable cut. The release note for v2026.11.5-beta.1 does **not** enumerate fixes for the P0 cluster, so operators should not assume they are resolved.

---

## 6. Feature Requests & Roadmap Signals

**Most likely to land in 2026.11.x (near-term):**

- **Claude web_search provider** — [#192632](https://github.com/openclaw/openclaw/issues/192632) (filed 2026-11-04, P3, 9 comments already). Clear request for parity with Gemini/Perplexity providers; small surface area. **High likelihood.**
- **Opt-in reaction-triggered agent turns** — [#17840](https://github.com/openclaw/openclaw/issues/17840) (since Feb 2026, 8 comments, +1 👍). Long-standing enhancement; plausible as an opt-in flag.
- **Telegram `sendDice` message action** — [#137110](https://github.com/openclaw/openclaw/issues/137110) (linked PR open). **Likely soon.**
- **Control UI: browse/fork prior channel-chat segments after `/new`** — [#158800](https://github.com/openclaw/openclaw/issues/158800). Fits the Knowledge Scribes direction announced in this release.

**Medium-term / needs product decision:**

- **`skills.workshop.allowSandboxedAgents`** — [#91676](https://github.com/openclaw/openclaw/issues/91676) (security review needed, 2 👍). Sandbox policy relaxation requires maintainer security sign-off.
- **Active Memory escalation provider plugin hook** — [#151178](https://github.com/openclaw/openclaw/issues/151178) (linked PR open).
- **Plugin hook for post-tool-result content injection** — [#164355](https://github.com/openclaw/openclaw/issues/164355). Extensibility gap; relevant to the plugin-SDK extension work in #192349.
- **macOS/Talk automation admin turns** — [#139586](https://github.com/openclaw/openclaw/issues/139586).
- **Native contributor admission/lifecycle contract** — [#158921](https://github.com/openclaw/openclaw/issues/158921) (closed stale).

**Prediction:** The 2026.11.x line will likely prioritize **provider parity (Claude search), small channel actions (Telegram dice, Matrix media batching #161505)** and **plugin-SDK extensibility**, while larger features (sandboxed skill workshop, memory escalation providers) wait for security/product review. The **GitHub-integration-as-plugin** refactor (#192349/#192350) is the most consequential roadmap item in flight and, if merged, will materially reshape the extension model.

---

## 7. User Feedback Summary

**Pain points (dissatisfaction):**

- **Upgrades and restarts are unreliable, especially on Windows and macOS** — schtasks permission errors, hangs at "Activating," `ServiceInspectionError` on macOS (#170956, #169844, #191944). Multiple users report needing many retries ("pas na veel proberen").
- **Cost inefficiency** — prompt-cache misses on heartbeats and per-turn envelope rewrites (#161208, #182891); per-turn `requester_profile` boilerplate (#161277); no run-level token budget and opt-in loop detection (#178373). Users are paying real money for avoidable tokens.
- **Phantom/duplicated messages and context leakage** — #151962 (phantom user prompts from internal runtime strings), #149153 (final reply rendered twice), #165041 (session-resume injection visible in UI). These erode trust in transcript fidelity.
- **Silent failures** — #148308 (Tailscale claim silently lost), #150822 (advertised tools without executor), #160610 (Discord "runtime degraded" despite healthy bots).
- **Memory subsystem correctness** — #150635 (recall eviction never promotes), #176187 (KNN deadline overruns), #143640 (memory publish blocks agent DB writes).

**Positive signals / use cases:**
- Strong demand for **multi-agent, multi-channel deployments** (Telegram, Discord, WhatsApp, Signal, Matrix, WeChat bridges all represented in comments), typically with 3–50 agents and long-lived sessions (in one case a 13-day, ~310k-token session).
- Users are pushing **production-scale** configurations (35 agents, 284 MB SQLite stores, 18k vectors), which explains the recurring large-state bugs — the project is being used well beyond hobby scale.
- Community contributors are actively filing well-structured, tagged reports (note the pervasive `clawsweeper` automated triage metadata), suggesting an engaged and technically sophisticated user base.

**Net sentiment:** Frustration concentrated in **operational reliability and cost**, balanced by enthusiasm for new capabilities (Knowledge Scribes, plugin ecosystem). Users clearly like the product but are hitting maturity-scale operational issues.

---

## 8. Backlog Watch

Long-unanswered or under-attended important items needing maintainer attention:

| Issue | Age | Concern |
|---|---|---|
| **[#91676](https://github.com/openclaw/openclaw/issues/91676)** — `skills.workshop.allowSandboxedAgents` opt-in | ~5 months (since 2026-06-09) | Awaiting security review; blocks a legitimate sandbox-extension use case. |
| **[#17840](https://github.com/openclaw/openclaw/issues/17840)** — reaction-triggered agent turns | ~9 months (since 2026-02-16) | Long-standing enhancement with clear use cases (emoji polling). |
| **[#160959](https://github.com/openclaw/openclaw/issues/160959)** — P0 gateway event-loop blocking | ~5 weeks, 70 comments | **Highest-priority unfixed P0.** Regression introduced in 2026.9.6; still no fix PR. |
| **[#161921](https://github.com/openclaw/openclaw/issues/161921)** — state-migrated-no-rollback deadlock | ~5 weeks, P0 | Blocks plugin updates and upgrades; mutually-blocking commands are a dangerous failure mode. |
| **[#162585](https://github.com/openclaw/openclaw/issues/162585)** — Windows plugin re-materialization explosion | ~5 weeks | Disk/time blowup on large npm trees; no fix. |
| **[#154812](https://github.com/openclaw/openclaw/issues/154812)** — RSS runaway → OOM | ~7 weeks, P1 | Host-level outage risk; no fix. |
| **[#164355](https://github.com/openclaw/openclaw/issues/164355)** — post-tool-result content hook gap | ~1 month | Architectural extensibility gap; relates to plugin-SDK work. |
| **[#150822](https://github.com/openclaw/openclaw/issues/150822)** — Codex children advertise tools without executors (security review) | ~7 weeks | Needs-live-repro; potential security implications. |

**PRs needing maintainer look (ready or near-ready):**

- **[#193049](https://github.com/openclaw/openclaw/pull/193049)** — fix: config reload during startup admission (P1, platinum hermit, ready for maintainer look). *Directly addresses #190307/#161921.*
- **[#191914](https://github.com/openclaw/openclaw/pull/191914)** — fix: child-process spawn fails on glibc < 2.31 (P1, compatibility risk) — important for RHEL/CentOS/Rocky 8 users.
- **[#191918](https://github.com/openclaw/openclaw/pull/191918)** — clarify Codex executable launch failures (small, ready).
- **[#191517](https://github.com/openclaw/openclaw/pull/191517)** — iOS native playback recovery (draft, awaiting native acceptance).
- **[#192350](https://github.com/openclaw/openclaw/pull/192350) / [#192349](https://github.com/openclaw/openclaw/pull/192349)** — GitHub-integration plugin migration (XL, security-review-required, dependency-ordered).

**Recommendation:** The P0 cluster (#160959, #170956, #161921, #169844) represents the clearest risk to the 2026.11 stable release. Given 194 PRs merged in 24h, maintainer bandwidth is demonstrably high — but the concentration of `no-new-fix-pr` tags on the highest-severity issues suggests these need **dedicated deep-dive owners** rather than routine triage. Prioritizing the two ready fix PRs (#193049, #191914) and assigning an owner to the plugin-capture/event-loop regression (#160959) would materially improve release confidence.

---

*Data source: github.com/openclaw/openclaw, snapshot 2026-11-05. Comment/reaction counts as provided; PR comment counts were unavailable in the source data for the sampled window.*

---

## Cross-Ecosystem Comparison

# Cross-Project Comparison Report — AI Agent & Personal Assistant Open-Source Ecosystem
**Snapshot date: 2026-11-05** · Projects: OpenClaw, Hermes Agent, IronClaw, QwenPaw, ZeroClaw

---

## 1. Ecosystem Overview

The open-source personal AI assistant landscape is consolidating around a common architecture — multi-channel gateways (Telegram/Discord/Slack/Matrix), pluggable tool/skill and memory backends, delegated multi-agent execution, and a desktop/Control-UI layer — while individual projects diverge sharply in maturity and focus. Three distinct maturity bands are visible: **OpenClaw** at hyperscale (11k+ open issues, ~1,000 PR events/day) with enterprise ambitions (PostgreSQL state plane, plugin-SDK refactors); a **stabilization tier** (Hermes, QwenPaw, ZeroClaw) working through migration- or release-induced regressions ahead of consolidation releases; and **IronClaw** in low-activity maintenance mode. Across all projects, the dominant community complaint has shifted from "missing features" to **operational reliability, security isolation, and cost efficiency** — a classic sign the category is moving from enthusiast adoption (3–50 agent homelabs, large plugin trees, 18-day gateway uptimes) into production use. Plugin ecosystems are the emerging competitive moat: hackathon-driven catalog growth (Hermes), memory-plugin backends (QwenPaw, OpenClaw Knowledge Scribes), and registry/marketplace requests (ZeroClaw) all point the same direction.

---

## 2. Activity Comparison

| Project | Issues updated (24h) | PRs updated (24h) | Merged/closed PRs | Release status | Health score |
|---|---|---|---|---|---|
| **OpenClaw** | 387 (226 open / 161 closed active) | 500 | 194 | **v2026.11.5-beta.1** shipped (Knowledge Scribes, PostgreSQL backend) | 🟡 **Yellow** — very high velocity, but P0 cluster (4 issues) unfixed 5–9 wks; `no-new-fix-pr` tags on top-severity items |
| **Hermes Agent** | 50 | 50 | 11 | None; v0.22.5 / v0.21.6 current | 🟡🟢 **Yellow-green** — strong contributor energy (plugin hackathon), >1,000 open issues, P1 silent-failure class open |
| **IronClaw** | 0 | 1 | 0 | None | ⚪ **Quiet/stable** — maintenance mode; 1 Dependabot PR (zip 8→9 major bump); possible data-snapshot partiality |
| **QwenPaw** | 10 | 50 | 13 (3 issues closed) | None; iterating against **2.3.0b2** beta | 🟢 **Green** — good but noisy; 3 High-sev 2.3.0b2 regressions, 2 with fix PRs in flight |
| **ZeroClaw** | 16 | 50 | 4 | None; **v0.9.0 release gate** assembling | 🟢 **Green (with risk)** — disciplined security hardening; 46 open PRs = review-bandwidth bottleneck |

---

## 3. OpenClaw's Position

**Scale and reference status.** OpenClaw operates roughly **an order of magnitude above its peers** on throughput (387 issues / 500 PRs updated daily vs. 10–50 for the others; 194 merges/day). It is effectively the reference implementation: 11k+ open issues signals both massive adoption and the burden of being the ecosystem's center of gravity.

**Advantages over peers:**
- **Architectural ambition ahead of the field** — the only project shipping an enterprise persistence layer (PostgreSQL state plane) and a formal Plugin SDK host-API migration (GitHub integration → bundled plugin, #192349/#192350). Peers are still on file-based/plugin-catalog models.
- **Flagship differentiation** — "Knowledge Scribes" (first-class reusable knowledge capture) is a product-level feature no peer matches; QwenPaw's memory-plugin work and Hermes' `auxiliary.skills_hub` confusion show the same need addressed less coherently.
- **Contribution infrastructure** — automated triage (`clawsweeper` metadata), dense CI hygiene, and dedicated deep-dive issue culture attract technically sophisticated production users (35-agent, 284 MB SQLite deployments).

**Divergences in technical approach:**
- Heavier, monolithic core with plugin capture at gateway startup — the direct cause of its worst P0 (#160959: event-loop blocking on large external plugin capture), a fragility pattern the lighter peers avoid.
- Release cadence via **beta-first minor lines** (2026.11.5-beta.1) with state-migration risk (#161921 `state-migrated-no-rollback` blocks upgrades) — an enterprise-tradeoff peers don't yet face.

**Community comparison.** OpenClaw's user base is the most production-scaled (largest plugin trees, longest sessions — one 13-day/310k-token session) and most demanding on cost (prompt-cache regression $0.40–0.95/heartbeat). Hermes has the **highest per-capita contributor energy** (hackathon plugin wave, `od -c`-quality reports); ZeroClaw the most security-literate reporters; QwenPaw the strongest **first-time-contributor pipeline**; IronClaw essentially silent.

**Caveat:** OpenClaw's velocity masks maintenance debt — of its top ~20 severity issues, only **two have open fix PRs**. Being the reference implementation is currently the project's greatest asset *and* its largest release risk (2026.11 stable).

---

## 4. Shared Technical Focus Areas

Five requirements recur across ≥3 projects, indicating genuine category-wide pressure:

| Need | Projects | Concrete signal |
|---|---|---|
| **Session/state integrity & crash recovery** | OpenClaw, Hermes, ZeroClaw, QwenPaw | OpenClaw #160959/#161921/#166634 (shared-state admission, no-rollback deadlock); Hermes #145807 (corrupted `active_sessions.json` blocks all sessions), #148762 (dual-profile session IDs); ZeroClaw RFC #10526 (append-only event history + deterministic replay — the architectural answer) |
| **Principal/tenant isolation for delegated tools** | ZeroClaw, OpenClaw, Hermes | ZeroClaw #11198 (memory tools lose principal scope), #11220 (SOP needs admin authority), #12227 (model-written "approved" value); OpenClaw #150822 (tools advertised without executors); Hermes delegated-subagent observability #149110 |
| **Silent-failure elimination** | Hermes, QwenPaw, OpenClaw | Hermes #149085 (commands dropped silently), #149107 (fallback model swapped unnoticed), #130272 (approval timeout mislabeled "denied"); QwenPaw #8278/#8579/#8580 (Markdown downgrade, generic "Internal error", tag 503); OpenClaw #148308/#150822 |
| **Cost & context efficiency** | OpenClaw, Hermes, QwenPaw | OpenClaw prompt-cache miss #182891, per-turn token overhead #161208/#161277, no token budget #178373; Hermes compression bugs #107628; QwenPaw cron session bounding #8557, media bounds #8034 |
| **Pluggable memory backends** | QwenPaw, OpenClaw, Hermes | QwenPaw OpenViking plugin #7613 + Piccolo runtime #8573; OpenClaw Knowledge Scribes + memory-flush recall issues #150635; Hermes `skills_hub`/plugin-settings UX |
| **Upgrade/migration safety rails** | Hermes, OpenClaw, ZeroClaw | Hermes unified-gateway fallout tracker #143547 (47 comments), #143150 storage routes; OpenClaw #161921 upgrade deadlock; ZeroClaw #11579 schema-version stamping data loss |

---

## 5. Differentiation Analysis

| Dimension | OpenClaw | Hermes Agent | QwenPaw | ZeroClaw | IronClaw |
|---|---|---|---|---|---|
| **Center of gravity** | Core platform: agents, sessions, Control UI, Plugin SDK | Unified multiplex gateway + Desktop UX (simplified mode) | Multi-channel breadth (DingTalk/Feishu/WeChat/Matrix E2EE) + console plugin theming | **Security-first**: principal scoping, SOP authority, OIDC, deterministic replay | Minimal signal |
| **Target user** | Production-scale operators (10–50+ agents, large plugin trees) | Multi-profile power users, hackathon/plugin builders | Chinese-ecosystem and multi-workspace channel users; first-time contributors | Self-hosted, security-conscious, multi-tenant deployments | Not observable |
| **Architecture signal** | Monolithic core + PostgreSQL state plane + beta-first release line | Multiplex profiles/gateways (post-migration, session-lineage fencing PRs) | Beta-iterated 2.3.0b2; per-run cron sessions; memory-ecosystem theme | Rust; RFC-driven append-only event ledger; review-gated security chains | Rust (zip crate); dormant |
| **Distinctive strength** | Knowledge Scribes; plugin-SDK host API | Plugin catalog velocity + in-terminal image preview | Channel parity + E2EE; contributor onboarding | Isolation guarantees (3 P0/S0 security items closed in 24h) | None surfaced 24h |
| **Known blind spot** | P0 unfixed backlog; Windows/macOS service lifecycle | Migration long tail; silent failures | 2.3.0b2 regressions (TOPMOST, provider-disable) | Long-standing Tokio stack overflow #9572 (3+ months) | Feedback channel silence |

**Key divergence:** OpenClaw and Hermes compete on **platform extensibility** (agents, plugins, desktop UX); QwenPaw competes on **channel coverage**; ZeroClaw competes on **trust and auditability** — a positioning that is strategically complementary rather than head-to-head, and which the ecosystem will need as deployments become multi-tenant.

---

## 6. Community Momentum & Maturity

**Tier 1 — Hyperscale iteration (OpenClaw).** Release week + 194 merges/day. Rapidly iterating on platform architecture (GitHub-as-plugin refactor, PostgreSQL) while stabilizing UI/CI. Maturity risk: maintenance debt accumulating faster than clearance; needs dedicated deep-dive owners for the P0 cluster before the 2026.11 stable cut.

**Tier 2 — Stabilizing through migration/release gates (Hermes, QwenPaw, ZeroClaw).**
- **Hermes** is post-migration (unified gateway, merged Oct 14): the next release carries outsized expectations (consolidation + migration guidance + session-ownership fencing PRs #149055/#149132). Ecosystem momentum is the best in the set.
- **QwenPaw** is in a clean beta-stabilization window (2.3.0b2): most High/Medium regressions are beta-specific, two Windows Hub fixes already in flight, healthy first-time-contributor flow. Well-positioned for a green 2.3.0 final.
- **ZeroClaw** is release-gating v0.9.0 with disciplined security closures (P0/P1 data-loss items fixed inside 24h) — a strong health signal — but 46 open PRs with stacked, review-gated dependencies make **maintainer review bandwidth the throughput ceiling**. Highest structural risk: the 3-month-old #9572 with no fix.

**Tier 3 — Maintenance mode (IronClaw).** Zero issues, one Dependabot major-version PR. No regression signal, but no forward momentum either; the sparse dataset may be partial, so engagement conclusions are low-confidence.

**Headline metric:** OpenClaw's 500 PR updates vs. Hermes/QwenPaw/ZeroClaw's ~50 each confirms OpenClaw as the reference node, while the Tier 2 trio collectively represents the ecosystem's **active stabilization and innovation frontier** (hackathon plugins, E2EE, security hardening).

---

## 7. Trend Signals

**1. Reliability is the new feature war.** Every active project's top-engagement issues are operational: upgrade deadlocks (OpenClaw #161921), corrupted state files blocking all sessions (Hermes #145807), provider-disable generic errors (QwenPaw #8579), principal leaks (ZeroClaw #11198). *Value for developers:* ship **migration/rollback stories, state-schema validation, and actionable error surfaces** as first-class deliverables — silent degradation (Hermes #149085/#149107, QwenPaw #8278/#8580) is penalized hardest because it breaks trust in agent infrastructure.

**2. Trust boundaries are moving into the model's path.** ZeroClaw's findings that shell approval can be driven by model-written values (#12227) and delegated tools can escape principal scope (#11198/#12232), plus Hermes' approval-timeout mislabeled as denial (#130272) and OpenClaw's tools-advertised-without-executor (#150822), define a new class of **agent-native security bugs** — neither traditional appsec nor prompt-injection, but "authority confusion at the tool-call boundary." *Value for developers:* build principal/authority propagation into the tool-execution contract from day one; ZeroClaw's append-only event ledger with deterministic replay (RFC #10526) is the strongest emerging pattern for auditability.

**3. Cost observability is an unmet product requirement.** Prompt-cache regressions and per-turn overhead are producing real dollar complaints (OpenClaw $0.40–0.95/heartbeat). *Value for developers:* token/cost budgets, cache-hit telemetry, and loop detection are differentiating platform features, not optimizations.

**4. Plugins are the ecosystem moat — and the next fragility source.** Hackathon catalogs (Hermes), memory backends (QwenPaw/OpenClaw), registry coexistence (ZeroClaw #12109→#12216), and the plugin-SDK host-API migration (OpenClaw #192349/#192350) show plugin ecosystems are where lock-in and lock-out will be decided. Simultaneously, OpenClaw's #160959 (plugin capture blocking the event loop) and #162585 (Windows re-materialization blowing up disk) warn that **plugin lifecycle isolation — capture, materialization, sandboxing — is the critical subsystem to design for scale**, not an afterthought.

**5. Editable, reversible, replayable sessions.** QwenPaw #7997 (message retraction/rollback, highest engagement), OpenClaw Knowledge Scribes + forked channel segments (#158800), Hermes session-lineage fencing (#148762), and ZeroClaw's replay RFC converge on one requirement: **users treat agent conversations as workspaces, not append-only logs.** *Value for developers:* snapshot/truncate/rollback primitives over session state will be table stakes for the next platform generation.

**6. Windows and constrained environments are a hidden adoption tax.** Persistent Windows reliability clusters (OpenClaw #170956/#162585, QwenPaw #8565, ZeroClaw #12119/#12218), sandboxed NAS (QwenPaw fnOS #8515), and Termux (Hermes #149130) indicate significant real-world demand outside Unix desktop/server. Cross-platform service management (schtasks, launchd/TCC, AppContainer) remains systematically under-resourced across the board — an open opportunity for any project that solves it first.

---

*Methodology note: health scores are synthesized from release cadence, fix-PR coverage of top-severity issues, triage latency, and contributor breadth. IronClaw conclusions are low-confidence due to an unusually sparse snapshot (1 PR, 0 issues, undefined comment counts).*

---

## Peer Project Reports

<details>
<summary><strong>Hermes Agent</strong> — <a href="https://github.com/nousresearch/hermes-agent">nousresearch/hermes-agent</a></summary>

# Hermes Agent — Project Digest
**Date: 2026-11-05** · Source: github.com/NousResearch/hermes-agent

---

## 1. Today's Overview

Hermes Agent remains in a high-activity, post-migration stabilization phase. Activity is strong and sustained: 50 issues and 50 PRs were touched in the last 24h, with 4 issues closed and 11 PRs merged/closed, though no new releases shipped. The dominant theme continues to be fallout from the **unified gateway migration (#106742, merged 14 Oct)** — the top issue of the day (#143547, 47 comments) is explicitly a post-merge problem tracker spanning gateway, cron, plugins, Desktop, Discord, Telegram, auth, sessions, and profiles. The issue tracker has crossed **1,000+ open issues** (now closed meta-issue #7335), and there is visible concern about triage load compared to peers like OpenClaw (11k+ open issues). Community energy is high — today alone brought **8+ plugin-catalog PRs** (a hackathon-driven wave), indicating a healthy extensibility ecosystem even as core stability work continues.

---

## 2. Releases

**None.** No new releases in the last 24h. Note: recent bug reports reference **v0.22.5** and **v0.21.6** as current shipping versions, and at least one fix (#148759) is specifically about degraded update-check behavior on older git versions — worth watching for the next release.

---

## 3. Project Progress

**Merged/closed PRs today:** 11 (merged/closed combined). Notable closures in the issue tracker:

- **#6675 [CLOSED]** — In-terminal image preview for screenshots and tool results. A long-standing (since April) CLI UX request now resolved.
- **#40703 / #142403 [CLOSED]** — `auxiliary.skills_hub` config confusion resolved: the setting was documented and exposed in pickers but no code path read it. Closed as stale/reserved documentation cleanup.

**Active feature advancement (open PRs under review):**
- **#148153** — Desktop: opt-in "Simplified agent mode" sidebar with nested chats (pinned chats → gateways → agents → chats). Stacked with **#148247** (agent settings overview).
- **#149145** — Agent: context boundary now resets the tool-loop guard's no-progress evidence (correctness fix for post-compaction loops).
- **#149141** — Voice: over-limit live-voice window now keeps *newest* turns (was keeping head).
- **#149140** — Skills: preserve LF line endings on Windows writes (fixes spurious full-file diffs).
- **#149099** — Chat Completions wire normalization for empty tool-call assistant content.

**Ecosystem growth:** A striking burst of **plugin-catalog PRs** today: `undo-effects` (#149148), `CaMeL Guard 1.1.0` (#149147, companion to long-running #3987), `agentmail-frontdoor`/`odoo-prod-guard`/`fleet-memory` (#149142), `voice-gate 1.3.5` (#149072), `guardrails` hard-spend-limits plugin (#149133), plus a plugin-settings UX fix (#149134). This strongly suggests an active **Plugin Hackathon** (explicitly referenced in #149148) driving community contributions.

---

## 4. Community Hot Topics

| Item | Comments | Link | Underlying need |
|---|---|---|---|
| **#143547** Unified gateway post-merge problems | **47** 👍1 | [Link](https://github.com/NousResearch/hermes-agent/issues/143547) | The single biggest coordination thread. Cross-cutting failures across gateway, cron, plugins, Desktop, Discord, Telegram, auth, sessions, profiles. Users need a **migration/rollback story** and a consolidated fix tracker. |
| **#7335** More than 1000 open issues (CLOSED) | 22 | [Link](https://github.com/NousResearch/hermes-agent/issues/7335) | Community anxiety about **triage scale** and maintainer bandwidth; closed but signals process concerns. |
| **#143150** Desktop `storage_unavailable` on session resume after multiplex migration | 7 | [Link](https://github.com/NousResearch/hermes-agent/issues/143150) | Concrete migration breakage: standalone→multiplex profile migration leaves `state_meta` routes unmigrated. Users need **safe upgrade paths**. |
| **#138105** Multiplex cron ticker starves Discord heartbeat | 7 | [Link](https://github.com/NousResearch/hermes-agent/issues/138105) | Performance/scalability of the multiplex model: O(profiles) work every 60s. Needs caching/backoff. |
| **#142574** Secret redactor corrupts non-secret content | 5 | [Link](https://github.com/NousResearch/hermes-agent/issues/142574) | Trust/safety tension: over-aggressive redaction breaks tool results (`PASS=$VAR`, build stamps) and can leak `***` placeholders to disk. |

**Analysis:** The community conversation is dominated by **migration maturity**, not new features. The unified gateway is architecturally ambitious but has produced a long tail of edge-case regressions (storage routes, Discord heartbeat starvation, session lineage). Plugin PR volume shows the ecosystem is thriving *despite* core churn — a positive health signal.

---

## 5. Bugs & Stability

Ranked by severity/impact:

**P1-class (data integrity / silent failure):**
- **#145807** — `active_sessions.json` corrupted by a stray TLS `close_notify` record; **gateway refuses every new session** until manual file removal. High blast radius. No fix PR linked yet. [Link](https://github.com/NousResearch/hermes-agent/issues/145807)
- **#149085** — Terminal pre-execution guard **wedges silently, dropping commands** (61/day, 90 leaked threads on long-lived macOS gateway). Silent command loss is a correctness hazard. [Link](https://github.com/NousResearch/hermes-agent/issues/149085)
- **#107628** — Compression crashes with `TypeError: unhashable type: 'dict'` on non-scalar content-part `type`; session becomes **permanently uncompressible**. Long-standing (Sep 10). [Link](https://github.com/NousResearch/hermes-agent/issues/107628)

**P2-class (functional regressions):**
- **#143150** — Desktop `storage_unavailable` on resume post-multiplex migration. [Link](https://github.com/NousResearch/hermes-agent/issues/143150)
- **#148762** — Same session ID unended in two profile stores; Desktop/CLI resume races shorter lineage into view. **Fix PRs exist: #149055 and #149132.** [Issue](https://github.com/NousResearch/hermes-agent/issues/148762) · [PR #149055](https://github.com/NousResearch/hermes-agent/pull/149055) · [PR #149132](https://github.com/NousResearch/hermes-agent/pull/149132)
- **#138105** — Cron ticker starves Discord heartbeat. [Link](https://github.com/NousResearch/hermes-agent/issues/138105)
- **#142574** — Secret redactor corrupts non-secret content (intermittent, `***` reached disk twice). [Link](https://github.com/NousResearch/hermes-agent/issues/142574)
- **#130272** — Approval **timeout reported to agent as "User denied"** — dangerous mis-signal for agents handling sensitive commands. [Link](https://github.com/NousResearch/hermes-agent/issues/130272)
- **#148958** — Kanban worker gateway-owner stall exit counted as crash; two in a row block the card. [Link](https://github.com/NousResearch/hermes-agent/issues/148958)
- **#149107** — `_api_kwargs_have_image_parts` raises TypeError on JSON schema with a `"type"` property — **crash silently activates fallback model** (user-unaware model swap). [Link](https://github.com/NousResearch/hermes-agent/issues/149107)
- **#149139** — macOS Desktop file drops from `~/Downloads`/`~/Desktop` fail with `storage_unavailable` (TCC/EPERM since attaching to launchd gateway). **Regression.** [Link](https://github.com/NousResearch/hermes-agent/issues/149139)
- **#149057** — `context_compression_timeout` auto-reset strands long-lived bot chats (lineage walker misses `session_reset`). [Link](https://github.com/NousResearch/hermes-agent/issues/149057)
- **#148759** — `hermes --version` takes ~40s on git < 2.44 (stock macOS git 2.39 affected). [Link](https://github.com/NousResearch/hermes-agent/issues/148759)
- **#149150** — Slack `admission_conflict` error reply for already-answered message post-restart. [Link](https://github.com/NousResearch/hermes-agent/issues/149150)

**Fix PRs in flight:** #149145 (tool-loop guard reset), #149137 (kanban receipt timeout reconciliation, fixes #149126), #149120 (batch clarify after reconnect), #89978 (TUI turn state truthfulness), #149141 (voice window), #149140 (Windows LF), #149099 (wire normalization).

**Assessment:** Stability load is elevated and concentrated around the multiplex gateway and session-state layer. Encouragingly, most P2 items have either fix PRs open or are scoped. The **P1 silent-failure class** (#145807, #149085, #149107, #130272) deserves maintainer priority — silent wrongness is far costlier than loud crashes for agent infrastructure.

---

## 6. Feature Requests & Roadmap Signals

**Likely next version (active PRs near completion):**
- **Simplified agent mode** sidebar + agent settings overview (Desktop) — #148153, #148247. Polished UX stack likely to land together.
- **Provider route visibility** on model rows — #149144 (transparency for multi-route same-model setups).
- **Tool-loop guard correctness** — #149145 (robustness, likely merged quickly).
- **Voice context window fix** — #149141.

**Requested, not yet implemented:**
- **#149149** — Configurable Kanban review-rework round cap (`kanban.max_review_rounds`). Direct response to infinite `review_requested → changes_requested` loops; #90574 (no circuit breaker) corroborates. [Link](https://github.com/NousResearch/hermes-agent/issues/149149)
- **#149110** — Delegation observability: silent subagent deaths, no stall detection, weak live monitoring, no per-child metrics. Strong roadmap signal for **agent orchestration observability**. [Link](https://github.com/NousResearch/hermes-agent/issues/149110)
- **#149146** — Clarify terminal approval timeout semantics for revised preflight validation. [Link](https://github.com/NousResearch/hermes-agent/issues/149146)
- **#149143** — Kanban PR acceptance impossible on repos that can't configure required checks (every free-tier private repo). [Link](https://github.com/NousResearch/hermes-agent/issues/149143)

**Prediction:** Next release likely bundles Desktop simplified mode, the session-ownership fencing PRs (#149055/#149132), tool-loop/voice/Windows fixes, and possibly `kanban.max_review_rounds` given two corroborating issues. Delegation observability (#149110) is a strong candidate for the release after.

---

## 7. User Feedback Summary

**Pain points (dissatisfaction):**
- **Migration complexity.** Multiple users (Relogys, kyanite-factory, KahChunRSGX, joerghaller) report that moving to the multiplexed gateway model broke working setups — session resume, macOS file drops, profile migration. Users want *clearer upgrade paths and safety rails*.
- **Silent failures erode trust.** Commands dropped silently (#149085), fallback model activated without notice (#149107), redactor corrupting content (#142574), approval timeout mislabeled as denial (#130272). Repeated theme: *"I need to know when something went wrong."*
- **Triage scale.** #7335 reflects community concern that 1,000+ open issues is becoming unmanageable.
- **Long-lived deployments degraded.** #149085 (18-day uptime gateway, 90 leaked threads) and #149057 (stranded bot chats) show production users hitting resource leaks and state issues over time.

**Use cases observed:** production gateway-owned sessions with 5–10 concurrent delegated children (#149110), multi-profile installs, Matrix/Discord/Telegram/Slack bots, macOS desktop workflows, Termux/Android installs (#149130 — repo key download failing).

**Satisfaction signals:** Very high contributor energy — hackathon plugin PRs, detailed reproducible bug reports with `od -c` dumps and commit hashes, and users building production tooling (payment guardrails, CaMeL security guard, undo middleware). The community is invested; the ask is for **stability and predictability** in return.

---

## 8. Backlog Watch

Items needing maintainer attention:

| Item | Age | Why it matters |
|---|---|---|
| **#107628** Compression `TypeError` → permanently uncompressible sessions | ~8 weeks (Sep 10) | P2 crash with data-durability impact; #104840 only fixed 1 of 3 sites. Needs completion. [Link](https://github.com/NousResearch/hermes-agent/issues/107628) |
| **#130272** Approval timeout reported as "User denied" | ~5 weeks (Oct 1) | Safety-critical mis-signal; no fix PR visible. [Link](https://github.com/NousResearch/hermes-agent/issues/130272) |
| **#90574** Kanban review loop without circuit breaker | ~11 weeks (Aug 20) | Corroborated by today's #149149; a config cap is a small, high-value fix. [Link](https://github.com/NousResearch/hermes-agent/issues/90574) |
| **#3987** CaMeL Guard plugin PR | **~7 months (Mar 30)** | Long-lived security plugin PR, refreshed onto new base today (#149147 depends on it). Deserves a maintainer decision. [Link](https://github.com/NousResearch/hermes-agent/pull/3987) |
| **#89978** TUI turn-state truthfulness PR | ~11 weeks (Aug 19) | Fixes "dead worker pins session as working" — user-visible confusion; still open. [Link](https://github.com/NousResearch/hermes-agent/pull/89978) |
| **#7335** Issue-growth control (closed) | — | Closed without an apparent process change; recommend a public triage/labeling roadmap response. |
| **#149130** Termux install blocked (apt key download failure) | New | Platform coverage blocker; likely infrastructure/config fix on `hermes-assets.nousresearch.com`. [Link](https://github.com/NousResearch/hermes-agent/issues/149130) |

---

### Health Verdict
**Yellow-green.** Ecosystem momentum is excellent (plugin hackathon, dense high-quality reports, active fix PRs), and the team is closing items (image preview, skills_hub ambiguity). However, the unified-gateway migration's long tail — plus a cluster of **silent-failure P1 bugs** and an issue count past 1,000 — means the project's near-term health depends on shipping a consolidation release with clear migration guidance and prioritizing the silent-error class. No release in 24h is fine given this context, but the next one carries outsized expectations.

</details>

<details>
<summary><strong>IronClaw</strong> — <a href="https://github.com/nearai/ironclaw">nearai/ironclaw</a></summary>

# IronClaw Project Digest — 2026-11-05

## 1. Today's Overview

IronClaw is in a low-activity state today, with zero issues updated in the last 24 hours and no new releases. The only activity is a single open Dependabot pull request (#8168) bumping the Rust `zip` crate from 8.6.0 to 9.0.5, a major-version jump that warrants review. With no merged PRs, no closed issues, and no community discussion, today's data reflects maintenance-mode operations rather than active feature development. Overall project health appears stable but quiet—there are no signals of regressions or urgent bugs, but also no evidence of forward momentum in the last 24 hours.

## 2. Releases

No new releases in the reporting window.

## 3. Project Progress

No pull requests were merged or closed today, and no issues were resolved. The only movement is the opening/update of PR #8168 (dependency bump), which has not yet been reviewed or merged. No features advanced and no fixes landed in this 24-hour window.

## 4. Community Hot Topics

- **[PR #8168 — chore(deps): bump zip from 8.6.0 to 9.0.5](https://github.com/nearai/ironclaw/pull/8168)** (author: dependabot[bot]; opened 2026-11-04; 0 comments, 0 👍)
  - The sole active item. It is an automated dependency update to the `zip` crate, crossing a major version boundary (8.x → 9.x). The pR summary references upstream bug fixes in v9.0.5, but the comment count is unavailable ("undefined") in the provided data, so no community reaction can be assessed.
  - **Underlying need:** Routine dependency hygiene and security/maintenance currency. Because this spans a major version bump, maintainers should verify the changelog for breaking API changes before merging—automated bumps across majors are a common source of silent breakage.

No other issues or PRs exist in the current dataset, so there are no additional hot topics, reactions, or comment threads to analyze.

## 5. Bugs & Stability

No bugs, crashes, or regressions were reported in the last 24 hours. The single open PR is a dependency maintenance update rather than a bug fix. Note: the `zip` 8→9 major bump (PR #8168) is a potential stability risk if merged without CI verification, but it is not itself a reported bug. No fix PRs are associated with any reported defects, as none exist.

## 6. Feature Requests & Roadmap Signals

No user-requested features were filed or discussed today, so there are no roadmap signals from the issue tracker. The only forward-looking item is the dependency upgrade in PR #8168, which—if merged—would modernize the archive-handling dependency. No prediction about next-version features can be made from today's data alone.

## 7. User Feedback Summary

There is no user feedback to summarize for this window: zero issues, zero comments, and zero reactions on the sole PR. No pain points, use cases, or satisfaction signals were surfaced. This absence itself is a data point—either the project's user base is not filing issues at the moment, or feedback channels are quiet.

## 8. Backlog Watch

The provided dataset contains only one item, and it is recent (created 2026-11-04), so there are no long-unanswered issues or PRs to flag. **PR #8168** ([link](https://github.com/nearai/ironclaw/pull/8168)) is the only item requiring maintainer attention this cycle, primarily for review of the major-version dependency bump. No stale or neglected backlog items are visible in the current data.

---

**Data caveats:** The dataset reports 0 issues and only 1 PR overall, which is unusually sparse for a project at PR #8168. The "Comments: undefined" field on PR #8168 and the absence of any issue records suggest the data snapshot may be partial or filtered; conclusions about community engagement should be treated with caution until a fuller dataset is available.

</details>

<details>
<summary><strong>QwenPaw</strong> — <a href="https://github.com/agentscope-ai/QwenPaw">agentscope-ai/QwenPaw</a></summary>

# QwenPaw Project Digest — 2026-11-05

## 1. Today's Overview

QwenPaw remains in a high-velocity stabilization phase: **50 PRs and 10 Issues** were updated in the last 24 hours, but **no new releases** were cut. The activity mix skews toward bug-fixing and hardening of the upcoming **2.3.0 beta line** (2.3.0b2), with multiple Windows, sandbox, channel, and provider-config stability regressions reported and several fix PRs already filed. Community contribution is healthy — at least four first-time-contributor PRs are active — and the maintainers are closing issues with fixes at a reasonable cadence (3 issues closed today). Overall health is **good but noisy**: the core is maturing, while the beta exposes edge-case fragility in less-traveled environments (sandboxed NAS, AppContainer, restricted shells).

---

## 2. Releases

**No new releases in the last 24 hours.** The project is effectively iterating against **2.3.0b2** as the current beta baseline; several of today's issues reference this version directly, suggesting a 2.3.0 stabilization window. Watch for the next beta/RC once the Windows and provider-config bugs are resolved.

---

## 3. Project Progress

Closed/merged items updated today (13 PRs merged/closed, 3 issues closed):

- **[#7613] feat(memory): add OpenViking memory plugin** (CLOSED, XXL, first-time contributor) — adds an OpenViking memory backend with automatic recall, completed-turn persistence, and an explicit `memory_search` tool via the OpenViking REST API. A significant new memory-plugin capability. [Link](https://github.com/agentscope-ai/QwenPaw/pull/7613)
- **[#8575] fix(desktop): restrict auto-update manifest to stable releases** (CLOSED) — aligns desktop auto-update with the browser console's stable-only policy; pre-releases remain manual-download only. Reduces accidental beta uptake. [Link](https://github.com/agentscope-ai/QwenPaw/pull/8575)
- **[#8422] feat(console): add plugin-facing theme token overrides** (CLOSED, L) — plugins can now express light/dark theme token pairs and touch layout roles (surface, border, text), following the six-token design from #8071. [Link](https://github.com/agentscope-ai/QwenPaw/pull/8422)
- **[#8559] feat(matrix): install E2EE dependencies by default** (CLOSED, S) — upgrades the matrix-nio range and installs E2EE deps automatically; encryption remains off by default. [Link](https://github.com/agentscope-ai/QwenPaw/pull/8559)
- **[#8278] DingTalk 3500-char silent downgrade** (Issue CLOSED) — long messages no longer silently degrade Markdown/tables to plain text (fix landed). [Link](https://github.com/agentscope-ai/QwenPaw/issues/8278)
- **[#8515] fnOS sandbox startup crash** (Issue CLOSED) — `dependency_maintenance` lock-root resolution no longer bypasses `$HOME` in restricted environments. [Link](https://github.com/agentscope-ai/QwenPaw/issues/8515)
- **[#8460] Optional browser/OS-level notifications for pending approvals** (Issue CLOSED, enhancement) — request accepted/closed. [Link](https://github.com/agentscope-ai/QwenPaw/issues/8460)

**Net advance:** memory plugin ecosystem +1, Matrix E2EE operational, console theming for plugins, and desktop update hygiene improved.

---

## 4. Community Hot Topics

Ranked by discussion activity:

1. **[#7997] WebUI message retraction/editing + workspace rollback** (9 comments, OPEN, enhancement) — users want to edit/retract messages with automatic conversation truncation and optional file-snapshot rollback. Underlying need: **conversation hygiene and deterministic state recovery** — users treat agent sessions as editable workspaces, not append-only logs. [Link](https://github.com/agentscope-ai/QwenPaw/issues/7997)
2. **[#8278] DingTalk long-message downgrade** (6 comments) — silent format loss on >3500 chars; table-heavy reporting is the key use case. Underlying need: **channel-parity fidelity** — agents producing long structured output (reports, tables) need lossless delivery. [Link](https://github.com/agentscope-ai/QwenPaw/issues/8278)
3. **[#8565] Windows Hub Local runtime cannot start** (4 comments) — AppContainer skill staging, missing CPU arch, reverse-tunnel readiness timeout. Underlying need: **Windows as a first-class runtime**, not an afterthought. [Link](https://github.com/agentscope-ai/QwenPaw/issues/8565)
4. **[#8538] Matrix plugin fails with multiple workspaces** (3 comments) — only first workspace wins a 10s shared startup window. Underlying need: **multi-workspace isolation/isolation correctness** for multi-tenant or multi-project users. [Link](https://github.com/agentscope-ai/QwenPaw/issues/8538)
5. **[#7613] OpenViking memory plugin PR** (XXL, closed) — the largest single contribution this cycle, signaling community appetite for **pluggable memory backends**. [Link](https://github.com/agentscope-ai/QwenPaw/pull/7613)

**Signal:** The community's energy is concentrated on (a) making the agent's conversation state **editable and reversible**, and (b) **environment portability** (Windows, sandboxed NAS, multi-workspace Matrix).

---

## 5. Bugs & Stability

Ranked by severity (all reported/updated today):

| Severity | Issue | Impact | Fix PR |
|---|---|---|---|
| **High** | [#8565](https://github.com/agentscope-ai/QwenPaw/issues/8565) Hub Local runtime cannot start on Windows — AppContainer staging, missing CPU arch, reverse-tunnel timeout | Blocks Hub mode entirely on Windows x64 | Yes — [#8577](https://github.com/agentscope-ai/QwenPaw/pull/8577) (argv token bug) + [#8570](https://github.com/agentscope-ai/QwenPaw/pull/8570) (arch fallback) |
| **High** | [#8579](https://github.com/agentscope-ai/QwenPaw/issues/8579) Disabling a provider leaves stale agent run-config → `ProviderError: Provider is disabled`, surfaced as uninformative "Internal error" | Breaks all channels (Feishu/DingTalk/WeCom/WeChat) until config re-bound | No PR yet |
| **High** | [#8538](https://github.com/agentscope-ai/QwenPaw/issues/8538) Matrix plugin fails when multiple workspaces enable it (10s shared startup window) | Multi-workspace Matrix accounts fail to come online | Related: [#8558](https://github.com/agentscope-ai/QwenPaw/pull/8558) (legacy migration/device locking) |
| **Medium** | [#8560](https://github.com/agentscope-ai/QwenPaw/issues/8560) Desktop window permanently `WS_EX_TOPMOST` in 2.3.0-beta.2 | Covers fullscreen apps (games, videos, kiosk, slides) — clear UX regression | No PR yet |
| **Medium** | [#8580](https://github.com/agentscope-ai/QwenPaw/issues/8580) Memory tag index never rebuilds; `/memory/tags` returns 503 | Tags generated but unreadable — feature effectively broken | No PR yet |
| **Low** | [#8576](https://github.com/agentscope-ai/QwenPaw/issues/8576) Default agent edit button disabled in console though PUT API allows it | UI/API inconsistency; blocks editing `default` agent | No PR yet |
| **Low** | [#8564](https://github.com/agentscope-ai/QwenPaw/pull/8564) Serialized response-policy cache raises `'str' object has no attribute` | Intermittent channel runtime errors | PR open |

**Assessment:** Three of the four High/Medium regressions are **2.3.0b2-specific**, consistent with a beta-exposes-edge-cases pattern. The Windows Hub cluster has partial fixes in flight; the provider-disable and TOPMOST regressions lack fix PRs and should be prioritized.

---

## 6. Feature Requests & Roadmap Signals

Active feature requests and newly landed capabilities pointing to next-version content:

- **Conversation editing/rollback** ([#7997](https://github.com/agentscope-ai/QwenPaw/issues/7997)) — highest-engagement enhancement; likely a 2.4+ candidate given schema/truncation/snapshot complexity.
- **Approval notifications** ([#8460](https://github.com/agentscope-ai/QwenPaw/issues/8460), closed) — browser/OS push for pending approvals; likely landing in the console soon.
- **Pluggable memory** — OpenViking plugin merged ([#7613](https://github.com/agentscope-ai/QwenPaw/pull/7613)) plus **Piccolo runtime install/select** ([#8573](https://github.com/agentscope-ai/QwenPaw/pull/8573)) and **stale daily-note Inbox reminders** ([#8578](https://github.com/agentscope-ai/QwenPaw/pull/8578)) suggest a **memory-ecosystem theme** for 2.3.x/2.4.
- **Cron/agent session hygiene** ([#8557](https://github.com/agentscope-ai/QwenPaw/pull/8557) per-run cron sessions) — bounds long-running job history growth; a scalability signal.
- **Shell memory limits** ([#8574](https://github.com/agentscope-ai/QwenPaw/pull/8574)) and **per-request inline media bounds** ([#8034](https://github.com/agentscope-ai/QwenPaw/pull/8034)) — platform-hardening features likely for a stability release.
- **Matrix E2EE by default** (landed, [#8559](https://github.com/agentscope-ai/QwenPaw/pull/8559)) — E2EE enablement could be the next step.

**Prediction for next version (2.3.0 final / 2.3.1):** Windows Hub Local fixes, provider-config validation ([#8550](https://github.com/agentscope-ai/QwenPaw/pull/8550)), Matrix multi-workspace fixes, and cron session bounding. Conversation editing and OpenViking memory likely target a later minor release.

---

## 7. User Feedback Summary

**Pain points (dissatisfaction):**
- **Silent failures** are a recurring theme: DingTalk downgrades Markdown without signaling the agent (#8278); provider-disable surfaces as generic "Internal error" with no fallback/retry (#8579); memory tags generated but unreadable with 503 (#8580). Users want **actionable errors, not silent degradation**.
- **Environment portability**: Windows Hub, fnOS sandbox, restricted shells — users deploy in constrained real-world environments and hit import-time crashes and startup failures.
- **UX regressions in beta**: always-on-top desktop window (#8560) is a clear annoyance reported with a reproduction.
- **UI/API inconsistency**: console blocks actions the API permits (#8576).

**Satisfaction / positive signals:**
- High-quality, detailed bug reports (multiple with environment, repro steps, and root-cause analysis) indicate an **engaged, technically sophisticated user base**.
- Steady first-time contributor participation (#7613, #8550, #8561, #8535) suggests a **welcoming contributor experience**.
- Feature requests are constructive and scoped (#7997, #8460), with maintainers closing them promptly.

---

## 8. Backlog Watch

Items needing maintainer attention:

- **[#7997] WebUI message retraction/editing + workspace rollback** — open since 2026-09-27, 9 comments, no linked PR. Highest-engagement enhancement; risk of stalling without a design decision. [Link](https://github.com/agentscope-ai/QwenPaw/issues/7997)
- **[#8234] GBK/CP936 decoding in read/edit/grep** — open PR since 2026-10-14 (Under Review, XXL), no recent merge signal; affects Chinese-Windows users' file tools significantly. [Link](https://github.com/agentscope-ai/QwenPaw/pull/8234)
- **[#8236] Ollama context window from `num_ctx`** — open PR since 2026-10-14 (Under Review, XL); local-model users silently truncate at wrong windows. [Link](https://github.com/agentscope-ai/QwenPaw/pull/8236)
- **[#8034] Bound inline media per request** — open XXL PR since 2026-09-29; multi-megabyte request bodies can break gateways; a latent production stability issue. [Link](https://github.com/agentscope-ai/QwenPaw/pull/8034)
- **[#8579] Stale provider run-config** — newly filed, High severity, **no fix PR**; should be triaged promptly as it breaks all channel messaging after a routine config change. [Link](https://github.com/agentscope-ai/QwenPaw/issues/8579)

---

*Digest generated from provided GitHub activity snapshot. Links point to the referenced issues/PRs on github.com/agentscope-ai/QwenPaw.*

</details>

<details>
<summary><strong>ZeroClaw</strong> — <a href="https://github.com/zeroclaw-labs/zeroclaw">zeroclaw-labs/zeroclaw</a></summary>

# ZeroClaw Project Digest — 2026-11-05

## 1. Today's Overview

ZeroClaw shows a **release-gate hardening phase** ahead of v0.9.0, with 7 issues closed and 4 PRs merged/closed in the last 24h — nearly all of them security- or CI-related. Activity is high (50 PR updates, 16 issue updates) but heavily skewed toward a security cluster: principal/identity scoping for delegated tools, SOP execution, and gateway bearer verification. Two P1 and several P0/S0 items (data loss / security risk) moved toward closure today, which is a strong health signal. However, the open PR queue is large (46 open), and the project shipped no releases. Overall: **healthy and disciplined, but in a high-risk security crunch with a growing review backlog.**

## 2. Releases

No new releases in the last 24h. The volume of `release:v0.9.0`-tagged items moving today (see #11198, #11579, #11961, #11230, #11220) indicates v0.9.0 is being actively assembled.

## 3. Project Progress

Merged/closed today (7 issues, 4 PRs), clustered around security and CI hygiene:

- **[#11198](https://github.com/zeroclaw-labs/zeroclaw/issues/11198)** [CLOSED, P0] — Delegated memory tools lost principal scope (data-loss/security risk). Fixed.
- **[#11579](https://github.com/zeroclaw-labs/zeroclaw/issues/11579)** [CLOSED, P1] — `save_dirty` wrongly stamped `schema_version = 3` on V1/V2 configs, causing agents to disappear after migration skip. Fixed.
- **[#11951](https://github.com/zeroclaw-labs/zeroclaw/issues/11951)** [CLOSED] — Gateway WebSocket upgrades failed over built-in TLS listener. Fixed.
- **[#11961](https://github.com/zeroclaw-labs/zeroclaw/issues/11961)** [CLOSED] — Dashboard OIDC sign-in feature landed.
- **[#12218](https://github.com/zeroclaw-labs/zeroclaw/issues/12218)** [CLOSED] and **[#12210](https://github.com/zeroclaw-labs/zeroclaw/issues/12210)** [CLOSED, P1] — CI regressions (Windows Clippy full-history fetch timeout; Docker source image build workspace load failure). Both fixed.
- **[#10526](https://github.com/zeroclaw-labs/zeroclaw/issues/10526)** [CLOSED, RFC accepted] — Append-only session event history & deterministic replay RFC, now backed by open PR **[#11921](https://github.com/zeroclaw-labs/zeroclaw/pull/11921)**.
- Merged/closed PRs: **[#11230](https://github.com/zeroclaw-labs/zeroclaw/pull/11230)** (withhold session-data tools from non-admin principals) and **[#11220](https://github.com/zeroclaw-labs/zeroclaw/pull/11220)** (SOP execution requires admin authority) — significant security containment work.

## 4. Community Hot Topics

- **[#10526](https://github.com/zeroclaw-labs/zeroclaw/issues/10526)** (15 comments, closed RFC) — Append-only session event history and deterministic state replay. Largest discussion today. Underlying need: **auditability and reproducibility** of agent runs — moving beyond mutable conversation logs toward a trustworthy event ledger split across runtime, tool receipts, cost records, and delegates.
- **[#6437](https://github.com/zeroclaw-labs/zeroclaw/issues/6437)** (5 comments, parking-lot) — Zulip channel request, framed as a sibling to the just-merged Rocket.Chat channel. Underlying need: **self-hosted chat parity**; a native implementation PR **[#12230](https://github.com/zeroclaw-labs/zeroclaw/pull/12230)** appeared today.
- **[#11198](https://github.com/zeroclaw-labs/zeroclaw/issues/11198)** (5 comments, P0) — Principal-scope leak in delegated memory tools. Underlying need: **multi-tenant privacy guarantees** for owned sessions/subagents.
- **[#12109](https://github.com/zeroclaw-labs/zeroclaw/issues/12109)** (2 comments) — Multiple plugin registries (`ZEROCLAW_PLUGIN_REGISTRY_URL` array). Underlying need: **public + private registry coexistence**; addressed by open PR **[#12216](https://github.com/zeroclaw-labs/zeroclaw/pull/12216)**.

Also worth noting: PR **[#11921](https://github.com/zeroclaw-labs/zeroclaw/pull/11921)** is a very large (size:XL) cross-cutting change touching nearly every subsystem tag — a likely future hot topic given its blast radius.

## 5. Bugs & Stability

Ranked by severity, today's new/updated bugs:

| Severity | Issue | Description | Fix status |
|---|---|---|---|
| **S0 / P0** | [#11198](https://github.com/zeroclaw-labs/zeroclaw/issues/11198) | Delegated memory tools lose principal scope (data loss/security) | ✅ Closed |
| **S1 / P1** | [#12210](https://github.com/zeroclaw-labs/zeroclaw/issues/12210) | Docker source image builds fail to load workspace | ✅ Closed |
| **S2 / P1** | [#9572](https://github.com/zeroclaw-labs/zeroclaw/issues/9572) | Debug gateway WebSocket turns overflow default Tokio worker stack | ⚠️ Open, no PR |
| **S2 / P1** | [#11579](https://github.com/zeroclaw-labs/zeroclaw/issues/11579) | `save_dirty` stamps schema_version on unmigrated config → agent disappears | ✅ Closed |
| **S2** | [#12119](https://github.com/zeroclaw-labs/zeroclaw/issues/12119) | Windows: shell/skill/coding-CLI timeouts kill only interpreter, descendants survive | ⚠️ Open |
| **S2** | [#12228](https://github.com/zeroclaw-labs/zeroclaw/issues/12228) | `cron update --agent` reports success but never reassigns agent (new today) | ⚠️ Open |
| **S2** | [#12227](https://github.com/zeroclaw-labs/zeroclaw/issues/12227) | Shell approval relies on model-written "approved" value (new today) | ⚠️ Open |
| **S2** | [#12225](https://github.com/zeroclaw-labs/zeroclaw/issues/12225) | `glob_search` never lists files under allowed roots outside workspace (new today) | ⚠️ Open |

**Notable pattern:** three fresh bugs arrived today (#12225, #12227, #12228), all open with **zero comments** — triage attention needed. #12227 (approval prompt driven by model-controlled input) is a **security-relevant trust boundary issue** and should be prioritized despite its S2 label. #12119 (Windows process-tree leak on timeout) is a sustained sandbox weakness on the Windows platform with no fix PR.

## 6. Feature Requests & Roadmap Signals

Candidates for v0.9.x/v1.0, ranked by momentum:

1. **Multiple plugin registries** — [#12109](https://github.com/zeroclaw-labs/zeroclaw/issues/12109) → PR **[#12216](https://github.com/zeroclaw-labs/zeroclaw/pull/12216)** already open. Strong signal for next minor.
2. **Zulip channel** — [#6437](https://github.com/zeroclaw-labs/zeroclaw/issues/6437) → PR **[#12230](https://github.com/zeroclaw-labs/zeroclaw/pull/12230)** opened today. Likely ships alongside other channel work.
3. **Append-only session history / deterministic replay** — RFC [#10526](https://github.com/zeroclaw-labs/zeroclaw/issues/10526) accepted, PR **[#11921](https://github.com/zeroclaw-labs/zeroclaw/pull/11921)** in flight — the most architecturally significant roadmap item.
4. **Long-running shell tasks with async/sync modes + interactive terminal** — [#11954](https://github.com/zeroclaw-labs/zeroclaw/issues/11954); early stage, likely v0.10+.
5. **Retired env-var warnings in `config migrate`** — [#12221](https://github.com/zeroclaw-labs/zeroclaw/issues/12221), small follow-up to schema V4 work.

Additional adjacent signal: transcription default-provider fallback **[#12198](https://github.com/zeroclaw-labs/zeroclaw/pull/12198)** and Telegram media reply-context fixes **[#12197](https://github.com/zeroclaw-labs/zeroclaw/pull/12197)** indicate ongoing polish on channel UX.

## 7. User Feedback Summary

- **Security & trust is the dominant pain point.** Users are finding real isolation gaps: delegated subagents inheriting owner memory (#11198), SOP execution without admin authority (#11220), tools that "do work that does not carry the caller's principal" (#12232), and model-driven shell approval (#12227). This is dissatisfaction with **tenant isolation guarantees**, not feature gaps.
- **Config reliability matters.** The schema-version stamping bug (#11579) is the kind of silent data-loss experience that erodes trust — and it was fixed quickly, which is a positive.
- **Platform friction on Windows** persists (#12119, #12218), suggesting a Windows user base whose sandbox and CI experience lags Unix.
- **Extensibility demand is clear and concrete**: users want private + public plugin registries (#12109) and more self-hosted chat channels (#6437).
- **CI/build stability** attracted rapid fixes (#12210, #12218), suggesting fast maintainer response to workflow blockers.

## 8. Backlog Watch

- **[#9572](https://github.com/zeroclaw-labs/zeroclaw/issues/9572)** (open since 2026-07-31, P1, 2 comments) — Tokio worker stack overflow on debug gateway WebSocket turns. Over three months old with **no fix PR**; an intermittent gateway abort is a serious stability concern.
- **[#6437](https://github.com/zeroclaw-labs/zeroclaw/issues/6437)** (open since 2026-05-06) — Zulip channel, parked in the backlog since May; the new PR #12230 is a sign of life, but the issue itself should be linked and tracked.
- **[#12119](https://github.com/zeroclaw-labs/zeroclaw/issues/12119)** — Windows descendant-process leak on timeout; only 1 comment, no fix. Security-relevant sandbox gap.
- **[#11954](https://github.com/zeroclaw-labs/zeroclaw/issues/11954)** — long-running shell tasks; only 1 comment, needs maintainer scoping.
- **New today, untriaged (0 comments each)**: [#12225](https://github.com/zeroclaw-labs/zeroclaw/issues/12225), [#12227](https://github.com/zeroclaw-labs/zeroclaw/issues/12227), [#12228](https://github.com/zeroclaw-labs/zeroclaw/issues/12228) — should be labeled and assigned within 24–48h per apparent triage norms.
- **PR review debt**: 46 open PRs, several size:XL and risk:high ([#12232](https://github.com/zeroclaw-labs/zeroclaw/pull/12232), [#12213](https://github.com/zeroclaw-labs/zeroclaw/pull/12213), [#11266](https://github.com/zeroclaw-labs/zeroclaw/pull/11266), [#11921](https://github.com/zeroclaw-labs/zeroclaw/pull/11921)). With review-gated security fixes stacked in dependency chains (e.g., #11230 depended on #11225/#11234), maintainer review bandwidth is the main throughput bottleneck heading into v0.9.0.

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar).*