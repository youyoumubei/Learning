sklearn 学习笔记
===

## 选择合适的模型
先放一张sklearn推荐的模型选择流程，正确的流程应该是进行数据预处理，因为第三方库的数据都是经过处理的可以直接拿来用，但是我们实际项目里拿到的数据可能存在缺失值、数据不平衡等问题，首先提高数据的质量才能得到符合预期的结果。
![avatar](/img/estimator.jpg)

## 通用模式
导入模块->创建数据->建立模型->训练->验证->预测->结果展示

### 导入模块
将我们需要用到的第三方库引入到项目中，常用的有sklearn、tensorflow、numpy、pandas、matplotlib等

```python
# 引入sklearn官方数据集
from sklearn import datasets
# 数据集分割
from sklearn.model_selection import train_test_split
# 该项目用到的算法
from sklearn.neighbors import KNeighborsClassifier
# 画图
import matplotlib.pyplot as plt
```

### 创建数据
在机器学习项目中，我们将数据分为训练集(train sets)和测试集(test sets)，利用训练集得到模型之后，使用测试集来验证模型的泛化能力(generalization)，我们的目标是找到泛化能力最高的模型。

在模型训练时通过调整参数等方式来防止模型出现过拟合(over fitting)或者欠拟合(under fitting)。

```python
# 加载数据集中的鸢尾花数据用来做分类训练
loaded_data = datasets.load_iris()
# 特征数据
data_x = loaded_data.data
# 标签数据
data_y = loaded_data.target
# 将数据集分割为训练集和测试集
# test_size数值代表测试集的百分比
x_train, x_test, y_train, y_test = train_test_split(data_x, data_y, test_size=0.3)
```

### 建立模型
