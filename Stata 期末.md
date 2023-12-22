# Stata 基础应用

**山东大学 政治学与公共管理学院 李欣桦**

本文旨在用最基础的方法，最不用理解的语言，带您使用常见的实证模型完成一套最为基本的定量实证研究程序。

---

[TOC]

---

## 0 为何而定量？

### 0.1 定量的任务

### 0.2 杜绝技术主义

想真正搞好社科研究，应当注重**方法论（methodology）**的理解，而不只是**研究方法（method）**的学习，否则很容易陷入**技术主义**的窠臼，导致不明白这些方法的哲学基础、最后往往只能是“觉得”“认为”式的研究一一这样的研究往往是经不起推敲的。

个人之辞：是保持中国式的实证主义传统，坚定发展质性研究；还是追求社会科学研究独立化、专业化，崇尚科学范式，推动定量研究走深走实，这个问题仍然没有确切的答案，因为它们各自的任务不同，研究的问题不同（质性研究更强调在过程中产生，需要不断修正，一般是研究特殊性问题、过程性问题、描述性问题和解释性问题；定量研究事先确定，提出假设，后面证伪，一般是研究概括性问题、差异性问题、推断性问题、评价性问题和因果性问题），适用的情况不同，不存在冲突，更没有优劣。

## 1 基本定量实证研究程序

### 1.1 主要导向与关键问题

### 1.1.1 描述性研究

描述性研究尤其适合**研究某一现象的发生，描述该现象的趋势或模式，或描述变量之间的关系**。

### 1.1.2 解释性研究

#### 1.1.2.1 关联研究

#### 1.1.2.2 因果研究

### 1.1.3 探索性研究

### 1.2 一般逻辑步骤

- 整理数据并进行数据预处理
- 形成数据结构并做出初步的模型适用性判断
- 针对适用的模型进行数据的基本处理
- 对数据进行描述性统计
- 进行所选取模型的初步估计（先使用OLS，或不带特殊方法的选定模型），观测结果特征
- 根据估计结果对数据结构进行检验，检验其是否满足对应模型的适用性假设（如共线性、异方差检验等）
- 根据检验结果对模型进行改进，并再次进行估计和检验（与原先的初步估计结果作比较，有改善则使用改善后结果，无改善则返回上一步）
- 模型结果解读
- 模型的稳健性检验

### 1.3 主要分析方法与策略

#### 1.3.1 组间分析

通过比较两个或多个组之间的差异来检验假设，适用于比较不同组的均值、方差等差异。

- T检验（T-test）
- 方差分析（ANOVA）

#### 1.3.2 因素（影响）分析

研究一个或多个自变量对一个因变量的影响，适用于探究因素对某一现象的影响。

- OLS模型
- Logit模型

#### 1.3.3 降维分析

将多个变量转换为较少的变量，适用于多个变量之间存在相关性或冗余的情况。

- 聚类分析
- 因子分析

#### 1.3.4 效应分析

研究中介和调节因素对变量之间关系的影响，适用于探究变量之间的复杂关系。

- 中介效应（ME）
- 调节效应（MM）

#### 1.3.5 结构分析

探究个体和群体层面的影响性因素，适用于研究个体和群体之间的关系。

- 结构方程模型（SEM）

#### 1.3.6 层次分析

将问题分解为多个层次，适用于分析多个因素对某一问题的影响。

- 多层线性模型（MLM）

#### 1.3.7 组合分析

将多个变量组合起来进行分析，适用于研究多个变量之间的关系。

- 定性比较分析（QCA）

#### 1.3.8 网络分析

研究社会和空间网络中的节点和关系，适用于研究网络结构和节点之间的关系。

- 社会网络分析（SNA）

#### 1.3.9 元分析

对多个研究结果进行综合分析，适用于综合多个研究结果的结论。

- 元分析（Meta-Analysis）

#### 1.3.10 时序分析

研究时间序列数据的变化趋势和周期性，适用于研究时间序列数据的变化规律。

- 方差自回归移动平均模型（ARIMA）

## 2 常用（好用）外部命令集

