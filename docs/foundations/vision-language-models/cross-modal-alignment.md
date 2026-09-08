---
tags:
  - foundations
  - vlm
  - alignment
---

# 跨模态对齐（Cross-modal Alignment）

跨模态对齐的目标，是让来自不同模态但语义相关的信息，在表示、评分或生成中建立合适的对应关系。“图像和文本已经对齐”需要进一步说明：对齐的是整张图与一句话，局部区域与短语，还是视觉输入与回答生成过程。

本页讨论视觉与语言之间的对应，不把它与模型遵循人类偏好的含义混用。理解具体论文时，应同时检查对齐单位、训练信号和任务要求。

## 整体图文匹配

全局对齐（Global Alignment）可以比较图像级与文本级的整体表示。例如，把一张海边照片与“有人在沙滩散步”配对，要求其匹配分数高于不相关描述。

对于已映射到同一空间的两个非零向量，可以使用余弦相似度（Cosine Similarity）：

$$
s(\mathbf{v},\mathbf{t})=
\frac{\mathbf{v}^{\top}\mathbf{t}}
{\|\mathbf{v}\|_2\,\|\mathbf{t}\|_2}
\tag{1}
\label{eq:cross-modal-cosine-similarity}
$$

$\mathbf{v},\mathbf{t}\in\mathbb{R}^{d}$ 分别为视觉与文本整体向量，$d$ 是共同维度，$\|\cdot\|_2$ 为欧氏范数，$s$ 为相似度分数。非零条件保证分母有定义。这个分数反映向量方向的接近程度，并不是回答正确的概率。

式 $\eqref{eq:cross-modal-cosine-similarity}$ 只是评分例子。计算出余弦相似度，并不意味着表示已经学会语义对应；对齐还取决于特征、训练目标及数据。

## 对比学习（Contrastive Learning）建立什么关系

[Radford et al. (2021)](https://proceedings.mlr.press/v139/radford21a.html "文献引用") 的CLIP使用配对图文训练：提升真实配对的相似度，并相对降低 batch 内其他配对的相似度。原方法同时考虑图像到文本与文本到图像两个方向，以对称的交叉熵目标训练。

这体现了对比学习的基本作用：相似度需要帮助区分匹配与不匹配的候选。这里的“拉近”“拉远”指训练目标对相对分数的要求，不能泛化为每一步训练中所有向量的欧氏距离都必然变化。

监督的粒度也限定了能直接要求什么。一条整体描述未必提到图中所有对象；“海边有人”也没有规定每个人的位置与衣服颜色。因此，图文配对信号与细节级别的证据标注不应混为一谈。

## 细粒度对应

细粒度对齐（Fine-grained Alignment）进一步处理区域与短语、patch 与 token、对象与实体名称等对应。它们的单位不同，不能把所有细粒度关系都当成同一个任务。

例如，[Plummer et al. (2015)](https://arxiv.org/abs/1505.04870v1 "文献引用") 将图像描述中的实体短语与图像区域建立标注联系。这比整图匹配提供了更具体的监督：不仅知道描述与图有关，还知道某个短语指向哪里。

但一个 patch 不是天然对象，一个 token 也未必对应独立实体。研究中得到的 patch–token 分数，可以作为对应关系的候选信号；是否真正定位到语言所指的对象，还需要与任务中的视觉目标比较。

## 通过生成目标建立联系

对齐也可以通过视觉条件下的文本生成训练获得，不限于对比目标。模型为了预测图像描述或回答中的 token，可以学习如何使用视觉表示。指令微调进一步提供问题、图像与期望响应之间的联系。

[Liu et al. (2023)](https://proceedings.neurips.cc/paper_files/paper/2023/hash/6dcf277ea32ce3288914faf369fe6de0-Abstract-Conference.html "文献引用") 的视觉指令微调工作说明了这一训练路径。生成式 LVLM 的语言侧需要将视觉输入用于条件生成，而不只是输出一个整图匹配分数。

不过，降低生成损失只是在给定训练目标下改善预测；如果某些训练答案可依靠文本规律预测，不能仅凭损失下降断言模型学会了所需的每项视觉对应。需要进一步检查视觉信息对具体输出的作用。

## 跨模态对齐与视觉定位（Visual Grounding）有什么区别？

视觉定位更具体地追问语言表达指向哪些视觉实体或证据。全局匹配较好，不自动意味着“左边的杯子”能被定位到正确区域，也不保证“杯子是空的”获得图像支持。

两者并非完全分离：区域与短语的对齐可以服务于定位任务，定位监督也可以帮助学习对应关系。本知识库把对齐作为较宽的跨模态关系概念，把具体指向及证据要求放在 [视觉定位](visual-grounding.md) 页面展开。

## 与可信性研究的关系

当输出与图像不一致时，可以研究对齐质量是否与失败有关，例如相关区域是否进入计算、语言表达是否对应了错误对象。但这只是待检验的解释，不能把所有错误都归因于“对齐不足”。

对 UQ，匹配分数与局部对应信息可能提供不同粒度的输入。它们是否能反映答案可靠性，需要针对目标任务验证；本页不把相似度直接解释为经过校准的 confidence。表示接口的基础见 [多模态表征](multimodal-representation.md)。

## 参考文献

- Radford, A., Kim, J. W., Hallacy, C., et al. (2021). *Learning Transferable Visual Models From Natural Language Supervision*. Proceedings of the 38th International Conference on Machine Learning, PMLR 139, 8748–8763. [Paper](https://proceedings.mlr.press/v139/radford21a.html)
- Plummer, B. A., Wang, L., Cervantes, C. M., Caicedo, J. C., Hockenmaier, J., Lazebnik, S. (2015). *Flickr30k Entities: Collecting Region-to-Phrase Correspondences for Richer Image-to-Sentence Models*. Proceedings of the IEEE International Conference on Computer Vision, 2641–2649. [Paper](https://arxiv.org/abs/1505.04870v1)
- Liu, H., Li, C., Wu, Q., Lee, Y. J. (2023). *Visual Instruction Tuning*. Advances in Neural Information Processing Systems, 36, 34892–34916. [Paper](https://proceedings.neurips.cc/paper_files/paper/2023/hash/6dcf277ea32ce3288914faf369fe6de0-Abstract-Conference.html)
