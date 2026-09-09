# The reimagined harness: the vendor's engine, the organization's authority, and an independent layer that proves the work

*Research program: agent harnesses. Synthesis written 2026-09-08 by the coordinator; revised 2026-09-09 after the red-team review in `04-red-team.md`. Status: final. Citations in square brackets name research documents (key in `01-findings.md`). Each component carries an evidence grade.*

## What this document answers

- What a harness built for both developers and non-technical people should look like, part by part, given the findings.
- What in that design exists already, what an outsider can own, and how strong the evidence is for each piece.
- Where "on the fly" belongs, and where it does not.

## What changed after the red team

The first version proposed a startup-owned "outer harness" that would hold identity, consequence-class permissions, an undo ledger, portable memory and an interruption policy, and would generate the inner harness per task. The red team showed, from the corpus, that most of those pieces already ship (Dust's tool stakes, MCP's risk annotations, Microsoft's per-agent identities with sign-off, Copilot Cowork's per-task caps, the Agent SDK's budget cap), that the identity provider is where the labs are sending policy, that an outside layer cannot enforce anything on a hosted engine, that a ledger cannot undo a released payment, that per-task regeneration of the inner harness contradicts the research and the cache economics, and that the whole stack multiplies rented tokens. The revised design keeps what survived: the split, the vendor's engine, the settled surfaces, consequence classes as a vocabulary, and verification as the one durable thing an outsider can own.

## The idea in one paragraph

The car analogy from the brief still holds, with a correction about who owns which part. The engine and the body styles belong to the labs and the suites, and they now come with their own seatbelts: classifier-gated permissions, per-agent identities, spend caps. The organization's identity provider is becoming the licensing authority that says what any agent may reach. What nobody provides is an inspector who is not the manufacturer: an independent check, after each trip, of what the vehicle actually did, whether the cargo matches the manifest, and which of its actions cannot be taken back, written into the logbook the owner already reads. The reimagined harness is that inspector, delivered inside every existing harness and every existing document surface, holding no keys and driving nothing.

## Architecture

```
  People, wherever they already are
  chat inside existing tools · office suite (the grid, the document) · OS assistant · pull requests · terminal
        │
        ▼
  ┌────────────────────────────────────────────────────────────────────────┐
  │ VENDOR ENGINE AND ITS OWN CONTROLS  (rented; Claude Code, Codex,        │
  │ Cowork, Copilot, open harnesses)                                        │
  │  loop · prompts · tool catalog · classifier-gated permissions · budgets │
  │  hooks and traces (the observation points an outsider can use)         │
  └───────┬──────────────────────────────────────────────┬─────────────────┘
          │ identity, grants, tenant policy               │ hook events, traces, artifacts
          ▼                                               ▼
  ┌─────────────────────────────┐        ┌───────────────────────────────────────────┐
  │ ORGANIZATION'S AUTHORITY    │        │ INDEPENDENT VERIFICATION LAYER            │
  │ (identity provider, suite   │        │ (reads everything, acts on nothing)       │
  │ admin: Entra, Cowork RBAC,  │        │  proof of work per task: what changed,    │
  │ enterprise-managed MCP auth)│        │  claims tied to sources, checks run,      │
  │  who the agent is · what it │        │  what could not be verified · actions by  │
  │  may reach · spend caps     │        │  consequence class · rendered into the    │
  └─────────────────────────────┘        │  grid, the document, the pull request     │
                                         │  optional pre-action gate where a hook    │
                                         │  exists (local harnesses only)            │
                                         └───────────────────────────────────────────┘
        │
        ▼
  RUNTIME (rented): cloud or local sandbox · credential-holding egress proxy · durable sessions
```

The engine is the vendor's. Authority is the organization's, administered through the identity provider and the suite. The independent layer is the product an outsider can own. The runtime is rented.

## The seven parts, revised

Grades: **strong** means several verified documents agree and there is independent evidence; **medium** means the documents agree but the evidence is mostly vendor claims or single studies; **weak** means an inference from gaps.

### 1. Loop: the vendor's, never regenerated

Every lab ships its loop behind a stable interface (the Claude Agent SDK, Codex's app-server, Managed Agents, the Copilot SDK, the Agent Client Protocol) [labs][protocols], and the corpus records double-digit swings from loop configuration alone [industry][history]. Use the vendor's tested default; re-simplify on upgrades; keep a baseline that postmortems can compare against. Do not generate the loop or the prompt per task: the best independent evaluation finds generated harnesses behind hand-built ones with unstable transfer, and per-task regeneration defeats prompt caching, which practitioners call the most important production metric [academic][industry]. **Evidence: strong.**

### 2. Tools: the vendor's catalog, read through one risk vocabulary

MCP won the tool layer and its specification already carries read-only and destructive annotations that the maintainers call a risk vocabulary; Dust tags every tool with a stake and defaults external tools to the highest one [protocols][nontech-startups]. The independent layer does not build a catalog. It reads the annotations and stakes each harness exposes, normalizes them into one consequence vocabulary (reversible inside the system, compensable, irreversible), and uses that vocabulary to describe what a run did. Where a tool carries no annotation, the layer records the action as unclassified, which is itself a finding. **Evidence: medium** (the annotations exist; their coverage is thin).

### 3. Context and memory: the organization's record, read-only by default

Memory is the least settled layer, vendor benchmarks disagree, and a memory store is an injection sink: a successful injection can write into it and later sessions read it as trusted [runtime]. Developers reject proprietary stores of project state [dev-users]. So the layer keeps no memory of its own beyond what verification needs (which artifacts were checked, against which sources, with what result). Organizational memory stays in the systems that already hold it, with read-only defaults and provenance tags, administered by the organization. **Evidence: medium.**

### 4. Permissions and safety: owned by the vendor and the identity provider; described, not enforced, by the outsider

Gating is being done twice already: at the engine by classifiers and modes, and at the tenant by identities, sign-off rules, data-loss prevention and spend caps [runtime][nontech-labs]. Policy is moving into the identity provider [nontech-labs][protocols]. An outside layer cannot interpose on a hosted engine's built-in tools, and hooks do not yet travel to hosted surfaces, so any enforcement it claims there is advisory [labs][protocols][anatomy]. The honest design therefore separates two things. First, description: every run ends with a consequence summary, the actions taken by class, the principal that took them, and the grants in force, so that an auditor and the person can see what happened without reading a trace. Second, optional gating where a real enforcement point exists: on local harnesses, a pre-action hook can block an irreversible class until a person confirms with a read-back of the consequence, and it can only tighten the vendor's policy, never loosen it, following OpenClaw's rule [messaging]. Compensating actions are recorded as facts, not sold as undo; a released payment is not reversible and the design says so. Safety by structure remains the vendor's and the organization's job: containment, credential-holding proxies, and breaking the trifecta per task by removing the external-communication leg while untrusted content is being read [runtime]. **Evidence: strong for the problem, medium for the description layer, weak for gating.**

### 5. Runtime: rented, with the constraints named

Sandboxing is a commodity; durable sessions, scheduling and self-hosted runners come from the labs and clouds [runtime][labs]. The layer rents them and names the constraints the recommendation must carry: Managed Agents is not eligible for zero data retention or a health-data agreement, the Agent SDK's terms forbid third parties from offering claude.ai login, and the subscription route changed five times in eight months [labs][runtime][strategy]. Local means the agent has the person's logins; cloud means the vendor holds the session and the meter [nontech-labs]. A verification layer that only reads traces and artifacts can run wherever the customer's data policy allows, which is a procurement advantage over anything that holds credentials. **Evidence: strong.**

### 6. Surface: the grid, the document and the pull request are the review surfaces

The surfaces are settled: chat inside existing tools, the office suite, the OS assistant, a phone notification, a desktop or web app to review in, the terminal for developers [findings 2, 3][adoption]. The office suite is now agentic and the add-ins are generally available [nontech-labs], and the grid is the one surface where a non-expert can check work cell by cell [generated-ui]. Generated pages are proven as output, not as control, and no HTML surface offers spreadsheet-grade provenance [generated-ui]. So proof is rendered where checking already happens: a provenance column in the sheet, tracked changes with sources in the document, a check report on the pull request. A generated view is used only for what the grid cannot show. Requests enter through authenticated, individual channels, never a shared room, because no product distinguishes the requester's instructions from bystanders' text inside a thread, and the vendors' own documentation says so [messaging]. **Evidence: medium.**

### 7. Orchestration: the vendor's workflows, the vendor's interruption controls, one report per run

Dynamic workflows, subagents and scheduled runs are standard and the labs let the model write the orchestration [labs][anatomy]; interruption controls are thin, their evidence is anecdotal, and they are the kind of judgment part vendors absorb [voice][anatomy]. The layer adds nothing here except the report: one proof per run, delivered when the run ends, with explicit schedules preferred to unrequested pushes. For developers running many agents it adds the piece the terminal cannot provide and the vendors' own reviewers do not: an independent check per pull request that can be made a merge requirement by policy the organization sets in its platform [dev-users]. **Evidence: medium.**

## Where "on the fly" belongs

Assembly per task is real and already the vendors' job: dynamic workflows choose subagents at runtime, and the commercial builders (Sierra's Ghostwriter, n8n's assistant, Relevance's Invent) build a workflow once from a description, then run it [labs][nontech-startups]. What should be chosen per task in the verification layer is the set of checks, which depends on the artifact: tests and diffs for code, source-row ties and formula provenance for a sheet, citations and change tracking for a document. What must never be generated per task is the checker's own baseline, its vocabulary, or the loop it observes.

## Two walkthroughs, revised

**An operations manager, no code.** She opens last month's invoice reconciliation in Excel with the vendor's add-in and asks the agent to match invoices to purchase orders and propose corrections. The agent runs under the identity and grants her organization configured in its directory; it can read the accounting and purchasing systems and can draft entries in a staging sheet, and it has no send or payment capability while it is reading vendor emails, which breaks the trifecta for that phase. Its proposals land in the sheet as a proposal column. The independent layer, subscribed to the run through the harness's hooks, writes a proof column beside it: each proposed correction tied to the invoice line and purchase-order row it rests on, each check that ran, the two entries it could not verify, and a summary line stating that the run took reversible actions only. She reviews in the grid she already uses, accepts the corrections she can see the evidence for, and the payment run stays a human decision in the finance system, where it belongs. Nothing here asks her to understand a permission model, and nothing in the layer could have sent an email or moved money.

**A developer running five agents.** He starts five tasks from the terminal on whichever engines the organization allows. Each engine's own permissions and the organization's tenant policy govern what the agents may do. The independent layer runs as a stop hook on each session and produces a check per pull request: the tests that actually ran, the diff against the task, claims in the description tied to code, and what it could not verify, from a model and context that did not write the code. The organization's platform makes that check a merge requirement, which the vendors' own reviewers are built not to be [dev-users]. His fleet view shows cost per task in dollars from the vendors' meters and the check status per pull request.

## What exists, and what an outsider can own

| Component | Who already ships it | What an outsider can own |
|---|---|---|
| Agent as a principal with grants | Microsoft's directory identities and Scout sign-off; Windows agent accounts; Cowork role-based access; enterprise-managed MCP authorization | Nothing; read it |
| Consequence classes | MCP read-only and destructive annotations; Dust's tool stakes with a high default for external tools | The normalized vocabulary and its use in proof |
| Spend control | Copilot Cowork per-task credits with tenant, group and user caps; the Agent SDK's budget cap; Claude Code cost reports | Cost per verified outcome, reported alongside proof |
| Classifier-gated approvals | Claude Code auto mode; Claude in Chrome's per-action classifier | A pre-action gate on local harnesses that can only tighten policy |
| Undo | Drafts in email agents; git for code; nothing for released actions | Honest description of what was compensable and what was not |
| Proof of work for code | Claude Code's Code Review (never blocks merging); Copilot and Cursor reviewers; Qodo, CodeRabbit | Independence: a check from a party that did not write the code, made a merge requirement by the organization |
| Proof of work for documents and data | Nothing in the corpus | The whole thing |
| Review surface | Excel, Word and PowerPoint agents and add-ins, generally available | Provenance rendered into them |
| Evaluation of real outcomes | AutomationBench for business tasks; nothing on the operator's own work | The corpus of verified and unverified artifacts, as a by-product |

## What this means for the thesis

Supports: the harness is the bottleneck, the surface is already separating from the engine, and the parts non-technical people need are missing; the design keeps the split and builds only in the outer layer. Contradicts: the design no longer reimagines the surface, the engine, the runtime or the permission model, because the evidence says each is settled and owned; anyone expecting a new body style, or a startup-owned control plane, will not find one here. Nuance: what an outsider can own is narrower than "the harness" and wider than a feature: an independent proof of work across engines and document surfaces, which nobody in the corpus provides for non-code work.

## Open questions and unverified claims

- Whether an independent check changes behaviour when the reader cannot evaluate evidence is untested; the corpus says formatted plausibility earns misplaced confidence [nontech-users].
- What "verified" means for judgment-heavy documents is undefined; the design starts with data and records, where claims tie to rows.
- Hook coverage on hosted surfaces is incomplete, so the layer's reach on cloud sessions depends on vendors exposing traces [protocols][labs].
- Cost per check is unmeasured; reading is cheaper than generating, but a full-trace review by a strong model is not free.
