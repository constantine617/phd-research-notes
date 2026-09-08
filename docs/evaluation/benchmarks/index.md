---
tags:
  - evaluation
  - benchmark
---

# Benchmark 选择（Benchmark Selection）

Benchmark 是用于比较系统表现的任务、数据与评价协议组合。选择时应先问：输入和输出是否对应研究问题，标签测量什么，是否存在合适的划分与独立评价。下载同名数据文件，并不保证复现了原始 benchmark 设置。

## 本模块的四个入口

- [文本语言模型](text-llm-benchmarks.md)：问答、知识与推理。
- [幻觉与事实核查](hallucination-benchmarks.md)：来源忠实性、主张核查与长文本事实精确程度。
- [视觉语言模型](lvlm-benchmarks.md)：视觉问答、对象幻觉、开放多模态回答与定位。
- [医学任务](medical-benchmarks.md)：医学知识、影像问答与报告研究。

## 选择时的共同检查

Gebru et al. (2021) 强调数据组成、采集、标签、预处理与使用限制。对本研究还要记录：评价单位、正类比例、参考证据、不可判断状态、模型是否能访问同类训练材料，以及数据是否覆盖目标使用条件。

一个 benchmark 的得分不能证明全部可信性（Trustworthiness）。例如通用知识选择题无法单独证明图像忠实性；对象存在问答无法覆盖关系或医学报告错误。使用少量互补任务，并解释每项任务对应的失败类型，通常比堆积大量不相关分数更有价值。

后续页面列的是长期可复用的代表任务，并非最新排行榜或完整目录。每次实验仍需固定具体版本、划分和评分脚本。

## 参考文献（References）

- Gebru, T., Morgenstern, J., Vecchione, B., et al. (2021). *Datasheets for Datasets*. Communications of the ACM, 64(12), 86–92. [Paper](https://doi.org/10.1145/3458723) · [作者预印本](https://arxiv.org/abs/1803.09010)
