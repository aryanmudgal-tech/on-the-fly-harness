# Developer harness matrix: labs and independents side by side

*Research program: agent harnesses. Written 2026-09-08. Status: draft, pending verification.*

## What this document answers

- One matrix of the eight first-party harness families and twenty-plus independent harnesses across the seven anatomy parts, surfaces offered, extensibility, pricing and open-source status, built only from the two verified profiles [labs] and [independent].
- What has converged, what is still contested, and which gaps nobody fills.
- Which patterns transfer to non-technical users, and where the two profiles agree and disagree about "one runtime, many surfaces" and about whether an independent harness can be the default.

## TL;DR

- Seven of the eight convergence candidates hold: instruction files, MCP, plan modes, subagents, background or cloud runs, git worktrees and scheduled runs appear in nearly every developer row. The eighth, classifier-gated auto mode, is one product, Claude Code [labs, Anthropic]. Permissions are the least converged part.
- Both profiles find the same engine shape: a CLI-shaped agent process behind thin surfaces. The labs profile adds that feature sets fork at the runtime seam; Anthropic's cloud sessions drop LSP, plugins and `bypassPermissions` [labs, Test].
- Openness moves in opposite directions. The labs are closing engines (Antigravity CLI, Kiro CLI, Copilot CLI licence) while the most-adopted independents are MIT or Apache; but the three best-funded independents (Cursor, Cognition, Factory) are closed [labs, TL;DR; independent, Comparison table].
- Pricing has no consensus: at least nine models are in use, ads were dropped within five months, and per-session-hour billing (Managed Agents, $0.08) is the only new one [labs, Anthropic; independent, What is still differentiated].
- The surfaces with the most users among independents, chat apps and browsers, are the ones the labs are weakest at, yet nobody offers a general agent for non-technical people that runs in the cloud and is reached from a chat app [independent, Nuance; labs, Nuance].
- On the thesis, the profiles agree that "not a CLI" is true of surfaces and false of engines. They disagree in emphasis on whether an independent can be the default: [labs] says platform risk makes it a poor bet; [independent] says only independents that own a model, an enterprise workflow or a non-developer surface survived 2026.

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
| Zed | Interactive; parallel agents (sec.) | Editor, MCP | Editor context | Editor-level |
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
| Hermes | Local, Docker, SSH, four cloud sandboxes | Telegram, Discord, Slack, WhatsApp, Signal, CLI, desktop, voice | Bot Mode; peer messaging; A2A |
| NanoClaw | Docker | 13 chat and work channels | Per-agent groups |
| Orchestrators | Local desktop or tmux; Terragon cloud | Desktop app, TUI, kanban | Many CLIs in parallel |
| Symphony | Spec plus Elixir reference | Linear board | Ticket-driven runs |

### Table 3: extensibility, pricing model, open source

| Harness | Extensibility | Pricing model | Open source |
|---|---|---|---|
| Claude Code family | MCP, skills, hooks, plugins; three marketplace tiers | Subscriptions plus usage credits; API; Managed Agents +$0.08/session-hour | No; SDK source under commercial terms |
| Codex family | AGENTS.md, MCP, skills, hooks, 90+ plugins, execpolicy | ChatGPT tiers plus credits (since Apr 2, 2026); API | CLI/core Apache-2.0; SDK MIT; app, cloud, IDE closed |
| Antigravity / Gemini CLI | Skills, hooks, subagents, plugins | AI Pro/Ultra ($100, $200); Code Assist | Gemini CLI Apache-2.0 (consumer access ends Jun 18, 2026); Antigravity CLI closed |
| GitHub Copilot | MCP, custom agents, skills, hooks, plugins | Seats plus premium requests; moving to usage-based | CLI proprietary licence; SDK MIT |
| Amazon Kiro | MCP, Powers, hooks, steering, skills | Free 50 credits; $20 to $200 per user (sec.) | IDE, CLI closed; Crew, Powers Apache-2.0 |
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
| Orchestrators | Any CLI agent | Conductor paid; Superset free desktop plus cloud; others none | Conductor closed; Superset ELv2; Claude Squad AGPL-3.0; Vibe Kanban, Terragon Apache-2.0 |
| Symphony | Spec | None (preview) | Apache-2.0 |

