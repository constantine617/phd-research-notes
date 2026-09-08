---
tags:
  - medical
  - trustworthiness
  - multimodal
---

# 医学 AI 应用

医学是可信 LLM 与 LVLM 的长期应用方向。本模块把幻觉（Hallucination）、UQ、多模态证据和评价方法放入医学研究条件中，关注系统在什么任务、证据与人类监督下值得进一步研究。

医学图像、报告与问答提供了具体场景，但离线 benchmark 表现与临床有效性之间仍有距离。[Vasey et al. (2022)](https://www.nature.com/articles/s41591-022-01772-9 "文献引用") 的 DECIDE-AI 强调，在早期临床评价中应同时考虑实际表现、安全、人因与工作流程。

## 阅读顺序

- [医学视觉语言模型](medical-lvlms.md)：医学图文信息与领域适配。
- [医学幻觉](medical-hallucination.md)：无依据主张、错误发现、遗漏与领域知识错误。
- [临床可靠性研究](clinical-reliability.md)：UQ、拒答（Abstention）、分布变化与专家监督。
- [医学评价](medical-evaluation.md)：专业标签、临床相关错误与人机共同使用。

知识与方法仍复用[多模态可信性](../multimodal/index.md)及[评价模块](../evaluation/index.md)。不为医学场景另建一套相同指标公式，也不将模型置信度（Confidence）直接作为临床决策依据。

这里讨论研究设计与证据边界，不提供个体诊断或治疗建议。未来具体任务的选择，应由公开文献、数据可获得性、专业合作与实际评价条件共同决定。

## 参考文献

- Vasey, B., Nagendran, M., Campbell, B., et al., the DECIDE-AI expert group (2022). *Reporting guideline for the early-stage clinical evaluation of decision support systems driven by artificial intelligence: DECIDE-AI*. Nature Medicine, 28, 924–933. [Paper](https://www.nature.com/articles/s41591-022-01772-9) · [BMJ 同期发表全文](https://europepmc.org/articles/PMC9116198)
