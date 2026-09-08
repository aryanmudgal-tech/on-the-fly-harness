# Non-technical agent surfaces: startups and enterprise vendors, from Manus to the Humane Pin

*Research program: agent harnesses. Written 2026-09-08. Status: draft, pending verification.*

**Sourcing note.** This session's egress proxy blocked web search, Wikipedia and every vendor and press domain except github.com. **Primary** facts were read from vendor repositories and docs hosted on GitHub. **Via digest** facts come from press headlines and excerpts reproduced in GitHub-hosted news archives; the outlet and date are given, but the article was not read. **Unverified** facts rest on training knowledge or one secondary source. Traction numbers are vendor claims unless labelled independent.

## What this document answers

- Where agents already reach non-developers: through which surface, doing what end to end, with what approvals, priced how, at what scale.
- Which of the seven harness parts each product rebuilt for non-technical users, and which it simply hid.
- What the 2025–2026 acquisitions, unwindings and shutdowns (Manus, Dia, Convergence, Atlas, Adept, Humane) say about new surfaces as businesses.
- What this supports or contradicts in the thesis that the default harness must be reimagined beyond the CLI and the desktop app.

## TL;DR

- **The revenue is in agents embedded in existing enterprise surfaces, not in new surfaces.** Agentforce crossed $1.2B ARR (May 2026) [57]; ServiceNow's Now Assist passed $600M ACV (end 2025) [61]; Glean passed $300M (May 2026) [49]; Sierra raised at $15B (May 2026) [66]. All live inside a CRM, an ITSM console, a search bar, Slack or a customer's chat widget.
- **General "do anything" agents grew faster and proved less stable.** Manus claimed $100M ARR eight months after launch (Dec 2025), sold to Meta for about $2B, was blocked by China's NDRC (Apr 2026), unwound, and bought back by a Tencent-led group (Aug 2026) [1]–[7]. Genspark self-reports $250M+ ARR at a $2.6B valuation (mid-2026), unaudited [10][12].
- **The browser as default surface has a poor record so far.** Comet is free and on iOS but shipped two publicised prompt-injection attacks [20][21]; Dia was bought for $610M and is still a macOS-only beta a year later [23][25]; OpenAI shut Atlas within a year (mid-2026) [26]; Convergence's Proxy was absorbed into Salesforce [30].
- **Messaging-native agents are real but tiny.** Poke: $25M raised at $300M, a 10-person team, about 100M messages relayed, first agent approved on Apple Messages for Business (Jun 2026) [33][34].
- **Approvals converge on one pattern:** auto-run, always ask, or let the agent decide, set per tool, answered in Slack or Teams. Relevance AI, Dust's tool "stakes", n8n's human-in-the-loop nodes, Poke's email-confirmation rule and H Company's kill switch are the same idea in five forms [31][41][44][45][16].
- **Reliability, not interface, is the binding constraint.** Zapier's AutomationBench: the best model passes 50.3% of business tasks (Jul 2026) [38]. Sierra's tau-bench: 22.5% of airline tasks pass four times in a row [67]. ServiceNow shipped a second-order prompt injection in default settings (Nov 2025) and a critical impersonation flaw (Jan 2026) [62][63]. An independent test scored the best of Cowork, Lindy, Sauna and Opal about 1.25 out of 3 (Apr 2026) [36].
- **Hardware harnesses failed on economics and capability, not on the idea.** Humane raised about $240M, sold roughly 10,000 Pins and went to HP for $116M with every device bricked [72]; Rabbit's "Large Action Model" shipped without the agent it promised [71]; Adept's $414M ended in a ~$25M licence and an acqui-hire [69].
- **The developer harness is leaking into non-technical products from the other side.** Salesforce publishes Claude Code skills and an Agent Script DSL for Agentforce [59]; Relevance ships a Claude Code plugin; Zapier ships connectors as Agent Skills plus MCP [39]; Notion ships a CLI "built for coding agents" [53].

## How to read a non-technical harness

Analogy first. A developer harness is a workshop: the tools hang on the wall, you watch each cut, you can pick up any tool yourself. A non-technical harness is a concierge desk: you state an outcome, the desk holds your keys (credentials), asks before spending your money (approvals), does the errand somewhere you cannot see (a cloud sandbox), and reports back in whatever channel you already use.

Precisely, the seven parts map like this:

| Part | Developer harness (CLI) | Non-technical harness |
|---|---|---|
| Loop | Visible turn by turn; steered in the terminal | Hidden; progress cards; pinged when input is needed |
| Tools | Shell, files, MCP the user configures | Pre-wired OAuth integrations (5,000–9,000 apps), a browser, a phone line |
| Context and memory | Repo files, CLAUDE.md, compaction | The app's own data (CRM, docs, inbox); memory often paid or absent |
| Permissions | Allow lists, sandboxes | Per-tool approval modes; "stakes"; approvals routed to Slack/Teams/iMessage |
| Runtime | Local process | Vendor cloud, usually a sandboxed VM per task |
| Surface | Terminal, IDE | Chat widget, CRM, docs app, browser sidebar, text message, device |
| Orchestration | Subagents, scripts | "Workforces", triggers, schedules, agent-to-agent discovery |

```
 surface family                                  who runs the loop        where actions land
 inside a suite (Agentforce, ServiceNow, Notion, Glean)   vendor cloud          SaaS records
 standalone app (Manus, Genspark, Relevance)               cloud VM per task     SaaS APIs, files
 the browser itself (Comet, Dia, Fellou, Proxy)            user's own browser    logged-in sites
 a messaging thread (Poke)                                 vendor cloud + MCP    email, calendar
 a device (Rabbit r1, Humane Pin)                          device + cloud        apps (mostly failed)
```

## 1. General-purpose agents in a cloud computer: Manus, Genspark, Runner H

