[toc]

# 阅读的文献翻译摘要记录

## 【1】FAST-Racing: An Open-Source Strong Baseline for SE(3) Planning in Autonomous Drone Racing

### Reference

* 9-11是极端环境下基于视觉的开源数据集
* 4和5是高速无人机竞速的数据集
* 12,14,15,16：高速，有效的在C-space下生成轨迹，但是不考虑无人机的姿态
* 17-19:使用SE3控制器，但是仍然是使用固定姿态进行规划

### 高效的SE3规划器

输入栅格地图，起始终止的状态，输出一条可行的SE3轨迹。

首先提出一种通用的四旋翼建模方法和带姿态约束的SE3优化形式，之后提出一种有效求解该优化问题的并行架构。

### 安全性约束

* 首先使用路径规划算法规划一条路径
* 从起点开始，检查无碰的，在最大距离内的终点（两点连线无碰），其中最大距离参数限制了凸包的长度
* 使用RILS[21]来形成凸包

![image-20211008115152229](../../WMY/Typora/All File Picture/image-20211008115152229.png)

#### 问题形式

使用旋转矩阵Rb表示无人机姿态，使用微分平坦原理，Rb可以被表示为四个平坦量的代数形式。

![image-20211008115957794](../../WMY/Typora/All File Picture/image-20211008115957794.png)

使用凸多面体表示四旋翼的模型。多面体的每个顶点都是由旋转+平移组成：

![image-20211010161753463](../../WMY/Typora/All File Picture/image-20211010161753463.png)

前述飞行走廊的描述如下：

![image-20211010163406422](../../WMY/Typora/All File Picture/image-20211010163406422.png)

即，对于凸包Pj中某一点，其一定满足点乘小于零。

那么用凸多面体表示的四旋翼模型，始终内切与飞行走廊的表示为：凸多面体中每个顶点均位于飞行走廊凸包以内。使用分段多项式表示飞行轨迹。

对期望轨迹的控制效果和时间分配上进行优化，保证轨迹的平滑性和高速性。同时为了保证安全性，需要使用空间约束。最终的优化问题变为：

![image-20211010164306712](../../WMY/Typora/All File Picture/image-20211010164306712.png)

#### 梯度估计

轨迹中的参数包括每个多项式的系数以及分配的时间，求梯度即求代价函数对系数和时间的偏导数。

![image-20211010174200012](../../WMY/Typora/All File Picture/image-20211010174200012.png)

22-24分别是对应惩罚项的离散近似，每段轨迹都被离散化为L个采样点（非贝塞尔曲线的控制点）。其中δ为时间。S表示轨迹的光滑代价。

只需要计算Ev，Ea，Ec三个分别对时间和系数求偏导即可。

对C求偏导数：

![image-20211010180301014](../../WMY/Typora/All File Picture/image-20211010180301014.png)

对T求偏导数：

![image-20211010180845085](../../WMY/Typora/All File Picture/image-20211010180845085.png)

根据汪博的大作，最优解的条件一定满足轨迹的某阶导数连续，将这个条件应用，可以直接计算得到一个非奇异矩阵M，将多项式轨迹系数以路径点和间隔条件表示。最终只需要求解路径点即可。

![image-20211010181830518](../../WMY/Typora/All File Picture/image-20211010181830518.png)

其中C对q和C对T的偏导数，都是线性复杂度的计算项，最终可以使用汪博提供的方法将上述约束问题转化为无约束的优化问题。

#### 高性能的SE3规划器

每段轨迹的梯度计算时间都是相同的，因此该问题时间复杂度取决于轨迹的段数，但是每段轨迹都是独立的，因此可以使用GPU提高计算速度。

### 实物实验

因为实际实验中使用的 是vins，而不是动捕，所以定位效果比较差，所以实物实验中速度限制比较低。

工程上限制了规划的角度，避免了90度的奇异点。



## 【2】Minimum snap trajecotry generation and Control for Quadrotors

我们开发了一种算法，该算法能够通过位置和方向集中的一系列关键帧或航路点生成最佳轨迹，同时确保安全通过指定走廊并满足对可实现速度、加速度和输入的约束。

### 四旋翼模型

![image-20211011102228108](../../WMY/Typora/All File Picture/image-20211011102228108.png)

上图为四旋翼的坐标系示意图。其中机体坐标系为$\mathcal{B}$，世界坐标系为$\mathcal{W}$，$\mathcal{C}$坐标系为中间过渡坐标系。使用Z-X-Y欧拉角旋转顺序。$^{WR_B}={^WR_{C}}{^CR_B}$其中$\mathcal{W}$到$\mathcal{C}$转动了Z轴（YAW），C到B转动了X（ROLL）和Y（PITCH）。另外，机体坐标系的角速度定义为：
$$
w_{\mathcal{BW}} = px_\mathcal{B}+qy_\mathcal{B}+rz_\mathcal{B}
$$
其中p,q,r三个变量分别表示三个轴角度的导数，即机体坐标系下，机体旋转的即时角速度。

每个电机都会因为转速$w_i$产生力$F_i$和力矩$M_i$：
$$
F_i=k_Fw_i^2, M_i=k_Mw_i^2
$$
在实践过程中，与刚体动力学和空气动力学相比，电机动力学相对较快，因此在控制器设计的过程中可以认为电机可以瞬间完成响应执行。

系统的控制输入$\mathbf{u}$可以表示为沿着机体坐标系的推力$u_1$以及沿着三个机体坐标轴方向旋转的力矩$u_2，u_3，u_4$，即
$$
\mathbf{u}=\left[ \begin{array}{} k_F & k_F & k_F & k_F \\ 0  & k_FL & 0 & -k_FL \\ -k_FL & 0 & k_FL & 0 \\ k_M & -k_M & k_M & -k_M \end{array} \right] \left[ \begin{array}{} w_1^2 \\ w_2^2 \\ w_3^2 \\ w_4^2 \\  \end{array} \right]
$$
其中L为每个转子旋转轴到四角转子中心的距离。世界坐标系中质心向量表示为$\mathbf{r}$，由此可以写出牛顿欧拉方程：
$$
m\mathbf{\ddot{r}}=-mg\mathbf{z_\mathcal{W}}+u_1\mathbf{z_\mathcal{B}} \\
\mathcal{I}\cdot\dot{w}_\mathcal{BW}+w_\mathcal{BW}\times\mathcal{I}\cdot w_\mathcal{BW}=\left[ \begin{array}{} u_2 \\ u_3 \\ u_4 \end{array} \right]
$$

