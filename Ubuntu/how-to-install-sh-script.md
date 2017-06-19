## 如何安装`sh`脚本

以下三种方式：
- `./file.sh`
- `sh file.sh`
- `bash file.sh`  

如果权限不足，需使用 `sudo` 运行命令。

如果提示 `command not found`:  
```bash
ubuntu@ip:/tmp$ sudo ./install.sh
sudo: ./install.sh: command not found
```
则需修改文件权限： `chmod +x file.sh`

