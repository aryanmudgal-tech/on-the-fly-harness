# Non-technical agent surfaces from the big vendors: OpenAI, Anthropic, Google, Microsoft, Apple, Amazon, Meta

*Research program: agent harnesses. Written 2026-09-08. Status: draft, pending verification.*

## What this document answers

- What a person who does not write code can hand to an agent today through each big vendor, and in which surface.
- How each vendor packages the harness: where the loop runs, what tools it reaches, how approvals and admin control work.
- What evidence exists for adoption, and what has broken: incidents, retreats, shutdowns, backlash, with dates.
- What this supports or contradicts in the program thesis.

**Sourcing note.** This session's proxy blocked openai.com, anthropic.com, claude.com, blog.google, support.google.com, learn.microsoft.com, apple.com, aboutamazon.com, ai.meta.com, Wikipedia and all press sites, and the shared web-search budget was already exhausted. Reachable: Anthropic's Claude Code docs, the Microsoft 365 and Security blogs, Microsoft Learn content mirrored on GitHub, Apple and Android developer docs, the Anthropic Economic Index (MCP tool), and dated community archives (smol.ai AI News, BuilderPulse, a security-headline archive). Facts read from a vendor page are **(primary)**; facts from a dated digest or mirrored copy are **(secondary)**; anything unconfirmed is **unverified**. Secondary dates are digest dates, not necessarily event dates.

## TL;DR

- **Every vendor converged on one shape for non-developers: a chat surface in front of a cloud-hosted, long-running agent runtime, with the desktop app as a client.** Anthropic's Cowork reached web and mobile with cloud sessions on July 7, 2026 (secondary) [41]; OpenAI merged Codex into one ChatGPT desktop app with a "Work" mode and cloud scheduled tasks on July 9, 2026 (secondary) [36]; Microsoft's Copilot Cowork is cloud-hosted so "tasks keep running even when your laptop is off" (primary) [17]; Google's Gemini Spark "runs on dedicated Google Cloud virtual machines" (secondary) [41]; Microsoft Scout is "always-on" with its own directory identity (primary) [20].
- **The standalone AI browser failed in under a year.** ChatGPT Atlas launched October 21, 2025 and stops working August 9, 2026; its agent features moved into a Chrome extension and the ChatGPT desktop app's built-in browser (secondary) [36][37][38]. Browser agency survived as a feature of a chat client, not as a browser.
- **Approvals are the least standardized harness part.** Microsoft: plan, checkpoints, "approve changes before they are applied" [16]. Google: "human confirmation required before checkout" [44]. Anthropic: per-action browser prompts with a site-scoped "allow all", plus a classifier that now approves by default in developer sessions [1][9]. Apple: developers declare "when they're safe to run" [35]. No two vendors show the user the same picture of blast radius.
- **Incidents cluster where an agent reads untrusted content: email, invites, web pages, extensions.** 2026 so far: Gemini leaking calendar data via malicious invites (Jan), the Reprompt Copilot session hijack (Jan), a Chrome flaw letting extensions hijack Gemini's camera and files (Mar), Meta AI's support bot tricked into handing over Instagram accounts (Jun), SearchLeak one-click exfiltration from Microsoft 365 Copilot (Jun), a Claude for Chrome flaw letting rogue extensions trigger Gmail reads (Jul), a Claude Cowork VM-escape flaw (Jul) (secondary headlines) [43]. A 2026 paper found Atlas, Chrome with Gemini, Claude for Chrome and Comet all fed cross-origin iframe content to the model, "reducing the same-origin policy to the model's injection resistance" (secondary) [51].
- **Adoption evidence is real but almost entirely vendor-reported.** Microsoft: 30M+ paid Microsoft 365 Copilot seats; "more than half of the Fortune 500" used Copilot Cowork in its three-month preview (primary) [17][21]. Google: 900M+ monthly Gemini app users (secondary) [41]. Amazon: "tens of millions" in Alexa+ early access, US-wide rollout February 2026 (secondary) [45][49]. The Anthropic Economic Index (May 2026) is the only dataset with a published method: 43% of Claude conversations are work, 40% personal, and in 49% the person directs Claude to complete the task outright (primary) [12].
- **Cost control is now a harness part.** Copilot Cowork is off by default and billed per task in credits at $0.01 each with tenant, group and user caps [17]; Anthropic moved top-model access to "usage credits" (secondary) [41]; GitHub Copilot moved to usage-based billing (secondary) [42].
- **The bets are on the office suite and the OS, not new apps.** Agentic Word/Excel/PowerPoint went GA April 22, 2026 with +52%/+67%/+11% weekly engagement (primary, vendor) [19]; Anthropic sells Excel, PowerPoint, Word, Outlook and Chrome add-ins (captured system prompt, secondary) [13]; Google and Apple are making the phone OS the tool layer (AppFunctions, Computer Control, App Intents schemas) (primary) [29][30][33].

## How to read a non-technical agent surface

Analogy: a developer harness is a workshop you walk into. A non-technical harness is a concierge desk: you hand over a request, answer a few questions, and later get a result and a bill. Every design decision is about the desk: how you describe the job, how you see progress, when you are asked, what the concierge may touch, who pays.

```
 Surface     chat app | desktop app | browser panel | Office add-in | phone OS | speaker/TV | Slack/Teams
 Loop        "plan -> checkpoints -> approve -> result" (Microsoft) | "task -> progress -> notification" (Anthropic, OpenAI)
 Tools       connectors/MCP | browser with your logins | Office object model | app functions / intents
 Context     tenant graph (Work IQ) | account memory | files in a sandbox | on-screen entities (Siri)
 Permissions per-action prompts | site-scoped allows | checkout confirmation | intent "safe to run" | classifier
 Runtime     cloud VM (Cowork, ChatGPT Work, Spark, Copilot Cowork) | local VM sandbox | phone | Echo
 Orchestr.   scheduled tasks, routines, "autopilots" with their own identity, plugin/skill bundles
```

