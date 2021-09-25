# 临床基线数据表统计

## 0. 你不要管了



## 1. 常见统计学分析方法

![image-20210723144345617](D:/software/Typora/image/image-20210723144345617.png)



## 2. 临床信息的使用

##### 2.1 变量名+总人群（通常为两列）

目的：疾病的预后风险因素评估，不涉及到比较分析

关键点：连续变量是正态数据还是分正态数据，如果为正态数据：均值±标准差；如果为非正态数据：中位数±上下四分位数

例如下面例子：（研究某类型乳腺癌的预后）

![image-20210813171834751](D:/software/Typora/image/image-20210813171834751.png)

参考文献：Lewis GD，et al. Prognosis of lymphotropic invasive micropapillary breast carcinoma analyzed by using data from the National Cancer Database. Cancer Commun (Lond). 2019 Oct 21;39(1):60.



##### 2.2 变量+某变量分列+p值（通常为4列）

目的：变量对预后的比较分析

关键点：通常使用该变量将全部人群分为几类，比较其他变量在这几个分类中是否有差异（及显著性p值）；往往涉及到连续变量和分类变量的相关性检验

例如，研究两种病理类型的乳腺癌临床病理特点

![image-20210813172514056](D:/software/Typora/image/image-20210813172514056.png)

参考文献：Yu JI,et al. Differences in prognostic factors and patterns of failure between invasive micropapillary carcinoma and invasive ductal carcinoma of the breast: matched case-control study. Breast. 2010 Jun;19(3):231-7.

##### 2.5 变量名+总数+某变量分组+p值（通常为5列）

目的：两种病理类型的乳腺癌临床病理特点的比较

关键点：基于某一变量的两种病理类型的临床病理特点的比较

![image-20210813173050244](D:/software/Typora/image/image-20210813173050244.png)

参考文献：Hashmi AA, et al. Clinicopathologic features of invasive metaplastic and micropapillary breast carcinoma: comparison with invasive ductal carcinoma of breast. BMC Res Notes. 2018 Jul 31;11(1):531.

## 3. 来吧，咱们分析吧

总体思路：【指定需要汇报的变量;指定这些变量中的分类变量；指定哪些连续变量是非正态分布】-----构建函数-----输出表格

##### 3.1 安装R包、数据放入工作目录

```
#1.加载R包
#install.packages("tableone")
library(tableone)
#2.清理运行环境
rm(list = ls()) 
#3.读入数据
aa<- read.csv('20210126.csv')
```

##### 3.2 查看数据构成

```
 #4.查看数据前6行 
 head(aa)
```

**源数据尽量用英文单词或缩写而非数值代替。因软件默认识别为连续变量，需要转换，很麻烦。用英文在整理表格时也方便，省时省力。**

![image-20210814093634435](D:/software/Typora/image/image-20210814093634435.png)

```
 #5.查看数据数据性质
 str(aa)
```

数据有5000病例；16变量，2连续变量（年龄和时间）；14分类变量及它们亚变量的个数和名字。

![image-20210814093839686](D:/software/Typora/image/image-20210814093839686.png)

```
 #6.提取变量的名字 
 names(aa)
```

age=年龄，age70=以70岁为界分组的年龄，race=种族，marry=婚姻，t=t分期，n=n分期，tnm=tnm分期，er=雌激素受体，pr=孕激素受体，her2，g=组织分级，sur=手术，rt=放疗，che=化疗，status=死亡否，time=时间

![image-20210814094014412](D:/software/Typora/image/image-20210814094014412.png)



##### 3.3 连续变量正态性检验

```
 shapiro.test(aa$age)#p＞0.05才符合正态分布 
 shapiro.test(aa$time)#p＞0.05才符合正态分布
```

age和time SW检验p<0.05, 不符合正态性分布，**Table1应汇报中位数+四分位数**

![image-20210814094147989](D:/software/Typora/image/image-20210814094147989.png)

