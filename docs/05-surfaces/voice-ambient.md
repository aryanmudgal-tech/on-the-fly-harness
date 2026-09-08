# Voice and ambient surfaces: talking to agents, wearing them, and letting them run in the background

*Research program: agent harnesses. Written 2026-09-08. Status: draft, pending verification.*

Sourcing note. The web-search budget was exhausted before this task started, and the session's egress proxy allowed only code.claude.com, github.com, raw.githubusercontent.com and registry.npmjs.org. It blocked openai.com (platform, help, developers), ai.google.dev, support.google.com, aboutamazon.com, developer.amazon.com, apple.com, developer.apple.com, meta.com, ai.meta.com, limitless.ai, bee.computer, rabbit.tech, poke.com, interaction.co, support.claude.com, Wikipedia, ACM, PNAS and the press. So: Anthropic's Claude Code docs and changelog, OpenAI's and Google's SDK repositories, and open-source projects are **primary** (read directly). Consumer products (Alexa+, Siri, Gemini Live app, Meta glasses, Limitless, Bee, Friend, ChatGPT tasks and Pulse, Poke) rest on the author's prior knowledge and are marked **unverified**, with the sibling documents in this corpus cited where they already sourced a fact.

## What this document answers

- What "voice agent" and "ambient agent" actually mean in harness terms, and why they are three different products (dictation, conversation, always-on capture), not one.
- What shipped by September 2026: the OpenAI Realtime API line, Gemini Live, voice in Claude Code and Codex, the wearable record (Humane, Rabbit, Meta, Limitless, Bee, Friend, Omi), and the background layer (Claude Code routines, desktop tasks, `/loop`, `/goal`, channels, Dispatch; Codex automations; ChatGPT tasks and Pulse; Poke; OpenClaw).
- Which tasks suit voice, where latency and confirmation break, how vendors are handling the cost of interruption, and what the privacy exposure is.
- A verdict for developers and for non-technical users, and what the evidence does to the program's thesis.

## TL;DR

- **Voice is now an API commodity but still not an operating surface for agents.** OpenAI's Realtime line has moved from `gpt-realtime` (SDK support Sep 3, 2025) through `gpt-realtime-1.5` (Feb 24, 2026), "realtime 2" (May 7, 2026) to `gpt-realtime-2.1` and `-2.1-mini` in the current model enum (primary, SDK source) [5][6]. Google's cookbook targets `gemini-3.1-flash-live-preview` (primary) [8]. Yet the two most capable developer harnesses treat voice as **input only**: Claude Code's `/voice` "streams your recorded audio to Anthropic's servers for transcription" and inserts text into the prompt (primary) [15]; Codex only began wiring a "voice helper lifecycle foundation" and "realtime conversation history" in release 0.153.0 (Sep 2026, primary) [20].
- **The hard voice problem is not latency but confirmation.** OpenAI's own voice-agent SDK states: "While the voice agent is waiting for approval for the tool call, the agent will not be able to process new requests from the user" (primary) [7]. The reference architecture OpenAI publishes for real work is a small realtime model that talks while a text model thinks, at the cost of answers that "start with 'Let me think'" (primary) [4].
- **Turn-taking moved into the model.** Semantic VAD with an `eagerness` knob (max waits of 8 s, 4 s, 2 s) and `interrupt_response` are now API parameters (primary) [6]. That is harness logic being absorbed by the model vendor, which is the half of the thesis that says better models need *less* harness.
- **Standalone AI hardware failed twice; the survivors are recorders and glasses.** Humane's Ai Pin shut down Feb 28, 2025 after HP bought the assets for $116M (sibling doc, TechCrunch) [24]; Rabbit r1 shipped Apr 2024 to a review titled "a $199 AI toy that fails at almost everything" (sibling doc, Engadget) [25]. What persists is capture-and-memory hardware attached to a phone (Limitless, Bee, Friend, the open-source Omi at 13.4k stars) and Meta's glasses (unverified details) [21][22].
- **Background agents are the real surface shift, and the labs built it themselves.** Claude Code now has three scheduling tiers (cloud routines, desktop tasks, in-session `/loop`), event channels, `/goal`, agent view, push notifications with a "Claude decides when to push" policy, and Dispatch ("You message Dispatch a task, and it decides how to handle it") (primary) [9][10][11][12][13][14][16][17]. Cowork's scheduled tasks run "with no device online" since July 2026 (sibling doc, help-center excerpt) [30].
- **Interruption policy is a real harness component and it is thin everywhere.** Anthropic's controls are a two-toggle push setting, a presence file that suppresses pushes while you are at the machine (v2.1.181, Jun 2026), a cap of three idle check-ins per `/goal` (v2.1.246, Aug 25, 2026) and folding of empty `/loop` wake-ups (v2.1.243) (primary, changelog) [18][19]. No vendor publishes an interruption-cost model; the HCI literature that would inform one (Horvitz 1999; Mark et al. 2008) could not be fetched [35][36].
- **Privacy is the least solved part.** Dictation audio leaves the machine; routines "appear as you" and connectors can write "without asking for permission during a run"; the iMessage channel reads `~/Library/Messages/chat.db` (primary) [9][12][15]. Always-on wearables add bystander consent, which no product in this record solves by design (unverified).
- **For the thesis:** voice contradicts "reimagine the default surface" as a *primary* surface for agents; background and proactive execution supports it, but the labs are ahead there too. The open ground is the policy layer: when to interrupt, how to confirm without a screen, and how to scope what an unattended run may do.

