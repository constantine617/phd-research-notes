---
tags:
  - paper-note
  - medical
  - evaluation
  - factuality
---

# Evaluating progress in automatic chest X-ray radiology report generation

## 文献信息

- 作者：Feiyang Yu, Mark Endo, Rayan Krishnan, Ian Pan, Andy Tsai, Eduardo Pontes Reis, Eduardo Kaiser Ururahy Nunes Fonseca, Henrique Min Ho Lee, Zahra Shakeri Hossein Abad, Andrew Y. Ng, Curtis P. Langlotz, Vasantha Kumar Venugopal, Pranav Rajpurkar。
- 年份：2023。
- 发表：Patterns，4（9），100802。
- 原始来源：[官方页面](https://doi.org/10.1016/j.patter.2023.100802)。
- 开放全文：[Europe PMC](https://europepmc.org/articles/PMC10499844)。
- 作者发布的代码归档：[Zenodo](https://doi.org/10.5281/zenodo.7579952)。
- 专家评价数据：[ReXVal 1.0.0](https://physionet.org/content/rexval-dataset/1.0.0/)；使用须遵守数据访问条件。


## 研究问题与评价设计

[Yu et al. (2023)](https://doi.org/10.1016/j.patter.2023.100802 "文献引用")研究胸部 X 光报告生成：常用自动指标是否与放射科专家对错误的判断一致。论文比较文本指标与临床表征指标，提出 RadGraph F1 和复合指标 RadCliQ。

研究利用参考报告，从训练语料中为不同指标选择得分最高的候选报告，称为 metric-oracle。它是分析指标行为的工具，使用了真实部署时未知的参考报告，不能当作可直接部署的报告生成系统。

六名放射科医师比较候选与参考报告，按六类错误记录数量，并区分临床重要与不重要错误。类别包括虚构发现、遗漏发现、位置和严重程度错误，以及不受支持或遗漏的比较描述。专家与自动指标在这个实验中都主要依据报告文本，不能据此声称完成了独立图像真值验证。

## 方法与实验条件

RadGraph F1 比较报告中抽取的医学实体与关系，试图捕捉比词汇重合更有结构的信息。RadCliQ 则将 BLEU、BERTScore、CheXbert 向量相似度和 RadGraph F1 标准化，再以受约束线性模型预测专家记录的总错误数。

复合指标使用 50 个检查对应的 200 份 metric-oracle 报告数据，按论文所述划分为 160 个开发数据点与 40 个测试数据点，并在开发部分交叉验证。该划分按文中数据点描述，不能额外声称已做独立医院或独立患者外部验证。

作者报告，在所研究的专家评价中，RadGraph F1 等指标比单纯词汇重合更贴近专家判断，RadCliQ 在留出测试数据上具有较强相关性。随后评价已有报告生成模型时，论文区分 findings、impression 及联合生成任务，不能跨任务分组直接排列模型优劣。

## 优势与局限

作者把指标验证落实到具体临床错误类别，并公开评价资源。这比只凭指标名称判断“临床相关性”更有说服力。

本笔记的理解是，与专家相关不等于逐例无误，也不等于预测患者结局。复合指标在有限报告与候选构造分布上拟合，迁移到不同机构、任务或模型输出时需要再验证。线性预测还可能产生负值，因此 RadCliQ 应作为低值较好的评价分数解释，不能直接充当逐例真实错误计数或正确概率。

结构抽取器和参考报告也可能遗漏或出错。对原图的忠实性仍需要额外证据审查；文本指标不能单独决定临床可用性。

## 与博士研究主线的关系

这篇论文为医学幻觉和可靠性评价提供标签设计范例。将 UQ 用于报告时，可以据此区分总体错误、临床严重程度及图像支持，并与一般校准和选择性预测指标共同报告。

## 相关笔记

- [医学评价](../medical/medical-evaluation.md)
- [人工评价](../evaluation/ground-truth/human-evaluation.md)
- [临床可靠性](../medical/clinical-reliability.md)

## 参考文献

- Yu, F., Endo, M., Krishnan, R., et al. (2023). *Evaluating progress in automatic chest X-ray radiology report generation*. Patterns, 4(9), 100802. [原文](https://doi.org/10.1016/j.patter.2023.100802)
