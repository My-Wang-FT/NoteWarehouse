[toc]

# ROS 操作系统

## 零、文件系统

![image-20200706102646605](D:/WMY/Typora/All File Picture/image-20200706102646605.png)

### Catkin编译系统

* 以package为单元编译
* 需要在workspace目录下编译，目录下需要有src文件：`$catkin_make`
* 编译之后需要手动source一下环境
* `$source YOUPATH/catkin_ws/devel/setup.bashrc`
* `$echo “source YOUPATH/catkin_ws/devel/setup.bashrc” >> ./bashrc`

### Package文件

* 包括package.xml与Cmakelists.txt

* `$catkin_create_pkg <package_name> [depend1] [depend2] ...`

  如：`$catkin_create_pkg test_pkg roscpp rospy std_msgs`

* `rospack help`

* `rospack list`：列出所有package，需要有source

* `rospack depends [package]`：显示package的依赖包

* `rospack find [package]`：定位某个package

* `rospack prifile`：刷新所有package的位置记录

* `roscd [package]`：cd到package的所在路径

* `rosls [package]`：列出package下的文件

* `rosdep check [package]`：检查package的依赖是否满足

* `rosdep install [package]`：安装package的依赖项

* `rosdep db`：生成和显示依赖的数据库

* `rsodep init`：初始化/etc/ros/rosdep中的源

* `rosdep keys [package]`：列出package所需要的所有公钥

* `rosdep update`：更新本地的rosdep数据库

* `rosdep install --from-paths src --ignore-src --rosdistro=kinetic -y`，用于安装工作空间中 src 路径下所有package的依赖项（由pacakge.xml文件指定）

## 一、通信架构

### Master

* 管理节点，实现节点之间的点对点通信
* node启动时需要向master注册
* `$roscore`：启动master，rosout（日志输出Node），和parameter server（参数服务器）  

### Node

* ROS进程，pkg中的可执行文件运行的实例
* `$rosrun [pkg_name] [node_name]`
* `$rosnode list`
* `$rosnode info [node_name]`
* `$rosnode kill [node_name]`
* `$rosnode ping`
* `$rosnode cleanup`：清除不可到达的节点的注册信息

### Launch文件

* 一次性启动多个Node

* launch文件遵循着xml格式规范，是一种标签文本，它的格式包括以下标签：

  ```xml
  <launch> 		<!--根标签-->
  <node> 			<!--需要启动的node及其参数-->
  <include> 		<!--包含其他launch-->
  <machine> 		<!--指定运行的机器-->
  <env-loader> 	<!--设置环境变量-->
  <param> 		<!--定义参数到参数服务器-->
  <rosparam> 		<!--启动yaml文件参数到参数服务器-->
  <arg> 			<!--定义变量-->
  <remap> 		<!--设定参数映射-->
  <group> 		<!--设定命名空间-->
  </launch> 		<!--根标签-->
  ```

* `$roslaunch [pkg_name] [file_name.launch]`：自动启动master（如果没启动）和多个节点

### Topic

* ROS中的异步通信，采用publish和subscribe通信，类似与公告板
*  一个Topic可以有多个Subscriber，可以有多个Publisher发布同一个Topic
* `$rostopic list`
* `$rostopic info /topic_name`
* `$rostopic echo /topic_name`：显示某个Topic的内容
* `$rostopic pub /topic_name …`
* `rostopic hz topic_nale`
* `$rostopic type /topic_name`：显示某个Topic的类型

### Message

* Topic内容的数据类型定义在.msg中，是Class，是Struct
* `$rosmsg list`
* `$rosmsg show /msg_name`
* 常用msg合集，请见下文

### Service

* 同步通信方式，通过request和reply方式通信，多对一，类似与服务器和用户
* 一个是Server，一个是Client，C发送请求之后会等待S回复，之后才会继续
* 可以理解为从其他Node调用函数
* 通信**格式**为.srv
* srv只能嵌套msg，不能嵌套srv
* 需要修改package.xml和CMakeList.txt
* `$rosservice list`
* `$rosservice info service_name`
* `$rosservice call service_name args`：args为传入参数
* `$rossrv list`
* `$rossrv show srv_name`

### Parameter Server

* 可以理解为全局define，是存储各种参数的字典
* 可以通过命令行，launch文件和API中维护
* `$rosparam list`：列出当前所有参数
* `$rosparam get param_key`：显示某个参数的值
* `$rosparam set param_key param_value`：设置某个参数的值
* `$rosparam dump file_name`：保存参数到文件
* `$rosparam load file_name`：从文件读取参数，需要遵从YAML格式
* `$rosparam delete param_key`：删除参数

### Action

* 有状态反馈，常常在长时间，可抢占（被打断）的任务中
* 通信数据格式为.antion
* .action分为三部分，goal，result，feedback

## 二、常用工具

### Gazebo

