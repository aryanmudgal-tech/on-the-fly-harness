# Recommendation: build the authority layer, enter through a workflow with a native checkpoint, rent everything else

*Research program: agent harnesses. Synthesis written 2026-09-08 by the coordinator. Status: draft, red-team pending. This is the startup-thesis reading of `01-findings.md` and `02-reimagined-harness.md`. Market sizing is deliberately excluded, as agreed at the start of the program.*

## What this document answers

- Given the evidence, what should a startup that believes in the thesis actually build, for whom, and why would it survive the labs?
- What would prove the recommendation wrong, and what to do next before any market sizing.

## The recommendation in one paragraph

Do not build a harness in the sense the market uses the word. Build the outer harness described in the design document, the authority-and-verification layer that decides who an agent acts as, what it may do, how its work is proven, what it remembers and what it costs, and make it the same on every surface and every engine. Do not sell it as infrastructure. Enter through one non-technical workflow that has a native checkpoint (a draft before a send, a hold before a release, a staged change before an applied one) and a countable outcome, where the data already sits behind connectors and the buyer already pays for governance. Rent the engine, the sandbox and the surfaces. Design the layer from the first day to be portable across engines and vendors, because that portability is the only property the labs are structurally disinclined to provide, and it is the property enterprises are starting to pay for. Treat the general-purpose version as the destination, not the wedge.

## The thesis you started with, and what survives

| You said | The evidence says | Consequence for the plan |
|---|---|---|
| As models get better, the harness becomes the bottleneck | True of the outer layer, false of the inner layer, which is shrinking and being automated [findings 1, 9] | Build only in the outer layer; generate the inner layer per task |
| A CLI cannot be the default way to operate agents | Already the labs' own position; the CLI is the engine [finding 2] | Never build an engine; adapt to all of them |
| A desktop app cannot be the default either | Wrong about the client: the labs converged on desktop and chat apps as the front of a cloud runtime [finding 3][adoption] | Project into their apps; do not ship a competing app first |
| The harness must be reimagined for non-technical people | True, and the part is the trust model, not the surface [findings 4, 5] | The product is authority, verification, memory and cost, delivered inside surfaces people already use |
| A startup can define the new default | Not for a general harness; possibly for a portable authority layer or a vertical operator-configured harness [findings 7, 8] | Pick a wedge; earn the general position later |

## The positions considered

The strategy document evaluated the candidate positions a harness startup could take. Restated with the rest of the corpus:

1. **Open runtime.** Sandboxes are a commodity with a dozen suppliers and the labs bundle the runtime behind one API [runtime][labs]. Infrastructure, not a venture-scale company. No.
2. **A new general surface for non-technical people.** The worst business record in the corpus: two browsers gone, two devices dead, one standalone agent bounced between owners, the messaging agent sold in four months, the visual agent builder wound down [findings 3]. The incumbents own distribution, identity and the data graph and moved from idea to half the Fortune 500 in six months (vendor) [nontech-labs]. No.
3. **Orchestration of many harnesses for developers.** The labs bundled the agent manager with the engine; standalone managers died or were absorbed; the largest independent sold to a company that owns a model lab [independent][strategy]. A feature or an exit, not a company. No, except as a capability inside position 5.
4. **A vertical, operator-configured harness.** The only archetype that monetizes reliably: a non-technical operator edits policy while the vendor owns the loop; the suites and their acquirers pay for it (Agentforce, ServiceNow, Sierra, Decagon, Salesforce buying Fin) [nontech-startups][strategy]. Defensible, and the least harness-shaped. Yes, as the wedge.
5. **A model-neutral authority and verification layer.** The layer every vendor rebuilds separately and nobody owns across surfaces [nontech-startups][protocols]; the layer where the incidents live [runtime]; the layer enterprises say is the blocker [strategy]. Contested by Microsoft's Agent 365, which bundles identity, policy and audit for its own surfaces [nontech-labs][os]. Yes, as the destination.

The recommendation is 4 as the entry and 5 as the company. Built in that order, the vertical pays for the layer and proves it with real actions, and the layer makes the vertical portable when the wedge customer changes engines or surfaces.

## Choosing the wedge

The corpus gives five criteria, each with a source. Rank candidate workflows against all five.

1. **Tasks people already hand over whole.** Conversations matched to clerical and secretarial tasks run 69 to 75% automation-style; conversations matched to judgment work run 31 to 38% [nontech-users]. Start with the former.
2. **A native checkpoint.** Draft-then-send, hold-then-release, stage-then-apply. It gives the approval model a natural home and makes undo real [messaging][design part 4].
3. **A countable outcome.** Reconciled, filed, scheduled, resolved, collected. Outcome pricing has only worked where the outcome can be counted, and the one product with public per-outcome pricing is a customer-service agent at $0.99 per resolution [strategy][nontech-startups].
4. **Data already behind connectors.** Email, calendar, CRM, ticketing, accounting and document stores all have MCP servers or equivalents; the integration cost is paid [protocols].
5. **A buyer who already pays for governance.** Enterprises buy governance from vendors they have paper with, and agent security is their stated blocker [strategy]. The wedge should live where a governance budget exists.

Three candidates score well on all five, and the program did not research them deeply enough to rank them; that is the work the next step covers.

