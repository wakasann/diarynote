# iview admin 使用 webuploader 分片上传 #



前端使用 `iview-admin`



安装 webuploader

```
npm install --save webuploader@^0.1.8
npm install --save jquery@^3.3.1
```



在 `src/main.js`  文件部 import 语句后面添加



```
import $ from 'jquery'
import WebUploader from 'webuploader'
window.$ = $
window.WebUploader = WebUploader
```







在 `src/components`，新建一个文件名为 `vue-uploader`,下面有2个文件，分别是 `index.vue`,`index.js`

`index.js`

```
import VueUpload from './index.vue'
export default VueUpload
```

`index.vue`

```
<template>
    <div class="upload">
    </div>
</template>
<script>
export default {
  name: 'vue-upload',
  props: {
    accept: {
      type: Object,
      default: null
    },
    // 上传地址
    url: {
      type: String,
      default: ''
    },
    // 上传最大数量 默认为100
    fileNumLimit: {
      type: Number,
      default: 1
    },
    // 大小限制 默认2M
    fileSingleSizeLimit: {
      type: Number,
      default: 2048000
    },
    // 上传时传给后端的参数，一般为token，key等
    formData: {
      type: Object,
      default: null
    },
    // 生成formData中文件的key，下面只是个例子，具体哪种形式和后端商议
    keyGenerator: {
      type: Function,
      default (file) {
        const currentTime = new Date().getTime()
        const key = `${currentTime}.${file.name}`
        return key
      }
    },
    multiple: {
      type: Boolean,
      default: false
    },
    // 上传按钮ID
    uploadButton: {
      type: String,
      default: ''
    }
  },
  data () {
    return {
      uploader: null
    }
  },
  mounted () {
    this.initWebUpload()
  },
  methods: {
    initWebUpload () {
      let testGuid = WebUploader.Base.guid() // 当前页面是生成的GUID作为标示
      this.uploader = WebUploader.create({
        auto: true, // 选完文件后，是否自动上传
        swf: '/static/lib/webuploader/Uploader.swf', // swf文件路径
        server: this.url, // 文件接收服务端
        pick: {
          id: this.uploadButton, // 选择文件的按钮
          multiple: this.multiple, // 是否多文件上传 默认false
          label: ''
        },
        accept: this.getAccept(this.accept), // 允许选择文件格式。
        threads: 1,
        fileNumLimit: this.fileNumLimit, // 限制上传个数
        // fileSingleSizeLimit: this.fileSingleSizeLimit, // 限制单个上传图片的大小
        // formData: this.formData, // 上传所需参数
        chunked: true, // 分片上传
        chunkSize: 2048000 * 80, // 分片大小
        duplicate: true, // 重复上传
        formData: { guid: testGuid },
        fileSizeLimit: 50 * 1024 * 1024 * 1024, // 所有文件总大小限制 6G
        fileSingleSizeLimit: 1 * 1024 * 1024 * 1024 // 单个文件大小限制 1 G
      })

      // 当有文件被添加进队列的时候，添加到页面预览
      this.uploader.on('fileQueued', (file) => {
        this.$emit('fileChange', file)
      })

      this.uploader.on('uploadStart', (file) => {
        // 在这里可以准备好formData的数据
        // this.uploader.options.formData.key = this.keyGenerator(file);
      })

      // 文件上传过程中创建进度条实时显示。
      this.uploader.on('uploadProgress', (file, percentage) => {
        this.$emit('progress', file, percentage)
      })

      this.uploader.on('uploadSuccess', (file, response) => {
        this.$emit('success', file, response)
      })

      this.uploader.on('uploadError', (file, reason) => {
        console.error(reason)
        this.$emit('uploadError', file, reason)
      })

      this.uploader.on('error', (type) => {
        let errorMessage = ''
        if (type === 'F_EXCEED_SIZE') {
          errorMessage = `文件大小不能超过${this.fileSingleSizeLimit / (1024 * 1000)}M`
        } else if (type === 'Q_EXCEED_NUM_LIMIT') {
          errorMessage = '文件上传已达到最大上限数'
        } else {
          errorMessage = `上传出错！请检查后重新上传！错误代码${type}`
        }

        console.error(errorMessage)
        this.$emit('error', errorMessage)
      })

      this.uploader.on('uploadComplete', (file, response) => {
        this.$emit('complete', file, response)
      })
    },

    upload (file) {
      this.uploader.upload(file)
    },
    stop (file) {
      this.uploader.stop(file)
    },
    // 取消并中断文件上传
    cancelFile (file) {
      this.uploader.cancelFile(file)
    },
    // 在队列中移除文件
    removeFile (file, bool) {
      this.uploader.removeFile(file, bool)
    },

    getAccept (accept) {
      return {
        title: 'Videos',
        exteensions: 'mp4,wmv,mkv,rmvb,3gp,avi,flv',
        mimeTypes: 'video/mp4,video/video/x-ms-wmv,video/mkv,video/rmvb,video/3gpp,video/x-msvideo,video/x-flv'
      }
    }
  }
}
</script>
<style lang="less">
.webuploader-container {
  position: relative;
}
.webuploader-element-invisible {
  position: absolute !important;
  clip: rect(1px 1px 1px 1px);
  clip: rect(1px,1px,1px,1px);
}
.webuploader-pick {
  position: relative;
  display: inline-block;
  cursor: pointer;
  background: #00b7ee;
  padding: 10px 15px;
  color: #fff;
  text-align: center;
  border-radius: 3px;
  overflow: hidden;
  }
.webuploader-pick-hover {
  background: #00a2d4;
  }

.webuploader-pick-disable {
  opacity: 0.6;
  pointer-events:none;
  }

</style>
```

