---
tags:
  - foundations
  - probability
  - likelihood
---

# 概率与似然（Probability and Likelihood）

概率与似然可以来自同一个表达式，区别在于什么被固定、什么被比较。本页以离散输出为主，避免把连续变量的概率密度与某个结果的概率混用。

## 概率：固定模型，考察结果

设 $p(y\mid x,\theta)$ 是一个条件概率模型。其中，$x$ 是输入或上下文，$y$ 是待预测结果，$\theta$ 是模型参数。固定 $x$ 和 $\theta$ 后，让 $y$ 遍历可能结果，就得到一个分布。对互斥且穷尽的离散结果，其概率非负，合计为 $1$。

例如，一个模型对三个候选结果给出 $0.7$、$0.2$ 和 $0.1$。这些数值回答的是“在这个模型和上下文下，各结果被赋予多少概率”。它们没有直接说明模型参数是否真实，也没有自动说明候选结果是否符合外部事实。

条件本身也是定义的一部分。改变 prompt、历史 token 或模型参数，得到的是另一个条件分布。因此，比较两个概率之前，不能只看数值，还要确认预测事件和给定信息相同。

## 条件概率

对事件 $A$ 和 $B$，当 $P(B)>0$ 时，条件概率（Conditional Probability）定义为：

$$
P(A\mid B)=\frac{P(A\cap B)}{P(B)}
\tag{1}
\label{eq:conditional-event-probability}
$$

这里，$P$ 是概率测度，$A\cap B$ 表示两个事件同时发生。式 $\eqref{eq:conditional-event-probability}$ 将讨论范围限制到 $B$ 已发生的情况，再计算其中 $A$ 所占的概率比例。一般不能交换 $A$ 与 $B$；条件概率也不等于干预产生的因果效果。

在语言模型中，条件通常写成具体上下文，而不是展开为事件集合。给定“今天的天气很”与给定“这道题的答案是”，下一个 token 面临的条件不同，即使 vocabulary 相同也不应期待分布相同。条件概率与概率链式法则的基础说明可参见 Goodfellow et al. (2016)。

## 似然：固定数据，考察参数

设已经观察到数据 $D$。似然（Likelihood）将这些数据固定，把概率模型看成参数 $\theta$ 的函数：

$$
\mathcal{L}(\theta;D)=p(D\mid\theta)
\tag{2}
\label{eq:likelihood-fixed-data}
$$

$\mathcal{L}$ 表示似然函数，分号用于强调 $D$ 已给定。式 $\eqref{eq:likelihood-fixed-data}$ 并没有把条件方向倒过来：右边仍然是参数给定时数据的概率，不是给定数据时参数的概率。

一个简单例子是观察到一次正面结果。若硬币正面概率为 $\theta$，这次观测对应的似然就是 $\mathcal{L}(\theta;D)=\theta$。参数 $0.8$ 对该观测赋予的似然高于参数 $0.2$。但不能据此说“参数为 $0.8$ 的概率是 $0.8$”。似然一般不在参数空间上归一化，也不是参数的后验分布。

“某个回答的 likelihood”是语言模型研究中常见的简写。阅读时应检查作者实际是在固定模型下计算回答分数，还是把它作为训练参数的目标。数值可以一致，统计角色仍有区别。

再考虑十次独立投掷，观察到的有序序列含六次正面、四次反面。固定这段序列时，似然为 $\theta^6(1-\theta)^4$。如果观测只记录“正面出现六次”这个计数事件，则应乘上对应的组合数。对这个例子，组合数不依赖参数，因此不改变最大似然参数；但它改变概率数值。这说明确定观测事件与比较参数的目标同样重要，不能省略因子后还声称保留了原事件的精确概率。

这一例子也提醒我们，文本的精确 token 序列与“表达同一个意思的所有回答”不是同一个事件。前者的概率不会自动等于后者的概率。

## 对数似然

对数似然（Log-Likelihood）定义为：

$$
\ell(\theta;D)=\log\mathcal{L}(\theta;D)
\tag{3}
\label{eq:log-likelihood-definition}
$$

$\ell$ 表示对数似然，本页的 $\log$ 为自然对数。对正似然取对数保留大小顺序，因此最大化似然与最大化对数似然具有相同的最优参数；零似然对应 $-\infty$。若似然已经按适当条件分解为乘积，取对数后就成为求和。

这对长序列尤其有用：直接连乘很小的概率容易发生数值下溢，累加 log probability 更适合实际计算。这里并未假设任意数据都独立；能否写成哪些因子的乘积，必须由模型或概率链式法则说明。

## 与语言模型研究的关系

大语言模型（Large Language Model，LLM）训练时，可以固定已观察到的 token 序列，通过调整参数提高其条件似然；通常的 inference 则固定参数，预测或生成新的 token。神经语言建模中的序列概率与似然训练可参见 Bengio et al. (2003)。

对不确定性量化（Uncertainty Quantification，UQ）而言，模型概率是可用信号，但“模型会生成这个回答”与“这个回答正确”是不同事件。要把分数解释为正确率，需要另行定义正确性目标并验证校准关系。

token 概率如何组成序列概率，见 [Token 与序列概率](../language-models/token-sequence-probability.md)；如何将似然转换为训练和评分目标，见 [负对数似然](negative-log-likelihood.md)。

## 参考文献（References）

- Goodfellow, I., Bengio, Y., Courville, A. (2016). *Deep Learning*. MIT Press. [Book](https://www.deeplearningbook.org/)
- Bengio, Y., Ducharme, R., Vincent, P., Jauvin, C. (2003). *A Neural Probabilistic Language Model*. Journal of Machine Learning Research, 3, 1137–1155. [Paper](https://www.jmlr.org/papers/v3/bengio03a.html)