## 1. Vocabulary: three products that share one word

Analogy. An intercom lets you bark an order to the kitchen. A colleague at the next desk holds a conversation and interrupts you when it matters. A dictaphone in your pocket records the whole day so someone can summarise it later. All three are "voice", and they need different harnesses.

| Mode | What the harness must do differently | Examples in this record |
|---|---|---|
| **Dictation** (voice as input) | Speech-to-text into an existing text loop; nothing else changes | Claude Code `/voice` [15]; Codex "native voice" work [20]; Antigravity desktop voice input (sibling doc) [31] |
| **Conversation** (speech-to-speech loop) | Loop runs under a sub-second clock; turn detection; barge-in; tool calls without dead air; confirmation without a screen | OpenAI Realtime API [5][6]; Gemini Live API [8]; Alexa+, Siri, ChatGPT voice, Gemini Live app (unverified) |
| **Ambient / always-on** (continuous capture, proactive output) | Runtime lives on a battery; context is hours of audio; privacy of bystanders; the agent decides *when* to speak | Limitless, Bee, Friend, Omi, Meta glasses [21][22]; Poke, Pulse (unverified); Dispatch, routines, channels [9][12][17] |

In the program's seven-part harness: dictation touches only **Surface**. Conversation rewrites the **Loop** (turn-taking replaces "send and wait"), constrains **Permissions** (no diff to inspect) and **Context** (audio is expensive per token). Ambient mode moves the **Runtime** onto a device, and makes **Orchestration** (scheduling, wake-ups) and the interruption policy the centre of the product.

## 2. Conversational voice: the loop under a 500 ms clock

Human conversation turns over in roughly a fifth of a second; the classic cross-language measurement (Stivers et al., PNAS 2009) could not be fetched and is cited from memory [37]. A voice agent that pauses for two seconds feels broken, and one that jumps in early talks over the user. Everything below is the industry solving those two failure modes.

**OpenAI Realtime.** From the SDK's own changelog: `gpt-realtime` models added Sep 3, 2025 and the "RealtimeGA API shape" shipped Sep 8, 2025; "realtime calls" (SIP telephony) Oct 2, 2025; `gpt-realtime-1.5` and `gpt-audio-1.5` Feb 24, 2026; "realtime 2" plus "realtime translate" May 7, 2026; "backend-mediated Realtime WebRTC calls" Aug 26, 2026 [5]. The current model union type lists `gpt-realtime`, `gpt-realtime-2`, `gpt-realtime-2.1`, `gpt-realtime-2.1-mini`, `gpt-realtime-mini-2025-12-15` and the older `gpt-4o-realtime-preview` dates [6]. The turn-taking controls are in the same file, and they are the harness moving into the API [6]:

- `server_vad` (energy-based) or `semantic_vad`: "Server-side semantic turn detection which uses a model to determine when the user has finished speaking."
- `eagerness`: "`low` will wait longer for the user to continue speaking, `high` will respond more quickly ... `low`, `medium`, and `high` have max timeouts of 8s, 4s, and 2s respectively."
- `interrupt_response`: "Whether or not to automatically interrupt (cancel) any ongoing response ... when a VAD start event occurs."
- `idle_timeout_ms`: fires after "the last model response's audio has finished playing", so the agent can prompt a silent user; "currently only supported for `server_vad` mode."

