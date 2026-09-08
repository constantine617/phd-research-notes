---
tags:
  - evaluation
  - benchmark
  - hallucination
---

# 幻觉与事实核查 Benchmark

幻觉评价必须固定参照与粒度。本页选择互补任务，分别对应世界事实、给定来源与原子主张。不要把所有任务的失败标签统一命名后，就视为同一个参考真值（Ground Truth）。

## 任务、证据与限制

| 代表资源 | 实际评价对象 | 使用边界 |
|---|---|---|
| TruthfulQA（[Lin et al., 2022](https://aclanthology.org/2022.acl-long.229/ "文献引用")） | 对常见误解相关问题生成真实且有信息量的回答 | 有意构造的难题分布；真实性与信息量应分开 |
| FEVER（[Thorne et al., 2018](https://aclanthology.org/N18-1074/ "文献引用")） | 根据 Wikipedia 证据支持、反驳或无法验证主张 | 标签受证据库与检索覆盖限制；Not Enough Info 不等于世界事实为假 |
| FRANK（[Pagnoni et al., 2021](https://aclanthology.org/2021.naacl-main.383/ "文献引用")） | 摘要相对于来源的事实错误类别 | 针对摘要任务的分类，不能原样当作所有生成错误的唯一分类 |
| FActScore（[Min et al., 2023](https://aclanthology.org/2023.emnlp-main.741/ "文献引用")） | 长文本原子事实的指定来源支持比例 | 是评价方法及相应研究资源；不衡量全部信息召回或任务完成度 |
| SelfCheckGPT 数据与评价（[Manakul et al., 2023](https://aclanthology.org/2023.emnlp-main.557/ "文献引用")） | 生成式人物介绍的句子级事实性判断 | 特定生成模型、文本领域与人工标签；自一致信号不提供独立世界证据 |

## 怎样组成一个实验组

若研究检索证据，优先明确证据库是否封闭以及检索失败怎样计分。若研究多次 sampling 信号，可在固定生成回答上评价，再核查“始终犯相同错误”的样例。若研究长文本，则同时记录长度、主张拆分与聚合方式。

检测器产生的分数不能参与真值标签生成，否则评价可能循环。对于自动标签，应增加独立人工复核子集，并报告裁判与检测器共享模型时的潜在影响。

本文使用原始论文定义说明任务，不提供跨版本排行榜。实际实验须检查公开数据许可、版本、标签与原始评分代码，并根据[幻觉标签](../ground-truth/hallucination-labels.md)记录差异。

## 参考文献

- Lin, S., Hilton, J., Evans, O. (2022). *TruthfulQA: Measuring How Models Mimic Human Falsehoods*. ACL, 3214–3252. [Paper](https://aclanthology.org/2022.acl-long.229/)
- Thorne, J., Vlachos, A., Christodoulopoulos, C., Mittal, A. (2018). *FEVER: a Large-scale Dataset for Fact Extraction and VERification*. NAACL-HLT, 809–819. [Paper](https://aclanthology.org/N18-1074/)
- Pagnoni, A., Balachandran, V., Tsvetkov, Y. (2021). *Understanding Factuality in Abstractive Summarization with FRANK: A Benchmark for Factuality Metrics*. NAACL-HLT, 4812–4829. [Paper](https://aclanthology.org/2021.naacl-main.383/)
- Min, S., Krishna, K., Lyu, X., Lewis, M., Yih, W., Koh, P. W., Iyyer, M., Zettlemoyer, L., Hajishirzi, H. (2023). *FActScore: Fine-grained Atomic Evaluation of Factual Precision in Long Form Text Generation*. EMNLP, 12076–12100. [Paper](https://aclanthology.org/2023.emnlp-main.741/)
- Manakul, P., Liusie, A., Gales, M. J. F. (2023). *SelfCheckGPT: Zero-Resource Black-Box Hallucination Detection for Generative Large Language Models*. EMNLP, 9004–9017. [Paper](https://aclanthology.org/2023.emnlp-main.557/)
