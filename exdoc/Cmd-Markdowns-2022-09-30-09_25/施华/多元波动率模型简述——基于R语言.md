# 多元波动率模型简述——基于R语言

标签（空格分隔）： 施华

---

### 1.多元波动率建模思路简述

我们的目的是用一个数学模型去描述一个数据生成过程(包括多元),计量经济学的思路是大多是对条件概率建模，
$$P(X|F_{t-1})$$
但直接对概率建模太难了，不是数学家做不出来，主要是做出来很少人看得懂，实际中用起来太复杂。所以，最简单的是从局部去对条件概率建模，也就是说我们可以从各阶矩建模，线性回归就是从二阶矩建模，但为了更精细地刻画出现实数据的生成过程，或者当低阶矩不满足现实数据时，我们就需要对更高阶的矩进行建模。在金融计量中,由于大多时间序列数据都具有条件异方差的特性(波动集聚性)，一阶矩不能很好地刻画，我们需要对二阶矩进行建模。
我们把一个多元时间序列$Z_{t}$分解为
$$Z_{t}=\mu_{t}+a_{t}$$
其中，$\mu_{t}=E(z_{t}|F_{t-1})$为可预测分量，$a_{t}$为不可预测的新息(随机扰动项)。这是对一阶矩建模，一元常用AR，多元常用VAR。
接下来，进入正题，我们要对波动$a_{t}$建模，将$a_{t}$表示为(这样表示才会有极限分布，有了极限分布才可以去估计做假设检验，具体可详见测度论和概率极限理论，但没什么用。)
$$a_{t}=\Sigma_{t}^{\frac{1}{2}}\epsilon_{t}$$
其中，$\{\epsilon_{t}\}$是一个独立同分布随机向量序列，$E(\epsilon_{t})=0,cov(\epsilon_{t})=I_{k}$,$\Sigma
_{t}^
{\frac{1}{2}}$表示$\Sigma_{t}$的正定平方根矩阵。


### 2.条件异方差的检验
从上文中我们知道，虽然波动率建模是为了处理条件异方差，但是条件异方差并不总是存在的，所以需要对条件异方差的存在进行检验。
常用的检验有混成检验，基于秩的检验。原假设都是不存在条件异方差。
**多元时间序列的波动率检验可以运行MTS包中的MarchTest命令实现，默认选项使用滞后10阶**。
**R语言条件异方差检验代码注释**
```R
> require(MTS)
> zt=matrix(rnorm(2000),400,5) ### 为了测试，产生随机序列
> MarchTest(zt)  #### 多元波动率检验
Q(m) of squared series(LM test):  
Test statistic:  5.054594  p-value:  0.8875013 
Rank-based Test:  
Test statistic:  9.377031  p-value:  0.4967344 
Q_k(m) of squared series:  
Test statistic:  223.293  p-value:  0.8867984 
Robust Test(5%) :  269.4988  p-value:  0.1894433 
### IBM和S&P对数收益率的条件异方差检验
> da=read.table("m-ibmsp-6111.txt",header=T)
> rtn=log(da[,2:3]+1)
> at=scale(rtn,scale=F)  ## Remove sample means
> MarchTest(at)
Q(m) of squared series(LM test):  
Test statistic:  38.06663  p-value:  3.695138e-05 
Rank-based Test:  
Test statistic:  108.3798  p-value:  0 
Q_k(m) of squared series:  
Test statistic:  109.4194  p-value:  2.276873e-08 
Robust Test(5%) :  118.7134  p-value:  9.894441e-10
```


### 3.波动率模型估计
由上文知，
$$a_{t}=\Sigma_{t}^{\frac{1}{2}}\epsilon_{t}$$
**此处注意实际运用中$a_{t}$是从均值模型中得到的残差**
$\epsilon_{t}$可以假定任意分布，因此$\Sigma_{t}$就成为我们波动率模型的估计目标。理论上，在$\epsilon_{t}$是正态分布时，可用正常的MLE求解，在$\epsilon_{t}$为其他分布时，可用QMLE求解。但实际中，我们需要用数值方法来做估计，总体思路都是在对$\Sigma_{t}$做不同形式的矩阵分解，从而得到一个关于$\Sigma_{t}$的迭代式，有了迭代式我们就可以去编程计算。具体的各种形式的估计会在第五部分给出介绍。


