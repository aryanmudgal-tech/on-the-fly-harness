# Developer harness matrix: labs and independents side by side

*Research program: agent harnesses. Written 2026-09-08. Status: verified with notes.*

## What this document answers

- One matrix of the eight first-party harness families and twenty-plus independent harnesses across the seven anatomy parts, surfaces offered, extensibility, pricing and open-source status, built only from the two verified profiles [labs] and [independent].
- What has converged, what is still contested, and which gaps nobody fills.
- Which patterns transfer to non-technical users, and where the two profiles agree and disagree about "one runtime, many surfaces" and about whether an independent harness can be the default.

## TL;DR

- Seven of the eight convergence candidates hold, with the row-level exceptions listed under "What every harness has converged on": instruction files, MCP, plan modes, subagents, background or cloud runs, git worktrees and scheduled runs appear in nearly every developer row. The eighth, classifier-gated auto mode, is one product, Claude Code [labs, Anthropic]. Permissions are the least converged part.
- Both profiles find the same engine shape: a CLI-shaped agent process behind thin surfaces. The labs profile adds that feature sets fork at the runtime seam; Anthropic's cloud sessions drop LSP, plugins and `bypassPermissions` [labs, Test].
- Openness moves in opposite directions. The labs are closing engines (Antigravity CLI, Kiro CLI, Copilot CLI licence) while the most-adopted independents are MIT or Apache; but the best-funded independents (Cursor, Cognition, Lovable, Replit, Factory) are closed [labs, TL;DR; independent, TL;DR and Comparison table].
- Pricing has no consensus: at least nine models are in use, ads were dropped within five months, and per-session-hour billing (Managed Agents, $0.08) is the only new one still standing [labs, Anthropic; independent, What is still differentiated].
- The surfaces with the most users among independents, chat apps and browsers, are the ones the labs have not claimed, yet nobody offers a general agent for non-technical people that runs in the cloud and is reached from a chat app [independent, Nuance; labs, Nuance].
- On the thesis, the profiles agree that "not a CLI" is true of surfaces and false of engines. They disagree in emphasis on whether an independent can be the default: [labs] says platform risk makes it a poor bet; [independent] says the harness businesses that survived 2026 own a model, an enterprise workflow or a non-developer surface.

## How to read the matrix

Analogy first: each row is a car's spec sheet. The loop is the engine control unit, tools are the attachments, context and memory are the fuel tank and trip log, permissions are the brakes, runtime is the garage, surface is the dashboard, orchestration is the fleet dispatcher. Precisely: the seven parts follow the program's definitions, and "Surfaces offered" lists the concrete fronts. Worktree, subagent, MCP and ACP are defined in [independent, A short glossary].

Cell conventions: "sec." means the profile relied on secondary sources; "(unverified)" carries the profile's own label; "n/s" means neither profile states it. First-party rows draw on the vendor sections and comparison table of [labs]; independent rows on the family sections and comparison table of [independent]. Corrections in each profile's verification notes override its body text and are reflected here.

### Table 1: loop, tools, context and memory, permissions and safety

| Harness | Loop | Tools | Context, memory | Permissions, safety |
|---|---|---|---|---|
| Claude Code family | Plan then act; `-p` headless; steer on any surface | File, shell, web, MCP, Chrome, LSP (plugins), computer use (preview) | CLAUDE.md; auto memory; auto-compact; 1M context; Managed Agents memory, "dreams" | 6 modes; classifier auto mode with fixed block list; Seatbelt/bubblewrap sandbox; cloud VM plus credential proxy |
| Codex family | TUI; `codex exec`; cloud tasks; app automations | Shell, file, MCP, skills, plugins, computer use, browser | AGENTS.md; memory preview | 4 approval policies (default `on-request`); 4 sandbox policies; bubblewrap/seccomp, Seatbelt, Windows service |
| Antigravity / Gemini CLI | Plan; subagents | MCP, plugins | GEMINI.md-style files, hooks (2025 docs, not re-verified) | Prompts plus sandbox (not re-verified) |
| GitHub Copilot | Cloud agent issue to PR; CLI plan/execute; Autopilot (experimental) | MCP (GitHub server bundled), skills | `.github/agents` profiles; repo instructions; session restore | PR review plus firewalled runner (2025 docs); CLI preview-before-execute; model policy |
| Amazon Kiro | Spec-driven (requirements, design, tasks); headless | MCP; Powers | Steering files, specs, skills; Crew "durable lessons" | Hooks, trust prompts (not verified) |
| Grok Build | Plan-first; headless | MCP | n/s | Approval prompts |
| Mistral Vibe | Plan (read-only); `--prompt` with approval controls | MCP, skills | AGENTS.md, skills | Approval controls |
| Muse Code | Terminal sessions (Muse Session Protocol) | n/s | n/s | Linux sandbox |
| Cursor | Long-running local and cloud; Automations | Editor, shell, browser, MCP | Index, rules, memories | Cloud isolation; self-hosted machines; approvals (unverified) |
| Devin | Autonomous cloud; Devin Local | Shell, browser, editor, MCP | Repo knowledge, Codemaps | Cloud VM isolation |
| OpenHands | Autonomous; automations | File, terminal, task tracker, MCP | Repo memory, condenser | Confirmation policies; Docker (else full filesystem access) |
| Amp | Interactive plus subagents | Shell, editor, MCP | Threads, AGENTS.md | Prompt-level |
| Cline | Plan/Act; scheduled; headless CI | Editor, shell, browser, MCP | Checkpoints, rules | Approve or auto-approve |
| Kilo | Interactive; `--auto`; KiloClaw always-on | Editor, shell, browser, MCP | Memory bank, rules | Mode-based approvals |
| Aider | Turn-based | Edit, git, shell | Repo map | Git as undo |
| Factory Droid | Interactive to headless | Shell, editor, MCP, plugins | AGENTS.md, specs | Autonomy levels (unverified) |
| Warp | Interactive; Oz background | Shell-native, MCP | Codebase index | Cloud containers; recorded sessions |
| Zed | Interactive; parallel agents (unverified) | Editor, MCP | Editor context | Editor-level |
| opencode | `build` and `plan` agents | Shell, editor, MCP, plugins | AGENTS.md, sessions | Per-tool permission config |
| Augment Intent | Coordinator, specialists, verifier | Editor, shell, MCP | Context Engine; living spec | Worktree isolation |
| pi | Interactive; ~300-word prompt | read, write, edit, bash | Session files; skills | None built in; containers |
| Browser builders (Lovable, Replit, Bolt, v0) | Hidden, autonomous; Replit checkpoints | Platform tools, deploy, DB; Bolt in-browser Node | The project | Platform-bounded; Bolt browser sandbox |
| OpenClaw | Always-on; cron; heartbeats | Shell, browser, files, email, calendar, skills | Local memory files | Optional sandbox |
| Hermes | Always-on; cron; self-improving | Web, browser, vision, TTS, MCP, skills | Agent-curated memory; self-made skills | Sandboxed runtimes |
| NanoClaw | Always-on; scheduled | Claude Code toolset, MCP, skills | Per-agent memory | Container per agent; vault |
| Orchestrators (Conductor, Superset, Claude Squad; Vibe Kanban and Terragon dead) | One CLI per ticket; human reviews diffs | The wrapped CLI's tools | Per-worktree | Worktree isolation; Claude Squad "yolo" |
| Symphony (OpenAI) | One run per Linear issue; "proof of work" | Codex | Per-issue run | Isolated run; CI and review gate |

