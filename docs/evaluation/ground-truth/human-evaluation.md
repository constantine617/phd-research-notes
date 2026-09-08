---
tags:
  - evaluation
  - human-evaluation
  - ground-truth
---

# 人工评价（Human Evaluation）

人工评价将任务要求和证据交给标注者判断。其优势在于能够处理语境与复杂错误，代价则包括时间、专业训练与分歧处理。不能仅写“由人类标注”，就假定标签质量已经得到保证。

## 先建立可执行指南

指南应包括评价目标、证据范围、标签定义、边界案例和无法判断规则。先做试标，集中讨论分歧，修订指南后再开始正式标注。正式阶段尽量隐藏待评模型名称与检测分数，随机化展示顺序，避免先入判断。

标注者的专业能力必须匹配任务。[Lau et al. (2018)](https://www.nature.com/articles/sdata2018251 "文献引用") 的 VQA-RAD 由临床人员围绕放射影像提出问题并提供答案，其专业来源是数据集设计的一部分。一般文本标注者不能自动替代医学专家。

## 一致率不是标签正确率

记录每项由多少人独立判断、原始一致比例、使用的一致性统计量及裁决流程。类别极不平衡时，全部选常见类别也可能得到高一致率；机会校正统计量又会受到类别比例影响。不能只报告一个汇总值而省略标签分布。

保留裁决前的独立标签。裁决后的完全一致只表示最终统一了结果，不能当作原始标注者一致性的证据。[Kwiatkowski et al. (2019)](https://aclanthology.org/Q19-1026/ "文献引用") 在 Natural Questions 中采用多重标注，并分析答案选择中的人类差异，说明多参考与分歧信息本身具有评价价值。

## 如何分配成本

可对自动标签做分层抽样复核，覆盖低置信度、证据冲突、少见类别与随机样本。若困难样本被过度抽样，应分别报告各层错误率，或按真实比例加权；不能把抽样复核集的比例直接当作全库比例。

最后记录标注耗时、培训、报酬或组织方式、专家范围与剩余争议。医学和其他高专业要求任务中的“无法判断”常反映信息缺失，应成为分析对象。

## 参考文献

- Lau, J. J., Gayen, S., Ben Abacha, A., Demner-Fushman, D. (2018). *A dataset of clinically generated visual questions and answers about radiology images*. Scientific Data, 5, 180251. [Paper](https://www.nature.com/articles/sdata2018251)
- Kwiatkowski, T., Palomaki, J., Redfield, O., et al. (2019). *Natural Questions: A Benchmark for Question Answering Research*. Transactions of the Association for Computational Linguistics, 7, 453–466. [Paper](https://aclanthology.org/Q19-1026/)
