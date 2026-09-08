# Voice and ambient surfaces: talking to agents, wearing them, and letting them run in the background

*Research program: agent harnesses. Written 2026-09-08. Status: draft, pending verification.*

Sourcing note. The web-search budget was exhausted before this task started, and the egress proxy allowed only code.claude.com, github.com, raw.githubusercontent.com and registry.npmjs.org. It blocked the openai.com family, ai.google.dev, support.google.com, Amazon, Apple, Meta, limitless.ai, bee.computer, rabbit.tech, poke.com, interaction.co, support.claude.com, Wikipedia, ACM, PNAS and the press. So Anthropic's Claude Code docs and changelog, OpenAI's and Google's SDK repositories and open-source projects are **primary** (read directly). Consumer products (Alexa+, Siri, the Gemini Live app, Meta glasses, Limitless, Bee, Friend, ChatGPT tasks and Pulse, Poke) rest on the author's prior knowledge and are marked **unverified**; sibling documents in this corpus are cited where they already sourced a fact.

## What this document answers

- What "voice agent" and "ambient agent" mean in harness terms, and why they are three products (dictation, conversation, always-on capture), not one.
- What shipped by September 2026: the OpenAI Realtime line, Gemini Live, voice in Claude Code and Codex, the wearable record, and the background layer (Claude Code routines, desktop tasks, `/loop`, `/goal`, channels, Dispatch; Codex automations; ChatGPT tasks and Pulse; Poke; OpenClaw).
- Which tasks suit voice, where latency and confirmation break, how vendors handle the cost of interruption, and the privacy exposure.
- A verdict for developers and for non-technical users, and what the evidence does to the thesis.

## TL;DR

- **Voice is an API commodity, not an operating surface for agents.** OpenAI's Realtime line ran from `gpt-realtime` (SDK support Sep 3, 2025) to `gpt-realtime-1.5` (Feb 24, 2026), "realtime 2" (May 7, 2026) and `gpt-realtime-2.1` in the current model enum [5][6]; Google's cookbook targets `gemini-3.1-flash-live-preview` [8]. Yet the two most capable harnesses use voice as **input only**: Claude Code's `/voice` "streams your recorded audio to Anthropic's servers for transcription" into the text prompt [15]; Codex began a "voice helper lifecycle foundation" in release 0.153.0 (Sep 2026) [20].
- **The hard problem is confirmation, not latency.** OpenAI's voice SDK: "While the voice agent is waiting for approval for the tool call, the agent will not be able to process new requests from the user" [7]. Its reference design has a small realtime model talk while a text model thinks, so answers "start with 'Let me think'" [4].
- **Turn-taking moved into the model.** Semantic VAD with an `eagerness` knob (max waits 8 s / 4 s / 2 s) and `interrupt_response` are API parameters [6]: harness logic absorbed by the vendor.
- **Standalone AI hardware failed twice; survivors are recorders and glasses.** Humane's Ai Pin went dark Feb 28, 2025 after HP paid $116M for the assets [24]; Rabbit r1 was "a $199 AI toy that fails at almost everything" [25]. What persists is capture-and-memory hardware tethered to a phone (Limitless, Bee, Friend, open-source Omi) and Meta's glasses (unverified details) [21][22].
- **Background agents are the real surface shift, and the labs built it.** Claude Code has three scheduling tiers, event channels, `/goal`, agent view, pushes where "Claude decides when to push", and Dispatch ("You message Dispatch a task, and it decides how to handle it") [9]–[17]. Cowork's scheduled tasks run "with no device online" since July 2026 [30].
- **Interruption policy is a harness component and it is thin everywhere.** Anthropic's controls are two push toggles, a presence file (Jun 2026), a cap of three idle `/goal` check-ins (Aug 25, 2026) and folded `/loop` wake-ups [14][18]. No vendor publishes an interruption-cost model.
- **Privacy is the least solved part.** Dictation audio leaves the machine; routines "appear as you" and connectors write "without asking for permission during a run"; wearables record bystanders [9][15].
- **For the thesis:** voice contradicts "reimagine the default surface" as a *primary* surface; background execution supports it, but the labs are ahead. The open ground is policy: when to interrupt, how to confirm without a screen, how to scope an unattended run.

## 1. Vocabulary: three products that share one word