* 可视化图形工具，作为主要的仿真环境
* 可以在上面进行各种物理模型等的仿真

### rviz

* 接收Topic用3D可视化呈现出来

### rqt

* rqt_graph:显示通信架构
* rqt_plot  :绘制曲线
* rqt_console   :查看日志

### rosbag

* 将Topic数据记录到.bag中，可以回放
* `$rosbag record topic_names`：记录某些Topic到bag中
* `$rosbag record -a`：记录所有Topic到bag中
* `$rosbag play bag_file`：回放bag





## 常用msg结构合集

本小节主要介绍常见的message类型，包括std_msgs, sensor_msgs, nav_msgs,geometry_msgs等

### Vector3.msg

```
#文件位置:geometry_msgs/Vector3.msg

float64 x
float64 y
float64 z
```

### Accel.msg

```
#定义加速度项，包括线性加速度和角加速度
#文件位置:geometry_msgs/Accel.msg

Vector3 linear
Vector3 angular
```

### Header.msg

```
#定义数据的参考时间和参考坐标
#文件位置:std_msgs/Header.msg

uint32 seq #数据ID
time stamp #数据时间戳
string frame_id #数据的参考坐标系
```

### Echos.msg

```
#定义超声传感器
#文件位置:自定义msg文件

Header header
uint16 front_left
uint16 front_center
uint16 front_right
uint16 rear_left
uint16 rear_center
uint16 rear_right
```

### Quaternion.msg

```
#消息代表空间中旋转的四元数
#文件位置:geometry_msgs/Quaternion.msg

float64 x
float64 y
float64 z
float64 w
```

### Imu.msg

```
#消息包含了从惯性原件中得到的数据，加速度为m/^2，角速度为rad/s
#如果所有的测量协方差已知，则需要全部填充进来如果只知道方差，则
#只填充协方差矩阵的对角数据即可
#位置：sensor_msgs/Imu.msg

Header header
Quaternion orientation
float64[9] orientation_covariance
Vector3 angular_velocity
float64[9] angular_velocity_covariance
Vector3 linear_acceleration
float64[] linear_acceleration_covariance
```

### LaserScan.msg

```
#平面内的激光测距扫描数据，注意此消息类型仅仅适配激光测距设备
#如果有其他类型的测距设备(如声呐)，需要另外创建不同类型的消息
#位置：sensor_msgs/LaserScan.msg

Header header 			#时间戳为接收到第一束激光的时间
float32 angle_min 		#扫描开始时的角度(单位为rad)
float32 angle_max 		#扫描结束时的角度(单位为rad)
float32 angle_increment #两次测量之间的角度增量(单位为rad)
float32 time_increment 	#两次测量之间的时间增量(单位为s)
float32 scan_time 		#两次扫描之间的时间间隔(单位为s)
float32 range_min 		#距离最小值(m)
float32 range_max 		#距离最大值(m)
float32[] ranges 		#测距数据(m,如果数据不在最小数据和最大数据之间，则抛弃)
float32[] intensities 	#强度，具体单位由测量设备确定，如果仪器没有强度测量，则数组为空即可
```

### Point.msg

```
#空间中的点的位置
#文件位置:geometry_msgs/Point.msg

float64 x
float64 y
float64 z
```

### Pose.msg

```
#消息定义自由空间中的位姿信息，包括位置和指向信息
#文件位置:geometry_msgs/Pose.msg

Point position
Quaternion orientation
```

### PoseStamped.msg

```
#定义有时空基准的位姿
#文件位置：geometry_msgs/PoseStamped.msg

Header header
Pose pose
```

### PoseWithCovariance.msg

```
#表示空间中含有不确定性的位姿信息
#文件位置：geometry_msgs/PoseWithCovariance.msg

Pose pose
float64[36] covariance
```

### Power.msg

```
#表示电源状态，是否开启
#文件位置：自定义msg文件

Header header
bool power
######################
bool ON = 1
bool OFF = 0
```

### Twist.msg

```
#定义空间中物体运动的线速度和角速度
#文件位置：geometry_msgs/Twist.msg

Vector3 linear
Vector3 angular
```

### TwistWithCovariance.msg

```
#消息定义了包含不确定性的速度量，协方差矩阵按行分别表示：
#沿x方向速度的不确定性，沿y方向速度的不确定性，沿z方向速度的不确定性
#绕x转动角速度的不确定性，绕y轴转动的角速度的不确定性，绕z轴转动的
#角速度的不确定性
#文件位置：geometry_msgs/TwistWithCovariance.msg

Twist twist
float64[36] covariance #分别表示[x; y; z; Rx; Ry; Rz]
```

### Odometry.msg

```
#消息描述了自由空间中位置和速度的估计值
#文件位置：nav_msgs/Odometry.msg

Header header
string child_frame_id
PoseWithCovariance pose
TwistWithCovariance twist
```