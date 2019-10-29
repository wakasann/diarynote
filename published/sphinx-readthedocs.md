Sphinx + Read the docs theme



前言:

使用[Sphinx](https://www.sphinx-doc.org/en/master/index.html)  生成文档和使用 [Read The Docs](https://readthedocs.org/) 的 [readthedocs/sphinx_rtd_theme](https://github.com/readthedocs/sphinx_rtd_theme)，假设是在Windows上运行并已安装好 python，可以执行python的 `pip`命令



1. 通过 pip命令安装 sphinx 和 sphinx_rtd_theme


```
pip install -U Sphinx
pip install sphinx-rtd-theme
```

2. 在一个系统中的一个空的文件夹中运行: 如我在D 盘中新建了一个名字为 `sphinx`的文件夹

```
sphinx-quickstart
```

------

```

D:\>mkdir sphinx-study

D:\>cd sphinx-study

D:\sphinx-study>sphinx-quickstart
Welcome to the Sphinx 2.2.0 quickstart utility.

Please enter values for the following settings (just press Enter to
accept a default value, if one is given in brackets).

Selected root path: .

You have two options for placing the build directory for Sphinx output.
Either, you use a directory "_build" within the root path, or you separate
"source" and "build" directories within the root path.
> Separate source and build directories (y/n) [n]: y

The project name will occur in several places in the built documentation.
> Project name: Study
> Author name(s): wakasann
> Project release []: 1.0.0

If the documents are to be written in a language other than English,
you can select a language here by its language code. Sphinx will then
translate text that it generates into that language.

For a list of supported codes, see
https://www.sphinx-doc.org/en/master/usage/configuration.html#confval-language.
> Project language [en]: zh_CN

Creating file .\source\conf.py.
Creating file .\source\index.rst.
Creating file .\Makefile.
Creating file .\make.bat.

Finished: An initial directory structure has been created.

You should now populate your master file .\source\index.rst and create other documentation
source files. Use the Makefile to build the docs, like so:
   make builder
where "builder" is one of the supported builders, e.g. html, latex or linkcheck.


D:\sphinx-study>
```



从上面的末尾的提示，可以看到，可以通过`make builder`来编译文档，`builder`是如`html`,`latex` 或者`linkcheck`其中的一个支持的编译器。

如命令: `make html` 是生成html格式的文档



3. 在Sphinx项目中使用sphinx-rtd-theme主题，将下面的配置项加到 `conf.py`文件中

```
import sphinx_rtd_theme

extensions = [
    ...
    "sphinx_rtd_theme",
]

html_theme = "sphinx_rtd_theme"
```

来自 [Read the Docs Sphinx Theme](https://github.com/readthedocs/sphinx_rtd_theme)的README.md的说明



4. 生成 html 格式的文档

命令行 进入 Sphinx 项目，运行

```
make html
```

or

```
make.bat html 
```



------

如果需要发布到 RTD官网

1. 编译失败，如在编译时遇到以下错误



```
Sphinx error: master file [..]/checkouts/latest/contents.rst not found
```



修复方法是: 修改 sphinx项目的`conf.py` 添加以下的一行:

```
master_doc = 'index'
```





## References

1. [技术文档编写利器：Sphinx+Read the Docs](https://www.jianshu.com/p/0077cfce8dab)
2. [Getting Started](https://www.sphinx-doc.org/en/master/usage/quickstart.html)
3. [Read the Docs Sphinx Theme](https://github.com/readthedocs/sphinx_rtd_theme)
4. [Sphinx error: master file ../checkouts/latest/contents.rst not found](https://github.com/readthedocs/readthedocs.org/issues/2569#issuecomment-485117471)
5. [Removing “Edit on …” Buttons from Documentation](https://docs.readthedocs.io/en/stable/guides/remove-edit-buttons.html) 隐藏RTD上的 "Edit on Github"信息