**Gemini Live.** Google's quickstart uses `gemini-3.1-flash-live-preview`, enables `context_window_compression` with `trigger_tokens=25600` and a sliding window of 12,800 target tokens ("so that model does not hallucinate in long conversations"), supports session resumption via a handle, exposes an `interrupted` flag to flush playback, and warns: "to prevent the model from interrupting itself it is important that you use headphones" [8]. That last line is the echo-cancellation problem in one sentence.

**The reference architecture for real work is two models.** OpenAI's `openai-realtime-agents` repo describes a *chat-supervisor* pattern: a realtime model "converse[s] with the user and handle[s] basic tasks" while a text model (`gpt-4.1` in the example) does "complex tool calls"; benefits are "high intelligence, excellent tool calling and instruction following", "lower cost" and that "the model responds to the user right away"; the cost is that "more assistant responses will start with 'Let me think'" [4].

```
 mic ──► realtime model (talks, ~fast) ──► speaker
              │  "let me check that"
              ▼ tool call: getNextResponseFromSupervisor
        text model (thinks, calls real tools, slower)
              │ result
              ▼
        realtime model reads the answer aloud
```

**Frameworks.** Pipecat (15.4k stars) offers both pipelines, "STT → LLM → TTS" and direct speech-to-speech, across "60+ services" [2]. LiveKit Agents (14.1k stars) ships "Semantic turn detection: Uses a transformer model to detect when a user is done with their turn, helps to reduce interruptions" and telephony [3]. Neither README publishes a latency number; every latency figure in this market is a vendor claim, and this session could not fetch an independent benchmark.

**The lab CLIs.** Claude Code's `/voice` is push-to-talk dictation: audio "is not processed locally", it needs a Claude.ai login, transcription "does not consume Claude messages or tokens", recording "stops automatically after 15 seconds of silence or two minutes total", and tap mode auto-submits only if "the transcript is at least three words long" so "an accidental tap does not send a stray word" [15]. Dictation also works into background sessions via agent view [15]. The changelog dates the feature to spring 2026 (rebindable `voice:pushToTalk` in v2.1.71; 20 languages in v2.1.69; npm publishes place those in Mar 2026) [18][19]. Codex's release 0.153.0 adds "the voice helper lifecycle foundation", "installed voice host lifecycle support", "Record realtime conversation history in Core" and "per-call sideband endpoints for existing realtime calls", meaning a native Realtime-API voice mode is being built into the CLI (the releases index dates it Sep 3, 2026; the tag page rendered the year as 2025, treated as a rendering error) [20].

## 3. Which tasks suit voice

| Task | Fit | Why (evidence) |
|---|---|---|
| Short commands, lookups, dictating a prompt | Good | Dictation adds no loop complexity; Claude Code even adds "your current project name and git branch name ... as recognition hints" [15] |
| Hands-busy or eyes-busy situations (driving, cooking, walking, repair) | Good | The only case where voice beats a screen outright; this is the glasses and earbuds market (unverified sizing) |
| Coaching, tutoring, language practice, customer support | Good to fair | Conversation *is* the task; telephony via SIP [5]; the chat-supervisor pattern was written for support flows [4] |
| Multi-step agent work (edit files, run tests, book travel with three options) | Poor | Results are visual (diffs, tables, prices); the voice channel blocks during approvals [7]; "Let me think" fillers [4] |
| Consequential, irreversible actions | Poor without a screen | No diff to inspect; VAD false positives; see section 4 |
| Continuous capture for memory | Good, but it is not "agent" work | Wearables transcribe and summarise; action is deferred to the phone app [21][22] |

## 4. Latency and confirmation

**Latency is a budget, not a number.** The pieces are: end-of-turn detection (the semantic VAD waits up to 2–8 s by design [6]), model time to first audio, any tool call the answer depends on, text-to-speech, and network. A speech-to-speech model removes the STT and TTS hops, which is why OpenAI's SDK says "Speech-to-speech models process user audio directly" [7]. But it does not remove the tool call. A flight search or a database query still takes seconds, so the harness fills the gap with speech ("Let me check that") or delegates to a slower model in the background [4]. Google's context compression exists because long sessions otherwise overflow [8].

**Three failure modes, and the knob for each.**

| Failure | Symptom | Mitigation shipped |
|---|---|---|
| Talk-over | Agent keeps speaking while the user speaks | `interrupt_response`; the JS SDK "truncates the assistant audio to what the user actually heard, and emits an `audio_interrupted` event" [6][7] |
| Premature end-of-turn | Agent answers a half-finished sentence | `semantic_vad` with `eagerness: low` (8 s) [6]; LiveKit's transformer turn detector [3] |
| Dead air during tools | Silence while a tool runs | Chat-supervisor fillers [4]; `idle_timeout_ms` to re-prompt a silent user [6] |

