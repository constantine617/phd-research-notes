---
tags:
  - uq
  - uncertainty
---

# 不确定性量化（Uncertainty Quantification，UQ）

UQ 是当前 PhD 的主要技术主线：从模型生成过程、回答及证据中提取信号，估计输出的未确定程度或可靠程度，并研究这些信号能否支持错误识别与后续处置。这里关注大语言模型（Large Language Model，LLM），同时保留通向大型视觉语言模型（Large Vision-Language Model，LVLM）与医疗应用的接口。长期范围仍是[可信模型研究](../hallucination/trustworthiness-overview.md)。

## 本模块解决什么问题

面对一个回答，除了判定它对不对，还可以询问：模型是否反复给出不同含义？关键内容是否得到证据支持？某个分数是否足以让系统选择继续回答、检索或交给人工？这些问题需要不同的估计对象。Geng et al. (2024) 梳理了概率、内部状态、语言表达和一致性等路线；本模块在此基础上按信号来源、访问能力及计算代价组织知识。

UQ 不等于[幻觉检测（Hallucination Detection）](../hallucination/detection.md)。检测需要明确的失败标签，UQ 提供的信号还可能用于识别歧义、选择性回答或资源分配。语义熵（Semantic Entropy）能反映生成含义的变化，但持续生成相同错误时也可能很低（Farquhar et al., 2024）。

[正确性（Correctness）](../hallucination/reliability-correctness.md)由任务规则确定；[置信度校准（Confidence Calibration）](../calibration/index.md)则检查分数所表达的概率与观察到的正确频率是否匹配。只有明确了事件、群体与验证条件，才能讨论把分数解释为概率。一个任意 UQ 分数并不自动具有这种含义。

## 阅读路径

先读[总览](uq-overview.md)和[分类](uq-taxonomy.md)，确定想估计的对象。随后根据实际能够取得的信号选择方法，而不是只按方法名称归类。

| 阅读主题 | 页面 |
|---|---|
| 概念边界 | [LLM 中的不确定性](foundations/uncertainty-in-llms.md)、[认知与数据不确定性](foundations/epistemic-aleatoric.md)、[置信度与不确定性](foundations/confidence-vs-uncertainty.md)、[不确定性与正确性](foundations/uncertainty-vs-correctness.md) |
| 概率信息 | [基于概率的 UQ](probability-based/index.md) |
| 多次生成 | [Sampling 总览](sampling-based/index.md)、[自一致性（Self-Consistency）](sampling-based/self-consistency.md)、[预测熵（Predictive Entropy）](sampling-based/predictive-entropy.md)、[语义熵](sampling-based/semantic-entropy.md) |
| 语言自报 | [Verbal Confidence](verbal-confidence/index.md) |
| 分数的概率解释 | [校准总览](../calibration/index.md)、[方法类别](../calibration/calibration-methods.md)、[后处理校准](../calibration/post-hoc-calibration.md) |
| 当前技术兴趣 | [高效 UQ](../efficient-uq/index.md)、[表征方法](../representation-uq/index.md)、[Hidden State 与不确定性](../representation-uq/hidden-state-uncertainty.md)、[证据感知 UQ](evidence-aware/index.md) |

方法比较同时考察信号是否有用、数值如何解释，以及获得信号所付出的全部代价。本模块建立这些方法知识；具体评估指标、完整多模态方法与医疗任务留在各自后续模块。

## 参考文献（References）

- Geng, J., Cai, F., Wang, Y., Koeppl, H., Nakov, P., Gurevych, I. (2024). *A Survey of Confidence Estimation and Calibration in Large Language Models*. NAACL-HLT, 6577–6595. [Paper](https://aclanthology.org/2024.naacl-long.366/)
- Farquhar, S., Kossen, J., Kuhn, L., Gal, Y. (2024). *Detecting hallucinations in large language models using semantic entropy*. Nature, 630, 625–630. [Paper](https://www.nature.com/articles/s41586-024-07421-0)
