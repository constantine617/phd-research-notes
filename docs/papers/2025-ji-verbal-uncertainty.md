---
tags:
  - paper-note
  - llm
  - uq
  - hallucination
  - representation
  - calibration
---

# Calibrating Verbal Uncertainty as a Linear Feature to Reduce Hallucinations

## 文献信息

- 作者：Ziwei Ji、Lei Yu、Yeskendir Koishekenov、Yejin Bang、Anthony Hartshorn、Alan Schelten、Cheng Zhang、Pascale Fung、Nicola Cancedda；前两位共同一作。
- 发表：EMNLP 2025，3769–3793 页。本笔记以正式会议版本为准。
- 原始来源：[ACL Anthology](https://aclanthology.org/2025.emnlp-main.187/)、[论文 PDF](https://aclanthology.org/2025.emnlp-main.187.pdf)。
- DOI：[10.18653/v1/2025.emnlp-main.187](https://doi.org/10.18653/v1/2025.emnlp-main.187)。
- 作者代码：[verbal_uncertainty_feature_calibration](https://github.com/facebookresearch/verbal_uncertainty_feature_calibration)。本笔记未运行复现实验。

## 研究问题

[Ji et al. (2025)](https://aclanthology.org/2025.emnlp-main.187/ "文献引用")研究的不是单纯让 LLM 更常说“我不确定”，而是让回答措辞更好地反映模型在语义层面的不确定性。模型可能针对同一个问题采样出互相矛盾的答案，却用十分确定的语气表达每个答案。

论文先寻找控制这种措辞的内部方向，再把语义不确定性和言语不确定性（Verbal Uncertainty）用于幻觉检测，最后只对被判定有风险且存在正向不确定性差值的问题干预生成。需要区分：**让错误回答听起来更谨慎，可以减少自信幻觉（Confident Hallucination），但不等于把错误事实改正确。**

## 两种不确定性分别测量什么

### 语义不确定性（Semantic Uncertainty）

论文以语义熵作为代理量：对同一问题生成多个答案，把语义等价的答案归入同一簇，再计算簇分布的熵。它关注回答含义的分歧，而不是仅比较词面变化。其通用定义见[语义熵知识页](../uncertainty/sampling-based/semantic-entropy.md)。

低语义熵不必然意味着正确。模型可以稳定地回答同一个错误事实；所有样本都拒答时，拒答表达也可能集中为一个语义簇，因此低熵与高言语不确定性同时出现不一定是需要修正的问题。

### 言语不确定性（Verbal Uncertainty）

它衡量回答措辞向读者传达了多少怀疑，而不是模型自行输出的数值置信度。原文 §2.2 给出：

$$
v(U\mid C)=1-\Pr(P\mid U,C).
\tag{1}\label{eq:ji-verbal-uncertainty}
$$

$U$ 为回答话语，$C$ 为问题及语境，$P$ 为被表达的命题；右侧是读者依据话语和语境形成的主观概率。式 $\eqref{eq:ji-verbal-uncertainty}$ 是概念定义，不代表能直接读取 LLM 内部“命题为真”的概率。

实际测量使用模型裁判（LLM-as-a-Judge）：给每个采样答案评估措辞的不确定程度，再对同一问题的多个答案取平均。主要评分模型为 Llama-3.1-70B-Instruct；附录还用与典型不确定表达的 embedding 相似度、替代评分模型及人工评价检查稳健性。措辞评分与参考答案正确性评价是不同任务。

## 如何提取控制措辞的方向

### 言语不确定性特征（Verbal Uncertainty Feature，VUF）

先依据生成答案的言语不确定性，把问题分为确定组与不确定组。**分组标签来自答案措辞，但用于提取方向的 hidden state 来自生成答案前的最后一个输入 token。** 不能把这一步写成抽取答案最后一个 token 的状态。

对第 $l$ 层，令 $h^{(l)}(x)$ 为问题 $x$ 的该位置状态。使用两组均值之差并做单位化：

$$
\begin{aligned}
\widehat r^{(l)}
&=\mathbb{E}_{x\in D_{\mathrm{uncertain}}}[h^{(l)}(x)]
-\mathbb{E}_{x\in D_{\mathrm{certain}}}[h^{(l)}(x)],\\
r_{\mathrm{VU}}^{(l)}
&=\frac{\widehat r^{(l)}}{\lVert\widehat r^{(l)}\rVert_2}.
\end{aligned}
\tag{2}\label{eq:ji-vuf-direction}
$$

$D_{\mathrm{uncertain}}$、$D_{\mathrm{certain}}$ 是两组问题，期望在实验中用样本均值估计；单位化要求均值差非零。附录 G.1 的缓解设置使用评分不低于 0.9、不高于 0.05 的样本构造两组。

均值差方向（Difference-in-Means Direction）从确定组中心指向不确定组中心。平均可以减少个别问题差异的影响，但不能证明已经排除全部混杂因素，更不能仅凭组间可分就认定它是普适的“真实性方向”。

### 干预如何验证方向的作用

作者在生成过程的选定层，对 token 激活施加：

$$
h^{(l)}\leftarrow h^{(l)}+\alpha r_{\mathrm{VU}}^{(l)}.
\tag{3}\label{eq:ji-vuf-intervention}
$$

$\alpha$ 是干预强度。正向干预提高所测模型表达的不确定性，负向干预降低它。这个步骤改变 inference 中的激活，不更新基础模型权重；提取方向所需的数据收集则使用了引导不确定性表达的 prompt，不能把“干预时不改 prompt”扩写成整个流程从未使用特定 prompt。

论文结合组间可分的可视化、跨数据集方向相似性和正负干预实验提供证据。迁移实验是在**同一模型内**，将 TriviaQA 提取的方向用于 NQ-Open、PopQA；没有证明不同模型可以直接共用同一向量。提取位置是最后一个输入 token，论文描述的干预范围则是生成流程中的所有 token，两者也不同。

## 检测器、探针与方向有什么区别

| 部件 | 学习或构造依据 | 输出及用途 |
|---|---|---|
| VUF | 依据答案措辞分组，计算问题状态的均值差 | 提供激活干预方向 |
| 两个不确定性探针 | 用问题状态分别拟合测得的语义、言语不确定性 | 近似两个数值分数 |
| 幻觉检测器 | 以两种分数为特征，使用幻觉标签训练逻辑回归 | 给出风险判断，决定是否进入干预 |

原文 §4.1 比较两种输入路径：直接对多次生成进行测量，或者使用两个线性回归探针预测分数。后者可在生成答案前打分，但探针训练仍需离线构造测量目标。VUF 不等于这两个回归器，回归器也不等于最终风险分类器。

检测任务的标签依赖正确性和拒答判断，原文的主检测设置把拒答归为非幻觉。不能把这类标签无条件等同于其他论文的错误标签，也不能直接把预测的不确定性当作已校准的错误概率。

## 机制性不确定性校准（Mechanistic Uncertainty Calibration，MUC）

MUC 将方向与风险判断结合。设问题 $x$ 的语义熵为 $H_{\mathrm{sem}}(x)$，每题采样数为 $N>1$，平均言语不确定性为 $v(x)$。附录 G.1 以理论最大熵 $\ln N$ 归一化：

$$
s(x)=\frac{H_{\mathrm{sem}}(x)}{\ln N}.
\tag{4}\label{eq:ji-normalized-entropy}
$$

按正文式 7，先对两种分数之差截断，再在风险检测通过时应用。把检测门控明确写出，可表示为：

$$
\alpha(x)=
\begin{cases}
\operatorname{clip}\bigl(s(x)-v(x),0,\alpha_{\max}\bigr),&g(x)=1,\\
0,&g(x)=0.
\end{cases}
\tag{5}\label{eq:ji-muc-gate}
$$

$g(x)$ 是检测器的风险标记，$\alpha_{\max}$ 为强度上限；将式 $\eqref{eq:ji-muc-gate}$ 的强度代入式 $\eqref{eq:ji-vuf-intervention}$。这个分段表达将原文的检测条件与强度公式合并，便于说明流程，不是原文额外提出的新公式。没有风险标记，或差值不为正，都不进行正向干预。

例如，已标记风险、$s(x)=0.8$、$v(x)=0.2$、上限为 1，则强度为 0.6。若用二维状态 $(1,2)$ 和单位方向 $(1,0)$ 作示意，更新后为 $(1.6,2)$。这些数字用于解释计算，不是论文激活实测结果。

```text
离线：测量答案不确定性 → 提取 VUF、训练分数探针与风险检测器
在线：测量或预测两种分数 → 风险检测
→ 风险为高且语义不确定性高于言语不确定性：沿 VUF 干预生成
→ 否则：保持不干预的生成流程
```

这里的校准主要评价两类不确定性的对齐，不等同于证明最终回答概率满足经典的频率校准条件。提高措辞的不确定性也不能保证提高回答准确率。

## 实验设置

主要检测实验使用 Llama-3.1-8B-Instruct、Mistral-7B-Instruct-v0.3、Qwen2.5-7B-Instruct；缓解实验另含 Llama-3.1-70B-Instruct。任务为 TriviaQA、NQ-Open、PopQA 的闭卷短答案问答。

附录 B 报告每个数据集约 10,000 个训练问题；TriviaQA 与 PopQA 明确各取 1,000 个验证、1,000 个测试问题。NQ-Open 的文字将验证与测试合写，复现前应进一步核对划分文件与问题 ID。附录 C.1 的语义不确定性计算每题采样 10 次，温度为 1，top-p 为 0.9，top-k 为 50；另以温度 0.1 生成被评价答案。两类不确定性的 prompt 与评分流程应分别固定，不能默认测量时完全复用同一批生成。

附录 E 的干预层为 Llama-3.1-8B 与 Mistral 的 15–31 层、Qwen2.5-7B 的 16–27 层；这些是所测模型的设置，不能照搬到层数不同的模型。模型和评分调用、离线样本构造及 hidden state 访问都应计入成本。

## 检测结果如何解释

下表摘录 Tables 1–2 的 **Llama-3.1-8B-Instruct／TriviaQA**，AUROC 按百分数展示。

| 输入方式 | AUROC（%）↑ | 比较意义 |
|---|---:|---|
| 直接计算语义不确定性 | 79.21 | 单一语义分数基线 |
| 直接计算语义与言语不确定性 | 79.71 | 在相同测量路径中加入措辞信息 |
| SEP | 66.85 | 原文采用的二值语义熵探针基线 |
| 探针预测语义与言语不确定性 | 73.53 | 降低在线测量成本的组合方式 |

79.71 与 66.85 的差异同时包含测量方式与输入信息的变化，不能全部解释为“加入言语信息的贡献”。直接计算路径中，相对语义分数的变化为 0.50 个百分点；探针路径也应在相应基线条件下比较。

组合并非所有配置都最好。例如 Table 1 的 Qwen2.5／PopQA，言语分数单独使用时为 75.66，组合后为 75.43。AUROC 衡量排序，不能把它当作概率校准程度或某个固定阈值下的准确率。

## 缓解结果与代价

自信幻觉指答案不被参考答案支持且言语不确定性低于所定阈值的情况；整体幻觉率则统计不被支持且未拒答的回答。原文阈值由分组平方偏差准则确定，复现时必须保留具体定义、分母和阈值选择流程。

下表为 Table 3 中 **Llama-3.1-8B-Instruct、三个数据集平均、直接计算不确定性**的结果。变化按原表展示值计算，单位为百分点。

| 指标 | 干预前（%） | MUC 后（%） | 变化 |
|---|---:|---:|---:|
| 自信幻觉率 ↓ | 32.4 | 22.3 | −10.1 |
| 整体幻觉率 ↓ | 32.4 | 25.7 | −6.7 |
| 正确率 ↑ | 48.5 | 46.4 | −2.1 |
| 拒答率 | 19.1 | 27.9 | +8.8 |

论文报告四个模型的平均自信幻觉相对降幅为 29.6%，它不是下降 29.6 个百分点，也不是这一个模型的准确率提升。干预后，自信幻觉率低于整体幻觉率，表明部分错误回答变得更谨慎，但错误内容仍可能存在。

正确率下降不能忽略。附录 G.3 展示：回答中的额外细节可能使本来正确的多个答案被判断为不同含义，语义熵偏高，进而引发不必要的拒答。拒答率没有统一的“越高越好”方向，应结合任务代价解释。

## 消融实验支持什么

Table 5 的 Llama-3.1-8B 结果比较同样干预设置下的随机方向与 VUF。NQ-Open 的自信幻觉率从 40.2% 降到随机方向的 35.2%，而 VUF 为 26.2%；PopQA 对应为 33.7%、28.8%、21.6%。方向选择有作用，不能把全部变化归因于随便扰动激活。

同一模型使用 TriviaQA 的方向后，NQ-Open 为 26.2%，PopQA 为 22.4%，支持所测任务间的方向迁移。它仍不证明可跨架构迁移。附录 E 的替代评分检查增加了稳健性证据，但没有消除所有评分模型偏差。

## 复现时必须厘清的强度约定

正文式 7 与附录 G.1 规定归一化分数在 $[0,1]$，因此正差值至多为 1。如果严格使用式 $\eqref{eq:ji-muc-gate}$，把上限从 1 增加到 3 或 5 不会进一步增加强度；但附录 G.4 的强度消融报告了这些设置下不同的结果。

因此，复现前需要核对实际实现是否另含缩放、采用哪些分数，以及强度上限如何参与计算，不能仅凭公布公式就声称完整复现了该消融。本笔记保留论文公式与结果，不猜测或代替作者修正实现。本轮未运行作者代码，也未把现有分享中的示意计算当作实验。

## 与研究主线的关系及局限

论文连接表征 UQ、语言自报置信度与生成干预。它提供了一个可检验的问题：模型是否有可用的不确定性信号，却没有在措辞中表达出来？这与增加外部事实证据、改善模型知识或检测所有幻觉是不同目标。

证据主要来自短答案、闭卷问答和特定模型。长文、医学、LVLM 及新领域都需要另行测试。语义聚类、措辞评分、拒答判断和探针近似均可能引入误差；平均方向与线性探针也不保证没有任务捷径。评价时应同时保留错误、谨慎措辞、正确答案和拒答四方面信息。

## 相关笔记

- [语言自报置信度](../uncertainty/verbal-confidence/index.md)
- [语义熵](../uncertainty/sampling-based/semantic-entropy.md)
- [内部表征](../foundations/language-models/hidden-representations.md)
- [Kossen 2024：语义熵探针](2024-kossen-semantic-entropy-probes.md)
- [Lamb 2026：温度缩放与语义校准](2026-lamb-semantic-calibration.md)

## 参考文献

- Ji, Z., Yu, L., Koishekenov, Y., Bang, Y., Hartshorn, A., Schelten, A., Zhang, C., Fung, P., Cancedda, N. (2025). *Calibrating Verbal Uncertainty as a Linear Feature to Reduce Hallucinations*. Proceedings of EMNLP 2025, 3769–3793. [正式论文](https://aclanthology.org/2025.emnlp-main.187/)