![图片](https://mmbiz.qpic.cn/mmbiz_png/lYDNMgmFjur6mfkPywZNgKZYXia5C4lA8Kd6CUYOibay3HbIfxBnNXH3v6jQZf6QquSL5dt6qYMrtQGh7go67FZg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



##### 3.5 构建表格及统计分析

######  3.5.1 n × 2 列表

- 定义想要在Table中出现的变量

```R
 myVars <- c("age","age70"，"race", "marry",
 	"t", "n",  "tnm", "er", "pr",
 	"g","her2", "sur","rt","che",
 	"time","status")#16个变量
```



- 定义分类变量

```R
 catVars <- c("age70"，"race", "marry",
 	"t", "n",  "tnm", "er", "pr", 
 	"g","her2", "sur","rt","che",
 	,"status")#14个分类变量
```

- 定义数据分布：正态分布 或者 非正态分布
  - tableone包默认数据是正态分布，输出均数+标准差，
  - ’nonnormal‘ 参数定义那些连续变量是非正态分布的（输出中位数+四分位数）

```R
nonvar <- c("time","age")  # 指定哪些变量是非正态分布变量
```

   

- 构建Table 对象

```R
 table<- CreateTableOne(vars = myVars,       #条件1
 	factorVars = catVars, #条件2
 	data = aa,  #源数据
 	addOverall = TRUE)  #增加overall列
```



- 输出结果

```R
  table1 <- print(
  	table,  #构建的table函数（包括条件1.2）
  	showAllLevels=TRUE, #显示所有变量
  	nonnormal = nonvar) #条件3
```

age和time均汇报为中位数+四分位数，分类变量均汇报为数目加百分数

![image-20210814100425079](D:/software/Typora/image/image-20210814100425079.png)

- 保存至Excel

```R
 write.csv(table1, file = "table1.csv")
```



###### 3.5.2 n × 4 列表

>  在构建table 1时加入了一个变量分列条件而已。
>
> 需要注意
>
> - 验证连续变量：正态分布与否
> - 分类变量检验方法。

- 前处理同上述方法

```R
library(tableone) #1.加载R包
rm(list = ls()) #2.清理运行环境
aa<- read.csv('20210126.csv') #3.读入数据
head(aa)  #4.查看数据前6行
str(aa)   #5.查看数据数据性质
names(aa) #6.提取变量的名字
myVars <- c("age","age70"，"race","marry",               
            "t","n", "tnm","er","pr",               
            "g","her2", "sur","rt","che",               
            "time","status") # 7. 定义输出变量
catVars <- c("age70"，"race", "marry",              
             "t", "n",  "tnm", "er", "pr",            
             "g","her2", "sur","rt","che",          
             ,"status") # 8. 定义分类变量
```

- 构建Table对象

构建table 函数，strata 参数 。英文引号内填入需要分列的变量，例如本研究想探索放疗对预后的影响，则为strata = "rt"

```R
table <- CreateTableOne(
	vars = myVars, #条件1        
	factorVars = catVars,#条件2          
    strata = "rt", #条件4              
    data = aa, #原始数据             
    )
```

- 输出参数设置

```R
 nonvar <- c("time","age") # 指定非正态分布连续变量变量
 exactvars <- c("a", "b") # 假如有T<5变量应使用Fisher精确检验，本文数量大，无需Fisher精确检验
catDigits = 2 # 修改连续变量小数位数为2位
contDigits = 3 # 分类变量百分比位数为3位
pDigits = 4 # 调整小数位数为4位；
```

输出结果

```R
table1<- print(table, #构建的table函数       
	nonnormal = nonvar,   
    #exact = exactvars,      
    catDigits = 2,contDigits = 3,pDigits = 4, 
    showAllLevels=TRUE, #显示所有变量    
    quote = FALSE, # 不显示引号           
    noSpaces = TRUE, # #删除用于对齐的空格   
    printToggle = TRUE) #展示输出结果
```

![image-20210814102130340](D:/software/Typora/image/image-20210814102130340.png)

- 保存为Excel

```R
 write.csv(table1, file = "table1.csv")
```

###### 3.5.3 n × 5 列表

> 在构建table对象时，增加一个addOverall = TRUE

- 构建Table对象

```R
 table <- CreateTableOne(vars = myVars,     
 	factorVars = catVars,       
    strata = "rt",       
    data = aa, #原始数据               
    addOverall = TRUE)
```

- 输出结果

```R
 table1<- print(table,        
 	nonnormal = nonvar,        
    #exact = exactvars,        
    catDigits = 2,contDigits = 3,pDigits = 4, 
    showAllLevels=TRUE, #显示所有变量     
    quote = FALSE, # 不显示引号           
    noSpaces = TRUE, # #删除用于对齐的空格   
    printToggle = TRUE) #展示输出结果
```

![image-20210814102527931](D:/software/Typora/image/image-20210814102527931.png)

##### 3.6 其他参数解析

###### 3.6.1 CreateTableOne()

```R
 table <- CreateTableOne(
 		vars, #指定哪些变量是Table需要汇总的变量
 		strata,#指定进行分层的变量，不写则只出Overall列  
 		data,# 变量的数据集名称  
 		factorVars,#指定哪些变量为分类变量，指定的变量应是vars参数中的变量 
        includeNA = FALSE,#为TRUE则将缺失值作为因子处理，仅对分类变量有效 
        test = TRUE,#默认为TRUE，当有2个或多个组时，自动进行组间比较 
        testApprox = chisq.test,#默认卡方检验 
        argsApprox = list(correct = TRUE),# 进行连续校正的chisq.test 
        testExact = fisher.test,#进行fisher精确检验
        argsExact = list(workspace = 2 * 10^5),# 指定fisher.test分配的内存空间 
        testNormal = oneway.test, # 连续变量为正态分布进行的检验，默认为oneway.test，两组时相当于t检验
        argsNormal = list(var.equal = TRUE), # 假设为等方差分析 
        testNonNormal = kruskal.test,# 默认为Kruskal-Wallis秩和检验 
        argsNonNormal = list(NULL),#传递给testNonNormal中指定的函数的参数的命名列表 
        smd = TRUE,#如果为TRUE（如默认值）并且有两个以上的组，则将计算所有成对比较的标准化均值差。
        addOverall = FALSE#仅在分组时使用）将整个列添加到表中。Smd和p值计算仅使用分层的列阵进行。);table
```



###### 3.6.2 print()

```R
table1<- print(  
	x, #CreateTableOne()的 <- 前的名字(x=table) 
    catDigits = 1, #连续变量小数位1位 
    contDigits = 2,#分类变量保留2位 
    pDigits = 3,#p值保留3位 
    quote = FALSE,#默认值为FALSE。如果为TRUE
    	#，则包括行名和列名在内的所有内容都用引号引起来，以便您可以轻松地将其复制到Excel。 
    missing = FALSE,#是否显示丢失的数据信息 
    explain = TRUE,#显示百分比时是否在变量名称中添加解释，即（％）添加到变量名称中。  
    printToggle = TRUE,#如果为FALSE，则不输出 
    test = TRUE,#是否显示p值。默认为TRUE。 
    smd = FALSE,#是否显示标准化均值差异。默认为FALSE。如果存在多个对比，则显示所有可能的标准化均值差的平均值。
    noSpaces = FALSE,#是否删除为对齐而添加的空格。
    padColnames = FALSE,#是否用空格填充列名以居中对齐。默认值为FALSE。如果noSpaces = TRUE，则不进行 
    varLabels = FALSE,#是否用从labelled :: var_label（）函数获得的变量标签替换变量名。
    format = c("fp", "f", "p", "pf")[1],#默认值为“ fp”频率（百分比）。您也可以选择仅“ f”频率，“仅p”百分比和“ pf”百分比（频率）。  
    showAllLevels = FALSE,#是否显示所有级别。 
    cramVars = NULL,#字符向量，用于指定两个级别的分类变量，对于这两个级别的变量，应在一行中显示两个级别。
    dropEqual = FALSE,#是否删除“ =第二级名称”描述，指示为两级分类变量显示哪个级别。  
    exact = NULL,#字符向量，用于指定p值应为精确测试值的变量。 
    nonnormal = NULL,#字符向量，用于指定p值应为非参数检验的变量的变量。
    minMax = FALSE#对于非正态变量，是否使用[min，max]而不是[p25，p75]。默认值为FALSE。)
```
