### 1.准备好需要的机器

复制出4台虚拟机，并配置好每个机器的IP与主机名字

### 2.配置ssh免密登录

安装ssh服务器：

```
sudo apt-get install openssh-server
```

配置root用户的密码：

`sudo passwd root`

切换为root用户，并且生成密钥：

```
ssh-keygen -t rsa
```

本机来分发：

```
cat ~/.ssh/id_rsa.pub | ssh root@master1 "mkdir -p ~/.ssh && chmod 700 ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys"
```

给其他的机器分发密钥：

```
ssh-copy-id -i ~/.ssh/id_rsa.pub root@192.168.0.101
```

开启root登录：

```
vim /etc/ssh/sshd_config
```

修改**PermitRootLogin**值为**yes**

```
PermitRootLogin yes
```

为每个节点配置python的软连接：

```
ssh test ln -s /usr/bin/python3 /usr/bin/python
```

### 3.安装k8s集群

安装kubeasz一键安装k8s脚本：

```
export release=3.1.1
wget https://github.com/easzlab/kubeasz/releases/download/${release}/ezdown
chmod +x ./ezdown
```

容器化运行kubeasz脚本：

```
./ezdown -S
```

配置命令的别名：

```
echo "alias dk='docker exec -it kubeasz'" >> /root/.bashrc
source /root/.bashrc
```

创建集群：

```
dk ezctl new k8s-pr
```

配置整个集群的一些特点：

```
vim /etc/kubeasz/clusters/k8s-test/hosts
```

配置一些节点组件的信息：

```
vim /etc/kubeasz/clusters/k8s-test/config.yml
```

创建k8s集群：

```
dk ezctl setup k8s-pr all
```

### 4.配置共享存储nfs

在一台主机上配置nfs服务器：

```
sudo apt install nfs-kernel-server
```

创建整个nfs系统需要的目录：

```
sudo mkdir -p /srv/nfs4/backups
sudo mkdir -p /srv/nfs4/data
```

创建nfs的文件共享目录：

```
sudo mkdir -p /data/nfs
sudo mkdir -p /data/nfs/backup
```

将目录挂载到共享的安装点：

```
sudo mount --bind /data/nfs/backup /srv/nfs4/backups
sudo mount --bind /data/nfs /srv/nfs4/data
```

配置文件并写入使其永久绑定，不会因开机而失效：

```
sudo vim /etc/fstab

/data/nfs/backup /srv/nfs4/backups  none   bind   0   0
/data/nfs     /srv/nfs4/data     none   bind   0   0
```

分配最高的权限：

```
sudo chmod -R 777 /data/nfs
sudo chmod -R 777 /data/nfs/backup
```

打开配置文件写入配置：

```
sudo vim /etc/exports

/srv/nfs4         192.168.0.0/24(rw,sync,no_subtree_check,crossmnt,fsid=0)
/srv/nfs4/backups 192.168.0.0/24(ro,sync,no_subtree_check) 192.168.0.101(rw,sync,no_subtree_check)
/srv/nfs4/data     192.168.0.101(rw,sync,no_subtree_check)
```

将配置文件生效：

```
sudo exportfs -ar
```

安装nfs的客户端：

```
sudo apt install nfs-common
```

在客户端上挂载：

```
sudo mkdir -p /data/nfs

mount 192.168.0.102:/srv/nfs4/data /data/nfs

```

### 5.创建基于nfs的storageClass

在主机上创建k8s的配置文件夹：

```
sudo mkdir -p /data/kubens/nfs
```



写入三个文件并执行：