**Confirmation is the unsolved part.** Every permission design in this corpus assumes a screen: a diff, a tool-call preview, an Allow/Deny button. Voice has none of these. OpenAI's SDK makes approvals explicit (`needsApproval: true`, then `session.approve()`), but states that while waiting "the agent will not be able to process new requests from the user" [7]. Guardrails run "every 100 characters" of transcript, i.e. after speech has started, so an unsafe answer can be partly spoken before it is stopped [7]. Handoffs cannot change the model mid-session, and voice changes "only work before the session has produced audio output" [7]. In Anthropic's stack the confirmation moved to the phone instead: Remote Control forwards permission prompts, and channels that declare the permission-relay capability let you approve from Telegram or iMessage, with the warning that "Anyone who can reply through the channel can approve or deny tool use in your session" [12][13]. The practical pattern that emerges is *say it, then tap it*: voice for intent, a glanceable screen for consent.

## 5. Ambient hardware: the record

Analogy. A smartwatch succeeded as a phone accessory and failed as a phone replacement. AI hardware is repeating that curve.

| Device | Dates and price | Outcome | Source |
|---|---|---|---|
| Humane Ai Pin | Shipped Apr 2024; ~$699 plus subscription (unverified) | HP bought assets for $116M; servers off Feb 28, 2025; "~10,000 sold vs 100,000 target" | sibling doc citing TechCrunch, Feb 2025 [24] |
| Rabbit r1 | $199; CES Jan 2024; shipped Apr 2024 | "a $199 AI toy that fails at almost everything"; later software updates (unverified) | sibling doc citing Engadget, May 2024 [25] |
| Meta Ray-Ban / Ray-Ban Display | Display model with a wrist band announced Sep 2025, ~$799; camera glasses from $299–379 (unverified) | The one hardware line with mass sales; EssilorLuxottica reported millions of units (unverified) | unverified |
| Limitless pendant | ~$99 pendant plus subscription (unverified); developer API with lifelog "transcripts" (primary) | Reported acquired by Meta, Dec 2025 (unverified) | [21] |
| Bee | ~$50 wristband plus subscription (unverified) | Reported acquired by Amazon, Jul 2025 (unverified) | unverified |
| Friend | ~$129 pendant; shipped 2025; subway ad campaign (unverified) | Widely panned reviews (unverified) | unverified |
| Omi (BasedHardware, ex-"Friend" repo name) | Open source, MIT; 13.4k stars | "captures your screen and conversations, transcribes in real-time, generates summaries and action items"; "Trusted by 300,000+ professionals" (vendor claim) | [22] |

Two lessons. First, the devices that died tried to be the whole harness (loop, tools, runtime, surface) on a weak model with 2024 latency; the sibling history doc's verdict is "runtime limits killed the surface" [24]. Second, the survivors are **context** devices: they feed memory into a phone app and leave action to the phone. Ambient capture is a harness input, not a harness.

**The incumbents' voice assistants** are the largest voice distribution channels and all rest on unverified facts this session could not fetch: Alexa+ (announced Feb 2025, subscription free with Prime, agentic bookings; Amazon has claimed millions of users), the Siri revamp (Apple's personalised Siri slipped from 2025; an Apple–Google deal to base Apple's foundation models on Gemini was reported in Jan 2026), Gemini Live in the Gemini app (camera and screen sharing, app actions), and ChatGPT voice. Treat all of it as unverified. The structural point stands regardless: whoever owns the OS or the speaker owns the wake word, and a startup does not.

## 6. Background and scheduled agents: the loop leaves the foreground

Analogy. A cron job is an alarm clock. A background agent is a night-shift employee: it needs a task sheet, its own keys, and a rule for when to phone you.

**Claude Code's three tiers** (all primary, from the docs' own comparison table) [10][11]:

| | Cloud routines | Desktop scheduled tasks | `/loop` (in-session) |
|---|---|---|---|
| Runs on | Anthropic-managed cloud (or self-hosted env) | Your machine | Your machine |
| Machine must be on | No | Yes ("If your computer sleeps through a scheduled time, the run is skipped") | Yes, session open |
| Minimum interval | 1 hour | 1 minute | 1 minute |
| Permissions | "no permission-mode picker and no approval prompts during a run" | Per-task mode; Manual mode "stalls until you approve" | Inherits session |
| Persistence | Yes | Yes; one catch-up run for missed fires | 7-day expiry; restored on `--resume` |
| Triggers | Schedule, HTTP `/fire` with bearer token, GitHub PR/release events | Schedule, or the task reschedules itself via `update_scheduled_task` | Cron or Claude-chosen interval (1 min to 1 h) |

