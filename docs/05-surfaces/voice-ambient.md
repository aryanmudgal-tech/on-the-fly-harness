# Voice and ambient surfaces: talking to agents, wearing them, and letting them run in the background

*Research program: agent harnesses. Written 2026-09-08. Status: verified with notes. Verification pass 2026-09-08; see "Verification notes" at the end.*

Sourcing note. The web-search budget was exhausted before this task started, and the egress proxy allowed only code.claude.com, github.com, raw.githubusercontent.com and registry.npmjs.org. It blocked the openai.com family, ai.google.dev, support.google.com, Amazon, Apple, Meta, limitless.ai, bee.computer, rabbit.tech, poke.com, interaction.co, support.claude.com, Wikipedia, ACM, PNAS and the press. So Anthropic's Claude Code docs and changelog, OpenAI's and Google's SDK repositories and open-source projects are **primary** (read directly). Consumer products (Alexa+, Siri, the Gemini Live app, Meta glasses, Limitless, Bee, Friend, ChatGPT tasks and Pulse, Poke) rest on the author's prior knowledge and are marked **unverified**; sibling documents in this corpus are cited where they already sourced a fact. **Verification pass (2026-09-08):** the primary sources above were re-read and every quoted figure checked; consumer-product claims were checked against two or more independent search excerpts because the press, vendor and help-centre domains stayed blocked (the list is in the Verification notes). Corrections are made in place; claims that could not be checked stay marked "(unverified)".

## What this document answers

- What "voice agent" and "ambient agent" mean in harness terms, and why they are three products (dictation, conversation, always-on capture), not one.
- What shipped by September 2026: the OpenAI Realtime line, Gemini Live, voice in Claude Code and Codex, the wearable record, and the background layer (Claude Code routines, desktop tasks, `/loop`, `/goal`, channels, Dispatch; Codex automations; ChatGPT tasks and Pulse; Poke; OpenClaw).
- Which tasks suit voice, where latency and confirmation break, how vendors handle the cost of interruption, and the privacy exposure.
- A verdict for developers and for non-technical users, and what the evidence does to the thesis.

## TL;DR

- **Voice is an API commodity, not an operating surface for agents.** OpenAI's Realtime line ran from `gpt-realtime` (SDK support Sep 3, 2025) to `gpt-realtime-1.5` (Feb 24, 2026), "realtime 2" (May 7, 2026) and `gpt-realtime-2.1` / `-2.1-mini` (announced Jul 2026, with a vendor claim of a 25%+ cut in p95 latency) in the current model enum [5][6][39]; Google's cookbook targets `gemini-3.1-flash-live-preview` [8]. Yet the two most capable harnesses use voice as **input only**: Claude Code's `/voice` "streams your recorded audio to Anthropic's servers for transcription" into the text prompt [15]; Codex began a "voice helper lifecycle foundation" in release 0.153.0 (Sep 2026) [20].
- **The hard problem is confirmation, not latency.** OpenAI's voice SDK: "While the voice agent is waiting for approval for the tool call, the agent will not be able to process new requests from the user" [7]. Its reference design has a small realtime model talk while a text model thinks, so answers "start with 'Let me think'" [4].
- **Turn-taking moved into the model.** Semantic VAD with an `eagerness` knob (max waits 8 s / 4 s / 2 s) and `interrupt_response` are API parameters [6]: harness logic absorbed by the vendor.
- **Standalone AI hardware died once and pivoted once; the survivors are recorders, glasses and a shell for other people's harnesses.** Humane's Ai Pin went dark Feb 28, 2025 after HP paid $116M for the assets [24]. Rabbit r1 was "a $199 AI toy that fails at almost everything" at launch [25], but the company did not die: in 2026 it raised new money and turned the r1 into a host for third-party agents (OpenClaw in alpha, the Hermes agent, a "proactive rabbit" mode, Jul 2026) [40]. The capture-and-memory pendants were bought by incumbents (Amazon acquired Bee, Jul 2025; Meta acquired Limitless, Dec 2025) [42][43]; Friend relaunched at $249 with a speaker (Jul 2026) [44]; Meta's Ray-Ban Display ($799 with the Neural Band, on sale Sep 30, 2025) is the only mass-market line [41]; open-source Omi is what is left for tinkerers [22].
- **Background agents are the real surface shift, and the labs built it.** Claude Code has three scheduling tiers, event channels, `/goal`, agent view, pushes where "Claude decides when to push", and Dispatch ("You message Dispatch a task, and it decides how to handle it") [9]–[17]. Cowork's scheduled tasks run "with no device online" since July 2026 [30]. OpenAI's equivalents are Codex automations, which since Apr 16, 2026 "run in the same thread" and can wake themselves for long-running work, and ChatGPT scheduled tasks (Jun 17, 2026) [47][50].
- **Interruption policy is a harness component and it is thin everywhere.** Anthropic's controls are two push toggles, a presence file (Jun 2026), a cap of three idle `/goal` check-ins (Aug 25, 2026) and folded `/loop` wake-ups [14][18]. No vendor publishes an interruption-cost model. The one natural experiment is ChatGPT Pulse, the unrequested morning feed launched Sep 25, 2025: OpenAI retired it in July 2026 in favour of schedules the user sets [47].
- **Privacy is the least solved part.** Dictation audio leaves the machine; routines "appear as you" and connectors write "without asking for permission during a run"; wearables record bystanders [9][15].
- **For the thesis:** voice contradicts "reimagine the default surface" as a *primary* surface; background execution supports it, but the labs are ahead. The open ground is policy: when to interrupt, how to confirm without a screen, how to scope an unattended run.

