---
title: Codeigniter设置时区
date: 2019-08-23 20:06:28
tags:
  - timezone
categories:
  - Codeigniter
description:  Codeigniter设置时区
---

Codeigniter 3.x，在`application/config/config.php` 末尾加上

```
date_default_timezone_set("Asia/Shanghai");
```

##### References
1. [CI如何设置时区](https://www.lixh.cn/archives/1065.html)