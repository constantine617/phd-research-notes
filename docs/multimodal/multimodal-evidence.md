---
tags:
  - multimodal
  - evidence
  - grounding
---

# 多模态证据（Multimodal Evidence）

多模态证据是判断主张时可检查的图像、文字、区域或外部材料。证据来源和证据支持关系应分开：一个材料可以来自可靠渠道，却不一定支持当前主张；经过模型提取的信息还可能包含新的错误。

## 原始材料与派生材料

| 证据形式 | 能提供什么 | 主要检查 |
|---|---|---|
| 原始图像 | 可见对象、属性和空间关系 | 分辨率、裁剪、遮挡、时间与对象身份 |
| 光学字符识别（Optical Character Recognition，OCR）结果 | 图中文字的可检索表达 | 识别错误、阅读顺序、否定与数字单位 |
| 区域特征与框 | 指向候选对象或局部证据 | 区域对应是否正确，是否覆盖关系所需内容 |
| 检测器输出 | 候选类别、位置与分数 | 漏检、误检、类别表与训练域限制 |
| 检索文本 | 背景知识或外部事实依据 | 实体、时间、出处与当前图像的对应 |

表格是证据审查框架，不是互斥的模型分类。OCR 文本、对象标签与自动图像描述都是派生输出，不能因为形式变成文字就升级为无误真值。

## 证据怎样进入模型

[Sun et al. (2024)](https://aclanthology.org/2024.findings-acl.775/ "文献引用") 在事实增强的人类反馈强化学习（Reinforcement Learning from Human Feedback，RLHF）中，向奖励模型加入图像描述和正确选项等事实信息。这是训练阶段的具体做法，不能误写为所有推理时检索证据的方法。

[Li et al. (2023)](https://aclanthology.org/2023.emnlp-main.20/ "文献引用") 的 POPE 可以依据人工对象标注或自动工具建立对象列表；自动构造扩大了覆盖，却也要求检查标注漏检。对象没有出现在派生列表里，不必然表示图像里不存在。

## 冲突与信息缺失

当图像、OCR 与检索材料冲突时，先核对是否来自同一对象、同一时点和同一任务。不要通过多数投票静默删除冲突，多个材料可能共享同一错误来源。

保留每项主张的证据出处、区域或文本跨度、处理步骤与版本，并记录支持、反驳或未能判断。证据不足时可以触发进一步检索或人工核验，但这属于后续策略，不能先把该状态改标为事实错误。

医学应用还会涉及既往影像、报告和外部专业知识。当前图像与患者特定背景之间必须有可靠关联；一般医学知识不能直接证明当前个体具有某项发现。具体边界见[医学可靠性](../medical/clinical-reliability.md)。

## 相关笔记

- [证据感知不确定性量化（Uncertainty Quantification，UQ）](../uncertainty/evidence-aware/index.md)
- [跨模态一致性](cross-modal-consistency.md)

## 参考文献

- Sun, Z., Shen, S., Cao, S., et al. (2024). *Aligning Large Multimodal Models with Factually Augmented RLHF*. Findings of ACL；首版预印本 2023. [Paper](https://aclanthology.org/2024.findings-acl.775/)
- Li, Y., Du, Y., Zhou, K., Wang, J., Zhao, W. X., Wen, J.-R. (2023). *Evaluating Object Hallucination in Large Vision-Language Models*. EMNLP, 292–305. [Paper](https://aclanthology.org/2023.emnlp-main.20/)
