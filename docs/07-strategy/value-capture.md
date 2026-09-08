# Where the Money Lands: Value Capture in the Agent Stack

*Research program: agent harnesses. Written 2026-09-08. Status: verified with notes.*

## What this document answers

- Which layer of the agent stack (model, harness, surface, distribution, data) captures revenue and margin as of September 2026, using the numbers that are public.
- What the labs are doing about harnesses and surfaces: building, buying, hiring, and policing who may use lab subscriptions inside third-party harnesses.
- How much platform risk a third-party harness carries, using the OpenClaw episode of 2026 as the case study.
- For four candidate startup positions, a blunt answer to "why would a lab not just do this?"

## TL;DR

- Harness-layer revenue is real: Cursor passed $4B annualized (June 2026) and was then bought by SpaceX for $60B in stock (closed August 2026), Cognition is reported at roughly $900M (Sept 2026), Lovable $500M (June 2026), Replit about $250M (late 2025; third-party estimates put it near $525M by April 2026). But the labs' own harnesses grow as fast from inside a bigger business: Claude Code passed $2.5B run-rate (Feb 2026); Codex reached 5M weekly users (June 2026).
- Gross margin lands at the model layer. Cursor's gross margin was reported at minus 23% for the quarter ending January 2026 and turned positive only after it shipped its own models and repriced. A harness that rents frontier models resells them at a loss or a thin spread.
- Labs are moving into every layer: surfaces (Claude Cowork, Jan 2026; the Codex desktop app, Feb 2026; Codex folded into ChatGPT, July 2026), runtime (OpenAI buying Ona, June 2026), tooling (Anthropic buying Bun, Dec 2025) and people (OpenClaw's creator joined OpenAI, Feb 2026). Companies with distribution or compute are buying harnesses too (SpaceX/Cursor, Atlassian/Dia, Cognition/Windsurf; Meta's purchase of Manus was ordered unwound by Beijing in April 2026).
- Platform risk is not theoretical. Anthropic stopped subscriptions from covering third-party harnesses on 4 April 2026, partially reinstated them in May behind a separate metered pool, paused a further change on 15 June, and by September was rejecting requests that carried OpenClaw's own prompt markers. OpenAI took the opposite public stance while enforcing unpublished caps.
- Pricing is converging on a hybrid: a seat-like subscription, metered credits underneath, and outcome pricing only where the outcome can be counted. No general-purpose harness has made outcome pricing work.
- Enterprises buy governance from vendors they already have paper with. Spend is concentrated in coding, buyers are increasingly line-of-business, multi-model is normal, and agent security is the stated blocker.
- For the thesis: the evidence supports "the harness is where the product is" and contradicts "an independent harness captures the value." Defensible positions own distribution, data or a countable outcome. The harness itself is squeezed from above (labs) and below (open source).

## 1. The stack, and where the meter sits

Analogy: a ride-hailing business. The model is the driver's skill, rented by the hour from an agency. The harness is the car, the dispatch system and the insurance. The surface is the app the rider taps. Distribution is being pre-installed on every phone. Data is the map and the riders' habits. The agency sets the hourly rate, can refuse to supply drivers to a rival app, and can launch its own app. That is the position of an independent harness company in 2026.

Precisely: a harness company buys tokens from a lab (its cost of goods), wraps them in the seven harness parts (loop, tools, context, permissions, runtime, surface, orchestration) and sells the result as a subscription or metered plan. "The meter" below means whoever decides what a unit of work costs and who may consume it.

```
layer         who holds it (Sept 2026)                          the meter
------------  ------------------------------------------------  ------------------------------
distribution  OS, browser, IDE and chat incumbents:             bundling, defaults, app review
              Microsoft/GitHub, Atlassian (Dia), ChatGPT
surface       labs (Claude app, Cowork, ChatGPT, Codex app),    subscription price
              IDEs (Cursor), app builders (Lovable, Replit)
harness       labs (Claude Code, Codex, Agent SDK),             seats, credits, overage
              independents (Cognition, Lovable, Replit;
              Cursor now inside SpaceX; Manus leaving Meta),
              open source (OpenClaw, OpenHands)
model         Anthropic, OpenAI, Google, xAI, open weights      $/token, plan quotas, terms
data          whoever holds the repos, tickets, customers       switching cost
```

In 2025 to 2026 the model layer reached up into harness and surface far faster than harness companies reached down into models.

## 2. Who is getting paid

All figures are company-disclosed run-rates or press reports, not audited revenue. "Run-rate" means the latest month's revenue times twelve.

| Company (layer) | Latest figure | Trajectory | Source type |
|---|---|---|---|
| Cursor (IDE harness; SpaceX subsidiary since Aug 2026) | >$4B annualized, early June 2026; ~75% enterprise | $2B Feb 2026, $3B late Apr, $4B Jun; forecasts >$6B by end 2026; sold to SpaceX for $60B in stock (announced 16 Jun, closed 14 Aug 2026) [72] | Dealroom [1], TechCrunch [2], Forbes [73] |
| Cognition (Devin + Windsurf) | $492M ARR May 2026; reported at roughly $900M Sept 2026 (Bloomberg, 2 Sep: "more than $900 million"; Unite.AI, 8 Sep: "almost $900 million") | Devin $73M Jun 2025; Windsurf ~$82M at acquisition Jul 2025; valuation $10.2B Sep 2025, $26B May 2026, $48B after a $2B Series E closed 8 Sep 2026 (talks at ~$47B reported 2 Sep) | Sacra [3], CNBC [4], Bloomberg via blog [5] and [74] |
| Lovable (app builder, non-technical users) | $500M annualized, 9 June 2026; "1 million new projects a week" | $400M Feb 2026; ~$200M late 2025; raised $400M at $13.3B, Aug 2026 | TechCrunch [6], [75] |
| Replit (app builder) | ~$250M ARR, CEO statement, late 2025 (unverified as a dated company disclosure); Sacra estimates ~$525M by Apr 2026 | ~$150M annualized at the Sep 2025 raise ($250M at $3B); $2.5M to $250M in about a year; raised $400M at $9B, Mar 2026; CEO targets $1B by end 2026 | Replit blog [7], StartupRiders [8] (secondary), Sacra estimate (secondary) |
| Claude Code (lab harness) | >$2.5B run-rate, 12 Feb 2026 | $1B Nov 2025, six months after GA; weekly actives doubled since 1 Jan 2026 | Anthropic [9], [10] (vendor claims) |
| Codex (lab harness) | >5M weekly users, 2 June 2026, ~20% knowledge workers | ~2M weekly Mar 2026 (reported); 7M to 8M mid-July after merging into the ChatGPT desktop app; "20M active" 21 Aug (window unspecified) | OpenAI [11], Unite.AI [12], Gradually [13] |
| GitHub Copilot (bundled harness) | 4.7M paid subscribers, Jan 2026 earnings call (reported); 20M all-time users Jul 2025 | FT (May 2026) reported at least $550M annual revenue | Stats sites [14], TechCrunch [15] |
| Manus (general agent, acquired) | $100M ARR within eight months (reported); sold to Meta for >$2B, Dec 2025 | | CNBC [16], TechCrunch [17] |

