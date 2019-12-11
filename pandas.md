Pandas
===

## 数据读取
```python
import pandas as pd

data = pd.read_cvs('')
# 数据中的元素
data.columns
# 读取前几行数据
data.head(3)
# 读取后几行数据
data.tail(3)
# 读取行的数据
data.loc[0]
data.loc[3:6]
# 读取某列的数据
cols = ['col1', 'col2']
data[cols]
# 模糊查找符合条件的列名
col_names = data.columns.tolist()
gram_columns = []
for name in col_names:
    if name.endwith('g'):
        gram_columns.append(name)
gram_df = data[gram_columns]
gram_df(3)
# 运算
# 维度一样时会对应相加减乘除
new_col = data['col1'] * data['col2']
data['new_col'] = new_col
# 常用函数
data['col1'].max()
data['clo1'] / data['col1'].max()
# 排序
# inplace=True返回一个新的DataFrame
data.sort_values('col1', inplace=True, ascending=False)
```

## 数据预处理
```python
# 获取缺失值并进行处理
age = data['age']
age_is_null = pd.isnull(age)
age_null_true = age[age_is_null]
```