### Table 2: runtime, surfaces offered, orchestration

| Harness | Runtime | Surfaces offered | Orchestration |
|---|---|---|---|
| Claude Code family | Local; Anthropic cloud VMs; self-hosted; SSH/WSL; Managed Agents hosted | CLI, VS Code/Cursor, JetBrains, desktop, web, mobile, Slack, Chrome, CI, Remote Control, Dispatch, chat channels | Subagents; agent teams; dynamic workflows (1,000 agents/run); routines; Managed Agents multi-agent |
| Codex family | Local; Codex cloud; desktop with background computer use; Agents SDK sandboxes | CLI, VS Code/Cursor/Windsurf, desktop, web, iOS, Slack, GitHub, Linear | Parallel agents in worktrees (sec.); automations; SDK handoffs; subagents "coming" |
| Antigravity / Gemini CLI | Local; background agents; Jules cloud | Desktop app, Go CLI, SDK, Jules (web, GitHub) | Agent manager; subagent workflows; scheduled tasks |
| GitHub Copilot | GitHub-hosted cloud agent; local CLI | github.com, mobile, VS Code, CLI, desktop (preview), SDK (6 languages) | Custom agents; SDK sub-agents; Agent HQ (Claude, Codex, Copilot) |
| Amazon Kiro | Local; cloud sandboxes (preview); Crew gateway | IDE, CLI, web, mobile; Crew via desktop, web, CLI and 7 chat apps | Custom and parallel agents; Crew scheduling |
| Grok Build | Local | CLI; editors via ACP | Up to 8 subagents in worktrees |
| Mistral Vibe | Local; remote agents (reported) | CLI; IDE via ACP | Subagents |
| Muse Code | Local binary (macOS, Linux) | CLI; TypeScript SDK | "Workflows" fan-out |
| Cursor | Local, cloud VM, SSH, self-hosted | IDE, CLI, web, Agents window | Parallel agents in worktrees; SDK |
| Devin | Cloud; local IDE | Web, Slack, issue trackers, Devin Desktop | Command Center kanban; ACP host |
| OpenHands | Local, Docker, Kubernetes, cloud | Web canvas, CLI, GitHub, Slack | Delegation; multi-agent SDK |
| Amp | Local | CLI, VS Code | Subagents |
| Cline | Local; Hub background process | VS Code, JetBrains, CLI, desktop, SDK | Multi-agent teams; scheduled agents |
| Kilo | Local, cloud | VS Code, JetBrains, CLI, web | Agent Manager; worktree sessions |
| Aider | Local | CLI | None |
| Factory Droid | Local, cloud, CI | CLI, VS Code, JetBrains, Zed, web, Slack/Teams, Linear/Jira, mobile | Subagents; SDKs |
| Warp | Local; Oz cloud | Terminal app | Oz agents (webhook, cron); wraps other CLIs |
| Zed | Local | IDE | ACP host (registry with JetBrains) |
| opencode | Local; desktop beta | TUI, desktop, IDE, web share | Subagent; ACP |
| Augment Intent | Local (macOS) | Desktop workspace; Auggie CLI | Three-agent pipeline |
| pi | Local; containers | TUI | Via extensions |
| Browser builders | Vendor cloud; Bolt WebContainers | Browser chat beside live preview | Replit parallel tasks; else none |
| OpenClaw | Local gateway, own hardware | WhatsApp, Telegram, Slack, Discord, Google Chat, Signal, iMessage, voice, canvas | Multi-agent sessions |
| Hermes | Local, Docker, Singularity, SSH; Modal, Daytona, Vercel Sandbox | Telegram, Discord, Slack, WhatsApp, Signal, CLI, desktop, voice | Bot Mode; peer messaging; A2A |
| NanoClaw | Docker | 13 chat and work channels | Per-agent groups |
| Orchestrators | Local desktop or tmux; Terragon cloud | Desktop app, TUI, kanban | Many CLIs in parallel |
| Symphony | Spec plus Elixir reference | Linear board | Ticket-driven runs |

### Table 3: extensibility, pricing model, open source

