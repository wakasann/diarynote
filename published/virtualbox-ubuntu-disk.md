virtualbox ubuntu 磁盘大小扩容

## 前言

虚拟机软件: virtuabox

虚拟机运行的系统: Ubuntu 18.04.3 desktop

如果需要扩容的虚拟机在运行中，请将需要扩容的虚拟机进行关机

## 方法

### 先设置虚拟机的虚拟硬盘的大小

1. 点击 virtualbox 软件的菜单，`管理` -> 选择`虚拟介质管理`(快捷键1: `ALT + F`,`ALT + V` 或者直接 `CTRL + D` )
2. 在`虚拟硬盘`选项卡中，找到需要扩大容量的虚拟机名称+`.vdi`,如:`ubuntu.vdi` ,然后 选中这个`vdi`，右键，选择`属性`
3. 然后弹出的`属性(A)`，在`大小(s)`上拖动 滚动条，将设置虚拟机的虚拟硬盘的大小,设定好之后，点击`应用`按钮保存当前的修改,然后点击`关闭`按钮来关闭`虚拟介质管理器`的窗口

### 修改虚拟机中的Ubuntu的磁盘大小

1. 在virtualbox 启动已扩大容量的虚拟机系统，找到`Disks`(`磁盘`)，如果没有，可通过`sudo apt-get install gnome-disk-utility` 命令来安装这个软件,启动`Disks`软件之后
2. 选择`Hard Disk`(`磁盘`)，点击在`Volumes`(`卷(V)`)下面的按钮组中带有`齿轮`图标的按钮，在弹出的菜单中，选择 `Resize...`(`调整大小...`)
    ![images/191009/fs2019100900001U.jpg](https://i.loli.net/2019/10/29/43MwfTxtI8Q5WbC.jpg)

3. 在弹出的`Resize Volume`(`调整卷大小`)的窗口，拖动 滚动条来进行调整大小，确认 调整之后，最后点击红色的`Resize`(`调整大小`)的按钮之后，虚拟机中磁盘的大小就 已经调整好了 :smile:

------

```
wa@wa-VirtualBox:~$ sudo apt search gnome-disk
正在排序... 完成
全文搜索... 完成  
gnome-disk-utility/bionic-updates 3.28.3-0ubuntu1~18.04.1 amd64
  管理和创建磁盘驱动器和媒体
```

[Disks](https://wiki.gnome.org/Apps/Disks)

------

## References
1. [VirtualBox：虚拟机硬盘vdi文件扩容增加大小](https://jingyan.baidu.com/article/6d704a136adcaf28db51ca8d.html)