# OpenClaw Ecosystem Digest 2026-11-03

> Issues: 391 | PRs: 500 | Projects covered: 5 | Generated: 2026-11-03 10:36 UTC

- [OpenClaw](https://github.com/openclaw/openclaw)
- [Hermes Agent](https://github.com/nousresearch/hermes-agent)
- [IronClaw](https://github.com/nearai/ironclaw)
- [QwenPaw](https://github.com/agentscope-ai/QwenPaw)
- [ZeroClaw](https://github.com/zeroclaw-labs/zeroclaw)

---

## OpenClaw Deep Dive

# OpenClaw Project Digest — 2026-11-03

## 1. Today's Overview

OpenClaw remained highly active over the last 24 hours, with **391 issues updated** (222 open/active, 169 closed) and **500 PRs touched** (306 open, 194 merged/closed), plus a new release (**v2026.10.10**). The trajectory is clearly feature-development-heavy — a large share of today's issue closures are `stale` items being swept rather than fresh fixes, which masks the true fix rate. Stability concerns cluster around a theme: **crash-loop/state-loss failures in session reclamation, native close wedging, and `doctor` hangs**, several tagged P0/P1 and release blockers. Security and privacy signal is also notable (credential leakage in browser CDP results, transcript redaction poisoning, `tools.fs.workspaceOnly` bypass). Overall project health: **high throughput, but elevated P0/P1 backlog depth and dependent on stale-sweeps for closure velocity**.

---

## 2. Releases — v2026.10.10

**Highlights**
- **Backup safety**: secret-redacted Git backups now cover PostgreSQL exports and omit bundled-plugin credentials, webhook tokens, and credential blobs, preserving nonsecret plugin state. (#174698, #186953) — thanks @RomneyDa, @art22s, @jlapenna.
- **Codex**: changelog truncated; no further changes listed in the provided data.

**Breaking changes / migration notes:** None explicitly documented in the provided excerpt. However, note the adjacent upgrade-risk signals from today's issue/PR data:
- **PR #191825** (open) reports `openclaw update` from 2026.10.10 stalling at `"database is locked"` during legacy `exec-approvals.json` migration. This is an upgrade-path risk from this release, not a breaking change per se, but relevant to adopters.
- **Issue #187917** (P2): update failure `updater-runtime-retention` on 2026.10.8 (linux/arm64, Node 26).
- **Issue #169423** (closed, stale): codex plugin migration stuck `unfinished` after 10.1.

**Adoption guidance:** upgrade to 2026.10.10 is reasonable for backup-safety benefits, but pair it with a `openclaw doctor` sanity pass and verify exec-approvals migration on a non-production profile. See also the P1 `doctor`-hang reports in §5.

---

## 3. Project Progress

**Merged/closed PRs today (194 merged/closed; representative items from the provided sample):**
- **PR #191813** [CLOSED] — fix(backup): macOS Git-backup cleanup test failures with `fs-safe` native disabled (P3). Unblocks local `pnpm release:push` gate.
- **PR #191816** [CLOSED] — perf(ci): reduce web-fetch provider boundary check memory retention (P2, proof sufficient).
- **PR #191826** [CLOSED] — docs(testing): keep mock annotations and baseline updates together (XS).

**Closed issues (169 today; sample shows heavy `stale` sweep):**
- **#159356** [CLOSED, stale] — llama.cpp manager/embedding HTTP 500; operational resolution reported (RAM 4 GB → 8 GB).
- **#158966** [CLOSED, stale] — browser credentialed CDP WebSocket URLs reached model-facing tab results (security-relevant, but appears closed via staleness — verify remediation).
- **#169154** [CLOSED] — channel message containing `Authorization: Bearer <token>` failing on transcript idempotency conflict.
- **#161208** [CLOSED, stale] — heartbeats not reusing Anthropic prompt cache (cost impact, ~$0.40–0.95/heartbeat).
- **#156864** [CLOSED, stale] — `tools.alsoAllow: ["browser"]` not granting the browser tool.

**Features advanced (open PRs with sufficient proof, pending maintainer look):**
- **#191715** fix(oc-path): complete multiline frontmatter parsing (diamond lobster).
- **#191791** fix(knowledge): encoded-hash Markdown filenames.
- **#181835** fix(ui): surface bounded-context warning in Control UI channel conversations.
- **#191749** improve(runtime): reduce waits in agent preparation/status reads.

**Merged-feature note:** The sample's "merged/closed PR" set is dominated by XS/S docs, perf, and test hygiene — consistent with a large changelog-driven release already cut (v2026.10.10) and follow-up hardening beginning.

---

## 4. Community Hot Topics

**Issues (by comments):**
1. **[#159356](https://github.com/openclaw/openclaw/issues/159356)** — 30 comments, CLOSED (stale) — llama.cpp manager/embedding readiness mismatch (`HTTP 500 Failed to read connection`). **Need:** reliable readiness/liveness signaling for managed child processes; memory-pressure correlation.
2. **[#177656](https://github.com/openclaw/openclaw/issues/177656)** — 25 comments, OPEN, P1 — Native host exec returns `Direct shared-state reader requires current integrity admission` with no child result, 28+ failures on stable 2026.10.5. **Need:** deterministic error surfaced to callers; no silent shared-state corruption.
3. **[#120385](https://github.com/openclaw/openclaw/issues/120385)** — 24 comments, OPEN, P2 — Code Mode tool catalog incomplete for cron/memory-flush turns → `Unknown tool id`, surfaced only as "Exec failed." **Need:** complete tool catalog for non-interactive turns; actionable error surfaces.
4. **[#166634](https://github.com/openclaw/openclaw/issues/166634)** — 23 comments, OPEN, **P0**, release blocker — Late reclamation checkpoint failure leaves agent unavailable after crash recovery (`SQLite session reclamation commit checkpoint expired`). **Need:** durable recovery after gateway death.
5. **[#178359](https://github.com/openclaw/openclaw/issues/178359)** — 23 comments, OPEN, P1 — `doctor --fix --non-interactive` idles 15+ min with no output (macOS). **Need:** progress/observability in long-running repair paths.

**PRs (highest-signal open):**
- **[#163765](https://github.com/openclaw/openclaw/pull/163765)** — iOS personal Tailscale identity; XL, high merge-risk (security boundary, compatibility). Status: needs proof.
- **[#191749](https://github.com/openclaw/openclaw/pull/191749)** — runtime wait reduction; XL, ready for maintainer look.
- **[#186888](https://github.com/openclaw/openclaw/pull/186888)** — live output for long-running command automations; security-review required.
- **[#163267](https://github.com/openclaw/openclaw/pull/163267)** — same-channel thread recall without agent-wide visibility; compatibility risk, waiting on author.

**Underlying analysis:** The comment-heavy issues are not feature requests — they are **diagnosability problems**. Users repeatedly report "silent success" (health green while agent wedged) and "opaque failures" (generic Exec failed, hangs without output). This suggests the next high-value maintainer investment is **invariant checks + surfaced error codes**, not net-new capabilities.

---

## 5. Bugs & Stability (ranked by severity)

**P0 / release-blocking:**
| Issue | Status | Description |
|---|---|---|
| **[#166634](https://github.com/openclaw/openclaw/issues/166634)** | OPEN, P0, blocker | Late reclamation commit checkpoint failure → agent permanently `UNAVAILABLE` after crash recovery. **No fix PR identified.** |
| **[#169844](https://github.com/openclaw/openclaw/issues/169844)** | OPEN, P0, blocker | Control UI Gateway update on Windows hangs at "Activating"; Scheduled Task left Disabled, gateway stopped. |
| **[#142821](https://github.com/openclaw/openclaw/issues/142821)** | OPEN, P0, security-review | Default-on transcript redaction poisons replayed context (masks propagate into commands/files/replies). Linked PR open. |
| **[#164800](https://github.com/openclaw/openclaw/issues/164800)** | OPEN, P0, fix-shape clear | 2026.9.7→2026.9.8 managed update leaves activation driver dead until host reboot. |

**P1:**
- **[#177656](https://github.com/openclaw/openclaw/issues/177656)** — shared-state integrity-admission error, no child result (stable 2026.10.5).
- **[#178359](https://github.com/openclaw/openclaw/issues/178359)** — `doctor --fix --non-interactive` idle 15+ min.
- **[#187900](https://github.com/openclaw/openclaw/issues/187900)** — `doctor` hangs indefinitely at startup, all modes (Linux, 2026.10.8). **Companion to #178359 — likely common root.**
- **[#144336](https://github.com/openclaw/openclaw/issues/144336)** — SIGUSR1 deferred restart fails shutdown (`service child cleanup identity lost`) — same family as #177656.
- **[#186684](https://github.com/openclaw/openclaw/issues/186684)** — claude-cli tool-list change invalidates native session; history reseed refused → agent restarts with no history.
- **[#145309](https://github.com/openclaw/openclaw/issues/145309)** — claude-cli ignores `CLAUDE_CONFIG_DIR`; missing-transcript resets.
- **[#169844](https://github.com/openclaw/openclaw/issues/169844)** — (also above)

**P2 / regressions of note:**
- **[#182891](https://github.com/openclaw/openclaw/issues/182891)** — prompt cache never reused (`cache read dropped N -> 0`); cost impact.
- **[#154104](https://github.com/openclaw/openclaw/issues/154104)** — idle Gateway with 4 Matrix E2EE accounts: ~50% CPU, ~52 MB/min writes (regression vs 2026.7.1).
- **[#138561](https://github.com/openclaw/openclaw/issues/138561)** — active-memory recall stops after 2026.8.2, silent at info level.
- **[#173058](https://github.com/openclaw/openclaw/issues/173058)** — security: `tools.fs.workspaceOnly` bypass via state root as allowed cwd.
- **[#162585](https://github.com/openclaw/openclaw/issues/162585)** — Windows plugin source-capture staging never converges (290 MB → 612 MB+/24 min).
- **[#137613](https://github.com/openclaw/openclaw/issues/137613)** — pre-compaction memory flush disabled on CLI backends; obvious fix hits `compactionCount` trap.

**Fix-PR status:** Only **#142821** has a linked open PR; **#164800** is tagged fix-shape-clear. **#166634, #169844, #177656, #178359, #187900** appear to have **no upstream fix PR yet** — the cluster is the project's most urgent stability debt.

---

## 6. Feature Requests & Roadmap Signals

**High-signal requests (with linked/open PRs, i.e., likely next-release candidates):**
- **[#155633](https://github.com/openclaw/openclaw/issues/155633)** — Databricks Unity Gateway as official provider. **PR #155634 open, proof sufficient** → plausible near-term.
- **[#128090](https://github.com/openclaw/openclaw/issues/128090)** — rendered Markdown preview in Review files (👍4).
- **[#138279](https://github.com/openclaw/openclaw/issues/138279)** — Linux aarch64 companion builds (deb + AppImage); Windows ARM already ships → packaging symmetry request.
- **[#186888](https://github.com/openclaw/openclaw/pull/186888)** — live output for running command automations (PR open, security review).
- **[#163267](https://github.com/openclaw/openclaw/pull/163267)** — scoped same-channel thread recall.

**Roadmap-shaped requests (no PR yet, product-decision tagged):**
- **[#112372](https://github.com/openclaw/openclaw/issues/112372)** — opt-in `wakeOnReply` for `sessions_send` (deferred, origin-bound completion).
- **[#138990](https://github.com/openclaw/openclaw/issues/138990)** — enforceable checkpoints/bounded yields for long foreground WebChat runs.
- **[#158068](https://github.com/openclaw/openclaw/issues/158068)** — adaptive test-time compute for Swarm populations.
- **[#107433](https://github.com/openclaw/openclaw/issues/107433)** — manifest-backed intentional omissions for `openclaw backup` (platinum hermit, security review).
- **[#158800](https://github.com/openclaw/openclaw/issues/158800)** — browse/fork prior channel segments after `/new` in Control UI.
- **[#136271](https://github.com/openclaw/openclaw/issues/136271)** — msteams dynamic SharePoint site resolution for channel file uploads.

**Prediction:** next patch release likely lands **Databricks provider (#155634), live automation output (#186888), and oc-path/knowledge filename fixes (#191715, #191791)**. Language/locale fixes (#191806, #191823) suggest a localization pass is in flight.

---

## 7. User Feedback Summary

**Pain points (recurring, cross-issue):**
1. **False-green health / silent wedging.** #159789 ("health stays green" while agent locked), #166634 (permanent `UNAVAILABLE`), #177656 (no child result), #120385 (only "Exec failed"). Users cannot distinguish healthy from wedged without log spelunking.
2. **Doctor is a liability.** #178359 and #187900 both report `doctor` hanging with no output across OSes — the intended recovery tool is itself unreliable, compounding incidents.
3. **Upgrade fragility.** #169844, #164800, #187917, #183479, #169423, #157812: Windows Scheduled Task hangs, activation-driver death, retained-package blocks, codex-migration stalls. Multi-hop upgrades (#169423) are a real operational hazard.
4. **Cost & performance regressions.** #182891 and #161208 (prompt-cache non-reuse), #154104 (Matrix E2EE idle burn ~50% CPU / 52 MB/min) — measurable infra cost for operators.
5. **Security surface.** #158966 (CDP credentials to model), #173058 (`workspaceOnly` bypass), #142821 (redaction poisoning) — trust-boundary hygiene is a live concern.

**Use cases visible in data:** multi-agent fleets with heavy channel routing (Telegram/WeChat/Matrix/Slack), long-lived sessions (~310k tokens in #151962), Claude-subscription-driven CLIs (#145309, #186684, #158922), Windows desktop/Scheduled Task deployments (#169844, #162585), and air-gapped/LAN local model servers (llama.cpp, Ollama, llmman — see #175923, #175414, #174877, #174861).

**Satisfaction signals:** strong community contribution on provider breadth (#155634), targeted UI/docs polish PRs (many by @ericcurtin, @vincentkoc), and the 2026.10.10 backup-safety work noted as a highlight. **Dissatisfaction** concentrates on operational reliability and diagnostic opacity — the same themes dominate the top-10 comment counts.

---

## 8. Backlog Watch (needs maintainer attention)

| Item | Age | Why it matters |
|---|---|---|
| **[#142821](https://github.com/openclaw/openclaw/issues/142821)** (P0, security, linked PR) | ~2 mo | P0 security/data-loss with an *open* PR — stale-PR risk despite P0 label. |
| **[#112313](https://github.com/openclaw/openclaw/issues/112313)** (dead-lettered outbound queue, permanent) | ~3.5 mo | Diamond-lobster rating; entries unreadable/undeletable; health counts them forever. No fix. |
| **[#107433](https://github.com/openclaw/openclaw/issues/107433)** (backup omissions, platinum, security review) | ~4 mo | Blocks safe backup/restore workflows; product + security decisions pending. |
| **[#137613](https://github.com/openclaw/openclaw/issues/137613)** (CLI-backend memory flush disabled) | ~2 mo | Diamond-lobster, product-decision tagged; known trap documented but no owner. |
| **[#144797](https://github.com/openclaw/openclaw/issues/144797)** (reused claude-cli session keeps deleted `--mcp-config`, tools 401) | ~2 mo | Stale but reproducible; auth-provider impact. |
| **[#112372](https://github.com/openclaw/openclaw/issues/112372)** (deferred `wakeOnReply`) | ~3.5 mo | Stale, product-decision; underlies async orchestration needs. |
| **[#90074](https://github.com/openclaw/openclaw/issues/90074)** (OpenAI image gen ignores Codex OAuth) | ~5 mo | Very old diamond-lobster; 1 👍. |
| **[#163765](https://github.com/openclaw/openclaw/pull/163765)** (iOS personal identity, XL) — 1 mo | Long-lived XL PR with security-boundary merge risk, "needs proof." |
| **[#155634](https://github.com/openclaw/openclaw/pull/155634)** (Databricks provider) — 6 wks | Feature PR with sufficient proof awaiting maintainer look. |

**Meta-observation:** several security-relevant issues (#158966, #161208) were closed as **stale**, not fixed — recommend a policy review to ensure staleness sweeps don't silently retire unresolved trust-boundary findings.

---

*Data window: 2026-11-02 → 2026-11-03 UTC. Comment counts reflect sample top-50 issues / top-30 PRs. `Comments: undefined` in the PR feed is a data-field gap, not zero activity.*

---

## Cross-Ecosystem Comparison

# Cross-Project Comparison Report — AI Agent / Personal AI Assistant Open-Source Ecosystem
**Data window: 2026-11-02 → 2026-11-03 UTC** | Projects: OpenClaw, Hermes Agent, IronClaw, QwenPaw, ZeroClaw

---

## 1. Ecosystem Overview

The five projects surveyed sit at different maturity points within a single converging category: persistent, multi-channel AI agents with tool execution, memory, and gateway-based orchestration. Activity is sharply bifurcated — OpenClaw, Hermes Agent, QwenPaw, and ZeroClaw are all in **post-migration or pre-release hardening phases** (unified gateway regressions, v0.9.0 gating, 2.3.0 beta stabilization), while IronClaw is effectively dormant with only automated dependency churn. The dominant engineering cost across the ecosystem is no longer model capability but **operational reliability**: session durability, silent-failure diagnostics, and security boundary enforcement. Security auditing by community members is now a routine, line-cited activity in at least three projects, indicating the ecosystem is being deployed as real infrastructure rather than toy assistants. A consistent gap remains between contribution volume and maintainer review/merge capacity.

---

## 2. Activity Comparison

| Project | Issues (touched / open / closed) | PRs (touched / open / merged-closed) | Release Status | Health Score (analyst) | Notes |
|---|---|---|---|---|---|
| **OpenClaw** | 391 / 222 / 169 | 500 / 306 / 194 | **v2026.10.10 shipped** (backup safety) | **6.5 / 10** — high throughput, deep P0/P1 backlog | Closure velocity inflated by stale sweeps; 4 open P0 blockers, several with no fix PR |
| **Hermes Agent** | 50 / ~48 / 12+ | 50 / 38 / 12 | **v0.22.6** rollup (~776 PRs), changelog deferred to v0.23.0 | **7.0 / 10** — stressed but functional | Unified gateway (#106742) fallout dominant; 47-comment triage thread |
| **IronClaw** | 0 / 0 / 0 | 4 / 2 / 2 | None | **5.0 / 10** — stable but dormant | Only dependabot `rustls` bumps; **no human-authored activity in window** |
| **QwenPaw** | 18 / ~12 / 6 | 37 / ~26 / 11 | None (2.3.0-beta.1–b3 in testing) | **7.5 / 10** — strong issue→PR turnaround | Fast same-day fixes; one unaddressed security issue (#8513) |
| **ZeroClaw** | 50 / 48 / 2 | 50 / 40 / 10 | None (`v0.9.0` gating in flight) | **6.0 / 10** — high contribution, review bottleneck | 24:1 open:closed ratio; S1 release-gate bugs open |

*Health score is a qualitative composite of throughput, backlog depth, fix-PR coverage of critical bugs, and release cadence — not a project-endorsed metric.*

**Interpretation:** OpenClaw and ZeroClaw share the same structural problem — bug *discovery* outpaces fix *throughput* and review capacity. QwenPaw has the best closure discipline relative to intake. Hermes Agent has the highest absolute PR volume but is absorbing a large architectural migration. IronClaw's zero-human-activity window is the only true red flag in the set.

---

## 3. OpenClaw's Position

**Advantages vs peers**
- **Largest absolute community and throughput** — 391 issues / 500 PRs touched in 24h is ~8–10× Hermes or ZeroClaw on a per-window basis, and orders of magnitude above QwenPaw.
- **Most mature release discipline** — the only project to ship a versioned release *with documented security-hardening content* (secret-redacted Git backups covering PostgreSQL and plugin credentials) in this window.
- **Broadest provider and channel surface** — Databricks provider PR with sufficient proof (#155634), plus Telegram/WeChat/Matrix/Slack routing already in production use.
- **Explicit security culture at the issue level** — P0 security tags (`transcript redaction poisoning` #142821, `tools.fs.workspaceOnly` bypass #173058) are tracked with severity labels rather than buried.

**Where it is weaker than it looks**
- The **stale-sweep closure pattern is a genuine risk**: two security-relevant issues (#158966 CDP credential leakage, #161208 prompt-cache cost) were closed as *stale*, not fixed. This is a governance debt that peers (notably ZeroClaw's line-cited audit culture) handle better.
- **P0 blockers lack fix PRs** (#166634 permanent `UNAVAILABLE` after crash recovery, #169844 Windows activation hang) — breadth of contributors is not translating to ownership of the hardest items.

**Technical approach differences**
- OpenClaw leans on a **rich plugin/extension model with bundled credentials and a native host-exec path** (`Direct shared-state reader`, integrity admission errors) — heavier machinery than QwenPaw's Python/AgentScope stack or Hermes's gateway-centric design.
- It is the only project in the set shipping a **dedicated backup/restore subsystem with security review** as a first-class release feature — a sign of enterprise-adjacent intent.

**Community size comparison:** OpenClaw ≫ Hermes ≈ ZeroClaw > QwenPaw ≫ IronClaw. The long tail of contributor handles (many drive-by docs/UI PRs) suggests OpenClaw has crossed into broad-contributor territory, whereas Hermes and ZeroClaw still show concentrated, repeat-contributor patterns.

---

## 4. Shared Technical Focus Areas

The following requirements recur across **three or more projects**, making them ecosystem-level rather than project-specific:

| Requirement | Projects | Specific Needs |
|---|---|---|
| **Silent-failure elimination / diagnosability** | OpenClaw (#166634, #177656, #120385), Hermes (#143547), QwenPaw (#8488, #8505, #8549), ZeroClaw (#12014, #12194, #12203) | Fail loudly at startup/config time; surface actionable error codes instead of "Exec failed" or "unknown"; health checks must detect wedged state, not just liveness. |
| **Session/turn durability across transport** | OpenClaw (#166634 crash-recovery), Hermes (#146344 admission_conflict, #145889 gateway wedge), ZeroClaw (#7759 turn/WS decoupling), QwenPaw (#8532 append-only session storage) | Long-running turns must survive reloads, disconnects, restarts; append-only/checkpointed session state is emerging as the convergent design. |
| **Security boundary enforcement of *declared* config** | OpenClaw (#173058 workspaceOnly bypass), ZeroClaw (#9394 pairing_dashboard unread, #12052 XFF spoof), QwenPaw (#8513 ungated shell actions), Hermes (#135415 credential fallback) | Config keys that are accepted but unread; default-on protections that poison context; documented guards that don't run. The theme is **"advertised ≠ enforced."** |
| **Context-window / provider-config correctness** | ZeroClaw (#12014/#12194 silent 32k fallback), QwenPaw (#8496 max_tokens injection, #8236 Ollama num_ctx), OpenClaw (#182891 prompt-cache non-reuse), Hermes (#148380 Anthropic json_mode) | BYO-provider users hit silent wrongness; need validated config and loud failures on unknown models. |
| **Plugin/dependency lifecycle robustness** | OpenClaw (bundled-plugin backups), Hermes (#148337 catalog pinning), QwenPaw (#8265 hot-install, #8553 DEPENDENCY_PLAN_UNVERIFIED, #8554 plugin CLI), ZeroClaw (#11703 tool_search cache bust) | Shared-dependency models create cascading activation failures; plugin loading must be isolated and distro-agnostic. |
| **Windows platform parity** | Hermes (#68128, #144964), QwenPaw (6+ Windows defects today), OpenClaw (#162585, #169844), ZeroClaw (#12126/#12127 UNC credential leaks) | CRLF, encoding (GBK/CP932), PyInstaller assets, UNC paths, Scheduled Tasks — Windows is consistently second-class despite real user bases. |

**Cross-cutting root cause:** Most "instability" reports are actually **observability failures** — the system is doing something (degraded fallback, silent skip, stale config) that the user cannot distinguish from a hang or a bug.

---

## 5. Differentiation Analysis

| Dimension | OpenClaw | Hermes Agent | IronClaw | QwenPaw | ZeroClaw |
|---|---|---|---|---|---|
| **Feature focus** | Full-stack assistant: providers, channels, backup, host-exec | Desktop + bot-orchestration + plugin catalog | Benchmarking/latency harness | Plugin ecosystem + long-session storage + channels | Security-first daemon: pairing, OIDC, A2A, gateway reliability |
| **Target user** | Power operators, multi-agent fleets, self-hosters | Desktop knowledge workers + multi-bot collaboration, i18n users | Internal/benchmark consumers | Technically sophisticated users, Chinese Windows, channel-heavy deployments | Security-conscious deployers, multi-agent topologies behind proxies |
| **Technical stack** | Native host execution, integrity-admission shared state, bundled plugins | Unified-gateway architecture (recently migrated) | Rust (`rustls`, latency runner), automated maintenance | Python/AgentScope, beta desktop builds, SQLite session work | Rust, crates split (`zeroclaw-a2a`), distroless images, OIDC |
| **Release posture** | Versioned releases with security content | Rapid rollup patches | None | Beta-gated stabilization | Security+reliability release gating (`v0.9.0`) |
| **Signature strength** | Breadth + backup safety | Bot Mode group chats + desktop polish | (dormant) | Issue→PR velocity + i18n/channel integrations | Rigorous community security auditing |
| **Signature weakness** | Stale-sweep hides unresolved security | Migration fallout; wedge-forever bug (#145889) | No activity | Security issue #8513 unassigned; Windows defects | 24:1 open:closed; review capacity |

**Architecture divergence note:** Hermes and ZeroClaw both recently centralized on a **gateway-based session admission model** — and both are now paying a regression tax (admission conflicts, profile scope bleed, worker permission denials). OpenClaw uses a **shared-state + integrity-admission** model with a different failure class (checkpoint expiration, identity loss). QwenPaw is converging on **append-only session storage**, which if it lands cleanly may prove the most robust of the three designs — worth watching as a reference implementation.

---

## 6. Community Momentum & Maturity

**Tier 1 — High-momentum, broad-contributor:** **OpenClaw.** Largest absolute activity; production-scale contributor diversity; but momentum is partly *artificial* (stale sweeps) and P0 ownership is thin. Maturity is high on feature surface, medium on reliability.

**Tier 2 — High-momentum, migration-stressed:** **Hermes Agent** and **ZeroClaw.**
- Hermes shows strong maintainer throughput and a highly engaged community functioning as a QA layer (47-comment triage). It is **stabilizing after an architectural migration** — v0.23.0 will be a stabilization release.
- ZeroClaw is **rapidly iterating toward a security+reliability v0.9.0** but is review-capacity-bound. Its community is the most security-literate in the set.

**Tier 2b — High-momentum, disciplined closure:** **QwenPaw.** Lower absolute volume than Tier 1/2, but the best issue-to-PR turnaround and cleanest closure ratio. In an **active beta stabilization** — most "bugs" are beta-testing surface, not architectural rot. Fastest path from report to fix in the cohort.

**Tier 3 — Dormant:** **IronClaw.** Zero human-authored activity; only automated dependency bumps. Stable and low-risk on security, but **cannot be assessed as actively maintained** from this window — monitor for return of human PRs before relying on it.

**Maturity framing:** Features-mature but reliability-immature (OpenClaw, Hermes, ZeroClaw) is the common pattern; QwenPaw is closest to balanced; IronClaw is maintenance-only. **No project in the set has reached "reliability-mature."**

---

## 7. Trend Signals (for AI Agent Developers)

Extracted from cross-project community feedback, ranked by signal strength:

1. **Reliability is the new differentiator — diagnostic opacity is the top complaint.** Across all four active projects, the highest-engagement threads are diagnosability problems, not feature requests. *Actionable:* invest in **invariant checks + surfaced error codes + progress observability** (especially in long-running repair/doctor paths) before adding capabilities. "False-green health" is a trust-killer.

2. **Session/turn durability is the architectural battleground.** Multiple projects are independently converging on **append-only, checkpointed, transport-decoupled session state** (ZeroClaw #7759, QwenPaw #8532, OpenClaw crash-recovery #166634). *Actionable:* design for turns that survive reload/restart/disconnect from day one; treat WS/socket lifetime as ephemeral.

3. **"Advertised ≠ enforced" is the dominant security failure mode.** Config keys accepted but unread (ZeroClaw), default-on protections that poison data (OpenClaw), declared guards that don't gate (QwenPaw). *Actionable:* **validate and enforce config at startup, fail loudly on unknown/unread keys, and audit that security switches actually run.** Community security auditing (line-cited, reproducible) is now expected — build for it.

4. **BYO-provider correctness is a silent-data-loss class.** Unknown models and missing context-window config silently fall back to wrong defaults (ZeroClaw 32k, QwenPaw max_tokens injection, OpenClaw cache non-reuse). *Actionable:* **loud validation on model/provider config** and explicit context-window contracts; do not assume.

5. **Cost observability is a first-class user need.** Prompt-cache non-reuse, idle CPU/GPU burn (Hermes desktop 25–45%, OpenClaw Matrix E2EE ~50% CPU), heartbeat cache misses — users measure and resent infrastructure cost. *Actionable:* **surface cache-hit rates, idle resource usage, and per-turn cost**; treat cache stability as a feature (e.g., deferred MCP loading that busts cache, ZeroClaw #11703).

6. **Plugin/dependency isolation is an ecosystem-health prerequisite.** Shared-dependency models cause cascading activation failures; catalog pinning and plugin CLI/extension points are emerging. *Actionable:* **isolate plugin dependency environments, pin catalog entries, and expose resource lifecycle hooks** — the plugin ecosystem is the moat, and fragility there blocks third-party growth.

7. **Windows and i18n are real, underserved segments.** Consistent Windows-specific defect clusters and explicit non-English deployment demand (Hermes 17-language parity, QwenPaw Chinese-Windows data-loss fixes). *Actionable:* **treat Windows and localization as first-class test targets**, not afterthoughts — encoding (GBK/CP932/CRLF), path handling (UNC, Chinese paths), and packaging completeness.

8. **Maintainer review capacity, not contribution volume, is the ecosystem's bottleneck.** OpenClaw's unowned P0s, ZeroClaw's stalled S1 fix PRs, QwenPaw's competing duplicate CRLF PRs all point the same way. *Actionable for developers:* file reproducible, line-cited reports (the ones getting fast turnaround share this trait); for maintainers: **explicit triage SLAs on security-labeled issues** and consolidation of duplicate fix efforts.

---

**Bottom line for decision-makers:** OpenClaw is the broadest and most production-ready on feature surface but carries unresolved reliability and stale-sweep security debt — adopt with a `doctor`-first upgrade discipline and independent verification of "closed-as-stale" security items. Hermes and ZeroClaw are stabilizing toward reliability releases and are the best bets for teams that prioritize security rigor and durable sessions, provided you can tolerate migration-phase friction. QwenPaw offers the best closure discipline and fastest fix cycles for channel/plugin-heavy and Chinese-Windows deployments. IronClaw should be treated as dormant until human-authored activity resumes. Across all: **budget for observability and session durability as core engineering, not polish.**

---

## Peer Project Reports

<details>
<summary><strong>Hermes Agent</strong> — <a href="https://github.com/nousresearch/hermes-agent">nousresearch/hermes-agent</a></summary>

# Hermes Agent — Project Digest
**Date: 2026-11-03** | Repo: [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent)

---

## 1. Today's Overview

Hermes Agent is running at high velocity: 50 issues and 50 PRs were touched in the last 24 hours, alongside a patch release (v0.22.6) that rolls up roughly 776 merged PRs since v0.22.5. Activity is dominated by the aftermath of the **unified gateway migration (#106742)**, which continues to generate post-merge regressions across desktop, sessions, cron, and multi-profile setups (see #143547 with 47 comments). The issue mix skews toward *stability and platform-specific bugs* (Windows, WSL2, macOS desktop) rather than new feature work, though several feature PRs landed today for Bot Mode group chats. Overall project health is **strong but stressed** — maintainer throughput is high, but the unified-gateway surface area is producing a long tail of session-state and permission bugs that will likely dominate v0.23.0's stabilization effort.

---

## 2. Releases

### v0.22.6 — Patch Release (2026-11-02)
**Type:** Patch / rollup release (stable channel: CLI, installers, Microsoft Store, Docker, Hermes Cloud).

> "Patch release. This tag rolls up the ~776 PRs merged since v0.22.5 into a stable release... Full curated notes for this window ship with v0.23.0."

**Notes:**
- **No breaking changes announced** in this release; it is explicitly a stabilization rollup.
- **Migration notes:** Curated changelog is deferred to v0.23.0. Users on the stable channel pulling v0.22.6 should still be aware that **v0.22.5 → v0.22.6 spans the unified-gateway migration window**, so desktop users with pre-migration sessions may hit the legacy-session issues below (#146344, #143547).
- **Implication for users:** The sheer PR volume (~776) rolled into a patch bracket reinforces that v0.22.x is a *stabilization line* ahead of the v0.23.0 feature drop.

🔗 [Release v0.22.6](https://github.com/NousResearch/hermes-agent/releases/tag/v0.22.6)

---

## 3. Project Progress

**12 PRs merged/closed** in the last 24h (38 remain open). Notable closed items and active workstreams:

- **#68927 [CLOSED, P1]** — Desktop: after long tasks, Enter reaches backend but user bubble not rendered `comp/desktop, area/sessions`. A high-priority session-state/UI desync bug got closed. 🔗 [#68927](https://github.com/NousResearch/hermes-agent/issues/68927)
- **#48411 [CLOSED]** — `uv.exe` flagged as malware (Bitdefender/Defender false positive). Resolved on main. 🔗 [#48411](https://github.com/NousResearch/hermes-agent/issues/48411)
- **Bot Mode group chats (active PRs):**
  - **#148422** — Group rooms now look like native chat and show each bot's live working/queued/passed state. 🔗 [#148422](https://github.com/NousResearch/hermes-agent/pull/148422)
  - **#148418** — Cross-machine bot seating restored (mixed-machine group chats). 🔗 [#148418](https://github.com/NousResearch/hermes-agent/pull/148418)
  - **#148436** — Relaunch no longer pins "gateway not connected" in Bots pane. 🔗 [#148436](https://github.com/NousResearch/hermes-agent/pull/148436)
- **Hosted Group Chat authority handoff (fix):** **#148438** — rooms stay serviceable across authority handoffs (fixes `member.unavailable / authority epoch mismatch`). 🔗 [#148438](https://github.com/NousResearch/hermes-agent/pull/148438)
- **Plugin catalog maintenance:** **#148337** updates `tirith` 0.1.0 → 0.1.1 (SHA-pinned, human-reviewed policy). 🔗 [#148337](https://github.com/NousResearch/hermes-agent/pull/148337)

**Theme:** The bulk of merged/open work is **incremental correctness fixes** (permissions messaging, install-stamp handling, heredoc approval parsing, reasoning-timeout matching) rather than net-new features — consistent with a post-migration hardening phase.

---

## 4. Community Hot Topics

Ranked by comment activity (last 24h):

| Rank | Item | Comments | Signal |
|---|---|---|---|
| 1 | **[#143547](https://github.com/NousResearch/hermes-agent/issues/143547)** — Unified gateway post-merge problems on main | **47** | Highest-engagement issue; the community is actively triaging regressions from the #106742 merge across CLI/TUI/Desktop/API/bots/cron. |
| 2 | **[#127647](https://github.com/NousResearch/hermes-agent/issues/127647)** — Desktop idle resource burn tracker (CPU/GPU/memory) | **35** | Sustained concern about desktop performance; scope-mapped tracker with linked PRs. |
| 3 | **[#68128](https://github.com/NousResearch/hermes-agent/issues/68128)** — WhatsApp bridge `WinError 5` on Windows no-breakaway job objects | **18** | Long-running Windows platform bug; low reaction but persistent. |
| 4 | **[#68927](https://github.com/NousResearch/hermes-agent/issues/68927)** — Desktop long-task Enter/bubble desync *(CLOSED)* | **15** | Resolved; strong community pressure paid off. |
| 5 | **[#52554](https://github.com/NousResearch/hermes-agent/issues/52554)** — Floating "Quote" button on selected text | **9** (👍4) | Highest-reaction feature request; UX polish for chat quoting. |

**Underlying needs analysis:**
- **#143547** reveals the community is functioning as a *de facto QA layer* for the unified gateway — users are collating cross-component breakage in one thread. This is a sign the migration shipped with insufficient pre-release coverage across profiles/cron/sessions.
- **#127647** and **#52554** indicate desktop users want **performance predictability** and **native-feeling chat UX** — the desktop is being used as a primary client, not a secondary one.

---

## 5. Bugs & Stability

Ranked by severity (P1 → P3). *Fix PRs noted where identifiable.*

### 🔴 P1
- **#68927 [CLOSED]** — Desktop long-task Enter submits to backend but user bubble never renders; text stays in composer. *Closed today.* 🔗 [#68927](https://github.com/NousResearch/hermes-agent/issues/68927)

### 🟠 P2 — Session-state & gateway regressions (highest concentration)
- **#143547** — Unified gateway post-merge problems (meta-issue, 47 comments). 🔗 [#143547](https://github.com/NousResearch/hermes-agent/issues/143547)
- **#142032 [WSL2]** — Every managed worker (kanban/cron/delegation) refused with sticky `permission_denied` after 9–65s due to `btime` re-anchor on exact `create_time` equality. **No fix PR linked.** 🔗 [#142032](https://github.com/NousResearch/hermes-agent/issues/142032)
- **#87575** — Profile scope bleed: a cron job for one profile makes another profile's live turn resolve `HERMES_HOME` incorrectly. 🔗 [#87575](https://github.com/NousResearch/hermes-agent/issues/87575)
- **#146344** — Legacy desktop-lane sessions: `admission_conflict` after every gateway restart; only focused tab recovers; queued turns hang. 🔗 [#146344](https://github.com/NousResearch/hermes-agent/issues/146344)
- **#145889** — Memory provider `initialize()` on turn critical path can wedge gateway forever; process unkillable while holding `gateway.lock` (requires reboot). **Severe.** 🔗 [#145889](https://github.com/NousResearch/hermes-agent/issues/145889)
- **#135415** — Anthropic token resolver falls back to Claude Code credentials even when `claude_code` suppressed (security boundary). 🔗 [#135415](https://github.com/NousResearch/hermes-agent/issues/135415)
- **#75772** — File-mutation verifier footer appended after `[SILENT]` defeats cron delivery suppression. 🔗 [#75772](https://github.com/NousResearch/hermes-agent/issues/75772)
- **#144964 [Windows]** — `--safe-mode` with `openai-codex` hangs forever (orphaned git in `version_info._run_git`). 🔗 [#144964](https://github.com/NousResearch/hermes-agent/issues/144964)

### 🟡 P3 — New today (filed 2026-11-03)
- **#148437** — `cron remove` silently resurrected by stale writer flush (shrink-merge guard is create-only). 🔗 [#148437](https://github.com/NousResearch/hermes-agent/issues/148437)
- **#148434** — Deleted cron job `workdir` fails every run with `invalid_params` at gateway admission. 🔗 [#148434](https://github.com/NousResearch/hermes-agent/issues/148434)
- **#148433** — Shutdown during redirected turn overwrites already-streamed reply with wait sentinel. 🔗 [#148433](https://github.com/NousResearch/hermes-agent/issues/148433)
- **#148430** — Checkpoints `DEFAULT_EXCLUDES` misses image/PDF binaries; snapshots balloon to 2+ GB. 🔗 [#148430](https://github.com/NousResearch/hermes-agent/issues/148430)
- **#148380** — Plugin `json_mode` on Anthropic always 400s (`additionalProperties` missing). 🔗 [#148380](https://github.com/NousResearch/hermes-agent/issues/148380)
- **#148432** — Catalog pins `hindsight` at commit lacking Windows cp932 encoding fix. 🔗 [#148432](https://github.com/NousResearch/hermes-agent/issues/148432)
- **#144879** — `optimize-storage` never completes when `messages.id` values are large. 🔗 [#144879](https://github.com/NousResearch/hermes-agent/issues/144879)

**Fix PRs in flight (open):**
- **#133597** — isolate adapter construction failures (webhook/gateway startup). 🔗 [#133597](https://github.com/NousResearch/hermes-agent/pull/133597)
- **#106221** — keep Settings/session mutations on owner gateway. 🔗 [#106221](https://github.com/NousResearch/hermes-agent/pull/106221)
- **#135880** — gateway lock dir follows real home (container fix). 🔗 [#135880](https://github.com/NousResearch/hermes-agent/pull/135880)
- **#138095** — denied reads report `Permission denied` not `File not found`. 🔗 [#138095](https://github.com/NousResearch/hermes-agent/pull/138095)
- **#136550** — detached starts no longer treated as long-lived foreground. 🔗 [#136550](https://github.com/NousResearch/hermes-agent/pull/136550)
- **#135548** — data-only heredocs no longer trip hardline floor. 🔗 [#135548](https://github.com/NousResearch/hermes-agent/pull/135548)

**Stability verdict:** The **unified gateway is the dominant risk vector** — session admission, profile scoping, worker permissions, and lock handling all trace back to it. The wedge-forever bug (#145889) is the most operationally serious open item.

---

## 6. Feature Requests & Roadmap Signals

| Issue | Ask | Likelihood for next version |
|---|---|---|
| **[#52554](https://github.com/NousResearch/hermes-agent/issues/52554)** (👍4) | Floating "Quote" button on selected chat text | **Medium** — small UX scope, high visibility; good candidate for a desktop polish release. |
| **[#148435](https://github.com/NousResearch/hermes-agent/issues/148435)** | Full-window/full-screen preview for PDF/DOCX/HTML + Pop Out tabs | **Medium** — non-developer document workflow is a named growth segment. |
| **[#148306](https://github.com/NousResearch/hermes-agent/issues/148306)** | Agent-loop hook for guarded decision reuse (skip already-decided model callbacks) | **Low–Medium** — plugin-ecosystem enabler, requires API design. |
| **[#148341](https://github.com/NousResearch/hermes-agent/issues/148341)** | Mobile beta i18n — ship the same 17-language UI as Desktop/web | **High** — parity gap is embarrassing; localization infra already exists. |
| **[#141738](https://github.com/NousResearch/hermes-agent/issues/141738)** | Vision prompts hard-coded in English (one nudges Chinese output); make configurable | **High** — clear bug-like i18n defect with trivial fix surface. |
| **[#135166](https://github.com/NousResearch/hermes-agent/issues/135166)** | Plugin catalog Rule 16 clarification (second entry from same repo) | **Low** — policy question, not roadmap. |

**Prediction:** v0.23.0 will be **primarily the unified-gateway stabilization release**, with i18n fixes (#141738, #148341) and Bot Mode group-chat parity shipped alongside. Larger feature hooks (#148306) likely slip to v0.24+.

---

## 7. User Feedback Summary

**Pain points (dissatisfaction):**
- **Desktop performance:** GPU engine at 25–45% while streaming on high-DPI Windows (#145979), and idle resource burn tracker (#127647) — users resent desktop overhead vs. TUI (0.1%).
- **Gateway migration fallout:** queued turns hanging, `admission_conflict` after restarts, wedged processes requiring reboot (#145889) — erosion of trust in gateway reliability.
- **Cross-connection relay:** Bot DMs on SSH/legacy dashboard routes silently never arrive (#146254).
- **Platform-specific friction:** WinError 5 job objects (#68128), WSL2 permission denials (#142032), `--safe-mode` hangs (#144964), in-app browser MFA failures (#119360).
- **iOS beta link broken:** Official TestFlight invite returns "Beta Not Found" (#147556, 👍2) — onboarding blocker.

**Use cases (satisfaction signals):**
- **Non-developer document workflows:** Desktop used to produce client-ready PDFs/DOCX/HTML (#148435) — a healthy expansion beyond devs.
- **Multi-bot collaboration:** users building group chats across machines (#148418/#148422) — Bot Mode is being adopted for real orchestration.
- **i18n demand** from non-English deployments (#141738, #148341) — international user base is real and vocal.
- **Positive signal:** 47-comment crowdsourced triage (#143547) shows an engaged, invested community willing to co-debug — a strong retention indicator despite friction.

---

## 8. Backlog Watch

Long-unanswered or high-impact items needing maintainer attention:

| Item | Age / Status | Why it matters |
|---|---|---|
| **[#68128](https://github.com/NousResearch/hermes-agent/issues/68128)** | Open since **2026-07-20** (~3.5 months), P3 | Windows WhatsApp bridge `WinError 5` — recurring platform-family issue; documented fallback exists but unimplemented. |
| **[#87575](https://github.com/NousResearch/hermes-agent/issues/87575)** | Open since **2026-08-16**, P2 | Multiplexed profile scope bleed — silent data-routing error, high blast radius, no fix PR. |
| **[#127647](https://github.com/NousResearch/hermes-agent/issues/127647)** | Tracker since **2026-09-29**, P2 | Desktop idle resource burn — tracker well-scoped (35 comments) but no consolidated fix. |
| **[#75772](https://github.com/NousResearch/hermes-agent/issues/75772)** | Open since **2026-08-01**, P2 | Cron `[SILENT]` suppression defeated — silent message leakage to messaging platforms. |
| **[#146344](https://github.com/NousResearch/hermes-agent/issues/146344)** | Open 2026-10-29, P2 | Legacy desktop sessions after restart — affects migration cohort; no fix PR linked. |
| **[#106221](https://github.com/NousResearch/hermes-agent/pull/106221)** | Open PR since **2026-09-09** (~2 months) | Desktop cross-owner Settings/session mutations — long-running fix, needs review/merge push. |
| **[#133597](https://github.com/NousResearch/hermes-agent/pull/133597)** | Open PR since **2026-10-06** | Gateway adapter isolation — salvaged fix stalled in review. |

**Maintainer call-to-action:** The **profile-scope bleed (#87575)** and **cron silent-suppression (#75772)** are silent-failure classes — they don't crash, they lose or leak data — and deserve prioritization over the visible-but-loud desktop UI bugs. The long-open PRs **#106221** and **#133597** are blocking related gateway/session fixes and would benefit from explicit triage.

---

*Digest generated from GitHub activity snapshot for 2026-11-03. All links point to NousResearch/hermes-agent.*

</details>

<details>
<summary><strong>IronClaw</strong> — <a href="https://github.com/nearai/ironclaw">nearai/ironclaw</a></summary>

# IronClaw Project Digest — 2026-11-03

## 1. Today's Overview
IronClaw shows a quiet, maintenance-only activity profile for the period: zero issues touched, zero new releases, and only automated dependency PRs in the pipeline. Four PRs were updated in the last 24h — two newly opened dependabot bumps of `rustls` (0.23.43 → 0.23.46) and two older `rustls` bumps (to 0.23.45) that were closed/superseded. There is no feature development, bug-fixing, or community discussion visible in this window. Overall project health appears stable but dormant from a development standpoint, with activity dominated entirely by supply-chain hygiene.

## 2. Releases
No new releases were published in this period.

## 3. Project Progress
No merged/closed PRs advancing features or fixes were identified today. The two closed PRs (#8151, #8152) were dependency bumps to `rustls` 0.23.45 that were superseded in favor of the 0.23.46 updates (#8165, #8166), representing routine dependency churn rather than functional progress. Net effect: the project's Rust TLS dependency is tracking toward 0.23.46 across both the root crate and the `/harness/latency/runner` sub-crate.

## 4. Community Hot Topics
No Issues or PRs recorded comments or reactions in this window — the most "active" items are the four dependabot PRs, all with zero 👍 and no discussion:
- [PR #8165](https://github.com/nearai/ironclaw/pull/8165) — bump `rustls` to 0.23.46 (root)
- [PR #8166](https://github.com/nearai/ironclaw/pull/8166) — bump `rustls` to 0.23.46 (`/harness/latency/runner`)
- [PR #8151](https://github.com/nearai/ironclaw/pull/8151) — closed, superseded bump to 0.23.45 (runner)
- [PR #8152](https://github.com/nearai/ironclaw/pull/8152) — closed, superseded bump to 0.23.45 (root)

Underlying signal: there is no user-facing demand or debate captured today; the only "topic" is automated dependency maintenance, which suggests either a mature/stable phase or low community engagement in this window.

## 5. Bugs & Stability
No bugs, crashes, or regressions were reported today. The `rustls` upgrades are security/robustness-relevant (TLS handshake handling — e.g., the "Delay saving KX hint until handshake confirmation" commit), with fix PRs already open (#8165, #8166). Severity: informational; no known vulnerability or outage is implied by the data.

## 6. Feature Requests & Roadmap Signals
No feature requests were filed in this window. The only forward-looking signal is continued maintenance of the `/harness/latency/runner` component, implying ongoing investment in latency benchmarking infrastructure. No roadmap items can be confidently predicted from today's data.

## 7. User Feedback Summary
No user feedback, pain points, or use-case reports appeared in Issues or PR comments today. Satisfaction/dissatisfaction cannot be assessed from this window; the absence of open issues may indicate a healthy stable state or simply low reporting volume.

## 8. Backlog Watch
No long-unanswered Issues are present (total issues: 0). The only items meriting light maintainer attention are the two open dependabot PRs awaiting review/merge:
- [PR #8165](https://github.com/nearai/ironclaw/pull/8165) (opened 2026-11-02)
- [PR #8166](https://github.com/nearai/ironclaw/pull/8166) (opened 2026-11-02)

Both are low-risk, one-day-old automated bumps; merging them promptly keeps the TLS dependency current and avoids duplicate-version drift between root and the latency runner.

---
**Health assessment:** Stable and low-risk, but development activity is essentially zero beyond automated dependency management. Recommend monitoring for a return of human-authored PRs/issues to confirm the project isn't stalling.

</details>

<details>
<summary><strong>QwenPaw</strong> — <a href="https://github.com/agentscope-ai/QwenPaw">agentscope-ai/QwenPaw</a></summary>

# QwenPaw Project Digest — 2026-11-03

## 1. Today's Overview

QwenPaw shows **high-velocity development activity** with 18 issues and 37 PRs updated in the last 24 hours, indicating an engaged contributor base and active triage cycle. The project is currently in a **2.3.0 beta stabilization phase** (v2.3.0-beta.1 through b3 referenced throughout), with numerous bug reports surfacing from beta testing—particularly around desktop builds, plugin dependency resolution, and channel integrations. Eleven PRs were merged or closed today, including fixes for the visual compression asset bundling issue (#8514, #8488) and CRLF/encoding preservation in file tools. However, **no new releases** were published, and several high-severity issues remain open, including a security governance gap (#8513) and a plugin activation blocker (#8553). Overall project health is **good but watchful**—the rapid issue-to-PR turnaround on most bugs suggests responsive maintainership, though the beta testing surface is exposing integration fragility across platform-specific paths.

---

## 2. Releases

**None.** No new releases were published in the last 24 hours.

---

## 3. Project Progress

**Merged/Closed PRs today (11 total):**

- **#8514 [CLOSED]** — `fix(desktop): bundle visual_compression glyph atlas assets` — Fixes the PyInstaller build omission where `agents/context/visual_compression/assets` was not in `_data_dirs`, causing `FileNotFoundError` on desktop builds. Directly resolves Issue #8488. [Link](https://github.com/agentscope-ai/QwenPaw/pull/8514)

- **#8556 [CLOSED]** — `fix(console): select decision-model approval directly` — Simplifies the decision-model tool approval UX by removing the redundant "Use decision model" switch; selecting approval mode is now the sole opt-in. [Link](https://github.com/agentscope-ai/QwenPaw/pull/8556)

- **#8463 [CLOSED]** — `feat(models): run local Piccolo with bidirectional candidate scoring` — Enables downloaded Piccolo models to supply native Jev decisions for automatic tool review and ReMe context compaction, using bidirectional prompt attention rather than a causal Chat Completions adapter. [Link](https://github.com/agentscope-ai/QwenPaw/pull/8463)

- **#8496 [CLOSED]** — `fix(context): support slow summary models and consistent token limits` — Resolves HTTP 400 errors on OpenAI-compatible providers when model cards set `generate_kwargs.max_tokens` (AgentScope 2.0.10 injects `max_completion_tokens` while Scroll also passes `max_tokens`). [Link](https://github.com/agentscope-ai/QwenPaw/pull/8496)

- **#8552 [CLOSED]** — `fix(tests): reopen backup archive before Windows restore verification` — Test-only fix for Windows ZIP handle path separator mismatch (`BadZipFile`). [Link](https://github.com/agentscope-ai/QwenPaw/pull/8552)

**Also closed as issues (6 today):** #8265 (plugin hot-install abort on `importlib.invalidate_caches()`), #8488 (desktop visual compression assets), #4312 (agent.json hot-reload scope), #8013 (skill pool download timeout), #8528 (Markdown preview relative path rewrite), #8540 (scroll history startup sync).

**Key advancement:** The `edit_file` CRLF/encoding preservation fix has **two competing open PRs** (#8235 and #8433), addressing Issues #8414 and #8415—this is an active hot-fix zone for Windows users.

---

## 4. Community Hot Topics

| Item | Comments | Link | Underlying Need |
|------|----------|------|-----------------|
| **#8265** Plugin hot-install aborts (`importlib.invalidate_caches` raises) | 5 | [Issue](https://github.com/agentscope-ai/QwenPaw/issues/8265) | **Plugin ecosystem reliability**—users install third-party plugins and need robust, distro-agnostic loading. This was the most-discussed issue, now closed. |
| **#8488** Desktop build omits visual_compression assets | 3 | [Issue](https://github.com/agentscope-ai/QwenPaw/issues/8488) | **Packaging completeness**—desktop users expect feature parity with source builds; missing assets silently break Visual Compact. |
| **#8505** Gateway bare `APIError` classified as `unknown` (no retry/fallback) | 3 | [Issue](https://github.com/agentscope-ai/QwenPaw/issues/8505) | **Resilience against OpenAI-compatible relays**—users route through Chinese gateways that emit non-standard error shapes; model fallback is critical. |
| **#8553** QQ channel plugin `DEPENDENCY_PLAN_UNVERIFIED` (aliyun-hub) | 2 | [Issue](https://github.com/agentscope-ai/QwenPaw/issues/8553) | **Shared dependency isolation**—hub plugins without dependency environments block downstream consumers; activation audit is too strict. |
| **#8511** Feishu channel intermittent `No active model configured` | 2 | [Issue](https://github.com/agentscope-ai/QwenPaw/issues/8511) | **Concurrency correctness** in channel message handling—race between inbound message and in-flight agent processing. |
| **#8532** `feat(chat): append-only session storage, paginated history, native Turn forks` | — | [PR](https://github.com/agentscope-ai/QwenPaw/pull/8532) | **Long-conversation scalability**—durable per-session SQLite with append-only facts replacing mutable snapshots. XXXL-sized, under review. |
| **#8554** `feat(plugins): CLI commands and extension points` | — | [PR](https://github.com/agentscope-ai/QwenPaw/pull/8554) | **Plugin developer ergonomics**—Click command registration, dependency checks, resource lifecycle hooks. |

**Analysis:** The community is pushing on **three fronts simultaneously**: (1) plugin/channel integration reliability, (2) desktop distribution parity, and (3) long-session durability. The PR #8532 (append-only session storage) and #8554 (plugin CLI) are the highest-leverage architectural changes in flight.

---

## 5. Bugs & Stability

**Ranked by severity:**

🔴 **Critical — Security**

- **#8513 [OPEN]** — *Shell-wrapped outbound actions execute with no findings or approval at AUTO/SMART* — Detector gap in `tool_guard` AND governance lanes; SMTP send / HTTP POST / `@file-to-network` all pass ungated. This is an **exfiltration risk** under `approval_level: AUTO`. No fix PR yet. [Issue](https://github.com/agentscope-ai/QwenPaw/issues/8513)

🟠 **High — Data Loss / Corruption**

- **#8414 [OPEN]** — *`edit_file` converts all CRLF to LF on targeted edit* — Rewrites entire file newline style; Windows users lose line-ending contract. **Fix PRs exist: #8235, #8433** (both under review, competing). [Issue](https://github.com/agentscope-ai/QwenPaw/issues/8414)

- **#8549 [OPEN]** — *Model config write path bypasses field validation* — Invalid `thinking_budget`/`max_input_length` persisted; **provider silently disappears after restart**. Fix PR **#8550** open (first-time contributor). [Issue](https://github.com/agentscope-ai/QwenPaw/issues/8549)

🟠 **High — Instability**

- **#8505 [OPEN]** — *Gateway bare `APIError` classified as `unknown`* — No retry, no model fallback on HTTP 502 relays. No fix PR. [Issue](https://github.com/agentscope-ai/QwenPaw/issues/8505)

- **#8553 [OPEN]** — *QQ channel `DEPENDENCY_PLAN_UNVERIFIED`* — Activation aborts when hub plugin (aliyun-hub) has no dependency environment. PR **#8527** (dependency state root reporting) is related but not a direct fix. [Issue](https://github.com/agentscope-ai/QwenPaw/issues/8553)

- **#8511 [OPEN]** — *Feishu channel `No active model configured` during concurrent processing* — Reproduced on stable 2.2.2, not beta-introduced. Race condition. [Issue](https://github.com/agentscope-ai/QwenPaw/issues/8511)

🟡 **Medium — Platform/UX**

- **#8555 [OPEN]** — *New task workspace selection reverts to default in subsequent messages* (v2.3.0b3) [Issue](https://github.com/agentscope-ai/QwenPaw/issues/8555)
- **#8517 [OPEN]** — *Hub Docker runtime Permission denied on `working/config.json` as non-root* [Issue](https://github.com/agentscope-ai/QwenPaw/issues/8517)
- **#8502 [OPEN]** — *Desktop HTML preview "Open in browser" fails on Chinese paths / out-of-workspace files* — Fix PR **#8524** open (decoded local path). [Issue](https://github.com/agentscope-ai/QwenPaw/issues/8502)
- **#8547 [OPEN]** — *Creator: bundled cat-perspective example still cannot open on Windows (regression of #7291)* [Issue](https://github.com/agentscope-ai/QwenPaw/issues/8547)
- **#8534 [OPEN]** — *Creator: TTS cannot use Aliyun Token Plan key* — `qwen-audio-tts` uses different DashScope service; protocol option missing from UI. [Issue](https://github.com/agentscope-ai/QwenPaw/issues/8534)
- **#8533 [OPEN]** — *Codex/Qoder backend cron jobs misjudge non-empty requests as empty* — `ValueError: Third-party agent requests require text or attachments`. [Issue](https://github.com/agentscope-ai/QwenPaw/issues/8533)

**Fix coverage:** 4 of 10 open bugs have associated fix PRs (#8414→#8235/#8433, #8549→#8550, #8502→#8524, #8488→#8514 closed). The **security issue #8513 has no fix PR** and should be prioritized.

---

## 6. Feature Requests & Roadmap Signals

**In-flight feature PRs (likely next version):**

- **#8532** [OPEN, XXXL] *Append-only session storage + paginated history + native Turn forks* — Major architectural change for long conversations. **Strong candidate for 2.3.0 final or 2.4.0.** [PR](https://github.com/agentscope-ai/QwenPaw/pull/8532)
- **#8554** [OPEN, XXXL] *Plugin CLI commands and extension points* — Extends Click tree with `register_cli(CliSpec(...))`, dependency checks, resource lifecycle. **Plugin ecosystem maturity.** [PR](https://github.com/agentscope-ai/QwenPaw/pull/8554)
- **#8557** [OPEN, M] *Per-run sessions for cron jobs* — Bounds cron history growth; currently every run appends to one dedicated per-job session with full history. [PR](https://github.com/agentscope-ai/QwenPaw/pull/8557)
- **#8444** [OPEN, L] *Jev completion decisions* — Optional decision-model self-check to flag promised-but-undelivered work when text-only replies trigger qualitative rubrics. [PR](https://github.com/agentscope-ai/QwenPaw/pull/8444)
- **#8599/#8581** [OPEN, S] *Matrix E2EE dependencies installed by default* + legacy state migration simplification — Matrix channel hardening. [PR #8559](https://github.com/agentscope-ai/QwenPaw/pull/8559)
- **#8504** [OPEN, XXXL] *Desktop approval notifications* — Unified system notifications for approval requests, completions, background tasks even when app is backgrounded. Closes #8460. [PR](https://github.com/agentscope-ai/QwenPaw/pull/8504)
- **#8434** [OPEN, S] *Quick Add Model button* on Models page — Reduces 5 interactions across 3 screens to a single action. Fixes #4036. [PR](https://github.com/agentscope-ai/QwenPaw/pull/8434)
- **#8236** [OPEN, L] *Ollama context window from `num_ctx` instead of 128k default* — Local model accuracy fix. [PR](https://github.com/agentscope-ai/QwenPaw/pull/8236)

**Prediction:** 2.3.0 final likely ships with session storage (#8532), plugin CLI (#8554), and the accumulated beta bug fixes. Matrix E2EE and desktop notifications may land in 2.4.0.

---

## 7. User Feedback Summary

**Pain points (verbatim themes):**

1. **Plugin dependency fragility** (#8265, #8553): *"我的插件被这个卡了"* ("my plugin is blocked by this") — users are hitting real-world distro backports and hub dependency audit failures. The shared-dependency model (`dependency_mode: shared`) creates cascading activation failures.

2. **Windows platform second-class citizenship** (#8488, #8414, #8502, #8547, #8528, #8552): CRLF normalization, Chinese paths, GBK encoding, PyInstaller asset omission—**6+ distinct Windows-specific defects** reported or fixed today. PR #8234 (GBK/CP936 decode instead of dropping bytes) addresses a long-standing data-loss issue for Chinese Windows users.

3. **Silent failures** (#8488, #8549, #8505): Visual Compact no-ops silently; model config accepts invalid values then provider vanishes; gateway 502 becomes `unknown` with no retry. Users want **loud, actionable diagnostics**.

4. **Third-party backend integration** (#8533, #8534): Codex/Qoder cron jobs misjudge content; Aliyun Token Plan keys incompatible with TTS. Users are mixing providers aggressively and hitting seam failures.

5. **Channel concurrency** (#8511): Feishu users get intermittent "Internal error" when sending while agent is busy—perceived as instability.

**Satisfaction signals:** High issue-to-PR velocity (multiple same-day fixes), first-time contributor engagement (#8550), and detailed bug reports with source-level analysis (#8517, #8414, #8505) suggest a technically sophisticated, invested user base. The **security report #8513** is particularly well-researched and reflects a user doing responsible disclosure rather than complaining.

---

## 8. Backlog Watch

**Long-unanswered / needing maintainer attention:**

| Item | Age | Why it matters |
|------|-----|----------------|
| **#8513** Security: outbound shell actions ungated at AUTO/SMART | Created 2026-10-30, 1 comment | **No fix PR; exfiltration risk.** Highest priority. [Issue](https://github.com/agentscope-ai/QwenPaw/issues/8513) |
| **#8235 / #8433** Competing CRLF `edit_file` fixes | Both created 2026-10-14/26, under review | **Duplicate effort**—maintainers should consolidate to avoid merge conflict and contributor frustration. [PR #8235](https://github.com/agentscope-ai/QwenPaw/pull/8235) · [PR #8433](https://github.com/agentscope-ai/QwenPaw/pull/8433) |
| **#8234** GBK/CP936 decode in read/edit/grep | Created 2026-10-14, under review | Data-loss fix for Chinese Windows; stalled 20 days. [PR](https://github.com/agentscope-ai/QwenPaw/pull/8234) |
| **#8236** Ollama `num_ctx` context sizing | Created 2026-10-14, under review | Local model accuracy; stalled 20 days. [PR](https://github.com/agentscope-ai/QwenPaw/pull/8236) |
| **#4312** agent.json hot-reload scope (MCP, Skills) | Created 2026-05-14, **closed today** after 6 months | Good closure, but indicates backlog depth—feature requests can languish months. [Issue](https://github.com/agentscope-ai/QwenPaw/issues/4312) |
| **#8013** Skill pool download timeout (12,994 files, 80MB) | Created 2026-09-29, closed today | Large-skill handling needed three-layer fix (frontend 30s abort, backend copy, final workspace landing). [Issue](https://github.com/agentscope-ai/QwenPaw/issues/8013) |

**Recommendation:** Maintainers should (1) assign #8513 to a security-capable contributor immediately, (2) pick one CRLF fix (#8235 or #8433) and close the other with credit, and (3) batch-review the three stalled 2026-10-14 PRs (#8234, #8235, #8236) from contributor `Li-john1021` to retain engagement.

---

*Digest generated from GitHub activity data for 2026-11-03. All links point to `github.com/agentscope-ai/QwenPaw`.*

</details>

<details>
<summary><strong>ZeroClaw</strong> — <a href="https://github.com/zeroclaw-labs/zeroclaw">zeroclaw-labs/zeroclaw</a></summary>

# ZeroClaw Project Digest — 2026-11-03

## 1. Today's Overview

ZeroClaw shows a very high-activity development day: 50 issues updated in the last 24h (48 open/active, only 2 closed) and 50 PRs updated (40 open, 10 merged/closed), with no new releases. The project is clearly in a pre-`v0.9.0` hardening phase — multiple P1-tagged items reference `release:v0.9.0` (pairing enforcement, release-size gating, Docker source builds), and security/identity-access work dominates new bug intake. The issue queue is not shrinking — a roughly 24:1 open-to-closed ratio today suggests triage- and fix-throughput is a growing bottleneck relative to bug discovery. Maintainer attention is visibly split across gateway security (pairing, OIDC, X-Forwarded-For trust), agent-turn lifecycle, and provider context-window correctness. Overall project health is strong on contribution volume but shows strain in issue closure and release-gate verification.

## 2. Releases

No new releases in the last 24h. (Latest known shipped versions in issue reports are `zeroclaw v0.8.7` / web `v0.8.x`; `v0.9.0` work is in flight.)

## 3. Project Progress

Merged/closed PRs today (10 total), notable items:

- **[PR #12208](https://github.com/zeroclaw-labs/zeroclaw/pull/12208)** — `fix(web): restore an in-flight turn after a reload`: persists an optimistic prompt + throttled `localStorage` snapshot of the partial answer/reasoning so chat reloads no longer lose work. Directly complements the gateway WS-lifetime issue #7759.
- **[PR #12209](https://github.com/zeroclaw-labs/zeroclaw/pull/12209)** — `fix(web): require confirmation and neutral wording for stopped turns`: stops accidental double-tap cancellations and removes `[interrupted by user]` persistence noise.
- **[PR #11054](https://github.com/zeroclaw-labs/zeroclaw/pull/11054)** — WhatsApp Web: thematic breaks (`---`, `***`, `___`) and setext headings now render correctly instead of leaking as literal punctuation (closes #11052).
- **[PR #11739](https://github.com/zeroclaw-labs/zeroclaw/pull/11739)** — Telegram: the ack reaction is now withdrawn when a reply finishes (missing `remove_reaction` override fixed).
- **[PR #11270](https://github.com/zeroclaw-labs/zeroclaw/pull/11270)** — CI: `zeroclaw-desktop` tests now run on macOS, Linux and Windows, closing a coverage gap where every other workspace job excluded the desktop crate.
- **[PR #12202](https://github.com/zeroclaw-labs/zeroclaw/pull/12202)** — dependency bump (rust-all group, 35 updates), superseded by **#12215** the same day.

**In-flight high-value work:** PR [#11921](https://github.com/zeroclaw-labs/zeroclaw/pull/11921) (append-only session event history — architectural, XL), [#11965](https://github.com/zeroclaw-labs/zeroclaw/pull/11965) (opt-in OIDC dashboard sign-in), [#12213](https://github.com/zeroclaw-labs/zeroclaw/pull/12213) (enforce `[gateway.pairing_dashboard]`, fixing #9394), [#11712](https://github.com/zeroclaw-labs/zeroclaw/pull/11712) (extract `zeroclaw-a2a` crate), [#11456](https://github.com/zeroclaw-labs/zeroclaw/pull/11456) (subprocess memory watchdog).

## 4. Community Hot Topics

| Item | Activity | Underlying need |
|---|---|---|
| [#7759](https://github.com/zeroclaw-labs/zeroclaw/issues/7759) — Decouple gateway WebSocket lifetime from agent turn lifecycle | 8 comments, P1, in-progress, risk:high | Users want background turns that survive disconnects — reliability of long-running agent work over flaky/mobile connections. |
| [#10926](https://github.com/zeroclaw-labs/zeroclaw/issues/10926) — Matrix `send_via` treats peer user identities as room destinations | 7 comments, P2 | Cross-channel addressing semantics are confusing; A2A/peer messaging needs precise target resolution. |
| [#12014](https://github.com/zeroclaw-labs/zeroclaw/issues/12014) — Custom OpenAI-compatible provider silently assumes 32k context | 5 comments, **S1** | Bring-your-own-provider users hit silent local rejection of valid turns; need explicit, loud configuration errors. |
| [#9394](https://github.com/zeroclaw-labs/zeroclaw/issues/9394) — `pairing_dashboard` accepted but unread; pairing codes never expire | 5 comments, P1, `v0.9.0` | Security expectations: documented config must actually be enforced; fix PR **#12213** is open. |
| [#11703](https://github.com/zeroclaw-labs/zeroclaw/issues/11703) — `tool_search` activation busts prompt cache | 4 comments | Cost-sensitive users with deferred MCP loading; wants stable system prompts. |

**Pattern:** the top threads are dominated by *reliability of long-lived turns*, *bring-your-own-model configuration correctness*, and *security config that is advertised but not enforced*.

## 5. Bugs & Stability

Ranked by severity; all newly reported/refreshed today unless noted:

**S1 — workflow blocked**
- [#12210](https://github.com/zeroclaw-labs/zeroclaw/issues/12210) — *New today.* Every source Docker image build (`Dockerfile`, `.debian`, `.alpine`) fails at dependency pre-fetch because `tests/fixtures/runtime-composition-consumer` isn't copied. **Release-gate risk (P1, `v0.9.0`); no fix PR yet.**
- [#12187](https://github.com/zeroclaw-labs/zeroclaw/issues/12187) — Channel cached-turn normalization merges a user turn into a `role=tool` envelope, permanently poisoning conversations on strict providers. **No fix PR identified.**
- [#12014](https://github.com/zeroclaw-labs/zeroclaw/issues/12014) — 32k context-window fallback rejects valid turns (see Hot Topics).
- [#9859](https://github.com/zeroclaw-labs/zeroclaw/issues/9859) — Distroless release image lacks `sh`, breaking `runtime.shell` webhooks (S1, ongoing since Aug).
- [#12126](https://github.com/zeroclaw-labs/zeroclaw/issues/12126) — Windows UNC attachment path can make the daemon authenticate to an attacker-controlled SMB/WebDAV server (credential disclosure).

**P1 / high-risk**
- [#12194](https://github.com/zeroclaw-labs/zeroclaw/issues/12194) — *New today.* Unknown model silently falls back to `UNCONFIGURED_CONTEXT_WINDOW_FALLBACK = 32_000`; same root cause family as #12014 — **a fix should probably be consolidated.**
- [#12203](https://github.com/zeroclaw-labs/zeroclaw/issues/12203) — `web_search_tool` silently compiled out without `tools-external`; no warning emitted (regression from #11221).
- [#9394](https://github.com/zeroclaw-labs/zeroclaw/issues/9394) — pairing codes never expire (fix PR #12213 open ✅).

**Security (identity/access, S2 class)**
- [#12052](https://github.com/zeroclaw-labs/zeroclaw/issues/12052) — rate limiters trust the first `X-Forwarded-For` entry → loopback exemption spoofable behind appending proxies.
- [#12050](https://github.com/zeroclaw-labs/zeroclaw/issues/12050) — OIDC callback token page has no browser binding → login CSRF.
- [#12053](https://github.com/zeroclaw-labs/zeroclaw/issues/12053) — `/api/docs` loads an unpinned third-party script on the dashboard origin (no SRI).
- [#12127](https://github.com/zeroclaw-labs/zeroclaw/issues/12127) — `git_operations` touches `gitdir:`/`commondir`/alternates paths before authorization on Windows (UNC credential leak vector).

**S2/S3 degraded behavior**
- [#10926](https://github.com/zeroclaw-labs/zeroclaw/issues/10926) (Matrix), [#10924](https://github.com/zeroclaw-labs/zeroclaw/issues/10924) (runtime-command replies enter voice routing), [#12167](https://github.com/zeroclaw-labs/zeroclaw/issues/12167) (`send_message_to_peer` ignores autonomy level/action budget — a policy bypass), [#12104](https://github.com/zeroclaw-labs/zeroclaw/issues/12104) (OpenAI Responses drops tool-result images), [#12133](https://github.com/zeroclaw-labs/zeroclaw/issues/12133) (Windows env allowlists omit `APPDATA`/`LOCALAPPDATA`).
- [#9562](https://github.com/zeroclaw-labs/zeroclaw/issues/9562) (WebChat auto-scroll), [#12186](https://github.com/zeroclaw-labs/zeroclaw/issues/12186) (`zerocode --agent` parsed but unused), [#12087](https://github.com/zeroclaw-labs/zeroclaw/issues/12087) (tool toggle double-save) — S3 UX issues.

**Assessment:** the security cluster (#12050/#12052/#12053/#12126/#12127) appears to be a single audit series by one reporter and deserves a consolidated security-release response before `v0.9.0`.

## 6. Feature Requests & Roadmap Signals

- [#7759](https://github.com/zeroclaw-labs/zeroclaw/issues/7759) — Background/resumable gateway turns (**P1, in-progress**, with web-side support already merged in #12208/#12209). **Very likely in v0.9.0.**
- [#10893](https://github.com/zeroclaw-labs/zeroclaw/issues/10893) — Mid-generation steering for channel messages (status:blocked; pipeline exists but has no producer). Natural companion to #7759.
- [#12183](https://github.com/zeroclaw-labs/zeroclaw/issues/12183) — Runtime prompt/command intercept & injection for active agents — same "in-flight controllability" theme.
- [#12181](https://github.com/zeroclaw-labs/zeroclaw/issues/12181) — Web managed document upload (`#9488` Phase 2 Web slice), off by default — likely next minor after OIDC.
- [#12191](https://github.com/zeroclaw-labs/zeroclaw/issues/12191) — Hybrid dense+BM25 recall for Qdrant memory backend (parity with SQLite backend) — a plausible mid-term roadmap item.
- [#12107](https://github.com/zeroclaw-labs/zeroclaw/issues/12107) — Show a detached turn's answer when the socket reopens — follow-up to #11965, pairs with #7759.

**Prediction for v0.9.0:** enforced pairing dashboard (#12213), OIDC dashboard sign-in (#11965), gateway turn decoupling (#7759), and the release/CI fixes (#12210, #11580, #11272) — i.e., v0.9.0 looks like a *security + reliability release*, not a feature-expansion release.

## 7. User Feedback Summary

- **Configuration trust is the loudest complaint.** Users report config keys that are "accepted and entirely unread" (#9394) and defaults that silently assume wrong values (#12014, #12194, #12203) — the recurring phrase is *silently*. The unmet expectation: invalid or degraded config should fail loudly at startup, not mid-turn.
- **Long-running work must survive the transport.** Multiple threads (#7759, #9562, #12107, #12208, #12209) trace back to users treating the web chat as a production console and being burned by reloads, disconnects, or auto-scroll stealing their scroll position — a clear sign of real daily use on mobile/flaky networks.
- **Power users are running multi-agent/multi-channel topologies.** Matrix peer routing (#10926), `send_message_to_peer` autonomy bypass (#12167), and mid-turn steering (#10893) come from users operating agent groups, not single assistants.
- **Security reporters are doing careful, line-cited audits** (#9394, #12050, #12052, #12126, #12127) — the community is treating ZeroClaw as a deployable daemon with real threat models (proxies, OIDC, Windows UNC), which is a sign of maturity but also of accumulated security debt.
- **Satisfaction signal:** quick turnaround on web/chat UX fixes (same-day PRs #12208/#12209) suggests maintainers are responsive to the frontend pain points users surface most.

## 8. Backlog Watch

Items needing maintainer attention due to age, severity, or blocked status:

- **[#9859](https://github.com/zeroclaw-labs/zeroclaw/issues/9859)** — Distroless image missing `sh` (S1, open since 2026-08-09, only 2 comments, 1 👍). Long-standing release-artifact blocker with no linked fix.
- **[#11580](https://github.com/zeroclaw-labs/zeroclaw/issues/11580)** — x86_64 Linux binary ~0.7 MB under the 64 MiB cap; needs a recorded headroom policy decision (`needs-maintainer-review`, `v0.9.0`). A gate decision, not a coding task — ideal for a maintainer call this week.
- **[#10893](https://github.com/zeroclaw-labs/zeroclaw/issues/10893)** — Steering pipeline exists but has no producer; **status:blocked** and unowned. Deserves a design decision or explicit deferral.
- **[#9394](https://github.com/zeroclaw-labs/zeroclaw/issues/9394)** — Though fix PR [#12213](https://github.com/zeroclaw-labs/zeroclaw/pull/12213) is open, the issue has lingered since July with `release:v0.9.0`; reviewer capacity is the bottleneck.
- **[#11272](https://github.com/zeroclaw-labs/zeroclaw/pull/11272)** — Release fix for missing dashboard embed in Linux/Windows desktop kernels, open since 2026-09-30 with no comments; if unmerged, shipped desktop builds are visibly broken.
- **[#11456](https://github.com/zeroclaw-labs/zeroclaw/pull/11456)** — Subprocess memory watchdog, `needs-maintainer-review`, open since Oct 2; valuable for untrusted-shell safety but stalled.
- **[#11052](https://github.com/zeroclaw-labs/zeroclaw/pull/11054)** — closed today ✅, listed only as a resolution note.

---

**Bottom line:** ZeroClaw is highly active and clearly converging on a security-and-reliability `v0.9.0`, but the open:closed issue ratio (24:1) and several stale S1 items (#9859, #11272) indicate that *review/merge capacity*, not contribution volume, is the project's current constraint.

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar).*