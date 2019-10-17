基于 CI 1.7.x 的 项目使用新版本CI的文件缓存类库



维护的项目使用的是 codeigniter 1.7.x版本，但是我想使用文件缓存，但是旧版本是没有缓存类库的，并且`autoload.php`没有`drivers`这个配置项。



我复制的是 Codeigniter 3.1.10的缓存类

## 创建文件缓存类库文件
方法一:
前提: 本地已有下载好或者有使用 Codeigniter 3.x的项目

将Codeigniter 3.1.10中 `system/libraries/Cache/drivers/Cache_file`复制到旧项目的`systen/libraries`文件夹中

方法二:

前提: 可以访问到 github上的[CodeIgniter](https://github.com/bcit-ci/CodeIgniter),路径是`system/libraries/Cache/drivers/Cache_file.php`

在旧项目的`systen/libraries`文件夹中创建名为`Cache_file.php`文件,将[Cache_file.php raw](https://raw.githubusercontent.com/bcit-ci/CodeIgniter/master/system/libraries/Cache/drivers/Cache_file.php)的内容复制到刚才创建的 `Cache_file.php`中

## 配置

在 `config.php`文件中默认添加 `$config['cache_path']= '';`,空值默认是`APPPATH.'cache/'`

未添加缓存路径配置或者没有修改缓存路径配置的情况下，需要在 `systen/application` 文件夹下文件一个名为`cache`的文件夹(默认存放文件cache的路径)并分配 写权限


## 使用




在 `config/autoload.php`的`libraries`添加`cache_file`值

如:
```
$autoload['libraries'] = array('database','cache_file');
```

使用

```
//如果没有在`autoload.php`配置自动加载，需手动加载
// $this->load->liabrary('cache_file');

$this->cache_file->set('test','test_cache');

$data = $this->cache_file->get('test');
```

具体使用方法可参考[缓存驱动器](https://codeigniter.org.cn/user_guide/libraries/caching.html)

使用的区别是

```
//Codeigniter 3.x使用文件缓存

$this->cache->file->set('test','test_cache');

$data = $this->cache->file->get('test');

//旧Codeigniter版本使用文件缓存

$this->cache_file->set('test','test_cache');

$data = $this->cache_file->get('test');
```

-------

本来想找其它 php的缓存类库，放入到旧Codeigniter项目中的，但是想想 Codenigter 新版本中就有缓存类，尝试放入到 旧CI版本的类库文件夹中使用，尝试成功，故记录过程方便以后查看。

