---
tags:
  - paper-note
  - multimodal
  - hallucination
  - grounding
---

# CGVR: Contradiction-Guided Visual Reasoning for Mitigating Hallucinations in Multimodal Large Language Models

## 文献信息

- 作者：Haoran Ding、Wanqi Zhou、Lei Xing、Lujuan Dang、Kaizhong Zheng、Badong Chen。前两位共同一作。
- 发表：IJCNLP-AACL 2026，4375–4387 页。本笔记依据正式会议版本。
- 原始来源：[ACL Anthology](https://aclanthology.org/2026.aacl-long.197/)、[论文 PDF](https://aclanthology.org/2026.aacl-long.197.pdf)。
- DOI：[10.18653/2026.aacl-long.197](https://doi.org/10.18653/2026.aacl-long.197)。
- 作者代码：[dhr1698/CGVR](https://github.com/dhr1698/CGVR)。本轮仅静态检查随分享提供的代码压缩包中相关评分脚本，未运行模型或复现实验。

## 研究问题

[Ding et al. (2026)](https://aclanthology.org/2026.aacl-long.197/ "文献引用")观察到：在问题前加入明显违背图像的陈述，有时能让 LVLM 更准确地回答视觉问题。作者据此提出矛盾引导视觉推理（Contradiction-Guided Visual Reasoning，CGVR），结合两份图像描述、两条推理通道和答案分数融合来缓解物体幻觉。

负向描述故意引入与图像冲突的内容，促使模型核查原图。普通描述同样由模型生成，其准确性也需要核查。

## 矛盾提示效应（Contradictory Prompting Effect，CPE）

论文 Figure 1 中，Qwen-VL 直接回答图中人数时数出四人；问题前加入“图像显示房间里没有人”后，它反驳此前提并回答五人。Figure 2 中，LLaVA-1.5 对单个滑雪者的图像描述虚构了背景中的其他人；加入“没有人在雪坡上滑雪”的前提后，所示回答减少了这类错误。

这些是论文的定性案例，说明这种现象可能出现，不代表任意矛盾提示都有效。原文 §3.1 也指出，矛盾需要明显冲突于核心视觉内容；模糊的提示可能无效甚至降低表现。

作者假设，文本与图像的冲突触发内部自我验证，使模型更多依赖视觉内容。这是作者的机制假设。案例与任务分数改善尚不能单独证明模型内部完成了特定的验证过程。

## 矛盾引导视觉推理（Contradiction-Guided Visual Reasoning，CGVR）

设 $I$ 为原始图像，$Q$ 为要回答的问题，$M$ 为同一个 LVLM。流程如下。

```text
图像 → 生成普通描述与负向描述
图像 + 问题 + 两份描述 → 矛盾感知通道 → 各候选答案分数
图像 + 问题 + 核查原则 → 原则引导通道 → 各候选答案分数
→ 融合两条通道的分数 → 回答原问题
```

两条通道都接收原图并使用同一模型。原则引导通道直接结合图像、问题与核查原则进行推理。

### 如何生成两份描述

普通提示 $P_{\mathrm{pos}}$ 要求描述图像，负向提示 $P_{\mathrm{neg}}$ 要求对物体、动作、属性或关系作否定描述。

$$
C^+=M(I,P_{\mathrm{pos}}),\qquad
C^-=M(I,P_{\mathrm{neg}}).
\tag{1}\label{eq:cgvr-captions}
$$

$C^+$ 为普通描述，$C^-$ 为负向描述。两者分别由同一模型根据图像生成。负向生成可能没有形成有效矛盾，普通描述也可能包含幻觉。

### 矛盾感知推理（Contradiction-Aware Inference）

这一通道把两份描述与问题组合，并提示负向描述可能有误，要求以图像为依据核查。

$$
R_{\mathrm{CA}}=M(I,C^-\oplus C^+\oplus Q).
\tag{2}\label{eq:cgvr-contradiction-channel}
$$

$\oplus$ 表示按提示模板拼接，$R_{\mathrm{CA}}$ 表示该通道响应。完整模板还包含说明负向描述可能有误的指令。

### 原则引导推理（Principle-Driven Inference）

另一通道使用核查原则 $G$，包括确认物体存在、只描述有视觉支持的关系、优先检查清晰可见内容，并围绕问题检查相关细节。

$$
R_{\mathrm{PD}}=M(I,G\oplus Q).
\tag{3}\label{eq:cgvr-principle-channel}
$$

与式 $\eqref{eq:cgvr-contradiction-channel}$ 不同，式 $\eqref{eq:cgvr-principle-channel}$ 不依赖生成的描述，可在矛盾描述无效时提供另一条回答路径。其效果取决于模型执行核查指令的能力。

### 如何融合答案分数

主要评估采用 Yes／No 问题。令 $S_{\mathrm{CA}}(a)$ 和 $S_{\mathrm{PD}}(a)$ 为两条通道对候选答案 $a$ 的分数，把原文 §3.5 的融合式按候选答案表示如下。

$$
A_{\mathrm{final}}=
\operatorname*{arg\,max}_{a\in\{\mathrm{Yes},\mathrm{No}\}}
\left[\alpha S_{\mathrm{CA}}(a)+(1-\alpha)S_{\mathrm{PD}}(a)\right].
\tag{4}\label{eq:cgvr-aggregation}
$$

论文采用 $\alpha=0.5$。最终 Yes／No 回答原问题 $Q$。两条通道发生分歧时，双方分数仍参与决策。

例如，两条通道对 Yes／No 的分数分别为 $(0.30,0.70)$ 和 $(0.60,0.40)$，等权融合后为 $(0.45,0.55)$，因此选择 No。这组数值仅用于演示计算。

配套压缩包中的 LLaVA 评分脚本在首个输出位置对 logits 做 softmax，默认只检查概率最高的三个 token，再将匹配到的英文 Yes／No token 概率计入分数。因此不能把这些分数写成完整回答的序列概率，或默认 Yes 与 No 的分数一定相加为 1。上面的规范化数值用于教学。代码访问了 token 概率，也不能直接迁移到只返回文本的接口。

## 实验设置与比较范围

主实验使用 LLaVA-1.5-7B 与 Qwen-VL-7B。POPE 覆盖 MS-COCO、A-OKVQA 和 GQA，分别采用 random、popular、adversarial 三种缺失物体采样设置。对抗设置会询问图中不存在但常与可见物体共现的物体。

MME 的主要幻觉子集包含存在性、计数、位置和颜色。每项得分为准确率与成对全对准确率（Accuracy+）之和，后者要求一对问题都回答正确。论文另在 Figure 5 报告了 LLaVA-1.5 的其他 MME 子任务，不应把主要二元视觉问题结果扩写为长文可靠性证据。

对照为原始模型、视觉对比解码（Visual Contrastive Decoding，VCD）和 OPERA。论文说明原始模型、VCD、CGVR 采用相同直接采样配置，OPERA 使用其 beam-search 干预。是否无需训练与是否具有相同推理预算是两回事。

## POPE 物体存在性判断

下表摘录 Table 1 的 **MS-COCO 准确率**，单位为百分比，保留模型和问题设置。

| 模型 | 设置 | 原始模型 | VCD | OPERA | CGVR |
|---|---|---:|---:|---:|---:|
| LLaVA-1.5 | Adversarial | 78.96 | 80.88 | 80.33 | 83.73 |
| LLaVA-1.5 | Random | 83.29 | 87.73 | 89.17 | 90.77 |
| Qwen-VL | Adversarial | 82.66 | 83.30 | 83.93 | 85.30 |
| Qwen-VL | Random | 84.36 | 86.03 | 86.13 | 89.33 |

这四行中 CGVR 准确率最高。LLaVA-1.5 在对抗设置下相对原始模型增加 4.77 个百分点，随机设置增加 7.48 个百分点。不能把这些差值说成所有模型、数据集上的统一提升，也不能据准确率推断召回率等每项指标均最优。

## MME 的收益与例外

下表来自 Table 2 的 **LLaVA-1.5 幻觉子集得分**。总分沿用论文报告值，单项展示值可能有舍入差异。

| 方法 | 存在性 | 计数 | 位置 | 颜色 | 总分 |
|---|---:|---:|---:|---:|---:|
| 原始模型 | 183.75 | 110.00 | 115.83 | 157.08 | 566.67 |
| VCD | 186.25 | 116.67 | 119.17 | 159.17 | 581.25 |
| OPERA | 185.00 | 138.33 | 128.67 | 153.00 | 605.00 |
| CGVR | 195.00 | 160.00 | 133.33 | 170.00 | 658.33 |

LLaVA-1.5 的四项均高于原始模型，其中计数增加 50 分。但 Qwen-VL 的位置得分从 131.67 降至 128.33，低于 OPERA 的 133.33。总分提高不代表每种任务都受益。

## 消融能说明矛盾本身的贡献吗

Table 3 使用 **LLaVA-1.5、POPE／MS-COCO 对抗设置**。

| 使用的组件 | 准确率（%） | F1（%） |
|---|---:|---:|
| 无 | 78.96 | 77.57 |
| 仅负向描述 | 82.80 | 83.00 |
| 仅普通描述 | 82.87 | 82.86 |
| 仅核查原则 | 81.57 | 79.99 |
| 两份描述 | 83.30 | 83.05 |
| 负向描述与核查原则 | 83.50 | 82.70 |
| 普通描述与核查原则 | 83.27 | 82.87 |
| 全部组件 | 83.73 | 83.81 |

完整组合最好，但仅普通描述与仅负向描述的准确率非常接近，前者还高 0.07 个百分点。加入组件也并非所有指标都单调改善，例如负向描述加核查原则的 F1 低于仅负向描述。

因此，现有结果支持组合流程有效，但不足以把全部收益归因于矛盾独有的因果作用。若要进一步检验，需要匹配文本长度、调用预算与答案融合方式，并比较普通补充描述、无关描述和真正与图像冲突的描述。上述控制实验是本笔记提出的后续验证建议。

Table 4 用 GPT-4o 生成两份描述，再由 LLaVA-1.5 回答；对抗设置准确率从 83.73% 提高到 85.97%。这说明描述来源可能影响表现，但该变体引入了额外模型，不应与主设置“同一模型生成和回答”的条件混写。

## 成本、风险与研究关系

CGVR 在推理时运行两次描述生成和两条回答通道，增加了调用和 token 成本。同图多个问题可能复用描述，但复用是否有效、实际缓存和延迟需要测量，不能由流程图直接推算已实现的加速比。

两条通道共享模型，错误可能相关。分数融合也没有证明置信度已经校准。负向描述可能没有否定关键内容，甚至诱导模型跟随错误前提。论文同样承认理论与因果解释仍不充分，更多模型、领域和复杂输入需要验证。

在多模态可信性研究中，矛盾文本可以作为检查图像依赖程度的干预条件。评估这种干预的准确性收益，需要独立参考标签和受控比较。

## 相关笔记

- [POPE 论文笔记](2023-li-pope.md)
- [视觉条件 decoding 干预](2024-favero-m3id.md)
- [LVLM 幻觉](../multimodal/lvlm-hallucination.md)
- [视觉定位与可信性](../multimodal/grounding-for-trustworthiness.md)
- [跨模态一致性](../multimodal/cross-modal-consistency.md)

## 参考文献

- Ding, H., Zhou, W., Xing, L., Dang, L., Zheng, K., Chen, B. (2026). *CGVR: Contradiction-Guided Visual Reasoning for Mitigating Hallucinations in Multimodal Large Language Models*. Proceedings of IJCNLP-AACL 2026, 4375–4387. [正式论文](https://aclanthology.org/2026.aacl-long.197/)
