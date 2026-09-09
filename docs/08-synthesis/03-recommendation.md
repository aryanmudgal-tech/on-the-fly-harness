# Recommendation: one hypothesis, one buyer, one test, and a fallback

*Research program: agent harnesses. Written 2026-09-08; rewritten 2026-09-09 after the red-team review in `04-red-team.md` and again on 2026-09-09 after the market test in `05-market-test.md`. Status: final. Market sizing is deliberately excluded, as agreed at the start of the program.*

## What this document answers

- Given the evidence, is there anything a startup should build here, and what would have to be true first?
- What was wrong with the two earlier versions of this recommendation, and why.

## The recommendation in one paragraph

Do not build yet. The two markets the previous version proposed are both occupied: checking code is CodeRabbit's product, and checking claims against sources in documents and spreadsheets is Clearbrief's, MindBridge's and Energent's. One narrow hypothesis survives the evidence: nobody, in any domain, judges the agent's execution trace, the record of what the agent actually did as opposed to what it produced. Test that hypothesis against the compliance and internal-audit buyer, who has a dated control requirement and a budget line no code-review vendor competes for, and test it with a measurement rather than with interviews. If the test passes, the product is trace verification sold as an audit control across every agent surface at once, not a code reviewer. If it fails, the fallback is the position the strategy document ranks highest anyway: a vertical, operator-configured harness that owns one workflow end to end.

## The two errors this document made, and how they were caught

**Version 1 proposed a startup-owned authority layer**: identity, consequence-class permissions, an undo ledger, portable memory and an interruption policy. The red team killed it from the corpus. Those pieces already ship from Dust, Microsoft and the specification itself; the neutral seat belongs to whoever owns the identity provider and the office suite; an outside layer cannot enforce anything on a hosted engine; a ledger cannot undo a released payment; and the design multiplied rented tokens against outcome prices measured in single dollars.

**Version 2 proposed an independent verifier**, entering through developers and going second to spreadsheets and documents "where nobody offers proof at all". The repository owner asked whether that was CodeRabbit. It was. The market test in `05-market-test.md` found that CodeRabbit ships five of the six outputs this document named, blocks merges, reads the linked ticket, executes checks in a sandbox, drops findings it cannot ground, and markets itself with the word "independent", at 17,000-plus customers and a $1.5B valuation as of August 2026. The second market was occupied too. The claim that nobody checks agent work from outside was false when written.

Both errors have the same cause worth recording: the research corpus profiled harnesses and never profiled the tools that check what harnesses produce. Nineteen documents, roughly 640 fact-checked claims, and the adjacent category was outside the plan's scope. A verification pass cannot catch a question nobody asked.

## What survives

One input is unread by every product found, and the negative is primary-grade on two of them. CodeRabbit's own harness skill sends "code diffs to the CodeRabbit API for analysis" and names no other input. Claude Code's reviewer analyzes "the diff and surrounding code" plus the instruction files. Neither reads the producing agent's session.

That matters because a trace covers a defect class a diff structurally cannot show: a deleted failing test, a hand-edited snapshot, a disabled lint rule, a live endpoint called mid-run, or any side effect that never reached the repository. It also matters because it is exactly what a control framework asks for. COSO's guidance on internal control over generative AI, dated 23 February 2026, requires an audit trail of prompts, inputs, outputs, model and configuration versions, and evidence of human review (secondary, multiple sources). That is a trace, described by an auditor.

## Why this is a hypothesis and not a plan

Four reasons it may still be a feature rather than a company, all from the market test.

- **The platform owner already captures the input.** GitHub's documentation describes opening the linked review session from the pull request timeline to see which tools were called and the agent's reasoning (secondary). Capture is solved by the party that owns the merge gate; only judgment is left, and judgment is a prompt.
- **A trace is a self-report.** Anything load-bearing in it must be re-derived against the repository, and CodeRabbit already re-derives by executing code in a sandbox, which is stronger evidence than reading an agent's account of itself.
- **The pipe is built for the incumbent.** Its command-line reviewer already runs inside Claude Code and Codex sessions, where the transcript is a file on disk, and its context assembler already fuses a dozen inputs with cheap-model compression first.
- **Someone is already there.** AgentPM sells an evidence layer that captures local agent sessions across four harnesses and keeps command execution and tool output with the session record (secondary; size, funding and customers not found).

## The test that settles it

Take at least 500 real agent-authored pull requests that a leading reviewer already passed clean. Recover each session trace. Measure two numbers.

| Measurement | What it decides |
|---|---|
| Share of reviewer-passed changes carrying a defect visible only in the trace | Whether the gap exists at all |
| Share of those a sandbox could have caught by re-executing the repository, without the trace | Whether the gap is a product or a better sandbox |

