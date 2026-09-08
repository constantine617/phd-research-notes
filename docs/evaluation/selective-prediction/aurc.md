---
tags:
  - evaluation
  - selective-prediction
  - aurc
---

# 风险–覆盖率曲线下面积（Area Under the Risk-Coverage Curve，AURC）

AURC 汇总不同覆盖率下的选择风险，越低越好。它既受置信度排序影响，也受基础模型产生了多少错误影响。只比较一个面积数字，不能把两者自动分开。

## 连续与经验约定

$$
\operatorname{AURC}=\int_0^1 R(c)\,dc
\tag{1}
\label{eq:aurc-integral}
$$

$c$ 为覆盖率，$R(c)$ 为相应选择风险；损失采用零一错误时，面积位于 $[0,1]$。零覆盖率处风险未定义，但单个端点不改变积分；数值绘图与积分仍须交代端点处理。

给定 $N$ 条固定预测，按置信度降序排列后记第 $i$ 条损失为 $\ell_{(i)}$。一种常见的等覆盖率步长、右端点经验计算为：

$$
\widehat{\operatorname{AURC}}
=\frac1N\sum_{k=1}^{N}\widehat R_k,
\qquad
\widehat R_k=\frac1k\sum_{i=1}^{k}\ell_{(i)}
\tag{2}
\label{eq:empirical-aurc}
$$

$k$ 为被接受样本数，$\widehat R_k$ 为前 $k$ 条的平均损失。本页采用公式 $\eqref{eq:empirical-aurc}$ 说明有限样本计算；若使用梯形积分、只按不同分数分组或仅积分部分覆盖率，结果可能不同，须明确命名与实现。

## 排序的作用

把错误尽量放到后面，会让较低覆盖率处风险下降。同一回答池中，AURC 因而能评价拒答排序。[Geifman et al. (2019)](https://arxiv.org/abs/1805.08206 "文献引用") 进一步讨论减去该分类器理想排序面积的超额面积（Excess AURC，E-AURC），以减少基础错误率的直接影响。

理想排序基线依赖同一损失集合及积分约定：不能用连续近似基线减去另一种有限样本面积而不说明。超额面积 也不应脱离基础错误率与实际覆盖率曲线单独报告。

## 为什么要报告基础错误率

若一个模型本身从不出错，它的 AURC 可以为零，即使所有置信度相同。另一个有少量错误、但排序完美的模型，完整覆盖率处仍有错误，AURC 不会因此为零。两者面积差异并非只来自检测能力。

当缓解方法重写回答时，应同时报告任务正确率与 AURC；当研究只比较不确定性信号时，优先使用同一批回答和标签。严格单调变换若保持排序，通常不改变固定排序约定下的面积，却可能改变概率校准。

## 有限样本与实用范围

并列分数中的标签排序不能由真值决定，除非明确计算理想基线。低覆盖率只有少量样本时，风险波动很大；置信区间与样本量有助于判断差异是否稳定。若应用只接受高覆盖率区间，可以额外报告该区间，但需保留区间边界与归一化方式。

## 相关笔记

- [风险–覆盖率定义](risk-coverage.md)
- [统计报告](../experimental-protocols/statistical-reporting.md)

## 参考文献

- Geifman, Y., Uziel, G., El-Yaniv, R. (2019). *Bias-Reduced Uncertainty Estimation for Deep Neural Classifiers*. ICLR；首版预印本 2018. [Paper](https://arxiv.org/abs/1805.08206)
