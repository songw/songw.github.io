---
layout: post
title:  "数据挖掘十大算法之-navie_bayes"
date:   2020-12-10 10:02:00 +0800
categories: jekyll update
---
<head>
    <script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
    <script type="text/x-mathjax-config">
        MathJax.Hub.Config({
            tex2jax: {
            skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
            inlineMath: [ ['$','$'], ["\\(","\\)"] ], 
            displayMath: [ ['$$','$$'], ["\\[","\\]"] ],
            processEscapes: true
            }
        });
    </script>
</head>

### 朴素贝叶斯

朴素贝叶斯法是基于贝叶斯定理和特征条件独立假设的分类方法。朴素贝叶斯法实现简单，学习与预测的效率都很高，被广泛应用于文本分类、垃圾邮件过滤、自然语言处理等场景。下面我们来介绍贝叶斯定理，在介绍贝叶斯定理之前，先介绍下条件概率和全概率公式。

#### 条件概率

所谓条件概率，就是在事件 $B$ 发生的条件下，事件 $A$ 发生的概率，用 $P(A \mid B)$ 来表示。在下面的文氏图中，定义了事件 $A$ 和 $B$，以及他们的交集 $A\cap B$，$\overline A$ 为 $A$ 的补集。则在事件 $B$ 发生的条件下，事件 $A$ 发生的概率为 $P(A|B)$ = $\displaystyle{P (A \cap B)} \over \displaystyle{P(B)}$ => $P(A \cap B)=P(A|B)P(B)$；同理可得，$P(B|A)$ = $\displaystyle{P (A \cap B)} \over \displaystyle{P(A)} $ => $P(A \cap B)=P(B|A)P(A)$，于是 $P(A|B)P(B) = P(B|A)P(A)$。

#### 全概率

在上面的文氏图中，$P(B) = P(A \cap B) + P(\overline A \cap B)$，代入上面的条件概率公式，$P(B) = P(A)P(B|A) + P(\overline A)P(B|\overline A)$，这便是全概率公式。

#### 贝叶斯定理

贝叶斯定理是 $Thomas Bayes$ 为了解决一个逆概率问题所写的一篇文章。在当时，人们已经能够计算正向概率。例如，一个袋子里装有 $m$ 个白球 和 $n$ 个黑球，把手伸进去摸到白球的概率是多少？这就是一个正向概率问题，而逆概率问题正好反过来，我们事先并不知道袋子里黑球和白球的比例，而是不断伸手去摸球，根据摸到球的颜色来推测黑球与白球的比例。由上面的条件概率，我们可以推导出<br> <center>$P(A|B)$ = $\displaystyle P(B|A)P(A) \over \displaystyle P(B)$</center><br>这便是贝叶斯定理。$P(A)$ 称为先验概率，$P(A|B)$ 称为后验概率，即在事件 $B$ 发生之后，我们对事件 $A$ 概率的重新评估。

#### 朴素贝叶斯分类器

朴素贝叶斯的思想基础是，对于待分类项 $x$，求解在 $x$ 出现的条件下各个类别出现的概率，哪个最大，就认为 $x$ 属于哪个类别。设输入空间 $ \mathcal X \subseteq R^n$ 为 $n$ 维向量的集合，输出空间为类标记集合 $ \mathcal Y = \{c_1, c_2, ..., c_k\}$，输入为特征向量 $x \in \mathcal X$，输出为类标记 $y \in \mathcal Y$。$X$ 是定义在输入空间 $\mathcal X$ 上的随机向量，$Y$ 是定义在输出空间 $\mathcal Y$ 上的随机变量。假设有一个待分类项 $x$，为了确定 $x$ 属于哪个类别，我们需要计算 $P(Y=c_k|X=x), k=1, 2, ..., K$，选取概率$P(Y=c_k|X=x)$ 最大时对应的类别为 $x$ 对应的类别。基于贝叶斯定理可以写为：<br><center>$P(Y=c_k|X=x)$ = $\displaystyle P(X=x|Y=c_k)P(Y=c_k) \over \displaystyle P(X=x)$ $,k=1, 2, ..., K$</center><br>基于此公式来估计后验概率 $P(Y=c_k|X=x)$ 的主要困难在于：条件概率 $P(X=x|Y=c_k)$ 是所有特征上的联合概率，难以从有限的训练样本直接估计而得。为避开这个障碍，朴素贝叶斯分类器采用了“特征条件独立假设”：对已知类别，假设所有特征相互独立。换言之，假设每个特征独立地对分类结果发生影响（这也是算法被叫做**朴素**贝叶斯分类器的原因）。基于特征条件独立假设，可将上述公式重写为：<br><center>$\displaystyle P(Y=c_k|X=x)$
= $\displaystyle \prod_{j=1}^nP(X^{(j)}=x^{(j)}|Y=c_k)P(Y=c_k) \over \displaystyle P(X=x)$，$k=1,2,...,K$</center><br>由于对所有的类别，$P(X=x)$ 相同，所以 <br><center>$y$ = ${\underset {c_k}{\operatorname {arg\,max} }}$ $P(Y=c_k)\displaystyle\prod_{j=1}^nP(X^{(j)}=x^{(j)}|Y=c_k)$（$y$ 便是 $x$的分类）</center><br>下面便是如何求得，$P(Y=c_k)$ 和 $P(X^{(j)}=x^{(j)}|Y=c_k)$。

