---
tags:
  - foundations
  - llm
  - generation
---

# 自回归生成（Autoregressive Generation）

自回归生成是指模型每次生成一个 token，再把它作为后续生成的条件。对生成式 LLM，可以把这个过程理解为：读取 prompt，计算下一个 token 的分布，选择一个 token，接到上下文末尾，然后继续。

这里的“自回归”描述输出之间的条件依赖，并不要求模型使用循环神经网络。[Vaswani et al. (2017)](https://papers.nips.cc/paper/7181-attention-is-all-you-need "文献引用") 的 Transformer decoder 同样按自回归方式生成输出。

## 每一步都依赖已经生成的内容

设 prompt 为 $x$。生成第一个 token 时，上下文是 $x$；生成第二个 token 时，上下文变成 $x$ 加上第一个输出；后续步骤继续使用已经生成的全部前缀，或模型实际能够处理的上下文范围。

因此，一旦较早的位置选择了不同的 token，后续计算所面对的上下文也会改变。不同生成路径可以逐步形成不同回答。模型并不是先为整段回答固定一组互不相关的 token 概率，再一次性选完。

## 自回归概率分解

记输出序列为 $y_{1:T}=(y_1,y_2,\ldots,y_T)$，并在本页固定模型参数，省略参数下标。根据概率的链式法则（Chain Rule），模型对该序列赋予的条件概率为：

$$
p(y_{1:T}\mid x)
=
\prod_{t=1}^{T}p(y_t\mid x,y_{<t}).
\tag{1}
\label{eq:autoregressive-factorization}
$$

其中，$T$ 是所计入序列的 token 数量，$t$ 是生成步骤，$y_t$ 是第 $t$ 个输出 token，$y_{<t}=(y_1,\ldots,y_{t-1})$ 是此前的输出前缀，$x$ 是固定输入。第一步的 $y_{<1}$ 为空。

公式 $\eqref{eq:autoregressive-factorization}$ 中的乘积来自链式法则，不是假设各个 token 相互独立。每一项都以此前的输出为条件。[Radford et al. (2019)](https://cdn.openai.com/better-language-models/language_models_are_unsupervised_multitask_learners.pdf "文献引用") 将序列的联合概率写为这类条件概率的乘积，并据此计算序列概率和进行 sampling。

如果模型使用序列结束标记（End of Sequence，EOS），完整回答的概率需要计入 序列结束标记 的生成概率。只计算尚未结束的 token 前缀时，得到的是该前缀的概率。关于计分边界的说明见[Token 与序列概率](token-sequence-probability.md)。

## 生成过程

```text
Input: prompt x, decoding settings, stopping conditions

1. Set the context to x.
2. Compute the next-token distribution from the current context.
3. Select or sample a token using the decoding settings.
4. Append the token to the context and the output sequence.
5. Stop if EOS or another stopping condition is reached.
6. Otherwise, return to step 2.

Output: generated token sequence
```

常见停止条件包括生成 序列结束标记 或达到输出长度上限。达到长度上限只表示本次生成被截断，不能据此认为模型已经赋予回答结束的概率。

第 3 步可以使用确定性选择，也可以使用 sampling。具体规则及其对分布的影响由[解码与 Sampling](decoding-sampling.md)解释。自回归分解本身不规定必须采用哪一种规则。

## 逐步生成与已知序列的计算

生成新内容时，下一步需要知道此前实际选出的 token。若整段序列已经给定，因果 Transformer 可以在一次前向计算中并行计算多个位置的预测；因果约束仍保证每个位置只能使用允许的前缀信息。[Vaswani et al. (2017)](https://papers.nips.cc/paper/7181-attention-is-all-you-need "文献引用") 在 decoder 中通过屏蔽后续位置保持这一约束。

因此，“逐 token 生成”与“为已知序列计算各位置概率”是两种不同的计算情形。后续计算回答概率时，应明确是在生成过程中记录分数，还是对已有回答重新计分。

## 与不确定性研究的关系

UQ 可以利用生成过程中每一步的概率信息，也可以分析多个生成结果。序列概率如何组合，见[Token 与序列概率](token-sequence-probability.md)；多个回答怎样产生，见[解码与 Sampling](decoding-sampling.md)。

需要区分模型分布与实际生成规则：经过分布调整后的 sampling，其生成路径概率通常不同于公式中原始模型分布下的概率。自回归形式可以用于两者，但各项条件概率必须来自同一个明确的分布。

## 参考文献

- Vaswani, A., Shazeer, N., Parmar, N., Uszkoreit, J., Jones, L., Gomez, A. N., Kaiser, Ł., Polosukhin, I. (2017). *Attention Is All You Need*. Advances in Neural Information Processing Systems, 30. [Paper](https://papers.nips.cc/paper/7181-attention-is-all-you-need)
- Radford, A., Wu, J., Child, R., Luan, D., Amodei, D., Sutskever, I. (2019). *Language Models are Unsupervised Multitask Learners*. OpenAI Technical Report. [Paper](https://cdn.openai.com/better-language-models/language_models_are_unsupervised_multitask_learners.pdf)
