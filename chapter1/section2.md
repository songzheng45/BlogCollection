## 文件解压、压缩

首先了解下Linux里面常用的压缩格式。

### ZIP 
zip可能是目前使用得最多的文档压缩格式。最大的优点就是在不同的操作系统平台，比如Linux， Windows以及Mac OS上使用，缺点就是支持的压缩率不是很高，而tar.gz和tar.gz2在压缩率方面做得非常好。
压缩一个目录：
```
# zip -r archive_name.zip directory_to_compress
```
解压一个 ZIP 文档：
```
# unzip archive_name.zip
```

### TAR
Tar是在Linux中使用得非常广泛的文档打包格式。它的好处就是它只消耗非常少的CPU以及时间去打包文件，他仅仅只是一个打包工具，并不负责压缩。
下面是如何打包一个目录：
```
# tar -cvf archive_name.tar directory_to_compress
```
 
如何解包：
```
# tar -xvf archive_name.tar.gz
```
 
上面这个解包命令将会将文档解开在当前目录下面。当然，你也可以用这个命令来捏住解包的路径：
```
# tar -xvf archive_name.tar -C /tmp/extract_here/
```

### TAR.GZ

TAG.GZ 在压缩时不会占用太多CPU，而且可以得到一个非常理想的压缩率（推荐）。
压缩目录：

```
# tar -zcvf archive_name.tar.gz directory_to_compress
```

解压文件：

```
# tar -zxvf archive_name.tar.gz
```

默认解压到当前目录，以下命令可以设置解压到指定的目录：

```
# tar -zxvf archive_name.tar.gz -C /tmp/extract_here/
```