#### 极大似然估计

在朴素贝叶斯分类器中，学习意味着估计 $P(Y=c_k)$ 和 $P(X^{(j)}=x^{(j)}|Y=c_k)$。可以应用极大似然估计法估计相应的概率。我们先通过一个例子来了解下极大似然估计。假设一个袋子里装有黑白两种颜色的球，黑白球的数量以及比例未知，我们不可以一次把袋子中的球倒出来，只能每次从袋子中任意取一个球，记录球的颜色，然后将球放回袋子。假如我们重复取球 100 次之后，在取出的 100 个球中，有白球 60 个，黑球 40 个，那么请问袋子里黑白球的比例最有可能是多少？也许你会回答黑白球的比例为 4:6，那么这个答案背后的理论支撑是什么呢？我们假设袋子里黑球的比例为 $p$，那么白球的比例为 $1-p$，因为每抽一个球出来，在记录颜色之后，把抽出的球放回了袋子里，所以每次抽出来的球的颜色服从同一独立分布。这里我们把一次抽出来球的颜色称为一次抽样。题目中在一百次抽样中，60 次是白球的，40 次为黑球事件的概率是$P(样本结果|M)$。如果第一次抽样的结果记为 $x_1$，第二次抽样的结果记为 $x_2$...，第一百次抽样的结果为 $x_100$。那么样本结果为 $(x_1,x_2.....,x_{100})$。这样，我们可以得到如下表达式：<br><center>$P(样本结果|M)= P(x_1,x_2,…,x_{100}|M)= P(x_1|M)P(x_2|M)…P(x_{100}|M)= p^{40}(1-p)^{60}$，<br></center>由于最后的概率只和 $p$ 有关，不同的 $p$ 会导致不同的结果。那么如何求 $p$ 的值呢？极大似然估计采取的方法是让这个样本结果出现的可能性最大，也就是使得$p^{40}(1-p)^{60}$值最大，那么我们就可以看成是$p$的方程，求导即可！令导数为0，即可求出 $p=0.4$。这便是极大似然估计的思想。

先验概率 $P(Y=c_k)$ 的极大似然估计是 <br><center>$P(Y=c_k)$ = $\displaystyle\sum_{i=1}^NI(y_i=c_k) \over \displaystyle N$，$k=1,2,...,K$，<br></center>设第 $j$ 个特征 $x^{(j)}$ 可能取值的集合为 $\{a_{j1}, a_{j2},...,a_{js_j}\}$，条件概率 $P(X^{(j)}=a_{jl}|Y=c_k)$ 的极大似然估计是<br><center>
$P(X^{(j)}=a_{jl}|Y=c_k)$ = $\displaystyle\sum_{i=1}^NI(x_i^{(j)} = a_{jl}, y_i=c_k) \over \displaystyle\sum_{i=1}^N(y_i=c_k)$，$j=1,2,...,n;l=1,2,...,S_j;k=1,2,...,K$，<br></center>式中，$x_i^{(j)}$ 是第 $i$ 个样本的第 $j$ 个特征；$a_{jl}$ 是第 $j$ 个特征可能取的第 $l$ 个值；$I$ 为指示函数。

#### 例子

下面我们通过一个例子来看下朴素贝叶斯分类器的工作过程，下表格包含了天气状况和是否去打高尔夫的关系，表中 $outlook、temperature、humidity、windy$ 为特征，取值的集合分别为 $\{rainy, overcast, sunny\}，\{hot, mild, cool\}，\{high, normal\}，\{false, true\}$，$playgolf$ 为类标记，取值为 $\{no, yes\}$。如果今天天气的状况为 $(sunny, hot, normal, false)$，我们是否要去打高尔夫球呢？

