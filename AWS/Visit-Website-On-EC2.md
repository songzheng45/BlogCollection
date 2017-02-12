# 访问 AWS EC2 实例上部署的站点

由于我是 .NET 开发者，这里使用 Jexus 作为 WebServer，后续可以部署 ASP.NET 项目。

## 1. 安装并启动 Jexus
参考 [如何安装Jexus](../HowToInstallJexus/index.html)

## 2. 配置安全组

Step01：打开EC2控制面板  
![EC2控制面板](http://ojm289en8.bkt.clouddn.com/image/20170212/AWS-EC2-Security.png)  

Step02：点击下方“描述”标签中的“安全组”链接，如这里是“MyBlog-WebServerSecurityGroup-xxxx”,进入安全组页面，如下图：  
![安全组](http://ojm289en8.bkt.clouddn.com/image/20170212/AWS-EC2-SecurityGroup-Inbound.png)  

Step03：选中下方的“入站”标签，点击“编辑”，弹出“编辑入站规则”：  
![编辑入站规则](http://ojm289en8.bkt.clouddn.com/image/20170212/AWS-EC2-SecurityGroup-Edit-Rules-Inbound-default.png)  

Step04：点击“添加规则”，添加一条类型为“HTTP”的规则，点击“保存”：  
![添加入站规则](http://ojm289en8.bkt.clouddn.com/image/20170212/AWS-EC2-SecurityGroup-Add-Rules-Inbound.png)  

## 3. 访问站点
在 EC2 实例的“描述”中，可以找到 Public DNS (IPv4)，值类似：ec2-xx-xx-xx-xx.us-west-2.compute.amazonaws.com。  
如果 WebServer 配置正确，访问 Public DNS 就可以看到 Jexus 的默认欢迎页了。

