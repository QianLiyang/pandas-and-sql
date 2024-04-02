# Sesame sauce Is All You Need

# 一、读取数据：

### `pd.read_csv()`、`pd.read_excel()`

- 主要参数：
- ​	path:文件路径
- ​	sep:分隔符
- ​	header:表示列名位置，默认是header=0
- ​	name:用于设置列名，特别是header=None的时候，当然也可以用来重命名列，传入一个列表即可
- ​	encoding：文件编码格式
- ​	parse_dates:要设置为日期格式的列

# 二、数据信息查看

1. `df.head()、df.tail()`->df

2. `df.shape->tuple与df.count()`->series区别：

   ​		df.shape返回的就是表的形状，shape[0]×shape[1]=df.size

   ​		df.count(axis=0)返回每列非缺失值的数量，类似于sql的count()

3. `df.index`和`df.columns`返回行索引和列索引->pandas.Index

   ​		`df.index.values`和`df.columns.values`:->Series

   ​		`df.index.tolist()`和`df.columns.tolist()`：->list;**Note:**Index和Series对象都有tolist()方法

4. `df.dtypes`->series 返回每列的数据类型

5. `df.info()`可以理解成上面各个属性或者函数的集成，提供了有关数据的基本信息摘要。

6. `df.describe()`->DataFrame:可以返回dataframe中的描述性统计信息：count mean std min 25% 中位数 75% max等

   ​		此外：可以对某个Series（例如：df["列名"]）可以使用df["列名"].count()、mean()、std()、min()、median()、max()等

7. `df.nunique(axis`)->Series:返回df指定轴中不同元素的数量,类似于sql中对所有列进行：count(distinct 列名)；

   ​	  `series.unique()`->Array:返回Series对象中的唯一值数组，类似于sql中 distinct 列名，**这样就不需要set(series.values.tolist())操作了**。