| $\displaystyle outlook$ | $\displaystyle temperature$ | $\displaystyle humidity$ | $\displaystyle windy$ | $\displaystyle playgolf$ |
| ------ | ------ | ------ | ------ | ------ |
| $\displaystyle rainy$ | $\displaystyle hot$ | $\displaystyle high$ | $\displaystyle false$ | $\displaystyle no$ |
| $\displaystyle rainy$ | $\displaystyle hot$ | $\displaystyle high$ | $\displaystyle true$ | $\displaystyle no$ |
| $\displaystyle overcast$ | $\displaystyle hot$ | $\displaystyle high$ | $\displaystyle false$ | $\displaystyle yes$ |
| $\displaystyle Sunny$ | $\displaystyle mild$ | $\displaystyle high$ | $\displaystyle false$ | $\displaystyle yes$ |
| $\displaystyle Sunny$ | $\displaystyle cool$ | $\displaystyle normal$ | $\displaystyle false$ | $\displaystyle yes$ |
| $\displaystyle Sunny$ | $\displaystyle cool$ | $\displaystyle normal$ | $\displaystyle true$ | $\displaystyle no$ |
| $\displaystyle overcast$ | $\displaystyle cool$ | $\displaystyle normal$ | $\displaystyle true$ | $\displaystyle yes$ |
| $\displaystyle rainy$ | $\displaystyle mild$ | $\displaystyle high$ | $\displaystyle false$ | $\displaystyle no$ |
| $\displaystyle rainy$ | $\displaystyle cool$ | $\displaystyle normal$ | $\displaystyle false$ | $\displaystyle yes$ |
| $\displaystyle Sunny$ | $\displaystyle mild$ | $\displaystyle normal$ | $\displaystyle false$ | $\displaystyle yes$ |
| $\displaystyle rainy$ | $\displaystyle mild$ | $\displaystyle normal$ | $\displaystyle true$ | $\displaystyle yes$ |
| $\displaystyle overcast$ | $\displaystyle mild$ | $\displaystyle high$ | $\displaystyle true$ | $\displaystyle yes$ |
| $\displaystyle overcast$ | $\displaystyle hot$ | $\displaystyle normal$ | $\displaystyle false$ | $\displaystyle yes$ |
| $\displaystyle Sunny$ | $\displaystyle mild$ | $\displaystyle high$ | $\displaystyle true$ | $\displaystyle no$ |

根据极大似然估计，容易计算下列概率： <br>
$P(playgolf=yes)$ = $\displaystyle9 \over \displaystyle14$，$P(playgolf=no)$ = $\displaystyle5 \over \displaystyle14$ <br><br> $P(outlook=rainy|playgolf=yes)$ = $\displaystyle2 \over \displaystyle9$，$P(outlook=overcast|playgolf=yes)$ = $\displaystyle4 \over \displaystyle9$，$P(outlook=sunny|playgolf=yes)$ = $\displaystyle1 \over \displaystyle3$，<br>$P(outlook=rainy|playgolf=no)$ = $\displaystyle3 \over \displaystyle5$，$P(outlook=overcast|playgolf=no)$ = $\displaystyle0 \over \displaystyle5$，$P(outlook=sunny|playgolf=no)$ = $\displaystyle2 \over \displaystyle5$，<br>
<br> $P(temperature=hot|playgolf=yes)$ = $\displaystyle2 \over \displaystyle9$，$P(temperature=mild|playgolf=yes)$ = $\displaystyle4 \over \displaystyle9$，$P(temperature=cool|playgolf=yes)$ = $\displaystyle1 \over \displaystyle3$，<br>$P(temperature=hot|playgolf=no)$ = $\displaystyle2 \over \displaystyle5$，$P(temperature=mild|playgolf=no)$ = $\displaystyle2 \over \displaystyle5$，$P(temperature=cool|playgolf=no)$ = $\displaystyle1 \over \displaystyle5$，<br>
<br> $P(humidity=high|playgolf=yes)$ = $\displaystyle1 \over \displaystyle3$，$P(humidity=normal|playgolf=yes)$ = $\displaystyle2 \over \displaystyle3$，$P(humidity=high|playgolf=no)$ = $\displaystyle4 \over \displaystyle5$，<br>$P(humidity=normal|playgolf=no)$ = $\displaystyle1 \over \displaystyle5$，<br>
<br> $P(windy=false|playgolf=yes)$ = $\displaystyle2 \over \displaystyle3$，$P(windy=true|playgolf=yes)$ = $\displaystyle1 \over \displaystyle3$，$P(windy=false|playgolf=no)$ = $\displaystyle2 \over \displaystyle5$，<br>$P(windy=true|playgolf=no)$ = $\displaystyle3 \over \displaystyle5$，<br>

