# Adoption evidence: who uses agents, for what, and through which surfaces

*Research program: agent harnesses. Written 2026-09-08. Status: draft, pending verification.*

## What this document answers

- Who uses AI agents today, for which tasks, and how that splits between developers and everyone else.
- What share of agent use runs through chat surfaces (web, mobile, Slack, Copilot) versus terminals and IDEs, as far as public data allows.
- Where autonomous ("agentic") use is growing fastest, and where it is stalling.
- What the data says about the assumption that a CLI or a desktop app is the default way people operate agents.

## TL;DR

- **By people and messages, chat wins by two orders of magnitude.** ChatGPT reported 900M weekly users (Feb 2026, via secondary). The largest terminal-born coding agent, OpenAI Codex, reported 8M weekly users (Jul 2026, via secondary). Programming was 4.2% of ChatGPT messages in mid-2025.
- **By money and by autonomy, developer surfaces win.** Coding took roughly 55% of departmental enterprise AI spend in 2025 (Menlo Ventures, Dec 2025, via secondary). Business API traffic is mostly automation-style; consumer chat is roughly half augmentation.
- **The Anthropic Economic Index (Claude chat and Cowork feed, May 2026 period)** shows 23.8% of conversations matched to Computer and Mathematical tasks; the other 76% spread across media, education, sales, administration, management and finance. Automation-style conversations are 48.6% overall and 60.8% among conversations matched to Software Developer tasks.
- **Agents are still a minority tool even among developers.** 90% of software professionals use AI at work (DORA, Sep 2025), but only 14% used agents daily and 38% had no plans to (Stack Overflow, Jul 2025, via secondary). Enterprise agent scaling sits at 23% of organizations and no more than 10% in any single business function (McKinsey, via secondary).
- **Fastest growth is in coding agents and platform-embedded agents.** Codex went from under 1M to 8M weekly users in five months; the Copilot coding agent authored 1M+ pull requests in five months; enterprise coding spend rose about sevenfold in a year; active agents in Microsoft 365 grew 15x year over year (all via secondary except Microsoft, which is primary).
- **Vendors are dismantling the CLI-as-default assumption themselves.** Claude Code ships on the terminal, two IDEs, a desktop app, the web, iOS and Android, Slack, Telegram/Discord channels and CI (primary docs). Codex was folded into the ChatGPT desktop app (Jul 2026, via secondary). The direction is "one engine, many surfaces", converging on the desktop app and the chat app, not the terminal.
- **Contradiction for the thesis:** the desktop app is exactly where the two leading labs are converging their agent products. The evidence supports "the CLI is not the default" and "the harness matters"; it does not support "a desktop app cannot be the default".

## 1. How to read adoption numbers

