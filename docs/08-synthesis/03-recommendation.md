# Recommendation: sell proof, not permission

*Research program: agent harnesses. Synthesis written 2026-09-08 by the coordinator; rewritten 2026-09-09 after the red-team review in `04-red-team.md`. Status: final. This is the startup-thesis reading of `01-findings.md` and `02-reimagined-harness.md`. Market sizing is deliberately excluded, as agreed at the start of the program.*

## What this document answers

- Given the evidence, what should a startup that believes in the thesis build, for whom, and why would it survive the labs and the suites?
- What would prove the recommendation wrong, and what to do next before any market sizing.

## The recommendation in one paragraph

Build the independent verifier: a service that checks an agent's work from outside the harness that produced it, and renders the proof where people already check work. It plugs into every harness through the hooks and traces they all expose and into the office suite through the add-ins that went generally available this year. It takes the task, the artifact and the trace, and returns proof: which claims rest on which sources, which checks ran, what changed, which actions of which consequence were taken, and what could not be verified. It never acts, holds no credentials, keeps no ledger of authority and owns no surface, so it carries none of the liability, the identity fight, the injection surface or the platform risk that a control layer carries. Start with developers, who have a budget, a review bottleneck and a countable unit, and whose harnesses already expose hooks. Go second to the spreadsheet and the document, where nobody offers proof at all. Price per verified outcome. Treat consequence-aware permissions as something the proof can later earn the right to gate, on local harnesses only, if customers pull for it.

## What changed, and why

The first version of this recommendation proposed a model-neutral authority layer as the company and a vertical workflow as the wedge. The red team, working only from the corpus, showed that the authority layer's pieces already ship from Dust, Microsoft and the MCP specification; that the neutral seat is held by the company that owns identity, the office suite and the directory, and that the labs are routing policy to it; that an outside layer cannot enforce anything on hosted engines; that the layer multiplies rented tokens against outcome prices measured in dollars; that whoever holds the grants holds the blast radius; and that the strategy document itself ranked that position last. The red team's alternative, an independent verifier, is better supported by the same corpus, and this document adopts it. The evidence that decided it: the developer document names verification and proof of work across any engine as the one defensible position for an outsider; the design table's "nearest existing thing" for proof on documents and data was nothing; a verifier reads rather than generates, so its cost scales differently from everything else in the corpus; and a check from the party that did the work is not independent, which the labs' own review products, built not to block, illustrate.

## The thesis you started with, and what survives

| You said | The evidence says | Consequence for the plan |
|---|---|---|
| As models get better, the harness becomes the bottleneck | True of the outer layer; the inner layer is the model owner's and should not be a startup's moat | Build only in the outer layer, and only the part an outsider can own |
| A CLI cannot be the default way to operate agents | Already the labs' own position; the CLI is the engine | Plug into every engine through its hooks; never build one |
| A desktop app cannot be the default either | Wrong about the client: the labs converged on desktop and chat apps as the front of a cloud runtime | Deliver proof into their apps and into the office suite; do not ship a competing app |
| The harness must be reimagined for non-technical people | True for the trust model; demand is thin, attended and document-shaped | Proof rendered into the grid and the document, for attended work first |
| A startup can define the new default | Not for a general harness, not for a neutral control plane | Own proof, not permission; earn anything more |

## The positions considered

1. **Open runtime.** A commodity with a dozen suppliers, bundled by the labs [runtime][labs]. No.
2. **A new general surface for non-technical people.** The worst business record in the corpus [findings 3]. No.
3. **Orchestration of many harnesses for developers.** Bundled by the labs; standalone managers died or were absorbed [independent]. No.
4. **A model-neutral authority and control layer.** Its components already ship; the seat belongs to the identity provider and the suite; it cannot enforce on hosted engines; it holds the blast radius; the strategy document ranks it last [strategy][nontech-labs][labs]. No, as a company. Its vocabulary survives inside proof.
5. **A vertical operator-configured harness.** The only archetype with reliable monetization, and the least harness-shaped [nontech-startups][strategy]. Yes, as the fallback if the verifier hypothesis fails, and as the shape the verifier's second market may take in practice.
6. **An independent verifier.** Reads every harness through hooks and traces; proves work where people check it; holds nothing and acts on nothing; prices per verified outcome. Yes.