## 1. Vocabulary: three products that share one word

Analogy. An intercom takes a barked order; a colleague holds a conversation and interrupts when it matters; a dictaphone records the day for later. All three are "voice"; each needs a different harness.

| Mode | What changes in the harness | Examples |
|---|---|---|
| **Dictation** (voice as input) | Speech-to-text into an existing text loop; only the **Surface** changes | Claude Code `/voice` [15]; Codex native voice [20]; Antigravity desktop voice input [29] |
| **Conversation** (speech-to-speech) | **Loop** runs under a sub-second clock: turn detection, barge-in, no dead air during tools; **Permissions** without a screen; audio-heavy **Context** | OpenAI Realtime [5][6]; Gemini Live API [8]; Alexa+ [45], Siri (Gemini-based revamp, 2026) [46], Gemini Live app [48], ChatGPT voice (unverified) |
| **Ambient / always-on** (continuous capture, proactive output) | **Runtime** on a battery; hours of audio as context; bystander privacy; the agent decides *when* to speak (**Orchestration**) | Limitless, Bee, Friend, Omi, Meta glasses [21][22]; Poke (Cognition-owned since Jul 2026) [49], Pulse (retired Jul 2026) [47]; Dispatch, routines, channels [9][12][17] |

## 2. Conversational voice: the loop under a 500 ms clock

Human turns change hands in roughly a fifth of a second (Stivers et al. 2009, from memory) [37]. A two-second pause feels broken; an early answer talks over the user. Everything below is the industry solving those two failures.

**OpenAI Realtime.** From the SDK changelog: `gpt-realtime` Sep 3, 2025; SIP "realtime calls" Oct 2, 2025; `gpt-realtime-1.5` Feb 24, 2026; "realtime 2" and "realtime translate" May 7, 2026; "backend-mediated Realtime WebRTC calls" Aug 26, 2026 [5]. The model enum now includes `gpt-realtime-2.1` and `-2.1-mini` [6]. OpenAI's own framing of the May 7, 2026 release (read as search excerpts; openai.com is blocked) was GPT-Realtime-2 as "the first voice model with GPT-5-class reasoning", GPT-Realtime-Translate (70+ input languages into 13 output languages) and GPT-Realtime-Whisper for streaming transcription; `gpt-realtime-2.1` and `-2.1-mini` followed in early July 2026 with a claimed 25%+ cut in p95 latency from caching [39]. The reasoning knob is visible in the SDK: `reasoning.effort` applies to "reasoning-capable Realtime models such as `gpt-realtime-2`", and the Agents SDK warns that "Higher reasoning effort can increase latency and token usage", so the latency budget below now has a thinking line item too [6][7]. The turn-taking controls in the same file are harness logic moving into the API [6]:

- `semantic_vad`: "Server-side semantic turn detection which uses a model to determine when the user has finished speaking."
- `eagerness`: "`low` will wait longer for the user to continue speaking, `high` will respond more quickly ... max timeouts of 8s, 4s, and 2s respectively."
- `interrupt_response`: "automatically interrupt (cancel) any ongoing response ... when a VAD start event occurs."
- `idle_timeout_ms`: re-prompts a silent user after "the last model response's audio has finished playing."

**Gemini Live.** Google's quickstart uses `gemini-3.1-flash-live-preview`, turns on `context_window_compression` (trigger 25,600 tokens, sliding window 12,800, "so that model does not hallucinate in long conversations"), supports session resumption, exposes an `interrupted` flag, and warns "to prevent the model from interrupting itself it is important that you use headphones" [8]. That is the echo problem in one line. In the consumer Gemini app, 2026 brought redesigned "Neural Expressive" voices for Live (May 27, 2026) and, in August 2026, voice-driven task features (Personal Intelligence, Daily Brief, Spark, inbox handling by voice across Workspace), per Google's and 9to5Google's accounts read as search excerpts [48].

**Real work takes two models.** OpenAI's `openai-realtime-agents` describes a *chat-supervisor*: a realtime model handles "basic tasks" while a text model (`gpt-4.1`) does "complex tool calls"; you get "excellent tool calling and instruction following", "lower cost" and a model that "responds to the user right away", at the price that "more assistant responses will start with 'Let me think'" [4]. The README's own demo transcript notes "~2s between the end of 'give me a moment to check on that.' being spoken aloud and the start of" the real answer, against "1.5s or longer" for a stitched STT-LLM-TTS chain: the only latency figures in these sources, and they are the dead-air cost of the pattern [4].

```
 mic ──► realtime model (talks, fast) ──► speaker
              │ "let me check that"
              ▼ tool: getNextResponseFromSupervisor
        text model (thinks, calls real tools, slower)
              │ result
              ▼ realtime model reads it aloud
```

**Frameworks.** Pipecat (15.4k stars) supports "STT → LLM → TTS" and direct speech-to-speech across "60+ services" [2]. LiveKit Agents (14.1k) ships "Semantic turn detection: Uses a transformer model to detect when a user is done with their turn" plus telephony [3]. Neither README gives a latency number; every latency figure in this market is a vendor claim.

