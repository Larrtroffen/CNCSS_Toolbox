# 结构方程模型（SEM）

结构方程模型在一定意义上其实就是组合回归，是路径分析和银子分析的有机结合。一方面，结构方程模型能够处理多个因变量，同时允许自变量和因变量为潜变量，可以含有测量误差；另一方面，它也能够帮我们对模型和数据的整体拟合做出评价。

## 1 天哪。

我们使用`db sem`指令打开结构方程模型构建器。

在这里我们最常使用的工具如下图所示，分别为路径和测量组件。

<img src="https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312271639218.png" alt="97341974de52d045efdb6b5f88b0d6a" style="zoom:50%;" />

点击测量组件，在空白的地方点击一下。

<img src="https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312271641486.png" alt="5b681d387202f1c2775c0121d1f7de1" style="zoom:50%;" />

点一下确定，就出来了。

<img src="https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312271642697.png" alt="image-20231227164236547" style="zoom: 33%;" />

如果你需要观测一个变量对另一个变量的影响，就点选其中一个变量，拖到另一个变量上，就可以了。

<img src="https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312271646987.png" alt="image-20231227164653922" style="zoom:50%;" />

然后点击上方的估计，Stata就会自动帮你生成代码，**不要纠结代码我求求你们了**。

## 2 模型的效果评估

### 2.1 模型基本指标的报告 

```stata
estat gof, stats(all)
```

<img src="https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312271648629.png" alt="image-20231227164848589" style="zoom:50%;" />

从表格中可以看出，当前模型的RMSEA值为0.081，CFI为0.946，SRMR为0.045。其中，RMSEA的值我们不是特别满意，**所以我们要对模型进行一些调整。每一次调整都需要重新尝试上述步骤**，直到跑出满意的结果（或者放弃）。

```stata
estat eqgof
```

![image-20231227170755245](C:/Users/Larrt/AppData/Roaming/Typora/typora-user-images/image-20231227170755245.png)

0.95，挺好的。

> **评价指标**
>
> RMSEA<0.06（好）或0.08（更好）
> CFI>0.9（好）或0.95（更好）
> SRMR<0.05
>
> ![image-20231227165222749](https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312271652857.png)

### 2.2 怎么筛选模型

回到这玩意，我们可以多加一些测量组件，然后再跑一次`estat gof, stats(all)`，再比较一下上面的指标，就行。

![image-20231227165318265](https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312271653331.png)

### 2.3 因子分析——信效度检验

这玩意是独立的，你们就这样写：

```stata
alpha 因子1 因子2 因子3, item
*科隆巴赫alpha系数是一种常用的内部一致性检验方法，用于评估问卷调查或测验的信度。它通过计算测量工具中各项之间的相关性来评估测量工具的一致性，即测量工具是否能够稳定地测量同一概念。科隆巴赫alpha系数的取值范围在0到1之间，数值越高表示测量工具的一致性越好。一般来说，大于0.7的科隆巴赫alpha系数被认为是可接受的，表明测量工具具有较高的内部一致性。
```

![image-20231227170415284](https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312271704319.png)

这里科隆巴赫alpha系数=0.8724>0.7，说明测量工具具有较高的内部一致性，量表可信度较高。

```stata
ssc install relicoef
relicoef
*雷科夫因子可靠性系数是一种用于评估测量工具（例如问卷调查）内部一致性的统计量。它衡量了测量工具中各个项目之间的相关性，以确定该工具是否能够稳定地测量特定的概念或变量。这个系数的值通常介于0和1之间，越接近1表示测量工具的内部一致性越高。
```

![image-20231227170102772](https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312271701800.png)

```stata
ssc install condisc
condisc
*在CFA中，通常会使用一些统计指标来评估收敛和鉴别效度。其中，收敛效度通常通过计算平均方差抽取（AVE）来进行评估，通常希望AVE的值大于0.5。鉴别效度通常通过计算相关性系数来进行评估，如果测量工具与其他不同概念的相关性系数较低，则说明具有良好的鉴别效度。一般来说，相关性系数小于0.7可以被认为是具有良好的鉴别效度。这些典型的判断值可以帮助研究者确定他们所使用的测量工具在收敛和鉴别效度方面的表现。
```

![image-20231227171018566](C:/Users/Larrt/AppData/Roaming/Typora/typora-user-images/image-20231227171018566.png)

AVE只要大于零点五或者不报告的话就是效度很可以。

## 3 模型的效应报告

```stata
estat teffects
```

![image-20231227165318265](https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/202312271653331.png)

我们回到上面这幅图，可以看见这里考察**感知质量对顾客满意度的直接效应，和感知质量通过感知价值对顾客满意度的间接效应**。

我们找到**direct effects**这张表，看到下面Structural，找到感知质量（perq），可以看到感知质量（perq）对顾客满意度（cusa）、感知质量（perq）对感知价值（perv）、感知价值（perv）对顾客满意度（cusa）的直接效应，我们报告p值和系数正负即可。

![image-20231227171246566](https://raw.githubusercontent.com/Larrtroffen/Stata_Guidebook/main/pic/image-20231227171246566.png)

在这里，cusa为头的表示顾客满意度（cusa）是箭头的终点；perv为头的表示感知价值（perv）是箭头的终点。

![image-20231227171313616](C:/Users/Larrt/AppData/Roaming/Typora/typora-user-images/image-20231227171313616.png)

进一步找到indirect effects这张表，看到下面Structural，可以看到感知质量（perq）对顾客满意度（cusa）的中介效应，同样报告p值和系数正负即可。

![image-20231227171713957](C:/Users/Larrt/AppData/Roaming/Typora/typora-user-images/image-20231227171713957.png)

最后是总效应，按照自己的需求报告即可。

![image-20231227171808371](C:/Users/Larrt/AppData/Roaming/Typora/typora-user-images/image-20231227171808371.png)

# 模型代码整理

```stata
// 打开
db sem
// 评估
estat gof, stats(all)
estat eqgof
// 信度
alpha 因子1 因子2 因子3, item
relicoef
// 效度
condisc
// 效应
estat teffects
```

