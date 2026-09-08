---
tags:
  - medical
  - reliability
  - uq
---

# 临床可靠性研究

临床可靠性不能由一次离线准确率或模型自报置信度直接确定。研究需要说明系统在何种输入、使用者、工作流程与失败处置条件下被评价。模型置信度不应直接作为临床决策依据。

## 不确定性信号能提供什么

UQ 可以为错误识别、进一步检查或拒答提供候选信号，但分数应针对明确事件，并在相应数据上评价校准与区分能力。不能因为模型在一般问答中低熵，就认为医学判断可靠。

拒答需要连同[覆盖率与选择风险](../evaluation/selective-prediction/risk-coverage.md)评价，还需考察哪些群体被更多拒绝、转交流程是否有容量，以及剩余错误的严重性。阈值只在开发数据拟合，不能通过测试标签事后保证风险目标。

## 分布变化与少见情况

[Zech et al. (2018)](https://doi.org/10.1371/journal.pmed.1002683 "文献引用") 研究胸部影像分类模型跨医院的泛化，发现模型表现与医院来源等因素有关。这是传统影像分类研究，不是现代 LVLM 的直接实验结论；它为医学系统必须检查外部环境与混杂因素提供了具体证据。

对生成系统，应相应检查医院、设备、协议、人口群体与疾病频率变化。少见情况样本不足时，既可能难以估计风险，也可能使总体校准掩盖局部问题。没有足够数据，不能给出稳定性保证。

## 人类监督与证据可追溯

[Vasey et al. (2022)](https://www.nature.com/articles/s41591-022-01772-9 "文献引用") 的 DECIDE-AI 关注早期真实使用中的临床表现、安全、人因与系统变化。研究设计不应只写“最终由医生负责”，还应明确谁看到哪些输出、何时复核、怎样处理冲突与系统失败，以及使用者是否受到界面或自信语言影响。

证据可追溯意味着能定位相关图像、文本与处理版本。一个自动生成的解释并非独立证明；应核查它是否支持实际主张，而不是仅与答案语气一致。

## 从离线研究到进一步评价

先用专家标签和外部数据检查错误、校准、拒答与子群表现，再研究模型与使用者共同工作的影响。离线表现支持的是进一步研究的证据，不等于已证明患者结局获益。具体临床有效性需要匹配应用情境的研究设计。

## 相关笔记

- [医学评价](medical-evaluation.md)
- [基于视觉支持的置信度](../multimodal/grounding-based-confidence.md)

## 参考文献

- Zech, J. R., Badgeley, M. A., Liu, M., Costa, A. B., Titano, J. J., Oermann, E. K. (2018). *Variable generalization performance of a deep learning model to detect pneumonia in chest radiographs: A cross-sectional study*. PLOS Medicine, 15(11), e1002683. [Paper](https://doi.org/10.1371/journal.pmed.1002683)
- Vasey, B., Nagendran, M., Campbell, B., et al., the DECIDE-AI expert group (2022). *Reporting guideline for the early-stage clinical evaluation of decision support systems driven by artificial intelligence: DECIDE-AI*. Nature Medicine, 28, 924–933. [Paper](https://www.nature.com/articles/s41591-022-01772-9) · [BMJ 同期发表全文](https://europepmc.org/articles/PMC9116198)
