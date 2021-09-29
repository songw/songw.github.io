---
layout: post
title:  "第八节pandas的介绍及Series、DataFrame的创建"
date:   2021-09-29 15:09:28 +0800
categories: 数据分析
---
## 1.Pandas 是什么？
Pandas 是一个强大的分析结构化数据的工具集；它的使用基础是 Numpy（提供高性能的矩阵运算）；用于数据挖掘和数据分析，同时也提供数据清洗功能。Pandas 的主要数据结构是 Series（一维数据）和 DataFrame（二维数据）。
## 2.Series
Series 是一种类似于一维数组的对象，是由一组数据以及一组与之相关的数据标签（即索引）组成。

创建 Series 对象的语法为 `my_series = pd.Series(data, index=index)`，这里的 data 可以是 ndarray、字典或者一个标量。下面我们就来讲下创建 Series 对象的不同方法。
### 2.1. 通过 ndarray 来创建
最简单的 Series 对象可以由一组数据生成。例如：
```python
import numpy as np
import pandas as pd

my_series = pd.Series(np.array([4, -7, 6, -5, 3, 2]))

print(my_series)
```
上面代码的输出结果中，左边的一列为索引，右边的一列为值。如果创建 Series 对象时没有明确指定索引，会自动创建一个从 0 到 n-1（n 为数据的长度） 的整数型索引。Series 对象创建完成后，我们可以通过它的 values 和 index 属性来获取数据和索引。例如：
```python
import numpy as np
import pandas as pd

my_series = pd.Series(np.array([4, -7, 6, -5, 3, 2]))

print(my_series.values)
print(my_series.index)
```
上面的例子中，在创建 Series 对象时没有明确指定索引，系统自动创建了一个索引，除了让系统自动生成索引之外，我们也可以明确指定索引。例如：
```python
import numpy as np
import pandas as pd

my_series = pd.Series(np.array([4, -7, 6, -5, 3, 2]), index=["a", "b", "c", "d", "e", "f"])

print(my_series)
```
在上面的代码中，我们通过列表 `["a", "b", "c", "d", "e", "f"]` 指定了 Series 对象 my_series 的索引。
### 2.2 通过字典来创建
Series 对象同样可以通过字典来实例化。例如：
```python
import pandas as pd

my_dict = {"f": 2, "c": 6, "d": -5, "e": 3, "a": 4, "b": -7}
my_series = pd.Series(my_dict)

print(my_series)
print(my_series.values)
print(my_series.index)
```
从上述代码的输出结果中，我们可以看出，在通过字典来实例化 Series 对象时，字典的 key 会成为 Series 对象的索引，字典的 value 会成为 Series 对象的值。当数据的类型是字典并且在没有明确指定索引的情况下，如果使用的 Python 版本 >= 3.6 并且 pandas 版本 >= 0.23，Series 对象中索引的顺序和字典中 {key: value} 的插入顺序相同；如果 Python 版本 < 3.6 或者 pandas 版本 < 0.23，Series 对象中索引的顺序为字典中 key 的词典顺序。在使用字典来实例化 Series 对象时，我们同样可以明确指定索引。当明确指定的索引和字典中的 key 完全匹配时，Series 对象的索引和数据的内容不会变化，只是索引的顺序按照明确指定的索引的顺序。例如：
```python
import pandas as pd

my_dict = {"f": 2, "c": 6, "d": -5, "e": 3, "a": 4, "b": -7}
my_series = pd.Series(my_dict, index=["a", "b", "c", "d", "e", "f"])

print(my_series)
print(my_series.values)
print(my_series.index)
```
当明确指定的索引和字典中的 key 不完全匹配时，有两种情况。一种情况是，当字典 key 的集合为指定索引的真子集时，匹配不上的 key 对应的 value 为 `NaN`（代表缺失值）。例如：
```python
import pandas as pd

my_dict = {"f": 2, "c": 6, "d": -5, "e": 3, "a": 4, "b": -7}
my_series = pd.Series(my_dict, index=["a", "b", "c", "d", "e", "f", "g"])

print(my_series)
print(my_series.values)
print(my_series.index)
```
在上面的代码中，指定索引中的 g 是没有 key 与之对应的，所以 g 对应的 value 为 `nan`。
另一种情况是，当指定的索引为字典 key 集合的真子集时，没有匹配上的 key 在 Series 对象中不存在。例如：
```python
import pandas as pd

my_dict = {"f": 2, "c": 6, "d": -5, "e": 3, "a": 4, "b": -7}
my_series = pd.Series(my_dict, index=["a", "b", "c", "d", "f"])

print(my_series)
print(my_series.values)
print(my_series.index)
```
在上面的代码中，字典中的键 e 没有匹配上，所以键 e 以及对应的值 3 都不会出现在最终的 Series 对象中。
### 2.3 通过标量来创建
当 data 为标量时，必须明确指定索引，标量会被重复一定的次数以匹配索引的长度。例如：
```python
import pandas as pd

my_series = pd.Series(3, index=["a", "b", "c", "d", "e", "f"])

print(my_series)
print(my_series.values)
print(my_series.index)
```
在上面的例子中，由于索引的长度为 6，所以 3 被重复了 6 次。
## 3.DataFrame
DataFrame 是 Pandas 中的一个表格型的数据结构，包含有一组有序的列，每列可以是不同的值类型（数值、字符串、布尔型等），DataFrame 对象既有行索引也有列索引，可以被看做由 Series 对象组成的字典。
### 3.1 通过 Series 对象的字典来创建
```python
import pandas as pd

d = {
    "Open": pd.Series([136, 137, 140, 143, 141, 142], index=['2021-07-01', '2021-07-02', '2021-07-06', '2021-07-07', '2021-07-08', '2021-07-09']),
    "High": pd.Series([137, 140, 143, 144, 144, 145], index=['2021-07-01', '2021-07-02', '2021-07-06', '2021-07-07', '2021-07-08', '2021-07-09']),
    "Low": pd.Series([135, 137, 140, 142, 140, 142], index=['2021-07-01', '2021-07-02', '2021-07-06', '2021-07-07', '2021-07-08', '2021-07-09']),
    "Close": pd.Series([137, 139, 142, 144, 143, 145], index = ['2021-07-01', '2021-07-02', '2021-07-06', '2021-07-07', '2021-07-08', '2021-07-09'])
}

df = pd.DataFrame(d)
print(df)
print(df.index)
print(df.values)
print(type(df.values))
```
在上面的代码中，`d` 为一个字典，字典的 `key` 分别为 `Open, High, Low, Close`，字典的值为 4 个 Series 对象。在最后生成的 DataFrame 对象中，`Index(['2021-07-01', '2021-07-02', '2021-07-06', '2021-07-07', '2021-07-08','2021-07-09'],dtype='object')` 为行的索引；`Index(['Open', 'High', 'Low', 'Close'], dtype='object') ` 为列的索引；DataFrame 对象值的类型为 `ndarray`。在上面的例子中，每个 Series 对象的索引是相同的，如果某个 Series 对象缺失了索引 `'2021-07-09'` 以及对应的值，则在最后生成的 DataFrame 对象中，这个缺失的索引对应的值为 `NaN`。例如：
```python
import pandas as pd

d = {
    "Open": pd.Series([136, 137, 140, 143, 141, 142], index=['2021-07-01', '2021-07-02', '2021-07-06', '2021-07-07', '2021-07-08', '2021-07-09']),
    "High": pd.Series([137, 140, 143, 144, 144, 145], index=['2021-07-01', '2021-07-02', '2021-07-06', '2021-07-07', '2021-07-08', '2021-07-09']),
    "Low": pd.Series([135, 137, 140, 142, 140, 142], index=['2021-07-01', '2021-07-02', '2021-07-06', '2021-07-07', '2021-07-08', '2021-07-09']),
    "Close": pd.Series([137, 139, 142, 144, 143], index = ['2021-07-01', '2021-07-02', '2021-07-06', '2021-07-07', '2021-07-08'])
}

df = pd.DataFrame(d)

print(df)
print(df.index)
print(df.columns)
print(df.values)
print(type(df.values))
```
虽然 Series 对象的索引会成为 DataFrame 对象行的索引，我们也可以明确指定索引，当明确指定索引时，以明确指定的索引为准，例如：
```python
import pandas as pd

d = {
    "Open": pd.Series([136, 137, 140, 143, 141, 142], index=['2021-07-01', '2021-07-02', '2021-07-06', '2021-07-07', '2021-07-08', '2021-07-09']),
    "High": pd.Series([137, 140, 143, 144, 144, 145], index=['2021-07-01', '2021-07-02', '2021-07-06', '2021-07-07', '2021-07-08', '2021-07-09']),
    "Low": pd.Series([135, 137, 140, 142, 140, 142], index=['2021-07-01', '2021-07-02', '2021-07-06', '2021-07-07', '2021-07-08', '2021-07-09']),
    "Close": pd.Series([137, 139, 142, 144, 143, 145], index = ['2021-07-01', '2021-07-02', '2021-07-06', '2021-07-07', '2021-07-08', '2021-07-09'])
}

df = pd.DataFrame(d, index=['2021-07-01', '2021-07-02', '2021-07-06', '2021-07-07'])

print(df)
print(df.index)
print(df.columns)
print(df.values)
print(type(df.values))
```
上面的例子中，明确指定的索引为 Series 对象索引的真子集，在最后生成的 DataFrame 对象中，行的索引便为明确指定的索引，没有明确指定的行索引对应的值不会出现在 DataFrame 对象中。当 Series 对象的索引为明确指定索引的真子集时，在最后生成的 DataFrame 对象中，Series 对象中不存在的索引对应的值为 `NaN`。例如：
```python
import pandas as pd

d = {
    "Open": pd.Series([136, 137, 140, 143, 141, 142], index=['2021-07-01', '2021-07-02', '2021-07-06', '2021-07-07', '2021-07-08', '2021-07-09']),
    "High": pd.Series([137, 140, 143, 144, 144, 145], index=['2021-07-01', '2021-07-02', '2021-07-06', '2021-07-07', '2021-07-08', '2021-07-09']),
    "Low": pd.Series([135, 137, 140, 142, 140, 142], index=['2021-07-01', '2021-07-02', '2021-07-06', '2021-07-07', '2021-07-08', '2021-07-09']),
    "Close": pd.Series([137, 139, 142, 144, 143, 145], index = ['2021-07-01', '2021-07-02', '2021-07-06', '2021-07-07', '2021-07-08', '2021-07-09'])
}

df = pd.DataFrame(d, index=['2021-07-01', '2021-07-02', '2021-07-06', '2021-07-07', '2021-07-08', '2021-07-09', '2021-07-10'])

print(df)
print(df.index)
print(df.columns)
print(df.values)
print(type(df.values))
```
在上面的例子中，2021-07-10 这一行的值都为 NaN。
当不明确指定列的索引时，DataFrame 对象的列索引用的是字典的 key。和明确指定行索引一样，我们也可以明确指定列索引，当明确指定索引时，以指定的索引为准。例如：
```python
import pandas as pd

d = {
    "Open": pd.Series([136, 137, 140, 143, 141, 142], index=['2021-07-01', '2021-07-02', '2021-07-06', '2021-07-07', '2021-07-08', '2021-07-09']),
    "High": pd.Series([137, 140, 143, 144, 144, 145], index=['2021-07-01', '2021-07-02', '2021-07-06', '2021-07-07', '2021-07-08', '2021-07-09']),
    "Low": pd.Series([135, 137, 140, 142, 140, 142], index=['2021-07-01', '2021-07-02', '2021-07-06', '2021-07-07', '2021-07-08', '2021-07-09']),
    "Close": pd.Series([137, 139, 142, 144, 143, 145], index = ['2021-07-01', '2021-07-02', '2021-07-06', '2021-07-07', '2021-07-08', '2021-07-09'])
}

df = pd.DataFrame(d, index=['2021-07-01', '2021-07-02', '2021-07-06', '2021-07-07', '2021-07-08', '2021-07-09'], columns=['Open', 'High', 'Low'])

print(df)
print(df.index)
print(df.columns)
print(df.values)
print(type(df.values))
```
上面的例子中，明确指定的列索引为字典的 key 集合的真子集，在最后生成的 DataFrame 对象中，列的索引便为明确指定的索引，没有明确指定的索引对应的值不会出现在 DataFrame 对象中。当字典 key 的集合为明确指定索引的真子集时，在最后生成的 DataFrame 对象中，Series 对象中不存在的列索引对应的值为 `NaN`。例如：
```python
import pandas as pd

d = {
    "Open": pd.Series([136, 137, 140, 143, 141, 142], index=['2021-07-01', '2021-07-02', '2021-07-06', '2021-07-07', '2021-07-08', '2021-07-09']),
    "High": pd.Series([137, 140, 143, 144, 144, 145], index=['2021-07-01', '2021-07-02', '2021-07-06', '2021-07-07', '2021-07-08', '2021-07-09']),
    "Low": pd.Series([135, 137, 140, 142, 140, 142], index=['2021-07-01', '2021-07-02', '2021-07-06', '2021-07-07', '2021-07-08', '2021-07-09']),
    "Close": pd.Series([137, 139, 142, 144, 143, 145], index = ['2021-07-01', '2021-07-02', '2021-07-06', '2021-07-07', '2021-07-08', '2021-07-09'])
}

df = pd.DataFrame(d, index=['2021-07-01', '2021-07-02', '2021-07-06', '2021-07-07', '2021-07-08', '2021-07-09'], columns=['Open', 'High', 'Low', 'Close', 'Volume'])

print(df)
print(df.index)
print(df.columns)
print(df.values)
print(type(df.values))
```
在上面例子中，明确指定的 `Volume` 没有对应的值，所以在最后生成的 DataFrame 对象中，Volume 列的值为 `NaN`。上面讲的是通过 Series 对象的字典来创建 DataFrame 对象，下面来讲下创建 DataFrame 对象其他几种方式。
### 3.2 通过 ndarray 的字典来创建
```python
import pandas as pd
import numpy as np

d = {
    "Open": np.array([136, 137, 140, 143, 141, 142]),
    "High": np.array([137, 140, 143, 144, 144, 145]),
    "Low": np.array([135, 137, 140, 142, 140, 142]),
    "Close": np.array([137, 139, 142, 144, 143, 145])
}

df = pd.DataFrame(d)
print(df)
```
在上面的代码中，我们没有明确指定索引，所以使用的是默认生成的索引。我们也可以明确指定索引。
```python
import pandas as pd
import numpy as np

d = {
    "Open": np.array([136, 137, 140, 143, 141, 142]),
    "High": np.array([137, 140, 143, 144, 144, 145]),
    "Low": np.array([135, 137, 140, 142, 140, 142]),
    "Close": np.array([137, 139, 142, 144, 143, 145])
}

df = pd.DataFrame(d, index=['2021-07-01', '2021-07-02', '2021-07-06', '2021-07-07', '2021-07-08', '2021-07-09'])
print(df)
```
### 3.3 通过列表的字典来创建
```python
import pandas as pd
import numpy as np

d = {
    "Open": [136, 137, 140, 143, 141, 142],
    "High": [137, 140, 143, 144, 144, 145],
    "Low": [135, 137, 140, 142, 140, 142],
    "Close": [137, 139, 142, 144, 143, 145]
}

df = pd.DataFrame(d, index=['2021-07-01', '2021-07-02', '2021-07-06', '2021-07-07', '2021-07-08', '2021-07-09'])
print(df)
```
在上面的代码中，我们通过列表的字典来创建 DataFrame 对象。
### 3.4 通过字典的列表来创建
```python
import pandas as pd
import numpy as np

d = [
    {"Open": 136, "High": 137, "Low": 135, "Close": 137},
    {"Open": 137, "High": 140, "Low": 137, "Close": 139},
    {"Open": 140, "High": 143, "Low": 140, "Close": 142},
    {"Open": 143, "High": 144, "Low": 142, "Close": 144},
    {"Open": 141, "High": 144, "Low": 140, "Close": 143},
    {"Open": 142, "High": 145, "Low": 142, "Close": 145}
]

df = pd.DataFrame(d, index=['2021-07-01', '2021-07-02', '2021-07-06', '2021-07-07', '2021-07-08', '2021-07-09'])
print(df)
```
在上面的代码中，我们通过字典的列表来创建 DataFrame 对象，在每个字典中，key 为列的索引。
### 3.5 通过元组的列表来创建
```python
import pandas as pd
import numpy as np

d = [
    (136, 137, 135, 137),
    (137, 140, 137, 139),
    (140, 143, 140, 142),
    (143, 144, 142, 144),
    (141, 144, 140, 143),
    (142, 145, 142, 145)
]

df = pd.DataFrame(d, index=['2021-07-01', '2021-07-02', '2021-07-06', '2021-07-07', '2021-07-08', '2021-07-09'], columns=['Open', 'High', 'Low', 'Close'])
print(df)
```
在上面的代码中，我们通过元组的列表来创建 DataFrame 对象，在创建 DataFrame 对象时，通过 index 参数来指定行索引，通过 columns 来指定列索引。
## 4.总结
本节课我们介绍了 Pandas 以及它的两个重要数据结构 Series 和 DataFrame，接下来介绍了 Series 对象以及 DataFrame 对象的创建。
![image](https://user-images.githubusercontent.com/1639828/135190535-6667f594-e9ef-4de9-a08c-95b41addb7ec.png)
