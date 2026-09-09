# On-the-fly harness

A research program on **agent harnesses**: the scaffolding around a language model that turns it into a working agent. It tests one thesis for a possible startup, "as models get better, the harness becomes the bottleneck, and neither a CLI nor a desktop app can be the default way people operate agents", and ends with a recommendation for a harness reimagined for developers and non-technical people.

Written for a software engineer who is new to agents: concepts get an analogy first, then precision.

## The answer in brief

- **The thesis survives as a design thesis and fails as a broad business thesis.** A harness has two layers that move in opposite directions. The inner layer (the loop, prompts, scaffolds) is small in lines, large in leverage, and belongs to whoever owns the model; it is being absorbed and automated. The outer layer (identity, permissions, verification, memory, cost, surfaces) is where every incident and every 2026 vendor investment lives, and it is the bottleneck.
- **"Not a CLI" is already the industry's position, for the surface.** Every lab runs one CLI-shaped engine behind many thin surfaces. The CLI became the engine, not the front door. Chat surfaces carry two orders of magnitude more people; developer surfaces lead on money and delegated autonomy. The desktop app is not dead: it is where the two largest labs put the window you approve things in.
- **For non-technical people, the part to reimagine is the trust model, not the body style.** Standalone new surfaces have the worst record in the corpus; the revenue sits in agents embedded where the data already is. Observed demand is thin, attended and document-shaped. Authority and reliability both bind; what nobody provides is a way to check the work without expertise.
- **The neutral control plane is not open.** Its pieces already ship (per-agent identities, tool stakes, spend caps, classifier-gated approvals), the labs are routing policy to the organization's identity provider, and the company that owns identity and the office suite already governs rival agents.
- **The recommendation is to sell proof, not permission.** Build an independent verifier that plugs into every harness through its hooks and into the office suite through its add-ins, checks an agent's work from outside the harness that produced it, renders the proof where people already check work (the pull request, the grid, the document), never acts and holds no credentials, and prices per verified outcome. Developers first, the spreadsheet and the document second, a vertical operator-configured harness as the fallback. This is the position the red team argued for; the coordinator's first recommendation, a startup-owned authority layer, did not survive the evidence, and the record of that reversal is in the repository.

## How to read this repository in thirty minutes

1. `docs/08-synthesis/01-findings.md`: eleven findings and ten principles, each cited to the research.
2. `docs/08-synthesis/02-reimagined-harness.md`: the design, part by part, with an evidence grade for each.
3. `docs/08-synthesis/03-recommendation.md`: the startup thesis, the wedge criteria, why a lab would not just do this, what would falsify it, and the next steps.
4. `docs/08-synthesis/04-red-team.md`: an adversarial review of the three documents above, with the coordinator's response.

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
