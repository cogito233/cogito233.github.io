---
title: "The MiniMax-M2 Series: Mini Activations Unleashing Max Real-World Intelligence"
collection: publications
selected: true
permalink: /publication/MiniMax-M2
excerpt: 'Technical report on the MiniMax-M2 MoE series (229.9B total / 9.8B active), built end-to-end for agentic deployment. 69% Pass@1 on SWE-bench Verified, #2 on MultiSWE and TerminalBench.'
date: 2026-05-26
venue: 'Technical Report'
paperurl: 'https://arxiv.org/abs/2605.26494'
citation: 'MiniMax. (2026). The MiniMax-M2 Series: Mini Activations Unleashing Max Real-World Intelligence. Technical Report.'
---

The MiniMax-M2 series is a family of Mixture-of-Experts language models built around the principle that mini activations can unleash maximum real-world intelligence: the flagship M2 has 229.9B total parameters with only 9.8B activated per token. Designed end-to-end for agentic deployment, the series rests on agent-driven data pipelines producing large-scale verifiable trajectories grounded in executable workspaces, and Forge, a scalable agent-native RL system for long-horizon agent trajectories.

M2 achieved **69% Pass@1 on SWE-bench Verified** and ranked **#2 on MultiSWE and TerminalBench**. During my internship on the base model team, I contributed to code-agent post-training across M1, M2, M2.1, and M2.5 — including large-scale SWE data synthesis (36K verifiable tasks from 5K+ sandbox environments), a rubric-based evaluation benchmark built from live user feedback, and CodeMirror ToolScaling for M2.5.

[Paper](https://arxiv.org/abs/2605.26494) · [Release notes](https://www.minimax.io/news/minimax-m2)

Recommended citation:

{% raw %}
```tex
@article{minimax2026m2,
  title={The MiniMax-M2 Series: Mini Activations Unleashing Max Real-World Intelligence},
  author={MiniMax},
  journal={arXiv preprint arXiv:2605.26494},
  year={2026}
}
```
{% endraw %}