Three observations. Independent harnesses earn billions, so the "wrapper" dismissal is wrong about revenue; note, though, that the largest of them, Cursor, stopped being independent in August 2026 when SpaceX (which merged with xAI in February 2026) closed its $60B purchase [72]. The two lab harnesses launched in 2025, already match the largest independent, sit inside businesses an order of magnitude larger (Anthropic reported a $30B run-rate in April 2026 [71] and a $47B run-rate by late May 2026 (reported) [76]), and have distribution the independents lack: Codex is included in every paid ChatGPT plan [11], [31]. And the independents' growth is inseparable from the labs' models: Cursor, Lovable and Replit all resell Anthropic, OpenAI and Google.

## 3. Margin: the model layer eats the harness's gross margin

Revenue is not value capture. Per The Information, as relayed by secondary analyses, Cursor's gross margin was negative 23% in the quarter ending January 2026, when it was approaching $2B annualized; estimates put inference at 40 to 70 cents of every revenue dollar [18], [19]. The same reports say margins turned slightly positive by April 2026 as Cursor routed traffic to its own Composer models and repriced, with enterprise accounts profitable and individual plans still loss-making [18]. Treat this as unverified: The Information is paywalled and the relaying sources are blogs.

The response was vertical integration. Cursor shipped Composer in October 2025, Composer 2 in March 2026 (reported to be built on a Kimi K2.5 base) and Composer 2.5 in May 2026, which it says matches frontier models on coding benchmarks at about a tenth of the cost per token [20] (vendor claim via a developer blog). In June 2026 it announced a frontier model trained on SpaceX's supercomputer and Origin, its own git hosting platform built from its December 2025 acquisition of Graphite [21], [22]. The only independent harness with any margin disclosure had to become a model company to get its gross margin above zero, and then sold itself to a buyer that owns a frontier lab and a supercomputer (SpaceX, $60B in stock, closed 14 August 2026 [72]).

The labs face the same arithmetic in reverse. Anthropic's stated reason for the April 2026 crackdown was that third-party harnesses caused capacity and service issues [23]; flat subscriptions used by always-on agents lose money for the lab too. Peter Steinberger's 100 Codex agents consumed $1.3M of OpenAI tokens in 30 days, absorbed by OpenAI as research spending [24]. Tokens are everyone's cost of goods, and only the layer that makes them has a structural margin.

## 4. Lab behaviour: build, buy, hire, police

| Date | Move | Harness part | Read |
|---|---|---|---|
| Jul 2025 | Google pays ~$2.4B to license Windsurf and hire its CEO; Cognition buys the rest days later [4], [25] | surface (IDE), harness | Labs pay for people and distribution |
| Sep 2025 | Atlassian buys The Browser Company (Dia) for $610M cash, for "the browser for knowledge work in the AI era" [26] | surface (browser) | An incumbent with data buys a surface |
| Oct 2025 | OpenAI launches apps in ChatGPT and the Apps SDK; submissions reviewed from Dec 2025; selling digital goods "not yet allowed" [27], [28] | surface, distribution (store) | The lab becomes the store |
| Dec 2025 | Anthropic buys Bun, its first acquisition, as Claude Code passes $1B [10] | runtime | The lab owns the toolchain under its harness |
| Dec 2025 | Meta agrees to buy Manus for >$2B [16]; China's NDRC orders the deal unwound in Apr 2026, and Manus says in Aug 2026 it will resume as an independent company [77] | whole harness, surface | A distribution owner buys a finished agent; a regulator takes it back |
| Jan 2026 | Anthropic launches Claude Cowork: Claude Code's architecture in the desktop app for non-technical users; Windows Feb 2026; later all paid plans, web and mobile (reported) [29], [30] | surface | The lab ships the non-technical surface itself |
| Feb 2026 | OpenAI ships the Codex desktop app for macOS, a "command center" for parallel agents; Windows Mar 2026 [31] | surface, orchestration | The lab ships the orchestration surface |
| Feb 2026 | OpenClaw's creator joins OpenAI; OpenClaw moves to a foundation [32], [33] | people | Labs hire harness authors |
| Mar 2026 | OpenAI buys Astral (19 Mar) and Promptfoo (early Mar); six deals in Q1 2026 alone by Crunchbase's count, which includes the OpenClaw hire, against eight in all of 2025 [34] | tools, evals | Developer tooling consolidates under labs |
| Jun 2026 | OpenAI agrees to buy Ona (Gitpod): cloud sandboxes that keep agents running after the laptop closes [35], [36] | runtime | The lab owns durable execution |
| Jun 2026 | Microsoft's Copilot Cowork, announced 9 Mar 2026 and built on Anthropic's Claude models, reaches general availability on 16 Jun 2026, billed per task in Copilot Credits at $0.01 each pay-as-you-go [37], [78] | surface (Office) | The distribution incumbent copies the lab's surface, on the lab's models |
| Jun 2026 | SpaceX agrees to buy Cursor for $60B in stock, exercising an option taken in April; closed 14 Aug 2026, and Cursor now sits inside SpaceXAI alongside xAI's Grok [72] | whole harness, surface, models | A lab-plus-compute owner buys the largest independent harness |
| Jul 2026 | Codex merged into the ChatGPT desktop app; ChatGPT Work agent launched (reported) [12] | surface, distribution | The harness collapses into the chat app |

Within twelve months the labs covered surface, runtime, orchestration and tooling. The buyers of independent harnesses were companies with distribution or compute (Meta, until Beijing unwound it; Atlassian; Google; SpaceX), not other harness companies; Cognition's purchase of Windsurf bought a seat-based IDE business and its enterprise customers.

## 5. Platform-risk case study: OpenClaw against the meter

Analogy: OpenClaw is a third-party taxi app that lets riders use their existing monthly pass from the driver agency, and the agency noticed its passes being used for 24-hour shifts.

OpenClaw is an open-source personal agent (MIT licence) that runs on the user's machine and routes to whichever model the user has credentials for, including Claude and ChatGPT subscriptions via their login tokens (the "OAuth token" a subscription hands to an app). It became the most-starred software project on GitHub within months of release (it passed React on 1 March 2026; only the freeCodeCamp aggregator repo, at about 454k stars, has more) [79]; the repo showed 389k stars and 82k forks on 8 September 2026, is stewarded by a 501(c)(3) foundation, and has "no paid tier or hosted service" [33], [38]. Press estimated 135,000+ active instances at the time of the April crackdown [39] (unverified).