Details that matter for a harness designer: routines "belong to your individual claude.ai account" and "Anything a routine does through your connected GitHub identity or connectors appears as you" [9]. The API trigger's `text` arrives "wrapped in a `<routine-fire-payload>` block that labels it as untrusted data" so a leaked token cannot inject instructions [9]. Before v2.1.213 (Jul 17, 2026) the routine's own prompt was "framed as an untrusted background notification and could refuse to act on it", a concrete case of prompt-injection defences fighting legitimate automation [9][19]. And "A green status in the run list ... does not mean the task in your prompt succeeded" [9]. `/loop` adds jitter (up to 30 min), a self-paced mode where "Claude chooses one dynamically", and a `/usage` breakdown "so runaway or chatty `/loop` tasks are easy to spot" (v2.1.243) [11][18]. `/goal` keeps a session working until a separate small model judges the condition met, with check-ins after 30 minutes of background work that back off "up to four times the first interval" [14].

**Timeline** (versions from the changelog, dates from npm publish times) [18][19]: `claude remote-control` v2.1.51 (Feb 2026); cron tools and `/loop` v2.1.71 (Mar 2026); `--channels` v2.1.80 and permission relay v2.1.81 (Mar–Apr 2026); routines Apr 14, 2026 (sibling doc) [32]; push notification tool v2.1.110 (Apr 2026); agent view and `/goal` v2.1.139 (May 2026); presence file v2.1.181 (Jun 2026); `/goal` idle-check-in cap v2.1.246 (Aug 25, 2026). Nine months from "CLI" to "runtime with a scheduler, an event bus and a pager".

**Non-developer packaging.** Dispatch "is a persistent conversation with Claude that lives in the Cowork tab. You message Dispatch a task, and it decides how to handle it", spawning a Code session when "the task is development work", with "a push notification on your phone when it finishes or needs your approval"; computer-use approvals last "30 minutes in Dispatch-spawned sessions" [17]. Cowork's July 2026 web and mobile rollout means "scheduled tasks run with no device online" (help-center excerpt via sibling doc) [30].

**OpenAI.** Codex cloud runs tasks at chatgpt.com/codex (primary README pointer) [20]; the app's "thread automations that resume across days or weeks" are secondary (sibling doc) [31]; the automations docs live on the blocked developers.openai.com. ChatGPT *tasks* (scheduled prompts with push or email, beta Jan 2025, limited to about ten active tasks) and *Pulse* (proactive morning research cards, Sep 2025, Pro first) are unverified.

**Independent always-on harnesses.** OpenClaw, at 389.2k GitHub stars, "runs on your own computer", reaches "Discord, iMessage, Slack, Teams, Telegram, WhatsApp, and 20+ more", adds "voice, Canvas, camera, screen, and device-local actions" through companion apps, and warns: "Treat inbound messages as untrusted input" [23]. Hermes and NanoClaw run cron with memory in sandboxes (sibling doc) [33]. Poke, the proactive iMessage/WhatsApp assistant from The Interaction Company, is reachable only through its GitHub org: integrations connect "your MCP server to Poke at (poke.com/settings/connections)", with example repos dated Sep–Oct 2025 [1]. Its proactive behaviour (texting you first about calendar or email events) is unverified.

## 7. Proactive agents and the cost of interruption

Analogy. A good assistant knocks once a day with a list, not twenty times with one item each. The value of a proactive agent is the *quality of its silence*.

The literature that would ground a policy is old and could not be fetched: Horvitz's mixed-initiative principles (weigh the expected utility of acting or interrupting against the user's attention; CHI 1999) and Mark, Gudith and Klocke's finding that interrupted work is finished faster but with more stress (CHI 2008) [35][36]. No vendor in this record cites either, and none publishes an interruption model.

What has shipped is a set of blunt rules, all primary [12][13][14][16][17][18]:

