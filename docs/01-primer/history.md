# Harness History 2022–2026: From ReAct to Managed Agents

*Research program: agent harnesses. Written 2026-09-08. Status: draft, pending verification.*

## What this document answers

- How the "harness" around a language model evolved from October 2022 to September 2026, with dates.
- Which of the seven harness parts (loop, tools, context/memory, permissions, runtime, surface, orchestration) each step changed, and why it mattered.
- Which big ideas failed (AutoGPT-style autonomy, Rabbit r1, Humane AI Pin, Adept) and what the failures teach.
- What this history supports and contradicts in the program's thesis.

## TL;DR

- "Harness" became a standard term only in late 2025: Anthropic's "Effective harnesses for long-running agents" (Nov 2025) and OpenAI's "Harness engineering" essay (Feb 2026) both say the environment around the model decides whether long tasks succeed. That is a vendor position, but by May–June 2026 independent papers (Harness-Bench and others) report double-digit score swings for the same model under different harnesses.
- Tool use moved from prompt tricks (ReAct, Oct 2022) to an API contract (function calling, Jun 2023) to shared protocols (MCP, Nov 2024; AGENTS.md, Aug 2025; Agent Skills, Oct–Dec 2025) under Linux Foundation governance (Dec 2025). Each step moved work out of the prompt and into the harness.
- Autonomy-first designs failed twice: AutoGPT (2023) looped and burned money; hardware agents (Humane, Rabbit, 2024) shipped before the loop was reliable. What won was the "agent-computer interface" (SWE-agent, 2024) and a permissioned loop in the terminal (Claude Code, Feb 2025).
- 2025 was the year of the CLI (Claude Code, Codex CLI, Gemini CLI). 2026 is the year the CLI is being wrapped: Codex desktop app (Feb), Claude Cowork (Jan; GA Apr; web and mobile Jul), Claude Code Remote Control and Routines (Feb–Apr), Gemini CLI folded into the Antigravity app (May–Jun), Windows pitched as an "agent OS" (Jun). Yet new CLIs keep launching (Meta Muse Code, Aug 2026).
- Non-technical demand is real and security is not solved: OpenClaw went viral through WhatsApp and Telegram in Jan 2026 and within weeks produced a leaked database, hundreds of malicious "skills" and an RCE CVE.
- Who gets to be the harness is now contested: Anthropic blocked third-party harnesses from subscriptions (Apr 4, 2026), then partially reinstated them with capped credits (May 13). SpaceX bought Cursor for $60B (Jun–Aug 2026). DeepSeek and Meta shipped their own harnesses (Aug 2026).
- Net: the history supports "the harness is the bottleneck" and "the surface is being reimagined," but contradicts "a startup can be the default": incumbents are converging on one runtime with many surfaces, and using pricing to protect it.

## Vocabulary in one minute

Analogy: the model is an engine. The harness is the rest of the car: pedals and steering (loop and human steering), attachments (tools), fuel tank and trip log (context and memory), seatbelts and speed limiter (permissions), the road (runtime), the driver's seat (surface), and the dispatcher running a fleet (orchestration).

Precisely: a harness is the program that repeatedly calls the model, executes what the model asks for, feeds results back, decides when to stop or ask a human, and exposes all of that through an interface. Every product below is a different answer to those questions.

```python
# The loop, as almost every harness since 2023 implements it
while True:
    msg = model(context)                     # 1. loop
    if not msg.tool_calls: break             # 1. stopping (or ask the human)
    for call in msg.tool_calls:
        if not policy.allows(call) and not ask_human(call):   # 4. permissions
            continue
        context.append(runtime.run(call))    # 2. tools, 5. runtime, 3. context
# surface = CLI / IDE / desktop / web / chat app / phone; orchestration = many loops
```

## Timeline at a glance

| Date | Event | Harness part changed |
|---|---|---|
| Oct 2022 | ReAct paper | Loop, tools (as text conventions) |
| Oct–Nov 2022 | LangChain, LlamaIndex | Context (retrieval), tools |
| Feb 2023 | Toolformer | Tools trained into the model |
| Mar 2023 | ChatGPT plugins | Tools inside a chat surface |
| Mar–Apr 2023 | AutoGPT, BabyAGI | Loop without humans; task queues; vector memory |
| Jun 2023 | OpenAI function calling | Tools as a JSON contract |
| Nov 2023 | GPTs replace plugins | Surface |
| Jan–Apr 2024 | Rabbit r1, Humane AI Pin ship | Surface (hardware); both failed |
| Mar 2024 | Devin | Runtime (sandbox), async surface |
| Apr–May 2024 | SWE-agent | Tools and context designed for the model |
| Jun 2024 | Adept founders to Amazon | End of the "own action model" bet |
| Oct 2024 | Claude computer use | Tools (any GUI), runtime (VM) |
| Nov 2024 | Model Context Protocol | Tools as a shared protocol |
| Jan–Jul 2025 | Operator, deep research, ChatGPT agent | Cloud browser runtime in a chat app |
| Feb 2025 | Claude Code preview (GA May) | Permissioned terminal loop; CLAUDE.md memory |
| Mar 2025 | Manus | Cloud "computer" for a general audience |
| Apr–May 2025 | Codex CLI, Codex cloud | Parallel cloud sandboxes returning PRs |
| Jun 2025 | Gemini CLI | CLI as a free commodity |
| Aug–Dec 2025 | AGENTS.md, Agent Skills, Agentic AI Foundation | Context conventions; neutral governance |
| Sep–Nov 2025 | Claude Agent SDK, Claude Code on the web, Antigravity, "Effective harnesses" post | Harness as SDK; cloud sessions; agent-manager IDE; multi-window loop |
| Jan 2026 | OpenClaw viral; Claude Cowork preview | Chat-app surface, always-on runtime; Claude Code for non-coders |
| Feb 2026 | Codex app; "Harness engineering"; Remote Control; OpenClaw creator joins OpenAI | Agent command center; phone as window onto local session |
| Apr 2026 | Anthropic blocks third-party harnesses; Managed Agents; Cowork GA; Routines; Meta–Manus unwound | Runtime as a service; fight over who is the harness |
| May–Jun 2026 | Agent SDK credits; Gemini CLI into Antigravity; Agent 365 GA; Windows as agent OS; Devin Desktop; SpaceX–Cursor | Consolidation onto apps and OS control planes |
| Jul–Aug 2026 | Cowork on web and mobile; Muse Code; DeepSeek Harness; Cursor deal closes | Every model lab ships a harness |

