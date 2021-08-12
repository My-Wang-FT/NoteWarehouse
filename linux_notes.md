form https://github.com/HuangJianxjtu/linux_learning/blob/master/linux_notes.md

# Linux Notes

## 1.远程连接

### 1.1 命令行连接 -- SSH

- server 端--局域网或在有固定 IP 的服务器

  server 即我们要远程控制的电脑。确认开启 ssh server 服务

  ```bash
  systemctl status sshd.service   # for ubuntu 16.0+ or centos 7+, which  based on systemctl
  service ssh status  # for ubuntu 14.04
  ```

  若显示没有这个 service, 则需安装 ssh-server:

  ```bash
  sudo apt update
  sudo apt install openssh-server
  sudo service ssh restart    # 重启ssh服务, for ubuntu 14.04
  sudo systemctl start sshd    # 重启ssh服务, for ubuntu 16.04+
  ```

  能够 ping 通 server 的 IP 则说明 ssh service 正常

- server 端--无固定 IP 的外网连接（内网穿透）

  - 推荐使用[slackhq/nebula](https://github.com/slackhq/nebula), 依靠云服务器实现内网穿透。 自动选择最短路径，P2P,太 TM 牛逼了
  - 使用[ngrok](https://dashboard.ngrok.com/get-started),用 github 账号登陆，可以得到 random 的公网地址。启动 ngrok:
    `$./ngrok tcp 22`
  - 用阿里云，自己搭建 ngrok 服务器，[参考 1](https://www.zhihu.com/question/27771692),[参考 2](https://www.jianshu.com/p/d35962b0dba4)
  - zerotier

- client 端

  `ssh USER@_IP_ -p [port]`

  在 linux 中，如果不写端口号，则默认是 22

- ssh 免密登录

  `ssh-copy-id -i ~/.ssh/id_dsa.pub _USER_@__IP__`

- ssh 多级跳转

  即:A 通过 ssh 登录 B, 再通过 ssh 登录 C. [参考](https://blog.csdn.net/CityzenOldwang/article/details/77097703)

### 1.2 图形界面连接

推荐使用 Teamviewer,不推荐 VNC

使用`ssh -X USER@_IP_ -p [port]`进行图形界面传输, 似乎传输不太顺畅。。。

xdg-open 命令会根据系统的默认打开方式打开一个文件，比如图片、音频等，比较花哨

## 2.电脑间文件传输

- scp

  scp -- secure cp. 传输更加安全。适合单个文件快捷传输。 [command](https://www.runoob.com/linux/linux-comm-scp.html):

  `scp [可选参数] file_source file_target`

  example:

  ```bash
  scp root@www.runoob.com:/home/root/others/music /home/space/music/1.mp3
  #scp 命令使用端口号 4588
  scp -P 4588 remote@www.runoob.com:/usr/local/sin.sh /home/administrator
  ```

- ftp

传输数据快，适合大文件传输。通常用来做网盘，[待尝试](https://www.cnblogs.com/hexige/p/7809481.html)

## 3.通过.deb 文件安装软件

`sudo apt install /path/to/package/name.deb`

## 4. Ubuntu16.04 的默认截图方式

> - 对整个屏幕截图： PrintScreen
> - 对活动窗口截图： Alt + PrintScreen
> - 对任意矩形截图： Shift + PrintScreen
> - 以上三个快捷键再加上 Ctrl，就会默认复制截图到粘贴板

## 5. 要尽量使用 apt, 而不是 apt-get

## 6. Linux 开机自启动脚本

- ubuntu 14.04

  将命令写入/etc/rc.local 文件中，[参考](https://blog.csdn.net/qq_24233615/article/details/81084222)

- ubuntu 16.04+, centos 7+

  使用 systemctl service 方式，非常方便！将服务文件(\*\*.service)放入/etc/systemd/system/目录下。运行

  ```bash
  sudo systemctl daemon-reload  # 刷新配置
  sudo systemctl enable **.service  # 设置开机自启。注意：.service文件必须[Install]区域(状态是static)，否则无法enable. service文件的模板见下文
  sudo systemctl start **.service  # 启动该服务
  ```

# 其他有用的 systemctl 命令

sudo systemctl stop **.service # 停止该服务
systemctl status **.service # 查询该服务的状态
systemctl is-enabled \*\*.service # 查询该服务是否已经设置为开机自启，返回 enable 说明已经设置了

````

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
````

## 7. 命令行后台运行程序 -- nohup &

用法：

```
nohup [YOUR_COMMAND] &
```

NOTE: nohup 命令执行后，不要直接关闭终端，使用 exit 命令退出会话.否则会出现 nohup 不起作用的情况！

[如何终止正在后台运行的程序](https://blog.csdn.net/qq_29663071/article/details/81030396)
