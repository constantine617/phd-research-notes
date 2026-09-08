---
tags:
  - evaluation
  - benchmark
  - medical
---

# 医学 Benchmark（Medical Benchmarks）

医学任务需要先明确研究评价的对象：医学知识问答、影像理解与报告生成，所依赖的参照并不相同。通用语言模型得分或医学考试得分都不能直接证明临床可靠性（Clinical Reliability）。

## 三类入口

| 资源 | 任务与参照 | 使用边界 |
|---|---|---|
| MedQA（Jin et al., 2021） | 来自医学考试的多项选择问题与答案 | 评价知识与问题求解；不是图像忠实性或真实诊疗效果评价 |
| VQA-RAD（Lau et al., 2018） | 临床人员针对放射影像提出问题并提供答案 | 专业标注有价值，但图像和问题范围有限；固定数据版本与划分 |
| MIMIC-CXR（Johnson et al., 2019） | 胸部影像与同期自由文本报告 | 是可支持多种任务的数据库，并非单一固定 benchmark；访问需满足官方要求 |

MedQA 原始预印本为 2020，正式期刊论文为 2021；本页引用正式版本。不同语言、选项整理方式或派生子集应分别说明，不能只写一个数据集名称。

## 报告不是逐句图像真值

Johnson et al. (2019) 说明放射科医生在报告时还可能看到简要临床背景与既往影像。若模型输入只有当前图像，参考报告中的比较或病史内容未必能从该图像单独推得。

因此评价报告生成时，应区分图像可支持内容、外部临床背景和未提供的时间比较。抽取器从报告得到的标签也是派生标签，需要记录工具版本、错误与不确定状态。

## 后续实验需要补充

按患者划分可以减少同源数据泄漏；外部中心、设备、群体和少见情况的验证用于考察迁移。临床相关正确性与错误严重程度需由合适专家建立规则，不能仅靠词面相似度。

本页提供数据选择入口；专家评价、图像忠实性与人机共同使用的设计见[医学评价](../../medical/medical-evaluation.md)，具体数学指标复用本模块。

## 参考文献（References）

- Jin, D., Pan, E., Oufattole, N., Weng, W.-H., Fang, H., Szolovits, P. (2021). *What Disease Does This Patient Have? A Large-Scale Open Domain Question Answering Dataset from Medical Exams*. Applied Sciences, 11(14), 6421. [Paper](https://doi.org/10.3390/app11146421) · [作者预印本](https://arxiv.org/abs/2009.13081)
- Lau, J. J., Gayen, S., Ben Abacha, A., Demner-Fushman, D. (2018). *A dataset of clinically generated visual questions and answers about radiology images*. Scientific Data, 5, 180251. [Paper](https://www.nature.com/articles/sdata2018251)
- Johnson, A. E. W., Pollard, T. J., Berkowitz, S. J., et al. (2019). *MIMIC-CXR, a de-identified publicly available database of chest radiographs with free-text reports*. Scientific Data, 6, 317. [Paper](https://www.nature.com/articles/s41597-019-0322-0)
