---
tags:
  - multimodal
  - uq
  - lvlm
---

# 多模态不确定性（Multimodal Uncertainty）

多模态不确定性讨论图像、语言及其交互中尚未确定的信息。在 LVLM 中，一个回答分数可能同时受到可见信息、问题歧义、模型知识和生成策略影响。没有一个无需假设便能把它唯一拆成若干部分的通用公式。

## 按问题定位不确定性来源

下面是实验分析的检查维度，不是相加分解，也不对应唯一文献分类。

- 视觉方面：图像模糊、遮挡、细节缺失，或模型无法稳定辨认关键对象。
- 语言方面：指代含糊、允许多种答案、缺少外部知识，或同一含义有多种表达。
- 跨模态方面：问题指向的对象与选中区域不符，图文材料冲突，或模型不能正确结合信息。
- 证据方面：外部检索与当前图像不匹配，所需历史或其他视角没有提供。

同一个模糊区域既可能反映观测限制，也可能反映当前视觉模型能力不足。认知不确定性（Epistemic Uncertainty）与数据不确定性（Aleatoric Uncertainty）的区分依赖模型、信息与任务假设，详见[既有概念页](../uncertainty/foundations/epistemic-aleatoric.md)。不能把每次 sampling 的变化直接归为数据不确定性。

## 不同信号测量不同对象

[Khan and Fu (2024)](https://openaccess.thecvf.com/content/CVPR2024/html/Khan_Consistency_and_Uncertainty_Identifying_Unreliable_Responses_From_Black-Box_Vision-Language_Models_CVPR_2024_paper.html "文献引用") 研究黑盒 VQA，通过较小的问题生成模型构造邻域问题，再检查原模型回答是否保持一致。他们在指定 VQA 数据与模型上评价这种一致性与选择性预测的关系。代理生成的问题不保证严格等价，因而分数还受代理质量影响。

[Favero et al. (2024)](https://openaccess.thecvf.com/content/CVPR2024/html/Favero_Multi-Modal_Hallucination_Control_by_Visual_Information_Grounding_CVPR_2024_paper.html "文献引用") 比较有图与无图生成分布，测量视觉 prompt 的影响。这衡量条件依赖，不是回答错误概率；语法性 token 或已在文本前缀中表达过的图像信息，都可能使两分布接近。

两种信号分别关注邻域行为与视觉条件影响，不能称为同一种不确定性，也不能直接相减来得到“纯视觉不确定性”。

## 怎样验证一个候选信号

固定待评价回答，在清晰与退化图像、语义保持的文本改写、错误前提和证据冲突等条件下检查信号。每个干预都应确认是否改变了真实答案：裁掉关键区域后，原问题可能已经不可回答。

同时评价错误排序、[概率校准](../evaluation/calibration-metrics/index.md)、[风险–覆盖率](../evaluation/selective-prediction/risk-coverage.md)与成本。回答稳定可能来自共同错误；不稳定也可能来自合理的多解或改写失真。解释必须回到任务标签与具体证据。

## 参考文献

- Khan, Z., Fu, Y. (2024). *Consistency and Uncertainty: Identifying Unreliable Responses From Black-Box Vision-Language Models for Selective Visual Question Answering*. Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition, 10854–10863. [Paper](https://openaccess.thecvf.com/content/CVPR2024/html/Khan_Consistency_and_Uncertainty_Identifying_Unreliable_Responses_From_Black-Box_Vision-Language_Models_CVPR_2024_paper.html)
- Favero, A., Zancato, L., Trager, M., Choudhary, S., Perera, P., Achille, A., Swaminathan, A., Soatto, S. (2024). *Multi-Modal Hallucination Control by Visual Information Grounding*. Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition, 14303–14312. [Paper](https://openaccess.thecvf.com/content/CVPR2024/html/Favero_Multi-Modal_Hallucination_Control_by_Visual_Information_Grounding_CVPR_2024_paper.html)
