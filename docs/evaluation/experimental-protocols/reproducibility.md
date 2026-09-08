---
tags:
  - evaluation
  - reproducibility
---

# 可复现性（Reproducibility）

可复现记录应让另一位研究者能够确定实验输入、实际过程和输出来源。保存一个随机种子或最终分数远远不够。模型与数据文档可分别参考 Mitchell et al. (2019) 和 Gebru et al. (2021) 的记录框架。

## 最小记录包

保存模型与 tokenizer 版本、依赖包版本、硬件与数值精度、完整 prompt、decoding 设置、种子、数据版本和划分清单。记录预处理、答案提取、异常样本排除、标签构建、评价脚本与指标约定。

对于应用程序接口（Application Programming Interface，API），保留调用日期、模型标识、可用 snapshot、原始返回值、失败重试与限流处理。服务没有提供固定 snapshot 时，应明确这一限制。

缓存应能够追溯到输入及配置。修改 prompt 或模型后，不可误用旧输出；可以通过内容摘要和配置标识检查对应关系。为了保护数据和遵守使用条款，不是所有原始输出都适合公开，公开包与受控保存记录应分开。

## 数据泄漏检查

训练、校准和测试用途应分离。同一问题的多个回答、同一文档拆出的主张或同一患者的多幅图像，可能具有强关联，划分时应考虑其共同来源。

同时记录公开 benchmark 可能已出现在模型训练数据中的情况。无法检查闭源训练集时，应说明未知；不能把“没有发现重叠”写成“确定不存在污染”。

## 从缓存重新计算

冻结输出后，用保存的评价脚本重新生成指标，核对样本数、标签分布、无效输出与聚合方式。这样可以区分模型调用是否可重现，与既有结果是否能被重新计算。

最终记录应包括成功条件与已知限制。若改了评价规则，应保留旧版本及变化原因，避免把规则变化误报为模型性能提升。

## 参考文献（References）

- Mitchell, M., Wu, S., Zaldivar, A., et al. (2019). *Model Cards for Model Reporting*. FAT*, 220–229. [Paper](https://doi.org/10.1145/3287560.3287596) · [作者版本](https://arxiv.org/abs/1810.03993)
- Gebru, T., Morgenstern, J., Vecchione, B., et al. (2021). *Datasheets for Datasets*. Communications of the ACM, 64(12), 86–92. [Paper](https://doi.org/10.1145/3458723) · [作者预印本](https://arxiv.org/abs/1803.09010)
