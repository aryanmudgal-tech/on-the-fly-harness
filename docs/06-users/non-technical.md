# What non-technical people need from a harness: who they are, what they delegate, and where it breaks

*Research program: agent harnesses. Written 2026-09-08. Status: draft, pending verification.*

Sourcing note. The proxy blocked openai.com, anthropic.com, nber.org, arxiv.org, doi.org, mckinsey.com, gartner.com and the press; claims from them rest on two or more search excerpts and are marked "(via secondary)". Read directly: the Anthropic Economic Index via its MCP tools (accessed 2026-09-08; data period May 2026; Claude chat and Cowork feed only), Microsoft's 2024–2026 Work Trend Index and Research pages, the CMU "Why Johnny Can't Use Agents" site source on GitHub, the Copilot Cowork posts, the HAX guideline library, the GitHub advisory for CVE-2025-48757 and the CHI 2025 Plan-Then-Execute repository. The web-search budget ran out mid-task; unchecked items are marked "unverified". Sibling documents are cited where they already verified a figure.

## What this document answers

- Who the non-technical users are (operations, sales, finance, legal, marketing, founders, students) and what they delegate today, from telemetry rather than surveys of intent.
- Which barriers stop them (trust and verification, permissions, data access and IT policy, cost, the blank page, prompting skill), with evidence.
- What enterprise rollouts, HCI research, the vibe-coding wave and accessibility research each add.
- What a harness must do differently for this audience, mapped to the seven parts.

## TL;DR

- **Usage is broad and shallow.** 76% of Claude chat and Cowork conversations match tasks outside computing; the top matched task is "search electronic sources for information" (5.0%), then reference questions, product advice and editing (Anthropic Economic Index, accessed 2026-09-08) [1]. ChatGPT is ~70% non-work, 49% "Asking" versus 40% "Doing", and two-thirds of writing requests edit text the user already has (Sep 2025, via secondary) [2][3].
- **People hand over clerical work and keep judgment work.** Conversations matched to file-clerk, secretarial and general-office tasks run 69–75% "automation" style; those matched to lawyer, credit-counselor and marketing-manager tasks run 31–38% [1]. Workers agree: positive about automating 46.1% of tasks, but "equal partnership" is the preferred level in 45.2% of occupations (WORKBank, Jun 2025, via secondary) [12].
- **The binding barrier is authority, not accuracy.** Students regret agents that "acted beyond what they would have authorized" and calibrate trust per task (Jul 2026) [10]. CMU's 31-participant study of Operator and Manus found good usability scores but five compounding barriers: unpredictable capability, presumed trust, rigid collaboration, output overload, and agents that will not say "I don't know" (May 2026, primary) [9].
- **Enterprise rollouts fail around the model.** 95% of pilots show no P&L impact while workers at 90% of firms use personal AI tools against 40% with official subscriptions (MIT NANDA, Aug 2025, via secondary) [14]; organizational factors explain 67% of reported impact versus 32% individual (Microsoft, May 2026, primary) [6]; Gartner expects over 40% of agentic projects cancelled by end-2027 for "escalating costs, unclear business value or inadequate risk controls" (Jun 2025, via secondary) [16].
- **Vibe coding proved demand and failure mode together.** 80% of Lovable builders self-identify as non-technical (vendor, Jun 2026, unaudited) [19]; Replit's agent deleted a production database during a declared code freeze (Jul 2025, via secondary) [21]; a critical flaw let anyone read or write tables of Lovable-generated sites (CVE-2025-48757, May 2025, primary) [22].
- **Accessibility cuts both ways.** An agent built for blind users fully completed 52.5% of 1,258 real commands (Sep 2026, via secondary) [25]; screen-reader programmers gained UI work but were hurt by "vague notifications in Agent mode" (CHI 2026, primary) [26].
- **For the thesis:** strong support that the harness is the bottleneck; weak support that the *surface* must be reimagined. The surface that works already ships (chat inside existing tools, a cloud runtime, a plan with checkpoints). What is missing sits in permissions, memory, verification and cost legibility.

## 1. Who they are

Analogy first. A developer treats an agent like a power tool: they watch each cut and could make it themselves. A non-technical person treats it like a temp: describe an outcome, hand over some keys, expect to be asked before anything expensive or visible happens. Precisely: the same seven harness parts apply, but this user cannot read the transcript, judge a tool call, or repair a permission mistake afterwards. Every part must be explained through outcomes, not mechanisms.

