---
tags:
  - paper-note
  - lvlm
  - medical
  - multimodal
---

# LLaVA-Med: Training a Large Language-and-Vision Assistant for Biomedicine in One Day

## 文献信息（Metadata）

- 作者：Chunyuan Li, Cliff Wong, Sheng Zhang, Naoto Usuyama, Haotian Liu, Jianwei Yang, Tristan Naumann, Hoifung Poon, Jianfeng Gao。
- 年份：2023。
- 发表：NeurIPS 36，Datasets and Benchmarks，28541–28564。
- 原始来源：[官方页面](https://papers.nips.cc/paper_files/paper/2023/hash/5abcdf8ecdcacba028c6662789194572-Abstract-Datasets_and_Benchmarks.html)。
- 预印本：[arXiv:2306.00890](https://arxiv.org/abs/2306.00890)；本笔记以正式论文为准。
- 论文给出的项目入口：[LLaVA-Med](https://aka.ms/llava-med)。


## 研究问题与方法

Li et al. (2023)研究如何把通用大型视觉语言模型（Large Vision-Language Model，LVLM）适配为生物医学图像对话助手。LLaVA-Med 从生物医学文献图像与图注构造训练数据，学习领域词汇和指令跟随。

课程式训练包含两个阶段。第一阶段用图像–图注对训练连接模块，冻结视觉编码器与语言模型；第二阶段用指令对话数据更新连接模块和语言模型，视觉编码器保持冻结。正式论文也说明，从已有 LLaVA 初始化时第一阶段可以是可选项，不能把两阶段写成所有初始化的强制条件。

合成对话的 GPT-4 教师看到的是图注和正文中的图像引用上下文，而不是医学图像本身。这个信息边界影响数据可以支持的视觉细节和潜在标签错误。

## 实验设置与主要发现

本文按正式 24 页论文阅读，不能把早期预印本实验表直接视为相同版本。数据来自 PMC-15M 相关生物医学图像–文本资源，论文比较不同对话数据设置，包括是否加入正文引用信息。

评价分为开放对话和医学视觉问答（Visual Question Answering，VQA）。开放对话使用 GPT-4 参考回答与相对评价；这不等于专家临床准确率，教师与被评价系统的输入信息也不同。VQA 使用 VQA-RAD、SLAKE、PathVQA，并分别讨论零样本和下游任务 fine-tuning。

作者报告，相比通用 LLaVA，领域训练改善研究设置下的生物医学问答与对话表现；不同任务的收益并不一致，下游监督适配与零样本结果必须分开理解。只做词汇对齐也不足以完成指令跟随。

## 优势与局限

作者展示了文献图像–文本资源与合成指令结合的训练路线，为通用模型领域适配提供可复用范例。

本笔记的理解是，图注通常经过选择性描述，并不覆盖图像中所有信息。教师基于文字生成对话，可能把图注遗漏或文本推断传给学生。论文中的 benchmark 增益不能证明对真实临床图像、罕见情况和机构变化具有可靠性。

标题的“one day”对应论文具体硬件与训练预算，不能视为任意环境的成本保证。自动对话评价也不能替代图像证据核查、专家审核和前瞻性临床评价。

## 与博士研究主线的关系

该论文适合作为医学多模态适配的基础案例。它将训练数据、视觉证据、合成监督和评价边界连接起来，但没有直接解决幻觉检测或置信度校准，相关可靠性仍需单独研究。

## 相关笔记（Related Notes）

- [医学 LVLM](../medical/medical-lvlms.md)
- [医学评价](../medical/medical-evaluation.md)
- [多模态证据](../multimodal/multimodal-evidence.md)

## 参考文献（References）

- Li, C., Wong, C., Zhang, S., Usuyama, N., Liu, H., Yang, J., Naumann, T., Poon, H., Gao, J. (2023). *LLaVA-Med: Training a Large Language-and-Vision Assistant for Biomedicine in One Day*. NeurIPS 36, Datasets and Benchmarks, 28541–28564. [原文](https://papers.nips.cc/paper_files/paper/2023/hash/5abcdf8ecdcacba028c6662789194572-Abstract-Datasets_and_Benchmarks.html)
