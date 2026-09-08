---
tags:
  - medical
  - evaluation
  - benchmark
---

# 医学评价（Medical Evaluation）

医学评价应把任务完成、事实性（Factuality）、图像忠实性（Faithfulness to Image）和临床相关错误分开。一个整体文字相似度无法同时测量这些要求。指标公式复用[评价模块](../evaluation/index.md)，这里集中讨论标签与研究设计。

## 专家参照如何建立

选择专业背景与任务相符的标注者，规定可访问的图像、历史、报告和临床信息。保留独立标注、分歧与裁决过程。Lau et al. (2018) 的 VQA-RAD 使用临床人员提出的问题和答案，体现了专业来源在任务建立中的作用；这仍不意味着所有标签对所有使用情境都充分。

临床相关正确性可以分项评价发现、否定、部位、程度和比较。严重程度加权必须有专家依据，并说明权重与聚合方式；不能把所有错误等权统计后，自动称为临床风险。

## 自动指标需要与专家核对

Yu et al. (2023) 比较自动报告指标与放射科医生的错误评分，并提出基于实体关系匹配的 RadGraph F1 与组合指标 RadCliQ。论文的主要标注是报告对照任务，使用挑选出的高指标匹配候选报告；其结论不能无条件外推到所有生成器、医院或真实临床过程。

自动实体提取也会出错，文字语义相近也可能遗漏临床重要限定。因此可以把自动指标用于批量比较，再在独立专家子集上检查错误类别与一致程度。不要把裁判模型或报告抽取器的输出直接当作无误真值。

## 图像忠实性与数据划分

另设直接核查图像的评价，判断主张是否由实际输入支持。参考报告若使用额外背景，应区分模型可见与不可见证据。按患者或研究单位划分，防止相同个体的关联资料跨越训练与测试。

Zech et al. (2018) 的跨医院影像研究说明外部验证的必要性。评价应按中心、设备、群体和少见情况分解，并报告数量、区间与无法判断比例；小样本群体的结论应受到相应限制。

## 人机共同使用

Vasey et al. (2022) 的 DECIDE-AI 要求关注早期临床使用、人因与安全。模型单独评价之外，应研究使用者在有无模型辅助条件下的行为、错误、耗时与复核负担，明确比较设计及系统版本。

置信度与拒答策略应同时报告校准、排序、[选择性风险](../evaluation/selective-prediction/risk-coverage.md)及转交成本。最终评价对象是指定任务中的完整流程；高模型分数本身不证明流程有效。

## 参考文献（References）

- Lau, J. J., Gayen, S., Ben Abacha, A., Demner-Fushman, D. (2018). *A dataset of clinically generated visual questions and answers about radiology images*. Scientific Data, 5, 180251. [Paper](https://www.nature.com/articles/sdata2018251)
- Yu, F., Endo, M., Krishnan, R., et al. (2023). *Evaluating progress in automatic chest X-ray radiology report generation*. Patterns, 4(9), 100802. [Paper](https://doi.org/10.1016/j.patter.2023.100802) · [全文](https://europepmc.org/articles/PMC10499844)
- Zech, J. R., Badgeley, M. A., Liu, M., Costa, A. B., Titano, J. J., Oermann, E. K. (2018). *Variable generalization performance of a deep learning model to detect pneumonia in chest radiographs: A cross-sectional study*. PLOS Medicine, 15(11), e1002683. [Paper](https://doi.org/10.1371/journal.pmed.1002683)
- Vasey, B., Nagendran, M., Campbell, B., et al., the DECIDE-AI expert group (2022). *Reporting guideline for the early-stage clinical evaluation of decision support systems driven by artificial intelligence: DECIDE-AI*. Nature Medicine, 28, 924–933. [Paper](https://www.nature.com/articles/s41591-022-01772-9) · [BMJ 同期发表全文](https://europepmc.org/articles/PMC9116198)
