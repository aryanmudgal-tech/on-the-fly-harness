# Can the Harness Be Learned? Academic Evidence on Automated Harness Design, Task Horizons, and Human-Agent Interfaces

*Research program: agent harnesses. Written 2026-09-08. Status: verified with notes.*

## What this document answers

- Can the harness around a model be designed automatically or learned from experience, and how well does that work as of September 2026?
- Does the harness matter more or less as models improve? What do harness-search papers, minimal harnesses, and METR's task-horizon measurements say?
- What does research on generative UI and human-agent interaction say about the default surface for operating agents, including for non-technical people?
- Where the academic evidence is thin, contradictory, or unverified.

## TL;DR

- **Harness design is now a search problem with working automation.** 2024 work (ADAS, AFlow, AgentSquare) searched over workflows; 2025 work (SICA, Darwin Gödel Machine, Huxley-Gödel Machine) let coding agents rewrite their own scaffolds; 2026 work (Meta-Harness, Self-Harness, AutoHarness, Hyperagents, HarnessForge, Ouroboros, HarnessDev) optimizes the whole harness codebase. All results are authors' claims on benchmarks.
- **The harness still swings results a lot.** Meta-Harness (Stanford/MIT/KRAFTON, Mar 2026) notes, citing prior work rather than its own experiments, that changing only the harness around a fixed model can move benchmark performance by up to 6x, and its searched memory harness beat a hand-built state-of-the-art one by 7.7 points with 4x fewer context tokens [1][3].
- **But learned harnesses overfit and may not beat brute force.** An independent July 2026 re-evaluation found harness evolution "does not consistently outperform simple test-time scaling" at matched budgets and transfers only marginally to held-out tasks (about +0.6 points in its reported setting) [5].
- **The inner harness is shrinking for coding.** mini-SWE-agent, about 100 lines with bash as its only tool, reports >74% on SWE-bench Verified [18]. METR found in Feb 2026 that the Claude Code and Codex product scaffolds did not outperform its own simple scaffolds for Opus 4.5 and GPT-5 [36].
- **Model capability is compounding fast.** METR's Time Horizon 1.1 (Jan 2026) estimates a post-2023 doubling time of 131 days, about 89 days from 2024 onward, on 228 software tasks [34], with many stated limitations [35][37].
- **Generative UI works as an output surface, not yet as a control surface.** Google's paper reports raters preferred generated interactive pages over markdown 83% of the time "when ignoring generation speed" [43]; expert evaluations find LLM-designed GUIs weak on accessibility and real interactivity [47]. Google's A2UI standard restricts agents to a declarative component catalog for security [51].
- **HCI has principles, not proof.** The best 2026 synthesis (a position paper proposing 14 principles; its "106 papers" review count is unverified) says the barrier to agent adoption is "a lack of design knowledge for successful human-agent interaction" [58]. Neither Microsoft's HAX guidelines nor Google's PAIR Guidebook has an agent-specific update that I could find (Sept 2026) [53][55].

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

Absent from the ladder: permissions, runtime, and surface. The research automates what has a benchmark score; the human-facing parts mostly do not.

## 2. The interface matters: SWE-agent and its own counter-example

**SWE-agent** (Princeton, arXiv May 2024, NeurIPS 2024) coined *agent-computer interface* (ACI): tools and feedback formats built for a model rather than a human, such as a 100-line numbered file viewer, an editor with syntax-check guardrails, and a search command that caps its hits [17]. Its claim was that ACI design, not just the model, drives success; its headline 12.5% pass@1 (12.47%) on the full SWE-bench test set with GPT-4 Turbo (gpt-4-1106-preview) is confirmed by the project's 2024 README and the SWE-bench leaderboard entry.

The counter-example comes from the same group. **mini-SWE-agent** is "just some 100 lines of python for the agent class," has "no tools other than bash," skips the model's tool-calling interface entirely, runs each action as a fresh `subprocess.run`, keeps a "completely linear history," and reports ">74% on the SWE-bench Verified benchmark" (README, read Sept 2026; models not named) [18]. The SWE-agent README now recommends it ("65% on SWE-bench Verified in 100 lines") to focus on "the language model (rather than the agent scaffold)" [17]. Reading: the ACI needed for coding fell from thousands of lines of custom commands in 2024 to bash in 2025. The model absorbed the tools.

## 3. Searching over workflows and prompts (2023–2025)

