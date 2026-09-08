---
tags:
  - uq
  - correctness
---

# 不确定性与正确性（Uncertainty vs Correctness）

正确性由任务要求和判定依据决定，不确定性由估计器及其观察条件决定。研究不确定性量化（Uncertainty Quantification，UQ）的目的之一，是检验二者在何种条件下具有可用关系，而不是预先把高不确定性定义成错误。基础定义沿用[正确性与可靠性](../../hallucination/reliability-correctness.md)。

## 标签先于分数解释

对于大语言模型（Large Language Model，LLM），最终答案正确、推理过程有效、主张得到文档支持，以及格式满足要求，可以是不同标签。参考真值（Ground Truth）来自数据集、证据或人工规则，也可能存在不完整和分歧。若标签没有明确，检测器“识别错误”的说法便缺乏固定对象。

长回答尤其不能默认一个二元标签足够。Min et al. (2023) 的 FActScore 拆分原子事实并判断来源支持，说明局部事实可以分别评价。主张支持比例既不等于整段完全正确概率，也不直接评价推理或任务覆盖程度。

## 四种组合都可能出现

| 观察到的状态 | 示例性解释 | 值得检查的因素 |
|---|---|---|
| 低不确定性且正确 | 模型稳定给出相同正确答案 | 是否只是任务过于简单 |
| 高不确定性且错误 | 多次生成出现不同猜测 | 是否属于信号能够识别的失败 |
| 低不确定性但错误 | 反复复述同一个错误事实 | 系统性误解、共同偏差、证据缺失 |
| 高不确定性但正确 | 当前答案正确，其他样本给出多种表达或有效解 | 语义分组、任务开放程度、样本不足 |

表格展示逻辑上允许的情况，不表示四种情况频率相同。高置信度错误（Confidently Wrong）是 UQ 的关键困难之一；低置信度但正确（Uncertain but Correct）则提醒我们，过于保守的系统可能拒绝本来可用的回答。

Farquhar et al. (2024) 将语义熵（Semantic Entropy）用于随生成变化出现的一类错误，并明确它难以发现持续生成同一种错误的情形。该限制来自信号所观察的变化范围，而不是调整一个阈值就能普遍解决的问题。

## 多个有效答案与歧义

要求“给出一个满足条件的例子”时，不同正确例子可以形成不同语义类别。此时高语义分歧未必是坏事。要求唯一事实答案时，分歧则可能更有诊断价值。语义等价与任务正确不能使用同一条未经说明的规则代替。

歧义样本也不宜随意贴错。问题缺少时间或对象，模型可能针对不同合理解释回答。先明确是否应追问、是否接受多个解释，再评价当前输出。否则分数可能成功发现了歧义，却因为参考答案只保留一种解释而被算作误判。

## 经验关系需要怎样验证

在固定任务、模型与生成设置下，可以观察错误是否更集中在某一分数区域，以及这种关系能否延续到独立数据。还应检查低分歧错误和高分歧正确分别包含什么类型，判断信号测到的是实际风险还是长度、题型或表达习惯。

排序能力与[置信度校准（Confidence Calibration）](../../calibration/index.md)需要分别验证。Xiong et al. (2024) 在置信度表达研究中同时考察校准和失败预测，发现改进前者并不自动解决后者。一个数值接近正确频率的估计，也仍可能无法把具体错误精确挑出。

当分数驱动拒答或追加检查时，还需评价最后交付的回答群体。大量拒答可以降低保留下来的错误比例，但也减少有效服务；追加生成可能改变原本被评分的回答。当前研究因此同时关注标签、信号和决策对象，保持“这个分数能检测哪些错误”与“系统最终提供了什么”之间的对应。

## 相关笔记（Related Notes）

- [置信度与不确定性](confidence-vs-uncertainty.md)
- [幻觉定义](../../hallucination/hallucination-definition.md)

## 参考文献（References）

- Min, S., Krishna, K., Lyu, X., Lewis, M., Yih, W., Koh, P. W., Iyyer, M., Zettlemoyer, L., Hajishirzi, H. (2023). *FActScore: Fine-grained Atomic Evaluation of Factual Precision in Long Form Text Generation*. EMNLP, 12076–12100. [Paper](https://aclanthology.org/2023.emnlp-main.741/)
- Farquhar, S., Kossen, J., Kuhn, L., Gal, Y. (2024). *Detecting hallucinations in large language models using semantic entropy*. Nature, 630, 625–630. [Paper](https://www.nature.com/articles/s41586-024-07421-0)
- Xiong, M., Hu, Z., Lu, X., Li, Y., Fu, J., He, J., Hooi, B. (2024). *Can LLMs Express Their Uncertainty? An Empirical Evaluation of Confidence Elicitation in LLMs*. ICLR. [Paper](https://proceedings.iclr.cc/paper_files/paper/2024/hash/6733cf15e10e2cd1d59af033c3bb8507-Abstract-Conference.html)
