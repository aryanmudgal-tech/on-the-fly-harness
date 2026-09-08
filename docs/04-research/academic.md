# Can the Harness Be Learned? Academic Evidence on Automated Harness Design, Task Horizons, and Human-Agent Interfaces

*Research program: agent harnesses. Written 2026-09-08. Status: draft, pending verification.*

## What this document answers

- Can the harness around a model be designed automatically or learned from experience, and how well does that work as of September 2026?
- Does the harness matter more or less as models improve? What do harness-search papers, minimal harnesses, and METR's task-horizon measurements say?
- What does research on generative UI and human-agent interaction say about the default surface for operating agents, including for non-technical people?
- Where the academic evidence is thin, contradictory, or unverified.

## TL;DR

- **Harness design is now a search problem with working automation.** The 2024 line (ADAS, AFlow, AgentSquare) searched over workflows; the 2025 line (SICA, Darwin Gödel Machine, Huxley-Gödel Machine) let coding agents rewrite their own scaffolds; the 2026 line (Meta-Harness, Self-Harness, AutoHarness, Hyperagents, HarnessForge, Ouroboros, HarnessDev) treats the whole harness codebase as the thing to optimize. All results are authors' claims on benchmarks.
- **The harness still swings results a lot.** Meta-Harness (Stanford/MIT/KRAFTON, Mar 2026) reports that changing only the harness around a fixed model can move benchmark performance by up to 6x, and its searched memory harness beat a hand-built state-of-the-art one by 7.7 points with 4x fewer context tokens [1][3].
- **But learned harnesses overfit and may not beat brute force.** An independent July 2026 re-evaluation found harness evolution "does not consistently outperform simple test-time scaling" at matched budgets and shows "near-zero transfer" to held-out tasks [5].
- **The inner harness is shrinking for coding.** mini-SWE-agent, about 100 lines with bash as its only tool, reports >74% on SWE-bench Verified [18]. METR found in Feb 2026 that the Claude Code and Codex product scaffolds did not outperform its own simple scaffolds when measuring Opus 4.5 and GPT-5 [36].
- **Model capability is compounding fast.** METR's Time Horizon 1.1 (Jan 2026) estimates a post-2023 doubling time of 131 days, and about 89 days from 2024 onward, on 228 software tasks [34]. METR lists many limitations of the metric [35][37].
- **Generative UI works as an output surface, not yet as a control surface.** Google's paper reports raters preferred generated interactive pages over markdown 83% of the time "when ignoring generation speed" [43]; expert evaluations find LLM-designed GUIs weak on accessibility and real interactivity [47]. Google's A2UI standard restricts agents to a declarative component catalog for security [51].
- **HCI has principles, not proof.** The best 2026 synthesis (14 principles from 106 papers) says the barrier to agent adoption is "a lack of design knowledge for successful human-agent interaction" [58]. Neither Microsoft's HAX guidelines nor Google's PAIR Guidebook has an agent-specific update that I could find (Sept 2026) [53][55].

## 1. What "learning the harness" means

Analogy: if the model is a pilot, the harness is the cockpit. Instruments are context, levers are tools, checklists and autopilot are the loop and orchestration, and the co-pilot's veto is permissions. "Learning the harness" means letting someone, increasingly the pilot itself, redesign the cockpit between flights, scored by how many flights land.

Precisely: a harness-optimization system has a **search space** (prompts, workflow graphs, or arbitrary code), a **proposer** that generates candidates (an LLM or coding agent), an **evaluator** (a benchmark with a scorer), and a **selection rule**. The generic loop, shared by ADAS, DGM, and Meta-Harness:

```python
archive = [baseline_harness]
for _ in range(budget):
    parent = select(archive)                  # ADAS: best so far; DGM: open-ended sampling; HGM: clade metaproductivity
    child  = proposer.edit(parent, evidence)  # evidence: past scores, traces, source of prior harnesses
    score  = evaluate(child, tasks, frozen_model)
    if accept(score):
        archive.append(child)
return pareto(archive)                        # Meta-Harness returns an accuracy-vs-cost frontier
```

A 2026 community reading list organizes the field as a ladder of what gets optimized [4], mapped here onto this program's seven harness parts:

