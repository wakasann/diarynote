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

#### Reference ####
1. [视频内容谁来保护？阿里云视频加密技术大揭秘，打造云上视频安全体系](https://yq.aliyun.com/articles/177637?spm=5176.180516.942817.3.5cd01017oPAQIj) 從這個文章中，知道一個 HLS 視頻標準加密
2. [HLS 如何实现加密 ？ - 闲散将军的回答 - 知乎](https://www.zhihu.com/question/20697986/answer/316969097) 知道一個 github項目和HLS有關的,
3. [使用ffmpeg视频切片并加密](https://www.cnblogs.com/codeAB/p/9184266.html)

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

