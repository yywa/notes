# 在CentOS 6上配置VNCServer

- ### 切换到root用户(或者在命令前加sudo)

- ### 通过yum源安装tigervnc

```shell
yum install tigervnc-server;
```

- ### 输入`vncserver`,启动程序。程序会提示你需要输入远程登录的密码，密码自设。

- ### 切换到/root/.vnc目录下，修改xstartup文件，将最后两行进行修改

```
cd /root/.vnc;
```

```
vim xstartup;
```

```
xterm -geometry 80x24+10+10 -ls -title "$VNCDESKTOP Desktop" &
twm &
```

### 	修改之后，再添加一行：gonme-session &

- ## 修改xstartup的模式

```
chmode 777 xstartup;
```

- ### 修改文件/etc/sysconfig/vncservers

#### 	还是修改文件的最后两行。2表示显示器编号，root表示你用windows机器登录时，显示的是root的桌面。后面数组的长度是2 ，表示两个参数，一个是屏幕大小，后面那个是32位颜色，可加可不加。

```
vim /etc/sysconfig/vncservers;
```

```
VNCSERVERS = "2:root"
VNCSERVERARGS[2] = "-geometry 1024x768 -depth 32" 
```

- ### 添加端口

#### 	对于vncserver来说，他监听3个端口，分别是5900，5901和5902.上一步设置的2号显示器，对应的就是5901端口，显示器编号是给vncserver看的。而端口号需要提供给windows机使用，用来远程登录，所以，此处向防火墙增加三个端口号，以便windows的访问能顺利通过。

```
vim /etc/sysconfig/iptables;
```

​	在`-A INPUT -m state --state NEW -m tcp -p tcp --dport 22 -j ACCEPT`之后添加

```
-A INPUT -m state --state NEW -m tcp -p tcp --dport 5900:5903 -j ACCEPT
```

- ### 重新启动防火墙

```
service iptables restart;
```

- ### 将vncserver加入到开机启动的服务中

```
chkconfig vncserver on;
```

- ### 在windows系统中下载一个TightVNC Viewer软件，输入ip地址加上端口号，以及自己设置的登录密码即可远程访问。



