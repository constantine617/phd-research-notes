---
tags:
  - multimodal
  - hallucination
  - lvlm
---

# LVLM 的幻觉（Hallucination）

LVLM 的幻觉不仅涉及世界知识错误，还可能涉及对图像内容的无依据描述。文本在常识上合理，并不说明它忠于当前图像；准确复述图中文字，也不自动说明文字所述事实为真。

## 文献如何划定评价对象

[Rohrbach et al. (2018)](https://aclanthology.org/D18-1437/ "文献引用") 的 CHAIR 聚焦图像描述中的对象幻觉；[Li et al. (2023)](https://aclanthology.org/2023.emnlp-main.20/ "文献引用") 的 基于轮询的物体探测评价（Polling-based Object Probing Evaluation，POPE） 通过对象存在问答评价相关问题。[Sun et al. (2024)](https://aclanthology.org/2024.findings-acl.775/ "文献引用") 的 MMHal-Bench 涵盖对象属性、对抗性对象问题、比较、计数、空间关系、环境与整体描述等类别。以下组织借鉴这些任务维度，不主张存在唯一、互斥、穷尽的领域分类。

| 失败维度 | 自拟示例 | 应核查的内容 |
|---|---|---|
| 对象 | 描述了不存在的自行车 | 对象是否可见，标注是否完整 |
| 属性 | 把黑色车辆说成白色 | 是否找到正确对象，属性是否可辨 |
| 关系与计数 | 把“旁边”说成“上方”，或数量错误 | 多对象关系、遮挡与计数范围 |
| 视觉证据被忽略 | 沿用常见场景模板，遗漏图中反例 | 图像变化是否实际影响回答 |
| 图文不一致 | 回答附和问题中的错误前提 | 问题、图像与输出是否相容 |

“忽略视觉证据”更接近失败机制或行为描述，不与对象和属性错误处于严格同一分类维度。一条回答可以同时符合多个维度。

## 语言先验怎样影响回答

[Goyal et al. (2017)](https://openaccess.thecvf.com/content_cvpr_2017/html/Goyal_Making_the_v_CVPR_2017_paper.html "文献引用") 通过同题异图异答设计检验语言先验捷径。[Li et al. (2023)](https://aclanthology.org/2023.emnlp-main.20/ "文献引用") 观察到其研究模型容易提及常见或共现对象。[Favero et al. (2024)](https://openaccess.thecvf.com/content/CVPR2024/html/Favero_Multi-Modal_Hallucination_Control_by_Visual_Information_Grounding_CVPR_2024_paper.html "文献引用") 则比较有图与无图条件下的生成分布，研究视觉条件影响随生成过程减弱的现象。

这些是特定模型、数据与实验设置下的证据，不证明所有幻觉都由语言先验导致。图像分辨率不足、视觉编码损失、指代失败或额外知识错误，也需要分别检查。

## 错误、遗漏与不可见信息

漏掉用户要求的对象可能是完整性错误；没有说出某个细节不必然构成幻觉。对于遮挡或无法辨认的部分，不能把“当前图像无法确认”直接改写为“不存在”。回答中额外添加无依据的确定性断言，才需要按所用幻觉定义处理。

评价时记录主张粒度、输入可见范围、证据来源和不确定状态，避免以一项对象幻觉分数代表全部多模态可靠性。

## 相关笔记

- [视觉语言 Benchmark](../evaluation/benchmarks/lvlm-benchmarks.md)

## 参考文献

- Rohrbach, A., Hendricks, L. A., Burns, K., Darrell, T., Saenko, K. (2018). *Object Hallucination in Image Captioning*. EMNLP, 4035–4045. [Paper](https://aclanthology.org/D18-1437/)
- Li, Y., Du, Y., Zhou, K., Wang, J., Zhao, W. X., Wen, J.-R. (2023). *Evaluating Object Hallucination in Large Vision-Language Models*. EMNLP, 292–305. [Paper](https://aclanthology.org/2023.emnlp-main.20/)
- Sun, Z., Shen, S., Cao, S., et al. (2024). *Aligning Large Multimodal Models with Factually Augmented RLHF*. Findings of ACL；首版预印本 2023. [Paper](https://aclanthology.org/2024.findings-acl.775/)
- Goyal, Y., Khot, T., Summers-Stay, D., Batra, D., Parikh, D. (2017). *Making the V in VQA Matter: Elevating the Role of Image Understanding in Visual Question Answering*. CVPR, 6904–6913. [Paper](https://openaccess.thecvf.com/content_cvpr_2017/html/Goyal_Making_the_v_CVPR_2017_paper.html)
- Favero, A., Zancato, L., Trager, M., Choudhary, S., Perera, P., Achille, A., Swaminathan, A., Soatto, S. (2024). *Multi-Modal Hallucination Control by Visual Information Grounding*. Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition, 14303–14312. [Paper](https://openaccess.thecvf.com/content/CVPR2024/html/Favero_Multi-Modal_Hallucination_Control_by_Visual_Information_Grounding_CVPR_2024_paper.html)
