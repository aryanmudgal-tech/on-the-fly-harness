# Independent developer harnesses: who builds them, how they are built, and what is converging

*Research program: agent harnesses. Written 2026-09-08. Status: draft, pending verification.*

## What this document answers

- Who builds agent harnesses outside the big labs (Anthropic, OpenAI, Google, GitHub, Amazon), and how each one is put together across the seven anatomy parts: loop, tools, context and memory, permissions, runtime, surface, orchestration.
- Which model provider each harness depends on, whether it is open source, and how it makes money.
- What happened to this category between January and September 2026: who died, who was acquired, who pivoted, and why.
- What is converging across independent harnesses, what is still differentiated, and what that says about the thesis that neither a CLI nor a desktop app can be the default way people operate agents.

## TL;DR

- **The independent field split into three surfaces, and none of them is "the CLI."** Developer harnesses are converging on an *agent manager* (a window that runs many agents in parallel in isolated git worktrees, local or cloud). Non-technical builders (Lovable, Replit, Bolt, v0) live in the browser. Personal agents (OpenClaw, Hermes, NanoClaw) live in messaging apps. The CLI survives as the *engine* that the other surfaces wrap, not as the surface itself.
- **2026 was brutal for standalone harness companies.** Terragon shut down (January 2026), Bloop killed Vibe Kanban (April 2026), Roo Code killed its 24k-star VS Code extension to bet on a Slack-first cloud agent (May 2026), and Cursor absorbed Continue (June 2026). Amp launched ad-supported coding (October 2025) and abandoned ads within five months (March 2026).
- **Model dependence is the hidden risk.** Almost every independent harness is model-agnostic on paper, but Anthropic's April 4, 2026 rule barring third-party harnesses from Claude Pro/Max subscriptions showed a provider can change a harness's unit economics overnight. Only Cursor and Cognition ship their own models, and Cursor's Composer 2 turned out to be post-trained on Moonshot's Kimi K2.5.
- **The personal-agent harnesses out-scaled the coding harnesses on GitHub.** OpenClaw sits at roughly 389k stars and Hermes Agent at roughly 243k (both 2026-09-08), versus 206k for opencode, 103k for pi and 68k for Cline. Their surface is WhatsApp, Telegram, Slack, Discord and Signal, with a local gateway process as the runtime.
- **Minimal harnesses are a real counter-trend.** pi (four tools, a ~300-word system prompt, no built-in permission prompts) is the engine under OpenClaw; NanoClaw replaces OpenClaw's "nearly half a million lines" with one process and a handful of files on top of Anthropic's Agent SDK. Both push safety into containers instead of approval dialogs.
- **Orchestration moved inside the harness.** Cursor 3's Agents window, Devin Desktop's Command Center, Kilo's Agent Manager, Augment's Intent, Warp's Oz and OpenAI's open-source Symphony all do what Conductor, Vibe Kanban and Terragon did as standalone products. Two of those three standalone products are dead.
- **Money is flowing to harnesses that own a workflow end to end**, not to generic agent loops: Cognition ($1B at $26B, May 2026), Lovable ($400M at $13.3B, August 2026), Factory ($150M at $1.5B, April 2026), Qodo ($70M, March 2026), Conductor ($22M, March 2026).

## A short glossary for readers new to agents

Analogy first, then the precise version.

- **Harness.** The car around the engine. Precisely: everything that turns a model API call into a working agent, split here into seven parts (loop, tools, context/memory, permissions, runtime, surface, orchestration).
- **BYOK (bring your own key).** You buy fuel directly from the refinery; the car maker does not resell it. Precisely: the harness lets you paste your own API key for Anthropic, OpenAI, Google or a local model, and bills nothing for tokens.
- **Git worktree.** Giving each contractor their own photocopy of the blueprints so they do not draw over each other. Precisely: a second checkout of the same repository on a separate branch, in a separate directory, so several agents can edit the same codebase in parallel without conflicts until you merge.
- **Subagent.** A helper the agent hires for one sub-task, with its own scratchpad. Precisely: a child model loop with a fresh context window, given a narrower task, whose result is summarised back to the parent.
- **MCP (Model Context Protocol).** USB for tools: one plug standard so any tool works with any agent. **ACP (Agent Client Protocol).** The same idea for editors: one plug so any agent can run inside any editor, the way the Language Server Protocol let one language server work in every editor.
- **Orchestrator.** A foreman who hands tickets to several workers and checks their work. Precisely: software that spawns many agent sessions (usually CLIs), isolates each in a worktree or sandbox, and shows you their diffs for review.

## The stack, in one picture

Independent harnesses have layered themselves. The CLIs became engines; protocols connect them to editors; orchestrators and vendor "agent managers" sit on top.

```
 Surfaces      IDE / desktop     Browser app       Chat app (WhatsApp, Slack, Telegram)
               (Cursor 3, Devin  (Lovable, Replit,  (OpenClaw, Hermes, NanoClaw, Roomote)
                Desktop, Zed,     Bolt, v0)
                Kilo, Intent)
                     |                 |                       |
 Orchestration  Agent managers, worktree runners, ticket-driven schedulers
               (Cursor Agents window, Devin Command Center, Conductor, Superset,
                Vibe Kanban [dead], Terragon [dead], Symphony, Warp Oz, Cline Hub)
                     |
 Protocols      ACP (agent <-> editor)      MCP (agent <-> tools)     AGENTS.md / skills
                     |
 Engines        CLI harnesses: opencode, pi, Amp, Droid, Auggie, Kilo CLI, Aider,
               plus the labs' Claude Code / Codex / Gemini CLI that the above embed
                     |
 Models         Anthropic, OpenAI, Google, open-weight (Kimi, DeepSeek, GLM, Qwen),
               and two in-house lines: Cursor Composer, Cognition SWE-1.x
```

## Family 1: IDE-centred harnesses that grew an agent manager

### Cursor (Anysphere)

- **Surface.** A VS Code fork, plus a CLI, a web app, and, since Cursor 3 (released April 2, 2026 per multiple secondary write-ups; cursor.com was unreachable for direct verification), an *Agents window* that runs many agents in parallel across local checkouts, worktrees, cloud sandboxes and SSH hosts from one pane ([9], [10]).
- **Loop and orchestration.** Cloud Agents run in Cursor-hosted environments; the May 13, 2026 changelog adds multi-repo support, Dockerfile-defined environments, version history with rollback and scoped secrets; August 2026 removed the requirement for a connected GitHub account; September 2026 added *self-hosted machines* so tool execution stays inside a customer's network (Cursor changelog, [1]). *Automations* (scheduled or triggered runs) require Pro (secondary, [10]).
- **Own harness as a product.** The Cursor SDK (April 2026) lets developers "build agents with the same runtime, harness, and models that power Cursor", in TypeScript, running in the desktop app, CLI and web app; the June 2026 SDK release added custom stores, custom tools and auto-review ([1], [2]).
- **Models.** Frontier models from Anthropic, OpenAI and Google, plus Cursor's in-house *Composer* line. Composer 2 shipped March 19, 2026; on March 22, 2026 Cursor confirmed, after a user found the model id `kimi-k2p5-rl-0317-s515-fast` in API responses, that it was built on Moonshot's Kimi K2.5 with Cursor's own RL on top; Cursor said only about a quarter of the compute came from the base model ([11]). Composer 2.5 followed in May 2026 (secondary, [1]).
- **Permissions and runtime.** Cloud agents run in isolated environments; local agents run in the editor. Detail on approval policies is not covered by sources reachable here (unverified).
- **Business and status.** Closed source; subscription tiers reported as Hobby (free), Pro $20, Pro+ $60, Ultra $200, Teams $40/user, Enterprise (secondary, [10]). Cursor acquired Continue on June 16, 2026 (The New Stack, [12]). One secondary source claims SpaceX agreed to buy Cursor for $60B around the same time; I could not verify that against any primary or major outlet and treat it as **unverified**.
- **Distinctive.** The only independent harness with an in-house model *and* a public SDK exposing its harness, and the first to make "many agents, many runtimes, one window" the default IDE view.

