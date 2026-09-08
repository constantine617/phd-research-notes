---
tags:
  - uq
  - confidence
---

# 语言自报置信度（Verbal Confidence）

语言自报置信度指模型用自然语言或数字表达对某个回答的把握程度；文献中也称 Verbalized Confidence。对大语言模型（Large Language Model，LLM）的不确定性量化（Uncertainty Quantification，UQ）而言，这是一类可通过文本接口取得的信号；它不等于 token 概率，也不自动等于经过校准的置信度。

## 表达形式与所问事件

直接概率询问（Direct Probability Elicitation）要求模型给出“当前回答满足指定标准的概率”。输出可以是 $0$ 到 $1$ 的数，或百分比。自然语言置信表达（Natural-language Confidence）则使用“很有把握”“证据不足”等词语；这些词的数值映射需要另行定义。

数值置信度（Numeric Confidence）只是输出格式。让模型多写几位小数，并不会增加它对概率的理解或校准程度。自报概率（Self-reported Probability）也可能集中在少数常见档位，造成大量同分样本，限制后续排序能力。

询问对象必须明确：是刚生成的具体回答完全正确，还是“如果重新回答，大概能否答对”？是符合世界事实，还是得到指定文档支持？Kadavath et al. (2022) 对具体回答自评与问题级可回答性作了区分，不能把后者的分数无条件附在任意当前回答上。

## 三种取得方式

一种方式是在原回答后要求同时输出置信度，优点是格式直接，但这个要求本身可能改变答案生成。另一种方式是固定回答，再用独立 prompt 要求评分，更容易明确被评分对象，同时增加调用成本。第三种方式通过 fine-tuning 教模型表达更合适的概率。

Lin et al. (2022) 研究第三种方式，在特定数学任务上训练模型用语言表达自身答案的不确定性，并比较了语言概率与 logits 信号。该结果不能直接解释为未经专门训练的任意模型都能自然输出准确概率。

Tian et al. (2023) 则研究通过 prompt 从接受人类反馈 fine-tuning 的模型中获取置信度。在其部分模型和问答 benchmark 上，合适的自报策略改善了校准；先考虑多个候选答案也可帮助改善结果。其 Label prob. 基线通过多次 sampling、语义等价判断和众数答案选择构造，不能概括为直接读取 token 概率。比较还需考虑答案选择与调用预算，具体边界见[论文笔记](../../papers/2023-tian-verbal-confidence.md)。

## Prompt 为什么会影响数值

“请给出概率”“请谨慎评估”“列出替代答案后给出置信度”会引导不同生成行为。要求先解释理由，既可能帮助核查，也可能促使模型为已经给出的答案寻找理由。角色设定、示例、选项顺序和评分时可见的信息都属于信号取得条件。

尺度使用同样重要。某模型把“可能”理解为中等把握，另一个可能用它作为礼貌措辞。跨语言或跨任务时，不宜沿用未经检验的词语概率表。数值解析还要区分百分比与小数，处理区间、多个数字、拒绝评分和格式错误。

Xiong et al. (2024) 观察到其研究中的模型常自报过高置信度，且不同 prompting、sampling 和聚合策略没有在所有条件下一致胜出。这与 Tian et al. (2023) 的局部积极结果并不矛盾：模型、任务和对照信号不同，结论的适用范围也不同。

## 校准与独立核查

置信度校准（Confidence Calibration）检查自报概率与经验正确频率的关系，不能只看文字是否谦逊。反复输出“也许”并不一定有助于风险排序；将所有分数普遍降低也可能仅改变数值偏差。具体边界见[校准总览](../../calibration/index.md)。

把多个样本的自报概率平均时，还要检查它们是否在评价同一个答案。若每个样本针对自己的不同答案给分，平均值并不自然对应最后选出的回答。若要求所有样本评价固定答案，则又形成另一个评分流程，应重新验证。

当前研究可以将 verbal confidence 作为文本接口下的基线，并与概率、sampling 分歧或证据信号互补。模型关于“我有把握”的陈述本身仍需要检验；流畅理由不是独立证据，辅助核查的调用和 token 也应计入成本。

## 相关笔记（Related Notes）

- [置信度与不确定性](../foundations/confidence-vs-uncertainty.md)
- [校准方法](../../calibration/calibration-methods.md)

## 参考文献（References）

- Kadavath, S., Conerly, T., Askell, A., et al. (2022). *Language Models (Mostly) Know What They Know*. arXiv:2207.05221. [Paper](https://arxiv.org/abs/2207.05221)
- Lin, S., Hilton, J., Evans, O. (2022). *Teaching Models to Express Their Uncertainty in Words*. Transactions on Machine Learning Research. [作者版本](https://arxiv.org/abs/2205.14334)
- Tian, K., Mitchell, E., Zhou, A., Sharma, A., Rafailov, R., Yao, H., Finn, C., Manning, C. D. (2023). *Just Ask for Calibration: Strategies for Eliciting Calibrated Confidence Scores from Language Models Fine-Tuned with Human Feedback*. EMNLP, 5433–5442. [Paper](https://aclanthology.org/2023.emnlp-main.330/)
- Xiong, M., Hu, Z., Lu, X., Li, Y., Fu, J., He, J., Hooi, B. (2024). *Can LLMs Express Their Uncertainty? An Empirical Evaluation of Confidence Elicitation in LLMs*. ICLR. [Paper](https://proceedings.iclr.cc/paper_files/paper/2024/hash/6733cf15e10e2cd1d59af033c3bb8507-Abstract-Conference.html)