| Mechanism | Rule | Where |
|---|---|---|
| Push when Claude decides | "It typically sends one when a long-running task finishes or when it needs a decision from you ... Beyond the two on/off toggles below, there is no per-event configuration" | Remote Control [13] |
| Presence suppression | `CLAUDE_CLIENT_PRESENCE_FILE` suppresses mobile pushes "while you're at the machine" (v2.1.181) | changelog [18] |
| Desktop notice | OS notification "when a Code session finishes a task and you aren't currently viewing that session" | Desktop [17] |
| State, not stream | Agent view shows Working / Needs input / Idle / Completed / Failed and notifies "when a local background session starts needing your input, finishes, or fails" | agent view [16] |
| Bounded nagging | `/goal` idle check-ins: "at most three idle check-ins per goal between your prompts"; empty `/loop` wake-ups "fold into a single line" | `/goal`, changelog [14][18] |
| Relay consent | Channels forward permission prompts to chat; the allowlist "gates permission relay" | channels [12] |

```
              ┌──────────── silent (working) ────────────┐
   event ─────►  needs decision?  ──yes──► is user present?
              │        │ no                    │ yes: in-app prompt
              │        ▼                       │ no : push to phone, wait
              │  done or failed? ──yes──► one notification, then idle
              │        │ no
              └────────┘  (fold repeats; cap check-ins; expire loops)
```

The asymmetry is the point. A false interruption costs attention and trust; a missed one costs a stalled run ("the run stalls until you approve it" [10]) or an unattended write ("Claude can use every tool from an included connector, including writes, without asking for permission during a run" [9]). Today's answer is to pick a permission mode per task and hope. That is a policy hole, and it is the same hole for a developer's nightly routine and for a non-technical user's Dispatch task.

## 8. Privacy

- **Dictation leaves the device.** "Voice dictation streams your recorded audio to Anthropic's servers for transcription. Audio is not processed locally" [15]. Fine for a prompt; not fine for a room.
- **Always-on capture records bystanders.** Every pendant in section 5 records people who did not consent; the hardware vendors' consent features (mute gestures, local-only modes) are unverified, and two-party-consent jurisdictions make the default legally fragile (general statement, unverified).
- **Chat bridges read your messages.** The iMessage channel "reads your Messages database directly" at `~/Library/Messages/chat.db` and needs Full Disk Access [12]. OpenClaw keeps "State, memory, and credentials ... on your hardware" but has the same shape [23].
- **Unattended runs carry your identity.** Routines act "as you" with no approval prompts; the mitigation is scoping ("Remove any [connectors] the routine doesn't need") and the untrusted-payload wrapper [9]. Cowork and cloud sessions now "always ask you first" before reading an artifact that is not yours, "even in auto mode" (v2.1.257) [18].
- **Incumbent assistants.** Alexa+'s data handling and Amazon's 2025 removal of local-only voice processing on Echo devices are unverified here and should be checked before any claim is made.

## 9. Verdicts

**Developers.** Voice is a keyboard, not a harness. Dictation earns its place (hands off the keys, phone-side steering, agent-view replies) and Codex is about to add a native voice mode, but every capable agent still confirms on a screen [7][15][20]. The surface that actually changed the developer's day is the background tier: routines, desktop tasks, channels, `/goal` and agent view turned the CLI into a runtime you monitor from a phone. The terminal did not go away; it became the place you read the diff.

**Non-technical users.** The voice channels with reach are Alexa+, Siri, Gemini Live and Meta's glasses, all owned by incumbents (unverified specifics). They suit short, reversible tasks. The wearables that survived are memory devices. The workable non-technical pattern in the record is *proactive over chat plus confirmation on a screen*: Dispatch, Cowork scheduled tasks, Poke, OpenClaw's channels. Voice can sit on top of that as input; it cannot be the whole loop until confirmation without a screen is solved.

## What this means for the thesis

**Supports.**
- "Neither a CLI nor a desktop app is the default way people operate agents" is exactly what the background layer shows: the same engine now runs from cloud schedules, GitHub events, HTTP triggers, Telegram, iMessage and a phone's push notification [9][12][13]. The loop left the foreground.
- "The harness becomes the bottleneck" holds for **permissions and interruption policy**. The model can already run unattended; what limits it is the lack of a screen-less confirmation model and of any principled interruption policy (sections 4 and 7).
- Non-technical demand for *proactive* agents is real enough that Anthropic built Dispatch and scheduled Cowork, OpenAI built tasks and Pulse, and Poke and OpenClaw found audiences (some unverified) [17][23][30].

