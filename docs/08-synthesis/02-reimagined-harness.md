# The reimagined harness: a stable outer harness that the person owns, and an inner harness the agent builds on the fly

*Research program: agent harnesses. Synthesis written 2026-09-08 by the coordinator. Status: draft, red-team pending. Citations in square brackets name research documents in this repository (see `01-findings.md` for the key). This is a design proposal grounded in the findings; each component carries an evidence grade.*

## What this document answers

- What a harness built for both developers and non-technical people should look like, part by part, given the eleven findings.
- What in that design already exists, what is genuinely new, and how strong the evidence is for each piece.
- Where "on the fly" belongs: which parts of the harness should be generated per task, and which must never be.

## The idea in one paragraph

Today's harnesses are vehicles with a fixed body: you sit in the terminal or in the app, and the engine, the controls and the dashboard are welded together by whoever built it. The evidence says the engine has become a commodity that the labs give away or rent, the body styles are multiplying, and the parts that decide whether an agent is safe and useful (who it acts as, what it may do, how you check its work, what it remembers, what it costs, when it interrupts you) are the parts nobody has standardized [findings 1, 2, 5, 7]. So the reimagined harness is not a vehicle. It is a driver's licence, a garage and a dispatcher. The licence is the person's authority layer: identity, scoped grants, approval rules, an undo ledger, a budget. The garage is a rented runtime: whichever engine and sandbox fit the task. The dispatcher assembles a vehicle for each trip from those parts, projects a thin view of it into whatever surface the person is already in, and reports back with proof of what it did. The person owns the licence and the logbook; everything else is disposable.

## Architecture

```
  People, wherever they already are
  chat (Slack, Teams, iMessage, WhatsApp where allowed) · office suite · OS assistant
  browser page the agent generates for review · phone push · terminal (developers)
        │   thin projections: MCP Apps, AG-UI, A2UI, ACP, messaging channels
        ▼
  ┌───────────────────────────────────────────────────────────────────┐
  │ OUTER HARNESS  (stable; owned by the person or the organization)  │
  │  identity and grants · consequence classes and approval policy    │
  │  undo ledger · proof of work and provenance · portable memory     │
  │  interruption policy · budgets in money · audit trail             │
  └───────────────────────────────────────────────────────────────────┘
        │   engine adapters: Claude Agent SDK, Codex app-server, Managed Agents,
        │   Copilot SDK, Agent Client Protocol
        ▼
  ┌───────────────────────────────────────────────────────────────────┐
  │ INNER HARNESS  (assembled per task by the agent; discarded after)  │
  │  loop · prompts · tool selection from the MCP catalog and skills   │
  │  workflow (dynamic subagents) · the generated review view          │
  └───────────────────────────────────────────────────────────────────┘
        │
        ▼
  RUNTIME (rented, interchangeable)
  cloud or local sandbox · credential-holding egress proxy · durable sessions
```

The outer harness is the product. The inner harness is generated. The runtime is rented. The surfaces are projections.

## The seven parts, reimagined

The program's definition of a harness has seven parts. Here is what each becomes, what exists today, and how strong the evidence is. Grades: **strong** means several verified documents agree and there is independent evidence; **medium** means the documents agree but the evidence is mostly vendor claims or single studies; **weak** means an inference from gaps.

### 1. Loop: rented, never built

The loop is a few hundred lines and every lab now ships one behind a stable interface: the Claude Agent SDK, Codex's app-server, Managed Agents, the Copilot SDK, and any agent that speaks the Agent Client Protocol [anatomy][labs][protocols]. The reimagined harness holds adapters to several engines and lets the task, the budget and the organization's policy choose among them. It never owns a loop, because a loop it owns will be worse than the vendor's within a model generation [industry][academic].

What exists: all of the adapters. What is new: nothing; the decision is to abstain. **Evidence: strong.**

### 2. Tools: a catalog assembled per task

MCP won the tool layer and went stateless in July 2026; skills and instruction files are near-identical across vendors [protocols][matrix]. The reimagined harness keeps a catalog of connectors and skills, tagged with the consequence class of each action (see part 4), and lets the agent assemble the subset a task needs. Where a tool does not exist, the agent may write one inside the sandbox, but a generated tool inherits the most restrictive consequence class until a person promotes it.

What exists: catalogs, marketplaces, code mode. What is new: consequence tags on tools, and the promotion rule for generated tools. **Evidence: medium** (the catalogs are proven; consequence tagging is an inference from the approval findings).

### 3. Context and memory: portable and inspectable

Memory is the least settled layer, vendor benchmarks contradict each other, and Anthropic's memory now lives in its own server-side store [runtime][anatomy]. Non-technical users lose most to missing memory across tools [nontech-startups][nontech-users]. The reimagined harness keeps the person's memory (preferences, organizational context, policies, past decisions) in a store the person owns, in a format the person can read, tagged with provenance, and injects it into whichever engine runs the task. This is the same move instruction files made for repositories, applied to a person and an organization instead of a codebase [anatomy].