其中 $\mathcal{I}$ 为参考质心的惯性矩阵。整个系统的状态包括：
$$
\mathbf{x}=\left[ x,y,z,\phi,\theta,\psi,\dot{x},\dot{y},\dot{z},p,q,r \right]^T
$$

### 微分平坦

微分平台表示了四旋翼的状态和输入都可以写成四个精心选择的平坦输出量及其导数的代数函数。在平坦输出空间中，任何平滑的轨迹（具有合理的有界导数）都可以被欠驱动四旋翼跟随。

在这篇文章中，微分平坦量选择为：
$$
\sigma=\left[ x,y,z,\psi \right]^T
$$

因此我们可以定义在平坦输出空间的光滑轨迹为：
$$
\sigma(t):[t_0,t_m]\rightarrow\mathbb{R}^3\times SO(2)
$$
接下来证明，系统的全部状态和输入都可以使用平坦量和其导数进行代数表达：

首先，位置，速度和加速度这三个量都可以直接使用$\sigma$，$\dot\sigma$和$\ddot\sigma$直接表示。根据牛顿方程，可以推导出机体坐标系的Z轴方向为：
$$
\mathbf{z}_\mathcal{B}=\frac{\mathbf{t}}{\mathbf{\Vert t\Vert}},\mathbf{t}=\left[ \ddot\sigma_1, \ddot\sigma_2, \ddot\sigma_3+g \right]^T
$$
根据给定的YAW角，我们可以计算得到 $x_\mathcal{C}$ 和 $y_\mathcal{C}$ ，即可知道中间坐标系的方向，加上平移向量$\mathbf{r}$，即可得到坐标系 $\mathcal{C}$ 的位姿。
$$
^\mathcal{B}T_\mathcal{W}=\left[\begin{array}{} ^\mathcal{B}R_\mathcal{W} & \mathbf{r} \\ 0 & 1 \end{array} \right] = \left[\begin{array}{} \cos{\sigma_4} & -\sin{\sigma_4} & 0 & \sigma_1 \\ \sin{\sigma_4} & \cos{\sigma_4} & 0 & \sigma_2 \\ 0 & 0 & 1 & \sigma_3 \\ 0 & 0 & 0 & 1 \end{array} \right]
$$
因为坐标系 $\mathcal{B}$ 是坐标系 $\mathcal{C}$ 先旋转ROLL，再旋转PITCH得到，根据几何关系，坐标轴 $y_\mathcal{B}$ 垂直于 $z_\mathcal{B}$ 和 $x_\mathcal{C}$，可得：
$$
\mathbf{y}_\mathcal{B}=\frac{\mathbf{z}_\mathcal{B}\times\mathbf{x}_\mathcal{C}}{\Vert\mathbf{z}_\mathcal{B}\times\mathbf{x}_\mathcal{C}\Vert}，\mathbf{x}_\mathcal{B}=\mathbf{y}_\mathcal{B}\times\mathbf{z}_\mathcal{B}
$$
上述式子的成立条件为：$\mathbf{z}_\mathcal{B}\times\mathbf{x}_\mathcal{C}\neq0$，**<u>否则会因为分母为零出现奇异点</u>**。满足上述条件之后，有：
$$
^\mathcal{W}R_\mathcal{B}=\left[ \begin{array} \mathbf{x}_\mathcal{B} & \mathbf{y}_\mathcal{B} & \mathbf{z}_\mathcal{B} \end{array}\right]
$$
对牛顿方程求导，可以得到：
$$
m\dot{\mathbf{a}}=\dot{u}_1\mathbf{z}_\mathcal{B}+w_\mathcal{BW}\times u_1\mathbf{z}_\mathcal{B}
$$
且旋翼仅有机体坐标系的Z轴上有力，即：$\dot{u}_1=\mathbf{z}_\mathcal{B}\cdot m\dot{\mathbf{a}}$，因此我们可以将该式子带入上式，并且定义向量：
$$
\mathbf{h}_\mathcal{W}=w_\mathcal{BW}\times\mathbf{z}_\mathcal{B}=\frac{m}{u_1}\left( \dot{\mathbf{a}}-\left( \mathbf{z}_\mathcal{B}\cdot\dot{\mathbf{a}}\right)\mathbf{z}_\mathcal{B}\right)
$$
即将$\frac{m}{u_1}\dot{\mathbf{a}}$投影到$x_B-y_B$平面上，由此可以得到x轴和y轴的角速度：
$$
p=-\mathbf{h}_\mathcal{W}\cdot\mathbf{y}_\mathcal{B},q=\mathbf{h}_\mathcal{W}\cdot\mathbf{x}_\mathcal{B}
$$
由于$w_\mathcal{BW}=w_\mathcal{BC}+w_\mathcal{CW}$，并且$w_\mathcal{BC}$没有$\mathbf{z}_\mathcal{B}$分量（备注：该陈述中，前半段是正确的，但是后半句并不正确，即并且$w_\mathcal{BC}$是存在$\mathbf{z}_\mathcal{B}$分量的，因此由此方法计算得到的角速度并不正确）：
$$
r=w_\mathcal{BW}\cdot \mathbf{z}_\mathcal{B}=w_\mathcal{CW}\cdot \mathbf{z}_\mathcal{B}=\dot{\psi}\mathbf{z}_\mathcal{W}\cdot\mathbf{z}_\mathcal{B}
$$
另外，通过计算牛顿方程的二阶导数，可以计算得到角加速度$\alpha_\mathcal{BW}$在$x_B-y_B$方向的分量。注意到以下事实：
$$
\alpha_\mathcal{BW}=\alpha_\mathcal{BC}+\omega_\mathcal{CW}\times\omega_\mathcal{BC}+\alpha_\mathcal{CW} \\
\alpha_\mathcal{BC}\cdot\mathbf{z}_\mathcal{B}=0 \\
\mathbf{z}_\mathcal{B}\cdot\omega_\mathcal{CW}\times\omega_\mathcal{BC}=0
$$


