---
tags:
  - paper-note
  - llm
  - uq
  - calibration
---

# Improving Semantic Uncertainty Quantification in Language Model Question-Answering via Token-Level Temperature Scaling

## 文献信息

- 作者：Tom A. Lamb、Desi R. Ivanova、Philip H. S. Torr、Tim G. J. Rudner。
- 年份：2026；本笔记固定依据 arXiv:2604.07172v1。
- 原始来源：[论文 v1](https://arxiv.org/abs/2604.07172v1)、[PDF](https://arxiv.org/pdf/2604.07172v1)。
- 作者资源：[项目页](https://tomalamb.github.io/semantic-calibration-via-temp-scaling/)、[代码](https://github.com/tomalamb/semantic-calibration)。代码尚未审计或运行。

## 研究问题

[Lamb et al. (2026)](https://arxiv.org/abs/2604.07172v1 "文献引用")研究：计算语义不确定性时，生成温度是否应该通过数据学习，而不是一直固定为 0.5 或 1.0？方法冻结 LLM，在参考答案上拟合一个 token 层温度，再用独立验证集选择语义层表现较好的配置。

理解这篇论文时，重点是温度如何影响完整答案、语义簇和最终评价。它不是对已经固定的语义分数做一次单调变换，也没有直接以语义熵或 AUROC 为训练损失。实验支持它在多项短答案问答设置中有效，但不支持“所有指标都改善”。

## 从生成答案到语义分布

### 经验语义置信度（Empirical Semantic Confidence，E-SC）

对同一个问题及 prompt $x$ 采样 $m$ 个答案，用 NLI 双向蕴含判断语义等价，形成 $C_1,\ldots,C_k$。E-SC 按每个簇的样本数赋予概率，再计算语义熵：

$$
q_i=\frac{|C_i|}{m},\qquad
H_{\mathrm{sem}}(x)=-\sum_{i=1}^{k}q_i\log q_i.
\tag{1}\label{eq:lamb-empirical-entropy}
$$

$k$ 是本次采样形成的簇数，$|C_i|$ 是簇内样本数，采用自然对数。例如，10 个答案中有 8 个表达同一含义，该簇的 E-SC 为 0.8；这是解释公式的例子，不是实验结果。

簇概率归一化不意味着它等于事实正确概率。模型反复给出同一个错误答案仍可能产生低熵；未采样到某种含义也不代表模型对它的真实概率为零。论文还比较了其他簇赋分方式，因此式 $\eqref{eq:lamb-empirical-entropy}$ 的频数定义不能代替所有方法。

## 温度作用在哪里

### Token 层温度缩放（Token-Level Temperature Scaling）

给定前缀 $x,y_{<t}$，设词表为 $V$、token $v$ 的 logit 为 $z_{t,v}$，温度 $\tau>0$ 对应：

$$
p_\tau(y_t=v\mid x,y_{<t})=
\frac{\exp(z_{t,v}/\tau)}{\sum_{u\in V}\exp(z_{t,u}/\tau)}.
\tag{2}\label{eq:lamb-token-temperature}
$$

一个选定配置内，所有问题和 token 位置共享同一温度，LLM 权重不变。下文表格沿用原文方法名 TS。较高温度使固定前缀下的分布变平，较低温度使其更集中，但不能据此认定所有任务都需要提高温度。

### Token 排序不变，为什么 AUROC 会变

对于固定前缀，正标量温度不会改变 token 的排序；其余规则相同时，纯 greedy decoding 的选择也不变。但是 sampling 概率改变后，可能抽到不同 token，随后前缀和 logits 都会变化。完整答案及其语义簇频数因而改变，跨问题的置信度与熵排序也可能改变。

所以“同一前缀下 token 排序不变”不能推出“不同问题的语义置信度（Semantic Confidence）排序不变”。若只对一组固定的最终分数施加严格单调映射，其排序不会改变；本文重新生成并重新计算语义分布，属于不同情形。

## 温度如何学习与选择

### 在参考答案上拟合候选温度

令 $D_{\mathrm{cal,train}}$ 为校准训练集，$y$ 为参考答案。用求和形式说明 NLL 目标：

$$
\tau^*_{\mathrm{candidate}}=
\operatorname*{arg\,min}_{\tau>0}
\sum_{(x,y)\in D_{\mathrm{cal,train}}}
\sum_{t=1}^{|y|}-\log p_\tau(y_t\mid x,y_{<t}).
\tag{3}\label{eq:lamb-nll-fit}
$$

这里使用参考前缀，不是对模型自己生成的答案进行无标签学习。式 $\eqref{eq:lamb-nll-fit}$ 说明优化对象；具体实现的 token mask 与损失归约方式仍需核对作者代码。

### 选择性平滑（Selective Smoothing，SS）

SS 是另一个候选训练目标。令 $\hat y_t$ 为最高概率 token，$\beta$ 为平滑权重，省略共同条件 $x,y_{<t}$：

$$
\begin{aligned}
\ell_{\mathrm{SS}}={}&-(1-\beta)\log p_\tau(y_t)
\mathbf{1}[\hat y_t=y_t]\\
&-\frac{\beta}{|V|}\sum_{v\in V}\log p_\tau(v)
\mathbf{1}[\hat y_t\ne y_t].
\end{aligned}
\tag{4}\label{eq:lamb-selective-smoothing}
$$

这里的正确与错误只指 token top-1 是否等于参考 token，不指完整回答是否事实正确。错误分支使用均匀目标对预测分布的交叉熵，最小化时推动预测趋于均匀。它与直接最大化预测熵具有相同的均匀最优点，但目标函数和梯度不同。符号 $\beta$ 用于区分后面簇赋分中的参数 $\alpha$。

### 在独立验证集上选择配置

对候选配置重新生成答案、聚类并计算语义置信度，以验证集 Brier 分数（Brier Score）选择配置：

$$
\widehat{\mathrm{BS}}=\frac{1}{N}\sum_{j=1}^{N}(c_j-r_j)^2.
\tag{5}\label{eq:lamb-brier-selection}
$$

$N$ 是验证样本数，$c_j$ 为所选答案簇的置信度，$r_j\in\{0,1\}$ 是对应正确性标签。Token 层损失负责拟合，语义层 Brier 分数负责选择，测试集负责最终评价。

每个模型、数据集分别校准，并针对每种语义置信度赋分方式选择配置。“一个标量”指单个最终配置的参数量，不是所有任务共用一个温度。

```text
校准训练集：冻结 LLM，拟合候选配置
→ 独立校准验证集：生成、聚类，以 Brier 分数选配置
→ 测试集：冻结配置，每题采样 10 个回答
→ 清理答案，聚类并计算簇置信度与语义熵
→ 按既定协议选答案，用测试参考答案评价
```

## 其他簇赋分方式如何比较

正文 §3 共比较七种方式。令 $a_y=p(y\mid x)^{1/|y|}$ 为长度归一化序列似然，$s_i=\sum_{y\in C_i}a_y$、$\pi_i=|C_i|/m$、$L_i=\prod_{y\in C_i}a_y$。下表保留论文方法标识；每种未归一化权重 $w_i$ 都需除以 $\sum_j w_j$。

| 方法标识 | 权重 $w_i$ | 作用 |
|---|---|---|
| E-SC | $\lvert C_i\rvert$ | 按样本频数赋分 |
| L-SC | $s_i$ | 累加长度归一化似然 |
| ML-SC | $s_i/\lvert C_i\rvert$ | 改用簇内均值 |
| B-SC | $\pi_iL_i$ | 组合经验先验与似然乘积 |
| T-SC | $(\pi_iL_i)^{1/\alpha}$ | 对组合权重调幂 |
| IC-SC | $s_i\exp[-H(r_i)]$，$r_i(y)=a_y/s_i$ | 用簇内归一化权重的熵调整分数 |
| G-SC | $\pi_i\exp(-\alpha E_i)$，$E_i=-\log L_i$ | 用 NLL 定义能量 |

这里 $\alpha>0$ 是赋分方式自身的参数，与 token 温度 $\tau$ 不同。长度归一化似然的聚合不是对全部可能序列求和得到的严格语义概率；B-SC 也只应按作者的贝叶斯启发式方法理解。

上述 G-SC 采用正文完整定义：Figure 2 的简式省略先验与尺度，图注的能量描述也与正文不同。IC-SC 的文字解释同样需要谨慎：固定簇大小与 $s_i$ 时，$r_i$ 越均匀，熵越高，惩罚反而越强；不能把公式解释成“似然越不一致，分数必然越低”。这两处尚未通过代码确定实现，核心 E-SC 与温度学习流程不依赖消除这些疑点。

## 最终答案与语义熵怎样对应

| 原文协议 | 被评价的答案 | 熵的来源 |
|---|---|---|
| $\mathrm{SE}_{\mathrm{vanilla}}$ | 另外进行 greedy decoding 得到的答案 | 温度采样形成的语义分布 |
| $\mathrm{SE}_{\mathrm{conf}}$ | 最高置信度簇中的答案 | 同一语义分布 |

最高置信度簇只有在 E-SC 下才等同于最大簇。原文 §4 的评估从最高置信度簇随机取最多四个回答，只要其中一个通过正确性检查就判为正确。考虑到聚类和词面判断误差，这不严格等同于部署时任取一个回答的正确率，也不是用测试参考答案替模型选择输出。

比较温度时应固定答案协议；比较两种协议时应固定温度配置。否则，结果差异同时包含生成分布和被评价答案的变化，不能全部归因于温度。

## 实验条件与结果

### 数据与评价设置

模型为 Llama-3.1-8B-Instruct、Qwen-2.5-7B-Instruct、Ministral-8B-Instruct-2410。TriviaQA 与 Natural Questions 为闭卷问答，SQuAD 提供 passage；prompt 要求简短答案。

| 数据集 | 校准训练 | 校准验证 | 测试 | Prompt 示例数 |
|---|---:|---:|---:|---:|
| TriviaQA | 59,374 | 2,000 | 2,000 | 10 |
| Natural Questions | 61,600 | 2,000 | 2,000 | 10 |
| SQuAD | 59,577 | 2,000 | 2,000 | 4 |

附录 A.2 报告两轮训练、AdamW、余弦学习率调度及首轮前 10% 预热。TS 初始温度为 1，学习率为 $10^{-4}$；候选目标包含 NLL 与 SS，SS 权重包括 0.1、0.25、0.5、0.75。

主实验每题采样 10 次，运行四次，主要结果表报告均值与标准误。聚类使用 DeBERTa-V2-XXLarge 的双向蕴含；正确性则采用直接或模糊词面匹配、SQuAD F1 阈值及数字／日期处理。NLI 聚类与答案正确性判断是不同步骤。

### 置信度校准与区分能力

自适应校准误差（Adaptive Calibration Error，ACE）越小越好，置信度 AUROC 越大越好。下表来自 Table 5，固定为 **Qwen、E-SC**，只摘录均值；原表包含标准误。

自适应温度缩放（Adaptive Temperature Scaling，ATS）用额外 head 预测 token 位置相关的温度；本文 Platt 使用词表维度的对角仿射变换，具有 $2|V|$ 个尺度与偏置参数。它不是仅对最终置信度拟合一个 sigmoid。

| 方法 | TriviaQA ACE ↓ | TriviaQA AUROC ↑ | SQuAD ACE ↓ | SQuAD AUROC ↑ |
|---|---:|---:|---:|---:|
| Fixed 1.0 | 0.308 | 0.763 | 0.051 | 0.534 |
| Fixed 0.5 | 0.165 | 0.832 | 0.050 | 0.618 |
| Platt | 0.170 | 0.837 | 0.050 | 0.619 |
| ATS | 0.204 | 0.841 | 0.056 | 0.523 |
| TS | 0.080 | 0.855 | 0.058 | 0.726 |

TriviaQA 两项指标都改善；SQuAD 相对 Fixed 0.5 的 ACE 从 0.050 增至 0.058，而 AUROC 从 0.618 增至 0.726。校准与区分能力必须分别解释。

### 语义熵与答案选择

下表来自 Table 1，固定为 **Qwen、SQuAD、E-SC 导出的熵**，同样只列均值。

| 温度 | Vanilla 熵 AUROC ↑ | Conf 熵 AUROC ↑ |
|---|---:|---:|
| Fixed 1.0 | 0.590 | 0.569 |
| Fixed 0.5 | 0.653 | 0.666 |
| Learned TS | 0.707 | 0.780 |

这里的 0.780 使用整个簇分布的熵；上一表的 0.726 使用最高簇置信度，两者不是同一指标。并非每个设置都有提升：Table 1(b) 中 Qwen／Natural Questions／L-SC 的 vanilla 熵 AUROC，TS 为 0.737，Fixed 0.5 为 0.745。

## 为什么简单校准器可能更有效

作者报告 ATS 可达到很低的训练损失，但语义层泛化较弱，并提出它可能过度拟合常见、语义贡献较弱的 token。单一共享温度限制局部调整能力，可能形成有利的归纳偏置。

这是有实验现象支持的机制解释，不是已确立的因果结论。Token 层拟合良好也不保证语义层校准良好；本文采用独立语义验证选择，正是这两个层级不能直接等同的体现。

## 局限与研究启发

一个参数不等于整个系统成本低：仍需要约六万条任务相关校准样本、多次生成和 NLI 聚类。样本数消融主要在 Llama／Natural Questions 上进行，约 10 次后校准收益趋缓，不能推广成所有任务都只需 10 次。

论文没有证明温度可跨任务直接迁移，也没有验证长文、LVLM 或医学场景。对这些方向的迁移只能作为待研究问题。低熵仍可能来自稳定错误，簇概率也仍需相对于明确正确性事件评价。

对后续研究，值得保留的比较基线是：在固定模型、数据、采样预算、聚类器和答案协议下，比较固定温度与学习温度，并分别报告置信度校准、错误检测和总成本。本笔记只整理作者结果与方法推导，未执行作者代码或产生复现实验。复现前仍需核对正温度参数化、采样截断、重复样本处理、似然重新评分及 ACE 分箱细节。

## 相关笔记

- [语义熵](../uncertainty/sampling-based/semantic-entropy.md)
- [后处理校准](../calibration/post-hoc-calibration.md)
- [Brier 分数](../evaluation/calibration-metrics/brier-score.md)
- [AUROC](../evaluation/detection-metrics/auroc.md)
- [Guo 2017：温度缩放](2017-guo-calibration.md)
- [Farquhar 2024：语义熵与幻觉检测](2024-farquhar-semantic-entropy.md)

## 参考文献

- Lamb, T. A., Ivanova, D. R., Torr, P. H. S., Rudner, T. G. J. (2026). *Improving Semantic Uncertainty Quantification in Language Model Question-Answering via Token-Level Temperature Scaling*. arXiv:2604.07172v1. [原文](https://arxiv.org/abs/2604.07172v1)
