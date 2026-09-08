---
tags:
  - evaluation
  - detection
  - auprc
---

# 精确率–召回率曲线下面积（Area Under the Precision-Recall Curve，AUPRC）

AUPRC 关注被检出的样本有多少真正属于正类，以及检出了多少正类。在错误或幻觉检测中，本页把待检出的错误设为正类；如果换成“正确回答为正类”，得到的是另一项评价，必须明确标注。

## 精确率（Precision）和召回率（Recall）

$$
\operatorname{Precision}(\tau)=\frac{\operatorname{TP}(\tau)}{\operatorname{TP}(\tau)+\operatorname{FP}(\tau)},
\qquad
\operatorname{Recall}(\tau)=\frac{\operatorname{TP}(\tau)}{\operatorname{TP}(\tau)+\operatorname{FN}(\tau)}
\tag{1}
\label{eq:precision-recall-rates}
$$

$\tau$ 是判为正类的分数阈值；$\operatorname{TP}$、$\operatorname{FP}$、$\operatorname{FN}$ 分别是真正例、假正例与假负例数。精确率表示检出集合的正类比例，召回率表示全部正类中被检出的比例。没有预测正类时精确率的绘图端点需要约定；数据完全没有正类时，不能把软件返回的数值当作有意义的检出能力。

随阈值变化，以召回率为横轴、精确率为纵轴得到精确率–召回率曲线（Precision-Recall Curve，PR Curve）：

$$
\operatorname{AUPRC}=\int_0^1 P(r)\,dr
\tag{2}
\label{eq:auprc-integral}
$$

$r$ 为召回率，$P(r)$ 为所选插值约定下的精确率。AUPRC 通常位于 $[0,1]$，同一任务和约定下越高越好。离散点之间怎样补成曲线，是数值定义的一部分。

## 平均精确率（Average Precision，AP）不是任意 PR 积分

平均精确率的一种常用非插值计算为：

$$
\operatorname{AP}=\sum_{k=1}^{K}(r_k-r_{k-1})P_k,\qquad r_0=0
\tag{3}
\label{eq:average-precision}
$$

$K$ 是不同分数阈值形成的有效步骤数，$r_k$ 与 $P_k$ 是按召回率递增排列的第 $k$ 步召回率与精确率。它用召回率增量给精确率加权。

scikit-learn 官方 `average_precision_score` 文档采用公式 $\eqref{eq:average-precision}$，明确区别于梯形积分。报告“PR-AUC”时，应写明函数、版本与插值方式；不要在不同论文中把 平均精确率 和梯形面积默认为同一数值。

## 基线随正类比例变化

若正类比例为 $\pi$，与标签独立的检出规则的总体精确率基线为 $\pi$。错误只占百分之一时，基线是百分之一，而非 $0.5$。有限样本随机排序的 平均精确率 有离散性与波动，不能要求每次都恰好等于 $\pi$。

[Saito and Rehmsmeier (2015)](https://doi.org/10.1371/journal.pone.0118432 "文献引用") 说明了 PR 基线与类别分布的关系，以及受试者工作特征曲线与 PR 插值的差异。正类稀少时，PR 更直接揭示误报导致的精确率下降；但它也更依赖正类比例。比较两个模型的错误检测 平均精确率 时，基础错误率变化本身就可能改变数值。

因此同时报告正类比例、AUROC、平均精确率 的明确计算约定，以及关心召回率处的精确率。比较多个数据集时，优先逐数据集报告，不要只给难以解释的总平均。

## 参考文献

- Saito, T., Rehmsmeier, M. (2015). *The Precision-Recall Plot Is More Informative than the ROC Plot When Evaluating Binary Classifiers on Imbalanced Datasets*. PLOS ONE, 10(3), e0118432. [Paper](https://doi.org/10.1371/journal.pone.0118432)
- scikit-learn developers（访问于 2026-09-08）. *average_precision_score*. 官方 API 文档，用于说明计算约定. [Documentation](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)
