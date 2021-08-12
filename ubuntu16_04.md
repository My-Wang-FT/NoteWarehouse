# Ubuntu16.04 重装系统后的软件环境安装指南

## 如何解决 Ubuntu16.04 与 Windows 系统时间不同步

> - `timedatectl set-local-rtc true`

## Mouse and Touchpad

- [x] nautural scrolling

## 搜狗中文输入法

> - install sougou package,官网;
> - 打开 System Setting,找到 Language Support，install 简体中文;"apply system-wide";将键盘输入法系统由默认的 iBus 改成 fcitx;
> - `sudo reboot`
> - (4)点击右上角输入法小图标,选择 config，去掉勾，点击左下角小加号，找到 Sogou Pinyin 添加即可
> - 若在菜单栏的右上角出现两个输入法图标，则：`sudo apt remove fcitx-ui-qimpanel`;再重启
> - 搜狗拼音的简体、繁体相互转换：Ctrl+Shift+F

## system monitor

> - `sudo add-apt-repository ppa:fossfreedom/indicator-sysmonitor`
> - `sudo apt update`
> - `sudo apt install indicator-sysmonitor`

启动，右击设置"Run on startup"

## Shadowsocks(optional)

> - `sudo add-apt-repository ppa:hzwhuang/ss-qt5`
> - `sudo apt update`
> - `sudo apt install shadowsocks-qt5`
> - 在 Startup Application 里面设置 shadowsocks 开机自动启动

## Google Chrome

> - install Chrome using package;
> - then, remove <http://dl.google.com/linux/chrome/deb/> from the software source!
> - 打开 shadowsocks,设置全局代理；然后，登录 google 账号，更新 Chrome 的设置(这个需要一些时间，约 10 分钟)。
> - 插件 1：Adblock Plus 的设置
> - 插件 2：SwitchyOmega auto-switch 的设置：github 上已经保存了设置文件

## 更换 Terminal

终端采用 zsh 和 oh-my-zsh，既美观又简单易用，主要是能提高你的逼格！！！ZSH, also called the Z shell

> - 首先，安装 zsh：

`sudo apt install zsh`

> - 将默认终端由 bash 换成 zsh

`chsh -s /bin/zsh #注意：不要使用sudo`

> - 安装 oh-my-zsh 项目来帮我们配置 zsh，采用 wget 安装：

`wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh`

但在国内按上面的方法安装 oh-my-zsh 比较麻烦，请参考这篇[国内教程](https://blog.csdn.net/qwe641259875/article/details/107201760/)

> - 安装 zsh-syntax-highlighting 语法高亮插件

```bash
cd /usr/share/
sudo git clone https://github.com/zsh-users/zsh-syntax-highlighting.git
echo "source ${(q-)PWD}/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh" >> ${ZDOTDIR:-$HOME}/.zshrc
source ~/.zshrc
```

> - 安装 zsh-autosuggestions 语法历史记录插件(自动补全插件)

```bash
git clone git://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions
echo "source $ZSH_CUSTOM/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh" >> ~/.zshrc
source ~/.zshrc
```

> - 修改透明度为 10%
> - 命令行自动提示：没多大用，用 Tab 即可

## 好看的主题(optional)

> - 先安装 unity-tweak-tool

`sudo apt install unity-tweak-tool`

> - 安装 Flatabulous 主题

```bash
sudo add-apt-repository ppa:noobslab/themes
sudo apt update
sudo apt install flatabulous-theme
```

> - 安装扁平化的图标

```bash
sudo add-apt-repository ppa:noobslab/icons
sudo apt update
sudo apt install ultra-flat-icons
```

> - 安装完成后，打开 unity-tweak-tool 软件.修改 theme 为 Flatabulous,修改 icon 为 Ultra-flat

## 安装 docky(optional)

> - `sudo apt install docky`
> - 卸载：`sudo apt remove docky`
> - [参考博客](https://www.jianshu.com/p/4bd2d9b1af41)
> - 小技巧：如果想要删除 docky 上的图标(除了 docky 本身)，直接用鼠标将其拖出即可；此外，如果要将一个图标添加到 docky,只需要打开这个软件，再在 docky 上右击，选择"pin to dock"
> - 也可以使用 cairo-dock，会玩的话, 足以媲美 macOS 自带的 dock.缺点是不能正常显示一些软件的图标，如 VSCode,需要手动设置

## 安装 WPS

> - 直接从官网下载安装包安装
> - 解决 WPS 缺少字体问题：

将 WPS 缺少的字体复制到 /usr/share/fonts/wps-office 目录下（字体文件已备份，共 6 个文件）

生成字体的索引信息,再更新字体缓存：

```bash
sudo mkfontscale
sudo mkfontdir
sudo fc-cache -f -v
```

> - 安装 Microsoft 开源字体

```bash
sudo apt update
sudo apt install ttf-mscorefonts-installer
```

> - 将 Windows 的字体搬到 ubuntu

在 /usr/share/fonts/ 中新建文件夹 fonts_from_windows . 再把 567 个字体文件 copy 到此文件夹下。重复上一步的字体更新过程。重启电脑。这样就能再 ubuntu 下完成 80%左右的办公任务了！！！

BUG:WPS 中不能正常显示出新增的 windows 字体名

## 清理不必要的软件

```bash
sudo apt update
sudo apt upgrade
sudo apt remove firefox libreoffice-common unity-webapps-common
sudo apt remove thunderbird totem rhythmbox empathy brasero simple-scan gnome-mahjongg aisleriot
sudo apt remove gnome-mines cheese transmission-common gnome-orca webbrowser-app gnome-sudoku  landscape-client-ui-install
sudo apt remove onboard deja-dup
```

## VScode

- [ ] minimap
- [x] auto save # 0.1s

> - 好的插件:C/C++, C++ Intellisense, CMake, CMake Tools, cmake-format, Code Runner, Markdown All in One, markdownlint, Python, ROS, XML, Remote SSH

## OpenCV

推荐安装 opencv3.4.8. 若在 ubuntu14.04,则安装 opencv2.4.14

> - 安装 opencv 前的准备

参考[此教程](https://github.com/HuangJianxjtu/opencv_learning.git)

## ROS-kinetic

> - 先设置国内的镜像源:

```bash
sudo sh -c '. /etc/lsb-release && echo "deb http://mirrors.ustc.edu.cn/ros/ubuntu/ $DISTRIB_CODENAME main" > /etc/apt/sources.list.d/ros-latest.list'
wget https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -O - | sudo apt-key add -
```

> - 然后继续按官网的安装教程继续安装。
> - 再测试 turtlesim 和 Gazebo 是否能正常工作。
> - 解决 gazebo 打不开 world 的解决方法：

```bash
cd ~/.gazebo/
mkdir -p models
cd ~/.gazebo/models/
wget http://file.ncnynl.com/ros/gazebo_models.txt
wget -i gazebo_models.txt #下载这些模型大概需要1小时！为了节省时间，已经备份模型了
ls model.tar.g* | xargs -n1 tar xzvf
```

## 其他

> - ubuntu 下好用的视频播放器：smplayer
> - PDF 工具：mendeley, foxit reader(unrecommended)
> - VPN 工具：easyConnect

## how to solve the problem "Error in REST request" in Gazebo in Ubuntu18.04

Answer: change ~/.ignition/fuel/config.yaml as following.
url: <https://api.ignitionfuel.org>
to
url: <https://api.ignitionrobotics.org>

## Chinese input for Ubuntu18.04

> - 在 setting 里面点"install/Remove Languages", 点击安装 Chinese
> - 重启
> - 添加"Chinese(Intelligent Pinyin). win+空格切换输入法