The recurring move is to wrap a developer agent engine in a concierge desk. Anthropic calls Cowork "Claude Code for the rest of your work" (secondary) [41]; ChatGPT Work has "Codex technology built-in" (secondary) [36]; Microsoft calls Copilot Cowork "a model wrapped in an agentic harness that can close its own feedback loop" (primary) [21] and built Scout on OpenClaw (primary) [20].

## OpenAI

**Surfaces.** ChatGPT web, mobile and desktop; since July 9, 2026 one desktop app that absorbed Codex, with "Work" and "Codex" as its modes and chat "relegated to a minor feature in the sidebar" (secondary) [36]; a Chrome extension and "revamped in-app browser" with "authenticated sites, persistent multi-tab sessions, file downloads" (secondary) [41]; apps inside ChatGPT via the Apps SDK; and, until August 9, 2026, Atlas.

**End-to-end delegation.**
- *ChatGPT Work* (July 9, 2026): "an agent powered by Codex + GPT-5.6 that can act across apps and files, stay on tasks for hours, and turn a goal into finished work", on desktop, web and mobile, pulling from "Slack, Microsoft Teams, Google Drive, SharePoint, calendars, CRM services", with cloud "Scheduled Tasks" (secondary) [36][41]. A "Sites" beta hosts apps the agent builds [41].
- *Workspace agents* (April 22, 2026): "shared, Codex-powered agents for teams" that "run in the cloud and can keep working when the user is away", "gather context from connected systems, follow team processes, ask for approvals", triggered from Slack or a schedule; "an evolution of GPTs" for Business/Enterprise/Edu; cited uses include a weekly metrics reporter and lead outreach (archived copy of the OpenAI post, secondary) [40].
- *Codex in the ChatGPT mobile app* (May 14, 2026): "start tasks, review outputs, approve commands, and steer execution remotely" (secondary) [41].
- *Apps in ChatGPT* (Apps SDK, repository created October 6, 2025): an app is an MCP server whose tool results carry `_meta.ui.resourceUri` so ChatGPT renders an inline widget; widgets read `toolInput`, `toolOutput`, `widgetState` and can `callTool`, `requestDisplayMode`, `sendFollowUpMessage` (primary) [14][15]. The developer, not the model, ships the UI.
- *Atlas* (October 21, 2025, macOS "Chromium fork AI browser ... integrated Agent mode and browser memory with local login", secondary) [41]; agent mode "could autonomously visit pages, fill forms, download files, and maintain authenticated sessions" (secondary) [39].
- Operator, agent mode in chat, scheduled tasks, Pulse, voice: no 2026 evidence reachable; status **unverified**.

**Approvals and trust.** Workspace agents "ask for approvals" within "enterprise permissions and controls" [40]; Codex mobile has an approve-commands step [41]. The approval model of ChatGPT Work and the built-in browser is **unverified**; one user note says the built-in browser is on the Free plan (secondary, single source) [38].

**Adoption.** Nothing from OpenAI was reachable. The workspace-agents launch drew 119 Hacker News points and a thread "openly skeptical" (secondary) [42]. The July client "landed with real UX regressions": the Work/Codex split confused users, chats became harder to find, usage burned faster, and OpenAI issued "multiple usage-limit resets" (secondary) [41].

**Failures.** Atlas retired "less than a year after launch" (Slashdot, July 10, 2026) [37]; OpenAI: "We'll begin sunsetting the standalone Atlas browser" (quoted, secondary) [36]. A week after launch, headlines reported an Atlas flaw that could "plant persistent hidden instructions" (October 29, 2025) [43]; the same-origin study found two browsers "even reading masked password fields from the DOM" (secondary) [51]. The no-code Agent Builder is slated for shutdown after November 30, 2026 (see the developer-harness document).

## Anthropic

**Surfaces.** claude.ai, iOS/Android, Claude Desktop with "Chat for conversations, Cowork for Dispatch and longer agentic work, and Code for software development" (primary) [2]; Claude in Chrome (GA the week of June 29 to July 3, 2026, primary) [6]; Claude Tag in Slack, an org-shared @Claude replacing the per-user version on Team/Enterprise (primary) [5]; Excel and PowerPoint add-ins (beta), with Word and Outlook in a later captured prompt (secondary) [13]; Claude Design (April 17, 2026, "Anthropic Labs", bundled with paid plans, secondary) [52]; Dispatch (Pro/Max only, primary) [2][4].

**End-to-end delegation.**
- *Cowork.* Launched January 12, 2026 as "Claude Code for the rest of your work": "an agent with browser automation, connectors, and a sandboxed execution environment" (secondary) [41]. Anthropic's prompts call it "a desktop tool for non-developers to automate file and task management", later "an agentic knowledge-work desktop app" that "can use all of these as tools" (Chrome, Excel, PowerPoint, Design) (secondary) [13]. A teacher's account (July 6, 2026): "curriculum design, grading support, PowerPoint generation, and student-grade data analysis" (secondary) [41]. Cowork reached mobile and web on July 7, 2026 with execution that "can run server-side ... independent of whether the laptop is powered on" (secondary) [41][49]. "Teach Claude a skill" (July 21, 2026): record your screen narrating a workflow and Claude saves it as a reusable skill like `/file-expenses` (secondary) [41].
- *Role plugins.* `knowledge-work-plugins` (created January 23, 2026; 23.9k stars by September) ships eleven plugins (productivity, sales, support, product, marketing, legal, finance, data, enterprise search, bio-research, plugin management), each bundling "skills, connectors, slash commands, and sub-agents", wired to Slack, HubSpot, Notion, Jira, Snowflake and others via MCP; "markdown and JSON, no code" (primary) [10]. A community marketplace lists plugins that "passed automated security scanning" (primary) [11].
- *Browser.* Claude in Chrome "shares your browser's login state ... When Claude encounters a login page or CAPTCHA, it pauses and asks you to handle it manually"; documented workflows: form filling, file uploads, drafting in Google Docs, data extraction, multi-site tasks (primary) [1]. The desktop app gained a "sandboxed and configurable" built-in browser in July 2026 where "safety classifiers review actions on external sites" (primary) [7].
- *Scheduling.* Local desktop tasks with per-task permission modes; cloud Routines on hourly cron, API call or GitHub event that run "autonomously" with "no permission prompts"; "morning briefings that pull from your calendar and inbox" is a listed use (primary) [3][8].
- *Phone.* The app "is a client for Claude Code sessions rather than a place where code runs": cloud sessions, Remote Control of your machine, or Dispatch, which "decides how to handle" a task and keeps "research, document editing, and spreadsheet work" in Cowork (primary) [2][4]. Push notifications fire "when a long task finishes or it needs a decision" [8].