### Cognition (Devin, and Devin Desktop, formerly Windsurf)

- **Surface.** Devin is a cloud agent reached through a web app, Slack, and issue trackers. Windsurf, the IDE Cognition acquired in July 2025, was relaunched as *Devin Desktop* on June 2, 2026 via an over-the-air update; its local agent Cascade reached end of life on July 1, 2026 and was replaced by *Devin Local* ([13], secondary detail [14]). Devin Desktop ships an *Agent Command Center*, a kanban view of every local and cloud agent, and supports ACP so third-party agents can run inside it (secondary, [14], [15]).
- **Models.** Frontier models plus in-house *SWE-1.5* (October 2025) and *SWE-1.6* (April 7, 2026, secondary [16]).
- **Business.** Closed source. Reported Devin Desktop tiers: Free, Pro $20, Max $200, Teams, Enterprise (secondary, [14]). Cognition raised over $1B at a $26B post-money valuation in May 2026 (TechCrunch, May 27, 2026, [17]); press coverage put ARR at $492M (secondary, [18]); TechCrunch reported in August 2026 that it was in talks at a $40B valuation ([19]).
- **Distinctive.** The clearest statement of the "agent manager wrapped in an IDE" shape, and the only independent that runs both a fully autonomous cloud agent and a local IDE agent under one brand.

### Zed and the Agent Client Protocol

- **Surface.** A GPL-3.0 (with Apache-2.0 components) Rust editor, ~90k GitHub stars (2026-09-08, [20]). Zed 1.0 shipped April 29, 2026 with parallel agents in one window and "Zed for Business" (secondary, [21]).
- **The protocol.** ACP is "a protocol for connecting any editor to any agent", Apache-2.0, protocol version 1, with official SDKs in Kotlin, Java, Python, Rust and TypeScript and no CLA ([22]). The ACP registry launched January 28, 2026 with JetBrains ([23]); secondary sources count more than 50 registered agents by late June 2026 ([24]). Adopters visible in this research: JetBrains IDEs, Devin Desktop (June 2026), Factory Droid (lists Zed as a surface), opencode, and Cursor's agent as an ACP client inside Zed (secondary, [21]).
- **Models and business.** BYOK plus Zed-billed models; subscriptions for hosted usage.
- **Distinctive.** Zed bet that the editor should *not* own the agent. That is the opposite of Cursor's bet, and JetBrains and Cognition sided with Zed.

### Kilo (Kilo Code)

- MIT, ~27k stars (2026-09-08). A fork of Roo Code (itself a Cline fork) that became "the all-in-one agentic engineering platform": VS Code, JetBrains, a CLI that is "a fork of OpenCode", a cloud web app, automated code reviews, and *KiloClaw*, an always-on agent ([25]). Five built-in agents (Code, Plan, Ask, Debug, Review), MCP marketplace, `kilo run --auto` for CI. Its Agent Manager release notes in September 2026 mention worktree sessions and grouped PR checks ([26]).
- **Models and business.** 500+ models through Kilo's gateway "at the model provider's rate with zero markup" ([25]); revenue comes from the gateway and cloud. Reported $8M seed co-founded by GitLab's Sid Sijbrandij (secondary, [27]; unverified).
- **Distinctive.** The main migration target when Roo Code shut down; it stitched together three open-source lineages (Cline, Roo, opencode) into one product.

### Cline

- Apache-2.0, ~68k stars (2026-09-08). "The open source coding agent in your IDE, terminal, and desktop": VS Code extension, CLI, desktop app, JetBrains plugin and SDK; Plan/Act modes with approval-required edits and commands or auto-approve; MCP; checkpoints; browser use; "multi-agent teams"; "scheduled agents"; headless CLI for CI ([28]). September 2026 releases show a desktop app (v0.0.23) with a background *Hub* process that runs plugins from `~/.agents/plugins` and scheduled runs, and "ClinePass" sign-in ([29]).
- **Models.** BYOK: Anthropic, OpenAI, Google, 200+ via OpenRouter, Bedrock, Vertex, Cerebras/Groq, Ollama/LM Studio, any OpenAI-compatible API ([28]).
- **Business.** Free OSS plus teams/enterprise plans; a reported $32M round from Emergence Capital (secondary, unverified).

### Roo Code (shut down) and Roomote

- Apache-2.0, ~24k stars. The README states the extension "was shut down on May 15th" 2026 and points users to the community fork ZooCode and to Cline ([30]). The team announced the sunset on April 20, 2026, saying it did not believe IDEs are the future of coding, and pivoted to *Roomote*, a cloud agent driven from Slack, GitHub and Linear that runs and verifies code before handing back a PR; as of May 2026 it had no public release (secondary, [31]).
- **Why it matters.** A harness with a large user base concluded the IDE surface was a dead end and moved to chat plus cloud. Whether Roomote finds paying users is the open question.

### Augment Code (Intent, Auggie)

- *Intent*, public beta February 2026 (macOS only), is a "post-IDE workspace" for orchestrating agents: a coordinator agent splits a spec into tasks, specialist agents run in parallel in isolated worktrees, and a verifier agent checks results against a "living spec" before review (secondary, [32]). The Context Engine was unbundled as an MCP server usable from Cursor or Claude Code (GA February 6, 2026, secondary [33]). *Auggie* is the CLI: source public on GitHub (279 stars), requires an Augment account, supports `--print` for CI ([34]).
- **Business.** Closed core, usage credits; funding figures circulating ($270M round, $472M total) are **unverified**.
- **Distinctive.** Sells the context engine as a component to competitors' harnesses.

### Continue (acquired)

- Apache-2.0, ~36k stars. A YC S23 company that pivoted in 2025 to "Continuous AI" (a `cn` CLI and PR checks defined in markdown). Cursor acquired it on June 16, 2026; the repository is now read-only after a final 2.0.0 release that removed telemetry and authentication; cloud data was deleted after July 15, 2026 ([12], [35]).

## Family 2: terminal-first harnesses

### Amp (Amp Inc, formerly Sourcegraph)

- Spun out of Sourcegraph as an independent company in December 2025, led by Quinn Slack with Thorsten Ball ([36], [37]). CLI plus VS Code-compatible extension; shareable threads; subagents.
- **Business experiments.** Amp Free launched in October 2025 with a daily credit grant paid for by ads inside the agent's output, and Amp said ad sales reached a $10M+ annual run rate; in March 2026 Amp removed the ads because, after the late-2025 releases of Gemini 3 Pro, Opus 4.5 and GPT-5.2 Codex, "ads don't pay for enough frontier tokens to make a difference" ([38], [39]). Paid use is pay-as-you-go without markup; a $10/month student tier was announced in 2026 ([40]).
- **Models.** Multiple frontier models chosen by Amp, historically without a model picker. Closed source.
- **Distinctive.** The only harness that tried advertising as a business model, and the clearest public statement that model price shifts can break a harness business model in months.