## What every harness has converged on

Checked against the matrix, candidate by candidate.

- **Instruction files.** CLAUDE.md, AGENTS.md (Codex, Vibe, Amp, opencode, Factory), GEMINI.md-style files, Kiro steering files, Cursor, Cline and Kilo rules, Copilot's `.github/agents` profiles. Only pi (skills instead), the browser builders (the project is the memory) and the personal agents (memory files) skip them. Converged wherever the unit of work is a repository.
- **MCP.** In every row except pi (an extension), Aider and the browser builders (n/s). ACP is the second plug and is spreading: Zed, JetBrains, Devin Desktop, opencode, Kiro Crew, Grok Build, Vibe and Factory through Zed [independent, Zed; labs, Amazon and Other labs].
- **Plan modes.** Claude Code, Copilot CLI, Grok Build, Vibe, Cline's Plan/Act, Kilo, opencode's `plan` agent, Kiro's spec loop and Augment's living spec. Codex's sources do not mention one. Converged.
- **Subagents.** Every lab and most independents; Codex's SDK still lists them as "coming" [labs, OpenAI]; pi has them only through extensions; Aider has none. Converged.
- **Background or cloud runs.** All eight lab families, plus Cursor, Devin, OpenHands, Kilo, Factory, Warp Oz, Cline's Hub and, reportedly, Vibe. The holdouts are the minimal or local-only harnesses: pi, Aider, Amp, Zed, Augment. Converged among funded products.
- **Git worktrees.** The Codex app (sec.), Grok Build, Cursor, Kilo, Augment Intent and every orchestrator isolate parallel agents in worktrees [labs, OpenAI and Other labs; independent, Families 1 and 5]. Converged as the developer unit of parallelism; meaningless for chat and browser harnesses.
- **Scheduled runs.** Routines, Codex and Cursor automations, Antigravity scheduled tasks, Cline scheduled agents, Warp Oz cron, Hermes, OpenClaw and NanoClaw cron, Crew scheduling, Managed Agents scheduled deployments, Symphony's board polling. Converged; "the loop is leaving the foreground" [independent, What is converging].
- **Permission modes, classifier-gated.** Modes exist everywhere. A classifier that replaces the human reviewer is Claude Code only; Codex's default `on-request` lets the model decide when to ask, and Copilot's Autopilot is experimental [labs, OpenAI and GitHub]. Not converged.

## What is still contested