## The product

- **Where it runs.** As a stop hook and an MCP server inside Claude Code, Codex, Cowork, Copilot and the open harnesses, whose hook event names are near-identical [protocols][matrix]; as an add-in in Excel, Word and Outlook, which are generally available surfaces for agent work [nontech-labs]; as a check on pull requests that the organization can make a merge requirement.
- **What it reads.** The task as stated, the trace of what the agent did, the artifact it produced, and the consequence annotations the harness exposes (MCP's read-only and destructive annotations, Dust-style stakes) [protocols][nontech-startups].
- **What it returns.** Proof: claims tied to source rows or files, checks run and their results, what changed against the task, actions taken by consequence class and by which principal, and an explicit list of what could not be verified. Rendered as a provenance column in the sheet, tracked changes with sources in the document, a report on the pull request.
- **What it never does.** Act, send, hold credentials, store organizational memory, or claim to undo.
- **What accumulates.** The corpus of verified and unverified artifacts and the evaluation it enables, which is the measurement the research documents say does not exist [runtime][academic].

## Why this position rather than the authority layer

| Test | Authority layer | Independent verifier |
|---|---|---|
| Gross margin | Adds a classifier round-trip, a second-model review, a generated page and fan-out on top of rented tokens; agent teams use about seven times the tokens (vendor) [runtime] | One read of a trace and an artifact per check; no fan-out; no generation of the work itself |
| Liability | Holds grants, credentials and a ledger: the blast radius [red team] | Holds nothing; the worst failure is a wrong "verified", a reputational risk to be measured and priced |
| Platform risk | Depends on subscription terms that changed five times in eight months and on hosted engines that admit no enforcement [strategy][labs] | Uses hooks and traces every harness exposes and public APIs; degrades to "unchecked" rather than "broken" when a vendor changes terms |
| Neutrality | Must be argued to buyers who already pay Microsoft for governance | Structural: a check is only worth paying for if it is independent of the producer |
| Enforcement | Advisory on hosted engines | Not needed; the organization's platform enforces "no merge without a check" |
| Incumbents | Microsoft Agent 365, Scout, Cowork RBAC, enterprise-managed MCP auth [nontech-labs] | Vendor reviewers that never block merging; code-review startups; nothing for documents and data [dev-users][generated-ui] |

## The wedge criteria, revised

1. **A buyer with a budget and a bottleneck.** Developers: review time up 91% in the one telemetry cited (vendor, via secondary), throughput up and stability down in DORA, three to five sessions per engineer in OpenAI's own account [dev-users].
2. **A countable unit.** A verified pull request; a reconciled statement; a report whose figures tie to sources.
3. **An observation point that exists today.** Hooks and traces in every developer harness; add-ins in the office suite [protocols][nontech-labs].
4. **A checkpoint nobody else owns.** The organization's merge requirement is set by the organization, not by the engine vendor; the same is true of who signs off a reconciliation.
5. **No credentials and no action** in the product's path, so procurement asks about data access, not blast radius.
6. **Evidence the labs cannot produce.** A check from the same vendor, model family and context as the work is not independent; the labs' reviewers are built not to block [dev-users].

Applied: developers first, because criteria 1, 3 and 6 are strongest there and the unit is clean. The office suite second: reconciliations, reports and records, where claims tie to rows and where the corpus records no proof product at all [generated-ui][nontech-startups]. Judgment-heavy documents last, because "verified" is undefined for them.

## Why a lab would not just do this

The honest answer is that a lab could ship a checker tomorrow, and the argument that it will not is about incentives, not capability.

- **Conflict of interest.** A first-party verifier is graded on the same outcomes as the generator it checks, and the labs' review products are shipped as advice that never blocks a merge [dev-users]. Separation of duties is a control organizations already understand and already pay for in finance and security.
- **The judgment part that must not be absorbed.** The anatomy document's rule is that judgment parts get absorbed into the model and plumbing stays outside [anatomy]. Independence is the one judgment property that cannot be absorbed by the producer without ceasing to be independence.
- **The suites.** Microsoft can add a checker to the office suite; it is the strongest counter to the second market. The answer is the same as for code: the check must not come from the party whose agent did the work, and Copilot Cowork runs on another lab's models, which makes even Microsoft's own stack a two-party system [nontech-labs].

What this argument does not do: it does not stop a lab from bundling a good-enough check into its own surface and winning on distribution, which is what happened to standalone browsers and orchestrators [findings 3]. The bet is that independence is a property buyers value in a checker, as they do in an auditor, in a way they do not value in a controller.

## What would falsify this

- Organizations accept the engine vendor's own review as sufficient and make it a merge requirement, and no buyer distinguishes independence from convenience.
- Checking turns out to cost as much as generating on real traces, so the margin argument fails.
- "Verified" cannot be defined for the second market in a way customers accept, so the product stays a code-review tool in a crowded field.
- Models become reliable enough that organizations stop reviewing agent work at all, and the review bottleneck disappears rather than moves.
- Vendors close the observation points: hooks removed, traces withheld on hosted surfaces.

## Risks, in order

1. **Gross margin.** The corpus's central finding about harnesses is that renting frontier models leaves a thin spread or a loss [strategy]. A check reads once and does not fan out, which is the structural reason to expect better economics, but a full-trace review by a strong model is not free, and the only public price for a code review is Anthropic's own at $15 to $25 per review [dev-users]. The prototype must produce a cost per check and a price per verified outcome before anything else is decided.
2. **A crowded first market.** Code review is contested by the labs (Claude Code's Code Review, Copilot review, Cursor's reviewer) and by funded startups (Qodo, CodeRabbit) [independent][dev-users]. Independence and the cross-engine corpus have to carry the difference; if they do not, the first market is a feature.
3. **The buyer can build it.** A third of organizations skipped buying at least one product because they could build it with agentic coding tools [adoption]. A hook and a model call are easy; a corpus, a calibrated evaluation and integrations into every harness and the office suite are less easy, and that is the whole defense.
4. **Models check themselves.** If self-verification becomes reliable and free, the product shrinks to the independence argument alone.
5. **Observation points.** Hosted surfaces expose fewer hooks than local ones [labs][protocols]; the product's reach on cloud sessions depends on vendors continuing to expose traces.
6. **Evidence quality.** Much of this corpus was verified through search excerpts and GitHub mirrors because primary sites were blocked in this session; the verification notes in each document say what could not be opened. Vendor numbers are vendor numbers.

## Next steps, before any market sizing

1. **Twenty interviews**: ten engineering leads who run agent fleets, ten finance or operations analysts who receive agent-produced reconciliations or reports. Ask what proof would let them stop checking, what they check today, and what a wrong "verified" would cost them.
2. **A prototype in two pieces**: a stop hook plus MCP server that produces a check per pull request across two engines, and an Excel add-in that writes a provenance column for an agent-produced reconciliation.
3. **A cost model from real traces**: cost per check against the price of the outcome it verifies, with the false-verified rate measured alongside.
4. **The evaluation nobody has**: did the operator's task get done, at what cost, with how many interruptions and how much regret, built from the prototype's own corpus.
5. **Then size the market**, with the first market chosen and the four numbers plus the false-verified rate in hand.

## What this recommendation is not

- Not a better chat app, a new browser, a device or a desktop app. All four have fresh corpses.
- Not an engine, and not a control plane. Engines are free or rented; the control plane is the identity provider's.
- Not "we learn the harness automatically" as a moat. The best independent evidence says learned harnesses overfit and transfer poorly [academic].
- Not a claim that the CLI is dead. It is the engine, and developers keep it.
- Not a claim that the authority problem is solved. It is real, it is being solved by the vendors and the identity providers divergently, and proof is how an outsider participates in it without owning the blast radius.

## What this means for the thesis

Supports: the thesis survives as a design thesis (the outer harness is the bottleneck) and as a narrow business thesis (proof across engines and document surfaces). Contradicts: the broad version, a startup as the new default harness for everyone, does not survive the evidence, and neither does the intermediate version, a startup as the neutral control plane. Nuance: the vertical operator-configured harness remains the best-proven monetization in the corpus, and the verifier's second market may turn into one.

## Open questions and unverified claims

- Whether buyers value independence in a checker is inferred from separation-of-duties practice and from the labs' review products' design; it is not measured.
- The review-time and session-per-engineer figures are vendor or via-secondary numbers recorded with their status in the developer document.
- Cost per check has not been measured; the margin argument is structural until the prototype produces numbers.