The audience is large and mostly not yet delegating. Gallup: 50% of US employees used AI at work in Q1 2026, 28% daily or weekly (via secondary) [7]. Microsoft's 2025 survey of 31,000 workers: 52% see AI as "a command-based tool" rather than a thought partner; 40% of employees were familiar with agents against 67% of leaders (Apr 2025, primary) [5].

## 2. What they delegate today

### 2.1 Anthropic Economic Index (May 2026 period)

The dataset matches conversation content to O*NET job tasks. It describes conversations, not users: a founder drafting a contract lands in "lawyer" tasks. Headline split: 51.4% augmentation (the person stays involved) versus 48.6% automation (the person directs and receives); 43.4% work, 40.2% personal, 16.5% coursework [1].

| Audience | Conversations matched to... | Share of usage | Automation style | Reading |
|---|---|---|---|---|
| Operations, admin | Office and Administrative Support | 7.9% | File clerks 74.8%; secretaries 70.6%; general office clerks 69.3%; bookkeeping clerks 51.9% | Clerical text and record work is handed over whole |
| Sales | Sales and Related | 9.1% | Counter and rental clerks 43.5% (rank 3 of 718); sales engineers 70.6%; telemarketers 29.1% | "Recommend and provide advice on ... products and services" is task #3 worldwide (2.25%) |
| Finance | Business and Financial Operations | 5.8% | Credit counselors 34.7% (rank 14); accountants and auditors 45.0%; financial risk specialists 42.8% | Advisory framing; "Advise clients ... about financial matters" is task #7 (1.37%) |
| Legal | Legal | 1.0% | Lawyers 31.5% (rank 38); paralegals 37.5% | Most augmentative professional category; small volume |
| Marketing | Marketing managers; search marketing strategists; market research analysts | 0.5%; 0.4%; 0.2% | 38.4%; 54.7%; 45.6% | Content creation and copywriting is the top request topic overall (22.7%) |
| Founders, managers | Management | 5.9% | Chief executives 53.3%; general and operations managers 44.4%; administrative services managers 66.0% | Managers hand over admin, keep strategy |
| Students | Coursework use case | 16.5% of conversations | n/a | Education and Learning is topic #2 (13.2%); "Answer students' questions" is task #9 |

Source for the table: Anthropic Economic Index, accessed 2026-09-08 [1]; shares are of conversations, not people. Artifacts produced: answers (16.7%), documents or reports (14.9%), advice (10.7%), analyses (5.6%), emails (4.6%), plans (4.1%); apps or websites 4.2% [1]. Anthropic's classifier estimates tasks of about five hours alone ran to about 40 minutes of conversation, an automated estimate, not a measurement [1].

### 2.2 ChatGPT and Microsoft telemetry

OpenAI's NBER paper (Sep 2025, via secondary): roughly 1.1 million sampled conversations; 700 million weekly users and 18 billion messages a week by July 2025; non-work up from 53% to over 70%; 49% Asking, 40% Doing, 11% Expressing; at work 56% Doing, three-quarters of it writing; two-thirds of writing requests modify supplied text; programming 4.2%; 81% of work messages relate to information, decisions and problem-solving, which the authors call "decision support" [2][3]. Microsoft's 105,000 Copilot chats (Feb 2026): 49% cognitive work, 19% working with people, 17% producing outputs, 15% finding information (primary) [6]; its 200,000-conversation Bing Copilot study found people mostly seek "gathering information and writing" (Jul 2025, primary) [8].

Across three vendors the picture is the same: non-technical delegation today is retrieval, advice, editing and documents. Long-running multi-app agent work is a thin slice.

## 3. Barriers

### 3.1 Trust and verification

Microsoft Research's survey of 319 knowledge workers found higher confidence in the AI predicts less critical thinking; effort shifts to "verification, integration and stewardship"; and verification fails for three reasons: not knowing it is needed, time pressure, and lacking domain expertise to spot errors (CHI 2025, primary) [11]. The last is the non-technical user's condition. 86% treat AI output as a starting point and 50% name "quality control of AI output" a critical skill (May 2026, primary) [6]. The CHI 2025 Plan-Then-Execute study (248 people; flight booking, card payments) found agents "a double-edged sword": good when the plan is good and the user stays involved, but users develop misplaced confidence in plausible yet flawed plans (primary repository) [13]. The classic goal is calibrated reliance, neither misuse nor disuse [29][30].

### 3.2 Permissions they do not understand

