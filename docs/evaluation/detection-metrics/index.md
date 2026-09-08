---
tags:
  - evaluation
  - detection
---

# 检测指标

检测评价检查分数能否识别已定义的失败标签。先固定正类：本板块默认错误或幻觉为正类，分数越高表示越应被检测出来。若实际使用正确性标签或高置信度（Confidence）分数，需要明确变换方向。

## 排序与决策

- [受试者工作特征曲线下面积（Area Under the Receiver Operating Characteristic Curve，AUROC）](auroc.md) 汇总真正率（True Positive Rate，TPR）与假正率（False Positive Rate，FPR）的关系，衡量正负样本之间的排序。
- [精确率–召回率曲线下面积（Area Under the Precision-Recall Curve，AUPRC）](auprc.md) 关注检出结果中有多少真正例（True Positive，TP），对正类稀少的场景尤其有帮助。
- [阈值指标](threshold-metrics.md) 描述一个实际决策阈值下的漏检与误报。

[Saito and Rehmsmeier (2015)](https://doi.org/10.1371/journal.pone.0118432 "文献引用") 说明，类别不平衡时，仅看受试者工作特征曲线（Receiver Operating Characteristic Curve，ROC）容易忽略大量负类带来的误报负担。因此报告错误检测结果时，应同时提供正类比例、排序指标和有实际意义的工作点。

排序好不等于概率校准好。给分数做严格单调递增变换，可以保持排序，却改变每个数值的概率解释。概率是否可信，应另外看[校准指标](../calibration-metrics/index.md)。

模型整体答得更准，也可能让测试集中的错误更少、更难检测。比较检测器时优先固定回答池；比较生成系统时另外报告基础错误率，避免混合解释。

## 参考文献

- Saito, T., Rehmsmeier, M. (2015). *The Precision-Recall Plot Is More Informative than the ROC Plot When Evaluating Binary Classifiers on Imbalanced Datasets*. PLOS ONE, 10(3), e0118432. [Paper](https://doi.org/10.1371/journal.pone.0118432)
