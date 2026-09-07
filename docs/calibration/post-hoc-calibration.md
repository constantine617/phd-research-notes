---
tags:
  - uq
  - calibration
  - confidence
---

# 后处理校准（Post-hoc Calibration）

后处理校准是在模型训练完成后，用一个转换调整置信度（Confidence）分数。若生成回答已经固定，该转换可以仅作用于分数，不必重训生成模型。它改善的是数值与目标事件的关系；是否成功，需要在独立数据上检查。

对于大语言模型（Large Language Model，LLM）的不确定性量化（Uncertainty Quantification，UQ），可以校准一个正确性评分器、某类有限候选的概率，或语言自报的概率。三者的输入与事件不同，应先确定对象再选转换。

## 温度缩放（Temperature Scaling）

Guo et al. (2017) 使用一个正标量温度调整分类 logits。设固定分类问题有 $J$ 个类别，logits 为 $z_1,\ldots,z_J$，则校准后的类别概率为：

$$
q_j=\frac{\exp(z_j/T_{\mathrm{cal}})}{\sum_{i=1}^{J}\exp(z_i/T_{\mathrm{cal}})},\qquad T_{\mathrm{cal}}>0
\tag{1}
\label{eq:posthoc-temperature-scaling}
$$

$q_j$ 是第 $j$ 类的调整后概率，$T_{\mathrm{cal}}$ 在有标签的校准集上拟合。增大温度使固定 logits 的分布更平，减小温度使其更尖。公式 $\eqref{eq:posthoc-temperature-scaling}$ 不改变该样本的最大 logit 所对应的类别；但在多类别情形下，不同样本的最大概率排序仍可能改变。

如果这里的类别是“回答正确或错误”，需要有对应的分类器 logits。如果类别是词表 token，则只是在调整 token 分布。两者使用相似公式，不意味着校准了同一个事件。

## 与 Decoding 温度的区别

Decoding 温度在选择下一 token 前修改生成分布，从而可能改变回答内容、长度与多样性。校准温度则针对所定义预测分数拟合，使其具有更合适的概率解释。两者可以在数学上都出现除以温度，但作用位置、拟合依据和目的不同。

降低 sampling 温度让答案更一致，不足以证明校准更好。若把拟合出来的温度重新用于生成，回答分布已经改变，便需要重新评价。相反，只调整固定回答的正确性分数，不要求重新生成回答。

## Platt 式缩放与保序回归

Platt 式缩放（Platt-style Scaling）用带斜率和截距的 sigmoid 映射一个标量分数。它可以同时修正偏移与尺度，适合二元事件；具体能否保留排序取决于斜率方向及所施加约束。不能把原来“越大越危险”的分数当作“越大越正确”直接拟合后仍沿用旧解释。

保序回归（Isotonic Regression）学习单调的非参数映射，在给定单调关系假设下适应更灵活的形状。它常产生分段常数输出，因此可能引入并列分数，也需要足够校准样本，尤其是分数两端的覆盖。Guo et al. (2017) 对这些经典方法作了统一讨论。

没有一种映射能凭空恢复原分数完全丢失的信息。例如所有错误和正确回答都得到同一原分数，后处理可以学到总体正确率，却不能据此把具体错误排出来。加入额外特征可能改善区分，但这时已超出单纯标量变换。

## 使用流程与迁移限制

先固定回答生成流程和正确性标准，再收集校准数据。拟合转换后，在独立数据上同时检查概率解释与错误区分，必要时查看不同题型或长度群体。参数选择不能反复使用最终测试结果。

Tian et al. (2023) 展示了将自报置信度与温度缩放结合的具体做法，说明后处理并不限于原始生成 logits。但一个任务上有效的映射不应直接宣称适用于新 checkpoint、新 prompt 或新增证据后的回答。

当前研究可把简单温度或标量映射作为校准基线，并记录拟合数据、信号方向与作用对象。部署时成本通常较低，标签收集、校准拟合和分布变化后的更新仍是完整成本的一部分。

## 相关笔记（Related Notes）

- [校准总览](index.md)
- [Decoding 与 Sampling](../foundations/language-models/decoding-sampling.md)

## 参考文献（References）

- Guo, C., Pleiss, G., Sun, Y., Weinberger, K. Q. (2017). *On Calibration of Modern Neural Networks*. ICML, Proceedings of Machine Learning Research, 70, 1321–1330. [Paper](https://proceedings.mlr.press/v70/guo17a.html)
- Tian, K., Mitchell, E., Zhou, A., Sharma, A., Rafailov, R., Yao, H., Finn, C., Manning, C. D. (2023). *Just Ask for Calibration: Strategies for Eliciting Calibrated Confidence Scores from Language Models Fine-Tuned with Human Feedback*. EMNLP, 5433–5442. [Paper](https://aclanthology.org/2023.emnlp-main.330/)
