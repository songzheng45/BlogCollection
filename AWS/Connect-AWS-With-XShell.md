# SSH连接 EC2 Ubuntu 实例

Windows 下使用 XShell 客户端， macOS 下使用自带终端即可连接。

## Windows下使用XShell连接EC2实例

### 1. 创建 EC2 实例
创建EC2实例后可以得到 *.pem 密钥文件，保存到磁盘。

### 2. 新建XShell会话连接
打开 XShell 新建会话窗口，选中“连接”节点，填写如下图设置：  
![](http://ojm289en8.bkt.clouddn.com/image/20170219/xshell-ssh-connection.png)  
其中：  
协议：SSH  
主机：填写EC2实例的 IPv4 公有 IP 或 Public DNS(IPv4)  
端口：22  

### 3. 身份验证
选中“用户身份验证”节点，填写如下图设置：  
![](http://ojm289en8.bkt.clouddn.com/image/20170219/xshell-ssh-username.png)  
其中：  
方法： Public Key  
用户名： ubuntu  
用户密钥： 选择浏览-导入，将刚才下载得到的 *.pem 文件导入进来并选中  
密码： 留空  

注意： 用户名处填写 ubuntu ，而不是网络上说的 ec2-user ,否则连接 AWS 时会弹出如下窗口让输入密码：  
![](http://ojm289en8.bkt.clouddn.com/image/20170219/xshell-ssh-%E8%BA%AB%E4%BB%BD%E9%AA%8C%E8%AF%81.png)  

点击“确定”完成，现在就可以连接 AWS EC2 实例了。  
第一次连接会弹出提示，是“一次保存”还是“永久保存”密钥，根据自己需要选择即可。  

## macOS 下 ssh 连接 EC2
示例：
```
ssh -i "AwsFree.pem" ubuntu@ec2-12.ap-northeast-1.compute.amazonaws.com
```