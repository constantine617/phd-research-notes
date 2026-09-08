---
tags:
  - reliability
  - correctness
  - trustworthiness
---

# 正确性与可靠性（Correctness and Reliability）

判断输出是否正确，与判断我们能否稳定依赖模型，是两个层次的问题。前者需要任务标准；后者还要考虑使用条件、失败识别和处置方式。本页为后续不确定性量化（Uncertainty Quantification，UQ）与评估研究提供共同的判定对象。

## 正确性依赖任务要求

正确性不是脱离任务的通用二元属性。实验可以为了分析采用二元标签，但这个标签必须来自已经说明的规则。开放生成还可能需要分级判断，或分别评价若干要求。

| 判定依据 | 适用时需要明确的内容 |
|---|---|
| 精确匹配（Exact Match） | 是否先归一化空格、大小写、标点与数字表示 |
| 语义等价（Semantic Equivalence） | 同义表达、别名、单位转换与省略何时可接受 |
| 参考答案（Reference Answer） | 是否允许多个正确答案，参考是否完整 |
| 人工判断（Human Judgment） | 评分指南、专业要求和分歧裁决 |
| 领域规则与证据支持 | 限定条件、证据来源、时间版本及支持要求 |

表格是本知识库的评估准备框架，不是一篇论文提出的固定分类。Min et al. (2023) 的 FActScore 提供了一个具体例子：长回答可以拆成原子事实（Atomic Fact），分别判定是否被指定知识来源支持，再聚合为比例。这个分数回答的是事实精确程度，不能代替整项任务是否完成。

参考真值（Ground Truth）也有建立过程。数据集标签、专家结论或指定知识库都可能承担参照角色；来源不完整或有歧义时，应保留这类状态，不能因为程序需要二元数组，就假装所有样本已有无争议答案。

## 与事实性（Factuality）的交集

[事实性](factuality.md)关注事实主张是否符合可核查事实。在事实问答中，它常是正确性的核心；但在摘要、推理或格式敏感任务中，还要考虑信息选择、推导要求、指令和结构。

例如，要求只输出一个指定格式的日期，模型却写出一整段真实历史，事实可能没错，任务仍未完成。摘要逐句都真实，却遗漏全部核心信息，也不能仅凭事实性合格就判定正确。相反，完整照抄错误来源可能满足一种来源一致性评分，却不符合世界事实。

因此，还应把[忠实性（Faithfulness）](faithfulness.md)与[幻觉（Hallucination）](hallucination-definition.md)标签分别说明。“错误检测”覆盖哪些失败类型，不能仅从其方法名称推断。

## 可靠性的实用工作定义

本知识库采用以下工作定义：**可靠性关注模型在具体任务和条件下，是否能够稳定地产生满足要求的输出，以及我们是否能够识别可能失败的情况。** 这是面向生成与 UQ 研究的实用范围，不是通用形式化定义。

美国国家标准与技术研究院（National Institute of Standards and Technology，NIST） 人工智能风险管理框架（Artificial Intelligence Risk Management Framework，AI RMF）1.0 将可靠性与给定条件和时间范围内按要求运作联系起来（Tabassi, 2023）。本页在此基础上突出失败识别，以服务输出评估研究；这部分扩展是知识库约定，并不是对 NIST 原句的直接翻译。

“稳定”要结合合格率、错误类型和使用条件理解。反复输出相同错误答案不构成可靠；在证据不足时识别限制并采取适当处置，也可能比坚持回答更符合系统要求。医疗等长期应用还需要额外的领域验证，不能由一般任务表现推断适用性。

## 置信度（Confidence）与不确定性（Uncertainty）

置信度是对某个预测或主张的把握程度的表达或估计。它可能来自模型概率、语言中的自报把握程度、经过校准的分数，或独立训练的外部估计器。比较之前必须说明它针对哪一个对象。

token 概率针对特定上下文中的符号选择，并不天然等于整段回答正确的概率。模型说“我有九成把握”，也只是一个待检验的输出。Kadavath et al. (2022) 分别研究了模型对具体回答的自评，以及模型能否答对某个问题的估计；两者相关，却不能交换使用。

不确定性描述预测中的未确定程度，但不同方法测到的对象并不相同。分数可能反映词语选择分散、生成含义变化，或专门训练的错误风险估计。没有明确映射时，不能自动把置信度取补数当作任意不确定性，也不能把不确定性直接读成错误概率。

