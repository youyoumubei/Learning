可视化类库Seaborn
===

## Seaborn简介
基于matplotlib之上进行了一层封装的画图类库
```python
import seaborn as sns
import numpy as np
import matplotlib as mpl
import matplotlib.pyplot as plt
%matplotlib inline
```

## 整体布局风格设置
```python
def sinplot(flip=1):
    x = np.linspace(0, 14, 100)
    for i in range(1, 7):
        plt.plot(x, np.sin(x + i*.5) * (7-i) * flip)
# 不适用seaborn直接画图
sinplot()

# 使用seaborn画图
sns.set()
sinplot()
```

### 5种主题风格
darkgrid,whitegrid,dark,white,ticks
```python
# 白色风格箱线图只有横向刻度线
sns.set_style('whitegrid')
data = np.random.normal(size=(20, 6)) + np.arange(6) / 2
sns.boxplot(data=data)

# 暗色背景并且无刻度线
sns.set_style('dark')
sinplot()

# 坐标轴有刻度线
sns.set_style('ticks')
sinplot()

# 去除右侧和上侧坐标轴
sinplot()
sns.despine()
```
