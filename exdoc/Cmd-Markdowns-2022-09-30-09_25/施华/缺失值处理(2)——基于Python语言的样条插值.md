# 缺失值处理(2)——基于Python语言的样条插值

标签（空格分隔）： 施华

---
本文紧接着缺失值处理(1)，这里我们主要用数学上的方法来做插值。主要用的就是多项式插值，采用Python的Scipy包
### 1.样条插值
多项式插值总的思想就是给定固定的一些点，找到一个函数，该函数的曲线可以穿过所有点。本文主要介绍用Python的Scipy包来实现样条插值。
大致步骤为
**(1)首先要使用splrep()计算欲插值曲线的样条系数(对于N-维空间使用
splprep);(2)在给定的点上用splev()计算样条插值结果。**
```Python
# 载入包
import numpy as np
import matplotlib.pyplot as plt
from scipy import interpolate
# 用sin函数生成数据
x = np.arange(0, 2*np.pi+np.pi/4, 2*np.pi/8)
y = np.sin(x)
# 三次样条插值
tck = interpolate.splrep(x, y, s=0) # 用splrep给出样本区间内的样条曲线系数
# 这里由于不是处理缺失值，只是在拟合曲线，所以选择的区间还是在样本内
# 实际处理缺失值时，可以选择给定的缺失值附近的区间
xnew = np.arange(0, 2*np.pi, np.pi/50)  
ynew = interpolate.splev(xnew, tck, der=0) # 用计算好的样条曲线去计算给定区间的函数值
# 实际处理缺失值时，我们利用缺失值附近区间计算的函数值，就是我们需要的插补缺失值
# 样条插值拟合可视化
plt.figure()
plt.plot(x, y, 'x', xnew, ynew, xnew, np.sin(xnew), x, y, 'b')
plt.legend(['Linear', 'Cubic Spline', 'True'])
plt.axis([-0.05, 6.33, -1.05, 1.05])
plt.title('Cubic-spline interpolation')
plt.show()
```
![相关图像](https://docs.scipy.org/doc/scipy/reference/_images/interpolate-4_00_00.png)
**注意，样条插值可以是选择给定的缺失值附近的区间来做，这样在选择的函数更容易拟合曲线，如果选择全样本拟合可能会出现拟合函数不好**

### 2.其他缺失值处理方法
其他处理缺失值的方法有很多，常用的有EM算法，滤波，有兴趣可以自行查找相关文献，这里我们只给出较为简单使用，快捷方便的缺失值插补方法，一个是基于R语言mice包的多重插补方法，一个是基于Python语言的样条插值。具体选择哪一个还是看实际情况需要而定。

