# 面板数据模型（PDM）

## 目录

[TOC]

## 1 简述与使用

面板数据是既有个体变量又有时间变量的数据。从维度来看，时间序列数据和截面数据均为一维。面板数据可以看做为**时间序列与截面混合数据**，是**截面上个体在不同时点重复观测数据，**因此它是二维数据。比如一个数据集有100家公司五年内的数据，总共100×5=500条数据，则该数据集是面板数据。

要使用面板数据，需要先对stata声明该数据集是面板数据，声明代码如下：

```stata
xtset 面板变量 时间变量
```

<img src="https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312261737735.png" alt="image-20231221211320214" style="zoom:100%;" />

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

## 2 平稳性检验（一般不用做）

在使用面板数据前，按照正规程序，在回归前需检验数据的**平稳性**。《计量经济学 第三版》李子奈 著 pp.274指出，一些非平稳的“经济时间序列”往往表现出共同的变化趋势，而这些序列间本身不一定有直接的关联，此时，对这些数据进行回归，尽管有较高的R平方，但其结果是没有任何实际意义的。这种情况称为称为**虚假回归或伪回归（spurious regression）**。因此为了避免伪回归，确保估计结果的有效性，我们必须对“各面板序列”的平稳性进行检验。

| **检验方法**   | **基本假设**                                       |
| -------------- | -------------------------------------------------- |
| **LLC**        | **假设该序列是截面不相关、同质的面板数据（平衡）** |
| **Breintung**  | **假设该序列是截面不相关、同质的面板数据（平衡）** |
| **IPS**        | **假设该序列是截面不相关、异质的面板数据**         |
| **ADF-Fisher** | **假设该序列是截面不相关、异质的面板数据**         |
| **PP-Fisher**  | **假设该序列是截面不相关、异质的面板数据**         |

其中，最常用的是**LLC与IPS**检验。

### 2.1 长面板LLC检验

**该检验适用于长面板（时间数>>个体数）！**

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

<img src="https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312261737813.png" alt="image-20231221222628962" style="zoom:50%;" />

由检验结果可知，调整后**t统计量对应的P值为0.0258>0.05**，拒绝原假设，该变量的所有面板是稳定的，在得出稳定结果后，后续的检验都没有必要做了，同样是看t统计量对应的P值即可。

```
xtline 被检验变量, overlay
```

<img src="https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312230150663.png" alt="image-20231221221002629" style="zoom:50%;" />

画图辅助分析，观察各时间序列的数据变化趋势，如果能看出较为明显的趋势，我们就把它当成是平稳的。

**在上述检验方法中，只要有一项是平稳的，我们都把它当成是平稳的。**

### 2.2 短面板IPS检验

适用于短面板，实际上短面板并不需要做单位根检验

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

<img src="https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312261737132.png" alt="image-20231223143630224" style="zoom: 67%;" />

由检验结果可知，**t-bar统计量为-1.7146，大于1%显著性水平的临界值-2.450**，所以不能拒绝面板单位根的原假设（即面板存在单位根）。此外，统计量**Z-t-tilder-bar对应的P值为0.1206>0.05**，同样不能拒绝原假设。

<img src="https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312261737065.png" alt="image-20231221214616389" style="zoom:50%;" />

由检验结果可知，**t-bar统计量为-1.0527，大于1%显著性水平的临界值**，所以不能拒绝面板单位根的原假设（即面板存在单位根）。此外，统计量**Z-t-tilder-bar对应的P值为0.9982>0.05**，同样不能拒绝原假设。

下面，我们考虑扰动项存在自相关的情形，并引入差分滞后项。

<img src="https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312261737003.png" alt="image-20231221220836864" style="zoom:50%;" />

由检验结果可知，统计量**Z-t-tilder-bar对应的P值为0.0358<0.05**，表示在平均**滞后1.67期**时，可以拒绝原假设，一些面板是平稳的。那么按照检验的结果，我们就需要将该变量滞后约1-2期，若不考虑滞后，则应进一步做**面板协整**检验。

```
xtline 被检验变量, overlay
```

<img src="https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312230150663.png" alt="image-20231221221002629" style="zoom:50%;" />

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

## 3 协整检验（一般不用做）

