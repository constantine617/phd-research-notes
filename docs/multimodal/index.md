---
tags:
  - multimodal
  - trustworthiness
  - lvlm
---

# 多模态可信性与不确定性

LVLM 同时接收图像与语言，因而可靠性评价不仅要问回答是否合理，还要问模型是否正确理解并使用了相应证据。该模块连接[多模态基础](../foundations/vision-language-models/index.md)、[幻觉研究](../hallucination/hallucination-definition.md)与[不确定性量化（Uncertainty Quantification，UQ）](../uncertainty/index.md)。

## 从视觉失败到证据评价

[Goyal et al. (2017)](https://openaccess.thecvf.com/content_cvpr_2017/html/Goyal_Making_the_v_CVPR_2017_paper.html "文献引用") 通过互补图像减少视觉问答（Visual Question Answering，VQA）中的语言捷径；[Li et al. (2023)](https://aclanthology.org/2023.emnlp-main.20/ "文献引用") 用对象存在问题检查对象幻觉；[Sun et al. (2024)](https://aclanthology.org/2024.findings-acl.775/ "文献引用") 的开放多模态评价进一步涉及属性、关系、计数等内容。这些研究提示，可信性不能缩减成“是否说出了图里没有的对象”。

本模块按研究问题组织，而不按模型名称建立重复页面：

- [LVLM 幻觉](lvlm-hallucination.md)：错误发生在对象、属性、关系还是证据使用上。
- [多模态不确定性](multimodal-uncertainty.md)：信号究竟反映视觉、语言还是两者交互中的未确定程度。
- [跨模态一致性](cross-modal-consistency.md)：问题、图像、回答和外部材料之间如何核验。
- [视觉定位与可信性](grounding-for-trustworthiness.md)：区域是否支持具体主张。
- [基于视觉支持的置信度](grounding-based-confidence.md)：证据信号如何进入可靠性估计与校准。
- [多模态证据](multimodal-evidence.md)：图像、文字识别、区域和检索信息的来源及局限。

UQ 是当前主要技术路线，同时保留模型训练、对齐、decoding 干预和外部验证。后续[医学应用](../medical/index.md)进一步要求领域证据、专家判定与明确的使用条件。

## 参考文献

- Goyal, Y., Khot, T., Summers-Stay, D., Batra, D., Parikh, D. (2017). *Making the V in VQA Matter: Elevating the Role of Image Understanding in Visual Question Answering*. CVPR, 6904–6913. [Paper](https://openaccess.thecvf.com/content_cvpr_2017/html/Goyal_Making_the_v_CVPR_2017_paper.html)
- Li, Y., Du, Y., Zhou, K., Wang, J., Zhao, W. X., Wen, J.-R. (2023). *Evaluating Object Hallucination in Large Vision-Language Models*. EMNLP, 292–305. [Paper](https://aclanthology.org/2023.emnlp-main.20/)
- Sun, Z., Shen, S., Cao, S., et al. (2024). *Aligning Large Multimodal Models with Factually Augmented RLHF*. Findings of ACL；首版预印本 2023. [Paper](https://aclanthology.org/2024.findings-acl.775/)