**Contradicts.**
- Voice as a reimagined *default* surface has the worst track record in this corpus: two dead hardware companies, incumbents owning the wake word, and the most capable harnesses shipping voice as dictation only [15][24][25].
- Half of the "harness gets thicker" claim is wrong for voice: turn detection, interruption and echo handling moved *into* the model vendor's API (`semantic_vad`, `eagerness`, `interrupt_response`) [6]. The harness got thinner there.
- The labs are building the background and notification layer themselves and iterating weekly (nine months of changelog above) [18][19]. A startup cannot out-ship Anthropic on routines.

**Nuance.** The layer that is thin *and* not being built by the labs is policy: an interruption model that weighs presence, urgency and reversibility; a confirmation protocol for eyes-free consent; per-run blast-radius scoping for non-technical users. Those are harness parts 4 and 7 in the program's definitions, and they are the same for a developer's nightly job and a parent's grocery agent. That is where the evidence says a new default could be built.

## Open questions and unverified claims

1. Alexa+, the Siri revamp, Gemini Live app features, ChatGPT voice, ChatGPT tasks and Pulse: every date, price and user number here is from memory; none was fetched. Re-verify before use.
2. Wearable business facts: Meta's acquisition of Limitless (Dec 2025?), Amazon's of Bee (Jul 2025?), Friend's price and reviews, Meta glasses prices and unit sales. All unverified.
3. Codex release 0.153.0's date: the releases index says Sep 3, 2026; the tag page rendered "2025". The changelog for Codex app automations is on the blocked developers.openai.com.
4. The `@openai/agents-realtime` npm metadata returned by the fetch tool was internally inconsistent (a 0.0.1 publish date of Jan 2025), so no SDK launch date is claimed.
5. No independent end-to-end latency benchmark for speech-to-speech agents was obtained; all latency statements are structural, not measured.
6. The HCI interruption papers and the PNAS turn-taking figure are cited from memory.
7. Poke's proactive behaviour, funding and launch date; OpenClaw's heartbeat and cron features (the README fetch did not surface them; the sibling doc did).
8. Whether Cowork's scheduled tasks share the routines infrastructure is unconfirmed; the desktop docs say only that local Desktop tasks and cloud routines are created from the same Routines page [10].

## Sources

