# AI CLI Tools Community Digest 2026-11-11

> Generated: 2026-11-10 23:14 UTC | Tools covered: 7

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

# AI CLI Tools Ecosystem — Cross-Tool Comparison Report
**Data window: 2026-11-11 (last 24h)**

---

## 1. Ecosystem Overview

The AI CLI landscape in this snapshot is simultaneously **maturing on infrastructure** and **fragmenting on reliability**. Every one of the seven tracked tools shipped at least one release in the window — an unusually high release cadence — but the dominant issue-tracker themes are regressions, not roadmap work: Windows platform stability, MCP/OAuth lifecycle fragility, sandbox-permission correctness, and subagent observability recur across independent projects. A second clear signal is the emergence of a **community tooling layer** *around* these CLIs (hooks, prompt-regression runners, approval routing in Codex; channel/webhook integrations in Qwen Code), suggesting the base tools have stabilized enough that users are building on top of them. Security and trust surface prominently and negatively — destructive-command classifier bypasses in Qwen Code and Gemini CLI, plaintext secret persistence in Codex, and server-side instruction injection in Claude Code collectively indicate that **agent-safety engineering, not raw capability, is the current bottleneck**.

---

## 2. Activity Comparison

| Tool | Active Issues (24h window) | Notable PRs (24h) | Discussions | Release Status | Notes |
|---|---|---|---|---|---|
| **Claude Code** | ~10 listed (2 live high-engagement: #100377, #106167) | 1 (closed, no merge) | N/A (no data supplied) | 1 release (v2.1.322) — infra-only | Tracker heavily pruned of stale reports; trust/injection issues upvoted |
| **OpenAI Codex** | ~10 + 3 honorable mentions | ~10 (mostly internal `copyberry` bot, CLOSED/landed) | Active — 6 notable threads (Show & Tell) | 3 releases (0.167.1, 0.169.0-alpha.9, 0.167.0-alpha.4.1) | Highest Windows friction; active community-built tooling |
| **Gemini CLI** | ~10 | ~10 (security/CI heavy) | N/A (no data supplied) | 2 releases (v0.69.0-preview.0, v0.68.0) | Subagent reliability is the defining problem; release-infra firefighting |
| **GitHub Copilot CLI** | ~10 | 1 (open, docs) | N/A (no data supplied) | 4 releases (v1.0.109/-1, v1.0.110-0/-1) | Rapid patch cadence; MCP lifecycle dominates pain points |
| **OpenCode** | ~10 (one at **346 comments**) | ~10 (active, all OPEN) | N/A (no data supplied) | 1 release (v2.2.7) | Single largest issue thread in the entire dataset; heavy v1→v2 migration debt |
| **Pi** | ~10 | ~10 (mostly CLOSED/landed) | 1 active Idea (#11984) | 2 releases (v1.5.1, v1.5.2) | Provider-integration and silent-degradation focus |
| **Qwen Code** | ~10 (CI noise dominates: 292, 55 comments) | ~10 (security/permissions heavy) | N/A (no data supplied) | 3 releases (v0.27.2, preview, SDK v0.1.32) | CI flakiness is the volume driver; P1 permission-classifier holes |

> **On N/A vs. inactive:** Claude Code, Gemini CLI, Copilot CLI, OpenCode, and Qwen Code show no Discussions data in this dataset. Per instruction, these are marked **N/A** — this reflects data availability in the supplied digests, not necessarily that Discussions are disabled upstream. Only Codex and Pi show confirmed Discussions activity. Issue/PR counts are approximate from the curated top-10 lists and should not be read as exhaustive tracker volume.

---

## 3. Shared Feature Directions

Four requirements appear independently across three or more communities:

**a) Subagent observability & lifecycle control**
Every agent-orchestration project is wrestling with the same problem: users cannot see or steer what subagents do.
- *Codex*: live activity + per-agent config in Subagents panel (#36266); explicit cross-thread orchestration (#14923, closed)
- *Gemini CLI*: subagent trajectories in `/chat share` (#22598), subagent context in `/bug` reports (#21763), false-success reporting (#22323)
- *Qwen Code*: unified agent loop across surfaces (#15327); ACP teammate-message delivery (#15738)
- *OpenCode*: always-background subagents with parent reporting (#63210, #63209)
- *Claude Code*: effort control for Claude-proposed sessions (#104151)

**b) MCP completeness outside the primary CLI surface**
MCP works in the happy path but degrades in IDEs, on Desktop, and under OAuth rotation — a *parity* problem, not a protocol problem.
- *Claude Code*: `structuredContent` dropped in Desktop Code tab (#103809); no Authenticate button in VS Code (#108783); refresh-token revocation across sessions (#108794)
- *Copilot CLI*: orphaned stdio processes (#4392), random loopback OAuth ports (#5391), Entra silent sign-in failures (#5402)
- *Codex*: read-only MCP tool hints in auto-approval (#60638); MCP refresh outside runtime gate (#60639)

**c) Cost, quota & proportionality control**
Users want explicit, discoverable budget controls rather than invisible sticky settings.
- *Claude Code*: "Ultracode" fan-out to 325 agents on a settings audit (#92349); sticky effort settings invisible to users
- *Codex*: quota opacity — misaligned billing cycles (#8581), banked resets expiring early (#60664), abnormal depletion (#59262)
- *Copilot CLI*: model block lists for auto/fusion routing (#5369); content-filter failures consuming credits (#5408)
- *OpenCode*: free-tier billing clarity as the single loudest theme (#59387)

**d) Windows platform parity**
A consistent, cross-vendor deficit — not one project's bug, an industry-wide one.
- *Codex*: VS Code extension UI failure (#56655, 85 👍), sandbox `WRITE_DAC` errors, CUA screenshot timeouts
- *Copilot CLI*: parent `pwsh.exe` crashes (#5404), keyboard regressions (#5171)
- *OpenCode*: console-window flashing ~1/sec (#52266), fnm/nvm upgrade paths (#63152)
- *Claude Code*: Windows worktree regressions closed stale unaddressed (#92019)

---

## 4. Differentiation Analysis

| Dimension | Claude Code | Codex | Gemini CLI | Copilot CLI | OpenCode | Pi | Qwen Code |
|---|---|---|---|---|---|---|---|
| **Primary focus** | Remote-control/session autonomy; MCP Apps ecosystem | Sandbox hardening + Guardian policy security | Subagent correctness + AST-aware tooling | Model catalog breadth + enterprise auth | Model flexibility + v2 migration | Provider breadth + extension runtime | Permission safety + multi-channel (WeChat/QQ/Email) |
| **Target user** | Professional devs on claude.ai Projects | Windows/Desktop + cloud-hybrid users | Google-stack devs, safety-conscious | GitHub/enterprise, Copilot Free→Pro funnel | Multi-provider power users | Self-hosted/local-model users | CN-market + multi-surface (CLI/Web Shell/daemon) |
| **Technical approach** | Server-delivered instructions; effort/agent fan-out | Rust core; `copyberry`-driven hardening; Guardian policy templates | Native bash affinity + proposed OS sandbox (#19873) | Fast patch cadence; experimental masked-prompt dialog | Background-subagent architecture; per-session MCP scoping | Chord stores for session sync; codemode globals | Moving to real shell parser (tree-sitter-bash) from hand-rolled model |
| **Distinctive risk** | Server-injection trust erosion (#106167) | Secret persistence in shell snapshots (#30971) | False-success subagent reporting | Enterprise credential masking gaps (#5388) | Catastrophic session-data loss on upgrade (#62659) | Silent model/catalog fallback (#10812) | Classifier bypass via redirection (#16856) |

**Key structural difference:** Codex and Qwen Code are investing in **defense-in-depth** (Guardian policy templates, tree-sitter shell parsing, fail-closed heredoc handling) — treating the agent as an untrusted execution surface. Claude Code and OpenCode are investing in **capability expansion** (server-side orchestration, background subagents) and are paying reliability costs as a result. Gemini CLI sits between, with its #19873 zero-dependency sandbox proposal signaling a planned architectural shift toward contained execution.

---

## 5. Community Momentum & Maturity

**Highest raw community engagement:**
- **OpenCode** — one issue thread (#59387) at **346 comments** dwarfs anything else in the dataset. This is a mixed signal: extreme engagement driven by free-tier friction, not enthusiasm. High momentum, but concentrated in a pain point.
- **Codex** — highest *upvote* density (#56655 at 85 👍) and the only tool with a robust **community-built tooling ecosystem** (Tapproval, RegressGuard, agent-harness, safety habits). This is the strongest maturity indicator: users are building *on* the tool, not just reporting on it.
- **Claude Code** — high-signal issues (server-injection at 10 👍 despite being a config complaint) but a tracker being aggressively pruned of stale Windows regressions — a **coverage-vs-cleanliness tension** that may be hiding real debt.

**Fastest iteration cadence:**
- **Copilot CLI** — 4 releases in 24h (v1.0.109 through v1.0.110-1). Extremely rapid patching, but the volume of MCP lifecycle bugs suggests fix-forward pressure.
- **Qwen Code** — 3 releases incl. SDK, but CI noise (#14724 at 292 comments) is consuming community signal bandwidth — flagged *Provisional* issues erode contributor trust.

**Most architecturally deliberate:**
- **Pi** — cleanest release-to-fix traceability (v1.5.2 directly backed by PR #11975). Lower engagement volume, but high fix quality and a coherent provider-auth roadmap (Entra ID, Kimi OAuth, ChatGPT context parity).
- **Gemini CLI** — actively firefighting release infra (#30207, 70 MB stale artifacts in a 106 MB tarball) while landing serious security PRs (#30171, #30095). Iterating, but maintenance-heavy.

**Maturity ranking (composite):** Codex ≈ Pi > Claude Code > Gemini CLI > Copilot CLI > OpenCode > Qwen Code — with the caveat that OpenCode's engagement is real and its subagent PR work (#63210, #63209) is architecturally ahead of several peers.

---

## 6. Trend Signals

**1. Agent safety is the #1 unsolved engineering problem.**
Destructive-command classifier bypasses (Qwen Code #16856: `git branch < /dev/null -D main` executes `-D main`; Gemini CLI #30171: `rm -Rf` variants classified safe), plaintext secret persistence (Codex #30971), and server-injected instructions (Claude Code #106167) all point the same direction. **Reference value:** if you're building on these CLIs, do not trust AUTO/permission classifiers as a security boundary — wrap them. The industry is converging on *real parsers* (tree-sitter-bash) and *fail-closed* semantics, but that transition is incomplete everywhere.

**2. MCP is winning the protocol war but losing the lifecycle war.**
Every tool supports MCP; every tool has OAuth callback, token rotation, and IDE-parity bugs. **Reference value:** multi-session OAuth is the sharpest edge (Claude Code #108794, Copilot #5402). For production MCP deployments, plan for re-auth churn and fixed callback ports (Copilot #5391 request) as first-class requirements.

**3. Subagent false-success reporting is a systemic trust defect.**
Gemini CLI (#22323, #21983), OpenCode (#63171 zero-content "succeeded"), and Claude Code (invisible effort settings) all show agents reporting success while failing. **Reference value:** for autonomous workflows, *never* trust a subagent's self-reported status — verify with side-effects or structured output schemas. This is the single highest-leverage observability gap.

**4. Silent degradation is the dominant UX failure mode.**
Cache warming stopping (Pi #10956), default-model fallback (#10812), stale catalogs (Pi #10539, Codex #60676), off-by-default telemetry (Gemini #30217). **Reference value:** configure explicit failure modes (models, catalog, warming) and monitor — these tools default to *quietly doing something different* rather than erroring.

**5. Vendor-specific undercurrents:**
- **Windows is an industry-wide second-class citizen** — every tool has a Windows regression cluster. If you're Windows-based, budget for parity lag across all vendors.
- **Enterprise auth is a competitive differentiator** — Pi's Entra ID PR (#12016), Copilot's NTLM proxy demand (#5407), and Codex's device-code sign-in (#60662) show headless/SSO auth as the next battleground.
- **The community tooling layer is forming** — Codex hooks/approval-routing/prompt-CI ecosystem and Qwen Code's channel webhooks indicate that **extensibility surfaces (hooks, Stop hooks, outbound integrations) are the next platform-neutral feature race**. Watch for these to become table stakes across all seven tools.

---

*Report compiled from 2026-11-11 community digests for Claude Code, OpenAI Codex, Gemini CLI, GitHub Copilot CLI, OpenCode, Pi, and Qwen Code. Counts are drawn from curated top-10 issue/PR lists and represent the snapshot, not full tracker volume. Issue/PR references are retained for verification.*

---

## Per-Tool Reports

<details>
<summary><strong>Claude Code</strong> — <a href="https://github.com/anthropics/claude-code">anthropics/claude-code</a></summary>

## Claude Code Skills Highlights

> Source: [anthropics/skills](https://github.com/anthropics/skills)

# Claude Code Skills — Community Highlights Report
*Data as of 2026-11-11 | Source: github.com/anthropics/skills*

---

## 1. Top Skills Ranking

> Note: The PR listing does not expose comment counts (all show `undefined`), so ranking below is based on total community attention — comment volume in linked Issues, recency of updates, and cross-referencing with active discussion threads.

| Rank | Skill / PR | Functionality | Discussion Highlights | Status |
|------|-----------|---------------|----------------------|--------|
| 1 | **fix(mcp-builder): mcp>=2 streamable_http_client** — [PR #1742](https://github.com/anthropics/skills/pull/1742) | Repairs `mcp-builder` for the MCP 2.0 API rename (`streamablehttp_client` → `streamable_http_client`) and custom header handling. | Tied to Issue #1668; part of a cluster of compatibility bugs surfacing as the MCP SDK matures. Author Kuldeeep18 also has a related skill-creator fix (PR #1681). | OPEN |
| 2 | **fix(skill-creator): isolate trigger evals** — [PR #1298](https://github.com/anthropics/skills/pull/1298) | Fixes false-negative trigger evaluation: worker command probe races, Windows `select()` on subprocess pipes, and runtime failures being mis-scored as non-triggers. | Directly addresses the broader eval-reliability theme seen in Issues #556 and #1352 — the most-commented bug class in the repo. | OPEN |
| 3 | **Add notion-spec-to-implementation + quantitative-resume-auditor** — [PR #1245](https://github.com/anthropics/skills/pull/1245) | Turns product/tech specs into actionable Notion tasks with acceptance criteria; second skill audits resumes quantitatively. | Dual-skill submission; spans workflow automation + document analysis. Long-lived (updated through Oct). | OPEN |
| 4 | **webapp-testing: avoid shell=True** — [PR #1980](https://github.com/anthropics/skills/pull/1980) | Removes command-injection risk (CWE-78) in `with_server.py` by eliminating `shell=True` for `cd && command` chains. | Security-hardening contribution; part of a wave of webapp-testing fixes alongside PRs #1976. | OPEN |
| 5 | **fix(docx): report LibreOffice timeout as error** — [PR #1792](https://github.com/anthropics/skills/pull/1792) | Stops `accept_changes.py` from reporting false success on `soffice` timeout; verifies revision marks are actually gone. | Reinforces the "verify outputs, don't trust exit codes" pattern prominent across docx/xlsx PRs. | OPEN |
| 6 | **Add pyxel skill for retro game development** — [PR #525](https://github.com/anthropics/skills/pull/525) | Guides creation, debugging, and headless verification of retro Python games via Pyxel. | One of the longest-open popular PRs (since March) with steady updates; signals demand for creative/game dev skills. | OPEN |
| 7 | **Add proofcore-contract-auditor** — [PR #1771](https://github.com/anthropics/skills/pull/1771) | Static analysis of Solidity/Rust contracts with cryptographic audit proofs anchored on TON blockchain. | First notable Web3/security-focused skill submission; novel "zero-storage Merkle" approach. | OPEN |
| 8 | **Add document-typography skill** — [PR #514](https://github.com/anthropics/skills/pull/514) | Typographic QC for generated docs: orphan/widow control, numbering alignment. | Addresses universal output-quality pain; complements the document-skills family. | OPEN |

**Also notable:** [PR #2019](https://github.com/anthropics/skills/pull/2019) (xlsx external-link false negatives) and [PR #1997](https://github.com/anthropics/skills/pull/1997) (YAML frontmatter parsing in skill-creator) — both high-value correctness fixes.

---

## 2. Community Demand Trends

Distilled from the most-commented Issues:

1. **Skill evaluation & triggering reliability** *(highest engagement)*
   - [Issue #556](https://github.com/anthropics/skills/issues/556) — 13 comments, 7 👍: `run_eval.py` shows **0% trigger rate** across all queries.
   - [Issue #1352](https://github.com/anthropics/skills/issues/1352) — parallel workers cross-match skill UUIDs, producing silent false negatives.
   - **Demand:** a trustworthy, deterministic way to verify that a Skill actually fires.

2. **Trust, security & namespace integrity**
   - [Issue #492](https://github.com/anthropics/skills/issues/492) — 46 comments, the single most-discussed issue: community skills impersonating the `anthropic/` namespace, enabling trust-boundary abuse.

3. **Org-wide skill sharing & distribution**
   - [Issue #228](https://github.com/anthropics/skills/issues/228) — 17 comments, 8 👍: users want a shared skill library instead of manual `.skill` file uploads via Slack.

4. **Context/token efficiency**
   - [Issue #1487](https://github.com/anthropics/skills/issues/1487) — `claude-api` skill eagerly injects ~156k tokens, exhausting context in one call.
   - [Issue #202](https://github.com/anthropics/skills/issues/202) — skill-creator reads like human docs rather than token-efficient instructions.

5. **Output correctness in document skills**
   - [Issue #2018](https://github.com/anthropics/skills/issues/2018) (docx invalid XML), [Issue #1868](https://github.com/anthropics/skills/issues/1868) (gradient interpolation off-by-one).

6. **Skill authorship quality standards**
   - [Issue #2064](https://github.com/anthropics/skills/issues/2064) — descriptions state *what* a skill does but not *when* to use it; a recurring frontmatter-design critique.

7. **Deduplication**
   - [Issue #189](https://github.com/anthropics/skills/issues/189) — 9 👍: `document-skills` and `example-skills` plugins ship identical content, wasting context.

**Emerging directions proposed by community:** reasoning/quality-gate pipelines ([Issue #1385](https://github.com/anthropics/skills/issues/1385)), agent governance/safety patterns ([Issue #412](https://github.com/anthropics/skills/issues/412)), and compact symbolic agent memory ([Issue #1329](https://github.com/anthropics/skills/issues/1329)).

---

## 3. High-Potential Pending Skills

Active-comment or recently-updated PRs likely to land soon:

- **[PR #1980](https://github.com/anthropics/skills/pull/1980)** — webapp-testing `shell=True` security fix (updated 2026-11-01). High merge probability: small, targeted, security-motivated.
- **[PR #1792](https://github.com/anthropics/skills/pull/1792)** — docx timeout verification (updated 2026-11-01). Clear correctness win with testable behavior.
- **[PR #1977](https://github.com/anthropics/skills/pull/1977)** & **[PR #1976](https://github.com/anthropics/skills/pull/1976)** — small, well-scoped bug fixes by the same contributor (wrapAround modulo; textarea/select discovery), each explicitly fixing a filed Issue.
- **[PR #1950](https://github.com/anthropics/skills/pull/1950)** — claude-api `pause_turn` Python guidance update (updated 2026-11-10, most recent). Docs-only, low-risk.
- **[PR #1997](https://github.com/anthropics/skills/pull/1997)** — skill-creator YAML frontmatter parsing; improves eval/description-optimization metadata fidelity.
- **[PR #2019](https://github.com/anthropics/skills/pull/2019)** — xlsx external-link resolution (updated 2026-11-03); fixes filed Issue #2013.
- **[PR #1681](https://github.com/anthropics/skills/pull/1681)** — skill-creator standalone script execution (`ModuleNotFoundError` fix); a common developer-experience blocker.
- **[PR #1245](https://github.com/anthropics/skills/pull/1245)** — dual Notion/workflow skills (updated 2026-10-15); larger feature PR, needs maintainer review but on-trend.

**Observation:** The pending backlog skews heavily toward **bug-fix PRs tied to filed Issues** rather than net-new skills — a sign the repo is in a hardening phase.

---

## 4. Skills Ecosystem Insight

**The community's most concentrated demand is reliability and trust — deterministic skill triggering/evaluation, security-safe namespacing, and verified (not assumed) outputs — rather than more new Skill categories.**

---

*Rankings reflect attention proxies (linked-issue comment volume, update recency, cross-references); the PR feed did not expose per-PR comment counts, so no PR is ranked on literal comment totals.*

---

# Claude Code Community Digest — 2026-11-11

## 1. Today's Highlights

The only release in the last 24 hours is a small infrastructure win: **v2.1.322 adds fast mode on Vertex AI** across global, US, and EU regions, with graceful fallback. The issue tracker is dominated by cleanup of stale reports, but two live threads stand out: the **CCR v2 remote-control registration failure** (#100377, 18 comments) and a heavily-upvoted complaint that a **server-delivered "games" instruction block is interfering with professional work** (#106167, 10 👍). On the tooling side, several fresh MCP/auth bugs filed yesterday suggest OAuth token lifecycle and MCP completeness remain active friction areas.

## 2. Releases

**v2.1.322**
- Added fast mode on Vertex in the global, `us`, and `eu` regions, falling back to standard speed if Vertex refuses the request.
- Low-risk, infrastructure-only change; no behavioral or API surface changes for CLI users.

## 3. Hot Issues

1. **[#100377](https://github.com/anthropics/claude-code/issues/100377) — Remote Control: Project threads fail with "CCR v2 worker registration failed … 400"** (OPEN, macOS, 18 comments)
   Self-started sessions register fine on the same server, but claude.ai Project threads consistently fail. Highest-engagement live issue; indicates a server-side/worker-registration regression rather than a client bug.

2. **[#106167](https://github.com/anthropics/claude-code/issues/106167) — Server-delivered "games" instruction blocks professional development work** (OPEN, macOS, 16 comments, 10 👍)
   The most-upvoted live issue. A model-side instruction injected by the server is derailing legitimate work — a rare case of community consensus that a *server config*, not the user prompt, is the problem.

3. **[#106900](https://github.com/anthropics/claude-code/issues/106900) — Duplicate report of CCR v2 400 on Project-started Remote Control sessions** (OPEN, macOS)
   Closed as duplicate of #100377 but corroborates the scope: the failure is reproducible across users and predates the latest release. Worth tracking as the canonical thread.

4. **[#103809](https://github.com/anthropics/claude-code/issues/103809) — Desktop Code tab: MCP App widgets receive no `structuredContent`** (OPEN, macOS)
   MCP Apps (SEP-1865) servers expose `outputSchema` and UI resources, but the desktop Code tab only passes payload as JSON text. Blocks the richer-UI MCP ecosystem on desktop.

5. **[#108677](https://github.com/anthropics/claude-code/issues/108677) — Gmail remote MCP: auth succeeds but tools fetch fails** (OPEN, macOS, filed 2026-11-09)
   "Error POSTing to endpoint" despite a valid `tools/list` body — a fresh, high-signal MCP interop bug affecting a first-party Google integration.

6. **[#108783](https://github.com/anthropics/claude-code/issues/108783) — VS Code MCP panel has no Authenticate button for connected HTTP servers** (OPEN, Linux/VS Code)
   Servers with optional auth can't be signed in from VS Code at all. Indicates UI parity gaps between CLI and IDE integrations for OAuth-capable MCP servers.

7. **[#108794](https://github.com/anthropics/claude-code/issues/108794) — Rotating MCP refresh tokens revoked across shared sessions** (OPEN, macOS)
   Multi-session users must re-OAuth every 2–3 days. Related to still-open #99539 and previously stale #84440 — a recurring token-lifecycle class of bug that keeps resurfacing.

8. **[#104151](https://github.com/anthropics/claude-code/issues/104151) — Desktop Code tab: choose effort for Claude-proposed/started sessions** (OPEN, enhancement, Windows)
   Requests the ability to set (or raise above parent) effort for child sessions. Part of a broader "visibility and control over autonomous session spawning" theme.

9. **[#92019](https://github.com/anthropics/claude-code/issues/92019) — Worktree session creation fails on Windows since 2.1.260** (CLOSED/stale, Windows)
   Background full checkout forces `checkout.workers=8`. Closed as stale without a fix — representative of a large batch of Windows regressions being swept out of the tracker unaddressed.

10. **[#92349](https://github.com/anthropics/claude-code/issues/92349) — Ultracode removes proportionality; 325 agents for a settings audit** (CLOSED/stale, macOS)
    Documents "use the Workflow tool on every substantive task; token cost is not a constraint" turning small tasks into massive agent fan-outs on an invisible sticky effort setting. Closed stale, but a strong signal on cost-control UX.

## 4. Key PR Progress

Only one PR was updated in the last 24 hours, so this section is necessarily short. It is also already closed.

1. **[#74722](https://github.com/anthropics/claude-code/pull/74722) — feat(commit-commands): support Conventional Branch naming in `/commit-push-pr`** (CLOSED)
   Adds an optional `conventional` argument that names branches per the Conventional Branch 1.0.0 spec (`<type>/<description>`), inferring `type` (feature, bugfix, hotfix, release, chore, docs, test) from the diff. Useful for teams standardizing branch hygiene via slash commands; closed without merge, but a clean template for a community contribution.
   *No other PRs met the 24h update window, so no further items are listed.*

## 5. Hot Discussions

*No GitHub Discussions data was provided in the source dataset; this section is omitted.*

## 6. Feature Request Trends

Distilled from open enhancements and recurring themes across the tracker:

- **Session control & effort configuration** — Let users pick/raise effort for Claude-proposed or self-started sessions (#104151); make "sticky" effort settings visible and overridable (#92349).
- **MCP completeness on non-CLI surfaces** — Structured `structuredContent` delivery to MCP App widgets (#103809), an Authenticate button in the VS Code MCP panel (#108783), and stable multi-session OAuth tokens (#108794).
- **Desktop/IDE parity** — Requests for pane-expansion shortcuts (#92157) and consistent UI affordances between the CLI, VS Code, and Desktop Code tab.
- **Command/skill ergonomics** — Conventional Branch naming for `/commit-push-pr` (#74722) and predictable positional argument substitution in custom slash commands (#92171, #92457).
- **Remote Control reliability** — Make Project-started Remote Control sessions as reliable as self-started ones (#100377, #106900).

## 7. Developer Pain Points

- **Remote Control is unreliable for Project threads.** Multiple reporters (#100377, #106900) see consistent CCR v2 worker-registration 400s, while self-started sessions on the same server work — pointing to a server-side registration path rather than local networking.
- **OAuth/MCP token lifecycle is fragile.** Rotating refresh tokens get revoked when sessions share a sign-in (#108794), and IDE clients lack ways to complete auth (#108783), forcing repeated re-logins.
- **MCP interop gaps surface late.** Auth succeeds but tool fetches fail (#108677), and structured content is dropped in the Desktop Code tab (#103809) — both degrade the value of the MCP ecosystem.
- **Cost and proportionality control.** "Ultracode"/workflow behavior can fan out to hundreds of agents on trivial tasks on an invisible setting (#92349) — developers want explicit, discoverable budget/effort controls.
- **Instruction injection and trust.** Server-injected "games" instructions (#106167, 10 👍) and unsolicited `<system-reminder>` content in tool results (#92386) erode confidence that model behavior follows user intent.
- **Windows and desktop regressions go stale.** A cluster of Windows-specific regressions (e.g., #92019 worktree checkout, MCP widget duplicates #92294, heredoc deadlocks #92262) is being closed as stale without fixes — a recurring cleanliness-vs-coverage tension in the tracker.
- **Session isolation issues.** Cross-session message delivery killing in-flight background Bash tasks (#91139) and per-fire process leaks in scheduled tasks (#91879) remain notable for multi-session users.

---
*Sources: github.com/anthropics/claude-code — Releases, Issues, and Pull Requests updated 2026-11-10 (data window: last 24h). No Discussions data was supplied, so that section was omitted per instructions.*

</details>

<details>
<summary><strong>OpenAI Codex</strong> — <a href="https://github.com/openai/codex">openai/codex</a></summary>

# OpenAI Codex Community Digest — 2026-11-11

## 1. Today's Highlights

Patch release **rust-v0.167.1** shipped, and a wave of `copyberry` PRs landed focused on sandbox hardening (Windows `cmd` quoting, MXC PowerShell working-directory fix), Guardian policy integrity, and MCP approval semantics. Windows remains the dominant source of community friction this cycle: VS Code extension startup failures, sandbox `WRITE_DAC` errors, Computer Use screenshot timeouts, and SSH auto-connect regressions all drew sustained attention. Separately, a notable model-catalog regression surfaced today where **GPT-6.1 Sol** disappears from refreshed catalogs on Linux/Plus accounts.

---

## 2. Releases

Three releases appear in the last 24h window:

- **[rust-v0.167.1](https://github.com/openai/codex/releases)** — a patch bump over `0.167.0` (no detailed changelog beyond version bump; full diff available via [compare](https://github.com/openai/codex/compare/rust-v0.167.0...rust-v0.167.1)).
- **[rust-v0.169.0-alpha.9](https://github.com/openai/codex/releases)** — next minor alpha, rolling forward toward 0.169.
- **[rust-v0.167.0-alpha.4.1](https://github.com/openai/codex/releases)** — hotfix line for the Desktop app; see related backport PR [#60457](https://github.com/openai/codex/pull/60457) adding the default-off `incremental_tools_legacy` feature for Amazon Bedrock Responses.

**Takeaway:** the 0.167.x line is receiving active patching (Desktop hotfixes via alpha.4.1) while 0.169 development continues in parallel. Teams on `0.167.0` should watch for the 0.167.1 patch, particularly Windows sandbox users.

---

## 3. Hot Issues

1. **[#56655](https://github.com/openai/codex/issues/56655) — VS Code extension UI doesn't start (Windows), 63 comments / 85 👍** — Highest-engagement issue in the window. Extension `26.51021.22027` fails to render UI on Windows 11 builds. The 👍 count signals broad blast radius; Pro users appear affected. **Why it matters:** extension regressions block the primary workflow for a large segment.

2. **[#49383](https://github.com/openai/codex/issues/49383) — Windows Computer Use screenshot capture fails (`FrameArrived` timeout, `E_INVALIDARG`), 52 comments** — Long-running (since Sept) Computer Use reliability bug. Window detection and capture are unreliable across apps. Community has been iterating on repros; a third-party repair tool appeared in Discussions (see §5).

3. **[#59855](https://github.com/openai/codex/issues/59855) — Windows Desktop fails to resume cloud-managed chats (`AbsolutePathBuf` error), 25 comments** — Path deserialization bug breaks resume of cloud chats from the sidebar. **Why it matters:** directly degrades the cloud/local hybrid workflow Windows users rely on.

4. **[#14923](https://github.com/openai/codex/issues/14923) — [CLOSED] Explicit cross-thread orchestration over `thread/*`/`turn/*` primitives, 18 comments** — Closed after a long design discussion about first-class multi-thread orchestration in the Desktop app. **Why it matters:** its closure likely signals an accepted direction (or consolidation) for agent orchestration — worth watching for follow-up issues/PRs.

5. **[#59931](https://github.com/openai/codex/issues/59931) — Windows Desktop doesn't auto-connect enabled SSH host after restart, 12 comments** — Regression with remote CLI `0.167.0`; reproduced on two devices. Remote development ergonomics.

6. **[#19975](https://github.com/openai/codex/issues/19975) — Desktop replay/fork hides steered user messages, 8 comments** — Mid-turn steered messages exist in raw JSONL but vanish in UI replay/fork; some prior message bodies stay expanded. **Why it matters:** auditability and trust in session history.

7. **[#15444](https://github.com/openai/codex/issues/15444) — Let Codex rename the current thread when asked, 7 comments / 19 👍** — Small, clearly-scoped UX enhancement with strong upvote ratio. Natural-language thread management.

8. **[#36266](https://github.com/openai/codex/issues/36266) — Improve Subagents panel with live activity + per-agent config, 6 comments** — As models lean harder on subagents, observability into what each agent is doing and how it's configured becomes critical.

9. **[#30971](https://github.com/openai/codex/issues/30971) — Shell snapshots can trigger credential prompts and persist secret env vars in plaintext, 6 comments** — **Security-relevant:** `.zshrc` with credential helpers (e.g. `op read`) can leak exported secrets into `~/.codex/shell_snapshots`. Serious for anyone with secret-manager-backed shells.

10. **[#60676](https://github.com/openai/codex/issues/60676) — GPT-6.1 Sol disappears from refreshed model catalog (Linux, Plus), 3 comments** — Fresh today. Model present in bundled catalog but missing after refresh. **Why it matters:** model availability regressions are high-severity; watch for a rapid fix.

*Honorable mentions:* [#60529](https://github.com/openai/codex/issues/60529) cold `thread/list` returns premature `nextCursor:null` (app-server pagination correctness), [#59262](https://github.com/openai/codex/issues/59262) severe perf regression + quota depletion, [#8581](https://github.com/openai/codex/issues/8581) usage/billing cycle misalignment on Pro→Plus downgrade.

---

## 4. Key PR Progress

Most PRs below are authored by `copyberry[bot]` (internal automation) and are marked CLOSED, indicating merged or landed work.

1. **[#60647](https://github.com/openai/codex/pull/60647) — Restore PowerShell working directory in MXC sandbox** — Fixes `$PWD` being unset when a workspace has an unreadable ancestor, which broke relative paths. Guarded workaround prefixes PowerShell commands.

2. **[#60624](https://github.com/openai/codex/pull/60624) — Preserve script quotes in Windows sandbox `cmd` invocations** — Uses `/s /c` with an outer quote pair to stop CRT escaping from corrupting quoted paths/exe names. Directly relevant to the Windows sandbox pain points above.

3. **[#60638](https://github.com/openai/codex/pull/60638) — Honor read-only MCP tool hints in automatic approval** — Gives `readOnlyHint: true` precedence over `destructiveHint`/`openWorldHint`, so read-only MCP tools stop requiring approval in auto mode.

4. **[#60639](https://github.com/openai/codex/pull/60639) — Refresh Apps tools outside the MCP runtime gate** — Stops holding the session MCP gate during `tools/list`, preventing stale refresh results from publishing after auth/config changes.

5. **[#60646](https://github.com/openai/codex/pull/60646) — Use trusted Guardian templates for managed policies** — Prevents user-configured model catalogs from supplying Guardian prompt templates that omit policy slots or inject conflicting instructions. **Security hardening.**

6. **[#60670](https://github.com/openai/codex/pull/60670) — Initialize Guardian evidence cursors from thread history state** — Adds `EvidenceCursor::at_thread_ready` with paginated-history validation so new/cleared threads start at position zero consistently.

7. **[#60662](https://github.com/openai/codex/pull/60662) — Gateway device-code sign-in in headless TUI sessions** — Enables auth over SSH / headless Linux where no local browser exists. Important for remote dev.

8. **[#60655](https://github.com/openai/codex/pull/60655) — Environment updates in app-server settings APIs** — Adds experimental `environments` to `turn/settings/update` and `thread/settings/update`, with turn-scoped vs. thread-scoped semantics.

9. **[#60651](https://github.com/openai/codex/pull/60651) — Model and reasoning-effort selection for Cloud conversations** — Cloud conversations can now fetch the account-scoped catalog on demand and change models/reasoning effort from the TUI.

10. **[#60635](https://github.com/openai/codex/pull/60635) — Archive and restore cloud threads** — Adds cloud archive/unarchive, archived listing, detach-on-archive, and archive notifications; reports loaded cloud threads from live attachments.

*Also notable:* [#60457](https://github.com/openai/codex/pull/60457) Desktop hotfix backport (`incremental_tools_legacy` for Bedrock), [#60669](https://github.com/openai/codex/pull/60669) skip submodule scans via `--ignore-submodules=dirty` (mitigates clean-filter execution from task-writable Git config), [#60671](https://github.com/openai/codex/pull/60671) macOS sign-only mode + stricter cert/team-ID verification.

---

## 5. Hot Discussions

**Show and tell**
- [#54474](https://github.com/openai/codex/discussions/54474) — *Windows Computer Use repair for Korean Chrome URL detection.* A version-bound patch for a localized CUA document-control-type bug, with linked bug report. Community-driven workaround for #49383-adjacent problems.
- [#60358](https://github.com/openai/codex/discussions/60358) — *10 MIT-licensed habits for running Codex (or any agent) safely.* Short READMEs + copy-paste snippets for unattended tool use (e.g. "stop when a field or permission is unclear").
- [#59121](https://github.com/openai/codex/discussions/59121) — *agent-harness:* skills, rules, and Stop hooks to make Codex finish the job and ask one thing at a time. Addresses the "agent stops halfway / hands back a to-do list" failure mode.
- [#60441](https://github.com/openai/codex/discussions/60441) — *Tapproval:* answer Codex approvals on an Apple Watch, each risk-tagged, via `hooks.json` + a local open-source helper.
- [#60593](https://github.com/openai/codex/discussions/60593) — *RegressGuard:* a small Python CLI for regression-checking prompt changes against a case file of saved replies. Useful for prompt CI.
- [#49920](https://github.com/openai/codex/discussions/49920) — Mobile app development vendor solicitation; low relevance to Codex tooling, included for completeness.

**Trend:** the community is building a tooling layer *around* Codex — hooks, watch approvals, prompt regression, safety habits — signaling demand for more first-class extensibility (hooks, Stop hooks, approval routing).

---

## 6. Feature Request Trends

- **Thread/session lifecycle control:** rename thread on request ([#15444](https://github.com/openai/codex/issues/15444)), client-supplied thread IDs for recoverable timed-out creates ([#41887](https://github.com/openai/codex/issues/41887)), explicit cross-thread orchestration ([#14923](https://github.com/openai/codex/issues/14923), closed), archive/restore cloud threads (PR [#60635](https://github.com/openai/codex/pull/60635)).
- **Subagent observability & configuration:** live activity and per-agent config in the Subagents panel ([#36266](https://github.com/openai/codex/issues/36266)).
- **Headless/automation parity:** synchronously generate memories for a specific session via `codex exec` ([#29430](https://github.com/openai/codex/issues/29430)); device-code sign-in for headless TUI (PR [#60662](https://github.com/openai/codex/pull/60662)).
- **Cloud/remote model control:** model + reasoning-effort selection for Cloud conversations (PR [#60651](https://github.com/openai/codex/pull/60651)); environment settings in app-server APIs (PR [#60655](https://github.com/openai/codex/pull/60655)).
- **Extensibility hooks:** community-built approval routing, Stop hooks, and prompt regression tooling (Discussions §5) point to demand for official, documented hook surfaces.

---

## 7. Developer Pain Points

1. **Windows is the dominant failure surface.** VS Code extension UI not starting ([#56655](https://github.com/openai/codex/issues/56655)), sandbox `helper_sandbox_lock_failed` / Win32 error 5 on `.sandbox-bin` ([#60057](https://github.com/openai/codex/issues/60057)), continued sandbox access denials ([#59744](https://github.com/openai/codex/issues/59744)), "Blocked by Policy" despite Full Access ([#43905](https://github.com/openai/codex/issues/43905)), and CUA screenshot timeouts ([#49383](https://github.com/openai/codex/issues/49383), [#60666](https://github.com/openai/codex/issues/60666)). Sandbox ACL repair attempts have not fully resolved the `WRITE_DAC` class of failures.
2. **Connectivity & request failures:** workspace-routing discovery timeouts / 5/5 reconnects ([#57063](https://github.com/openai/codex/issues/57063)); zstd-compressed repeated image inputs returning HTTP 400 while uncompressed succeeds ([#60594](https://github.com/openai/codex/issues/60594)).
3. **Credential & secret handling:** shell snapshots persisting exported secret env vars in plaintext ([#30971](https://github.com/openai/codex/issues/30971)) — the most security-critical open item.
4. **Quota/billing opacity:** usage cycles misaligned with ChatGPT billing on Pro→Plus downgrade ([#8581](https://github.com/openai/codex/issues/8581)); banked resets showing a future expiry but expiring without resetting ([#60664](https://github.com/openai/codex/issues/60664)); abnormal weekly quota depletion alongside perf regressions ([#59262](https://github.com/openai/codex/issues/59262)). Users want transparent, predictable quota accounting.
5. **Resource exhaustion:** whole-Mac freezes under memory pressure during Codex use on 8 GB machines ([#56586](https://github.com/openai/codex/issues/56586)).
6. **Correctness of surfaces:** app-server cold `thread/list` returning premature `nextCursor:null` ([#60529](https://github.com/openai/codex/issues/60529)) and status UI conflating context-window usage with cumulative tokens ([#21799](https://github.com/openai/codex/issues/21799), closed) undermine programmatic and human trust in reported state.

---

*Digest generated from github.com/openai/codex data for 2026-11-11. Comment/👍 counts reflect the snapshot provided.*

</details>

<details>
<summary><strong>Gemini CLI</strong> — <a href="https://github.com/google-gemini/gemini-cli">google-gemini/gemini-cli</a></summary>

# Gemini CLI Community Digest — 2026-11-11

## 1. Today's Highlights
Two releases shipped in the last 24 hours: the v0.69.0-preview.0 cut (with a `$`-escaping fix and a new `/stats heatmap` subcommand) and the stable v0.68.0 release. The issue tracker remains dominated by subagent reliability concerns — misreported GOAL status, hangs in the generalist agent, and configuration overrides being ignored are all drawing sustained maintainer attention. On the PR side, the biggest themes are release-infrastructure fixes (stale build artifacts bloating the npm tarball) and hardening shell-command safety checks.

## 2. Releases
**v0.69.0-preview.0**
- `fix(core,cli)`: Preserves literal `$` characters during template placeholder replacement ([#30136](https://github.com/google-gemini/gemini-cli/pull/30136))
- `feat(cli)`: Adds a `/stats heatmap` subcommand for daily token usage

**v0.68.0**
- Auto-generated changelogs for v0.67.0-preview.0 and v0.66.0
- `docs(readme)`: Clarifies Gemini CLI scope, limitations, and tool capabilities

## 3. Hot Issues

1. **[#22323](https://github.com/google-gemini/gemini-cli/issues/22323) — Subagent MAX_TURNS reported as GOAL success (P1, 13 comments).** The `codebase_investigator` subagent reports `status: "success"` / `Termination Reason: "GOAL"` even when it hits the turn limit without doing any analysis. This masks failures and corrupts downstream agent orchestration — the highest-engagement issue of the day.

2. **[#21409](https://github.com/google-gemini/gemini-cli/issues/21409) — Generalist agent hangs indefinitely (P1, 8 comments, 8 👍).** Any deferral to the generalist agent hangs forever, even for trivial tasks like folder creation. The workaround (instructing the model not to use subagents) effectively disables a core feature; the 👍 count signals broad impact.

3. **[#19873](https://github.com/google-gemini/gemini-cli/issues/19873) — Zero-dependency OS sandboxing + post-execution intent routing (P2, 9 comments).** Proposes leaning into Gemini 3's native bash affinity by chaining POSIX tools, while containing risk through sandboxing and intent routing. A significant architectural proposal for the agent execution model.

4. **[#21968](https://github.com/google-gemini/gemini-cli/issues/21968) — Gemini under-uses skills and sub-agents (P2, 7 comments).** Anecdotal but important: the model rarely invokes custom skills/subagents unless explicitly told, even when descriptions clearly match. Undermines the value of the extensibility system.

5. **[#22745](https://github.com/google-gemini/gemini-cli/issues/22745) — EPIC: AST-aware file reads, search, and mapping (P2, 7 comments).** Tracks whether AST-aware tools reduce misaligned reads, turn counts, and token noise. A likely direction for future tooling quality improvements.

6. **[#22267](https://github.com/google-gemini/gemini-cli/issues/22267) — Browser Agent ignores `settings.json` overrides (P2, 5 comments).** The AgentRegistry merges settings correctly at init, but the Browser Agent bypasses them (e.g., `maxTurns`). Configuration drift erodes trust in project-level config.

7. **[#21983](https://github.com/google-gemini/gemini-cli/issues/21983) — Browser subagent fails on Wayland (P1, 4 comments).** Linux/Wayland users see the browser agent finish with "GOAL" despite failing — another instance of the misleading termination-status pattern.

8. **[#24246](https://github.com/google-gemini/gemini-cli/issues/24246) — 400 error with >128 tools (P2, 3 comments).** Large tool inventories trigger API 400s; the expectation is smarter scoping of enabled tools. Relevant as MCP servers multiply tool counts.

9. **[#22672](https://github.com/google-gemini/gemini-cli/issues/22672) — Agent should discourage destructive behavior (P2, 3 comments).** The model occasionally reaches for `git reset` or `--force` when safer alternatives exist. Safety-adjacent and tied to the shell-heuristics PRs below.

10. **[#18836](https://github.com/google-gemini/gemini-cli/issues/18836) — Replace WriteToDo with persistent file-based task tracking (P3, 2 comments).** Proposes deprecating in-context todo tracking to combat context rot, token cost, and memory loss between sessions — a recurring theme in agent-state discussions.

## 4. Key PR Progress

1. **[#30207](https://github.com/google-gemini/gemini-cli/pull/30207) — Clear `bundle/` before esbuild (P0).** Nightly releases have failed since 2026-11-05 with `413 Request Entity Too Large`; ~70 MB of the ~106 MB tarball JS is stale output from prior builds. Critical release-infra fix.

2. **[#30171](https://github.com/google-gemini/gemini-cli/pull/30171) — Flag recursive/forced `rm` regardless of flag position (P1).** Closes a `PolicyEngine` bypass where forms like `rm -r -f`, `rm -Rf`, `rm --recursive --force`, and trailing-flag variants were classified as safe. Directly addresses the destructive-command concern in #22672.

3. **[#30095](https://github.com/google-gemini/gemini-cli/pull/30095) — Prevent Windows git diff permission bypass (P1, security).** Extracts shared Git safety validation and rejects dangerous arguments on Windows, closing a read-only-command classification gap.

4. **[#30219](https://github.com/google-gemini/gemini-cli/pull/30219) — Record ACP tool calls on active chat after compression.** Prevents post-compression tool calls from attaching to removed messages, avoiding 400 errors on session reload.

5. **[#30217](https://github.com/google-gemini/gemini-cli/pull/30217) — Fix PDF/telemetry buffer memory retention.** Addresses a V8 heap OOM during multi-turn PDF ingestion when telemetry is disabled (the default).

6. **[#30020](https://github.com/google-gemini/gemini-cli/pull/30020) — Drop tool calls streamed by failed retry attempts.** A subtle but serious correctness fix: previously both the failed and successful attempt's tool calls were executed, duplicating side-effecting commands.

7. **[#30220](https://github.com/google-gemini/gemini-cli/pull/30220) — Preserve Unicode in session previews, strip ANSI.** Replaces an ASCII-only regex in `cleanMessage()` that mangled international scripts, accents, and emojis.

8. **[#30214](https://github.com/google-gemini/gemini-cli/pull/30214) — Recognize TortoiseGitPlink as PuTTY-family SSH client.** Ensures `-batch` is injected instead of OpenSSH's `BatchMode` flag for Windows users of TortoiseGit.

9. **[#30089](https://github.com/google-gemini/gemini-cli/pull/30089) — Bump npm-dependencies group (70 updates).** Includes `@modelcontextprotocol/sdk` 1.23.0 → 1.32.2 — notable for MCP compatibility.

10. **[#29960](https://github.com/google-gemini/gemini-cli/pull/29960) — Telemetry `SERVICE_VERSION` uses CLI version, not Node runtime.** Fixes enterprise telemetry dashboards reporting the wrong service version.

## 5. Hot Discussions
No discussion data was provided for this period; this section is omitted.

## 6. Feature Request Trends

- **Subagent observability & correctness**: Surface subagent trajectories via `/chat share` ([#22598](https://github.com/google-gemini/gemini-cli/issues/22598)), include subagent context in `/bug` reports ([#21763](https://github.com/google-gemini/gemini-cli/issues/21763)), and fix termination-status reporting (#22323).
- **AST-aware tooling**: A cluster of issues (#22745, #22746, #22747) explores AST grep/tilth/glyph for precise reads, searches, and codebase mapping.
- **Persistent, file-based agent state**: Move beyond in-context todo lists (#18836) and experiment with native file tools for task tracking (#21000).
- **Safer execution**: Sandboxing (#19873), dangerous-command discouragement (#22672), and per-workspace policies (#18397).
- **Smarter tool scoping**: Dynamic tool selection to avoid the >128-tool 400 error (#24246).

## 7. Developer Pain Points

- **Subagent reliability is the top friction point**: hangs (#21409), false-success reporting (#22323, #21983), ignored configuration (#22267), and under-triggering (#21968) collectively suggest the subagent system needs a correctness pass before broader adoption.
- **Release pipeline instability**: repeated nightly failures from stale build artifacts (#30207) and flaky VS Code download retries (#29951) are consuming maintainer cycles.
- **Safety heuristics have gaps**: multiple PRs (#30171, #30095) reveal that dangerous `rm` and Git invocations could slip past the PolicyEngine depending on argument order or platform.
- **Retry semantics can duplicate side effects**: the mid-stream retry bug (#30020) is the kind of issue that erodes trust in autonomous execution.
- **Token/context efficiency**: context rot, firehose file reads (#19561), and the ~36.6k-token baseline per turn remain persistent concerns driving the AST and persistent-state proposals.

</details>

<details>
<summary><strong>GitHub Copilot CLI</strong> — <a href="https://github.com/github/copilot-cli">github/copilot-cli</a></summary>

# GitHub Copilot CLI Community Digest — 2026-11-11

## 1. Today's Highlights

The CLI shipped a rapid triple-release sequence (v1.0.109, v1.0.109-1, v1.0.110-0/-1) headlined by **Claude Fable 5.2** in the model picker and **IPv4/IPv6 CIDR support** for sandbox host allow/deny rules. An experimental masked-dialog flow for handling git/ssh/sudo password prompts is now available behind `/experimental on`. Meanwhile, a large batch of MCP-related issues was triaged/closed, though the MCP area continues to dominate the tracker with orphaned processes, OAuth callback, and protocol-negotiation problems.

---

## 2. Releases

### v1.0.110-1
**Fixed**
- A session reopened with `copilot --resume` is now marked in-use immediately, preventing `copilot -p --resume` and a second window from writing into an idle session.
- Custom status lines preserve the first row's indentation for consistent alignment.
- Control characters are now shown visibly.

### v1.0.110-0
**Added**
- *Experimental:* answer git, ssh, and sudo password prompts from agent commands in a masked dialog (enable via `/experimental on`).

**Improved**
- Selecting an unavailable account in `/user switch` shows recovery guidance and a retry action.
- Clicking anywhere in expanded reasoning collapses it.

### v1.0.109 / v1.0.109-1
**Added**
- IPv4/IPv6 CIDR support for sandbox host allow/deny rules in settings.
- Claude Fable 5.2 support in the model picker under "New models."
- `copilot help input` clarifies Backspace at the start of shell input leaves shell mode and preserves typed text.
- Sandbox path settings now support environment variables while preserving authored expressions.

---

## 3. Hot Issues

1. **[#4438](https://github.com/github/copilot-cli/issues/4438) — `disable-model-invocation: true` makes skills unreachable (17 comments, 👍20)**
   A frontmatter flag intended to make a skill "manual-only" actually renders it entirely inaccessible — `skill()` returns `Skill not found`. Highest-engagement issue of the day; users want a true manual-invocation escape hatch.

2. **[#191](https://github.com/github/copilot-cli/issues/191) — MCP servers configured but tools not exposed (15 comments, 👍15)**
   A long-running (since Oct 2025) MCP integration gap, now closed. The high comment count signals persistent community frustration with MCP tool visibility.

3. **[#5283](https://github.com/github/copilot-cli/issues/5283) — Remote hosting blocked on Copilot Free (👍5)**
   Personal-account users with Copilot Free cannot enable remote environment hosting despite local settings showing enabled. Notable for the free-tier gating implications.

4. **[#5171](https://github.com/github/copilot-cli/issues/5171) — Windows NumLock-off numpad & Delete ignored**
   A keyboard-input regression on Windows 11. Small but disruptive for Windows Terminal users; reflects ongoing platform-parity gaps.

5. **[#4888](https://github.com/github/copilot-cli/issues/4888) — Legacy `initialize` sent after successful `server/discover`**
   A protocol-correctness bug affecting dual-era MCP SDK servers — the CLI negotiates modern protocol then sends a legacy handshake. Matters for MCP SDK interoperability.

6. **[#5402](https://github.com/github/copilot-cli/issues/5402) — MCP Entra silent sign-in uses `/common`, fails for single-tenant apps (AADSTS50194)**
   Since 1.0.107, Windows users must run `/mcp auth` every session. Significant enterprise-authentication regression.

7. **[#5404](https://github.com/github/copilot-cli/issues/5404) — Launching copilot in git repo from pwsh kills parent `pwsh.exe`**
   A serious Windows stability bug (tgrep indexing): the parent PowerShell process terminates with an unhandled exception, taking attached processes with it.

8. **[#5369](https://github.com/github/copilot-cli/issues/5369) — Allow a model block list in fusion/auto mode**
   Users want to exclude specific models from auto-routing. Reflects growing demand for model-routing control as the model catalog expands.

9. **[#5388](https://github.com/github/copilot-cli/issues/5388) — Sandbox credential masking ineffective for `GH_TOKEN`/`GH_ENTERPRISE_TOKEN` on GHE data-residency**
   Token env vars are dropped before masking on `<tenant>.ghe.com`. A security-relevant sandbox gap for enterprise tenants (now closed).

10. **[#5397](https://github.com/github/copilot-cli/issues/5397) — Copilot Connectors unavailable in `--acp` mode**
    Connectors working interactively don't surface in ACP sessions. Important for editor/ACP-client integrations.

---

## 4. Key PR Progress

Only **one** PR was updated in the last 24h:

1. **[#4770](https://github.com/github/copilot-cli/pull/4770) — Document the WebSocket responses opt-out (OPEN)**
   Documents the escape hatch for models that default to a WebSocket responses endpoint. When that transport is unusable (network blocks WebSocket, or sessions fail with "400 input item ID does not belong to this connection"), users can opt out. Still open since Sept 2026 — worth watching for docs completeness.

*Note: The remaining PR queue showed no activity in the 24h window, so no further items are listed.*

---

## 5. Hot Discussions

No Discussion data was provided in the source dataset; this section is omitted.

---

## 6. Feature Request Trends

- **Model routing control** — Block lists for fusion/auto mode ([#5369](https://github.com/github/copilot-cli/issues/5369)); clearer model/provider interactions with `providers.json` ([#5403](https://github.com/github/copilot-cli/issues/5403)).
- **MCP OAuth flexibility** — Fixed callback ports ([#5391](https://github.com/github/copilot-cli/issues/5391)) and plugin-level OAuth options (`oauthClientId`, `redirectPort`) ([#5410](https://github.com/github/copilot-cli/issues/5410)) for pre-registered-client auth servers.
- **Enterprise/proxy networking** — NTLM fallback for Negotiate-authenticated proxies ([#5407](https://github.com/github/copilot-cli/issues/5407)); better Windows credential-storage diagnostics ([#5399](https://github.com/github/copilot-cli/issues/5399)).
- **Tooling ecosystem integration** — Use system-installed `gh` instead of bundled `gh.exe` ([#4456](https://github.com/github/copilot-cli/issues/4456)); connector parity in ACP mode ([#5397](https://github.com/github/copilot-cli/issues/5397)).
- **Terminal UX** — Keep user-entered `!` shell commands out of automatic Shell grouping ([#5409](https://github.com/github/copilot-cli/issues/5409)).

---

## 7. Developer Pain Points

- **MCP lifecycle & cleanup** — Orphaned stdio processes after auth rebuild ([#4392](https://github.com/github/copilot-cli/issues/4392)), Docker MCP containers surviving session close ([#4461](https://github.com/github/copilot-cli/issues/4461)), and inotify-limit (`ENOSPC`) watch failures stalling sessions on Linux ([#5242](https://github.com/github/copilot-cli/issues/5242)).
- **MCP authentication friction** — Repeated per-session auth ([#5402](https://github.com/github/copilot-cli/issues/5402)), random loopback OAuth ports breaking pre-registered clients ([#5391](https://github.com/github/copilot-cli/issues/5391)), and registry reachability issues ([#4949](https://github.com/github/copilot-cli/issues/4949)).
- **Windows platform stability** — Parent `pwsh.exe` crashes ([#5404](https://github.com/github/copilot-cli/issues/5404)), keyboard input regressions ([#5171](https://github.com/github/copilot-cli/issues/5171)), and window-callback display bugs ([#5398](https://github.com/github/copilot-cli/issues/5398)).
- **Sandbox security correctness** — Credential masking gaps on GHE data-residency tenants ([#5388](https://github.com/github/copilot-cli/issues/5388)); continues to be a sensitive area.
- **Tool output fidelity** — Image pixels not reaching the model (`view`, Playwright screenshots) ([#5405](https://github.com/github/copilot-cli/issues/5405)); content-filter failures consuming AI credits ([#5408](https://github.com/github/copilot-cli/issues/5408)).

---

*Digest compiled from github.com/github/copilot-cli data ending 2026-11-11.*

</details>

<details>
<summary><strong>OpenCode</strong> — <a href="https://github.com/anomalyco/opencode">anomalyco/opencode</a></summary>

# OpenCode Community Digest — 2026-11-11

## Today's Highlights
OpenCode shipped v2.2.7, headlined by a **GitHub Copilot "Auto" model option** that lets Copilot's router pick the model per session at the Auto discount, with a graceful fallback when routing fails. Meanwhile, the issue tracker remains dominated by the still-unresolved **free tier usage-limit problem (#59387, 346 comments)**, plus a cluster of v2.2.x regressions touching Plan mode enforcement, Windows console-window flashing, and plugin cache staleness. On the PR side, the team is actively landing background-subagent and desktop/CLI robustness work.

---

## Releases

**v2.2.7** — [Release page](https://github.com/anomalyco/opencode/releases)
- **GitHub Copilot Auto model selection** ([#62997](https://github.com/anomalyco/opencode/pull/62997)): When your Copilot account has an Auto pool, selecting **Auto** in the model picker lets Copilot's router choose the model per session at the Auto discount. If routing fails, OpenCode falls back to the account's default chat model.

---

## Hot Issues

1. **[#59387 [OPEN] Free tier issues — read this first](https://github.com/anomalyco/opencode/issues/59387)** — The central triage thread for free-tier failures ("usage exceeded," limits that never reset, desktop version gating). At **346 comments** it's the highest-traffic open issue; treat as the canonical signal for go-to-market/billing friction.
2. **[#60250 [OPEN] Plan mode enforcement broken in v2.2.1](https://github.com/anomalyco/opencode/issues/60250)** — Plan agent immediately offers to implement edits instead of only planning. Core workflow regression for anyone relying on plan-then-execute discipline.
3. **[#62659 [CLOSED] All sessions lost — data dir recreated (2.2.5→2.2.6)](https://github.com/anomalyco/opencode/issues/62659)** — Total transcript loss on upgrade via a schema bootstrap that started fresh. Severity is extreme (permanent data loss), even though now closed — worth verifying the fix in 2.2.7.
4. **[#52266 [OPEN] Windows console windows flashing (~1/sec)](https://github.com/anomalyco/opencode/issues/52266)** — Spawns lack `CREATE_NO_WINDOW`, causing per-second `git.exe` console flashes. A re-report of prior issues; long-lived Windows platform debt.
5. **[#59461 [OPEN] Crash on startup (Error 134 / missing SSE4.2)](https://github.com/anomalyco/opencode/issues/59461)** — New Bun runtime requires SSE4.2, crashing older CPUs on the desktop client. Hardware-compatibility regression.
6. **[#49742 [OPEN] Message timestamp toggle missing in CLI v2](https://github.com/anomalyco/opencode/issues/49742)** — A v1 `Ctrl+P` capability dropped in v2.0.8. Representative of accumulated v1→v2 feature-parity gaps (8 👍).
7. **[#63188 [OPEN] `session.move` doesn't update project assignment](https://github.com/anomalyco/opencode/issues/63188)** — Moved sessions keep their old `projectID`, so they stay filed under the wrong project. Data-model correctness bug in the sessions API.
8. **[#63075 [OPEN] [FEATURE] Deny rules should carry a message to the model](https://github.com/anomalyco/opencode/issues/63075)** — Deny-rule hits return a fixed `Permission denied` string with no reason/alternative, unlike the `ask` flow. Directly affects agent self-correction quality.
9. **[#63174 [OPEN] [reproduced] Invalid `retry-after-ms` overrides valid `Retry-After`](https://github.com/anomalyco/opencode/issues/63174)** — An empty/negative `retry-after-ms` yields a 0 ms delay instead of honoring the header, causing aggressive retries. Networking correctness for provider resilience.
10. **[#63171 [CLOSED] Turn settles as "succeeded" with zero content](https://github.com/anomalyco/opencode/issues/63171)** — Answer fully trapped in the reasoning channel is recorded as a normal completion. Silent-failure observability gap.

---

## Key PR Progress

1. **[#63210 [OPEN] Always run subagents in the background](https://github.com/anomalyco/opencode/pull/63210)** — Removes the `background` option; every subagent call returns a `sessionID` immediately. Stacked on #63209.
2. **[#63209 [OPEN] Report subagent results to the parent on every idle](https://github.com/anomalyco/opencode/pull/63209)** — Makes background subagents observable beyond the one-shot job observer, enabling interactive/typed-into children.
3. **[#63196 [OPEN] Pipe mode on Windows (process daemon)](https://github.com/anomalyco/opencode/pull/63196)** — Adds `create_pipe` with `CREATE_NO_WINDOW`, directly addressing the console-flash class of bugs (#52266).
4. **[#63208 [OPEN] Stop freezing main process during login shell env probe](https://github.com/anomalyco/opencode/pull/63208)** — Replaces `spawnSync` env capture with async; fixes startup UI freeze up to 5 s.
5. **[#63213 [OPEN] Scope a client MCP connection to one session](https://github.com/anomalyco/opencode/pull/63213)** — Optional `sessionID` param for client MCP connections; improves isolation for multi-session MCP use.
6. **[#63197 [OPEN] Preserve Claude subscription limit errors](https://github.com/anomalyco/opencode/pull/63197)** — Reclassifies plain `Error` from Claude Agent so limits aren't surfaced as `Unknown`.
7. **[#62729 [OPEN] CLI fails instead of hanging when event stream never opens](https://github.com/anomalyco/opencode/pull/62729)** — Closes #62681; fixes unbounded waits in `opencode run` / ACP / TUI reconnect loops.
8. **[#63152 [OPEN] Update npm installs through their own prefix](https://github.com/anomalyco/opencode/pull/63152)** — Fixes `opencode upgrade` under fnm/nvm by using the install's own npm rather than PATH npm.
9. **[#61299 [OPEN] @-mention files outside the project directory (TUI)](https://github.com/anomalyco/opencode/pull/61299)** — Ports the V1 behavior (#16464) to V2; a frequently requested ergonomics win.
10. **[#60767 [OPEN] Handle stream errors without messages](https://github.com/anomalyco/opencode/pull/60767)** — Fixes schema rejection of OpenAI-compatible error events lacking a `message` field.

---

## Feature Request Trends

- **Model catalog transparency & deployment control**: Pin/verify exact model checkpoints and serve specific builds ([#41969](https://github.com/anomalyco/opencode/issues/41969), [#63110](https://github.com/anomalyco/opencode/issues/63110)).
- **Agent context & permission control**: Edit/delete context messages ([#7712](https://github.com/anomalyco/opencode/issues/7712)), and make deny rules explain themselves to the model ([#63075](https://github.com/anomalyco/opencode/issues/63075)).
- **Desktop UX polish**: Keyboard-shortcut help overlay ([#63190](https://github.com/anomalyco/opencode/issues/63190)), keep-awake while agents run ([#63082](https://github.com/anomalyco/opencode/issues/63082)).
- **Billing flexibility**: PayPal support for Console credits / Go subscriptions ([#62639](https://github.com/anomalyco/opencode/issues/62639)).
- **Internationalization**: Speech-recognition language setting on iOS ([#56973](https://github.com/anomalyco/opencode/issues/56973)).

---

## Developer Pain Points

- **Free-tier reliability & billing clarity** is the single loudest signal (#59387), compounded by requests for transparent model versions/checkpoints on paid plans.
- **v2.x migration friction**: missing v1 CLI capabilities (timestamps), Plan-mode enforcement regressions, and a catastrophic session-data-loss upgrade path (#62659) erode upgrade confidence.
- **Windows platform quality**: console-window flashing, stale-path upgrades under fnm/nvm, and older-CPU/SSE4.2 crashes persist across releases.
- **Silent/ambiguous failures**: zero-content "succeeded" turns, stale auth state after `DELETE /auth`, MCP/plugin cache not invalidating, and retry-header parsing bugs all reduce trust in agent outcomes.
- **Subagent & MCP ergonomics**: background execution and per-session MCP scoping are the active frontier the team is addressing this week.

</details>

<details>
<summary><strong>Pi</strong> — <a href="https://github.com/earendil-works/pi">earendil-works/pi</a></summary>

# Pi Community Digest — 2026-11-11

## 1. Today's Highlights

Pi shipped v1.5.1 and v1.5.2, adding standalone shell auth commands (`pi auth login/logout`) and a fork flow for sessions whose working folder has been moved or deleted. Issue activity was dominated by provider-integration friction: Anthropic OAuth version pinning, missing Azure/OpenAI auth paths, and stale model catalogs. A significant cluster of fixes landed around session state, codemode globals, and tool-search ranking.

## 2. Releases

**v1.5.2**
- **Fork sessions whose folder is gone**: sessions whose original directory was moved/deleted can now be forked into the current folder instead of only continued, eliminating repeated resume prompts.
  https://github.com/earendil-works/pi/releases/tag/v1.5.2

**v1.5.1**
- **Shell login and logout**: `pi auth login <provider>` / `pi auth logout <provider>` now work without starting a session, with API-key and no-browser sign-in options.
  https://github.com/earendil-works/pi/releases/tag/v1.5.1

## 3. Hot Issues

1. **#10956 — `cacheWarming: "idle"` silently stops** (13 comments, 👍3). Idle cache warming never fires once an extension handles `turn_end` and the context holds a custom/compaction message — 507 decisions logged, all at `continuationProbability: 1`. A high-traffic cost/latency regression for extension users.
   https://github.com/earendil-works/pi/issues/10956
2. **#10074 — Anthropic corrupted non-ASCII edit args** (CLOSED, 12 comments). Dropped `u` in `\uXXXX` silently converts Korean text edits into `\b`/`\f` control chars, occasionally corrupting files. A correctness/data-integrity issue that took weeks to track.
   https://github.com/earendil-works/pi/issues/10074
3. **#11021 — ChatGPT sign-in reports 272K for GPT-6.1 Sol vs 500K catalog** (10 comments, 👍8). Pi advertises a smaller context/compaction budget than the account's live catalog. Strong community agreement; direct impact on long-context workflows.
   https://github.com/earendil-works/pi/issues/11021
4. **#7062 — openai-completions array content / missing finish_reason** (9 comments, 👍3). Non-standard Databricks streaming responses yield `[object Object]` content. Long-running provider-compat gap.
   https://github.com/earendil-works/pi/issues/7062
5. **#10812 — Stale default model silently falls back** (7 comments). A renamed model in `settings.json` causes a silent switch to a different model with no warning — surprising and hard to debug.
   https://github.com/earendil-works/pi/issues/10812
6. **#11112 — Let `prepareArguments` repair unparseable tool args** (CLOSED, 6 comments). Sonnet 5 typos like `"oldText">` collapse to `arguments: {}`; a proposed repair hook would recover these turns.
   https://github.com/earendil-works/pi/issues/11112
7. **#10536 — Cache warming stops for virtual models** (4 comments, 👍1). `registerVirtualModel` sessions report warmer inactive with "conversation context changed" — same root cause family as #10956.
   https://github.com/earendil-works/pi/issues/10536
8. **#10539 — Cloudflare AI Gateway catalog skips DeepSeek/xAI/Qwen** (3 comments). `/model` only lists `openai/*`, `anthropic/*`, `workers-ai/*`, despite gateway support and models.dev coverage.
   https://github.com/earendil-works/pi/issues/10539
9. **#11989 — Anthropic OAuth pins `claude-cli/2.1.280`, rejected (needs 2.1.290)** (CLOSED, 3 comments). Subscription OAuth blocked on newer models due to a hardcoded CLI version string.
   https://github.com/earendil-works/pi/issues/11989
10. **#11956 — P0 crash on cancelling a read during file open** (2 comments). Uncaught `AbortError` terminates Pi when a read is cancelled mid-stat; surfaced via codemode `Promise.all`.
    https://github.com/earendil-works/pi/issues/11956

## 4. Key PR Progress

1. **#12016 — Microsoft Entra ID auth for Azure OpenAI** (CLOSED). Adds `DefaultAzureCredential` support so Foundry can be used without an API key — closes a major enterprise gap.
   https://github.com/earendil-works/pi/pull/12016
2. **#11975 — Offer fork when session cwd no longer exists** (CLOSED). Backs v1.5.2's headline feature; previously the "continue" choice was in-memory only.
   https://github.com/earendil-works/pi/pull/11975
3. **#11961 — Harness.close() releases provider resources** (CLOSED). Fixes leaked WebSockets/timers keeping Node processes alive after Codex responses.
   https://github.com/earendil-works/pi/pull/11961
4. **#11986 — Tool-search ranks read-only tools for read-verb queries** (CLOSED). Fixes `gmail_read_email` not matching "read my email" because the name wasn't in the search document.
   https://github.com/earendil-works/pi/pull/11986
5. **#11985 — Codemode wrapper no longer shadows prelude globals** (CLOSED). Removes redundant `tools`/`console` params that shadowed `globalThis` definitions.
   https://github.com/earendil-works/pi/pull/11985
6. **#11978 — Expand env vars in custom `shellPath`** (CLOSED). Fixes `%LOCALAPPDATA%`-style paths failing on Windows.
   https://github.com/earendil-works/pi/pull/11978
7. **#11998 — Ling 3.x Flash models in ant-ling** (CLOSED). Replaces deprecated Ling/Ring 2.6 with Ling 3.0–3.2 Flash; 3.2 adds image input.
   https://github.com/earendil-works/pi/pull/11998
8. **#12002 / #12011 — Session catalog, shares, subagents move to Chord stores** (CLOSED). Makes export/import/delete include the session list and enables Chord outbox-based sync.
   https://github.com/earendil-works/pi/pull/12002 · https://github.com/earendil-works/pi/pull/12011
9. **#11990 — Radius session sync adapter prototype** (OPEN). Introduces a backend boundary for session backup/restore without coupling to Radius HTTP API.
   https://github.com/earendil-works/pi/pull/11990
10. **#12001 / #12005 — llama.cpp default port + simplified setup guide** (OPEN). Standardizes on port `9931` and `llama serve` as the unified entry point.
    https://github.com/earendil-works/pi/pull/12001 · https://github.com/earendil-works/pi/pull/12005

## 5. Hot Discussions

**Ideas**
- **#11984 — Redux/Redux-Saga for Pi state management**. Proposes an effect-based agent orchestration model; author references a Redux-Saga discussion thread but admits they lack the means to implement it. Early-stage, worth watching for architecture debate.
  https://github.com/earendil-works/pi/discussions/11984

## 6. Feature Request Trends

- **Provider authentication breadth**: Entra ID/Azure (#12016), Kimi OAuth host migration (#9819), and ChatGPT sign-in context parity (#11021) all point to demand for first-class, non-API-key auth paths.
- **Catalog fidelity**: Multiple reports (#10539, #10683, #10812, #11021) that the model catalog diverges from what providers/accounts actually offer — users want live catalogs and loud failures on stale config.
- **Extension ergonomics**: npm resolution via `main`/`exports` (#9817), Shiki resolution in standalone builds (#12012), and `StringEnum` typing regressions (#11869) show the extension surface needs hardening.
- **Cache warming reliability**: #10956 and #10536 are the same class of bug across custom messages and virtual models; expect a consolidated fix request.
- **Trust/safety defaults**: #12023 argues `.pi/agents/` should be trust-requiring and that subagent approval gates are model-disableable — a policy discussion likely to grow.

## 7. Developer Pain Points

- **Silent degradation**: Cache warming stopping, default-model fallback, and stale catalogs all fail quietly, forcing users to dig through logs (`/session`, `cache_warming_decision` events).
- **Provider compat fragility**: Non-standard streaming shapes (Databricks), header/version pinning (Anthropic OAuth, Fable 5.2), and PII redaction edge cases (#11928) consume significant triage effort.
- **Argument parsing resilience**: Corrupted Unicode, lone surrogates (#12014), and malformed JSON from models repeatedly break tool calls — users want a repair layer rather than session restarts.
- **Process lifecycle leaks**: #11676/#11961 highlight that resource cleanup on harness shutdown was incomplete, affecting embedding and durable workflows.
- **Standalone binary dependency resolution**: Extensions depending on packages not bundled with the binary (Shiki) fail at runtime, a recurring packaging complaint (#12012).
- **Small-terminal UX**: Footer truncation on narrow/mobile terminals (#12022) remains an unaddressed quality-of-life issue.

</details>

<details>
<summary><strong>Qwen Code</strong> — <a href="https://github.com/QwenLM/qwen-code">QwenLM/qwen-code</a></summary>

# Qwen Code Community Digest — 2026-11-11

## 1. Today's Highlights

The project shipped **v0.27.2** alongside **v0.27.2-preview.0** and **SDK TypeScript v0.1.32** (bundling CLI 0.27.2), with the Web Shell gaining multi-channel configuration for WeChat, QQ, and Email (QR login, identity display, logout). Meanwhile, security and permission-classification defects dominated issue traffic: multiple P1 reports show the AUTO-mode classifier approving destructive commands (`git branch -D`) and misreading redirections, with corresponding fix PRs already open.

---

## 2. Releases

**v0.27.2** — Stable release (release notes pending generation).

**v0.27.2-preview.0** — Preview build including:
- `fix(ci)`: read prettier's full ignore set at the base tip ([#16729](https://github.com/QwenLM/qwen-code/pull/16729))
- `fix(cli,core)`: allow an Agent Host to retry an enrollment

**sdk-typescript-v0.1.32** — Bundles CLI 0.27.2 (built from the same branch/ref as the SDK).

**Web Shell Channels (highlighted in release notes)** — WeChat, QQ, and Email channel configuration, including QR-based login, scan progress, identity display, and logout where the platform supports it ([#16736](https://github.com/QwenLM/qwen-code/pull/16736), [#16724](https://github.com/QwenLM/qwen-code/pull/16724)).

---

## 3. Hot Issues

1. **[#14724](https://github.com/QwenLM/qwen-code/issues/14724)** — *Main CI failed: Managed Agent gate — no test identified* (292 comments). A long-running CI tracking issue that keeps accumulating failures; the volume of comments signals persistent instability in the managed-agent gate rather than a one-off break.

2. **[#16672](https://github.com/QwenLM/qwen-code/issues/16672)** — *Managed Agent gate: HostedExtensionsIT failure on MySQL 8.4 + 11 more* (55 comments). Hosted-extension tests failing under harness replacement/cancellation; broad blast radius across the Java SDK full suite.

3. **[#8662](https://github.com/QwenLM/qwen-code/issues/8662)** — *Migrate TUI rendering from ink to OpenTUI (tracking)* (36 comments). Major terminal-UX roadmap item; checkpoint notes show implementation waves merged and the gate now on installation/platform acceptance.

4. **[#16787](https://github.com/QwenLM/qwen-code/issues/16787)** — *E2E: tui-agent-loop-parity — approved write then declined one* (34 comments). TUI/agent-loop parity regression in an interactive approval flow — directly user-visible behavior.

5. **[#16425](https://github.com/QwenLM/qwen-code/issues/16425)** — *P1: Workflow agent permission approval never renders; run hangs* (7 comments). Reproduced on 0.26.6 and 0.26.8; the status bar shows a pending badge but no approval UI appears, deadlocking multi-agent workflows.

6. **[#16856](https://github.com/QwenLM/qwen-code/issues/16856)** — *P1 security: mid-command input redirection hides words from read-only classifier* (2 comments). Example: `git branch < /dev/null -D main` executes `-D main` while the classifier sees only the prefix — a genuine safety hole in `evaluateCommandSafety`.

7. **[#16496](https://github.com/QwenLM/qwen-code/issues/16496)** — *AUTO mode classifier told the pending call already ran* (5 comments). The transcript echoes the call being classified as a `Prior action`, distorting approval decisions.

8. **[#15660](https://github.com/QwenLM/qwen-code/issues/15660)** — *Subagents start with huge context (~105k) of unknown origin* (6 comments). Parent at 42k vs. subagent at 105k — token accounting and context inheritance need investigation.

9. **[#15327](https://github.com/QwenLM/qwen-code/issues/15327)** — *P1 tracking: run every surface on one agent loop behind `experimental.unifiedAgentLoop`* (4 comments). Architectural consolidation of the agent loop across CLI, Web Shell, and daemon — high leverage for parity fixes like #16787.

10. **[#16824](https://github.com/QwenLM/qwen-code/issues/16824)** — *Model repeatedly misspells project name ("Lulumi" vs "Luminum") after clean reinstalls* (3 comments). Persistent long-context/model-quality complaint; tagged `model/long-context`, suggesting a caching or context-corruption angle.

---

## 4. Key PR Progress

1. **[#16857](https://github.com/QwenLM/qwen-code/pull/16857)** — *fix(permissions): keep the pending call out of the classifier's prior actions.* Threads the call id through the scheduler and ACP session so the transcript builder skips the in-flight `functionCall`. Direct fix for #16496.

2. **[#16862](https://github.com/QwenLM/qwen-code/pull/16862)** — *fix(ci): retry release Docker test suites once on transient failures.* Adds a single retry with container cleanup scoped to the job owner; targets release-pipeline flakiness.

3. **[#16574](https://github.com/QwenLM/qwen-code/pull/16574)** — *fix(cli): in-session MCP enable lifts glob exclusions; rate-limit duplicate WS rejection logs.* Two operational fixes for `qwen serve`.

4. **[#9417](https://github.com/QwenLM/qwen-code/pull/9417)** — *fix(cli): fail closed on expanding heredoc bodies in the worktree guard.* Unquoted heredoc delimiters let bash expand bodies that the guard stripped as inert — a security-relevant hardening.

5. **[#16664](https://github.com/QwenLM/qwen-code/pull/16664)** — *fix(acp): report `max_tokens` when output recovery leaves the answer truncated.* Surfaces truncation as a stop reason instead of silently resolving the round.

6. **[#16607](https://github.com/QwenLM/qwen-code/pull/16607)** — *fix(cli): bound provider-warning URL sanitizing to the URL authority.* Replaces bespoke `indexOf` heuristics with `sanitizeProviderBaseUrl`, closing credential-leak edge cases in `/status`.

7. **[#16669](https://github.com/QwenLM/qwen-code/pull/16669)** — *fix(cli): enable synchronized output on Windows Terminal 1.23+.* DECSET 2026 detection includes native Windows and WSL2 build-number paths — reduces TUI flicker.

8. **[#16055](https://github.com/QwenLM/qwen-code/pull/16055)** — *fix(cli): explicit wrap width for static transcript text.* Fixes the mid-word breakage/orphaned-character bug reported in #14075.

9. **[#16845](https://github.com/QwenLM/qwen-code/pull/16845)** — *fix(web-shell): admit GitLab as a repository-only channel.* Generalizes the channel model beyond GitHub; hides inert DM controls and accepts nested allowlists.

10. **[#16811](https://github.com/QwenLM/qwen-code/pull/16811)** — *test(generated-ui): acceptance evaluation for #14019.* End-to-end runs through `qwen serve`, Web Shell, the bundled Generated UI provider, and Chromium — only the model is replaced.

---

## 5. Hot Discussions

No discussion data was provided in this dataset; section omitted.

---

## 6. Feature Request Trends

- **Channel & integration expansion** — Outbound platform webhooks via Channel delivery (DingTalk custom robots) with Web Shell configuration and Scheduled Tasks flows ([#16751](https://github.com/QwenLM/qwen-code/issues/16751)); GitLab channel support landing via PR.
- **Web Shell UX maturity** — Model discovery for custom providers with refresh-and-select ([#16785](https://github.com/QwenLM/qwen-code/issues/16785)), a dedicated centered session-search dialog ([#16778](https://github.com/QwenLM/qwen-code/issues/16778)), and a stateful "intelligent UI" with native components and isolated HTML visualizations ([#14019](https://github.com/QwenLM/qwen-code/issues/14019)).
- **Unified agent loop across surfaces** — Consolidating CLI, Web Shell, and daemon onto one loop in `packages/core` ([#15327](https://github.com/QwenLM/qwen-code/issues/15327)).
- **Real shell parsing instead of hand-rolled models** — Replace `shell-semantics.ts` with tree-sitter-bash / mvdan/sh plus AST attribution ([#16604](https://github.com/QwenLM/qwen-code/issues/16604)).
- **Performance at scale** — Batched GraphQL CI re-check leg for the GitHub channel ([#16692](https://github.com/QwenLM/qwen-code/issues/16692)).
- **Terminal UX migration** — OpenTUI rendering layer tracking ([#8662](https://github.com/QwenLM/qwen-code/issues/8662)).

---

## 7. Developer Pain Points

- **Permission-classifier correctness is the top concern.** Three separate reports ([#16856](https://github.com/QwenLM/qwen-code/issues/16856), [#16496](https://github.com/QwenLM/qwen-code/issues/16496), [#16497](https://github.com/QwenLM/qwen-code/issues/16497)) show AUTO mode either seeing the wrong command text or approving destructive actions inferred from vague goals ("clean up my branch" → `git branch -D`). The hand-rolled shell model is repeatedly patched and repeatedly bypassed — the push toward a real parser (#16604) reflects reviewer fatigue.
- **Workflow/multi-agent hangs.** Approval prompts that never render ([#16425](https://github.com/QwenLM/qwen-code/issues/16425)) and teammate messages never reaching the leader in ACP mode ([#15738](https://github.com/QwenLM/qwen-code/issues/15738)) make multi-agent experiments unreliable.
- **CI noise and flakiness.** The bot-generated CI issues dominate volume (#14724 at 292 comments, #16672 at 55, #16787 at 34), with several flagged *Provisional* — main moving past the head before reports are filed. This erodes signal for contributors.
- **Session-state locks.** `/cd` and `/branch` failing with "response or tool call in progress" even when idle ([#12224](https://github.com/QwenLM/qwen-code/issues/12224), [#16700](https://github.com/QwenLM/qwen-code/issues/16700)) — a recurring class of stale in-flight-state bugs.
- **Context/token accounting opacity.** Subagents ballooning to ~105k tokens ([#15660](https://github.com/QwenLM/qwen-code/issues/15660)) and model-quality drift after reinstall ([#16824](https://github.com/QwenLM/qwen-code/issues/16824)).
- **Rendering edge cases.** Zero-height viewports blanking the virtualized transcript ([#16707](https://github.com/QwenLM/qwen-code/pull/16707)), static-mode word-wrap orphans ([#14075](https://github.com/QwenLM/qwen-code/issues/14075)), and Windows Terminal sync-output gaps ([#16669](https://github.com/QwenLM/qwen-code/pull/16669)) show long-tail TUI polish work is still substantial.

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/zhangzuxuan2023-hub/agents-radar).*