| Date | Event | Source |
|---|---|---|
| 9 Jan 2026 | Server-side checks reject Claude subscription tokens outside Claude Code with "This credential is only authorized for use with Claude Code and cannot be used for other API requests" | OpenClaw issue #559, opened 9 Jan 2026 [80]; MindStudio [40] |
| 20 Feb 2026 | Terms updated to prohibit subscription tokens in third-party tools: "The use of OAuth tokens obtained via Claude Free, Pro, or Max accounts in any other product, tool, or service is not permitted" (reported) | [40], [41], AlternativeTo and The Register [81]; Anthropic's own page not fetched |
| 4 Apr 2026, 12:00 PT | Claude subscriptions "no longer cover usage through third-party tools"; OpenClaw first, others "in the coming weeks"; pay-as-you-go "extra usage" at API rates; one-time credits of $20 (Pro), $100 (Max 5x) and $200 (Max 20x, Team) for accounts subscribed by 3 April, redeemable 3 to 17 April | ccleaks [42], TNW [39], Hacker News [43], claude-mem issue [70] |
| 12 Apr 2026 | Requests containing the string `openclaw.inbound_meta.v1` rejected with "You're out of extra usage"; renaming it to `v2` bypasses the filter | OpenClaw issue #65399 [44] |
| 13 May 2026 | Third-party agents reinstated on subscriptions; a separate monthly "Agent SDK" credit ($20 Pro to $200 Max and Enterprise, spent at API rates, covering the Agent SDK, `claude -p`, GitHub Actions and third-party apps built on the SDK) announced for 15 June; OpenClaw named (Conductor unverified) | VentureBeat [23], The New Stack and DevOps.com [82] |
| 15 Jun 2026 | Anthropic pauses the planned move of Agent SDK, `claude -p` and third-party usage to a separate monthly credit; such usage draws on plan limits again | OpenClaw docs [45], DigitalApplied [46] |
| 13 Jul 2026 | API error text: "Third-party apps now draw from your extra usage, not your plan limits." | issue #106839 [47] |
| 26 Aug 2026 | After OpenClaw moved its Claude transport onto Anthropic's official Agent SDK, Max subscribers were intermittently billed as third-party extra usage | issue #129765 [48] |
| 3 Sep 2026 | Detection extended to `openclaw.inbound_meta.v2` and `openclaw.diagnostics.v1`; all Anthropic models unusable for subscription users without extra-usage credit | issue #137413 [49] |

The billing question every third-party harness now answers on each request:

```
user's Claude subscription --> first-party client (Claude Code, Cowork) --> plan quota
                           \-> third-party harness --> server-side classifier
                                                       |-- looks first-party: plan quota
                                                       '-- looks third-party: "extra usage"
                                                           (API rates, or a 400 if none)
API key -------------------> any harness -------------------------------> pay per token
```

OpenAI took the opposite public position: Sam Altman posted on 1 May 2026, "you can sign in to openclaw with your chatgpt account now and use your subscription there! happy lobstering." [50], [83], and OpenClaw routes through Codex's login. But users report "unpublished 5-hour/weekly subscription caps" and 429 errors when several agents share one subscription [51]; a reported 161-hour cooldown turned out to be OpenClaw's own cached state rather than an OpenAI limit [52]. One report suggests Anthropic gates its newest model on subscription tokens by the Claude Code client version presented, so a third-party harness must impersonate a newer first-party client to reach it [53] (user hypothesis, unconfirmed).

Bluntly:

1. A harness built on someone else's consumer subscription is an arbitrage the lab can close with a server-side rule, and did.
2. Even API-key harnesses depend on the lab's classifier, model gating and terms, which changed at least five times in eight months.
3. Both labs used the episode to promote their own harness surface (Agent SDK, Codex) as the sanctioned path.
4. The most popular open harness monetised none of its 389k stars: the foundation has no paid tier, and its creator works for a lab.

## 6. Pricing models

Analogy: a gym sells memberships because most members do not show up. An agent is a member who never leaves the treadmill.

| Model | Examples | Who carries inference risk | How it is going |
|---|---|---|---|
| Flat subscription (seat-like) | Claude Pro $20, Max $100 or $200, Team $25 to $30 per user [30]; Codex inside ChatGPT plans [31] | vendor; viable only for the lab with the cheapest tokens | Labs added weekly caps and "extra usage" overage; the April 2026 crackdown was a cost measure [23] |
| Seat plus credits | Cursor (credits since June 2025), Windsurf seats, Microsoft Copilot Cowork metered Copilot Credits [37], [78] | shared; the customer watches credits burn | Cursor's negative margin at $2B ARR [18]; hybrid pricing is now the most common SaaS model per pricing surveys [54] (secondary) |
| Pure usage | API keys; Devin's compute units [3]; Replit's effort-based pricing [8] | customer | Enterprises accept it for bulk agents; consumers dislike it |
| Outcome | Intercom Fin: $0.99 per resolved conversation, $9.99 per qualified lead [55]; Sierra [56] | vendor | Works only where an outcome is a countable event; Intercom's help centre spends paragraphs defining a resolution [55] |

Per-seat pricing is structurally wrong for agents: the better the agent, the fewer seats the customer needs. Outcome pricing fixes that only for narrow, countable work. A general-purpose harness has no countable outcome, so it lands on seat-plus-credits, competing with a lab that sells the same tokens flat.

## 7. Enterprise procurement realities

- Spend is concentrated and coding leads. Menlo Ventures estimated $37B of enterprise generative-AI spend in 2025, up from $11.5B; coding tools were about $4B and the largest category (the "55% of departmental spend" share is unverified) [57] (survey of about 500 US decision-makers, Dec 2025). Menlo's Deedy Das: "The era of automatic OpenAI wins is over, and it may be hard for anyone to catch Anthropic"; the report says Anthropic has "dominated coding for 18 months straight" [57].
- Model share is contested and multi-vendor. Menlo's mid-2025 update put Anthropic at 32% of enterprise LLM API usage, OpenAI 25%, Google 20%, and found only 16% of deployments were "true agents" rather than fixed workflows [58]. a16z's 2025 CIO survey found procurement "now mirrors traditional software buying", with LLM budgets expected to grow about 75% [59]. Claims that line-of-business leaders are the largest buyer group (46%) and that 81% of enterprises run three or more model families come from a secondary summary and are unverified [60].
- Security is the stated blocker. Bessemer calls securing agents "the defining cybersecurity challenge of 2026" [61]; that is what a permissions-and-blast-radius story sells into.
- Read for a harness startup: enterprises want a model-neutral control plane, governance (audit, permissions, cost visibility) and a vendor they already buy from. OpenHands' commercial pitch, a control plane with scheduled automations, roles and cost dashboards [62], is exactly that, and competes with GitHub, Microsoft and the labs' enterprise tiers.

## 8. What investors are saying

