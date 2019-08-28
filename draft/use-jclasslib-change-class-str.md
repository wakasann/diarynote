---
title: 使用jclasslib 直接修改 class文件
date: 2019-08-19 20:06:28
tags:
  - jclasslib
categories:
  - Java
description:  使用jclasslib 直接修改 class文件
---


软件:

[jclasslib](https://github.com/ingokegel/jclasslib): 5.3.2

[jd-gui](http://java-decompiler.github.io/): 1.4.1



如果 .class 文件 使用 jd-gui 工具保存为java文件之后，再进行修改里面的代码，编译的 java文件如果依赖自己写的类比较多的情况下，可以直接使用 javac 命令去编译 会出现编译不到的情况。



如: 修改`MyTestJar.java` 中的 `testStr` 变量的值 为 `jclasslib Changed`



```
public class MyTestJar{
	
	public static void main(String args[]){
		String testStr = "Hello World";
		System.out.println("output String:"+testStr);
	}
	
}

```



使用 `javac -d . MyTestJar.java` 编辑得到 `MyTestJar.class`文件.

## 使用 jclasslib 工具找到修改的代码在.class文件中的位置

然后使用 jclasslib 工具打开 `MyTestJar.class`文件.

![](https://raw.githubusercontent.com/wakasann/diarynote/master/draft/uploads/190820/20190820144039.png)



然后找到代码的方法，查看里面的 Code

![](https://raw.githubusercontent.com/wakasann/diarynote/master/draft/uploads/190820/20190820144157.png)

1. 是查看 所有方法的`main` 方法，选中点击它，旧可以看到右边的`Bytecode` 窗口
2.  找到了 需要修改的 值`Hello World`了，然后鼠标点击`#2`，会跳到下图

![](https://raw.githubusercontent.com/wakasann/diarynote/master/draft/uploads/190820/20190820144617.png)

3. 下一步使用鼠标点击`cp info #21`, 会跳到如下图

   ![](https://raw.githubusercontent.com/wakasann/diarynote/master/draft/uploads/190820/20190820144809.png)

然后记录 这个的索引是`21`,在修改这个值的时候会使用到

## 使用Eclipse ide创建java项目来进行修改

使用Eclipse 创建一个项目(Project)

第一步，引入 `jclasslib` 安装路径`lib` 文件夹下的所有 jar包

在左边的项目名称上鼠标右键，依次选择 `Build Path` -> `Add External Archives...`,然后选中 `jclasslib` 安装路径`lib` 文件夹下的所有 jar包
最终项目 引入的包如下图

![](https://raw.githubusercontent.com/wakasann/diarynote/master/draft/uploads/190820/20190820145933.png)

下一步 创建进行 class 文件的类，如:`Test`

类的代码如下:

```
package com.waka.test;

import java.io.*;   
import org.gjt.jclasslib.structures.Constant;
import org.gjt.jclasslib.io.ClassFileWriter;
import org.gjt.jclasslib.structures.ClassFile;   
import org.gjt.jclasslib.structures.constants.ConstantUtf8Info; 

public class Test {
	public static void main(String args[]) throws Exception {
		// .class 文件绝对的路径
		String filePath = "E:\\workspace\\java\\MyTestJar.class";   
	    FileInputStream fis = new FileInputStream(filePath);   
	       
	    DataInput di = new DataInputStream(fis);   
	    ClassFile cf = new ClassFile();
	    cf.read(di);
	    Constant[] infos = cf.getConstantPool();
	    
	    int count = infos.length;   
	    for (int i = 0; i < count; i++) {   
	        if (infos[i] != null) {   
	            System.out.print(i);   
	            System.out.print(" = ");   
	            System.out.print(infos[i].getVerbose());   
	            System.out.print(" = ");
	            System.out.println("end");
	            // 通过jclasslib 工具 清楚修改的索引位置是 21
	            if(i == 21){   
	                ConstantUtf8Info uInfo = (ConstantUtf8Info)infos[i];
	                //设置修改的字符串是jclasslib Changed
	                uInfo.setString("jclasslib Changed");
	                infos[i]=uInfo;   
	            }   
	        }   
	    }   
	    cf.setConstantPool(infos);   
	    fis.close();   
	    File f = new File(filePath);   
	    ClassFileWriter.writeToFile(f, cf);   
	}
}
```



代码中 三处需要修改的地方，已添加注释.



下一步 在 Eclipse ide，在打开的 `Test`类编辑窗口中点击右键，然后选中 `Run as` -> `1 Java Application Alt + Shit + x,j`

![](https://raw.githubusercontent.com/wakasann/diarynote/master/draft/uploads/190820/20190820150954.png)

控制台没有出现错误，即运行修改了。



下一步 使用 `java` 命令执行已修改过`.class`查看执行的结果或者通过 `JD GUI` 工具查看 `.class` 文件的代码



如通过`java`命令执行 `.class` 文件查看执行的结果



```
E:\workspace\java> java MyTestJar
output String:jclasslib Changed
```

 执行结果是 修改后的，修改成功 ✅

或者通过 `JD GUI` 工具查看`.class` 文件，查看修改的地方是否修改到

![](https://raw.githubusercontent.com/wakasann/diarynote/master/draft/uploads/190820/20190820151551.png)

查看变量的值是修改后的，修改成功 ✅



##### References

1. [直接修改别人jar包里面的class文件 工具:jclasslib](https://blog.csdn.net/hexin373/article/details/6669813)
2. [【转】怎么解决java.lang.NoClassDefFoundError错误 ，以及类的加载机制](https://www.cnblogs.com/chenjfblog/p/7904024.html#_label1)
3. [Java————错误：找不到或无法加载主类](https://blog.csdn.net/weixin_42247720/article/details/81334611)