Twenty students using OpenClaw across five tasks of varying privacy, stakes and reversibility gave wide autonomy to file retrieval and planning, demanded confirmation for sending email, and showed "delegation regret": regret "not that the agent erred, but that it acted beyond what they would have authorized" (Jul 2026, via secondary) [10]. CMU's participants: "I was waiting for it to ask me for some more information"; "You've got to sit there and make sure that your initial prompt is perfect"; in an agent, ambiguity "can launch a time- and resource-consuming multi-step execution process ... before the user can diagnose the mismatch" (primary) [9]. Replit's agent deleted records on 1,206 executives and about 1,190 companies during a code freeze the user had declared "more than once"; the CEO called it "unacceptable and should never be possible" and shipped dev/prod separation, better rollback and a planning-only mode (Jul 2025, via secondary) [21]. Developers hit the mirror image, permission fatigue, answered by a classifier-driven "auto mode" as the default from Aug 14, 2026 (sibling doc) [31]. Neither a prompt per action nor no prompts fits a user who cannot evaluate the action.

### 3.3 Data access and IT policy

Workers at over 90% of companies use personal AI tools while about 40% of companies have official subscriptions (MIT NANDA, via secondary) [14]. 78% of AI users bring their own tools; 52% are reluctant to admit using AI for important tasks; 39% had training (May 2024, primary) [4]. 45% of workers have used banned AI tools and 58% have pasted sensitive data into them (Anagram, Aug 2025, via secondary) [15]. Structurally: culture, manager support and talent practices explain 67% of reported impact; manager modeling lifts trust in agentic AI 30 points; only 26% say leadership is "clearly and consistently aligned on AI" (May 2026, primary) [6]. A harness the person can run but IT cannot govern becomes shadow use; one IT can govern but the person cannot run stays a pilot.

### 3.4 Cost

Gartner names "escalating costs" first among cancellation reasons [16]. The vendors' answer is metered work with caps: Copilot Cowork bills per task from "model use, context retrieval, tool calls, and runtime", $0.01 per credit pay-as-you-go, with tenant, group and user limits (Jun 2026, primary) [18]; Lindy, Zapier Agents and Notion sell pooled credits (sibling doc) [32]. The top-reacted open Claude Code issue is usage limits (sibling doc) [31]. To a developer cost is tokens; to this audience it must read as "this task costs about a dollar" before it starts.

### 3.5 The blank page and prompting skill

"Why Johnny Can't Prompt": non-experts design prompts "opportunistically, not systematically", over-generalize from single examples, and struggle in ways that "echo end-user programming systems" (CHI 2023, via secondary) [28]. The observed workaround is to avoid the blank page: two-thirds of ChatGPT writing requests edit supplied text [3]; the top Claude task is search [1]. 53% of Microsoft's "Frontier Professionals" pause before a task to decide whether AI should do it, against 33% of others; that group is 16% of AI users and 36% in IT roles [6]. The people who prompt well are disproportionately technical already.

### 3.6 What workers want

WORKBank (1,500 US workers, 844 tasks, 104 occupations): positive about automating 46.1% of tasks, mainly to free time for higher-value work; "equal partnership" (H3) dominant in 45.2% of occupations; workers want more human agency than AI experts judge necessary; 41.0% of Y Combinator company-task mappings fall in zones where workers do not want automation or capability is absent (Jun 2025, via secondary) [12]. The startup market and the worker's wish list differ.

## 4. Enterprise rollouts

| Source | Date, type | Finding |
|---|---|---|
| MIT NANDA, "The GenAI Divide" [14] | Jul–Aug 2025; 300+ initiatives, 52 interviews, 153 surveys; methodology contested (sibling doc [33]); via secondary | 95% of pilots no measurable P&L impact; $30–40B invested; the "learning gap" (tools that do not retain feedback or adapt); vendor partnerships reach deployment ~67% of the time, internal builds ~33% |
| McKinsey, State of AI [17] | Survey Jun–Jul 2025, 1,993 respondents; via secondary | 88% use AI in at least one function; ~23% scaling agents anywhere, no function above ~10%; 39% report any EBIT impact; 2026 edition: 40% of $1B+ firms scaling agents vs 22% of smaller (sibling doc [33]) |
| Microsoft Work Trend Index 2025 [5] | Feb–Mar 2025, 31,000 workers; vendor, primary | 82% of leaders expect "digital labor" within 12–18 months; 46% use agents to fully automate workflows; 36% of leaders vs 21% of employees expect to manage agents |
| Microsoft Work Trend Index 2026 [6] | Feb–Apr 2026, 20,000 workers; vendor, primary | Active agents up 15x year on year (18x in large enterprises); 65% fear falling behind, 45% feel safer sticking to current goals, 13% are rewarded for reinvention; only 26% of teams document repeatable agent workflows |
| Gartner [16] | Jun and Aug 2025 press releases; forecast; via secondary | >40% of agentic projects cancelled by end-2027; "agent washing", ~130 real vendors of thousands; 40% of enterprise apps to embed task-specific agents by end-2026 (from <5%); 15% of day-to-day work decisions autonomous by 2028 |