| Firm | Claim | Date | Note |
|---|---|---|---|
| a16z | The model "might be" the commodity layer while value accrues to those "effectively building the harness"; multi-agent collaboration becomes the moat via switching costs [63] (quotes unverified; a16z pages not fetchable) | Dec 2025 and 2026 | Includes an a16z crypto piece; commentary, not data |
| Sequoia | "2026: This is AGI": 2023 to 2024 apps were "talkers", 2026 to 2027 apps are "doers"; founder questions include "Are you obsessively improving your agent harness?" and "Can you price and package to value and outcomes?" [64] | essay Jan 2026 (date inferred from coverage dated 27 Jan 2026); keynote of the same title at AI Ascent, late Apr 2026 | An explicit application-layer bet |
| Menlo | Coding is the "gateway" category; agentic infrastructure startups will become "$10B-plus platforms" [57], [58] | Jul and Dec 2025 | Menlo invests in Anthropic and OpenHands |
| Bessemer | The browser is "the most capable interface layer for agentic AI" [65] | Aug 2025 | Consistent with Atlassian/Dia and Anthropic's Chrome work |

All four say the harness matters. None says an independent, general-purpose harness captures the value; each points to data, workflow, collaboration or a vertical.

## 9. The "wrapper" debate, briefly

"Wrapper" is the claim that a product adds nothing a user could not get by pasting the same prompt into ChatGPT. The 2026 version concedes that Cursor, Lovable and Cognition started as wrappers and now earn billions, and moves the goalposts to "GTM is the new moat" [66]. The useful test is not the label but two questions: who holds the meter, and what does the customer lose by switching? Cursor answered by owning models and a git forge. Lovable answered with distribution to non-developers. OpenClaw, with the most stars on GitHub, had no answer.

## 10. Analogies from previous platform shifts

- Browsers. Netscape had the better product and the early share; Microsoft bundled Internet Explorer with Windows; the antitrust remedy arrived years after the market had moved [67]. Mapping: the lab's chat app is Windows, the first-party harness is IE, and Codex being folded into ChatGPT in July 2026 is the bundling.
- App stores. Apple set the 30% commission and the review rules; developers thrived only in the permitted lanes, and the anti-steering dispute ran from a 2020 lawsuit to a 2025 contempt ruling [68]. Mapping: apps in ChatGPT are reviewed before listing and could not sell digital goods at launch [28]; the token price and plan quota are the commission.
- Where the analogy breaks, in the startup's favour: there are at least four frontier vendors plus open weights, switching is a configuration change, and enterprises already run several. No single lab is iOS. That is the structural reason a model-neutral harness can exist at all.

## 11. "Why would a lab not just do this?"

**Open harness runtime (OpenClaw- or OpenHands-shaped).** The labs already did the first-party version: Claude Code, the Agent SDK, Codex CLI. They will not do the model-neutral version, because neutrality is against their interest. That gap is real but poorly monetised: OpenClaw is a foundation with no revenue [38]; OpenHands has raised about $24M and sells an enterprise control plane [62], [69]. Revenue means becoming the enterprise's governance layer, against GitHub, Microsoft and the labs' enterprise tiers, while the lab can reclassify your traffic at will. Verdict: viable as open-source infrastructure; weak as a venture-scale business unless it owns the control plane and the API keys.

**Non-technical surface.** The labs are doing exactly this, and so is Microsoft: Cowork (Jan 2026), Codex inside ChatGPT and ChatGPT Work (Jul 2026), Copilot Cowork (Jun 2026). Lovable is the counter-example at $500M annualized, but its margin exposure is Cursor's from 2025 and its models are the labs'. A startup wins here only with distribution the labs lack (a vertical, an existing software estate, a channel such as messaging) or a data asset. Verdict: the surface alone is the most exposed position in this document.

**Orchestration of many harnesses (Conductor, Vibe Kanban and similar).** Both labs' desktop apps already run parallel agents; the Codex app launched as a "command center" for that [31]. Cross-vendor orchestration survives only while enterprises stay multi-model and labs keep blocking each other's subscriptions, which favours an orchestrator holding API keys for all of them. Margins are thin because you resell everyone's tokens; the natural end state is acquisition, as with Ona to OpenAI and Graphite to Cursor. Verdict: a feature or an exit, not a platform.

**Vertical harness (Fin-, Sierra- or Harvey-shaped).** The one position the labs are structurally unlikely to take: they do not want two hundred verticals' workflows, compliance and sales teams, and Menlo's data says coding, the vertical the labs did take, is where the money currently is [57]. Vertical harnesses can price outcomes because the outcome is countable [55]. The risks: the lab's general agent plus a store (apps in ChatGPT, MCP) absorbs the vertical's tools, and the harness parts themselves are commodity; the value is in the data, integrations and metering. Verdict: the most defensible position, and the least "harness-shaped".

## What this means for the thesis

**Supports.** The harness is where product decisions are made and where the labs are spending: acquisitions, desktop apps, a non-technical surface, a runtime purchase, a hire of the most popular harness author. Sequoia and a16z agree. The clause that neither a CLI nor a desktop app is the end state matches the labs' own moves: Anthropic went from a CLI (2025) to a desktop app (Jan 2026) to web and mobile (mid-2026), and OpenAI folded its desktop app into the chat app.

**Contradicts.** The thesis implies a reimagined harness is a startup-sized opportunity. For a general-purpose harness the evidence says otherwise: the meter sits with the lab; the only independent with margin data had to build models; the most popular open harness earns nothing and was policed by server-side classifiers; the non-technical surface is already shipped by two labs and Microsoft. "Better models make the harness the bottleneck" is true for product quality and false for economics: better, cheaper models make a harness easier to copy.

**Nuance.** Multi-vendor reality, the labs' mutual blocking, and enterprise demand for governance leave a durable niche for model-neutral control planes. Distribution and compute owners (Atlassian, Google, SpaceX; Meta paid too, before Beijing forced the Manus deal apart) pay real money for harness and surface companies, so "build to be bought by a distributor" is legitimate, with the caveat that the largest such exit went to a buyer that also owns a model lab. The thesis reads best as a design thesis, not a business thesis: the best products will be reimagined harnesses, but the companies that capture their value will also hold the meter, the data or the channel.

## Open questions and unverified claims

*Author's list as written on 2026-09-08; statuses are updated in the verification notes at the end of this document.*

