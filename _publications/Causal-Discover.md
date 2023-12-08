---
title: "Can Large Language Models Distinguish Cause from Effect?"
collection: publications
permalink: /publication/Causal-Discover
excerpt: 'Our paper conducts a post-hoc analysis to check whether large language models can be used to distinguish cause from effect.'
date: 2023-05-06
venue: ''
paperurl: 'https://openreview.net/forum?id=ucHh-ytUkOH'
citation: 'Jin, Lalwani, A., Vaidhya, T., Shen, X., Ding, Y., Lyu, Z., Sachan, M., Mihalcea, R., & Schölkopf, B. (2022). Logical Fallacy Detection.'
---
Identifying the causal direction between two variables has long been an important but challenging task for causal inference. Existing work proposes to distinguish whether X->Y or Y->X by setting up an input-output learning task using the two variables, since causal and anticausal learning have different performance under semi-supervised learning and domain shift. This approach works for many task-specific models trained on the input-output pairs. However, with the rise of general-purpose large language models (LLMs), there are various challenges posed to this previous task-specific learning approach, since continued training of LLMs is less likely to be affordable for university labs, and LLMs are no longer trained on specific input-output pairs. In this work, we propose a new paradigm to distinguish cause from effect using LLMs. Specifically, we conduct post-hoc analysis using natural language prompts that describe different possible causal stories behind the X, Y pairs, and test their zero-shot performance. Through the experiments, we show that the natural language prompts that describe the same causal story as the ground-truth data generating direction achieve the highest zero-shot performance, with 2% margin over anticausal prompts. We highlight that it will be an interesting direction to identify more causal relations using LLMs. Our code and data are at https://github.com/cogito233/llm-bivariate-causal-discovery

[Download paper here](https://openreview.net/forum?id=ucHh-ytUkOH)

Recommended citation:

```tex
@inproceedings{zhiheng2022can,
  title={Can Large Language Models Distinguish Cause from Effect?},
  author={Zhiheng, LYU and Jin, Zhijing and Mihalcea, Rada and Sachan, Mrinmaya and Sch{\"o}lkopf, Bernhard},
  booktitle={UAI 2022 Workshop on Causal Representation Learning},
  year={2022}
}

```

