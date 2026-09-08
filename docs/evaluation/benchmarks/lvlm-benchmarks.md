---
tags:
  - evaluation
  - benchmark
  - lvlm
---

# 大型视觉语言模型（Large Vision-Language Model，LVLM）的 benchmark

LVLM的评价应让图像信息真正参与任务，同时区分感知、推理、开放回答事实性（Factuality）与视觉定位（Visual Grounding）。一种任务得分不能覆盖全部维度。

## 互补的代表资源

| 资源 | 主要目标 | 标签与限制 |
|---|---|---|
| VQA v2（Goyal et al., 2017） | 视觉问答（Visual Question Answering，VQA） | 同一问题配不同答案的互补图像，减弱语言先验捷径；并不消除所有偏差 |
| CHAIR（Rohrbach et al., 2018） | 图像描述中的对象幻觉 | 根据对象标注与描述统计；不直接覆盖属性、关系或全部细节 |
| POPE（Li et al., 2023） | 用是否存在对象的问题检查对象幻觉 | 包括随机、常见与共现导向的负对象选择；不同负例设置难度不同 |
| MMHal-Bench（Sun et al., 2024） | 开放图文回答中的幻觉 | 涵盖属性、比较、计数、空间关系等问题；小规模挑战集及裁判依赖限制外推 |
| Flickr30k Entities（Plummer et al., 2015） | 短语与图像区域对应 | 区域定位标注；定位准确不自动说明完整生成回答正确 |

POPE 的“是”表示对象存在，不应直接把其原始正类称为“幻觉正类”。例如对不存在对象回答“是”是某种错误，但 benchmark 的二元标签与额外检测器的幻觉标签并非同一编码。

## 控制视觉条件

固定图像版本、分辨率、裁剪、多图顺序、问题文本与答案后处理。图像上文字是否由额外文字识别工具提供，也应记录，因为这改变了模型可访问的证据。

可用互补图像、语言单模态基线或局部证据核验检查回答是否依赖视觉。Goyal et al. (2017) 的设计直接针对语言先验问题；但任何单个对照都不足以证明完整因果解释。

本页只帮助选择任务。幻觉机制、证据冲突及定位与可靠性的联系，见[多模态可信性](../../multimodal/index.md)。

## 参考文献（References）

- Goyal, Y., Khot, T., Summers-Stay, D., Batra, D., Parikh, D. (2017). *Making the V in VQA Matter: Elevating the Role of Image Understanding in Visual Question Answering*. CVPR, 6904–6913. [Paper](https://openaccess.thecvf.com/content_cvpr_2017/html/Goyal_Making_the_v_CVPR_2017_paper.html)
- Rohrbach, A., Hendricks, L. A., Burns, K., Darrell, T., Saenko, K. (2018). *Object Hallucination in Image Captioning*. EMNLP, 4035–4045. [Paper](https://aclanthology.org/D18-1437/)
- Li, Y., Du, Y., Zhou, K., Wang, J., Zhao, W. X., Wen, J.-R. (2023). *Evaluating Object Hallucination in Large Vision-Language Models*. EMNLP, 292–305. [Paper](https://aclanthology.org/2023.emnlp-main.20/)
- Sun, Z., Shen, S., Cao, S., et al. (2024). *Aligning Large Multimodal Models with Factually Augmented RLHF*. Findings of ACL；首版预印本 2023. [Paper](https://aclanthology.org/2024.findings-acl.775/)
- Plummer, B. A., Wang, L., Cervantes, C. M., Caicedo, J. C., Hockenmaier, J., Lazebnik, S. (2015). *Flickr30k Entities: Collecting Region-to-Phrase Correspondences for Richer Image-to-Sentence Models*. Proceedings of the IEEE International Conference on Computer Vision, 2641–2649. [Paper](https://arxiv.org/abs/1505.04870v1)
