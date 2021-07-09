[TOC]

# MATLAB学习笔记

## 矩阵

### 矩阵的创建

>  所有 MATLAB 变量都是矩阵，这意味着每个变量均可以包含多个元素。单个称为标量的数值实际上是一个 1×1 矩阵，也即它包含 1 行 1 列。

#### 一般矩阵

* 使用方括号创建包含多个元素的矩阵。

    ```matlab
    >> x = [3 5]
    x = 
    	3    5
    ```

* 用空格（或逗号）分隔数值时，MATLAB会将这些数值组合为一个行向量，行向量是一个包含一行多列的矩阵 (1×n)。当您用分号分隔数值时，MATLAB 会创建一个列向量 (n×1)

    ```matlab
    >> x = [1;3]
    x =
        1
        3
    ```

#### 等间距矩阵

* 我们经常需要创建一些包含等间距数值的向量，例如：

    ```matlab
    >> y = [5 6 7 8]
    y = 
        5    6    7    8
    ```

* 对于长向量，输入单个数值是不实际的。可用来创建等间距向量的替代便捷方法是使用 `:` 运算符并仅指定起始值和最终值：`first : last`。

    ```matlab
    y = 5:8
    y = 
        5    6    7    8
    ```

* `:` 运算符使用默认的间距 `1`，但是您可以指定您自己的间距：`first : step : last`。

    ```matlab
    >> x = 20:2:26
    x = 
        20    22    24    26
    ```

* 如果您知道向量中所需的元素数目（而不是每个元素之间的间距），则可以改用 `linspace` 函数：`linspace(first,last,number_of_elements)`。注意，请使用逗号 (`,`) 分隔 `linspace` 函数的输入。

    ```matlab
    >> x = linspace(0,1,5)
    x = 
        0    0.250    0.500    0.750    1.000
    ```

* `linspace` 和 `:` 运算符都可创建行向量。

* `logspace` - 生成对数间距向量

#### 特殊矩阵

##### 通用特殊矩阵

* `zeros()`：全0矩阵

* `ones()`：全1矩阵
* `eye()`：产生对角线为1的矩阵，当矩阵为方阵时为单位阵
* `rand()`：0~1之间的均匀分布的随机矩阵
* `randn()`：产生均值为0，方差为1的标准正态分布随机矩阵

##### 专门学科应用的特殊矩阵

* 魔方矩阵`magic()`：形成幻方

* 范德蒙矩阵`vander(V)`形成以向量`V`为基础的范德蒙矩阵

* 希尔伯特矩阵`hilb(n)`形成n阶希尔伯特矩阵

  > H(i,j) = 1/(i+j+1)

* 伴随矩阵`compan(p)`生成以向量p为多项式系数的伴随矩阵

* 帕斯卡矩阵`pascal(n)`，帕斯卡矩阵的逆矩阵也为整数

  > P(i,1) = P(1,j) = 1;	P(i,j) = P(i,j-1)+P(i-1,j);	杨辉三角形

#### 结构矩阵和元胞矩阵

##### 结构矩阵

**结构体数组**是使用名为**字段**的数据容器将相关数据组合在一起的数据类型。每个字段都可以包含任意类型的数据。可以使用 `structName.fieldName` 格式的圆点表示法来访问字段中的数据。

```matlab
>> data.x = linspace(0,2*pi);
>> data.y = sin(data.x);
>> data.title = 'y = sin(x)';

data = struct with fields:
        x: [1x100 double]
        y: [1x100 double]
    title: 'y = sin(x)'
```

* 可以采用`s = struct(field,value)` 创建具有指定字段和值的结构体数组。`value` 输入参数可以是任何数据类型，例如数值、逻辑值、字符或元胞数组。
  * 如果 `value` *不是*元胞数组，或者 `value` 是标量元胞数组，则 `s` 是标量结构体。例如，`s = struct('a',[1 2 3])` 创建一个 1×1 结构体，其中 `s.a = [1 2 3]`。
  * 如果 `value` 是非标量元胞数组，则 `s` 是维度与 `value` 相同的结构体数组。`s` 的每个元素包含 `value` 的对应元素。例如，`s = struct('x',{'a','b'})` 返回 `s(1).x = 'a'` 和 `s(2).x = 'b'`。
  * 如果 `value` 是空元胞数组 `{}`，则 `s` 是空 (0×0) 结构体。

* `s = struct(field1,value1,...,fieldN,valueN)` 创建一个包含多个字段的结构体数组。
  
* 如果 `value` 的所有输入都不是元胞数组，或者 `value` 中作为元胞数组的所有输入都是标量，则 `s` 是标量结构体。
  
* 如果任一 `value` 输入是非标量元胞数组，则 `s` 具有与该元胞数组相同的维度。此外，如果两个或多个 `value` 输入是非标量元胞数组，则它们必须都具有相同的维度。
  
  对于类型为标量元胞数组或任何其他数据类型的数组的任何 `value`，`struct` 将 `value` 的内容插入 `s` 的所有元素的相关字段中。例如，`s = struct('x',{'a','b'},'y','c')` 返回 `s(1).x = 'a'`、`s(2).x = 'b'`、`s(1).y = 'c'` 和 `s(2).y = 'c'`。
  
  * 如果任何 `value` 输入是空元胞数组 `{}`，则输出 `s` 是空 (0×0) 结构体。要指定一个空字段并保留其他字段的值，请改用 `[]` 作为 `value` 输入。

##### 元胞矩阵

**元胞数组**是一种包含名为**元胞**的索引数据容器的数据类型，其中的每个元胞都可以包含任意类型的数据。元胞数组通常包含文本列表、文本和数字的组合或者不同大小的数值数组。通过将索引括在圆括号 `()` 中可以引用元胞集。使用花括号 `{}` 进行索引来访问元胞的内容。

* 当要将数据放入一个元胞数组中时，请使用元胞数组构造运算符 `{}` 创建该数组。

```matlab
>> C = {1,2,3;'text',rand(5,10,2),{11; 22; 33}}
C = 2x3 cell array
    {[   1]}    {[          2]}    {[     3]}
    {'text'}    {5x10x2 double}    {3x1 cell}
```

* 您也可以使用 `{}` 创建一个空的 0×0 元胞数组。

```matlab
>> C = {}
C =

  0x0 empty cell array
```

* `C = cell(n)` 返回由空矩阵构成的 `n`×`n` 元胞数组。

* `C = cell(sz1,...,szN)` 返回由空矩阵构成的 `sz1`×...×`szN` 元胞数组，其中，`sz1,...,szN` 表示每个维度的大小。例如，`cell(2,3)` 返回一个 2×3 元胞数组。

* `C = cell(sz)` 返回由空矩阵构成的元胞数组，并由大小向量 `sz` 来定义数组大小 `size(C)`。例如，`cell([2 3])` 会返回一个 2×3 元胞数组。

### 矩阵的操作

#### 基本操作

* 转置

  * `'`：转置之后再取每个数的复共轭
  * `.'`：转置运算符

* 矩阵的旋转
  
* `rot90(A,k)`：将矩阵A**逆时针**旋转90°的k倍
  
* 矩阵的翻转
  * `fliplr(A)`：矩阵A进行左右翻转
  * `flipud(A)`：矩阵A进行上下翻转
* `flipdim(A,dim)`：指定沿某个维度翻转，适用于高维矩阵，比如左右翻转即为2维翻转
  
* 求大小
  * `size` 函数可以应用于矩阵，以生成包含矩阵大小的单个输出变量。

	  ```matlab
	  >> s = size(x)
	  ```

  * `size` 函数可以应用于矩阵，以生成单个输出变量或两个输出变量。使用方括号 `[ ]` 获取多个输出。

	  ```matlab
	  >> [xrow,xcol] = size(x)
	  ```
  
  * `length(A)`：返回尺寸的最大值，即`max(size(A))`
  
  * `numel(A)`：矩阵所有元素的个数
  
* 求逆矩阵

	```matlab
	A = [1,2;2,3];
	T = A^-1;
	T = inv(A);
	```

