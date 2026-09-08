---
tags:
  - evaluation
  - selective-prediction
---

# 风险–覆盖率（Risk-Coverage）

风险–覆盖率曲线描述：随着系统接受更多预测，被接受部分的平均损失怎样变化。它将置信度（Confidence）排序转化为选择性预测（Selective Prediction）的评价，而不要求分数已经校准为概率。

## 接受规则与经验量

设固定预测为 $f(x_i)$，置信度为 $c_i$，阈值为 $\tau$，接受指示为 $g_\tau(x_i)=\mathbf1[c_i\geq\tau]$。共有 $N$ 条带标签样本，$x_i$ 是输入，$y_i$ 是参照标签。

$$
\widehat C(\tau)=\frac1N\sum_{i=1}^{N}g_\tau(x_i),
\qquad
\widehat R(\tau)=
\frac{\sum_{i=1}^{N}\ell(f(x_i),y_i)g_\tau(x_i)}
{\sum_{i=1}^{N}g_\tau(x_i)}
\tag{1}
\label{eq:empirical-risk-coverage}
$$

$\widehat C$ 为经验覆盖率，$\widehat R$ 为经验选择风险（Selective Risk）；$\ell$ 为任务损失。分母必须大于零。若采用零一错误损失，$\widehat R$ 就是被接受回答的错误比例。

Geifman and El-Yaniv (2017) 以及 Geifman et al. (2019) 用覆盖率和选择风险评价带拒答的分类器。本页将相同经验计数结构用于已经建立回答正确标签的生成任务，不沿用分类论文中的保证到任意生成任务。

## 曲线怎样读

按置信度从高到低逐步加入样本，以覆盖率为横轴、风险为纵轴。覆盖率为一时，风险等于基础模型在完整回答池上的平均损失。同一覆盖率下风险较低，说明交付部分的损失更低。

理想排序先接受低损失样本，但经验曲线未必单调：加入一个正确回答可能降低平均风险，加入一个错误回答则可能升高。不能强行把观察曲线平滑成单调下降或上升，再不说明处理方式。

一个自拟例子：十个固定回答中，系统只接受置信度最高的六个，其中一个错误，则覆盖率为 $0.6$，选择风险为 $1/6$。这是对已接受集合的结果，不表示拒绝部分都错误。

## 工作点与并列分数

若分数是“不确定性越大越可疑”，应按低分优先接受。相同分数的样本通常需要整组接受；逐样本曲线隐含了组内排序或随机化，应记录约定。

在验证集选择满足风险目标的阈值后，报告测试覆盖率与风险及其区间。不能通过测试标签寻找一个刚好满足目标的阈值，再称作提前可知的风险控制。跨医院、领域或生成设置变化后，原阈值的风险可能变化。

## 相关笔记（Related Notes）

- [风险–覆盖率曲线下面积](aurc.md)

## 参考文献（References）

- Geifman, Y., El-Yaniv, R. (2017). *Selective Classification for Deep Neural Networks*. Advances in Neural Information Processing Systems, 30. [Paper](https://papers.nips.cc/paper_files/paper/2017/hash/4a8423d5e91fda00bb7e46540e2b0cf1-Abstract.html)
- Geifman, Y., Uziel, G., El-Yaniv, R. (2019). *Bias-Reduced Uncertainty Estimation for Deep Neural Classifiers*. ICLR；首版预印本 2018. [Paper](https://arxiv.org/abs/1805.08206)