可以推出角加速度在$\mathbf{z}_\mathcal{B}$方向的分量为：
$$
\alpha_\mathcal{BW}\cdot\mathbf{z}_\mathcal{B}=\alpha_\mathcal{CW}\cdot\mathbf{z}_\mathcal{B}=\ddot\psi\mathbf{z}_\mathcal{W}\cdot\mathbf{z}_\mathcal{B}
$$

### 控制

根据已知轨迹和微分平坦，可以计算上述无人机的全状态（奇异点除外）。

首先定义位置和速度的误差：$e_p=r-r_T$；$e_v=\dot{r}-\dot{r}_T$，之后计算期望的力的方向：$F_{des}=-K_pe_p-K_ve_v+mgz_W+m\ddot{r}_T$。其中有两个正定的增益矩阵：$K_p$和$K_v$，这里假设期望的力的大小始终不为零。接下来我们将所需的力的矢量投影到机体坐标系的Z轴上，以计算四旋翼期望力和第一个输入：
$$
u_1=\mathbf{F}_{des}\cdot\mathbf{z}_B
$$
为了计算其他三个变量，我们需要考虑旋转的误差。首先，我们注意到期望的$z_b$方向是沿着期望力矢量方向的，即：$\mathbf{z}_{B,des}=\frac{\mathbf{F}_{des}}{||\mathbf{F}_{des}||}$。因此如果$e_3 =[0,0,1]^T$，使用$R_{des}$表示$^\mathcal{W}R_\mathcal{B}$应该等于：$R_{des}e_3=z_{B,des}$。

已知YAW角$\psi_T(t)$规定沿着轨迹方向，因此使用前面的方法计算得到机体坐标系期望的X轴和Y轴：
$$
X_{C,des}=[\cos(\psi_T),\sin(\psi_T),0]^T; \\
\mathbf{y}_{\mathcal{B},des}=\frac{\mathbf{z}_{\mathcal{B},des}\times\mathbf{x}_{\mathcal{B},des}}{\Vert\mathbf{z}_{\mathcal{B},des}\times\mathbf{x}_{\mathcal{C},des}\Vert}，\mathbf{x}_{\mathcal{B},des}=\mathbf{y}_{\mathcal{B},des}\times\mathbf{z}_{\mathcal{B},des}
$$
其中仍然要保证分母不为零，由此可以定义整个$R_{des}$。在数学意义上讲，这个奇异点是SO3空间上的一个点，在这个奇异点附近会导致单位向量产生大的变化，在实际控制中，我们使用$x_b$和$y_b$两种计算方法，并且检查哪种计算方法和当前的姿态更接近，那我们就是用哪一种方法。

接下来我们定义了旋转的误差：
$$
\mathbf{e}_R=\frac{1}{2}\left(R_{des}^T{^\mathcal{W}R_\mathcal{B}}-{^\mathcal{W}R_\mathcal{B}^T}R_{des} \right)^{\vee}
$$
其中$\vee$表示从so3到R3的映射。角速度误差是实际角度苏和期望角速度在机体坐标系中的差值：
$$
\mathbf{e}_\omega=^\mathcal{B}[\omega_\mathcal{BW}]-^\mathcal{B}[\omega_{\mathcal{BW},T}]
$$
由此，我们可以计算期望时刻剩下的三个输出变量：
$$
[u_2, u_3, u_4]^T=-K_R \mathbf{e}_R-K_\omega \mathbf{e}_\omega
$$
其中$K_R$和$K_\omega$是对角增益矩阵，这允许ROLL，PITCH和YAW三个轴有单独的增益。最后，我们根据已有是四个期望输入，计算电机转速。

该模型在悬停点附近尽心线性化之后，与之前的模型一致。这种非线性的控制器有两个重要特征：

* 旋转误差不再基于有奇异点的欧拉角进行计算
* 期望推力被投影到了实际的Z坐标系上

我们给出了【16】中类似的控制器稳定性和收敛性的证明，但是：

* 增加了包括角速度在内的前馈项
* 取消了包括$\omega\times\mathcal{I}\omega$的反馈项
* 假设所有增益矩阵都是单位矩阵的标量的倍数
* 假设电机的动力学并不重要
* 认为飞机的质量和转动惯量已知

在以上这些条件下，满足以下两个初始条件即可保证动力学指数稳定？？？：
$$
tr[I-R^T_{des}(0) {^\mathcal{W}R_\mathcal{B}(0)}]<2 \\
||e_\omega(0)||^2<\frac{2}{\lambda_{min}(\mathcal{I})}k_R(1-\frac{1}{2}tr[I-R^T_{des}(0) {^\mathcal{W}R_\mathcal{B}(0)}])
$$
在实际实现控制器的时候并不完全满足上面提出的假设，然而我们在第六节可以看到，控制器在非常大的滚转和俯仰角度下仍然可以产生良好的跟踪性能。

### 轨迹生成

我们将关键帧定义为空间中的位置以及偏航角，我们考虑在指定时间经过m个关键帧的导航问题。在每个关键帧之间，都有一个无人机必须保持在内的飞行走廊。满足以上约束的一条容易生成的轨迹是使用直线在关键帧之间插值的轨迹，然而这条轨迹是低效的，因为它在关键帧处具有无穷曲率，这要求无人机在每个关键帧上停下。

我们生成最佳轨迹的方法是在给定时间内平稳的通过关键帧，同时保持轨迹在一个安全走廊中。轨迹表示为：

