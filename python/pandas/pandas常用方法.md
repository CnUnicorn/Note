[toc]

# pandas常用方法

## 1、生成DataFrame

1. 用pandas创建数据表

```python
df = pandas.DataFrame({'A': [1, 2, 3, 4, 5],
                      'B': ['a', 'b', 'c', 'd', np.nan]})
# 字典的key为DataFrame的列名，value为DataFrame对应对应列的数据
# 结果如下
   A    B
0  1    a
1  2    b
2  3    c
3  4    d
4  5  NaN
```



## 2、DataFrame使用

1. 查看DataFrame维度信息

   ```python
   df.shape
   ```

2. DataFrame基本信息（行数、列名、每列非空值行数统计、每列数据类型、使用内存等）

   ```python
   df.info()
   # 示例如下
   <class 'pandas.core.frame.DataFrame'>
   RangeIndex: 101 entries, 0 to 100
   Data columns (total 2 columns):
    #   Column  Non-Null Count  Dtype  
   ---  ------  --------------  -----  
    0   A       99 non-null     float64
    1   B       101 non-null    float64
   dtypes: float64(2)
   memory usage: 1.7 KB
   ```

3. DataFrame转成Numpy

   ```python
   df.values  # 返回类型为numpy.ndarray
   ```

4. 向DataFrame中插入一列

   ```python
   df.insert(self, loc: int, column: Any, value: Any, allow_duplicates: boolean = False) -> None
   """
   loc：插入列的索引位置
   column：列名
   value：插入的值（list，ndarray都可以）
   allow_duplicates：列名是否允许重复，默认列名不能与现有DataFrame中的列名重复
   """
   Example:
   >>> df = pd.DataFrame({'col1': [1, 2], 'col2': [3, 4]})
   >>> df
      col1  col2
   0     1     3
   1     2     4
   >>> df.insert(1, "newcol", [99, 99])
   >>> df
      col1  newcol  col2
   0     1      99     3
   1     2      99     4
   >>> df.insert(0, "col1", [100, 100], allow_duplicates=True)
   >>> df
      col1  col1  newcol  col2
   0   100     1      99     3
   1   100     2      99     4
   
   ```

5. 去重

   ```python
   DataFrame.drop_duplicates(subset=None, keep='first', inplace: bool = False, ignore_index: bool = False) -> Any
   """
   subset：按照指定的列名去重，默认=None，按所有列去重.['a', 'b']
   keep：重复项数据的保留策略。first：保留第一次出现的数据，last：保留最后一次出现的数据，False：重复的所有数据都不保留
   inplace：是否修改原数据集
   """
   ```

   



## 3、pandas.merge

`pandas.merge()` 方法用于将两个 DataFrame 合并，类似于 SQL 中的 JOIN 操作。该方法提供了多种连接方式，可以根据需要选择连接的列和方式。以下是 `pandas.merge()` 方法的参数和使用示例：

`pandas.merge()` 方法的主要参数：

- **left**：左侧 DataFrame。

- **right**：右侧 DataFrame。

- how

  ：指定连接方式，可以是以下值之一：

  - `'left'`：左连接（保留左侧 DataFrame 的所有行）。
  - `'right'`：右连接（保留右侧 DataFrame 的所有行）。
  - `'outer'`：外连接（保留左右两侧 DataFrame 的所有行）。
  - `'inner'`：内连接（仅保留左右两侧 DataFrame 都有的行）。

- **on**：指定连接的列名。该列名在两个 DataFrame 中必须都存在。

- **left_on**：左侧 DataFrame 中用作连接键的列。

- **right_on**：右侧 DataFrame 中用作连接键的列。

- **left_index**：如果为 True，使用左侧 DataFrame 的索引作为连接键。

- **right_index**：如果为 True，使用右侧 DataFrame 的索引作为连接键。

- **sort**：排序合并的数据，默认为 True。

- **suffixes**：字符串元组，用于附加到重叠列名的后缀，默认为 (`'_x'`, `'_y'`)。

## 4、np.where 结合dataframe

可以使用 `np.where` 替换 DataFrame 中某列满足某个条件的元素。

```python
import pandas as pd
import numpy as np

# 创建一个示例 DataFrame
data = {
    'A': [10, 20, 30, 40, 50],
    'B': [1, 2, 3, 4, 5]
}
df = pd.DataFrame(data)

print("原始 DataFrame:")
print(df)

# 使用 np.where 替换 DataFrame 中某列满足条件的元素
df['A'] = np.where(df['A'] > 25, 'High', 'Low')

print("\n替换后的 DataFrame:")
print(df)

```

输出：

```
原始 DataFrame:
    A  B
0  10  1
1  20  2
2  30  3
3  40  4
4  50  5

替换后的 DataFrame:
      A  B
0   Low  1
1   Low  2
2  High  3
3  High  4
4  High  5

```



## 5、实现SQL中的开窗

```sql
SELECT key1, key2, value1, value2
       , row_number() over(partition by key1, key2 order by value1 desc, value2 desc) as rn
FROM test_table
```



```python
import pandas as pd

data = {
    'key1': ['A', 'A', 'A', 'B', 'B', 'B'],
    'key2': ['X', 'X', 'Y', 'Y', 'Y', 'Z'],
    'value1': [10, 20, 30, 40, 50, 60],
    'value2': [5, 15, 25, 35, 45, 55]
}

df = pd.DataFrame(data)
print("原始 DataFrame:")
print(df)

  key1 key2  value1  value2
0    A    X      10       5
1    A    X      20      15
2    A    Y      30      25
3    B    Y      40      35
4    B    Y      50      45
5    B    Z      60      55

```



pandas实现以上SQL，可用以下两种方式：

**生成新的DataFrame**

```python
# 按 value1 和 value2 降序排序
df_sorted = df.sort_values(by=['key1', 'key2', 'value1', 'value2'], ascending=[True, True, False, False])

# 添加 row_number
df_sorted['rn'] = df_sorted.groupby(['key1', 'key2']).cumcount() + 1

print("\n添加 row_number 列后的 DataFrame:")
print(df_sorted)

```

**不改变原来DataFrame的顺序**

```python
# 如果想保持原始 DataFrame 的顺序，可以在排序和计算行号之后重新排序回原始顺序
# 保持原始顺序
df['rn'] = df.sort_values(by=['key1', 'key2', 'value1', 'value2'], ascending=[True, True, False, False]) \
             .groupby(['key1', 'key2']).cumcount() + 1

print("\n保持原始顺序的 DataFrame:")
print(df)

```

## 6、时间转换

```python
# 这里统一使用datetime.datetime

# 字符串转换成时间
str2time = datetime.datetime.strptime('2023-03-25 12:07:01', '%Y-%m-%d %H:%M:%S')
# 得到datetime时间datetime.datetime(2023, 2, 5, 12, 7, 1)

# datetime时间个格式化成指定字符串形式
str2time.strftime('%Y-%m-%d')
# 得到指定格式的时间字符，2023-03-25

```