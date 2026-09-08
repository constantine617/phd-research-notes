---
tags:
  - medical
  - lvlm
  - multimodal
---

# 医学领域的大型视觉语言模型（Large Vision-Language Model，LVLM）

医学 LVLM 将医学图像与语言输入结合，用于影像问答、研究图解读或报告相关任务。它与仅处理医学文本的大语言模型（Large Language Model，LLM）不同，也不能与所有医学图像分类模型混为一谈。

## 医学信息为什么更依赖语境

医学图像可能有多视角、多切片、不同采集协议和专业标注；文本可能包含观察发现、既往比较与临床背景。若模型只看到一张经过缩放的图像，就不能假定它拥有医生阅片时的全部信息。

Johnson et al. (2019) 的 MIMIC-CXR 将胸部影像与同期报告关联，并说明报告过程可以使用临床简要背景与既往研究。因此设计输入时应明确当前影像、历史影像和文本背景分别是否提供。

## 从通用模型到领域适配

Li et al. (2023) 的 LLaVA-Med 以通用视觉语言模型为起点，采用生物医学图像–描述对的概念对齐与生成指令对话训练；正式论文说明从已有 LLaVA 初始化时，对齐阶段可以是可选项。其构造对话时使用的 GPT-4 读取图注及相关文本，而不是直接查看图像；这使训练数据质量与图文证据边界成为必要检查。

这一工作提供领域适配实例，并不意味着短时间 fine-tuning 已充分验证临床可用性。其研究包括生物医学视觉问答（Visual Question Answering，VQA）与开放对话评价，结论应限制于相应模型和数据设置。

Lu et al. (2024) 的 CONCH 则面向计算病理学的图像与文本表征，使用病理图文预训练并考察分类、检索等任务。它说明不同医学领域需要匹配的数据与表征，但不能自动将这种视觉语言基础模型当作任意临床对话 LVLM。

## 领域不是可直接互换的标签

放射影像、病理图像和眼科图像具有不同视觉尺度、采集方式和标注需求。某一领域训练与评价的成功，不足以推断其他领域表现；从二维图解读到完整检查资料整合，也改变了任务范围。

模型记录至少包括视觉编码器、语言模型、连接结构、领域数据来源、训练阶段、输入预处理和可访问证据。后续可信性研究应在这些条件固定后，检验错误类型、置信度与证据支持，而不是只看回答是否使用了专业术语。

## 相关笔记（Related Notes）

- [医学 Benchmark](../evaluation/benchmarks/medical-benchmarks.md)

## 参考文献（References）

- Johnson, A. E. W., Pollard, T. J., Berkowitz, S. J., et al. (2019). *MIMIC-CXR, a de-identified publicly available database of chest radiographs with free-text reports*. Scientific Data, 6, 317. [Paper](https://www.nature.com/articles/s41597-019-0322-0)
- Li, C., Wong, C., Zhang, S., Usuyama, N., Liu, H., Yang, J., Naumann, T., Poon, H., Gao, J. (2023). *LLaVA-Med: Training a Large Language-and-Vision Assistant for Biomedicine in One Day*. NeurIPS 36, Datasets and Benchmarks, 28541–28564. [Paper](https://papers.nips.cc/paper_files/paper/2023/hash/5abcdf8ecdcacba028c6662789194572-Abstract-Datasets_and_Benchmarks.html)
- Lu, M. Y., Chen, B., Williamson, D. F. K., et al. (2024). *A visual-language foundation model for computational pathology*. Nature Medicine, 30, 863–874. [Paper](https://www.nature.com/articles/s41591-024-02856-4)