1. Cursor's negative 23% gross margin and its recovery are attributed to The Information via secondary blogs; not verified against the original.
2. Cognition's ">$900M ARR" and "~$47B" valuation (Sept 2026) come from a blog citing Bloomberg; not verified.
3. The 9 January and 20 February 2026 steps in Anthropic's policy timeline are from secondary sources; Anthropic's support pages could not be fetched from this environment. The ~13 May reinstatement date is inferred from a mirrored copy of the VentureBeat article.
4. Microsoft Copilot Cowork's GA date, billing model and any relationship to Anthropic's Cowork are from trade-press summaries; not fetched.
5. Composer 2's reported Kimi K2.5 base and Composer 2.5's cost claim are from a developer blog; unverified.
6. OpenAI's policy on Codex logins in third-party tools rests on a reported Altman statement (1 May 2026), not published terms.
7. "LOB leaders are 46% of decision-makers" and "81% run three or more model families" could not be traced to a primary a16z page.
8. Replit's ARR (~$250M) and the timing of its $400M raise rest on secondary posts.
9. Whether Anthropic's classifier deliberately targets OpenClaw marker strings or applies a generic heuristic that matches them is unknown; the GitHub reports document behaviour, not intent.
10. Not covered: Google's harness economics (Gemini CLI, Jules, Antigravity), xAI, Chinese harness vendors, Anthropic Managed Agents' pricing, and any audited financials.

## Sources

Fetched directly from this environment: GitHub items [38], [44], [45], [47] to [49], [51] to [53], [62], [70], [80], and Microsoft's Copilot Cowork GA post [78] (verification pass, 8 Sep 2026). All other pages were located by web search but could not be fetched (network egress restrictions), so their figures come from search-result summaries and should be re-verified against the page.

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
72. TechCrunch, "SpaceX to acquire Cursor for $60B in stock, days after blockbuster IPO", https://techcrunch.com/2026/06/16/spacex-to-acquire-cursor-for-60b-in-stock-days-after-blockbuster-ipo/ (16 Jun 2026); CNBC, "SpaceX to acquire the AI coding startup Cursor for $60 billion", https://www.cnbc.com/2026/06/16/spacex-spcx-cursor-acquisition-ipo.html (16 Jun 2026); TechCrunch, "SpaceX officially closes its Cursor acquisition", https://techcrunch.com/2026/08/15/spacex-officially-closes-its-cursor-acquisition/ (15 Aug 2026); SatNews, "SpaceX Finalizes Regulatory Procedures to Close $60 Billion Acquisition of AI Platform Cursor", https://satnews.com/2026/08/13/spacex-finalizes-regulatory-procedures-to-close-60-billion-acquisition-of-ai-platform-cursor/ (Aug 2026). Located by search; not opened.
73. Forbes, "Cursor Hits $4 Billion Annualized Revenue Ahead Of SpaceX IPO", https://www.forbes.com/sites/richardnieva/2026/06/08/cursor-4-billion-annualized-revenue/ (8 Jun 2026). Located by search; not opened.
74. Bloomberg, "AI Startup Cognition Set to Raise Around $1 Billion at a $47 Billion Value", https://www.bloomberg.com/news/articles/2026-09-02/ai-startup-cognition-set-to-raise-around-1-billion-at-a-47-billion-value (2 Sep 2026); Bloomberg, "AI Startup Cognition Raises $2 Billion at a $48 Billion Value", https://www.bloomberg.com/news/articles/2026-09-08/ai-startup-cognition-raises-2-billion-at-a-48-billion-value (8 Sep 2026); Unite.AI, "Cognition Raises Over $2B Series E at $48B Valuation to Scale Devin Agents", https://www.unite.ai/cognition-raises-over-2b-series-e-at-48b-valuation-to-scale-devin-agents/ (8 Sep 2026); Seeking Alpha, "Cognition AI raises $2B at $48B valuation in Series E funding round" (8 Sep 2026). Located by search; not opened.
75. TechCrunch, "Lovable confirms new $13.3B valuation, raises another $400M", https://techcrunch.com/2026/08/12/lovable-confirms-new-13-3b-valuation-raises-another-400m/ (12 Aug 2026). Located by search; not opened.
76. Simon Willison, "Anthropic's run-rate revenue hits $47 billion", https://simonwillison.net/2026/May/29/anthropic/ (29 May 2026). Located by search; not opened.
77. TechCrunch, "China blocks Meta's $2B Manus deal after months-long probe", https://techcrunch.com/2026/04/27/china-vetoes-metas-2b-manus-deal-after-months-long-probe/ (27 Apr 2026); CNN Business, "China blocks Meta's acquisition of Chinese-founded AI startup Manus", https://edition.cnn.com/2026/04/27/tech/china-blocks-meta-manus-intl-hnk (27 Apr 2026); CNBC, "Manus to return as independent company after China forced Meta to unwind $2 billion deal", https://www.cnbc.com/2026/08/11/manus-china-meta-acquisition.html (11 Aug 2026); Quartz, "Meta is unwinding its $2 billion AI acquisition as China forces the deal apart", https://qz.com/meta-manus-acquisition-unwind-china-beijing-061226 (Jun 2026). Located by search; not opened.
78. Microsoft 365 Blog, "Copilot Cowork is now generally available", https://www.microsoft.com/en-us/microsoft-365/blog/2026/06/16/copilot-cowork-is-now-generally-available/ (16 Jun 2026), fetched 8 Sep 2026: "At general availability, Copilot Cowork runs on Anthropic models, including Opus 4.8 and Sonnet 4.6"; "PayGo is priced at $0.01 per Copilot Credit". GeekWire, "Microsoft's new Copilot Cowork integrates Anthropic's Claude in rollout of new E7 licensing tier", https://www.geekwire.com/2026/microsofts-new-copilot-cowork-integrates-anthropics-claude-in-rollout-of-new-e7-licensing-tier/ (Mar 2026), not opened.
79. star-history.com, "OpenClaw Surpasses React to Become the Most-Starred Software Project on GitHub", https://www.star-history.com/blog/openclaw-surpasses-react-most-starred-software/ (Mar 2026); gitstarclub, "All-Time GitHub Star Rankings", https://gitstarclub.com/rankings (freeCodeCamp ~454k, OpenClaw ~387k, Aug 2026). Located by search; not opened.
80. OpenClaw issue #559, "Claude Code OAuth tokens now blocked for external API use", https://github.com/openclaw/openclaw/issues/559 (opened 9 Jan 2026), fetched 8 Sep 2026.
81. AlternativeTo, "Anthropic officially bans using subscription authentication for third-party Claude use", https://alternativeto.net/news/2026/2/anthropic-officially-bans-using-subscription-authentication-for-third-party-claude-use (Feb 2026); The Register, "Anthropic closes door on subscription use of OpenClaw", https://www.theregister.com/2026/04/06/anthropic_closes_door_on_subscription/ (6 Apr 2026); OpenClaw.report, "Anthropic Bans OAuth Tokens from Consumer Plans in Third-Party Tools", https://openclaw.report/ecosystem/anthropic-bans-oauth-tokens-third-party-tools (2026). Located by search; not opened.
82. The New Stack, "Anthropic pauses Claude Agent SDK subscription change on day it was due to take effect", https://thenewstack.io/anthropic-pauses-claude-agent-sdk-subscription-change/ (Jun 2026); DevOps.com, "Anthropic Hits Pause on Claude Agent SDK Billing Change, For Now", https://devops.com/anthropic-hits-pause-on-claude-agent-sdk-billing-change-for-now/ (Jun 2026); GIGAZINE, "Anthropic has announced Claude Agent SDK credits...", https://gigazine.net/gsc_news/en/20260514-anthropic-claude-agent-sdk-credits/ (14 May 2026 JST); Claude Help Center, "Use the Claude Agent SDK with your Claude plan", https://support.claude.com/en/articles/15036540-use-the-claude-agent-sdk-with-your-claude-plan. Located by search; not opened.
83. Sam Altman on X, 1 May 2026, https://x.com/sama/status/2050357911915028689 (text reproduced in search results and by Tom's Guide, Storyboard18 and TNW; x.com not fetchable); TNW, "OpenAI opens ChatGPT subscriptions to OpenClaw's 3.2M users as Anthropic blocks Claude access to the AI agent platform", https://thenextweb.com/news/openai-openclaw-chatgpt-subscription-agent (May 2026), not opened.