**The lab CLIs.** Claude Code's `/voice` is push-to-talk dictation: audio "is not processed locally", transcription "does not consume Claude messages or tokens", recording "stops automatically after 15 seconds of silence or two minutes total", and tap mode auto-submits only when "the transcript is at least three words long" so "an accidental tap does not send a stray word" [15]. The changelog's first voice entries, in v2.1.69 (Mar 4, 2026), are fixes plus an expansion to 20 dictation languages, so the feature was already live, feature-flagged, before it was documented; there is no "added `/voice`" entry, and the docs' first version reference is v2.1.195 [15][18][19]. Codex 0.153.0 adds "the voice helper lifecycle foundation" and "Record realtime conversation history in Core": a native Realtime-API voice mode is being built into the CLI (tag `rust-v0.153.0` dated Sep 3, 2026 UTC; the repository's `docs/` directory has no voice page yet) [20].

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
| Humane Ai Pin | Shipped Apr 2024; $699 plus a $24/month subscription (unverified) | HP bought the assets for $116M (announced Feb 18, 2025); servers off Feb 28, 2025 with server-side data deleted; returns outpaced sales from mid-2024; "~10,000 sold vs 100,000 target" (unverified) | [24][52] |
| Rabbit r1 | $199; CES Jan 2024; shipped Apr 2024; still $199 and still shipping in 2026 | Panned at launch ("a $199 AI toy that fails at almost everything"), then pivoted: new funding, DLAM computer control, OpenClaw (alpha) and the Hermes agent on the device, "proactive rabbit" (Jul 10, 2026), and a second hardware product announced | [25][40] |
| Meta Ray-Ban / Ray-Ban Display | Ray-Ban Display with the EMG Neural Band announced Sep 17, 2025, $799, on sale Sep 30, 2025 (600x600 display, ~6 h mixed use); camera-only Ray-Ban Meta from ~$299 (unverified) | The only mass-selling line; EssilorLuxottica unit numbers (unverified) | [41] |
| Limitless pendant | ~$99 at launch plus subscription (price unverified); developer API over lifelog "transcripts" (primary) | Acquired by Meta, announced Dec 5, 2025 (terms undisclosed); pendant sales stopped at once, owners promised at least a year of support and data export; team folded into Reality Labs | [21][42] |
| Bee | $49.99 wristband plus $19/month; also an Apple Watch app | Acquisition by Amazon announced Jul 22, 2025 (price undisclosed; whole team joining Amazon) | [43] |
| Friend | $99 at pre-order (2024), $129 when units shipped (2025, after delays), $249 for the Jul 2026 version with a speaker and spoken personality, plus an optional $10/month memory plan | WIRED's review found the $129 unit delivered "something closer to digital harassment" than friendship; a Museum of Failure exhibit; early reviews of the 2026 version still found it prone to misreading emotional context | [44] |
| Omi (BasedHardware) | Open source, MIT; 13.4k stars | "transcribes in real-time, generates summaries and action items"; "Trusted by 300,000+ professionals" (vendor claim) | [22] |

Two lessons. The device that died tried to be the whole harness (loop, tools, runtime, surface) on a weak model with 2024 latency; the sibling history doc's verdict is "runtime limits killed the surface" [24]. The device that lived stopped being a harness: Rabbit's 2026 r1 is a $199 shell that runs OpenClaw and Hermes, other people's loops [40]. The other survivors are **context** devices: they feed memory into a phone app and leave action to the phone. Ambient capture is a harness input, not a harness.

**Incumbent assistants** are the largest voice channels. Checked against multiple press excerpts (the vendor sites are blocked): Alexa+ (announced Feb 2025 (date unverified); free for Prime members, $19.99/month otherwise) reached every US Prime member on Feb 4, 2026, then the UK (Mar 19, £19.99), Spain (Apr 23), Germany (May 7, €22.99), France (May 26) and Australia [45]; the Siri revamp, which slipped from 2025, is being rebuilt on Gemini-based "Apple Foundation Models" under an Apple–Google deal first reported Jan 12, 2026 and confirmed by Google on Apr 22, 2026, reportedly worth about $1B a year, with a phased rollout across iOS 26.4 and iOS 27 (which parts have shipped as of Sep 8, 2026 is unverified) [46]; the Gemini app's Live mode gained new voices in May 2026 and voice-driven task features in Aug 2026 [48]. ChatGPT voice's 2026 state remains unverified, and no user count for any of these could be verified. The structural point survives: whoever owns the OS or the speaker owns the wake word, and a startup does not; Amazon and Meta have now also bought the two pendant startups [42][43].

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

**Timeline** (changelog versions, npm dates) [18][19]: `claude remote-control` v2.1.51 (Feb 2026); cron tools and `/loop` v2.1.71 (Mar 6); `--channels` v2.1.80 and permission relay v2.1.81 (Mar 19–20); routines Apr 14 (week 16, Apr 13–17) [32]; push notification tool v2.1.110 (Apr); agent view and `/goal` v2.1.139 (May); presence file v2.1.181 (Jun); `/goal` check-in cap v2.1.246 (Aug 25). Nine months from CLI to a runtime with a scheduler, an event bus and a pager.

