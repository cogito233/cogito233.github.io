---
permalink: /
title: "About Zhiheng"
excerpt: "About me"
author_profile: true
redirect_from: 
  - /about/
  - /about.html
---

Hi, I'm Zhiheng. Welcome to my personal website! I'm currently a second-year Master's student at the [University of Waterloo](https://uwaterloo.ca/), supervised by Professor [Wenhu Chen](https://wenhuchen.github.io/) at the [TIGER Lab](https://tiger-ai-lab.github.io/). My research mainly spans three areas: **Agentic Post-Training**, **Benchmarks**, and **Knowledge Argument Methods**.

I did my undergrad in Computer Science at the [University of Hong Kong](https://www.hku.hk/), where I was active in algorithm competitions—I entered the [ICPC World Finals](https://icpc.global/) and won two regional gold medals. I've had the privilege to work with amazing research groups including [Berkeley NLP](https://nlp.cs.berkeley.edu/), [ETH NLPED Lab](https://www.mrinmaya.io/), and [University of Michigan LIT Lab](https://lit.eecs.umich.edu/).

Currently, I'm focused on **AI for Software Engineering**, particularly post-training for large models using RL-based methods. I'm a core contributor to the open-source framework [VerlTool](https://github.com/TIGER-AI-Lab/verl-tool/tree/main) and have been deeply involved in the development of MiniMax-M1 and M2-preview on software engineering tasks. I've also worked as a Research Scientist Intern at [MiniMax](https://www.minimaxi.com/) on their Base Model Team. 

## Research Highlight

### Agentic Post-Training
- Deep involvement in the full pipeline of [MiniMax-M1](https://arxiv.org/abs/placeholder) and M2-preview on software engineering tasks
- Core contributor to [VerlTool](https://github.com/TIGER-AI-Lab/verl-tool/tree/main), developing environment interaction modules and post-training setup for SWE tasks
- Research on [BrowserAgent](https://arxiv.org/abs/placeholder) for information-seeking tasks with direct browser environment interaction

### Benchmarks & Evaluation
- Leading SWE-QA-Pro: a repo-level QA benchmark with human-in-the-loop annotation
- Contributed to [StructEval](https://tiger-ai-lab.github.io/StructEval/) and [VideoScore](https://tiger-ai-lab.github.io/VideoScore/) benchmark projects
- Created [PixelWorld](https://arxiv.org/abs/placeholder) for probing VLM reasoning and [Corr2Cause](https://huggingface.co/datasets/causalnlp/corr2cause) dataset for causal inference evaluation

### Causal Reasoning & Knowledge Methods
- [FactTrack](https://arxiv.org/abs/placeholder) project at [Berkeley NLP](https://nlp.cs.berkeley.edu/): fact tracking and contradiction detection in narrative structures
- Probing large language models' understanding of causality with [Psychologically-Inspired Causal Prompts](https://arxiv.org/pdf/2305.01764.pdf)
- Lightweight methods to enhance LLM capabilities without retraining

## Publications

{% include base_path %}

{% for post in site.publications reversed %}
  {% include archive-single.html %}
{% endfor %}

## Future Research Directions

I'm particularly interested in **AI for Software Engineering** for three key reasons:
1. **Structural data**: SWE data is easier to collect and synthesize
2. **Real-world relevance**: Closer to practical applications with ill-defined task paradigms
3. **Economic value**: Training on code tasks can generalize to improve reasoning across domains

I'm exploring how to decompose SWE tasks into skill-specific components using expert knowledge, focusing on areas like debugging, performance optimization, refactoring, test generation, repository-level QA, and security.

## Contact

If you're a potential collaborator with similar interests, feel free to reach out at `z63lyu@uwaterloo.ca`. I'm open to discussing research projects, open source development, community building, or startup opportunities.

For students interested in CS research, I'm excited to share experiences and potentially start mentorships.