## Verification notes (2026-09-08)

Method: every claim below was checked against at least one source independent of the one the author cited, using web search plus direct fetches where the proxy allowed. Pages opened in full: the OpenClaw repository and issues #559, #65399, #106839, #114834, #115939, #116315, #129765 and #137413; the OpenClaw `docs/providers/anthropic.md` page; claude-mem issue #1826; and Microsoft's Copilot Cowork GA post. Everything else rests on search-result text from two or more distinct outlets, which is noted per claim. Verdicts: 32 confirmed, 9 corrected, 3 unverified.

| # | Claim | Verdict | Evidence and notes |
|---|---|---|---|
| 1 | Cursor gross margin minus 23% in the quarter to Jan 2026; slightly positive by Apr 2026 (enterprise positive, individuals loss-making) | confirmed, as reported | The Information's figures are relayed consistently by Dealroom ("Inside Cursor's $60B SpaceX Deal: Explosive Revenue, Negative Margins"), Contrary Research ("Cursor's $60 Billion Escape Hatch") and April 2026 posts by Sheel Mohnot and Aakash Gupta on X. The original is paywalled and was not opened; one relay puts Cursor at $2.7B annualized by March 2026, so "approaching $2B" applies to the January quarter only. |
| 2 | Cognition ">$900M ARR" and "~$47B valuation" (Sep 2026) | corrected | Bloomberg (2 Sep) reported a ~$1B raise at $47B with "more than $900 million" annualized; Bloomberg, Unite.AI, Seeking Alpha and TNW (8 Sep) report the round closed at $2B (Series E) and $48B, with run-rate "almost $900 million" (Unite.AI). Table and TL;DR updated to $48B closed and "roughly $900M"; the two outlets disagree on which side of $900M. |
| 3a | 9 Jan 2026: subscription tokens rejected outside Claude Code | confirmed | OpenClaw issue #559, opened 9 Jan 2026, quotes the error "This credential is only authorized for use with Claude Code and cannot be used for other API requests" (fetched). MindStudio and Medium coverage agree. |
| 3b | 20 Feb 2026: terms updated to prohibit subscription OAuth tokens in third-party tools | confirmed by secondary sources only | AlternativeTo (Feb 2026), The Register (6 Apr 2026), openclaw.report and Hongkiat all give 20 Feb and the same sentence of the terms. Anthropic's legal page was not fetchable, so the row keeps "(reported)". |
| 3c | 4 Apr 2026, 12:00 PT: subscriptions stop covering third-party tools; extra usage; credits | confirmed | TechCrunch (4 Apr), TNW, The Register, TechRadar and the Hacker News thread agree on date, noon PT, "OpenClaw first, others in the coming weeks", and Boris Cherny's "subscriptions weren't built for the usage patterns of these third-party tools". ccleaks gives the credits as $20 Pro, $100 Max 5x, $200 Max 20x and Team, for accounts subscribed by 3 Apr, redeemable 3 to 17 Apr; row made specific. |
| 3d | ~13 May 2026: third-party agents reinstated behind a separate Agent SDK credit | confirmed, date firmed to 13 May | The New Stack: "announced on May 13"; GIGAZINE dated 14 May JST; the VentureBeat mirror is dated 13 May. The credit ($20 Pro to $200 Max/Enterprise, dollar-for-dollar at API rates, covering the Agent SDK, `claude -p`, GitHub Actions and third-party apps built on the SDK) was announced for 15 June. OpenClaw was named; the "Conductor" mention could not be checked because VentureBeat is blocked. |
| 3e | 15 Jun 2026: the credit change paused; such usage draws on plan limits again | confirmed | The New Stack ("pauses ... on day it was due to take effect"), DevOps.com, DigitalApplied, and the OpenClaw Anthropic provider doc (fetched): "Anthropic's June 15, 2026 support update paused the previously announced separate Agent SDK credit plan ... third-party app usage still draw[s] from the signed-in subscription's usage limits." |
| 4 | Microsoft Copilot Cowork GA June 2026 with metered billing | confirmed and made specific | Microsoft 365 Blog (fetched): GA worldwide on 16 Jun 2026; billed per task in Copilot Credits; "PayGo is priced at $0.01 per Copilot Credit"; "runs on Anthropic models, including Opus 4.8 and Sonnet 4.6". GeekWire and WinBuzzer: announced 9 Mar 2026 with Claude inside; Frontier preview from 30 Mar. This also answers the author's open question 4: Copilot Cowork is built on Anthropic's models. |
| 5 | Sam Altman, 1 May 2026: OpenClaw covered under paid ChatGPT plans | confirmed | X post of 1 May 2026 (text reproduced in search results): "you can sign in to openclaw with your chatgpt account now and use your subscription there! happy lobstering." Corroborated by Tom's Guide, Storyboard18 and TNW ("OpenAI opens ChatGPT subscriptions to OpenClaw's 3.2M users"). Verbatim quote inserted. |
| 6a | Cursor >$4B annualized, early June 2026; $2B Feb, $3B late Apr; ~75% enterprise; >$6B forecast | confirmed | Forbes (8 Jun 2026), Dealroom, Bloomberg and TechCrunch (17 Apr 2026, $2B raise at $50B talks) agree. Note one relay puts enterprise at "about $2.6B", which implies a lower total; the author's Dealroom figures are kept. |
| 6b | Cursor's status as an independent harness | corrected (omission) | SpaceX took an option in April 2026 ($10B partnership or $60B purchase), announced the $60B all-stock acquisition on 16 Jun 2026 (TechCrunch, CNBC, Yahoo Finance) and closed it on 14 Aug 2026 per an 8-K (TechCrunch 15 Aug, SatNews, Yahoo Finance, TNW). Cursor is now a subsidiary in SpaceXAI, which absorbed xAI in Feb 2026. Added to TL;DR, section 1, section 2, section 3, section 4 table and text, and section 11. |
| 7 | Cognition $492M ARR May 2026; $26B; Devin $73M Jun 2025; Windsurf ~$82M; $10.2B Sep 2025 | confirmed | Sacra, TNW, KuCoin, Bloomberg (27 May 2026: $1B at $26B; TechCrunch: $25B pre-money), Yahoo Finance and DevOps.com (Windsurf $82M ARR, 350+ enterprises), CNBC (8 Sep 2025, $10.2B). |
| 8 | Lovable $500M annualized, 9 Jun 2026; 1M new projects a week; $400M Feb 2026 | confirmed | TechCrunch (9 Jun 2026) and its Yahoo syndication; TechCrunch on X. Added: $400M raised at $13.3B, 12 Aug 2026 (TechCrunch). |
| 9 | Replit ~$250M ARR (2026); $400M at $9B | corrected and qualified | The "$2.5M to $250M in 12 months" line is a CEO statement from around the turn of 2025/26 (BigGo, ARR Club); the company's last dated disclosure was ~$150M annualized at the Sep 2025 $250M raise (TechCrunch); the $400M Series D at $9B was announced 11 to 13 Mar 2026 (Bloomberg 15 Jan talks; Built In; TFN); Sacra estimates ~$525M by Apr 2026. Marked "(unverified as a dated company disclosure)". |
| 10 | Claude Code >$2.5B run-rate, Feb 2026; $1B Nov 2025 six months after GA | confirmed | Series G announcement (Feb 2026, $30B at $380B post) relayed by VentureBeat, SQ Magazine, Panto; $1B milestone announced 2 Dec 2025 with the Bun acquisition (Bun blog, US News, AIwire). anthropic.com not fetchable. |
| 11 | Codex >5M weekly users, 2 Jun 2026, ~20% knowledge workers; ~2M weekly Mar 2026 | confirmed | Constellation Research, Tech Insider, TechJack, Medium (Hightower); March figure of "over 2 million weekly users" in OpenAI's March statements as relayed by Panto and Gradually. |
| 12 | Codex folded into the ChatGPT desktop app, Jul 2026; 7M to 8M mid-July; 20M on 21 Aug (window unspecified) | confirmed | Merger on 9 Jul 2026 (Addigy, Daniel Vaughan's Codex knowledge base, Developers Digest); 8M "in one week" on 14 to 15 Jul, 10M on 21 Jul, 20M on 21 Aug and 25M by end of August (AGTP on X, Memeburn, BigGo, KuCoin); OpenAI has not said whether the 20M and 25M figures are weekly. |
| 13 | Anthropic buys Bun, Dec 2025, first acquisition | confirmed | 2 Dec 2025 (Bun blog "Bun is joining Anthropic", US News, DevOps.com, AIwire "first acquisition"). |
| 14 | OpenAI agrees to buy Ona (Gitpod), Jun 2026 | confirmed | 11 Jun 2026 (TNW, Dealroom, TechTimes, How2Shout); terms undisclosed; pending regulatory approval at announcement; completion not verified. |
| 15 | OpenClaw's creator joins OpenAI, Feb 2026; project moves to a foundation | confirmed | Announced 14 Feb 2026 (Steinberger's post; TechCrunch and CNBC 15 Feb; Forbes 16 Feb; CGTN). Foundation is a 501(c)(3) per the README (fetched). |
| 16 | Meta buys Manus for >$2B, Dec 2025 | corrected | Announced 29 Dec 2025 (>$2B; WSJ/Reuters $2B to $3B; Manus at $100M ARR eight months after launch, confirmed). Beijing opened a probe in Jan 2026; the NDRC ordered the deal unwound on 27 Apr 2026 (TechCrunch, CNN, CNBC); founders sought ~$1B to buy the company back (WinBuzzer, May); Meta cut Manus off from internal systems and called it "sunsetting" (TNW); Manus said on 11 Aug 2026 it will "soon resume operating as an independent company" (CNBC). Every mention of Meta as a completed buyer was amended. |
| 17 | Atlassian buys The Browser Company for $610M cash, Sep 2025 | confirmed | 4 Sep 2025 (Business Wire, CNBC, TidBITS, Daring Fireball, Atlassian blog). |
| 18 | Windsurf: Google ~$2.4B licence and CEO hire; Cognition buys the rest days later | confirmed | Google hired Varun Mohan and licensed technology on 11 Jul 2025; Cognition signed on 14 Jul 2025 (CNBC, TechCrunch, DevOps.com, Cognition on X). |
| 19 | Anthropic $30B run-rate, spring 2026 | confirmed | Early April 2026 (Bloomberg 6 Apr, VentureBeat, PYMNTS, MLQ). Added: $47B by 29 May 2026 (Simon Willison, reported). |
| 20 | GitHub Copilot 4.7M paid subscribers (Jan 2026 call); 20M all-time users (Jul 2025); FT "at least $550M" | confirmed | FY26 Q2 call, 28 Jan 2026 (Office365ITPros, Windows Forum); TechCrunch 30 Jul 2025; FT May 2026 figure relayed by Panto and Axis Intelligence as a floor based on the cheapest tier. |
| 21 | OpenClaw: 389k stars, 82k forks, MIT, 501(c)(3), no paid tier | confirmed (fetched) | README on 8 Sep 2026: 389.2k stars, 81.8k forks, MIT, "independent 501(c)(3)", "no paid tier, hosted service, or token". |
| 22 | OpenClaw "the most-starred repository on GitHub within five months" | corrected | It is the most-starred software project, having passed React on 1 Mar 2026 (star-history, The New Stack, DEV); the freeCodeCamp aggregator repo has ~454k stars against OpenClaw's ~387k to 389k (gitstarclub). Wording changed. |
| 23 | Steinberger's 100 Codex agents used $1.3M of tokens in 30 days, absorbed by OpenAI | confirmed | TNW, Tom's Hardware (603B tokens, 7.6M requests), The Decoder. |
| 24 | OpenAI buys Astral and Promptfoo (Mar 2026); "six acquisitions by June 2026" | corrected | Astral 19 Mar 2026 (CNBC, Simon Willison); Promptfoo early Mar 2026. Crunchbase News counts six deals in Q1 2026 alone (Astral, Promptfoo, Torch, Convogo, Crixet and the OpenClaw hire) against eight in 2025, so "six by June" understated and mixed acqui-hires with acquisitions. Row reworded. |
| 25 | Cursor: Graphite (Dec 2025), Composer 1/2/2.5, Kimi K2.5 base, Origin and a SpaceX-trained model (Jun 2026) | confirmed, as reported | Graphite 19 Dec 2025 (Cursor blog, Wilson Sonsini); Composer 2.5 on 18 May 2026 (WinBuzzer); the Kimi K2.5 base is reported by Emelia, BuildFastWithAI and DigitalApplied but Cursor's own confirmation was not fetched; Origin and the 1.5T-parameter Composer 3 pre-trained on SpaceX's Colossus were announced at Compile on 16 Jun 2026 (VentureBeat, MLQ, TechTimes). |
| 26 | Menlo: $37B from $11.5B; coding ~$4B; ~500 decision-makers; Deedy Das quote; "55% of departmental spend" | corrected (quote) and unverified (55%) | Press release (9 Dec 2025, via Yahoo Finance and GlobeNewswire listings) confirms $37B, tripling from $11.5B, "nearly 500" decision-makers, and coding as "a $4 billion category". The verbatim quote is "The era of automatic OpenAI wins is over, and it may be hard for anyone to catch Anthropic"; "dominated coding for 18 months straight" is report text. The 55% figure was not found. |
| 27 | Sequoia "2026: This is AGI": talkers/doers; founder questions; date | corrected (quote and date) | Talkers/doers confirmed. The article's questions are "Are you obsessively improving your agent harness?" and "Can you price and package to value and outcomes?". Chinese-language coverage of the essay is dated 27 Jan 2026; the same title was the AI Ascent keynote (late Apr 2026; Grady, Huang, Buhler). sequoiacap.com not fetchable. |
| 28 | a16z: model "might be" the commodity layer; value to those "effectively building the harness" | unverified | a16z.com and a16z.news pages are blocked and no search result reproduces these phrases. Marked inline. |
| 29 | Bessemer, "Securing AI agents: the defining cybersecurity challenge of 2026" | confirmed | Title matches the BVP Atlas listing and OODAloop's mirror. |
| 30 | OpenHands (All Hands AI) raised about $24M | confirmed | $5M seed (Sep 2024, Menlo) plus $18.8M Series A (Nov 2025, Madrona) = $23.8M (Tracxn, StartupIntros). |
| 31 | Intercom Fin: $0.99 per resolution, $9.99 per qualified lead | confirmed | Intercom help centre and pricing round-ups (Macha, Aimdoc, Featurebase); also $0.99 for procedure handoff and disqualification. |
| 32 | Claude Cowork: Jan 2026 launch; Windows Feb 2026; later all paid plans, web and mobile | confirmed | 12 Jan 2026 macOS research preview (Max first, then Pro); Windows 10 Feb 2026; web and mobile beta from 7 Jul 2026 (Vellum, Techsy, Tech Insider, DEV). |
| 33 | Codex desktop app, Feb 2026 macOS "command center"; Windows Mar 2026 | confirmed | macOS in Feb 2026 with 1M downloads in week one; Windows 4 Mar 2026 (IntuitionLabs, Medium, DEV). |
| 34 | Apps in ChatGPT Oct 2025; submissions from Dec 2025; digital goods "not yet allowed" | confirmed | 6 Oct 2025 launch; submissions and App Directory opened 17 Dec 2025 (Engadget, PYMNTS, YourStory); physical goods only at that stage. |
| 35 | OpenClaw GitHub issues cited in section 5 | confirmed (fetched), one corrected | #65399 (12 Apr), #106839 (13 Jul), #129765 (26 Aug), #137413 (3 Sep), #116315 (30 Jul) and #115939 (29 Jul, user hypothesis) say what the document says. #114834 (28 Jul) documents a 161-hour cooldown that OpenClaw itself cached while the account showed usable quota; the text now says so instead of attributing it to OpenAI. |
| 36 | 135,000+ active OpenClaw instances at the April crackdown | confirmed as a press estimate | TNW, tbreak, KuCoin and Medium repeat the figure; none gives a method. |

