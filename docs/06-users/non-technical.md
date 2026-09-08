# What non-technical people need from a harness: who they are, what they delegate, and where it breaks

*Research program: agent harnesses. Written 2026-09-08. Status: draft, pending verification.*

Sourcing note. Blocked by the proxy: openai.com, anthropic.com, nber.org, arxiv.org, doi.org, mckinsey.com, gartner.com and the press; claims from them rest on two or more search excerpts and are marked "(via secondary)". Read directly: the Anthropic Economic Index via its MCP tools (accessed 2026-09-08; data period May 2026; Claude chat and Cowork only), Microsoft's Work Trend Index and Research pages, the CMU "Why Johnny Can't Use Agents" site source on GitHub, the Copilot Cowork posts, the HAX guideline library, the CVE-2025-48757 advisory and the CHI 2025 Plan-Then-Execute repository. The web-search budget ran out mid-task; unchecked items are marked "unverified".

## What this document answers

- Who the non-technical users are (operations, sales, finance, legal, marketing, founders, students) and what they delegate today, from telemetry rather than surveys of intent.
- Which barriers stop them (trust and verification, permissions, data access and IT policy, cost, the blank page, prompting skill), with evidence.
- What enterprise rollouts, HCI research, vibe coding and accessibility research each add.
- What a harness must do differently for this audience, mapped to the seven parts.

## TL;DR

- **Usage is broad and shallow.** 76% of Claude chat and Cowork conversations match tasks outside computing; the top matched task is "search electronic sources for information" (5.0%), then reference questions, product advice and editing (Anthropic Economic Index, accessed 2026-09-08) [1]. ChatGPT is ~70% non-work, 49% "Asking" versus 40% "Doing", and two-thirds of writing requests edit text the user supplied (Sep 2025, via secondary) [2][3].
- **People hand over clerical work and keep judgment work.** Conversations matched to file-clerk, secretarial and office-clerk tasks run 69–75% "automation" style; those matched to lawyer, credit-counselor and marketing-manager tasks run 31–38% [1]. Workers want automation for 46.1% of tasks but "equal partnership" in 45.2% of occupations (WORKBank, Jun 2025, via secondary) [12].
- **The binding barrier is authority, not accuracy.** Students regret agents that "acted beyond what they would have authorized" (Jul 2026) [10]; CMU's 31-participant study of Operator and Manus found good usability scores but five compounding barriers, from unpredictable capability to agents that will not say "I don't know" (May 2026, primary) [9].
- **Enterprise rollouts fail around the model.** 95% of pilots show no P&L impact; workers at 90% of firms use personal AI tools against 40% with official subscriptions (MIT NANDA, Aug 2025, via secondary) [14]; organizational factors explain 67% of reported impact (Microsoft, May 2026, primary) [6]; Gartner expects over 40% of agentic projects cancelled by end-2027 (Jun 2025, via secondary) [16].
- **Vibe coding proved demand and failure mode together.** 80% of Lovable builders self-identify as non-technical (vendor, Jun 2026, unaudited) [19]; Replit's agent deleted a production database during a declared code freeze (Jul 2025, via secondary) [21]; a critical flaw exposed Lovable-generated databases (CVE-2025-48757, May 2025, primary) [22].
- **Accessibility cuts both ways.** An agent built for blind users fully completed 52.5% of 1,258 real commands (Sep 2026, via secondary) [25]; screen-reader programmers gained UI work but were hurt by "vague notifications in Agent mode" (CHI 2026, primary) [26].
- **For the thesis:** strong support that the harness is the bottleneck; weak support that the *surface* must be reimagined. The working surface already ships (chat inside existing tools, a cloud runtime, a plan with checkpoints). The gaps are permissions, memory, verification and cost legibility.

## 1. Who they are