代码是直接复制参考文章1封装好的上传组件 [上传组件 upload.vue代码](https://github.com/shady-xia/Blog/blob/master/vue-webuploader/upload.vue)



视图 vue 文件直接使用的是 参考文章1封装好的 [上传组件的代码 page.vue](https://github.com/shady-xia/Blog/blob/master/vue-webuploader/page.vue)



```
<template>
    <div class="page">
        <div id="filePicker">选择文件</div>

        <div class="file-panel">
            <h2>文件列表</h2>
            <div class="file-list">
                <ul class="file-item" :class="`file-${file.id}`" v-for="file in fileList">
                    <li class="file-name">{{file.name}}</li>
                    <li class="file-size">{{fileSize(file.size)}}</li>
                    <li class="file-status">上传中...</li>
                    <li class="file-operate">
                        <a title="开始" @click="resume(file)"><i class="iconfont icon-control-play"></i></a>
                        <a title="暂停" @click="stop(file)"><i class="iconfont icon-video-pause"></i></a>
                        <a title="移除" @click="remove(file)"><i class="iconfont icon-close-big"></i></a>
                    </li>
                    <li class="progress"></li>
                </ul>
                <div class="no-file" v-if="!fileList.length"><i class="iconfont icon-empty-file"></i> 暂无待上传文件</div>
            </div>
        </div>

        <vue-upload
                ref="uploader"
                uploadButton="#filePicker"
                multiple
                @fileChange="fileChange"
                @progress="onProgress"
                @success="onSuccess"
        ></vue-upload>
    </div>
</template>

<script>
    import vueUpload from '_c/vue-uploader'
    import api from '@/assets/js/api';

    export default {

        data() {
            return {
                fileList: [],
            }
        },
        mounted() {

        },
        computed: {
            uploader() {
                return this.$refs.uploader;
            }
        },
        methods: {
            fileChange(file) {
                if (!file.size) return;

                this.fileList.push(file);

                console.log(file);
            },

            onProgress(file, percent) {

                $(`.file-${file.id} .progress`).css('width', percent * 100 + '%');
                $(`.file-${file.id} .file-status`).html((percent * 100).toFixed(2) + '%');
            },

            onSuccess (file, response) {

                console.log('上传成功', response);

                if (response.needMerge) {
                    api.mergeUpload({
                        tempName: response.tempName,
                        fileName: file.name
                    }).then(res => {
                        let $fileStatus = $(`.file-${file.id} .file-status`);
                        console.log(res);
                        if (res.status === 0) {
                            $fileStatus.html('上传成功，转码中');
                        } else if (res.status === 1) {
                            $fileStatus.html('上传失败');
                        } else if (res.status === 2) {
                            $fileStatus.html('上传成功');
                        }
                    });
                }
            },

            resume(file) {
                this.uploader.upload(file);
            },
            stop(file) {
                this.uploader.stop(file);
            },
            remove(file) {
                // 取消并中断文件上传
                this.uploader.cancelFile(file);
                // 在队列中移除文件
                this.uploader.removeFile(file, true);

                // 在ui上移除
                let index = this.fileList.findIndex(ele => ele.id === file.id);
                this.fileList.splice(index, 1);
            },

            fileSize(size) {
                return WebUploader.Base.formatSize(size);
            },

        },
        watch: {},
        components: {
            vueUpload
        }
    }
</script>

<style lang="scss">

   .file-item{
    list-style:none;
    
}
</style>
```



------

Laravel api，注释使用 apidoc的语法格式

```
/**
         * @api {post} /myupload 上传mp4的api
         * @apiDescription 上传mp4，分片上传的api
         * @apiGroup Tools
         * @apiPermission none
         * @apiParam {String} chunk     当前分片下标
         * @apiParam {String} chunks  总分片数
         * @apiVersion 0.2.0
         * @apiSuccessExample {json} Success-Response:
         *     HTTP/1.1 200 OK
         *     {
         *         "data": {
         *             "url": "http://192.168.1.140/laravelapi/public/uploads/190318/20190318095650815662.mp4",
         *             "host": "http://192.168.1.140/laravelapi/public",
         *             "file": "temp/190318/20190318095650815662.mp4"
         *         }
         *     }
         * @apiErrorExample {json} Error-Response:
         *     HTTP/1.1 400
         *     {
         *       "error": "请上传mp4文件"
         *     }
         */
        public function myupload(Request $request){
            $num = $request->get('chunk',0);
            $count = $request->get('chunks',0);
            //判断请求中是否包含name=file的上传文件
            if (!$request->hasFile('file')){
                return $this->responseError('请上传mp4文件');
            }
            //允许的文件类型
            $fileTypes = array('video/mp4');
            // 判断图片上传中是否出错
            $file = $request->file('file');

            //获取原文件名
            $originalName = $file->getClientOriginalName();
            //扩展名
            $ext = $file->getClientOriginalExtension();//  上传文件后缀
            //文件类型
            $type = $file->getClientMimeType();
            //临时绝对路径
            $realPath = $file->getRealPath();
            $mime = $file->getMimeType();
            $dir = public_path();
            $dateDir = date('ymd');
            if(!is_dir($dir.$dateDir)){
                mkdir($dir.$dateDir);
                chmod($dir.$dateDir,755);
            }

            $host = url('/');
            if($num == $count){
                //分片1个，直接上传保存
                $filename = uniqid().'.'.$ext;
                $img_path = $host.'/uploads/'.$dateDir.'/'.$filename;
                $files_names = $dir.$dateDir.'/'.$filename;
                $file->move($dir.$dateDir.'/',$filename);  // 重命名保存;
            }else{
                //分片临时文件名
                $filename = md5($originalName).'-'.($num+1).'.tmp';
                //上传目录
                $path_name = $dir.'tmp/'.$filename;
                //保存临时文件
                $img_path = $host.'/uploads/tmp/'.$filename;
                //$bool = Storage::disk('tmp')->put($filename, file_get_contents($realPath));
                $file->move($dir.'tmp/',$filename);  // 重命名保存;
                //当分片上传完时 合并
                if(($num+1) == $count){
                    //最后合成后的名字及路径
                    $filename = uniqid().'.'.$ext;
                    $files_names = $dir.$dateDir.'/'.$filename;
                    //打开文件
                    $fp = fopen($files_names,"ab");
                    //循环读取临时文件，写入最终文件
                    for($i=0;$i<$count;$i++){
                        //临时文件路径及名称
                        $tmp_files = $dir.'tmp/'.md5($originalName).'-'.($i+1).'.tmp';
                        //打开临时文件
                        $handle = fopen($tmp_files,"rb");
                        //读取临时文件 写入最终文件
                        fwrite($fp,fread($handle,filesize($tmp_files)));
                        //关闭句柄 不关闭删除文件会出现没有权限
                        fclose($handle);
                        //删除临时文件
                        unlink($tmp_files);
                    }
                    //关闭句柄
                    fclose($fp);
                    unset($fp);
                    $img_path = $host.'/uploads/'.$dateDir.'/'.$filename;
                }
            }

            return response()->json(array(
                    'url'=>$img_path,
                    'host'=>$host,
                    'file'=>substr($img_path,strpos($img_path,'uploads')))
            );
        }
```


###### Reference #####
1. [Vue2.0结合webuploader实现文件分片上传](https://www.cnblogs.com/xiahj/p/8529545.html)

2. [laravel webupload实现分片上传文件及分片上传原理](https://www.liangjucai.com/article/112)



# [转发]Lumen 使用事件需要注意的事项


Lumen

版本 5.2

参考手册 [laravel event](https://learnku.com/docs/laravel/5.4/events/1250)

需要注意的事项

如果是第一次在lumen下使用事件，需要修改`bootstrap\app.php`文件 添加对`EventServiceProvider` 的注册(一般是被注释掉了，取消注释即可)

```
$app->register(App\Providers\EventServiceProvider::class);
```

在编写Listener的时候，注意`use` 需要监听的`Event`类

使用异步事件的时候，需要使用队列，请先配置好队列，需要启动监听，命令如下：

```
php artisan queue:listen --queue:default 
```

##### Reference ######

1. 出处: [Lumen 使用事件需要注意的事项](https://www.cnblogs.com/cshua/p/7084353.html)