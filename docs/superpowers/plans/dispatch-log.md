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
