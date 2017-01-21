# 如何安装 Jexus 

Jexus web server for linux 是一款基于mono环境，运行于Linux/unix操作系统之上，以支持ASP.NET为核心功能的高性能WEB服务器。

独立版Jexus已经继承了mono运行时，不再需要单独安装mono，这里记录下安装独立版Jexus的过程。

## 1. 下载最新 Jexus 安装包 
我下载的最新版是 5.8.2 ，论坛链接写作"Jexus V5.8.2 64位 Linux 专用版"，压缩包名"jexus-5.8.2-x64.tar.gz"。

## 2. 上传到 Ubuntu 并解压
我这里上传到 /home/pkg 目录下，并解压缩。
可以使用 FTP 或 XShell ZMODEM 上传。
解压缩后的文件夹是 jexus，将它移动到 /usr/ 下。

## 3. 启动 Jexus
独立版Jexus不需要安装，解压后启动即可。
```
/usr/jexus# sudo ./jws start
starting... OK
```

## 4. 参照Jexus readme文件，创建默认网站
在 /var/www/default 目录下创建index.html，访问站点即可看到页面。或者访问/info查看Jexus欢迎页面，说明启动成功。
