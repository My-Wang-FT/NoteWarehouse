[TOC]

# Python 学习笔记

## 示例一：温度转换

```python
#TempConvert.py
TempStr = input("请输入带有符号的温度值：")
if TempStr[-1] in ['F', 'f']:
	C = (eval(TempStr[0:-1]) - 32)/1.8
	print("转换后的温度是{:.2f}C".format(C))
elif TempStr[-1] in ['C', 'c']:
	F = 1.8*eval(TempStr[0:-1] + 32)
	print("转换后的温度是{:.2f}F".format(F))
else:
	print("输入格式错误")
```

### Python 相关知识

#### 缩进

> 是语法的一部分，表达代码间包含层次关系的唯一手段

#### 注释

> 用 `#` 表示，没有多行注释的方法，但是可以使用 **''' '''** 的字符串表示多行注释

#### 变量

- 大小写字母，数字，下划线和中文等组合
- 因为**Python**以**Unicode**进行编码，因此支持多种文字
- 首字母不能是数字

#### 保留字

|   **and**    |  **as**   | **assert** | **break**  |  **class**   |
| :----------: | :-------: | :--------: | :--------: | :----------: |
| **continue** |  **def**  |  **elif**  |  **else**  |  **except**  |
| **finally**  |  **for**  |  **from**  |   **if**   |  **import**  |
|    **in**    |  **is**   | **lambda** |  **not**   |    **or**    |
|   **pass**   | **raise** | **return** |  **try**   |  **while**   |
|   **with**   | **yield** |  **del**   | **global** | **nonlocal** |
|   **True**   | **False** |  **None**  | **async**  |  **await**   |

### 数据类型

> #### 字符串，整数，浮点数，列表

#### 字符串

- 可以用单引号，双引号表示

- 索引： <字符串>[M] 可以为正，也可以为负。`TempStr[-1]` 代表最后一个字符
- 切片： <字符串>[M:N] 从 M 到 N 个，不包括 N 代表的字符。`TempStr[0:-1]`除去最后一个字符以外的字符

#### 列表

- 0 个或多个数据组成的有序数列`['F', 'f']`
- 保留字 `in` 判断是否在列表中。`TempStr[-1] in ['F', 'f']`

### 语句和函数

#### 分支语句

- `if`, `elif`, `else`，必须加 `:`
- 必须严格缩进

#### `input()`

- `<变量> = input(<提示信息字符串>)`
- 输入信息以字符串的形式保存在变量中

#### `eval()`

- `eval(<字符串或者字符串变量>)`
- 去掉参数最外侧引号并且执行剩下语句
- `eval("1")`>>>`1`；`eval("2+1")`>>>`3`；`eval('"1+2"')`>>>`'1+2'`
- `eval('print("Hello")')`>>>`Hello`

## 示例二：Python 蟒蛇绘制

```python
#PythonDraw.py
import turtle
turtle.setup(650, 350, 200, 200)
turtle.penup()
turtle.fd(-250)
turtle.pendown()
turtle.pensize(25)
turtle.pencolor("purple")
turtle.seth(-40)
for i in range(4):
	turtle.circle(40, 80)
	turtle.circle(-40, 80)
turtle.circle(40, 80/2)
turtle.fd(40)
turtle.circle(16, 180)
turtle.fd(40 * 2/3)
turtle.done()
```

### turtle 库

> Python 标准库之一，用于图形绘制
>
> Python 计算生态 = 标准库 + 第三方库

#### 窗口

- 控制函数`turtle.setup(width, height, startx, starty) `
- 设置窗体大小和位置
- 前两个控制窗口大小，后两个控制窗口位置，后两个参数不必须，默认窗口在中心
- 不是必须的函数，不用`setup()`默认大小，位置在左上角

#### 坐标系

- 空间坐标系
  - 中心为原点，左 X 正，上 Y 正
  - `turtle.goto(x, y)`到坐标系相应位置