Analogy. An intercom takes a barked order; a colleague holds a conversation and interrupts when it matters; a dictaphone records the day for later. All three are "voice"; each needs a different harness.

| Mode | What changes in the harness | Examples |
|---|---|---|
| **Dictation** (voice as input) | Speech-to-text into an existing text loop; only the **Surface** changes | Claude Code `/voice` [15]; Codex native voice [20]; Antigravity desktop voice input [29] |
| **Conversation** (speech-to-speech) | **Loop** runs under a sub-second clock: turn detection, barge-in, no dead air during tools; **Permissions** without a screen; audio-heavy **Context** | OpenAI Realtime [5][6]; Gemini Live API [8]; Alexa+, Siri, ChatGPT voice (unverified) |
| **Ambient / always-on** (continuous capture, proactive output) | **Runtime** on a battery; hours of audio as context; bystander privacy; the agent decides *when* to speak (**Orchestration**) | Limitless, Bee, Friend, Omi, Meta glasses [21][22]; Poke, Pulse (unverified); Dispatch, routines, channels [9][12][17] |

## 2. Conversational voice: the loop under a 500 ms clock

Human turns change hands in roughly a fifth of a second (Stivers et al. 2009, from memory) [37]. A two-second pause feels broken; an early answer talks over the user. Everything below is the industry solving those two failures.

**OpenAI Realtime.** From the SDK changelog: `gpt-realtime` Sep 3, 2025; SIP "realtime calls" Oct 2, 2025; `gpt-realtime-1.5` Feb 24, 2026; "realtime 2" and "realtime translate" May 7, 2026; "backend-mediated Realtime WebRTC calls" Aug 26, 2026 [5]. The model enum now includes `gpt-realtime-2.1` and `-2.1-mini` [6]. The turn-taking controls in the same file are harness logic moving into the API [6]:

- `semantic_vad`: "Server-side semantic turn detection which uses a model to determine when the user has finished speaking."
- `eagerness`: "`low` will wait longer for the user to continue speaking, `high` will respond more quickly ... max timeouts of 8s, 4s, and 2s respectively."
- `interrupt_response`: "automatically interrupt (cancel) any ongoing response ... when a VAD start event occurs."
- `idle_timeout_ms`: re-prompts a silent user after "the last model response's audio has finished playing."

**Gemini Live.** Google's quickstart uses `gemini-3.1-flash-live-preview`, turns on `context_window_compression` (trigger 25,600 tokens, sliding window 12,800, "so that model does not hallucinate in long conversations"), supports session resumption, exposes an `interrupted` flag, and warns "to prevent the model from interrupting itself it is important that you use headphones" [8]. That is the echo problem in one line.

**Real work takes two models.** OpenAI's `openai-realtime-agents` describes a *chat-supervisor*: a realtime model handles "basic tasks" while a text model (`gpt-4.1`) does "complex tool calls"; you get "excellent tool calling and instruction following", "lower cost" and a model that "responds to the user right away", at the price that "more assistant responses will start with 'Let me think'" [4].

```
 mic ──► realtime model (talks, fast) ──► speaker
              │ "let me check that"
              ▼ tool: getNextResponseFromSupervisor
        text model (thinks, calls real tools, slower)
              │ result
              ▼ realtime model reads it aloud
```

**Frameworks.** Pipecat (15.4k stars) supports "STT → LLM → TTS" and direct speech-to-speech across "60+ services" [2]. LiveKit Agents (14.1k) ships "Semantic turn detection: Uses a transformer model to detect when a user is done with their turn" plus telephony [3]. Neither README gives a latency number; every latency figure in this market is a vendor claim.

**The lab CLIs.** Claude Code's `/voice` is push-to-talk dictation: audio "is not processed locally", transcription "does not consume Claude messages or tokens", recording "stops automatically after 15 seconds of silence or two minutes total", and tap mode auto-submits only when "the transcript is at least three words long" so "an accidental tap does not send a stray word" [15]. Changelog and npm dates put it in Mar 2026 (v2.1.69–2.1.71) [18][19]. Codex 0.153.0 adds "the voice helper lifecycle foundation" and "Record realtime conversation history in Core": a native Realtime-API voice mode is being built into the CLI (releases index Sep 3, 2026; the tag page rendered "2025") [20].