如果发现面板数据中的每个时间序列都是单位根过程（**如果变量都拒绝了单位根存在的原假设，说明数据都是平稳的0阶单整，无需再进行协整检验**），则应进一步做**面板协整**检验（panel cointegration tests），考察变量之间是否存在长期均衡的协整关系。协整检验是数据不平稳但是同阶单整的前提下，检验变量X与变量y之间是否存在长期均衡关系。

对于有单位根的变量，传统的处理方法是进行**一阶差分**而得到平稳序列。 但一阶差分后变量的经济含义与原序列并不相同，而有时我们仍然希望**使用原序列**进行回归。 如果多个单位根变量之间由于某种经济力量而存在“长期均衡关系”(long-run equilibrium)，则有可能**使用原序列**进行回归。

### 3.1 Kao检验

 ```stata
xtcointtest kao 被解释变量 解释变量1 解释变量2 解释变量3, demean 
/*Kao检验假定同期截面不相关，demean是截距项，为了减轻截面相关对协整检验的影响*/
 ```

<img src="https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312261737491.png" alt="image-20231221223108748" style="zoom:50%;" />

上表汇报了 5 种不同的检验统计量，我们主要关注**前三种**：MDF、DF、ADF，其对应的 p 值均小于 0.05，故可在 5% 水平上拒绝 “不存在协整关系” 的原假设，认为存在协整关系。

### 3.2 Pedroni 检验

```stata
xtcointtest pedroni 被解释变量 解释变量1 解释变量2 解释变量3, trend demean ar(panels)
xtcointtest pedroni 被解释变量 解释变量1 解释变量2 解释变量3, demean ar(panels)
xtcointtest pedroni 被解释变量 解释变量1 解释变量2 解释变量3, noconstant demean ar(panels)
*(1)三个方程：含个体固定效应项和时间趋势项、仅含个体固定效应项和两者均不含的检验
*(2)ar(panels)意为该检验在异质面板数据的情况下进行；ar(same)意为该检验在同质面板数据的情况下进行，面板数据一般都是异质的
```

<img src="https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312261737886.png" alt="image-20231223143701078" style="zoom:50%;" />

上表汇报了 3 种不同的检验统计量，其中MP-t对应的 p 值大于 0.05，不能拒绝 “不存在协整关系” 的原假设，认为不存在协整关系。

<img src="https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312261737232.png" alt="image-20231221224123687" style="zoom:50%;" />

调整参数后，上表汇报了 3 种不同的检验统计量，其对应的 p 值均小于 0.05，故可在 5% 水平上拒绝 “不存在协整关系” 的原假设，认为存在协整关系。

同样地，**在上述检验方法中，只要有一项是平稳的，我们都把它当成是平稳的**。

## 4 多重共线性检验（一般不用做，默认有，加robust）

经典线性回归模型的一个重要假定：回归模型的解释变量之间不存在线性关系，也就是说，解释变量中的任何一个都不能是其他解释变量的线性组合。如果违背这一假定，即线性回归模型中**某一个解释变量与其他解释变量间存在线性关系**，就称线性回归模型中存在多重共线性。

> 面板数据**报告一张相关系数矩阵**即可，若是出现强的多重共线性，stata会直接提示omit，直接忽略某一变量进行估计。
>
> 只有完全多重共线性的时候会自动省略。对于不完全多重共线性，**如果回归结果显著，可不进行多重共线性检验**，这是因为多重共线性的后果是增大方差，降低显著性。在结果显著的情况下，就算是有多重共线性，只能说明没有多重共线性的时候更加显著。不显著的情况下，才需要看是否是多重共线性导致的结果不显著。
>
> (1) 如果不关心具体的回归系数，只关心整个方程的预测能力， 则通常可不必理会多重共线性。多重共线性的主要后果是使得对 单个变量的贡献估计不准，但所有变量的整体效应仍可准确估计。
> (2) 如果关心具体的回归系数，但多重共线性并不影响所关心变量的显著性，也可不必理会。即使在有方差膨胀的情况下，这些系数依然显著；如果没有多重共线性，只会更加显著。
> (3) 如果多重共线性影响到所关心变量的显著性，则需要增大样本容量，剔除导致严重共线性的变量，或对模型设定进行修改。

### 4.1 画图观察法

```stata
graph matrix 被解释变量 解释变量1 解释变量2 解释变量3 ,half
```

<img src="https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312261744747.png" alt="image-20231223144326767" style="zoom:50%;" />

