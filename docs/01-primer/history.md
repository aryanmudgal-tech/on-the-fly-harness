# Harness History 2022–2026: From ReAct to Managed Agents

*Research program: agent harnesses. Written 2026-09-08. Status: verified with notes.*

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

**ChatGPT plugins** ([OpenAI, Mar 2023](https://openai.com/index/chatgpt-plugins/)) put tools inside a mass-market surface via OpenAPI specs. Usage stayed low; OpenAI replaced them with GPTs ([Nov 2023](https://openai.com/index/introducing-gpts/)) and wound plugins down in spring 2024: no new plugin installs or plugin conversations after Mar 19, 2024, and a full shutdown on Apr 9, 2024 (OpenAI help-center notice "Winding down the ChatGPT plugins beta", quoted in the [OpenAI developer forum](https://community.openai.com/t/plugin-store-and-new-chats-with-plugins-closed-march-19-2024/689877); help.openai.com itself was unreachable). Lesson: tools bolted onto a chat loop that cannot plan, retry or verify are a novelty.

**AutoGPT** ([Mar 30, 2023](https://github.com/Significant-Gravitas/AutoGPT)) and **BabyAGI** ([Apr 2023](https://github.com/yoheinakajima/babyagi)) added orchestration and memory: a task queue, self-generated sub-tasks, a vector database, no human in the loop. Both went viral within weeks and both failed the same way: the model looped on one step, lost the goal, could not verify progress, and spent tokens without bound. Their failure is where the "stopping" and "human steering" parts of the loop definition come from.

**Function calling** ([OpenAI, Jun 13, 2023](https://openai.com/index/function-calling-and-other-api-updates/)) is the most important harness change of 2023. Developers declared functions with JSON schemas and the model returned a structured call. Tool-call reliability moved from the harness's parser into the model's training. MCP and Skills both build on this contract.

## Era 2 (2024): the agent-computer interface, and the first products

**Devin** ([Cognition, Mar 12, 2024](https://cognition.ai/blog/introducing-devin)) gave the agent its own computer: a sandboxed shell, editor and browser, driven asynchronously from a chat-like surface, with a claimed 13.86% on a SWE-bench subset (vendor claim; critics questioned the launch demo in Apr 2024, unverified here). Its contribution was runtime and surface: the agent runs elsewhere and reports back.

**SWE-agent** (Princeton; [repo Apr 2024, paper May 2024](https://arxiv.org/abs/2405.15793)) supplied the era's core idea, the "agent-computer interface" (ACI): tools designed for a model rather than a human. A file viewer that shows 100 lines at a time, bounded search results, an edit command that lints before accepting. With GPT-4 Turbo and no model change it reached 12.5% on the full SWE-bench test set. This is the earliest quantified evidence that the same model does better or worse depending on how its tools are shaped.

**Cursor** (Anysphere) turned the IDE into an agent surface: a VS Code fork with inline edits, then agent modes. It grew from about $100M ARR in January 2025 to a $29.3B valuation by November 2025 ([CNBC, Nov 2025](https://www.cnbc.com/2025/11/13/cursor-ai-startup-funding-round-valuation.html)). It is the strongest counter-example to "the IDE is over": for two years it was the largest agent business by revenue.

**Claude computer use** ([Anthropic, Oct 22, 2024](https://www.anthropic.com/news/3-5-models-and-computer-use)) made any GUI a tool: screenshot in, mouse and keyboard out, 14.9% on OSWorld (vendor figure). Tools went from "APIs the developer declared" to "anything a human could do on a screen," with a VM as the natural runtime.

**Model Context Protocol** ([Anthropic, Nov 25, 2024](https://www.anthropic.com/news/model-context-protocol)) standardized how a harness discovers and calls tools, resources and prompts from separate servers. OpenAI adopted it in its Agents SDK in [March 2025](https://openai.github.io/openai-agents-python/mcp/); Google followed on Apr 9, 2025, when Demis Hassabis announced MCP support for Gemini models and the SDK ([his post on X](https://x.com/demishassabis/status/1910107859041271977); [TechCrunch](https://techcrunch.com/2025/04/09/google-says-itll-embrace-anthropics-standard-for-connecting-ai-models-to-data/)). MCP made tools portable across harnesses: exactly what a startup harness needs, and exactly what erodes any one harness's moat.

Anthropic's **"Building effective agents"** ([Dec 2024](https://www.anthropic.com/engineering/building-effective-agents)) set 2025's tone: prefer simple, composable loops; separate predefined workflows from open-ended agents; distrust frameworks that hide the loop.

### What failed, and why

| Product | Bet | What happened | Lesson |
|---|---|---|---|
| Rabbit r1 | $199 device with a "large action model" that operates apps ([CES, Jan 2024](https://www.techradar.com/phones/rabbit-r1s-ai-companion-is-the-ces-gadget-i-want-to-hate-but-may-end-up-loving)) | Shipped Apr 2024; "a $199 AI toy that fails at almost everything" ([Engadget, May 2024](https://www.engadget.com/rabbit-r1-review-a-199-ai-toy-that-fails-at-almost-everything-161043050.html)); improved by updates, never a default | A new surface cannot carry an unreliable loop; the phone already owned the surface |
| Humane AI Pin | Screenless wearable as primary surface | ~10,000 sold vs 100,000 target; HP bought assets for $116M, servers off Feb 28, 2025 ([TechCrunch, Feb 2025](https://techcrunch.com/2025/02/18/humanes-ai-pin-is-dead-as-hp-buys-startups-assets-for-116m)) | Same, plus latency and battery: runtime limits killed the surface |
| Adept | Train an "action model" (ACT-1, 2022) and sell enterprise agents; ~$415M raised | Amazon hired the founders and licensed the tech, Jun 28, 2024 ([CNBC](https://www.cnbc.com/2024/06/28/amazon-hires-execs-from-ai-startup-adept-and-licenses-its-technology.html); [Adept](https://www.adept.ai/blog/adept-update/)) | Competing with frontier labs on the model lost; the harness layer was where a startup could still compete |
| ChatGPT plugins | Tools inside chat | Retired Apr 9, 2024 | Tools need a capable loop and a runtime, not a chat window |
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

**OpenClaw.** Peter Steinberger's open-source personal agent, first published on GitHub on Nov 24, 2025 under the name Warelay (renamed Clawdis in December and Clawdbot on Jan 2, 2026; repository creation date from the GitHub API), runs on the user's machine, talks through WhatsApp, Telegram, Discord and iMessage, keeps memory across conversations, wakes itself on a "heartbeat" or cron schedule, and loads community skills. After Anthropic trademark complaints it became Moltbot (Jan 27, 2026) then OpenClaw (Jan 30), with about 68,000 GitHub stars by Feb 2 ([CNBC](https://www.cnbc.com/2026/02/02/openclaw-open-source-ai-agent-rise-controversy-clawdbot-moltbot-moltbook.html)). Then the security bill arrived: Moltbook, a social network for OpenClaw agents, exposed a database with about 1.5 million API tokens and 35,000 emails; researchers found 341 of 2,857 ClawHub skills malicious ([Wiz](https://www.wiz.io/blog/exposed-moltbook-database-reveals-millions-of-api-keys); [Koi Security](https://www.koi.ai/blog/clawhavoc-341-malicious-clawedbot-skills-found-by-the-bot-they-were-targeting); [The Hacker News, Feb 2026](https://thehackernews.com/2026/02/openclaw-integrates-virustotal-scanning.html); [Infosecurity](https://www.infosecurity-magazine.com/news/malicious-crypto-trading-skills/)); a gateway flaw became CVE-2026-25253 ([secondary](https://adversa.ai/blog/openclaw-security-101-vulnerabilities-hardening-2026/)); The Register called it a "dumpster fire" ([Feb 3](https://www.theregister.com/2026/02/03/openclaw_security_problems/)). On Feb 15 Steinberger joined OpenAI and the project moved to a foundation ([TechCrunch](https://techcrunch.com/2026/02/15/openclaw-creator-peter-steinberger-joins-openai/); [his post](https://steipete.me/posts/2026/openclaw)). Surface: the chat apps people already use. Runtime: always-on. Orchestration: self-triggered. It is the clearest demand signal for a non-developer harness in this history, and the clearest demonstration that permissions and blast radius are the unsolved part.

**Claude Cowork.** A research preview for Max subscribers on the macOS app on Jan 12, 2026 ([Simon Willison](https://simonwillison.net/2026/Jan/12/claude-cowork/); [VentureBeat](https://venturebeat.com/technology/anthropic-launches-cowork-a-claude-desktop-agent-that-works-in-your-files-no)): Claude Code's runtime with a folder picker and a UI for non-coders, built, Anthropic said, in about a week and a half with Claude Code (vendor claim). GA on macOS and Windows for all paid plans on Apr 9, 2026 with enterprise controls ([Anthropic](https://claude.com/blog/cowork-for-enterprise); [TestingCatalog](https://www.testingcatalog.com/anthropic-launches-claude-cowork-in-general-availability/)); web and mobile (iOS and Android) announced Jul 7, 2026 as a beta rolling out to Max subscribers first ([Anthropic](https://claude.com/blog/cowork-web-mobile); [TechCrunch](https://techcrunch.com/2026/07/07/the-coding-agent-wars-are-spilling-into-the-rest-of-the-office-claude-cowork/)). A lab explicitly testing the thesis's second half: same harness, new surface, non-technical audience.

**Codex desktop app.** Launched for macOS on Feb 2, 2026 ([VentureBeat](https://venturebeat.com/orchestration/openai-launches-a-codex-desktop-app-for-macos-to-run-multiple-ai-coding); [OpenAI](https://openai.com/index/introducing-the-codex-app/)) as a "command center" for many agents in parallel git worktrees, with automations and skills; Windows followed on Mar 4, 2026, and OpenAI began positioning Codex as a daily work agent connected to Slack, Drive and email ([The Deep View, 2026](https://www.thedeepview.com/articles/openai-turns-codex-into-a-daily-work-agent)). The CLI is still there; the default view is a manager of agents.

**"Harness engineering."** OpenAI's essay ([Feb 11, 2026](https://openai.com/index/harness-engineering/); [InfoQ summary](https://www.infoq.com/news/2026/02/openai-harness-engineering-codex/)) reports five months, roughly one million lines of code, about 1,500 merged PRs, three engineers, no hand-written code (vendor claim). The techniques are all harness: AGENTS.md as a map to a docs directory that is the agent's memory, custom linters that encode taste, observability the agent can read, and "garbage collection" of stale docs. Two labs agreeing within three months is not independent evidence, but it shows where the labs' own effort goes.

**Claude Code's 2026 changes** show a CLI turning into a runtime with many surfaces. Remote Control ([Feb 25, 2026](https://www.helpnetsecurity.com/2026/02/25/anthropic-remote-control-claude-code-feature/)) lets a phone or browser drive a session on your own machine. Routines ([docs, Apr 13–17, 2026](https://code.claude.com/docs/en/whats-new/2026-w16)) are "templated cloud agents that fire on a schedule, a GitHub event, or an API call"; the same week added mobile push notifications, an "auto mode" that removes permission prompts on Opus 4.7, a `/usage` breakdown and native binaries. Loop, permissions, runtime, surface and orchestration all moved in one quarter.

**Who is allowed to be the harness.** On Apr 4, 2026 Anthropic stopped letting Pro and Max subscriptions be consumed through third-party harnesses such as OpenClaw, citing cost: its own tools are engineered for prompt-cache hits and outside loops are not ([TNW](https://thenextweb.com/news/anthropic-openclaw-claude-subscription-ban-cost); [Hacker News](https://news.ycombinator.com/item?id=47633396)). On May 13 it partially reversed course with fixed monthly "Agent SDK" credits for programmatic use (effective Jun 15, 2026), billed at API rates and expiring monthly ([VentureBeat](https://venturebeat.com/technology/anthropic-reinstates-openclaw-and-third-party-agent-usage-on-claude-subscriptions-with-a-catch)). Days after the block, Anthropic launched **Claude Managed Agents** on Apr 8, 2026 ([Anthropic](https://claude.com/blog/claude-managed-agents); [SiliconANGLE, Apr 8](https://siliconangle.com/2026/04/08/anthropic-launches-claude-managed-agents-speed-ai-agent-development/)): hosted sessions with sandboxing, persistence, tracing and a multi-agent orchestration preview, priced at API token rates plus $0.08 per session-hour ([pricing docs](https://platform.claude.com/docs/en/about-claude/pricing#claude-managed-agents-pricing)), with Notion, Rakuten and Sentry as launch customers ([TechRadar](https://www.techradar.com/pro/go-from-prototype-to-launch-in-days-rather-than-months-anthropic-reveals-claude-managed-agents-promises-to-make-agent-building-10x-faster); [AlternativeTo](https://alternativeto.net/news/2026/4/anthropic-launches-claude-managed-agents-to-accelerate-ai-agent-development-and-deployment)). Read together: restrict the cheap path for outside harnesses, sell the runtime as a service.

**Platform consolidation.** Microsoft made Agent 365, a control plane that gives agents identities and governs them with its existing security stack, generally available on May 1, 2026 at $15 per user per month, standalone or inside Microsoft 365 E7 ([Microsoft Security Blog](https://www.microsoft.com/en-us/security/blog/2026/05/01/microsoft-agent-365-now-generally-available-expands-capabilities-and-integrations/)), and at Build (Jun 2, 2026) pitched Windows as an OS for agents with a Windows Agent Runtime and "Windows 365 for Agents" cloud PCs ([Windows Developer Blog](https://blogs.windows.com/windowsdeveloper/2026/06/02/windows-platform-security-for-ai-agents/); [Visual Studio Magazine](https://visualstudiomagazine.com/articles/2026/06/02/at-build-2026-microsoft-sets-up-windows-as-an-os-for-ai-agents.aspx)). Google launched Antigravity 2.0 as a desktop app at I/O on May 19, 2026 and folded Gemini CLI into Antigravity CLI, with the old CLI ceasing to serve consumer tiers on Jun 18 ([Google Developers Blog](https://developers.googleblog.com/an-important-update-transitioning-gemini-cli-to-antigravity-cli/); [The Register](https://www.theregister.com/ai-ml/2026/05/20/bye-bye-gemini-cli-google-nudges-devs-toward-antigravity/5243605)). SpaceX announced an all-stock $60B acquisition of Cursor on Jun 16, 2026 and closed it on Aug 14 ([CBS News](https://www.cbsnews.com/news/spacex-cursor-60-billion-ai-acquisition/); [SatNews](https://satnews.com/2026/08/13/spacex-finalizes-regulatory-procedures-to-close-60-billion-acquisition-of-ai-platform-cursor/)).

**Every lab ships a harness.** Meta released **Muse Code** in beta on Aug 5, 2026: a terminal agent with persistent sub-agents in parallel git worktrees and no app ([9to5Mac](https://9to5mac.com/2026/08/05/meta-launches-muse-code-ai-coding-agent-for-macos-and-linux/); [Meta on X](https://x.com/AIatMeta/status/2085084709277565213)). Meta took it out of beta on Aug 31, 2026 (dated Sep 1 by outlets in other time zones) with inter-session messaging, a workflows engine, an SDK developer preview and $5–$50 monthly plans ([Meta developer blog](https://developer.meta.com/ai/resources/blog/muse-code-new-plans-and-features/); [Alexandr Wang on X](https://x.com/alexandr_wang/status/2094502557129543774), posted Aug 31, 2026 19:08 UTC). DeepSeek released **DeepSeek Harness v0.1** on Aug 13, 2026 under MIT (repository created that day, per the GitHub API): "everything is a plugin," with models, tools, skills, sessions, sandboxes, loops, scheduling and the UI all swappable, plus a web UI and headless mode ([GitHub](https://github.com/deepseek-ai/deepseek-harness); [DeepSeek](https://deepseek.com/harness/en/)). A model lab open-sourcing a harness says the harness is now distribution for the model.

**Evidence that the harness moves outcomes** has caught up with the essays. Harness-Bench ([arXiv, May 2026](https://arxiv.org/html/2605.27922v1)) and "Stop Comparing LLM Agents Without Disclosing the Harness" ([arXiv, May 2026](https://arxiv.org/pdf/2605.23950)) argue a benchmark score is a property of model plus harness and report double-digit swings for the same model; a June 2026 survey frames agent design as harness design ([arXiv](https://arxiv.org/pdf/2606.20683)). Widely repeated but secondary, and not supported by the public leaderboards: a blog claim that the same Claude Opus model scored 93% on Terminal-Bench 2.0 inside Cursor versus 77% inside Claude Code (unverified) ([blog, Apr 2026](https://codex.danielvaughan.com/2026/04/19/the-harness-effect-same-model-different-tool-different-score/)). No published Terminal-Bench 2.0 result matches either number: Anthropic reported 65.4% for Opus 4.6 (Feb 2026) and 69.4% for Opus 4.7 (Apr 2026), Cursor's own [Composer 2 post](https://cursor.com/blog/composer-2) (Mar 19, 2026) put Opus 4.6 at 58.0 and the leaderboard leader (GPT-5.4) at 75.1, and even in Sep 2026 the top entry is about 92%. Better supported: Opus 4.5 at 45.9% on SWE-bench Pro under Scale's standardized scaffold versus 50.2–55.4% for the same model across three other agent scaffolds (unverified), per secondary summaries of the Scale leaderboard that do not name Claude Code. Treat the direction as established, the SWE-bench Pro spread as indicative, and the Terminal-Bench figures as unusable until someone points to a leaderboard entry.

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

- Clawdbot's original release date is now verified (repository created Nov 24, 2025 as Warelay; see verification notes). Later star counts (a secondary guide claims ~347,000 by April 2026) remain unverified; the GitHub API showed 389,231 stars on Sep 8, 2026.
- Terminal-Bench 2.0 "93% in Cursor vs 77% in Claude Code": still unverified and inconsistent with every published leaderboard figure (see verification notes); do not cite. SWE-bench Pro: 45.9% is Scale's standardized-scaffold score for Opus 4.5; the 55.4% figure and its attribution to Claude Code remain unverified.
- Anthropic's "built Cowork in about a week and a half" and OpenAI's "zero hand-written lines": vendor claims, no independent audit.
- Codex "more than 2 million weekly active users by March 2026": verified as OpenAI's own figure in its Mar 19, 2026 Astral acquisition announcement (reported by CNBC and others; openai.com unreachable here).
- Muse Code leaving beta: verified, but the date is Aug 31, 2026 (Meta developer blog; Alexandr Wang's post), not Sep 1.
- Managed Agents launch date: verified as Apr 8, 2026 (Anthropic blog; SiliconANGLE dated Apr 8; Apr 9 dates are next-day coverage).
- Google's MCP adoption (Apr 9, 2025) and the plugin shutdown (Mar 19 and Apr 9, 2024): now verified against the Hassabis post, TechCrunch, and forum and Zapier notices quoting OpenAI's help-center article.
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
77. Google to embrace Anthropic's MCP standard (TechCrunch) — https://techcrunch.com/2025/04/09/google-says-itll-embrace-anthropics-standard-for-connecting-ai-models-to-data/ — Apr 9, 2025; Hassabis post https://x.com/demishassabis/status/1910107859041271977
78. Plugin store and new chats with plugins closed March 19, 2024 (OpenAI developer forum, quoting the help-center notice) — https://community.openai.com/t/plugin-store-and-new-chats-with-plugins-closed-march-19-2024/689877 — Mar 2024
79. OpenAI to acquire Astral (Codex "more than 2 million weekly active users") — https://openai.com/index/openai-to-acquire-astral/ — Mar 19, 2026; CNBC https://www.cnbc.com/2026/03/19/openai-to-acquire-developer-tooling-startup-astral.html
80. Introducing Composer 2 (Cursor; Terminal-Bench 2.0 figures for Opus 4.6 and GPT-5.4) — https://cursor.com/blog/composer-2 — Mar 19, 2026
81. Claude Managed Agents: get to production 10x faster (Anthropic) — https://claude.com/blog/claude-managed-agents — Apr 8, 2026; pricing https://platform.claude.com/docs/en/about-claude/pricing#claude-managed-agents-pricing; SiliconANGLE https://siliconangle.com/2026/04/08/anthropic-launches-claude-managed-agents-speed-ai-agent-development/
82. Making Claude Cowork ready for enterprise (Cowork GA) — https://claude.com/blog/cowork-for-enterprise — Apr 9, 2026
83. Anthropic Agent SDK credit policy, summary citing support.claude.com article 15036540 (gist) — https://gist.github.com/MagnaCapax/d9177e35b355853f03c730dfcaa693ef — May 2026
84. China orders unwinding of the Meta–Manus deal (Lexology) — https://www.lexology.com/library/detail.aspx?g=2175a4dd-124d-419d-a93e-d3ea5bd84726 — 2026
85. Microsoft Agent 365, now generally available (Microsoft Security Blog) — https://www.microsoft.com/en-us/security/blog/2026/05/01/microsoft-agent-365-now-generally-available-expands-capabilities-and-integrations/ — May 1, 2026
86. Windows platform security for AI agents (Windows Developer Blog, Build 2026) — https://blogs.windows.com/windowsdeveloper/2026/06/02/windows-platform-security-for-ai-agents/ — Jun 2, 2026
87. Windsurf is now Devin Desktop (Cognition) — https://devin.ai/blog/windsurf-is-now-devin-desktop — Jun 2, 2026
88. SpaceX to acquire Cursor for $60 billion (CNBC) — https://www.cnbc.com/2026/06/16/spacex-spcx-cursor-acquisition-ipo.html — Jun 16, 2026; closing: Bloomberg https://www.bloomberg.com/news/articles/2026-08-14/spacex-completes-its-60-billion-cursor-acquisition — Aug 14, 2026
89. Claude Cowork on web and mobile (Anthropic) — https://claude.com/blog/cowork-web-mobile — Jul 7, 2026
90. Introducing Muse Code and Muse Spark 1.2 (Meta AI Research) — https://research.meta.ai/blog/introducing-muse-code-and-muse-spark-1-2 — Aug 5, 2026; TechCrunch https://techcrunch.com/2026/08/05/meta-launches-muse-code-an-ai-agent-for-large-code-bases/
91. Muse Code: New plans and features (Meta developer blog) — https://developer.meta.com/ai/resources/blog/muse-code-new-plans-and-features/ — Aug 31, 2026; Alexandr Wang on X https://x.com/alexandr_wang/status/2094502557129543774
92. Hacking Moltbook: exposed database reveals 1.5M API keys (Wiz) — https://www.wiz.io/blog/exposed-moltbook-database-reveals-millions-of-api-keys — Feb 2026
93. ClawHavoc: 341 malicious skills on ClawHub (Koi Security) — https://www.koi.ai/blog/clawhavoc-341-malicious-clawedbot-skills-found-by-the-bot-they-were-targeting — Feb 2026
94. Repository metadata for openclaw/openclaw and deepseek-ai/deepseek-harness (GitHub API, retrieved Sep 8, 2026) — https://api.github.com/repos/openclaw/openclaw ; https://api.github.com/repos/deepseek-ai/deepseek-harness

## Verification notes (2026-09-08)

**Method.** Each claim below was checked against its primary source where the network allowed, otherwise against several independent reports; X post dates were decoded from the post IDs (snowflake timestamps, UTC); repository dates come from the GitHub API. Blocked by the network proxy and not opened: openai.com, help.openai.com, anthropic.com, claude.com, support.claude.com, cursor.com, tbench.ai, labs.scale.com, wikipedia.org, developer.meta.com, research.meta.ai, developers.googleblog.com, cnbc.com, techcrunch.com, venturebeat.com, theregister.com, 9to5mac.com, siliconangle.com, news.ycombinator.com, omm.com, finance.yahoo.com, codex.danielvaughan.com, morphllm.com, llm-stats.com, benchlm.ai, vals.ai, devin.ai, blogs.windows.com, techcommunity.microsoft.com and most smaller blogs; where a primary page is cited but was unreachable, its content is taken from search excerpts of that page plus independent coverage. Reachable and read in full: platform.claude.com docs, the Microsoft Security Blog, gist.github.com, GitHub API repository metadata.

| # | Claim in the document | Verdict | Evidence and notes |
|---|---|---|---|
| 1 | Same Claude Opus model: 93% on Terminal-Bench 2.0 in Cursor vs 77% in Claude Code | Corrected (reframed; figures marked unverified) | Origin is a single blog (Vaughan, Apr 19, 2026), echoed by other blogs; none cites a leaderboard entry. Published figures: Anthropic 65.4% (Opus 4.6, Feb 2026) and 69.4% (Opus 4.7, Apr 2026); Cursor's Composer 2 post (Mar 19, 2026) has Opus 4.6 at 58.0 and GPT-5.4 at 75.1; the leaderboard top in Sep 2026 is about 92%. A 93% Opus run in April 2026 would have beaten every public entry by ~18 points. tbench.ai was unreachable, so the leaderboard itself was not opened. |
| 2 | Opus 4.5 at 45.9% on SWE-bench Pro (standard scaffold) vs 55.4% under Claude Code | Unverified (45.9% confirmed; 55.4% and its attribution not) | 45.9% is Scale's standardized public-set score for Opus 4.5 (multiple summaries; labs.scale.com unreachable). 55.4% appears in secondary summaries as the top of a 50.2–55.4% range across "three agent systems" running Opus 4.5; none names Claude Code. Wording changed accordingly. |
| 3 | Clawdbot first released "in late 2025 (exact date unverified)" | Corrected | GitHub API: openclaw/openclaw created 2025-11-24T10:16:47Z. Timelines (Wikipedia, openclaw.academy, via search) give first release v0.1.1 on Nov 25, 2025 as "Warelay", renames to Clawdis (Dec 3, 2025), Clawdbot (Jan 2, 2026), Moltbot (Jan 27), OpenClaw (Jan 30). |
| 4 | Muse Code left beta on Sep 1, 2026 | Corrected | Meta developer blog "Muse Code: New plans and features" is dated Aug 31, 2026; Alexandr Wang's post ID decodes to 2026-08-31 19:08 UTC. Enterprise DNA, Neowin and The New Stack covered it on Sep 1. Features (inter-session messaging, workflows, SDK developer preview, $5–$50 plans) confirmed. |
| 5 | Codex "more than 2 million weekly active users by March 2026" | Confirmed | OpenAI's Astral acquisition announcement (Mar 19, 2026), reported by CNBC and gHacks: more than 2M weekly active users, 3x since January; the Windows app launch on Mar 4 cited 1.6M. |
| 6 | ChatGPT plugins wound down in spring 2024 | Confirmed, dates added | OpenAI help-center notice quoted by the OpenAI developer forum and Zapier: no new installs or plugin conversations after Mar 19, 2024; existing conversations ended Apr 9, 2024. |
| 7 | Google adopted MCP in April 2025 | Confirmed | Hassabis post on X (ID decodes to 2025-04-09 23:09 UTC); TechCrunch, Apr 9, 2025. |
| 8 | Managed Agents launched Apr 8–9, 2026; $0.08 per session-hour; Notion, Rakuten, Sentry | Corrected (date) / confirmed (pricing, customers) | SiliconANGLE dated Apr 8, 2026; Help Net Security Apr 9 is next-day coverage; Anthropic blog post. Pricing page on platform.claude.com read in full: "$0.08 per session-hour", tokens billed at model rates. |
| 9 | SpaceX acquired Cursor for $60B, announced Jun 16, closed Aug 14, 2026 | Confirmed | CNBC Jun 16, 2026 (agreement); Bloomberg, Yahoo Finance and Seeking Alpha Aug 14, 2026: all-stock, effective Aug 14 per regulatory filing, roughly 391M SpaceX Class A shares; SatNews Aug 13 on regulatory clearance. |
| 10 | Anthropic blocked third-party harnesses on Apr 4, 2026 and partially reinstated them with capped credits on May 13 | Confirmed | TNW, VentureBeat, Hacker News thread 47633396, dev.to: effective Apr 4, 2026, 12:00 PT, with the prompt-cache rationale. Agent SDK credits announced May 13, 2026, effective Jun 15, 2026, metered at API list prices, no rollover (gist summarizing support.claude.com article 15036540; VentureBeat; GIGAZINE May 14). |
| 11 | China ordered the Meta–Manus deal unwound on Apr 27, 2026 | Confirmed | O'Melveny, Lexology and Trivium China: order of Apr 27, 2026 by the Office of the Foreign Investment Security Review Mechanism; first forced unwinding of a completed deal under that mechanism. Follow-up not added to the body: CNBC (Jun 12, 2026) reported Meta beginning to dismantle the deal; ChinaTechNews (Aug 12, 2026) reported Manus separated from Meta. |
| 12 | Cowork GA on Apr 9, 2026; web and mobile on Jul 7, 2026 | Confirmed | Anthropic posts "Making Claude Cowork ready for enterprise" (Apr 9, 2026: GA on all paid plans, macOS and Windows) and "Claude Cowork on web and mobile" (Jul 7, 2026: beta, Max first, iOS and Android); 9to5Mac (Jul 13 update). Primary pages identified through search excerpts, not opened. |
| 13 | Microsoft Agent 365 GA on May 1, 2026 at $15 per user | Confirmed | Microsoft Security Blog, May 1, 2026 (read in full): "General availability starts today"; "standalone at USD15 per user per month" or included in Microsoft 365 E7. Secondary link replaced. |
| 14 | Build (Jun 2, 2026): Windows as an OS for agents, Windows Agent Runtime, Windows 365 for Agents | Confirmed | Windows Developer Blog, Redmondmag and Visual Studio Magazine, all Jun 2, 2026; Windows Agent Runtime in preview; Windows 365 for Agents GA on Jun 1, 2026 (Windows IT Pro blog). |
| 15 | Antigravity 2.0 launched at I/O on May 19, 2026; Gemini CLI stops serving consumer tiers on Jun 18 | Confirmed | Google Developers Blog "Transitioning Gemini CLI to Antigravity CLI" (via search excerpts), Virtualization Review (May 19), The Register (May 20): cutoff Jun 18, 2026 for AI Pro, AI Ultra and free Code Assist users; Antigravity CLI shares the Antigravity 2.0 harness. |
| 16 | DeepSeek Harness v0.1 released Aug 13, 2026 under MIT, "everything is a plugin", web UI and headless mode | Confirmed | GitHub API: repository created 2026-08-13T11:56:32Z, MIT, description "DeepSeek Harness: Everything is a Plugin.", 216k stars on Sep 8, 2026. AIbase, MarkTechPost (Aug 17) and others on the plugin list, local web UI and headless mode. |
| 17 | Muse Code beta on Aug 5, 2026: terminal-only, sub-agents in parallel worktrees, no app | Confirmed | Wang's and AI at Meta's posts decode to 2026-08-05; TechCrunch, 9to5Mac, CNBC, Meta AI Research blog (Aug 5). "Persistent" sub-agents is the author's wording; sources say sub-agents run simultaneously in isolated worktrees. |
| 18 | Manus reached ~$100M ARR by Dec 2025 and was bought by Meta for more than $2B on Dec 30, 2025 | Confirmed | CNBC Dec 30, 2025: Manus announced $100M ARR in December, eight months after launch; deal announced Dec 30 at more than $2B (some reports: ~$2.5B cash plus a $500M retention pool). |
| 19 | OpenClaw incidents: Moltbook exposed ~1.5M API tokens and ~35,000 emails; 341 of 2,857 ClawHub skills malicious; CVE-2026-25253 | Confirmed | Wiz (Supabase misconfiguration; 1.5M tokens, 35,000 emails, private DMs); Koi Security "ClawHavoc" (341 of 2,857 skills, 335 from one campaign; The Hacker News, SC Media); CVE-2026-25253 is a one-click token-theft-to-RCE flaw, CVSS 8.8 (SentinelOne, runZero, SonicWall). |
| 20 | "Harness engineering" essay, Feb 11, 2026: five months, ~1M lines, ~1,500 PRs, three engineers, no hand-written code | Confirmed | OpenAI essay by Ryan Lopopolo, published Feb 11, 2026 (openai.com unreachable; InfoQ and other summaries): team grew from three to seven engineers; ~3.5 PRs per engineer per day. |
| 21 | Windsurf became Devin Desktop on Jun 2, 2026 | Confirmed | Cognition blog "Windsurf is now Devin Desktop" and Devin docs (via search), plus several reports: over-the-air rename on Jun 2, 2026, Agent Command Center, Cascade rewritten as Devin Local. |
| 22 | Codex app: Windows "followed in March" | Confirmed, date added | Mar 4, 2026 (Neowin, SD Times, TechRepublic, WinBuzzer). |
| 23 | Routines (Apr 13–17, 2026), auto mode on Opus 4.7 | Confirmed | Claude Code "What's new" week 16; Routines launched Apr 14, 2026 as a research preview with scheduled, API and GitHub triggers; Opus 4.7 released Apr 16, 2026. |

**Corrections made in the body.**
1. Terminal-Bench 2.0 paragraph: the 93% vs 77% claim is now presented as unsupported by any published leaderboard figure, with the published Opus 4.6/4.7 and Cursor Composer 2 numbers for contrast; the SWE-bench Pro sentence now attributes 45.9% to Scale's standardized scaffold and drops the unverified "under Claude Code" attribution of 55.4%.
2. OpenClaw: first release corrected from "Clawdbot in late 2025 (exact date unverified)" to Nov 24, 2025 under the name Warelay, renamed Clawdbot on Jan 2, 2026.
3. Muse Code: exit from beta corrected from Sep 1 to Aug 31, 2026, with Meta's post and Wang's timestamped announcement as sources.
4. Managed Agents: launch date fixed at Apr 8, 2026 (was "Apr 8–9, sources differ"); pricing now cited to Anthropic's pricing page.
5. Smaller edits: exact plugin shutdown dates (Mar 19 and Apr 9, 2024) in the text and the failures table; Google's MCP date (Apr 9, 2025) with the Hassabis post; Cowork GA and web/mobile now cite Anthropic's posts and note the Jul 7 rollout was a Max-first beta; Codex Windows app dated Mar 4, 2026; Agent SDK credits given their Jun 15, 2026 effective date; Agent 365 sourced to Microsoft's own blog; Build sourced to the Windows Developer Blog; Wiz and Koi Security added as primary sources for the OpenClaw incidents; DeepSeek Harness repository date added; the "Open questions" list updated to match.

**Remaining doubts.**
- Terminal-Bench 93% vs 77%: could be an internal or partial run, or a different task set; until a leaderboard entry or reproducible run exists, do not cite the figures.
- SWE-bench Pro 55.4%: which scaffold produced it is unknown; the Scale leaderboard itself was not opened.
- Agent SDK credit amounts differ between sources (Max 20x at $200 per the gist, $400 per VentureBeat's excerpt); the body states no amounts.
- Vendor claims left as vendor claims: Cowork "built in about a week and a half", OpenAI's "zero hand-written lines", Devin's 13.86%, computer use's 14.9%.
- OpenClaw star counts: ~68,000 by Feb 2 (CNBC) not re-checked; ~347,000 by April 2026 unverified; 389,231 on Sep 8, 2026 per the GitHub API.
- 2022–2024 dates were not re-checked in this pass except the plugin shutdown and Google's MCP adoption; they match the primary sources listed.
- Primary pages for Antigravity 2.0, Devin Desktop, Cowork, Managed Agents, Muse Code, the SpaceX–Cursor filings and the Manus order were identified but could not be opened; each is supported by at least two independent reports.


## Coordinator's cross-document note (2026-09-09)

The account above of Anthropic's third-party harness policy (credits announced May 13, 2026 for June 15) is the version reachable through search excerpts. The strategy document, `docs/07-strategy/value-capture.md` (section 5 and verification note 3e), fetched the OpenClaw provider documentation and records the later sequence: the June 15 credit change was paused on the day it was due, with such usage drawing on plan limits again; from July 13 third-party apps drew on "extra usage" rather than plan limits; and by September 3 detection of OpenClaw's prompt markers left subscription users without a model unless they bought extra-usage credit. Read the strategy document as authoritative on this timeline.