对给定的$(sunny, hot, normal, false)$计算：<br>
$P(playgolf=yes)P(outlook=sunny|playgolf=yes)P(temperature=hot|playgolf=yes)$<br>
$P(humidity=Normal|playgolf=yes)P(windy=false|playgolf=yes)$<br>
= $\displaystyle9 \over \displaystyle14$ * $\displaystyle1 \over \displaystyle3$ * $\displaystyle2 \over \displaystyle9$ * $\displaystyle2 \over \displaystyle3$ * $\displaystyle2 \over \displaystyle3$ $\approx$ $0.0211$

$P(playgolf=no)P(outlook=sunny|playgolf=no)P(temperature=hot|playgolf=no)$<br>
$P(humidity=normal|playgolf=no)P(windy=false|playgolf=no)$<br>
= $\displaystyle5 \over \displaystyle14$ * $\displaystyle2 \over \displaystyle5$ * $\displaystyle2 \over \displaystyle5$ * $\displaystyle1 \over \displaystyle5$ * $\displaystyle2 \over \displaystyle5$ $\approx$ $0.0046$

由于 $0.0211 > 0.0046$，所以今天去打高尔夫。

#### 贝叶斯估计

用极大似然估计可能会出现所要估计的概率值为 0 的情况。这时会影响到后验概率的计算结果，是分类产生偏差。解决这一问题的方法是采用贝叶斯估计。具体地，条件概率的贝叶斯估计是<br><center>
$P_\lambda(X^{(j)}=a_{jl}|Y=c_k)$ = $\displaystyle\sum_{i=1}^NI(x_i^{(j)} = a_{jl}, y_i=c_k)+ \lambda \over \displaystyle\sum_{i=1}^N(y_i=c_k)+S_j\lambda$，式中$\lambda \geq 0$。<br></center>等价于在随机变量各个取值的频数上赋予一个正数 $\lambda > 0$。当 $\lambda = 0$ 时就是极大似然估计。常取 $\lambda = 1$，这时称为拉普拉斯平滑。同样，先验概率的贝叶斯估计是<br><center>$P_\lambda(Y=c_k)$ = $\displaystyle\sum_{i=1}^NI(y_i=c_k)+\lambda \over \displaystyle N+K\lambda$，<br></center>使用贝叶斯估计计算上面例子中的概率为：<br><br>
$P(playgolf=yes)$ = $\displaystyle10 \over \displaystyle16$，$P(playgolf=no)$ = $\displaystyle6 \over \displaystyle16$ <br><br> $P(outlook=rainy|playgolf=yes)$ = $\displaystyle1 \over \displaystyle4$，$P(outlook=Overcast|playgolf=yes)$ = $\displaystyle5 \over \displaystyle12$，$P(outlook=sunny|playgolf=yes)$ = $\displaystyle1 \over \displaystyle3$，<br>$P(outlook=rainy|playgolf=no)$ = $\displaystyle1 \over \displaystyle2$，$P(outlook=Overcast|playgolf=no)$ = $\displaystyle1 \over \displaystyle8$，$P(outlook=sunny|playgolf=no)$ = $\displaystyle3 \over \displaystyle8$，<br>
<br> $P(temperature=hot|playgolf=yes)$ = $\displaystyle1 \over \displaystyle4$，$P(temperature=mild|playgolf=yes)$ = $\displaystyle5 \over \displaystyle12$，$P(temperature=cool|playgolf=yes)$ = $\displaystyle1 \over \displaystyle3$，<br>$P(temperature=hot|playgolf=no)$ = $\displaystyle3 \over \displaystyle8$，$P(temperature=mild|playgolf=no)$ = $\displaystyle3 \over \displaystyle8$，$P(temperature=cool|playgolf=no)$ = $\displaystyle1 \over \displaystyle4$，<br>
<br> $P(humidity=high|playgolf=yes)$ = $\displaystyle4 \over \displaystyle11$，$P(humidity=normal|playgolf=yes)$ = $\displaystyle7 \over \displaystyle11$，$P(humidity=high|playgolf=no)$ = $\displaystyle5 \over \displaystyle7$，<br>$P(humidity=normal|playgolf=no)$ = $\displaystyle2 \over \displaystyle7$，<br>
<br> $P(windy=false|playgolf=yes)$ = $\displaystyle7 \over \displaystyle11$，$P(windy=true|playgolf=yes)$ = $\displaystyle4 \over \displaystyle11$，$P(windy=false|playgolf=no)$ = $\displaystyle3 \over \displaystyle7$，<br>$P(windy=true|playgolf=no)$ = $\displaystyle4 \over \displaystyle7$，<br>