**Non-developer packaging.** Dispatch "is a persistent conversation with Claude that lives in the Cowork tab. You message Dispatch a task, and it decides how to handle it", spawning a Code session when "the task is development work", with "a push notification on your phone when it finishes or needs your approval"; computer-use approvals last "30 minutes in Dispatch-spawned sessions" [17]. Cowork's July 2026 web and mobile rollout means "scheduled tasks run with no device online" [30].

**OpenAI.** Codex cloud runs at chatgpt.com/codex [20]. The Codex app's Automations pair a prompt and optional skills with a schedule, add event triggers, and since Apr 16, 2026 "run in the same thread, so Codex can pick up where it left off, with the original context intact", scheduling future work and waking themselves for long-running tasks; the same day's update bundled scheduled automations with 90+ plugins [29][50]. The automations docs sit on the blocked developers.openai.com and the CLI repository's `docs/` has no automations page [20]. On the consumer side, ChatGPT *tasks* (scheduled prompts with push or email; beta Jan 2025, unverified) were relaunched as *scheduled tasks* on Jun 17, 2026 for Go, Plus, Pro, Business and Enterprise, extended to Free users on Aug 25, 2026 (single excerpt), and *Pulse* (proactive morning cards, launched Sep 25, 2025 for Pro on mobile) was retired 14 days after that launch, its useful parts folded into schedules and web-monitoring tasks the user configures explicitly [47].

**Independent always-on harnesses.** OpenClaw, at 389.2k stars, "runs on your own computer", reaches "Discord, iMessage, Slack, Teams, Telegram, WhatsApp, and 20+ more", adds "voice, Canvas, camera, screen, and device-local actions" via companion apps, and warns "Treat inbound messages as untrusted input" [23]. Hermes and NanoClaw run cron with memory in sandboxes [33]. Poke, the iMessage/WhatsApp assistant from The Interaction Company, was acquired by Cognition (maker of Devin) on Jul 23, 2026 for a reported "low nine figures"; at the sale it claimed hundreds of thousands of users, over 100 million messages in three months, and the first third-party agent approval for Apple Messages for Business, and Cognition said it would put its models and infrastructure behind Poke and Poke's personality into Devin [49]. Its GitHub org still only shows how to "connect your MCP server to Poke" (repos updated through Aug 2026) [1]; its proactive texting behaviour rests on the program's sibling document rather than on a fetched source.

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

The clearest market signal on interruption cost came from OpenAI: Pulse, the one feed that pushed unrequested cards every morning, lasted nine months before being replaced by schedules the user sets (Jun–Jul 2026) [47]; Google's Daily Brief in Gemini Live (Aug 2026) is the same idea re-packaged as a briefing the user asks for [48]. Neither vendor published why.

The asymmetry is the point. A false interruption costs attention and trust; a missed one costs a stalled run ("the run stalls until you approve it" [10]) or an unattended write ("Claude can use every tool from an included connector, including writes, without asking for permission during a run" [9]). Today's answer is to pick a permission mode per task and hope, for a developer's nightly routine and a non-technical user's Dispatch task alike.

## 8. Privacy

- **Dictation leaves the device.** "Audio is not processed locally" [15]. Fine for a prompt; not for a room.
- **Always-on capture records bystanders.** Every pendant in section 5 records people who did not consent; vendor consent features are unverified, and two-party-consent jurisdictions make the default legally fragile (unverified).
- **Chat bridges read your messages.** The iMessage channel "reads your Messages database directly" and needs Full Disk Access [12]; OpenClaw keeps "State, memory, and credentials ... on your hardware" but has the same shape [23].
- **Unattended runs carry your identity.** Routines act "as you" with no prompts; mitigations are scoping connectors and the untrusted-payload wrapper [9]. Cowork and cloud sessions now "always ask you first" before reading an artifact that is not yours, "even in auto mode" (v2.1.257) [18].
- **Incumbents.** Alexa+ data handling and Amazon's 2025 removal of local-only Echo processing are unverified. The two pendant makers now belong to Amazon and Meta, so always-on capture data sits with the two largest advertising-and-commerce platforms [42][43].

## 9. Verdicts

**Developers.** Voice is a keyboard, not a harness. Dictation earns its place and Codex is adding a native voice mode, but every capable agent still confirms on a screen [7][15][20]. What changed the developer's day is the background tier: routines, desktop tasks, channels, `/goal` and agent view turned the CLI into a runtime monitored from a phone. The terminal became the place you read the diff.

