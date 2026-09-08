---
tags:
  - evaluation
  - benchmark
  - llm
---

# LLM 的文本 benchmark

LLM 的文本任务可以检验知识、回答检索与推理，但它们的正确标准不同。选择 UQ 实验数据时，应先说明预测的是哪个任务事件。

## 代表任务与标签边界

| Benchmark | 输入与任务 | 参照与主要限制 |
|---|---|---|
| SQuAD（[Rajpurkar et al., 2016](https://aclanthology.org/D16-1264/ "文献引用")） | 给定文章的片段问答 | 答案文本与多参考匹配；原始论文主要实验基于 v1.0，不自动代表带无答案问题的后续版本 |
| Natural Questions（[Kwiatkowski et al., 2019](https://aclanthology.org/Q19-1026/ "文献引用")） | 真实搜索问题与 Wikipedia 页面 | 长答案、短答案或无答案标注；开放域改写若移除给定页面，任务条件已改变 |
| MMLU（[Hendrycks et al., 2021](https://arxiv.org/abs/2009.03300 "文献引用")） | 多学科多项选择 | 正确选项与按学科准确率；选择题得分不等于开放生成事实性（Factuality） |
| GSM8K（[Cobbe et al., 2021](https://arxiv.org/abs/2110.14168 "文献引用")） | 多步小学数学应用题 | 人工编写解答与最终答案；最终数字正确不自动证明每步推理正确 |
| TruthfulQA（[Lin et al., 2022](https://aclanthology.org/2022.acl-long.229/ "文献引用")） | 易诱发常见误解的问题 | 真实与有信息量分别评价；带有特定构造目的，不代表普通问题的自然分布 |

这些来源分别支持表中的任务描述。它们不共享统一的正确标签，也不共享同一种基础错误率。

## 长回答需要不同评价

长篇人物介绍或事实解释可能同时有正确与错误的主张。可用 [Min et al. (2023)](https://aclanthology.org/2023.emnlp-main.741/ "文献引用") 的 FActScore 路线拆解原子事实并检查来源支持，但还要单独评价信息覆盖与问题完成度。

若把原始短答案任务改为“先解释再回答”，应分别保存解释与最终答案，并规定附加错误是否影响整体标签。不同 prompt 会改变回答长度、错误机会和不确定性分布。

## 使用建议

先选择一个标签清楚的任务开展方法比较，再加入不同知识或推理需求的任务检验迁移。固定 few-shot 示例、答案提取规则、数据划分和评分脚本。公开任务可能存在训练污染；缺少训练数据可见性时应记录限制。

避免将知识、推理和长文本主张级分数简单平均后称为“总体幻觉检测能力”。逐任务结果更能解释方法在哪些条件下有效。

## 参考文献

- Rajpurkar, P., Zhang, J., Lopyrev, K., Liang, P. (2016). *SQuAD: 100,000+ Questions for Machine Comprehension of Text*. EMNLP, 2383–2392. [Paper](https://aclanthology.org/D16-1264/)
- Kwiatkowski, T., Palomaki, J., Redfield, O., et al. (2019). *Natural Questions: A Benchmark for Question Answering Research*. Transactions of the Association for Computational Linguistics, 7, 453–466. [Paper](https://aclanthology.org/Q19-1026/)
- Hendrycks, D., Burns, C., Basart, S., et al. (2021). *Measuring Massive Multitask Language Understanding*. ICLR. [Paper](https://arxiv.org/abs/2009.03300)
- Cobbe, K., Kosaraju, V., Bavarian, M., et al. (2021). *Training Verifiers to Solve Math Word Problems*. arXiv:2110.14168. [Paper](https://arxiv.org/abs/2110.14168)
- Lin, S., Hilton, J., Evans, O. (2022). *TruthfulQA: Measuring How Models Mimic Human Falsehoods*. ACL, 3214–3252. [Paper](https://aclanthology.org/2022.acl-long.229/)
- Min, S., Krishna, K., Lyu, X., Lewis, M., Yih, W., Koh, P. W., Iyyer, M., Zettlemoyer, L., Hajishirzi, H. (2023). *FActScore: Fine-grained Atomic Evaluation of Factual Precision in Long Form Text Generation*. EMNLP, 12076–12100. [Paper](https://aclanthology.org/2023.emnlp-main.741/)