![image-20211109160120931](../../WMY/Typora/All File Picture/image-20211109160120931.png)

使用上述轨迹表示，可以找到通用的优化轨迹的代价函数：

![image-20211109160254029](../../WMY/Typora/All File Picture/image-20211109160254029.png)

其中：$k_r$表示估轨迹的某阶导数，$k_\psi$表示YAW角的某阶导数，其中$\mu_r$和$\mu_\psi$是两个常数，将代价函数无量纲化。我们确保了轨迹的k阶导数的连续性质。在本文中，$k_r=4$表示最小化snap，且u2u3在位置的四阶导数中；而$k_\psi=2$对应u4出现在偏航角的二阶导数中。以上问题为经典的QR问题。

#### 无量纲化

位置和YAW四个微分平坦输出量在代价函数和约束条件内都是解耦的，因此我们可以将其分解为四个优化问题。现在我们考虑无量纲变量$\tilde{w}(\tau)$的优化问题的一般形式，其中$\tau$表示无量纲的时间。

![image-20211109171241465](../../WMY/Typora/All File Picture/image-20211109171241465.png)

接下来我们引入一维时间$t=\alpha \tau$和一维变量$w(t)=w(\alpha\tau)=\beta_1+\beta_2\tilde{w}(\tau)$，因此我们可以使用$w$和$t$重写上式：

![image-20211109193527820](../../WMY/Typora/All File Picture/image-20211109193527820.png)

注意到在这个问题上，边界条件在空间上移动了$\beta_1$并按$\beta_2$缩放，而时间则按$\alpha$缩放。设无量纲问题的最优解为$\tilde{w}^*$，则新问题的解为：
$$
w^*(t)=\beta_1+\beta_2\tilde{w}^*(t/\alpha)
$$
接下来让我们考虑公式10的无量纲形式，其中$\mathbf{r},\psi,t$都被无量纲的变量$\tilde{\mathbf{r}},\tilde{\psi},\tau$代替了。可以通过令$\tilde{\mathbf{r}}_T=[\tilde{w}_1,\tilde{w}_2,\tilde{w}_3]^T$和$\tilde{\psi}_T=\tilde{w}_4$来一次解决四个无量纲问题，之后无量纲的最优解$\tilde{w}^*$可以被映射到原本问题上$x_T,y_T,z_T,\psi_T$四个变量的最优解上。对于每个变量时间尺度$\alpha$都是一样的，但是空间尺度$\beta$会不一样。

* 时间尺度：如果我们改变关键帧的导航时间，使到达关键帧的新时间变为$t_i=\alpha\tau_i$，那么问题的解决方法就是无量纲问题的简单时间尺度版本：
  $$
  \mathbf{r}_T^*(t)=\tilde{\mathbf{r}}_T^*(t/\alpha),\psi_T^*(t)=\tilde{\psi}_T^*(t/\alpha),
  $$
  随着$\alpha$的增加，规划的时间会越来越长，并且越来越安全，当$\alpha$趋于无穷时，位置和YAW的导数，以及角速度等量都会趋近于零，导致$u=[mg,0,0,0]^T$。因此我们可以通过使$\alpha$变大来适应任何安全性约束，相反的，当$\alpha$变小，轨迹执行时间变短，轨迹导数增加，会导致飞机的轨迹更激进。

* 空间尺度：我们研究了如何利用空间尺度特性来研究只有两个关键帧的轨迹。我们考虑10中无量纲的如下情况：$\tilde{\mathbf{r}}_T(0)=\mathbf{0},\tilde{\mathbf{r}}_T(1)=\mathbf{1}$，其他量都是用实际问题的解决方案来确定。实际问题的最优解为：
  $$
  x_T^*(t)=x_0+(x_1-x_0)\tilde{x}_T^*(t/t_1)
  $$
  y轴和z轴同理。这种解法很方便，因为通过分析修改一个解比解一个QP问题要快得多。因此这种方法对于快速反应动态障碍物或者目标是有用的。空间缩放同样适用于多个关键帧的问题，但是这个属性并不是很有用，因为每个关键帧的位置必须按照相同的因素缩放。

#### 增加走廊约束

首先，我们定义$\mathbf{t}_i$为$\mathbf{r}_i$到$\mathbf{r}_{i+1}$段的单位向量。第i段的垂直距离矢量$\mathbf{d}_i(t)=(\mathbf{r}_T(t)-\mathbf{r}_i)-((\mathbf{r}_T(t)-\mathbf{r}_i)\cdot\mathbf{t}_i)\mathbf{t}_i$。每个走廊的走廊宽度的无穷范数$\delta_i$定义为：$||\mathbf{d}_i(t)||_\infty\leq\delta_i$当$t_i\leq t \leq t_{i+1}$。这问题可以通过引入$n_c$个中间点来包含进QP问题中：
$$
\left|\mathbf{x}_W\cdot\mathbf{d}_i\left(t_i+\frac{j}{1+n_c}(t_{i_1}-t_i)\right)\right|\leq\delta_i,\ for \ j=1\cdots n_c
$$
同理适用于y和z。注意到每个绝对值约束都可以写为两个线性约束，走廊约束的效果如下图所示：

![image-20211109213400232](../../WMY/Typora/All File Picture/image-20211109213400232.png)

#### 优化每段时间

轨迹中有些关键帧的到达时间很重要，而有些并不是很重要。使用$T_i=t_i-t_{i-1}$表示每段的时间。因此时间问题可以从10中抽象出来，变为一个时间序列的优化问题：

![image-20211110111823130](../../WMY/Typora/All File Picture/image-20211110111823130.png)

我们使用受约束的梯度下降法来求解该问题。通过计算m个方向导数$\mathbf{g}_i$来求解该问题：
$$
\nabla_{\mathbf{g}_i}f=\frac{f(\mathbf{T}+h\mathbf{g}_i)-f(\mathbf{T})}{h}
$$
其中h是一个很小的数字，$\mathbf{g}_i$通过如下方式构造：其中第i部分为1，剩余部分均为$\frac{-1}{m-2}$，这样做可以让$\mathbf{g}_i$的总和为零，这样时间T可以加减$\mathbf{g}_i$，从而保证总时间仍然不变。给定方向导数的估计值，我们可以使用回溯线搜索进行梯度下降估计。

