---
tags:
  - uq
  - calibration
  - confidence
---

# 置信度校准（Confidence Calibration）

置信度校准关注分数表达的概率是否与相应事件的发生频率匹配。例如，若分数明确表示“回答正确的概率”，在大量条件可比、分数接近八成的回答中，应观察到接近八成正确。Guo et al. (2017) 给出了现代分类模型中广泛使用的校准表述；用于大语言模型（Large Language Model，LLM）时，还需明确开放生成中的目标事件。

## 固定正确性事件与置信度

令 $C\in\{0,1\}$ 表示回答是否满足已经固定的任务标准，$S\in[0,1]$ 为对 $C=1$ 所给的置信度。相对于指定数据与生成流程的联合分布，理想校准可写为：

$$
\Pr(C=1\mid S=s)=s
\tag{1}
\label{eq:confidence-calibration-condition}
$$

$s$ 是置信度可能取得的数值；条件对该分布支持上的几乎所有 $s$ 成立。连续分数的条件概率应按条件分布理解，实际有限数据通常需要邻近分数组、分箱或其他估计方式，而不是指望每个精确数值都出现大量样本。

公式 $\eqref{eq:confidence-calibration-condition}$ 是群体层面的概率关系，不保证某一条回答。它也没有直接约束某个分数下每种题型、长度或领域都同样校准。总体匹配可能掩盖子群体差异，因此必须说明声明覆盖哪个群体。

## 与区分能力的区别

区分能力（Discrimination）关心正确与错误回答能否被分数排开。校准关心数值表达是否准确。一个系统如果对所有回答都报八成，而实际整体正确率恰好八成，可以满足这一总体校准关系，却完全不能指出哪条更可能出错。

反过来，一个分数可以很好地把错误排在前面，但数值系统性过高。若对该标量采用适当的严格单调变换，排序可以保留，概率解释却发生变化。后处理校准（Post-hoc Calibration）常利用这一点，但一般多类别 logits 变换不保证所有样本间的置信度排序都保持不变。

因此，不确定性量化（Uncertainty Quantification，UQ）方案若要支持错误检测和概率呈现，需要分别验证两项目标。Xiong et al. (2024) 在 LLM 置信度表达研究中也观察到，改善校准并不意味着失败预测已经有效。

## 开放生成中的三个选择

第一，选择正确性标准。短答案事实正确、推导有效、主张得到来源支持或整段完全满足要求，都可以构成不同的 $C$。定义应沿用[任务正确性](../hallucination/reliability-correctness.md)，并处理多个有效答案和无法判断的样本。

第二，选择评分粒度。token 预测被校准，不等于序列正确性被校准；每条主张有合适的支持概率，也不等于整段全部正确概率已知。把局部分数相乘还需要依赖关系等额外假设。

第三，选择置信度来源。它可以来自模型概率、[语言自报](../uncertainty/verbal-confidence/index.md)或独立评分器。Tian et al. (2023) 比较了具体问答设置中的概率和自报分数，说明来源不同可能产生不同的校准行为。没有必要预先把某一种来源认定为“天然概率正确”。

## 校准集与使用条件

用于拟合映射的数据称为校准集（Calibration Set）。它必须代表目标使用条件，并与最终评价数据区分。若在测试数据上选择温度、分组或阈值，所得结果不能作为独立验证。选择 prompt 或内部特征层也属于数据驱动选择，应纳入同一边界。

分布变化（Distribution Shift）可能破坏原有关系。例如换成更难题型、改变回答长度、允许检索或更新 checkpoint，置信度与正确性之间的条件关系都可能变化。Guo et al. (2017) 的经典后处理讨论假定训练、验证与测试来自同分布；该前提不能在部署时被默默忽略。

## 如何使用校准后的信息

校准后的分数可以支持向人工解释风险，或配合成本与损失作决策。但它不是事实证明，也不自动给出某个高风险应用的可接受阈值。追加检索、拒答或答案重写还会改变输出群体，决策后的校准应按实际交付对象检查。

当前研究同时关注概率解释、错误区分和计算成本。校准页面负责分清目标与方法条件；具体评价指标与实验教程由后续评估模块承接。

## 相关笔记（Related Notes）

- [校准方法](calibration-methods.md)
- [后处理校准](post-hoc-calibration.md)

## 参考文献（References）

- Guo, C., Pleiss, G., Sun, Y., Weinberger, K. Q. (2017). *On Calibration of Modern Neural Networks*. ICML, Proceedings of Machine Learning Research, 70, 1321–1330. [Paper](https://proceedings.mlr.press/v70/guo17a.html)
- Xiong, M., Hu, Z., Lu, X., Li, Y., Fu, J., He, J., Hooi, B. (2024). *Can LLMs Express Their Uncertainty? An Empirical Evaluation of Confidence Elicitation in LLMs*. ICLR. [Paper](https://proceedings.iclr.cc/paper_files/paper/2024/hash/6733cf15e10e2cd1d59af033c3bb8507-Abstract-Conference.html)
- Tian, K., Mitchell, E., Zhou, A., Sharma, A., Rafailov, R., Yao, H., Finn, C., Manning, C. D. (2023). *Just Ask for Calibration: Strategies for Eliciting Calibrated Confidence Scores from Language Models Fine-Tuned with Human Feedback*. EMNLP, 5433–5442. [Paper](https://aclanthology.org/2023.emnlp-main.330/)
