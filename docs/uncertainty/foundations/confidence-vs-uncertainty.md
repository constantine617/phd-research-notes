---
tags:
  - uq
  - confidence
---

# 置信度与不确定性（Confidence vs Uncertainty）

置信度表达对一个明确事件或预测的把握程度，不确定性描述某种尚未确定的程度。二者常常相关，但其数值关系取决于定义。对于大语言模型（Large Language Model，LLM）的不确定性量化（Uncertainty Quantification，UQ），首先要确认分数针对的是哪个对象，然后才讨论大小方向与概率解释。

## 相似名称可能对应不同事件

基于概率的置信度（Probability-based Confidence）可能取自已选 token 概率、某个候选答案概率或序列分数。语言自报置信度（Verbalized Confidence）是模型通过文字或数字报告的把握程度。经过校准的置信度（Calibrated Confidence）则应在指定目标和群体上接受频率匹配的检验。

例如，模型给回答附上“80%”，这个数字是文本内容；生成字符“80%”的 token 概率，是产生该表达的概率。后者并不自动说明回答正确的概率是八成。Lin et al. (2022) 将语言表达的概率与从 logits 获取的概率分别研究，正是为了区分这两种对象。

不确定性分数还可能是熵、距离或分类器输出。熵的单位和范围与结果空间及对数底有关；距离通常依赖特征尺度；分类器的数值又依赖训练目标。不能仅因都画成一条曲线，就把它们当作同一尺度上的量。

## 什么时候可以取补数

如果 $c$ 被定义为某个二元事件的概率，那么 $1-c$ 是其补事件概率。这里必须是同一事件、同一条件和同一概率模型。例如“完全正确”与“并非完全正确”可以互补，但“完全正确”与“至少一条事实得到支持”不是互补事件。

即使 $c$ 是正确概率，正确性这个二元变量的熵也不等于 $1-c$。当 $c$ 接近零或一时，二元结果分布都很集中；当 $c$ 接近二分之一时才最分散。因此，“很确定会错”可以同时对应高错误风险与低结果熵。

对任意分数 $u$ 写成 $1-u$ 更缺少依据。如果 $u$ 是以自然对数计算的熵或一个没有上界的距离，取补数可能连概率范围都无法满足。数值被缩放进零到一区间，也只是范围变化，并未因此获得概率含义。

## 明确分数方向

| 常见量 | 常用方向 | 必须补充的限定 |
|---|---|---|
| 回答的平均 log-probability | 越大通常越符合模型分布 | 不保证越正确 |
| 负对数似然（Negative Log-Likelihood，NLL）式分数 | 越大通常越不符合模型分布 | 长度与归一化影响解释 |
| 预测分布的熵 | 越大越分散 | 先确定随机变量与输出空间 |
| 正确性分类器输出 | 取决于哪个标签为正类 | 预测错误还是正确必须写清 |
| 特征距离 | 越大通常越偏离参照 | 参照代表常见分布还是错误类别 |

一个严格单调变换可保留某个标量的排序，却改变其数值与概率解释。若把分数方向反转，还需要同步改变阈值和图轴；否则可能把高风险样本当作低风险。

## 排序与校准分别回答什么

Geng et al. (2024) 区分相对置信度与绝对概率解释。前者关心哪些回答更值得怀疑；后者还要求“八成”在相应预测群体中接近八成正确。[置信度校准（Confidence Calibration）](../../calibration/index.md)讨论后一种关系，其经典依据见 Guo et al. (2017)。

当前研究记录每个信号时，应给出目标事件、取值范围、分数方向和转换过程。对“高分”先问它代表更分散、更常见还是更可能正确，能避免许多比较错误。若多个分数方向一致但对象不同，仍需分别解释，不能直接求平均并称其为综合正确概率。

## 相关笔记（Related Notes）

- [基于概率的 UQ](../probability-based/index.md)
- [Verbal Confidence](../verbal-confidence/index.md)

## 参考文献（References）

- Lin, S., Hilton, J., Evans, O. (2022). *Teaching Models to Express Their Uncertainty in Words*. Transactions on Machine Learning Research. [作者版本](https://arxiv.org/abs/2205.14334)
- Geng, J., Cai, F., Wang, Y., Koeppl, H., Nakov, P., Gurevych, I. (2024). *A Survey of Confidence Estimation and Calibration in Large Language Models*. NAACL-HLT, 6577–6595. [Paper](https://aclanthology.org/2024.naacl-long.366/)
- Guo, C., Pleiss, G., Sun, Y., Weinberger, K. Q. (2017). *On Calibration of Modern Neural Networks*. ICML, Proceedings of Machine Learning Research, 70, 1321–1330. [Paper](https://proceedings.mlr.press/v70/guo17a.html)