**Manus** (Butterfly Effect, Beijing 2022, Singapore from mid-2025) launched on March 6, 2025 as an invite-only web and mobile app that gives each task its own cloud virtual machine and shows a live view of it [7]; its docs list Projects, Wide Research, Browser Operator and Mail Manus (Jul 2026) [12]. It runs autonomously and pauses for input. Sandbox code that leaked in March 2025 shows a Docker container exposing terminal, browser and file services behind a token-keyed API proxy, with a modified browser-use library driving Chromium (primary) [8]; OpenManus replicated the design "within 3 hours" and has 58k stars [9]. Traction: Manus's own post of December 17, 2025 claimed $100M ARR eight months after launch (vendor) [1][7]. Ownership: Meta announced a roughly $2B acquisition on December 29, 2025 [2]; China's Ministry of Commerce opened a review on January 9, 2026 [3]; the NDRC prohibited the deal on April 27, 2026 [4]; Meta moved to unwind it in June [5]; a Tencent-led consortium including HongShan bought the company back at the original price in August 2026 [6][7]. Failures: the sandbox leak; dependence on third-party models (Claude Sonnet, The Decoder, Mar 2025) [7]; eight months of ownership limbo.

**Genspark** (MainFunc; ex-Baidu founders) launched Super Agent on April 2, 2025, routing tasks across nine models and 80+ tools [10]: research, documents, slides, video and real phone calls, with minimal approvals by design [11]. Surfaces: a web and mobile workspace (Slides, Sheets, Docs, Inbox, Drive and a browser since January 2026) [10] and GenOffice, an Apache-2.0 desktop office suite with built-in agents released July 31, 2026 (6.1k stars) that signs in "keyless" or takes your own model keys (primary) [13]. Traction: $36M ARR within 45 days (OpenAI case study, Jul 2025) [11]; $100M ARR within nine months; $300M Series B at $1.25B (Jan 2026) [10]; a Reuters-confirmed $100M extension at $2.6B (Jun 2026); self-reported $645M raised, 6,400+ business clients and $250M+ ARR, unaudited [12]. Failures: a disputed 87.8% GAIA claim (leaked validation set); reports of rigidity, accuracy problems, a regression after a GPT-5 switch and no memory as of February 2026 [10].

**H Company's Runner H** (Paris; $220M seed in May 2024, unverified) entered beta in November 2024 [14a]. By 2026 the consumer agent is secondary: the Surfer-H CLI was archived on June 25, 2026 with a note to "use the Models API and Agent API instead" (primary) [14]. What remains is a harness for others to embed: a Computer-Use Agent API whose sessions can be steered, paused, resumed or forced to answer, in a sandboxed browser with saved profiles and a credential vault [15]; open-weight Holo3 models (35B free, 122B paid); and a desktop agent CLI whose main safety control is a kill switch, Escape twice [16]. The runtime binary is closed source [16]. Traction: none public.

## 2. The browser as the surface: Comet, Dia, Fellou, Convergence

**Perplexity Comet** launched July 9, 2025 for $200/month Max subscribers, went free on October 2, 2025, reached Android on November 20, 2025 and iOS in March 2026 (via digests) [17][18][19][22]. A Chromium sidebar assistant reads the page, uses the user's logged-in sessions and runs multi-step tasks, with "Background Assistants" running asynchronously (secondary) [22a]. Perplexity made Claude Opus 4.6 the default browsing model on February 6, 2026 and added a cloud "Computer" agent in a locked sandbox on February 25, 2026 [22]. Approvals: session trust plus a 1Password integration (Sep 2025). Business model: a free browser as distribution for subscriptions [22a]. Traction: no verified user numbers. Failures: Brave disclosed an indirect prompt injection in August 2025 in which hidden page text drove the assistant against the user's accounts (source blocked; unverified) [21]; LayerX's "CometJacking" (Oct 2025) hid a prompt in a link to exfiltrate email and calendar data; Perplexity disputed it, then patched [20][23].

**Dia** (The Browser Company) shipped in 2025 as an "AI-first" successor to Arc: chat with your tabs, briefs of calendar and inbox, and "skills", user-written prompt macros [23]. Atlassian agreed to pay $610M in cash on September 4, 2025 and closed on October 21, 2025, promising "an AI-powered browser that helps move work forward" [24][25]. A year on, Dia is still a macOS-only beta [23], "closer to a consumer AI browser with emerging enterprise aspirations" (late 2025) [23a]. It mostly reads and drafts; no traction is published. Context: OpenAI's ChatGPT Atlas (Oct 2025) was shut down in mid-2026, "didn't even last a year" (The Verge, Jul 10, 2026) [26][23]. Two of 2025's four AI browsers no longer exist as independent products.

**Fellou** launched May 11, 2025 claiming the "world's first agentic browser": "Deep Action" turns a sentence into a multi-step workflow and a "shadow workspace" runs hidden browser windows in parallel (secondary) [28]. Its open-source Eko framework has human-in-the-loop hooks (MIT, v4.0 Nov 2025, about 5k stars) [27]; the org's last activity is March 2026. Funding and users: unverified. **Convergence** (London, 2024, $12M pre-seed) built Proxy, a consumer web agent, released a 3B open-weights "proxy-lite" [29], and was acquired by Salesforce (agreement May 15, 2025, closed June 11, 2025) for Agentforce; its site is gone [30][74][75]. Standalone browser agents were acquired or absorbed within a year.

## 3. Messaging as the surface: Poke

**Poke** (The Interaction Company of California) has no app: you text it over iMessage, SMS or Telegram, and WhatsApp where Meta's policies allow [31][77]. Its leaked system prompt (Sep 2025) says a separate "agent" with "full browser-use capabilities" does the work and that Poke must "get user confirmation before sending, forwarding, or replying to emails" [31]. Tools arrive as MCP servers the user connects in settings (primary template, Sep 2025) [32]. It watches inbox and calendar, nudges proactively and runs "recipes"; pricing is free to start, usage-based for real-time features [77]. Traction: $25M raised at a $300M post-money valuation (TechCrunch, Apr 8, 2026) [33]; on June 4, 2026 Apple approved it as the first AI agent on Messages for Business, at about 100M messages relayed and a 10-person team [34]. Limits: no dashboard, no documented persistent memory, about 20 integrations [77].

