Eclipse导入的Maven项目没有Build Path

我导入的是 [Signal-Server](https://github.com/signalapp/Signal-Server)项目到 Eclipse中，发现 src 文件夹上面没有`#`号，包视图和语法提示都没有 ~~

解决方法:

1. 修改 Project Facets

在 项目右键 -> Properties  -> Project Facets  ->Convert to faceted from..--->勾选Java

![images/191012/fs20191012000017.jpg](https://i.loli.net/2019/10/29/FEIYvaDLczhyq3e.jpg)


2. 在项目中新建或者编辑`.classpath`文件

```
<?xml version="1.0" encoding="UTF-8"?>
<classpath>
    <classpathentry kind="src" output="target/classes" path="src/main/java"/>
    <classpathentry kind="src" output="target/classes" path="src/main/resources"/>
    <classpathentry kind="src" output="target/test-classes" path="src/test/java"/>
    <classpathentry kind="src" output="target/test-classes" path="src/test/resources"/>
    <classpathentry kind="con" path="org.eclipse.jdt.launching.JRE_CONTAINER">
        <attributes>
            <attribute name="owner.project.facets" value="java"/>
        </attributes>
    </classpathentry>
    <classpathentry kind="con" path="org.eclipse.jst.j2ee.internal.web.container"/>
    <classpathentry kind="con" path="org.eclipse.jst.j2ee.internal.module.container"/>
    <classpathentry kind="output" path="target/classes"/>
</classpath>
```
3. 再次进入 Eclipse ,项目右键之后的弹出菜单上面的 Build Path 不是灰色的了



-------

## Eclipse Package 视图


1. 显示包视图(Package Explorer),可通过 Eclipse 头部的菜单 

`Window` -> `Show View` -> `Other` -> `Java` -> `package Explorer`

通过菜单，学习到快捷键是 `Alt + Shift + Q`,再按 `P`键，就会显示 `package Explorer` 了

2. 调整项目 包(package) 显示的方式

在 `Project Explorer` 或者 `Package Explorer` 右边有个小箭头的图标，可以调整 项目中 包 显示的方式，操作如下图:

![images/191012/fs2019101200002V.jpg](https://i.loli.net/2019/10/29/Ni2VmHJrE8aecBt.jpg)

注: 好像java项目右键之后弹出的菜单`build path`不是灰色的，切换包的才有效果 :smile:




## References
1. [Eclipse导入Maven项目解决Build Path不能配置问题](https://www.cnblogs.com/ycyzharry/p/9720003.html)
2. [解决eclipse项目src下创建的文件夹(folder)变成了包问题](https://blog.csdn.net/line_to_sea/article/details/44859223) mark,或许以后用的上
3. [eclipse projectExplorer视图（以包的方式显示）与navigator视图切换（以文件夹的方式显示）及树状视图与平面视图的切换](https://www.cnblogs.com/hfx123/p/9835812.html)