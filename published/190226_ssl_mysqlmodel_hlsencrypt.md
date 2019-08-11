- [x] All published to blog

------

# laravel sql mode only_full_group_by#

環境:
mysql:  5.7.*
Laravel:  5.4.*

sql 中使用到了 group by,會提示 500錯誤,將 `config/database.php`中的 `strict`的值改爲`true`，並 設定了 `modes`

   ```
   'mysql' => [
       ...
       'strict' => true, //mysql version 5.6 set to false,mysql version 5.7 set to true
       'modes' => [
           'STRICT_ALL_TABLES',
           'ERROR_FOR_DIVISION_BY_ZERO',
           'NO_ZERO_DATE',
           'NO_ZERO_IN_DATE',
           'NO_AUTO_CREATE_USER',
       ],
   ],
   ```

   ##### Reference #####

   1. [How can I solve incompatible with sql_mode=only_full_group_by in laravel eloquent?](https://stackoverflow.com/questions/43776758/how-can-i-solve-incompatible-with-sql-mode-only-full-group-by-in-laravel-eloquen) 學習到在 `config/database.php` 中 配置 mysql的`strict`的值，是根據 mysql的版本改變而改變的
   2. [sql_mode=only_full_group_by研读](https://blog.csdn.net/allen_tsang/article/details/54892046) `config/database.php` 中mysql 鍵，可以通過設定 `modes`的值，來設定Laravel程序的sql_mode 

# HLS video #

```
ffmpeg -y   -i 80s_sd.mp4  -hls_time 60  -hls_key_info_file encrypt2.keyinfo  -hls_segment_filename "file%d.ts" playlist.m3u8  
```


```
cd /tmp
mkdir -p /tmp/drmvideo
ffmpeg -y -i /tmp/test.mp4 -strict -2 -c:a aac -hls_time 60 -hls_list_size 0 -hls_wrap 0 -hls_key_info_file encrypt.keyinfo  -hls_segment_filename /tmp/drmvideo/"test%d.ts" /tmp/drmvideo/playlist.m3u8
```

1. -i : 输入的文件
2. -hls_time n: 设置每片的长度，默认值为2。单位为秒
3. -hls_list_size n:  设置播放列表保存的最多条目，设置为0会保存有所片信息，默认值为5
4. -hls_wrap n:设置多少片之后开始覆盖，如果设置为0则不会覆盖，默认值为0.这个选项能够避免在磁盘上存储过多的片，而且能够限制写入磁盘的最多的片的数量
5. -hls_key_info_file： 加密文件
6. -hls_segment_filename: tis 切片文件名格式
7. 最后的路径是保存 m3u8文件的路径和文件名，目前是默认使用 `playlist.m3u8`

`/tmp/test/`文件夾中就會有一個 `playlist.m3u8` 和很多 ts切片文件

------

截取视频:

```
ffmpeg  -i ./test.mp4 -ss 00:00:00 -to 00:00:05 -vcodec copy -acodec copy  ./test2.mp4 -y
```

#### Reference ####
1. [视频内容谁来保护？阿里云视频加密技术大揭秘，打造云上视频安全体系](https://yq.aliyun.com/articles/177637?spm=5176.180516.942817.3.5cd01017oPAQIj) 從這個文章中，知道一個 HLS 視頻標準加密
2. [HLS 如何实现加密 ？ - 闲散将军的回答 - 知乎](https://www.zhihu.com/question/20697986/answer/316969097) 知道一個 github項目和HLS有關的,
3. [使用ffmpeg视频切片并加密](https://www.cnblogs.com/codeAB/p/9184266.html)
4. [FFmpeg foramts](http://www.ffmpeg.org/ffmpeg-formats.html#Options-5)
5. [ffmpeg的转码输出信息](http://www.supperxin.com/Coding/Details/get-transcode-progess-and-remaining-time)
6. [ffmpeg截取一段视频中一段视频](https://www.cnblogs.com/wainiwann/p/6972954.html)


# SSL 證書 #

之前的證書都是通過 [certbot/certbot](https://github.com/certbot/certbot)的命令生成的，但是目前一個服務器太多個網站，太多個ssl證書，證書過期之後，目前是 通過 `ssl for free` 網站再生成新的 ssl證書，再次更新證書週期




Let’s Encrypt 的文件有

1. privkey.pem  私钥
2. fullchain.pem  包括了certificate.crt(32行左右)和ca_bundle.crt(27行左右)的内容

ssl for free網站，下載的ssl證書有3個文件

1. private.key 私钥
2. ca_bundle.crt (27行左右)
3. certificate.crt (32行左右)

ssl for free的 `certificate.crt` + `ca_bundle.crt`的內容合併成一個 fullchain.pem

# Laravel hasFile return false#

如果传递的参数正确，而 `hasFile()` 返回 false, 应是 `php.ini` 中

1. post_max_size
2. upload_max_filesize

两个的值 需要修改