## 4. Agent builders for operators: Lindy, Zapier Agents, n8n, Relevance AI, Dust

**Lindy** (2023, Flo Crivello) sells "AI employees": a no-code builder where a trigger (email, Slack, phone call, schedule) starts an agent that acts across "5,000–6,000+" integrations (vendor figure) [36][76]. Pricing is seats plus pooled credits, $29.99 to $199.99 per user per month (Aug 2026); proprietary, no self-hosting [35]. Funding: not verified in this session. In mid-2026 Crivello moved production inference from Anthropic to DeepSeek V4, citing millions in savings (secondary) [76]. Independent test (Apr 4, 2026): black-box execution, unpredictable credit burn, a 2.4/5 Trustpilot rating [36].

**Zapier Agents** launched as Zapier Central in March 2024 and was renamed [37a]. Agents act across 9,000+ apps without predefined steps, metered in "activities": Free 400/month capped at 10 per run; Pro $400/year for 1,500 (Aug 2026) [35][37]. Zapier holds the OAuth connections and logs each step. It also sells the catalogue to developer harnesses: Zapier MCP for Claude Code, Cursor, Codex, Copilot CLI, Claude Cowork, Kiro, Gemini CLI and VS Code, and a "connectors" prototype where each tool is both an Agent Skill and an MCP tool (primary) [39]. Its own AutomationBench (600 tasks across six business functions): Claude Opus 5 passes 50.3%, GPT-5.6 45.8%, Gemini 3.6 Flash 45.0% (Jul 2026) [38]. Traction for Agents: undisclosed.

**n8n** (Berlin, 2019) has 203.8k GitHub stars and weekly releases (2.39.0 on September 8, 2026; primary) [40]. The visual graph is the harness: an AI Agent node with tools and memory, deterministic nodes around it, and human-in-the-loop nodes that post approvals to Slack, Telegram or Gmail, gate individual agent tools (Jan 2026) and record who approved (Jul 2026) [41]. July 2026 added n8n Assistant: "describe an automation in plain language and have n8n Assistant plan, build, test, and iterate on it until it actually runs"; September 2026 added prepaid "gateway credits" for models [41]. Licence: Sustainable Use License, "only for your own internal business purposes" [40]. Pricing: executions, $20 to $800/month (Aug 2026) [35]. Traction: $60M Series B (Mar 2025), $180M Series C led by Accel at $2.5B (Oct 2025); n8n claimed 6x users and 10x revenue in a year (vendor) [42][43].

**Relevance AI** (Sydney) sells an "AI workforce": agents, tools, multi-agent workforces, knowledge, triggers and evals, plus an agent ("Invent") that builds all of these from a chat (primary docs) [44]. Its approvals are the most explicit here: each tool is Auto Run, Approval Required or Let Agent Decide; workforces add escalation rules; approvals can be answered with buttons in Slack; there is an Android app and a Teams integration [44]. Pricing since September 2025 is "Actions" plus pass-through "Vendor Credits" with no markup: Free (200 actions), Pro from $19/month, Team from $234, top-ups $80 per 1,000 actions [44]. It also ships a Claude Code plugin. Funding: about $37M, Series B led by Bessemer (secondary, date unverified).

**Dust** (Paris and San Francisco, 2022) is an MIT-licensed monorepo whose agents sit on company data sources and surface in Slack and Teams (primary) [45]. Its approval model is in code: every MCP tool carries a stake, `never_ask` (read-only), `low`, `medium` or `high` ("destructive or high-impact"), which "map to review/approval expectations"; external MCP tools default to `high` [45]. Business model: per seat with model credits (about $29/user/month, secondary). Traction: $16M Series A (Jun 2024) after $1M ARR; $40M Series B on May 18, 2026 led by Sequoia and Abstract; vendor claims 3,000+ organisations, 300K+ agents, 70% weekly active and zero churn in 2025 [46].

## 5. Agents inside the enterprise suite: Glean, Notion, Agentforce, ServiceNow

**Glean** runs Glean Agents on its enterprise search index. Its developer site routes "Non-technical → Agent Builder, Python developer → LangChain, Multi-language → Direct API", alongside a remote MCP server and a CLI (primary) [50]; approval gates are not documented there. Business model: per seat (unverified). Traction: $150M Series F at $7.2B (Jun 10, 2025) [47]; $200M ARR nine months after $100M, 250M+ agentic actions, 27B+ indexed documents (Jan 27, 2026 post, vendor) [48]; top line above $300M with $765M raised (TechCrunch, May 28, 2026) [49]. Failures: none public.

