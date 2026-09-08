---
tags:
  - foundations
  - llm
  - decoding
  - sampling
---

# 解码与 Sampling（Decoding and Sampling）

LLM 给出下一个 token 的概率分布后，还需要决定实际输出哪一个 token。decoding 是完成这一选择并逐步形成序列的过程；sampling 是其中使用随机选择的一类方式。

本页用 $c_t$ 表示第 $t$ 步的上下文，用 $p$ 表示原始模型分布，用 $q$ 表示经过生成设置调整后、实际用于 sampling 的分布。关于 logits 与原始概率的关系，见[Token 与序列概率](token-sequence-probability.md)。

## 贪心 decoding（Greedy Decoding）

贪心 decoding 每一步都选择概率最高的 token：

$$
y_t
=
\arg\max_{i\in\{1,\ldots,V\}}p(y_t=i\mid c_t).
\tag{1}
\label{eq:greedy-decoding}
$$

其中，$V$ 是词表大小，$i$ 是候选 token 编号，$y_t$ 是选出的 token。当多个候选并列最高时，需要使用固定规则选择其中一个。在模型、输入、并列处理和计算条件固定时，这是一种确定性的选择方式。

逐步选择局部最高概率，不保证得到全局概率最高的序列。例如，只考虑两个输出位置：第一步 token $A$ 和 $B$ 的概率分别为 $0.6$ 和 $0.4$；接在它们后面时，最高的第二步条件概率分别为 $0.5$ 和 $0.9$。贪心 decoding 会先选 $A$，对应路径概率为 $0.3$，而经过 $B$ 的候选路径概率可达 $0.36$。

## 温度（Temperature）调整什么

温度通过缩放 logits 改变概率分布的集中程度。这里使用 $\tau$ 表示 temperature，避免与其他页面的序列长度 $T$ 混淆：

$$
q_{\tau}(i\mid c_t)
=
\frac{\exp(z_{t,i}/\tau)}
{\sum_{j=1}^{V}\exp(z_{t,j}/\tau)},
\qquad \tau>0.
\tag{2}
\label{eq:temperature-softmax}
$$

其中，$z_{t,i}$ 是第 $i$ 个 token 的原始 logit，$q_{\tau}$ 是调整后的分布。若没有其他生成调整，$\tau=1$ 时它等于原始模型分布。[Holtzman et al. (2020)](https://iclr.cc/virtual_2020/poster_rygGQyrFvH.html "文献引用") 在比较文本生成策略时讨论了这种 temperature 调整。

- $0<\tau<1$：logits 之间的相对差异被放大，高 logit 候选占据更多概率质量，分布更集中。
- $\tau=1$：保持原始 softmax 分布。
- $\tau>1$：logits 差异被缩小，候选间的概率差距减小，分布更平缓。

例如，两个候选的原始概率为 $0.8$ 和 $0.2$。仅进行 temperature 调整后，$\tau=0.5$ 时概率约为 $0.941$ 和 $0.059$；$\tau=2$ 时约为 $0.667$ 和 $0.333$。

对正的 temperature，候选的排序不变。因此，如果仍然采用 greedy 选择，仅调整 temperature 不会改变最高概率 token。公式本身没有定义 $\tau=0$；当最大 logit 唯一时，$\tau\to0^+$ 的极限集中在该候选上，软件中的零值设置应按具体实现解释。

## Sampling 怎样产生不同输出

Sampling 按当前分布随机抽取 token。例如，某候选的概率是 $0.2$，表示在同一条件下重复进行该步随机选择时，它被抽中的比例趋向 $0.2$，而不是保证每五次恰好出现一次。

同一个 prompt 可以因为某一步抽到了不同 token 而走向不同的后续上下文。即使每一步采用相同规则，整段回答也可能不同。

## Top-k 与 Top-p

### Top-k

Top-k 保留当前概率最高的 $k$ 个 token，排除其他候选，并在保留项中重新归一化后进行 sampling。这里 $1\leq k\leq V$。候选数量固定，但保留的总概率质量会随上下文改变；在固定并列处理规则下，$k=1$ 等价于选择最高概率 token。

### Top-p / Nucleus Sampling

[Holtzman et al. (2020)](https://iclr.cc/virtual_2020/poster_rygGQyrFvH.html "文献引用") 提出了 nucleus sampling。它先按概率从高到低排序，再保留累计概率首次达到或超过指定阈值的最短前缀候选集合。

该策略通常称为 top-p。为避免把阈值与模型分布 $p$ 混淆，本页将阈值记为 $\rho\in(0,1]$。如果排序后的概率为 $0.5$、$0.3$、$0.15$、$0.05$，当 $\rho=0.75$ 时保留前两个候选；当 $\rho=0.9$ 时保留前三个。

与 top-k 相比，top-p 固定的是累计概率门槛，候选数量可以变化。这里的“最小集合”指按降序排序后达到门槛的最短前缀；相同概率的边界项需按实现规定处理。

### 截断后需要重新归一化

无论由 top-k 还是 top-p 得到候选集合 $S_t$，排除其他 token 后，都需要将保留项的概率重新缩放：

$$
q(i\mid c_t)
=
\begin{cases}
\displaystyle
\frac{q_{\tau}(i\mid c_t)}
{\sum_{j\in S_t}q_{\tau}(j\mid c_t)}, & i\in S_t,\\
0, & i\notin S_t.
\end{cases}
\tag{3}
\label{eq:truncated-sampling-distribution}
$$

其中，$S_t$ 是当前保留的非空 token 集合，$q$ 是最终用于 sampling 的分布。本页以先调整 temperature、再选择候选集合为约定。实际系统若组合多种规则，需要记录其具体顺序。

## 与不确定性研究的关系

UQ 中的多回答分析依赖所生成的样本。由公式 $\eqref{eq:temperature-softmax}$ 和 $\eqref{eq:truncated-sampling-distribution}$ 可知，改变 temperature 或截断规则，就可能改变回答的分布，进而影响从回答集合计算的统计量。

Sampling 中的随机变化也不能直接等同于模型对正确答案的不确定性。不同文本可能表达同一个意思。[Farquhar et al. (2024)](https://www.nature.com/articles/s41586-024-07421-0 "文献引用") 在研究不确定性时明确区分了具体措辞的变化与答案含义的变化。

因此，比较不同研究的 UQ 结果时，需要检查 temperature、top-k、top-p、生成次数、长度上限和停止规则，并说明概率来自调整前的 $p$ 还是实际生成所用的 $q$。多次 greedy 得到相同输出，也不能单独证明回答正确。

## 参考文献

- Holtzman, A., Buys, J., Du, L., Forbes, M., Choi, Y. (2020). *The Curious Case of Neural Text Degeneration*. International Conference on Learning Representations. [Conference](https://iclr.cc/virtual_2020/poster_rygGQyrFvH.html) · [Paper](https://arxiv.org/abs/1904.09751)
- Farquhar, S., Kossen, J., Kuhn, L., Gal, Y. (2024). *Detecting hallucinations in large language models using semantic entropy*. Nature, 630, 625–630. [Paper](https://www.nature.com/articles/s41586-024-07421-0)
