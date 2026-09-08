---
tags:
  - uq
  - efficiency
---

# 不确定性量化（Uncertainty Quantification，UQ）：高效方法

高效 UQ 研究在给定计算、延迟和存储预算下，怎样保留有用的不确定性信息。对于大语言模型（Large Language Model，LLM），主要代价往往来自生成与辅助处理，而不只是最后计算一个分数。当前 PhD 将效率作为重要技术兴趣，比较对象是完整流程的效果与代价。

## 先统一成本口径

生成次数、调用次数、token 数和实际等待时间不能互相替代。一次接口调用可能批量返回多条序列，一条长推理序列也可能比数条短答案更贵。并行处理可以减少墙钟时间（Wall-clock Time），却不一定减少总计算或计费量。

| 成本环节 | 至少记录什么 | 容易遗漏的内容 |
|---|---|---|
| 目标模型生成 | 序列数、调用数、输入与输出 token | 重试、最长回答、批处理和缓存条件 |
| 验证器（Verifier） | 模型、调用数、读入文本与生成长度 | 多个主张分别核查的成本 |
| 语义分组 | 比较次数及判定器 | 自然语言推断（Natural Language Inference，NLI）两个方向的判断 |
| 内部特征 | 提取层、token 位置、保存精度 | 额外前向计算、特征搬运及显存峰值 |
| 证据处理 | 检索、重排、文档长度 | 索引构建、读取与过期证据更新 |
| 学习与维护 | 标签或代理目标生成、拟合、更新 | 校准集、probe 训练与模型版本迁移 |

Fadeeva et al. (2023) 在 LM-Polygraph 中区分不同方法的计算、内存和训练数据要求。表格进一步细化本知识库比较时的成本口径，不是对某个系统实测速度的报告。

## 多样本方法的主要代价

当目标模型额外生成 $K$ 条长度相近的回答时，生成计算通常随总 token 增长；但最终延迟还受批量大小、并行和缓存影响。不能笼统写成“恰好慢 $K$ 倍”。原始回答是否算入 $K$ 也应明示。

[语义熵（Semantic Entropy）](../uncertainty/sampling-based/semantic-entropy.md)还需要含义分组。若逐对检查所有回答，比较数量按 $K(K-1)/2$ 增长；严格双向蕴含又涉及两个方向的判断。使用类别代表可减少实际比较，但最坏情况仍可能接近平方增长。Kuhn et al. (2023) 给出了这种聚类思路；判定器是否足够便宜需要在实际环境中测量。

长篇回答的主张级方法还会拆分内容、生成核查问题并重新作答。Farquhar et al. (2024) 的传记设置包含这样的额外阶段。因此，短问答中“若干次 sampling”的成本不能直接套到长回答。

## 单序列路线

单序列 UQ（Single-sequence UQ）可以利用一次生成已有的 log-probability、局部熵或 hidden state。如果运行接口已经返回这些信息，附加计算可能较低；若需要重新评分、存储全词表 logits 或保留所有层的特征，成本就不同。

Kossen et al. (2024) 的 Semantic Entropy Probes 是一个具体例子：训练时先通过多次生成取得语义熵代理标签，再让线性探针预测高或低语义熵，部署时使用单次生成的 hidden state。这种方式将部分成本移到训练阶段，而不是把成本彻底消除。

该论文的模型和任务结果支持“可以学习便宜的代理信号”，不能据此声称所有模型都能零成本恢复精确语义熵。新 checkpoint、领域或生成格式可能要求重新生成监督数据和拟合 probe。

## 减少 Sampling 的几种设计

固定减少样本数最容易实现，但会增加估计波动并漏掉少见模式。重复利用已有回答可以节约生成，前提是它们来自可比较的输入和设置，不能把不同上下文的回答混成同一分布。

自适应 sampling（Adaptive Sampling）按当前观察决定是否追加样本；提前停止（Early Stopping）则在预设准则满足后结束。例如，先做少量生成，对分歧明显的输入继续检查。这是一般流程设计，不是本页宣称复现了某一篇特定加速方法。

这样的设计需要保留少量样本下的错误一致风险。若只因为最初两条相同就停止，可能漏掉之后才会出现的少数答案。停止规则、最大预算、未决状态和最终评分方式都要固定；随机停止得到的频率或校准表现，不能直接继承固定样本数估计器的结论。

## 效率取舍怎样解释

可以让便宜信号先筛选，再对疑难输入使用较贵的 sampling 或检索。这可能改善平均成本，但也可能把便宜信号漏掉的稳定错误永久留在快速路径。应检查平均代价、困难输入的尾部代价，以及最终错误类型，而不只报告节约多少调用。

当前研究的有效比较应固定任务与最终决策目标，分别报告离线准备和在线使用成本，再比较检测、校准与可用覆盖范围。额外信息带来的收益需要足以解释新增代价；“单次生成”“无标签”或“小型分类器”本身都不是效率结论。

## 相关笔记（Related Notes）

- [基于概率的 UQ](../uncertainty/probability-based/index.md)
- [hidden state 与不确定性](../representation-uq/hidden-state-uncertainty.md)

## 参考文献（References）

- Fadeeva, E., Vashurin, R., Tsvigun, A., et al. (2023). *LM-Polygraph: Uncertainty Estimation for Language Models*. EMNLP: System Demonstrations, 446–461. [Paper](https://aclanthology.org/2023.emnlp-demo.41/)
- Kuhn, L., Gal, Y., Farquhar, S. (2023). *Semantic Uncertainty: Linguistic Invariances for Uncertainty Estimation in Natural Language Generation*. ICLR. [作者会议版本](https://arxiv.org/abs/2302.09664v3)
- Farquhar, S., Kossen, J., Kuhn, L., Gal, Y. (2024). *Detecting hallucinations in large language models using semantic entropy*. Nature, 630, 625–630. [Paper](https://www.nature.com/articles/s41586-024-07421-0)
- Kossen, J., Han, J., Razzak, M., Schut, L., Malik, S., Gal, Y. (2024). *Semantic Entropy Probes: Robust and Cheap Hallucination Detection in LLMs*. ICML Workshop on Foundation Models in the Wild；本页使用 arXiv v1. [作者版本](https://arxiv.org/abs/2406.15927v1)
