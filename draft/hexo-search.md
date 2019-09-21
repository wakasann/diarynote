Hexo next主题添加站内搜索功能

根据关键字搜索博文，站内搜索的功能很实用。hexo开启站内搜索很方便，已经有现成的插件可以使用，也是为了方便自己

1. 安装插件

```
npm install hexo-generator-search  --save
npm install hexo-generator-searchdb --save
```
2. 配置hexo站点主配置文件`_config.yml`,在文件末尾添加

```
search:
  path: search.xml
  field: post
  format: html
  limit: 10000
```

3. 修改next主题配置文件`_config.yml`，启用 `local_search`

```
# Local search
local_search:
  enable: false
  # if auto, trigger search by changing input
  # if manual, trigger search by pressing enter key or search button
  trigger: auto
  # show top n results per article, show all results by setting to -1
  top_n_per_article: 1
```

将 enable的 `false`改为`true` 

这样就开启了站内搜索了。


##### References

1. [Hexo开启站内搜索功能](http://molock.cn/posts/61829/)