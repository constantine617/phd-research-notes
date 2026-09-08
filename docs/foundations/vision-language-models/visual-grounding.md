---
tags:
  - foundations
  - vlm
  - grounding
---

# 视觉定位（Visual Grounding）

视觉定位关注语言表达与具体视觉对象、区域或证据之间的对应。经典定位任务通常要求根据短语找出图像区域；在讨论生成回答的可信性时，还需要进一步问：这个区域是否支持答案所表达的属性或关系。

本页保留“视觉定位”作为标题，同时区分空间定位与更宽的证据支持含义。后者是本知识库讨论回答可靠性时采用的工作范围，不意味着所有文献都把完整的答案验证称为同一种 grounding 任务。

## 从一个简单例子理解

假设图像中，一只黑狗坐在红色沙发旁。用户问：“沙发是什么颜色？”回答为“红色”。

这里至少有三项需要区分的对应：问题中的“沙发”应指向沙发区域；颜色判断应利用该区域的可见颜色；输出“红色”应表达这一属性。定位到黑狗，即使碰巧输出“红色”，也没有提供正确对象的证据。定位到沙发，但回答“蓝色”，则说明找到对象仍不足以保证属性判断正确。

若图像颜色被遮挡或无法辨认，模型也可能根据常见场景猜测。答案偶然正确与证据足以支持答案，不是同一个判断。这个例子只是概念说明，不是对某个模型的实验结论。

## 常见任务形式与粒度

短语定位（Phrase Grounding）将描述中的短语与图像区域联系起来。[Plummer et al. (2015)](https://arxiv.org/abs/1505.04870v1 "文献引用") 的 Flickr30k Entities 工作为实体短语与区域建立了对应标注。这类任务可以涉及一句话中的多个实体，而不只是整张图像的一个主角。

指代表达理解（Referring Expression Comprehension）要求根据描述识别所指对象，例如“靠近窗户的那个杯子”。当图中存在多个杯子时，仅识别“杯子”类别不够，还需要利用能够区分目标的属性或关系。[Mao et al. (2016)](https://arxiv.org/abs/1511.02283 "文献引用") 同时研究了这种表达的生成与理解。

区域级定位（Region-level Grounding）强调证据的空间粒度。区域可以用边界框（Bounding Box）等形式表示，但“区域级”不是与前两类互斥的任务：短语定位和指代表达理解都可以输出边界框。

对生成回答，还可以检查实体、属性和关系分别由什么证据支持。比如“狗在沙发左侧”需要辨认两个对象及其相对位置，单独给出狗的边界框并未覆盖整个陈述。对于数量、关系或图中不存在某对象的判断，也未必存在一个框就能充分表达证据。

## 在生成式模型中的作用

LVLM 可以把空间指向与语言生成结合。[Peng et al. (2024)](https://proceedings.iclr.cc/paper_files/paper/2024/file/e112a4671e8779aa9f640a0e3f81bd26-Paper-Conference.pdf "文献引用") 的 Kosmos-2 将文本片段与位置表示联系起来，使模型能够处理和输出带区域指向的内容。这说明定位可以进入语言交互接口，而不只作为独立的检测步骤。

但带有坐标的输出仍是模型预测。边界框可能错误，框内属性也可能被误读；如果解释文字是在答案之后生成，还不能仅凭解释与答案一致，就证明它忠实反映了原先的计算依据。评价时应区分位置是否正确、陈述是否被支持，以及证据是否实际影响生成。

## Attention 不能自动充当证据解释

注意力（Attention）权重描述某个计算模块如何对输入表示加权。较高权重可以提示一个值得检查的位置，但并不自动等于“这个位置解释了答案”，更不自动证明它支持答案中的具体事实。

[Jain and Wallace (2019)](https://aclanthology.org/N19-1357/ "文献引用") 在所研究的文本任务中发现，不同 attention 分布可以产生近似相同的预测，attention 权重也未必与其他特征重要性信号一致。这些结果不是针对所有 LVLM 的普遍定理；它们支持的谨慎结论是，不能未经验证就把 attention 可视化当成忠实解释。

在视觉任务中，可以结合区域标注与受控输入变化检查这种联系。例如，改变与问题相关的局部内容，观察模型是否相应改变回答。这样的检查也需要控制额外影响：遮挡可能改变图像分布或破坏上下文，单次输出变化本身不构成完整因果证明。

## 与跨模态对齐的区别

[跨模态对齐](cross-modal-alignment.md) 可以只要求整图与文本在语义上相配。视觉定位则要求更具体的指向：语言中的对象究竟对应哪里，以及那里能支持什么。

这一区别也不是绝对分界。细粒度对齐可以提供定位所需的关系，定位监督也能约束对齐。需要避免的是，从“图文整体相似”直接跳到“每个生成陈述都有正确的局部证据”。

## 与后续可靠性研究的关系

视觉证据为 UQ 增加了一类可研究的信息：不仅观察输出分布是否集中，还可以考察相关对象是否能定位、关键属性是否可见，以及语言陈述与证据是否一致。它是否比其他信号更有效，需要实验比较，本页不预设结论。

Grounding 也不能覆盖全部正确性要求。一个回答即使准确描述了可见对象，仍可能在额外知识或推理上出错；图像本身没有提供的信息，也不能仅靠找到区域来确认。医疗应用中的证据要求可作为长期扩展，但不在这里加入诊断流程或数据集内容。

内部信息如何支持这些检查，见 [多模态表征](multimodal-representation.md)；本页保留为视觉定位与证据联系的概念入口，不展开具体 uncertainty 方法。

## 参考文献

- Plummer, B. A., Wang, L., Cervantes, C. M., Caicedo, J. C., Hockenmaier, J., Lazebnik, S. (2015). *Flickr30k Entities: Collecting Region-to-Phrase Correspondences for Richer Image-to-Sentence Models*. Proceedings of the IEEE International Conference on Computer Vision, 2641–2649. [Paper](https://arxiv.org/abs/1505.04870v1)
- Mao, J., Huang, J., Toshev, A., Camburu, O., Yuille, A., Murphy, K. (2016). *Generation and Comprehension of Unambiguous Object Descriptions*. Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition, 11–20. [Paper](https://arxiv.org/abs/1511.02283)
- Peng, Z., Wang, W., Dong, L., Hao, Y., Huang, S., Ma, S., Ye, Q., Wei, F. (2024). *Grounding Multimodal Large Language Models to the World*. The Twelfth International Conference on Learning Representations. [Paper](https://proceedings.iclr.cc/paper_files/paper/2024/file/e112a4671e8779aa9f640a0e3f81bd26-Paper-Conference.pdf)
- Jain, S., Wallace, B. C. (2019). *Attention is not Explanation*. Proceedings of the 2019 Conference of the North American Chapter of the Association for Computational Linguistics: Human Language Technologies, Volume 1, 3543–3556. [Paper](https://aclanthology.org/N19-1357/)
