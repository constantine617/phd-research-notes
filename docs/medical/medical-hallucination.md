---
tags:
  - medical
  - hallucination
  - faithfulness
---

# 医学幻觉（Medical Hallucination）

医学幻觉包括医学生成内容中缺乏相应证据或与参照冲突的主张。判断时必须明确任务与信息范围：世界医学知识、当前图像、临床背景和参考报告，是不同参照。

## 不把所有错误都叫幻觉

[Yu et al. (2023)](https://doi.org/10.1016/j.patter.2023.100802 "文献引用") 在胸部影像报告评价中，让放射科医生区分虚报发现、遗漏发现、位置错误、严重程度错误、无依据的比较，以及遗漏既往变化比较，并区分临床显著与不显著错误。这是其报告比较研究采用的类别，不是医学领域唯一分类，也不意味着其中每类都应统一命名为幻觉。

| 研究中需要区分的现象 | 判定重点 |
|---|---|
| 无依据的发现或诊断主张 | 现有图像与允许背景是否支持该断言 |
| 属性、部位或程度错误 | 否定、位置、数量与程度是否改变医学含义 |
| 领域知识错误 | 医学知识主张是否符合指定可靠来源 |
| 遗漏关键发现或证据 | 属于完整性或敏感度问题，不能默认与新增无依据内容同类 |
| 不适当的时间比较 | 是否确实提供既往检查及可比较信息 |

## 图像支持与报告一致性

[Yu et al. (2023)](https://doi.org/10.1016/j.patter.2023.100802 "文献引用") 的主要专家标注比较候选报告与参考报告。参考一致性有助于研究自动指标，却不等于对每句内容进行了独立图像核查。

[Johnson et al. (2019)](https://www.nature.com/articles/s41597-019-0322-0 "文献引用") 说明，临床报告可能使用模型未获提供的背景与既往影像。如果当前输入没有这些信息，模型即便生成了恰好与参考相同的比较语句，也未必具有相应证据。反之，参考报告没有提及的内容，需要专家检查后才能判为错误。

## 保留不确定性表达

“不能排除”“可能存在”和明确肯定，在医学报告中可能承担不同含义。主张拆分、文字归一化和自动标签提取不能静默删除这些限定。把不确定陈述改写为确定发现，会改变评价事件。

模型用专业术语和自信语气表达，不说明事实已经核验。标签应保留原始跨度、证据范围、错误类别、严重程度与专家分歧；必要时单列无法判断。

本研究方向关注如何识别和减少这些失败，并不根据模型生成内容给出个体医学判断。

## 相关笔记

- [医学评价](medical-evaluation.md)
- [通用幻觉标签](../evaluation/ground-truth/hallucination-labels.md)

## 参考文献

- Yu, F., Endo, M., Krishnan, R., et al. (2023). *Evaluating progress in automatic chest X-ray radiology report generation*. Patterns, 4(9), 100802. [Paper](https://doi.org/10.1016/j.patter.2023.100802) · [全文](https://europepmc.org/articles/PMC10499844)
- Johnson, A. E. W., Pollard, T. J., Berkowitz, S. J., et al. (2019). *MIMIC-CXR, a de-identified publicly available database of chest radiographs with free-text reports*. Scientific Data, 6, 317. [Paper](https://www.nature.com/articles/s41597-019-0322-0)
