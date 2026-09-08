---
tags:
  - evaluation
  - correctness
  - ground-truth
---

# 正确性判定（Correctness Definition）

本页将[正确性与可靠性](../../hallucination/reliability-correctness.md)中的任务依赖原则落实为评价协议，不再建立第二套概念定义。实验中的正确标签应来自事先约定的规则，而不是检测分数或模型的自评。

## 把要求转成判定规则

对于短答案，先规定精确匹配（Exact Match，EM）前允许哪些归一化。大小写、标点、别名和单位是否能改写，都取决于任务。不能一律删除否定词、数量词或单位。Rajpurkar et al. (2016) 的 SQuAD 使用特定文本归一化与多参考答案规则；将其评估脚本移植到另一任务时，需要重新确认这些规则仍成立。

开放回答可能有多个有效答案。可以维护允许的答案集合，或采用人工确认的语义等价（Semantic Equivalence）关系。若存在部分正确，先保留分级标签，再说明为何以及怎样二值化。例如“核心答案正确，但附加事实错误”应由任务决定是否合格，不能在看到方法排名后修改标准。

## 证据支持与任务完成分开记录

Min et al. (2023) 的 FActScore 将长文本拆成原子事实并检查指定来源支持。它提供事实精确程度，不能说明回答是否完整、是否遗漏问题关键要求，也不自动评价推导过程。

一个简单的协议可以分别记录：核心答案正确、附加主张被支持、格式合规、关键内容完整。是否聚合为“全对”由实验问题决定。这里的字段是研究设计建议，不是通用标签标准。

## 防止评价对象变化

固定待评回答及其版本。若模型重新生成答案，原来的正确性标签通常不能直接复用。对时效事实保存证据日期；对领域问题说明专家依据。无法判断、空答案和显式拒答应分别记录，避免将拒答同时算作普通错误，又在选择性评价中当作已接受回答。

用于校准的事件也须一致：“整段完全正确”的概率不能用“包含至少一个正确事实”的标签验证。

## 相关笔记（Related Notes）

- [语义等价](semantic-equivalence.md)
- [选择性预测](../selective-prediction/index.md)

## 参考文献（References）

- Rajpurkar, P., Zhang, J., Lopyrev, K., Liang, P. (2016). *SQuAD: 100,000+ Questions for Machine Comprehension of Text*. EMNLP, 2383–2392. [Paper](https://aclanthology.org/D16-1264/)
- Min, S., Krishna, K., Lyu, X., Lewis, M., Yih, W., Koh, P. W., Iyyer, M., Zettlemoyer, L., Hajishirzi, H. (2023). *FActScore: Fine-grained Atomic Evaluation of Factual Precision in Long Form Text Generation*. EMNLP, 12076–12100. [Paper](https://aclanthology.org/2023.emnlp-main.741/)