| Harness | Extensibility | Pricing model | Open source |
|---|---|---|---|
| Claude Code family | MCP, skills, hooks, plugins; three marketplace tiers | Subscriptions plus usage credits; API; Managed Agents +$0.08/session-hour | No; SDK source under commercial terms |
| Codex family | AGENTS.md, MCP, skills, hooks, 90+ plugins, execpolicy | ChatGPT tiers plus credits (since Apr 2, 2026); API | CLI/core Apache-2.0; SDK MIT; app, cloud, IDE closed |
| Antigravity / Gemini CLI | Skills, hooks, subagents, plugins | AI Pro; AI Ultra ($100 and $200 tiers); Code Assist | Gemini CLI Apache-2.0 (consumer access ends Jun 18, 2026); Antigravity CLI closed |
| GitHub Copilot | MCP, custom agents, skills, hooks, plugins | Seats plus premium requests; moving to usage-based | CLI proprietary licence; SDK MIT |
| Amazon Kiro | MCP, Powers, hooks, steering, skills | Free 50 credits; $20 to $200 per user (sec.) | IDE, CLI closed; Crew Apache-2.0; Powers public (licence n/s) |
| Grok Build | MCP; extension system | SuperGrok, X Premium+ tiers; model price (unverified) | Apache-2.0 (Jul 2026) |
| Mistral Vibe | MCP, skills, hooks, AGENTS.md | Mistral API or self-host | Apache-2.0 |
| Muse Code | TypeScript SDK | PAYG $1.25/$4.25 per MTok; Contributor $0.10/$0.20 | No; SDK MIT |
| Cursor | MCP, rules, SDK | Free to $200; Teams $40 (sec.) | No |
| Devin | MCP; ACP host | Free, $20, $200, Teams, Enterprise (sec.) | No |
| OpenHands | MCP; SDK | Free; cloud credits; enterprise | MIT |
| Amp | MCP, AGENTS.md | PAYG, no markup; $10 student; ads dropped | No |
| Cline | MCP, plugins, rules | Free; teams, enterprise | Apache-2.0 |
| Kilo | MCP marketplace | Zero-markup gateway; cloud | MIT |
| Aider | n/s | None; maintenance mode | Apache-2.0 |
| Factory Droid | MCP, plugin marketplace, SDKs | Enterprise; BYOK | No |
| Warp | MCP; runs other CLIs | Subscriptions plus credits (sec.) | MIT and AGPL v3 |
| Zed | ACP, MCP | BYOK; Zed-billed models; subscriptions | GPL-3.0 with Apache parts |
| opencode | MCP, plugins, ACP | Zen model resale | MIT |
| Augment Intent | MCP; Context Engine sold as MCP server | Credits | No (Auggie source public) |
| pi | TypeScript extensions, skills, packages; no built-in MCP | None | MIT |
| Lovable / Replit / Bolt / v0 | n/s | Subscription plus credits / subscription plus effort billing / subscription, enterprise / credits | No / no / base repo MIT / no |
| OpenClaw | ClawHub skills and plugins; provider plugins | None; 501(c)(3) | MIT |
| Hermes | Skills (agentskills.io), MCP | Nous Portal subscription; BYOK | MIT |
| NanoClaw | Edit the source; channel skills | None | MIT |
| Orchestrators | Any CLI agent | Conductor n/s; Superset free desktop plus optional paid cloud; others none | Conductor closed; Superset ELv2; Claude Squad AGPL-3.0; Vibe Kanban, Terragon Apache-2.0 |
| Symphony | Spec | None (preview) | Apache-2.0 |

## What every harness has converged on

Checked against the matrix, candidate by candidate.

- **Instruction files.** CLAUDE.md, AGENTS.md (Codex, Vibe, Amp, opencode, Factory), GEMINI.md-style files, Kiro steering files, Cursor, Cline and Kilo rules, Copilot's `.github/agents` profiles. pi (skills instead), the browser builders (the project is the memory) and the personal agents (memory files) skip them, and neither profile names one for Grok Build, Muse Code, Devin, Warp, Zed, Aider or OpenHands. Converged wherever a profile describes the mechanism, which is the repository-scoped harnesses.
- **MCP.** In every developer row except pi (an extension), Aider and Muse Code (n/s); among the rest, Hermes and NanoClaw list it, OpenClaw and the browser builders do not, and the orchestrators and Symphony inherit the wrapped CLI's. ACP is the second plug and is spreading: Zed, JetBrains, Devin Desktop, opencode, Kiro Crew, Grok Build, Vibe and Factory through Zed [independent, Zed; labs, Amazon and Other labs].
- **Plan modes.** Claude Code, Antigravity, Copilot CLI, Grok Build, Vibe, Cline's Plan/Act, Kilo, opencode's `plan` agent, Kiro's spec loop and Augment's living spec. Codex's sources do not mention one, pi omits it by design, and the profiles state none for Cursor, Devin, Amp, Warp or Factory. Converged among the terminal engines.
- **Subagents.** Every lab and most independents; Codex's SDK still lists them as "coming" [labs, OpenAI]; pi has them only through extensions; Aider has none. Converged.
- **Background or cloud runs.** Five lab families outright (Claude Code, Codex, Antigravity, Copilot, Kiro) and Vibe reportedly, plus Cursor, Devin, OpenHands, Kilo, Factory, Warp Oz, Cline's Hub and the always-on personal agents. The holdouts are the minimal or local-only harnesses: Grok Build, Muse Code, pi, Aider, Amp, Zed, Augment. Converged among the cloud and IDE products, not among the terminal-only ones.
- **Git worktrees.** The Codex app (sec.), Grok Build, Cursor, Kilo, Augment Intent and every orchestrator isolate parallel agents in worktrees [labs, OpenAI and Other labs; independent, Families 1 and 5]. Converged as the developer unit of parallelism; meaningless for chat and browser harnesses.
- **Scheduled runs.** Routines, Codex and Cursor automations, Antigravity scheduled tasks, Cline scheduled agents, Warp Oz cron, Hermes, OpenClaw and NanoClaw cron, Crew scheduling, Managed Agents scheduled deployments, Symphony's board polling. Converged; "the loop is leaving the foreground" [independent, What is converging].
- **Permission modes, classifier-gated.** Modes exist in most rows (pi, NanoClaw and the browser builders have none, by design). A classifier that replaces the human reviewer is Claude Code only; Codex's default `on-request` lets the model decide when to ask, and Copilot's Autopilot is experimental [labs, OpenAI and GitHub]. Not converged.

## What is still contested

