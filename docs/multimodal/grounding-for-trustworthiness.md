---
tags:
  - multimodal
  - grounding
  - trustworthiness
---

# 视觉定位（Visual Grounding）与可信性（Grounding for Trustworthiness）

[视觉定位（Visual Grounding）](../foundations/vision-language-models/visual-grounding.md)已有独立基础页。本页关注语言主张对应的区域能否成为可核查证据，以及这种对应能否帮助发现错误；不重复定位任务教程。

## 从区域指向到主张支持

一个区域框只说明模型指向哪里。要支持完整主张，还应检查对象身份、属性、关系和可见程度。多个对象的关系可能需要多个区域及全局位置，计数需要足够完整的视野，“不存在”则不能仅凭没有返回一个框判断。

[Peng et al. (2024)](https://proceedings.iclr.cc/paper_files/paper/2024/file/e112a4671e8779aa9f640a0e3f81bd26-Paper-Conference.pdf "文献引用") 的 Kosmos-2 将文本片段与位置表达相连，为这种细粒度交互提供了模型接口。但预测坐标依然可能错误，接口具备定位能力也不意味着它生成的每项事实都获得了有效证明。

## 区域级核验流程

一种通用研究设计是：保留原回答，提取可核查主张，定位候选区域，再判断区域对主张的支持程度。这里是评价流程建议，不是本知识库提出的新方法。

为每项核验分别记录“对象找到了吗”“关键属性可辨吗”“关系所需区域齐全吗”“有没有相反证据”。定位失败和核验失败应分开，否则无法判断改进来自哪里。

## 注意力（Attention）与证据的边界

注意力权重反映模型内部的信息加权，不能直接证明某个区域支持答案。[Jain and Wallace (2019)](https://aclanthology.org/N19-1357/ "文献引用") 的文本实验显示，attention 不能未经检验就等同于忠实解释；该论文不是针对所有视觉模型的普遍证明，但足以支持避免自动等同的审慎做法。

可结合区域标注、删除或替换相关区域、无关区域对照检查解释。但干预可能引入分布变化，单次答案变化也不能作为完整因果证明。答案后生成的框或解释尤其需要核验，它可能只是事后匹配。

## 视觉信息利用也可以参与缓解

[Favero et al. (2024)](https://openaccess.thecvf.com/content/CVPR2024/html/Favero_Multi-Modal_Hallucination_Control_by_Visual_Information_Grounding_CVPR_2024_paper.html "文献引用") 的多模态互信息 decoding（Multi-Modal Mutual Information Decoding，M3ID）根据有图与无图生成分布调整 decoding，提高视觉信息影响。该方法中的 grounding 主要通过条件依赖操作化，不是显式区域标注核验；也不是对正确概率的后处理校准。

区域支持与条件依赖分别提供可观察信号。两者都不能解决图像之外的全部知识与推理错误。评价仍需同时看回答内容、定位质量和失败后的处理。

## 相关笔记

- [幻觉缓解](../hallucination/mitigation.md)

## 参考文献

- Peng, Z., Wang, W., Dong, L., Hao, Y., Huang, S., Ma, S., Ye, Q., Wei, F. (2024). *Grounding Multimodal Large Language Models to the World*. The Twelfth International Conference on Learning Representations. [Paper](https://proceedings.iclr.cc/paper_files/paper/2024/file/e112a4671e8779aa9f640a0e3f81bd26-Paper-Conference.pdf)
- Jain, S., Wallace, B. C. (2019). *Attention is not Explanation*. Proceedings of the 2019 Conference of the North American Chapter of the Association for Computational Linguistics: Human Language Technologies, Volume 1, 3543–3556. [Paper](https://aclanthology.org/N19-1357/)
- Favero, A., Zancato, L., Trager, M., Choudhary, S., Perera, P., Achille, A., Swaminathan, A., Soatto, S. (2024). *Multi-Modal Hallucination Control by Visual Information Grounding*. Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition, 14303–14312. [Paper](https://openaccess.thecvf.com/content/CVPR2024/html/Favero_Multi-Modal_Hallucination_Control_by_Visual_Information_Grounding_CVPR_2024_paper.html)
