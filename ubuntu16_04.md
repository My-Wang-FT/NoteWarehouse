# Ubuntu16.04重装系统后的软件环境安装指南

## 如何解决Ubuntu16.04与Windows系统时间不同步

>* `timedatectl set-local-rtc true`

## Mouse and Touchpad

* [x] nautural scrolling

## 搜狗中文输入法

  >* install sougou package,官网;
  >* 打开System Setting,找到Language Support，install简体中文;"apply system-wide";将键盘输入法系统由默认的iBus改成fcitx;
  >* `sudo reboot`
  >* (4)点击右上角输入法小图标,选择config，去掉勾，点击左下角小加号，找到Sogou Pinyin添加即可
  >* 若在菜单栏的右上角出现两个输入法图标，则：`sudo apt remove fcitx-ui-qimpanel`;再重启
  >* 搜狗拼音的简体、繁体相互转换：Ctrl+Shift+F

## system monitor

  >* `sudo add-apt-repository ppa:fossfreedom/indicator-sysmonitor`
  >* `sudo apt update`
  >* `sudo apt install indicator-sysmonitor`

启动，右击设置"Run on startup"

## Shadowsocks(optional)

  >* `sudo add-apt-repository ppa:hzwhuang/ss-qt5`
  >* `sudo apt update`
  >* `sudo apt install shadowsocks-qt5`
  >* 在Startup Application里面设置shadowsocks开机自动启动

## Google Chrome

  >* install Chrome using package;
  >* then, remove <http://dl.google.com/linux/chrome/deb/> from the software source!
  >* 打开shadowsocks,设置全局代理；然后，登录google账号，更新Chrome的设置(这个需要一些时间，约10分钟)。
  >* 插件1：Adblock Plus的设置
  >* 插件2：SwitchyOmega auto-switch的设置：github上已经保存了设置文件

## 更换Terminal

终端采用zsh和oh-my-zsh，既美观又简单易用，主要是能提高你的逼格！！！ZSH, also called the Z shell

  >* 首先，安装zsh：

   `sudo apt install zsh`

  >* 将默认终端由bash换成zsh

  `chsh -s /bin/zsh   #注意：不要使用sudo`

  >* 安装oh-my-zsh 项目来帮我们配置 zsh，采用wget安装：

  `wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh`

  但在国内按上面的方法安装oh-my-zsh比较麻烦，请参考这篇[国内教程](https://blog.csdn.net/qwe641259875/article/details/107201760/)

  >* 安装zsh-syntax-highlighting语法高亮插件

  ```bash
  cd /usr/share/
  sudo git clone https://github.com/zsh-users/zsh-syntax-highlighting.git
  echo "source ${(q-)PWD}/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh" >> ${ZDOTDIR:-$HOME}/.zshrc
  source ~/.zshrc
  ```

  >* 安装zsh-autosuggestions语法历史记录插件(自动补全插件)

  ```bash
  git clone git://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions
  echo "source $ZSH_CUSTOM/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh" >> ~/.zshrc
  source ~/.zshrc
  ```

  >* 修改透明度为10%
  >* 命令行自动提示：没多大用，用Tab即可

## 好看的主题(optional)

  >* 先安装unity-tweak-tool

`sudo apt install unity-tweak-tool`

  >* 安装Flatabulous主题

```bash
sudo add-apt-repository ppa:noobslab/themes
sudo apt update
sudo apt install flatabulous-theme
```

  >* 安装扁平化的图标

```bash
sudo add-apt-repository ppa:noobslab/icons
sudo apt update
sudo apt install ultra-flat-icons
```

  >* 安装完成后，打开unity-tweak-tool软件.修改theme为Flatabulous,修改icon为Ultra-flat

## 安装docky(optional)

  >* `sudo apt install docky`
  >* 卸载：`sudo apt remove docky`
  >* [参考博客](https://www.jianshu.com/p/4bd2d9b1af41)
  >* 小技巧：如果想要删除docky上的图标(除了docky本身)，直接用鼠标将其拖出即可；此外，如果要将一个图标添加到docky,只需要打开这个软件，再在docky上右击，选择"pin to dock"
  >* 也可以使用cairo-dock，会玩的话, 足以媲美macOS自带的dock.缺点是不能正常显示一些软件的图标，如VSCode,需要手动设置

## 安装WPS

  >* 直接从官网下载安装包安装
  >* 解决WPS缺少字体问题：

  将WPS缺少的字体复制到 /usr/share/fonts/wps-office 目录下（字体文件已备份，共6个文件）

生成字体的索引信息,再更新字体缓存：

  ```bash
  sudo mkfontscale
  sudo mkfontdir
  sudo fc-cache -f -v
  ```

  >* 安装Microsoft开源字体

  ```bash
  sudo apt update
  sudo apt install ttf-mscorefonts-installer
  ```

  >* 将Windows的字体搬到ubuntu
  
  在 /usr/share/fonts/ 中新建文件夹 fonts_from_windows . 再把567个字体文件copy到此文件夹下。重复上一步的字体更新过程。重启电脑。这样就能再ubuntu下完成80%左右的办公任务了！！！

  BUG:WPS中不能正常显示出新增的windows字体名

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

* [ ] minimap
* [x] auto save  # 0.1s
  
>* 好的插件:C/C++, C++ Intellisense, CMake, CMake Tools, cmake-format, Code Runner, Markdown All in One, markdownlint, Python, ROS, XML, Remote SSH

## OpenCV

推荐安装opencv3.4.8. 若在ubuntu14.04,则安装opencv2.4.14

>* 安装opencv前的准备

  参考[此教程](https://github.com/HuangJianxjtu/opencv_learning.git)

## ROS-kinetic

  >* 先设置国内的镜像源:

```bash
sudo sh -c '. /etc/lsb-release && echo "deb http://mirrors.ustc.edu.cn/ros/ubuntu/ $DISTRIB_CODENAME main" > /etc/apt/sources.list.d/ros-latest.list'
wget https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -O - | sudo apt-key add -
```

  >* 然后继续按官网的安装教程继续安装。
  >* 再测试turtlesim和Gazebo是否能正常工作。
  >* 解决gazebo打不开world的解决方法：

  ```bash
  cd ~/.gazebo/
  mkdir -p models
  cd ~/.gazebo/models/
  wget http://file.ncnynl.com/ros/gazebo_models.txt
  wget -i gazebo_models.txt #下载这些模型大概需要1小时！为了节省时间，已经备份模型了
  ls model.tar.g* | xargs -n1 tar xzvf
  ```


## 其他

  >* ubuntu下好用的视频播放器：smplayer
  >* PDF工具：mendeley, foxit reader(unrecommended)
  >* VPN工具：easyConnect

## how to solve the problem "Error in REST request" in Gazebo in Ubuntu18.04

  Answer: change ~/.ignition/fuel/config.yaml as following.
      url: <https://api.ignitionfuel.org>
  to
      url: <https://api.ignitionrobotics.org>

## Chinese input for Ubuntu18.04

  >* 在setting里面点"install/Remove Languages", 点击安装Chinese
  >* 重启
  >* 添加"Chinese(Intelligent Pinyin). win+空格切换输入法
