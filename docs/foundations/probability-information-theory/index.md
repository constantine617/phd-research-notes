---
tags:
  - foundations
  - probability
  - information-theory
---

# 概率与信息论（Probability & Information Theory）

本节整理理解模型概率、训练目标和可靠性信号所需的数学基础。重点是分清每个量的计算对象、条件和解释范围，为后续不确定性量化（Uncertainty Quantification，UQ）与置信度校准（Confidence Calibration）研究建立共同语言。

概率描述给定条件下各个结果的可能性。似然使用同一个概率模型，但固定已经观察到的数据，比较不同参数对这些数据的解释程度。熵描述一个分布自身的不确定程度；交叉熵则使用一个分布提供的概率，对另一个分布下的结果计算平均负对数分数。负对数似然（Negative Log-Likelihood，NLL）从观测数据出发构造目标。KL 散度（Kullback–Leibler Divergence，KL Divergence）比较两个分布，并保留比较方向。

这些概念在计算上紧密相连，却不能互换名称。读到一个 uncertainty score 时，应先确认它针对单个 token、完整回答还是其他随机变量；使用模型原始概率，还是经过 sampling 设置改变后的分布；最后进行求和还是平均。同样，训练损失较小、输出分布集中与回答事实正确，是需要分别判断的事情。

这些页面以定义和小例子为主，不展开具体 uncertainty 方法，也不把数学量直接命名为可信度。

## 页面导航

- [概率与似然](probability-likelihood.md)：区分固定模型预测结果与固定数据比较参数。
- [熵](entropy.md)：理解分布不确定性的定义、单位和边界。
- [交叉熵](cross-entropy.md)：明确目标分布与预测分布的不同角色。
- [负对数似然](negative-log-likelihood.md)：连接数据似然、序列评分和长度处理。
- [KL 散度](kl-divergence.md)：理解有方向的分布差异与零概率问题。

本节默认使用自然对数，并以离散变量为主；连续分布需要额外区分概率与密度。具体的语言模型概率计算见 [Token 与序列概率](../language-models/token-sequence-probability.md)。Goodfellow et al. (2016) 的第 3 章为这些概率与信息论概念提供了统一背景。

## 参考文献（References）

- Goodfellow, I., Bengio, Y., Courville, A. (2016). *Deep Learning*. MIT Press. [Book](https://www.deeplearningbook.org/)