```
rbac.yml

apiVersion: v1
kind: ServiceAccount
metadata:
  name: nfs-client-provisioner
  # replace with namespace where provisioner is deployed
  namespace: nfs        #根据实际环境设定namespace,下面类同
---
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: nfs-client-provisioner-runner
rules:
  - apiGroups: [""]
    resources: ["persistentvolumes"]
    verbs: ["get", "list", "watch", "create", "delete"]
  - apiGroups: [""]
    resources: ["persistentvolumeclaims"]
    verbs: ["get", "list", "watch", "update"]
  - apiGroups: ["storage.k8s.io"]
    resources: ["storageclasses"]
    verbs: ["get", "list", "watch"]
  - apiGroups: [""]
    resources: ["events"]
    verbs: ["create", "update", "patch"]
---
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: run-nfs-client-provisioner
subjects:
  - kind: ServiceAccount
    name: nfs-client-provisioner
    # replace with namespace where provisioner is deployed
    namespace: nfs
roleRef:
  kind: ClusterRole
  name: nfs-client-provisioner-runner
  apiGroup: rbac.authorization.k8s.io
---
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: leader-locking-nfs-client-provisioner
  # replace with namespace where provisioner is deployed
  namespace: nfs
rules:
  - apiGroups: [""]
    resources: ["endpoints"]
    verbs: ["get", "list", "watch", "create", "update", "patch"]
---
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: leader-locking-nfs-client-provisioner
subjects:
  - kind: ServiceAccount
    name: nfs-client-provisioner
    # replace with namespace where provisioner is deployed
    namespace: nfs
roleRef:
  kind: Role
  name: leader-locking-nfs-client-provisioner
  apiGroup: rbac.authorization.k8s.io

storageClass.yml

apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: managed-nfs-storage
provisioner: nfs-storage #这里的名称要和provisioner配置文件中的环境变量PROVISIONER_NAME保持一致
parameters:
  #  archiveOnDelete: "false"
  archiveOnDelete: "true"
reclaimPolicy: Retain


provisioner.yml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nfs-client-provisioner
  labels:
    app: nfs-client-provisioner
  # replace with namespace where provisioner is deployed
  namespace: nfs  #与RBAC文件中的namespace保持一致
spec:
  replicas: 1
  strategy:
    type: Recreate
  selector:
    matchLabels:
      app: nfs-client-provisioner
  template:
    metadata:
      labels:
        app: nfs-client-provisioner
    spec:
      serviceAccountName: nfs-client-provisioner
      containers:
        - name: nfs-client-provisioner
          #image: quay.io/external_storage/nfs-client-provisioner:latest
          #这里特别注意，在k8s-1.20以后版本中使用上面提供的包，并不好用，这里我折腾了好久，才解决，后来在官方的github上，别人提的问题中建议使用下面这个包才解决的，我这里是下载后，传到我自已的仓库里
          #easzlab/nfs-subdir-external-provisioner:v4.0.1
          image: registry-op.test.cn/nfs-subdir-external-provisioner:v4.0.1
          volumeMounts:
            - name: nfs-client-root
              mountPath: /persistentvolumes
          env:
            - name: PROVISIONER_NAME
              value: nfs-storage  #provisioner名称,请确保该名称与 nfs-StorageClass.yaml文件中的provisioner名称保持一致
            - name: NFS_SERVER
              value: 192.168.0.102   #NFS Server IP地址
            - name: NFS_PATH
              value: "/data/nfs"    #NFS挂载卷
      volumes:
        - name: nfs-client-root
          nfs:
            server: 192.168.0.102  #NFS Server IP地址
            path: "/data/nfs"     #NFS 挂载卷
      imagePullSecrets:
        - name: registry-op.test.cn
```

执行：

```
kubectl apply -f xxx.yml
```

### 6. 安装kubesphere

安装curl：

```
apt install curl
```

首先安装helm：

```
curl https://baltocdn.com/helm/signing.asc | sudo apt-key add -
sudo apt-get install apt-transport-https --yes
echo "deb https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm
```

然后配置出helm的自动补全：

```
echo "source <(helm completion bash)" >>  ~/.bash_profile
```

在kubesphere官网安装：

```
kubectl apply -f https://github.com/kubesphere/ks-installer/releases/download/v3.3.0/kubesphere-installer.yaml
   
kubectl apply -f https://github.com/kubesphere/ks-installer/releases/download/v3.3.0/cluster-configuration.yaml

```

查看日志：

```
kubectl logs -n kubesphere-system $(kubectl get pod -n kubesphere-system -l 'app in (ks-install, ks-installer)' -o jsonpath='{.items[0].metadata.name}') -f

```