在上图如果看出两个变量之间存在较强的线性相关，则可以判断可能会产生多重共线性问题。

### 4.2 方差膨胀系数（VIF）

在统计中, 我们常用**方差膨胀系数 (variance inflation factor)** 来衡量多元线性回归模型中多重共线性 (multicollinearity) 的严重程度，VIF表示回归系数估计量的方差与假设自变量间不线性相关时方差相比的比值。

```stata
reg 被解释变量 解释变量1 解释变量2 解释变量3
estat vif
// 或
vif
```

<img src="https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312261744525.png" alt="image-20231222014704772" style="zoom:80%;" />

门槛值为10，没有一个变量的VIF值超过10，不存在多重共线性。为了判断哪几个变量之间存在多重共线性, 这里将会使用相关性矩阵来进一步判断。

```stata
pwcorr_a 被解释变量 解释变量1 解释变量2 解释变量3
```

<img src="https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312261744595.png" alt="image-20231222014942195" style="zoom:67%;" />

在这里，我们看见了自变量工业二氧化硫排放量吨与工业烟尘排放量吨之间存在一定的相关相关 (相关系数为0.627)。如果有两个变量对实际上在测量同一件事情，要是把这两个变量都考虑进模型中, 可能会加重多重共线性问题，影响模型的精确性。

### 4.3 多重共线性问题改善

- **逐步回归**

```stata
xtreg 被解释变量 解释变量1 解释变量3
estat vif
xtreg 被解释变量 解释变量1 解释变量2 
estat vif
xtreg 被解释变量 解释变量2 解释变量3
estat vif
xtreg 被解释变量 解释变量1 解释变量2 解释变量3 解释变量4
estat vif
xtreg 被解释变量 解释变量1 解释变量2 解释变量3 解释变量4 解释变量5
estat vif
```

- **建立虚拟变量**

若是有些变量之间存在多重共线性，但是由于涵义不同难以直接剔除，此时就可以结合两个变量生成有含义/没有含义的虚拟变量进行回归。

```stata
reg 被解释变量 解释变量1 解释变量2 解释变量3
gen 解释变量12 = 解释变量1/解释变量2
reg 被解释变量 解释变量12 解释变量3
```

**上述改善方法视具体情况选取，也可以不改善，因为Stata面板模型会自动剔除强多重共线性的变量。**

## 5 异方差检验（一般不用做，默认有，加robust）

经典线性回归模型的一个重要假定：总体回归函数中的随机误差项满足同方差性，即它们都有相同的方差。如果这一假定不满足，即：随机误差项具有不同的方差，则称线性回归模型存在异方差性。由下图所示，我们理想的方差分布应当是左上的，其他三种情况都属于异方差。

<img src="https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312230150410.png" alt="image-20231221225103562" style="zoom:33%;" />

### 5.1 画残差图

```stata
// 使用以下的估计需要先运行线性回归
reg 被解释变量 解释变量1 解释变量2 解释变量3
rvfplot , yline(0) xline(0) // 总体情况
rvpplot 需要的解释变量 // 将实际使用中需要的解释变量画图进行异方差查看（而不是控制变量）
```

<img src="https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312230150740.png" alt="image-20231221225330394" style="zoom:50%;" />

上图可以看出明显的异方差性。

### 5.2 面板数据的组间异方差检验

面板数据的异方差检验直接采用xttest3即可。

```stata
xtreg 被解释变量 解释变量1 解释变量2 ,fe
xttest3 // 面板模型估计后使用
```

![image-20231226180247241](https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312261802286.png)

上图中p值=0.000<0.01，在1%的显著性水平上认为存在异方差。

### 5.2 B-P检验

```stata
// 只有一个解释变量时
estat hettest 解释变量
// 有多个解释变量时，iid为独立同分布
estat hettest 解释变量1 解释变量2，iid rhs
```

![image-20231221225552534](https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312230150099.png)

统计量Prob>卡方对应的p值为0.0000<0.05，拒绝原假设，**认为存在异方差**。

### 5.3 White检验

```stata
estat imtest,white
```

<img src="https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312231442142.png" alt="image-20231223144255046" style="zoom:67%;" />

只需要看上半部分，IM-test不关心。Prob>卡方对应的p值为0.0000<0.05，拒绝原假设，**认为存在异方差**。