### Factory (Droid)

- Closed source. Droid is "the agent-native development platform" across CLI, web, Slack/Teams, Linear/Jira, mobile, VS Code, JetBrains and Zed, with TypeScript and Python SDKs, a GitHub Action for reviews and security scans, and a plugins marketplace ([41]). Any model, BYOK. Factory topped Terminal-Bench in September 2025 at 58.75% when it raised a $50M Series B ([42]); it raised a $150M Series C at a $1.5B valuation led by Khosla on April 16, 2026 ([43], [44]). Autonomy levels and a headless `droid exec` mode are documented at docs.factory.ai (unreachable here; unverified detail).
- **Distinctive.** Sells one agent runtime across the widest surface list of any independent, with the enterprise as the buyer.

### opencode (SST / Anomaly)

- MIT, ~206k stars (2026-09-08), releasing several times a week (v1.18.29 on September 4, 2026) ([45], [46]). Terminal UI, a desktop app in beta for macOS and Windows, an IDE extension, and shareable sessions; two built-in agents (`build`, full access; `plan`, read-only) and a `general` subagent; MCP; plugins; ACP support. Recent releases handle Codex OAuth logins and GitHub Copilot sessions ([46]).
- **Models.** 75+ providers (secondary); the project sells *Zen*, a curated set of coding models (sign-ups reportedly paused in 2026, secondary [47]).
- **Business.** Model reselling via Zen; funding not found (unverified).
- **Distinctive.** The most-starred coding harness in the world and the base of Kilo's CLI; a pure open-source terminal engine that other surfaces embed.

### pi (Earendil Works)

- MIT, ~103k stars (2026-09-08). Created by Mario Zechner (libGDX). The coding agent ships four tools (read, write, edit, bash) and a system prompt of roughly 300 words; there is no built-in MCP, subagent, plan mode, to-do list, permission popup or background shell, each replaced by TypeScript extensions, skills, prompt templates and packages shared via npm or git ([48], [49]). The monorepo packages (`pi-ai` multi-provider API, `pi-agent-core`, `pi-coding-agent`, `pi-tui`, `chord`) document container patterns (Gondolin, Docker, OpenShell) instead of permission prompts ([48]).
- **Ownership.** Earendil (founders Armin Ronacher and Colin Sidoti) acquired pi in April 2026; the repository and npm packages moved to the `earendil-works` scope in May 2026 (secondary, [50]); releases continued through v0.85.1 in September 2026 ([51]).
- **Evidence claim.** A Databricks internal benchmark reportedly found pi had the highest pass rate of any harness tested on a top-tier Anthropic model at markedly lower cost because it sent roughly 3x less context per turn (secondary, [49]; **unverified**).
- **Distinctive.** The strongest evidence that a thin loop plus containers can match thick harnesses, and the engine under OpenClaw.

### Aider

- Apache-2.0, ~49k stars. The last feature release was 0.86.0 on August 9, 2025; a patch (0.86.2) shipped February 12, 2026; the main branch had community commits as late as May 22, 2026; the README still recommends Claude 3.7 Sonnet ([52], [53], [54]). Aider is in maintenance mode. Its ideas (repo map, architect/editor model pairing, git-native edits) were absorbed by everyone else. No business model.

### Warp

- Warp open-sourced its Rust client on April 28, 2026 under a dual MIT / AGPL v3 licence (~65k stars) and describes itself as "an agentic development environment, born out of the terminal" that runs Warp's own agent or Claude Code, Codex, Gemini CLI and others ([55], [56]). *Oz* is the cloud side: containerised agents started by webhooks, cron or by hand, with recorded sessions for audit ([55]). The GitHub README states that OpenAI is the founding sponsor of the open-source project and that agentic workflows are "powered by GPT models" ([56]).
- **Business.** Subscriptions plus credits (secondary). **Distinctive.** A terminal company that now sells an orchestrator and took a lab as sponsor.

### Qodo (review layer, not a coding loop)

- Qodo builds review, testing and governance agents that sit on pull requests, in the IDE and in a CLI; it raised a $70M Series B on March 30, 2026 (total $120M), pitched explicitly as a defence against "software slop from OpenClaw and Claude Code" ([57]). Its open-source PR-Agent (MIT, ~13k stars) is now "a community-maintained legacy project" ([58]).
- **Why it is here.** It is a harness component (verification) sold separately, which is evidence that the loop alone is not where buyers see value.

## Family 3: browser app builders for people who do not code

| Product | Surface and runtime | Models | Business model | Evidence of traction (date) |
|---|---|---|---|---|
| **Lovable** | Browser workspace with live preview, agent mode, hosted backend | Not disclosed in sources reached (unverified) | Subscription and credits; closed | $400M Series C at $13.3B, August 12, 2026; ARR $500M in June 2026 (TechCrunch, Bloomberg, [59], [60]) |
| **Replit Agent 4** | Browser workspace on Replit's cloud; Agent 4 (March 11, 2026) adds parallel tasks, design control and outputs beyond code: mobile apps, presentations, data visualisations ([61], [62]) | Multiple frontier models (unverified) | Subscription plus effort-based billing; closed | $400M at a $9B valuation, March 2026 (secondary, [63]); ARR figures ($240M for 2025, ~$525M mid-2026) are secondary and **unverified** |
| **Bolt (StackBlitz)** | In-browser Node runtime (WebContainers); an MIT open-source `bolt.new` repo (~17k stars) underlies the hosted product ([64]) | Claude by default; a "Claude Agent" became the default agent in August 2026 (secondary, [65]) | Subscription; enterprise via AWS and, since May 2026, Microsoft marketplaces (secondary, [65]) | ~$40M ARR within its first months (2025, secondary [66]) |
| **v0 (Vercel)** | Browser builder that deploys to Vercel; sandboxes | Vercel composite model plus frontier models (unverified for 2026) | Credits; closed | ~$50M annualised by April 2026 (secondary, **unverified** [67]); Vercel's June 17, 2026 Ship event added an agent framework and AI SDK 7 ([68]) |

Anatomy notes for the family: the loop is hidden; tools are the platform's own (file edits, deploy, database); memory is the project; permissions are implicit (the agent can only touch your project on their cloud); the surface is a chat pane next to a live preview. None of them offers a CLI or a desktop app as the primary surface.

## Family 4: personal agents

These are harnesses whose user is a person, not a repository. They run on your machine (or a rented box) and you talk to them through the chat app you already use.

### OpenClaw

