# Ubuntu 20.04 安装配置Docker

## 1.先删除原来的docker 文件

​		完整删除原来文件

```
sudo apt-get purge docker-ce docker-ce-cli containerd.io
sudo rm -rf /var/lib/docker
```

​		常规的方法：

> 	1.常归删除操作

> sudo apt-get autoremove docker docker-ce docker-engine docker.io containerd runc
>
> 2. 删除docker其他没有没有卸载
> dpkg -l | grep docker
> dpkg -l |grep ^rc|awk ‘{print $2}’ |sudo xargs dpkg -P # 删除无用的相关的配置文件
>
> 3.卸载没有删除的docker相关插件(结合自己电脑的实际情况)
> sudo apt-get autoremove docker-ce-*
>
> 4.删除docker的相关配置&目录
> sudo rm -rf /etc/systemd/system/docker.service.d
> sudo rm -rf /var/lib/docker
>
> 5.确定docker卸载完毕
> docker --version
> 	

​	卸载原来的docker：

```
$ sudo apt-get remove docker docker-engine docker.io containerd runc
```

### 2.安装所需要的包

```
sudo apt-get install apt-transport-https ca-certificates software-properties-common curl
```

### 3.添加gpg密钥，并且添加docker-ce软件源

​		中科大的软件源：

`curl -fsSL https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu/gpg | sudo apt-key add -` 



```text
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable" 
```


然后开始安装：

```text
sudo apt install docker-ce
```

测试是否成功：

```text
sudo systemctl status docker 
```

### 4.添加当前用户到docker组

将当前用户添加到docker用户组：

```
sudo gpasswd -a ${USER} docker
```

重新登陆或者输入：

```
sudo service docker restart
```

重启docker服务：

```
sudo service docker restart
```

