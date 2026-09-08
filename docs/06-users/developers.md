# What developers need from a harness: jobs, pain points, and the terminal question

*Research program: agent harnesses. Written 2026-09-08. Status: draft, pending verification.*

Sourcing note: the research proxy blocked stackoverflow.co, stackoverflow.blog, jetbrains.com, dora.dev, metr.org, anthropic.com, github.blog, faros.ai, cursor.com, openai.com and every.to. Claims from those sources rest on two or more search excerpts or secondary write-ups and are marked "(via secondary)". Read directly: the Claude Code documentation on code.claude.com, README files on raw.githubusercontent.com (Symphony, Gas Town, the Ralph Wiggum plugin, METR's study repository), Google Cloud's DORA announcement, and the anthropics/claude-code issue tracker through the GitHub API (reaction counts as of 2026-09-08). Revenue figures and product dates are cross-referenced to sibling documents in this program rather than re-verified.

## What this document answers

- What jobs developers hire a coding agent for, and what the surveys and field studies of 2025–2026 say about adoption, trust and measured productivity.
- Which parts of today's harnesses hurt most (context loss, permission fatigue, verification and review load, cost surprises, multi-agent chaos), with primary evidence from vendor documentation and the Claude Code issue tracker.
- What developers value in the CLI, which workflows are forming around it (worktrees, background agents, pull-request loops, orchestrators, agent-manager desktops), and what would have to be true for developers to leave the terminal.

## TL;DR

- Adoption is near-universal; trust is not. 84% of Stack Overflow's 2025 respondents used or planned to use AI tools, but 46% distrust the accuracy of the output against 33% who trust it, only 3% "highly trust" it, and 66% name "almost right, but not quite" as their top frustration (via secondary) [1][2][3]. DORA 2025 puts adoption at 90% of ~5,000 respondents [10].
- Agents went from minority to majority in a year. 31% of developers used AI agents in mid-2025, 38% had no plans to [2]; JetBrains reports 90% of professional developers using coding agents at least weekly in May–July 2026, 68% daily (single secondary excerpt; unverified) [8].
- Measured productivity is contested. METR's July 2025 randomized trial found experienced open-source developers 19% slower with early-2025 tools while believing they were 20% faster; its February 2026 follow-up (57 developers, 800+ tasks) could not produce a reliable estimate, partly because developers refused to work without AI and ran several agents at once (via secondary) [11][12]. Anthropic's engineers self-report roughly 50% gains, with Claude in about 60% of their work (vendor study, via secondary) [13].
- The bottleneck moved to review. Faros telemetry on 10,000+ developers: 98% more PRs merged, review time up 91%, PR size up 154%, bugs up 9% per developer (vendor report, via secondary) [15]. DORA 2025: AI now correlates with higher throughput but still with lower delivery stability [10].
- The loudest developer complaints are harness complaints. The top-reacted open Claude Code issue is about usage limits (694 thumbs-up, 1,494 comments, opened Jan 2026); permission-prompt bugs and compaction that drops CLAUDE.md follow [33]. Anthropic's answer to permission fatigue was a classifier, "auto mode", the default on paid plans since Aug 14, 2026 [19][32].
- The terminal is not dying; it is becoming an engine. Anthropic's docs describe one engine behind terminal, IDE, desktop, web, phone, Slack and chat channels [17]; every lab and most independents now ship an "agent manager" that runs many CLI sessions in git worktrees and shows diffs [26][39][42].
- The unit of work is no longer a session but a fleet plus a review queue. Everything shipped in 2026 (agent view, worktrees, Code Review, auto-fix PRs, Symphony, Gas Town, dynamic workflows) exists to supervise many agents cheaply [21][25][29][35][36].
- For the thesis: "the CLI cannot be the default surface" is supported by vendor behaviour and developer practice; "developers will leave the terminal" is not. Developers are leaving *watching* the terminal.

## 1. Vocabulary for a newcomer

Analogy: hiring a contractor. The model is the contractor's skill. The harness is the job site: the keys you hand over (permissions), the plans on the wall (context), the inspector who signs off (verification), and the foreman who runs several crews at once (orchestration). Most developer complaints about "AI coding" are complaints about the job site.

Precisely, in this program's seven-part vocabulary: **context loss** is what happens when the loop's window fills and the harness summarises ("compacts") history, dropping instructions with it. **Permission fatigue** is the stream of approval prompts a manual-approval permission layer produces. The **verification burden** is the human time spent proving an agent's output correct; at the pull-request stage it shows up as the **review bottleneck**. A **git worktree** is a second checkout of the same repository so parallel agents never edit the same files. An **agent manager** is a surface that lists many running sessions with their diffs and pull-request status.

## 2. Jobs to be done

The evidence on what developers delegate is thin but consistent. Anthropic's internal study found its engineers use Claude most for fixing code errors and learning about the codebase, and that 27% of Claude-assisted work "would not have been done otherwise", such as dashboards and scaling projects that were never cost-effective by hand (via secondary) [13]. Anthropic's product page leads with "automate the work you keep putting off": tests for untested code, lint fixes across a project, merge conflicts, dependency updates, release notes [17]. Its best-practices guide says onboarding to a new codebase is the workflow that needs "no special prompting" [18]. In the Anthropic Economic Index (Claude chat and Cowork, not Claude Code; May 2026 period), computer-and-mathematical tasks are the largest occupational category at 23.8% of matched conversations, software development is 11.5% of request topics, and 48.6% of conversations worldwide are "automation" style, where the person directs Claude to finish the task rather than collaborating [41].

| Job | What it demands from the harness | Part that limits it today |
| --- | --- | --- |
| Understand unfamiliar code | Read many files without polluting the main conversation | Context (subagents exist for this) [18] |
| Fix a bug from a symptom | Reproduce, edit, run tests, iterate | Loop plus a verification signal [18] |
| Clear the tedious backlog (tests, lint, migrations, docs) | Run unattended on many files | Permissions and cost (fan-out multiplies both) [20][30] |
| Ship a feature and open a PR | Plan, implement, verify, git | Surface for review; the PR is the accountability unit [31] |
| Review a change (human or agent authored) | Diff, evidence, CI, second opinion | Verification; the review bottleneck [15][29] |
| Run recurring jobs (PR triage, nightly CI fixes) | Scheduling, background runtime, notifications | Runtime (routines, scheduled tasks) [31] |
| Multiply oneself | Many isolated sessions, one overview | Orchestration and cost [21][23] |

## 3. How developers use agents today: the numbers

| Source (date) | Sample | Finding | Caveat |
| --- | --- | --- | --- |
| Stack Overflow Developer Survey 2025 (Jul 2025) [1][2][3][6] | Annual survey | 84% use or plan to use AI tools; 46% distrust accuracy vs 33% trust, 3% highly trust; 31% use agents, 17% plan to, 38% will not; 66% "almost right"; 45% say debugging AI code takes more time; reported trust in accuracy fell from 40% to 29% | Blocked; via secondary. The 33% and 29% trust figures come from different Stack Overflow write-ups and may be different questions |
| Stack Overflow Developer Survey 2026 (opened Jun 23, 2026) [5] | In field or analysis | New sections on agents and AI ROI; results not found as of Sep 8, 2026 | Secondary write-ups claim "agent usage doubled"; unverified |
| JetBrains State of Developer Ecosystem 2025 (Oct 2025) [7] | 24,534 developers, 194 countries | 85% use AI regularly; 62% rely on at least one assistant, agent or AI editor; only 44% say AI is fully or partially integrated into their workflow | Via secondary |
| JetBrains agent-adoption post (Aug 2026) [8] | Professional developers, May–Jul 2026 | 90% use coding agents at work at least weekly; 68% daily | One search excerpt; unverified |
| DORA 2025 (Sep 23, 2025) [10] | ~5,000 professionals, 100+ hours qualitative | 90% use AI at work (+14 points); >80% believe it raised productivity; positive relationship with throughput and product performance, negative with delivery stability; "AI doesn't fix a team; it amplifies what's already there" | Read directly (Google Cloud blog) |
| METR RCT (Jul 2025) [11] | 16 experienced OSS developers, 246 tasks, early-2025 Cursor/Claude | 19% slower with AI; forecast 24% faster; perceived 20% faster afterwards | Via secondary; repository confirms 246 issues and the 0.188 slowdown regression |
| METR follow-up (Feb 24, 2026) [12] | 57 developers, 143 repositories, 800+ tasks | Returning developers 18% slower (CI −38% to +9%); new developers 4% slower (CI −15% to +9%); METR calls the data unreliable and believes real speedup has grown | Via secondary. Developers declined to work without AI; time-on-task could not be measured for people running several agents at once |
| Anthropic internal study (Dec 2025) [13] | 132 engineers and researchers surveyed, 53 interviews, usage data, Aug 2025 | Claude in ~60% of work; ~50% self-estimated productivity gain; 27% of assisted work is new work; worries about skill erosion and ability to supervise | Vendor study; via secondary |
| GitHub Octoverse 2025 (Oct 2025) [14] | Platform data | 180M+ developers, 36M new in a year; 43.2M PRs merged per month (+23%); ~1B commits (+25%); 1.1M public repos import an LLM SDK (+178%); ~80% of new developers use Copilot in week one | Via secondary |

Usage disclosures follow the same curve. Claude Code passed a $1B run-rate in December 2025, six months after general availability, and $2.5B in February 2026 with weekly actives said to have doubled since January 1 (vendor claims) [16]. Cursor went from $2B annualized (February–March 2026) to $4B (June 2026), about three quarters enterprise, and was sold to SpaceX for $60B in stock (closed August 2026) [16]. Codex reported 5M weekly users in June 2026 [16]. On GitHub, anthropics/claude-code has ~144k stars and openai/codex ~123k (2026-09-08) [34].

Two details in the METR follow-up matter more than the point estimates. Recruitment failed because experienced developers would not take paid work without AI, and the measurement failed because "developers running several agents at once" break the idea of time on task [12]. Both are evidence that by early 2026 the working unit had changed from "a developer with an assistant" to "a developer supervising a fleet".

## 4. Pain points with current harnesses

### 4.1 Context loss

Anthropic's own guide is blunt: "Claude's context window fills up fast, and performance degrades as it fills", "Claude may start 'forgetting' earlier instructions", and "the context window is the most important resource to manage" [18]. The remedies it lists are manual hygiene: `/clear` between tasks, `/compact` with custom instructions, subagents for exploration, `/btw` for side questions, "Summarize up to here" in the rewind menu, and keeping CLAUDE.md under 200 lines because "bloated CLAUDE.md files cause Claude to ignore your actual instructions" [18][20]. The issue tracker shows what happens when hygiene fails: "/compact causes Claude Code to ignore CLAUDE.md" (Jul 2025), "expose how much context is left before auto-compact" (May 2025), a request for a `--no-auto-compact` switch (Aug 2025, 47 reactions), and in May–June 2026 reports that auto-compact does not trigger at 100% and the session stops itself [33]. Bigger windows help but do not remove the problem: Opus 5 shipped in July 2026 with a 1M-token window, and the docs still describe degradation as the window fills [32][18]. Compaction has also moved server-side (see the anatomy document), which lowers the cost of the workaround, not the need for it [39].

### 4.2 Permission fatigue

The docs describe manual mode honestly: "That's safe but tedious. After the tenth approval you're clicking through rather than reviewing" [18]. The permission layer grew a rule language (allow, deny, ask, prefix matching, sandbox, protected paths), and the tracker fills with the rules not working: a prompt that triggers on `cd` instead of the real command in compound statements (Feb 2026, 206 reactions, open), "Allow all edits" not respected (Oct 2025), allow lists in settings files ignored (Aug and Dec 2025), skills and subagents not inheriting permissions (Jan 2026, 71 reactions), and Remote Control showing prompts despite `--dangerously-skip-permissions` (Feb 2026, 81 reactions) [33]. Remote Control even has a notification, after "several permission prompts in a session", inviting you to "Approve tool calls from your phone" [28].

The vendor's fix was to replace the human with a model. Auto mode arrived as a research preview in March 2026, reached the Pro plan in May, and became the default permission mode for new sessions on Pro, Max and Team from August 14, 2026 [32]. In it "a second model, the classifier, reviews actions instead of you", blocking "anything that escalates beyond your request, targets unrecognized infrastructure, or appears driven by hostile content Claude read"; the mode table lists its purpose as "long tasks, reducing prompt fatigue", and `bypassPermissions` is now for "isolated containers and VMs only" [19]. Permission fatigue was real enough to change the default of the most-used developer harness within five months.

### 4.3 Verification burden and the review bottleneck

The Stack Overflow numbers (66% "almost right", 45% "debugging AI code takes more time") are verification numbers [2][3]. Anthropic's guide says why: "Without a check it can run, 'looks done' is the only signal available, and you become the verification loop: every mistake waits for you to notice it", and "if you can't verify it, don't ship it" [18]. Its recommended ladder (a test in the prompt, a `/goal` condition, a Stop hook that blocks the turn until a script passes, an adversarial review subagent in a fresh context) is a list of ways to take the human out of the loop [18].

At team scale the load lands on reviewers. Faros's telemetry across 1,255 teams found high-adoption developers completing 21% more tasks and merging 98% more PRs, with review time up 91%, PR size up 154%, and bug rates up 9%, and no measurable improvement at the company level (vendor report, via secondary) [15]. DORA 2025 makes the same point from survey data: AI now helps throughput but still hurts stability because "an increase in change volume leads to instability" without strong testing, version control and fast feedback [10]. Octoverse's 43.2M merged PRs a month, up 23%, is the same pressure from the platform side [14].

The vendors' response is agent reviewers. Claude Code's Code Review runs "a fleet of specialized agents" plus "a verification step" that filters false positives, averages 20 minutes and $15–25 per review, and deliberately "never blocks merging" [29]. `/code-review` runs locally as a background subagent (May–July 2026), and "ultrareview" runs a bug-hunting fleet in the cloud (April 2026) [32]. OpenAI's Symphony spec requires "proof of work" (CI status, PR review feedback, complexity analysis, walkthrough videos) before an agent's run is accepted [35]. None of these remove the human reader; they change what the human reads.

### 4.4 Cost surprises

Cost is a harness property. Anthropic's docs give the enterprise baseline (about $13 per developer per active day, $150–250 per month, 90% of users under $30 a day) and then a section titled "Why usage climbs in a long session": the full conversation is resent on every request, cache misses after a break reprocess everything, scheduled tasks and cross-session messages fire with full context, each agent teammate keeps consuming, and compaction "is itself a large request" [20]. Parallelism multiplies it: "running ten agents in parallel uses quota roughly ten times as fast" [21]; agent teams use "approximately 7x more tokens" in plan mode [20]; dynamic workflows warn at 25 agents or 1.5M projected tokens and cap a run at 1,000 agents [25].

Developers feel it as limits, not invoices. The most-reacted issue in the Claude Code tracker is "Instantly hitting usage limits with Max subscription" (opened Jan 3, 2026; 694 thumbs-up, 1,494 comments, still open), followed by reports that Opus 4.6 consumed far more quota than 4.5 (Feb 2026), that a Max 20 plan was exhausted within about 70 minutes of a reset (Apr 2026), and "usage explosion" threads in March 2026 [33]. Subscription usage now draws from "a rolling five-hour window and a weekly window" [20]; the weekly window dates from August 2025, introduced after users ran Claude Code continuously in the background (from training knowledge; not re-verified, no URL). Cursor's June–July 2025 pricing change and apology followed the same pattern (from training knowledge; not re-verified). In April 2026 Anthropic barred third-party harnesses from consuming Pro and Max subscriptions, citing the cost of loops not engineered for cache hits, then partially reversed with metered "Agent SDK" credits in May [39]. Whoever owns the harness owns the bill.

### 4.5 Multi-agent chaos

The failure modes are documented by the vendor itself. Agent teams are "experimental and disabled by default"; "two teammates editing the same file leads to overwrites"; "letting a team run unattended for too long increases the risk of wasted effort"; task status "can lag"; in-process teammates do not survive `/resume` [24]. The tracker adds "general-purpose sub-agents recursively spawn unbounded child agents, causing exponential fan-out and massive token burn" (Jun 2026) and "ESC key kills ALL background tasks/subagents" (Jan 2026) [33]. Gas Town's README states the human limit directly: "4-10 agents become chaotic", and sells persistence in "git-backed hooks" to "scale comfortably to 20-30 agents" [36]. Cognition's 2025–2026 lesson, recorded in the practitioner document, was to keep a single writer and let extra agents contribute "intelligence rather than actions" [39]. Anthropic's parallelism page now tells users to partition files by hand because "agent teams don't isolate teammates in worktrees" [23].

## 5. What developers value in the CLI

- **It runs where the code runs.** SSH hosts, CI, containers, any editor. The vendor's own pitch: "Claude Code is composable and follows the Unix philosophy. Pipe logs into it, run it in CI, or chain it with other tools" [17]. `claude -p` reads stdin, returns JSON with `total_cost_usd`, sets exit codes, and `--bare` gives "the same result on every machine" [30].
- **Configuration as code.** CLAUDE.md, `.claude/settings.json`, hooks, skills and subagent definitions are files checked into git; hooks "are deterministic and guarantee the action happens" where instructions are only advisory [18]. Teams share a harness by sharing a repository.
- **Control.** Plan mode, `Esc` to interrupt, `/rewind` checkpoints, `/clear`, permission rules, and the option to drop the guardrails inside a container [18][19].
- **Context economy.** "CLI tools are the most context-efficient way to interact with external services"; `gh`, `aws`, `gcloud` beat MCP servers on tokens [18][20].
- **Parallelism by habit.** Tabs and tmux. Boris Cherny, who leads Claude Code, is reported to run five terminal tabs plus web sessions (secondary tips compilation) and said in February 2026 that "coding is largely solved" (via secondary) [40]. His earlier rationale for a terminal product, as paraphrased from 2025 interviews and not re-verified here, was that the terminal is the one environment every engineer already has, works over SSH and in CI, and composes with existing tools.

What the CLI does not give them: a diff you can comment on, a view of ten sessions at once, PR and CI status, screenshots. Those are exactly what the desktop app's "Coming from the CLI" section adds [26]. Counter-evidence that the terminal keeps winning new entrants: Meta's Muse Code launched terminal-only in August 2026 with sub-agents in parallel worktrees and no app [43].

## 6. Emerging workflows

```
 idea / ticket / CI failure / Slack message
        |
   dispatch: CLI tab | `claude agents` | desktop | web VM | routine | @Claude
        |
   +----------+----------+----------+
   | worktree |  worktree |  cloud VM |   isolated checkouts, one agent loop each
   | tests    |  tests    |  tests    |   a check the agent can read closes the loop
   +----------+----------+----------+
        |
   pull requests  -->  CI + agent review (Code Review, Symphony "proof of work")
        |
   human review queue   <-- the bottleneck the whole stack is built around
```

**Parallel agents in git worktrees.** `claude --worktree feature-auth` creates a checkout under `.claude/worktrees/` on its own branch; the desktop app gives "every new session its own worktree automatically"; Claude Code blocks edits, commands and git redirects that would touch the main checkout, and subagents can carry `isolation: worktree` in their definition [22]. `/batch` splits one change across "5 to 30 subagents" that each open a pull request [23]. Cursor 3's Agents window (April 2026), the Codex app (February 2026) and Muse Code all run agents in worktrees [39][42][43].

**Background and cloud agents.** `claude agents` opens agent view, "one screen for all your background sessions: what's running, what needs your input, and what's done", with rows labelled Working, Needs input, Idle, Completed, Failed, Stopped, and PR labels coloured by check status (research preview, May 2026) [21][32]. Claude Code on the web runs each task in an Anthropic-managed VM, persists after the laptop closes, can be monitored from the phone, and moves between surfaces with `claude --cloud` and `claude --teleport` [27]. Codex cloud, GitHub's Copilot cloud agent (issue in, PR out) and Google's Jules occupy the same slot [39]. Routines fire cloud sessions from a schedule, a GitHub event or an API call (April 2026) [31][32].

**Pull-request-driven loops.** Auto-fix subscribes a session to a PR: "when a check fails or a reviewer leaves a comment, Claude investigates and pushes a fix if one is clear", asks on ambiguous requests, and replies on GitHub labelled as Claude Code [27]. Code Review posts severity-tagged inline findings and asks reviewers to rate them with thumbs so Anthropic can tune the reviewer [29]. Symphony "turns project work into isolated, autonomous implementation runs, allowing teams to manage work instead of supervising coding agents": it watches a Linear board, spawns one run per issue and demands proof of work (repository created Feb 26, 2026; ~27k stars) [35]. The PR has become the agent's unit of accountability, and the docs warn that comment-triggered automation (Atlantis, Terraform Cloud) can now be fired by an agent's reply [27].

**Orchestration tools.** The Ralph Wiggum loop, credited to Geoffrey Huntley and shipped as an official Claude Code plugin, is "a Bash loop" whose Stop hook blocks session exit and re-feeds the same prompt until a completion phrase appears, with `--max-iterations` as "the primary safety mechanism" [37]. Gas Town (Steve Yegge; ~18k stars) adds a "mayor" coordinator, "polecats" workers and git-backed "beads" so that work survives restarts [36]. Anthropic's dynamic workflows (May 2026) move the plan into a JavaScript script Claude writes, with `agent()`, `pipeline()` and `parallel()`, up to 16 concurrent agents and 1,000 per run, and a bundled `/deep-research` that cross-checks sources [25]. Agent teams remain experimental [24]. The common design across all four: state lives in git or files rather than in context, a verification gate decides when to stop, and hard caps bound the bill.

**Agent-manager desktops.** Claude Code Desktop runs parallel worktree-isolated sessions, a diff view with inline comments, a "Review code" button, a CI status bar with Auto-fix and Auto-merge toggles, panes for browser, terminal, file, plan and subagents, an iOS simulator, computer use (research preview), Dispatch from the phone, and a Linux beta [26]. The Codex app launched as a "command center" for parallel agents (macOS Feb 2, 2026; Windows Mar 4) [42][39]. Independents converged on the same shape: Conductor (reported $22M Series A, March 2026), Superset ("orchestrate 100+ coding agents in parallel. Run any agent with your own subscription", ~14k stars), Claude Squad (tmux plus worktrees, ~8.5k stars), Vibe Kanban (~28k stars, product killed April 2026) and Terragon (shut down January 2026) [38][39]. The manager sits on a CLI engine; two of the standalone managers died within a year of launch.

## 7. Leaving the terminal: what would have to be true, and what developers would refuse to give up

What would have to be true:

1. **Verification is visible and cheap in the new surface, at fleet scale.** Diffs with comments, test output, screenshots, CI status and a second-opinion review must be one glance per session, not one terminal per session. The desktop's diff view and CI bar are the current best attempt; agent view's one-line summaries are the CLI's [21][26].
2. **The permission model is better than the terminal's, not merely hidden.** Auto mode plus sandboxing plus organisation policy has to make "what can this fleet touch" legible; the surface owns blast radius [19].
3. **Nothing scriptable is lost.** Hooks, headless runs, exit codes, JSON output and settings-as-code must survive and stay in git. A surface that stores project state in a proprietary database will be rejected by the people it targets [18][30].
4. **It runs where the code runs.** SSH, WSL, containers, self-hosted cloud environments (the desktop already offers SSH and WSL sessions; cloud sessions can run on self-hosted infrastructure) [26][27].
5. **Cost is visible per task and per fleet, with budgets.** `/usage` attribution and "Large workflow" warnings are early versions [20][25].
6. **Surfaces are exits, not prisons.** `--teleport`, `/desktop`, Remote Control and cross-session messaging already let a session move; developers will leave the terminal only if they can come back [17][27][28].

What they would refuse to give up: the shell and their own tools; plain-text configuration in git; keyboard-only operation; `Esc` and rewind; local execution with secrets staying local; the raw engine (`claude -p`) for scripts and CI; and the freedom to bring their own subscription or model, which Anthropic's April 2026 policy change showed can be revoked from above [18][30][38][39].

## What this means for the thesis

**Supports.**
- The harness is the bottleneck for developer *throughput*. Where the gains are lost is review, verification, cost and coordination, all harness parts: Faros's review-time doubling, DORA's stability penalty, METR's failure to even measure fleet-running developers [15][10][12]. Anthropic's 2026 release history is almost entirely harness: auto mode, agent view, worktrees, workflows, Code Review, routines, Remote Control [32].
- "Neither CLI nor desktop app can be the default" matches what the vendor built: "each surface connects to the same underlying Claude Code engine" [17]. Developers already mix tabs, web sessions and a phone.
- Developers' own priorities (fewer prompts, proof over plausibility, predictable bills, control of many agents) are exactly the reimagining the thesis calls for.

**Contradicts.**
- The terminal is still the engine and the fallback, and new entrants still launch terminal-first (Muse Code, August 2026) [43]. Everything developers say they value (composability, scripting, control, context economy) is a terminal virtue [17][18].
- The largest harness business won on the IDE, and the labs bundled the agent manager with the engine; standalone managers died or were absorbed [16][39]. A startup "reimagining the developer harness" competes with a free tab in the vendor's app.
- The measured productivity gain for experienced developers is still uncertain, which weakens any pitch that a better harness alone unlocks a large, known prize [11][12].

**Nuance.**
- What developers want reimagined is the supervision layer, not the surface: a review queue, a fleet view, budgets and a permission model that scales past ten agents. The terminal cannot do this well; the labs have only begun (Code Review does not block merges, Symphony is a spec, agent teams are experimental) [29][35][24].
- The defensible position for an outsider is verification and proof of work across any engine, because that is the one thing neither the terminal nor the current managers do, and it is where DORA and Faros say the value leaks [10][15]. Generic managers without it (Vibe Kanban, Terragon) did not survive [38][39].

## Open questions and unverified claims

- Stack Overflow 2026 results: not found as of 2026-09-08; the "agent usage doubled" line is secondary and unverified [5].
- JetBrains August 2026 figures (90% weekly, 68% daily) come from a single search excerpt [8].
- METR's 2026 numbers, Anthropic's internal-study numbers, Octoverse and Faros figures are all via secondary sources; Faros and Anthropic are vendor studies [12][13][14][15].
- The 33% versus 29% trust figures for Stack Overflow 2025 may be different questions [2][6].
- Cherny's terminal rationale is a paraphrase from 2025 interviews, not re-verified; the "five tabs" detail comes from a tips compilation [40].
- The August 2025 weekly-limit rationale and Cursor's July 2025 pricing apology are from training knowledge with no URL.
- Gas Town's release date is unverified; its star count comes from a rendered GitHub page [36].
- DORA's seven "AI capabilities" are not listed item by item because they appear only in an image [10].
- Copilot cloud agent and Jules details rely on the sibling labs document; docs.github.com was blocked [39].
- Revenue and user figures for Cursor, Claude Code and Codex are vendor or press claims cross-referenced from the value-capture document [16].

## Sources

1. Stack Overflow, "2025 Developer Survey", https://survey.stackoverflow.co/2025/ (Jul 2025; blocked, via secondary)
2. Stack Overflow press release, "Stack Overflow's 2025 Developer Survey Reveals Trust in AI at an All Time Low", https://stackoverflow.co/company/press/archive/stack-overflow-2025-developer-survey/ (Jul 2025; via secondary)
3. Stack Overflow blog, "Developers remain willing but reluctant to use AI: The 2025 Developer Survey results are here", https://stackoverflow.blog/2025/12/29/developers-remain-willing-but-reluctant-to-use-ai-the-2025-developer-survey-results-are-here/ (Dec 2025; via secondary)
4. Stack Overflow blog, "Mind the gap: Closing the AI trust gap for developers", https://stackoverflow.blog/2026/02/18/closing-the-developer-ai-trust-gap/ (Feb 2026; blocked)
5. Stack Overflow blog, "The 2026 Developer Survey is now open (for human developers only)!", https://stackoverflow.blog/2026/06/23/the-2026-developer-survey-is-now-open-for-human-developers-only/ (Jun 2026; blocked, via secondary)
6. ADTmag, "Developers Lean on AI More, But Report Growing Doubts About Accuracy, Stack Overflow Survey Says", https://adtmag.com/blogs/watersworks/2026/01/stack-overflow-survey.aspx (Jan 2026)
7. JetBrains, "The State of Developer Ecosystem 2025", https://blog.jetbrains.com/research/2025/10/state-of-developer-ecosystem-2025/ (Oct 2025; blocked) and InfoWorld, "85% of developers use AI regularly – JetBrains survey", https://www.infoworld.com/article/4077352/85-of-developers-use-ai-regularly-jetbrains-survey.html (Oct 2025)
8. JetBrains, "AI Coding Agents: Adoption Trends", https://blog.jetbrains.com/research/2026/08/ai-coding-agent-adoption-2026/ (Aug 2026; blocked, one search excerpt)
9. JetBrains, "Which AI Coding Tools Do Developers Actually Use at Work?", https://blog.jetbrains.com/research/2026/04/which-ai-coding-tools-do-developers-actually-use-at-work/ (Apr 2026; blocked, not read)
10. Google Cloud blog, "Announcing the 2025 DORA Report", https://cloud.google.com/blog/products/ai-machine-learning/announcing-the-2025-dora-report (Sep 2025; read directly); report page https://dora.dev/dora-report-2025/ (blocked)
11. METR, "Measuring the Impact of Early-2025 AI on Experienced Open-Source Developer Productivity", https://metr.org/blog/2025-07-10-early-2025-ai-experienced-os-dev-study/ (Jul 2025; blocked, via secondary); study repository https://github.com/METR/Measuring-Early-2025-AI-on-Exp-OSS-Devs (read directly)
12. METR, "We are Changing our Developer Productivity Experiment Design", https://metr.org/blog/2026-02-24-uplift-update/ (Feb 2026; blocked, via secondary incl. https://andrewwegner.com/metr-ai-productivity-study-update.html)
13. Anthropic, "How AI Is Transforming Work at Anthropic", https://www.anthropic.com/research/how-ai-is-transforming-work-at-anthropic (Dec 2025; blocked, via https://www.interviewquery.com/p/anthropic-ai-skill-erosion-report and https://news.ycombinator.com/item?id=46125534)
14. GitHub, "Octoverse: A new developer joins GitHub every second as AI leads TypeScript to #1", https://github.blog/news-insights/octoverse/octoverse-a-new-developer-joins-github-every-second-as-ai-leads-typescript-to-1/ (Oct 2025; blocked, via https://www.infoworld.com/article/4080454/typescript-rises-to-the-top-on-github.html, https://visualstudiomagazine.com/articles/2025/10/31/typescript-tops-github-octoverse-as-ai-era-reshapes-language-choices.aspx and https://www.forbes.com/sites/janakirammsv/2025/11/01/10-key-takeaways-from-github-octoverse-2025-report/)
15. Faros AI, "The AI Productivity Paradox Research Report", https://www.faros.ai/blog/ai-software-engineering (mid-2025; blocked, via https://getunblocked.com/blog/ai-productivity-paradox/ and https://dev.to/alexcloudstar/the-ai-productivity-paradox-why-developers-who-ship-more-code-are-not-actually-more-productive-12f7)
16. Revenue and traction, cross-referenced from docs/07-strategy/value-capture.md: Dealroom, "Cursor tops $4B annualized revenue", https://app.dealroom.co/news/note/cursor-tops-4b-annualized-revenue-june-2026 (Jun 2026); TechCrunch, "Cursor has reportedly surpassed $2B in annualized revenue", https://www.techcrunch.com/2026/03/02/cursor-has-reportedly-surpassed-2b-in-annualized-revenue/ (Mar 2026); TechCrunch, "SpaceX to acquire Cursor for $60B in stock", https://techcrunch.com/2026/06/16/spacex-to-acquire-cursor-for-60b-in-stock-days-after-blockbuster-ipo/ (Jun 2026); Anthropic, "Anthropic acquires Bun as Claude Code reaches $1B milestone", https://anthropic.com/news/anthropic-acquires-bun-as-claude-code-reaches-usd1b-milestone (Dec 2025)
17. Claude Code docs, "Overview", https://code.claude.com/docs/en/overview (read Sep 2026)
18. Claude Code docs, "Best practices", https://code.claude.com/docs/en/best-practices (read Sep 2026)
19. Claude Code docs, "Choose a permission mode", https://code.claude.com/docs/en/permission-modes (read Sep 2026)
20. Claude Code docs, "Manage costs effectively", https://code.claude.com/docs/en/costs (read Sep 2026)
21. Claude Code docs, "Agent view", https://code.claude.com/docs/en/agent-view (read Sep 2026)
22. Claude Code docs, "Run parallel sessions with worktrees", https://code.claude.com/docs/en/worktrees (read Sep 2026)
23. Claude Code docs, "Run agents in parallel", https://code.claude.com/docs/en/agents (read Sep 2026)
24. Claude Code docs, "Orchestrate teams of Claude Code sessions", https://code.claude.com/docs/en/agent-teams (read Sep 2026)
25. Claude Code docs, "Orchestrate subagents at scale with dynamic workflows", https://code.claude.com/docs/en/workflows (read Sep 2026)
26. Claude Code docs, "Desktop application", https://code.claude.com/docs/en/desktop (read Sep 2026)
27. Claude Code docs, "Use Claude Code on the web", https://code.claude.com/docs/en/claude-code-on-the-web (read Sep 2026)
28. Claude Code docs, "Remote Control", https://code.claude.com/docs/en/remote-control (read Sep 2026)
29. Claude Code docs, "Code Review", https://code.claude.com/docs/en/code-review (read Sep 2026)
30. Claude Code docs, "Run Claude Code programmatically", https://code.claude.com/docs/en/headless (read Sep 2026)
31. Claude Code docs, "Common workflows", https://code.claude.com/docs/en/common-workflows (read Sep 2026)
32. Claude Code docs, "What's new" weekly digests, weeks 13–34 of 2026, https://code.claude.com/docs/en/whats-new (read Sep 2026)
33. anthropics/claude-code issue tracker via the GitHub API, reaction counts as of 2026-09-08: #16157 https://github.com/anthropics/claude-code/issues/16157 (Jan 2026), #41788 (Apr 2026), #23706 (Feb 2026), #38239 and #37917 (Mar 2026), #28240 https://github.com/anthropics/claude-code/issues/28240 (Feb 2026), #29214 (Feb 2026), #18950 (Jan 2026), #9348 (Oct 2025), #13340 (Dec 2025), #6850 (Aug 2025), #4017 (Jul 2025), #1157 (May 2025), #6689 (Aug 2025), #63015 (May 2026), #66144 (Jun 2026), #68110 (Jun 2026), #68619 (Jun 2026), #21167 (Jan 2026)
34. GitHub repositories via the GitHub API, 2026-09-08: anthropics/claude-code (~144k stars) https://github.com/anthropics/claude-code; openai/codex (~123k) https://github.com/openai/codex
35. OpenAI, Symphony README, https://github.com/openai/symphony (repository created Feb 26, 2026; ~27k stars; read via raw.githubusercontent.com); announcement https://openai.com/index/open-source-codex-orchestration-symphony/ (Apr 2026; blocked)
36. Gas Town README, https://github.com/gastownhall/gastown (formerly steveyegge/gastown; ~18k stars; read via raw.githubusercontent.com, Sep 2026)
37. Anthropic, Ralph Wiggum plugin README, https://github.com/anthropics/claude-code/tree/main/plugins/ralph-wiggum (read via raw.githubusercontent.com, Sep 2026)
38. Orchestrator repositories via the GitHub API, 2026-09-08: BloopAI/vibe-kanban (~28k stars) https://github.com/BloopAI/vibe-kanban; superset-sh/superset (~14k) https://github.com/superset-sh/superset; smtg-ai/claude-squad (~8.5k) https://github.com/smtg-ai/claude-squad; Vibe Kanban shutdown notice https://www.vibekanban.com/blog/shutdown (Apr 2026, via sibling document)
39. Sibling documents in this program: docs/02-landscape/developer-harnesses-independent.md (Cursor 3, Conductor, Terragon, Vibe Kanban, Superset); docs/02-landscape/developer-harnesses-labs.md (Codex app, Copilot cloud agent, Jules, Muse Code); docs/01-primer/history.md (2026 timeline, Remote Control, Routines, the April 2026 subscription policy); docs/01-primer/anatomy.md (auto mode default, server-side compaction); docs/04-research/industry-engineering.md (Cognition's single-writer lesson)
40. Boris Cherny, secondary coverage: Lenny's Podcast, Feb 19, 2026, via https://tomaszs2.medium.com/is-boris-cherny-right-coding-is-solved-and-does-he-mean-typing-bfe2f68afc6a; tips compilation https://howborisusesclaudecode.com/ (2026); Every, "How to Use Claude Code Like the People Who Built It", https://every.to/podcast/how-to-use-claude-code-like-the-people-who-built-it (2025; blocked)
41. Anthropic Economic Index, data period May 2026, via the econ_index MCP tools, https://www.anthropic.com/economic-index
42. OpenAI, "Introducing the Codex app", https://openai.com/index/introducing-the-codex-app/ (Feb 2026; blocked, via sibling documents)
43. 9to5Mac, "Meta launches Muse Code AI coding agent for macOS and Linux", https://9to5mac.com/2026/08/05/meta-launches-muse-code-ai-coding-agent-for-macos-and-linux/ (Aug 2026; via docs/01-primer/history.md)
