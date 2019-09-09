# Laravel Storage remove folder #

放文件，會先創建一個文件夾，然後再放文件進入這個文件夾，如:

绝对路径是: `/home/wa/www/wp-content/uploads/testfolder/test`

```
use Illuminate\Support\Facades\Storage;

...

Storage::disk('drm')->deleteDirectory('test');
```


```
RecursiveDirectoryIterator::__construct(/home/wa/www/wp-content/uploads/testfolder/test): failed to open dir: Permission denied 469 /home/wa/www/api/vendor/league/flysystem/src/Adapter/Local.php  

```



php 在創建文件夾的時候，給 `777` 權限

```
$folder = 'test';
if(!is_dir($folder)){
    $old_umask = umask(0);
    mkdir($folder, 0777);
    umask($old_umask);
    //mkdir($drmDir);
    //chmod($drmDir,755);
}
```

看了文件夹的权限是 777了，再次执行，里面的文件可以删除掉，最后留下一个空的文件夹

```
rmdir(/home/wa/www/wp-content/uploads/testfolder/test): Permission denied 405 /home/wa/www/api/vendor/league/flysystem/src/Adapter/Local.php
```


##### Reference #####
1. [mkdir() in php is setting folder permission to 755 But I Need 777?](https://stackoverflow.com/a/4134734)



# Laravel Queue timeout #


```
App\Jobs\Test has been attempted too many times or run too long. The job may have previously timed out.
```

在执行的命令后面添加

```
--timeout=0
```


##### Reference #####

1. [Laravel queue process timeout error](https://stackoverflow.com/a/26238686)

# Wordpress 获取自定义文章类型的文章列表 #

```
$args = array( 
  'numberposts'		=> -1, // -1 is for all
  'post_type'		=> 'movie', // or 'post', 'page' 文章类型
  'orderby' 		=> 'ID', // or 'date', 'rand' 
  'order' 		=> 'ASC', // or 'DESC'
);

// Get the posts
$myposts = get_posts($args);
if($myposts){
    foreach($myposts as $mypost){
        echo $mypost->ID.' '.get_the_title($mypost->ID);
    }
    wp_reset_postdata();
}
```

##### Reference #####
1. [SHOWING A LIST OF CUSTOM POST TYPE USING GET_POSTS() LOOP IN WORDPRESS](http://blog.netgloo.com/2014/08/27/showing-a-list-of-custom-post-type-using-get_posts-loop-in-wordpress/)