Analogy first. A developer uses an agent like a power tool: they watch each cut and could make it themselves. A non-technical person uses it like a temp: describe an outcome, hand over some keys, expect to be asked before anything expensive or visible happens. Precisely: the same seven harness parts apply, but this user cannot read the transcript, judge a tool call, or repair a permission mistake afterwards, so every part must be explained through outcomes.

The audience is large and mostly not yet delegating. Gallup: 50% of US employees used AI at work in Q1 2026, 28% daily or weekly (via secondary) [7]. Microsoft's 2025 survey of 31,000 workers: 52% see AI as "a command-based tool"; 40% of employees were familiar with agents against 67% of leaders (Apr 2025, primary) [5].

## 2. What they delegate today

### 2.1 Anthropic Economic Index (May 2026 period)

The dataset matches conversation content to O*NET job tasks; it describes conversations, not users, so a founder drafting a contract lands in "lawyer" tasks. Headline: 51.4% augmentation (the person stays involved) versus 48.6% automation (the person directs and receives); 43.4% work, 40.2% personal, 16.5% coursework [1].

| Audience | Matched category or occupations | Share of usage | Automation style | Note |
|---|---|---|---|---|
| Operations, admin | Office and Administrative Support | 7.9% | File clerks 74.8%; secretaries 70.6%; office clerks 69.3% | Clerical text and records handed over whole |
| Sales | Sales and Related | 9.1% | Counter and rental clerks 43.5% (rank 3 of 718); sales engineers 70.6% | "Recommend and provide advice on ... products and services" is task #3 worldwide (2.25%) |
| Finance | Business and Financial Operations | 5.8% | Credit counselors 34.7% (rank 14); accountants and auditors 45.0% | "Advise clients ... about financial matters" is task #7 (1.37%) |
| Legal | Legal | 1.0% | Lawyers 31.5% (rank 38); paralegals 37.5% | Most augmentative professional category |
| Marketing | Marketing managers; search marketing strategists | 0.5%; 0.4% | 38.4%; 54.7% | Content creation and copywriting is the top request topic (22.7%) |
| Founders, managers | Management | 5.9% | Chief executives 53.3%; general and operations managers 44.4% | Admin handed over, strategy kept |
| Students | Coursework use case | 16.5% | n/a | Education and Learning is topic #2 (13.2%) |

Source: [1]; shares are of conversations, not people. Artifacts produced: answers 16.7%, documents or reports 14.9%, advice 10.7%, analyses 5.6%, emails 4.6%, plans 4.1%, apps or websites 4.2% [1].

### 2.2 ChatGPT and Microsoft telemetry

OpenAI's NBER paper (Sep 2025, via secondary): about 1.1 million sampled conversations; 700 million weekly users by July 2025; non-work up from 53% to over 70%; 49% Asking, 40% Doing, 11% Expressing; at work 56% Doing, three-quarters of it writing; two-thirds of writing requests modify supplied text; programming 4.2%; 81% of work messages are "decision support" (information, decisions, problem-solving) [2][3]. Microsoft's 105,000 Copilot chats (Feb 2026): 49% cognitive work, 19% working with people, 17% producing outputs, 15% finding information (primary) [6]; its 200,000-conversation Bing Copilot study found people mostly seek "gathering information and writing" (Jul 2025, primary) [8]. Three vendors, one picture: delegation today is retrieval, advice, editing and documents. Long-running multi-app agent work is a thin slice.

## 3. Barriers

### 3.1 Trust and verification

A Microsoft Research survey of 319 knowledge workers found that higher confidence in the AI predicts less critical thinking; effort shifts to "verification, integration and stewardship"; and verification fails when people do not know it is needed, lack time, or lack the domain expertise to spot errors (CHI 2025, primary) [11]. The last is the non-technical user's condition. 86% treat AI output as a starting point; 50% name "quality control of AI output" a critical skill (May 2026, primary) [6]. The CHI 2025 Plan-Then-Execute study (248 people; flight booking, card payments) found agents "a double-edged sword": good when the plan is good and the user stays involved, but plausible yet flawed plans earn misplaced confidence (primary repository) [13]. The classic goal is calibrated reliance, neither misuse nor disuse [29][30].

