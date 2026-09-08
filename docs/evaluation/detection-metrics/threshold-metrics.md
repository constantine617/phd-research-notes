---
tags:
  - evaluation
  - detection
---

# 阈值指标

阈值指标描述一个固定决策规则的实际误报与漏检。本页把错误回答设为正类，分数不小于阈值时报警。真正例、假正例、真负例与假负例都相对于这一正类定义。

## 常用指标

$$
\begin{aligned}
\operatorname{Accuracy}&=\frac{TP+TN}{TP+FP+TN+FN},&
\operatorname{Precision}&=\frac{TP}{TP+FP},\\
\operatorname{Recall}&=\operatorname{Sensitivity}=\frac{TP}{TP+FN},&
\operatorname{Specificity}&=\frac{TN}{TN+FP},\\
F_1&=\frac{2TP}{2TP+FP+FN}.
\end{aligned}
\tag{1}
\label{eq:threshold-metrics}
$$

准确率（Accuracy）是全部判定中正确的比例；精确率关注报警纯度；召回率也称敏感度（Sensitivity），关注漏检；特异度（Specificity）关注负类被正确放行的比例；$F_1$ 为精确率与召回率的调和平均。这里 $TP$ 等符号均表示计数，所有比例通常在 $[0,1]$，越高越好。

零分母时应写明 `zero_division` 等实现约定，必要时报告未定义。把未定义值设为零是软件处理政策，不是数学等式。$F_1$ 不使用 $TN$，不能概括全部决策后果。

## 阈值怎样选择

阈值应由验证集及预先说明的目标确定，例如限制误报率或满足最低召回率。测试集只用于评估这个已固定规则。若逐测试集调到最高 $F_1$，报告的是事后最优阈值表现，不能当作可部署结果。

错误稀少时，始终预测“没有错误”也能得到高准确率。[Saito and Rehmsmeier (2015)](https://doi.org/10.1371/journal.pone.0118432 "文献引用") 说明仅依赖这类整体数字会掩盖检出性能。报告混淆矩阵、类别比例和阈值，比只给 Accuracy 更容易判断代价。

## 与基础任务准确率分开

这里的 Accuracy 是“检测器是否判对错误标签”，不是“生成模型回答是否正确”。两个数都可能称 accuracy，因此表头应注明评价对象。

实际应用中的误报与漏检成本可能不对称；仅最大化 $F_1$ 未必符合目标。若阈值引发拒答，还需评价被接受回答的风险与覆盖率，见[选择性预测](../selective-prediction/index.md)。

## 参考文献

- Saito, T., Rehmsmeier, M. (2015). *The Precision-Recall Plot Is More Informative than the ROC Plot When Evaluating Binary Classifiers on Imbalanced Datasets*. PLOS ONE, 10(3), e0118432. [Paper](https://doi.org/10.1371/journal.pone.0118432)