```
Ladder (Awesome-Harness-Self-Improvement, 2026)   Representative work            Harness part touched
L0  instruction prompts                            DSPy, GEPA                     loop (instructions)
L1  context & memory                               AWM, MCE, Meta-Harness         context & memory
L2  workflow / graph structure                     ADAS, AFlow, AgentSquare       orchestration, loop
L3  harness / agent code                           SICA, DGM, HGM                 loop, tools, context
L4  optimizer / meta-harness code                  Meta-Harness, Self-Harness,    the outer loop itself
                                                   Hyperagents, Ouroboros
L5  harness + model weights jointly                HAT, HarnessForge              model + all of the above
```

Absent from the ladder: permissions and safety, runtime, and surface. The research automates the parts that have a benchmark score. The parts that face humans mostly do not.

## 2. The interface matters: SWE-agent and its own counter-example

**SWE-agent** (Princeton, arXiv May 2024, NeurIPS 2024) coined *agent-computer interface* (ACI): tools and feedback formats built for a model rather than a human, such as a file viewer that shows 100 numbered lines, an editor with syntax-check guardrails, and a search command that caps its hits [17]. The argument was that ACI design, not just the model, drives success; the headline was 12.5% pass@1 on full SWE-bench with GPT-4 Turbo (recalled from the abstract; arXiv unreachable today, so unverified).

The counter-example comes from the same group. **mini-SWE-agent** is "just some 100 lines of python for the agent class," has "no tools other than bash," does not use the model's tool-calling interface at all, runs each action as a fresh `subprocess.run`, keeps a "completely linear history," and reports ">74% on the SWE-bench Verified benchmark" (README, read Sept 2026; models not named) [18]. The SWE-agent README now recommends it, citing "65% on SWE-bench Verified in 100 lines" [17], so as to focus on "the language model (rather than the agent scaffold)."

Reading: the ACI still matters, but the ACI needed for coding fell from thousands of lines of custom commands in 2024 to bash in 2025. The model absorbed the tools.

## 3. Searching over workflows and prompts (2023–2025)