What exists: instruction files, vendor memory stores, third-party memory libraries. What is new: ownership by the person rather than the vendor, portability across engines, provenance tags. **Evidence: medium.**

### 4. Permissions and safety: the core of the product

This is the part every document points at. Seven vendors show seven approval models and none shows the user what an action can cost or destroy [nontech-labs]; approvals in chat are text-grade [messaging]; policy does not travel across surfaces [protocols]; humans approve 93% of prompts and catch 13.6% of dangerous commands against 89% for a classifier (vendor figures) [runtime]; the failures non-technical users report are about authority, not accuracy [nontech-users].

The reimagined design has five pieces.

- **The agent is a principal.** It has its own identity and acts under grants: scope (which systems, which records), time (until when), budget (how much), and consequence ceiling (the worst class of action it may take without asking). Microsoft's per-agent directory identities and Windows' separate agent account are the nearest existing things [nontech-labs][os]; routines that "appear as you" are the anti-pattern [voice].
- **Consequence classes, not command lists.** Every action is classified by reversibility, blast radius and cost: reversible inside the system (edit a draft, stage a change), compensable (send an email that can be followed by a correction, place an order that can be cancelled), irreversible (payment, deletion, publication to the world). Policy is written against classes, so it means the same thing in every tool and on every surface. A classifier, like Claude Code's auto mode, proposes the class; the person's policy decides what happens [anatomy][runtime].
- **Approvals that fit the class.** Reversible actions run without asking and appear in the ledger. Compensable actions run with a preview and a delay window in which they can be pulled back. Irreversible actions require a confirmation that reads back the consequence in plain words, works without a screen, and can be batched when several share a cause. Draft-then-send, hold-then-release and stage-then-apply are the native checkpoints the design leans on; the messaging document notes that email agents come closest to a usable approval boundary for exactly this reason [messaging].
- **An undo ledger.** Every action is recorded with what it changed and how to reverse or compensate it. Where the underlying system supports transactions or drafts, the harness uses them; where it does not, it records the compensating action and who to notify. No shipping product offers this across tools [messaging][nontech-startups].
- **Safe by structure.** Containment in a sandbox, credentials held by an egress proxy outside the boundary, capability scoping at the read boundary so that content the agent reads cannot widen what it may do. These are the defenses the runtime document found actually hold [runtime].

What exists: classifier-gated modes, per-agent identities, per-tool approval settings, sandboxes and proxies, draft-first email agents. What is new: consequence classes as the unit of policy, grants with a consequence ceiling, the cross-tool undo ledger, and policy that is the same on every surface. **Evidence: strong for the problem, medium for the design** (the pieces exist separately; nobody has shipped them together, so there is no evidence yet that people will use them well).

### 5. Runtime: rented, with the local-versus-cloud choice made explicit

Sandboxing is a commodity with a dozen suppliers; durable cloud sessions, scheduling and self-hosted runners come from the labs and clouds [runtime][labs]. The reimagined harness rents them. The one decision it surfaces is where the agent runs, because local means the agent has the person's logins and cloud means the vendor holds the session and the meter [nontech-labs]. The design default for non-technical users is a cloud runtime with connector-scoped credentials; for developers it is whatever the repository needs.

What exists: everything. What is new: making the location a policy decision the person can understand. **Evidence: strong.**

### 6. Surface: many thin projections and one generated review page

The surfaces are settled: chat inside existing tools, the office suite, the OS assistant, a phone notification, a desktop or web app for review, and the terminal for developers [findings 2, 3][adoption]. The reimagined harness owns none of them. It projects into them through the protocols that now exist (messaging channels, MCP Apps, AG-UI, A2UI, ACP) and adds one thing of its own: a generated review page for the moments that matter, following the pattern the generated-UI document calls "generate the view, fix the controls" [generated-ui]. The page shows the proposal before an irreversible step, the consequence read-back, the proof of work after, and the undo control, with spreadsheet-grade provenance for documents and data. The controls are fixed and auditable; only the view is generated.

What exists: artifacts and generated pages as output, chat relays of approvals, agent-manager windows for developers. What is new: a generated page whose controls are fixed policy objects rather than generated code, used to steer rather than to display. **Evidence: medium** (generated pages are proven as output; using them for control is untested).

### 7. Orchestration: generated workflows inside a fixed interruption policy

Dynamic workflows, subagents and scheduled runs are now standard, and the labs let the model write the orchestration [labs][anatomy]. The loop has left the foreground; the missing piece is policy about when the agent may interrupt the person, how it confirms without a screen, and how an unattended run is scoped [voice]. The reimagined harness lets the agent generate the workflow for each task and holds a fixed interruption policy above it: a small model of presence (is the person here), urgency (does this need them now) and reversibility (can it wait), with explicit schedules preferred to unrequested pushes, following the one natural experiment on record [voice]. For developers it adds the supervision layer they ask for: a review queue, a fleet view, budgets and permissions that scale past ten agents [dev-users].

