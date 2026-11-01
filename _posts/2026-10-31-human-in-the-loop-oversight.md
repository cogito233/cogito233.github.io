---
title: "Toward Human-in-the-Loop Oversight for Long-Horizon Agent Projects"
date: 2026-10-31
permalink: /blog/human-in-the-loop-oversight/
excerpt: "The cost of overseeing many agents depends on how many breaks in continuity a human has to reconcile, not on how many agents run. Treat agents as procedures and tasks as objects: keep each task's history in the repo, keep the human's view to one screen, and let agents attach, act and exit."
tags:
  - agents
  - human-in-the-loop
  - research workflow
classes:
  - wide
  - bilingual
---

{% include base_path %}

<style>
.post-figure img { max-width: 560px; width: 100%; margin-left: auto; margin-right: auto; display: block; }
.state-block pre { padding: 1em 1.2em !important; }
.state-block pre code { font-size: 0.72rem !important; line-height: 1.55; }
.post-repo-link { margin: -0.4em 0 1.2em; font-size: 0.95rem; }
.post-repo-link a { font-weight: 600; }
.install-cmd { margin: 1.2em 0 !important; }
.install-cmd pre { padding: 1em 1.2em !important; white-space: pre-wrap; word-break: break-word; }
.install-cmd pre code { font-size: 0.85rem !important; line-height: 1.6; }
.page__content table { display: table; width: auto; max-width: 100%; font-size: 0.82rem; margin: 1.2em 0; }
.page__content th, .page__content td { padding: 0.45em 0.8em; }
</style>

<div class="lang-switch" role="tablist" aria-label="Language">
  <span class="lang-switch__label">Language / 语言</span>
  <button type="button" class="lang-btn is-active" role="tab" data-lang="en" aria-selected="true">English</button>
  <button type="button" class="lang-btn" role="tab" data-lang="zh" aria-selected="false">中文</button>
</div>

<div class="lang lang-en" markdown="1">

# Toward Human-in-the-Loop Oversight for Long-Horizon Agent Projects

<p class="post-repo-link"><a href="https://github.com/cogito233/swarm-oversight-harness"><i class="fab fa-github" aria-hidden="true"></i> cogito233/swarm-oversight-harness</a></p>

<figure class="post-figure">
  <img src="{{ base_path }}/images/blog/oversight-layers.svg" alt="Three tiers. Top, you: one screen with charter, budget and threads, and two actions, approve a task and accept a result. Middle, a planner and task files in the repo, four task cards each with TASK, LOG and RESULT. Bottom, many short-lived agents, some running and some exited, grouped under the tasks they work on. A two-way arrow links you and the middle layer; many thin arrows link the middle layer and the agents; there is no direct link from you to the agents." loading="lazy">
  <figcaption>How to read it: you look at one screen and make two kinds of decision. The middle layer turns those decisions into work for many short-lived agents, and turns their output back into that one screen. You do not watch the agents directly.</figcaption>
</figure>

