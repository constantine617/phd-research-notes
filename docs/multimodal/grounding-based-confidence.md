---
tags:
  - multimodal
  - grounding
  - calibration
  - uq
---

# 基于视觉支持的置信度（Grounding-based Confidence）

基于视觉支持的置信度尝试利用主张与图像证据的联系，估计回答是否可靠。这里的置信度必须明确对象：区域定位成功、主张得到支持和整段回答正确，是不同事件。

## 三类可区分的信号

区域定位分数反映定位模型对候选位置的判断；视觉支持分数评价区域能否支持特定主张；图像条件依赖分数比较输入图像对生成分布的影响。这些分数来自不同过程，不能统一读作“回答有多少概率正确”。

[Favero et al. (2024)](https://openaccess.thecvf.com/content/CVPR2024/html/Favero_Multi-Modal_Hallucination_Control_by_Visual_Information_Grounding_CVPR_2024_paper.html "文献引用") 的视觉 prompt 依赖度比较有图与无图分布，并用于M3ID。论文明确指出，低依赖并不必然意味着幻觉，例如连接词与已在前缀中表达的信息仍可能正确。因此，依赖度不能未经验证就当作主张级错误概率。

## 融合前先对齐评价对象

如果将语言模型分数、区域支持与外部核验结果融合，应先固定待预测事件和训练标签。不同输入分数的尺度、相关性和缺失模式可能不同；简单相加不自动产生概率。

一种可复用的实验设计是：在训练或验证数据拟合组合映射，再在独立测试集评价。保留无证据、证据冲突和定位失败状态，避免将缺失分数统一填零后假定它代表“明确不支持”。分别比较单独语言分数、单独视觉支持和两者组合，以确认新增证据是否提供额外信息。

这是实验设计建议，不是对某篇论文方法的命名或新方法贡献声明。

## 证据条件下的校准

证据条件校准（Evidence-conditioned Calibration）在这里指：不仅检查总体分数，还检查不同证据质量、对象类型或可见条件下的概率表现。样本不足时细分估计会很不稳定，应报告每组数量与区间。

[Slyman et al. (2025)](https://openaccess.thecvf.com/content/ICCV2025/html/Slyman_Calibrating_MLLM-as-a-judge_via_Multimodal_Bayesian_Prompt_Ensembles_ICCV_2025_paper.html "文献引用") 给出一个相邻但不同的已发表实例：其多模态模型裁判通过图像聚类条件化 prompt 集成权重，评价文本生成图像的偏好判断与校准。它说明视觉条件可以参与分数校准，但其目标是与人类偏好标签一致，不能直接当作区域 grounding 正确概率或医学事实性方法。

## 评价与成本

同时报告错误排序、[校准指标](../evaluation/calibration-metrics/index.md)、[选择性风险](../evaluation/selective-prediction/risk-coverage.md)和区域核验成本。若新增信号改变了最终回答，应另外评价缓解效果，不能把回答改变后的收益全部归为更好的置信度。

高支持分数也无法保证全部正确：视觉上合理的主张可能依赖错误对象，外部知识部分可能未经验证。证据链应允许研究者看到分数针对的具体内容。

## 参考文献

- Favero, A., Zancato, L., Trager, M., Choudhary, S., Perera, P., Achille, A., Swaminathan, A., Soatto, S. (2024). *Multi-Modal Hallucination Control by Visual Information Grounding*. Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition, 14303–14312. [Paper](https://openaccess.thecvf.com/content/CVPR2024/html/Favero_Multi-Modal_Hallucination_Control_by_Visual_Information_Grounding_CVPR_2024_paper.html)
- Slyman, E., Tanjim, M., Kafle, K., Lee, S. (2025). *Calibrating MLLM-as-a-judge via Multimodal Bayesian Prompt Ensembles*. Proceedings of the IEEE/CVF International Conference on Computer Vision, 17224–17234. [Paper](https://openaccess.thecvf.com/content/ICCV2025/html/Slyman_Calibrating_MLLM-as-a-judge_via_Multimodal_Bayesian_Prompt_Ensembles_ICCV_2025_paper.html)
