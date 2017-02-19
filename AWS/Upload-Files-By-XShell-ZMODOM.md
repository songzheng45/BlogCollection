# 使用XShell ZMODOM上传文件

在 XShell 中选择使用 ZMODEM 发送文件，如果没有安装rz命令，则有如下提示：
```
ubuntu@ip-172-31-24-110:/usr$ rz -E
The program 'rz' is currently not installed. You can install it by typing:
sudo apt install lrzsz
```

根据提示安装后，再次重试，或者手动输入命令（主要要使用管理员身份 sudo）：

```
sudo rz -E
```
Windows会弹出选择“打开”文件对话框，选择即可。