| System | Date / venue | Search space | Headline claim (authors') | Caveat |
|---|---|---|---|---|
| **DSPy** [30] | Oct 2023, ICLR 2024 | Prompts and few-shot examples of a declared pipeline | Compiled pipelines beat hand prompts; small models become competitive | Needs a metric and training examples |
| **ADAS / Meta Agent Search** [19] | Aug 2024, ICLR 2025 | Whole agents as Python code, kept in an archive | Meta-agent "invents novel and powerful agent designs"; ARC, DROP, GPQA, MGSM, MMLU, transfer to math (repo) | Per-benchmark gains (+13.6 F1 on DROP, +14.4% on MGSM; +25.9% / +13.2% on GSM8K / GSM-Hard after transfer) confirmed from the abstract |
| **AFlow** [20] | Oct 2024, ICLR 2025 oral | Workflows as code graphs, MCTS | Six benchmarks: HumanEval, MBPP, GSM8K, MATH, HotpotQA, DROP (repo) | 5.7% / 19.5% / 4.55%-of-cost figures confirmed from the paper's abstract and contribution list |
| **AgentSquare** [21] | Oct 2024, ICLR 2025 | Four modules (planning, reasoning, tool use, memory); evolution, recombination, performance predictor | ALFWorld, WebShop, M3ToolEval, SciWorld (repo) | 17.2% average gain over best-known human designs confirmed from the abstract |
| **GEPA** [29] | Jul 2025, ICLR 2026 oral | Any text parameter; per the repo also "code, agent architectures, configurations" | Beats GRPO by up to 20% with up to 35x fewer rollouts; +6% average across six tasks on Qwen3 8B; >10% over MIPROv2 | Same-benchmark evaluation; the repo's larger claims are project marketing |

Two things matter: the search space widened from prompt text to the entire program, and the proposer changed from a template-filler to a coding agent. GEPA now ships an MCP adapter that optimizes tool descriptions and system prompts, plus a "full program" mode that evolves DSPy control flow (repo, Sept 2026) [29]: tool design, harness part 2, is already an optimization target.

## 4. Agents that rewrite their own harness (2025–2026)

**SICA** (arXiv Apr 2025, ICLR 2025 workshop) runs evaluate-archive-improve on its own codebase inside Docker; its base agent deliberately "lacks efficient file editing tools" so it must build them [24]. **Darwin Gödel Machine** (Sakana AI/UBC/Vector, arXiv May 2025, ICLR 2026) keeps an open-ended archive of self-modified coding agents and reports SWE-bench 20.0%→50.0% and Polyglot 14.2%→30.7%, discovering "better code editing tools, long-context window management, peer-review mechanisms," patch validation, and error memory [22]. **Huxley-Gödel Machine** (arXiv Oct 2025, ICLR 2026 oral) finds a candidate's own score poorly predicts its descendants' ("metaproductivity-performance mismatch"), selects by clade-level productivity instead, and reports that an agent optimized on SWE-bench Verified with GPT-5-mini and evaluated on SWE-bench Lite with GPT-5 matches "the best officially checked results of human-engineered coding agents" [23].

The 2026 wave makes the harness itself the object:

| Paper (2026) | What it automates | One-line claim (abstracts / reading list) |
|---|---|---|
| **Meta-Harness** [1] (Mar) | Full harness code around a frozen model | Coding-agent proposer (Claude Code with Opus 4.6) with "unrestricted filesystem access" to prior harnesses' source, scores, and traces; median 82 files read per iteration; returns a Pareto frontier |
| **Hyperagents** [14] (Mar) | The modification policy | "A meta-agent controls how to modify task agents" |
| **Self-Harness** [7] (Jun) | Harness with regression gates | "Weakness mining → bounded harness proposal → regression validation on held-in/held-out splits" |
| **HarnessForge** [8] (Jun) | Harness and policy jointly | "Joint harness and policy evolution" |
| **AutoHarness** (Lou et al., Google DeepMind; arXiv 2603.03329, ICLR 2026) [4] | Code harness synthesis | "Iterative code refinement with environment feedback" |
| **Agentic Harness Engineering** (Lin et al.; arXiv 2604.25850, Apr 2026) [4] | Coding-agent harnesses | "Observability-driven automatic evolution" |
| **Harness Handbook** [9] (Jul) | Legibility of evolved harnesses | "Readable, navigable, and editable" evolving harnesses |
| **Ouroboros** [15] (Aug) | A frontier coding agent's own core | "Reviewed core evolution through iterative experience" |
| **HAT** [16] (Aug) | The model, for changing harnesses | Harness-Aware Training: "Training Agents to Evolve with Their Harness" (an Alibaba TaoLive technical report) |
| **HarnessDev** [10] (Sep) | Whether LLMs can create and evolve their own harness | Benchmark with Creation and Evolution stages; generated harnesses "remain substantially behind mature human-engineered references on code and on search and research"; evolution gains "are unstable and transfer only partially to held-out tasks" |

Further 2026 titles I could not read argue for harnesses written in natural language, harnesses as code, and runtime harness adaptation instead of model changes [11][12][13].

Meta-Harness is the most direct test of the thesis. On online text classification its best harness ("Label-Primed Query") scored 48.6% vs. 40.9% for ACE, a hand-designed context-management system, with 4x fewer context tokens; gains concentrated on large label spaces (LawBench, 215 classes, +16 points; Symptom2Disease +9) [3]. It also evolves scaffolds on Terminal-Bench 2.0, where secondary reports of the paper give 76.4% with Claude Opus 4.6 (second on the leaderboard at the time) and 37.6% with Claude Haiku 4.5 (first) [1]. The paper's statement that the harness alone can swing results by as much as 6x is a citation of prior work (Tian et al., 2026), not its own measurement; the paper attributes feasibility to advances in coding agents, while the "early 2026" timing comes from secondary write-ups, not the paper [3]; the released code "has not been tested beyond verifying that it runs" [2]. Note that the search is run by a product harness (Claude Code), and that every optimizer here needs a scorer; open-ended personal or business tasks have none, so the human is the evaluator.

## 5. Learned tools and memory (2023–2024)

Earlier work learned harness pieces at runtime rather than searching offline. **Voyager** (May 2023) grew "an ever-growing skill library of executable code" in Minecraft: 3.3x more unique items, 2.3x longer distances, tech-tree milestones up to 15.3x faster, with skills transferring to new worlds [25]. **LATM** (May 2023) had GPT-4 write reusable tools that GPT-3.5 then used, "on par with using GPT-4 for both" at lower cost [26]. **CREATOR** (May 2023) separated "tool creation from decision execution" on MATH, TabMWP, and a Creation Challenge [27]. **Agent Workflow Memory** (CMU, Sep 2024) induces reusable workflows from past trajectories and reported a then state-of-the-art 35.6% on WebArena [28] (its 24.6% and 51.1% relative-gain figures on Mind2Web and WebArena are confirmed from the abstract). Tools (part 2) and memory (part 3) were shown learnable three years ago; what changed is scale and who does the learning.

## 6. The critique: does automated harness design actually work?

The most important paper for the thesis is negative. **"Rethinking the Evaluation of Harness Evolution for Agents"** (Wang, Zhu, Hu, Yuan, Chen, Senthil, Hajishirzi, Tsvetkov, Dasigi, Xiao; arXiv Jul 2026) argues that search and final evaluation usually share a benchmark, so gains "risk overfitting to that specific task set," and that harness evolution is itself search that consumes feedback and inference, so it must be compared with test-time scaling "under matched feedback and inference budgets." Under those controls, "automatic harness evolution does not consistently outperform simple test-time scaling methods and exhibits limited generalization," with only marginal transfer to held-out tasks. Per secondary summaries of the paper, it re-ran harness evolution as Agentic Harness Engineering (explore agent disabled) against direct, parallel, and sequential sampling and a new "harness scaling" baseline at a unified budget on Terminal-Bench 2.1 (89 tasks) with Claude Opus 4.6, GPT-5.4, and GPT-5.4 mini: without unit-test feedback, harness evolution averaged 67.4 pass@1 versus 72.3 for parallel sampling and 68.2 for direct sampling; held-out transfer was about +0.6 points on average, and "many edits encode task-specific fixes rather than transferable strategies" [5][65].

Three independent signals point the same way. METR (Feb 2026) ran Opus 4.5 and GPT-5 through the Claude Code and Codex products and found "neither Claude Code nor Codex outperform the default scaffolds METR uses" (Triframe and a ReAct-style loop); it describes Codex as "a simple loop ... with access to a more sophisticated file editing tool" and Claude Code as a simple main loop that "can spin up subagents" [36]. The self-evolving-agent surveys (Jul and Aug 2025) note evaluation is mostly episodic benchmarks plus LLM-as-judge, with safety framed as jailbreak resistance rather than oversight of a changing system [31][32]; SEA-Eval, a benchmark for evolution over time, appeared only in Apr 2026 [33]. And HGM's finding that a harness's score poorly predicts its descendants' warns that greedy harness search is noisy [23].

Counter-signals: Self-Harness validates on held-out splits [7]; Meta-Harness reports a Pareto frontier rather than one cherry-picked harness [1]; DGM/HGM show transfer across models [23]. None has been independently replicated as far as I could find.

## 7. How much harness remains as models improve?

**METR's time horizon** is the cleanest external yardstick of raw capability: the human task length at which a model succeeds 50% of the time. The March 2025 paper found a roughly 7-month doubling since 2019 [40]. **Time Horizon 1.1** (Jan 29, 2026) grew the suite 34% (228 tasks vs. 170) and doubled the 8-hour-plus tasks (31 vs. 14); under it the post-2023 doubling time is 131 days (vs. 165 under TH1), and "from 2024 onward the doubling rate is just 89 days" [34], which commentators round to 10x per year [42]. METR also published a limitations note [35], a sensitivity analysis of modelling assumptions [37], a Frontier Risk Report [39], and an "expenditure horizon" metric for optimization ability [38] (Jan–Jul 2026). Per-model horizons under TH1.1 are on METR's live page [41], which I could not reach; I do not quote them.

| Evidence | Direction | What it implies |
|---|---|---|
| mini-SWE-agent: bash-only, ~100 lines, >74% Verified [18] | Harness shrinks | For coding, loop and tools collapse into model plus shell |
| METR Feb 2026: product scaffolds do not beat simple ones [36] | Harness shrinks | Scaffold sophistication is not what moves measured capability |
| Meta-Harness: up to 6x swing from harness alone (a figure it cites from prior work); feasibility attributed to coding-agent advances [1][3] | Harness matters, and moves | The optimal harness is model-specific and re-derived per generation |
| Harness survey (Jun 2026): agent = "foundation model coupled with an execution harness"; six runtime responsibilities (observation, context, control, action, state, verification); paradigms run from prompt engineering to "agent-native training with co-evolution" [6] | Harness migrates into training | Vendors will fold harness behavior into the model |
| HAT (Aug 2026): training agents for changing harnesses [16] | Harness treated as environment | Model labs assume the harness keeps changing |
| GEPA (2025) and Meta-Harness (2026) use a coding agent as the optimizer [29][1] | Harness eats itself | Today's product harness designs the next one |

My read: the harness parts with a benchmark (loop, tools, context, orchestration) are being automated and absorbed, in that order. Nothing here automates permissions, runtime, or surface, and the harness survey's six responsibilities include no human-facing one.

## 8. Generative and adaptive interfaces

Analogy: today's chat UI is a teleprinter; generative UI is a printing press that lays out a fresh page per request. The open question is whether the model can also build the *controls*, not just the page.

**Google's generative UI.** Announced with Gemini 3 in Nov 2025 and shipped as "Dynamic View" in the Gemini app and in AI Mode; the paper "Generative UI: LLMs are Effective UI Generators" (Leviathan, Valevski, et al.; arXiv Apr 2026) describes a recipe of prompting, tools, and post-processing, releases PAGEN (expert-crafted pages), and reports generated HTML/CSS/JS preferred over markdown 83% of the time, "when ignoring generation speed" [43][52]. The capability is called "emergent," with no UI-specific training. At I/O in May 2026 Google said generative UI would become free in Search that summer (secondary report) [45]; Jakob Nielsen's favorable Nov 2025 review is commentary [44].

**Independent and adjacent work.** *Generative Interfaces for Language Models* (arXiv Aug 2025) has LLMs answer with generated UIs and reports they "significantly outperform conversational ones across diverse query types"; the exact preference rate is unverified today [46]. *Qualitative Evaluation of LLM-Designed GUI* (Jan 2026) finds good structured layouts but "challenges in meeting accessibility standards and providing interactive functionality" [47]. *AlignUI* (Jan 2026) and *Efficient Personalization of Generative User Interfaces* (Apr 2026) fold user preference or ability into the generation objective [48][49]; *PerceptUI* (Jun 2026) uses LLM agents as synthetic users to evaluate UIs [50]. **A2UI** (Google-originated open standard, v0.9.1 stable, v1.0 release candidate, Sept 2026) has agents emit *declarative JSON* against a client-controlled component catalog, because "running arbitrary code generated by an LLM may present a security risk"; renderers exist for Lit, Angular, React (via CopilotKit), and Flutter, over A2A or AG-UI transports [51].

Reading: generative UI research is about *rendering answers*, not *steering an agent*. Every evaluation is a rater looking at a page; none measures a user controlling a long-running task through a generated interface. A2UI's catalog-not-code rule is the blast-radius logic of harness part 4, and it caps how "reimagined" a generated surface can be.

## 9. HCI on human-agent interaction

The canon is old: Horvitz's mixed-initiative principles (CHI 1999) and Amershi et al.'s 18 human-AI interaction guidelines (CHI 2019), now Microsoft's HAX Toolkit [53]. The 2026 synthesis *Design Principles for Human-Agent Interaction* (arXiv Jun 2026) says these served "bounded and discrete tasks such as recommendations and search"; it is a position paper that proposes 14 principles across four stages (initially, during interaction, over time, when things go wrong) and applies them to nine agent systems (the "106 papers, 15 themes" review figures are unverified), calling the adoption barrier "a lack of design knowledge for successful human-agent interaction" [58]. Workplace-focused principle papers followed in Jun and Jul 2026 [59][63].

Empirical systems are fewer. **Cocoa** (Allen AI/UW, arXiv Dec 2024, later a CHI paper) proposed "co-planning and co-execution": an editable plan document as the shared control surface instead of a chat transcript [60]. **ALLOY** (Oct 2025) generates reusable agent workflows from user demonstration [64]. CHI 2026 (April) had an extended abstract on how users assign responsibility when "multiple specialized agents coordinate behind unified interfaces" [61] and a workshop on GUI agents across modalities [62].

Vendor guidance has not caught up. Microsoft's HAX site still centers the 2019 guidelines; its agent-specific 2026 release is a security toolkit (Agent Governance Toolkit, Apr 2026, covering the OWASP Agentic Top 10 of Dec 2025), not a UX one [54]. Google's PAIR Guidebook has no agent chapter I could find; a 2026 critique says it "was written in a world where AI predicted and recommended but did not act" [56], and Google's agent guidance lives in engineering whitepapers ("Introduction to Agents," Nov 2025) [57].

## What this means for the thesis

**Supports.**
- The harness is a large, model-specific lever: up to 6x swings from harness alone (a figure Meta-Harness cites from prior work, Mar 2026) and 20%→50% SWE-bench from self-rewritten scaffolds (DGM, May 2025) [1][22].
- The harness is a moving target re-derived per model generation, now by another agent. That matches "the harness becomes the bottleneck" as engineering effort, and says the durable product is the *outer loop* (evaluation, traces, regression gates, permissions), not any hand-built inner loop.
- Chat is not the ceiling: generated interfaces beat markdown 83% of the time [43] and beat chat in a separate study [46]; HCI syntheses say validated design for agents is missing [58]. A gap is an opportunity.

**Contradicts.**
- In the best-benchmarked domain, coding, the inner harness is shrinking: a 100-line bash-only agent gets >74% Verified [18], and product scaffolds did not beat METR's simple ones [36]. Under the narrow reading, "the harness is the bottleneck" is false for loop and tools.
- The strongest independent evaluation says automated harness design has not been shown to beat test-time scaling at equal cost and overfits its benchmark [5]. A startup whose moat is "we learn the harness" has weak evidence behind it as of Sept 2026.
- Generative UI evidence excludes latency, measures page preference rather than task control, and finds interactivity and accessibility gaps [43][47]. No study shows a generated interface lets non-technical people operate a long-running agent better than a chat app.
- HCI offers principles and thematic reviews; it does not show any specific surface (CLI, desktop, web, generated) is the wrong default. Absence of evidence, not evidence of absence.

**Nuance, bluntly.** The research uses "harness" to mean the inner loop plus context, exactly the part models are absorbing and agents are learning to write. The thesis is really about the outer harness: permissions, runtime, surface, and the human as evaluator, which has almost no academic evidence either way because it has no benchmark. The defensible thesis: *the inner harness is being automated; the outer harness, which makes a human the evaluator and steering loop, is unautomated and under-studied, and that is where a product can live.* The indefensible version is that a hand-designed harness stays a bottleneck; the 2026 papers argue the opposite, and METR's 89-to-131-day doubling means whatever harness is right today is wrong within a year.

## Open questions and unverified claims

- Meta-Harness's Terminal-Bench 2.0 results are now confirmed via secondary sources (76.4% Opus 4.6, 37.6% Haiku 4.5); compute cost beyond "a single search run completes within hours of wall-clock time" and cross-model transfer details remain unverified (arXiv, project page, and mirrors blocked).
- Authors and the methods re-run in "Rethinking the Evaluation of Harness Evolution for Agents" are now identified from secondary sources (see §6 and verification notes); affiliations and the paper's exact wording remain unverified.
- Per-model 50% and 80% time horizons under METR TH1.1, and any METR measurements of mid-2026 models: not quoted; metr.org unreachable.
- Recalled figures, now confirmed against abstracts and READMEs mirrored on GitHub (see verification notes): SWE-agent 12.5% pass@1; ADAS +13.6 F1 (DROP), +14.4% (MGSM); AFlow 5.7% / 19.5% / 4.55%; AgentSquare 17.2%; AWM 24.6% / 51.1%; SICA 17%→53%; DGM's reported cases of hallucinated tool use and removal of its own hallucination-detection markers (the last via secondary quotations of the Sakana post).
- The exact preference rate in "Generative Interfaces for Language Models," and whether its participants included non-technical users.
- Whether "Meng et al." surveying "22 harness systems" with labeled-transition-system semantics is the June 2026 harness survey [6] or a separate paper.
- No paper found that evaluates a *generated* control surface for a running agent with real users; none found that automates permissions, runtime, or surface design.

## Sources

1. Meta-Harness: End-to-End Optimization of Model Harnesses (Lee, Nair, Zhang, Lee, Khattab, Finn) — https://arxiv.org/abs/2603.28052 — Mar 2026
2. Meta-Harness reference code, stanford-iris-lab — https://github.com/stanford-iris-lab/meta-harness — 2026
3. Meta-Harness write-up (Maxim blog, secondary) — https://www.getmaxim.ai/blog/meta-harness-what-if-we-let-an-agent-optimize-the-code-around-an-llm/ — 2026
4. Awesome-Harness-Self-Improvement reading list — https://github.com/leezythu/Awesome-Harness-Self-Improvement — 2026
5. Rethinking the Evaluation of Harness Evolution for Agents (Wang et al.) — https://arxiv.org/abs/2607.12227 — Jul 2026
6. From Question Answering to Task Completion: A Survey on Agent System and Harness Design — https://arxiv.org/abs/2606.20683 — Jun 2026
7. Self-Harness: Harnesses That Improve Themselves — https://arxiv.org/abs/2606.09498 — Jun 2026
8. HarnessForge: Joint Harness and Policy Evolution for Adaptive Agent Systems (Chen, Lv, Zhang et al.; code https://github.com/mingju-c/HarnessForge) — https://arxiv.org/pdf/2606.01779 — Jun 2026
9. Harness Handbook: Making Evolving Agent Harnesses Readable, Navigable, and Editable — https://huggingface.co/papers/2607.13285 — Jul 2026
10. HarnessDev: Can LLMs Create and Evolve Their Own Agent Harness? — https://arxiv.org/html/2609.01437 — Sep 2026
11. Natural-Language Agent Harnesses — https://arxiv.org/html/2603.25723v1 — Mar 2026
12. Adapting the Interface, Not the Model: Runtime Harness Adaptation — https://arxiv.org/html/2605.22166v1 — May 2026
13. Code as Agent Harness — https://arxiv.org/pdf/2605.18747 — May 2026
14. Hyperagents (Meta FAIR; Zhang, Zhao, Yang, Foerster, Clune, Jiang, Devlin, Shavrina; code https://github.com/facebookresearch/HyperAgents) — https://arxiv.org/abs/2603.19461 — Mar 2026
15. Ouroboros: Self-Developing Frontier Coding Agent — https://arxiv.org/abs/2608.08311 — Aug 2026
16. Training Agents to Evolve with Their Harness: TaoLive Digital Avatar Agent Technical Report (Harness-Aware Training, HAT) — https://arxiv.org/abs/2608.15763 — Aug 2026
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

## Verification notes (2026-09-08)

**Method.** The web-search allowance was exhausted after five searches in this pass (the session's 200-search budget had been spent earlier), so checks relied on those five result sets plus fetches of reachable hosts: project READMEs and raw files on GitHub, third-party GitHub repositories that mirror arXiv abstracts and paper notes (AkihikoWatanabe/paper_notes, CSQianDong/Awesome-arXiv-Daily-Reporter, HuggingAGI/HuggingArxiv, qhduan/cn-chat-arxiv, masamasa59/ai-agent-papers, deusyu/harness-engineering, BobYeger/state-of-agents, lhl/realitycheck-data, and others found by GitHub code search), the METR analysis repository (METR/eval-analysis-public), and microsoft.com. "Confirmed" means a primary GitHub artifact (project README, abstract mirror, repository file) or two independent secondary notes agree with the text; "corrected" means the text was changed; "unverified" means no reachable source settled it. No primary paper PDF or HTML on arxiv.org was opened.

| # | Claim checked | Verdict | Evidence and notes |
|---|---|---|---|
| 1 | Meta-Harness authors (Lee, Nair, Zhang, Lee, Khattab, Finn), Stanford/KRAFTON/MIT, proposer = Claude Code with Opus 4.6, code "not tested beyond verifying that it runs" | confirmed | stanford-iris-lab/meta-harness README and its Terminal-Bench 2 skill file ("Model: Claude Opus 4.6"); abstract mirror (paper_notes #5059); full-text translation in deusyu/harness-engineering |
| 2 | +7.7 points over ACE with 4x fewer context tokens; 48.6% vs 40.9%; LawBench +16 (45.0 vs 29.0); Symptom2Disease +9 (86.8 vs 77.8) | confirmed | abstract; results table reproduced in Aidenzich/road-to-master (11.4K vs 50.8K context tokens; MCE 40.0 at 28.5K) and azrod/opencode-team-lead notes |
| 3 | "median 82 files read per iteration" | confirmed | paper Appendix A.1 / Table 8 via the translation: median 82 (range 69-99) in the most demanding setting, about 41% prior harness source and 40% execution traces |
| 4 | Harness alone can swing results "by as much as 6x" | corrected | Section 1 of the paper states it while citing prior work (Tian et al., 2026, "SWE-bench mobile"); it is not a Meta-Harness measurement. Text re-attributed in the TL;DR, section 4, the section 7 table, and "Supports" |
| 5 | Workflow "only became practical recently, following major improvements in coding-agent capabilities around early 2026" | corrected | Not in the paper; the paper says the domain was opened by "advances in coding agents" without dating it. The "early 2026" timing is commentary (Maxim blog [3]; Aidenzich notes). Text re-attributed |
| 6 | Terminal-Bench 2.0 results (previously "not verifiable") | confirmed, added | 76.4% with Opus 4.6 (#2 behind ForgeCode at 81.8%, which the authors could not reproduce from public code) and 37.6% with Haiku 4.5 (#1), agreed by three independent GitHub notes; "a single search run completes within hours of wall-clock time" |
| 7 | "Rethinking the Evaluation of Harness Evolution for Agents" exists (arXiv 2607.12227, Jul 2026) and its authors | confirmed, authors added | arXiv listing via search snippet ("Yike Wang and 9 other authors"); full list and date 2026-07-14 in deusyu/harness-engineering section 68 |
| 8 | Its quoted claims: "risk overfitting to that specific task set", "matched feedback and inference budgets", "does not consistently outperform simple test-time scaling methods and exhibits limited generalization" | confirmed | abstract mirror (qhduan/cn-chat-arxiv) and search snippet |
| 9 | "near-zero transfer" and "memorizing fixes instead of learning general scaffolding strategies" | corrected | Not found in any reachable rendering of the paper; they read as the AI Weekly paraphrase [65]. Two secondary summaries (vollero/hf-daily-paper-summaries; matt-seb-ho/sci-sim-op notes) agree on: Terminal-Bench 2.1, 89 tasks, unified budget K=5; harness evolution instantiated as Agentic Harness Engineering with the explore agent disabled; baselines direct, parallel, and sequential sampling plus a "harness scaling" baseline; models Claude Opus 4.6, GPT-5.4, GPT-5.4 mini; pass@1 without unit-test feedback 68.2 direct, 72.3 parallel, 69.3 sequential, 67.4 harness evolution, 71.8 harness scaling; with unit tests 86.0 parallel vs 75.8 harness evolution; held-out gain +0.6 on average (+1.2 Opus 4.6, +0.0 GPT-5.4); "many edits encode task-specific fixes rather than transferable strategies". Text replaced with these figures |
| 10 | SWE-agent 12.5% pass@1 on full SWE-bench with GPT-4 Turbo | confirmed | SWE-agent README at tag v0.6.1: "SWE-agent resolves 12.47% of issues, achieving the state-of-the-art performance on the full test set"; SWE-bench/experiments entry 20240402_sweagent_gpt4 (gpt-4-1106-preview) |
| 11 | ADAS +13.6 F1 (DROP), +14.4% (MGSM) | confirmed | abstract quoted in Haozhe-Xing/agent_learning and imjeson/byteai (also +25.9% / +13.2% on GSM8K / GSM-Hard after transfer) |
| 12 | AFlow 5.7% / 19.5% / 4.55% | confirmed | abstract (HuggingAGI/HuggingArxiv; GreenHandHand/markdown-note): 5.7% over state-of-the-art baselines, 4.55% of GPT-4o inference cost; contribution list (HKUSTDial/Supervisor-Skills): "surpasses existing automated approaches by 19.5%" |
| 13 | AgentSquare 17.2% average gain | confirmed | abstract mirror (HuggingArxiv): "average performance gain of 17.2% against best-known human designs" across six benchmarks |
| 14 | AWM 24.6% / 51.1% relative gains; 35.6% on WebArena | confirmed | abstract (paper_notes #1854); repository README ("35.6% success rate") |
| 15 | SICA 17% to 53% | confirmed | paper text mirror (standardgalactic/kitbash): "from 17% to 53% ... on a random subset of SWE Bench Verified" (a 50-problem subset per BobYeger notes); ICLR 2025 workshop venue per the repository README; the arXiv ID 2504.15228 was not itself confirmed |
| 16 | DGM 20.0% to 50.0% (SWE-bench), 14.2% to 30.7% (Polyglot); hallucinated tool use and removal of hallucination-detection markers | confirmed | abstract (paper_notes #2012, listed as ICLR'26); the safety anecdote via three GitHub secondaries quoting sakana.ai/dgm ("asked to reduce hallucination it removed the markers used to detect hallucination"); the Sakana page itself is blocked |
| 17 | HGM: metaproductivity-performance mismatch, clade selection, GPT-5-mini search transferring to GPT-5 on SWE-bench Lite and matching human-engineered agents; ICLR 2026 oral | confirmed | abstract mirror (CSQianDong daily reporter, 27-Oct-2025); repository README ("oral presentation in ICLR 2026") |
| 18 | METR TH1.1: Jan 29 2026; 228 vs 170 tasks (+34%); 31 vs 14 tasks over 8 hours; 131-day post-2023 doubling (vs 165 under TH1); 89 days from 2024 (vs 109 under TH1) | confirmed | search snippets (228/170, 14 to 31, 29 January, 89 days); two independent GitHub notes quoting the METR post give 131/165 and 89/109 (BobYeger/state-of-agents; lhl/realitycheck-data). Caveat: several commentaries (ai2027-tracker and notes derived from it) give the post-2023 figure as 129 days / 4.3 months; the METR analysis repo confirms the fit windows (2023-onward, 2024-onward) but its computed outputs are not committed. Also noted: METR corrected a modelling bug on 2026-03-03 that lowered Opus 4.6's TH1.1 estimate from about 14.5 h to about 12 h |
| 19 | METR Feb 2026: "neither Claude Code nor Codex outperform the default scaffolds METR uses" for Opus 4.5 and GPT-5; scaffold descriptions | confirmed (one phrase unverified) | search snippet of the METR note; Claude Code "can spin up subagents at will"; Codex "a simple loop that takes actions in a sequence"; the phrase "with access to a more sophisticated file editing tool" was not re-fetched |
| 20 | Google generative UI: Nov 2025 launch with Gemini 3 (Dynamic View), paper by Leviathan, Valevski et al. (arXiv 2604.09577, Apr 2026), 83% preference "when ignoring generation speed", PAGEN, "emergent" | confirmed | search snippets of the arXiv abstract (83%; PAGEN, "match its quality in 50% of cases"; emergent capability); narrowin/awesome-generative-ui; Gemini release notes dated 2025-11-18 cited in a GitHub feature catalog (Dynamic view, Visual layout); thesysdev/openui report |
| 21 | 2026 papers exist under the stated names: Self-Harness (2606.09498, Hangfan Zhang et al.), AutoHarness (2603.03329, Lou et al., Google DeepMind, ICLR 2026), Hyperagents (2603.19461, Meta FAIR, Jenny Zhang et al.; one review gives the subtitle "Self-Referential Agents with Metacognitive Self-Modification"), HarnessForge (2606.01779, Chen, Lv, Zhang et al.), Ouroboros (2608.08311, Razzhigaev and Kaznacheev), HarnessDev (2609.01437, Yuhao Wu et al.), Harness Handbook (2607.13285, Ruhan Wang et al.), Agentic Harness Engineering (2604.25850, Jiahang Lin et al.); also [11], [12], [13], the harness survey [6] (2606.20683, Guo et al., 17 authors) and SEA-Eval [33] (2604.08988) | confirmed | leezythu/Awesome-Harness-Self-Improvement; paper_notes #5753 and #6501; facebookresearch/HyperAgents and mingju-c/HarnessForge READMEs; razzant/ouroboros README; masamasa59/ai-agent-papers lists. Agentic Harness Engineering did not appear in the reading-list README that [4] cites, so its arXiv ID is now given in the table |
| 22 | HAT title "Training Agents to Evolve with Changing Harnesses" | corrected | Actual title: "Training Agents to Evolve with Their Harness: TaoLive Digital Avatar Agent Technical Report" (v1 had the two halves in the other order); HAT = Harness-Aware Training, Alibaba TaoLive AIGC team. Fixed in the section 4 table and source [16] |
| 23 | HarnessDev "Title only" | corrected (findings added) | abstract (paper_notes #6501): a benchmark with Creation and Evolution stages; six creator LLMs, four domains, five downstream benchmarks (2,207 instances); generated harnesses lag mature human-engineered references on code and on search and research, match or exceed them on writing and ML experimentation; evolution gains "are unstable and transfer only partially to held-out tasks" and depend strongly on the runtime model |
| 24 | Design Principles for Human-Agent Interaction: 14 principles across four stages; the quote "a lack of design knowledge for successful human-agent interaction" | confirmed | abstract (arXiv RSS mirror in ehijano/rss_fetch, Jun 2026); authors Zhu, Wang, Xiao, Shen per vasilyu1983/AI-Agents-public notes |
| 25 | The same paper "coded 106 papers, found 15 themes" and says prior guidelines served "bounded and discrete tasks such as recommendations and search" | unverified | The abstract calls it a position paper that applies the principles to nine agent systems; no review count or that quote was found. Text flagged |
| 26 | GEPA "+6% average across six tasks on Qwen3 8B"; "ICLR 2026 oral" | unverified (partly supported) | The v1 abstract says 10% average over GRPO across four tasks (up to 20%, up to 35x fewer rollouts; over 10% above MIPROv2 across two LLMs); two GitHub notes of a later version list six tasks (HotpotQA, IFBench, HoVer, PUPA, AIME-2025, LiveBench-Math) and "+6% average vs GRPO"; "oral" not confirmed (paper_notes lists ICLR'26). Text left as is |
| 27 | mini-SWE-agent ">74%" and README wording; SWE-agent README "65% on SWE-bench Verified in 100 lines"; A2UI v0.9.1 stable / v1.0 release candidate and its renderers; Voyager 3.3x / 2.3x / 15.3x; the harness survey's six runtime responsibilities; the HAX guidelines page centres the 2019 guidelines with no agent-specific update | confirmed | repository READMEs; microsoft.com HAX page; GitHub notes for Voyager and the survey |
| 28 | Microsoft Agent Governance Toolkit (Apr 2026, covering the OWASP Agentic Top 10 of Dec 2025) | unverified | opensource.microsoft.com is blocked |
| 29 | "Generative Interfaces for Language Models" preference rate | unverified | The paper exists (SALT-NLP/GenUI; ACL 2026 Findings per one note); one GitHub audit attributes a 72% user-preference figure to it; not confirmed |

**Counts.** 20 confirmed, 5 corrected, 4 unverified (29 claims).

**Corrections made in the text.**
- Status line changed to "verified with notes".
- The "6x" swing is now attributed to prior work cited by Meta-Harness, not to Meta-Harness's own experiments (TL;DR, section 4, section 7 table, "Supports").
- The "only became practical ... early 2026" statement is no longer attributed to the Meta-Harness authors; Terminal-Bench 2.0 numbers (76.4% Opus 4.6, 37.6% Haiku 4.5) added (section 4, open questions).
- "Rethinking ..." now carries its ten authors, the methods it re-ran, its baselines, models, and pass@1 and transfer numbers; the unsourced quotes "near-zero transfer" and "memorizing fixes ..." were removed (TL;DR, section 6, open questions, source [5]).
- HAT's title corrected; HarnessDev's "Title only" replaced with its abstract's findings; AutoHarness and Agentic Harness Engineering given arXiv IDs (section 4 table; sources [8], [14], [16]).
- "Recalled, unverified" caveats for SWE-agent, ADAS, AFlow, AgentSquare, and AWM replaced with "confirmed" plus the numbers (sections 2, 3, 5, open questions).
- The 2026 HCI synthesis is described as a position paper; its "106 papers, 15 themes" figures are flagged as unverified (TL;DR, section 9).

**Sources that could not be opened.** arxiv.org, alphaxiv.org, huggingface.co, metr.org, metr.substack.com, lesswrong.com, sakana.ai, research.google, generativeui.github.io, openreview.net, api.semanticscholar.org, getmaxim.ai, hugocisneros.com, hyper.ai, aiweekly.co, ai2027-tracker.com, montanaresearch.org, futuresearch.ai, planned-obsolescence.org, awesomegenerativeui.com, opensource.microsoft.com. Anonymous api.github.com listing returned 403 and the GitHub connector was scoped to this repository, so METR's report folders were read through github.com pages and raw files only (params.yaml and dvc.yaml; computed metrics are not committed).

**Remaining doubts.**
- Every Meta-Harness figure beyond the abstract comes from a Chinese full-text translation and English reading notes hosted on GitHub, not from the PDF.
- The "Rethinking ..." numbers rest on two secondary summaries that agree with each other; the paper's own wording, and the authors' affiliations, were not seen.
- METR's post-2023 doubling time is quoted as 131 days here and 129 days in some commentary; both round to 4.3 months. The "vs 165 under TH1" comparison rests on two secondary notes.
- The METR Feb 2026 note's description of Codex's file-editing tool, and the exact wording of its scaffold descriptions, were not re-fetched.
- GEPA's "+6% across six tasks" may reflect a later paper version than the abstract checked.
- Sections 8 and 9 items [47], [48], [49], [50], [56], [57], [59], [61], [62], [63], [64] and the Search Engine Journal report [45] were not checked in this pass.
