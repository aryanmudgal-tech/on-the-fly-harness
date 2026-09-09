# On-the-fly harness

A research program on **agent harnesses**: the scaffolding around a language model that turns it into a working agent. It tests one thesis for a possible startup, "as models get better, the harness becomes the bottleneck, and neither a CLI nor a desktop app can be the default way people operate agents", and ends with a recommendation for a harness reimagined for developers and non-technical people.

Written for a software engineer who is new to agents: concepts get an analogy first, then precision.

## The answer in brief

- **The thesis survives as a design thesis and fails as a business thesis.** A harness has two layers that move in opposite directions. The inner layer (the loop, prompts, scaffolds) is small in lines, large in leverage, and belongs to whoever owns the model. The outer layer (identity, permissions, verification, memory, cost, surfaces) is where every incident and every 2026 vendor investment lives, and it is the bottleneck.
- **"Not a CLI" is already the industry's position, for the surface.** Every lab runs one CLI-shaped engine behind many thin surfaces. The CLI became the engine, not the front door. Chat surfaces carry two orders of magnitude more people; developer surfaces lead on money and delegated autonomy. The desktop app is where the two largest labs put the window you approve things in.
- **For non-technical people, the part to reimagine is the trust model, not the body style.** Standalone new surfaces have the worst record in the corpus. Observed demand is thin, attended and document-shaped. Authority and reliability both bind.
- **Two candidate businesses were proposed and both were falsified in this repository.** A startup-owned authority layer died to the red team: its pieces already ship, its seat belongs to the identity provider, and it cannot enforce anything on a hosted engine. An independent verifier died to a market test: checking code is CodeRabbit's product at a $1.5B valuation, and checking claims against sources in documents and spreadsheets shipped in December 2025 and July 2026. The corpus profiled harnesses and never profiled the tools that check what harnesses produce, which is how both errors survived nineteen fact-checked documents.
- **What is left is one hypothesis and one measurement.** Nobody, in any domain, judges the agent's execution trace, the record of what it actually did rather than what it produced. Whether that gap is a company or a feature is decided by one test: on real agent-written changes that a leading reviewer already passed, how many carry a defect visible only in the trace, and how many of those a sandbox would have caught anyway. The fallback, if it fails, is the vertical operator-configured harness, the only archetype in the corpus with proven monetization.

## How to read this repository in thirty minutes

1. `docs/08-synthesis/01-findings.md`: eleven findings and ten principles, each cited to the research.
2. `docs/08-synthesis/02-reimagined-harness.md`: the design, part by part, with an evidence grade for each.
3. `docs/08-synthesis/03-recommendation.md`: the startup thesis, the wedge criteria, why a lab would not just do this, what would falsify it, and the next steps.
4. `docs/08-synthesis/04-red-team.md`: an adversarial review of the three documents above, with the coordinator's response.
5. `docs/08-synthesis/05-market-test.md`: the test that falsified the second recommendation, prompted by one question from the repository owner.

New to agents? Start with `docs/01-primer/anatomy.md` (what a harness is, from a thirty-line loop to production) and `docs/01-primer/history.md` (2022 to 2026, dated).

## The research corpus

Every document has a TL;DR, a "What this means for the thesis" section that states support and contradiction bluntly, an "Open questions and unverified claims" section, numbered sources, and a "Verification notes" section written by a second, independent fact-checking pass.

| Document | Question it answers |
|---|---|
| `docs/01-primer/anatomy.md` | What a harness is, part by part, and the thin-versus-thick tension |
| `docs/01-primer/history.md` | How harnesses evolved from ReAct to Managed Agents, with what failed |
| `docs/02-landscape/developer-harnesses-labs.md` | The first-party developer harnesses (Anthropic, OpenAI, Google, GitHub, Amazon, others) |
| `docs/02-landscape/developer-harnesses-independent.md` | The independent and open-source harnesses, and the 2026 shakeout |
| `docs/02-landscape/developer-harness-matrix.md` | One comparison matrix; what converged, what is contested, what nobody fills |
| `docs/02-landscape/non-technical-labs.md` | What non-technical people can delegate through the big vendors |
| `docs/02-landscape/non-technical-startups-enterprise.md` | The same for startups and enterprise suites, with the cautionary cases |
| `docs/02-landscape/adoption-evidence.md` | Who uses agents, for what, on which surfaces, with the Economic Index data |
| `docs/03-building-blocks/protocols.md` | MCP, A2A, ACP, AG-UI, A2UI, skills, hooks, payments, and what they change |
| `docs/03-building-blocks/runtime-and-safety.md` | Sandboxes, computer use, durability, memory, permissions, security, evals |
| `docs/04-research/academic.md` | Automated harness design, generative UI, HCI, METR's task horizons |
| `docs/04-research/industry-engineering.md` | What the people who build harnesses say, and where they disagree |
| `docs/05-surfaces/messaging.md` | The agent as a coworker you message |
| `docs/05-surfaces/browser-generated-ui.md` | Interfaces the agent generates for the task |
| `docs/05-surfaces/voice-ambient.md` | Voice, wearables, background and proactive agents |
| `docs/05-surfaces/os-level.md` | Agents woven into Windows, macOS, Android, iOS and the browser |
| `docs/06-users/developers.md` | What developers need, with survey and study evidence |
| `docs/06-users/non-technical.md` | What non-technical people need, with the Economic Index and HCI evidence |
| `docs/07-strategy/value-capture.md` | Where value accrues, platform risk, and why a lab would or would not do this |
| `docs/sources.md` | Every document's sources in one place |

## How it was produced, and what to trust

- **Method.** The brief, the design spec and the plan are in `docs/00-brief.md` and `docs/superpowers/`. Nineteen research agents each wrote one document from a shared brief; a separate fact-checking agent then verified the ten to fifteen highest-stakes claims in each, corrected the text in place, marked what it could not verify, and appended its notes. The coordinator wrote the synthesis and had it attacked by a red-team agent. The dispatch log in `docs/superpowers/plans/dispatch-log.md` records what ran, what failed and why.
- **Two constraints shaped the evidence.** The session's network policy blocked most primary sites (the labs' own blogs, arXiv, Wikipedia, the press), so many claims were confirmed through two or more independent search excerpts and GitHub-hosted mirrors rather than the original page; and the web-search allowance was shared across agents and ran out repeatedly, so some fact-checks leaned on reachable primary documents (GitHub repositories, vendor documentation sites, Microsoft's blogs). Each document's verification notes list which sources could not be opened and which claims remain "(unverified)".
- **Read numbers with their labels.** "Vendor" means the company that benefits reported it. "Via secondary" means the original could not be opened. Dates matter: the field moved monthly through 2026, and every document is dated.

## Repository layout

| Path | What it is |
|---|---|
| `docs/00-brief.md` | The research brief: framing, definitions, assumptions, the questions asked at the start |
| `docs/01-primer/` to `docs/07-strategy/` | The research corpus (table above) |
| `docs/08-synthesis/` | Findings, design, recommendation, red team |
| `docs/sources.md` | Consolidated bibliography |
| `docs/superpowers/` | Design spec, execution plan, research brief template, dispatch log |
| `docs/report.html` | Source of the published one-page report |
