# Runtime, Safety and Infrastructure Under the Harness

*Research program: agent harnesses. Written 2026-09-08. Status: verified with notes.*

## What this document answers

- What infrastructure sits under a harness in September 2026 (sandboxes, computer and browser control, durable execution, memory, context management, permission models, security controls, observability and evals) and how mature each layer is.
- Which layers the model providers now ship themselves, which are commodities, and which are still unsolved.
- What each layer implies for a harness serving non-technical people, who cannot write allowlists, read a diff, or judge a permission prompt.
- Where the evidence supports and where it contradicts the thesis that the harness, not the model, is the bottleneck.

## TL;DR

- Sandboxing is a commodity. Firecracker boots a microVM in under 125 ms with under 5 MiB overhead [10]; E2B, Modal, Cloudflare, Apple and Anthropic all ship isolation, and Anthropic lists eleven partner runtimes for self-hosted Managed Agents sandboxes (shipped 27 May 2026) [23][24]. Daytona, the most-starred sandbox project (71.8k stars), moved core development to a private codebase in June 2026 [5]. Isolation is not a moat.
- Prompt injection is unsolved and every vendor says so. Anthropic: Claude "sometimes follows instructions found in page content even when they conflict with yours" [28]; OpenAI's CISO: "unlikely to ever be fully solved" (Dec 2025, via secondary) [72]. Incidents hit Perplexity Comet (Aug 2025), ChatGPT Atlas (Oct 2025), GitHub's MCP server (May 2025) and OpenClaw (Jan to Feb 2026: tens of thousands of exposed instances and a CVSS 8.8 RCE) [68][71][79][74].
- The defenses that work are harness-level: OS sandboxes, egress proxies that hold credentials outside the boundary, and a second model that reviews actions. Anthropic reports sandboxing cut permission prompts by 84% (vendor, via secondary) [107]; its auto-mode classifier has been the starting permission mode for new sessions on Pro, Max and Team plans since 14 Aug 2026 [62][113].
- Approval fatigue is measured: users approve 93% of prompts (coverage of the Aug 2026 default announcement says 97%; which figure is current is unverified); humans caught 13.6% of planted dangerous commands versus 89% for the classifier (143 versus 937 of 1,053; vendor figures, via secondary) [65]. Everyone converged on "replace the human prompt with a machine judge plus a hard boundary."
- Memory is the least settled layer. Vendors report LoCoMo scores from 74% (a plain filesystem, Letta) to 92.5% (Mem0, Apr 2026) on different setups [48][45]. Anthropic ships server-side memory stores with versioning and a background "dreaming" consolidation job in research preview [53][54]. No independent evidence ties any of it to task outcomes.
- Evals fail exactly where non-technical work lives. SWE-bench Verified is contamination-prone and self-reported; SWE-bench Pro's standardized public scores (59 to 62%) trail vendor-reported ones (80%+) by 20 points [90]; OSWorld-Verified scores now exceed the 72.36% human baseline (official results reach 90.19%) [94][114]; HAL, the one cost-controlled leaderboard, was archived in July 2026 [98]. Nothing measures "did the ops manager's task get done."
- The labs are absorbing the runtime. Managed Agents bundles loop, sandbox, memory, tracing and scheduling at token rates plus $0.08 per session-hour [25]; compaction, context editing and injection classifiers moved server-side. This supports "the harness is the bottleneck" and undercuts "an independent harness can own the runtime."

## 1. The stack under a harness

**Analogy.** A harness is the cockpit; this document is about the airframe, the fuel system and the air-traffic rules. Pilots (developers) tolerate switches. Passengers (everyone else) need the switches hidden and the safety systems automatic.

```
  SURFACES      CLI . IDE . desktop . web . chat app . phone . browser extension
                  |            (one set of cloud environments serves all of them [21])
  HARNESS       loop . tools . permission rules . classifier . compaction . subagents
                  |
  RUNTIME   +-----v-----------------------------------------------------------+
            | sandbox (Seatbelt / bubblewrap / gVisor / Firecracker / VM)      |
            |   egress proxy: domain allowlist, credential injection, logging |
            |   memory store . session state . scheduler (routines)            |
            +-----------------------------------------------------------------+
                  |
  MODEL API   context editing . server-side compaction . injection probes . tool sets
                  |
  EVALS       traces (LangSmith, Braintrust) . benchmarks (Terminal-Bench, SWE-bench,
              OSWorld, tau-bench) . cost accounting (HAL)
```

## 2. Sandboxes: where the agent's code runs

**Analogy.** A rental car with a speed governor and a geofence: the driver can go anywhere inside the fence, and the car refuses to leave it, whatever the driver intends. **Precisely:** an isolation boundary enforced by the OS or a hypervisor, plus a network proxy that decides which hosts are reachable. It works even when the model is fooled, which is why it matters more than prompt-level defenses.

| Runtime | Isolation | Startup claim | License / status | Notes (Sep 2026) |
|---|---|---|---|---|
| Firecracker (AWS) | KVM microVM, jailer, seccomp [11] | "<= 125 ms" to guest init, "<= 5 MiB" overhead [10] | Apache-2.0 | Underlies Lambda and Fargate [11] and E2B (via secondary) [3] |
| gVisor (Google) | User-space kernel (`runsc`) [12] | Container-like | Apache-2.0 | GKE, Cloud Run [12]; "small operations impose a large overhead" [13]; simple syscalls about 2x slower, heavy file I/O "10-200x slower" per Anthropic's guide [19] |
| E2B | Firecracker (infra repo has a `firecracker` directory; via secondary) [2][3] | About 150 ms (secondary) [3] | Apache-2.0, 13.7k stars [1] | Self-host on GCP, AWS beta [2]; about $0.166/hour for 2 vCPU / 4 GiB (secondary) [3] |
| Modal Sandboxes | gVisor (secondary) [4] | Sub-second | Proprietary; Claude Managed Agents integration (vendor post, blocked) [103] | Lovable and Quora as customers (vendor via secondary); about $0.047 per vCPU-hour (secondary) [4] |
| Daytona | OCI containers, "dedicated kernel" [5] | "under 90ms" [5] | 71.8k stars; "no longer maintained. As of June 2026, Daytona's core development has moved to a private codebase" [5] | $24M Series A, Feb 2026 (secondary) [6] |
| Cloudflare | One container per sandbox [7]; Dynamic Workers on V8 isolates, under 5 ms (secondary) [8] | ms | SDK "Beta," Apache-2.0 [7]; product page blocked [105]; Dynamic Workers open beta Mar 2026 [8] | $0.002 per unique Worker per day, waived in beta (secondary) [9] |
| Apple `container` | One lightweight VM per Linux container [14] | "sub-second" (secondary) [15] | Apache-2.0; macOS 26; 1.0.0 on the project's first anniversary, 9 Jun 2026 [14][15] | Adds `container machine`, a persistent Linux environment [14] |
| Anthropic `sandbox-runtime` | Seatbelt (macOS), bubblewrap (Linux), dedicated user plus WFP (Windows); HTTP/SOCKS egress proxy [16] | Process-level, no container | Apache-2.0, "research preview developed for Claude Code" [16] | Documents its holes: domain fronting bypasses the proxy; Linux nested mode "considerably weakens security" [16] |

Two patterns recur. **Credentials live outside the boundary:** Claude Code on the web keeps GitHub tokens in a proxy that swaps a scoped credential for the real one [20]; the Bash sandbox shows commands a sentinel value and injects the real secret at the proxy [17]; the Agent SDK guide prescribes the same for any deployment [19]. **Isolation is a ladder:** Claude Code documents six rungs from sandboxed Bash to hosted VM, and says the per-command sandbox "is not sufficient for fully unattended runs" [18].

**Maturity: high and commoditizing.** Anthropic's self-hosted sandbox docs list guides for "AWS Lambda MicroVMs, Blaxel, Cloudflare, Daytona, E2B, Fly.io, GKE Agent Sandbox, Modal, Namespace, Superserve, and Vercel" [23]; Harbor runs on "Daytona, Modal, LangSmith, Blaxel, Novita Sandbox, Tensorlake, and Runta" [85]; Docker gives away a microVM product [18]. When the most popular open project goes closed-source while the labs list a dozen interchangeable suppliers, the layer is a utility.

