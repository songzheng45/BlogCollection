# 安装 Jexus

## 下载最新 Jexus 安装包 
我下载的最新版是 5.8.2 ，[论坛](https://www.linuxdot.net/) 里“重要下载”版块的"Jexus V5.8.2 64位 Linux 专用版"，压缩包名为"jexus-5.8.2-x64.tar.gz"。

## 上传到 Ubuntu 并解压
上传到 /usr 目录下，并解压缩，解压缩后的文件夹是 jexus。

可以使用 FTP 或 XShell ZMODEM 上传。

## 启动 Jexus
独立版Jexus不需要安装，解压后启动即可。
```
/usr/jexus# sudo ./jws start
starting... OK
```

## 参照Jexus readme文件，创建默认网站
在 /var/www/default 目录下创建index.html，访问站点即可看到页面。或者访问/info查看Jexus欢迎页面，说明启动成功。

## 查看 Jexus 运行状态
```
ubuntu@ip-172-31-42-174:/usr/jexus$ sudo ./jws status
Jexus is running.
```

## Jexus的常用命令

启动：sudo ./jws start

重启：sudo ./jws restart

停止：sudo ./jws stop

启动某个网站： sudo start 网站名

重启某个网站：sudo restart 网站名

停止某个网站：sudo stop 网站名

参考： [无需安装Mono就能运行ASP.NET的Jexus“独立版”][1]

[1]:https://www.linuxdot.net/bbsfile-4330