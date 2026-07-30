---
permalink: /
title: "About Zhiheng"
excerpt: "About me"
author_profile: true
redirect_from: 
  - /about/
  - /about.html
---

Hi, I'm Zhiheng. Welcome to my personal website! I will join the University of Illinois Urbana-Champaign (UIUC) as a Computer Science PhD student in Fall 2026, advised by Professor Lingming Zhang. My research focuses on **AI for Software Engineering**, particularly agentic post-training, evaluation, task synthesis, and causal reasoning.

I am completing my M.Math. in Computer Science at the University of Waterloo, where I am supervised by Professor Wenhu Chen at the TIGER Lab. I did my undergrad in Computer Science at the University of Hong Kong, where I was active in algorithm competitions—I entered the ICPC World Finals and won two regional gold medals. I've also had the privilege to work with research groups at Berkeley, ETH Zürich, and the University of Michigan.

## Previous Work

From February to July 2026, I was an **Intern (Qingyun Program)** working on Tencent **WorkBuddy**, where I worked closely with [**Ke Li**](https://keli.info/) and [**Chao Peng**](https://chao-peng.github.io/). I was responsible for the team's evaluation platform and infrastructure, and explored how to synthesize realistic tasks from user data, including production traces and interaction trajectories.

During this internship, we released [**Tencent WorkBuddy Bench**](https://arxiv.org/abs/2607.20911) ([code](https://github.com/Tencent/workbuddy-bench)), a contamination-resistant benchmark with 260 real-world tasks across Code, Web, Office, and Security. The benchmark evaluates coding agents across a broader range of computer-based work and provides domain-specific environments, tests, and evaluation tools.

## Research Areas

### Agentic Post-Training
I have been deeply involved in the full pipeline of post-training for software engineering agents. As a core contributor to **VerlTool**, I develop environment interaction modules and post-training setups for SWE tasks. During my internship at MiniMax, my work on **MiniMax-M2** achieved 69% Pass@1 on SWE-Verified and ranked #2 on MultiSWE and TerminalBench. I designed large-scale SWE data synthesis pipelines generating over 36K verifiable tasks from 5K+ sandbox environments.

I'm also working on **BrowserAgent**, which focuses on information-seeking tasks through direct browser environment interaction, moving beyond traditional tool-based approaches to enable more natural web navigation and information extraction.

### Benchmarks & Evaluation
I believe that as models get stronger, the definition of tasks becomes increasingly important. My benchmark work spans three approaches:

- **Synthesis**: Converting existing data (PixelWorld converts textual reasoning to images, Corr2Cause generates causal reasoning problems)
- **Human-in-the-loop**: Developing repo-level QA benchmarks with crowdsourced annotation and validation
- **Structural data**: Building benchmarks from web pages and GitHub repositories

I've contributed to **StructEval** for structured output evaluation and **VideoScore** for video generation assessment.

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