8. ``df["column_name"].value_counts()`->Series:返回Series对象中每个取值的数量，类似于sql中group by(Series.unique())后再count()

9. `df["column_name"].isin(set or list-like)`->Series:常用于判断df某列中的元素是否在给定的集合或者列表里面。

# 三、缺失值、重复值检查与处理

## 1、空表检查:

Series/DataFrame.empty()->Ture or False.注意:如果 Series/DataFrame 仅包含 NaN，它仍然不被视为空。

这些操作对Dataframe与Series都可以用，需要注意的是Series没有axis.

## 2、缺失值：

### 检查：

`df.isna()、df.isnull()`:检查是否有缺失值->df,df.isna().any(axis=0 or 1)->series用于检查指定axis方向上是否有缺失值;df.notna():与df.isna()逻辑相反

补充：any()函数用用于检查指定axis方向上是否至少存在一个True值.

应用：series.isnull()->bool series 可以用来当一个布尔索引

### 处理：

丢弃：`df.dropna(axis=0,how="all",inplace,ignore_index)`其中how:{'any', 'all'}

填充：固定值填充：`df.fillna(value,method,axis,inplace)`,均值填充的例子:`series.fillna(series.mean(),inplace=True)`

使用前一个或者后一个填充：`df.fillna(method,axis,inplace),`method:{"ffill","bfill"}

## 3、重复值：

### 检查:

`df.duplicated(subset)`->series:Return boolean Series denoting duplicate rows

### 丢弃:

`df.drop_duplicates(subset,keep,inplace,ignore_index)`->DataFrame **Note:**duplicate别忘了s

# 四、排序

1、按照values排序:`sort_values(by,asceding,inplace,ignore_index)`,默认采用快排。书写结构和sql里面的order by是完全类似的。

2、按照index排序:`sort_index(asceding,inplace,ignore_index)`
**​Note:**这两个函数的ignore_index可以起到重新设置index的作用，故无需再调用reset_index()

# 五、重设Index与Columns_name

## 	1、重设Index行标签：

1. 方法一：`df.index`=自定义的索引值np数组(列表)
2. 方法二：`df.set_index(keys,drop,inplace)`:把现有的列(列组合则是多级索引multiIndex)或者一个长度正确的array设置为index
3. 方法三：`df.reset_index(drop,inplace)`:重新设置索引，即变成0、1、2、3...
4. 方法四：有些带有ignore_index参数的操作,可以起到重设index的作用。例如：dropna(),drop_duplicates(),sort_index(),sort_value()等

## 	2、重设Columns_name列标签:

1. 方法一：`df.columns`=自定义的列名值np数组(列表)

2. 方法二：`df.rename(columns=mapper,inplace=True)`等价于:

    `df.rename(mapper : dict-like or function,axis=1,inplace=True)`,其中mapper :Function / dict values must be unique (1-to-1).

   第二种方法思想更通用，可以发现rename()还可以对Index进行重命名。mapper+axis== colums=mapper or index=mapper

3. 方法三：在读取文件的时候设置name参数

# 六、文本数据类型

### **Note:**只能对Series使用,不能对DatdFrame使用下面函数(因此改数据类型要一列一列的改)

## 1、设置数据类型为字符串类型:

​	pandas中存储文本数据有两种方式:object-dtype的numpy数组和StringDtype扩展类型吗，官方更推荐后者。

1. 方法一：创建时，显式请求stringdtype即:pd.Series(data,dtype="string")或者dtype=pd.StringDtype(),这种方式和np.array()里面显示指定数据类型完全一样。
2. 方法二：Series=Series.astype("string") or astype(pd.StringDtype())    **Note：astype用处广泛：astype(int|float|"int"|"float32"等)**

## 2、字符串处理：

​	在将Series数据类型设置为string之后,再使用Series.str:这是一种Accessor Methods,后面再指定对应的python中字符串函数即可。

​	Accessor Methods:

<img src="C:\Users\11565\Desktop\Accessor_Methods.png" alt="Accessor_Methods" style="zoom: 50%;" />

1. 大小写转换:Series.str.upper()/lower()/title()->Series
2. 去除空白字符：Series.str.split()/lsplit()/rsplit()->Series
3. 替换(正则化):Series.str.replace(old,new,regex=True or False)->Series
4. 分割:Series.str.split(separator)->Series.	Note:此时Series的每个元素是一个list,list装的是分好的元素
5. 计算长度:Series.str.len()->Series.
6. 正则化提取:Series.str.extract(regex)/Series.str.extractall(regex)->Series. Note:extract()提取第一个匹配项,extractall()提取所有匹配项,会创建多级索引。
7. 切片提取:Series.str.slice[:]->Series
8. 检查(是否包含):Series.str.contains(substring)->Series(bool).
9. 检查(是否以指定前缀/后缀开始):Series.str.startswith(prefixx)/Series.str.endswith(sufixx)->Series(bool)
10. 检查(是否为数字字符串):Series.str.isnumeric()->Series(bool).

# 七、日期数据类型：这一块涉及的函数特别多，下面是几种常用的函数

## 1、设置数据类型为时间

方法一:在读取文件的时候指定参数parse_dates

方法二：`pd.to_datetime(args,format="%Y/%m/%d")`,其中arg可以是int, float, str, datetime, list, tuple, 1-d array, Series, DataFrame/dict-like->Datetime数据类型

**Besides：**取具体时间的日期或者时间部分:在将Series数据类型设置为Datetime之后,使用**Series.dt**:这也是一种Accessor Methods,记忆上可以参考上面的str.

- 取年份:series.dt.year  # `.dt` 属性是用于 `Series` 或 `DataFrame` 对象的，用于处理日期时间类型


- 取日期:series.dt.date


- 取时间:series.dt.time


- 通过格式化日期时间:series.dt.strftime("format")可以自由取出想要的模式,其中'%Y年 %m月%d日 %H时%M分%S秒'


## 2、计算两个日期之间的差->Timedelta对象

直接两个数据类型为TimeStamp的Series相减:Series1-Series2->Series数据类型为Timedelta，

再对Timedelta对象调用.dt.days->int64将Timedelta数据类型变int

因此:(series1-series2).dt.days->int

# 八、查、删、改、增

## 1、查数据iloc

1. `df["column_name"]`==`df.column_name`->Series,若要提取多个列，传入列名称列表即可:`df[["column1","column2","column3"]]`->DataFrame

2. loc:使用标签来提取数据,可以是行标签或列标签,所谓标签就是索引(位置)的标签->Series or DataFrame

   - A single label:	`df.loc[indexl,column_label]`


   - A list or array of labels:	`df.loc[[index1,index2...],[column_label,column_labe2...]`


   - A slice object with labels：	`df.loc[:,:]`，用:表示都取出来


   - A callable function with one argument:	


   **NOTE:**有时候行标签(index)和索引一样，会让人产生一种误会，觉得row_label参数就是索引值。其实这是不一样的！当不特别设置行标签index的时	候,index默认就是从0开始的，这时候行标签index和索引是一样的。此外: contrary to usual python slices, both the start and the stop are included

3. iloc:使用整数索引(位置)来提取数据

   - An integer:	`df.iloc[row_index,column_index]`


   - A list or array of integers：	`df.iloc[[row_index1,row_index2...],[column_indexl,column_index2...]`


   - A slice object with ints：	`df.iloc[:,:]`，用:表示都取出来


   - A callable function with one argument:	


   **NOTE:**由于iloc是基于索引(位置)的，所以遵循python索引机制，即:df.iloc[0:3,:]只会取出索引=0，索引=1，索引=2，不会取出索引=3！

4. query:使用类似SQL的语法在Pandas中进行数据查询,可以更加直观地编写查询条件->DataFrame or None

   ​		df.query("类似于sql where后面的expression"),

   ​		两个例子:

   ​		    `result = df.query('Area/2 > 200')`,将四则运算符/和逻辑运算符>都是写在字符串里面

   ​			`result = df.query('Area > @avg')`通过在变量前面加上“@”字符来引用Python中的变量

   **NOTE:**query类似于sql中的where，只能过滤行，不能过滤列。要过滤列需要再次调用上面的几个函数，例如:df.query('Age > 25')[['Name', 'Salary']]

5. Bool indexing:

   ​	`df[bool expression]`->Series or DataFrame

   ​	`df.loc[bool expression,""]`**.Note**:无需将Series转化为array,因为loc支持：A boolean array and An alignable boolean Series

   ​	`df.iloc[bool expression.values,""]`.**Note:**这里要用bool indexing必须加.values将series转化成array,因为iloc期望:A boolean array.

## 2、删数据drop

**虽然上面的函数肯定是可以通过取特定的部分来实现删数据的，但是有些情况下drop函数更方便**

​	**NOTE:**df.drop()函数传参方式类似于rename()函数

- 第一种:

​		丢列：`df.drop(columns=["A","B"],inplace=True/False)`

​		丢行：`df.drop(index=["0","2"],inplace=True/False)`

- 第二种:

​		丢列`df.drop(["A","B"],axis=1,inplace=True/False)`

​		丢行`df.drop(["0","2"],axis=0,inplace=True/False)`

## 3、改数据update

1. 通过等号修改：和python中给变量赋值一样，但是要传入对应大小的值(数组)。`df["A"]=[2,3,4]、df.loc[0,"A"]=2、`

   `df.loc[0,:]=[]|np.array()|Series`

2. 通过update函数进行修改：**Modify** **in place** using **non-NA** values from another DataFrame.`df.updtae(other)`

   **Note：如果用NA来更新，则原数据不会被更新。**

   参数other:可以是DataFrame也可以是Series

   ​	用法：

   - 保证列名一样，就可以一次性修改好几列(下面例1)，这时other参数一般是一个dataframe
   - 保证列名一样，**设置好要修改的元素位置(在列中的位置),**可以一次性修改好几个值(下面例2)，这样对**不连续的值修改比较方便**，这时other参数一般是一个Series

```python
df = pd.DataFrame({'A': [1, 2, 3],'B': [400, 500, 600],'C':[700,800,900]})
new_df = pd.DataFrame({'B': [4, 5, 6],'C': [7, 8, 9]})
df.update(new_df)
>>>df
A B c
1 4 7
2 5 8
3 6 9
df = pd.DataFrame({'A': ['a', 'b', 'c'],'B': ['x', 'y', 'z']})
new_column = pd.Series(['d', 'e'], name='B', index=[0, 2])
df.update(new_column)
>>>df
A B
a d
b y
c e
```



## 4、增数据insert

### 增添行数据：

- 1、和修改类似，可以通过直接赋值的方法：`df.loc[len(df),:]=[]|np.array()|Series`->最后一行新增一条观测

- 2、在**指定位置增加一(多行)行数**:利用concat函数

  ```python
  position=1  # 表示在行标签为1的后面加一条观测
  df = pd.DataFrame({'A': [1, 2, 4],'B': [4, 5, 7]})
  new_row=pd.DataFrame({'A':[3,],'B':[6,]})
  result=pd.concat([df.loc[0:position,:],new_row,df.loc[position+1:,:]],axis=0,ignore_index=True)
  >>>result
  A B
  1 4
  2 5
  3 6
  4 7
  ```

### 增添列数据：

- 1、增添到最后一列:和修改类似，可以通过直接赋值的方法：df["new_column"]=2、df["new_column"]=df["c1"]+df["c2"]...

- 2、增添到指定位置:Insert column into DataFrame at specified location

  **Note:**如果要插入的列已经包含在df里面了则会报错，除非allow_duplicates` 被设置为 True.

  `df.insert(loc,column,value,allow_duplicates)`

  loc:要插入的列位置，int

  column:要插入的列的名字

  value：要插入的值，Scalar, Series, or array-like

  ```python
  df = pd.DataFrame({'A': [1, 2, 3],'B': [4, 5, 6]})
  df.insert(1, 'C', [7, 8, 9]) # array-like
  >>>df
  A C B
  1 7 4
  2 8 5
  3 9 6
  
  df = pd.DataFrame({'A': [1, 2, 3],'B': [4, 5, 6]})
  df.insert(1, 'C', 7) # Scalar
  >>>df
  A C B
  1 7 4
  2 7 5
  3 7 6
  ```

# 九、数据框合并

## 1、merge:类似于sql中的join,关系连接

1. 格式：

   `df=df1.merge(right,how,on)`，这种是左表的基础之上，去merge右张表

   `df=pd.merge(left,right,how,on)`，这种是调用pd.merge函数，去merge左表和右表

2. 主要参数： 

   how:{'left', 'right', 'outer', 'inner', 'cross'}：指定连接方式，和sql一样

   on : str, list of str, or array-like, optional：指定连接字段，前提是左表和右表字段名一样，**其实这更像sql的using**

   **Note:**如果字段名称不一样，可以利用left_on和right_on：

   `result = pd.merge(df1, df2, left_on='ID', right_on='UserID')`

3. 多表连接：

   `result = pd.merge(df1, df2, on='ID', how='outer').merge(df3, on='ID', how='outer')`

   ``result = df1.merge(df2, on='ID', how='outer').merge(df3, on='ID', how='outer')`

## 2、concat:沿着特定的axis将多个对象以某种方式连接起来，非关系连接

1. 格式：

   `df=pd.concat(objs,axis,join,ignore_index)`

   **Note:**concat和merge有点区别，DataFrame和Series是没有concat方法的，但是他们有merge方法

2. 主要参数： 

   objs:有两种传参方式:

   ​	`result = pd.concat([s1, s2])`

   ​	`	result = d.concat({'df1': df1, 'df2': df2})`,这中方法会给每个表一个key,形成多级索引，因此：`result["df1"]=df1`

   axis: {0/'index', 1/'columns'}, default 0

   join : {'inner', 'outer'}, default 'outer'

   ignore_index:如果为 True，则连接方向axis不使用原来的索引值，重新设置。

**Note：一定要注意，concat是非关系连接，没有参数on**

# 十、分组与聚合

## 分组:result=df.groupby(by,axis=0,level= None,dropna=True)

参数：

​	1、by就是分组依据，传入一个列表,列表元素是列，如果有好几个列，会形成多级索引

​	2、axis默认就是0，为沿着什么方向分组。

​	3、level默认是None，用于指定在多级索引（MultiIndex）上进行分组时的级别。这个参数可以是索引级别的标签（label），也可以是索引级别的位置（position），如果dataframe不是多级索引结构，不需要管这个参数。

​	4、dropna默认True：表示是否丢弃na，如果为False，则分组的时候会将na视为单独一组.

**Note：**`result=df.groupby(by,axis=0`)其实是没有显式结果的，会返回<pandas.core.groupby.generic.DataFrameGroupBy object at XXXXXX

若想要看结果,可以使用以下循环打印出来：

```python
`for key, group in result:`    

​		`print(f"Key: {key}, Group:\n{result}")`
```

## 聚合:

#### 与sql一样，groupby将原表分成好几组，再对每组使用聚合函数，最后再把结果汇总起来

<img src="C:\Users\11565\Desktop\微信图片_20240401205217.png" style="zoom:50%;" />

### 格式：

1、`result=df.groupby(by=["sex","province"]).sum()/min()/count()...`对每一列都计算前面的函数值

​      `group_df=df.groupby(by=["Extreme_Weather_Event","Policy_Change"])["Stock_Index"].describe()`对聚合后的表只对"Stock_Index"列做描述性统计

2、`agg()`允许同时使用多个聚合操作。可以向`agg()`方法传递一个聚合函数列表**(包括自定义的)**，或者一个函数名称与列名的字典，来对不同的列应用不同的聚合函数。

例如：

​    `df.agg(["sum","median"],axis=0)`没调用groupby函数，就是把整个表看成一个组，对每个列调用sum、median方法

​	在使用 `groupby` 方法后，`agg()` 方法默认会沿着列方向应用聚合函数：

​	`result=df.groupby(by=["sex","province"]).agg(["fun1","fun2"...])`对每一列都使用fun1、fun2

​	`result=df.groupby(by=["sex","province"]).agg({"c1":["fun1","自定义函数f"],"c2":["fun3"]})`指定对c1列使用fun1、**自定义函数f，**对c2列使用fun3



**Note:agg = aggregate**

### 常见的聚合函数

1. `sum()`计算每个分组的数值列的总和。
2. `mean()`计算每个分组的数值列的平均值。
3. `std()`计算每个分组的数值列的标准差。
4. `min()`找出每个分组的数值列的最小值。
5. `max()`找出每个分组的数值列的最大值。
6. `count()`计算每个分组的大小（即该分组中的元素数量）。
7. `size()`计算每个分组的大小，与`count()`类似，但`size()`包含NaN值，而`count()`不包含。类似于mysql的count(*)
8. `first()`返回每个分组的第一个非NA值。
9. `last()`返回每个分组的最后一个非NA值。
10. `median()`计算每个分组的数值列的中位数。
11. `var()`计算每个分组的数值列的方差。
12. `describe()`为每个分组提供描述性统计摘要，包括均值、标准差、最小值、四分位数、最大值等

# 十一、map、apply、applymap

## Map：只能对Series使用

功能:用于对**Series对象**中的每个元素做映射-> Series，Same index as caller

**解析:对每个元素做映射，这相当于有一个循环在里面，不过这个循环是pandas内部帮我们实现的，不是显式的，因此如果写函数，参数应该是针对value而不是series或者dataframe**

格式: `result=s.map(arg,na_action)`，

**Note：当arg是字典时，如果s里面的元素不在字典里面，则映射为Nan。**

参数:

​	arg: Callable(function) | Mapping(dict) | Series，这里Callable(function)函数**只能有一个形参**

​	na_action:为了避免将**函数**应用于缺失值（并将它们保留为 ''NaN''），可以使用 ''na_action='ignore''',因此只有当arg为function的时候才有用

例子：

```python
#dict
s=pd.Series(["cat","dog","mouse","pig"],name="animal")
mapper={"cat":"猫","dog":"狗","pig":"猪"}
s1=s.map(mapper)
>>s1
animal
猫
狗
NaN
猪
#function
s=pd.Series(["abcde","abc","abcdefg","abcdefgh",None],name="character")
s1=s.map(lambda x:len(x),na_action="ignore") # 参数应该是针对value而不是series或者dataframe
>>s1
character
5.0
3.0
7.0
8.0
NaN  # 注意区别

s = pd.Series(["cat", "dog", "mouse", "pig"], name="animal")
s1 = s.map(lambda x: f"它是{x}")
>>s1
animal
它是cat
它是dog
它是mouse
它是pig

#series 不推荐
```

## Apply：既可以用于Series也可以用于DataFrame

1. 功能：

   - 对于Series:apply用于对 Series 的**每个元素应用一个函数**,因此对于Series而言,如果**映射是字典，只能用map。**

     **如果函数简单即只有一个参数，更加推荐map，而不是apply，但是如果函数参数多余两个，map就不能用了，只能用apply，具体使用格式看下面例子**

   - 对于DataFrame:用于对DataFrame确定方向的**每个Series应用一个函数**，是在Series级别上的。

     **Note：因为对于DataFrame而言，元素是Series，取dataframe中一列是series，取一行也是series**

     ​             axis=1:表明对行series进行操作，因为要把函数用在这个行series上，所以是横向(axis=1)

     ​             axis=0:表明对列series进行操作，因为要把函数用在这个列series上，所以是纵向(axis=0)

2. 参数以及例子：

- Series

  result=s.apply(func,args,**kwargs)

  ​	func :Python function or NumPy ufunc to apply,**这里函数不需要加括号，直接当成变量使用！**

  ​	args:  tuple，func的位置形参

  ​	kwargs:func的关键字形参

  ```python
  # 例一 简单函数
  s=pd.Series(["abcde","abc","abcdefg","abcdefgh",None],name="character")
  s1=s.apply(lambda x:len(x),na_action="ignore") 
  >>s1  # 这里会报错，因为apply没有na_action参数，由此可见，在None的处理上map比apply更好
  
  # 例二 复杂函数
  s=pd.Series([10,20,30,40],name="score")
  def add_score(x,y,z=1):  # 这里x是series里面的元素
      return (x+y)*z
  s1=s.apply(add_score,y=100,z=2) # 这里默认series里的值就是add_score函数的第一个形参，因此只需定义后面两个就行了
  >>s1
  score
  220
  240
  260
  280
  ```

- DataFrame

  df["new_column"]=df.apply(func,args,**kwargs)

  参数比Series多一个axis

  ```python
  df=pd.DataFrame({"小明":[100,200,300],"小红":[200,300,10],"小刚":[400,800,100]})
  def avg_score(series,weight=1):  # 这里第一个形参series代指的是dataframe里面的series，一定要注意和Series运用apply的区别。
      return (series["小明"]+series["小红"]+series["小刚"])*weight
  df["avg"]=df.apply(avg_score,axis=1,weight=0.9)
  >>df
  小明 小红 小刚 avg
  100 200 400 630.0
  200 300 800 1170.0
  300 10  100 369.0
  ```

## Applymap：只能对DataFrame使用

功能：将函数应用于**DataFrame的每一个元素**，该函数接受并向 DataFrame 的每个元素返回标量

格式:df1=df.applymaxp(func,na_action)

参数说明

​	func:Python function, **returns a single value from a single value.**

​	na_action:为了避免将**函数**应用于缺失值（并将它们保留为 ''NaN''）,和map函数一样，并且这里func函数**只能有一个形参**，不像apply。

例子：

```python
# 这里直接引用pandas官方的例子
# 无None
df = pd.DataFrame([[1, 2.12], [3.356, 4.567]])
df.applymap(lambda x: len(str(x)))  # x代指dataframe


# 有None
df_copy = df.copy()
df_copy.iloc[0, 0] = pd.NA
df_copy.applymap(lambda x: len(str(x)), na_action='ignore')  # 设置对None不适用函数
```

## 十二、多级索引Multi-Index:设置多级行标签

## 1、设置Multi-Index

1. 通过`df.set_index(keys,drop,inplace)`，指定keys参数为表中的多个列名称就行了。

   ```python
   import pandas as pd
   data = {
       '省份': ['广东', '广东', '广东', '北京', '北京', '上海'],
       '城市': ['深圳', '广州', '珠海', '北京', '朝阳区', '浦东新区'],
       '人口': [1500, 1200, 300, 1300, 1000, 900]}
   df = pd.DataFrame(data)
   df.set_index(['省份', '城市'], inplace=True) # 以'省份', '城市'为index
   
   >>df
              人口
   省份 城市        
   广东 深圳    1500
       广州    1200
       珠海    300
   北京 北京    1300
       朝阳区   1000
   上海 浦东新区 900
   ```

   

2. groupby函数by参数接了不止一个列

```python
import pandas as pd
data = {
    '地区':["南方","南方","南方","南方","北方","北方","南方",]
    '省份': ['广东', '广东', '广东','广东','北京', '北京', '上海'],
    '城市': ['深圳', '广州', '珠海', '珠海','朝阳区', '朝阳区', '浦东新区'],
    '今年人口': [1500, 1200, 300, 1300, 1000, 900]}
df = pd.DataFrame(data)
sum_population=df.groupby(by=["地区","省份"]).sum()
>>>sum_population
          人口
地区 省份      
北方 北京   2400
南方 上海   900
    广东  4300
```

## 2、通过Multi-Index查询数据

**Note:**使用loc函数，**但是应当以一个元组的形式传入Multi-Index**

**df.loc((一级行标签，二级行标签...), start:end)**

类似地：

- 如果每级选择好几个行标签，应当使用列表

```python
sum_population.index  # 查看索引
>>>
MultiIndex([('北方', '北京'),
            ('南方', '上海'),
            ('南方', '广东')],
           names=['地区', '省份'])
sum_population.loc[("南方",["上海","广东"]),:] # 取多个二级行标签，用列表
>>>
           人口
地区 省份      
南方 上海   900
     广东  4300
```



- **如果选择连续地行标签**，**要用slice(start_index_name,end_index_name,step),不能用:(冒号)**,

  **Note：**`slice(start_index_name,end_index_name)`是左闭右也是闭的!

```python
sum_population.loc[("南方",slice("上海","广东")),:] # "上海","广东"都会被取到
>>>
           人口
地区 省份      
南方 上海   900
     广东  4300
    
sum_population.loc[("南方",slice(None)),:]  # slice(None)==:
>>>
           人口
地区 省份      
南方 上海   900
     广东  4300
```

## 3、取消Multi-Index

```python
sum_population.reset_index(drop=False,inplace=True)  # 使用reset_index就好
>>>sum_population
   地区  省份  人口
0  北方  北京  2400
1  南方  上海   900
2  南方  广东  4300
```

# 十三、窗口操作

1. 功能：对**Series数据**的特定窗口进行计算，**注意：只能针对Series对象使用，即在df中只能对一列或者一行使用！**

2. 格式：`rolling_outpout=df["column_name"].rolling(window,min_periods,closed,step).func()|.apply(自定义的函数)`

3. 常用参数：

   - window：窗口的大小，可以是int、时间增量（dt.timedelta）、字符串、BaseOffset 或 BaseIndexer 类型。指定了窗口的长度。

   - min_periods：指定在计算统计量时，窗口内所需的最小观测值数量,默认由窗口大小决定！
   - closed：可选参数，字符串，默认为 None。指定窗口边界的闭合方式。具体可选的方式包括 right、left、both、neither。
   - step：指定滑动窗口的步长，即窗口在数据上滑动的间隔大小。

4. 例子：

   ```python
   # window为整数
   s=pd.Series([10,20,30,40,50,-10,-20],name="num")
   s1=s.rolling(window=2,min_periods=1).mean()
   >>s1
   15.0
   25.0
   35.0
   45.0
   20.0
   -15.0
   
   # window为timedelta
   import pandas as pd
   time_index=pd.date_range(start="2019/06/24",end="2024/06/24",freq=pd.DateOffset(years=1))
   s=pd.Series([60,70,80,90,100,110],name="score",index=time_index)
   s1=s.rolling(window=pd.Timedelta(days=365*2),min_periods=2).median()
   >>S1
   2019-06-24      NaN
   2020-06-24     65.0
   2021-06-24     75.0
   2022-06-24     85.0
   2023-06-24     95.0
   2024-06-24    105.0
   # 对窗口使用自定义的函数
   #用的少
   ```

# 十四、实用函数

groupby+agg>>实现sql中聚合操作

groupby+transform>>实现sql中窗口操作

## 1、transform()：辅助实现sql窗口函数

格式：例如：df['Value1_Mean'] = df.groupby('Group')['Value1'].transform('mean')

常用参数：

​		func:要应用于每个组的函数。可以是函数名称（字符串）、函数对象。

​		axis：函数作用的方向,默认axis=0

​		*args 和 **kwargs：传递给 func 函数的额外参数，这个和前面提到的apply完全一样

功能：根据指定的函数**对分组后的group中的一列或者多列(groupby之后的对象是group)数据进行计算**，然后将转换后的结果广播回原始DataFrame的相应位置，**因此不会改变原来数据的行**

例子:

```python
data = {'Group': ['A', 'A', 'B', 'B', 'B'],'Value': [3, 2, 1, 7, 5]}
df = pd.DataFrame(data)

def easy_func(x, constant):
    return (x ** 2) * constant
df['Transformed_Value'] = df.groupby('Group')['Value'].transform(easy_func, constant=3) # 自定义的函数不止一个参数

>>df
 	Group  Value  Transformed_Value
0     A      3                 27
1     A      2                 12
2     B      1                  3
3     B      7                147
4     B      5                 75
```

- **实现sql中：聚合函数(column)over(partition by 'column1')**，**实现分区计算。**

```python
import pandas as pd
data = {'Group': ['A', 'A', 'B', 'B', 'B'],
        'Value1': [3, 2, 1, 7, 5],
        'Value2': [10, 20, 30, 40, 50]}
df = pd.DataFrame(data)
df[['Value1_Mean', 'Value2_Mean']] = df.groupby('Group')[['Value1', 'Value2']].transform('mean')
"""
这等价于：
df['Value1_Mean'] = df.groupby('Group')['Value1'].transform('mean')
df['Value2_Mean'] = df.groupby('Group')['Value2'].transform('mean')
"""
>>>df
 Group  Value1  Value2  Value1_Mean  Value2_Mean
0     A       3      10     2.500000         15.0
1     A       2      20     2.500000         15.0
2     B       1      30     4.333333         40.0
3     B       7      40     4.333333         40.0
4     B       5      50     4.333333         40.0
```

- **实现sql中：聚合函数(column)over(partition by  'column1' order by  "column2")，实现分区内累计计算**

  **Note：**在sql中，(partition by 'column1' order by  "column2")中是否设置order by参数是有区别的，以sum函数为例：有order by则是在窗口内计算累计和，没有则是直接计算区间和。

  **Note：**`df['Value'] = df.groupby('Group')['Value'].transform(lambda x: x.sort_values().values) `，一定要加.values

  **！！原因：！！transform()广播时，如果结果是Series，则会按索引将其数据与原始数据对齐。x.sort_values().values直接变成数组赋值了，**

```python
import pandas as pd
data = {'Group': ['A', 'A', 'B', 'B', 'B'],
        'Value': [3, 2, 1, 7, 5]}
df = pd.DataFrame(data)
# 对每个分区内的值进行排序
df['Value'] = df.groupby('Group')['Value'].transform(lambda x: x.sort_values().values)  # 一定要有.values
# 原因:transform()广播时，是按原始数据的索引对齐的
df['roll_sum'] = df.groupby('Group')['Value'].transform(lambda x:x.rolling(window=1).sum()) 
>>df
 Group  Value  roll_sum
0     A      2       2.0
1     A      3       3.0
2     B      1       1.0
3     B      5       5.0
4     B      7       7.0
```

- 对每个组数据做标准化处理

```python
data = {'Group': ['A', 'A', 'B', 'B', 'B'],
        'Value': [1, 2, 3, 4, 5]}
df = pd.DataFrame(data)
df['normlized'] = df.groupby('Group')['Value'].transform(lambda x:(x-x.mean()/x.std()))  # 这里的x是分组下的小Series
>>df
 Group  Value  normlized
0     A      1   -1.12132
1     A      2   -0.12132
2     B      3   -1.00000
3     B      4    0.00000
4     B      5    1.00000
```

## 2、shift

- 对于某个Series，取它的上n个或者下n个

```python
data = {'Value': [1, 2, 3, 4, 5]}
df = pd.DataFrame(data)
df['Value2'] = df['Value'].shift(2) # 使用 shift 函数获取上2个值
>>df
     Value      Value2
0      1         NaN
1      2         NaN
2      3         1.0
3      4         2.0
4      5         3.0
```

- **实现sql中的lead与lag**

```python
data = {'Group': ['A', 'A', 'B', 'B', 'B'],
        'Value': [1, 2, 3, 4, 5]}
df = pd.DataFrame(data)

# 定义自定义函数，用于在每个分组中获取前一个值
def get_previous_value(group,n):
    return group.shift(n)

df['Previous_Value'] = df.groupby('Group')['Value'].transform(get_previous_value,n=1) 
>>df
roup	Value	Previous_Value
0	A	1	NaN
1	A	2	1.0
2	B	3	NaN
3	B	4	3.0
4	B	5	4.0
```

## 3、rank

格式:**ranked_series=series.rank(method,na_option,ascending)**（因为通常用于Series，所以就没写axis参数）

参数：

​	method :指定重复值的排序方法 {'average', 'min', 'max', 'first', 'dense'}, default 'average'}。

​	**其中dense==sql的dense_rank、min==sql的rank、first==sql的row_number.**

```python
* average: average rank of the group
* min: lowest rank in the group
* max: highest rank in the group
* first: ranks assigned in order they appear in the array
* dense: like 'min', but rank always increases by 1 between groups.
```

​	na_option : 指定缺失值排序方法{'keep'：保留缺失值，排名将为 NaN, 'top'：将缺失值排名为最高, 'bottom'：将缺失值排名为最低。}, default 'keep

- 给Series排名

```python
import pandas as pd
import numpy as np

data = {'A': [1, 2, np.nan, 3, 4],
        'B': [4, 4, 3, np.nan, 1]}
df = pd.DataFrame(data)
df['A_rank'] = df['A'].rank(method='min', na_option='keep')  # 使用类似sql的dense，不对缺失值排名
df['B_rank'] = df['B'].rank(method='first', na_option='top') # 使用类似sql的row_number，给予缺失值最高排名

>>>df
    A    B  A_rank  B_rank
0  1.0  4.0     1.0     4.0
1  2.0  4.0     2.0     5.0
2  NaN  3.0     NaN     3.0
3  2.0  NaN     2.0     1.0
4  4.0  1.0     4.0     2.0
```

- 实现sql中的dense_rank、rank、row_number

```python
# dense_rank
import pandas as pd
data = {'Group': ['A', 'A','A', 'B','B', 'B', 'B'],
        'Value': [2, 2, 1, 7,5, 5,1]}
df = pd.DataFrame(data)
df['rk']=df.groupby('Group')['Value'].transform(lambda x: x.rank(method="dense")).astype(int) # 把浮点型变成int
>>df
	Group  Value  rk
0     A      2   2
1     A      2   2
2     A      1   1
3     B      7   3
4     B      5   2
5     B      5   2
6     B      1   1

# rank
data = {'Group': ['A', 'A','A', 'B','B', 'B', 'B'],
        'Value': [2, 2, 1, 7,5, 5,1]}
df = pd.DataFrame(data)
df['rk']=df.groupby('Group')['Value'].transform(lambda x: x.rank(method="min")).astype(int) # 换成min即可
>>df
  Group  Value  rk
0     A      2   2
1     A      2   2
2     A      1   1
3     B      7   4
4     B      5   2
5     B      5   2
6     B      1   1

# row_number
data = {'Group': ['A', 'A','A', 'B','B', 'B', 'B'],
        'Value': [2, 2, 1, 7,5, 5,1]}
df = pd.DataFrame(data)
df['rk']=df.groupby('Group')['Value'].transform(lambda x: x.rank(method="first")).astype(int) # # 换成first即可
>>df
 Group  Value  rk
0     A      2   2
1     A      2   3
2     A      1   1
3     B      7   4
4     B      5   2
5     B      5   3
6     B      1   1
```

## 4、cut:将连续型数据按照数值区间划分成离散型变量

格式：df["new"]=pd.cut(x,bins,labels)

参数：x : array-like， The input array to be binned. Must be 1-dimensional,必须是一维的，例如Series

```
import pandas as pd
data = {'Name': ['Alice', 'Bob', 'Charlie', 'David', 'Eve'],
        'Age': [15, 20, 17,30, 45]}
df = pd.DataFrame(data)
bins = [0,18,30, 50]  # (0,18]、(18,30]....
labels = ['未成年人', '青年人', '中年人']
df['AgeGroup'] = pd.cut(df['Age'], bins=bins, labels=labels)
```

















  