- MIT; ~389k stars and ~82k forks (2026-09-08); date-versioned releases every few days (2026.9.3 on September 8, 2026) ([69], [70]). Created by Peter Steinberger; renamed from Clawdbot to Moltbot on January 27, 2026 after an Anthropic trademark request and to OpenClaw on January 30, 2026 ([71], [72]).
- **Anatomy.** The *Gateway* is a local control plane for sessions, tools, events and channel connections; channels include WhatsApp, Telegram, Slack, Discord, Google Chat, Signal and iMessage; skills and plugins are shared through *ClawHub*; companion apps and *nodes* add voice, canvas, camera, screen and device-local actions; sandboxing is optional configuration; "hosted and local model providers" (Claude, Codex, local models) are swappable plugins; "state, memory, and credentials live on your hardware" ([69]). The engine is pi (secondary, [49]).
- **Governance and business.** "Stewarded by the OpenClaw Foundation, an independent 501(c)(3)" with "no paid tier or hosted service" ([69]). Steinberger joined OpenAI on February 15, 2026 to work on personal agents, with Sam Altman saying the project would live in a foundation OpenAI would support ([73], [74]); the foundation was announced July 8, 2026 (secondary, [75]).
- **Model-provider shock.** From April 4, 2026 Anthropic stopped Claude Pro and Max subscribers from routing plan usage through third-party harnesses such as OpenClaw; API keys still worked; Anthropic later reinstated subscription use with a separate "extra usage" charge (VentureBeat, [76], [77]; reinstatement date unverified).
- **Distinctive.** The fastest-growing open-source repository in this landscape, running in a chat app rather than a terminal, and the trigger for both a lab's hiring decision and a lab's billing policy.

### Hermes Agent (Nous Research)

- MIT; ~243k stars (2026-09-08); released February 2026 (secondary, [78]); v0.21.0 "Pantheon" on August 31, 2026 and v0.21.1 on September 7, 2026 ([79], [80]).
- **Anatomy.** "Self-improving": it creates skills from experience and curates its own memory; skills follow the agentskills.io standard; one gateway process serves Telegram, Discord, Slack, WhatsApp, Signal and the CLI; runtimes include local, Docker, SSH, Singularity, Modal, Daytona and Vercel Sandbox, with hibernating serverless environments; a built-in cron scheduler delivers to any platform; a desktop app exists ([79]). Pantheon added *Bot Mode* ("a society of named agents with their own faces and group chats"), `hermes peer` agent-to-agent messaging, persistent memory for cron agents, live steering of subagents, an MCP command centre for 20+ servers, and desktop browser control; v0.20 "Herald" (August 3, 2026) added streaming voice with barge-in, on-device wake words and an A2A v1.0 endpoint ([80]).
- **Models and business.** Nous Portal, OpenRouter, OpenAI, Anthropic or any endpoint; Nous Portal is a subscription launched April 27, 2026 bundling 300+ models (secondary, [78]).
- **Distinctive.** The most complete open-source implementation of "one agent, every surface, every runtime", and the one that treats memory and skill creation as the core loop rather than an add-on.

### NanoClaw

- MIT; ~31k stars (2026-09-08); open-sourced January 31, 2026 (secondary). "A lightweight alternative to OpenClaw that runs in containers for security": one process and a handful of files against OpenClaw's "nearly half a million lines of code"; runs "directly on Anthropic's Agents SDK"; Docker isolation per agent; per-agent memory; scheduled tasks; on-demand channel skills (WhatsApp, Telegram, Discord, Slack, Teams, iMessage, Matrix, Google Chat, Webex, Linear, GitHub, WeChat, email); credentials through a vault; "Want different behavior? Modify the code" ([81]).
- **Model dependence.** Anthropic only, by construction. **Distinctive.** Permissions by container boundary and customisation by rewriting the source, with no configuration system at all.

## Family 5: tools that orchestrate other harnesses

| Tool | What it does | Agents wrapped | Licence, stars (2026-09-08) | Status and money |
|---|---|---|---|---|
| **Conductor** | macOS app; each agent gets an isolated worktree; review and merge | Claude Code, Codex | Closed | YC S24 (Charlie Holtz, Jackson de Campos); reported $22M Series A from Spark and Matrix, March 2026 (secondary, [82]) |
| **Superset** | "Agentic IDE to orchestrate 100+ coding agents in parallel", one worktree, branch, terminal and environment each; diff review | 20+ including Claude Code, Cursor Agent, Copilot, Codex, Gemini CLI, Mistral Vibe, any CLI agent | Elastic License 2.0 (source-available), ~14k | YC-backed; desktop "free forever", optional paid cloud; macOS and experimental Linux ([83]) |
| **Vibe Kanban (Bloop)** | Kanban board that runs agents in workspaces, inline diff comments, app preview, MCP server | Claude Code, Codex, Gemini CLI, Copilot, Amp, Cursor, opencode, Droid, Qwen Code | Apache-2.0, Rust, ~28k | Bloop shut down (announced April 10, 2026; "vast majority" were free users and no business model excited the team, per its post); last release v0.1.44 on April 24, 2026; no releases since ([84], [85], [86]) |
| **Terragon** | Cloud orchestrator: task in via Slack or GitHub, agent in a sandbox, PR out | Claude Code, Codex, Amp, Gemini | Apache-2.0 snapshot | Shut down; snapshot notice dated January 16, 2026, service end reported as February 9, 2026 (secondary) ([87]) |
| **Claude Squad** | tmux plus worktrees TUI; background "yolo" mode | Claude Code, Codex, Gemini, Aider | AGPL-3.0, ~8k | Community project ([88]) |
| **Symphony (OpenAI)** | Spec plus Elixir reference: watches a Linear board, spawns one isolated run per issue, demands "proof of work" (CI status, review feedback, walkthrough videos), lands PRs | Codex | Apache-2.0, ~27k; "low-key engineering preview" | First commits March 27, 2026, announced April 27-28, 2026; OpenAI said engineers could manage only three to five sessions before context switching hurt, and some teams saw a 500% rise in landed PRs (secondary) ([89], [90], [91]) |

A sketch of what every orchestrator does, stripped to the bone:

```python
# one worker per ticket, each in its own worktree, each a plain CLI harness
for ticket in board.open_issues():
    wt = git.worktree_add(f"agent/{ticket.id}")          # isolated copy of the repo
    proc = spawn(["claude", "-p", ticket.prompt, "--cwd", wt, "--permission-mode", "auto"])
    runs[ticket.id] = proc                                # keep a handle for the review UI
# the human reviews diffs, merges, or sends the ticket back with comments
```

The insight is that the CLI harness is the unit of orchestration. Nobody wraps an IDE.

## Comparison table: the seven parts, model dependence, licence, business

Abbreviations: Loop = how far it runs unattended; Ctx = context and memory; Perm = permissions and safety; RT = runtime; Orch = orchestration. "BYOK" means any provider with your key.

