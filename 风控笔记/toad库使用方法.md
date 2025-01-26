# 关于toad.qulity方法

toad库中有专门的issue

https://github.com/amphibian-dev/toad/issues/100

https://github.com/amphibian-dev/toad/issues/47



以下为isuue的回答，问题可以点击链接查看：

`toad.quality` 计算IV等需要分箱的指标时，只会对 数值类型 的变量进行分箱，如果原始变量不是数值类型，则按照类别型变量直接进行计算，而通常类别较多时，IV会越高这是符合逻辑的。我想这应该可以帮你理解1、2问题的现象。
对于 `selection` 中的 数值变量，默认会使用 `dt` 进行 `10` 组的分箱



`quality` 函数默认的分箱方法是使用决策树分箱，默认分10箱。

其他三个问题基本都是关于分箱的，我调整顺序回答你一下：

- (3) toad 中 `Combiner` 模块是负责分箱操作的，toad 中几乎所有的分箱都是依赖它来完成的。 `Combiner` 在分箱时会对 类别型变量 首先进行 `WOE` 编码排序，然后进行相似合并，所以基本不需要考虑分别分箱
- (2) `Combiner` 默认输出就是分箱编号，从 0 开始，当然也可以使用 `labels` 参数输出分箱内容，详情可以查看一下文档
- (1) 如果你希望使用特殊分箱策略进行分箱再计算 `IV`，目前有两种方式可以实现：
  - `quality` 函数支持传入 `method`, `n_bins` 等参数控制分箱策略，参数同 `Combiner`，参考文档看一下
  - 如果你希望可以手动调整某些特殊分箱，可以使用 `Combiner` 对数据进行分箱，然后将分箱好的数据送进 `quality` 函数中进行 `IV` 计算



# toad自定义分箱

```python
import toad
import pandas as pd

# 创建示例数据
data = {
    'feature': [1, 2, 2, 3, 3, 3, 4, 4, 4, 4, 99],
    'target': [0, 0, 1, 0, 1, 1, 0, 1, 1, 1, 0]
}
df = pd.DataFrame(data)

# 初始化 Combiner
combiner = toad.transform.Combiner()

# 进行自动分箱，bins 参数指定分箱的数量
combiner.fit(df, y='target', method='chi', n_bins=4, empty_separate=True)

# 打印初始的分箱方案
print("初始分箱方案：")
print(combiner.export())

# 自定义合并分箱，将单值 99 合并到 [3.0, 4.0) 这个箱中
combiner.set_rules({'feature': [[1.0, 2.0], [2.0, 3.0], [3.0, 4.0, 99], [4.0, 100.0]]})

# 打印合并后的分箱方案
print("合并后的分箱方案：")
print(combiner.export())

# 使用新的分箱方案对数据进行转换
binned_data = combiner.transform(df)
print("分箱后的数据：")
print(binned_data)

```

```
初始分箱方案：
{'feature': [[1.0, 2.0], [2.0, 3.0], [3.0, 4.0], [4.0, 100.0]]}
合并后的分箱方案：
{'feature': [[1.0, 2.0], [2.0, 3.0], [3.0, 4.0, 99], [4.0, 100.0]]}
分箱后的数据：
    feature  target
0         0       0
1         0       0
2         0       1
3         1       0
4         1       1
5         1       1
6         2       0
7         2       1
8         2       1
9         2       1
10        2       0

```

**自动分箱**： 使用 `combiner.fit` 方法对数据进行自动分箱。在这个示例中，我们使用了 `chi` 方法进行卡方分箱，分成 4 个箱。

**自定义合并分箱**： 使用 `combiner.set_rules` 方法自定义分箱规则，并将单值 `99` 合并到 `[3.0, 4.0)` 这个箱中。

**转换数据**： 使用新的分箱方案对原始数据进行转换，得到分箱后的数据。




# toad.metrics.KS_bucket

这个方法里面，空值会被默认替换为-1，放到最小一箱，需要结合特征实际指标含义来考虑。如果空值赋值为-1不满足业务含义，需要自己提前处理空值