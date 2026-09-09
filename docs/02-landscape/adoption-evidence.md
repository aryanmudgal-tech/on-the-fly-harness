# Adoption evidence: who uses agents, for what, and through which surfaces

*Research program: agent harnesses. Written 2026-09-08. Status: verified with notes (fact-check and refresh pass on 2026-09-08; see the verification notes at the end).*

## What this document answers

- Who uses AI agents today, for which tasks, and how that splits between developers and everyone else.
- What share of agent use runs through chat surfaces (web, mobile, Slack, Copilot) versus terminals and IDEs, as far as public data allows.
- Where autonomous ("agentic") use is growing fastest, and where it is stalling.
- What the data says about the assumption that a CLI or a desktop app is the default way people operate agents.

## TL;DR

- **By people and messages, chat wins by two orders of magnitude.** ChatGPT reported 900M weekly users on 27 Feb 2026 and about 1B weekly users in Jul-Aug 2026 (OpenAI statements, via secondary); Google says the Gemini app passed 1B monthly users in Aug 2026 (via secondary). The largest terminal-born coding agent, OpenAI Codex, reported 8M weekly users in mid-Jul 2026 and 10M combined Codex plus ChatGPT Work users on 21 Jul 2026 (via secondary). Programming was 4.2% of ChatGPT messages in mid-2025.
- **By money and by autonomy, developer surfaces win.** Coding took roughly 55% of departmental enterprise AI spend in 2025 (Menlo Ventures, Dec 2025, via secondary). Business API traffic is mostly automation-style; consumer chat is roughly half augmentation.
- **The Anthropic Economic Index (Claude chat and Cowork feed, May 2026 period)** shows 23.8% of conversations matched to Computer and Mathematical tasks; the other 76% spread across media, education, sales, administration, management and finance. Automation-style conversations are 48.6% overall and 60.8% among conversations matched to Software Developer tasks. (All Section 2 figures were re-read from the Economic Index tools on 2026-09-08 and match.)
- **Autonomy is measurably denser on the terminal surface.** Anthropic's Jun 2026 "Cadences" report finds AI autonomy higher on Claude Code than on chat or Cowork for 26 of 31 output types, by 0.37 points on a 1-5 scale on average and 0.53 points for scripts and code snippets (report text via GitHub mirrors). The Sep 2025 report's figures of 77% automation-style conversations in first-party API traffic versus about 50% on Claude.ai, and "directive" chat conversations rising from 27% to 39% in eight months, are now confirmed.
- **Agents are still a minority tool even among developers.** 90% of software professionals use AI at work (DORA, Sep 2025), but only 14% used agents daily and 38% had no plans to (Stack Overflow, Jul 2025, via secondary; the 2026 results were still unpublished as of mid-Aug 2026). Enterprise agent scaling sits at 23% of organizations and no more than about 10% in any single business function (McKinsey, Nov 2025, via secondary); McKinsey's Aug 2026 edition adds that 32% of organizations skipped buying at least one software product because agentic coding tools let them build it in-house (via secondary).
- **Fastest growth is in coding agents and platform-embedded agents.** Codex went from about 3M weekly users (Apr 2026) to 8M (mid-Jul 2026), and OpenAI reported 10M combined Codex plus ChatGPT Work users on 21 Jul after usage "nearly doubled" in three weeks; Claude Code's run-rate revenue more than doubled in the first six weeks of 2026 to over $2.5B; the Copilot coding agent authored 1M+ pull requests in five months; enterprise coding spend rose about sevenfold in a year; active agents in Microsoft 365 grew 15x year over year (all via secondary except Microsoft, which is primary). The fastest-growing cohort inside the coding agents is non-developers: knowledge workers were about 20% of Codex's weekly users in Jun 2026 and growing more than 3x faster than developers (OpenAI, via secondary).
- **Vendors are dismantling the CLI-as-default assumption themselves.** Claude Code ships on the terminal, two IDEs, a desktop app, the web, iOS and Android, Slack, Telegram, Discord and iMessage channels, Chrome and CI (primary docs, re-fetched 2026-09-08). Codex was placed inside the ChatGPT app with role-specific plugins on 2 Jun 2026 and merged into the ChatGPT desktop app alongside "ChatGPT Work" on 9 Jul 2026 (via secondary). The direction is "one engine, many surfaces", converging on the desktop app and the chat app, not the terminal.
- **Contradiction for the thesis:** the desktop app is exactly where the two leading labs are converging their agent products. The evidence supports "the CLI is not the default" and "the harness matters"; it does not support "a desktop app cannot be the default".

## 1. How to read adoption numbers

