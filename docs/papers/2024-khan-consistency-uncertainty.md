---
tags:
  - paper-note
  - vlm
  - uq
  - multimodal
---

# Consistency and Uncertainty: Identifying Unreliable Responses From Black-Box VLM for Selective 视觉问答（Visual Question Answering，VQA）

## 文献信息

- 作者：Zaid Khan, Yun Fu。
- 年份：2024。
- 发表：CVPR，10854–10863。
- 原始来源：[官方页面](https://openaccess.thecvf.com/content/CVPR2024/html/Khan_Consistency_and_Uncertainty_Identifying_Unreliable_Responses_From_Black-Box_Vision-Language_Models_CVPR_2024_paper.html)。
- DOI：[10.1109/CVPR52733.2024.01032](https://doi.org/10.1109/CVPR52733.2024.01032)。
- 预印本：[arXiv:2404.10193](https://arxiv.org/abs/2404.10193)。


## 研究问题与方法

[Khan and Fu (2024)](https://openaccess.thecvf.com/content/CVPR2024/html/Khan_Consistency_and_Uncertainty_Identifying_Unreliable_Responses_From_Black-Box_Vision-Language_Models_CVPR_2024_paper.html "文献引用")研究 VQA 中的选择性预测：当只能访问 VLM 的回答接口时，能否通过邻近问题上的一致性识别不可靠回答。

方法先取得原始图像–问题的答案，再由辅助视觉问题生成模型根据图像与该答案生成多个邻近问题。目标 VQA 模型回答这些问题，回答与原始答案的吻合程度构成一致性信号。

辅助生成器使用 BLIP 基础并在 VQAv2 上 fine-tuning。因此黑盒访问描述的是目标模型接口，不表示完整系统没有额外训练组件。邻近问题也未被保证是严格保持语义的原问题改写。

## 实验设置与发现

论文在 VQAv2、OK-VQA、AdVQA 上使用 BLIP、ALBEF 和 BLIP-2 FLAN-T5 等模型。作者比较一致性、原始置信度与回答错误之间的关系，并分析不同一致性组中的选择风险。

作者报告，一致性与原始置信度提供不同信息；在研究设置中，一致性较高的回答往往具有较低风险，在对抗性问题上的关系较弱。该结论支持用一致性补充已有分数，并不证明得到经过校准的正确概率。

原文部分风险–覆盖率曲线是在一致性分组内部计算的。组内保留比例与全体样本的保留比例分母不同，不能把图中的组内覆盖率直接解释为系统在完整测试集上的覆盖率。

## 优势与局限

作者把输入侧问题变化与输出侧答案稳定性联系起来，提供了不依赖目标 hidden state 的评价路线。

本笔记的理解是，生成器根据原始答案构造问题可能放大该答案的偏向。若问题不再询问同一事实，答案不同可能合理；若所有问题都围绕错误先验，一致性也可能很高。要解释该信号，需要检查问题质量与图像相关性。

成本包括辅助问题生成、目标模型多次回答及一致性判断。模型迁移时还要检查辅助生成器是否适用于新的图像领域。

## 与博士研究主线的关系

这篇论文把黑盒 UQ 连接到多模态一致性和选择性问答。研究中应分别检验问题扰动质量、分数的错误排序和最终风险–覆盖率表现。

## 相关笔记

- [跨模态一致性](../multimodal/cross-modal-consistency.md)
- [风险–覆盖率](../evaluation/selective-prediction/risk-coverage.md)
- [多模态不确定性](../multimodal/multimodal-uncertainty.md)

## 参考文献

- Khan, Z., Fu, Y. (2024). *Consistency and Uncertainty: Identifying Unreliable Responses From Black-Box Vision-Language Models for Selective Visual Question Answering*. Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition, 10854–10863. [原文](https://openaccess.thecvf.com/content/CVPR2024/html/Khan_Consistency_and_Uncertainty_Identifying_Unreliable_Responses_From_Black-Box_Vision-Language_Models_CVPR_2024_paper.html)
