---
tags:
  - foundations
  - vlm
  - representation
---

# 多模态表征（Multimodal Representation）

多模态模型处理的不是直接可读的“图像含义”，而是由网络计算出的表示。本页关注视觉与文本分别如何表示、怎样交互，以及任务所需信息能否通过这些表示保留下来。表征是计算载体，是否有用需要结合任务判断。

## 不同模态的表示

图像和文本具有不同的原始形式。图像可以由视觉编码器转换为 patch 或区域特征；文本先转换为 token embedding，再在上下文计算中形成 hidden state。为方便讨论，把某一处理阶段的视觉表示记为：

$$
\mathbf{v}_i\in\mathbb{R}^{d_v}
\tag{1}
\label{eq:visual-representation-vector}
$$

$i$ 是视觉 token、patch 或区域的索引，$d_v$ 是该阶段的特征维度，$\mathbf{v}_i$ 是一个实向量。相应的文本表示记为：

$$
\mathbf{t}_j\in\mathbb{R}^{d_t}
\tag{2}
\label{eq:text-representation-vector}
$$

$j$ 是文本 token 位置，$d_t$ 是文本表示维度，$\mathbf{t}_j$ 可以表示指定阶段的 embedding 或 hidden state，但使用时必须说明是哪一种。本页省略层索引，是为了讨论模态关系，不表示各层向量相同。

式 $\eqref{eq:visual-representation-vector}$ 与式 $\eqref{eq:text-representation-vector}$ 只是抽象记号，不规定模型架构。视觉 patch 与文本 token 也没有天然的一一对应关系：“一只狗”可以覆盖多个视觉位置和多个文本 token。

## 共享空间与连接空间

一种方式是把两种表示映射到可直接比较的共同空间。Radford et al. (2021) 的对比语言图像预训练（Contrastive Language–Image Pre-training，CLIP）分别编码图像和文本，再将整体表示投影到共同空间进行匹配。

另一种方式是让视觉信息成为大语言模型（Large Language Model，LLM）的条件。Liu et al. (2023) 使用视觉特征投影构造语言侧可接收的输入。这里的目标是支持后续计算和生成，不要求把所有中间表示都转换为同一个可直接比较的语义向量。

因此，视觉语言模型（Vision-Language Model，VLM）需要建立可比较或可交互的接口，但不必让所有层、所有模态都共享完全相同的空间。即使两个向量维度相同，未经适当训练，也不能仅凭余弦相似度认定它们语义相近。

## 融合意味着信息参与共同计算

多模态融合（Multimodal Fusion）是让不同模态的信息共同影响表示、分数或输出。它可以发生在输入组织、中间特征交互或结果组合等位置；这些描述只表示融合位置，不构成所有模型唯一的分类法。

例如，把视觉向量和文本 embedding 放入共同序列，后续层可以处理两者的联系；也可以让一组查询通过跨注意力（Cross-Attention）读取视觉特征。Li et al. (2023) 的 BLIP-2 提供了后一种交互的实例。

拼接只是提供交互机会，并不保证每个位置都得到有效利用。比较两个已独立计算的整体向量，也与在中间层让细粒度特征相互作用不同。阅读论文时，应说明交互发生在哪里、参与交互的单位是什么，而不是只写“进行了融合”。

## 信息保留要相对于任务判断

一个表示可以足以识别场景，却不足以区分局部细节。对“图里有没有杯子”，对象类别可能足够；对“杯柄朝哪边”，则还需要形状和空间信息。图中文字、细粒度颜色、对象数量及相互关系，也分别需要相应信息可被后续计算访问。

可以用一个条件推理理解信息丢失：假设两幅图在任务答案上不同，却经过编码和连接后得到完全相同的视觉表示；若其他输入也相同，后续模型就不能仅凭这份表示区分原图。它仍可能依赖先验猜中某个答案，但这不等于恢复了被丢弃的视觉证据。

这个推理不声称某种维度、分辨率或压缩方式必然失败。实际表示是否保留了信息、保留的信息是否可被当前语言模型利用，是需要区分的两个问题。某个属性能被额外探测器读出，也不自动证明原生成过程使用了该属性。

## 与可信性研究的关系

表征为后续 [跨模态对齐](cross-modal-alignment.md) 提供计算对象，也为 [视觉定位](visual-grounding.md) 提供可能的区域和特征基础。但有表示、可匹配和有证据支持，是逐步增加要求的不同判断。

对不确定性量化（Uncertainty Quantification，UQ），内部表示可以作为研究输入，帮助检验模型是否保留并利用了相关视觉信息。当前不预设哪一层、哪一种表示最好，也不将表示维度或融合复杂度直接当作可靠性指标。语言侧 hidden state 的基础见 [内部表征](../language-models/hidden-representations.md)。

## 参考文献（References）

- Radford, A., Kim, J. W., Hallacy, C., et al. (2021). *Learning Transferable Visual Models From Natural Language Supervision*. Proceedings of the 38th International Conference on Machine Learning, PMLR 139, 8748–8763. [Paper](https://proceedings.mlr.press/v139/radford21a.html)
- Liu, H., Li, C., Wu, Q., Lee, Y. J. (2023). *Visual Instruction Tuning*. Advances in Neural Information Processing Systems, 36, 34892–34916. [Paper](https://proceedings.neurips.cc/paper_files/paper/2023/hash/6dcf277ea32ce3288914faf369fe6de0-Abstract-Conference.html)
- Li, J., Li, D., Savarese, S., Hoi, S. (2023). *BLIP-2: Bootstrapping Language-Image Pre-training with Frozen Image Encoders and Large Language Models*. Proceedings of the 40th International Conference on Machine Learning, PMLR 202, 19730–19742. [Paper](https://proceedings.mlr.press/v202/li23q.html)
