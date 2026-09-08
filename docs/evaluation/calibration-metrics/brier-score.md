---
tags:
  - evaluation
  - calibration
  - brier-score
---

# Brier Score

Brier Score 评价概率预测与实际结果之间的平方误差，越低越好。它源自概率预报评价（[Brier, 1950](https://doi.org/10.1175/1520-0493%281950%29078%3C0001%3AVOFEIT%3E2.0.CO%3B2 "文献引用")）。本页明确二元与多类的缩放约定，避免相差两倍的结果被误读为性能差异。

## 二元事件

设第 $i$ 条样本的事件标签为 $y_i\in\{0,1\}$，预测事件发生概率为 $p_i\in[0,1]$，共 $N$ 条样本：

$$
\operatorname{BS}_{\mathrm{binary}}=\frac1N\sum_{i=1}^{N}(p_i-y_i)^2
\tag{1}
\label{eq:brier-binary}
$$

$\operatorname{BS}_{\mathrm{binary}}$ 是本页采用的二元 Brier Score，范围为 $[0,1]$。例如预测正确概率为 $0.8$，实际正确时损失为 $0.04$，实际错误时损失为 $0.64$。若 $p_i$ 改为错误概率，$y_i$ 也必须相应改成错误事件标签。

## 多类概率向量

$$
\operatorname{BS}_{\mathrm{multi}}
=\frac1N\sum_{i=1}^{N}\sum_{k=1}^{K}
\left(p_{ik}-\mathbf1[y_i=k]\right)^2
\tag{2}
\label{eq:brier-multiclass}
$$

$K$ 为互斥且穷尽的类别数，$p_{ik}$ 为第 $i$ 条属于类别 $k$ 的概率，各类概率之和为一；$y_i$ 为真实类别，$\mathbf1[\cdot]$ 为指示函数。本式不除以类别数，也不额外乘二分之一，范围为 $[0,2]$。

当 $K=2$，对两个类别求和的公式 $\eqref{eq:brier-multiclass}$ 等于公式 $\eqref{eq:brier-binary}$ 的两倍。部分实现会做二分之一缩放，或按类别平均，必须报告实际约定。scikit-learn 官方 `brier_score_loss` 文档明确提供了 `scale_by_half` 参数；实验还应记录所用版本与类别列顺序。

## 严格适当评分规则（Strictly Proper Scoring Rule）

严格适当评分规则意味着：在真实分布下计算期望时，诚实报告真实概率分布可唯一最小化期望损失。它不意味着一个有限测试集上分数最小的模型一定具有真实概率，也不保证训练过程找到了总体最优解。

Brier 评价概率预测的整体质量，同时受校准与区分不同风险样本的能力影响。因此它不应被称为“纯校准误差”。相同数据上应结合[期望校准误差](expected-calibration-error.md)或可靠性图解释；跨数据集比较时，事件基率改变也会改变可达到的分数。

## 生成任务中应输入哪种概率？

如果评价“整段正确”这一二元事件，就使用针对该事件的概率，而不是回答中最高 token 的概率。多类形式要求明确的类别空间；不能把若干生成答案的未归一化分数直接作为概率向量。零一硬判断可代入，但会丢失概率预测所包含的信息。

## 参考文献

- Brier, G. W. (1950). *Verification of Forecasts Expressed in Terms of Probability*. Monthly Weather Review, 78(1), 1–3. [Paper](https://doi.org/10.1175/1520-0493%281950%29078%3C0001%3AVOFEIT%3E2.0.CO%3B2)
- scikit-learn developers（访问于 2026-09-08）. *brier_score_loss*. 官方 API 文档，用于说明概率输入与缩放约定. [Documentation](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.brier_score_loss.html)
