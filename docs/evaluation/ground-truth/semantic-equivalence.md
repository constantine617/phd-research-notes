---
tags:
  - evaluation
  - semantics
  - ground-truth
---

# 语义等价（Semantic Equivalence）

回答措辞不同，不代表含义不同。“三千米”和“三千公尺”可能表达相同距离；“大约三千米”则增加了近似限定。等价判断应保留问题语境、实体、否定、数量、单位与时间范围。

## 从匹配工具到判定标准

精确匹配（Exact Match，EM）可复现且成本低，却会漏掉别名与合理改写。自然语言推断（Natural Language Inference，NLI）可以检查一个回答是否蕴含另一个回答；双向蕴含比单向蕴含更接近相同含义，但仍受推断模型与上下文构造影响。

Kuhn et al. (2023) 在语义不确定性研究中，结合问题语境与双向蕴含建立答案语义分组。这是特定方法的判定过程，不意味着任意 NLI 输出都构成严格的数学等价关系。实际预测可能不满足传递性，因此分组顺序和冲突处理也会影响结果。

embedding 相似度适合检索候选匹配，却可能把主题接近但结论相反的句子判得很近。大语言模型（Large Language Model，LLM）裁判可以处理更复杂的表达，但需要固定 prompt、参考与模型版本，并用人工样例检验。

## 分开两个问题

“两个答案意思相同”与“它们都正确”是不同问题。两个回答可以表达完全相同的错误事实。反过来，对一个允许列举多个实例的问题，两个不同的正确实例可能都合格，却不表达相同命题。

因此，建立正确标签时可以使用“属于允许答案集合”的关系；计算[语义熵](../../uncertainty/sampling-based/semantic-entropy.md)时则需要说明怎样划分输出含义。不要把这两种关系无条件交换。

## 建议的核验样例

在正式评价前，覆盖别名、单位转换、否定、部分包含、数字近似、多个正确答案和错误但同义的答案对。人工裁决应能说明为什么接受或拒绝，而不只给一个相似度阈值。阈值须在验证数据确定，并保留未能判断的样例。

## 相关笔记（Related Notes）

- [模型裁判](llm-as-a-judge.md)

## 参考文献（References）

- Kuhn, L., Gal, Y., Farquhar, S. (2023). *Semantic Uncertainty: Linguistic Invariances for Uncertainty Estimation in Natural Language Generation*. ICLR. [作者会议版本](https://arxiv.org/abs/2302.09664v3)
