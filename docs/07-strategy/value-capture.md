# Where the Money Lands: Value Capture in the Agent Stack

*Research program: agent harnesses. Written 2026-09-08. Status: draft, pending verification.*

## What this document answers

- Which layer of the agent stack (model, harness, surface, distribution, data) is capturing revenue and margin as of September 2026, using the numbers that are public.
- What the labs are doing about harnesses and surfaces: building them, buying them, hiring their authors, and policing who may use lab subscriptions inside them.
- How much platform risk a third-party harness carries, using the OpenClaw episode of 2026 as the case study.
- For four candidate startup positions, a blunt answer to "why would a lab not just do this?"

## TL;DR

- Revenue at the harness layer is real and large: Cursor passed $4B annualized (June 2026), Cognition is reported above $900M (Sept 2026), Lovable $500M (June 2026), Replit about $250M (2026). But the labs' own harnesses grow at least as fast from inside a larger business: Claude Code passed $2.5B run-rate (Feb 2026) and Codex reached 5M weekly users (June 2026).
- Gross margin lands at the model layer. Cursor's gross margin was reported at minus 23% for the quarter ending January 2026 and turned positive only after it shipped its own models and repriced. A harness that rents frontier models resells them at a loss or a thin spread.
- Labs are expanding into every layer: surfaces (Claude Cowork, Jan 2026; the Codex desktop app, Feb 2026; Codex folded into ChatGPT, July 2026), runtime (OpenAI buying Ona, June 2026), tooling (Anthropic buying Bun, Dec 2025) and people (OpenClaw's creator joined OpenAI, Feb 2026). Companies with distribution are buying harnesses too (Meta/Manus, Atlassian/Dia, Cognition/Windsurf).
- Platform risk is not theoretical. Anthropic stopped subscriptions from covering third-party harnesses on 4 April 2026, partially reinstated them in May behind a separate metered pool, paused a further metering change on 15 June, and by September was rejecting requests that carried OpenClaw's own prompt markers. OpenAI took the opposite public stance while enforcing unpublished caps.
- Pricing is converging on a hybrid: a subscription that looks like a seat, metered credits underneath, and outcome pricing only where the outcome can be counted (support resolutions). No general-purpose harness has made outcome pricing work.
- Enterprises buy governance and a vendor they already have paper with, not a better loop. Spend is concentrated in coding, buyers are increasingly line-of-business, multi-model is the norm, and agent security is the stated blocker.
- For the thesis: the evidence supports "the harness is where the product is" and contradicts "an independent harness captures the value." The defensible startup positions own distribution, data or a countable outcome. The harness itself is being squeezed from above (labs) and below (open source).

## 1. The stack, and where the meter sits

Analogy first. Think of a ride-hailing business. The model is the driver's skill, rented by the hour from an agency. The harness is the car, the dispatch system and the insurance. The surface is the app the rider taps. Distribution is being pre-installed on every phone. Data is the map and the riders' habits. The agency sets the hourly rate, can raise it, can refuse to supply drivers to a rival app, and can launch its own app. That is the position of an independent harness company in 2026.

Precisely: a harness company buys tokens from a model lab (its cost of goods), wraps them in the seven harness parts (loop, tools, context, permissions, runtime, surface, orchestration) and sells the result as a subscription or metered plan. Its gross margin is the gap between what customers pay and what the lab charges. "The meter" in this document means whoever decides what a unit of work costs and who is allowed to consume it.

```
layer         who holds it (Sept 2026)                          the meter
------------  ------------------------------------------------  ------------------------------
distribution  OS, browser, IDE and chat incumbents:             bundling, defaults, app review
              Microsoft/GitHub, Atlassian (Dia), ChatGPT
surface       labs (Claude app, Cowork, ChatGPT, Codex app),    subscription price
              IDEs (Cursor), app builders (Lovable, Replit)
harness       labs (Claude Code, Codex, Agent SDK),             seats, credits, overage
              independents (Cursor, Cognition, Manus),
              open source (OpenClaw, OpenHands)
model         Anthropic, OpenAI, Google, xAI, open weights      $/token, plan quotas, terms
data          whoever holds the repos, tickets, customers       switching cost
```

Each layer can reach up or down. The evidence below shows that in 2025 to 2026 the model layer reached up into harness and surface far faster than harness companies reached down into models.

## 2. Who is getting paid

All figures are company-disclosed run-rates or press reports, not audited revenue. "Run-rate" means the latest month's revenue times twelve.

| Company (layer) | Latest figure | Trajectory | Source type |
|---|---|---|---|
| Cursor (IDE harness) | >$4B annualized, early June 2026; ~75% enterprise | $2B Feb 2026, $3B late Apr, $4B Jun; company forecasts >$6B by end 2026 | Dealroom note [1], TechCrunch [2] |
| Cognition (Devin + Windsurf) | $492M ARR May 2026; reported >$900M Sept 2026 | Devin $73M Jun 2025; Windsurf ~$82M at acquisition Jul 2025; valuation $10.2B Sep 2025, $26B May 2026, ~$47B in talks Sep 2026 | Sacra [3], CNBC [4], blog citing Bloomberg [5] (Sept figures unverified) |
| Lovable (app builder, non-technical users) | $500M annualized, 9 June 2026; "1 million new projects a week" | $400M Feb 2026; ~$200M late 2025 | TechCrunch [6] |
| Replit (app builder) | ~$250M ARR (2026) | $2.5M to $250M in roughly a year; raised $400M at $9B; CEO targets $1B by end 2026 | Replit blog [7], StartupRiders [8] (secondary) |
| Claude Code (lab harness) | >$2.5B run-rate, 12 Feb 2026 | $1B Nov 2025, six months after GA; weekly actives doubled since 1 Jan 2026; business subscriptions quadrupled | Anthropic [9], [10] (vendor claims) |
| Codex (lab harness) | >5M weekly users, 2 June 2026, ~20% knowledge workers | ~2M weekly Mar 2026 (reported); 7M to 8M mid-July after merging into the ChatGPT desktop app; "20M active" 21 Aug (window unspecified) | OpenAI [11], Unite.AI [12], Gradually [13] |
| GitHub Copilot (bundled harness) | 4.7M paid subscribers, Jan 2026 earnings call (reported); 20M all-time users Jul 2025 | FT (May 2026) reported at least $550M annual revenue | Secondary stats sites [14], TechCrunch [15] |
| Manus (general agent, acquired) | $100M ARR within eight months of launch (reported); sold to Meta for >$2B, Dec 2025 | | CNBC [16], TechCrunch [17] |

Three things stand out. First, independent harnesses are earning billions; the "wrapper" dismissal is empirically wrong about revenue. Second, the two lab harnesses launched in 2025 and are already at the scale of the largest independent, with distribution the independents do not have: Codex is included in every paid ChatGPT plan [11], [31]. Third, the independents' growth is inseparable from the labs' models: Cursor, Lovable and Replit all resell Anthropic, OpenAI and Google models.

## 3. Margin: the model layer eats the harness's gross margin

Revenue is not value capture. According to The Information, as relayed by secondary analyses, Cursor's gross margin was negative 23% in the quarter ending January 2026, when it was approaching $2B annualized; estimates put inference at 40 to 70 cents of every revenue dollar [18], [19]. The same reports say margins turned slightly positive by April 2026 as Cursor routed traffic to its own Composer models and repriced, with enterprise accounts profitable and individual plans still loss-making [18]. Treat all of this as unverified: The Information is paywalled and the relaying sources are blogs.

The response was vertical integration. Cursor shipped Composer in October 2025, Composer 2 in March 2026 (reported to be built on a Kimi K2.5 base) and Composer 2.5 in May 2026, which Cursor says matches frontier models on coding benchmarks at roughly a tenth of the cost per token [20] (vendor claim via a developer blog). In June 2026 it announced a frontier model trained on SpaceX's supercomputer and Origin, its own git hosting platform built from its December 2025 acquisition of Graphite [21], [22]. The lesson: the only independent harness with any margin disclosure had to become a model company to get its gross margin above zero.

The labs face the same arithmetic in reverse. Anthropic's stated reason for the April 2026 subscription crackdown was that third-party harnesses caused capacity and service issues [23]. Flat subscriptions used by always-on agents lose money for the lab too. Peter Steinberger's 100 Codex agents consumed $1.3M of OpenAI tokens in 30 days, which OpenAI absorbed as research spending [24]. Tokens are the cost of goods for everyone, and only the layer that makes them has a structural margin.

## 4. Lab behaviour: build, buy, hire, police

| Date | Move | Harness part | Read |
|---|---|---|---|
| Jul 2025 | Google pays ~$2.4B to license Windsurf and hire its CEO; Cognition buys the rest days later [4], [25] | surface (IDE), harness | Labs pay for people and distribution, not code |
| Sep 2025 | Atlassian buys The Browser Company (Dia) for $610M cash, to "deliver the browser for knowledge work in the AI era" [26] | surface (browser) | An incumbent with data buys a surface |
| Oct 2025 | OpenAI launches apps in ChatGPT and the Apps SDK; third-party submissions reviewed from Dec 2025; selling digital goods "not yet allowed" [27], [28] | surface, distribution (store) | The lab becomes the store |
| Dec 2025 | Anthropic buys Bun, its first acquisition, as Claude Code passes $1B [10] | runtime | The lab owns the toolchain under its harness |
| Dec 2025 | Meta buys Manus for >$2B [16] | whole harness, surface | A distribution owner buys a finished agent |
| Jan 2026 | Anthropic launches Claude Cowork: Claude Code's architecture in the desktop app for non-technical users; Windows Feb 2026; later on all paid plans plus web and mobile (reported) [29], [30] | surface (desktop, web, mobile) | The lab ships the non-technical surface itself |
| Feb 2026 | OpenAI ships the Codex desktop app for macOS, a "command center" for parallel agents; Windows Mar 2026 [31] | surface, orchestration | The lab ships the orchestration surface |
| Feb 2026 | OpenClaw's creator joins OpenAI; OpenClaw moves to a foundation [32], [33] | people | Labs hire harness authors |
| Mar 2026 | OpenAI buys Astral and Promptfoo; six acquisitions by June 2026 [34] | tools, evals | Developer tooling consolidates under labs |
| Jun 2026 | OpenAI agrees to buy Ona (Gitpod): cloud sandboxes that keep agents running after the laptop closes [35], [36] | runtime | The lab owns durable execution |
| Jun 2026 | Microsoft's Copilot Cowork reaches general availability with metered billing (reported) [37] | surface (Office) | The distribution incumbent copies the lab's surface |
| Jul 2026 | Codex merged into the ChatGPT desktop app; ChatGPT Work agent launched (reported) [12] | surface, distribution | The harness collapses into the chat app |

Pattern: within twelve months the labs covered surface, runtime, orchestration and tooling, and the buyers of independent harnesses were companies with distribution (Meta, Atlassian, Google), not other harness companies. The exception, Cognition buying Windsurf, bought a seat-based IDE business and its enterprise customers.

## 5. Platform-risk case study: OpenClaw against the meter

Analogy: OpenClaw is a third-party taxi app that lets riders use their existing monthly pass from the driver agency. The agency noticed its passes were being used for 24-hour shifts.

OpenClaw is an open-source personal agent (MIT licence) that runs on the user's own machine and routes to whichever model the user has credentials for, including Claude and ChatGPT subscriptions via their login tokens (the "OAuth token" a subscription hands to an app). It became the most-starred repository on GitHub within five months of release; the repo showed 389k stars and 82k forks on 8 September 2026, is stewarded by a 501(c)(3) foundation, and has "no paid tier or hosted service" [33], [38]. Press estimated 135,000+ active instances at the time of the April crackdown [39] (unverified).

| Date | Event | Source |
|---|---|---|
| 9 Jan 2026 | Server-side checks reject Claude subscription tokens outside Claude Code (reported) | MindStudio [40]; not verified against Anthropic |
| 20 Feb 2026 | Anthropic terms updated to prohibit subscription tokens in third-party tools (reported) | [40], [41]; unverified |
| 4 Apr 2026, 12:00 PT | Enforcement: Claude subscriptions "no longer cover usage through third-party tools"; OpenClaw first, other harnesses "in the coming weeks"; users may enable pay-as-you-go "extra usage" at API rates; one-time credits of $20 to $200 by plan | ccleaks [42], TNW [39], Hacker News [43], claude-mem issue [70] |
| 12 Apr 2026 | Requests containing the literal string `openclaw.inbound_meta.v1` are rejected with "You're out of extra usage"; renaming the marker to `v2` bypasses the filter | OpenClaw issue #65399 [44] |
| ~13 May 2026 | Anthropic reinstates third-party agents on subscriptions via a separate "Agent SDK" credit pool at API rates; Conductor and OpenClaw named | VentureBeat [23] (date from a mirror) |
| 15 Jun 2026 | Anthropic pauses the planned move of Agent SDK, `claude -p` and third-party usage to a separate monthly credit; such usage draws on plan limits again | OpenClaw docs [45], DigitalApplied [46] |
| 13 Jul 2026 | API error text: "Third-party apps now draw from your extra usage, not your plan limits." | issue #106839 [47] |
| 26 Aug 2026 | After OpenClaw moved its Claude transport onto Anthropic's official Agent SDK, Max subscribers were intermittently billed as third-party extra usage | issue #129765 [48] |
| 3 Sep 2026 | Detection extended to `openclaw.inbound_meta.v2` and `openclaw.diagnostics.v1`; all Anthropic models unusable for subscription users without extra-usage credit | issue #137413 [49] |

The diagram below is the billing question every third-party harness now has to answer on each request.

```
user's Claude subscription --> first-party client (Claude Code, Cowork) --> plan quota
                           \-> third-party harness --> server-side classifier
                                                       |-- looks first-party: plan quota
                                                       '-- looks third-party: "extra usage"
                                                           (API rates, or a 400 if none)
API key -------------------> any harness -------------------------------> pay per token
```

OpenAI took the opposite public position: Sam Altman said on 1 May 2026 that OpenClaw is available under paid ChatGPT plans [50] (reported), and OpenClaw routes through Codex's login. But OpenClaw users report "unpublished 5-hour/weekly subscription caps", 429 errors when several agents share one subscription, and cooldowns that persisted for 161 hours [51], [52]. One user report suggests Anthropic gates its newest model on subscription tokens by the Claude Code client version presented, so a third-party harness must impersonate a newer first-party client to reach it [53] (user hypothesis, unconfirmed).

What this shows, bluntly:

1. A harness built on someone else's consumer subscription is not a business. It is an arbitrage the lab can close with a server-side rule, and did.
2. Even API-key harnesses depend on the lab's classifier, model gating and terms, all of which changed at least five times in eight months.
3. Both labs used the episode to promote their own harness surface (Agent SDK, Codex) as the sanctioned path.
4. The most popular open harness could not monetise any of its 389k stars: the foundation has no paid tier, and its creator now works for a lab.

## 6. Pricing models

Analogy: a gym sells memberships because most members do not show up. An agent is a member who never leaves the treadmill.

| Model | Examples | Who carries inference risk | How it is going |
|---|---|---|---|
| Flat subscription (seat-like) | Claude Pro $20, Max $100 or $200, Team $25 to $30 per user [30]; Codex inside ChatGPT plans [31] | vendor; only viable for the lab with the cheapest tokens | Labs added weekly caps and "extra usage" overage; Anthropic's April 2026 crackdown was a cost measure [23] |
| Seat plus credits | Cursor (credits since its June 2025 pricing change), Windsurf seats, Microsoft Copilot Cowork metered credits (reported) [37] | shared; the customer watches credits burn | Cursor's negative margin at $2B ARR [18]; hybrid pricing is now the most common SaaS model per pricing surveys [54] (secondary) |
| Pure usage | API keys; Devin's compute units [3]; Replit's effort-based pricing [8] | customer | Enterprises accept it for agents they run in bulk; consumers dislike it |
| Outcome | Intercom Fin: $0.99 per resolved conversation, $9.99 per qualified lead [55]; Sierra [56] | vendor | Works only where an outcome is a countable event; Intercom's help centre spends paragraphs defining when a resolution counts [55] |

Per-seat pricing is structurally wrong for agents: the better the agent, the fewer seats the customer needs. Outcome pricing fixes that, but only for narrow, countable work. A general-purpose harness has no countable outcome, so it ends up on seat-plus-credits, competing with a lab that sells the same tokens flat.

## 7. Enterprise procurement realities

- Spend is concentrated and coding leads. Menlo Ventures estimated $37B of enterprise generative-AI spend in 2025, up from $11.5B; coding tools were about $4B, 55% of departmental spend and the largest single category [57] (survey of about 500 US decision-makers, Dec 2025). Menlo partner Deedy Das: "it may be hard for anyone to catch Anthropic. They've dominated coding for 18 months straight" [57].
- Model share is contested and multi-vendor. Menlo's mid-2025 update put Anthropic at 32% of enterprise LLM API usage, OpenAI at 25% and Google at 20%, and found only 16% of enterprise deployments were "true agents" rather than fixed workflows [58]. a16z's 2025 CIO survey found procurement "now mirrors traditional software buying", with more rigorous evaluations and LLM budgets expected to grow about 75% [59]. Claims that line-of-business leaders are now the largest buyer group (46%) and that 81% of enterprises run three or more model families come from a secondary summary and are unverified here [60].
- Security is the stated blocker. Bessemer calls securing agents "the defining cybersecurity challenge of 2026" [61]. That is what a permissions-and-blast-radius story sells into.
- Read for a harness startup: enterprises want a model-neutral control plane (multi-vendor is real), governance (audit, permissions, cost visibility) and a vendor they can already buy from. OpenHands' commercial pitch, an enterprise control plane with scheduled automations, role management and cost dashboards [62], is exactly that, and it competes with GitHub, Microsoft and the labs' enterprise tiers.

## 8. What investors are saying

| Firm | Claim | Date | Note |
|---|---|---|---|
| a16z | The model "might be" the commodity layer while value accrues to those "effectively building the harness"; multi-agent collaboration becomes the moat via switching costs [63] | Dec 2025 and 2026 | Includes an a16z crypto piece; commentary, not data |
| Sequoia | "2026: This is AGI": long-horizon agents are the capability; 2023 to 2024 apps were "talkers", 2026 to 2027 apps are "doers"; founder questions reportedly include "are you obsessively improving your agent harness?" and "how will you price and package outcomes?" [64] | early 2026 (month unverified) | An explicit bet on the application layer |
| Menlo | Coding is the "gateway" category; startups building agentic infrastructure will become "$10B-plus platforms" [57], [58] | Jul and Dec 2025 | Menlo invests in Anthropic and OpenHands; discount accordingly |
| Bessemer | The browser is "the most capable interface layer for agentic AI"; AI-native browsers from labs and Google will push what agents can do [65] | Aug 2025 | Consistent with Atlassian/Dia and Anthropic's Chrome work |

All four say the harness matters. None says an independent, general-purpose harness captures the value; each points to data, workflow, collaboration or a vertical.

## 9. The "wrapper" debate, briefly

"Wrapper" is the claim that a product adds nothing a user could not get by pasting the same prompt into ChatGPT. The 2026 version of the debate concedes that Cursor, Lovable and Cognition started as wrappers and now earn billions, and moves the goalposts to "GTM is the new moat" [66]. The useful test is not the label but the two questions this document keeps hitting: who holds the meter, and what does the customer lose by switching? Cursor answered by owning models and a git forge. Lovable answered with distribution to non-developers and a million projects a week. OpenClaw, with the most stars on GitHub, had no answer.

## 10. Analogies from previous platform shifts

- Browsers. Netscape had the better product and the early share; Microsoft bundled Internet Explorer with Windows; the antitrust remedy arrived years after the market had moved [67]. Mapping: the lab's chat app is Windows, the first-party harness is IE, and Codex being folded into ChatGPT in July 2026 is the bundling. Distribution beat product then; nothing in section 2 suggests it will not now.
- App stores. Apple set the 30% commission and the review rules; developers thrived only in the lanes the platform allowed, and the anti-steering dispute ran from a 2020 lawsuit to a 2025 contempt ruling [68]. Mapping: apps in ChatGPT are reviewed before listing and could not sell digital goods at launch [28]; the token price and the plan quota are the commission.
- Where the analogy breaks, in the harness startup's favour: there are at least four frontier vendors plus open weights, switching between them is a configuration change, and enterprises already run several. No single lab is iOS. That is the structural reason a model-neutral harness can exist at all.

## 11. "Why would a lab not just do this?"

**Open harness runtime (an OpenClaw- or OpenHands-shaped product).** The labs already did the first-party version: Claude Code, the Agent SDK, Codex CLI. What they will not do is the model-neutral version, because neutrality is against their interest. That gap is real but poorly monetised: OpenClaw is a foundation with no revenue [38]; OpenHands has raised about $24M and sells an enterprise control plane [62], [69]. Revenue here means becoming the enterprise's governance layer, which puts you against GitHub, Microsoft and the labs' enterprise tiers, and the OpenClaw timeline shows the lab can reclassify your traffic at will. Verdict: viable as open-source infrastructure; weak as a venture-scale business unless it owns the control plane and the API keys.

**Non-technical surface.** The labs are doing exactly this, and so is Microsoft: Cowork (Jan 2026), Codex inside ChatGPT and ChatGPT Work (Jul 2026), Copilot Cowork (Jun 2026). Lovable is the counter-example, $500M annualized on a non-technical surface, but its margin exposure is Cursor's from 2025 and its models are the labs'. A startup wins here only with distribution the labs lack (a vertical, an existing software estate, a channel such as messaging) or a data asset. Verdict: the surface alone is the most exposed position in this document.

**Orchestration of many harnesses (Conductor, Vibe Kanban and similar).** Both labs' desktop apps already run parallel agents; the Codex app launched as a "command center" for exactly that [31]. Cross-vendor orchestration survives only while enterprises stay multi-model and labs keep blocking each other's subscriptions, which ironically favours an orchestrator that holds API keys for all of them. Margins are thin because you resell everyone's tokens, and the natural end state is acquisition, as with Ona to OpenAI and Graphite to Cursor. Verdict: a feature or an exit, not a platform.

**Vertical harness (a Fin-, Sierra- or Harvey-shaped product).** This is the one position the labs are structurally unlikely to take: they do not want to own two hundred verticals' workflows, compliance and sales teams, and Menlo's data says coding, the one vertical the labs did take, is where the money currently is [57]. Vertical harnesses can price outcomes because the outcome is countable [55]. The risks are that the lab's general agent plus a store (apps in ChatGPT, MCP) absorbs the vertical's tools, and that the harness parts themselves are commodity; the value is in the data, the integrations and the metering. Verdict: the most defensible position, and the least "harness-shaped".

## What this means for the thesis

**Supports.** The harness is where product decisions are made and where the labs are spending: acquisitions, desktop apps, a non-technical surface, a runtime purchase, a hire of the most popular harness author. Sequoia and a16z say the same. The thesis's second clause, that neither a CLI nor a desktop app is the end state, is consistent with the labs' own moves: Anthropic went from a CLI (2025) to a desktop app (Jan 2026) to web and mobile (mid-2026), and OpenAI folded its desktop app into the chat app.

**Contradicts.** The thesis implies that a reimagined harness is a startup-sized opportunity. The value-capture evidence says the opposite for a general-purpose harness: the meter sits with the lab; the only independent with margin data had to build models; the most popular open harness earns nothing and was policed by server-side classifiers; and the non-technical surface is already shipped by two labs and Microsoft. "Better models make the harness the bottleneck" is true for product quality and false for economics: better and cheaper models make a harness easier to copy, not harder.

**Nuance.** Multi-vendor reality, the labs' mutual blocking, and enterprise demand for governance leave a durable niche for model-neutral control planes. Distribution owners (Atlassian, Meta, Microsoft, Google) are paying real money for harness and surface companies, so "build to be bought by a distributor" is a legitimate plan. The thesis reads best as a design thesis, not a business thesis: the field's best products will be reimagined harnesses, but the companies that capture their value will be the ones that also hold the meter, the data or the channel.

## Open questions and unverified claims

1. Cursor's negative 23% gross margin and its recovery to positive margin are attributed to The Information via secondary blogs; not verified against the original.
2. Cognition's ">$900M ARR" and "~$47B" valuation (Sept 2026) come from a blog citing Bloomberg; not verified.
3. The 9 January and 20 February 2026 steps in Anthropic's policy timeline are from secondary sources; Anthropic's support pages could not be fetched from this environment.
4. The ~13 May 2026 reinstatement date is inferred from a mirrored copy of the VentureBeat article.
5. Microsoft Copilot Cowork's GA date, billing model and any relationship to Anthropic's Cowork are from trade-press summaries; not fetched.
6. The claim that Composer 2 was built on a Kimi K2.5 base is from a developer blog; unverified.
7. OpenAI's policy on Codex logins in third-party tools rests on a reported Altman statement (1 May 2026), not on published terms.
8. "LOB leaders are 46% of decision-makers" and "81% run three or more model families" could not be traced to a primary a16z page.
9. Replit's ARR (~$250M) and the timing of its $400M raise rest on secondary posts.
10. Whether Anthropic's classifier deliberately targets OpenClaw marker strings or applies a generic third-party heuristic that happens to match them is unknown; the GitHub reports document behaviour, not intent.
11. Not covered: Google's harness economics (Gemini CLI, Jules, Antigravity), xAI, Chinese harness vendors, Anthropic Managed Agents' pricing, and any audited financials.

## Sources

Fetched directly from this environment: GitHub items [38], [44], [45], [47] to [49], [51] to [53], [62], [70]. All other pages were located by web search but could not be fetched (network egress restrictions), so their figures come from search-result summaries and should be re-verified against the page.

1. Dealroom, "Cursor tops $4B annualized revenue", https://app.dealroom.co/news/note/cursor-tops-4b-annualized-revenue-june-2026 (Jun 2026)
2. TechCrunch, "Sources: Cursor in talks to raise $2B+ at $50B valuation as enterprise growth surges", https://techcrunch.com/2026/04/17/sources-cursor-in-talks-to-raise-2b-at-50b-valuation-as-enterprise-growth-surges/ (Apr 2026)
3. Sacra, "Cognition revenue, valuation & funding", https://sacra.com/c/cognition/ (2026)
4. CNBC, "Cognition to buy AI startup Windsurf days after Google poached CEO in $2.4 billion licensing deal", https://www.cnbc.com/2025/07/14/cognition-to-buy-ai-startup-windsurf-days-after-google-poached-ceo.html (Jul 2025); CNBC, "Cognition valued at $10.2 billion two months after Windsurf purchase", https://www.cnbc.com/2025/09/08/cognition-valued-at-10point2-billion-two-months-after-windsurf-.html (Sep 2025)
5. Value Add VC, "$47B Valuation: How Cognition (Devin) Makes Money", https://valueaddvc.com/blog/how-does-cognition-make-money-devin-pricing-windsurf-enterprise-and-the-492m-arr-breakdown (Sep 2026)
6. TechCrunch, "Lovable says it has hit $500M in annualized revenue, with 1 million new projects a week", https://techcrunch.com/2026/06/09/lovable-says-it-has-hit-500m-in-annualized-revenue-with-1-million-new-projects-a-week/ (Jun 2026)
7. Replit, "The Future is Actually Very Human", https://replit.com/blog/replit-raises-400-million-dollars (2026)
8. StartupRiders, "Replit's Growth Playbook: $2.5M to $250M ARR in 12 Months", https://www.startupriders.com/p/replit-growth-playbook (2026)
9. Anthropic, "Anthropic raises $30 billion in Series G funding at $380 billion post-money valuation", https://www.anthropic.com/news/anthropic-raises-30-billion-series-g-funding-380-billion-post-money-valuation (Feb 2026)
10. Anthropic, "Anthropic acquires Bun as Claude Code reaches $1B milestone", https://anthropic.com/news/anthropic-acquires-bun-as-claude-code-reaches-usd1b-milestone (Dec 2025)
11. OpenAI, "Codex is becoming a productivity tool for everyone", https://openai.com/index/codex-for-knowledge-work/ (Jun 2026)
12. Unite.AI, "OpenAI Says Codex and ChatGPT Work Hit 10 Million Users", https://www.unite.ai/openai-says-codex-and-chatgpt-work-hit-10-million-users/ (Jul 2026)
13. Gradually, "OpenAI Codex Statistics 2026", https://www.gradually.ai/en/codex-statistics/ (2026)
14. Panto, "GitHub Copilot Statistics 2026", https://www.getpanto.ai/blog/github-copilot-statistics (2026)
15. TechCrunch, "GitHub Copilot crosses 20 million all-time users", https://techcrunch.com/2025/07/30/github-copilot-crosses-20-million-all-time-users/ (Jul 2025)
16. CNBC, "Meta acquires intelligent agent firm Manus, capping year of aggressive AI moves", https://www.cnbc.com/2025/12/30/meta-acquires-singapore-ai-agent-firm-manus-china-butterfly-effect-monicai.html (Dec 2025)
17. TechCrunch, "Meta just bought Manus, an AI startup everyone has been talking about", https://techcrunch.com/2025/12/29/meta-just-bought-manus-an-ai-startup-everyone-has-been-talking-about (Dec 2025)
18. Second Order Labs, "The 80% Gross Margin That Built SaaS Is Gone", https://secondorderlabs.com/articles/tech-journalism/ai-inference-costs-permanently-break-the-gross-margin-assumption-that-built-the-saas-valuation-model/ (2026)
19. SaaS Mag, "The AI COGS Problem: SaaS Gross Margin Compression 2026", https://www.saasmag.com/ai-cogs-saas-gross-margin-compression/ (2026)
20. Developers Digest, "Cursor Composer 2.5 Developer Guide 2026", https://www.developersdigest.tech/blog/cursor-composer-2-5-developer-guide-2026 (May 2026)
21. VentureBeat, "Cursor launches Origin code hosting platform as GitHub outage exposes opening in AI coding race", https://venturebeat.com/infrastructure/cursor-launches-origin-code-hosting-platform-as-github-outage-exposes-opening-in-ai-coding-race (Jun 2026)
22. Learn Cursor, "Cursor Compile 2026 Recap: Origin, Mobile & the New Model", https://www.learncursor.dev/research/cursor-compile-2026-announcements (Jun 2026)
23. VentureBeat, "Anthropic reinstates OpenClaw and third-party agent usage on Claude subscriptions, with a catch", https://venturebeat.com/technology/anthropic-reinstates-openclaw-and-third-party-agent-usage-on-claude-subscriptions-with-a-catch (May 2026)
24. Tom's Hardware, "OpenClaw creator burned through $1.3 million in OpenAI API tokens in a single month", https://www.tomshardware.com/tech-industry/artificial-intelligence/openclaw-creator-burns-through-1-3-million-in-openai-api-tokens-in-a-single-month (2026)
25. Cognition, "Cognition's acquisition of Windsurf", https://cognition.com/blog/windsurf (Jul 2025)
26. Business Wire, "Atlassian Enters Into Definitive Agreement to Acquire The Browser Company of New York", https://www.businesswire.com/news/home/20250904645125/en/Atlassian-Enters-Into-Definitive-Agreement-to-Acquire-The-Browser-Company-of-New-York (Sep 2025)
27. OpenAI, "Introducing apps in ChatGPT and the new Apps SDK", https://openai.com/index/introducing-apps-in-chatgpt/ (Oct 2025)
28. VentureBeat, "OpenAI now accepting ChatGPT app submissions from third-party devs, launches App Directory", https://venturebeat.com/technology/openai-now-accepting-chatgpt-app-submissions-from-third-party-devs-launches (Dec 2025)
29. ADTmag, "Anthropic Expands Claude's 'Computer Agent' Tools Beyond Developers with Cowork Research Preview", https://adtmag.com/articles/2026/01/20/anthropic-expands-claude-computer-agent-with-cowork.aspx (Jan 2026)
30. The AI Career Lab, "Claude Cowork on Windows (2026)", https://theaicareerlab.com/blog/claude-cowork-on-windows-download-setup-2026 (2026), secondary, for the Windows date and plan pricing
31. OpenAI, "Introducing the Codex app", https://openai.com/index/introducing-the-codex-app/ (Feb 2026); VentureBeat, "OpenAI launches a Codex desktop app for macOS to run multiple AI coding agents in parallel", https://venturebeat.com/orchestration/openai-launches-a-codex-desktop-app-for-macos-to-run-multiple-ai-coding (Feb 2026)
32. Peter Steinberger, "OpenClaw, OpenAI and the future", https://steipete.me/posts/2026/openclaw (Feb 2026)
33. Y Combinator on X, announcing Steinberger as a Startup School speaker (346k+ stars, most-starred repo, now at OpenAI), https://x.com/ycombinator/status/2062942526856941994 (2026)
34. Crunchbase News, "OpenAI Has Already Done Nearly As Many M&A Deals In 2026 As It Did All of Last Year", https://news.crunchbase.com/ma/data-openai-2023-2026-acquisitions-open-source-astral-promptfoo/ (2026)
35. OpenAI, "OpenAI to acquire Ona", https://openai.com/index/openai-to-acquire-ona/ (Jun 2026)
36. CNBC, "OpenAI to acquire Ona to support its AI coding assistant, Codex", https://www.cnbc.com/2026/06/11/open-ai-ona-acquisition-codex.html (Jun 2026)
37. Neowin, "Microsoft's Copilot Cowork now generally available with usage-based billing", https://www.neowin.net/news/microsofts-copilot-cowork-now-generally-available-with-usage-based-billing/ (Jun 2026)
38. OpenClaw repository, https://github.com/openclaw/openclaw (fetched 8 Sep 2026)
39. TNW, "Anthropic blocks OpenClaw from Claude subscriptions in cost crackdown", https://thenextweb.com/news/anthropic-openclaw-claude-subscription-ban-cost (Apr 2026)
40. MindStudio, "What Is the Anthropic OpenClaw Ban? How Third-Party Harnesses Were Blocked From Claude Subscriptions", https://www.mindstudio.ai/blog/anthropic-openclaw-ban-third-party-harnesses-claude-subscriptions (2026)
41. explainx, "OpenClaw meets ChatGPT Plus: OpenAI's subscription path", https://explainx.ai/blog/openclaw-chatgpt-plus-pro-openai-anthropic-subscription-2026 (2026)
42. ccleaks, "Anthropic Extra Usage: Harnesses, Credits, PBC Charges", https://ccleaks.com/news/anthropic-kills-third-party-harnesses (Apr 2026)
43. Hacker News, "Tell HN: Anthropic no longer allowing Claude Code subscriptions to use OpenClaw", https://news.ycombinator.com/item?id=47633396 (Apr 2026)
44. OpenClaw issue #65399, https://github.com/openclaw/openclaw/issues/65399 (Apr 2026)
45. OpenClaw docs, Anthropic provider page, https://github.com/openclaw/openclaw/blob/main/docs/providers/anthropic.md (fetched Sep 2026)
46. DigitalApplied, "Claude Credit Overhaul 2026: Anthropic Pauses the June 15 Change", https://www.digitalapplied.com/blog/anthropic-claude-credit-overhaul-june-15-2026 (Jun 2026)
47. OpenClaw issue #106839, https://github.com/openclaw/openclaw/issues/106839 (Jul 2026)
48. OpenClaw issue #129765, https://github.com/openclaw/openclaw/issues/129765 (Aug 2026)
49. OpenClaw issue #137413, https://github.com/openclaw/openclaw/issues/137413 (Sep 2026)
50. MindStudio, "OpenClaw's Creator Joined OpenAI, Then OpenAI Made OpenClaw Free", https://www.mindstudio.ai/blog/peter-steinberger-openclaw-creator-openai-codex-free (May 2026)
51. OpenClaw issue #116315, https://github.com/openclaw/openclaw/issues/116315 (Jul 2026)
52. OpenClaw issue #114834, https://github.com/openclaw/openclaw/issues/114834 (Jul 2026)
53. OpenClaw issue #115939, https://github.com/openclaw/openclaw/issues/115939 (Jul 2026)
54. Monetizely, "The 2026 Guide to SaaS, AI, and Agentic Pricing Models", https://www.getmonetizely.com/blogs/the-2026-guide-to-saas-ai-and-agentic-pricing-models (2026)
55. Fin (Intercom) help centre, "Fin pricing: Outcomes", https://fin.ai/help/en/articles/13975800-fin-pricing-outcomes (2026)
56. Sierra, "Outcome-based pricing for AI agents", https://sierra.ai/blog/outcome-based-pricing-for-ai-agents (2025)
57. Menlo Ventures, "2025: The State of Generative AI in the Enterprise", https://menlovc.com/perspective/2025-the-state-of-generative-ai-in-the-enterprise/ (Dec 2025)
58. Menlo Ventures, "2025 Mid-Year LLM Market Update", https://menlovc.com/perspective/2025-mid-year-llm-market-update/ (Jul 2025)
59. a16z, "How 100 Enterprise CIOs Are Building and Buying Gen AI in 2025", https://a16z.com/ai-enterprise-2025/ (2025)
60. Michael Burnett, "Deep Dive: AI Adoption in the Enterprise", https://michaelburnett3.substack.com/p/deep-dive-ai-adoption-in-the-enterprise (2026), secondary
61. Bessemer, "Securing AI agents: the defining cybersecurity challenge of 2026", https://www.bvp.com/atlas/securing-ai-agents-the-defining-cybersecurity-challenge-of-2026 (2026)
62. OpenHands repository, https://github.com/OpenHands/OpenHands (fetched 8 Sep 2026); AgentAya, "OpenHands 2026", https://agentaya.com/ai-review/openhands/ (2026) for Enterprise features
63. a16z, "Big Ideas 2026: Part 1", https://a16z.com/newsletter/big-ideas-2026-part-1/ (Dec 2025); a16z crypto, "AI in 2026: 3 trends", https://a16zcrypto.com/posts/article/trends-ai-agents-automation-crypto/ (2026)
64. Sequoia, "2026: This is AGI", https://sequoiacap.com/article/2026-this-is-agi (2026); InnMind summary of Sequoia AI Ascent 2026, https://blog.innmind.com/sequoia-ai-ascent-2026-what-ai-founders-should-change-in-their-pitch-deck/ (2026)
65. Bessemer, "The State of AI 2025", https://www.bvp.com/atlas/the-state-of-ai-2025 (Aug 2025)
66. Forbes, "Every Company Is Now An AI Wrapper So GTM Is The New Moat", https://www.forbes.com/sites/josipamajic/2026/06/24/every-company-is-now-an-ai-wrapper-so-gtm-is-the-new-moat/ (Jun 2026)
67. Wikipedia, "United States v. Microsoft Corp.", https://en.wikipedia.org/wiki/United_States_v._Microsoft_Corp.
68. Wikipedia, "Epic Games v. Apple", https://en.wikipedia.org/wiki/Epic_Games_v._Apple
69. Tracxn, "All Hands AI, funding rounds and investors", https://tracxn.com/d/companies/all-hands-ai/__oIikiRbTowteZcN-BKCkIOUP9EBluSMLuATTpkeefbc/funding-and-investors (2026)
70. claude-mem issue #1826, "PSA: Anthropic's April 4 third-party harness ban affects claude-mem's default billing path", https://github.com/thedotmack/claude-mem/issues/1826 (Apr 2026)
71. VentureBeat, "Anthropic says it hit a $30 billion revenue run rate after 'crazy' 80x growth", https://venturebeat.com/technology/anthropic-says-it-hit-a-30-billion-revenue-run-rate-after-crazy-80x-growth (2026)