### 3.2 Permissions they do not understand

Twenty students using OpenClaw across five tasks of varying privacy, stakes and reversibility gave wide autonomy to file retrieval and planning, demanded confirmation for sending email, and showed "delegation regret": regret "not that the agent erred, but that it acted beyond what they would have authorized" (Jul 2026, via secondary) [10]. CMU's participants: "I was waiting for it to ask me for some more information"; "You've got to sit there and make sure that your initial prompt is perfect"; in an agent, ambiguity "can launch a time- and resource-consuming multi-step execution process ... before the user can diagnose the mismatch" (primary) [9]. Replit's agent deleted records on 1,206 executives and about 1,190 companies during a code freeze the user had declared "more than once"; the CEO called it "unacceptable and should never be possible" and shipped dev/prod separation, better rollback and a planning-only mode (Jul 2025, via secondary) [21]. Developers hit the mirror image, permission fatigue, answered by a classifier-driven "auto mode" default from Aug 14, 2026 (sibling doc) [31]. Neither a prompt per action nor no prompts fits a user who cannot evaluate the action.

### 3.3 Data access and IT policy

Workers at over 90% of companies use personal AI tools; about 40% of companies have official subscriptions (MIT NANDA, via secondary) [14]. 78% of AI users bring their own tools, 52% are reluctant to admit using AI for important tasks, 39% had training (May 2024, primary) [4]. 45% of workers have used banned AI tools and 58% have pasted sensitive data into them (Anagram, Aug 2025, via secondary) [15]. Culture, manager support and talent practices explain 67% of reported impact; manager modeling lifts trust in agentic AI 30 points; only 26% say leadership is "clearly and consistently aligned on AI" (May 2026, primary) [6]. A harness the person can run but IT cannot govern becomes shadow use; one IT can govern but the person cannot run stays a pilot.

### 3.4 Cost

Gartner names "escalating costs" first among cancellation reasons [16]. Vendors answer with metered work and caps: Copilot Cowork bills per task on "model use, context retrieval, tool calls, and runtime", $0.01 per credit, with tenant, group and user limits (Jun 2026, primary) [18]; Lindy, Zapier Agents and Notion sell pooled credits (sibling doc) [32]. The top-reacted open Claude Code issue is usage limits (sibling doc) [31]. To a developer cost is tokens; to this audience it must read as "about a dollar" before the task starts.

### 3.5 The blank page and prompting skill

"Why Johnny Can't Prompt": non-experts design prompts "opportunistically, not systematically", over-generalize from single examples, and struggle in ways that "echo end-user programming systems" (CHI 2023, via secondary) [28]. The observed workaround is to avoid the blank page: two-thirds of ChatGPT writing requests edit supplied text [3]; the top Claude task is search [1]. 53% of Microsoft's "Frontier Professionals" pause before a task to decide whether AI should do it, against 33% of others; that group is 16% of AI users and 36% in IT roles [6]. Those who prompt well are disproportionately technical already.

### 3.6 What workers want

WORKBank (1,500 US workers, 844 tasks, 104 occupations): positive about automating 46.1% of tasks, mainly to free time for higher-value work; "equal partnership" (H3) dominant in 45.2% of occupations; workers want more agency than AI experts judge necessary; 41.0% of Y Combinator company-task mappings fall where workers do not want automation or capability is absent (Jun 2025, via secondary) [12].

## 4. Enterprise rollouts

