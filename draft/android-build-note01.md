Android 编译笔记20191205



## 前言

是在Android Studio 重新导入 [gksvideourla](https://github.com/wakasann/gksvideourla) 项目时，在编辑时遇到问题的解决笔记



## The application could not be installed: INSTALL_PARSE_FAILED_INCONSISTENT_CERTIFICATES



在 Android Studio 中直连手机 安装App时，如果手机已安装的 app的签名和调试版本的签名不一致的情况时会出现这个提示，需要先将手机上的 需要调试的App 卸载掉，然后再安装.

### References
* [INSTALL_PARSE_FAILED_INCONSISTENT_CERTIFICATES错误解决方法](https://www.cnblogs.com/exmyth/p/4600270.html ) 


## gradle下载很慢


解决问题的方法如下：
使用文件管理器 打开用户主目录

>  windows平台: `c:\Users\用户名\.gradle`

> macos平台: /Users/用户名/.gradle
>
> linux平台: /home/用户名/.gradle

找到`./gradle/wrapper/dist/` 目录

找到对应的`gradle-版本好-all`的文件夹，进入到里面，如下图:

> 去官方网站下载这个版本号对应的安装包 [https://services.gradle.org/distributions/](https://services.gradle.org/distributions/) 
>
> 把下载下来的zip压缩文件 放到对应版本的子目录名里面 (目录名由字母和数字组成) 

![images/191213/fs2019121300002R.jpg](https://i.loli.net/2019/12/13/5MUjvtmanyAJqZO.jpg)
将压缩文件放入这个目录，我解压了一下，好像没有影响编译。



android 项目下载依赖jar包慢

解决：

在 `build.gradle` 文件中添加国内镜像

```
repositories {
        maven{ url 'https://maven.aliyun.com/repository/public/'}
        jcenter()
        //maven {url 'https://jitpack.io'}
        //maven {url 'https://maven.google.com'}
    }
```

### References
* [gradle下载很慢-csdn](https://blog.csdn.net/qq_40442656/article/details/86490012)
* [解决gradle下载慢的问题-csdh](https://blog.csdn.net/chenhaifeng2016/article/details/78654469)
* [阿里云公共代理库](https://help.aliyun.com/document_detail/102512.html)




## Android videoview

播放网络视频

在 `AndroidMainfest.xml` 新增网络的访问权限

```
<uses-permission android:name="android.permission.INTERNET"/>
```


在布局中添加

```
 <VideoView
               android:id="@+id/video_preview"
               android:layout_width="match_parent"
               android:layout_height="match_parent"
               android:layout_gravity="center" />
```


```
//引入包
import android.widget.VideoView;
import android.widget.MediaController;
import android.net.Uri;

//原生的videoview和控制
private VideoView mVideoView;
private MediaController mMediaController;


mVideoView = findViewById(R.id.video_preview);
mMediaController = new MediaController(MainActivity.this);

//将路径转换成uri
Uri uri = Uri.parse(videoUrl);
mVideoView.setVideoURI(uri);
mVideoView.setMediaController(mMediaController);
mVideoView.getBufferPercentage();
mVideoView.seekTo(0);
mVideoView.requestFocus();
```


### References

* [Android - 简单使用VideoView播放MP4](https://www.jianshu.com/p/554debd5f759)
* [Android VideoView无法播放网络视频](https://www.cnblogs.com/adyyzjb/p/8795935.html)
* [Android videoView简单使用-csdn](https://blog.csdn.net/qq_30711091/article/details/81867284)
* [Android studio 视频播放（利用Android原生的videoview）-csdn](https://blog.csdn.net/ternence_hsu/article/details/82836560)
* [Android--使用VideoView播放视频](https://www.cnblogs.com/shitaotao/p/7635710.html ) 了解到引入的包名
* [Android开发 VideoView视频播放详解](https://www.cnblogs.com/guanxinjing/p/11177739.html) 

## Android 下载网络的mp4文件

在 `AndroidMainfest.xml` 新增文件写入的权限

```
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

代码记录:

```
import java.io.File;
import java.io.FileOutputStream;
import java.io.BufferedInputStream;
import java.text.SimpleDateFormat;
import java.util.Date;
import android.os.Environment;
import java.net.URL;
import java.net.HttpURLConnection;
import java.io.IOException;
import java.io.InputStream;
import android.content.Intent;
import android.app.ProgressDialog;

//使用
final ProgressDialog pd;
pd = new ProgressDialog(MainActivity.this);
pd.setProgressStyle(ProgressDialog.STYLE_HORIZONTAL);
pd.setMessage("下载中...");
pd.setCanceledOnTouchOutside(false);
pd.show();
new Thread() {
    @Override
    public void run() {
        try {
            File file = getFileFromServer('http://www.example.com/example.mp4', pd);
            sendBroadcast(new Intent(Intent.ACTION_MEDIA_SCANNER_SCAN_FILE, Uri.parse("file://" + file)));

            sleep(1000);
            pd.dismiss(); // 结束掉进度条对话框
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}.start();
                    

/**
     * 将MP4文件存入到 应用的路径 files下
     * 固定是: Android/data/你的包名/files
     * @param path
     * @param pd
     * @return
     * @throws Exception
     */
public  File getFileFromServer(String path, ProgressDialog pd) throws Exception {
        // 如果相等的话表示当前的sdcard挂载在手机上并且是可用的
        if (Environment.getExternalStorageState().equals(Environment.MEDIA_MOUNTED)) {
            String path3 = getExternalFilesDir(null).getPath();
            File mediaStorageDir = new File(path3);
            if (!mediaStorageDir.exists()) {
                if (!mediaStorageDir.mkdirs()) {
                    showPromptToast("文件夹创建失败"+path3);
                    return null;
                }
            }

            URL url = new URL(path);
            HttpURLConnection conn = (HttpURLConnection) url.openConnection();
            conn.setConnectTimeout(5000);
            // 获取到文件的大小
            pd.setMax(conn.getContentLength());
            InputStream is = conn.getInputStream();
            if (!mediaStorageDir.exists()) {
                mediaStorageDir.mkdir();
            }
            // 文件根据当前的毫秒数给自己命名
            SimpleDateFormat myFmt = new SimpleDateFormat("yyyyMMddHHmmss");
            Date now = new Date();
            String videoFileName = "D" + myFmt.format(now);
            String suffix = ".mp4";
            File file = new File(mediaStorageDir + File.separator + videoFileName + suffix);
            FileOutputStream fos = new FileOutputStream(file);
            BufferedInputStream bis = new BufferedInputStream(is);
            byte[] buffer = new byte[1024];
            int len;
            int total = 0;
            while ((len = bis.read(buffer)) != -1) {
                fos.write(buffer, 0, len);
                total += len;
                // 获取当前下载量
                pd.setProgress(total);
            }
            fos.close();
            bis.close();
            is.close();
            return file;
        } else {
            return null;
        }
    }
    
```



### References

* [Android - 视频的录制保存以及视频列表查看](https://www.jianshu.com/p/c531d48cc492)
* [Android SD卡创建文件和文件夹失败](https://www.cnblogs.com/zhou-guobao/p/4667645.html)  path
* [Android Environment 类详解](https://www.cnblogs.com/roger-yu/p/5592458.html) 清楚 `Environment` 的完整路径是 `android.os.Environment`
* [Android 保存图片、视频到本地，并且刷新系统图库](https://blog.csdn.net/qq_36158551/article/details/88379839) download code
* [Android获取系统文件常用路径（详细）-csdn](https://blog.csdn.net/nicepainkiller/article/details/81181861) path info
* [Android存储路径你了解多少？](https://www.jianshu.com/p/2de0113b3164 )Environment.DIRECTORY_DOWNLOADS
* [彻底了解android中的内部存储与外部存储](https://www.cnblogs.com/jingmo0319/p/5586559.html)

##  edittext  focus

> 1.在布局文件中给edittext的父控件增加两个属性(我编写代码时，没有给Edittext添加这2个属性)
android:focusable="true"
android:focusableInTouchMode="true"

> 2.直接调用edittext的clearFocus()方法，不过该方法有时候会不生效
```
//searchView.setFocusable(true);
//searchView.setFocusableInTouchMode(true);
//searchView.requestFocus();
searchView.clearFocus();//失去焦点
//searchView.requestFocus();//获取焦点
```

### References
* [Android 如何使edittext默认失去焦点](https://www.cnblogs.com/rocksmall/p/7479674.html)