- **Sandboxing.** Five schools coexist: OS-level sandboxing of each call (Claude Code's Seatbelt and bubblewrap; Codex's bubblewrap, seccomp, Seatbelt and Windows service); an isolated VM or container per session (Claude and Codex cloud, Cursor, Devin, Copilot's runner, Warp Oz, OpenHands' Docker); a container per agent with no prompts at all (pi, NanoClaw); a platform boundary (Lovable, Replit, Bolt, v0); a second model as reviewer (Claude auto mode). OpenClaw's sandbox is optional, so the harnesses with the widest blast radius have the weakest defaults [independent, What is still differentiated].
- **Pricing.** Subscription plus credits (Anthropic, OpenAI, Google, Cursor, Devin, Warp), seats moving to usage-based (Copilot), per-user credits (Kiro), per-session-hour (Managed Agents), pay-as-you-go without markup (Amp), zero-markup gateway (Kilo), model resale (opencode Zen, Nous Portal), enterprise contracts (Factory, Augment, Qodo), effort-based billing (Replit), and nothing (OpenClaw, pi, Aider, NanoClaw). Ads lasted five months [independent, Amp].
- **Open source.** Labs: closed by default with open exceptions (Codex core Apache-2.0 at about 122k stars, Vibe, Grok Build since July 2026, KiroCrew, Copilot SDK MIT) and two reversals, Gemini CLI retired for consumer tiers and Q Developer CLI replaced by the closed Kiro CLI [labs, TL;DR]. Independents: MIT and Apache dominate by adoption (OpenClaw 389k, Hermes 243k, opencode 206k, pi 103k stars), licences vary (GPL, AGPL, Elastic), and the closed ones raised the most money [independent, Comparison table]. My reading of the matrix: openness tracks adoption, not revenue.
- **Agent-manager surfaces.** Labs ship them as desktop apps (Codex app, Antigravity 2.0 desktop, Copilot app preview, Claude Desktop's Code tab with an agent view); independents put them inside the IDE (Cursor's Agents window, Devin's Command Center, Kilo's Agent Manager, Augment Intent, Cline's Hub) or ship them standalone (Conductor, Superset); GitHub puts it in the repository host (Agent HQ). Two standalone orchestrators died in 2026 [independent, Family 5].
- **Multi-agent orchestration.** From nothing (Aider) and one subagent (opencode) to model-written workflows with up to 1,000 agents per run (Claude Code), agent societies with peer messaging and A2A (Hermes), ticket-driven runs with proof of work (Symphony) and coordinator-verifier pipelines (Augment). No shared shape and no shared protocol beyond Hermes's A2A endpoint.
- **Model dependence.** Labs lock to their own model; independents are BYOK with a house model at the top (Composer 2 on Kimi K2.5, SWE-1.6); NanoClaw is Anthropic-only; Warp is GPT-sponsored. Anthropic's April 4 block and June 15 Agent SDK credits set the terms for everyone downstream [independent, OpenClaw].
- **Who owns the editor's agent.** Cursor owns it; Zed, JetBrains and Devin Desktop host any agent over ACP [independent, Zed].

## Gaps nobody fills

1. A cross-vendor, cross-runtime control plane. Agent HQ is the only attempt, and it is bound to GitHub's repository model [labs, Nuance].
2. Runtime parity. No harness offers the same feature set locally and in the cloud; Anthropic's cloud sessions drop LSP, plugins and `bypassPermissions`, and Chrome and routines are unavailable through Bedrock, Vertex or Foundry logins [labs, Test].
3. A unit of work for non-technical people. Every row assumes a repository and a developer identity; Cowork's files-and-connectors model is the first alternative and is months old [labs, Nuance]. Personal agents solved the surface but not installation; browser builders solved installation but only build apps. Nobody combined them [independent, Nuance].
4. Verification. Symphony's proof of work, Augment's verifier and Qodo's review agents are early; most harnesses still rely on a human reading a diff [independent, What is still differentiated].
5. Supervision. OpenAI's own figure is three to five sessions per engineer before context switching hurts [independent, Family 5]; agent managers address it, and none claims to have solved it.
6. A portable policy layer. Each harness has its own permission vocabulary (six modes; four-by-four policies; Plan/Act; autonomy levels). The matrix shows no MCP-equivalent for policy (my observation, not a sourced claim).
7. Subscription economics for third parties. Third-party products may not offer claude.ai login or rate limits [labs, Anthropic], and subscription use through third-party harnesses is capped by Agent SDK credits [independent, OpenClaw].

## What transfers to non-technical users

Transfers: a chat app as the surface (OpenClaw, Hermes, NanoClaw, Kiro Crew, Anthropic's channels, Codex in Slack); a cloud runtime that needs no device online (Cowork, routines, Codex cloud, the browser builders); scheduled runs; permissions as a platform boundary (browser builders) or a classifier (Claude auto mode) rather than prompts the user cannot judge; memory the agent curates itself (auto memory, "dreams", Hermes); packaged skills and plugin marketplaces (Cowork loads account skills; Codex's 90+ plugins; ClawHub) [labs, Anthropic and OpenAI; independent, Family 4].

Does not transfer: the repository as the unit of work; worktrees; diff review as the acceptance step; approval prompts about shell commands; instruction files in a repo; API keys and Node installs, which make personal-agent users "their own IT department" [independent, Nuance]; ACP and headless CLI modes; kanbans of worktrees.

Unsettled: computer use is a research preview limited to Pro and Max and off by default [labs, Anthropic], and was macOS-only at Codex's launch [labs, OpenAI]; visual builders failed (Agent Builder is being wound down) [labs, TL;DR]; mobile surfaces drive or monitor sessions that still live in a repository [labs, Anthropic and Amazon].

## What this means for the thesis

**Where the profiles agree.**

- "One runtime, many surfaces" is real, and the runtime is the CLI-shaped engine: "the runtime is CLI-shaped" [labs, TL;DR]; "the CLI survives as the engine that the other surfaces wrap" and "the labs' pattern is mirrored exactly" [independent, TL;DR and What is converging].
- The thesis conflates surface with runtime. "Not a CLI" is true of surfaces and false of engines [labs, Contradicts; independent, Contradicts].
- The harness thickened in runtime, orchestration and surface and thinned in the loop and permissions (auto mode, model-written workflows, dreams; pi, NanoClaw) [labs, Contradicts; independent, Contradicts].
- Non-technical operation is unsolved by anyone [labs, Supports; independent, Nuance].

**Where they disagree.**

- On the purity of "one runtime". [labs] qualifies it twice: feature sets fork by execution location, and GitHub is building the inverse, one surface for many runtimes [labs, Test]. [independent] counts surfaces (Factory eight; Hermes six chat platforms plus CLI and desktop) and calls the convergence exact. The labs reading is stricter and better evidenced.
- On whether an independent harness can be the default. [labs] is blunt: a startup ends up rebuilding an engine the labs give away (Codex, Vibe) or rent (Managed Agents), on top of demonstrated platform risk [labs, Contradicts]. [independent] is split: generic harness companies died (Terragon, Vibe Kanban, Roo Code's extension, Continue; Amp's ads) and "independence is partial", yet independents hold the two surfaces the labs are weakest at, chat-app personal agents and browser builders, with the largest star counts and the largest disclosed revenues [independent, Contradicts and Supports].

**Blunt reading.** For developers, an independent generic harness will not be the default: the engine is a commodity, the labs own the surfaces, the largest independent (Cursor) sold to SpaceX, and Cognition's route needed a house model and a billion dollars. For non-technical people the default is unclaimed: independents own the adopted surfaces but not a general runtime; labs own the runtime but not the surface. That opening is narrower than "reimagine the harness", and it is a surface, runtime and unit-of-work problem, not a loop problem.

## Open questions and unverified claims

- Inherited unverified items that affect cells: Cursor 3's April 2 and Devin Desktop's June 2 dates; Zed 1.0's parallel agents; Factory's autonomy levels and `droid exec`; Cursor's approval policies; Grok Build's model pricing and May 14 date; Copilot's cloud-agent runtime and pricing (2025 docs); Antigravity's context and permission rows (2025 docs); Kiro's trust prompts; Vibe 2.0 remote agents; whether Roomote shipped; which models Lovable, Replit and v0 run.
- The Agent SDK credit mechanics ($20, $100, $400) rest on secondary write-ups; [labs] could not re-check the reinstatement, so [independent] is treated as authoritative here.
- Muse Code's tools, context and extensibility are not covered by either profile.
- Two readings are mine, not sourced: that openness tracks adoption rather than revenue, and that no portable policy layer exists.
- No new web fetches were made; every fact traces to one of the two profiles.

## Sources

1. [labs] Developer harnesses from the labs: Anthropic, OpenAI, Google, GitHub, Amazon, and the rest, /home/user/on-the-fly-harness/docs/02-landscape/developer-harnesses-labs.md, Sep 2026 (verified with notes). Sections cited: TL;DR, Anthropic, OpenAI, Google, GitHub, Amazon, Other labs, Comparison table, Test (are the labs converging), What this means for the thesis (Supports, Contradicts, Nuance), Verification notes.
2. [independent] Independent developer harnesses: who builds them, how they are built, and what is converging, /home/user/on-the-fly-harness/docs/02-landscape/developer-harnesses-independent.md, Sep 2026 (verified with notes). Sections cited: TL;DR, A short glossary, Families 1 to 5 (Cursor, Zed, Amp, OpenClaw), Comparison table, What is converging, What is still differentiated, What this means for the thesis (Supports, Contradicts, Nuance), Verification notes.
