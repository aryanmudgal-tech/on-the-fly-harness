# What the evidence says: eleven findings and ten principles

*Research program: agent harnesses. Synthesis written 2026-09-08 by the coordinator from the nineteen research documents in this repository. Status: draft, red-team pending. Every finding cites the documents it rests on; the documents carry the sources and their verification notes.*

## What this document answers

- Which parts of the thesis "as models get better, the harness becomes the bottleneck, and neither a CLI nor a desktop app can be the default way people operate agents" survive contact with the evidence, and which do not.
- What the evidence says a reimagined harness has to do, stated as principles that the design document (`02-reimagined-harness.md`) and the recommendation (`03-recommendation.md`) build on.

## How to read the citations

Square brackets name a research document: [anatomy] and [history] are the primers in `01-primer/`; [labs], [independent], [matrix], [nontech-labs], [nontech-startups] and [adoption] are in `02-landscape/`; [protocols] and [runtime] in `03-building-blocks/`; [academic] and [industry] in `04-research/`; [messaging], [generated-ui], [voice] and [os] in `05-surfaces/`; [dev-users] and [nontech-users] in `06-users/`; [strategy] in `07-strategy/`. "Vendor" after a number means the figure was reported by the company that benefits from it. Many 2026 figures were verified through search excerpts and GitHub-hosted mirrors because this session's network policy blocked most primary sites; each document lists what could not be opened.

## The thesis, scored

| Clause of the thesis | Verdict | Where the evidence is |
|---|---|---|
| The harness decides whether agents work | Supported, with a split: true for the outer layer (permissions, runtime, memory, verification, surfaces), false for the inner layer (loop, prompts, scaffolds), which is shrinking | Findings 1, 9 |
| Better models make the harness the bottleneck | Half right: better models absorb the inner harness and expose the outer one as the remaining bottleneck | Findings 1, 6, 9 |
| Neither a CLI nor a desktop app can be the default surface | Half right: the CLI became the engine, not the front door, and chat surfaces carry two orders of magnitude more people; but the desktop app as a client of a cloud runtime is exactly where the two largest labs converged in 2026 | Findings 2, 3 |
| The default harness must be reimagined for non-technical people | True, but the part to reimagine is the trust model (authority, verification, memory, cost), not the body style | Findings 4, 5, 10 |
| A startup can own the reimagined default | Not supported for a general-purpose harness; supported only for narrow positions that own a workflow, an outcome, or a cross-vendor governance layer | Findings 7, 8 |

## Eleven findings

### 1. The harness is two layers that move in opposite directions

The loop is trivial and getting more so. A code-editing agent fits in a few hundred lines [industry]; pi ships four tools and a system prompt of about 300 words and is the engine under OpenClaw [independent]; mini-SWE-agent, about 100 lines with bash as its only tool, reports over 74% on SWE-bench Verified [academic]; METR found in February 2026 that the Claude Code and Codex product scaffolds did not outperform its own simple scaffolds [academic]. Anthropic now tells builders that every harness component "assumes the model can't do something; those assumptions expire" and to "re-simplify on model upgrades" [industry]. Whole harness functions have moved into vendor APIs: compaction became a request header, memory became a server-side store, permission prompts became a classifier, turn-taking in voice became an API parameter [anatomy][runtime][voice].

The other layer thickens. The labs spent 2026 on sandboxes, credential vaults, checkpointing, scheduling, webhooks, agent identities, cost meters and classifier-gated permissions, not on prompting [labs][runtime]. Microsoft's answer to non-technical agents was a runtime, per-agent identities in its directory, per-task credits and an approval loop [nontech-labs]. Every incident in the corpus lives in this layer [runtime][os][messaging].

The industry document names the split: a compensation layer that expires with each model release, and an environment-and-authority layer that does not [industry]. The academic document says the same from the research side: the inner harness is being automated, the outer harness has no benchmark [academic].

**So what.** "Better models need better harnesses" is right about the outer layer and wrong about the inner layer. Anything built in the inner layer must be designed to be thrown away on every model upgrade.

### 2. "One runtime, many surfaces" is the settled shape, and the CLI became the engine

Anthropic's documentation states that each surface connects to the same underlying Claude Code engine, across terminal, IDEs, desktop, web, mobile, Slack, Chrome, Remote Control and messaging channels [labs]. Codex is one Rust core under a CLI, IDE extensions, a desktop app and a cloud service; GitHub's Copilot SDK talks to the same engine behind the Copilot CLI; Amazon's Kiro drives its CLI over the Agent Client Protocol [labs]. Cloud environments apply wherever a session starts: web, terminal, Slack, routines, mobile, desktop [runtime]. Among independents, every orchestrator wraps a CLI and the Agent Client Protocol standardizes "agent as a subprocess" [independent]. New harnesses still launch terminal-first: Meta's Muse Code in August 2026 [history].

