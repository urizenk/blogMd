

### 1.准备好jdk的安装包。并在opt目录建立安装位置

```
sudo mkdir /opt/jdk/
```

### 2.将java安装包解压,并添加软连接

```
sudo tar zxvf /media/sf_share/pkg/jdk-8u181-linux-x64.tar.gz -C /opt/jdk/

sudo update-alternatives --install /usr/bin/java java /opt/jdk/jdk1.8.0_181/bin/java 100

sudo update-alternatives --install /usr/bin/javac javac /opt/jdk/jdk1.8.0_181/bin/javac 100
```



### 3.编辑环境变量，并使其生效

```
sudo vim /etc/profile
```

写入如下信息：

```
export JAVA_HOME=/opt/jdk/jdk1.8.0_181
export JRE_HOME=/opt/jdk/jdk1.8.0_181/jre
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
```

输入：

```
source /etc/profile
```



