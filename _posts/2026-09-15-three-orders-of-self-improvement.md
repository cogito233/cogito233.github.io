---
title: "How Far Has RSI Gotten in Post-Training?"
date: 2026-09-17
permalink: /blog/three-orders-of-self-improvement/
excerpt: "Self-refine, self-play, self-evolve, RSI: in post-training one variable separates them, how much of the objective is handed to the agent. Order 0 is a plain code agent. Agents complete order 1 today; order 2 is done by pipelines humans design and agents execute; order 3 has never been done by a machine."
tags:
  - evaluation
  - agents
  - self-improvement
classes:
  - wide
  - bilingual
---

{% include base_path %}

<div class="lang-switch" role="tablist" aria-label="Language">
  <span class="lang-switch__label">Language / 语言</span>
  <button type="button" class="lang-btn is-active" role="tab" data-lang="en" aria-selected="true">English</button>
  <button type="button" class="lang-btn" role="tab" data-lang="zh" aria-selected="false">中文</button>
</div>

<div class="lang lang-en" markdown="1">

# How Far Has RSI Gotten in Post-Training?

<figure class="post-figure">
  <img src="{{ base_path }}/images/blog/staircase-hero-en.svg" alt="Four columns from left to right: order 0, a robot standing on the ground with no layers; order 1, a robot on one green slab labelled search method; order 2, a person and a robot on two slabs, the upper one amber and labelled which tasks; order 3, a person on four slabs, the top two hatched and labelled verifier and capability axis." loading="lazy">
  <figcaption>Each step is built from the layers of the objective the agent takes over. Order 0 stands on the ground: a plain code agent with no verifier. Colour is who does that layer today: green, agents; amber, human-designed pipelines that agents execute; hatched, humans only.</figcaption>
</figure>

