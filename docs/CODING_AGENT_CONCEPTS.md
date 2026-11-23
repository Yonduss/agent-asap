# Coding Agent Project: Research and Concept Candidates

> **Latest decision (2026-11-23):** Agent ASAP is the project selected for planning. See the v0.3 entry below and the [Agent ASAP project plan](AGENT_ASAP_PLAN.md). Earlier recommendations are dated research snapshots.

**Research snapshot:** 2026-09-29 (Asia/Seoul)  
**Status:** Agent ASAP selected for planning  
**Working language:** English for discussion, documentation, code, issues, and demos

## Purpose

Build a long-term coding-agent project with a clear user, problem, and measurable outcome. Use the project both to learn agent internals and to develop credible open-source contribution experience. A broad clone of an existing coding agent would be hard to distinguish; a focused workflow can produce a stronger demonstration.

## What the references actually show

| Reference | Observed product angle | Useful lesson | Caution for this project |
| --- | --- | --- | --- |
| [Pi](https://github.com/earendil-works/pi) | Modular toolkit: provider API, agent loop, state, TUI, coding CLI, extensions. | A small core with clear extension points makes the agent understandable and adaptable. | Pi says its core should stay minimal and points contributors toward extensions. It has no built-in filesystem/process/network permission boundary by default; a new tool must define its own execution policy. [Source](https://github.com/earendil-works/pi/blob/main/CONTRIBUTING.md) |
| [OpenCode](https://github.com/anomalyco/opencode) | Full open-source coding agent with CLI and desktop surfaces; built-in build and read-only plan agents. | Study the boundary between exploration/planning and mutation, and how a mature project exposes agent modes. | Its breadth and large codebase make a general competitor a poor first product target. [Source](https://github.com/anomalyco/opencode) |
| [Gajae Code](https://github.com/Yeachan-Heo/gajae-code) | Plan-gated coding workflow, evidence, context control, remote questions, and subscription-backed provider access. | The workflow around the model is part of the product: interview, plan, critique, execute, verify. | Several proposed features below overlap it; differentiation must be demonstrated with a specific task and benchmark. [Source](https://github.com/Yeachan-Heo/gajae-code) |
| [Hungry](https://hungryide.github.io/) | Lightweight coding-agent experience centered on OpenAI-compatible providers, model pools, approval modes, token saving, review, and remote access. | A sharp resource or workflow constraint can define the concept even when the underlying agent loop is simple. | The live site could not be fetched by the research tool. These details were checked against the site's [published HTML source](https://github.com/hungryide/hungryide.github.io/blob/main/index.html), so they are product claims, not independently tested behavior. |

### Trend scan

The [Trendshift monthly list](https://trendshift.io/monthly) and [GitHub weekly list](https://github.com/trending?since=weekly) were checked on the research date. The GitHub page returned a snapshot crawled about two weeks earlier; rankings and star counts should not be treated as current market measurements. Trending is a discovery signal, not evidence of user need or product quality.

Relevant recurring themes in the visible lists:

- **Agent configuration and skills:** [ECC](https://github.com/affaan-m/ECC), [openai/skills](https://github.com/openai/skills), and [humanlayer/skills](https://github.com/humanlayer/skills) suggest interest in reusable procedures around existing agents.
- **Context and memory discipline:** [context-mode](https://github.com/mksglu/context-mode) and [Hindsight](https://github.com/vectorize-io/hindsight) suggest interest in reducing context noise and retaining useful state.
- **Review and independently checked output:** [Alibaba Open Code Review](https://github.com/alibaba/open-code-review) and [Cloudflare's security audit skill](https://github.com/cloudflare/security-audit-skill) suggest demand for narrow, verifiable workflows.
- **Simplicity as a feature:** [Ponytail](https://github.com/DietrichGebert/ponytail) frames avoided or reduced code as a positive outcome.

**Inference:** An agent with a narrow job, explicit evidence, and measurable evaluation has a clearer opening than another all-purpose chat-and-edit interface. This is a hypothesis to validate with users, not a conclusion proved by trend rankings.

## Candidate concepts

The names are working labels. Each candidate can start as a CLI to keep the first milestone small.

### 1. ReproLab: issue-to-reproduction coding agent — recommended first candidate

**User and problem:** OSS maintainers and contributors receive bug reports that lack a reproducible case. The agent turns an issue description into a minimal failing test, identifies the relevant code path, proposes a small fix, and records what passed.

**Core loop:** intake issue and repository context → ask for missing reproduction details when necessary → create isolated workspace → locate likely code path → produce a failing test or a clear “cannot reproduce” record → propose a patch → run focused and existing checks → produce an evidence report and reviewable diff.

**Concept boundary:** It must never claim a bug is fixed solely because code changed. The distinctive artifact is a reproducible failing case and an evidence trail. Human approval remains required for upstream submission.

**First useful version:** Local CLI that accepts a markdown issue and repo path; supports one language/ecosystem; creates a worktree, runs configured test commands, and writes `reproduction.md` plus a patch summary. Start with local issue text before adding GitHub integration.

**Success measures:** Share of benchmark issues with a valid failing test; share of fixes passing existing tests; false “fixed” claims; median time and model cost per case. Compare against a plain agent prompt on the same cases.

**Main risks:** Tests can pass for the wrong reason; environment setup can dominate the run; real issues may need private data. Mitigate with a small public benchmark and explicit unsupported-case reports.

**References:** [Pi](https://github.com/earendil-works/pi) for a compact runtime and extension model; [Gajae Code](https://github.com/Yeachan-Heo/gajae-code) for plan/evidence gates; [OpenCode](https://github.com/anomalyco/opencode) for plan/build separation; [Alibaba Open Code Review](https://github.com/alibaba/open-code-review) for structured review output.

### 2. FirstMap: repository onboarding and task navigation agent

**User and problem:** A new contributor needs to understand where a change belongs, how to run the project, and which small issue is realistic. Generic code search returns files but not a trustworthy path to a first contribution.

**Core loop:** inspect repository guidance and scripts → build a cited architecture map → trace one selected task through definitions, callers, tests, and contribution rules → propose a small implementation plan and validation commands → optionally hand the plan to an editing agent.

**Concept boundary:** Every architectural claim links to a file and line or command output; unknowns remain marked unknown. The first version is read-only and does not pretend to understand the entire repo.

**First useful version:** A CLI that produces `ONBOARDING_MAP.md` for a chosen task in one repository, with entry points, data flow, likely edit points, checks, and questions for maintainers.

**Success measures:** Time for a new contributor to find the right files; accuracy of cited locations; number of incorrect setup instructions; usefulness ratings from 3–5 developers. Compare with README plus manual search.

**Main risks:** Repository maps can become stale or verbose. Keep task scope explicit, attach source locations, and regenerate on demand.

**References:** [OpenCode](https://github.com/anomalyco/opencode) for read-only planning; [Pi](https://github.com/earendil-works/pi) for extensibility; [context-mode](https://github.com/mksglu/context-mode) for compact context; [Archify](https://github.com/tt-a1i/archify) for verifiable diagrams if visualization later proves useful.

### 3. PatchProof: change verification and review agent

**User and problem:** Developers receive plausible AI-generated patches but spend time finding missing tests, unrelated edits, and unsupported completion claims.

**Core loop:** read the requested behavior and diff → identify changed behavior and risk points → select focused checks → run them → inspect failures and missing coverage → produce line-linked findings, evidence, and a confidence-limited verdict. It may propose a fix, but the MVP does not merge or publish.

**Concept boundary:** Separate deterministic checks from model judgment. A passing test is evidence only for the behavior it actually exercises.

**First useful version:** Review a local Git diff against a task statement and emit a structured markdown/JSON report with exact commands and results. Add one ecosystem-specific rule set instead of broad language support.

**Success measures:** Precision of actionable findings on a seeded patch set; missed regressions; time to review; proportion of claims backed by executable checks. Compare with lint/test alone and a generic review prompt.

**Main risks:** Review agents can overwhelm users with low-signal findings. Rank only issues that can be reproduced or tied to a specific requirement.

**References:** [Alibaba Open Code Review](https://github.com/alibaba/open-code-review), [Cloudflare security audit skill](https://github.com/cloudflare/security-audit-skill), [Gajae Code](https://github.com/Yeachan-Heo/gajae-code), and [OpenCode](https://github.com/anomalyco/opencode).

### 4. BudgetPilot: reliable coding under model and context limits

**User and problem:** Students and solo developers hit free-tier limits or spend too much on exploratory agent runs. A request can fail halfway through or consume context on unhelpful output.

**Core loop:** classify task and estimate budget → choose a configured provider/model → compact tool output while preserving errors and evidence → checkpoint state → on rate limit, resume with the next allowed provider → report cost, failures, and handoffs.

**Concept boundary:** The measurable goal is successful completion under a budget, with no silent loss of task state or degraded verification.

**First useful version:** Wrap one existing local agent or a minimal tool loop; support two user-provided OpenAI-compatible endpoints; record turn checkpoints and deterministic replay of routing decisions. Do not rely on undocumented subscription authentication.

**Success measures:** Completion rate under artificial rate limits; total tokens/cost; quality compared with one-provider baseline; number of lost or duplicated tool actions during failover.

**Main risks:** Provider behavior differs, billing and terms change, and automatic failover can hide quality drops. Keep credentials local, make routing visible, and document provider-specific limits.

**References:** [Hungry website/source](https://github.com/hungryide/hungryide.github.io/blob/main/index.html) for model pools and token-saving ideas; [Pi](https://github.com/earendil-works/pi) for provider abstraction; [Gajae Code](https://github.com/Yeachan-Heo/gajae-code) for context controls; [context-mode](https://github.com/mksglu/context-mode).

## Decision matrix

Scores are initial judgment on a 1–5 scale, not measured results. **Higher is better** for all columns; “buildability” means a smaller, testable MVP. A user interview and small spike should replace these scores before committing.

| Candidate | Distinct story | Buildability | Measurability | OSS fit | Total / 20 |
| --- | ---: | ---: | ---: | ---: | ---: |
| ReproLab | 5 | 3 | 5 | 5 | **18** |
| FirstMap | 4 | 5 | 3 | 5 | **17** |
| PatchProof | 4 | 4 | 5 | 3 | **16** |
| BudgetPilot | 3 | 3 | 4 | 3 | **13** |

**September recommendation:** ReproLab has the stronger demo and evaluation story; FirstMap is a good fallback if the first reproduction spike shows environment setup is too expensive. PatchProof is a possible second phase for ReproLab. BudgetPilot is technically interesting but overlaps Hungry and requires careful provider handling to stand out.

## Questions to validate before choosing

1. Which user is most accessible for feedback: maintainers, first-time OSS contributors, or developers reviewing AI patches?
2. Can we collect 10–20 public, closed issues with known fixes and runnable tests in one ecosystem?
3. What is the smallest output a real user would trust: a failing test, a file-linked map, or a verification report?
4. What quality bar beats a plain coding-agent prompt on the same tasks?
5. Which ecosystem and stack can the project author maintain for months? Choose this from familiarity and benchmark availability, not GitHub rank.

## September proposal for evaluating a candidate

1. **Concept brief:** Freeze one target user, one job, non-goals, and three example tasks in `PROJECT_BRIEF.md`.
2. **Evidence set:** Select public tasks with known outcomes; store issue links, setup steps, expected checks, and baseline results. Respect each repository's contribution and data rules.
3. **Thin vertical slice:** Implement the smallest end-to-end run for one task before adding a TUI, desktop app, multiple providers, or autonomous PR creation.
4. **Evaluation:** Run the same tasks through the project and a simple baseline; record successes, failures, cost, and manual intervention.
5. **OSS contribution:** Use what the spike reveals to make a focused upstream contribution, ideally a reproducible bug fix, test, documentation correction, or extension. Verify current contribution guidelines immediately before proposing a PR.

For Pi specifically, [current contribution guidance](https://github.com/earendil-works/pi/blob/main/CONTRIBUTING.md) says new contributors' issues and PRs are auto-closed by default and a maintainer `lgtm` is required before opening a PR. An independent Pi extension is a plausible starting route. OpenCode and Gajae Code each have their own [contribution](https://github.com/anomalyco/opencode/blob/dev/CONTRIBUTING.md) [guides](https://github.com/Yeachan-Heo/gajae-code/blob/main/CONTRIBUTING.md); read them again when an actual contribution is scoped.

## Codex workflow for this long-term project

- **Now, during concept selection:** Use this research file as durable context. Ask for critique of the problem, user, benchmark, and scope. Plan mode is useful for exploring a risky or underspecified implementation path before code changes; it is not needed just to keep notes.
- **At architecture decisions:** Switch to **Plan mode** with `/plan` and ask for a milestone plan, alternatives, acceptance criteria, and risks. Review the plan, then implement in **Default mode** one milestone at a time.
- **Across many turns:** Keep a brief, plan, decision log, evaluation results, and current status in markdown. A durable Codex goal may help when there is a specific multi-turn outcome; create one only when you explicitly want Codex to pursue that outcome.
- **During implementation:** Use an isolated worktree for changes that should be reviewed separately; run the benchmark and focused tests after each milestone; use `/review` or the app's diff review before accepting changes.
- **For side questions:** Use a side chat when available so architecture questions do not interrupt the implementation chat. Availability of particular commands and surfaces can vary by host and app version.

This guidance follows [official OpenAI documentation on Plan mode and goals](https://developers.openai.com/blog/mastering-codex-remote-for-engineering) and [a long-horizon project example using durable spec, plan, validation, and status files](https://developers.openai.com/blog/run-long-horizon-tasks-with-codex).

## Source and freshness notes

Primary project links are embedded above. The trend lists are changing snapshots; revisit them for discovery, but preserve the benchmark and user problem as the basis for decisions. The Hungry site itself was inaccessible through the research browser, so its published HTML source was used. No reference project was installed or behavior-tested in this research pass.


---

## v0.2 — Trend review and concept revisions (2026-11-07)

**Decision at the time:** Refine the four original concepts and add RelayCheck and UIRepro. ReproLab remained the leading recommendation. No concept had been selected or validated with users at this point.

### Evidence and retrieval method

- [GitHub weekly trending](https://github.com/trending?since=weekly) and [GitHub monthly trending](https://github.com/trending?since=monthly): fetched directly on 2026-11-07 after the research browser rejected both URLs. Both HTML pages were parsed for repository cards.
- [Trendshift monthly](https://trendshift.io/monthly) and [Trendshift weekly](https://trendshift.io/weekly): retrieved through the research browser; both reported a crawl from today. These are a separate ranking source, not a mirror of GitHub's results.
- Selected repository landing pages were opened to check their stated purpose. This is a documentation review, not an installation, benchmark, or claim that advertised capabilities work.
- Period labels mean the sources' displayed rolling windows; they do not establish a calendar October report. Exact star counts are omitted because popularity is volatile and is not evidence of product quality.

### What appeared in the current lists

| Signal | GitHub weekly | GitHub monthly | Implication for our candidates |
| --- | --- | --- | --- |
| Persistent context | [claude-mem](https://github.com/thedotmack/claude-mem) | claude-mem | Memory is an established competitive area. A new memory concept needs a concrete correctness problem. |
| Agent workspaces | [worktrunk](https://github.com/max-sixty/worktrunk), [t3code](https://github.com/pingdotgg/t3code), [openbot](https://github.com/nightly-labs/openbot) | t3code | Workspace management is a useful integration surface; a generic workspace UI needs stronger differentiation. |
| Skills and development procedures | [anthropics/skills](https://github.com/anthropics/skills), [superpowers](https://github.com/obra/superpowers), [hallmark](https://github.com/Nutlope/hallmark), [impeccable](https://github.com/pbakaus/impeccable) | hallmark, impeccable, [knowledge-work-plugins](https://github.com/anthropics/knowledge-work-plugins) | A focused workflow can be distributed through existing agents. Test whether a skill suffices before building a standalone application. |
| Review and verifiable findings | [PR Agent](https://github.com/The-PR-Agent/pr-agent) | [security-audit-skill](https://github.com/cloudflare/security-audit-skill) | PatchProof should specialize in executable regression evidence rather than general review commentary. |
| Software behavior exploration | — | [rea](https://github.com/morluto/rea), [Ghidra](https://github.com/NationalSecurityAgency/ghidra) | A behavior-driven debugging concept is worth considering; binary analysis would expand the first milestone too far. |

Trendshift additionally showed [agent-desktop](https://github.com/vercel-labs/agent-desktop), [craft-anything](https://github.com/storytold/craft-anything), and [dsh-superagent-prototype](https://github.com/deepseek-ai/dsh-superagent-prototype) in both windows, and [deepseek-harness](https://github.com/deepseek-ai/deepseek-harness) in the weekly list. The selected primary pages describe computer-use tooling, software exploration, and a plugin-oriented harness. These support investigating integrations; they do not prove that a more autonomous general agent is the right project for us.

**Interpretation:** September's interest in skills and verification continues, while this snapshot adds prominent examples of memory and workspace tooling. We cannot infer an industry-wide shift or market growth rate from two snapshots and different ranking methods. The useful change is our competitive comparison and product scope.

### Revisions to the original candidates

| Original concept | November revision | Position |
| --- | --- | --- |
| ReproLab v0.1 | **ReproLab v0.2: reproducible OSS bug evidence.** Start with a regression test and reproducible environment record. Require the same test to fail on the buggy revision and pass on the fixed revision. Patching becomes the next milestone after reliable reproduction. | First recommendation |
| FirstMap v0.1 | **FirstMap v0.2: task-specific contributor handoff.** Produce a short map for one issue, pinned to a commit, with files, test commands, contribution constraints, and unresolved questions. Check cited locations when the revision changes. | Smaller fallback |
| PatchProof v0.1 | **PatchProof v0.2: regression verification.** Check a supplied patch using before/after tests and an evidence report. Measure false findings and missed regressions. | Possible ReproLab companion; narrower standalone option |
| BudgetPilot v0.1 | **BudgetPilot v0.2: budget-constrained completion evaluation.** Compare completion quality and cost under a fixed budget. Keep routing and checkpoint correctness visible. | Lower priority until a concrete user need is established |

The distinction between ReproLab and PatchProof is the input: ReproLab starts with a bug report and must discover the reproduction; PatchProof starts with a proposed patch and verifies its behavior. Their shared test infrastructure is a reason to sequence them, not to build both at once.

### New candidate 5 — RelayCheck: verified agent-session handoffs

**User/problem:** A developer resumes a task in a fresh session or another worktree. The summary may refer to a different revision, a check may no longer be valid, or an earlier decision may be presented as a current fact.

**Concept:** Generate a compact handoff with task intent, base/current commit, changed files, decisions, unfinished work, and verification commands. On resume, compare it with the actual repository and flag stale evidence before the agent continues.

**First useful version:** A local CLI with `capture` and `verify` operations for one Git repository. Store the handoff in Markdown plus a small structured manifest. Support explicitly supplied task notes and command results before attempting to import proprietary chat formats. Distinguish an old verification result from a result rerun on the present revision.

**Why it differs:** The target is detecting invalid assumptions during a handoff. A general memory store alone does not establish whether remembered evidence is still applicable. This is a differentiation hypothesis requiring direct comparison.

**Evaluation:** Create resume cases involving a changed base commit, modified test configuration, uncommitted edits, and unchanged state. Measure stale-evidence detection, false alarms, resume success, and time spent re-exploring. Compare with a manually written handoff and an ordinary agent summary. Do not invalidate every claim merely because any file changed; track dependencies and acknowledge uncertainty.

**References:** [claude-mem](https://github.com/thedotmack/claude-mem) for persistent session context; [worktrunk](https://github.com/max-sixty/worktrunk) for worktree workflows; [Pi](https://github.com/earendil-works/pi) for runtime/extension structure. No compatibility with these projects has been implemented or verified.

**Main risk:** A small, deterministic manifest checker may solve most of the problem. Use an agent for extracting intent and resolving ambiguous stale assumptions only if evaluation shows added value. This could be a harness component rather than a standalone coding agent.

### New candidate 6 — UIRepro: browser bug reproduction agent

**User/problem:** A frontend contributor receives a report such as “the form loses my input after navigation” but lacks reliable steps and an automated regression test.

**Concept:** Observe a supplied local web application, derive a short interaction sequence, reproduce the failure, and produce a browser test plus trace and expected/actual behavior. Let an editing agent propose a fix after reproduction is stable.

**First useful version:** One local web stack, one browser, and functional bugs with explicit expected behavior. Record the application revision and test fixture state. Start from a user-supplied URL and report; avoid generalized desktop control in the first release.

**Evaluation:** Use seeded frontend regressions with known expected results. Measure repeated reproduction success, test stability, false positives, and time to a runnable test. Compare with a plain agent using the same browser tools. A screenshot by itself does not establish correctness.

**References:** [agent-desktop](https://github.com/vercel-labs/agent-desktop) for the adjacent computer-use tool surface; [rea](https://github.com/morluto/rea) for behavior exploration; [Playwright](https://github.com/microsoft/playwright) as a potential browser-test foundation, not a claim that it appeared in the fetched trending lists. UIRepro is a browser-focused specialization of ReproLab and should not become a parallel build effort.

**Main risk:** Browser state and timing make failures difficult to reproduce. Visual style criticism is subjective, so begin with functional regressions and test repeatability.

### November 7 shortlist and next decision

1. **ReproLab v0.2:** strongest connection to contributing useful OSS tests and fixes; clearest first demonstration.
2. **RelayCheck:** strongest new alternative if session continuity is a problem we can observe personally and validate with other developers.
3. **FirstMap v0.2:** smallest starting scope; its value must exceed a normal agent's task explanation.
4. **UIRepro:** choose as the ReproLab specialization if frontend work is the author's strongest area.
5. **PatchProof v0.2:** reuse the reproduction infrastructure later, or select it if reviewers are easier to recruit than maintainers.
6. **BudgetPilot v0.2:** retain for later comparison, with no immediate build recommendation.

This ordering is judgment, not benchmark data; the original numerical matrix remains historical. The new snapshot does not justify replacing the whole project direction.

**Proposed experiment at the time:** Define five evaluation cases and a baseline before writing the runtime. Build one complete case, evaluate failures, then expand to 10–20 cases. To study agent internals, build a minimal tool loop, tool result handling, execution policy, and event log. To validate the product first, prototype on an existing runtime.

### Version history and future review policy

| Version | Date | Changes |
| --- | --- | --- |
| v0.1 | 2026-09-29 | Original reference analysis and four concepts; preserved above. |
| v0.2 | 2026-11-07 | Direct GitHub weekly/monthly scan, Trendshift cross-check, revised scopes, and RelayCheck/UIRepro. |
| v0.3 | 2026-11-23 | Selected Agent ASAP for planning and recorded its scope, references, and evaluation approach. |

For future requested reviews, append a dated section, preserve old concepts and decisions, list the source windows and access limitations, and state what evidence changes the recommendation. Review before choosing the MVP and when a relevant competitor or user finding materially changes the scope. This document does not set up automatic monitoring or scheduled updates.

## v0.3 — Agent ASAP (2026-11-23)

### Candidate 7 — Agent ASAP

Agent ASAP turns a project idea into a short plan and a runnable starting point that the developer can understand and extend. It creates the frontend, backend, and data model only as the first agreed user flow calls for them. The developer takes over once that flow runs and the next change is clear.

The first experiment will use a small local web app. ASAP will define one flow, produce a readable set of files, verify the flow, and explain where development can continue. The prototype will start with local templates to keep routine scaffolding fast and predictable. A later model experiment will test whether a short model call helps translate a vague brief into the right scope.

Ponytail already addresses unnecessary code and reports time and token savings in its own benchmark. ASAP tests a more specific outcome: whether a developer can start from a blank folder and make the next change independently. Karpathy’s personal site demonstrates how far HTML and CSS can go when the requirements fit. [Ponytail](https://github.com/DietrichGebert/ponytail) · [Andrej Karpathy’s personal site](https://karpathy.ai/)

Evaluate ASAP against a normal coding agent and Ponytail using the same starter briefs. Check functionality first, then compare time, model usage, dependencies, and how easily a developer can explain or extend the result. The [project plan](AGENT_ASAP_PLAN.md) defines the initial scope, stopping point, and milestones.
