---
title: "SWE-Next: Scalable Real-World Software Engineering Tasks for Agents"
collection: publications
permalink: /publication/SWE-Next
excerpt: 'An execution-grounded framework for scalable SWE task and trajectory collection: 2,308 verifiable tasks mined from 311 real repositories.'
date: 2026-03-21
venue: 'Under Review'
paperurl: 'https://arxiv.org/abs/2603.20691'
citation: 'Jiarong Liang*, Zhiheng Lyu*, Zijie Liu, Xiangchao Chen, Ping Nie, Kai Zou, Wenhu Chen (2026). SWE-Next: Scalable Real-World Software Engineering Tasks for Agents. Under Review.'
---

SWE-Next is an execution-grounded framework for scalable software-engineering task and trajectory collection. On the data side, it mines real merged pull requests, executes candidate base/merged commit pairs, and retains only pairs that produce strict test improvements without regressions — yielding self-verifying task instances (2,308 verifiable tasks from 311 repositories). Strict submission gating keeps collected trajectories evidence-driven rather than speculative. On the systems side, SWE-Next amortizes the dominant cost of building repository-specific environments.

Zhiheng is a co-first author.

[Paper](https://arxiv.org/abs/2603.20691)

Recommended citation:

{% raw %}
```tex
@article{liang2026swenext,
  title={SWE-Next: Scalable Real-World Software Engineering Tasks for Agents},
  author={Liang, Jiarong and Lyu, Zhiheng and Liu, Zijie and Chen, Xiangchao and Nie, Ping and Zou, Kai and Chen, Wenhu},
  journal={arXiv preprint arXiv:2603.20691},
  year={2026}
}
```
{% endraw %}
