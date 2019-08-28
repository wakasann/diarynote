---
title: 尝试使用jpegtran优化jpeg图片
date: 2019-08-22 20:06:28
tags:
  - 图片优化
categories:
  - Tools
description:  尝试使用jpegtran优化jpeg图片
---

运行环境: Windows

## 前言
在 尝试图片优化时，安装 [psliwa/image-optimizer](https://github.com/psliwa/image-optimizer)这个类库，安装了对应的依赖，但是 使用这个类库 默认的配置优化不是很理想，优化出的jpeg很模糊

## 尝试 jpegtran

参考博文 [如何使用jpegtran 压缩JPG图片](https://www.cnblogs.com/hubl/p/5768597.html)

首先进入 [jpegtran](http://jpegclub.org/jpegtran/) ，下载可执行文件  jpegtran.exe 文件

第一步将 jpegtran.exe 放入到 `D:\test`目录下，然后复制一张 jpg 图片到 `D:\test`。

下一步 打开cmd 命令窗口，进入到 `D:\test`

```
jpegtran.exe -copy none -optimize -perfect 1.jpg 2.jpg
```
上面的命令是 对 `1.jpg`进行优化之后，输出 `2.jpg`

```
./jpegtran.exe -copy none -optimize -perfect 113c98f787472cbb519ce877da747ec2.jpg test5.jpg
```



------
git bash 执行的记录

```
Administrator@QXM40RPCS1XK71R MINGW64 /d/test
$ ./jpegtran.exe -optimize 1.jpg
D:\test\jpegtran.exe: must name one input and one output file
usage: D:\test\jpegtran.exe [switches] inputfile outputfile
Switches (names may be abbreviated):
  -copy none     Copy no extra markers from source file
  -copy comments Copy only comment markers (default)
  -copy all      Copy all extra markers
  -optimize      Optimize Huffman table (smaller file, but slow compression)
  -progressive   Create progressive JPEG file
Switches for modifying the image:
  -crop WxH+X+Y  Crop to a rectangular subarea
  -flip [horizontal|vertical]  Mirror image (left-right or top-bottom)
  -grayscale     Reduce to grayscale (omit color data)
  -perfect       Fail if there is non-transformable edge blocks
  -rotate [90|180|270]         Rotate image (degrees clockwise)
  -scale M/N     Scale output image by fraction M/N, eg, 1/8
  -transpose     Transpose image
  -transverse    Transverse transpose image
  -trim          Drop non-transformable edge blocks
  -wipe WxH+X+Y  Wipe (gray out) a rectangular subarea
Switches for advanced users:
  -arithmetic    Use arithmetic coding
  -restart N     Set restart interval in rows, or in blocks with B
  -maxmemory N   Maximum memory to use (in kbytes)
  -outfile name  Specify name for output file
  -verbose  or  -debug   Emit debug output
Switches for wizards:
  -scans file    Create multi-scan JPEG per script file
```