## 【3】Differential Flatness Transformations for Aggressive Quadrotor Flight

使用微分平坦特性允许四旋翼的完整状态使用平坦输出量进行表示，但是其仍然具有一定的局限性。

### 奇异点 Singularities

微分平坦有**四**个奇异点：

* 当油门推力为零时（期望加速度可以由重力完全提供）：这一奇异点是一个变换的基本限制，因为模型是建立在使用期望推力来设定四旋翼姿态的概念之上的。这个奇异点可以通过对规划器和控制器施加最小油门约束来避免。
* 当所需的推理矢量在xy平面上并且与所需要的运动方向对齐时（有两个）（比如向前90度倾斜）：其产生原因是需要使用zb和xc来确定yb的方向，但是当两个轴平行的时候就无法计算得到yb。另外在这个奇异点以及附近，系统的敏感性   会大大增加。
* 倒转180度：万向节死锁。在映射过程中，会存在一个雅阁比矩阵，倒转180度时矩阵中会有无穷项？？

建议使用四元数代替，

### 现有的解决奇异点的方法

#### 标准方法（Standard）

直接广泛使用微分平坦模型，并且假设奇异点永远不会到达。大部分使用稍微偏离悬停的状态飞行，也有人使用30-45度的姿态进行飞行，但是这些都距离90度的奇异点状态很远。

#### 消极检查法（Negative Check Method）

[11]展示了横滚接近90度的飞行，他们注意到机体坐标系的x和y轴可以被期望的YAW角和期望的机体坐标系Z轴进行表示。他们会检查（x,y,z）还是（-x,-y,-z）更接近当前方向，之后使用最近的坐标系来表示转换的输出。这种方法已经被高效的应用在船上和室内环境中，但是仅仅适用于普通的横滚或者俯仰角度。在他们的实验中仅仅单独展示了ROLL，并且不超过90度，因此他们并不会操作奇异点附近的变换。

#### 第二角度法（Second Angle Method）

[15]展示了一个钟可以让姿态保持在90度一段时间，并且翻过90度的方法（让飞机停靠在垂直90度和倾斜的平面上）。为了去除奇异点，他们引入了额外的角度$\gamma$来将$x_c$向量旋转到垂直平面：
$$
\mathbf{x}_\mathcal{C}=\left[\begin{array}{} \cos(\psi)\cos(\gamma), & \sin{\psi}\sin(\gamma), & \sin(\gamma)\end{array}\right]
$$
这将$x_c$从xy平面上移走，以此来避免与$z_b$平行。这种方法移动了奇异点，但是并没有真正去除它。同样在实验中只展示了一个轴的旋转（Pitch）而不是全方向的滚转。

#### 倒转飞行（Inverted flight）

经过360度的反转的方法大多使用的是切换控制器，将轨迹分割简化，包括启动、弹道轨迹、恒定角速率和回复。这些方法要么学习一组简化的机动参数[5]，[19]，要么拟合一个专家飞行员的模型[20]，[21]。机动的分割和简化避免了任何奇异性，但这种方法受限于有限的动力学范围。

#### 回复（Recovery）

机动飞行的一个重要组成部分就是回复状态部分。Faessler等[16]开发了一种自主恢复的控制器，可以有效地管理微分平坦的变换。他们将俯仰滚转的角速度控制和偏航分开，首先推导了独立于奇异点的俯仰角和横滚角的误差来计算出正确的$z_b$，使用误差乘以增益饿到所需要的俯仰和横滚速率。偏航所需要的额外旋转是使用标准的微分平坦变换计算的，但是因为滚转和俯仰速率已经确定，因此当遇见奇异点时，偏航速率可以简单地设置为零。另外如果$z_b$指向下，则可以使用$x_c$，这非常类似于PX4控制器的默认转换[22]。Faessler的工作[18]证明了该方法的可行性，包括反转姿态。然而这种恢复只发生在命令下的水平姿态，因此当发生奇异的时候，系统从来没有命令90度的俯仰或者横滚角度，或者倒置的方向。

#### 总结（Summary）

上面描述的每一种微分平面度变换方法都有局限性，这些局限性会导致变换在特定的情况下失败。虽然奇点可以避免，在下一节中，我们将展示围绕奇点和它附近的转换中仍然存在的问题，此时加速度矢量微小的变化将在变换上造成姿态的大幅度改变。

### 对微分平坦变换的分析

本节中分析上述方法在什么地方会产生问题。我们提出新的方法试图解决上述问题，并分析他们。我们的目标是展示每种方法的局限性，以及在四旋翼的整个飞行包络线中应用微分平坦度的最佳方法。最终的总结为表格一：

![image-20211023221350124](../../WMY/Typora/All File Picture/image-20211023221350124-16349984313641.png)

#### 标准方法（Standard）

标准的变换会在奇异点处会直接受到影响，但是更值得关注的是奇异点附近的姿态变化。如下图所示，当zb与xc靠近的时候，很小的加速度变化就会导致很大的姿态变动。

![singularity](../../WMY/Typora/All File Picture/singularity.gif)

#### 消极检查法（Negative Check）

Negative Check方法可以解决上述问题，如下图所示，取消原本的$x_b$与$y_b$，并维持一个与变换相似的姿态。

![image-20211024111905087](../../WMY/Typora/All File Picture/image-20211024111905087.png)

但是该方法仍然会当俯仰在奇异点附近经过90度时变得十分敏感。

#### 使用其他坐标系（Use Other Axes）

