# Dispatch log

Running record of what was dispatched, when, and what came back. Times are UTC, 2026-09-08.

| Time | What | Mechanism | Status |
|------|------|-----------|--------|
| 19:00 | Task 1 primer/anatomy, Task 2 primer/history, Task 19 strategy/value-capture | Direct subagents (research); verifiers to follow | running |
| 19:00 | Workflow A: Tasks 3, 4 → 5 (matrix), 6, 7, 8, 9, 10; each research agent piped into a fact-checker | Workflow tool, 15 agents | running |
| 19:00 | Workflow B: Tasks 11 to 18; each research agent piped into a fact-checker | Workflow tool, 16 agents | running |

Rules in force: research agents write only their own document and never run git; the coordinator commits results as they land, authored as the repo owner.

## Environment constraint found at 19:10

The session's network egress policy blocks most primary-source domains (openai.com, anthropic.com, arxiv.org, wikipedia.org, techcrunch.com, news.ycombinator.com, simonwillison.net, modelcontextprotocol.io, huggingface.co, developers.googleblog.com). Reachable: raw.githubusercontent.com, docs.claude.com, microsoft.com, code.claude.com, package registries. Web search still works, so agents can see search snippets but often cannot read the primary page. Consequence: some claims rest on snippets and secondary coverage; every document labels those as unverified, and the verification notes say which sources could not be opened. Fix for a future session: widen the environment's network policy (see https://code.claude.com/docs/en/claude-code-on-the-web).

## Re-dispatch at 19:55

Two problems with the workflows surfaced from their journals. First, the container has 4 cores, which caps each workflow at 2 concurrent agents, so 31 agents would have serialized into roughly two hours. Second, all agents inside one workflow share a single web-search allowance; it ran out after the first two documents in each workflow, so the later documents (non-technical surfaces from big vendors and from startups, messaging, browser with generated UI, voice and ambient) were written from background knowledge plus GitHub-hosted sources only. Direct subagents each get their own allowance.

Action: both workflows stopped (the only in-flight agents had started minutes earlier). Remaining research (adoption evidence, protocols, runtime and safety, OS-level surface, developer users, non-technical users) re-dispatched as direct subagents. All nine finished drafts sent to direct fact-checkers; the five written without search got a "verify and refresh" mandate that also searches for 2026 developments the authors could not see. The developer-harness matrix runs after the two developer-harness profiles are verified.

| Time | What | Mechanism | Status |
|------|------|-----------|--------|
| 19:55 | Research: Tasks 8, 9, 10, 16, 17, 18 | Direct subagents | running |
| 19:55 | Verify: Tasks 3, 4, 11, 12 | Direct fact-checkers | running |
| 19:55 | Verify and refresh: Tasks 6, 7, 13, 14, 15 | Direct fact-checkers with refresh mandate | running |
| 19:10 to 19:50 | Verify: Tasks 1, 2, 19 | Direct fact-checkers | done, committed |

## Blocked at 20:00: account spend limit

The first of the re-dispatched agents (Task 18, non-technical users) terminated with an API error: the account has hit its monthly spend limit (HTTP 429; the message says the session limit resets at 23:20 UTC and the monthly limit is raised at claude.ai/settings/usage). Every other running agent is expected to fail the same way. Everything on disk is committed. To resume: raise the limit, then re-dispatch the agents listed as "running" in the 19:55 table, skipping any whose document already carries "Status: verified with notes".

## Resumed at 20:07

The limit was lifted. Five agents from the 19:55 batch survived and finished or are finishing (protocols research, OS-level research, developer-users research, independent-harness fact-check, non-technical-startups fact-check). Ten agents that had died were re-dispatched: research for runtime and safety and for non-technical users; fact-checks for labs, academic and industry; verify-and-refresh for adoption evidence, non-technical labs, messaging, generated UI and voice. Note: two documents (adoption evidence, developer users) were first written by workflow agents that outlived the stop signal and had no web search; their verify-and-refresh passes compensate.

## Session limit at 20:10, resumed 23:31

The 5-hour session limit stopped 12 of the 14 running agents; protocols research and non-technical-startups fact-check finished first. Committed drafts at 20:11. Resumed at 23:31 with 13 direct agents: research for runtime and safety and for non-technical users; fact-checks for labs, academic, industry, protocols, OS-level and developer users; verify-and-refresh for adoption evidence, non-technical labs, messaging, generated UI and voice.

## Session limit again at 00:2x, resumed 04:32 (2026-09-09)

Four agents died with the 5-hour limit (matrix, and fact-checks for adoption, runtime and safety, non-technical users); the adoption refresh and the matrix draft had finished writing before they died. Relaunched at 04:32: fact-checks for runtime and safety, non-technical users and the matrix. The coordinator wrote the three synthesis documents and consolidated the sources meanwhile, then dispatched the red-team agent.


## Complete, 2026-09-09

All nineteen research documents verified with notes. The red team's critique (`docs/08-synthesis/04-red-team.md`) reversed the coordinator's first recommendation on the evidence; the three synthesis documents were rewritten, the response recorded, and cross-document notes added where the corpus disagreed with itself. Deliverables: the repository and a published report page.
