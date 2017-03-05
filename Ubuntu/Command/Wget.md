## Wget 

wget是一个命令行工具——是历史上最快的单线程传输工具，用于下载网站/批量文件，支持HTTP和FTP。 它的任务就是获取互联网。  
首先，基本上所有的面向个人计算机的Linux都带 wget。  
如果我们想下载ftp里面某个目录里面的所有文件，
```
wget -r ftp://10.8.8.8/movie/
```
呵呵，等吧！下完了，发觉有些不对劲，怎么出来个10.8.8.8的目录，进去看看，又是一个movie，哦，wget将目录结构和网站标题都给记录下来了，不要？？没有问题！比如说还是这个例子
```
wget -r -nd ftp://10.8.8.8/movie/
```
结果什么目录都没有了，faint！怎么会这样？呵呵，你如果想要这样就让它这样吧，否则使用
```
wget -r -nH ftp://10.8.8.8/movie/
```
恩？movie也不要？OK，那就这样
```
wget -r -nH --cut-dirs=1 ftp://10.8.8.8/movie/
```
断线了？超时了？ 加个 -c 继续
```
wget -c -r -nH --cut-dirs=1 ftp://10.8.8.8/movie/
```
我们下载这里面所有的宏包，呵呵
```
wget -r -k http://www.ctan.org/tex-archive/macros/latex/
```
-k表示将连接转换为本地连接。但是现在同样有上面的问题啊，那就把你需要的加上吧，另外也许你根本不需要向下走那么多层，比如，我们就要到第二层，那么
```
wget -r -l2 -k http://www.ctan.org/tex-archive/macros/latex/
```
现在新的问题是，由于网页有一个排序功能，很讨厌，因为下载的时候把网页重复了好多次，那么我们可使用-A和-R开关控制下载类型，并且可以使用通配符，呵呵，现在随心所欲了吧
```
wget -r -R ".htm\?*" -k http://www.ctan.org/tex-archive/macros/latex/
```
这次没有那种网页了吧？-R的意义在于拒绝下载匹配类型的文件，-A表示仅仅接受的文件类型，如-A "*.gif"将仅下载gif图片，如果有多个允许或者不允许，可以使用,分开。
那么，我们现在在使用代理服务器，怎么办呢？呵呵，很高兴你选择了wget，你可以使用它的配置文件，环境变量来利用代理。这里推荐使用环境变量，如在 bash里面我们可以把天天用的proxy加到.bash_profile里面，这是Linux标准写法（很多软件都用的，什么apt-get，yum等等）
```
export http_proxy=http://10.20.30.40:8080
```
然后，proxy就默认打开了，如果需要暂时关闭，可以使用
```
wget --proxy=off -r -k http://www.ctan.org/tex-archive/macros/latex/
```
当然，写一个.wgetrc文件也可以，该文件可以从/usr/local/etc里面找到，里面有很详细的注释，我就不多说了。  
下载网页的时候比较麻烦的事情是，有的网页被同时指向了很多遍，那么为了避免多次下载，我们使用
```
wget -nc -r -k http://www.ctan.org/tex-archive/macros/latex/
```
可以避免这件事情。为了不被有的连接指向非http://www.ctan.org/tex-archive/macros/latex/ 内层目录，我们还应该加上
```
wget -nc -np -r -k http://www.ctan.org/tex-archive/macros/latex/
```
避免下载非该目录里面的文件，这也避免了到不同的host上面去。当然，如果你希望有这个功能，在多个host之间跳来跳去的下载，可以使用
```
wget -nc -np -H -r -k http://www.ctan.org/tex-archive/macros/latex/
```
使得可以在多个host之间span，同时-I和-X可以使得我们仅仅跟踪某些目录或者不跟踪某些目录。如果某些HTML里面你需要的东西不是由这种东西作出来的，你就得使用--follow-tags和--ignore-tags了。  
嘿，我有个文件里面都是连接，怎么办？要是不是html你只需要
```
wget -i your.file
```
如果是，那也不繁
```
wget -F -i your.file
```

下面是补充：
```
wget -m ftp://username:password@IPAddress/*
```
-m  做站点镜像时的选项，如果你想做一个站点的镜像，使用这个选项，它将自动设定其他合适的选项以便于站点镜像；等价于 -r -N -l inf -nr.

转载自： http://wiki.ubuntu.org.cn/Wget
