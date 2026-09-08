---
tags:
  - multimodal
  - consistency
  - evidence
---

# 跨模态一致性（Cross-modal Consistency）

跨模态一致性检查图像、问题、生成主张与其他材料是否相容。它与[跨模态对齐](../foundations/vision-language-models/cross-modal-alignment.md)有关，但评价完整主张通常需要比整体图文相似度更细的条件。

## 检查问题、主张或检索文本是否与图像一致

问题与图像的一致性先检查问题前提是否成立，例如问题提到的对象是否可见。主张与图像的一致性检查对象、属性和关系。检索文本与图像的一致性还需要实体、时间和场景对应；一段一般知识可以正确，却与当前图像无关。

[Sun et al. (2024)](https://aclanthology.org/2024.findings-acl.775/ "文献引用") 的 MMHal-Bench 包含不存在对象的对抗性问题与空间关系等类型。这些任务说明，直接顺着问题前提回答，可能产生看似相关却不受图像支持的内容。

## 怎样利用一致性核查回答

可以先拆分回答主张，再寻找对应区域或文字，记录支持、冲突与无法判断。区域是否存在与区域是否支持全部谓词应分别判断；“找到了一个杯子”没有验证“杯子是蓝色并位于盘子左边”。

[Khan and Fu (2024)](https://openaccess.thecvf.com/content/CVPR2024/html/Khan_Consistency_and_Uncertainty_Identifying_Unreliable_Responses_From_Black-Box_Vision-Language_Models_CVPR_2024_paper.html "文献引用") 的方法在保持图像的同时，用代理模型生成邻域问题，观察回答一致性。这是一种行为核验信号，不是直接的主张–区域支持检验。其优势是适合内部信息不可见的模型，但需要额外生成与回答调用。

## 一致不等于真实

多个模型、多个回答或多个证据片段可能复述同一个错误来源。若裁判和生成器共享视觉错误，它们互相同意不能提供独立核验。检查来源独立性和具体证据，比单纯统计赞成票更重要。

对受控图像变化，预先说明何时答案应保持不变、何时应改变。语义保持扰动下的稳定性与关键内容改变后的敏感性都值得评价；始终回答同一句话只能满足前者的一部分。

## 使用时的记录

保留主张、证据区域或文本跨度、核验器版本、prompt、冲突类型和裁决。若将一致性作为置信度（Confidence）输入，仍需独立标签检验其排序与校准效果，不把一致率直接命名为正确概率。

## 相关笔记

- [多模态证据](multimodal-evidence.md)
- [基于视觉支持的置信度](grounding-based-confidence.md)

## 参考文献

- Sun, Z., Shen, S., Cao, S., et al. (2024). *Aligning Large Multimodal Models with Factually Augmented RLHF*. Findings of ACL；首版预印本 2023. [Paper](https://aclanthology.org/2024.findings-acl.775/)
- Khan, Z., Fu, Y. (2024). *Consistency and Uncertainty: Identifying Unreliable Responses From Black-Box Vision-Language Models for Selective Visual Question Answering*. Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition, 10854–10863. [Paper](https://openaccess.thecvf.com/content/CVPR2024/html/Khan_Consistency_and_Uncertainty_Identifying_Unreliable_Responses_From_Black-Box_Vision-Language_Models_CVPR_2024_paper.html)
