---
layout: archive
title: "Research Statements"
permalink: /statements/
author_profile: true

---

## Future Research Directions

My main focus is on **AI for Software Engineering**, driven by three key motivations:

### Why AI for Software Engineering?

**1. Structural and Scalable Data**
Software engineering data is inherently structured, making it easier to collect, synthesize, and scale. Unlike many NLP tasks that rely on unstructured text, code repositories, issue trackers, and development workflows provide rich, multi-modal data sources that can be systematically leveraged.

**2. Real-World Impact and Practical Relevance**
Software engineering tasks are much closer to real-world applications compared to traditional NLP benchmarks. The task paradigms are still ill-defined, leaving substantial room for innovation and exploration. This practical relevance means that advances in this area can have immediate economic and social impact.

**3. Cross-Domain Generalization**
Training on code tasks has been shown to generalize and improve reasoning capabilities across many domains. This suggests that advances in AI for software engineering could unlock broader improvements in AI systems.

### Research Focus Areas

I'm interested in decomposing SWE tasks into skill-specific components using expert knowledge, then using large model techniques to help models master these specific skills. Current work mostly focuses on bug fixing (like SWE-Bench), but "vibe coding" involves much more:

- **Debugging**: Integrating real tools like GDB and interactive environments
- **Performance Optimization**: Moving beyond GPU-level benchmarks to define CPU-level performance tasks
- **Refactoring**: Addressing the 40% of refactor commits in real GitHub repos that reduce technical debt but are difficult to verify automatically
- **Test Generation**: Still quite weak but tightly related to bug fixing
- **Repository-level QA**: Common in tools like Cursor but rarely studied in academia
- **Security**: A fascinating and high-impact area I'm beginning to explore

### Technical Directions

**Mid-training vs Post-training**: I see post-training as more task-focused and closer to auto benchmark generation, while mid-training is about scalability—balancing between structured data distributions needed for post-training and scalable data that helps models generalize.

**Agentic Technologies**: SWE agents provide a perfect verifiable environment for exploring more advanced agentic technologies. Unlike information-seeking tasks (where Google Search does most of the work), software engineering has sparse rewards and complex challenges, making it ideal for testing techniques like GRM, memory systems, and multi-agent frameworks.

