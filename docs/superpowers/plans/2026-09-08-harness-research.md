# Harness Research Program: Execution Plan

> **For agentic workers:** this is a research plan, not a code plan. Each task produces one sourced Markdown document. Execute with fresh subagents per task (superpowers: subagent-driven-development), verify each document with a second agent, then synthesize. Checkboxes track progress.

**Goal:** a verified research corpus and a startup-grade recommendation for reimagining the default agent harness.

**Architecture:** research fan-out (one agent per document) → verification pass (one fact-checker per document) → matrices → coordinator synthesis with a red-team critique.

**Tooling:** Agent tool for direct dispatch; Workflow tool for research→verify pipelines; WebSearch/WebFetch for sources; the Anthropic Economic Index MCP tools for usage data.

**Spec:** `docs/superpowers/specs/2026-09-08-harness-research-design.md`

## Global constraints

- Every research agent receives `docs/superpowers/plans/research-brief-template.md` verbatim, followed by its scope block below.
- Agents write only their own file and never run git. The coordinator commits, authored as `aryanmudgal-tech`.
- Documents are dated 2026-09-08 and follow the template format.
- Word targets are guidance, not limits; sources and correctness beat length.

---

## Phase 1: Research fan-out

### Task 1: Primer, anatomy of a harness
**File:** `docs/01-primer/anatomy.md`
**Scope:** Explain what an agent harness is from first principles. Start with the loop and a minimal code sketch (about 30 lines, Python or TypeScript) of an agentic tool loop. Then show how a production harness differs, part by part: tool design; context management (compaction, summarization, subagent isolation, just-in-time retrieval); memory (instruction files like CLAUDE.md and AGENTS.md, memory tools, persistent notes); permissions (allow lists, approval prompts, sandboxes, "yolo" modes); runtime (local vs cloud, durability, background execution); surfaces (CLI, IDE, desktop, web, chat, voice, OS); orchestration (subagents, workflows, scheduled and background runs). Use open-source harnesses as concrete references and cite their code or docs: OpenAI Codex CLI, Google Gemini CLI, OpenHands, pi (Mario Zechner's minimal agent), Thorsten Ball's "How to build an agent" essay, the Claude Agent SDK documentation. Include a table: what each anatomy part does and what goes wrong without it. One section on the "thin vs thick harness" tension: what tends to move into the model over time and what stays outside. 2,500 to 3,500 words.

### Task 2: Primer, history 2022 to 2026
**File:** `docs/01-primer/history.md`
**Scope:** A dated timeline of how harnesses evolved from 2022 to September 2026, and what each step changed. Candidates to verify and place: ReAct and Toolformer; ChatGPT plugins; AutoGPT and BabyAGI; LangChain and LlamaIndex; OpenAI function calling; SWE-agent and its "agent-computer interface"; Devin (2024); Cursor's rise; Claude computer use (Oct 2024); the Model Context Protocol (Nov 2024); Claude Code (2025); Codex CLI and Codex cloud (2025); Gemini CLI; ChatGPT agent; Manus; AGENTS.md; Agent Skills; Anthropic's "effective harnesses for long-running agents"; the OpenClaw (formerly Clawdbot, Moltbot) personal-agent phenomenon (early 2026); Claude Cowork; OpenAI's "harness engineering" essay; the Codex desktop app; Anthropic Managed Agents; and anything from March to September 2026 that a web search surfaces. For each: what changed in the harness and why it mattered. Include which big ideas failed and why (AutoGPT-style autonomy, Rabbit r1, Humane AI Pin, Adept). End with "the five shifts" the history shows, in your own words. 2,500 to 3,500 words.

### Task 3: Developer harnesses from the labs
**File:** `docs/02-landscape/developer-harnesses-labs.md`
**Scope:** Profile the first-party developer harnesses: Anthropic (Claude Code CLI, desktop app, web and cloud sessions, IDE extensions, Claude Agent SDK, Claude Cowork, Claude in Chrome, Claude in Slack, mobile; Managed Agents), OpenAI (Codex CLI, Codex desktop app, Codex cloud, IDE extension, Slack and GitHub integrations, Agents SDK, AgentKit), Google (Gemini CLI, Jules, Antigravity, Gemini Code Assist), GitHub (Copilot coding agent, agent mode, Copilot CLI, Agent HQ), Amazon (Kiro, Q Developer), and any other lab-built harness a search surfaces (xAI, Mistral, Meta). For each: the seven anatomy parts; extensibility (MCP, skills, hooks, plugins, marketplaces); pricing model; open-source status; notable design choices; stated roadmap. Test one observation explicitly: are the labs converging on "one runtime, many surfaces"? Include a comparison table. 3,000 to 4,000 words.

### Task 4: Independent developer harnesses
**File:** `docs/02-landscape/developer-harnesses-independent.md`
**Scope:** Profile independent and open-source harnesses: Cursor (IDE, background and cloud agents, own models), Cognition (Devin, Windsurf), OpenHands, Amp (Sourcegraph), Cline, Roo Code, Kilo, Aider, Factory (Droid), Warp, Zed and its Agent Client Protocol, opencode, Replit Agent, Lovable, Bolt, v0, Augment, Qodo, Continue; the "personal agent" harnesses: OpenClaw (formerly Clawdbot and Moltbot), Hermes Agent (Nous Research), pi, NanoClaw; and tools that orchestrate other harnesses (Conductor, Vibe Kanban, Terragon, Superset, and others you can verify). For each: the seven anatomy parts as far as public information allows; which model provider it depends on; business model; open-source status; what is distinctive. Close with what is converging and what is still differentiated. Comparison table. 3,000 to 4,000 words.

### Task 5: Developer harness matrix (depends on Tasks 3 and 4)
**File:** `docs/02-landscape/developer-harness-matrix.md`
**Scope:** Read `docs/02-landscape/developer-harnesses-labs.md` and `docs/02-landscape/developer-harnesses-independent.md`. Produce one comparison matrix across the seven anatomy parts plus surfaces, extensibility, pricing and open-source status. Then three sections: what every harness has converged on (candidates: instruction files, MCP, permission modes, subagents, background or cloud runs, git worktrees, plan modes); what is still contested; gaps that nobody fills. Add a short section on which patterns transfer to non-technical users and which do not. 1,500 to 2,500 words. Only use facts present in the two source documents or that you verify yourself.

### Task 6: Non-technical surfaces from the big vendors
**File:** `docs/02-landscape/non-technical-labs.md`
**Scope:** What a non-technical person can delegate to an agent today through the big vendors, and how it is packaged: OpenAI (ChatGPT agent mode, the former Operator, apps in ChatGPT via the Apps SDK, scheduled tasks, Pulse, voice, the Atlas browser), Anthropic (claude.ai, Claude Cowork, Claude in Chrome, Claude in Slack, Claude for Excel and PowerPoint, mobile, Claude Design), Google (Gemini app, Gemini in Workspace, agentic browsing in Chrome, Gemini controlling Android apps), Microsoft (Copilot in Microsoft 365, Copilot Studio, Agent 365, Windows agent features), Apple (Siri and Apple Intelligence, App Intents), Amazon (Alexa+), Meta AI. For each: which surface it lives in; what it can actually do end to end; how approvals and trust work; evidence of usage or adoption; known failures, incidents or backlash. Comparison table. 3,000 to 4,000 words.

### Task 7: Non-technical surfaces from startups and enterprise vendors
**File:** `docs/02-landscape/non-technical-startups-enterprise.md`
**Scope:** Same treatment for: Manus (and its acquisition), Perplexity Comet, The Browser Company's Dia (and its acquisition), Poke by The Interaction Company, Lindy, Zapier Agents, n8n, Relevance AI, Dust, Glean, Notion Agents, Salesforce Agentforce, ServiceNow, Sierra and Decagon (as harnesses operated by non-technical teams), Genspark, Fellou, H Company's Runner H, Convergence, plus the cautionary cases: Adept (to Amazon), Rabbit r1, Humane AI Pin (to HP). For each: surface, what it can do end to end, approvals and trust, business model, traction numbers with sources, notable failures. Comparison table. 3,000 to 4,000 words.

### Task 8: Adoption evidence
**File:** `docs/02-landscape/adoption-evidence.md`
**Scope:** Who uses agents, for what, through which surfaces, and how that splits between developers and everyone else. Use the Anthropic Economic Index MCP tools (the `econ_index_*` tools: dataset overview, top work tasks, occupation usage, global and country usage) and cite them as "Anthropic Economic Index, accessed 2026-09-08". Then web sources: the Anthropic Economic Index reports, OpenAI's "How people use ChatGPT" paper (Sep 2025), Microsoft Work Trend Index, McKinsey State of AI, the MIT NANDA "GenAI Divide" report, Stack Overflow Developer Survey 2025 and 2026, DORA 2025, the METR developer productivity study (Jul 2025) and any follow-ups, Ramp AI Index, Menlo Ventures reports, and any 2026 updates. Answer directly: what share of agent use runs through chat surfaces versus terminals or IDEs; where autonomous (agentic) use is growing fastest; what the data says about the assumption that CLI and desktop apps are the default. Present numbers in tables. 2,000 to 3,000 words.

### Task 9: Protocols and standards
**File:** `docs/03-building-blocks/protocols.md`
**Scope:** The standards that shape harnesses: Model Context Protocol (spec evolution, governance under the Agentic AI Foundation and Linux Foundation, the MCP Apps UI extension, the registry, adoption figures), Agent2Agent (A2A), Zed's Agent Client Protocol (ACP) and IBM's former ACP, AG-UI (CopilotKit), A2UI (Google), OpenAI Apps SDK and ChatKit, Vercel AI SDK generative UI, AGENTS.md, the Agent Skills (SKILL.md) standard, hooks, plugin marketplaces, payment protocols (AP2, x402), the Universal Commerce Protocol, llms.txt. For each: the problem it solves, who backs it, adoption evidence, how it changes the harness, and what it means for surfaces beyond the CLI. Include a diagram of how the protocols relate. 2,500 to 3,500 words.

### Task 10: Runtime, safety and infrastructure
**File:** `docs/03-building-blocks/runtime-and-safety.md`
**Scope:** The infrastructure under a harness: sandboxes (E2B, Modal, Daytona, Cloudflare Sandboxes, Firecracker, gVisor, Apple's container tooling, Anthropic's open-source sandbox runtime); computer use and browser use (Anthropic computer use, OpenAI CUA, Browser Use, Playwright MCP, Stagehand); durable execution (Temporal, Inngest, Restate, DBOS); memory (Mem0, Letta, Zep, file-based memory, Anthropic's memory tool); context engineering (compaction, subagent isolation, just-in-time retrieval); permission models (allow lists, auto modes, approval fatigue); security (prompt injection, Simon Willison's "lethal trifecta", incidents in Comet and Atlas, OpenClaw exposure incidents, MCP tool poisoning); observability and evals (LangSmith, Braintrust, Terminal-Bench, SWE-bench Verified and Pro, OSWorld, tau-bench, HAL); cost and latency realities. For each area: state of maturity, and what it implies for a harness serving non-technical users. 3,000 to 4,000 words.

### Task 11: Academic research
**File:** `docs/04-research/academic.md`
**Scope:** Research that bears on whether harness design can be automated or learned, and how much harness remains as models improve: SWE-agent (agent-computer interface); ADAS and Meta Agent Search; AFlow; AgentSquare; the Darwin Gödel Machine; Voyager; LATM (LLMs as tool makers); CREATOR; Agent Workflow Memory; surveys on self-evolving agents (2025); 2026 work on automated harness or scaffold search (search terms: "meta-harness", "automated harness design", "agent scaffold optimization"); DSPy and GEPA; generative UI research (Google's generative UI work, late 2025, and adaptive-interface papers); HCI on human-agent interaction (mixed-initiative interfaces, Microsoft HAX, Google PAIR guidebook, agentic UX papers); METR's task-horizon measurements. For each: the claim, the evidence, and what it implies for the thesis. 2,500 to 3,500 words.

### Task 12: Industry engineering writing
**File:** `docs/04-research/industry-engineering.md`
**Scope:** What practitioners who build harnesses say. Anthropic: "Building effective agents", the multi-agent research system post, "Writing tools for agents", "Effective context engineering", the Claude Agent SDK post, "Effective harnesses for long-running agents", Agent Skills, Claude Code best practices, Managed Agents. OpenAI: "Harness engineering" (2026), "A practical guide to building agents", Codex engineering posts. Cognition: "Don't build multi-agents". Cursor engineering posts. Manus's context-engineering post. Thorsten Ball's "How to build an agent". Simon Willison's writing on agents and harnesses. Latent Space coverage. The debate over the Bitter Lesson applied to harnesses (do harnesses thin out as models improve, or is the harness the moat?). Extract the principles practitioners agree on and the points where they disagree, with quotes and links. 2,500 to 3,500 words.

### Task 13: Surface deep dive, messaging
**File:** `docs/05-surfaces/messaging.md`
**Scope:** The agent as a coworker you message. Cover Slack (Claude in Slack, Codex in Slack, Agentforce, Slack's own agents), Microsoft Teams and Copilot, WhatsApp, Telegram and iMessage agents (the OpenClaw phenomenon, Poke, Meta AI in WhatsApp, ChatGPT in WhatsApp), email agents (Lindy, Fyxer, Superhuman, Shortwave), Discord bots. Evaluate: which tasks fit; the asynchronous and background pattern; approvals inside chat; attachments and artifacts; security (who may message the agent, injection via messages); identity; group contexts; limits (no rich UI, weak undo); adoption evidence. End with a verdict for developers and a verdict for non-technical users. 2,000 to 3,000 words.

### Task 14: Surface deep dive, browser workspace with generated UI
**File:** `docs/05-surfaces/browser-generated-ui.md`
**Scope:** The agent builds the interface it needs for the task. Cover Claude artifacts, Cowork and Claude Design; ChatGPT canvas and Apps SDK apps; Google's generative UI in Gemini; v0, Lovable, Bolt and Replit as "UI on demand"; Manus; Notion Agents; MCP Apps; AG-UI; A2UI; Vercel AI SDK; spreadsheets as a harness (Claude for Excel, Shortcut, Paradigm); canvas and whiteboard agents (tldraw, Figma Make). Evaluate: when the agent should build the interface versus use a fixed one; consistency versus novelty; trust and verification affordances; technical maturity; cost. Verdict for developers and for non-technical users. 2,000 to 3,000 words.

### Task 15: Surface deep dive, voice and ambient
**File:** `docs/05-surfaces/voice-ambient.md`
**Scope:** Voice-first and always-on agents, and background agents that interrupt only when needed. Cover OpenAI voice and the Realtime API, ChatGPT voice, Gemini Live, Alexa+, the Siri revamp, Meta's Ray-Ban glasses, wearables (Limitless, Bee, Friend), the Humane and Rabbit failures, background and scheduled agents (Claude Code routines, Codex scheduled tasks, Cowork scheduled tasks, ChatGPT tasks and Pulse), proactive agents (Poke). Evaluate: which tasks suit voice; latency and confirmation problems; ambient and proactive patterns and the cost of interruption; privacy. Verdict for developers and for non-technical users. 2,000 to 3,000 words.

### Task 16: Surface deep dive, OS-level integration
**File:** `docs/05-surfaces/os-level.md`
**Scope:** The agent woven into the operating system. Cover Windows (MCP in Windows, the "agentic OS" positioning and the late-2025 backlash, Copilot Actions, agent workspace), macOS (Apple Intelligence, App Intents, Shortcuts, the Siri revamp), Android (Gemini controlling apps), iOS, Chrome and ChromeOS (Gemini in Chrome, auto-browse), Anthropic computer use and Claude in Chrome, OpenAI Operator and Atlas, Perplexity Comet, agent-native browsers and desktops from startups, Raycast, Warp; accessibility APIs versus pixel-based control. Evaluate: OS-level permission models; security incidents; what the OS vendors will own and what they leave open to third parties. Verdict for developers and for non-technical users. 2,000 to 3,000 words.

### Task 17: Users, developers
**File:** `docs/06-users/developers.md`
**Scope:** What developers need from a harness, with evidence. Jobs to be done; how developers use agents today (Stack Overflow Developer Survey 2025 and 2026, JetBrains surveys, DORA 2025, the METR randomized study of July 2025 and follow-ups, Anthropic's report on how its own engineers use Claude Code, GitHub Octoverse 2025, Cursor and Claude Code usage disclosures); pain points with current harnesses (context loss, permission fatigue, verification burden, cost surprises, multi-agent chaos, the review bottleneck); what developers value in the CLI; emerging workflows (parallel agents in git worktrees, background agents, pull-request-driven loops, orchestration tools). Close with: what would have to be true for developers to leave the terminal, and what they would refuse to give up. 2,000 to 3,000 words.

### Task 18: Users, non-technical
**File:** `docs/06-users/non-technical.md`
**Scope:** What non-technical people need from a harness, with evidence. Who they are (operations, sales, finance, legal, marketing, founders, students); what they delegate today (Anthropic Economic Index tasks via the `econ_index_*` MCP tools, OpenAI's ChatGPT usage paper); barriers (trust, verification, permissions they do not understand, data access and IT policy, cost, the blank-page problem, prompting skill); evidence from enterprise rollouts (MIT NANDA, McKinsey, Microsoft Work Trend Index, Gartner's forecasts on cancelled agent projects); HCI research on trust and delegation; what "vibe coding" taught about non-developers building software (Lovable, Replit, Bolt user evidence); accessibility. End with an explicit list of what a harness must do differently for this audience. 2,000 to 3,000 words.

### Task 19: Strategy, value capture
**File:** `docs/07-strategy/value-capture.md`
**Scope:** Where value accrues in the agent stack (model, harness, surface, distribution, data) and what that means for a harness startup. Evidence: revenue disclosures for Cursor, Cognition, Lovable, Replit; traction of Claude Code and Codex; open-source harness traction (OpenClaw, OpenHands); lab behaviour (building surfaces themselves: Cowork, Codex app, apps in ChatGPT; acquisitions such as Windsurf, Manus, Dia); the "wrapper" debate; platform risk (rate limits, terms of service on third-party harnesses using consumer subscriptions, verify the OpenClaw-related policy changes); pricing models (seat, usage, outcome); enterprise procurement realities; investor theses on agents and harnesses (a16z, Sequoia, Menlo, Bessemer); analogies from previous platform shifts (browsers, mobile app stores). Blunt final section: for each candidate startup position (open harness runtime, non-technical surface, orchestration of many harnesses, vertical harness), answer "why would a lab not just do this?" No market sizing. 2,500 to 3,500 words.

## Phase 2: Verification (one agent per document from Phase 1)

**Verifier scope (same for every document; substitute the path):** You are a fact-checker for a research program on AI agent harnesses. Today is 2026-09-08. Read `<path>`. Identify the 10 to 15 highest-stakes factual claims (dates, numbers, product capabilities, acquisitions, quotes, attributions). Verify each against primary sources with WebSearch and WebFetch. Fix errors in place with minimal edits that keep the author's structure. Mark claims you cannot verify as "(unverified)" inline. Append a section `## Verification notes (2026-09-08)` listing each claim checked with a verdict (confirmed, corrected, unverified), the corrections made, and remaining doubts. Change the status line at the top to "Status: verified with notes". Do not run git. Do not edit any other file. Reply with counts of confirmed, corrected and unverified claims and the three most important corrections.

- [ ] Verify Tasks 1 to 19

## Phase 3: Synthesis (coordinator)

- [ ] `docs/08-synthesis/principles.md`: design principles for a default harness, each traced to evidence in the corpus.
- [ ] `docs/08-synthesis/reimagined-harness.md`: the proposed harness across the seven anatomy parts; architecture diagram; how each of the four surfaces plugs in; what is thin and what is thick as models improve.
- [ ] `docs/08-synthesis/recommendation.md`: the startup position, the wedge user, why the labs would not crush it, what would falsify it, first experiments to run.
- [ ] `docs/08-synthesis/red-team.md`: a separate agent attacks the recommendation; unresolved objections stay published.
- [ ] `docs/sources.md`: consolidated bibliography.
- [ ] `README.md`: navigation updated.
- [ ] Published report artifact for sharing.

## Execution assignment

| Tasks | Mechanism |
|-------|-----------|
| 1, 2, 19 (+ their verification) | Direct subagents |
| 3, 4 → 5, 6, 7, 8, 9, 10 (+ verification) | Workflow A: landscape and building blocks |
| 11 to 18 (+ verification) | Workflow B: research, surfaces, users |
| Synthesis | Coordinator, with one red-team subagent |

Commit after each phase lands, authored as the repo owner.