**Approvals and trust.** Browser actions prompt with "Claude in Chrome wants to" and an "allow all actions on that site for the session" option (primary) [1]. Computer-use approvals in Dispatch-spawned sessions "expire after 30 minutes and re-prompt" (primary) [2]. The Slack doc warns: "Claude may follow directions from other messages in the context, so users should make sure to only use Claude in trusted Slack conversations" (primary) [5]. Local Cowork sessions read device-deployed policy but "never fetch admin-console settings"; a `requireCoworkFullVmSandbox` setting exists; remote Cowork sessions "receive neither" (primary) [2]. Since the week of August 3, 2026, "auto mode becomes the default permission mode" for Claude Code: a classifier approves actions (primary) [9].

**Adoption.** Economic Index (period May 2026, claude.ai chat and Cowork, CC BY 4.0): 43.4% work, 40.2% personal, 16.5% coursework; 48.6% "automation" versus 51.4% "augmentation"; top topics content creation (22.7%), education (13.2%), software development (11.5%), research (10.9%); top artifacts "explanation or answer" (16.7%) and "document or report" (14.9%); the most common matched work task is "search electronic sources ... for information" (5%) (primary) [12]. Non-technical delegation in the data is research, writing and documents, not multi-app automation. Anecdotes cut both ways: "top 3 most exciting moments I've ever had with technology" (483 upvotes, January 2026) [41]; a trend digest flagged "claude cowork" search interest as "collapsed" by mid-April 2026 and cited its own move "away from Cowork due to instability" (secondary, single source) [42]; an April 30, 2026 status page showed claude.ai, Claude Code and Cowork in "Major Outage" (secondary) [41].

**Failures.** "Claude for Chrome Flaw Lets Rogue Extensions Trigger Gmail Reads" (July 15 to 16, 2026); "Claude Cowork Flaw Could Let AI Agent Escape Its VM and Access Mac Files" (July 24, 2026) (secondary headlines) [43]. Pricing churn: a test removing Claude Code from the Pro plan (April 22, 2026, 413 HN points) was reversed within days, Anthropic citing "Claude Code, Cowork, and long-running async agents" changing usage patterns (secondary) [41][42]; in July 2026 top-model access moved to "usage credits" [41].

## Google

**Surfaces.** The Gemini app (900M+ monthly users per Google at I/O, May 19, 2026, secondary) [41]; Gemini in Chrome (US launch with an agentic preview, September 18, 2025, secondary) [42]; "Gemini Intelligence" on Android (May 12, 2026, secondary) [44]; Search's AI Mode; Gemini in Workspace (no 2026 evidence reachable, **unverified**); Gemini Spark.

**End-to-end delegation.**
- *Gemini Spark* (I/O, May 19, 2026): "a 24/7 personal AI agent on cloud VMs, checking with users before major actions", on "dedicated Google Cloud virtual machines, allowing long-running tasks while user devices are closed", with MCP planned and a macOS app (secondary) [41]. Search gained "information agents" that monitor the web over time and "generative UI" that "dynamically builds custom visual tools and simulations on the fly" [41].
- *Android.* Gemini Intelligence: "agentic cross-app task completion, web browsing, form-filling"; "copying a grocery list from notes and adding to a shopping cart, with human confirmation required before checkout"; "auto browse" moves from Chrome desktop to Android; forms filled from an opt-in "Personal Intelligence" profile; "vibe-code" widgets by description; Galaxy and Pixel first, summer 2026 (TechCrunch, May 12, 2026, secondary) [44].
- *The OS as tool layer.* **AppFunctions**: "the mobile equivalent of tools within the Model Context Protocol"; apps "behave like on device MCP servers"; callers need `EXECUTE_APP_FUNCTIONS`; Android 16+; "As of May 2026, AppFunctions integration with Gemini is in a private preview with trusted testers" (primary) [29]; still alpha11 on August 26, 2026 (primary) [31]. **Android Computer Control**: "allows OEM-preloaded AI assistants to perform task automation on selected apps"; target apps run "on a virtual device, similar to casting", the assistant captures screenshots and injects input, and "the system automatically displays a permission dialog" per app; requires the privileged `ACCESS_COMPUTER_CONTROL` (primary, preview) [30]. A typed tool API and a screenshot-and-click fallback, both in the OS, and only preloaded assistants get the fallback.
- *Chrome.* Auto browse reportedly needs Google AI Pro or Ultra and was US-only for consumers as of July 2026 (secondary, single source) [38]; in January 2026 Google said it would make Chrome for Android agentic and tested Gemini "Skills" (secondary headlines) [43].

**Approvals and trust.** Checkout confirmation and Spark's "checking with users before major actions" [44][41]; Chrome's security team published "Architecting Security for Agentic Capabilities in Chrome" on December 8, 2025 (primary, intro only) [32]; Computer Control's per-app dialog is the OS gate [30].

**Adoption.** Vendor only: 900M+ Gemini app users, "3.2 quadrillion tokens/month" (secondary) [41]. Observers "tried to decode the relation between Gemini Spark, Antigravity, and Google's internal/external agent harnesses" [41]: the packaging is not legible yet.