| Harness | Loop | Tools | Ctx and memory | Perm | RT | Surface | Orch | Models | Open source | Business |
|---|---|---|---|---|---|---|---|---|---|---|
| Cursor | Long-running local and cloud agents; Automations | Editor, shell, browser, MCP | Codebase index, rules, memories | Cloud isolation; self-hosted machines (Sep 2026) | Local, cloud VM, SSH, self-hosted | IDE, CLI, web, Agents window | Parallel agents in worktrees; SDK | Frontier plus own Composer (on Kimi K2.5) | No | Subscriptions |
| Cognition | Fully autonomous cloud (Devin); local (Devin Local) | Shell, browser, editor, MCP | Repo knowledge, Codemaps | Cloud VM isolation | Cloud, local IDE | Web, Slack, IDE (Devin Desktop) | Command Center kanban; ACP host | Frontier plus own SWE-1.6 | No | Subscriptions, enterprise |
| OpenHands | Autonomous; automations | File, terminal, task tracker, MCP | Repo memory, condenser | Confirmation policies, Docker | Local, Docker, K8s, cloud | Web canvas, CLI, GitHub, Slack | Delegation, multi-agent SDK | BYOK (LiteLLM) | MIT | Free, cloud credits, enterprise |
| Amp | Interactive with subagents | Shell, editor, MCP | Threads, AGENTS.md | Prompt-level | Local | CLI, VS Code | Subagents | Amp-chosen frontier | No | Pay-as-you-go, no markup |
| Cline | Plan/Act; scheduled runs | Editor, shell, browser, MCP | Checkpoints, rules | Approve or auto-approve | Local; Hub background process | VS Code, JetBrains, CLI, desktop | Multi-agent teams | BYOK | Apache-2.0 | Free plus teams/enterprise |
| Kilo | Interactive; `--auto`; always-on KiloClaw | Editor, shell, browser, MCP | Memory bank, rules | Mode-based approvals | Local, cloud | VS Code, JetBrains, CLI, web | Agent Manager, worktrees | 500+ via gateway | MIT | Zero-markup gateway, cloud |
| Aider | Turn-based | Edit, git, shell | Repo map | Git as undo | Local | CLI | None | BYOK | Apache-2.0 | None |
| Factory Droid | Interactive to headless | Shell, editor, MCP, plugins | AGENTS.md, specs | Autonomy levels (per docs) | Local, cloud, CI | CLI, IDE, Zed, web, Slack/Teams, Linear/Jira, mobile | Subagents; SDKs | BYOK | No | Enterprise |
| Warp | Interactive; Oz background | Shell-native, MCP | Codebase index | Cloud containers, audit | Local, Oz cloud | Terminal app | Oz cloud agents; wraps CLIs | Multi-model; GPT sponsor | MIT / AGPL v3 | Subscriptions, credits |
| Zed | Interactive; parallel agents | Editor, MCP | Editor context | Editor-level | Local | IDE | ACP host | BYOK, Zed-billed | GPL-3.0 | Subscriptions |
| opencode | Interactive; plan/build agents | Shell, editor, MCP, plugins | AGENTS.md, sessions | Per-tool permission config | Local; desktop beta | TUI, desktop, IDE, web share | Subagents; ACP | 75+ providers, Zen | MIT | Zen model resale |
| pi | Interactive | read, write, edit, bash | Session files; skills | None built in; containers | Local, containers | TUI | Via extensions | BYOK (pi-ai) | MIT | None (Earendil) |
| Augment Intent | Coordinator to verifier | Editor, shell, MCP | Context Engine, living spec | Worktree isolation | Local (macOS) | Desktop workspace, CLI | Three-agent pipeline | Frontier | No (CLI source public) | Credits |
| Qodo | Review on PR events | Git platforms, IDE, CLI | Repo context | Governance rules | Cloud, CI | PR, IDE, CLI | Multi-agent review | BYOK/Qodo | PR-Agent MIT; platform no | Enterprise |
| Replit | Autonomous with checkpoints | Platform tools, deploy, DB | Project | Platform-bounded | Replit cloud | Browser | Parallel tasks (Agent 4) | Frontier (undisclosed) | No | Subscription, effort billing |
| Lovable | Autonomous | Platform tools, hosted backend | Project | Platform-bounded | Cloud | Browser | Agent mode | Undisclosed | No | Subscription, credits |
| Bolt | Autonomous | In-browser Node, deploy | Project | Browser sandbox | WebContainers | Browser | None | Claude | Base repo MIT | Subscription, enterprise |
| v0 | Autonomous | UI generation, sandboxes, deploy | Project | Sandbox | Vercel cloud | Browser | None | Composite plus frontier | No | Credits |
| OpenClaw | Always-on; cron; heartbeats | Shell, browser, files, email, calendar, skills | Local memory files | Optional sandbox | Local gateway, own hardware | WhatsApp, Telegram, Slack, Discord, Signal, iMessage, voice, canvas | Multi-agent sessions | Swappable (Claude, Codex, local) | MIT, 501(c)(3) | None |
| Hermes | Always-on; cron with memory | Web, browser, vision, TTS, MCP, skills | Agent-curated memory; self-made skills | Sandboxed runtimes | Local, Docker, Modal, Daytona, SSH, Vercel Sandbox | Telegram, Discord, Slack, WhatsApp, Signal, CLI, desktop, voice | Bot Mode, peer messaging, subagents, A2A | Nous Portal, OpenRouter, OpenAI, Anthropic, own | MIT | Nous Portal subscription |
| NanoClaw | Always-on; scheduled | Claude Code toolset, MCP, skills | Per-agent memory | Container per agent | Docker | Chat apps (many) | Per-agent groups | Anthropic only | MIT | None |

## What is converging

1. **The agent manager is the new default view for developers.** Cursor 3, Devin Desktop, Kilo, Augment Intent, Zed 1.0, Warp, Cline's desktop Hub and Superset all present a list of running agents with worktree isolation and diff review. This shape did not exist as a product in January 2025.
2. **One runtime, many surfaces.** Factory (eight surfaces), Cline (five), Kilo (five), Hermes (six chat platforms plus CLI and desktop) and Cursor's SDK all treat the surface as a thin adapter over one agent runtime. The labs' pattern (Task 3) is mirrored exactly.
3. **CLI harness as engine, protocol as plug.** ACP (Zed, JetBrains, Cognition, opencode) and MCP are how surfaces and engines connect; orchestrators spawn CLIs. This is why the terminal harnesses with no GUI (opencode, pi) have the largest developer star counts.
4. **Model-agnosticism plus a house model at the top.** Everyone supports BYOK; the two best-funded independents (Cursor, Cognition) also train models to control cost and speed, and Cursor's Composer 2 shows "own model" usually means "post-trained open-weight base".
5. **Background execution and scheduling everywhere.** Warp Oz, Cursor Automations, Cline scheduled agents, Hermes cron, NanoClaw scheduled tasks, OpenHands automations, Symphony's board polling. The loop is leaving the foreground.
6. **Chat apps as the surface for personal agents and for cloud coding agents.** OpenClaw, Hermes, NanoClaw, Roomote, Terragon and Factory all took the "message it" route.
7. **Standard files over proprietary config.** AGENTS.md, skills (agentskills.io compatibility in Hermes), MCP servers, and plugin marketplaces (Cline, Kilo, Factory, ClawHub).

## What is still differentiated

- **Permissions philosophy.** Approval dialogs (Cline, Kilo, opencode's per-tool policy) versus container boundaries with no prompts (pi, NanoClaw) versus platform-bounded (Lovable, Replit). There is no consensus, and the personal agents, which have the widest blast radius (email, shell, browser), have the weakest default sandboxing (OpenClaw's is optional).
- **Business model.** Subscription (Cursor, Cognition, Warp), zero-markup gateway (Kilo, Amp), model resale (opencode Zen, Nous Portal), enterprise (Factory, Qodo, Augment), source-available desktop plus paid cloud (Superset), non-profit (OpenClaw), and nothing at all (pi, Aider). Ads were tried and dropped.
- **Thick versus thin.** OpenClaw's roughly half-million lines versus NanoClaw's handful of files; Cursor's SDK-exposed harness versus pi's four tools. Both ends have six-figure star counts.
- **Who is the user.** Repositories (coding harnesses), businesses without developers (Lovable, Replit), or a single person's life (OpenClaw, Hermes). The parts that differ most across these are context/memory (project versus person) and surface (IDE versus browser versus chat).
- **Verification.** Symphony's "proof of work", Augment's verifier agent and Qodo's review agents are early, and most harnesses still rely on a human reading a diff.

