---
tags:
  - foundations
  - information-theory
  - cross-entropy
---

# 交叉熵（Cross-Entropy）

交叉熵涉及两个分布：一个决定哪些结果需要被重视，另一个提供这些结果的预测概率。理解它时，首先要分清谁是目标、谁是模型，以及平均是按哪个分布进行的。

## 目标分布与预测分布

设 $p$ 和 $q$ 定义在同一个有限结果集合 $\mathcal{X}$ 上。$p(x)$ 是目标分布，$q(x)$ 是用于预测或评分的分布。交叉熵定义为：

$$
H(p,q)=-\sum_{x\in\mathcal{X}}p(x)\log q(x)
\tag{1}
\label{eq:cross-entropy-definition}
$$

$x$ 是可能结果，$H(p,q)$ 表示按 $p$ 加权的负对数预测概率，本页使用自然对数，单位为 nat。$p(x)=0$ 的项计为零；如果某个结果满足 $p(x)>0$ 而 $q(x)=0$，交叉熵为正无穷。

式 $\eqref{eq:cross-entropy-definition}$ 的权重来自 $p$，对数内部则是 $q$。因此，不能把模型自己的概率同时代入两处，再继续称其为目标与模型之间的交叉熵：那样算到的是模型分布自身的熵。

这里的“目标”不保证是已知的真实数据生成分布。实际训练中，它可以是人工标签形成的分布或数据的经验分布。应明确目标如何取得，不能把有限数据中的标签分配直接当成完整世界的概率规律。

## 与熵及分布差异的关系

记 $H(p)$ 为目标分布的熵，$D_{\mathrm{KL}}(p\|q)$ 为 KL。当 $q$ 在 $p$ 的正概率结果上也为正时：

$$
H(p,q)=H(p)+D_{\mathrm{KL}}(p\|q)
\tag{2}
\label{eq:cross-entropy-decomposition}
$$

这一关系可由在式 $\eqref{eq:cross-entropy-definition}$ 中加上并减去 $\sum_x p(x)\log p(x)$ 直接得到。$H(p)$ 描述目标自身的不确定性，散度项描述预测分布与目标的差异。相关标准定义见 [Goodfellow et al. (2016)](https://www.deeplearningbook.org/ "文献引用")，散度的性质见 [KL 散度](kl-divergence.md)。

固定 $p$ 时，$H(p)$ 是常数，因此最小化交叉熵等价于最小化这个方向的散度；若允许任意预测分布，最小值在 $q=p$ 时达到。受限模型未必能够表示 $p$，实际训练也未必找到全局最优。

若目标本身不是确定分布，即使 $q=p$，交叉熵也通常不为零。因此不能跨不同目标任务，仅凭交叉熵数值判断哪个模型“更可靠”。

## 单个独热（One-hot）标签

在离散分类中，假设已观察到类别 $y$，并用独热目标 $p_y$ 表示：$p_y(y)=1$，其他类别概率为零。式 $\eqref{eq:cross-entropy-definition}$ 简化为：

$$
H(p_y,q)=-\log q(y)
\tag{3}
\label{eq:cross-entropy-one-hot}
$$

$q(y)$ 是模型赋给观察类别的概率。比如，同一标签在两个预测下分别得到概率 $0.8$ 和 $0.2$，对应损失约为 $0.223$ 和 $1.609$ nat。模型给标签的概率越小，这一项损失越大；只知道预测的最高概率类别，则不足以还原交叉熵。

若目标是软标签（Soft Label），多个类别可以有非零权重，就应使用完整加权和，不能仍只取一个类别的负对数概率。

多个位置的损失还需要一个聚合规则。若模型在两个位置分别赋予对应目标 token 的概率为 $0.8$ 和 $0.2$，其交叉熵之和约为 $1.833$，平均约为 $0.916$。这两个数来自同一预测，区别只是分母；不报告聚合方式，就不能进行有意义的数值比较。

对于 one-hot 目标，未命中标签的类别虽然没有单独出现在式中，却并非与预测完全无关：模型输出必须归一化，增加其他类别的概率质量会挤占目标类别的概率。相反，如果直接把未经归一化的 logits 当成概率代入对数，就不是式中定义的交叉熵。

## 与语言模型训练的关系

LLM 进行 next-token 训练时，可把数据中的下一个 token 作为 one-hot 标签，把当前上下文下的 vocabulary 分布作为预测分布。每个有效位置因此产生式 $\eqref{eq:cross-entropy-one-hot}$ 形式的损失，再按训练约定聚合。神经语言模型使用观测序列训练条件概率的基础可参见 [Bengio et al. (2003)](https://www.jmlr.org/papers/v3/bengio03a.html "文献引用")。

这里的上下文通常包含数据中的前缀；它与 inference 时模型自行生成的前缀可能不同。比较训练损失与生成回答分数时，需要先说明两者使用了什么上下文，而不是仅因为表达式都含有 $-\log q(y)$ 就视为同一测量。

## 交叉熵与 NLL 何时相等？

NLL 从观测数据的似然出发。对相同模型、one-hot 观测标签、相同位置权重及相同求和或平均方式，交叉熵损失与 NLL 可以相等。使用软标签、不同 mask 或不同归一化后，不能不加条件地沿用这个等式。

交叉熵衡量目标分布下的平均预测损失；它不是模型自己的熵，也不是一个已生成回答的事实正确率。后续将其用于 UQ 时，仍需说明标签和可靠性目标之间的关系。序列聚合与长度处理见 [负对数似然](negative-log-likelihood.md)。

## 参考文献

- Goodfellow, I., Bengio, Y., Courville, A. (2016). *Deep Learning*. MIT Press. [Book](https://www.deeplearningbook.org/)
- Bengio, Y., Ducharme, R., Vincent, P., Jauvin, C. (2003). *A Neural Probabilistic Language Model*. Journal of Machine Learning Research, 3, 1137–1155. [Paper](https://www.jmlr.org/papers/v3/bengio03a.html)
