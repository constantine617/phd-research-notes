---
tags:
  - foundations
  - probability
  - nll
---

# 负对数似然（Negative Log-Likelihood，NLL）

NLL 将“提高已观察数据的似然”改写为最小化目标。它既可作为训练目标，也可用于固定模型下的数据评分；这两种用途需要说明参数是否在变化。本页讨论离散输出，使用自然对数。

## 从数据似然到损失

设数据集 $D=\{(x_i,y_i)\}_{i=1}^{N}$，其中 $N$ 是样本数，$x_i$ 为输入，$y_i$ 为观测输出，$\theta$ 为模型参数。若不同样本的输出在给定输入及参数后条件独立，则条件似然可以写成：

$$
\mathcal{L}(\theta;D)=\prod_{i=1}^{N}p_\theta(y_i\mid x_i)
\tag{1}
\label{eq:conditional-dataset-likelihood}
$$

这里 $p_\theta$ 是参数化条件概率模型，$\mathcal{L}$ 不包括输入本身的概率。独立性是这一数据集分解的假设，不是“所有 token 互相独立”的假设。

对式 $\eqref{eq:conditional-dataset-likelihood}$ 取对数得到：

$$
\ell(\theta;D)=\sum_{i=1}^{N}\log p_\theta(y_i\mid x_i)
\tag{2}
\label{eq:conditional-dataset-log-likelihood}
$$

$\ell$ 为对数似然。其相反数就是 NLL：

$$
\operatorname{NLL}(\theta;D)=-\sum_{i=1}^{N}\log p_\theta(y_i\mid x_i)
\tag{3}
\label{eq:dataset-negative-log-likelihood}
$$

在数据、模型和可行参数集合不变时，最大化似然、最大化对数似然和最小化 NLL 的最优参数相同。若另外添加正则项，优化的就是组合目标，不能再把整体目标称为纯粹的最大似然。

对于离散概率，每项 NLL 非负；观测结果被赋予零概率时，该项为正无穷。实际计算通常直接使用稳定的 log probability，避免先连乘小概率再取对数。

## 序列的负对数似然

对给定 prompt $x$ 和输出序列 $y_{1:T}$，自回归概率链式分解给出：

$$
\operatorname{NLL}(y_{1:T}\mid x)
=-\log p_\theta(y_{1:T}\mid x)
=-\sum_{t=1}^{T}\log p_\theta(y_t\mid x,y_{<t})
\tag{4}
\label{eq:autoregressive-sequence-nll}
$$

$T$ 是计分 token 数，$y_t$ 是第 $t$ 个 token，$y_{<t}$ 是此前的 token 前缀，$\theta$ 在评分时固定。式 $\eqref{eq:autoregressive-sequence-nll}$ 允许 token 依赖整个已有前缀，不要求序列内部独立。神经语言模型对序列条件概率的建模可参见 Bengio et al. (2003)。

若计算“恰好生成这一完整回答并终止”的概率，应计入序列结束 token（End-of-Sequence Token，EOS）。若没有计入终止事件，通常是在给一个前缀评分。对被最大长度强制截断的输出，也应明确它没有因此自动成为模型自然终止的完整序列。

## 与交叉熵何时相等

对 one-hot 观测标签，单个位置的交叉熵为目标 token 的负对数预测概率。将相同有效位置按相同权重聚合，就得到相同的 NLL 目标。这个等价关系依赖标签和聚合约定，并非任意交叉熵与任意 NLL 都相等。

从分布角度，交叉熵是期望；有限数据上的平均 NLL 是相应观测平均。在经验分布及匹配的条件输入约定下可以写成相等关系，但不能把有限样本平均直接宣称为未知真实分布下的精确期望。定义与 one-hot 情形见 [交叉熵](cross-entropy.md)。

## 长度归一化

序列越长，式 $\eqref{eq:autoregressive-sequence-nll}$ 中累加的非负项越多。给同一前缀继续追加 token 不会降低总 NLL；不过，两个内容不同的回答并不存在仅由长度决定的严格排序。

常见的每 token 平均为：

$$
\overline{\operatorname{NLL}}(y_{1:T}\mid x)
=\frac{\operatorname{NLL}(y_{1:T}\mid x)}{T},\qquad T>0
\tag{5}
\label{eq:mean-token-nll}
$$

$\overline{\operatorname{NLL}}$ 表示平均 token 损失。它减弱直接求和的长度累积效应，但不是完整序列概率的负对数，也不保证消除所有长度相关差异。

数据集上的“总 token 损失除以总 token 数”，会让长回答具有更大权重；“先对每个回答取平均，再对回答平均”，则让回答等权。两者可以产生不同结果。报告时还需交代 prompt 是否计分、padding 是否排除、EOS 是否计入，以及使用何种 tokenization。

一个数值例子可以显示权重差异。假设两条回答分别计分两个和八个 token，总 NLL 分别为二和十六。它们的每 token 平均分别为一和二；把所有 token 合并后平均得到一点八，而先对回答平均再等权汇总得到一点五。前者更重视包含更多 token 的回答，后者更重视回答作为一个整体的单位。

此外，分数还取决于哪些文本被计入。一项任务只对答案区域计分，另一项任务把问题与答案一起计分，即使使用同一个模型，也不能直接比较它们的均值。移除某些位置不仅改变分母，还可能改变实际评价目标。

训练实现中的 mask 通常表示“这一位置是否贡献损失”，不必表示它从模型上下文中被删除。一个不计分的 prompt 仍然可以影响所有答案 token 的概率，记录实验时应把输入范围与计分范围分开说明。

## 与可靠性研究的关系

在大语言模型（Large Language Model，LLM）研究中，NLL 可作为不确定性量化（Uncertainty Quantification，UQ）的候选输入分数，但低 NLL 只说明给定内容在该模型下获得较高概率。它没有把模型偏好的文本自动转化为真实正确的文本。

比较实验还应固定计分分布：模型原始概率与实际 sampling 分布可以不同。若回答由一种分布生成、再由另一种分布评分，应明确记录。基础概率约定见 [Token 与序列概率](../language-models/token-sequence-probability.md)，两类分布的区别见 [解码与 Sampling](../language-models/decoding-sampling.md)。

## 参考文献（References）

- Bengio, Y., Ducharme, R., Vincent, P., Jauvin, C. (2003). *A Neural Probabilistic Language Model*. Journal of Machine Learning Research, 3, 1137–1155. [Paper](https://www.jmlr.org/papers/v3/bengio03a.html)
