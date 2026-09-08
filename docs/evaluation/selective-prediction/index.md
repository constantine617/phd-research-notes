---
tags:
  - evaluation
  - selective-prediction
---

# 选择性预测（Selective Prediction）

选择性预测允许系统只交付部分预测，其余拒答（Abstention）或转交其他流程。评价不仅看接受部分是否更准确，还看覆盖率（Coverage）下降了多少。Geifman and El-Yaniv (2017) 在深度分类中研究这一风险与覆盖率的权衡。

## 两个互补页面

- [风险–覆盖率](risk-coverage.md)：定义接受集合与其平均损失，解释完整曲线。
- [曲线下面积](aurc.md)：汇总不同覆盖率下的表现，并说明基础错误率的影响。

拒答全部问题可以避免交付错误，却没有完成任何问题。因此零覆盖率下的接受风险通常未定义，不能作为“零风险系统”的充分证据。真实使用还需考虑拒答后的成本、人工容量与服务范围。

开放生成应事先定义显式拒答、部分回答、空输出与工具失败。如果模型只交付有把握的若干主张，主张覆盖率与问题覆盖率不是同一个指标。改变回答内容的策略需要端到端评估；只根据固定回答的分数筛选，才是固定预测上的排序比较。

分类研究中的有限样本或分布假设下保证不能直接推广到开放生成与分布变化。应用前仍要检查任务标签、数据独立性与阈值选择过程。

## 参考文献（References）

- Geifman, Y., El-Yaniv, R. (2017). *Selective Classification for Deep Neural Networks*. Advances in Neural Information Processing Systems, 30. [Paper](https://papers.nips.cc/paper_files/paper/2017/hash/4a8423d5e91fda00bb7e46540e2b0cf1-Abstract.html)
