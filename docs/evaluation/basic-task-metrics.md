---
tags:
  - evaluation
  - benchmark
---

# 基础任务指标

可靠性信号的评价不能替代生成任务本身的表现。一个错误检测器很好，不表示模型答得好；一个缓解策略减少了无依据主张，也可能同时减少有效信息。实验应先报告基础任务表现，再解释信号与系统行为。

## 指标必须匹配输出形式

短答案问答常用精确匹配（Exact Match，EM）；文本片段任务还可采用 token 重叠的 $F_1$。[Rajpurkar et al. (2016)](https://aclanthology.org/D16-1264/ "文献引用") 的 SQuAD 对每个问题使用多个参考答案，并按其任务规则计算匹配与重叠。这里的 $F_1$ 衡量答案 token 的重叠，不是[错误检测中的分类 $F_1$](detection-metrics/threshold-metrics.md)。

多项选择任务通常报告选项准确率，但必须说明选项提取、拒答与无效格式处理。[Hendrycks et al. (2021)](https://arxiv.org/abs/2009.03300 "文献引用") 的 MMLU 覆盖多学科任务；一个整体平均应搭配按学科分解，不能仅用总分断言所有领域能力相同。

开放长回答应将事实精确程度、信息完整性和任务完成度分开。[Min et al. (2023)](https://aclanthology.org/2023.emnlp-main.741/ "文献引用") 的 FActScore 评价原子事实的来源支持比例，不能奖励所有形式的信息覆盖，也不能把少写几句话带来的高精确程度等同于更好回答。

## 避免指标替代目标

词面相似不保证事实正确，最终数值正确不保证推导正确，选择题答对不保证开放回答可靠。若研究目标要求过程、证据或完整性，就应增加相应判定，而不是把一个方便的基础分数扩大解释。

同时交代聚合单位：按问题等权、按主张等权或按 token 等权，会给长回答不同权重。对多个参考取最佳匹配，也要说明参考集合的来源与覆盖范围。

## 参考文献

- Rajpurkar, P., Zhang, J., Lopyrev, K., Liang, P. (2016). *SQuAD: 100,000+ Questions for Machine Comprehension of Text*. EMNLP, 2383–2392. [Paper](https://aclanthology.org/D16-1264/)
- Hendrycks, D., Burns, C., Basart, S., et al. (2021). *Measuring Massive Multitask Language Understanding*. ICLR. [Paper](https://arxiv.org/abs/2009.03300)
- Min, S., Krishna, K., Lyu, X., Lewis, M., Yih, W., Koh, P. W., Iyyer, M., Zettlemoyer, L., Hajishirzi, H. (2023). *FActScore: Fine-grained Atomic Evaluation of Factual Precision in Long Form Text Generation*. EMNLP, 12076–12100. [Paper](https://aclanthology.org/2023.emnlp-main.741/)
