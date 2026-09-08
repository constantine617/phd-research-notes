---
tags:
  - evaluation
  - calibration
  - ece
---

# 期望校准误差（Expected Calibration Error，ECE）

ECE 把预测分数分箱，比较每箱平均置信度（Confidence）与平均正确率，再按样本数加权。本页采用 Guo et al. (2017) 中常见的预测正确性校准形式：每条样本只有一个置信度和一个正确标签。

## 分箱计算

设共有 $N$ 条样本，第 $i$ 条的置信度为 $c_i\in[0,1]$，正确标签为 $z_i\in\{0,1\}$。将置信度区间分为 $M$ 个箱，$B_m$ 为落入第 $m$ 箱的样本索引集合。

$$
\operatorname{acc}(B_m)=\frac{1}{|B_m|}\sum_{i\in B_m}z_i,
\qquad
\operatorname{conf}(B_m)=\frac{1}{|B_m|}\sum_{i\in B_m}c_i
\tag{1}
\label{eq:ece-bin-statistics}
$$

$|B_m|$ 为非空箱中的样本数；$\operatorname{acc}$ 与 $\operatorname{conf}$ 分别为箱内正确率与平均置信度。

$$
\widehat{\operatorname{ECE}}
=\sum_{m:|B_m|>0}\frac{|B_m|}{N}
\left|\operatorname{acc}(B_m)-\operatorname{conf}(B_m)\right|
\tag{2}
\label{eq:ece-weighted-gap}
$$

$\widehat{\operatorname{ECE}}$ 为样本分箱估计，空箱不贡献项。它位于 $[0,1]$，在相同约定下越低越好。有限样本上得到零，不足以证明总体或所有子群精确校准。

例如某箱有二十条回答，平均置信度 $0.8$，其中十二条正确，则箱内差为 $0.2$；该箱对总分的贡献还要乘以二十除以总样本数的权重。

## 等宽与等量

等宽分箱（Equal-width Binning）让区间宽度相同，便于比较固定概率范围；等量分箱（Equal-mass Binning）让箱内样本数大致相同，减少极稀疏箱，但箱边界随数据改变。并列分数可能导致等量不精确，应说明并列分配规则。

记录箱数、边界、端点归属、空箱处理和数据量。箱数越多，并不意味着估计必然更准确：细粒度增加，但每箱频率的统计波动也可能更大。

## 主要局限

Nixon et al. (2020) 分析分箱、只看最高类别概率、类别条件评价等选择对结论的影响。本页引用其 2020 修订预印本，不将其版本作者列表混同于早期版本。

宽箱可能让箱内过度自信与不够自信相互抵消。样本跨越固定边界时，估计可能发生跳变，因而该指标对分箱边界不连续。总体低 ECE 也可能掩盖某个领域、少数类别或困难问题上的失准。只评价最高类别置信度，不能保证整个多类概率向量已校准。

在开放生成中，首先固定“正确”的规则与回答版本。若 $c_i$ 只是任意不确定性量化（Uncertainty Quantification，UQ）分数，应先在独立数据上建立概率映射；随意缩放到 $[0,1]$ 并不赋予其正确概率含义。

解释结果时同时看可靠性图、箱内样本量、[Brier Score](brier-score.md)与任务准确率，避免只根据单个 ECE 选择系统。

## 参考文献（References）

- Guo, C., Pleiss, G., Sun, Y., Weinberger, K. Q. (2017). *On Calibration of Modern Neural Networks*. ICML, Proceedings of Machine Learning Research, 70, 1321–1330. [Paper](https://proceedings.mlr.press/v70/guo17a.html)
- Nixon, J., Dusenberry, M., Jerfel, G., Nguyen, T., Liu, J., Zhang, L., Tran, D. (2020). *Measuring Calibration in Deep Learning*. arXiv:1904.01685v2；首版 2019，本页使用 2020 修订版. [Paper](https://arxiv.org/abs/1904.01685v2)