**For non-technical users.** A hosted VM behind a default-deny proxy is now cheap and standard ($0.08 per session-hour on Managed Agents [25]; no separate compute charge on Claude Code on the web [20]), so a consumer harness inherits strong isolation for free. It cannot inherit the configuration: `~/.srt-settings.json`, `allowedDomains`, dev containers and `autoMode.environment` are developer artifacts. The boundary has to be derived from the task ("this agent may touch your calendar and this one folder"), not written by the user.

## 3. Computer use and browser use

**Analogy.** Computer use is an assistant working your screen through a webcam and a robot hand. Browser use gives the same assistant the page's outline (the accessibility tree), so it can say "click Submit" instead of "click at (412, 833)."

- *Anthropic.* `computer_toolset_20260801` works from screenshots plus a `zoom` action (about 4,500 tokens of tool definitions per request) [27][25]. `browser_toolset_20260801` reads pages "through its structure (the accessibility tree, elements, forms, and tabs)" and through pixels; the browser "runs in your environment" and "nothing runs on Anthropic's side" [28]. Claude Code drives native macOS apps in a research preview (Pro and Max only) with per-app approval and the terminal excluded from screenshots so on-screen prompts cannot feed back into the model [29]. Its docs rank options by precision: MCP, then Bash, then Chrome, then screen control, "the broadest and slowest" [29].
- *OpenAI.* The Computer-Using Agent shipped as Operator on 23 Jan 2025 at 38.1% OSWorld, 58.1% WebArena, 87% WebVoyager (vendor, via secondary; openai.com is blocked here) [32]. Operator was folded into ChatGPT agent in Jul 2025 (unverified); the Atlas browser launched 21 Oct 2025 [71].
- *Open source.* Browser Use (113.5k stars, MIT; $17M seed, Mar 2025) pairs DOM extraction with an LLM over Playwright/CDP [33][34]. Playwright MCP (36.9k stars) exposes the accessibility tree so the model works "purely on structured data" [35]. Stagehand (24.2k stars, MIT) offers `act`, `observe`, `extract` with "hybrid accessibility tree trimming" [36].

**Benchmarks.** OSWorld's human baseline is 72.36% (secondary) [95]; OSWorld-Verified landed 28 Jul 2025 [92]. Vendor-reported 2026 scores exceed the human line: Sonnet 5 at 81.2%, Opus 4.8 at 83.4% (secondary; not on the official results file) [94]. The official leaderboard page [93] was unreachable, but its GitHub-hosted results file (entries through 1 Aug 2026) lists Intelligence-Indeed Agent at 90.19%, claude-fable-5 at 85.96%, Pointer Agent with Opus 4.7 at 83.64% and claude-opus-5 at 83.39%, all at 100 steps [114]; the 86.1% Qwen figure from an aggregator does not appear there, where the best Qwen entry is 73.30% (unverified) [95].

**Maturity: medium.** Perception improved fast; the safety posture did not move. Anthropic still prescribes "a dedicated virtual machine or container with minimal privileges," no logins, a domain allowlist, and "a human to confirm decisions that might result in meaningful real-world consequences," with a classifier that steers the model to ask for confirmation when it detects injection in a screenshot [27]. Claude in Chrome launched with attack success cut from 23.6% to 11.2% in autonomous mode (Aug 2025, vendor via secondary) [31]; 11.2% is not a consumer number.

**For non-technical users.** This is the layer they most want (it works on any app) and the worst risk profile (it sees everything they see and holds their logins). Shipping products all answer with a pause: Claude Code's Chrome integration "pauses and asks you to handle it manually" at logins and CAPTCHAs [30]; Atlas added "logged out mode" and a "watch mode" requiring the tab to stay active on sensitive sites (secondary) [72]. A non-technical harness should prefer structured access (a scoped connector) and use screen control only for the residue, which is Anthropic's own ordering [29].

## 4. Durable execution

**Analogy.** A video game with autosave: lose power at level 12, restart at level 12. **Precisely:** engines journal each step's result (Temporal's event history, DBOS's Postgres tables, Restate's log) and replay on restart so completed steps, including payments, do not run twice [37][43][41].

