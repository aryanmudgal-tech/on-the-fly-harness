# Dispatch log

Running record of what was dispatched, when, and what came back. Times are UTC, 2026-09-08.

| Time | What | Mechanism | Status |
|------|------|-----------|--------|
| 19:00 | Task 1 primer/anatomy, Task 2 primer/history, Task 19 strategy/value-capture | Direct subagents (research); verifiers to follow | running |
| 19:00 | Workflow A: Tasks 3, 4 → 5 (matrix), 6, 7, 8, 9, 10; each research agent piped into a fact-checker | Workflow tool, 15 agents | running |
| 19:00 | Workflow B: Tasks 11 to 18; each research agent piped into a fact-checker | Workflow tool, 16 agents | running |

Rules in force: research agents write only their own document and never run git; the coordinator commits results as they land, authored as the repo owner.
