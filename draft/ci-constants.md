---
title: Codeigniter 常量定义文件 constants.php
date: 2019-08-22 20:06:28
tags:
  - Codeigniter
categories:
  - Codeigniter
description: Codeigniter  常量定义文件 constants.php
---

Codeigniter 3.x 在 `application/config/constants.php`,可以看到很多  define 语句。

如果项目需要定义自己的常量，可以将常量定义在这里，自定义常量部分需要备份好，避免在后面升级 CI 框架时，覆盖了`constants.php` 中编辑的内容。

如在 `constants.php` 文件加了

```
//session 前缀
defined('SESSION_PREFIX')        OR define('SESSION_PREFIX', 'demo_');
```

可以在 `application/config`在新建一个 `constants_custom_backup.php` 文件用来做`constants.php`自定义常量的备份文件，里面放入自定义常量的代码:

```
<?php
defined('BASEPATH') OR exit('No direct script access allowed');
//session 前缀
defined('SESSION_PREFIX')        OR define('SESSION_PREFIX', 'demo_');
```