## 3. Which tasks suit voice

| Task | Fit | Why |
|---|---|---|
| Short commands, lookups, dictating a prompt | Good | No loop change; Claude Code adds "your current project name and git branch name ... as recognition hints" [15] |
| Hands- or eyes-busy (driving, cooking, repair, walking) | Good | The one case where voice beats a screen outright; the glasses and earbuds market (unverified sizing) |
| Coaching, tutoring, language practice, support calls | Good to fair | Conversation *is* the task; SIP telephony [5]; chat-supervisor was written for support [4] |
| Multi-step agent work (edit, test, compare three fares) | Poor | Results are visual; the voice channel blocks during approvals [7]; filler speech [4] |
| Consequential, irreversible actions | Poor without a screen | No diff to inspect; VAD false positives (section 4) |
| Continuous capture for memory | Good, but not agent work | Wearables transcribe and summarise; action is deferred to the phone [21][22] |

## 4. Latency and confirmation

**Latency is a budget.** Its parts: end-of-turn detection (semantic VAD waits up to 2–8 s by design [6]), time to first audio, any tool call the answer depends on, text-to-speech, network. Speech-to-speech removes the STT and TTS hops [7] but not the tool call, so the harness fills the gap with speech or delegates to a slower model [4].

| Failure | Symptom | Mitigation shipped |
|---|---|---|
| Talk-over | Agent keeps speaking over the user | `interrupt_response`; the SDK "truncates the assistant audio to what the user actually heard, and emits an `audio_interrupted` event" [6][7] |
| Premature end-of-turn | Answers a half sentence | `semantic_vad` with `eagerness: low` (8 s) [6]; LiveKit's transformer detector [3] |
| Dead air during tools | Silence while a tool runs | Chat-supervisor fillers [4]; `idle_timeout_ms` [6] |

**Confirmation is unsolved.** Every permission design in this corpus assumes a screen: a diff, a tool preview, an Allow button. OpenAI's SDK makes approvals explicit (`needsApproval: true`, `session.approve()`) but the conversation freezes while waiting; guardrails run "every 100 characters" of transcript, after speech has begun, so an unsafe answer can be partly spoken before it stops [7]. Anthropic moved confirmation to the phone instead: Remote Control forwards permission prompts, and channels can relay them to Telegram or iMessage, with the warning that "Anyone who can reply through the channel can approve or deny tool use in your session" [12][13]. The pattern that emerges is *say it, then tap it*: voice for intent, a glanceable screen for consent.

## 5. Ambient hardware: the record

Analogy. The smartwatch succeeded as a phone accessory and failed as a phone replacement. AI hardware is repeating that curve.

| Device | Dates and price | Outcome | Source |
|---|---|---|---|
| Humane Ai Pin | Shipped Apr 2024; ~$699 plus subscription (unverified) | HP bought assets for $116M; servers off Feb 28, 2025; "~10,000 sold vs 100,000 target" | [24] |
| Rabbit r1 | $199; CES Jan 2024; shipped Apr 2024 | "a $199 AI toy that fails at almost everything" | [25] |
| Meta Ray-Ban / Ray-Ban Display | Display model with wrist band Sep 2025, ~$799; camera glasses from ~$299 (unverified) | The only mass-selling line; EssilorLuxottica reported millions of units (unverified) | unverified |
| Limitless pendant | ~$99 plus subscription (unverified); developer API over lifelog "transcripts" (primary) | Reportedly acquired by Meta, Dec 2025 (unverified) | [21] |
| Bee | ~$50 wristband plus subscription (unverified) | Reportedly acquired by Amazon, Jul 2025 (unverified) | unverified |
| Friend | ~$129 pendant; shipped 2025 (unverified) | Widely panned (unverified) | unverified |
| Omi (BasedHardware) | Open source, MIT; 13.4k stars | "transcribes in real-time, generates summaries and action items"; "Trusted by 300,000+ professionals" (vendor claim) | [22] |

Two lessons. The devices that died tried to be the whole harness (loop, tools, runtime, surface) on a weak model with 2024 latency; the sibling history doc's verdict is "runtime limits killed the surface" [24]. The survivors are **context** devices: they feed memory into a phone app and leave action to the phone. Ambient capture is a harness input, not a harness.