Analogy: measuring traffic on a road. You can count cars at a toll booth (vendor telemetry: what actually flowed through one company's product), ask people how they commute (surveys: what they say they do), or read fuel receipts (spend data: what companies paid for). Each instrument answers a different question, and none covers the whole road. This document uses all three and labels which is which.

Three definitions used below:

- **Augmentation vs automation** (Anthropic's classifier): augmentation means the person stays actively involved in the task (iterating, learning, validating); automation means the person directs Claude to complete the task. These are conversation styles, not job outcomes.
- **"Agent"** means different things in different surveys. In Microsoft's data an "active agent" can be a small Copilot Studio Q&A bot; in Stack Overflow's survey it means a tool that plans and executes multi-step coding work. Treat cross-survey comparisons as rough.
- **Surface** is harness part 6 from the program definitions: CLI, IDE, desktop app, web, chat app, voice, OS.

## 2. Anthropic Economic Index: what the chat surface is used for

Source: Anthropic Economic Index MCP tools, accessed 2026-09-08 (data period 2026-05-01, snapshot dated 2026-06-24; landing page https://www.anthropic.com/economic-index). Important scope note: this dataset covers the `claude_ai` feed only, meaning Claude chat and Cowork conversations. It does not include the API or Claude Code, so it describes the chat surface, not the terminal. Numbers match conversation content to O*NET job tasks; they say nothing about who the users are. Every figure in this section was re-read from the tools on 2026-09-08 and matches; the tools describe a snapshot (Apr-May 2026 periods), not a trend series.

**Headline splits (worldwide, May 2026 period)**

| Measure | Value |
|---|---|
| Augmentation / automation | 51.4% / 48.6% |
| Work / personal / coursework | 43.4% / 40.2% / 16.5% |
| Classifier-estimated task time | about 5 hours alone vs about 40 minutes with Claude (order-of-magnitude estimate, not measured) |
| Coverage | 121 countries, 51 US states/DC, 22 job categories, 718 of 923 tracked occupations published |

**Top task categories and occupation categories (Anthropic Economic Index, accessed 2026-09-08; shares of sampled Claude chat and Cowork conversations, May 2026 period)**

| Rank | Conversations matched to tasks of this occupation category | Share | Rank | Conversations matched to this O*NET work task | Share |
|---|---|---|---|---|---|
| 1 | Computer and Mathematical tasks | 23.8% | 1 | Search electronic sources, databases or repositories for information | 4.95% |
| 2 | Arts, Design, Entertainment, Sports, and Media tasks | 13.6% | 2 | Search reference materials to answer patrons' reference questions | 3.74% |
| 3 | Educational Instruction and Library tasks | 12.8% | 3 | Recommend and advise on a wide variety of products and services | 2.25% |
| 4 | Sales and Related tasks | 9.1% | 4 | Answer user inquiries about computer software or hardware operation | 1.98% |
| 5 | Office and Administrative Support tasks | 7.9% | 5 | Write new programs or modify existing programs | 1.47% |

Read these as "conversations matched to X tasks", not "X professionals use Claude"; the dataset does not identify users. The smallest of the 22 categories is Farming, Fishing, and Forestry tasks at 0.04%.

**Share of conversations matched to tasks by job category (top 10 of 22)**

| Job category | Share of conversations |
|---|---|
| Computer and Mathematical | 23.8% |
| Arts, Design, Entertainment, Sports, and Media | 13.6% |
| Educational Instruction and Library | 12.8% |
| Sales and Related | 9.1% |
| Office and Administrative Support | 7.9% |
| Management | 5.9% |
| Business and Financial Operations | 5.8% |
| Life, Physical, and Social Science | 4.5% |
| Architecture and Engineering | 3.6% |
| Healthcare Practitioners and Technical | 3.3% |
| (Legal, for comparison) | 1.0% |

Three of every four chat conversations are matched to non-computing tasks. Conversations matched specifically to the Software Developers occupation are only 0.35% of usage (rank 70 of 718); coding conversations are spread across many computing occupations, which is why the category is large and the single occupation is small. Those Software Developer-matched conversations skew toward automation: 60.8% automation vs 39.2% augmentation, compared with 48.6% automation overall.

**Request topics (complete top-level list, selected rows)**

| Request topic | Share |
|---|---|
| Content Creation & Copywriting | 22.7% |
| Education & Learning | 13.2% |
| Software Development | 11.5% |
| Research & Intelligence | 10.9% |
| Hobbies & Lifestyle | 9.5% |
| Business Process & Operations | 4.7% |
| Document Processing & Extraction | 4.3% |
| Data Analysis & Business Intelligence | 3.8% |
| Knowledge Retrieval & Enterprise Search | 3.6% |
| Existential, Relational, and Emotional Support | 3.4% |
| Personal AI Assistant | 2.9% |
| DevOps & Infrastructure Operations | 2.8% |
| Customer Support & Service Operations | 0.6% |

Software Development plus DevOps is 14.3% of chat conversations. The most common outputs are an "explanation or answer" (16.7%), a "document or report" (14.9%) and "advice or recommendation" (10.7%); an "app or website" is 4.2%.

**Top work tasks matched (rank, share)**

| Rank | O*NET task (abridged) | Share |
|---|---|---|
| 1 | Search electronic sources or databases for information | 4.95% |
| 2 | Search reference materials to answer patrons' reference questions | 3.74% |
| 3 | Recommend and advise on a wide variety of products and services | 2.25% |
| 4 | Answer user inquiries about computer software or hardware operation | 1.98% |
| 5 | Write new programs or modify existing programs | 1.47% |
| 7 | Advise clients on financial matters | 1.37% |
| 9 | Answer students' questions | 1.15% |
| 14 | Troubleshoot program and system malfunctions | 0.93% |
| 34 | Develop Web sites | 0.52% |

The chat surface is dominated by search, advice, explanation and writing; the first coding task appears at rank 5.

**Geography.** The Anthropic Usage Index is a geography's share of Claude usage divided by its share of working-age population (1.0 = proportional). Leaders are small rich countries: Australia 6.4, Singapore 5.8, Switzerland 5.0, Luxembourg 4.9, New Zealand 4.8, Canada 4.1. The United States is 3.87 (rank 12), the United Kingdom 3.35, Germany 2.4, Japan 1.9, Brazil 0.96, India 0.3, Nigeria 0.24; Tanzania is last at 0.07. US usage is 41% work, 50% personal and 9% coursework, so even the largest market is majority personal on the chat surface. Brazil is the most work-heavy of the large countries at 57% work. Cross-country automation shares differ only slightly (US 49.6%, India 51.9%, Norway 41.3%).

## 3. Chat surfaces at large: ChatGPT and Microsoft 365 Copilot

| Source | What it measures | Key numbers | Type |
|---|---|---|---|
| Chatterji et al., "How People Use ChatGPT", NBER w34255, 15 Sep 2025 (paper text read via GitHub mirrors; nber.org blocked) | About 1M de-identified consumer messages (May 2024-Jun 2025) plus a 1.58M-message sample drawn at conversation level; user and message totals as of Jul 2025 | 700M weekly users, 18B messages/week (Jul 2025); non-work "more than 70%" (about 73%; up from 53% in Jun 2024); Asking 49% / Doing 40% / Expressing 11%; programming 4.2% of messages; Practical Guidance + Seeking Information + Writing "nearly 80%"; of work messages 56% are Doing and nearly three quarters of those are writing; writing alone is 40% of work messages | Telemetry, vendor-authored |
| OpenAI announcements, Feb and Aug 2026 (via secondary) | ChatGPT reach | 900M weekly active users on 27 Feb 2026 (from 400M in Feb 2025 and 800M in Oct 2025), with 50M+ consumer subscribers and 9M+ paying business users; "approximately one billion" weekly users per an OpenAI spokesperson in Jul 2026, and a 1B weekly users announcement reported by multiple outlets on 3-7 Aug 2026 | Vendor claim |
| OpenAI, "The state of enterprise AI", 8 Dec 2025 (post text read via GitHub mirror; openai.com blocked) | Usage data from about 1M business customers plus a survey of 9,000 workers at almost 100 enterprises | Weekly ChatGPT Enterprise messages up roughly 8x YoY; reasoning tokens per organization up about 320x; Projects and Custom GPTs up 19x year-to-date, about 20% of enterprise ChatGPT traffic; workers report saving 40-60 minutes/day; 75% say AI improved the speed or quality of their output; coding-related messages up 36% among workers outside technical functions; frontier workers (95th percentile) send 6x the median employee's messages | Telemetry plus survey, vendor-authored |
| Microsoft 2026 Work Trend Index, 5 May 2026 (primary, fetched) | 105,000 M365 Copilot chats from one week of Feb 2026; survey of 20,000 knowledge workers in 10 countries, 2,000 per country (18 Feb-7 Apr 2026) | 49% of chats support cognitive work, 19% working with people, 17% producing work, 15% finding information; 86% treat AI output as a starting point; 66% of AI users report more time on high-value work; 19% of AI users in the "frontier" zone, 50% "emergent", 16% "stalled", 10% "blocked agency", 5% "unclaimed capacity" | Telemetry plus survey, vendor-authored |
| OpenAI, "Codex is becoming a productivity tool for everyone" and a companion Codex usage paper, 2 Jun 2026 (via secondary) | Codex telemetry | More than 5M weekly Codex users, up 6x since Jan 2026 and 10x since Aug 2025; knowledge workers about 20% of users and growing more than 3x faster than developers; non-developer individual users up 137x and non-developer organizational users up 189x since Aug 2025; among adopters, engineers route 88.3% of their tokens through Codex versus 17.6% for legal roles | Telemetry, vendor-authored |
| OpenAI Economic Research, "How AI is expanding what people do at work" (Work at the Frontier, report 1), 27 Jul 2026 (post text via GitHub mirror) | 800,000+ messages from US ChatGPT users | 61.5% of work usage is generic (writing, summarizing, planning); of the occupation-specific remainder, 43.5% of messages (16.8% of all work messages) concern tasks belonging to another occupation; crossover is highest for customer experience (77%), design (75%) and HR (69%) and lowest for engineering (28%); slightly higher in 2-5 seat workspaces (18.9%) than in 100+ seat workspaces (16.3%) | Telemetry, vendor-authored |

Two points matter for the thesis. First, "Doing" (asking the model to produce output) is 40% of consumer ChatGPT messages and 56% of work messages: the chat surface already carries delegation, not just questions. Second, the ChatGPT paper and the Economic Index agree that coding is a small minority of chat use (4.2% of ChatGPT messages; 11.5-14.3% of Claude chat conversations, with Claude's user base skewing more technical). Third, the 2026 OpenAI disclosures show the chat surface absorbing agent work rather than losing it: Codex moved inside the ChatGPT app, non-developers became its fastest-growing cohort, and coding-shaped messages rose 36% among non-technical enterprise workers.

## 4. Developer surfaces: terminal, IDE, desktop, web

### 4.1 Scale of the coding agents

| Product | Figure | Date | Source and reliability |
|---|---|---|---|
| ChatGPT (for scale) | 900M weekly users (Feb 2026); about 1B weekly users (Jul-Aug 2026) | Feb-Aug 2026 | OpenAI, via secondary |
| OpenAI Codex (CLI, IDE, cloud, desktop, inside ChatGPT) | about 3M weekly users (Altman, 8 Apr 2026); more than 5M on 2 Jun 2026, "up 6x since January", which implies roughly 0.8M weekly in Jan 2026; 7M on 13 Jul 2026 and 8M within days, after Codex merged into the ChatGPT desktop app on 9 Jul; 10M combined Codex plus ChatGPT Work users on 21 Jul 2026 (OpenAI, with the small-business program launch); about 20M "active agent users" reported in Aug 2026 (window unspecified, unverified) | Apr-Aug 2026 | OpenAI statements as reported by The New Stack, 9to5Mac, Unite.AI, Constellation Research and others; via secondary and GitHub mirrors |
| Claude Code (CLI-first) | Run-rate revenue above $2.5B and "more than doubled since the beginning of 2026", business subscriptions quadrupled since 1 Jan 2026, enterprise use more than half of Claude Code revenue (Anthropic Series G announcement, 12 Feb 2026, via mirrors). An earlier draft of this document attributed the doubling to weekly active users; the announcement says revenue (corrected). "More than 10x in three months" after the May 2025 launch (unverified) | Feb 2026 | Anthropic statements via GitHub mirrors and aggregator sites; absolute user counts quoted by aggregators conflict (4.2M vs 2M weekly), unverified |
| Cursor (IDE) | $2B ARR by Feb 2026 and over 1M paying customers (Apr 2026); about 2M monthly active users (single secondary source, unverified); acquired by SpaceX in an all-stock deal of about $60B announced 16 Jun 2026 and closed 14 Aug 2026, at roughly $2.6B annualized revenue (Reuters, TechCrunch, CNBC, via secondary digests) | Feb-Aug 2026 | Via secondary, vendor-derived |
| GitHub Copilot | 80% of new GitHub developers use Copilot within their first week; Copilot coding agent authored 1M+ pull requests between May and Sep 2025; 180M+ developers on GitHub | Oct 2025 | Octoverse 2025, via secondary (github.blog blocked) |
| Claude Code awareness/adoption among developers | awareness 31% (Q2 2025) to 57% (Jan 2026); adoption 3% to 18% (unverified) | Jan 2026 | Unnamed developer survey summarized by uvik.net; unverified, source site blocked |

Even at 8-10M weekly users, the largest terminal-born agent reaches about 1% of ChatGPT's weekly audience. Against the developer population (180M GitHub accounts, of which a fraction are professional developers), 2-8M weekly users of each coding agent is meaningful but not majority adoption.

### 4.2 What developer surveys and experiments say

| Study | Sample and date | Findings | Type |
|---|---|---|---|
| Stack Overflow Developer Survey 2025 (via secondary; survey site blocked) | 49,009 responses, 166 countries, fielded 29 May-23 Jun 2025 | 84% use or plan to use AI tools; 51% of professional developers use them daily; trust 33% vs distrust 46%; favorable sentiment 60% (from 70%+ in 2024); 66% frustrated by "almost right" answers. Agents: 14.1% use agents daily at work; 23% "regularly"; 13.8% use AI only in copilot/autocomplete mode; 37.9% have no plans to use agents | Survey, independent |
| Stack Overflow 2026 survey | Opened 23 Jun 2026; the launch post says agent usage "has doubled", measured against a 27 May 2026 Stack Overflow agents pulse survey rather than as a clean year-over-year figure (via secondary) | Results not published as of 19 Aug 2026 (survey.stackoverflow.co/2026 returned 404 in a third-party check); pages claiming "2026 results" recycle 2025 numbers | Survey, pending |
| DORA 2025, "State of AI-assisted Software Development", 23 Sep 2025 (primary announcement re-fetched 2026-09-08) | nearly 5,000 technology professionals, 100+ hours of interviews | 90% use AI at work (+14 points YoY, unverified); median about 2 hours/day (single secondary source); more than 80% believe AI raised their productivity; 30% report little or no trust in AI-generated code; "AI amplifies what's already there" | Survey, Google-sponsored |
| DORA and Google Cloud, "The ROI of AI-assisted Software Development", Apr 2026, v2026.1 (via GitHub mirrors of summaries; dora.dev blocked) | A financial-modeling framework rather than a new survey; worked example of a 500-engineer organization at $176k fully loaded salary | Value follows a J-curve: an initial productivity dip from workflow adaptation, verification overhead and downstream absorption of more code, then recovery; the worked example shows an $8.4M investment, about $11.6M first-year value, 39% ROI and about an 8-month payback; cites 35-40% gains on simple greenfield tasks versus 10% or less on complex legacy code. The 2026 "State of AI-assisted Software Development" survey report had not been released as of 2026-09-08 | Framework, Google-sponsored |
| METR, Jul 2025 (GitHub repository README re-fetched 2026-09-08; metr.org and arXiv blocked) | 16 experienced open-source developers, 246 tasks, Cursor Pro with Claude 3.5/3.7 Sonnet | Developers were 18.8% slower with AI allowed (METR's blog rounds this to "19% longer"; CI roughly +2% to +39%), while forecasting 24% faster beforehand and believing 20% faster afterwards | Randomized experiment, independent |
| METR update, 24 Feb 2026 (via GitHub mirrors and the study's data repository; metr.org blocked) | 57 developers (10 returning from the 2025 study, 47 new), 143 repos, 800+ tasks, median 10 years' experience, pay cut from $150 to $50/hour | METR says AI "likely provides productivity benefits in early 2026" but calls its own data "very weak evidence": the 10 returning developers show about -18% completion time (CI -38% to +9%), the 47 new developers about -4% (CI -15% to +9%), neither distinguishable from zero (negative means faster); 30-50% of developers said they declined to submit tasks they did not want to do without AI, which biases the estimate downward; METR judged the signal unreliable and is redesigning the experiment around fixed tasks | Experiment, independent |
| METR, "Measuring the Self-Reported Impact of Early-2026 AI on Technical Worker Productivity", 11 May 2026 (via secondary) | 349 technical workers surveyed | Median self-reported change in the value of their work of 1.4-2x, which METR itself flags as likely overstated; a self-report, not a measurement, so it mainly documents the perception gap | Survey, independent |
| Anthropic internal study, "How AI is transforming work at Anthropic", 2 Dec 2025 (via GitHub mirrors; anthropic.com blocked) | 132 engineers and researchers surveyed Aug 2025, 53 interviews, Claude Code logs | 27% of Claude-assisted work would not otherwise have been done; self-reported productivity gain about 50% (from about 20% a year earlier); Claude used for about 60% of work; staff say they can fully delegate 0-20% of their work; engineers become more "full-stack" | Survey plus telemetry, vendor-authored |

Pattern: near-universal use of AI assistance, minority use of agents, a large perception-versus-measurement gap (self-reports of 1.4-2x against measured effects indistinguishable from zero), and productivity effects that were negative in mid-2025 and, on METR's own reading, probably positive but unmeasurable with its 2026 design.

### 4.3 Surface sprawl: the terminal agents leave the terminal

The most direct evidence about surfaces comes from vendor documentation, which is primary. Claude Code's docs (fetched 2026-09-08) list the following, all running "the same underlying engine":

| Surface | Vendor description |
|---|---|
| CLI | "the most complete surface for terminal-native work: scripting and the Agent SDK are CLI-only" |
| Desktop app (Claude Desktop, tabs: Chat, Cowork, Code) | "If you'd rather not use a terminal, Desktop gives you the same engine with a graphical interface"; visual diffs, parallel sessions, computer use (research preview, Pro/Max only, off by default) |
| VS Code and JetBrains | inline diffs, plan review, selection sharing |
| Web (claude.ai/code) | cloud sessions that "continue after you disconnect" |
| Mobile (iOS/Android) | "a thin client into those same cloud sessions", plus Remote Control of a local session and Dispatch (message a task from the phone; Desktop spawns a session) |
| Slack / Claude Tag | mention @Claude with a bug report, get a pull request back; Claude Tag makes @Claude an organization-shared identity |
| Channels | push events from Telegram, Discord, iMessage or webhooks into a session (research preview; Telegram, Discord and iMessage plugins confirmed in the docs) |
| GitHub Actions, GitLab CI, Code Review, Routines | headless, scheduled and event-triggered runs |

OpenAI moved in the same direction: a Codex desktop app in Feb 2026; Codex placed inside the ChatGPT app with six role-specific plugins on 2 Jun 2026; and, on 9 Jul 2026, Codex merged into the redesigned ChatGPT desktop app alongside a "ChatGPT Work" agentic mode for knowledge workers, after which OpenAI said agent usage "nearly doubled" within three weeks to 10M combined users (21 Jul 2026, via secondary). An earlier draft's figure of "2.5x in a week" could not be verified and was dropped. Read together: the engine is being made surface-agnostic, the terminal keeps the deepest feature set, and the desktop and chat apps are where the labs are steering non-terminal users.

## 5. Enterprises: experiments, scaling, and shadow use

| Source | Key numbers | Type |
|---|---|---|
| McKinsey, "The state of AI in 2025: Agents, innovation, and transformation" (Nov 2025; 1,993 respondents in 105 countries, fielded 25 Jun-29 Jul 2025) and "The state of AI in 2026: On the road to ROI" (25 Aug 2026; 1,719 respondents in 97 countries, fielded 4 May-8 Jun 2026), via secondary (mckinsey.com blocked) | 2025: 23% of respondents scaling an agentic system somewhere; 62% experimenting; no single function above roughly 10% scaled agent deployment; chatbots the most-scaled tool at 47% (unverified). 2026: 32% decided against purchasing at least one software product or feature because they could build it in-house with agentic coding tools (confirmed); 44% say AI is scaling across the enterprise, from 38% (unverified); 40% of $1B+ companies scaling agents, from 27%, vs 22% of smaller ones (unverified) | Survey, consultancy |
| Menlo Ventures, "2025 Mid-Year LLM Market Update" (31 Jul 2025; survey of 150 technical leaders) and "2025: The State of Generative AI in the Enterprise" (Dec 2025; survey of 495 US enterprise decision-makers plus spend estimates), via secondary and GitHub mirrors (menlovc.com blocked) | Enterprise genAI spend $37B in 2025 (3.2x from $11.5B), of which about $18B infrastructure and about $19B applications ($8.4B horizontal, $7.3B departmental, $3.5B vertical); coding about $4B (some summaries say $4.2B), about 55% of departmental spend; prior-year coding spend "about $550M" (unverified); enterprise LLM API spend share Anthropic 40% (from 24% in 2024 and 12% in 2023), OpenAI 27% (from 50% in 2023), Google 21%; Anthropic's coding share 42% vs OpenAI 21% at mid-year and 54% vs 21% by December; 76% of AI use cases purchased rather than built (from 53%); only 16% of enterprise deployments and 27% of startup deployments qualify as "true agents", the rest fixed-sequence or routing workflows; 86% of horizontal application spend goes to copilot-style tools rather than agentic ones; "over 50% of developers use AI coding assistants, 65%+ in top-quartile teams" (unverified) | Survey plus spend estimates, VC; Menlo is an Anthropic investor |
| MIT NANDA, "The GenAI Divide: State of AI in Business 2025", Jul-Aug 2025, via secondary and GitHub mirrors | $30-40B invested; 95% of pilots show no measurable P&L impact; 52 structured interviews, 153 senior-leader surveys and 300+ public deployments reviewed, Jan-Jun 2025 (some press summaries cite 150 interviews and 350 employees; the report's own methodology section gives 52, 153 and 300); 80%+ piloted ChatGPT/Copilot-class tools, about 40% report deployment; only 40% of firms have official LLM subscriptions while 90% of workers report daily personal AI use for work ("shadow AI") | Mixed methods, academic-affiliated, contested methodology |
| Microsoft 2026 Work Trend Index, May 2026, primary | Active agents in Microsoft 365 up 15x YoY (18x in large enterprises), measured as the year-over-year change in unique active agents over a rolling 28-day period; an absolute count of 18M active agents (unverified: the report's methodology says it publishes shares and ratios, not absolute counts); only 26% of AI users say leadership is aligned on AI strategy; organizational factors explain 67% of reported AI impact vs 32% individual; 19% of AI users in the "frontier" zone, 16% "stalled" | Telemetry plus survey, vendor |
| Ramp AI Index, Feb-Aug 2026 releases, via secondary and GitHub mirrors of Ramp's text (ramp.com blocked) | Mar 2026 data (Apr release): Anthropic 30.6% of US businesses vs OpenAI 35.2%; Apr 2026 data (13 May release): Anthropic 34.4% (+3.8 points) vs OpenAI 32.3% (-2.9), the first month Anthropic led, with overall business AI adoption at 50.6% and Anthropic up about 4x in twelve months from 7.9%; Jul 2026 data (12 Aug release): Anthropic 43.5% (+1.1), OpenAI 39.7% (+0.2), xAI 4.0% (+0.9); model-serving platforms 6.1% of AI-using businesses; Aug 2026 headline "Cracks in the AI thesis", with spend concentrated in the top 1% of firms; about 79% of Anthropic's business customers also pay OpenAI (Ramp data via Axios, Feb 2026). The panel grew from 50,000+ to 70,000+ businesses between the May and Aug releases, so the levels are not strictly comparable (see notes) | Card and bill-pay spend data, independent |
| Anthropic Economic Index reports: Sep 2025 ("Uneven geographic and enterprise AI adoption", data 4-11 Aug 2025), Jan 2026 ("Economic primitives", data 13-20 Nov 2025), Mar 2026 ("Learning curves", 24 Mar 2026, data 5-12 Feb 2026) and Jun 2026 ("Cadences", 26 Jun 2026, data 10 Apr-10 Jun 2026), report text read via GitHub mirrors (anthropic.com blocked) | Sep 2025: 77% of first-party API conversations automation-style (12% augmentation) versus about 50% on Claude.ai (confirmed); "directive" chat conversations rose from 27% (Dec 2024-Jan 2025) to 39% (Aug 2025), the first report where automation exceeded augmentation on Claude.ai (confirmed); computer and mathematical tasks about 36% of Claude.ai conversations and nearly half of API traffic; the majority of the top 15 API use clusters are coding; 40% of US employees use AI at work, from 20% in 2023. Jan 2026: computer and mathematical tasks a third of Claude.ai conversations and nearly half of API traffic; Claude.ai augmentation 52% / automation 45% in Nov 2025; about 49% of occupations have at least a quarter of their tasks appearing in Claude use; Claude rates personal tasks as successfully completed 78% of the time versus 61% for software development; "coding tasks continue to migrate from augmentative usage in Claude.ai to more automated workflows in first-party API traffic" (quotation not re-verified). Mar 2026: augmentation up slightly on Claude.ai, automation "decreased sharply" in API data; computer and mathematical tasks 35% of Claude.ai conversations; top-10 tasks 19% of traffic, from 24%; users with 6+ months' tenure have about 10% higher success rates; Claude Code traffic appears in the API data as many small task-labelled calls. Jun 2026: 93% of chat and Cowork conversations produce an artifact (explanations 17%, documents and reports 15%, guidance 11%); AI autonomy is higher on Claude Code than on chat or Cowork for 26 of 31 output types, by 0.37 points on a 1-5 scale on average and 0.53 points for scripts and code; personal use rises from about 35% of conversations on weekdays to just under 50% at weekends; a survey of about 9,700 Claude users (May-Jun 2026) found close to 6 in 10 expect AI to handle a larger share of their work next year and over a third expect it to do most or nearly all of their tasks | Telemetry plus survey, vendor-authored |

The consistent picture: broad experimentation, narrow scaling, and a shadow economy where individuals use consumer chat tools far ahead of sanctioned deployments. Spend and paid adoption keep rising through Aug 2026 even as Ramp flags "cracks".

## 6. The three questions, answered directly

### 6.1 What share of agent use runs through chat versus terminals or IDEs?

No dataset measures this directly, so the answer depends on the lens.

| Lens | Chat surfaces | Terminal, IDE, desktop coding agents | Basis |
|---|---|---|---|
| Weekly users | ChatGPT about 1B weekly (Jul-Aug 2026), Gemini app 1B monthly (Aug 2026), plus Meta AI, Claude and Copilot (not sized here) | Codex 8M weekly and 10M combined with ChatGPT Work (Jul 2026); Claude Code low single-digit millions (unverified); Cursor 1M+ paying (2026) | Coding agents are roughly 1-2% of chat-scale audiences |
| Messages by topic | Programming 4.2% of ChatGPT messages; Software Development plus DevOps 14.3% of Claude chat | The rest of coding traffic runs through API, IDE and terminal | Chat carries well over 90% of messages, of which a small fraction is code |
| Autonomy style | Claude chat 48.6% automation (May 2026); consumer ChatGPT 40% "Doing" | API traffic mostly automation (77% in Sep 2025, confirmed); Software Developer-matched chat conversations 60.8% automation; Claude Code shows 0.37 points more autonomy than chat or Cowork for the same output types, 0.53 for code (Jun 2026) | Delegation is common on chat but denser on developer surfaces |
| Enterprise spend | Chatbots the most-scaled enterprise tool (47%, McKinsey) | Coding about 55% of departmental AI spend; $4B in 2025 | Dollars follow the developer surfaces |

Best estimate: by people and messages, chat surfaces carry the overwhelming majority (above 90%) of what the public would call "using an AI agent"; by autonomous multi-step execution and by revenue, terminals, IDEs and cloud coding sessions carry the majority. Both statements are true at once, and the thesis needs to say which one it is about. The Jun 2026 Economic Index adds the first within-vendor measurement of the second half: the same output types are produced with more delegated autonomy on Claude Code than on chat or Cowork, with the same models behind both.

### 6.2 Where is agentic use growing fastest?

1. **Coding agents.** Codex from about 3M weekly users in Apr 2026 to 8M in mid-Jul and 10M combined with ChatGPT Work by 21 Jul, after OpenAI put usage at 6x its January level in June (via secondary); Claude Code run-rate revenue more than doubling in the first six weeks of 2026 (via secondary); Copilot's coding agent authoring a million pull requests in its first five months (Octoverse, Oct 2025); enterprise coding spend up about 7x in 2025 (Menlo).
2. **Platform-embedded agents.** Microsoft 365 active agents up 15x YoY (primary), though many are simple. Custom GPTs and Projects now carry about 20% of ChatGPT Enterprise traffic (via secondary).
3. **API automation.** Token consumption per OpenAI enterprise customer up 320x YoY (via secondary); Anthropic reports coding migrating from chat to automated API workflows (via secondary).
4. **Chat apps adding agent modes** (Claude Cowork and Dispatch, Codex inside ChatGPT, "ChatGPT Work"): the first usage figures arrived in mid-2026. Knowledge workers were about 20% of Codex's 5M weekly users in Jun 2026 and growing more than 3x faster than developers, and OpenAI said Codex plus ChatGPT Work usage "nearly doubled" in the three weeks after the 9 Jul launch, to 10M users (via secondary). Anthropic has published no Cowork or Dispatch usage figures; the Jun 2026 Economic Index reports chat and Cowork together. This is now the second-fastest-growing channel after coding agents themselves, and it is growing inside the chat app.

Where it is not growing fast: scaled enterprise agents per function (about 10% or less), "true agents" as a share of enterprise deployments (16%; 86% of horizontal application spend still goes to copilot-style tools), and measured productivity in controlled settings, where the only independent instrument (METR) stopped producing reliable estimates in 2026.

### 6.3 Does the data support "CLI and desktop apps are the default"?

- For **developers using capable coding agents**, yes for the CLI in mid-2025 to early 2026: Claude Code launched terminal-first and its docs still call the CLI the most complete surface. But the vendors have since built five to ten alternative surfaces on the same engine, and Codex's growth spike coincided with its move into the ChatGPT app and desktop app. Among developers overall, agents are used daily by about one in seven (Stack Overflow, 2025; 2026 results pending).
- For **everyone else**, no. Chat apps (ChatGPT, Copilot, Claude chat and Cowork), Slack, and platform-embedded agents are the default, and the Economic Index shows the chat surface is three-quarters non-computing work.
- The **desktop app** is not marginal. Both leading labs now ship a desktop app as the "graphical interface to the same engine", with local file access, computer use and phone-initiated sessions. If anything, the desktop app is the vendors' bet for the non-terminal default. One caveat from the Jun 2026 Economic Index: the same outputs are produced with less delegated autonomy in the Chat and Cowork tabs than in Claude Code, so the surfaces share an engine but not yet a level of autonomy.

## What this means for the thesis

**Supports**

- "The CLI cannot be the default." Confirmed by scale (about 1B weekly ChatGPT users against roughly 10M for the largest coding agent in Aug 2026; coding agents are 1-2% of chat audiences), by developer surveys (agents are a minority tool even among developers), and by vendor behavior (every terminal agent has grown a web, desktop, mobile and chat surface within a year, and Codex now lives inside ChatGPT).
- "The harness must be reimagined for non-technical people." The chat surface is 76% non-computing tasks and about half automation-style already; enterprise pilots fail on integration and workflow fit (MIT NANDA) rather than on model capability; organizational factors explain twice the impact of individual behavior (Microsoft). The bottleneck is around the model, not inside it.
- "The harness is the bottleneck for developers too." METR's mid-2025 slowdown with a strong model in a mainstream IDE harness, followed by probable but unmeasurable speedups in 2026 (METR's design broke because developers refused to work without AI), is the cleanest evidence that harness plus workflow, not raw model quality, sets the productivity outcome. This is one experiment with wide intervals, so it is suggestive, not decisive. The Jun 2026 Economic Index points the same way from the vendor side: the same models produce the same output types with 0.37 points more delegated autonomy on Claude Code than on chat, a difference in harness (files, execution, tools), not in model.

**Contradicts**

- "Nor a desktop app." The two largest agent vendors are converging on desktop apps as the non-terminal home for agents, and the Codex desktop merge coincided with its fastest growth: 5M to 10M combined users between the 9 Jul launch and 21 Jul 2026 (via secondary). The thesis needs an argument for why desktop convergence fails, not an assumption.
- "Reimagine the default." For most people the default already exists and is growing fast: the chat app. Non-developer agent use is arriving as agent modes inside chat and productivity suites (Cowork, Codex inside ChatGPT, ChatGPT Work, Copilot agents), not as a new category of product; OpenAI's 2026 numbers make this concrete, with knowledge workers the fastest-growing Codex cohort and coding-shaped messages up 36% among non-technical enterprise workers. A startup would be competing with the incumbents' own surface strategy.

**Nuance**

- Agentic growth is real but concentrated: coding first, then platform-embedded agents, with scaled enterprise agents still rare. Any harness bet should expect coding to remain the beachhead through 2027.
- The measurement base is thin and vendor-authored. Independent, controlled evidence exists only for developers (METR), it is noisy, and in 2026 METR itself declared its estimates unreliable; the largest "independent" datasets (Ramp, Stack Overflow) measure spend and self-report, not autonomy.
- Surface-share answers as of Sep 2026: by people and messages, chat surfaces carry well over 90% of agent-style use (about 1B weekly ChatGPT users versus about 10M for the largest coding agent); by delegated autonomy, developer surfaces lead (77% automation-style in API traffic, 60.8% among Software Developer-matched chat conversations, and 0.37 points more autonomy on Claude Code than on chat). Autonomous use is growing fastest in coding agents and, since mid-2026, in agent modes inside the chat apps (Codex inside ChatGPT, ChatGPT Work), with knowledge workers the fastest-growing cohort; scaled enterprise agents remain rare.

## Open questions and unverified claims

- **Sep 2025 Economic Index API figures** (77% automation on API; directive chat conversations 27% to 39%) are now confirmed against GitHub mirrors of the report text (2026-09-08).
- **Codex merging into the ChatGPT desktop app on 9 Jul 2026, "GPT-5.6", and "ChatGPT Work"** are consistent across many secondary digests and mirrors, and the 10M combined-user figure is attributed to OpenAI's 21 Jul 2026 small-business announcement; none could be checked against openai.com directly. The Aug 2026 "20M active agent users" figure is unverified.
- **Claude Code absolute user counts** (4.2M vs 2M weekly) conflict across aggregator sites; only the statements attributed to Anthropic's 12 Feb 2026 announcement (run-rate above $2.5B, more than doubled since 1 Jan, business subscriptions quadrupled) are quoted with confidence. The doubling refers to revenue, not users.
- **METR 2026 sign convention.** Resolved: the "-18% (CI -38% to +9%)" figure is an 18% reduction in completion time for the 10 returning developers; the 47 new developers show -4% (CI -15% to +9%). METR calls both unreliable because of selection.
- **McKinsey editions.** Resolved in part: the 2025 figures come from the Nov 2025 report (1,993 respondents) and the 32% build-in-house figure from "The state of AI in 2026: On the road to ROI" (25 Aug 2026; 1,719 respondents). The 44% and 40%-vs-22% figures attributed to the 2026 edition remain unverified.
- **Stack Overflow 2026 results** had not been published as of 19 Aug 2026; the "doubled" claim in the launch post compares against a May 2026 agents pulse survey, not the 2025 Developer Survey.
- **Consumer scale of Claude and Meta AI** was not sized; Gemini's 1B monthly users (Google, Aug 2026, via secondary) are monthly, not weekly, so the chat-versus-terminal ratio still rests mainly on ChatGPT and is conservative.
- **Ramp level comparability.** The Apr 2026 (34.4%) and Jul 2026 (43.5%) Anthropic shares imply a 9-point rise in three months although the Aug release reports only +1.1 points for July; the panel also grew from 50,000+ to 70,000+ businesses. The Jun and Jul releases were not read, so the trajectory is directionally reliable but the levels may not be comparable (unverified).
- **Microsoft's "18M active agents"** count could not be found in the Work Trend Index, whose methodology says it reports ratios only (unverified).
- **No source measures surface share directly.** Section 6.1 is triangulation, not measurement.

## Sources

1. Anthropic Economic Index, MCP dataset tools (overview, top work tasks, occupation usage, global usage, countries), accessed 2026-09-08; data period May 2026. https://www.anthropic.com/economic-index
2. Anthropic Economic Index dataset README (release history), GitHub mirror of the Hugging Face dataset, Mar 2026 snapshot. https://github.com/tkc88888888/EconomicIndex
3. Anthropic, "Economic Index report: Uneven geographic and enterprise AI adoption", Sep 2025 (blocked; via secondary). https://www.anthropic.com/research/anthropic-economic-index-september-2025-report
4. Anthropic, "Economic Index report: Economic primitives", Jan 2026 (blocked; via secondary). https://www.anthropic.com/research/anthropic-economic-index-january-2026-report
5. Anthropic, "Economic Index report: Learning curves", Mar 2026 (blocked; via secondary). https://www.anthropic.com/research/economic-index-march-2026-report
6. Anthropic, "Economic Index report: Cadences", Jun 2026 (blocked; via secondary). https://www.anthropic.com/research/economic-index-june-2026-report
7. Secondary summaries of the 2026 Economic Index reports: CFA UK Technology Community, Jan 2026, https://connect.cfauk.org/discussion/the-anthropic-economic-index-report-first-for-2026 ; DC the Median, Jan 2026, https://dcthemedian.substack.com/p/7-key-findings-from-the-new-anthropic ; Thirty3 Labs, Jun 2026, https://www.thirty3labs.co.uk/news/anthropic-economic-index-report-cadences-june-2026 ; explainx.ai, Jun 2026, https://www.explainx.ai/blog/anthropic-economic-index-cadences-june-2026
8. Chatterji, Cunningham, Deming, Hitzig, Ong, Shan, Wadman, "How People Use ChatGPT", NBER Working Paper 34255, Sep 2025 (blocked; via secondary). https://www.nber.org/papers/w34255
9. Secondary summaries of the ChatGPT paper: Baobab Tech, Sep 2025, https://baobabtech.ai/posts/nber-paper-how-people-use-chatgpt ; invuai, Sep 2025, https://www.invuai.com/how-people-use-chatgpt-chatterji-2025-nber-paper/ ; The AI Enterprise, Sep 2025, https://www.theaienterprise.io/p/how-people-actually-use-ai-nber-study-2025
10. hanfang, "chatgpt-usage-taxonomies" (taxonomies from the NBER paper), GitHub, Sep 2025. https://github.com/hanfang/chatgpt-usage-taxonomies
11. OpenAI, "The state of enterprise AI", Dec 2025 (blocked; via secondary). https://openai.com/index/the-state-of-enterprise-ai-2025-report/ ; summaries: VKTR, https://www.vktr.com/ai-disruption/the-8-biggest-takeaways-from-the-openai-state-of-enterprise-ai-report/ ; LeadDev, https://leaddev.com/ai/openai-report-enterprise-ai-is-still-in-the-early-innings
12. ChatGPT 900M weekly users, Feb 2026 (via secondary): DemandSage, https://www.demandsage.com/chatgpt-statistics/ ; ALM Corp, https://almcorp.com/blog/chatgpt-900-million-weekly-active-users/
13. Microsoft, "2026 Work Trend Index: Agents, human agency, and the opportunity for every organization", May 2026 (primary, fetched). https://www.microsoft.com/en-us/worklab/work-trend-index/agents-human-agency-and-the-opportunity-for-every-organization
14. Microsoft, "How Frontier Firms are rebuilding the operating model for the age of AI", May 2026 (blocked; via secondary). https://blogs.microsoft.com/blog/2026/05/05/how-frontier-firms-are-rebuilding-the-operating-model-for-the-age-of-ai/
15. McKinsey, "The state of AI" global survey, Nov 2025 and 2026 editions (blocked; via secondary). https://www.mckinsey.com/capabilities/quantumblack/our-insights/the-state-of-ai ; Forbes, Mar 2026, https://www.forbes.com/sites/josipamajic/2026/03/22/10-of-enterprise-functions-use-ai-agents-mckinsey-finds/ ; CX Today, https://www.cxtoday.com/ai-automation-in-cx/mckinseys-state-of-ai-the-scaling-gap-is-now-cxs-problem/
16. MIT NANDA, "The GenAI Divide: State of AI in Business 2025", Jul 2025 (via secondary): Legal.io, Aug 2025, https://www.legal.io/blog/5719519/MIT-Report-Finds-95-of-AI-Pilots-Fail-to-Deliver-ROI-Exposing-GenAI-Divide ; Virtualization Review, Aug 2025, https://virtualizationreview.com/articles/2025/08/19/mit-report-finds-most-ai-business-investments-fail-reveals-genai-divide.aspx
17. Stack Overflow, 2025 Developer Survey, Jul 2025 (blocked; via secondary). https://survey.stackoverflow.co/2025/ ; The New Stack, https://thenewstack.io/23-of-devs-regularly-use-ai-agents-per-stack-overflow-survey/ ; ADTmag, Jan 2026, https://adtmag.com/blogs/watersworks/2026/01/stack-overflow-survey.aspx
18. Stack Overflow, "The 2026 Developer Survey is now open", Jun 2026 (blocked; via secondary). https://stackoverflow.blog/2026/06/23/the-2026-developer-survey-is-now-open-for-human-developers-only/
19. Google Cloud, "Announcing the 2025 DORA Report", Sep 2025 (primary, fetched). https://cloud.google.com/blog/products/ai-machine-learning/announcing-the-2025-dora-report ; report page https://dora.dev/dora-report-2025/ ; median hours via Scrum.org summary, https://www.scrum.org/resources/blog/dora-report-2025-summary-state-ai-assisted-software-development
20. METR, "Measuring the Impact of Early-2025 AI on Experienced Open-Source Developer Productivity", Jul 2025; GitHub repository README fetched. https://github.com/METR/Measuring-Early-2025-AI-on-Exp-OSS-Devs ; blog https://metr.org/blog/2025-07-10-early-2025-ai-experienced-os-dev-study/ ; arXiv 2507.09089
21. METR, "We are changing our developer productivity experiment design", Feb 2026 (blocked; via secondary). https://metr.org/blog/2026-02-24-uplift-update/ ; Rob Bowley, Apr 2026, https://blog.robbowley.net/2026/04/04/metrs-developer-productivity-research-2026-update/
22. Anthropic, "How AI is transforming work at Anthropic", Dec 2025 (blocked; via secondary). https://www.anthropic.com/research/how-ai-is-transforming-work-at-anthropic ; Fortune, Dec 2025, https://fortune.com/2025/12/02/how-anthropics-safety-first-approach-won-over-big-business-and-how-its-own-engineers-are-using-its-claude-ai
23. Ramp AI Index, May 2026 and Aug 2026 (blocked; via secondary). https://ramp.com/data/ai-index-may-2026 ; https://ramp.com/data/ai-index-august-2026 ; Econlab summary, https://econlab.substack.com/p/ai-index-august-2026 ; MindStudio, https://www.mindstudio.ai/blog/anthropic-vs-openai-business-adoption-2026-ramp-data-2
24. Menlo Ventures, "2025 Mid-Year LLM Market Update", Jul 2025 (blocked; via secondary). https://menlovc.com/perspective/2025-mid-year-llm-market-update/ ; BigDATAwire, https://www.hpcwire.com/bigdatawire/this-just-in/menlo-ventures-report-enterprise-llm-spend-reaches-8-4b-as-anthropic-overtakes-openai/
25. Menlo Ventures, "2025: The State of Generative AI in the Enterprise", Dec 2025 (blocked; via secondary). https://menlovc.com/perspective/2025-the-state-of-generative-ai-in-the-enterprise/ ; GlobeNewswire release, https://www.globenewswire.com/news-release/2025/12/09/3202258/0/en/Menlo-Ventures-2025-State-of-Generative-AI-Report-Enterprise-Investment-Hit-37B-in-2025-Tripling-in-One-Year.html
26. GitHub, "Octoverse 2025", Oct 2025 (blocked; via secondary). https://github.blog/news-insights/octoverse/octoverse-a-new-developer-joins-github-every-second-as-ai-leads-typescript-to-1/ ; Visual Studio Magazine, https://visualstudiomagazine.com/articles/2025/10/31/typescript-tops-github-octoverse-as-ai-era-reshapes-language-choices.aspx ; Forbes, https://www.forbes.com/sites/janakirammsv/2025/11/01/10-key-takeaways-from-github-octoverse-2025-report/
27. Claude Code documentation: Overview, Platforms and integrations, Desktop, Slack (primary, fetched Sep 2026). https://code.claude.com/docs/en/overview ; https://code.claude.com/docs/en/platforms ; https://code.claude.com/docs/en/desktop ; https://code.claude.com/docs/en/slack
28. Codex user figures, Apr-Jul 2026 (via secondary): The New Stack, https://thenewstack.io/gpt-5-6-codex-user-surge/ ; Unite.AI, https://www.unite.ai/openai-says-codex-and-chatgpt-work-hit-10-million-users/ ; Constellation Research, https://www.constellationr.com/insights/news/openai-touts-broadening-codex-usage-5-million-weekly-active-users
29. Claude Code and Cursor figures, 2026 (via secondary, low reliability): DemandSage, https://www.demandsage.com/claude-ai-statistics/ ; Uvik, https://uvik.net/blog/claude-code-vs-cursor-vs-copilot-vs-codex-2026/
30. Anthropic, "Economic Index report: Learning curves", 24 Mar 2026 (blocked; report text read via a GitHub mirror of anthropic.com). https://www.anthropic.com/research/economic-index-march-2026-report
31. Anthropic, "Economic Index report: Cadences", 26 Jun 2026 (blocked; report text read via GitHub mirrors, e.g. https://github.com/dkkyjy/simon-daily and https://github.com/ai-native-engineer/anthropic-mirror ). https://www.anthropic.com/research/economic-index-june-2026-report
32. Anthropic, Series G announcement, 12 Feb 2026 (blocked; quoted via GitHub mirrors): Claude Code run-rate revenue over $2.5B, more than doubled since the beginning of 2026.
33. METR, "Measuring-Late-2025-AI-on-OSS-Devs" data repository (fetched 2026-09-08). https://github.com/METR/Measuring-Late-2025-AI-on-OSS-Devs ; METR, "Measuring the Self-Reported Impact of Early-2026 AI on Technical Worker Productivity", 11 May 2026 (blocked; via secondary). https://metr.org/blog/2026-05-11-ai-usage-survey/
34. DORA and Google Cloud, "The ROI of AI-assisted Software Development", Apr 2026, v2026.1 (blocked; via GitHub mirrors of summaries). https://dora.dev/ai/roi/report/ ; https://cloud.google.com/resources/content/dora-roi-of-ai-assisted-software-development ; InfoQ, May 2026, https://www.infoq.com/news/2026/05/dora-roi-ai-assisted-dev-report/
35. OpenAI, "Codex is becoming a productivity tool for everyone", 2 Jun 2026 (blocked; via secondary). https://openai.com/index/codex-for-knowledge-work/ ; OpenAI, "Codex for every role, tool, and workflow", 2 Jun 2026. https://openai.com/index/codex-for-every-role-tool-workflow/
36. OpenAI, "Introducing the ChatGPT for small business program", 21 Jul 2026 (blocked; via secondary): 10M combined ChatGPT Work and Codex users. https://openai.com/index/introducing-chatgpt-small-business-program/ ; 9to5Mac, 21 Jul 2026, https://9to5mac.com/2026/07/21/openai-launches-small-business-program-as-it-touts-10m-chatgpt-work-and-codex-users/
37. OpenAI Economic Research, "How AI is expanding what people do at work" (Work at the Frontier, report 1), 27 Jul 2026 (blocked; post text via GitHub mirror). https://openai.com/index/how-ai-is-expanding-what-people-do-at-work/
38. ChatGPT 900M weekly users, 27 Feb 2026: Search Engine Land, https://searchengineland.com/chatgpt-900-million-weekly-active-users-470492 ; TechCrunch, https://techcrunch.com/2026/02/27/chatgpt-reaches-900m-weekly-active-users ; ChatGPT 1B weekly users, 3-7 Aug 2026 (via secondary digests citing TechCrunch, TechSpot, heise and The Verge).
39. McKinsey, "The state of AI in 2026: On the road to ROI", 25 Aug 2026 (blocked; via secondary, e.g. The Register, 25 Aug 2026). https://www.mckinsey.com/capabilities/quantumblack/our-insights/the-state-of-ai
40. Ramp AI Index, Mar 2026 (Anthropic 30.6% vs OpenAI 35.2%) and the 79% overlap figure: Axios, 11 Feb 2026, https://www.axios.com/2026/02/11/openai-anthropic-chatgpt-claude-subscriptions ; TechCrunch, 13 May 2026, https://techcrunch.com/2026/05/13/anthropic-now-has-more-business-customers-than-openai-according-to-ramp-data/
41. SpaceX acquisition of Anysphere (Cursor), announced 16 Jun 2026, closed 14 Aug 2026 (via secondary): Reuters, https://www.reuters.com/legal/transactional/spacex-buy-anysphere-60-billion-2026-06-16/ ; TechCrunch, https://techcrunch.com/2026/06/16/spacex-to-acquire-cursor-for-60b-in-stock-days-after-blockbuster-ipo/
42. Google, Gemini app 1B monthly users, 11 Aug 2026 (via secondary). Announcement not fetched.
43. GitHub mirrors of primary texts used for verification (all fetched 2026-09-08): NBER paper text, https://github.com/artur-shlyapnikov/hn-distill ; Anthropic Economic Index Sep 2025 and Jan 2026 report text, https://github.com/thevibeworks/claude-code-docs and https://github.com/vishalsachdev/aei ; OpenAI enterprise report post, https://github.com/windwild/news-articles ; OpenAI Work at the Frontier post, https://github.com/lin-mouren/ai-agent-tech-blog ; METR 2026 summaries, https://github.com/venturemavenwill/high-velocity-engineering and https://github.com/mohsseha/effective_claude_code

## Verification notes (2026-09-08)

Method: the Economic Index figures were re-read from the MCP tools (`econ_index_get_dataset_overview`, `econ_index_list_top_work_tasks`, `econ_index_get_occupation_usage`, `econ_index_get_global_usage`, `econ_index_list_countries`, `econ_index_get_usage_by_country`). Other claims were checked with 17 web searches (the session's search budget then ran out), direct fetches of the reachable primaries (microsoft.com, cloud.google.com, code.claude.com, raw.githubusercontent.com), and GitHub code search over mirrored copies of primary texts and independent digests. "Confirmed" means the figure matched a primary text or at least two independent secondary sources; "corrected" means the document's number or attribution was changed; "unverified" means no reachable source could confirm it and the claim is now marked inline.

| # | Claim in the document | Status | Evidence |
|---|---|---|---|
| 1 | Economic Index headline splits: 51.4% augmentation / 48.6% automation; 43.4% work / 40.2% personal / 16.5% coursework; 121 countries, 51 states, 22 categories, 718 of 923 occupations | confirmed | MCP tools, May 2026 period, snapshot 2026-06-24 |
| 2 | Job-category shares (Computer and Mathematical 23.8% ... Legal 1.0%); Software Developers 0.35%, rank 70 of 718, 60.8% automation | confirmed | MCP tools |
| 3 | Request topics (Content Creation 22.7%, Software Development 11.5%, DevOps 2.8% ...), artifacts (explanation 16.7%, document 14.9%, app or website 4.2%), task-time 5 hours vs 40 minutes | confirmed | MCP tools; Knowledge Retrieval & Enterprise Search 3.6% added |
| 4 | Top work tasks (search electronic sources 4.95% ... Develop Web sites 0.52%) | confirmed | MCP tools |
| 5 | Geography: Australia 6.4, Singapore 5.8, Switzerland 5.0, Luxembourg 4.9, New Zealand 4.8, Canada 4.1, US 3.87 (rank 12), UK 3.35, Germany 2.4, Japan 1.9, Brazil 0.96, India 0.3, Nigeria 0.24, Tanzania 0.07; US 41/50/9; Brazil 57% work; automation US 49.6%, India 51.9%, Norway 41.3% | confirmed | MCP tools |
| 6 | ChatGPT 900M weekly users, Feb 2026 (from 400M a year earlier) | confirmed | Announced 27 Feb 2026 with the $110B round; multiple outlets |
| 7 | NBER: 700M weekly users, 18B messages/week, non-work 73%, Asking 49 / Doing 40 / Expressing 11, programming 4.2%, top three topics about 78%, work messages 56% Doing, three quarters writing | confirmed | Paper text via GitHub mirrors; paper says "more than 70%" and "nearly 80%" |
| 8 | NBER sample "1.5M sampled consumer conversations to Jul 2025" | corrected | About 1M messages (May 2024-Jun 2025) plus a 1.58M-message conversation-level sample |
| 9 | OpenAI enterprise report: 8x messages, 320x tokens, 19x Custom GPTs and Projects at about 20% of traffic, 40-60 minutes/day, 75% speed or quality | confirmed | Post text via GitHub mirror; survey of 9,000 workers at almost 100 enterprises added |
| 10 | Microsoft 2026 WTI: 105,000 chats, 20,000 workers in 10 countries, 49/19/17/15, 86%, 66%, 26%, 67% vs 32%, 19% frontier / 16% stalled, agents 15x (18x large enterprises) | confirmed | Primary page fetched |
| 11 | Microsoft "18M active agents in a 28-day window" | unverified | Not on the primary page; methodology says ratios only, rolling 28-day window |
| 12 | Codex 3M weekly (Altman, Apr 2026), 5M, 7M on 13 Jul, 8M days later; "fewer than 1M in Feb 2026" | confirmed / corrected | 3M on 8 Apr, 5M on 2 Jun ("6x since January"), 7-8M mid-Jul confirmed via mirrors; the "under 1M in Feb" claim replaced by the implied January level |
| 13 | Codex merged into the ChatGPT desktop app on 9 Jul 2026 with ChatGPT Work; usage up "2.5x in a week" | corrected | Merge date and ChatGPT Work confirmed across digests; "2.5x" replaced by OpenAI's "nearly doubled" to 10M combined users by 21 Jul |
| 14 | Claude Code "weekly active users doubled since 1 Jan 2026"; run-rate above $2.5B | corrected | Series G announcement (12 Feb 2026): run-rate revenue over $2.5B, more than doubled since 1 Jan; business subscriptions quadrupled; the doubling was revenue, not users |
| 15 | Claude Code absolute weekly users (4.2M vs 2M); "10x in three months" after launch | unverified | Aggregator sites only, blocked |
| 16 | Cursor about 2M users, over 1M paying | confirmed / unverified | 1M+ paying and $2B ARR (Feb-Apr 2026) confirmed by two secondaries; 2M MAU single source; SpaceX acquisition added |
| 17 | GitHub Octoverse 2025: 80% of new developers use Copilot in week one, 1M+ coding-agent PRs May-Sep 2025, 180M+ developers | confirmed | Multiple search snippets ("nearly 80%") |
| 18 | Claude Code awareness 31% to 57%, adoption 3% to 18% | unverified | uvik.net blocked; unnamed survey |
| 19 | Stack Overflow 2025: 84% use or plan; 51% of professionals daily; trust 33% vs distrust 46%; 66% "almost right"; agents 14.1% daily; 38% no plans | confirmed | Multiple independent secondaries and mirrors; 23% "regularly", 13.8% copilot-only and the 60% favorable figure were not individually re-checked |
| 20 | Stack Overflow 2026: launch post says agent usage "doubled year over year"; results not found | corrected | The comparison is against a 27 May 2026 agents pulse survey; results unpublished as of 19 Aug 2026 |
| 21 | DORA 2025: about 5,000 respondents, 90% use AI, 80%+ productivity, 30% little or no trust | confirmed | Google Cloud announcement fetched ("nearly 5,000") |
| 22 | DORA 2025 median about 2 hours/day; +14 points YoY | unverified | One weak secondary for the median; the YoY delta not found |
| 23 | METR Jul 2025: 16 developers, 246 tasks, 18.8% slower, forecast 24% faster, believed 20% faster | confirmed | Repository README (18.8%); METR reports 19%, CI +2% to +39% |
| 24 | METR Feb 2026: 57 developers, 143 repos, 800+ tasks; -18% (CI -38% to +9%); METR says speedup "likely" | confirmed / corrected | Figures confirmed; the -18% applies only to the 10 returning developers, the 47 new ones show -4% (CI -15% to +9%), and METR calls the data "very weak evidence" because 30-50% of developers refused AI-off tasks; added |
| 25 | Anthropic internal study: 132 surveyed, 53 interviews, 27% new work, 0-20% fully delegable | confirmed | Multiple mirrors and digests |
| 26 | McKinsey 2025: 23% scaling agents, 62% experimenting, no function above about 10% | confirmed | Multiple secondaries; 1,993 respondents, 105 countries, fielded 25 Jun-29 Jul 2025 |
| 27 | McKinsey: chatbots the most-scaled tool at 47% | unverified | Not found |
| 28 | McKinsey "2026 web edition": 44% scaling (from 38%), 40% of $1B+ firms scaling agents (from 27%) vs 22%, 32% skipped buying software | corrected / unverified | Edition identified as "The state of AI in 2026: On the road to ROI" (25 Aug 2026; 1,719 respondents); 32% confirmed; 44% and 40% vs 22% unverified |
| 29 | Menlo: $37B from $11.5B; $19B applications; coding $4B, about 55% of departmental spend; Anthropic 40% / OpenAI 27% / Google 21%; Anthropic 42% of code generation (mid-2025); 16% true agents | confirmed | GitHub mirrors of press coverage and Menlo text; 54% coding share by Dec 2025 and 27% startup true-agent share added |
| 30 | Menlo: sample "150 technical leaders" | corrected | 150 applies to the mid-year update; the Dec 2025 report surveyed 495 US decision-makers |
| 31 | Menlo: coding "from about $550M"; "over 50% of developers use AI coding assistants, 65%+ in top-quartile teams" | unverified | Not found in any mirror |
| 32 | MIT NANDA: $30-40B, 95% no P&L impact, 52 interviews, 153 surveys, 300 deployments, 40% subscriptions vs 90% shadow use | confirmed | Multiple mirrors; press variants (150 interviews, 350 employees) noted |
| 33 | Ramp May 2026: Anthropic 34.4% vs OpenAI 32.3%, first lead | confirmed | Ramp text (13 May 2026 release, April data) via mirrors and TechCrunch; 50.6% overall adoption added |
| 34 | Ramp Aug 2026: Anthropic 43.5% (+1.1), OpenAI 39.7% (+0.2), model-serving platforms 6.1%, "Cracks in the AI thesis" | confirmed | Ramp text (12 Aug 2026 release, July data) via mirror |
| 35 | Ramp: about 79% of Anthropic's customers also pay OpenAI | confirmed / corrected | Ramp data via Axios, 11 Feb 2026; dated accordingly |
| 36 | Comparability of Ramp levels between releases | unverified | Panel grew from 50,000+ to 70,000+ businesses; Jun and Jul releases not read |
| 37 | AEI Sep 2025: 77% of API conversations automation-style; directive 27% to 39% | confirmed | Report text via GitHub mirrors (previously marked unverified) |
| 38 | AEI: computer and mathematical tasks a third of Claude.ai and nearly half of API; majority of top-15 API clusters coding; 49% of occupations with a quarter of tasks | confirmed | Jan 2026 and Sep 2025 report text via mirrors |
| 39 | AEI Jan 2026 quotation on coding migrating to automated API workflows | unverified | Exact wording not found in the mirror read; consistent with the report's findings |
| 40 | AEI 2026 report titles: Mar 2026 "Learning curves", Jun 2026 "Cadences" | confirmed | Mirrors of anthropic.com pages, dated 24 Mar and 26 Jun 2026 |
| 41 | Claude Code surfaces table (CLI, Desktop, VS Code, JetBrains, web, mobile, Slack, Claude Tag, channels, CI) and quoted descriptions | confirmed | code.claude.com docs fetched 2026-09-08; Chrome integration and Routines present |
| 42 | Codex "about 20M active agent users" (Aug 2026); "15M combined" | unverified | Single-source digests; window unspecified |

Counts (each row counted once, by the first status shown): 27 confirmed, 6 corrected, 9 unverified, 42 claims checked. Rows 12, 24 and 35 also carry corrections (the Codex February level, the METR cohort split, the dating of the Ramp overlap figure), and rows 16 and 28 also carry unverified sub-figures (Cursor's 2M monthly users; McKinsey's 44% and 40% vs 22%).

### Refresh notes

Added (all dated, all marked by source type):

- Anthropic Economic Index Jun 2026 "Cadences" (26 Jun 2026; data 10 Apr-10 Jun 2026): 93% of chat and Cowork conversations produce an artifact; AI autonomy higher on Claude Code than on chat or Cowork for 26 of 31 output types (0.37 points average, 0.53 for code); personal use 35% weekdays to just under 50% weekends; survey of about 9,700 users. Also Mar 2026 "Learning curves" (24 Mar 2026): augmentation up slightly, API automation down, 10% higher success for 6-month-plus users, Claude Code traffic visible in API data. Both via GitHub mirrors of the report text.
- Sep 2025 and Jan 2026 Economic Index figures upgraded from "unverified" to confirmed (77% API automation; directive 27% to 39%; 49% of occupations; Nov 2025 Claude.ai split 52/45; task success 78% vs 61%).
- ChatGPT about 1B weekly users (OpenAI spokesperson to The Verge, Jul 2026; announcement reported 3-7 Aug 2026); Gemini app 1B monthly users (Google, 11 Aug 2026). Both via secondary.
- OpenAI 2026 disclosures: Codex more than 5M weekly users on 2 Jun 2026, 6x since January, knowledge workers about 20% and growing 3x faster than developers, non-developer users up 137x since Aug 2025; Codex inside ChatGPT with role plugins (2 Jun); ChatGPT Work and the desktop merge (9 Jul); 10M combined users (21 Jul); "How AI is expanding what people do at work" (27 Jul 2026): 16.8% of work messages and 43.5% of occupation-specific messages cross occupational lines.
- DORA and Google Cloud "The ROI of AI-assisted Software Development" (Apr 2026, v2026.1): J-curve, 39% ROI and 8-month payback in the worked example; the 2026 State of AI-assisted Software Development report not yet released.
- METR 11 May 2026 self-report survey (349 workers, median 1.4-2x self-reported, flagged by METR as likely overstated) and the full Feb 2026 breakdown (returning -18%, new -4%, 30-50% task refusal).
- McKinsey "The state of AI in 2026: On the road to ROI" (25 Aug 2026; 1,719 respondents): 32% built software in-house with agentic coding tools instead of buying.
- Ramp trajectory: Mar 2026 data (30.6% vs 35.2%), Apr 2026 data (34.4% vs 32.3%, overall adoption 50.6%), Jul 2026 data (43.5% vs 39.7%, xAI 4.0%); the 79% overlap figure dated to Feb 2026.
- Menlo Dec 2025 detail: 495-respondent survey, 54% coding share, 27% startup true agents, 76% purchased vs built, 86% of horizontal spend on copilots, application-layer split.
- Cursor: $2B ARR (Feb 2026), 1M+ paying; SpaceX acquisition for about $60B (announced 16 Jun, closed 14 Aug 2026).
- Stack Overflow 2026: results unpublished as of 19 Aug 2026; the "doubled" agent-usage claim reframed.
- Section 2: a compact task-category and occupation-category table cited to the Economic Index tools, plus the Knowledge Retrieval & Enterprise Search topic (3.6%).
- Claude Code surfaces re-verified against code.claude.com (Chrome integration, Routines, iMessage channel added to the TL;DR).

Sources that could not be opened (network proxy): anthropic.com and assets.anthropic.com, openai.com and cdn.openai.com, nber.org, ssrn.com, metr.org and metr.substack.com, arxiv.org, mckinsey.com, menlovc.com, ramp.com, survey.stackoverflow.co and stackoverflow.blog, dora.dev, huggingface.co, mlq.ai, blogs.microsoft.com, forbes.com, techcrunch.com, venturebeat.com, qz.com, finance.yahoo.com, infoq.com, thenewstack.io, unite.ai, constellationr.com, gradually.ai, demandsage.com, uvik.net, scrum.org, virtualizationreview.com, sify.com, dataconomy.com, techbriefly.com, toolhunt.io, thirty3labs.co.uk, explainx.ai, connect.cfauk.org, treycausey.com, blog.robbowley.net, philippdubach.com, paddo.dev, letsdatascience.com, baobabtech.ai, aiinstitute.hbs.edu, searchengineland.com, leaddev.com, pymnts.com, skillademia.com, rogerwong.me, cxtoday.com, colabsoftware.com, semanticos.io, digitalapplied.com, byteiota.com, mindstudio.ai, cryptobriefing.com, techjacksolutions.com, getpanto.ai, technologychecker.io, ingenire.com, makemeacto.cc, jacob.blog and all Substack domains. Reachable: microsoft.com (Work Trend Index), cloud.google.com (DORA 2025 announcement), code.claude.com (Claude Code docs), github.com and raw.githubusercontent.com (METR repositories, the Economic Index dataset mirror, and mirrors of the NBER paper, the Economic Index reports, the OpenAI posts and many digests). The web search budget was exhausted after 17 searches, so most 2026 items rest on GitHub-hosted mirrors and digests rather than on the outlets themselves; where a claim rests on a single such source it is marked unverified.

Remaining doubts:

- Ramp's Apr-to-Jul 2026 jump for Anthropic (34.4% to 43.5%) is larger than the reported monthly changes explain; a panel or methodology change is likely and the Jun and Jul releases were not read.
- Microsoft's "18M active agents" and Copilot user counts could not be sourced to the Work Trend Index; the report gives ratios only.
- The Codex user series mixes windows: weekly users (Apr-Jul), "combined users" for Codex plus ChatGPT Work (Jul), and "active agent users" with no window (Aug). Only the weekly figures are comparable with each other.
- McKinsey's 2026 agent-scaling percentages (44%, 40% vs 22%) and Menlo's developer-adoption percentages remain unsourced.
- The METR 2026 numbers come from mirrors and third-party summaries of the blog post and repository, not from the post itself; the returning-cohort -18% and new-cohort -4% figures agree across four independent summaries.
- Claude Code has no published user count from Anthropic; every absolute figure in circulation is an aggregator estimate.
- The Jun 2026 Economic Index compares autonomy across Claude Code, chat and Cowork by output type but does not publish surface shares, so Section 6.1 remains triangulation.
- Reported 2026 events that were not checked against a primary and are not used in the analysis: Anthropic's "Fable 5" model release and pricing, OpenAI's GPT-5.6 family, and Anthropic revenue figures beyond Claude Code.
