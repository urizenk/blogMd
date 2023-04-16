### 1.下载安装vsftpd

```
sudo apt-get install vsftpd
```

备份配置文件：

```
sudo cp /etc/vsftpd.conf /etc/vsftpd.conf.bak
```

生成ssl证书：

```
mkdir -p /etc/vsftpd/.sslkey
cd /etc/vsftpd/.sslkey

openssl req -new -x509 -nodes -days 3650 -out vsftpd.pem -keyout vsftpd.pem
```

写入配置文件：

```
#是否允许使用匿名帐户
anonymous_enable=NO
#是否允许本地用户登录
local_enable=YES
#是否允许写入
write_enable=YES
#创建文件时的权限掩码，若没有文件掩码文件的默认权限为666,文件夹的默认权限为777。比如umask是022,你创建一个文件本来是666 就要 -022 = 644
local_umask=022
#进入每个目录是否显示欢迎信息，可在每个目录下建立.message文件在里面写欢迎信息
dirmessage_enable=YES
#上传/下载文件时记录日志
xferlog_enable=YES
#主动模式(PORT)有效，是否使用20端口传输数据
connect_from_port_20=YES
#主动模式(PORT)有效，设置connect_from_port_20=YES，自定义主动模式下传输数据的端口,默认20
#ftp_data_port=2020
#被动模式(PASV)有效，数据连接可以使用的端口范围的最小端口，0 表示任意端口。默认值为0
#pasv_min_port=6000
#被动模式(PASV)有效，数据连接可以使用的端口范围的最大端口，0 表示任意端口。默认值为0
#pasv_max_port=7000
#是否被动模式，若设置为YES，则使用PASV被动模式；若设置为NO，则使用PORT主动模式。默认值为YES
pasv_enable=NO
#日志文件
xferlog_file=/var/log/xferlog
#使用标准文件日志
xferlog_std_format=YES
#是否允许上传二进制文件
ascii_upload_enable=YES
#是否允许下载二进制文件
ascii_download_enable=YES
 
#是否允许切换到上级目录
chroot_local_user=YES
#设置是否启用下面chroot_list_file配置文件
chroot_list_enable=YES
#在/etc/vsftpd/chroot_list文件中列出的用户，可以切换到根路径的上级路径；未在文件中列出的用户，则不能切换。
chroot_list_file=/etc/vsftpd/chroot_list
#自定义home目录
#local_root=/home/zhangjinhao/
#用户被限定在了其主目录下，需要配置该项
#allow_writeable_chroot=YES
 
#指定监听端口，默认21
listen_port=21
#开启ipv4监听，与listen_ipv6不能同时开启
listen=NO
#同时监听IPv4和IPv6的FTP请求
listen_ipv6=YES
 
#使用pam模块控制，文件位置/etc/pam.d/vsftpd
pam_service_name=vsftpd
#控制用户访问，/etc/vsftpd/user_list是一个黑名单，所有出现在名单中的用户都会被拒绝登入
userlist_enable=YES
userlist_deny=NO
#控制主机访问，检查/etc/hosts.allow 和/etc/hosts.deny 中的设置，来决定请求连接的主机，是否允许访问该FTP服务器。
tcp_wrappers=YES
 
#是否启用SSL，默认NO
ssl_enable=YES
#是否激活sslv2加密，默认NO
ssl_sslv2=YES
#是否激活sslv3加密，默认NO
ssl_sslv3=YES
#是否激活tls v1加密，默认NO
ssl_tlsv1=YES
#非匿名用户登陆时是否加密
force_local_logins_ssl=YES
#非匿名用户传输数据时是否加密
force_local_data_ssl=YES
#ssl证书位置
rsa_cert_file=/etc/vsftpd/.sslkey/vsftpd.pem
 
#是否启用隐式ssl功能，不建议开启
#implicit_ssl=YES
#隐式ftp端口设置，如果不设置，默认还是21，但是当客户端以隐式ssl连接时，默认会使用990端口，导致连接失败
#listen_port=990


##########虚拟用户######一般匿名用户设置前两项即可######################################
anonymous_enable=YES    # 启用匿名访问，匿名用户使用的登陆名为ftp或anonymous，口令为空；匿名用户不能离开匿名用户家目录/var/ftp,且只能下载不能上传。
anon_root=/var/ftp  # 使用匿名登入时，所登入的目录。默认值为/var/ftp。注意ftp目录不能是777的权限属性，即匿名用户的家目录不能有777的权限。可以设置为755。
no_anon_password=YES/NO（NO）  # 若是启动这项功能，则使用匿名登入时，不会询问密码。默认值为NO。
ftp_username=ftp  # 定义匿名登入的使用者名称。默认值为ftp。
anon_umask=022          # 匿名用户上传文件的权限掩码
anon_upload_enable=YES  # 允许匿名上传文件
anon_world_readable_only=YES/NO（YES）# 如果设为YES，则允许匿名登入者下载可阅读的档案（可以下载到本机阅读，不能直接在FTP服务器中打开阅读）。默认值为YES。
anon_mkdir_write_enable=YES  # 允许匿名创建目录
anon_other_write_enable=YES  # 开放其他写入权限，譬如删除或者重命名
anon_max_rate=0   # 限制最大上传速率，0表示不限制
#########本地用户##########################################################################
local_enable=YES  # 允许本地用户登录，本地用户的登录名为本地用户名，口令为此本地用户的口令；本地用户可以在自 己家目录中进行读写操作；本地用户可以离开自家目录切换至有权限访问的其他目录，并在权限允许的情况下进行上传/下载。
local_root=/home/username  # 当本地用户登入时，将被更换到定义的目录下。默认值为各用户的家目录。
chroot_local_user=YES/NO（NO）  # 自己指定登录用户目录时，该项设置为NO。具体不知道为什么。
write_enable=YES/NO（YES）# 是否允许登陆用户有写权限。属于全局设置，默认值为YES。
local_umask=022  # 本地用户新增档案时的umask 值。默认值为077。
file_open_mode=0755  # 本地用户上传档案后的档案权限，与chmod 所使用的数值相同。默认值为0666。
#########欢迎语设置####################################################################
dirmessage_enable=YES/NO（YES）# 如果启动这个选项，那么使用者第一次进入一个目录时，会检查该目录下是否有.message这个档案，如果有，则会出现此档案的内容，通常这个档案会放置欢迎话语，或是对该目录的说明。默认值为开启。
message_file=.message  # 设置目录消息文件，可将要显示的信息写入该文件。默认值为.message。
banner_file=/etc/vsftpd/banner  # 当使用者登入时，会显示此设定所在的档案内容，通常为欢迎话语或是说明。默认值为无。如果欢迎信息较多，则使用该配置项。
ftpd_banner=Welcome to BOB's FTP server # 这里用来定义欢迎话语的字符串，banner_file是档案的形式，而ftpd_banner 则是字符串的形式。预设为无。
###########控制用户是否允许切换到上级目录################################################
chroot_list_enable=YES/NO（NO） # 设置是否启用chroot_list_file配置项指定的用户列表文件。默认值为NO。
chroot_list_file=/etc/vsftpd.chroot_list  # 用于指定用户列表文件，该文件用于控制哪些用户可以切换到用户家目录的上级目录。
chroot_local_user=YES/NO（NO）#  用于指定用户列表文件中的用户是否允许切换到上级目录。默认值为NO。
#############控制用户访问#############################################################
userlist_file=/etc/vsftpd.user_list  # 控制用户访问FTP的文件，里面写着用户名称。一个用户名称一行。
userlist_enable=YES/NO（NO）  # 是否启用vsftpd.user_list文件。
userlist_deny=YES/NO（YES）  # 决定vsftpd.user_list文件中的用户是否能够访问FTP服务器。若设置为YES，则vsftpd.user_list文件中的用户不允许访问FTP，若设置为NO，则只有vsftpd.user_list文件中的用户才能访问FTP。
# /etc/vsftpd/ftpusers文件专门用于定义不允许访问FTP服务器的用户列表

listen=YES  #是否以独立运行的方式监听服务
listen_address=192.168.4.1  #设置监听的 IP 地址
listen_port=21  #设置监听 FTP 服务的端口号
write_enable=YES  #是否启用写入权限（影响整个服务器）
download_enable＝YES  #是否允许下载文件
userlist_enable=YES  #是否启用 user_list 列表文件
userlist_deny=YES  #是否禁用 user_list 中的用户
max_clients=0 #限制并发客户端连接数，就是最多允许多少用户同时登录
max_per_ip=0  #限制同一IP地址的并发连接数，就是一个IP最多同时下载几个文件



```

