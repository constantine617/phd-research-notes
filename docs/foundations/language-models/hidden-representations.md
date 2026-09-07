---
tags:
  - foundations
  - llm
  - representation
---

# Hidden Representations

本页使用 hidden state 和 hidden representation 作为标准术语。讨论范围是基于因果 Transformer 的生成式大语言模型（Large Language Model，LLM），重点解释内部向量怎样与 token 位置、输出 logits 和可靠性研究联系起来。

## Hidden state 是什么

模型处理 token 序列时，每一层都会在各个 token 位置形成内部向量。在本页中，一个确定层、确定位置的向量称为 hidden state；hidden representation 则用于泛指这些内部表示，也可以指研究者从多个位置整理出的特征。

可以将第 $\ell$ 层、第 $t$ 个输入位置的 hidden state 记为：

$$
\mathbf{h}^{(\ell)}_t\in\mathbb{R}^{d}.
\tag{1}
\label{eq:hidden-state-vector}
$$

其中，$\ell$ 是层编号，$t$ 是已经输入模型的 token 位置，$d$ 是 hidden dimension。向量的每个分量是模型计算得到的数值，不能仅凭分量编号就为它指定一个固定语义。

输入端的 token embedding 通常从词表表示中取得；经过模型层处理后的 hidden state 则结合了当前位置允许使用的上下文。因而，同一个 token 出现在不同上下文中时，其 hidden state 可以不同。

[Vaswani et al. (2017)](https://papers.nips.cc/paper/7181-attention-is-all-you-need) 的 decoder 使用因果约束，使一个位置只能读取自身及此前位置的信息。本页只需要这一约束与逐层处理的概念，更完整的 Transformer architecture 可以后续单独建立 canonical page。

## Hidden state 怎样连接 logits

在常见语言模型输出层中，最后一层的表示经过输出投影（Output Projection）得到词表 logits。可以用以下简化表达理解这一步：

$$
\mathbf{z}_t
=
W_{\mathrm{out}}\mathbf{h}^{(L)}_t+\mathbf{b}.
\tag{2}
\label{eq:hidden-state-to-logits}
$$

其中，$L$ 是最后一层的编号，$W_{\mathrm{out}}\in\mathbb{R}^{V\times d}$ 是输出投影矩阵，$V$ 是词表大小，$\mathbf{b}\in\mathbb{R}^{V}$ 是可选偏置，$\mathbf{z}_t\in\mathbb{R}^{V}$ 是该输入位置对应的 logits 向量。

这是概念上的表达。实际模型可能在投影前进行归一化，也可能不使用偏置；提取的 hidden state 是否已经包含最后的归一化，要按具体实现确认。Vaswani et al. (2017) 描述了从 decoder 输出经线性变换和 softmax 得到下一个 token 概率的过程。

### 位置与预测目标需要对齐

本页的 $t$ 标记已经输入模型的位置。按常见因果语言模型的索引方式，$\mathbf{h}^{(L)}_t$ 及其 logits 用于预测第 $t+1$ 个 token。这与其他页面中用 $t$ 标记“当前待生成的第几步”不同。

例如，要预测第一个回答 token，需要使用完整 prompt 最后一个位置的状态。若读取最后一个回答 token 处理之后的状态，它已经能够使用整段回答；用于预测该 token 的前一位置状态尚未读到该 token。

因此，记录“最后一个 hidden state”时，需要说明是 prompt 的末尾、回答的末尾，还是其他位置。得到 logits 后如何转换为概率，见[Token 与序列概率](token-sequence-probability.md)。

## 为什么研究内部表示

logits 是面向词表候选的输出分数，其 softmax 决定下一个 token 的分布。Hidden representations 则提供模型内部计算得到的特征。研究者可以检验这些特征是否与回答正确性（Correctness）、模型具备的知识、置信度（Confidence）或不确定性有关。

[Azaria and Mitchell (2023)](https://aclanthology.org/2023.findings-emnlp.68/) 在特定模型与数据上使用 hidden-layer activations 训练分类器，预测陈述的真假。这为从内部表示研究输出可靠性提供了直接例子。

这一结果不能解释为任意 hidden state 都能直接读出正确答案，也不能保证换一个模型或任务仍然有效。读取某层向量只是获得信号，如何整理特征、如何判断目标，以及适用范围是什么，仍需分别检验。

## 需要什么模型访问条件

为便于讨论，本页按能够读取的信息区分三种条件；这些名称的具体边界在不同论文中可能不同。

- 黑箱访问（Black-box Access）：只能提交输入并获得输出文本，可以重复调用，但不能直接读取内部状态。
- Log-prob access：还能读取部分或全部 token 的 log probability。可用范围取决于服务提供的信息，不能据此假定拥有完整词表分布。
- 白箱 hidden-state 访问（White-box Hidden-state Access）：能够读取指定层和位置的内部状态。这里仅指访问 hidden state 的能力，不额外假定可以修改参数或取得梯度。

纯文本应用程序接口（Application Programming Interface，API）通常不直接提供 hidden state。开展相关研究需要能够运行并读取模型内部状态，或使用明确暴露这些状态的服务。

## 与当前研究的关系

不确定性量化（Uncertainty Quantification，UQ）中的基于表征的 UQ（Representation-based UQ）是当前研究兴趣之一。它需要先明确所用向量的层、位置、是否经过归一化，以及与输出 token 的对应关系。

这些约定决定了研究者实际读取什么信息。本页提供这一前置基础，具体使用哪类表征信号、是否比其他信号更有用，应由后续方法比较与错误分析回答。

## 参考文献（References）

- Vaswani, A., Shazeer, N., Parmar, N., Uszkoreit, J., Jones, L., Gomez, A. N., Kaiser, Ł., Polosukhin, I. (2017). *Attention Is All You Need*. Advances in Neural Information Processing Systems, 30. [Paper](https://papers.nips.cc/paper/7181-attention-is-all-you-need)
- Azaria, A., Mitchell, T. (2023). *The Internal State of an LLM Knows When It’s Lying*. Findings of the Association for Computational Linguistics: EMNLP 2023, 967–976. [Paper](https://aclanthology.org/2023.findings-emnlp.68/)
