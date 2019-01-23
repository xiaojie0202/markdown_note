# Pandas
    pandas is an open source, BSD-licensed library providing high-performance, easy-to-use data structures and data analysis tools for the Python programming language.
## Pandas 数据类型
    Series 一维，带标签数组
    DataFrame 二维，Series容器
## Series
    类似于一维数组的对象，由一组数据和一组与之相关的标签(索引)组成
### 创建Series
```python
import pandas as pd
# 创建Series
t = pd.Series([1, 2, 3, 4, 5])

# 根据字典创建Series，key为索引，value为值
t1 = pd.Series({'name': 'xiaojie', 'age': 18, 'tel': 10086})

# 创建Series指定索引
t2 = pd.Series([1, 2, 3, 4, 5], index=['a', 'b', 'c', 'd', 'e'])
t2.astype(float) # 修改Series数据类型

t.index # 取出来所有标签（索引）， 属于可迭代对象
t.values # Series的所有值，  属于可迭代对象
```
### Series切片和索引
```python
import pandas as pd
t2 = pd.Series([1, 2, 3, 4, 5], index=['a', 'b', 'c', 'd', 'e'])

# 根据索引取值
t2['a']
# 取指定行的值
t2[2]
# 取指定行之间的数据
t2[2:3]
# 根据索引取多行的值
t2[['a', 'e']]
# 根据行号取指定值
t2[[0,4]]

# 取所有的索引
for i in t2.index:
    print(i)

# 取所有的值
for i in t2.values:
    print(i)

```


## DataFrame
    DataFrame是Series容器， 既有行索引，还有列索引
![](https://note.youdao.com/yws/public/resource/4d9f2b353221035aa2dc87736c84e628/xmlnote/BB84A41A0C05424C8183F23B0A0A9103/7577)
>- DataFrame对象既有行索引，又有列索引
>>- 行索引，表明不同行，横向索引，叫index，0轴，axis=0
>>>- 列索引，表名不同列，纵向索引，叫columns，1轴，axis=1
### 创建DataFrame
    pandas.DateFrame(data=None, index=None, columns=None, dtype=None, copy=False)
```python
import pandas as pd
import numpy as np

# -----------------------------------------------------------------------------------------------------------------------
# -----------------------------------------------------------------------------------------------------------------------

a = pd.DataFrame(np.arange(12).reshape(3, 4), index=list('abc'), columns=[10, 20, 30, 40])
print(a)
'''
   10  20  30  40
a   0   1   2   3
b   4   5   6   7
c   8   9  10  11
'''
# -----------------------------------------------------------------------------------------------------------------------
# -----------------------------------------------------------------------------------------------------------------------

b = pd.DataFrame({'name': ['zhangsan', 'lisi'], 'age': [12, 18], 'tel': [10086, 10010]})
print(b)
'''
# 字典的建做列索引
       name  age    tel
0  zhangsan   12  10086
1      lisi   18  10010
'''
# -----------------------------------------------------------------------------------------------------------------------
# -----------------------------------------------------------------------------------------------------------------------
c = pd.DataFrame(
    [{'name': 'zhangsan', 'age': 12, 'tel': 10010}, {'name': 'lisi', 'age': 13, 'tel': 10086}, {'name': 'xiaojie'}])
print(c)
'''
# 自动提取每个字典的键，做列索引
    age      name      tel
0  12.0  zhangsan  10010.0
1  13.0      lisi  10086.0
2   NaN   xiaojie      NaN

'''
```
### DataFrame基础属性
![](https://note.youdao.com/yws/public/resource/4d9f2b353221035aa2dc87736c84e628/xmlnote/FF034AC614F64B4CA5DB7038EB923BE6/7579)
### DtaFrame 索引取值
#### 通过切割进行索引
```python
import pandas as pd

df = pd.DataFrame(
    [{'name': 'zhangsan', 'age': 12, 'tel': 10010}, 
    {'name': 'lisi', 'age': 13, 'tel': 10086}, 
    {'name': 'xiaojie'},
    {'name': 'mayun', 'age': '66', 'tel': 110}])

df[:2]  # 取前两行
df[:2]['age']  # 取前两行的指定列数据
df['age']  # 取指定列的数据
```
#### loc 通过标签进行索引数据
```python
import pandas as pd

df = pd.DataFrame(
    [{'name': 'zhangsan', 'age': 12, 'tel': 10010}, 
    {'name': 'lisi', 'age': 13, 'tel': 10086}, 
    {'name': 'xiaojie'},
    {'name': 'mayun', 'age': '66', 'tel': 110}])
df.loc[[1, 2], ['age', 'tel']] # 取指定行指定列的数据（第1，2行的age，和tel列）
df.loc[1, 'age']  # 取指定行和指定列交叉的数据
df.loc[1, ['age', 'name']]  # 取指定行，的指定的多列的数据
df.loc[1:2, ['age']]  # 取1-2行的age列
```
#### iloc 通过位置进行索引数据
```python
import pandas as pd

df = pd.DataFrame(
    [{'name': 'zhangsan', 'age': 12, 'tel': 10010}, 
    {'name': 'lisi', 'age': 13, 'tel': 10086}, 
    {'name': 'xiaojie'},
    {'name': 'mayun', 'age': '66', 'tel': 110}])
    
# iloc 通过位置进行索引数据
df.iloc[1:3,[2,3]]
df[1:3, 1:3]

```
####  排序
```python
import pandas as pd

df = pd.DataFrame(
    [{'name': 'zhangsan', 'age': 12, 'tel': 10010}, 
    {'name': 'lisi', 'age': 13, 'tel': 10086}, 
    {'name': 'xiaojie'},
    {'name': 'mayun', 'age': 66, 'tel': 110}])
    
# 排序
print(df[:2].sort_index())  # 按列索引进行排序
print(df.sort_values('name'))  # 按指定列进行排序  
```