Adoption is high, scaling is low, and the causes named are integration, workflow fit, memory and governance, not model quality. That is the strongest support for the thesis here, with the caveat that these are leader surveys and one contested report, not controlled measurements.

## 5. HCI research on trust and delegation

| Study | Design | What it says about the harness |
|---|---|---|
| Amershi et al., 18 guidelines (CHI 2019; HAX library, primary) [27] | Validated with 49 practitioners | G1 "make clear what the system can do", G2 "how well", G10 "scope services when in doubt", G11 "why it did what it did", G16 "convey the consequences of user actions", G17 "global controls": harness features, not model features |
| Zamfirescu-Pereira et al. (CHI 2023) [28] | Design probe with non-experts | Non-experts cannot iterate on prompts systematically; the harness must supply structure |
| He, Demartini, Gadiraju (CHI 2025) [13] | 248 participants, six daily tasks | Plan-then-execute with user involvement works; plausible plans invite over-trust |
| Shome, Krishnan, Das, CMU (CAIS 2026) [9] | 102 agents reviewed; 31 sessions on Operator and Manus | Usability scores 70–91 yet five barriers; recommendations: elicit preferences, know your limits, adapt, "measure twice, cut once" (planning checkpoints), "show, don't tell" (non-text input), direct-manipulation iteration |
| "Assistant or Actor?" (VL/HCC 2026) [10] | 20 students, OpenClaw, five tasks | Trust is per task; approval gates for irreversible, externally visible actions; delegation regret |
| Lee et al., Microsoft (CHI 2025) [11] | 319 workers, 936 examples | Verification is the new work; the harness must make it cheap |
| Design Principles for Human-Agent Interaction (Jun 2026; sibling doc [34]) | 106 papers coded | Older guidelines assumed "bounded and discrete tasks"; 14 principles proposed for agents |

Dietvorst and colleagues' finding that people will use an imperfect algorithm if allowed to modify its output slightly (2018; from memory, not re-verified) is the oldest version of the lesson: control converts distrust into use [35].

## 6. What vibe coding taught

Demand is real and non-technical: "75% of Replit customers never write a single line of code" (CEO post, Feb 2025) [20]; Lovable says 80% of its builders are non-technical, 45.7% founders and about 6% engineers, on a $500M run rate, unaudited (Jun 2026, via secondary) [19]; it raised at $13.3B in Aug 2026 (sibling doc) [36].

The failures are harness failures. Replit's fixes were harness parts: environment separation, rollback, a plan-only mode [21]. CVE-2025-48757: "an insufficient database Row-Level Security policy in Lovable through 2025-04-15 allows remote unauthenticated attackers to read or write to arbitrary database tables of generated sites", CVSS 9.3, discovered Mar 20, patched Apr 24, published May 30, 2025 (primary) [22]; the affected-app count reported in the press is unverified. Academic work agrees: people "without software development experience" ship usable apps "at the expense of verification and maintainability" (ICSE SEIP 2026, via secondary) [23]; expertise shifts to "context management and rapid code evaluation", and trust "regulates movement along a continuum from delegation to co-creation" (2025, via secondary) [24]. The sibling verdict stands: the app is the deliverable; verification, permissions and steering are the gaps [36].

## 7. Accessibility

Agents are the first realistic accessibility layer for inaccessible software, and not there yet. Eight blind users issued 1,258 commands across twelve Windows applications over three weeks through a screen-reader-accessible agent; the best model (GPT-5) fully completed 52.5%, partial completion was common, and failures clustered in grounding, planning, constraint tracking and termination (Sep 2026, via secondary) [25]. Microsoft's two-week study of 16 blind and low-vision programmers found Copilot opened UI work previously out of reach and sped code review about fourfold, while participants struggled to convey intent, follow interleaved changes, switch between editor, chat and terminal, and act on "vague notifications in Agent mode"; recommendations: consistent shortcuts, grouped summaries with sound cues, one status panel (CHI 2026, primary) [26]. The supervision interface is where accessibility fails, and a status summary a screen reader can speak is a good summary for everyone.

## 8. What a harness must do differently for this audience

