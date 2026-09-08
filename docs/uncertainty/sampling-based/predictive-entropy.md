---
tags:
  - uq
  - sampling
  - probability
---

# 预测熵（Predictive Entropy）

预测熵描述给定输入后，某个预测结果分布有多分散。用于 LLM 的 UQ 时，关键问题是随机结果究竟代表 token、完整回答，还是提取后的答案。Shannon 熵的基础性质复用[熵页面](../../foundations/probability-information-theory/entropy.md)；本页聚焦生成任务中的对象与估计。

## 先给出结果空间

固定输入 $x$，令离散随机变量 $Y$ 取值于 $\mathcal Y$，其预测概率为 $p(y\mid x)$。定义：

$$
H(Y\mid x)=-\sum_{y\in\mathcal Y}p(y\mid x)\log p(y\mid x)
\tag{1}
\label{eq:predictive-entropy}
$$

$y$ 为一个可能结果，$\log$ 使用自然对数，单位为 nat，并约定 $0\log0=0$。这里的 $H(Y\mid x)$ 是固定输入处的条件分布熵，不是进一步对输入随机变量取平均后的条件熵。有限结果空间的均匀分布有最大熵；无限空间不一定存在有限上界。

如果 $Y$ 是下一 token，结果空间为词表；如果它是完整序列，则需要规定结束与截断方式。若模型可能不终止，有限完整字符串的概率未必构成归一分布，必须补充停止机制或其他结果状态。若 $Y$ 是规范化答案，则还需要定义从文本映射到答案的规则。

## 概率可计算时的蒙特卡洛（Monte Carlo，MC）估计

如果独立样本 $y^{(1)},\ldots,y^{(K)}$ 确实来自公式 $\eqref{eq:predictive-entropy}$ 中的 $p$，且能够计算相同分布下的完整结果概率，可以使用蒙特卡洛均值：

$$
\widehat H_{\mathrm{MC}}=-\frac{1}{K}\sum_{k=1}^{K}\log p(y^{(k)}\mid x)
\tag{2}
\label{eq:predictive-entropy-mc}
$$

$K$ 为样本数。对于熵有限的目标分布，公式 $\eqref{eq:predictive-entropy-mc}$ 的期望等于目标熵；有限样本估计仍有波动。[Kuhn et al. (2023)](https://arxiv.org/abs/2302.09664v3 "文献引用") 将序列生成中的熵估计与长度归一化问题分别讨论。

如果样本来自经温度或截断修改后的 $q$，却使用原始模型 $p$ 的 log-probability，平均值对应的是在 $q$ 下评价 $p$ 的交叉熵型量，通常不是 $H(p)$，也不是 $H(q)$。若要修正抽样分布差异，还需满足支持集和权重等条件，不能省略这一步解释。

## 只有样本文本时的经验分布

对于已经定义的离散类别，可以按频率估计 $\hat p(y)=n_y/K$，其中 $n_y$ 为结果 $y$ 出现次数，再计算：

$$
\widehat H_{\mathrm{freq}}=-\sum_{y:n_y>0}\frac{n_y}{K}\log\frac{n_y}{K}
\tag{3}
\label{eq:predictive-entropy-frequency}
$$

公式 $\eqref{eq:predictive-entropy-frequency}$ 是经验分布的代入估计，不是公式 $\eqref{eq:predictive-entropy-mc}$。在固定有限类别及适当抽样条件下，它可随样本数增加而一致，但有限样本通常低估分布熵，未见类别尤其影响结果。

完整语言序列空间很大，少量回答往往每条都不同。此时每个出现过的字符串只有一次，经验熵就是 $\log K$，难以区分它们的真实概率结构。按语义合并可以缓解表面表达造成的稀疏性，但也改变了随机变量，详见[语义熵（Semantic Entropy）](semantic-entropy.md)。

## 常见近似不可混称

平均 token 分布熵是在给定回答前缀上作局部统计；序列蒙特卡洛熵对完整生成结果取期望；按答案频率计算的熵则依赖答案映射。另有方法平均长度归一化后的负 log-probability，实际测得的是每 token 型分数。它们有各自用途，但不是同一个定义的可随意替代实现。

[Fadeeva et al. (2023)](https://aclanthology.org/2023.emnlp-demo.41/ "文献引用") 的 LM-Polygraph 同时实现多种信息类信号，也提示复现实验时需要记录具体估计器。仅写“使用 predictive entropy”，不足以说明所需访问能力或数值含义。

## 结果如何解释

高预测熵表示所定义结果分布更分散。它可能来自事实猜测，也可能来自同义表达、多个有效答案或开放任务。低熵则可能意味着正确答案稳定，也可能意味着稳定错误。当前研究需要先固定结果空间与估计版本，再分析它们与任务错误之间的经验关系。

## 相关笔记

- [基于概率的 UQ](../probability-based/index.md)
- [LLM 中的不确定性](../foundations/uncertainty-in-llms.md)

## 参考文献

- Kuhn, L., Gal, Y., Farquhar, S. (2023). *Semantic Uncertainty: Linguistic Invariances for Uncertainty Estimation in Natural Language Generation*. ICLR. [作者会议版本](https://arxiv.org/abs/2302.09664v3)
- Fadeeva, E., Vashurin, R., Tsvigun, A., et al. (2023). *LM-Polygraph: Uncertainty Estimation for Language Models*. EMNLP: System Demonstrations, 446–461. [Paper](https://aclanthology.org/2023.emnlp-demo.41/)