### 4.波动率模型的诊断检验
为了检验拟合的多元波动率模型的准确性，对残差$\hat a_{t}=Z_{t}-\hat \mu_{t}$进行一些诊断检验，其中$\hat \mu_{t}$是$Z_{t}$的拟合条件均值，用$\hat a_{t}$检测均值方程，并且用$\hat a_{t}$的一些二次函数验证波动率方差。
常见的模型拟合检验有Ling&Li统计量，Tse统计量。(统计量的构建详见陈希孺的那本很厚的数理统计引论，友情提醒慎入，没什么用)
**在第五部分我们用不同的数值方法构建$\Sigma_{t}$以此建立波动率模型，再给出对应模型的拟合检验。**


### 5.花式估计$\Sigma_{t}$
此部分的介绍框架为迭代式，思想，代码注释
#### 5.1EWMA
$$\hat \Sigma_{t}=\lambda\hat \Sigma_{t-1}+(1-\lambda)\hat a_{t-1}\hat a_{t-1}^{'}$$
该模型的思想其实就是指数加权平均，不过是用到多维上。
**R语言实例注释**
```R
##### EWMA方法
> da=read.table("m-dec125910-6111.txt",header=T)
> head(da)
      date      dec1      dec2      dec5      dec9     dec10
1 19610131  0.058011  0.067392  0.081767  0.096754  0.087207
2 19610228  0.029241  0.042784  0.055524  0.056564  0.060245
3 19610330  0.025896  0.025474  0.041304  0.060563  0.071875
4 19610428  0.005667  0.001365  0.000780  0.011911  0.023328
5 19610531  0.019208  0.036852  0.049590  0.046248  0.050362
6 19610630 -0.024670 -0.025225 -0.040046 -0.050651 -0.051434
> rtn=log(da[,2:4]+1)
> m1=VAR(rtn,1)  ## 用VAR(1)来拟合均值方程
Constant term: 
Estimates:  0.006376978 0.007034631 0.007342962 
Std.Error:  0.001759562 0.001950008 0.002237004 
AR coefficient matrix 
AR( 1 )-matrix 
       [,1]  [,2]     [,3]
[1,] -0.194 0.224  0.00836
[2,] -0.232 0.366 -0.04186
[3,] -0.313 0.452  0.00238
standard error 
      [,1]  [,2]  [,3]
[1,] 0.108 0.160 0.101
[2,] 0.120 0.177 0.111
[3,] 0.138 0.204 0.128
  
Residuals cov-mtx: 
            [,1]        [,2]        [,3]
[1,] 0.001814678 0.001859113 0.001962277
[2,] 0.001859113 0.002228760 0.002420858
[3,] 0.001962277 0.002420858 0.002933081
  
det(SSE) =  1.712927e-10 
AIC =  -22.45809 
BIC =  -22.39289 
HQ  =  -22.43273 
> at=m1$residuals  ## ARCH检验
> MarchTest(at)
Q(m) of squared series(LM test):  
Test statistic:  244.7878  p-value:  0 
Rank-based Test:  
Test statistic:  215.215  p-value:  0 
Q_k(m) of squared series:  
Test statistic:  176.9811  p-value:  1.252294e-07 
Robust Test(5%) :  155.2633  p-value:  2.347499e-05 
> m2=EWMAvol(at,lambda=-0.1)  ### EWMA迭代式估计

Coefficient(s):
        Estimate  Std. Error  t value Pr(>|t|)    
lambda   0.96427     0.00549    175.6   <2e-16 ***
---
> Sigma.t=m2$Sigma.t  ### 从估计的模型中提取波动率矩阵
> m3=MCHdiag(at,Sigma.t)
Test results:  
Q(m) of et: 
Test and p-value:  59.5436 4.421175e-09 
Rank-based test: 
Test and p-value:  125.0929 0 
Qk(m) of epsilon_t: 
Test and p-value:  189.5403 4.518401e-09 
Robust Qk(m):  
Test and p-value:  228.234 5.57332e-14 
> 
> par(mfcol=c(3,2)) ### 可视化
> tdx=c(2:609)/12+1961
> plot(tdx,Sigma.t[,1],xlab='Year',ylab="Variance",type='l')
> title(main="(a) Dec 1")
> plot(tdx,Sigma.t[,5],xlab='Year',ylab="Variance",type='l')
> title(main="(b) Dec 2")
> plot(tdx,Sigma.t[,9],xlab='Year',ylab="Variance",type='l')
> title(main="(c) Dec 5")
> plot(tdx,Sigma.t[,2],xlab='Year',ylab="Covariance",type='l')
> title(main="(d) Dec 1 vs Dec 2")
> plot(tdx,Sigma.t[,3],xlab='Year',ylab="Covariance",type='l')
> title(main="(e) Dec 1 vs Dec 5")
> plot(tdx,Sigma.t[,6],xlab='Year',ylab="Covariance",type='l')
> title(main="(f) Dec 2 vs Dec 5")
```


#### 5.2BEKK(1,1)
$$\Sigma_{t}=A_{0}A_{0}^{'}+A_{1}a_{t-1}a_{t-1}^{'}A_{1}^{'}+B_{1}\Sigma_{t-1}B_{1}^{'}$$
其中，$A_{0}$是下三角矩阵，因此$A_{0}A_{0}^{'}$是正定的。
该模型的思想就是一元GARCH模型的扩展，在迭代式的右边用ARMA形式构建，但改模型计算参数过多，计算困难。
**R语言实例注释**
```R
########## BEKK(1,1)模型
> da=read.table("m-ibmsp-6111.txt",header=T)
> rtn=log(da[,2:3]+1)
> m1a=BEKK11(rtn)  ### BEKK(1,1)拟合估计
Initial estimates:  0.00772774 0.005023909 0.06977651 0.02639684 0.03502962 0.1 0.02 0.02 0.1 0.8 0.1 0.1 0.8 
Lower limits:  -0.0772774 -0.05023909 0.0139553 0.005279367 0.007005923 1e-06 -0.5 -0.5 1e-06 1e-06 -0.5 -0.5 1e-06 
Upper limits:  0.0772774 0.05023909 0.07675416 0.02903652 0.03853258 0.999999 0.5 0.5 0.999999 0.999999 0.5 0.5 0.999999 

Coefficient(s):
           Estimate  Std. Error  t value   Pr(>|t|)    
mu1.ibm  0.00775929  0.00253971  3.05518 0.00224922 ** 
mu2.sp   0.00565084  0.00154553  3.65624 0.00025594 ***
A011     0.01395530  0.00408488  3.41633 0.00063472 ***
A021     0.00838972  0.00268086  3.12949 0.00175112 ** 
A022     0.00700592  0.00193247  3.62537 0.00028855 ***
A11      0.15648877  0.06002824  2.60692 0.00913610 ** 
A21     -0.05926387  0.03253895 -1.82132 0.06855810 .  
A12      0.23398204  0.08575142  2.72861 0.00636022 ** 
A22      0.40977179  0.05400961  7.58702 3.2641e-14 ***
B11      0.97639151  0.02283328 42.76178 < 2.22e-16 ***
B21      0.01449633  0.01196030  1.21204 0.22549813    
B12     -0.09287696  0.03227225 -2.87792 0.00400306 ** 
B22      0.89077633  0.02476925 35.96300 < 2.22e-16 ***
---
> names(m1a)   
[1] "estimates"  "HessianMtx" "Sigma.t"   
> Sigma.t=m1a$Sigma.t  ### 提取波动率矩阵
> at=cbind(rtn[,1]-0.00776,rtn[,2]-0.00565)
> MCHdiag(at,Sigma.t)  ### 模型拟合检验
Test results:  
Q(m) of et: 
Test and p-value:  5.280566 0.8716653 
Rank-based test: 
Test and p-value:  16.02931 0.0987965 
Qk(m) of epsilon_t: 
Test and p-value:  29.46281 0.889654 
Robust Qk(m):  
Test and p-value:  52.13744 0.09462895 
```


#### 5.3Cholesky分解
将$a_{t}$重新构造为
$$\beta_{t}a_{t}=b_{t}$$
则
$$a_{t}=\beta_{t}^{-1}b_{t}$$
定义$b_{t}$的波动率矩阵为$\Sigma_{b,t}$,得到
$$\Sigma_{t}=\beta_{t}^{-1}\Sigma_{b,t}(\beta_{t}^{-1})$$
其中，$\Sigma_{b,t}$为对角矩阵。
该模型的思路是为了给$a_{t}$建模，我们可以直接对其建模，也可以找一个和$a_{t}$相关且已知的$b_{t}$来代替。这样，我们处理好$b_{t}$和相关系数$\beta_{t}$就可以反过来对$a_{t}$建模。而这一过程的表达用线性代数的语言来说是一种矩阵分解过程，此处的Cholesky分解实质上是常见的LU分解的一种特例。
**R语言代码注释**
```R
##### Cholesky分解
> da=read.table("m-ibmspko-6111.txt",header=T)
> rtn=log(da[,2:4]+1)
> require(fGarch)
> m3=MCholV(rtn)  ### 用Cholesky估计
Sample means:  0.007728 0.005024 0.010595 
Estimation of the first component 
Estimate (alpha0, alpha1, beta1):  0.000356 0.117515 0.810288 
s.e.                            :  0.000157 0.037004 0.057991 
t-value                         :  2.262897 3.175772 13.97261 
Component  2  Estimation Results (residual series): 
Estimate (alpha0, alpha1, beta1):  6.4e-05 0.099156 0.858354 
s.e.                            :  3.1e-05 0.027785 0.037238 
t-value                         :  2.034528 3.568616 23.05076 
Component  3  Estimation Results (residual series): 
Estimate (alpha0, alpha1, beta1):  0.000173 0.117506 0.818722 
s.e.                            :  6.2e-05 0.028651 0.038664 
t-value                         :  2.808075 4.101297 21.17521 
> names(m3)
[1] "betat"   "bt"      "Vol"     "Sigma.t"
> at=scale(rtn[37:612,],center=T,scale=F)
> Sigma.t=m3$Sigma.t   ### 调取波动率矩阵
> MCHdiag(at,Sigma.t)  ### 波动率模型拟合检验
Test results:  
Q(m) of et: 
Test and p-value:  15.94978 0.1010791 
Rank-based test: 
Test and p-value:  21.99727 0.01511849 
Qk(m) of epsilon_t: 
Test and p-value:  123.7687 0.01057302 
Robust Qk(m):  
Test and p-value:  95.49626 0.3259654 
> MCHdiag(at,Sigma.t,5)
Test results:  
Q(m) of et: 
Test and p-value:  5.717035 0.3347333 
Rank-based test: 
Test and p-value:  5.579834 0.3492711 
Qk(m) of epsilon_t: 
Test and p-value:  59.837 0.06842123 
Robust Qk(m):  
Test and p-value:  58.97874 0.07891472 
```


#### 5.4DCC
$$\rho_{t}=D^{-1}_{t}\Sigma_{t}D_{t}^
{-1}$$
$D_{t}$为对角矩阵。
该模型的思想是从相关系数出发，我们先对相关系数建模，再找到波动率序列构成$D_{t}$矩阵，我们就可以反解出$\Sigma_{t}$.
**R语言实例代码注释**
```R
#### DCC模型
####
> m1=dccPre(rtn,include.mean=T,p=0)  ###对均值方程的估计
Sample mean of the returns:  0.00772774 0.005023909 0.01059521 
Component:  1 
Estimates:  0.000419 0.126739 0.788307 
se.coef  :  0.000162 0.035405 0.055645 
t-value  :  2.593448 3.57973 14.16662 
Component:  2 
Estimates:  9e-05 0.127725 0.836053 
se.coef  :  4.1e-05 0.03084 0.031723 
t-value  :  2.20126 4.141592 26.35486 
Component:  3 
Estimates:  0.000256 0.098705 0.830358 
se.coef  :  8.5e-05 0.022361 0.033441 
t-value  :  3.015321 4.414112 24.83088 
> names(m1)
[1] "marVol"  "sresi"   "est"     "se.coef"
> rtn1=m1$sresi
> Vol=m1$marVol

> m2=dccFit(rtn1)  ### 用Tse&Tsui和Engle模型估计相关系数矩阵。
Estimates:  0.8088046 0.04027251 7.959158 
st.errors:  0.1491729 0.02259832 1.135936 
t-values:   5.421929 1.782101 7.006697 
> names(m2)
[1] "estimates" "Hessian"   "rho.t"    
> S2.t=m2$rho.t

> m3=dccFit(rtn1,type="Engle")  ### 用Engle模型估计相关系数矩阵。
Estimates:  0.9126634 0.04530917 8.623668 
st.errors:  0.0294762 0.01273911 1.332371 
t-values:   30.96272 3.556697 6.472423 
> S3.t=m3$rho.t

> MCHdiag(rtn1,S2.t)  ### 模型拟合检验
Test results:  
Q(m) of et: 
Test and p-value:  20.74259 0.02296172 
Rank-based test: 
Test and p-value:  30.20662 0.0007924436 
Qk(m) of epsilon_t: 
Test and p-value:  132.423 0.002425899 
Robust Qk(m):  
Test and p-value:  109.9671 0.07501565 
> MCHdiag(rtn1,S3.t)
Test results:  
Q(m) of et: 
Test and p-value:  20.02958 0.02897411 
Rank-based test: 
Test and p-value:  27.61638 0.002078829 
Qk(m) of epsilon_t: 
Test and p-value:  131.982 0.002625755 
Robust Qk(m):  
Test and p-value:  111.353 0.06307334 
```


#### 5.5正交变换
$$a_{t}=Mb_{t}$$
$$\Sigma_{t}=MV_{t}M^{'}$$
该模型的思想是由一个已知$b_{t}$乘以一个矩阵，从而映射出来一个东西，成为$a_{t}$。我们只要知道$b_{t}$和M，$a_{t}$的构建就简单了。我们给$b_{t}$一些假定后，M的寻找过程实质上就是一个矩阵的分解过程，比如假定$cCOV(b_{t})=I$,则$COV(a_{t})=MM^{'}$。
**R语言实例代码注释**
```R
*** GO-GARCH 模型
#### Go-GARCH models
> require(gogarch)
> crtn=scale(rtn,center=T,scale=F)
> m1=gogarch(crtn,~garch(1,1),estby="ica")  ### go-garch模型估计
> m1
Components estimated by: fast ICA
Dimension of data matrix: (612 x 3).
Formula for component GARCH models: ~ garch(1, 1) 

Orthogonal Matrix U:
           [,1]       [,2]       [,3]
[1,]  0.3723864 -0.4878111  0.7895370
[2,] -0.7629122  0.3235351  0.5597232
[3,] -0.5284821 -0.8107807 -0.2516770

Linear Map Z:
            [,1]        [,2]         [,3]
[1,]  0.00873623 -0.03332306  0.060614008
[2,] -0.03042534 -0.00736970  0.030670986
[3,] -0.03975322 -0.04713965 -0.001324943

Estimated GARCH coefficients:
        omega     alpha1     beta1
y1 0.06929475 0.12917711 0.7753224
y2 0.06211986 0.08091875 0.8965807
y3 0.16343143 0.11574017 0.7954333

Convergence codes of component GARCH models:
y1 y2 y3 
 1  1  1 
> sigma.t=NULL  ### 获得波动率矩阵
> for (i in 1:612){
+ sigma.t=rbind(sigma.t,c(m1@H[[i]]))
+ }

> MCHdiag(crtn,sigma.t) ### 模型拟合检验
Test results:  
Q(m) of et: 
Test and p-value:  20.99959 0.02109645 
Rank-based test: 
Test and p-value:  30.76079 0.0006425339 
Qk(m) of epsilon_t: 
Test and p-value:  198.4193 3.856975e-10 
Robust Qk(m):  
Test and p-value:  157.3386 1.469088e-05 

> M=m1@Z
> Minv=solve(M)
> bt=crtn%*%t(Minv)
> cor(bt^2)  ### 关于$b_{t}$的相关系数矩阵
          [,1]      [,2]      [,3]
[1,] 1.0000000 0.1746403 0.3421938
[2,] 0.1746403 1.0000000 0.1063800
[3,] 0.3421938 0.1063800 1.0000000
```


#### 5.5DOC
未完待续，敬请期待。


#### 5.6copula
未完待续，敬请期待。
#### 5.7主波动成分
未完待续，敬请期待。