注意到在中间过渡的过程中使用$x_c$和$y_c$作为坐标轴计算机体坐标系是完全可以的。
$$
\begin{array}{}
\mathbf{x}_\mathcal{C}=\left[ \begin{array}{} \cos\psi_{sp}, & \sin\psi_{sp}, & 0 \end{array}\right] & | & 
\mathbf{y}_\mathcal{C}=\left[ \begin{array}{} -\sin\psi_{sp}, & \cos\psi_{sp}, & 0 \end{array}\right]  \\
\mathbf{y}_\mathcal{B}=\frac{\mathbf{z}_\mathcal{B}\times\mathbf{x}_\mathcal{C}}{\Vert\mathbf{z}_\mathcal{B}\times\mathbf{x}_\mathcal{C}\Vert} & | & 
\mathbf{x}_\mathcal{B}=\frac{\mathbf{y}_\mathcal{C}\times\mathbf{z}_\mathcal{B}}{\Vert\mathbf{y}_\mathcal{C}\times\mathbf{z}_\mathcal{B}\Vert} \\
\mathbf{x}_\mathcal{B}=\mathbf{y}_\mathcal{B}\times\mathbf{z}_\mathcal{B} & | & 
\mathbf{y}_\mathcal{B}=\mathbf{z}_\mathcal{B}\times\mathbf{x}_\mathcal{B}
\end{array}
$$
即使用$x_c$和$y_c$作为中间过渡，都可以计算得到对应的机体坐标系，而其奇异点并不相同。可以使用如下两种方法来计算哪种方法得到的结果最好：

* 采取与$z_b$最接近90◦角(检查最远的方法)的向量
* 选择向量，使得到的xb最接近当前的xb

以上两种方法很有效率，但是仍然会存在敏感区域：

* 当变换经过$x_c$和$y_c$的过渡决定点时
* 当机体的x轴垂直时

#### 第二角度（Second Angle）

由Thomas[15]所描述的第二角度方法，通过在遇到奇点的地方移动，成功地避免了奇点，但是这也引入了控制第二角度$\lambda$的挑战（原作者并没有很好的解释最佳控制方法）。

如果只是设定了一个恒定角度，只会改变奇异点的位置，而如果不恰当的控制第二角度也会导致姿态跳变。我们需要让第二角度$\lambda$始终落后或者超前与$z_c$。

在这种情况下，我们可以完成一个俯仰360度的翻转，但是我们并不能完成横滚90度的翻滚？？。

如果将第二角度法和消极检查法结合起来，那么该方法可以完成绝大多数的变换。

#### 仅横滚或者俯仰（Pitch and Roll only）

受fessler[16]提出的角度误差方法的启发，我们可以在于偏航无关的应用中使用如下方法：当给定飞机的姿态时，可以使用一个四元数直接表示从世界坐标系到机体坐标系的变换。这种方法避免了奇点或敏感区域的任何问题，但不允许对偏航进行任何控制。

#### 联合方法（Combined Methods）

我们提出的解决奇点问题以及设置偏航能力的方法是组合方法。其基本原理是用多种不同的方法计算变换，然后选择具有最小方向变化的结果。

通过x_c和y_c或者第二角度法可以计算得到三个不同的机体坐标系，另外加上每个坐标系的反坐标系，一共是六个坐标系，通过比较每个坐标系与上一次计算的结果，可以选择一个最接近上一个坐标系的坐标系。

另外也可以将第二角度法的计算去掉，变为四轴集合，但是在某些方面仍然可能会遇到问题。

使用第二角度法，并且给定更多的轴，可以有效减少因为方法过渡带来的突变误差。

这种六轴组合方法是足够健壮的，可以包含可能的动态过渡测试的传播，而不需要在方向上发生大的离散变化。

#### 讨论（Discussion）

* 六轴组合法虽然在大多数场景都表现的很好，但是他的计算量大概是最简单的标准法的三倍左右。
* 如果飞机并不做大姿态机动飞行，那么仅需要使用标准方法即可。
* 如果在应用中控制yaw角并不是那么重要，那么可以只使用俯仰和滚转是一个很好的计算方法，可以简单快速的计算所需要的方向。

### 机动轨迹飞行应用

在实际飞行中，如果产生大机动飞行，则很有可能经过前文所述的一系列奇异点或其附近的状态，这将导致飞机姿态发生大幅度改变。

联合的四轴甚至六轴方法仍然会遇到一些不必要的机动情况。



## 【4】Aggressive Flight With Quadrotors for Perching on Inclined Surfaces

仅阅读了动态控制方面的内容，了解第二角度法结果。

第二角度法，使用了另一个角度，让YAW坐标系的坐标轴在$x_c$和$z_c$形成的平面内进行旋转，确保$x_c$与$z_b$的夹角始终保持九十度，相当于$z_b$的法平面和$y_c$的法平面相交，得到一个向量，该向量和$x_c$的夹角就是第二角度。

问题在于：如何计算第二角度，让旋转后的轴始终超前（落后）于$z_b$呢？原作者在文章中并没有详细说明。

文章【3】说当进行横滚90度的时候仍然会出现奇异点，根据前面推断，不难想象，当z_b靠近y_c时，按照之前的方法计算第二角度，会导致第二角度计算值很敏感，从而导致发生奇异，此时奇异点会发生大幅度旋转。



## 【5】Robust Trajectory Planning for Spatial-Temporal Multi-Drone Coordination in Large Scenes

### 摘要

在本文中，我们提出了一种多无人机在大场景下高速规划的框架。该方法使用了以自由空间为导向的地图（free-space-oriented map）将优化从难以处理的环境数据中解放出来。我们设计了一种胶囊型的安全约束来避免飞机在干扰下偏离正常飞行过程导致的互相碰撞。我们进一步证明了包含非线性阻力效应（nonlinear drag effects）的无人机动力学的最小奇异点的微分平坦性。利用平坦映射，可以有效的对平坦输出进行轨迹优化，同时考虑到高速的阻力，保持物理限制。我们在大规模仿真中验证了该框架的鲁棒性和有效性，该方法可以在几分钟内计算出数百架无人机满足高保真飞机约束的无碰轨迹。

