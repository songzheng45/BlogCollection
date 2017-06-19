## 文件(夹)操作

### ls
列出当前目录下的所有目录和文件
```
ls -l       // 列出目录的详细信息，包括总字节数，目录、文件的权限
```

### cd 
切换目录
```
cd ..           //切换到上一级目录
cd /home/       //切换到指定目录
```

### 创建文件
```
touch filename          // 创建一个名为"filename"的空文件
```

### mkdir 目录名
创建一个目录。 如下：
```
mkdir www           // 在当前目录下创建一个名称为"www"的目录
mkdir /home www     // 在 /home 下创建一个名称为"www"的目录
```

### 删除一个空目录
```
rmdir test          // 删除空目录test
```

### 删除文件
```
rm filename1 filename2      // 删除文件 filename1 和 filename2
rm -rf [目录名或文件名]       // 强力删除指定目录或文件（如果目录非空，也能够删除）
```
可以使用通配符
```
rm *                        // 删除当前目录下所有文件(不包含目录)
rm -rf *                    // 删除当前目录下所有文件及目录
```

### 移动文件
mv 命令是move的缩写，可以用来移动文件或给文件重命名，是Linux系统下常用的命令，常用来备份文件或目录。

**1．命令格式：**
```
mv [options] <source file or directory> <target file or directory>
```    
**2．命令功能：**
- mv命令中第二个参数类型的不同（是目标文件还是目标目录），mv命令将文件重命名或将其移至一个新的目录中。
- 当 target 是文件时，mv命令执行重命名，此时，source 只能有一个（文件或目录），它将把源名称 source 重命名为 target.
- 当 target 是已存在的目录时，source可以有多个，mv命令将 source 移至 target 目录中。
- 在跨文件系统移动文件时，mv先拷贝，再将原有文件删除，而链至该文件的链接也将丢失。

**3．命令参数：**
- -b : 若需覆盖文件，则覆盖前先行备份。 
- -f : force 强制的意思，如果目标文件已经存在，不会询问而直接覆盖；
- -i : 若目标文件 (target) 已经存在时，就会询问是否覆盖！
- -u : 若目标文件已经存在，且 source 比较新，才会更新(update)
- -t : --target-directory=DIRECTORY move all SOURCE arguments into DIRECTORY，
     即指定mv的目标目录，该选项适用于移动多个源文件到一个目录的情况，此时目标目录在前，源文件在后。

### 复制文件
命令格式：
```
cp [options] <source file or directory> <target file or directory>
cp [options] source1 source2 source3 …. directory
```
options 选项包括：
- -a : 保留链接和文件属性，递归拷贝目录，相当于下面的d、p、r三个选项组合。
- -d : 拷贝时保留链接。
- -f : 删除已经存在目标文件而不提示。
- -i : 覆盖目标文件前将给出确认提示，属交互式拷贝。
- -p : 复制源文件内容后，还将把其修改时间和访问权限也复制到新文件中。
- -r : 若源文件是一目录文件，此时cp将递归复制该目录下所有的子目录和文件。当然，目标文件必须为一个目录名。
- -l : 不作拷贝，只是链接文件。
- -s : 复制成符号连结文件 (symbolic link)，亦即『快捷方式』档案；
- -u : 若 target 比 source 旧才更新 target
