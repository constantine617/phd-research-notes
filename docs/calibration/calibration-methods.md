---
tags:
  - uq
  - calibration
  - confidence
---

# 校准方法（Calibration Methods）

校准方法试图改善置信度（Confidence）与目标事件发生频率的匹配。对于大语言模型（Large Language Model，LLM）的不确定性量化（Uncertainty Quantification，UQ），方法可以改变生成模型、改变置信表达，也可以在生成完成后学习分数映射。这些操作不在同一位置，不能仅按“校准方法”名称假定其作用相同。

Geng et al. (2024) 分别讨论生成概率、语言表达与其他置信度路线。本页据此作面向使用条件的工作分组（Working Grouping），各组可以重叠，不是互斥的领域标准。

## 方法类别与干预位置

| 方法类别 | 调整什么 | 主要数据与访问要求 |
|---|---|---|
| 训练阶段校准（Training-time Calibration） | 参数、训练目标或概率表达行为 | 可训练模型；目标标签或适当监督信号 |
| 后处理校准（Post-hoc Calibration） | 固定回答及分数上的转换 | 校准集；所需原始分数或 logits |
| 语言自报校准（Verbal-confidence Calibration） | prompt、概率表达训练或自报数值映射 | 文本接口即可开始；验证仍需目标标签 |
| 外部信号映射（External-signal Mapping） | 将检索或核查结果映射为目标概率 | 证据、核查器及相应标签 |
| 基础模型或辅助信号（Base-model / Auxiliary Signal） | 引入其他模型分数或特征 | 额外 checkpoint、调用或特征提取 |
| 任务特定校准（Task-specific Calibration） | 按任务、粒度或子群体拟合 | 足够的目标任务校准数据 |

训练阶段的方法可能同时改变回答质量和置信表达。后处理方法如果只对已固定回答附加新分数，通常不改变这条回答，但可能改变后续弃答或路由结果。比较时需要分别记录生成质量、分数质量和最终决策行为。

## 已验证的代表性思路

Guo et al. (2017) 研究温度缩放（Temperature Scaling）等后处理方法，重点是分类预测概率的调整。它为构造轻量映射提供基线；开放生成的正确性目标和分数输入还需要重新明确，不能把词表概率校准直接当成回答事实性校准。

Lin et al. (2022) 通过 fine-tuning 训练模型以语言表达自身答案的不确定性，说明置信表达可以成为学习目标。Tian et al. (2023) 通过 prompting 获取自报分数并结合后处理，展示文本接口下也可以研究校准。两者的训练要求和部署时成本不同。

外部核查或基础模型分数可以提供新的输入特征，但“多一个信号”不等于“已校准”。若它帮助预测正确性，仍需在目标数据上检查数值关系。辅助模型还可能共享训练偏差，或者因任务不同而产生不可直接比较的分数。

## 有监督与无监督的边界

监督校准依赖已定义事件的标签，标签可以是人工判断、可核验答案或明确规则生成的结果。标签噪声会传入映射：若核查器将缺证据当成错误，学习出的分数也会继承这一混合目标。

无需目标正确性标签的方法可以用一致性、内部概率或其他代理信号改变评分。它们可能有用，但没有标签不意味着已经证明真实正确率匹配。若所谓无监督方法使用预训练核查器，还应区分“不需要当前任务标签”和“整个组件从未接受监督”。

黑盒（Black-box）或白盒（White-box）标签只粗略描述访问。文本接口可支持自报分数的映射；温度缩放可能要求目标分类 logits；表征映射需要 hidden state。训练阶段能访问的信息与部署阶段真正用到的信息应分开报告。

## 校准集怎样影响方法选择

简单参数映射需要估计的参数少，但可能无法表达复杂的误校准形状。更灵活的非参数方法需要足够样本覆盖分数范围。按领域分别校准能够反映差异，却使每组样本更少；不能只因总体数据多就忽略子群体的数据不足。

模型和 prompt 选择、特征筛选、校准器拟合及最终检验应有清楚的数据分工。校准后的输出若参与筛选，再只在筛选群体中报告结果，需要说明这与原始群体的不同。

当前研究先从可解释、低成本的映射开始，再检验新增信号的价值。分布变化后是否仍可迁移、是否改善错误排序、是否增加生成开销，都需要分别回答，而不能只看某个单一校准结果。

## 相关笔记（Related Notes）

- [校准总览](index.md)
- [后处理校准](post-hoc-calibration.md)
- [证据感知 UQ](../uncertainty/evidence-aware/index.md)

## 参考文献（References）

- Geng, J., Cai, F., Wang, Y., Koeppl, H., Nakov, P., Gurevych, I. (2024). *A Survey of Confidence Estimation and Calibration in Large Language Models*. NAACL-HLT, 6577–6595. [Paper](https://aclanthology.org/2024.naacl-long.366/)
- Guo, C., Pleiss, G., Sun, Y., Weinberger, K. Q. (2017). *On Calibration of Modern Neural Networks*. ICML, Proceedings of Machine Learning Research, 70, 1321–1330. [Paper](https://proceedings.mlr.press/v70/guo17a.html)
- Lin, S., Hilton, J., Evans, O. (2022). *Teaching Models to Express Their Uncertainty in Words*. Transactions on Machine Learning Research. [作者版本](https://arxiv.org/abs/2205.14334)
- Tian, K., Mitchell, E., Zhou, A., Sharma, A., Rafailov, R., Yao, H., Finn, C., Manning, C. D. (2023). *Just Ask for Calibration: Strategies for Eliciting Calibrated Confidence Scores from Language Models Fine-Tuned with Human Feedback*. EMNLP, 5433–5442. [Paper](https://aclanthology.org/2023.emnlp-main.330/)
