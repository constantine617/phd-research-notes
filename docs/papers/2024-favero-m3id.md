---
tags:
  - paper-note
  - lvlm
  - hallucination
  - grounding
  - mitigation
---

# Multi-Modal Hallucination Control by Visual Information Grounding

## 文献信息（Metadata）

- 作者：Alessandro Favero, Luca Zancato, Matthew Trager, Siddharth Choudhary, Pramuditha Perera, Alessandro Achille, Ashwin Swaminathan, Stefano Soatto。
- 年份：2024。
- 发表：CVPR，14303–14312。
- 原始来源：[官方页面](https://openaccess.thecvf.com/content/CVPR2024/html/Favero_Multi-Modal_Hallucination_Control_by_Visual_Information_Grounding_CVPR_2024_paper.html)。
- DOI：[10.1109/CVPR52733.2024.01356](https://doi.org/10.1109/CVPR52733.2024.01356)。
- 预印本：[arXiv:2403.14003](https://arxiv.org/abs/2403.14003)。


## 研究问题与方法

Favero et al. (2024)研究大型视觉语言模型（Large Vision-Language Model，LVLM）在生成过程中对图像信息的依赖。作者比较有图像与无图像条件下的下一 token 分布，构造提示依赖度量（Prompt Dependency Measure，PDM），主要使用 Hellinger 距离。

这种依赖度量并不把“低图像依赖”直接定义成幻觉。语法 token 可以不依赖图像，已经写进前缀的视觉事实也会降低后续 token 对图像的额外依赖；论文将后者与上下文压力联系起来。

作者提出多模态互信息 decoding（Multi-Modal Mutual Information Decoding，M3ID），通过条件分布与无图像语言先验的差异修正下一 token 选择。修正随生成位置变化，并在条件分布对下一 token 已较确定时受到门控约束。

## 工作流与实验

每步 decoding 需要有图像与无图像的分布，因此方法要求访问概率并控制生成过程，不能直接应用于只返回文本的封闭接口。它修改回答内容，不是回答生成后的置信度校准器。

论文还用 M3ID 产生偏好数据，通过直接偏好优化（Direct Preference Optimization，DPO）训练模型；负例由特定无图像生成流程构造。没有人工偏好标注不代表没有监督信号，监督来自合成数据流程。

实验主要围绕特定 LLaVA 版本及 7B／13B 语言骨干，在 COCO 描述任务上评价物体幻觉与物体覆盖，并使用 POPE。作者报告 M3ID 降低所测的物体幻觉，结合偏好训练可进一步改善部分结果；过强修正会损害覆盖或生成质量。

## 优势与局限

作者将视觉条件对 token 分布的影响用于实际 decoding 干预，并同时报告幻觉与内容覆盖，有助于识别通过少说内容获得分数改善的情况。

本笔记的理解是，无图像对照提供的是条件依赖信号，并非独立的真值验证。分布差异不能保证某条陈述受到正确图像区域支持，名称中的 grounding 也不表示方法输出了区域定位。

效果依赖模型访问、超参数和任务。自然图像物体实验不能直接推出医学报告可靠性；合成偏好数据也可能带入生成器偏差。

## 与博士研究主线的关系

该论文为视觉证据利用与幻觉缓解提供方法实例，也帮助区分检测、校准和生成干预。后续使用图像依赖分数时，应独立检验其与错误、视觉支持及内容覆盖的关系。

## 相关笔记（Related Notes）

- [Grounding 与可信性](../multimodal/grounding-for-trustworthiness.md)
- [幻觉缓解](../hallucination/mitigation.md)
- [视觉支持与置信度](../multimodal/grounding-based-confidence.md)

## 参考文献（References）

- Favero, A., Zancato, L., Trager, M., Choudhary, S., Perera, P., Achille, A., Swaminathan, A., Soatto, S. (2024). *Multi-Modal Hallucination Control by Visual Information Grounding*. Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition, 14303–14312. [原文](https://openaccess.thecvf.com/content/CVPR2024/html/Favero_Multi-Modal_Hallucination_Control_by_Visual_Information_Grounding_CVPR_2024_paper.html)