- 海龟坐标系
  - 初始位于窗口中间，朝 X 正向
  - `turtleforward(d)`别名`turtle.fd(d)`向前 d 个像素，d 可以为负数
  - `turtlebackward(d)`别名`turtle.bk(d)`向后 d 个像素，d 可以为负数
  - `turtle.circle(r,angle)`画弧线，默认圆心在海龟左侧，默认 angle 为 360^o^
- 角度坐标
  - 极坐标方向
  - `turtsetheading(d)`别名`turtle.seth(angle)`其中`angel`为绝对角度，并且只改变方向不前进
  - `turtle.left(angle)`和`turtle.right(angle)`相对海龟的角度，转弯不移动

#### 色彩体系

- 取值范围：[0-255]、[0-1]
- `turtle.colormode(mode)`其中`mode`可以为 RGB 小数或者 RGB 整数

#### 画笔控制

- `turtle.penup()`别名`turtle.pu()`抬起画笔
- `turtle.pendown()`别名`turtle.pd()`放下画笔
- `turtle.pensize(width)`别名`turtle.penwidth(width)`画笔宽度
- `turtle.pencolor(color)`其中`color`为颜色字符串或者 RGB 值
  - 颜色字符串`turtle.pencolor("yellow")`
  - RGB 小数值`turtle.pencolor(0.63, 0.13, 0.94)`
  - RGB 元组值`turtle.pencolor( (0.63, 0.13, 0.94) )`

### 库引用、函数和语句

#### `import`

- 使用`import`引用其他库来扩充 Python 程序功能
- `import <库名>`之后调用库函数时采用`<库名>.<函数名>(<函数参数>)`进行引用
- `form <库名> import <函数名>`或者`form <库名> import *`可以做到省略调用库函数前面的库名
- `import <库名> as <别名>`可以自定义别名，方便书写和使用

#### 循环语句 for

- `for <变量> in <次数>:`
  - `:`不能省略，没有大括号，严格缩进

#### `range()`

- `range(N)`产生从 0 到 N-1 的整数序列
- `range(M,N)`产生从 M 到 N-1 的整数序列

## 示例三：天天向上的力量

```python
#DayDayUpQ1.py
dayup = pow(1.001, 365)
daydown = pow(0.999, 365)
print("向上：{:.2f}，向下：{:.2f}".format(dayup, daydown))

#DayDayUpQ2.py
dayfactor = 0.005
dayup = pow(1+dayfactor, 365)
daydown = pow(1-dayfactor, 365)
print("向上：{:.2f}，向下：{:.2f}".format(dayup, daydown))

#DayDayUpQ3.py
dayup = 1.0
dayfactor = 0.01
for i in range(365):
	if i % 7 in [6,0]:
		dayup = dayup*(1-dayfactor)
	else:
		dayup = dayup*(1+dayfactor)
print("工作日的力量：{:.2f} ".format(dayup))

#DayDayUpQ4.py
def dayUP(df):
	dayup = 1
	for i in range(365):
		if i % 7 in [6,0]:
			dayup = dayup*(1 - 0.01)
		else:
			dayup = dayup*(1 + df)
	return dayup
dayfactor = 0.01
while dayUP(dayfactor) < 37.78:
	dayfactor += 0.001
print("工作日的努力参数是：{:.3f} ".format(dayfactor))
```

### 数字类型及操作

#### 整数类型

- 可正可负，没有范围限制
- `power(x,y)`计算 $x^y$ 可以非常大
- 进制表示
  - 十进制
  - 二进制，$0b$ 或者 $0B$ 开头
  - 八进制，$0o$ 或 $0O$ 者开头
  - 十六进制，$0x$ 或者 $0X$ 开头

#### 浮点数类型

- 取值范围大约 -10^307^ **~** 10^308^ ，精度数量级为 10^-16^
- 不确定尾数
- 采用 `round(x,d)` 对`x`进行四舍五入，`d`是截取位数
- 科学计数法表示方法 `<a>e<b>` 或者 `<a>E<b>` 表示 a^b^

#### 复数类型

