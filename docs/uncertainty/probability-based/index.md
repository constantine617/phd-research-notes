---
tags:
  - uq
  - probability
---

# UQ：基于概率的方法

这类方法把 LLM 分配的概率转化为不确定性或置信度信号。基础概率计算见[token 与序列概率](../../foundations/language-models/token-sequence-probability.md)；本页关注如何选择、聚合和解释这些量。模型分配给语言输出的概率，不自动等于输出正确的概率。

## 从单个 token 到序列分数

所选 token 的概率可以帮助定位模型不容易继续的位置。例如，回答中的年份或实体名概率较低，可能值得检查；但罕见且正确的名字也可能低概率。单位置概率必须结合前缀理解，不能脱离上下文解释成一个词“真实”的程度。

序列 log-probability 聚合整条回答在模型分布中的支持程度。直接累加会受到长度影响，因为新增 token 通常增加负对数代价。常见做法是构造平均负对数似然（Negative Log-Likelihood，NLL）分数：

$$
u_{\mathrm{avg}}(x,y)
=-\frac{1}{L}\sum_{t=1}^{L}\log p_\theta(y_t\mid x,y_{<t})
\tag{1}
\label{eq:probability-average-nll}
$$

其中，$x$ 为输入，$y$ 为长度为 $L>0$ 的回答，$y_t$ 是第 $t$ 个 token，$y_{<t}$ 为此前缀，$\theta$ 为模型参数。本页使用自然对数。公式 $\eqref{eq:probability-average-nll}$ 越大，表示这些已选 token 平均越不符合所评分的模型分布；它不是错误概率。

对该分数取负再取指数，得到几何平均 token 概率。这可以作为长度归一化似然分数，但在所有可能回答上通常不归一。[Kuhn et al. (2023)](https://arxiv.org/abs/2302.09664v3 "文献引用") 指出，长度归一化的价值依赖任务：它缓解长度主导的比较，也可能削弱长回答实际包含更多不确定内容的信息。

## 所选概率与分布熵

另一种信号观察每个位置完整 token 分布的分散程度，再沿给定回答前缀平均：

$$
u_{\mathrm{tok}}(x,y)
=\frac{1}{L}\sum_{t=1}^{L}
\left[-\sum_{v\in\mathcal V}p_\theta(v\mid x,y_{<t})\log p_\theta(v\mid x,y_{<t})\right]
\tag{2}
\label{eq:probability-mean-token-entropy}
$$

$\mathcal V$ 是词表，$v$ 遍历候选 token，其余符号同前。取 $0\log0=0$。公式 $\eqref{eq:probability-mean-token-entropy}$ 是沿已给定前缀计算的平均条件熵，不是完整序列空间的精确熵。前者看所有候选有多分散，公式 $\eqref{eq:probability-average-nll}$ 则看实际选出的 token 得到多少概率。

如果某位置的主导候选很稳定，但 sampling 偶尔抽到低概率候选，这两个信号可能方向不同。若要计算完整序列的预测熵（Predictive Entropy），还需要对可能前缀或序列分布进行适当求和或期望，详见[预测熵（Predictive Entropy）](../sampling-based/predictive-entropy.md)。

## 访问与实现条件

读取已生成 token 的 log-probability，通常足以计算公式 $\eqref{eq:probability-average-nll}$；计算公式 $\eqref{eq:probability-mean-token-entropy}$ 则需要完整分布或有明确误差说明的近似。只获得前若干候选时，把剩余候选删除并重新归一，测到的是截断分布的熵，不能冒充原分布。

还要固定评分位置与 token 范围：是否包括结束 token、格式前缀、解释文字和工具输出。对外部回答重新评分需要接口支持相应条件概率；只给生成结果附带分数的接口不一定允许这种操作。不同 tokenizer 和模型分布下，原始分数也不宜直接混用。

[Fadeeva et al. (2023)](https://aclanthology.org/2023.emnlp-demo.41/ "文献引用") 将单次预测的概率信息作为一类容易实现的基线，同时区分多序列与集成方法的资源要求。实际代价取决于概率是否随生成返回、是否需要再次前向计算，以及完整 logits 的保存量。

## 从可计算到有用

概率信号可能受常见措辞、长度和局部语法影响，对事实错误的敏感度需要验证。[Tian et al. (2023)](https://aclanthology.org/2023.emnlp-main.330/ "文献引用") 在其人类反馈 fine-tuning 模型与问答设置中，比较了语言自报置信度（Verbal Confidence）与答案概率基线。其 Label prob. 基线通过多次 sampling 和语义等价判断估计答案频率，并返回众数答案；不能把这一比较写成自报置信度普遍优于本页的 token 概率或序列似然。具体方法与结果范围见[论文笔记](../../papers/2023-tian-verbal-confidence.md)。

当前研究可将这些分数作为可解释、成本清楚的起点，再检查关键主张是否被平均值掩盖、归一化是否改变排序，以及[校准](../../calibration/index.md)能否改善概率解释。若估计对象仍是字符串常见程度，仅做数值变换并不会把它自动变成事实核查器。

## 相关笔记

- [负对数似然基础](../../foundations/probability-information-theory/negative-log-likelihood.md)
- [高效 UQ](../../efficient-uq/index.md)

## 参考文献

- Kuhn, L., Gal, Y., Farquhar, S. (2023). *Semantic Uncertainty: Linguistic Invariances for Uncertainty Estimation in Natural Language Generation*. ICLR. [作者会议版本](https://arxiv.org/abs/2302.09664v3)
- Fadeeva, E., Vashurin, R., Tsvigun, A., et al. (2023). *LM-Polygraph: Uncertainty Estimation for Language Models*. EMNLP: System Demonstrations, 446–461. [Paper](https://aclanthology.org/2023.emnlp-demo.41/)
- Tian, K., Mitchell, E., Zhou, A., Sharma, A., Rafailov, R., Yao, H., Finn, C., Manning, C. D. (2023). *Just Ask for Calibration: Strategies for Eliciting Calibrated Confidence Scores from Language Models Fine-Tuned with Human Feedback*. EMNLP, 5433–5442. [Paper](https://aclanthology.org/2023.emnlp-main.330/)