### 介绍

在多机规划中存在几个实际的问题：

* 规划算法会为了障碍物信息频繁的访问地图数据，在大场景下，一个以障碍物为主导的地图可能会过于笨重，导致计算上的瓶颈
* 多机器人规划的鲁棒算法应该利用空间和时间两方面的灵活性，但是后者常常在文献中被忽略
* 无法预期的扰动可能会让无人机偏离标准轨迹很多。在这种情况下，可能的相互碰撞会威胁到整个系统。因此实际飞行进度和规划飞行进度的不匹配很容易让规划阶段确保的基于距离的安全准则失效。
* 在大场景下的告诉飞行固然可以提高任务效率，但是当确保飞机的物理限制时，空气动力学阻力的影响不能被忽略。这也使得过渡简化的可行性标准在这里不够充分。

本文提出了一种多机器人在大场景下的鲁棒规划框架，该框架基于三个飞行合作准则建立。

* 一个避免障碍物的准则确保在飞行器之间和飞行器和环境之前不会发生任何碰撞。我们使用一个自由空间主导的地图来代替，这个地图用一系列多面体紧密地近似所有空闲配置空间。
* 使用MINCO轨迹在多面体形状的走廊中进行在线的时空优化。
* 我们通过一个时空“胶囊”约束设计了一个互相安全的准则，它允许时间和位置上的大飞行误差。

因此，我们的规划结果在合理扰动下是鲁棒的。为了确保一个动态的可行性准则，我们展示了我们的无人机在非线性阻力下的微分平坦性。平面映射使得通过MINCO支持的惩罚函数强制用户定义的物理限制成为可能。

贡献总结：

* 一种带走廊生成的地图多面体化方案，可用于在线自由空间的查询
* 针对大飞行误差的鲁棒互相安全问题，设计了一种时空胶囊约束
* 给出了受限于非线性阻力影响的无人机最小奇异点的微分平坦模型
* 为具有物理限制的多无人机协作提供了一种系统的鲁棒轨迹规划方法

### 带非线性阻力的车辆动力学模型物理限制

大场景下的高速飞行对动态可行性有更高的要求。不像现在已有的过度简化动力学的基于微分平坦方法，我们执行了非线性阻力作用下的物理限制，同时仍然在平坦输出空间中做优化。

状态：$x = \{ r,\dot{r},R \}$，其中$r\in \mathbb{R}^3$和$R\in SO(3)$分别为机器人的平移和旋转

输入：$u = \{f,\omega\}$，其中$f\in \mathbb{R}_{\geq0}$为推力，$\omega\in \mathbb{R}^3$为角速度

