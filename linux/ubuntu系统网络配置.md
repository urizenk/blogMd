# 1.ubuntu系统20.04设置静态ip

​	1.虚拟机设置为桥接模式，重启Ubuntu系统

​	2.确认要修改的网卡号 `ip addr` 我的配置：

> 1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
>     link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
>     inet 127.0.0.1/8 scope host lo
>        valid_lft forever preferred_lft forever
>     inet6 ::1/128 scope host 
>        valid_lft forever preferred_lft forever
> 2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
>     link/ether 08:00:27:8b:e0:20 brd ff:ff:ff:ff:ff:ff
>     inet 192.168.101.2/24 brd 192.168.101.255 scope global dynamic noprefixroute enp0s3
>        valid_lft 172699sec preferred_lft 172699sec
>     inet6 fe80::bd63:ac60:87e0:4d25/64 scope link noprefixroute 
>        valid_lft forever preferred_lft forever

​	3.编辑配置文件 `sudo vim /etc/netplan/01-network-manager-all.yaml`如下

> network:
>     version: 2
>     renderer: NetworkManager
>     ethernets:
>       enp0s3:
>          addresses: [192.168.0.69/24]
>          dhcp4: no
>          dhcp6: no
>          gateway4: 192.168.0.1
>          nameservers:
>            addresses: [192.168.0.1]

wq保存退出

​	4.启动 `sudo netplan --debug apply`

成功，可以ping通

##### 5.更改hosts文件以及hostname文件来做映射

```
sudo vim /etc/hosts
```

在Windows系统写入：

```
39.106.64.223  gateway
39.106.141.56  dev
39.106.72.211  test
47.94.150.76   prod
```

在Linux写入如下的信息：

```
172.26.7.125  gateway
172.26.7.123  dev
172.26.7.122  test
172.26.7.124  prod
```

更改hostname



```
192.168.0.70  master1
192.168.0.71  master2
192.168.0.72  master3
192.168.0.73  node1
192.168.0.74  node2
192.168.0.75  node3
192.168.0.76  node4
192.168.0.77  node5
192.168.0.78  source
192.168.0.79  gateway
192.168.0.80  storage
192.168.0.81  test
192.168.0.82  cdn
192.168.0.83  back1
192.168.0.84  back2
192.168.0.85  back3
```

```
sudo vim /etc/hostname
```

写入：

dev

重启后服务生效

