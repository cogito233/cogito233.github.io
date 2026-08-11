---
title: "SWE-QA-Pro: A Representative Benchmark and Scalable Training Recipe for Repository-Level Code Understanding"
collection: publications
permalink: /publication/SWE-QA-Pro
excerpt: 'A contamination-resistant benchmark for agentic repository-level code understanding, plus a training recipe that lets an 8B model surpass GPT-4o.'
date: 2026-03-17
venue: 'ACL 2026 Findings'
paperurl: 'https://arxiv.org/abs/2603.16124'
citation: 'Songcheng Cai*, Zhiheng Lyu*, Yuansheng Ni, Xiangchao Chen, Baichuan Zhou, Shenzhe Zhu, Yi Lu, Haozhe Wang, Chi Ruan, Benjamin Schneider, Weixu Zhang, Xiang Li, Andy Zheng, Yuyu Zhang, Ping Nie, Wenhu Chen (2026). SWE-QA-Pro: A Representative Benchmark and Scalable Training Recipe for Repository-Level Code Understanding. ACL 2026 Findings.'
---

SWE-QA-Pro is a benchmark for agentic repository-level code understanding, built from diverse long-tail repositories with executable environments so that models cannot cheat via memorized knowledge of popular repos. Topical balance is enforced via issue-driven clustering, and questions solvable by direct-answer baselines are filtered out through difficulty calibration — leaving a dataset where agentic workflows significantly outperform direct answering (~13-point gap for Claude Sonnet 4.5).

The paper also contributes a scalable training recipe: with it, an 8B model surpasses GPT-4o on repository-level QA. Zhiheng is a co-first author.

[Paper](https://arxiv.org/abs/2603.16124)

Recommended citation:

{% raw %}
```tex
@article{cai2026sweqapro,
  title={SWE-QA-Pro: A Representative Benchmark and Scalable Training Recipe for Repository-Level Code Understanding},
  author={Cai, Songcheng and Lyu, Zhiheng and Ni, Yuansheng and Chen, Xiangchao and Zhou, Baichuan and Zhu, Shenzhe and Lu, Yi and Wang, Haozhe and Ruan, Chi and Schneider, Benjamin and Zhang, Weixu and Li, Xiang and Zheng, Andy and Zhang, Yuyu and Nie, Ping and Chen, Wenhu},
  journal={arXiv preprint arXiv:2603.16124},
  year={2026}
}
```
{% endraw %}
