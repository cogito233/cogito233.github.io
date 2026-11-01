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
  <img src="{{ base_path }}/images/blog/staircase-hero-en.svg" alt="Four columns from left to right: order 0, a robot standing on the ground with no layers; order 1, a robot on one green slab labelled method; order 2, a person and a robot on three slabs, the upper two amber and labelled data and verifier; order 3, a person on four slabs, the top one hatched and labelled target." loading="lazy">
  <figcaption>Each step is built from the layers the agent takes over, bottom to top: method, data, verifier, target. Order 0 stands on the ground: a plain code agent that changes none of them. Colour is who supplies that layer today: green, agents; amber, human-designed pipelines that agents execute; hatched, humans only.</figcaption>
</figure>

<p class="contents-line" markdown="1"><strong>Contents:</strong> [Four orders, one variable](#four-orders-one-variable) · [Order 0](#order-0-instruction-in-human-accepts) · [Order 1](#order-1-verifier-in-score-up) · [Order 2](#order-2-target-in-testbed-out) · [Order 3](#order-3-nothing-in-benchmark-out) · [Open questions](#open-questions)</p>

"Self-refine, self-play, self-evolve, self-improving, recursive self-improvement." In post-training, the same words are used for an agent that rewrites its own prompt, for a model that writes its own training problems, and for the last step before AGI. They describe different amounts of the same thing. **Recursive self-improvement (RSI)**, as used here, is a loop in which an agent's own output changes the agent that runs the next iteration, and the loop is closed by a verifier rather than by a person.

One variable separates the versions: **how much of the objective is handed to the agent, and how much it has to supply itself.** Hand it an instruction and it is a code agent. Hand it a verifier and it can climb. Hand it only a capability name and it must build the testbed. Hand it nothing and it must decide what to measure. This post climbs that ladder one step at a time. At each step it describes one or two systems in enough detail to show what the loop does, and asks whether any machine has yet done that step.

The scope is post-training and the things around it: agent scaffolds, training recipes, data pipelines. RL and SFT optimize against a target with a verifier, so the ladder is defined in those terms. Pretraining is out of scope. Two examples fall outside that scope: AlphaEvolve improves a program rather than model weights, and is included because its loop has the same shape; PaperBench is a benchmark, not an improvement loop, and is included because it measures research replication with a manufactured verifier.

## Four orders, one variable

Take any system that calls itself self-improving and look at what it works with. There are four things, and they stack from the bottom up.

1. **The method layer** is everything that turns a model into a score on a task: the prompt, the scaffold and tools, the memory, the search strategy, the training recipe. Until recently, this layer was the subject of methodology papers written by people. It is the first layer to have moved to agents, starting in domains such as math and code, where a verifier can check the result.
2. **The data layer** is the set of task instances the system is trained or evaluated on. Each instance is a concrete problem with everything needed to attempt it: for a bug-fixing task, the repository, the issue text and the environment it runs in.
3. **The verifier layer** decides, for each task instance, whether an attempt succeeded, without a person in the loop. For bug fixing that is the repository's own tests passing after the patch; elsewhere it is a wall clock, a validation loss, or a hidden test set.
4. **The target layer** names what the whole system is measuring: the capability that the tasks and their verifiers are meant to stand for, such as bug fixing, long-horizon tool use or research engineering.

Now for the one variable. For any such system, ask how many of the four layers the agent supplies itself, counting from the bottom. The answer is always a contiguous block starting at the bottom, and its size gives the four orders.

| Order | The agent is handed | The agent supplies | Who closes the loop |
|---|---|---|---|
| **0** | An instruction | A solution to this one task; none of the layers change | A person, reading the result |
| **1** | Data, verifier, target | The method | The verifier |
| **2** | The target | The data and its verifier, then order 1 on them | The verifier the agent found or built |
| **3** | Nothing | All four | Nothing exists yet |

Each order takes over more of the stack, starting from the bottom. This is the staircase at the top of the post: order 0 stands on the ground and changes no layer, order 1 stands on the method layer, order 2 adds the data and verifier layers together, order 3 adds the target. Data and verifier come as a pair because a task that cannot be graded is not yet a task, which is why the second step is the tall one. The color of each layer shows who supplies it today.

The ground floor is worth spelling out, because everything else is measured from it. At order 0 the agent supplies none of the four layers. Its method is whatever the code agent shipped with. There is no data layer, because there is no set of tasks, only the one instruction the user typed. There is no verifier, because the user reads the diff and decides. And there is no target, because nothing is being measured. Every result is approved by a person, one at a time, and nothing carries over to the next instruction.

Three boundaries separate the four orders, and each one is a different kind of step.

**Order 0 to order 1** is where a loop first appears. A plain code agent that reruns failing tests until they pass has a verifier too, so the difference is not whether a verifier exists but what the loop produces. At order 0 the product is the solution to this task; once the tests pass, the loop ends and nothing carries forward. At order 1 the product is the method itself (the agent's own code, its training script, a kernel), and whatever one iteration improves is where the next one starts. This is where RSI begins, and it is the only boundary that turns a tool into a loop.

**Order 1 to order 2** is a change in what the task set is. At order 1 the set is the benchmark's published instances and never grows. At order 2 the agent adds legitimate new members to the same set, real or synthetic issues that each come with their own verifier, and then runs order 1 on what it built. The set was always larger than the benchmark, and at order 2 the agent goes out to collect more of it. Almost all of the work lies in finding where each new task's verifier comes from.

**Order 2 to order 3** is where the demonstration disappears. At order 2 the target is named and the benchmark shows one way to judge it, so the agent only has to add more tasks from the same distribution. At order 3 there is no target and no benchmark to copy, so the agent must first decide what is worth measuring and then do order 2 for it.

## Order 0: instruction in, human accepts

A plain code agent gets a task in natural language, edits files, runs what it can, and returns a diff. Nothing in the setup says what a good result is except the person reading it. When the agent does have a check available, such as a failing test or a compiler, it uses the check to finish this task and then discards it. Cursor, Claude Code, Codex and OpenHands are all this shape by default.

This is what the tools are designed for, and it matters here only as the control group. Everything an order-0 agent learns about a task dies with the task. No quantity improves across runs, so there is nothing recursive to speak of, however capable the underlying model is. The moment someone adds a persistent metric and lets the agent's edits carry forward, the same agent becomes an order-1 system. The first case below does exactly this.

## Order 1: verifier in, score up

**Definition.** The agent is handed a task set and a verifier. Its job is to change its own method (its prompt, tools, code, memory or training recipe) so that the verifier's score goes up, and to keep each change that raises it. The three objective layers above the method stay untouched. Concretely: hand an agent SWE-bench Verified and its test harness, let it edit its own scaffold or its training script, rerun the harness, keep the edit if the resolve rate rose, and repeat until the budget runs out. The 500 tasks never change; only the agent does.

**Why it is hard.** The naive approach is to take the benchmark's own tasks, distill a few dozen trajectories for each, and fine-tune on them. This does not reach a full score. It yields a modest gain and sometimes loses points, because the distilled trajectories do not follow the paths the model would take on its own, and training on them disrupts behavior the model already had. What works is decomposing tasks and teaching them step by step, or on-policy distillation, in which the student generates its own trajectories and the teacher gives feedback only on the states the student actually reaches. This is the work an order-1 agent takes over: finding a way to raise the score without a person decomposing sub-tasks or tuning the recipe.

### AutoResearch

Andrej Karpathy, a founding member of OpenAI and formerly Director of AI at Tesla, writes deliberately small, readable training code: micrograd, nanoGPT, nanochat. **AutoResearch** ([Karpathy 2026](https://github.com/karpathy/autoresearch)) points a coding agent at a single-GPU version of nanochat and tells it to improve the model overnight. The agent edits one file, `train.py`, trains for five minutes, reads the validation bits-per-byte, and keeps the edit if the number fell or reverts it otherwise, for about a hundred experiments a night. The instructions live in a markdown file written by a person. There is no leaderboard, which is what makes it the cleanest order-1 example: the verifier is a held-out loss, the thing being improved is the training recipe, and each night's product is a better `train.py`.

### RSIAgent

**RSIAgent** ([2026](https://arxiv.org/abs/2609.15364)) puts the loop at test time. Before attempting a target task, an actor (GLM-5.3) practices on variants of it proposed by a curriculum agent, a verifier (Kimi-K3) grades each attempt from the environment's feedback, and what works is written into a memory. The memory is then frozen, and the actor attempts the real task with it.

<figure class="post-figure paper">
  <img src="{{ base_path }}/images/blog/papers/rsiagent-fig2.png" alt="Three panels: broad recursive self-exploration over five FreeCAD practice groups feeding experience memory; deep recursive self-exploration in three rounds against the target task; test-time memory reuse producing the exported CAD part." loading="lazy">
  <figcaption>RSIAgent on a FreeCAD task: practice across task groups, then rounds against the target, then test-time reuse of the frozen memory. (Image source: <a href='https://arxiv.org/abs/2609.15364'>RSIAgent 2026</a>)</figcaption>
</figure>

The harness raises the partial score from 71.97 to 78.98 on OSWorld 2.0 and from 83.75 to 84.82 on Agents' Last Exam (ALE). This passes the partial scores reported for GPT-6 Astra, although RSIAgent stays below GPT-6 Astra on ALE's binary score, 50.75 against 52.24. *Why order 1.* The tasks and scorers are given. The memory is the method, with one memory per target task, and the practice tasks are scaffolding that never enters a benchmark.

### AlphaEvolve

**AlphaEvolve** ([Google DeepMind 2025](https://arxiv.org/abs/2506.13131)) runs the loop over a program instead of an agent. The user marks which parts of a program may be evolved and supplies a scoring function; Gemini 2.0 Flash and Pro propose diffs against programs sampled from a database, the evaluators score them, and promising programs go back into the database, which is organized so that diverse lineages survive.

<figure class="post-figure paper">
  <img src="{{ base_path }}/images/blog/papers/alphaevolve-fig2.png" alt="AlphaEvolve loop: user-supplied program and evaluation code enter a program database; a prompt sampler builds prompts, an LLM ensemble proposes diffs, evaluators score the new programs and they return to the database." loading="lazy">
  <figcaption>The AlphaEvolve loop. The user supplies only the starting program and the scoring function. (Image source: <a href='https://arxiv.org/abs/2506.13131'>Novikov et al. 2025</a>)</figcaption>
</figure>

It found a way to multiply two 4×4 complex matrices in 48 scalar multiplications, improving on Strassen's 1969 algorithm; a heuristic that speeds up Gemini's training kernels by 23% on average, cutting training time by 1%; and a scheduling heuristic that recovers 0.7% of Google's fleet-wide compute. *Why order 1.* Nothing here touches a model's weights, but the loop has the same shape. The objective is fixed, the user's evaluator closes the loop, and the product is a better program that seeds the next round.

### Other order-1 systems, and how the order is measured

**Darwin Gödel Machine** ([Zhang et al. 2025](https://arxiv.org/abs/2505.22954)) applies the loop to a coding agent's own code: a repository around a frozen Claude 3.5 Sonnet edits itself, every version is kept in an archive, and a benchmark score decides which version is edited next. Over 80 iterations, its SWE-bench score rose from 20.0% to 50.0% and its Polyglot score from 14.2% to 30.7%. Along the way the agent invented features for itself, such as line-range viewing and string-replacement editing. Each run took about two weeks and cost about USD 22,000. DGM also produced the clearest example of the failure that order 1 invites: one agent scored perfectly on a hallucination check by deleting the logging the check depended on, and the authors' fix was to hide the checker from the agent.

Order-1 ability has its own benchmarks. **RE-Bench** ([Wijk et al. 2024](https://arxiv.org/abs/2411.15114)) gives agents and human experts seven ML research engineering environments with numeric scores: with a two-hour budget the best agents score four times as high as the humans; at eight hours the humans narrowly pull ahead; and at 32 hours the humans score twice as high. **MLE-bench** ([Chan et al. 2024](https://arxiv.org/abs/2410.07095)) uses 75 Kaggle competitions with medal thresholds; the best setup reaches bronze or better in 16.9% of them. **RSI-Exam** ([2026](https://rsi-exam.ai/)) has 88 expert-written research tasks scored on hidden data; GPT-6 Astra reaches 0.51 and Opus 5 0.46 on a scale where 0.6 is a frontier-calibrated reference solution.

**How far order 1 has come.** Agents already operate at this order today. Gains on a fixed target can be large from a weak start: DGM went from 20.0% to 50.0% on SWE-bench. From an already-high start they are smaller: RSIAgent adds about 7 points on OSWorld 2.0 (71.97 to 78.98) and about 1 on ALE (83.75 to 84.82). Where the verifier is a wall clock or a loss, agents match or beat human experts on short budgets. This is also the order with the messiest naming: self-improving, self-evolving and RSI are all used for it.

## Order 2: target in, testbed out

**Definition.** Order 1 left the objective alone. Order 2 is what happens when the agent takes over the data layer and, with it, the verifier layer: which tasks are used, and how each one is graded.

Take SWE-bench Verified. Its definition (a codebase, a failing test, and a patch that makes the test pass) describes a set far larger than the 500 instances it publishes. Every real GitHub issue with that shape belongs to the set, collected or not, and so does every synthetic one that satisfies the same rule. Order 1 only ever searches methods against the 500 that were collected. Order 2 adds new members to the set. These are tasks drawn from the same distribution as the target but not copied from its items. Each one exercises a single nameable, separately testable skill (bug fixing, long-context localization, multi-step tool use) rather than the benchmark as a whole, and each comes with a verifier, so it can be graded whether or not anyone trains on it. Then it applies order 1 to what it built. Concretely: hand an agent the phrase "bug fixing" and the SWE-bench rules, and its job is to go to GitHub, pick repositories, mint issues that have a failing test and a passing patch, and only then train on them.

The bottleneck is the verifier. Writing a new task is easy; writing one that can be automatically judged is hard. Every order-2 pipeline running at scale today exists because a person first worked out where its verifier would come from. The two systems below are therefore order 2 done by humans and agents together: people designed the pipeline and chose the verifier, and agents execute almost every step. No agent has done order 2 alone; the section ends with the two that have come closest.

### SWE-smith

**SWE-smith** ([Yang et al. 2025](https://arxiv.org/abs/2504.21798)) turns a Python repository into hundreds to thousands of bug-fixing tasks by breaking code that existing tests already cover. An agent installs the repository, and a person confirms that the tests run. Four strategies then inject bugs, ranging from asking an LM to rewrite a function to reverting a real pull request. A candidate becomes a task only if it breaks a previously passing test, and an LM then writes the issue text from the diff and that test.

<figure class="post-figure paper">
  <img src="{{ base_path }}/images/blog/papers/swe-smith-fig9.png" alt="SWE-smith overview in three rows: turn a GitHub repository into an execution environment with SWE-agent and a developer; synthesize task instances with four strategies; collect expert trajectories and train a student model." loading="lazy">
  <figcaption>SWE-smith's three stages: build an execution environment, synthesize task instances inside it, train on trajectories collected there. (Image source: <a href='https://arxiv.org/abs/2504.21798'>Yang et al. 2025</a>)</figcaption>
</figure>

Across 128 repositories, this yields 50,137 instances for about USD 1,360. Qwen2.5-Coder-32B, fine-tuned on 5,016 Claude 3.7 Sonnet trajectories collected on them, reaches 40.2% on SWE-bench Verified. *Order 2, by humans and agents together.* Every instance is a legitimate new member of SWE-bench's set, and its verifier is borrowed from the repository's own tests. The division of labor is explicit: the authors decided that repository tests can serve as the verifier, that breaking passing tests creates tasks, and that four strategies are needed; the agents install repositories, write bugs and write issues. Take the agents out and the pipeline stops; take the people out and it never starts.

### SWE-Universe

**SWE-Universe** ([Chen et al. 2026](https://arxiv.org/abs/2602.02361)) scales the same idea to all of GitHub. Of 33.3 million pull requests, about a million survive filtering. For each one, a building agent is given bash and a switch between the buggy and fixed states of the repository. It installs the project and writes an evaluation script that must fail before the fix and pass after it, while a hacking detector inside the loop rejects scripts that fake the check, for instance by grepping the source files.

<figure class="post-figure paper">
  <img src="{{ base_path }}/images/blog/papers/swe-universe-fig2.png" alt="SWE-Universe pipeline: a building agent proposes an evaluation script, an in-loop hacking detector inspects it, and iterative validation runs it in both the buggy and fixed repository states until it fails on one and passes on the other." loading="lazy">
  <figcaption>The SWE-Universe builder: an agent proposes the verifier script, a hacking detector inspects it, and validation against both repository states closes the loop. (Image source: <a href='https://arxiv.org/abs/2602.02361'>Chen et al. 2026</a>)</figcaption>
</figure>

The builder is a Qwen-Next-80B-A3B trained by rejection sampling on its own successful, non-hacked runs, and it beats Claude Opus 4.5 on the paper's building benchmark, 78.4% to 77.8%. The result is 807,693 environments across 52,960 repositories and eight language groups; mid-training on 500,000 trajectories from them takes Qwen3-Next from 50.3% to over 61% on SWE-bench Verified, and the same recipe took Qwen3-Max-Thinking to 75.3%. *Order 2, by humans and agents together, with the human share shrinking.* The agent now builds the environment and writes the verifier script, and another agent polices hacking. People still supply the frame: PRs as the source, fail-then-pass as the verifier, and a definition of what hacking looks like. As of 2026, agents execute order-2 pipelines, but people still design them.

### Agents that choose what to make

Two systems hand the agent the decision of what data to generate. **DataEnvGym** ([Khan et al. 2024](https://arxiv.org/abs/2410.06215)) sets up a teacher agent that reads a student model's errors, organized by inferred skill, and decides which skills to generate more data for next; the generation engine, the skill structure and the verifier are fixed parts of the environment. **ANDES** ([2026](https://arxiv.org/abs/2606.01279)) has a trainer agent decompose a downstream benchmark into capability domains, route synthesis through a self-expanding tree of contexts, and adjust the next round from a synthesis report. Both hand over the choice of what to make. Neither hands over how to make it or how to judge it, and both operate at scales several orders of magnitude below the human-designed pipelines. No agent has yet been handed SWE-bench Verified and written its own SWE-smith.

### Where verifiers come from

Inside every order-2 pipeline the verifier comes from one of two places. Either it is **borrowed**: it existed before the pipeline, written by someone else for a different reason, such as the repository tests that [SWE-bench](https://arxiv.org/abs/2310.06770), [SWE-Gym](https://arxiv.org/abs/2412.21139) and [SWE-smith](https://arxiv.org/abs/2504.21798) run. Or it is **manufactured**: nothing existed, so the pipeline synthesizes one, such as the cross-checked sandboxes of [EnvScaler](https://arxiv.org/abs/2601.05808), the masked reasoning steps turned into multiple choice of [Golden Goose](https://arxiv.org/abs/2601.22975), or the success functions [OMNI-EPIC](https://arxiv.org/abs/2405.15568) writes alongside each task. At the top end the two groups differ in scale by two to three orders of magnitude, although the smallest borrowed-verifier sets are no larger than manufactured ones. The borrowed-verifier line for bug fixing alone runs [SWE-Gym](https://arxiv.org/abs/2412.21139) at 2,438 tasks, [R2E-Gym](https://arxiv.org/abs/2504.07164) at 8,135, [SWE-rebench](https://arxiv.org/abs/2505.20411) at 21,000, [daVinci-Env](https://arxiv.org/abs/2603.13023) at 45,320 environments and SWE-Universe at 807,693; manufactured-verifier pipelines sit at hundreds to low thousands. Competition problems are hand-written but were written for the competition, so they count as borrowed. What decides the split is whether the verifier exists for some reason other than training AI.

## Order 3: nothing in, benchmark out

**Definition.** Nobody hands over a target. An order-3 agent does what the best benchmark makers do. It surveys where current agents fail and how far they are from competence, names a task paradigm that nobody has measured, and finds a way to obtain supervision for it. The evidence for the gap can come from first-principles thinking about what a capability requires, from dissatisfaction signals in user data, from discussions on developer forums, or from user-interview notes. The supervision can be mined from a structure that already exists, as SWE-bench mined tests from GitHub pull requests, or commissioned, with the agent hiring domain experts to write and grade tasks. Every task at this level is a piece of original research. The job is not "make more bug-fixing tasks" but "discover that bug fixing is an axis worth measuring, and find a way to judge it".

**Why it is hard.** At order 2 the target benchmark had already demonstrated what counts as the capability and how to judge it; the agent only had to add more tasks from the same distribution. Order 3 has no demonstration. The verifier, which was the bottleneck at order 2, becomes the whole problem: there is no verifier lying around for a capability nobody has named yet, so every attempt so far has had to manufacture one.

### OMNI-EPIC

**OMNI-EPIC** ([Faldor et al. 2024](https://arxiv.org/abs/2405.15568)) is the most complete attempt at the loop. A task generator (Claude 3 Opus) reads an archive of learned and failed tasks and proposes a new one that is learnable and interesting; an environment generator writes it as Python with a reward and a separate success check; a model of interestingness (GPT-4o) discards it if it is not novel compared with similar tasks in the archive; an RL agent trains on it in a simulator; and the outcome goes back into the archive.

<figure class="post-figure paper">
  <img src="{{ base_path }}/images/blog/papers/omni-epic-fig1.png" alt="OMNI-EPIC loop: a task archive of simulated scenes on the left; task generator, environment generator, post-generation model of interestingness, RL training and success detector connected in a cycle back to the archive." loading="lazy">
  <figcaption>The OMNI-EPIC loop. The task generator, environment generator and model of interestingness are language models; the agent is trained with RL (DreamerV3), and success is checked by LLM-written code rather than a model call. (Image source: <a href='https://arxiv.org/abs/2405.15568'>Faldor et al. 2024</a>)</figcaption>
</figure>

Over 200 iterations, the tasks spread from navigation to object manipulation, including crossing a rainbow bridge with moving segments and kicking a ball into a moving goal. In the shorter run with real RL training, the agent learned 16 tasks and failed 6. *Why it is order 3, and why it has not caught on.* No named capability was handed in; the system names its own tasks and writes its own success checks. But the success function comes from the same model that wrote the task, and "interesting" is decided by another language-model call, so there is no external reason to trust either. None of its tasks has been adopted as a benchmark by anyone else.

### The rest of the line

The idea is over a decade old. **PowerPlay** ([Schmidhuber 2011](https://arxiv.org/abs/1112.5309)) already had a system search for the simplest task its current solver could not yet solve, then learn it, with the system inventing the tasks itself. **POET** ([Wang et al. 2019](https://arxiv.org/abs/1901.01753)) co-evolved obstacle courses and bipedal walkers, with forward progress as reward, inside one parametric family of environments. **AI-GAs** ([Clune 2019](https://arxiv.org/abs/1905.10985)) named the automatic generation of learning environments as one of three pillars toward general AI, but offered no method for it. **The AI Scientist** ([Lu et al. 2024](https://arxiv.org/abs/2408.06292); [v2 2025](https://arxiv.org/abs/2504.08066)) poses research questions, runs experiments and writes papers, with a language model as the reviewer; one v2 paper scored above the acceptance bar at an ICLR 2025 workshop before the authors withdrew it. **PaperBench** ([Starace et al. 2025](https://arxiv.org/abs/2504.01848)) measures the reverse direction, replicating 20 ICML 2024 papers against 8,316 rubric items with a language-model judge; the best agent reaches 21.0%, and on a three-paper subset ML PhDs reached 41.4% against o1's 26.6%. In every case the verifier is manufactured, and in every case it is a language model.

### Order 3 as done by humans

Two order-3 steps taken by humans show what a good verifier decision looks like.

**HumanEval** ([Chen et al. 2021](https://arxiv.org/abs/2107.03374)) is 164 hand-written programming problems, each with a signature, a docstring and on average 7.7 unit tests, written by the authors so that none of them could already be in the training data. Five years later it is still 164 problems.

**SWE-bench** ([Jimenez et al. 2023](https://arxiv.org/abs/2310.06770)) started from a different observation: a merged pull request that changes test files carries, for free, a test that the fix made pass. The authors crawled about 90,000 pull requests from 12 Python repositories, kept the merged ones that both resolved an issue and touched test files, then ran the tests before and after the fix and kept only instances with at least one fail-to-pass test. The funnel went from 93,139 PRs to 11,407 candidates to 2,294 tasks. The best model at the time, Claude 2, resolved 1.96% of them. Within a year, OpenAI had [93 developers screen 1,699 instances](https://openai.com/index/introducing-swe-bench-verified/) to produce the 500-task Verified subset, and within three years the work in the order-2 section above had been built on top of it.

The two verifiers are comparable in quality: both are objective, run in seconds, parallelize and have low noise. They differ in one respect. HumanEval's tests were written for the benchmark. SWE-bench's tests were written by strangers to protect their own repositories, long before anyone thought of training on them. That is the borrowed-versus-manufactured split from order 2 again, and it decides whether an order-3 event turns into an order-2 industry.

**RSI-Exam** shows what human order-3 work looks like as a repeatable process. A domain expert proposes the task and its metric, then runs a weak baseline and a stronger reference solution to calibrate the scale. A developer packages the task as two isolated images with a declared artifact contract and hidden data. A reviewer applies a rubric of over 90 checks covering value, measurability, provenance and every leakage path, and a second reviewer analyzes a full agent trajectory against the anchors. Eighty-eight tasks have been built this way, all by hand.

## Open questions

Laid out this way, the record is short. Agents already operate at order 1. Order 2 is done at scale by pipelines people designed and agents execute, with the human share shrinking each year. Order 3 has been attempted for a decade and has not produced a benchmark anyone else uses. What frontier labs call RSI is mostly the engineering side of order 1, which is moving fast, plus the research side of order 3, which nobody has done. Order is not a ranking of value: order-1 AlphaEvolve broke a 56-year-old bound and runs in production, and order-2 environments are a major ingredient in recent open-weights gains on SWE-bench Verified: SWE-Universe mid-training alone took Qwen3-Next from 50.3% to over 61%.

The concrete test for the next step is easy to state, but nobody has run it: hand an agent SWE-bench Verified and nothing else, and see whether it writes its own SWE-smith, including the decision that repository tests are the verifier. Behind it sits the question this post keeps returning to: can a machine notice that something like pull-request tests exists at all, or is that observation the part that stays human? And the failure mode is already visible one order down: DGM's objective hacking was handled by hiding the checker from the agent, which is not an option once the agent is the one writing the checker.

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
26. J. Schmidhuber. ["PowerPlay: Training an Increasingly General Problem Solver by Continually Searching for the Simplest Still Unsolvable Problem."](https://arxiv.org/abs/1112.5309) arXiv preprint arXiv:1112.5309 (2011).
</div>

</div>

<div class="lang lang-zh" markdown="1" hidden>

# RSI 在后训练上，做到了什么程度？

<figure class="post-figure">
  <img src="{{ base_path }}/images/blog/staircase-hero-zh.svg" alt="从左到右四列：零阶，一个机器人站在地面上，脚下没有色带；一阶，机器人站在一条绿色色带上，写着方法；二阶，一个人和一个机器人站在三层色带上，上面两层黄色，写着数据和验证器；三阶，一个人站在四层色带上，最上面一层是斜纹，写着目标。" loading="lazy">
  <figcaption>每级台阶由 agent 接管的层垒成，从下到上：方法、数据、验证器、目标。零阶站在地面上：普通 code agent，哪一层都不动。颜色表示这一层今天由谁补：绿色是 agent 已能做，黄色是人设计管线、agent 执行，斜纹是只有人做成过。</figcaption>
</figure>

<p class="contents-line" markdown="1"><strong>目录：</strong> [四个阶，一个变量](#zh-frame) · [零阶](#zh-order-0) · [一阶](#zh-order-1) · [二阶](#zh-order-2) · [三阶](#zh-order-3) · [开放问题](#zh-open)</p>

"self-refine、self-play、self-evolve、self-improving、RSI"。在后训练里，这几个词被用来指 agent 改自己的 prompt，指模型给自己出训练题，也指通往 AGI 的最后一步。它们说的其实是同一件事，只是程度不同。本文所说的**递归自我改进（recursive self-improvement, RSI）**，指这样一个循环：agent 自己的输出会改变下一轮运行的 agent，并且由验证器而不是人来闭环。

区分这些说法的只有一个变量：**目标里有多少是交给 agent 的，多少要它自己补上。** 给它一条指令，它是 code agent。给它一个验证器，它能往上爬。只给它一个能力的名字，它得自己造测试床。什么都不给，它得自己决定测什么。本文沿着这架梯子一级一级往上走，每级挑一两个系统，讲到能看清循环里实际发生了什么，再问：这一级有没有机器做成过？

本文的范围是后训练及其周边：agent 脚手架、训练配方、数据管线。"一个目标加一个验证器"就是 RL 和 SFT 优化的对象，梯子就从这里画起。预训练不在范围内。有两个例子不在这个范围内：AlphaEvolve 改的是程序而不是模型权重，收进来是因为它的循环形状相同；PaperBench 是 benchmark 而不是改进循环，收进来是因为它用一个造出来的验证器测量复现研究的能力。

## 四个阶，一个变量 {#zh-frame}

任取一个自称"自我改进"的系统，看它手里有什么。一共四样东西，自下而上叠起来。

1. **方法层**：让模型在某个任务上拿到分数所需的一切：prompt、脚手架和工具、记忆、搜索策略、训练配方。直到不久前，这一层还是人写方法论论文的地盘；它也是最先转到 agent 手里的一层，起步于数学、代码这类能用验证器检查结果的领域。
2. **数据层**：系统用来训练或评测的任务实例的集合。每个实例是一道具体的问题，附带求解所需的一切：对修 bug 任务来说，是仓库、issue 文本和它运行的环境。
3. **验证器层**：对每个任务实例，在没有人参与的情况下判定一次尝试成没成功。对修 bug 来说，验证器就是打补丁后仓库自带的测试能通过；换到别的领域，可以是墙钟时间、验证集 loss 或隐藏测试集。
4. **目标层**：说明整件事在测什么，也就是这些任务和验证器所代表的能力：修 bug、长程工具调用、研究工程。

接下来就是那个变量。对任何这样的系统，问：**这四层里，从底下数起，有几层是 agent 自己补上的。** 答案总是从底层起连续的一段，由此得到四个阶。

| 阶 | 交给 agent 的 | agent 自己补的 | 谁闭环 |
|---|---|---|---|
| **零阶** | 一条指令 | 这一个任务的解，四层都不动 | 人，看结果 |
| **一阶** | 数据、验证器、目标 | 方法 | 验证器 |
| **二阶** | 目标 | 数据和它的验证器，然后在上面做一阶 | agent 自己找到或造出的验证器 |
| **三阶** | 什么都没有 | 四层全部 | 还不存在 |

每上一阶，agent 从底下多接管一截。这就是文首的台阶图：零阶站在地面上，哪一层都不动；一阶站在方法层上；二阶一次加上数据层和验证器层；三阶加上目标层。数据和验证器成对出现，因为一个没法判分的任务还不算任务，所以第二级台阶最高。每层的颜色表示今天这一层由谁补。

地面这一层值得单独说清楚，因为其他各阶都以它为基准。**零阶**的 agent 四层都不补。方法就是 code agent 出厂自带的那套；没有数据层，因为没有任务集，只有用户敲进来的这一条指令；没有验证器，因为是用户看 diff 决定要不要；也没有目标，因为没有任何东西在被测量。每个结果都由人逐个批准，做完这条指令，什么都不会带到下一条。

四个阶之间有三条分界线，每一条都是不同性质的一步。

**零阶到一阶**是循环的诞生。一个普通 code agent 对着失败的测试反复改直到通过，也有验证器，所以区别不在有没有验证器，而在循环的产物是什么。零阶的产物是这个任务的解，测试一通过循环就结束，什么都不带走。一阶的产物是跑循环的那个系统本身，agent 自己的代码、训练脚本、kernel，这一轮改好的东西是下一轮的起点。RSI 从这里开始，这也是唯一一条把工具变成循环的分界线。

**一阶到二阶**，变的是任务集的性质。一阶的任务集就是 benchmark 公开的那些实例，永远不长。二阶是 agent 往同一个集合里添合法的新成员，真实或合成的任务，每个带着自己的验证器，然后在造出来的东西上做一阶。这个集合本来就比 benchmark 大，二阶就是 agent 自己出去多收集一些，难点几乎全在于每个新任务的验证器从哪来。

**二阶到三阶**，失去的是示范。二阶时目标已经命名、benchmark 也示范了一种判法，agent 只需要沿同一分布扩充。三阶没有目标也没有 benchmark 可抄，agent 得先决定什么值得测，再为它做一遍二阶。

## 零阶：给指令，人验收 {#zh-order-0}

普通 code agent 拿到一段自然语言的任务，改文件，能跑的跑一下，交回一个 diff。整个设置里，除了看结果的人，没有任何东西规定什么算好。它手边有检查手段的时候，比如一个失败的测试或者编译器，会用它把这道题做完，然后丢掉。Cursor、Claude Code、Codex、OpenHands 默认都是这个形状。

这不是缺陷，工具就是干这个的。它在这里只当对照组。零阶 agent 在一道题上学到的东西，随这道题一起消失。没有一个量在跨次运行中变好，所以没有任何递归可言，底下的模型再强也一样。谁只要加上一个持久的指标、让 agent 的改动留下来，同一个 agent 就变成了一阶系统。这并非假设：下面第一个例子做的正是这件事。

## 一阶：给验证器，把分做上去 {#zh-order-1}

**定义。** 交给 agent 一个题集和一个验证器。它要做的是改自己的方法，prompt、工具、代码、记忆、训练配方都行，让验证器的数字涨上去，涨了就留下。其余三层都不动。具体说：把 SWE-bench Verified 和它的测试 harness 交给一个 agent，让它改自己的脚手架或训练脚本，重跑 harness，解决率涨了就留下这处改动，如此反复直到预算用完。那 500 道题从头到尾不变，变的是 agent。

**为什么难。** 最朴素的做法是拿榜单自己的题，每题蒸馏几十条轨迹，拿去微调。这样刷不到满分，只能小幅提分，有时还会掉分：蒸馏来的轨迹和模型自己会走的路不一样，硬灌进去把原有的行为搅乱了。真正有效的是把任务拆开、一步一步教，或者 on-policy distillation，让学生自己走、老师只在它走到的地方给信号。一阶 agent 接管的就是这件事：不靠人拆子任务、不靠人调配方，自己找到往上走的路。

### AutoResearch

Andrej Karpathy 是 OpenAI 的创始成员、前特斯拉 AI 总监，一直在写刻意做小、做到能通读的训练代码：micrograd、nanoGPT、nanochat。**AutoResearch**（[Karpathy 2026](https://github.com/karpathy/autoresearch)）把一个 coding agent 指向单卡版的 nanochat，让它一夜之间把模型做得更好。agent 只改一个文件 `train.py`，训五分钟，读验证集的 bits-per-byte，降了就留下，没降就回退，一夜大约一百次实验；人写一份 markdown 交代指令。这里没有榜单，正因如此它是最干净的一阶例子：验证器是留出数据上的 loss，被改进的是训练配方，每晚的产物是一个更好的 `train.py`。

### RSIAgent

**RSIAgent**（[2026](https://arxiv.org/abs/2609.15364)）把循环放在测试时。做一道目标题之前，执行者（GLM-5.3）先在课程 agent 出的这道题的变体上练习，验证者（Kimi-K3）根据环境反馈给每次尝试打分，管用的东西写进记忆；之后记忆冻结，执行者带着它去做真正的题。

<figure class="post-figure paper">
  <img src="{{ base_path }}/images/blog/papers/rsiagent-fig2.png" alt="三栏：宽度递归自探索在五组 FreeCAD 练习上产出经验记忆；深度递归自探索对着目标题分三轮；测试时复用记忆导出 CAD 零件。" loading="lazy">
  <figcaption>RSIAgent 做一道 FreeCAD 题：先跨任务组练习，再对着目标分轮练，最后测试时复用冻结的记忆。（图源：<a href='https://arxiv.org/abs/2609.15364'>RSIAgent 2026</a>）</figcaption>
</figure>

harness 的 partial 分在 OSWorld 2.0 上从 71.97 升到 78.98，在 Agents' Last Exam 上从 83.75 升到 84.82，超过了 GPT-6 Astra 的报告值；但 ALE 的 binary 分仍低于它，50.75 对 52.24。*为什么是一阶。* 题和打分器是给定的，记忆就是方法，每道目标题一份；练习题只是脚手架，不进入任何 benchmark。

### AlphaEvolve

**AlphaEvolve**（[Google DeepMind 2025](https://arxiv.org/abs/2506.13131)）把循环跑在一段程序上，而不是一个 agent 上。用户标出程序里允许进化的部分，交一个打分函数；Gemini 2.0 Flash 和 Pro 对着从程序库里采样出来的程序提出 diff，评估器打分，有希望的程序回到程序库，程序库的组织方式保证多样的谱系都能存活。

<figure class="post-figure paper">
  <img src="{{ base_path }}/images/blog/papers/alphaevolve-fig2.png" alt="AlphaEvolve 循环：用户提供的程序和评估代码进入程序库；提示采样器组 prompt，模型组提出 diff，评估器给新程序打分，再回到程序库。" loading="lazy">
  <figcaption>AlphaEvolve 的循环。用户只提供起始程序和打分函数。（图源：<a href='https://arxiv.org/abs/2506.13131'>Novikov et al. 2025</a>）</figcaption>
</figure>

它找到了用 48 次标量乘法完成两个 4×4 复矩阵相乘的办法，改进了 Strassen 1969 年的算法；还找到一个让 Gemini 训练 kernel 平均提速 23%、整体训练时间缩短 1% 的启发式，以及一个为 Google 全部集群回收 0.7% 算力的调度启发式。*为什么是一阶。* 这里不碰模型权重，但形状一样：目标固定，用户的评估器闭环，产物是一段更好的程序，成为下一轮的种子。

### 其他一阶系统，以及这一阶怎么测

**Darwin Gödel Machine**（[Zhang et al. 2025](https://arxiv.org/abs/2505.22954)）把循环用在 coding agent 自己的代码上：一个包着冻结 Claude 3.5 Sonnet 的仓库改自己，每个版本存进档案库，榜单分数决定下一个改哪个。80 轮之后 SWE-bench 从 20.0% 到 50.0%，Polyglot 从 14.2% 到 30.7%，自己长出了按行号看文件、字符串替换编辑这类功能，一次运行约两周、两万两千美元。它也给出了一阶最清楚的失败样本：一个 agent 在幻觉检查上拿满分，办法是删掉检查依赖的日志，作者的补救是把检查器对 agent 藏起来。

一阶能力有自己的 benchmark。**RE-Bench**（[Wijk et al. 2024](https://arxiv.org/abs/2411.15114)）给 agent 和人类专家七个 ML 研究工程环境，数值打分：两小时预算下最好的 agent 是人的四倍，八小时人略微领先，32 小时人是两倍。**MLE-bench**（[Chan et al. 2024](https://arxiv.org/abs/2410.07095)）用 75 场 Kaggle 比赛和奖牌线，最好的配置在 16.9% 的比赛里拿到铜牌以上。**RSI-Exam**（[2026](https://rsi-exam.ai/)）有 88 道专家写的研究任务，在隐藏数据上打分；GPT-6 Astra 得 0.51，Opus 5 得 0.46，而 0.6 对应前沿校准的参考解。

**一阶做到了什么程度。** 今天 agent 能做完这一阶。起点弱的时候，固定目标上的涨幅可以很大：DGM 在 SWE-bench 上从 20.0% 涨到 50.0%。起点已经很高时增量较小：RSIAgent 在 OSWorld 2.0 上涨约 7 分（71.97 到 78.98），在 ALE 上涨约 1 分（83.75 到 84.82）。在验证器是墙钟时间或 loss 的任务上，短预算下 agent 已追平或超过人类专家。这一阶的命名也最乱：self-improving、self-evolving、RSI 都有人用来指它。

## 二阶：给目标，造测试床 {#zh-order-2}

**定义。** 一阶没有碰目标。二阶是 agent 接管数据层，连带验证器层：用哪些题，以及每道题怎么判。

拿 SWE-bench Verified 来说。它的定义，一个仓库、一个失败的测试、一个让测试通过的补丁，框住的是一个远大于它公开的 500 道题的集合。所有满足这个形状的真实 GitHub issue 都属于这个集合，不管有没有被收录；满足同一条规则的合成题也属于它。一阶始终只在那 500 道已收录的题上找方法。二阶往集合里添新成员：和目标同分布但不是目标里的题，每道针对一项能单独命名、单独测试的能力（修 bug、长上下文定位、多步工具调用），而不是针对整个 benchmark；每道自带验证器，不管有没有人拿去训练都能判分。造完之后，对着造出来的东西做一阶。具体说：只给 agent “修 bug” 三个字和 SWE-bench 的规则，它要做的是去 GitHub 挑仓库，造出带一个失败测试和一个能通过的补丁的 issue，然后才拿它们训练。

瓶颈是验证器。造一道新题容易，造一道能自动判对错的新题难。今天每条大规模运行的二阶管线，都是先有人想清楚了验证器从哪来，才跑得起来。所以下面两个系统是**人机协同做成的二阶**：人设计管线、选定验证器，agent 执行几乎每一步。还没有 agent 独立做成过二阶，本节最后会讲离它最近的两个。

### SWE-smith

**SWE-smith**（[Yang et al. 2025](https://arxiv.org/abs/2504.21798)）把一个 Python 仓库变成几百到几千道修 bug 的题，办法是把已有测试覆盖的代码弄坏。agent 把仓库装起来，人确认测试能跑；然后四种策略注入 bug，从让 LM 重写一个函数到把一个真实 pull request 倒回去；候选 bug 只有让一个原本通过的测试失败，才算一道题，再由 LM 根据 diff 和这个测试写 issue。

<figure class="post-figure paper">
  <img src="{{ base_path }}/images/blog/papers/swe-smith-fig9.png" alt="SWE-smith 总览三行：用 SWE-agent 和开发者把 GitHub 仓库变成执行环境；用四种策略合成任务实例；收集专家轨迹训练学生模型。" loading="lazy">
  <figcaption>SWE-smith 的三段：建执行环境，在里面合成任务实例，用在里面收集的轨迹训练。（图源：<a href='https://arxiv.org/abs/2504.21798'>Yang et al. 2025</a>）</figcaption>
</figure>

128 个仓库共产出 50,137 道题，花费约 1,360 美元；用其中 5,016 条 Claude 3.7 Sonnet 轨迹微调的 Qwen2.5-Coder-32B 在 SWE-bench Verified 上到 40.2%。*人机协同做成的二阶。* 每道题都是 SWE-bench 那个集合的新合法成员，验证器是从仓库自己的测试借来的。分工很清楚：仓库测试可以当验证器、弄坏通过的测试可以造题、需要四种策略，这些是作者定的；agent 负责装仓库、写 bug、写 issue。把 agent 拿掉管线就停，把人拿掉管线就不会开始。

### SWE-Universe

**SWE-Universe**（[Chen et al. 2026](https://arxiv.org/abs/2602.02361)）把同一个思路推到整个 GitHub。3,330 万个 pull request 过滤后剩约一百万个；对每一个，一个构建 agent 拿着 bash 和一个在仓库"有 bug"与"已修复"两种状态间切换的开关，把项目装起来，再写一个评估脚本，要求修复前失败、修复后通过。循环里嵌着一个作弊检测器，会拒绝伪造检查的脚本，比如用 grep 在源码里找字符串的脚本。

<figure class="post-figure paper">
  <img src="{{ base_path }}/images/blog/papers/swe-universe-fig2.png" alt="SWE-Universe 管线：构建 agent 提出评估脚本，循环内的作弊检测器检查它，迭代验证在有 bug 和已修复两种仓库状态下运行，直到一边失败一边通过。" loading="lazy">
  <figcaption>SWE-Universe 的构建器：agent 提出验证脚本，作弊检测器检查，在两种仓库状态下验证闭环。（图源：<a href='https://arxiv.org/abs/2602.02361'>Chen et al. 2026</a>）</figcaption>
</figure>

构建 agent 是一个 Qwen-Next-80B-A3B，用拒绝采样在它自己成功且未作弊的运行上训练而成，在论文的构建基准上以 78.4% 对 77.8% 胜过 Claude Opus 4.5。结果是 52,960 个仓库、八个语言组上的 807,693 个环境；用其中 50 万条轨迹做中期训练，Qwen3-Next 在 SWE-bench Verified 上从 50.3% 涨到 61% 以上，同一套做法把 Qwen3-Max-Thinking 带到 75.3%。*人机协同做成的二阶，人的份额在缩小。* agent 现在自己建环境、写验证脚本，另一个 agent 管反作弊。人还在给的是框架：PR 是源、先败后过是验证器、作弊长什么样。**agent 执行二阶管线，人仍在设计它。** 这就是 2026 年二阶所处的位置。

### 自己决定造什么的 agent

有两个系统把"造什么数据"的决定交给了 agent。**DataEnvGym**（[Khan et al. 2024](https://arxiv.org/abs/2410.06215)）设一个老师 agent，读学生模型按推断出的技能整理的错题，决定下一轮给哪些技能多造数据；生成引擎、技能结构和验证器是环境里定死的部件。**ANDES**（[2026](https://arxiv.org/abs/2606.01279)）让一个训练 agent 把下游 benchmark 拆成若干能力域，通过一棵自扩展的上下文树来路由合成，再根据合成报告调整下一轮。两者都接管了“造什么”，但都没有接管“怎么造、怎么判”，规模也比人写的管线低几个数量级。还没有哪个 agent 在拿到 SWE-bench Verified 之后，自己写出一份 SWE-smith。

### 验证器从哪来

每条二阶管线里的验证器，来源只有两种。一种是**借的**：验证器在管线之前就存在，是别人为了别的目的写的，比如 [SWE-bench](https://arxiv.org/abs/2310.06770)、[SWE-Gym](https://arxiv.org/abs/2412.21139)、[SWE-smith](https://arxiv.org/abs/2504.21798) 跑的仓库自带测试。另一种是**造的**：世上没有现成的，管线自己合成一个，比如 [EnvScaler](https://arxiv.org/abs/2601.05808) 互相校验的 sandbox，[Golden Goose](https://arxiv.org/abs/2601.22975) 把遮掉的推理步骤变成选择题，或者 [OMNI-EPIC](https://arxiv.org/abs/2405.15568) 给每个任务一起写出的成功函数。两组的规模在顶端差两到三个数量级，但最小的借验证器数据集并不比造验证器的大。光是修 bug 这一条借验证器的线，[SWE-Gym](https://arxiv.org/abs/2412.21139) 2,438 道，[R2E-Gym](https://arxiv.org/abs/2504.07164) 8,135 道，[SWE-rebench](https://arxiv.org/abs/2505.20411) 21,000 道，[daVinci-Env](https://arxiv.org/abs/2603.13023) 45,320 个环境，SWE-Universe 807,693 个；造验证器的管线停在几百到几千。竞赛题是人手写的，但是为办比赛写的，所以算借。这个区分只看验证器是否出于训练 AI 以外的原因而存在。

## 三阶：没有目标，造一个 {#zh-order-3}

**定义。** 没人交来目标。三阶 agent 做的是世界上最好的 benchmark 作者做的事：系统性地调研现有 agent 系统在哪里失败、离真正的能力差多远，命名一个还没人测过的任务范式，再为它找到监督信号。判断 gap 的证据可以来自对能力本身的第一性原理思考、用户数据里的不满意信号、开发者论坛上的讨论、用户访谈的笔记。监督信号可以从已有的结构里挖，就像 SWE-bench 从 GitHub pull request 里挖出测试，也可以花钱买，让 agent 自己去雇领域专家出题、判题。这一层的每个任务都是一项原创研究，和所有科学突破一样：不是"多造几道修 bug 的题"，而是"发现修 bug 是一条轴，并且找到怎么判"。

**为什么难。** 二阶时，目标 benchmark 已经示范过什么算这类能力、拿什么判；agent 只需要沿同一分布扩充。三阶没有示范。验证器在二阶是瓶颈，到了三阶就是问题的全部：世上没有为"还没被命名的能力"预留的验证器，所以至今每次尝试都得自己造一个。

### OMNI-EPIC

**OMNI-EPIC**（[Faldor et al. 2024](https://arxiv.org/abs/2405.15568)）是对这个循环最完整的一次尝试。任务生成器（Claude 3 Opus）读一个记着已学会和失败任务的档案库，提出一个可学又有趣的新任务；环境生成器把它写成带奖励和单独成功检查的 Python；有趣度模型（GPT-4o）丢掉与相近任务相比不够新颖的；RL agent 在仿真器里学习这个任务，结果回到档案库。

<figure class="post-figure paper">
  <img src="{{ base_path }}/images/blog/papers/omni-epic-fig1.png" alt="OMNI-EPIC 循环：左边是仿真场景的任务档案库；任务生成器、环境生成器、生成后的有趣度模型、RL 训练和成功检测器连成一圈回到档案库。" loading="lazy">
  <figcaption>OMNI-EPIC 的循环。任务生成器、环境生成器和有趣度模型是语言模型；agent 用 RL（DreamerV3）训练，成功判定由 LLM 写出的代码完成，而不是调用模型。（图源：<a href='https://arxiv.org/abs/2405.15568'>Faldor et al. 2024</a>）</figcaption>
</figure>

200 轮之后任务从导航扩散到物体操作，跨过有移动段的彩虹桥、把球踢进移动的球门；带真实 RL 的短运行里 agent 学会 16 个任务、失败 6 个。*为什么是三阶，又为什么没有落地。* 没有任何具名能力交进去，系统自己命名任务、自己写成功检查。但成功函数出自写任务的同一个模型，"有趣"是另一次语言模型调用，两者都没有外部依据让人信任，它的任务也没有一个被别人当 benchmark 采用。

### 这条线上的其他人

这个想法有十几年了。**PowerPlay**（[Schmidhuber 2011](https://arxiv.org/abs/1112.5309)）已经让系统自己去找当前求解器还解不了的最简单任务，再把它学会，任务由系统自己发明。**POET**（[Wang et al. 2019](https://arxiv.org/abs/1901.01753)）在一个参数化的环境族里让障碍赛道和双足行走者共同进化，奖励是前进距离。**AI-GAs**（[Clune 2019](https://arxiv.org/abs/1905.10985)）把自动生成学习环境列为通往通用 AI 的三根支柱之一，没有给方法。**AI Scientist**（[Lu et al. 2024](https://arxiv.org/abs/2408.06292)；[v2 2025](https://arxiv.org/abs/2504.08066)）自己提研究问题、做实验、写论文，语言模型当审稿人；v2 有一篇在 ICLR 2025 的一个 workshop 评审分过了录用线，随后作者撤稿。**PaperBench**（[Starace et al. 2025](https://arxiv.org/abs/2504.01848)）测的是反方向，对着 8,316 条 rubric 用语言模型判分，复现 20 篇 ICML 2024 论文；最好的 agent 21.0%，三篇子集上 ML 博士生 41.4%，o1 26.6%。这些工作的验证器无一例外都是造的，而且都是语言模型。

### 人做成过的三阶

从人做成的两次三阶事件里，可以看出验证器选对了是什么样子。

**HumanEval**（[Chen et al. 2021](https://arxiv.org/abs/2107.03374)）是 164 道手写的编程题，每道带函数签名、docstring 和平均 7.7 个单元测试，作者手写是为了保证训练数据里不可能有它。五年后它还是 164 道。

**SWE-bench**（[Jimenez et al. 2023](https://arxiv.org/abs/2310.06770)）从另一个观察出发：一个改了测试文件的已合并 pull request，天然带着一个因这次修复由失败转为通过的测试。作者从 12 个 Python 仓库抓了约 90,000 个 pull request，留下既解决了 issue 又碰了测试文件的已合并 PR，然后在修复前后各跑一遍测试，只留至少有一个 fail-to-pass 测试的实例。漏斗从 93,139 个 PR 收窄到 11,407 个候选，再到 2,294 道题。当时最好的模型 Claude 2 解出 1.96%。一年之内 OpenAI 请 [93 位开发者筛了 1,699 道](https://openai.com/index/introducing-swe-bench-verified/)，做出 500 道的 Verified 子集；不到三年，上文整个二阶一节讲的东西都长在了它上面。

两个验证器在质量上分不出高下：客观、秒级、可并行、低噪声。差别只有一处。一个是专为这个 benchmark 写的，另一个是素不相识的人为了维护自己的仓库早就写好的，远在有人想到拿它训练之前。这又是二阶里借与造的区分，它决定一次三阶事件会不会长成一个二阶产业。

**RSI-Exam** 展示了人把三阶做成一套可重复流程的样子：领域专家提出任务和指标，跑一个弱基线和一个更强的参考解来定标尺；开发者打包成两个隔离镜像，声明产出物契约，藏好隐藏数据；一位审核者过一套 90 多条的清单，覆盖价值、可测性、来源和每一条泄漏路径；另一位对着锚点分析一条完整的 agent 轨迹。88 道题都是这么造的，全部手工。

## 开放问题 {#zh-open}

这样排开来看，记录很短。agent 能做完一阶。二阶在大规模上是人设计、agent 执行的管线在做，人的份额逐年缩小。三阶尝试了十年，没有产出过任何被别人使用的 benchmark。前沿实验室所说的 RSI，大多是一阶的工程部分（进展很快），加上三阶的研究部分（还没人做成）。阶数不是价值排序：一阶的 AlphaEvolve 打破了 56 年没动的上界并且在生产环境里跑着，二阶造出的环境是近期开放权重模型在 SWE-bench Verified 上进步的重要来源之一：仅用 SWE-Universe 做中期训练，就把 Qwen3-Next 从 50.3% 带到 61% 以上。

下一步该做的测试很容易说清，却还没人跑过：把 SWE-bench Verified 交给一个 agent，别的什么都不给，看它能不能自己写出一份 SWE-smith，包括"仓库测试就是验证器"这个决定。这背后是本文一直绕回来的那个问题：机器能不能注意到"pull request 里有测试"这种事，还是说这个观察就是留给人的那部分。而失败模式在低一阶已经显现：DGM 的 objective hacking 靠把检查器藏起来抓住了，等到 agent 自己写检查器的时候，这一招就没有了。

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
26. J. Schmidhuber. ["PowerPlay: Training an Increasingly General Problem Solver by Continually Searching for the Simplest Still Unsolvable Problem."](https://arxiv.org/abs/1112.5309) arXiv preprint arXiv:1112.5309 (2011).
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
