# OpenClaw Ecosystem Digest 2026-11-11

> Issues: 287 | PRs: 500 | Projects covered: 5 | Generated: 2026-11-10 23:14 UTC

- [OpenClaw](https://github.com/openclaw/openclaw)
- [Hermes Agent](https://github.com/nousresearch/hermes-agent)
- [IronClaw](https://github.com/nearai/ironclaw)
- [QwenPaw](https://github.com/agentscope-ai/QwenPaw)
- [ZeroClaw](https://github.com/zeroclaw-labs/zeroclaw)

---

## OpenClaw Deep Dive

# OpenClaw Project Digest — 2026-11-11

## 1. Today's Overview

OpenClaw remains a high-velocity project with **287 issues** and **500 PRs** updated in the last 24 hours, though the pipeline is clearly asymmetric: 342 PRs remain open against only 158 merged/closed, and 198 active issues versus 89 closed. Two releases landed — a patch release (v2026.11.3) and an `extended-stable` maintenance-backport (v2026.9.37) — indicating ongoing dual-track support for the September line. Activity assessment: **very active but maintenance-heavy**. The most-commented items cluster around session-state integrity, memory/dreaming promotion, and update/migration failures, suggesting the project's stability risk is concentrated in long-lived session and upgrade paths rather than core agent execution.

## 2. Releases

**v2026.11.3** ([release notes](https://github.com/openclaw/openclaw/releases))
- **Private task results:** private subagent completions are now kept out of streamed chat replies (#188490, @fuller-stack-dev) — a privacy-hygiene fix for users running subagents alongside shared chat surfaces.
- **Reliable child-task handoffs:** a parent can hand off to replacement work after cancelling a child without leaving the cancelled child stranded. Relevant to fix-seekers on the long-running #154572 / #194403 subagent-authority cluster.
- No breaking changes or migration notes stated.

**v2026.9.37** ([release notes](https://github.com/openclaw/openclaw/releases))
- Gateway-only `extended-stable` release from the **retired 2026.9 maintenance line**, published by explicit maintainer decision. Ships September-2026 code plus critical security updates, reliability/perf fixes, and new model support. Operators on the 2026.9 line should treat this as the last-stop backport and plan migration to the 2026.10/11 line.

**Note:** Multiple reports indicate the fix path to 2026.11.5-beta.1 is itself blocked for affected installs (see #193962, #193157, #144739). Update reliability is a live release-readiness concern.

## 3. Project Progress

Closed/merged this cycle (from the visible sample):

- **[#193962](https://github.com/openclaw/openclaw/issues/193962) (CLOSED, P0, ux-release-blocker):** SQLite header-probe timeout fix unreachable from 2026.10.10 — closed after 25 comments; the underlying update-path defect (#187197 / #187150 lineage) is at least triaged.
- **[#154572](https://github.com/openclaw/openclaw/issues/154572) (CLOSED, P1):** `sessions_spawn` to `claude-cli` children failing with `SessionTranscriptWriterClaimReboundError` — a meaningful subagent-reliability close.
- **[#149631](https://github.com/openclaw/openclaw/issues/149631) (CLOSED):** CI preflight Node-matrix cap (121 > 120 jobs) — CI plumbing fixed.
- **[#134918](https://github.com/openclaw/openclaw/issues/134918) (CLOSED):** embedded-agent empty/incomplete turns on `gpt-5.6-sol` — a long-standing model-specific reliability issue closed.
- **[#175645](https://github.com/openclaw/openclaw/issues/175645) (CLOSED, `not-repro-on-main`):** `NO_REPLY` subagent announce misclassified as `empty_response`, burning the fallback chain.
- **[#156898](https://github.com/openclaw/openclaw/issues/156898) (CLOSED, P1):** usage-cost background refresh worker OOM.
- **[#149221](https://github.com/openclaw/openclaw/issues/149221) (CLOSED):** Control UI double-render from "no active tool authority snapshot" race.
- **[#141115](https://github.com/openclaw/openclaw/issues/141115) (CLOSED):** primary-model 429 terminating the session instead of consulting fallbacks.

PR-side merges visible: [#191795](https://github.com/openclaw/openclaw/pull/191795) (gateway operator Home privacy from restricted guests, security-sensitive), [#196893](https://github.com/openclaw/openclaw/pull/196893) (macOS reopen last-selected Gateway), [#196901](https://github.com/openclaw/openclaw/pull/196901) (test fixture cleanup), [#196898](https://github.com/openclaw/openclaw/pull/196898) (Kova perf evaluator pin).

**Features advancing (open PRs):** A2A streaming with reconnect ([#196903](https://github.com/openclaw/openclaw/pull/196903)), inline template-package review before publishing ([#196427](https://github.com/openclaw/openclaw/pull/196427)), editable pending-chat template adds ([#196395](https://github.com/openclaw/openclaw/pull/196395)), Git co-author profile config ([#196867](https://github.com/openclaw/openclaw/pull/196867)), and a large CI backend migration to "Boat" ([#193038](https://github.com/openclaw/openclaw/pull/193038), [#196894](https://github.com/openclaw/openclaw/pull/196894), [#196904](https://github.com/openclaw/openclaw/pull/196904)).

## 4. Community Hot Topics

1. **[#150635](https://github.com/openclaw/openclaw/issues/150635) — 27 comments (OPEN, P2, "diamond lobster")**: short-term recall eviction at the 512-entry cap means the "dreaming deep phase" never promotes memories. The most-discussed item today; the underlying need is **durable, bounded memory with meaningful promotion semantics** — users want dreaming to be observable and effective, not just a pipeline stage.
2. **[#193962](https://github.com/openclaw/openclaw/issues/193962) — 25 comments (now CLOSED, P0)**: update path cannot deliver the SQLite probe fix to affected installs. Reflects user frustration with **self-update reliability on mature installs**.
3. **[#123792](https://github.com/openclaw/openclaw/issues/123792) — 18 comments**: assistant turns double-rendered on `claude-cli` backends (UI + Android), Telegram unaffected. Points to **CLI-backend transcript persistence being written through two paths**.
4. **[#154572](https://github.com/openclaw/openclaw/issues/154572) — 16 comments (CLOSED)**: subagent spawn to CLI-backed children failing fast. High engagement despite closure; users on 2026.9.5 were badly affected.
5. **[#151962](https://github.com/openclaw/openclaw/issues/151962) — 15 comments**: "phantom user messages" — internal runtime strings reaching the model as user prompts but absent from `transcript_events`. This is the most **trust-eroding** report: users see a transcript that doesn't match what the model saw. Underlying need: **transcript/audit fidelity**.
6. PR-side: [#188295](https://github.com/openclaw/openclaw/pull/188295) (silent bootstrap-file skip warning) carries a "diamond lobster" rating and is ready for maintainer look.

**Takeaway:** community energy is concentrated on **session-state observability and trust** — memory promotion, transcript integrity, and update correctness — not on new surface features.

## 5. Bugs & Stability

Ranked by severity (P0 → P1 → P2):

**P0 / release-blocking:**
- **[#194664](https://github.com/openclaw/openclaw/issues/194664)**: Codex "protected continuation" blocks all follow-ups while session Stop reports no active run (2026.11.1). Every message fails pre-inference. No fix PR linked — **actively blocking users**.
- **[#190563](https://github.com/openclaw/openclaw/issues/190563)**: cannot resume paused Codex chat — provider review/native thread mismatch (regression). Needs security review; no fix PR.
- **[#193962](https://github.com/openclaw/openclaw/issues/193962)**: (CLOSED today) update path blocked by header-probe timeout.

**P1:**
- **[#194403](https://github.com/openclaw/openclaw/issues/194403)**: helper-completion continuations invalidate their own authority, leaving conversations blocked (2026.11.1).
- **[#157255](https://github.com/openclaw/openclaw/issues/157255)**: turn claim not released after lane timeout — sessions wedged 90+ minutes.
- **[#181459](https://github.com/openclaw/openclaw/issues/181459)**: 3.5 GB agent DB makes startup a gamble; `commit checkpoint expired`; embeddings stored 3×.
- **[#91931](https://github.com/openclaw/openclaw/issues/91931)**: preseeded SOUL/IDENTITY/USER files cause bootstrap auto-complete and **deletion of user BOOTSTRAP.md** before first run — data-loss with clear UX impact. Has a corresponding fix PR in flight: [#196411](https://github.com/openclaw/openclaw/pull/196411) (preserve custom bootstrap onboarding).
- **[#168101](https://github.com/openclaw/openclaw/issues/168101)**: Retry-After ignores `maxRetryDelayMs` after a tool call, holding the session lane for the full sleep.

**P2, notable clusters:**
- **Memory/dreaming:** [#150635](https://github.com/openclaw/openclaw/issues/150635) (eviction vs. promotion), [#138561](https://github.com/openclaw/openclaw/issues/138561) (active-memory recall silently stops after upgrade), [#151173](https://github.com/openclaw/openclaw/issues/151173) (recall rehydration anchors on fragments).
- **Session integrity / message loss:** [#144594](https://github.com/openclaw/openclaw/issues/144594) (pendingInputs stuck `interrupted`), [#141172](https://github.com/openclaw/openclaw/issues/141172) (leaked `agent.run.started`), [#151962](https://github.com/openclaw/openclaw/issues/151962) (phantom prompts).
- **Storage bloat:** [#143973](https://github.com/openclaw/openclaw/issues/143973) — `view_image` persists full base64 in `transcript_events` (1.3% of events = 80% of bytes). No fix PR; a high-leverage cleanup target.
- **Security-adjacent:** [#159230](https://github.com/openclaw/openclaw/issues/159230) (`message_tool_only` stranded-reply recovery re-prompts the model with private text and force-sends an English error DM; no off switch). Open fix PRs: [#196848](https://github.com/openclaw/openclaw/pull/196848) (Discord removed-DM sender still runs commands via stale binding), [#196847](https://github.com/openclaw/openclaw/pull/196847) (self-hosted web tool redirects followed with private-network access).
- **Update/migration:** [#193157](https://github.com/openclaw/openclaw/issues/193157) (retired `agents.defaults.heartbeat` key rejects 2026.11.5-beta.1), [#144739](https://github.com/openclaw/openclaw/issues/144739) (2026.9.3→9.4 npm update runs old version against schema-17 state).
- **Perf/idle CPU:** [#192496](https://github.com/openclaw/openclaw/issues/192496) (idle skills refresh ~100–170% core at 20 agents), [#138918](https://github.com/openclaw/openclaw/issues/138918) (macOS WindowServer CPU from visible WebChat).

**Fix PRs existing:** [#196411](https://github.com/openclaw/openclaw/pull/196411) (bootstrap), [#196254](https://github.com/openclaw/openclaw/pull/196254) (spawned-session memory after parent compact/rewind), [#196853](https://github.com/openclaw/openclaw/pull/196853) (paused goal still runs turns), [#196896](https://github.com/openclaw/openclaw/pull/196896) (streaming-surge session work reduction), [#187668](https://github.com/openclaw/openclaw/pull/187668) (ACP failed identity save), [#165805](https://github.com/openclaw/openclaw/pull/165805) (node directory listing pagination).

## 6. Feature Requests & Roadmap Signals

- **[#138366](https://github.com/openclaw/openclaw/issues/138366)** — let the memory slot owner drive the Dreams page via a `dreaming` provider in `MemoryPluginCapability`. If addressed, this could plausibly pair with the v11.3 subagent-privacy stream into an upcoming **memory/extension API** increment.
- **[#192009](https://github.com/openclaw/openclaw/issues/192009)** — explicit cross-provider cron fallback authorization. High operator demand; likely a config-schema addition rather than new UI.
- **[#140213](https://github.com/openclaw/openclaw/issues/140213)** — multiple policy aliases resolving to one configured model; has a maintainer decision packet, a strong candidate for near-term merge.
- **[#151980](https://github.com/openclaw/openclaw/issues/151980)** — session model pins should inherit the agent fallback chain. Directly complements the 2026.9-era fallback fixes; low implementation cost, high operator value.
- **[#112372](https://github.com/openclaw/openclaw/issues/112372)** — opt-in `wakeOnReply` deferred completion for `sessions_send` (closed this cycle, likely superseded).
- **[#138990](https://github.com/openclaw/openclaw/issues/138990)** — enforceable checkpoints/bounded yields for long foreground WebChat runs.
- **[#155039](https://github.com/openclaw/openclaw/issues/155039)** — manual team-sidebar agent ordering via synced prefs (UI polish).
- **[#119294](https://github.com/openclaw/openclaw/issues/119294)** — MCP 2.0 (stateless spec) migration tracking; closed but signals a future breaking-change track.

**Prediction:** next-release visible features are most likely **bootstrap-preservation ([#196411](https://github.com/openclaw/openclaw/pull/196411)), spawned-session memory lineage ([#196254](https://github.com/openclaw/openclaw/pull/196254)), paused-goal spending stop ([#196853](https://github.com/openclaw/openclaw/pull/196853)), A2A streaming ([#196903](https://github.com/openclaw/openclaw/pull/196903)), and template-publishing review ([#196427](https://github.com/openclaw/openclaw/pull/196427))**. Dreaming/provider extensibility is a longer arc.

## 7. User Feedback Summary

**Pain points:**
- **Upgrade anxiety dominates sentiment.** Users on 2026.10.10 cannot reach fixes; users on 2026.9.x hit schema-17 state mismatches; 2026.11.5-beta.1 is refused by retired config keys. The update path is the single most consistent source of frustration.
- **Memory is the feature users most want to trust.** Nightly eviction defeating dreaming, silent recall stops after upgrade, and 3.5 GB stores show users leaning on memory in production and feeling it is opaque.
- **Transcript vs. runtime divergence worries operators.** Phantom prompts and double-rendered turns indicate the audit surface doesn't match what the model actually sees.
- **Large-install scaling issues:** multi-agent CPU at idle, long startup on multi-GB DBs, base64-in-transcript bloat.

**Satisfaction signals:** rapid triage of P0/P1 (e.g., #193962 closed in ~4 days with 25 comments), an explicit maintainer-backed backport policy for the retired 9.x line, and a steady stream of well-credited security PRs (#196848, #196847) show a maintained security posture.

**Dissatisfaction signals:** several P1/P0 items lack fix PRs after days-weekly waits (#194664, #190563, #157255, #181459); stale-tagged issues like #57278, #92473, #112313 remain open for months.

## 8. Backlog Watch

Long-lived or high-severity items needing maintainer attention:

- **[#91931](https://github.com/openclaw/openclaw/issues/91931) (P1, diamond lobster, since 2026-06-10)**: user `BOOTSTRAP.md` deletion — data loss on first run. **[Fix PR #196411 exists](https://github.com/openclaw/openclaw/pull/196411)**; reviewers should prioritize it.
- **[#112313](https://github.com/openclaw/openclaw/issues/112313) (since 2026-07-21, 12 comments)**: dead-lettered outbound queue entries are permanent and un-clearable. Needs a product decision on TTL/CLI cleanup.
- **[#112638](https://github.com/openclaw/openclaw/issues/112638) (since 2026-07-22)**: `session.maintenance` `enforce` mode does not bound the store — thread/channel entries bypass all reclaim. Large-install relevance; needs product decision.
- **[#92473](https://github.com/openclaw/openclaw/issues/92473) (since 2026-06-12)**: model fallback notice leaking into user-facing channels (internal info disclosure). Fix-shape seems clear but unmerged.
- **[#57278](https://github.com/openclaw/openclaw/issues/57278) (since 2026-03-29, oldest in view)**: Telegram bot-photo reply-context attribution — reported conclusion was too strong; still awaiting maintainer resolution.
- **[#144594](https://github.com/openclaw/openclaw/issues/144594) (platinum hermit)**: `pendingInputs` stuck in `interrupted` with no replay — session-state/message-loss with a clear fix shape.
- **[#132767](https://github.com/openclaw/openclaw/issues/132767)**: CLI host omits "assemble-before-prompt" so context-engine `systemPromptAddition` arrives one turn late; source fix existed (PR #100984) but was closed unmerged — the original PR is worth reconsidering.
- **[#143973](https://github.com/openclaw/openclaw/issues/143973)**: base64 image payloads dominating transcript storage — high leverage for large DBs, no fix PR.
- PR-side:**[#171315](https://github.com/openclaw/openclaw/pull/171315)** (Telegram attachment prep hook) has been open ~4 weeks with a security-boundary merge risk and needs proof; **#186966** (copy text from Browser panel) and **#188295** (bootstrap skip warning) are marked ready for maintainer look.

---

**Overall health assessment:** Velocity is healthy and security triage is active, but the project carries **concentrated debt in session state, memory durability, and the self-update path**. The clearest near-term wins are merging already-reviewed fixes (#196411, #196254, #196853) and resolving the P0 Codex continuation blocks (#194664, #190563) before the next stable cut.

---

## Cross-Ecosystem Comparison

# Cross-Project Comparison Report — Personal AI Assistant / Agent Open-Source Ecosystem
**Date: 2026-11-11 | Projects: OpenClaw, Hermes Agent, IronClaw, QwenPaw, ZeroClaw**

---

## 1. Ecosystem Overview

The personal AI assistant / agent open-source landscape is in a **consolidation-and-hardening phase**: all four active projects have shipped major architectural changes (gateway unification, monorepo moves, plugin runtime rework, OIDC auth) and are now absorbing the fallout in the form of session-state, memory-durability, and provider-compatibility bugs. Velocity remains exceptionally high — OpenClaw alone touched 287 issues and 500 PRs in 24h — but the *character* of activity has shifted from new surface features toward **reliability, observability, and upgrade-path correctness**. A consistent cross-project pain pattern is emerging around **silent failures** (dropped messages, truncated paths, deleted user data, misrouted config) and **self-update/migration integrity**, suggesting the ecosystem's next competitive axis is trust and operability rather than raw capability. Meanwhile, **multi-channel reach** (QQ, Feishu, Telegram, XMPP, SimpleX) and **local inference** (llama.cpp, MLX) are the two clearest expansion vectors.

---

## 2. Activity Comparison

| Project | Issues (24h) | PRs (24h) | Merge/Close Rate (PR) | Release Status | Health Score* | Notes |
|---|---|---|---|---|---|---|
| **OpenClaw** | 287 updated (198 open / 89 closed) | 500 updated (342 open / 158 merged+closed) | ~32% | v2026.11.3 (patch) + v2026.9.37 (extended-stable backport) | **7.0 / 10** | Highest absolute velocity; heavy maintenance load; P0 Codex blocks unresolved |
| **Hermes Agent** | 50 (23 open / 27 closed) | 50 (43 open / 7 merged+closed) | ~14% | v0.23.1 (~618 PR roll-up, Nov 9) | **6.5 / 10** | Healthy close rate on issues; growing PR queue; gateway P1s without fix PRs |
| **IronClaw** | 0 | 0 | — | — | **N/A (dormant)** | No activity in 24h |
| **QwenPaw** | 14 (9 open / 5 closed) | 39 (21 open / 18 merged+closed) | ~46% | v2.3.0-beta.4 (Nov 10); GA bump PR open | **7.5 / 10** | Best merge ratio; beta-regression risk before GA |
| **ZeroClaw** | 19 (12 closed) | 50 (9 merged+closed / 41 open) | ~18% | v0.9.0 imminent (bump PR #12377) | **7.5 / 10** | Excellent same-day bug turnaround; CI flakiness + aging XL PRs |

\* *Health score is an analyst composite of: merge/close ratio, P0/P1 fix-PR coverage, release cadence, security responsiveness, and backlog age. Not a maintainer metric.*

**Reading the table:** OpenClaw dominates in absolute volume but carries the heaviest open-PR debt (342 open) and the most unresolved P0s. QwenPaw and ZeroClaw show the healthiest *ratios* (fast closure, same-day fixes) despite smaller absolute numbers. Hermes has the weakest PR merge ratio in the sample and the most acute silent-data-loss gaps.

---

## 3. OpenClaw's Position

**Advantages vs peers**
- **Ecosystem gravity / reference status:** The digest frames OpenClaw as the "core reference," and its feature surface (A2A streaming, template-package publishing, Git co-author profiles, CI "Boat" migration) is a generation broader than peers.
- **Dual-track maintenance maturity:** The explicit `extended-stable` backport for the retired 2026.9 line (v2026.9.37) is a level of release-engineering discipline not visible in any peer. ZeroClaw and QwenPaw are still single-track pre-GA.
- **Security posture:** Visible, well-credited security PRs (#191795 gateway operator privacy, #196848 Discord stale-binding, #196847 web-tool SSRF) indicate an active threat-review process.
- **Absolute velocity:** 500 PRs / 24h dwarfs every peer by an order of magnitude.

**Technical approach differences**
- OpenClaw is **gateway + session-state-centric** with an explicit `sessions_spawn` / subagent-authority model and a "dreaming"/memory-promotion pipeline. Peers are converging on similar gateway concepts (Hermes #106742 unified gateway) but OpenClaw is further along — and correspondingly further into the associated debt.
- It runs a **plugin/memory capability API** (`MemoryPluginCapability`, `dreaming` provider) — a formalization peers like QwenPaw (#8426 extract-plugin) and ZeroClaw are only beginning.

**Community size comparison**
- OpenClaw's 24h issue+PR volume (787 items) is **~6.5× Hermes (100)**, **~14× QwenPaw (53)**, and **~11× ZeroClaw (69)**. Its comment threads (27, 25, 18, 16, 15) are the deepest in the cohort, indicating a large, vocal operator base rather than a small maintainer-driven project.

**Caveat:** OpenClaw's advantage is *breadth under stress*. Its P0 Codex continuation blocks (#194664, #190563) have **no fix PRs** — a vulnerability the smaller, faster-closing projects (ZeroClaw, QwenPaw) would likely have already patched. OpenClaw wins on capability and ecosystem; it is mid-pack on release readiness.

---

## 4. Shared Technical Focus Areas (Cross-Project Requirements)

| Requirement | OpenClaw | Hermes | QwenPaw | ZeroClaw | Emerging Need |
|---|---|---|---|---|---|
| **Session-state integrity / recovery** | #194664, #190563, #157255, #144594 | #148778 (stranded `unknown` turns), #137652 | #8652 (stale model dispatch) | #12376 (detached turns lose answer) | Recoverable, observable turn lifecycle; no silent wedging |
| **Silent-failure elimination** | #151962 phantom prompts, #143973 base64 bloat | #137652 silent msg deletion, #121078 silent plugin shadow | #8651 image drop, #8406 flag reset, #8655 path truncation | #12361 missing `reasoning_content` | Users demand explicit warnings/logs on any drop, reset, or truncation |
| **Upgrade / migration integrity** | #193962, #193157, #144739 (self-update blocks) | #152008 (compat shims for old updaters) | beta-series regressions (#8655) | #12153 install.sh musl/glibc | Idempotent, downgrade-safe, shim-backed upgrades |
| **Memory durability & observability** | #150635 (dreaming vs eviction), #138561, #181459 (3.5 GB DB) | (compaction #137652) | — | — | Bounded, promotions that actually fire; no silent recall stops |
| **Provider compatibility (non-OpenAI)** | gpt-5.6 / Codex continuation | #148380 Anthropic `json_mode` | #8656 Mistral 422, #7990 Aliyun reasoning metadata | #12366/#12362 temperature hardcoding | Stop hardcoding `temperature`/schema assumptions; catalog-driven metadata |
| **Multi-channel deployment** | (Discord, web) | Telegram wave (10 fixes) | QQ, Feishu, WeChat, DingTalk | XMPP (#11847), SimpleX (#12316), Git channel | Channel startup reliability is the #1 integration complaint |
| **Local inference as first-class** | — | Modal provider (#152951) | llama.cpp/MLX stack (#8649/#8647/#8648) | — | GPU-aware, auto-updating, console-managed local engines |
| **CI/test infrastructure debt** | Node-matrix cap (#149631) | — | — | #12351/#12350 flaky/hanging tests | Flaky tests now gate releases directly |

**Synthesis:** Four requirements recur in *every* active project — **(1) turn/session recoverability, (2) loud-over-silent failure semantics, (3) migration correctness, and (4) provider-parameter flexibility**. These are no longer differentiators; they are table stakes. Local inference and multi-channel are the two areas where projects diverge strategically.

---

## 5. Differentiation Analysis

| Dimension | OpenClaw | Hermes | QwenPaw | ZeroClaw |
|---|---|---|---|---|
| **Feature focus** | Broad agent platform: subagents, A2A, templates, dreaming memory | Gateway unification, Kanban multi-worker, plugin catalog | Multi-channel (QQ/Feishu) + local engines | Auth hardening, docs/SEO, channel breadth |
| **Target user** | Power operators running multi-agent, multi-surface installs | Power users on parallel agent workflows (Kanban) | Chinese-market multi-channel personal assistants, Desktop users | Multi-user/team deployments, security-conscious operators |
| **Architecture signal** | Gateway + session-authority + plugin/memory capability API | Single-process unified gateway (post-#106742) | Channel-worker plugin runtime + managed local engines | OIDC/device-cert auth, zerorelay frontdoor, zerocode CLI |
| **Release posture** | Dual-track mature (patch + extended-stable) | Roll-up train (~618 PRs per patch) | Beta stabilization → GA | Pre-v0.9.0 sprint |
| **Biggest weakness** | Update path + unresolved P0s | Gateway P1s + silent data loss | Beta regressions | CI flakiness + aging XL PR queue |
| **Distinct bet** | Memory/dreaming extensibility as moat | Kanban parallel-agent workflows | Local inference + China-channel reach | Auth/security + docs maturity |

**Key insight:** No two projects are competing on the same primary axis. OpenClaw bets on **platform depth**; Hermes on **parallel-workflow (Kanban)**; QwenPaw on **channel breadth + local models**; ZeroClaw on **multi-user auth/operability**. The overlap is in the *enabling layer* (gateway, session state, upgrades), which is precisely where all four are accumulating debt.

---

## 6. Community Momentum & Maturity

**Activity tiers (by 24h volume):**
- **Tier 1 — High-volume platform:** OpenClaw (787 items/24h). Rapidly iterating, but maintenance-dominated; maturity high, release-readiness mid.
- **Tier 2 — Focused sprinters:** Hermes (100), ZeroClaw (69), QwenPaw (53). All three are **rapidly iterating toward a release** (Hermes v0.24.0, ZeroClaw v0.9.0, QwenPaw v2.3.0 GA).
- **Tier 3 — Dormant:** IronClaw (0/24h). Should be excluded from active ecosystem comparison until activity resumes.

**Rapidly iterating (feature-forward):**
- **QwenPaw** — local-engine stack (#8649/#8647/#8648), plugin extraction (#8426), skill categorization (#8178). Beta cadence with same-day fix closures.
- **ZeroClaw** — OIDC device grants, XMPP/SimpleX channels, scoped secrets (#12373). Imminent v0.9.0.

**Stabilizing (debt-repayment mode):**
- **Hermes** — absorbs unified-gateway fallout; Telegram wave *closing* but gateway P1s *open*. Genuinely mid-stabilization.
- **OpenClaw** — mature but carrying concentrated session/memory/update debt; the "maintenance-heavy" self-assessment matches the data.

**Maturity ranking (release engineering, not features):**
1. **OpenClaw** — dual-track backports, explicit EOL policy.
2. **ZeroClaw** — documented risk/size labels on PRs, same-day S1 fixes.
3. **QwenPaw** — clean beta gating but regression-prone beta line.
4. **Hermes** — strongest issue-close rate, weakest PR merge ratio and P1 fix coverage.

**Momentum verdict:** ZeroClaw and QwenPaw have the **best closure-to-open balance** for their size and are the most likely to convert momentum into a clean GA. Hermes has the most **acute gap between community energy and maintainer fix velocity** on its P1s. OpenClaw has the most momentum but the least headroom to absorb it without a dedicated stabilization pass.

---

## 7. Trend Signals (Value for AI Agent Developers)

Extracted from cross-project community feedback — actionable signals for anyone building on or against this ecosystem:

1. **Trust is the new feature.** Every project's top complaints are *silent* failures — deleted user messages (Hermes #137652), truncated attachment paths (QwenPaw #8655), phantom prompts (OpenClaw #151962), dropped image messages (QwenPaw #8651), ignored `-w` (Hermes #67458). **Design principle for agent developers:** every mutation, drop, reset, or truncation must emit an explicit, user-visible warning and a recoverable artifact. Silent degradation is now a competitive liability.

2. **Self-update is the ecosystem's weakest link.** OpenClaw cannot deliver fixes to affected installs (#193962), Hermes needs compat shims for old updaters (#152008), ZeroClaw's installer mis-picks musl/glibc (#12153), QwenPaw's beta line regresses on upgrade. **Build downgrade-safe, shim-backed, idempotent upgraders from day one**; treat the update path as a first-class, tested product surface.

3. **Provider-parameter hardcoding is a recurring integration tax.** Three separate projects hit failures from hardcoded `temperature` (ZeroClaw #12366/#12362), missing `reasoning_content` (ZeroClaw #12361), schema `additionalProperties` (Hermes #148380), and missing `thinking_param_style` (QwenPaw #7990). **Drive provider behavior from catalog metadata, not inline constants** — and expose reasoning-effort controls as first-class UX.

4. **Memory must be observable, bounded, and promotion-honest.** OpenClaw's "dreaming never promotes" (#150635), 3.5 GB agent DBs (#181459), and silent recall stops (#138561) show memory is used in production but opaque. **Instrument memory stages; make eviction and promotion visible; cap and reclaim storage.**

5. **Transcript/audit fidelity is a governance requirement.** OpenClaw's phantom-prompt and double-render reports (#151962, #123792) show the audit surface diverging from what the model sees. For regulated or audited deployments, **the transcript must be the ground truth** — write it through a single path.

6. **Parallel-agent workflows (Kanban, subagents) hit concurrency reliability walls.** Hermes' two P1 Kanban-worker crashes (#145022, #139029) and OpenClaw's subagent-authority cluster (#154572 lineage) both stem from **owner arbitration and event-loop blocking under concurrency**. If you build multi-agent orchestration, **budget for owner-arbitration and non-blocking persistence engineering up front**.

7. **Local inference is going first-class.** QwenPaw's llama.cpp/MLX/console stack is the largest single roadmap bet in the cohort. Expect **GPU-aware, auto-updating, UI-managed local engines** to become a baseline expectation, not a plugin.

8. **Multi-channel is a reliability surface, not a checkbox.** Telegram (Hermes), QQ/Feishu (QwenPaw), XMPP/SimpleX/Git (ZeroClaw) all generate the majority of integration bugs — and startup races (QwenPaw #8669) and silent drops (#8651) dominate. **Treat each channel as an independently tested adapter with startup-health probes.**

9. **CI flakiness now gates releases.** ZeroClaw's hanging Telegram tests and intermittent Anthropic fallback test (#12350/#12351) are explicitly cited as release-delay risks. **Test-quarantine and flake-detection infrastructure is release-critical.**

---

**Bottom line for decision-makers:** The ecosystem's capability frontier is advancing fast, but the *operational* frontier — upgrades, session recovery, silent-failure elimination, provider metadata, and CI reliability — is where all four active projects are bleeding. **OpenClaw leads on platform depth and ecosystem gravity but needs a stabilization pass before its next stable cut** (merge #196411/#196254/#196853, resolve #194664/#190563). **ZeroClaw and QwenPaw are the momentum leaders with the cleanest path to a healthy release**, and **Hermes carries the highest silent-data-loss risk** that should be triaged before v0.24.0. **IronClaw is dormant and excluded.**

---

## Peer Project Reports

<details>
<summary><strong>Hermes Agent</strong> — <a href="https://github.com/nousresearch/hermes-agent">nousresearch/hermes-agent</a></summary>

# Hermes Agent — Project Digest for 2026-11-11

## 1. Today's Overview

Hermes Agent shows a **high-velocity, stable-release cadence** with v0.23.1 rolling up ~618 merged PRs since v0.23.0, while post-release stabilization continues on `main`. Activity is balanced: 50 issues updated in 24h (23 open, 27 closed) and 50 PRs updated (43 open, 7 merged/closed), indicating a **healthy close rate but a growing open PR queue**. The dominant theme today is the **unified gateway (#106742) post-merge fallout** — a cluster of P1/P2 session-state, Kanban-worker, and message-delivery bugs all trace back to the single-process gateway architecture. Secondary themes are **Telegram adapter reliability** (many closed fixes today) and **plugin-catalog expansion** (multiple new catalog PRs, likely hackathon-driven). Overall the project is **functionally advancing but carries elevated stability risk in the gateway/session layer**.

---

## 2. Releases

**v0.23.1** (released November 9, 2026) — *patch release*
- Rolls up **~618 PRs** merged since v0.23.0 into a stable release for: GitHub release (CLI + installers), Microsoft Store app, Docker image, and Hermes Cloud.
- **Breaking changes / migration notes:** None documented in this patch. However, a related open PR (#152008) warns that **generated compat shims are required for v0.22.7–v0.23.1 updaters** to accept the monorepo module move — without the root `hermes_state_common.py` shim, old updaters would reject the moved tree as a downgrade. This is a **migration risk to watch** for users on older updaters.
- Full curated notes deferred to v0.24.0.

> **Note:** This is a roll-up release; individual fix attribution should be read from the v0.24.0 notes when published.

---

## 3. Project Progress

**Merged/closed PRs today: 7** (limited detail available from the 20-PR sample; all sampled PRs are open). Notable advancement signals:

- **Telegram reliability wave (closed today):** #76022, #150861, #143122, #133164, #33270, #133093, #47093, #47415, #78719, #89615 — a large batch of Telegram adapter bugs closed (attachment loss, shutdown message drops, media timeouts, dice/group-photo handling).
- **Gateway message delivery (closed):** #72131 (duplicate provider-error delivery), #126461 (duplicate streaming sends on timeout), #145161 (`/steer` deadlock during clarify), #150153 (API session boundary 500).
- **Tooling/infra (closed):** #124357 (`_ensure_mcp_sdk` race).
- **In-flight feature work (open PRs):** monorepo module-move tooling (#151960, #152293, #151880, #151798, #152008), Modal inference provider (#152951), and a large set of **plugin-catalog additions** (visual-memory, tripscout, loshify, agentline, lydite, kumi, EarnFi, pixel-hq bump).

**Assessment:** Progress is strong on adapter-level fixes; the monorepo migration tooling is a significant ongoing engineering investment.

---

## 4. Community Hot Topics

| Rank | Item | Comments | Signal |
|------|------|----------|--------|
| 1 | [#143547 — Unified gateway post-merge problems](https://github.com/NousResearch/hermes-agent/issues/143547) (P2, open) | 60 | **Highest engagement.** Central tracking issue for the #106742 gateway consolidation fallout. |
| 2 | [#145022 — Kanban workers crash "execution unknown; retained by owner"](https://github.com/NousResearch/hermes-agent/issues/145022) (P1, open) | 20 | Severe: 4 workers killed by one gateway stall; work-in-progress lost. |
| 3 | [#67458 — `-w/--worktree` silently ignored in `-z` one-shot mode](https://github.com/NousResearch/hermes-agent/issues/67458) (P3, needs-decision) | 9 | Silent data-safety footgun: commits land on current branch. |
| 4 | [#20510 — Cloud sync for all configs across devices](https://github.com/NousResearch/hermes-agent/issues/20510) (P2, **👍24**) | 9 | **Most-upvoted issue in sample** — strong cross-device demand. |
| 5 | [#139029 — Kanban worker `WorkerPersistenceError: synchronous_rpc_on_event_loop`](https://github.com/NousResearch/hermes-agent/issues/139029) (P1, open) | 8 | Second P1 Kanban-worker failure mode; event-loop blocking. |

**Underlying needs analysis:**
- The **unified gateway** is the community's focal pain point — users are collating symptoms (sessions, cron, plugins, multi-platform) rather than filing isolated bugs, signaling **architectural, not surface-level, friction**.
- **Kanban multi-worker reliability** is a distinct P1 cluster: two P1 issues (#145022, #139029) both involve worker persistence/owner arbitration under concurrency.
- **Config portability** (👍24) reflects real multi-device workflows and unmet "roaming" expectations.

---

## 5. Bugs & Stability

Ranked by severity (open = unresolved):

| Severity | Issue | Description | Fix status |
|----------|-------|-------------|-----------|
| **P1** | [#145022](https://github.com/NousResearch/hermes-agent/issues/145022) | Kanban workers crash ("retained by owner") on gateway stall; WIP lost | Open, no linked PR |
| **P1** | [#139029](https://github.com/NousResearch/hermes-agent/issues/139029) | `WorkerPersistenceError: synchronous_rpc_on_event_loop` — event-loop blocking in managed worker | Open, no linked PR |
| **P1** | [#137652](https://github.com/NousResearch/hermes-agent/issues/137652) | Compaction **silently deletes a user message** merged after a todo snapshot | Open, no linked PR |
| **P2** | [#148778](https://github.com/NousResearch/hermes-agent/issues/148778) | Turn stranded `unknown` by settlement crash — desktop has no recovery path | Open |
| **P2** | [#148380](https://github.com/NousResearch/hermes-agent/issues/148380) | Anthropic `json_mode` always 400s (schema lacks `additionalProperties`) | Open |
| **P2** | [#121078](https://github.com/NousResearch/hermes-agent/issues/121078) | Plugin manifest name collision is **silent** — `.bak` dir can shadow real plugin | Open |
| **P2** | [#45106](https://github.com/NousResearch/hermes-agent/issues/45106) | SELinux breaks Docker backend volume binds | Open (long-standing, since June) |
| **P2** | [#150713](https://github.com/NousResearch/hermes-agent/issues/150713) | Nix `pnpm-deps` hash mismatch on macOS after pnpm migration | Open |
| **P2 (fix PR open)** | [#151399](https://github.com/NousResearch/hermes-agent/pull/151399) | Gateway omits `interim_assistant_callback` on admission path | **Fix PR exists** |
| **P2 (fix PR open)** | [#152106](https://github.com/NousResearch/hermes-agent/pull/152106) | Telegram polling-conflict reconnect with bounded back-off | **Fix PR exists** |

**Stability assessment:** The three P1s (two Kanban, one compaction data-loss) are **unresolved with no visible fix PRs** and represent the highest project-health risk. The compaction data-loss (#137652) is particularly concerning as it is **silent**. Telegram bugs are trending *closed* (good), but gateway/session P1s are not.

---

## 6. Feature Requests & Roadmap Signals

| Issue | Ask | Likelihood for next version |
|-------|-----|----------------------------|
| [#20510](https://github.com/NousResearch/hermes-agent/issues/20510) | Cloud/cross-device config sync (👍24) | **Medium-long term** — high demand but architectural; likely Hermes Cloud aligned |
| [#114029](https://github.com/NousResearch/hermes-agent/issues/114029) | Filter reasoning-effort picker to model-supported levels | **High** — UI polish, low risk, touches many surfaces |
| [#137034](https://github.com/NousResearch/hermes-agent/issues/137034) | Markdown preview in input box (👍3) | **Medium** — contained desktop feature |
| [#67458](https://github.com/NousResearch/hermes-agent/issues/67458) | Honor `-w` in one-shot mode (needs-decision) | **Low-medium** — blocked on design decision |
| [#55038](https://github.com/NousResearch/hermes-agent/issues/55038) | Fix docs: cron "3-min" vs actual 600s inactivity timeout | **High** — docs-only, quick win |

**Roadmap signal:** The open PR set (Modal provider #152951, plugin-catalog expansion, monorepo shims #152008) suggests the **next release trajectory centers on provider breadth, plugin ecosystem growth, and the module-move migration** rather than on the gateway P1s — a potential mismatch with community priorities.

---

## 7. User Feedback Summary

**Pain points (dissatisfaction):**
- **Gateway consolidation disruption** — users experience broad, cross-cutting breakage (sessions, cron, Telegram, desktop) that feels systemic rather than localized.
- **Data-loss anxiety** — silent message deletion (#137652), worktree flag ignored (#67458), lost Kanban WIP (#145022), and silent plugin shadowing (#121078). Multiple "silent" failure modes are a recurring trust theme.
- **Recovery gaps** — stranded `unknown` turns (#148778) leave users with no in-app recourse.
- **Long-tail platform friction** — Telegram has absorbed a large share of complaints but *also* most fixes today (positive responsiveness signal).

**Use cases observed:**
- Multi-device personal config portability (#20510).
- Kanban-driven parallel agent workflows (multi-worker) — a serious power-user pattern now hitting P1 reliability limits.
- Long-running chat sessions with typing/status indicators.

**Satisfaction signals:** High bug-close velocity (27 closed issues today), upvote-driven demand (#20510, 👍24), and an active hackathon-driven plugin ecosystem (many catalog PRs) indicate an engaged, invested community.

---

## 8. Backlog Watch

Important items needing maintainer attention (aging + severity/engagement):

| Item | Age / Status | Why it matters |
|------|-------------|----------------|
| [#45106 — SELinux breaks Docker backend](https://github.com/NousResearch/hermes-agent/issues/45106) (P2) | Open since **2026-06-12** | ~5 months unaddressed; blocks all SELinux-enforcing distros (OpenSUSE etc.). |
| [#67458 — `-w` ignored in one-shot](https://github.com/NousResearch/hermes-agent/issues/67458) (P3, needs-decision) | Open since **2026-07-19** | Data-safety footgun stalled on a design decision; needs triage owner. |
| [#55038 — cron docs contradiction](https://github.com/NousResearch/hermes-agent/issues/55038) (docs) | Open since **2026-06-29** | Trivial docs fix, 4+ months old; quick credibility win. |
| [#137652 — compaction deletes user message](https://github.com/NousResearch/hermes-agent/issues/137652) (**P1**) | Open since 2026-10-13, no fix PR | Silent data loss; the single highest-priority backlog item. |
| [#139029 — Kanban `synchronous_rpc_on_event_loop`](https://github.com/NousResearch/hermes-agent/issues/139029) (**P1**) | Open since 2026-10-15, no fix PR | Second P1 without a linked fix; pairs with #145022. |

**Recommendation:** Triage the two **P1 no-fix-PR** items (#137652, #139029) and the ~5-month-old P2 #45106 before v0.24.0 notes ship; the docs issue #55038 is a low-cost close.

---

*Digest generated from GitHub activity snapshot 2026-11-10 → 2026-11-11. PR/issue details reflect the provided top-N sample (30 issues, 20 PRs by comment count); closed-PR details were not individually enumerated in the source data.*

</details>

<details>
<summary><strong>IronClaw</strong> — <a href="https://github.com/nearai/ironclaw">nearai/ironclaw</a></summary>

No activity in the last 24 hours.

</details>

<details>
<summary><strong>QwenPaw</strong> — <a href="https://github.com/agentscope-ai/QwenPaw">agentscope-ai/QwenPaw</a></summary>

# QwenPaw Project Digest — 2026-11-11

## 1. Today's Overview

QwenPaw is in an active beta stabilization phase for the **v2.3.0** line, with a fourth beta (v2.3.0-beta.4) shipped today and a `chore: bump version to 2.3.0` PR already open (#8666), signaling imminent general availability. Activity is high: **39 PRs** touched in 24h (18 merged/closed, 21 open) and **14 issues** updated (5 closed, 9 active). The dominant theme is **channel/plugin runtime reliability on packaged Desktop builds** — three related issues (#8644, #8669, and the closed fix chain #8653/#8659) trace the same failure mode around `CHANNEL_NOT_CONNECTED` / `WORKER_START_FAILED`. A cluster of regressions introduced in the 2.3.0-beta series (attachment path truncation, Mission model-switch failures) is being actively triaged, indicating a healthy but high-tempo pre-release cadence.

## 2. Releases

**v2.3.0-beta.4** — released 2026-11-10 (tracked by release-duty issue #8667)

- **fix(channels): preserve plugin dependencies across updates and fix channel startup** — PR [#8653](https://github.com/agentscope-ai/QwenPaw/pull/8653)
  - Includes the interpreter's supported wheel tags in lightweight dependency evidence, so desktop channel workers validate runtime identity without `WORKER_START_FAILED` (ABI mismatches still rejected).
  - Preserves plugin dependencies across plugin updates and restores channel startup.
- **fix(plugins): recover interrupted additive dependency installations** — PR [#8659](https://github.com/agentscope-ai/QwenPaw/pull/8659)
  - Adds recovery for a repair marker left behind when dependency writes succeed but verification fails, which previously blocked subsequent installs and could prevent startup.

**Notes:** This is a beta; no stated breaking changes or migration steps. However, users on 2.3.0-beta.1–beta.3 should be aware of the **known regression #8655** (attachment paths truncated to drive root, still reproducing on beta.3) — recommend upgrading to beta.4 for the channel/plugin fixes, and tracking #8655 for the attachment fix. Version-bump PRs [#8665](https://github.com/agentscope-ai/QwenPaw/pull/8665) (2.3.0b4, closed) and [#8666](https://github.com/agentscope-ai/QwenPaw/pull/8666) (2.3.0, open) confirm the release train.

## 3. Project Progress

Merged/closed today (highlights):

| PR | Change |
|---|---|
| [#8653](https://github.com/agentscope-ai/QwenPaw/pull/8653) | Channel plugin dependency preservation + startup fix (shipped in beta.4) |
| [#8659](https://github.com/agentscope-ai/QwenPaw/pull/8659) | Plugin dependency install recovery (shipped in beta.4) |
| [#8664](https://github.com/agentscope-ai/QwenPaw/pull/8664) | feat(community): remote password login with saved credentials — enables remote/Docker deployments without local callback browser auth |
| [#8319](https://github.com/agentscope-ai/QwenPaw/pull/8319) | fix(qq): handle `GROUP_MESSAGE_CREATE` gateway events (ordinary group messages previously dropped) |
| [#8665](https://github.com/agentscope-ai/QwenPaw/pull/8665) | Version bump to 2.3.0b4 |

Closed issues resolved: **#8651** (QQ channel dropping image-only C2C messages), **#8406** (QQ channel enabled flag silently reset), **#8534** (Creator TTS incompatibility with Aliyun Token Plan keys), **#8644** (channel plugins not activating on packaged Desktop).

**In-flight major work:** A three-PR stack for local model engines — [#8649](https://github.com/agentscope-ai/QwenPaw/pull/8649) (llama.cpp GPU + auto-update, merge first), [#8647](https://github.com/agentscope-ai/QwenPaw/pull/8647) (managed MLX engine), [#8648](https://github.com/agentscope-ai/QwenPaw/pull/8648) (console controls for local engine/GPU/auto-update) — represents a significant push toward first-class local inference. Also open: [#8426](https://github.com/agentscope-ai/QwenPaw/pull/8426) (extract third-party agent into plugins, XXL) and [#8178](https://github.com/agentscope-ai/QwenPaw/pull/8178) (nested/categorized skill directories).

## 4. Community Hot Topics

- **[#8651](https://github.com/agentscope-ai/QwenPaw/issues/8651)** — QQ channel silently drops image-only C2C messages (4 comments, closed). High engagement because it's a *silent* data-loss failure before any model call; the fix PR referencing #8319's QQ dispatch work is now closed.
- **[#7990](https://github.com/agentscope-ai/QwenPaw/issues/7990)** — Model catalog missing `thinking_param_style` for Aliyun Token Plan models, hiding Console thinking controls (4 comments, open since Sep 25). Reflects demand for **first-class reasoning-control UX** across non-OpenAI providers.
- **[#8406](https://github.com/agentscope-ai/QwenPaw/issues/8406)** — QQ channel enabled flag silently reset to false (3 comments, closed). Another silent-failure complaint — users want explicit warnings on config mutation.
- **[#8669](https://github.com/agentscope-ai/QwenPaw/issues/8669)** — Feishu channel startup race: `_wait_connected` treats initial `disconnected` as fatal → `CHANNEL_NOT_CONNECTED` on every start in packaged Desktop builds (2 comments, open, reported against 2.3.0b4). Follow-up to #8644, indicating the channel startup fix may not fully cover the race condition.
- **[#8644](https://github.com/agentscope-ai/QwenPaw/issues/8644)** — All channel plugins fail to activate on packaged Desktop builds via `validate_runtime_identity` AttributeError (2 comments, closed).

**Underlying need:** The community is heavily invested in **multi-channel deployment (QQ, Feishu, WeChat, DingTalk)** and is reporting a pattern of *silent failures* in channel/plugin initialization. Users want observability — clear warnings when channels fail, drop messages, or configs are reset — rather than quiet degradation.

## 5. Bugs & Stability

Ranked by severity:

1. **🔴 [#8655](https://github.com/agentscope-ai/QwenPaw/issues/8655) — Attachment path truncated to drive root ("C:\") passed to model (regression since 2.3.0-beta.1, still on beta.3).** File saves fine but images fail to load. **No fix PR yet** — highest-priority regression; directly breaks multimodal workflows.
2. **🟠 [#8669](https://github.com/agentscope-ai/QwenPaw/issues/8669) — Feishu channel `CHANNEL_NOT_CONNECTED` on every packaged Desktop start (deterministic).** Open; relates to the #8653/#8659 fix chain but appears to be a residual startup race in `_wait_connected`.
3. **🟠 [#8652](https://github.com/agentscope-ai/QwenPaw/issues/8652) — Mission tasks dispatch with stale model after switching models → task failure.** Open; no fix PR visible.
4. **🟡 [#8661](https://github.com/agentscope-ai/QwenPaw/issues/8661) — `/workspace/` file download links in chat open SPA HTML instead of downloading.** **Fix PR exists:** [#8662](https://github.com/agentscope-ai/QwenPaw/pull/8662) (path-segment encoding + existing auth download flow, with regression tests and docs).
5. **🟡 [#8663](https://github.com/agentscope-ai/QwenPaw/issues/8663) — Safari: message-queue action buttons (edit/interrupt/delete) not rendered; Chrome works.** Open; browser-specific Console regression, no fix PR yet.
6. **🟢 [#8650](https://github.com/agentscope-ai/QwenPaw/issues/8650) — Visual inconsistency between thinking blocks and tool cards; left edge jitters in long tasks.** Open enhancement/polish; no fix PR.

**Fixed today:** #8651 (QQ image-only drop), #8406 (QQ flag reset), #8644 (Desktop channel activation), #8534 (Creator TTS / Token Plan). Net: channel stability is improving, but 2.3.0-beta regressions (#8655, #8652) remain the main 2.3.0 GA blockers.

## 6. Feature Requests & Roadmap Signals

- **Mistral provider plugin** — [#8656](https://github.com/agentscope-ai/QwenPaw/issues/8656) requests inclusion of a Mistral plugin addressing 422 `extra_forbidden` (reasoning_content), chunk-list content rejection by the OpenAI SDK, and `prompt_cache_key` blocked by empty `cache_modes`. Signals demand for **broader provider parity** and plugin-directory curation.
- **Model catalog reasoning metadata** — [#7990](https://github.com/agentscope-ai/QwenPaw/issues/7990) asks for `thinking_param_style` declarations for Aliyun Token Plan models (`reasoning_effort` / `thinking_budget` / `enable_thinking`). Likely to land as a catalog JSON update rather than code.
- **Nested skill directories** — PR [#8178](https://github.com/agentscope-ai/QwenPaw/pull/8178) would support `<root>/<category>/<skill>/SKILL.md`, aligning with other agent ecosystems.
- **Local model management UX** — the #8649/#8647/#8648 stack (llama.cpp GPU, managed MLX, auto-update, Console controls) is the largest roadmap signal: **local inference is becoming a first-class deployment target**.
- **Shell command intent titles** — PR [#8638](https://github.com/agentscope-ai/QwenPaw/pull/8638) adds a display-only `title` to shell cards.
- **Office skills sync** — PR [#8479](https://github.com/agentscope-ai/QwenPaw/pull/8479) syncs DOCX/PPTX/XLSX skills with Anthropic's July 2026 update.

**Prediction for 2.3.0 / 2.3.x:** channel startup hardening, the attachment-path regression fix, Mistral/Token-Plan provider metadata, and (likely post-GA) the local-engine stack. Skill-directory categorization and Office skill sync are plausible 2.4.0 candidates.

## 7. User Feedback Summary

- **Pain points:** Silent failures dominate — image-only QQ messages vanish (#8651), channel enabled flags reset without warning (#8406), Feishu channels fail at startup (#8669), and attachment paths are silently mangled (#8655). Users explicitly call out *lack of warnings/logging* as the aggravating factor.
- **Use cases:** Multi-channel personal assistant deployments (QQ C2C/groups, Feishu tenant bots, DingTalk AI cards, WeChat, Yuanbao), packaged Desktop builds on Windows, remote Docker/Hub deployments (motivating #8664's password login), and long-running Mission tasks with model switching.
- **Satisfaction:** Positive on responsiveness — multiple bug reports closed same-day with linked PRs (#8651, #8406, #8644). **Dissatisfaction** centers on regressions introduced in the 2.3.0-beta line (attachment truncation, stale-model dispatch) and browser-specific Console breakage (Safari queue controls), suggesting QA gaps in the beta channel and cross-browser testing.

## 8. Backlog Watch

- **[#7990](https://github.com/agentscope-ai/QwenPaw/issues/7990)** — Aliyun Token Plan `thinking_param_style` missing (open since **2026-09-25**, 47 days, 4 comments). Small catalog fix with outsized UX impact; needs an owner.
- **[#8178](https://github.com/agentscope-ai/QwenPaw/pull/8178)** — Nested skill directories (open since **2026-10-11**, size/XXL, first-time contributor). Long-running review; risk of contributor attrition.
- **[#8234](https://github.com/agentscope-ai/QwenPaw/pull/8234) / [#8236](https://github.com/agentscope-ai/QwenPaw/pull/8236)** — GBK/CP936 decoding in file tools and Ollama `num_ctx` context sizing (open since **2026-10-14**, both size/XXL). Important correctness fixes for Chinese Windows users and local Ollama users respectively; both under review for ~4 weeks.
- **[#8426](https://github.com/agentscope-ai/QwenPaw/pull/8426)** — Extract third-party agent into plugins (open since **2026-10-26**, size/XXXL). Architecturally significant; needs maintainer direction on merge sequencing.
- **[#8479](https://github.com/agentscope-ai/QwenPaw/pull/8479)** — Office skills sync with Anthropic (open since **2026-10-28**, size/XXXL, first-time contributor). Large diff, under review — worth prioritizing for cross-agent skill compatibility.
- **[#8655](https://github.com/agentscope-ai/QwenPaw/issues/8655)** — Attachment path truncation regression (no fix PR yet). **Highest-priority unassigned item**; should gate 2.3.0 GA.

---

**Health assessment:** Strong velocity and fast issue turnaround, but the 2.3.0-beta series shows a regression rate that warrants a dedicated stabilization pass before GA. Channel/plugin reliability is the clear investment focus and the local-engine stack is the marquee roadmap initiative.

</details>

<details>
<summary><strong>ZeroClaw</strong> — <a href="https://github.com/zeroclaw-labs/zeroclaw">zeroclaw-labs/zeroclaw</a></summary>

# ZeroClaw Project Digest — 2026-11-11

## 1. Today's Overview

ZeroClaw is in an intense pre-release sprint toward **v0.9.0**, with a version-bump PR (#12377) opened today and the v0.9.0 release label appearing across many issues and PRs. Activity is very high: 19 issues and 50 PRs updated in 24h, including 12 issue closures and 9 merged/closed PRs — a healthy close rate that indicates responsive triage rather than backlog accumulation. The day's work was dominated by three themes: **zerocode OIDC/device-certificate authentication hardening**, **docs/SEO infrastructure fixes** (canonical URLs, 404 handling, noindex for the rustdoc tree), and a cluster of **temperature-hardcoding bugs** that break models rejecting the `temperature` parameter. However, the PR queue is heavily skewed toward large, long-running XL changes (several open since August–October), and two CI test-stability issues (intermittent Anthropic fallback test, hanging Telegram tests) signal infrastructure debt that could delay the release. Overall health: **strong throughput, but release-critical stability and review bandwidth are the watch items.**

## 2. Releases

No new releases in the last 24 hours. However, **PR #12377** (`chore(release): bump version to v0.9.0`) was opened, moving the workspace from 0.8.7 to 0.9.0 across `Cargo.toml`, 26 path dependencies, and `Cargo.lock` — the v0.9.0 release is imminent.
- https://github.com/zeroclaw-labs/zeroclaw/pull/12377

## 3. Project Progress

**Merged/closed PRs today (9):**
- **#12317** — feat(zerocode): pairing-free OIDC login and saved remote credentials. A major auth UX improvement: unknown clients can now sign in to a remote daemon without manually copying bearer tokens. (risk:high, size:XL)
  - https://github.com/zeroclaw-labs/zeroclaw/pull/12317
- **#12368** — fix(runtime): stop hard-coding temperature in the heartbeat decision and model-routing probe (closes #12366).
  - https://github.com/zeroclaw-labs/zeroclaw/pull/12368
- **#12328 / #12329 / #12327** — docs infrastructure: stable docs URL that survives releases, root `404.html` for pruned versions, and `noindex` on the rustdoc API tree + error pages.
- **#12364 / #12370** — docs(zerocode): PKCE on every device grant; device certificate end-of-life and JWKS `azp` matching.
- Other smaller doc/config closures supporting the auth and docs workstreams.

**Notable open PRs advancing features:**
- **#12316** — SimpleX messaging channel with voice notes and encrypted calls (opt-in).
- **#11847** — native Rust XMPP with optional legacy OMEMO (open since Oct 19, XL).
- **#12269** — paired-device voice settings and browser dictation/narration in web chat.
- **#12376** — web fix for detached chat turns losing their answer mid-turn.

## 4. Community Hot Topics

- **#7432 [Tracker] Runtime, dashboard sign-in and gateway delivery** — 16 comments, the most-discussed item. The long-running v0.9.0 roadmap tracker owning remaining runtime/gateway gaps from RFC #5574. Its activity confirms v0.9.0 scope is the community's central concern.
  - https://github.com/zeroclaw-labs/zeroclaw/issues/7432
- **#11984 [Feature] Dashboard chat for signed-in non-admin accounts** — closed today after 2 comments; the "sign in but can't chat" gap was a clear friction point for OIDC deployments.
  - https://github.com/zeroclaw-labs/zeroclaw/issues/11984
- **PR #11847 (XMPP/OMEMO)** and **PR #10407 (persistent session prompt attachments, open since Aug 27)** remain the highest-touch open PRs, reflecting sustained demand for broad channel coverage and durable session state.

**Underlying need:** users are pushing ZeroClaw from "single-operator tool" toward **multi-user, multi-channel deployments** (non-admin dashboard users, XMPP/SimpleX/Telegram, paired devices), and authentication/delivery plumbing is the bottleneck.

## 5. Bugs & Stability

Ranked by severity:

1. **S1 — #12354 (closed): zerorelay doesn't flush TLS writes** — relay-served dashboard assets hang until the 300s idle timeout; frontdoor assets arrive late. Workflow-blocking. Fix landed (closed same day).
   - https://github.com/zeroclaw-labs/zeroclaw/issues/12354
2. **S2 — #12366 (closed): heartbeat phase-1 and model-routing probe hard-code temperature 0.0** — breaks providers/models that reject `temperature`. Fix PR **#12368** merged.
   - https://github.com/zeroclaw-labs/zeroclaw/issues/12366
3. **S2 — #12362 (closed): skill review fork hardcodes `temperature: Some(0.3)`** — HTTP 400 on Anthropic-style models rejecting temperature; v0.9.0-labeled.
   - https://github.com/zeroclaw-labs/zeroclaw/issues/12362
4. **S2 — #12357 (closed): zerocode exits at startup for limited (non-admin) accounts** — config/logs panes treat a permission refusal as fatal.
   - https://github.com/zeroclaw-labs/zeroclaw/issues/12357
5. **S2 — #12325 (closed): docs canonical URLs name the release dir** — every stable release moved 1,025 canonical URLs; fixed by #12328.
6. **S3 — #12351 (open): intermittent CI test failure** in Anthropic server fallback test — flaky, no fix PR yet.
   - https://github.com/zeroclaw-labs/zeroclaw/issues/12351
7. **S3 — #12350 (open): Telegram channel tests hang in CI Parallel Runtime Test** until the 30-minute job timeout — no fix PR yet; CI risk.
   - https://github.com/zeroclaw-labs/zeroclaw/issues/12350
8. **S3 — #12361 (open): multi-turn fails with providers requiring `reasoning_content`** in assistant history (Reflection AI / Beam) — `ChatMessage` lacks the field; interoperability gap, no fix PR.
   - https://github.com/zeroclaw-labs/zeroclaw/issues/12361
9. **S3 — #12153 (closed): install.sh picks musl build on glibc hosts** with the musl package installed (Ubuntu 24.04).
10. **S3 — #12347 (closed): zerocode config writers can drop concurrent updates** to `zerocode-config.toml` (read-modify-write race).

**Assessment:** strong same-day fix velocity on the temperature cluster and auth bugs; the two open CI flakiness issues are the main residual stability risk.

## 6. Feature Requests & Roadmap Signals

New requests opened today:
- **#12373 — Write-only WebUI credential management and scoped secret references** (operator-only secrets catalog; author explicitly asks for scope discussion before implementation). Security-conscious, likely post-0.9.0.
  - https://github.com/zeroclaw-labs/zeroclaw/issues/12373
- **#12372 — Git channel `draft_mode`** (placeholder comment instead of streaming progress) — reduces comment spam on Git forges; small, plausible for a near-term minor release.
  - https://github.com/zeroclaw-labs/zeroclaw/issues/12372
- **#12353 — Running estimated cost per conversation** in ZeroCode and web chat, reusing the existing cost ledger. Low-risk UX feature; a plausible 0.9.x candidate.
  - https://github.com/zeroclaw-labs/zeroclaw/issues/12353

**Prediction for v0.9.0:** the release will be dominated by **auth (OIDC/device certs/PKCE), dashboard access for non-admin users, and docs infrastructure** — evidenced by the v0.9.0 labels on #7432, #11984, #12377, #12362, #12368, #12364, #12370. Cost display and Git `draft_mode` are more likely 0.9.x follow-ups.

## 7. User Feedback Summary

- **Pain point — auth friction:** copying bearer tokens for remote daemons was painful; #12317's pairing-free OIDC login directly addresses this, and #11984's "sign in but can't chat" gap frustrated non-admin OIDC users.
- **Pain point — provider compatibility:** multiple reports (#12366, #12362, #12361) show users running non-OpenAI-style providers (Anthropic variants, Reflection AI/Beam) hit hard-coded parameter assumptions — a recurring integration-quality complaint.
- **Use case — team/multi-user deployments:** non-admin dashboard accounts, limited principals in zerocode, and write-only secret management (#12373) indicate adoption beyond single-operator use.
- **Satisfaction signal:** same-day closures of reported bugs and rapid merge of fixes suggest users see responsive maintenance; the volume of well-scoped issue reports (largely from repeat contributors like JordanTheJet) indicates an engaged, technically sophisticated community.
- **Dissatisfaction signal:** CI flakiness (#12351, #12350) and long-lived XL PRs (#10407, #11847, #11869) may frustrate contributors awaiting review.

## 8. Backlog Watch

Long-unanswered or stalled high-value items needing maintainer attention:
- **PR #10407** — persistent session prompt attachments (open since **2026-08-27**, XL, risk:high). Oldest major feature PR; needs review/decision.
  - https://github.com/zeroclaw-labs/zeroclaw/pull/10407
- **PR #11847** — native Rust XMPP/OMEMO (open since **2026-10-19**, XL, needs-maintainer-review).
  - https://github.com/zeroclaw-labs/zeroclaw/pull/11847
- **PR #11869** — opt-in task-owned SSH transport reuse (open since **2026-10-19**, XL).
  - https://github.com/zeroclaw-labs/zeroclaw/pull/11869
- **PR #11911 / #11868** — channel tool-result preservation and repeated-tool-call prevention (open since Oct 19–20, XL, risk:high); #11868 closes #11866 but awaits review.
- **PR #12269** — paired-device voice settings (open since Nov 6, XL).
- **Issue #7432** — v0.9.0 runtime/gateway tracker: release-critical coordination item; continue monitoring for acceptance evidence.

**Bottom line:** ZeroClaw shows high velocity with excellent bug turnaround, but the v0.9.0 release hinges on clearing the open CI flakiness and getting maintainer review bandwidth onto the large, aging PR queue.

---
*Data source: github.com/zeroclaw-labs/zeroclaw — digest generated for 2026-11-11.*

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar).*