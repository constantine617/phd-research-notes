---
tags:
  - evaluation
  - reproducibility
---

# 实验协议

实验协议固定比较条件，使结果能够归因于所研究的变化。先确定任务、回答池、标签与主要指标，再选择模型和设置。测试集不应承担阈值、prompt 或方法配置的反复选择。

## 建议的执行顺序

1. 选择数据与划分，检查重复、泄漏与任务适用范围。
2. 确认[模型及访问方式](model-selection.md)，冻结版本。
3. 固定[sampling 设置](sampling-settings.md)、prompt 与输出后处理。
4. 在训练或验证数据上开发方法、拟合校准器、选择阈值。
5. 冻结配置，在测试数据评价，并按[统计报告](statistical-reporting.md)呈现差异与不确定性。
6. 保存[复现记录](reproducibility.md)，说明尚不能复现的外部条件。

[Mitchell et al. (2019)](https://doi.org/10.1145/3287560.3287596 "文献引用") 的 Model Cards 强调模型细节、用途与分组评价；[Gebru et al. (2021)](https://doi.org/10.1145/3458723 "文献引用") 的 Datasheets 强调数据建立与使用过程。这些记录可以帮助说明一次实验的适用边界，但文档齐全本身不能证明实验无偏。

方法消融应一次明确改变什么。如果同时换模型、换输出、换标签和换阈值，就不能只把最终提升归因于新置信度信号。对于会修改回答的缓解策略，分别设计固定回答上的信号比较和完整系统比较。

## 参考文献

- Mitchell, M., Wu, S., Zaldivar, A., et al. (2019). *Model Cards for Model Reporting*. FAT*, 220–229. [Paper](https://doi.org/10.1145/3287560.3287596) · [作者版本](https://arxiv.org/abs/1810.03993)
- Gebru, T., Morgenstern, J., Vecchione, B., et al. (2021). *Datasheets for Datasets*. Communications of the ACM, 64(12), 86–92. [Paper](https://doi.org/10.1145/3458723) · [作者预印本](https://arxiv.org/abs/1803.09010)
