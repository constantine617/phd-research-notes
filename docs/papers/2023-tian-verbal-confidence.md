---
tags:
  - paper-note
  - llm
  - calibration
  - verbal-confidence
---

# Just Ask for Calibration: Strategies for Eliciting Calibrated Confidence Scores from Language Models Fine-Tuned with Human Feedback

## 文献信息

- 作者：Katherine Tian, Eric Mitchell, Allan Zhou, Archit Sharma, Rafael Rafailov, Huaxiu Yao, Chelsea Finn, Christopher D. Manning。
- 年份：2023。
- 发表：EMNLP，5433–5442。
- 原始来源：[官方页面](https://aclanthology.org/2023.emnlp-main.330/)。
- 预印本：[arXiv:2305.14975v2](https://arxiv.org/abs/2305.14975v2)；方法与实验叙述仍按本页注明的正式来源。


## 研究问题与方法

[Tian et al. (2023)](https://aclanthology.org/2023.emnlp-main.330/ "文献引用")研究经过人类反馈训练的 LLM：能否通过 prompt 直接获得较好校准的语言自报置信度，而不依赖其原始输出概率。

作者比较一次输出答案及置信度、先回答再报告置信度，以及先提出多个候选答案再赋予置信度等策略。置信度可以是数值，也可以是映射到数值的语言表达。论文另行研究后处理温度缩放，并未为这些置信度策略执行专门的模型 fine-tuning。

需要区分一个关键基线：封闭模型上的 label probability 由多次答案 sampling 与语义等价判断估计，选取众数答案。它不是直接读取所有模型的 token logits。比较时必须同时考虑被评价的答案是否改变，以及额外调用预算。

## 实验与主要发现

论文使用 TriviaQA、SciQ 和 TruthfulQA，以及当时版本的 GPT-3.5、GPT-4、Claude 1/2 和 Llama 2-70B-Chat。正确性依赖参考答案及模型语义判断，结论对应这些版本和标注流程。

作者报告，合适的 verbal prompt 在若干模型与任务上改善校准，但效果并不对所有模型一致；Claude 1 和 Llama 2 的部分结果较为混合。加入思维链（Chain-of-Thought，CoT）也不稳定地产生收益。一个方法校准更好，并不意味着其正确与错误答案排序也更好。

## 指标阅读注意

原文 §2 的 Metrics 段（第 5435 页）将曲线下面积（Area Under the Curve，AUC）用于选择准确率–覆盖率曲线，不能标成受试者工作特征曲线下面积（Area Under the Receiver Operating Characteristic Curve，AUROC）。

原文 §2 的 Metrics 段（第 5434 页）对 ECE 的描述使用分箱差的平方，与常见绝对差版本不同。复现或跨论文引用数值时，应核对论文版本与实现，不能只凭 ECE 缩写合并比较。本笔记不转录这些数值。

## 优势、局限与研究关系

作者的贡献是把 verbal confidence 当作可系统评价的接口，并展示 prompt 和模型之间的交互。

本笔记的理解是，自述概率仍是生成内容：它会受表达模板、回答习惯、模型版本和参考判断器影响。这里的结果支持特定条件下的经验校准，不能推出模型会忠实读取某个内部“真实正确概率”。

对博士研究中的 UQ，该论文适合作为低访问权限基线。应固定模型快照、答案选择规则及 prompt，同时报告校准、错误排序与总调用成本。

## 相关笔记

- [语言自报置信度知识页](../uncertainty/verbal-confidence/index.md)
- [校准指标](../evaluation/calibration-metrics/index.md)
- [选择性预测](../evaluation/selective-prediction/index.md)

## 参考文献

- Tian, K., Mitchell, E., Zhou, A., Sharma, A., Rafailov, R., Yao, H., Finn, C., Manning, C. D. (2023). *Just Ask for Calibration: Strategies for Eliciting Calibrated Confidence Scores from Language Models Fine-Tuned with Human Feedback*. EMNLP, 5433–5442. [原文](https://aclanthology.org/2023.emnlp-main.330/)