2022–2024 dates come from the primary sources listed at the end; 2025–2026 dates were re-verified by web search for this document.

## Era 1 (Oct 2022 – mid 2023): agents as prompts

**ReAct** ([arXiv, Oct 2022](https://arxiv.org/abs/2210.03629)) showed a model could interleave "Thought / Action / Observation" in plain text and beat chain-of-thought on question answering and text games. The harness was a Python loop parsing "Action:" lines; a malformed line broke it. **Toolformer** ([arXiv, Feb 2023](https://arxiv.org/abs/2302.04761)) made the opposite bet: train the model to insert API calls itself. That idea returned later as function calling and computer-use training, but in 2023 the prompt approach won on flexibility.

**LangChain** ([Oct 2022](https://github.com/langchain-ai/langchain)) and **LlamaIndex** ([Nov 2022](https://github.com/run-llama/llama_index)) packaged these patterns as chains, tool agents and retrieval. They were the first general harness libraries; for a year, retrieval-augmented generation was how outside knowledge got into the window.

**ChatGPT plugins** ([OpenAI, Mar 2023](https://openai.com/index/chatgpt-plugins/)) put tools inside a mass-market surface via OpenAPI specs. Usage stayed low; OpenAI replaced them with GPTs ([Nov 2023](https://openai.com/index/introducing-gpts/)) and wound plugins down in spring 2024 (month from OpenAI help-center notices; not re-fetched). Lesson: tools bolted onto a chat loop that cannot plan, retry or verify are a novelty.

**AutoGPT** ([Mar 30, 2023](https://github.com/Significant-Gravitas/AutoGPT)) and **BabyAGI** ([Apr 2023](https://github.com/yoheinakajima/babyagi)) added orchestration and memory: a task queue, self-generated sub-tasks, a vector database, no human in the loop. Both went viral within weeks and both failed the same way: the model looped on one step, lost the goal, could not verify progress, and spent tokens without bound. Their failure is where the "stopping" and "human steering" parts of the loop definition come from.

**Function calling** ([OpenAI, Jun 13, 2023](https://openai.com/index/function-calling-and-other-api-updates/)) is the most important harness change of 2023. Developers declared functions with JSON schemas and the model returned a structured call. Tool-call reliability moved from the harness's parser into the model's training. MCP and Skills both build on this contract.

## Era 2 (2024): the agent-computer interface, and the first products

**Devin** ([Cognition, Mar 12, 2024](https://cognition.ai/blog/introducing-devin)) gave the agent its own computer: a sandboxed shell, editor and browser, driven asynchronously from a chat-like surface, with a claimed 13.86% on a SWE-bench subset (vendor claim; critics questioned the launch demo in Apr 2024, unverified here). Its contribution was runtime and surface: the agent runs elsewhere and reports back.

**SWE-agent** (Princeton; [repo Apr 2024, paper May 2024](https://arxiv.org/abs/2405.15793)) supplied the era's core idea, the "agent-computer interface" (ACI): tools designed for a model rather than a human. A file viewer that shows 100 lines at a time, bounded search results, an edit command that lints before accepting. With GPT-4 Turbo and no model change it reached 12.5% on the full SWE-bench test set. This is the earliest quantified evidence that the same model does better or worse depending on how its tools are shaped.

**Cursor** (Anysphere) turned the IDE into an agent surface: a VS Code fork with inline edits, then agent modes. It grew from about $100M ARR in January 2025 to a $29.3B valuation by November 2025 ([CNBC, Nov 2025](https://www.cnbc.com/2025/11/13/cursor-ai-startup-funding-round-valuation.html)). It is the strongest counter-example to "the IDE is over": for two years it was the largest agent business by revenue.

**Claude computer use** ([Anthropic, Oct 22, 2024](https://www.anthropic.com/news/3-5-models-and-computer-use)) made any GUI a tool: screenshot in, mouse and keyboard out, 14.9% on OSWorld (vendor figure). Tools went from "APIs the developer declared" to "anything a human could do on a screen," with a VM as the natural runtime.

**Model Context Protocol** ([Anthropic, Nov 25, 2024](https://www.anthropic.com/news/model-context-protocol)) standardized how a harness discovers and calls tools, resources and prompts from separate servers. OpenAI adopted it in its Agents SDK in [March 2025](https://openai.github.io/openai-agents-python/mcp/); Google followed in April 2025 (announced on X; not re-fetched). MCP made tools portable across harnesses: exactly what a startup harness needs, and exactly what erodes any one harness's moat.

Anthropic's **"Building effective agents"** ([Dec 2024](https://www.anthropic.com/engineering/building-effective-agents)) set 2025's tone: prefer simple, composable loops; separate predefined workflows from open-ended agents; distrust frameworks that hide the loop.

### What failed, and why

| Product | Bet | What happened | Lesson |
|---|---|---|---|
| Rabbit r1 | $199 device with a "large action model" that operates apps ([CES, Jan 2024](https://www.techradar.com/phones/rabbit-r1s-ai-companion-is-the-ces-gadget-i-want-to-hate-but-may-end-up-loving)) | Shipped Apr 2024; "a $199 AI toy that fails at almost everything" ([Engadget, May 2024](https://www.engadget.com/rabbit-r1-review-a-199-ai-toy-that-fails-at-almost-everything-161043050.html)); improved by updates, never a default | A new surface cannot carry an unreliable loop; the phone already owned the surface |
| Humane AI Pin | Screenless wearable as primary surface | ~10,000 sold vs 100,000 target; HP bought assets for $116M, servers off Feb 28, 2025 ([TechCrunch, Feb 2025](https://techcrunch.com/2025/02/18/humanes-ai-pin-is-dead-as-hp-buys-startups-assets-for-116m)) | Same, plus latency and battery: runtime limits killed the surface |
| Adept | Train an "action model" (ACT-1, 2022) and sell enterprise agents; ~$415M raised | Amazon hired the founders and licensed the tech, Jun 28, 2024 ([CNBC](https://www.cnbc.com/2024/06/28/amazon-hires-execs-from-ai-startup-adept-and-licenses-its-technology.html); [Adept](https://www.adept.ai/blog/adept-update/)) | Competing with frontier labs on the model lost; the harness layer was where a startup could still compete |
| ChatGPT plugins | Tools inside chat | Retired spring 2024 | Tools need a capable loop and a runtime, not a chat window |
| AutoGPT-style autonomy | No human in the loop | Looping, cost, no verification | Stopping rules, verification and steering are part of the harness |

## Era 3 (2025): the terminal wins, standards form

**Operator** ([OpenAI, Jan 2025](https://openai.com/index/introducing-operator/)) and deep research (Feb 2025) put a cloud browser VM behind ChatGPT and merged into **ChatGPT agent** ([Jul 17, 2025](https://openai.com/index/introducing-chatgpt-agent/)): chat app as surface, cloud VM as runtime. Good for research and shopping tasks; not how people operate agents on their own files.

**Claude Code** (preview [Feb 24, 2025](https://www.anthropic.com/news/claude-3-7-sonnet); GA [May 22, 2025](https://www.anthropic.com/news/claude-4)) is the pivotal product. A terminal agent with a per-tool permission prompt, a `CLAUDE.md` project memory, hooks, subagents and an MCP client. It proved that loop, permissions and filesystem in the terminal beat the IDE sidebar for agentic work; every later CLI copied its shape. Anthropic then sold the harness itself as the **Claude Agent SDK** ([Sep 2025](https://www.anthropic.com/engineering/building-agents-with-the-claude-agent-sdk)) and moved it to the cloud with **Claude Code on the web** ([Oct 2025](https://www.anthropic.com/news/claude-code-on-the-web)): parallel sessions in cloud sandboxes, started from a browser or phone.

**Codex** went cloud-first. OpenAI open-sourced **Codex CLI** with o3 and o4-mini ([Apr 16, 2025](https://openai.com/index/introducing-o3-and-o4-mini/); [repo](https://github.com/openai/codex)) and launched **Codex cloud** ([May 16, 2025](https://openai.com/index/introducing-codex/)): many tasks in parallel, each in an isolated sandbox, each returning a pull request. The developer's machine stopped being where the agent runs. **Gemini CLI** ([Google, Jun 25, 2025](https://blog.google/technology/developers/introducing-gemini-cli-open-source-ai-agent/)), open source with a large free tier, made the CLI a commodity four months after Claude Code's preview.

**Manus** (launched Mar 2025) gave a general audience a cloud computer with visible steps, reached about $100M ARR by December 2025, and was acquired by Meta for more than $2B on Dec 30, 2025 ([CNBC](https://www.cnbc.com/2025/12/30/meta-acquires-singapore-ai-agent-firm-manus-china-butterfly-effect-monicai.html)). On Apr 27, 2026 China's foreign-investment security review ordered the completed deal unwound ([CNBC, Apr 2026](https://www.cnbc.com/2026/04/28/china-meta-manus-ai-deal.html); [O'Melveny](https://www.omm.com/insights/alerts-publications/china-unwinds-meta-s-acquisition-of-manus-implications-for-cross-border-ai-transactions/)). Manus is the best evidence that non-technical users pay for a general agent, and that a harness startup's exit can be undone by policy.

**Cognition bought Windsurf** on Jul 14, 2025, days after Google paid a reported $2.4B to license its technology and hire its leaders ([summary](https://www.itechguides.com/cognition-maker-of-the-ai-coding-agent-devin-acquires-windsurf/)); Windsurf became **Devin Desktop** on Jun 2, 2026 ([secondary](https://www.digitalapplied.com/blog/windsurf-becomes-devin-desktop-ide-migration-2026)). An agent company decided it needed an IDE.

Three conventions turned context into a shared standard. **AGENTS.md** ([OpenAI, Aug 2025](https://agents.md/)) is a README for agents, in more than 60,000 repositories by December 2025. **Agent Skills** ([Anthropic, Oct 16, 2025](https://www.anthropic.com/news/skills)) are folders with a `SKILL.md` and optional scripts that the harness loads only when relevant; they became an open standard at [agentskills.io](https://agentskills.io) on Dec 18, 2025 ([SiliconANGLE](https://siliconangle.com/2025/12/18/anthropic-makes-agent-skills-open-standard/)), with about 40 products supporting them by mid-2026 (secondary count). On Dec 9, 2025 the Linux Foundation formed the **Agentic AI Foundation** to hold MCP, AGENTS.md and Block's goose, with AWS, Anthropic, Google, Microsoft and OpenAI as platinum members ([Linux Foundation](https://www.linuxfoundation.org/press/linux-foundation-announces-the-formation-of-the-agentic-ai-foundation); [MCP blog](https://blog.modelcontextprotocol.io/posts/2025-12-09-mcp-joins-agentic-ai-foundation/)). The tools and context layers are now neutral ground.

**Google Antigravity** ([Nov 18, 2025](https://antigravity.google/blog/introducing-google-antigravity)), built by the ex-Windsurf team, was the first "agent-first IDE": a manager view over several agents, browser control, model-agnostic.

**"Effective harnesses for long-running agents"** ([Anthropic, Nov 26, 2025](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)) named the field. An initializer agent sets up a repo, an init script, a feature list and a progress log; a coding agent then does one feature per session, runs end-to-end tests, commits, and updates the log, so the next session recovers from files rather than from the context window. The loop now spans many windows and memory lives on disk.

## Era 4 (Jan – Sep 2026): the harness becomes the product

**OpenClaw.** Peter Steinberger's open-source personal agent, first released as Clawdbot in late 2025 (exact date unverified), runs on the user's machine, talks through WhatsApp, Telegram, Discord and iMessage, keeps memory across conversations, wakes itself on a "heartbeat" or cron schedule, and loads community skills. After Anthropic trademark complaints it became Moltbot (Jan 27, 2026) then OpenClaw (Jan 30), with about 68,000 GitHub stars by Feb 2 ([CNBC](https://www.cnbc.com/2026/02/02/openclaw-open-source-ai-agent-rise-controversy-clawdbot-moltbot-moltbook.html)). Then the security bill arrived: Moltbook, a social network for OpenClaw agents, exposed a database with about 1.5 million API tokens and 35,000 emails; researchers found 341 of 2,857 ClawHub skills malicious ([The Hacker News, Feb 2026](https://thehackernews.com/2026/02/openclaw-integrates-virustotal-scanning.html); [Infosecurity](https://www.infosecurity-magazine.com/news/malicious-crypto-trading-skills/)); a gateway flaw became CVE-2026-25253 ([secondary](https://adversa.ai/blog/openclaw-security-101-vulnerabilities-hardening-2026/)); The Register called it a "dumpster fire" ([Feb 3](https://www.theregister.com/2026/02/03/openclaw_security_problems/)). On Feb 15 Steinberger joined OpenAI and the project moved to a foundation ([TechCrunch](https://techcrunch.com/2026/02/15/openclaw-creator-peter-steinberger-joins-openai/); [his post](https://steipete.me/posts/2026/openclaw)). Surface: the chat apps people already use. Runtime: always-on. Orchestration: self-triggered. It is the clearest demand signal for a non-developer harness in this history, and the clearest demonstration that permissions and blast radius are the unsolved part.

**Claude Cowork.** A research preview for Max subscribers on the macOS app on Jan 12, 2026 ([Simon Willison](https://simonwillison.net/2026/Jan/12/claude-cowork/); [VentureBeat](https://venturebeat.com/technology/anthropic-launches-cowork-a-claude-desktop-agent-that-works-in-your-files-no)): Claude Code's runtime with a folder picker and a UI for non-coders, built, Anthropic said, in about a week and a half with Claude Code (vendor claim). GA on macOS and Windows for all paid plans on Apr 9, 2026 with enterprise controls ([TestingCatalog](https://www.testingcatalog.com/anthropic-launches-claude-cowork-in-general-availability/)); web and mobile on Jul 7, 2026 ([TechCrunch](https://techcrunch.com/2026/07/07/the-coding-agent-wars-are-spilling-into-the-rest-of-the-office-claude-cowork/)). A lab explicitly testing the thesis's second half: same harness, new surface, non-technical audience.

**Codex desktop app.** Launched for macOS on Feb 2, 2026 ([VentureBeat](https://venturebeat.com/orchestration/openai-launches-a-codex-desktop-app-for-macos-to-run-multiple-ai-coding); [OpenAI](https://openai.com/index/introducing-the-codex-app/)) as a "command center" for many agents in parallel git worktrees, with automations and skills; Windows followed in March, and OpenAI began positioning Codex as a daily work agent connected to Slack, Drive and email ([The Deep View, 2026](https://www.thedeepview.com/articles/openai-turns-codex-into-a-daily-work-agent)). The CLI is still there; the default view is a manager of agents.

**"Harness engineering."** OpenAI's essay ([Feb 11, 2026](https://openai.com/index/harness-engineering/); [InfoQ summary](https://www.infoq.com/news/2026/02/openai-harness-engineering-codex/)) reports five months, roughly one million lines of code, about 1,500 merged PRs, three engineers, no hand-written code (vendor claim). The techniques are all harness: AGENTS.md as a map to a docs directory that is the agent's memory, custom linters that encode taste, observability the agent can read, and "garbage collection" of stale docs. Two labs agreeing within three months is not independent evidence, but it shows where the labs' own effort goes.

**Claude Code's 2026 changes** show a CLI turning into a runtime with many surfaces. Remote Control ([Feb 25, 2026](https://www.helpnetsecurity.com/2026/02/25/anthropic-remote-control-claude-code-feature/)) lets a phone or browser drive a session on your own machine. Routines ([docs, Apr 13–17, 2026](https://code.claude.com/docs/en/whats-new/2026-w16)) are "templated cloud agents that fire on a schedule, a GitHub event, or an API call"; the same week added mobile push notifications, an "auto mode" that removes permission prompts on Opus 4.7, a `/usage` breakdown and native binaries. Loop, permissions, runtime, surface and orchestration all moved in one quarter.

**Who is allowed to be the harness.** On Apr 4, 2026 Anthropic stopped letting Pro and Max subscriptions be consumed through third-party harnesses such as OpenClaw, citing cost: its own tools are engineered for prompt-cache hits and outside loops are not ([TNW](https://thenextweb.com/news/anthropic-openclaw-claude-subscription-ban-cost); [Hacker News](https://news.ycombinator.com/item?id=47633396)). On May 13 it partially reversed course with fixed monthly "Agent SDK" credits for programmatic use, billed at API rates and expiring monthly ([VentureBeat](https://venturebeat.com/technology/anthropic-reinstates-openclaw-and-third-party-agent-usage-on-claude-subscriptions-with-a-catch)). Days after the block, Anthropic launched **Claude Managed Agents** (Apr 8–9, 2026; sources differ by a day): hosted sessions with sandboxing, persistence, tracing and a multi-agent orchestration preview, priced at API token rates plus $0.08 per session-hour, with Notion, Rakuten and Sentry as launch customers ([TechRadar](https://www.techradar.com/pro/go-from-prototype-to-launch-in-days-rather-than-months-anthropic-reveals-claude-managed-agents-promises-to-make-agent-building-10x-faster); [AlternativeTo](https://alternativeto.net/news/2026/4/anthropic-launches-claude-managed-agents-to-accelerate-ai-agent-development-and-deployment)). Read together: restrict the cheap path for outside harnesses, sell the runtime as a service.

**Platform consolidation.** Microsoft made Agent 365, a control plane that gives agents identities and governs them with its existing security stack, generally available on May 1, 2026 at $15 per user ([secondary](https://nerdleveltech.com/microsoft-agent-365-ga-ai-agent-control-plane)), and at Build (Jun 2, 2026) pitched Windows as an OS for agents with a Windows Agent Runtime and "Windows 365 for Agents" cloud PCs ([Visual Studio Magazine](https://visualstudiomagazine.com/articles/2026/06/02/at-build-2026-microsoft-sets-up-windows-as-an-os-for-ai-agents.aspx)). Google launched Antigravity 2.0 as a desktop app at I/O on May 19, 2026 and folded Gemini CLI into Antigravity CLI, with the old CLI ceasing to serve consumer tiers on Jun 18 ([Google Developers Blog](https://developers.googleblog.com/an-important-update-transitioning-gemini-cli-to-antigravity-cli/); [The Register](https://www.theregister.com/ai-ml/2026/05/20/bye-bye-gemini-cli-google-nudges-devs-toward-antigravity/5243605)). SpaceX announced an all-stock $60B acquisition of Cursor on Jun 16, 2026 and closed it on Aug 14 ([CBS News](https://www.cbsnews.com/news/spacex-cursor-60-billion-ai-acquisition/); [SatNews](https://satnews.com/2026/08/13/spacex-finalizes-regulatory-procedures-to-close-60-billion-acquisition-of-ai-platform-cursor/)).

**Every lab ships a harness.** Meta released **Muse Code** in beta on Aug 5, 2026: a terminal agent with persistent sub-agents in parallel git worktrees and no app ([9to5Mac](https://9to5mac.com/2026/08/05/meta-launches-muse-code-ai-coding-agent-for-macos-and-linux/); [Meta on X](https://x.com/AIatMeta/status/2085084709277565213)); a secondary source says it left beta on Sep 1 with a workflows engine and SDK (unverified). DeepSeek released **DeepSeek Harness v0.1** on Aug 13, 2026 under MIT: "everything is a plugin," with models, tools, skills, sessions, sandboxes, loops, scheduling and the UI all swappable, plus a web UI and headless mode ([GitHub](https://github.com/deepseek-ai/deepseek-harness); [DeepSeek](https://deepseek.com/harness/en/)). A model lab open-sourcing a harness says the harness is now distribution for the model.

**Evidence that the harness moves outcomes** has caught up with the essays. Harness-Bench ([arXiv, May 2026](https://arxiv.org/html/2605.27922v1)) and "Stop Comparing LLM Agents Without Disclosing the Harness" ([arXiv, May 2026](https://arxiv.org/pdf/2605.23950)) argue a benchmark score is a property of model plus harness and report double-digit swings for the same model; a June 2026 survey frames agent design as harness design ([arXiv](https://arxiv.org/pdf/2606.20683)). Widely repeated but secondary: the same Claude Opus model at 93% on Terminal-Bench 2.0 inside Cursor versus 77% inside Claude Code ([blog, Apr 2026](https://codex.danielvaughan.com/2026/04/19/the-harness-effect-same-model-different-tool-different-score/); unverified against leaderboards), and Opus 4.5 at 45.9% on SWE-bench Pro under a standard scaffold versus 55.4% under Claude Code (secondary). Treat the direction as established and the exact figures as unverified.

## The five shifts

1. **From prompt tricks to contracts.** Tool use went from parsed text (2022) to a trained JSON contract (2023) to shared protocols and file conventions (MCP 2024; AGENTS.md and Skills 2025) under neutral governance (Dec 2025). Each step moved work from the prompt into the harness and made harness parts more interchangeable.
2. **From autonomy to interface.** The ambitious loops (AutoGPT, hardware agents) failed; the winners designed the agent's environment (SWE-agent's ACI, Claude Code's permissioned terminal). Reliability came from shaping what the model sees and may do, not from letting it run.
3. **From chat window to computer.** The unit of context moved from a conversation to a filesystem, a shell and a browser. The terminal won 2025 because it was where the computer already was.
4. **From session to service.** Codex cloud (May 2025), Claude Code on the web (Oct 2025), Routines and Managed Agents (Apr 2026), OpenClaw's heartbeat: the harness now owns durability, scheduling and background execution. The runtime stopped being the user's laptop by default.
5. **From developer tool to operator, and the fight over economics.** Cowork, the Codex app, OpenClaw and Antigravity 2.0 widened the audience in the first half of 2026. In the same months the labs pulled the harness in-house, restricted subsidized third-party use, sold runtime by the hour, and folded CLIs into apps. The harness became the product, so the labs decided to own it.

## What this means for the thesis

**Supports.**
- "Harness is the bottleneck" is the stated position of Anthropic (Nov 2025) and OpenAI (Feb 2026), and by mid-2026 has independent benchmark support: same model, different harness, double-digit swings. SWE-agent showed the same in 2024 with GPT-4.
- "Neither CLI nor desktop app is the end state" matches vendor behavior: every major harness now runs on several surfaces at once (Claude Code: terminal, web, phone, desktop via Cowork; Codex: CLI, IDE, cloud, app; Antigravity: app plus CLI).
- "Reimagined for non-technical people" has demand evidence: Manus's $100M ARR and $2B+ exit; Cowork going from Max-only preview to every paid plan to web and mobile in six months; OpenClaw's viral adoption through chat apps.

**Contradicts.**
- The CLI has not lost. It won 2025 and new entrants still launch terminal-first in August 2026 (Muse Code). Google's retreat from Gemini CLI is a move into an app, not away from the terminal, which survives as Antigravity CLI.
- The desktop app is not dead either: the Codex app, Cowork, Antigravity 2.0 and Devin Desktop all shipped in 2026. The evidence says "many surfaces on one runtime," not "a new default surface."
- The largest harness business to date (Cursor) won on the IDE, the surface the thesis discounts, then sold to a mega-platform. Every other successful harness of 2025–2026 belongs to a model lab or platform, and the labs are using pricing (Apr–May 2026) to make outside harnesses more expensive. A startup competes on distribution and economics against companies that own the model.
- Standardization (MCP, AGENTS.md, Skills) helps a startup build, but it also makes the harness's parts commodities. Moats, if any, are in permissions, runtime and workflow, not in the loop.

**Nuance.**
- "Better models make the harness the bottleneck" is half right. The 2026 harness changes ride on model improvements (auto mode arrived with Opus 4.7); the benchmark papers show model and harness interact.
- The unsolved part for non-technical users is permissions and blast radius, not surface. OpenClaw's incidents and Microsoft's identity-first Agent 365 both point there.
- Policy risk is new and real: the Meta–Manus unwinding shows a harness company's exit can be reversed by a government.

## Open questions and unverified claims

- Clawdbot's original release date (late 2025) and later star counts (a secondary guide claims ~347,000 by April 2026): unverified.
- Terminal-Bench 2.0 "93% in Cursor vs 77% in Claude Code" and the SWE-bench Pro scaffold spread: from blogs, not checked against leaderboards.
- Anthropic's "built Cowork in about a week and a half" and OpenAI's "zero hand-written lines": vendor claims, no independent audit.
- Codex "more than 2 million weekly active users by March 2026": secondary article, not verified against an OpenAI statement.
- Muse Code leaving beta on Sep 1, 2026: one secondary source.
- Managed Agents launch date (Apr 8 vs Apr 9, 2026): sources differ, probably time zones.
- Google's MCP adoption (Apr 2025) and the plugin shutdown (spring 2024): from memory of announcements, not re-fetched.
- Not covered: the content of "Natural-Language Agent Harnesses" (arXiv 2603.25723, Mar 2026) beyond its title, because arXiv was unreachable; OpenAI's own text for the Codex app and harness essay (openai.com unreachable; secondary summaries used).

## Sources

1. ReAct: Synergizing Reasoning and Acting in Language Models — https://arxiv.org/abs/2210.03629 — Oct 2022
2. Toolformer — https://arxiv.org/abs/2302.04761 — Feb 2023
3. LangChain repository — https://github.com/langchain-ai/langchain — Oct 2022
4. LlamaIndex repository — https://github.com/run-llama/llama_index — Nov 2022
5. ChatGPT plugins — https://openai.com/index/chatgpt-plugins/ — Mar 2023
6. AutoGPT repository — https://github.com/Significant-Gravitas/AutoGPT — Mar 2023
7. BabyAGI repository — https://github.com/yoheinakajima/babyagi — Apr 2023
8. Function calling and other API updates — https://openai.com/index/function-calling-and-other-api-updates/ — Jun 2023
9. Introducing GPTs — https://openai.com/index/introducing-gpts/ — Nov 2023
10. Rabbit r1 at CES (TechRadar) — https://www.techradar.com/phones/rabbit-r1s-ai-companion-is-the-ces-gadget-i-want-to-hate-but-may-end-up-loving — Jan 2024
11. Rabbit R1 review (Engadget) — https://www.engadget.com/rabbit-r1-review-a-199-ai-toy-that-fails-at-almost-everything-161043050.html — May 2024
12. Introducing Devin (Cognition) — https://cognition.ai/blog/introducing-devin — Mar 2024
13. SWE-agent: Agent-Computer Interfaces Enable Automated Software Engineering — https://arxiv.org/abs/2405.15793 — May 2024
14. Amazon hires execs from Adept (CNBC) — https://www.cnbc.com/2024/06/28/amazon-hires-execs-from-ai-startup-adept-and-licenses-its-technology.html — Jun 2024
15. An update from Adept — https://www.adept.ai/blog/adept-update/ — Jun 2024
16. Claude 3.5 models and computer use (Anthropic) — https://www.anthropic.com/news/3-5-models-and-computer-use — Oct 2024
17. Introducing the Model Context Protocol (Anthropic) — https://www.anthropic.com/news/model-context-protocol — Nov 2024
18. Building effective agents (Anthropic) — https://www.anthropic.com/engineering/building-effective-agents — Dec 2024
19. Introducing Operator (OpenAI) — https://openai.com/index/introducing-operator/ — Jan 2025
20. Humane's AI Pin is dead as HP buys assets (TechCrunch) — https://techcrunch.com/2025/02/18/humanes-ai-pin-is-dead-as-hp-buys-startups-assets-for-116m — Feb 2025
21. Claude 3.7 Sonnet and Claude Code (Anthropic) — https://www.anthropic.com/news/claude-3-7-sonnet — Feb 2025
22. OpenAI Agents SDK: MCP — https://openai.github.io/openai-agents-python/mcp/ — Mar 2025
23. Introducing o3 and o4-mini (Codex CLI) — https://openai.com/index/introducing-o3-and-o4-mini/ — Apr 2025; repo https://github.com/openai/codex
24. Introducing Codex (cloud) — https://openai.com/index/introducing-codex/ — May 2025
25. Introducing Claude 4 (Claude Code GA) — https://www.anthropic.com/news/claude-4 — May 2025
26. Introducing Gemini CLI — https://blog.google/technology/developers/introducing-gemini-cli-open-source-ai-agent/ — Jun 2025
27. Cognition acquires Windsurf (summary) — https://www.itechguides.com/cognition-maker-of-the-ai-coding-agent-devin-acquires-windsurf/ — Jul 2025
28. Introducing ChatGPT agent — https://openai.com/index/introducing-chatgpt-agent/ — Jul 2025
29. AGENTS.md — https://agents.md/ — Aug 2025
30. Building agents with the Claude Agent SDK — https://www.anthropic.com/engineering/building-agents-with-the-claude-agent-sdk — Sep 2025
31. Agent Skills (Anthropic) — https://www.anthropic.com/news/skills — Oct 2025
32. Claude Code on the web — https://www.anthropic.com/news/claude-code-on-the-web — Oct 2025
33. Cursor raises $2.3B at $29.3B (CNBC) — https://www.cnbc.com/2025/11/13/cursor-ai-startup-funding-round-valuation.html — Nov 2025
34. Introducing Google Antigravity — https://antigravity.google/blog/introducing-google-antigravity — Nov 2025
35. Effective harnesses for long-running agents (Anthropic) — https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents — Nov 2025
36. Linux Foundation forms the Agentic AI Foundation — https://www.linuxfoundation.org/press/linux-foundation-announces-the-formation-of-the-agentic-ai-foundation — Dec 2025
37. MCP joins the Agentic AI Foundation — https://blog.modelcontextprotocol.io/posts/2025-12-09-mcp-joins-agentic-ai-foundation/ — Dec 2025
38. Anthropic makes Agent Skills an open standard (SiliconANGLE) — https://siliconangle.com/2025/12/18/anthropic-makes-agent-skills-open-standard/ — Dec 2025; spec https://agentskills.io
39. Meta acquires Manus (CNBC) — https://www.cnbc.com/2025/12/30/meta-acquires-singapore-ai-agent-firm-manus-china-butterfly-effect-monicai.html — Dec 2025
40. First impressions of Claude Cowork (Simon Willison) — https://simonwillison.net/2026/Jan/12/claude-cowork/ — Jan 2026
41. Anthropic launches Cowork (VentureBeat) — https://venturebeat.com/technology/anthropic-launches-cowork-a-claude-desktop-agent-that-works-in-your-files-no — Jan 2026
42. From Clawdbot to Moltbot to OpenClaw (CNBC) — https://www.cnbc.com/2026/02/02/openclaw-open-source-ai-agent-rise-controversy-clawdbot-moltbot-moltbook.html — Feb 2026
43. OpenClaw is a security "dumpster fire" (The Register) — https://www.theregister.com/2026/02/03/openclaw_security_problems/ — Feb 2026
44. OpenClaw integrates VirusTotal scanning (The Hacker News) — https://thehackernews.com/2026/02/openclaw-integrates-virustotal-scanning.html — Feb 2026
45. Malicious crypto trading skills (Infosecurity Magazine) — https://www.infosecurity-magazine.com/news/malicious-crypto-trading-skills/ — Feb 2026
46. OpenClaw security guide, CVE-2026-25253 (Adversa) — https://adversa.ai/blog/openclaw-security-101-vulnerabilities-hardening-2026/ — 2026
47. Introducing the Codex app (OpenAI) — https://openai.com/index/introducing-the-codex-app/ — Feb 2026
48. OpenAI launches a Codex desktop app (VentureBeat) — https://venturebeat.com/orchestration/openai-launches-a-codex-desktop-app-for-macos-to-run-multiple-ai-coding — Feb 2026
49. Harness engineering: leveraging Codex in an agent-first world (OpenAI) — https://openai.com/index/harness-engineering/ — Feb 2026
50. OpenAI introduces harness engineering (InfoQ) — https://www.infoq.com/news/2026/02/openai-harness-engineering-codex/ — Feb 2026
51. OpenClaw creator Peter Steinberger joins OpenAI (TechCrunch) — https://techcrunch.com/2026/02/15/openclaw-creator-peter-steinberger-joins-openai/ — Feb 2026; his post https://steipete.me/posts/2026/openclaw
52. Claude Code Remote Control (Help Net Security) — https://www.helpnetsecurity.com/2026/02/25/anthropic-remote-control-claude-code-feature/ — Feb 2026
53. OpenAI turns Codex into a daily work agent (The Deep View) — https://www.thedeepview.com/articles/openai-turns-codex-into-a-daily-work-agent — 2026
54. Anthropic blocks OpenClaw from Claude subscriptions (TNW) — https://thenextweb.com/news/anthropic-openclaw-claude-subscription-ban-cost — Apr 2026; Hacker News thread https://news.ycombinator.com/item?id=47633396
55. Anthropic reveals Claude Managed Agents (TechRadar) — https://www.techradar.com/pro/go-from-prototype-to-launch-in-days-rather-than-months-anthropic-reveals-claude-managed-agents-promises-to-make-agent-building-10x-faster — Apr 2026
56. Anthropic launches Claude Managed Agents (AlternativeTo) — https://alternativeto.net/news/2026/4/anthropic-launches-claude-managed-agents-to-accelerate-ai-agent-development-and-deployment — Apr 2026
57. Claude Cowork general availability (TestingCatalog) — https://www.testingcatalog.com/anthropic-launches-claude-cowork-in-general-availability/ — Apr 2026
58. Claude Code, What's new, Week 16 (Apr 13–17, 2026) — https://code.claude.com/docs/en/whats-new/2026-w16 — Apr 2026
59. The harness effect: same model, different tool, different score (blog) — https://codex.danielvaughan.com/2026/04/19/the-harness-effect-same-model-different-tool-different-score/ — Apr 2026
60. Why China blocked the Meta–Manus deal (CNBC) — https://www.cnbc.com/2026/04/28/china-meta-manus-ai-deal.html — Apr 2026
61. China unwinds Meta's acquisition of Manus (O'Melveny) — https://www.omm.com/insights/alerts-publications/china-unwinds-meta-s-acquisition-of-manus-implications-for-cross-border-ai-transactions/ — 2026
62. Microsoft Agent 365 GA (Nerd Level Tech) — https://nerdleveltech.com/microsoft-agent-365-ga-ai-agent-control-plane — May 2026
63. Anthropic reinstates OpenClaw and third-party agent usage (VentureBeat) — https://venturebeat.com/technology/anthropic-reinstates-openclaw-and-third-party-agent-usage-on-claude-subscriptions-with-a-catch — May 2026
64. Transitioning Gemini CLI to Antigravity CLI (Google Developers Blog) — https://developers.googleblog.com/an-important-update-transitioning-gemini-cli-to-antigravity-cli/ — May 2026
65. Bye-bye, Gemini CLI (The Register) — https://www.theregister.com/ai-ml/2026/05/20/bye-bye-gemini-cli-google-nudges-devs-toward-antigravity/5243605 — May 2026
66. Harness-Bench — https://arxiv.org/html/2605.27922v1 — May 2026
67. Stop Comparing LLM Agents Without Disclosing the Harness — https://arxiv.org/pdf/2605.23950 — May 2026
68. At Build 2026, Microsoft sets up Windows as an OS for AI agents (Visual Studio Magazine) — https://visualstudiomagazine.com/articles/2026/06/02/at-build-2026-microsoft-sets-up-windows-as-an-os-for-ai-agents.aspx — Jun 2026
69. Windsurf becomes Devin Desktop (secondary) — https://www.digitalapplied.com/blog/windsurf-becomes-devin-desktop-ide-migration-2026 — Jun 2026
70. SpaceX to buy Cursor for $60 billion (CBS News) — https://www.cbsnews.com/news/spacex-cursor-60-billion-ai-acquisition/ — Jun 2026
71. From question answering to task completion: a survey on agent system and harness design — https://arxiv.org/pdf/2606.20683 — Jun 2026
72. Claude Cowork expands to mobile and web (TechCrunch) — https://techcrunch.com/2026/07/07/the-coding-agent-wars-are-spilling-into-the-rest-of-the-office-claude-cowork/ — Jul 2026
73. Meta launches Muse Code (9to5Mac) — https://9to5mac.com/2026/08/05/meta-launches-muse-code-ai-coding-agent-for-macos-and-linux/ — Aug 2026; Meta on X https://x.com/AIatMeta/status/2085084709277565213
74. DeepSeek Harness repository — https://github.com/deepseek-ai/deepseek-harness — Aug 2026; https://deepseek.com/harness/en/
75. SpaceX closes Cursor acquisition (SatNews) — https://satnews.com/2026/08/13/spacex-finalizes-regulatory-procedures-to-close-60-billion-acquisition-of-ai-platform-cursor/ — Aug 2026
76. Natural-Language Agent Harnesses — https://arxiv.org/pdf/2603.25723 — Mar 2026 (title only; not read)