| Source | Date, type | Finding |
|---|---|---|
| MIT NANDA, "The GenAI Divide" [14] | Jul–Aug 2025; 300+ initiatives, 52 interviews, 153 surveys; methodology contested [33]; via secondary | 95% of pilots no measurable P&L impact; $30–40B invested; the "learning gap" (tools that do not retain feedback); vendor partnerships deploy ~67% of the time, internal builds ~33% |
| McKinsey, State of AI [17] | Jun–Jul 2025, 1,993 respondents; via secondary | 88% use AI in one function or more; ~23% scaling agents anywhere, no function above ~10%; 39% report any EBIT impact; 2026 edition: 40% of $1B+ firms scaling agents vs 22% of smaller [33] |
| Microsoft WTI 2025 [5] | Feb–Mar 2025, 31,000 workers; vendor, primary | 82% of leaders expect "digital labor" within 12–18 months; 46% use agents to fully automate workflows; 36% of leaders vs 21% of employees expect to manage agents |
| Microsoft WTI 2026 [6] | Feb–Apr 2026, 20,000 workers; vendor, primary | Active agents up 15x year on year; 65% fear falling behind, 45% feel safer sticking to current goals, 13% are rewarded for reinvention; 26% of teams document repeatable agent workflows |
| Gartner [16] | Jun and Aug 2025; forecast; via secondary | >40% of agentic projects cancelled by end-2027 for "escalating costs, unclear business value or inadequate risk controls"; "agent washing", ~130 real vendors; 40% of enterprise apps to embed task-specific agents by end-2026; 15% of day-to-day work decisions autonomous by 2028 |

Adoption is high, scaling is low, and the named causes are integration, workflow fit, memory and governance, not model quality. That is the strongest support for the thesis here; the caveat is that these are leader surveys and one contested report, not controlled measurements.

## 5. HCI research on trust and delegation

| Study | Design | Harness lesson |
|---|---|---|
| Amershi et al. (CHI 2019; HAX library, primary) [27] | 18 guidelines, 49 practitioners | G1 "make clear what the system can do", G2 "how well", G10 "scope services when in doubt", G11 "why it did what it did", G16 "convey the consequences of user actions", G17 "global controls" |
| Zamfirescu-Pereira et al. (CHI 2023) [28] | Probe with non-experts | Non-experts cannot iterate on prompts; the harness must supply structure |
| He, Demartini, Gadiraju (CHI 2025) [13] | 248 participants, six tasks | Plan-then-execute with user involvement works; plausible plans invite over-trust |
| Shome, Krishnan, Das (CAIS 2026) [9] | 102 agents reviewed; 31 sessions | Usability scores 70–91 yet five barriers; fixes: elicit preferences, know your limits, adapt, planning checkpoints, non-text input, direct-manipulation iteration |
| "Assistant or Actor?" (VL/HCC 2026) [10] | 20 students, OpenClaw | Trust is per task; approval gates for irreversible, visible actions |
| Lee et al. (CHI 2025) [11] | 319 workers | Verification is the new work; make it cheap |
| Dietvorst, Simmons, Massey (2018; from memory, not re-verified) [35] | Lab experiments | People use an imperfect algorithm if they can modify its output slightly: control converts distrust into use |
| Design Principles for Human-Agent Interaction (Jun 2026) [34] | 106 papers | Older guidelines assumed "bounded and discrete tasks"; 14 agent principles |

## 6. What vibe coding taught

Demand is real and non-technical: "75% of Replit customers never write a single line of code" (CEO post, Feb 2025) [20]; Lovable says 80% of builders are non-technical, 45.7% founders, about 6% engineers, on a $500M run rate, unaudited (Jun 2026, via secondary) [19]; it raised at $13.3B in Aug 2026 (sibling doc) [36].

The failures are harness failures. Replit's fixes were harness parts: environment separation, rollback, plan-only mode [21]. CVE-2025-48757: "an insufficient database Row-Level Security policy in Lovable through 2025-04-15 allows remote unauthenticated attackers to read or write to arbitrary database tables of generated sites", CVSS 9.3, discovered Mar 20, patched Apr 24, published May 30, 2025 (primary) [22]; the affected-app count in the press is unverified. Studies agree: people "without software development experience" ship usable apps "at the expense of verification and maintainability" (ICSE SEIP 2026, via secondary) [23]; expertise shifts to "context management and rapid code evaluation", and trust "regulates movement along a continuum from delegation to co-creation" (2025, via secondary) [24]. The sibling verdict stands: the app is the deliverable; verification, permissions and steering are the gaps [36].