1. The Interaction Company of California, GitHub organisation (mcp-server-template, poke-mcp-examples), https://github.com/InteractionCo, fetched Sep 2026 (primary; repos dated Sep–Oct 2025)
2. Pipecat README, https://github.com/pipecat-ai/pipecat, Sep 2026 (primary)
3. LiveKit Agents README, https://github.com/livekit/agents, Sep 2026 (primary)
4. OpenAI, openai-realtime-agents README (chat-supervisor and sequential handoff patterns), https://github.com/openai/openai-realtime-agents, Sep 2026 (primary)
5. OpenAI Node SDK CHANGELOG (realtime entries, Feb 2025 to Sep 8, 2026), https://github.com/openai/openai-node/blob/master/CHANGELOG.md (primary)
6. OpenAI Node SDK, `src/resources/realtime/realtime.ts` (model enum, `semantic_vad`, `eagerness`, `interrupt_response`, `idle_timeout_ms`), https://github.com/openai/openai-node/blob/master/src/resources/realtime/realtime.ts, Sep 2026 (primary)
7. OpenAI Agents SDK (JS), "Voice agents" and "Build" guides, https://github.com/openai/openai-agents-js/blob/main/docs/src/content/docs/guides/voice-agents.mdx and .../voice-agents/build.mdx, Sep 2026 (primary)
8. Google Gemini cookbook, `quickstarts/Get_started_LiveAPI.py` (model `gemini-3.1-flash-live-preview`, context compression, resumption), https://github.com/google-gemini/cookbook, Sep 2026 (primary)
9. Claude Code docs, "Automate work with routines", https://code.claude.com/docs/en/routines, Sep 2026 (primary)
10. Claude Code docs, "Schedule recurring tasks in Claude Code Desktop", https://code.claude.com/docs/en/desktop-scheduled-tasks, Sep 2026 (primary)
11. Claude Code docs, "Run prompts on a schedule" (`/loop`, cron tools), https://code.claude.com/docs/en/scheduled-tasks, Sep 2026 (primary)
12. Claude Code docs, "Push events into a running session with channels", https://code.claude.com/docs/en/channels, Sep 2026 (primary)
13. Claude Code docs, "Continue local sessions from any device with Remote Control" (mobile push notifications), https://code.claude.com/docs/en/remote-control, Sep 2026 (primary)
14. Claude Code docs, "Keep Claude working toward a goal", https://code.claude.com/docs/en/goal, Sep 2026 (primary)
15. Claude Code docs, "Voice dictation", https://code.claude.com/docs/en/voice-dictation, Sep 2026 (primary)
16. Claude Code docs, "Agent view", https://code.claude.com/docs/en/agent-view, Sep 2026 (primary)
17. Claude Code docs, "Desktop application" (Sessions from Dispatch; notifications), https://code.claude.com/docs/en/desktop, Sep 2026 (primary)
18. Claude Code CHANGELOG (v2.1.51 to v2.1.263), https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md, Sep 2026 (primary)
19. npm registry, `@anthropic-ai/claude-code` publish times (2.1.0 Jan 7, 2026; 2.1.75 Mar 13; 2.1.100 Apr 10; 2.1.150 May 23; 2.1.181 ~Jun 2026; 2.1.213 Jul 17; 2.1.246 Aug 25; 2.1.263 Sep 6, 2026), https://registry.npmjs.org/@anthropic-ai/claude-code (primary)
20. OpenAI Codex repository: README pointer to Codex cloud, docs index, and release rust-v0.153.0 notes, https://github.com/openai/codex and https://github.com/openai/codex/releases/tag/rust-v0.153.0, Sep 2026 (primary)
21. Limitless, limitless-api-examples README and organisation page, https://github.com/limitless-ai-inc/limitless-api-examples, Sep 2026 (primary)
22. BasedHardware, Omi README, https://github.com/BasedHardware/omi, Sep 2026 (primary; "300,000+" is a vendor claim)
23. OpenClaw README, https://github.com/openclaw/openclaw, Sep 2026 (primary)
24. Sibling doc `docs/01-primer/history.md` citing TechCrunch, "Humane's AI Pin is dead as HP buys startup's assets for $116M", https://techcrunch.com/2025/02/18/humanes-ai-pin-is-dead-as-hp-buys-startups-assets-for-116m, Feb 2025 (secondary)
25. Sibling doc `docs/01-primer/history.md` citing Engadget, "Rabbit R1 review: a $199 AI toy that fails at almost everything", https://www.engadget.com/rabbit-r1-review-a-199-ai-toy-that-fails-at-almost-everything-161043050.html, May 2024 (secondary)
26. Sibling doc `docs/01-primer/anatomy.md` (scheduling tiers, "runs when your laptop is closed"), Sep 2026 (corpus)
27. Sibling doc `docs/04-research/industry-engineering.md` ("Each surface connects to the same underlying Claude Code engine"), Sep 2026 (corpus)
28. Sibling doc `docs/07-strategy/value-capture.md` (Cowork Jan 2026; Codex app Feb 2026; Copilot Cowork Jun 2026), Sep 2026 (corpus)
29. Sibling doc `docs/02-landscape/developer-harnesses-labs.md` (Cowork dates; Codex app and automations; Antigravity 2.0 voice input), Sep 2026 (corpus)
30. Anthropic help center, "Use Claude Cowork on web, desktop and mobile", https://support.claude.com/en/articles/15520349-use-claude-cowork-on-web-desktop-and-mobile, Jul 2026 (via search excerpt in [29]; not fetched)
31. TechCrunch on Antigravity 2.0 at I/O, May 19, 2026 (via search excerpt in [29]; not fetched)
32. Claude Code "What's new", week 16 (routines launched Apr 14, 2026), https://code.claude.com/docs/en/whats-new/2026-w16 (cited in `docs/01-primer/history.md`; not re-fetched)
33. Sibling doc `docs/02-landscape/developer-harnesses-independent.md` (OpenClaw, Hermes, NanoClaw always-on and cron features), Sep 2026 (corpus)
34. Anthropic, Claude Desktop "Dispatch" help article, https://support.claude.com/en/articles/13947068 (linked from [17]; not fetched)
35. E. Horvitz, "Principles of mixed-initiative user interfaces", CHI 1999, doi:10.1145/302979.303030 (not fetched; cited from memory)
36. G. Mark, D. Gudith, U. Klocke, "The cost of interrupted work: more speed and stress", CHI 2008, doi:10.1145/1357054.1357072 (not fetched; cited from memory)
37. T. Stivers et al., "Universals and cultural variation in turn-taking in conversation", PNAS 2009, doi:10.1073/pnas.0903616106 (not fetched; cited from memory)
38. Google, live-api-web-console README (proactive-audio demo branches; "not an official Google product"), https://github.com/google-gemini/live-api-web-console, Sep 2026 (primary)
