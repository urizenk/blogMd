## 1.git的基本组成

### 1.1 仓库

git 分为本地仓库以及远程仓库，每个仓库存储的是本地的历史提交记录，每个仓库可以配置私有的账号

，可以切换出不同的提交记录到工作区，而工作区的文件可以add到暂存区，暂存区可以提交到本地仓库

### 1.2 分支

可以在当前工作区上面签出分支，git底层记录不同的分支信息

### 1.3 提交记录

当你在工作区完成了工作的时候，可以添加到暂存区，当确定无误的时候，可以提交到本地仓库形成一个提交记录，保存一个文件快照，用以标识一个独立的记录

## 2.git的基本命令

### 2.1 git add 

将当前的工作修改添加到暂存区，有如下的参数：

+ 长命令 ：--verbose ，短命令： -v  ，展示详细的添加结果

+ 长命令： --dry-run，短命令： -n ， 并没有实际添加文件，只是显示一下

+ 长命令： --force，短命令： -f ， 能够添加本来被忽略的文件

+ 长命令： --interactive， 短命令： -i 交互模式相关，不需要考虑

+ 长命令：--patch ，短命令：-p 交互模式相关的，不管

+ 长命令： --edit ，短命令： -e  在编辑器模式下修改冲突

+ 长命令： --update，短命令： -u 更新索引并且不会添加文件

### 2.2 git init

初始化一个本地仓库，会生成.git文件夹，结构如下

![image-20230227175907507](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230227175907507.png)

参数如下：

+ 长命令：--quiet，短命令：-q 只打印错误和警告信息

+ 长命令： --bare 创建一个裸仓库，没有设置默认为工作目录

+ 长命令： --object-format=<format> 选定hash格式

+ 长命令：--template=<template_directory> 指定模板生成

+ 长命令：--separate-git-dir=<git dir> 创建一个符号连接连接到一个非初始化的仓库

+ 长命令：--initial-branch=<branch-name>  短命令: -b <branch-name> 指定初始仓库时的默认分支名称

+ 长命令：--shared[=(false|true|umask|group|all|world|everybody|0xxx)] 指定共享的范围

### 2.3 git clone

克隆远程的仓库到本地，参数如下：

+ 长命令： --local ，短命令： -l 克隆本地的仓库，通过复制HEAD，对象还有refs目录下的内容

+ 长命令： --no-checkout ,短命令： -n 克隆完成后不检出HEAD
+ 长命令： --bare 制作一个裸的git仓库，同时生效--no-checkout，远程的分支头会直接复制到本地，而不是做关联映射到refs/remotes/origin/
+ 长命令：--mirror 设置原仓库的镜像，等同于--bare
+ 长命令： --origin <name> 短命令： -o<name> 更改克隆过来的远程仓库的名字
+ 长命令： --branch <name> 短命令：-b<name>  新建的HEAD指向自己定义的分支名，还可以获取标签并分离HEAD
+ 长命令： --no-tags 不克隆任何的标签
+ 参数： <repository>  你要克隆的远程仓库
+ 参数： <directory> 要克隆的本地目录的地址

### 2.4

![image-20230227202330888](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230227202330888.png)

不写了，到时候自己查