*推荐：[连玉君自用外部命令集](https://1.14.139.41/details/15.html)，安装教程见网页内。

```stata
*应该没有人会用的搜索引擎
ssc install lianxh
*用例
lianxh XX法
*下方是本文涉及的外部命令集
ssc install missings
ssc install xtbalance
ssc install diff
ssc install reghdfe
ssc install ftolls
ssc install dpplot
ssc install oparallel
findit spost13_ado // and then install it.
```

## 3 数据基本处理

在实际操作中，数据收集与处理所占用的时间、需要耗费的精力要远大于

### 3.1 数据合并与匹配

```stata
merge 1:1 id year using 要合并的数据集.dta 
// 1:1指的是1对1匹配；1:m指的是1对多匹配；m:1指的是多对1匹配；m:m指的是多对多匹配
keep if _merge==3 // 保留完全匹配的记录（主数据中记录_merge=1，要合并的数据中记录_merge=2）
drop _merge // 删除合并标识变量
```

### 3.2 变量处理

用于对变量进行基本处理（清洗）

- 常用指令

```stata
gen 变量=数值/表达式 // 生成新变量
gen ln变量=log(需要取对数的变量) // 变量取对数
replace 变量=数值/表达式 条件表达式 // 更改变量
order 排序变量1 排序变量2 // 变量的排序
rename 更名前变量 更名后变量 // 变量的重命名
drop 要删除的变量 条件表达式 // 删除变量
tab 字符串组,gen(id变量) // 生成用字符串分组的虚拟变量
egen id变量=group(字符串组) // 生成用字符串分组的虚拟变量
```

- 缺失值处理

```stata
// 注意！使用了外部命令！
missings report // 报告缺失值

egen nmiss=rmiss(要处理的变量1 要处理的变量2 要处理的变量n)
tab nmiss
tab1 要处理的变量1 要处理的变量2 要处理的变量n if nmiss==0
sum a_exprc if nmiss==0
rmiss
```

## 4 数据的描述性统计

### 4.1 纯粹描述

```
su
summarize
sum
```

### 4.2 相关性分析

```s
pwcorr_a
graph matrix 变量1 变量2 变量3 变量4 变量5

```

### 4.3 结果的输出

```stata
esttab 存储模型名1 存储模型名2 nogap ar2 b(%6.4f) t(%6.4f) star(* 0.1 ** 0.05 *** 0.01) // 输出结果为表格
// 输出结果为word
```

## 5 数据结构判断与模型选取

### 5.1 基础模型

#### 5.1.1 最小二乘回归模型（OLS）

- **典型数据结构辨识**

由被解释变量、解释变量构成，由于是基础模型，无典型标识。

![4f68ed6187bd93ac97e2c148d1a9bce](D:\wechat\WeChat Files\wxid_kmz6nsf4osqv22\FileStorage\Temp\4f68ed6187bd93ac97e2c148d1a9bce.png)

- **使用与简述**

**惯用的程序**

```stata
*基本形式
reg 被解释变量 解释变量 控制变量1 控制变量2 // 最基本的回归
reg 被解释变量 解释变量 控制变量1 控制变量2 if 条件语句 // 带条件的回归
xi:reg 被解释变量 解释变量 控制变量1 控制变量2  i.个体变量 // 分组回归 加上个体效应
xi:reg 被解释变量 解释变量 控制变量1 控制变量2  i.时间变量 // 分组回归 加上时间效应
xi:reg 被解释变量 解释变量 控制变量1 控制变量2  i.时间变量 i.个体变量 // 分组回归 加上双向效应
est store 存储模型名
esttab 存储模型名1 存储模型名2 nogap ar2 b(%6.4f) t(%6.4f) star(* 0.1 ** 0.05 *** 0.01) // 输出结果为表格
```

**惯用的模型量**

```stata
predict 新建预测值存储变量名 // 存储模型预测变量
predict e, resid // 存储模型残差
predict new, cooksd // 存储Cook距离，概括观测对拟合的影响
```

**惯用的画图手段**

```stata
graph twoway lfit 被预测变量 预测变量 || scatter 被预测变量 预测变量 // 简单回归线
graph twoway mspline 新建预测值存储变量名 预测变量 || scatter 被预测变量 预测变量 // 带散点图的简单回归线
graph twoway scatter e 新建预测值存储变量名, yline(0) // 残差对预测值拟合图
graph matrix 变量1 变量2 变量3 变量4, half // 散点图矩阵
avplot 预测变量 // 附加变量图，显示y与x的关系
avplots // 将所有附加变量图合并在一幅图中
```

#### **最小二乘回归模型代码的整理**

```stata
*=========================================================================*
*                                  估计部分                                *
*=========================================================================*
reg 被解释变量 解释变量 控制变量1 控制变量2 // 最基本的回归
reg 被解释变量 解释变量 控制变量1 控制变量2 if 条件语句 // 带条件的回归
xi:reg 被解释变量 解释变量 控制变量1 控制变量2  i.个体变量 // 分组回归 加上个体效应
xi:reg 被解释变量 解释变量 控制变量1 控制变量2  i.时间变量 // 分组回归 加上时间效应
xi:reg 被解释变量 解释变量 控制变量1 控制变量2  i.时间变量 i.个体变量 // 分组回归 加上双向效应
est store 存储模型名
esttab 存储模型名1 存储模型名2 nogap ar2 b(%6.4f) t(%6.4f) star(* 0.1 ** 0.05 *** 0.01) // 输出结果为表格
*=========================================================================*
*                                  检验部分                                *
*=========================================================================*
*==========================================*
*                 直接处理                  *
*==========================================*
// 如果什么检验都不打算做，可以直接采取稳健标准误（一般数据）或聚类稳健标准误（面板数据）进行估计。
reg 被解释变量 解释变量1 解释变量2 解释变量3, robust 
reg 被解释变量 解释变量1 解释变量2 解释变量3, cluster 聚类组别
*==========================================*
*                异方差问题                  *
*==========================================*
// 使用以下的估计需要先运行线性回归
reg 被解释变量 解释变量1 解释变量2 解释变量3
*/----------/*
*   问题检验
*/----------/*
rvfplot , yline(0) xline(0) // 总体残差图，先做这个
rvpplot 需要的解释变量 // 各变量残差图，再做这个
// 有一个解释变量情况 BP检验
estat hettest 解释变量 
// 有多个解释变量情况 BP检验
estat hettest 解释变量1 解释变量2，iid rhs
// white检验（用这个）
estat imtest,white
*/----------/*
*   问题改善
*/----------/*
reg 被解释变量 解释变量1 解释变量2 解释变量3, robust // 稳健标准误，加robust即可，用这个
// WLS
qui reg 被解释变量 解释变量1 解释变量2 
predict e,residuals      // 提取回归模型中的残差并命名为e
gen r2 = e^2          // 增加新变量残差的平方
gen 解释变量12 = 解释变量1^2   
gen 解释变量22 = 解释变量2^2        // 增加解释变量X1 X2的平方
regress r2 解释变量12 解释变量22     #得出残差平方和与解释变量X1 X2的回归关系
predict ee,residuals    
gen rr2 = ee^2          #提取回归结果中的残差并进行平方和
#以上代码是对权重的计算，得到权重后，进行回归。
reg 被解释变量 解释变量1 解释变量2[aw = 1/rr2] 
*==========================================*
*               自相关问题                   *
*==========================================*
tsset 年份变量 // 若是时间序列数据则运行这行
xtset 面板变量 时间变量 // 若是面板数据则运行这行
reg 被解释变量 解释变量1 解释变量2 解释变量3
predict e1,residuals
gen Le1=L.e1
twoway (scatter e1 Le1)(lfit e1 Le1) // 看残差图
ac e1 // 看残差自相关图
pac e1 // 看残差偏自相关图
estat bgodfrey // BG检验
wntestq e1 // Q检验
*/----------/*
*   问题改善
*/----------/*
reg 被解释变量 解释变量1 解释变量2 解释变量3, cluster 聚类组别 // 聚类稳健标准误，用这个
newey 被解释变量 解释变量1 解释变量2 解释变量3,lag(#) // 延后期数为上面图上所观测到
prais 被解释变量 解释变量1 解释变量2 解释变量3 // 使用默认的 PW 估计法
prais 被解释变量 解释变量1 解释变量2 解释变量3,corc // 使用 CO 估计法
*==========================================*
*              多重共线性问题                 *
*==========================================*
graph matrix 被解释变量 解释变量1 解释变量2 解释变量3 ,half // 画图
reg 被解释变量 解释变量1 解释变量2 解释变量3
estat vif 
pwcorr_a 被解释变量 解释变量1 解释变量2 解释变量3
*/----------/*
*   问题改善
*/----------/*
reg 被解释变量 解释变量1 解释变量3
estat vif
reg 被解释变量 解释变量1 解释变量2 
estat vif
reg 被解释变量 解释变量2 解释变量3
estat vif
reg 被解释变量 解释变量1 解释变量2 解释变量3 解释变量4
estat vif
reg 被解释变量 解释变量1 解释变量2 解释变量3 解释变量4 解释变量5
estat vif
reg 被解释变量 解释变量1 解释变量2 解释变量3
gen 解释变量12 = 解释变量1/解释变量2
reg 被解释变量 解释变量12 解释变量3
```

#### 5.1.2 离散选择模型（DCM）

模型选择指示：

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222181608838.png" alt="image-20231222181608838" style="zoom:67%;" />

##### 5.1.2.1 分类评定（逻辑回归）模型（Logit）

##### 5.1.2.2 有序分类评定（逻辑回归）模型（OLogit）

无序Logit模型，即不考虑因变量的序次性。但在现实实践中，经常会遇到**序次被解释变量**，如果不考虑其序次性，将会损失部分数据信息。序次变量尤其常见于问卷调查中。当用无序Logit模型（多项式Logit模型、随机参数Logit模型、潜类别Logit模型等）分析这些带有序次的因变量时，则无法考虑因变量的序次性，将造成一部分信息损失（information loss），因此这种问题时应该使用序次离散选择模型，尤其是有序分类评定（逻辑回归）模型（OLogit）。

- **典型数据结构辨识**

由上，我们可得典型数据结构：被解释变量是定序变量，长得很像**问卷数据**。

<img src="D:\wechat\WeChat Files\wxid_kmz6nsf4osqv22\FileStorage\Temp\7fcebe8f1887309dcb99bf2ce52f088.png" alt="7fcebe8f1887309dcb99bf2ce52f088" style="zoom: 80%;" />

- **使用与简述**

**变量的处理**

```stata
// 若要进行重新编码
tab 被解释变量 // 描述
recode 被解释变量 (编码前取值=编码后取值 "XXX") (编码前取值=编码后取值 "XXX") (编码前取值=编码后取值 "XXX"), gen (新变量)
label var 新变量 "标签"
tab 被解释变量 // 描述
```

**模型的估计**

```stata
ologit 被解释变量 解释变量1 解释变量2 解释变量3
omodel logit 被解释变量 解释变量1 解释变量2 解释变量3 // 推荐
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222180813017.png" alt="image-20231222180813017" style="zoom:50%;" />

因为因变量为5个类别，所以估计出4个截距（cut），截距参数值可以解释为临界点或需先达到哪个值才会进入到相应因变量的类别：

当估计值<-3.96时，y=1，依此类推。

针对自变量的参数估计值，主要依据其正负号进行解释。参数的符号反映的是y更有可能落在定序变量的某一端。例如，A7a的系数为-0.106，表明随着受教育程度的增加，A45更有可能落在因变量分类值较小的一端 5→1 ；A13的系数为0.001，表明其更可能落在因变量分类值更大的一端。

除了定量的解释，我们还可以用边际效应结果进行解释：

```stata
margins,dydx(*)
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222181246521.png" alt="image-20231222181246521" style="zoom:50%;" />

边际效应结果显示，A7a每增加1，A45是1、2、3的概率将分别增加0.81%、0.64%、-1.15%，依此类推。

```stata
prtab 要解释的变量
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222190746964.png" alt="image-20231222190746964" style="zoom:50%;" />

prtab可以解释在被解释变量的每个取值下，解释变量的概率。

```stata
// 用定序变量的每级取值来进行可视化
predict 第一个取值 第二个取值 第三个取值 
sum 第一个取值 第二个取值 第三个取值
twoway qfit 第一个取值 要解释的变量
twoway qfit 第二个取值 要解释的变量
twoway qfit 第三个取值 要解释的变量
twoway qfit 第一个取值 要解释的变量 || 第二个取值 要解释的变量 || 第三个取值 要解释的变量 ||, ///
legend(label(1 标签1) label(2 标签2) label(3 标签3))
```

- **模型评估**

```stata
fitstat // 哪都可以用！神包！
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222191327974.png" alt="image-20231222191327974" style="zoom:50%;" />

fitstat外部命令几乎可以评价一切模型。主要使用AIC进行比较，AIC鼓励数据拟合的优良性但尽量避免出现过度拟合（Overfitting）的情况。所以优先考虑的模型应是AIC值最小的那一个。赤池信息量准则的方法是寻找可以最好地解释数据但包含最少自由参数的模型。

- **平行线假设检验**

```stata
ologit 被解释变量 解释变量1 解释变量2 解释变量3
est store 模型存储名
oparallel // 平行线检验
omodel logit 被解释变量 解释变量1 解释变量2 解释变量3
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222183707298.png" alt="image-20231222183707298" style="zoom:80%;" />

平行性检验的原假设为**模型满足平行性**，因而如果P值大于0.05则说明模型接受原假设，即符合平行性假设。反之如果P值小于0.05则说明模型拒绝原假设，模型不满足平行性假设，brant检验与之相反。由上图可以看出确实完全不满足平行性假设。

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222183005992.png" alt="image-20231222183005992" style="zoom:50%;" />

omodel自带平行性检验。平行性检验的原假设为**模型满足平行性**，因而如果P值大于0.05则说明模型接受原假设，即符合平行性检验。反之如果P值小于0.05则说明模型拒绝原假设，模型不满足平行性检验。由上图可以看出确实完全不满足平行性假设。

- **问题解决与模型改进**

```stata
oglm 被解释变量 解释变量1 解释变量2 解释变量3 // 考虑异方差的OLogit，出现异方差时使用，但一般还是使用omodel logit
gologit2 被解释变量 解释变量1 解释变量2 解释变量3 // 广义有序逻辑回归，不符合平行性假设时使用
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222192341021.png" alt="image-20231222192341021" style="zoom:50%;" />

分阶段回归并解释即可。

##### **有序逻辑回归模型代码的整理**

```stata
*==========================================*
*               数据的预处理                 *
*==========================================*
// 若要进行重新编码
tab 被解释变量 // 描述
recode 被解释变量 (编码前取值=编码后取值 "XXX") (编码前取值=编码后取值 "XXX") (编码前取值=编码后取值 "XXX"), gen (新变量)
label var 新变量 "标签"
tab 被解释变量 // 描述
*==========================================*
*                 模型的估计                 *
*==========================================*
ologit 被解释变量 解释变量1 解释变量2 解释变量3
omodel logit 被解释变量 解释变量1 解释变量2 解释变量3 // 推荐
// 用定序变量的每级取值来进行可视化
predict 第一个取值 第二个取值 第三个取值 
sum 第一个取值 第二个取值 第三个取值
twoway qfit 第一个取值 要解释的变量
twoway qfit 第二个取值 要解释的变量
twoway qfit 第三个取值 要解释的变量
twoway qfit 第一个取值 要解释的变量 || 第二个取值 要解释的变量 || 第三个取值 要解释的变量 ||, ///
legend(label(1 标签1) label(2 标签2) label(3 标签3))
fitstat
ologit 被解释变量 解释变量1 解释变量2 解释变量3
*==========================================*
*            模型的检验与优化                 *
*==========================================*
est store 模型存储名
oparallel // 平行线检验
omodel logit 被解释变量 解释变量1 解释变量2 解释变量3
oglm 被解释变量 解释变量1 解释变量2 解释变量3 // 考虑异方差的OLogit，出现异方差时使用，但一般还是使用omodel logit
gologit2 被解释变量 解释变量1 解释变量2 解释变量3 // 广义有序逻辑回归，不符合平行性假设时使用
```

#### 5.1.3 时间序列模型（TSM）

#### 5.1.4 截面数据模型（CSM）

#### 5.1.5 面板数据模型（PDM）

- **典型数据结构辨识**

由面板变量（id）、时间变量（year）、被解释变量、解释变量构成，典型特征是id和year同时存在在同一条记录上。

<img src="D:\wechat\WeChat Files\wxid_kmz6nsf4osqv22\FileStorage\Temp\1caa3c7c91d460b9da30c51fe399899.png" alt="1caa3c7c91d460b9da30c51fe399899" style="zoom:80%;" />

- **使用与简述**

面板数据是既有个体变量又有时间变量的数据。从维度来看，时间序列数据和截面数据均为一维。面板数据可以看做为**时间序列与截面混合数据**，是**截面上个体在不同时点重复观测数据，**因此它是二维数据。比如一个数据集有100家公司五年内的数据，总共100×5=500条数据，则该数据集是面板数据。

要使用面板数据，需要先对stata声明该数据集是面板数据，声明代码如下：

```stata
xtset 面板变量 时间变量
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231221211320214.png" alt="image-20231221211320214" style="zoom:100%;" />

在声明面板数据后，stata会报告变量个体变量名（后附面板平衡情况。如果100个公司，每个公司5年，总计500行并没有缺失数据，此种数据叫**平衡数据**。如果出现个别公司少了某个的数据，此种数据叫**不平衡数据**。通常，平衡数据适用固定效应面板模型，不平衡数据适用随机随机效应面板模型），时间变量名，时间跨度，时间间隔（Delta）。

若需要将不平衡面板数据变为平衡面板数据，需要在声明面板数据后使用平衡面板命令，其会自动将多余的记录删除，将不平衡面板数据变为平衡面板数据。

```stata
// 注意！使用了外部命令！
xtbalance, range(观测首期年份 观测末期年份)
```

>同样还值得注意的概念是长面板与短面板。**如果个体维度比时间维度大（ N>>T ），我们称之为短面板；反之，如果时间维度比个体维度大（ T>>N ），我们称之为长面板**。
>
>还有同质面板与异质面板，同质面板数据是指**数据集中的所有个体或单位具有相同的特征和属性**。例如，一个同质面板数据集可以包括同一家公司在不同时间点的财务数据，所有的个体都属于同一类别并且具有相似的特征。
>异质面板数据则是指**数据集中的个体或单位具有不同的特征和属性**。例如，一个异质面板数据集可以包括不同公司在不同时间点的财务数据，每个个体都可能属于不同的行业或具有不同的规模和属性。
>因此，同质面板数据和异质面板数据在数据分析和建模时需要考虑的因素和方法可能会有所不同。在处理同质面板数据时，可以更加关注时间序列分析和固定效应模型；而在处理异质面板数据时，则可能需要考虑更多的交叉效应和随机效应模型。

- **平稳性检验**

在使用面板数据前，按照正规程序，在回归前需检验数据的**平稳性**。《计量经济学 第三版》李子奈 著 pp.274指出，一些非平稳的“经济时间序列”往往表现出共同的变化趋势，而这些序列间本身不一定有直接的关联，此时，对这些数据进行回归，尽管有较高的R平方，但其结果是没有任何实际意义的。这种情况称为称为**虚假回归或伪回归（spurious regression）**。因此为了避免伪回归，确保估计结果的有效性，我们必须对“各面板序列”的平稳性进行检验。

| **检验方法**   | **基本假设**                                       |
| -------------- | -------------------------------------------------- |
| **LLC**        | **假设该序列是截面不相关、同质的面板数据（平衡）** |
| **Breintung**  | **假设该序列是截面不相关、同质的面板数据（平衡）** |
| **IPS**        | **假设该序列是截面不相关、异质的面板数据**         |
| **ADF-Fisher** | **假设该序列是截面不相关、异质的面板数据**         |
| **PP-Fisher**  | **假设该序列是截面不相关、异质的面板数据**         |

其中，最常用的是**LLC与IPS**检验。

**LLC检验流程（适用于长面板，有做的必要）**

> （1）带**截距项**和**时间趋势**
> （2）带**截距项**
> （3）**不带截距项**和**不带时间趋势项**
>
> （4）**考虑扰动项存在自相关**，引入差分滞后项

一般检验流程

```stata
xtunitroot llc 待检验变量, trend demean // 带截距项和时间趋势
xtunitroot llc 待检验变量, demean // 带截距项
xtunitroot llc 待检验变量, noconstant // 不带截距项和时间趋势
xtunitroot llc 待检验变量, trend demean lags(aic #) 
// 带截距项和时间趋势，考虑扰动项存在自相关的情形，#取小于等于4的任意值
xtunitroot llc 待检验变量, demean lags(aic #)
// 带截距项，考虑扰动项存在自相关的情形，#取小于等于4的任意值
xtunitroot llc 待检验变量, noconstant lags(aic #)
// 不带截距项和时间趋势，考虑扰动项存在自相关的情形，#取小于等于4的任意值
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231221222628962.png" alt="image-20231221222628962" style="zoom:50%;" />

由检验结果可知，调整后**t统计量对应的P值为0.0258>0.05**，拒绝原假设，该变量的所有面板是稳定的，在得出稳定结果后，后续的检验都没有必要做了，同样是看t统计量对应的P值即可。

```
xtline 被检验变量, overlay
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231221221002629.png" alt="image-20231221221002629" style="zoom:50%;" />

画图辅助分析，观察各时间序列的数据变化趋势，如果能看出较为明显的趋势，我们就把它当成是平稳的。

**在上述检验方法中，只要有一项是平稳的，我们都把它当成是平稳的。**

**IPS检验流程（短面板，实际上短面板不需要做单位根检验）**

>（1）带**截距项**和**时间趋势**（截距项是为了减轻截面相关对检验的影响）
>（2）带**截距项**
>（3）考虑扰动项存在自相关，引入差分滞后项

一般检验流程

```stata
xtunitroot ips 待检验变量, trend demean // 带截距项和时间趋势
xtunitroot ips 待检验变量, demean // 带截距项
xtunitroot ips 待检验变量, lags(aic #) trend demean 
// 带截距项和时间趋势，考虑扰动项存在自相关的情形，#取小于等于4的任意值
xtunitroot ips 待检验变量, lags(aic #) demean 
// 带截距项，考虑扰动项存在自相关的情形，#取小于等于4的任意值
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231221214159536.png" alt="image-20231221214159536" style="zoom:50%;" />

由检验结果可知，**t-bar统计量为-1.7146，大于1%显著性水平的临界值-2.450**，所以不能拒绝面板单位根的原假设（即面板存在单位根）。此外，统计量**Z-t-tilder-bar对应的P值为0.1206>0.05**，同样不能拒绝原假设。

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231221214616389.png" alt="image-20231221214616389" style="zoom:50%;" />

由检验结果可知，**t-bar统计量为-1.0527，大于1%显著性水平的临界值**，所以不能拒绝面板单位根的原假设（即面板存在单位根）。此外，统计量**Z-t-tilder-bar对应的P值为0.9982>0.05**，同样不能拒绝原假设。

下面，我们考虑扰动项存在自相关的情形，并引入差分滞后项。

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231221220836864.png" alt="image-20231221220836864" style="zoom:50%;" />

由检验结果可知，统计量**Z-t-tilder-bar对应的P值为0.0358<0.05**，表示在平均**滞后1.67期**时，可以拒绝原假设，一些面板是平稳的。那么按照检验的结果，我们就需要将该变量滞后约1-2期，若不考虑滞后，则应进一步做**面板协整**检验。

```
xtline 被检验变量, overlay
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231221221002629.png" alt="image-20231221221002629" style="zoom:50%;" />

画图辅助分析，观察各时间序列的数据变化趋势，如果能看出较为明显的趋势，我们就把它当成是平稳的。

**在上述检验方法中，只要有一项是平稳的，我们都把它当成是平稳的。**

下面是批量IPS检验方法。

```stata
//IPS检验
//使用方法：修改xtset语句，设定面板变量与时间变量；将需要检验的变量替换in后的"待检验变量x"中即可，支持多个变量一次性检验
xtset 面板变量 时间变量
foreach var in 待检验变量1 待检验变量2 待检验变量3 待检验变量4 待检验变量5 待检验变量6 {
	local variable "`var'"
	di _n _n "---`variable'---"
	qui xtsum `var'
	local n=r(n)
	local T=r(Tbar)
	local i=int(12 * (r(Tbar) / 100)^(1/4))-1
	while `T' < `n' {
		di _n "直接进行检验:"
		qui xtunitroot ips `var',tr
		local p=r(p_zttildebar)
		while `p'<0.05{
			local result "平稳"
			local p=0.05
		}
		while `p'>0.05{
			local result "非平稳"
			local p=0.05
		}
		di "带截距项和时间趋势,`result'"
		qui xtunitroot ips `var'
		local p=r(p_zttildebar)
		while `p'<0.05{
			local result "平稳"
			local p=0.05
		}
		while `p'>0.05{
			local result "非平稳"
			local p=0.05
		}
		di "带截距项,`result'"
		di _n "将面板数据减去各截面单位的均值进行检验:"
		qui xtunitroot ips `var',tr demean
		local p=r(p_zttildebar)
		while `p'<0.05{
			local result "平稳"
			local p=0.05
		}
		while `p'>0.05{
			local result 非平稳"
			local p=0.05
		}
		di "带截距项和时间趋势,`result'"
		qui xtunitroot ips `var',demean
		local p=r(p_zttildebar)
		while `p'<0.05{
			local result "平稳"
			local p=0.05
		}
		while `p'>0.05{
			local result "非平稳"
			local p=0.05
		}
		di "带截距项,`result'"
		local T=0
		local n=0
	}
	while `T' > `n'{
		di "该面板为长面板,不适合IPS检验"
		local T=0
		local n=0
	}
}
```

- **协整检验**

如果发现面板数据中的每个时间序列都是单位根过程（**如果变量都拒绝了单位根存在的原假设，说明数据都是平稳的0阶单整，无需再进行协整检验**），则应进一步做**面板协整**检验（panel cointegration tests），考察变量之间是否存在长期均衡的协整关系。协整检验是数据不平稳但是同阶单整的前提下，检验变量X与变量y之间是否存在长期均衡关系。

对于有单位根的变量，传统的处理方法是进行**一阶差分**而得到平稳序列。 但一阶差分后变量的经济含义与原序列并不相同，而有时我们仍然希望**使用原序列**进行回归。 如果多个单位根变量之间由于某种经济力量而存在“长期均衡关系”(long-run equilibrium)，则有可能**使用原序列**进行回归。

**Kao检验**

 ```stata
 xtcointtest kao 被解释变量 解释变量1 解释变量2 解释变量3, demean 
 /*Kao检验假定同期截面不相关，demean是截距项，为了减轻截面相关对协整检验的影响*/
 ```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231221223108748.png" alt="image-20231221223108748" style="zoom:50%;" />

上表汇报了 5 种不同的检验统计量，我们主要关注**前三种**：MDF、DF、ADF，其对应的 p 值均小于 0.05，故可在 5% 水平上拒绝 “不存在协整关系” 的原假设，认为存在协整关系。

**Pedroni 检验**

```stata
xtcointtest pedroni 被解释变量 解释变量1 解释变量2 解释变量3, trend demean ar(panels)
xtcointtest pedroni 被解释变量 解释变量1 解释变量2 解释变量3, demean ar(panels)
xtcointtest pedroni 被解释变量 解释变量1 解释变量2 解释变量3, noconstant demean ar(panels)
*(1)三个方程：含个体固定效应项和时间趋势项、仅含个体固定效应项和两者均不含的检验
*(2)ar(panels)意为该检验在异质面板数据的情况下进行；ar(same)意为该检验在同质面板数据的情况下进行，面板数据一般都是异质的
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231221223854210.png" alt="image-20231221223854210" style="zoom:50%;" />

上表汇报了 3 种不同的检验统计量，其中MP-t对应的 p 值大于 0.05，不能拒绝 “不存在协整关系” 的原假设，认为不存在协整关系。

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231221224123687.png" alt="image-20231221224123687" style="zoom:50%;" />

调整参数后，上表汇报了 3 种不同的检验统计量，其对应的 p 值均小于 0.05，故可在 5% 水平上拒绝 “不存在协整关系” 的原假设，认为存在协整关系。

同样地，**在上述检验方法中，只要有一项是平稳的，我们都把它当成是平稳的**。

##### 5.1.5.1 混合估计模型（POOL）

混合模型的特点是无论对任何个体或者截面，回归系数都是相同的。即不分组的全局OLS回归。不同个体之间不存在差异，不同时间项之间也不存在显著性差异，可以直接把面板数据混合在一起用普通最小二乘法（OLS）估计参数。

```stata
reg 被解释变量 解释变量1 解释变量2 解释变量3
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231221232544560.png" alt="image-20231221232544560" style="zoom:50%;" />

##### 5.1.5.2 固定效应模型：个体效应、时间效应、双向效应（FE-TE，FE-FE，FE-ME）

- **个体固定效应模型**：个体固定效应模型是对于不同的时间序列（个体）只有截距项不同的模型：

从时间和个体上看，面板数据回归模型的解释变量对被解释变量的边际影响均是相同的，而目除模型的解释变量之外，影响被解释变量的其他所有（未包括在回归模型或不可观测的）确定性变量的效应只是随个体变化而不随时间变化。

> **下面是对个体固定效应进行估计**

**好方法**

```stata
xtreg 被解释变量 解释变量1 解释变量2 解释变量3, fe
// 或者 
xtreg 被解释变量 解释变量1 解释变量2 解释变量3, fe i(id)
// 或者
reghdfe 被解释变量 解释变量1 解释变量2 解释变量3, absorb(面板变量) vce(cluster 面板变量)
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231221235111510.png" alt="image-20231221235111510" style="zoom:50%;" />

针对个体固定效应（原假设：不存在个体固定效应）的F检验自动生成（最后一行），此处F检验对应的p值为0.0000，表示个体固定效应显著。　

**笨方法**

```stata
xi:xtreg 被解释变量 解释变量1 解释变量2 解释变量3 i.面板变量,fe 
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231221234131818.png" alt="image-20231221234131818" style="zoom:50%;" />

观测个体固定效应是否大多数都显著（p<0.05），若是，则说明个体固定效应显著，个体固定效应模型效果好。

**个体固定效应就是个体层面不随时间变化的影响因素，每个个体都独特的个体特征，其目的是为了控制个体层面独一无二的不随时间变化的特征，因此分析个体固定效应，除了个体特征说明、模型比较以外，没有其他的意义。**

- **时间固定效应模型**：时点固定效应模型就是对于不同的截面（时点）有不同截距的模型。如果确知对于不同的截面，模型的截距显著不同，但是对于不同的时间序列（个体）截距是相同的，那么应该建立时点固定效应模型。

> **下面是对时间固定效应进行估计**

**好方法**

```stata
xtreg 被解释变量 解释变量1 解释变量2 解释变量3 ,fe i(year)
reghdfe 被解释变量 解释变量1 解释变量2 解释变量3, absorb(年份变量) vce(cluster 面板变量)
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231221235313172.png" alt="image-20231221235313172" style="zoom:50%;" />

针对时间固定效应（原假设：不存在时间固定效应）的F检验自动生成（最后一行），此处F检验对应的p值为0.0000，表示时间固定效应显著。　

**笨方法**

```stata
xi:reg 被解释变量 解释变量1 解释变量2 解释变量3 i.时间变量
test 观测的时间虚拟变量1 观测的时间虚拟变量2 观测的时间虚拟变量3 观测的时间虚拟变量4 观测的时间虚拟变量5 （以上模型生成的）........
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222000159777.png" alt="image-20231222000159777" style="zoom:50%;" />

上图是时间固定效应模型的估计结果。

![image-20231222000259103](C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222000259103.png)

用Wald检验检验时间固定效应，上图中检验结果对应的p值为0.0000，表示时间固定效应显著。　

**时间固定效应就是时间层面不随个体变化的影响因素，每个年份都有独特的年份特征，其目的是为了控制时间层面独一无二的不随个体变化的特征，比如每年的宏观经济。除了时序特征说明、模型比较以外，没有其他的意义。**

- **双向固定效应模型**：双向固定效应模型就是对于不同的截面（时点）、不同的时间序列（个体）都有不同截距的模型。如果确知对于不同的截面、不同的时间序列（个体）模型的截距都显著不相同，那么应该建立时点个体固定效应模型。

> **下面是对双向固定效应进行估计**

**第一种方法（若不显著去试第二种方法）**

重新生成一个panel varible比如code，此code是id和year的综合，可以克服传统方法的局限性（“independent variables are collinear with the panel variable year”）。

```stata
gen code = 年份变量+面板变量
xtset code 年份变量
xi:xtreg 被解释变量 解释变量1 解释变量2 解释变量3 i.year,fe
// 记得恢复原面板
xtset 面板变量 年份变量
或
reghdfe 被解释变量 解释变量1 解释变量2 解释变量3, absorb(面板变量 年份变量) vce(cluster 面板变量)
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222000919547.png" alt="image-20231222000919547" style="zoom:50%;" />

针对双向固定效应（原假设：不存在双向固定效应）的F检验自动生成（最后一行），此处F检验对应的p值为0.4483>0.05，表示不存在双向固定效应。　

**第二种方法（若报错去试第一种方法）**

```stata
xi:xtreg 被解释变量 解释变量1 解释变量2 解释变量3 i.时间变量,fe 
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222001229846.png" alt="image-20231222001229846" style="zoom:50%;" />

针对双向固定效应（原假设：不存在双向固定效应）的F检验自动生成（最后一行），此处F检验对应的p值为0.0000>0.05，表示双向固定效应显著。　

>最后在三种模型中到底选择哪个，主要根据**F检验值是否显著**进行判断，第一个显著后面不显著就选个体固定效应模型，第二个显著其他不显著选择时间固定效应模型，第三个显著意味着前两个均显著，那么选择个体时间双固定模型。

##### 5.1.5.3 随机效应模型（RE）

随机效应模型与固定效应模型FE的区别在于**对个体差别的定义**，**固定效应模型刻画了不同个体的特殊影响，个体间的差别反映在每个个体都有各自截距项；而随机效应模型则假设个体间的差别是随机的**。由此固定效应模型更适合用于研究样本之间的区别（异质性），而随机效应更适合用于由样本来推断总体特征。

```stata
xtreg 被解释变量 解释变量1 解释变量2 解释变量3 ,re 
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222001411102.png" alt="image-20231222001411102" style="zoom:50%;" />

上图为随机效应估计结果。

**模型的选择（豪斯曼检验）**

```stata
xtreg 被解释变量 解释变量1 解释变量2 解释变量3, fe
est store FE
xtreg 被解释变量 解释变量1 解释变量2 解释变量3, re
est store RE
hausman FE RE
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222001555900.png" alt="image-20231222001555900" style="zoom:50%;" />

原假设是随机效应和固定效应无差异，上图中p值为0.0000<0.05，**拒绝原假设，则采用固定效应模型**，否则随机效应模型。　

**模型的具体解读与OLS一致**

##### **面板数据模型代码的整理**

```stata
*==========================================*
*              面板数据的声明与处理           *
*==========================================*
xtset 面板变量 时间变量
xtbalance, range(观测首期年份 观测末期年份)
*==========================================*
*         平稳性检验（大多数情况可以不做）       *
*==========================================*
*若时间明显大于个体数，用这个平稳性检验方法。
xtunitroot llc 待检验变量, trend demean // 带截距项和时间趋势
xtunitroot llc 待检验变量, demean // 带截距项
xtunitroot llc 待检验变量, noconstant // 不带截距项和时间趋势
xtunitroot llc 待检验变量, trend demean lags(aic #) 
// 带截距项和时间趋势，考虑扰动项存在自相关的情形，#取小于等于4的任意值
xtunitroot llc 待检验变量, demean lags(aic #)
// 带截距项，考虑扰动项存在自相关的情形，#取小于等于4的任意值
xtunitroot llc 待检验变量, noconstant lags(aic #)
// 不带截距项和时间趋势，考虑扰动项存在自相关的情形，#取小于等于4的任意值
xtline 被检验变量, overlay
*若个体明显大于时间数，用这个平稳性检验方法，也可以不检验。
xtunitroot ips 待检验变量, trend demean // 带截距项和时间趋势
xtunitroot ips 待检验变量, demean // 带截距项
xtunitroot ips 待检验变量, lags(aic #) trend demean 
// 带截距项和时间趋势，考虑扰动项存在自相关的情形，#取小于等于4的任意值
xtunitroot ips 待检验变量, lags(aic #) demean 
// 带截距项，考虑扰动项存在自相关的情形，#取小于等于4的任意值
xtline 待检验变量, overlay
*==========================================*
*      协整检验（平稳性检验都没过就要做这个）    *
*==========================================*
*有一个过了就行
xtcointtest kao 被解释变量 解释变量1 解释变量2 解释变量3, demean 
/*Kao检验假定同期截面不相关，demean是截距项，为了减轻截面相关对协整检验的影响*/
xtcointtest pedroni 被解释变量 解释变量1 解释变量2 解释变量3, trend demean ar(panels)
xtcointtest pedroni 被解释变量 解释变量1 解释变量2 解释变量3, demean ar(panels)
xtcointtest pedroni 被解释变量 解释变量1 解释变量2 解释变量3, noconstant demean ar(panels)
*(1)三个方程：含个体固定效应项和时间趋势项、仅含个体固定效应项和两者均不含的检验
*(2)ar(panels)意为该检验在异质面板数据的情况下进行；ar(same)意为该检验在同质面板数据的情况下进行，面板数据一般都是异质的
*==========================================*
*                模型估计                   *
*==========================================*
reg 被解释变量 解释变量1 解释变量2 解释变量3 // 最原始的，用来做6部分的数据检验
xtreg 被解释变量 解释变量1 解释变量2 解释变量3, fe i(id) // 个体固定效应模型
xtreg 被解释变量 解释变量1 解释变量2 解释变量3 ,fe i(year) // 时间固定效应模型
xi:xtreg 被解释变量 解释变量1 解释变量2 解释变量3 i.时间变量,fe // 双向固定效应模型法二，若报错做法一
gen code = 年份变量+面板变量 // 双向固定效应模型法一
xtset code 年份变量 // 双向固定效应模型法一
xi:xtreg 被解释变量 解释变量1 解释变量2 解释变量3 i.year,fe // 双向固定效应模型法一
// 记得恢复原面板
xtset 面板变量 年份变量 // 双向固定效应模型法一
* 到这里就可以判断如果用固定的话用谁了
xtreg 被解释变量 解释变量1 解释变量2 解释变量3 ,re // 随机效应模型
* 豪斯曼检验判断用固定还是随机
xtreg 被解释变量 解释变量1 解释变量2 解释变量3, fe
est store FE
xtreg 被解释变量 解释变量1 解释变量2 解释变量3, re
est store RE
hausman FE RE 
```

### 5.2 结构方程模型（SEM）





### 5.3 政策评估模型（PEM）

#### 5.3.1 断点回归法（RD）

#### 5.3.2 双重差分法（DID）

##### 5.3.2.1 普通双重差分（DID）

- **典型数据结构辨识**

DID模型往往运用在政策评估领域之中，常用面板数据模型，其与事件分析法的关键不同点在于它的面板在数据结构上（不讨论缺失值），往往是**平衡的**（平衡面板概念见面板数据模型部分），每个个体的时间跨度完整。**典型特征是有政策发生后虚拟指示变量（time）和控制组虚拟指示变量（treated）**。

>1）事件研究法有一个差异:pre vs post。而DID 做了2个差异:pre vs post和 control vs 处理。
>
>2）DID是一种ATE(处理窗口期间的平均效应)方法。ESM是LATE(处理瞬间的效应)方法。

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222092043828.png" alt="image-20231222092043828" style="zoom:50%;" />

- **使用与简述**

DID方法的核心思想是：构建自然对照实验，即施加政策影响前两个地区（如A和B）的情况均相同或差不多，区别在于**A地区实施了某项政策而B地区未实施**，则可以对比或定量分析实施该政策前后A和B的目标变量的变化，从而评估该政策的效应。

**变量的构建**

```stata
gen time = (时间变量 >= 政策采取时间)&!missing(时间变量)
// 政策发生时间的虚拟变量构建
gen treated = 自定控制变量组限制表达式&!missing(面板变量)
// 政策发生时间的虚拟变量构建
gen did = time*treated
// 构建DID估计量，即时间和空间的交互项
```

**模型的估计**

```stata
reg 被解释变量 did time treated,r // 一般的DID模型（稳健标准误）
reg 被解释变量 time##treated,r // 无需设置交互项，结果是一样的
diff 被解释变量, t(treated) p(time) // 使用外部命令估计DID
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222094639148.png" alt="image-20231222094639148" style="zoom:60%;" />

看did变量后的效应系数（Coefficient）和p值，显然看出p值=0.088<0.100，政策在10%水平上，有显著的负效应（效应系数为：-2.52e+09）。

**平行趋势检验**

以上的基准回归只有当地区在政策前足够相似才能够保证DID提取的是政策的因果效应，所以研究者需要知道两组地区在政策前有多大差异。实现这一目标的方法是将年份虚拟变量乘以实验组虚拟变量，这一交互项就可以捕捉两组地区在每一年份的差异。

```stata
gen Dyear = 年份变量-1994
gen 前第n期 = (Dyear==-n&treated==1)
gen 前第二期 = (Dyear==-2&treated==1)
gen 前第一期 = (Dyear==-1&treated==1)
gen 当期 = (Dyear==0&treated==1)
gen 后第一期 = (Dyear==1&treated==1)
gen 后第二期 = (Dyear==2&treated==1)
gen 后第m期 = (Dyear==n&treated==1)
// 或者
gen Dyear = 年份变量-1994
forvalues i = n(-1)1{  
	gen pre_`i' = (Dyear == -`i' & treated == 1) 
}  
gen current = (Dyear == 0 & treated == 1)  
forvalues j = 1(1)m{  
	gen time_`j' = (Dyear == `j' & treated == 1)  
}
// 将以上交互项作为解释变量进行回归
xtreg y time treated 前第n期 前第二期 前第一期 当期 后第一期 后第二期 后第m期 i.年份变量, fe
// 进行双向固定效应回归，观察结果分析即平行趋势检验检验
est sto reg
coefplot reg,keep(第n期 前第二期 前第一期 当期 后第一期 后第二期 后第m期) vertical recast(connect) yline(0) 
// 用外部命令绘制多期动态效应图
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222100937343.png" alt="image-20231222100937343" style="zoom:50%;" />

双向固定效应回归结果如上，**观察前期结果是否显著**，在这里前第三期、前第二期、前第一期的p值均大于0.05，不显著、系数较小（相对）则满足了平行趋势假定。**再观察后期结果显著与否**，在这里后第一期p值为0.003<0.01，结果显著，产生了明显的负效应（系数值为-7.15e+09）。

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222100610001.png" alt="image-20231222100610001" style="zoom: 50%;" />

多期动态效应图回应了双向固定效应回归的结果。

**安慰剂检验**

在DID模型中，安慰剂检验是为了排除非政策因素对研究结果的影响，避免研究对象因提前得知政策将要实施这一信号而产生了主观上的变化，从而导致“政策效应”存在误差。在安慰剂检验中，最常见的就是个体安慰剂检验，通过绘制核密度图进行观测，一般来说，点越集中在横轴零点附近，说明通过了安慰剂检验，DID模型的“政策效应”就越靠谱。

**安慰剂检验的流程**

>法一：个体安慰剂检验
>
>法二：提前政策时间

```stata
// 注意！外部命令！
cap erase "原dta数据集名"
permute did beta = _b[did] se = _se[did] df = e(df_r),reps(500) seed(123) ///
saving("更改后dta数据集名"): reghdfe 被解释变量 did, absorb(面板变量 时间变量) vce(robust)

use "更改后dta数据集名",clear
gen t = beta / se
gen p = 2* ttail(df,abs(beta/se))

dpplot beta, xtitle("估计值", size(*0.8)) xlabel(, format(%4.3f) labsize(small)) ///
ytitle("P值", size(*0.8)) ylabel(, nogrid format(%4.3f) labsize(small)) ///
caption("") graphregion(fcolor(white))
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222105117374.png" alt="image-20231222105117374" style="zoom:50%;" />

从图形来看，点大多都**分布在横轴0的左右**，所以我们可以认为DID模型的结果是稳健的。

```stata
use "Panel101.dta", clear
gen time = (year >= 1993)&!missing(year)
gen treated = (country >4)&!missing(country)
diff y, t(treated) p(time)
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222110159541.png" alt="image-20231222110159541" style="zoom:60%;" />

一般来说，如果政策执行时间**不提前得到的回归结果是显著的**，而**提前得到的结果是不显著的**话，就能说明“政策效应”是真实存在的，因果推断更让人信服，结果更加稳健。在这里提前得到的结果p值=0.069<0.1，在10%水平下显著，“政策效应”不一定存在。

##### **双重差分法代码的整理**

```stata
*==========================================*
*                DID关键变量的构建           *
*==========================================*
gen time = (时间变量 >= 政策采取时间)&!missing(时间变量)
// 政策发生时间的虚拟变量构建
gen treated = 自定控制变量组限制表达式&!missing(面板变量)
// 政策发生时间的虚拟变量构建
gen did = time*treated
// 构建DID估计量，即时间和空间的交互项
*==========================================*
*               DID模型效应的估计            *
*==========================================*
* 推荐使用第三个
reg 被解释变量 did time treated,r // 一般的DID模型（稳健标准误）
reg 被解释变量 time##treated,r // 无需设置交互项，结果是一样的
diff 被解释变量, t(treated) p(time) // 使用外部命令估计DID
*==========================================*
*                 平行趋势检验               *
*==========================================*
gen Dyear = 年份变量-1994
gen 前第n期 = (Dyear==-n&treated==1)
gen 前第二期 = (Dyear==-2&treated==1)
gen 前第一期 = (Dyear==-1&treated==1)
gen 当期 = (Dyear==0&treated==1)
gen 后第一期 = (Dyear==1&treated==1)
gen 后第二期 = (Dyear==2&treated==1)
gen 后第m期 = (Dyear==n&treated==1)
// 或者
gen Dyear = 年份变量-1994
forvalues i = n(-1)1{  
	gen pre_`i' = (Dyear == -`i' & treated == 1) 
}  
gen current = (Dyear == 0 & treated == 1)  
forvalues j = 1(1)m{  
	gen time_`j' = (Dyear == `j' & treated == 1)  
}
// 将以上交互项作为解释变量进行回归
xtreg y time treated 前第n期 前第二期 前第一期 当期 后第一期 后第二期 后第m期 i.年份变量, fe
// 进行双向固定效应回归，观察结果分析即平行趋势检验检验
est sto reg
coefplot reg,keep(第n期 前第二期 前第一期 当期 后第一期 后第二期 后第m期) vertical recast(connect) yline(0) 
// 用外部命令绘制多期动态效应图
*==========================================*
*                  安慰剂检验               *
*==========================================*
* 法一
// 注意！外部命令！
cap erase "原dta数据集名"
permute did beta = _b[did] se = _se[did] df = e(df_r),reps(500) seed(123) ///
saving("更改后dta数据集名"): reghdfe 被解释变量 did, absorb(面板变量 时间变量) vce(robust)
use "更改后dta数据集名",clear
gen t = beta / se
gen p = 2* ttail(df,abs(beta/se))
dpplot beta, xtitle("估计值", size(*0.8)) xlabel(, format(%4.3f) labsize(small)) ///
ytitle("P值", size(*0.8)) ylabel(, nogrid format(%4.3f) labsize(small)) ///
caption("") graphregion(fcolor(white))
* 法二
use "原dta数据集名", clear
gen time = (时间变量 >= 政策采取时间的前一年)&!missing(时间变量)
gen treated = 自定控制变量组限制表达式&!missing(面板变量)
diff 被解释变量, t(treated) p(time) // 要与之前的diff结果进行比较
```

##### 5.3.2.2 三重差分法（DDD）

#### 5.3.3 倾向得分匹配模型（PSM）

##### 5.3.3.1 一般倾向得分匹配模型（PSM）

##### 5.3.3.2 结合双重差分法的倾向得分匹配模型（PSM-DID）

#### 5.3.4 合成控制法（SCM）

```stata
synth 被解释变量 解释变量1 解释变量2 解释变量3 解释变量4 解释变量5 解释变量6 ///
	y(观测起始期) y(事件发生期) y(观测结束期), ///
	trunit(实验组样本id)trperiod(事件发生期) xperiod(观测起始期(1)观测结束期) ///
	figure nested keep(保存模型文件，其中包含效应值)
```

### 5.4 混合效应模型（MEM）

#### 5.4.1 多层线性模型（HLM/MLM）

所谓“分层”就是通过多个线性模型，将**数据的高低/大小/内外关系**展现出来。举个栗子，如果我们想使用CEPS中国教育追踪调查数据，研究影响学生学业成绩的因素，由于CEPS包含了来自学生、班级、学校不同层面的各种调查内容，我们会很自然地想使用OLS回归建立一个关于学业成绩的回归方程，这个方程将纳入**学生、班级、学校等层面的控制变量**，但这样做可能存在一个问题：**同一个班级学生学业成绩的相关度可能比不同班级学生学业成绩的相关度大，因为他们身处同样的班级教育环境；同一个学校学生学业成绩的相关度可能比不同学校学生学业成绩的相关度大，因为院校可能在入学时对学生进行了筛选，且同一个学校奉行同样的办学理念等**。虽然使用OLS回归时可以使用cluster(group)进行分组减少偏差，但面对**更多的层级和更为复杂的影响因素（尤其是在不同层面上影响因素不同）**时，使用**HLM分层线性模型**就展现出了优越性。

- **典型数据结构辨识**

有很多的**可分组**的变量，尤其是一些客观条件、环境上**容易带来显著异质性的变量**，作为**控制变量**。比如下图想要考察努力程度与绩点的关系，控制变量有宿舍、班级和年级。

<img src="D:\wechat\WeChat Files\wxid_kmz6nsf4osqv22\FileStorage\Temp\b907c339445c627402456066ec711c2.png" alt="b907c339445c627402456066ec711c2" style="zoom:80%;" />

- **简述与使用**

**一般程序**

>1.建立零模型，判断是否要使用HLM。
>
>2.建立随机截距和随机截距-斜率模型。
>
>3.根据IC选择模型。

**建立零模型和比较模型**

```stata
mixed 被解释变量 || 上层分组变量:,mle variance nostderr // 零模型
mixed 被解释变量, mle variance nostderr // OLS模型
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222200553752.png" alt="image-20231222200553752" style="zoom:50%;" />

观察零模型语句后输出结果的最后一行，LR test vs. linear regression的卡方检验p值为0.0000<0.01，显著，推荐使用HLM。

**建立随机截距模型**

```stata
mixed 被解释变量 解释变量 || 上层分组变量:, mle variance nostderr // 随机截距
estat ic // 使用AIC和BIC评价选择模型
mixed 被解释变量 解释变量1 解释变量2 控制变量1 控制变量2 || 上层分组变量:, mle variance nostderr // 带控制变量的随机截距
estat ic
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222202225379.png" alt="image-20231222202225379" style="zoom:50%;" />

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222203216928.png" alt="image-20231222203216928" style="zoom:67%;" />

**每次跑一个模型出来之后就跑一下这张表，比较各个模型AIC的大小，AIC越小的越好。**

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222202204317.png" alt="image-20231222202204317" style="zoom:50%;" />

**建立随机斜率-截距模型**

```stata
mixed 被解释变量 解释变量1 解释变量2 控制变量1 控制变量2 || 上层分组变量: 解释变量1, covariance(unstructured) mle variance nostderr nolog // x1随机斜率+截距
estat ic
mixed 被解释变量 解释变量1 解释变量2 控制变量1 控制变量2 || 上层分组变量: 解释变量2, covariance(unstructured) mle variance nostderr nolog // x2随机斜率+截距
estat ic
mixed 被解释变量 解释变量1 解释变量2 控制变量1 控制变量2 || 上层分组变量: 解释变量1 解释变量2, covariance(unstructured) mle variance nostderr nolog // x1、x2随机斜率+截距
estat ic
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222201824007.png" alt="image-20231222201824007" style="zoom: 40%;" />

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222202012907.png" alt="image-20231222202012907" style="zoom:40%;" />

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222201938775.png" alt="image-20231222201938775" style="zoom:40%;" />

**更多层的随机斜率-截距模型**

```stata
mixed 被解释变量 解释变量1 解释变量2 控制变量1 控制变量2 || 上层分组变量: || 上上层分组变量: , covariance(unstructured) mle variance nostderr nolog 
mixed 被解释变量 解释变量1 解释变量2 控制变量1 控制变量2 || 上层分组变量: 解释变量1 || 上上层分组变量: 解释变量2 , covariance(unstructured) mle variance nostderr nolog 
```

**多层线性模型需要你对数据结构的了解，以便找出最能反映层次的变量、最受层次影响的变量，将其进行组合，在优化模型合理性的情况下，进行模型的性能的性能评估和选取。**

#### **多层线性模型代码的整理**

```stata
* 评估
mixed 被解释变量 || 上层分组变量:,mle variance nostderr // 零模型
mixed 被解释变量, mle variance nostderr // OLS模型
* 随机截距
mixed 被解释变量 解释变量 || 上层分组变量:, mle variance nostderr // 随机截距
estat ic // 使用AIC和BIC评价选择模型
mixed 被解释变量 解释变量1 解释变量2 控制变量1 控制变量2 || 上层分组变量:, mle variance nostderr // 带控制变量的随机截距
estat ic
* 随机斜率-截距
mixed 被解释变量 解释变量1 解释变量2 控制变量1 控制变量2 || 上层分组变量: 解释变量1, covariance(unstructured) mle variance nostderr nolog // x1随机斜率+截距
estat ic
mixed 被解释变量 解释变量1 解释变量2 控制变量1 控制变量2 || 上层分组变量: 解释变量2, covariance(unstructured) mle variance nostderr nolog // x2随机斜率+截距
estat ic
mixed 被解释变量 解释变量1 解释变量2 控制变量1 控制变量2 || 上层分组变量: 解释变量1 解释变量2, covariance(unstructured) mle variance nostderr nolog // x1、x2随机斜率+截距
estat ic
// 下面别管
mixed 被解释变量 解释变量1 解释变量2 控制变量1 控制变量2 || 上层分组变量: || 上上层分组变量: , covariance(unstructured) mle variance nostderr nolog 
mixed 被解释变量 解释变量1 解释变量2 控制变量1 控制变量2 || 上层分组变量: 解释变量1 || 上上层分组变量: 解释变量2 , covariance(unstructured) mle variance nostderr nolog 
```

### 5.5 事件研究法（ESM）

### 5.6 事件史分析法（EHA）

#### 5.6.1 单种类事件的事件史分析

- **典型数据结构辨识**

适用事件史分析法的数据往往是时间序列数据或者面板数据。典型特征是**存在事件变量（离散型变量：用0-1或0-n进行指示）**，且该变量的发生取值（如1、2、3等）只会出现一次（简单理解就是**每个个体的记录下的事件变量只会出现一个1**）。

<img src="D:\wechat\WeChat Files\wxid_kmz6nsf4osqv22\FileStorage\Temp\13971f871c8092d44cf20bffe3ace0a.png" alt="13971f871c8092d44cf20bffe3ace0a" style="zoom: 67%;" />

以上的结构存在每条记录的起点时间和时间变量，在宏观经济的角度可以把它理解为一段时间内（一年内）的一条统计记录，尤其注意失效时间指示变量，每个id往往最多只会出现一个1。

或者

<img src="D:\wechat\WeChat Files\wxid_kmz6nsf4osqv22\FileStorage\Temp\3597a828f278814b1d6229d1c02b39d.png" alt="3597a828f278814b1d6229d1c02b39d" style="zoom: 67%;" />

一组数据还可以不存在可视的起点时间和时间变量，而用转化后的一个**持续时间变量**进行表示，持续时间变量在单个体内是累积的。（表中的仅为示意，不具有实际价值）

- **简述与使用**

>**常见流程**
>
>- **第一步** 声明生存分析数据
>- **第二步** 进行数据描述、画生存函数、累积风险函数和风险函数
>- **第三步** 进行参数回归及非参数回归
>- **第四步** 比例风险假定的检验

**事件史数据的删失**

**事件史数据的声明与描述**

若数据结构是第一种（找不到持续时间变量），则使用下方代码进行声明。

```stata
stset 时间变量, origin(time 起点时间) id(面板变量) failure(失效事件指示变量 == 1)
```

若数据结构是第二种（可得持续时间变量），则使用下方代码进行声明。

```stata
stset 持续时间变量, failure(失效事件指示变量 == 1) id(面板变量)
```

stata会自动生成四个新变量： _t0:观察窗口的起始时间（相对时间） _t:观察窗口的结束时间（相对时间） _d:归并虚拟变量（失效1，归并0） _st:有效观测值虚拟变量（与当前分析有关的观测值=1,其他=0) 。stata的后续计算将基于这四个变量，而忽略数据中原有的时间变量。**如果在分析中途改变原始数据，需要再次运行stset命令**，以更新上述4个值。

```stata
stdes
stsum // 事件史数据描述性统计
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222153002641.png" alt="image-20231222153002641" style="zoom:60%;" />

上方stdes结果依次为个体数、记录数、**起点时间、失效时间**；个体间隔、时间间隔、**事件发生的累积时间**。一般报告粗体字即可。

stsum结果为事件发生的累积时间统计。

```stata
sts graph // 生存函数图
sts graph, failure // 风险函数图
sts graph, cumhaz // 累积风险函数图
sts graph, ci // 带95%置信区间的生存函数图
sts graph, failure ci // 带95%置信区间的风险函数图
sts graph, cumhaz ci // 带95%置信区间的累积风险函数图
* 注意下方的变量只能使用定类变量（0、1、2、3等离散数据）
sts graph, ci by(变量名) // 带95%置信区间的按变量分组的生存函数图
sts graph, failure ci by(变量名) // 带95%置信区间的按变量分组的风险函数图
sts graph, cumhaz ci by(变量名) // 带95%置信区间的按变量分组的累积风险函数图
* 参见下图
sts graph, ci by(变量名) atrisk xlabel(最小值(尺度)最大值) ylabel(最小值(尺度)最大值) legend(cols(2)) ///
xtitle("x轴的标签") ytitle("y轴的标签") title("这里是标题") subtitle("这里是副标题") ///
caption("这里是注释") note("这里可以写数据来源")
```

更多选项见stata中的生存函数绘图（sts graph）菜单。

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222154027010.png" alt="image-20231222154027010" style="zoom:50%;" />

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222154301084.png" alt="image-20231222154301084" style="zoom:50%;" />

由此图可以得出，unfrmaj=1时，事件发生的风险普遍要大于unfrmaj=0时。

我们可以进一步对这种差异进行检验。

```stata
sts test 分组变量名 // LR检验
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222160235842.png" alt="image-20231222160235842" style="zoom:50%;" />

在LR检验中，P=0.0336<0.05，因此我们拒绝零假设(两个组别没有显著性差别)。因此得出结论，unfrmaj=0的情况能够显著提高生存率（不让事件发生），unfrmaj=1的情况能够显著降低生存率（让事件发生）。

关于模型选择，我们需要讨论事件与时间是离散的还是连续的。

>**离散事件**：事件是离散型的：是/否，存在/不存在（0-1）
>
>**离散时间**：等尺度的样本更新：每年、每月、每季度等，各个体间的样本更新尺度一致。
>
>**连续时间**：不等尺度的样本更新。

在单种类离散事件的情况下，主要讨论以下几个模型。离散时间常用**离散时间 Logit 模型（DTLM）**，连续时间常用**加速时间失效模型 （AFT）**、**比例风险模型（PH）**、**Cox 比例风险模型（Cox PH）**。

**离散时间 Logit 模型 （DTLM）**

```stata
logistic 被解释变量 解释变量1 解释变量2 解释变量3, vce(robust)
logistic 被解释变量 解释变量1 解释变量2 解释变量3 年份变量 , vce(robust)
logistic 被解释变量 解释变量1 解释变量2 解释变量3 c.年份变量#c.年份变量 , vce(robust)
// 逐步考虑时间效应，若模型二效果好则选二，模型三效果好则选三，以显著性为标准。
```

<img src="D:\wechat\WeChat Files\wxid_kmz6nsf4osqv22\FileStorage\Temp\233aa8e616251ffe795c85e2272e039.png" alt="233aa8e616251ffe795c85e2272e039" style="zoom:50%;" />

据上图所示，在不考虑时间效应的情况下，所有变量所对应的p值都=0.000<0.01，在1%的水平下显著，且都具有正效应，其中第三个变量会导致时间发生的概率提升20%。

<img src="D:\wechat\WeChat Files\wxid_kmz6nsf4osqv22\FileStorage\Temp\4385a46cef478037aba224e9405b251.png" alt="4385a46cef478037aba224e9405b251" style="zoom:50%;" />

据上图所示，在考虑时间效应的情况下，所有变量所对应的p值依然都=0.000<0.01，在1%的水平下显著，且都具有正效应，时间效应也显著。

<img src="D:\wechat\WeChat Files\wxid_kmz6nsf4osqv22\FileStorage\Temp\38501f98df928903569c7603e72982f.png" alt="38501f98df928903569c7603e72982f" style="zoom:50%;" />

据上图所示，在考虑时间交互效应的情况下，所有变量所对应的p值依然都=0.000<0.01，在1%的水平下显著，且都具有正效应，时间交互效应也显著。我们可以通过观察模型结果的不同对模型进行合理的选择。若有时间效应，则优先选择考虑时间效应的模型。

**加速时间失效模型（AFT）**

```stata
streg 解释变量1 解释变量2 解释变量3,  nolog dist(logn)
stcurve, hazard
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222162026395.png" alt="image-20231222162026395" style="zoom:50%;" />

据上图显示，所有变量都不显著。

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222165625359.png" alt="image-20231222165625359" style="zoom:50%;" />

**比例风险模型（PH）**

```stata
streg 解释变量1 解释变量2 解释变量3, nohr nolog dist(weib) // 优先，若/ln_p显著则不用运行下面的模型
streg 解释变量1 解释变量2 解释变量3, nohr nolog dist(e)
streg 解释变量1 解释变量2 解释变量3, nohr nolog dist(gom)
* dist(weib) 代表威布尔回归的风险函数, 
* dist(e) 为指数回归，dist(gom) 为刚珀茨回归，
* nohr 显示回归系数而不显示风险比率
stcurve, hazard
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222161951305.png" alt="image-20231222161951305" style="zoom:50%;" />

据上图显示，所有变量都不显著。 倒数第 3 行 ln_p 的 p 值为 0.138, 不能拒绝指数回归的原假设, 认为应该使用指数回归。倒数第 2 行的 p 值大于 1, 说明风险函数随时间而递增。

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222164547247.png" alt="image-20231222164547247" style="zoom:50%;" />

进行指数回归，依然不显著。

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222164630750.png" alt="image-20231222164630750" style="zoom:50%;" />

依然不显著，考虑Cox PH模型。

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222165657959.png" alt="image-20231222165657959" style="zoom:50%;" />

**Cox 比例风险模型（Cox PH）**

```stata
stcox 解释变量1 解释变量2 解释变量3,r nohr nolog
stcurve, hazard
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222162133460.png" alt="image-20231222162133460" style="zoom:50%;" />

从上图看出，Cox PH模型回归结果中protect变量p值为0.000<0.01，在1%的水平下显著呈负效应，说明穿戴防护装置会导致老年人骨折的风险降低 41.94%，在10%的水平下年龄上升会导致骨折风险的增加，血钙的增加会降低骨折风险，但不显著。 且Cox PH不依赖于具体的分布假设，故结果更稳健。

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222165729188.png" alt="image-20231222165729188" style="zoom:50%;" />

- **比例风险检验（针对Cox PH模型）**

**对数图**

```stata
stphplot, by(分组变量)
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222170242685.png" alt="image-20231222170242685" style="zoom:50%;" />

若两条曲线大致是平行的，则满足比例风险假定。

**预测图**

```stata
stcoxkm, by(分组变量)
```

**舍恩尔德残差图**

```stata
qui stcox 分组变量 其他变量1 其他变量2,r nohr nolog
estat phtest, detail
estat phtest, plot(分组变量) // 画图
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222170648762.png" alt="image-20231222170648762" style="zoom:70%;" />

三个解释变量都没有拒绝原舍恩尔德残差对时间回归的斜率为 0 的原假设, 故支持比例风险假定。

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222170727715.png" alt="image-20231222170727715" style="zoom:50%;" />

从残差与时间的拟合图来看其斜率大致为 0， 满足比例风险假定。

- **比例风险问题的解决**

**分层Cox PH模型**

```stata
stcox 解释变量1 解释变量2 解释变量3, strata(分组变量) r nohr nolog
```

将不满足比例风险假设的变量按照其取值水平分组

**引入随时间变化的解释变量**

```stata
stcox 解释变量1 解释变量2 解释变量3 时间变量 r nohr nolog
```

**模型的选取（Logit还是Cox PH？）**

结果差不多，显著性也相同

关键看AIC和BIC

统计分析的方便：离散性时间模型，logit模型，但是需要考虑潜在时间相关性问题。

对风险率的兴趣：如果不感兴趣但需要加以控制，选择Cox模型；但是要满足比例风险假定。

#### **单种类事件的事件史分析代码的整理**

```stata
*==========================================*
*                 数据声明                  *
*==========================================*
stset 时间变量, origin(time 起点时间) id(面板变量) failure(失效事件指示变量 == 1)
stset 持续时间变量, failure(失效事件指示变量 == 1) id(面板变量)
*==========================================*
*                 描述性统计                 *
*==========================================*
stdes
stsum // 事件史数据描述性统计
sts graph // 生存函数图
sts graph, failure // 风险函数图
sts graph, cumhaz // 累积风险函数图
sts graph, ci // 带95%置信区间的生存函数图
sts graph, failure ci // 带95%置信区间的风险函数图
sts graph, cumhaz ci // 带95%置信区间的累积风险函数图
* 注意下方的变量只能使用定类变量（0、1、2、3等离散数据）
sts graph, ci by(变量名) // 带95%置信区间的按变量分组的生存函数图
sts graph, failure ci by(变量名) // 带95%置信区间的按变量分组的风险函数图
sts graph, cumhaz ci by(变量名) // 带95%置信区间的按变量分组的累积风险函数图
* 参见下图
sts graph, ci by(变量名) atrisk xlabel(最小值(尺度)最大值) ylabel(最小值(尺度)最大值) legend(cols(2)) ///
xtitle("x轴的标签") ytitle("y轴的标签") title("这里是标题") subtitle("这里是副标题") ///
caption("这里是注释") note("这里可以写数据来源")
sts test 分组变量名 // LR检验
*==========================================*
*              离散时间生存分析               *
*==========================================*
logistic 被解释变量 解释变量1 解释变量2 解释变量3, vce(robust)
logistic 被解释变量 解释变量1 解释变量2 解释变量3 年份变量 , vce(robust)
logistic 被解释变量 解释变量1 解释变量2 解释变量3 c.年份变量#c.年份变量 , vce(robust)
// 逐步考虑时间效应，若模型二效果好则选二，模型三效果好则选三，以显著性为标准。
*==========================================*
*              连续时间生存分析               *
*==========================================*
streg 解释变量1 解释变量2 解释变量3,  nolog dist(logn)
stcurve, hazard
streg 解释变量1 解释变量2 解释变量3, nohr nolog dist(weib) // 优先，若/ln_p显著则不用运行下面的模型
streg 解释变量1 解释变量2 解释变量3, nohr nolog dist(e)
streg 解释变量1 解释变量2 解释变量3, nohr nolog dist(gom)
* dist(weib) 代表威布尔回归的风险函数, 
* dist(e) 为指数回归，dist(gom) 为刚珀茨回归，
* nohr 显示回归系数而不显示风险比率
stcurve, hazard
stcox 解释变量1 解释变量2 解释变量3,r nohr nolog
stcurve, hazard
*==========================================*
*               比例风险检验                 *
*==========================================*
stphplot, by(分组变量)
stcoxkm, by(分组变量)
qui stcox 分组变量 其他变量1 其他变量2,r nohr nolog
estat phtest, detail
estat phtest, plot(分组变量) // 画图
*==========================================*
*                 问题解决                  *
*==========================================*
stcox 解释变量1 解释变量2 解释变量3, strata(分组变量) r nohr nolog
stcox 解释变量1 解释变量2 解释变量3 时间变量 r nohr nolog
```

#### 5.6.2 多种类事件的事件史分析

### 5.7 变量的选取

模型比较与控制变量的选取。

```stata
* 导入数据
use 示例数据.dta, clear
* 定义全局控制变量ControlVariable，后面使用 $ControlVariable引用
global ControlVariable Size Lev Growth Agency CF TANG Balance  COMPEN Age i.Industry i.year
* OLS回归
xi: reg Y X  $ControlVariable , robust
est store res_1
* 固定效应回归
xi: xtreg Y X  $ControlVariable , fe robust
est store res_2
* 2SLS
xi: ivreg2 Y  $ControlVariable (X=IV), robust  
est store res_3
* Tobit回归
xi: tobit Y X  $ControlVariable ,  vce(robust) ll(0)
est store res_4
* 输出回归结果
esttab res_*, mtitle("OLS回归" "固定效应回归" "2SLS回归" "Tobit回归")nogap replace star(* 0.1 ** 0.05 *** 0.01) b(3) t(3) indicate("Industry=*Industry*"  "Year=*year*") ar2   
```

## 6 使用模型进行估计

### 6.1 初步估计

#### 6.1.1 模型效果评估



#### 6.1.2 数据特征检验与改善

##### 6.1.2.1 异方差问题检验

经典线性回归模型的一个重要假定：总体回归函数中的随机误差项满足同方差性，即它们都有相同的方差。如果这一假定不满足，即：随机误差项具有不同的方差，则称线性回归模型存在异方差性。由下图所示，我们理想的方差分布应当是左上的，其他三种情况都属于异方差。

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231221225103562.png" alt="image-20231221225103562" style="zoom:33%;" />

- **画残差图**

```stata
// 使用以下的估计需要先运行线性回归
reg 被解释变量 解释变量1 解释变量2 解释变量3
rvfplot , yline(0) xline(0) // 总体情况
rvpplot 需要的解释变量 // 将实际使用中需要的解释变量画图进行异方差查看（而不是控制变量）
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231221225330394.png" alt="image-20231221225330394" style="zoom:50%;" />

上图可以看出明显的异方差性。

- **B-P检验**

```stata
// 只有一个解释变量时
estat hettest 解释变量
// 有多个解释变量时，iid为独立同分布
estat hettest 解释变量1 解释变量2，iid rhs
```

![image-20231221225552534](C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231221225552534.png)

统计量Prob>卡方对应的p值为0.0000<0.05，拒绝原假设，**认为存在异方差**。

- **White检验（最常用）**

```stata
estat imtest,white
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231221230022505.png" alt="image-20231221230022505" style="zoom:60%;" />

只需要看上半部分，IM-test不关心。Prob>卡方对应的p值为0.0000<0.05，拒绝原假设，**认为存在异方差**。

##### 6.1.2.2 异方差问题改善

- **最小二乘法（OLS）+异方差稳健标准误**

```stata
reg 被解释变量 解释变量1 解释变量2 解释变量3, robust // 加robust即可
```

- **加权最小二乘法（WLS）**

WLS 的主要目的是在普通最小二乘法（OLS）的基础上解决**异方差问题**。当线性回归模型中的误差方差随着自变量的变化而变化时，OLS 估计可能是有偏的且不是最佳的线性无偏估计。在这种情况下，WLS 可以提供更准确、更有效的估计。

```stata
quietly regress 被解释变量 解释变量1 解释变量2  // 做安静回归，即做回归但不显示回归结果
predict e,residuals      // 提取回归模型中的残差并命名为e
gen r2 = e^2          // 增加新变量残差的平方
gen 解释变量12 = 解释变量1^2   
gen 解释变量22 = 解释变量2^2        // 增加解释变量X1 X2的平方
regress r2 解释变量12 解释变量22     #得出残差平方和与解释变量X1 X2的回归关系
predict ee,residuals    
gen rr2 = ee^2          #提取回归结果中的残差并进行平方和
#以上代码是对权重的计算，得到权重后，进行回归。
reg 被解释变量 解释变量1 解释变量2[aw = 1/rr2] 
```

**采用此方法须再次检验新模型是否已经不存在异方差，如果还存在异方差，则须再进行异方差处理，直到模型不存在异方差为止。**

##### 6.1.2.3 自相关问题检验

经典线性回归模型的一个重要假定：随机误差项是不相关的。如果不满足该条件则称随机误差项之间存在自相关现象。注意这里的自相关不是值属性/特征/自变量之间具有相关关系，而是指同一个变量的前后数值之间存在相关。

**不同于异方差问题，一般自相关问题往往出现在时间序列数据中，且在面板数据中出现的异方差、自相关问题往往直接运用聚类标准误即可解决。**

- **画残差图**

```stata
tsset 年份变量 // 若是时间序列数据则运行这行
xtset 面板变量 时间变量 // 若是面板数据则运行这行
reg 被解释变量 解释变量1 解释变量2 解释变量3
predict e1,residuals
gen Le1=L.e1
twoway (scatter e1 Le1)(lfit e1 Le1)
// 看残差图
ac e1
// 看残差自相关图
pac e1
// 看残差偏自相关图
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222012849202.png" alt="image-20231222012849202" style="zoom:50%;" />

按上图显示**斜率为正**则可判定可能存在一定自相关 。

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222013042258.png" alt="image-20231222013042258" style="zoom:50%;" />

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222013101756.png" alt="image-20231222013101756" style="zoom:50%;" />

在此节，只需看竖线哪一根超出了灰色范围，在上例中，可以明显看出偏自相关和自相关的第一根超出了灰色范围，所以可以在一定范围内判定存在**一阶自相关**。

- **BG检验**

```stata
estat bgodfrey
```



<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222013316233.png" alt="image-20231222013316233" style="zoom:67%;" />

观察右下角的p值为0.0018<0.05，原假设为“无自相关”，则否认原假设，这组数据存在自相关。下面的检验也是同理。

- **Q检验**

```stata
wntestq e1
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222013511086.png" alt="image-20231222013511086" style="zoom:80%;" />

观察右下角的p值为0.0046<0.05，原假设为“无自相关”，则否认原假设，这组数据存在自相关。

##### 6.1.2.4 自相关问题改善

- **最小二乘法（OLS）+聚类稳健标准误**

```stata
reg 被解释变量 解释变量1 解释变量2 解释变量3, cluster 聚类组别
```

- **HAC标准误**

```stata
newey 被解释变量 解释变量1 解释变量2 解释变量3,lag(#) // 延后期数为上面图上所观测到的
```

- **处理一阶自相关的 FGLS**

```stata
prais 被解释变量 解释变量1 解释变量2 解释变量3
// 使用默认的 PW 估计法
prais 被解释变量 解释变量1 解释变量2 解释变量3,corc
// 使用 CO 估计法
```

##### 6.1.2.4 多重共线性问题检验

经典线性回归模型的一个重要假定：回归模型的解释变量之间不存在线性关系，也就是说，解释变量中的任何一个都不能是其他解释变量的线性组合。如果违背这一假定，即线性回归模型中**某一个解释变量与其他解释变量间存在线性关系**，就称线性回归模型中存在多重共线性。

> 面板数据**报告一张相关系数矩阵**即可，若是出现强的多重共线性，stata会直接提示omit，直接忽略某一变量进行估计。
>
> 只有完全多重共线性的时候会自动省略。对于不完全多重共线性，**如果回归结果显著，可不进行多重共线性检验**，这是因为多重共线性的后果是增大方差，降低显著性。在结果显著的情况下，就算是有多重共线性，只能说明没有多重共线性的时候更加显著。不显著的情况下，才需要看是否是多重共线性导致的结果不显著。
>
> (1) 如果不关心具体的回归系数，只关心整个方程的预测能力， 则通常可不必理会多重共线性。多重共线性的主要后果是使得对 单个变量的贡献估计不准，但所有变量的整体效应仍可准确估计。
> (2) 如果关心具体的回归系数，但多重共线性并不影响所关心变量的显著性，也可不必理会。即使在有方差膨胀的情况下，这些系数依然显著；如果没有多重共线性，只会更加显著。
> (3) 如果多重共线性影响到所关心变量的显著性，则需要增大样本容量，剔除导致严重共线性的变量，或对模型设定进行修改。

- **画图观察法**

```stata
graph matrix 被解释变量 解释变量1 解释变量2 解释变量3 ,half
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222015435581.png" alt="image-20231222015435581" style="zoom:50%;" />

在上图如果看出两个变量之间存在较强的线性相关，则可以判断可能会产生多重共线性问题。

- **方差膨胀系数（VIF）**

在统计中, 我们常用**方差膨胀系数 (variance inflation factor)** 来衡量多元线性回归模型中多重共线性 (multicollinearity) 的严重程度，VIF表示回归系数估计量的方差与假设自变量间不线性相关时方差相比的比值。

```stata
reg 被解释变量 解释变量1 解释变量2 解释变量3
estat vif
// 或
vif
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222014704772.png" alt="image-20231222014704772" style="zoom:80%;" />

门槛值为10，没有一个变量的VIF值超过10，不存在多重共线性。为了判断哪几个变量之间存在多重共线性, 这里将会使用相关性矩阵来进一步判断。

```stata
pwcorr_a 被解释变量 解释变量1 解释变量2 解释变量3
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222014942195.png" alt="image-20231222014942195" style="zoom:67%;" />

在这里，我们看见了自变量工业二氧化硫排放量吨与工业烟尘排放量吨之间存在一定的相关相关 (相关系数为0.627)。如果有两个变量对实际上在测量同一件事情，要是把这两个变量都考虑进模型中, 可能会加重多重共线性问题，影响模型的精确性。

##### 6.1.2.5 多重共线性问题改善

- **逐步回归**

```stata
reg 被解释变量 解释变量1 解释变量3
estat vif
reg 被解释变量 解释变量1 解释变量2 
estat vif
reg 被解释变量 解释变量2 解释变量3
estat vif
reg 被解释变量 解释变量1 解释变量2 解释变量3 解释变量4
estat vif
reg 被解释变量 解释变量1 解释变量2 解释变量3 解释变量4 解释变量5
estat vif
```

- **建立虚拟变量**

若是有些变量之间存在多重共线性，但是由于涵义不同难以直接剔除，此时就可以结合两个变量生成有含义/没有含义的虚拟变量进行回归。

```stata
reg 被解释变量 解释变量1 解释变量2 解释变量3
gen 解释变量12 = 解释变量1/解释变量2
reg 被解释变量 解释变量12 解释变量3
```

##### **常用三大检验代码的整理**

```stata
*==========================================*
*                 直接处理                  *
*==========================================*
// 如果什么检验都不打算做，可以直接采取稳健标准误（一般数据）或聚类稳健标准误（面板数据）进行估计。
reg 被解释变量 解释变量1 解释变量2 解释变量3, robust 
reg 被解释变量 解释变量1 解释变量2 解释变量3, cluster 聚类组别
*==========================================*
*                异方差问题                  *
*==========================================*
// 使用以下的估计需要先运行线性回归
reg 被解释变量 解释变量1 解释变量2 解释变量3
*/----------/*
*   问题检验
*/----------/*
rvfplot , yline(0) xline(0) // 总体残差图，先做这个
rvpplot 需要的解释变量 // 各变量残差图，再做这个
// 有一个解释变量情况 BP检验
estat hettest 解释变量 
// 有多个解释变量情况 BP检验
estat hettest 解释变量1 解释变量2，iid rhs
// white检验（用这个）
estat imtest,white
*/----------/*
*   问题改善
*/----------/*
reg 被解释变量 解释变量1 解释变量2 解释变量3, robust // 稳健标准误，加robust即可，用这个
// WLS
qui reg 被解释变量 解释变量1 解释变量2 
predict e,residuals      // 提取回归模型中的残差并命名为e
gen r2 = e^2          // 增加新变量残差的平方
gen 解释变量12 = 解释变量1^2   
gen 解释变量22 = 解释变量2^2        // 增加解释变量X1 X2的平方
regress r2 解释变量12 解释变量22     #得出残差平方和与解释变量X1 X2的回归关系
predict ee,residuals    
gen rr2 = ee^2          #提取回归结果中的残差并进行平方和
#以上代码是对权重的计算，得到权重后，进行回归。
reg 被解释变量 解释变量1 解释变量2[aw = 1/rr2] 
*==========================================*
*               自相关问题                   *
*==========================================*
tsset 年份变量 // 若是时间序列数据则运行这行
xtset 面板变量 时间变量 // 若是面板数据则运行这行
reg 被解释变量 解释变量1 解释变量2 解释变量3
predict e1,residuals
gen Le1=L.e1
twoway (scatter e1 Le1)(lfit e1 Le1) // 看残差图
ac e1 // 看残差自相关图
pac e1 // 看残差偏自相关图
estat bgodfrey // BG检验
wntestq e1 // Q检验
*/----------/*
*   问题改善
*/----------/*
reg 被解释变量 解释变量1 解释变量2 解释变量3, cluster 聚类组别 // 聚类稳健标准误，用这个
newey 被解释变量 解释变量1 解释变量2 解释变量3,lag(#) // 延后期数为上面图上所观测到
prais 被解释变量 解释变量1 解释变量2 解释变量3 // 使用默认的 PW 估计法
prais 被解释变量 解释变量1 解释变量2 解释变量3,corc // 使用 CO 估计法
*==========================================*
*              多重共线性问题                 *
*==========================================*
graph matrix 被解释变量 解释变量1 解释变量2 解释变量3 ,half // 画图
reg 被解释变量 解释变量1 解释变量2 解释变量3
estat vif 
pwcorr_a 被解释变量 解释变量1 解释变量2 解释变量3
*/----------/*
*   问题改善
*/----------/*
reg 被解释变量 解释变量1 解释变量3
estat vif
reg 被解释变量 解释变量1 解释变量2 
estat vif
reg 被解释变量 解释变量2 解释变量3
estat vif
reg 被解释变量 解释变量1 解释变量2 解释变量3 解释变量4
estat vif
reg 被解释变量 解释变量1 解释变量2 解释变量3 解释变量4 解释变量5
estat vif
reg 被解释变量 解释变量1 解释变量2 解释变量3
gen 解释变量12 = 解释变量1/解释变量2
reg 被解释变量 解释变量12 解释变量3
```

##### 6.1.2.6 内生性问题的检验

内生性问题，是指模型中的**一个或多个解释变量与误差项存在相关关系**，内生性会破坏参数估计的“一致性”。参数估计的“一致性”就是指当样本量很大时，用样本估计出的参数会无限趋近于总体的真实参数。当用样本估计出的参数没有了一致性，那它也就没什么参考价值了。

>重要概念：内生变量、外生变量
>
>内生变量是指在一个经济模型中，其取值不是独立确定的，而是由模型内部其他变量的取值所决定的变量。换句话说，内生变量的变化是由模型内部因果关系所决定的。
>
>外生变量则是指在一个经济模型中，其取值是独立确定的，不受模型内部其他变量的影响。
>
>再补充一下其他变量的吧，怕不清楚。
>
>1.解释变量（或自变量）：解释变量是指作为研究对象，用于解释某个现象或行为模式的变量。其中有些解释变量是直接影响被解释变量的，有些则是间接或中介影响的。在回归分析中，解释变量通常被放在方程的右边。
>
>2.被解释变量（或因变量）：被解释变量是指通过解释变量来解释其变化产生的影响的变量，也可以称为因变量。在回归分析中，被解释变量通常被放在方程的左边。
>
>3.控制变量（或干扰变量）：控制变量是指在控制所研究的解释变量和被解释变量之间的关系时，需要控制的可能具有影响的变量。例如，我们希望研究教育对收入的影响，但同时需要将一些其他因素（例如性别、年龄、工作经验等）视为控制变量。通过对这些变量进行控制，可以更准确地估计教育对收入的影响。

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222021903631.png" alt="image-20231222021903631" style="zoom:50%;" />

由回归结果可知，多排放一万吨废水可以增长1289.178万元的GDP。从回归模型中可以推测，随机干扰项u中含有其他影响GDP的因素，比如一些更加环保的因素。从而产生遗漏变量问题，进而违反了经典OLS假设，怀疑有内生性问题。

```stata
ovtest 
linktest
* 可做可不做
```


##### 6.1.2.6 内生性问题的改善

- **工具变量法（IV）**

工具变量方法通过引入一个或多个外生性的变量（工具）来解决内生性问题。这些工具变量必须满足一定的条件，例如**与内生变量相关**，但**与误差项不相关**。通过使用工具变量，可以得到更准确和一致的估计结果。

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222022844415.png" alt="image-20231222022844415" style="zoom:80%;" />

通过图示可知：x对y有显著影响，ε和x有显著关系，而**X**对y也有显著关系，而**X**并不在回归模型之中，包含在了u之中。如果**X**与x有显著相关关系，就会导致内生性问题，这是由遗漏变量导致的。而Z与**X**不相关，Z与μ不相关，但与x有显著相关关系，因此**可以用Z来作为工具变量来进行衡量**。

- **工具变量的两阶段最小二乘回归**

```
ivreg 被解释变量 (内生变量 = 外生工具变量1 外生工具变量2),first
```

拟选定普通中学学校数所、小学学校数所两个变量作为工具变量来拟合教育支出万元对地区生产总值万元的影响。利用两阶段回归来探讨。

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222023202800.png" alt="image-20231222023202800" style="zoom:50%;" />

可以发现教育支出万元与普通中学学校数所、小学学校数所是存在极显著的相关关系的，因此满足第一个适用条件。 再结合二阶段回归结果可知道普通中学学校数所、小学学校数所作为工具变量发现能够极为地显著拟合教育支出万元对地区生产总值万元的影响。但是还没有检验educ的内生性和两个工具变量的外生性。接下来就需要对这两个问题进行检验。分别是豪斯曼检验和过度识别约束检验。

- **工具变量的豪斯曼检验**

**判断怀疑的内生变量是否为内生变量的检验方法**

```
qui reg 被解释变量 内生变量 外生工具变量1 外生工具变量2
predict e,residuals
reg 被解释变量 内生变量 e
test e=0
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222023631672.png" alt="image-20231222023631672" style="zoom:67%;" />

因此拒绝原假设，认为残差的系数不等于0，即原模型中内生变量和随机扰动项μ显著相关。

- **过度识别约束检验**

**判断两个工具变量是否是同期外生变量**

```
qui ivreg 被解释变量 (内生变量 = 外生工具变量1 外生工具变量2)
predict e1,residuals
reg e1 外生工具变量1 外生工具变量2
test 外生工具变量1=外生工具变量2=0
```

<img src="C:\Users\Larrt\AppData\Roaming\Typora\typora-user-images\image-20231222023929493.png" alt="image-20231222023929493" style="zoom:67%;" />

因此拒绝原假设，认为外生工具变量1和外生工具变量2系数不能同时为0，两者不是同期外生性变量。不满足相关假设，反之满足。

### 6.2 估计结果分析









## 7 模型稳健性检验







## 8 Stata的编程式应用

### 循环

- **foreach**

```
```

- **forvalues**

```

```



## 9 Stata绘图







## 参考文献