| Engine | License | Agent positioning |
|---|---|---|
| Temporal (workflows and activities; fork of Uber's Cadence) [37] | MIT, 22.9k stars | OpenAI Agents SDK integration, public preview since 30 Jul 2025; activities exposed as tools [38] |
| Inngest (event-triggered durable functions) [39] | Server SSPL with delayed Apache-2.0; SDKs Apache-2.0 | AgentKit multi-agent framework with MCP tooling (vendor) [40] |
| Restate (journaling plus "Virtual Objects") [41] | Business Source License 1.1 | "Durable AI loops"; Vercel AI SDK, OpenAI Agents SDK, Pydantic AI (vendor) [42] |
| DBOS (Postgres-backed checkpoints) [43] | MIT, 1.6k stars | Pydantic AI, LlamaIndex, OpenAI Agents SDK, Google ADK, Vercel AI SDK (vendor, secondary) [43] |
| Vercel Workflow (durable TypeScript, suspends without compute) [44] | Apache-2.0, 2.4k stars | "AI Agents in TypeScript" [44] |

Inngest claims the model "crossed into the early majority in 2025" as AWS, Cloudflare and Vercel shipped their own (vendor, secondary) [40]. The bigger shift is that harness vendors built it in: Claude Code on the web sessions "persist even if you close your browser," though after a VM is reclaimed "background work that was still running... isn't restored" [20]; Managed Agents sessions "resume cleanly after pauses" [22]; routines run in the cloud "so they keep working when your laptop is closed" [102].

**Maturity: high for engines, medium for agent integration.** The open question is what to journal (transcript, sandbox filesystem, pending approvals) and what "resume" means when the environment is gone.

**For non-technical users.** Durability is the difference between "the agent" and "a chat that dies when I close the tab." Routines model the Friday-evening delegation but are candid about the gap: "A green status... does not mean the task in your prompt succeeded" [102]. Durability solves crashes, not verification.

## 5. Memory

**Analogy.** Sticky notes an assistant leaves for the next shift. The hard questions: what to write, when to throw a note away, and whether a stranger can slip a forged note into the pile.

- *Files the user can read.* Claude Code loads `CLAUDE.md` files (managed, user, project, local) and an auto-memory `MEMORY.md` index ("first 200 lines or 25KB") with topic files read on demand; both are "context, not enforced configuration" [51]. Letta reports 74.0% on LoCoMo "by simply storing conversational histories in a file," beating specialized libraries in its own gpt-4o-mini test (vendor) [48].
- *A tool the model drives.* Anthropic's `memory_20250818` tool is client-side (`view`, `create`, `str_replace`, `insert`, `delete`, `rename` under a `/memories` prefix); the API injects "ASSUME INTERRUPTION: Your context window might be reset at any moment," and the docs warn that `/memories/../../secrets.env` must be rejected [52].
- *Server-side stores.* Managed Agents mounts stores at `/mnt/memory/<slug>/`, caps them at 10,000 memories of 100 kB, keeps immutable versions for audit and rollback, and warns that "a successful prompt injection could write malicious content into the store. Later sessions then read that content as trusted memory" [53]. "Dreams" read a store plus up to 100 transcripts and emit a deduplicated store, taking "minutes to a few hours" (research preview, `dreaming-2026-04-21`) [54]. Letta's "sleep-time compute" is the same idea (vendor) [104].
- *Specialized vendors.* Mem0 (64.9k stars, Apache-2.0) reports 92.5 on LoCoMo and 94.4 on LongMemEval for its April 2026 algorithm, up 21 and 27 points on its own prior version (vendor) [45][46]. Zep's Graphiti (30.7k stars) invalidates rather than deletes old facts; its Jan 2025 paper reports 94.8% versus MemGPT's 93.4% on DMR and "up to 18.5%" gains with 90% lower latency on LongMemEval (vendor paper) [49][50]. Letta's main repository is now "a landing page"; development moved to `letta-code`, an agent harness [47]. A memory vendor became a harness vendor.

**Maturity: low.** Every number is a vendor number on a recall benchmark, and the benchmarks disagree on whether a database beats a text file. No independent study links a memory system to better real-task completion, and the platform is absorbing the primitive.

**For non-technical users.** Memory must be inspectable in plain language ("plain markdown you can read, edit, or delete" [51]; redactable versions [53]); it is an injection sink, so shared stores need `read_only` defaults; and consolidation should be a background job the person never sees.

## 6. Context engineering

**Analogy.** The context window is a suitcase with a weight limit; context engineering is deciding what to pack, what to leave at the hotel, and what to summarize on a postcard.

- *Compaction.* Claude Code auto-compacts near the limit; the project `CLAUDE.md` is re-read afterward, but instructions given only in conversation are lost [51]. Server-side compaction (`compact-2026-01-12`, beta) summarizes at a default 150,000 input tokens and drops everything before the `compaction` block [56]. OpenAI added server-side compaction to the Responses API on 11 Feb 2026 (secondary) [59].
- *Context editing.* `clear_tool_uses_20250919` clears the oldest tool results past 100,000 tokens [55]. At the Sonnet 4.5 launch (Sep 2025) Anthropic reported memory plus context editing improved agentic search by 39%, editing alone by 29%, with 84% fewer tokens over 100 turns (vendor, secondary) [57].
- *Subagent isolation.* "Each subagent runs in its own context window," and "only the summary returns" [60]; subagents spend tens of thousands of tokens and return 1,000 to 2,000 (Anthropic, secondary) [58].
- *Just-in-time retrieval.* `CLAUDE.md` loads up front; grep and glob pull files when needed [58]; MCP tool definitions "are deferred by default, so only tool names and server instructions enter context until Claude uses a specific tool" [26].

**A concrete hazard.** Auto mode treats a boundary stated in conversation ("don't push until I review") as a block signal, but "a boundary can be lost if context compaction removes the message that stated it. For a hard guarantee, add a deny rule instead" [62]. Compaction can silently delete a safety instruction: a harness bug class, not a model bug class.

**Maturity: medium, moving into the API.** Two years ago compaction was harness code; now it is a beta header at Anthropic and OpenAI, with equivalents at xAI and Bedrock (secondary) [59]. This is the clearest case of a "thick harness" feature thinning into the model layer.

**For non-technical users.** They will never run `/compact`. The harness must compact automatically, keep the person's rules somewhere that survives compaction (settings, not chat), and show a plain summary of what was dropped. "Never email my boss" must be a rule, not a message.

## 7. Permission models

**Analogy.** Manual mode is a bank teller who phones the manager for every withdrawal. An allowlist is a standing instruction ("cash checks under $500"). Auto mode is a second teller who reviews each transaction and calls the manager only when something looks wrong.

| Harness | Modes | Enforcement |
|---|---|---|
| Claude Code | `default` (Manual), `acceptEdits`, `plan`, `auto`, `dontAsk`, `bypassPermissions` [62] | allow/ask/deny rules first; deny binds in every mode; protected paths and critical-path `rm` never auto-approved; classifier; sandbox [61][62] |
| Codex CLI | approvals `untrusted`, `on-request`, `never`; sandbox read-only, workspace-write, danger-full-access; network off by default in workspace-write (secondary) [66][106] | Seatbelt on macOS; Landlock plus seccomp on Linux, bubblewrap fallback (secondary) [106] |
| OpenClaw | No per-action approvals; "Exec behavior is host-first by default: agents.defaults.sandbox.mode defaults to off" [75] | Gateway bound to loopback; "Do not expose it to the public internet" [75] |
| Managed Agents | Sessions idle "waiting for your next message or a tool confirmation" [25] | Hosted or self-hosted sandbox; fetch allowlists [22] |

**Auto mode, the first widely deployed machine-in-the-loop.** A second model (Sonnet 5 by default) reviews each shell and network action; reads and working-directory edits skip it [62]. It sees "user messages, tool calls other than read-only lookups... and your CLAUDE.md content. Tool results are stripped, so hostile content in a file or web page can't manipulate it directly," while "a separate server-side probe scans incoming tool results" [62]. Blocked by default: `curl | bash`, force pushes, production deploys, "merging a pull request no human has approved," printing a live credential, requesting instance-metadata credentials [62]. Subagents are checked at spawn, per action and on return; after three consecutive or 20 total blocks "auto mode pauses and Claude Code resumes prompting" [62]. Trusted infrastructure is described in prose ("Trusted cloud buckets: s3://acme-build-artifacts") [63]. Anthropic's own timeline: research preview in the week of 23 to 27 Mar 2026 (v2.1.83 to v2.1.85), Pro plan 18 to 22 May 2026, available without an opt-in flag on Bedrock, Vertex AI and Foundry from Jul 2026, and the default for new sessions on Pro, Max and Team plans from 14 Aug 2026 [62][113].

**Evidence on fatigue.** Anthropic's engineering post reports 93% of prompts approved (coverage of the Aug 2026 default announcement says 97%; unverified which is current), humans catching 13.6% of planted dangerous commands versus 89% for the classifier (143 versus 937 of 1,053), and 800 human-approved commands blocked (the 800 figure is unverified) (vendor, via secondary; anthropic.com is blocked here) [65][109]. Claude Code's security page lists "prompt fatigue mitigation" as a built-in protection [64]; sandboxing alone is claimed to cut prompts 84% (vendor, secondary) [107].

**Maturity: medium.** Layered and real, with documented gaps: the classifier "is a per-action control, not an isolation boundary" [18]; narrow allow rules can let "a destructive argument through without the classifier seeing it" unless `classifyAllShell` is on [63]; an independent researcher published "Breaking Claude Code Opus 5 Auto Mode with Indirect Prompt Injection" in 2026 (page blocked; unverified) [111].

**For non-technical users.** The developer model does not translate. `Bash(git push *)` means nothing to an operations manager, and "Claude wants to run `rm -rf ./build`" is a coin flip for them, which the 93% approval rate already shows for developers. The human prompt has to nearly disappear, replaced by a boundary derived from the task's scope (the "blast radius" framing in [108]), a classifier, and a few high-stakes confirmations phrased as outcomes ("send this to 40 customers?") with undo. Managed Agents' tool confirmation state and Atlas's watch mode are early versions.

## 8. Security: prompt injection and the incident record

**Analogy.** A con artist slips a forged instruction into the stack of papers you asked your assistant to summarize; the assistant cannot reliably tell your handwriting from the forger's.

**Threat model.** Simon Willison's "lethal trifecta" (Jun 2025): private data, untrusted content and external communication together enable exfiltration; remove one leg and the attack fails (via secondary; simonwillison.net blocked) [67]. Anthropic's deployment guide links to it and removes the third leg with `--network none` plus a proxy [19].

| When | Target | What happened |
|---|---|---|
| Apr 2025 | MCP clients | Invariant Labs: instructions hidden in tool descriptions exfiltrate SSH keys; "tool shadowing" and a "sleeper rug pull" that swaps the interface on second load [80] |
| 26 May 2025 | GitHub MCP server | A malicious issue redirected an agent to private repositories; "does not require the MCP tools themselves to be compromised" (secondary) [79] |
| 20 Aug 2025 | Perplexity Comet | Brave: hidden text in a Reddit comment made Comet fetch the user's credentials and one-time codes; Perplexity's July fix was incomplete (secondary) [68] |
| Oct 2025 | Comet | LayerX "CometJacking": a crafted URL made the assistant base64-encode email and calendar data to an attacker (secondary) [69] |
| 21 to 24 Oct 2025 | ChatGPT Atlas | NeuralTrust: malformed omnibox URLs parsed as trusted intent with "fewer safety checks" (secondary) [71] |
| Dec 2025 | Atlas | OpenAI published a hardening post; its CISO called the class "unlikely to ever be fully solved" (secondary) [72][73] |
| 23 Jan to 2 Feb 2026 | OpenClaw (ex Clawdbot, Moltbot) | Censys found 21,639 gateways on the public internet on 31 Jan (secondary); exposed instances leaked Anthropic keys, Telegram and Slack tokens and chat histories; GitHub advisory CVE-2026-25253 (GHSA-g8p2-7wf7-98mq), "1-Click RCE via Authentication Token Exfiltration From gatewayUrl," published 31 Jan 2026 (Advisory Database entry 2 Feb), severity High, CVSS 3.1 score 8.8, affecting clawdbot npm 2026.1.28 and earlier, patched in 2026.1.29 [74][76][77] |
| Feb 2026 | OpenClaw skills registry | "ClawHavoc": 341 of 2,857 ClawHub skills malicious (about 12%), 335 of them from one operation, per Koi Security (secondary; koi.ai blocked) [78] |
| Mar 2026 | Comet | Zenity "PleaseFix": injections in calendar invites reached local files and credentials (secondary, unverified) [70] |

OpenClaw is the cautionary case. It is the most-starred personal agent (389k stars, MIT) [75], and its security policy declares "Prompt-injection-only attacks (without a policy/auth/sandbox boundary bypass)" out of scope and says it "does not model one gateway as a multi-tenant, adversarial user boundary" [75]. A developer-shaped harness shipped to non-developers produced the category's worst exposure record.

**Defenses that exist.** Sandboxes and default-deny egress; credential injection at a proxy so "the agent never sees the actual credentials" [19]; hiding tool results from the classifier and probing them for injection [62][27]; summarizing search results instead of passing raw pages [19]; per-site permissions and pauses at logins [30]; labeling externally supplied text as untrusted, as routines do with `<routine-fire-payload>` [102]. OWASP codified tool poisoning as MCP03:2025 (secondary) [81]; the Cloud Security Alliance wrote in Jul 2026 that "there is currently no native MCP mechanism to detect or prevent these injections" (secondary) [82]; one tally counts 30-plus MCP-server CVEs in a 60-day window in early 2026 (secondary, unverified) [83].

**Maturity: low for the problem, medium for mitigations.** No vendor claims a fix. What holds up is architectural: assume the model will be fooled and make the fooled action harmless.

**For non-technical users.** They hold all three legs of the trifecta by default (their email, the open web, and the ability to send) and cannot be asked to reason about it. Scope must be narrowed per task automatically, egress allowlisted by the harness, and outbound actions confirmed in plain language and reversibly. "Logged out mode" and "watch mode" are crude first versions; OpenClaw shows what their absence costs.

## 9. Observability and evals

**Analogy.** Traces are the flight recorder; benchmarks are the driving test. Passing the test says little about the road.

**Observability.** LangSmith and Braintrust offer nested agent spans, LLM-as-judge scoring of production traces and cost roll-ups, with wrappers for the major agent SDKs (vendor, secondary) [100]. Harnesses built the same in: Claude Code exports OpenTelemetry and attributes usage to skills, subagents and MCP servers [26]; Managed Agents persists event history [22]. Mature for developers; nothing here is a consumer surface.

| Benchmark | Measures | Status (Sep 2026) |
|---|---|---|
| Terminal-Bench 2.0 | 89 validated terminal tasks (4 easy, 55 medium, 30 hard) via Harbor; Nov 2025 [86][84] | Official leaderboard blocked here; the GitHub leaderboard repositories hold run logs, not scores [84]. Aggregators diverge by harness: 65.9% (GPT-5.6 Sol) with the fixed Terminus-2 agent on Terminal-Bench 2.1 versus 82.7% (GPT-5.5) on the best-agent leaderboard, Sep 2026 (secondary); the 91.9% figure could not be traced (unverified) [112] |
| SWE-bench Verified | 500 issues validated with OpenAI, Aug 2024 [87] | Self-reported 80 to 97% on vendor scaffolds; contamination audits find solution leakage; OpenAI stopped reporting it in Feb 2026 after auditing 138 hard tasks (59.4% flawed; frontier models reproduced gold patches verbatim) and now recommends SWE-bench Pro (openai.com blocked; confirmed via several secondary reports) [91] |
| SWE-bench Pro (Scale) | Harder, partly private; SWE-agent scaffold [88] | Standardized public leaders 59.1 to 61.5% [89] versus vendor-reported 80 to 81%; "most pages quoting a score never say which one they mean" (secondary) [90] |
| OSWorld-Verified | Real desktop tasks; human 72.36% | Vendor and aggregator scores of 81 to 86% exceed the human baseline (secondary) [94][95] |
| tau2 / tau3-bench | Customer-service agents against a simulated user and policy; pass^k reliability | Telecom leaders about 99% (likely avg@k, secondary) [97]; tau3 (v1.0.0, 18 Mar 2026) added full-duplex voice and a banking knowledge-retrieval domain; a Jul 2026 v1.0.1 grading update re-scored that domain [96] |
| HAL | Nine benchmarks with cost as a first-class axis [98] | 21,730 rollouts, 9 models, about $40,000; higher reasoning effort reduced accuracy in 21 of 36 runs; "the most costly models are rarely on the Pareto frontier" (paper via secondary; ICLR 2026) [99]. Archived 1 Jul 2026 to focus "on agent reliability" [98] |

HAL's central finding is that "the choice of agent scaffold drastically affects both accuracy and cost" (secondary) [99], which is why vendor and standardized SWE-bench Pro scores diverge by 20 points. The community's response was to make the harness part of the benchmark (Harbor fixes the agent, Scale fixes the scaffold), an admission that model scores without a harness are not comparable.

**Maturity: medium for developer tasks, near zero for everyone else.** The closest non-technical benchmark is tau-bench, which tests the agent as a service rep, not as a helper for a person doing their own job.

**For non-technical users.** The measurement gap is itself a finding: a harness for this audience cannot prove its value on any public leaderboard, so it needs one-click task-level success signals and traces a support team can read.

## 10. Cost and latency realities

**Prices (Sep 2026).** Claude Fable 5.1: $10 input, $50 output per million tokens, cache hits $0.25; Opus 5: $5 / $25; Sonnet 5: $2 / $10; Haiku 4.5: $1 / $5 [25]. Models from Claude 4.7 on use a tokenizer producing "approximately 30% more tokens for the same text" [25]. Managed Agents adds $0.08 per running session-hour; idle time is free; code execution is $0.05 per container-hour past 1,550 free hours [25].

**What a harness spends.** Anthropic's enterprise figure: "around $13 per developer per active day and $150-250 per developer per month, with costs remaining below $30 per active day for 90% of users" [26]. Long sessions cost more because "Claude Code sends your full conversation with every request" [26]; one analysis attributes about 85% of session cost to input re-reads (secondary) [101]. Agent teams "use approximately 7x more tokens" [26]. Safety layers add tokens or round trips: 4,500 and 6,600 tokens of tool definitions for the computer and browser toolsets [25]; the classifier adds "a round-trip before execution," billed on Enterprise and API accounts [62].

**Latency.** Infrastructure is fast (Firecracker under 125 ms [10], Daytona under 90 ms claimed [5], V8 isolates under 5 ms [8]); the model loop is slow. Screen control is "the broadest and slowest" path [29]; gVisor costs 2x on syscalls and up to 10 to 200x on heavy file I/O [19]; dreams take "minutes to a few hours" [54]; cloud sessions add provisioning and setup scripts before the first token [20].

**Maturity: prices transparent, bills unpredictable.** The variance lives in the harness: session length, re-reads, fan-out, safety layers.

**For non-technical users.** Per-token pricing is meaningless to them. The harness must sell a bounded-price outcome or session, absorb the variance, and answer "how much did that cost" in one line, on top of API rates that are 5x higher for the best model than the mid-tier.

## 11. Maturity scorecard

| Layer | Maturity | Who is winning | Implication for a non-technical harness |
|---|---|---|---|
| Sandboxes | High, commodity | Clouds and labs; open-source leader went private | Inherit it; derive the boundary from the task |
| Computer and browser use | Medium capability, low safety | Labs plus open source | Structured access first; screen control as residue with confirmations |
| Durable execution | High engines, medium integration | Temporal et al.; labs building it into sessions and routines | Sessions must outlive the tab; verification is separate |
| Memory | Low | Nobody; labs absorbing the primitive | Plain-language, inspectable, read-only by default when shared |
| Context engineering | Medium, moving into the API | Labs | Rules must live outside the transcript |
| Permissions | Medium | Labs (classifier plus boundary) | Remove the prompt; confirm outcomes, not commands |
| Security | Low for the problem | Architecture patterns, not products | Break the trifecta per task; allowlist egress automatically |
| Observability and evals | High for devs, near zero for others | LangSmith, Braintrust, Harbor; HAL archived | Build task-level success signals |
| Cost and latency | Transparent prices, unpredictable bills | Labs (plan pricing) | Bounded-price outcomes; hide tokens |

## What this means for the thesis

**Supports.**

- Everything that made agents usable unattended in the last year is harness infrastructure, not model weights: OS sandboxes (84% fewer prompts, vendor), credential-holding proxies, the auto-mode classifier (89% versus 13.6%, vendor), durable cloud sessions, routines. The model did not get safer to run alone; the box around it did.
- The default harness is already not a CLI. Anthropic's cloud environments "apply wherever you start a cloud session: the web, the terminal, Claude Tag, routines, and the mobile and Desktop apps" [21]. The CLI is one client of a hosted runtime, the "one runtime, many surfaces" pattern the thesis predicts.
- The developer permission and security model does not transfer. Allowlists, prose infrastructure descriptions and `rm -rf` prompts are unusable by non-developers, and the one developer-shaped harness that reached consumers at scale (OpenClaw) produced the category's worst incident record. A non-technical default must be designed, not ported.
- The remaining bottlenecks (injection, memory, verification, cost predictability) are harness problems with no model-side fix in sight, on the vendors' own admission.

**Contradicts.**

- The labs are eating the runtime. Managed Agents bundles loop, sandbox, memory stores, versioning, dreams, tracing, scheduling and self-hosted execution behind one API at $0.08 per session-hour [22][25]. Compaction, context editing, injection probes and the permission classifier moved into the model provider's stack. An independent harness competing on runtime competes with its supplier's default.
- Several "thick harness" pieces are thinning as the Bitter Lesson camp predicts: compaction became an API header; Letta's plain filesystem beat specialized memory libraries in its own test; OSWorld agents passed the human baseline with the same screenshot-and-click loop that scored 14.9% at launch two years earlier (launch figure unverified). Capability moved into the model while the harness code stayed put.
- Isolation, the most tangible runtime asset, is a utility with a dozen interchangeable suppliers.

**Nuance.** The evidence supports "the harness is the bottleneck" and contradicts "an independent company can own the runtime." What remains for a newcomer is the layer above: translating scope, permissions, memory, cost and verification into terms a non-developer can act on, over runtime primitives that are cheap and interchangeable. The runtime is table stakes; the interface to its safety properties is the open problem, and no eval can yet measure whether anyone has solved it.

## Open questions and unverified claims

- Auto mode launch date: resolved. Anthropic's Claude Code "What's new" page dates the research preview to the week of 23 to 27 Mar 2026 and the default switch for new sessions on Pro, Max and Team plans to 14 Aug 2026; the 10 Jul 2026 date in [110] refers to auto mode losing its opt-in flag on third-party providers, not to launch [113]. Anthropic's blog and engineering posts remain unreachable.
- The 93% approval rate (some Aug 2026 coverage says 97%), the 13.6% versus 89% catch rates (143 versus 937 of 1,053 planted commands) and the 84% prompt reduction are Anthropic figures quoted by third parties; the catch rates and the 84% were consistent across several such reports, while "800 commands a human had approved" appeared in none of them (unverified) [65][107][109].
- The embracethered post on breaking auto mode could not be opened; its findings are unknown [111].
- OpenClaw figures vary by source and method: 21,639 exposed instances (Censys, 31 Jan 2026), "more than 30,000" (Bitsight) and 42,665 with 5,194 verified vulnerable (Maor Dayan) are consistently reported; later scans claim 63,070 (Mar 2026) to 220,000+, and the 135,000 figure could not be traced (unverified). The 1.5 million leaked tokens are confirmed as a Moltbook (agent social network) Supabase misconfiguration found by Wiz on 31 Jan 2026, alongside 35,000 email addresses and private messages; the 341 malicious skills are Koi Security's count out of 2,857 audited; the "later 800+" figure is unverified [76][77][78].
- Apple `container` 1.0.0: resolved; the release page dates it 9 Jun 2026, and its notes say "container is one year old" and introduce `container machine` [14][15].
- Daytona's move to a private codebase is confirmed by its README; the hosted product's status and the Series A details are secondary [5][6].
- OpenAI stopping SWE-bench Verified reporting is now confirmed by several independent reports of its Feb 2026 post (138 hard tasks audited, 59.4% flawed; SWE-bench Pro recommended) [91].
- Terminal-Bench 2.0's leaderboard remained blocked and its 2026 scores still come from aggregators that differ by harness [112]; OSWorld-Verified's official results file was read from the leaderboard's GitHub repository and is cited in section 3 [114]; the vendor figures in [94] are not on that file and the 86.1% figure in [95] is contradicted by it.
- SWE-bench Pro task counts (reported elsewhere as 1,865 total, 731 public) were not visible in the repository README and are omitted.
- Mem0's 2025 paper claims were not re-verified; only the April 2026 README numbers are cited (README numbers confirmed 9 Sep 2026) [45].
- ChatGPT agent absorbing Operator (Jul 2025) and Anthropic's 14.9% OSWorld launch score (Oct 2024) are from memory and marked unverified.
- Modal's gVisor basis and pricing, E2B's Firecracker basis and pricing, and Cloudflare Dynamic Workers pricing come from vendor comparison blogs, not primary pages [3][4][8][9].

## Sources

1. E2B repository, https://github.com/e2b-dev/E2B (accessed Sep 2026)
2. E2B infrastructure repository, https://github.com/e2b-dev/infra (accessed Sep 2026)
3. Morph, "E2B Pricing Breakdown," https://www.morphllm.com/e2b-pricing (2026, secondary)
4. Northflank, "What's the best code execution sandbox for AI agents in 2026?", https://northflank.com/blog/best-code-execution-sandbox-for-ai-agents (2026, secondary)
5. Daytona repository README, https://github.com/daytonaio/daytona (notice dated Jun 2026)
6. Unicorner, "Daytona: Instant sandboxes for AI agents," https://read.unicorner.news/p/daytona (2026, secondary)
7. Cloudflare Sandbox SDK, https://github.com/cloudflare/sandbox-sdk (accessed Sep 2026)
8. InfoWorld, "Cloudflare launches Dynamic Workers for AI agent execution," https://www.infoworld.com/article/4149869/cloudflare-launches-dynamic-workers-for-ai-agent-execution.html (Mar 2026, secondary)
9. VentureBeat, "Cloudflare's new Dynamic Workers ditch containers," https://venturebeat.com/infrastructure/cloudflares-new-dynamic-workers-ditch-containers-to-run-ai-agent-code-100x (Mar 2026, secondary)
10. Firecracker SPECIFICATION.md, https://github.com/firecracker-microvm/firecracker/blob/main/SPECIFICATION.md
11. Firecracker README, https://github.com/firecracker-microvm/firecracker
12. gVisor README, https://github.com/google/gvisor
13. gVisor performance guide, https://github.com/google/gvisor/blob/master/g3doc/architecture_guide/performance.md
14. Apple `container` repository and releases, https://github.com/apple/container and https://github.com/apple/container/releases (1.0.0, Jun 2026)
15. InfoQ, "Apple container Linux," https://www.infoq.com/news/2025/06/apple-container-linux (Jun 2025, secondary); explainx, "Apple Container 1.0," https://www.explainx.ai/blog/apple-container-1-linux-containers-macos-26-swift-2026 (2026, secondary)
16. Anthropic sandbox-runtime, https://github.com/anthropic-experimental/sandbox-runtime (accessed Sep 2026)
17. Claude Code docs, "Configure the sandboxed Bash tool," https://code.claude.com/docs/en/sandboxing (Sep 2026)
18. Claude Code docs, "Choose a sandbox environment," https://code.claude.com/docs/en/sandbox-environments (Sep 2026)
19. Claude Code docs, "Securely deploying AI agents," https://code.claude.com/docs/en/agent-sdk/secure-deployment (Sep 2026)
20. Claude Code docs, "Use Claude Code on the web," https://code.claude.com/docs/en/claude-code-on-the-web (Sep 2026)
21. Claude Code docs, "Configure cloud environments," https://code.claude.com/docs/en/cloud-environments (Sep 2026)
22. Claude Platform docs, "Claude Managed Agents overview," https://platform.claude.com/docs/en/managed-agents/overview (beta header 2026-04-01)
23. Claude Platform docs, "Self-hosted sandboxes," https://platform.claude.com/docs/en/managed-agents/self-hosted-sandboxes (Sep 2026)
24. The Decoder, "Anthropic adds self-hosted sandboxes and MCP tunnels to Claude Managed Agents," https://the-decoder.com/anthropic-adds-self-hosted-sandboxes-and-mcp-tunnels-to-claude-managed-agents/ (May 2026, secondary)
25. Claude Platform docs, "Pricing," https://platform.claude.com/docs/en/about-claude/pricing (Sep 2026)
26. Claude Code docs, "Manage costs effectively," https://code.claude.com/docs/en/costs (Sep 2026)
27. Claude Platform docs, "Computer use tool," https://platform.claude.com/docs/en/agents-and-tools/tool-use/computer-use-tool (toolset 2026-08-01)
28. Claude Platform docs, "Browser use tool," https://platform.claude.com/docs/en/agents-and-tools/tool-use/browser-use-tool (toolset 2026-08-01)
29. Claude Code docs, "Let Claude use your computer from the CLI," https://code.claude.com/docs/en/computer-use (Sep 2026)
30. Claude Code docs, "Use Claude Code with Chrome," https://code.claude.com/docs/en/chrome (Sep 2026)
31. VentureBeat, "Anthropic launches Claude for Chrome in limited beta," https://venturebeat.com/infrastructure/anthropic-launches-claude-for-chrome-in-limited-beta-but-prompt-injection-attacks-remain-a-major-concern (Aug 2025, secondary)
32. OpenAI, "Computer-Using Agent," https://openai.com/index/computer-using-agent/ (Jan 2025, blocked); figures via https://www.arturmarkus.com/openai-launches-operator-on-january-23-chatgpt-pros-200-month-browser-agent-scores-38-1-on-osworld-58-1-on-webarena/ (secondary)
33. Browser Use repository, https://github.com/browser-use/browser-use (accessed Sep 2026)
34. Browser Use, "We Raised $17M," https://browser-use.com/posts/seed-round (Mar 2025)
35. Playwright MCP, https://github.com/microsoft/playwright-mcp (accessed Sep 2026)
36. Stagehand, https://github.com/browserbase/stagehand (accessed Sep 2026)
37. Temporal repository, https://github.com/temporalio/temporal (accessed Sep 2026)
38. Temporal, "Production-ready agents with the OpenAI Agents SDK + Temporal," https://temporal.io/blog/announcing-openai-agents-sdk-integration (Jul 2025)
39. Inngest repository, https://github.com/inngest/inngest (accessed Sep 2026)
40. Inngest, "Durable Execution: The Key to Harnessing AI Agents in Production," https://www.inngest.com/blog/durable-execution-key-to-harnessing-ai-agents (2025, vendor)
41. Restate repository and LICENSE, https://github.com/restatedev/restate (accessed Sep 2026)
42. Restate, "Durable AI Loops," https://www.restate.dev/blog/durable-ai-loops-fault-tolerance-across-frameworks-and-without-handcuffs (2025, vendor)
43. DBOS Transact (Python), https://github.com/dbos-inc/dbos-transact-py (accessed Sep 2026); integrations per https://www.dbos.dev/solutions/agentic-ai-platform (vendor)
44. Vercel Workflow, https://github.com/vercel/workflow (accessed Sep 2026)
45. Mem0 repository README, https://github.com/mem0ai/mem0 (algorithm update Apr 2026)
46. Mem0, "State of AI Agent Memory 2026," https://mem0.ai/blog/state-of-ai-agent-memory-2026 (2026, vendor)
47. Letta repository, https://github.com/letta-ai/letta (accessed Sep 2026)
48. Letta, "Benchmarking AI Agent Memory: Is a Filesystem All You Need?", https://www.letta.com/blog/benchmarking-ai-agent-memory/ (2025, vendor)
49. Graphiti repository, https://github.com/getzep/graphiti (accessed Sep 2026)
50. Rasmussen et al., "Zep: A Temporal Knowledge Graph Architecture for Agent Memory," https://arxiv.org/abs/2501.13956 (Jan 2025)
51. Claude Code docs, "How Claude remembers your project," https://code.claude.com/docs/en/memory (Sep 2026)
52. Claude Platform docs, "Memory tool," https://platform.claude.com/docs/en/agents-and-tools/tool-use/memory-tool (tool version 2025-08-18)
53. Claude Platform docs, "Using agent memory," https://platform.claude.com/docs/en/managed-agents/memory (beta header 2026-07-22)
54. Claude Platform docs, "Dreams," https://platform.claude.com/docs/en/managed-agents/dreams (beta header 2026-04-21)
55. Claude Platform docs, "Context editing," https://platform.claude.com/docs/en/build-with-claude/context-editing (Sep 2026)
56. Claude Platform docs, "Compaction," https://platform.claude.com/docs/en/build-with-claude/compaction (beta header 2026-01-12)
57. AWS, "Introducing Claude Sonnet 4.5 in Amazon Bedrock," https://aws.amazon.com/blogs/aws/introducing-claude-sonnet-4-5-in-amazon-bedrock-anthropics-most-intelligent-model-best-for-coding-and-complex-agents/ (Sep 2025, secondary for Anthropic's context-management figures)
58. Anthropic, "Effective context engineering for AI agents," https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents (Sep 2025, blocked); summary via https://agentic-ai.readthedocs.io/en/latest/ContextEngineering/anthropic/ (secondary)
59. OpenAI, "Compaction," https://developers.openai.com/api/docs/guides/compaction (Feb 2026, blocked); date via https://zylos.ai/research/2026-04-21-agent-context-compaction-long-running-sessions/ (secondary)
60. Claude Code docs, "Subagents," https://code.claude.com/docs/en/sub-agents (Sep 2026)
61. Claude Code docs, "Configure permissions," https://code.claude.com/docs/en/permissions (Sep 2026)
62. Claude Code docs, "Choose a permission mode," https://code.claude.com/docs/en/permission-modes (Sep 2026)
63. Claude Code docs, "Configure auto mode," https://code.claude.com/docs/en/auto-mode-config (Sep 2026)
64. Claude Code docs, "Security," https://code.claude.com/docs/en/security (Sep 2026)
65. Anthropic, "How we built Claude Code auto mode," https://www.anthropic.com/engineering/claude-code-auto-mode (2026, blocked); figures via https://dev.to/rulestack/auto-mode-is-now-claude-codes-default-what-the-classifier-approves-and-how-to-switch-back-4j2j (secondary)
66. OpenAI, "Agent approvals & security," https://developers.openai.com/codex/agent-approvals-security (blocked)
67. Simon Willison, "The lethal trifecta for AI agents," https://simonwillison.net/2025/Jun/16/the-lethal-trifecta/ (Jun 2025, blocked); via https://www.promptfoo.dev/blog/lethal-trifecta-testing/ (secondary)
68. Brave, "Agentic Browser Security: Indirect Prompt Injection in Perplexity Comet," https://brave.com/blog/comet-prompt-injection/ (Aug 2025, blocked); via https://www.malwarebytes.com/blog/news/2025/08/ai-browsers-could-leave-users-penniless-a-prompt-injection-warning and https://ppc.land/comet-browser-faces-multiple-security-vulnerabilities-from-prompt-injection/ (secondary)
69. The Hacker News, "CometJacking," https://thehackernews.com/2025/10/cometjacking-one-click-can-turn.html (Oct 2025, secondary)
70. Aviatrix, "Perplexity Comet 2026 Zenity Labs Prompt Injection," https://aviatrix.ai/threat-research-center/perplexity-comet-2026-zenity-labs-prompt-injection/ (Mar 2026, secondary)
71. NeuralTrust, "OpenAI Atlas Omnibox Prompt Injection," https://neuraltrust.ai/blog/openai-atlas-omnibox-prompt-injection (Oct 2025)
72. Fortune, "OpenAI says prompt injections that can trick AI browsers may never be fully 'solved'," https://fortune.com/2025/12/23/openai-ai-browser-prompt-injections-cybersecurity-hackers/ (Dec 2025, secondary)
73. OpenAI, "Continuously hardening ChatGPT Atlas against prompt injection attacks," https://openai.com/index/hardening-atlas-against-prompt-injection/ (Dec 2025, blocked)
74. GitHub Advisory Database, CVE-2026-25253, https://github.com/advisories?query=CVE-2026-25253 (published 2 Feb 2026)
75. OpenClaw repository and SECURITY.md, https://github.com/openclaw/openclaw (accessed Sep 2026)
76. Adversa, "OpenClaw security guide 2026," https://adversa.ai/blog/openclaw-security-101-vulnerabilities-hardening-2026/ (2026, secondary)
77. SecurityScorecard, "Moltbot's Real Risk Is Exposed Infrastructure," https://securityscorecard.com/blog/beyond-the-hype-moltbots-real-risk-is-exposed-infrastructure-not-ai-superintelligence/ (Jan 2026, secondary)
78. Conscia, "The OpenClaw security crisis," https://conscia.com/blog/the-openclaw-security-crisis/ (2026, secondary)
79. Invariant Labs, "GitHub MCP Exploited," https://invariantlabs.ai/blog/mcp-github-vulnerability (May 2025); via https://www.devclass.com/ai-ml/2025/05/27/researchers-warn-of-prompt-injection-vulnerability-in-github-mcp-with-no-obvious-fix/1623458 (secondary)
80. Invariant Labs, mcp-injection-experiments, https://github.com/invariantlabs-ai/mcp-injection-experiments (Apr 2025)
81. OWASP, "MCP03:2025 Tool Poisoning," https://owasp.org/www-project-mcp-top-10/2025/MCP03-2025%E2%80%93Tool-Poisoning (blocked; via secondary)
82. Cloud Security Alliance, "MCP Tool Poisoning: Adversarial Hijacking of AI Agent Workflows," https://labs.cloudsecurityalliance.org/research/csa-research-note-mcp-tool-poisoning-ai-agent-exfiltration-2/ (Jul 2026)
83. Practical DevSecOps, "MCP Security Statistics 2026," https://www.practical-devsecops.com/mcp-security-statistics-2026-report/ (2026, secondary)
84. Terminal-Bench repository, https://github.com/laude-institute/terminal-bench (accessed Sep 2026)
85. Harbor repository, https://github.com/laude-institute/harbor (accessed Sep 2026)
86. VentureBeat, "Terminal-Bench 2.0 launches alongside Harbor," https://venturebeat.com/ai/terminal-bench-2-0-launches-alongside-harbor-a-new-framework-for-testing (Nov 2025, secondary)
87. SWE-bench repository, https://github.com/SWE-bench/SWE-bench (accessed Sep 2026)
88. SWE-bench Pro repository, https://github.com/scaleapi/SWE-bench_Pro-os (accessed Sep 2026)
89. Scale, SWE-bench Pro public leaderboard, https://labs.scale.com/leaderboard/swe_bench_pro_public (blocked)
90. Morph, "SWE-bench Pro Leaderboard (2026)," https://www.morphllm.com/swe-bench-pro (Sep 2026, secondary)
91. Digital Applied, "SWE-bench in 2026: Benchmarks vs Scaffolding Reality," https://www.digitalapplied.com/blog/swe-bench-verified-june-2026-benchmark-vs-scaffolding-analysis (Jun 2026, secondary)
92. OSWorld repository, https://github.com/xlang-ai/OSWorld (OSWorld-Verified update 28 Jul 2025)
93. OSWorld leaderboard, https://os-world.github.io/ (blocked)
94. Vellum, "Claude Sonnet 5 Benchmarks Explained," https://www.vellum.ai/blog/claude-sonnet-5-benchmarks-explained (Jul 2026, secondary)
95. BenchLM, "OSWorld-Verified Leaderboard," https://benchlm.ai/benchmarks/osworld-verified (Sep 2026, secondary aggregator)
96. tau2-bench repository, https://github.com/sierra-research/tau2-bench (tau3-bench Jul 2026)
97. llm-stats, "Tau2 Telecom Leaderboard," https://llm-stats.com/benchmarks/tau2-telecom (Aug 2026, secondary aggregator)
98. HAL harness repository, https://github.com/princeton-pli/hal-harness (archived 1 Jul 2026)
99. Kapoor et al., "Holistic Agent Leaderboard: The Missing Infrastructure for AI Agent Evaluation," https://arxiv.org/abs/2510.11977 (Oct 2025, blocked); findings via https://www.themoonlight.io/en/review/holistic-agent-leaderboard-the-missing-infrastructure-for-ai-agent-evaluation (secondary)
100. Braintrust, "Agent observability: The complete guide for 2026," https://www.braintrust.dev/articles/agent-observability-complete-guide-2026 (vendor); Digital Applied, "Agent Observability 2026," https://www.digitalapplied.com/blog/agent-observability-2026-evals-traces-cost-guide (secondary)
101. Vantage, "The Hidden Cost Driver in Agentic Coding Sessions in 2026," https://www.vantage.sh/blog/agentic-coding-costs (2026, secondary)
102. Claude Code docs, "Automate work with routines," https://code.claude.com/docs/en/routines (Sep 2026)
103. Modal, "Introducing Claude Managed Agents with Modal Sandboxes," https://modal.com/blog/introducing-claude-managed-agents-with-modal-sandboxes (2026, blocked)
104. Letta, research page and sleep-time compute, https://www.letta.com/research/ (vendor)
105. Cloudflare, "Cloudflare Sandboxes," https://www.cloudflare.com/products/sandboxes/ (blocked)
106. Codex Knowledge Base, "Inside the Codex Sandbox: Platform-Specific Implementation," https://codex.danielvaughan.com/2026/04/08/codex-sandbox-platform-implementation/ (Apr 2026, secondary)
107. Anthropic, "Claude Code sandboxing," https://anthropic.com/engineering/claude-code-sandboxing (Oct 2025, blocked); 84% figure via https://www.infralovers.com/blog/2026-02-15-sandboxing-claude-code-macos/ and https://arte.itlibra.com/en/articles/claude-code-sandboxing-guide (secondary)
108. WorkOS, "Your agent's permission model stops at your home folder," https://workos.com/blog/agent-permissions-blast-radius (2026, secondary)
109. A. Giridharan, "Claude Code's Auto Mode Solves the Permission Fatigue Problem," https://medium.com/@AdithyaGiridharan/claude-codes-auto-mode-solves-the-permission-fatigue-problem-1bb7417bb858 (2026, secondary)
110. AI Catchup, "Claude Code Auto Mode Is Now the Default," https://aicatchup.com/news/claude-code-auto-mode-default-august-2026 (Aug 2026, secondary)
111. Embrace The Red, "Breaking Claude Code Opus 5 Auto Mode with Indirect Prompt Injection," https://embracethered.com/blog/posts/2026/breaking-claude-code-opus-5-and-automode/ (2026, blocked; title only)
112. llm-stats, "Terminal-Bench 2.0 Leaderboard," https://llm-stats.com/benchmarks/terminal-bench-2 and Artificial Analysis, "Terminal-Bench v2.1," https://artificialanalysis.ai/evaluations/terminalbench-v2-1 (Sep 2026, secondary aggregators, conflicting)
113. Claude Code docs, "What's new," https://code.claude.com/docs/en/whats-new (weekly digests, Mar to Aug 2026)
114. OSWorld-Verified official results file, https://github.com/os-world/os-world.github.io/blob/main/static/data/osworld_verified_results.xlsx (entries through 1 Aug 2026)

## Verification notes (2026-09-09)

**Method.** Primary files on GitHub (raw.githubusercontent.com, GitHub advisory pages, repository metadata through the GitHub API), Anthropic documentation on code.claude.com and platform.claude.com, and 12 web searches for figures whose primary pages are blocked from this session. Star counts are as of 9 Sep 2026. Verdict counts: 15 confirmed, 8 corrected, 2 unverified.

**Claims checked.**

1. Firecracker "<= 125 ms" from InstanceStart to guest init and "<= 5 MiB" VMM overhead: confirmed (SPECIFICATION.md).
2. Daytona: 71,755 stars; README notice "As of June 2026, Daytona's core development has moved to a private codebase"; "under 90ms"; "dedicated kernel": confirmed.
3. HAL: repository "archived by the owner on Jul 1, 2026"; README says the team is "focusing our current work on agent reliability": confirmed.
4. Mem0: README "New Memory Algorithm (April 2026)" table gives LoCoMo 92.5 (+21 points) and LongMemEval 94.4 (+27 points); 64,943 stars: confirmed.
5. Letta filesystem agent at 74.0% on LoCoMo with GPT-4o mini: confirmed via secondary summaries of the Letta post (letta.com blocked); Letta drops LoCoMo's adversarial category and uses its own judge, so the figure is not directly comparable with Mem0's.
6. Managed Agents pricing: "$0.08 per session-hour", accrued only while running; 1,550 free container hours per month, then $0.05 per container-hour; model prices (Fable 5.1 $10/$50 with $0.25 cache hits, Opus 5 $5/$25, Sonnet 5 $2/$10, Haiku 4.5 $1/$5); "approximately 30% more tokens" for Claude 4.7 and later; 4,500 and 6,600-token toolset overheads: confirmed (pricing page). Sonnet 5's introductory price was made permanent.
7. Eleven self-hosted sandbox guides (AWS Lambda MicroVMs, Blaxel, Cloudflare, Daytona, E2B, Fly.io, GKE Agent Sandbox, Modal, Namespace, Superserve, Vercel): confirmed; the platform release notes date self-hosted sandboxes and MCP tunnels to 27 May 2026 (Managed Agents public beta 8 Apr 2026, memory public beta 29 Apr, dreams research preview 12 May).
8. Auto mode timeline: corrected. The Claude Code "What's new" page dates the research preview to the week of 23 to 27 Mar 2026 (v2.1.83 to v2.1.85), Pro plan availability to 18 to 22 May 2026, third-party-provider availability to Jun 2026 with the opt-in flag removed in Jul 2026, and the default for new sessions on Pro, Max and Team plans to 14 Aug 2026 (announced in the 3 to 7 Aug digest). The permission-modes page states "On Pro, Max, and Team plans, the built-in starting permission mode is auto mode" and lists the surfaces that still start in Manual (Enterprise, API keys, `-p`, third-party providers). The 10 Jul 2026 "launch" date in [110] matches the provider opt-in change, not the launch. The changelog (v2.1.251) carries fixes for "accounts whose startup default is auto mode." Also confirmed on the docs: the classifier runs on Sonnet 5 by default; auto mode pauses after three consecutive or 20 total denials; the compaction-loses-boundary warning; the blocked-by-default list; `classifyAllShell`; the trust-slot prose.
9. 13.6% versus 89% catch rates: confirmed (secondary, consistent across several reports: humans blocked 143 and the classifier 937 of 1,053 planted dangerous commands). One report adds that the deployed classifier missed 17% of 52 real cases in which Claude exceeded its authorization.
10. 93% approval rate: unverified. Coverage of the same Anthropic material gives both 93% and 97%; anthropic.com and claude.com are blocked, so the current figure could not be settled. Both figures are now shown in the text.
11. "800 commands a human had approved": unverified; the figure appears in none of the reachable reports and is flagged inline.
12. 84% fewer permission prompts from sandboxing: confirmed as Anthropic's Oct 2025 claim ("in internal use"), via secondary.
13. OpenClaw exposure counts: corrected. Censys 21,639 (31 Jan 2026), Bitsight "more than 30,000" and Maor Dayan 42,665 (5,194 verified vulnerable) are consistently reported; later scans claim 63,070 (Mar 2026) and 220,000+; the 135,000 figure could not be traced.
14. 1.5 million leaked tokens: confirmed via secondary as the Moltbook Supabase misconfiguration found by Wiz on 31 Jan 2026 (1.5 million agent API tokens, 35,000 email addresses, private messages, hardcoded Supabase key with row-level security off); wiz.io blocked.
15. 341 malicious skills: confirmed via secondary as Koi Security's count out of 2,857 ClawHub skills (335 in the ClawHavoc operation); the "later 800+" figure is unverified.
16. CVE-2026-25253: corrected. GitHub advisory GHSA-g8p2-7wf7-98mq: published 31 Jan 2026 (Advisory Database listing 2 Feb 2026), severity High, CVSS 3.1 score 8.8 (AV:N/AC:L/PR:N/UI:R/S:U/C:H/I:H/A:H), clawdbot npm 2026.1.28 and earlier, patched in 2026.1.29. OpenClaw SECURITY.md quotations, the MIT license and the 389,262-star count: confirmed.
17. Terminal-Bench 2.0: corrected. 89 tasks (4 easy, 55 medium, 30 hard) confirmed via secondary; tbench.ai blocked; the laude-institute leaderboard repositories contain run logs only; the terminal-bench-2 repository now holds 103 task directories, consistent with a 2.1 revision. Aggregator scores differ by harness (65.9% for GPT-5.6 Sol with Terminus-2 on Terminal-Bench 2.1, 82.7% for GPT-5.5 on the best-agent leaderboard, both Sep 2026); the 91.9% figure could not be traced.
18. OSWorld-Verified: corrected. The 72.36% human baseline is confirmed from the site source; the official results file in the leaderboard's GitHub repository (entries through 1 Aug 2026) lists Intelligence-Indeed Agent 90.19%, claude-fable-5[1m] 85.96%, Pointer Agent w/ Opus 4.7 83.64%, claude-opus-5[1m] 83.39%, Coasty CUA v1 82.81%, Holo3-35B-A3B 82.56% and claude-sonnet-4-6 72.11%, all at 100 steps. The vendor figures for Sonnet 5 (81.2%) and Opus 4.8 (83.4%) are not on the file; the 86.1% Qwen figure is contradicted by it (Qwen 3.7 Plus 73.30%).
19. OpenAI and SWE-bench Verified: corrected from unverified to confirmed. OpenAI's Feb 2026 post "Why SWE-bench Verified no longer measures frontier coding capabilities" (openai.com blocked) is reported consistently: 138 hard tasks audited, 59.4% with flawed tests, frontier models reproducing gold patches verbatim, SWE-bench Pro recommended; the OpenAI Developers announcement is dated 23 Feb 2026.
20. Apple `container` 1.0.0: confirmed, 9 Jun 2026 (release page; notes say "container is one year old" and introduce `container machine`); the README requires macOS 26.
21. tau3-bench: corrected. v1.0.0 (18 Mar 2026) added voice full-duplex, the `banking_knowledge` retrieval domain (97 tasks, 698 policy documents) and 75+ task fixes; July 2026 is the v1.0.1 grading update that re-scored that domain.
22. Star counts: OpenClaw 389,262; Browser Use 113,650; Playwright MCP 36,908; Graphiti 30,707; Stagehand 24,177; Temporal 22,914; E2B 13,716; Vercel Workflow 2,386; DBOS Transact 1,565: confirmed within rounding.
23. Harbor's provider list ("Daytona, Modal, LangSmith, Blaxel, Novita Sandbox, Tensorlake, and Runta") and Letta's README ("landing page", development in `letta-code`): confirmed.
24. Claude documentation quotations: confirmed (six sandbox approaches; "per-action control, not an isolation boundary"; the Bash sandbox "is not sufficient for fully unattended runs"; Docker Sandboxes as a free microVM product; "$13 per developer per active day and $150-250 per developer per month"; agent teams "approximately 7x more tokens"; MCP definitions "deferred by default"; gVisor "~2x" and "10-200x"; "--network none" plus a host proxy; sessions "persist even if you close your browser" and background work "isn't restored"; no separate compute charge; dreams read "1 to 100 sessions" and take "minutes to a few hours"; 10,000 memories of 100 kB with immutable versions and the injection warning; MEMORY.md "first 200 lines or 25KB"; Chrome "pauses and asks you to handle it manually"; routines' "green status" caveat and `<routine-fire-payload>`; computer use from the CLI requires a Pro or Max plan; Managed Agents sessions "resume cleanly after pauses" and idle "waiting for your next message or a tool confirmation").
25. Browser-use tool quotation: corrected to the docs' wording ("runs in your environment"; "nothing runs on Anthropic's side"); the earlier wording was a paraphrase presented as a quote.

**Corrections made in the text.** Status line; TL;DR bullets on the sandbox partners date, auto mode's default, approval fatigue and OSWorld; the Apple `container` row; the browser-use quotation; the OSWorld benchmarks paragraph; the auto mode timeline sentence; the fatigue paragraph; the CVE and ClawHavoc table rows; the Terminal-Bench, SWE-bench Verified and tau rows; seven "Open questions" bullets; sources [113] and [114] added.

**Sources that could not be opened.** anthropic.com, claude.com, openai.com, simonwillison.net, brave.com, owasp.org, arxiv.org, tbench.ai, os-world.github.io, letta.com, temporal.io, wiz.io, koi.ai, censys.com, dev.to, medium.com, infralovers.com, arte.itlibra.com, digitalapplied.com, aicatchup.com, blog.cyberdesserts.com, and the press sites (The Register, Help Net Security, The New Stack, InfoWorld, TechCrunch, DevOps.com, gbhackers, cyberpress, developmentstoday, mindstudio). github.com HTML pages and api.github.com refused plain curl (403) but opened through the fetch tool and the GitHub API tool; the platform.claude.com Managed Agents release-notes URL returned 404, so the general platform release notes were used instead.

**Remaining doubts.** Whether Anthropic's current approval-rate figure is 93% or 97%, and where "800 commands" comes from; the 135,000 exposure figure and the "later 800+" skills figure; the 91.9% Terminal-Bench figure and the official Terminal-Bench 2.0 leaderboard values; whether 1,053 counts planted commands or testers (the reports disagree; the percentages fit 143 and 937 of 1,053 commands). Not re-checked here and still resting on the cited secondary sources: Modal's and E2B's isolation basis and pricing, Cloudflare Dynamic Workers pricing, Temporal's 30 Jul 2025 preview date, Graphiti's paper figures, HAL's paper figures, the CUA and Claude-for-Chrome launch numbers, the Sonnet 4.5 context-management figures, and the compaction beta details.
