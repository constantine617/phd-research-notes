---
tags:
  - evaluation
  - ground-truth
---

# 参考真值（Ground Truth）

参考真值是实验采用的判定参照，其可靠性取决于来源、任务和建立过程。它不是一个天然无误的标签文件。数据说明应交代收集、标注、预处理与使用限制；Gebru et al. (2021) 的 Datasheets for Datasets 为这类记录提供了可复用的组织方式。

## 先定义标签，再比较分数

正确性（Correctness）判断任务是否完成；事实性（Factuality）判断事实主张是否成立；忠实性（Faithfulness）判断是否符合指定输入或来源；幻觉（Hallucination）标签则依赖研究采用的操作性定义。这些维度可以相交，不能直接合并成同一个布尔字段。

建议先写一份可执行的标签说明：输入是什么、评价单位是什么、允许使用哪些证据、什么算合格、怎样处理部分正确、无法判断与拒答。再用少量困难样例检验说明能否让不同标注者得到相近判断。标签方案冻结后，才开展测试集比较。

## 页面分工

- [正确性判定](correctness-definition.md)：将任务要求转为可执行规则。
- [幻觉标签](hallucination-labels.md)：固定证据与粒度。
- [语义等价](semantic-equivalence.md)：处理含义相同但措辞不同的回答。
- [人工评价](human-evaluation.md)：记录专业背景、分歧和裁决。
- [模型裁判](llm-as-a-judge.md)：扩大标注规模，并检查裁判自身的偏差。

当无法确定标签时，保留“无法判断”及原因。若主指标只接受二元标签，应公开排除条件与数量，并分析被排除样本是否集中在困难问题上。

## 参考文献（References）

- Gebru, T., Morgenstern, J., Vecchione, B., et al. (2021). *Datasheets for Datasets*. Communications of the ACM, 64(12), 86–92. [Paper](https://doi.org/10.1145/3458723) · [作者预印本](https://arxiv.org/abs/1803.09010)