* 对角操作
  * 提取矩阵对角元素
    * `diag(A)`：提取矩阵A主对角线元素，形成一个列向量
    * `diag(A,k)`：提取矩阵A第k条对角线元素，形成列向量，其中主对角线k=0；主对角线向上依次为1,2,3……，往下依次为-1,-2,-3……
  * 构造对角阵
    * `diag(V)`：以向量V为主对角元素产生对角阵
    * `diag(V,k)`：以向量V为第k条对角线元素，产生对角矩阵

* 三角阵操作
  * `triu()`与`tril()`分别表示上三角阵(triangle up)和下三角阵(triangle low)，两个函数操作类似
  * `triu(A)`：提取矩阵A主对角以上的元素，形成上三角阵
  * `triu(A,k)`：提取矩阵A第k条对角线以上的矩阵
  
* 矩阵重塑

  * `reshape(A,m,n)`：将矩阵A改为m行n列的矩阵
  * 注意：MATLAB在内存中矩阵按照列的顺序存放，reshape时也是按照内存的顺序重新排列

#### 矩阵索引

##### 单个矩阵元素索引

* 您可以使用行、列索引从矩阵中提取值。

  ```matlab
  >> x = A(5,7);
  ```

  此语法将会提取 `A` 的第 5 行第 7 列的值，然后将结果赋给变量 `x`。

* 您可以使用 MATLAB 关键字 `end`，作为行或列索引来引用最后一个元素。

  ```matlab
  >> x = A(end,2);
  ```

* 请注意，您可以将算术运算与关键字 `end` 结合使用。

  ```matlab
  >> x = A(end-1,end-2)
  ```

* 单个索引值可用于引用向量元素。例如，以下语句

  ```matlab
  >> x = v(3)
  ```

  会返回向量 `v` 的第 3 个元素（当 `v` 为行向量或列向量时）。

##### 多个元素提取

* 用作索引时，冒号运算符 (`:`) 可指代该维度中的所有元素。以下语法：

  ```matlab
  >> x = A(2,:)
  ```

  会创建一个包含 `A` 中第 2 行上所有元素的行向量。

* 冒号运算符可以引用某个值范围。以下语法会创建一个包含矩阵 `A` 的第 1 行、第 2 行和第 3 行所有元素的矩阵。

  ```matlab
  >> x = A(1:3,:)
  ```

* 单个索引值范围可用于引用向量元素的子集。例如

  ```matlab
  >> x = v(3:end)
  ```

  返回向量 `v` 的一个子集，其中包含从第三个到最后一个元素范围内的所有元素。

##### 矩阵的增加和削减

* 增加

  ```matlab
  x = [1,2,3]
  x(end+1) = 4;
  
  x = [1,2,3,4]
  
  x(end+1:end+3) = [5 6 7]
  
  x = [1 2 3 4 5 6 7]
  ```

* 削减

  用空集表示`[]` 即可

### 矩阵的运算

####  矩阵加法

* 您可以将一个标量值与矩阵中的所有元素相加。

  ```matlab
  >> y = x + 2
  ```

* 您可以将任意两个大小相同的矩阵相加。

  ```matlab
  >> z = x + y
  ```

#### 矩阵数乘

* 您可以将矩阵中的所有元素与某个标量相乘或相除。

  ```matlab
  >> z = 2*x
  >> y = x/3
  ```

#### 矩阵乘法

