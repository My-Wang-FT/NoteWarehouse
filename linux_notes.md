 form https://github.com/HuangJianxjtu/linux_learning/blob/master/linux_notes.md

# Linux Notes

## 1.远程连接

### 1.1 命令行连接 -- SSH

* server端--局域网或在有固定IP的服务器

    server即我们要远程控制的电脑。确认开启ssh server服务

    ```bash
    systemctl status sshd.service   # for ubuntu 16.0+ or centos 7+, which  based on systemctl
    service ssh status  # for ubuntu 14.04
    ```

    若显示没有这个service, 则需安装ssh-server:

    ```bash
    sudo apt update
    sudo apt install openssh-server
    sudo service ssh restart    # 重启ssh服务, for ubuntu 14.04
    sudo systemctl start sshd    # 重启ssh服务, for ubuntu 16.04+
    ```

    能够ping通server的IP则说明ssh service正常

* server端--无固定IP的外网连接（内网穿透）

  * 推荐使用[slackhq/nebula](https://github.com/slackhq/nebula), 依靠云服务器实现内网穿透。 自动选择最短路径，P2P,太TM牛逼了
  * 使用[ngrok](https://dashboard.ngrok.com/get-started),用github账号登陆，可以得到random的公网地址。启动ngrok:
    `$./ngrok tcp 22`
  * 用阿里云，自己搭建ngrok服务器，[参考1](https://www.zhihu.com/question/27771692),[参考2](https://www.jianshu.com/p/d35962b0dba4)
  * zerotier

* client端

    `ssh USER@_IP_ -p [port]`

    在linux中，如果不写端口号，则默认是22

* ssh免密登录
  
  `ssh-copy-id -i ~/.ssh/id_dsa.pub _USER_@__IP__`

* ssh多级跳转

  即:A通过ssh登录B, 再通过ssh登录C. [参考](https://blog.csdn.net/CityzenOldwang/article/details/77097703)

### 1.2 图形界面连接

推荐使用Teamviewer,不推荐VNC

使用`ssh -X USER@_IP_ -p [port]`进行图形界面传输, 似乎传输不太顺畅。。。

xdg-open命令会根据系统的默认打开方式打开一个文件，比如图片、音频等，比较花哨

## 2.电脑间文件传输

* scp

    scp -- secure cp. 传输更加安全。适合单个文件快捷传输。 [command](https://www.runoob.com/linux/linux-comm-scp.html):

    `scp [可选参数] file_source file_target`

    example:

    ```bash
    scp root@www.runoob.com:/home/root/others/music /home/space/music/1.mp3
    #scp 命令使用端口号 4588
    scp -P 4588 remote@www.runoob.com:/usr/local/sin.sh /home/administrator
    ```

* ftp

传输数据快，适合大文件传输。通常用来做网盘，[待尝试](https://www.cnblogs.com/hexige/p/7809481.html)

## 3.通过.deb文件安装软件

`sudo apt install /path/to/package/name.deb`

## 4. Ubuntu16.04的默认截图方式

>* 对整个屏幕截图： PrintScreen
>* 对活动窗口截图： Alt + PrintScreen
>* 对任意矩形截图： Shift + PrintScreen
>* 以上三个快捷键再加上Ctrl，就会默认复制截图到粘贴板

## 5. 要尽量使用apt, 而不是apt-get

## 6. Linux开机自启动脚本

* ubuntu 14.04
  
  将命令写入/etc/rc.local文件中，[参考](https://blog.csdn.net/qq_24233615/article/details/81084222)

* ubuntu 16.04+, centos 7+
  
  使用systemctl service方式，非常方便！将服务文件(**.service)放入/etc/systemd/system/目录下。运行

  ```bash
  sudo systemctl daemon-reload  # 刷新配置
  sudo systemctl enable **.service  # 设置开机自启。注意：.service文件必须[Install]区域(状态是static)，否则无法enable. service文件的模板见下文
  sudo systemctl start **.service  # 启动该服务
 
 # 其他有用的systemctl命令
  sudo systemctl stop **.service  # 停止该服务
  systemctl status **.service  # 查询该服务的状态
  systemctl is-enabled **.service   # 查询该服务是否已经设置为开机自启，返回enable说明已经设置了
  ```

* .service文件模板
  
  ```.service
  [Unit]
  Description=slackhq nebula
  After=network.target

  [Service]
  # 下一行是所要运行的命令
  ExecStart=/etc/nebula/nebula -config /etc/nebula/config.yml

  # 设置服务安装方式，支持多用户.这样才能设置开机自启动
  [Install]  
  WantedBy=multi-user.target
  ```

## 7. 命令行后台运行程序 -- nohup &

用法：

```
nohup [YOUR_COMMAND] &
```

NOTE: nohup命令执行后，不要直接关闭终端，使用exit命令退出会话.否则会出现nohup不起作用的情况！

[如何终止正在后台运行的程序](https://blog.csdn.net/qq_29663071/article/details/81030396)

