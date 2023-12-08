---
title: "Psychologically-Inspired Causal Prompts."
collection: publications
permalink: /publication/Causal-Prompt
excerpt: 'This paper is about a prompting method embedded causal direction and analyze the performance gap of LLMs'
date: 2023-05-01
venue: ''
paperurl: 'https://arxiv.org/pdf/2305.01764'
citation: 'Lyu, Z., Jin, Z., Mattern, J., Mihalcea, R., Sachan, M., & Schoelkopf, B. (2023). Psychologically-Inspired Causal Prompts. arXiv preprint arXiv:2305.01764.'
---
NLP datasets are richer than just input-output pairs; rather, they carry causal relations between the input and output variables. In this work, we take sentiment classification as an example and look into the causal relations between the review (X) and sentiment (Y). As psychology studies show that language can affect emotion, *different psychological processes* are evoked when a person first makes a rating and then self-rationalizes their feeling in a review (where the sentiment causes the review, i.e., Y → X), versus first describes their experience, and weighs the pros and cons to give a final rating (where the review causes the sentiment, i.e., X → Y ). Furthermore, it is also a completely different psychological process if an annotator infers the original rating of the user by theory of mind (ToM) (where the review causes the rating, i.e., X −−(ToM)−→ Y ). In this paper, we verbalize these three causal mechanisms of human psychological processes of sentiment classification into three different causal prompts, and study (1) how differently they perform, and (2) what nature of sentiment classification data leads to agreement or diversity in the model responses elicited by the prompts. We suggest future work raise awareness of different causal structures in NLP tasks.1


[Download paper here](https://arxiv.org/pdf/2305.01764.pdf)

Recommended citation:

```tex
@article{lyu2023psychologically,
  title={Psychologically-Inspired Causal Prompts},
  author={Lyu, Zhiheng and Jin, Zhijing and Mattern, Justus and Mihalcea, Rada and Sachan, Mrinmaya and Schoelkopf, Bernhard},
  journal={arXiv preprint arXiv:2305.01764},
  year={2023}
}

```