**Non-technical users.** The voice channels with reach (Alexa+, free with Prime in six countries by Sep 2026; the Gemini-based Siri due in 2026; Gemini Live; Meta's glasses) are incumbent-owned and suit short, reversible tasks. The wearables that survived are memory devices, and two of them now belong to Amazon and Meta; the one standalone device still shipping, Rabbit's r1, survives as a host for open-source harnesses rather than as a harness. The workable pattern in the record is *proactive over chat plus confirmation on a screen*: Dispatch, Cowork scheduled tasks, Poke, OpenClaw's channels. The record also shows what does not work: unrequested proactivity. ChatGPT Pulse was withdrawn within nine months in favour of explicit schedules, and the chat-native proactive assistant with the most users, Poke, was sold to a coding-agent company [47][49]. Voice can sit on top as input; it cannot be the whole loop until screen-less confirmation is solved.

## What this means for the thesis

**Supports.**
- "Neither a CLI nor a desktop app is the default" is what the background layer shows: one engine now runs from cloud schedules, GitHub events, HTTP triggers, Telegram, iMessage and a phone's push notification [9][12][13]. The loop left the foreground.
- "The harness becomes the bottleneck" holds for **permissions and interruption policy**: the model already runs unattended; the missing pieces are screen-less confirmation and a principled interruption policy (sections 4, 7).
- Demand for *proactive* agents is real enough that Anthropic built Dispatch and scheduled Cowork, OpenAI built tasks, Pulse and Codex automations, Google added Daily Brief to Gemini Live, and Poke (hundreds of thousands of users at its Jul 2026 sale) and OpenClaw (389k stars) found audiences [17][23][30][47][48][49].

**Contradicts.**
- Voice as a reimagined *default* surface has the worst record in this corpus: one dead hardware company and one that survived only by hosting other people's agents, incumbents owning the wake word, and the most capable harnesses shipping voice as dictation only [15][24][25].
- The "harness gets thicker" half of the thesis is wrong for voice: turn detection, interruption and echo handling moved *into* the vendor's API [6].
- The labs are building the background and notification layer themselves, weekly [18][19]. A startup will not out-ship Anthropic on routines.

**Nuance.** The layer that is thin *and* not being built by the labs is policy: an interruption model weighing presence, urgency and reversibility; a confirmation protocol for eyes-free consent; per-run blast-radius scoping a non-technical user can understand. Those are harness parts 4 and 7, identical for a developer's nightly job and a parent's grocery agent. That is where the evidence says a new default could be built. The Pulse retirement is the first natural experiment on that policy: a lab pushed unrequested output daily, users did not keep it, and the replacement is explicit scheduling, the bluntest possible interruption policy [47].

## Open questions and unverified claims

1. Alexa+ pricing and rollout, the Siri–Gemini deal, Gemini Live's 2026 features, Pulse's launch and retirement and ChatGPT scheduled tasks are now confirmed from multiple press excerpts (sections 5 and 6). Still unverified: Alexa+'s Feb 2025 announcement date and any user counts, which Siri features have actually shipped, ChatGPT voice's 2026 state, and the Jan 2025 date of the original ChatGPT tasks beta.
2. Wearable business facts: the Limitless (Dec 5, 2025) and Bee (Jul 22, 2025) acquisitions, Friend's price history and the Ray-Ban Display price and dates are confirmed. Still unverified: Limitless's pendant price, Ray-Ban Meta camera-glasses prices, EssilorLuxottica unit numbers, and what Amazon and Meta have done with Bee and Limitless since buying them.
3. Resolved: the `rust-v0.153.0` tag is dated Sep 3, 2026 (UTC). Codex app automation docs remain on the blocked developers.openai.com; the CLI repository's `docs/` has no voice or automations page.
4. The `@openai/agents-realtime` npm metadata came back internally inconsistent, so no SDK launch date is claimed.
5. No independent end-to-end latency benchmark for speech-to-speech agents was obtained; all latency statements are structural.
6. The HCI interruption papers and the PNAS turn-taking figure are cited from memory.
7. Poke's ownership (Cognition, Jul 23, 2026) and scale are confirmed; its proactive texting behaviour and launch date were not independently fetched. OpenClaw's heartbeat and cron features: the README still does not mention them; the sibling doc does.
8. Whether Cowork's scheduled tasks share the routines infrastructure is unconfirmed; the desktop docs only say local tasks and cloud routines are created from one Routines page [10]. The July 2026 date for Cowork's web and mobile rollout rests on the sibling doc's help-centre excerpt; the feature itself is confirmed by three independent excerpts [30][51].
9. Rabbit's 2026 funding amount and its announced second device: only the vendor's blog excerpts and one review were seen; rabbit.tech is blocked [40].
10. Humane's unit sales (~10,000 against a 100,000 target) and price ($699 plus $24/month) are still from memory; the shutdown and the HP price are confirmed [52].

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
19. npm registry, `@anthropic-ai/claude-code` publish times (2.1.0 Jan 7, 2026; 2.1.51 Feb 23; 2.1.69 Mar 4; 2.1.71 Mar 6; 2.1.75 Mar 13; 2.1.80 Mar 19; 2.1.81 Mar 20; 2.1.100 Apr 10; 2.1.110 Apr 15; 2.1.139 May 11; 2.1.150 May 23; 2.1.181 Jun 17; 2.1.213 Jul 17; 2.1.246 Aug 25; 2.1.257 Sep 1; 2.1.263 Sep 6, 2026), https://registry.npmjs.org/@anthropic-ai/claude-code (primary; all re-checked Sep 8, 2026)
20. OpenAI Codex repository: README, `docs/` directory, release rust-v0.153.0 notes (tag dated 2026-09-03T00:52Z; releases index shows Sep 3, 2026), https://github.com/openai/codex and https://github.com/openai/codex/releases/tag/rust-v0.153.0, Sep 2026 (primary)
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
39. OpenAI, "Advancing voice intelligence with new models in the API" (May 7, 2026: GPT-Realtime-2, -Translate, -Whisper) and OpenAI Developer Community, "New Realtime models on the API: gpt-realtime-2.1 and gpt-realtime-2.1-mini" (Jul 2026); MarkTechPost, Jul 6, 2026. Search excerpts only; openai.com, community.openai.com and marktechpost.com are blocked (secondary)
40. Rabbit, "First major update of 2026 — DLAM, OpenClaw, and a surprise" and "rabbit r1 - quarterly update" (Q2 2026), rabbit.tech; Layer3Labs, "Rabbit R1 Review (2026)" (Aug 2026); Yahoo Tech, "Rabbit overhauls r1 AI device". Search excerpts only; rabbit.tech and layer3labs.io are blocked (secondary)
41. 9to5Google, "Meta Ray-Ban Display smart glasses launch for $799", Sep 17, 2025; Road to VR; Tom's Guide; Meta newsroom, "Meta Ray-Ban Display: AI Glasses With an EMG Wristband", Sep 2025. Search excerpts only; press and meta.com blocked (secondary)
42. CNBC, "Meta acquiring AI wearable company Limitless", Dec 5, 2025; AI Business; MLQ News; Sacra. Search excerpts only; cnbc.com blocked (secondary)
43. TechCrunch, "Amazon acquires Bee, the AI wearable that records everything you say", Jul 22, 2025; Entrepreneur; Retail Dive. Search excerpts only; techcrunch.com blocked (secondary)
44. The Gadgeteer, "Friend AI Pendant Now Talks Back at $249", Jul 31, 2026; TNW, "Friend's AI pendant returns with a speaker, at double the price"; TechCrunch, "Friend delays shipments of its AI companion pendant", Jan 20, 2025; TechBuzz on WIRED's review of the $129 unit; Museum of Failure exhibit page. Search excerpts only (secondary)
45. MacRumors, "Amazon's Alexa+ Now Free for All U.S. Prime Members", Feb 4, 2026; 9to5Toys; WinBuzzer; About Amazon, "Alexa+ arrives in Australia" (international dates and prices), 2026. Search excerpts only; macrumors.com and aboutamazon.com blocked (secondary)
46. TechCrunch, "Google's Gemini to power Apple's AI features like Siri", Jan 12, 2026; MacRumors and AppleInsider, "Google confirms Gemini-powered Siri coming later this year", Apr 22, 2026. Search excerpts only (secondary)
47. 9to5Mac, "OpenAI launches scheduled tasks in ChatGPT", Jun 17, 2026; MSN; Enterprise DNA; TechJack Solutions (Pulse retired); Yellow.com, "ChatGPT Pulse Is Dying As Scheduled Tasks Become The Hub"; Prowlo, "ChatGPT Pulse Shut Down"; scriptbyai timeline (Pulse launch Sep 25, 2025). Search excerpts only; all blocked (secondary)
48. 9to5Google, "New Gemini Live voices rolling out", May 27, 2026; Android Headlines; Google, "The latest AI news we announced in August 2026" and "Turn your voice into action with new productivity features in Gemini Live", blog.google, Aug 2026. Search excerpts only; blog.google blocked (secondary)
49. TechCrunch, "Why Cognition bought Poke: AI personality is becoming a competitive advantage", Jul 24, 2026; Dealroom; KuCoin News; Crypto Briefing (deal announced Jul 23, 2026, "low nine figures"); GitHub search for the InteractionCo organisation (repos updated Apr–Aug 2026). Search excerpts and GitHub API; techcrunch.com blocked (secondary)
50. OpenAI, "Introducing the Codex app" and OpenAI Academy, "Scheduled tasks" (Codex automations); OpenAI on X, "Automations can now run in the same thread" (post ID dates it to Apr 16, 2026); Tudor Daniel, "OpenAI Codex Automations: Schedules and Triggers Guide", Apr 24, 2026; Spicy Advisory on the Apr 16, 2026 update. Search excerpts only; openai.com and the blogs blocked (secondary)
51. Anthropic, "Claude Cowork on web and mobile: hand off work anywhere", claude.com blog, 2026; Claude Help Center, "Schedule recurring tasks in Claude Cowork" (article 13854387); Android Authority, "Claude Cowork lands on web and mobile". Search excerpts only; claude.com and support.claude.com blocked (secondary)
52. Axios, "Humane AI Pin shut down, HP acquisition", Feb 18, 2025; How-To Geek; gHacks; GSMArena; TECHi. Search excerpts only; press blocked (secondary)
53. GitHub repository metadata via the GitHub API (star counts and update times), Sep 8, 2026: openclaw/openclaw 389,242; pipecat-ai/pipecat 15,351; livekit/agents 14,074; BasedHardware/omi 13,429 (primary)

## Verification notes (2026-09-08)

Method. WebSearch was available for the first fourteen queries of this pass and then hit the session cap; the rest was done with direct fetches of github.com, raw.githubusercontent.com, code.claude.com and the npm registry, plus the GitHub API for repository metadata. Every fetch of a press, vendor or help-centre domain was refused by the egress proxy (list below), so consumer-product claims are graded "confirmed" only where two or more independent search excerpts agreed.

### Claims table

| # | Claim in the draft | Result | Evidence |
|---|---|---|---|
| 1 | OpenAI Realtime SDK dates: `gpt-realtime` Sep 3, 2025; calls Oct 2, 2025; `-1.5` Feb 24, 2026; realtime 2 and translate May 7, 2026; backend-mediated WebRTC Aug 26, 2026 | Confirmed | openai-node CHANGELOG entries 5.19.0, 6.1.0, 6.24.0, 6.37.0, 7.6.0 [5] |
| 2 | Model enum has `gpt-realtime-2.1`/`-2.1-mini`; `eagerness` max timeouts 8 s / 4 s / 2 s; `interrupt_response`; `idle_timeout_ms` | Confirmed | `realtime.ts` read directly [6] |
| 3 | Agents SDK: approval freezes the voice agent; guardrails "every 100 characters"; audio truncated to what the user heard | Confirmed | voice-agents guides read directly [7] |
| 4 | Gemini cookbook uses `gemini-3.1-flash-live-preview`, compression 25,600 / 12,800, headphones warning | Confirmed | `Get_started_LiveAPI.py` read directly [8] |
| 5 | Claude Code `/voice` behaviour quotes (server-side audio, no token cost, 15 s / 2 min stop, three-word rule, recognition hints) | Confirmed | voice-dictation doc read directly [15] |
| 6 | Voice dictation shipped "Mar 2026 (v2.1.69–2.1.71)" | Corrected | v2.1.69 (Mar 4, 2026) entries are fixes and a 20-language expansion; the feature predates them and has no "added" entry [18][19] |
| 7 | Codex 0.153.0 adds the voice helper foundation; dated Sep 3, 2026 | Confirmed | release notes and tag date (2026-09-03T00:52Z) [20] |
| 8 | Claude Code timeline: remote-control v2.1.51 Feb; `/loop` v2.1.71 Mar; channels v2.1.80–81 "Mar–Apr"; push tool v2.1.110 Apr; agent view and `/goal` v2.1.139 May; presence file v2.1.181 Jun; check-in cap v2.1.246 Aug 25 | Corrected (one item) | channels and relay are Mar 19–20; all other versions and dates match CHANGELOG and npm [18][19] |
| 9 | Routines launched Apr 14, 2026 | Confirmed | "What's new" week 16 (Apr 13–17, 2026) lists Routines [32] |
| 10 | Routines, `/goal`, desktop tasks, channels, Remote Control, Dispatch and agent-view quotes and numbers | Confirmed | all seven docs re-read; every quoted sentence found verbatim [9]–[17] |
| 11 | Star counts: OpenClaw 389.2k, Pipecat 15.4k, LiveKit 14.1k, Omi 13.4k | Confirmed | GitHub API Sep 8, 2026 [53] |
| 12 | Humane: HP paid $116M; servers off Feb 28, 2025 | Confirmed | Axios, How-To Geek, gHacks, GSMArena excerpts [52] |
| 13 | Humane: "~10,000 sold vs 100,000 target"; $699 plus subscription | Unverified | no excerpt gave unit numbers or price; search budget exhausted |
| 14 | Rabbit r1's outcome was the "$199 AI toy" verdict | Corrected | company alive in 2026: new funding, OpenClaw and Hermes on r1, "proactive rabbit", new device announced (rabbit.tech, Layer3Labs, Yahoo excerpts) [40] |
| 15 | Meta Ray-Ban Display Sep 2025, ~$799 | Confirmed | announced Sep 17, on sale Sep 30, 2025, $799 with Neural Band (9to5Google, Road to VR, Tom's Guide) [41] |
| 16 | Camera glasses from ~$299; EssilorLuxottica "millions of units" | Unverified | no excerpt obtained |
| 17 | Limitless acquired by Meta, Dec 2025 | Confirmed | announced Dec 5, 2025 (CNBC, AI Business, MLQ) [42] |
| 18 | Limitless pendant ~$99 plus subscription | Unverified | price not in any excerpt |
| 19 | Bee ~$50 wristband, acquired by Amazon Jul 2025 | Confirmed | $49.99 plus $19/month; deal announced Jul 22, 2025 (TechCrunch, Entrepreneur, Retail Dive) [43] |
| 20 | Friend ~$129 pendant, shipped 2025, panned | Confirmed and extended | $99 pre-order, $129 at shipping, $249 relaunch Jul 2026; WIRED pan (Gadgeteer, TNW, TechCrunch, TechBuzz) [44] |
| 21 | Alexa+ free with Prime | Confirmed | $19.99/month without Prime; all US Prime members Feb 4, 2026; UK, Spain, Germany, France, Australia in 2026 (MacRumors, 9to5Toys, WinBuzzer, About Amazon) [45] |
| 22 | Alexa+ announced Feb 2025; user numbers | Unverified | no excerpt carried the announcement date or a user count |
| 23 | Siri revamp slipped from 2025; Apple–Google Gemini deal reported Jan 2026 | Confirmed | TechCrunch Jan 12, 2026; Google confirmation Apr 22, 2026 (MacRumors, AppleInsider) [46] |
| 24 | ChatGPT tasks beta Jan 2025 | Unverified | not in any excerpt |
| 25 | ChatGPT Pulse, Sep 2025, as a current proactive product | Corrected | launched Sep 25, 2025 (Pro, mobile); retired Jul 2026 after scheduled tasks launched Jun 17, 2026 (9to5Mac, MSN, TechJack, Yellow, Prowlo) [47] |
| 26 | Cowork scheduled tasks "run with no device online" since July 2026 | Confirmed (feature); month per sibling doc | claude.com blog, help-centre article and Android Authority excerpts [30][51] |
| 27 | Poke acquired by Cognition Jul 23, 2026 (added from the program's verified doc) | Confirmed | TechCrunch Jul 24, 2026, Dealroom, KuCoin, Crypto Briefing [49] |
| 28 | Stivers et al. 2009: turns change hands in about a fifth of a second | Unverified | cited from memory; no search left to check the figure |

Totals: 20 confirmed (two of them extended with new facts), 4 corrected, 6 unverified. Rows 21 and 22 split one draft sentence into its confirmed and unverified halves.

### Refresh notes

Added, with sources:

- OpenAI Realtime in 2026: GPT-Realtime-2 with "GPT-5-class reasoning", Realtime-Translate and Realtime-Whisper (May 7, 2026); `gpt-realtime-2.1` and `-2.1-mini` (Jul 2026) with a claimed 25%+ p95 latency cut; `reasoning.effort` on Realtime models and the SDK's warning that it raises latency (sections TL;DR, 2) [6][7][39].
- The realtime-agents README's own latency figures ("~2s" of dead air in the chat-supervisor demo; "1.5s or longer" for stitched pipelines) (section 2) [4].
- Gemini Live: new voices (May 27, 2026) and voice-driven task features including Daily Brief (Aug 2026) (sections 2, 7) [48].
- Alexa+: US-wide for Prime on Feb 4, 2026, $19.99/month otherwise, and the 2026 international rollout with prices (section 5) [45].
- Siri: the Gemini-based rebuild, Google's Apr 22, 2026 confirmation, the reported ~$1B/year, the phased iOS 26.4 / iOS 27 plan (section 5) [46].
- Wearables: Bee to Amazon (Jul 22, 2025), Limitless to Meta (Dec 5, 2025) with sales halted, Friend's $249 relaunch (Jul 2026), Ray-Ban Display dates and specs (section 5) [41]–[44].
- Rabbit's 2026 pivot to hosting OpenClaw and Hermes, with the consequence for the "runtime limits killed the surface" lesson (sections TL;DR, 5, 9, thesis) [40].
- Background agents: Codex automations (same-thread and self-scheduling since Apr 16, 2026, with 90+ plugins), ChatGPT scheduled tasks (Jun 17, 2026; Free tier Aug 25, 2026) and the retirement of Pulse (sections TL;DR, 6, 7, 9, thesis) [47][50].
- Poke's acquisition by Cognition (Jul 23, 2026), its scale at the sale, and its GitHub activity through Aug 2026 (sections 1, 6, 9, thesis) [49].
- Codex `rust-v0.153.0` date resolved to Sep 3, 2026; its `docs/` has no voice or automations page (section 2, open questions) [20].
- Claude Code CHANGELOG cross-check: the voice feature's first entries are fixes in v2.1.69; channels and relay dated Mar 19–20 (sections 2, 6) [18][19].

Verdict changes. The TL;DR no longer says hardware "failed twice": one company died, one pivoted into a shell for open-source harnesses, and the pendants were absorbed by Amazon and Meta. The interruption-policy bullet and section 7 now cite the Pulse retirement as the first natural experiment on unrequested proactivity, and the non-technical verdict adds that unrequested proactivity is the pattern the record rejects. The thesis section's "two dead hardware companies" became "one dead, one hosting other people's agents"; the nuance paragraph gains the Pulse evidence.

Sources that could not be opened (egress blocked): openai.com and community.openai.com, platform.openai.com, blog.google, apple.com, amazon.com and aboutamazon.com, meta.com, rabbit.tech, limitless.ai, bee.computer, friend.com, poke.com, claude.com and support.claude.com, techcrunch.com, cnbc.com, macrumors.com, 9to5mac.com, 9to5google.com, androidauthority.com, marktechpost.com, layer3labs.io, the-gadgeteer.com, hedy.ai, yellow.com, tudordaniel.ro, wikipedia.org, and the ACM and PNAS papers. The GitHub REST API (api.github.com) was also blocked, so repository metadata came through the GitHub MCP connector and tag dates from a sparse clone.

Remaining doubts.

- Humane's unit sales and price, Limitless's pendant price, Ray-Ban Meta prices and EssilorLuxottica unit numbers, the Jan 2025 ChatGPT tasks date and the Alexa+ announcement date all stay "(unverified)".
- Which Siri features have shipped by Sep 8, 2026 (iOS 26.4 versus iOS 27) is not established; the phased plan came from lower-quality outlets.
- The Free-tier date for ChatGPT scheduled tasks (Aug 25, 2026) rests on a single excerpt; Codex's Apr 16, 2026 update is one excerpt plus the timestamp encoded in OpenAI's own X post ID.
- Rabbit's funding amount and second device rest on the vendor's own blog excerpts plus one review.
- Poke's proactive texting is still described from the program's sibling document; the acquisition coverage describes Poke as an SMS/iMessage assistant for tasks and schedules without confirming unprompted messages.
- No independent end-to-end latency benchmark for speech-to-speech agents was obtained; the only figures are OpenAI's own demo notes.
- The Anthropic help-centre article dating Cowork's web and mobile rollout to July 2026 could not be opened; the month is carried from the sibling doc.
