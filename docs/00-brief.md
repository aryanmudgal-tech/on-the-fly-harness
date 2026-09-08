# Research brief: reimagining the agent harness

*Started 2026-09-08. Author: aryanmudgal-tech. Status: brainstorming, questions pending.*

## The ask

Deep research into "on-the-fly harnesses": the scaffolding that turns a language model into a working agent. The thesis to test: **as models get better, the harness becomes the bottleneck, and neither a CLI nor a desktop app can be the default way people operate agents.** The output is a recommendation that reimagines the harness, optimised for developers and for non-technical people.

The author is new to the field. Every document here should be readable from first principles, with analogies before jargon.

## Working definition (first pass)

A **model** is the engine. A **harness** is everything else that makes the engine useful: the steering wheel and pedals (the interface), the dashboard (what the user can see), the seatbelts and speed limiter (permissions and safety), the fuel system (how context and memory get fed in), the toolbox in the trunk (tools, integrations), and the garage where it lives (the runtime: local machine, cloud sandbox, phone).

A CLI or a desktop app is one **body style** for that car. The engine does not care which body it sits in. The question this research asks is which body styles the next few years of engines deserve, and whether the body should be fixed at all, or built **on the fly** for each trip.

## Two readings of "on the fly"

1. **Broad:** study the whole harness landscape and reimagine the default one.
2. **Narrow:** harnesses that are generated or adapted at runtime for the task at hand: the agent builds its own interface, tools and workflow per job instead of a human pre-building them.

Which reading (or both) drives the work is the first open question below.

## Honest pushbacks to carry into the research

The author asked for no sycophancy, so these are recorded up front as things the research must weigh rather than assume:

- **"Better models need better harnesses" is only half true.** The other half of the evidence says better models need *less* harness: scaffolding gets absorbed into the model, and hand-built workflow logic becomes a liability. The recommendation must say which parts of the harness get thinner and which get thicker as models improve.
- **CLIs became the developer default for reasons that will not vanish:** they run where the code runs, they compose with every other tool, they script, they work over SSH, and they are cheap to build. A reimagined harness has to beat those properties, not ignore them.
- **Desktop apps and CLIs are already not the default for most agent use.** Chat surfaces (web, mobile, Slack, WhatsApp) carry far more agent traffic than terminals do. The interesting question is why the *capable* agents still live in terminals and what has to change for that capability to reach the other surfaces.

## Assumptions made without asking

- Commits are authored as `aryanmudgal-tech <aryanmudgal4493@gmail.com>`, with no assistant co-author trailer, per the author's instruction.
- The superpowers plugin is not in the plugin catalog for this account, so its skills were cloned from `github.com/obra/superpowers` and are followed by hand: brainstorming, writing-plans, subagent-driven execution, verification before completion.
- Research runs as parallel subagents and dynamic workflows, each with a self-contained brief; results land in this repo as Markdown with sources.
- Time horizon for the recommendation: the next two to three years of model capability, not today's models only.
- Market sizing (TAM) is deliberately out of scope until the landscape is understood.

## Open questions (sent to the author)

1. Which reading of "on the fly": broad, narrow, or landscape first then the narrow thesis?
2. What is the research for: a startup thesis, personal learning, or a build spec?
3. How technical is the author, so the writing level can be set correctly?
4. Which candidate "default surfaces" must be evaluated in depth: messaging apps, a browser workspace with generated UI, voice or ambient agents, OS-level integration, or others?

## Proposed workstreams (draft, pending answers)

| # | Workstream | Question it answers |
|---|-----------|---------------------|
| 1 | Primer | What is a harness, from first principles, with the history of how we got here |
| 2 | Developer harness landscape | Who builds harnesses for developers, how they differ, what is converging |
| 3 | Non-technical surfaces | Where agents already reach non-developers, what works, what breaks |
| 4 | Building blocks and protocols | Tools, context, memory, permissions, sandboxes, MCP and the other protocols, generated UI |
| 5 | Research on dynamic harnesses | Academic and industry work on agents that build their own tools, workflows and interfaces |
| 6 | Users | What developers need versus what non-technical people need; where those needs conflict |
| 7 | Synthesis | Principles, a reimagined harness, and a recommendation with the counter-arguments stated |