- Python 提供复数类型
- `z.real()` 获得实部 `z.imag()` 获得虚部

#### 数值运算操作符

|  操作符  |               描述               |
| :------: | :------------------------------: |
|   x/y    |           正常数学除法           |
|   x//y   |             整数除法             |
| x \*\* y | 幂运算：x^y^ 如果 y 是小数则开方 |
|  x op y  | 二元增强操作符，与赋值运算符结合 |

- 数值之间会自动进行类型转换

#### 数值运算函数

- `abs(x)` 绝对值
- `divmod(x,y)` 商余，`(x//y, x%y)` , 同时输出商和余数
- `pow(x,y[,z])` 幂余，表示 `(x**y)%z` 其中 […] 的参数可以省略
- `round(x[,d])` 四舍五入，默认 d 为零
- `max(x1, x2, …,xn)` 最大值
- `max(x1, x2, …,xn)` 最小值
- `int(x)` x 转化为整数，舍弃小数部分
- `float(x)` 转化为浮点数
- `complex(x)` 转化为复数

### 字符串类型及操作

#### 字符串类型表示

- Unicode 编码

  - 统一字符编码，即覆盖几乎所有字符的编码方式
  - 从 0 到 0x10FFFF 空间，每个编码对应一个字符
  - Python 字符串中每个字符都是 Unicode 编码字符

- 一对单引号或者一对双引号表示 ，或者由一对三单引号或三双引号表示多行字符串

  > `"Python"` 或者 `'Python'`
  >
  > `''' 这里有个双引号(") '''`
  >
  > `""" 这里有个单引号(') """`

- 引用可以使用正向递增序号和反向递减序号

  > ​ **-5 -4 -3 -2 -1**
  >
  > ​ 请 输 入 数 字
  >
  > ​ **0 1 2 3 4**

- 索引：返回单个字符 `"Python"[-2]` >>> `"o"`

- 切片：返回一段子串 `"Python"[0:-1]` >>> `"Pytho"`

- 高级切片：指定步长切片 `<字符串>[M:N:K]` M 缺失表示至开头，N 缺失表示至结尾，K 表示根据步长 K 切片

  > `"012345"[0:-1:2]` >>> `024`
  >
  > `"01234567[::-1]"` >>> `"76543210"`

- 特殊字符：转义符 `\`

  > `"\b"` 回退 `"\n"` 换行 `"\r"` 回车（光标移动到行首）

#### 字符串操作符

|  操作符和使用  |                   描述                    |
| :------------: | :---------------------------------------: |
|     x + y      |           连接两个字符串 x 和 y           |
| n _ x 或 x _ n |             复制 n 次字符串 x             |
|   x **in** s   | 如果 x 是 s 的子串，返回 True, 否则 False |

#### 字符串处理函数

- `len(x)` 返回字符串 x 的长度
- `str(x)` 任意类型 x 所对应的字符串形式
- `hex(x)` 或 `oct(x)` 将整数 x 的十六进制或者八进制写成字符串
- `chr(u)` x 为 Unicode 编码，返回对应字符
- `ord(x)` x 为字符，返回其对应的 Unicode 编码

#### 字符串处理方法

> 面向对象，对于字符串这个对象，下面介绍字符串对象提供的函数
>
> 调用形式`<a>.<b>()` 此中字符串是<a>

- `str.lower()` 或 `str.upper()` 返回字符串副本，全部小写\大写
- `str.split(sep=None)` 返回列表，由`str`根据`sep`被分隔的部分组成
- `str.count(sub)` 返回`sub`在`str`中出现的次数
- `str.replace(old, new)` 返回`str`副本，所有 old 的子串被替换为 new
- `str.center(width[,fillchar])` 字符串`str`根据宽度`width`居中，`fillchar`是填充字符
- `str.srtip(chars)` 从`str`中去掉在其左侧和在其右侧`chars`中列出的字符
- `str.join(iter)` 在`str`变量除最后元素外每个元素后增加一个`iter`，主要用于分隔字符

#### 字符串类型的格式化
