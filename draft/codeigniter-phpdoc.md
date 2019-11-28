Codeigniter项目使用phpDocumentor生成api文档

## 前言

运行环境: 

1. vagrant 2.2.4

2. virtualbox 6.0 
3. box `bento/ubuntu-16.04`  (Apache 2.4.18 + Mysql 5.7.26 + PHP 5.6.40)
4. [phpDocumentor](https://www.phpdoc.org/) 

 phpDocumentor 2  是一个尽可能的通过你的PHP源码来生成文档的工具。

通过 Codeigniter项目applicaiton下控制器和模型 2个文件夹的源码来生成 api文档

```
vagrant@vagrant:~$ phpdoc -V
phpDocumentor version v2.9.0
```

我是通过 [phar]( https://docs.phpdoc.org/getting-started/installing.html#phar ) 方式进行安装

```
wget https://www.phpdoc.org/phpDocumentor.phar # 下载phpDocumentor.phar文件
# php phpDocumentor.phar -V
sudo mv phpDocumentor.phar /usr/local/bin/phpdoc #将文件移入到/usr/local/bin下，并重命名为phpdoc,在所有目录下，都可以运行 phpdoc命令了，即全局命令
phpdoc -h # 输出帮助信息
phpdoc -V # 输出版本信息
phpdoc template:list # 列出使用的模板
```

## 使用

简单的使用 phpDocumentor  是使用命令行参数(`-d` 一个目录，`-f` 一个文件)来提供一个输入点和告诉它你喜欢放的文件夹(`-t`)来输出你的文档。

例如:

```
$ phpdoc -d ./src -t ./docs/api
```

 上面的案列会扫描 在`src` 和它子文件夹下的所有文件，执行分析和生成一个包含文档的网站在`docs/api`下。如果你没有指定`-t`选项，文档的输出将会写入到一个子文件夹为`output`文件夹中。

命令:

```
codeignniter$: phpdoc -d "application/controllers,application/models" -t ./docs/api
```

在`docs/api`可以看到生成的文档



通过[配置](https://docs.phpdoc.org/references/configuration.html)文件进行配置

`phpdoc.dist.xml` 配置文件内容

```
<?xml version="1.0" encoding="UTF-8" ?>
<phpdoc>
    <title>Codeigniter</title>
    <parser>
        <target>docs/api</target>
    </parser>
    <files>
        <directory>application/controllers</directory>
        <directory>application/models</directory>
    </files>
    <transformations>
        <template name="responsive-twig" />
        <target>docs/api</target>
    </transformations>
</phpdoc>
```

命令:

```
codeigniter$: phpdoc -c phpdoc.dist.xml
```

