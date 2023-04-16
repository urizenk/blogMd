# virtualbox下Ubuntu系统的配置





## 1.虚拟机与主机间的复制粘贴

​	1.在虚拟机设置的常规选项中选择高级，勾	选共享粘贴板与拖放

![在这里插入图片描述](https://img-blog.csdnimg.cn/20181030002107680.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NoZW5neXExMTY=,size_16,color_FFFFFF,t_70#pic_center)

​	2.将虚拟机界面上的设备选项里面的拖放与共享粘贴板选择双向

![在这里插入图片描述](https://img-blog.csdnimg.cn/20181030004659238.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NoZW5neXExMTY=,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20181030004816512.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NoZW5neXExMTY=,size_16,color_FFFFFF,t_70#pic_center)

​	3.安装增强功能

![img](https://www.pianshen.com/images/148/ccd0bed5d77a1f6b0a6c1b60a9b72a94.png)

​	4.关闭虚拟机（**注意不是Ubuntu系统**）后，在存储里面的SATA下勾选使用主机输入\输出缓存

![img](https://www.pianshen.com/images/803/f37b92be39a4e84c6b223ba482dce94b.png)



​	5.再勾选下面vdi文件里面的固态驱动器

![img](https://www.pianshen.com/images/803/f37b92be39a4e84c6b223ba482dce94b.png)

​	6.运行Ubuntu桌面上的增强功能光盘

![Snipaste_2021-07-06_00-56-23](D:\common\snipaste\save\Snipaste_2021-07-06_00-56-23.png)

​	7.重启virtualbox，打开Ubuntu系统就好了









## 2.更换软件源

#### 更换为清华镜像站的软件源

​	1.Ubuntu系统中软件源的地址为`/etc/apt/sources.list`备份原来的源 

```
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
```



​	2.打开`/etc/apt/sources.list`文件，并在其中加入对应的软件源。

```
sudo vim /etc/apt/sources.list
```



加入的文件

```text
#添加阿里源
deb http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
#添加清华源
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse multiverse
```

​	3.更新源 `sudo apt-get update` 如果出现依赖问题就按如下解决

`sudo apt-get -f install`

安装情况是这个样子

![Snipaste_2021-07-06_13-11-07](D:\common\snipaste\save\Snipaste_2021-07-06_13-11-07.png)

​	4.更新软件 `sudo apt-get upgrade`界面是这个样子![Snipaste_2021-07-06_13-13-22](D:\common\snipaste\save\Snipaste_2021-07-06_13-13-22.png)









## 3.添加共享文件夹

​	1.点击设备-共享文件夹-共享文件夹

![VirtualBox开启Ubuntu 18.04的双向共享文件夹，共享粘贴板，拖放](https://www.linuxidc.com/upload/2020_02/20021612196611.png)

​	2.点击图标加上新建

![VirtualBox开启Ubuntu 18.04的双向共享文件夹，共享粘贴板，拖放](https://www.linuxidc.com/upload/2020_02/20021612196426.png)

​	3.设置好共享文件路径，勾选自动挂载与固定分配

![VirtualBox开启Ubuntu 18.04的双向共享文件夹，共享粘贴板，拖放](https://www.linuxidc.com/upload/2020_02/20021612205668.png)

​	4.可能会出现没有权限访问的问题，这时可以

> virtualbox共享文件夹无访问权限问题解决方法,造成这个问题的原因是不跟virtualbox在同一个用户组,所以加入同个组即可解决这个问题
>
> virtualbox的共享文件夹一般都挂载在/media下面，用ll查看会发现文件夹的所有者是root，所有组是vboxsf，所以文件管理去无法访问是正常的，解决方法是把你自己加入到vboxsf组里面。输入`sudo usermod -a -G vboxsf 你的ubuntu的名字`
>
> ————————————————
> 版权声明：本文为CSDN博主「michaelhan3」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
> 原文链接：https://blog.csdn.net/michaelhan3/article/details/52032837

​	然后重启Ubuntu就可以了





## 4.nginx服务器的配置

	#### 在Ubuntu20.04上面安装并配置nginx服务器

​	1.安装nginx服务器：nginx在Ubuntu的默认存储库中，可以用apt更新`sudo apt update`之后安装nginx `sudo apt install nginx` 

![Snipaste_2021-07-06_15-04-01](D:\common\snipaste\save\Snipaste_2021-07-06_15-04-01.png)

成功

​	2.调整防火墙，如果有设置可以来调`sudo ufw app list`  出现结果如下

![Snipaste_2021-07-06_15-06-52](D:\common\snipaste\save\Snipaste_2021-07-06_15-06-52.png)

启用限制性最强的配置文件 `sudo ufw allow 'Nginx HTTP'` 

然后验证更改 `sudo ufw status`

出现了结果

![Snipaste_2021-07-06_15-11-35](D:\common\snipaste\save\Snipaste_2021-07-06_15-11-35.png)

​	3.检查web服务器 ：检查nginx状态  `systemctl status nginx`

结果

![Snipaste_2021-07-06_15-13-25](D:\common\snipaste\save\Snipaste_2021-07-06_15-13-25.png)

待定（https://blog.csdn.net/cukw6666/article/details/107987167）

## 5.docker 权限设置

通过将用户添加到docker用户组可以将sudo去掉，命令如下

sudo groupadd docker #添加docker用户组

sudo gpasswd -a $USER docker #将登陆用户加入到docker用户组中

newgrp docker #更新用户组

ubuntu18.04在重启后会生效，如果不是特别着急，可以先重启然后再做docker操作。

## 6.设置虚拟机开机自启

新建vm.bat

```
@ECHO OFF
cd F:\devtools\virtual Box\virtual box
start VirtualBox.exe -startvm dev --type headless
start VirtualBox.exe -startvm test --type headless
start VirtualBox.exe -startvm prod --type headless
start VirtualBox.exe -startvm code-center --type headless
start VirtualBox.exe -startvm gateway --type headless
EXIT

```








​	