## 7. Accessibility

Agents are the first realistic accessibility layer for inaccessible software, and not there yet. Eight blind users issued 1,258 commands across twelve Windows applications over three weeks through a screen-reader-accessible agent; the best model (GPT-5) fully completed 52.5%, partial completion was common, and failures clustered in grounding, planning, constraint tracking and termination (Sep 2026, via secondary) [25]. Microsoft's two-week study of 16 blind and low-vision programmers found Copilot opened UI work previously out of reach and sped code review about fourfold, while participants struggled to convey intent, follow interleaved changes, switch between editor, chat and terminal, and act on "vague notifications in Agent mode"; recommendations: consistent shortcuts, grouped summaries with sound cues, one status panel (CHI 2026, primary) [26]. The supervision interface is where accessibility fails, and a status summary a screen reader can speak is a good summary for everyone.

## 8. What a harness must do differently for this audience

1. **Plan first, checkpoint, ask when unsure.** Show the plan, let the person edit it, stop at ambiguity, say "I don't know" [9][13]. Copilot Cowork's script is the template: "describe the outcome", "a plan ... with clear checkpoints", "approve changes before they are applied" (Mar 2026, primary) [18].
2. **Autonomy per task, keyed to reversibility and visibility.** Reads and drafts run free; sends, payments, deletions and anything others will see confirm [10].
3. **The user's own data under IT's identity.** Sanctioned connectors with plain-language scopes, so the tool is neither shadow AI nor a locked pilot [6][14][18].
4. **Memory of corrections and preferences.** The "learning gap", guidelines G12–G13 and CMU's "know your user" [9][14][27].
5. **No blank page.** Start from the person's document, a template or a worked example; most delegated writing is editing [3][28].
6. **Actions classified by consequence, with real undo.** Dev/prod separation, rollback and spending caps shipped after Replit and in Copilot Cowork [18][21]; prompt-injection defenses are mandatory because this user cannot audit what the agent read (sibling doc) [37].
7. **Consequences and confidence stated before acting.** G16 and G2: "this will email 40 people"; "I am usually right about X, often wrong about Y" [27].
8. **Cloud, durable, background, notifying runtime.** Every lab converged here (sibling doc) [37].
9. **Inside the tools they already use, with visible work and a document as output.** Outputs are answers, documents and emails, not code [1][6]; add non-text input, "show, don't tell" [9]; accessible by construction: one status panel, grouped summaries [26].
10. **Verification as a feature.** Citations, diffs against the source, "what I checked and what I did not" [11].
11. **Cost legibility.** Estimated cost per task before it runs; caps per person and team [16][18].
12. **Routines and shared workflows, aimed at partnership.** Only 26% of teams document workflows and manager modeling moves trust 30 points [6][18]; target H3 on the 46% of tasks people want automated, not full autonomy on the rest [12].

## What this means for the thesis

**Supports.** For this audience the harness is plainly the bottleneck. The recorded failures are permission semantics (Replit, delegation regret), missing memory (the learning gap), missing verification affordances and organizational governance (67% of impact), not model capability. Demand is proven in dollars (Lovable) and seats (Copilot Cowork in more than half of the Fortune 500, vendor claim, Jun 2026) [18]. The CLI was never a candidate default here.

**Contradicts.** "The default harness must be reimagined" overreaches twice. The surface non-technical people use is settled: chat inside the tools they have, a cloud runtime, a plan with checkpoints, and every incumbent ships it (sibling doc) [37]; a startup that reimagines the surface competes with distribution, identity and the data graph. And observed delegation is thin: search, advice, editing, documents. Workers want partnership on most tasks, and 41% of startup task targets already miss what workers want [12]; an autonomous harness may be ahead of demand.

