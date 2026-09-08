---
tags:
  - uq
  - sampling
  - semantic-uncertainty
---

# 语义熵（Semantic Entropy）

语义熵把含义相同的回答放入同一类别，再度量类别分布的分散程度。它试图减少措辞变化对 UQ 的干扰。例如，“巴黎”与“法国首都是巴黎”在相应问题下表达同一答案；只统计字符串差异，会把这种表达选择当成答案分歧。

## 原始工作与 Nature 后续工作

[Kuhn et al. (2023)](https://arxiv.org/abs/2302.09664v3 "文献引用") 的 *Semantic Uncertainty: Linguistic Invariances for Uncertainty Estimation in Natural Language Generation* 发表于 ICLR 2023，提出语义等价类上的概率聚合及熵估计，主要在自由形式问答中研究它与模型正确性的关系。

[Farquhar et al. (2024)](https://www.nature.com/articles/s41586-024-07421-0 "文献引用") 的 Nature 论文 *Detecting hallucinations in large language models using semantic entropy* 是后续工作。它扩展模型与生成设置，发展句子和长篇主张层面的应用，并明确提出无需 token 概率的离散版本。该文主要关注会随生成改变含义的一类编造性错误（Confabulation），不覆盖所有[幻觉（Hallucination）](../../hallucination/hallucination-definition.md)。

本页以原始论文的语义概率质量定义为基础，并把不同版本的近似计算分开描述。它们共享思想，但具体估计器不能无条件互换。

## 从序列概率到含义概率

给定输入 $x$，设 LLM 在完整回答空间 $\mathcal Y$ 上具有归一分布 $p(y\mid x)$。假定已固定终止规则，并存在相对于当前任务和上下文的语义等价关系。其等价类构成划分 $\mathcal C_x$，任意类别 $c$ 的概率质量为：

$$
p(c\mid x)=\sum_{y\in c}p(y\mid x)
\tag{1}
\label{eq:semantic-class-mass}
$$

$y$ 是完整序列，$c$ 包含所有表达相同相关含义的序列，而不只是已经抽到的几条回答。由公式 $\eqref{eq:semantic-class-mass}$ 诱导的语义熵是：

$$
H_{\mathrm{sem}}(x)=-\sum_{c\in\mathcal C_x}p(c\mid x)\log p(c\mid x)
\tag{2}
\label{eq:semantic-entropy}
$$

本页使用自然对数，单位为 nat，并取 $0\log0=0$。这对应 [Kuhn et al. (2023)](https://arxiv.org/abs/2302.09664v3 "文献引用") 的式（2）和式（3）。等价划分会合并概率质量；在同一归一分布与确定划分下，类别熵不会超过原序列熵。实际估计值若来自不同归一化或不同分布，则不能直接套用该比较。

原论文给出的简化例子中，三个字符串概率分别为 $0.5$、$0.4$、$0.1$。前两个都回答巴黎时，语义类别概率变为 $0.9$ 和 $0.1$，熵从约 $0.94$ 降为约 $0.33$。这是合并同义表达后的变化，不表示正确率被提高了多少。

## 如何近似语义等价

自然语言推断（Natural Language Inference，NLI）判断一个文本是否蕴含另一个文本。原始方法使用双向蕴含（Bidirectional Entailment）：在问题上下文下，两个回答必须互相蕴含，才视为同义。主题相似或仅单向蕴含并不足够。

[Kuhn et al. (2023)](https://arxiv.org/abs/2302.09664v3 "文献引用") 使用在 MNLI 上 fine-tuning 的 DeBERTa-large，把问题与各回答拼接后判断两个方向。聚类过程将新回答与已有类别的代表回答比较；无法匹配时建立新类。其抽象依据是等价关系的传递性，但学习得到的判定器并不保证严格满足自反、对称和传递性质。

[Farquhar et al. (2024)](https://www.nature.com/articles/s41586-024-07421-0 "文献引用") 比较了专用 NLI 模型与通过 prompt 作蕴含判断的 LLM。句子长度问答实验选择 GPT-3.5 作为蕴含判定器；长篇传记的主张核查则使用 DeBERTa，并放宽为至少一个方向蕴含且没有方向被判矛盾。**不能把这项主张层面的放宽规则，写成所有语义熵实验都采用严格双向蕴含。**

因此，聚类依赖上下文拼接、代表选择、输入顺序和判定模型。日期、否定、数值范围或跨语言表达容易使误差具有系统性。模型判成“等价”仍是近似观察，不是形式逻辑证明。

## 理论定义与实际估计分开

全体序列和全部语义类无法枚举。原始论文先生成有限回答、合并同类序列的似然，再在式（4）中写出对语义类负 log-probability 的蒙特卡洛平均；部分实验还使用长度归一化。

标准 Monte Carlo 平均的无偏解释，需要被平均的类别样本来自目标类别分布，且类别概率正确。若把已经去重的类别均匀平均，或只用有限样本的似然和替代整个类别质量，就不能直接继承该解释。原文的类别集合记号与抽样索引需要结合算法阅读；本页不把“有限样本聚类后算出的分数必然无偏”作为结论。

Nature 后续工作在 Methods 式（5）中明确对观察到的类别权重归一，再计算加权熵。用本页记号可写成：

$$
\hat\pi_j=\frac{w_j}{\sum_{i=1}^{M}w_i},\qquad
\widehat H_{\mathrm{sem},w}=-\sum_{j=1}^{M}\hat\pi_j\log\hat\pi_j
\tag{3}
\label{eq:semantic-normalized-estimator}
$$

$M$ 为观察到的类别数，$w_j\geq0$ 是按所选序列评分规则累积的类别权重，且总权重大于零。公式 $\eqref{eq:semantic-normalized-estimator}$ 是有限观察下的类别分布估计。Nature 论文特别指出，长度归一化可能使未归一的类别权重大于一，因此需要这一步；但归一化本身并不证明权重就等于公式 $\eqref{eq:semantic-class-mass}$ 的真实质量。

没有序列概率时，[Farquhar et al. (2024)](https://www.nature.com/articles/s41586-024-07421-0 "文献引用") 的离散语义熵（Discrete Semantic Entropy）使用样本频率：

$$
\hat\pi_j^{\mathrm{freq}}=\frac{n_j}{K},\qquad
\widehat H_{\mathrm{sem},\mathrm{freq}}=-\sum_{j=1}^{M}\frac{n_j}{K}\log\frac{n_j}{K}
\tag{4}
\label{eq:semantic-frequency-estimator}
$$

$K$ 为生成次数，$n_j$ 为第 $j$ 类的样本数，且 $\sum_jn_j=K$。公式 $\eqref{eq:semantic-frequency-estimator}$ 将每次抽样按次数计入，估计当前 sampling 分布诱导的类别频率。它不要求知道 token 概率，但仍需要语义判定器。其与概率加权版本是否接近，取决于抽样分布、评分与近似条件，不能只由“大样本”三个字保证。

## 需要哪些输入、有哪些成本，结果能说明什么？

运行前应固定目标回答、prompt、模型、sampling 参数、样本数、长度处理、重复样本处理及聚类器。长篇主张级应用还会增加拆分主张、生成核查问题和重新作答的调用，不能按一个短问答流程估算全部成本。

高语义熵可能来自猜测，也可能来自歧义或多个合法答案。低语义熵可能来自充分掌握，也可能来自重复同错、过低温度或样本遗漏。聚类误差又会导致误合并或误拆分。该方法提供的是语义变化信号，不是事实真值判定，也不是经过校准的错误概率。

当前研究中，语义熵适合作为含义层面的比较基线。研究重点应包括估计版本、样本预算和语义判定误差如何影响错误识别，再考察能否通过更便宜的信号近似其有用部分。

## 相关笔记

- [预测熵](predictive-entropy.md)
- [高效 UQ](../../efficient-uq/index.md)
- [基于表征的 UQ](../../representation-uq/index.md)

## 参考文献

- Kuhn, L., Gal, Y., Farquhar, S. (2023). *Semantic Uncertainty: Linguistic Invariances for Uncertainty Estimation in Natural Language Generation*. ICLR. [作者会议版本](https://arxiv.org/abs/2302.09664v3)
- Farquhar, S., Kossen, J., Kuhn, L., Gal, Y. (2024). *Detecting hallucinations in large language models using semantic entropy*. Nature, 630, 625–630. [Paper](https://www.nature.com/articles/s41586-024-07421-0)
