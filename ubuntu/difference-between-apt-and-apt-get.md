# apt 与 apt-get 的区别

两者很相似，`apt-get`和`apt-cache`的大部分查用命令在`apt`命令中都可以用。

`apt`命令对最终用户而言更乐于使用，而且不需要像 `apt-get` 一样向后兼容。

apt 提供了 apt-get 及 apt-cache 大部分功能，但更方便使用。

常用命令和`apt-get`的比较：

- list 根据名称列出软件包 等价于 `dpkg list`，并且可以配合`--installed`、`--upgradable`使用
- search 搜索软件包描述，类似`apt-cache search`，但是按字母顺序排序
- show 显示软件包细节，类似`apt-cache show`，但是隐藏了一些人们不需要关心的细节（像hash）
- install,remove 安装（移除）软件包，类似`apt-get install`和`apt-get remove`，并且增加进度输出
- update 更新可用软件包列表，类似`apt-get update`，并且启用彩色输出
- upgrade 通过 安装/升级 软件来更新系统，类似`apt-get upgrade`
- full-upgrade 通过 卸载/安装/升级来更新系统，等价于`apt-get dist-upgrade`
- edit-sources 使用  $EDITOR 编辑软件源信息文件`sources.list`，等价于 `vim /etc/apt/sources.list`

参考：
[What is the difference between apt and apt-get?][1]




[1]:https://askubuntu.com/questions/445384/what-is-the-difference-between-apt-and-apt-get