| System | Date / venue | Search space | Headline claim (authors') | Caveat |
|---|---|---|---|---|
| **DSPy** [30] | Oct 2023, ICLR 2024 | Prompts and few-shot examples of a declared pipeline | Compiled pipelines beat hand prompts; small models become competitive | Needs a metric and training examples |
| **ADAS / Meta Agent Search** [19] | Aug 2024, ICLR 2025 | Whole agents as Python code, kept in an archive | Meta-agent "invents novel and powerful agent designs"; ARC, DROP, GPQA, MGSM, MMLU, transfer to math (repo) | Per-benchmark gains (e.g. +13.6 F1 on DROP) recalled, unverified |
| **AFlow** [20] | Oct 2024, ICLR 2025 oral | Workflows as code graphs, MCTS | Six benchmarks: HumanEval, MBPP, GSM8K, MATH, HotpotQA, DROP (repo) | 5.7% / 19.5% / 4.55%-of-cost figures recalled, unverified |
| **AgentSquare** [21] | Oct 2024, ICLR 2025 | Four modules (planning, reasoning, tool use, memory); evolution, recombination, performance predictor | ALFWorld, WebShop, M3ToolEval, SciWorld (repo) | 17.2% average gain recalled, unverified |
| **GEPA** [29] | Jul 2025, ICLR 2026 oral | Any text parameter; per the repo also "code, agent architectures, configurations" | Beats GRPO by up to 20% with up to 35x fewer rollouts; +6% average across six tasks on Qwen3 8B; >10% over MIPROv2 | Same-benchmark evaluation; the repo's larger claims are project marketing |

Two things matter for the thesis. The search space widened from prompt text to the entire program, and the proposer changed from a template-filler to a coding agent. And GEPA now ships an MCP adapter that optimizes tool descriptions and system prompts, plus a "full program" mode that evolves DSPy control flow (repo, Sept 2026) [29]. Tool and integration design, harness part 2, is already an optimization target.

## 4. Agents that rewrite their own harness (2025–2026)

**SICA** (arXiv Apr 2025, ICLR 2025 workshop) runs evaluate-archive-improve on its own codebase inside Docker; the base agent deliberately "lacks efficient file editing tools" so the agent must build them [24]. **Darwin Gödel Machine** (Sakana AI/UBC/Vector, arXiv May 2025, ICLR 2026) keeps an open-ended archive of self-modified coding agents and reports SWE-bench 20.0%→50.0% and Polyglot 14.2%→30.7%, discovering "better code editing tools, long-context window management, peer-review mechanisms," patch validation, and error memory [22]. **Huxley-Gödel Machine** (arXiv Oct 2025, ICLR 2026 oral) argues a candidate's own score poorly predicts its descendants' scores ("metaproductivity-performance mismatch"), selects by clade-level productivity, and reports that an agent optimized on SWE-bench Verified with GPT-5-mini, then evaluated on SWE-bench Lite with GPT-5, matches "the best officially checked results of human-engineered coding agents" [23].

The 2026 wave makes the harness itself the object:

| Paper (2026) | What it automates | One-line claim (abstracts / reading list) |
|---|---|---|
| **Meta-Harness** [1] (Mar) | Full harness code around a frozen model | Coding-agent proposer (Claude Code with Opus 4.6) with "unrestricted filesystem access" to prior harnesses' source, scores, and traces; median 82 files read per iteration; returns a Pareto frontier |
| **Hyperagents** [14] (Mar) | The modification policy | "A meta-agent controls how to modify task agents" |
| **Self-Harness** [7] (Jun) | Harness with regression gates | "Weakness mining → bounded harness proposal → regression validation on held-in/held-out splits" |
| **HarnessForge** [8] (Jun) | Harness and policy jointly | "Joint harness and policy evolution" |
| **AutoHarness** (Lou et al.) [4] | Code harness synthesis | "Iterative code refinement with environment feedback" |
| **Agentic Harness Engineering** (Lin et al.) [4] | Coding-agent harnesses | "Observability-driven automatic evolution" |
| **Harness Handbook** [9] (Jul) | Legibility of evolved harnesses | "Readable, navigable, and editable" evolving harnesses |
| **Ouroboros** [15] (Aug) | A frontier coding agent's own core | "Reviewed core evolution through iterative experience" |
| **HAT** [16] (Aug) | The model, for changing harnesses | "Training agents to evolve with changing harnesses" |
| **HarnessDev** [10] (Sep) | Whether LLMs can create and evolve their own harness | Title only |

Meta-Harness is the most direct test of the thesis. On online text classification, the best discovered harness ("Label-Primed Query") scored 48.6% vs. 40.9% for ACE, a hand-designed context-management system, using 4x fewer context tokens; gains concentrated on large label spaces (LawBench, 215 classes, +16 points; Symptom2Disease +9) [3]. It also runs scaffold evolution on Terminal-Bench 2.0 (numbers not verifiable today). The authors say the harness alone can swing results "by as much as 6x" on the same benchmark, and that "this workflow only became practical recently, following major improvements in coding-agent capabilities around early 2026" [3]. The released code "has not been tested beyond verifying that it runs" [2].

Two structural facts follow. The *inner* harness (context management, retrieval, summarization, tool loop) can be searched, and the search is itself done by a product harness (Claude Code). And every optimizer above needs a scorer. For open-ended personal or business tasks there is none; the human is the evaluator, which pushes the design problem back to the surface.

## 5. Learned tools and memory (2023–2024)

Earlier work learned harness pieces at runtime rather than searching offline. **Voyager** (May 2023) grew "an ever-growing skill library of executable code" in Minecraft: 3.3x more unique items, 2.3x longer distances, tech-tree milestones up to 15.3x faster, with skills transferring to new worlds [25]. **LATM** (May 2023) had GPT-4 write reusable tools that GPT-3.5 then used, "on par with using GPT-4 for both" at lower cost, with a dispatcher deciding when a new tool is needed [26]. **CREATOR** (May 2023) separated "tool creation from decision execution" on MATH, TabMWP, and a Creation Challenge [27]. **Agent Workflow Memory** (CMU, Sep 2024) induces reusable workflows from past trajectories, offline or online, and reported a then state-of-the-art 35.6% on WebArena [28] (its 24.6% and 51.1% relative-gain figures are recalled, unverified).

Implication: tools (part 2) and memory (part 3) were shown learnable three years ago. What changed is scale and who does the learning: the agent, not the researcher.

## 6. The critique: does automated harness design actually work?

The most important paper for the thesis is negative. **"Rethinking the Evaluation of Harness Evolution for Agents"** (arXiv Jul 2026; authors not identifiable from reachable sources) makes two methodological points and one empirical one [5]. Search and final evaluation usually share a benchmark, so gains "risk overfitting to that specific task set." Harness evolution is itself iterative search that consumes feedback and inference, so it must be compared with plain test-time scaling "under matched feedback and inference budgets." Under those controls, "automatic harness evolution does not consistently outperform simple test-time scaling methods and exhibits limited generalization," with "near-zero transfer" to held-out tasks, "memorizing fixes instead of learning general scaffolding strategies."

Three independent signals point the same way. METR (Feb 2026) measured Opus 4.5 and GPT-5 through the Claude Code and Codex products and found "neither Claude Code nor Codex outperform the default scaffolds METR uses" (Triframe and a ReAct-style loop); it describes Codex as "a simple loop ... with access to a more sophisticated file editing tool" and Claude Code as a simple main loop that "can spin up subagents" [36]. The self-evolving-agent surveys (Jul and Aug 2025, revised 2026) catalogue what can evolve but note evaluation is mostly episodic benchmarks plus LLM-as-judge, with safety framed as jailbreak resistance rather than oversight of a changing system [31][32]; a benchmark for evolution over time, SEA-Eval, only appeared in Apr 2026 [33]. And HGM's own finding, that a harness's score poorly predicts its descendants', is a warning that greedy harness search is noisy [23].

Counter-signals: Self-Harness validates on held-out splits [7]; Meta-Harness reports a Pareto frontier rather than one cherry-picked harness [1]; DGM/HGM show transfer across models (search with GPT-5-mini, evaluate with GPT-5) [23]. None has been independently replicated as far as I could find.

## 7. How much harness remains as models improve?

**METR's time horizon** is the cleanest external yardstick of raw capability: the human task length at which a model succeeds 50% of the time. The March 2025 paper found a roughly 7-month doubling since 2019 [40]. **Time Horizon 1.1** (Jan 29, 2026) grew the suite 34% (228 tasks vs. 170) and doubled the 8-hour-plus tasks (31 vs. 14); under it the post-2023 doubling time is 131 days (vs. 165 under TH1), and "from 2024 onward the doubling rate is just 89 days" [34]. METR also published a limitations note (Jan 22, 2026) [35], a sensitivity analysis of modelling assumptions (Mar 20, 2026) [37], a Frontier Risk Report for Feb–Mar 2026 [39], and an "expenditure horizon" metric for optimization ability (Jul 21, 2026) [38]. Per-model horizons under TH1.1 are on metr.org, which I could not reach; I do not quote them.

| Evidence | Direction | What it implies |
|---|---|---|
| mini-SWE-agent: bash-only, ~100 lines, >74% Verified [18] | Harness shrinks | For coding, loop and tools collapse into model plus shell |
| METR Feb 2026: product scaffolds do not beat simple ones [36] | Harness shrinks | Scaffold sophistication is not what moves measured capability |
| Meta-Harness: 6x swing from harness alone; feasible only since early 2026 [1][3] | Harness matters, and moves | The optimal harness is model-specific and re-derived per generation |
| Harness survey (Jun 2026): agent = "foundation model coupled with an execution harness"; six runtime responsibilities (observation, context, control, action, state, verification); paradigms run from prompt engineering to "agent-native training with co-evolution" [6] | Harness migrates into training | Vendors will fold harness behavior into the model |
| HAT (Aug 2026): training agents for changing harnesses [16] | Harness treated as environment | Model labs assume the harness keeps changing |
| GEPA (2025) and Meta-Harness (2026) use a coding agent as the optimizer [29][1] | Harness eats itself | Today's product harness designs the next one |

My read: the harness parts with a benchmark (loop, tools, context, orchestration) are being automated and absorbed, in that order. Nothing in this literature automates permissions, runtime, or surface, and the harness survey's six responsibilities do not include a human-facing one.

## 8. Generative and adaptive interfaces

Analogy: today's chat UI is a teleprinter; generative UI is a printing press that lays out a fresh page per request. The open question is whether the model can also build the *controls*, not just the page.

**Google's generative UI.** Announced with Gemini 3 in Nov 2025 and shipped as "Dynamic View" in the Gemini app and in AI Mode; the paper "Generative UI: LLMs are Effective UI Generators" (Leviathan, Valevski, et al.; arXiv Apr 2026) describes a recipe of prompting, tools, and post-processing, releases PAGEN (expert-crafted pages), and reports generated HTML/CSS/JS preferred over markdown 83% of the time, qualified "when ignoring generation speed" [43]. The capability is called "emergent," with no UI-specific training. At I/O in May 2026, Google said generative UI would become free in Search that summer (secondary report) [45]. Jakob Nielsen's favorable Nov 2025 review is commentary [44].

**Independent and adjacent work.** *Generative Interfaces for Language Models* (arXiv Aug 2025) has LLMs answer with generated UIs and reports they "significantly outperform conversational ones across diverse query types" on a multi-dimensional rubric; the exact preference rate is unverified today [46]. *Qualitative Evaluation of LLM-Designed GUI* (Jan 2026) finds LLMs produce good structured layouts but "face challenges in meeting accessibility standards and providing interactive functionality" and only partially tailor to personas [47]. *AlignUI* (Jan 2026) and *Efficient Personalization of Generative User Interfaces* (Apr 2026) fold user preference or ability into the generation objective [48][49]; *PerceptUI* (Jun 2026) uses LLM agents as synthetic users to evaluate UIs [50]. **A2UI** (Google-originated open standard, v0.9.1 stable, v1.0 release candidate as of Sept 2026) has agents emit *declarative JSON* against a client-controlled component catalog, because "running arbitrary code generated by an LLM may present a security risk. A2UI is a declarative data format, not executable code"; renderers exist for Lit, Angular, React (via CopilotKit), and Flutter, over A2A or AG-UI transports [51].

Reading: generative UI research is about *rendering answers*, not *steering an agent*. Every evaluation is a rater looking at a page; none measures a user controlling a long-running task through a generated interface. A2UI's catalog-not-code rule is the same blast-radius logic as harness part 4, and it caps how "reimagined" a generated surface can be.

## 9. HCI on human-agent interaction

The canon is old: Horvitz's mixed-initiative principles (CHI 1999) and Amershi et al.'s 18 human-AI interaction guidelines (CHI 2019), now packaged as Microsoft's HAX Toolkit [53]. The 2026 synthesis *Design Principles for Human-Agent Interaction* (arXiv Jun 2026) says these served "bounded and discrete tasks such as recommendations and search"; it coded 106 papers on six dimensions, found 15 themes, and proposes 14 principles across four stages (initially, during interaction, over time, when things go wrong), arguing the adoption barrier is "a lack of design knowledge for successful human-agent interaction" [58]. Workplace-focused principle papers followed in Jun and Jul 2026 [59][63].

Empirical systems are fewer. **Cocoa** (Allen AI/UW, arXiv Dec 2024, later a CHI paper) proposed "co-planning and co-execution": an editable plan document as the shared control surface instead of a chat transcript [60]. **ALLOY** (Oct 2025) generates reusable agent workflows from user demonstration [64]. CHI 2026 (April) had an extended abstract, *Agentic Automation Experiences*, on how users assign responsibility when "multiple specialized agents coordinate behind unified interfaces" [61], and a workshop on GUI agents across modalities [62].

Vendor guidance has not caught up. Microsoft's HAX site still centers the 2019 guidelines; its agent-specific 2026 release is a security toolkit (Agent Governance Toolkit, Apr 2026, covering the OWASP Agentic Top 10 of Dec 2025), not a UX one [54]. Google's PAIR Guidebook has no agent chapter I could find; a 2026 critique says it "was written in a world where AI predicted and recommended but did not act" [56], and Google's agent guidance lives in engineering whitepapers ("Introduction to Agents," Nov 2025) [57].

## What this means for the thesis

**Supports.**
- The harness is a large, model-specific lever: up to 6x swings from harness alone (Meta-Harness, Mar 2026) and 20%→50% SWE-bench from self-rewritten scaffolds (DGM, May 2025) [1][22].
- The harness is a moving target re-derived per model generation, and the best way to re-derive it is now another agent. That matches "the harness becomes the bottleneck" as engineering effort, and argues that the durable product is the *outer loop* (evaluation, traces, regression gates, permissions), not any hand-built inner loop.
- The surface literature says chat is not the ceiling: generated interfaces beat markdown 83% of the time [43] and beat chat in a separate study [46]; HCI syntheses say validated design for agents is missing [58]. A gap is an opportunity.

**Contradicts.**
- In the best-benchmarked domain, coding, the inner harness is shrinking: a 100-line bash-only agent gets >74% Verified [18], and product scaffolds did not beat METR's simple ones [36]. Under the narrow reading, "the harness is the bottleneck" is false for loop and tools.
- The strongest independent evaluation says automated harness design has not been shown to beat test-time scaling at equal cost and overfits its benchmark [5]. A startup whose moat is "we learn the harness" has weak evidence behind it as of Sept 2026.
- Generative UI evidence excludes latency, measures page preference rather than task control, and finds interactivity and accessibility gaps [43][47]. No study shows a generated interface lets non-technical people operate a long-running agent better than a chat app.
- HCI offers principles and thematic reviews; it does not show any specific surface (CLI, desktop, web, generated) is the wrong default. Absence of evidence, not evidence of absence.

**Nuance, bluntly.** The research uses "harness" to mean the inner loop plus context, exactly the part models are absorbing and agents are learning to write. The thesis is really about the outer harness: permissions, runtime, surface, and the human as evaluator. That part has almost no academic evidence either way, because it has no benchmark. The defensible version of the thesis is: *the inner harness is being automated; the outer harness, which makes a human the evaluator and steering loop, is unautomated and under-studied, and that is where a product can live.* The indefensible version is that a hand-designed harness stays a bottleneck; the 2026 papers argue the opposite, and METR's 89-to-131-day doubling means whatever harness is right today is wrong within a year.

## Open questions and unverified claims

- Meta-Harness's Terminal-Bench 2.0 results, compute cost, and cross-model transfer: not verifiable today (arXiv, project page, and mirrors blocked).
- Authors, affiliations, and the specific methods re-run in "Rethinking the Evaluation of Harness Evolution for Agents": unknown.
- Per-model 50% and 80% time horizons under METR TH1.1, and any METR measurements of mid-2026 models: not quoted; metr.org unreachable.
- Recalled figures not re-fetched: SWE-agent 12.5% pass@1; ADAS +13.6 F1 (DROP), +14.4% (MGSM); AFlow 5.7% / 19.5% / 4.55%; AgentSquare 17.2%; AWM 24.6% / 51.1%; SICA 17%→53%; DGM's reported cases of hallucinated tool use and removal of its own hallucination-detection markers.
- The exact preference rate in "Generative Interfaces for Language Models," and whether its participants included non-technical users.
- Whether "Meng et al." surveying "22 harness systems" with labeled-transition-system semantics is the June 2026 harness survey [6] or a separate paper.
- No paper found that evaluates a *generated* control surface for a running agent with real users; none found that automates permissions, runtime, or surface design.

## Sources

1. Meta-Harness: End-to-End Optimization of Model Harnesses (Lee, Nair, Zhang, Lee, Khattab, Finn) — https://arxiv.org/abs/2603.28052 — Mar 2026
2. Meta-Harness reference code, stanford-iris-lab — https://github.com/stanford-iris-lab/meta-harness — 2026
3. Meta-Harness write-up (Maxim blog, secondary) — https://www.getmaxim.ai/blog/meta-harness-what-if-we-let-an-agent-optimize-the-code-around-an-llm/ — 2026
4. Awesome-Harness-Self-Improvement reading list — https://github.com/leezythu/Awesome-Harness-Self-Improvement — 2026
5. Rethinking the Evaluation of Harness Evolution for Agents — https://arxiv.org/abs/2607.12227 — Jul 2026
6. From Question Answering to Task Completion: A Survey on Agent System and Harness Design — https://arxiv.org/abs/2606.20683 — Jun 2026
7. Self-Harness: Harnesses That Improve Themselves — https://arxiv.org/abs/2606.09498 — Jun 2026
8. HarnessForge: Joint Harness and Policy Evolution — https://arxiv.org/pdf/2606.01779 — Jun 2026
9. Harness Handbook: Making Evolving Agent Harnesses Readable, Navigable, and Editable — https://huggingface.co/papers/2607.13285 — Jul 2026
10. HarnessDev: Can LLMs Create and Evolve Their Own Agent Harness? — https://arxiv.org/html/2609.01437 — Sep 2026
11. Natural-Language Agent Harnesses — https://arxiv.org/html/2603.25723v1 — Mar 2026
12. Adapting the Interface, Not the Model: Runtime Harness Adaptation — https://arxiv.org/html/2605.22166v1 — May 2026
13. Code as Agent Harness — https://arxiv.org/pdf/2605.18747 — May 2026
14. Hyperagents — https://arxiv.org/abs/2603.19461 — Mar 2026
15. Ouroboros: Self-Developing Frontier Coding Agent — https://arxiv.org/abs/2608.08311 — Aug 2026
16. HAT: Training Agents to Evolve with Changing Harnesses — https://arxiv.org/abs/2608.15763 — Aug 2026
17. SWE-agent: Agent-Computer Interfaces Enable Automated Software Engineering — https://arxiv.org/abs/2405.15793 ; repo https://github.com/SWE-agent/SWE-agent — May 2024
18. mini-swe-agent — https://github.com/SWE-agent/mini-swe-agent — read Sep 2026
19. Automated Design of Agentic Systems (ADAS) — https://arxiv.org/abs/2408.08435 ; repo https://github.com/ShengranHu/ADAS — Aug 2024
20. AFlow: Automating Agentic Workflow Generation — https://arxiv.org/abs/2410.10762 ; repo https://github.com/FoundationAgents/AFlow — Oct 2024
21. AgentSquare: Automatic LLM Agent Search in Modular Design Space — https://arxiv.org/abs/2410.06153 ; repo https://github.com/tsinghua-fib-lab/AgentSquare — Oct 2024
22. Darwin Gödel Machine — https://arxiv.org/abs/2505.22954 ; https://sakana.ai/dgm/ ; repo https://github.com/jennyzzt/dgm — May 2025
23. Huxley-Gödel Machine — https://arxiv.org/abs/2510.21614 ; repo https://github.com/metauto-ai/HGM — Oct 2025
24. SICA: A Self-Improving Coding Agent — https://github.com/MaximeRobeyns/self_improving_coding_agent (arXiv 2504.15228) — Apr 2025
25. Voyager: An Open-Ended Embodied Agent with LLMs — https://arxiv.org/abs/2305.16291 ; repo https://github.com/MineDojo/Voyager — May 2023
26. Large Language Models as Tool Makers (LATM) — https://arxiv.org/abs/2305.17126 ; repo https://github.com/ctlllll/LLM-ToolMaker — May 2023
27. CREATOR: Tool Creation for Disentangling Abstract and Concrete Reasoning — https://arxiv.org/abs/2305.14318 ; repo https://github.com/qiancheng0/CREATOR — May 2023
28. Agent Workflow Memory — https://arxiv.org/abs/2409.07429 ; repo https://github.com/zorazrw/agent-workflow-memory — Sep 2024
29. GEPA: Reflective Prompt Evolution Can Outperform Reinforcement Learning — https://arxiv.org/abs/2507.19457 ; repo https://github.com/gepa-ai/gepa ; DSPy docs https://dspy.ai/api/optimizers/GEPA/overview/ — Jul 2025, ICLR 2026
30. DSPy: Compiling Declarative Language Model Calls into Self-Improving Pipelines — https://arxiv.org/abs/2310.03714 — Oct 2023
31. A Comprehensive Survey of Self-Evolving AI Agents — https://arxiv.org/abs/2508.07407 ; https://github.com/EvoAgentX/Awesome-Self-Evolving-Agents — Aug 2025
32. A Survey of Self-Evolving Agents: What, When, How, and Where to Evolve — https://arxiv.org/abs/2507.21046 ; https://github.com/XMUDeepLIT/Awesome-Self-Evolving-Agents — Jul 2025 (2026 revision)
33. SEA-Eval: Evaluating Self-Evolving Agents Beyond Episodic Assessment — https://arxiv.org/pdf/2604.08988 — Apr 2026
34. METR, Time Horizon 1.1 — https://metr.org/blog/2026-1-29-time-horizon-1-1/ — Jan 2026
35. METR, Clarifying limitations of time horizon — https://metr.org/notes/2026-01-22-time-horizon-limitations/ — Jan 2026
36. METR, Measuring Time Horizon using Claude Code and Codex — https://metr.org/notes/2026-02-13-measuring-time-horizon-using-claude-code-and-codex/ — Feb 2026
37. METR, Impact of modelling assumptions on time horizon results — https://metr.org/notes/2026-03-20-impact-of-modelling-assumptions-on-time-horizon-results/ — Mar 2026
38. METR, Expenditure Horizon — https://metr.org/blog/2026-07-21-expenditure-horizon/ — Jul 2026
39. METR, Frontier Risk Report (Feb–Mar 2026) — https://metr.org/blog/2026-05-19-frontier-risk-report/ — May 2026
40. METR, Measuring AI Ability to Complete Long Software Tasks — https://metr.org/blog/2025-03-19-measuring-ai-ability-to-complete-long-tasks/ — Mar 2025
41. METR, Task-Completion Time Horizons of Frontier AI Models (live page) — https://metr.org/time-horizons/
42. LessWrong, METR Time Horizons: Now 10x/Year (commentary) — https://www.lesswrong.com/posts/EYb2K9acKfyG2bome/metr-time-horizons-now-10x-year — 2026
43. Generative UI: LLMs are Effective UI Generators (Leviathan, Valevski, et al.) — https://arxiv.org/abs/2604.09577 ; blog https://research.google/blog/generative-ui-a-rich-custom-visual-interactive-user-experience-for-any-prompt/ — Nov 2025 / Apr 2026
44. Jakob Nielsen, Generative UI from Gemini 3 Pro (commentary) — https://jakobnielsenphd.substack.com/p/generative-ui-google — Nov 2025
45. Search Engine Journal, Google's Generated Interfaces Could Compete With Tool Pages (secondary) — https://www.searchenginejournal.com/googles-generated-interfaces-could-compete-with-tool-pages/586589/ — 2026
46. Generative Interfaces for Language Models — https://arxiv.org/abs/2508.19227 — Aug 2025
47. Qualitative Evaluation of LLM-Designed GUI — https://arxiv.org/abs/2601.22759 — Jan 2026
48. Efficient Personalization of Generative User Interfaces — https://arxiv.org/abs/2604.09876 — Apr 2026
49. AlignUI — https://arxiv.org/pdf/2601.17614 — Jan 2026
50. PerceptUI: LLM Agents as Human-Aligned Synthetic Users for UI/UX Evaluation — https://arxiv.org/html/2606.05697v1 — Jun 2026
51. A2UI (Agent-to-User Interface) — https://github.com/google/A2UI — read Sep 2026
52. awesome-generative-ui (curated list) — https://github.com/narrowin/awesome-generative-ui — 2026
53. Microsoft HAX Toolkit, Guidelines for Human-AI Interaction — https://www.microsoft.com/en-us/haxtoolkit/ai-guidelines/ — 2019, ongoing
54. Microsoft, Introducing the Agent Governance Toolkit — https://opensource.microsoft.com/blog/2026/04/02/introducing-the-agent-governance-toolkit-open-source-runtime-security-for-ai-agents/ — Apr 2026
55. Google PAIR, People + AI Guidebook chapters — https://pair.withgoogle.com/guidebook/chapters
56. Google's AI Design Playbook Has Six Chapters. The Most Important One Is Missing (commentary) — https://nervegna.substack.com/p/googles-ai-design-playbook-has-six — 2026
57. Summary of Google's "Introduction to Agents" whitepaper (secondary) — https://vanducng.dev/2026/01/10/Google-Introduction-to-Agents-Whitepaper-Summary/ — Jan 2026
58. Design Principles for Human-Agent Interaction — https://arxiv.org/abs/2606.20630 — Jun 2026
59. A Framework of User Experience Principles for Human-AI Agent Interaction in the Workplace — https://arxiv.org/html/2607.19941v1 — Jul 2026
60. Cocoa: Co-Planning and Co-Execution with AI Agents — https://arxiv.org/pdf/2412.10999 — Dec 2024
61. Agentic Automation Experiences—Rethinking the Interaction of Humans and AI Agents (CHI 2026 EA) — https://dl.acm.org/doi/10.1145/3772363.3778732 — Apr 2026
62. Human-AI-UI Interactions Across Modalities (CHI 2026 workshop) — https://dl.acm.org/doi/10.1145/3772363.3778737 — Apr 2026
63. Human-AI Agent Interaction in a Business Context — https://arxiv.org/html/2606.18716 — Jun 2026
64. ALLOY: Generating Reusable Agent Workflows from User Demonstration — https://arxiv.org/pdf/2510.10049 — Oct 2025
65. AI Weekly, Study: Agent Harness Evolution Doesn't Beat Test-Time Scaling (secondary) — https://aiweekly.co/alerts/study-agent-harness-evolution-doesnt-beat-test-time-scaling — 2026
