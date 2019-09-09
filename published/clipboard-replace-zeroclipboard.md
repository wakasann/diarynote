---
title: 使用clipboard.js来替換ZeroClipboard.js进行复制
date: 2019-08-19 20:06:28
tags:
  - clipboard
categories:
  - Linux
description:  使用clipboard.js来替換ZeroClipboard.js进行复制
---


ZeroClipboard.js 有使用Flash格式的".swf" 覆盖在 按钮上方，进行复制，因为在使用Chrome浏览器浏览时，需要手动去开启网站可运行Flash,体验不算很好。

参考[ZeroClipboard过时了，js复制插件clipboard.js无需flash文件](http://www.webkaka.com/blog/archives/clipboard-no-flash.html)，发现了 [clipboard.js](https://github.com/zenorocha/clipboard.js) 的插件。

测试使用

```
<!DOCTYPE html>
<html>
<head>
	<meta charset="utf-8">
	<title>Test</title>
	<script src="https://cdnjs.cloudflare.com/ajax/libs/clipboard.js/2.0.4/clipboard.min.js"></script>
</head>
<body>
	<!-- Trigger -->
<button  type="button" class="btn" data-clipboard-text="Test">
    Copy to clipboard
</button>
<script type="text/javascript">
	var clipboard = new ClipboardJS('.btn');

	clipboard.on('success', function(e) {
	    console.info('Action:', e.action);
	    console.info('Text:', e.text);
	    console.info('Trigger:', e.trigger);

	    e.clearSelection();
	});

	clipboard.on('error', function(e) {
	    console.error('Action:', e.action);
	    console.error('Trigger:', e.trigger);
	});
</script>
</body>
</html>
```