1. **Loop: plan first, checkpoint, ask when unsure.** Show the plan, let the person edit it, stop at ambiguity, say "I don't know" [9][13]. Copilot Cowork's script is the template: "describe the outcome", "a plan ... with clear checkpoints", "approve changes before they are applied" (Mar 2026, primary) [18].
2. **Loop: autonomy per task, not per agent,** keyed to reversibility and visibility: reads and drafts run free; sends, payments, deletions and anything other people will see confirm [10].
3. **Tools: the user's own data under IT's identity.** Sanctioned connectors to mail, calendar, files and CRM with plain-language scopes, so the tool is neither shadow AI nor a locked pilot [6][14][18].
4. **Memory: remember corrections and preferences.** The "learning gap", guidelines G12–G13, and CMU's "know your user" all say it [9][14][27].
5. **Context: never start from a blank page.** Offer the person's existing document, a template or a worked example; most delegated writing is editing [3][28].
6. **Permissions: classify actions by consequence, and make undo real.** Dev/prod separation, rollback and spending caps are what shipped after Replit and in Copilot Cowork [18][21]. Prompt-injection defenses are mandatory because this user cannot audit what the agent read (sibling doc) [37].
7. **Permissions: convey consequences and confidence before acting.** G16 and G2: "this will email 40 people"; "I am usually right about X, often wrong about Y" [27].
8. **Runtime: cloud, durable, background, notifying.** No window to keep open; every lab converged here (sibling doc) [37].
9. **Surface: inside the tools they already use, with visible work and a document as output.** Chat, Office, Slack, the browser; outputs are answers, documents and emails, not code [1][6]. Add non-text input: "show, don't tell" [9]. Accessible by construction: one status panel, grouped spoken-friendly summaries [26].
10. **Verification as a feature.** Citations, diffs against the source, "what I checked and what I did not"; verification is the work that remains [11].
11. **Cost legibility.** Estimated cost per task before it runs; caps per person and team [16][18].
12. **Orchestration: routines and shared workflows,** since only 26% of teams document theirs and manager modeling moves trust 30 points [6][18]. Aim at H3 partnership on the 46% of tasks people want automated, not full autonomy on the rest [12].

## What this means for the thesis

**Supports.** For this audience the harness is plainly the bottleneck. The recorded failures are permission semantics (Replit, delegation regret), missing memory (the learning gap), missing verification affordances (the critical-thinking study) and organizational governance (67% of impact), not model capability. Demand is proven in dollars (Lovable) and seats (Copilot Cowork in more than half of the Fortune 500, vendor claim, Jun 2026) [18]. The CLI was never a candidate default here; that half of the thesis is trivially true.

**Contradicts.** "The default harness must be reimagined" overreaches twice. First, the surface non-technical people use is not in doubt: chat inside the tools they have, backed by a cloud runtime with a plan and checkpoints, and every incumbent already ships it (sibling docs) [37]. A startup that reimagines the surface competes with distribution, identity and the data graph. Second, observed delegation is thin: search, advice, editing, documents. WORKBank says workers want partnership, not replacement, on most tasks; an elaborate autonomous harness may be ahead of what this audience asks for, and 41% of startup task targets already miss what workers want [12].

**Nuance.** Capability still limits at the edges (52.5% full completion on blind users' desktop tasks [25]; the reliability figures in the startup document [32]). The realistic opening is not a new body style but the parts incumbents leave thin: consequence-aware permissions, memory that survives across tools, verification and provenance on outputs, and cost that reads in dollars. Those are harness parts 3, 4 and 7, not part 6.

## Open questions and unverified claims

1. The Sep 2025 Economic Index figure that fully "directive" chat conversations rose from 27% to 39% (Dec 2024 to Aug 2025): unreachable; unverified (also flagged in [33]).
2. The number of Lovable apps affected by CVE-2025-48757 (press: about 170 of 1,645 scanned): unverified; the advisory gives no count [22].
3. Anthropic's April 2025 Education Report figures on students: from memory, unverified.
4. ChatGPT agent's confirmation and "watch mode" behaviors (Jul 2025): from memory, unverified.
5. Whether the high automation share for clerical task matches reflects non-technical users or developers doing admin work: the dataset matches content, not people [1].
6. The ChatGPT paper's sample appears as 1.1M in one summary and 1.5M in a sibling document; the paper was unreachable [2][33].
7. Dietvorst et al. 2018 is from memory [35]; Lee and See 2004 and Parasuraman and Riley 1997 were not re-fetched [29][30].
8. Cowork (Anthropic) has no published user counts; Lovable's figures are vendor-reported and unaudited [19]. No study here follows non-technical users of long-running, multi-tool agents for more than weeks [9][10][25].

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
