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

Hi, I'm Zhiheng. Welcome to my personal website! This fall I'm joining the University of Illinois Urbana-Champaign (UIUC) as a Computer Science PhD student, advised by Professor Lingming Zhang. My research focuses on **AI for Software Engineering**, particularly agentic post-training, evaluation, and task synthesis.

I completed my M.Math. in Computer Science at the University of Waterloo (2026), supervised by Professor Wenhu Chen at the TIGER Lab, with a thesis on post-training LLMs as software engineering agents. Before that, I did my undergrad at the University of Hong Kong, where I entered the **ICPC World Finals** and won two regional gold medals, and worked with research groups at Berkeley, ETH Zürich, and the University of Michigan.

## Highlights

<ul class="highlights">
  <li><span class="hl__date">Aug 2026</span><span>Joining <strong>UIUC</strong> as a CS PhD student, advised by Prof. Lingming Zhang.</span></li>
  <li><span class="hl__date">Jul 2026</span><span>Released <a href="https://arxiv.org/abs/2607.20911"><strong>Tencent WorkBuddy Bench</strong></a> — 260 contamination-resistant real-world tasks across Code, Web, Office, and Security.</span></li>
  <li><span class="hl__date">Jun 2026</span><span><a href="https://github.com/TIGER-AI-Lab/verl-tool"><strong>VerlTool</strong></a> accepted to TMLR 2026, after winning the <span class="hl__award">Best Paper Award at ICLR 2026 SPOT</span>.</span></li>
  <li><span class="hl__date">May 2026</span><span><a href="https://arxiv.org/abs/2605.26494"><strong>MiniMax-M2</strong></a> tech report released — <strong>69% Pass@1 on SWE-bench Verified</strong>, #2 on MultiSWE and TerminalBench.</span></li>
  <li><span class="hl__date">May 2026</span><span><a href="https://arxiv.org/abs/2603.16124"><strong>SWE-QA-Pro</strong></a> accepted to <strong>ACL 2026 Findings</strong> — our trained 8B model surpasses GPT-4o on repository-level QA.</span></li>
</ul>

## Experience

**Tencent** (Feb – Jul 2026) — Intern (Qingyun Program) on **WorkBuddy**, working closely with [Ke Li](https://keli.info/) and [Chao Peng](https://chao-peng.github.io/). I owned the team's evaluation platform and infrastructure (**3× evaluation volume**), explored task synthesis from production user data, and co-led the release of [**Tencent WorkBuddy Bench**](https://arxiv.org/abs/2607.20911) ([code](https://github.com/Tencent/workbuddy-bench)).

**MiniMax** (May 2025 – Feb 2026) — Research Scientist Intern on the base model team, contributing to code-agent post-training for [**MiniMax M1 through M2.5**](https://arxiv.org/abs/2605.26494). M2 achieved **69% Pass@1 on SWE-bench Verified** (#2 on MultiSWE and TerminalBench). I led large-scale SWE data synthesis (**36K verifiable tasks** from 5K+ sandbox environments), built the rubric-based evaluation benchmark that became the team's core metric for code-agent user experience, and led CodeMirror ToolScaling for M2.5.

## Research

### Agentic Post-Training

I work on the full pipeline of post-training for software engineering agents. As a co-first author and core developer of [**VerlTool**](https://github.com/TIGER-AI-Lab/verl-tool) (TMLR 2026; **Best Paper Award at ICLR 2026 SPOT**), I built the stateful environment interaction protocol and SWE agent training pipeline. In my master's thesis, RLVR training took Qwen3-8B from **10.4% to 19.5%** on SWE-bench Verified. I also co-first authored [**SWE-Next**](https://arxiv.org/abs/2603.20691) (2,308 verifiable tasks mined from 311 real repositories) and [**BrowserAgent**](https://arxiv.org/abs/2502.01882) (TMLR).

### Benchmarks & Evaluation

As models get stronger, the definition of tasks becomes increasingly important. [**SWE-QA-Pro**](https://arxiv.org/abs/2603.16124) (ACL 2026 Findings) builds a contamination-resistant repository-understanding benchmark from long-tail repositories; [**WorkBuddy Bench**](https://arxiv.org/abs/2607.20911) reconstructs real-world tasks from production scenarios; [**PixelWorld**](https://arxiv.org/abs/2501.19339) (TMLR) probes reasoning by converting text into pixels.

<p class="dim">Earlier, I worked on causal reasoning and knowledge methods for LLMs: <a href="https://arxiv.org/abs/2407.16347">FactTrack</a> (NAACL 2025 Oral) for time-aware world state tracking in story outlines, <a href="https://arxiv.org/abs/2306.05836">Corr2Cause</a> (ICLR 2024) for testing pure causal inference, and psychologically-inspired causal prompting. I also contributed to StructEval and VideoScore.</p>

For future research directions, see my [Research Statements](/statements/); my complete background is in my [CV](/files/CV.pdf).

## Selected Publications

{% include base_path %}

<div class="pub-list">
{% assign pubs = site.publications | where: "selected", true | sort: 'date' | reverse %}
{% for pub in pubs %}
  <div class="pub">
    <a class="pub__title" href="{{ base_path }}{{ pub.url }}">{{ pub.title }}</a>
    <p class="pub__excerpt">{{ pub.excerpt | markdownify | strip_html | strip_newlines }}</p>
    <p class="pub__meta">{% if pub.venue and pub.venue != '' %}<span class="pub__venue">{{ pub.venue }}</span> &middot; {% endif %}{{ pub.date | date: "%Y" }}{% if pub.paperurl and pub.paperurl != '' %} &middot; <a href="{{ pub.paperurl }}">paper</a>{% endif %}</p>
  </div>
{% endfor %}
</div>

<p class="pub-list__more"><a href="{{ base_path }}/publications/">Full publication list →</a></p>

## Contact

Feel free to reach out at `zhihenglyu.cs@gmail.com` for research collaboration, open-source projects, or mentorship.
