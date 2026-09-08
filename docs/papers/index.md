---
tags:
  - paper-note
  - research-overview
---

# 论文笔记（Paper Notes）

这里记录核心论文的具体方法、实验条件和证据边界。通用定义与公式保留在知识页；论文笔记作为证据层，帮助追溯哪些结论来自哪些实验。

## 概率与语言自报置信度能否校准

- [Guo et al. (2017)：现代网络的校准与温度缩放](2017-guo-calibration.md)。
- [Tian et al. (2023)：通过 prompt 获取语言自报置信度](2023-tian-verbal-confidence.md)。

## 多次生成的分歧意味着什么

- [Wang et al. (2023)：自一致性与答案选择](2023-wang-self-consistency.md)。
- [Kuhn et al. (2023)：从字符串概率转向语义不确定性](2023-kuhn-semantic-uncertainty.md)。
- [Farquhar et al. (2024)：语义熵与幻觉检测](2024-farquhar-semantic-entropy.md)。
- [Manakul et al. (2023)：SelfCheckGPT 句子一致性检测](2023-manakul-selfcheckgpt.md)。

## 内部表征能否减少估计成本

- [Azaria and Mitchell (2023)：用激活预测陈述真假](2023-azaria-internal-state.md)。
- [Kossen et al. (2024)：用探针预测高、低语义熵](2024-kossen-semantic-entropy-probes.md)。

## 外部证据与图像如何约束回答

- [Min et al. (2023)：FActScore 的原子事实支持评价](2023-min-factscore.md)。
- [Li et al. (2023)：POPE 物体存在性探测](2023-li-pope.md)。
- [Khan and Fu (2024)：邻近问题一致性与选择性视觉问答](2024-khan-consistency-uncertainty.md)。
- [Favero et al. (2024)：视觉条件 decoding 干预](2024-favero-m3id.md)。

## 医学适配与评价需要哪些额外证据

- [Li et al. (2023)：LLaVA-Med 生物医学指令适配](2023-li-llava-med.md)。
- [Yu et al. (2023)：放射学报告指标与专家错误评价](2023-yu-radiology-evaluation.md)。

阅读时区分作者报告与笔记中的技术分析，并以各页标明的正式论文或预印本版本为准。模型名相同但 checkpoint、prompt、数据和评价协议不同，结果也不能直接拼成一个排行榜。

## 相关笔记（Related Notes）

- [研究范围](../research-overview/research-scope.md)
- [评价与 benchmark](../evaluation/index.md)