Farquhar et al. (2024) 的研究聚焦生成含义变化与一类随机性错误的关系，并明确不覆盖持续犯同一种错误等情况。这个限制解释了为什么低不确定性仍可能对应错误回答：方法测量的变化小，不代表所有相关事实都经过核查。

## 为什么还要检查置信度校准（Confidence Calibration）

置信度校准检查分数所表达的概率与相应条件下观察到的正确频率是否相符。Guo et al. (2017) 在分类模型中给出了经典表述，并显示准确率较高的模型仍可能校准不好。该结论的实验对象是分类模型，不能直接当作所有生成式模型的实测结论。

假设一个分数明确表示“回答完全正确的概率”，在足够多、可比较且分数接近九成的预测中，应检查正确比例是否也接近九成。即使这种群体关系成立，也不保证眼前这一条一定正确；有限样本估计还存在误差。

校准也不同于风险排序。一个信号可能善于把较容易失败的回答排到前面，却不能将具体数值解释为概率。Kadavath et al. (2022) 在跨任务估计中观察到区分能力与校准不能简单同步迁移，因此新条件下仍须验证两者。

## 从信号到可靠使用

选择性预测（Selective Prediction）允许系统只对部分输入作答，其余拒答或转交其他流程。Geifman and El-Yaniv (2017) 在深度分类模型中研究了错误风险与覆盖率（Coverage）的权衡。高覆盖率说明回答得多，不直接说明回答可靠；较低错误率若靠大量拒答获得，也必须连同覆盖率报告。

将这一思路用于生成任务时，先要定义什么算拒答、部分回答和合格回答，再检查实际交付部分的错误。分类论文在指定假设下的保证，不能直接被当作开放生成或分布变化后的保证。

## UQ 实验前的标签约定

开始比较信号之前，应记录：

1. 标签由谁或什么来源建立，是否与待评价检测器独立。
2. 采用二元、分级还是多维标签，局部错误怎样聚合。
3. 使用精确匹配还是语义匹配，以及允许的等价变换。
4. 多个有效答案、歧义、证据冲突和无法判断如何处理。
5. 回答是否必须提供证据，以及证据支持如何单独核查。
6. 信号预测的是具体回答正确、来源支持，还是模型在某种 sampling 设置下的可回答性。

当前研究的重要目标之一，是寻找能够区分较可靠与可能失败输出的信号。先固定这些判定对象，再比较信号的排序、校准和计算成本，结果才有可解释的意义。具体方法和指标留给后续模块；本页不把任何一种分数预先指定为可靠性的充分证明。

## 参考文献（References）

- Min, S., Krishna, K., Lyu, X., Lewis, M., Yih, W., Koh, P. W., Iyyer, M., Zettlemoyer, L., Hajishirzi, H. (2023). *FActScore: Fine-grained Atomic Evaluation of Factual Precision in Long Form Text Generation*. EMNLP, 12076–12100. [Paper](https://aclanthology.org/2023.emnlp-main.741/)
- Tabassi, E. (2023). *Artificial Intelligence Risk Management Framework (AI RMF 1.0)*. NIST AI 100-1. [DOI](https://doi.org/10.6028/NIST.AI.100-1)
- Kadavath, S., Conerly, T., Askell, A., et al. (2022). *Language Models (Mostly) Know What They Know*. arXiv:2207.05221. [Paper](https://arxiv.org/abs/2207.05221)
- Farquhar, S., Kossen, J., Kuhn, L., Gal, Y. (2024). *Detecting hallucinations in large language models using semantic entropy*. Nature, 630, 625–630. [Paper](https://www.nature.com/articles/s41586-024-07421-0)
- Guo, C., Pleiss, G., Sun, Y., Weinberger, K. Q. (2017). *On Calibration of Modern Neural Networks*. ICML, Proceedings of Machine Learning Research, 70, 1321–1330. [Paper](https://proceedings.mlr.press/v70/guo17a.html)
- Geifman, Y., El-Yaniv, R. (2017). *Selective Classification for Deep Neural Networks*. Advances in Neural Information Processing Systems, 30. [Paper](https://papers.nips.cc/paper_files/paper/2017/hash/4a8423d5e91fda00bb7e46540e2b0cf1-Abstract.html)