**Nuance.** Capability still limits at the edges (52.5% completion on blind users' desktop tasks [25]; the reliability figures in the startup document [32]). The realistic opening is not a new body style but the parts incumbents leave thin: consequence-aware permissions, memory across tools, verification and provenance, cost in dollars. Those are harness parts 3, 4 and 7, not part 6.

## Open questions and unverified claims

1. The Sep 2025 Economic Index figure that "directive" chat conversations rose from 27% to 39%: unreachable; unverified [33].
2. The number of Lovable apps affected by CVE-2025-48757 (press: about 170 of 1,645 scanned): unverified [22].
3. Anthropic's April 2025 Education Report figures on students: from memory, unverified.
4. ChatGPT agent's confirmation and "watch mode" behaviors (Jul 2025): from memory, unverified.
5. Whether high automation shares for clerical task matches reflect non-technical users or developers doing admin work: the dataset matches content, not people [1].
6. The ChatGPT paper's sample appears as 1.1M in one summary and 1.5M in a sibling document [2][33].
7. Dietvorst et al. 2018 is from memory [35]; Lee and See 2004 and Parasuraman and Riley 1997 were not re-fetched [29][30].
8. Cowork has no published user counts; Lovable's figures are unaudited [19]. No study here follows non-technical users of long-running, multi-tool agents beyond weeks [9][10][25].

## Sources

1. Anthropic Economic Index, accessed 2026-09-08 via the econ_index MCP tools (dataset overview, global usage, top work tasks, occupation usage for Sales and Related, Business and Financial Operations, Legal, Office and Administrative Support, Management, "marketing"); data period 2026-05-01, snapshot 2026-06-24; https://www.anthropic.com/economic-index (primary data)
2. Chatterji, Cunningham, Deming, Hitzig, Ong, Shan, Wadman, "How People Use ChatGPT", NBER Working Paper 34255, https://www.nber.org/papers/w34255, Sep 2025 (via secondary; nber.org blocked)
3. DataCamp, "How 700 Million People Use ChatGPT", https://dcthemedian.substack.com/p/how-700-million-people-use-chatgpt, Sep 2025; Obsurfable, "How People Actually Use ChatGPT", https://obsurfable.com/resources/articles/how-people-use-chatgpt-findings, 2025 (secondary summaries of [2])
4. Microsoft, 2024 Work Trend Index, "AI at Work Is Here. Now Comes the Hard Part", https://www.microsoft.com/en-us/worklab/work-trend-index/ai-at-work-is-here-now-comes-the-hard-part, May 2024 (primary)
5. Microsoft, 2025 Work Trend Index, "The Year the Frontier Firm Is Born", https://www.microsoft.com/en-us/worklab/work-trend-index/2025-the-year-the-frontier-firm-is-born, Apr 2025 (primary)
6. Microsoft, 2026 Work Trend Index, "Agents, Human Agency, and the Opportunity for Every Organization", https://www.microsoft.com/en-us/worklab/work-trend-index/agents-human-agency-and-the-opportunity-for-every-organization, May 2026 (primary)
7. Gallup, "Rising AI Adoption Spurs Workforce Changes", https://www.gallup.com/workplace/704225/rising-adoption-spurs-workforce-changes.aspx, 2026; Tom's Hardware summary of Gallup Q1 2026 data, https://www.tomshardware.com/tech-industry/artificial-intelligence/half-of-all-us-employees-now-use-artificial-intelligence-at-work-crossing-landmark-threshold-for-first-time-gallup-data-shows-daily-and-weekly-usage-hitting-all-time-high-of-28-percent-in-q1-2026-with-65-percent-feeling-positive-about-its-impact-on-productivity, 2026 (via secondary)
8. Tomlinson et al., Microsoft Research, "Working with AI: Measuring the Occupational Implications of Generative AI", https://www.microsoft.com/en-us/research/publication/working-with-ai-measuring-the-occupational-implications-of-generative-ai/, Jul 2025 (primary)
9. Shome, Krishnan, Das, "Why Johnny Can't Use Agents: Industry Aspirations vs. User Realities with AI Agents", CAIS 2026 (May 2026); site source https://github.com/cmu-spuds/why-johnny-can-t-use-agents (primary); arXiv 2509.14528, Sep 2025 (blocked)
10. "Assistant or Actor? Student Trust, Control, and Delegation Regret When Using a General-Purpose AI Agent", arXiv 2607.18257, https://arxiv.org/abs/2607.18257, Jul 2026, accepted to IEEE VL/HCC 2026 (via secondary)
11. Lee, Sarkar, Tankelevitch, Drosos, Rintel, Banks, Wilson, "The Impact of Generative AI on Critical Thinking", CHI 2025, https://www.microsoft.com/en-us/research/wp-content/uploads/2025/01/lee_2025_ai_critical_thinking_survey.pdf, Apr 2025 (primary)
12. Shao et al., "Future of Work with AI Agents: Auditing Automation and Augmentation Potential across the U.S. Workforce", arXiv 2506.06576, Jun 2025; repository https://github.com/SALT-NLP/WORKBank (primary, README only); figures via GitHub-hosted summary https://github.com/nbremner/llm-research-wiki/blob/main/wiki/sources/2025-shao-future-work-ai-agents.md and search excerpts (via secondary)
13. He, Demartini, Gadiraju, "Plan-Then-Execute: An Empirical Study of User Trust and Team Performance When Using LLM Agents As A Daily Assistant", CHI 2025, https://github.com/RichardHGL/CHI2025_Plan-then-Execute_LLMAgent, Apr 2025 (primary repository)
14. MIT NANDA, "The GenAI Divide: State of AI in Business 2025", Jul–Aug 2025; Fortune, https://fortune.com/2025/08/18/mit-report-95-percent-generative-ai-pilots-at-companies-failing-cfo/, Aug 2025; VentureBeat, https://venturebeat.com/ai/mit-report-misunderstood-shadow-ai-economy-booms-while-headlines-cry-failure, Aug 2025 (via secondary)
15. HR Dive, "Nearly half of workers say they've used banned AI tools at work" (Anagram survey), https://www.hrdive.com/news/workers-use-banned-ai-tools-at-work/757481/, Aug 2025 (via secondary)
16. Gartner, "Gartner Predicts Over 40% of Agentic AI Projects Will Be Canceled by End of 2027", https://www.gartner.com/en/newsroom/press-releases/2025-06-25-gartner-predicts-over-40-percent-of-agentic-ai-projects-will-be-canceled-by-end-of-2027, Jun 2025; Gartner, "40% of Enterprise Apps Will Feature Task-Specific AI Agents by 2026", https://www.gartner.com/en/newsroom/press-releases/2025-08-26-gartner-predicts-40-percent-of-enterprise-apps-will-feature-task-specific-ai-agents-by-2026-up-from-less-than-5-percent-in-2025, Aug 2025 (via secondary; gartner.com blocked)
17. McKinsey, "The State of AI in 2025: Agents, Innovation, and Transformation", https://www.mckinsey.com/capabilities/quantumblack/our-insights/the-state-of-ai, Nov 2025 (via secondary; mckinsey.com blocked)
18. Microsoft 365 blog, "Copilot Cowork: A new way of getting work done", https://www.microsoft.com/en-us/microsoft-365/blog/2026/03/09/copilot-cowork-a-new-way-of-getting-work-done/, Mar 2026; "Copilot Cowork is now generally available", https://www.microsoft.com/en-us/microsoft-365/blog/2026/06/16/copilot-cowork-is-now-generally-available/, Jun 2026 (primary, vendor)
19. The Next Web, "Lovable: $500M ARR, 146 staff, 80% non-technical builders", https://thenextweb.com/news/lovable-build-economy-500m-arr-vibe-coding, Jun 2026 (via secondary; vendor report, unaudited)
20. Amjad Masad on X, "75% of Replit customers never write a single line of code", https://x.com/amasad/status/1886516600653930924, Feb 2025 (vendor claim)
21. Fortune, "AI coding tool Replit wiped database, called it a 'catastrophic failure'", https://fortune.com/2025/07/23/ai-coding-tool-replit-wiped-database-called-it-a-catastrophic-failure, Jul 2025 (via secondary)
22. GitHub Advisory GHSA-773x-pxjg-gxgx / CVE-2025-48757, https://github.com/advisories/GHSA-773x-pxjg-gxgx, May 2025; disclosure gist https://gist.github.com/lhchavez/625ee42a6c408a850d35e50f8e649de9 (primary)
23. "Vibe Coding in Practice: Motivations, Challenges, and a Future Outlook – a Grey Literature Review", ICSE SEIP 2026, https://dl.acm.org/doi/10.1145/3786583.3786866 (via secondary)
24. Sarkar and Drosos, "Vibe coding: programming through conversation with artificial intelligence", arXiv 2506.23253, Jun 2025; "Good Vibrations? A Qualitative Study of Co-Creation, Communication, Flow, and Trust in Vibe Coding", arXiv 2509.12491, Sep 2025 (via secondary)
25. "Are We There Yet? Assessing Computer-Use Agents for Blind Users' Accessible Interaction with Desktop Applications", arXiv 2609.00524, Sep 2026; "A11y-CUA Dataset: Characterizing the Accessibility Gap in Computer Use Agents", CHI 2026, arXiv 2602.09310 (via secondary)
26. Microsoft Research, "Microsoft study shows AI assistants help with development for programmers who are blind or have low vision", https://www.microsoft.com/en-us/research/articles/microsoft-study-shows-ai-assistants-help-with-development-for-programmers-who-are-blind-or-have-low-vision/, 2025–2026; paper at CHI 2026, https://dl.acm.org/doi/full/10.1145/3772318.3790726 (primary)
27. Amershi et al., "Guidelines for Human-AI Interaction", CHI 2019; HAX Design Library, https://www.microsoft.com/en-us/haxtoolkit/library/ (primary)
28. Zamfirescu-Pereira, Wong, Hartmann, Yang, "Why Johnny Can't Prompt", CHI 2023, https://dl.acm.org/doi/10.1145/3544548.3581388, Apr 2023 (via secondary)
29. Parasuraman and Riley, "Humans and Automation: Use, Misuse, Disuse, Abuse", Human Factors 39(2), 1997 (classic; not re-fetched)
30. Lee and See, "Trust in Automation: Designing for Appropriate Reliance", Human Factors 46(1), 2004 (classic; not re-fetched)
31. `docs/06-users/developers.md` (this corpus), Sep 2026: permission fatigue, auto mode default Aug 14, 2026, usage-limit issue counts
32. `docs/02-landscape/non-technical-startups-enterprise.md` (this corpus), Sep 2026: Lindy, Zapier Agents, Notion credits; Dust and Relevance approval models; AutomationBench and tau-bench reliability figures
33. `docs/02-landscape/adoption-evidence.md` (this corpus), Sep 2026: McKinsey 2026 edition, MIT NANDA methodology caveat, ChatGPT paper figures
34. `docs/04-research/academic.md` (this corpus), Sep 2026: "Design Principles for Human-Agent Interaction", arXiv Jun 2026
35. Dietvorst, Simmons, Massey, "Overcoming Algorithm Aversion: People Will Use Imperfect Algorithms If They Can (Even Slightly) Modify Them", Management Science, 2018 (from memory; not re-verified)
36. `docs/05-surfaces/browser-generated-ui.md` (this corpus), Sep 2026: Lovable revenue and valuation, "the UI they build is the deliverable"
37. `docs/02-landscape/non-technical-labs.md` (this corpus), Sep 2026: Cowork, Copilot Cowork, Claude in Chrome approvals, prompt-injection incidents, convergence on cloud runtime plus chat
