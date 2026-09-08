---
tags:
  - evaluation
  - benchmark
---

# 评估与 Benchmark（Evaluation and Benchmarks）

评估首先要明确“测什么”，然后才是“用什么分数”。同一个回答可以符合参考文本，却包含世界事实错误；一个信号可以把错误排在前面，却不能解释为错误概率。本模块将参考真值（Ground Truth）、评价指标与实验协议连接起来，服务于可信性（Trustworthiness）、幻觉（Hallucination）和不确定性量化（Uncertainty Quantification，UQ）研究。

## 从问题到实验

| 研究问题 | 应先确定的对象 | 阅读入口 |
|---|---|---|
| 能否识别错误或无依据主张？ | 正类、判定单位、证据范围 | [标签建立](ground-truth/index.md)、[检测指标](detection-metrics/index.md) |
| 分数能否解释为概率？ | 概率所指事件与验证群体 | [校准指标](calibration-metrics/index.md) |
| 拒答是否留下更可靠的回答？ | 接受规则、覆盖率与损失 | [选择性预测](selective-prediction/index.md) |
| 改进是否值得其代价？ | 完整运行过程与资源预算 | [任务指标](basic-task-metrics.md)、[效率](efficiency-metrics.md) |
| 结果是否能复现和迁移？ | 数据划分、模型版本与设置 | [实验协议](experimental-protocols/index.md)、[Benchmark 选择](benchmarks/index.md) |

这些问题没有共同的单一最优指标。Guo et al. (2017) 研究概率校准；Geifman et al. (2019) 研究风险排序与选择性预测。两条路线可以使用同一个置信度（Confidence）信号，但评价对象不同。实验报告应同时说明基础任务表现、信号表现与获取信号的成本。

例如比较两个幻觉检测器，应固定生成回答与标签，先比较排序，再在验证集确定阈值并报告测试结果。如果检测器还重写了回答，就需要另加端到端实验，因为此时被评价的输出也改变了。

## 参考文献（References）

- Guo, C., Pleiss, G., Sun, Y., Weinberger, K. Q. (2017). *On Calibration of Modern Neural Networks*. ICML, Proceedings of Machine Learning Research, 70, 1321–1330. [Paper](https://proceedings.mlr.press/v70/guo17a.html)
- Geifman, Y., Uziel, G., El-Yaniv, R. (2019). *Bias-Reduced Uncertainty Estimation for Deep Neural Classifiers*. ICLR；首版预印本 2018. [Paper](https://arxiv.org/abs/1805.08206)
