解决 java.lang.UnsupportedClassVersionError

错误原因是：编译的 class 文件的 jdk版本和运行这个 class 文件的jdk 版本不一致

比如：服务器运行的是 jdk 1.7 ，本地去编译一个Java文件使用 jdk 1.8,然后上传到服务器使用，就会出现这样的错误

##### References
1. [【java.lang.UnsupportedClassVersionError】版本不一致出错](https://www.cnblogs.com/qiumingcheng/p/7151629.html)
2. [解决java.lang.UnsupportedClassVersionError问题](https://www.jianshu.com/p/94568f2c77fd)
3. [How to fix java.lang.UnsupportedClassVersionError: Unsupported major.minor version](https://stackoverflow.com/a/11432195)