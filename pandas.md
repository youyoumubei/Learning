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
data.loc[3, 'col']
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
# 求均值
data['age'].mean()
# 统计两个变量之间的关系
data.plvot_table(index='col1', values='col2', aggfunc=np.mean)
data.plvot_table(index='Pclass', values='Age', aggfunc=np.mean)
data.plvot_table(index='Pclass', values=['Fare', 'Survived'], aggfunc=np.sum)
# 丢弃缺失值
data.dropna(axis=1)
data.dropna(axis=0, subset=['Age', 'Sex'])
# 重新排序
new_data = data.sort_values('col1' ascending=False)
# drop=True生成新的数据
new_data.reset_index(drop=True)
```

## 自定义函数
```python
# 自定义apply函数
def some_func():
    ...
    return something

def not_null_account(column):
    column_null = pd.isnull(column)
    null = column[column_null]
    return len(null)
# 数据离散化
def is_minor(row):
    if row['age'] < 18:
        return True
    else:
        return False

data.apply(some_func)
```

## Series结构
DataFrame的一行或一列
```python
from pandas import Series

series_film = data['FILM']
file_name = series_film.values
rt_scores = data['SCORES'].values
print(type(file_name)) # numpy.ndarray

# 用str当做索引
series_custom = Series(rt_scores, index=file_name)
series_custom[['Film A', 'Film B']]
print(series_custom[5:10])

# 排序
series_custom.sort_index()
series_custom.sort_values()

# 针对字符串索引的一些操作
film_data = data.set_index('FILM', drop=False)
film_data['Axxxx':'Hxxxx']
film_data.loc['Bxxx']
```