## What this means for the thesis

**Supports.**

- Among independents, the surfaces with the most users are not CLIs or desktop apps. The three most-starred harnesses (OpenClaw, Hermes, opencode) are, respectively, a chat-app agent, a chat-app agent, and a terminal engine that other surfaces embed. The independents with the largest revenue disclosed (Lovable, Replit) are browser apps for non-developers.
- Vendors that started in the IDE are moving the primary surface above it: Devin Desktop is "an agent manager wrapped in an IDE", Augment calls Intent "post-IDE", Roo Code killed its IDE extension outright, Cursor 3 put the Agents window beside the editor. The bottleneck they are attacking is human supervision of many agents, which is a harness problem, not a model problem; OpenAI's own Symphony post says engineers could handle only three to five sessions.
- Harness design measurably changes outcomes with the same model: pi's context-per-turn claim (unverified) and Composer 2's cost story both argue that the harness, not the model, sets cost and throughput once models are good.

**Contradicts.**

- The CLI did not lose; it became the substrate. Every orchestrator wraps a CLI, ACP standardises "agent as a subprocess", and Factory, Amp, Cline and Kilo all ship a CLI as the reference surface. A thesis that says "not a CLI" must say "not a CLI *as the surface*" and accept that the CLI is the default *runtime interface*.
- Being the harness is not the same as capturing value. Within six months, Terragon, Vibe Kanban, Roo Code's extension and Continue were gone, and Amp's ads failed. The survivors either own a model (Cursor, Cognition), own an enterprise workflow (Factory, Qodo), or own the non-developer builder surface (Lovable, Replit). "The harness is the bottleneck" was true and still did not save the generic harness companies.
- Model providers set the rules. Anthropic's April 4, 2026 policy on subscriptions cut off OpenClaw-style harnesses for a period; Cursor's "own model" turned out to depend on Moonshot's licence; Warp's open-source project is sponsored by OpenAI. Independence is partial.
- Better models made harnesses *thinner* in the loop and permission layers (pi, NanoClaw) while making them *thicker* in orchestration, runtime and surface. The thesis should say which parts, not "the harness".

**Nuance.** The personal-agent wave is the clearest existence proof for "reimagine the default harness for non-technical people": OpenClaw and Hermes reached hundreds of thousands of stars with a surface that is a chat app and a runtime that is a local gateway. But they still require installing Node, editing config and managing API keys; their users are developers acting as their own IT department. The non-technical builders (Lovable, Replit) solved installation by putting the runtime in the cloud and the surface in the browser, but their agents build apps rather than run a person's work. Nobody in this landscape has combined the two.

## Open questions and unverified claims

