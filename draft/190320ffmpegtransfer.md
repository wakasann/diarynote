# ffMpeg 转换格式为mp4 #

```shell
ffmpeg -i huapai.mkv -c:v libx264 -crf 23 -c:a libfaac -q:a 100 -strict -2 huapai.mp4
````

```
ffmpeg  -i huapai.mkv -ss 00:00:00 -to 00:05:00 -c:v copy -c:a copy  huapai0500.mkv -y
```

```
ffmpeg -i huapai.mkv -ss 00:00:00 -to 00:05:00 -c:v libx264 -crf 23 -c:a libfaac -q:a 100 -strict -2 huapai2.mp4
```

##### Reference ######
1. [使用ffmpeg将视频转为x264编码的mp4文件](https://blog.csdn.net/yeyinglingfeng/article/details/78749322)
2. [ffmpeg mp4 to wmv and wmv to mp4](https://www.cnblogs.com/tianciliangen/p/5616905.html)
3. [ffmpeg转换视频 格式](https://blog.csdn.net/xy707707/article/details/80987985)
4. [ffmpeg格式转换基础知识](https://www.cnblogs.com/yongfengnice/p/7133714.html)

# Wordpress 收到添加文件到媒体库中 #

```
$filename = '2019/03/test.mp4';
$uploadpath = wp_get_upload_dir();
//return $uploadpath;
$f_dir = '/'.$filename; 
$file = $uploadpath['basedir'].$f_dir;
$post_parent_id = 0;
if(file_exists($file)){
    if ( !function_exists('media_sideload_image') ){
        //require_once( ABSPATH.'/wp-admin/includes/media.php' );
        //require_once( ABSPATH.'/wp-admin/includes/file.php' );
        require_once( ABSPATH.'/wp-admin/includes/image.php' );
    }
    $wp_filetype = wp_check_filetype(basename($file), null ); //获取 mime类型
    $title = preg_replace('/\.[^.]+$/', '', basename($file));
    // Construct the attachment array.
    $attachment = array(
        'post_mime_type' => $wp_filetype, 
        'guid' => $uploadpath['baseurl'].$f_dir,
        'post_parent' => $post_parent_id,
        'post_title' => $title,
        'post_content' => '', // 指定空的内容
        'post_status' => 'inherit' // 上传文件的状态固定是inherit
    );
    // Save the attachment metadata
    $attach_id = wp_insert_attachment($attachment, $file, $post_parent_id);
    if ( !is_wp_error($attach_id) ){
    	wp_update_attachment_metadata( $attach_id, wp_generate_attachment_metadata( $attach_id, $file ) );
	}
}
````


##### Reference #####
1. [wp_insert_attachment.php](https://gist.github.com/fallenagus/dbdd2392a05a40db19c617da2ca0592e)
2. [WordPressの「wp_insert_attachment」を使って、特定の投稿に対して添付ファイルをメディアライブラリにアップロードする](https://www.imamura.biz/blog/16736)
3. [Uncaught Error: Call to undefined function wp_generate_attachment_metadata() @ wp-cron](https://wordpress.stackexchange.com/a/261262)


# ffMpeg Too many packets buffered for output stream 0:1#

出错的原因是:

有些视频数据有问题，导致视频处理过快，容器封装时队列溢出。

增大容器封装队列大小，比如

```
-max_muxing_queue_size 1024
```

如:

```
ffmpeg  -i test.mp4 -ss 00:00:00 -to 00:00:05 -max_muxing_queue_size 1024 -c:v libx264 -c:a copy  test_out.mp4 -y
```

以上的命令是截取 `test.mp4` 前5秒视频

##### Reference #####
1. [FFmpeg的那些坑-Too many packets buffered for output stream](https://blog.csdn.net/noahsun1024/article/details/80875460)

# Centos7 redis server #

```
wget http://download.redis.io/releases/redis-5.0.4.tar.gz
tar xzf redis-5.0.4.tar.gz
cd redis-5.0.4
make
make install
cp /root/redis-5.0.4/src/redis.conf /etc/redis/6379.conf
cp /root/redis-5.0.4/utils/redis_init_script /etc/init.d/redisd
chkconfig redisd on

service redisd start #启动redis
service redisd stop # 停止redis
```

##### Reference #####
1. [CENTOS7下安装REDIS](https://www.cnblogs.com/zuidongfeng/p/8032505.html)