对给定的$(sunny, hot, normal, false)$计算：<br>
$P(playgolf=Yes)P(outlook=sunny|playgolf=yes)P(temperature=hot|playgolf=yes)$<br>
$P(humidity=normal|playgolf=yes)P(windy=false|playgolf=yes)$<br>
= $\displaystyle10 \over \displaystyle16$ * $\displaystyle1 \over \displaystyle3$ * $\displaystyle1 \over \displaystyle4$ * $\displaystyle7 \over \displaystyle11$ * $\displaystyle7 \over \displaystyle11$ $\approx$ $0.0211$

$P(playgolf=no)P(outlook=sunny|playgolf=no)P(temperature=hot|playgolf=no)$<br>
$P(humidity=normal|playgolf=no)P(windy=false|playgolf=no)$<br>
= $\displaystyle6 \over \displaystyle14$ * $\displaystyle2 \over \displaystyle5$ * $\displaystyle2 \over \displaystyle5$ * $\displaystyle1 \over \displaystyle5$ * $\displaystyle2 \over \displaystyle5$ $\approx$ $0.0065$

由于 $0.0211 > 0.0065$，所以今天去打高尔夫。

#### 鸢尾花分类

```python
import pandas as pd
from sklearn import metrics
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score
from sklearn.preprocessing import StandardScaler
from sklearn.naive_bayes import GaussianNB
```

```python
iris_data = pd.read_csv('Iris.csv')
```

```python
y = iris_data['Species']
x = iris_data[['SepalLengthCm', 'SepalWidthCm', 'PetalLengthCm', 'PetalWidthCm']]
train_x, test_x, train_y, test_y = train_test_split(x, y, test_size=0.3, random_state=33)
```

```python
gnb = GaussianNB()
gnb.fit(train_x, train_y)
```

```python
predict_y = gnb.predict(test_x)
```

```python
print("NB准确率: %.4lf" % accuracy_score(test_y, predict_y))
```

#### 糖尿病的预测

```python
import pandas as pd
import numpy as np
from sklearn import metrics
from sklearn.neighbors import KNeighborsClassifier
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score
from sklearn.preprocessing import StandardScaler
```

```python
diabetes_data = pd.read_csv('diabetes.csv')
```

```python
diabetes_data.replace({
    'Glucose': 0,
    'BloodPressure': 0,
    'SkinThickness': 0,
    'BMI': 0,
    'Insulin': 0
}, np.NaN, inplace=True)

glucose_mean = diabetes_data['Glucose'].mean()
blood_pressure_mean = diabetes_data['BloodPressure'].mean()
skin_thickness_mean = diabetes_data['SkinThickness'].mean()
bmi_mean = diabetes_data['BMI'].mean()
insulin_mean = diabetes_data['Insulin'].mean()

diabetes_data['Glucose'].replace(np.NaN, glucose_mean, inplace=True)
diabetes_data['BloodPressure'].replace(np.NaN, blood_pressure_mean, inplace=True)
diabetes_data['SkinThickness'].replace(np.NaN, skin_thickness_mean, inplace=True)
diabetes_data['BMI'].replace(np.NaN, bmi_mean, inplace=True)
diabetes_data['Insulin'].replace(np.NaN, insulin_mean, inplace=True)
```

```python
y = diabetes_data['Outcome']
x = diabetes_data[['Pregnancies', 'Glucose', 'BloodPressure', 'SkinThickness', 'Insulin', 'BMI', 'DiabetesPedigreeFunction', 'Age']]
train_x, test_x, train_y, test_y = train_test_split(x, y, test_size=0.3, random_state=1)
```

```python
gnb = GaussianNB()
gnb.fit(train_x, train_y)
```

```python
predict_y = gnb.predict(test_x)
print("NB 准确率: %.4lf" % accuracy_score(test_y, predict_y))
```
