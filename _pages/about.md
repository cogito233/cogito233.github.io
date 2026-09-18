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

Hi, I'm Zhiheng. Welcome to my personal website! I'm a first-year Computer Science PhD student at the University of Illinois Urbana-Champaign (UIUC), advised by Professor [Lingming Zhang](http://lingming.cs.illinois.edu/). My research focuses on **AI for Software Engineering**, particularly agentic post-training, evaluation, and task synthesis.

<p class="visitor-geo" id="visitor-geo" aria-live="polite"></p>
<script>
(function(){
  var el=document.getElementById('visitor-geo'); if(!el) return;
  var ctl=new AbortController(); setTimeout(function(){ctl.abort()},4000);
  fetch('https://ipapi.co/json/',{signal:ctl.signal}).then(function(r){return r.ok?r.json():null}).then(function(d){
    if(!d||!d.country_name) return;
    var place=[d.city,d.region,d.country_name].filter(function(x){return x&&x.length}).filter(function(x,i,a){return a.indexOf(x)===i}).join(', ');
    var h=new Date().getHours(); var g=h<5?'Good night':h<12?'Good morning':h<18?'Good afternoon':'Good evening';
    el.textContent=g+', visitor from '+place+'.';
  }).catch(function(){});
})();
</script>


I completed my M.Math. in Computer Science at the University of Waterloo (2026), supervised by Professor [Wenhu Chen](https://wenhuchen.github.io/) at the [TIGER Lab](https://tiger-ai-lab.github.io/), with a thesis on post-training LLMs as software engineering agents. Before that, I did my undergrad at the University of Hong Kong, where I entered the **ICPC World Finals** and won two regional gold medals, and worked with research groups at Berkeley, ETH Zürich, and the University of Michigan.

## Highlights

<ul class="highlights">
  <li><span class="hl__date">Sep 2026</span><span>New blog post: <a href="/blog/three-orders-of-self-improvement/"><strong>How Far Has RSI Gotten in Post-Training?</strong></a> — from plain code agent to RSI: four orders, which one agents have reached (English / 中文).</span></li>
  <li><span class="hl__date">Aug 2026</span><span>Joining <strong>UIUC</strong> as a CS PhD student, advised by Prof. <a href="http://lingming.cs.illinois.edu/">Lingming Zhang</a>.</span></li>
  <li><span class="hl__date">Jul 2026</span><span>Released <a href="https://arxiv.org/abs/2607.20911"><strong>Tencent WorkBuddy Bench</strong></a> — 260 contamination-resistant real-world tasks across Code, Web, Office, and Security.</span></li>
  <li><span class="hl__date">Jun 2026</span><span><a href="https://arxiv.org/abs/2509.01055"><strong>VerlTool</strong></a> accepted to <a href="https://jmlr.org/tmlr/">TMLR</a>, after winning the <span class="hl__award">Best Paper Award</span> at the <a href="https://spoticlr.github.io/">SPOT Workshop @ ICLR 2026</a>.</span></li>
  <li><span class="hl__date">May 2026</span><span><a href="https://arxiv.org/abs/2605.26494"><strong>MiniMax-M2</strong></a> tech report released — <strong>69% Pass@1 on SWE-bench Verified</strong>, #2 on MultiSWE and TerminalBench.</span></li>
  <li><span class="hl__date">May 2026</span><span><a href="https://arxiv.org/abs/2603.16124"><strong>SWE-QA-Pro</strong></a> accepted to <strong>ACL 2026 Findings</strong> — our trained 8B model surpasses GPT-4o on repository-level QA.</span></li>
</ul>

## Experience

**Tencent** (Feb – Jul 2026) — Intern (Qingyun Program) on **WorkBuddy**, working closely with [Ke Li](https://keli.info/) and [Chao Peng](https://chao-peng.github.io/). I owned the team's evaluation platform and infrastructure (**3× evaluation volume**), explored task synthesis from production user data, and co-led the release of [**Tencent WorkBuddy Bench**](https://arxiv.org/abs/2607.20911) ([code](https://github.com/Tencent/workbuddy-bench)).

**MiniMax** (May 2025 – Feb 2026) — Research Scientist Intern on the base model team, contributing to code-agent post-training for [**MiniMax M1 through M2.5**](https://arxiv.org/abs/2605.26494). M2 achieved **69% Pass@1 on SWE-bench Verified** (#2 on MultiSWE and TerminalBench). I led large-scale SWE data synthesis (**36K verifiable tasks** from 5K+ sandbox environments), built the rubric-based evaluation benchmark that became the team's core metric for code-agent user experience, and led CodeMirror ToolScaling for M2.5.

## Research

### Agentic Post-Training

I work on the full pipeline of post-training for software engineering agents. As a co-first author and core developer of [**VerlTool**](https://github.com/TIGER-AI-Lab/verl-tool) (TMLR 2026; **Best Paper Award** at the [SPOT Workshop @ ICLR 2026](https://spoticlr.github.io/)), I built the stateful environment interaction protocol and SWE agent training pipeline. In my master's thesis, RLVR training took Qwen3-8B from **10.4% to 19.5%** on SWE-bench Verified. I also co-first authored [**SWE-Next**](https://arxiv.org/abs/2603.20691) (2,308 verifiable tasks mined from 311 real repositories) and [**BrowserAgent**](https://arxiv.org/abs/2502.01882) (TMLR).

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
    <a class="pub__title" href="{% if pub.paperurl and pub.paperurl != '' %}{{ pub.paperurl }}{% else %}{{ base_path }}{{ pub.url }}{% endif %}">{{ pub.title }}</a>
    <p class="pub__excerpt">{{ pub.excerpt | markdownify | strip_html | strip_newlines }}</p>
    <p class="pub__meta">{% if pub.venue and pub.venue != '' %}<span class="pub__venue">{{ pub.venue }}</span> &middot; {% endif %}{{ pub.date | date: "%Y" }}</p>
  </div>
{% endfor %}
</div>

<p class="pub-list__more"><a href="{{ base_path }}/publications/">Full publication list →</a></p>

## Contact

Feel free to reach out at `zhihenglyu.cs@gmail.com` for research collaboration, open-source projects, or mentorship.