**Failures.** "Google Gemini Prompt Injection Flaw Exposed Private Calendar Data via Malicious Invites" (January 20 to 21, 2026); "Chrome flaw let extensions hijack Gemini's camera, mic, and file access" (March 3 to 4, 2026) (secondary headlines) [43]. Project Mariner was reportedly shut down as a standalone prototype on May 4, 2026 and absorbed into the Gemini app (secondary, single source, **unverified**) [49]. The June 18, 2026 Gemini CLI retirement drew heavy developer backlash (developer document).

## Microsoft

Microsoft has the most complete non-technical stack and the most documented approval and admin model, because it already owned identity, data and admin.

**Surfaces.** The Microsoft 365 Copilot app (redesigned May 28, 2026) and Copilot in Word, Excel, PowerPoint, Outlook, Teams; Copilot Cowork as a toggle in the Copilot app plus iOS and Android (primary) [17][18]; Scout in Teams and a desktop app (primary) [20]; Copilot Studio for makers; the Agent Store and admin center; Windows 11's taskbar, File Explorer and voice (primary) [27][28]; Windows 365 for Agents (primary, preview) [22].

**End-to-end delegation.**
- *Copilot Cowork* (announced March 9, 2026; Frontier preview March 30; GA June 16): "Describe the outcome you want and Cowork automatically grounds the work in your emails, meetings, messages, files, and data"; it "turns your request into a plan. The plan continues in the background, with clear checkpoints ... You can see any actions that it is recommending, then approve changes before they are applied" (primary) [16]. Observed uses: "orchestrating inbox workflows, conducting deep research, generating structured documents, and even building full web pages" [18]; comparing "nearly four thousand files across two product versions" (vendor anecdote) [17]. At GA: "Browser use via Edge" in Frontier under existing enterprise policies, partner plugins (Harvey, Miro, monday.com, Moody's, S&P Global, LSEG; Adobe, Atlassian, Box, Canva coming), reusable "skills", and model choice: "At general availability, Copilot Cowork runs on Anthropic models, including Opus 4.8 and Sonnet 4.6", GPT-5.5 in Frontier, a fine-tuned "Cowork 1" coming (primary) [17][18].
- *Agentic Office* (GA April 22, 2026): "multi-step, app-native actions directly in your documents, worksheets, and presentations". Microsoft's lesson: "When we first shipped Copilot, foundation models were not powerful enough to use Copilot to command the applications ... models have made meaningful leaps ... now better at handling multi-step edits reliably" (primary) [19].
- *Scout and "Autopilots"* (June 2, 2026): "always-on agents that work autonomously, with their own identity"; Scout "can proactively schedule and coordinate meeting times across time zones, flag important meetings, and generate the materials you need ... automatically blocks time on your calendar"; "powered by OpenClaw open-source technology"; reaches "your browser, local resources, and model context protocol servers" via the desktop app; experimental via Frontier with Intune policy and an opt-in attestation (primary) [20].
- *Prebuilt and maker agents.* Researcher and Analyst ship with the license; makers build in Copilot Studio with "a natural language authoring tool"; admins deploy from the Agent Store to "Just me, Entire organization, or Specific users/groups" (primary) [24]. Copilot Studio's Computer Use lets an agent "control the mouse and keyboard ... navigate websites and desktop apps ... extract text from the screen", for "legacy systems, vendor portals, desktop-only applications" (primary) [26]. Scheduled prompts run in an auto-provisioned environment where "all connectors are blocked" except Copilot actions, Teams and Outlook (primary) [25].
- *Windows.* Long tasks "transition out of the interaction interface and continue running in the cloud" and appear as a taskbar icon with hover summaries; Copilot in File Explorer and voice are "in preview" (primary) [27][28]. Copilot Actions (an isolated "Agent Workspace") and "Copilot Tasks" (background goals with "consent gates before anything consequential — payments, purchases, outbound email") are described by one secondary note; Microsoft's pages were unreachable: **unverified** [49]. At Build (June 2026) Microsoft framed Windows as "a secure execution layer for agents" and showed "OpenClaw in Windows" (secondary) [41].

**Approvals and trust.** Three documented layers: in-task checkpoints and approve-before-apply [16]; identity, where every Scout agent "operates under its own governed Entra identity, not a shared, anonymous service account", "Sensitive actions can require a human to sign off", and Purview labels and DLP are "enforced in the moment" [20]; and tenant governance via Agent 365, GA May 1, 2026, "the control plane for agents", with "Windows 365 for Agents" as "a secured, managed environment", Defender/Intune discovery of "shadow agents" starting with OpenClaw "and expanding soon to ... GitHub Copilot CLI and Claude Code", and from June 2026 a map of "the devices they run on, MCP servers configured for those agents, the identities associated with them" (primary) [22][23]. Cost is a permission too: Cowork "is off by default", with "spending limits at the tenant, group, and user levels" and per-task credit prices shown to the user (primary) [17].

**Adoption (vendor).** Microsoft 365 Copilot "has surpassed 30 million paid seats, with net seat adds more than doubling quarter over quarter" (Q4 FY26, cited July 30, 2026); Cowork was "the fastest growing feature in the history of our Frontier program"; Office agentic mode raised weekly tries per user 52% (Word), 67% (Excel), 11% (PowerPoint); "analysis-related work accounts for 49% of all tasks" delegated to Cowork in late July 2026 (primary) [17][19][21]. The Cowork "core team ... growing from three engineers at the start to just nine" (primary) [21]. The claim that Copilot Cowork is "30-40% cheaper" than Claude Cowork rests on an internal test of 12 prompts (primary, vendor) [17].

**Failures.** EchoLeak (CVE-2025-32711, June 2025), zero-click exfiltration via a crafted email (secondary) [49]; "Reprompt attack let hackers hijack Microsoft Copilot sessions" (January 15, 2026); "SearchLeak" / "One-Click Microsoft 365 Copilot Flaw Could Have Let Attackers Steal Emails, Files, and MFA Codes" (June 16 to 18, 2026); "Microsoft Copilot Personal Flaws Could Let One Click Exfiltrate Data From Connected Apps" (August 19, 2026) (secondary headlines) [43]; a new Recall extraction proof of concept (April 2026, secondary) [49]. A Copilot rename in Windows 11 (April 2026) was widely misread as a removal (secondary) [42].

## Apple

**Surfaces.** Siri and Apple Intelligence on iPhone, iPad, Mac; App Intents as the developer contract; Shortcuts, Spotlight, the Action button.

**What can be delegated.** Little that is agentic today; most is next release. On January 12, 2026 Apple said "the next generation of Apple Foundation Models" would be "based on Google's Gemini models and cloud technology", hosted with Private Cloud Compute, for "a more personalized Siri" (joint statement via AI News, secondary) [41], targeting iOS 26.4 in March 2026 with "personal context understanding and improved app control" [41]. On February 11, 2026 Bloomberg reported the update "runs into snags in internal testing" and 9to5Mac reported features "pushed back ... beyond iOS 26.4" (secondary, via a digest) [50]; a March 4 digest refers to "the delay of Apple Intelligence/New Siri" [41]. At WWDC on June 8, 2026 Apple introduced "Siri AI in iOS 27": "revisit your conversations ... in a new dedicated app", "natural language abilities to edit and write emails, texts, and documents" (primary) [34]. The developer session: "Siri can now access your app's entities", "Siri can take action using your app's intents ... Intents describe the actions your app supports, the parameters they require, and when they're safe to run", "Siri can also understand on-screen context", with cross-app requests like "text my wife her plane ticket" (primary, transcript) [35]. Apple's docs now say "Siri AI" and list schema domains including Assistant, Browser, Calendar, Files, Mail, Messages, Presentation, Spreadsheet and Word processor (primary) [33].

**Approvals and trust.** The most static model: developers declare intents and "when they're safe to run"; on-screen context is only what apps annotate; donations give "behavioral cues" (primary) [33][35]. No user-facing approval loop for multi-step tasks appears in reachable sources: **unverified**.

**Adoption and backlash.** No usage numbers reachable. Apple "did not roll out Siri in the EU after regulatory issues" following a Commission compliance finding (Reuters, June 9, 2026, secondary) [42]. The 2024 to 2026 Siri delay is the best-known consumer agent retreat of the period; specifics are prior knowledge, **unverified** here.

## Amazon

**Surfaces.** Echo devices ("97% of existing Alexa devices"), the Alexa app, Alexa.com (January 5, 2026), Fire TV (August 19, 2026), Amazon's shopping search bar (May 13, 2026) (secondary) [45][48][53].

**End-to-end delegation.** Household operations: "smart home control, calendar and to-do updates, dinner reservations, grocery additions to Amazon Fresh or Whole Foods, recipe discovery"; partners "Angi, Expedia, Square, and Yelp, alongside ... OpenTable, Suno, Ticketmaster, Thumbtack, and Uber"; Amazon asks users to "share personal documents, emails, and calendars so the AI can manage school schedules, medical appointments, and household reminders" (TechCrunch, January 5, 2026, secondary) [45]. Alexa+ became "available to everyone in the U.S." on February 4, 2026 (secondary) [49], costs $19.99/month without Prime, and is free on compatible Fire TV devices since August 19, 2026 (secondary) [48].

**Approvals and trust.** Not documented in reachable sources; **unverified**. Internal quality target: "97% correct answers" (Jassy, Bloomberg memo, secondary) [46].

**Adoption (vendor).** "Over a million" in early access (June 2025) [47]; "tens of millions have Early Access", "2–3x more conversations, 3x more shopping ... opt-outs in the low single digits", and "76% of Alexa+ usage involves capabilities 'no other AI can do'" (January 2026, secondary) [45]; users "talk to Alexa+ 2x as much" (June 2026, secondary) [46]; Q2 2026: Alexa for Shopping "used by 350M+ customers in 12 months", "Alexa+ triers convert to Prime at ~25% higher rates" (secondary) [46]. An analyst memo: "Consumer AI remains unproven: Alexa+ has distribution and usage uplift, but Nova is not viewed as frontier" [46]. The agent is a funnel.

**Failures.** "Some users report misfires that Amazon claims are overrepresented online" (secondary) [45]. No Alexa+ security incidents appear in the 2026 archive [43].

## Meta

**Surfaces.** The Meta AI app and meta.ai; Meta AI inside WhatsApp, Instagram, Messenger, Facebook; Ray-Ban Meta glasses; business agents for WhatsApp Business (inferred from June 2026 search trends, secondary) [42].

**What can be delegated.** Chat, voice, image and video, shopping help; little documented long-running or multi-app automation. Meta acquired Manus, a general agent startup, "for over $2B, at $100M ARR" (December 29, 2025, secondary) [41]; its role in Meta AI is **unverified**. Muse Spark (April 8, 2026), the first Meta Superintelligence Labs model, "with tool use, visual chain of thought, and multi-agent orchestration", went "live on meta.ai and the Meta AI app" that day (secondary) [41]; "Meta AI voice conversations powered by Muse Spark" added "interruption, language switching, image generation, and live camera-grounded interaction" (May 12, 2026) [41]; Muse Image (July 7, 2026) runs "an explicitly agentic generation loop: planning, web search, tool use, code execution, and self-refinement" [41]. The distribution thesis: "Meta can distribute a capable free assistant to 1B+ users inside its existing surfaces" (analyst, secondary) [41].

**Approvals and trust.** Undocumented in reachable sources.

**Failures.** "Hackers hijacked Instagram accounts by tricking Meta AI support chatbot into granting access" (June 2 to 5, 2026); "Meta AI Recovery Tool Flaw Exposed 20,000+ Instagram Accounts" (June 9, 2026) (secondary headlines) [43]: a support agent, not a browsing agent, socially engineered into a consequential action.

## Comparison table

| Vendor | Flagship non-technical agent (Sep 2026) | Lives in | End-to-end delegation today | Approval model (documented) | Runtime | Adoption evidence | Notable failures |
|---|---|---|---|---|---|---|---|
| OpenAI | ChatGPT Work (Jul 2026); workspace agents (Apr 2026); Apps SDK apps | ChatGPT desktop/web/mobile, Chrome extension | Multi-hour tasks over files, connectors, browser; cloud scheduled tasks; shared team agents | "Ask for approvals"; mobile command approval; rest unverified | Cloud + local built-in browser | None public; modest HN reception; launch UX regressions | Atlas retired < 1 year; injection findings; Agent Builder shutdown |
| Anthropic | Cowork (Jan 2026; cloud + mobile Jul 2026); Claude in Chrome (GA Jul 2026); Office add-ins; Design | Desktop tabs, web, mobile, Chrome, Slack, Excel/PowerPoint | Research, documents, spreadsheets, browser tasks, routines, skills taught by screen recording | Per-action browser prompts, site-scoped allow; 30-min computer-use approvals; classifier on external sites; routines unprompted | Local VM sandbox or Anthropic cloud | Economic Index (method published); 23.9k-star plugin repo; anecdotes; outages | Chrome flaw, Cowork VM escape (Jul 2026); pricing churn |
| Google | Gemini Spark (May 2026); Gemini Intelligence on Android; auto browse | Gemini app, Android OS, Chrome | Cross-app phone tasks with checkout confirmation; 24/7 cloud agent; web monitoring | "Checks before major actions"; checkout confirmation; per-app OS dialog | Google Cloud VMs; phone virtual device | 900M+ MAU (vendor); AppFunctions still private preview | Calendar-invite injection (Jan 2026); Chrome/Gemini extension flaws (Mar 2026); Mariner shutdown (reported) |
| Microsoft | Copilot Cowork (GA Jun 2026); Scout/Autopilots (preview); agentic Office (GA Apr 2026) | M365 Copilot app, Office, Teams, Windows taskbar | Plan-and-execute over tenant data, Edge browsing, plugins, skills; always-on calendar agent; UI automation via Copilot Studio | Checkpoints, approve-before-apply, per-agent Entra identity, Purview, spending caps, Agent 365 | Microsoft cloud; Windows 365 for Agents; local workspace (reported) | 30M+ paid seats; >half Fortune 500 on Cowork (vendor) | EchoLeak, Reprompt, SearchLeak, Copilot Personal exfil; Recall PoC |
| Apple | Siri AI (iOS 27, announced Jun 2026) | iPhone/iPad/Mac OS | Next release: app actions, on-screen context, cross-app hand-offs | Developer-declared intents "safe to run"; no user loop documented | On-device + Private Cloud Compute (Gemini-based) | None; not shipping in EU | Delays (26.4 to 27); EU compliance finding |
| Amazon | Alexa+ (US GA Feb 2026) | Echo, app, web, Fire TV | Reservations, rides, groceries, smart home, household docs via partners | Unverified | Amazon cloud + devices | Tens of millions (vendor); Prime conversion uplift | Reported misfires; quality "still needs proof" |
| Meta | Meta AI (Muse Spark, Apr 2026) | WhatsApp, Instagram, app, glasses | Chat, voice, media; agentic creation loops; business agents emerging | Undocumented | Meta cloud | 1B+ reach framing; no delegation data | Support bot social-engineered into account takeovers (Jun 2026) |

## Cross-cutting observations

1. **One script.** Describe the outcome; the agent plans; you see checkpoints; you get a notification; you review. Microsoft wrote it down [16]; Dispatch and Cowork behave the same way [2][41]; ChatGPT Work and Gemini Spark are described identically by observers [36][41]. A harness convention independent of model.
2. **Local versus cloud runtime is the live design fight.** Anthropic started local (a VM on the Mac) and added cloud in July 2026; Microsoft started cloud and added local Edge browsing; OpenAI kept a local built-in browser plus cloud Work; Google went to cloud VMs plus an OS-level phone runtime. Each choice moves the blast radius: local means the agent has your logins [1]; cloud means the vendor holds the session and the meter.
3. **Approvals are being pushed down into identity and policy, and up into classifiers.** An Entra identity per agent with Purview in the loop [20][22]; a second model reviewing actions [7][9]; a static schema [35]. None gives the user a consistent view of what an action can cost or destroy; Copilot Cowork's per-task credit price is the closest thing, and it is a price, not a risk.
4. **Extensions and connectors are where trust breaks.** Four 2026 incidents were extension or connector escalations: the Gemini panel, Claude for Chrome, Copilot Personal connected apps, the Cowork VM [43].
5. **Generated UI is arriving from the vendors.** Apps SDK widgets [14], Google's generative UI in Search and vibe-coded widgets [41][44], Claude Design and `/design` artboards [52][9], OpenAI's Sites [41], Cowork "building full web pages" [18]. The "browser workspace with generated UI" the program wants to evaluate is already a feature of every incumbent chat client.

## What this means for the thesis

**Supports.**
- *The harness is the product.* Microsoft says models could not command Office in 2023 and can now, and what it shipped in 2026 was a runtime, identities, cost controls and an approval loop, from a nine-person core team [19][21]. The competitive claims are harness properties: cheaper per task, cloud-hosted, governed, multi-model [17].
- *Neither a CLI nor a desktop app is the default for non-technical people.* True and already priced in: the desktop is a client [4]; ChatGPT Work is "web, mobile, and desktop" [36]; traffic runs through chat apps, the office suite, the phone OS and the speaker.
- *The default harness still needs reimagining.* OpenAI's July 2026 client confused users enough to force usage resets [41]; buyers could not tell Spark from Antigravity [41]; approval models differ across all seven vendors; the incident record shows open seams; Apple, with the most OS control, has not shipped.

**Contradicts.**
- *The incumbents are not stuck; they move faster than a startup can.* Cowork went from idea to half the Fortune 500 in six months on top of Work IQ and Entra [21]. Google put the tool layer into Android itself [29][30]. The moat is distribution plus identity plus data graph, not the interface. A reimagined harness must enter through one of these surfaces or a gap they leave.
- *The standalone new surface already failed once.* Atlas lasted nine months and was folded back into the chat client [36][37]. "Reimagine the surface" has a fresh counter-example; "reimagine the runtime and trust model inside existing surfaces" is what survived.
- *What non-technical people actually delegate is mostly research and writing.* The Economic Index shows non-developer use dominated by search, explanation, documents and copy [12]. The elaborate multi-app harness may be ahead of demand; the thin harness (chat plus retrieval plus a document) is where usage sits.

**Nuance.**
- The bottleneck the vendors are hitting is trust plumbing: identity per agent, permission scoping, injection resistance at the browser and email boundary, spend control. Microsoft sells exactly that as Agent 365 and Scout [20][22]. A thesis that "the harness is the bottleneck" should name which part; the evidence points at permissions, runtime and cost, not surface.
- The desktop is not dead as a *runtime* (local VMs, local browsers with your logins, Windows agent workspaces). It is dead as the place you watch the agent from. The winning shape is "phone as remote control, cloud or local VM as workshop", which every vendor now ships.
- Vendor claims dominate this document. The independent-ish data (Economic Index, incident headlines, community sentiment) says usage is broad and shallow, failures are frequent at the edges, and enthusiasm is real but volatile.

## Open questions and unverified claims

1. OpenAI's approval model inside ChatGPT Work and the built-in browser, and the 2026 status of Operator, agent mode, Pulse, scheduled tasks and voice: no OpenAI page reachable. **Unverified.**
2. Whether Cowork runs on the Claude Code engine: strongly implied (same app, Dispatch spawning Code sessions, shared Customize configuration, the "Claude Code for the rest of your work" framing) but not stated on a reachable primary page [2][41].
3. Cowork's GA date (April 2026 per the developer document's secondary sources) and any Cowork user counts. **Unverified.**
4. Project Mariner's reported May 4, 2026 shutdown and the Windows Copilot Actions/Tasks details come from one community note [49]. **Unverified.**
5. Gemini in Workspace's 2026 state and the Gemini app's "Agent Mode": no reachable evidence. **Unverified.**
6. Apple: the reported $1B to $1.5B per year Gemini payment and the exact iOS 26.4 outcome are secondary [50].
7. Alexa+ approval behavior for purchases and bookings: **unverified**; all Alexa+ numbers are Amazon's.
8. Meta: 2025 Meta AI app user claims, Manus integration, and how business agents act for users: **unverified**.
9. The Chrome agentic-security design: only the post's introduction was mirrored [32].
10. Copilot Studio computer-use GA date and Agent 365's original announcement (Ignite, November 2025, prior knowledge): **unverified**.

