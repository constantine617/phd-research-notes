---
tags:
  - evaluation
  - hallucination
  - ground-truth
---

# 幻觉标签

幻觉标签需要说明哪些主张与哪些参照发生冲突，或缺乏支持。概念边界见[幻觉定义](../../hallucination/hallucination-definition.md)。这里重点说明如何建立可复用标签。

## 四项必要约定

先固定操作性定义、证据范围、判定粒度与标注指南。例如，检查摘要是否忠于给定文章，与检查人物介绍是否符合外部知识库，是不同任务。[Pagnoni et al. (2021)](https://aclanthology.org/2021.naacl-main.383/ "文献引用") 的 FRANK 针对摘要事实错误建立分类；[Min et al. (2023)](https://aclanthology.org/2023.emnlp-main.741/ "文献引用") 的 FActScore 则逐项检查长文本原子事实的来源支持。不能直接把两套标签混在同一个“幻觉率”分母中。

证据状态至少应区分“支持”“反驳”“现有证据不足”。[Thorne et al. (2018)](https://aclanthology.org/N18-1074/ "文献引用") 的 FEVER 明确保留 Not Enough Info 类别。检索没有找到支持，可能来自检索失败或证据覆盖不足，并不自动说明主张在世界中为假。

## 粒度决定聚合含义

一段回答含多个主张时，可以报告不受支持主张的比例，也可以判断是否至少出现一项幻觉。前者偏向主张级精确程度，后者更严格且受回答长度影响。还需规定重复主张如何计数，以及不可独立核查的修辞性内容是否进入分母。

标注记录应保留原文跨度、拆分后的主张、证据出处、判定理由与裁决结果。拆分时不能丢失否定、时间、数量、比较范围或指代关系，否则标签针对的是被改写后的新命题。

## 与错误检测的关系

算术错误、格式错误或遗漏不必都归入幻觉。若研究检测“所有任务错误”，应使用相应正确性标签；若只检测“来源不支持的事实”，就保留这个更窄目标。论文之间的标签差异本身就是结果不可直接比较的重要原因。

自动标签也应抽样复核，尤其是检索失败、证据冲突和长回答。记录不确定标签的比例，比静默强制二元化更有助于解释结果。

## 参考文献

- Pagnoni, A., Balachandran, V., Tsvetkov, Y. (2021). *Understanding Factuality in Abstractive Summarization with FRANK: A Benchmark for Factuality Metrics*. NAACL-HLT, 4812–4829. [Paper](https://aclanthology.org/2021.naacl-main.383/)
- Min, S., Krishna, K., Lyu, X., Lewis, M., Yih, W., Koh, P. W., Iyyer, M., Zettlemoyer, L., Hajishirzi, H. (2023). *FActScore: Fine-grained Atomic Evaluation of Factual Precision in Long Form Text Generation*. EMNLP, 12076–12100. [Paper](https://aclanthology.org/2023.emnlp-main.741/)
- Thorne, J., Vlachos, A., Christodoulopoulos, C., Mittal, A. (2018). *FEVER: a Large-scale Dataset for Fact Extraction and VERification*. NAACL-HLT, 809–819. [Paper](https://aclanthology.org/N18-1074/)
