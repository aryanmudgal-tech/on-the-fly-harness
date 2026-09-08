# Harness research program: design

*2026-09-08. Status: approved by the author's answers to the brainstorming questions; execution in progress.*

## Goal

Produce a startup-grade research corpus and a recommendation that reimagines the **default agent harness** so it serves developers and non-technical people. Readable by a software engineer who is new to agents.

## Decisions taken in brainstorming

| Decision | Choice | Consequence |
|----------|--------|-------------|
| Meaning of "harness" | Broad: everything around the model | The landscape must cover every harness type, not only runtime-generated ones |
| Purpose | Startup thesis | Synthesis must state a position, the wedge, why the labs would not crush it, and what would falsify it |
| Reader | Software engineer, new to agents | Code and architecture welcome; agent-specific concepts get an analogy first |
| Surfaces in depth | Messaging, browser workspace with generated UI, voice and ambient, OS-level | Four surface deep dives, each ending in a verdict for developers and for non-technical users |
| Market sizing | Deferred | Strategy doc covers value capture, not TAM |

## Approaches considered

1. **One deep-research agent, one long report.** Fast to start. Shallow, hard to verify, single point of view. Rejected.
2. **Fan-out by workstream, verification pass per document, synthesis owned by the coordinator.** Parallel, each document self-contained and sourced, claims checked by a second agent before synthesis. **Chosen.**
3. **Build first, learn by doing.** Premature: the author wants a recommendation before a build. Deferred; the synthesis may propose a prototype as the next step.

## Shared vocabulary: the seven parts of a harness

Every document uses the same anatomy so the comparisons line up:

1. **Loop:** model call, tool calls, results, repeat; plus how it stops and how a human steers it.
2. **Tools and integrations:** what the agent can act on.
3. **Context and memory:** what goes into the context window and what persists across sessions.
4. **Permissions and safety:** approvals, sandboxing, injection defenses, blast radius.
5. **Runtime:** where the loop runs (local process, cloud sandbox, browser, phone), durability, background execution.
6. **Surface:** what the human touches (CLI, IDE, desktop app, web, chat app, voice, OS).
7. **Orchestration:** subagents, workflows, scheduling, multi-agent coordination.

## Deliverables

| Path | Content |
|------|---------|
| `docs/01-primer/` | Anatomy of a harness from first principles; history 2022 to 2026 |
| `docs/02-landscape/` | Developer harnesses (labs, independents, comparison matrix); non-technical surfaces (big vendors, startups and enterprise); adoption evidence |
| `docs/03-building-blocks/` | Protocols and standards; runtime, safety and infrastructure |
| `docs/04-research/` | Academic research; industry engineering writing |
| `docs/05-surfaces/` | Deep dives: messaging, browser workspace with generated UI, voice and ambient, OS-level |
| `docs/06-users/` | Developers; non-technical users |
| `docs/07-strategy/` | Value capture and competitive dynamics (no TAM) |
| `docs/08-synthesis/` | Principles; the reimagined harness; recommendation; red-team critique |
| `docs/sources.md` | Consolidated bibliography |

## Method

1. **Research fan-out.** One agent per document, each with a self-contained brief (`docs/superpowers/plans/research-brief-template.md` plus a scope block). Agents write directly to their file and touch nothing else.
2. **Verification pass.** A second agent per document checks the highest-stakes claims against primary sources, corrects in place, and appends verification notes.
3. **Matrices.** Comparison documents are built only from verified documents.
4. **Synthesis.** The coordinator reads everything and writes principles, the reimagined harness, and the recommendation. A red-team agent attacks the recommendation before it is final; the critique is published alongside it.
5. **Commits** after each stage, authored as the repo owner.

Execution uses both direct subagents and the Workflow tool (pipelines of research then verification), as the author asked.

## Quality bar

- Every claim about a product, paper, number or event carries a source URL and a month/year.
- Unknowns are written down as unknowns. No invented numbers, quotes or URLs.
- Vendor claims are labelled as vendor claims.
- The author's thesis is tested, not assumed. Each document ends with "what this means for the thesis" and says where the thesis is wrong.
- Because the field moves monthly, documents are dated and note anything that changed recently.

## Out of scope

Market sizing; writing product code; exhaustive pricing tables beyond what informs the thesis.

## Success criteria

1. A reader new to agents can explain what a harness is and how the main ones differ after the primer and landscape.
2. Each of the four surfaces has an evidence-backed verdict for developers and for non-technical users.
3. The recommendation names a specific reimagined harness, the wedge user, why the labs would not simply do it, and what evidence would falsify it.
4. Every research document has passed verification, with unresolved items listed rather than hidden.
