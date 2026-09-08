---
tags:
  - evaluation
  - sampling
  - reproducibility
---

# Sampling 设置

UQ 中，多回答的差异取决于生成分布和生成预算。相同模型在不同 temperature 或截断规则下的分数，未必具有相同解释。基础原理见[解码与 Sampling](../../foundations/language-models/decoding-sampling.md)。

## 必须冻结的设置

记录 temperature、top-p、top-k、样本数、最大输出 token 数、随机种子、停止字符串或终止 token、重复惩罚和特殊 token 处理。若接口不支持其中某项，应写不可控制，而不是套用另一实现的默认值。

还要记录完整 prompt、聊天模板、示例顺序和 system 指令。对视觉输入，图像分辨率、裁剪、压缩与多图排列也属于生成条件。

[Wang et al. (2023)](https://arxiv.org/abs/2203.11171v4 "文献引用") 的自一致性（Self-Consistency）通过多条推理路径及答案聚合改进推理；[Kuhn et al. (2023)](https://arxiv.org/abs/2302.09664v3 "文献引用") 的语义不确定性研究则对生成含义进行分组。两者都依赖生成样本，但回答选优与分布估计的目标不同，不能把同一套 sampling 参数视为所有方法的默认最优设置。

## 生成分布与评分分布

temperature 或 top-p 修改后的 sampling 分布可能不同于模型原始概率分布。若从修改后分布生成回答，再使用原始 log probability 评分，必须明确这一组合。不要将多个返回分数统一称为“模型概率”而省略接口含义。

截断输出应标记。达到最大 token 数的回答，未必是自然完成；把其作为完整语义回答聚类可能改变不确定性估计。

## 怎样做敏感性分析

先在验证数据选定主设置，再对样本数和少数关键参数做预设范围的分析。不同方法尽量共享固定候选回答池，以减少额外的生成差异；但若方法本身改变 sampling，就另作端到端比较。

相同种子不保证跨硬件、软件版本或外部服务逐字复现。保存实际输出与设置，才能区分方法差异和生成随机性。

## 参考文献

- Wang, X., Wei, J., Schuurmans, D., Le, Q., Chi, E. H., Narang, S., Chowdhery, A., Zhou, D. (2023). *Self-Consistency Improves Chain of Thought Reasoning in Language Models*. ICLR. [作者会议版本](https://arxiv.org/abs/2203.11171v4)
- Kuhn, L., Gal, Y., Farquhar, S. (2023). *Semantic Uncertainty: Linguistic Invariances for Uncertainty Estimation in Natural Language Generation*. ICLR. [作者会议版本](https://arxiv.org/abs/2302.09664v3)
