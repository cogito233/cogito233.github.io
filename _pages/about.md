---
permalink: /
title: "About Zhiheng"
excerpt: "About me"
author_profile: true
redirect_from: 
  - /about/
  - /about.html
---

Hi, I'm Zhiheng. Welcome to my personal website! I'm currently a second-year Master's student at the University of Waterloo, supervised by Professor Wenhu Chen at the TIGER Lab. My research focuses on **AI for Software Engineering**, particularly in agentic post-training, benchmarks, and causal reasoning.

I did my undergrad in Computer Science at the University of Hong Kong, where I was active in algorithm competitions—I entered the ICPC World Finals and won two regional gold medals. I've had the privilege to work with research groups at Berkeley, ETH Zürich, and University of Michigan.

Currently, I'm focused on post-training for large models using RL-based methods. I'm a core contributor to the open-source framework VerlTool and have been involved in the development of MiniMax-M1 on software engineering tasks. I've also worked as a Research Scientist Intern at MiniMax. 

## Research Areas

### Agentic Post-Training
I'm deeply involved in the full pipeline of post-training for software engineering agents. As a core contributor to **VerlTool**, I develop environment interaction modules and post-training setups for SWE tasks. My work on **MiniMax-M1** achieved 64% Pass@1 on SWE-Verified and ranked #2 on MultiSWE and TerminalBench. I've designed large-scale SWE data synthesis pipelines generating over 36K verifiable tasks from 5K+ sandbox environments.

I'm also working on **BrowserAgent**, which focuses on information-seeking tasks through direct browser environment interaction, moving beyond traditional tool-based approaches to enable more natural web navigation and information extraction.

### Benchmarks & Evaluation
I believe that as models get stronger, the definition of tasks becomes increasingly important. My benchmark work spans three approaches:

- **Synthesis**: Converting existing data (PixelWorld converts textual reasoning to images, Corr2Cause generates causal reasoning problems)
- **Human-in-the-loop**: Leading SWE-QA-Pro with crowdsourced annotation and validation
- **Structural data**: Building benchmarks from web pages and GitHub repositories

I've contributed to **StructEval** for structured output evaluation and **VideoScore** for video generation assessment.

### Causal Reasoning & Knowledge Methods
My work explores lightweight ways to enhance LLM capabilities without retraining. At Berkeley, I developed **FactTrack** for time-aware world state tracking in story outlines, decomposing complex narratives into atomic facts for contradiction detection.

I've investigated how large language models understand causal relations through **Psychologically-Inspired Causal Prompts**, exploring different psychological processes in sentiment classification. The **Corr2Cause** dataset tests pure causal inference skills of LLMs.

## Current Focus

I'm particularly interested in **AI for Software Engineering** because it combines structural data that's easy to synthesize, real-world relevance with immediate impact, and strong economic value. My research explores decomposing SWE tasks into skill-specific components: debugging, performance optimization, refactoring, test generation, repository-level QA, and security.

For detailed future research directions, see my [Research Statements](/statements/) page. My complete background is in my [CV](https://cogito233.github.io/files/CV_short_Zhiheng.pdf).

## Publications

{% include base_path %}

{% for post in site.publications reversed %}
  {% include archive-single.html %}
{% endfor %}

## Contact

Feel free to reach out at `z63lyu@uwaterloo.ca` for research collaboration, open source projects, or mentorship opportunities.