- **Accounts payable and receivable operations for mid-sized companies.** Invoices, purchase orders, vendor emails, payment holds. Checkpoint: the payment release. Outcome: reconciled and paid on time. Blast radius is money, which makes the consequence classes vivid and the value of an undo ledger obvious.
- **Sales operations administration.** CRM hygiene, quote drafts, renewal follow-ups. Checkpoint: the send and the quote approval. Outcome: renewals closed, records correct. The buyer already pays Salesforce for governance, which is also the risk.
- **Internal service-desk operations for small and mid-sized organizations.** Access requests, onboarding and offboarding, license changes. Checkpoint: the access grant. Outcome: tickets resolved within policy. Identity is the product here, which aligns with the layer, and it is the workflow the suites are buying into fastest, which is the risk.

The choice among them should be made by interviews, not by this document.

## Why a lab would not just do this

The question every position has to answer. The honest answer is that a lab could build every component, and two of them already build several. The defense is not technical.

- **Neutrality is structurally against the labs' interest.** Each lab's harness binds the person to its engine and its surfaces, and the labs use pricing and terms to protect that binding [strategy][labs]. A layer whose value is that policy and memory travel across engines is a layer they will build last and half-heartedly. That is the same shape as identity providers, secrets managers and observability vendors in earlier platform shifts, and those companies existed because the platforms would not be neutral.
- **The suites are the real competitor, not the labs.** Microsoft's Agent 365 is exactly this layer for Microsoft's surfaces, and Salesforce, ServiceNow and Notion bundle approval, credential and memory layers into their products [nontech-labs][nontech-startups]. The startup's answer is the same: cross-suite, cross-engine, owned by the customer.
- **The wedge earns the right.** A general authority layer with no customers is a slide. A layer that already governs real payments or real access grants for paying customers has the evidence the labs lack: how non-technical people behave when given consequence classes, ledgers and read-backs. Nobody has that evidence today, because nobody has shipped the combination [design].
- **Speed in a second-priority layer.** The labs ship the runtime weekly; the authority layer for non-developers moves slower and inconsistently, and the seven-vendors-seven-models finding is the proof [nontech-labs]. A company that does only this can move faster than a team for whom it is one of forty features.

What this defense does not do: it does not stop a lab from bundling a good-enough version into its own surface and winning on distribution, which is what happened to standalone browsers and orchestrators. The bet is that neutrality and customer ownership matter to buyers; the strategy document's evidence that enterprises are multi-model and buy governance supports that bet without proving it [strategy].

## What would falsify this

- A lab or a suite ships policy, memory and an undo ledger that travel across engines and surfaces, and customers accept it despite it belonging to one vendor.
- Interviews find that non-technical operators do not want to see consequence classes or ledgers at all, only outcomes, in which case the layer is invisible plumbing and the company is the vertical alone.
- The wedge's outcome cannot be counted cleanly enough to price, or the native checkpoint turns out to be bypassed in practice.
- Model improvements make verification cheap enough that vendors bundle proof of work for free across document and data work, removing the verification half of the product.
- A platform decision cuts off engine access on acceptable terms, as happened to third-party harnesses on consumer subscriptions in April 2026 [strategy].

## Risks the plan carries

- **Liability.** Whoever owns the authority layer owns the blast radius. The incident record shows what that costs a company with weak boundaries [messaging][runtime].
- **Bundling.** The suites buy the best standalone agents; the exit may be the plan, but it is not a default the founder controls.
- **Timing.** The task horizon doubles every three to four months [academic]; a layer designed for today's failure modes must be re-simplified as often as an inner harness.
- **Evidence quality.** Much of this corpus was verified through search excerpts and GitHub mirrors because primary sites were blocked in this session; the verification notes in each document say what could not be opened. Treat vendor numbers as vendor numbers.

## Next steps, before any market sizing

1. **Twenty interviews** with operators in the three candidate workflows, structured around delegation regret, approval load and what proof they would need to stop checking; choose the wedge on that evidence.
2. **A prototype of the outer harness** over rented engines (the Claude Agent SDK, Codex's app-server and Managed Agents), with one entry surface (Slack or Teams) and one generated review page, implementing grants, consequence classes, the ledger and proof of work for the chosen workflow.
3. **An evaluation that does not exist yet**: did the operator's task get done, at what cost, with how many interruptions and how much regret. Publish it; nobody measures this [runtime][academic].
4. **Measure the four numbers** that decide the thesis: approval load per task, delegation-regret rate, time to proof, cost per completed task. Compare against the incumbents' agent modes on the same tasks.
5. **Then size the market**, with the wedge chosen and the four numbers in hand.

## What this recommendation is not

- Not a better chat app, a new browser, a device or a desktop app. All four have fresh corpses.
- Not an engine. Engines are free or rented.
- Not "we learn the harness automatically" as a moat. The best independent evidence says learned harnesses overfit and transfer poorly [academic].
- Not a claim that the CLI is dead. It is the engine, and developers keep it.

## What this means for the thesis

Supports: the thesis survives as a design thesis and as a narrow business thesis. Contradicts: the broad version, a startup as the new default harness for everyone, does not survive the evidence. Nuance: the path from the narrow position to the broad one exists, but it runs through a vertical wedge and through neutrality, and it is contested by Microsoft at one end and by the labs at the other.

## Open questions and unverified claims

- Whether buyers will pay for neutrality is inferred from the strategy document's investor and enterprise evidence, parts of which are unverified.
- The three candidate wedges are the coordinator's reading of the user documents; none was researched as a market.
- The per-resolution pricing figure and several revenue figures are vendor or press claims recorded in the source documents with their status.