What exists: routines, scheduled tasks, push toggles, agent views. What is new: an interruption policy as a first-class object, and supervision that spans engines. **Evidence: medium.**

## Where "on the fly" belongs

The repository's name asks whether the harness should be built at runtime. The evidence gives a split answer.

Generate per task, and discard: the inner harness. The loop configuration, the prompt, the tool subset, the workflow shape and the review view. The research shows automated harness design works on benchmarks and overfits them [academic]; the practitioner writing says every hand-built inner component expires with the next model [industry]; and the products that build workflows from a sentence, a document or a call recording (Sierra's Ghostwriter, n8n's assistant, Relevance's Invent, ServiceNow's Otto) are the clearest commercial evidence that on-the-fly assembly is where the inner harness is going [nontech-startups]. Generating it per task is also the only way to keep pace with a task horizon that doubles every three to four months [academic].

Never generate: the outer harness. Identity, grants, consequence classes, the ledger, the memory store, the interruption policy and the budget must be stable, inspectable and the same on every surface, because they are what the person trusts. A generated permission model is the OpenClaw failure mode at scale [messaging][runtime].

## Two walkthroughs

**An operations manager, no code.** She tells the agent, in the Slack channel where the finance team works, to reconcile last month's vendor invoices against purchase orders and flag anything unusual. The dispatcher assembles an inner harness: the accounting connector, the purchasing connector and the email connector from the catalog, a workflow with one subagent per vendor, and a review view. The grants say the agent may read both systems, may create draft correction entries (reversible), may draft emails to vendors (compensable, held for review), and may not post journal entries or send payments (irreversible, ceiling exceeded). It runs in a cloud sandbox with connector-scoped credentials; nothing it reads can widen what it may do. It works unattended; the interruption policy holds questions until it has a batch or until a deadline. When it finishes, she gets one message with a link to a generated page: what matched, what did not, the three draft corrections with the evidence next to each, the two vendor emails ready to send, and one button per compensable action plus a single approve-all for the reversible ones. Everything is in the ledger; each draft can be undone for as long as it is a draft. The proof of work is the page itself, with each number linked to its source row. Nothing here requires her to understand a permission prompt.

**A developer running five agents.** He starts five tasks from the terminal, each in its own worktree on a rented engine. The outer harness gives each agent the same grants: write inside the worktree (reversible), open pull requests (compensable), merge to main (irreversible, ceiling exceeded). His fleet view shows cost per task in dollars, the review queue in order of consequence, and each pull request with proof of work: the tests that ran, the diff, and the second-model review. He approves merges from his phone through a read-back of what each merge changes. The engines differ per task, chosen by cost and policy; the supervision layer is the same. This is the layer the developer document says the terminal cannot provide and the labs have only begun [dev-users].

## What is new, honestly

| Component | Nearest existing thing | The gap |
|---|---|---|
| Agent as a principal with grants | Microsoft's per-agent directory identities; Windows agent accounts | No consequence ceiling; no portability across vendors |
| Consequence classes | Claude Code's auto-mode classifier; per-tool approval settings | Not the unit of policy; not the same across tools or surfaces |
| Cross-tool undo ledger | Drafts in email agents; git for code | Nothing spans tools; nothing shows compensating actions |
| Proof of work for non-code | Code Review in Claude Code; test output | Nothing for documents, spreadsheets, records |
| Portable memory owned by the person | Instruction files; vendor memory stores | Vendor-bound; not inspectable by non-experts |
| Generated review page with fixed controls | Artifacts, Claude Design, ChatGPT apps | Output only; controls are generated or absent |
| Interruption policy | Push toggles, presence file, idle-check caps | No model of urgency or reversibility |
| Budgets in money | Copilot Cowork credits with caps; Claude Code cost reports | Not per task across engines |
| Supervision across engines | Cursor 3, Devin Command Center, Symphony | Single-engine, developer-only |

## What this means for the thesis

Supports: the design is the thesis restated with the split the evidence demands. The harness is the bottleneck, and the parts to reimagine are the outer ones; the inner harness should be built on the fly. Contradicts: the design does not reimagine the surface or the engine, because the evidence says both are settled and owned by the labs; anyone expecting a new body style will not find one here. Nuance: every component in the table has a nearest existing thing owned by a lab or by Microsoft, so the design's defensibility rests on combining them across vendors and surfaces, which is the question the recommendation takes up.

## Open questions and unverified claims

- No study shows whether non-technical people will act correctly on consequence read-backs; the 93% approval rate for prompts is a warning that any confirmation can become a reflex [runtime].
- Compensating actions are not undo; the design's ledger is only as good as the underlying systems' support for drafts, holds and cancellations.
- The cost of generated review pages is unmeasured; vendors say they are more token-intensive than text [generated-ui].
- The interruption model is a proposal; the only evidence is one retired product and vendors' own thin controls [voice].