* `*` 运算符执行[矩阵乘法](http://www.mathworks.com/help/matlab/ref/mtimes.html)。请确保向量维度一致。

* 而 `.*` 运算符执行按元素乘法，允许您将两个大小相同的矩阵的对应元素相乘。

  ```matlab
  >> z = [3 4] .* [10 20]
  z = 
      30    80
  ```
  
* 矩阵叉乘查阅命令`cross`

#### 矩阵除法

当矩阵A为非奇异矩阵时

* `B/A` 等价于`B*inv(A)` 
* `A\B` 等价于`inv(A)*B` 

#### 矩阵求值

* 行列式`det(A)`：求方阵A的行列式
* 秩`rank(A)`：求矩阵A的秩
* 迹`trace(A)`：求矩阵A的迹（矩阵对角线之和，特征值之和）
* 范数：度量矩阵或者向量在某种意义下的长度
  * 向量1-范数`norm(V,1)`：向量V元素绝对值之和
  * 向量2-范数`norm(V)`或`norm(V,2)`：向量V元素平方和的平方根
  * 向量∞-范数`norm(V,inf)`：向量V中所有向量元素绝对值中的最大值
  * 矩阵1-范数`norm(A,1)`：矩阵A列元素绝对值之和的最大值
  * 矩阵2-范数`norm(A)`或`norm(A,2)`：矩阵A‘A的最大特征值的平方根
  * 矩阵∞-范数`norm(A,inf)`：所有矩阵行元素绝对值之和的最大值
* 条件数：描述矩阵性能的数，为矩阵范数和矩阵的逆的范数之积，条件数越接近1，矩阵性能越好
  * `cond(A,1)`：计算A的1-范数下的条件数
  * `cond(A)`或`cond(A,2)`：计算A的2-范数下的条件数
  * `cond(A,inf)`：计算A的∞-范数下的条件数
* 特征值和特征向量
  * `E = eig(A)`：求矩阵A的全部特征值，构成向量E
  * `[X,D] = eig(A)`：矩阵A的全部 特征值构成对角阵D，矩阵X的每一列为D特征值对应的特征向量
  * 特征值的几何意义可以采用`eigshow(A)`

#### 稀疏矩阵

* 完全存储和稀疏存储方式的转化
  * `A = sparse(S)`：将矩阵S转化为稀疏存储方式的矩阵A
  * `S = full(A)`：将矩阵A转化为完全存储方式的矩阵S
* 直接建立稀疏存储矩阵
  * `sparse(m,n)`：生成m×n的所有元素都是0的稀疏矩阵
  * `sparse(u,v,S)`：u，v，S为三个等长向量。S为要建立的稀疏存储矩阵的非零元素，u(i)和v(i)分别为S(i)的行和列下标
  * `B =  spconvert(A)`：A为m×3或者m×4的矩阵，m为非零元素个数。第一列为行号，第二列为列号，第三列为对应实部，第四行为对应虚部（无则省）
* 有规则的稀疏矩阵
  * 带状稀疏矩阵的稀疏存储
    * `[B,d]=spdiags(A)`：从带状稀疏矩阵A中提取全部非零对角线元素赋值给矩阵B及其这些对角线的编号d
    * `A=spdiags(B,d,m,n)`：产生稀疏矩阵A，其中m,n为稀疏矩阵A的行数和列数，B,d与上述同。矩阵B 的第i列位于带状稀疏矩阵的第d(i)个
    * 单位矩阵的稀疏存储：`speyes(m,n)`：返回一个m×n的稀疏存储的单位矩阵

## 逻辑运算和变量

### 逻辑运算

#### 关系运算符

* [关系运算符](https://www.mathworks.com/help/matlab/matlab_prog/array-comparison-with-relational-operators.html)（例如 `>`、`<`、`==` 和 `~=`）执行两个值之间的比较。相等或不相等比较的结果为 `1` (`true`) 或 `0` (`false`)。

* 您可以使用关系运算符将某个向量或矩阵与单个标量值进行比较。结果是与原始矩阵相同大小的逻辑矩阵。

  ```matlab
  >> [5 10 15] > 12
  ans = 
      0    0    1
  ```

* 可以使用关系运算符对两个矩阵的对应元素进行比较。这两个矩阵的大小必须相同，其比较结果是与这两个矩阵具有相同大小的逻辑矩阵。

  ```matlab
  >> [5 10 15] > [6 9 20]
  ans = 
      0    1    0
  ```

#### 逻辑运算符

* MATLAB 包含 AND (`&`) 和 OR (`|`) 等逻辑运算符，可将多个逻辑条件组合在一起。如果两个元素都为 `true`，`&` 运算符将返回 `true` (`1`)，否则返回 `false` (`0`)。例如：

  ```matlab
  >> x = (pi > 5) & (0 < 6)
  x =
       0
  ```
  
* 或者采用函数也可以`and(x,y)`与,`or(a,b)`或,`xor(a,b)`异或,`not(a)`非

#### [逻辑索引](http://www.mathworks.com/help/matlab/math/matrix-indexing.html)

* 您可以使用逻辑矩阵作为矩阵索引，在这种情况下，MATLAB 会提取索引为 `true` 的矩阵元素。以下示例将会提取 `v1` 中大于 6 的所有元素。

  ```matlab
  >> v = v1(v1 > 6)
  v =
      6.6678
      9.0698
  ```

* 您可以使用逻辑索引在矩阵中重新赋值。例如，如果您要将矩阵 `x` 中等于 `999` 的所有值都替换为 `0`，请使用以下语法。

  ```matlab
  x(x==999) = 0
  ```

### 保存和加载变量

* 您可以使用 `save` 命令将工作区中的变量保存到称为 MAT 文件的 MATLAB 特定格式文件中。

  ```matlab
  >> save foo x
  ```

  以上命令将名为 `x` 的变量保存到名为 `foo.mat` 的 MAT 文件中。

* `clear`可以将工作区域的变量清空

* `clc`可以清空命令行

* 您可以使用 `load` 命令从 MAT 文件加载变量。

  ```matlab
  >> load foo
  ```

  `foo.mat` 中的变量将会加载到工作空间中

## 脚本编程

### `if-else`

```matlab
x = rand;
if x > 0.5
    y = 3;
elseif x > 0.9
    y = 4;
else 
	y = 5;
end
```

* 判断条件如果为矩阵，则矩阵非空且无零时为true
* 更多资源参考：[if 语句](http://www.mathworks.com/help/matlab/ref/if.html) 

### `switch-case`

```matlab
x = input('输入一个0-9的数字');
switch x
	case {1:5}
		disp(2*x);
    case {0,6:9}
    	disp(3*x);
    otherwise
    	disp('超出范围');
end
```

* 更多资源参考：[switch 语句](http://www.mathworks.com/help/matlab/ref/switch.html) 

### `For` 

* 常见的编程任务是重复执行某个代码段。在 MATLAB 中，您可以使用 `for` 循环完成该操作。

  ```matlab
  for i = 1:2:10
      disp(i)
  end
  ```

  请注意，`for` 循环包含单个 `end` 关键字，与 `if` 语句类似。

  运行此代码时，`for` 和 `end` 关键字之间的代码在该示例中将被执行5次，i的最终结果为9。

* `for`语句的循环变量也可以是矩阵，每次循环将矩阵每一列赋给循环变量，一共循环总列数次

### `while`

* 与一般高级语言规则相同，包括`break`和`continue`

### 自定义函数

* 通用格式

  ```matlab
  function 函数返回参数 = 函数名（函数输入参数）
  ```

  如果函数返回参数多于一个，则需要采用`[]`将参数括起来

  函数可以有`return`语句，碰到此语句时函数直接结束并返回，也可以没有，程序结束自动返回

* 匿名函数

  ```matlab
  函数句柄变量（别名）= @(匿名函数输入参数)匿名函数表达式
  ```

  如`f = @(x,y)x*x+x*y+y*y`定义了一个匿名函数$f(x,y) = x^2+xy+y^2$，之后可以直接调用，如`f(2,3)`

  ```matlab
  函数句柄变量 = @函数名
  ```

  此时的函数名可以为自定义函数或者内部函数，相当于为函数取了别名

  如`h = @sin`，之后可以调用`h(pi/2)`得到结果为`ans=1`

* 函数的重构

  * `nargin()`和`nargout()`可以判断函数的输入和输出参数个数

* 全局变量的定义：`global Name`。全局变量的作用域是整个工作空间，需要在所有使用它的地方声明

### 程序控制

* `input(‘提示信息’)`用于接收用户输入
* `disp(输出项)`用于输出数据
* `pause(延迟秒数)`若无参数则程序暂停，直到按任意键继续

## 绘图表达

更多绘图参考：[MATLAB 绘图类型](https://ww2.mathworks.cn/help/matlab/creating_plots/types-of-matlab-plots.html)

### 图形绘制和完善

#### 一般二维线图

##### `plot()`

* 可以使用 `plot` 函数在一张图上绘制两个相同长度的向量。

  ```matlab
  >> plot(x,y)
  ```

* 要在一张图上先后绘制两条线，请使用 `hold on` 命令保留之前的绘图，然后添加另一条线。您也可以使用 `hold off` 命令返回到默认行为。

* 输入 `close all` 命令以关闭所有打开的图窗窗口。

  * 当您单独绘制一个向量时，MATLAB 会使用向量值作为 y 轴数据，并将 x 轴数据的范围设置为从 `1` 到 `n`（向量中的元素数目）。 

* 绘制的图标有两个y轴坐标，用函数`plotyy()`

* 更多详细用法详见：[ Plot -- 二维线图](https://ww2.mathworks.cn/help/matlab/ref/plot.html?s_tid=doc_ta)

##### `fplot()`

* `fplot(f,lims,选项)`

  > fplot会根据函数的变化趋势自动设置绘制点的间隔以更好反映函数的变化趋势

  f表示函数，通常采用函数句柄方式表示。lims为x轴的取值范围，用二元向量[xmin,xmax]表示，默认为[-5,5]。选项定义和plot相同，包括线性颜色点标记等。

* `fplot(funx,funy,tlims,选项)`

  > 绘制双输入函数，比如参数方程

  ```matlab
  fplot(@(t)t.*sin(t),@(t)t.*cos(t),[0,10*pi],'b');	% 绘制螺旋线，注意点乘
  ```

##### `ezplot()`

* easy plot，简单绘图

  ```matlab
  >> f = inline('1/x-(x-1)')
  
  f =
  
       内联函数:
       f(x) = 1/x-(x-1)
  
  >> ezplot(f,0,4)
  % 绘制 f 函数在 （0,4）之间的图像
  ```


##### `subplot()`

将图形窗口分区域，如`subplot(2,2,3)`表示将当前窗口分为四个绘图区域，并且在编号为3的绘图区域内绘图。

#### 其他坐标的二维线图

##### 对数坐标图

* `semilogx()` x 为对数 y 为常数
* `semilogy()` x 为常数 y 为对数
* `loglog()` x,y均为对数坐标

##### 极坐标图

* `polar(theta,rho,选项)`：theta为极角，rho为极径

##### 统计图

* 条形图：`bar()`和`barh()`分别绘制垂直和水平条形图
* 直方图：`hist()`和`rose()`分别绘制直角坐标和极坐标下的直方图
* 面积类：`pie()`绘制扇形图；`area()`绘制面积图
* 散点类：`scatter()`散点图；`stairs()`阶梯图；`stem()`杆图
* 矢量图：`compass()`罗盘图；`feather()`羽毛图；`quiver()`箭头图

#### 三维线图

##### 三维曲线

* `plot3(x,y,z)`

* `fplot3(funx,funy,funz,tlims)`

##### 三维曲面

* 形成平面网格数据：`[X,Y]=meshgrid(x,y)`：其中x,y为对应点的行，列向量，形成矩阵`[X,Y]`为平面网格矩阵
* `mesh(x,y,z,c)`：绘制三维网格图，x,y为网格坐标矩阵，z为高度矩阵，c指定不同高度下的颜色
* `surf(x,y,z,c)`：绘制三维曲面图，x,y为网格坐标矩阵，z为高度矩阵，c指定不同高度下的颜色
* `meshc()`：带等高线的三维网格曲面
* `meshz()`：带底座的三维网格曲面
* `surfc()`：具有等高线的曲面函数
* `surfl()`：具有光照效果的曲面函数
* `sphere(n)`：绘制三维球面，n表示光滑度，默认为20
* `cylinder(R,n)`：绘制三维柱面，n为圆上点，n越多越光滑，R为不同高度的半径
* `fsurf(funx,funy,funz,uvlims)`和`fmesh(funx,funy,funz,uvlims)`：参数绘制，`uvlims`表示`[umin,umax,vmin,vmax]`，是参数的范围
* `contour()`：根据三维数据绘制等高线图

#### 图形完善

##### 图形标注

* 可以使用绘图注释函数（例如 `title`）在绘图中添加标签。这些函数的输入是一个字符串。MATLAB 中的字符串是用单引号 (`'`) 引起来的。

  ```matlab
  >> title('Plot Title')
  ```

* `xlabel()` 和 `ylabel()` 用于添加横纵坐标轴标签。参数均为字符串。

* 使用 `legend` 函数指定图例。以标记绘制图例的线型和变量名。

  ```matlab
  >> legend('a','b','c')
  ```
  
* `axis([xmin,xmax,ymin,ymax,zmin,zmax])`：设置坐标轴范围

* `axis equal`：横纵坐标采用等长刻度；`axis square`：产生正方形坐标系

* `grid on`显示网格线，`text(x,y,文本)`在指定(x,y)坐标处显示注释文本

* 更多用法详见：[函数  legend](https://ww2.mathworks.cn/help/matlab/ref/legend.html?s_tid=doc_ta)

##### 图形修饰

* 视点处理：`view()`从不同角度观察图形
* 色彩处理
  * `colormap()`改变色图渲染矩阵
  * `shading faceted`：网格线为黑色，曲面为高度对应颜色
  * `shading flat`：每个网格片和颜色用相同颜色
  * ` shading interp`：网格片内采用颜色插值，最光滑
* 裁剪处理：将网格数据赋值为`NaN`可以进行裁剪

### 图形用户界面设计

#### 图形窗口与坐标轴

* 句柄：用句柄来标识图形对象，可以通过句柄访问图形对象的属性。访问图形对象：对象句柄.属性名

  * gcf：获取当前图形窗口的句柄
  * gca：获取当前坐标轴的句柄
  * gco：获取最近被选中图形对象的句柄
  * findobj：按照指定的属性来获取图形对象的句柄

  图形对象的常用公共属性：Children，parent，type，tag

  **坐标轴对象是图形窗口的子对象**

  **图形对象是坐标轴对象的子对象**

* 窗口：句柄变量=figure(属性1,属性值1,···)

  图形窗口的属性

  * MenuBar：控制图形窗口是否显示菜单条，'none'无，'figure'默认
  * Name：图形窗口标题
  * NumberTitle：图形窗口的标题中是否以“Figure n”为标题前缀
  * Color：图形窗口背景颜色
  * Position：[x,y,w,h]定义窗口在平面上的大小和位置
  * Units：定义图形窗口使用的长度单位：'pixels'像素 'inches'英寸 'centimeters'厘米 'points'磅 'normalized'相对单位，左下角(0,0) 右上角(1,1)，默认为像素

* 坐标轴对象：句柄变量=axes(属性1,属性值1,···)

  坐标轴属性：

  * Position：[x,y,w,h]定义坐标轴在图形窗口中的大小和位置
  * Units：坐标轴度量单位，默认为相对坐标单位
  * Box：坐标轴是否带有边框，可以取'on'或'off'（默认）
  * GridLineStyle：网格线类型‘:’（默认）'-'  '-.'  '--'  'none'
  * Title：对坐标轴对象进行操作，取值是通过title函数建立的标题对象的句柄
  * XLabel,YLabel,ZLabel：通过xlabel,ylabel,zlabel函数建立的坐标轴标签对象的句柄
  * XLim,YLim,ZLim：定义各个坐标轴的上下限，取值是向量[Lmin,Lmax]
  * XScale,YScale,ZScale：定义各坐标轴的刻度类型，可取'linear'和'log'
  * View：定义视点，取值为向量[az,el]，az定义方位角，el定义视点仰角
  * ColorOrder：设置多条曲线的颜色顺序，是一个n×3的矩阵，矩阵每一行用RGB表示一种颜色，默认n为7，该坐标轴绘图将会依次选取其中的颜色绘制图形

#### 曲线与曲面对象

* 曲线对象：句柄变量=line（x,y,z,属性1,属性值1,···）

  曲线属性：

  * Color：曲线颜色，默认为黑色
  * LineStyle：线性，默认为实线'-'
  * LineWidth：线宽，默认为0.5磅
  * Marker：数据点标记符号，默认'none'
  * MarkerSize：数据点标记符号大小，默认6磅
  * XData，YData，ZData：设置三个坐标轴的数据源

* 曲面对象：surface(x,y,z,c,属性1,属性值1,···)

  曲面属性：除了上述以外还有

  * FaceColor：曲面网格片的颜色，可以取值
    * 'flat'：每一个网格片用单一颜色填充
    * 'interp'：用渐变方式填充网格线
    * 'none'：网格片无颜色
    * 'texturemap'：用Cdata属性定义的颜色填充网格片
    * RGB向量或者代表颜色的字符

  * EdgeColor：定义曲面网格线的颜色，有'flat'  'interp'  'none'默认为[0,0,0]

* 此外图形显示还有光照处理和反射处理

#### 图形用户界面设计方法

* 控件对象和操作：句柄变量=uicontrol()

* 菜单对象：句柄变量=uimenu()

* GUIDE是MATLAB的可视化用户界面开发环境，可以可视化地开发用户操作界面，在命令行输入命令guide可以打开

  更多相关知识查看网址：[使用 GUIDE 创建简单的 App](https://ww2.mathworks.cn/help/matlab/creating_guis/about-the-simple-guide-gui-example.html)

## 常用函数

### 数值计算函数

> MATLAB 的函数可在单个命令中对**整个向量或值矩阵**执行数学运算。

* `max()`

	> 可以使用 `max` 函数确定向量的最大值及其对应的索引值。`max` 函数的第一个输出为输入向量的最大值。执行带两个输出的调用时，第二个输出为索引值。
	>
	> max函数按列比较，如果输入为矩阵，输出为行向量，每个为当前列最大值和最大值元素行号，dim为1时以列为单位比较，dim为2时以行为单位比较

	```matlab
	>> [xMax,idx] = max(x)
	>> xMax = max(x,[],dim)
	```

* `mean()` `median()` `sum()` `prod()`

	平均值，中值函数，求和，求积，参数和返回值与`min() max()`一致

* `comsum()`和`comprod()`：求累加和和累乘积

* `std()`：计算标准差函数

  ```matlab
  >> std(X)
  >> std(A)
  >> std(A,flag,dim)
  ```

  其中X为向量，A为矩阵，flag是0计算样本标准差，flag为1计算总体标准差。dim同上，1列2行。

* `corrcoef()`：相关系数

  ```matlab
  >> corrcoed(A)
  >> corrcoed(X,Y)
  ```

  返回矩阵，其中第（i,j）个元素表示第 i **列**和第 j **列**之间的相关系数，第二个X和Y为向量，等价于A = [X,Y]

* `sort()`：排序函数

	```matlab 
	>> sort(X)
	>> [Y,I] = sort(A,dim,mode)
	```

X为向量，升序排列。A为矩阵，dim表示以列为单位还是以行为单位，mode指明是升序还是降序，'ascend'为升序，’descend‘为降序，Y为排序后的矩阵，I记录当前位置元素在A的位置。
	
* `rem(x, y)` & `mod(x,y)`

	>计算 x 除以 y 的余数
	>
	>rem(x,y) 计算结果和 x 正负号相同
	>
	>mod(x,y)计算结果和 y 正负号相同

	```matlab
	>> x = [37, -37, 37, -37]';
	>> y = [10, 10, -10, -10]';
	>> r = [x, y, rem(x,y), mod(x,y)]
	
	r =
	
	    37    10     7     7
	   -37    10    -7     3
	    37   -10     7    -3
	   -37   -10    -7    -7
	```

### 多项式计算函数

* `conv(P1,P2)`：多项式相乘，P1P2为两个多项式向量
* `[Q,r]=deconv(P1,P2)`：Q为P1除以P2的商式，r为余式，即P1 = conv(Q,P2) + r 
* `polyder()`：多项式求导
  
  * `p = polyder(P)`：多项式P的导函数
  * `p = polyder(P,Q)`：多项式P·Q的导函数
  * `[p,q] = polyder(P,Q)`：多项式P/Q，p为分子系数，q为分母系数
* `polyval(p,x)`：多项式求值，P为多项式向量，x为自变量，可以为数值，向量，矩阵，实行点运算
* `polyvalm(p,x)`：多项式矩阵求值，x必须为方阵，实行的是矩阵运算

* `roots()`：多项式求根，可以求复根

    > 多项式求零点函数，向量表示各项系数，返回列向量表示根
    ```matlab
    >> p = [1, -3, 1];
    >> r = roots(p)
    
	r =
    
	    2.6180
        0.3820
    ```
    
	得到结果为多项式$$f(x)=x^2-3x+1$$的根
	
* `poly(x)`：x为多项式的全部根，可以反推出多项式的系数


* `vpa(x,n)`：变精度运算函数，计算数值x给定精度n的数值
* `[r,p,k]=residue(b,a)`：部分分式展开，`b`、`a`分别为分子分母多项式系数矢量，`r`、`p`、`k`分别是留数、极点和直项矢量


### 插值函数

* 一维插值函数`Y1=interp1(X,Y,X1，method)`：根据X,Y的值，计算在X1处的值。

  其中X，Y为两个等长向量，分别表示采样点和采样值，X1是一个向量或者标量，表示要插值的点。method表示插值方法，具体如下：

  1. `linear`：线性插值，默认方法。将于插值点靠近的两个数据点用直线连接，在直线上选取对应插值点的数据
  2. `nearest`：最近点插值，选择距离最近的样本点的值作为插值数据，如果为中点则选择后面的点
  3. `pchip`：分段3次埃米尔特插值。采用分段三次多项式，除了满足插值条件，还需要满足在若干结点处相邻段插值函数的一阶导数相等，使得曲线光滑的同时，还具有保形性(即函数值不会过度偏离给定的数据)
  4. `spline`：3次样条插值。每个分段内构造一个三次多项式，使其插值函数除了满足插值条件外，还要求在各节点处具有连续的一阶和二阶导数

* 拉格朗日插值法会根据n个采样点形成一个n-1次多项式

  可以采用如下方法得到：采样点坐标为向量x,y，V=vander(x)得到x的范德蒙矩阵，拟合多项式的系数矩阵c = V\y'

  >  此方法适用于间隔均匀，函数值变化不大的函数，对于一般性问题此方法有时是有危险的

  函数`polyinterp()`是基于拉格朗日形式的插值函数

* 二维插值函数`Z1=interp2(X,Y,Z,X1,Y1,method)`：根据X,Y,Z的值计算在X1,Y1处的值

  其中X,Y是两个向量，表示采样点，Z是采样点对应的函数值。X1，Y1是两个标量或者向量，表示要插值的点

### 曲线拟合

* 多项式拟合函数`polyfit()`：求得最小二乘拟合多项式系数

  1. `P=polyfit(X,Y,m)`
  2. `[P,S]=polyfit(X,Y,m)`
  3. `[P,S,mu]=polyfit(X,Y,m)`

  根据样本数据X,Y产生一个m次多项式P及其在采样点误差数据S，mu是一个二元向量，mu(1)是mean(X)（均值）；mu(2)是std(X)（标准差）

## 数值微积分与方程求解

### 数值微分与数值积分

* 数值微分

  `diff` - 差分和近似导数：此 MATLAB函数计算沿大小不等于 1 的第一个数组维度的 X 相邻元素之间的差分；

  * `Y = diff(X)`：计算向量X的一阶向前差分，$dx(i)=x(i+1)-x(i)，i=1,2,···，n-1$
  * `Y = diff(X,n)`：计算向量X的n阶向前差分，如`diff(x,2)=diff(diff(x))`
  * `Y = diff(X,n,dim)`：计算矩阵A的n阶差分，dim=1时按列计算差分，dim=2按行计算差分

* 数值积分

  * `[I,n]=quad(filename,a,b,tol,trace)`：基于自适应辛普森方法
  * `[I,n]=quadl(filename,a,b,tol,trace)`：基于自适应高斯洛巴托方法

  > filename为被积函数名；a,b为定积分上下限，必须有限，**不能为无穷大**；tol用于控制积分精度，默认为$10^{-6}$；trace控制是否展现积分过程，非0展现，0不展现，默认为0；返回I为积分值，n为被积函数调用次数；

  * `I=integral(filename,a,b)`：基于全局自适应积分方法

  > 其中I是积分结果，filename为被积函数，a,b为积分上下限，**可为无穷大**

  * `[I,err]=quadgk(filename,a,b)`：基于自适应高斯克朗罗德方法

  > **求振荡函数的定积分**，err为近似误差范围，积分上下限可为无穷大，也可为复数，如果上下限是复数，则quadgk函数是在复平面上求积分

  * `I=trapz(x,y)`：基于梯形积分法

  > 在**未知函数表达式**，只**知道一组采样点**时采用梯形积分法，x,y为等长的向量，x从x1变化到xn，y从y1变化到yn，且x1<x2<···<xn  
  > $$
  > I =\sum_{i=1}^{n-1}(x_{i+1}-x_i)(y_{i+1}+y_i)/2
  > $$

  * 重积分

    * 二重积分数值解  $\int_{c}^{d}\int_{a}^{b}f(x,y)dxdy$

      `I=integral2(filename,a,b,c,d)`

      `I=quad2d(filename,a,b,c,d)`

      `I=dblquad(filename,a,b,c,d,tol)`

    * 三重积分数值解  $\int_{e}^{f}\int_{c}^{d}\int_{a}^{b}f(x,y,z)dxdydz$

      `I=integral3(filename,a,b,c,d,e,f)`

      `I=triplequad(filename,a,b,c,d,e,f,tol)`

### 方程组求解

#### 线性方程组求解

* Ax=b  -> x=A\b，用左除运算符可以直接求解线性方程组（不推荐：复杂且不精确）

* 利用矩阵分解求线性方程组(LU分解)

  > 当矩阵是非奇异的时候，可以确保方阵A分解为一个上三角和一个下三角阵的乘积，即A=LU，此时再次利用左除运算符可以更快的计算出x的结果

  `[L,U]=lu(A)`：A=LU；Ax=b -> x = U \ ( L \ b )

  `[L,U,P]=lu(A)`：PA=LU；Ax=b -> x = U \ ( L \ P * b )

#### 非线性方程数值求解

* `x=fzero(filename,x0)`：单变量非线性方程求解，x0为初始值，采用迭代方法求值，x0的值很大程度上影响了求解的答案，并非一定是靠近的x0的零点值

  ```matlab
  >> f = @(x)x*x-3*x+1;	% MATLAB的匿名函数
  % 求0.5和3附近的零点值
  >> x1 = fzero(f,0.5)	
  
  x1 =
  
      0.3820
  
  >> x2 = fzero(f,3)
  
  x2 =
  
      2.6180
  ```

* `x=fsolve(filename,x0,option)`：x为返回的近似解，x0为初值，可以调用`optimeset（）`函数设置最优化工具箱中的优化参数`option`函数

#### 函数极值计算

* 无约束最优化问题：计算函数在区间内的极小值

  `[xmin,fmin]=fminbnd(filename,x1,x2,option)`

  `[xmin,fmin]=fminsearch(filename,x1,x2,option)`

  `[xmin,fmin]=fminunc(filename,x1,x2,option)`

  x1,x2为被研究区间的左右边界。x0是向量，表示极值点的初值。option为优化参数，通过调用`optimset()`函数来设置。

* 有约束的最优化问题：在约束条件下的极小值

  `[xmin,fmin]=fmincon(filename,x0,A,b,Aeq,beq,Lbnd,Ubnd,NonF,option)`

  xmin,fmin,filename,x0,option与最小值函数相同。其余为约束条件包括线性不等式约束，线性等式约束，x的下界和上界以及定义的非线性约束函数。如果某个约束不存在，则用空矩阵表示。

#### 常微分方程数值求解

* `[t,y]=solver(fliename,tspan,y0,option)`

  t,y分别为给出的时间向量和响应的数值解，solver为求常微分数值解的函数，filename是定义了f(t,y)的函数名，函数必须返回一个列向量，tspan形式为[t0,tf]，表示求解区间，y0是初始状态向量，Option是可选参数，用于设置求解属性

* 一般常微分函数求解函数形式：`odennxx`

  其中ode是Ordinary Differential Equation的缩写，表示常微分方程；nn是数字，代表所用方法的阶数，如ode23用二阶龙格-库塔算法，以三阶公式做误差估计；xx是字母，用于标注方法的专门特征，如s表示函数适用于刚性方程

* 刚性问题（stiff）：有一类常微分方程，其解的分量有的变化很快，有的变化很慢，且相差悬殊，数值解算法必须取很小的步长，才能达到很好的效果，如果用非刚性算法需要花费大量时间，所以需要采用带s的刚性算法求解问题。

更多可以查看课程：[用MATLAB求解常微分方程](https://ww2.mathworks.cn/learn/tutorials/solving-ordinary-differential-equations-with-matlab.html?s_tid=srchtitle)

## 符号计算

### 符号对象

* 符号对象的建立

  * `符号对象名 = sym(A)`：A可以是一个数值常量，数值矩阵或者数值表达式(不加单引号)，此时符号对象为一个符号常量，A可以是一个变量名(加单引号)，这是符号对象为符号变量

    ```matlab
    >> t=sym(2);t+1/2
    ans = 
    	5/2
    >> a=5;b=8;x=sym('a');y=sym('b');
    >> w=(a+b)*(a-b)
    w = 
    	-39
    >> s=(x+y)*(x-y)
  s = 
    	(a+b)*(a-b)
    ```
  
  * `syms`：可以一次性定义多个符号变量
  
    ```matlab
    >> syms X Y Z
    ```
  
    定义了三个符号变量：X,Y,Z
  
* 符号对象的运算：运算结果仍然是符号表达式

  * 四则运算：`+  -  *  /  ^`

  * 关系运算：`< <= > >= == ~=`；对应函数为：`lt() le() gt() ge() eq() ne()`

  * 逻辑运算：`&  |  ~`；四个逻辑运算函数：`and() or() not() xor()`与 或 非 异或

  * 因式分解与展开运算

    * `factor(s)`：对符号表达式s分解因式，如果S为符号常量，则分解因数
    * `expand(s)`：对符号表达式s进行展开
    * `collect(s)`：对符号表达式s合并同类项
    * `collect(s,v)`：对符号变量s按变量v合并同类项

  * 指定符号变量的值域

    * `assume(condition)`
    * `assume(expr,set)`

    第一种格式指定变量满足的条件，第二种格式指定表达式expr属于集合set

  * 其他运算

    * `[n,d]=numden(s)`：提取有理分式的分子分母
    * `c=coeffs(s,x)`：提取符号表达式的系数（从次数低的开始）
    * `simplify(s)`：符号表达式的化简
    * `p=sym2poly(s)`：符号多项式转化为多项式系数向量
    * `s=poly2sym(p)`：多项式系数向量转化为符号多项式 

  * 符号变量的确定：`symvar(s,n)`返回符号表达式s的n个符号变量

  * 符号对象的正常排版`pretty(s)`

* 符号矩阵：符号表达式运算会作用于符号矩阵中的每个元素

### 符号微积分

* 极限：`limit(f,x,a)`，若x省略，采用系统默认变量，a默认为0；单边极限：`limit(f,x,a,'right')`或`limit(f,x,a,'left')`
* 导数：`diff(f,x,n)`求f关于变量x的n阶导数，n默认为1，x与上同，可以求偏导
* 积分`int`
  * 不定积分`int(f,x)`
  * 定积分`int(f,x,a,b)`：a,b表示下限和上限；
    * f在关于变量x在区间[a,b]可积时，函数返回一个定积分结果
    * 当a,b中有个inf时，返回一个广义积分
    * 当a,b中有一个符号表达式时，函数返回一个符号函数

### 级数

* `symsum(s,v,n,m)`：无穷级数求和，s为级数通项，为符号表达式，v是求和变量，省略时为默认变量，n,m是求和变量v的初值和末值，可以为无穷大
* `taylor(f,v,a,Name,Value)`：将函数f按照变量v在点a处展开为泰勒级数，Name与Value为选项设置，前者为选项名，后者为选项值，经常成对使用。Name的值和Value如下
  * 'ExpansionPoint'：指定展开点，对应值可以是标量或者向量。为设置时展开点为0；
  * 'Order'：指定截断参数，对应值为正整数，未设置时截断参数为6，即展开式最高阶为5
  * 'OrderMode'：指定展开式采用绝对阶或者相对阶，对应值为'Absolute'或'Relative'，默认为'Absolute'

### 符号方程求解

* `solve()`和`eval()`

  `solve()`求代数方程的符号解

  * `solve(s)`：求解符号表达式s的代数方程，变量为默认变量
  * `solve(s,v)`：求解符号表达式s的代数方程，求解变量为v
  * `solve(s1,s2,···，sn,v1,v2,···,vn)`：求解符号表达式s1,s2,sn组成的代数方程组，变量为v1,v2,vn；

  `eval()`将符号表达式转化为数值结果

  ```matlab
  >> syms x	% 定义符号变量x
  >> x = solve(x^2-3*x+1)
  x =
  	3/2 - 5^(1/2)/2
  	5^(1/2)/2 + 3/2
  >> x = eval(x)	% 转化符号解为数值解
  x =
      0.3820
      2.6180
  ```

* 常微分方程求解

  在MATLAB中用大写字母**D**表示导数：Dy表示y'，D2y表示y''，Dy(0)=5表示y'(0)=5
  
  `dsolve(e,c,v)`求解符号常微分方程，常微分方程e在初值条件下的特解。参数v为方程自变量，省略时按照默认原则处理，如果没有给出初值条件c,则求方程通解。
  
  `dsolve(e1,e2,···,en,c1,c2,···,cn,v)`用于求解常微分方程组

## 自控原理

[MATLAB Transfor Function](https://ww2.mathworks.cn/help/control/ref/tf.html)

### 拉氏变换和Z变换

#### Laplace Transform

* 拉氏变换

  > F为符号函数，默认符号变量为 t 与 s 
  * `L = laplace(F)`： 系统F（缺省独立变量为t）的拉氏变换，缺省返回为s的函数，如果F = F(s)，那么LAPLACE返回为t的函数L = L(t)
  * `L = laplace(F,t)`： 返回L为t的函数，而不是缺省时s的函数
  * `L = laplace(F,w,z)`： 返回L为z的函数，而不是缺省时s的函数，积分变量为w

* 拉氏反变换
  * `F = ilaplace(L)`：系统L（缺省独立变量为s）的拉氏逆变换，缺省返回t的函数；如果L=L(t)，那么LAPLACE返回为s的函数L=L(s)
  * `F = ilaplace(L,y)`： 返回F为y的函数，而不是缺省时的t的函数
  * `F = ilaplace(L,y,x)`： 返回F为x的函数，而不是缺省时的t的函数。积分变量为y

#### Z Transform

* Z变换
  * `F = ztrans(f)`： 表示函数f(n)的Z变换,返回为z的函数F(z)，f = f(n) =>F = F(z).

  * `F = ztrans(f,w)`： 返回F是w的函数

  * `F = ztrans(f,k,w)`： 表示f是变量k的函数，返回F(w)

    > 应用到连续系统时，必须首先将连续系统离散化，例：sin(wt)写为sin(wTn)，然后用ztrans进行Z变换。

* Z反变换
  * `f = iztrans(F)`： 表示F(z)的反变换，缺省时返回f为n的函数f(n)，F = F(z) => f = f(n).如果F = F(n), 那么返回f为k的函数: f = f(k).
  * `f = iztrans(F,k)`： 表示返回f为k的函数（而不是缺省时的n的函数）
  * `f = iztrans(F,w,k)`：表示F为w的函数，返回f为k的函数

### 系统模型

<img src="../../../WMY/Typora/All File Picture/image-20201006161743179.png" alt="image-20201006161743179" style="zoom:50%;" />

<img src="../../../WMY/Typora/All File Picture/image-20201006161808607.png" alt="image-20201006161808607" style="zoom: 33%;" />

#### 传递函数模型

* `SYS = tf(NUM,DEN)`： 生成连续时间函数，分子num， 分母den
* `SYS = tf(NUM,DEN,TS)`：生成离散传递函数，Ts为采样周期，Ts＝-1表示采样时间不定
* `SYS = tf(SYS)`：将任意形式的模型（状态空间模型，零极点模型）转换为传递函数
* `SYS = tf(NUM,DEN,’inputdelay’,T)`： 生成有纯滞后的传递函数
* `s = tf('s')`：表示传递函数是s域的，这种方法当分子分母多项式的系数不容易获得时（如分子分母
  是乘积形式的）是非常有效的
* `[num,den] =tfdata(SYS,'v')`：对于SISO连续/离散系统
* `[num,den,Ts,Td] =tfdata(SYS,'v')`：Ts和Td为采样周期和输入时延

#### 状态空间模型

* `SYS = ss(A,B,C,D)`：生成连续状态方程，A、B、C、D表示状态方程的相应矩阵。
* `SYS = ss(A,B,C,D,Ts)`：生成离散状态方程。Ts为采样时间，Ts＝-1表示采样时间不定
* `SYS = ss(SYS)`：将任意形式的模型（传递函数模型，零极点模型）转换为状态方程
* `[a,b,c,d]=ssdata(sys)`
* `[a,b,c,d,Ts,Td]=ssdata(sys)`

#### 零极点模型

* `SYS = zpk(Z,P,K)`：生成连续零极点增益模型，零点Z, 极点P,增益K。
* `SYS = zpk(Z,P,K,Ts)`：生成离散零极点增益模型，Ts为采样周期，Ts＝－1表示采样时间不确定
* `SYS = zpk(SYS)`：将任意形式的模型（状态空间模型，传递函数模型）转换为零极点增益模型
* `[z,p,k]=zpkdata(sys,’v’)`：'v'是一个参数，而已
* `[z,p,k,Ts,Td]=zpkdata(sys,’v’)`

#### 模型之间的转换

* 传递函数到状态方程：`[A,B,C,D] = ss2tf(NUM,DEN)`，对于离散系统，必须保证分子和分母多项式的长度相同

* 状态方程到传递函数：`[NUM,DEN] = ss2tf(A,B,C,D,iu)`，返回当具有多个输入的方程组的第 `ni` 个输入受单位脉冲影响时所生成的传递函数

* 传递函数部分分式展开：`[r, p, k]=residue(num, den)`： 将求出两个多项式B(s)和A(s)之比的部分分式展开留数、极点和直接项

* 连续系统离散化：`SYSD = c2d(SYSC,Ts,METHOD)`

  * METHOD缺省为采用零阶保持器的方法，Ts为采样周期
    * `'zoh'`——采用零阶保持器
    * `'foh'`——采用一阶保持器
    * `'tustin'`——采用双线形（tustin）逼近方法
    * `'prewarp'`——采用改进的tustin方法
    * `'matched'`——采用SISO系统的零极点匹配法。

* 离散系统连续化：`sysc=d2c(sysd,method)`

  * `'zoh'`——采用零阶保持器

    > zoh法不适合系统具有z=0的极点的情况，对于具有负实数极点的系统，该方法将增加系统的阶

  * `'tustin'`——采用双线形（tustin）逼近方法

    > Tustin法不适合系统具有z=1

  * `'prewarp'`——采用改进的tustin方法

  * `'matched'`——采用SISO系统的零极点匹配法。具有接近1的极点的情况。

#### 系统的典型连接

* SISO合并为MIMO：`SYS = APPEND(SYS1,SYS2, ...)`

* 系统串联

  * `sys=series(sys1,sys2)`：返回两个系统sys1和sys2的串联系统。两个子系统必须连续时间系统或者具有相同采样周期的离散时间系统
  * `sys=series(sys1,sys2,outputs1,inputs2)`：outputs1和inputs2用于指定sys1的部分输出与sys2的部分输入进行连接。

* 系统并联

  * `sys=parallel(sys1,sys2)`：返回两个系统并联连接系统，两个子系统必须连续时间系统或者具有相同采样周期的离散时间系统
  * `sys=parallel(sys1,sys2,inp1,inp2,out1,out2)`：inp1和inp2分别表示两个系统连接在一起的输入端，out1和out2中分别指定要做相加的输出端编号

* 系统反馈

  * `sys=feedback(sys1,sys2)`：返回sys1和sys2的反馈连接系统sys，反馈为负反馈。两个子系统必须连续时间系统或者具有相同采样周期的离散时间系统。
  * `sys=feedback(sys1,sys2,sign)`：定义反馈形式sign，sign=+1表示正反馈，sign=-1表示负反馈。
  * `sys=feedback(sys1,sys2,feedin,feedout,sign)`：将sys1的指定输出feedout连接到sys2的输入，sys2的输出连接到sys1的指定输入feedin，以此构成闭环系统

* 框图连接

  ![image-20201006171731758](../../../WMY/Typora/All File Picture/image-20201006171731758.png)

### 系统分析

#### 特征方程和特征根

* `pole(sys), eig(A)`——计算系统的极点，所有极点都位于系统左半平面，则系统是稳定的；否则系统不稳定，返回极点。
* `[V,D]=eig(A)`——矩阵的特征值和特征向量。
* `poly(A)`——求矩阵的特征多项式。当A为向量时，A中的元素为所求得的多项式的根。
* `polyeig(A)`——求矩阵的特征值。
* `roots(B)`——求多项式的根。
* `Z = zero(SYS)`——求传递函数的零点

#### 时域分析

控制系统最常用的时域分析方法是：当输入信号为单位阶跃和单位脉冲信号时的系统输出响应。

| 函数名称  |     功能     |
| :-------: | :----------: |
|  `step`   | 单位阶跃响应 |
| `impulse` | 单位脉冲响应 |
| `intial`  |  零输入响应  |
|  `lsim`   | 任意输入响应 |

离散系统响应可在上述函数名称前加d，例：dstep。也可以采用上述函数，对应的系统采用离散形式。

* `step(SYS)`——线性时不变模型sys的单位阶跃响应。
* `step(SYS,TFINAL)` —— 连续系统响应时间`t=0` 到`t=TFINAL`。离散系统中，`TFINAL`表示采样步数
* `step(SYS,T)` ——用户定义仿真时间向量。对离散模型，T为Ti:Ts:Tf ，Ts为采样时间，对于连续模型，T为Ti:dt:Tf，dt为离散化时的采样时间，阶跃输入t=0开始。
* `initial(SYS,X0)`——X0为初始状态的状态方程零输入响应。
* `initial(SYS,X0,TFINAL)`， `initial(SYS,X0,T)` 中参数同上。
* `lsim(SYS,U,T)`——任意输入的响应，输入由U和T表示，T，U的列表示输入个数，U的行u(i)表示T(i)时刻对应的行：t = 0:0.01:5; u = sin(t); lsim(sys,u,t)，对于离散系统，U按照sys的采样周期采样，如果sys的采样周期不定，则T可省略，或者[]表示。
* `lsim(SYS,U,T,X0)` 非零初始状态X0。

#### 根轨迹分析

* `pzmap(SYS)`——计算系统的零极点，并绘制图形，极点用x表示，零点用o表示。
* `[P, Z] = pzmap(SYS)`——返回系统零极点列向量，不画图
* `rlocus(SYS)`——计算并绘制系统的根轨迹图。根轨迹图用来分析负反馈系统，并显示当反馈增益从0变化到∞时，闭环极点的轨迹。
* `[R, K] = RLOCUS(SYS)`，`R＝rlocus(SYS,K)` ——K为用户定义的增益。
* `[K, POLES] = rlocfind(SYS)`——可在图形窗口根轨迹图中显示出十字光标，当用户选择其中一点时，其相应的增益由k记录，与增益相关的所有极点记录在poles中
* `[K, POLES] = rlocfind(SYS,P)`——指定要得到增益的根矢量P。
* `sgrid`——在连续系统的根轨迹或零极点图上绘制出栅格线，栅格线由等阻尼系数和等自然频率线构成，阻尼系数以步长0.1从ξ＝0到ξ＝1绘出
* `sgrid(‘new’)`——先清除图形屏幕，然后绘制出栅格线，并设置成`hold on`，使后续绘图命令能绘制在栅格上。
* `sgrid(z, wn)`——可制定阻尼系数z和自然频率wn
* `sgrid(‘new’, z, wn)`——可制定阻尼系数z和自然频率wn，并且在绘制栅格线之前清除图形窗口。

#### 频域分析

| 函数名称  |            函数功能            |
| :-------: | :----------------------------: |
|  `bode`   | 绘制系统的波特图（幅频和相频） |
| `nyquist` |     绘制系统的奈奎斯特曲线     |
|  `freqs`  |         S域的频率响应          |
|  `freqz`  |         Z域的频率响应          |
| `margin`  |       幅值裕度和相角裕度       |

* `bode(SYS)`—— 绘制Bode图。sys是由tf, zpk, ss生成

  * `bode(SYS, {WMIN, WMAX})`——绘制频率为WMIN和WMAX之间的Bode图

  * `[MAG, PHASE] = bode(SYS,W)` ，`[MAG,PHASE,W] = bode(SYS)`返回幅度、相位和频率点。

    > 为了得到对数幅度(dB), MAGDB = 20*log10(MAG)

* `nyquist(SYS)` ——绘制Nyquist曲线

  * `nyquist(SYS,{WMIN,WMAX})` ——设定了频率范围
  * `nyquist(SYS,W)` ——用户定义频率
  * `[RE,IM] = nyquist(SYS,W)` ，`[RE,IM,W] = nyquist(SYS)` 返回频率响应的实部和虚部。

* `margin`——求增益和相位裕度

  > 从频率响应数据中计算出增益、相位裕度以及有关的交叉频率。

  * `margin(sys)`——得到增益和相位裕度，并绘制出Bode图。
  * `[gm, pm, wcg, wcp]=margin(mag, phase, w)`——得到增益和相位裕度以及相应的频率wcg、wcp，而不直接绘制出Bode图。其中mag,phase, w为由bode得到的增益、相位及其频率值。利用margin函数可以计算稳定裕度大于设定值条件的K值范围。

* `LTIview` ——线性时不变系统用户界面

  LTIview —— 打开LTI系统的用户界面，可以在这个界面内选择LTI系统，也可以用函数`LTIVIEW(PLOTTYPE,SYS1,SYS2,...,SYSN)。`

  绘制类型包括：`step` Step response `impulse` Impulse response `bode` Bode diagram `bodemag` Bode Magnitude diagram `nyquist` Nyquist plot `nichols` Nichols plot `sigma` Singular value plot `pzmap` Pole/Zero map `iopzmap` I/O Pole/Zero map

### 状态空间

#### 状态空间系统分析

| 函数名称  |          函数功能           |
| :-------: | :-------------------------: |
| `ploy(A)` | 返回矩阵A的特征多项式的系数 |
|  `ss2ss`  |          相似变换           |
|  `ctrb`   |       可控性矩阵计算        |
|  `ctrbf`  |     系统可控不可控分解      |
|  `obsv`   |       可观性矩阵计算        |
|  `obsvf`  |     系统可观不可观分解      |
|  `canon`  |     状态空间的正则实现      |
| `minreal` |     状态空间的最小实现      |
| `jordan`  |         约当标准型          |

* 系统能控能观性

  * `co = ctrb(A, B)`——用于计算由A和B给出的系统的可控性矩阵  [B,AB,A^2^B,...]。
  * `co = ctrb(SYS)`——计算状态空间对象的可控性矩阵，等价与`ctrb(sys.a, sys.b)`，
  * `ob = obsv(A, C)`——用于计算由A和C给出的系统的可观性矩阵 [C,CA,CA^2^,...]^T^。
  * `ob = obsv(SYS)`——计算状态空间对象的可观性矩阵，等价与`obsv(sys.a,sys.b)`

* 状态空间方程的几种特殊形式

  * 能控能观标准型，约当标准型

    * `jordan (A)` 计算A的约当标准型形式
    * `[V, J] = jordan(A)` 返回J为A阵的约当标准型，V为转换矩阵，V\A*V = J.
    * `csys=canon(sys, type)`——状态方程的正则实现，type有两种，‘modal’表示对角线型标准实现，‘companiona’表示伴随矩阵（能观相伴标准型）

  * 能控子空间分解

    `[Abar,Bbar,Cbar,T,k]=ctrbf(A,B,C)`——将系统分解为可控和不可控两部分，T为相似变换，k是长度为n的矢量，其元素为各个块的秩，sum(k)可求出A中可控部分的秩。

  * 能观子空间分解

    `[Abar,Bbar,Cbar,T,k]=obsvf(A,B,C)`——将系统分解为可观和不可观两部分，T为相似变换，k是长度为n的矢量，其元素为各个块的秩，sum(k)可求出A中可控部分的秩。

  * 状态空间的最小实现

    `sysr=mineral(sys)`——sys的最小实现，即删除状态空间模型中的不可观和不可控状态，或对消零极点模型中相同的零极点，输出系统sysr具有最小的阶。

* 离散状态方程求解

  * `lsim(SYS,U,T,X0)`——系统对输入的响应，初始状态为X0（在时间T(1)），省略表示X0为0；对离散模型，U的采样与离散采样周期一致（T可以省略，或置为[]）。
  * `[Y,T,X] = lsim(SYS,U,T,X0)`

#### 状态反馈极点配置与状态观测器

* 状态反馈极点配置
  * `k=acker(a, b, p)`——SISO极点配置。利用Ackermann公式计算反馈增益矩阵k，使采用全反馈u= -kx的单输入系统具有指定的闭环极点p.
  * `k=place(A, B, p)`——MIMO极点配置。利用Ackermann公式计算反馈增益矩阵k，使采用全反馈u= -kx的多输入系统具有指定的闭环极点p.

* 生成系统状态估计器

  > 从LTI系统的状态空间模型和增益矩阵L中生成系统的状态估计器，增益矩阵L可由极点配置函数place或acker形成。
  > 注意：生成的增益矩阵L为对偶系统（AT，CT，BT）的反馈增益矩阵

  `est=estim(sys,L)`——生成给定增益矩阵L下的状态空间模型的LTI系统sys的状态和输出估计器est，并假定sys的所有输入为随机的，所有输出可测。返回估计器形式estimator

## Simulink仿真

* Simulink Library Browser：是模块库，包含基本模块和专业模块两类
* Simulink教学视频：[Simulink Onramp](https://www.mathworks.com/learn/tutorials/simulink-onramp.html#getstarted) 在MATLAB中打开Simulink，左边框的learn

## 其他

*  `>> doc fcnName` 以获取任何 MATLAB 函数的相关信息。
* `>> edit filename`查看或者编辑文件
* `>> help functionName`查看函数注释内容
* `>> diary`用于将命令行窗口的显示以文本文件的方法显示，从`diary on`到`diary off`之间
* `>> lookfor keyword`查找关键字，此 MATLAB 函数 在搜索路径中找到的所有 MATLAB 程序文件的帮助文本的第一个注释行（H1行）中搜索指定的关键字。对于存在匹配项的所有文件，lookfor 显示 H1 行
* [官方中文翻译文档](https://ww2.mathworks.cn/help/index.html)