**So what.** The author's assumption that the CLI cannot be the default is already the industry's position for the surface and wrong for the engine. A startup harness that rebuilds the engine competes with engines the labs give away (Codex, Mistral Vibe) or rent (Managed Agents at tokens plus $0.08 per session-hour) [labs][runtime].

### 3. Standalone new surfaces have the worst record in the corpus; embedded surfaces have the revenue

Failed or absorbed within a year: OpenAI's Operator (folded into ChatGPT within six months) and Atlas browser (292 days) [os][nontech-labs]; Google's Project Mariner (shut May 2026) [nontech-labs]; Humane (dead), Rabbit (survived by hosting other people's agents) [voice]; Dia (bought for $610M, still single-platform a year later), Manus (bounced between owners after a government order), Poke (sold four months after public launch), Convergence's Proxy (absorbed into Salesforce) [nontech-startups]; OpenAI's visual Agent Builder (wound down after about eight months) [labs]; Terragon, Vibe Kanban, Roo Code's extension and Continue on the developer side [independent].

Where the revenue is: Agentforce at $1.2B ARR, ServiceNow's AI contract value past $1B, Glean past $300M, Microsoft 365 Copilot at 30M paid seats, Copilot Cowork used by more than half the Fortune 500 during a three-month preview (all vendor figures) [nontech-startups][nontech-labs][messaging]. Those live in a CRM, a service console, a search bar, Slack, Teams and the office suite.

By people and messages, chat wins by two orders of magnitude: ChatGPT reported about 1B weekly users in mid-2026 against roughly 10M combined users for Codex and ChatGPT Work, the largest terminal-born agent (vendor statements via secondary reports) [adoption]. By money and delegated autonomy, developer surfaces lead: coding took roughly 55% of departmental enterprise AI spend in 2025 (Menlo Ventures, via secondary), and the Economic Index shows automation-style conversations at 60.8% among conversations matched to software-developer tasks against 48.6% overall [adoption]. The fastest-growing cohort inside the coding agents is non-developers: knowledge workers were about 20% of Codex's weekly users in June 2026 and growing more than three times faster than developers (vendor, via secondary) [adoption]. And the desktop app is not dead: the two largest labs converged on a desktop app as the non-terminal client of a cloud runtime (the Codex desktop app merged into the ChatGPT app in July 2026; Claude Cowork), and that merge coincided with Codex's fastest growth [adoption][nontech-labs].

**So what.** For non-technical people the default surface is "wherever the data and identity already are": chat inside existing tools, the office suite, the OS assistant, a browser page the agent generates, a phone notification, with a desktop or web app as the place to review. What survived was reimagining the trust model inside existing surfaces, not the body style. The thesis clause "nor a desktop app" is wrong about the client and right about the runtime: the desktop is dead as the place you watch the agent from, not as the window you approve things in.

### 4. Non-technical delegation is broad and shallow, and the binding constraint is authority, not accuracy

Seventy-six percent of Claude chat and Cowork conversations match tasks outside computing; the top matched tasks are searching for information, reference questions, product advice and editing (Anthropic Economic Index, accessed 2026-09-08) [nontech-users]. ChatGPT use is about 70% non-work, and two-thirds of writing requests edit text the user supplied [nontech-users]. People hand over clerical work whole (conversations matched to clerk and secretarial tasks run 69 to 75% automation-style) and keep judgment work collaborative (31 to 38% for tasks matched to lawyers, credit counselors and marketing managers) [nontech-users].

The failures are about authority. Students regret agents that "acted beyond what they would have authorized" [nontech-users]. Replit's agent deleted a production database during a declared code freeze [nontech-users]. OpenClaw, the one developer-shaped harness that reached consumers at scale, produced the category's worst incident record [runtime][messaging]. An independent tester of Cowork, Lindy, Sauna and Opal failed them on memory, inspectable artifacts and compounding context, and put the problem in one line: "Code has a test suite. A strategy memo doesn't compile." [nontech-startups]

**So what.** The product problem for this audience is consequence-aware permissions, verification without expertise, memory across tools, cost in dollars and the ability to undo. Those are harness parts 3, 4 and 7. The surface, part 6, is settled: chat inside the tools people have, a cloud runtime, a plan with checkpoints, a notification, a review [nontech-labs][nontech-users].

### 5. Approvals are the least standardized part of the harness and the most valuable

Seven vendors show seven approval models, and none shows the user a consistent picture of what an action can cost or destroy [nontech-labs]. Approval fatigue is measured: users approved 93% of prompts, humans caught 13.6% of dangerous commands against 89% for a classifier (vendor figures) [runtime]; Anthropic's answer, a classifier-gated auto mode, became the default for new Claude Code sessions on paid plans on August 14, 2026 [anatomy]. Approvals inside chat are text-grade: a relayed prompt answered with "yes" plus a code [messaging]. Nobody offers scoped, batched or reversible approvals across surfaces [messaging]. Policy lives in terminal and desktop artifacts, and Claude Code on the web ignores local settings, so policy does not travel with the person [protocols]. Microsoft Teams' approve-before-sharing step is the only genuinely new approval primitive found, and it authorizes disclosure, not action [messaging]. Windows gives agents their own account and session, yet consent is per host application, so a consented app exposes the user's files to every tool in the session [os].

**So what.** "Policy that travels with the person across surfaces and vendors" is unsolved, and the protocols document says whoever solves it solves a problem the labs have not [protocols].

### 6. Security failures are structural and cluster where the agent reads untrusted content

Prompt injection is unsolved on every vendor's admission [runtime][os]. The 2026 incident list runs through calendar invites, browser extensions, support bots, enterprise search and a VM escape [nontech-labs]; OpenClaw published roughly 650 advisories in five months, many of them authorization bypasses in message handling [messaging]. The defenses that hold are architectural: an OS boundary, an egress proxy that keeps credentials outside the boundary, and a second model that reviews actions [runtime]. Sandboxing itself is a commodity with a dozen interchangeable suppliers [runtime].

**So what.** A harness for non-technical people must be safe by structure, not by prompts: containment, credential-holding proxies, capability scoping at the read boundary. That is outer-harness work.

### 7. The labs hold the meter, and value goes to whoever owns the outcome, the data or the channel

Cursor's gross margin was reported at minus 23% for the quarter ending January 2026 and turned positive only after it shipped its own models and repriced [strategy]. Anthropic stopped consumer subscriptions from covering third-party harnesses on April 4, 2026, then reinstated capped credits from June 15 [strategy][independent]. Within six months of 2026, four standalone developer harnesses died or were absorbed; the survivors own a model (Cursor, Cognition), a workflow (Factory, Qodo) or a builder surface for non-developers (Lovable, Replit) [independent]. The year's deals went to distribution and compute owners: SpaceX and Cursor, Salesforce and Fin, Cognition and Poke, SAP's stake in n8n, Atlassian and Dia [strategy][nontech-startups]. The labs are absorbing the runtime: Managed Agents bundles loop, sandbox, memory, tracing and scheduling behind one API [runtime].

**So what.** A general-purpose harness is not a venture-scale business on this evidence. The strategy document's verdict is that the thesis holds as a design thesis and fails as a value-capture thesis unless the company also holds the meter, the data or the channel [strategy].

### 8. Protocols separated surface from runtime and commoditized the thick features, but the labs own both ends

MCP won the tool layer (about half a billion SDK downloads a month, vendor), moved to the Linux Foundation, and in July 2026 dropped sessions and the initialization handshake to become stateless HTTP [protocols]. Instruction files, skills, hooks and plugin manifests are near-identical across Claude Code, Codex, Gemini CLI and Cursor [protocols][matrix]. The Agent Client Protocol puts 41 agents behind one editor interface [protocols]. The UI protocols (MCP Apps, AG-UI, A2UI, generative UI in the Vercel AI SDK) are months old and mostly pre-1.0 [protocols][generated-ui]. Every one of these standards was created by Anthropic, OpenAI or Google, and the two most-used UI paths deliver third-party interfaces into lab-owned chat apps [protocols].

**So what.** A startup inherits the plugs but not the traffic. Build on the protocols; expect them to churn; do not expect them to provide distribution.

### 9. Automated harness design is real, overfits, and has no benchmark for the layer that matters

Harness design is now a search problem with working automation, from workflow search in 2024 to self-rewriting coding agents in 2025 to whole-harness optimization in 2026 [academic]. An independent re-evaluation in July 2026 found harness evolution does not consistently beat plain test-time scaling at matched budgets and transfers about 0.6 points to held-out tasks [academic]. METR's task-horizon doubling time is 89 to 131 days depending on the window [academic]. No benchmark measures whether a non-technical person's task got done; the one cost-controlled leaderboard was archived in July 2026; Zapier's business-task benchmark tops out at 50.3% and Sierra's airline tasks pass four times in a row only 22.5% of the time [runtime][nontech-startups].

**So what.** Whatever inner harness is right today is wrong within a year, so it should be generated per task and discarded. The durable product is the outer loop: evaluation, traces, regression gates, permissions, and an eval for non-technical outcomes that does not yet exist.

### 10. Generated interfaces are an output surface, not a control surface

Raters preferred generated interactive pages to markdown 83% of the time, ignoring generation speed [academic][generated-ui]. Claude Code publishes live pages from a terminal session, and those pages now carry a shared database, presence and a page-side call to the model [generated-ui]. Nothing in the record shows a generated interface used to operate a long-running agent, and the one visual agent builder from a lab was wound down [generated-ui][labs]. The safety split is unresolved: sandboxed generated code (Anthropic, OpenAI) against a declarative component catalog (Google) [generated-ui]. Spreadsheets may be closer to a non-technical harness than any generated page, because the grid already solves verification [generated-ui].

**So what.** Generate the view; fix the controls. The gap is a generated review page with fixed, auditable controls for steering, approval and undo, with spreadsheet-grade provenance.

### 11. Background execution is the real surface shift; interruption policy is thin everywhere

One engine now runs from cloud schedules, repository events, HTTP triggers, messaging channels and a phone's push notification; the loop left the foreground [voice]. Cowork's scheduled tasks run with no device online; Codex automations wake themselves; ChatGPT has scheduled tasks [voice][nontech-labs]. Interruption controls are two push toggles, a presence file and a cap on idle check-ins; no vendor publishes an interruption-cost model [voice]. The one natural experiment, ChatGPT Pulse, pushed unrequested output daily and was retired in July 2026 in favour of schedules the user sets [voice]. Voice is dictation: the two most capable harnesses use it as input only, and the confirmation problem, not latency, is what blocks it as a control surface [voice].

**So what.** The default harness runs unattended and reports back. What is missing is policy: when to interrupt, how to confirm without a screen, how to scope an unattended run so a non-expert understands the blast radius.

## Ten principles for a reimagined harness

1. **Split the harness.** A stable outer harness that the person or organization owns; an inner harness that the agent assembles for each task and discards. Never let a feature that compensates for a model weakness harden into the product. (Findings 1, 9)
2. **The agent is a principal, not a proxy.** It acts under its own identity with scoped, time-boxed, budgeted grants, never silently "as you". (Findings 5, 6)
3. **Approve consequences, not commands.** Classify actions by reversibility, blast radius and cost; batch what is reversible; preview what is not; keep an undo ledger. (Findings 4, 5)
4. **Verification is a product, not a prompt.** Every task ends with proof of work: what changed, what evidence supports it, which checks ran, with provenance a non-expert can inspect. Documents and data need this more than code does, because they do not compile. (Findings 4, 10)
5. **Policy and memory travel with the person** across surfaces, devices and model vendors, in a portable, inspectable form. (Findings 5, 8)
6. **Safe by structure.** Containment, credential-holding proxies, capability scoping at the read boundary; prompts are not a control. (Finding 6)
7. **Surfaces are projections.** Many, thin, and wherever the person already is. The harness is never the app. (Findings 2, 3)
8. **Interrupt on the person's terms.** A policy that weighs presence, urgency and reversibility; explicit schedules over unrequested pushes; screen-less confirmation that reads back consequences. (Finding 11)
9. **Cost is a first-class control.** Budgets per task and per run, expressed in money, with predictable bills. (Findings 4, 7)
10. **Design for next year's model.** Re-simplify the inner harness on every model upgrade and measure the outer harness with an evaluation you own, because none exists. (Findings 1, 9)

## What this means for the thesis

Supports: the harness is where the work and the incidents are, the surface is already separating from the engine, and the parts non-technical people need are missing everywhere. Contradicts: the inner harness is shrinking, the default surface is settled and embedded, and the labs hold the runtime and the meter. Nuance: the thesis survives if it is restated as "the outer harness is the bottleneck, it must be reimagined for non-technical people, and the way to build it is to make the inner harness disposable."

## Open questions and unverified claims

- The adoption split between chat surfaces and terminals or IDEs rests on vendor disclosures (via secondary reports) and the Economic Index; [adoption] lists each figure's status. Independent, controlled evidence exists only for developers (METR), and METR itself declared its 2026 estimates unreliable.
- Several 2026 figures cited above were confirmed only through two or more independent search excerpts because primary pages were blocked; the verification notes in each document say which.
- No document could verify the current state of the spreadsheet-native harnesses (Claude for Excel, Shortcut, Paradigm), which Finding 10 flags as potentially the closest thing to a non-technical harness.