Corrections made in the body: (1) Meta/Manus is no longer a completed acquisition: unwind ordered 27 Apr 2026, Manus returning to independence (TL;DR, sections 1, 4 and 11). (2) SpaceX's $60B all-stock purchase of Cursor, announced 16 Jun and closed 14 Aug 2026, added; Cursor is no longer described as independent (TL;DR, sections 1, 2, 3, 4 and 11). (3) Cognition updated from "~$47B in talks" to a $2B Series E closed at $48B on 8 Sep 2026, and ">$900M" softened to "roughly $900M" (TL;DR, section 2). (4) Replit's ~$250M dated to late 2025 and qualified; Series D dated Mar 2026. (5) The 161-hour OpenAI "cooldown" reattributed to OpenClaw's cache. (6) "Most-starred repository" narrowed to most-starred software project. (7) OpenAI acquisition count restated per Crunchbase. (8) Deedy Das and Sequoia quotes restored to the wording found; Sequoia dated. (9) Copilot Cowork row made specific (9 Mar announcement on Claude models, 16 Jun GA, $0.01 per credit); timeline rows for 9 Jan, 20 Feb, 4 Apr and 13 May given their sources and exact terms; Altman quote inserted verbatim; Anthropic's $47B run-rate and Lovable's $13.3B round added.

