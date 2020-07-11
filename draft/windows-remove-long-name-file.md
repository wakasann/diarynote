解决windows文件名过长无法删除的问题

> 1. 在要删除的文件夹(delete_dir)同级新建一个空文件夹(empty_dir);
> 2. 点下空白处且按住Shift键不松, 然后右击鼠标, 选择在此处打开命令窗口;
> 3. 弹出的命令行窗口中执行如下命令, 然后即可删除以前不能删除的文件夹：
>    robocopy empty_dir delete_dir /purge

测试发现, 每次执行命令后只需要删除以前不能删除的文件夹, 空文件夹可以重复利用, 不用每次把空文件夹删掉再创建!

## Referencs
* [解决windows文件名过长无法删除的问题](https://www.cnblogs.com/aios/p/10032740.html)