- **Sandboxing.** Five schools coexist: OS-level sandboxing of each call (Claude Code's Seatbelt and bubblewrap; Codex's bubblewrap, seccomp, Seatbelt and Windows service); an isolated VM or container per session (Claude and Codex cloud, Cursor, Devin, Copilot's runner, Warp Oz, OpenHands' Docker); a container per agent with no prompts at all (pi, NanoClaw); a platform boundary (Lovable, Replit, Bolt, v0); a second model as reviewer (Claude auto mode). OpenClaw's sandbox is optional, so the harnesses with the widest blast radius have the weakest defaults [independent, What is still differentiated].
- **Pricing.** Subscriptions, some with credits (Anthropic, OpenAI, Google, Cursor, Devin, Warp), seats moving to usage-based (Copilot), per-user credits (Kiro), per-session-hour (Managed Agents), pay-as-you-go without markup (Amp), zero-markup gateway (Kilo), model resale (opencode Zen, Nous Portal), enterprise contracts (Factory, Augment, Qodo), effort-based billing (Replit), and nothing (OpenClaw, pi, Aider, NanoClaw). Ads lasted five months [independent, Amp].
- **Open source.** Labs: closed by default with open exceptions (Codex core Apache-2.0 at about 122k stars, Vibe, Grok Build since July 2026, KiroCrew, Copilot SDK MIT) and two reversals, Gemini CLI retired for consumer tiers and Q Developer CLI replaced by the closed Kiro CLI [labs, TL;DR]. Independents: MIT and Apache dominate by adoption (OpenClaw 389k, Hermes 243k, opencode 206k, pi 103k stars), licences vary (GPL, AGPL, Elastic), and the closed ones raised the most money [independent, Comparison table]. My reading of the matrix: openness tracks adoption, not revenue.
- **Agent-manager surfaces.** Labs ship them as desktop apps (Codex app, Antigravity 2.0 desktop, Copilot app preview, Claude Desktop's Code tab with an agent view); independents put them in or above the IDE (Cursor's Agents window, Devin's Command Center, Kilo's Agent Manager, Augment's "post-IDE" Intent, Cline's desktop Hub) or ship them standalone (Conductor, Superset); GitHub puts it in the repository host (Agent HQ). Two standalone orchestrators died in 2026 [independent, Family 5].
- **Multi-agent orchestration.** From nothing (Aider) and one subagent (opencode) to model-written workflows with up to 1,000 agents per run (Claude Code), agent societies with peer messaging and A2A (Hermes), ticket-driven runs with proof of work (Symphony) and coordinator-verifier pipelines (Augment). No shared shape and no shared protocol beyond Hermes's A2A endpoint.
- **Model dependence.** Labs lock to their own model, except GitHub, whose Copilot CLI defaults to a Claude model and whose Agent HQ hosts Claude and Codex [labs, Test]; independents are BYOK with a house model at the top (Composer 2 on Kimi K2.5, SWE-1.6); NanoClaw is Anthropic-only; Warp is GPT-sponsored. Anthropic's April 4 block and the Agent SDK credits announced May 13 and effective June 15 set the terms for everyone downstream [independent, OpenClaw].
- **Who owns the editor's agent.** Cursor owns it; Zed, JetBrains and Devin Desktop host any agent over ACP [independent, Zed].

## Gaps nobody fills

1. A cross-vendor, cross-runtime control plane. Agent HQ is the only first-party attempt, and it is bound to GitHub's repository model [labs, Nuance]; the independent orchestrators (Superset, Conductor, Warp Oz) are cross-vendor but wrap CLIs one worktree or sandbox at a time rather than spanning runtimes [independent, Family 5].
2. Runtime parity. Neither profile documents a harness with the same feature set locally and in the cloud; Anthropic's cloud sessions drop LSP, plugins and `bypassPermissions`, and Chrome and routines are unavailable through Bedrock, Vertex or Foundry logins [labs, Test].
3. A unit of work for non-technical people. Every lab row assumes a repository and a developer identity; Cowork's files-and-connectors model is the labs' first alternative and is months old [labs, Nuance]. Among independents, personal agents solved the surface but not installation; browser builders solved installation but only build apps. Nobody combined them [independent, Nuance].
4. Verification. Symphony's proof of work, Augment's verifier and Qodo's review agents are early; most harnesses still rely on a human reading a diff [independent, What is still differentiated].
5. Supervision. OpenAI's own figure is three to five sessions per engineer before context switching hurts [independent, Family 5]; agent managers address it, and none claims to have solved it.
6. A portable policy layer. Each harness has its own permission vocabulary (six modes; four-by-four policies; Plan/Act; autonomy levels). The matrix shows no MCP-equivalent for policy (my observation, not a sourced claim).
7. Subscription economics for third parties. Third-party products may not offer claude.ai login or rate limits without prior approval [labs, Anthropic], and subscription use through third-party harnesses is capped by Agent SDK credits [independent, OpenClaw].

## What transfers to non-technical users

Transfers: a chat app as the surface (OpenClaw, Hermes, NanoClaw, Kiro Crew, Anthropic's channels, Codex in Slack); a cloud runtime that needs no device online (Cowork, routines, Codex cloud, the browser builders); scheduled runs; permissions as a platform boundary (browser builders) or a classifier (Claude auto mode) rather than prompts the user cannot judge; memory the agent curates itself (auto memory, "dreams", Hermes); packaged skills and plugin marketplaces (Cowork loads account skills; Codex's 90+ plugins; ClawHub) [labs, Anthropic and OpenAI; independent, Family 4].

Does not transfer: the repository as the unit of work; worktrees; diff review as the acceptance step; approval prompts about shell commands; instruction files in a repo; API keys and Node installs, which make personal-agent users "their own IT department" [independent, Nuance]; ACP and headless CLI modes; kanbans of worktrees.

