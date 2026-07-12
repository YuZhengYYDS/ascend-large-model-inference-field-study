# On the Limitations of Non-GPU AI Accelerators for Large-Model Inference

### A Field Study of MoE and Multimodal Serving on Huawei Ascend

[![Paper](https://img.shields.io/badge/Paper-PDF-b31b1b.svg)](https://arxiv.org/pdf/2607.08215)
[![Project Page](https://img.shields.io/badge/Project-Page-19324a.svg)](https://yuzhengyyds.github.io/ascend-large-model-inference-field-study/)
[![arXiv](https://img.shields.io/badge/arXiv-2607.08215-b31b1b.svg)](https://arxiv.org/abs/2607.08215)
[![HTML](https://img.shields.io/badge/arXiv-HTML-255492.svg)](https://arxiv.org/html/2607.08215)

**Zheng Yu**  
School of Data Science, The Chinese University of Hong Kong, Shenzhen  
[zheng.yu3@mail.mcgill.ca](mailto:zheng.yu3@mail.mcgill.ca)

This repository accompanies a field study of deploying frontier-scale Mixture-of-Experts and multimodal inference workloads on a 16-device Huawei Ascend 910 system with CANN and vLLM-Ascend. It documents the integration work, correctness trade-offs, device faults, scalability ceilings, and operational lessons that are often missing from vendor documentation.

<p align="center">
  <a href="https://yuzhengyyds.github.io/ascend-large-model-inference-field-study/">
    <img src="docs/assets/paper-overview.png" alt="Overview of the model architecture, benchmark quality, and serving throughput results" width="900">
  </a>
</p>

## At a glance

| 16 accelerators | 12 source patches | 8 limitation classes | ~4-stream sweet spot |
|:---:|:---:|:---:|:---:|
| Ascend 910 devices | Required for serviceability | From stack maturity to portability | Peak observed aggregate throughput |

## What this study finds

- **Feature combinations expose the weakest paths.** Pipeline parallelism, multimodal input injection, MoE routing, sparse attention, and speculative decoding interacted in ways that were not covered by the reference deployment.
- **Correctness required disabling performance features.** Sequence parallelism and fused MC2 expert routing were disabled for the multimodal path; eager execution was preferred where graph capture was unstable.
- **Low-level faults become an operational concern.** Aicore and vector-core exceptions could terminate workers under sustained load, requiring watchdogs and automatic restart.
- **More concurrency was not better.** Aggregate throughput peaked around four in-flight streams and then fell as queue depth and failures increased.
- **End-to-end quality remained verifiable.** The multimodal deployment reached 48.03% on MMMU-Pro and 57.78% on MMMU validation under the forced-direct protocol.

## Workloads

| | Value-alignment judge | Multimodal VLM |
|---|---|---|
| Model | DeepSeek-V4-Flash W8A8 MoE | DeepSeek-V4-Flash-Vision bf16 |
| Scale | ~300 GB | ~540 GB |
| Parallel layout | TP=8 × DP=2 | TP=8 × PP=2 |
| Primary stressors | MoE, DSA, MTP, sustained batch serving | Vision bridge, MoE, DSA, pipeline parallelism |
| Validation | Tens of thousands of prompts per evaluated model | MMMU and MMMU-Pro |

## Eight observed limitation classes

1. Software-stack maturity and operator/feature coverage gaps
2. Fragile multi-axis parallelism
3. Kernel-level numerical and stability faults
4. Immature graph compilation
5. Advanced-feature gaps
6. Performance and scalability ceilings
7. Operational reliability and observability
8. Ecosystem fragmentation and portability tax

## Repository contents

- [`paper/main.tex`](paper/main.tex) — LaTeX source
- [`paper/references.bib`](paper/references.bib) — bibliography
- [`docs/`](docs/) — static project page for GitHub Pages
- [`arxiv/`](arxiv/) — upload-ready arXiv source archive

## arXiv

Read the paper on [arXiv](https://arxiv.org/abs/2607.08215), in [experimental HTML](https://arxiv.org/html/2607.08215), or as a [PDF](https://arxiv.org/pdf/2607.08215). The canonical identifier is **arXiv:2607.08215** and the DOI is [10.48550/arXiv.2607.08215](https://doi.org/10.48550/arXiv.2607.08215).

## Citation

```bibtex
@article{yu2026limitations,
  title  = {On the Limitations of Non-GPU AI Accelerators for Large-Model Inference: A Field Study of MoE and Multimodal Serving on Huawei Ascend},
  author = {Yu, Zheng},
  year   = {2026},
  month  = jul,
  eprint = {2607.08215},
  archivePrefix = {arXiv},
  primaryClass = {cs.DC},
  doi = {10.48550/arXiv.2607.08215}
}
```

## License

The paper is distributed under the [Creative Commons Attribution 4.0 International license](https://creativecommons.org/licenses/by/4.0/), matching the arXiv record.

## Scope

This is an observational field study from two real deployments on one 16-device system. It is not a controlled cross-vendor benchmark, and the patches described in the paper are evidence of integration effort rather than proposed canonical fixes.
