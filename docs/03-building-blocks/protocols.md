# Protocols and Standards That Shape Agent Harnesses

*Research program: agent harnesses. Written 2026-09-08. Status: verified with notes.*

## What this document answers

- Which protocols and file-format standards now define the boundaries of an agent harness (tools, agent-to-agent, agent-to-editor, agent-to-UI, instructions, skills, hooks, plugins, payments, web content), what problem each solves, and who backs it.
- How each standard changed the harness in practice, with dated spec history and adoption evidence, separating vendor claims from independent data.
- What the standards imply for surfaces beyond the CLI: chat apps, IDEs, web apps, phones, checkout flows.
- Where the evidence supports the program's thesis and where it cuts against it.

## TL;DR

- MCP won the tool layer. It moved to the Linux Foundation's Agentic AI Foundation in Dec 2025, and its maintainers report "close to half-a-billion downloads a month" across Tier 1 SDKs and over 1 billion total downloads each for the TypeScript and Python SDKs (Jul 2026, vendor figures) [6][7]. Five spec revisions in 20 months; the 2026-07-28 release removed protocol sessions and the initialization handshake to make MCP "stateless, cacheable, routable" HTTP [5][6].
- The tool layer is settled; the UI layer is not. Four agent-to-UI approaches compete (MCP Apps, AG-UI, A2UI, AI SDK generative UI), plus OpenAI's Apps SDK, whose examples now use MCP Apps' metadata key. MCP Apps is seven months old; A2UI calls itself an "early stage public preview"; Vercel's harness packages are "experimental" [13][37][39][42].
- Agent-to-editor is being standardized by Zed's Agent Client Protocol: 41 agents in its registry repo (Claude, Codex, Gemini CLI, Cursor, Copilot, Devin, Goose and others; 8 of them quarantined for failing CI checks) and 15+ client types (Zed, JetBrains, Neovim, Emacs, VS Code, Obsidian and others). JetBrains co-leads it since Feb 2026; a working group is adding HTTP/WebSocket transports for remote agents (Apr 2026) [27][30][31][32].
- Harness extension formats have converged across vendors: `AGENTS.md` (site claims "over 60k open-source projects"), `SKILL.md` (46 listed clients; anthropics/skills at 175k GitHub stars), hooks with the same event names in Claude Code and Codex (Codex ships code to migrate Cursor hooks), and near-identical plugin manifests and marketplaces [47][50][51][54][61][62].
- Payments are the least mature layer: four overlapping protocols (AP2, x402, the OpenAI/Stripe Agentic Commerce Protocol, UCP). x402's settlement volume reportedly fell 93% in 2026 with about half of transactions "gamified" (secondary sources) [75][76].
- llms.txt shows that publication is not adoption: publishing is common (SE Ranking's ~10% figure is unverified; Ahrefs found about 28% of its 137,000 developer-skewed domains publishing one), yet 97% of the files got zero requests (Ahrefs; log month unverified), and Google's John Mueller said in Jun 2025 that no AI system uses it (secondary, unverified) [83][84][85][99].
- For the thesis: protocols have separated the runtime from the surface (supports). But the labs own both ends of most protocols and are plugging their own runtimes into their own chat surfaces (contradicts). Standards make a new surface buildable; they do not give it distribution.

## 1. Why protocols decide what a harness is

**Analogy.** Before USB-C, every device shipped its own charger and every laptop maker designed around each one. A protocol is the shared plug: the device maker stops caring who built the charger. In agent land the "device" is the harness and the "chargers" are tools, other agents, editors, user interfaces and payment rails.

**Precisely.** Nearly every standard below is a JSON-RPC 2.0 or JSON-over-HTTP schema plus a discovery convention (a well-known URL, a manifest, or a Markdown file in a repository). What differs is which boundary of the harness each one standardizes.

| Boundary | Standard(s) | Harness part affected |
|---|---|---|
| Agent to tools, data, prompts | MCP, its registry and extensions | Tools; context; permissions (OAuth) |
| Agent to agent | A2A (absorbed IBM's ACP in Aug 2025) | Orchestration |
| Agent to editor or client app | Zed's Agent Client Protocol (ACP) | Surface; runtime |
| Agent to user interface | MCP Apps, OpenAI Apps SDK and ChatKit, AG-UI, A2UI, AI SDK generative UI | Surface |
| Repository to agent | AGENTS.md | Context and memory |
| Procedures to agent | Agent Skills (SKILL.md) | Context; tools |
| Harness lifecycle to user code | Hooks, plugins, marketplaces | Permissions; loop; distribution |
| Agent to money | AP2, x402, Agentic Commerce Protocol, UCP | Permissions; tools |
| Website to model | llms.txt | Context |

## 2. How the protocols relate

```
                                  PEOPLE
   +--------------+----------------+-----------------+------------------+
   |  CLI / TUI   |  IDE / editor  |  Chat app       |  Web or native   |
   |              |  (ACP client)  |  (ChatGPT,      |  app (AG-UI,     |
   |              |                |   Claude, ...)  |   ChatKit, ...)  |
   +------+-------+-------+--------+--------+--------+---------+--------+
          |               | ACP             | MCP Apps /       | AG-UI events
          | direct        | (JSON-RPC over  | Apps SDK         | A2UI JSON
          | process       |  stdio; HTTP/   | (ui:// HTML in   | AI SDK tool parts
          |               |  WebSocket WG)  |  sandboxed       |
          |               |                 |  iframes)        |
   +------v---------------v-----------------v------------------v--------+
   |                        HARNESS (agent runtime)                     |
   |         loop . context . permissions . runtime . orchestration     |
   |   +-------------+   +-------------+   +---------------------------+ |
   |   |  AGENTS.md  |   |  SKILL.md   |   | hooks (PreToolUse, ...)   | |
   |   |  repo rules |   |  procedures |   | plugins and marketplaces  | |
   |   +-------------+   +-------------+   +---------------------------+ |
   +-------+--------------------------+-----------------------+---------+
           | MCP: tools, resources,   | A2A: tasks between    | AP2, x402,
           | prompts; OAuth; apps     | opaque agents         | ACP (OpenAI/
           v                          v                       | Stripe), UCP
   +----------------+         +----------------+              v
   | MCP servers    |         | other agents   |     +--------------------+
   | (registry,     |         | (Agent Cards,  |     | merchants, PSPs,   |
   |  ~10k public)  |         |  172 partners) |     | card networks      |
   +----------------+         +----------------+     +--------------------+

   upstream content hygiene: llms.txt (site-curated Markdown for models)
```

Reading the diagram: MCP and A2A sit below the harness (what the agent reaches out to). ACP and the UI protocols sit above it (how people reach the agent). AGENTS.md, skills, hooks and plugins live inside it. Payment protocols hang off tool calls. llms.txt is upstream content hygiene.

## 3. MCP: the tool layer

### 3.1 Problem and backers

Before MCP, each harness wrote its own connector for GitHub, Postgres, Slack and so on: N harnesses times M tools. MCP lets a tool server expose *tools*, *resources* and *prompts* over one JSON-RPC interface that any client can consume. The spec repository was created in Sep 2024 and the protocol was announced in Nov 2024 (launch month via secondary) [1][19][38]. Anthropic donated it on Dec 9, 2025 to the Agentic AI Foundation (AAIF), a Linux Foundation directed fund co-founded by Anthropic, Block and OpenAI, with Google, Microsoft, AWS, Cloudflare and Bloomberg as supporting members; goose (Block) and AGENTS.md (OpenAI) were the other founding projects [7]. The Linux Foundation's release lists AWS, Anthropic, Block, Bloomberg, Cloudflare, Google, Microsoft and OpenAI as platinum members (via secondary; linuxfoundation.org blocked) [8].

Governance stayed with the maintainers. The governance document names two lead maintainers (David Soria Parra, Den Delimarsky) with final authority, six core maintainers, working and interest groups, a biweekly vote, and Specification Enhancement Proposals (SEPs) as "the primary mechanism for proposing major new features"; the legal entity is "Model Context Protocol a Series of LF Projects, LLC" [9]. The maintainer team was expanded on Apr 8, 2026: Clare Liguori joined the core maintainers and Den Delimarsky became a lead maintainer (blog index) [11].

### 3.2 Spec evolution

| Revision | Headline changes (from the changelogs) |
|---|---|
| 2024-11-05 | Initial: stdio and HTTP+SSE transports; tools, resources, prompts, sampling, roots [1] |
| 2025-03-26 | OAuth 2.1 authorization framework; Streamable HTTP replaces HTTP+SSE; JSON-RPC batching; tool annotations (read-only vs destructive); audio content; completions [2] |
| 2025-06-18 | Batching removed; structured tool output; servers classified as OAuth resource servers with RFC 8707 resource indicators; elicitation (server asks the user for input); resource links; `MCP-Protocol-Version` header [3] |
| 2025-11-25 | OpenID Connect discovery; icons; incremental scope consent; tool-naming guidance; enum and URL-mode elicitation; sampling with tools; Client ID Metadata Documents; experimental tasks (SEP-1686) [4] |
| 2026-07-28 | Protocol-level sessions and `Mcp-Session-Id` removed (SEP-2567); initialization handshake removed, every request carries version and capabilities in `_meta` (SEP-2575); `server/discover`; `subscriptions/listen`; tasks moved to an extension (SEP-2663); "Multi Round-Trip Requests" replace server-initiated requests (SEP-2322); SSE resumability removed; cacheable list results with `ttlMs`; OpenTelemetry trace context; roots, sampling and logging deprecated (SEP-2577) [5] |

The direction is consistent: away from "a local process talking to a desktop app" and toward "a web service behind an OAuth gateway". The maintainers describe the goal as "stateless, cacheable, routable, and globally scalable" with "HTTP workload" characteristics, and note that four Tier 1 SDKs (TypeScript, Python, Go, C#) support the new release, with Rust in beta [6]. The features that assumed a rich, stateful local client (roots, sampling, logging) are the ones deprecated [5]. Extensions became the pressure valve: an extension is optional and "a client or server that doesn't recognize an extension simply skips it during negotiation" (Mar 2026); official ones cover Apps, tasks, OAuth client credentials and Enterprise-Managed Authorization ("zero-touch OAuth", Jun 2026) [5][10][11].

Churn is real. Batching was added in Mar 2025 and removed in Jun 2025; sessions were added in Mar 2025 and removed in Jul 2026 [2][3][5]. A harness that tracks MCP has had to change transport handling roughly every six months.

### 3.3 The registry

The official registry launched in preview on Sep 8, 2025 and was tagged v1.0.0 the same day; the README declared an "API freeze (v0.1)" on Oct 24, 2025 and still describes preview status. It is Go plus PostgreSQL, verifies namespaces through GitHub OAuth, OIDC or DNS/HTTP (for example `io.github.username/server-name`), and is run by a working group led by Radoslav Dimitrov (Stacklok) with contributors from PulseMCP, TeamSpark and Ravenmail [15][16]. Releases continued through v1.8.1 (Aug 6, 2026), adding crates.io package support in Jul 2026 [16]. General availability status: unverified. A secondary tally counted about 5,800 registry entries in Mar 2026 against the "10,000 active servers" the MCP blog claimed in Dec 2025; the registry excludes private enterprise servers, so the two numbers measure different things [7][19].

### 3.4 MCP Apps

MCP Apps (SEP-1865) was proposed on Nov 21, 2025 by MCP core maintainers at OpenAI and Anthropic together with the creators of the earlier community project MCP-UI (Ido Salomon, Liad Yosef), which the post says was already adopted by Postman, Shopify and Hugging Face (vendor claim) [12]. The stable spec is dated 2026-01-26 and the README lists Claude, ChatGPT, VS Code, Goose and Postman as hosts, plus MCPJam, mcp-use and Alpic as tooling [13]. Claude's support went live the same week (The Register, Jan 26, 2026, secondary) [17].

Mechanism: a tool declares `_meta.ui.resourceUri` pointing to a `ui://` resource whose `mimeType` "MUST be `text/html;profile=mcp-app`"; the host renders it "in sandboxed iframes with restricted permissions", "MUST construct CSP headers based on declared domains", and talks to it with MCP's own JSON-RPC over `postMessage` (`ui/initialize`, `ui/notifications/tool-result`, `ui/open-link`, `ui/request-display-mode`, `ui/update-model-context`). It is optional and negotiated through the `io.modelcontextprotocol/ui` capability [14].

### 3.5 Adoption evidence

- Vendor figures: "over 97 million monthly SDK downloads" and "10,000 active servers" (Dec 2025); "close to half-a-billion downloads a month" and >1B total each for TypeScript and Python (Jul 2026); "one ecosystem partner reports nearly 20% of all monthly interactive queries are now made by agents" (Honeycomb, Jul 2026) [6][7].
- Client support named by the maintainers: ChatGPT, Claude, Cursor, Gemini, Microsoft Copilot, VS Code (Dec 2025) [7]. Secondary roundups say OpenAI, Google, Microsoft and Salesforce all shipped support within 13 months of launch [19].
- GitHub: modelcontextprotocol/servers has 90k stars; the spec repo 9.2k; the registry 7.2k; ext-apps 2.8k (Sep 2026) [38].

### 3.6 Counter-current: code instead of tool calls

Cloudflare's Code Mode package turns MCP tool schemas into a TypeScript API and has the model write code against it inside an isolated Worker, on the argument that "LLMs are better at writing code than calling tools" and that this needs fewer tokens and round trips; it is marked experimental [86]. Anthropic published a similar "code execution with MCP" argument in Nov 2025 (unverified here: anthropic.com blocked). If this pattern wins, MCP remains the catalog but the loop calls tools from generated code in a sandbox, which shifts weight from per-call permission prompts to the runtime's sandboxing.

### 3.7 What MCP changes in the harness, and for surfaces beyond the CLI

- Tools become external, shareable and increasingly remote. The harness's job shifts to authentication, catalog management and risk classification; the maintainers themselves frame tool annotations as a "risk vocabulary" (Mar 2026 post title) [11].
- A stateless, cacheable MCP suits hosted surfaces (chat apps, phones, web) where no local process exists. The deprecated features were the "local desktop" ones [5].
- MCP Apps lets a tool bring its own interface into a chat window. The chat app becomes an application platform, which is the single most surface-relevant change in this document (see section 6).

## 4. A2A: agent to agent

**Problem.** Two agents built on different frameworks need to delegate work without exposing internals. A2A (Google, repo created Mar 25, 2025) defines an Agent Card for discovery, long-running tasks, messages and artifacts, over JSON-RPC 2.0 with SSE streaming and push notifications, and says it "complements MCP" [20][38]. It is "an open source project under the Linux Foundation, contributed by Google"; the Linux Foundation announced the A2A project on Jun 23, 2025 at Open Source Summit North America, with AWS, Cisco, Google, Microsoft, Salesforce, SAP and ServiceNow as founding members (LF release, via secondary snippets) [20][95].

**Evolution.** v0.2.2 (Jun 2025) added gRPC and REST definitions and extensions; v0.3.0 (Jul 2025) added mTLS and signed Agent Cards; v1.0.0 (Mar 12, 2026) was a breaking release with three formally equivalent bindings (JSON-RPC, gRPC, HTTP+JSON), a restructured Agent Card supporting multiple protocol versions, `tasks/list` with cursor pagination, native multi-tenancy, OAuth device-code flow and required PKCE, and a versioned extension mechanism; v1.0.1 followed on May 28, 2026 [21][23].

**Backers and adoption.** The repository's partners page lists 172 organizations, including Accenture, Adobe, AWS, Atlassian, Auth0, Autodesk, Block, Bloomberg, Box, BCG, Capgemini and Cisco; the repo has 25.7k stars [22][38]. IBM's competing Agent Communication Protocol (BeeAI, created Apr 2025) was archived on Aug 27, 2025 with the notice "ACP is now part of A2A under the Linux Foundation" [24][38]. A partner list is an endorsement, not a deployment count; I found no independent measure of A2A traffic.

**Harness impact.** A2A standardizes the orchestration part: subagents can be remote services from other vendors, with tasks as the unit of work. For surfaces it is invisible to end users, but it is the plumbing that would let a consumer chat surface hand off to a merchant's or an enterprise's agent.

## 5. Zed's Agent Client Protocol: agent to editor

**Problem.** Every editor was writing bespoke integrations for every coding agent. ACP applies the Language Server Protocol idea: "A protocol for connecting any editor to any agent" over JSON-RPC, with the agent running as a subprocess of the client [25]. The repository was created on Jun 23, 2025 under Zed and now lives in a separate `agentclientprotocol` organization with official Rust, TypeScript, Python, Kotlin and Java SDKs, Apache-2.0 and no CLA; it has 4.2k stars [25][26][38].

**Adoption.** The agents page lists Claude Agent, Codex CLI, Gemini CLI, Cursor, GitHub Copilot, Goose, Cline, OpenCode, OpenHands, Factory Droid, Junie (JetBrains), Kimi CLI, Kiro CLI, Mistral Vibe, Qwen Code, Pi, OpenClaw, Hermes Agent and more; the registry repo holds 41 agent directories (8 of them quarantined for failing CI checks, per `quarantine.json`), including `devin`, `antigravity-acp`, `amp-acp`, `poolside` and `github-copilot`; the docs' agents page lists 40 [27][29][101]. Clients include Zed, JetBrains, Neovim (CodeCompanion, avante.nvim and others), Emacs (agent-shell), several VS Code extensions, Visual Studio (Poolside), Obsidian plugins, Sublime Text, Qt Creator, Unity and a Chrome extension [30]. Gemini CLI ships an `--acp` flag (the older `--experimental-acp` is deprecated) [34]. Anthropic's Claude Agent SDK is exposed to ACP clients through `claude-agent-acp` (2.5k stars) and OpenAI's Codex through `codex-acp` [26].

**Governance and direction.** On Feb 18, 2026 Sergey Ignatov of JetBrains joined Ben Brandt (Zed) as a second lead maintainer [31]. A transports working group (Apr 22, 2026, with Goose's team) is standardizing WebSocket and HTTP transports for remote agents [32]. The v2 draft (Jul 20, 2026) moves "beyond turn-based" sessions so background work can stream updates at any time, adds structured file changes and richer permission requests, and is not for production [33]. Version 1.0.0 of the schema shipped Jun 24, 2026; 1.7.0 (Aug 2026) stabilized terminal authentication and elicitation, and 0.13.0 (May 2026) added experimental "MCP-over-ACP" passthrough [28].

**Harness impact.** ACP makes the runtime swappable behind a surface: an editor can host Claude, Codex or Gemini through one integration. The transports work and the v2 background model are what would let non-editor surfaces (a phone, a web dashboard) drive the same agents.

## 6. Agent to user interface: five approaches

The tool layer has one standard; the UI layer has five. This is the least settled area and the one most relevant to a reimagined surface.

| | MCP Apps | OpenAI Apps SDK + ChatKit | AG-UI | A2UI | AI SDK generative UI |
|---|---|---|---|---|---|
| Backer | MCP maintainers (Anthropic, OpenAI, MCP-UI authors) | OpenAI | CopilotKit | Google (repo now under `a2ui-project`) | Vercel |
| First public | Nov 2025 proposal; spec Jan 26, 2026 [12][13] | Examples repo Oct 6, 2025; ChatKit repo Oct 4, 2025 [38] | Repo May 7, 2025 [38] | Repo Sep 24, 2025; announced by Google in Dec 2025 (Google Developers Blog, via secondary snippets; exact day unverified) [38][98] | RSC `streamUI` path (now marked experimental); tool-part rendering in current AI SDK UI docs [43][44] |
| Payload | HTML in a sandboxed iframe, JSON-RPC bridge [14] | HTML widget in ChatGPT; `window.openai` API for state, `callTool`, display modes [39] | ~16 event types over SSE, WebSocket or webhooks; state deltas [35] | Declarative JSON mapped to a client-side catalog of approved components; "safe like data, but expressive like code" [37] | Tool calls streamed as typed `tool-<name>` message parts rendered per state (`input-available`, `output-available`, `output-error`) [43] |
| Where it renders | Claude, ChatGPT, VS Code, Goose, Postman [13] | ChatGPT; ChatKit embeds a hosted chat in your app [39][41] | Your own web app; adapters for LangGraph, CrewAI, Microsoft Agent Framework, Google ADK, AWS Strands, Mastra, Pydantic AI, Agno, LlamaIndex, AG2; Claude Agent SDK via community [35] | Web (Lit), Flutter (GenUI SDK), Angular; transports via A2A and AG-UI [37] | React apps using `useChat` [43] |
| Maturity signal | 2.8k stars; live in production chat clients [13][38] | 2.3k stars on examples; 1.9k on ChatKit [38] | 15.8k stars; .NET and Java packages Aug 2026 [36][38] | 16.3k stars; v0.9.1 stable, v1.0 RC, "early stage public preview" [37][38] | RSC path "currently experimental", UI path recommended [44]; `ai` at 7.0.94 (Sep 2026) [45] |

Two facts matter most. First, the Apps SDK examples originally bound widgets with `_meta["openai/outputTemplate"]` (Oct 2025 code) and the README now documents `_meta.ui.resourceUri`, the MCP Apps key: OpenAI's app platform has converged on the shared extension rather than a proprietary one [14][39][40]. Second, the two agent-hosted approaches (MCP Apps, Apps SDK) put the interface *inside a chat surface* owned by a lab, while AG-UI, A2UI and the AI SDK put the agent *inside an interface you own*. A startup building a new surface lives on the second side of that line.

Vercel's AI SDK is also notable for naming the thing this program studies. Its new harness package defines a harness as "a complete agent runtime, such as Claude Code, Codex, or Pi. It owns capabilities that are larger than a model call: workspace access, built-in coding tools, native session state, compaction, permission flows, and runtime-specific configuration", and ships adapters such as `@ai-sdk/harness-claude-code` plus a terminal UI; the packages are "experimental" [42]. That is a third-party framework treating the labs' harnesses as embeddable backends.

## 7. Instructions and skills: AGENTS.md and SKILL.md

**AGENTS.md** is "a simple, open format for guiding coding agents", a Markdown file at the repo root (nested files allowed) that any agent reads before working. The site says it "emerged from collaborative efforts across the AI software development ecosystem, including OpenAI Codex, Amp, Jules from Google, Cursor" and others, and claims use by "over 60k open-source projects", a figure that links to a GitHub code search for `path:AGENTS.md NOT is:fork NOT is:archived` [47]. The compatibility list names Codex (OpenAI), Jules and Gemini CLI (Google), GitHub Copilot's coding agent, Devin and Windsurf (Cognition), UiPath, Junie (JetBrains) and 15 more [47]. The repo (created Aug 19, 2025) has 24.2k stars and was contributed by OpenAI as an AAIF founding project [7][38][46]. Anthropic's harness is built around `CLAUDE.md` (see the anatomy document in this program); whether Claude Code also honors AGENTS.md was not verified here, so "one instruction file" is not yet established.

**Agent Skills.** A skill is a folder with a `SKILL.md` whose frontmatter has a `name` (1 to 64 lowercase characters, matching the folder) and `description` (up to 1,024 characters), optional `license`, `compatibility`, `metadata` and experimental `allowed-tools`, plus optional `scripts/`, `references/` and `assets/`. Loading is staged: metadata (about 100 tokens) at startup, the body (under 5,000 tokens recommended) on activation, resources on demand [49]. Anthropic released it (anthropics/skills created Sep 22, 2025; now 175k stars and 20.7k forks) and published it as an open standard at agentskills.io on Dec 18, 2025 (spec repo created Dec 16, 2025; 25.2k stars; Simon Willison and Unite.AI coverage, secondary) [38][48][51][52][53]. The clients page lists 46 products, including Claude Code, Claude, "ChatGPT & Codex", Cursor, GitHub Copilot, VS Code, Gemini CLI, Goose, OpenCode, OpenHands, Amp, Factory, Kiro, Roo Code, Qodo, Tabnine, Databricks Genie Code, Snowflake Cortex Code, Spring AI, Hermes Agent and OpenClaw [50]. Gemini CLI extensions and Codex plugins both bundle skills [60][64].

**Harness impact.** Both formats move know-how out of the harness binary and into files that travel with the repo or the user. That is what makes procedures portable across harnesses, and it is why a skill written for Claude Code runs in Codex. For non-CLI surfaces the stakes are higher: a skill can carry a script and a rubric that a chat user never has to see (Anthropic's `k12-teacher-skills` repo, Jul 2026, is a non-developer example [38]).

## 8. Extension points inside the harness: hooks, plugins, marketplaces

**Hooks** are user-supplied handlers that run at lifecycle points of the loop. They are the mechanism by which permissions and policy become code rather than dialogs.

| Harness | Events (Sep 2026) | Handler types | Enterprise control |
|---|---|---|---|
| Claude Code | 30+ including `SessionStart`, `UserPromptSubmit`, `PreToolUse`, `PostToolUse`, `PermissionRequest`, `PermissionDenied`, `Stop`, `SubagentStart/Stop`, `TeammateIdle`, `PreCompact/PostCompact`, `WorktreeCreate`, `FileChanged`, `Elicitation`, `Setup` | `command`, `http`, `mcp_tool`, `prompt`, `agent`; can block (exit 2), allow/deny, rewrite tool input, inject context | Managed policy hooks cannot be disabled by users; cloud sessions on Claude Code web do not read local `~/.claude/settings.json` [54] |
| Codex | 12: `PreToolUse`, `PermissionRequest`, `PostToolUse`, `PreCompact`, `PostCompact`, `SessionStart`, `SessionEnd`, `UserPromptSubmit`, `SubagentStart`, `SubagentStop`, `Stop`, `Interrupt` | `hooks.json` in config or plugins | `allow_managed_hooks_only = true` in `requirements.toml` ignores user, project and session hooks [61][63] |
| Gemini CLI | 11: `SessionStart/End`, `BeforeAgent/AfterAgent`, `BeforeModel/AfterModel`, `BeforeToolSelection`, `BeforeTool/AfterTool`, `PreCompress`, `Notification` | shell commands returning JSON; exit 2 blocks | project, user and system `settings.json` tiers [59] |
| Cursor | `sessionStart`, `sessionEnd`, `beforeSubmitPrompt`, `preToolUse`, `postToolUse`, `postToolUseFailure`, `subagentStart/Stop`, `beforeShellExecution`, `afterShellExecution`, `beforeMCPExecution`, `afterMCPExecution`, `beforeReadFile`, `afterFileEdit`, `afterAgentResponse`, `afterAgentThought`, `preCompact`, `stop` (cursor.com docs via search snippets; `postCompact` appears only in Codex's Cursor-migration map, unverified) | not verified | not verified [62][96] |

The vocabulary converged on Claude Code's names. Codex's source contains a migration module that maps Cursor's camelCase events onto Codex's (`beforeSubmitPrompt` becomes `UserPromptSubmit`) [62], and an OpenAI Figma plugin ships a `PostToolUse` hook matching `Write|Edit`, the same shape as a Claude Code hook [64].

**Plugins and marketplaces** bundle skills, agents, hooks, MCP servers and more into an installable unit with a manifest and a catalog file.

| | Claude Code | Codex | Gemini CLI |
|---|---|---|---|
| Manifest | `.claude-plugin/plugin.json`; bundles `skills/`, `agents/`, `hooks/`, `.mcp.json`, `.lsp.json`, `monitors/`, `bin/`, `settings.json` [55] | `.codex-plugin/plugin.json`; bundles `skills/`, `.app.json`, `.mcp.json`, agents, commands, `hooks.json` [64] | `gemini-extension.json`; bundles prompts, MCP servers, commands, themes, hooks, sub-agents, skills [60] |
| Catalog | `.claude-plugin/marketplace.json`; sources: relative path, GitHub, git URL, git subdir, npm, archive, command [56] | `.agents/plugins/marketplace.json` [64] | install by GitHub URL; extension gallery [60] |
| Official supply | `claude-plugins-official` (292 entries across 14 categories: development, database, productivity, security, deployment and others, including Adobe, Google, AWS, Microsoft, Salesforce integrations) and `claude-community` (2,282 entries) [57][58] | `openai/plugins` (created Mar 4, 2026; 65 plugins in its default `marketplace.json` plus 49 in `api_marketplace.json`, including Figma, Notion, Linear, Slack, Stripe, Shopify, Supabase, Vercel); marketplace launched Mar 27, 2026 (secondary, unverified) [38][65][66][67][102] | gallery (count not verified) |
| Enterprise controls | `strictKnownMarketplaces`, `blockedMarketplaces`, org-level distribution that rejects top-level `bin/` [56] | policy files controlling pre-installed, available or blocked plugins (secondary) [67] | not verified |

**Harness impact.** Hooks and plugins are where the "thick harness" features (linting, approvals, compliance, telemetry) now live, as third-party code rather than product features. Two consequences for surfaces beyond the CLI: the enterprise controls are the part a non-technical organization actually needs, and they exist today only for terminal and desktop products; and Claude Code's note that web sessions ignore local settings shows that hooks do not yet travel cleanly from a laptop to a hosted surface [54].

## 9. Money: four overlapping commerce protocols

| Protocol | Backer, date | What it standardizes | Status and evidence |
|---|---|---|---|
| AP2 (Agent Payments Protocol) | Google; repo May 30, 2025; announced Sep 16, 2025 with "60+" partners including Mastercard, PayPal, American Express, Coinbase, Salesforce, Adyen, Worldpay, Etsy (secondary; unverified here, sources blocked) [38][70][71] | Six roles (shopping agent, credentials provider, merchant, merchant payment processor, "trusted surface", network and issuer) and three signed mandates (Intent, Cart, Payment) so a merchant can prove "verifiable intent, not inferred action"; human-present and human-not-present flows; an extension for A2A, MCP and UCP [69] | 3.2k stars; endorsements exceeded 100 by Oct 2025 (secondary) [38][71]; a separate `a2a-x402` extension adds crypto settlement [89] |
| x402 | Coinbase; repo Feb 21, 2025; x402 Foundation announced with Cloudflare Sep 23, 2025; the Linux Foundation reportedly announced the foundation's formation with 22 founding members on Apr 2, 2026 (date from one secondary snippet, the 22-member count from two; unverified) and announced its operational launch on Jul 16, 2026 with about 40 members, premier members including Adyen, AWS, American Express, Circle, Cloudflare, Coinbase, Fiserv, Google, Mastercard, Ripple, Shopify, Solana Foundation, Stripe and Visa (LF release via several secondary snippets; Microsoft is not among the members named) [38][73][74][76][91][92][93] | Revives HTTP 402: server replies "Payment Required" with options, client returns a signed payment header, a facilitator verifies and settles; "network, token, and currency agnostic" [72] | 6.6k stars [38]. Volume claims conflict: "119M transactions on Base and 35M on Solana" by Mar 2026 and "165M transactions, ~69,000 active agents, ~$50M cumulative" by late Apr 2026 (unverified: sources blocked; one tracker reports cumulative settlements flat at about $41.6M since Apr 2026 and another says the x402.org counter has not changed since Mar 2026), then "settlement volume down 93% year to date" with about half of transactions gamified per Artemis (Yahoo Finance and CryptoPotato) and roughly $28K daily real volume in mid-May 2026 (daily figure unverified) (all secondary) [74][75][76][94] |
| Agentic Commerce Protocol (ACP) | OpenAI and Stripe; launched Sep 29, 2025; releases Dec 12, 2025, Jan 16, Jan 30 and Apr 17, 2026 (capability negotiation, extensions and payment handlers, cart, feed, orders, authentication) [77] | OpenAPI specs for checkout and delegated payment; used for checkout inside ChatGPT (ChatGPT usage via secondary) [77] | "in beta" per the repo; reference implementations from both companies [77] |
| UCP (Universal Commerce Protocol) | Google; repo Dec 31, 2025; announced Jan 11, 2026 at NRF, co-developed with Shopify, Etsy, Wayfair, Target and Walmart, with 20+ backers including Adyen, American Express, Mastercard, Visa, Stripe (secondary, corroborated by TechCrunch and PYMNTS snippets; Google and Shopify posts blocked) [38][81][97] | Capabilities: checkout, OAuth 2.0 identity linking, order webhooks, payment token exchange; "transport agnostic" over REST, MCP or A2A; uses AP2 mandates for security [78] | 3.4k stars [38]; wired into Google Search's AI Mode and the Gemini app (secondary, corroborated by TechCrunch and PYMNTS snippets) [81][97] |

**Harness impact.** Payments are a permissions problem wearing a commerce costume: who authorized what, and can the merchant prove it. AP2's "trusted surface" is a UI requirement (a consent screen the agent cannot fake), which means any new surface that wants agents to buy things must implement it. The fragmentation (Google's AP2 and UCP versus OpenAI/Stripe's ACP, with x402 for machine-to-machine micropayments) and x402's volume collapse say the "agent economy" is early and vendor-aligned, not neutral infrastructure.

## 10. llms.txt: a cautionary standard

Proposed on Sep 3, 2024 by Jeremy Howard (Answer.AI), `/llms.txt` is a Markdown file (H1, blockquote summary, H2 link sections, an "Optional" section) that gives a model a curated map of a site; the README says OpenAI, Anthropic and Google publish it for their developer docs, that Chrome's Lighthouse audits for it, and that Mintlify generates it automatically [82]. Claude Code's own docs serve one (`code.claude.com/docs/llms.txt`) [55]. Independent evidence says consumers ignore it: Google's John Mueller wrote in Jun 2025 that "no AI system currently uses llms.txt" (unverified: no reachable source); an Ahrefs study of 137,000 domains (about 28% of which, a developer-skewed sample, published the file) found 97% of llms.txt files received zero requests (month of the log data unverified) and AI retrieval bots made about 1.1% of the requests that did occur; SE Ranking found no significant correlation with AI citations across 300,000 domains (its ~10% adoption figure is unverified) (all secondary) [83][84][85][99]. The lesson for every other standard here: a file format spreads on the publishing side long before, and sometimes without, the consuming side honoring it.

## 11. Summary matrix

| Standard | Problem solved | Backer | Adoption evidence (Sep 2026) | Harness part | Surface implication |
|---|---|---|---|---|---|
| MCP | one tool interface for all clients | AAIF (Anthropic, OpenAI, Block, Google, Microsoft, AWS, Cloudflare, Bloomberg) | ~500M SDK downloads/month (vendor); 10k servers (vendor); every major client | tools, context, permissions | stateless HTTP fits hosted and mobile surfaces; Apps turn chat into a platform |
| MCP Apps | tool-supplied UI | MCP maintainers, OpenAI, Anthropic | 5 hosts; 7 months old | surface | chat apps become app stores |
| A2A | agent delegation | Google, Linux Foundation | 172 partners listed; v1.0 Mar 2026 | orchestration | invisible plumbing for hand-offs |
| ACP (Zed) | any editor, any agent | Zed + JetBrains maintainers | 41 agents; 15+ client types | surface, runtime | runtime swappable behind a surface; remote transports coming |
| AG-UI | agent events into your app | CopilotKit | 15.8k stars; 10+ framework adapters | surface | build-your-own-surface path |
| A2UI | agent-generated declarative UI | Google | 16.3k stars; preview | surface | generative UI without executing agent code |
| Apps SDK / ChatKit | apps inside ChatGPT; ChatGPT-like chat in your app | OpenAI | examples 2.3k stars; converged on MCP Apps key | surface | lab-owned surface as platform |
| AI SDK generative UI + harnesses | React rendering of tool state; embed lab harnesses | Vercel | `ai` 7.x; harness packages experimental | surface, loop | third parties embedding lab runtimes |
| AGENTS.md | repo instructions | OpenAI, AAIF | "60k+" repos (self-reported search) | context | works only where a repo exists |
| Agent Skills | portable procedures | Anthropic, open spec | 46 clients; 175k-star example repo | context, tools | carries scripts and rubrics to non-developers |
| Hooks | policy as code | per-harness, converging names | Claude Code, Codex, Gemini CLI, Cursor | permissions, loop | do not yet follow users to hosted surfaces |
| Plugins and marketplaces | distribution of harness extensions | Anthropic, OpenAI, Google | 292 official and 2,282 community (Claude); 65 (Codex) | distribution | enterprise controls exist for terminal and desktop only |
| AP2, x402, ACP, UCP | agent payments | Google; Coinbase and Cloudflare; OpenAI and Stripe; Google and retailers | endorsements; x402 volume collapsed | permissions | consent surfaces become mandatory |
| llms.txt | site content for models | Answer.AI | ~10% publish (unverified; 28% in Ahrefs' sample); ~3% fetched | context | publication is not adoption |

## What this means for the thesis

**Supports.**

- The surface has been separated from the runtime by protocol, which is the precondition the thesis needs. ACP puts 41 agents behind one editor-side interface; MCP Apps and the Apps SDK put tool UIs inside chat; AG-UI, A2UI and the AI SDK put agents inside interfaces third parties own; MCP's 2026-07-28 release removed protocol sessions and deprecated the features (roots, sampling, logging) that assumed a stateful local client [5][13][27][35][37][42].
- The "thick harness" features have commoditized into shared formats. Hook event names, `SKILL.md`, `AGENTS.md` and plugin manifests are near-identical across Claude Code, Codex, Gemini CLI and Cursor [54][61][62][49][64]. When the features are the same everywhere, differentiation moves to the surface, the permission model and distribution, which is where the thesis says it should be.
- The most active new standards target non-CLI surfaces: MCP Apps hosts are chat apps and IDEs, commerce protocols target Search, Gemini and ChatGPT checkout, and ACP's transports work and v2 background sessions are designed for remote, always-on agents [13][32][33][81].
- Vercel now sells adapters that embed Claude Code as a backend under a different front end [42]. A reimagined surface does not have to rebuild the runtime.

**Contradicts.**

- The labs own both ends. MCP, A2A, the Apps SDK, A2UI, AP2, UCP, the OpenAI/Stripe ACP, AGENTS.md and Agent Skills were all created by Anthropic, OpenAI or Google, and the two most-used UI paths (MCP Apps in Claude, Apps SDK in ChatGPT) deliver third-party UI *into lab-owned chat surfaces*. Standards make a new surface buildable; the labs use the same standards to make their own surfaces platforms. A startup inherits the plugs but not the traffic.
- The UI protocols are immature relative to the tool protocol. MCP Apps is seven months old, A2UI is a preview, Vercel's harness packages are experimental, and AG-UI's evidence is stars and adapters rather than named production deployments [13][35][37][42]. Betting a product on any one of them today is betting on a draft.
- Protocol churn is a cost the thesis underweights. MCP made breaking transport changes roughly every six months; A2A v1.0 and the ACP v2 draft are both breaking [2][3][5][21][33]. A harness team spends real effort tracking specs, which favors incumbents with maintainers on the committees.
- Payments contradict "agents are ready for consumers." Four overlapping protocols, endorsement lists rather than transaction data, and x402's reported 93% volume drop with half its transactions gamified [75][76].
- Some standards are theater. llms.txt is published widely and consumed almost never [83][84][85]. Partner lists (172 for A2A, 60+ for AP2) are the same kind of evidence and should be read the same way.

**Nuance.**

- Code Mode and the stateless MCP turn suggest the tool layer thins as models write more code: the catalog stays, the per-call ceremony goes [5][86]. That is consistent with "better models need less harness" and with the harness's remaining weight sitting in sandboxing, permissions and surface.
- Hooks and enterprise plugin controls are where policy lives, and they are terminal-and-desktop artifacts today (Claude Code web ignores local settings) [54][56]. Whoever makes policy travel with the user across surfaces solves a problem the labs have not.

## Open questions and unverified claims

1. MCP registry general availability: the README still says preview despite v1.8.x releases; no GA announcement found. The v1.0.0 tag's year (2025) is inferred from the release sequence [15][16].
2. A2UI's exact announcement day and any named production adopters (repo created Sep 24, 2025; Google announced it in Dec 2025 per Google Developers Blog and SD Times snippets, primary post blocked) [37][38][98].
3. AG-UI: no verified list of production deployments, only framework adapters and stars [35].
4. x402 figures are mutually inconsistent across secondary sources ("$600M annualized" versus "~$50M cumulative" versus "flat at $41.6M"), and none could be opened; the Apr 2, 2026 announcement with 22 members rests on one or two secondary snippets, while the Jul 16, 2026 operational launch with about 40 members is corroborated by several [74][75][76][91][92][93][94].
5. UCP partner list and its use in Google Search AI Mode and Gemini: Google and Shopify posts were blocked; corroborated by TechCrunch and PYMNTS snippets [81][97].
6. OpenAI's ChatGPT app directory, submission and monetization policies: developers.openai.com blocked; not covered.
7. Vercel `ai@6.0.0` and `ai@7.0.0` dates (Dec 22, 2025 and Jun 25, 2026): confirmed against the npm registry's publish timestamps [45][90].
8. AAIF membership changes during 2026 and whether A2A, Agent Skills or x402 formally joined: aaif.io blocked.
9. The date A2A was transferred to the Linux Foundation: Jun 23, 2025 per the Linux Foundation release and SiliconANGLE (snippets; primary blocked) [20][95].
10. Codex's AGENTS.md discovery rules and skill directories: the repo docs link to blocked pages [88].
11. Cursor's hooks, plugins and marketplace: hook event names now taken from cursor.com docs snippets; handler types, plugins and marketplace still unverified [62][96].
12. Anthropic's "code execution with MCP" post (Nov 2025): blocked, cited from memory as unverified.
13. The Aug 22, 2026 "New MCP Roadmap" post: listed but not read [11].
14. MCP-UI adoption by Postman, Shopify and Hugging Face: vendor claim in the MCP blog [12].
15. The "60k open-source projects" AGENTS.md figure is a GitHub code-search count published by the project itself [47].
16. MCP core maintainers' employers: the governance document lists names without affiliations [9].

## Sources

1. MCP specification directory listing (versions 2024-11-05 to 2026-07-28, draft), https://github.com/modelcontextprotocol/modelcontextprotocol/tree/main/docs/specification, Sep 2026.
2. MCP changelog 2025-03-26, https://raw.githubusercontent.com/modelcontextprotocol/modelcontextprotocol/main/docs/specification/2025-03-26/changelog.mdx, Mar 2025.
3. MCP changelog 2025-06-18, https://raw.githubusercontent.com/modelcontextprotocol/modelcontextprotocol/main/docs/specification/2025-06-18/changelog.mdx, Jun 2025.
4. MCP changelog 2025-11-25, https://raw.githubusercontent.com/modelcontextprotocol/modelcontextprotocol/main/docs/specification/2025-11-25/changelog.mdx, Nov 2025.
5. MCP changelog 2026-07-28, https://raw.githubusercontent.com/modelcontextprotocol/modelcontextprotocol/main/docs/specification/2026-07-28/changelog.mdx, Jul 2026.
6. MCP blog, "The 2026-07-28 Specification", https://blog.modelcontextprotocol.io/posts/2026-07-28/, Jul 2026.
7. MCP blog, "MCP joins the Agentic AI Foundation", https://blog.modelcontextprotocol.io/posts/2025-12-09-mcp-joins-agentic-ai-foundation/, Dec 2025.
8. Linux Foundation press release, "Linux Foundation Announces the Formation of the Agentic AI Foundation (AAIF)", https://www.linuxfoundation.org/press/linux-foundation-announces-the-formation-of-the-agentic-ai-foundation, Dec 2025 (via secondary; domain blocked).
9. MCP governance document, https://raw.githubusercontent.com/modelcontextprotocol/modelcontextprotocol/main/docs/community/governance.mdx, Sep 2026.
10. MCP blog, "Understanding MCP Extensions", https://blog.modelcontextprotocol.io/posts/2026-03-11-understanding-mcp-extensions/, Mar 2026.
11. MCP blog post index (roadmap Aug 22, 2026; Enterprise-Managed Authorization Jun 18, 2026; maintainer update Apr 8, 2026; tool annotations Mar 16, 2026), https://blog.modelcontextprotocol.io/posts/, Sep 2026.
12. MCP blog, "MCP Apps: Extending servers with interactive user interfaces", https://blog.modelcontextprotocol.io/posts/2025-11-21-mcp-apps/, Nov 2025.
13. MCP Apps repository README, https://github.com/modelcontextprotocol/ext-apps, Sep 2026.
14. MCP Apps specification 2026-01-26, https://raw.githubusercontent.com/modelcontextprotocol/ext-apps/main/specification/2026-01-26/apps.mdx, Jan 2026.
15. MCP Registry README, https://github.com/modelcontextprotocol/registry, Sep 2026.
16. MCP Registry releases (v1.0.0 Sep 8, 2025; v1.8.1 Aug 6, 2026), https://github.com/modelcontextprotocol/registry/releases, Sep 2026.
17. The Register, "Claude supports MCP Apps, presents UI within chat window", https://www.theregister.com/2026/01/26/claude_mcp_apps_arrives/, Jan 2026 (secondary).
18. Inkeep, "Anthropic and OpenAI Join Forces to Standardize Interactive AI Interfaces with MCP Apps Extension", https://inkeep.com/blog/anthropic-openai-mcp-apps-extension, Nov 2025 (secondary).
19. Digital Applied, "MCP Adoption Statistics 2026", https://www.digitalapplied.com/blog/mcp-adoption-statistics-2026-model-context-protocol, 2026 (secondary).
20. A2A repository README, https://github.com/a2aproject/A2A, Sep 2026.
21. A2A releases (v0.2.1 to v1.0.1), https://github.com/a2aproject/A2A/releases, May 2026.
22. A2A partners list, https://raw.githubusercontent.com/a2aproject/A2A/main/docs/partners.md, Sep 2026.
23. A2A, "What's new in v1.0", https://raw.githubusercontent.com/a2aproject/A2A/main/docs/whats-new-v1.md, Mar 2026.
24. IBM/BeeAI Agent Communication Protocol repository (archived Aug 27, 2025), https://github.com/i-am-bee/acp, Aug 2025.
25. Agent Client Protocol README, https://github.com/agentclientprotocol/agent-client-protocol, Sep 2026.
26. Agent Client Protocol GitHub organization (registry, claude-agent-acp, codex-acp, SDKs), https://github.com/agentclientprotocol, Sep 2026.
27. ACP registry, https://github.com/agentclientprotocol/registry, Sep 2026.
28. ACP CHANGELOG, https://raw.githubusercontent.com/agentclientprotocol/agent-client-protocol/main/CHANGELOG.md, Aug 2026.
29. ACP docs, "Agents", https://raw.githubusercontent.com/agentclientprotocol/agent-client-protocol/main/docs/get-started/agents.mdx, Sep 2026.
30. ACP docs, "Clients", https://raw.githubusercontent.com/agentclientprotocol/agent-client-protocol/main/docs/get-started/clients.mdx, Sep 2026.
31. ACP announcement, "Sergey Ignatov joins as Lead Maintainer", https://raw.githubusercontent.com/agentclientprotocol/agent-client-protocol/main/docs/announcements/sergey-ignatov-lead-maintainer.mdx, Feb 2026.
32. ACP announcement, "Transports Working Group", https://raw.githubusercontent.com/agentclientprotocol/agent-client-protocol/main/docs/announcements/transports-working-group.mdx, Apr 2026.
33. ACP announcement, "ACP v2 draft", https://raw.githubusercontent.com/agentclientprotocol/agent-client-protocol/main/docs/announcements/acp-v2-draft.mdx, Jul 2026.
34. Gemini CLI reference (`--acp`, `--experimental-acp`), https://github.com/google-gemini/gemini-cli/blob/main/docs/cli/cli-reference.md, Sep 2026.
35. AG-UI repository README, https://github.com/ag-ui-protocol/ag-ui, Sep 2026.
36. AG-UI releases (Aug 2026), https://github.com/ag-ui-protocol/ag-ui/releases, Aug 2026.
37. A2UI repository README (google/A2UI, now a2ui-project/a2ui), https://github.com/google/A2UI, Sep 2026.
38. GitHub repository metadata (created_at, stargazers) via the GitHub API for the repositories cited, accessed 2026-09-08.
39. OpenAI Apps SDK examples README, https://github.com/openai/openai-apps-sdk-examples, Sep 2026.
40. OpenAI Apps SDK examples, `openai/outputTemplate` metadata in kitchen_sink_server_python/main.py and READMEs, https://github.com/openai/openai-apps-sdk-examples/blob/main/kitchen_sink_server_python/main.py, Oct 2025.
41. ChatKit README, https://github.com/openai/chatkit-js, Sep 2026.
42. Vercel AI SDK docs, "AI SDK Harnesses: Overview", https://raw.githubusercontent.com/vercel/ai/main/content/docs/03-ai-sdk-harnesses/01-overview.mdx, Sep 2026.
43. Vercel AI SDK docs, "Generative User Interfaces", https://raw.githubusercontent.com/vercel/ai/main/content/docs/04-ai-sdk-ui/04-generative-user-interfaces.mdx, Sep 2026.
44. Vercel AI SDK docs, "AI SDK RSC: Overview", https://raw.githubusercontent.com/vercel/ai/main/content/docs/05-ai-sdk-rsc/01-overview.mdx, Sep 2026.
45. Vercel `ai` releases (ai@6.0.0, ai@7.0.0, ai@7.0.94), https://github.com/vercel/ai/releases, Sep 2026.
46. AGENTS.md repository, https://github.com/agentsmd/agents.md, Sep 2026.
47. agents.md site source: Hero.tsx ("over 60k open-source projects"), AboutSection.tsx (origins), CompatibilitySection.tsx (supported tools), https://github.com/agentsmd/agents.md/tree/main/components, Sep 2026.
48. Agent Skills specification repository README, https://github.com/agentskills/agentskills, Sep 2026.
49. Agent Skills specification, https://raw.githubusercontent.com/agentskills/agentskills/main/docs/specification.mdx, Sep 2026.
50. Agent Skills client showcase data, https://raw.githubusercontent.com/agentskills/agentskills/main/docs/snippets/clients.jsx, Sep 2026.
51. anthropics/skills repository, https://github.com/anthropics/skills, Sep 2026.
52. Simon Willison, "Agent Skills", https://simonwillison.net/2025/Dec/19/agent-skills/, Dec 2025 (secondary).
53. Unite.AI, "Anthropic Opens Agent Skills Standard", https://www.unite.ai/anthropic-opens-agent-skills-standard-continuing-its-pattern-of-building-industry-infrastructure/, Dec 2025 (secondary).
54. Claude Code docs, "Hooks", https://code.claude.com/docs/en/hooks, Sep 2026.
55. Claude Code docs, "Create plugins", https://code.claude.com/docs/en/plugins, Sep 2026.
56. Claude Code docs, "Create and distribute a plugin marketplace", https://code.claude.com/docs/en/plugin-marketplaces, Sep 2026.
57. Anthropic official plugin marketplace catalog, https://raw.githubusercontent.com/anthropics/claude-plugins-official/main/.claude-plugin/marketplace.json, Sep 2026.
58. Anthropic community plugin marketplace catalog, https://raw.githubusercontent.com/anthropics/claude-plugins-community/main/.claude-plugin/marketplace.json, Sep 2026.
59. Gemini CLI docs, "Hooks", https://raw.githubusercontent.com/google-gemini/gemini-cli/main/docs/hooks/index.md, Sep 2026.
60. Gemini CLI docs, "Extensions", https://raw.githubusercontent.com/google-gemini/gemini-cli/main/docs/extensions/index.md, Sep 2026.
61. Codex hooks source (event names), https://github.com/openai/codex/blob/main/codex-rs/hooks/src/lib.rs, Sep 2026.
62. Codex Cursor-hooks migration source, https://github.com/openai/codex/blob/main/codex-rs/external-agent-migration/src/hooks_cur.rs, Sep 2026.
63. Codex docs, "Configuration" (lifecycle hooks, managed hooks), https://github.com/openai/codex/blob/main/docs/config.md, Sep 2026.
64. OpenAI Codex plugins repository README, https://github.com/openai/plugins, Sep 2026.
65. OpenAI Codex plugins directory (65 plugins listed in the marketplace file; see source 102), https://github.com/openai/plugins/tree/main/plugins, Sep 2026.
66. The New Stack, "OpenAI's Codex gets plugins", https://thenewstack.io/openais-codex-gets-plugins/, Mar 2026 (secondary).
67. WinBuzzer, "OpenAI Launches Plugin Marketplace for Codex with Enterprise Controls", https://winbuzzer.com/2026/03/31/openai-launches-plugin-marketplace-codex-enterprise-controls-xcxwbn/, Mar 2026 (secondary).
68. AP2 repository README, https://github.com/google-agentic-commerce/AP2, Sep 2026.
69. AP2 overview (roles, mandates, flows), https://raw.githubusercontent.com/google-agentic-commerce/AP2/main/docs/overview.md, Sep 2026.
70. Digital Commerce 360, "Google launches payments protocol for AI commerce, names dozens of partners", https://www.digitalcommerce360.com/2025/09/19/google-ai-payments-protocol-ap2/, Sep 2025 (secondary).
71. Eco, "AP2 (Agent Payments Protocol) Explained", https://eco.com/support/en/articles/14845479-ap2-agent-payments-protocol-explained, 2026 (secondary).
72. x402 repository README (coinbase/x402, development fork of x402-foundation/x402), https://github.com/coinbase/x402 and https://github.com/x402-foundation/x402, Sep 2026.
73. Coinbase, "Coinbase and Cloudflare Will Launch the x402 Foundation", https://www.coinbase.com/blog/coinbase-and-cloudflare-will-launch-x402-foundation, Sep 2025 (secondary; snippet only).
74. CoinDesk, "AI agents are breaking web economics, but Cloudflare says x402 can help", https://www.coindesk.com/tech/2026/05/05/ai-agents-are-breaking-web-economics-but-cloudflare-says-x402-can-help, May 2026 (secondary).
75. Yahoo Finance, "x402 Settlement Volume Plunges 93% YTD", https://finance.yahoo.com/markets/crypto/articles/x402-settlement-volume-plunges-93-105710906.html, 2026 (secondary).
76. Presenc AI, "x402 Protocol Adoption Tracker 2026", https://presenc.ai/research/x402-protocol-adoption-tracker-2026, 2026 (secondary).
77. Agentic Commerce Protocol (OpenAI and Stripe) repository README, https://github.com/agentic-commerce-protocol/agentic-commerce-protocol, Apr 2026.
78. Universal Commerce Protocol repository README, https://github.com/Universal-Commerce-Protocol/ucp, Sep 2026.
79. Google Developers Blog, "Under the Hood: Universal Commerce Protocol (UCP)", https://developers.googleblog.com/under-the-hood-universal-commerce-protocol-ucp/, Jan 2026 (blocked; via secondary).
80. Shopify Engineering, "Building the Universal Commerce Protocol", https://shopify.engineering/UCP, Jan 2026 (blocked; via secondary).
81. Lengow, "Google's Universal Commerce Protocol: What It Changes", https://blog.lengow.com/googles-universal-commerce-protocol-the-end-of-e-commerce-as-we-know-it/, 2026 (secondary).
82. llms.txt repository README (proposal, format, adopters), https://github.com/AnswerDotAI/llms-txt, Sep 2026.
83. PPC Land, "llms.txt adoption rises 8.8x but 97% of files get zero AI requests", https://ppc.land/llms-txt-adoption-rises-8-8x-but-97-of-files-get-zero-ai-requests/, 2026 (secondary).
84. Digital Applied, "llms.txt in Practice: Adoption Data, Evidence, and Setup", https://www.digitalapplied.com/blog/llms-txt-in-practice-adoption-evidence-2026, 2026 (secondary).
85. 1ClickReport, "llms.txt in 2026: The Evidence Says It Does Nothing", https://www.1clickreport.com/blog/llms-txt-evidence-2026, 2026 (secondary).
86. Cloudflare Agents, "codemode" package README, https://github.com/cloudflare/agents/tree/main/packages/codemode, Sep 2026.
87. Bitcoin.com News, "MCP in 2026: 97 Million Downloads", https://news.bitcoin.com/mcp-in-2026-97-million-downloads-and-growing-crypto-infrastructure-from-bitgo-to-coingecko/, 2026 (secondary).
88. OpenAI Codex docs directory (agents_md.md, skills.md link out to blocked pages), https://github.com/openai/codex/tree/main/docs, Sep 2026.
89. A2A x402 extension repository, https://github.com/google-agentic-commerce/a2a-x402, Sep 2026.
90. npm registry metadata for `ai` (publish times for 6.0.0, 7.0.0 and 7.0.94), https://registry.npmjs.org/ai, accessed 2026-09-08.
91. Linux Foundation press release, "Linux Foundation Announces Operational Launch of x402 Foundation to Standardize Internet-Native Payments for AI Agents and Applications", https://www.linuxfoundation.org/press/linux-foundation-announces-operational-launch-of-x402-foundation-to-standardize-internet-native-payments-for-ai-agents-and-applications, Jul 2026 (blocked, as are the x402.org and PR Newswire mirrors; via search snippets).
92. CoinDesk, "Visa, Stripe and Google join massive open-source project to let AI agents pay each other", https://www.coindesk.com/business/2026/07/16/ai-payments-have-a-new-open-standards-body-its-aim-is-to-reinvent-the-internet, Jul 16, 2026; TechTimes, "Visa, Mastercard, and Stripe Back Open Standard Letting AI Agents Pay Autonomously", https://www.techtimes.com/articles/320813/20260717/visa-mastercard-stripe-back-open-standard-letting-ai-agents-pay-autonomously.htm, Jul 17, 2026; Stellagent, "x402 Foundation Goes Live with Visa, Mastercard and 40 Members", https://stellagent.ai/insights/x402-foundation-launch-visa-mastercard, 2026 (all secondary; blocked, snippets only).
93. Coinlaw, "Linux Foundation Launches x402 Foundation for AI Payments", https://coinlaw.io/linux-foundation-x402-foundation-launch/, and AgentPayTrend, "22 x402 Foundation Members: 5 Strategic Blocs", https://agentpaytrend.com/x402-foundation-members-5-strategic-blocs/, 2026 (secondary; blocked, snippets only; source of the Apr 2, 2026 date and the 22-member count).
94. CryptoPotato, "x402 Volume Plunges 93% YTD as Agentic AI Economy Hype Fades", https://cryptopotato.com/x402-volume-plunges-93-ytd-as-agentic-ai-economy-hype-fades/, 2026; Blockchain.News, "x402: Settlements Flat at $41.6M Since April", https://blockchain.news/flashnews/x402-settlements-flat-41-6m-since-april, 2026; Daniel McGlynn, "x402 Volume Numbers Are Wrong: The Counter Everyone Quotes Hasn't Moved Since March", https://www.danielmcglynn.com/the-x402-counter-has-shown-the-same-four-numbers-since-march/, 2026 (all secondary; blocked, snippets only).
95. Linux Foundation press release, "Linux Foundation Launches the Agent2Agent Protocol Project to Enable Secure, Intelligent Communication Between AI Agents", https://www.linuxfoundation.org/press/linux-foundation-launches-the-agent2agent-protocol-project-to-enable-secure-intelligent-communication-between-ai-agents, Jun 23, 2025 (blocked; via snippets, including the PR Newswire/Morningstar copy dated 2025-06-23); SiliconANGLE, "Google donates Agent2Agent Protocol to the Linux Foundation", https://siliconangle.com/2025/06/24/google-donates-agent2agent-protocol-linux-foundation/, Jun 24, 2025 (secondary; snippet only).
96. Cursor docs, "Hooks", https://cursor.com/docs/hooks, Sep 2026 (blocked; event names via search snippets).
97. TechCrunch, "Google announces a new protocol to facilitate commerce using AI agents", https://techcrunch.com/2026/01/11/google-announces-a-new-protocol-to-facilitate-commerce-using-ai-agents/, Jan 11, 2026; PYMNTS, "Google Debuts 'Universal' Protocol for Agentic Commerce", https://www.pymnts.com/google/2026/google-debuts-universal-protocol-for-agentic-commerce/, Jan 2026 (secondary; blocked, snippets only).
98. Google Developers Blog, "Introducing A2UI: An open project for agent-driven interfaces", https://developers.googleblog.com/introducing-a2ui-an-open-project-for-agent-driven-interfaces/, Dec 2025 (blocked); SD Times, "Google launches A2UI project to enable agents to build contextually relevant UIs", https://sdtimes.com/ai/google-launches-a2ui-project-to-enable-agents-to-build-contextually-relevant-uis/, Dec 2025; MarkTechPost, "Google Introduces A2UI (Agent-to-User Interface)", https://www.marktechpost.com/2025/12/22/google-introduces-a2ui-agent-to-user-interface-an-open-sourc-protocol-for-agent-driven-interfaces/, Dec 22, 2025 (secondary; blocked, snippets only).
99. Ahrefs, "We Analyzed 137K Sites: 97% of llms.txt Files Never Get Read", https://ahrefs.com/blog/llmstxt-study/, 2026; Search Engine Journal, "97% Of llms.txt Files Got No Requests, Ahrefs Data Shows", https://www.searchenginejournal.com/97-of-llms-txt-files-got-no-requests-ahrefs-data-shows/579478/, 2026 (blocked; snippets only).
100. MCP blog, "MCP Apps - Bringing UI Capabilities To MCP Clients", https://blog.modelcontextprotocol.io/posts/2026-01-26-mcp-apps/, Jan 26, 2026; VS Code blog, "Giving Agents a Visual Voice: MCP Apps Support in VS Code", https://code.visualstudio.com/blogs/2026/01/26/mcp-apps-support, Jan 26, 2026 (snippet only).
101. ACP registry `quarantine.json` and `FORMAT.md`, https://raw.githubusercontent.com/agentclientprotocol/registry/main/quarantine.json, Sep 2026.
102. OpenAI Codex plugin marketplace files, https://raw.githubusercontent.com/openai/plugins/main/.agents/plugins/marketplace.json and https://raw.githubusercontent.com/openai/plugins/main/.agents/plugins/api_marketplace.json, Sep 2026.

## Verification notes (2026-09-08)

**Method.** Checked on 2026-09-08 through the session's network proxy. Reachable: raw.githubusercontent.com, rendered github.com pages, registry.npmjs.org, blog.modelcontextprotocol.io and code.claude.com. The GitHub REST API (and github.com requests with a JSON `Accept` header) were refused by the proxy, so directory counts, release dates and star counts come from raw files, rendered pages and the npm registry rather than from the API. The web-search allowance was exhausted after ten searches (the session counter was already near its cap), so every later check used direct fetches, and most secondary domains were blocked (list below). Where a claim rests on search-result snippets alone, the table says so; where two or more independent snippets agree, the verdict is "confirmed (secondary snippets)".

### Claims checked

| # | Claim in the document | Verdict | Evidence |
|---|---|---|---|
| 1 | Five MCP spec revisions, 2024-11-05 to 2026-07-28 | confirmed | `docs/docs.json` in the spec repo lists exactly 2024-11-05, 2025-03-26, 2025-06-18, 2025-11-25 and "2026-07-28 (latest)" plus Draft; each changelog names the previous revision [1]-[5] |
| 2 | 2026-07-28 removed protocol sessions and the initialization handshake; deprecated roots, sampling and logging | confirmed | Changelog: SEP-2567 (sessions and `Mcp-Session-Id`), SEP-2575 (handshake, `server/discover`, `subscriptions/listen`, SSE resumability), "Deprecate the Roots, Sampling, and Logging features" (SEP-2577); it also removes `ping`, `logging/setLevel` and `notifications/roots/list_changed` [5] |
| 3 | "close to half-a-billion downloads a month"; >1B total each for TypeScript and Python; four Tier 1 SDKs with Rust in beta | confirmed (vendor figures) | MCP blog, Jul 28, 2026, by David Soria Parra and Den Delimarsky; the "nearly 20%" partner is Honeycomb [6] |
| 4 | MCP Apps went live as the first official extension on Jan 26, 2026 | confirmed | MCP blog of Jan 26, 2026 ("MCP Apps are now live as an official MCP extension"; Claude, Goose, VS Code Insiders, ChatGPT that week); VS Code blog the same day (snippet) [100] |
| 5 | MCP donated to AAIF on Dec 9, 2025; co-founded by Anthropic, Block and OpenAI with Google, Microsoft, AWS, Cloudflare and Bloomberg; goose and AGENTS.md founding projects; 97M monthly downloads, 10,000 servers, named clients | confirmed | MCP blog post of Dec 9, 2025 [7]. The platinum-member list attributed to the LF release remains unverified (linuxfoundation.org blocked) [8] |
| 6 | Maintainer team expanded in Apr 2026 | confirmed, detail added | Blog index: Apr 8, 2026, Clare Liguori joins the core maintainers and Den Delimarsky becomes lead maintainer [11] |
| 7 | Registry v1.0.0 on Sep 8 (2025) and v1.8.1 on Aug 6, 2026 | confirmed | Release pages show "08 Sep" and "06 Aug"; GitHub omits the year and the release sequence fixes it [16] |
| 8 | ACP registry holds 54 agents | corrected to 41 | Two fetches of the rendered repo tree list the same 41 agent directories; `quarantine.json` marks 8 of them as failing CI; the docs' agents page lists 40 (including OpenHands, Kiro CLI, Hermes Agent and OpenClaw, which have no registry directory) [27][29][101] |
| 9 | JetBrains co-lead Feb 2026; transports working group Apr 2026; v2 draft Jul 20, 2026; schema 1.0.0 Jun 24, 2026; 1.7.0 Aug 2026; 0.13.0 with MCP-over-ACP May 2026 | confirmed | Announcement files dated Feb 18, Apr 22 and Jul 20, 2026; CHANGELOG: 1.0.0 2026-06-24, 1.7.0 2026-08-20, 0.13.0 2026-05-12 [28][31][32][33] |
| 10 | A2A v1.0.0 on Mar 12, 2026 and v1.0.1 on May 28, 2026 | confirmed | Releases page ("12 Mar", "28 May"; v0.3.0 "30 Jul" 2025 fixes the years) [21] |
| 11 | A2A partners page lists 186 organizations | corrected to 172 | `docs/partners.md` has 172 bulleted entries (A2A Net to zyprova) [22] |
| 12 | A2A transfer date to the Linux Foundation not found | corrected: Jun 23, 2025 | LF release "Linux Foundation Launches the Agent2Agent Protocol Project" (Open Source Summit North America; founding members AWS, Cisco, Google, Microsoft, Salesforce, SAP, ServiceNow) via LF, PR Newswire/Morningstar (2025-06-23) and SiliconANGLE (Jun 24, 2025) snippets; none openable [95] |
| 13 | IBM's ACP archived Aug 27, 2025 with the "now part of A2A" notice | confirmed | GitHub archive banner and notice [24] |
| 14 | SKILL.md clients page lists 46 products | confirmed | `clients.jsx` has 46 `name:` entries [50] |
| 15 | anthropics/skills at 175k stars | confirmed | Rendered GitHub page: 175k stars, 20.7k forks [51] |
| 16 | Claude plugin catalogs: 200+ official, 500+ community | corrected | `marketplace.json`: 292 official entries (14 categories) and 2,282 community entries, all with distinct names [57][58] |
| 17 | Codex: 73 plugin folders | corrected to 65 | `.agents/plugins/marketplace.json` lists 65 plugins with 65 distinct `./plugins/...` paths; `api_marketplace.json` lists 49; the rendered tree page summaries gave 62 to 66 folders and were not reliable enough to quote [65][102] |
| 18 | Codex marketplace launched Mar 27, 2026 | unverified | The New Stack and WinBuzzer blocked; the WinBuzzer URL is dated Mar 31, 2026 [66][67] |
| 19 | `ai@6.0.0` Dec 22, 2025; `ai@7.0.0` Jun 25, 2026; `ai@7.0.94` Sep 2026 | confirmed | npm registry publish times 2025-12-22T17:11Z and 2026-06-25T12:47Z; 7.0.94 published 2026-09-08 and tagged `latest` [90] |
| 20 | UCP announced Jan 11, 2026 at NRF; co-developed with Shopify, Etsy, Wayfair, Target and Walmart; 20+ backers including Adyen, American Express, Mastercard, Visa and Stripe; buying inside Google Search's AI Mode and the Gemini app | confirmed (secondary snippets) | TechCrunch (Jan 11, 2026), PYMNTS, Lengow and others agree; none openable. Snippets add that Cart and Product Discovery capabilities landed Mar 19, 2026 and a cross-retailer Universal Cart on May 20, 2026 (single snippet; not added to the text) [81][97] |
| 21 | Cursor hook event names | corrected | cursor.com/docs/hooks snippets list sessionStart, sessionEnd, preToolUse, postToolUse, postToolUseFailure, subagentStart, subagentStop, beforeShellExecution, afterShellExecution, beforeMCPExecution, afterMCPExecution, beforeReadFile, afterFileEdit, beforeSubmitPrompt, afterAgentResponse, afterAgentThought, stop and preCompact; `postCompact` appears only in Codex's migration map [62][96] |
| 22 | Gemini CLI extension manifest name | corrected (filled in) | `gemini-extension.json` in `docs/extensions/reference.md` and `writing-extensions.md` [60] |
| 23 | Hook counts: Codex 12, Gemini CLI 11, Claude Code 30+ with five handler types and the managed-hook and cloud-session quotes | confirmed | `codex-rs/hooks/src/lib.rs` (12 names), Gemini `docs/hooks/index.md` (11 names), code.claude.com hooks page (32 events; `command`, `http`, `mcp_tool`, `prompt`, `agent`; both quotes verbatim) [54][59][61] |
| 24 | A2UI announcement date | unverified (day); month confirmed | Google Developers Blog "Introducing A2UI", SD Times and MarkTechPost (Dec 22, 2025) snippets place it in Dec 2025; all blocked. The repo has no GitHub releases; its README confirms v0.9.1 and "early stage public preview" [37][98] |
| 25 | x402 Foundation formalized under the Linux Foundation on Apr 2, 2026 with 22 members including AWS, Google, Microsoft, Stripe, Visa, Mastercard and Shopify | corrected | Coinlaw and AgentPayTrend snippets say 22 companies formed the foundation on Apr 2, 2026 (date in one snippet, count in two; unverified). The LF release, CoinDesk (Jul 16, 2026), TechTimes (Jul 17), Stellagent and Logicity report the operational launch on Jul 16, 2026 with about 40 members. Premier members named: Adyen, AWS, American Express, Circle, Cloudflare, Coinbase, Fiserv, Google, Mastercard, Monad Foundation, MoonPay, Ripple, Shopify, Solana Foundation, Stellar Development Foundation, Stripe and Visa; general members include Aleo, Fireblocks, KakaoPay, LayerZero Labs, NEAR Foundation, Polygon Labs and zerohash. Microsoft is not named. The `x402-foundation/tsc` repo lists one unaffiliated representative plus Cloudflare and Stripe, and four working groups, but no formation date [91][92][93] |
| 26 | x402 settlement volume down 93% year to date; about half of transactions gamified | confirmed as reported (secondary) | Yahoo Finance and CryptoPotato headlines and snippets; Artemis is the analysis behind the "gamified" share [75][94] |
| 27 | x402 transaction and dollar figures (119M on Base and 35M on Solana; 165M transactions, ~69,000 agents, ~$50M cumulative; $28K daily; "$600M annualized") | unverified | CoinDesk, Presenc, Chainalysis, Blockchain.News and agenteconomy.to blocked. Snippets cut against the cumulative figures: Blockchain.News "Settlements Flat at $41.6M Since April"; Daniel McGlynn "the counter everyone quotes hasn't moved since March"; Chainalysis titles its analysis "100M Agentic Payments on Base" [74][76][94] |
| 28 | llms.txt: Ahrefs, 137,000 domains, 97% of files got zero requests, AI retrieval bots about 1% of requests | confirmed (secondary snippets) | Ahrefs blog and Search Engine Journal snippets; about 28% of the (technical, Ahrefs-customer) sample published the file; roughly 38,000 valid files, about 1,100 with any traffic [83][99] |
| 29 | The Ahrefs data is from May 2026 | unverified | No snippet gives the log month; Ahrefs, SEJ and PPC Land blocked [83][99] |
| 30 | SE Ranking: ~10% adoption across 300,000 domains and no correlation with AI citations | unverified (~10%); correlation finding confirmed | Snippets confirm the 300,000-domain no-correlation finding but none states an adoption share; seranking.com blocked [84][85] |
| 31 | John Mueller, Jun 2025, "no AI system currently uses llms.txt" | unverified | No reachable source; consistent with the checker's recollection of his Jun 2025 posts but not confirmed [83]-[85] |
| 32 | OpenAI/Stripe ACP launched Sep 29, 2025 with releases Dec 12, 2025, Jan 16, Jan 30 and Apr 17, 2026; in beta | confirmed | Repository README directory layout and status badge [77] |
| 33 | AP2 announced Sep 16, 2025 with 60+ partners | unverified | Digital Commerce 360 and ap2-protocol.org blocked; the AP2 README does not name partners [68][70][71] |
| 34 | agents.md claims "over 60k open-source projects" | confirmed | `components/Hero.tsx` [47] |
| 35 | Apps SDK examples README documents `_meta.ui.resourceUri` | confirmed | README, line 30 [39] |
| 36 | x402 repository at 6.6k stars | confirmed | Organization page shows 6,588 stars [38] |

**Tally:** 21 confirmed, 8 corrected, 7 unverified (36 claims).

### Corrections made in the text

1. Status line changed to "verified with notes".
2. ACP registry: 54 agents became 41 agent directories (8 quarantined; 40 on the docs' agents page) in the TL;DR, section 5, the summary matrix and the thesis section; the lead-maintainer and transports-group dates were made exact (Feb 18 and Apr 22, 2026).
3. A2A: 186 partners became 172 (diagram, section 4, summary matrix, thesis section); the Linux Foundation transfer date (Jun 23, 2025) and founding members were filled in (section 4, open question 9).
4. Plugins: Claude official 200+ became 292 and community 500+ became 2,282; Codex 73 folders became 65 marketplace entries plus 49 API-marketplace entries; the Mar 27, 2026 marketplace launch date is marked unverified (section 8 table, summary matrix).
5. Hooks: the Cursor row now lists the events documented by cursor.com (via snippets) instead of the names inferred from Codex's migration code, with `postCompact` marked unverified; the Gemini CLI manifest name `gemini-extension.json` was filled in.
6. x402: the governance cell now separates the single-sourced Apr 2, 2026 announcement (22 founding members) from the corroborated Jul 16, 2026 operational launch (about 40 members), lists the premier members as reported, and notes that Microsoft is not among them; the transaction and dollar figures are marked unverified with the contrary snippets; open question 4 rewritten.
7. llms.txt: the ~10% adoption figure is marked unverified, Ahrefs' 28% sample share added, the "May 2026" month marked unverified, and the Mueller quote marked unverified (TL;DR, section 10, summary matrix).
8. A2UI: announcement narrowed to Dec 2025 with the day unverified (section 6 table, open question 2).
9. UCP: corroboration by TechCrunch and PYMNTS snippets noted (section 9 table, open question 5).
10. Vercel: open question 7 now records the npm confirmation of both release dates.
11. MCP: the Apr 8, 2026 maintainer change is described (section 3.1) and the "20%" partner named as Honeycomb (section 3.5).
12. AP2: the row is marked "unverified here" because no source could be opened.
13. Sources 90 to 102 added for the material above.

### Sources that could not be opened

- Blocked by the proxy: api.github.com (and JSON requests to github.com); x402.org; linuxfoundation.org; prnewswire.com; coindesk.com; finance.yahoo.com; cryptopotato.com; coinlaw.io; agentpaytrend.com; stellagent.ai; logicity.in; techtimes.com; presenc.ai; blockchain.news; chainalysis.com; agenteconomy.to; danielmcglynn.com; blog.cloudflare.com; ahrefs.com; searchenginejournal.com; ppc.land; 1clickreport.com; digitalapplied.com; seranking.com; nohacks.co; tryvizup.com; rabbitrank.com; eagleactivator.com; developers.googleblog.com; sdtimes.com; marktechpost.com; the-decoder.com; copilotkit.ai; hia2ui.com; royfactory.net; thenewstack.io; winbuzzer.com; techcrunch.com; pymnts.com; blog.lengow.com; retailgentic.com; indexbox.io; ucp.dev; ap2-protocol.org; digitalcommerce360.com; cursor.com; docs.cursor.com; ntorres.dev; blog.gitbutler.com; truefoundry.com; petralian.com; agentclientprotocol.com; cdn.agentclientprotocol.com; a2a-protocol.org.
- Opened: raw.githubusercontent.com files (MCP changelogs and `docs.json`, ACP CHANGELOG and announcements, A2A `partners.md`, Agent Skills `clients.jsx`, Claude and Codex marketplace files, Gemini CLI docs, Codex hook sources, AGENTS.md `Hero.tsx`, ACP registry `quarantine.json`, x402 TSC README, A2UI README, Apps SDK README, OpenAI/Stripe ACP README); rendered github.com pages (anthropics/skills, i-am-bee/acp, release pages for A2A, the MCP registry and Vercel `ai`, the ACP registry and openai/plugins trees, the x402-foundation organization); registry.npmjs.org/ai; blog.modelcontextprotocol.io (posts of Dec 9, 2025, Jan 26, 2026 and Jul 28, 2026, and the post index); code.claude.com/docs/en/hooks.

### Remaining doubts

- Every x402 number is secondary and the sources disagree with each other; the Apr 2, 2026 event may be an "intent to form" announcement rather than a formalization, and neither the LF release nor any tracker could be opened.
- The ACP registry count is the repository's directory count; the published `registry.json` on the CDN (blocked) may differ, for example by excluding the 8 quarantined agents.
- The A2A partner count is from the repository file; the project website (blocked) may list a different number.
- Star counts other than anthropics/skills and x402 were not re-checked because the GitHub API was unavailable; the MCP "10,000 active servers" and the ~5,800 registry tally were not re-checked either.
- The Ahrefs 28% publication share is from a developer-skewed sample and is not comparable with SE Ranking's unverified ~10%.
- Cursor's handler types, plugins and marketplace, the Codex marketplace launch date, AP2's launch partner count, the Mueller quote and the exact A2UI announcement day remain unverified.
