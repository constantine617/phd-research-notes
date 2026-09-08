---
tags:
  - paper-note
  - lvlm
  - hallucination
  - benchmark
---

# Evaluating Object Hallucination in LVLM

## 文献信息

- 作者：Yifan Li, Yifan Du, Kun Zhou, Jinpeng Wang, Wayne Xin Zhao, Ji-Rong Wen。
- 年份：2023。
- 发表：EMNLP，292–305。
- 原始来源：[官方页面](https://aclanthology.org/2023.emnlp-main.20/)。
- 预印本：[arXiv:2305.10355](https://arxiv.org/abs/2305.10355)；方法与实验叙述仍按本页注明的正式来源。
- 作者提供的代码与数据：[POPE](https://github.com/RUCAIBox/POPE)。


## 研究问题与方法

[Li et al. (2023)](https://aclanthology.org/2023.emnlp-main.20/ "文献引用")研究 LVLM 的物体幻觉（Object Hallucination）。开放描述的长度和表述方式会干扰比较，论文因此设计基于轮询的物体探测评价（Polling-based Object Probing Evaluation，POPE）。

POPE 针对图像询问某个物体是否存在。正例为标注存在的物体；负例从未出现物体中按随机、全局高频或与已出现物体高共现的策略选择。最后一种策略称为 adversarial，针对语言共现倾向设计较难负例，并非任意对抗扰动。

流程是选择图像及物体标签、构造平衡的是／否问题、获取回答，再按标准二分类规则计算准确率、精确率、召回率和 F1，并记录回答 yes 的比例。

## 实验设置与发现

主要 COCO 实验选择具有多个已标注物体的 500 张验证图像，每张构造 6 个问题。论文还借助 SEEM 自动分割结果向 A-OKVQA 和 GQA 扩展，因此这些标签与人工 COCO 标签具有不同误差来源。

测试模型包括当时的 MiniGPT-4、语言视觉助手（Large Language and Vision Assistant，LLaVA）、mPLUG-Owl、MultiModal-GPT 和 InstructBLIP。作者报告，部分模型有明显 yes 偏向，较难的负例设置会暴露物体共现先验造成的问题，问题措辞也会影响结果。

## 标签与解释边界

POPE 原始二分类标签表示物体存在与否，不能直接当作“不确定性分数的幻觉正类”。若研究检测器，应先根据模型回答与物体标签构造回答错误事件，再评价检测分数。

原文 §5.2 的 Metrics 段（第 298 页）把精确率和召回率分别对应到真实答案为 yes 和 no 的问题，这一文字说明不符合标准定义；使用时应以标准混淆矩阵定义为准。例如将物体存在作为正类时，precision 是所有 yes 预测中真实存在的比例。

## 优势、局限与研究关系

作者提供了可控的物体存在性探测方式，使回答长度差异不再是主要干扰。

本笔记的理解是，这种控制也限制了覆盖范围：结果不能直接代表开放描述的全部幻觉，更不覆盖属性、关系、医学发现或图像证据遗漏。标注不完整和自动检测错误也会影响负例。

对博士研究，POPE 适合作为物体层面受控 benchmark，需与开放回答评价和多模态证据分析组合，避免把单个分数当作整体多模态可靠性。

## 相关笔记

- [LVLM 幻觉](../multimodal/lvlm-hallucination.md)
- [LVLM benchmarks](../evaluation/benchmarks/lvlm-benchmarks.md)
- [阈值指标](../evaluation/detection-metrics/threshold-metrics.md)

## 参考文献

- Li, Y., Du, Y., Zhou, K., Wang, J., Zhao, W. X., Wen, J.-R. (2023). *Evaluating Object Hallucination in Large Vision-Language Models*. EMNLP, 292–305. [原文](https://aclanthology.org/2023.emnlp-main.20/)