**Incumbent assistants** are the largest voice channels, and every specific here is unverified: Alexa+ (announced Feb 2025, free with Prime, agentic bookings), the Siri revamp (slipped from 2025; an Apple–Google Gemini deal reported Jan 2026), Gemini Live in the Gemini app (camera and screen sharing), ChatGPT voice. The structural point survives the uncertainty: whoever owns the OS or the speaker owns the wake word, and a startup does not.

## 6. Background and scheduled agents: the loop leaves the foreground

Analogy. Cron is an alarm clock. A background agent is a night-shift employee: it needs a task sheet, its own keys, and a rule for when to phone you.

**Claude Code's three tiers** (from the docs' comparison table) [10][11]:

| | Cloud routines | Desktop scheduled tasks | `/loop` (in-session) |
|---|---|---|---|
| Runs on | Anthropic-managed cloud or self-hosted env | Your machine | Your machine, session open |
| Machine on? | No | Yes; a sleeping machine's run "is skipped" | Yes |
| Minimum interval | 1 hour | 1 minute | 1 minute |
| Permissions | "no permission-mode picker and no approval prompts during a run" | Per task; Manual mode "stalls until you approve" | Inherits session |
| Persistence | Yes | Yes; one catch-up run for missed fires | 7-day expiry; restored on `--resume` |
| Triggers | Schedule, HTTP `/fire` with bearer token, GitHub PR and release events | Schedule; a task can reschedule itself via `update_scheduled_task` | Cron, or a Claude-chosen interval (1 min to 1 h) |

Details that matter to a harness designer. Routines "belong to your individual claude.ai account" and "Anything a routine does ... appears as you" [9]. Fire-payload `text` arrives "wrapped in a `<routine-fire-payload>` block that labels it as untrusted data", so a leaked token cannot inject instructions; before v2.1.213 (Jul 17, 2026) the routine's own prompt was "framed as an untrusted background notification and could refuse to act on it", injection defences fighting legitimate automation [9][19]. "A green status ... does not mean the task in your prompt succeeded" [9]. `/goal` runs until a separate small model judges the condition met, with check-ins after 30 minutes of background work that back off "up to four times the first interval" [14].

**Timeline** (changelog versions, npm dates) [18][19]: `claude remote-control` v2.1.51 (Feb 2026); cron tools and `/loop` v2.1.71 (Mar); `--channels` v2.1.80 and permission relay v2.1.81 (Mar–Apr); routines Apr 14 [32]; push notification tool v2.1.110 (Apr); agent view and `/goal` v2.1.139 (May); presence file v2.1.181 (Jun); `/goal` check-in cap v2.1.246 (Aug 25). Nine months from CLI to a runtime with a scheduler, an event bus and a pager.

**Non-developer packaging.** Dispatch "is a persistent conversation with Claude that lives in the Cowork tab. You message Dispatch a task, and it decides how to handle it", spawning a Code session when "the task is development work", with "a push notification on your phone when it finishes or needs your approval"; computer-use approvals last "30 minutes in Dispatch-spawned sessions" [17]. Cowork's July 2026 web and mobile rollout means "scheduled tasks run with no device online" [30].

**OpenAI.** Codex cloud runs at chatgpt.com/codex [20]; the app's "thread automations that resume across days or weeks" are secondary [29]; the automations docs sit on the blocked developers.openai.com. ChatGPT *tasks* (scheduled prompts with push or email, beta Jan 2025) and *Pulse* (proactive morning cards, Sep 2025) are unverified.

**Independent always-on harnesses.** OpenClaw, at 389.2k stars, "runs on your own computer", reaches "Discord, iMessage, Slack, Teams, Telegram, WhatsApp, and 20+ more", adds "voice, Canvas, camera, screen, and device-local actions" via companion apps, and warns "Treat inbound messages as untrusted input" [23]. Hermes and NanoClaw run cron with memory in sandboxes [33]. Poke, the proactive iMessage/WhatsApp assistant, is reachable only through its GitHub org, where you "connect your MCP server to Poke" (repos dated Sep–Oct 2025) [1]; its proactive texting is unverified.

## 7. Proactive agents and the cost of interruption

Analogy. A good assistant knocks once a day with a list, not twenty times with one item. The value of a proactive agent is the quality of its silence.

