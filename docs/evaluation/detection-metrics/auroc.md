---
tags:
  - evaluation
  - detection
  - auroc
---

# 受试者工作特征曲线下面积（Area Under the Receiver Operating Characteristic Curve，AUROC）

AUROC 衡量一个分数能否把正类排在负类之前。本页以错误检测为例：真实错误为正类，错误分数越大越可疑。它评价排序能力，不要求输入分数本身是概率。

## 从阈值到曲线

设阈值为 $\tau$，分数为 $s$，当 $s\geq\tau$ 时预测为正类。真正例（True Positive，TP）、假正例（False Positive，FP）、真负例（True Negative，TN）与假负例（False Negative，FN）都是该阈值下的样本数。

$$
\operatorname{TPR}(\tau)=\frac{\operatorname{TP}(\tau)}{\operatorname{TP}(\tau)+\operatorname{FN}(\tau)},
\qquad
\operatorname{FPR}(\tau)=\frac{\operatorname{FP}(\tau)}{\operatorname{FP}(\tau)+\operatorname{TN}(\tau)}
\tag{1}
\label{eq:roc-rates}
$$

真正率（True Positive Rate，TPR）是检出的错误占全部错误的比例；假正率（False Positive Rate，FPR）是被误报的正确回答占全部正确回答的比例。两类样本都必须存在。

遍历阈值，以 FPR 为横轴、TPR 为纵轴，得到受试者工作特征曲线（Receiver Operating Characteristic Curve，ROC）。从全部拒绝正类到全部判为正类，曲线覆盖两个端点。采用常见 ROC 线性插值约定时：

$$
\operatorname{AUROC}=\int_0^1\operatorname{TPR}(u)\,du
\tag{2}
\label{eq:auroc-integral}
$$

$u$ 表示 FPR，$\operatorname{TPR}(u)$ 为 ROC 上相应的真正率；并列分数通常作为一组改变阈值，线段可理解为该组的随机化决策。

## 排序解释与并列

$$
\operatorname{AUROC}
=\Pr(S^+>S^-)+\frac12\Pr(S^+=S^-)
\tag{3}
\label{eq:auroc-ranking}
$$

$S^+$ 和 $S^-$ 分别来自独立抽取的正类与负类样本。公式 $\eqref{eq:auroc-ranking}$ 将并列计为半个成功排序；经验估计对应正负样本对的平均。这一约定与常见梯形 ROC 面积一致。

AUROC 位于 $[0,1]$，越高越好。无区分能力的总体基线为 $0.5$；有限样本的随机排序会有波动。低于 $0.5$ 应首先检查分数方向与标签编码，但不能看完测试结果后再选择有利方向。

## 类别不平衡时如何解释

在正负类条件分布不变、仅类别比例变化的假设下，ROC 不随正类比例变化。但它没有直接表达检出结果的纯度。举例说，一万个正确回答中的百分之一被误报，就是一百次误报；十个错误回答全部检出，检出集合仍大多是假警报。

Saito and Rehmsmeier (2015) 对这种解释风险进行了分析。AUROC 并非因此无效，而是应与正类比例、[精确率–召回率曲线下面积（Area Under the Precision-Recall Curve，AUPRC）](auprc.md)和低误报工作点一起看。整体面积还可能掩盖真正关心的阈值区间表现。

## 实践记录

记录正类定义、分数方向、并列处理、样本数及置信区间。不要输入已经二值化的标签来代替连续分数，否则只剩一个工作点。不同回答池、不同错误类型或不同难度分布上的 AUROC 不能直接解释为检测器能力的单独变化。

## 参考文献（References）

- Saito, T., Rehmsmeier, M. (2015). *The Precision-Recall Plot Is More Informative than the ROC Plot When Evaluating Binary Classifiers on Imbalanced Datasets*. PLOS ONE, 10(3), e0118432. [Paper](https://doi.org/10.1371/journal.pone.0118432)