1. The reported SpaceX acquisition of Cursor for $60B (June 2026) appears in one secondary source only; unverified.
2. Cline's $32M raise from Emergence Capital, Kilo's $8M seed, Conductor's $22M Series A, Augment's funding totals and Replit's ARR come from secondary write-ups; none was confirmed against a primary source here.
3. The exact date and mechanics of Anthropic's reinstatement of third-party harness use on Claude subscriptions ("extra usage" billing) are unverified; only the April 4, 2026 restriction and the fact of a later reversal were corroborated.
4. The Databricks benchmark favouring pi is reported second-hand (Pragmatic Engineer coverage); no primary publication was found.
5. Which models Lovable, Replit and v0 run today is undisclosed in the sources reached.
6. Whether Roomote (Roo Code's successor) has shipped since May 2026 is unknown.
7. Factory Droid's autonomy levels, `droid exec` and sandboxing are documented on docs.factory.ai, which was unreachable from this environment.
8. Cursor's cloud-agent permission model and Composer 2.5's base model were not verifiable against cursor.com (blocked); changelog facts come from search-engine excerpts of the changelog.
9. Kilo, Cline and pi release dates on GitHub were read without a year label; September 2026 is inferred from content (GPT-6 support, current version numbers).
10. Research limits: the web-search budget was exhausted after roughly 50 searches, and many vendor domains (cursor.com, devin.ai, zed.dev, ampcode.com, warp.dev, lovable.dev, replit.com, factory.ai, Wikipedia) were blocked by the network proxy, so GitHub READMEs and release pages carried most primary verification.

## Sources

1. Cursor changelog, https://cursor.com/changelog (entries May, August and September 2026; read via search excerpts).
2. Cursor, "Custom stores, custom tools, and auto-review for the Cursor SDK", https://cursor.com/changelog/sdk-updates-jun-2026 (June 2026).
3. BuildFastWithAI, "Cursor Cloud Agents & Dev Environments: Complete 2026 Guide", https://www.buildfastwithai.com/blogs/cursor-cloud-agents-development-environments-2026 (2026).
4. Digital Applied, "Cursor 3: Agents Window, Cloud Agents, and What Changed", https://www.digitalapplied.com/blog/cursor-3-agents-window-complete-guide (2026).
5. ofox.ai, "Cursor Composer 2.5", https://ofox.ai/blog/cursor-composer-2-5-setup-guide-2026/ (May 2026).
6. Codersera, "Cursor 3 + Composer 2 walkthrough", https://codersera.com/blog/cursor-3-composer-2-walkthrough-2026/ (2026).
7. DataCamp, "What Is Cursor 3?", https://www.datacamp.com/blog/cursor-3 (April 2026).
8. DEV Community, "Cursor 3 'Glass' Replaced Composer with an Agents Window", https://dev.to/gabrielanhaia/cursor-3-glass-replaced-composer-with-an-agents-window-1pcg (April 2026).
9. Medium (Ewan Mak), "Cursor 3 Ships an Agent-First Interface", https://medium.com/@tentenco/cursor-3-ships-an-agent-first-interface-heres-what-it-actually-changes-1f2bf8f383e2 (April 2026).
10. devtoolpicks, "Cursor 3 Review: Agents Window, Pricing", https://devtoolpicks.com/blog/cursor-3-agents-window-review-2026 (2026).
11. TechCrunch, "Cursor admits its new coding model was built on top of Moonshot AI's Kimi", https://techcrunch.com/2026/03/22/cursor-admits-its-new-coding-model-was-built-on-top-of-moonshot-ais-kimi/ (March 22, 2026).
12. The New Stack, "Cursor quietly acquires Continue", https://thenewstack.io/cursor-acquires-continue-coding/ (June 2026).
13. Devin blog, "Windsurf is now Devin Desktop", https://devin.ai/blog/windsurf-is-now-devin-desktop (June 2, 2026).
14. Digital Applied, "Windsurf Is Now Devin Desktop: What Users Should Do", https://www.digitalapplied.com/blog/windsurf-becomes-devin-desktop-ide-migration-2026 (June 2026).
15. TestingCatalog, "Windsurf 2.0 adds Devin and Agent Command Center", https://www.testingcatalog.com/windsurf-2-0-adds-devin-and-agent-command-center/ (2026).
16. apidog, "Devin vs Cursor in 2026: Windsurf is now Devin Desktop", https://apidog.com/blog/whats-new-in-devin-2026/ (2026).
17. TechCrunch, "AI coding startup Cognition raises $1B at $25B pre-money valuation", https://techcrunch.com/2026/05/27/ai-coding-startup-cognition-raises-1b-at-25b-pre-money-valuation/ (May 27, 2026).
18. The Next Web, "Cognition just raised $1 billion at a $26 billion valuation", https://thenextweb.com/news/cognition-just-raised-1-billion-at-a-26-billion-valuation-and-90-of-its-own-code-is-written-by-its-ai (May 2026).
19. TechCrunch, "AI coding startup Cognition reportedly already in talks to raise at $40B valuation", https://techcrunch.com/2026/08/12/ai-coding-startup-cognition-reportedly-already-in-talks-to-raise-at-40b-valuation/ (August 12, 2026).
20. GitHub, zed-industries/zed, https://github.com/zed-industries/zed (accessed September 2026).
21. Danilo Falcão, "Zed 1.0", https://falcao.org/posts/zed-1-0/ (April/May 2026); Builder.io, "Is Zed ready for AI power users in 2026?", https://www.builder.io/blog/zed-ai-2026 (2026).
22. GitHub, agentclientprotocol/agent-client-protocol, https://github.com/agentclientprotocol/agent-client-protocol (accessed September 2026).
23. Zed blog, "The ACP Registry is Live", https://zed.dev/blog/acp-registry (January 28, 2026).
24. Morph, "Agent Client Protocol (ACP) Explained", https://www.morphllm.com/agent-client-protocol (2026); Groundy, "ACP Registry Is Live", https://groundy.com/articles/acp-registry-is-live-zed-and-jetbrains-just-did-for-ai-agents-what-lsp-did/ (2026).
25. GitHub, Kilo-Org/kilocode README, https://github.com/Kilo-Org/kilocode (accessed September 2026).
26. GitHub, Kilo-Org/kilocode releases, https://github.com/Kilo-Org/kilocode/releases (September 2026).
27. PrimeAIcenter, "Kilo Code Review 2026", https://primeaicenter.com/kilo-code-review/ (2026).
28. GitHub, cline/cline README, https://github.com/cline/cline (accessed September 2026).
29. GitHub, cline/cline releases, https://github.com/cline/cline/releases (September 2026).
30. GitHub, RooCodeInc/Roo-Code README (archived), https://github.com/RooCodeInc/Roo-Code (May 2026).
31. The New Stack, "Roo Code pivots to cloud-based agent, says IDEs aren't the future of coding", https://thenewstack.io/roo-code-cloud-ides-ai-coding/ (April/May 2026).
32. Open Orchestrators, "Augment Code launches Intent", https://openorchestrators.org/news/augment-code-intent-launch/ (February 2026); Augment Code, "Intent: A workspace for agent orchestration", https://www.augmentcode.com/blog/intent-a-workspace-for-agent-orchestration (2026).
33. Ry Walker Research, "Augment Context Engine", https://rywalker.com/research/augment-context-engine (2026).
34. GitHub, augmentcode/auggie, https://github.com/augmentcode/auggie (accessed September 2026).
35. GitHub, continuedev/continue (read-only), https://github.com/continuedev/continue (accessed September 2026).
36. Hacker News, "Amp, Inc. – Amp is spinning out of Sourcegraph", https://news.ycombinator.com/item?id=46124649 (December 2025).
37. Wikipedia, "Sourcegraph", https://en.wikipedia.org/wiki/Sourcegraph (accessed September 2026).
38. Amp, "Amp Free", https://ampcode.com/news/amp-free (October 2025); Tessl, "Amp's new business model? Ad-supported AI coding", https://tessl.io/blog/amp-s-new-business-model-ad-supported-ai-coding/ (October 2025).
39. Amp, "Amp Free Is Ad-Free", https://ampcode.com/news/amp-free-is-ad-free (March 2026).
40. Quinn Slack on X, student pricing, https://x.com/sqs/status/2089654397152481467 (2026).
41. GitHub, Factory-AI/factory README, https://github.com/Factory-AI/factory (accessed September 2026).
42. Business Wire, "Factory Unleashes the Droids, Raises $50 Million Series B", https://www.businesswire.com/news/home/20250925993478/en/ (September 25, 2025).
43. Factory, "Factory raises $150M Series C", https://factory.ai/news/series-c (April 2026).
44. Crowdfund Insider, "Factory Raises $150m Series C Led By Khosla At $1.5 Billion Valuation", https://www.crowdfundinsider.com/2026/04/274335-ai-infrastructure-startup-factory-raises-150m-series-c-led-by-khosla-at-1-5-billion-valuation/ (April 2026).
45. GitHub, sst/opencode README, https://github.com/sst/opencode (accessed September 2026).
46. GitHub, sst/opencode releases, https://github.com/sst/opencode/releases (September 2026).
47. OpenCode, "OpenCode Zen", https://opencode.ai/zen; Developers Digest, "OpenCode Developer Guide 2026", https://www.developersdigest.tech/blog/opencode-developer-guide-2026 (2026).
48. GitHub, earendil-works/pi-mono (formerly badlogic/pi-mono) README, https://github.com/badlogic/pi-mono (accessed September 2026).
49. Pragmatic Engineer, "Building Pi, and what makes self-modifying software so fascinating", https://newsletter.pragmaticengineer.com/p/building-pi-and-what-makes-self-modifying (April 2026); Pasquale Pillitteri, "Pi Coding Agent: The Strength Is in What They Didn't Build", https://pasqualepillitteri.it/en/news/5649/pi-coding-agent-what-they-didnt-build (2026).
50. Wikipedia, "Pi (AI agent)", https://en.wikipedia.org/wiki/Pi_(AI_agent) (accessed via search, September 2026); Agent Wars, "Mario Zechner Joins Earendil, Brings Coding Agent pi", https://agent-wars.com/news/2026-04-08-zechner-pi-earendil (April 8, 2026).
51. GitHub, earendil-works/pi-mono releases, https://github.com/earendil-works/pi-mono/releases (September 2026).
52. GitHub, Aider-AI/aider, https://github.com/Aider-AI/aider (accessed September 2026).
53. PyPI, aider-chat release history, https://pypi.org/project/aider-chat/ (accessed September 2026).
54. GitHub, Aider-AI/aider commits, https://github.com/Aider-AI/aider/commits/main (accessed September 2026).
55. Warp newsroom, "Warp Open-Sources Its Agentic Development Environment", https://www.warp.dev/newsroom/2026/4/28/warp-open-sources-its-agentic-development-environment (April 28, 2026).
56. GitHub, warpdotdev/warp, https://github.com/warpdotdev/warp (accessed September 2026).
57. GlobeNewswire, "Qodo Raises $70M to Accelerate Fight Against Software Slop From OpenClaw and Claude Code", https://www.globenewswire.com/news-release/2026/03/30/3264740/0/en/ (March 30, 2026); TechCrunch, "Qodo bets on code verification as AI coding scales", https://www.techcrunch.com/2026/03/30/qodo-bets-on-code-verification-as-ai-coding-scales-raises-70m/ (March 30, 2026).
58. GitHub, qodo-ai/pr-agent, https://github.com/qodo-ai/pr-agent (accessed September 2026).
59. TechCrunch, "Lovable confirms new $13.3B valuation, raises another $400M", https://techcrunch.com/2026/08/12/lovable-confirms-new-13-3b-valuation-raises-another-400m/ (August 12, 2026).
60. Bloomberg, "AI Coding Startup Lovable Raises $400 Million at $13.3 Billion Valuation", https://www.bloomberg.com/news/articles/2026-08-12/ai-coding-startup-lovable-raises-400-million-at-13-3-billion-valuation (August 12, 2026); Lovable, "We just raised $400M in Series C", https://lovable.dev/blog/series-c (August 2026).
61. Replit, "Live from Replit HQ: Agent 4 Launch Pt. 1", https://replit.com/blog/live-from-hq-agent4-launch-pt1 (March 2026).
62. Latent Space, "Replit Agent 4: The Knowledge Work Agent", https://www.latent.space/p/ainews-replit-agent-4-the-knowledge (March 2026).
63. Sacra, "Replit revenue, valuation & funding", https://sacra.com/c/replit/ (2026); tech-insider.org, "Replit vs Lovable vs Bolt.new", https://tech-insider.org/replit-vs-lovable-vs-bolt-new-2026/ (2026).
64. GitHub, stackblitz/bolt.new, https://github.com/stackblitz/bolt.new (accessed September 2026).
65. AIUnpacking, "Bolt.new Review 2026: After the Microsoft Deal", https://aiunpacking.com/review/bolt-new/ (2026); Bolt status page, https://status.bolt.new/incidents/01KVQVZTAN12JQWNAF0BRYSC2M (August 2026).
66. Sacra, "Bolt.new revenue, funding & news", https://sacra.com/c/bolt-new/ (2025-2026).
67. makeanapplike, "Vercel v0 in 2026: AI App Builders Hit Production", https://makeanapplike.com/news/launches/vercel-v0-production-ready-2026 (2026).
68. Business Wire via Morningstar, "Vercel Brings New Agent Framework, Full-Stack Capabilities, and Enterprise Controls", https://www.morningstar.com/news/business-wire/20260617093685/ (June 17, 2026); Vercel, "Vercel Ship 2026 recap", https://vercel.com/blog/vercel-ship-2026-recap (June 2026).
69. GitHub, openclaw/openclaw README, https://github.com/openclaw/openclaw (accessed September 8, 2026).
70. GitHub, openclaw/openclaw releases, https://github.com/openclaw/openclaw/releases (September 2026).
71. CNBC, "From Clawdbot to Moltbot to OpenClaw", https://www.cnbc.com/2026/02/02/openclaw-open-source-ai-agent-rise-controversy-clawdbot-moltbot-moltbook.html (February 2, 2026).
72. Forbes, "Moltbot Gets Another New Name, OpenClaw", https://www.forbes.com/sites/ronschmelzer/2026/01/30/moltbot-molts-again-and-becomes-openclaw-pushback-and-concerns-grow/ (January 30, 2026).
73. TechCrunch, "OpenClaw creator Peter Steinberger joins OpenAI", https://techcrunch.com/2026/02/15/openclaw-creator-peter-steinberger-joins-openai/ (February 15, 2026).
74. CNBC, "OpenClaw creator Peter Steinberger joining OpenAI, Altman says", https://www.cnbc.com/2026/02/15/openclaw-creator-peter-steinberger-joining-openai-altman-says.html (February 15, 2026); Peter Steinberger, "OpenClaw, OpenAI and the future", https://steipete.me/posts/2026/openclaw (February 2026).
75. The New Stack, "'The Switzerland of AI': OpenClaw becomes a non-profit foundation", https://thenewstack.io/openclaw-foundation-nonprofit-status/ (July 2026); explainx.ai, "OpenClaw Foundation: 501(c)(3) & Partners", https://explainx.ai/blog/openclaw-foundation-501c3-nonprofit-july-2026 (July 2026).
76. VentureBeat, "Anthropic cuts off the ability to use Claude subscriptions with OpenClaw and third-party AI agents", https://venturebeat.com/technology/anthropic-cuts-off-the-ability-to-use-claude-subscriptions-with-openclaw-and (April 2026); The Next Web, "Anthropic blocks OpenClaw from Claude subscriptions in cost crackdown", https://thenextweb.com/news/anthropic-openclaw-claude-subscription-ban-cost (April 2026).
77. VentureBeat, "Anthropic reinstates OpenClaw and third-party agent usage on Claude subscriptions — with a catch", https://venturebeat.com/technology/anthropic-reinstates-openclaw-and-third-party-agent-usage-on-claude-subscriptions-with-a-catch (2026).
78. Medium (Ewan Mak), "Hermes Agent Desktop App", https://medium.com/@tentenco/hermes-agent-desktop-app-everything-you-need-to-know-about-nous-researchs-self-improving-ai-agent-3cb59bd31e5f (2026); Nous Research, Hermes Agent site, https://hermes-agent.nousresearch.com/ (2026).
79. GitHub, NousResearch/hermes-agent README, https://github.com/NousResearch/hermes-agent (accessed September 2026).
80. GitHub, NousResearch/hermes-agent releases, https://github.com/NousResearch/hermes-agent/releases (August-September 2026).
81. GitHub, nanocoai/nanoclaw, https://github.com/nanocoai/nanoclaw (accessed September 2026).
82. Ry Walker Research, "Conductor", https://rywalker.com/research/conductor (2026); Charlie Holtz on X, Conductor launch, https://x.com/charliebholtz/status/1945870105109246401 (July 2025); Y Combinator, Conductor, https://ycombinator.com/companies/conductor.
83. GitHub, superset-sh/superset, https://github.com/superset-sh/superset (accessed September 2026); Superset, https://superset.sh/.
84. GitHub, BloopAI/vibe-kanban README, https://github.com/BloopAI/vibe-kanban (accessed September 2026).
85. Vibe Kanban, "Goodbye bloop", https://www.vibekanban.com/blog/shutdown (April 10, 2026).
86. GitHub, BloopAI/vibe-kanban releases, https://github.com/BloopAI/vibe-kanban/releases (April 2026).
87. GitHub, terragon-labs/terragon-oss, https://github.com/terragon-labs/terragon-oss (snapshot notice January 16, 2026).
88. GitHub, smtg-ai/claude-squad, https://github.com/smtg-ai/claude-squad (accessed September 2026).
89. GitHub, openai/symphony, https://github.com/openai/symphony (accessed September 2026).
90. OpenAI, "An open-source spec for Codex orchestration: Symphony", https://openai.com/index/open-source-codex-orchestration-symphony/ (April 2026).
91. Help Net Security, "OpenAI releases Symphony to automate Codex work through Linear", https://www.helpnetsecurity.com/2026/04/28/openai-symphony-codex-orchestration-linear/ (April 28, 2026); InfoWorld, "OpenAI's Symphony spec pushes coding agents from prompts to orchestration", https://www.infoworld.com/article/4164173/ (April 2026).
92. GitHub, OpenHands/OpenHands README, https://github.com/OpenHands/OpenHands (accessed September 2026).
93. GitHub, OpenHands/software-agent-sdk, https://github.com/OpenHands/software-agent-sdk (accessed September 2026).
94. Tracxn / Yahoo Finance, All Hands AI funding ($5M seed September 2024; $18.8M Series A November 2025), https://finance.yahoo.com/news/hands-ai-raises-5m-build-161415119.html and https://tracxn.com/d/companies/all-hands-ai/__oIikiRbTowteZcN-BKCkIOUP9EBluSMLuATTpkeefbc (accessed September 2026).
