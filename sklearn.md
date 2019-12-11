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

### 建立模型-训练-预测
机器学习分为监督学习和无监督学习，监督学习可用的算法有决策树、SVM、Logistic Regression等，无监督学习包括聚类、PCA等。机器学习各个模型没有好与坏的区别，它们在不同的场景下都能发挥各自的用途。
```python
# 定义模型
knn = KNeighborsClassifier()
# 使用fit来训练测试集
knn.fit(x_train, y_train)
# 通过训练集得到的模型预测测试集的输出
print(knn.predict(x_test))
print(y_test)
```

### 实用的功能
在sklearn中除了以上提到的，其实还有一些使用的功能可以帮助更有效的理解和调试模型
```python
# 度量模型泛化能力的另一种方式是使用score得到模型预测的分数
print(knn.score(x_test, y_test))
# 通过get_params()取出之前定义的参数
print(knn.get_params())
```

#### 数据标准化()
由于数据的偏差与跨度会影响机器学习的效果，因此正规化(标准化)数据可以提升机器学习的效果
```python
# 标准化数据模块
from sklearn import preprocessing
import numpy as np
from sklearn.model_selection import train_test_split
from sklearn.datasets.samples_generator import make_classification
from sklearn.svm import SVC
# 可视化数据的模块
import matplotlib.pyplot as plt

#生成具有2种属性的300笔数据
X, y = make_classification(
    n_samples=300, n_features=2,
    n_redundant=0, n_informative=2, 
    random_state=22, n_clusters_per_class=1, 
    scale=100)

#可视化数据
plt.scatter(X[:, 0], X[:, 1], c=y)
plt.show()
# 未进行数据处理的预测结果
x_train, x_test, y_train, y_test = train_test_split(X, y, test_size=0.3)
clf = SVC()
clf.fit(x_train, y_train)
print(clf.score(x_test, y_test))
# 进行预处理后的预测结果
X = preprocessing.scale(X)
x_train, x_test, y_train, y_test = train_test_split(X, y, test_size=0.3)
clf = SVC()
clf.fit(x_train, y_train)
print(clf.score(x_test, y_test))
```

### 交叉验证(cross validation)
为了避免在训练过程中造成模型的过拟合(overfitting)，通常的做法就是保留数据集的一部分作为测试集，下图是模型训练的典型交叉验证流程。更多内容可以看[这里](https://scikit-learn.org/stable/modules/cross_validation.html)
![avatar](/img/cros_val.jpg)

```python
# K-fold交叉验证模块，计算代价高但是数据利用率高
from sklearn.cross_validation import cross_val_score

#使用K-fold交叉验证模块
scores = cross_val_score(knn, X, y, cv=5, scoring='accuracy')
#将5次的预测准确率打印出
print(scores)
#将5次的预测准确平均率打印出
print(scores.mean())
```
![avatar](/img/kfold.jpg)