由此，无人机的动力学方程为：
$$
\left\{ \begin{array}{lc} m\ddot{r}=-mge_3-RDR^T\sigma(||\dot{r}||)\dot{r}+Rfe_3 & (10a)\\ \dot{R}=R\hat{\omega} & (10b)\end{array} \right.
$$
其中$D=diag\{d_h,d_h,d_v\}$，表示水平对称的阻力系数矩阵，$\sigma:\mathbb{R}_{\geq0}\mapsto\mathbb{R}_{\geq0}$，表示一个从正实数到正实数的映射（可以理解为函数），$\hat{\omega}$是一个斜对称矩阵(skew-symmetric matrix)。注意到我们在此假设无人机是水平对称的。正如文献[18]和[19]中假设的一样，这种假设可以将偏航航向和阻力效应解耦，在多旋翼飞机中很常见。根据文献[20]的分析，我们在集中参数模型中应用了$\sigma(x)=1+C_px$的函数表达式，来体现线性阻力[21]和摩擦阻力。

公式10对应的物理约束$\mathcal{G}(x,u)\preceq0$表示为：
$$
\mathcal{G}(x,u)=\left(\begin{array}{c} ||\dot{r}||^2-v^2_{max} \\ ||\omega||^2-\omega^2_{max} \\ \arccos(e_3^TRe_3)-\theta_{max} \\ (f-f_m)^2-f_r^2 \end{array}  \right) \preceq 0 \ \ \ \ \ \ (11)
$$
其中$f_m=(f_{max}+f_{min})/2$，$f_r=(f_{max}-f_{min})/2$是中间常量。最大速度，最大角速度，最大倾角和最大推力上面带max的量，此外，限制倾斜角度可以限制过度激进的机动，$f_{min}$作为下界推力，有助于扰动下的姿态稳定。

将状态x和输入u的物理限制都包含在一个平坦轨迹的基本思想是利用微分平坦的代数变换和其导数。我们用$r^{[s]}$表示一系列有限导数$(r,\dot{r},\dots，r^{(s)})$，同样的用$\psi$表示YAW角。微分平坦的变换即可表示为：
$$
(x,u)=\Psi(r^{[s]},\psi^{[s]})
$$
所有的物理限制都由惩罚函数来执行：
$$
\mathcal{\Gamma}_3(c,T)=\int_{t_o}^{t_f} \mathbf{1}^T\phi_{\mu}\left[ \mathcal{G}\circ \Psi\left(r^{[s]}(t),\psi^{[s]}(t)\right)\right]\,dt
$$
其中，$\psi(\cdot)$是任何规划的YAW角。为了保证光滑飞行，我们还增加了一个能量函数：
$$
\mathcal{\Gamma}_0(c,T)=\int_{t_o}^{t_f} \left( ||r_{(s)(t)}||_2^2=\rho \right)\,dt
$$
其中，$t_f$和$r(\cdot)$都取决于$\{c,T\}$。

接下来主要使用平坦量计算状态和输出，首先，我们将$x_b=Re_1$和$y_b=Re_2$两个向量分别左乘到10a式子上去，可以得到：
$$
(Re_i)^T \cdot\left\{ m\ddot{r} + mge_3 + RDR^T\sigma(||\dot{r}||)\dot{r}\right\} =(Re_i)^T \cdot Rfe_3
$$
其中，等式右边为正交向量点乘，因此为零，而等式左边的$RDR^T$在左乘单位向量之后，只剩下$d_h$一个常数，因此上式整理之后可以变为：
$$
(Re_i)^T \cdot\left\{ \ddot{r} + ge_3 + \frac{d_h}{m}\sigma(||\dot{r}||)\dot{r}\right\} =0
$$
从几何的物理意义上考虑，$Re_i$与另一个向量点乘为零，则可以判断该向量的方向为$z_b$的方向，又因为四旋翼无人机的机体坐标系Z轴为推力的正方向，因此：
$$
z_b=\mathcal{N}(\ddot{r} + ge_3 + \frac{d_h}{m}\sigma(||\dot{r}||)\dot{r})
$$
其中$\mathcal{N}(x)=x/||x||_2$，由此可以得到$z_b$的方向，之后根据$z_b$再可以带入(10a)计算推力：
$$
f=z_b^T(m\ddot{r}+d_v\sigma(||\dot{r}||)\dot{r}+mge_3)
$$
接下来使用Hopf fibration来计算得到姿态：







## 【6】Control of Quadrotors Using the Hopf Fibriation on SO(3)

### 概述

文献8和14使用了无位置反馈，仅耦控制四旋翼姿态的SO3控制器。没有位置反馈，就不存在奇异性问题，这种方法已经被证明在SO3上几乎所有地方都是稳定的。

提出了一种使用Hopf fibration的四旋翼几何控制器，这控制器对SO3上几乎所有的控制点都是有效的，并且具有比另外两个文章【8,14】更远离悬停的奇异点。

霍普夫映射可以形成一个如下映射：$S^2\times S^1\rightarrow SO(3)$ ，我们提出了一种新的霍普夫纤维丛控制算法（HFCA），该控制器使用了两个属于$S^2$的charts，来表示整个流形。因为需要使用两个charts才能覆盖S1，我们最终使用了 $2\times2=4$个charts来覆盖S03空间，这也是理论上的最小数量。使用参数化的SO3空间，我们可以使用几何反馈控制器在SE3空间中任意一点控制无人机。因为在SE3上每个状态对我们的控制器都是有效地，我们也必须有一个在这个空间中的可行轨迹，该轨迹能满足平台只有四个输入的非完整约束条件。我们在仿真中验证了该控制器的性能，并且与其他几何控制器进行了比较。在一个实验平台上，我们展示了激进的机动飞行，这种飞行会到达其他其他控制器的奇异点，但是可以在我们的方法上可行。

与传统四旋翼几何控制器相比，我们的方法优势在于：

* 霍普夫纤维丛直接表示了机器人的旋转，而不需要进行GS分解
* 可以确保在SO3空间上所有方向的位置反馈都具有连续型控制输入
* 这种表示方法很容易允许通过选择YAW的方式让机体坐标系的YAW角度速度为零，而这一般都是控制机器人位置的冗余自由度。

<img src="../../WMY/Typora/All File Picture/image-20211108165643658.png" alt="image-20211108165643658" style="zoom:50%;" />

HFCA控制器根据机器人油门朝向，在两个SE3连续的几何的空间上进行切换。其中一个奇异点在上方，另一个在下方。为了可视化，结构的每个可行方向集都被绘制成球体的子集，结构上的每个奇异点都被展开(?)。通过在这两个映射上面切换，我们确保了我们的控制器在SO3上所有的方向都是可行的，并且因此可以在SE3上有位置反馈。

文章内容概要：

* 第二部分：回顾动态模型和微分平坦模型
* 第三部分：霍普夫纤维丛的映射和在四旋翼上的应用
* 第四部分：HFCA算法的设计，以及使用第二次霍普夫映射来完全覆盖SO3的必要性
* 第五部分：实验条件，轨迹生成和仿真实验结果

### 建模

#### 动态模型

电机转推力扭矩模型：

![image-20211110122917972](../../WMY/Typora/All File Picture/image-20211110122917972.png)

牛顿欧拉方程：

![image-20211110122937777](../../WMY/Typora/All File Picture/image-20211110122937777.png)

状态和输入的表示符号和所属域：

![image-20211110123106853](../../WMY/Typora/All File Picture/image-20211110123106853.png)

#### 微分平坦

微分平坦的状态量表示和所属域：

![image-20211110123458664](../../WMY/Typora/All File Picture/image-20211110123458664.png)

一个映射不足以完成从3到5的映射，因为SO3需要至少四个部分。传统的几何方法使用了一个映射，可以覆盖SO3上的一个开环子集，这个子集排除了a2和期望推力平行的情况。另外对于只能产生正向推力的四旋翼来说，当所有电机停转时会产生奇异现象。因此我们假设机器人永远不会产生自由下落情况。

#### SE3上的几何控制

四旋翼几何控制的原理是基于机器人的推力平行于机体坐标系的b3轴。这可以使用如下约束来表示：

![image-20211110131310853](../../WMY/Typora/All File Picture/image-20211110131310853.png)

使用中间过渡坐标系完成对机体坐标系的计算，但是这也导致了期望推理与a1平行时产生的奇异点：

![image-20211110131637962](../../WMY/Typora/All File Picture/image-20211110131637962.png)

有了期望姿态，我们就可以使用之前文章的方式，计算得到扭矩和推力：

![image-20211110131741889](../../WMY/Typora/All File Picture/image-20211110131741889.png)

![image-20211110131753045](../../WMY/Typora/All File Picture/image-20211110131753045.png)

在这里，我们可以注意到只要公式6成立，那么我们可以使用任何参数化SO3的方法表示$R(\gamma)$。如果我们使用欧拉角，则$R(\gamma)$是一个复杂的函数，因为欧拉角是一个非线性约束。我们同样在下文使用霍普夫映射来代替公式6。

我们可以证明，从期望推理构造旋转矩阵R的几何控制方法是不可能不引入奇异点的。对于任意状态$\gamma$，可以构造一个处处光滑微分同胚的非奇异$R(\gamma)$。

### 四元数与霍普夫纤维丛