Unsettled: computer use is a research preview limited to Pro and Max and off by default [labs, Anthropic], and was macOS-only at Codex's launch [labs, OpenAI]; the labs' one visual agent builder failed (OpenAI's Agent Builder is being wound down) [labs, TL;DR]; mobile surfaces drive or monitor sessions that still live in a repository [labs, Anthropic and Amazon].

## What this means for the thesis

**Where the profiles agree.**

- "One runtime, many surfaces" is real, and the runtime is the CLI-shaped engine: "the runtime is CLI-shaped" [labs, TL;DR]; "the CLI survives as the engine that the other surfaces wrap" and "the labs' pattern is mirrored exactly" [independent, TL;DR and What is converging].
- The thesis conflates surface with runtime. "Not a CLI" is true of surfaces and false of engines [labs, Contradicts; independent, Contradicts].
- The harness thickened in runtime, orchestration and surface and thinned in the loop and permissions (auto mode, model-written workflows, dreams; pi, NanoClaw) [labs, Contradicts; independent, Contradicts].
- Non-technical operation is unsolved by anyone [labs, Supports; independent, Nuance].

**Where they disagree.**

- On the purity of "one runtime". [labs] qualifies it twice: feature sets fork by execution location, and GitHub is building the inverse, one surface for many runtimes [labs, Test]. [independent] counts surfaces (Factory eight; Hermes six chat platforms plus CLI and desktop) and calls the convergence exact. The labs reading is stricter and better evidenced.
- On whether an independent harness can be the default. [labs] is blunt: a startup ends up rebuilding an engine the labs give away (Codex, Vibe) or rent (Managed Agents), on top of demonstrated platform risk [labs, Contradicts]. [independent] is split: generic harness companies died (Terragon, Vibe Kanban, Roo Code's extension, Continue; Amp's ads) and "independence is partial", yet independents hold two surfaces the labs have not claimed, chat-app personal agents and browser builders, with the largest star counts and the largest disclosed revenues [independent, Contradicts and Supports].

**Blunt reading.** For developers, an independent generic harness will not be the default: the engine is a commodity, the labs ship every developer surface themselves, the largest independent (Cursor) sold to SpaceX (announced June 16, closed August 14, 2026), and Cognition's route needed a house model and a billion dollars. For non-technical people the default is unclaimed: independents own the adopted surfaces but not a general runtime; labs own the runtime but not the surface. That opening is narrower than "reimagine the harness", and it is a surface, runtime and unit-of-work problem, not a loop problem.

## Open questions and unverified claims

- Inherited unverified items that affect cells: Cursor 3's April 2 and Devin Desktop's June 2 dates; Zed 1.0's parallel agents; Factory's autonomy levels and `droid exec`; Cursor's approval policies; Grok Build's model pricing and May 14 date; Copilot's cloud-agent runtime and pricing (2025 docs); Antigravity's context and permission rows (2025 docs); Kiro's trust prompts; Vibe 2.0 remote agents; whether Roomote shipped; which models Lovable, Replit and v0 run.
- The Agent SDK credit mechanics ($20, $100, $400) rest on secondary write-ups; [labs] could not re-check the reinstatement, so [independent] is treated as authoritative here.
- Muse Code's tools and context are not covered by either profile's body; the labs TL;DR credits it with plan mode, subagents and MCP, but the labs verification note confirms only the terminal agent, Linux sandbox, Workflows fan-out and MIT SDK, so those cells stay "n/s".
- Two readings are mine, not sourced: that openness tracks adoption rather than revenue, and that no portable policy layer exists.
- No new web fetches were made by the author; every fact traces to one of the two profiles. The verification pass of 2026-09-09 (below) re-checked a handful of cells against GitHub and code.claude.com.

## Sources

1. [labs] Developer harnesses from the labs: Anthropic, OpenAI, Google, GitHub, Amazon, and the rest, /home/user/on-the-fly-harness/docs/02-landscape/developer-harnesses-labs.md, Sep 2026 (verified with notes). Sections cited: TL;DR, Anthropic, OpenAI, Google, GitHub, Amazon, Other labs, Comparison table, Test (are the labs converging), What this means for the thesis (Supports, Contradicts, Nuance), Verification notes.
2. [independent] Independent developer harnesses: who builds them, how they are built, and what is converging, /home/user/on-the-fly-harness/docs/02-landscape/developer-harnesses-independent.md, Sep 2026 (verified with notes). Sections cited: TL;DR, A short glossary, Families 1 to 5 (Cursor, Zed, Amp, OpenClaw), Comparison table, What is converging, What is still differentiated, What this means for the thesis (Supports, Contradicts, Nuance), Verification notes.

## Verification notes (2026-09-09)

Method: consistency check of every cell and sentence against the two profiles, read in full, with each profile's verification notes taken as authoritative over its body. Spot checks on this pass: the GitHub API through the session's connector (xai-org/grok-build; the kirodotdev organisation; star counts for openai/codex, openclaw/openclaw, NousResearch/hermes-agent, anomalyco/opencode, earendil-works/pi and google-gemini/gemini-cli) and WebFetch of code.claude.com/docs/en/permission-modes and raw.githubusercontent.com (openai/codex `codex-rs/protocol/src/protocol.rs`). No web searches were used (0 of the 5 allowed). Cell conventions unchanged: "sec." for secondary sourcing, "(unverified)" for a profile's own label, "n/s" for not stated.

### Claims checked

| # | Claim in the document | Verdict | Evidence and action |
|---|---|---|---|
| 1 | Status line "draft, pending verification" | corrected | Changed to "verified with notes". |
| 2 | Eight first-party families, twenty-plus independents, seven parts | confirmed | [labs] profiles eight families; the 19 independent rows cover 26 products, all profiled in [independent]. |
| 3 | TL;DR: seven of eight convergence candidates hold; classifier auto mode is Claude Code only | confirmed, hedged | Holds at row level only with the exceptions now spelled out in the converged section; the bullet points to them. |
| 4 | Cloud sessions drop LSP, plugins and `bypassPermissions` | confirmed | [labs, Test]: no LSP plugins, no `/plugin`, no `bypassPermissions`, agent teams off by default. |
| 5 | Labs are closing engines (Antigravity CLI, Kiro CLI, Copilot CLI licence); most-adopted independents are MIT or Apache | confirmed | [labs, TL;DR; #14, #17]; [independent, Comparison table]. |
| 6 | "The three best-funded independents (Cursor, Cognition, Factory)" | corrected | [independent, TL;DR]: Lovable ($400M at $13.3B) and Replit ($400M at $9B) outrank Factory ($150M at $1.5B); all five are closed. Reworded to "the best-funded independents (Cursor, Cognition, Lovable, Replit, Factory)". |
| 7 | At least nine pricing models; ads dropped within five months; Managed Agents $0.08 per session-hour | confirmed, hedged | The contested section lists ten; October 2025 to March 2026 [independent, Amp; #19]; [labs #26]. "The only new one" became "the only new one still standing", since ads were also new. |
| 8 | Chat apps and browsers are the independents' most-used surfaces; nobody combines a cloud runtime with a chat surface for non-technical people | confirmed as reading | [independent, Supports and Nuance]; [labs, Supports and Nuance]. "The labs are weakest at" softened to "have not claimed": neither profile ranks the labs' chat fronts (Anthropic channels, Codex in Slack and Crew exist, for developer engines). |
| 9 | The profiles agree "not a CLI" is true of surfaces and false of engines, and disagree on whether an independent can be the default | confirmed | [labs, Contradicts]; [independent, Contradicts]. "Only independents that ... survived 2026" reworded to the source's "survivors" framing. |
| 10 | Claude Code row: plan then act, `-p`, tool list, CLAUDE.md, auto memory, 1M context, dreams, 6 modes, classifier with fixed block list, Seatbelt/bubblewrap, cloud VM plus credential proxy | confirmed | [labs, Anthropic; #31]. Permission-modes page re-read 2026-09-09: modes `default`, `acceptEdits`, `plan`, `auto`, `dontAsk`, `bypassPermissions`; `auto` is the starting mode on Pro, Max and Team; "a second model, the classifier, reviews actions instead of you"; the block list includes `curl \| bash`, production deploys, force push and secret exfiltration; blanket `Bash(*)` is dropped. |
| 11 | Codex row: 4 approval policies (default `on-request`), 4 sandbox policies, bubblewrap/seccomp, Seatbelt, Windows service | confirmed | [labs #25]. `protocol.rs` re-read 2026-09-09: `SandboxPolicy` = `danger-full-access`, `read-only`, `external-sandbox`, `workspace-write`; `AskForApproval` = `untrusted`, `on-request` (`#[default]`, alias `on-failure`), `granular`, `never`. |
| 12 | Antigravity, Copilot, Kiro, Grok Build and Vibe rows in Table 1, including the "(not re-verified)" and "(not verified)" labels | confirmed | Match the seven-parts paragraphs and comparison table of [labs]; labels carried over unchanged. |
| 13 | Muse Code row: tools and context n/s, Linux sandbox, Muse Session Protocol | confirmed, doubt noted | [labs #24]. The labs TL;DR credits Muse Code with plan mode, subagents and MCP, which the labs body and note do not support; n/s kept and the open question expanded. |
| 14 | Independent rows in Table 1 (Cursor to Symphony) | confirmed | Cell by cell against [independent, Comparison table] and the Family 5 table. |
| 15 | Zed "parallel agents (sec.)" | corrected | [independent #32] marks Zed 1.0's parallel agents unverified (the tag notes do not mention them); label changed to "(unverified)" per the matrix's own convention. |
| 16 | Table 2 lab rows: Claude Code surfaces and 1,000 agents per run; Codex subagents "coming"; Antigravity desktop app, Go CLI, SDK, Jules; Copilot SDK in 6 languages and Agent HQ; Crew via 7 chat apps; Grok Build 8 subagents; Vibe remote agents (reported); Muse Code binary and SDK | confirmed | [labs] sections and #12, #20, #38; counts recomputed (SDK: Python, TypeScript, Go, .NET, Java, Rust; Crew: Slack, Discord, Telegram, Teams, Webex, WeCom, WeChat). |
| 17 | Hermes runtime "Local, Docker, SSH, four cloud sandboxes" | corrected | [independent, Hermes]: local, Docker, SSH, Singularity, Modal, Daytona and Vercel Sandbox. Singularity is a container runtime, so three cloud sandboxes; the cell now names them. |
| 18 | NanoClaw 13 channels; Factory 8 surfaces; OpenClaw surface list | confirmed | Counts recomputed from [independent, NanoClaw, Factory, OpenClaw]. |
| 19 | Remaining Table 2 rows | confirmed | Against [independent, Comparison table]. |
| 20 | Table 3 Claude Code and Codex cells (three marketplace tiers, usage credits, $0.08, 90+ plugins, execpolicy, credits since Apr 2, 2026, licences) | confirmed | [labs, Anthropic and OpenAI; #10, #26]. |
| 21 | Antigravity pricing "AI Pro/Ultra ($100, $200)" | corrected | [labs #13]: $100 and $200 are both AI Ultra tiers (the top tier cut from $250); AI Pro's price is not in the source. Cell reworded so the figures are not read as Pro and Ultra. |
| 22 | Gemini CLI consumer access ends Jun 18, 2026; Antigravity CLI closed | confirmed | [labs #14]. |
| 23 | Copilot pricing and licences (seats plus premium requests, moving to usage-based; CLI proprietary; SDK MIT) | confirmed | [labs, GitHub; remaining doubts]; the cell already carries the usage-based transition. |
| 24 | Kiro pricing (free 50 credits; $20 to $200 per user) | confirmed | [labs #18], secondary. |
| 25 | Kiro open source "Crew, Powers Apache-2.0" | corrected | GitHub 2026-09-09: kirodotdev/KiroCrew is Apache-2.0 (3,767 stars); kirodotdev/powers is public (376 stars) with no licence detected by GitHub; [labs] says only "Crew and Powers open". Cell now "Crew Apache-2.0; Powers public (licence n/s)". |
| 26 | Grok Build Apache-2.0 (Jul 2026); SuperGrok and X Premium+ tiers; model price unverified | confirmed | GitHub API 2026-09-09: Apache-2.0, Rust, created 2026-07-14, 26,595 stars, described as "SpaceXAI's coding agent harness and TUI"; [labs #19 to #22]. |
| 27 | Muse Code pricing and licence ($1.25/$4.25; Contributor $0.10/$0.20; closed with MIT SDK) | confirmed | [labs #23, #24]. |
| 28 | Table 3 cells for Cursor, Devin, OpenHands, Amp, Cline, Kilo, Aider, Factory, Warp, Zed, opencode, Augment, pi, the browser builders, OpenClaw, Hermes, NanoClaw and Symphony | confirmed | [independent, Comparison table; #19, #28, #29]; Cursor and Devin tiers keep the "(sec.)" label. |
| 29 | Orchestrators pricing "Conductor paid" | corrected | [independent, Family 5] gives Conductor's funding and licence but no pricing; cell now "n/s"; Superset "desktop free forever, optional paid cloud" per source. |
| 30 | Orchestrator licences (Conductor closed; Superset ELv2; Claude Squad AGPL-3.0; Vibe Kanban and Terragon Apache-2.0) | confirmed | [independent, Family 5]. |
| 31 | Instruction files: "Only pi, the browser builders and the personal agents skip them" | corrected | Seven further rows (Grok Build, Muse Code, Devin, Warp, Zed, Aider, OpenHands) have no instruction file in either profile; wording hedged and the verdict scoped to rows where a profile describes the mechanism. |
| 32 | MCP "in every row except pi, Aider and the browser builders" | corrected | Muse Code is n/s, OpenClaw's tools and extensibility cells do not list MCP, and the orchestrators and Symphony wrap CLIs. Rewritten. |
| 33 | ACP adopters (Zed, JetBrains, Devin Desktop, opencode, Kiro Crew, Grok Build, Vibe, Factory) | confirmed | [independent, Zed]; [labs, Amazon and Other labs]. |
| 34 | Plan modes list and "Converged" | corrected | Antigravity ("loop with plan/subagents") added; pi omits it by design and the profiles state none for Cursor, Devin, Amp, Warp or Factory; verdict narrowed to the terminal engines. |
| 35 | Subagents | confirmed | [labs] all families, Codex SDK "coming"; [independent] pi via extensions, Aider none. |
| 36 | Background or cloud runs "all eight lab families"; "converged among funded products" | corrected | Grok Build and Muse Code are local only in Table 2 and Vibe is only reported; Amp, Zed and Augment are funded and local. Rewritten. |
| 37 | Git worktrees; scheduled runs | confirmed | [labs, OpenAI and Other labs]; [independent, What is converging 1 and 5]. |
| 38 | Permission modes "exist everywhere" | corrected | pi, NanoClaw and the browser builders have none; wording changed. |
| 39 | Sandboxing: five schools; OpenClaw's optional sandbox | confirmed | [labs #25, #31; Anthropic sandboxing]; [independent, What is still differentiated]. |
| 40 | Pricing list in the contested section | confirmed, hedged | Google, Cursor and Devin are subscription-only in the sources, so "subscription plus credits" became "subscriptions, some with credits". |
| 41 | Star counts: Codex ~122k; OpenClaw 389k, Hermes 243k, opencode 206k, pi 103k | confirmed | GitHub API 2026-09-09: 122,609; 389,261; 243,512; 205,994; 103,227 (Gemini CLI 106,876). Consistent with [labs #36] and [independent #21]. |
| 42 | Agent-manager surfaces; two standalone orchestrators died in 2026 | corrected in part | Terragon (notice Jan 16, service end Feb 9, 2026) and Vibe Kanban (Apr 10, 2026) [independent #14, #15]; Augment Intent is "post-IDE" [independent, Augment], so "inside the IDE" became "in or above the IDE". |
| 43 | Multi-agent orchestration range; no shared protocol beyond A2A | confirmed as reading | [labs, Anthropic]; [independent, Hermes, Symphony, Augment]. |
| 44 | "Labs lock to their own model" | corrected | [labs, Test]: Copilot CLI defaults to a Claude model and Agent HQ hosts Claude and Codex. Exception added. |
| 45 | Anthropic subscription timeline: April 4 block, June 15 Agent SDK credits | confirmed, sharpened | [independent #2, #3] are authoritative ([labs #39] could not re-check): block April 4, announcement May 13, effective June 15, $20/$100/$400 pools with extra-usage overflow. May 13 added. |
| 46 | Editor's agent: Cursor owns it; Zed, JetBrains and Devin Desktop host any agent over ACP | confirmed | [independent, Zed]. |
| 47 | Gap 1: Agent HQ is "the only attempt" | corrected | [labs, Nuance] says "the only first-party attempt"; Superset, Conductor and Warp are cross-vendor orchestrators [independent, Family 5, Warp]. Reworded. |
| 48 | Gap 2: "No harness offers the same feature set locally and in the cloud" | corrected | The evidence is Anthropic's cloud sessions only [labs, Test]; reworded to what the profiles document. |
| 49 | Gap 3: "Every row assumes a repository and a developer identity" | corrected | [labs, Nuance] says every lab harness; the personal agents' user "is a person, not a repository" [independent, Family 4]. Scoped to lab rows; Cowork is "the labs' first alternative". |
| 50 | Gaps 4, 5 and 7 (verification, three to five sessions, claude.ai login rule and credit cap) | confirmed | [independent, What is still differentiated; Family 5; OpenClaw]; [labs, Anthropic]. "Without prior approval" added to match the Agent SDK quote "unless previously approved". |
| 51 | Gap 6: no portable policy layer | unverified | The author's own observation, labelled as such; no source makes the claim. |
| 52 | Transfers and does-not-transfer lists | confirmed | Each item traced to [labs, Anthropic and OpenAI] or [independent, Family 4 and Nuance]. |
| 53 | Unsettled: computer use a preview limited to Pro and Max; macOS-only at Codex's launch; "visual builders failed" | corrected in part | Computer use and the macOS-only detail confirmed [labs, Anthropic; #8]; "visual builders failed" narrowed to OpenAI's Agent Builder, since Lovable and Replit are visual builders that did not fail [independent, Family 3]. |
| 54 | Thesis quotations from both profiles | confirmed | Verbatim in [labs, TL;DR] and [independent, TL;DR and What is converging]. |
| 55 | Disagreement: "the two surfaces the labs are weakest at" attributed to [independent] | corrected | Not in the source; reworded to "two surfaces the labs have not claimed". |
| 56 | Blunt reading: "the labs own the surfaces"; Cursor sold to SpaceX | corrected in part | Cursor, Zed and JetBrains are independent developer surfaces; reworded to "ship every developer surface themselves" [labs, TL;DR]. SpaceX dates added (announced June 16, closed August 14, 2026) [independent #1]. |
| 57 | Cognition's route needed a house model and a billion dollars | confirmed | [independent, Cognition; #22]: SWE-1.6; $1B at $26B. |
| 58 | Open questions: inherited unverified list; Agent SDK credit mechanics on secondary sources | confirmed | Matches the unverified rows of both profiles. |
| 59 | Open question: Muse Code "extensibility not covered" | corrected | The SDK cell covers extensibility; the bullet now records the labs TL;DR discrepancy instead. |
| 60 | "No new web fetches were made" | corrected | True of the author's pass; amended to point to this verification. |
| 61 | Every product named in the matrix appears in a source | confirmed | All names traced; Qodo, Roomote, Roo Code, Continue and Windsurf appear only in prose and are profiled in [independent]. |
| 62 | Every table renders | confirmed | 29 rows of 5 cells and 58 rows of 4 cells, checked by script before and after the edits. |
| 63 | "Openness tracks adoption, not revenue" | unverified | The author's own reading, labelled as such; consistent with the star counts and funding list but stated by neither profile. |
| 64 | Inherited unverified cells: Zed parallel agents, Cursor approvals, Factory autonomy levels and `droid exec`, Grok Build model price and May 14 date, Vibe 2.0 remote agents, Kiro trust prompts, Antigravity and Copilot 2025-doc cells, Cursor 3 and Devin Desktop dates, Roomote, builder models | unverified | Carry the sources' own labels; none contradicted by anything read on this pass. |

Tally: 64 claims checked; 39 confirmed, 22 corrected, 3 unverified.

### Corrections made

1. The converged section overstated the rows. Instruction files: seven further rows have none stated. MCP: Muse Code is n/s, OpenClaw does not list it, orchestrators and Symphony inherit the wrapped CLI's. Plan modes: Antigravity added; Codex, pi, Cursor, Devin, Amp, Warp and Factory noted; verdict narrowed to terminal engines. Background or cloud runs: five lab families plus Vibe reportedly, not "all eight" (Grok Build and Muse Code are local only); Amp, Zed and Augment are funded holdouts, so "converged among funded products" is gone. Permission modes: "most rows", not "everywhere".
2. Model dependence: "Labs lock to their own model" now carries the GitHub exception from [labs, Test] (Copilot CLI defaults to a Claude model; Agent HQ hosts Claude and Codex); the Agent SDK credits carry their May 13 announcement date alongside the June 15 effective date.
3. Kiro open-source cell: Powers is no longer credited with Apache-2.0; GitHub detects no licence on kirodotdev/powers and [labs] says only "open". KiroCrew keeps Apache-2.0.
4. TL;DR: "the three best-funded independents (Cursor, Cognition, Factory)" replaced by the five best-funded in [independent] (Lovable and Replit outrank Factory), all closed; a pointer to the row-level exceptions; "the only new one still standing"; "have not claimed" instead of "weakest at"; "harness businesses that survived 2026" instead of "only independents that ... survived".
5. Table cells: Zed parallel agents relabelled "(unverified)"; Hermes runtimes named (Singularity is not a cloud sandbox); Antigravity pricing disambiguated ($100 and $200 are both AI Ultra tiers); Conductor pricing "n/s" and Superset "optional paid cloud".
6. Contested section: "subscriptions, some with credits" (Google, Cursor and Devin are subscription-only in the sources); Augment Intent moved from "inside the IDE" to "in or above the IDE".
7. Gaps: Agent HQ is the only first-party attempt (independent orchestrators are cross-vendor); runtime parity scoped to what the profiles document; unit of work scoped to lab rows with Cowork as the labs' first alternative; "without prior approval" on the claude.ai login rule.
8. Transfers: "visual builders failed" narrowed to OpenAI's Agent Builder.
9. Thesis: "weakest at" no longer attributed to [independent]; "the labs own the surfaces" became "ship every developer surface themselves"; SpaceX acquisition dates added.
10. Open questions: Muse Code bullet now records the labs TL;DR discrepancy; the "no new web fetches" bullet distinguishes the author's pass from this verification.
11. Status line changed to "verified with notes".

### Remaining doubts

- Muse Code: the labs TL;DR asserts plan mode, subagents and MCP, but the labs body and its verification note support only a terminal agent with a Linux sandbox, Workflows fan-out and an MIT SDK. The cells stay n/s until the labs profile reconciles its own TL;DR.
- Hermes "six chat platforms": [independent] names five (Telegram, Discord, Slack, WhatsApp, Signal) but counts six in "What is converging"; the matrix quotes the count in the thesis section and lists five in Table 2.
- kirodotdev/powers: GitHub's licence detector reports none at the repository root; individual powers may carry their own licence files, which were not checked.
- "The only new one still standing" for per-session-hour billing: Replit's effort-based billing is undated in the sources, so its novelty is unknown.
- The Agent SDK credit amounts ($20, $100, $400) rest on secondary write-ups; the labs profile did not re-check the reinstatement and Anthropic's own pages were unreachable to both profiles.
- "Have not claimed" for the chat and browser surfaces is the matrix's synthesis: the labs do ship chat fronts (channels, Slack, Crew), but for developer engines that assume a repository.
- Star counts move daily; the 2026-09-09 readings agree with the rounded cells, but "about 122k" for Codex and the other figures will drift.
- Everything in row 64 of the table stays unverified with the sources' own labels.