Analogy: measuring traffic on a road. You can count cars at a toll booth (vendor telemetry: what actually flowed through one company's product), ask people how they commute (surveys: what they say they do), or read fuel receipts (spend data: what companies paid for). Each instrument answers a different question, and none covers the whole road. This document uses all three and labels which is which.

Three definitions used below:

- **Augmentation vs automation** (Anthropic's classifier): augmentation means the person stays actively involved in the task (iterating, learning, validating); automation means the person directs Claude to complete the task. These are conversation styles, not job outcomes.
- **"Agent"** means different things in different surveys. In Microsoft's data an "active agent" can be a small Copilot Studio Q&A bot; in Stack Overflow's survey it means a tool that plans and executes multi-step coding work. Treat cross-survey comparisons as rough.
- **Surface** is harness part 6 from the program definitions: CLI, IDE, desktop app, web, chat app, voice, OS.

## 2. Anthropic Economic Index: what the chat surface is used for

Source: Anthropic Economic Index MCP tools, accessed 2026-09-08 (data period 2026-05-01, snapshot dated 2026-06-24; landing page https://www.anthropic.com/economic-index). Important scope note: this dataset covers the `claude_ai` feed only, meaning Claude chat and Cowork conversations. It does not include the API or Claude Code, so it describes the chat surface, not the terminal. Numbers match conversation content to O*NET job tasks; they say nothing about who the users are.

**Headline splits (worldwide, May 2026 period)**

| Measure | Value |
|---|---|
| Augmentation / automation | 51.4% / 48.6% |
| Work / personal / coursework | 43.4% / 40.2% / 16.5% |
| Classifier-estimated task time | about 5 hours alone vs about 40 minutes with Claude (order-of-magnitude estimate, not measured) |
| Coverage | 121 countries, 51 US states/DC, 22 job categories, 718 of 923 tracked occupations published |

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
| Chatterji et al., "How People Use ChatGPT", NBER w34255, Sep 2025 (via secondary; nber.org blocked) | 1.5M sampled consumer conversations to Jul 2025 | 700M weekly users, 18B messages/week (Jul 2025); non-work 73% (up from 53% in Jun 2024); Asking 49% / Doing 40% / Expressing 11%; programming 4.2% of messages; Practical Guidance + Seeking Information + Writing about 78%; of work messages 56% are Doing and about three quarters of those are writing | Telemetry, vendor-authored |
| OpenAI announcement, Feb 2026 (via secondary) | ChatGPT reach | 900M weekly active users (from 400M a year earlier) | Vendor claim |
| OpenAI, "The state of enterprise AI", Dec 2025 (via secondary; openai.com blocked) | ChatGPT Enterprise and API customers | Weekly enterprise messages up about 8x YoY; API tokens per organization up 320x; Custom GPTs and Projects up 19x, about 20% of enterprise ChatGPT traffic; workers report saving 40-60 minutes/day; 75% say AI improves speed or quality | Telemetry plus survey, vendor-authored |
| Microsoft 2026 Work Trend Index, 5 May 2026 (primary, fetched) | 105,000 M365 Copilot chats (Feb 2026); survey of 20,000 knowledge workers in 10 countries (18 Feb-7 Apr 2026) | 49% of chats support cognitive work, 19% working with people, 17% producing work, 15% finding information; 86% treat AI output as a starting point; 66% of AI users report more time on high-value work | Telemetry plus survey, vendor-authored |

Two points matter for the thesis. First, "Doing" (asking the model to produce output) is 40% of consumer ChatGPT messages and 56% of work messages: the chat surface already carries delegation, not just questions. Second, the ChatGPT paper and the Economic Index agree that coding is a small minority of chat use (4.2% of ChatGPT messages; 11.5-14.3% of Claude chat conversations, with Claude's user base skewing more technical).

## 4. Developer surfaces: terminal, IDE, desktop, web

### 4.1 Scale of the coding agents

| Product | Figure | Date | Source and reliability |
|---|---|---|---|
| ChatGPT (for scale) | 900M weekly users | Feb 2026 | OpenAI, via secondary |
| OpenAI Codex (CLI, IDE, cloud, desktop) | about 3M weekly users (Altman, Apr 2026); 5M; 7M on 13 Jul 2026; 8M days later after Codex merged into the ChatGPT desktop app; "fewer than 1M in Feb 2026" | Apr-Jul 2026 | Multiple outlets (The New Stack, Unite.AI, Constellation Research); via secondary, unverified against OpenAI |
| Claude Code (CLI-first) | Weekly active users "doubled since 1 Jan 2026"; run-rate revenue above $2.5B (Feb 2026); grew "more than 10x in three months" after May 2025 launch | Feb 2026 | Anthropic statements via aggregator sites; absolute user counts quoted by those sites conflict (4.2M vs 2M), unverified |
| Cursor (IDE) | about 2M users, over 1M paying | Late 2025 / early 2026 | Via secondary, vendor-derived |
| GitHub Copilot | 80% of new GitHub developers use Copilot within their first week; Copilot coding agent authored 1M+ pull requests between May and Sep 2025; 180M+ developers on GitHub | Oct 2025 | Octoverse 2025, via secondary (github.blog blocked) |
| Claude Code awareness/adoption among developers | awareness 31% (Q2 2025) to 57% (Jan 2026); adoption 3% to 18% | Jan 2026 | Unnamed developer survey summarized by uvik.net; unverified |

Even at 8M weekly users, the largest terminal-born agent reaches about 1% of ChatGPT's weekly audience. Against the developer population (180M GitHub accounts, of which a fraction are professional developers), 2-8M weekly users of each coding agent is meaningful but not majority adoption.

### 4.2 What developer surveys and experiments say

| Study | Sample and date | Findings | Type |
|---|---|---|---|
| Stack Overflow Developer Survey 2025 (via secondary; survey site blocked) | 49,009 responses, 166 countries, fielded 29 May-23 Jun 2025 | 84% use or plan to use AI tools; 51% of professional developers use them daily; trust 33% vs distrust 46%; favorable sentiment 60% (from 70%+ in 2024); 66% frustrated by "almost right" answers. Agents: 14.1% use agents daily at work; 23% "regularly"; 13.8% use AI only in copilot/autocomplete mode; 37.9% have no plans to use agents | Survey, independent |
| Stack Overflow 2026 survey | Opened 23 Jun 2026; launch post says agent usage has doubled year over year (via secondary) | Results not found in this session; pages claiming "2026 results" appear to recycle 2025 numbers | Survey, pending |
| DORA 2025, "State of AI-assisted Software Development", 23 Sep 2025 (primary announcement fetched) | about 5,000 technology professionals, 100+ hours of interviews | 90% use AI at work (+14 points YoY); median about 2 hours/day (via secondary); more than 80% believe AI raised their productivity; 30% report little or no trust in AI-generated code; "AI amplifies what's already there" | Survey, Google-sponsored |
| METR, Jul 2025 (GitHub repository README fetched; metr.org and arXiv blocked) | 16 experienced open-source developers, 246 tasks, Cursor Pro with Claude 3.5/3.7 Sonnet | Developers were 18.8% slower with AI allowed, while forecasting 24% faster beforehand and believing 20% faster afterwards | Randomized experiment, independent |
| METR update, 24 Feb 2026 (via secondary) | 57 developers, 143 repos, 800+ tasks across the extended study | METR says it is "likely" developers are now sped up; the re-measured subset shows an estimated change of about -18% in completion time (CI roughly -38% to +9%), i.e. probably faster but not statistically distinguishable from zero; METR is redesigning the experiment | Experiment, independent |
| Anthropic internal study, Dec 2025 (via secondary; anthropic.com blocked) | 132 engineers and researchers surveyed Aug 2025, 53 interviews, Claude Code logs | 27% of Claude-assisted work would not otherwise have been done; staff say they can fully delegate 0-20% of their work; engineers become more "full-stack" | Survey plus telemetry, vendor-authored |

Pattern: near-universal use of AI assistance, minority use of agents, a large perception-versus-measurement gap, and productivity effects that were negative in mid-2025 and are probably positive but noisy in 2026.

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
| Channels | push events from Telegram, Discord, iMessage or webhooks into a session |
| GitHub Actions, GitLab CI, Code Review, Routines | headless, scheduled and event-triggered runs |

OpenAI moved in the same direction: a Codex desktop app in Feb 2026 and, on 9 Jul 2026, Codex merged into the redesigned ChatGPT desktop app alongside a "ChatGPT Work" agentic mode for knowledge workers, after which combined usage reportedly rose 2.5x in a week (via secondary, several outlets, unverified against OpenAI). Read together: the engine is being made surface-agnostic, the terminal keeps the deepest feature set, and the desktop and chat apps are where the labs are steering non-terminal users.

## 5. Enterprises: experiments, scaling, and shadow use

| Source | Key numbers | Type |
|---|---|---|
| McKinsey, State of AI (Nov 2025 edition; 2026 web edition), via secondary (mckinsey.com blocked) | 23% of respondents scaling an agentic system somewhere; 62% experimenting (2025); no more than 10% scaling agents in any single function; chatbots are the most-scaled tool at 47%. 2026 edition: 44% say AI is scaling across the enterprise (from 38%); 40% of $1B+ companies scaling agents (from 27%) vs 22% of smaller ones; 32% skipped buying a software product because agentic coding tools let them build it in-house | Survey, consultancy |
| Menlo Ventures, mid-2025 and Dec 2025 reports, via secondary (menlovc.com blocked) | Enterprise genAI spend $37B in 2025 (3.2x from $11.5B); $19B at the application layer; coding $4B (from about $550M), about 55% of departmental spend; Anthropic 40% of enterprise LLM API share, OpenAI 27%, Google 21%; Anthropic 42% of code generation (mid-2025); only 16% of enterprise deployments are "true agents", the rest fixed-sequence workflows; over 50% of developers use AI coding assistants, 65%+ in top-quartile teams | Survey of 150 technical leaders plus spend estimates, VC |
| MIT NANDA, "The GenAI Divide", Jul-Aug 2025, via secondary | $30-40B invested; 95% of pilots show no measurable P&L impact; 52 interviews, 153 leader surveys, 300 deployments; 80%+ piloted ChatGPT/Copilot-class tools, about 40% report deployment; only 40% of firms have official LLM subscriptions while 90% of workers report daily personal AI use for work ("shadow AI") | Mixed methods, academic-affiliated, contested methodology |
| Microsoft 2026 Work Trend Index, May 2026, primary | Active agents in Microsoft 365 up 15x YoY (18x in large enterprises), 18M active agents in a 28-day window; only 26% of AI users say leadership is aligned on AI strategy; organizational factors explain 67% of reported AI impact vs 32% individual; 19% of workers in the "frontier" zone, 16% "stalled" | Telemetry plus survey, vendor |
| Ramp AI Index, May and Aug 2026, via secondary (ramp.com blocked) | May 2026: Anthropic 34.4% of US businesses paying vs OpenAI 32.3%, the first month Anthropic led; Aug 2026: Anthropic 43.5% (+1.1 points), OpenAI 39.7% (+0.2); about 79% of Anthropic's customers also pay OpenAI; model-serving platforms 6.1% of AI-using businesses; Aug 2026 headline "Cracks in the AI thesis" | Card and bill-pay spend data, independent |
| Anthropic Economic Index, Sep 2025 and Jan 2026 reports, via secondary | Computer and mathematical tasks are about a third of Claude.ai conversations and nearly half of first-party API traffic; the majority of the top 15 API use clusters (about half of API traffic) are coding; "coding tasks continue to migrate from augmentative usage in Claude.ai to more automated workflows in first-party API traffic"; about 49% of occupations have at least a quarter of their tasks appearing in Claude use. Unverified in this session: the Sep 2025 figure that 77% of API conversations were automation-style, and that fully "directive" chat conversations rose from 27% to 39% between Dec 2024 and Aug 2025 | Telemetry, vendor-authored |

The consistent picture: broad experimentation, narrow scaling, and a shadow economy where individuals use consumer chat tools far ahead of sanctioned deployments. Spend and paid adoption keep rising through Aug 2026 even as Ramp flags "cracks".

## 6. The three questions, answered directly

### 6.1 What share of agent use runs through chat versus terminals or IDEs?

No dataset measures this directly, so the answer depends on the lens.

| Lens | Chat surfaces | Terminal, IDE, desktop coding agents | Basis |
|---|---|---|---|
| Weekly users | ChatGPT 900M (Feb 2026) plus Gemini, Meta AI, Claude, Copilot (not sized here) | Codex 8M, Claude Code low single-digit millions, Cursor about 2M (2026) | Coding agents are roughly 1-2% of chat-scale audiences |
| Messages by topic | Programming 4.2% of ChatGPT messages; Software Development plus DevOps 14.3% of Claude chat | The rest of coding traffic runs through API, IDE and terminal | Chat carries well over 90% of messages, of which a small fraction is code |
| Autonomy style | Claude chat 48.6% automation (May 2026); consumer ChatGPT 40% "Doing" | API traffic mostly automation (77% in Sep 2025, unverified); Software Developer-matched chat conversations 60.8% automation | Delegation is common on chat but denser on developer surfaces |
| Enterprise spend | Chatbots the most-scaled enterprise tool (47%, McKinsey) | Coding about 55% of departmental AI spend; $4B in 2025 | Dollars follow the developer surfaces |

Best estimate: by people and messages, chat surfaces carry the overwhelming majority (above 90%) of what the public would call "using an AI agent"; by autonomous multi-step execution and by revenue, terminals, IDEs and cloud coding sessions carry the majority. Both statements are true at once, and the thesis needs to say which one it is about.

### 6.2 Where is agentic use growing fastest?

1. **Coding agents.** Codex from under 1M to 8M weekly users between Feb and Jul 2026 (via secondary); Claude Code weekly users doubling in six weeks at the start of 2026 (via secondary); Copilot's coding agent authoring a million pull requests in its first five months (Octoverse, Oct 2025); enterprise coding spend up about 7x in 2025 (Menlo).
2. **Platform-embedded agents.** Microsoft 365 active agents up 15x YoY (primary), though many are simple. Custom GPTs and Projects now carry about 20% of ChatGPT Enterprise traffic (via secondary).
3. **API automation.** Token consumption per OpenAI enterprise customer up 320x YoY (via secondary); Anthropic reports coding migrating from chat to automated API workflows (via secondary).
4. **Chat apps adding agent modes** (Claude Cowork and Dispatch, "ChatGPT Work"): launched 2026, no usage figures published. Unverified whether they are growing.

Where it is not growing fast: scaled enterprise agents per function (10% or less), "true agents" as a share of deployments (16%), and measured productivity in controlled settings.

### 6.3 Does the data support "CLI and desktop apps are the default"?

- For **developers using capable coding agents**, yes for the CLI in mid-2025 to early 2026: Claude Code launched terminal-first and its docs still call the CLI the most complete surface. But the vendors have since built five to ten alternative surfaces on the same engine, and Codex's growth spike coincided with its move into the ChatGPT desktop app. Among developers overall, agents are used daily by about one in seven (Stack Overflow, 2025).
- For **everyone else**, no. Chat apps (ChatGPT, Copilot, Claude chat and Cowork), Slack, and platform-embedded agents are the default, and the Economic Index shows the chat surface is three-quarters non-computing work.
- The **desktop app** is not marginal. Both leading labs now ship a desktop app as the "graphical interface to the same engine", with local file access, computer use and phone-initiated sessions. If anything, the desktop app is the vendors' bet for the non-terminal default.

## What this means for the thesis

**Supports**

- "The CLI cannot be the default." Confirmed by scale (coding agents are 1-2% of chat audiences), by developer surveys (agents are a minority tool even among developers), and by vendor behavior (every terminal agent has grown a web, desktop, mobile and chat surface within a year).
- "The harness must be reimagined for non-technical people." The chat surface is 76% non-computing tasks and about half automation-style already; enterprise pilots fail on integration and workflow fit (MIT NANDA) rather than on model capability; organizational factors explain twice the impact of individual behavior (Microsoft). The bottleneck is around the model, not inside it.
- "The harness is the bottleneck for developers too." METR's mid-2025 slowdown with a strong model in a mainstream IDE harness, followed by probable speedups in 2026 with better tooling, is the cleanest evidence that harness plus workflow, not raw model quality, sets the productivity outcome. This is one experiment with wide intervals, so it is suggestive, not decisive.

**Contradicts**

- "Nor a desktop app." The two largest agent vendors are converging on desktop apps as the non-terminal home for agents, and the Codex desktop merge coincided with its fastest growth (via secondary). The thesis needs an argument for why desktop convergence fails, not an assumption.
- "Reimagine the default." For most people the default already exists and is growing fast: the chat app. Non-developer agent use is arriving as agent modes inside chat and productivity suites (Cowork, ChatGPT Work, Copilot agents), not as a new category of product. A startup would be competing with the incumbents' own surface strategy.

**Nuance**

- Agentic growth is real but concentrated: coding first, then platform-embedded agents, with scaled enterprise agents still rare. Any harness bet should expect coding to remain the beachhead through 2027.
- The measurement base is thin and vendor-authored. Independent, controlled evidence exists only for developers (METR), and it is noisy.

## Open questions and unverified claims

- **Sep 2025 Economic Index API figures** (77% automation on API; directive chat conversations 27% to 39%) come from memory of the report; the primary page and its GitHub mirrors were unreachable. Treat as unverified.
- **Codex merging into the ChatGPT desktop app on 9 Jul 2026, "GPT-5.6", and "ChatGPT Work"** rest on several secondary outlets and could not be checked against OpenAI.
- **Claude Code absolute user counts** (4.2M vs 2M weekly) conflict across aggregator sites; only the relative statements attributed to Anthropic (doubling, run-rate above $2.5B) are quoted with any confidence.
- **METR 2026 sign convention.** The "-18% (CI -38% to +9%)" figure is read here as an 18% reduction in completion time; the primary post was blocked.
- **McKinsey edition ambiguity.** Some figures may belong to the Nov 2025 report and others to the 2026 web edition; sample sizes were not retrievable.
- **Stack Overflow 2026 results** were not located; whether agent use "doubled" is from the survey launch post as summarized by third parties.
- **Consumer scale of Claude, Gemini and Meta AI** was not sized; the chat-versus-terminal ratio therefore rests on ChatGPT alone and is conservative.
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
