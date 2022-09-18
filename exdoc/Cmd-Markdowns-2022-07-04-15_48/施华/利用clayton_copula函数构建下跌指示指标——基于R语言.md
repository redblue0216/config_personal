# 利用clayton_copula函数构建下跌指示指标——基于R语言

标签（空格分隔）： 施华

---

### 1.利用下尾相关性构建指标
基于copula技术我们可以计算出下尾条件概率，也就是说当一个序列$X(t)$的实时数据出现在下尾部时，另一个序列$Y(t)$的实时数据出现在下尾部的条件概率为：
$$P(Y(t)<Y_{low}(t)|X(t)<X_{low}(t))=p_{t}$$
### 2.R语言实例代码
本实例采用clayton_copula函数来计算下尾相关性的条件概率，数据随便取的汇率和沪深300，大体思路是找寻其他的提前几期的变量来作为$X(t)$,从而计算出对应的下尾部条件概率，以这个条件概率作为下跌指示变量。大致的R语言代码如下：
```R
setwd('D:/quant/fe')
getwd()



library(Ecdat)
library(copula)
library(fGarch)
library(MASS)
library(fCopulae)



# 载入数据
library("openxlsx")
fe_data = read.xlsx("fe_data.xlsx",sheet=1)
fe_data$date <- convertToDate(fe_data$date)
x = fe_data$fe_USA
y = fe_data$hs300_close
y[is.na(y)] <- 0
x[is.na(x)] <- 0
test_data = data.frame(x=x[1400:1500],y=y[1400:1500])


# 数据可视化
library(ggplot2)
pp1 <- ggplot(fe_data[1400:1500,],aes(date,fe_USA))+geom_line()
pp1
pp0 <- ggplot(fe_data[1400:1500,],aes(date,hs300_close))+geom_line()
pp0
# 分面画图
# Multiple plot function
#
# ggplot objects can be passed in ..., or to plotlist (as a list of ggplot objects)
# - cols:   Number of columns in layout
# - layout: A matrix specifying the layout. If present, 'cols' is ignored.
#
# If the layout is something like matrix(c(1,2,3,3), nrow=2, byrow=TRUE),
# then plot 1 will go in the upper left, 2 will go in the upper right, and
# 3 will go all the way across the bottom.
#
multiplot <- function(..., plotlist=NULL, file, cols=1, layout=NULL) {
  library(grid)
  
  # Make a list from the ... arguments and plotlist
  plots <- c(list(...), plotlist)
  
  numPlots = length(plots)
  
  # If layout is NULL, then use 'cols' to determine layout
  if (is.null(layout)) {
    # Make the panel
    # ncol: Number of columns of plots
    # nrow: Number of rows needed, calculated from # of cols
    layout <- matrix(seq(1, cols * ceiling(numPlots/cols)),
                     ncol = cols, nrow = ceiling(numPlots/cols))
  }
  
  if (numPlots==1) {
    print(plots[[1]])
    
  } else {
    # Set up the page
    grid.newpage()
    pushViewport(viewport(layout = grid.layout(nrow(layout), ncol(layout))))
    
    # Make each plot, in the correct location
    for (i in 1:numPlots) {
      # Get the i,j matrix positions of the regions that contain this subplot
      matchidx <- as.data.frame(which(layout == i, arr.ind = TRUE))
      
      print(plots[[i]], vp = viewport(layout.pos.row = matchidx$row,
                                      layout.pos.col = matchidx$col))
    }
  }
}
multiplot(pp0,pp1,cols=1)






# 用clayton拟合x,y的联合分布
test_data = as.matrix(test_data)
u <- pobs(test_data)
u
f_clayton = fitCopula(data=u,method="mpl",copula=claytonCopula(1,dim=2))
summary(f_clayton)
library(stats)
AIC(f_clayton)



# 提取clayton参数
clayton_alpha = coef(f_clayton)
clayton_alpha



# 定义clayton_copula函数
clayton_func <- function(u,v)
{
  clayton_c = (u^(-clayton_alpha) + v^(-clayton_alpha) - 1)^(-1/(clayton_alpha))                                      
  return(clayton_c)
}


# 定义clayton_copula下尾相关系数
clayton_lambda_l_func <- function(clayton_alpha0)
{
  clayton_lambda_l = 2^(-1/clayton_alpha0)
  return(clayton_lambda_l)
}


# 计算条件概率，以此作为指示变量。
clayton_lambda_l_func(clayton_alpha)
clayton_func(0.05,0.05)/0.05




```