## Sources

1. Anthropic, "Use Claude Code with Chrome", code.claude.com/docs/en/chrome (Sep 2026). primary
2. Anthropic, "Desktop application", code.claude.com/docs/en/desktop (Sep 2026). primary
3. Anthropic, "Schedule recurring tasks in Claude Code Desktop", code.claude.com/docs/en/desktop-scheduled-tasks (Sep 2026). primary
4. Anthropic, "Mobile", code.claude.com/docs/en/mobile (Sep 2026). primary
5. Anthropic, "Claude Code in Slack", code.claude.com/docs/en/slack (Sep 2026). primary
6. Anthropic, "What's new: Week 27", code.claude.com/docs/en/whats-new/2026-w27 (Jul 2026). primary
7. Anthropic, "What's new: Week 28", code.claude.com/docs/en/whats-new/2026-w28 (Jul 2026). primary
8. Anthropic, "What's new: Week 16", code.claude.com/docs/en/whats-new/2026-w16 (Apr 2026). primary
9. Anthropic, Claude Code docs index with weekly digests (weeks 13, 14, 32, 34), code.claude.com/docs/llms.txt (Sep 2026). primary
10. Anthropic, knowledge-work-plugins README, github.com/anthropics/knowledge-work-plugins (Jan–Sep 2026). primary
11. Anthropic, claude-plugins-community README, github.com/anthropics/claude-plugins-community (Sep 2026). primary
12. Anthropic Economic Index, global usage and top work tasks, period May 2026, anthropic.com/economic-index (via MCP tool, Sep 2026). primary
13. Community-captured claude.ai system prompts (Opus 4.6, Opus 5, Fable 5 of Jun 9, 2026), github.com/asgeirtj/system_prompts_leaks and github.com/jujumilk3/leaked-system-prompts. secondary
14. OpenAI, openai-apps-sdk-examples README, github.com/openai/openai-apps-sdk-examples (Oct 2025–Sep 2026). primary
15. OpenAI, apps-sdk-ui README, github.com/openai/apps-sdk-ui (Sep 2026). primary
16. Microsoft, "Copilot Cowork: A new way of getting work done", microsoft.com/en-us/microsoft-365/blog/2026/03/09/copilot-cowork-a-new-way-of-getting-work-done/ (Mar 2026). primary
17. Microsoft, "Copilot Cowork is now generally available", microsoft.com/en-us/microsoft-365/blog/2026/06/16/copilot-cowork-is-now-generally-available/ (Jun 2026). primary
18. Microsoft, "Copilot Cowork: From conversation to action across skills, integrations, and devices", microsoft.com/en-us/microsoft-365/blog/2026/05/05/copilot-cowork-from-conversation-to-action-across-skills-integrations-and-devices/ (May 2026). primary
19. Microsoft, "Copilot's agentic capabilities in Word, Excel, and PowerPoint are generally available", microsoft.com/en-us/microsoft-365/blog/2026/04/22/copilots-agentic-capabilities-in-word-excel-and-powerpoint-are-generally-available/ (Apr 2026). primary
20. Microsoft, "Introducing Microsoft Scout, your always-on personal agent", microsoft.com/en-us/microsoft-365/blog/2026/06/02/introducing-microsoft-scout-your-always-on-personal-agent/ (Jun 2026). primary
21. Microsoft, "The next measure of AI momentum is work transformed", microsoft.com/en-us/microsoft-365/blog/2026/07/30/the-next-measure-of-ai-momentum-is-work-transformed/ (Jul 2026). primary
22. Microsoft Security, "Microsoft Agent 365, now generally available, expands capabilities and integrations", microsoft.com/en-us/security/blog/2026/05/01/microsoft-agent-365-now-generally-available-expands-capabilities-and-integrations/ (May 2026). primary
23. Microsoft Learn, "Agent management in Microsoft 365 admin center", via github.com/MicrosoftDocs/microsoft-365-docs (agent-365-overview.md, Sep 2026). primary
24. Microsoft Learn, "Set up Agent Store in Microsoft 365 Copilot", via MicrosoftDocs/microsoft-365-docs (Sep 2026). primary
25. Microsoft Learn, "Microsoft 365 environment for scheduled prompts", via MicrosoftDocs/microsoft-365-docs (Sep 2026). primary
26. Microsoft Learn, Copilot Studio "Computer Use" unit, via github.com/MicrosoftDocs/learn (Sep 2026). primary
27. Microsoft Learn, "Run and monitor AI agents on the Windows taskbar", via MicrosoftDocs/learn (Sep 2026). primary
28. Microsoft Learn, "AI and agent integration across Windows experiences", via MicrosoftDocs/learn (Sep 2026). primary
29. Google, "Overview of AppFunctions", developer.android.com/ai/appfunctions (Sep 2026). primary
30. Google, "Android Computer Control", developer.android.com/ai/computer-control (Sep 2026). primary
31. Google, androidx.appfunctions release notes, developer.android.com/jetpack/androidx/releases/appfunctions (Aug 2026). primary
32. Google Security Blog, "Architecting Security for Agentic Capabilities in Chrome", security.googleblog.com/2025/12/architecting-security-for-agentic.html (Dec 2025), via the chromium/chrome.security mirror, intro only. primary, partial
33. Apple, App Intents docs: "Apple Intelligence and Siri AI", "App schema domains", developer.apple.com/documentation/appintents (Sep 2026). primary
34. Apple, WWDC26 session 121, "Announcing Apple's next big step for Siri and iPhone", developer.apple.com/videos/play/wwdc2026/121/ (Jun 2026). primary
35. Apple, WWDC26 session 240, "Build intelligent Siri experiences with App Schemas", developer.apple.com/videos/play/wwdc2026/240/ (Jun 2026). primary
36. F. Lardinois, The New Stack, on GPT-5.6, ChatGPT Work, the Codex merger and Atlas sunset (Jul 2026), via the rocksun/mwblog mirror. secondary
37. Slashdot, "OpenAI to Retire ChatGPT Atlas Browser Less Than a Year After Launch" (Jul 10, 2026), via api-evangelist mirror. secondary
38. comparethe.com, "ChatGPT Atlas Is Shutting Down. What Browser Should I Use Instead?" (Jul 29, 2026), github.com/comparethe/comparethe. secondary
39. D. Vaughan, "Atlas Sunsets on 9 August" (Aug 1, 2026), github.com/danielvaughan/codex-blog. secondary
40. OpenAI, "Introducing workspace agents in ChatGPT", openai.com/index/introducing-workspace-agents-in-chatgpt/ (Apr 22, 2026), via an archived copy in coolplayagent/relay-teams. secondary copy of a primary
41. smol.ai AI News daily issues, github.com/smol-ai/ainews-web-2025: 2025-10-21, 2025-12-29, 2026-01-12, 01-20, 03-04, 03-10, 04-08, 04-09, 04-22, 04-30, 05-12, 05-14, 05-19, 05-20, 06-02, 06-05, 06-08, 06-30, 07-01, 07-07, 07-09, 07-10, 07-21. secondary
42. BuilderPulse daily digests, github.com/BuilderPulse/BuilderPulse, en/2026 (Apr 13–Aug 14, 2026). secondary
43. Vu1nT0tal/yarb security-headline archive, github.com/Vu1nT0tal/yarb, archive/2025 and 2026, dated entries. secondary headlines
44. I. Mehta, TechCrunch, "Google brings agentic AI and vibe-coded widgets to Android" (May 12, 2026), summarized in github.com/blamouche/Engineering-Forward. secondary
45. TechCrunch, "Alexa without an Echo" (Jan 5, 2026), summarized in the Skynet Today digest of Jan 5, 2026. secondary
46. hczhu/stock-research: Bloomberg Jassy interview memo (Jun 28, 2026) and Amazon Q2 2026 earnings notes. secondary
47. The New Oracle, "Alexa Gets a Gen-AI Boost: Over a Million Users Gain Early Access" (Jun 24, 2025). secondary
48. Lucaskk/daily-news, Aug 20, 2026 entry citing Amazon and TechCrunch (Aug 19, 2026). secondary
49. zylos-ai/zylos-timeline, "Desktop Agent UIs and the Rise of Ambient Computing" (Jul 16, 2026), citing TechCrunch and BleepingComputer. secondary
50. GamerScroll compilation (Sep 2026) citing MacRumors (Jan 12, Jan 30, 2026), CNBC (Jan 12, 2026), Bloomberg and 9to5Mac (Feb 11, 2026). secondary
51. F. Roesner and D. Kohlbrenner, "Agentic Browsers and the Same-Origin Policy" (2026), franziroesner.com/pdf/roesner_kohlbrenner_2026_agentic_sop.pdf, via the irsdl/webhacklist entry (Aug 2026). secondary summary
52. rohitg00/awesome-claude-design README (Claude Design, Apr 17, 2026; anthropic.com/news/claude-design-anthropic-labs). secondary
53. koltregaskes/kols-korner daily digest (May 13, 2026), Alexa+ shopping assistant. secondary