Sources that could not be opened (network egress blocked; figures rest on search-result text from the outlets named): The Information (paywalled, not attempted); anthropic.com; support.claude.com; openai.com; techcrunch.com; venturebeat.com; cnbc.com; bloomberg.com; forbes.com; thenextweb.com; theregister.com; thenewstack.io; devops.com; geekwire.com; unite.ai; seekingalpha.com; investing.com; finance.yahoo.com; app.dealroom.co; contraryresearch.substack.com; sacra.com; a16z.com; sequoiacap.com; bvp.com; menlovc.com; globenewswire.com; news.crunchbase.com; constellationr.com; gigazine.net; alternativeto.net; openclaw.report; docs.openclaw.ai; openclawlaunch.com; ccleaks.com; digitalapplied.com; simonwillison.net; star-history.com; satnews.com; qz.com; edition.cnn.com; tomsguide.com; storyboard18.com; techfundingnews.com; blog.replit.com; cognition.com; x.com; wikipedia.org.

Remaining doubts: (a) Cursor's margin figures are The Information's and were never seen at source; the "40 to 70 cents of inference per revenue dollar" range in section 3 was not checked. (b) Cognition's run-rate sits on either side of $900M depending on the outlet; the "$492M" is a Sacra estimate that Bloomberg repeated. (c) The 20 Feb 2026 terms change and the exact wording of Anthropic's 13 May and 15 Jun support notices rest on secondary reproductions. (d) Whether OpenAI's Ona purchase has closed is unknown. (e) Replit has not published a dated ARR since Sep 2025; the $250M and $525M figures are a CEO remark and an analyst estimate respectively. (f) The 20M and 25M Codex figures have no stated window. (g) Whether Anthropic's classifier targets OpenClaw's markers deliberately (author's open question 9) remains unknown; the GitHub issues show behaviour only. (h) The Kimi K2.5 base for Composer 2 and 2.5 is reported by several outlets but not confirmed from Cursor's own material here.