The grounding literature is old and could not be fetched: Horvitz's mixed-initiative principles (weigh the expected utility of interrupting against attention, CHI 1999) and Mark, Gudith and Klocke's finding that interrupted work finishes faster with more stress (CHI 2008) [35][36]. No vendor cites either. What shipped is blunt rules:

| Mechanism | Rule | Source |
|---|---|---|
| Push when Claude decides | "typically ... when a long-running task finishes or when it needs a decision from you ... Beyond the two on/off toggles below, there is no per-event configuration" | [13] |
| Presence suppression | `CLAUDE_CLIENT_PRESENCE_FILE` suppresses pushes "while you're at the machine" (v2.1.181) | [18] |
| Desktop notice | Notification "when a Code session finishes a task and you aren't currently viewing that session" | [17] |
| State, not stream | Agent view: Working / Needs input / Idle / Completed / Failed; notifies when a session "starts needing your input, finishes, or fails" | [16] |
| Bounded nagging | "at most three idle check-ins per goal between your prompts"; empty `/loop` wake-ups "fold into a single line" | [14][18] |
| Relay consent | Channels forward permission prompts; the allowlist "gates permission relay" | [12] |

```
 event ──► needs a decision? ──yes──► user present? ──yes──► in-app prompt
              │ no                          │ no ──► push to phone, wait
              ▼
        done or failed? ──yes──► one notification, then idle
              │ no
              └──► stay silent (fold repeats, cap check-ins, expire loops)
```

The asymmetry is the point. A false interruption costs attention and trust; a missed one costs a stalled run ("the run stalls until you approve it" [10]) or an unattended write ("Claude can use every tool from an included connector, including writes, without asking for permission during a run" [9]). Today's answer is to pick a permission mode per task and hope, for a developer's nightly routine and a non-technical user's Dispatch task alike.

## 8. Privacy

- **Dictation leaves the device.** "Audio is not processed locally" [15]. Fine for a prompt; not for a room.
- **Always-on capture records bystanders.** Every pendant in section 5 records people who did not consent; vendor consent features are unverified, and two-party-consent jurisdictions make the default legally fragile (unverified).
- **Chat bridges read your messages.** The iMessage channel "reads your Messages database directly" and needs Full Disk Access [12]; OpenClaw keeps "State, memory, and credentials ... on your hardware" but has the same shape [23].
- **Unattended runs carry your identity.** Routines act "as you" with no prompts; mitigations are scoping connectors and the untrusted-payload wrapper [9]. Cowork and cloud sessions now "always ask you first" before reading an artifact that is not yours, "even in auto mode" (v2.1.257) [18].
- **Incumbents.** Alexa+ data handling and Amazon's 2025 removal of local-only Echo processing are unverified.

## 9. Verdicts

**Developers.** Voice is a keyboard, not a harness. Dictation earns its place and Codex is adding a native voice mode, but every capable agent still confirms on a screen [7][15][20]. What changed the developer's day is the background tier: routines, desktop tasks, channels, `/goal` and agent view turned the CLI into a runtime monitored from a phone. The terminal became the place you read the diff.