The second row decides it. If re-execution finds the defects, the right product is a sandbox and CodeRabbit owns it. Any threshold on the first row is a judgment call, not a measured figure, and should be set before the data is collected rather than after.

Run this before interviews. Interviews will restate what the corpus already says. This will not.

## If the test passes

Build trace verification as an audit control, not a code reviewer. Sell to compliance and internal audit. Cover every agent surface at once rather than starting with code, because the control requirement is surface-independent and because code is where the incumbent is strongest. Price against the control, not against a per-review comparison, since the per-review ceiling is brutal: a leading reviewer costs roughly one to two dollars per pull request, and the only public price for a model-written review is a vendor's own at fifteen to twenty-five dollars for a diff, which a trace exceeds in length.

Note the substrate constraint before designing anything. Anthropic's compliance interface does expose full session transcripts for enterprise organizations across its surfaces, but it is read-only, compliance-scoped and lossy: reasoning blocks are never included, the system prompt is never returned, and tool inputs and results are truncated by default.

## If the test fails

Take the vertical operator-configured harness, which the strategy document ranks as the most defensible position in the corpus and the least harness-shaped: a non-technical operator edits policy while the vendor owns the loop, in one workflow, end to end. It is the only archetype with reliable monetization in the entire research program. It is also the answer that requires no new market to exist.

## Risks, in order

1. **The first market is occupied, not crowded.** CodeRabbit, as of 2026-08-12: $143M Series C at a $1.5B valuation, more than 17,000 customers, more than two million reviews per week, revenue up more than five times year over year (secondary; all primary domains blocked, corroborated across five or more independent snippets). It ships five of the six outputs version 2 named.
2. **The surviving differentiator is a feature on someone else's surface.** See the four reasons above.
3. **The second market is occupied too.** Clearbrief shipped a document cite-checking add-in in December 2025 with at least three named competitors; Energent shipped a fresh-sub-agent number-retracing audit around 2026-07-07; MindBridge, DataSnipper, Caseware and Fieldguide surround the spreadsheet (all secondary).
4. **Gross margin, still unmeasured.** Reading is structurally cheaper than generating, but a trace is longer than a diff and the price ceiling is set by a bundled free reviewer and a thirty-dollar-a-seat paid one.
5. **The incumbent ships it before the buyer builds it.** CodeRabbit owns the webhook, the merge gate, the ticket link and an in-harness reviewer sitting beside the trace file. GitHub owns the session log and the approval rule.
6. **Independence is contested and the market is bundling against it.** Cursor acquired Graphite in December 2025; Sonar acquired Gitar in May 2026 explicitly to span from the moment an agent starts writing to the moment work lands (both secondary).
7. **No measured buyer.** Nothing in the corpus shows an organization judging its existing reviewers insufficient. Evidence of stacking is configuration files, not procurement.
8. **Evidence quality on the central claim.** Every CodeRabbit primary domain was blocked in this session, so "it does not read traces" is strongly indicated, not proven. A differentiator that cannot be verified cannot be sold against.

## What would falsify what remains

- Trace-only defects are rare, or a sandbox catches most of them by re-execution. This is the test above and it is the decisive one.
- CodeRabbit or GitHub ships trace-aware review, which either could do in a quarter.
- Compliance buyers accept the platform's own session log as the audit trail, which is what it is being built to be.
- Auditors treat an agent trace as unnecessary because they audit the output and the control, not the process.

## What this recommendation is not

- Not a code reviewer. That market has a $1.5B incumbent that does the job described.
- Not a document or spreadsheet cite-checker. Those shipped in December 2025 and July 2026.
- Not an engine, a control plane, a new surface, a device or a desktop app. Each was ruled out earlier and none of those rulings changed.
- Not a claim that agent verification is unnecessary. It is necessary, largely served, and the unserved sliver may belong to the platform.

## What this means for the thesis

The thesis survives only as a design thesis: the outer harness is the bottleneck, and the inner harness belongs to the model's owner. As a business thesis it has now failed twice under examination, once as a control layer and once as a verifier, and both times because the corpus was read as describing an empty space that was not empty. The honest position is that this research produced a good map of harnesses, a clear design principle, and no validated company. The next artifact should be a measurement, not another document.

## Open questions and unverified claims

- Every CodeRabbit product fact is secondary; its primary domains were blocked.
- The COSO requirement is multi-sourced but secondary.
- AgentPM's size, funding and customers were not found.
- No threshold for the trace-only defect rate is defensible from evidence; it must be set as a judgment before measurement.