<p class="contents-line" markdown="1"><strong>Contents:</strong> [What the human sees](#what-the-human-sees) · [Why not watch the agents](#why-not-watch-the-agents) · [The middle layer](#the-middle-layer) · [Making acceptance mean something](#making-acceptance-mean-something) · [Related work and limits](#related-work-and-limits) · [Try it](#try-it)</p>

Multi-agent harnesses are maturing. Runtimes now keep many agents busy in parallel [1], and agent teams share persistent memory and build on each other's work [5, 7]. As these systems improve, the scarce resource shifts from compute to the attention of the person who oversees them. I ran into this about four months ago in my own research projects and have been working around it since. I assumed this was common knowledge, but after this week's announcements I am less sure, so I am writing down what I do.

Running ten agents at once is easy. Overseeing them on one project for three months is not. When I did this on research projects, the bottleneck after a few weeks was knowing what was true in my own project, more than compute or model quality.

The claim of this post is that **the cost of overseeing a project depends on how many breaks in continuity the human has to reconcile, not on how many agents are running.** The design goal follows from it: keep the project's history continuous, and keep it in the place that is cheapest for a human to read. The analogy that helped me is the move from procedural to object-oriented programming. An agent session is a procedure call. It has a stack (its context window), does some work, returns a value, and its local state is gone. Most agent setups are procedural in this sense, because the state of the work lives in whichever agent is running. The middle layer makes the task the object instead: it has an identity, state that persists between calls, invariants, and an interface. Agents become methods that act on it, one after another or in parallel, and none of them owns it.

The rest of the post describes that layer in practice: what I look at, what I decide, and what the layer does for me. I keep three things in my head: the charter, the budget and the list of threads. I act at two points: approving what a task is for, and accepting what it produced. Everything else lives in task files in the repo. A planner agent maintains those files, and worker agents attach to a task, do their part, and exit. I am releasing the method as [Swarm Oversight Harness](https://github.com/cogito233/swarm-oversight-harness).

## 1. What the human sees {#what-the-human-sees}

My model of a project fits on one screen and has three parts:

1. **Charter**: what we are trying to find out, and the hypothesis whose outcome would change our plans.
2. **Budget**: how much compute and money each thread may spend before it has to come back and ask.
3. **Threads**: what each line of work is doing, what stage it has *verifiably* reached, and what it is waiting for.

An illustrative example (not a real project):

```
CHARTER  Can a 7B model learn to check its own patches with repo tests?
         H1: training on fail→pass test pairs adds ≥10 points of
             self-check accuracy on held-out repos.
         Kill if the 200-task pilot gains < 3 points.
BUDGET   2,000 GPU-h + $1,500 API in total. Over a thread's cap → ask.

STATE    critical path: T2 pilot result
  T1 build    fail→pass pipeline    verified, CI #41    40/100 GPU-h   → accept gate
  T2 run      SFT pilot, 200 tasks  launched, no agent  310/400 GPU-h  → done hook
  T3 review   red-team T1 leakage   running             $90/$200
  T4 read     related-work map      draft               $15/$50
```
{: .state-block}

I make two kinds of decision from this screen:

- **Approve a task** that the planner has drafted: what it is for, what counts as done, and how much it may spend.
- **Accept a result**: once accepted, the result is no longer a draft, and other work can build on it.

Everything below this screen is written for agents. I open a task's files only when I need them for one of these two decisions. The budget is part of what a task is authorized to do, so an agent that would go over its thread's cap stops and asks instead of retrying.

## 2. Why not watch the agents directly {#why-not-watch-the-agents}

**Attention does not scale.** DeepSeek's Super-agent design makes the same point: today's agents cap single-task performance at model capability and multi-task performance at human attention [1]. In my runs the ceiling was three to five sessions.

**Projects lose continuity.** How hard a project is to understand depends less on its size than on its continuity: one name for each thing, one version of each fact, and a record that reads in order. Every break makes the reader notice a discrepancy, decide which side is right, and remember the decision. Months of procedural agent work, where each session carries the state and then disappears, produce such breaks without ever raising an error. These are real incidents from my projects, lightly anonymized:

| Break | What happened |
|---|---|
| Time | A retry passed and the earlier failure disappeared from the log. |
| Names | "Arms A–D launched." Two weeks later nobody knew what arm C was. |
| Meaning | "Done" covered six different facts, from *edited* to *accepted*. |
| Meaning | Missing judge verdicts were filled in as "fail": a cell read −0.388; the true value was about +0.07. |
| Versions | Two docs described the same plan differently; agents followed different ones. |
| Authority | One agent's "next step: scale up" was read by the next agent as an instruction. |

Each break was small, but together they meant I had to re-read the project every morning. Watching more agents would only have added more breaks, because each new session is one more place where state can live and then disappear.

## 3. The middle layer {#the-middle-layer}

### Agents are procedures; tasks are objects

A single agent is easy to follow because it has one linear history that you can read in order. That history is lost when the call returns, that is, when the context window ends. A hand-built pipeline is worse, because its history is spread across stages and no single file says what happened in order.

The middle layer keeps a linear history but stores it in the task instead of the agent. The programming analogy maps directly:

| Programming | Agent project |
|---|---|
| procedure call | one agent session |
| call stack, local variables | the context window |
| return value | the summary an agent hands back |
| object identity | the task id (`T2`) |
| object state | `TASK.md`, the `LOG`, the `RESULT`s |
| invariants | acceptance criteria, authorization, budget |
| methods | agents that attach, act and exit |
| public interface | the task's row in `STATE` |
{: .analogy-table}

The analogy has one important limit. An ordinary object overwrites its fields, but a task never rewrites its LOG, so it is closer to an object whose state is rebuilt from an event log. That is what keeps the history readable in order. Concretely, a task is a small, durable object in the repo:

- `TASK.md`: goal, files it may and must not touch, the authorization and budget it runs under, acceptance criteria with how each is checked, stop conditions;
- a `LOG` that is only ever appended to, with a snapshot of the current state at the top;
- `RESULT` files with evidence;
- one row in `STATE`, which is the screen in section 1.

<figure class="post-figure">
  <img src="{{ base_path }}/images/blog/task-timeline.svg" alt="A bar labelled task T2 runs left to right and accumulates TASK.md, an append-only LOG and a RESULT. Above it, agent A launches an experiment and exits; a gap where the experiment runs with no agent alive and no polling, ending in a completion hook; agent B picks up, analyzes and writes the result; verifier C checks it. Below the bar: at the start the human approves the TASK.md the planner drafted; at the end the human accepts the result after the verifier has checked it." loading="lazy">
  <figcaption>How to read it: the bar is one task and it never stops; the boxes above are agents and they come and go. While the experiment runs, no agent is alive. The human touches the task only at the start and the end.</figcaption>
</figure>

One habit follows: an agent that launches a long experiment exits instead of polling. Before it exits, it appends a handoff to the task's LOG: what was launched, where the outputs will appear, and how to check them. A completion hook or marker file signals the end of the run, and a new agent picks up the task. Polling would spend context on waiting, and the only record of what is pending would sit in a context that will be compacted.

### The planner

A planner agent maintains the layer. Each round it re-reads the repo and fixes STATE wherever it disagrees with reality. It then names one critical path, splits work into tasks that touch different files, dispatches workers, has their results verified, and consolidates everything into one STATE update. I never merge N results by hand; if I find myself doing it, the layer is broken. One rule decides whether the planner keeps going or comes back to me: **retry HOW problems, stop on WHAT problems.** A failing test is a HOW problem and is retried within budget. A plan that contradicts reality, or a budget about to be exceeded, is a WHAT problem and is escalated to me.

### Handing a task to an agent through files

The default way to delegate is to write the task into a subagent's prompt and read back its summary. Over weeks, this breaks down. The task exists only in the parent agent's context. The result is only the worker's account. The parent has to merge N summaries. Failed attempts leave no record. In the middle layer the prompt is only a pointer, *"do threads/T2/tasks/TASK-03.md"*, and the result comes back as a file.

| | Prompt subagent | Ticket runtime [1] | Task files |
|---|---|---|---|
| Task lives in | parent's context | runtime database | `TASK.md` |
| Comes back as | summary | thread reply | `RESULT.md` + evidence |
| Survives restart | no | yes | yes |
| Who can resume | that parent | runtime executors | any agent |
| Authorization | the prompt | enforced in code | written down, not enforced |
| Failed attempts | lost | ticket history | LOG, old RESULTs |

### Which memory to trust and which to clean up

Moving memory into files only helps if the files stay trustworthy; otherwise the breaks in section 2 move into the repo. Not everything in the repo is equally valid, so each piece of memory is assigned to one of four kinds:

| Kind | Examples | Valid as | Kept by |
|---|---|---|---|
| Current | `STATE`, `CHARTER`, open `TASK.md` | what is true now | overwritten; checked against reality every round |
| History | `LOG` entries, old `RESULT`s, `DECISIONS` | what happened | append-only; never edited |
| Stale | a doc older than the code it describes; a superseded plan | nothing until re-checked | marked stale, not silently trusted |
| Disposable | scratch files, an agent's context, unaccepted drafts | nothing | deleted freely |

The planner does the cleanup at the start of each round. It compares STATE with git, tests and outputs. Every current doc records when it was last checked and which files it covers. If any of those files has changed since, the doc is marked stale until someone re-checks it. Superseded plans are not deleted; they move to history with a pointer to what replaced them. Anything that exists only in an agent's context is treated as gone, because the agent will exit.

Three rules keep memory readable. Each thing has one name, based on its content ("the 4k-context ablation" rather than "arm C"). Each headline number is stored once and linked from elsewhere. Nothing is deleted to make the record look cleaner.

## 4. Making acceptance mean something {#making-acceptance-mean-something}

Acceptance is the one place my judgment enters the record, so it has to mean something. In research the output is a belief, and unlike a bad merge, a wrong belief raises no error. Agents that grade their own progress systematically accept non-improvements. Park and Choi show that a separate judge is not enough on its own: a strong judge that read the diff and the worker's report still accepted cycles, and 44% of the cycles it accepted were real regressions [2]. What helped was externally grounded verification. In the middle layer this becomes three rules:

- **The verifier re-runs checks.** For each acceptance criterion it runs the check named in the task and compares the result with what was claimed.
- **Six facts are reported separately**: edited, committed, launched, ran OK, verified, accepted. Anything without evidence is recorded as `unknown`.
- **Dead ends are recorded.** A hypothesis that was killed goes into the decision log with its reason, so later agents do not try it again.

## 5. Related work and limits {#related-work-and-limits}

Anthropic's long-running harness already treats the environment as memory: a progress file and git history let one agent resume a project across context windows [3]. Agensh makes tasks claimable objects in a shared workspace and scales a decentralized team to 1,024 agents [5]. Evolving Agent Teams keeps a persistent learnings file so a supervisor can restructure teams that stall [4]. GitSwarm lets homogeneous agents compound each other's intermediate work through shared persistent memory [7]. None of them separates who verifies a result from who decides that it counts. That separation is the human-facing part this post adds.

- **Not a runtime.** There is no scheduler and no daemon. For an always-on agent that takes requests from a team's chat and GitHub, see DeepSeek's Super-agent prototype [1].
- **No enforcement.** The rules hold only because agents read and follow them. Enforcement at the execution layer is a separate job, handled by tools such as agentsh [6].
- **Not for chores.** If you already know the answer and there is one result to merge, ask the agent directly.
- **One person's practice.** This is based on about four months of my own research projects. It is not a controlled study, the incidents are mine, and the example in section 1 is illustrative. It may not transfer to teams, to work where actions cannot be undone, or to projects where the hard part is waiting on other people.

## Try it

Paste this into Claude Code or Codex:

```
Install Swarm Oversight Harness from https://github.com/cogito233/swarm-oversight-harness — follow its INSTALL.md.
```
{: .install-cmd}

Then say `set up swarm oversight in this repo`.

Feedback and issues are welcome on [GitHub](https://github.com/cogito233/swarm-oversight-harness/issues).

## Disclaimer

> This post was drafted, checked against the cited sources, and typeset by an AI coding agent (Claude Code), working with the author in the way described above. The author decided what to write and accepted or rejected each result. Any errors that remain are the author's.

## Citation

Cited as:

> Lyu, Zhiheng. (Oct 2026). "Toward Human-in-the-Loop Oversight for Long-Horizon Agent Projects". cogito233.github.io. https://cogito233.github.io/blog/human-in-the-loop-oversight/.

Or

<div class="cite-box" markdown="1">
```bibtex
@article{lyu2026oversight,
  title   = "Toward Human-in-the-Loop Oversight for Long-Horizon Agent Projects",
  author  = "Lyu, Zhiheng",
  journal = "cogito233.github.io",
  year    = "2026",
  month   = "Oct",
  url     = "https://cogito233.github.io/blog/human-in-the-loop-oversight/"
}
```
</div>

## References

<div class="references" markdown="1">

1. DeepSeek Harness Team. ["Exploring Super-agent Design: Towards a More Autonomous Agent System"](https://github.com/deepseek-ai/dsh-superagent-prototype), with the dsh-superagent research prototype. October 2026.
2. H. Park, B. Choi. ["When Do Agent Loops Mistake Stagnation for Progress? Self-Evaluation Bias and Externally Grounded Verification in Long-Running Autonomous LLM Agent Loops."](https://arxiv.org/abs/2607.25152) arXiv preprint arXiv:2607.25152 (2026).
3. J. Young (Anthropic). ["Effective harnesses for long-running agents."](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents) Engineering blog, November 2025.
4. S. Cao, Z. Mao, D. Li, J. E. Gonzalez, I. Stoica. ["Evolving Agent Teams."](https://www.alphaxiv.org/abs/2610.36848) arXiv preprint arXiv:2610.36848 (2026).
5. Z. Zhan, T. Song, L. Dong, S. Huang, J. Lian, Y. Xia, F. Wei. ["Agensh: Scaling Organizational Intelligence to 1,024 Agents."](https://arxiv.org/abs/2609.26781) arXiv preprint arXiv:2609.26781 (2026).
6. ["agentsh: execution-layer security for AI agents."](https://github.com/canyonroad/agentsh) GitHub, 2026.
7. V. Shah, A. Samanta, P. Dahal, M. Plekhanov, C.-J. Wu, S. Yih, R. Munos, R. Fergus, J. Foerster, R. Salakhutdinov, S. Arora, J. Weston, A. Courville, A. Goyal. ["GitSwarm: Decentralized Compounding Inference."](https://arxiv.org/abs/2610.04862) arXiv preprint arXiv:2610.04862 (2026).
8. Z. Lyu. ["Swarm Oversight Harness."](https://github.com/cogito233/swarm-oversight-harness) GitHub, 2026.

</div>

</div>

<div class="lang lang-zh" markdown="1" hidden>

# 面向长周期 Agent 项目的人在回路监督

<p class="post-repo-link"><a href="https://github.com/cogito233/swarm-oversight-harness"><i class="fab fa-github" aria-hidden="true"></i> cogito233/swarm-oversight-harness</a></p>

<figure class="post-figure">
  <img src="{{ base_path }}/images/blog/oversight-layers.svg" alt="三层。顶层是你：一屏，包含章程、预算和各条线，以及两个动作：批准任务、验收结果。中间层是 planner 和仓库里的任务文件，四张任务卡，每张都有 TASK、LOG、RESULT。底层是许多短命的 agent，有的在跑、有的已退出，按所服务的任务分组。你和中间层之间是双向箭头；中间层和 agent 之间是许多细箭头；你和 agent 之间没有直接连线。" loading="lazy">
  <figcaption>读法：你只看一屏，只做两类决定。中间层把这些决定变成许多短命 agent 的工作，再把它们的产出汇回这一屏。你不直接盯着 agent。</figcaption>
</figure>

<p class="contents-line" markdown="1"><strong>目录：</strong> [人看到什么](#zh-1) · [为什么不直接盯 agent](#zh-2) · [中间层](#zh-3) · [让验收有分量](#zh-4) · [相关工作与局限](#zh-5) · [试一试](#zh-try)</p>

多 agent 的 harness 正在快速成熟：运行时已经能让很多 agent 并行干活 [1]，agent 团队也能共享持久记忆、在彼此的工作上继续推进 [5, 7]。系统越强，稀缺资源就越从算力转到监督者的注意力上。大约四个月前，我在自己的研究项目里碰到了这个问题，之后一直在找办法绕开它。我原以为大家都清楚这一点，看了这周的几条发布后又不太确定了，所以把我的做法写出来。

同时跑十个 agent 不难，难的是在一个项目上监督它们三个月。我在研究项目上这样做，几周后卡住我的既不是算力，也不是模型能力，而是弄清自己的项目里哪些是真的。

本文的论点是：**监督一个项目的成本，取决于人需要消解多少处连续性的断裂，而不取决于有多少个 agent 在跑。** 设计目标由此而来：让项目的历史保持连续，并且放在人读起来最省力的地方。对我帮助最大的类比，是编程从面向过程到面向对象的转变。一次 agent 会话就是一次过程调用：它有自己的调用栈（上下文窗口），做完一些事，返回一个值，然后局部状态就没了。大多数 agent 用法在这个意义上都是面向过程的，工作的状态存在当时正在跑的那个 agent 身上。中间层改为让任务成为对象：它有身份，有在多次调用之间保留下来的状态，有不变量，也有对外接口。agent 则成了作用在它上面的方法，可以先后来，也可以并行来，但没有哪个 agent 拥有它。

下面讲这一层在实践中的样子：我看什么、决定什么，以及这一层替我做了哪些事。我脑子里只装三样东西：一份章程、一份预算和一张各条线的列表。我只在两处出手：批准任务的目标，验收任务的产出。其余内容都在仓库的任务文件里，由一个 planner agent 维护；worker agent 挂到某个任务上，做完自己那部分就退出。这套方法已开源为 [Swarm Oversight Harness](https://github.com/cogito233/swarm-oversight-harness)。

## 1. 人看到什么 {#zh-1}

我对一个项目需要掌握的东西，一屏就装得下，分三部分：

1. **章程（Charter）**：我们想弄清什么，以及哪个假设的结果会改变我们的计划。
2. **预算**：每条线在回来请示之前，最多能花多少算力和钱。
3. **各条线（threads）**：每条工作线在做什么，凭证据走到了哪个阶段，在等什么。

一个示意例子（不是真实项目）：

```
CHARTER  问题：7B 模型能否学会用仓库测试检查自己的 patch？
         H1：用"先失败后通过"的测试对训练，留出仓库上
             自检准确率提升 ≥10 个点。
         终止：200 题 pilot 提升 < 3 个点就放弃。
预算     共 2,000 GPU 小时 + $1,500 API。超出单线上限 → 请示。

STATE    关键路径：T2 pilot 结果
  T1 开发  失败→通过 数据管线  已验证 CI #41   40/100 GPU-h   → 验收闸
  T2 实验  SFT pilot 200 题    已启动 无agent  310/400 GPU-h  → 完成钩子
  T3 审查  T1 泄漏 red-team    进行中          $90/$200
  T4 阅读  相关工作梳理        草稿            $15/$50
```
{: .state-block}

我从这一屏出发，只做两类决定：

- **批准一个任务**（由 planner 起草）：它为了什么，做到什么程度算完，最多能花多少。
- **验收一个结果**：验收后它不再是草稿，后续工作会以它为基础。

这一屏之外的内容都是写给 agent 看的。只有做这两类决定需要时，我才会打开某个任务的文件。预算属于任务授权的一部分，所以 agent 如果要超出本线上限，会停下来请示，不会接着重试。

## 2. 为什么不直接盯 agent {#zh-2}

**注意力无法扩展。** DeepSeek 的 Super-agent 设计指出，今天的 agent 在单任务上受限于模型能力，在多任务上受限于人的注意力 [1]。我自己的经验是，上限在三到五个会话。

**项目会丢失连续性。** 一个东西难不难懂，主要看它连不连续，跟它有多大关系不大：每个东西只有一个名字，每个事实只有一个版本，记录能按顺序读下来。每一处断裂都要读者先发现不一致，再判断哪边对，最后记住这个判断。几个月面向过程的 agent 工作，也就是每次会话带着状态干完就消失，会在不报任何错的情况下制造出这样的断裂。下面是我自己项目里真实发生过的例子（已脱敏）：

| 断裂 | 发生了什么 |
|---|---|
| 时间 | 重试通过后，之前的失败从日志里消失了。 |
| 命名 | "A–D 臂已启动"。两周后没人知道 C 臂是什么。 |
| 语义 | "完成"一词混指六件事，从"已修改"到"已验收"。 |
| 语义 | 缺失的评审判定被当成"失败"填入：某一格读数为 −0.388，真值约 +0.07。 |
| 版本 | 两份文档对同一个计划说法不一，agent 各照各的干。 |
| 权限 | 一个 agent 的"下一步：扩量"被下一个 agent 当成了指令。 |

每处断裂都不大，但累积起来，我每天早上都得把项目从头读一遍。盯的 agent 越多，断裂也越多，因为每多一次会话，就多一个状态可能存在又消失的地方。

## 3. 中间层 {#zh-3}

### agent 是过程，任务是对象

单个 agent 好理解，因为它只有一条线性历史，可以按顺序读下来。可这条历史会在调用返回时，也就是上下文窗口结束时消失。手工搭的 pipeline 更糟：历史散落在各个阶段，没有一个文件按顺序记下发生了什么。

中间层保留这条线性历史，只是改存到任务里，不放在 agent 身上。编程上的类比可以直接对应：

| 编程 | agent 项目 |
|---|---|
| 过程调用 | 一次 agent 会话 |
| 调用栈、局部变量 | 上下文窗口 |
| 返回值 | agent 交回的总结 |
| 对象身份 | 任务编号（`T2`） |
| 对象状态 | `TASK.md`、`LOG`、各份 `RESULT` |
| 不变量 | 验收标准、授权、预算 |
| 方法 | 挂上来、做事、退出的 agent |
| 对外接口 | 任务在 `STATE` 里的那一行 |
{: .analogy-table}

这个类比有一处重要的不同：普通对象会覆盖自己的字段，而任务从不改写自己的 LOG，所以它更像一个由事件日志重建状态的对象。正是这一点让历史能按顺序读下来。具体来说，任务是仓库里一个小而持久的对象，包括：

- `TASK.md`：目标，可以改和不能改的文件，所依据的授权和预算，验收标准及每条的验证方法，停止条件；
- 一份只追加的 `LOG`，顶部有当前状态的快照；
- 带证据的 `RESULT` 文件；
- `STATE` 里的一行，即第 1 节那一屏上的一行。

<figure class="post-figure">
  <img src="{{ base_path }}/images/blog/task-timeline.svg" alt="一条横条代表任务 T2，从左到右延伸，逐渐积累 TASK.md、只追加的 LOG 和 RESULT。上方：agent A 挂上实验后退出；中间一段实验在跑，没有任何 agent 存活，也不轮询，结束时触发完成钩子；agent B 接手、分析、写结果；验证者 C 检查结果。横条下方：起点是人批准 planner 起草的 TASK.md；终点是验证者检查之后，人验收结果。" loading="lazy">
  <figcaption>读法：横条是一个任务，它从不中断；上面的方框是 agent，来了又走。实验在跑的时候，一个 agent 都不在。人只在开头和结尾碰这个任务。</figcaption>
</figure>

由此有了一个习惯：agent 启动长实验后直接退出，不轮询。退出前，它往任务的 LOG 里追加一段交接：启动了什么、产物会出现在哪、怎么检查。实验结束时，完成钩子或标记文件发出信号，由新的 agent 接手这个任务。轮询只会把上下文浪费在等待上，还会让"我们在等什么"只记在一个迟早会被压缩的上下文里。

### Planner

中间层由一个 planner agent 维护。每一轮，它先重读仓库，把 STATE 里和现实不符的地方改过来。接着它确定关键路径，把工作拆成互不碰同一文件的任务派给 worker，安排验证结果，最后把所有结果汇总成一次 STATE 更新。我从不亲手合并 N 份结果，如果发现自己在这么做，就说明这一层出了问题。它是继续做还是回来找我，只看一条规则：HOW 问题尽力去试，WHAT 问题立刻停下。测试失败是 HOW 问题，在预算内重试；计划和现实矛盾，或者预算即将超出，是 WHAT 问题，要上报。

### 把任务交给 agent：用文档代替 prompt

默认的委派方式是把任务写进 subagent 的 prompt，再读它交回的总结。用上几周，这种方式就会出问题：任务只存在于父 agent 的上下文里，结果只是 worker 的自述，N 份总结要在一个脑子里合并，失败的尝试也不留记录。在中间层里，prompt 只是一个指针（*"去做 threads/T2/tasks/TASK-03.md"*），结果以文件形式返回。

| | prompt 子 agent | 工单式运行时 [1] | 任务文件 |
|---|---|---|---|
| 任务存在哪 | 父 agent 上下文 | 运行时数据库 | `TASK.md` |
| 以什么形式返回 | 总结 | 原对话回复 | `RESULT.md` + 证据 |
| 重启后还在 | 否 | 是 | 是 |
| 谁能接手 | 那个父 agent | 运行时执行器 | 任何 agent |
| 授权 | prompt 本身 | 代码强制 | 写明，但不强制 |
| 失败的尝试 | 丢失 | 工单历史 | LOG、旧 RESULT |

### 记忆：哪些可信，哪些要清理

把记忆搬进文件，前提是文件本身可信，否则第 2 节那些断裂只是换到了仓库里。仓库里的内容可信程度不一，每份记忆都属于下面四类之一：

| 类别 | 例子 | 能说明什么 | 怎么维护 |
|---|---|---|---|
| 当前 | `STATE`、`CHARTER`、进行中的 `TASK.md` | 现在什么是真的 | 直接覆盖；每轮对照现实核一遍 |
| 历史 | `LOG` 条目、旧 `RESULT`、`DECISIONS` | 发生过什么 | 只追加，从不修改 |
| 过期 | 比它描述的代码更旧的文档；被取代的计划 | 重新核对前一概不算 | 标为过期，不再默认可信 |
| 可丢弃 | 草稿文件、agent 的上下文、未验收的草稿 | 什么都不算 | 随时删除 |

清理由 planner 在每轮开始时完成：它把 STATE 和 git、测试、产物逐一对照，以现实为准。每份"当前"文档都记下上次核对的时间和它覆盖的文件；之后其中任一文件有改动，这份文档就标为过期，直到有人重新核对。被取代的计划不删，移进历史并注明被什么取代。只存在于 agent 上下文里的东西一律当作已经丢失，因为那个 agent 会退出。

有三条命名规则让记忆保持可读：一个东西只用一个名字，并按内容命名（叫"4k 上下文消融"，不叫"C 臂"）；一个事实只放一处，每个关键数字只存一份，别处用链接；不为了让记录好看而删除任何内容。

## 4. 让验收有分量 {#zh-4}

我的判断只在验收这一步进入记录，所以验收必须有分量。研究产出的是信念，跟合并代码不同，信念错了不会报错。如果让 agent 自己评估进展，它会系统性地把没有带来改进的改动当成改进。Park 和 Choi 的研究表明，只换一个评审不够：即使是读过 diff 和 worker 报告的强评审，它接受的轮次中仍有 44% 实际上是退步 [2]。有效的做法是以外部事实为依据。在中间层里，这落实为三条规则：

- **验证者要重跑，不能只读。** 对每条验收标准，它都运行任务里写明的检查，把实际结果和声称的结果对照。
- **六个事实分开报**：修改、提交、启动、运行成功、验证、验收。没有证据的一律写 `unknown`。
- **死胡同要记下来。** 被推翻的假设连同理由写进决策日志，之后的 agent 就不会再走一遍。

## 5. 相关工作与局限 {#zh-5}

Anthropic 的长时运行 harness 已经把环境用作记忆，靠一个进度文件和 git 历史，让单个 agent 跨多个上下文窗口持续推进一个项目 [3]。Agensh 把任务做成共享工作区里可认领的对象，把去中心化团队扩展到 1,024 个 agent [5]。Evolving Agent Teams 维护一份持久的经验文件，让 supervisor 能重组停滞的团队 [4]。GitSwarm 让同质的 agent 通过共享的持久记忆，在彼此的中间结果上不断积累 [7]。这些工作都没有把"谁来验证结果"和"谁决定结果算数"分开，本文补的就是这个面向人的部分。

- **不是运行时。** 没有调度器，也没有守护进程。如果想要一个常驻、从团队群聊和 GitHub 接收请求的 agent，可以看 DeepSeek 的 Super-agent 原型 [1]。
- **只是一套协议。** 规则之所以成立，是因为 agent 读了并遵守。执行层的强制是另一回事，由 agentsh 这类工具负责 [6]。
- **不适合杂活。** 如果你已经知道答案，只有一份结果要合并，直接让 agent 去做就行。
- **来自一个人的实践。** 这套做法来自我大约四个月的研究项目，没有做对照研究；翻车案例都出自我自己的项目，第 1 节的例子只是示意。它未必适用于团队协作、包含不可撤销操作的工作，或者主要难在等待他人的项目。

## 试一试 {#zh-try}

把这句贴进 Claude Code 或 Codex：

```
Install Swarm Oversight Harness from https://github.com/cogito233/swarm-oversight-harness — follow its INSTALL.md.
```
{: .install-cmd}

然后说 `set up swarm oversight in this repo`。

欢迎在 [GitHub](https://github.com/cogito233/swarm-oversight-harness/issues) 上提 issue 或反馈。

## 声明 {#zh-disclaimer}

> 本文由 AI coding agent（Claude Code）起草、对照引用来源核对事实并排版，它和作者的协作方式就是上文描述的那样：作者决定写什么，并逐条验收或驳回。经过这个流程后仍然存在的错误，由作者负责。

## 引用本文 {#zh-cite}

引用格式：

> Lyu, Zhiheng. (Oct 2026). "Toward Human-in-the-Loop Oversight for Long-Horizon Agent Projects". cogito233.github.io. https://cogito233.github.io/blog/human-in-the-loop-oversight/.

或

<div class="cite-box" markdown="1">
```bibtex
@article{lyu2026oversight,
  title   = "Toward Human-in-the-Loop Oversight for Long-Horizon Agent Projects",
  author  = "Lyu, Zhiheng",
  journal = "cogito233.github.io",
  year    = "2026",
  month   = "Oct",
  url     = "https://cogito233.github.io/blog/human-in-the-loop-oversight/"
}
```
</div>

## 参考文献 {#zh-references}

<div class="references" markdown="1">

1. DeepSeek Harness Team. ["Exploring Super-agent Design: Towards a More Autonomous Agent System"](https://github.com/deepseek-ai/dsh-superagent-prototype), 及 dsh-superagent 研究原型。2026 年 10 月。
2. H. Park, B. Choi. ["When Do Agent Loops Mistake Stagnation for Progress? Self-Evaluation Bias and Externally Grounded Verification in Long-Running Autonomous LLM Agent Loops."](https://arxiv.org/abs/2607.25152) arXiv preprint arXiv:2607.25152 (2026).
3. J. Young (Anthropic). ["Effective harnesses for long-running agents."](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents) Engineering blog，2025 年 11 月。
4. S. Cao, Z. Mao, D. Li, J. E. Gonzalez, I. Stoica. ["Evolving Agent Teams."](https://www.alphaxiv.org/abs/2610.36848) arXiv preprint arXiv:2610.36848 (2026).
5. Z. Zhan, T. Song, L. Dong, S. Huang, J. Lian, Y. Xia, F. Wei. ["Agensh: Scaling Organizational Intelligence to 1,024 Agents."](https://arxiv.org/abs/2609.26781) arXiv preprint arXiv:2609.26781 (2026).
6. ["agentsh: execution-layer security for AI agents."](https://github.com/canyonroad/agentsh) GitHub, 2026.
7. V. Shah, A. Samanta, P. Dahal, M. Plekhanov, C.-J. Wu, S. Yih, R. Munos, R. Fergus, J. Foerster, R. Salakhutdinov, S. Arora, J. Weston, A. Courville, A. Goyal. ["GitSwarm: Decentralized Compounding Inference."](https://arxiv.org/abs/2610.04862) arXiv preprint arXiv:2610.04862 (2026).
8. Z. Lyu. ["Swarm Oversight Harness."](https://github.com/cogito233/swarm-oversight-harness) GitHub, 2026.

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
