---
tags:
  - foundations
  - llm
  - probability
---

# Token 与序列概率（Token and Sequence Probability）

大语言模型（Large Language Model，LLM）可以为下一个 token 分配概率，也可以为给定的输出序列计算概率。本页区分这些数值的定义、长度影响，以及它们与回答正确性之间的关系。

以下固定模型参数与输入格式，用 $p$ 表示未经生成策略调整的模型分布。输出是特定的 token 序列，计分时不包含 prompt 本身的概率。

## Token 层面的概率（Token-level Probability）

设第 $t$ 步的上下文为 $c_t=(x,y_{<t})$，其中 $x$ 是输入，$y_{<t}$ 是已有输出前缀。模型先产生 logits 向量 $\mathbf{z}_t=(z_{t,1},\ldots,z_{t,V})$，再使用 softmax 转换为词表上的概率：

$$
p(y_t=i\mid c_t)
=
\frac{\exp(z_{t,i})}
{\sum_{j=1}^{V}\exp(z_{t,j})}.
\tag{1}
\label{eq:token-softmax}
$$

其中，$V$ 是词表大小；$i$ 和 $j$ 是 token 编号；$z_{t,i}$ 是当前上下文下第 $i$ 个 token 的 logit；$p(y_t=i\mid c_t)$ 表示下一个 token 取该编号的模型概率。

logits 可以为正或为负，也不要求相加等于一。softmax 输出的各项非负，且在整个词表上求和为一。logit 大小需要相对其他候选项解释：给所有 logits 加上同一个常数，不会改变概率分布。[Bengio et al. (2003)](https://www.jmlr.org/papers/v3/bengio03a.html) 的神经语言模型使用 softmax 输出条件概率，其原文也讨论了数值计算中的稳定处理。

一个位置上实际生成的 token 只对应其中一个概率值。只保存这个值，会丢失其他候选项的分布信息；保存完整分布，则能够分析这一位置上的不同可能性。两种数据支持的后续分析不同。

## 序列概率（Sequence Probability）

给定输出 $y_{1:T}=(y_1,\ldots,y_T)$，序列概率由沿该输出路径的条件概率相乘得到：

$$
p(y_{1:T}\mid x)
=
\prod_{t=1}^{T}p(y_t\mid x,y_{<t}).
\tag{2}
\label{eq:sequence-probability}
$$

这里 $T$ 是计入的输出 token 数量，$y_t$ 是相应位置的 token。每一项使用各自的上下文，不能用第一步的分布为整段序列计分。生成时这些上下文如何形成，见[自回归生成](autoregressive-generation.md)。

本页将序列结束标记（End of Sequence，EOS）视为可计分的 token。若所讨论的是生成完整回答并结束的概率，应把 EOS 包含在序列中；若未包含 EOS，公式描述的是相应前缀的概率。比较不同方法时，需要统一或明确这一约定。

## 序列的 log probability

对公式 $\eqref{eq:sequence-probability}$ 取自然对数，得到：

$$
\log p(y_{1:T}\mid x)
=
\sum_{t=1}^{T}\log p(y_t\mid x,y_{<t}).
\tag{3}
\label{eq:sequence-log-probability}
$$

本页所有 $\log$ 均指自然对数。公式中的各 token 条件概率与上一节相同，只是乘积变成了求和。

实际计算常使用 log probability，因为大量小概率直接相乘可能产生数值下溢。若每一步的概率都是 $0.1$，长度为 $100$ 的路径概率就是 $10^{-100}$；用对数表示则为 $100\log 0.1$。对数是单调递增函数，因此对相同候选集合比较原始序列概率与其对数，排序相同。log probability 越大、越接近零，表示该序列的模型概率越高。

计算时仍应使用稳定的 softmax 或 log-softmax 实现。先得到已经数值下溢的零概率，再取对数，并不能恢复丢失的信息。

## 长度为什么影响分数

对同一个尚未结束的前缀继续追加 token，新增的每个条件概率都不超过一，因此前缀概率不会增大。若沿两条示例路径的每一步条件概率均为 $0.8$，长度为 $2$ 和 $5$ 的路径概率分别为 $0.8^2=0.64$ 与 $0.8^5=0.32768$。

这是连乘带来的长度效应，不能直接解释为长回答更不可靠。对任意两个不同回答，也不能仅凭长度断言哪个序列概率更小，因为各步的条件概率可能不同。

一种常见的长度归一化（Length Normalization）分数是平均 token log probability：

$$
s_{\mathrm{mean}}(y_{1:T},x)
=
\frac{1}{T}\sum_{t=1}^{T}\log p(y_t\mid x,y_{<t}),
\qquad T\geq 1.
\tag{4}
\label{eq:mean-token-log-probability}
$$

其中，$s_{\mathrm{mean}}$ 是归一化后的分数，$T$ 必须与求和中实际计入的 token 数量一致。它的指数等于各 token 条件概率的几何平均数。

这个分数通常不再是完整序列的概率，也没有在所有候选序列上归一化。不同方法可能采用不同长度处理方式；平均分数也不能保证消除所有长度相关影响。比较时还需核对 tokenizer、是否计入 EOS，以及分数的正负号与方向。

## 模型概率与回答正确性

模型概率回答的是“在这个模型分布下，这段 token 序列有多大概率出现”。回答正确性（Correctness）则需要依据任务、事实或给定证据判断。二者对应不同事件。

因此，即使某段文本获得较高模型概率，也不能直接把该数值当作回答正确的概率。这首先是概率对象的区别，并不只是数值是否经过校准的问题。

置信度（Confidence）若用于表示回答正确的可能性，还需要检查置信度校准（Confidence Calibration）。[Guo et al. (2017)](https://proceedings.mlr.press/v70/guo17a.html) 在分类问题中研究了置信度与实际正确率的匹配。这里借用该校准目标，应用到回答层面时仍须先明确正确性事件与判断规则。

## 与当前研究的关系

不确定性量化（Uncertainty Quantification，UQ）可以从这些概率构造信号，但用于幻觉（Hallucination）或错误检测时，需要验证信号与目标错误的关系。实验中应说明使用的是单 token 概率、序列 log probability，还是某种归一化分数。

还应区分原始模型分布与实际 sampling 分布。生成过程中若调整了概率，必须说明报告的分数来自调整前还是调整后；相关区别见[解码与 Sampling](decoding-sampling.md)。

## 参考文献（References）

- Bengio, Y., Ducharme, R., Vincent, P., Jauvin, C. (2003). *A Neural Probabilistic Language Model*. Journal of Machine Learning Research, 3, 1137–1155. [Paper](https://www.jmlr.org/papers/v3/bengio03a.html)
- Guo, C., Pleiss, G., Sun, Y., Weinberger, K. Q. (2017). *On Calibration of Modern Neural Networks*. ICML, Proceedings of Machine Learning Research, 70, 1321–1330. [Paper](https://proceedings.mlr.press/v70/guo17a.html)
