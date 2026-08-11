---
layout: home
permalink: /
title: "About Zhiheng"
excerpt: "About me"
author_profile: true
redirect_from: 
  - /about/
  - /about.html
---

Hi, I'm Zhiheng. Welcome to my personal website! This fall I'm joining the University of Illinois Urbana-Champaign (UIUC) as a Computer Science PhD student, advised by Professor Lingming Zhang. My research focuses on **AI for Software Engineering**, particularly agentic post-training, evaluation, task synthesis, and causal reasoning.

I completed my M.Math. in Computer Science at the University of Waterloo (2026), supervised by Professor Wenhu Chen at the TIGER Lab, with a thesis on post-training LLMs as software engineering agents. I did my undergrad in Computer Science at the University of Hong Kong, where I was active in algorithm competitions—I entered the ICPC World Finals and won two regional gold medals. I've also had the privilege to work with research groups at Berkeley, ETH Zürich, and the University of Michigan.

## Previous Work

From February to July 2026, I was an **Intern (Qingyun Program)** working on Tencent **WorkBuddy**, where I worked closely with [**Ke Li**](https://keli.info/) and [**Chao Peng**](https://chao-peng.github.io/). I was responsible for the team's evaluation platform and infrastructure, and explored how to synthesize realistic tasks from user data, including production traces and interaction trajectories.

During this internship, we released [**Tencent WorkBuddy Bench**](https://arxiv.org/abs/2607.20911) ([code](https://github.com/Tencent/workbuddy-bench)), a contamination-resistant benchmark with 260 real-world tasks across Code, Web, Office, and Security. The benchmark evaluates coding agents across a broader range of computer-based work and provides domain-specific environments, tests, and evaluation tools.

From May 2025 to February 2026, I was a **Research Scientist Intern** on the base model team at **MiniMax**, contributing to code-agent post-training for [**MiniMax M1, M2, M2.1, and M2.5**](https://arxiv.org/abs/2605.26494). M2 achieved 69% Pass@1 on SWE-bench Verified and ranked #2 on MultiSWE and TerminalBench. I led large-scale SWE data synthesis (36K verifiable tasks from 5K+ sandbox environments), built a rubric-based evaluation benchmark from live user feedback that became the core metric for diagnosing code-agent user experience, and led CodeMirror ToolScaling for M2.5 to help the model generalize across diverse scaffolding frameworks.

## Research Areas

### Agentic Post-Training
I have been deeply involved in the full pipeline of post-training for software engineering agents. As a co-first author and core developer of [**VerlTool**](https://github.com/TIGER-AI-Lab/verl-tool) (TMLR 2026; Best Paper Award at ICLR 2026 SPOT), I develop the stateful environment interaction protocol and SWE agent training pipeline. In my master's thesis, RLVR training took Qwen3-8B from 10.4% to 19.5% on SWE-bench Verified.

I also co-first authored [**SWE-Next**](https://arxiv.org/abs/2603.20691), an execution-grounded framework that mines 2,308 verifiable SWE tasks from 311 real repositories, and [**BrowserAgent**](https://arxiv.org/abs/2502.01882) (TMLR), which builds web agents through direct browser environment interaction rather than traditional tool-based approaches.

### Benchmarks & Evaluation
I believe that as models get stronger, the definition of tasks becomes increasingly important. My benchmark work spans three approaches:

- **Synthesis**: Converting existing data (PixelWorld converts textual reasoning to images, Corr2Cause generates causal reasoning problems)
- **Human-in-the-loop**: Repo-level QA with crowdsourced annotation and validation—[**SWE-QA-Pro**](https://arxiv.org/abs/2603.16124) (ACL 2026 Findings) builds a contamination-resistant repository-understanding benchmark where our trained 8B model surpasses GPT-4o
- **Real-world tasks**: Building benchmarks from production scenarios and GitHub repositories, such as WorkBuddy Bench

I've also contributed to **StructEval** for structured output evaluation and **VideoScore** for video generation assessment.

### Causal Reasoning & Knowledge Methods
My work explores lightweight ways to enhance LLM capabilities without retraining. At Berkeley, I developed **FactTrack** for time-aware world state tracking in story outlines, decomposing complex narratives into atomic facts for contradiction detection.

I've investigated how large language models understand causal relations through **Psychologically-Inspired Causal Prompts**, exploring different psychological processes in sentiment classification. The **Corr2Cause** dataset tests pure causal inference skills of LLMs.

## Current Focus

I'm particularly interested in **AI for Software Engineering** because it combines structural data that's easy to synthesize, real-world relevance with immediate impact, and strong economic value. My research explores decomposing SWE tasks into skill-specific components: debugging, performance optimization, refactoring, test generation, repository-level QA, and security.

For detailed future research directions, see my [Research Statements](/statements/) page. My complete background is in my [CV](/files/CV.pdf).

## Publications

{% include base_path %}

{% for post in site.publications reversed %}
  {% include archive-single.html %}
{% endfor %}

## Contact

Feel free to reach out at `zhihenglyu.cs@gmail.com` for research collaboration, open-source projects, or mentorship.