### 5.4 改善方法

- **最小二乘法（OLS）+异方差稳健标准误**

```stata
reg 被解释变量 解释变量1 解释变量2 解释变量3, robust // 加robust即可
* 所有的模型后面都加, robust 或者, r即可。
* 随机效应模型不用加，因为随机效应模型本身也是一种改善异方差的方法。
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

## 6 自相关检验（一般不用做，默认有，加robust）

### 6.1 序列相关检验

```stata
xtserial 被解释变量 解释变量1 解释变量2
```

![image-20231226190226678](https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312261902732.png)

p<0.05，认为存在序列相关。

### 6.2 截面相关（组内同期相关）检验

##### 6.2.1 长面板截面相关

```stata
xtreg 被解释变量 解释变量1 解释变量2 , fe
xttest2 //xttest2只能在运行xtreg, fe 或 xtgls 或 ivreg2 之后用，且仅适用于长面板（T大，N小）
```

<img src="https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312261858338.png" alt="image-20231226185840275" style="zoom:50%;" />

p<0.05，认为存在组内同期相关。

##### 6.2.2 短面板截面相关

```stata
xtreg 被解释变量 解释变量1 解释变量2 , fe //只适用于平衡面板
xtcsd, pes //假设统计量服从标准正态分布
xtcsd, fri //假设统计量服从卡方分布
```

![image-20231226190156620](https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312261901665.png)

p<0.05，认为存在组内同期相关。

## 5 模型的估计

### 5.1 混合模型（POOL）

混合模型的特点是无论对任何个体或者截面，回归系数都是相同的。即不分组的全局OLS回归。不同个体之间不存在差异，不同时间项之间也不存在显著性差异，可以直接把面板数据混合在一起用普通最小二乘法（OLS）估计参数。

```stata
reg 被解释变量 解释变量1 解释变量2 解释变量3
```

<img src="https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312261737772.png" alt="image-20231221232544560" style="zoom:50%;" />

### 5.2 固定效应模型：个体效应、时间效应、双向效应（FE-TE，FE-FE，FE-ME）

#### 5.2.1 个体固定效应模型

**个体固定效应模型是对于不同的时间序列（个体）只有截距项不同的模型**

从时间和个体上看，面板数据回归模型的解释变量对被解释变量的边际影响均是相同的，而目除模型的解释变量之外，影响被解释变量的其他所有（未包括在回归模型或不可观测的）确定性变量的效应只是随个体变化而不随时间变化。

##### 5.2.1.1 个体固定效应模型的估计（一般方法）

```stata
xtreg 被解释变量 解释变量1 解释变量2 解释变量3, fe
// 或者 
xtreg 被解释变量 解释变量1 解释变量2 解释变量3, fe i(id)
// 或者
reghdfe 被解释变量 解释变量1 解释变量2 解释变量3, absorb(面板变量) vce(cluster 面板变量)
```

<img src="https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312230150852.png" alt="image-20231221235111510" style="zoom:50%;" />

针对个体固定效应（原假设：不存在个体固定效应）的F检验自动生成（最后一行），此处F检验对应的p值为0.0000，表示个体固定效应显著。　

##### 5.2.1.1 个体固定效应模型的估计（LSDV方法）

```stata
xi:reg 被解释变量 解释变量1 解释变量2 解释变量3 i.面板变量,fe 
```

<img src="https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312261737066.png" alt="image-20231221234131818" style="zoom:50%;" />

观测个体固定效应是否大多数都显著（p<0.05），若是，则说明个体固定效应显著，个体固定效应模型效果好。

**个体固定效应就是个体层面不随时间变化的影响因素，每个个体都独特的个体特征，其目的是为了控制个体层面独一无二的不随时间变化的特征，因此分析个体固定效应，除了个体特征说明、模型比较以外，没有其他的意义。**

#### 5.2.2 时间固定效应模型

时间固定效应模型就是对于不同的截面（时点）有不同截距的模型。如果确知对于不同的截面，模型的截距显著不同，但是对于不同的时间序列（个体）截距是相同的，那么应该建立时点固定效应模型。

##### 5.2.2.1 时间固定效应模型的估计（一般方法）

```stata
xtreg 被解释变量 解释变量1 解释变量2 解释变量3 ,fe i(year)
reghdfe 被解释变量 解释变量1 解释变量2 解释变量3, absorb(年份变量) vce(cluster 面板变量)
```

<img src="https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312261737670.png" alt="image-20231221235313172" style="zoom:50%;" />

针对时间固定效应（原假设：不存在时间固定效应）的F检验自动生成（最后一行），此处F检验对应的p值为0.0000，表示时间固定效应显著。　

##### 5.2.2.2 时间固定效应模型的估计（LSDV方法）

```stata
xi:reg 被解释变量 解释变量1 解释变量2 解释变量3 i.时间变量
test 观测的时间虚拟变量1 观测的时间虚拟变量2 观测的时间虚拟变量3 观测的时间虚拟变量4 观测的时间虚拟变量5 （以上模型生成的）........
```

<img src="https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312261737457.png" alt="image-20231222000159777" style="zoom:50%;" />

上图是时间固定效应模型的估计结果。

![image-20231222000259103](https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312261737295.png)

用Wald检验检验时间固定效应，上图中检验结果对应的p值为0.0000，表示时间固定效应显著。　

**时间固定效应就是时间层面不随个体变化的影响因素，每个年份都有独特的年份特征，其目的是为了控制时间层面独一无二的不随个体变化的特征，比如每年的宏观经济。除了时序特征说明、模型比较以外，没有其他的意义。**

#### 5.2.3 双向固定效应模型

双向固定效应模型就是对于不同的截面（时点）、不同的时间序列（个体）都有不同截距的模型。如果确知对于不同的截面、不同的时间序列（个体）模型的截距都显著不相同，那么应该建立时点个体固定效应模型。

##### 5.2.3.1 双向固定效应模型的估计（一般方法）

```stata
xtreg 被解释变量 解释变量1 解释变量2 解释变量3 i.时间变量,fe
// 或
reghdfe 被解释变量 解释变量1 解释变量2 解释变量3 ,absorb(时间变量 面板变量) cluster()
```

<img src="https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312261828820.png" alt="image-20231226182839703" style="zoom:50%;" />

估计结果如上图所示，下方的p值为0.000<0.05，说明个体固定效应显著。若要检验时间固定效应共同验证双向固定效应的话，需要运行以下代码：

```stata
testparm i.时间变量
```

<img src="https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312261830835.png" alt="image-20231226183048790" style="zoom:50%;" />

上图中p值0.13>0.05，说明时间效应不显著，双向效应不显著。

##### 5.2.3.2 双向固定效应模型的估计（交叉项法）

重新生成一个panel varible比如code，此code是id和year的综合，可以克服传统方法的局限性（“independent variables are collinear with the panel variable year”）。

```stata
gen code = 年份变量+面板变量
xtset code 年份变量
xi:xtreg 被解释变量 解释变量1 解释变量2 解释变量3 i.code,fe
// 记得恢复原面板
xtset 面板变量 年份变量
```

<img src="https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312231437401.png" alt="image-20231223143746333" style="zoom:50%;" />

针对双向固定效应（原假设：不存在双向固定效应）的F检验自动生成（最后一行），此处F检验对应的p值为0.4483>0.05，表示不存在双向固定效应。　

##### 5.2.3.2 双向固定效应模型的估计（LSDV法）

```stata
xi:reg 被解释变量 解释变量1 解释变量2 解释变量3 i.时间变量,fe 
```

<img src="https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312261737109.png" alt="image-20231222001229846" style="zoom:50%;" />

针对双向固定效应（原假设：不存在双向固定效应）的F检验自动生成（最后一行），此处F检验对应的p值为0.0000>0.05，表示双向固定效应显著。　

>最后在三种模型中到底选择哪个，主要根据**F检验值是否显著**进行判断，第一个显著后面不显著就选个体固定效应模型，第二个显著其他不显著选择时间固定效应模型，第三个显著意味着前两个均显著，那么选择个体时间双固定模型。

### 5.3 随机效应模型（RE）

随机效应模型与固定效应模型FE的区别在于**对个体差别的定义**，**固定效应模型刻画了不同个体的特殊影响，个体间的差别反映在每个个体都有各自截距项；而随机效应模型则假设个体间的差别是随机的**。由此固定效应模型更适合用于研究样本之间的区别（异质性），而随机效应更适合用于由样本来推断总体特征。

#### 5.3.1 随机效应模型的估计

```stata
xtreg 被解释变量 解释变量1 解释变量2 解释变量3 ,re 
```

<img src="https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312261737903.png" alt="image-20231222001411102" style="zoom:50%;" />

上图为随机效应估计结果。

#### 5.3.2 随机效应检验

```stata
xttest0
```

<img src="https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312261808904.png" alt="image-20231226180849860" style="zoom:50%;" />

上图中p值为0.000<0.05，说明随机效应显著。

## 6 模型的选择

### 6.1 豪斯曼（Hausman）检验

```stata
xtreg 被解释变量 解释变量1 解释变量2 解释变量3, fe
est store FE
xtreg 被解释变量 解释变量1 解释变量2 解释变量3, re
est store RE
hausman FE RE
```

<img src="https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312261844849.png" alt="image-20231226184444799" style="zoom:50%;" />

原假设是随机效应和固定效应无差异，上图中p值为0.8508>0.05，**接受原假设，采用随机效应模型**，否则固定效应模型。　

### 6.2 基于bootstrap法的豪斯曼（Hausman）检验

若模型存在序列相关和异方差，经典的命令将不再适用，需要使用基于bootstrap法的Hausman检验。

```stata
xtreg 被解释变量 解释变量1 解释变量2 解释变量3, fe
est store FE
xtreg 被解释变量 解释变量1 解释变量2 解释变量3, re
est store RE
rhausman FE RE,reps(200) cluster
```

<img src="https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312261845700.png" alt="image-20231226184543647" style="zoom:50%;" />

原假设是随机效应和固定效应无差异，上图中p值为0.8508>0.05，**接受原假设，采用随机效应模型**，否则固定效应模型。　

# **面板数据模型代码的整理**

```stata
*==========================================*
*              面板数据的声明与处理           *
*==========================================*
xtset 面板变量 时间变量
xtbalance, range(观测首期年份 观测末期年份)
*==========================================*
*                模型估计                   *
*==========================================*
* 个体固定效应
xtreg 被解释变量 解释变量1 解释变量2 解释变量3, fe robust
// 或者 
xtreg 被解释变量 解释变量1 解释变量2 解释变量3, fe i(id) robust
// 或者
reghdfe 被解释变量 解释变量1 解释变量2 解释变量3, absorb(面板变量) robust
// 或者
xi:reg 被解释变量 解释变量1 解释变量2 解释变量3 i.面板变量,fe robust
* 时间固定效应
xtreg 被解释变量 解释变量1 解释变量2 解释变量3 ,fe i(year) robust
// 或者
reghdfe 被解释变量 解释变量1 解释变量2 解释变量3, absorb(年份变量) robust
// 或者
xi:reg 被解释变量 解释变量1 解释变量2 解释变量3 i.时间变量, robust
test 观测的时间虚拟变量1 观测的时间虚拟变量2 观测的时间虚拟变量3 观测的时间虚拟变量4 观测的时间虚拟变量5 （以上模型生成的）........
* 双向固定效应
xtreg 被解释变量 解释变量1 解释变量2 解释变量3 i.时间变量,fe robust
testparm i.时间变量
// 或
reghdfe 被解释变量 解释变量1 解释变量2 解释变量3 ,absorb(时间变量 面板变量) robust
// 或者
gen code = 年份变量+面板变量
xtset code 年份变量
xi:xtreg 被解释变量 解释变量1 解释变量2 解释变量3 i.code,fe robust
// 记得恢复原面板
xtset 面板变量 年份变量
// 或者
xi:reg 被解释变量 解释变量1 解释变量2 解释变量3 i.时间变量,fe robust
* 随机效应
xtreg 被解释变量 解释变量1 解释变量2 解释变量3 ,re robust
xttest0
*==========================================*
*                模型选择                   *
*==========================================*
* 豪斯曼检验判断用固定还是随机
xtreg 被解释变量 解释变量1 解释变量2 解释变量3, fe
est store FE
xtreg 被解释变量 解释变量1 解释变量2 解释变量3, re
est store RE
hausman FE RE 
// 或者
xtreg 被解释变量 解释变量1 解释变量2 解释变量3, fe
est store FE
xtreg 被解释变量 解释变量1 解释变量2 解释变量3, re
est store RE
rhausman FE RE,reps(200) cluster
```