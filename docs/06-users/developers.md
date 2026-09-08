# What developers need from a harness: jobs, pain points, and the terminal question

*Research program: agent harnesses. Written 2026-09-08. Status: verified with notes.*

Sourcing note: the proxy blocked stackoverflow.co/.blog, jetbrains.com, dora.dev, metr.org, anthropic.com, github.blog, faros.ai, cursor.com, openai.com and every.to; claims from them rest on two or more search excerpts and are marked "(via secondary)". Read directly: Claude Code docs on code.claude.com, README files on raw.githubusercontent.com (Symphony, Gas Town, the Ralph Wiggum plugin, METR's repository), Google Cloud's DORA post, and the anthropics/claude-code issue tracker via the GitHub API (reaction counts as of 2026-09-08). Revenue figures and product dates are cross-referenced to sibling documents.

## What this document answers

- What jobs developers hire a coding agent for, and what the 2025–2026 surveys and field studies say about adoption, trust and measured productivity.
- Which harness parts hurt (context loss, permission fatigue, verification and review load, cost surprises, multi-agent chaos), with primary evidence from vendor docs and the Claude Code issue tracker.
- What developers value in the CLI, which workflows are forming around it, and what would have to be true for developers to leave the terminal.

## TL;DR

- Adoption is near-universal; trust is not. 84% of Stack Overflow's 2025 respondents used or planned to use AI tools, but 46% distrust the output's accuracy against 33% who trust it, and 66% (unverified) name "almost right, but not quite" as their top frustration (via secondary) [1][2][3]. DORA 2025 puts adoption at 90% [10].
- Agents went from minority to majority in a year: 31% of developers used them in mid-2025 [2]; JetBrains reports 90% of professionals using coding agents at least weekly by mid-2026 (two search excerpts of one JetBrains post; primary not opened; unverified) [8].
- Measured productivity is contested. METR's July 2025 trial found experienced open-source developers 19% slower while believing they were 20% faster; its February 2026 follow-up produced no reliable estimate, partly because developers ran several agents at once (via secondary; the several-agents detail is unverified) [11][12]. Anthropic's engineers self-report roughly 50% gains (vendor study; unverified) [13].
- The bottleneck moved to review: 98% more PRs merged, review time up 91% in Faros telemetry (vendor, via secondary; unverified) [15]; DORA finds AI now helps throughput but still hurts stability [10].
- The loudest complaints are harness complaints. Claude Code's most-commented issue is about usage limits (1,494 comments, 694 thumbs-up; by thumbs-up it trails the closed AGENTS.md request and three open feature requests); permission-prompt bugs and compaction dropping CLAUDE.md follow [33]. Anthropic answered permission fatigue with a classifier, "auto mode", the default since Aug 14, 2026 [19][32].
- The terminal is becoming an engine: one engine behind terminal, IDE, desktop, web, phone and chat [17], and every lab and most independents ship an "agent manager" that runs many CLI sessions in git worktrees [26][39][42]. For the thesis: "the CLI cannot be the default surface" is supported; "developers will leave the terminal" is not. They are leaving *watching* the terminal.

## 1. Vocabulary for a newcomer

Analogy: hiring a contractor. The model is the contractor's skill. The harness is the job site: the keys you hand over (permissions), the plans on the wall (context), the inspector who signs off (verification), the foreman who runs several crews (orchestration). Most complaints about "AI coding" are complaints about the job site.

Precisely: **context loss** is when the loop's window fills and the harness summarises ("compacts") history, dropping instructions with it. **Permission fatigue** is the approval-prompt stream a manual permission layer produces. The **verification burden** is human time spent proving output correct; at the pull-request stage it is the **review bottleneck**. A **git worktree** is a second checkout of the same repository so parallel agents never edit the same files. An **agent manager** is a surface listing many running sessions with their diffs and PR status.

## 2. Jobs to be done

Anthropic's internal study found its engineers use Claude most for fixing code errors and learning the codebase, and that 27% of assisted work "would not have been done otherwise" (via secondary; unverified) [13]. Its product page leads with "automate the work you keep putting off": tests, lint fixes, merge conflicts, dependency updates, release notes [17]. In the Anthropic Economic Index (chat and Cowork, not Claude Code; May 2026), computer-and-mathematical tasks are the largest occupational category (23.8%), software development is 11.5% of request topics, and 48.6% of conversations are "automation" style, where the person directs Claude to finish the task [41].

| Job | Harness demand | Limiting part today |
| --- | --- | --- |
| Understand unfamiliar code | Read many files without polluting the main conversation | Context [18] |
| Fix a bug from a symptom | Reproduce, edit, test, iterate | Loop plus a verification signal [18] |
| Clear the tedious backlog | Run unattended over many files | Permissions and cost [20][30] |
| Ship a feature as a PR | Plan, implement, verify, git | Surface for review [31] |
| Review a change | Diff, evidence, CI, second opinion | Verification [15][29] |
| Run recurring jobs | Scheduling, background runtime, notifications | Runtime [31] |
| Multiply oneself | Many isolated sessions, one overview | Orchestration and cost [21][23] |

## 3. How developers use agents today: the numbers

| Source (date) | Sample | Finding | Caveat |
| --- | --- | --- | --- |
| Stack Overflow 2025 (Jul 2025) [1][2][3][6] | Annual survey | 84% use or plan to use AI; 46% distrust accuracy vs 33% trust, 3% highly trust; agents 31% use, 17% plan, 38% never; 66% "almost right"; 45% say debugging AI code takes longer; trust reported down from 40% to 29% | Via secondary; 84%, 46%/33%/3% and 31/17/38 confirmed by search excerpts; 66%, 45% and the 40%-to-29% comparison (unverified); 33% and 29% may be different questions |
| Stack Overflow 2026 (opened Jun 23, 2026) [5] | In field | New agent and ROI sections; results not found by Sep 8, 2026 | "Agent usage doubled" line appears in secondary coverage of the launch post; the comparison behind it is unverified |
| JetBrains 2025 (Oct 2025) [7] | 24,534 developers | 85% use AI regularly; 62% rely on an assistant, agent or AI editor; 44% call it integrated into their workflow | Via secondary (unverified) |
| JetBrains agents post (Aug 2026) [8] | Professionals, May–Jul 2026 | 90% use coding agents weekly, 68% daily | Two search excerpts of one post; primary not opened (unverified) |
| DORA 2025 (Sep 2025) [10] | ~5,000 professionals | 90% use AI (+14 points on 2024's "more than 75 percent"); >80% believe it raised productivity; positive link to throughput, negative to stability; "AI doesn't fix a team; it amplifies what's already there" | Read directly |
| METR RCT (Jul 2025) [11] | 16 experienced OSS developers, 246 tasks | 19% slower; forecast 24% faster; perceived 20% faster | Via secondary; repository data confirms 16 developers, 246 tasks, the 0.188 regression and a ~24% forecast implied by per-task predictions |
| METR follow-up (Feb 2026) [12] | 57 developers, 800+ tasks (unverified) | Returning developers 18% slower (CI −38% to +9%); new developers 4% slower (−15% to +9%); data judged unreliable | Estimates, CIs and the refusal to work without AI confirmed via a search excerpt of METR's post; time on task unmeasurable with several agents running (unverified) |
| Anthropic internal (Dec 2025) [13] | 132 engineers, 53 interviews | Claude in ~60% of work; ~50% self-estimated gain; skill-erosion worries | Vendor study; via secondary (unverified) |
| GitHub Octoverse (Oct 2025) [14] | Platform data | 43.2M PRs merged per month (+23%); 1.1M public repos import an LLM SDK (+178%); ~80% of new developers use Copilot in week one | Via secondary (unverified) |

Usage disclosures follow the same curve: Claude Code passed a $1B run-rate in December 2025 and $2.5B in February 2026 (vendor claims); Cursor went from $2B annualized in early 2026 to $4B in June and was sold to SpaceX for $60B; Codex reported 5M weekly users in June 2026 [16] (all unverified here: the vendor and press pages are blocked). anthropics/claude-code has ~144k GitHub stars, openai/codex ~123k [34].

Two details in the METR follow-up matter more than the estimates: recruitment failed because developers would not work without AI, and measurement failed for "developers running several agents at once" (unverified) [12]. By early 2026 the working unit had changed from a developer with an assistant to a developer supervising a fleet.

## 4. Pain points with current harnesses

### 4.1 Context loss

Anthropic's guide: "Claude's context window fills up fast, and performance degrades as it fills"; "the context window is the most important resource to manage" [18]. The remedies are manual hygiene: `/clear` between tasks, `/compact` with instructions, subagents for exploration, and a CLAUDE.md under 200 lines because bloated files "cause Claude to ignore your actual instructions" [18][20]. The tracker shows the failures: "/compact causes Claude Code to ignore CLAUDE.md" (Jul 2025), a `--no-auto-compact` request (Aug 2025, 47 reactions), and May–June 2026 reports of auto-compact never firing at 100% [33]. Opus 5 brought a 1M-token window in July 2026; the docs still describe degradation as it fills [32][18].

### 4.2 Permission fatigue

The docs concede manual mode is "safe but tedious. After the tenth approval you're clicking through rather than reviewing" [18]. The rule language (allow, deny, ask, prefixes, sandbox) then fails in practice: a prompt triggered by `cd` in compound commands (Feb 2026, 206 reactions, open), "Allow all edits" ignored (Oct 2025), allow lists ignored (Aug and Dec 2025), subagents not inheriting permissions (Jan 2026, 71) [33]. Remote Control even offers, after "several permission prompts", to "approve tool calls from your phone" [28].

The fix replaced the human with a model. Auto mode was a research preview in March 2026, reached Pro in May, and became the default for new sessions on Pro, Max and Team from August 14, 2026 [32]. "A second model, the classifier, reviews actions instead of you", blocking what "escalates beyond your request, targets unrecognized infrastructure, or appears driven by hostile content"; the mode table lists its purpose as "reducing prompt fatigue", and `bypassPermissions` is now for "isolated containers and VMs only" [19].

### 4.3 Verification burden and the review bottleneck

The Stack Overflow figures (66% "almost right", 45% "debugging takes longer"; both unverified) are verification figures [2][3]. The guide explains why: "Without a check it can run, 'looks done' is the only signal available, and you become the verification loop"; "if you can't verify it, don't ship it" [18]. Its ladder (a test in the prompt, a `/goal` condition, a Stop hook that blocks the turn until a script passes, an adversarial subagent review) is a list of ways to remove the human from the loop [18].

At team scale the load lands on reviewers. Faros: 21% more tasks, 98% more PRs, review time up 91%, PR size up 154%, bugs up 9%, no company-level improvement (vendor, via secondary; unverified) [15]. DORA: "an increase in change volume leads to instability" without strong testing and fast feedback [10]. The vendors answer with agent reviewers: Code Review runs "a fleet of specialized agents" plus a verification step, averages 20 minutes and $15–25, and "never blocks merging" [29]; `/code-review` and cloud "ultrareview" arrived in April–July 2026 [32]; OpenAI's Symphony requires "proof of work" (CI status, review feedback, complexity analysis, walkthrough videos) [35]. None removes the human reader; they change what is read.

### 4.4 Cost surprises

Cost is a harness property. The docs give a baseline (about $13 per developer per active day; 90% under $30) and then "Why usage climbs in a long session": the full conversation is resent each request, cache misses reprocess everything, scheduled tasks and teammates keep spending, and compaction "is itself a large request" [20]. Parallelism multiplies it: "running ten agents in parallel uses quota roughly ten times as fast" [21]; agent teams use about 7x tokens in plan mode [20]; workflows warn at 25 agents or 1.5M projected tokens [25].

Developers feel it as limits. The most-commented issue in the tracker is "Instantly hitting usage limits with Max subscription" (Jan 2026; 1,494 comments, 694 thumbs-up, open; by thumbs-up it trails the closed AGENTS.md request at 5,124 and open feature requests at 1,178, 791 and 700), alongside Opus 4.6 consuming far more quota than 4.5 (Feb 2026) and a Max 20 plan exhausted about 70 minutes after a reset (Apr 2026) [33]. Subscriptions now draw on "a rolling five-hour window and a weekly window" [20]; the weekly window dates from August 2025, after users ran Claude Code continuously in the background, and Cursor's June–July 2025 pricing change ended in an apology (both from training knowledge; unverified, though the tracker shows weekly limits in force by October 2025, #9424). In April 2026 Anthropic barred third-party harnesses from Pro and Max subscriptions on cost grounds, then sold metered "Agent SDK" credits in May [39]. Whoever owns the harness owns the bill.

### 4.5 Multi-agent chaos

The vendor documents it: agent teams are "experimental and disabled by default"; "two teammates editing the same file leads to overwrites"; unattended teams risk "wasted effort"; teammates do not survive `/resume` [24]. The tracker adds subagents that "recursively spawn unbounded child agents, causing exponential fan-out and massive token burn" (Jun 2026) and an `Esc` that "kills ALL background tasks" (Jan 2026) [33]. Gas Town's README: "4-10 agents become chaotic", fixed by persisting work in "git-backed hooks" to "scale comfortably to 20-30 agents" [36]. Cognition's lesson, in the practitioner document, was a single writer with extra agents contributing "intelligence rather than actions" [39].

## 5. What developers value in the CLI

- **It runs where the code runs.** "Claude Code is composable and follows the Unix philosophy. Pipe logs into it, run it in CI, or chain it with other tools" [17]. `claude -p` reads stdin, returns JSON with `total_cost_usd`, sets exit codes, and `--bare` gives "the same result on every machine" [30].
- **Configuration as code.** CLAUDE.md, settings, hooks, skills and subagent definitions live in git; hooks "are deterministic and guarantee the action happens" [18]. Teams share a harness by sharing a repository.
- **Control.** Plan mode, `Esc`, `/rewind` checkpoints, `/clear`, permission rules, and the option to drop the guardrails inside a container [18][19].
- **Context economy.** "CLI tools are the most context-efficient way to interact with external services" [18].
- **Parallelism by habit.** Tabs and tmux. Boris Cherny, who leads Claude Code, is reported to run five terminal tabs plus web sessions and said in February 2026 that "coding is largely solved" (via secondary; unverified) [40]; his 2025 rationale for a terminal product (the one environment every engineer has; works over SSH and in CI) is a paraphrase (unverified).

What the CLI does not give: a diff you can comment on, ten sessions at a glance, PR and CI status, screenshots. Those are exactly what the desktop app's "Coming from the CLI" section adds [26]. Yet new entrants still choose the terminal: Meta's Muse Code launched terminal-only in August 2026, with sub-agents in parallel worktrees and no app (unverified) [43].

## 6. Emerging workflows

```
 idea / ticket / CI failure / Slack message
        |
   dispatch: CLI tab | `claude agents` | desktop | web VM | routine | @Claude
        |
   +----------+----------+----------+
   | worktree | worktree | cloud VM |   isolated checkouts, one agent loop each
   |  tests   |  tests   |  tests   |   a check the agent can read closes the loop
   +----------+----------+----------+
        |
   pull requests --> CI + agent review (Code Review, Symphony "proof of work")
        |
   human review queue   <-- the bottleneck the whole stack is built around
```

**Worktrees.** `claude --worktree feature-auth` creates a checkout under `.claude/worktrees/`; the desktop gives "every new session its own worktree automatically"; Claude Code blocks edits and git commands aimed at the main checkout, and subagents can declare `isolation: worktree` [22]. `/batch` splits a change across "5 to 30 subagents" that each open a PR [23]. Cursor 3's Agents window (April 2026), the Codex app (February 2026) and Muse Code do the same [39][42][43].

**Background and cloud agents.** `claude agents` is "one screen for all your background sessions: what's running, what needs your input, and what's done", with PR labels coloured by check status (research preview, May 2026) [21][32]. Web sessions run in Anthropic-managed VMs, persist after the laptop closes, are monitored from the phone, and move with `--cloud` and `--teleport` [27]. Codex cloud, GitHub's Copilot cloud agent (the name is confirmed on github.com/features/copilot) and Jules (unverified) fill the same slot [39]; Routines fire cloud sessions from a schedule, a GitHub event or an API call [31][32].

**Pull-request loops.** Auto-fix subscribes a session to a PR: "when a check fails or a reviewer leaves a comment, Claude investigates and pushes a fix if one is clear" [27]. Code Review posts severity-tagged inline findings and collects thumbs to tune the reviewer [29]. Symphony "turns project work into isolated, autonomous implementation runs, allowing teams to manage work instead of supervising coding agents", spawning one run per Linear issue (repo created Feb 26, 2026; ~27k stars) [35]. The PR is now the agent's unit of accountability; the docs warn that an agent's reply can trigger comment-driven deploy automation [27].

**Orchestration tools.** The Ralph Wiggum loop, credited to Geoffrey Huntley and shipped as an official plugin, is "a Bash loop" whose Stop hook blocks exit and re-feeds the prompt until a completion phrase appears, with `--max-iterations` as "the primary safety mechanism" [37]. Gas Town (Steve Yegge; ~18k stars) adds a "mayor", "polecats" and git-backed "beads" so work survives restarts [36]. Dynamic workflows (May 2026) put the plan in a script Claude writes, with up to 16 concurrent agents and 1,000 per run [25]; agent teams remain experimental [24]. The shared design: state in git or files, not context; a verification gate to stop; hard caps on spend.

**Agent-manager desktops.** Claude Code Desktop offers parallel worktree sessions, a diff view with inline comments, a "Review code" button, a CI bar with Auto-fix and Auto-merge, panes for browser, terminal, files and subagents, computer use (research preview), Dispatch from the phone, and a Linux beta [26]. The Codex app launched as a "command center" for parallel agents (macOS Feb 2, 2026; unverified) [42][39]. Independents converged on the same shape: Conductor (reported $22M Series A, March 2026; unverified), Superset ("orchestrate 100+ coding agents in parallel. Run any agent with your own subscription", ~14k stars), Claude Squad (~8.5k), Vibe Kanban (~28k stars; product killed April 2026) and Terragon (shut down January 2026) [38][39]. The manager sits on a CLI engine, and two standalone managers died within a year.

## 7. Leaving the terminal: what would have to be true, and what developers would refuse to give up

1. **Verification is visible and cheap at fleet scale.** Diffs with comments, test evidence, screenshots, CI status and a second opinion in one glance per session. The desktop diff view and CI bar are the best attempt so far; agent view's one-line summaries are the CLI's [21][26].
2. **The permission model is better than the terminal's, not merely hidden.** Classifier plus sandbox plus organisation policy must make "what can this fleet touch" legible [19].
3. **Nothing scriptable is lost.** Hooks, headless runs, exit codes, JSON output and settings in git must survive; a surface that hides project state in a proprietary store will be rejected by the people it targets [18][30].
4. **It runs where the code runs.** SSH, WSL, containers, self-hosted cloud environments [26][27].
5. **Cost is visible per task and per fleet, with budgets.** `/usage` attribution and "Large workflow" warnings are early versions [20][25].
6. **Surfaces are exits, not prisons.** `--teleport`, `/desktop`, Remote Control and cross-session messaging already move sessions; developers will leave the terminal only if they can come back [17][27][28].

They would refuse to give up: the shell and their own tools; plain-text configuration in git; keyboard-only operation; `Esc` and rewind; local execution with secrets staying local; the raw engine (`claude -p`) for scripts and CI; and the freedom to bring their own subscription or model, which Anthropic's April 2026 policy showed can be revoked from above [18][30][38][39].

## What this means for the thesis

**Supports.**
- The harness is the bottleneck for developer throughput. Gains leak in review, verification, cost and coordination, all harness parts: Faros's doubled review time, DORA's stability penalty, METR's inability to measure fleet-running developers [15][10][12]. Anthropic's 2026 release history is almost entirely harness: auto mode, agent view, worktrees, workflows, Code Review, routines, Remote Control [32].
- "Neither CLI nor desktop is the default" matches what the vendor built: "each surface connects to the same underlying Claude Code engine" [17]. Developers already mix tabs, web sessions and a phone.

**Contradicts.**
- The terminal is still the engine and the fallback, and new entrants launch terminal-first (Muse Code, August 2026) [43]. Everything developers say they value is a terminal virtue [17][18].
- The largest harness business won on the IDE, and the labs bundle the agent manager with the engine; standalone managers died or were absorbed [16][39]. A startup "reimagining the developer harness" competes with a free tab in the vendor's app.
- The measured gain for experienced developers is still uncertain, which weakens any pitch that a better harness alone unlocks a known prize [11][12].

**Nuance.**
- What developers want reimagined is the supervision layer, not the surface: a review queue, a fleet view, budgets, and permissions that scale past ten agents. The terminal cannot do this well, and the labs have only begun (Code Review does not block merges, Symphony is a spec, agent teams are experimental) [29][35][24].
- The defensible position for an outsider is verification and proof of work across any engine, the one thing neither the terminal nor the current managers do, and where DORA and Faros say value leaks [10][15]. Generic managers without it (Vibe Kanban, Terragon) did not survive [38][39].

## Open questions and unverified claims

- Stack Overflow 2026 results not found as of 2026-09-08 (re-checked the same day with the same result); the "agent usage doubled" line is secondary and unverified [5].
- JetBrains August 2026 figures (90% weekly, 68% daily) rest on two search excerpts of one post; the post itself could not be opened [8].
- METR's 2026 point estimates and confidence intervals were confirmed from a search excerpt of METR's post, but its 57-developer, 800-task sample and the "several agents" detail, and the Anthropic, Octoverse and Faros figures, remain unverified; Faros and Anthropic are vendor studies [12][13][14][15].
- The 33% and 29% trust figures for Stack Overflow 2025 may be different questions; the secondary pages carrying both figures were blocked on re-check [2][6].
- Cherny's terminal rationale is a paraphrase; the "five tabs" detail is from a tips compilation; neither could be re-verified (every.to, medium.com and howborisusesclaudecode.com blocked) [40].
- The August 2025 weekly-limit rationale and Cursor's July 2025 pricing apology are from training knowledge with no URL; the tracker's #9424 (Oct 2025) confirms only that weekly limits were in force by then.
- Gas Town's public release date is unverified; the GitHub API gives a repository creation date of 2025-12-16 and 17,972 stars on 2026-09-08 [36].
- DORA's seven "AI capabilities" are not listed because they appear only in an image [10].
- Copilot "cloud agent" naming was confirmed on github.com/features/copilot; Jules details still rely on the sibling labs document (jules.google and docs.github.com blocked) [39].
- Revenue and user figures for Cursor, Claude Code and Codex are vendor or press claims cross-referenced from the value-capture document and could not be re-verified (Dealroom, TechCrunch and anthropic.com blocked) [16].

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
33. anthropics/claude-code issue tracker via the GitHub API, reaction counts as of 2026-09-08: #16157 https://github.com/anthropics/claude-code/issues/16157 (Jan 2026), #41788 (Apr 2026), #23706 (Feb 2026), #38239 and #37917 (Mar 2026), #28240 https://github.com/anthropics/claude-code/issues/28240 (Feb 2026), #29214 (Feb 2026), #18950 (Jan 2026), #9348 (Oct 2025), #13340 (Dec 2025), #6850 (Aug 2025), #4017 (Jul 2025), #1157 (May 2025), #6689 (Aug 2025), #63015 (May 2026), #66144 (Jun 2026), #68110 (Jun 2026), #68619 (Jun 2026), #21167 (Jan 2026); reaction comparison on 2026-09-08 via the GitHub issue-search endpoint: #6235 (5,124 thumbs-up, closed), #45596 (1,178, open), #18435 (791, open), #36151 (700, open), #9424 (Oct 2025 weekly-limit complaint)
34. GitHub repositories via the GitHub API, 2026-09-08: anthropics/claude-code (~144k stars) https://github.com/anthropics/claude-code; openai/codex (~123k) https://github.com/openai/codex
35. OpenAI, Symphony README, https://github.com/openai/symphony (repository created Feb 26, 2026; ~27k stars; read via raw.githubusercontent.com); announcement https://openai.com/index/open-source-codex-orchestration-symphony/ (Apr 2026; blocked)
36. Gas Town README, https://github.com/gastownhall/gastown (formerly steveyegge/gastown; 17,972 stars and repository created 2025-12-16 per the GitHub API on 2026-09-08; read via raw.githubusercontent.com, Sep 2026)
37. Anthropic, Ralph Wiggum plugin README, https://github.com/anthropics/claude-code/tree/main/plugins/ralph-wiggum (read via raw.githubusercontent.com, Sep 2026)
38. Orchestrator repositories via the GitHub API, 2026-09-08: BloopAI/vibe-kanban (~28k stars) https://github.com/BloopAI/vibe-kanban; superset-sh/superset (~14k) https://github.com/superset-sh/superset; smtg-ai/claude-squad (~8.5k) https://github.com/smtg-ai/claude-squad; Vibe Kanban shutdown notice https://www.vibekanban.com/blog/shutdown (Apr 2026, via sibling document)
39. Sibling documents in this program: docs/02-landscape/developer-harnesses-independent.md (Cursor 3, Conductor, Terragon, Vibe Kanban, Superset); docs/02-landscape/developer-harnesses-labs.md (Codex app, Copilot cloud agent, Jules, Muse Code); docs/01-primer/history.md (2026 timeline, Remote Control, Routines, the April 2026 subscription policy); docs/01-primer/anatomy.md (auto mode default, server-side compaction); docs/04-research/industry-engineering.md (Cognition's single-writer lesson)
40. Boris Cherny, secondary coverage: Lenny's Podcast, Feb 19, 2026, via https://tomaszs2.medium.com/is-boris-cherny-right-coding-is-solved-and-does-he-mean-typing-bfe2f68afc6a; tips compilation https://howborisusesclaudecode.com/ (2026); Every, "How to Use Claude Code Like the People Who Built It", https://every.to/podcast/how-to-use-claude-code-like-the-people-who-built-it (2025; blocked)
41. Anthropic Economic Index, data period May 2026, via the econ_index MCP tools, https://www.anthropic.com/economic-index
42. OpenAI, "Introducing the Codex app", https://openai.com/index/introducing-the-codex-app/ (Feb 2026; blocked, via sibling documents)
43. 9to5Mac, "Meta launches Muse Code AI coding agent for macOS and Linux", https://9to5mac.com/2026/08/05/meta-launches-muse-code-ai-coding-agent-for-macos-and-linux/ (Aug 2026; via docs/01-primer/history.md)

## Verification notes (2026-09-08)

Method: search budget was exhausted after four queries, so most checks ran through direct fetches of reachable pages (code.claude.com, cloud.google.com, github.com, raw.githubusercontent.com), the GitHub repository-search and issue-search endpoints (the repository API and the rendered issue pages did not expose reaction counts), the METR dataset in its repository, and the Anthropic Economic Index tools. Everything on a blocked domain that could not be covered by two search excerpts is marked "(unverified)" inline.

| # | Claim | Verdict | Evidence and notes |
| --- | --- | --- | --- |
| 1 | Stack Overflow 2025: 84% use or plan to use AI; 46% distrust vs 33% trust, 3% highly trust; agents 31% use, 17% plan, 38% never | Confirmed | Two search excerpts (survey.stackoverflow.co/2025/ai and the press release); pages blocked |
| 2 | Stack Overflow 2025: 66% "almost right", 45% debugging takes longer, trust down from 40% to 29% | Unverified | The one excerpt found says only that "almost right" is the top frustration and debugging the runner-up; the 29%/40% pair appears in one secondary excerpt; shiftmag.dev, intelligenttools.co, adtmag.com and survey.stackoverflow.co blocked |
| 3a | Stack Overflow 2026 survey opened Jun 23, 2026; no results by Sep 8, 2026 | Confirmed | Launch-post URL and date returned by search; a fresh results search returned only 2025 data and the launch post |
| 3b | "Agent usage doubled" | Unverified | Appears in secondary coverage of the launch post (earezki.com title); which surveys are compared could not be checked; stackoverflow.blog blocked |
| 4 | JetBrains Aug 2026: 90% of professionals use coding agents at least weekly, 68% daily, May-Jul 2026 | Unverified against the primary | A second search excerpt matches the figures and period exactly and adds Claude Code at ~39% of professionals (18% in Jan 2026); blog.jetbrains.com and every secondary tried were blocked |
| 5 | JetBrains 2025: 24,534 respondents; 85%; 62%; 44% | Unverified | infoworld.com and blog.jetbrains.com blocked; no search budget left |
| 6 | DORA 2025: 90% use AI; >80% believe productivity rose; ~5,000 respondents; "AI doesn't fix a team; it amplifies what's already there"; throughput up, stability down | Confirmed | Google Cloud announcement read directly ("nearly 5,000", "30% report little or no trust"); the "+14 points" is derived from DORA 2024's "more than 75 percent" (75.9%), not stated in the 2025 post |
| 7 | METR Jul 2025: 16 developers, 246 tasks, 19% slower, forecast 24% faster, perceived 20% faster | Confirmed | data_complete.csv in METR's repository has 16 unique dev_id and 246 issues; README reports the 0.188 coefficient; per-task predictions imply a 23.7% expected speedup; the 20% perceived figure comes from a search excerpt (metr.org and arxiv.org blocked) |
| 8a | METR Feb 2026: returning developers -18% (CI -38% to +9%), new developers -4% (-15% to +9%), data judged unreliable, recruitment hurt by developers unwilling to work without AI | Confirmed | Search excerpt quoting METR's post verbatim; metr.org, metr.substack.com and four commentaries blocked |
| 8b | METR Feb 2026: 57 developers, 800+ tasks; time on task unmeasurable with several agents running | Unverified | Not in the excerpt obtained; no METR 2026 repository exists on GitHub |
| 9 | Anthropic internal study: 132 engineers, 53 interviews, ~60% of work, ~50% gain, 27% not otherwise done, Dec 2025 | Unverified | anthropic.com, interviewquery.com and news.ycombinator.com blocked |
| 10 | Octoverse 2025: 43.2M PRs merged per month (+23%), 1.1M repos import an LLM SDK (+178%), ~80% of new developers use Copilot in week one | Unverified | github.blog, octoverse.github.com, infoworld.com, visualstudiomagazine.com and forbes.com blocked |
| 11 | Faros: 21% more tasks, 98% more PRs, review time +91%, PR size +154%, bugs +9%, no company-level gain | Unverified | faros.ai, getunblocked.com and dev.to blocked |
| 12 | Issue #16157: 694 thumbs-up, 1,494 comments, open, Jan 2026; #28240 206 reactions (Feb 2026, open); #6689 47 (Aug 2025); #18950 71 (Jan 2026, open); #23706 Feb 2026; #63015 May 2026; #66144 Jun 2026 | Confirmed, one claim corrected | All counts and dates match the issue-search endpoint exactly, and #16157 is first when sorted by comments. It is not the top-reacted issue: #6235 (AGENTS.md, closed) has 5,124 thumbs-up, and open requests #45596 (1,178), #18435 (791) and #36151 (700) rank above it. TL;DR and section 4.4 now say "most-commented" |
| 13 | Stars: claude-code ~144k, codex ~123k, symphony ~27k (created Feb 26, 2026), vibe-kanban ~28k, superset ~14k, claude-squad ~8.5k | Confirmed | Repository-search endpoint on 2026-09-08: 144,463; 122,544; 27,105 (created 2026-02-26); 28,038 (last push 2026-04-24, consistent with the April shutdown); 13,951; 8,451 |
| 14 | Gas Town: "4-10 agents become chaotic", "git-backed hooks", "20-30 agents", mayor, polecats, beads; ~18k stars; release date | Confirmed except the date | Raw README carries every quoted phrase; 17,972 stars; repository created 2025-12-16; the public announcement date remains unverified |
| 15 | Auto mode: research preview Mar 2026, Pro in May, default for new sessions on Pro, Max and Team from Aug 14, 2026; classifier quotes; "reducing prompt fatigue"; bypassPermissions for "isolated containers and VMs only" | Confirmed | What's-new weeks 13, 21 and 32 and the permission-modes page, read directly |
| 16 | Opus 5 with a 1M-token window in July 2026 | Confirmed | What's-new week 30 (July 20-24, 2026) |
| 17 | Claude Code docs quotes: Code Review "fleet of specialized agents", verification step, 20 minutes, $15-25, never blocks merging; costs ~$13/day, 90% under $30, ~7x tokens for plan-mode teammates, five-hour and weekly windows; workflows 16 concurrent, 1,000 per run, warning at 25 agents or 1.5M tokens; agent view quotes and "ten agents ... ten times"; /batch 5 to 30 subagents; overview and best-practices quotes; Ralph Wiggum "Bash loop" and --max-iterations | Confirmed | Pages read directly on code.claude.com and raw.githubusercontent.com; Code Review is a research preview for Team and Enterprise |
| 18 | Economic Index (May 2026): computer-and-mathematical 23.8%, software development 11.5% of topics, automation 48.6% | Confirmed | econ_index tools: 23.8, 11.51, 48.62; period 2026-05-01; chat and Cowork only |
| 19 | Copilot cloud agent and Jules | Split | "Cloud agent" naming and issue assignment confirmed on github.com/features/copilot; Jules unverified (jules.google, developers.google.com blocked) |
| 20 | Boris Cherny: five tabs plus web sessions; "coding is largely solved" (Feb 2026); terminal rationale | Unverified | every.to, tomaszs2.medium.com and howborisusesclaudecode.com blocked |
| 21 | Weekly limits introduced Aug 2025 because of continuous background use; Cursor's June-July 2025 pricing change ended in an apology | Unverified | anthropic.com and cursor.com blocked; the docs confirm the weekly window exists today and #9424 (Oct 2025) shows it was in force by then |
| 22 | Claude Code $1B run-rate (Dec 2025) and $2.5B (Feb 2026); Cursor $2B (early 2026), $4B (Jun 2026), SpaceX acquisition for $60B; Codex 5M weekly users (Jun 2026) | Unverified | anthropic.com, claude.com, techcrunch.com and app.dealroom.co blocked |
| 23 | Muse Code terminal-only launch (Aug 2026); Codex app macOS Feb 2, 2026; Conductor $22M Series A (Mar 2026) | Unverified | 9to5mac.com and openai.com blocked; sibling documents not re-checked |

Corrections made in place:
- TL;DR and section 4.4: "top-reacted open issue" and "most-reacted issue in the tracker" changed to "most-commented issue", with the thumbs-up comparison added; source [33] lists the comparison issues.
- Section 3 table: METR 2025 caveat now records that the repository data confirms 16 developers and a ~24% implied forecast; METR 2026 sample size and the "several agents" detail marked unverified while the estimates are marked confirmed; DORA "+14 points" tied to the 2024 baseline; JetBrains 2026 caveat changed from one excerpt to two excerpts of one post.
- "(unverified)" added inline to the Stack Overflow 66%/45% figures, the Anthropic internal study, Octoverse, Faros, JetBrains 2025, Cherny, Muse Code, the Codex app date, Conductor's round, Jules, the revenue and user figures, and the weekly-limit and Cursor-pricing recollections.
- Gas Town: repository creation date (2025-12-16) and exact star count added to the open question and source [36].
- Open questions updated to say what was re-checked and what stayed blocked.

Sources that could not be opened: survey.stackoverflow.co, stackoverflow.co, stackoverflow.blog, blog.jetbrains.com, dora.dev, metr.org, metr.substack.com, arxiv.org, anthropic.com, claude.com, github.blog, octoverse.github.com, faros.ai, cursor.com, openai.com, every.to, adtmag.com, infoworld.com, visualstudiomagazine.com, forbes.com, techcrunch.com, 9to5mac.com, app.dealroom.co, interviewquery.com, news.ycombinator.com, hn.algolia.com, api.stackexchange.com, dev.to, medium.com, howborisusesclaudecode.com, jules.google, developers.google.com, vibekanban.com, shiftmag.dev, intelligenttools.co, thenewstack.io, byteiota.com, earezki.com, digitalapplied.com, antigravitylab.net, preuve.ai, uvik.net, onechatai.ai, analyticsinsight.net, blog.robbowley.net, andrewwegner.com, jasonmoon.dev, particula.tech, philippdubach.com. The GitHub repository API and the repository-scoped issue endpoint were closed to this session; the search endpoints and rendered pages were not.

Remaining doubts:
- The 33% and 29% Stack Overflow figures are most likely the same question aggregated differently (29% as "somewhat trust" alone, 33% as somewhat plus highly trust); this is the fact-checker's reading and is not confirmed.
- The JetBrains 2026 figures are consistent across two excerpts but rest on a post nobody in this program has opened.
- METR's "several agents at once" explanation for unmeasurable time on task may be a paraphrase of a different sentence in the February 2026 post.
- Vibe Kanban's April 2026 shutdown is supported only by the repository's last push (2026-04-24) and the sibling document.
- No 2026 Stack Overflow results existed in search on 2026-09-08; if they appear later, rows 1-3 of the section 3 table should be re-run.
