# Cacti 发送警告邮件

Cacti版本: 0.8.8a

目的:监听CPU 高于1个设定值时发送警告邮件

1. 需要安装的插件:
1.1. [thold](https://docs.cacti.net/plugin:thold)  Threshold，下载使用的是`v0.5.0`版本
1.2. [Settings](https://docs.cacti.net/plugin:settings)  应该是主要用于发送邮件的,下载使用的是`v0.71-1`版本

将解压后的文件夹复制放入到 cacti根目录的`plugins` 文件夹中

![](images/190716/20190716183714.png)


##### References
1. [Cacti 流量监控全步骤、邮件报警实现过程](https://blog.csdn.net/redhat_xiaoguaishou/article/details/19335581)
2. [cacti报警邮件的设置](https://www.cnblogs.com/xzlive/p/10578140.html)
3. [cacti安装记录 第四阶段cacti的基本使用（3）setting and thresholds （邮件和阀值报警）](https://blog.csdn.net/xdnabl/article/details/51273395)