**Non-technical users.** The voice channels with reach (Alexa+, Siri, Gemini Live, Meta's glasses) are incumbent-owned and suit short, reversible tasks. The wearables that survived are memory devices. The workable pattern in the record is *proactive over chat plus confirmation on a screen*: Dispatch, Cowork scheduled tasks, Poke, OpenClaw's channels. Voice can sit on top as input; it cannot be the whole loop until screen-less confirmation is solved.

## What this means for the thesis

**Supports.**
- "Neither a CLI nor a desktop app is the default" is what the background layer shows: one engine now runs from cloud schedules, GitHub events, HTTP triggers, Telegram, iMessage and a phone's push notification [9][12][13]. The loop left the foreground.
- "The harness becomes the bottleneck" holds for **permissions and interruption policy**: the model already runs unattended; the missing pieces are screen-less confirmation and a principled interruption policy (sections 4, 7).
- Demand for *proactive* agents is real enough that Anthropic built Dispatch and scheduled Cowork, OpenAI built tasks and Pulse, and Poke and OpenClaw found audiences (partly unverified) [17][23][30].

**Contradicts.**
- Voice as a reimagined *default* surface has the worst record in this corpus: two dead hardware companies, incumbents owning the wake word, and the most capable harnesses shipping voice as dictation only [15][24][25].
- The "harness gets thicker" half of the thesis is wrong for voice: turn detection, interruption and echo handling moved *into* the vendor's API [6].
- The labs are building the background and notification layer themselves, weekly [18][19]. A startup will not out-ship Anthropic on routines.

**Nuance.** The layer that is thin *and* not being built by the labs is policy: an interruption model weighing presence, urgency and reversibility; a confirmation protocol for eyes-free consent; per-run blast-radius scoping a non-technical user can understand. Those are harness parts 4 and 7, identical for a developer's nightly job and a parent's grocery agent. That is where the evidence says a new default could be built.

## Open questions and unverified claims

1. Alexa+, the Siri revamp, the Gemini Live app, ChatGPT voice, ChatGPT tasks and Pulse: every date, price and user number is from memory. Re-verify before use.
2. Wearable business facts: Meta's acquisition of Limitless (Dec 2025?), Amazon's of Bee (Jul 2025?), Friend's price and reviews, Meta glasses prices and units. All unverified.
3. Codex 0.153.0's date: the releases index says Sep 3, 2026; the tag page rendered "2025". Codex app automation docs are on the blocked developers.openai.com.
4. The `@openai/agents-realtime` npm metadata came back internally inconsistent, so no SDK launch date is claimed.
5. No independent end-to-end latency benchmark for speech-to-speech agents was obtained; all latency statements are structural.
6. The HCI interruption papers and the PNAS turn-taking figure are cited from memory.
7. Poke's proactive behaviour, funding and launch date; OpenClaw's heartbeat and cron features (the README fetch did not surface them; the sibling doc did).
8. Whether Cowork's scheduled tasks share the routines infrastructure is unconfirmed; the desktop docs only say local tasks and cloud routines are created from one Routines page [10].

## Sources

1. The Interaction Company of California, GitHub organisation (mcp-server-template, poke-mcp-examples), https://github.com/InteractionCo, fetched Sep 2026 (primary; repos dated Sep–Oct 2025)
2. Pipecat README, https://github.com/pipecat-ai/pipecat, Sep 2026 (primary)
3. LiveKit Agents README, https://github.com/livekit/agents, Sep 2026 (primary)
4. OpenAI, openai-realtime-agents README (chat-supervisor, sequential handoff), https://github.com/openai/openai-realtime-agents, Sep 2026 (primary)
5. OpenAI Node SDK CHANGELOG (realtime entries, Feb 2025 to Sep 8, 2026), https://github.com/openai/openai-node/blob/master/CHANGELOG.md (primary)
6. OpenAI Node SDK, `src/resources/realtime/realtime.ts` (model enum, `semantic_vad`, `eagerness`, `interrupt_response`, `idle_timeout_ms`), https://github.com/openai/openai-node/blob/master/src/resources/realtime/realtime.ts, Sep 2026 (primary)
7. OpenAI Agents SDK (JS), "Voice agents" and "Build" guides, https://github.com/openai/openai-agents-js/blob/main/docs/src/content/docs/guides/voice-agents.mdx and .../voice-agents/build.mdx, Sep 2026 (primary)
8. Google Gemini cookbook, `quickstarts/Get_started_LiveAPI.py`, https://github.com/google-gemini/cookbook, Sep 2026 (primary)
9. Claude Code docs, "Automate work with routines", https://code.claude.com/docs/en/routines, Sep 2026 (primary)
10. Claude Code docs, "Schedule recurring tasks in Claude Code Desktop", https://code.claude.com/docs/en/desktop-scheduled-tasks, Sep 2026 (primary)
11. Claude Code docs, "Run prompts on a schedule", https://code.claude.com/docs/en/scheduled-tasks, Sep 2026 (primary)
12. Claude Code docs, "Push events into a running session with channels", https://code.claude.com/docs/en/channels, Sep 2026 (primary)
13. Claude Code docs, "Continue local sessions from any device with Remote Control", https://code.claude.com/docs/en/remote-control, Sep 2026 (primary)
14. Claude Code docs, "Keep Claude working toward a goal", https://code.claude.com/docs/en/goal, Sep 2026 (primary)
15. Claude Code docs, "Voice dictation", https://code.claude.com/docs/en/voice-dictation, Sep 2026 (primary)
16. Claude Code docs, "Agent view", https://code.claude.com/docs/en/agent-view, Sep 2026 (primary)
17. Claude Code docs, "Desktop application" (Sessions from Dispatch; notifications), https://code.claude.com/docs/en/desktop, Sep 2026 (primary)
18. Claude Code CHANGELOG (v2.1.51 to v2.1.263), https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md, Sep 2026 (primary)
19. npm registry, `@anthropic-ai/claude-code` publish times (2.1.0 Jan 7, 2026; 2.1.75 Mar 13; 2.1.100 Apr 10; 2.1.150 May 23; 2.1.213 Jul 17; 2.1.246 Aug 25; 2.1.263 Sep 6, 2026), https://registry.npmjs.org/@anthropic-ai/claude-code (primary)
20. OpenAI Codex repository: README, docs index, release rust-v0.153.0 notes, https://github.com/openai/codex and https://github.com/openai/codex/releases/tag/rust-v0.153.0, Sep 2026 (primary)
21. Limitless, limitless-api-examples README and organisation page, https://github.com/limitless-ai-inc/limitless-api-examples, Sep 2026 (primary)
22. BasedHardware, Omi README, https://github.com/BasedHardware/omi, Sep 2026 (primary; "300,000+" is a vendor claim)
23. OpenClaw README, https://github.com/openclaw/openclaw, Sep 2026 (primary)
24. Sibling doc `docs/01-primer/history.md` citing TechCrunch, "Humane's AI Pin is dead as HP buys startup's assets for $116M", https://techcrunch.com/2025/02/18/humanes-ai-pin-is-dead-as-hp-buys-startups-assets-for-116m, Feb 2025 (secondary)
25. Sibling doc `docs/01-primer/history.md` citing Engadget, "Rabbit R1 review", https://www.engadget.com/rabbit-r1-review-a-199-ai-toy-that-fails-at-almost-everything-161043050.html, May 2024 (secondary)
26. Sibling doc `docs/01-primer/anatomy.md` (scheduling tiers), Sep 2026 (corpus)
27. Sibling doc `docs/04-research/industry-engineering.md` ("Each surface connects to the same underlying Claude Code engine"), Sep 2026 (corpus)
28. Sibling doc `docs/07-strategy/value-capture.md` (Cowork Jan 2026; Codex app Feb 2026; Copilot Cowork Jun 2026), Sep 2026 (corpus)
29. Sibling doc `docs/02-landscape/developer-harnesses-labs.md` (Cowork dates; Codex app and automations; Antigravity 2.0 voice input), Sep 2026 (corpus)
30. Anthropic help center, "Use Claude Cowork on web, desktop and mobile", https://support.claude.com/en/articles/15520349-use-claude-cowork-on-web-desktop-and-mobile, Jul 2026 (search excerpt via [29]; not fetched)
31. TechCrunch on Antigravity 2.0 at I/O, May 19, 2026 (search excerpt via [29]; not fetched)
32. Claude Code "What's new", week 16 (routines launched Apr 14, 2026), https://code.claude.com/docs/en/whats-new/2026-w16 (cited in `docs/01-primer/history.md`; not re-fetched)
33. Sibling doc `docs/02-landscape/developer-harnesses-independent.md` (OpenClaw, Hermes, NanoClaw), Sep 2026 (corpus)
34. Anthropic, Claude Desktop "Dispatch" help article, https://support.claude.com/en/articles/13947068 (linked from [17]; not fetched)
35. E. Horvitz, "Principles of mixed-initiative user interfaces", CHI 1999, doi:10.1145/302979.303030 (not fetched; from memory)
36. G. Mark, D. Gudith, U. Klocke, "The cost of interrupted work: more speed and stress", CHI 2008, doi:10.1145/1357054.1357072 (not fetched; from memory)
37. T. Stivers et al., "Universals and cultural variation in turn-taking in conversation", PNAS 2009, doi:10.1073/pnas.0903616106 (not fetched; from memory)
38. Google, live-api-web-console README (proactive-audio demo branches; "not an official Google product"), https://github.com/google-gemini/live-api-web-console, Sep 2026 (primary)
