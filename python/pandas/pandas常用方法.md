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

   