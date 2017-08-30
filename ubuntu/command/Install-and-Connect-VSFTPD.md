# 安装和配置 VSFTPD

OS : Ubuntu 16.04

## 关于 vsFTPd
vsFTPd 代表 **V**ery **S**ecure **F**ile **T**ransfer **P**rotocol **D**aemon （非常安全的文件传输协议守护进程）。它是一个用于 Unix 和 Linux 操作系统的开源、轻量、稳定、最安全和最快的 FTP 服务器。
一些流行的FTP站点的Debian，FreeBSD，RED HAT，SUSE，Kernel，KDE，GENOME等，就是基于VSFTPD。
它是大多数Linux和Unix操作系统（例如Red Hat，CentOS，Fedora和Ubuntu）的默认FTP服务器。
它支持IPv6和SSL。因此，您可以使用VSFTPD为您的组织设置安全的FTP服务器。它是根据GPL许可的，完全免费。


## 1.更新软件仓库
```
root@mail:/# apt-get update
root@mail:/# apt-get upgrade
```

## 2.安装vsFTPd
```
root@mail:/# apt-get install vsftpd
```
备份下配置文件
```
root@mail:/# cp /etc/vsftpd.conf /etc/vsftpd.conf.backup
```

## 3.编辑配置文件
使用 nano 打开 vsftpd.conf
```
root@mail:/# nano /etc/vsftpd.conf
```
使用```CTRL+W```搜索并修改以下配置项：  
```
anonymous_enable=NO
local_enable=YES
write_enable=YES
allow_writeable_chroot=YES
chroot_local_user=YES
file_open_mode=0777
local_umask=022
```
如果存在则取消注释，否则添加行。  

## 4.配置只允许特定用户访问
```
userlist_deny=NO
userlist_enable=YES
userlist_file=/etc/vsftpd.allowed_users
```

## 5.创建文件 vsftpd.allowed_users
```
sudo touch /etc/vsftpd.allowed_users
```
该文件包含了可以访问的用户名：
```
m.slavov
yourSECONDuser
yourTHIRDuser
```

如果你想的话还可以做一些高级配置：
```
#Useful to not write over hidden files:
force_dot_files=YES

#Hide the info about the owner (user and group) of the files.
hide_ids=YES

#Maximum number of clients:
max_clients=20

#Connection limit for each IP:
max_per_ip=2

#Chnage listen port from 21 to other
listen_port=999
```

## 6.添加ftp用户并且修改它的家目录
这点很重要，因为你能够限制这个用户只能访问一个目录。你可以允许它访问`/var/www`下的所有站点或只能访问其中某一个站点。  
例如，如果你想创建用户`ftp`并只允许它访问一个站点，输入以下命令，并且不要忘了在`/etc/vsftpd.allowed_users`中添加一个用户：
```
root@mail:/# adduser ftp
root@mail:/# usermod –home /var/www/YOURFOLDERSITE ftp
```
或者打开文件`/etc/passwd`，然后搜索你想改变家目录的用户，把`/home/ftpuser`修改为`/var/www`：
```
root@mail:/# nano /etc/passwd
root@mail:/# ftpuser:x:1001:1001:FTP USER,,,:/var/www:/bin/bash
```

## 7.重启vsFTPd
为了使修改生效，你必须重启vsFTPd服务。
```
root@mail:/# systemctl restart vsftpd
```
现在当用户通过 ftpzilla 连接 ftp，只能看到一个站点。   
如果你想看到命令的输出，用命令`/etc/init.d/vsftpd restart`。  
显示vsFTPd状态，用命令：`systemctl status vsftpd`  

## 8.配置权限
如果你想所有用户都有权限写入到这个文件夹，你必须设置全部权限777。  
如果你只想某个组有权限写入、其他用户只能读，你就得设置权限为775.  
```
root@mail:/# chmod 777 -R /var/www/YOURFOLDERSITE
root@mail:/# chown ftp:ftp -R /var/www/YOURFOLDERSITE
```

## 9.使用 Xftp 连接
新建连接，输入 ip 地址和刚才新建的 ftp 用户以及密码。  

这里我遇到一个问题：
>在命令行用`ftp`命令可以连接到 ftp 服务器，但是用 Xftp 连接却总是提示“无法显示远程文件夹”。

解决：
>在“会话属性”的“选项”窗口，勾掉“使用被动模式”，重试。



----------
参考：  
http://linux-sys-adm.com/ubuntu-16.04-lts-how-to-install-and-configure-vsftpd-on-ubuntu/  
https://www.ostechnix.com/install-vsftpd-server-ubuntu-16-04-lts/  
