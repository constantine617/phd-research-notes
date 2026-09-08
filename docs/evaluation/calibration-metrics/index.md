---
tags:
  - evaluation
  - calibration
---

# 校准（Calibration）指标

校准评价分数所表达的概率是否与相应事件的观察频率匹配。使用前先定义事件：是预测类别正确、整段回答完全正确，还是某项主张被来源支持。概念与校准方法见[置信度校准](../../calibration/index.md)。

## 三种指标分别评价什么？

- [期望校准误差](expected-calibration-error.md) 按分箱比较平均置信度（Confidence）与正确率，直观但依赖分箱与样本。
- [Brier Score](brier-score.md) 评价概率向量与结果之间的平方误差，是概率预测的整体质量分数。
- [负对数似然（Negative Log-Likelihood，NLL）](../../foundations/probability-information-theory/negative-log-likelihood.md) 对实际结果被赋予的低概率施加对数损失；本模块复用既有定义。

[Guo et al. (2017)](https://proceedings.mlr.press/v70/guo17a.html "文献引用") 讨论分箱校准误差与 负对数似然（Negative Log-Likelihood，NLL） 等评价方式；Brier Score 的原始来源和缩放约定见其独立页面。NLL 与 Brier 都不是纯校准误差：其变化还受预测分辨能力影响。一个全体都预测总体正确率的模型可能在总体上校准，却不能识别哪条回答更容易错。

开放生成中还要区分答案文本的 NLL 与“答案正确”这个二元事件的对数损失。它们的样本空间不同，不能把 token NLL 直接当作正确性校准指标。

## 推荐报告方式

在独立测试数据上报告分箱图与样本量、至少一种概率评分规则、基础准确率，并按重要子群检查。校准器用独立验证数据拟合。换任务、模型版本或生成设置后，应重新检验；原条件下的校准不是迁移保证。

## 参考文献

- Guo, C., Pleiss, G., Sun, Y., Weinberger, K. Q. (2017). *On Calibration of Modern Neural Networks*. ICML, Proceedings of Machine Learning Research, 70, 1321–1330. [Paper](https://proceedings.mlr.press/v70/guo17a.html)