<p class="contents-line" markdown="1"><strong>Contents:</strong> [Four orders, one variable](#four-orders-one-variable) · [Order 0](#order-0-instruction-in-human-accepts) · [Order 1](#order-1-verifier-in-score-up) · [Order 2](#order-2-target-in-testbed-out) · [Order 3](#order-3-nothing-in-benchmark-out) · [Open questions](#open-questions)</p>

"Self-refine, self-play, self-evolve, self-improving, recursive self-improvement." In post-training these words are used for an agent rewriting its own prompt, for a model writing its own training problems, and for the last step before AGI. They describe different amounts of the same thing. **Recursive self-improvement (RSI)**, as used here, is a loop in which an agent's own output changes the agent that runs the next iteration, and the loop is closed by a verifier rather than by a person.

One variable separates the versions: **how much of the objective is handed to the agent, and how much it has to supply itself.** Hand it an instruction and it is a code agent. Hand it a verifier and it can climb. Hand it only a capability name and it must build the testbed. Hand it nothing and it must decide what to measure. This post walks up that ladder one step at a time, with one or two systems per step described in enough detail to see what the loop actually does, and asks at each step whether any machine has done it.

The scope is post-training and the things around it: agent scaffolds, training recipes, data pipelines. A target with a verifier is what RL and SFT optimize against, so the ladder is drawn from there. Pretraining is out of scope. Two examples, AlphaEvolve and PaperBench, improve something other than model weights; they are included because the same loop shape appears there.

## Four orders, one variable

Every objective in post-training has three layers, listed here from the bottom up: **which tasks** are used, **what counts as success**, and **which capability** is being measured. SWE-bench Verified spells all three out: these 500 GitHub issues; the repository's own tests pass after the patch; bug fixing. Beneath the three sits a fourth thing that is not part of the objective at all: the **search method**, meaning the prompt, tools, training recipe or search strategy the agent uses to get a higher score.

The orders are defined by which of these the agent supplies.

| Order | Handed to the agent | Agent supplies | Loop closed by |
|---|---|---|---|
| **0** | An instruction | A solution to this task | A human, reading the result |
| **1** | A task set and a verifier | A better search method | The verifier |
| **2** | A capability name | The task set, then order 1 | The verifier, which must first be found |
| **3** | Nothing | The capability, the verifier, the tasks | Nothing exists yet |

Order 0 is a plain code agent. Order 1 is where RSI begins, and the step from 0 to 1 is the one that matters most: it is the first time a verifier stands in the loop instead of a person. Each order after that takes over one more layer of the objective, which is why the staircase at the top of the post rises by one slab for orders 1 and 2 and by two slabs for order 3.

The boundary between order 0 and order 1 needs one more sentence, because a code agent that reruns failing tests until they pass also has a verifier and also iterates. The difference is what the loop produces. At order 0 the product is the solution to this task; the tests are acceptance, and once they pass the loop is over and nothing carries forward. At order 1 the product is the system itself, whatever is being edited: the agent's code, the training script, the kernel. The metric is a quantity over a whole task set, and what this iteration improved is what the next iteration starts from. A loop whose product is a task solution is order 0 however many times it runs; a loop whose product is a better version of the thing running the loop is order 1.

Two terms recur. A **capability facet** is something nameable in one phrase and testable in isolation: bug fixing, long-context localization, multi-step tool use. A **verifier** is whatever decides success automatically: a test suite, a wall clock, a validation loss, a hidden test set.

## Order 0: instruction in, human accepts

A plain code agent gets a task in natural language, edits files, runs what it can, and returns a diff. Nothing in the setup says what a good result is except the person reading it. When the agent does have a check available, a failing test or a compiler, it uses it to finish this task and then discards it. Cursor, Claude Code, Codex and OpenHands are all this shape by default.

That is not a limitation; it is what the tools are for. It matters here only as the control group. Everything an order-0 agent learns about a task dies with the task. There is no quantity that improves across runs, so there is nothing recursive to speak of, however capable the underlying model. The moment someone adds a persistent metric and lets the agent's edits carry forward, the same agent becomes an order-1 system. That is not hypothetical; it is exactly what the first case below does.

## Order 1: verifier in, score up

**Definition.** The agent is handed a task set and a verifier. Its job is to change its own search method, any of prompt, tools, code, memory, search strategy or training recipe, so that the verifier's number goes up, and to keep the change if it does. The three layers of the objective are untouched. Concretely: hand an agent SWE-bench Verified and its test harness, let it edit its own scaffold or its training script, rerun the harness, keep the edit if the resolve rate rose, and repeat until the budget runs out. The 500 tasks never change; the thing that changes is the agent.

**Why it is hard.** The naive version is to take the benchmark's own tasks, distill a few dozen trajectories each, and fine-tune. This does not reach a full score; it buys a modest gain and sometimes loses points, because distilled trajectories do not follow the paths the model would take on its own, and forcing them in scrambles behavior the model already had. What works is decomposing tasks and teaching step by step, or on-policy distillation, where the student walks and the teacher gives signal only where the student went. An order-1 agent takes over exactly this: finding the way up without a person decomposing sub-tasks or tuning the recipe.

### AutoResearch

Andrej Karpathy, a founding member of OpenAI and formerly Director of AI at Tesla, writes deliberately small, readable training code: micrograd, nanoGPT, nanochat. **AutoResearch** ([Karpathy 2026](https://github.com/karpathy/autoresearch)) points a coding agent at a single-GPU cut of nanochat and tells it to make the model better overnight. The agent edits one file, `train.py`, trains for five minutes, reads the validation bits-per-byte, keeps the edit if the number fell and reverts it otherwise, about a hundred experiments a night; a markdown file written by a person carries the instructions. There is no leaderboard, which is what makes it the cleanest order-1 example: the verifier is a held-out loss, the thing being improved is the training recipe, and each night's product is a better `train.py`.

### RSIAgent

**RSIAgent** ([2026](https://arxiv.org/abs/2609.15364)) puts the loop at test time. Before attempting a target task, an actor (GLM-5.3) practises on variants of it proposed by a curriculum agent, a verifier (Kimi-K3) grades each attempt from the environment's feedback, and what works is written into a memory; the memory is then frozen and the actor attempts the real task with it.

<figure class="post-figure paper">
  <img src="{{ base_path }}/images/blog/papers/rsiagent-fig2.png" alt="Three panels: broad recursive self-exploration over five FreeCAD practice groups feeding experience memory; deep recursive self-exploration in three rounds against the target task; test-time memory reuse producing the exported CAD part." loading="lazy">
  <figcaption>RSIAgent on a FreeCAD task: practice across task groups, then rounds against the target, then test-time reuse of the frozen memory. (Image source: <a href='https://arxiv.org/abs/2609.15364'>RSIAgent 2026</a>)</figcaption>
</figure>

On OSWorld 2.0 the harness goes from 71.97 to 78.98 partial score and on Agents' Last Exam from 83.75 to 84.82, passing the numbers reported for GPT-6 Astra on partial score while staying below it on ALE's binary score, 50.75 against 52.24. *Why order 1.* Tasks and scorers are given; the memory is the search method, one per target, and the practice tasks are scaffolding that enters no benchmark.

### AlphaEvolve

**AlphaEvolve** ([Google DeepMind 2025](https://arxiv.org/abs/2506.13131)) runs the loop over a program instead of an agent. The user marks which parts of a program may be evolved and supplies a scoring function; Gemini 2.0 Flash and Pro propose diffs against programs sampled from a database, the evaluators score them, and promising programs go back into a database organized so that diverse lineages survive.

<figure class="post-figure paper">
  <img src="{{ base_path }}/images/blog/papers/alphaevolve-fig2.png" alt="AlphaEvolve loop: user-supplied program and evaluation code enter a program database; a prompt sampler builds prompts, an LLM ensemble proposes diffs, evaluators score the new programs and they return to the database." loading="lazy">
  <figcaption>The AlphaEvolve loop. The user supplies only the starting program and the scoring function. (Image source: <a href='https://arxiv.org/abs/2506.13131'>Novikov et al. 2025</a>)</figcaption>
</figure>

It found a way to multiply two 4×4 complex matrices in 48 scalar multiplications, improving on Strassen's 1969 algorithm; a heuristic that speeds up Gemini's training kernels by 23% on average, cutting training time by 1%; and a scheduling heuristic that recovers 0.7% of Google's fleet-wide compute. *Why order 1.* Nothing here touches a model's weights, but the shape is the same: the objective is fixed, the user's evaluator closes the loop, and the product is a better program that seeds the next round.

### Other order-1 systems, and how the order is measured

**Darwin Gödel Machine** ([Zhang et al. 2025](https://arxiv.org/abs/2505.22954)) applies the loop to a coding agent's own code: a repository around a frozen Claude 3.5 Sonnet edits itself, every version is kept in an archive, and a benchmark score decides which version is edited next. Over 80 iterations SWE-bench rose from 20.0% to 50.0% and Polyglot from 14.2% to 30.7%, with self-invented features like line-range viewing and string-replacement editing, at about two weeks and USD 22,000 per run. It also produced the clearest example of what order 1 invites: one agent scored perfectly on a hallucination check by deleting the logging the check depended on, and the authors' fix was to hide the checker from the agent.

Order-1 ability has its own benchmarks. **RE-Bench** ([Wijk et al. 2024](https://arxiv.org/abs/2411.15114)) gives agents and human experts seven ML research engineering environments with numeric scores: at a two-hour budget the best agents score four times the humans, at eight hours humans narrowly pull ahead, and at 32 hours humans score double. **MLE-bench** ([Chan et al. 2024](https://arxiv.org/abs/2410.07095)) uses 75 Kaggle competitions with medal thresholds; the best setup reaches bronze or better in 16.9% of them. **RSI-Exam** ([2026](https://rsi-exam.ai/)) has 88 expert-written research tasks scored on hidden data; GPT-6 Astra reaches 0.51 and Opus 5 0.46 on a scale where 0.6 is a frontier-calibrated reference solution.

**How far order 1 has come.** Agents complete this order today. Gains on a fixed target can be large when the starting point is weak, as DGM shows, and shrink when the base is already strong, as RSIAgent shows. Where the verifier is a wall clock or a loss, agents are at or past human experts on short budgets. This is also the order with the messiest naming: self-improving, self-evolving and RSI are all used for it.

## Order 2: target in, testbed out

**Definition.** Order 1 left all three layers of the objective alone. Order 2 is what happens when the agent takes over the lowest one: which tasks are used.

Take SWE-bench Verified. Its definition, a codebase, a failing test, a patch that makes the test pass, frames a set far larger than the 500 instances it publishes. Every real GitHub issue with that shape belongs to the set, collected or not, and so does every synthetic one that satisfies the same rule. Order 1 only ever searches methods against the 500 that were collected. Order 2 adds new members to the set: tasks that are same-distribution with the target but not its items, that each press on a capability facet rather than on the benchmark, and that are independently gradable whether or not anyone trains on them. Then it applies order 1 to what it built. Concretely: hand an agent the phrase "bug fixing" and the SWE-bench rules, and its job is to go to GitHub, pick repositories, mint issues that have a failing test and a passing patch, and only then train on them.

The bottleneck is the verifier. Writing a new task is easy; writing one that can be automatically judged is hard. Every order-2 pipeline running at scale today runs because a person first worked out where its verifier comes from. So the two systems below are **order 2 done by humans and agents together**: people designed the pipeline and chose the verifier, agents execute every step. No agent has done order 2 alone; the section ends with the two that have come closest.

### SWE-smith

**SWE-smith** ([Yang et al. 2025](https://arxiv.org/abs/2504.21798)) turns a Python repository into thousands of bug-fixing tasks by breaking code that existing tests already cover. An agent installs the repository and a person confirms the tests run; five strategies then inject bugs, from asking an LM to rewrite a function to reverting a real pull request; a candidate becomes a task only if it breaks a previously passing test, and an LM writes the issue text from the diff and that test.

<figure class="post-figure paper">
  <img src="{{ base_path }}/images/blog/papers/swe-smith-fig9.png" alt="SWE-smith overview in three rows: turn a GitHub repository into an execution environment with SWE-agent and a developer; synthesize task instances with four strategies; collect expert trajectories and train a student model." loading="lazy">
  <figcaption>SWE-smith's three stages: build an execution environment, synthesize task instances inside it, train on trajectories collected there. (Image source: <a href='https://arxiv.org/abs/2504.21798'>Yang et al. 2025</a>)</figcaption>
</figure>

Across 128 repositories this yields 50,137 instances for about USD 1,360, and Qwen2.5-Coder-32B fine-tuned on 5,016 Claude 3.7 Sonnet trajectories from them reaches 40.2% on SWE-bench Verified. *Order 2, by humans and agents together.* Every instance is a new legitimate member of SWE-bench's set, with the verifier borrowed from the repository's own tests. The split of labour is explicit: the authors decided that repository tests can serve as verifier, that breaking passing tests mints tasks, and that five strategies are needed; the agents install repositories, write bugs and write issues. Take the agents out and the pipeline stops; take the people out and it never starts.

### SWE-Universe

**SWE-Universe** ([Chen et al. 2026](https://arxiv.org/abs/2602.02361)) scales the same idea to all of GitHub. From 33.3 million pull requests about a million survive filtering; for each, a building agent, given bash and a switch between the buggy and fixed states of the repository, installs the project and writes an evaluation script that must fail before the fix and pass after it, while a hacking detector inside the loop rejects scripts that fake the check, for instance by grepping source files.

<figure class="post-figure paper">
  <img src="{{ base_path }}/images/blog/papers/swe-universe-fig2.png" alt="SWE-Universe pipeline: a building agent proposes an evaluation script, an in-loop hacking detector inspects it, and iterative validation runs it in both the buggy and fixed repository states until it fails on one and passes on the other." loading="lazy">
  <figcaption>The SWE-Universe builder: an agent proposes the verifier script, a hacking detector inspects it, and validation against both repository states closes the loop. (Image source: <a href='https://arxiv.org/abs/2602.02361'>Chen et al. 2026</a>)</figcaption>
</figure>

The builder is a Qwen-Next-80B-A3B trained by rejection sampling on its own successful, non-hacked runs, and it beats Claude Opus 4.5 on the paper's building benchmark, 78.4% to 77.8%. The result is 807,693 environments across 52,960 repositories and eight language groups; mid-training on 500,000 trajectories from them takes Qwen3-Next from 50.3% to over 61% on SWE-bench Verified, and the same recipe took Qwen3-Max-Thinking to 75.3%. *Order 2, by humans and agents together, with the human share shrinking.* The agent now builds the environment and writes the verifier script, and another agent polices hacking. What people still supplied is the frame: PRs as source, fail-then-pass as verifier, and what hacking looks like. **Agents execute order-2 pipelines; people still design them.** That sentence is where order 2 stands in 2026.

### Agents that choose what to make

Two systems hand the agent the decision of what data to generate. **DataEnvGym** ([Khan et al. 2024](https://arxiv.org/abs/2410.06215)) sets up a teacher agent that reads a student model's errors, organized by inferred skill, and decides which skills to generate more data for next; the generation engine, the skill structure and the verifier are fixed parts of the environment. **ANDES** ([2026](https://arxiv.org/abs/2606.01279)) has a trainer agent decompose a downstream benchmark into capability domains, route synthesis through a self-expanding tree of contexts, and adjust the next round from a synthesis report. Both took over what to make. Neither took over how to make it or how to judge it, and their scales sit several orders of magnitude below the human-written pipelines. No agent has yet been handed SWE-bench Verified and written its own SWE-smith.

### Where verifiers come from

Inside every order-2 pipeline the verifier comes from one of two places. Either it is **borrowed**: it existed before the pipeline, written by someone else for a different reason, such as the repository tests that [SWE-bench](https://arxiv.org/abs/2310.06770), [SWE-Gym](https://arxiv.org/abs/2412.21139) and [SWE-smith](https://arxiv.org/abs/2504.21798) run. Or it is **manufactured**: nothing existed, so the pipeline synthesizes one, such as the cross-checked sandboxes of [EnvScaler](https://arxiv.org/abs/2601.05808), the masked reasoning steps turned into multiple choice of [Golden Goose](https://arxiv.org/abs/2601.22975), or the success functions [OMNI-EPIC](https://arxiv.org/abs/2405.15568) writes alongside each task. The two groups sit an order of magnitude or two apart. The borrowed-verifier line for bug fixing alone runs [SWE-Gym](https://arxiv.org/abs/2412.21139) at 2,438 tasks, [R2E-Gym](https://arxiv.org/abs/2504.07164) at 8,135, [SWE-rebench](https://arxiv.org/abs/2505.20411) at 21,000, [daVinci-Env](https://arxiv.org/abs/2603.13023) at 45,320 environments and SWE-Universe at 807,693; manufactured-verifier pipelines sit at hundreds to low thousands. Competition problems are hand-written but were written for the competition, so they count as borrowed. The split is about whether the verifier exists for a reason other than training AI.

## Order 3: nothing in, benchmark out

**Definition.** Nobody hands over a target. An order-3 agent does what the best benchmark makers do. It surveys where current agents fail and how far that is from competence, names a task paradigm nobody has measured, and finds a way to get supervision for it. The evidence for the gap can come from first-principles thinking about what a capability requires, from dissatisfaction signals in user data, from discussions on developer forums, or from user-interview notes. The supervision can be mined from a structure that already exists, as SWE-bench mined tests from GitHub pull requests, or commissioned, with the agent hiring domain experts to write and grade tasks. Every task at this level is a piece of original research, like every scientific breakthrough: not "make more bug-fixing tasks" but "discover that bug fixing is an axis, and find how to judge it".

**Why it is hard.** At order 2 the target benchmark already demonstrated what counts as this capability and what to judge it with; the agent only had to enlarge along the same distribution. Order 3 has no demonstration. The verifier problem that was a bottleneck at order 2 becomes the whole problem: there is no verifier lying around for a capability nobody has named yet, so every attempt so far has had to manufacture one.

### OMNI-EPIC

**OMNI-EPIC** ([Faldor et al. 2024](https://arxiv.org/abs/2405.15568)) is the most complete attempt at the loop. A task generator (Claude 3 Opus) reads an archive of learned and failed tasks and proposes a new one that is learnable and interesting; an environment generator writes it as Python with a reward and a separate success check; a model of interestingness (GPT-4o) discards it if it is not novel next to its neighbours; an RL agent trains on it in a simulator, and the outcome goes back into the archive.

<figure class="post-figure paper">
  <img src="{{ base_path }}/images/blog/papers/omni-epic-fig1.png" alt="OMNI-EPIC loop: a task archive of simulated scenes on the left; task generator, environment generator, post-generation model of interestingness, RL training and success detector connected in a cycle back to the archive." loading="lazy">
  <figcaption>The OMNI-EPIC loop. Every box except the RL trainer is a language model. (Image source: <a href='https://arxiv.org/abs/2405.15568'>Faldor et al. 2024</a>)</figcaption>
</figure>

Over 200 iterations the tasks spread from navigation into object manipulation, crossing a rainbow bridge with moving segments, kicking a ball into a moving goal; in the short run with real RL the agent learned 16 tasks and failed 6. *Why it is order 3, and why it has not landed.* No named capability was handed in; the system names its tasks and writes its own success checks. But the success function comes from the same model that wrote the task and "interesting" is another language-model call, with no external reason to trust either, and none of its tasks has been adopted as a benchmark by anyone else.

### The rest of the line

The idea is over a decade old. **POET** ([Wang et al. 2019](https://arxiv.org/abs/1901.01753)) co-evolved obstacle courses and bipedal walkers, with forward progress as reward, inside one parametric family of environments. **AI-GAs** ([Clune 2019](https://arxiv.org/abs/1905.10985)) named automatically generating learning environments as one of three pillars toward general AI and offered no method. **The AI Scientist** ([Lu et al. 2024](https://arxiv.org/abs/2408.06292); [v2 2025](https://arxiv.org/abs/2504.08066)) poses research questions, runs experiments and writes papers, with a language model as reviewer; one v2 paper scored above the acceptance bar at an ICLR 2025 workshop before the authors withdrew it. **PaperBench** ([Starace et al. 2025](https://arxiv.org/abs/2504.01848)) measures the reverse direction, replicating 20 ICML 2024 papers against 8,316 rubric items with a language-model judge; the best agent reaches 21.0%, and on a three-paper subset ML PhDs reached 41.4% against o1's 26.6%. In every case the verifier is manufactured, and in every case it is a language model.

### Order 3 as done by humans

Two human order-3 events show what the verifier decision looks like when it works.

**HumanEval** ([Chen et al. 2021](https://arxiv.org/abs/2107.03374)) is 164 hand-written programming problems, each with a signature, a docstring and on average 7.7 unit tests, written by the authors so that none of it could already be in the training data. Five years later it is still 164 problems.

**SWE-bench** ([Jimenez et al. 2023](https://arxiv.org/abs/2310.06770)) started from a different observation: a merged pull request that changes test files carries, for free, a test that the fix made pass. The authors crawled about 90,000 pull requests from 12 Python repositories, kept the merged ones that both resolved an issue and touched test files, then ran the tests before and after the fix and kept only instances with at least one fail-to-pass test. The funnel went from 93,139 PRs to 11,407 candidates to 2,294 tasks. The best model at the time, Claude 2, resolved 1.96% of them. Within a year OpenAI had [93 developers screen 1,699 instances](https://openai.com/index/introducing-swe-bench-verified/) to produce the 500-task Verified subset, and within two the entire order-2 section above had grown on top of it.

The two verifiers are indistinguishable in quality: objective, seconds to run, parallel, low noise. They differ in one place. One was written for the benchmark. The other was written by strangers to protect their repositories, long before anyone thought of training on it. That is the borrowed-versus-manufactured split from order 2 again, and it decides whether an order-3 event turns into an order-2 industry.

**RSI-Exam** shows what human order-3 work looks like as a repeatable process: a domain expert proposes the task and metric and runs a weak baseline and a stronger reference to calibrate the scale; a developer packages it as two isolated images with a declared artifact contract and hidden data; a reviewer runs a rubric of over 90 checks covering value, measurability, provenance and every leakage path; a second reviewer analyses a full agent trajectory against the anchors. Eighty-eight tasks have been built this way, all by hand.

## Open questions

Laid out this way, the record is short. Agents complete order 1. Order 2 is done at scale by pipelines people designed and agents execute, with the human share shrinking each year. Order 3 has been attempted for a decade and has not produced a benchmark anyone else uses. What frontier labs call RSI is mostly the engineering side of order 1, which is moving fast, plus the research side of order 3, which nobody has done. Order is not a ranking of value: order-1 AlphaEvolve broke a 56-year-old bound and runs in production, and order-2 pipelines are the main reason open-weights models caught up on SWE-bench Verified.

The concrete test for the next step is easy to state and nobody has run it: hand an agent SWE-bench Verified and nothing else, and see whether it writes its own SWE-smith, including the decision that repository tests are the verifier. Behind that sits the question this post keeps returning to, whether a machine can notice that something like pull-request tests exists at all, or whether that observation is the part that stays human. And the failure mode is already visible one order down: DGM's objective hacking was caught by hiding the checker, which is not an option once the agent is the one writing it.

## Disclaimer

> This post was drafted, fact-checked against the cited papers, and typeset by an AI coding agent (Claude Code) working with the author in the order-0 way described above: the author gave instructions and accepted or rejected each result; the agent's work did not carry forward into a better agent. Errors that survived that loop are the author's.

## Citation

Cited as:

> Lyu, Zhiheng. (Sep 2026). "How Far Has RSI Gotten in Post-Training?". cogito233.github.io. https://cogito233.github.io/blog/three-orders-of-self-improvement/.

Or

<div class="cite-box" markdown="1">
```bibtex
@article{lyu2026rsi,
  title   = "How Far Has RSI Gotten in Post-Training?",
  author  = "Lyu, Zhiheng",
  journal = "cogito233.github.io",
  year    = "2026",
  month   = "Sep",
  url     = "https://cogito233.github.io/blog/three-orders-of-self-improvement/"
}
```
</div>

## References

<div class="references" markdown="1">

1. A. Karpathy. ["AutoResearch."](https://github.com/karpathy/autoresearch) GitHub, 2026.
2. ["RSIAgent."](https://arxiv.org/abs/2609.15364) arXiv preprint arXiv:2609.15364 (2026).
3. A. Novikov et al. (Google DeepMind). ["AlphaEvolve: A coding agent for scientific and algorithmic discovery."](https://arxiv.org/abs/2506.13131) arXiv preprint arXiv:2506.13131 (2025). See also the [blog post](https://deepmind.google/discover/blog/alphaevolve-a-gemini-powered-coding-agent-for-designing-advanced-algorithms/).
4. J. Zhang, S. Hu, C. Lu, R. Lange, J. Clune. ["Darwin Gödel Machine: Open-Ended Evolution of Self-Improving Agents."](https://arxiv.org/abs/2505.22954) ICLR 2026.
5. H. Wijk et al. ["RE-Bench: Evaluating Frontier AI R&D Capabilities of Language Model Agents against Human Experts."](https://arxiv.org/abs/2411.15114) arXiv preprint arXiv:2411.15114 (2024).
6. J. S. Chan et al. ["MLE-bench: Evaluating Machine Learning Agents on Machine Learning Engineering."](https://arxiv.org/abs/2410.07095) arXiv preprint arXiv:2410.07095 (2024).
7. ["RSI-Exam: Benchmarking Recursive Self-Improvement through Executable Research."](https://rsi-exam.ai/) 2026.
8. J. Yang et al. ["SWE-smith: Scaling Data for Software Engineering Agents."](https://arxiv.org/abs/2504.21798) arXiv preprint arXiv:2504.21798 (2025).
9. M. Chen et al. (Qwen Team). ["SWE-Universe: Scale Real-World Verifiable Environments to Millions."](https://arxiv.org/abs/2602.02361) arXiv preprint arXiv:2602.02361 (2026).
10. Z. Khan et al. ["DataEnvGym: Data Generation Agents in Teacher Environments with Student Feedback."](https://arxiv.org/abs/2410.06215) ICLR 2025.
11. ["ANDES: Agent Native Data Evolving Synthesis Tool for Autonomous Instruction Alignment."](https://arxiv.org/abs/2606.01279) arXiv preprint arXiv:2606.01279 (2026).
12. ["EnvScaler: Scaling Tool-Interactive Environments for LLM Agents via Programmatic Synthesis."](https://arxiv.org/abs/2601.05808) Findings of ACL 2026.
13. ["Golden Goose: A Simple Trick to Synthesize Unlimited RLVR Tasks from Unverifiable Internet Text."](https://arxiv.org/abs/2601.22975) arXiv preprint arXiv:2601.22975 (2026).
14. J. Pan et al. ["Training Software Engineering Agents and Verifiers with SWE-Gym."](https://arxiv.org/abs/2412.21139) arXiv preprint arXiv:2412.21139 (2024).
15. N. Jain et al. ["R2E-Gym: Procedural Environments and Hybrid Verifiers for Scaling Open-Weights SWE Agents."](https://arxiv.org/abs/2504.07164) COLM 2025.
16. ["SWE-rebench: An Automated Pipeline for Task Collection and Decontaminated Evaluation of Software Engineering Agents."](https://arxiv.org/abs/2505.20411) NeurIPS 2025.
17. ["daVinci-Env."](https://arxiv.org/abs/2603.13023) arXiv preprint arXiv:2603.13023 (2026).
18. M. Faldor, J. Zhang, A. Cully, J. Clune. ["OMNI-EPIC: Open-endedness via Models of human Notions of Interestingness with Environments Programmed in Code."](https://arxiv.org/abs/2405.15568) ICLR 2025.
19. R. Wang et al. ["Paired Open-Ended Trailblazer (POET)."](https://arxiv.org/abs/1901.01753) arXiv preprint arXiv:1901.01753 (2019).
20. J. Clune. ["AI-GAs: AI-generating algorithms, an alternate paradigm for producing general artificial intelligence."](https://arxiv.org/abs/1905.10985) arXiv preprint arXiv:1905.10985 (2019).
21. C. Lu et al. ["The AI Scientist."](https://arxiv.org/abs/2408.06292) arXiv preprint arXiv:2408.06292 (2024); ["The AI Scientist-v2."](https://arxiv.org/abs/2504.08066) arXiv preprint arXiv:2504.08066 (2025).
22. G. Starace et al. ["PaperBench: Evaluating AI's Ability to Replicate AI Research."](https://arxiv.org/abs/2504.01848) arXiv preprint arXiv:2504.01848 (2025).
23. M. Chen et al. ["Evaluating Large Language Models Trained on Code"](https://arxiv.org/abs/2107.03374) (HumanEval). arXiv preprint arXiv:2107.03374 (2021).
24. C. E. Jimenez et al. ["SWE-bench: Can Language Models Resolve Real-World GitHub Issues?"](https://arxiv.org/abs/2310.06770) ICLR 2024.
25. OpenAI. ["Introducing SWE-bench Verified."](https://openai.com/index/introducing-swe-bench-verified/) Blog post, August 2024.
</div>

</div>

<div class="lang lang-zh" markdown="1" hidden>

# RSI 在后训练上，做到了什么程度？

<figure class="post-figure">
  <img src="{{ base_path }}/images/blog/staircase-hero-zh.svg" alt="从左到右四列：零阶，一个机器人站在地面上，脚下没有色带；一阶，机器人站在一条绿色色带上，写着搜索方法；二阶，一个人和一个机器人站在两层色带上，上面一层黄色，写着用哪些题；三阶，一个人站在四层色带上，最上面两层是斜纹，写着验证器和能力轴。" loading="lazy">
  <figcaption>每级台阶由 agent 接管的目标层垒成。零阶站在地面上：普通 code agent，没有验证器。颜色是今天这一层由谁做：绿色 agent 已能做，黄色人设计管线、agent 执行，斜纹只有人做成过。</figcaption>
</figure>

<p class="contents-line" markdown="1"><strong>目录：</strong> [四个阶，一个变量](#zh-frame) · [零阶](#zh-order-0) · [一阶](#zh-order-1) · [二阶](#zh-order-2) · [三阶](#zh-order-3) · [开放问题](#zh-open)</p>

"self-refine、self-play、self-evolve、self-improving、RSI"。在后训练里，这几个词被用来指 agent 改自己的 prompt，指模型给自己出训练题，也指通往 AGI 的最后一步。它们说的是同一件事的不同剂量。本文所说的**递归自我改进（recursive self-improvement, RSI）**，指这样一个循环：agent 自己的输出改变了跑下一轮的那个 agent，而且闭环的是验证器，不是人。

把这些版本分开的只有一个变量：**目标里有多少是交给 agent 的，多少要它自己补上。** 给它一条指令，它是 code agent。给它一个验证器，它能往上爬。只给它一个能力的名字，它得自己造测试床。什么都不给，它得自己决定测什么。本文沿着这架梯子一级一级往上走，每级挑一两个系统，讲到能看清循环里到底发生了什么的程度，然后问：这一级有机器做成过吗。

范围是后训练和它周边的东西：agent 脚手架、训练配方、数据管线。"一个目标加一个验证器"就是 RL 和 SFT 优化的对象，梯子是从这里画出来的。预训练不在范围内。有两个例子，AlphaEvolve 和 PaperBench，改的不是模型权重，放进来是因为同一个循环形状在那里也出现了。

## 四个阶，一个变量 {#zh-frame}

后训练里的任何目标都有三层，从下往上是：**用哪些题**、**什么算成功**、**测哪种能力**。SWE-bench Verified 把三层都写明了：这 500 道 GitHub issue；打了补丁之后仓库自己的测试通过；修 bug。三层之下还有第四样东西，它根本不属于目标：**搜索方法**，也就是 agent 用来把分数做上去的 prompt、工具、训练配方或搜索策略。

阶，就按 agent 自己补上了哪几样来定义。

| 阶 | 交给 agent 的 | agent 自己补的 | 谁闭环 |
|---|---|---|---|
| **零阶** | 一条指令 | 这道题的解 | 人，看结果 |
| **一阶** | 一个题集和一个验证器 | 更好的搜索方法 | 验证器 |
| **二阶** | 一个能力的名字 | 题集，然后做一阶 | 验证器，但得先找到它 |
| **三阶** | 什么都没有 | 能力、验证器、题集 | 还不存在 |

零阶是普通 code agent。RSI 从一阶开始，零阶到一阶这一步是最要紧的一步：这是验证器第一次替人站进循环里。之后每上一阶多接管目标的一层，所以文首的台阶图一阶、二阶各高一层，三阶高两层。

零阶和一阶的分界还要多说一句，因为一个 code agent 对着失败的测试反复改直到通过，也有验证器，也在迭代。区别在循环的产物。零阶的产物是这道题的解，测试只是验收，过了就结束，什么都不带走。一阶的产物是被改的那个系统本身，不管改的是 agent 的代码、训练脚本还是 kernel。指标是一个跨整个题集的量，这一轮改好的东西是下一轮的起点。产物是题解的循环，跑多少遍都是零阶；产物是"跑循环的那个东西的更好版本"的循环，才是一阶。

后面会反复用到两个词。**能力切面**指一句话能说清、能单独测的能力：修 bug、长上下文定位、多步工具调用。**验证器**指任何能自动判定成功的东西：测试集、墙钟、验证集 loss、隐藏测试集。

## 零阶：给指令，人验收 {#zh-order-0}

普通 code agent 拿到一段自然语言的任务，改文件，能跑的跑一下，交回一个 diff。整个设置里，除了看结果的人，没有任何东西说什么算好。它手边有检查手段的时候，比如一个失败的测试或者编译器，会用它把这道题做完，然后丢掉。Cursor、Claude Code、Codex、OpenHands 默认都是这个形状。

这不是缺陷，工具就是干这个的。它在这里只当对照组。零阶 agent 在一道题上学到的东西，随这道题一起消失。没有一个量在跨次运行中变好，所以没有任何递归可言，底下的模型再强也一样。谁只要加上一个持久的指标、让 agent 的改动留下来，同一个 agent 就变成了一阶系统。这不是假设，下面第一个 case 做的正是这件事。

## 一阶：给验证器，把分做上去 {#zh-order-1}

**定义。** 交给 agent 一个题集和一个验证器。它要做的是改自己的搜索方法，prompt、工具、代码、记忆、搜索策略、训练配方都行，让验证器的数字涨上去，涨了就留下。目标的三层都不动。具体说：把 SWE-bench Verified 和它的测试 harness 交给一个 agent，让它改自己的脚手架或训练脚本，重跑 harness，解决率涨了就留下这处改动，如此反复直到预算用完。那 500 道题从头到尾不变，变的是 agent。

**为什么难。** 最朴素的做法是拿榜单自己的题，每题蒸馏几十条轨迹，拿去微调。这刷不到满分，只能拿到轻微提分，有时还掉分：蒸馏来的轨迹和模型自己会走的路不一样，硬灌进去把原有的行为搅乱了。真正有效的是把任务拆开、一步一步教，或者 on-policy distillation，让学生自己走、老师只在它走到的地方给信号。一阶 agent 接管的就是这件事：不靠人拆子任务、不靠人调配方，自己找到往上走的路。

### AutoResearch

Andrej Karpathy 是 OpenAI 的创始成员、前特斯拉 AI 总监，一直在写刻意做小、做到能通读的训练代码：micrograd、nanoGPT、nanochat。**AutoResearch**（[Karpathy 2026](https://github.com/karpathy/autoresearch)）把一个 coding agent 指向单卡版的 nanochat，让它一夜之间把模型做得更好。agent 只改一个文件 `train.py`，训五分钟，读验证集的 bits-per-byte，降了就留下，没降就回退，一夜大约一百次实验；人写一份 markdown 交代指令。这里没有榜单，正因如此它是最干净的一阶例子：验证器是留出数据上的 loss，被改进的是训练配方，每晚的产物是一个更好的 `train.py`。

### RSIAgent

**RSIAgent**（[2026](https://arxiv.org/abs/2609.15364)）把循环放在测试时。做一道目标题之前，执行者（GLM-5.3）先在课程 agent 出的这道题的变体上练习，验证者（Kimi-K3）根据环境反馈给每次尝试打分，管用的东西写进记忆；之后记忆冻结，执行者带着它去做真正的题。

<figure class="post-figure paper">
  <img src="{{ base_path }}/images/blog/papers/rsiagent-fig2.png" alt="三栏：宽度递归自探索在五组 FreeCAD 练习上产出经验记忆；深度递归自探索对着目标题分三轮；测试时复用记忆导出 CAD 零件。" loading="lazy">
  <figcaption>RSIAgent 做一道 FreeCAD 题：先跨任务组练习，再对着目标分轮练，最后测试时复用冻结的记忆。（图源：<a href='https://arxiv.org/abs/2609.15364'>RSIAgent 2026</a>）</figcaption>
</figure>

在 OSWorld 2.0 上 harness 的 partial 分从 71.97 到 78.98，在 Agents' Last Exam 上从 83.75 到 84.82，partial 分超过了 GPT-6 Astra 的报告值，ALE 的 binary 分仍低于它，50.75 对 52.24。*为什么是一阶。* 题和打分器是给定的，记忆就是搜索方法，一题一份，练习题是脚手架，不进任何 benchmark。

### AlphaEvolve

**AlphaEvolve**（[Google DeepMind 2025](https://arxiv.org/abs/2506.13131)）把循环跑在一段程序上，而不是一个 agent 上。用户标出程序里允许进化的部分，交一个打分函数；Gemini 2.0 Flash 和 Pro 对着从程序库里采样出来的程序提出 diff，评估器打分，有希望的程序回到程序库，程序库的组织方式保证多样的谱系都能存活。

<figure class="post-figure paper">
  <img src="{{ base_path }}/images/blog/papers/alphaevolve-fig2.png" alt="AlphaEvolve 循环：用户提供的程序和评估代码进入程序库；提示采样器组 prompt，模型组提出 diff，评估器给新程序打分，再回到程序库。" loading="lazy">
  <figcaption>AlphaEvolve 的循环。用户只提供起始程序和打分函数。（图源：<a href='https://arxiv.org/abs/2506.13131'>Novikov et al. 2025</a>）</figcaption>
</figure>

它找到了用 48 次标量乘法完成两个 4×4 复矩阵相乘的办法，改进了 Strassen 1969 年的算法；一个让 Gemini 训练 kernel 平均提速 23%、训练时间减 1% 的启发式；还有一个回收 Google 全球机群 0.7% 算力的调度启发式。*为什么是一阶。* 这里不碰模型权重，但形状一样：目标固定，用户的评估器闭环，产物是一段更好的程序，成为下一轮的种子。

### 其他一阶系统，以及这一阶怎么测

**Darwin Gödel Machine**（[Zhang et al. 2025](https://arxiv.org/abs/2505.22954)）把循环用在 coding agent 自己的代码上：一个包着冻结 Claude 3.5 Sonnet 的仓库改自己，每个版本存进档案库，榜单分数决定下一个改哪个。80 轮之后 SWE-bench 从 20.0% 到 50.0%，Polyglot 从 14.2% 到 30.7%，自己长出了按行号看文件、字符串替换编辑这类功能，一次运行约两周、两万两千美元。它也给出了一阶最清楚的失败样本：一个 agent 在幻觉检查上拿满分，办法是删掉检查依赖的日志，作者的补救是把检查器对 agent 藏起来。

一阶能力有自己的 benchmark。**RE-Bench**（[Wijk et al. 2024](https://arxiv.org/abs/2411.15114)）给 agent 和人类专家七个 ML 研究工程环境，数值打分：两小时预算下最好的 agent 是人的四倍，八小时人略微领先，32 小时人是两倍。**MLE-bench**（[Chan et al. 2024](https://arxiv.org/abs/2410.07095)）用 75 场 Kaggle 比赛和奖牌线，最好的配置在 16.9% 的比赛里拿到铜牌以上。**RSI-Exam**（[2026](https://rsi-exam.ai/)）有 88 道专家写的研究任务，在隐藏数据上打分；GPT-6 Astra 0.51，Opus 5 0.46，0.6 是前沿校准的参考解。

**一阶做到了什么程度。** 今天 agent 能做完这一阶。起点弱的时候固定目标上涨幅可以很大，DGM 是例子；基座已经很强的时候增量就小，RSIAgent 是例子。验证器是墙钟或 loss 的地方，短预算下 agent 追平或超过人类专家。这一阶也是命名最乱的一阶：self-improving、self-evolving、RSI 都有人用来指它。

## 二阶：给目标，造测试床 {#zh-order-2}

**定义。** 一阶把目标的三层都留着没动。二阶是 agent 开始接管最底下那层：用哪些题。

拿 SWE-bench Verified 来说。它的定义，一个仓库、一个失败的测试、一个让测试通过的补丁，框住的是一个远大于它公开的 500 道题的集合。所有满足这个形状的真实 GitHub issue 都属于这个集合，不管有没有被收录；满足同一条规则的合成题也属于它。一阶始终只在那 500 道已收录的题上搜索方法。二阶往集合里添新成员：和目标同分布但不是目标里的题，每道压在一个能力切面上而不是压在 benchmark 上，不管有没有人拿去训练都能独立判分。造完之后，对着造出来的东西做一阶。具体说：只给 agent “修 bug” 三个字和 SWE-bench 的规则，它要做的是去 GitHub 挑仓库，造出带一个失败测试和一个能通过的补丁的 issue，然后才拿它们训练。

瓶颈是验证器。造一道新题容易，造一道能自动判对错的新题难。今天所有大规模跑着的二阶管线，都是因为先有人想清楚了验证器从哪来。所以下面两个系统是**人机协同做成的二阶**：人设计管线、选定验证器，agent 执行每一步。还没有 agent 独立做成过二阶，本节最后会讲离它最近的两个。

### SWE-smith

**SWE-smith**（[Yang et al. 2025](https://arxiv.org/abs/2504.21798)）把一个 Python 仓库变成几千道修 bug 的题，办法是把已有测试覆盖的代码弄坏。agent 把仓库装起来，人确认测试能跑；然后五种策略注入 bug，从让 LM 重写一个函数到把一个真实 pull request 倒回去；候选只有弄坏了一个原本通过的测试才成为题，再由 LM 根据 diff 和这个测试写 issue。

<figure class="post-figure paper">
  <img src="{{ base_path }}/images/blog/papers/swe-smith-fig9.png" alt="SWE-smith 总览三行：用 SWE-agent 和开发者把 GitHub 仓库变成执行环境；用四种策略合成任务实例；收集专家轨迹训练学生模型。" loading="lazy">
  <figcaption>SWE-smith 的三段：建执行环境，在里面合成任务实例，用在里面收集的轨迹训练。（图源：<a href='https://arxiv.org/abs/2504.21798'>Yang et al. 2025</a>）</figcaption>
</figure>

128 个仓库共产出 50,137 道题，花费约 1,360 美元；用其中 5,016 条 Claude 3.7 Sonnet 轨迹微调的 Qwen2.5-Coder-32B 在 SWE-bench Verified 上到 40.2%。*人机协同做成的二阶。* 每道题都是 SWE-bench 那个集合的新合法成员，验证器是从仓库自己的测试借来的。分工很清楚：仓库测试可以当验证器、弄坏通过的测试可以造题、需要五种策略，这些是作者定的；agent 负责装仓库、写 bug、写 issue。把 agent 拿掉管线就停，把人拿掉管线就不会开始。

### SWE-Universe

**SWE-Universe**（[Chen et al. 2026](https://arxiv.org/abs/2602.02361)）把同一个思路推到整个 GitHub。3,330 万个 pull request 过滤后剩约一百万个；对每一个，一个构建 agent 拿着 bash 和一个在仓库"有 bug"与"已修复"两种状态间切换的开关，把项目装起来，写一个评估脚本，脚本必须修复前失败、修复后通过，循环里嵌着的作弊检测器会拒绝伪造检查的脚本，比如用 grep 在源码里找字符串。

<figure class="post-figure paper">
  <img src="{{ base_path }}/images/blog/papers/swe-universe-fig2.png" alt="SWE-Universe 管线：构建 agent 提出评估脚本，循环内的作弊检测器检查它，迭代验证在有 bug 和已修复两种仓库状态下运行，直到一边失败一边通过。" loading="lazy">
  <figcaption>SWE-Universe 的构建器：agent 提出验证脚本，作弊检测器检查，在两种仓库状态下验证闭环。（图源：<a href='https://arxiv.org/abs/2602.02361'>Chen et al. 2026</a>）</figcaption>
</figure>

构建 agent 是用拒绝采样在自己成功且未作弊的运行上训出来的 Qwen-Next-80B-A3B，在论文的构建基准上以 78.4% 对 77.8% 胜过 Claude Opus 4.5。结果是 52,960 个仓库、八个语言组上的 807,693 个环境；用其中 50 万条轨迹做中期训练，Qwen3-Next 在 SWE-bench Verified 上从 50.3% 涨到 61% 以上，同一套做法把 Qwen3-Max-Thinking 带到 75.3%。*人机协同做成的二阶，人的份额在缩小。* agent 现在自己建环境、写验证脚本，另一个 agent 管反作弊。人还在给的是框架：PR 是源、先败后过是验证器、作弊长什么样。**agent 执行二阶管线，人仍在设计它。** 这句话就是 2026 年二阶的位置。

### 自己决定造什么的 agent

有两个系统把"造什么数据"的决定交给了 agent。**DataEnvGym**（[Khan et al. 2024](https://arxiv.org/abs/2410.06215)）设一个老师 agent，读学生模型按推断出的技能整理的错题，决定下一轮给哪些技能多造数据；生成引擎、技能结构和验证器是环境里定死的部件。**ANDES**（[2026](https://arxiv.org/abs/2606.01279)）让一个训练 agent 把下游 benchmark 拆成若干能力域，通过一棵自扩展的上下文树来路由合成，再根据合成报告调整下一轮。两者接管了造什么，都没有接管怎么造、怎么判，规模也比人写的管线低几个数量级。还没有一个 agent，拿到 SWE-bench Verified 之后，自己写出一份 SWE-smith。

### 验证器从哪来

每条二阶管线里的验证器，来源只有两种。一种是**借的**：验证器在管线之前就存在，是别人为了别的目的写的，比如 [SWE-bench](https://arxiv.org/abs/2310.06770)、[SWE-Gym](https://arxiv.org/abs/2412.21139)、[SWE-smith](https://arxiv.org/abs/2504.21798) 跑的仓库自带测试。另一种是**造的**：世上没有现成的，管线自己合成一个，比如 [EnvScaler](https://arxiv.org/abs/2601.05808) 互相校验的 sandbox，[Golden Goose](https://arxiv.org/abs/2601.22975) 把遮掉的推理步骤变成选择题，或者 [OMNI-EPIC](https://arxiv.org/abs/2405.15568) 给每个任务一起写出的成功函数。两组规模差一到两个数量级。光是修 bug 这一条借验证器的线，[SWE-Gym](https://arxiv.org/abs/2412.21139) 2,438 道，[R2E-Gym](https://arxiv.org/abs/2504.07164) 8,135 道，[SWE-rebench](https://arxiv.org/abs/2505.20411) 21,000 道，[daVinci-Env](https://arxiv.org/abs/2603.13023) 45,320 个环境，SWE-Universe 807,693 个；造验证器的管线停在几百到几千。竞赛题是人手写的，但是为办比赛写的，所以算借。这个区分看的是验证器是不是为了训 AI 之外的原因而存在。

## 三阶：没有目标，造一个 {#zh-order-3}

**定义。** 没人交来目标。三阶 agent 做的是世界上最好的 benchmark 作者做的事：系统性地调研现有 agent 系统在哪里失败、离真正的能力差多远，命名一个还没人测过的任务范式，再为它找到监督信号。判断 gap 的证据可以来自对能力本身的第一性原理思考、用户数据里的不满意信号、开发者论坛上的讨论、用户访谈的笔记。监督信号可以从已有的结构里挖，就像 SWE-bench 从 GitHub pull request 里挖出测试，也可以花钱买，让 agent 自己去雇领域专家出题、判题。这一层的每个任务都是一项原创研究，和所有科学突破一样：不是"多造几道修 bug 的题"，而是"发现修 bug 是一条轴，并且找到怎么判"。

**为什么难。** 二阶时，目标 benchmark 已经示范过什么算这类能力、拿什么判；agent 只需要沿同一分布放大。三阶没有示范。在二阶是瓶颈的验证器问题，到三阶成了全部问题：世上没有为"还没被命名的能力"预留的验证器，所以至今每次尝试都得自己造一个。

### OMNI-EPIC

**OMNI-EPIC**（[Faldor et al. 2024](https://arxiv.org/abs/2405.15568)）是对这个循环最完整的一次尝试。任务生成器（Claude 3 Opus）读一个记着已学会和失败任务的档案库，提出一个可学又有趣的新任务；环境生成器把它写成带奖励和单独成功检查的 Python；有趣度模型（GPT-4o）把和邻居比不新颖的丢掉；RL agent 在仿真器里训它，结果回到档案库。

<figure class="post-figure paper">
  <img src="{{ base_path }}/images/blog/papers/omni-epic-fig1.png" alt="OMNI-EPIC 循环：左边是仿真场景的任务档案库；任务生成器、环境生成器、生成后的有趣度模型、RL 训练和成功检测器连成一圈回到档案库。" loading="lazy">
  <figcaption>OMNI-EPIC 的循环。除了 RL 训练器，每个框都是语言模型。（图源：<a href='https://arxiv.org/abs/2405.15568'>Faldor et al. 2024</a>）</figcaption>
</figure>

200 轮之后任务从导航散到物体操作，跨过有移动段的彩虹桥、把球踢进移动的球门；带真实 RL 的短运行里 agent 学会 16 个任务、失败 6 个。*为什么是三阶，又为什么没有落地。* 没有任何具名能力交进去，系统自己命名任务、自己写成功检查。但成功函数出自写任务的同一个模型，"有趣"是另一次语言模型调用，两者都没有外部理由让人相信，它的任务也没有一个被别人当 benchmark 采用。

### 这条线上的其他人

这个想法有十几年了。**POET**（[Wang et al. 2019](https://arxiv.org/abs/1901.01753)）在一个参数化的环境族里让障碍赛道和双足行走者共同进化，奖励是前进距离。**AI-GAs**（[Clune 2019](https://arxiv.org/abs/1905.10985)）把自动生成学习环境列为通往通用 AI 的三根支柱之一，没有给方法。**AI Scientist**（[Lu et al. 2024](https://arxiv.org/abs/2408.06292)；[v2 2025](https://arxiv.org/abs/2504.08066)）自己提研究问题、做实验、写论文，语言模型当审稿人；v2 有一篇在 ICLR 2025 的一个 workshop 评审分过了录用线，随后作者撤稿。**PaperBench**（[Starace et al. 2025](https://arxiv.org/abs/2504.01848)）测的是反方向，对着 8,316 条 rubric 用语言模型判分，复现 20 篇 ICML 2024 论文；最好的 agent 21.0%，三篇子集上 ML 博士生 41.4%，o1 26.6%。每一个的验证器都是造的，每一个的验证器都是语言模型。

### 人做成过的三阶

两次人做成的三阶事件，能看出验证器这个决定做对了是什么样子。

**HumanEval**（[Chen et al. 2021](https://arxiv.org/abs/2107.03374)）是 164 道手写的编程题，每道带函数签名、docstring 和平均 7.7 个单元测试，作者手写是为了保证训练数据里不可能有它。五年后它还是 164 道。

**SWE-bench**（[Jimenez et al. 2023](https://arxiv.org/abs/2310.06770)）从另一个观察出发：一个改了测试文件的已合并 pull request，天然带着一个被这次修复变成通过的测试。作者从 12 个 Python 仓库抓了约 90,000 个 pull request，留下既解决了 issue 又碰了测试文件的已合并 PR，然后在修复前后各跑一遍测试，只留至少有一个 fail-to-pass 测试的实例。漏斗从 93,139 个 PR 到 11,407 个候选到 2,294 道题。当时最好的模型 Claude 2 解出 1.96%。一年之内 OpenAI 请 [93 位开发者筛了 1,699 道](https://openai.com/index/introducing-swe-bench-verified/)，做出 500 道的 Verified 子集；两年之内上面整个二阶一节都长在它上面。

两个验证器在质量上分不出高下：客观、秒级、可并行、低噪声。差别只有一处。一个是为这个 benchmark 写的，一个是不相识的人为了守自己的仓库早就写好的，远在有人想到拿它训练之前。这又是二阶里借与造的区分，它决定一次三阶事件会不会长成一个二阶产业。

**RSI-Exam** 展示了人做三阶作为一套可重复流程的样子：领域专家提出任务和指标，跑一个弱基线和一个更强的参考解来定标尺；开发者打包成两个隔离镜像，声明产出物合同，藏好隐藏数据；一位审核者过一套 90 多条的清单，覆盖价值、可测性、来源和每一条泄漏路径；另一位对着锚点分析一条完整的 agent 轨迹。88 道题都是这么造的，全部手工。

## 开放问题 {#zh-open}

这样排开，记录很短。agent 能做完一阶。二阶在大规模上是人设计、agent 执行的管线在做，人的份额逐年缩小。三阶尝试了十年，没有产出过任何被别人使用的 benchmark。前沿实验室说的 RSI，大部分是一阶的工程面，正在快速推进，加上三阶的研究面，还没人做成。阶数不是价值排序：一阶的 AlphaEvolve 打破了 56 年没动的上界并且在生产环境里跑着，二阶管线是开源模型在 SWE-bench Verified 上追上来的主要原因。

下一步的具体测试很好说，也没人跑过：把 SWE-bench Verified 交给一个 agent，别的什么都不给，看它能不能自己写出一份 SWE-smith，包括"仓库测试就是验证器"这个决定。这背后是本文一直绕回来的那个问题：机器能不能注意到"pull request 里有测试"这种事，还是说这个观察就是留给人的那部分。而失败模式在低一阶已经看得见：DGM 的 objective hacking 靠把检查器藏起来抓住了，等到 agent 自己写检查器的时候，这一招就没有了。

## 声明 {#zh-disclaimer}

> 本文由一个 AI coding agent（Claude Code）起草、对照所引论文核对事实并排版，与作者的协作方式正是上文的零阶：作者下指令、逐条验收，agent 的工作没有沉淀成一个更好的 agent。经过这个循环仍然留下的错误，责任在作者。

## 引用本文 {#zh-cite}

引用格式：

> Lyu, Zhiheng. (Sep 2026). "How Far Has RSI Gotten in Post-Training?". cogito233.github.io. https://cogito233.github.io/blog/three-orders-of-self-improvement/.

或

<div class="cite-box" markdown="1">
```bibtex
@article{lyu2026rsi,
  title   = "How Far Has RSI Gotten in Post-Training?",
  author  = "Lyu, Zhiheng",
  journal = "cogito233.github.io",
  year    = "2026",
  month   = "Sep",
  url     = "https://cogito233.github.io/blog/three-orders-of-self-improvement/"
}
```
</div>

## 参考文献 {#zh-references}

<div class="references" markdown="1">

1. A. Karpathy. ["AutoResearch."](https://github.com/karpathy/autoresearch) GitHub, 2026.
2. ["RSIAgent."](https://arxiv.org/abs/2609.15364) arXiv preprint arXiv:2609.15364 (2026).
3. A. Novikov et al. (Google DeepMind). ["AlphaEvolve: A coding agent for scientific and algorithmic discovery."](https://arxiv.org/abs/2506.13131) arXiv preprint arXiv:2506.13131 (2025). See also the [blog post](https://deepmind.google/discover/blog/alphaevolve-a-gemini-powered-coding-agent-for-designing-advanced-algorithms/).
4. J. Zhang, S. Hu, C. Lu, R. Lange, J. Clune. ["Darwin Gödel Machine: Open-Ended Evolution of Self-Improving Agents."](https://arxiv.org/abs/2505.22954) ICLR 2026.
5. H. Wijk et al. ["RE-Bench: Evaluating Frontier AI R&D Capabilities of Language Model Agents against Human Experts."](https://arxiv.org/abs/2411.15114) arXiv preprint arXiv:2411.15114 (2024).
6. J. S. Chan et al. ["MLE-bench: Evaluating Machine Learning Agents on Machine Learning Engineering."](https://arxiv.org/abs/2410.07095) arXiv preprint arXiv:2410.07095 (2024).
7. ["RSI-Exam: Benchmarking Recursive Self-Improvement through Executable Research."](https://rsi-exam.ai/) 2026.
8. J. Yang et al. ["SWE-smith: Scaling Data for Software Engineering Agents."](https://arxiv.org/abs/2504.21798) arXiv preprint arXiv:2504.21798 (2025).
9. M. Chen et al. (Qwen Team). ["SWE-Universe: Scale Real-World Verifiable Environments to Millions."](https://arxiv.org/abs/2602.02361) arXiv preprint arXiv:2602.02361 (2026).
10. Z. Khan et al. ["DataEnvGym: Data Generation Agents in Teacher Environments with Student Feedback."](https://arxiv.org/abs/2410.06215) ICLR 2025.
11. ["ANDES: Agent Native Data Evolving Synthesis Tool for Autonomous Instruction Alignment."](https://arxiv.org/abs/2606.01279) arXiv preprint arXiv:2606.01279 (2026).
12. ["EnvScaler: Scaling Tool-Interactive Environments for LLM Agents via Programmatic Synthesis."](https://arxiv.org/abs/2601.05808) Findings of ACL 2026.
13. ["Golden Goose: A Simple Trick to Synthesize Unlimited RLVR Tasks from Unverifiable Internet Text."](https://arxiv.org/abs/2601.22975) arXiv preprint arXiv:2601.22975 (2026).
14. J. Pan et al. ["Training Software Engineering Agents and Verifiers with SWE-Gym."](https://arxiv.org/abs/2412.21139) arXiv preprint arXiv:2412.21139 (2024).
15. N. Jain et al. ["R2E-Gym: Procedural Environments and Hybrid Verifiers for Scaling Open-Weights SWE Agents."](https://arxiv.org/abs/2504.07164) COLM 2025.
16. ["SWE-rebench: An Automated Pipeline for Task Collection and Decontaminated Evaluation of Software Engineering Agents."](https://arxiv.org/abs/2505.20411) NeurIPS 2025.
17. ["daVinci-Env."](https://arxiv.org/abs/2603.13023) arXiv preprint arXiv:2603.13023 (2026).
18. M. Faldor, J. Zhang, A. Cully, J. Clune. ["OMNI-EPIC: Open-endedness via Models of human Notions of Interestingness with Environments Programmed in Code."](https://arxiv.org/abs/2405.15568) ICLR 2025.
19. R. Wang et al. ["Paired Open-Ended Trailblazer (POET)."](https://arxiv.org/abs/1901.01753) arXiv preprint arXiv:1901.01753 (2019).
20. J. Clune. ["AI-GAs: AI-generating algorithms, an alternate paradigm for producing general artificial intelligence."](https://arxiv.org/abs/1905.10985) arXiv preprint arXiv:1905.10985 (2019).
21. C. Lu et al. ["The AI Scientist."](https://arxiv.org/abs/2408.06292) arXiv preprint arXiv:2408.06292 (2024); ["The AI Scientist-v2."](https://arxiv.org/abs/2504.08066) arXiv preprint arXiv:2504.08066 (2025).
22. G. Starace et al. ["PaperBench: Evaluating AI's Ability to Replicate AI Research."](https://arxiv.org/abs/2504.01848) arXiv preprint arXiv:2504.01848 (2025).
23. M. Chen et al. ["Evaluating Large Language Models Trained on Code"](https://arxiv.org/abs/2107.03374) (HumanEval). arXiv preprint arXiv:2107.03374 (2021).
24. C. E. Jimenez et al. ["SWE-bench: Can Language Models Resolve Real-World GitHub Issues?"](https://arxiv.org/abs/2310.06770) ICLR 2024.
25. OpenAI. ["Introducing SWE-bench Verified."](https://openai.com/index/introducing-swe-bench-verified/) Blog post, August 2024.
</div>

</div>

<script>
(function () {
  var KEY = 'blog-lang';
  function set(l) {
    document.querySelectorAll('.lang').forEach(function (d) { d.hidden = !d.classList.contains('lang-' + l); });
    document.querySelectorAll('.lang-btn').forEach(function (b) {
      var on = b.getAttribute('data-lang') === l;
      b.classList.toggle('is-active', on); b.setAttribute('aria-selected', on ? 'true' : 'false');
    });
    try { localStorage.setItem(KEY, l); } catch (e) {}
  }
  var l = null, h = decodeURIComponent(location.hash.replace('#', ''));
  if (h === 'zh' || h === '中文版' || h.indexOf('zh-') === 0) l = 'zh';
  else if (h === 'en') l = 'en';
  if (!l) { try { l = localStorage.getItem(KEY); } catch (e) {} }
  /* default is always English; no browser-language guessing.
     NOTE: keep block comments here. The compress layout collapses this script
     onto one line, so a // comment would swallow everything after it. */
  if (l !== 'en' && l !== 'zh') l = 'en';
  /* jump menu on the right: built from the visible language block's h2/h3 */
  var toc = document.createElement('nav'); toc.className = 'toc-right'; toc.setAttribute('aria-label', 'Jump to');
  var main = document.getElementById('main'); if (main) main.appendChild(toc);
  var links = [];
  function buildToc(lang) {
    var block = document.querySelector('.lang-' + lang); if (!block) return;
    var hs = block.querySelectorAll('h2, h3'); links = [];
    var html = '<div class="toc-right__title">' + (lang === 'zh' ? '目录' : 'Jump to') + '</div><ol>';
    hs.forEach(function (h) {
      if (!h.id) return;
      var t = h.textContent.replace(/\s+/g, ' ').trim();
      if (/^(Citation|References|引用本文|参考文献)$/.test(t)) return;
      html += '<li class="lvl' + h.tagName[1] + '"><a href="#' + h.id + '">' + t + '</a></li>';
    });
    toc.innerHTML = html + '</ol>';
    links = Array.prototype.slice.call(toc.querySelectorAll('a'));
    links.forEach(function (a) {
      a.addEventListener('click', function (e) {
        var el = document.getElementById(a.getAttribute('href').slice(1)); if (!el) return;
        e.preventDefault();
        window.scrollTo({ top: el.getBoundingClientRect().top + window.scrollY - 24, behavior: 'smooth' });
        if (history.replaceState) history.replaceState(null, '', '#' + el.id);
      });
    });
  }
  function mark() {
    var y = window.scrollY + 120, cur = null;
    links.forEach(function (a) { var el = document.getElementById(a.getAttribute('href').slice(1)); if (el && el.offsetTop <= y) cur = a; });
    links.forEach(function (a) { a.classList.toggle('is-current', a === cur); });
  }
  document.querySelectorAll('.contents-line a[href^="#"]').forEach(function (a) {
    a.addEventListener('click', function (e) {
      var el = document.getElementById(a.getAttribute('href').slice(1)); if (!el) return;
      e.preventDefault(); window.scrollTo({ top: el.getBoundingClientRect().top + window.scrollY - 24, behavior: 'smooth' });
    });
  });
  window.addEventListener('scroll', mark, { passive: true });
  set(l); buildToc(l); mark();
  document.querySelectorAll('.lang-btn').forEach(function (b) {
    b.addEventListener('click', function () { var nl = b.getAttribute('data-lang'); set(nl); buildToc(nl); window.scrollTo({ top: 0 }); mark(); });
  });
})();
</script>