**Notion** launched Notion Agent with Notion 3.0 on September 18, 2025 [51]. Custom Agents arrived with Notion 3.3 on February 24, 2026: triggers on schedules, Slack messages, database changes and email; Business ($20/seat/month) and Enterprise plans; free beta until May 3, 2026, then $10 per 1,000 credits; 21,000 agents built in beta (secondary, citing Notion's release notes) [52]. On May 13, 2026 Notion added a Developer Platform with hosted "Workers" and a CLI "built for coding agents", and said customers had built over 1 million agents since February [53]; an Agents SDK is in alpha (Aug 2026; primary) [54]. Approvals: workspace permissions; explicit gates not documented in the sources read.

**Salesforce Agentforce** launched in October 2024; Agentforce 3 (Jun 2025) added observability and MCP; Agentforce 360 (Oct 13, 2025) added a natural-language Builder, Agent Script and Voice [58]. Surface: the CRM, Slack, web and voice channels. Approvals: deterministic guardrails in Agent Script and a Testing Center (Nov 2024). Pricing: $2 per conversation at launch, later credits and add-ons (unverified). Traction (earnings transcripts, copies): 6,000 paid deals by Q2 FY26 (Sep 2025) [55]; ARR above $500M, up 330%, 9,500+ paid deals in Q3 FY26 (Dec 3, 2025) [56]; $1.2B, up 205%, with 3.8B "Agentic Work Units" and more than half of bookings from existing customers in Q1 FY27 (May 27, 2026) [57]. Developer harness: Salesforce AI Research's agentforce-adlc (Apr 2026) builds agents from Claude Code skills and an Agent Script DSL with "LLM-driven safety review across the entire lifecycle" (primary) [59]. Its own CRMArena-Pro benchmark (2025, TMLR 2026) omits headline numbers in its README; the roughly 58% single-turn and 35% multi-turn success I recall is unverified [60].

**ServiceNow** shipped AI Agent Studio and Orchestrator in the Yokohama release (Mar 2025), AI Control Tower (May 2025), closed the $2.85B Moveworks acquisition on December 15, 2025, and showed an "Otto" conversational front end at Knowledge 2026 (secondary) [61]. Now Assist is sold as per-seat add-ons with metered "assists", moving toward consumption; net-new Now Assist ACV passed $600M at end-2025, targeting $1B+ in 2026 (earnings call, via secondary) [61]. Failures: AppOmni showed on November 19, 2025 that default settings let one Now Assist agent recruit another through agent-to-agent discovery, a second-order prompt injection: "it isn't a bug in the AI; it's expected behavior as defined by certain default configuration options" [62]; its January 2026 "BodySnatcher" report chained a hard-coded platform secret with email-based account linking to impersonate any user and bypass MFA and SSO; patched [63]. ServiceNow's own WorkArena++ "is not solved" [64].

## 6. Customer-service agents run by non-technical teams: Sierra, Decagon

**Sierra** (2023, Bret Taylor and Clay Bavor) sells customer-service agents over chat and voice; the customer's operations team edits behaviour in a no-code studio and pays per outcome (vendor claims, unverified). Traction: $350M at $10B on September 4, 2025 [65]; $100M ARR in November 2025 and $150M by February 2026 (secondary); $950M Series E at $15B on May 4, 2026, with CNBC citing more than 40% of the Fortune 50 [66]. Reliability: Sierra's own tau-bench (Jun 2024) found the best model then passed 46.0% of airline tasks once and 22.5% four times in a row (primary README) [67].

**Decagon** (2023) has customers write "agent operating procedures" in plain language (vendor, unverified); OpenAI published a case study in October 2024; it completed a tender offer at $4.5B on March 4, 2026 [68]; Sacra estimated about $35M ARR in October 2025 (secondary). Both are the clearest case of a harness operated by non-technical teams: the surface is the customer's chat widget or phone line, the loop and permissions belong to the vendor, and the operator edits policy text.

## 7. Cautionary cases: Adept, Rabbit r1, Humane

**Adept** (2022) raised about $415M at a $1B valuation (Mar 2023) to build agents that operate software (ACT-1). On June 28, 2024 Amazon hired CEO David Luan, four co-founders and much of the team and took a non-exclusive licence; Zach Brock became CEO of what remained [69]. Semafor reported Adept received about $25M and investors would "roughly recoup" (Aug 2, 2024) [69]. Luan now runs Amazon's AGI SF Lab and has defended the "reverse acquihire" (Aug 2025) [70]. Lesson: a harness company with no surface of its own and a frontier-model training bill could not survive independently.

**Rabbit r1** was announced at CES in January 2024 for $199 and shipped in April 2024; The Verge: "nothing to see here" (May 2, 2024) [71]. The promised "Large Action Model" turned out to be scripted automations, and a security group found hard-coded API keys (Jun 2024; unverified). Rabbit later shipped "teach mode" (Nov 2024), a "generalist Android agent" demo, "the AI agent it should have launched with" (The Verge, Feb 19, 2025), and RabbitOS 2 (Sep 2025) (secondary) [71]. Units sold: unverified. Jony Ive: Rabbit and Humane "made bad products" (May 2025) [73].

**Humane AI Pin** cost $699 plus $24/month and shipped in April 2024 on roughly $240M of funding (secondary). Returns outpaced sales within months; about 7,000 units remained with customers by August 2024 (The Verge, via digest) [72]. HP bought the assets, CosmOS and 300+ patents for $116M on February 18, 2025 and the Pins lost cloud features on February 28, 2025 [72]. Lesson: a new surface that does a fraction of a phone at several times the price cannot fund a harness.

## 8. Comparison table

| Product | Surface | End-to-end action | Approvals and trust | Business model | Traction (date, source type) | Notable failure |
|---|---|---|---|---|---|---|
| Manus | Web, mobile | Cloud VM: browse, code, files, slides, email | Autonomous, pauses for input; sandbox per task | Credits | $100M ARR (Dec 2025, vendor); ~$2B deal blocked, bought back (Aug 2026) | Sandbox leak; ownership unwound |
| Genspark | Web, mobile, desktop (GenOffice) | Docs, slides, research, phone calls | Minimal | Credits; Pro $249.99/mo | $250M+ ARR, $2.6B (mid-2026, self-reported) | Disputed GAIA claim; quality regressions |
| Runner H / H Company | API, CLI, desktop CLI | Browser and desktop control | Pause, steer, force-answer; kill switch | API and models (free and paid tiers) | None public | Consumer CLI archived (Jun 2026) |
| Comet | Browser (desktop, Android, iOS) | Acts in logged-in sites; background tasks | Session trust; 1Password | Free; drives subscriptions | No verified users; free since Oct 2025 | Brave injection; CometJacking |
| Dia | Browser (macOS only) | Chat with tabs, briefs, skills | Read/draft mostly | Free beta | $610M acquisition (Oct 2025) | Still beta a year later |
| Fellou | Browser | Deep Action workflows | Site-style permission prompts (secondary) | Unverified | Unverified | Little activity since Mar 2026 |
| Convergence Proxy | Web agent | Web tasks | n/a | Free tier | $12M pre-seed; acquired Jun 2025 | Absorbed; site gone |
| Poke | iMessage, SMS, Telegram, WhatsApp | Email, calendar, browser tasks, recipes | Confirm before sending email; drafts shown | Free plus usage | $25M at $300M; ~100M messages (Jun 2026, via digest) | No dashboard or memory layer |
| Lindy | Web builder | Trigger-driven agents across 5,000+ apps | Per-step confirmations (unverified) | Seats plus credits, $29.99–$199.99 | Funding unverified | Credit burn, 2.4/5 Trustpilot (Apr 2026) |
| Zapier Agents | Web builder | Acts across 9,000+ apps | Zapier-held OAuth; step logs | Activities; Free 400/mo, Pro $400/yr | Undisclosed | Best model 50.3% on own benchmark |
| n8n | Visual editor (self-host or cloud) | Any workflow plus AI Agent node | HITL nodes to Slack/Telegram/Gmail; per-tool gates | Executions; $20–$800/mo | $2.5B (Oct 2025); 203.8k stars | Licence limits hosting |
| Relevance AI | Web builder, Slack, Teams, Android | Agents, workforces, triggers | Three modes per tool; Slack approvals; escalations | Actions plus vendor credits; $19–$234/mo | ~$37M raised (secondary) | None public |
| Dust | Web, Slack, Teams | Agents over company data with MCP tools | Tool stakes never_ask/low/medium/high | Per seat plus credits | $40M Series B (May 2026); 3,000+ orgs (vendor) | None public |
| Glean | Search bar, chat, Agent Builder, MCP | Agents over enterprise index | Not documented | Per seat | $300M+ (May 2026, press); $7.2B (Jun 2025) | None public |
| Notion Agents | Docs app, Slack, email triggers | Create and update pages and databases | Workspace permissions | $20/seat plus $10 per 1,000 credits | 1M+ agents built (May 2026, vendor) | None public |
| Agentforce | CRM, Slack, web and voice channels | Service, sales, custom agents | Agent Script guardrails; Testing Center | $2/conversation, credits, add-ons | $1.2B ARR (May 2026, earnings) | Own benchmark shows sub-60% task success (unverified) |
| ServiceNow | ITSM console, Otto, Moveworks | Workflow agents | AI Control Tower; guardian checks | Per seat plus assists, moving to consumption | $600M+ ACV (end 2025, earnings) | Second-order injection; BodySnatcher |
| Sierra | Customer chat and voice | Resolve support cases | Policy studio; outcome pricing | Per outcome (unverified) | $15B (May 2026); $150M ARR (Feb 2026, secondary) | tau-bench pass^4 22.5% |
| Decagon | Customer chat and voice | Resolve support cases | Plain-language procedures | Undisclosed | $4.5B tender (Mar 2026) | None public |
| Adept | None shipped | Software operation (ACT-1) | n/a | Enterprise pilots | $415M raised; ~$25M licence (2024) | Acqui-hired |
| Rabbit r1 | $199 device | App actions via "LAM" | None | Hardware, no subscription | Units unverified | Scripted "LAM"; poor reviews |
| Humane Pin | $699 wearable plus $24/mo | Voice assistant | None | Hardware plus subscription | ~10,000 units (secondary) | Returns beat sales; bricked Feb 2025 |

## 9. Cross-cutting patterns

- **Approvals are a product, and they moved into chat.** Relevance, n8n, Dust and Poke route approvals to where the human already is (Slack, Teams, iMessage) and share the three-mode pattern (auto, ask, agent decides) [44][45][41]. Browsers are the exception: Comet and Dia rely on the user's session, which is where the injection attacks landed.
- **Consumption is the unit; models are pass-through.** Activities (Zapier), actions (Relevance), executions (n8n), credits (Notion, Manus, Genspark, Lindy), conversations (Agentforce), assists (ServiceNow), outcomes (Sierra). Nobody prices on seats alone.
- **Models are swappable suppliers.** Lindy moved to DeepSeek V4, Comet defaulted to Claude Opus 4.6, Manus ran on Claude Sonnet, Genspark routes across nine models [76][22][7][10].
- **The runtime is a cloud sandbox, not the user's machine.** Manus, Genspark, H Company and Perplexity Computer run a VM or browser per task in the vendor's cloud; the user watches a replay [13].
- **Developer and non-developer harnesses converge from both ends.** Salesforce builds Agentforce agents with Claude Code skills [59]; Notion ships a CLI for coding agents [53]; Zapier's connectors are Agent Skills [39]. The lab document found "one engine, many surfaces"; here it is "many surfaces, borrowed engine".
- **Failures cluster into four shapes:** prompt injection through data the agent reads (Comet, ServiceNow) [20][62]; a reliability ceiling near 50% on realistic business tasks, far lower on repeated runs [38][67]; ownership and regulatory shocks (Manus) [4]; hardware unit economics (Humane, Rabbit) [72][71].

## What this means for the thesis

**Supports.**

- Non-technical people already operate agents, never through a CLI or a desktop agent app. The products with real revenue sit inside the CRM, the ITSM console, the docs app, the search bar and the support widget, as the brief's third pushback predicted.
- The harness is what these vendors sell. They swap models freely and compete on integrations, credentials, approval routing, memory and billing units. When Lindy changes model vendor and keeps its customers, the harness is the product.
- The independent test of Cowork, Lindy, Sauna and Opal failed them on memory, inspectable artifacts and compounding context: harness parts, not model parts [36]. Its author's line is the thesis in one sentence: "Code has a test suite. A strategy memo doesn't compile."

**Contradicts.**

- Reimagined surfaces have the worst business record in this set: two AI browsers gone (Atlas, Proxy), one acquired and stalled (Dia), two devices dead (Humane, Rabbit), one standalone agent bounced between owners (Manus). The winners were unglamorous embeddings. For non-technical users the default surface is "wherever the data already is", not a new body style.
- The bottleneck is reliability and security, and models still move that needle: AutomationBench scores spread by five points across frontier models and the best is 50% [38]. "Harness is the bottleneck" holds for approvals and memory, not for whether the task gets done.
- Desktop is not dead: Genspark's newest surface is an open-source desktop office suite, and Perplexity and Manus ship desktop-shaped "computers", hosted in the cloud.

**Nuance.**

- Two archetypes behave differently. The operator-configured policy harness (Sierra, Decagon, Agentforce, ServiceNow) monetises because a non-technical operator edits policy text while the vendor owns the loop. The user-driven general agent (Manus, Genspark, Comet) grows fast on credits and is unstable. A startup thesis should say which it is building.
- Every vendor rebuilds the same approval, credential and memory layer separately. Nobody owns that layer across surfaces, but the suites (Notion, Salesforce, ServiceNow) bundle it, which limits room for an independent.
- The best evidence for "on the fly" harness generation here is n8n Assistant (Jul 2026) and Relevance's Invent: agents that build the workflow, the tools and the approval gates from a sentence, then run them [41][44].

## Open questions and unverified claims

- H Company's $220M seed (May 2024) and the fate of the consumer Runner H app: training knowledge only.
- Brave's August 2025 Comet disclosure (source blocked), Comet user counts, Dia's launch date and users: unverified.
- Manus's credit tiers and the exact Tencent buyback terms: one secondary source [7].
- Lindy's funding and approval controls; Relevance AI's round size and date; Dust's per-seat price: secondary or missing.
- Sierra's outcome pricing and no-code studio; Decagon's "agent operating procedures" and $1.5B Series C (Jun 2025): training knowledge.
- Agentforce's add-on prices and CRMArena-Pro's headline rates: unverified. One secondary source claims Salesforce agreed to acquire Fin (formerly Intercom) for about $3.6B on June 15, 2026; not corroborated.
- ServiceNow's "Otto" and the Armis and Veza acquisitions: investor memos only. Rabbit's unit sales and API-key incident; Humane's exact funding: secondary or training knowledge.
- OpenAI Atlas's shutdown date (Aug 9, 2026 per one source) rests on two secondary sources and is outside this document's scope.

## Sources

Primary (read directly from GitHub-hosted repositories or docs): 8, 9, 13, 14, 15, 16, 27, 29, 32, 38, 39, 40, 41, 44, 45, 50, 54, 59, 60, 64, 67. Everything else is a press item reproduced in a digest, a secondary write-up, or a vendor page that could not be fetched.

1. Manus, "Manus: $100M ARR, $125M revenue run-rate", https://manus.im/blog/manus-100m-arr, Dec 2025 (not fetched; quoted in [7]).
2. TechCrunch, "Meta just bought Manus", https://techcrunch.com/2025/12/29/meta-just-bought-manus-an-ai-startup-everyone-has-been-talking-about/, Dec 2025 (via digest).
3. AI News, "The Meta-Manus review", https://www.artificialintelligence-news.com/news/meta-manus-ai-vendor-compliance-risk/, Jan 2026 (via digest).
4. CNBC, "China blocks Meta's acquisition of Manus", https://www.cnbc.com/2026/04/27/meta-manus-china-blocks-acquisition-ai-startup.html, Apr 2026 (via digest).
5. TechCrunch, "Meta reportedly moves to unwind $2B Manus deal", https://techcrunch.com/2026/06/13/meta-reportedly-moves-to-unwind-2b-manus-deal-after-beijings-demand/, Jun 2026 (via [7]).
6. CNBC, "Manus to return as independent company", https://www.cnbc.com/2026/08/11/manus-china-meta-acquisition.html, Aug 2026 (via [7]).
7. AIHawk docs, "Manus alternatives", https://github.com/feder-cr/AIHawk/blob/main/docs/manus-alternatives.md, Sep 2026 (secondary).
8. whit3rabbit/manus-open, https://github.com/whit3rabbit/manus-open, Mar–May 2025.
9. FoundationAgents/OpenManus, https://github.com/FoundationAgents/OpenManus, Mar 2025 onward.
10. Research catalogue, "Genspark", https://github.com/razpetel/research-catalogue/blob/main/research/catalogue/2026-02-19-genspark.md, Feb 2026 (secondary).
11. OpenAI, "No-code personal agents" (Genspark case study), https://openai.com/index/genspark, Jul 2025 (via digest); VentureBeat, Jun 2025 (via digest).
12. Field brief on Genspark, https://github.com/yan5xu/oh-my-ai-company/blob/main/bodies/note.agi-playground-2026-a-group-field-brief-2026-08-03.md, Aug 2026 (secondary); Manus feature list via https://github.com/phuetz/code-buddy/blob/main/docs/audits/manus-genspark-gap-2026-07-12.md, Jul 2026.
13. genspark-ai/genoffice, https://github.com/genspark-ai/genoffice, Jul 2026.
14. hcompai/surfer-h-cli (archived), https://github.com/hcompai/surfer-h-cli, Jun 2026. 14a. AI News digest on Runner H beta, https://github.com/smol-ai/ainews-web-2025, Nov 2024.
15. hcompai/hai-agents-python, https://github.com/hcompai/hai-agents-python, Aug 2026.
16. hcompai/holo-desktop-cli, https://github.com/hcompai/holo-desktop-cli, Jul 2026.
17. TechCrunch, "Perplexity launches Comet", https://techcrunch.com/2025/07/09/perplexity-launches-comet-an-ai-powered-web-browser/, Jul 2025 (via digest).
18. The Verge, "Perplexity's Comet browser is now available to everyone for free", https://www.theverge.com/news/790419/perplexity-comet-available-everyone-free, Oct 2025 (via digest).
19. The Verge, "Perplexity brings its Comet browser to Android", https://www.theverge.com/news/825221/perplexity-comet-ai-browser-launch-android, Nov 2025 (via digest).
20. LayerX, "CometJacking", https://layerxsecurity.com/blog/cometjacking-how-one-click-can-turn-perplexitys-comet-ai-browser-against-you/, Oct 2025; The Hacker News, https://thehackernews.com/2025/10/cometjacking-one-click-can-turn.html (via digest).
21. Brave, "Comet prompt injection", https://brave.com/blog/comet-prompt-injection/, Aug 2025 (blocked; unverified).
22. Research note on Comet and Perplexity Computer, https://github.com/coco-research/coco/blob/main/systems/superintelligence/ai/research/aravind-srinivas/06-comet-browser-and-agents.md, May 2026 (secondary). 22a. Comet review, https://github.com/proxnox/proxnox.github.io/blob/main/_posts/2026-01-18-Perplexity-Comet-AI-Browser-Review-and-Specifications.md, Jan 2026 (secondary).
23. AIHawk docs, "AI browser vs AI browser agent", https://github.com/feder-cr/AIHawk/blob/main/docs/ai-browser-vs-ai-browser-agent.md, Sep 2026 (secondary). 23a. Keep-Aware-Security/ownthebrowser, Dia description, late 2025 (secondary).
24. ZDNet, "How Atlassian's $610 million AI browser acquisition puts knowledge workers first", https://www.zdnet.com/article/how-atlassians-610-million-ai-browser-acquisition-puts-knowledge-workers-first/, Sep 2025 (via digest).
25. Atlassian, "Atlassian Completes Acquisition of The Browser Company of New York", https://investors.atlassian.com/news/news-details/2025/Atlassian-Completes-Acquisition-of-The-Browser-Company-of-New-York/default.aspx, Oct 21, 2025 (read from a GitHub copy).
26. The Verge, "OpenAI is shutting down ChatGPT Atlas", https://www.theverge.com/ai-artificial-intelligence/963654/openai-chatgpt-atlas-ai-browser-shut-down-sunset, Jul 2026 (via digest).
27. FellouAI/eko, https://github.com/FellouAI/eko, Nov 2025; org page, Mar 2026.
28. BusinessWire, "Fellou CE launches", https://www.businesswire.com/news/home/20250902953779/en/, Sep 2025 (via secondary list); Fellou launch coverage, May 2025 (secondary).
29. convergence-ai/proxy-lite, https://github.com/convergence-ai/proxy-lite, 2025.
30. Salesforce, "Salesforce signs definitive agreement to acquire Convergence.ai", https://www.salesforce.com/news/stories/salesforce-signs-definitive-agreement-to-acquire-convergence-ai/, May 2025 (via [74]).
31. Poke system prompt (leaked), https://github.com/EliFuzz/awesome-system-prompts/blob/main/leaks/poke/2025-09-15_prompt_guidelines.md, Sep 2025 (secondary).
32. InteractionCo/mcp-server-template, https://github.com/InteractionCo/mcp-server-template, Sep 2025.
33. TechCrunch, "Poke makes AI agents as easy as sending a text", https://techcrunch.com/2026/04/08/poke-makes-ai-agents-as-easy-as-sending-a-text/, Apr 2026 (via digest).
34. TechCrunch, "Apple approves Poke as the first AI agent on its Messages for Business platform", https://techcrunch.com/2026/06/04/apple-approves-poke-as-the-first-ai-agent-on-its-messages-for-business-platform/, Jun 2026 (via digest).
35. Sim, "Best no-code AI agent builders 2026", https://github.com/simstudioai/sim/blob/main/apps/sim/content/library/best-no-code-ai-agent-builders-2026/index.mdx, Aug 2026 (secondary, cites vendor pricing pages).
36. Nate Jones, "I tested Cowork, Lindy, Sauna and Opal against 3 questions", https://github.com/StuartLeitch/nate_jones_articles/blob/main/articles/2026-04/2026-04-04-i-tested-cowork-lindy-sauna-and-opal-against-3-questions-the-best-scored-1-out-of-4.md, Apr 2026 (independent, secondary copy).
37. Zapier Help, "How is Zapier Agents usage measured", https://help.zapier.com/hc/en-us/articles/26559132765325-How-is-Zapier-Agents-usage-measured (not fetched); Zapier pricing via [35]. 37a. skills-il/developer-tools evidence on the Central rename, Aug 2026 (secondary).
38. zapier/AutomationBench, https://github.com/zapier/AutomationBench, Jul 2026.
39. zapier/zapier-mcp and zapier/connectors, https://github.com/zapier/zapier-mcp, https://github.com/zapier/connectors, Jul–Aug 2026.
40. n8n-io/n8n README, LICENSE.md and releases, https://github.com/n8n-io/n8n, Sep 2026.
41. n8n docs changelog, https://github.com/n8n-io/n8n-docs/blob/main/docs/changelog/README.md, Jan–Sep 2026.
42. n8n, "Series C", https://blog.n8n.io/series-c/, Oct 2025 (via HN digest).
43. TechCrunch, "Fair-code pioneer n8n raises $60M", https://techcrunch.com/2025/03/24/fair-code-pioneer-n8n-raises-60m-for-ai-powered-workflow-automation/, Mar 2025 (via digest).
44. Relevance AI docs (pricing, approvals and escalations, tools, changelog), https://github.com/RelevanceAI/relevance-docs, Sep 2026.
45. dust-tt/dust, front/lib/actions/constants.ts and .claude/skills/dust-mcp-server/SKILL.md, https://github.com/dust-tt/dust, Sep 2026.
46. Tech.eu, "Dust raises $40M Series B", https://tech.eu/2026/05/18/dust-raises-40m-series-b-to-build-the-multiplayer-operating-system-for-enterprise-ai/, May 2026 (via digest); TLDR digest on the $16M Series A, Jun 2024.
47. TechCrunch, "Enterprise AI startup Glean lands a $7.2B valuation", https://techcrunch.com/2025/06/10/enterprise-ai-startup-glean-lands-a-7-2b-valuation/, Jun 2025 (via digest).
48. Glean, "$200M ARR" post, Jan 2026, via https://github.com/kzinmr/ai-topics (secondary copy).
49. TechCrunch, "Glean's top line crosses $300M", https://techcrunch.com/2026/05/28/gleans-top-line-crosses-300m-as-ai-budget-cutting-becomes-its-major-selling-point/, May 2026 (via secondary).
50. gleanwork/glean-developer-site, agents overview, https://github.com/gleanwork/glean-developer-site/blob/main/docs/guides/agents/overview.mdx, 2026.
51. TechCrunch, "Notion launches agents for data analysis and task automation", https://techcrunch.com/2025/09/18/notion-launches-agents-for-data-analysis-and-task-automation/, Sep 2025 (via digest).
52. Notion releases, Feb 24, 2026, https://www.notion.com/releases/2026-02-24 (via secondary, https://github.com/jikig-ai/soleur).
53. TechCrunch, "Notion just turned its workspace into a hub for AI agents", https://techcrunch.com/2026/05/13/notion-just-turned-its-workspace-into-a-hub-for-ai-agents/, May 2026 (via secondary).
54. makenotion/notion-agents-sdk-js, https://github.com/makenotion/notion-agents-sdk-js, Aug 2026.
55. Salesforce Q2 FY26 earnings call transcript, Sep 3, 2025 (GitHub copy, https://github.com/bencrowe0/citibank-arp).
56. Salesforce Q3 FY26 earnings, Dec 3, 2025 (same copy).
57. CNBC, "Salesforce Q1 FY27 earnings", https://www.cnbc.com/2026/05/27/salesforce-crm-q1-earnings-report-2027.html, May 2026 (via digest); Salesforce 8-K, May 27, 2026 (GitHub copy).
58. TechCrunch, "Salesforce announces Agentforce 360", https://techcrunch.com/2025/10/13/salesforce-announces-agentforce-360-as-enterprise-ai-competition-heats-up/, Oct 2025 (via digest).
59. SalesforceAIResearch/agentforce-adlc, https://github.com/SalesforceAIResearch/agentforce-adlc, Apr 2026.
60. SalesforceAIResearch/CRMArena, https://github.com/SalesforceAIResearch/CRMArena, 2025–2026.
61. ServiceNow Q4 2025 earnings quotes and acquisition timeline via https://github.com/guzus/ai-research-arm, May 2026 (secondary).
62. The Hacker News, "ServiceNow AI agents can be tricked", https://thehackernews.com/2025/11/servicenow-ai-agents-can-be-tricked.html, Nov 2025; AppOmni, https://appomni.com/ao-labs/ai-agent-to-agent-discovery-prompt-injection (via archived copy).
63. AppOmni, "BodySnatcher", https://appomni.com/ao-labs/bodysnatcher-agentic-ai-security-vulnerability-in-servicenow/, Jan 2026 (via archived copy).
64. ServiceNow/WorkArena, https://github.com/ServiceNow/WorkArena, 2024.
65. TechCrunch, "Bret Taylor's Sierra raises $350M at a $10B valuation", https://techcrunch.com/2025/09/04/bret-taylors-sierra-raises-350m-at-a-10b-valuation/, Sep 2025 (via digest).
66. TechCrunch, "Sierra raises $950M", https://techcrunch.com/2026/05/04/sierra-raises-950m-as-the-race-to-own-enterprise-ai-gets-serious/; CNBC, https://www.cnbc.com/2026/05/04/bret-taylor-sierra-fundraise-openai.html, May 2026 (via digests).
67. sierra-research/tau-bench and tau2-bench, https://github.com/sierra-research/tau-bench, https://github.com/sierra-research/tau2-bench, 2024–2026.
68. TechCrunch, "Decagon completes first tender offer at $4.5B valuation", https://techcrunch.com/2026/03/04/decagon-completes-first-tender-offer-at-4-5b-valuation/, Mar 2026 (via digest).
69. TechCrunch, "Amazon hires founders away from AI startup Adept", https://techcrunch.com/2024/06/28/amazon-hires-founders-away-from-ai-startup-adept/, Jun 2024; Semafor, https://www.semafor.com/article/08/02/2024/investors-in-adept-ai-will-be-paid-back-after-amazon-hires-startups-top-talent, Aug 2024 (via secondary citations).
70. TechCrunch, "Amazon AGI Labs chief defends his reverse acquihire", https://techcrunch.com/2025/08/23/amazon-agi-labs-chief-defends-his-reverse-acquihire/, Aug 2025 (via digest).
71. The Verge, "Rabbit R1 review: nothing to see here", https://www.theverge.com/2024/5/2/24147159/rabbit-r1-review-ai-gadget, May 2024; "Rabbit now lets you teach the R1", Nov 2024; "Rabbit shows off the AI agent it should have launched with", https://www.theverge.com/news/615990/rabbit-ai-agent-demonstration-lam-android-r1, Feb 2025 (via digest).
72. The Verge, "Humane is shutting down the AI Pin and selling its remnants to HP", https://www.theverge.com/news/614883/humane-ai-hp-acquisition-pin-shutdown, Feb 2025; TechCrunch, https://techcrunch.com/2025/02/18/humanes-ai-pin-is-dead-as-hp-buys-startups-assets-for-116m/ (via digest); TLDR digest on returns, Aug 2024.
73. The Verge, "Jony Ive says Rabbit and Humane made bad products", https://www.theverge.com/news/671955/jony-ive-rabbit-r1-humane-ai-pin, May 2025 (via digest).
74. steel-dev/awesome-web-agents ARCHIVE, https://github.com/steel-dev/awesome-web-agents/blob/main/ARCHIVE.md, Aug 2026.
75. api-evangelist/providers, Convergence, https://github.com/api-evangelist/providers/blob/main/_providers/convergence.md, Jul 2026.
76. "Lindy's DeepSeek switch" (mwblog copy), https://github.com/rocksun/mwblog, 2026 (secondary); news roundup Jul 14, 2026, https://github.com/jrob5756/blog.
77. Research note on Poke, https://github.com/theexperiencecompany/gaia/blob/1265832c8e9ed2b5d723c4512ad15a594895e09d/apps/web/src/features/comparisons/data/research/poke.md, May 2026 (secondary).
