---
layout: post
title: Slient Data Corruption
tags: SDC CPU GPU
---

Many of us have encountered the errors of storage media, including the slient data corruptions, but slient data errors of CPU are much less common. I have only heard about it in classes, seen it in techinical reports or papers. Although xPUs other than CPU are more error-prone, HPC and LLM can tolerate it since they can start from checkpoints when errors occurred. If we use xPUs to do data storage and data analysis, the damages caused by SDC will be much worse.

There are some research and industry work on Silent Data Corruption, esp. in recent years. Since the large scale of the cloud computing data centers and the AI data centers make the problem more visible. On 2021-02-22, Facebook published on arXiv "Silent Data Corruptions at Scale"<sup><a id="fnr.1" class="footref" href="#fn.1" role="doc-backlink">1</a></sup>. It focus on CPU, saying CPU's computations are not always accurate. Facebook starts this work since 2018. The example is a specific CPU core returns inaccurate result for power funciton in Spark/Scala. On 2023-09-19, The Adventure of the Errant Hardware<sup><a id="fnr.2" class="footref" href="#fn.2" role="doc-backlink">2</a></sup>, a blog saying about SDC found in model training. And on 2024-03-12, Silent Data Corruption Considerations For Advanced Node Designs<sup><a id="fnr.3" class="footref" href="#fn.3" role="doc-backlink">3</a></sup>. Synopsys also published a series of talks on this problem, they are 2024-05-31, Speaking Up About Silent Data Corruption - Part I by Synopsys<sup><a id="fnr.4" class="footref" href="#fn.4" role="doc-backlink">4</a></sup>, and 2024-05-31, Speaking Up About Silent Data Corruption - Part II by Synopsys<sup><a id="fnr.5" class="footref" href="#fn.5" role="doc-backlink">5</a></sup>. Then, there is a report on NVIDIA GPU by Tom's hardware, 2024-07-28, Faulty Nvidia H100 GPUs and HBM3 memory caused half of failures during LLama 3 training<sup><a id="fnr.6" class="footref" href="#fn.6" role="doc-backlink">6</a></sup>. Also on 2024-11-04, LLM training at scale<sup><a id="fnr.7" class="footref" href="#fn.7" role="doc-backlink">7</a></sup>.

One of the latest one is on 2024-11-20, from SOSP 2023/TACO 2024, Understanding Silent Data Corruption in Processors for Mitigating its Effects by Alibaba<sup><a id="fnr.8" class="footref" href="#fn.8" role="doc-backlink">8</a></sup>. It also focus on CPUs. And it starts the work from 2021, maybe it is inspired by the Facebook paper published on 2021. The findings are well organized, so I copied here:

1.  In general, 3.61% of the CPUs are identified to cause SDCs in their study.
2.  The failure rates observed during the pre-production testing period and the regular testing period amount to 3.262% and 0.348%, respectively.
3.  CPU SDCs have been identified across all micro-architectures present within Alibaba Cloud fleet. The failure rate does not decrease with newer chips.
4.  A single processor fault may exert its influence on an individual physical core or encompass all cores within the processor.
5.  Processor faults that occur before the third year of deployment contribute the majority of CPU SDCs in the production environment, revealing two necessary root causes of CPU SDCs: device faults (e.g., logic bugs and manufacturing defects) and in-field faults (e.g., transistor weakness and wearing out).
6.  CPU SDCs exhibit a substantial prevalence in particular workloads, exposing five vulnerable features, namely, arithmetic logic computation, vector operations, floating-point calculation, cache coherency, and transactional memory.
7.  CPU SDCs do not exist in isolation—processors that produce SDCs can lead to crashes simultaneously.
8.  CPU SDCs have been confirmed to affect operations on all tested data types, including integers, floating-point numbers, bytes, and more. Notably, operations related to floating-point numbers demonstrate heightened vulnerability to CPU SDCs.
9.  Different processor faults lead to distinct bitflip distributions, which can be categorized as four types: center-gathered, end-gathered, specific-bits, and uniform.
10. Some bitflips are position-correlated, i.e., in some settings different inputs manifest bitflips at several fixed positions.
11. For floating-point numbers, bitflips predominantly occur in the fractional part, resulting in minor precision losses.
12. Some CPU SDCs are highly reproducible, resulting in large impact on applications.
13. Among those less-reproducible SDCs, temperature serves as an important SDC triggering condition.
14. In a production environment with tens of thousands of CPUs, 560 out of the 633 testcases have not detected any errors.
15. The effectiveness of existing fault-tolerance techniques is diminished when confronted with CPU SDCs.
16. Due to the inefficient diagnosis and handling strategies, CPU SDCs introduce significant human costs and the risk of irreparable failures.

However, despite all SDC tests, they still encounter CPU SDC issues that affect Alibaba Cloud services.

Living in a error-prone world, we system designers have to mitigate the issues raised by ill-functional hardwares.

## Footnotes

<sup><a id="fn.1" class="footnum" href="#fnr.1">1</a></sup> <https://arxiv.org/pdf/2102.11245>

<sup><a id="fn.2" class="footnum" href="#fnr.2">2</a></sup> <https://www.adept.ai/blog/sherlock-sdc>

<sup><a id="fn.3" class="footnum" href="#fnr.3">3</a></sup> <https://semiengineering.com/silent-data-corruption-considerations-for-advanced-node-designs/>

<sup><a id="fn.4" class="footnum" href="#fnr.4">4</a></sup> <https://www.youtube.com/watch?v=lGBRFq2RW3k>

<sup><a id="fn.5" class="footnum" href="#fnr.5">5</a></sup> <https://www.youtube.com/watch?v=-N0DIxuqfoI>

<sup><a id="fn.6" class="footnum" href="#fnr.6">6</a></sup> <https://www.tomshardware.com/tech-industry/artificial-intelligence/faulty-nvidia-h100-gpus-and-hbm3-memory-caused-half-of-the-failures-during-llama-3-training-one-failure-every-three-hours-for-metas-16384-gpu-training-cluster>

<sup><a id="fn.7" class="footnum" href="#fnr.7">7</a></sup> <https://www.glennklockwood.com/garden/llm-training-at-scale>

<sup><a id="fn.8" class="footnum" href="#fnr.8">8</a></sup> <https://dl.acm.org/doi/10.1145/3690825>
